# 데이터베이스: 마이그레이션

- [시작하기](#introduction)
- [마이그레이션 파일 생성하기](#generating-migrations)
- [마이그레이션의 구조](#migration-structure)
- [마이그레이션 실행하기](#running-migrations)
    - [마이그레이션 되돌리기](#rolling-back-migrations)
- [마이그레이션 작성하기](#writing-migrations)
    - [테이블 생성하기](#creating-tables)
    - [테이블의 이름변경 / 제거](#renaming-and-dropping-tables)
    - [컬럼 생성하기](#creating-columns)
    - [컬럼 수정하기](#modifying-columns)
    - [컬럼 삭제하기](#dropping-columns)
    - [인덱스 생성하기](#creating-indexes)
    - [인덱스 없애기](#dropping-indexes)
    - [외래키 제약](#foreign-key-constraints)

<a name="introduction"></a>
## 시작하기

마이그레이션은 한 팀에서 손쉽게 애플리케이션의 데이터베이스를 수정하고 데이터베이스 스키마를 공유할 수 있도록 해주며 데이터베이스를 위한 버전 컨트롤과 같은 역할을 합니다. 마이그레이션은 보통 라라벨의 스키마 빌더와 쌍을 이루어 애플리케이션의 데이터베이스 스키마를 손쉽게 만들 수 있습니다.

라라벨 `Schema` [파사드](/docs/{{version}}/facades)는 테이블을 생성하고 조작하기 위해 데이터베이스에 광범위한 지원을 제공합니다. 라라벨이 지원하는 데이터베이스 시스템 모두에 대해서 유연하고, 다양한 표현이 가능한 일관된 API를 사용합니다.

<a name="generating-migrations"></a>
## 마이그레이션 생성하기

마이그레이션을 생성하기 위해서는 `make:migration` [아티즌 명령어](/docs/{{version}}/artisan)를 사용합니다:

    php artisan make:migration create_users_table

새로운 마이그레이션 파일은 `database/migrations` 디렉토리에 위치하게 됩니다. 각 마이그레이션 파일의 이름에는 타임스탬프를 포함하여 라라벨이 마이그레이션들의 순서를 판별할 수 있도록 합니다.

`--table`와 `--create` 옵션들은 테이블의 이름과 마이그레이션이 테이블을 새로 생성할지에 대해 명시하는데 사용될 수 있습니다. 이 옵션들은 단순히 생성된 마이그레이션 stub 파일을 특정한 테이블로 미리 채워 놓습니다:

    php artisan make:migration add_votes_to_users_table --table=users

    php artisan make:migration create_users_table --create=users

생성된 마이그레이션 파일이 생성될 출력 경로를 지정하고 싶다면 `make:migration` 명령어를 실행할 때 `--path` 옵션을 사용하면 됩니다. 제공되는 경로는 애플리케이션의 기본 경로에 따라 결정됩니다:

<a name="migration-structure"></a>
## 마이그레이션의 구조

마이그레이션 클래스는 `up`과 `down`, 두 개의 메드를 가지고 있습니다: `up` 메소드는 데이터베이스에 테이블, 컬럼, 인덱스를 추가하는데 사용되는 반면 `down` 메소드는 단순히 `up` 메소드의 동작을 취소합니다.

이 두 메소드 안에서 모두 라라벨 스키마 빌더를 사용하여 테이블을 다양한 방법을 통해서 생성하고 변경할 수 있습니다. `Schema` 빌더가 제공하는 모든 메소드에 대해 알아보려면 [문서](#creating-tables)을 확인하세요. 다음의 예로, `flights` 테이블을 생성하는 마이그레이션 샘플을 살펴보겠습니다:

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
## 마이그레이션 실행하기

아직 실행되지 않은 모든 마이그레이션을 실행하려면 `migrate` 아티즌 커맨드를 사용하면 됩니다. [홈스테드 가상머신](/docs/{{version}}/homestead)을 사용하고 있다면 VM 안에서 다음 명령어를 실행하세요:

    php artisan migrate

마이그레이션 실행 시 "class not found" 오류가 나타난다면 `composer dump-autoload` 명령어를 실행하고 마이그레이트 명령어를 다시 실행하십시오.

#### 실제 제품 서버에서 강제로 마이그레이션 실행하기

일부의 마이그레이션 작업은 구조의 변경이 있을 수 있으며, 이로 인해 데이터를 잃어 버릴 수도 있습니다. 이러한 위험들로 부터 실제 제품 서버의 데이터베이스를 보호하기 위해서, 마이그레이션 명령어를 실행하기 전에는 확인 메시지가 출력됩니다. 확인 메시지 없이 명령어을 실행시키기 위해서는, `--force` 플래그를 사용하면 됩니다:

    php artisan migrate --force

<a name="rolling-back-migrations"></a>
### 마이그레이션 되돌리기

가장 최근의 마이그레이션 "작업(operation)"을 되돌리려면 `rollback` 명령어를 사용하면 됩니다. 이 행위는 가장 최근에 실행된 마이그레이션의 "모음(batch)", 즉 여러 마이그레이션 파일에 대해서 되돌릴 수 있다는 것에 유의하십시오:

    php artisan migrate:rollback

<a name="writing-migrations"></a>
## 마이그레이션 작성하기

<a name="creating-tables"></a>
### 테이블 생성하기

새로운 데이터베이스 테이블을 생성하려면 `Schema` 파사드에 `create` 메소드를 사용하면 됩니다. `create` 메소드는 두개의 인자를 전달 받습니다. 첫번째 인자는 테이블의 이름이고, 두번째 인자는 `Blueprint` 객체를 받아 새로운 테이블을 정의하는 `Closure`입니다:

    Schema::create('users', function (Blueprint $table) {
        $table->increments('id');
    });

물론 테이블을 생성할 때, 테이블의 컬럼을 정의하기 위하여 자유롭게 스키마 빌더의 [컬럼 메소드](#creating-columns)를 사용할 수 있습니다.

#### 테이블 / 컬럼이 존재하는지 확인하기

`hasTable`와 `hasColumn` 메소드를 사용하면 손쉽게 테이블이나 컬럼의 존재 유무를 확인할 수 있습니다:

    if (Schema::hasTable('users')) {
        //
    }

    if (Schema::hasColumn('users', 'email')) {
        //
    }

#### 커넥션-connection & 스토리지 엔진

기본 커넥션-connection이 아닌 다른 데이터베이스 커넥션-connection에 스키마 작업을 수행하려면 `connection` 메소드를 사용하면 됩니다:

    Schema::connection('foo')->create('users', function ($table) {
        $table->increments('id');
    });

테이블을 위해 스토리지 엔진을 설정하려면, 스키마 빌더에 `engine` 속성을 설정하십시오.

    Schema::create('users', function ($table) {
        $table->engine = 'InnoDB';

        $table->increments('id');
    });

<a name="renaming-and-dropping-tables"></a>
### 테이블 이름 변경 / 제거하기

이미 존재하는 테이블의 이름을 바꾸려면 `rename` 메소드를 이용하십시오:

    Schema::rename($from, $to);

이미 존재하는 테이블을 제거하려면 `drop`이나 `dropIfExists` 메소드를 사용하세요:

    Schema::drop('users');

    Schema::dropIfExists('users');

#### 외래 키를 가진 테이블의 이름 변경

테이블의 이름 변경하기 전에, 마이그레이션 파일에서 라라벨의 규약에 따르는 기본 이름으로 지정되지 않고 고유한 이름을 붙인 외래 키 제약 조건이 존재하지 않는지 확인하십시오. 그렇지 않다면 외래 키 제약의 이름이 기존 테이블 이름을 참고하게 됩니다.

<a name="creating-columns"></a>
### 컬럼 생성하기

이미 존재하는 테이블을 변경하기 위해서, `Schema` 파사드의 `table` 메소드를 사용할 것입니다. `create` 메소드와 같이 `table` 메소드도 두개읜 인자를 전달 받습니다: 하나는 테이블의 이름이고 ,다른 하나는 테이블에 컬럼을 추가하는데 사용할 수 있는 `Blueprint` 인스턴스를 받아들이는 `Closure`입니다:

    Schema::table('users', function ($table) {
        $table->string('email');
    });

#### 사용가능한 컬럼의 타입들

당연하게도, 스키마 빌더는 테이블을 만들 때 사용할 수 있는 다양한 컬럼 타입들을 가지고 있습니다.

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
`$table->nullableTimestamps();`  |  `timestamps()`와 동일하지만 NULL 허용.
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

#### 컬럼 Modifiers

위 목록의 컬럼 타입 이외에도, 컬럼을 추가할 때 사용할 수 있는 여러가지 컬럼 "modifier"가 존재합니다. 예를 들어, 컬럼을 "nullable" 하게 만들려면 `nullable` 메소드를 사용할 수 있습니다:

    Schema::table('users', function ($table) {
        $table->string('email')->nullable();
    });

아래는 사용 가능한 모든 컬럼 Modifier의 목록입니다. 이 목록은 [인덱스 modifiers](#creating-indexes)를 포함하지 않습니다:

Modifier  | 설명
------------- | -------------
`->first()`  |  테이블에 컬럼을 "맨 처음" 위치로 옮기세요 (MySQL의 경우에만)
`->after('column')`  |  컬럼을 다른 컬럼 "뒤"로 옮기세요 (MySQL의 경우에만)
`->nullable()`  |  NULL 값들이 컬럼에 입력되는 것을 허용합니다
`->default($value)`  |  컬럼의 "기본"값을 설정합니다
`->unsigned()`  | `UNSIGNED`에 `integer` 컬럼을 설정합니다
`->comment('my comment')`  |  컬럼에 코멘트 추가

<a name="changing-columns"></a>
<a name="modifying-columns"></a>
### 컬럼 수정하기

#### 요구사항

컬럼을 수정하기 전에, 꼭 `composer.json` 파일에 `doctrine/dbal` 의존성을 추가하십시오. Doctrine DBAL 라이브러리는 컬럼의 현재 상태를 확인하고 필요한 SQL 쿼리를 생성하여 컬럼에 지정된 변경사항을 수행하기 위해 사용됩니다.

#### 컬럼의 속성 변경하기

`change` 메소드는 이미 존재하는 컬럼을 새로운 타입으로 수정하거나 컬럼의 속성을 변경할 수 있게 해줍니다. 예를 들어, 문자열 컬럼의 사이즈를 늘이고 싶을 수 있습니다. `change` 메소드가 어떻게 작동하는지 `name` 컬럼 사이즈를 25에서 50으로 늘여서 확인해 보겠습니다:

    Schema::table('users', function ($table) {
        $table->string('name', 50)->change();
    });

컬럼을 nullable로 수정할 수도 있습니다:

    Schema::table('users', function ($table) {
        $table->string('name', 50)->nullable()->change();
    });

> **주의:** 테이블에서 `enum`, `json` 또는 `jsonb` 컬럼을 수정하는 것은 현재 지원하지 않습니다.

<a name="renaming-columns"></a>
#### 컬럼의 이름 변경하기

컬럼의 이름을 변경하기 위해서, 스키마 빌더에 `renameColumn` 메소드를 사용할 수 있습니다. 컬럼의 이름을 바꾸기 전에 반드시 `composer.json` 파일에 `doctrine/dbal` 의존성을 추가하십시오:

    Schema::table('users', function ($table) {
        $table->renameColumn('from', 'to');
    });

> **주의:** 테이블의 `enum` 컬럼의 이름을 바꾸는 것은 현재 지원되지 않습니다.

<a name="dropping-columns"></a>
### 컬럼 삭제하기

컬럼을 삭제하기 위해서는, 스키마 빌더에서 `dropColumn` 메소드를 사용하면 됩니다:

    Schema::table('users', function ($table) {
        $table->dropColumn('votes');
    });

`dropColumn` 메소드에 컬럼 이름들의 배열을 전달하면 테이블에서 여러 개의 컬럼을 없앨 수 있습니다:

    Schema::table('users', function ($table) {
        $table->dropColumn(['votes', 'avatar', 'location']);
    });

>**주의:** SQLite 데이터베이스의 컬럼을 없애려면, 먼저 `composer.json` 파일에 `doctrine/dbal` 의존성을 추가하고 터미널에서 `composer update` 명령어를 실행하여 라이브러리를 설치해야 합니다.

> **주의:** SQLite 데이터베이스를 사용하는 중에는, 하나의 마이그레이션 안에서 여러 개의 컬럼을 없애거나 수정할 수 없습니다.

<a name="creating-indexes"></a>
### 인덱스 생성하기

스키마 빌더는 여러 타입의 인덱스를 지원합니다. 우선 컬럼의 값이 유니크 해야 함을 지정하는 예를 살펴보겠습니다. 인덱스를 생성하려면 간단하게 컬럼의 정의에서 `unique` 메소드를 체이닝 하면 됩니다:

    $table->string('email')->unique();

위와 같이 하는 대신에, 컬럼을 정의한 뒤에 인덱스를 생성할 수도 있습니다. 예를 들어:

    $table->unique('email');

인덱스 메소드에 컬럼의 배열을 전달하여 복합 인덱스를 생성할 수도 있습니다:

    $table->index(['account_id', 'created_at']);

라라벨은 자동으로 알맞은 인덱스 이름을 생성할 것입니다만, 메소드의 두번째 인자로 인덱스 이름을 지정할 수도 있습니다:

    $table->index('email', 'my_index_name');

#### 사용가능한 인덱스 타입

커맨드  | 설명
------------- | -------------
`$table->primary('id');`  |  프라이머리 키 추가.
`$table->primary(['first', 'last']);`  |   복합 키 추가.
`$table->unique('email');`  |  유니크 인덱스 추가.
`$table->unique('state', 'my_index_name');`  |  인덱스의 이름을 지정하기
`$table->index('state');`  |  기본적인 인덱스 추가.

<a name="dropping-indexes"></a>
### 인덱스 삭제하기

인덱스를 삭제하기 위해서는 인덱스의 이름을 지정해야 합니다. 라라벨은 자동으로 인덱스에 합리적인 이름을 부여하도록 설정되어 있습니다. 간단하게는 테이블 이름, 인덱스된 컬럼의 이름, 그리고 인덱스 타입을 합친것입니다. 다음은 몇 개의 예제 입니다:

명령어  | 설명
------------- | -------------
`$table->dropPrimary('users_id_primary');`  |  "users" 테이블에서 프라이머리 키 지우기.
`$table->dropUnique('users_email_unique');`  |  "users" 테이블에서 유니크 인덱스 지우기.
`$table->dropIndex('geo_state_index');`  |  "geo" 테이블에서 기본적인 인덱스 지우기.

인덱스들을 삭제하기 위해서 메소드에 컬럼의 배열을 전달하게 되면 인덱스의 이름은 테이블명, 컬럼이름 그리고 키의 타입을 기반으로 컨벤션에 의해서 인덱스 이름을 추정할 것입니다.

    Schema::table('geo', function ($table) {
        $table->dropIndex(['state']); // Drops index 'geo_state_index'
    });

<a name="foreign-key-constraints"></a>
### 외래키 제한

라라벨은 데이터베이스에서 또한 참조 무결성을 강제하는 외래 키 제한을 생성하는 것을 제공합니다. 예를 들어 `users` 테이블의 `id` 컬럼을 참조하는 `posts` 테이블에 `user_id` 컬럼을 정의해보겠습니다:

    Schema::table('posts', function ($table) {
        $table->integer('user_id')->unsigned();

        $table->foreign('user_id')->references('id')->on('users');
    });

제한(constraint)의 "on delete"와 "on update" 속성에 원하는 동작을 지정할 수도 있습니다:

    $table->foreign('user_id')
          ->references('id')->on('users')
          ->onDelete('cascade');

외래 키를 지우기 위해서는 `dropForeign` 메소드를 사용할 수 있습니다. 외래 키 제한은 인덱스와 같은 명명 규칙을 사용합니다. 따라서 테이블 이름과 제한(constraint)의 컬럼들을 합치고 뒤에 "_foreign"을 붙일 것입니다:

    $table->dropForeign('posts_user_id_foreign');

또는 배열 값을 전달하면 삭제시 자동으로 컨벤션 규칙에 따르는 이름이 사용됩니다.

    $table->dropForeign(['user_id']);

마이그레이션에서 다음의 메소드들을 사용하여 외래키 제약을 활성/비활성 할 수 있습니다.

    Schema::enableForeignKeyConstraints();

    Schema::disableForeignKeyConstraints();
