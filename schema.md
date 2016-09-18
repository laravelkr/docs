# 스키마 빌더(Schema Builder)

- [소개](#introduction)
- [테이블 생성 & 삭제](#creating-and-dropping-tables)
- [컬럼 추가](#adding-columns)
- [컬럼 변경](#changing-columns)
- [컬럼 이름 변경](#renaming-columns)
- [컬럼 삭제](#dropping-columns)
- [존재 유무 확인](#checking-existence)
- [인덱스 추가](#adding-indexes)
- [외래키](#foreign-keys)
- [인덱스 삭제](#dropping-indexes)
- [Timestamps와 Soft Deletes 컬럼 삭제](#dropping-timestamps)
- [스토리지 엔진](#storage-engines)

<a name="introduction"></a>
## 소개

라라벨의 `Schema` 클래스는 데이터베이스에 의존하지 않는 테이블 조작 방법을 제공합니다. 이 기능은 라라벨이 지원하는 모든 데이터베이스에서 잘 동작하며 공통의 API를 가지고 있다는 것을 의미합니다.

<!--chak-comment-스키마-빌더(Schema-Builder)-소개-->

<a name="creating-and-dropping-tables"></a>
## 테이블 생성 & 삭제

새로운 데이터베이스 테이블을 생성하기 위해서는 `Schema::create` 메소드를 사용합니다:

	Schema::create('users', function($table)
	{
		$table->increments('id');
	});

`create` 메소드의 첫 번째 인자는 테이블 이름을 나타내고 두 번째 인자는 새로운 테이블을 정의하는 데 사용되는 `Blueprint` 객체를받는 `Closure`입니다.

존재하는 데이터베이스 테이블 이름을 바꾸기 위해서는 `rename` 메소드를 사용하면 됩니다:

	Schema::rename($from, $to);

스키마 작업이 진행될 위치를 지정하고자 한다면 `Schema::connection` 메소드를 사용하면 됩니다:

	Schema::connection('foo')->create('users', function($table)
	{
		$table->increments('id');
	});

테이블을 삭제하려면 `Scheme::drop` 메소드를 사용하면 됩니다:

	Schema::drop('users');

	Schema::dropIfExists('users');

<!--chak-comment-스키마-빌더(Schema-Builder)-테이블-생성---삭제-->

<a name="adding-columns"></a>
## 컬럼 추가하기

존재하는 테이블을 수정하라면 `Schema::table` 메소드를 사용하게 될것입니다:

	Schema::table('users', function($table)
	{
		$table->string('email');
	});

테이블 빌더에는 테이블을 만들 때 사용할 수 있는 다양한 컬럼의 종류들을 가지고 있습니다:

명령어 |  설명
------------- | -------------
`$table->bigIncrements('id');`  |   증가 ID를 “big integer”로 하는것
`$table->bigInteger('votes');`  |  테이블에 BIGINT 컬럼을 지정하는것
`$table->binary('data');`  |  테이블에 BLOB 컬럼 지정
`$table->boolean('confirmed');`  |  테이블에 BOOLEAN 컬럼 지정
`$table->char('name', 4);`  |  길이를 지정한 CHAR 타입을 지정
`$table->date('created_at');`  |  테이블에 DATE 타입 지정
`$table->dateTime('created_at');`  |  테이블에 DATETIME 타입 지정
`$table->decimal('amount', 5, 2);`  |  유효성과 크기 지정한 DECIMAL 타입 지정
`$table->double('column', 15, 8);`  |  전체 15자리에 소수점 8자리까지 허용가능한 DOUBLE 타입 지정
`$table->enum('choices', ['foo', 'bar']);` | 테이블에 ENUM 타입 지정
`$table->float('amount');`  |  테이블에 FLOAT 타입 지정
`$table->increments('id');`  |  테이블에 (primary key) ID 증가시키기
`$table->integer('votes');`  |  테이블에 INTEGER 타입 지정
`$table->json('options');`  |  테이블에 JSON 타입 지정
`$table->jsonb('options');`  |  테이블에 JSONB 타입 지정
`$table->longText('description');`  |  테이블에 LONGTEXT 타입 지정
`$table->mediumInteger('numbers');`  |  테이블에  MEDIUMINT 타입 지정
`$table->mediumText('description');`  |  테이블에 MEDIUMTEXT 타입 지정
`$table->morphs('taggable');`  |  INTERGER의 `taggable_id`와 문자열 `taggable_type`타입 추가
`$table->nullableTimestamps();`  |  NULL값을 허용하는것 이외에 `timestamps()`와 동일합니다
`$table->smallInteger('votes');`  |  테이블에 SMALLINT 지정
`$table->tinyInteger('numbers');`  |  테이블에 TINYINT 지정
`$table->softDeletes();`  |  soft deletes을 위한 **deleted_at** 컬럼 추가
`$table->string('email');`  |  VARCHAR 타입 지정
`$table->string('name', 100);`  |  길이를 지정한 VARCHAR 타입 지정
`$table->text('description');`  |  테이블에 TEXT 타입 지정
`$table->time('sunrise');`  |  테이블에 TIME 타입 지정
`$table->timestamp('added_on');`  |  테이블에 TIMESTAMP 타입 지정
`$table->timestamps();`  |  **created_at**과 **updated_at** 컬럼 추가
`$table->rememberToken();`  |  VARCHAR(100) NULL의 `remember_token` 추가
`->nullable()`  |   컬럼에 NULL 허용
`->default($value)`  |  컬럼의 기본값 설정
`->unsigned()`  |  INTEGER를 UNSIGNED로 지정

#### Mysql에서 After 사용하기

만약 MySQL 데이터베이스를 사용하고 있다면 컬럼의 순서지정을 위해서 `after` 메소드를 사용할 수 있습니다:

	$table->string('name')->after('email');

<!--chak-comment-스키마-빌더(Schema-Builder)-컬럼-추가하기-->

<a name="changing-columns"></a>
## 컬럼 변경

** 주의:** 컬럼을 변경하기 전에 `composer.json`에 `doctrine/dbal` 의존 패키지를 추가했는지 확인하십시오.

때로는 이미 생성된 컬럼을 수정해야 할 필요가 있습니다. 예를 들어, 문자열 컬럼의 사이즈를 늘리고자 할 경우가 그렇습니다. `change` 메소드가 이 역할을 수행합니다! 예를 들어, 다음 예제는 `name` 컬럼의 사이즈를 25에서 50으로 늘려줍니다:

	Schema::table('users', function($table)
	{
		$table->string('name', 50)->change();
	});

또한, 다음처럼 컬럼에 Null 값이 가능하게 변경할 수도 있습니다:

	Schema::table('users', function($table)
	{
		$table->string('name', 50)->nullable()->change();
	});

<!--chak-comment-스키마-빌더(Schema-Builder)-컬럼-변경-->

<a name="renaming-columns"></a>
## 컬럼 이름 변경

컬럼의 이름을 변경하기 위해서는 스키마 빌더에서 `renameColumn` 메소드를 사용하면 됩니다. 컬럼이름을 변경하기 전에 `composer.json`에 `doctrine/dbal` 의존성을 추가했는지 확인하십시오.

(역자주 컬럼 이름 변경을 사용하기 위해서는 composer.json의 require에 doctrine/dbal을 추가해서 composer를 업데이트 해준 상태여야 합니다.)

	Schema::table('users', function($table)
	{
		$table->renameColumn('from', 'to');
	});

> **주의 :** 현재 `enum` 컬럼 타입의 이름을 변경하는 것은 지원되지 않습니다.

<!--chak-comment-스키마-빌더(Schema-Builder)-컬럼-이름-변경-->

<a name="dropping-columns"></a>
## 컬럼 삭제

컬럼을 삭제하고자 한다면 스키마 빌더에서 `dropColumn` 메소드를 사용하면 됩니다. 앞서 컬럼 이름 변경과 마찬가지로 컬럼 삭제 작업을 수행하기 전에 `composer.json`에 `doctrine/dbal` 의존성을 추가해주어야 합니다.

#### 데이터베이스 테이블에서 컬럼 삭제하기

	Schema::table('users', function($table)
	{
		$table->dropColumn('votes');
	});

#### 데이터베이스 테이블에서 다수의 컬럼 삭제하기

	Schema::table('users', function($table)
	{
		$table->dropColumn(['votes', 'avatar', 'location']);
	});

<!--chak-comment-스키마-빌더(Schema-Builder)-컬럼-삭제-->

<a name="checking-existence"></a>
## 존재 유무 확인

#### 테이블이 존재하는지 확인하기

테이블이나 컬럼이 존재하는지 확인하는 것은 `hasTable`과 `hasColumn` 메소드르 사용하여 쉽게 확인할 수 있습니다:

	if (Schema::hasTable('users'))
	{
		//
	}

#### 컬럼이 존재하는지 확인하기

	if (Schema::hasColumn('users', 'email'))
	{
		//
	}

<!--chak-comment-스키마-빌더(Schema-Builder)-존재-유무-확인-->

<a name="adding-indexes"></a>
## 인덱스 추가하기

스키마 빌더는 여러 가지 타입의 인덱스들을 지원합니다. 인덱스를 추가하는 데에는 두 가지 방법이 있습니다. 첫 번째는 컬럼을 정의하면서 동시에 인덱스를 추가하는 것이고, 두 번째는 이를 분리해서 작업하는 것입니다:
	$table->string('email')->unique();

인덱스를 추가하는 것을 라인을 나눠서 할 수도 있습니다. 아래의 예는 사용할 수 있는 인덱스 타입들의 목록입니다:

명령어 | 설명
------------- | -------------
`$table->primary('id');`  |  Primary 키 추가
`$table->primary(['first', 'last']);`  |  복합키 추가
`$table->unique('email');`  |  유니크 인덱스 추가
`$table->index('state');`  |  기본 인덱스 추가

<!--chak-comment-스키마-빌더(Schema-Builder)-인덱스-추가하기-->

<a name="foreign-keys"></a>
## 외래키

라라벨은 테이블에 외래키 제약을 거는것도 지원합니다:

	$table->integer('user_id')->unsigned();
	$table->foreign('user_id')->references('id')->on('users');

이 예에서 `user_id` 컬럼이`users`테이블에 `id`컬럼을 참조한다는 것을 선언 하고 있습니다. 외래키 컬럼을 먼저 생성하는것을 잊지 마십시오!

또한, 외래키 옵션인 "on delete”와 "on update"에 대한 처리를 지정할 수 있습니다:

	$table->foreign('user_id')
          ->references('id')->on('users')
          ->onDelete('cascade');

외래키를 삭제하려면 `dropForeign` 메소드를 사용하면 됩니다. 다른 인덱스에서 사용되는 것과 유사한 인덱스 이름 명명 규칙이 외래 키에도 사용되고 있습니다:

	$table->dropForeign('posts_user_id_foreign');

> **주의:** 정수형 증분되는 값(incrementing integer)을 참조하여 외래키를 만들때 외래키 컬럼을 항상 `unsigned`로 만드는 것을 유의해야 합니다.

<!--chak-comment-스키마-빌더(Schema-Builder)-외래키-->

<a name="dropping-indexes"></a>
## 인덱스 삭제하기

인덱스를 삭제하려면 인덱스 이름을 지정해야 합니다. 라라벨은 기본적으로 인덱스에 자체적인 의미가 있는 이름을 할당한다. 간단하게 테이블 이름, 인덱스가 있는 컬럼의 이름, 인데스 타입을 연결한 것입니다. 몇 가지 예제입니다:

명령어 | 설명
------------- | -------------
`$table->dropPrimary('users_id_primary');`  |  “users” 테이블에서 Primary 키 삭제
`$table->dropUnique('users_email_unique');`  |  “users” 테이블에서 유니크 인덱스 삭제
`$table->dropIndex('geo_state_index');`  |  “geo” 테이블에서 기본 인덱스 삭제

<!--chak-comment-스키마-빌더(Schema-Builder)-인덱스-삭제하기-->

<a name="dropping-timestamps"></a>
## Timestamps와 softDeletes 컬럼 삭제

`timestamps`, `nullableTimestamps`, `softDeletes`타입의 컬럼을 삭제하려면 다음과 같은 메소드들을 사용합니다:

명령어| 설명
------------- | -------------
`$table->dropTimestamps();`  |  테이블에서 **created\_at**와 **updated\_at** 컬럼을 삭제
`$table->dropSoftDeletes();`  |  테이블에서 **deleted\_at** 컬럼을 삭제

<!--chak-comment-스키마-빌더(Schema-Builder)-Timestamps와-softDeletes-컬럼-삭제-->

<a name="storage-engines"></a>
## 스토리지 엔진

테이블에 스토리지 엔진을 지정하려면 스키마 빌더에서 `engine` 속성을 지정하면 됩니다:

    Schema::create('users', function($table)
    {
        $table->engine = 'InnoDB';

        $table->string('email');
    });

<!--chak-comment-스키마-빌더(Schema-Builder)-스토리지-엔진-->
