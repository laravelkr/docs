# 데이터베이스: 마이그레이션

- [시작하기](#introduction)
- [마이그레이션 파일 생성하기](#generating-migrations)
- [마이그레이션의 구조](#migration-structure)
- [마이그레이션 실행하기](#running-migrations)
    - [마이그레이션 되돌리기-롤백](#rolling-back-migrations)
- [테이블](#tables)
    - [테이블 생성하기](#creating-tables)
    - [테이블의 이름변경 / 제거](#renaming-and-dropping-tables)
- [컬럼](#columns)
    - [컬럼 생성하기](#creating-columns)
    - [컬럼 수정자](#column-modifiers)
    - [컬럼 수정하기](#modifying-columns)
    - [컬럼 삭제하기](#dropping-columns)
- [인덱스](#indexes)
    - [인덱스 생성하기](#creating-indexes)
    - [인덱스 이름 변경하기](#renaming-indexes)
    - [인덱스 없애기](#dropping-indexes)
    - [외래키 제약](#foreign-key-constraints)

<a name="introduction"></a>
## 시작하기

마이그레이션은 여러분의 팀에서 애플리케이션의 데이터베이스를 수정하고 데이터베이스 스키마를 공유할 수 있도록 해주며 데이터베이스를 위한 버전 컨트롤과 같은 역할을 합니다. 마이그레이션은 보통 라라벨의 스키마 빌더와 쌍을 이루어 애플리케이션의 데이터베이스 스키마를 손쉽게 만들 수 있습니다. 만약 팀 동료에게 로컬 데이터베이스 스키마에 수동으로 컬럼을 추가하는 것에 대해서 이야기를 해야한다면, 데이터베이스 마이그레이션 기능이 해결하고자 하는 문제에 직면해 있는 것입니다.

라라벨의 `Schema` [파사드](/docs/{{version}}/facades)는 라라벨이 지원하는 모든 데이터베이스 시스템을 지원하는 테이블을 생성과 조작에 대한 광범위한 지원을 제공합니다.

<a name="generating-migrations"></a>
## 마이그레이션 파일 생성하기

마이그레이션 파일을 생성하기 위해서는 `make:migration` [아티즌 명령어](/docs/{{version}}/artisan)를 사용합니다.

    php artisan make:migration create_users_table

새로운 마이그레이션 파일은 `database/migrations` 디렉토리에 생성됩니다. 각 마이그레이션 파일의 이름에는 타임스탬프를 포함하며 라라벨이 마이그레이션들의 순서를 판별할 수 있도록 합니다.

> {tip} [stub publishing](/docs/{{version}}/artisan#stub-customization)를 통해서 마이그레이션 stubs을 커스트마이징 할 수 있습니다. 

`--table`와 `--create` 옵션들은 테이블의 이름과 마이그레이션이 테이블을 새로 생성할지 여부를 표시하는 데 사용할 수 있습니다. 이 옵션들은 생성된 마이그레이션 stub 파일을 특정한 테이블로 미리 채워 놓습니다.

    php artisan make:migration create_users_table --create=users

    php artisan make:migration add_votes_to_users_table --table=users

생성된 마이그레이션 파일이 생성될 출력 경로를 지정하고 싶다면 `make:migration` 명령어를 실행할 때 `--path` 옵션을 사용하면 됩니다. 주어진 경로는 애플리케이션의 베이스 경로로 부터 연관된 상대경로여야 합니다.

<a name="migration-structure"></a>
## 마이그레이션의 구조

마이그레이션 클래스는 `up`과 `down`, 두 개의 메드를 가지고 있습니다. `up` 메소드는 데이터베이스에 테이블, 컬럼, 인덱스를 추가하는데 사용되고, 이와 반대로 `down` 메소드는 `up` 메소드의 동작을 취소합니다.

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
## 마이그레이션 실행하기

아직 실행된적이 없는 모든 마이그레이션을 실행하려면, `migrate` 아티즌 명령어를 실행하면 됩니다.

    php artisan migrate

> {note} 만약 [홈스테드 가상머신](/docs/{{version}}/homestead)을 사용중이라면, 이 명령어를 가상머신 안에서 실행해야 합니다.

#### 실제 production 서버에서 강제로 마이그레이션 실행하기

일부의 마이그레이션 작업은 구조의 변경이 있을 수 있으며, 이로 인해 데이터를 잃어 버릴 수도 있습니다. 이러한 위험들로 부터 실제 production 서버의 데이터베이스를 보호하기 위해서, 마이그레이션 명령어를 실행하기 전에는 확인 메시지가 출력됩니다. 확인 메시지 없이 명령어을 실행시키기 위해서는, `--force` 플래그를 사용하면 됩니다.

    php artisan migrate --force

<a name="rolling-back-migrations"></a>
### 마이그레이션 되돌리기-롤백

가장 최근의 마이그레이션 작업(operation)을 되돌리려면 `rollback` 명령어를 사용하면 됩니다. 이 명령어는 가장 최근에 실행된 마이그레이션의 "모음(batch)", 즉 여러개의 마이그레이션 파일에 대해서 되돌릴 수 있습니다.

    php artisan migrate:rollback

`rollback` 명령어에 `step` 옵션을 전달하여 제한된 숫자의 마이그레이션만 되돌릴 수 있습니다. 예를들어 다음의 명령어는 최근 5개의 마이그레이션만 되돌립니다.

    php artisan migrate:rollback --step=5

`migrate:reset` 커맨드는 애플리케이션의 모든 마이그레이션을 되돌립니다.

    php artisan migrate:reset

#### 하나의 명령어로 롤백과 마이그레이트 함께 실행 하기

`migrate:refresh` 명령어는 모든 마이그레이션을 되돌리고, `migrate` 명령어를 실행합니다. 이 명령어는 전체 데이터베이스를 효과적으로 재생성합니다.

    php artisan migrate:refresh

    // Refresh the database and run all database seeds...
    php artisan migrate:refresh --seed

`refresh` 명령어에 `step` 옵션을 전달하여 제한된 숫자의 마이그레이션만 되돌리고 다시 마이그레이션을 수행할 수 있습니다. 예를들어 다음의 명령어는 최근 5개의 마이그레이션만 되돌리고 마이그레이션을 다시 실행합니다.

    php artisan migrate:refresh --step=5

#### 모든 테이블과 마이그레이션 Drop

`migrate:fresh` 명령어는 `migrate` 명령이 실행될 때 데이터베이스의 모든 테이블을 drop 합니다.

    php artisan migrate:fresh

    php artisan migrate:fresh --seed

<a name="tables"></a>
## 테이블

<a name="creating-tables"></a>
### 테이블 생성하기

새로운 데이터베이스 테이블을 생성하려면 `Schema` 파사드에 `create` 메소드를 사용하면 됩니다. `create` 메소드는 두개의 인자를 전달 받습니다. 첫번째 인자는 테이블의 이름이고, 두번째 인자는 새로운 테이블을 정의하는 사용되는 `Blueprint` 객체를 받는 `Closure`입니다.

    Schema::create('users', function (Blueprint $table) {
        $table->id();
    });

테이블을 생성할 때, 테이블의 컬럼을 정의하기 위하여 자유롭게 스키마 빌더의 [컬럼 메소드](#creating-columns)를 사용할 수 있습니다.

#### 테이블 / 컬럼이 존재하는지 확인하기

`hasTable`와 `hasColumn` 메소드를 사용해 테이블이나 컬럼의 존재 유무를 확인할 수 있습니다.

    if (Schema::hasTable('users')) {
        //
    }

    if (Schema::hasColumn('users', 'email')) {
        //
    }

#### 데이터베이스 커넥션-connection & 테이블 옵션

기본 커넥션-connection이 아닌 다른 데이터베이스 커넥션-connection에 스키마 작업을 수행하려면 `connection` 메소드를 사용하면 됩니다.

    Schema::connection('foo')->create('users', function (Blueprint $table) {
        $table->id();
    });

테이블 옵션을 정의하기 위해서 스키마 빌더의 다음 명령어를 사용할 수 있습니다.

명령어 | 설명
-------  |  -----------
`$table->engine = 'InnoDB';`  |  테이블 엔진을 지정합니다.(MySQL).
`$table->charset = 'utf8';`  |  테이블의 기본 케릭터셋을 지정합니다.(MySQL).
`$table->collation = 'utf8_unicode_ci';`  |  테이블의 기본 collation을 지정합니다.(MySQL).
`$table->temporary();`  |  임시 테이블을 생성 (SQL Server 제외).

<a name="renaming-and-dropping-tables"></a>
### 테이블 이름 변경 / 제거하기

이미 존재하는 테이블의 이름을 바꾸려면 `rename` 메소드를 이용하십시오:

    Schema::rename($from, $to);

이미 존재하는 테이블을 제거하려면 `drop`이나 `dropIfExists` 메소드를 사용하세요:

    Schema::drop('users');

    Schema::dropIfExists('users');

#### 외래 키를 가진 테이블의 이름 변경

테이블의 이름 변경하기 전에, 마이그레이션 파일에서 라라벨의 규약(Convention)에 따르는 기본 이름으로 지정되지 않고 고유한 이름을 붙인 외래 키 제약 조건이 존재하지 않는지 확인하십시오. 그렇지 않다면 외래 키 제약의 이름이 기존 테이블 이름을 참고하게 됩니다.

<a name="columns"></a>
## 컬럼

<a name="creating-columns"></a>
### 컬럼 생성하기

이미 존재하는 테이블을 변경하기 하는데, `Schema` 파사드의 `table` 메소드를 사용할 수 있습니다. `create` 메소드와 같이 `table` 메소드도 두개읜 인자를 전달 받습니다. 하나는 테이블의 이름이고 ,다른 하나는 테이블에 컬럼을 추가하는데 사용할 수 있는 `Blueprint` 인스턴스를 받아들이는 `Closure`입니다.

    Schema::table('users', function (Blueprint $table) {
        $table->string('email');
    });

#### 사용가능한 컬럼의 타입들

스키마 빌더는 테이블을 만들 때 이를 지정할 수 있는 다양한 컬럼 타입들을 가지고 있습니다.

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
### 컬럼 수정자(modifiers)

위 목록의 컬럼 타입들에 더하여, 데이터베이스 테이블에 컬럼을 추가하는데 사용할 수 있는 몇가지 컬럼 "modifier"가 존재합니다. 예를 들어, 컬럼을 "nullable" 하게 만들려면, `nullable` 메소드를 사용할 수 있습니다.

    Schema::table('users', function (Blueprint $table) {
        $table->string('email')->nullable();
    });

다음은 사용 가능한 모든 컬럼 Modifier의 목록입니다. 이 목록은 [인덱스 modifiers](#creating-indexes)를 포함하지 않습니다.

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

#### 기본 표현식

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

> {note} 기본 표현식의 지원은 데이터베이스 드라이버, 데이터베이스 버전 및 필드 유형에 따라 다릅니다. 호환성에 대해서는 관련 문서를 참조하십시오. 또한 데이터베이스 특정 기능을 사용하면 특정 드라이버에 밀접하게 연관될 수 있습니다.

<a name="modifying-columns"></a>
### 컬럼 수정하기

#### 사전 준비사항

컬럼을 수정하기 전에, 꼭 `composer.json` 파일에 `doctrine/dbal` 의존성을 추가하십시오. Doctrine DBAL 라이브러리는 컬럼의 현재 상태를 확인하고 필요한 SQL 쿼리를 생성하기 위해 사용됩니다.

    composer require doctrine/dbal

#### 컬럼의 속성 변경하기

`change` 메소드는 이미 존재하는 컬럼 타입을 유형과 속성을 변경합니다. 예를 들어, `string` 컬럼의 사이즈를 늘이고 싶을 수 있습니다. `change` 메소드가 어떻게 작동하는지 `name` 컬럼 사이즈를 25에서 50으로 늘여서 확인해 보겠습니다.

    Schema::table('users', function (Blueprint $table) {
        $table->string('name', 50)->change();
    });

컬럼을 nullable로 수정할 수도 있습니다.

    Schema::table('users', function (Blueprint $table) {
        $table->string('name', 50)->nullable()->change();
    });

> {note} 다음의 컬럼 타입들만 "변경" 할 수 있습니다. bigInteger, binary, boolean, date, dateTime, dateTimeTz, decimal, integer, json, longText, mediumText, smallInteger, string, text, time, unsignedBigInteger, unsignedInteger, unsignedSmallInteger.

#### 컬럼의 이름 변경하기

컬럼의 이름을 변경하기 위해서, 스키마 빌더에 `renameColumn` 메소드를 사용할 수 있습니다. 컬럼의 이름을 바꾸기 전에 반드시 `composer.json` 파일에 `doctrine/dbal` 의존성을 추가하십시오:

    Schema::table('users', function (Blueprint $table) {
        $table->renameColumn('from', 'to');
    });

> {note} 테이블의 `enum` 타입을 가진 컬럼의 이름을 바꾸는 것은 현재 지원되지 않습니다.

<a name="dropping-columns"></a>
### 컬럼 삭제하기

컬럼을 삭제하기 위해서는, 스키마 빌더에서 `dropColumn` 메소드를 사용하면 됩니다. SQLite 데이터베이스에서 컬럼을 삭제하기 전에, `composer.json` 파일에 `doctrine/dbal` 의존성을 추가하고 라이브러리를 설치하기 위해서 터미널에서 `composer update` 명령어를 실행할 필요가 있습니다.

    Schema::table('users', function (Blueprint $table) {
        $table->dropColumn('votes');
    });

`dropColumn` 메소드에 컬럼 이름들의 배열을 전달하면 테이블에서 여러 개의 컬럼을 없앨 수 있습니다.

    Schema::table('users', function (Blueprint $table) {
        $table->dropColumn(['votes', 'avatar', 'location']);
    });

> {note} SQLite 데이터베이스를 사용하는 중에는, 하나의 마이그레이션 안에서 여러 개의 컬럼을 없애거나 수정할 수 없습니다.

#### 사용가능한 명령어 alias(별칭)

명령어 |  설명
-------  |  -----------
`$table->dropMorphs('morphable');`  | `morphable_id` 와 `morphable_type` 컬럼 drop.
`$table->dropRememberToken();`  |  `remember_token` 컬럼 drop.
`$table->dropSoftDeletes();`  |  `deleted_at` 컬럼 drop.
`$table->dropSoftDeletesTz();`  |  `dropSoftDeletes()` 메소드의 alias.
`$table->dropTimestamps();`  |  `created_at`와 `updated_at` 컬럼 drop.
`$table->dropTimestampsTz();` |  `dropTimestamps()` 메소드의 alias.

<a name="indexes"></a>
## 인덱스

<a name="creating-indexes"></a>
### 인덱스 생성하기

Laravel 스키마 빌더는 여러 타입의 인덱스를 지원합니다. 다음에서 새로운 `email` 컬럼을 만들고 해당 값을 고유 값을 가지도록 만들어 보겠습니다. 인덱스를 생성하려면 컬럼의 정의에서 `unique` 메소드를 체이닝 하면 됩니다.

    $table->string('email')->unique();

위와 같이 하는 대신에, 컬럼을 정의한 뒤에 인덱스를 생성할 수도 있습니다. 예를 들어:

    $table->unique('email');

인덱스 메소드에 컬럼의 배열을 전달하여 결합(복합) 인덱스를 생성할 수도 있습니다.

    $table->index(['account_id', 'created_at']);

라라벨은 테이블, 컬럼 이름 및 인덱스 유형을 기반으로 인덱스 이름을 자동으로 생성하지만 메소드의 두번째 인자로 인덱스 이름을 지정할 수도 있습니다.

    $table->unique('email', 'unique_email');

#### 사용가능한 인덱스 타입

각각의 인덱스 메소드는 두번쨰 인수를 통해 선택적으로 인덱스의 이름입력받습니다. 이 값이 생략된다면, 인덱스의 이름은 테이블, 컬럼과 인덱스의 타입을 기반으로 생성됩니다.

커맨드  | 설명
-------  |  -----------
`$table->primary('id');`  |  primary key 추가.
`$table->primary(['id', 'parent_id']);`  |   복합 키 추가.
`$table->unique('email');`  |  유니크 인덱스 추가.
`$table->index('state');`  |  기본적인 인덱스 추가.
`$table->spatialIndex('location');`  |  공간(spatial) 인덱스 추가 (SQLite 제외.)

#### 인덱스 길이 & MySQL / MariaDB

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

또는 데이터베이스에 `innodb_large_prefix` 옵션을 활성화 할 수도 있습니다. 이 옵션을 제대로 사용하는 방법은 데이터베이스 매뉴얼을 참고하십시오.

<a name="renaming-indexes"></a>
### 인덱스 이름 변경하기

인덱스의 이름을 변경하기 위해서는 `renameIndex` 메소드를 사용하면 됩니다. 이 메소드는 현재의 인덱스 이름을 첫번째 인자로, 변경하고자 하는 새이름을 두번째 인자로 전달받습니다.

    $table->renameIndex('from', 'to')

<a name="dropping-indexes"></a>
### 인덱스 삭제하기

인덱스를 삭제하기 위해서는 인덱스의 이름을 지정해야 합니다. 기본적으로 라라벨은 테이블 이름, 인덱스된 컬럼의 이름, 그리고 인덱스 타입을 기반으로 자동으로 인덱스의 이름을 부여하도록 설정되어 있습니다. 다음은 몇 개의 예제 입니다.

명령어  | 설명
------------- | -------------
`$table->dropPrimary('users_id_primary');`  |  "users" 테이블에서 프라이머리 키 지우기.
`$table->dropUnique('users_email_unique');`  |  "users" 테이블에서 유니크 인덱스 지우기.
`$table->dropIndex('geo_state_index');`  |  "geo" 테이블에서 기본적인 인덱스 지우기.
`$table->dropSpatialIndex('geo_location_spatialindex');`  |  "geo" 테이블에서 공간(spatial) 인덱스 지우기(SQLite 제외).

인덱스들을 삭제하기 위해서 메소드에 컬럼의 배열을 전달하게 되면 인덱스의 이름은 테이블명, 컬럼이름 그리고 키의 타입을 기반으로 컨벤션에 의해서 인덱스 이름을 추정할 것입니다.

    Schema::table('geo', function (Blueprint $table) {
        $table->dropIndex(['state']); // Drops index 'geo_state_index'
    });

<a name="foreign-key-constraints"></a>
### 외래키 제약조건(Constraints)

라라벨은 데이터베이스에서 또한 참조 무결성을 강제하는 외래 키 제약조건을 생성하는 것을 제공합니다. 예를 들어 `users` 테이블의 `id` 컬럼을 참조하는 `posts` 테이블에 `user_id` 컬럼을 정의해보겠습니다.

    Schema::table('posts', function (Blueprint $table) {
        $table->unsignedBigInteger('user_id');

        $table->foreign('user_id')->references('id')->on('users');
    });

이 구문은 다소 장황하므로 라라벨은 더 나은 개발자 경험을 제공하기 위해 규칙을 사용하는 추가의 terser 메소드를 제공합니다. 위의 예제는 다음과 같이 작성할 수 있습니다.

    Schema::table('posts', function (Blueprint $table) {
        $table->foreignId('user_id')->constrained();
    });

`foreignId` 메소드는 `unsignedBigInteger`의 별명 인 반면, `constrained` 메소드는 참조되는 테이블 및 컬럼 이름을 판별하기 위해 규칙을 사용합니다.

제약조건(constraint)의 "on delete"와 "on update" 속성에 원하는 동작을 지정할 수도 있습니다.

    $table->foreignId('user_id')
          ->constrained()
          ->onDelete('cascade');

외래 키를 지우기 위해서는 `dropForeign` 메소드를 사용할 수 있습니다. 외래 키 제약조건(constraint)은 인덱스와 같은 명명 규칙을 사용합니다. 따라서 테이블 이름과 제약조건(constraint)의 컬럼들을 합치고 뒤에 "_foreign"을 붙일 것입니다.

    $table->dropForeign('posts_user_id_foreign');

또는 외래 키를 포함하는 컬럼 이름이 포함 된 배열을 `dropForeign` 메서드에 전달할 수 있습니다. 배열은 라라벨의 스키마 빌더가 사용하는 제약 조건 이름 규칙을 사용하여 자동으로 변환됩니다.

    $table->dropForeign(['user_id']);

마이그레이션에서 다음의 메소드들을 사용하여 외래키 제약조건을 활성/비활성 할 수 있습니다.

    Schema::enableForeignKeyConstraints();

    Schema::disableForeignKeyConstraints();

> {note} SQLite는 기본적으로 외래 키 제약 조건을 비활성화합니다. SQLite를 사용하는 경우, 마이그레이션에서 데이터베이스 설정을 작성하기 전에 데이터베이스 설정에서 [외래 키 지원](/docs/{{version}}/database#configuration)을 사용해야합니다. 또한 SQLite는 테이블 생성시 및 [테이블이 변경되지 않을 때](https://www.sqlite.org/omitted.html) 외래 키만 지원합니다.
