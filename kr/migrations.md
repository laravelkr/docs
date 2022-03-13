# Database: Migrations
# 데이터베이스: 마이그레이션

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Generating Migrations](#generating-migrations)
- [마이그레이션 파일 생성하기](#generating-migrations)
    - [Squashing Migrations](#squashing-migrations)
    - [스쿼싱 마이그레이션](#squashing-migrations)
- [Migration Structure](#migration-structure)
- [마이그레이션의 구조](#migration-structure)
- [Running Migrations](#running-migrations)
- [마이그레이션 실행하기](#running-migrations)
    - [Rolling Back Migrations](#rolling-back-migrations)
    - [마이그레이션 되돌리기-롤백](#rolling-back-migrations)
- [Tables](#tables)
- [테이블](#tables)
    - [Creating Tables](#creating-tables)
    - [테이블 생성하기](#creating-tables)
    - [Updating Tables](#updating-tables)
    - [테이블 수정하기](#updating-tables)
    - [Renaming / Dropping Tables](#renaming-and-dropping-tables)
    - [테이블의 이름변경 / 제거](#renaming-and-dropping-tables)
- [Columns](#columns)
    - [Creating Columns](#creating-columns)
    - [컬럼 생성하기](#creating-columns)
    - [Available Column Types](#available-column-types)
    - [사용가능한 컬럼의 타입들](#available-column-types)
    - [Column Modifiers](#column-modifiers)
    - [컬럼 수정자](#column-modifiers)
    - [Modifying Columns](#modifying-columns)
    - [컬럼 수정하기](#modifying-columns)
    - [Dropping Columns](#dropping-columns)
    - [컬럼 삭제하기](#dropping-columns)
- [Indexes](#indexes)
- [인덱스](#indexes)
    - [Creating Indexes](#creating-indexes)
    - [인덱스 생성하기](#creating-indexes)
    - [Renaming Indexes](#renaming-indexes)
    - [인덱스 이름 변경하기](#renaming-indexes)
    - [Dropping Indexes](#dropping-indexes)
    - [인덱스 없애기](#dropping-indexes)
    - [Foreign Key Constraints](#foreign-key-constraints)
    - [외래키 제약조건](#foreign-key-constraints)
- [Events](#events)
- [이벤트](#events)

<a name="introduction"></a>
## Introduction
## 시작하기

Migrations are like version control for your database, allowing your team to define and share the application's database schema definition. If you have ever had to tell a teammate to manually add a column to their local database schema after pulling in your changes from source control, you've faced the problem that database migrations solve.

마이그레이션은 데이터베이스의 버전 컨트롤과 같으므로 팀에서 애플리케이션의 데이터베이스 스키마 정의를 정의하고 공유할 수 있습니다. 소스 제어에서 변경 사항을 가져온 후 팀원에게 수동으로 로컬 데이터베이스 스키마에 컬럼을 추가하도록 지시해야 하는 경우 데이터베이스 마이그레이션이 해결하는 문제에 직면해 있는 것입니다.

The Laravel `Schema` [facade](/docs/{{version}}/facades) provides database agnostic support for creating and manipulating tables across all of Laravel's supported database systems. Typically, migrations will use this facade to create and modify database tables and columns.

라라벨의 `Schema` [파사드](/docs/{{version}}/facades)는 라라벨에서 지원하는 모든 데이터베이스 시스템에서 테이블을 생성하고 조작하기 위한 데이터베이스 광범위한 지원을 제공합니다. 일반적으로 마이그레이션은 이 파사드를 사용하여 데이터베이스 테이블과 컬럼을 만들고 수정합니다.

<a name="generating-migrations"></a>
## Generating Migrations
## 마이그레이션 파일 생성하기

You may use the `make:migration` [Artisan command](/docs/{{version}}/artisan) to generate a database migration. The new migration will be placed in your `database/migrations` directory. Each migration filename contains a timestamp that allows Laravel to determine the order of the migrations:

마이그레이션 파일을 생성하기 위해서는 `make:migration` [아티즌 명령어](/docs/{{version}}/artisan)를 사용합니다. 새로운 마이그레이션 파일은 `database/migrations` 디렉토리에 생성됩니다. 각 마이그레이션 파일 이름에는 Laravel이 마이그레이션 순서를 결정할 수 있는 타임스탬프가 포함되어 있습니다.

    php artisan make:migration create_flights_table

Laravel will use the name of the migration to attempt to guess the name of the table and whether or not the migration will be creating a new table. If Laravel is able to determine the table name from the migration name, Laravel will pre-fill the generated migration file with the specified table. Otherwise, you may simply specify the table in the migration file manually.

라라벨은 마이그레이션 이름을 사용하여 테이블 이름을 추측하고 마이그레이션이 새 테이블을 생성할지 여부를 추측합니다. 라라벨이 마이그레이션 이름에서 테이블 이름을 결정할 수 있다면 라라벨은 생성된 마이그레이션 파일을 지정된 테이블로 미리 채울 것입니다. 그렇지 않으면 마이그레이션 파일에 테이블을 수동으로 지정할 수 있습니다.

If you would like to specify a custom path for the generated migration, you may use the `--path` option when executing the `make:migration` command. The given path should be relative to your application's base path.

생성된 마이그레이션에 대한 사용자 지정 경로를 지정하려면 `make:migration` 명령을 실행할 때 `--path` 옵션을 사용할 수 있습니다. 주어진 경로는 애플리케이션의 기본 경로에 상대적이어야 합니다.

> {tip} Migration stubs may be customized using [stub publishing](/docs/{{version}}/artisan#stub-customization).

> {tip} [stub publishing](/docs/{{version}}/artisan#stub-customization)를 통해서 마이그레이션 stubs을 커스트마이징 할 수 있습니다.

<a name="squashing-migrations"></a>
### Squashing Migrations
### 스쿼싱 마이그레이션

As you build your application, you may accumulate more and more migrations over time. This can lead to your `database/migrations` directory becoming bloated with potentially hundreds of migrations. If you would like, you may "squash" your migrations into a single SQL file. To get started, execute the `schema:dump` command:

애플리케이션을 빌드하면서 시간이 지남에 따라 더 많은 마이그레이션이 누적될 수 있습니다. 이로 인해 `database/migrations` 디렉토리가 잠재적으로 수백 번의 마이그레이션으로 비대해질 수 있습니다. 원하는 경우 마이그레이션을 단일 SQL 파일로 "squash" 할 수 있습니다. 시작하려면 `schema:dump` 명령을 실행하세요:

    php artisan schema:dump

    // Dump the current database schema and prune all existing migrations...
    php artisan schema:dump --prune

When you execute this command, Laravel will write a "schema" file to your application's `database/schema` directory. Now, when you attempt to migrate your database and no other migrations have been executed, Laravel will execute the schema file's SQL statements first. After executing the schema file's statements, Laravel will execute any remaining migrations that were not part of the schema dump.

이 명령을 실행하면 라라벨은 `database/schema` 폴더에 "schema" 파일을 작성합니다. 이제 데이터베이스 마이그레이션을 시도하고 다른 마이그레이션이 실행되지 않은 경우 라라벨은 스키마 파일의 SQL 문을 먼저 실행합니다. 스키마 파일의 명령문을 실행한 후 라라벨은 스키마 덤프의 일부가 아닌 나머지 마이그레이션을 실행합니다.

You should commit your database schema file to source control so that other new developers on your team may quickly create your application's initial database structure.

여러분은 팀의 다른 새로운 개발자가 애플리케이션의 초기 데이터베이스 구조를 빠르게 만들 수 있도록 데이터베이스 스키마 파일을 소스 컨트롤에 커밋해야 합니다.

> {note} Migration squashing is only available for the MySQL, PostgreSQL, and SQLite databases and utilizes the database's command-line client. Schema dumps may not be restored to in-memory SQLite databases.

> {note} 마이그레이션 스쿼싱은 MySQL, PostgreSQL 및 SQLite 데이터베이스에서만 사용할 수 있으며 데이터베이스의 명령줄 클라이언트를 활용합니다. 스키마 덤프는 메모리 내 SQLite 데이터베이스로 복원되지 않을 수 있습니다.

<a name="migration-structure"></a>
## Migration Structure
## 마이그레이션의 구조

A migration class contains two methods: `up` and `down`. The `up` method is used to add new tables, columns, or indexes to your database, while the `down` method should reverse the operations performed by the `up` method.

마이그레이션 클래스에는 `up` 및 `down`의 두 가지 방법이 있습니다. `up` 메소드는 데이터베이스에 새 테이블, 열 또는 인덱스를 추가하는 데 사용되는 반면, `down` 방법은 `up` 방법이 수행하는 작업을 반대로 해야 합니다.

Within both of these methods, you may use the Laravel schema builder to expressively create and modify tables. To learn about all of the methods available on the `Schema` builder, [check out its documentation](#creating-tables). For example, the following migration creates a `flights` table:

이 두 메소드 모두에서 라라벨 스키마 빌더를 사용하여 테이블을 다양한 방법을 통해서 생성하고 변경할 수 있습니다. `Schema` 빌더에서 사용할 수 있는 모든 메소드에 대해 알아보려면 [문서](#creating-tables)를 확인하세요. 예를 들어 다음 마이그레이션은 `flights` 테이블을 생성합니다.

    <?php

    use Illuminate\Database\Migrations\Migration;
    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Support\Facades\Schema;

    class CreateFlightsTable extends Migration
    {
        /**
         * Run the migrations.
         *
         * @return void
         */
        public function up()
        {
            Schema::create('flights', function (Blueprint $table) {
                $table->id();
                $table->string('name');
                $table->string('airline');
                $table->timestamps();
            });
        }

        /**
         * Reverse the migrations.
         *
         * @return void
         */
        public function down()
        {
            Schema::drop('flights');
        }
    }

<a name="anonymous-migrations"></a>
#### Anonymous Migrations
#### 익명 마이그레이션

As you may have noticed in the example above, Laravel will automatically assign a class name to all of the migrations that you generate using the `make:migration` command. However, if you wish, you may return an anonymous class from your migration file. This is primarily useful if your application accumulates many migrations and two of them have a class name collision:

위의 예에서 알 수 있듯이 라라벨은 `make:migration` 명령을 사용하여 생성하는 모든 마이그레이션에 클래스 이름을 자동으로 할당합니다. 그러나 원하는 경우 마이그레이션 파일에서 익명 클래스를 반환할 수 있습니다. 이는 애플리케이션이 많은 마이그레이션을 쌓여있을때 그 중 두 개에 클래스 이름 충돌이 있는 경우에 주로 유용합니다.

    <?php

    use Illuminate\Database\Migrations\Migration;

    return new class extends Migration
    {
        //
    };

<a name="setting-the-migration-connection"></a>
#### Setting The Migration Connection
#### 마이그레이션 연결 설정

If your migration will be interacting with a database connection other than your application's default database connection, you should set the `$connection` property of your migration:

마이그레이션이 애플리케이션의 기본 데이터베이스 연결 이외의 데이터베이스 연결과 상호 작용하는 경우 마이그레이션의 `$connection` 속성을 설정해야 합니다.

    /**
     * The database connection that should be used by the migration.
     *
     * @var string
     */
    protected $connection = 'pgsql';

    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        //
    }

<a name="running-migrations"></a>
## Running Migrations
## 마이그레이션 실행하기

To run all of your outstanding migrations, execute the `migrate` Artisan command:

아직 실행된적이 없는 모든 마이그레이션을 실행하려면, `migrate` 아티즌 명령어를 실행하면 됩니다.

    php artisan migrate

If you would like to see which migrations have run thus far, you may use the `migrate:status` Artisan command:

지금까지 어떤 마이그레이션이 실행되었는지 확인하려면 `migrate:status` 아티즌 명령을 사용할 수 있습니다.

    php artisan migrate:status

<a name="forcing-migrations-to-run-in-production"></a>
#### Forcing Migrations To Run In Production
#### 실제 production 서버에서 강제로 마이그레이션 실행하기

Some migration operations are destructive, which means they may cause you to lose data. In order to protect you from running these commands against your production database, you will be prompted for confirmation before the commands are executed. To force the commands to run without a prompt, use the `--force` flag:

일부 마이그레이션 작업은 구조의 변경이 있을 수 있으며 데이터 손실이 발생할 수 있습니다. production 서버의 데이터베이스에 대해 이러한 명령을 실행하지 못하도록 보호하기 위해 명령을 실행하기 전에 확인 메시지가 표시됩니다. 확인 메시지 없이 명령을 강제 실행하려면 `--force` 플래그를 사용하세요:

    php artisan migrate --force

<a name="rolling-back-migrations"></a>
### Rolling Back Migrations
### 마이그레이션 되돌리기-롤백

To roll back the latest migration operation, you may use the `rollback` Artisan command. This command rolls back the last "batch" of migrations, which may include multiple migration files:

가장 최근의 마이그레이션 작업(operation)을 되돌리려면 `rollback` 명령어를 사용하면 됩니다. 이 명령어는 가장 최근에 실행된 마이그레이션의 "모음(batch)", 즉 여러개의 마이그레이션 파일에 대해서 되돌릴 수 있습니다.

    php artisan migrate:rollback

You may roll back a limited number of migrations by providing the `step` option to the `rollback` command. For example, the following command will roll back the last five migrations:

`rollback` 명령어에 `step` 옵션을 전달하여 제한된 숫자의 마이그레이션만 되돌릴 수 있습니다. 예를 들어 다음의 명령어는 최근 5개의 마이그레이션만 되돌립니다.

    php artisan migrate:rollback --step=5

The `migrate:reset` command will roll back all of your application's migrations:

`migrate:reset` 커맨드는 애플리케이션의 모든 마이그레이션을 되돌립니다.

    php artisan migrate:reset

<a name="roll-back-migrate-using-a-single-command"></a>
#### Roll Back & Migrate Using A Single Command
#### 하나의 명령어로 롤백과 마이그레이트 함께 실행 하기

The `migrate:refresh` command will roll back all of your migrations and then execute the `migrate` command. This command effectively re-creates your entire database:

`migrate:refresh` 명령어는 모든 마이그레이션을 되돌리고, `migrate` 명령어를 실행합니다. 이 명령어는 전체 데이터베이스를 효과적으로 재생성합니다.

    php artisan migrate:refresh

    // Refresh the database and run all database seeds...
    php artisan migrate:refresh --seed

You may roll back and re-migrate a limited number of migrations by providing the `step` option to the `refresh` command. For example, the following command will roll back and re-migrate the last five migrations:

`refresh` 명령어에 `step` 옵션을 전달하여 제한된 숫자의 마이그레이션만 되돌리고 다시 마이그레이션을 수행할 수 있습니다. 예를들어 다음의 명령어는 최근 5개의 마이그레이션만 되돌리고 마이그레이션을 다시 실행합니다.

    php artisan migrate:refresh --step=5

<a name="drop-all-tables-migrate"></a>
#### Drop All Tables & Migrate
#### 모든 테이블과 마이그레이션 Drop

The `migrate:fresh` command will drop all tables from the database and then execute the `migrate` command:

`migrate:fresh` 명령어는 `migrate` 명령이 실행될 때 데이터베이스의 모든 테이블을 drop 합니다.

    php artisan migrate:fresh

    php artisan migrate:fresh --seed

> {note} The `migrate:fresh` command will drop all database tables regardless of their prefix. This command should be used with caution when developing on a database that is shared with other applications.

> {note} `migrate:fresh` 명령은 접두어에 관계없이 모든 데이터베이스 테이블을 삭제합니다. 이 명령은 다른 응용 프로그램과 공유되는 데이터베이스에서 개발할 때 주의해서 사용해야 합니다.

<a name="tables"></a>
## Tables
## 테이블

<a name="creating-tables"></a>
### Creating Tables
### 테이블 생성하기

To create a new database table, use the `create` method on the `Schema` facade. The `create` method accepts two arguments: the first is the name of the table, while the second is a closure which receives a `Blueprint` object that may be used to define the new table:

새로운 데이터베이스 테이블을 생성하려면 `Schema` 파사드에 `create` 메소드를 사용하면 됩니다. `create` 메소드는 두개의 인자를 전달 받습니다. 첫번째 인자는 테이블의 이름이고, 두번째 인자는 새로운 테이블을 정의하는 사용되는 `Blueprint` 객체를 받는 `Closure`입니다.

    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Support\Facades\Schema;

    Schema::create('users', function (Blueprint $table) {
        $table->id();
        $table->string('name');
        $table->string('email');
        $table->timestamps();
    });

When creating the table, you may use any of the schema builder's [column methods](#creating-columns) to define the table's columns.

테이블을 생성할 때, 테이블의 컬럼을 정의하기 위하여 자유롭게 스키마 빌더의 [컬럼 메소드](#creating-columns)를 사용할 수 있습니다.

<a name="checking-for-table-column-existence"></a>
#### Checking For Table / Column Existence
#### 테이블 / 컬럼이 존재하는지 확인하기

You may check for the existence of a table or column using the `hasTable` and `hasColumn` methods:

`hasTable`와 `hasColumn` 메소드를 사용해 테이블이나 컬럼의 존재 유무를 확인할 수 있습니다.

    if (Schema::hasTable('users')) {
        // The "users" table exists...
    }

    if (Schema::hasColumn('users', 'email')) {
        // The "users" table exists and has an "email" column...
    }

<a name="database-connection-table-options"></a>
#### Database Connection & Table Options
#### 데이터베이스 커넥션-connection & 테이블 옵션

If you want to perform a schema operation on a database connection that is not your application's default connection, use the `connection` method:

기본 커넥션-connection이 아닌 다른 데이터베이스 커넥션-connection에 스키마 작업을 수행하려면 `connection` 메소드를 사용하면 됩니다.

    Schema::connection('sqlite')->create('users', function (Blueprint $table) {
        $table->id();
    });

In addition, a few other properties and methods may be used to define other aspects of the table's creation. The `engine` property may be used to specify the table's storage engine when using MySQL:

또한 몇 가지 다른 속성과 메소드를 사용하여 테이블 생성의 다른 측면을 정의할 수 있습니다. `engine` 속성은 MySQL을 사용할 때 테이블의 스토리지 엔진을 지정하는 데 사용할 수 있습니다.

    Schema::create('users', function (Blueprint $table) {
        $table->engine = 'InnoDB';

        // ...
    });

The `charset` and `collation` properties may be used to specify the character set and collation for the created table when using MySQL:

`charset` 및 `collation` 속성은 MySQL을 사용할 때 생성된 테이블에 대한 문자 집합 및 데이터 정렬을 지정하는 데 사용할 수 있습니다.

    Schema::create('users', function (Blueprint $table) {
        $table->charset = 'utf8mb4';
        $table->collation = 'utf8mb4_unicode_ci';

        // ...
    });

The `temporary` method may be used to indicate that the table should be "temporary". Temporary tables are only visible to the current connection's database session and are dropped automatically when the connection is closed:

`temporary` 메소드는 테이블이 "temporary"이여야 함을 나타내는 데 사용할 수 있습니다. 임시 테이블은 현재 연결의 데이터베이스 세션에서만 볼 수 있으며 연결이 닫힐 때 자동으로 삭제됩니다.

    Schema::create('calculations', function (Blueprint $table) {
        $table->temporary();

        // ...
    });

<a name="updating-tables"></a>
### Updating Tables
### 테이블 수정하기

The `table` method on the `Schem-a` facade may be used to update existing tables. Like the `create` method, the `table` method accepts two arguments: the name of the table and a closure that receives a `Blueprint` instance you may use to add columns or indexes to the table:

`Schema` 파사드의 `table` 메소드는 기존 테이블을 수정하는 데 사용할 수 있습니다. `create` 메소드와 마찬가지로 `table` 메소드는 테이블 이름과 테이블에 컬럼이나 인덱스를 추가하는 데 사용할 수 있는 `Blueprint` 인스턴스를 받는 클로저의 두 가지 인수를 허용합니다.

    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Support\Facades\Schema;

    Schema::table('users', function (Blueprint $table) {
        $table->integer('votes');
    });

<a name="renaming-and-dropping-tables"></a>
### Renaming / Dropping Tables
### 테이블 이름 변경 / 제거하기

To rename an existing database table, use the `rename` method:

이미 존재하는 테이블의 이름을 바꾸려면 `rename` 메소드를 이용하십시오:

    use Illuminate\Support\Facades\Schema;

    Schema::rename($from, $to);

To drop an existing table, you may use the `drop` or `dropIfExists` methods:

이미 존재하는 테이블을 제거하려면 `drop`이나 `dropIfExists` 메소드를 사용하세요:

    Schema::drop('users');

    Schema::dropIfExists('users');

<a name="renaming-tables-with-foreign-keys"></a>
#### Renaming Tables With Foreign Keys
#### 외래 키를 가진 테이블의 이름 변경

Before renaming a table, you should verify that any foreign key constraints on the table have an explicit name in your migration files instead of letting Laravel assign a convention based name. Otherwise, the foreign key constraint name will refer to the old table name.

테이블의 이름 변경하기 전에, 마이그레이션 파일에서 라라벨의 규약(Convention)에 따르는 기본 이름으로 지정되지 않고 고유한 이름을 붙인 외래 키 제약 조건이 존재하지 않는지 확인하십시오. 그렇지 않다면 외래 키 제약의 이름이 기존 테이블 이름을 참고하게 됩니다.

<a name="columns"></a>
## Columns
## 컬럼

<a name="creating-columns"></a>
### Creating Columns
### 컬럼 생성하기

The `table` method on the `Schema` facade may be used to update existing tables. Like the `create` method, the `table` method accepts two arguments: the name of the table and a closure that receives an `Illuminate\Database\Schema\Blueprint` instance you may use to add columns to the table:

이미 존재하는 테이블을 변경하기 하는데, `Schema` 파사드의 `table` 메소드를 사용할 수 있습니다. `create` 메소드와 같이 `table` 메소드도 두개의 인자를 전달 받습니다. 하나는 테이블의 이름이고, 다른 하나는 테이블에 컬럼을 추가하는데 사용할 수 있는 `Blueprint` 인스턴스를 받는 `Closure`입니다.

    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Support\Facades\Schema;

    Schema::table('users', function (Blueprint $table) {
        $table->integer('votes');
    });

<a name="available-column-types"></a>
### Available Column Types
### 사용가능한 컬럼의 타입들

The schema builder blueprint offers a variety of methods that correspond to the different types of columns you can add to your database tables. Each of the available methods are listed in the table below:

스키마 빌더 청사진(blueprint)은 데이터베이스 테이블에 추가할 수 있는 다양한 유형의 컬럼에 해당하는 다양한 방법을 제공합니다. 사용 가능한 각 방법은 아래 표에 나열되어 있습니다.

<style>
    #collection-method-list > p {
        column-count: 3; -moz-column-count: 3; -webkit-column-count: 3;
        column-gap: 2em; -moz-column-gap: 2em; -webkit-column-gap: 2em;
    }

    #collection-method-list a {
        display: block;
    }

    .collection-method code {
        font-size: 14px;
    }

    .collection-method:not(.first-collection-method) {
        margin-top: 50px;
    }
</style>

<div id="collection-method-list" markdown="1">

[bigIncrements](#column-method-bigIncrements)
[bigInteger](#column-method-bigInteger)
[binary](#column-method-binary)
[boolean](#column-method-boolean)
[char](#column-method-char)
[dateTimeTz](#column-method-dateTimeTz)
[dateTime](#column-method-dateTime)
[date](#column-method-date)
[decimal](#column-method-decimal)
[double](#column-method-double)
[enum](#column-method-enum)
[float](#column-method-float)
[foreignId](#column-method-foreignId)
[foreignIdFor](#column-method-foreignIdFor)
[foreignUuid](#column-method-foreignUuid)
[geometryCollection](#column-method-geometryCollection)
[geometry](#column-method-geometry)
[id](#column-method-id)
[increments](#column-method-increments)
[integer](#column-method-integer)
[ipAddress](#column-method-ipAddress)
[json](#column-method-json)
[jsonb](#column-method-jsonb)
[lineString](#column-method-lineString)
[longText](#column-method-longText)
[macAddress](#column-method-macAddress)
[mediumIncrements](#column-method-mediumIncrements)
[mediumInteger](#column-method-mediumInteger)
[mediumText](#column-method-mediumText)
[morphs](#column-method-morphs)
[multiLineString](#column-method-multiLineString)
[multiPoint](#column-method-multiPoint)
[multiPolygon](#column-method-multiPolygon)
[nullableMorphs](#column-method-nullableMorphs)
[nullableTimestamps](#column-method-nullableTimestamps)
[nullableUuidMorphs](#column-method-nullableUuidMorphs)
[point](#column-method-point)
[polygon](#column-method-polygon)
[rememberToken](#column-method-rememberToken)
[set](#column-method-set)
[smallIncrements](#column-method-smallIncrements)
[smallInteger](#column-method-smallInteger)
[softDeletesTz](#column-method-softDeletesTz)
[softDeletes](#column-method-softDeletes)
[string](#column-method-string)
[text](#column-method-text)
[timeTz](#column-method-timeTz)
[time](#column-method-time)
[timestampTz](#column-method-timestampTz)
[timestamp](#column-method-timestamp)
[timestampsTz](#column-method-timestampsTz)
[timestamps](#column-method-timestamps)
[tinyIncrements](#column-method-tinyIncrements)
[tinyInteger](#column-method-tinyInteger)
[tinyText](#column-method-tinyText)
[unsignedBigInteger](#column-method-unsignedBigInteger)
[unsignedDecimal](#column-method-unsignedDecimal)
[unsignedInteger](#column-method-unsignedInteger)
[unsignedMediumInteger](#column-method-unsignedMediumInteger)
[unsignedSmallInteger](#column-method-unsignedSmallInteger)
[unsignedTinyInteger](#column-method-unsignedTinyInteger)
[uuidMorphs](#column-method-uuidMorphs)
[uuid](#column-method-uuid)
[year](#column-method-year)

</div>

<a name="column-method-bigIncrements"></a>
#### `bigIncrements()` {.collection-method .first-collection-method}

The `bigIncrements` method creates an auto-incrementing `UNSIGNED BIGINT` (primary key) equivalent column:

`bigIncrements` 메소드는 자동 증가하는 `UNSIGNED BIGINT`(기본 키)에 해당하는 컬럼을 생성합니다.

    $table->bigIncrements('id');

<a name="column-method-bigInteger"></a>
#### `bigInteger()` {.collection-method}

The `bigInteger` method creates a `BIGINT` equivalent column:

`bigInteger` 메소드는 `BIGINT`에 해당하는 컬럼을 생성합니다.

    $table->bigInteger('votes');

<a name="column-method-binary"></a>
#### `binary()` {.collection-method}

The `binary` method creates a `BLOB` equivalent column:

`binary` 메소드는 `BLOB`에 해당하는 컬럼을 생성합니다.

    $table->binary('photo');

<a name="column-method-boolean"></a>
#### `boolean()` {.collection-method}

The `boolean` method creates a `BOOLEAN` equivalent column:

`boolean` 메소드는 `BOOLEAN`에 해당하는 컬럼을 생성합니다.

    $table->boolean('confirmed');

<a name="column-method-char"></a>
#### `char()` {.collection-method}

The `char` method creates a `CHAR` equivalent column with of a given length:

`char` 메소드는 주어진 길이의 `CHAR`에 해당하는 컬럼을 생성합니다.

    $table->char('name', 100);

<a name="column-method-dateTimeTz"></a>
#### `dateTimeTz()` {.collection-method}

The `dateTimeTz` method creates a `DATETIME` (with timezone) equivalent column with an optional precision (total digits):

`dateTimeTz` 메소드는 선택적 정밀도(총 자릿수)를 사용하여 `DATETIME`(시간대 포함)에 해당하는 컬럼을 생성합니다.

    $table->dateTimeTz('created_at', $precision = 0);

<a name="column-method-dateTime"></a>
#### `dateTime()` {.collection-method}

The `dateTime` method creates a `DATETIME` equivalent column with an optional precision (total digits):

`dateTime` 메소드는 선택적 정밀도(총 자릿수)를 사용하여 `DATETIME`에 해당하는 컬럼을 생성합니다.

    $table->dateTime('created_at', $precision = 0);

<a name="column-method-date"></a>
#### `date()` {.collection-method}

The `date` method creates a `DATE` equivalent column:

`date` 메소드는 `DATE`에 해당하는 컬럼을 생성합니다.

    $table->date('created_at');

<a name="column-method-decimal"></a>
#### `decimal()` {.collection-method}

The `decimal` method creates a `DECIMAL` equivalent column with the given precision (total digits) and scale (decimal digits):

`decimal` 메소드는 주어진 정밀도(총 자릿수) 및 스케일(십진 자릿수)을 사용하여 `DECIMAL`에 해당하는 컬럼을 생성합니다.

    $table->decimal('amount', $precision = 8, $scale = 2);

<a name="column-method-double"></a>
#### `double()` {.collection-method}

The `double` method creates a `DOUBLE` equivalent column with the given precision (total digits) and scale (decimal digits):

`double` 메소드는 주어진 정밀도(총 자릿수)와 스케일(십진수)을 사용하여 `DOUBLE`에 해당하는 컬럼을 생성합니다.

    $table->double('amount', 8, 2);

<a name="column-method-enum"></a>
#### `enum()` {.collection-method}

The `enum` method creates a `ENUM` equivalent column with the given valid values:

`enum` 메소드는 주어진 유효한 값으로 `ENUM`에 해당하는 컬럼을 생성합니다.

    $table->enum('difficulty', ['easy', 'hard']);

<a name="column-method-float"></a>
#### `float()` {.collection-method}

The `float` method creates a `FLOAT` equivalent column with the given precision (total digits) and scale (decimal digits):

`float` 메소드는 주어진 정밀도(총 자릿수)와 스케일(십진수)로 `FLOAT`에 해당하는 컬럼을 생성합니다.

    $table->float('amount', 8, 2);

<a name="column-method-foreignId"></a>
#### `foreignId()` {.collection-method}

The `foreignId` method creates an `UNSIGNED BIGINT` equivalent column:

`foreignId` 메소드는 `UNSIGNED BIGINT`에 해당하는 컬럼을 생성합니다.

    $table->foreignId('user_id');

<a name="column-method-foreignIdFor"></a>
#### `foreignIdFor()` {.collection-method}

The `foreignIdFor` method adds a `{column}_id UNSIGNED BIGINT` equivalent column for a given model class:

`foreignIdFor` 메소드는 주어진 모델 클래스에 대해 `{column}_id UNSIGNED BIGINT`에 해당하는 컬럼을 추가합니다.

    $table->foreignIdFor(User::class);

<a name="column-method-foreignUuid"></a>
#### `foreignUuid()` {.collection-method}

The `foreignUuid` method creates a `UUID` equivalent column:

`foreignUuid` 메소드는 `UUID`에 해당하는 컬럼을 생성합니다.

    $table->foreignUuid('user_id');

<a name="column-method-geometryCollection"></a>
#### `geometryCollection()` {.collection-method}

The `geometryCollection` method creates a `GEOMETRYCOLLECTION` equivalent column:

`geometryCollection` 메소드는 `GEOMETRYCOLLECTION`에 해당하는 컬럼을 생성합니다.

    $table->geometryCollection('positions');

<a name="column-method-geometry"></a>
#### `geometry()` {.collection-method}

The `geometry` method creates a `GEOMETRY` equivalent column:

`geometry` 메소드는 `GEOMETRY`에 해당하는 컬럼을 생성합니다.

    $table->geometry('positions');

<a name="column-method-id"></a>
#### `id()` {.collection-method}

The `id` method is an alias of the `bigIncrements` method. By default, the method will create an `id` column; however, you may pass a column name if you would like to assign a different name to the column:

`id` 메소드는 `bigIncrements` 메소드의 별칭입니다. 기본적으로 이 메소드는 `id` 컬럼을 생성합니다. 그러나 컬럼에 다른 이름을 지정하려면 컬럼 이름을 전달할 수 있습니다.

    $table->id();

<a name="column-method-increments"></a>
#### `increments()` {.collection-method}

The `increments` method creates an auto-incrementing `UNSIGNED INTEGER` equivalent column as a primary key:

`increments` 메소드는 기본 키로 자동 증가하는 `UNSIGNED INTEGER`에 해당하는 컬럼을 생성합니다.

    $table->increments('id');

<a name="column-method-integer"></a>
#### `integer()` {.collection-method}

The `integer` method creates an `INTEGER` equivalent column:

`integer` 메소드는 `INTEGER`에 해당하는 컬럼을 생성합니다.

    $table->integer('votes');

<a name="column-method-ipAddress"></a>
#### `ipAddress()` {.collection-method}

The `ipAddress` method creates a `VARCHAR` equivalent column:

`ipAddress` 메소드는 `VARCHAR`에 해당하는 컬럼을 생성합니다.

    $table->ipAddress('visitor');

<a name="column-method-json"></a>
#### `json()` {.collection-method}

The `json` method creates a `JSON` equivalent column:

`json` 메소드는 `JSON`에 해당하는 컬럼을 생성합니다.

    $table->json('options');

<a name="column-method-jsonb"></a>
#### `jsonb()` {.collection-method}

The `jsonb` method creates a `JSONB` equivalent column:

`jsonb` 메소드는 `JSONB`에 해당하는 컬럼을 생성합니다.

    $table->jsonb('options');

<a name="column-method-lineString"></a>
#### `lineString()` {.collection-method}

The `lineString` method creates a `LINESTRING` equivalent column:

`lineString` 메소드는 `LINESTRING`에 해당하는 컬럼을 생성합니다.

    $table->lineString('positions');

<a name="column-method-longText"></a>
#### `longText()` {.collection-method}

The `longText` method creates a `LONGTEXT` equivalent column:

`longText` 메소드는 `LONGTEXT`에 해당하는 컬럼을 생성합니다.

    $table->longText('description');

<a name="column-method-macAddress"></a>
#### `macAddress()` {.collection-method}

The `macAddress` method creates a column that is intended to hold a MAC address. Some database systems, such as PostgreSQL, have a dedicated column type for this type of data. Other database systems will use a string equivalent column:

`macAddress` 메소드는 맥어드레스를 담기 위한 컬럼을 생성합니다. PostgreSQL과 같은 일부 데이터베이스 시스템에는 이러한 유형의 데이터에 대한 전용 컬럼 유형이 있습니다. 다른 데이터베이스 시스템은 문자열에 해당하는 컬럼을 사용합니다.

    $table->macAddress('device');

<a name="column-method-mediumIncrements"></a>
#### `mediumIncrements()` {.collection-method}

The `mediumIncrements` method creates an auto-incrementing `UNSIGNED MEDIUMINT` equivalent column as a primary key:

`mediumIncrements` 메소드는 기본 키로 자동 증가하는 `UNSIGNED MEDIUMINT`에 해당하는 컬럼을 생성합니다.

    $table->mediumIncrements('id');

<a name="column-method-mediumInteger"></a>
#### `mediumInteger()` {.collection-method}

The `mediumInteger` method creates a `MEDIUMINT` equivalent column:

`mediumInteger` 메소드는 `MEDIUMINT`에 해당하는 컬럼을 생성합니다.

    $table->mediumInteger('votes');

<a name="column-method-mediumText"></a>
#### `mediumText()` {.collection-method}

The `mediumText` method creates a `MEDIUMTEXT` equivalent column:

`mediumText` 메소드는 `MEDIUMTEXT`에 해당하는 컬럼을 생성합니다.

    $table->mediumText('description');

<a name="column-method-morphs"></a>
#### `morphs()` {.collection-method}

The `morphs` method is a convenience method that adds a `{column}_id` `UNSIGNED BIGINT` equivalent column and a `{column}_type` `VARCHAR` equivalent column.

`morphs` 메소드는 `{column}_id` `UNSIGNED BIGINT`에 해당하는 컬럼과 `{column}_type` `VARCHAR`에 해당하는 컬럼을 추가하는 편리한 메소드입니다.

This method is intended to be used when defining the columns necessary for a polymorphic [Eloquent relationship](/docs/{{version}}/eloquent-relationships). In the following example, `taggable_id` and `taggable_type` columns would be created:

이 방법은 다형성 [Relationships - 관계](/docs/{{version}}/eloquent-relationships)에 필요한 컬럼을 정의할 때 사용하기 위한 것입니다. 다음 예에서는 `taggable_id` 및 `taggable_type` 컬럼이 생성됩니다.

    $table->morphs('taggable');

<a name="column-method-multiLineString"></a>
#### `multiLineString()` {.collection-method}

The `multiLineString` method creates a `MULTILINESTRING` equivalent column:

`multiLineString` 메소드는 `MULTILINESTRING`에 해당하는 컬럼을 생성합니다.

    $table->multiLineString('positions');

<a name="column-method-multiPoint"></a>
#### `multiPoint()` {.collection-method}

The `multiPoint` method creates a `MULTIPOINT` equivalent column:

`multiPoint` 메소드는 `MULTIPOINT`에 해당하는 컬럼을 생성합니다.

    $table->multiPoint('positions');

<a name="column-method-multiPolygon"></a>
#### `multiPolygon()` {.collection-method}

The `multiPolygon` method creates a `MULTIPOLYGON` equivalent column:

`multiPolygon` 메소드는 `MULTIPOLYGON`에 해당하는 컬럼을 생성합니다.

    $table->multiPolygon('positions');

<a name="column-method-nullableTimestamps"></a>
#### `nullableTimestamps()` {.collection-method}

The `nullableTimestamps` method is an alias of the [timestamps](#column-method-timestamps) method:

`nullableTimestamps` 메소드는 [timestamps](#column-method-timestamps) 메소드의 별칭입니다.

    $table->nullableTimestamps(0);

<a name="column-method-nullableMorphs"></a>
#### `nullableMorphs()` {.collection-method}

The method is similar to the [morphs](#column-method-morphs) method; however, the columns that are created will be "nullable":

이 방법은 [morphs](#column-method-morphs) 방법과 유사합니다. 그러나 생성되는 컬럼은 "nullable"이 됩니다.

    $table->nullableMorphs('taggable');

<a name="column-method-nullableUuidMorphs"></a>
#### `nullableUuidMorphs()` {.collection-method}

The method is similar to the [uuidMorphs](#column-method-uuidMorphs) method; however, the columns that are created will be "nullable":

이 방법은 [uuidMorphs](#column-method-uuidMorphs) 방법과 유사합니다. 그러나 생성되는 컬럼은 "nullable"이 됩니다.

    $table->nullableUuidMorphs('taggable');

<a name="column-method-point"></a>
#### `point()` {.collection-method}

The `point` method creates a `POINT` equivalent column:

`point` 메소드는 `POINT`에 해당하는 컬럼을 생성합니다.

    $table->point('position');

<a name="column-method-polygon"></a>
#### `polygon()` {.collection-method}

The `polygon` method creates a `POLYGON` equivalent column:

`polygon` 메소드는 `POLYGON`에 해당하는 컬럼을 생성합니다.

    $table->polygon('position');

<a name="column-method-rememberToken"></a>
#### `rememberToken()` {.collection-method}

The `rememberToken` method creates a nullable, `VARCHAR(100)` equivalent column that is intended to store the current "remember me" [authentication token](/docs/{{version}}/authentication#remembering-users):

`rememberToken` 메소드는 현재 "remember me"[authentication token](/docs/{{version}}/authentication#remembering-users)를 저장하기 위한 null 허용 `VARCHAR(100)` 컬럼을 생성합니다.

    $table->rememberToken();

<a name="column-method-set"></a>
#### `set()` {.collection-method}

The `set` method creates a `SET` equivalent column with the given list of valid values:

`set` 메소드는 유효한 값의 주어진 목록으로 `SET`에 해당하는 컬럼을 생성합니다.

    $table->set('flavors', ['strawberry', 'vanilla']);

<a name="column-method-smallIncrements"></a>
#### `smallIncrements()` {.collection-method}

The `smallIncrements` method creates an auto-incrementing `UNSIGNED SMALLINT` equivalent column as a primary key:

`smallIncrements` 메소드는 기본 키로 자동 증가하는 `UNSIGNED SMALLINT`에 해당하는 컬럼을 생성합니다.

    $table->smallIncrements('id');

<a name="column-method-smallInteger"></a>
#### `smallInteger()` {.collection-method}

The `smallInteger` method creates a `SMALLINT` equivalent column:

`smallInteger` 메소드는 `SMALLINT`에 해당하는 컬럼을 생성합니다.

    $table->smallInteger('votes');

<a name="column-method-softDeletesTz"></a>
#### `softDeletesTz()` {.collection-method}

The `softDeletesTz` method adds a nullable `deleted_at` `TIMESTAMP` (with timezone) equivalent column with an optional precision (total digits). This column is intended to store the `deleted_at` timestamp needed for Eloquent's "soft delete" functionality:

`softDeletesTz` 메소드는 선택적 정밀도(총 자릿수)가 있는 nullable `deleted_at` `TIMESTAMP`(시간대 포함) 해당 컬럼을 추가합니다. 이 컬럼은 Eloquent의 "soft delete" 기능에 필요한 `deleted_at` 타임스탬프를 저장하기 위한 것입니다.

    $table->softDeletesTz($column = 'deleted_at', $precision = 0);

<a name="column-method-softDeletes"></a>
#### `softDeletes()` {.collection-method}

The `softDeletes` method adds a nullable `deleted_at` `TIMESTAMP` equivalent column with an optional precision (total digits). This column is intended to store the `deleted_at` timestamp needed for Eloquent's "soft delete" functionality:

`softDeletes` 메소드는 선택적 정밀도(총 자릿수)를 사용하여 nullable `deleted_at` `TIMESTAMP`에 해당하는 컬럼을 추가합니다. 이 컬럼은 Eloquent의 "soft delete" 기능에 필요한 `deleted_at` 타임스탬프를 저장하기 위한 것입니다.


    $table->softDeletes($column = 'deleted_at', $precision = 0);

<a name="column-method-string"></a>
#### `string()` {.collection-method}

The `string` method creates a `VARCHAR` equivalent column of the given length:

`string` 메소드는 주어진 길이의 `VARCHAR`에 해당하는 컬럼을 생성합니다.

    $table->string('name', 100);

<a name="column-method-text"></a>
#### `text()` {.collection-method}

The `text` method creates a `TEXT` equivalent column:

`text` 메소드는 `TEXT`에 해당하는 컬럼을 생성합니다.

    $table->text('description');

<a name="column-method-timeTz"></a>
#### `timeTz()` {.collection-method}

The `timeTz` method creates a `TIME` (with timezone) equivalent column with an optional precision (total digits):

`timeTz` 메소드는 선택적 정밀도(총 자릿수)를 사용하여 `TIME`(시간대 포함)에 해당하는 컬럼을 생성합니다.

    $table->timeTz('sunrise', $precision = 0);

<a name="column-method-time"></a>
#### `time()` {.collection-method}

The `time` method creates a `TIME` equivalent column with an optional precision (total digits):

`time` 메소드는 선택적 정밀도(총 자릿수)를 사용하여 `TIME`에 해당하는 컬럼을 생성합니다.

    $table->time('sunrise', $precision = 0);

<a name="column-method-timestampTz"></a>
#### `timestampTz()` {.collection-method}

The `timestampTz` method creates a `TIMESTAMP` (with timezone) equivalent column with an optional precision (total digits):

`timestampTz` 메소드는 선택적 정밀도(총 자릿수)를 사용하여 `TIMESTAMP`(시간대 포함)에 해당하는 컬럼을 생성합니다.

    $table->timestampTz('added_at', $precision = 0);

<a name="column-method-timestamp"></a>
#### `timestamp()` {.collection-method}

The `timestamp` method creates a `TIMESTAMP` equivalent column with an optional precision (total digits):

`timestamp` 메소드는 선택적 정밀도(총 자릿수)를 사용하여 `TIMESTAMP`에 해당하는 컬럼을 생성합니다.

    $table->timestamp('added_at', $precision = 0);

<a name="column-method-timestampsTz"></a>
#### `timestampsTz()` {.collection-method}

The `timestampsTz` method creates `created_at` and `updated_at` `TIMESTAMP` (with timezone) equivalent columns with an optional precision (total digits):

`timestampsTz` 메소드는 선택적 정밀도(총 자릿수)를 사용하여 `created_at` 및 `updated_at` `TIMESTAMP`(시간대 포함)에 해당하는 컬럼을 생성합니다.

    $table->timestampsTz($precision = 0);

<a name="column-method-timestamps"></a>
#### `timestamps()` {.collection-method}

The `timestamps` method creates `created_at` and `updated_at` `TIMESTAMP` equivalent columns with an optional precision (total digits):

`timestamps` 메소드는 선택적 정밀도(총 자릿수)를 사용하여 `created_at` 및 `updated_at` `TIMESTAMP`에 해당하는 컬럼을 생성합니다.

    $table->timestamps($precision = 0);

<a name="column-method-tinyIncrements"></a>
#### `tinyIncrements()` {.collection-method}

The `tinyIncrements` method creates an auto-incrementing `UNSIGNED TINYINT` equivalent column as a primary key:

`tinyIncrements` 메소드는 기본 키로 자동 증가하는 `UNSIGNED TINYINT`에 해당하는 컬럼을 생성합니다.

    $table->tinyIncrements('id');

<a name="column-method-tinyInteger"></a>
#### `tinyInteger()` {.collection-method}

The `tinyInteger` method creates a `TINYINT` equivalent column:

`tinyInteger` 메소드는 `TINYINT`에 해당하는 컬럼을 생성합니다.

    $table->tinyInteger('votes');

<a name="column-method-tinyText"></a>
#### `tinyText()` {.collection-method}

The `tinyText` method creates a `TINYTEXT` equivalent column:

`tinyText` 메소드는 `TINYTEXT`에 해당하는 컬럼을 생성합니다.

    $table->tinyText('notes');

<a name="column-method-unsignedBigInteger"></a>
#### `unsignedBigInteger()` {.collection-method}

The `unsignedBigInteger` method creates an `UNSIGNED BIGINT` equivalent column:

    $table->unsignedBigInteger('votes');

<a name="column-method-unsignedDecimal"></a>
#### `unsignedDecimal()` {.collection-method}

The `unsignedDecimal` method creates an `UNSIGNED DECIMAL` equivalent column with an optional precision (total digits) and scale (decimal digits):

    $table->unsignedDecimal('amount', $precision = 8, $scale = 2);

<a name="column-method-unsignedInteger"></a>
#### `unsignedInteger()` {.collection-method}

The `unsignedInteger` method creates an `UNSIGNED INTEGER` equivalent column:

`unsignedInteger` 메소드는 `UNSIGNED INTEGER`에 해당하는 컬럼을 생성합니다.

    $table->unsignedInteger('votes');

<a name="column-method-unsignedMediumInteger"></a>
#### `unsignedMediumInteger()` {.collection-method}

The `unsignedMediumInteger` method creates an `UNSIGNED MEDIUMINT` equivalent column:

`unsignedMediumInteger` 메소드는 `UNSIGNED MEDIUMINT`에 해당하는 컬럼을 생성합니다.

    $table->unsignedMediumInteger('votes');

<a name="column-method-unsignedSmallInteger"></a>
#### `unsignedSmallInteger()` {.collection-method}

The `unsignedSmallInteger` method creates an `UNSIGNED SMALLINT` equivalent column:

`unsignedSmallInteger` 메소드는 `UNSIGNED SMALLINT`에 해당하는 컬럼을 생성합니다.

    $table->unsignedSmallInteger('votes');

<a name="column-method-unsignedTinyInteger"></a>
#### `unsignedTinyInteger()` {.collection-method}

The `unsignedTinyInteger` method creates an `UNSIGNED TINYINT` equivalent column:

`unsignedTinyInteger` 메소드는 `UNSIGNED TINYINT`에 해당하는 컬럼을 생성합니다.

    $table->unsignedTinyInteger('votes');

<a name="column-method-uuidMorphs"></a>
#### `uuidMorphs()` {.collection-method}

The `uuidMorphs` method is a convenience method that adds a `{column}_id` `CHAR(36)` equivalent column and a `{column}_type` `VARCHAR` equivalent column.

`uuidMorphs` 메소드는 `{column}_id` `CHAR(36)`에 해당하는 컬럼과 `{column}_type` `VARCHAR`에 해당하는 컬럼을 추가하는 편리한 메소드입니다.

This method is intended to be used when defining the columns necessary for a polymorphic [Eloquent relationship](/docs/{{version}}/eloquent-relationships) that use UUID identifiers. In the following example, `taggable_id` and `taggable_type` columns would be created:

이 방법은 UUID 식별자를 사용하는 다형성 [Relationships - 관계](/docs/{{version}}/eloquent-relationships)에 필요한 컬럼을 정의할 때 사용하기 위한 것입니다. 다음 예에서는 `taggable_id` 및 `taggable_type` 컬럼이 생성됩니다.

    $table->uuidMorphs('taggable');

<a name="column-method-uuid"></a>
#### `uuid()` {.collection-method}

The `uuid` method creates a `UUID` equivalent column:

`uuid` 메소드는 `UUID`에 해당하는 컬럼을 생성합니다.

    $table->uuid('id');

<a name="column-method-year"></a>
#### `year()` {.collection-method}

The `year` method creates a `YEAR` equivalent column:

`year` 메소드는 `YEAR`에 해당하는 컬럼을 생성합니다.

    $table->year('birth_year');

<a name="column-modifiers"></a>
### Column Modifiers

In addition to the column types listed above, there are several column "modifiers" you may use when adding a column to a database table. For example, to make the column "nullable", you may use the `nullable` method:

위에 나열된 컬럼 유형 외에도 데이터베이스 테이블에 컬럼을 추가할 때 사용할 수 있는 여러 컬럼 "수정자-modifier"가 있습니다. 예를 들어 컬럼을 "nullable"로 만들려면 `nullable` 메서드를 사용할 수 있습니다.

    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Support\Facades\Schema;

    Schema::table('users', function (Blueprint $table) {
        $table->string('email')->nullable();
    });

The following table contains all of the available column modifiers. This list does not include [index modifiers](#creating-indexes):

다음 표에는 사용 가능한 모든 컬럼 수정자가 포함되어 있습니다. 이 목록에는 [인덱스 생성하기](#creating-indexes)가 포함되어 있지 않습니다.

Modifier  |  Description
--------  |  -----------
`->after('column')`  |  Place the column "after" another column (MySQL).
`->autoIncrement()`  |  Set INTEGER columns as auto-incrementing (primary key).
`->charset('utf8mb4')`  |  Specify a character set for the column (MySQL).
`->collation('utf8mb4_unicode_ci')`  |  Specify a collation for the column (MySQL/PostgreSQL/SQL Server).
`->comment('my comment')`  |  Add a comment to a column (MySQL/PostgreSQL).
`->default($value)`  |  Specify a "default" value for the column.
`->first()`  |  Place the column "first" in the table (MySQL).
`->from($integer)`  |  Set the starting value of an auto-incrementing field (MySQL / PostgreSQL).
`->invisible()`  |  Make the column "invisible" to `SELECT *` queries (MySQL).
`->nullable($value = true)`  |  Allow NULL values to be inserted into the column.
`->storedAs($expression)`  |  Create a stored generated column (MySQL / PostgreSQL).
`->unsigned()`  |  Set INTEGER columns as UNSIGNED (MySQL).
`->useCurrent()`  |  Set TIMESTAMP columns to use CURRENT_TIMESTAMP as default value.
`->useCurrentOnUpdate()`  |  Set TIMESTAMP columns to use CURRENT_TIMESTAMP when a record is updated.
`->virtualAs($expression)`  |  Create a virtual generated column (MySQL).
`->generatedAs($expression)`  |  Create an identity column with specified sequence options (PostgreSQL).
`->always()`  |  Defines the precedence of sequence values over input for an identity column (PostgreSQL).
`->isGeometry()`  |  Set spatial column type to `geometry` - the default type is `geography` (PostgreSQL).

Modifier  |  설명
--------  |  -----------
`->after('column')`  |  컬럼을 다른 컬럼 "뒤"로 옮깁니다 (MySQL)
`->autoIncrement()`  |  INTEGER 컬럼을 자동으로 증가하는 (auto-increment) (primary key)로 지정합니다
`->charset('utf8mb4')`  |  컬럼의 캐릭터셋을 지정합니다 (MySQL)
`->collation('utf8mb4_unicode_ci')`  |  컬럼의 collation 지정합니다 (MySQL/PostgreSQL/SQL Server)
`->comment('my comment')`  |  컬럼에 코멘트 추가합니다 (MySQL/PostgreSQL)
`->default($value)`  |  컬럼의 "기본"값을 설정합니다
`->first()`  |  컬럼을 테이블의 "맨 처음" 위치로 옮깁니다 (MySQL)
`->from($integer)`  |  자동 증가 필드의 시작 값을 설정합니다 (MySQL / PostgreSQL)
`->invisible()`  |  컬럼을 `SELECT *` 쿼리에 "invisible(보이지 않게)" 만듭니다. (MySQL)
`->nullable($value = true)`  |  컬럼에 NULL 값이 입력되는 것을 허용합니다(기본값)
`->storedAs($expression)`  |  stored generated 컬럼 생성하기 (MySQL)
`->unsigned()`  |  INTEGER 컬럼을 UNSIGNED 으로 지정 (MySQL)
`->useCurrent()`  |  CURRENT_TIMESTAMP를 기본값으로 사용하도록 TIMESTAMP 컬럼을 설정합니다.
`->useCurrentOnUpdate()`  |  레코드가 수정될 때 CURRENT_TIMESTAMP를 사용하도록 TIMESTAMP 컬럼을 설정합니다.
`->virtualAs($expression)`  |  virtual generated 컬럼 생성하기 (MySQL)
`->generatedAs($expression)`  |  지정한 시퀀스 옵션을 사용하여 ID 컬럼 만들기 (PostgreSQL)
`->always()`  |  id 컬럼에 입력할 순차 값의 우선 순위를 정의합니다 (PostgreSQL)
`->isGeometry()`  |  공간 컬럼 유형을 `geometry`으로 설정 - 기본 유형은 `geography`입니다. (PostgreSQL).

<a name="default-expressions"></a>
#### Default Expressions
#### 기본 표현식

The `default` modifier accepts a value or an `Illuminate\Database\Query\Expression` instance. Using an `Expression` instance will prevent Laravel from wrapping the value in quotes and allow you to use database specific functions. One situation where this is particularly useful is when you need to assign default values to JSON columns:

`default` 수정자-modifier는 값 또는 `\Illuminate\Database\Query\Expression` 인스턴스를 허용합니다. `Expression` 인스턴스를 사용하면 라라벨이 값을 따옴표로 묶는 것을 방지하고 데이터베이스 별 함수를 사용할 수 있습니다. 이것이 특히 유용한 상황 중 하나는 JSON 컬럼에 기본값을 할당하는 것입니다.

    <?php

    use Illuminate\Support\Facades\Schema;
    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Database\Query\Expression;
    use Illuminate\Database\Migrations\Migration;

    class CreateFlightsTable extends Migration
    {
        /**
         * Run the migrations.
         *
         * @return void
         */
        public function up()
        {
            Schema::create('flights', function (Blueprint $table) {
                $table->id();
                $table->json('movies')->default(new Expression('(JSON_ARRAY())'));
                $table->timestamps();
            });
        }
    }

> {note} Support for default expressions depends on your database driver, database version, and the field type. Please refer to your database's documentation.

> {note} 기본 표현식 지원은 데이터베이스 드라이버, 데이터베이스 버전 및 필드 유형에 따라 다릅니다. 데이터베이스의 설명서를 참조하십시오.

<a name="column-order"></a>
#### Column Order
#### 컬럼 순서

When using the MySQL database, the `after` method may be used to add columns after an existing column in the schema:

MySQL 데이터베이스를 사용할 때 `after` 메서드를 사용하여 스키마의 기존 컬럼 뒤에 컬럼을 추가할 수 있습니다.

    $table->after('password', function ($table) {
        $table->string('address_line1');
        $table->string('address_line2');
        $table->string('city');
    });

<a name="modifying-columns"></a>
### Modifying Columns
### 컬럼 수정

<a name="prerequisites"></a>
#### Prerequisites
#### 전제 조건

Before modifying a column, you must install the `doctrine/dbal` package using the Composer package manager. The Doctrine DBAL library is used to determine the current state of the column and to create the SQL queries needed to make the requested changes to your column:

열을 수정하기 전에 Composer 패키지 관리자를 사용하여 `doctrine/dbal` 패키지를 설치해야 합니다. Doctrine DBAL 라이브러리는 컬럼의 현재 상태를 확인하고 컬럼에 요청된 변경을 수행하는 데 필요한 SQL 쿼리를 만드는 데 사용됩니다.

    composer require doctrine/dbal

If you plan to modify columns created using the `timestamp` method, you must also add the following configuration to your application's `config/database.php` configuration file:

`timestamp` 메소드를 사용하여 생성된 컬럼을 수정하려는 경우 애플리케이션의 `config/database.php` 구성 파일에 다음 구성도 추가해야 합니다.

```php
use Illuminate\Database\DBAL\TimestampType;

'dbal' => [
    'types' => [
        'timestamp' => TimestampType::class,
    ],
],
```

> {note} If your application is using Microsoft SQL Server, please ensure that you install `doctrine/dbal:^3.0`.

> {참고} 응용 프로그램이 Microsoft SQL Server를 사용하는 경우 `doctrine/dbal:^3.0`을 설치해야 합니다.

<a name="updating-column-attributes"></a>
#### Updating Column Attributes
#### 컬럼의 속성 변경하기

The `change` method allows you to modify the type and attributes of existing columns. For example, you may wish to increase the size of a `string` column. To see the `change` method in action, let's increase the size of the `name` column from 25 to 50. To accomplish this, we simply define the new state of the column and then call the `change` method:

`change` 메소드는 이미 존재하는 컬럼 타입을 유형과 속성을 변경합니다. 예를 들어, `string` 컬럼의 사이즈를 늘이고 싶을 수 있습니다. `change` 메소드가 어떻게 작동하는지 `name` 컬럼 사이즈를 25에서 50으로 늘여서 확인해 보겠습니다. 이를 수행하기 위해 컬럼의 새 상태를 정의한 다음 `change` 메서드를 호출하기만 하면 됩니다.

    Schema::table('users', function (Blueprint $table) {
        $table->string('name', 50)->change();
    });

We could also modify a column to be nullable:

컬럼을 nullable로 수정할 수도 있습니다.

    Schema::table('users', function (Blueprint $table) {
        $table->string('name', 50)->nullable()->change();
    });

> {note} The following column types can be modified: `bigInteger`, `binary`, `boolean`, `date`, `dateTime`, `dateTimeTz`, `decimal`, `integer`, `json`, `longText`, `mediumText`, `smallInteger`, `string`, `text`, `time`, `unsignedBigInteger`, `unsignedInteger`, `unsignedSmallInteger`, and `uuid`.  To modify a `timestamp` column type a [Doctrine type must be registered](#prerequisites).

> {note} 수정할 수 있는 컬럼 유형은 `bigInteger`, `binary`, `boolean`, `date`, `dateTime`, `dateTimeTz`, `decimal`, `integer`, `json`, `longText입니다. `, `mediumText`, `smallInteger`, `string`, `text`, `time`, `unsignedBigInteger`, `unsignedInteger`, `unsignedSmallInteger` 및 `uuid`. `timestamp` 컬럼 유형을 수정하려면 [Doctrine 타입 등록 필요](#prerequisites).

<a name="renaming-columns"></a>
#### Renaming Columns
### 인덱스 이름 변경하기

컬럼의 이름을 바꾸려면 스키마 빌더 청사진(blueprint)에서 제공하는 `renameColumn` 메서드를 사용할 수 있습니다. 컬럼 이름을 바꾸기 전에 Composer 패키지 관리자를 통해 `doctrine/dbal` 라이브러리를 설치했는지 확인하세요:

    Schema::table('users', function (Blueprint $table) {
        $table->renameColumn('from', 'to');
    });

> {note} Renaming an `enum` column is not currently supported.

> {note} `enum` 컬럼의 이름을 바꾸는 것은 현재 지원되지 않습니다.

<a name="dropping-columns"></a>
### Dropping Columns

To drop a column, you may use the `dropColumn` method on the schema builder blueprint. If your application is utilizing an SQLite database, you must install the `doctrine/dbal` package via the Composer package manager before the `dropColumn` method may be used:

컬럼을 삭제하려면 스키마 빌더 청사진(blueprint)에서 `dropColumn` 메서드를 사용할 수 있습니다. 애플리케이션이 SQLite 데이터베이스를 사용하는 경우 `dropColumn` 메소드를 사용하기 전에 Composer 패키지 관리자를 통해 `doctrine/dbal` 패키지를 설치해야 합니다.

    Schema::table('users', function (Blueprint $table) {
        $table->dropColumn('votes');
    });

You may drop multiple columns from a table by passing an array of column names to the `dropColumn` method:

컬럼 이름의 배열을 `dropColumn` 메서드에 전달하여 테이블에서 여러 컬럼을 삭제할 수 있습니다.

    Schema::table('users', function (Blueprint $table) {
        $table->dropColumn(['votes', 'avatar', 'location']);
    });

> {note} Dropping or modifying multiple columns within a single migration while using an SQLite database is not supported.

> {note} SQLite 데이터베이스를 사용하는 동안 단일 마이그레이션 내에서 여러 컬럼을 삭제하거나 수정하는 것은 지원되지 않습니다.

<a name="available-command-aliases"></a>
#### Available Command Aliases
#### 사용가능한 명령어 alias(별칭)

Laravel provides several convenient methods related to dropping common types of columns. Each of these methods is described in the table below:

라라벨은 일반적인 유형의 컬럼 삭제와 관련된 몇 가지 편리한 방법을 제공합니다. 이러한 각 방법은 아래 표에 설명되어 있습니다.

Command  |  Description
-------  |  -----------
`$table->dropMorphs('morphable');`  |  Drop the `morphable_id` and `morphable_type` columns.
`$table->dropRememberToken();`  |  Drop the `remember_token` column.
`$table->dropSoftDeletes();`  |  Drop the `deleted_at` column.
`$table->dropSoftDeletesTz();`  |  Alias of `dropSoftDeletes()` method.
`$table->dropTimestamps();`  |  Drop the `created_at` and `updated_at` columns.
`$table->dropTimestampsTz();` |  Alias of `dropTimestamps()` method.

명령어  |  설명
-------  |  -----------
`$table->dropMorphs('morphable');`  | `morphable_id` 와 `morphable_type` 컬럼 drop.
`$table->dropRememberToken();`  |  `remember_token` 컬럼 drop.
`$table->dropSoftDeletes();`  |  `deleted_at` 컬럼 drop.
`$table->dropSoftDeletesTz();`  |  `dropSoftDeletes()` 메소드의 alias.
`$table->dropTimestamps();`  |  `created_at`와 `updated_at` 컬럼 drop.
`$table->dropTimestampsTz();` |  `dropTimestamps()` 메소드의 alias.

<a name="indexes"></a>
## Indexes
## 인덱스

<a name="creating-indexes"></a>
### Creating Indexes
### 인덱스 생성하기

The Laravel schema builder supports several types of indexes. The following example creates a new `email` column and specifies that its values should be unique. To create the index, we can chain the `unique` method onto the column definition:

Laravel 스키마 빌더는 여러 타입의 인덱스를 지원합니다. 다음에서 새로운 `email` 컬럼을 만들고 해당 값을 고유 값을 가지도록 만들어 보겠습니다. 인덱스를 생성하려면 컬럼의 정의에서 `unique` 메소드를 체이닝 하면 됩니다.

    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Support\Facades\Schema;

    Schema::table('users', function (Blueprint $table) {
        $table->string('email')->unique();
    });

Alternatively, you may create the index after defining the column. To do so, you should call the `unique` method on the schema builder blueprint. This method accepts the name of the column that should receive a unique index:

또는 컬럼을 정의한 후 인덱스를 생성할 수 있습니다. 그렇게 하려면 스키마 빌더 청사진(blueprint)에서 `unique` 메서드를 호출해야 합니다. 이 메서드는 고유 인덱스를 받아야 하는 컬럼의 이름을 수락합니다.

    $table->unique('email');

You may even pass an array of columns to an index method to create a compound (or composite) index:

인덱스 메소드에 컬럼의 배열을 전달하여 결합(복합) 인덱스를 생성할 수도 있습니다.

    $table->index(['account_id', 'created_at']);

When creating an index, Laravel will automatically generate an index name based on the table, column names, and the index type, but you may pass a second argument to the method to specify the index name yourself:

인덱스를 생성할 때, 라라벨은 자동으로 테이블, 컬럼 이름, 인덱스 타입을 기반으로 인덱스 이름을 생성하지만, 인덱스 이름을 지정하기 위해 두 번째 인자를 메소드에 전달할 수도 있습니다.

    $table->unique('email', 'unique_email');

라라벨은 테이블, 컬럼 이름 및 인덱스 유형을 기반으로 인덱스 이름을 자동으로 생성하지만 메소드의 두번째 인자로 인덱스 이름을 지정할 수도 있습니다.

<a name="available-index-types"></a>
#### Available Index Types
#### 사용가능한 인덱스 타입

Laravel's schema builder blueprint class provides methods for creating each type of index supported by Laravel. Each index method accepts an optional second argument to specify the name of the index. If omitted, the name will be derived from the names of the table and column(s) used for the index, as well as the index type. Each of the available index methods is described in the table below:

라라벨의 스키마 빌더 청사진(blueprint) 클래스는 라라벨이 지원하는 각 유형의 인덱스를 생성하기 위한 메소드를 제공합니다. 각 인덱스 메서드는 인덱스 이름을 지정하기 위해 선택적 두 번째 인수를 허용합니다. 생략하면 이름은 인덱스 유형뿐만 아니라 인덱스에 사용된 테이블 및 컬럼의 이름에서 파생됩니다. 사용 가능한 각 인덱스 방법은 아래 표에 설명되어 있습니다.

Command  |  Description
-------  |  -----------
`$table->primary('id');`  |  Adds a primary key.
`$table->primary(['id', 'parent_id']);`  |  Adds composite keys.
`$table->unique('email');`  |  Adds a unique index.
`$table->index('state');`  |  Adds an index.
`$table->fulltext('body');`  |  Adds a fulltext index (MySQL/PostgreSQL).
`$table->fulltext('body')->language('english');`  |  Adds a fulltext index of the specified language (PostgreSQL).
`$table->spatialIndex('location');`  |  Adds a spatial index (except SQLite).

커맨드  |  설명
-------  |  -----------
`$table->primary('id');`  |  primary key 추가
`$table->primary(['id', 'parent_id']);`  |   복합 키 추가
`$table->unique('email');`  |  유니크 인덱스 추가
`$table->index('state');`  |  기본적인 인덱스 추가
`$table->fulltext('body');`  |  fulltext 인덱스 추가 (MySQL/PostgreSQL).
`$table->fulltext('body')->language('english');`  |  특정 언어의 fulltext 인덱스 추가 (PostgreSQL).
`$table->spatialIndex('location');`  |  공간(spatial) 인덱스 추가 (SQLite 제외.)

<a name="index-lengths-mysql-mariadb"></a>
#### Index Lengths & MySQL / MariaDB
#### 인덱스 길이 & MySQL / MariaDB

By default, Laravel uses the `utf8mb4` character set. If you are running a version of MySQL older than the 5.7.7 release or MariaDB older than the 10.2.2 release, you may need to manually configure the default string length generated by migrations in order for MySQL to create indexes for them. You may configure the default string length by calling the `Schema::defaultStringLength` method within the `boot` method of your `App\Providers\AppServiceProvider` class:

기본적으로 라라벨은 `utf8mb4` 캐릭터셋을 사용합니다. 5.7.7 릴리스 이전의 MySQL 또는 10.2.2 릴리스 이전의 MariaDB를 실행 중인 경우 MySQL이 해당 인덱스를 생성하도록 마이그레이션에 의해 생성된 기본 문자열 길이를 수동으로 구성해야 할 수 있습니다. `App\Providers\AppServiceProvider` 클래스의 `boot` 메소드 내에서 `Schema::defaultStringLength` 메소드를 호출하여 기본 문자열 길이를 구성할 수 있습니다.

    use Illuminate\Support\Facades\Schema;

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        Schema::defaultStringLength(191);
    }

Alternatively, you may enable the `innodb_large_prefix` option for your database. Refer to your database's documentation for instructions on how to properly enable this option.

또는 데이터베이스에 `innodb_large_prefix` 옵션을 활성화 할 수도 있습니다. 이 옵션을 제대로 사용하는 방법은 데이터베이스 매뉴얼을 참고하십시오.

<a name="renaming-indexes"></a>
### Renaming Indexes
### 인덱스 이름 변경하기

To rename an index, you may use the `renameIndex` method provided by the schema builder blueprint. This method accepts the current index name as its first argument and the desired name as its second argument:

인덱스의 이름을 변경하기 위해서는 `renameIndex` 메소드를 사용하면 됩니다. 이 메소드는 현재의 인덱스 이름을 첫번째 인자로, 변경하고자 하는 새이름을 두번째 인자로 전달받습니다.

    $table->renameIndex('from', 'to')

<a name="dropping-indexes"></a>
### Dropping Indexes
### 인덱스 삭제하기

To drop an index, you must specify the index's name. By default, Laravel automatically assigns an index name based on the table name, the name of the indexed column, and the index type. Here are some examples:

인덱스를 삭제하기 위해서는 인덱스의 이름을 지정해야 합니다. 기본적으로 라라벨은 테이블 이름, 인덱스된 컬럼의 이름, 그리고 인덱스 타입을 기반으로 자동으로 인덱스의 이름을 부여하도록 설정되어 있습니다. 다음은 몇 개의 예제 입니다.

Command  |  Description
-------  |  -----------
`$table->dropPrimary('users_id_primary');`  |  Drop a primary key from the "users" table.
`$table->dropUnique('users_email_unique');`  |  Drop a unique index from the "users" table.
`$table->dropIndex('geo_state_index');`  |  Drop a basic index from the "geo" table.
`$table->dropSpatialIndex('geo_location_spatialindex');`  |  Drop a spatial index from the "geo" table  (except SQLite).

명령어  |  설명
-------------  |  -------------
`$table->dropPrimary('users_id_primary');`  |  "users" 테이블에서 프라이머리 키 지우기.
`$table->dropUnique('users_email_unique');`  |  "users" 테이블에서 유니크 인덱스 지우기.
`$table->dropIndex('geo_state_index');`  |  "geo" 테이블에서 기본적인 인덱스 지우기.
`$table->dropSpatialIndex('geo_location_spatialindex');`  |  "geo" 테이블에서 공간(spatial) 인덱스 지우기(SQLite 제외).

If you pass an array of columns into a method that drops indexes, the conventional index name will be generated based on the table name, columns, and index type:

인덱스들을 삭제하기 위해서 메소드에 컬럼의 배열을 전달하게 되면 인덱스의 이름은 테이블명, 컬럼명 그리고 키의 타입을 기반으로 컨벤션에 의해서 인덱스 이름을 추정할 것입니다.

    Schema::table('geo', function (Blueprint $table) {
        $table->dropIndex(['state']); // Drops index 'geo_state_index'
    });

<a name="foreign-key-constraints"></a>
### Foreign Key Constraints
### 외래키 제약조건(Constraints)

Laravel also provides support for creating foreign key constraints, which are used to force referential integrity at the database level. For example, let's define a `user_id` column on the `posts` table that references the `id` column on a `users` table:

라라벨은 데이터베이스에서 또한 참조 무결성을 강제하는 외래 키 제약조건을 생성하는 것을 제공합니다. 예를 들어 `users` 테이블의 `id` 컬럼을 참조하는 `posts` 테이블에 `user_id` 컬럼을 정의해보겠습니다.

    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Support\Facades\Schema;

    Schema::table('posts', function (Blueprint $table) {
        $table->unsignedBigInteger('user_id');

        $table->foreign('user_id')->references('id')->on('users');
    });

Since this syntax is rather verbose, Laravel provides additional, terser methods that use conventions to provide a better developer experience. When using the `foreignId` method to create your column, the example above can be rewritten like so:

이 구문은 다소 장황하기 때문에 라라벨은 더 나은 개발자 경험을 제공하기 위해 규칙을 사용하는 간결한 추가 메서드를 제공합니다. `foreignId` 메소드를 사용하여 컬럼을 생성할 때 위의 예는 다음과 같이 다시 작성할 수 있습니다.

    Schema::table('posts', function (Blueprint $table) {
        $table->foreignId('user_id')->constrained();
    });

The `foreignId` method creates an `UNSIGNED BIGINT` equivalent column, while the `constrained` method will use conventions to determine the table and column name being referenced. If your table name does not match Laravel's conventions, you may specify the table name by passing it as an argument to the `constrained` method:

`foreignId` 메서드는 `UNSIGNED BIGINT`에 해당하는 컬럼을 생성하는 반면, `constrained` 메서드는 규칙을 사용하여 참조되는 테이블과 컬럼 이름을 결정합니다. 테이블 이름이 라라벨의 규칙과 일치하지 않으면 `constrained` 메소드에 인수로 전달하여 테이블 이름을 지정할 수 있습니다.

    Schema::table('posts', function (Blueprint $table) {
        $table->foreignId('user_id')->constrained('users');
    });

You may also specify the desired action for the "on delete" and "on update" properties of the constraint:

제약조건(constraint)의 "on delete"와 "on update" 속성에 원하는 동작을 지정할 수도 있습니다.

    $table->foreignId('user_id')
          ->constrained()
          ->onUpdate('cascade')
          ->onDelete('cascade');

An alternative, expressive syntax is also provided for these actions:

다음 작업에 대한 대안적인 표현 구문도 제공됩니다.

Method  |  Description
-------  |  -----------
`$table->cascadeOnUpdate();` | Updates should cascade.
`$table->restrictOnUpdate();`| Updates should be restricted.
`$table->cascadeOnDelete();` | Deletes should cascade.
`$table->restrictOnDelete();`| Deletes should be restricted.
`$table->nullOnDelete();`    | Deletes should set the foreign key value to null.

메소드  |  설명
-------  |  -----------
`$table->cascadeOnUpdate();` | 참조하는 테이블의 수정이 같이
`$table->restrictOnUpdate();`| 참조하는 테이블이 있으면 수정 불가
`$table->cascadeOnDelete();` | 참조하는 테이블의 삭제가 같이
`$table->restrictOnDelete();`| 참조하는 테이블이 있으면 삭제 불가
`$table->nullOnDelete();`    | 참조하는 테이블의 데이타 NULL로 변경

Any additional [column modifiers](#column-modifiers) must be called before the `constrained` method:

모든 추가 [컬럼 수정자-modifiers](#column-modifiers)는 `constrained` 메서드보다 먼저 호출되어야 합니다.

    $table->foreignId('user_id')
          ->nullable()
          ->constrained();

<a name="dropping-foreign-keys"></a>
#### Dropping Foreign Keys
#### 외래 키 삭제

To drop a foreign key, you may use the `dropForeign` method, passing the name of the foreign key constraint to be deleted as an argument. Foreign key constraints use the same naming convention as indexes. In other words, the foreign key constraint name is based on the name of the table and the columns in the constraint, followed by a "\_foreign" suffix:

외래 키를 삭제하려면 삭제할 외래 키 제약 조건의 이름을 인수로 전달하여 `dropForeign` 메서드를 사용할 수 있습니다. 외래 키 제약 조건은 인덱스와 동일한 명명 규칙을 사용합니다. 즉, 외래 키 제약 조건 이름은 테이블 이름과 제약 조건에 있는 컬럼을 기반으로 하며 그 뒤에 "\_foreign" 접미사가 붙습니다.

    $table->dropForeign('posts_user_id_foreign');

Alternatively, you may pass an array containing the column name that holds the foreign key to the `dropForeign` method. The array will be converted to a foreign key constraint name using Laravel's constraint naming conventions:

또는 외래 키를 포함하는 컬럼 이름을 포함하는 배열을 `dropForeign` 메서드에 전달할 수 있습니다. 배열은 라라벨의 제약 조건 명명 규칙을 사용하여 외래 키 제약 조건 이름으로 변환됩니다.

    $table->dropForeign(['user_id']);

<a name="toggling-foreign-key-constraints"></a>
#### Toggling Foreign Key Constraints
#### 외래 키 제약 조건 토글

You may enable or disable foreign key constraints within your migrations by using the following methods:

다음 방법을 사용하여 마이그레이션 내에서 외래 키 제약 조건을 활성화하거나 비활성화할 수 있습니다.

    Schema::enableForeignKeyConstraints();

    Schema::disableForeignKeyConstraints();

> {note} SQLite disables foreign key constraints by default. When using SQLite, make sure to [enable foreign key support](/docs/{{version}}/database#configuration) in your database configuration before attempting to create them in your migrations. In addition, SQLite only supports foreign keys upon creation of the table and [not when tables are altered](https://www.sqlite.org/omitted.html).

> {note} SQLite는 기본적으로 외래 키 제약 조건을 비활성화합니다. SQLite를 사용하는 경우 마이그레이션에서 생성을 시도하기 전에 데이터베이스 구성에서 [외래 키 지원 활성화](/docs/{{version}}/database#configuration)를 확인하십시오. 또한 SQLite는 테이블 생성 시에만 외래 키를 지원하며 [테이블이 변경되는 경우는 지원하지 않음](https://www.sqlite.org/omitted.html).

<a name="events"></a>
## Events
## 이벤트

For convenience, each migration operation will dispatch an [event](/docs/{{version}}/events). All of the following events extend the base `Illuminate\Database\Events\MigrationEvent` class:

편의를 위해 각 마이그레이션 작업은 [이벤트](/docs/{{version}}/events)를 전달합니다. 다음의 모든 이벤트는 기본 `Illuminate\Database\Events\MigrationEvent` 클래스를 확장합니다.

 Class | Description
-------|-------
| `Illuminate\Database\Events\MigrationsStarted` | A batch of migrations is about to be executed. |
| `Illuminate\Database\Events\MigrationsEnded` | A batch of migrations has finished executing. |
| `Illuminate\Database\Events\MigrationStarted` | A single migration is about to be executed. |
| `Illuminate\Database\Events\MigrationEnded` | A single migration has finished executing. |

 클래스 | 설명               
-------|-------
| `Illuminate\Database\Events\MigrationsStarted` | 일괄 마이그레이션 실행 시작시 |
| `Illuminate\Database\Events\MigrationsEnded` | 일괄 마이그레이션 실행 종료후 |
| `Illuminate\Database\Events\MigrationStarted` | 단일 마이그레이션 실행 시작시 |
| `Illuminate\Database\Events\MigrationEnded` | 단일 마이그레이션 실행 종료후 |