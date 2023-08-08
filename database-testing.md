# 데이터베이스 테스팅

- [시작하기](#introduction)
  - [각각의 테스트 수행 후에 데이터베이스 재설정하기](#resetting-the-database-after-each-test)
- [모델 팩토리](#model-factories)
- [시더 실행하기](#running-seeders)
- [사용 가능한 검증-Assertions](#available-assertions)

<a name="introduction"></a>
## 시작하기

라라벨은 데이터베이스 기반 애플리케이션을 더 쉽게 테스트할 수 있도록 다양한 유용한 도구와 검증을 제공합니다. 또한 라라벨 모델 팩토리와 시더를 사용하면 애플리케이션의 Eloquent 모델과 관계를 사용하여 테스트 데이터베이스 레코드를 손쉽게 생성할 수 있습니다. 다음 문서에서 이러한 모든 강력한 기능에 대해 설명합니다.

<a name="resetting-the-database-after-each-test"></a>
### 각각의 테스트 수행 후에 데이터베이스 재설정하기

더 진행하기 전에 이전 테스트의 데이터가 후속 테스트를 방해하지 않도록 각 테스트 후에 데이터베이스를 재설정하는 방법에 대해 논의하겠습니다. 라라벨에 포함된 `Illuminate\Foundation\Testing\RefreshDatabase` 트레이트-trait이 이 문제를 처리합니다. 테스트 클래스에서 트레이트-trait을 사용하기만 하면 됩니다.

    <?php

    namespace Tests\Feature;

    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        use RefreshDatabase;

        /**
         * A basic functional test example.
         *
         * @return void
         */
        public function test_basic_example()
        {
            $response = $this->get('/');

            // ...
        }
    }

`Illuminate\Foundation\Testing\RefreshDatabase` 트레이트-trait는 스키마가 최신상태인 경우에 데이터베이스 마이그레이션을 수행하지 않습니다. 대신에, 데이터베이스 트랜잭션 안에서만 테스트를 실행합니다. 따라서 데이터베이스에 이 트레이트-trait을 사용하지 않는 테스트 케이스에 의해 추가된 레코드는 계속 남아 있을 수 있습니다. 

데이터베이스를 완전히 리셋하려면 `Illuminate\Foundation\Testing\DatabaseMigrations`, `Illuminate\Foundation\Testing\DatabaseTruncation` 트레이트-trait을 사용할 수 있습니다. 하지만 이 두개의 트레이트-trait 는 `RefreshDatabase` 보다 훨씬 느립니다.

<a name="model-factories"></a>
## 모델 팩토리

테스트를 할 때 테스트를 실행하기 전에 레코드를 데이터베이스에 넣어둘 필요가 있습니다. 이 테스트 데이터를 생성할 때 각 열의 값을 수동으로 지정하는 대신, 라라벨을 사용하면 [모델 팩토리](/docs/{{version}}/eloquent-factories) 를 사용하여 각 [엘로퀀트 모델](/docs/{{version}}/eloquent) 에 대한 기본 속성 세트를 정의할 수 있습니다.

모델 팩토리를 생성하고 활용하여 모델을 생성하는 방법에 대해 자세히 알아보려면 전체 [모델 팩토리 문서](/docs/{{version}}/eloquent-factories)를 참조하십시오 . 모델 팩토리를 정의한 후에는 테스트 내에서 팩토리를 활용하여 모델을 생성할 수 있습니다.

    use App\Models\User;

    public function test_models_can_be_instantiated()
    {
        $user = User::factory()->create();

        // ...
    }

## Running Seeders
## 시더 실행하기

기능 테스트 중에 [database seeders](/docs/{{version}}/seeding)를 사용하여 데이터베이스를 채우려면 `seed` 메서드를 호출할 수 있습니다. 기본적으로 `seed` 메소드는 다른 모든 시드를 실행해야 하는 `DatabaseSeeder`를 실행합니다. 또는 `seed` 메서드에 특정 시드 클래스 이름을 전달합니다.

    <?php

    namespace Tests\Feature;

    use Database\Seeders\OrderStatusSeeder;
    use Database\Seeders\TransactionStatusSeeder;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        use RefreshDatabase;

        /**
         * Test creating a new order.
         *
         * @return void
         */
        public function test_orders_can_be_created()
        {
            // Run the DatabaseSeeder...
            $this->seed();

            // Run a specific seeder...
            $this->seed(OrderStatusSeeder::class);

            // ...

            // Run an array of specific seeders...
            $this->seed([
                OrderStatusSeeder::class,
                TransactionStatusSeeder::class,
                // ...
            ]);
        }
    }

또는 `RefreshDatabase` 트레이트-trait을 사용하는 각 테스트 전에 데이터베이스를 자동으로 시드하도록 라라벨에 지시할 수 있습니다. 기본 테스트 클래스에서 `$seed` 속성을 정의하여 이를 수행할 수 있습니다.

    <?php

    namespace Tests;

    use Illuminate\Foundation\Testing\TestCase as BaseTestCase;

    abstract class TestCase extends BaseTestCase
    {
        use CreatesApplication;

        /**
         * Indicates whether the default seeder should run before each test.
         *
         * @var bool
         */
        protected $seed = true;
    }

`$seed` 속성이 `true`면 테스트는 `RefreshDatabase` 트레이트-trait을 사용하는 각 테스트 전에 `Database\Seeders\DatabaseSeeder` 클래스를 실행합니다. 그러나 테스트 클래스에 `seeder` 속성을 정의하여 실행해야 하는 특정 시드를 지정할 수 있습니다.

    use Database\Seeders\OrderStatusSeeder;

    /**
     * Run a specific seeder before each test.
     *
     * @var string
     */
    protected $seeder = OrderStatusSeeder::class;

<a name="available-assertions"></a>
## 사용 가능한 검증-Assertions

라라벨은 [PHPUnit](https://phpunit.de/) 기능 테스트를 위한 여러 데이터베이스 검증을 제공합니다. 아래에서는 이러한 각 검증에 대해 살펴볼 것입니다.

<a name="assert-database-count"></a>
#### assertDatabaseCount

데이터베이스의 테이블에 지정된 수의 레코드가 포함되어 있는지 확인합니다.

    $this->assertDatabaseCount('users', 5);

<a name="assert-database-has"></a>
#### assertDatabaseHas

데이터베이스의 테이블에 주어진 키 값 쿼리 제약 조건과 일치하는 레코드가 포함되어 있는지 확인합니다.

    $this->assertDatabaseHas('users', [
        'email' => 'sally@example.com',
    ]);

<a name="assert-database-missing"></a>
#### assertDatabaseMissing

데이터베이스의 테이블에 주어진 키 값 쿼리 제약 조건과 일치하는 레코드가 포함되어 있지 않음을 확인합니다.

    $this->assertDatabaseMissing('users', [
        'email' => 'sally@example.com',
    ]);

<a name="assert-deleted"></a>
#### assertSoftDeleted

`assertSoftDeleted` 메소드는 주어진 Eloquent 모델이 "소프트 삭제"되었다고 확인하기 위해 사용될 수 있습니다.

    $this->assertSoftDeleted($user);

<a name="assert-not-deleted"></a>
#### assertNotSoftDeleted

`assertNotSoftDeleted` 메서드는 주어진 엘로퀀트 모델이 "소프트 삭제" 되지 않았는지 확인합니다.

    $this->assertNotSoftDeleted($user);

<a name="assert-model-exists"></a>
#### assertModelExists

주어진 모델이 데이터베이스에 존재하는지 확인합니다.

    use App\Models\User;

    $user = User::factory()->create();

    $this->assertModelExists($user);

<a name="assert-model-missing"></a>
#### assertModelMissing

주어진 모델이 데이터베이스에 존재하지 않는지 확인합니다.

    use App\Models\User;

    $user = User::factory()->create();

    $user->delete();

    $this->assertModelMissing($user);

<a name="expects-database-query-count"></a>
#### expectsDatabaseQueryCount

The `expectsDatabaseQueryCount` method may be invoked at the beginning of your test to specify the total number of database queries that you expect to be run during the test. If the actual number of executed queries does not exactly match this expectation, the test will fail:

`expectsDatabaseQueryCount` 메서드는 테스트가 시작할 때, 이 테스트 도중에 실행될 것으로 기대되는 전체 데이터베이스 쿼리 수를 지정할 수 있습니다. 실행된 쿼리의 수가, 예상치와 일치하지 않으면 테스트가 실패합니다.

    $this->expectsDatabaseQueryCount(5);

    // Test...
