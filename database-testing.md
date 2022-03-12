# 데이터베이스 테스팅

- [시작하기](#introduction)
    - [각각의 테스트 수행 후에 데이터베이스 재설정하기](#resetting-the-database-after-each-test)
- [모델 팩토리 정의](#defining-model-factories)
    - [개념 소개](#concept-overview)
    - [팩토리 생성하기](#generating-factories)
    - [팩토리 상태](#factory-states)
    - [팩토리 콜백](#factory-callbacks)
- [팩토리를 사용하여 모델 생성](#creating-models-using-factories)
    - [모델 인스턴스화](#instantiating-models)
    - [모델 저장하기](#persisting-models)
    - [시퀀스](#sequences)
- [팩토리 관계](#factory-relationships)
    - [1:*(일대다) 관계](#has-many-relationships)
    - [1:*(일대다) 역관계](#belongs-to-relationships)
    - [\*:*(다대다) 관계](#many-to-many-relationships)
    - [다형성 관계](#polymorphic-relationships)
    - [팩토리 내 관계 정의](#defining-relationships-within-factories)
- [시더 실행하기](#running-seeders)
- [사용 가능한 검증-Assertions](#available-assertions)

<a name="introduction"></a>
## 시작하기

라라벨은 데이터베이스 기반 애플리케이션을 더 쉽게 테스트할 수 있도록 다양한 유용한 도구와 검증을 제공합니다. 또한 라라벨 모델 팩토리와 시더를 사용하면 애플리케이션의 Eloquent 모델과 관계를 사용하여 테스트 데이터베이스 레코드를 손쉽게 생성할 수 있습니다. 다음 문서에서 이러한 모든 강력한 기능에 대해 설명합니다.

<a name="resetting-the-database-after-each-test"></a>
### 각각의 테스트 수행 후에 데이터베이스 재설정하기

더 진행하기 전에 이전 테스트의 데이터가 후속 테스트를 방해하지 않도록 각 테스트 후에 데이터베이스를 재설정하는 방법에 대해 논의하겠습니다. 라라벨에 포함된 `Illuminate\Foundation\Testing\RefreshDatabase` 특성-trait이 이 문제를 처리합니다. 테스트 클래스에서 특성-trait을 사용하기만 하면 됩니다.

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

<a name="defining-model-factories"></a>
## 모델 팩토리 정의

<a name="concept-overview"></a>
### 개념 설명

먼저 Eloquent 모델 팩토리에 대해 이야기해 보겠습니다. 테스트할 때 테스트를 실행하기 전에 데이터베이스에 몇 개의 레코드를 삽입해야 할 수도 있습니다. 이 테스트 데이터를 생성할 때 각 열의 값을 수동으로 지정하는 대신 라라벨을 사용하면 모델 팩토리를 사용하여 [Eloquent 모델](/docs/{{version}}/eloquent) 각각에 대한 기본 속성 세트를 정의할 수 있습니다.

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

보시다시피 가장 기본적인 형태의 팩토리는 라라벨의 기본 팩토리 클래스를 확장하고 `definition` 메소드를 정의하는 클래스입니다. `definition` 메서드는 팩토리를 사용하여 모델을 생성할 때 적용해야 하는 기본 속성 값 집합을 반환합니다.

팩토리는 `faker` 속성을 통해 [Faker](https://github.com/Faker/PHPFaker) PHP 라이브러리에 액세스할 수 있으므로 테스트를 위해 다양한 종류의 임의 데이터를 편리하게 생성할 수 있습니다.

> {tip} `config/app.ph1p` 설정 파일에 `faker_locale` 옵션을 추가하여 애플리케이션의 Faker 로케일을 설정할 수 있습니다.

<a name="generating-factories"></a>
### 팩토리 생성하기

팩토리를 생성하려면 `make:factory` [Artisan command](/docs/{{version}}/artisan)를 실행하세요.

    php artisan make:factory PostFactory

새 팩토리 클래스는 `database/factories` 디렉토리에 생성됩니다.

<a name="factory-and-model-discovery-conventions"></a>
#### 모델 및 팩토리 연동 규칙

팩토리를 정의한 후에는, 해당 모델에 대한 팩토리 인스턴스를 인스턴스화하기 위해 `Illuminate\Database\Eloquent\Factories\HasFactory` 트레잇에 의해 모델에 제공되는 정적 `factory` 메소드를 사용할 수 있습니다.

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
### 팩토리 상태(States)

상태 조작 방법을 사용하면 어떤 조합으로든 모델 팩토리에 적용할 수 있는 개별 수정을 정의할 수 있습니다. 예를 들어, `Database\Factories\UserFactory` 팩토리에는 기본 속성 값 중 하나를 수정하는 `suspended` 상태 메소드가 포함될 수 있습니다.

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
### 팩토리 콜백

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
## 팩토리를 사용하여 모델 생성

<a name="instantiating-models"></a>
### 모델 인스턴스화

팩토리를 정의한 후에는 해당 모델에 대한 팩토리 인스턴스를 인스턴스화하기 위해 `Illuminate\Database\Eloquent\Factories\HasFactory` 트레잇에 의해 모델에 제공되는 정적 `factory` 메소드를 사용할 수 있습니다. 모델 생성의 몇 가지 예를 살펴보겠습니다. 먼저, 모델을 데이터베이스에 유지하지 않고 생성하기 위해 `make` 메소드를 사용할 것입니다.

    use App\Models\User;

    public function test_models_can_be_instantiated()
    {
        $user = User::factory()->make();

        // Use model in tests...
    }

`count` 메소드를 사용하여 여러 모델의 컬렉션을 만들 수 있습니다.

    $users = User::factory()->count(3)->make();

<a name="applying-states"></a>
#### 상태 적용

[states](#factory-states)를 모델에 적용할 수도 있습니다. 여러 상태 변환을 모델에 적용하려면 상태 변환 메서드를 직접 호출하면 됩니다.

    $users = User::factory()->count(5)->suspended()->make();

<a name="overriding-attributes"></a>
#### 속성 재지정(오버라이딩) 하기

모델의 기본값 중 일부를 재정의하려면 `make` 메서드에 값 배열을 전달할 수 있습니다. 지정된 속성만 교체되고 나머지 속성은 공장에서 지정한 기본값으로 설정된 상태로 유지됩니다.

    $user = User::factory()->make([
        'name' => 'Abigail Otwell',
    ]);

또는 팩토리 인스턴스에서 `state` 메소드를 직접 호출하여 인라인 상태 변환을 수행 할 수 있습니다.

    $user = User::factory()->state([
        'name' => 'Abigail Otwell',
    ])->make();

> {tip} [대량 할당 보호](/docs/{{version}}/eloquent#mass-assignment)는 팩토리를 사용하여 모델을 생성할 때는 자동으로 비활성화됩니다.

<a name="persisting-models"></a>
### 모델 저장하기

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

속성 배열을 `create` 메소드에 전달하여 팩토리의 기본 모델 속성을 재정의할 수 있습니다.

    $user = User::factory()->create([
        'name' => 'Abigail',
    ]);

<a name="sequences"></a>
### 시퀀스

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

이 예에서는 `admin` 값이 `Y`인 5명의 사용자가 생성되고 `admin` 값이 `N`인 5명의 사용자가 생성됩니다.

필요한 경우 클로저를 시퀀스 값으로 포함할 수 있습니다. 시퀀스에 새 값이 필요할 때마다 클로저가 호출됩니다.

    $users = User::factory()
                    ->count(10)
                    ->state(new Sequence(
                        fn ($sequence) => ['role' => UserRoles::all()->random()],
                    ))
                    ->create();

시퀀스 클로저 내에서 클로저에 주입된 시퀀스 인스턴스의 `$index` 또는 `$count` 속성에 액세스할 수 있습니다. `$index` 속성은 지금까지 발생한 시퀀스의 반복 횟수를 포함하고, `$count` 속성은 시퀀스가 호출될 총 횟수를 포함합니다.

    $users = User::factory()
                    ->count(10)
                    ->sequence(fn ($sequence) => ['name' => 'Name '.$sequence->index])
                    ->create();

<a name="factory-relationships"></a>
## 팩토리 관계

<a name="has-many-relationships"></a>
### 1:*(일대다) 관계

다음으로, 라라벨의 유창한 팩토리 메서드를 사용하여 Eloquent 모델 관계를 구축하는 방법을 살펴보겠습니다. 먼저 애플리케이션에 `App\Models\User` 모델과 `App\Models\Post` 모델이 있다고 가정해 보겠습니다. 또한 `User` 모델이 `Post`와 `hasMany` 관계를 정의한다고 가정해 보겠습니다. 라라벨의 팩토리에서 제공하는 `has` 메소드를 사용하여 세 개의 게시물이 있는 사용자를 만들 수 있습니다. `has` 메소드는 팩토리 인스턴스를 입력받습니다.

    use App\Models\Post;
    use App\Models\User;

    $user = User::factory()
                ->has(Post::factory()->count(3))
                ->create();

관례적으로 `Post` 모델을 `has` 메소드에 전달할 때 라라벨은 `User` 모델에 관계를 정의하는 `posts` 메소드가 있어야한다고 가정합니다. 필요한 경우 조작하려는 관계의 이름을 명시적으로 지정할 수 있습니다.

    $user = User::factory()
                ->has(Post::factory()->count(3), 'posts')
                ->create();

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
#### 매직 메소드 사용

편의를 위해 라라벨의 매직 팩토리 관계 메소드를 사용하여 관계를 구축할 수 있습니다. 예를 들어, 다음 예는 관례를 사용하여 `User` 모델의 `posts` 관계 메소드를 통해 관련 모델을 생성해야 하는지 결정합니다.

    $user = User::factory()
                ->hasPosts(3)
                ->create();

매직 메소드를 사용하여 팩토리 관계를 만들 때 관련 모델을 재정의 할 속성 배열을 전달할 수 있습니다.

    $user = User::factory()
                ->hasPosts(3, [
                    'published' => false,
                ])
                ->create();

상태 변경에 상위 모델에 대한 액세스가 필요한 경우 클로저 기반 상태 변환을 제공할 수 있습니다.

    $user = User::factory()
                ->hasPosts(3, function (array $attributes, User $user) {
                    return ['user_type' => $user->type];
                })
                ->create();

<a name="belongs-to-relationships"></a>
### 1:*(일대다) 역관계

이제 팩토리를 사용하여 "많은" 관계를 구축하는 방법을 살펴보았으므로 관계의 역을 살펴보겠습니다. `for` 메소드는 공장에서 생성된 모델이 속한 상위 모델을 정의하는 데 사용할 수 있습니다. 예를 들어 단일 사용자에게 속한 세 개의 `App\Models\Post` 모델 인스턴스를 만들 수 있습니다.

    use App\Models\Post;
    use App\Models\User;

    $posts = Post::factory()
                ->count(3)
                ->for(User::factory()->state([
                    'name' => 'Jessica Archer',
                ]))
                ->create();

생성 중인 모델과 연결되어야 하는 상위 모델 인스턴스가 이미 있는 경우 모델 인스턴스를 `for` 메서드에 전달할 수 있습니다.

    $user = User::factory()->create();

    $posts = Post::factory()
                ->count(3)
                ->for($user)
                ->create();

<a name="belongs-to-relationships-using-magic-methods"></a>
#### 매직 메소드 사용

편의를 위해 "belong to" 관계를 정의하기 위해 라라벨의 매직 팩토리 관계 메소드를 사용할 수 있습니다. 예를 들어 다음 예제에서는 규칙을 사용하여 세 개의 게시물이 `Post` 모델의 `user` 관계에 속해있어야 한다고 지정합니다.

    $posts = Post::factory()
                ->count(3)
                ->forUser([
                    'name' => 'Jessica Archer',
                ])
                ->create();

<a name="many-to-many-relationships"></a>
### \*:*(다대다) 관계

[1:*(일대다) 관계](#has-many-relationships)와 같이 `has` 메소드를 사용하여 \*:*(다대다) 관계 만들 수 있습니다.

    use App\Models\Role;
    use App\Models\User;

    $user = User::factory()
                ->has(Role::factory()->count(3))
                ->create();

<a name="pivot-table-attributes"></a>
#### 피벗 테이블 속성

모델을 연결하는 피벗 / 중간 테이블에 설정해야하는 속성을 정의해야하는 경우 `hasAttached` 메서드를 사용할 수 있습니다. 이 메서드는 피벗 테이블 속성 이름과 값의 배열을 두 번째 인수로받습니다.

    use App\Models\Role;
    use App\Models\User;

    $user = User::factory()
                ->hasAttached(
                    Role::factory()->count(3),
                    ['active' => true]
                )
                ->create();

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

생성 중인 모델에 연결하려는 모델 인스턴스가 이미 있는 경우 모델 인스턴스를 `hasAttached` 메서드에 전달할 수 있습니다. 이 예제에서는 동일한 세 가지 역할이 세 사용자 모두에게 연결됩니다.

    $roles = Role::factory()->count(3)->create();

    $user = User::factory()
                ->count(3)
                ->hasAttached($roles, ['active' => true])
                ->create();

<a name="many-to-many-relationships-using-magic-methods"></a>
#### 매직 메소드 사용

편의를 위해 라라벨의 매직 팩토리 관계 메소드를 사용하여 다대다 관계를 정의할 수 있습니다. 예를 들어, 다음 예제는 관례를 사용하여 관련 모델이 `User` 모델의 `roles` 관계 메소드를 통해 생성되어야 하는지 결정합니다.

    $user = User::factory()
                ->hasRoles(1, [
                    'name' => 'Editor'
                ])
                ->create();

<a name="polymorphic-relationships"></a>
### 다형성 관계

[다형성 관계](/docs/{{version}}/eloquent-relationships#polymorphic-relationships)도 팩토리를 사용하여 만들 수 있습니다. 다형성 "morph many" 관계는 일반적인 "다수" 관계와 동일한 방식으로 생성됩니다. 예를 들어 `App\Models\Post` 모델에 `App\Models\Comment` 모델과 `morphMany` 관계가 있는 경우라면 다음과 같습니다.

    use App\Models\Post;

    $post = Post::factory()->hasComments(3)->create();

<a name="morph-to-relationships"></a>
#### Morph To 관계

매직 메서드는 `morphTo` 관계를 만드는 데 사용할 수 없습니다. 대신 `for` 메서드를 직접 사용해야 하며 관계 이름을 명시적으로 제공해야 합니다. 예를 들어 `Comment` 모델에 `morphTo` 관계를 정의하는 `commentable` 메서드가 있다고 가정해 보겠습니다. 이 상황에서 `for` 메서드를 직접 사용하여 단일 게시물에 속하는 세 개의 댓글을 만들 수 있습니다.

    $comments = Comment::factory()->count(3)->for(
        Post::factory(), 'commentable'
    )->create();

<a name="polymorphic-many-to-many-relationships"></a>
#### 다형성 다 대다 관계

다형성 "다대다"(`morphToMany` `morphedByMany`) 관계는 비다형성 "다대다" 관계처럼 생성될 수 있습니다.

    use App\Models\Tag;
    use App\Models\Video;

    $videos = Video::factory()
                ->hasAttached(
                    Tag::factory()->count(3),
                    ['public' => true]
                )
                ->create();

물론 매직 `has` 메소드는 다형성 "다 대다" 관계를 생성하는 데에도 사용할 수 있습니다.

    $videos = Video::factory()
                ->hasTags(3, ['public' => true])
                ->create();

<a name="defining-relationships-within-factories"></a>
### 팩토리 내 관계 정의

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

또는 `RefreshDatabase` 특성-trait을 사용하는 각 테스트 전에 데이터베이스를 자동으로 시드하도록 라라벨에 지시할 수 있습니다. 기본 테스트 클래스에서 `$seed` 속성을 정의하여 이를 수행할 수 있습니다.

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

`$seed` 속성이 `true`면 테스트는 `RefreshDatabase` 특성-trait을 사용하는 각 테스트 전에 `Database\Seeders\DatabaseSeeder` 클래스를 실행합니다. 그러나 테스트 클래스에 `seeder` 속성을 정의하여 실행해야 하는 특정 시드를 지정할 수 있습니다.

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
#### assertDeleted

`assertDeleted`는 주어진 Eloquent 모델이 데이터베이스에서 삭제되었는지 확인합니다.

    use App\Models\User;

    $user = User::find(1);

    $user->delete();

    $this->assertDeleted($user);

`assertSoftDeleted` 메소드는 주어진 Eloquent 모델이 "소프트 삭제"되었다고 확인하기 위해 사용될 수 있습니다.

    $this->assertSoftDeleted($user);

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
