# 데이터베이스 테스팅

- [시작하기](#introduction)
- [팩토리 생성하기](#generating-factories)
- [각각의 테스트 수행 후에 데이터베이스 재설정하기](#resetting-the-database-after-each-test)
- [팩토리 작성하기](#writing-factories)
    - [팩토리 확장](#extending-factories)
    - [팩토리 상태(States)](#factory-states)
    - [팩토리 콜백](#factory-callbacks)
- [팩토리 사용하기](#using-factories)
    - [모델 생성하기](#creating-models)
    - [모델 저장하기](#persisting-models)
    - [Relationships-관계](#relationships)
- [Seed 사용](#using-seeds)
- [사용 가능한 Assertions](#available-assertions)

<a name="introduction"></a>
## 시작하기

라라벨은 데이터베이스를 기반으로 하는 애플리케이션을 손쉽게 테스트 할수 있도록 도와주는 다양한 툴을 제공합니다. 우선 `assertDatabaseHas` 헬퍼를 이용하여 데이터베이스 안에 특정 조건을 만족하는 데이터가 존재하는지 확인할 수 있습니다. 예를 들어, `users` 테이블에 `sally@example.com`의 `email` 값을 가진 레코드가 존재하는지 확인하기 위해 다음과 같이 할 수 있습니다.

    public function testDatabase()
    {
        // Make call to application...

        $this->assertDatabaseHas('users', [
            'email' => 'sally@example.com',
        ]);
    }

또한 데이터베이스에 데이터가 존재하지 않는 것을 확인하기 위해서 `assertDatabaseMissing` 헬퍼 함수를 사용할 수 있습니다.

`assertDatabaseHas` 메소드와 기타 다른 헬퍼들은 사용하기에 더 편리합니다. PHPUnit의 피쳐 테스트 구문에서 자유롭게 이 함수들을 사용할 수 있습니다.

<a name="generating-factories"></a>
## 팩토리 생성하기

팩토리 클래스를 생성하기 위해서는 `make:factory` [아티즌 명령어](/docs/{{version}}/artisan)를 사용합니다.

    php artisan make:factory PostFactory

새로운 팩토리는 `database/factories` 디렉토리에 생성됩니다.

팩토리에 의해서 생성될 모델의 이름을 지정하고자 한다면 `--model` 옵션을 사용하면 됩니다. 이 옵션은 생성된 팩토리 파일을 주어진 모델로 미리 채워둡니다.

    php artisan make:factory PostFactory --model=Post

<a name="resetting-the-database-after-each-test"></a>
## 각각의 테스트 수행 후에 데이터베이스 재설정하기

종종 이전의 테스트를 위한 데이터가 다음에 이어지는 테스트들을 방해하는 것을 막기 위해 각각의 테스트가 끝난 뒤에 데이터베이스를 재설정하는 것이 유용합니다. `RefreshDatabase` 트레이트-trait 은 전통적인 데이터베이스 또는 in-memory 데이터베이스를 사용하는 경우 여러분의 테스트 데이터베이스를 마이그레이션 하는데 가장 적합한 방법입니다. 테스트 클래스에 트레이트-trait를 사용하면 손쉽게 처리됩니다.

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
        public function testBasicExample()
        {
            $response = $this->get('/');

            // ...
        }
    }

<a name="using-transactions"></a>
### 트랜잭션 이용하기

데이터베이스를 재설정하는 다른 방법으로는 데이터베이스 트랜잭션으로 모든 테스트 케이스를 감싸는 것입니다. 이 때에도 라라벨은 편리하게 `DatabaseTransactions` 트레이트-trait을 제공하여 이것이 자동으로 처리되도록 합니다.

    <?php

    namespace Tests\Feature;

    use Tests\TestCase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Foundation\Testing\DatabaseMigrations;
    use Illuminate\Foundation\Testing\DatabaseTransactions;

    class ExampleTest extends TestCase
    {
        use DatabaseTransactions;

        /**
         * A basic functional test example.
         *
         * @return void
         */
        public function testBasicExample()
        {
            $response = $this->get('/');

            // ...
        }
    }

<a name="writing-factories"></a>
## 팩토리 작성하기

테스트를 할 때, 실행하기 전 데이터베이스에 몇몇 레코드를 입력하는 것이 필요할 수 있습니다. 이 테스트 데이터를 생성할 때 수동으로 각각의 컬럼의 값을 지정하는 대신에 라라벨은 모델 팩토리를 사용하여 각각의 [Eloquent 모델](/docs/{{version}}/eloquent)을 위한 기본 속성의 세트를 정의하도록 해줍니다. 먼저 애플리케이션의 `database/factories/UserFactory.php` 파일을 살펴보겠습니다. 이 파일은 바로 사용이 가능한 하나의 팩토리 정의를 가지고 있습니다.

    use Faker\Generator as Faker;
    use Illuminate\Support\Str;

    $factory->define(App\User::class, function (Faker $faker) {
        return [
            'name' => $faker->name,
            'email' => $faker->unique()->safeEmail,
            'email_verified_at' => now(),
            'password' => '$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi', // password
            'remember_token' => Str::random(10),
        ];
    });

팩토리의 정의를 제공하는 클로저 안에서 모든 모델의 속성의 기본 테스트 값을 반환할 수 있습니다. 클로저는 [Faker](https://github.com/fzaninotto/Faker) PHP 라이브러리 인스턴스를 전달 받을 것이고, 이는 테스트할 수 있는 다양한 랜덤 데이터를 편리하게 생성할 수 있게 해줍니다.

여러분은 또한 보다 나은 구성을 위해서 각각의 모델들을 위한 추가적인 팩토리 파일들을 생성할 수도 있습니다. 예를 들어 `database/factories` 디렉토리에 `UserFactory.php` 와 `CommentFactory.php` 파일을 생성할 수 있습니다. `factories` 디렉토리 안에 들어 있는 모든 파일들은 라라벨에 의해서 자동으로 로딩됩니다.

> {tip} `config/app.php` 설정 파일에 `faker_locale` 옵션을 추가하여 Faker 로케일을 설정할 수 있습니다.

<a name="extending-factories"></a>
### 팩토리 확장

모델을 확장 한 경우 테스트 및 시드 중에 하위 모델의 팩토리 속성을 활용하기 위해 팩토리를 확장 할 수도 있습니다. 이를 달성하기 위해 팩토리 빌더의 `raw` 메소드를 호출하여 주어진 팩토리에서 속성의 원시 배열을 얻을 수 있습니다.

    $factory->define(App\Admin::class, function (Faker\Generator $faker) {
        return factory(App\User::class)->raw([
            // ...
        ]);
    });

<a name="factory-states"></a>
### 팩토리 상태(States)

상태를 통하면, 모델 팩토리에서 적용 할 수 있는 개별 수정사항을 정의 할 수 있습니다. 예를 들어, `User` 모델이 기본 속성 값 중에 하나를 변경하는 `delinquent` 상태를 가지고 있다고 가정해보겠습니다. 여러분은 `state` 메소드를 사용하여 상태 변경을 정의할 수 있습니다. 간단한 상태를 정의한다면, 속성값들의 변경사항을 배열로 전달 할 수 있습니다.

    $factory->state(App\User::class, 'delinquent', [
        'account_status' => 'delinquent',
    ]);

상태에 `$faker` 인스턴스가 필요한 경우라면, Closure를 사용하여 상태의 속성에 대한 변경사항을 지정할 수 있습니다.

    $factory->state(App\User::class, 'address', function ($faker) {
        return [
            'address' => $faker->address,
        ];
    });

<a name="factory-callbacks"></a>
### 팩토리 콜백

팩토리 콜백은 `afterMaking` 와 `afterCreating` 메소드를 사용하여 등록합니다. 이는 모델을 생성할 때나 생성된 다음에 추가적인 작업을 수행할 수 있게 해줍니다. 예를 들어 콜백을 사용하여 생성된 모델과, 추가 모델을 서로 연결할 수 있습니다.

    $factory->afterMaking(App\User::class, function ($user, $faker) {
        // ...
    });

    $factory->afterCreating(App\User::class, function ($user, $faker) {
        $user->accounts()->save(factory(App\Account::class)->make());
    });

또한 [팩토리 상태](#factory-states)에 대한 콜백을 정의할 수도 있습니다.

    $factory->afterMakingState(App\User::class, 'delinquent', function ($user, $faker) {
        // ...
    });

    $factory->afterCreatingState(App\User::class, 'delinquent', function ($user, $faker) {
        // ...
    });

<a name="using-factories"></a>
## 팩토리 사용하기

<a name="creating-models"></a>
### 모델 생성하기

팩토리를 정의한 뒤에는, 피쳐 테스트나 모델 인스턴스를 생성하기 위한 시드 파일안에서 글로벌 `factory` 함수를 사용할 수 있습니다. 그럼 모델들을 생성하는 몇몇의 예제를 살펴보겠습니다. 우선 모델을 생성하지만 데이터베이스에 저장하지는 않는, `make` 메소드를 사용할 것입니다.

    public function testDatabase()
    {
        $user = factory(App\User::class)->make();

        // Use model in tests...
    }

또한 여러 모델의 컬렉션을 생성하거나 주어진 타입의 모델을 생성할 수도 있습니다.

    // Create three App\User instances...
    $users = factory(App\User::class, 3)->make();

#### 상태(States) 적용하기

어떤 모델이라도 [팩토리 상태(states)](#factory-states)를 적용할 수 있습니다. 모델에 여러개의 팩토리 상태(state) 변경을 지정하고자 한다면, 다음과 같이 각각의 팩토리 상태(state)들의 이름을 지정하면 됩니다.

    $users = factory(App\User::class, 5)->states('delinquent')->make();

    $users = factory(App\User::class, 5)->states('premium', 'delinquent')->make();

#### 속성 재지정(오버라이딩) 하기

모델의 몇몇 기본값들을 대체하고 싶다면 `make` 메소드로 값들의 배열을 전달하면 됩니다. 지정된 값들만 대체되고 나머지 값들은 팩토리에 의해 지정된 기본값들을 유지할 것입니다.

    $user = factory(App\User::class)->make([
        'name' => 'Abigail',
    ]);

> {tip} [Mass assignment protection](/docs/{{version}}/eloquent#mass-assignment) is automatically disabled when creating models using factories.

<a name="persisting-models"></a>
### 모델 저장하기

`create` 메소드는 모델 인스턴스를 단지 생성하기만 하기 때문에 데이터베이스에 저장하려면, Eloquent의 `save` 메소드를 사용하면 됩니다.

    public function testDatabase()
    {
        // Create a single App\User instance...
        $user = factory(App\User::class)->create();

        // Create three App\User instances...
        $users = factory(App\User::class, 3)->create();

        // Use model in tests...
    }

`create` 메소드에 배열을 전달하여 모델의 속성들을 재지정 할 수 있습니다.

    $user = factory(App\User::class)->create([
        'name' => 'Abigail',
    ]);

<a name="relationships"></a>
### 관계(relationships)

이 예제에서는, 생성된 몇몇의 모델에 관계를 추가할 것입니다. 여러 모델을 생성하기 위해서 `create` 메소드를 사용할 때 Eloquent [컬렉션 인스턴스](/docs/{{version}}/eloquent-collections)가 반환되어 `each`와 같이 컬렉션이 제공하는 편리한 함수들을 이용할 수 있도록 해줍니다.

    $users = factory(App\User::class, 3)
               ->create()
               ->each(function ($user) {
                    $user->posts()->save(factory(App\Post::class)->make());
                });

`createMany` 메소드를 사용하여 여러개의 연관 모델을 작성할 수 있습니다.

    $user->posts()->createMany(
        factory(App\Post::class, 3)->make()->toArray()
    );

#### 관계 & 속성 클로저-Closures

팩토리 정의에서 모델에 관계를 추가 할 수도 있습니다. 예를 들어, `Post`를 생성할 때 새로운 `User` 인스턴스를 생성하려면 다음처럼 하면 됩니다.

    $factory->define(App\Post::class, function ($faker) {
        return [
            'title' => $faker->title,
            'content' => $faker->paragraph,
            'user_id' => factory(App\User::class),
        ];
    });

팩토리에 정의된 관계에 의존해야하는 경우 evaluated 된 속성 배열을 입력받는 콜백을 사용 할 수 있습니다.

    $factory->define(App\Post::class, function ($faker) {
        return [
            'title' => $faker->title,
            'content' => $faker->paragraph,
            'user_id' => factory(App\User::class),
            'user_type' => function (array $post) {
                return App\User::find($post['user_id'])->type;
            },
        ];
    });

<a name="using-seeds"></a>
## Seed 사용

피쳐 테스트 도중 [database seeders](/docs/{{version}}/seeding)를 사용하여 데이터베이스를 채워 넣으려면 `seed` 메소드를 사용할 수 있습니다. 기본적으로`seed` 메소드는 `DatabaseSeeder`를 반환하는데, 이 메소드는 다른 모든 seeder를 실행해야 합니다. 또는 특정 seeder 클래스 이름을 `seed` 메소드에 전달하십시오.

    <?php

    namespace Tests\Feature;

    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use OrderStatusesTableSeeder;
    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        use RefreshDatabase;

        /**
         * Test creating a new order.
         *
         * @return void
         */
        public function testCreatingANewOrder()
        {
            // Run the DatabaseSeeder...
            $this->seed();

            // Run a single seeder...
            $this->seed(OrderStatusesTableSeeder::class);

            // ...
        }
    }

<a name="available-assertions"></a>
## 사용 가능한 Assertions

라라벨은 [PHPUnit](https://phpunit.de/) 피쳐 테스트에서 사용가능한 데이터데이스 assertion을 제공합니다.

메소드 | 설명
------------- | -------------
`$this->assertDatabaseHas($table, array $data);`  |  데이터베이스에서 테이블에 주어진 데이터가 존재하는지 확인.
`$this->assertDatabaseMissing($table, array $data);`  |  데이터베이스에서 테이블에 주어진 데이터가 존재하지 않는 것을 확인.
`$this->assertDeleted($table, array $data);`  |  주어진 레코드가 삭제되었는지 확인.
`$this->assertSoftDeleted($table, array $data);`  |  주어진 레코드가 소프트 삭제 되었는지 확인.

편의상 모델의 기본 키를 기반으로 데이터베이스에서 레코드가 삭제 또는 소프트 삭제되었는지 확인하도록 모델을 `assertDeleted` 및 `assertSoftDeleted` 헬퍼에 전달 할 수 있습니다.

예를 들어 테스트에서 모델 팩토리를 사용하는 경우, 이 모델을 이 헬퍼들 중 하나에 전달하여 애플리케이션이 데이터베이스에서 레코드를 올바르게 삭제했는지 테스트 할 수 있습니다.

    public function testDatabase()
    {
        $user = factory(App\User::class)->create();

        // Make call to application...

        $this->assertDeleted($user);
    }
