# Schema Builder 스키마 빌더

- [Introduction 소개](#introduction)
- [Creating & Dropping Tables 테이블 생성 & 삭제](#creating-and-dropping-tables)
- [Adding Columns 컬럼 추가](#adding-columns)
- [Changing Columns 컬럼 변경](#changing-columns)
- [Renaming Columns 컬럼 이름 변경](#renaming-columns)
- [Dropping Columns 컬럼 삭제](#dropping-columns)
- [Checking Existence 존재 유무 확인](#checking-existence)
- [Adding Indexes 인덱스 추가](#adding-indexes)
- [Foreign Keys 외래키](#foreign-keys)
- [Dropping Indexes 인덱스 삭제](#dropping-indexes)
- [Dropping Timestamps & Soft Deletes Timestamps 와 Soft Deletes 컬럼 삭제](#dropping-timestamps)
- [Storage Engines 스토리지 엔진](#storage-engines)

<a name="introduction"></a>
## Introduction 소개

The Laravel `Schema` class provides a database agnostic way of manipulating tables.  라라벨의 `Schema` 클래스는 데이터베이스에 의존하지 않는 테이블 조작 방법을 제공합니다. It works well with all of the databases supported by Laravel, and has a unified API across all of these systems. 이기능은 라라벨이 지원하는 모든 데이터베이스에서 잘 동작하며 공통의 API를 가지고 있다는 것을 의미합니다. 

<a name="creating-and-dropping-tables"></a>
## Creating & Dropping Tables 
## 테이블 생성 & 삭제

To create a new database table, the `Schema::create` method is used:
새로운 데이터베이스 테이블을 생성하기 위해서는 `Schema::create` 메소드를 사용합니다.

	Schema::create('users', function($table)
	{
		$table->increments('id');
	});

The first argument passed to the `create` method is the name of the table, and the second is a `Closure` which will receive a `Blueprint` object which may be used to define the new table. `create` 메소드의 첫번째 인자는 테이블 이름을 나타내고 두번째 인자는 새로운 테이블을 정의하는데 사용되는 `Blueprint` 객체를 받는 `Closure` 입니다. 

To rename an existing database table, the `rename` method may be used:
존재하는 데이터베이스 테이블 이름을 바꾸기 위해서는 `rename` 메소드를 사용하면 됩니다. 

	Schema::rename($from, $to);

To specify which connection the schema operation should take place on, use the `Schema::connection` method: 스키마 작업이 진행될 위치를 지정하고자 한다면 `Schema::connection` 메소드를 사용하면 됩니다. 

	Schema::connection('foo')->create('users', function($table)
	{
		$table->increments('id');
	});

To drop a table, you may use the `Schema::drop` method:
테이블을 삭제하려면 `Scheme::drop` 메소드를 사용하면 됩니다. 

	Schema::drop('users');

	Schema::dropIfExists('users');

<a name="adding-columns"></a>
## Adding Columns
## 컬럼 추가하기

To update an existing table, we will use the `Schema::table` method:
존재하는 테이블을 수정하라면 `Schema::table` 메소드를 사용하게 될것입니다. 

	Schema::table('users', function($table)
	{
		$table->string('email');
	});

The table builder contains a variety of column types that you may use when building your tables: 테이블 빌더에는 테이블을 만들 때 사용할 수 있는 다양한 컬럼의 종류들을 가지고 있습니다. 

Command  명령어 | Description 설명
------------- | -------------
`$table->bigIncrements('id');`  |  Incrementing ID using a "big integer" equivalent. 증가 ID 를 “big integer” 로 하는것 
`$table->bigInteger('votes');`  |  BIGINT equivalent to the table 테이블에 BIGINT 컬럼을 지정하는것
`$table->binary('data');`  |  BLOB equivalent to the table 테이블에 BLOB 컬럼 지정
`$table->boolean('confirmed');`  |  BOOLEAN equivalent to the table 테이블에 BOOLEAN 컬럼 지정
`$table->char('name', 4);`  |  CHAR equivalent with a length 길이 제한한 CHAR 타입을 지정
`$table->date('created_at');`  |  DATE equivalent to the table 테이블에 DATE 타입 지정
`$table->dateTime('created_at');`  |  DATETIME equivalent to the table 테이블에 DATETIME 타입 지정
`$table->decimal('amount', 5, 2);`  |  DECIMAL equivalent with a precision and scale 유효성과 크기 지정한 DECIMAL 타입 지정 
`$table->double('column', 15, 8);`  |  DOUBLE equivalent with precision, 15 digits in total and 8 after the decimal point 전체 15자리에 소수점 8자리까지 허용가능한 DOUBLE 타입 지정
`$table->enum('choices', ['foo', 'bar']);` | ENUM equivalent to the table 테이블에 ENUM 타입 지정
`$table->float('amount');`  |  FLOAT equivalent to the table 테이블에 FLOAT 타입 지정
`$table->increments('id');`  |  Incrementing ID to the table (primary key) 테이블에 (primary key) ID 증가시키기
`$table->integer('votes');`  |  INTEGER equivalent to the table 테이블에 INTEGER 타입 지정
`$table->json('options');`  |  JSON equivalent to the table 테이블에 JSON 타입 지정
`$table->jsonb('options');`  |  JSONB equivalent to the table 테이블에 JSONB 타입 지정
`$table->longText('description');`  |  LONGTEXT equivalent to the table 테이블에 LONGTEXT 타입 지정
`$table->mediumInteger('numbers');`  |  MEDIUMINT equivalent to the table 테이블에  MEDIUMINT 타입 지정
`$table->mediumText('description');`  |  MEDIUMTEXT equivalent to the table 테이블에 MEDIUMTEXT 타입 지정
`$table->morphs('taggable');`  |  Adds INTEGER `taggable_id` and STRING `taggable_type` INTERGER의 `taggable_id` 와 문자열 `taggable_type`타입 추가
`$table->nullableTimestamps();`  |  Same as `timestamps()`, except allows NULLs  NULL값을 허용하는것 이외에 `timestamps()` 와 동일합니다
`$table->smallInteger('votes');`  |  SMALLINT equivalent to the table 테이블에 SMALLINT 지정
`$table->tinyInteger('numbers');`  |  TINYINT equivalent to the table 테이블에 TINYINT 지정
`$table->softDeletes();`  |  Adds **deleted\_at** column for soft deletes soft deletes을 위한 **deleted_at** 컬럼 추가
`$table->string('email');`  |  VARCHAR equivalent column VARCHAR 타입 지정
`$table->string('name', 100);`  |  VARCHAR equivalent with a length 길이를 지정한 VARCHAR 타입 지정
`$table->text('description');`  |  TEXT equivalent to the table 테이블에 TEXT 타입 지정
`$table->time('sunrise');`  |  TIME equivalent to the table 테이블에 TIME 타입 지정
`$table->timestamp('added_on');`  |  TIMESTAMP equivalent to the table 테이블에 TIMESTAMP 타입 지정
`$table->timestamps();`  |  Adds **created\_at** and **updated\_at** columns **created_at** 과 **updated_at** 컬럼 추가
`$table->rememberToken();`  |  Adds `remember_token` as VARCHAR(100) NULL VARCHAR(100) NULL의 `remember_token` 추가
`->nullable()`  |  Designate that the column allows NULL values 컬럼에 NULL 허용
`->default($value)`  |  Declare a default value for a column 컬럼의 기본값 설정
`->unsigned()`  |  Set INTEGER to UNSIGNED INTEGER 를 UNSIGNED 로 지정

#### Using After On MySQL
#### Mysql 에서 After 사용하기

If you are using the MySQL database, you may use the `after` method to specify the order of columns: 만약 MySQL 데이터베이스를 사용하고 있다면 컬럼의 순서지정을 위해서 `after` 메소드를 사용할 수 있습니다. 

	$table->string('name')->after('email');

<a name="changing-columns"></a>
## Changing Columns
## 컬럼 변경

**Note 주의:** Before changing a column, be sure to add the `doctrine/dbal` dependency to your `composer.json` file. 컬럼을 변경하기 전에 `composer.json` 에 `doctrine/dbal` 의존 패키지를 추가했는지 확인하십시오. 

Sometimes you may need to modify an existing column. 때로는 이미 생성된 컬럼을 수정해야할 필요가 있습니다. For example, you may wish to increase the size of a string column. 예를들어 문자열 컬럼의 사이즈를 늘리고자 할 경우가 그렇습니다. The `change` method makes it easy! `change` 메소드가 이 역활을 수행합니다. For example, let's increase the size of the `name` column from 25 to 50: 예를들어 다음 예제는 `name` 컬럼의 사이즈를 25에서 50으로 늘려줍니다. 

	Schema::table('users', function($table)
	{
		$table->string('name', 50)->change();
	});

We could also modify a column to be nullable:
또한 다음처럼 컬럼에 Null 값이 가능하게 변경할 수도 있습니다. 

	Schema::table('users', function($table)
	{
		$table->string('name', 50)->nullable()->change();
	});

<a name="renaming-columns"></a>
## Renaming Columns
## 컬럼 이름 변경

To rename a column, you may use the `renameColumn` method on the Schema builder. 컬럼의 이름을 변경하기 위해서는 스키마 빌더에서 `renameColumn` 메소드를 사용하면 됩니다. Before renaming a column, be sure to add the `doctrine/dbal` dependency to your `composer.json` file. 컬럼이름을 변경하기 전에 `composer.json` 에 `doctrine/dbal` 의존성을 추가했는지 확인하십시오. 

(역자주 컬럼 이름 변경을 사용하기 위해서는 composer.json 의 require 에 doctrine/dbal 을 추가해서 composer 를 업데이트 해준 상태여야 합니다.)

	Schema::table('users', function($table)
	{
		$table->renameColumn('from', 'to');
	});

> **Note주의 :** Renaming columns in a table with `enum` column is currently not supported. 현재 `enum` 컬럼 타입의 이름을 변경하는 것은 지원되지 않습니다. 

<a name="dropping-columns"></a>
## Dropping Columns
## 컬럼 삭제 

To drop a column, you may use the `dropColumn` method on the Schema builder. 컬럼을 삭제하고자 한다면 스키마 빌더에서 `dropColumn` 메소드를 사용하면 됩니다. Before dropping a column, be sure to add the `doctrine/dbal` dependency to your `composer.json` file. 앞서 컬럼 이름 변경과 마찬가지로 컬럼 삭제 작업을 수행하기 전에 `composer.json` 에 `doctrine/dbal` 의존성을 추가해주어야 합니다. 

#### Dropping A Column From A Database Table
데이터베이스 테이블에서 컬럼 삭제하기

	Schema::table('users', function($table)
	{
		$table->dropColumn('votes');
	});

#### Dropping Multiple Columns From A Database Table
데이터베이스 테이블에서 다수의 컬럼 삭제하기

	Schema::table('users', function($table)
	{
		$table->dropColumn(['votes', 'avatar', 'location']);
	});

<a name="checking-existence></a>
## Checking Existence
## 존재 유무 확인 

#### Checking For Existence Of Table
#### 테이블이 존재하는지 확인하기

You may easily check for the existence of a table or column using the `hasTable` and `hasColumn` methods: 테이블이나 컬럼이 존재하는지 확인하는 것은 `hasTable` 과 `hasColumn` 메소드르 사용하여 쉽게 확인할 수 있습니다. 

	if (Schema::hasTable('users'))
	{
		//
	}

#### Checking For Existence Of Columns
#### 컬럼이 존재하는지 확인하기

	if (Schema::hasColumn('users', 'email'))
	{
		//
	}

<a name="adding-indexes"></a>
## Adding Indexes
## 인덱스 추가하기

The schema builder supports several types of indexes. 스키마 빌더는 여러가지 타입의 인덱스들을 지원합니다. There are two ways to add them. 인덱스를 추가하는 데에는 두가지 방법이 있습니다. First, you may fluently define them on a column definition, or you may add them separately:
첫번째는 컬럼을 정의하면서 동시에 인덱스를 추가하는 것이고, 두번째는 이를 분리해서 작업하는 것입니다. 
	$table->string('email')->unique();

Or, you may choose to add the indexes on separate lines. Below is a list of all available index types:인덱스를 추가하는 것을 라인을 나눠서 할 수도 있습니다. 아래의 예는 사용할 수 있는 인덱스 타입들의 목록입니다. 

Command 명령어 | Description 설명
------------- | -------------
`$table->primary('id');`  |  Adding a primary key Primary 키 추가
`$table->primary(['first', 'last']);`  |  Adding composite keys 복합키 추가
`$table->unique('email');`  |  Adding a unique index 유니크 인덱스 추가
`$table->index('state');`  |  Adding a basic index 기본 인덱스 추가

<a name="foreign-keys"></a>
## Foreign Keys
## 외래키

Laravel also provides support for adding foreign key constraints to your tables:
라라벨은 테이블에 외래키 제약을 거는것도 지원합니다. 

	$table->integer('user_id')->unsigned();
	$table->foreign('user_id')->references('id')->on('users');

In this example, we are stating that the `user_id` column references the `id` column on the `users` table.  이 예에서 `user_id` 컬럼이`users`테이블에 `id`컬럼을 참조한다는 것을 선언 하고 있습니다. Make sure to create the foreign key column first! 외래키 컬럼을 먼저 생성하는것을 잊지 마십시오. 

You may also specify options for the "on delete" and "on update" actions of the constraint:
또한 외래키 옵션인 "on delete”와 "on update" 에 대한 처리를 지정할 수 있습니다.

	$table->foreign('user_id')
          ->references('id')->on('users')
          ->onDelete('cascade');

To drop a foreign key, you may use the `dropForeign` method. 외래키를 삭제하려면 `dropForeign` 메소드를 사용하면 됩니다. A similar naming convention is used for foreign keys as is used for other indexes: 다른 인덱스에서 사용되는 것과 유사한 인덱스 이름 명명 규칙이 외래 키에도 사용되고 있습니다.

	$table->dropForeign('posts_user_id_foreign');

> **Note 주의:** When creating a foreign key that references an incrementing integer, remember to always make the foreign key column `unsigned`. 정수형 증분되는 값(incrementing integer)을 참조하여 외래키를 만들때 외래키 컬럼을 항상 `unsigned`로 만드는 것을 유의해야 합니다.

<a name="dropping-indexes"></a>
## Dropping Indexes
## 인덱스 삭제하기

To drop an index you must specify the index's name. 인덱스를 삭제하려면 인덱스 이름을 지정해야 합니다. Laravel assigns a reasonable name to the indexes by default. 라라벨은 기본적으로 인덱스에 자체적인 의미가 있는 이름을 할당한다. Simply concatenate the table name, the names of the column in the index, and the index type. 간단하게 테이블 이름, 인덱스가 있는 컬럼의 이름, 인데스 타입을 연결한 것입니다. Here are some examples: 몇가지 예제입니다. 

Command  명령어 | Description 설명
------------- | -------------
`$table->dropPrimary('users_id_primary');`  |  Dropping a primary key from the "users" table “users” 테이블에서 Primary 키 삭제
`$table->dropUnique('users_email_unique');`  |  Dropping a unique index from the "users" table “users” 테이블에서 유니크 인덱스 삭제
`$table->dropIndex('geo_state_index');`  |  Dropping a basic index from the "geo" table “geo” 테이블에서 기본 인덱스 삭제

<a name="dropping-timestamps"></a>
## Dropping Timestamps & SoftDeletes
## Timestamps와 softDeletes 컬럼 삭제

To drop the `timestamps`, `nullableTimestamps` or `softDeletes` column types, you may use the following methods: `timestamps`, `nullableTimestamps`, `softDeletes`타입의 컬럼을 삭제하려면 다음과 같은 메소드들을 사용합니다. 

Command  명령어| Description 설명
------------- | -------------
`$table->dropTimestamps();`  |  Dropping the **created\_at** and **updated\_at** columns from the table 테이블에서 **created\_at** 와 **updated\_at** 컬럼을 삭제
`$table->dropSoftDeletes();`  |  Dropping **deleted\_at** column from the table 테이블에서 **deleted\_at** 컬럼을 삭제

<a name="storage-engines"></a>
## Storage Engines
## 스토리지 엔진

To set the storage engine for a table, set the `engine` property on the schema builder:
테이블에 스토리지 엔진을 지정하려면 스키마 빌더에서 `engine` 속성을 지정하면 됩니다. 

    Schema::create('users', function($table)
    {
        $table->engine = 'InnoDB';

        $table->string('email');
    });
