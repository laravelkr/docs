# Database: Getting Started
# 데이터베이스 : 시작하기

- [Introduction](#introduction)
- [시작하기](#introduction)
  - [Configuration](#configuration)
  - [설정하기](#configuration)
  - [Read & Write Connections](#read-and-write-connections)
  - [읽기 & 쓰기 커넥션](#read-and-write-connections)
- [Running SQL Queries](#running-queries)
- [SQL 쿼리 실행](#running-queries)
  - [Using Multiple Database Connections](#using-multiple-database-connections)
  - [여러개의 데이터베이스 커넥션 사용하기](#using-multiple-database-connections)
  - [Listening For Query Events](#listening-for-query-events)
  - [쿼리 이벤트 리스닝](#listening-for-query-events)
- [Database Transactions](#database-transactions)
- [데이터베이스 트랙잭션](#database-transactions)
- [Connecting To The Database CLI](#connecting-to-the-database-cli)
- [데이터베이스 CLI에 연결](#connecting-to-the-database-cli)

<a name="introduction"></a>
## Introduction
## 시작하기

Almost every modern web application interacts with a database. Laravel makes interacting with databases extremely simple across a variety of supported databases using raw SQL, a [fluent query builder](/docs/{{version}}/queries), and the [Eloquent ORM](/docs/{{version}}/eloquent). Currently, Laravel provides first-party support for five databases:

거의 모든 최신 웹 애플리케이션은 데이터베이스와 상호 작용합니다. 라라벨은 원시 SQL, [유창한 쿼리 빌더](docs{{version}}queries) 및 [Eloquent ORM](docs{{version}}eloquent)을 사용하여 지원되는 다양한 데이터베이스에서 데이터베이스와 매우 간단하게 상호 작용할 수 있습니다. 현재 라라벨은 5개의 데이터베이스를 주요하게 지원하고 있습니다.

- MariaDB 10.2+ ([Version Policy](https://mariadb.org/about/#maintenance-policy))
- MySQL 5.7+ ([Version Policy](https://en.wikipedia.org/wiki/MySQL#Release_history))
- PostgreSQL 9.6+ ([Version Policy](https://www.postgresql.org/support/versioning/))
- SQLite 3.8.8+
- SQL Server 2017+ ([Version Policy](https://docs.microsoft.com/en-us/lifecycle/products/?products=sql-server))

<a name="configuration"></a>
### Configuration
### 설정하기

The configuration for Laravel's database services is located in your application's `config/database.php` configuration file. In this file, you may define all of your database connections, as well as specify which connection should be used by default. Most of the configuration options within this file are driven by the values of your application's environment variables. Examples for most of Laravel's supported database systems are provided in this file.

라라벨의 데이터베이스 서비스 설정은 애플리케이션의 `config/database.php` 설정 파일에 존재합니다. 이 파일에서 모든 데이터베이스 연결을 정의하고 기본적으로 사용해야 하는 연결을 지정할 수 있습니다. 이 파일에 있는 대부분의 설정 옵션은 애플리케이션의 환경 변수 값에 의해 결정됩니다. 라라벨에서 지원하는 대부분의 데이터베이스 시스템에 대한 예제가 이 파일에 제공됩니다.

By default, Laravel's sample [environment configuration](/docs/{{version}}/configuration#environment-configuration) is ready to use with [Laravel Sail](/docs/{{version}}/sail), which is a Docker configuration for developing Laravel applications on your local machine. However, you are free to modify your database configuration as needed for your local database.

기본적으로 라라벨의 샘플 [환경 설정](/docs/{{version}}/configuration#environment-configuration)은 로컬 머신에서 Laravel 애플리케이션을 개발하기 위한 Docker 설정인 [Laravel Sail](/docs/{{version}}/sail)과 함께 사용할 준비가 되어 있습니다. 그러나 로컬 데이터베이스에 대해 필요에 따라 데이터베이스 설정을 자유롭게 수정할 수 있습니다.

<a name="sqlite-configuration"></a>
#### SQLite Configuration
#### SQLite 설정하기

SQLite databases are contained within a single file on your filesystem. You can create a new SQLite database using the `touch` command in your terminal: `touch database/database.sqlite`. After the database has been created, you may easily configure your environment variables to point to this database by placing the absolute path to the database in the `DB_DATABASE` environment variable:

SQLite 데이터베이스는 파일시스템에 한개의 파일로 만들 수 있습니다. 터미널에서 `touch` 명령을 사용하여 새 SQLite 데이터베이스를 만들 수 있습니다(`touch database/database.sqlite`). 데이터베이스가 생성된 후 `DB_DATABASE` 환경 변수에 데이터베이스에 대한 절대 경로를 배치하여 이 데이터베이스를 가리키도록 환경 변수를 쉽게 설정할 수 있습니다.

```ini
DB_CONNECTION=sqlite
DB_DATABASE=/absolute/path/to/database.sqlite
```

To enable foreign key constraints for SQLite connections, you should set the `DB_FOREIGN_KEYS` environment variable to `true`:

SQLite 연결에 외래 키 제약 조건을 사용하려면 `DB_FOREIGN_KEYS` 환경 변수를 `true`로 설정해야합니다.

```ini
DB_FOREIGN_KEYS=true
```

<a name="mssql-configuration"></a>
#### Microsoft SQL Server Configuration
#### 마이크로소프트 SQL 서버 설정

To use a Microsoft SQL Server database, you should ensure that you have the `sqlsrv` and `pdo_sqlsrv` PHP extensions installed as well as any dependencies they may require such as the Microsoft SQL ODBC driver.

Microsoft SQL Server 데이터베이스를 사용하려면 `sqlsrv` 및 `pdo_sqlsrv` PHP 확장과 Microsoft SQL ODBC 드라이버와 같이 필요한 의존성이 설치되어 있는지 확인해야 합니다.

<a name="configuration-using-urls"></a>
#### Configuration Using URLs
#### URL을 사용하여 설정

Typically, database connections are configured using multiple configuration values such as `host`, `database`, `username`, `password`, etc. Each of these configuration values has its own corresponding environment variable. This means that when configuring your database connection information on a production server, you need to manage several environment variables.

일반적으로 데이터베이스 연결은 `host`, `database`, `username`, `password` 등과 같은 여러가지 설정 값을 사용하여 설정됩니다. 이러한 설정 값 각각은 해당 환경 변수를 갖습니다. 즉, 프로덕션 서버에서 데이터베이스 연결 정보를 설정 할 때 여러 환경 변수를 관리해야합니다.

Some managed database providers such as AWS and Heroku provide a single database "URL" that contains all of the connection information for the database in a single string. An example database URL may look something like the following:

AWS나 Heroku 같은 일부 관리형 데이터베이스 공급자는 단일 문자열에서 데이터베이스의 모든 연결 정보를 포함하는 단일 데이터베이스 "URL"을 제공합니다. 데이터베이스 URL의 예는 다음과 같습니다.

```html
mysql://root:password@127.0.0.1/forge?charset=UTF-8
```

These URLs typically follow a standard schema convention:

이러한 URL은 일반적으로 표준 스키마 규칙을 따릅니다.

```html
driver://username:password@host:port/database?options
```

For convenience, Laravel supports these URLs as an alternative to configuring your database with multiple configuration options. If the `url` (or corresponding `DATABASE_URL` environment variable) configuration option is present, it will be used to extract the database connection and credential information.

편의상 라라벨은 여러 설정 옵션으로 데이터베이스를 설정하는 대신 이러한 URL을 지원합니다. `url` (또는 대응하는`DATABASE_URL` 환경 변수) 설정 옵션이 존재하면, 데이터베이스 연결과 인증 정보를 추출하는데 사용됩니다.

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
        'sticky' => true,
        'driver' => 'mysql',
        'database' => 'database',
        'username' => 'root',
        'password' => '',
        'charset' => 'utf8mb4',
        'collation' => 'utf8mb4_unicode_ci',
        'prefix' => '',
    ],

Note that three keys have been added to the configuration array: `read`, `write` and `sticky`. The `read` and `write` keys have array values containing a single key: `host`. The rest of the database options for the `read` and `write` connections will be merged from the main `mysql` configuration array.

설정 배열에 `read`, `write` 그리고 `sticky` 세개의 키가 추가 된것을 참고하십시오. `read` 와 `write` 키는 `host`라는 하나의 키를 포함하는 배열 값입니다. `read` 와 `write` 의 나머지 데이터베이스 옵션 값은 기본 `mysql` 설정 배열에서 합쳐(merge)집니다.

You only need to place items in the `read` and `write` arrays if you wish to override the values from the main `mysql` array. So, in this case, `192.168.1.1` will be used as the host for the "read" connection, while `192.168.1.3` will be used for the "write" connection. The database credentials, prefix, character set, and all other options in the main `mysql` array will be shared across both connections. When multiple values exist in the `host` configuration array, a database host will be randomly chosen for each request.

기본 `mysql` 배열의 값을 재정의하려면 `read` 및 `write` 배열에만 항목을 배치하면 됩니다. 따라서 이 경우 `192.168.1.1`이 "읽기" 연결을 위한 호스트로 사용되고 `192.168.1.3`이 "쓰기" 연결에 사용됩니다. 기본 `mysql` 배열의 데이터베이스 자격 증명, 접두사, 문자 집합 및 기타 모든 옵션은 두 연결에서 공유됩니다. `host` 설정 배열에 값이 여러개가 있는 경우 각 요청에 대해 데이터베이스 호스트가 무작위로 선택됩니다.

<a name="the-sticky-option"></a>
#### The `sticky` Option
#### `sticky` 옵션

The `sticky` option is an *optional* value that can be used to allow the immediate reading of records that have been written to the database during the current request cycle. If the `sticky` option is enabled and a "write" operation has been performed against the database during the current request cycle, any further "read" operations will use the "write" connection. This ensures that any data written during the request cycle can be immediately read back from the database during that same request. It is up to you to decide if this is the desired behavior for your application.

`sticky` 옵션은 현재 request-요청사이클 에서 데이터베이스에 기록된 레코드를 바로 읽을 수 있도록 하는 *있어도 되고 없어도 되는* 값입니다. `sticky` 옵션이 활성화 되어 있고 현재 request-요청사이클에서 데이터베이스에 "쓰기" 작업을 수행한 경우 이 뒤에 "읽기"작업은 "쓰기"에서 사용한 커넥션으르 사용합니다. 이렇게 되면, request-요청사이클 동안에 작성된 모든 데이터를 동일한 request-요청 중에서는 바로 데이터베이스에서 읽을 수 있습니다. 여러분의 애플리케이션에서 이러한 동작이 필요한지에 대해서 결정하는 건 여러분의 선택에 따라 달라질 수 있습니다.

<a name="running-queries"></a>
## Running SQL Queries
## SQL 쿼리 실행

Once you have configured your database connection, you may run queries using the `DB` facade. The `DB` facade provides methods for each type of query: `select`, `update`, `insert`, `delete`, and `statement`.

일단 데이터베이스 연결을 설정하면 `DB` 파사드를 사용해서 쿼리를 실행 할 수 있습니다. `DB` 파사드는 각각 쿼리 타입에 해당하는 메소드 : `select`, `update`, `delete` 그리고 `statement` 메소드를 제공합니다.

<a name="running-a-select-query"></a>
#### Running A Select Query
#### Select 쿼리 실행하기

To run a basic SELECT query, you may use the `select` method on the `DB` facade:

기본 SELECT 쿼리를 실행하려면 `DB` 파사드에서 `select` 메소드를 사용할 수 있습니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Support\Facades\DB;

    class UserController extends Controller
    {
        /**
         * Show a list of all of the application's users.
         *
         * @return \Illuminate\Http\Response
         */
        public function index()
        {
            $users = DB::select('select * from users where active = ?', [1]);

            return view('user.index', ['users' => $users]);
        }
    }

The first argument passed to the `select` method is the SQL query, while the second argument is any parameter bindings that need to be bound to the query. Typically, these are the values of the `where` clause constraints. Parameter binding provides protection against SQL injection.

`select` 메소드에 전달된 첫 번째 인수는 SQL 쿼리이고 두 번째 인수는 쿼리에 바인딩해야 하는 매개변수 바인딩입니다. 일반적으로 이들은 `where` 절 제약 조건의 값입니다. 매개변수 바인딩은 SQL 주입에 대한 보호를 제공합니다.

The `select` method will always return an `array` of results. Each result within the array will be a PHP `stdClass` object representing a record from the database:

`select` 메소드는 항상 결과의 `array`를 반환합니다. 배열 내의 각 결과는 데이터베이스의 레코드를 나타내는 PHP `stdClass` 객체가 됩니다.

    use Illuminate\Support\Facades\DB;

    $users = DB::select('select * from users');

    foreach ($users as $user) {
        echo $user->name;
    }

<a name="using-named-bindings"></a>
#### Using Named Bindings
#### 이름이 부여된 바인딩 사용하기

Instead of using `?` to represent your parameter bindings, you may execute a query using named bindings:

`?` 를 사용하는 파라미터 바인딩 대신에, 이름을 지정한 바인딩을 사용한 쿼리를 실행시킬 수 있습니다.

    $results = DB::select('select * from users where id = :id', ['id' => 1]);

<a name="running-an-insert-statement"></a>
#### Running An Insert Statement
#### Insert 문 실행하기

To execute an `insert` statement, you may use the `insert` method on the `DB` facade. Like `select`, this method accepts the SQL query as its first argument and bindings as its second argument:

`insert` 문을 실행하려면 `DB` 파사드에서 `insert` 메소드를 사용할 수 있습니다. `select`와 마찬가지로 이 메서드는 SQL 쿼리를 첫 번째 인수로 받아들이고 바인딩을 두 번째 인수로 받아들입니다.

    use Illuminate\Support\Facades\DB;

    DB::insert('insert into users (id, name) values (?, ?)', [1, 'Marc']);

<a name="running-an-update-statement"></a>
#### Running An Update Statement
#### Update 쿼리 실행하기

The `update` method should be used to update existing records in the database. The number of rows affected by the statement is returned by the method:

`update` 메소드는 데이터베이스의 기존 레코드를 업데이트하는 데 사용해야 합니다. 명령문의 영향을 받는 행 수는 다음 메서드에서 반환됩니다.

    use Illuminate\Support\Facades\DB;

    $affected = DB::update(
        'update users set votes = 100 where name = ?',
        ['Anita']
    );

<a name="running-a-delete-statement"></a>
#### Running A Delete Statement
#### Delete 쿼리 실행하기

The `delete` method should be used to delete records from the database. Like `update`, the number of rows affected will be returned by the method:

`delete` 메소드는 데이터베이스에서 레코드를 삭제하는 데 사용해야 합니다. `update`와 마찬가지로 영향을 받는 행 수는 다음 메서드에 의해 반환됩니다.

    use Illuminate\Support\Facades\DB;

    $deleted = DB::delete('delete from users');

<a name="running-a-general-statement"></a>
#### Running A General Statement
#### 일반적인 구문의 쿼리 실행하기

Some database statements do not return any value. For these types of operations, you may use the `statement` method on the `DB` facade:

몇몇 데이터베이스 구문은 값을 반환하지 않습니다. 이러한 유형의 작업들을 위해서는 `DB` 파사드의 `statement` 메소드를 사용할 수 있습니다.

    DB::statement('drop table users');

<a name="running-an-unprepared-statement"></a>
#### Running An Unprepared Statement
#### 준비되지 않은 구문 실행

Sometimes you may want to execute an SQL statement without binding any values. You may use the `DB` facade's `unprepared` method to accomplish this:

값을 바인딩하지 않고 SQL 문을 실행하려는 경우가 있습니다. 이것을 수행하기 위해 `DB` 파사드의 `unprepared` 메소드를 사용할 수 있습니다.

    DB::unprepared('update users set votes = 100 where name = "Dries"');

> {note} Since unprepared statements do not bind parameters, they may be vulnerable to SQL injection. You should never allow user controlled values within an unprepared statement.

> {참고} 준비되지 않은 구문은 매개변수를 바인딩하지 않으므로 SQL 주입에 취약할 수 있습니다. 준비되지 않은 명령문 내에서 사용자 제어 값을 허용해서는 안 됩니다.

<a name="implicit-commits-in-transactions"></a>
#### Implicit Commits
#### 묵시적 커밋

When using the `DB` facade's `statement` and `unprepared` methods within transactions you must be careful to avoid statements that cause [implicit commits](https://dev.mysql.com/doc/refman/8.0/en/implicit-commit.html). These statements will cause the database engine to indirectly commit the entire transaction, leaving Laravel unaware of the database's transaction level. An example of such a statement is creating a database table:

트랜잭션 내에서 `DB` 파사드의 `statement` 및 `unprepared` 메소드를 사용할 때 [묵시적 커밋](https://dev.mysql.com/doc/refman/8.0/en/implicit-commit.html) 을 유발하는 명령문을 피하도록 주의해야 합니다. 이러한 명령문은 데이터베이스 엔진이 전체 트랜잭션을 간접적으로 커밋하도록 하여 라라벨이 데이터베이스의 트랜잭션 수준을 인식하지 못하게 합니다. 이러한 명령문의 예를들자면 데이터베이스 테이블을 생성하는 것입니다.

    DB::unprepared('create table a (col varchar(1) null)');

Please refer to the MySQL manual for [a list of all statements](https://dev.mysql.com/doc/refman/8.0/en/implicit-commit.html) that trigger implicit commits.

묵시적 커밋을 유발하는 [모든 명령문 목록](https://dev.mysql.com/doc/refman/8.0/en/implicit-commit.html) 은 MySQL 매뉴얼을 참조하십시오.

<a name="using-multiple-database-connections"></a>
### Using Multiple Database Connections
### 다중 데이터베이스 연결 사용

If your application defines multiple connections in your `config/database.php` configuration file, you may access each connection via the `connection` method provided by the `DB` facade. The connection name passed to the `connection` method should correspond to one of the connections listed in your `config/database.php` configuration file or configured at runtime using the `config` helper:

애플리케이션이 `config/database.php` 설정 파일에 다중 연결을 정의하는 경우 `DB` 파사드에서 제공하는 `connection` 메소드를 통해 각 연결에 액세스할 수 있습니다. `connection` 메소드에 전달된 연결 이름은 `config/database.php` 설정 파일에 나열된 연결 중 하나와 일치하거나 런타임 시 `config` 헬퍼를 사용하여 설정해야 합니다.

    use Illuminate\Support\Facades\DB;

    $users = DB::connection('sqlite')->select(...);

You may access the raw, underlying PDO instance of a connection using the `getPdo` method on a connection instance:

연결 인스턴스에서 `getPdo` 메서드를 사용하여 연결의 기본 PDO 인스턴스에 액세스할 수 있습니다.

    $pdo = DB::connection()->getPdo();

<a name="listening-for-query-events"></a>
### Listening For Query Events
### 쿼리 이벤트 리스닝

If you would like to specify a closure that is invoked for each SQL query executed by your application, you may use the `DB` facade's `listen` method. This method can be useful for logging queries or debugging. You may register your query listener closure in the `boot` method of a [service provider](/docs/{{version}}/providers):

애플리케이션이 실행하는 각 SQL 쿼리에 대해 호출되는 클로저를 지정하려면 `DB` 파사드의 `listen` 메소드를 사용할 수 있습니다. 이 방법은 쿼리 로깅이나 디버깅에 유용할 수 있습니다. [service provider](/docs/{{version}}/providers)의 `boot` 메소드에 쿼리 리스너 클로저를 등록할 수 있습니다.

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
                // $query->sql;
                // $query->bindings;
                // $query->time;
            });
        }
    }

<a name="database-transactions"></a>
## Database Transactions
## 데이터베이스 트랜잭션

You may use the `transaction` method provided by the `DB` facade to run a set of operations within a database transaction. If an exception is thrown within the transaction closure, the transaction will automatically be rolled back and the exception is re-thrown. If the closure executes successfully, the transaction will automatically be committed. You don't need to worry about manually rolling back or committing while using the `transaction` method:

데이터베이스 트랜잭션 내에서 일련의 작업을 실행하기 위해 `DB` 파사드에서 제공하는 `transaction` 메소드를 사용할 수 있습니다. 트랜잭션 클로저 내에서 예외가 발생하면 트랜잭션이 자동으로 롤백되고 예외가 다시 발생합니다. 클로저가 성공적으로 실행되면 트랜잭션이 자동으로 커밋됩니다. `transaction` 메소드를 사용하는 동안 수동으로 롤백하거나 커밋하는 것에 대해 걱정할 필요가 없습니다.

    use Illuminate\Support\Facades\DB;

    DB::transaction(function () {
        DB::update('update users set votes = 1');

        DB::delete('delete from posts');
    });

<a name="handling-deadlocks"></a>
#### Handling Deadlocks
#### 데드락 처리하기

The `transaction` method accepts an optional second argument which defines the number of times a transaction should be retried when a deadlock occurs. Once these attempts have been exhausted, an exception will be thrown:

`transaction` 메소드는 교착 상태가 발생할 때 트랜잭션을 재시도해야 하는 횟수를 정의하는 선택적 두 번째 인수를 허용합니다. 이러한 시도가 모두 소진되면 예외가 발생합니다.

    use Illuminate\Support\Facades\DB;

    DB::transaction(function () {
        DB::update('update users set votes = 1');

        DB::delete('delete from posts');
    }, 5);

<a name="manually-using-transactions"></a>
#### Manually Using Transactions
#### 수동으로 트랜잭션 사용하기

If you would like to begin a transaction manually and have complete control over rollbacks and commits, you may use the `beginTransaction` method provided by the `DB` facade:

트랜잭션을 수동으로 시작하고 롤백과 커밋을 완전히 제어하려면 `DB` 파사드에서 제공하는 `beginTransaction` 메소드를 사용할 수 있습니다.

    use Illuminate\Support\Facades\DB;

    DB::beginTransaction();

You can rollback the transaction via the `rollBack` method:

`rollBack` 메소드는 트랜잭션을 롤백 할 수 있습니다.

    DB::rollBack();

Lastly, you can commit a transaction via the `commit` method:

마지막으로 `commit` 메소드는 트랜잭션을 커밋 할 수 있습니다.

    DB::commit();

> {tip} The `DB` facade's transaction methods control the transactions for both the [query builder](/docs/{{version}}/queries) and [Eloquent ORM](/docs/{{version}}/eloquent).

> {tip} `DB` 파사드의 트랜잭션 메소드는 [query builder](/docs/{{version}}/queries) 및 [Eloquent ORM](/docs/{{version}}/eloquent) 모두에 대한 트랜잭션을 제어합니다.

<a name="connecting-to-the-database-cli"></a>
## Connecting To The Database CLI
## 데이터베이스 CLI에 연결

If you would like to connect to your database's CLI, you may use the `db` Artisan command:

데이터베이스의 CLI에 연결하려면 `db` Artisan 명령을 사용할 수 있습니다.

```shell
php artisan db
```

If needed, you may specify a database connection name to connect to a database connection that is not the default connection:

필요한 경우 기본 연결이 아닌 데이터베이스 연결에 연결할 데이터베이스 연결 이름을 지정할 수 있습니다.

```shell
php artisan db mysql
```
