# Database Testing
# 데이터베이스 테스팅

- [Introduction](#introduction)
- [시작하기](#introduction)
  - [Resetting The Database After Each Test](#resetting-the-database-after-each-test)
  - [각각의 테스트 수행 후에 데이터베이스 재설정하기](#resetting-the-database-after-each-test)
- [Defining Model Factories](#defining-model-factories)
- [모델 팩토리 정의](#defining-model-factories)
  - [Concept Overview](#concept-overview)
  - [개념 소개](#concept-overview)
  - [Generating Factories](#generating-factories)
  - [팩토리 생성하기](#generating-factories)
  - [Factory States](#factory-states)
  - [팩토리 상태](#factory-states)
  - [Factory Callbacks](#factory-callbacks)
  - [팩토리 콜백](#factory-callbacks)
- [Creating Models Using Factories](#creating-models-using-factories)
- [팩토리를 사용하여 모델 생성](#creating-models-using-factories)
  - [Instantiating Models](#instantiating-models)
  - [모델 인스턴스화](#instantiating-models)
  - [Persisting Models](#persisting-models)
  - [모델 저장하기](#persisting-models)
  - [Sequences](#sequences)
  - [시퀀스](#sequences)
- [Factory Relationships](#factory-relationships)
- [팩토리 관계](#factory-relationships)
  - [Has Many Relationships](#has-many-relationships)
  - [1:*(일대다) 관계](#has-many-relationships)
  - [Belongs To Relationships](#belongs-to-relationships)
  - [1:*(일대다) 역관계](#belongs-to-relationships)
  - [Many To Many Relationships](#many-to-many-relationships)
  - [\*:*(다대다) 관계](#many-to-many-relationships)
  - [Polymorphic Relationships](#polymorphic-relationships)
  - [다형성 관계](#polymorphic-relationships)
  - [Defining Relationships Within Factories](#defining-relationships-within-factories)
  - [팩토리 내 관계 정의](#defining-relationships-within-factories)
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

The `Illuminate\Foundation\Testing\RefreshDatabase` trait does not migrate your database if your schema is up to date. Instead, it will only execute the test within a database transaction. Therefore, any records added to the database by test cases that do not use this trait may still exist in the database.

`Illuminate\Foundation\Testing\RefreshDatabase` 트레이트-trait는 스키마가 최신상태인 경우에 데이터베이스 마이그레이션을 수행하지 않습니다. 대신에, 데이터베이스 트랜잭션 안에서만 테스트를 실행합니다. 따라서 데이터베이스에 이 트레이트-trait을 사용하지 않는 테스트 케이스에 의해 추가된 레코드는 계속 남아 있을 수 있습니다. 

If you would like to totally reset the database using migrations, you may use the `Illuminate\Foundation\Testing\DatabaseMigrations` trait instead. However, the `DatabaseMigrations` trait is significantly slower than the `RefreshDatabase` trait.

마이그레이션을 사용하여 데이터베이스를 완전히 리셋하려면 `Illuminate\Foundation\Testing\DatabaseMigrations` 트레이트-trait을 사용할 수 있습니다. 하지만 `DatabaseMigrations` 트레이트-trait 는 `RefreshDatabase` 보다 훨씬 느립니다.

<a name="defining-model-factories"></a>
## Defining Model Factories
## 모델 팩토리 정의

<a name="concept-overview"></a>
### Concept Overview
### 개념 설명

First, let's talk about Eloquent model factories. When testing, you may need to insert a few records into your database before executing your test. Instead of manually specifying the value of each column when you create this test data, Laravel allows you to define a set of default attributes for each of your [Eloquent models](/docs/{{version}}/eloquent) using model factories.

먼저 Eloquent 모델 팩토리에 대해 이야기해 보겠습니다. 테스트할 때 테스트를 실행하기 전에 데이터베이스에 몇 개의 레코드를 삽입해야 할 수도 있습니다. 이 테스트 데이터를 생성할 때 각 열의 값을 수동으로 지정하는 대신 라라벨을 사용하면 모델 팩토리를 사용하여 [Eloquent 모델](/docs/{{version}}/eloquent) 각각에 대한 기본 속성 세트를 정의할 수 있습니다.

To see an example of how to write a factory, take a look at the `database/factories/UserFactory.php` file in your application. This factory is included with all new Laravel applications and contains the following factory definition:

팩토리를 작성하는 방법의 예제를 보려면 애플리케이션의 `database/factories/UserFactory.php` 파일을 살펴보세요. 이 팩토리는 새로만든 모든 라라벨 애플리케이션에 포함되어 있으며 다음과 같은 팩토리 정의를 포함하고 있습니다.

    namespace Database\Factories;

    use Illuminate\Database\Eloquent\Factories\Factory;
    use Illuminate\Support\Str;

    class UserFactory extends Factory
    {
        /**
         * Define the model's default state.
         *
         * @return array
         */
        public function definition()
        {
            return [
                'name' => $this->faker->name(),
                'email' => $this->faker->unique()->safeEmail(),
                'email_verified_at' => now(),
                'password' => '$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi', // password
                'remember_token' => Str::random(10),
            ];
        }
    }

As you can see, in their most basic form, factories are classes that extend Laravel's base factory class and define a `definition` method. The `definition` method returns the default set of attribute values that should be applied when creating a model using the factory.

보시다시피 가장 기본적인 형태의 팩토리는 라라벨의 기본 팩토리 클래스를 확장하고 `definition` 메소드를 정의하는 클래스입니다. `definition` 메서드는 팩토리를 사용하여 모델을 생성할 때 적용해야 하는 기본 속성 값 집합을 반환합니다.

Via the `faker` property, factories have access to the [Faker](https://github.com/FakerPHP/Faker) PHP library, which allows you to conveniently generate various kinds of random data for testing.

팩토리는 `faker` 속성을 통해 [Faker](https://github.com/Faker/PHPFaker) PHP 라이브러리에 액세스할 수 있으므로 테스트를 위해 다양한 종류의 임의 데이터를 편리하게 생성할 수 있습니다.

> {tip} You can set your application's Faker locale by adding a `faker_locale` option to your `config/app.php` configuration file.

> {tip} `config/app.ph1p` 설정 파일에 `faker_locale` 옵션을 추가하여 애플리케이션의 Faker 로케일을 설정할 수 있습니다.

<a name="generating-factories"></a>
### Generating Factories
### 팩토리 생성하기

To create a factory, execute the `make:factory` [Artisan command](/docs/{{version}}/artisan):

팩토리를 생성하려면 `make:factory` [Artisan command](/docs/{{version}}/artisan)를 실행하세요.

```shell
php artisan make:factory PostFactory
```

The new factory class will be placed in your `database/factories` directory.

새 팩토리 클래스는 `database/factories` 디렉토리에 생성됩니다.

<a name="factory-and-model-discovery-conventions"></a>
#### Model & Factory Discovery Conventions
#### 모델 및 팩토리 연동 규칙

Once you have defined your factories, you may use the static `factory` method provided to your models by the `Illuminate\Database\Eloquent\Factories\HasFactory` trait in order to instantiate a factory instance for that model.

팩토리를 정의한 후에는, 해당 모델에 대한 팩토리 인스턴스를 인스턴스화하기 위해 `Illuminate\Database\Eloquent\Factories\HasFactory` 트레잇에 의해 모델에 제공되는 정적 `factory` 메소드를 사용할 수 있습니다.

The `HasFactory` trait's `factory` method will use conventions to determine the proper factory for the model the trait is assigned to. Specifically, the method will look for a factory in the `Database\Factories` namespace that has a class name matching the model name and is suffixed with `Factory`. If these conventions do not apply to your particular application or factory, you may overwrite the `newFactory` method on your model to return an instance of the model's corresponding factory directly:

`HasFactory` 트레잇의 `factory` 메소드는 규칙을 사용하여 트레잇이 할당된 모델에 대한 적절한 팩토리를 결정합니다. 특히 이 메서드는 모델 이름과 일치하는 클래스 이름이 있고 접미사가 `Factory`인 `Database\Factories` 네임스페이스의 팩토리를 찾습니다. 이러한 규칙이 특정 애플리케이션이나 팩토리에 적용되지 않는 경우 모델의 `newFactory` 메소드를 덮어써서 모델의 해당 팩토리 인스턴스를 직접 반환할 수 있습니다.

    use Database\Factories\Administration\FlightFactory;

    /**
     * Create a new factory instance for the model.
     *
     * @return \Illuminate\Database\Eloquent\Factories\Factory
     */
    protected static function newFactory()
    {
        return FlightFactory::new();
    }

Next, define a `model` property on the corresponding factory:

다음으로 해당 팩토리에서 `model` 속성을 정의합니다.

    use App\Administration\Flight;
    use Illuminate\Database\Eloquent\Factories\Factory;

    class FlightFactory extends Factory
    {
        /**
         * The name of the factory's corresponding model.
         *
         * @var string
         */
        protected $model = Flight::class;
    }

<a name="factory-states"></a>
### Factory States
### 팩토리 상태(States)

State manipulation methods allow you to define discrete modifications that can be applied to your model factories in any combination. For example, your `Database\Factories\UserFactory` factory might contain a `suspended` state method that modifies one of its default attribute values.

상태 조작 방법을 사용하면 어떤 조합으로든 모델 팩토리에 적용할 수 있는 개별 수정을 정의할 수 있습니다. 예를 들어, `Database\Factories\UserFactory` 팩토리에는 기본 속성 값 중 하나를 수정하는 `suspended` 상태 메소드가 포함될 수 있습니다.

State transformation methods typically call the `state` method provided by Laravel's base factory class. The `state` method accepts a closure which will receive the array of raw attributes defined for the factory and should return an array of attributes to modify:

상태 변환 메소드는 일반적으로 라라벨의 기본 팩토리 클래스에서 제공하는 `state` 메소드를 호출합니다. `state` 메소드는 팩토리에 대해 정의된 원시 속성의 배열을 수신하고 수정할 속성의 배열을 반환해야 하는 클로저를 입력받습니다.

    /**
     * Indicate that the user is suspended.
     *
     * @return \Illuminate\Database\Eloquent\Factories\Factory
     */
    public function suspended()
    {
        return $this->state(function (array $attributes) {
            return [
                'account_status' => 'suspended',
            ];
        });
    }

<a name="factory-callbacks"></a>
### Factory Callbacks
### 팩토리 콜백

Factory callbacks are registered using the `afterMaking` and `afterCreating` methods and allow you to perform additional tasks after making or creating a model. You should register these callbacks by defining a `configure` method on your factory class. This method will be automatically called by Laravel when the factory is instantiated:

팩토리 콜백은 `afterMaking` 및 `afterCreating` 메소드를 사용하여 등록되며 모델을 만들거나 생성한 후 추가 작업을 수행할 수 있습니다. 팩토리 클래스에서 `configure` 메소드를 정의하여 이러한 콜백을 등록해야 합니다. 이 메소드는 팩토리가 인스턴스화될 때 라라벨에 의해 자동으로 호출됩니다.

    namespace Database\Factories;

    use App\Models\User;
    use Illuminate\Database\Eloquent\Factories\Factory;
    use Illuminate\Support\Str;

    class UserFactory extends Factory
    {
        /**
         * Configure the model factory.
         *
         * @return $this
         */
        public function configure()
        {
            return $this->afterMaking(function (User $user) {
                //
            })->afterCreating(function (User $user) {
                //
            });
        }

        // ...
    }

<a name="creating-models-using-factories"></a>
## Creating Models Using Factories
## 팩토리를 사용하여 모델 생성

<a name="instantiating-models"></a>
### Instantiating Models
### 모델 인스턴스화

Once you have defined your factories, you may use the static `factory` method provided to your models by the `Illuminate\Database\Eloquent\Factories\HasFactory` trait in order to instantiate a factory instance for that model. Let's take a look at a few examples of creating models. First, we'll use the `make` method to create models without persisting them to the database:

팩토리를 정의한 후에는 해당 모델에 대한 팩토리 인스턴스를 인스턴스화하기 위해 `Illuminate\Database\Eloquent\Factories\HasFactory` 트레잇에 의해 모델에 제공되는 정적 `factory` 메소드를 사용할 수 있습니다. 모델 생성의 몇 가지 예를 살펴보겠습니다. 먼저, 모델을 데이터베이스에 유지하지 않고 생성하기 위해 `make` 메소드를 사용할 것입니다.

    use App\Models\User;

    public function test_models_can_be_instantiated()
    {
        $user = User::factory()->make();

        // Use model in tests...
    }

You may create a collection of many models using the `count` method:

`count` 메소드를 사용하여 여러 모델의 컬렉션을 만들 수 있습니다.

    $users = User::factory()->count(3)->make();

<a name="applying-states"></a>
#### Applying States
#### 상태 적용

You may also apply any of your [states](#factory-states) to the models. If you would like to apply multiple state transformations to the models, you may simply call the state transformation methods directly:

[states](#factory-states)를 모델에 적용할 수도 있습니다. 여러 상태 변환을 모델에 적용하려면 상태 변환 메서드를 직접 호출하면 됩니다.

    $users = User::factory()->count(5)->suspended()->make();

<a name="overriding-attributes"></a>
#### Overriding Attributes
#### 속성 재지정(오버라이딩) 하기

If you would like to override some of the default values of your models, you may pass an array of values to the `make` method. Only the specified attributes will be replaced while the rest of the attributes remain set to their default values as specified by the factory:

모델의 기본값 중 일부를 재정의하려면 `make` 메서드에 값 배열을 전달할 수 있습니다. 지정된 속성만 교체되고 나머지 속성은 공장에서 지정한 기본값으로 설정된 상태로 유지됩니다.

    $user = User::factory()->make([
        'name' => 'Abigail Otwell',
    ]);

Alternatively, the `state` method may be called directly on the factory instance to perform an inline state transformation:

또는 팩토리 인스턴스에서 `state` 메소드를 직접 호출하여 인라인 상태 변환을 수행 할 수 있습니다.

    $user = User::factory()->state([
        'name' => 'Abigail Otwell',
    ])->make();

> {tip} [Mass assignment protection](/docs/{{version}}/eloquent#mass-assignment) is automatically disabled when creating models using factories.

> {tip} [대량 할당 보호](/docs/{{version}}/eloquent#mass-assignment)는 팩토리를 사용하여 모델을 생성할 때는 자동으로 비활성화됩니다.

<a name="persisting-models"></a>
### Persisting Models
### 모델 저장하기

The `create` method instantiates model instances and persists them to the database using Eloquent's `save` method:

`create` 메소드는 모델 인스턴스를 인스턴스화하고 Eloquent의 `save` 메소드를 사용하여 데이터베이스에 저장합니다.

    use App\Models\User;

    public function test_models_can_be_persisted()
    {
        // Create a single App\Models\User instance...
        $user = User::factory()->create();

        // Create three App\Models\User instances...
        $users = User::factory()->count(3)->create();

        // Use model in tests...
    }

You may override the factory's default model attributes by passing an array of attributes to the `create` method:

속성 배열을 `create` 메소드에 전달하여 팩토리의 기본 모델 속성을 재정의할 수 있습니다.

    $user = User::factory()->create([
        'name' => 'Abigail',
    ]);

<a name="sequences"></a>
### Sequences
### 시퀀스

Sometimes you may wish to alternate the value of a given model attribute for each created model. You may accomplish this by defining a state transformation as a sequence. For example, you may wish to alternate the value of an `admin` column between `Y` and `N` for each created user:

때로는 생성된 각 모델에 대해 주어진 모델 속성의 값을 대체하고자 할 수 있습니다. 상태 변환을 시퀀스로 정의하여 이를 수행할 수 있습니다. 예를 들어, 생성된 각 사용자에 대해 `admin` 열의 값을 `Y`와 `N`으로 대체할 수 있습니다.

    use App\Models\User;
    use Illuminate\Database\Eloquent\Factories\Sequence;

    $users = User::factory()
                    ->count(10)
                    ->state(new Sequence(
                        ['admin' => 'Y'],
                        ['admin' => 'N'],
                    ))
                    ->create();

In this example, five users will be created with an `admin` value of `Y` and five users will be created with an `admin` value of `N`.

이 예에서는 `admin` 값이 `Y`인 5명의 사용자가 생성되고 `admin` 값이 `N`인 5명의 사용자가 생성됩니다.

If necessary, you may include a closure as a sequence value. The closure will be invoked each time the sequence needs a new value:

필요한 경우 클로저를 시퀀스 값으로 포함할 수 있습니다. 시퀀스에 새 값이 필요할 때마다 클로저가 호출됩니다.

    $users = User::factory()
                    ->count(10)
                    ->state(new Sequence(
                        fn ($sequence) => ['role' => UserRoles::all()->random()],
                    ))
                    ->create();

Within a sequence closure, you may access the `$index` or `$count` properties on the sequence instance that is injected into the closure. The `$index` property contains the number of iterations through the sequence that have occurred thus far, while the `$count` property contains the total number of times the sequence will be invoked:

시퀀스 클로저 내에서 클로저에 주입된 시퀀스 인스턴스의 `$index` 또는 `$count` 속성에 액세스할 수 있습니다. `$index` 속성은 지금까지 발생한 시퀀스의 반복 횟수를 포함하고, `$count` 속성은 시퀀스가 호출될 총 횟수를 포함합니다.

    $users = User::factory()
                    ->count(10)
                    ->sequence(fn ($sequence) => ['name' => 'Name '.$sequence->index])
                    ->create();

<a name="factory-relationships"></a>
## Factory Relationships
## 팩토리 관계

<a name="has-many-relationships"></a>
### Has Many Relationships
### 1:*(일대다) 관계

Next, let's explore building Eloquent model relationships using Laravel's fluent factory methods. First, let's assume our application has an `App\Models\User` model and an `App\Models\Post` model. Also, let's assume that the `User` model defines a `hasMany` relationship with `Post`. We can create a user that has three posts using the `has` method provided by the Laravel's factories. The `has` method accepts a factory instance:

다음으로, 라라벨의 유창한 팩토리 메서드를 사용하여 Eloquent 모델 관계를 구축하는 방법을 살펴보겠습니다. 먼저 애플리케이션에 `App\Models\User` 모델과 `App\Models\Post` 모델이 있다고 가정해 보겠습니다. 또한 `User` 모델이 `Post`와 `hasMany` 관계를 정의한다고 가정해 보겠습니다. 라라벨의 팩토리에서 제공하는 `has` 메소드를 사용하여 세 개의 게시물이 있는 사용자를 만들 수 있습니다. `has` 메소드는 팩토리 인스턴스를 입력받습니다.

    use App\Models\Post;
    use App\Models\User;

    $user = User::factory()
                ->has(Post::factory()->count(3))
                ->create();

By convention, when passing a `Post` model to the `has` method, Laravel will assume that the `User` model must have a `posts` method that defines the relationship. If necessary, you may explicitly specify the name of the relationship that you would like to manipulate:

관례적으로 `Post` 모델을 `has` 메소드에 전달할 때 라라벨은 `User` 모델에 관계를 정의하는 `posts` 메소드가 있어야한다고 가정합니다. 필요한 경우 조작하려는 관계의 이름을 명시적으로 지정할 수 있습니다.

    $user = User::factory()
                ->has(Post::factory()->count(3), 'posts')
                ->create();

Of course, you may perform state manipulations on the related models. In addition, you may pass a closure based state transformation if your state change requires access to the parent model:

물론 관련 모델에 대해 상태 조작을 수행할 수 있습니다. 또한 상태 변경에 상위 모델에 대한 액세스가 필요한 경우 클로저 기반 상태 변환을 전달할 수 있습니다.

    $user = User::factory()
                ->has(
                    Post::factory()
                            ->count(3)
                            ->state(function (array $attributes, User $user) {
                                return ['user_type' => $user->type];
                            })
                )
                ->create();

<a name="has-many-relationships-using-magic-methods"></a>
#### Using Magic Methods
#### 매직 메소드 사용

For convenience, you may use Laravel's magic factory relationship methods to build relationships. For example, the following example will use convention to determine that the related models should be created via a `posts` relationship method on the `User` model:

편의를 위해 라라벨의 매직 팩토리 관계 메소드를 사용하여 관계를 구축할 수 있습니다. 예를 들어, 다음 예는 관례를 사용하여 `User` 모델의 `posts` 관계 메소드를 통해 관련 모델을 생성해야 하는지 결정합니다.

    $user = User::factory()
                ->hasPosts(3)
                ->create();

When using magic methods to create factory relationships, you may pass an array of attributes to override on the related models:

매직 메소드를 사용하여 팩토리 관계를 만들 때 관련 모델을 재정의 할 속성 배열을 전달할 수 있습니다.

    $user = User::factory()
                ->hasPosts(3, [
                    'published' => false,
                ])
                ->create();

You may provide a closure based state transformation if your state change requires access to the parent model:

상태 변경에 상위 모델에 대한 액세스가 필요한 경우 클로저 기반 상태 변환을 제공할 수 있습니다.

    $user = User::factory()
                ->hasPosts(3, function (array $attributes, User $user) {
                    return ['user_type' => $user->type];
                })
                ->create();

<a name="belongs-to-relationships"></a>
### Belongs To Relationships
### 1:*(일대다) 역관계

Now that we have explored how to build "has many" relationships using factories, let's explore the inverse of the relationship. The `for` method may be used to define the parent model that factory created models belong to. For example, we can create three `App\Models\Post` model instances that belong to a single user:

이제 팩토리를 사용하여 "많은" 관계를 구축하는 방법을 살펴보았으므로 관계의 역을 살펴보겠습니다. `for` 메소드는 공장에서 생성된 모델이 속한 상위 모델을 정의하는 데 사용할 수 있습니다. 예를 들어 단일 사용자에게 속한 세 개의 `App\Models\Post` 모델 인스턴스를 만들 수 있습니다.

    use App\Models\Post;
    use App\Models\User;

    $posts = Post::factory()
                ->count(3)
                ->for(User::factory()->state([
                    'name' => 'Jessica Archer',
                ]))
                ->create();

If you already have a parent model instance that should be associated with the models you are creating, you may pass the model instance to the `for` method:

생성 중인 모델과 연결되어야 하는 상위 모델 인스턴스가 이미 있는 경우 모델 인스턴스를 `for` 메서드에 전달할 수 있습니다.

    $user = User::factory()->create();

    $posts = Post::factory()
                ->count(3)
                ->for($user)
                ->create();

<a name="belongs-to-relationships-using-magic-methods"></a>
#### Using Magic Methods
#### 매직 메소드 사용

For convenience, you may use Laravel's magic factory relationship methods to define "belongs to" relationships. For example, the following example will use convention to determine that the three posts should belong to the `user` relationship on the `Post` model:

편의를 위해 "belong to" 관계를 정의하기 위해 라라벨의 매직 팩토리 관계 메소드를 사용할 수 있습니다. 예를 들어 다음 예제에서는 규칙을 사용하여 세 개의 게시물이 `Post` 모델의 `user` 관계에 속해있어야 한다고 지정합니다.

    $posts = Post::factory()
                ->count(3)
                ->forUser([
                    'name' => 'Jessica Archer',
                ])
                ->create();

<a name="many-to-many-relationships"></a>
### Many To Many Relationships
### \*:*(다대다) 관계

Like [has many relationships](#has-many-relationships), "many to many" relationships may be created using the `has` method:

[1:*(일대다) 관계](#has-many-relationships)와 같이 `has` 메소드를 사용하여 \*:*(다대다) 관계 만들 수 있습니다.

    use App\Models\Role;
    use App\Models\User;

    $user = User::factory()
                ->has(Role::factory()->count(3))
                ->create();

<a name="pivot-table-attributes"></a>
#### Pivot Table Attributes
#### 피벗 테이블 속성

If you need to define attributes that should be set on the pivot / intermediate table linking the models, you may use the `hasAttached` method. This method accepts an array of pivot table attribute names and values as its second argument:

모델을 연결하는 피벗 / 중간 테이블에 설정해야하는 속성을 정의해야하는 경우 `hasAttached` 메서드를 사용할 수 있습니다. 이 메서드는 피벗 테이블 속성 이름과 값의 배열을 두 번째 인수로받습니다.

    use App\Models\Role;
    use App\Models\User;

    $user = User::factory()
                ->hasAttached(
                    Role::factory()->count(3),
                    ['active' => true]
                )
                ->create();

You may provide a closure based state transformation if your state change requires access to the related model:

상태 변경에 관련 모델에 대한 액세스가 필요한 경우 클로저 기반 상태 변환을 제공 할 수 있습니다.

    $user = User::factory()
                ->hasAttached(
                    Role::factory()
                        ->count(3)
                        ->state(function (array $attributes, User $user) {
                            return ['name' => $user->name.' Role'];
                        }),
                    ['active' => true]
                )
                ->create();

If you already have model instances that you would like to be attached to the models you are creating, you may pass the model instances to the `hasAttached` method. In this example, the same three roles will be attached to all three users:

생성 중인 모델에 연결하려는 모델 인스턴스가 이미 있는 경우 모델 인스턴스를 `hasAttached` 메서드에 전달할 수 있습니다. 이 예제에서는 동일한 세 가지 역할이 세 사용자 모두에게 연결됩니다.

    $roles = Role::factory()->count(3)->create();

    $user = User::factory()
                ->count(3)
                ->hasAttached($roles, ['active' => true])
                ->create();

<a name="many-to-many-relationships-using-magic-methods"></a>
#### Using Magic Methods
#### 매직 메소드 사용

For convenience, you may use Laravel's magic factory relationship methods to define many to many relationships. For example, the following example will use convention to determine that the related models should be created via a `roles` relationship method on the `User` model:

편의를 위해 라라벨의 매직 팩토리 관계 메소드를 사용하여 다대다 관계를 정의할 수 있습니다. 예를 들어, 다음 예제는 관례를 사용하여 관련 모델이 `User` 모델의 `roles` 관계 메소드를 통해 생성되어야 하는지 결정합니다.

    $user = User::factory()
                ->hasRoles(1, [
                    'name' => 'Editor'
                ])
                ->create();

<a name="polymorphic-relationships"></a>
### Polymorphic Relationships
### 다형성 관계

[Polymorphic relationships](/docs/{{version}}/eloquent-relationships#polymorphic-relationships) may also be created using factories. Polymorphic "morph many" relationships are created in the same way as typical "has many" relationships. For example, if a `App\Models\Post` model has a `morphMany` relationship with a `App\Models\Comment` model:

[다형성 관계](/docs/{{version}}/eloquent-relationships#polymorphic-relationships)도 팩토리를 사용하여 만들 수 있습니다. 다형성 "morph many" 관계는 일반적인 "다수" 관계와 동일한 방식으로 생성됩니다. 예를 들어 `App\Models\Post` 모델에 `App\Models\Comment` 모델과 `morphMany` 관계가 있는 경우라면 다음과 같습니다.

    use App\Models\Post;

    $post = Post::factory()->hasComments(3)->create();

<a name="morph-to-relationships"></a>
#### Morph To Relationships
#### Morph To 관계

Magic methods may not be used to create `morphTo` relationships. Instead, the `for` method must be used directly and the name of the relationship must be explicitly provided. For example, imagine that the `Comment` model has a `commentable` method that defines a `morphTo` relationship. In this situation, we may create three comments that belong to a single post by using the `for` method directly:

매직 메서드는 `morphTo` 관계를 만드는 데 사용할 수 없습니다. 대신 `for` 메서드를 직접 사용해야 하며 관계 이름을 명시적으로 제공해야 합니다. 예를 들어 `Comment` 모델에 `morphTo` 관계를 정의하는 `commentable` 메서드가 있다고 가정해 보겠습니다. 이 상황에서 `for` 메서드를 직접 사용하여 단일 게시물에 속하는 세 개의 댓글을 만들 수 있습니다.

    $comments = Comment::factory()->count(3)->for(
        Post::factory(), 'commentable'
    )->create();

<a name="polymorphic-many-to-many-relationships"></a>
#### Polymorphic Many To Many Relationships
#### 다형성 다 대다 관계

Polymorphic "many to many" (`morphToMany` / `morphedByMany`) relationships may be created just like non-polymorphic "many to many" relationships:

다형성 "다대다"(`morphToMany` `morphedByMany`) 관계는 비다형성 "다대다" 관계처럼 생성될 수 있습니다.

    use App\Models\Tag;
    use App\Models\Video;

    $videos = Video::factory()
                ->hasAttached(
                    Tag::factory()->count(3),
                    ['public' => true]
                )
                ->create();

Of course, the magic `has` method may also be used to create polymorphic "many to many" relationships:

물론 매직 `has` 메소드는 다형성 "다 대다" 관계를 생성하는 데에도 사용할 수 있습니다.

    $videos = Video::factory()
                ->hasTags(3, ['public' => true])
                ->create();

<a name="defining-relationships-within-factories"></a>
### Defining Relationships Within Factories
### 팩토리 내 관계 정의

To define a relationship within your model factory, you will typically assign a new factory instance to the foreign key of the relationship. This is normally done for the "inverse" relationships such as `belongsTo` and `morphTo` relationships. For example, if you would like to create a new user when creating a post, you may do the following:

모델 팩토리 내에서 관계를 정의하려면 일반적으로 관계의 외래 키에 새 팩토리 인스턴스를 할당합니다. 이것은 일반적으로 `belongsTo` 및 `morphTo` 관계와 같은 "역" 관계에 대해 수행됩니다. 예를 들어 게시물을 만들 때 새 사용자를 만들고 싶다면 다음과 같이 처리 할 수 있습니다.

    use App\Models\User;

    /**
     * Define the model's default state.
     *
     * @return array
     */
    public function definition()
    {
        return [
            'user_id' => User::factory(),
            'title' => $this->faker->title(),
            'content' => $this->faker->paragraph(),
        ];
    }

If the relationship's columns depend on the factory that defines it you may assign a closure to an attribute. The closure will receive the factory's evaluated attribute array:

관계의 열이 그것을 정의하는 팩토리에 의존하는 경우, 속성에 클로저를 할당할 수 있습니다. 클로저는 팩토리의 실행된 속성 배열을 받을 것입니다.

    /**
     * Define the model's default state.
     *
     * @return array
     */
    public function definition()
    {
        return [
            'user_id' => User::factory(),
            'user_type' => function (array $attributes) {
                return User::find($attributes['user_id'])->type;
            },
            'title' => $this->faker->title(),
            'content' => $this->faker->paragraph(),
        ];
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
