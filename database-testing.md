# 데이터베이스 테스팅

- [소개하기](#introduction)
- [각각의 테스트 수행 후에 데이터베이스 재설정하기](#resetting-the-database-after-each-test)
    - [마이그레이션 이용하기](#using-migrations)
    - [트랜잭션 이용하기](#using-transactions)
- [팩토리 작성하기](#writing-factories)
    - [팩토리 상태(States)](#factory-states)
- [팩토리 사용하기](#using-factories)
    - [모델 생성하기](#creating-models)
    - [모델 저장하기](#persisting-models)
    - [관계-Relationships](#relationships)

<a name="introduction"></a>
## 소개하기

라라벨은 데이터베이스를 기반으로 하는 애플리케이션을 손쉽게 테스트 할수 있도록 도와주는 다양한 툴을 제공합니다. 우선 `seeInDatabase` 헬퍼를 이용하여 데이터베이스 안에 특정 조건을 만족하는 데이터가 존재하는지 확인할 수 있습니다. 예를 들어, `users` 테이블에 `sally@example.com`의 `email` 값을 가진 레코드가 존재하는지 확인하기 위해 다음과 같이 할 수 있습니다:

    public function testDatabase()
    {
        // Make call to application...

        $this->seeInDatabase('users', [
            'email' => 'sally@example.com'
        ]);
    }

Of course, the `seeInDatabase` method and other helpers like it are for convenience. You are free to use any of PHPUnit's built-in assertion methods to supplement your tests.

`seeInDatabase` 메소드와 기타 다른 헬퍼들은 사용하기에 더 편리합니다. PHPUnit의 테스트 구문에서 자유롭게 이 함수들을 사용할 수 있습니다.

<a name="resetting-the-database-after-each-test"></a>
## 각각의 테스트 수행 후에 데이터베이스 재설정하기

종종 이전의 테스트를 위한 데이터가 다음에 이어지는 테스트들을 방해하는 것을 방지하기 위해 각 테스트 후에 데이터베이스를 재설정하는 것이 유용합니다.

<a name="using-migrations"></a>
### 마이그레이션 이용하기

데이터베이스를 재설정하는 하나의 방법은 각각의 테스트 수행 후에 데이터베이스를 롤백하고 다음 테스트를 수행하기 전에 다시 마이그레이션을 실행하는 것입니다. 라라벨은 `DatabaseMigrations` 트레이트-trait을 제공하여 이를 자동으로 처리 해줍니다. 테스트 클래스에서 이 트레이트-trait을 사용하기만 하면, 모든게 처리될것입니다:

    <?php

    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Foundation\Testing\DatabaseMigrations;
    use Illuminate\Foundation\Testing\DatabaseTransactions;

    class ExampleTest extends TestCase
    {
        use DatabaseMigrations;

        /**
         * A basic functional test example.
         *
         * @return void
         */
        public function testBasicExample()
        {
            $this->visit('/')
                 ->see('Laravel 5');
        }
    }

<a name="using-transactions"></a>
### 트랜잭션 이용하기

데이터베이스를 재설정하는 다른 방법으로는 데이터베이스 트랜잭션으로 모든 테스트 케이스를 감싸는 것입니다. 이 때에도 라라벨은 편리하게 `DatabaseTransactions` 트레이트-trait을 제공하여 이것이 자동으로 처리되도록 합니다:

    <?php

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
            $this->visit('/')
                 ->see('Laravel 5');
        }
    }

> {note} 기본적으로, 이 트레이트-trait은 트랜잭션의 기본 데이터베이스 커넥션만을 랩핑(wrap) 하게 됩니다. 만약 애플리케이션에서 여러개의 데이터베이스 커넥션을 사용중이라면, 테스트 클래스에 `$connectionsToTransact` 속성을 정의해야 합니다. 수동으로 이 커넥션들을 위한 트랙잭션 처리를 할 필요가 있을 것입니다. 이 속성은 트랜젝션이 실행될 커넥션 이름의 배열이어야 합니다.

<a name="writing-factories"></a>
## 팩토리 작성하기

테스트를 실행하기 데이터베이스에 몇몇 레코드를 입력하는 것이 필요할 수 있습니다. 이 테스트 데이터를 생성할 때 수동으로 각각의 컬럼의 값을 지정하는 대신에 라라벨은 모델 팩토리를 사용하여 각각의 [Eloquent 모델](/docs/{{version}}/eloquent)을 위한 기본 속성의 세트를 정의하도록 해줍니다. 먼저 애플리케이션의 `database/factories/ModelFactory.php` 파일을 살펴보겠습니다. 이 파일은 바로 사용이 가능한 하나의 팩토리 정의를 가지고 있습니다.

    $factory->define(App\User::class, function (Faker\Generator $faker) {
        static $password;

        return [
            'name' => $faker->name,
            'email' => $faker->unique()->safeEmail,
            'password' => $password ?: $password = bcrypt('secret'),
            'remember_token' => str_random(10),
        ];
    });

팩토리의 정의를 제공하는 클로저 안에서 모든 모델의 속성의 기본 테스트 값을 반환할 수 있습니다. 클로저는 [Faker](https://github.com/fzaninotto/Faker) PHP 라이브러리 인스턴스를 전달 받을 것이고, 이는 테스트할 수 있는 다양한 랜덤 데이터를 편리하게 생성할 수 있게 해줍니다.

물론 `ModelFactory.php` 파일에 여러분의 고유한 팩토리들을 자유롭게 추가할 수 있습니다. 여러분은 또한 보다 나은 구성을 위해서 각각의 모델들을 위한 추가적인 팩토리 파일들을 생성할 수도 있습니다. 예를 들어 `database/factories` 디렉토리에 `UserFactory.php` 와 `CommentFactory.php` 파일을 생성할 수 있습니다. `factories` 디렉토리 안에 들어 있는 모든 파일들은 라라벨에 의해서 자동으로 로딩됩니다.

<a name="factory-states"></a>
### 팩토리 상태(States)

상태를 사용하면 어떤 조합의 모델 팩토리에 대해서라도 적용가능한 별개의 수정사항을 정의할 수 있습니다. 예를 들어 `User` 모델이 가본 속성 값을 변경하는 `delinquent` 상태를 가지고 있다고 가정해보겠습니다. 여러분은 `state` 메소드를 사용하여 상태 변경을 정의할 수 있습니다.

    $factory->state(App\User::class, 'delinquent', function ($faker) {
        return [
            'account_status' => 'delinquent',
        ];
    });

<a name="using-factories"></a>
## 팩토리 사용하기

<a name="creating-models"></a>
### 모델 생성하기

팩토리를 정의한 뒤에는, 테스트파일 에서나 모델 인스턴스를 생성하기 위한 시드 파일안에서 글로벌 `factory` 함수를 사용할 수 있습니다. 그럼 모델들을 생성하는 몇몇의 예제를 살펴보겠습니다. 우선 모델을 생성하지만 데이터베이스에 저장하지는 않는, `make` 메소드를 사용할 것입니다:

    public function testDatabase()
    {
        $user = factory(App\User::class)->make();

        // Use model in tests...
    }

또한 여러 모델의 컬렉션을 생성하거나 주어진 타입의 모델을 생성할 수도 있습니다:

    // Create three App\User instances...
    $users = factory(App\User::class, 3)->make();

#### 상태(States) 적용하기

어떤 모델이라도 [팩토리 상태(states)](#factory-states)를 적용할 수 있습니다. 모델에 여러개의 팩토리 상태(state) 변경을 지정하고자 한다면, 다음과 같이 각각의 팩토리 상태(state)들의 이름을 지정하면 됩니다:

    $users = factory(App\User::class, 5)->states('delinquent')->make();

    $users = factory(App\User::class, 5)->states('premium', 'delinquent')->make();

#### 속성 재지정(오버라이딩) 하기

모델의 몇몇 기본값들을 대체하고 싶다면 `make` 메소드로 값들의 배열을 전달하면 됩니다. 지정된 값들만 대체되고 나머지 값들은 팩토리에 의해 지정된 기본값들을 유지할 것입니다.

    $user = factory(App\User::class)->make([
        'name' => 'Abigail',
    ]);

<a name="persisting-models"></a>
### 모델 저장하기

`create` 메소드는 모델 인스턴스를 단지 생성하기만 하기 때문에 데이터베이스에 저장하려면, Eloquent의 `save` 메소드를 사용하면 됩니다:

    public function testDatabase()
    {
        // Create a single App\User instance...
        $user = factory(App\User::class)->create();

        // Create three App\User instances...
        $users = factory(App\User::class, 3)->create();

        // Use model in tests...
    }

`create` 메소드에 배열을 전달하여 모델의 속성들을 재지정 할 수 있습니다:

    $user = factory(App\User::class)->create([
        'name' => 'Abigail',
    ]);

<a name="relationships"></a>
### 관계(relationships)

이 예제에서는, 생성된 몇몇의 모델에 관계를 추가할 것입니다. 여러 모델을 생성하기 위해서 `create` 메소드를 사용할 때 Eloquent [컬렉션 인스턴스](/docs/{{version}}/eloquent-collections)가 반환되어 `each`와 같이 컬렉션이 제공하는 편리한 함수들을 이용할 수 있도록 해줍니다:

    $users = factory(App\User::class, 3)
               ->create()
               ->each(function ($u) {
                    $u->posts()->save(factory(App\Post::class)->make());
                });

#### 관계 & 속성 클로저-Closures

팩토리 정의안에서 클로저를 속성으로 사용하여 모델에 관계를 추가할 수도 있습니다. 예를 들어 `Post` 가 생성될 때 `User` 인스턴스를 생성하고자 한다면, 다음처럼 하면 됩니다:

    $factory->define(App\Post::class, function ($faker) {
        return [
            'title' => $faker->title,
            'content' => $faker->paragraph,
            'user_id' => function () {
                return factory(App\User::class)->create()->id;
            }
        ];
    });

이 클로저는 팩토리의 계산된 속성 배열을 받을 수도 있습니다.

    $factory->define(App\Post::class, function ($faker) {
        return [
            'title' => $faker->title,
            'content' => $faker->paragraph,
            'user_id' => function () {
                return factory(App\User::class)->create()->id;
            },
            'user_type' => function (array $post) {
                return App\User::find($post['user_id'])->type;
            }
        ];
    });
