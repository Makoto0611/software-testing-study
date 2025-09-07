# 第1章：テスト基礎 - Laravel開発者のための体系的テスト技術

## 1.1 テストの原則と概念

### 1.1.1 ソフトウェアテストの7つの基本原則

ソフトウェアテストの世界的な標準であるISTQB（International Software Testing Qualifications Board）が定める7つの基本原則は、Laravelアプリケーション開発においても重要な指針となります。これらの原則を理解し、実践することで、より効果的なテスト戦略を構築できます。

#### 原則1：テストは欠陥の存在を示すが、欠陥がないことは証明できない

**理論的説明**
テストによって欠陥を発見することはできますが、すべての欠陥が除去されたことを証明することは不可能です。これは論理学における「全称命題の否定」と同じ概念です。

**Laravelでの実装例**
```php
<?php
namespace Tests\Feature;

use Tests\TestCase;
use App\Models\Order;
use Illuminate\Foundation\Testing\RefreshDatabase;

class OrderProcessingTest extends TestCase
{
    use RefreshDatabase;

    public function test_order_calculation_with_normal_inputs()
    {
        // 正常系のテストは欠陥を見つける可能性がある
        $order = Order::factory()->create([
            'items' => [
                ['price' => 1000, 'quantity' => 2],
                ['price' => 500, 'quantity' => 3]
            ]
        ]);

        $total = $order->calculateTotal();
        
        $this->assertEquals(3500, $total);
        
        // しかし、このテストが通過しても、
        // - 負の数量での計算
        // - 極端に大きな数値での計算
        // - 並行処理時の計算
        // などのケースで欠陥がないことは保証されない
    }

    public function test_edge_cases_and_boundary_values()
    {
        // エッジケースを追加してもすべてをカバーすることは不可能
        $testCases = [
            [0, 1, 0],           // ゼロ価格
            [PHP_INT_MAX, 1, PHP_INT_MAX],  // 最大値
            [0.01, 100, 1],      // 小数点処理
        ];

        foreach ($testCases as [$price, $quantity, $expected]) {
            $result = (new Order)->calculateItemTotal($price, $quantity);
            $this->assertEquals($expected, $result);
        }
    }
}
```

**ベストプラクティス**
- リスクベースのアプローチを採用し、重要度の高い機能から優先的にテストする
- カバレッジ率を指標として使用するが、100%カバレッジが完全性を意味しないことを理解する
- 本番環境で発見された欠陥をテストケースに追加し、回帰テストを充実させる

**よくある落とし穴と対策**
- **落とし穴**: 高いコードカバレッジ率に満足してテストを終了する
- **対策**: カバレッジ率は一つの指標に過ぎないことを認識し、境界値テスト、異常系テスト、統合テストなど多角的なアプローチを採用する

#### 原則2：全数テストは不可能

**理論的説明**
すべての入力の組み合わせ、すべての実行パス、すべてのタイミングをテストすることは、単純なアプリケーションを除いて現実的に不可能です。

**Laravelでの実装例**
```php
<?php
namespace Tests\Feature;

use Tests\TestCase;

class RegistrationFormTest extends TestCase
{
    /**
     * フォームに12個のフィールドがあり、各フィールドが10通りの値を取れる場合、
     * 10^12 = 1兆通りの組み合わせが存在する
     */
    public function test_registration_with_equivalence_partitioning()
    {
        // 全数テストの代わりに同値分割法を使用
        $validTestCases = [
            // 各フィールドの有効な代表値をテスト
            [
                'name' => 'John Doe',          // 通常の名前
                'email' => 'john@example.com', // 有効なメール
                'age' => 25,                   // 有効範囲内
                'password' => 'SecurePass123!', // 強力なパスワード
            ],
        ];

        $invalidTestCases = [
            // 各フィールドの無効な代表値をテスト
            ['name' => '', 'expected_error' => 'name'],           // 空の名前
            ['email' => 'invalid', 'expected_error' => 'email'],  // 無効なメール
            ['age' => -1, 'expected_error' => 'age'],            // 負の年齢
            ['age' => 151, 'expected_error' => 'age'],           // 上限超過
        ];

        // 有効なケースのテスト
        foreach ($validTestCases as $data) {
            $response = $this->post('/register', $data);
            $response->assertRedirect('/dashboard');
        }

        // 無効なケースのテスト
        foreach ($invalidTestCases as $data) {
            $response = $this->post('/register', array_merge(
                $validTestCases[0],
                [$data['expected_error'] => $data[$data['expected_error']]]
            ));
            $response->assertSessionHasErrors($data['expected_error']);
        }
    }

    /**
     * 境界値分析を使用した効率的なテスト
     */
    public function test_age_boundary_values()
    {
        // 年齢フィールドの境界値のみをテスト（18-150歳が有効範囲と仮定）
        $boundaryTestCases = [
            [17, false],  // 下限境界の直前
            [18, true],   // 下限境界
            [19, true],   // 下限境界の直後
            [149, true],  // 上限境界の直前
            [150, true],  // 上限境界
            [151, false], // 上限境界の直後
        ];

        foreach ($boundaryTestCases as [$age, $shouldPass]) {
            $response = $this->post('/register', [
                'name' => 'Test User',
                'email' => "test{$age}@example.com",
                'age' => $age,
                'password' => 'Password123!',
            ]);

            if ($shouldPass) {
                $response->assertSessionDoesntHaveErrors('age');
            } else {
                $response->assertSessionHasErrors('age');
            }
        }
    }
}
```

