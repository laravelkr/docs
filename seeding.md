# 데이터베이스: 시딩

- [시작하기](#introduction)
- [Seeders 작성하기](#writing-seeders)
    - [모델 팩토리 사용하기](#using-model-factories)
    - [추가적인 Seeder 호출하기](#calling-additional-seeders)
- [Seeders 실행하기](#running-seeders)

<a name="introduction"></a>
## 시작하기

라라벨에서는 시드(seed) 클래스를 사용해서 테스트 데이터를 데이터베이스에 설정하는 간단한 메소드를 포함하고 있습니다. 모든 시드(Seed) 클래스는 `database/seeds` 디렉토리에 저장됩니다. 시드(Seed)클래스의 이름은 아무렇게나 작성할 수 있지만 가급적 `UsersTableSeeder`등과 같이 직관적인 이름 규칙을 따르는 것이 좋습니다. 기본값으로 `DatabaseSeeder`클래스가 정의 되어 있습니다. 이 클래스에서 `call` 메소드로 다른 시드(seed) 클래스를  호출해서 시딩(seeding) 순서를 조정 할 수 있습니다.

<a name="writing-seeders"></a>
## Seeder 작성하기

seeder를 생성하기 위해서는, `make:seeder` [아티즌 명령어](/docs/{{version}}/artisan)를 실행하면 됩니다. 프레임워크에 의해서 생성된 모든 seeder들은 `database/seeds` 디렉토리에 위치할 것입니다.

    php artisan make:seeder UsersTableSeeder

seeder 클래스는 기본적으로 `run` 이라는 하나의 메소드만 가지고 있습니다. 이 메소드는 `db:seed` [아티즌 명령어](/docs/{{version}}/artisan)가 실행될 때 호출 됩니다. `run` 메소드 안에서 여러분은 원하는 어떤 데이터라도 입력할 수 있습니다. 직접 [query builder](/docs/{{version}}/queries)를 사용하여 데이터를 입력하거나, [Eloquent 모델 팩토리](/docs/{{version}}/database-testing#writing-factories)를 사용할 수 있습니다.

> {tip} 데이터베이스 시딩이 진행되는 동안 [Mass assignment protection](/docs/{{version}}/eloquent#mass-assignment)는 자동으로 동작하지 않습니다.

다음 예제에서, `DatabaseSeeder` 클래스를 수정해서 `run` 메소드에 입력 구문을 추가해보겠습니다. 

    <?php

    use Illuminate\Support\Str;
    use Illuminate\Database\Seeder;
    use Illuminate\Support\Facades\DB;

    class DatabaseSeeder extends Seeder
    {
        /**
         * Run the database seeds.
         *
         * @return void
         */
        public function run()
        {
            DB::table('users')->insert([
                'name' => Str::random(10),
                'email' => Str::random(10).'@gmail.com',
                'password' => bcrypt('password'),
            ]);
        }
    }

> {tip} `run` 메소드에 필요한 모든 의존성의 타입힌트를 지정할 수 있습니다. 이 의존성은 Laravel [서비스 컨테이너](/docs/{{version}}/container)를 통해 자동으로 처리됩니다.

<a name="using-model-factories"></a>
### 모델 팩토리 사용하기

당연하게도, 수동으로 각각의 모델 seed 를 위한 속성들을 지정하는 것은 번거로운 일입니다. 대신에, 여러분은 대량의 데이터베이스 레코드를 생성하는데 유용한 [모델 팩토리](/docs/{{version}}/database-testing#writing-factories)를 사용할 수 있습니다. 먼저 [모델 팩토리 문서](/docs/{{version}}/database-testing#writing-factories)를 확인하고, 팩토리를 어떻게 정의하는지 알아 봅시다. 팩토리를 정의했다면, `factory` 헬퍼 함수를 사용하여 데이터베이스에 레코드를 추가할 수 있습니다. 

예를 들어, 50 개의 레코드를 생성하고 각각의 사용자에 관계 모델을 추가해 봅시다.

    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        factory(App\User::class, 50)->create()->each(function ($user) {
            $user->posts()->save(factory(App\Post::class)->make());
        });
    }

<a name="calling-additional-seeders"></a>
### 추가적인 Seeder 호출하기

`DatabaseSeeder` 클래스 안에서, `call` 메소드를 사용하여 추가적인 seeder 클래스를 실행할 수 있습니다. `call` 메소드를 사용하면, 여러분의 데이터베이스 seeding 을 하나의 매우 커다란 파일에서 여러개의 파일로 쪼갤 수 있게 됩니다. 메소드에서는 실행하려는 seeder 클래스의 이름을 전달하면 됩니다.

    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        $this->call([
            UsersTableSeeder::class,
            PostsTableSeeder::class,
            CommentsTableSeeder::class,
        ]);
    }

<a name="running-seeders"></a>
## Seeder 실행하기

seeder 클래스를 작성하고 나면, `dump-autoload` 명령어를 사용해서 컴포저의 오토로더를 다시 생성할 필요가 있습니다.

    composer dump-autoload

이제, `db:seed` 아티즌 명령어를 통해서 데이터베이스에 데이터를 입력할 수 있습니다. 기본적으로 `db:seed` 명령어는 다른 시드(seed) 클래스들을 호출하는 `DatabaseSeeder`클래스를 실행합니다. 하지만 `--class`옵션을 사용해서 특정 시더(seeder) 클래스가 개별적으로 실행되도록 지정할 수 있습니다.

    php artisan db:seed

    php artisan db:seed --class=UsersTableSeeder

`migrate:refresh`명령어를 사용하면 데이터베이스 초기값을 설정할 때 모든 마이그레이션들을 롤백한 다음 다시 실행 할 것입니다. 이 명령어는 데이터베이스를 완전히 재구성할 때 유용합니다. 

    php artisan migrate:refresh --seed

<a name="forcing-seeding-production"></a>
#### 프로덕션 환경에서 강제 시딩

일부 시드 작업으로 인해 데이터가 변경되거나 손실 될 수 있습니다. 프로덕션 데이터베이스에 대해 시드 명령을 실행하지 못하도록하기 위해 시드를 실행하기 전에 확인 메시지가 나타납니다. 시드를 프롬프트없이 실행하게하려면 `--force` 플래그를 사용하십시오.

    php artisan db:seed --force
