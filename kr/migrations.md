# Database: Migrations
# 데이터베이스: 마이그레이션

- [Introduction](#introduction)
- [시작하기](#introduction)
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
    - [Renaming Indexes](#renaming-indexes)
    - [인덱스 이름 변경하기](#renaming-indexes)
    - [Dropping Indexes](#dropping-indexes)
    - [인덱스 없애기](#dropping-indexes)
    - [Foreign Key Constraints](#foreign-key-constraints)
    - [외래키 제약](#foreign-key-constraints)

<a name="introduction"></a>
## Introduction
## 시작하기

Migrations are like version control for your database, allowing your team to modify and share the application's database schema. Migrations are typically paired with Laravel's schema builder to build your application's database schema. If you have ever had to tell a teammate to manually add a column to their local database schema, you've faced the problem that database migrations solve.

마이그레이션은 여러분의 팀에서 애플리케이션의 데이터베이스를 수정하고 데이터베이스 스키마를 공유할 수 있도록 해주며 데이터베이스를 위한 버전 컨트롤과 같은 역할을 합니다. 마이그레이션은 보통 라라벨의 스키마 빌더와 쌍을 이루어 애플리케이션의 데이터베이스 스키마를 손쉽게 만들 수 있습니다. 만약 팀 동료에게 로컬 데이터베이스 스키마에 수동으로 컬럼을 추가하는 것에 대해서 이야기를 해야한다면, 데이터베이스 마이그레이션 기능이 해결하고자 하는 문제에 직면해 있는 것입니다.

The Laravel `Schema` [facade](/docs/{{version}}/facades) provides database agnostic support for creating and manipulating tables across all of Laravel's supported database systems.

라라벨의 `Schema` [파사드](/docs/{{version}}/facades)는 라라벨이 지원하는 모든 데이터베이스 시스템을 지원하는 테이블을 생성과 조작에 대한 광범위한 지원을 제공합니다.

<a name="generating-migrations"></a>
## Generating Migrations
## 마이그레이션 파일 생성하기

To create a migration, use the `make:migration` [Artisan command](/docs/{{version}}/artisan):

마이그레이션 파일을 생성하기 위해서는 `make:migration` [아티즌 명령어](/docs/{{version}}/artisan)를 사용합니다.

    php artisan make:migration create_users_table

The new migration will be placed in your `database/migrations` directory. Each migration file name contains a timestamp, which allows Laravel to determine the order of the migrations.

새로운 마이그레이션 파일은 `database/migrations` 디렉토리에 생성됩니다. 각 마이그레이션 파일의 이름에는 타임스탬프를 포함하며 라라벨이 마이그레이션들의 순서를 판별할 수 있도록 합니다.

> {tip} Migration stubs may be customized using [stub publishing](/docs/{{version}}/artisan#stub-customization)

> {tip} [stub publishing](/docs/{{version}}/artisan#stub-customization)를 통해서 마이그레이션 stubs을 커스트마이징 할 수 있습니다. 

The `--table` and `--create` options may also be used to indicate the name of the table and whether or not the migration will be creating a new table. These options pre-fill the generated migration stub file with the specified table:

`--table`와 `--create` 옵션들은 테이블의 이름과 마이그레이션이 테이블을 새로 생성할지 여부를 표시하는 데 사용할 수 있습니다. 이 옵션들은 생성된 마이그레이션 stub 파일을 특정한 테이블로 미리 채워 놓습니다.

    php artisan make:migration create_users_table --create=users

    php artisan make:migration add_votes_to_users_table --table=users

If you would like to specify a custom output path for the generated migration, you may use the `--path` option when executing the `make:migration` command. The given path should be relative to your application's base path.

생성된 마이그레이션 파일이 생성될 출력 경로를 지정하고 싶다면 `make:migration` 명령어를 실행할 때 `--path` 옵션을 사용하면 됩니다. 주어진 경로는 애플리케이션의 베이스 경로로 부터 연관된 상대경로여야 합니다.

<a name="migration-structure"></a>
## Migration Structure
## 마이그레이션의 구조

A migration class contains two methods: `up` and `down`. The `up` method is used to add new tables, columns, or indexes to your database, while the `down` method should reverse the operations performed by the `up` method.

마이그레이션 클래스는 `up`과 `down`, 두 개의 메드를 가지고 있습니다. `up` 메소드는 데이터베이스에 테이블, 컬럼, 인덱스를 추가하는데 사용되고, 이와 반대로 `down` 메소드는 `up` 메소드의 동작을 취소합니다.

Within both of these methods you may use the Laravel schema builder to expressively create and modify tables. To learn about all of the methods available on the `Schema` builder, [check out its documentation](#creating-tables). For example, the following migration creates a `flights` table:

이 두 메소드 안에서 모두 라라벨 스키마 빌더를 사용하여 테이블을 다양한 방법을 통해서 생성하고 변경할 수 있습니다. `Schema` 빌더가 제공하는 모든 메소드에 대해 알아보려면 [문서](#creating-tables)을 확인하세요. 예를 들어, 다음 마이그레이션은 `flights` 테이블을 생성합니다.

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

<a name="running-migrations"></a>
## Running Migrations
## 마이그레이션 실행하기

To run all of your outstanding migrations, execute the `migrate` Artisan command:

아직 실행된적이 없는 모든 마이그레이션을 실행하려면, `migrate` 아티즌 명령어를 실행하면 됩니다.

    php artisan migrate

> {note} If you are using the [Homestead virtual machine](/docs/{{version}}/homestead), you should run this command from within your virtual machine.

> {note} 만약 [홈스테드 가상머신](/docs/{{version}}/homestead)을 사용중이라면, 이 명령어를 가상머신 안에서 실행해야 합니다.

#### Forcing Migrations To Run In Production
#### 실제 production 서버에서 강제로 마이그레이션 실행하기

Some migration operations are destructive, which means they may cause you to lose data. In order to protect you from running these commands against your production database, you will be prompted for confirmation before the commands are executed. To force the commands to run without a prompt, use the `--force` flag:

일부의 마이그레이션 작업은 구조의 변경이 있을 수 있으며, 이로 인해 데이터를 잃어 버릴 수도 있습니다. 이러한 위험들로 부터 실제 production 서버의 데이터베이스를 보호하기 위해서, 마이그레이션 명령어를 실행하기 전에는 확인 메시지가 출력됩니다. 확인 메시지 없이 명령어을 실행시키기 위해서는, `--force` 플래그를 사용하면 됩니다.

    php artisan migrate --force

<a name="rolling-back-migrations"></a>
### Rolling Back Migrations
### 마이그레이션 되돌리기-롤백

To roll back the latest migration operation, you may use the `rollback` command. This command rolls back the last "batch" of migrations, which may include multiple migration files:

가장 최근의 마이그레이션 작업(operation)을 되돌리려면 `rollback` 명령어를 사용하면 됩니다. 이 명령어는 가장 최근에 실행된 마이그레이션의 "모음(batch)", 즉 여러개의 마이그레이션 파일에 대해서 되돌릴 수 있습니다.

    php artisan migrate:rollback

You may roll back a limited number of migrations by providing the `step` option to the `rollback` command. For example, the following command will roll back the last five migrations:

`rollback` 명령어에 `step` 옵션을 전달하여 제한된 숫자의 마이그레이션만 되돌릴 수 있습니다. 예를들어 다음의 명령어는 최근 5개의 마이그레이션만 되돌립니다.

    php artisan migrate:rollback --step=5

The `migrate:reset` command will roll back all of your application's migrations:

`migrate:reset` 커맨드는 애플리케이션의 모든 마이그레이션을 되돌립니다.

    php artisan migrate:reset

#### Roll Back & Migrate Using A Single Command
#### 하나의 명령어로 롤백과 마이그레이트 함께 실행 하기

The `migrate:refresh` command will roll back all of your migrations and then execute the `migrate` command. This command effectively re-creates your entire database:

`migrate:refresh` 명령어는 모든 마이그레이션을 되돌리고, `migrate` 명령어를 실행합니다. 이 명령어는 전체 데이터베이스를 효과적으로 재생성합니다.

    php artisan migrate:refresh

    // Refresh the database and run all database seeds...
    php artisan migrate:refresh --seed

You may roll back & re-migrate a limited number of migrations by providing the `step` option to the `refresh` command. For example, the following command will roll back & re-migrate the last five migrations:

`refresh` 명령어에 `step` 옵션을 전달하여 제한된 숫자의 마이그레이션만 되돌리고 다시 마이그레이션을 수행할 수 있습니다. 예를들어 다음의 명령어는 최근 5개의 마이그레이션만 되돌리고 마이그레이션을 다시 실행합니다.

    php artisan migrate:refresh --step=5

#### Drop All Tables & Migrate
#### 모든 테이블과 마이그레이션 Drop

The `migrate:fresh` command will drop all tables from the database and then execute the `migrate` command:

`migrate:fresh` 명령어는 `migrate` 명령이 실행될 때 데이터베이스의 모든 테이블을 drop 합니다.

    php artisan migrate:fresh

    php artisan migrate:fresh --seed

<a name="tables"></a>
## Tables
## 테이블

<a name="creating-tables"></a>
### Creating Tables
### 테이블 생성하기

To create a new database table, use the `create` method on the `Schema` facade. The `create` method accepts two arguments: the first is the name of the table, while the second is a `Closure` which receives a `Blueprint` object that may be used to define the new table:

새로운 데이터베이스 테이블을 생성하려면 `Schema` 파사드에 `create` 메소드를 사용하면 됩니다. `create` 메소드는 두개의 인자를 전달 받습니다. 첫번째 인자는 테이블의 이름이고, 두번째 인자는 새로운 테이블을 정의하는 사용되는 `Blueprint` 객체를 받는 `Closure`입니다.

    Schema::create('users', function (Blueprint $table) {
        $table->id();
    });

When creating the table, you may use any of the schema builder's [column methods](#creating-columns) to define the table's columns.

테이블을 생성할 때, 테이블의 컬럼을 정의하기 위하여 자유롭게 스키마 빌더의 [컬럼 메소드](#creating-columns)를 사용할 수 있습니다.

#### Checking For Table / Column Existence
#### 테이블 / 컬럼이 존재하는지 확인하기

You may check for the existence of a table or column using the `hasTable` and `hasColumn` methods:

`hasTable`와 `hasColumn` 메소드를 사용해 테이블이나 컬럼의 존재 유무를 확인할 수 있습니다.

    if (Schema::hasTable('users')) {
        //
    }

    if (Schema::hasColumn('users', 'email')) {
        //
    }

#### Database Connection & Table Options
#### 데이터베이스 커넥션-connection & 테이블 옵션

If you want to perform a schema operation on a database connection that is not your default connection, use the `connection` method:

기본 커넥션-connection이 아닌 다른 데이터베이스 커넥션-connection에 스키마 작업을 수행하려면 `connection` 메소드를 사용하면 됩니다.

    Schema::connection('foo')->create('users', function (Blueprint $table) {
        $table->id();
    });

You may use the following commands on the schema builder to define the table's options:

테이블 옵션을 정의하기 위해서 스키마 빌더의 다음 명령어를 사용할 수 있습니다.

Command  |  Description
-------  |  -----------
`$table->engine = 'InnoDB';`  |  Specify the table storage engine (MySQL).
`$table->charset = 'utf8';`  |  Specify a default character set for the table (MySQL).
`$table->collation = 'utf8_unicode_ci';`  |  Specify a default collation for the table (MySQL).
`$table->temporary();`  |  Create a temporary table (except SQL Server).

명령어 | 설명
-------  |  -----------
`$table->engine = 'InnoDB';`  |  테이블 엔진을 지정합니다.(MySQL).
`$table->charset = 'utf8';`  |  테이블의 기본 케릭터셋을 지정합니다.(MySQL).
`$table->collation = 'utf8_unicode_ci';`  |  테이블의 기본 collation을 지정합니다.(MySQL).
`$table->temporary();`  |  임시 테이블을 생성 (SQL Server 제외).

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

테이블의 이름 변경하기 전에, 마이그레이션 파일에서 라라벨의 규약(Convention)에 따르는 기본 이름으로 지정되지 않고 고유한 이름을 붙인 외래 키 제약 조건이 존재하지 않는지 확인하십시오. 그렇지 않다면 외래 키 제약의 이름이 기존 테이블 이름을 참고하게 됩니다.

<a name="columns"></a>
## Columns
## 컬럼

<a name="creating-columns"></a>
### Creating Columns
### 컬럼 생성하기

The `table` method on the `Schema` facade may be used to update existing tables. Like the `create` method, the `table` method accepts two arguments: the name of the table and a `Closure` that receives a `Blueprint` instance you may use to add columns to the table:

이미 존재하는 테이블을 변경하기 하는데, `Schema` 파사드의 `table` 메소드를 사용할 수 있습니다. `create` 메소드와 같이 `table` 메소드도 두개읜 인자를 전달 받습니다. 하나는 테이블의 이름이고 ,다른 하나는 테이블에 컬럼을 추가하는데 사용할 수 있는 `Blueprint` 인스턴스를 받아들이는 `Closure`입니다.

    Schema::table('users', function (Blueprint $table) {
        $table->string('email');
    });

#### Available Column Types
#### 사용가능한 컬럼의 타입들

The schema builder contains a variety of column types that you may specify when building your tables:

스키마 빌더는 테이블을 만들 때 이를 지정할 수 있는 다양한 컬럼 타입들을 가지고 있습니다.

Command  |  Description
-------  |  -----------
`$table->id();`  |  Alias of `$table->bigIncrements('id')`.
`$table->foreignId('user_id');`  |  Alias of `$table->unsignedBigInteger('user_id')`.
`$table->bigIncrements('id');`  |  Auto-incrementing UNSIGNED BIGINT (primary key) equivalent column.
`$table->bigInteger('votes');`  |  BIGINT equivalent column.
`$table->binary('data');`  |  BLOB equivalent column.
`$table->boolean('confirmed');`  |  BOOLEAN equivalent column.
`$table->char('name', 100);`  |  CHAR equivalent column with a length.
`$table->date('created_at');`  |  DATE equivalent column.
`$table->dateTime('created_at', 0);`  |  DATETIME equivalent column with precision (total digits).
`$table->dateTimeTz('created_at', 0);`  |  DATETIME (with timezone) equivalent column with precision (total digits).
`$table->decimal('amount', 8, 2);`  |  DECIMAL equivalent column with precision (total digits) and scale (decimal digits).
`$table->double('amount', 8, 2);`  |  DOUBLE equivalent column with precision (total digits) and scale (decimal digits).
`$table->enum('level', ['easy', 'hard']);`  |  ENUM equivalent column.
`$table->float('amount', 8, 2);`  |  FLOAT equivalent column with a precision (total digits) and scale (decimal digits).
`$table->geometry('positions');`  |  GEOMETRY equivalent column.
`$table->geometryCollection('positions');`  |  GEOMETRYCOLLECTION equivalent column.
`$table->increments('id');`  |  Auto-incrementing UNSIGNED INTEGER (primary key) equivalent column.
`$table->integer('votes');`  |  INTEGER equivalent column.
`$table->ipAddress('visitor');`  |  IP address equivalent column.
`$table->json('options');`  |  JSON equivalent column.
`$table->jsonb('options');`  |  JSONB equivalent column.
`$table->lineString('positions');`  |  LINESTRING equivalent column.
`$table->longText('description');`  |  LONGTEXT equivalent column.
`$table->macAddress('device');`  |  MAC address equivalent column.
`$table->mediumIncrements('id');`  |  Auto-incrementing UNSIGNED MEDIUMINT (primary key) equivalent column.
`$table->mediumInteger('votes');`  |  MEDIUMINT equivalent column.
`$table->mediumText('description');`  |  MEDIUMTEXT equivalent column.
`$table->morphs('taggable');`  |  Adds `taggable_id` UNSIGNED BIGINT and `taggable_type` VARCHAR equivalent columns.
`$table->uuidMorphs('taggable');`  |  Adds `taggable_id` CHAR(36) and `taggable_type` VARCHAR(255) UUID equivalent columns.
`$table->multiLineString('positions');`  |  MULTILINESTRING equivalent column.
`$table->multiPoint('positions');`  |  MULTIPOINT equivalent column.
`$table->multiPolygon('positions');`  |  MULTIPOLYGON equivalent column.
`$table->nullableMorphs('taggable');`  |  Adds nullable versions of `morphs()` columns.
`$table->nullableUuidMorphs('taggable');`  |  Adds nullable versions of `uuidMorphs()` columns.
`$table->nullableTimestamps(0);`  |  Alias of `timestamps()` method.
`$table->point('position');`  |  POINT equivalent column.
`$table->polygon('positions');`  |  POLYGON equivalent column.
`$table->rememberToken();`  |  Adds a nullable `remember_token` VARCHAR(100) equivalent column.
`$table->set('flavors', ['strawberry', 'vanilla']);`  |  SET equivalent column.
`$table->smallIncrements('id');`  |  Auto-incrementing UNSIGNED SMALLINT (primary key) equivalent column.
`$table->smallInteger('votes');`  |  SMALLINT equivalent column.
`$table->softDeletes('deleted_at', 0);`  |  Adds a nullable `deleted_at` TIMESTAMP equivalent column for soft deletes with precision (total digits).
`$table->softDeletesTz('deleted_at', 0);`  |  Adds a nullable `deleted_at` TIMESTAMP (with timezone) equivalent column for soft deletes with precision (total digits).
`$table->string('name', 100);`  |  VARCHAR equivalent column with a length.
`$table->text('description');`  |  TEXT equivalent column.
`$table->time('sunrise', 0);`  |  TIME equivalent column with precision (total digits).
`$table->timeTz('sunrise', 0);`  |  TIME (with timezone) equivalent column with precision (total digits).
`$table->timestamp('added_on', 0);`  |  TIMESTAMP equivalent column with precision (total digits).
`$table->timestampTz('added_on', 0);`  |  TIMESTAMP (with timezone) equivalent column with precision (total digits).
`$table->timestamps(0);`  |  Adds nullable `created_at` and `updated_at` TIMESTAMP equivalent columns with precision (total digits).
`$table->timestampsTz(0);`  |  Adds nullable `created_at` and `updated_at` TIMESTAMP (with timezone) equivalent columns with precision (total digits).
`$table->tinyIncrements('id');`  |  Auto-incrementing UNSIGNED TINYINT (primary key) equivalent column.
`$table->tinyInteger('votes');`  |  TINYINT equivalent column.
`$table->unsignedBigInteger('votes');`  |  UNSIGNED BIGINT equivalent column.
`$table->unsignedDecimal('amount', 8, 2);`  |  UNSIGNED DECIMAL equivalent column with a precision (total digits) and scale (decimal digits).
`$table->unsignedInteger('votes');`  |  UNSIGNED INTEGER equivalent column.
`$table->unsignedMediumInteger('votes');`  |  UNSIGNED MEDIUMINT equivalent column.
`$table->unsignedSmallInteger('votes');`  |  UNSIGNED SMALLINT equivalent column.
`$table->unsignedTinyInteger('votes');`  |  UNSIGNED TINYINT equivalent column.
`$table->uuid('id');`  |  UUID equivalent column.
`$table->year('birth_year');`  |  YEAR equivalent column.

명령  | 설명
------------- | -------------
`$table->id();`  | `$table->bigIncrements('id')`의 별칭.
`$table->foreignId('user_id');`  |  `$table->unsignedBigInteger('user_id')`의 별칭.
`$table->bigIncrements('id');`  |  자동으로 증가하는(auto increment) UNSIGNED BIGINT (primary key) 컬럼.
`$table->bigInteger('votes');`  |  BIGINT 컬럼.
`$table->binary('data');`  |  BLOB 컬럼.
`$table->boolean('confirmed');`  |  BOOLEAN 컬럼.
`$table->char('name', 100);`  |  값을 길이로 하는 CHAR 컬럼.
`$table->date('created_at');`  |  DATE 컬럼.
`$table->dateTime('created_at', 0);`  |  정확도를 가지는 DATETIME과 동등한 컬럼(총 자릿수)
`$table->dateTimeTz('created_at', 0);`  | 정확도를 가지는 DATETIME (타임존 포함)과 동등한 컬럼 (총자릿수).
`$table->decimal('amount', 8, 2);`  |  전체 자릿수와 소수점 자릿수를 지정한 DECIMAL 컬럼.
`$table->double('amount', 8, 2);`  |  전체 자릿수와 소수점 자릿수를 지정한 DOUBLE 컬럼.
`$table->enum('level', ['easy', 'hard']);`  |  ENUM 컬럼.
`$table->float('amount', 8, 2);`  |  전체 자릿수와 소수점 자릿수를 지정한 FLOAT 컬럼.
`$table->geometry('positions');`  |  GEOMETRY 컬럼.
`$table->geometryCollection('positions');`  |  GEOMETRYCOLLECTION 컬럼.
`$table->increments('id');`  |  자동으로 증가하는(auto increment) UNSIGNED INTEGER (primary key) 컬럼.
`$table->integer('votes');`  |  INTEGER 컬럼.
`$table->ipAddress('visitor');`  |  IP 주소 컬럼.
`$table->json('options');`  |  JSON 컬럼.
`$table->jsonb('options');`  |  JSONB 컬럼.
`$table->lineString('positions');`  |  LINESTRING 컬럼.
`$table->longText('description');`  |  LONGTEXT 컬럼.
`$table->macAddress('device');`  |  MAC address 컬럼.
`$table->mediumIncrements('id');`  |  자동으로 증가하는(auto increment) UNSIGNED MEDIUMINT (primary key) 컬럼.
`$table->mediumInteger('votes');`  |  MEDIUMINT 컬럼.
`$table->mediumText('description');`  |  MEDIUMTEXT 컬럼.
`$table->morphs('taggable');`  |  UNSIGNED BIGINT 의 `taggable_id` 컬럼과 `taggable_type` VARCHAR 컬럼을 추가.
`$table->uuidMorphs('taggable');`  |  `taggable_id` CHAR (36) 컬럼과 `taggable_type` VARCHAR (255) UUID 컬럼을 추가.
`$table->multiLineString('positions');`  |  MULTILINESTRING 컬럼.
`$table->multiPoint('positions');`  |  MULTIPOINT 컬럼.
`$table->multiPolygon('positions');`  |  MULTIPOLYGON 컬럼.
`$table->nullableMorphs('taggable');`  |   `morphs()` 컬럼의 nullable (null 값이 허용되는) 버전.
`$table->nullableUuidMorphs('taggable');`  |   `uuidMorphs()` 컬럼의 nullable (null 값이 허용되는) 버전.
`$table->nullableTimestamps(0);`  |  `timestamps()` 의 별칭입니다.
`$table->point('position');`  |  POINT 컬럼.
`$table->polygon('positions');`  |  POLYGON 컬럼.
`$table->rememberToken();`  |  nullable (null 값이 허용되는) `remember_token` VARCHAR(100) 컬럼.
`$table->set('flavors', ['strawberry', 'vanilla']);`  |  동일한 컬럼으로 지정.
`$table->smallIncrements('id');`  |  자동으로 증가하는(auto increment) UNSIGNED SMALLINT (primary key) 컬럼.
`$table->smallInteger('votes');`  |  SMALLINT 컬럼.
`$table->softDeletes('deleted_at', 0);`  |  soft delete 를 위한 nullable (null 값이 허용되는) `deleted_at` TIMESTAMP 컬럼 (자릿수 포함).
`$table->softDeletesTz('deleted_at', 0);`  |  soft delete 를 위한 nullable (null 값이 허용되는) `deleted_at` (타임존을 포함한) TIMESTAMP 컬럼 (자릿수 포함)
`$table->string('name', 100);`  |  값을 길이로 하는 VARCHAR 컬럼.
`$table->text('description');`  |  TEXT 컬럼.
`$table->time('sunrise', 0);`  | 정확도를 가지는 TIME 컬럼 (자릿수 포함).
`$table->timeTz('sunrise', 0);`  | 정확도를 가지는 TIME (타임존을 가지는) 컬럼 (자릿수 포함).
`$table->timestamp('added_on', 0);`  | 정확도를 가지는 TIMESTAMP 컬럼 (자릿수 포함).
`$table->timestampTz('added_on', 0);`  | 정확도를 가지는 TIMESTAMP (타임존을 가지는) 컬럼 (자릿수 포함).
`$table->timestamps(0);`  | 정확도를 가지는 nullable (null 값이 허용되는) `created_at` 와 `updated_at` TIMESTAMP 컬럼 (자릿수 포함).
`$table->timestampsTz(0);`  | 정확도를 가지는 nullable (null 값이 허용되는) `created_at` 와 `updated_at` TIMESTAMP (타임존을 가지는) 컬럼 (자릿수 포함).
`$table->tinyIncrements('id');`  |  자동으로 증가하는(auto increment) UNSIGNED TINYINT (primary key) 컬럼.
`$table->tinyInteger('votes');`  |  TINYINT 컬럼.
`$table->unsignedBigInteger('votes');`  |  UNSIGNED BIGINT 컬럼.
`$table->unsignedDecimal('amount', 8, 2);`  |  전체 자릿수와 소수점 자릿수를 지정한 UNSIGNED DECIMAL 컬럼.
`$table->unsignedInteger('votes');`  |  UNSIGNED INTEGER 컬럼.
`$table->unsignedMediumInteger('votes');`  |  UNSIGNED MEDIUMINT 컬럼.
`$table->unsignedSmallInteger('votes');`  |  UNSIGNED SMALLINT 컬럼.
`$table->unsignedTinyInteger('votes');`  |  UNSIGNED TINYINT 컬럼.
`$table->uuid('id');`  |  UUID 컬럼.
`$table->year('birth_year');`  |  YEAR 컬럼.

<a name="column-modifiers"></a>
### Column Modifiers
### 컬럼 수정자(modifiers)

In addition to the column types listed above, there are several column "modifiers" you may use while adding a column to a database table. For example, to make the column "nullable", you may use the `nullable` method:

위 목록의 컬럼 타입들에 더하여, 데이터베이스 테이블에 컬럼을 추가하는데 사용할 수 있는 몇가지 컬럼 "modifier"가 존재합니다. 예를 들어, 컬럼을 "nullable" 하게 만들려면, `nullable` 메소드를 사용할 수 있습니다.

    Schema::table('users', function (Blueprint $table) {
        $table->string('email')->nullable();
    });

The following list contains all available column modifiers. This list does not include the [index modifiers](#creating-indexes):

다음은 사용 가능한 모든 컬럼 Modifier의 목록입니다. 이 목록은 [인덱스 modifiers](#creating-indexes)를 포함하지 않습니다.

Modifier  |  Description
--------  |  -----------
`->after('column')`  |  Place the column "after" another column (MySQL)
`->autoIncrement()`  |  Set INTEGER columns as auto-increment (primary key)
`->charset('utf8')`  |  Specify a character set for the column (MySQL)
`->collation('utf8_unicode_ci')`  |  Specify a collation for the column (MySQL/PostgreSQL/SQL Server)
`->comment('my comment')`  |  Add a comment to a column (MySQL/PostgreSQL)
`->default($value)`  |  Specify a "default" value for the column
`->first()`  |  Place the column "first" in the table (MySQL)
`->nullable($value = true)`  |  Allows (by default) NULL values to be inserted into the column
`->storedAs($expression)`  |  Create a stored generated column (MySQL)
`->unsigned()`  |  Set INTEGER columns as UNSIGNED (MySQL)
`->useCurrent()`  |  Set TIMESTAMP columns to use CURRENT_TIMESTAMP as default value
`->virtualAs($expression)`  |  Create a virtual generated column (MySQL)
`->generatedAs($expression)`  |  Create an identity column with specified sequence options (PostgreSQL)
`->always()`  |  Defines the precedence of sequence values over input for an identity column (PostgreSQL)

Modifier  | 설명
--------  |  -----------
`->after('column')`  |  컬럼을 다른 컬럼 "뒤"로 옮깁니다 (MySQL)
`->autoIncrement()`  |  INTEGER 컬럼을 자동으로 증가하는 (auto-increment) (primary key)로 지정합니다
`->charset('utf8')`  |  컬럼의 캐릭터셋을 지정합니다 (MySQL)
`->collation('utf8_unicode_ci')`  |  컬럼의 collation 지정합니다 (MySQL/PostgreSQL/SQL Server)
`->comment('my comment')`  |  컬럼에 코멘트 추가합니다 (MySQL/PostgreSQL)
`->default($value)`  |  컬럼의 "기본"값을 설정합니다
`->first()`  |  컬럼을 테이블의 "맨 처음" 위치로 옮깁니다 (MySQL)
`->nullable($value = true)`  |  컬럼에 NULL 값이 입력되는 것을 허용합니다(기본값)
`->storedAs($expression)`  |  stored generated 컬럼 생성하기 (MySQL)
`->unsigned()`  |  INTEGER 컬럼을 UNSIGNED 으로 지정 (MySQL)
`->virtualAs($expression)`  |  virtual generated 컬럼 생성하기 (MySQL)
`->generatedAs($expression)`  |  지정한 시퀀스 옵션을 사용하여 ID 컬럼 만들기 (PostgreSQL)
`->always()`  |  id 컬럼에 입력할 순차 값의 우선 순위를 정의합니다 (PostgreSQL)

(역자주 : generated column (virtual/stored) 는 mysql 5.7 부터 가능한 컬럼 유형으로 자세한 내용은 [mysql 공식 매뉴얼](https://dev.mysql.com/doc/refman/5.7/en/create-table-generated-columns.html)을 참고하십시오)

#### Default Expressions
#### 기본 표현식

The `default` modifier accepts a value or an `\Illuminate\Database\Query\Expression` instance. Using an `Expression` instance will prevent wrapping the value in quotes and allow you to use database specific functions. One situation where this is particularly useful is when you need to assign default values to JSON columns:

`default` 수정자-modifier는 값 또는 `\Illuminate\Database\Query\Expression` 인스턴스를 허용합니다. `Expression` 인스턴스를 사용하면 값을 따옴표로 묶는 것을 방지하고 데이터베이스 별 함수를 사용할 수 있습니다. 이것이 특히 유용한 상황 중 하나는 JSON 열에 기본값을 할당하는 것입니다.

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

> {note} Support for default expressions depends on your database driver, database version, and the field type. Please refer to the appropriate documentation for compatibility. Also note that using database specific functions may tightly couple you to a specific driver.

> {note} 기본 표현식의 지원은 데이터베이스 드라이버, 데이터베이스 버전 및 필드 유형에 따라 다릅니다. 호환성에 대해서는 관련 문서를 참조하십시오. 또한 데이터베이스 특정 기능을 사용하면 특정 드라이버에 밀접하게 연관될 수 있습니다.

<a name="modifying-columns"></a>
### Modifying Columns
### 컬럼 수정하기

#### Prerequisites
#### 사전 준비사항

Before modifying a column, be sure to add the `doctrine/dbal` dependency to your `composer.json` file. The Doctrine DBAL library is used to determine the current state of the column and create the SQL queries needed to make the required adjustments:

컬럼을 수정하기 전에, 꼭 `composer.json` 파일에 `doctrine/dbal` 의존성을 추가하십시오. Doctrine DBAL 라이브러리는 컬럼의 현재 상태를 확인하고 필요한 SQL 쿼리를 생성하기 위해 사용됩니다.

    composer require doctrine/dbal

#### Updating Column Attributes
#### 컬럼의 속성 변경하기

The `change` method allows you to modify type and attributes of existing columns. For example, you may wish to increase the size of a `string` column. To see the `change` method in action, let's increase the size of the `name` column from 25 to 50:

`change` 메소드는 이미 존재하는 컬럼 타입을 유형과 속성을 변경합니다. 예를 들어, `string` 컬럼의 사이즈를 늘이고 싶을 수 있습니다. `change` 메소드가 어떻게 작동하는지 `name` 컬럼 사이즈를 25에서 50으로 늘여서 확인해 보겠습니다.

    Schema::table('users', function (Blueprint $table) {
        $table->string('name', 50)->change();
    });

We could also modify a column to be nullable:

컬럼을 nullable로 수정할 수도 있습니다.

    Schema::table('users', function (Blueprint $table) {
        $table->string('name', 50)->nullable()->change();
    });

> {note} Only the following column types can be "changed": bigInteger, binary, boolean, date, dateTime, dateTimeTz, decimal, integer, json, longText, mediumText, smallInteger, string, text, time, unsignedBigInteger, unsignedInteger and unsignedSmallInteger.

> {note} 다음의 컬럼 타입들만 "변경" 할 수 있습니다. bigInteger, binary, boolean, date, dateTime, dateTimeTz, decimal, integer, json, longText, mediumText, smallInteger, string, text, time, unsignedBigInteger, unsignedInteger, unsignedSmallInteger.

#### Renaming Columns
#### 컬럼의 이름 변경하기

To rename a column, you may use the `renameColumn` method on the schema builder. Before renaming a column, be sure to add the `doctrine/dbal` dependency to your `composer.json` file:

컬럼의 이름을 변경하기 위해서, 스키마 빌더에 `renameColumn` 메소드를 사용할 수 있습니다. 컬럼의 이름을 바꾸기 전에 반드시 `composer.json` 파일에 `doctrine/dbal` 의존성을 추가하십시오:

    Schema::table('users', function (Blueprint $table) {
        $table->renameColumn('from', 'to');
    });

> {note} Renaming any column in a table that also has a column of type `enum` is not currently supported.

> {note} 테이블의 `enum` 타입을 가진 컬럼의 이름을 바꾸는 것은 현재 지원되지 않습니다.

<a name="dropping-columns"></a>
### Dropping Columns
### 컬럼 삭제하기

To drop a column, use the `dropColumn` method on the schema builder. Before dropping columns from a SQLite database, you will need to add the `doctrine/dbal` dependency to your `composer.json` file and run the `composer update` command in your terminal to install the library:

컬럼을 삭제하기 위해서는, 스키마 빌더에서 `dropColumn` 메소드를 사용하면 됩니다. SQLite 데이터베이스에서 컬럼을 삭제하기 전에, `composer.json` 파일에 `doctrine/dbal` 의존성을 추가하고 라이브러리를 설치하기 위해서 터미널에서 `composer update` 명령어를 실행할 필요가 있습니다.

    Schema::table('users', function (Blueprint $table) {
        $table->dropColumn('votes');
    });

You may drop multiple columns from a table by passing an array of column names to the `dropColumn` method:

`dropColumn` 메소드에 컬럼 이름들의 배열을 전달하면 테이블에서 여러 개의 컬럼을 없앨 수 있습니다.

    Schema::table('users', function (Blueprint $table) {
        $table->dropColumn(['votes', 'avatar', 'location']);
    });

> {note} Dropping or modifying multiple columns within a single migration while using a SQLite database is not supported.

> {note} SQLite 데이터베이스를 사용하는 중에는, 하나의 마이그레이션 안에서 여러 개의 컬럼을 없애거나 수정할 수 없습니다.

#### Available Command Aliases
#### 사용가능한 명령어 alias(별칭)

Command  |  Description
-------  |  -----------
`$table->dropMorphs('morphable');`  |  Drop the `morphable_id` and `morphable_type` columns.
`$table->dropRememberToken();`  |  Drop the `remember_token` column.
`$table->dropSoftDeletes();`  |  Drop the `deleted_at` column.
`$table->dropSoftDeletesTz();`  |  Alias of `dropSoftDeletes()` method.
`$table->dropTimestamps();`  |  Drop the `created_at` and `updated_at` columns.
`$table->dropTimestampsTz();` |  Alias of `dropTimestamps()` method.

명령어 |  설명
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

    $table->string('email')->unique();

Alternatively, you may create the index after defining the column. For example:

위와 같이 하는 대신에, 컬럼을 정의한 뒤에 인덱스를 생성할 수도 있습니다. 예를 들어:

    $table->unique('email');

You may even pass an array of columns to an index method to create a compound (or composite) index:

인덱스 메소드에 컬럼의 배열을 전달하여 결합(복합) 인덱스를 생성할 수도 있습니다.

    $table->index(['account_id', 'created_at']);

Laravel will automatically generate an index name based on the table, column names, and the index type, but you may pass a second argument to the method to specify the index name yourself:

라라벨은 테이블, 컬럼 이름 및 인덱스 유형을 기반으로 인덱스 이름을 자동으로 생성하지만 메소드의 두번째 인자로 인덱스 이름을 지정할 수도 있습니다.

    $table->unique('email', 'unique_email');

#### Available Index Types
#### 사용가능한 인덱스 타입

Each index method accepts an optional second argument to specify the name of the index. If omitted, the name will be derived from the names of the table and column(s) used for the index, as well as the index type.

각각의 인덱스 메소드는 두번쨰 인수를 통해 선택적으로 인덱스의 이름입력받습니다. 이 값이 생략된다면, 인덱스의 이름은 테이블, 컬럼과 인덱스의 타입을 기반으로 생성됩니다.

Command  |  Description
-------  |  -----------
`$table->primary('id');`  |  Adds a primary key.
`$table->primary(['id', 'parent_id']);`  |  Adds composite keys.
`$table->unique('email');`  |  Adds a unique index.
`$table->index('state');`  |  Adds a plain index.
`$table->spatialIndex('location');`  |  Adds a spatial index. (except SQLite)

커맨드  | 설명
-------  |  -----------
`$table->primary('id');`  |  primary key 추가.
`$table->primary(['id', 'parent_id']);`  |   복합 키 추가.
`$table->unique('email');`  |  유니크 인덱스 추가.
`$table->index('state');`  |  기본적인 인덱스 추가.
`$table->spatialIndex('location');`  |  공간(spatial) 인덱스 추가 (SQLite 제외.)

#### Index Lengths & MySQL / MariaDB
#### 인덱스 길이 & MySQL / MariaDB

Laravel uses the `utf8mb4` character set by default, which includes support for storing "emojis" in the database. If you are running a version of MySQL older than the 5.7.7 release or MariaDB older than the 10.2.2 release, you may need to manually configure the default string length generated by migrations in order for MySQL to create indexes for them. You may configure this by calling the `Schema::defaultStringLength` method within your `AppServiceProvider`:

라라벨은 기본적으로 "emojis"를 데이터베이스에 저장할 수 있는 `utf8mb4` 캐릭터셋을 사용합니다. 5.7.7 이전의 MySQL 이나 10.2.2 이전의 MariaDB를 사용하는 경우, MySQL이 인덱스를 구성할 수 있게 하기 위해서 마이그레이션으로 생성된 문자열 길이를 수동으로 조정해야 할 수도 있습니다. `AppServiceProvider` 파일 안에서 `Schema::defaultStringLength` 메소드를 호출하여 이를 설정할 수 있습니다.

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

To rename an index, you may use the `renameIndex` method. This method accepts the current index name as its first argument and the desired new name as its second argument:

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

명령어  | 설명
------------- | -------------
`$table->dropPrimary('users_id_primary');`  |  "users" 테이블에서 프라이머리 키 지우기.
`$table->dropUnique('users_email_unique');`  |  "users" 테이블에서 유니크 인덱스 지우기.
`$table->dropIndex('geo_state_index');`  |  "geo" 테이블에서 기본적인 인덱스 지우기.
`$table->dropSpatialIndex('geo_location_spatialindex');`  |  "geo" 테이블에서 공간(spatial) 인덱스 지우기(SQLite 제외).

If you pass an array of columns into a method that drops indexes, the conventional index name will be generated based on the table name, columns and key type:

인덱스들을 삭제하기 위해서 메소드에 컬럼의 배열을 전달하게 되면 인덱스의 이름은 테이블명, 컬럼이름 그리고 키의 타입을 기반으로 컨벤션에 의해서 인덱스 이름을 추정할 것입니다.

    Schema::table('geo', function (Blueprint $table) {
        $table->dropIndex(['state']); // Drops index 'geo_state_index'
    });

<a name="foreign-key-constraints"></a>
### Foreign Key Constraints
### 외래키 제약조건(Constraints)

Laravel also provides support for creating foreign key constraints, which are used to force referential integrity at the database level. For example, let's define a `user_id` column on the `posts` table that references the `id` column on a `users` table:

라라벨은 데이터베이스에서 또한 참조 무결성을 강제하는 외래 키 제약조건을 생성하는 것을 제공합니다. 예를 들어 `users` 테이블의 `id` 컬럼을 참조하는 `posts` 테이블에 `user_id` 컬럼을 정의해보겠습니다.

    Schema::table('posts', function (Blueprint $table) {
        $table->unsignedBigInteger('user_id');

        $table->foreign('user_id')->references('id')->on('users');
    });

Since this syntax is rather verbose, Laravel provides additional, terser methods that use convention to provide a better developer experience. The example above could be written like so:

이 구문은 다소 장황하므로 라라벨은 더 나은 개발자 경험을 제공하기 위해 규칙을 사용하는 추가의 terser 메소드를 제공합니다. 위의 예제는 다음과 같이 작성할 수 있습니다.

    Schema::table('posts', function (Blueprint $table) {
        $table->foreignId('user_id')->constrained();
    });

The `foreignId` method is an alias for `unsignedBigInteger` while the `constrained` method will use convention to determine the table and column name being referenced.

`foreignId` 메소드는 `unsignedBigInteger`의 별명 인 반면, `constrained` 메소드는 참조되는 테이블 및 컬럼 이름을 판별하기 위해 규칙을 사용합니다.

You may also specify the desired action for the "on delete" and "on update" properties of the constraint:

제약조건(constraint)의 "on delete"와 "on update" 속성에 원하는 동작을 지정할 수도 있습니다.

    $table->foreignId('user_id')
          ->constrained()
          ->onDelete('cascade');

To drop a foreign key, you may use the `dropForeign` method, passing the foreign key constraint to be deleted as an argument. Foreign key constraints use the same naming convention as indexes, based on the table name and the columns in the constraint, followed by a "\_foreign" suffix:

외래 키를 지우기 위해서는 `dropForeign` 메소드를 사용할 수 있습니다. 외래 키 제약조건(constraint)은 인덱스와 같은 명명 규칙을 사용합니다. 따라서 테이블 이름과 제약조건(constraint)의 컬럼들을 합치고 뒤에 "_foreign"을 붙일 것입니다.

    $table->dropForeign('posts_user_id_foreign');

Alternatively, you may pass an array containing the column name that holds the foreign key to the `dropForeign` method. The array will be automatically converted using the constraint name convention used by Laravel's schema builder:

또는 외래 키를 포함하는 컬럼 이름이 포함 된 배열을 `dropForeign` 메서드에 전달할 수 있습니다. 배열은 라라벨의 스키마 빌더가 사용하는 제약 조건 이름 규칙을 사용하여 자동으로 변환됩니다.

    $table->dropForeign(['user_id']);

You may enable or disable foreign key constraints within your migrations by using the following methods:

마이그레이션에서 다음의 메소드들을 사용하여 외래키 제약조건을 활성/비활성 할 수 있습니다.

    Schema::enableForeignKeyConstraints();

    Schema::disableForeignKeyConstraints();

> {note} SQLite disables foreign key constraints by default. When using SQLite, make sure to [enable foreign key support](/docs/{{version}}/database#configuration) in your database configuration before attempting to create them in your migrations. In addition, SQLite only supports foreign keys upon creation of the table and [not when tables are altered](https://www.sqlite.org/omitted.html). 

> {note} SQLite는 기본적으로 외래 키 제약 조건을 비활성화합니다. SQLite를 사용하는 경우, 마이그레이션에서 데이터베이스 설정을 작성하기 전에 데이터베이스 설정에서 [외래 키 지원](/docs/{{version}}/database#configuration)을 사용해야합니다. 또한 SQLite는 테이블 생성시 및 [테이블이 변경되지 않을 때](https://www.sqlite.org/omitted.html) 외래 키만 지원합니다.