**ベストプラクティス**
- ペアワイズテスト技法を使用して、パラメータの組み合わせを効率的にカバー
- リスクベースアプローチで、重要な組み合わせを優先的にテスト
- Fakerライブラリを活用してランダムデータでのテストを実施

#### 原則3：早期テストでコストと時間を節約

**理論的説明**
欠陥を早期に発見・修正することで、後工程での修正コストを大幅に削減できます。要件定義段階での欠陥修正コストを1とすると、本番環境での修正は100倍以上のコストがかかることがあります。

**Laravelでの実装例**
```php
<?php
// 1. 静的解析の早期導入（phpstan.neon）
return [
    'includes' => [
        './vendor/nunomaduro/larastan/extension.neon',
    ],
    'parameters' => [
        'paths' => [
            'app',
            'tests',
        ],
        'level' => 8,
    ],
];

// 2. Pre-commitフックでのテスト実行（.git/hooks/pre-commit）
#!/bin/bash
echo "Running static analysis..."
./vendor/bin/phpstan analyse

echo "Running tests..."
php artisan test --parallel

if [ $? -ne 0 ]; then
    echo "Tests failed. Commit aborted."
    exit 1
fi

// 3. TDD（テスト駆動開発）の実践
namespace Tests\Feature;

use Tests\TestCase;
use App\Services\InvoiceService;

class InvoiceServiceTest extends TestCase
{
    /**
     * TDDアプローチ：まずテストを書く
     */
    public function test_invoice_calculation_with_tax()
    {
        // Red: テストを先に書く（この時点では実装は存在しない）
        $service = new InvoiceService();
        
        $items = [
            ['price' => 1000, 'quantity' => 2, 'tax_rate' => 0.10],
            ['price' => 500, 'quantity' => 1, 'tax_rate' => 0.08],
        ];
        
        $result = $service->calculateTotal($items);
        
        // Green: 最小限の実装でテストを通す
        $this->assertEquals(2740, $result); // (1000*2*1.10) + (500*1*1.08)
        
        // Refactor: コードを改善しながらテストが通ることを確認
    }
}
```

**CI/CDパイプラインの設定例**
```yaml
# .github/workflows/tests.yml
name: Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: '8.2'
          extensions: mbstring, pdo, pdo_mysql
          coverage: pcov
      
      - name: Install Dependencies
        run: composer install --no-interaction
      
      - name: Run Static Analysis
        run: ./vendor/bin/phpstan analyse
      
      - name: Run Tests with Coverage
        run: php artisan test --coverage --min=80
```

#### 原則4：欠陥の偏在

**理論的説明**
パレートの法則（80-20の法則）がソフトウェアテストにも適用され、システムの20%のモジュールに80%の欠陥が集中する傾向があります。

**Laravelでの実装例**
```php
<?php
namespace Tests\Analysis;

use Tests\TestCase;
use Illuminate\Support\Facades\DB;

class DefectClusteringAnalysisTest extends TestCase
{
    /**
     * 欠陥の偏在を分析し、リスクベースのテスト戦略を立てる
     */
    public function test_identify_high_risk_modules()
    {
        // 過去の欠陥データを分析（実際のプロジェクトではログやバグトラッカーから収集）
        $defectsByModule = [
            'PaymentController' => 45,      // 決済処理
            'AuthenticationService' => 38,  // 認証処理
            'FileUploadService' => 32,      // ファイルアップロード
            'UserController' => 8,
            'ProductController' => 5,
            'CategoryController' => 3,
        ];
        
        // 複雑度メトリクスの収集（循環的複雑度）
        $complexityByModule = [
            'PaymentController' => 25,
            'AuthenticationService' => 20,
            'FileUploadService' => 18,
            'UserController' => 8,
            'ProductController' => 6,
            'CategoryController' => 4,
        ];
        
        // リスクスコアの計算
        $riskScores = [];
        foreach ($defectsByModule as $module => $defects) {
            $complexity = $complexityByModule[$module] ?? 1;
            $riskScores[$module] = $defects * log($complexity + 1);
        }
        
        arsort($riskScores);
        
        // 高リスクモジュールに対する集中的なテスト
        $highRiskModules = array_slice($riskScores, 0, 3, true);
        
        foreach ($highRiskModules as $module => $score) {
            $this->runIntensiveTestsForModule($module);
        }
    }
    
    private function runPaymentTests()
    {
        // 決済処理の集中的なテスト
        $testScenarios = [
            'concurrent_payments',        // 並行処理
            'payment_timeout',            // タイムアウト
            'invalid_card_data',          // 無効なカードデータ
            'duplicate_transactions',     // 重複トランザクション
            'refund_processing',          // 返金処理
            'currency_conversion',        // 通貨変換
            'payment_gateway_failure',    // ゲートウェイ障害
        ];
        
        foreach ($testScenarios as $scenario) {
            // 各シナリオのテストを実行
            $this->assertTrue($this->executeTestScenario($scenario));
        }
    }
}
```

