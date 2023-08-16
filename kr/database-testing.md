# Database Testing
# 데이터베이스 테스팅

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [Resetting The Database After Each Test](#resetting-the-database-after-each-test)
    - [각각의 테스트 수행 후에 데이터베이스 재설정하기](#resetting-the-database-after-each-test)
- [Model Factories](#model-factories)
- [모델 팩토리](#model-factories)
- [Running Seeders](#running-seeders)
- [시더 실행하기](#running-seeders)
- [Available Assertions](#available-assertions)
- [사용 가능한 검증-Assertions](#available-assertions)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel provides a variety of helpful tools and assertions to make it easier to test your database driven applications. In addition, Laravel model factories and seeders make it painless to create test database records using your application's Eloquent models and relationships. We'll discuss all of these powerful features in the following documentation.

라라벨은 데이터베이스 기반 애플리케이션을 더 쉽게 테스트할 수 있도록 다양한 유용한 도구와 검증을 제공합니다. 또한 라라벨 모델 팩토리와 시더를 사용하면 애플리케이션의 Eloquent 모델과 관계를 사용하여 테스트 데이터베이스 레코드를 손쉽게 생성할 수 있습니다. 다음 문서에서 이러한 모든 강력한 기능에 대해 설명합니다.

<a name="resetting-the-database-after-each-test"></a>
### Resetting The Database After Each Test
### 각각의 테스트 수행 후에 데이터베이스 재설정하기

Before proceeding much further, let's discuss how to reset your database after each of your tests so that data from a previous test does not interfere with subsequent tests. Laravel's included `Illuminate\Foundation\Testing\RefreshDatabase` trait will take care of this for you. Simply use the trait on your test class:

더 진행하기 전에 이전 테스트의 데이터가 후속 테스트를 방해하지 않도록 각 테스트 후에 데이터베이스를 재설정하는 방법에 대해 논의하겠습니다. 라라벨에 포함된 `Illuminate\Foundation\Testing\RefreshDatabase` 트레이트-trait이 이 문제를 처리합니다. 테스트 클래스에서 트레이트-trait을 사용하기만 하면 됩니다.

    <?php

    namespace Tests\Feature;

    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        use RefreshDatabase;

        /**
         * A basic functional test example.
         */
        public function test_basic_example(): void
        {
            $response = $this->get('/');

            // ...
        }
    }

The `Illuminate\Foundation\Testing\RefreshDatabase` trait does not migrate your database if your schema is up to date. Instead, it will only execute the test within a database transaction. Therefore, any records added to the database by test cases that do not use this trait may still exist in the database.

`Illuminate\Foundation\Testing\RefreshDatabase` 트레이트-trait는 스키마가 최신상태인 경우에 데이터베이스 마이그레이션을 수행하지 않습니다. 대신에, 데이터베이스 트랜잭션 안에서만 테스트를 실행합니다. 따라서 데이터베이스에 이 트레이트-trait을 사용하지 않는 테스트 케이스에 의해 추가된 레코드는 계속 남아 있을 수 있습니다. 

If you would like to totally reset the database, you may use the `Illuminate\Foundation\Testing\DatabaseMigrations` or `Illuminate\Foundation\Testing\DatabaseTruncation` traits instead. However, both of these options are significantly slower than the `RefreshDatabase` trait.

데이터베이스를 완전히 리셋하려면 `Illuminate\Foundation\Testing\DatabaseMigrations` 또는 `Illuminate\Foundation\Testing\DatabaseTruncation` 트레이트-trait을 사용할 수 있습니다. 하지만 이 두 옵션은 `RefreshDatabase` 보다 훨씬 느립니다.

<a name="model-factories"></a>
## Model Factories
## 모델 팩토리

When testing, you may need to insert a few records into your database before executing your test. Instead of manually specifying the value of each column when you create this test data, Laravel allows you to define a set of default attributes for each of your [Eloquent models](/docs/{{version}}/eloquent) using [model factories](/docs/{{version}}/eloquent-factories).

테스트를 할 때 테스트를 실행하기 전에 레코드를 데이터베이스에 넣어둘 필요가 있습니다. 이 테스트 데이터를 생성할 때 각 열의 값을 수동으로 지정하는 대신, 라라벨을 사용하면 [모델 팩토리](/docs/{{version}}/eloquent-factories) 를 사용하여 각 [엘로퀀트 모델](/docs/{{version}}/eloquent) 에 대한 기본 속성 세트를 정의할 수 있습니다.

To learn more about creating and utilizing model factories to create models, please consult the complete [model factory documentation](/docs/{{version}}/eloquent-factories). Once you have defined a model factory, you may utilize the factory within your test to create models:

모델 팩토리를 생성하고 활용하여 모델을 생성하는 방법에 대해 자세히 알아보려면 전체 [모델 팩토리 문서](/docs/{{version}}/eloquent-factories)를 참조하십시오 . 모델 팩토리를 정의한 후에는 테스트 내에서 팩토리를 활용하여 모델을 생성할 수 있습니다.

    use App\Models\User;

    public function test_models_can_be_instantiated(): void
    {
        $user = User::factory()->create();

        // ...
    }

<a name="running-seeders"></a>
## Running Seeders
## 시더 실행하기

If you would like to use [database seeders](/docs/{{version}}/seeding) to populate your database during a feature test, you may invoke the `seed` method. By default, the `seed` method will execute the `DatabaseSeeder`, which should execute all of your other seeders. Alternatively, you pass a specific seeder class name to the `seed` method:

기능 테스트 중에 [database seeders](/docs/{{version}}/seeding)를 사용하여 데이터베이스를 채우려면 `seed` 메서드를 호출할 수 있습니다. 기본적으로 `seed` 메소드는 다른 모든 시드를 실행해야 하는 `DatabaseSeeder`를 실행합니다. 또는 `seed` 메서드에 특정 시드 클래스 이름을 전달합니다.

    <?php

    namespace Tests\Feature;

    use Database\Seeders\OrderStatusSeeder;
    use Database\Seeders\TransactionStatusSeeder;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        use RefreshDatabase;

        /**
         * Test creating a new order.
         */
        public function test_orders_can_be_created(): void
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

Alternatively, you may instruct Laravel to automatically seed the database before each test that uses the `RefreshDatabase` trait. You may accomplish this by defining a `$seed` property on your base test class:

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

When the `$seed` property is `true`, the test will run the `Database\Seeders\DatabaseSeeder` class before each test that uses the `RefreshDatabase` trait. However, you may specify a specific seeder that should be executed by defining a `$seeder` property on your test class:

`$seed` 속성이 `true`면 테스트는 `RefreshDatabase` 트레이트-trait을 사용하는 각 테스트 전에 `Database\Seeders\DatabaseSeeder` 클래스를 실행합니다. 그러나 테스트 클래스에 `seeder` 속성을 정의하여 실행해야 하는 특정 시드를 지정할 수 있습니다.

    use Database\Seeders\OrderStatusSeeder;

    /**
     * Run a specific seeder before each test.
     *
     * @var string
     */
    protected $seeder = OrderStatusSeeder::class;

<a name="available-assertions"></a>
## Available Assertions
## 사용 가능한 검증-Assertions

Laravel provides several database assertions for your [PHPUnit](https://phpunit.de/) feature tests. We'll discuss each of these assertions below.

라라벨은 [PHPUnit](https://phpunit.de/) 기능 테스트를 위한 여러 데이터베이스 검증을 제공합니다. 아래에서는 이러한 각 검증에 대해 살펴볼 것입니다.

<a name="assert-database-count"></a>
#### assertDatabaseCount
#### assertDatabaseCount

Assert that a table in the database contains the given number of records:

데이터베이스의 테이블에 지정된 수의 레코드가 포함되어 있는지 확인합니다.

    $this->assertDatabaseCount('users', 5);

<a name="assert-database-has"></a>
#### assertDatabaseHas
#### assertDatabaseHas

Assert that a table in the database contains records matching the given key / value query constraints:

데이터베이스의 테이블에 주어진 키 값 쿼리 제약 조건과 일치하는 레코드가 포함되어 있는지 확인합니다.

    $this->assertDatabaseHas('users', [
        'email' => 'sally@example.com',
    ]);

<a name="assert-database-missing"></a>
#### assertDatabaseMissing
#### assertDatabaseMissing

Assert that a table in the database does not contain records matching the given key / value query constraints:

데이터베이스의 테이블에 주어진 키 값 쿼리 제약 조건과 일치하는 레코드가 포함되어 있지 않음을 확인합니다.

    $this->assertDatabaseMissing('users', [
        'email' => 'sally@example.com',
    ]);

<a name="assert-deleted"></a>
#### assertSoftDeleted
#### assertSoftDeleted

The `assertSoftDeleted` method may be used to assert a given Eloquent model has been "soft deleted":

`assertSoftDeleted` 메소드는 주어진 Eloquent 모델이 "소프트 삭제"되었다고 확인하기 위해 사용될 수 있습니다.

    $this->assertSoftDeleted($user);
    
<a name="assert-not-deleted"></a>
#### assertNotSoftDeleted

The `assertNotSoftDeleted` method may be used to assert a given Eloquent model hasn't been "soft deleted":

`assertNotSoftDeleted` 메서드는 주어진 엘로퀀트 모델이 "소프트 삭제" 되지 않았는지 확인합니다.

    $this->assertNotSoftDeleted($user);

<a name="assert-model-exists"></a>
#### assertModelExists
#### assertModelExists

Assert that a given model exists in the database:

주어진 모델이 데이터베이스에 존재하는지 확인합니다.

    use App\Models\User;

    $user = User::factory()->create();

    $this->assertModelExists($user);

<a name="assert-model-missing"></a>
#### assertModelMissing
#### assertModelMissing

Assert that a given model does not exist in the database:

주어진 모델이 데이터베이스에 존재하지 않는지 확인합니다.

    use App\Models\User;

    $user = User::factory()->create();

    $user->delete();

    $this->assertModelMissing($user);

<a name="expects-database-query-count"></a>
#### expectsDatabaseQueryCount
#### expectsDatabaseQueryCount

The `expectsDatabaseQueryCount` method may be invoked at the beginning of your test to specify the total number of database queries that you expect to be run during the test. If the actual number of executed queries does not exactly match this expectation, the test will fail:

`expectsDatabaseQueryCount` 메서드는 테스트 시작 시 테스트 중 실행될 데이터베이스 쿼리의 총 개수를 지정하는 데 사용할 수 있습니다. 실제로 실행된 쿼리의 개수가 이 기대치와 정확히 일치하지 않으면 테스트가 실패합니다.

    $this->expectsDatabaseQueryCount(5);

    // Test...
