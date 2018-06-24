# Database: Migrations
# 데이터베이스: 마이그레이션

- [Introduction](#introduction)
- [소개하기](#introduction)
- [Generating Migrations](#generating-migrations)
- [마이그레이션 파일 생성하기](#generating-migrations)
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
    - [Renaming / Dropping Tables](#renaming-and-dropping-tables)
    - [테이블의 이름변경 / 제거](#renaming-and-dropping-tables)
- [Columns](#columns)
- [컬럼](#columns)
    - [Creating Columns](#creating-columns)
    - [컬럼 생성하기](#creating-columns)
    - [Column Modifiers](#column-modifiers)
    - [컬럼 수정 처리](#column-modifiers)
    - [Modifying Columns](#modifying-columns)
    - [컬럼 수정하기](#modifying-columns)
    - [Dropping Columns](#dropping-columns)
    - [컬럼 삭제하기](#dropping-columns)
- [Indexes](#indexes)
- [인덱스](#indexes)
    - [Creating Indexes](#creating-indexes)
    - [인덱스 생성하기](#creating-indexes)
    - [Dropping Indexes](#dropping-indexes)
    - [인덱스 없애기](#dropping-indexes)
    - [Foreign Key Constraints](#foreign-key-constraints)
    - [외래키 제약](#foreign-key-constraints)

<a name="introduction"></a>
## Introduction
## 소개하기

Migrations are like version control for your database, allowing your team to easily modify and share the application's database schema. Migrations are typically paired with Laravel's schema builder to easily build your application's database schema. If you have ever had to tell a teammate to manually add a column to their local database schema, you've faced the problem that database migrations solve.

마이그레이션은 여러분의 팀에서 손쉽게 애플리케이션의 데이터베이스를 수정하고 데이터베이스 스키마를 공유할 수 있도록 해주며 데이터베이스를 위한 버전 컨트롤과 같은 역할을 합니다. 마이그레이션은 보통 라라벨의 스키마 빌더와 쌍을 이루어 애플리케이션의 데이터베이스 스키마를 손쉽게 만들 수 있습니다. 만약 팀 동료에게 로컬 데이터베이스 스키마에 수동으로 컬럼을 추가하는 것에 대해서 이야기를 해야한다면, 데이터베이스 마이그레이션 기능이 해결하고자 하는 문제에 직면해 있는 것입니다.

The Laravel `Schema` [facade](/docs/{{version}}/facades) provides database agnostic support for creating and manipulating tables across all of Laravel's supported database systems.

라라벨의 `Schema` [파사드](/docs/{{version}}/facades)는 라라벨이 지원하는 모든 데이터베이스 시스템을 지원하는 테이블을 생성과 조작에 대한 광범위한 지원을 제공합니다.

<a name="generating-migrations"></a>
## Generating Migrations
## 마이그레이션 파일 생성하기

To create a migration, use the `make:migration` [Artisan command](/docs/{{version}}/artisan):

마이그레이션 파일을 생성하기 위해서는 `make:migration` [아티즌 명령어](/docs/{{version}}/artisan)를 사용합니다:

    php artisan make:migration create_users_table

The new migration will be placed in your `database/migrations` directory. Each migration file name contains a timestamp which allows Laravel to determine the order of the migrations.

새로운 마이그레이션 파일은 `database/migrations` 디렉토리에 생성됩니다. 각 마이그레이션 파일의 이름에는 타임스탬프를 포함하며 라라벨이 마이그레이션들의 순서를 판별할 수 있도록 합니다.

The `--table` and `--create` options may also be used to indicate the name of the table and whether the migration will be creating a new table. These options simply pre-fill the generated migration stub file with the specified table:

`--table`와 `--create` 옵션들은 테이블의 이름과 마이그레이션이 테이블을 새로 생성할지에 대해 명시하는데 사용될 수 있습니다. 이 옵션들은 단순히 생성된 마이그레이션 stub 파일을 특정한 테이블로 미리 채워 놓습니다:

    php artisan make:migration create_users_table --create=users

    php artisan make:migration add_votes_to_users_table --table=users

If you would like to specify a custom output path for the generated migration, you may use the `--path` option when executing the `make:migration` command. The given path should be relative to your application's base path.

생성된 마이그레이션 파일이 생성될 출력 경로를 지정하고 싶다면 `make:migration` 명령어를 실행할 때 `--path` 옵션을 사용하면 됩니다. 주어진 경로는 애플리케이션의 베이스 경로로 부터 연관된 상대경로여야 합니다.

<a name="migration-structure"></a>
## Migration Structure
## 마이그레이션의 구조

A migration class contains two methods: `up` and `down`. The `up` method is used to add new tables, columns, or indexes to your database, while the `down` method should simply reverse the operations performed by the `up` method.

마이그레이션 클래스는 `up`과 `down`, 두 개의 메드를 가지고 있습니다: `up` 메소드는 데이터베이스에 테이블, 컬럼, 인덱스를 추가하는데 사용되고, 이와 반대로 `down` 메소드는 `up` 메소드의 동작을 취소합니다.

Within both of these methods you may use the Laravel schema builder to expressively create and modify tables. To learn about all of the methods available on the `Schema` builder, [check out its documentation](#creating-tables). For example, this migration example creates a `flights` table:

이 두 메소드 안에서 모두 라라벨 스키마 빌더를 사용하여 테이블을 다양한 방법을 통해서 생성하고 변경할 수 있습니다. `Schema` 빌더가 제공하는 모든 메소드에 대해 알아보려면 [문서](#creating-tables)을 확인하세요. 다음에서, 마이그레이션 예제는 `flights` 테이블을 생성합니다:

    <?php

    use Illuminate\Database\Schema\Blueprint;
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
                $table->increments('id');
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


<a name="running-migrations"></a>
## Running Migrations
## 마이그레이션 실행하기

To run all of your outstanding migrations, execute the `migrate` Artisan command:

아직 실행된적이 없는 모든 마이그레이션을 실행하려면, `migrate` 아티즌 명령어를 실행하면 됩니다:

    php artisan migrate

> {note} If you are using the [Homestead virtual machine](/docs/{{version}}/homestead), you should run this command from within your virtual machine.

> {note} 만약 [홈스테드 가상머신](/docs/{{version}}/homestead)을 사용중이라면, 이 명령어를 가상머신 안에서 실행해야 합니다.

#### Forcing Migrations To Run In Production
#### 실제 production 서버에서 강제로 마이그레이션 실행하기

Some migration operations are destructive, which means they may cause you to lose data. In order to protect you from running these commands against your production database, you will be prompted for confirmation before the commands are executed. To force the commands to run without a prompt, use the `--force` flag:

일부의 마이그레이션 작업은 구조의 변경이 있을 수 있으며, 이로 인해 데이터를 잃어 버릴 수도 있습니다. 이러한 위험들로 부터 실제 production 서버의 데이터베이스를 보호하기 위해서, 마이그레이션 명령어를 실행하기 전에는 확인 메시지가 출력됩니다. 확인 메시지 없이 명령어을 실행시키기 위해서는, `--force` 플래그를 사용하면 됩니다:

    php artisan migrate --force

<a name="rolling-back-migrations"></a>
### Rolling Back Migrations
### 마이그레이션 되돌리기-롤백

To rollback the latest migration operation, you may use the `rollback` command. This command rolls back the last "batch" of migrations, which may include multiple migration files:

가장 최근의 마이그레이션 작업(operation)을 되돌리려면 `rollback` 명령어를 사용하면 됩니다. 이 명령어는 가장 최근에 실행된 마이그레이션의 "모음(batch)", 즉 여러개의 마이그레이션 파일에 대해서 되돌릴 수 있습니다:

    php artisan migrate:rollback

You may rollback a limited number of migrations by providing the `step` option to the `rollback` command. For example, the following command will rollback the last five migrations:

`rollback` 명령어에 `step` 옵션을 전달하여 제한된 숫자의 마이그레이션만 되돌릴 수 있습니다. 예를들어 다음의 명령어는 최근 5개의 마이그레이션만 되돌립니다:

    php artisan migrate:rollback --step=5

The `migrate:reset` command will roll back all of your application's migrations:

`migrate:reset` 커맨드는 애플리케이션의 모든 마이그레이션을 되돌립니다:

    php artisan migrate:reset

#### Rollback & Migrate In Single Command
#### 하나의 명령어로 롤백과와 마이그레이트 함께 실행 하기

The `migrate:refresh` command will roll back all of your migrations and then execute the `migrate` command. This command effectively re-creates your entire database:

`migrate:refresh` 명령어는 모든 마이그레이션을 되돌리고, `migrate` 명령어를 실행합니다. 이 명령어는 전체 데이터베이스를 효과적으로 재생성합니다:

    php artisan migrate:refresh

    // Refresh the database and run all database seeds...
    php artisan migrate:refresh --seed

You may rollback & re-migrate a limited number of migrations by providing the `step` option to the `refresh` command. For example, the following command will rollback & re-migrate the last five migrations:

`refresh` 명령어에 `step` 옵션을 전달하여 제한된 숫자의 마이그레이션만 되돌리고 다시 마이그레이션을 수행할 수 있습니다. 예를들어 다음의 명령어는 최근 5개의 마이그레이션만 되돌리고 마이그레이션을 다시 실행합니다:

    php artisan migrate:refresh --step=5

<a name="tables"></a>
## Tables
## 테이블

<a name="creating-tables"></a>
### Creating Tables
### 테이블 생성하기

To create a new database table, use the `create` method on the `Schema` facade. The `create` method accepts two arguments. The first is the name of the table, while the second is a `Closure` which receives a `Blueprint` object that may be used to define the new table:

새로운 데이터베이스 테이블을 생성하려면 `Schema` 파사드에 `create` 메소드를 사용하면 됩니다. `create` 메소드는 두개의 인자를 전달 받습니다. 첫번째 인자는 테이블의 이름이고, 두번째 인자는 새로운 테이블을 정의하는 사용되는 `Blueprint` 객체를 받는 `Closure`입니다:

    Schema::create('users', function (Blueprint $table) {
        $table->increments('id');
    });

Of course, when creating the table, you may use any of the schema builder's [column methods](#creating-columns) to define the table's columns.

물론 테이블을 생성할 때, 테이블의 컬럼을 정의하기 위하여 자유롭게 스키마 빌더의 [컬럼 메소드](#creating-columns)를 사용할 수 있습니다.

#### Checking For Table / Column Existence
#### 테이블 / 컬럼이 존재하는지 확인하기

You may easily check for the existence of a table or column using the `hasTable` and `hasColumn` methods:

`hasTable`와 `hasColumn` 메소드를 사용하면 손쉽게 테이블이나 컬럼의 존재 유무를 확인할 수 있습니다:

    if (Schema::hasTable('users')) {
        //
    }

    if (Schema::hasColumn('users', 'email')) {
        //
    }

#### Connection & Storage Engine
#### 커넥션-connection & 스토리지 엔진

If you want to perform a schema operation on a database connection that is not your default connection, use the `connection` method:

기본 커넥션-connection이 아닌 다른 데이터베이스 커넥션-connection에 스키마 작업을 수행하려면 `connection` 메소드를 사용하면 됩니다:

    Schema::connection('foo')->create('users', function (Blueprint $table) {
        $table->increments('id');
    });

You may use the `engine` property on the schema builder to define the table's storage engine:

테이블의 스토리지 엔진을 정의하기 위해서 스키마 빌더의 `engine` 속성을 사용할 수 있습니다:

    Schema::create('users', function (Blueprint $table) {
        $table->engine = 'InnoDB';

        $table->increments('id');
    });

<a name="renaming-and-dropping-tables"></a>
### Renaming / Dropping Tables
### 테이블 이름 변경 / 제거하기

To rename an existing database table, use the `rename` method:

이미 존재하는 테이블의 이름을 바꾸려면 `rename` 메소드를 이용하십시오:

    Schema::rename($from, $to);

To drop an existing table, you may use the `drop` or `dropIfExists` methods:

이미 존재하는 테이블을 제거하려면 `drop`이나 `dropIfExists` 메소드를 사용하세요:

    Schema::drop('users');

    Schema::dropIfExists('users');

#### Renaming Tables With Foreign Keys
#### 외래 키를 가진 테이블의 이름 변경

Before renaming a table, you should verify that any foreign key constraints on the table have an explicit name in your migration files instead of letting Laravel assign a convention based name. Otherwise, the foreign key constraint name will refer to the old table name.

테이블의 이름 변경하기 전에, 마이그레이션 파일에서 라라벨의 규약에 따르는 기본 이름으로 지정되지 않고 고유한 이름을 붙인 외래 키 제약 조건이 존재하지 않는지 확인하십시오. 그렇지 않다면 외래 키 제약의 이름이 기존 테이블 이름을 참고하게 됩니다.

<a name="columns"></a>
## Columns
## 컬럼

<a name="creating-columns"></a>
### Creating Columns
### 컬럼 생성하기

The `table` method on the `Schema` facade may be used to update existing tables. Like the `create` method, the `table` method accepts two arguments: the name of the table and a `Closure` that receives a `Blueprint` instance you may use to add columns to the table:

이미 존재하는 테이블을 변경하기 하는데, `Schema` 파사드의 `table` 메소드를 사용할 수 있습니다. `create` 메소드와 같이 `table` 메소드도 두개읜 인자를 전달 받습니다: 하나는 테이블의 이름이고 ,다른 하나는 테이블에 컬럼을 추가하는데 사용할 수 있는 `Blueprint` 인스턴스를 받아들이는 `Closure`입니다:

    Schema::table('users', function (Blueprint $table) {
        $table->string('email');
    });

#### Available Column Types
#### 사용가능한 컬럼의 타입들

Of course, the schema builder contains a variety of column types that you may specify when building your tables:

당연하게도, 스키마 빌더는 테이블을 만들 때 이를 지정할 수 있는 다양한 컬럼 타입들을 가지고 있습니다:

Command  | Description
------------- | -------------
`$table->bigIncrements('id');`  |  Incrementing ID (primary key) using a "UNSIGNED BIG INTEGER" equivalent.
`$table->bigInteger('votes');`  |  BIGINT equivalent for the database.
`$table->binary('data');`  |  BLOB equivalent for the database.
`$table->boolean('confirmed');`  |  BOOLEAN equivalent for the database.
`$table->char('name', 4);`  |  CHAR equivalent with a length.
`$table->date('created_at');`  |  DATE equivalent for the database.
`$table->dateTime('created_at');`  |  DATETIME equivalent for the database.
`$table->dateTimeTz('created_at');`  |  DATETIME (with timezone) equivalent for the database.
`$table->decimal('amount', 5, 2);`  |  DECIMAL equivalent with a precision and scale.
`$table->double('column', 15, 8);`  |  DOUBLE equivalent with precision, 15 digits in total and 8 after the decimal point.
`$table->enum('choices', ['foo', 'bar']);` | ENUM equivalent for the database.
`$table->float('amount');`  |  FLOAT equivalent for the database.
`$table->increments('id');`  |  Incrementing ID (primary key) using a "UNSIGNED INTEGER" equivalent.
`$table->integer('votes');`  |  INTEGER equivalent for the database.
`$table->ipAddress('visitor');`  |  IP address equivalent for the database.
`$table->json('options');`  |  JSON equivalent for the database.
`$table->jsonb('options');`  |  JSONB equivalent for the database.
`$table->longText('description');`  |  LONGTEXT equivalent for the database.
`$table->macAddress('device');`  |  MAC address equivalent for the database.
`$table->mediumInteger('numbers');`  |  MEDIUMINT equivalent for the database.
`$table->mediumText('description');`  |  MEDIUMTEXT equivalent for the database.
`$table->morphs('taggable');`  |  Adds INTEGER `taggable_id` and STRING `taggable_type`.
`$table->nullableMorphs('taggable');`  |  Nullable versions of the `morphs()` columns.
`$table->nullableTimestamps();`  |  Nullable versions of the `timestamps()` columns.
`$table->rememberToken();`  |  Adds `remember_token` as VARCHAR(100) NULL.
`$table->smallInteger('votes');`  |  SMALLINT equivalent for the database.
`$table->softDeletes();`  |  Adds `deleted_at` column for soft deletes.
`$table->string('email');`  |  VARCHAR equivalent column.
`$table->string('name', 100);`  |  VARCHAR equivalent with a length.
`$table->text('description');`  |  TEXT equivalent for the database.
`$table->time('sunrise');`  |  TIME equivalent for the database.
`$table->timeTz('sunrise');`  |  TIME (with timezone) equivalent for the database.
`$table->tinyInteger('numbers');`  |  TINYINT equivalent for the database.
`$table->timestamp('added_on');`  |  TIMESTAMP equivalent for the database.
`$table->timestampTz('added_on');`  |  TIMESTAMP (with timezone) equivalent for the database.
`$table->timestamps();`  |  Adds `created_at` and `updated_at` columns.
`$table->uuid('id');`  |  UUID equivalent for the database.

명령  | 설명
------------- | -------------
`$table->bigIncrements('id');`  |  "UNSIGNED BIG INTEGER"에 해당하는 Incrementing ID (프라이머리 키).
`$table->bigInteger('votes');`  |  데이터베이스의 BIGINT.
`$table->binary('data');`  |  데이터베이스의 BLOB. 
`$table->boolean('confirmed');`  |  데이터베이스의 BOOLEAN.
`$table->char('name', 4);`  |  CHAR에 해당하며 길이(length)를 가짐.
`$table->date('created_at');`  |  데이터베이스의 DATE.
`$table->dateTime('created_at');`  |  데이터베이스의 DATETIME.
`$table->dateTimeTz('created_at');`  | 데이터베이스의 DATETIME (타임존과 함께)
`$table->decimal('amount', 5, 2);`  |  유효값과 소수 자릿수를 지정한 DECIMAL
`$table->double('column', 15, 8);`  |  15자리, 소수점 8자릿수를 지정한 DOUBLE .
`$table->enum('choices', ['foo', 'bar']);` | 데이터베이스의 ENUM.
`$table->float('amount');`  |  데이터베이스의 FLOAT. 
`$table->increments('id');`  |  "UNSIGNED INTEGER"에 해당하는 Incrementing ID (프라이머리 키).
`$table->integer('votes');`  |  데이터베이스의 INTEGER.
`$table->ipAddress('visitor');`  |  IP 주소.
`$table->json('options');`  |  데이터베이스의 JSON.
`$table->jsonb('options');`  |  데이터베이스의 JSONB.
`$table->longText('description');`  |  데이터베이스의 LONGTEXT.
`$table->macAddress('device');`  |  MAC 어드레스.
`$table->mediumInteger('numbers');`  |  데이터베이스의 MEDIUMINT.
`$table->mediumText('description');`  |  데이터베이스의 MEDIUMTEXT.
`$table->morphs('taggable');`  |  `taggable_id` INTEGER와 `taggable_type` STRING 추가.
`$table->nullableMorphs('taggable');`  |  `morphs()` 컬럼의 Null 허용이 가능한 버전.
`$table->nullableTimestamps();`  |  `timestamps()` 컬럼의 Null 허용이 가능한 버전.
`$table->rememberToken();`  |  `remember_token`을 VARCHAR(100) NULL로 추가.
`$table->smallInteger('votes');`  |  데이터베이스의 SMALLINT.
`$table->softDeletes();`  |  soft delete할 때 `deleted_at` 컬럼을 추가함.
`$table->string('email');`  |  VARCHAR에 해당하는 컬럼.
`$table->string('name', 100);`  |  VARCHAR에 해당하며 길이(length)를 가짐.
`$table->text('description');`  |  데이터베이스의 TEXT.
`$table->time('sunrise');`  |  데이터베이스의 TIME.
`$table->timeTz('sunrise');`  |  데이터베이스의 TIME(타임존과 함께).
`$table->tinyInteger('numbers');`  |  데이터베이스의 TINYINT.
`$table->timestamp('added_on');`  |  데이터베이스의 TIMESTAMP.
`$table->timestampTz('added_on');`  |  데이터베이스의 TIMESTAMP (타임존과 함께).
`$table->timestamps();`  |  `created_at`과 `updated_at` 컬럼을 추가함.
`$table->uuid('id');`  |  데이터베이스의 UUID에 해당.

<a name="column-modifiers"></a>
### Column Modifiers
### 컬럼 Modifiers

In addition to the column types listed above, there are several column "modifiers" you may use while adding a column to a database table. For example, to make the column "nullable", you may use the `nullable` method:

위 목록의 컬럼 타입들에 더하여, 데이터베이스 테이블에 컬럼을 추가하는데 사용할 수 있는 몇가지 컬럼 "modifier"가 존재합니다. 예를 들어, 컬럼을 "nullable" 하게 만들려면, `nullable` 메소드를 사용할 수 있습니다:

    Schema::table('users', function (Blueprint $table) {
        $table->string('email')->nullable();
    });

Below is a list of all the available column modifiers. This list does not include the [index modifiers](#creating-indexes):

아래는 사용 가능한 모든 컬럼 Modifier의 목록입니다. 이 목록은 [인덱스 modifiers](#creating-indexes)를 포함하지 않습니다:

Modifier  | Description
------------- | -------------
`->after('column')`  |  Place the column "after" another column (MySQL Only)
`->comment('my comment')`  |  Add a comment to a column
`->default($value)`  |  Specify a "default" value for the column
`->first()`  |  Place the column "first" in the table (MySQL Only)
`->nullable()`  |  Allow NULL values to be inserted into the column
`->storedAs($expression)`  |  Create a stored generated column (MySQL Only)
`->unsigned()`  |  Set `integer` columns to `UNSIGNED`
`->virtualAs($expression)`  |  Create a virtual generated column (MySQL Only)

Modifier  | 설명
------------- | -------------
`->after('column')`  |  컬럼을 다른 컬럼 "뒤"로 옮기세요 (MySQL의 경우에만)
`->comment('my comment')`  |  컬럼에 코멘트 추가
`->default($value)`  |  컬럼의 "기본"값을 설정합니다
`->first()`  |  테이블에 컬럼을 "맨 처음" 위치로 옮기세요 (MySQL의 경우에만) 
`->nullable()`  |  NULL 값들이 컬럼에 입력되는 것을 허용합니다
`->storedAs($expression)`  |  Create a stored generated column (MySQL의 경우에만)
`->unsigned()`  | `UNSIGNED`에 `integer` 컬럼을 설정합니다
`->virtualAs($expression)`  |  Create a virtual generated column (MySQL의 경우에만)

<a name="changing-columns"></a>
<a name="modifying-columns"></a>
### Modifying Columns
### 컬럼 수정하기

#### Prerequisites
#### 사전 준비사항

Before modifying a column, be sure to add the `doctrine/dbal` dependency to your `composer.json` file. The Doctrine DBAL library is used to determine the current state of the column and create the SQL queries needed to make the specified adjustments to the column:

컬럼을 수정하기 전에, 꼭 `composer.json` 파일에 `doctrine/dbal` 의존성을 추가하십시오. Doctrine DBAL 라이브러리는 컬럼의 현재 상태를 확인하고 필요한 SQL 쿼리를 생성하여 컬럼에 지정된 변경사항을 수행하기 위해 사용됩니다:

    composer require doctrine/dbal

#### Updating Column Attributes
#### 컬럼의 속성 변경하기

The `change` method allows you to modify some existing column types to a new type or modify the column's attributes. For example, you may wish to increase the size of a string column. To see the `change` method in action, let's increase the size of the `name` column from 25 to 50:

`change` 메소드는 이미 존재하는 컬럼 타입을 새로운 타입으로 수정하거나 컬럼의 속성을 변경합니다. 예를 들어, 문자열 컬럼의 사이즈를 늘이고 싶을 수 있습니다. `change` 메소드가 어떻게 작동하는지 `name` 컬럼 사이즈를 25에서 50으로 늘여서 확인해 보겠습니다:

    Schema::table('users', function (Blueprint $table) {
        $table->string('name', 50)->change();
    });

We could also modify a column to be nullable:

컬럼을 nullable로 수정할 수도 있습니다:

    Schema::table('users', function (Blueprint $table) {
        $table->string('name', 50)->nullable()->change();
    });

> {note} The following column types can not be "changed": char, double, enum, mediumInteger, timestamp, tinyInteger, ipAddress, json, jsonb, macAddress, mediumIncrements, morphs, nullableMorphs, nullableTimestamps, softDeletes, timeTz, timestampTz, timestamps, timestampsTz, unsignedMediumInteger, unsignedTinyInteger, uuid.

> {note} 다음의 컬럼 타입들은 "변경" 할 수 없습니다: char, double, enum, mediumInteger, timestamp, tinyInteger, ipAddress, json, jsonb, macAddress, mediumIncrements, morphs, nullableMorphs, nullableTimestamps, softDeletes, timeTz, timestampTz, timestamps, timestampsTz, unsignedMediumInteger, unsignedTinyInteger, uuid. 

<a name="renaming-columns"></a>
#### Renaming Columns
#### 컬럼의 이름 변경하기

To rename a column, you may use the `renameColumn` method on the Schema builder. Before renaming a column, be sure to add the `doctrine/dbal` dependency to your `composer.json` file:

컬럼의 이름을 변경하기 위해서, 스키마 빌더에 `renameColumn` 메소드를 사용할 수 있습니다. 컬럼의 이름을 바꾸기 전에 반드시 `composer.json` 파일에 `doctrine/dbal` 의존성을 추가하십시오:

    Schema::table('users', function (Blueprint $table) {
        $table->renameColumn('from', 'to');
    });

> {note} Renaming any column in a table that also has a column of type `enum` is not currently supported.

> {note} 테이블의 `enum` 타입을 가진 컬럼의 이름을 바꾸는 것은 현재 지원되지 않습니다.

<a name="dropping-columns"></a>
### Dropping Columns
### 컬럼 삭제하기

To drop a column, use the `dropColumn` method on the Schema builder. Before dropping columns from a SQLite database, you will need to add the `doctrine/dbal` dependency to your `composer.json` file and run the `composer update` command in your terminal to install the library:

컬럼을 삭제하기 위해서는, 스키마 빌더에서 `dropColumn` 메소드를 사용하면 됩니다. SQLite 데이터베이스에서 컬럼을 삭제하기 전에, `composer.json` 파일에 `doctrine/dbal` 의존성을 추가하고 라이브러리를 설치하기 위해서 터미널에서 `composer update` 명령어를 실행할 필요가 있습니다:

    Schema::table('users', function (Blueprint $table) {
        $table->dropColumn('votes');
    });

You may drop multiple columns from a table by passing an array of column names to the `dropColumn` method:

`dropColumn` 메소드에 컬럼 이름들의 배열을 전달하면 테이블에서 여러 개의 컬럼을 없앨 수 있습니다:

    Schema::table('users', function (Blueprint $table) {
        $table->dropColumn(['votes', 'avatar', 'location']);
    });

> {note} Dropping or modifying multiple columns within a single migration while using a SQLite database is not supported.

> {note} SQLite 데이터베이스를 사용하는 중에는, 하나의 마이그레이션 안에서 여러 개의 컬럼을 없애거나 수정할 수 없습니다.

<a name="indexes"></a>
## Indexes
## 인덱스

<a name="creating-indexes"></a>
### Creating Indexes
### 인덱스 생성하기

The schema builder supports several types of indexes. First, let's look at an example that specifies a column's values should be unique. To create the index, we can simply chain the `unique` method onto the column definition:

스키마 빌더는 여러 타입의 인덱스를 지원합니다. 우선 컬럼의 값이 유니크 해야 함을 지정하는 예를 살펴보겠습니다. 인덱스를 생성하려면 간단하게 컬럼의 정의에서 `unique` 메소드를 체이닝 하면 됩니다:

    $table->string('email')->unique();

Alternatively, you may create the index after defining the column. For example:

위와 같이 하는 대신에, 컬럼을 정의한 뒤에 인덱스를 생성할 수도 있습니다. 예를 들어:

    $table->unique('email');

You may even pass an array of columns to an index method to create a compound index:

인덱스 메소드에 컬럼의 배열을 전달하여 복합 인덱스를 생성할 수도 있습니다:

    $table->index(['account_id', 'created_at']);

Laravel will automatically generate a reasonable index name, but you may pass a second argument to the method to specify the name yourself:

라라벨은 자동으로 알맞은 인덱스 이름을 생성할 것입니다만, 메소드의 두번째 인자로 인덱스 이름을 지정할 수도 있습니다:

    $table->index('email', 'my_index_name');

#### Available Index Types
#### 사용가능한 인덱스 타입

Command  | Description
------------- | -------------
`$table->primary('id');`  |  Add a primary key.
`$table->primary(['first', 'last']);`  |  Add composite keys.
`$table->unique('email');`  |  Add a unique index.
`$table->unique('state', 'my_index_name');`  |  Add a custom index name.
`$table->unique(['first', 'last']);`  |  Add a composite unique index.
`$table->index('state');`  |  Add a basic index.

커맨드  | 설명
------------- | -------------
`$table->primary('id');`  |  프라이머리 키 추가. 
`$table->primary(['first', 'last']);`  |   복합 키 추가.
`$table->unique('email');`  |  유니크 인덱스 추가.
`$table->unique('state', 'my_index_name');`  |  인덱스의 이름을 지정하기
`$table->unique(['first', 'last']);`  |  복합키를 유니크 인덱스로 지정하기
`$table->index('state');`  |  기본적인 인덱스 추가.

<a name="dropping-indexes"></a>
### Dropping Indexes
### 인덱스 삭제하기

To drop an index, you must specify the index's name. By default, Laravel automatically assigns a reasonable name to the indexes. Simply concatenate the table name, the name of the indexed column, and the index type. Here are some examples:

인덱스를 삭제하기 위해서는 인덱스의 이름을 지정해야 합니다. 라라벨은 자동으로 인덱스에 합리적인 이름을 부여하도록 설정되어 있습니다. 간단하게는 테이블 이름, 인덱스된 컬럼의 이름, 그리고 인덱스 타입을 합친것입니다. 다음은 몇 개의 예제 입니다:

Command  | Description
------------- | -------------
`$table->dropPrimary('users_id_primary');`  |  Drop a primary key from the "users" table.
`$table->dropUnique('users_email_unique');`  |  Drop a unique index from the "users" table.
`$table->dropIndex('geo_state_index');`  |  Drop a basic index from the "geo" table.

명령어  | 설명
------------- | -------------
`$table->dropPrimary('users_id_primary');`  |  "users" 테이블에서 프라이머리 키 지우기.
`$table->dropUnique('users_email_unique');`  |  "users" 테이블에서 유니크 인덱스 지우기.
`$table->dropIndex('geo_state_index');`  |  "geo" 테이블에서 기본적인 인덱스 지우기.

If you pass an array of columns into a method that drops indexes, the conventional index name will be generated based on the table name, columns and key type:

인덱스들을 삭제하기 위해서 메소드에 컬럼의 배열을 전달하게 되면 인덱스의 이름은 테이블명, 컬럼이름 그리고 키의 타입을 기반으로 컨벤션에 의해서 인덱스 이름을 추정할 것입니다:

    Schema::table('geo', function (Blueprint $table) {
        $table->dropIndex(['state']); // Drops index 'geo_state_index'
    });

<a name="foreign-key-constraints"></a>
### Foreign Key Constraints
### 외래키 제한

Laravel also provides support for creating foreign key constraints, which are used to force referential integrity at the database level. For example, let's define a `user_id` column on the `posts` table that references the `id` column on a `users` table:

라라벨은 데이터베이스에서 또한 참조 무결성을 강제하는 외래 키 제한을 생성하는 것을 제공합니다. 예를 들어 `users` 테이블의 `id` 컬럼을 참조하는 `posts` 테이블에 `user_id` 컬럼을 정의해보겠습니다:

    Schema::table('posts', function (Blueprint $table) {
        $table->integer('user_id')->unsigned();

        $table->foreign('user_id')->references('id')->on('users');
    });

You may also specify the desired action for the "on delete" and "on update" properties of the constraint:

제한(constraint)의 "on delete"와 "on update" 속성에 원하는 동작을 지정할 수도 있습니다:

    $table->foreign('user_id')
          ->references('id')->on('users')
          ->onDelete('cascade');

To drop a foreign key, you may use the `dropForeign` method. Foreign key constraints use the same naming convention as indexes. So, we will concatenate the table name and the columns in the constraint then suffix the name with "_foreign":

외래 키를 지우기 위해서는 `dropForeign` 메소드를 사용할 수 있습니다. 외래 키 제한은 인덱스와 같은 명명 규칙을 사용합니다. 따라서 테이블 이름과 제한(constraint)의 컬럼들을 합치고 뒤에 "_foreign"을 붙일 것입니다:

    $table->dropForeign('posts_user_id_foreign');

Or, you may pass an array value which will automatically use the conventional constraint name when dropping:

또는 배열 값을 전달하면 삭제시 자동으로 컨벤션 규칙에 따르는 이름이 사용됩니다:

    $table->dropForeign(['user_id']);

You may enable or disable foreign key constraints within your migrations by using the following methods:

마이그레이션에서 다음의 메소드들을 사용하여 외래키 제약을 활성/비활성 할 수 있습니다:

    Schema::enableForeignKeyConstraints();

    Schema::disableForeignKeyConstraints();