#### 原則5：殺虫剤のパラドックス

**理論的説明**
同じテストを繰り返し実行すると、新しい欠陥を見つける効果が低下します。殺虫剤が害虫に対して効果を失うように、テストも「耐性」を持つ欠陥に対して無力になります。

**Laravelでの実装例**
```php
<?php
namespace Tests\Feature;

use Tests\TestCase;
use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;

class EvolvingTestStrategy extends TestCase
{
    use RefreshDatabase;

    /**
     * Property-Based Testing（性質ベースのテスト）
     */
    public function test_registration_properties()
    {
        for ($i = 0; $i < 100; $i++) {
            $faker = \Faker\Factory::create();
            $name = $faker->name();
            $email = $faker->unique()->safeEmail();
            
            $user = User::create([
                'name' => $name,
                'email' => $email,
                'password' => bcrypt('password'),
            ]);

            // 不変条件のテスト
            $this->assertNotEmpty($user->name);
            $this->assertNotFalse(filter_var($user->email, FILTER_VALIDATE_EMAIL));
            $this->assertLessThanOrEqual(now(), $user->created_at);
        }
    }

    /**
     * Mutation Testing の導入
     */
    public function test_with_mutation_testing()
    {
        // Infectionを使用したミューテーションテスト
        // composer require --dev infection/infection
        // ./vendor/bin/infection --min-msi=80 --min-covered-msi=85
        
        $calculator = new \App\Services\TaxCalculator();
        
        // ミューテーションに強いテスト
        $testCases = [
            [100, 10, 110],   // 基本ケース
            [0, 10, 0],       // ゼロ処理
            [100, 0, 100],    // 税率ゼロ
            [-100, 10, -110], // 負の値
            [0.01, 10, 0.011], // 小数点
        ];

        foreach ($testCases as [$amount, $taxRate, $expected]) {
            $result = $calculator->calculateWithTax($amount, $taxRate);
            $this->assertEquals($expected, $result, "Failed for amount: $amount, tax: $taxRate");
        }
    }
}
```

#### 原則6：テストは文脈に依存する

**理論的説明**
ECサイト、金融システム、ゲームアプリケーションなど、システムの種類によって適切なテストアプローチは異なります。

**Laravelでの実装例**
```php
<?php
namespace Tests\Contexts;

use Tests\TestCase;

/**
 * ECサイトのテスト戦略
 */
class EcommerceTestStrategy extends TestCase
{
    public function test_payment_processing_reliability()
    {
        // 決済処理の信頼性を重視
        $this->withoutExceptionHandling();
        
        // 冪等性のテスト（同じリクエストを複数回送信）
        $orderId = 'ORD-' . uniqid();
        
        for ($i = 0; $i < 3; $i++) {
            $response = $this->post('/api/payments', [
                'order_id' => $orderId,
                'amount' => 10000,
                'idempotency_key' => 'key-123',
            ]);
            
            $response->assertStatus(200);
        }
        
        // 1回だけ処理されていることを確認
        $this->assertDatabaseCount('payments', 1, ['order_id' => $orderId]);
    }
}

/**
 * 金融システムのテスト戦略
 */
class FinancialSystemTestStrategy extends TestCase
{
    public function test_transaction_accuracy()
    {
        // 計算精度の厳密なテスト
        $testCases = [
            ['0.01', '0.02', '0.03'],
            ['999999999.99', '0.01', '1000000000.00'],
            ['0.333333', '0.333333', '0.666666'],
        ];
        
        foreach ($testCases as [$amount1, $amount2, $expected]) {
            $result = bcadd($amount1, $amount2, 6);
            $this->assertEquals($expected, $result);
        }
    }
}
```

#### 原則7：バグゼロの落とし穴

**理論的説明**
すべてのテストが合格しても、ユーザーのニーズを満たさなければシステムは失敗です。技術的な正しさと、ビジネス価値の提供は別物です。

