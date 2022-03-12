# 데이터베이스: 시딩

- [시작하기](#introduction)
- [Seeders 작성하기](#writing-seeders)
    - [모델 팩토리 사용하기](#using-model-factories)
    - [추가적인 Seeder 호출하기](#calling-additional-seeders)
- [Seeders 실행하기](#running-seeders)

<a name="introduction"></a>
## 시작하기

라라벨에는 시드 클래스를 사용하여 데이터로 데이터베이스를 시드하는 기능이 포함되어 있습니다. 모든 시드 클래스는 `database/seeders` 디렉토리에 저장됩니다. 기본적으로 `DatabaseSeeder` 클래스가 정의되어 있습니다. 이 클래스에서 `call` 메소드를 사용하여 다른 시드 클래스를 실행할 수 있으므로 시드 순서를 제어할 수 있습니다.

> {tip} [대량 할당 보호](/docs/{{version}}/eloquent#mass-assignment)는 데이터베이스 시딩 중에 자동으로 비활성화됩니다.

<a name="writing-seeders"></a>
## Seeder 작성하기

seeder를 생성하기 위해서는, `make:seeder` [아티즌 명령어](/docs/{{version}}/artisan)를 실행하면 됩니다. 프레임워크에 의해서 생성된 모든 seeder들은 `database/seeders` 디렉토리에 위치할 것입니다.

    php artisan make:seeder UserSeeder

시더 클래스에는 기본적으로 `run`이라는 하나의 메소드만 포함됩니다. 이 메소드는 `db:seed` [Artisan command](/docs/{{version}}/artisan)이 실행될 때 호출됩니다. `run` 메소드 내에서 원하는 대로 데이터베이스에 데이터를 삽입할 수 있습니다. [쿼리 빌더](/docs/{{version}}/queries)를 사용하여 수동으로 데이터를 삽입하거나 [Eloquent 모델 팩토리](/docs/{{version}}/database-testing#defining-model-factories)를 사용할 수 있습니다.

다음 예제에서, `DatabaseSeeder` 클래스를 수정해서 `run` 메소드에 입력 구문을 추가해보겠습니다. 

    <?php

    namespace Database\Seeders;

    use Illuminate\Database\Seeder;
    use Illuminate\Support\Facades\DB;
    use Illuminate\Support\Facades\Hash;
    use Illuminate\Support\Str;

    class DatabaseSeeder extends Seeder
    {
        /**
         * Run the database seeders.
         *
         * @return void
         */
        public function run()
        {
            DB::table('users')->insert([
                'name' => Str::random(10),
                'email' => Str::random(10).'@gmail.com',
                'password' => Hash::make('password'),
            ]);
        }
    }

> {tip} `run` 메소드에 필요한 모든 의존성의 타입힌트를 지정할 수 있습니다. 이 의존성은 Laravel [서비스 컨테이너](/docs/{{version}}/container)를 통해 자동으로 처리됩니다.

<a name="using-model-factories"></a>
### 모델 팩토리 사용하기

물론 각 모델 시드의 속성을 수동으로 지정하는 것은 번거롭습니다. 대신 [모델 팩토리](/docs/{{version}}/database-testing#defining-model-factories)를 사용하여 대량의 데이터베이스 레코드를 편리하게 생성할 수 있습니다. 먼저 [모델 팩토리 문서](/docs/{{version}}/database-testing#defining-model-factories)를 검토하여 팩토리를 정의하는 방법을 알아보세요.

예를 들어, 각각 관련 게시물이 하나씩 있는 50명의 사용자를 생성해 보겠습니다.

    use App\Models\User;

    /**
     * Run the database seeders.
     *
     * @return void
     */
    public function run()
    {
        User::factory()
                ->count(50)
                ->hasPosts(1)
                ->create();
    }

<a name="calling-additional-seeders"></a>
### 추가적인 Seeder 호출하기

`DatabaseSeeder` 클래스 내에서 `call` 메소드를 사용하여 추가 시드 클래스를 실행할 수 있습니다. `call` 메소드를 사용하면 단일 시드 클래스가 너무 커지지 않도록 데이터베이스 시드를 여러 파일로 나눌 수 있습니다. `call` 메소드는 실행되어야 하는 시드 클래스의 배열을 입력받습니다.

    /**
     * Run the database seeders.
     *
     * @return void
     */
    public function run()
    {
        $this->call([
            UserSeeder::class,
            PostSeeder::class,
            CommentSeeder::class,
        ]);
    }

<a name="running-seeders"></a>
## Seeder 실행하기

`db:seed` Artisan 명령을 실행하여 데이터베이스를 시드할 수 있습니다. 기본적으로 `db:seed` 명령은 `Database\Seeders\DatabaseSeeder` 클래스를 실행하며, 이는 차례로 다른 시드 클래스를 호출할 수 있습니다. 그러나 `--class` 옵션을 사용하여 개별적으로 실행할 특정 시드 클래스를 지정할 수 있습니다.

    php artisan db:seed

    php artisan db:seed --class=UserSeeder

모든 테이블을 삭제하고 모든 마이그레이션을 다시 실행하는 `--seed` 옵션과 함께 `migrate:fresh` 명령을 사용하여 데이터베이스를 시드할 수도 있습니다. 이 명령은 데이터베이스를 완전히 재구축하는 데 유용합니다.

    php artisan migrate:fresh --seed

<a name="forcing-seeding-production"></a>
#### 프로덕션 환경에서 강제 시딩

일부 시딩 작업으로 인해 데이터가 변경되거나 손실될 수 있습니다. 프로덕션 데이터베이스에 대해 시드 명령을 실행하지 못하도록 보호하기 위해 `production` 환경에서 시드를 실행하기 전에 확인 메시지가 표시됩니다. 확인창 없이 시더를 강제 실행하려면 `--force` 플래그를 사용하세요.

    php artisan db:seed --force
