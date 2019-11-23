# 데이터베이스 : 시작하기

- [시작하기](#introduction)
    - [설정하기](#configuration)
    - [읽기 & 쓰기 커넥션](#read-and-write-connections)
    - [여러개의 데이터베이스 커넥션 사용하기](#using-multiple-database-connections)
- [Raw SQL 쿼리 실행하기](#running-queries)
- [쿼리 이벤트 리스닝](#listening-for-query-events)
- [데이터베이스 트랙잭션](#database-transactions)

<a name="introduction"></a>
## 시작하기

라라벨은 직접 raw SQL을 사용할 때에도, [쿼리 빌더](/docs/{{version}}/queries)를 사용하거나 그리고 [Eloquent ORM](/docs/{{version}}/eloquent)을 사용할 때에도 데이터베이스를 처리하는 것이 쉽도록 해줍니다. 현재 라라벨은 4가지 데이터베이스를 지원하고 있습니다.

- PostgreSQL 9.4+ ([Version Policy](https://www.postgresql.org/support/versioning/))
- SQLite 3.8.8+
- SQL Server 2017+ ([Version Policy](https://support.microsoft.com/en-us/lifecycle/search))

<a name="configuration"></a>
### 설정하기

애플리케이션을 위한 데이터베이스 설정 파일은 `config/database.php`에 있습니다. 이 파일에서 모든 데이터베이스 커넥션에 대한 설정을 정의하고 기본적으로 사용할 커넥션을 지정할 수 있습니다. 이 파일에서는 지원하는 대부분의 데이터베이스 예제가 들어 있습니다.

기본적으로 라라벨의 샘플 [환경 설정](/docs/{{version}}/configuration#environment-configuration)은 여러분의 로컬 머신에서 라라벨 개발 환경을 구축할 수 있는 편리한 방법을 제공하는 [라라벨 홈스테드](/docs/{{version}}/homestead)에 맞춰져 있습니다. 로컬 데이터베이스에 맞게 변경하시면 됩니다.

#### SQLite 설정하기

`touch database/database.sqlite` 와 같은 명령어를 사용하여 SQLite 데이터베이스를 새롭게 생성한 뒤에, 데이터베이스의 절대 경로를 사용하여 새롭게 생성된 데이터베이스를 지정하는 환경 설정을 손쉽게 추가할 수 있습니다.

    DB_CONNECTION=sqlite
    DB_DATABASE=/absolute/path/to/database.sqlite

SQLite 연결에 외래 키 제약 조건을 사용하려면 `DB_FOREIGN_KEYS` 환경 변수를 `true`로 설정해야합니다.

    DB_FOREIGN_KEYS=true

#### URL을 사용하여 구성

일반적으로 데이터베이스 연결은 `host`, `database`, `username`, `password` 등과 같은 여러가지 설정 값을 사용하여 구성됩니다. 이러한 구성 값 각각은 해당 환경 변수를 갖습니다. 즉, 프로덕션 서버에서 데이터베이스 연결 정보를 구성 할 때 여러 환경 변수를 관리해야합니다.

Heroku와 같은 일부 관리 데이터베이스 공급자는 단일 문자열에서 데이터베이스의 모든 연결 정보를 포함하는 단일 데이터베이스 "URL"을 제공합니다. 데이터베이스 URL의 예는 다음과 같습니다.

    mysql://root:password@127.0.0.1/forge?charset=UTF-8

이러한 URL은 일반적으로 표준 스키마 규칙을 따릅니다.

    driver://username:password@host:port/database?options

편의상 Laravel은 여러 구성 옵션으로 데이터베이스를 구성하는 대신 이러한 URL을 지원합니다. `url` (또는 대응하는`DATABASE_URL` 환경 변수) 설정 옵션이 존재하면, 데이터베이스 연결과 인증 정보를 추출하는데 사용됩니다.

<a name="read-and-write-connections"></a>
### 읽기 & 쓰기 커넥션

SELECT문에서 사용하는 데이터베이스와 INSERT, UPDATE 그리고 DELETE문을 사용하는 데이터 베이스에 다른 연결을 사용할고 싶은 경우도 있습니다. 라라벨은 이를 쉽게 할 수 있습니다. Raw 쿼리를 사용하든, 쿼리 빌더 또는 Eloquent ORM 을 사용하든 적절한 연결들을 사용할 수 있습니다.

다음은 어떻게 read / write 커넥션을 설정하는지에 대한 예제 입니다.

    'mysql' => [
        'read' => [
            'host' => [
                '192.168.1.1',
                '196.168.1.2',
            ],
        ],
        'write' => [
            'host' => [
                '196.168.1.3',
             ],
        ],
        'sticky'    => true,
        'driver'    => 'mysql',
        'database'  => 'database',
        'username'  => 'root',
        'password'  => '',
        'charset'   => 'utf8mb4',
        'collation' => 'utf8mb4_unicode_ci',
        'prefix'    => '',
    ],

설정 배열에 `read`, `write` 그리고 `sticky` 세개의 키가 추가 된것을 참고하십시오. `read` 와 `write` 키는 `host`라는 하나의 키를 포함하는 배열 값입니다. `read` 와 `write` 의 나머지 데이터베이스 옵션 값은 기본 `mysql` 배열에서 합쳐(merge)집니다.

따라서 메인 배열에서 재정의하고자하는 값들을 `read`와 `write` 배열에 입력하기만 하면 됩니다. 위의 경우에서는 `192.168.1.1`는 "read(읽기용)" 커넥션에 대한 호스트로 사용되고, `192.168.1.3`는  "write(쓰기용)" 커넥션에 대한  호스트로 사용될 것입니다. 메인 `mysql`설정 배열에 포함된 데이터베이스 연결정보, 프리픽스, 캐릭터 셋 등 다른 모든 옵션들은 양쪽연결에서 모두 공유될 것입니다.

#### `sticky` 옵션

`sticky` 옵션은 현재 request-요청사이클 에서 데이터베이스에 기록된 레코드를 바로 읽을 수 있도록 하는 *있어도 되고 없어도 되는* 값입니다. `sticky` 옵션이 활성화 되어 있고 현재 request-요청사이클에서 데이터베이스에 "쓰기" 작업을 수행한 경우 이 뒤에 "읽기"작업은 "쓰기"에서 사용한 커넥션으르 사용합니다. 이렇게 되면, request-요청사이클 동안에 작성된 모든 데이터를 동일한 request-요청 중에서는 바로 데이터베이스에서 읽을 수 있습니다. 여러분의 애플리케이션에서 이러한 동작이 필요한지에 대해서 결정하는 건 여러분의 선택에 따라 달라질 수 있습니다.

<a name="using-multiple-database-connections"></a>
### 여러개의 데이터베이스 커넥션 사용하기

여러개의 커넥션을 사용하는 경우, `DB` 파사드의 `connection` 메소드를 통해 각각의 커넥션에 액세스 할 수 있습니다. `connection` 메소드에 전달되는 `name`은 `config/database.php` 설정 파일에 나열되어 있는 이름이어야 합니다.

    $users = DB::connection('foo')->select(...);

또한 커넥션 인스턴스에서 `getPdo` 메소드를 사용하여 PDO 인스턴스로 액세스 할 수 있습니다.

    $pdo = DB::connection()->getPdo();

<a name="running-queries"></a>
## Raw 쿼리 실행

일단 데이터베이스 연결을 설정하면 `DB` 파사드를 사용해서 쿼리를 실행 할 수 있습니다. `DB` 파사드는 각각 쿼리 타입에 해당하는 메소드 : `select`, `update`, `delete` 그리고 `statement` 메소드를 제공합니다.

#### Select 쿼리 실행하기

기본적인 쿼리를 실행하기 위해서 `DB` 파사드의 `select` 메소드를 사용할 수 있습니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Support\Facades\DB;

    class UserController extends Controller
    {
        /**
         * Show a list of all of the application's users.
         *
         * @return Response
         */
        public function index()
        {
            $users = DB::select('select * from users where active = ?', [1]);

            return view('user.index', ['users' => $users]);
        }
    }

`select` 메소드의 첫번째 인자는 raw SQL 쿼리이고, 두번째는 쿼리에 바이딩될 파라미터 입니다. 일반적으로 파라미터들은 `where` 절을 위한 값들입니다. 파라미터 바인딩은 SQL 인젝션을 방지하기 위해 제공됩니다.

`select` 메소드는 항상 결과를 `배열`로 반환합니다. 배열안의 값들은 PHP 의 `stdClass` 객체 형태로 다음과 같이 결과 값에 엑세스 할 수 있습니다.

    foreach ($users as $user) {
        echo $user->name;
    }

#### 이름이 부여된 바인딩 사용하기

`?` 를 사용하는 파라미터 바인딩 대신에, 이름을 지정한 바인딩을 사용한 쿼리를 실행시킬 수 있습니다.

    $results = DB::select('select * from users where id = :id', ['id' => 1]);

#### Insert 문 실행하기

`insert` 쿼리문을 실행하기 위해서는 `DB` 파사드의 `insert` 메소드를 사용하면 됩니다. `select` 메소드와 마찬가지로, 이 메소드는 첫번째 인자로 raw SQL 쿼리를, 두번째로 바인딩할 인자들을 전달 받습니다.

    DB::insert('insert into users (id, name) values (?, ?)', [1, 'Dayle']);

#### Update 쿼리 실행하기

`update` 메소드는 데이터베이스에 존재하는 레코드 업데이트 하는데 사용되어집니다. 그 결과 영향을 받은 레코드들의 개수가 반환될 것입니다.

    $affected = DB::update('update users set votes = 100 where name = ?', ['John']);

#### Delete 쿼리 실행하기

`delete` 메소드는 데이터베이스에서 레코드를 삭제하는데 사용됩니다. `update` 와 같이, 영향을 받은 레코드 개수가 반환됩니다.

    $deleted = DB::delete('delete from users');

#### 일반적인 구문의 쿼리 실행하기

몇몇 데이터베이스 구문은 값을 반환하지 않습니다. 이러한 유형의 작업들을 위해서는 `DB` 파사드의 `statement` 메소드를 사용할 수 있습니다.

    DB::statement('drop table users');

<a name="listening-for-query-events"></a>
## 쿼리 이벤트 리스닝

애플리케이션에서 실행되는 각각의 쿼리를 확인하고자 한다면 `listen` 메소드를 사용하면 됩니다. 이 메소드는 쿼리를 로그로 남기거나, 디버깅 할 때 유용합니다. [서비스 프로바이더](/docs/{{version}}/providers)에서 쿼리 리스너를 등록할 수 있습니다.

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\DB;
    use Illuminate\Support\ServiceProvider;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Register any application services.
         *
         * @return void
         */
        public function register()
        {
            //
        }

        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            DB::listen(function ($query) {
                // $query->sql
                // $query->bindings
                // $query->time
            });
        }
    }

<a name="database-transactions"></a>
## 데이터베이스 트랜잭션

일련의 쿼리들을 하나의 데이터베이스 트랜잭션으로 실행시키기 위해서 `DB` 파사드의 `transaction`메소드를 사용할 수 있습니다. 트랜잭션 메소드에 전달된 `Closure` 안에서 예외-exception 이 발생하게 되면 트랜잭션은 자동으로 롤백됩니다. `Closure`가 성공적으로 실행되면 트랜잭션은 자동으로 커밋됩니다. `transaction` 메소드를 사용하게 되면 일일이 롤백과 커밋에 대해서 걱정할 필요가 없습니다.

    DB::transaction(function () {
        DB::table('users')->update(['votes' => 1]);

        DB::table('posts')->delete();
    });

#### 데드락 처리하기

`transaction` 메소드는 데드락이 발생하면 트랜젝션을 재시도 해야 하는 횟수를 정의하는 두번째 인자를 선택적으로 받아들입니다. 이러한 시도가 모두 종료되면, exception이 발생합니다.

    DB::transaction(function () {
        DB::table('users')->update(['votes' => 1]);

        DB::table('posts')->delete();
    }, 5);


#### 수동으로 트랙잭션 사용하기

트랜잭션을 직접 시작하고 롤백과 커밋을 제어하고 싶은 경우는 `DB` 파사드의 `beginTransaction` 메소드를 사용하면 됩니다.

    DB::beginTransaction();

`rollBack` 메소드는 트랜잭션을 롤백 할 수 있습니다.

    DB::rollBack();

마지막으로 `commit` 메소드는 트랜잭션을 커밋 할 수 있습니다.

    DB::commit();

> {tip} `DB` 파사드의 트랜잭션 메소드는 [쿼리 빌더](/docs/{{version}}/queries) 와 [Eloquent ORM](/docs/{{version}}/eloquent)에 모두에서, 트랜잭션을 제어 할 수도 있습니다.