**Laravelでの実装例**
```php
<?php
namespace Tests\Acceptance;

use Tests\TestCase;

class BusinessRequirementsTest extends TestCase
{
    /**
     * 技術的には正しいが、ビジネスルールに反するケース
     */
    public function test_business_rules_compliance()
    {
        // 例：クーポンの適用ロジック
        $order = Order::factory()->create([
            'subtotal' => 1000,
            'shipping' => 500,
        ]);
        
        $coupon = Coupon::factory()->create([
            'code' => 'SAVE10',
            'discount_percent' => 10,
        ]);
        
        // 技術的には正しい計算
        $technicalDiscount = ($order->subtotal + $order->shipping) * 0.1; // 150
        
        // しかし、ビジネスルールでは送料は割引対象外
        $businessDiscount = $order->subtotal * 0.1; // 100
        
        $order->applyCoupon($coupon);
        
        $this->assertEquals($businessDiscount, $order->discount_amount);
        $this->assertNotEquals($technicalDiscount, $order->discount_amount);
    }
}
```

### 1.1.2 テストの目的

テストの目的は単にバグを見つけることだけではありません。品質保証、リスク軽減、意思決定支援など、多岐にわたる目的があります。

**主要な目的**
1. **欠陥の検出と防止** - バグを早期に発見し、システムの品質を向上
2. **品質評価** - 要件への適合性、パフォーマンス、セキュリティの評価
3. **意思決定支援** - リリース判断のための客観的データ提供
4. **リスク軽減** - 本番環境での障害リスクを最小化
5. **信頼性の構築** - ステークホルダーへの品質保証

## 1.2 テストプロセス

### 1.2.1 テストレベル

Laravelアプリケーションにおけるテストレベルは、テストの範囲と目的によって分類されます。

#### 単体テスト（Unit Test）

**特徴**
- 個々のクラスやメソッドを独立してテスト
- 外部依存をモック化
- 高速実行
- `tests/Unit/`ディレクトリに配置

**Laravelでの実装例**
```php
<?php
namespace Tests\Unit;

use PHPUnit\Framework\TestCase;
use App\Services\PriceCalculator;
use App\Services\TaxService;

class PriceCalculatorTest extends TestCase
{
    private PriceCalculator $calculator;
    private $taxServiceMock;
    
    protected function setUp(): void
    {
        parent::setUp();
        
        // モックオブジェクトの作成
        $this->taxServiceMock = $this->createMock(TaxService::class);
        $this->calculator = new PriceCalculator($this->taxServiceMock);
    }
    
    /**
     * 単一メソッドの振る舞いをテスト
     */
    public function test_calculate_subtotal_with_quantity()
    {
        $price = 1000;
        $quantity = 3;
        
        $result = $this->calculator->calculateSubtotal($price, $quantity);
        
        $this->assertEquals(3000, $result);
    }
    
    /**
     * 依存関係のモック化
     */
    public function test_calculate_with_tax()
    {
        // モックの振る舞いを定義
        $this->taxServiceMock
            ->expects($this->once())
            ->method('getTaxRate')
            ->with('Tokyo')
            ->willReturn(0.10);
        
        $result = $this->calculator->calculateWithTax(1000, 'Tokyo');
        
        $this->assertEquals(1100, $result);
    }
    
    /**
     * データプロバイダーを使用した複数ケースのテスト
     */
    /**
     * @dataProvider discountDataProvider
     */
    public function test_apply_discount($originalPrice, $discountPercent, $expected)
    {
        $result = $this->calculator->applyDiscount($originalPrice, $discountPercent);
        
        $this->assertEquals($expected, $result);
    }
    
    public function discountDataProvider(): array
    {
        return [
            'normal_discount' => [1000, 10, 900],
            'no_discount' => [1000, 0, 1000],
            'full_discount' => [1000, 100, 0],
            'decimal_discount' => [1000, 15.5, 845],
        ];
    }
}
```

#### 統合テスト（Integration Test）

**特徴**
- 複数のコンポーネントの連携をテスト
- データベースやAPIとの実際の通信を含む
- Feature Testとして実装されることが多い
- `tests/Feature/`ディレクトリに配置

