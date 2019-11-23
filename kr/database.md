# Database: Getting Started
# 데이터베이스 : 시작하기

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [Configuration](#configuration)
    - [설정하기](#configuration)
    - [Read & Write Connections](#read-and-write-connections)
    - [읽기 & 쓰기 커넥션](#read-and-write-connections)
    - [Using Multiple Database Connections](#using-multiple-database-connections)
    - [여러개의 데이터베이스 커넥션 사용하기](#using-multiple-database-connections)
- [Running Raw SQL Queries](#running-queries)
- [Raw SQL 쿼리 실행하기](#running-queries)
- [Listening For Query Events](#listening-for-query-events)
- [쿼리 이벤트 리스닝](#listening-for-query-events)
- [Database Transactions](#database-transactions)
- [데이터베이스 트랙잭션](#database-transactions)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel makes interacting with databases extremely simple across a variety of database backends using either raw SQL, the [fluent query builder](/docs/{{version}}/queries), and the [Eloquent ORM](/docs/{{version}}/eloquent). Currently, Laravel supports four databases:

라라벨은 직접 raw SQL을 사용할 때에도, [쿼리 빌더](/docs/{{version}}/queries)를 사용하거나 그리고 [Eloquent ORM](/docs/{{version}}/eloquent)을 사용할 때에도 데이터베이스를 처리하는 것이 쉽도록 해줍니다. 현재 라라벨은 4가지 데이터베이스를 지원하고 있습니다.

- MySQL 5.6+ ([Version Policy](https://en.wikipedia.org/wiki/MySQL#Release_history))
- PostgreSQL 9.4+ ([Version Policy](https://www.postgresql.org/support/versioning/))
- SQLite 3.8.8+
- SQL Server 2017+ ([Version Policy](https://support.microsoft.com/en-us/lifecycle/search))

<a name="configuration"></a>
### Configuration
### 설정하기

The database configuration for your application is located at `config/database.php`. In this file you may define all of your database connections, as well as specify which connection should be used by default. Examples for most of the supported database systems are provided in this file.

애플리케이션을 위한 데이터베이스 설정 파일은 `config/database.php`에 있습니다. 이 파일에서 모든 데이터베이스 커넥션에 대한 설정을 정의하고 기본적으로 사용할 커넥션을 지정할 수 있습니다. 이 파일에서는 지원하는 대부분의 데이터베이스 예제가 들어 있습니다.

By default, Laravel's sample [environment configuration](/docs/{{version}}/configuration#environment-configuration) is ready to use with [Laravel Homestead](/docs/{{version}}/homestead), which is a convenient virtual machine for doing Laravel development on your local machine. You are free to modify this configuration as needed for your local database.

기본적으로 라라벨의 샘플 [환경 설정](/docs/{{version}}/configuration#environment-configuration)은 여러분의 로컬 머신에서 라라벨 개발 환경을 구축할 수 있는 편리한 방법을 제공하는 [라라벨 홈스테드](/docs/{{version}}/homestead)에 맞춰져 있습니다. 로컬 데이터베이스에 맞게 변경하시면 됩니다.

#### SQLite Configuration
#### SQLite 설정하기

After creating a new SQLite database using a command such as `touch database/database.sqlite`, you can easily configure your environment variables to point to this newly created database by using the database's absolute path:

`touch database/database.sqlite` 와 같은 명령어를 사용하여 SQLite 데이터베이스를 새롭게 생성한 뒤에, 데이터베이스의 절대 경로를 사용하여 새롭게 생성된 데이터베이스를 지정하는 환경 설정을 손쉽게 추가할 수 있습니다.

    DB_CONNECTION=sqlite
    DB_DATABASE=/absolute/path/to/database.sqlite

To enable foreign key constraints for SQLite connections, you should set the `DB_FOREIGN_KEYS` environment variable to `true`:

SQLite 연결에 외래 키 제약 조건을 사용하려면 `DB_FOREIGN_KEYS` 환경 변수를 `true`로 설정해야합니다.

    DB_FOREIGN_KEYS=true

#### Configuration Using URLs
#### URL을 사용하여 구성

Typically, database connections are configured using multiple configuration values such as `host`, `database`, `username`, `password`, etc. Each of these configuration values has its own corresponding environment variable. This means that when configuring your database connection information on a production server, you need to manage several environment variables.

일반적으로 데이터베이스 연결은 `host`, `database`, `username`, `password` 등과 같은 여러가지 설정 값을 사용하여 구성됩니다. 이러한 구성 값 각각은 해당 환경 변수를 갖습니다. 즉, 프로덕션 서버에서 데이터베이스 연결 정보를 구성 할 때 여러 환경 변수를 관리해야합니다.

Some managed database providers such as Heroku provide a single database "URL" that contains all of the connection information for the database in a single string. An example database URL may look something like the following:

Heroku와 같은 일부 관리 데이터베이스 공급자는 단일 문자열에서 데이터베이스의 모든 연결 정보를 포함하는 단일 데이터베이스 "URL"을 제공합니다. 데이터베이스 URL의 예는 다음과 같습니다.

    mysql://root:password@127.0.0.1/forge?charset=UTF-8

These URLs typically follow a standard schema convention:

이러한 URL은 일반적으로 표준 스키마 규칙을 따릅니다.

    driver://username:password@host:port/database?options

For convenience, Laravel supports these URLs as an alternative to configuring your database with multiple configuration options. If the `url` (or corresponding `DATABASE_URL` environment variable) configuration option is present, it will be used to extract the database connection and credential information.

편의상 Laravel은 여러 구성 옵션으로 데이터베이스를 구성하는 대신 이러한 URL을 지원합니다. `url` (또는 대응하는`DATABASE_URL` 환경 변수) 설정 옵션이 존재하면, 데이터베이스 연결과 인증 정보를 추출하는데 사용됩니다.

<a name="read-and-write-connections"></a>
### Read & Write Connections
### 읽기 & 쓰기 커넥션

Sometimes you may wish to use one database connection for SELECT statements, and another for INSERT, UPDATE, and DELETE statements. Laravel makes this a breeze, and the proper connections will always be used whether you are using raw queries, the query builder, or the Eloquent ORM.

SELECT문에서 사용하는 데이터베이스와 INSERT, UPDATE 그리고 DELETE문을 사용하는 데이터 베이스에 다른 연결을 사용할고 싶은 경우도 있습니다. 라라벨은 이를 쉽게 할 수 있습니다. Raw 쿼리를 사용하든, 쿼리 빌더 또는 Eloquent ORM 을 사용하든 적절한 연결들을 사용할 수 있습니다.

To see how read / write connections should be configured, let's look at this example:

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

Note that three keys have been added to the configuration array: `read`, `write` and `sticky`. The `read` and `write` keys have array values containing a single key: `host`. The rest of the database options for the `read` and `write` connections will be merged from the main `mysql` array.

설정 배열에 `read`, `write` 그리고 `sticky` 세개의 키가 추가 된것을 참고하십시오. `read` 와 `write` 키는 `host`라는 하나의 키를 포함하는 배열 값입니다. `read` 와 `write` 의 나머지 데이터베이스 옵션 값은 기본 `mysql` 배열에서 합쳐(merge)집니다.

You only need to place items in the `read` and `write` arrays if you wish to override the values from the main array. So, in this case, `192.168.1.1` will be used as the host for the "read" connection, while `192.168.1.3` will be used for the "write" connection. The database credentials, prefix, character set, and all other options in the main `mysql` array will be shared across both connections.

따라서 메인 배열에서 재정의하고자하는 값들을 `read`와 `write` 배열에 입력하기만 하면 됩니다. 위의 경우에서는 `192.168.1.1`는 "read(읽기용)" 커넥션에 대한 호스트로 사용되고, `192.168.1.3`는  "write(쓰기용)" 커넥션에 대한  호스트로 사용될 것입니다. 메인 `mysql`설정 배열에 포함된 데이터베이스 연결정보, 프리픽스, 캐릭터 셋 등 다른 모든 옵션들은 양쪽연결에서 모두 공유될 것입니다.

#### The `sticky` Option
#### `sticky` 옵션

The `sticky` option is an *optional* value that can be used to allow the immediate reading of records that have been written to the database during the current request cycle. If the `sticky` option is enabled and a "write" operation has been performed against the database during the current request cycle, any further "read" operations will use the "write" connection. This ensures that any data written during the request cycle can be immediately read back from the database during that same request. It is up to you to decide if this is the desired behavior for your application.

`sticky` 옵션은 현재 request-요청사이클 에서 데이터베이스에 기록된 레코드를 바로 읽을 수 있도록 하는 *있어도 되고 없어도 되는* 값입니다. `sticky` 옵션이 활성화 되어 있고 현재 request-요청사이클에서 데이터베이스에 "쓰기" 작업을 수행한 경우 이 뒤에 "읽기"작업은 "쓰기"에서 사용한 커넥션으르 사용합니다. 이렇게 되면, request-요청사이클 동안에 작성된 모든 데이터를 동일한 request-요청 중에서는 바로 데이터베이스에서 읽을 수 있습니다. 여러분의 애플리케이션에서 이러한 동작이 필요한지에 대해서 결정하는 건 여러분의 선택에 따라 달라질 수 있습니다.

<a name="using-multiple-database-connections"></a>
### Using Multiple Database Connections
### 여러개의 데이터베이스 커넥션 사용하기

When using multiple connections, you may access each connection via the `connection` method on the `DB` facade. The `name` passed to the `connection` method should correspond to one of the connections listed in your `config/database.php` configuration file:

여러개의 커넥션을 사용하는 경우, `DB` 파사드의 `connection` 메소드를 통해 각각의 커넥션에 액세스 할 수 있습니다. `connection` 메소드에 전달되는 `name`은 `config/database.php` 설정 파일에 나열되어 있는 이름이어야 합니다.

    $users = DB::connection('foo')->select(...);

You may also access the raw, underlying PDO instance using the `getPdo` method on a connection instance:

또한 커넥션 인스턴스에서 `getPdo` 메소드를 사용하여 PDO 인스턴스로 액세스 할 수 있습니다.

    $pdo = DB::connection()->getPdo();

<a name="running-queries"></a>
## Running Raw SQL Queries
## Raw 쿼리 실행

Once you have configured your database connection, you may run queries using the `DB` facade. The `DB` facade provides methods for each type of query: `select`, `update`, `insert`, `delete`, and `statement`.

일단 데이터베이스 연결을 설정하면 `DB` 파사드를 사용해서 쿼리를 실행 할 수 있습니다. `DB` 파사드는 각각 쿼리 타입에 해당하는 메소드 : `select`, `update`, `delete` 그리고 `statement` 메소드를 제공합니다.

#### Running A Select Query
#### Select 쿼리 실행하기

To run a basic query, you may use the `select` method on the `DB` facade:

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

The first argument passed to the `select` method is the raw SQL query, while the second argument is any parameter bindings that need to be bound to the query. Typically, these are the values of the `where` clause constraints. Parameter binding provides protection against SQL injection.

`select` 메소드의 첫번째 인자는 raw SQL 쿼리이고, 두번째는 쿼리에 바이딩될 파라미터 입니다. 일반적으로 파라미터들은 `where` 절을 위한 값들입니다. 파라미터 바인딩은 SQL 인젝션을 방지하기 위해 제공됩니다.

The `select` method will always return an `array` of results. Each result within the array will be a PHP `stdClass` object, allowing you to access the values of the results:


`select` 메소드는 항상 결과를 `배열`로 반환합니다. 배열안의 값들은 PHP 의 `stdClass` 객체 형태로 다음과 같이 결과 값에 엑세스 할 수 있습니다.

    foreach ($users as $user) {
        echo $user->name;
    }

#### Using Named Bindings
#### 이름이 부여된 바인딩 사용하기

Instead of using `?` to represent your parameter bindings, you may execute a query using named bindings:

`?` 를 사용하는 파라미터 바인딩 대신에, 이름을 지정한 바인딩을 사용한 쿼리를 실행시킬 수 있습니다.

    $results = DB::select('select * from users where id = :id', ['id' => 1]);

#### Running An Insert Statement
#### Insert 문 실행하기

To execute an `insert` statement, you may use the `insert` method on the `DB` facade. Like `select`, this method takes the raw SQL query as its first argument and bindings as its second argument:

`insert` 쿼리문을 실행하기 위해서는 `DB` 파사드의 `insert` 메소드를 사용하면 됩니다. `select` 메소드와 마찬가지로, 이 메소드는 첫번째 인자로 raw SQL 쿼리를, 두번째로 바인딩할 인자들을 전달 받습니다.

    DB::insert('insert into users (id, name) values (?, ?)', [1, 'Dayle']);

#### Running An Update Statement
#### Update 쿼리 실행하기

The `update` method should be used to update existing records in the database. The number of rows affected by the statement will be returned:

`update` 메소드는 데이터베이스에 존재하는 레코드 업데이트 하는데 사용되어집니다. 그 결과 영향을 받은 레코드들의 개수가 반환될 것입니다.

    $affected = DB::update('update users set votes = 100 where name = ?', ['John']);

#### Running A Delete Statement
#### Delete 쿼리 실행하기

The `delete` method should be used to delete records from the database. Like `update`, the number of rows affected will be returned:

`delete` 메소드는 데이터베이스에서 레코드를 삭제하는데 사용됩니다. `update` 와 같이, 영향을 받은 레코드 개수가 반환됩니다.

    $deleted = DB::delete('delete from users');

#### Running A General Statement
#### 일반적인 구문의 쿼리 실행하기

Some database statements do not return any value. For these types of operations, you may use the `statement` method on the `DB` facade:

몇몇 데이터베이스 구문은 값을 반환하지 않습니다. 이러한 유형의 작업들을 위해서는 `DB` 파사드의 `statement` 메소드를 사용할 수 있습니다.

    DB::statement('drop table users');

<a name="listening-for-query-events"></a>
## Listening For Query Events
## 쿼리 이벤트 리스닝

If you would like to receive each SQL query executed by your application, you may use the `listen` method. This method is useful for logging queries or debugging. You may register your query listener in a [service provider](/docs/{{version}}/providers):

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
## Database Transactions
## 데이터베이스 트랜잭션

You may use the `transaction` method on the `DB` facade to run a set of operations within a database transaction. If an exception is thrown within the transaction `Closure`, the transaction will automatically be rolled back. If the `Closure` executes successfully, the transaction will automatically be committed. You don't need to worry about manually rolling back or committing while using the `transaction` method:

일련의 쿼리들을 하나의 데이터베이스 트랜잭션으로 실행시키기 위해서 `DB` 파사드의 `transaction`메소드를 사용할 수 있습니다. 트랜잭션 메소드에 전달된 `Closure` 안에서 예외-exception 이 발생하게 되면 트랜잭션은 자동으로 롤백됩니다. `Closure`가 성공적으로 실행되면 트랜잭션은 자동으로 커밋됩니다. `transaction` 메소드를 사용하게 되면 일일이 롤백과 커밋에 대해서 걱정할 필요가 없습니다.

    DB::transaction(function () {
        DB::table('users')->update(['votes' => 1]);

        DB::table('posts')->delete();
    });

#### Handling Deadlocks
#### 데드락 처리하기

The `transaction` method accepts an optional second argument which defines the number of times a transaction should be reattempted when a deadlock occurs. Once these attempts have been exhausted, an exception will be thrown:

`transaction` 메소드는 데드락이 발생하면 트랜젝션을 재시도 해야 하는 횟수를 정의하는 두번째 인자를 선택적으로 받아들입니다. 이러한 시도가 모두 종료되면, exception이 발생합니다.

    DB::transaction(function () {
        DB::table('users')->update(['votes' => 1]);

        DB::table('posts')->delete();
    }, 5);


#### Manually Using Transactions
#### 수동으로 트랙잭션 사용하기

If you would like to begin a transaction manually and have complete control over rollbacks and commits, you may use the `beginTransaction` method on the `DB` facade:

트랜잭션을 직접 시작하고 롤백과 커밋을 제어하고 싶은 경우는 `DB` 파사드의 `beginTransaction` 메소드를 사용하면 됩니다.

    DB::beginTransaction();

You can rollback the transaction via the `rollBack` method:

`rollBack` 메소드는 트랜잭션을 롤백 할 수 있습니다.

    DB::rollBack();

Lastly, you can commit a transaction via the `commit` method:

마지막으로 `commit` 메소드는 트랜잭션을 커밋 할 수 있습니다.

    DB::commit();

> {tip} The `DB` facade's transaction methods control the transactions for both the [query builder](/docs/{{version}}/queries) and [Eloquent ORM](/docs/{{version}}/eloquent).

> {tip} `DB` 파사드의 트랜잭션 메소드는 [쿼리 빌더](/docs/{{version}}/queries) 와 [Eloquent ORM](/docs/{{version}}/eloquent)에 모두에서, 트랜잭션을 제어 할 수도 있습니다.
