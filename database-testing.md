# 데이터베이스 테스팅

- [시작하기](#introduction)
- [각각의 테스트 수행 후에 데이터베이스 재설정하기](#resetting-the-database-after-each-test)
- [팩토리 생성하기](#creating-factories)
- [팩토리 작성하기](#writing-factories)
    - [팩토리 상태(States)](#factory-states)
    - [팩토리 콜백](#factory-callbacks)
- [팩토리 사용하기](#using-factories)
    - [모델 생성하기](#creating-models)
    - [모델 저장하기](#persisting-models)
    - [시퀀스](#sequences)
- [팩토리 관계](#factory-relationships)
    - [정의 내의 관계](#relationships-within-definition)
    - [1:*(일대다) 관계](#has-many-relationships)
    - [1:*(일대다) 역관계](#belongs-to-relationships)
    - [\*:*(다대다) 관계](#many-to-many-relationships)
    - [다형성 관계](#polymorphic-relationships)
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

<a name="resetting-the-database-after-each-test"></a>
## 각각의 테스트 수행 후에 데이터베이스 재설정하기

이전 테스트의 데이터가 후속 테스트를 방해하지 않도록 각 테스트 후에 데이터베이스를 재설정하는 것이 유용한 경우가 많습니다. `RefreshDatabase` trait은 인 메모리 데이터베이스를 사용하는지 아니면 기존 데이터베이스를 사용하는지에 따라 테스트 데이터베이스를 마이그레이션하는 가장 최적의 접근 방식을 취합니다. 테스트 클래스에서 trait을 사용하면 모든 것이 처리됩니다.

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

<a name="creating-factories"></a>
## 팩토리 생성하기

테스트를 할 때, 실행하기 전 데이터베이스에 몇몇 레코드를 입력하는 것이 필요할 수 있습니다. 이 테스트 데이터를 생성할 때 수동으로 각각의 컬럼의 값을 지정하는 대신에 라라벨은 모델 팩토리를 사용하여 각각의 [Eloquent 모델](/docs/{{version}}/eloquent)을 위한 기본 속성의 세트를 정의하도록 해줍니다. 

팩토리 클래스를 생성하기 위해서는 `make:factory` [아티즌 명령어](/docs/{{version}}/artisan)를 사용합니다.

    php artisan make:factory PostFactory

새로운 팩토리는 `database/factories` 디렉토리에 생성됩니다.

팩토리에 의해서 생성될 모델의 이름을 지정하고자 한다면 `--model` 옵션을 사용하면 됩니다. 이 옵션은 생성된 팩토리 파일을 주어진 모델로 미리 채워둡니다.

    php artisan make:factory PostFactory --model=Post

<a name="writing-factories"></a>
## 팩토리 작성하기

먼저 애플리케이션의 `database/factories/UserFactory.php` 파일을 살펴보겠습니다. 이 파일은 바로 사용이 가능한 하나의 팩토리 정의를 가지고 있습니다.

    namespace Database\Factories;

    use App\Models\User;
    use Illuminate\Database\Eloquent\Factories\Factory;
    use Illuminate\Support\Str;

    class UserFactory extends Factory
    {
        /**
         * The name of the factory's corresponding model.
         *
         * @var string
         */
        protected $model = User::class;

        /**
         * Define the model's default state.
         *
         * @return array
         */
        public function definition()
        {
            return [
                'name' => $this->faker->name,
                'email' => $this->faker->unique()->safeEmail,
                'email_verified_at' => now(),
                'password' => '$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi', // password
                'remember_token' => Str::random(10),
            ];
        }
    }

보시다시피 가장 기본적인 형태에서 팩토리는 라 라벨의 기본 팩토리 클래스를 확장하고 `model` 속성과 `definition` 메서드를 정의하는 클래스입니다. `definition` 메소드는 팩토리를 사용하여 모델을 만들 때 적용해야하는 기본 속성 값 집합을 반환합니다.

`faker` 속성을 통해 팩토리는 [Faker](https://github.com/fzaninotto/Faker) PHP 라이브러에 액세스하여 테스트를위한 다양한 종류의 랜덤 데이터를 편리하게 생성 할 수 있습니다.

> {tip} `config/app.php` 설정 파일에 `faker_locale` 옵션을 추가하여 Faker 로케일을 설정할 수 있습니다.

<a name="factory-states"></a>
### 팩토리 상태(States)

상태 조작 방법을 사용하면 어떤 조합 으로든 모델 팩토리에 적용 할 수있는 개별 수정을 정의 할 수 있습니다. 예를 들어 `User` 모델은 기본 속성 값 중 하나를 수정하는 `suspended` 상태 일 수 있습니다. 기본 팩토리의 `state` 메소드를 사용하여 상태 변환을 정의 할 수 있습니다. 상태 메소드의 이름을 원하는대로 지정할 수 있습니다. 결국, 이것은 일반적인 PHP 메소드 일뿐입니다.

    /**
     * Indicate that the user is suspended.
     *
     * @return \Illuminate\Database\Eloquent\Factories\Factory
     */
    public function suspended()
    {
        return $this->state([
            'account_status' => 'suspended',
        ]);
    }

상태 변환이 팩토리에서 정의한 다른 속성에 액세스해야하는 경우 `state` 메소드에 콜백을 전달할 수 있습니다. 콜백은 팩토리에 대해 정의 된 원시 속성 배열을 수신합니다.

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

팩토리 콜백은 `afterMaking` 와 `afterCreating` 메소드를 사용하여 등록합니다. 이는 모델을 생성할 때나 생성된 다음에 추가적인 작업을 수행할 수 있게 해줍니다. 팩토리 클래스에서 `configure` 메소드를 정의하여 이러한 콜백을 등록해야합니다. 이 메서드는 팩토리가 인스턴스화 될 때 Laravel에 의해 자동으로 호출됩니다.

    namespace Database\Factories;

    use App\Models\User;
    use Illuminate\Database\Eloquent\Factories\Factory;
    use Illuminate\Support\Str;

    class UserFactory extends Factory
    {
        /**
         * The name of the factory's corresponding model.
         *
         * @var string
         */
        protected $model = User::class;

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

<a name="using-factories"></a>
## 팩토리 사용하기

<a name="creating-models"></a>
### 모델 생성하기

팩토리를 정의한 후에는, 해당 모델에 대한 팩토리 인스턴스를 인스턴스화하기 위해 Eloquent 모델에 `Illuminate\Database\Eloquent\Factories\HasFactory` 트레이트가 제공하는 정적 `factory` 메소드를 사용할 수 있습니다.

    namespace App\Models;

    use Illuminate\Database\Eloquent\Factories\HasFactory;
    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        use HasFactory;
    }

모델 생성의 몇 가지 예를 살펴 보겠습니다. 먼저 `make` 메소드를 사용하여 모델을 데이터베이스에 저장하지 않고 생성합니다.

    use App\Models\User;

    public function testDatabase()
    {
        $user = User::factory()->make();

        // Use model in tests...
    }

`count` 메소드를 사용하여 여러 모델의 컬렉션을 만들 수 있습니다.

    // Create three App\Models\User instances...
    $users = User::factory()->count(3)->make();

`HasFactory` 트레이트의 `factory` 메소드는 규칙을 사용하여 모델에 적합한 팩토리를 결정합니다. 특히 이 메소드는 모델 이름과 일치하는 클래스 이름이 있고 접미사가 `Factory` 인 `Database\Factories` 네임 스페이스에서 팩토리를 찾습니다. 이러한 규칙이 특정 애플리케이션이나 팩토리에 적용되지 않는 경우 모델의 `newFactory` 메서드를 덮어 써 모델의 해당 팩토리 인스턴스를 직접 반환 할 수 있습니다.

    /**
     * Create a new factory instance for the model.
     *
     * @return \Illuminate\Database\Eloquent\Factories\Factory
     */
    protected static function newFactory()
    {
        return \Database\Factories\Administration\FlightFactory::new();
    }

#### 상태(States) 적용하기

어떤 모델이라도 [팩토리 상태(states)](#factory-states)를 적용할 수 있습니다. 모델에 여러개의 팩토리 상태(state) 변경을 지정하고자 한다면, 상태 메소드를 직접 호출 할 수 있습니다.

    $users = User::factory()->count(5)->suspended()->make();

#### 속성 재지정(오버라이딩) 하기

모델의 몇몇 기본값들을 대체하고 싶다면 `make` 메소드로 값들의 배열을 전달하면 됩니다. 지정된 값들만 대체되고 나머지 값들은 팩토리에 의해 지정된 기본값들을 유지할 것입니다.

    $user = User::factory()->make([
        'name' => 'Abigail Otwell',
    ]);

또는 `state` 메소드를 팩토리 인스턴스에서 직접 호출하여 인라인 상태 변환을 수행 할 수 있습니다.

    $user = User::factory()->state([
        'name' => 'Abigail Otwell',
    ])->make();

> {tip} 팩토리를 사용하여 모델을 생성 할 때 [Mass assignment protection](/docs/{{version}}/eloquent#mass-assignment)은 자동으로 비활성화됩니다.

<a name="persisting-models"></a>
### 모델 저장하기

`create` 메소드는 모델 인스턴스를 생성하고 Eloquent의 `save` 메소드를 사용하여 데이터베이스에 유지합니다.

    use App\Models\User;

    public function testDatabase()
    {
        // Create a single App\Models\User instance...
        $user = User::factory()->create();

        // Create three App\Models\User instances...
        $users = User::factory()->count(3)->create();

        // Use model in tests...
    }

속성 배열을 `create` 메소드에 전달하여 모델의 속성을 재정의 할 수 있습니다.

    $user = User::factory()->create([
        'name' => 'Abigail',
    ]);

<a name="sequences"></a>
### 시퀀스

때로는 생성 된 각 모델에 대해 주어진 모델 속성의 값을 대체 할 수 있습니다. 상태 변환을 `Sequence` 인스턴스로 정의하여 이를 수행 할 수 있습니다. 예를 들어, 생성 된 각 사용자에 대해 `User` 모델의 `admin` 열 값을 `Y`와 `N` 사이에서 번갈아 할 수 있습니다.

    use App\Models\User;
    use Illuminate\Database\Eloquent\Factories\Sequence;

    $users = User::factory()
                    ->count(10)
                    ->state(new Sequence(
                        ['admin' => 'Y'],
                        ['admin' => 'N'],
                    ))
                    ->create();

이 예에서는 `admin` 값이 `Y` 인 5명의 사용자가 생성되고 5명의 사용자가 `admin` 값이 `N` 인 생성됩니다.

<a name="factory-relationships"></a>
## 팩토리 관계

<a name="relationships-within-definition"></a>
### 정의 내의 관계

팩토리 정의에서 모델에 관계를 추가 할 수 있습니다. 예를 들어 `Post`를 만들 때 새 `User` 인스턴스를 만들고 싶다면 다음을 수행 할 수 있습니다.

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
            'title' => $this->faker->title,
            'content' => $this->faker->paragraph,
        ];
    }

팩토리에 정의된 관계 컬럼에 의존해야하는 경우 evaluated 된 속성 배열을 입력받는 콜백을 사용 할 수 있습니다.

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
            'title' => $this->faker->title,
            'content' => $this->faker->paragraph,
        ];
    }

<a name="has-many-relationships"></a>
### 1:*(일대다) 관계

다음으로 Laravel의 유연한 팩토리 방법을 사용하여 Eloquent 모델 관계를 구축하는 방법을 살펴 보겠습니다. 먼저 애플리케이션에 `User` 모델과 `Post` 모델이 있다고 가정 해 보겠습니다. 또한 `User` 모델이 `Post` 와 `hasMany` 관계를 정의한다고 가정 해 보겠습니다. 팩토리에서 제공하는 `has` 메소드를 사용하여 3개의 게시물이있는 사용자를 생성 할 수 있습니다. `has` 메소드는 팩토리 인스턴스를 허용합니다.

    use App\Models\Post;
    use App\Models\User;

    $users = User::factory()
                ->has(Post::factory()->count(3))
                ->create();

관례 적으로 `Post` 모델을 `has` 메소드에 전달할 때 Laravel은 `User` 모델에 관계를 정의하는 `posts` 메소드가 있어야한다고 가정합니다. 필요한 경우 조작하려는 관계의 이름을 명시 적으로 지정할 수 있습니다.

    $users = User::factory()
                ->has(Post::factory()->count(3), 'posts')
                ->create();

물론 관련 모델에서 상태 조작을 수행 할 수 있습니다. 또한 상태 변경에 상위 모델에 대한 액세스가 필요한 경우 클로저 기반 상태 변환을 전달할 수 있습니다.

    $users = User::factory()
                ->has(
                    Post::factory()
                            ->count(3)
                            ->state(function (array $attributes, User $user) {
                                return ['user_type' => $user->type];
                            })
                )
                ->create();

#### 매직 메소드 사용

편의를 위해 팩토리의 매직 관계 메소드를 사용하여 관계를 정의 할 수 있습니다. 예를 들어, 다음 예제는 규칙을 사용하여 관련 모델이 `User` 모델의 `posts` 관계 메소드를 통해 생성되어야 함을 결정합니다.

    $users = User::factory()
                ->hasPosts(3)
                ->create();

매직 메소드를 사용하여 팩토리 관계를 만들 때 관련 모델을 재정의 할 속성 배열을 전달할 수 있습니다.

    $users = User::factory()
                ->hasPosts(3, [
                    'published' => false,
                ])
                ->create();

상태 변경에 상위 모델에 대한 액세스가 필요한 경우 클로저 기반 상태 변환을 제공 할 수 있습니다.

    $users = User::factory()
                ->hasPosts(3, function (array $attributes, User $user) {
                    return ['user_type' => $user->type];
                })
                ->create();

<a name="belongs-to-relationships"></a>
### 1:*(일대다) 역관계

이제 팩토리를 사용하여 "1:*(일대다) 관계" 구축하는 방법을 살펴 보았으므로 역관계를 살펴 보겠습니다. `for` 메소드는 팩토리 생성 모델이 속한 모델을 정의하는 데 사용할 수 있습니다. 예를 들어 단일 사용자에게 속한 3개의 `Post`모델 인스턴스를 만들 수 있습니다.

    use App\Models\Post;
    use App\Models\User;

    $posts = Post::factory()
                ->count(3)
                ->for(User::factory()->state([
                    'name' => 'Jessica Archer',
                ]))
                ->create();

#### 매직 메소드 사용

편의상 팩토리의 매직 관계 메서드를 사용하여 역관계를 정의 할 수 있습니다. 예를 들어 다음 예제는 규칙을 사용하여 3개의 게시물이 `Post` 모델의 `user` 관계에 속해야 함을 결정합니다.

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

    $users = User::factory()
                ->has(Role::factory()->count(3))
                ->create();

#### 피벗 테이블 속성

모델을 연결하는 피벗 / 중간 테이블에 설정해야하는 속성을 정의해야하는 경우 `hasAttached` 메서드를 사용할 수 있습니다. 이 메서드는 피벗 테이블 속성 이름과 값의 배열을 두 번째 인수로받습니다.

    use App\Models\Role;
    use App\Models\User;

    $users = User::factory()
                ->hasAttached(
                    Role::factory()->count(3),
                    ['active' => true]
                )
                ->create();

상태 변경에 관련 모델에 대한 액세스가 필요한 경우 클로저 기반 상태 변환을 제공 할 수 있습니다.

    $users = User::factory()
                ->hasAttached(
                    Role::factory()
                        ->count(3)
                        ->state(function (array $attributes, User $user) {
                            return ['name' => $user->name.' Role'];
                        }),
                    ['active' => true]
                )
                ->create();

#### 매직 메소드 사용

편의를 위해 팩토리의 매직 관계 메소드를 사용하여 다 대다 관계를 정의 할 수 있습니다. 예를 들어, 다음 예제에서는 규칙을 사용하여 관련 모델이 `User` 모델의 `roles` 관계 메소드를 통해 생성되어야 함을 결정합니다.

    $users = User::factory()
                ->hasRoles(1, [
                    'name' => 'Editor'
                ])
                ->create();

<a name="polymorphic-relationships"></a>
### 다형성 관계

[다형성 관계](/docs/{{version}}/eloquent-relationships#polymorphic-relationships)는 팩토리를 사용하여 생성 할 수도 있습니다. 다형성의 일대 다 관계는 일반적인 일대 다 관계와 동일한 방식으로 생성됩니다. 예를 들어 `Post` 모델에 `Comment` 모델과 `morphMany` 관계가있는 경우

    use App\Models\Post;

    $post = Post::factory()->hasComments(3)->create();

#### Morph To 관계

매직 메서드는 `morphTo` 관계를 만드는 데 사용할 수 없습니다. 대신 `for` 메소드를 직접 사용해야하며 관계 이름을 명시 적으로 제공해야합니다. 예를 들어 `Comment` 모델에 `morphTo` 관계를 정의하는 `commentable` 메소드가 있다고 가정 해보십시오. 이 상황에서 우리는 직접 `for` 메소드를 사용하여 단일 게시물에 속하는 3개의 댓글을 만들 수 있습니다.

    $comments = Comment::factory()->count(3)->for(
        Post::factory(), 'commentable'
    )->create();

#### 다형성 다 대다 관계

다형성 "다대 다" 관계는 다형성이 아닌 "다 대다" 관계처럼 생성 될 수 있습니다.

    use App\Models\Tag;
    use App\Models\Video;

    $users = Video::factory()
                ->hasAttached(
                    Tag::factory()->count(3),
                    ['public' => true]
                )
                ->create();

물론 매직 `has` 메소드는 다형성 "다 대다" 관계를 생성하는 데에도 사용할 수 있습니다.

    $users = Video::factory()
                ->hasTags(3, ['public' => true])
                ->create();

<a name="using-seeds"></a>
## Seed 사용

피쳐 테스트 도중 [database seeders](/docs/{{version}}/seeding)를 사용하여 데이터베이스를 채워 넣으려면 `seed` 메소드를 사용할 수 있습니다. 기본적으로 `seed` 메소드는 `DatabaseSeeder`를 반환하는데, 이 메소드는 다른 모든 seeder를 실행해야 합니다. 또는 특정 seeder 클래스 이름을 `seed` 메소드에 전달하십시오.

    <?php

    namespace Tests\Feature;

    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use OrderStatusSeeder;
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
            $this->seed(OrderStatusSeeder::class);

            // ...
        }
    }

<a name="available-assertions"></a>
## 사용 가능한 Assertions

라라벨은 [PHPUnit](https://phpunit.de/) 피쳐 테스트에서 사용가능한 데이터데이스 assertion을 제공합니다.

메소드 | 설명
------------- | -------------
`$this->assertDatabaseCount($table, int $count);`  |  데이터베이스의 테이블에 주어진 숫자의 항목이 포함되어 있는지 확인하십시오.
`$this->assertDatabaseHas($table, array $data);`  |  데이터베이스에서 테이블에 주어진 데이터가 존재하는지 확인.
`$this->assertDatabaseMissing($table, array $data);`  |  데이터베이스에서 테이블에 주어진 데이터가 존재하지 않는 것을 확인.
`$this->assertDeleted($table, array $data);`  |  주어진 레코드가 삭제되었는지 확인.
`$this->assertSoftDeleted($table, array $data);`  |  주어진 레코드가 소프트 삭제 되었는지 확인.

편의상 모델의 기본 키를 기반으로 데이터베이스에서 레코드가 삭제 또는 소프트 삭제되었는지 확인하도록 모델을 `assertDeleted` 및 `assertSoftDeleted` 헬퍼에 전달 할 수 있습니다.

예를 들어 테스트에서 모델 팩토리를 사용하는 경우, 이 모델을 이 헬퍼들 중 하나에 전달하여 애플리케이션이 데이터베이스에서 레코드를 올바르게 삭제했는지 테스트 할 수 있습니다.

    public function testDatabase()
    {
        $user = User::factory()->create();

        // Make call to application...

        $this->assertDeleted($user);
    }