**Laravelでの実装例**
```php
<?php
namespace Tests\Feature;

use Tests\TestCase;
use App\Models\User;
use App\Models\Product;
use App\Models\Order;
use App\Services\OrderService;
use Illuminate\Foundation\Testing\RefreshDatabase;

class OrderIntegrationTest extends TestCase
{
    use RefreshDatabase;
    
    /**
     * 複数コンポーネントの連携テスト
     */
    public function test_complete_order_workflow()
    {
        // 準備
        $user = User::factory()->create(['balance' => 10000]);
        $product = Product::factory()->create([
            'price' => 1000,
            'stock' => 10,
        ]);
        
        // 注文作成
        $orderService = app(OrderService::class);
        $order = $orderService->createOrder([
            'user_id' => $user->id,
            'items' => [
                ['product_id' => $product->id, 'quantity' => 2]
            ],
        ]);
        
        // 検証1: 注文が作成されている
        $this->assertDatabaseHas('orders', [
            'id' => $order->id,
            'user_id' => $user->id,
            'status' => 'pending',
            'total' => 2000,
        ]);
        
        // 検証2: 在庫が減っている
        $product->refresh();
        $this->assertEquals(8, $product->stock);
        
        // 支払い処理
        $paymentResult = $orderService->processPayment($order);
        
        // 検証3: 支払いが完了している
        $this->assertTrue($paymentResult->success);
        $order->refresh();
        $this->assertEquals('paid', $order->status);
        
        // 検証4: ユーザーの残高が減っている
        $user->refresh();
        $this->assertEquals(8000, $user->balance);
    }
}
```

#### システムテスト（System Test）

**特徴**
- エンドツーエンドの完全なワークフローをテスト
- 実際のブラウザを使用（Laravel Dusk）
- ユーザー視点でのテスト
- `tests/Browser/`ディレクトリに配置

**Laravel Duskでの実装例**
```php
<?php
namespace Tests\Browser;

use Laravel\Dusk\Browser;
use Tests\DuskTestCase;
use App\Models\User;
use App\Models\Product;

class EcommerceSystemTest extends DuskTestCase
{
    /**
     * エンドツーエンドのシステムテスト
     */
    public function test_complete_purchase_journey()
    {
        $user = User::factory()->create([
            'email' => 'testuser@example.com',
            'password' => bcrypt('password'),
        ]);
        
        $product = Product::factory()->create();
        
        $this->browse(function (Browser $browser) use ($user, $product) {
            $browser
                // ログイン
                ->visit('/login')
                ->type('email', $user->email)
                ->type('password', 'password')
                ->press('Login')
                
                // 商品をカートに追加
                ->visit('/products')
                ->click("@product-{$product->id}")
                ->press('Add to Cart')
                
                // チェックアウト
                ->visit('/cart')
                ->press('Proceed to Checkout')
                ->type('shipping_address', '123 Test Street')
                ->type('card_number', '4242424242424242')
                ->press('Place Order')
                
                // 注文確認
                ->assertPathIs('/orders/confirmation')
                ->assertSee('Order Confirmed');
        });
    }
}
```

#### 受入テスト（Acceptance Test）

**特徴**
- ビジネス要求を満たしているかを検証
- ユーザーストーリーベースのテスト
- 非技術者にも理解しやすい形式

**Laravelでの実装例**
```php
<?php
namespace Tests\Acceptance;

use Tests\TestCase;
use App\Models\User;
use App\Models\Order;

class BusinessAcceptanceTest extends TestCase
{
    /**
     * ユーザーストーリー: 
     * 「顧客として、過去の注文を確認できるようにしたい」
     */
    public function test_user_can_view_order_history()
    {
        // Given: 顧客が過去に3つの注文を持っている
        $user = User::factory()->create();
        $orders = Order::factory()->count(3)->create([
            'user_id' => $user->id,
        ]);
        
        // When: 顧客が注文履歴ページにアクセスする
        $response = $this->actingAs($user)->get('/orders');
        
        // Then: すべての注文が表示される
        $response->assertOk();
        foreach ($orders as $order) {
            $response->assertSee($order->order_number);
        }
    }
}
```

## 1.3 Laravelテストツールの詳細解説

### 1.3.1 PHPUnit vs Pest PHP

**PHPUnit（従来型）**
```php
<?php
namespace Tests\Feature;

use Tests\TestCase;

class TraditionalPHPUnitTest extends TestCase
{
    protected function setUp(): void
    {
        parent::setUp();
        // セットアップ処理
    }
    
    public function test_user_registration()
    {
        $response = $this->post('/register', [
            'name' => 'John Doe',
            'email' => 'john@example.com',
            'password' => 'password',
        ]);
        
        $response->assertRedirect('/dashboard');
    }
}
```

**Pest PHP（モダン）**
```php
<?php
// Pest PHPによる同じテスト
test('user registration', function () {
    $response = $this->post('/register', [
        'name' => 'John Doe',
        'email' => 'john@example.com',
        'password' => 'password',
    ]);
    
    expect($response->status())->toBe(302);
    expect($response)->toRedirect('/dashboard');
});

// データセットを使った複数ケースのテスト
test('calculates price with tax', function ($price, $taxRate, $expected) {
    $calculator = new TaxCalculator();
    
    expect($calculator->calculate($price, $taxRate))->toBe($expected);
})->with([
    [100, 0.1, 110],
    [200, 0.08, 216],
    [0, 0.1, 0],
]);

// アーキテクチャテスト
arch('controllers follow naming convention')
    ->expect('App\Http\Controllers')
    ->toHaveSuffix('Controller')
    ->toExtend('App\Http\Controllers\Controller');
```

### 1.3.2 テストダブル（モック、スタブ、スパイ）

```php
<?php
namespace Tests\Feature;

use Tests\TestCase;
use App\Services\PaymentGateway;
use App\Services\EmailService;
use Mockery;

class TestDoubleExamples extends TestCase
{
    /**
     * スタブ：事前定義された値を返す
     */
    public function test_with_stub()
    {
        $paymentStub = $this->createStub(PaymentGateway::class);
        $paymentStub->method('charge')
                    ->willReturn(['success' => true, 'transaction_id' => 'TX123']);
        
        $order = new Order($paymentStub);
        $result = $order->process();
        
        $this->assertTrue($result->success);
    }
    
    /**
     * モック：期待される呼び出しを検証
     */
    public function test_with_mock()
    {
        $emailMock = $this->createMock(EmailService::class);
        $emailMock->expects($this->once())
                  ->method('send')
                  ->with('user@example.com', 'Order Confirmation');
        
        $notification = new OrderNotification($emailMock);
        $notification->notify('user@example.com');
    }
    
    /**
     * スパイ：後から呼び出しを検証
     */
    public function test_with_spy()
    {
        Cache::spy();
        
        $service = new CachedDataService();
        $service->getData('key');
        
        Cache::shouldHaveReceived('get')
             ->once()
             ->with('key');
    }
}
```

### 1.3.3 データベーステスト戦略

```php
<?php
namespace Tests\Feature;

use Tests\TestCase;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\DatabaseTransactions;

class DatabaseTestingStrategies extends TestCase
{
    // 戦略1: RefreshDatabase（推奨）
    use RefreshDatabase;
    
    public function test_with_refresh_database()
    {
        // 各テスト前にマイグレーションを実行
        // テストはトランザクション内で実行される
        $user = User::factory()->create();
        
        $this->assertDatabaseHas('users', [
            'email' => $user->email,
        ]);
    }
}

class TransactionStrategy extends TestCase
{
    // 戦略2: DatabaseTransactions
    use DatabaseTransactions;
    
    public function test_with_transaction()
    {
        // テスト全体がトランザクションでラップされる
        // テスト後に自動的にロールバック
        $product = Product::create(['name' => 'Test Product']);
        
        $this->assertDatabaseHas('products', [
            'name' => 'Test Product',
        ]);
    }
}

class CustomDatabaseStrategy extends TestCase
{
    protected function setUp(): void
    {
        parent::setUp();
        
        // カスタムデータベース設定
        config(['database.default' => 'testing']);
        
        // テスト用シードデータの投入
        $this->seed(TestDataSeeder::class);
    }
    
    public function test_with_custom_setup()
    {
        // 特定のテストデータで初期化された状態でテスト
        $this->assertDatabaseCount('users', 10);
    }
}
```

## 1.4 実践的なベストプラクティス

### 1.4.1 テストの構造化と命名規則

```php
<?php
namespace Tests\Feature\Order;

use Tests\TestCase;

class OrderProcessingTest extends TestCase
{
    /**
     * 命名規則: test_[action]_[context]_[expected_result]
     */
    public function test_create_order_with_valid_data_returns_success()
    {
        // Arrange（準備）
        $user = User::factory()->create();
        $product = Product::factory()->create();
        
        // Act（実行）
        $response = $this->actingAs($user)
                         ->post('/api/orders', [
                             'product_id' => $product->id,
                             'quantity' => 2,
                         ]);
        
        // Assert（検証）
        $response->assertStatus(201)
                 ->assertJson([
                     'status' => 'success',
                     'data' => [
                         'user_id' => $user->id,
                         'product_id' => $product->id,
                     ]
                 ]);
    }
    
    /**
     * Givenメソッドパターンで可読性向上
     */
    public function test_vip_customer_gets_discount()
    {
        $this->givenVipCustomer()
             ->whenPurchasingProduct(1000)
             ->thenDiscountShouldBe(100);
    }
    
    private function givenVipCustomer()
    {
        $this->customer = User::factory()->vip()->create();
        return $this;
    }
    
    private function whenPurchasingProduct($price)
    {
        $this->order = Order::create([
            'user_id' => $this->customer->id,
            'amount' => $price,
        ]);
        return $this;
    }
    
    private function thenDiscountShouldBe($expectedDiscount)
    {
        $this->assertEquals($expectedDiscount, $this->order->discount);
    }
}
```

### 1.4.2 パフォーマンステスト

```php
<?php
namespace Tests\Performance;

use Tests\TestCase;
use Illuminate\Support\Facades\DB;

class PerformanceTest extends TestCase
{
    /**
     * レスポンスタイムのテスト
     */
    public function test_api_response_time()
    {
        $startTime = microtime(true);
        
        $response = $this->get('/api/products');
        
        $responseTime = (microtime(true) - $startTime) * 1000;
        
        $this->assertLessThan(200, $responseTime, 
            "Response time {$responseTime}ms exceeds 200ms threshold");
    }
    
    /**
     * データベースクエリ数のテスト
     */
    public function test_query_optimization()
    {
        DB::enableQueryLog();
        
        $response = $this->get('/api/users?include=posts,comments');
        
        $queryCount = count(DB::getQueryLog());
        
        $this->assertLessThan(5, $queryCount, 
            "Query count {$queryCount} exceeds maximum of 5");
        
        // N+1問題の検出
        $this->assertNoNPlusOneQueries();
    }
    
    /**
     * メモリ使用量のテスト
     */
    public function test_memory_usage()
    {
        $initialMemory = memory_get_usage();
        
        // 大量データ処理
        $service = new DataProcessingService();
        $service->processLargeDataset();
        
        $memoryUsed = (memory_get_peak_usage() - $initialMemory) / 1024 / 1024;
        
        $this->assertLessThan(64, $memoryUsed,
            "Memory usage {$memoryUsed}MB exceeds 64MB limit");
    }
    
    private function assertNoNPlusOneQueries()
    {
        $queries = collect(DB::getQueryLog());
        
        $similarQueries = $queries->groupBy(function ($query) {
            return preg_replace('/\d+/', '?', $query['query']);
        })->filter(function ($group) {
            return $group->count() > 1;
        });
        
        $this->assertEmpty($similarQueries,
            "N+1 query detected: " . $similarQueries->keys()->first());
    }
}
```

### 1.4.3 セキュリティテスト

```php
<?php
namespace Tests\Security;

use Tests\TestCase;

class SecurityTest extends TestCase
{
    /**
     * SQLインジェクション対策のテスト
     */
    public function test_sql_injection_prevention()
    {
        $maliciousInput = "'; DROP TABLE users; --";
        
        $response = $this->get("/api/search?q={$maliciousInput}");
        
        $response->assertOk();
        $this->assertDatabaseHas('users', []); // テーブルが存在することを確認
    }
    
    /**
     * XSS対策のテスト
     */
    public function test_xss_prevention()
    {
        $xssPayload = '<script>alert("XSS")</script>';
        
        $response = $this->post('/api/comments', [
            'content' => $xssPayload,
        ]);
        
        $response->assertOk();
        
        $comment = Comment::latest()->first();
        $this->assertStringNotContainsString('<script>', $comment->content);
        $this->assertEquals('&lt;script&gt;alert("XSS")&lt;/script&gt;', 
                          $comment->content);
    }
    
    /**
     * CSRF対策のテスト
     */
    public function test_csrf_protection()
    {
        // CSRFトークンなしでのリクエスト
        $response = $this->withoutMiddleware(VerifyCsrfToken::class)
                         ->post('/api/transfer', [
                             'amount' => 10000,
                             'to_account' => 'malicious',
                         ]);
        
        $response->assertStatus(419); // Token Mismatch
    }
    
    /**
     * 認証・認可のテスト
     */
    public function test_authorization()
    {
        $user = User::factory()->create(['role' => 'user']);
        $admin = User::factory()->create(['role' => 'admin']);
        
        // 一般ユーザーは管理画面にアクセスできない
        $response = $this->actingAs($user)->get('/admin');
        $response->assertStatus(403);
        
        // 管理者はアクセス可能
        $response = $this->actingAs($admin)->get('/admin');
        $response->assertOk();
    }
}
```

## 1.5 演習問題と実践課題

### 演習問題1: TDDでショッピングカート機能を開発

**課題**: 以下の要件を満たすショッピングカート機能をTDDで開発してください。

要件:
1. カートに商品を追加できる
2. カートから商品を削除できる
3. 商品の数量を変更できる
4. 合計金額を計算できる（税込み）
5. クーポンを適用できる（割引率または固定額）

**スタートコード**:
```php
<?php
namespace Tests\Feature;

use Tests\TestCase;
use App\Models\Product;
use App\Services\ShoppingCart;

class ShoppingCartTest extends TestCase
{
    private ShoppingCart $cart;
    
    protected function setUp(): void
    {
        parent::setUp();
        $this->cart = new ShoppingCart();
    }
    
    public function test_can_add_product_to_cart()
    {
        // ここからテストを書き始める
        // Red -> Green -> Refactor のサイクルで開発
        
        $product = Product::factory()->create(['price' => 1000]);
        
        $this->cart->add($product, 2);
        
        $this->assertEquals(1, $this->cart->itemCount());
        $this->assertEquals(2, $this->cart->totalQuantity());
    }
    
    // 他のテストケースを追加...
}
```

### 演習問題2: レガシーコードのリファクタリング

**課題**: 以下のレガシーコードにテストを追加し、リファクタリングしてください。

```php
<?php
// app/Legacy/OrderProcessor.php
class OrderProcessor
{
    public function process($orderId)
    {
        $order = DB::table('orders')->find($orderId);
        if (!$order) return false;
        
        $items = DB::table('order_items')
            ->where('order_id', $orderId)->get();
            
        $total = 0;
        foreach ($items as $item) {
            $total += $item->price * $item->quantity;
        }
        
        if ($order->coupon_code) {
            $coupon = DB::table('coupons')
                ->where('code', $order->coupon_code)->first();
            if ($coupon) {
                $total = $total * (1 - $coupon->discount_percent / 100);
            }
        }
        
        DB::table('orders')->where('id', $orderId)
            ->update(['total' => $total, 'processed_at' => now()]);
            
        return true;
    }
}
```

**リファクタリング手順**:
1. 現在の動作を保証するテストを作成
2. 依存性注入を導入
3. ビジネスロジックを分離
4. Eloquentモデルを使用
5. サービスクラスに分割

### 演習問題3: パフォーマンステストの実装

**課題**: 商品検索APIのパフォーマンステストを実装してください。

要件:
- レスポンスタイム: 200ms以下
- メモリ使用量: 64MB以下
- データベースクエリ: 10個以下
- 1000件の商品データでテスト

```php
<?php
namespace Tests\Performance;

use Tests\TestCase;

class ProductSearchPerformanceTest extends TestCase
{
    protected function setUp(): void
    {
        parent::setUp();
        
        // 1000件のテストデータを生成
        Product::factory()->count(1000)->create();
    }
    
    public function test_search_performance()
    {
        // パフォーマンステストを実装
    }
}
```

### 演習問題4: E2Eテストシナリオの作成

**課題**: ECサイトの購入フロー全体をカバーするE2Eテストを作成してください。

シナリオ:
1. ユーザー登録
2. ログイン
3. 商品検索
4. 商品詳細確認
5. カート追加
6. 配送先入力
7. 支払い情報入力
8. 注文確定
9. 注文確認メール受信
10. 注文履歴確認

### 演習問題5: ミューテーションテストの導入

**課題**: Infectionを使用してミューテーションテストを導入し、MSI（Mutation Score Indicator）を80%以上にしてください。

```bash
# Infectionのインストール
composer require --dev infection/infection

# 設定ファイルの生成
./vendor/bin/infection --init

# ミューテーションテストの実行
./vendor/bin/infection --min-msi=80
```

## 1.6 まとめと次のステップ

### 本章で学んだこと

1. **ISTQBの7つのテスト原則**
   - テストの限界と可能性を理解
   - 効率的なテスト戦略の立案方法
   - リスクベースアプローチの重要性

2. **テストレベルとその実装**
   - 単体テスト、統合テスト、システムテスト、受入テスト
   - 各レベルでのLaravel実装方法
   - 適切なテストツールの選択

3. **Laravelのテストエコシステム**
   - PHPUnit、Pest PHP、Laravel Dusk
   - モック、スタブ、スパイの使い分け
   - データベーステスト戦略

4. **実践的なテスト技法**
   - TDD/BDDアプローチ
   - パフォーマンステスト
   - セキュリティテスト

### 次のステップ

1. **継続的な学習**
   - Laravel公式ドキュメントのテストセクションを定期的に確認
   - 新しいテスティングツールやフレームワークの動向をフォロー
   - コミュニティのベストプラクティスを学習

2. **実プロジェクトへの適用**
   - 小規模な機能からTDDを実践
   - 既存コードへのテスト追加
   - CI/CDパイプラインの構築

3. **チーム内での知識共有**
   - テストガイドラインの策定
   - コードレビューでのテスト品質チェック
   - ペアプログラミングでのテスト作成

4. **高度なテスト技法の習得**
   - プロパティベーステスト
   - コントラクトテスト
   - カオスエンジニアリング

### 推奨リソース

1. **書籍**
   - "Test-Driven Laravel" by Adam Wathan
   - "Laravel Testing Decoded" by Jeffrey Way
   - "PHPUnit Essentials" by Zdenek Machek

2. **オンラインコース**
   - Laracasts: Testing Laravel
   - Spatie: Testing Laravel
   - Pest PHP公式チュートリアル

3. **コミュニティ**
   - Laravel公式Discord
   - LaravelDaily
   - Laravel News

テストは単なる品質保証の手段ではなく、より良いコード設計を促進し、開発の自信を高める重要な実践です。本章で学んだ原則と技法を日々の開発に適用し、継続的に改善していくことで、堅牢で保守性の高いLaravelアプリケーションを構築できるようになります。

次章では、より高度なテスト戦略として、マイクロサービスアーキテクチャでのテスト、APIテストの自動化、パフォーマンステストの詳細などを扱います。