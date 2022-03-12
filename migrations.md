# 데이터베이스: 마이그레이션

- [시작하기](#introduction)
- [마이그레이션 파일 생성하기](#generating-migrations)
    - [스쿼싱 마이그레이션](#squashing-migrations)
- [마이그레이션의 구조](#migration-structure)
- [마이그레이션 실행하기](#running-migrations)
    - [마이그레이션 되돌리기-롤백](#rolling-back-migrations)
- [테이블](#tables)
    - [테이블 생성하기](#creating-tables)
    - [테이블 수정하기](#updating-tables)
    - [테이블의 이름변경 / 제거](#renaming-and-dropping-tables)
- [Columns](#columns)
    - [컬럼 생성하기](#creating-columns)
    - [사용가능한 컬럼의 타입들](#available-column-types)
    - [컬럼 수정자](#column-modifiers)
    - [컬럼 수정하기](#modifying-columns)
    - [컬럼 삭제하기](#dropping-columns)
- [인덱스](#indexes)
    - [인덱스 생성하기](#creating-indexes)
    - [인덱스 이름 변경하기](#renaming-indexes)
    - [인덱스 없애기](#dropping-indexes)
    - [외래키 제약조건](#foreign-key-constraints)
- [이벤트](#events)

<a name="introduction"></a>
## 시작하기

마이그레이션은 데이터베이스의 버전 컨트롤과 같으므로 팀에서 애플리케이션의 데이터베이스 스키마 정의를 정의하고 공유할 수 있습니다. 소스 제어에서 변경 사항을 가져온 후 팀원에게 수동으로 로컬 데이터베이스 스키마에 컬럼을 추가하도록 지시해야 하는 경우 데이터베이스 마이그레이션이 해결하는 문제에 직면해 있는 것입니다.

라라벨의 `Schema` [파사드](/docs/{{version}}/facades)는 라라벨에서 지원하는 모든 데이터베이스 시스템에서 테이블을 생성하고 조작하기 위한 데이터베이스 광범위한 지원을 제공합니다. 일반적으로 마이그레이션은 이 파사드를 사용하여 데이터베이스 테이블과 컬럼을 만들고 수정합니다.

<a name="generating-migrations"></a>
## 마이그레이션 파일 생성하기

마이그레이션 파일을 생성하기 위해서는 `make:migration` [아티즌 명령어](/docs/{{version}}/artisan)를 사용합니다. 새로운 마이그레이션 파일은 `database/migrations` 디렉토리에 생성됩니다. 각 마이그레이션 파일 이름에는 Laravel이 마이그레이션 순서를 결정할 수 있는 타임스탬프가 포함되어 있습니다.

    php artisan make:migration create_flights_table

라라벨은 마이그레이션 이름을 사용하여 테이블 이름을 추측하고 마이그레이션이 새 테이블을 생성할지 여부를 추측합니다. 라라벨이 마이그레이션 이름에서 테이블 이름을 결정할 수 있다면 라라벨은 생성된 마이그레이션 파일을 지정된 테이블로 미리 채울 것입니다. 그렇지 않으면 마이그레이션 파일에 테이블을 수동으로 지정할 수 있습니다.

생성된 마이그레이션에 대한 사용자 지정 경로를 지정하려면 `make:migration` 명령을 실행할 때 `--path` 옵션을 사용할 수 있습니다. 주어진 경로는 애플리케이션의 기본 경로에 상대적이어야 합니다.

> {tip} [stub publishing](/docs/{{version}}/artisan#stub-customization)를 통해서 마이그레이션 stubs을 커스트마이징 할 수 있습니다.

<a name="squashing-migrations"></a>
### 스쿼싱 마이그레이션

애플리케이션을 빌드하면서 시간이 지남에 따라 더 많은 마이그레이션이 누적될 수 있습니다. 이로 인해 `database/migrations` 디렉토리가 잠재적으로 수백 번의 마이그레이션으로 비대해질 수 있습니다. 원하는 경우 마이그레이션을 단일 SQL 파일로 "squash" 할 수 있습니다. 시작하려면 `schema:dump` 명령을 실행하세요:

    php artisan schema:dump

    // Dump the current database schema and prune all existing migrations...
    php artisan schema:dump --prune

이 명령을 실행하면 라라벨은 `database/schema` 폴더에 "schema" 파일을 작성합니다. 이제 데이터베이스 마이그레이션을 시도하고 다른 마이그레이션이 실행되지 않은 경우 라라벨은 스키마 파일의 SQL 문을 먼저 실행합니다. 스키마 파일의 명령문을 실행한 후 라라벨은 스키마 덤프의 일부가 아닌 나머지 마이그레이션을 실행합니다.

여러분은 팀의 다른 새로운 개발자가 애플리케이션의 초기 데이터베이스 구조를 빠르게 만들 수 있도록 데이터베이스 스키마 파일을 소스 컨트롤에 커밋해야 합니다.

> {note} 마이그레이션 스쿼싱은 MySQL, PostgreSQL 및 SQLite 데이터베이스에서만 사용할 수 있으며 데이터베이스의 명령줄 클라이언트를 활용합니다. 스키마 덤프는 메모리 내 SQLite 데이터베이스로 복원되지 않을 수 있습니다.

<a name="migration-structure"></a>
## 마이그레이션의 구조

마이그레이션 클래스에는 `up` 및 `down`의 두 가지 방법이 있습니다. `up` 메소드는 데이터베이스에 새 테이블, 열 또는 인덱스를 추가하는 데 사용되는 반면, `down` 방법은 `up` 방법이 수행하는 작업을 반대로 해야 합니다.

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
#### 익명 마이그레이션

위의 예에서 알 수 있듯이 라라벨은 `make:migration` 명령을 사용하여 생성하는 모든 마이그레이션에 클래스 이름을 자동으로 할당합니다. 그러나 원하는 경우 마이그레이션 파일에서 익명 클래스를 반환할 수 있습니다. 이는 애플리케이션이 많은 마이그레이션을 쌓여있을때 그 중 두 개에 클래스 이름 충돌이 있는 경우에 주로 유용합니다.

    <?php

    use Illuminate\Database\Migrations\Migration;

    return new class extends Migration
    {
        //
    };

<a name="setting-the-migration-connection"></a>
#### 마이그레이션 연결 설정

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
## 마이그레이션 실행하기

아직 실행된적이 없는 모든 마이그레이션을 실행하려면, `migrate` 아티즌 명령어를 실행하면 됩니다.

    php artisan migrate

지금까지 어떤 마이그레이션이 실행되었는지 확인하려면 `migrate:status` 아티즌 명령을 사용할 수 있습니다.

    php artisan migrate:status

<a name="forcing-migrations-to-run-in-production"></a>
#### 실제 production 서버에서 강제로 마이그레이션 실행하기

일부 마이그레이션 작업은 구조의 변경이 있을 수 있으며 데이터 손실이 발생할 수 있습니다. production 서버의 데이터베이스에 대해 이러한 명령을 실행하지 못하도록 보호하기 위해 명령을 실행하기 전에 확인 메시지가 표시됩니다. 확인 메시지 없이 명령을 강제 실행하려면 `--force` 플래그를 사용하세요:

    php artisan migrate --force

<a name="rolling-back-migrations"></a>
### 마이그레이션 되돌리기-롤백

가장 최근의 마이그레이션 작업(operation)을 되돌리려면 `rollback` 명령어를 사용하면 됩니다. 이 명령어는 가장 최근에 실행된 마이그레이션의 "모음(batch)", 즉 여러개의 마이그레이션 파일에 대해서 되돌릴 수 있습니다.

    php artisan migrate:rollback

`rollback` 명령어에 `step` 옵션을 전달하여 제한된 숫자의 마이그레이션만 되돌릴 수 있습니다. 예를 들어 다음의 명령어는 최근 5개의 마이그레이션만 되돌립니다.

    php artisan migrate:rollback --step=5

`migrate:reset` 커맨드는 애플리케이션의 모든 마이그레이션을 되돌립니다.

    php artisan migrate:reset

<a name="roll-back-migrate-using-a-single-command"></a>
#### 하나의 명령어로 롤백과 마이그레이트 함께 실행 하기

`migrate:refresh` 명령어는 모든 마이그레이션을 되돌리고, `migrate` 명령어를 실행합니다. 이 명령어는 전체 데이터베이스를 효과적으로 재생성합니다.

    php artisan migrate:refresh

    // Refresh the database and run all database seeds...
    php artisan migrate:refresh --seed

`refresh` 명령어에 `step` 옵션을 전달하여 제한된 숫자의 마이그레이션만 되돌리고 다시 마이그레이션을 수행할 수 있습니다. 예를들어 다음의 명령어는 최근 5개의 마이그레이션만 되돌리고 마이그레이션을 다시 실행합니다.

    php artisan migrate:refresh --step=5

<a name="drop-all-tables-migrate"></a>
#### 모든 테이블과 마이그레이션 Drop

`migrate:fresh` 명령어는 `migrate` 명령이 실행될 때 데이터베이스의 모든 테이블을 drop 합니다.

    php artisan migrate:fresh

    php artisan migrate:fresh --seed

> {note} `migrate:fresh` 명령은 접두어에 관계없이 모든 데이터베이스 테이블을 삭제합니다. 이 명령은 다른 응용 프로그램과 공유되는 데이터베이스에서 개발할 때 주의해서 사용해야 합니다.

<a name="tables"></a>
## 테이블

<a name="creating-tables"></a>
### 테이블 생성하기

새로운 데이터베이스 테이블을 생성하려면 `Schema` 파사드에 `create` 메소드를 사용하면 됩니다. `create` 메소드는 두개의 인자를 전달 받습니다. 첫번째 인자는 테이블의 이름이고, 두번째 인자는 새로운 테이블을 정의하는 사용되는 `Blueprint` 객체를 받는 `Closure`입니다.

    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Support\Facades\Schema;

    Schema::create('users', function (Blueprint $table) {
        $table->id();
        $table->string('name');
        $table->string('email');
        $table->timestamps();
    });

테이블을 생성할 때, 테이블의 컬럼을 정의하기 위하여 자유롭게 스키마 빌더의 [컬럼 메소드](#creating-columns)를 사용할 수 있습니다.

<a name="checking-for-table-column-existence"></a>
#### 테이블 / 컬럼이 존재하는지 확인하기

`hasTable`와 `hasColumn` 메소드를 사용해 테이블이나 컬럼의 존재 유무를 확인할 수 있습니다.

    if (Schema::hasTable('users')) {
        // The "users" table exists...
    }

    if (Schema::hasColumn('users', 'email')) {
        // The "users" table exists and has an "email" column...
    }

<a name="database-connection-table-options"></a>
#### 데이터베이스 커넥션-connection & 테이블 옵션

기본 커넥션-connection이 아닌 다른 데이터베이스 커넥션-connection에 스키마 작업을 수행하려면 `connection` 메소드를 사용하면 됩니다.

    Schema::connection('sqlite')->create('users', function (Blueprint $table) {
        $table->id();
    });

또한 몇 가지 다른 속성과 메소드를 사용하여 테이블 생성의 다른 측면을 정의할 수 있습니다. `engine` 속성은 MySQL을 사용할 때 테이블의 스토리지 엔진을 지정하는 데 사용할 수 있습니다.

    Schema::create('users', function (Blueprint $table) {
        $table->engine = 'InnoDB';

        // ...
    });

`charset` 및 `collation` 속성은 MySQL을 사용할 때 생성된 테이블에 대한 문자 집합 및 데이터 정렬을 지정하는 데 사용할 수 있습니다.

    Schema::create('users', function (Blueprint $table) {
        $table->charset = 'utf8mb4';
        $table->collation = 'utf8mb4_unicode_ci';

        // ...
    });

`temporary` 메소드는 테이블이 "temporary"이여야 함을 나타내는 데 사용할 수 있습니다. 임시 테이블은 현재 연결의 데이터베이스 세션에서만 볼 수 있으며 연결이 닫힐 때 자동으로 삭제됩니다.

    Schema::create('calculations', function (Blueprint $table) {
        $table->temporary();

        // ...
    });

<a name="updating-tables"></a>
### 테이블 수정하기

`Schema` 파사드의 `table` 메소드는 기존 테이블을 수정하는 데 사용할 수 있습니다. `create` 메소드와 마찬가지로 `table` 메소드는 테이블 이름과 테이블에 컬럼이나 인덱스를 추가하는 데 사용할 수 있는 `Blueprint` 인스턴스를 받는 클로저의 두 가지 인수를 허용합니다.

    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Support\Facades\Schema;

    Schema::table('users', function (Blueprint $table) {
        $table->integer('votes');
    });

<a name="renaming-and-dropping-tables"></a>
### 테이블 이름 변경 / 제거하기

이미 존재하는 테이블의 이름을 바꾸려면 `rename` 메소드를 이용하십시오:

    use Illuminate\Support\Facades\Schema;

    Schema::rename($from, $to);

이미 존재하는 테이블을 제거하려면 `drop`이나 `dropIfExists` 메소드를 사용하세요:

    Schema::drop('users');

    Schema::dropIfExists('users');

<a name="renaming-tables-with-foreign-keys"></a>
#### 외래 키를 가진 테이블의 이름 변경

테이블의 이름 변경하기 전에, 마이그레이션 파일에서 라라벨의 규약(Convention)에 따르는 기본 이름으로 지정되지 않고 고유한 이름을 붙인 외래 키 제약 조건이 존재하지 않는지 확인하십시오. 그렇지 않다면 외래 키 제약의 이름이 기존 테이블 이름을 참고하게 됩니다.

<a name="columns"></a>
## 컬럼

<a name="creating-columns"></a>
### 컬럼 생성하기

이미 존재하는 테이블을 변경하기 하는데, `Schema` 파사드의 `table` 메소드를 사용할 수 있습니다. `create` 메소드와 같이 `table` 메소드도 두개의 인자를 전달 받습니다. 하나는 테이블의 이름이고, 다른 하나는 테이블에 컬럼을 추가하는데 사용할 수 있는 `Blueprint` 인스턴스를 받는 `Closure`입니다.

    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Support\Facades\Schema;

    Schema::table('users', function (Blueprint $table) {
        $table->integer('votes');
    });

<a name="available-column-types"></a>
### 사용가능한 컬럼의 타입들

스키마 빌더 청사진(blueprint)은 데이터베이스 테이블에 추가할 수 있는 다양한 유형의 컬럼에 해당하는 다양한 방법을 제공합니다. 사용 가능한 각 방법은 아래 표에 나열되어 있습니다.

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

<a name="column-method-bigIncrements"></a>
#### `bigIncrements()` {.collection-method .first-collection-method}

`bigIncrements` 메소드는 자동 증가하는 `UNSIGNED BIGINT`(기본 키)에 해당하는 컬럼을 생성합니다.

    $table->bigIncrements('id');

<a name="column-method-bigInteger"></a>
#### `bigInteger()` {.collection-method}

`bigInteger` 메소드는 `BIGINT`에 해당하는 컬럼을 생성합니다.

    $table->bigInteger('votes');

<a name="column-method-binary"></a>
#### `binary()` {.collection-method}

`binary` 메소드는 `BLOB`에 해당하는 컬럼을 생성합니다.

    $table->binary('photo');

<a name="column-method-boolean"></a>
#### `boolean()` {.collection-method}

`boolean` 메소드는 `BOOLEAN`에 해당하는 컬럼을 생성합니다.

    $table->boolean('confirmed');

<a name="column-method-char"></a>
#### `char()` {.collection-method}

`char` 메소드는 주어진 길이의 `CHAR`에 해당하는 컬럼을 생성합니다.

    $table->char('name', 100);

<a name="column-method-dateTimeTz"></a>
#### `dateTimeTz()` {.collection-method}

`dateTimeTz` 메소드는 선택적 정밀도(총 자릿수)를 사용하여 `DATETIME`(시간대 포함)에 해당하는 컬럼을 생성합니다.

    $table->dateTimeTz('created_at', $precision = 0);

<a name="column-method-dateTime"></a>
#### `dateTime()` {.collection-method}

`dateTime` 메소드는 선택적 정밀도(총 자릿수)를 사용하여 `DATETIME`에 해당하는 컬럼을 생성합니다.

    $table->dateTime('created_at', $precision = 0);

<a name="column-method-date"></a>
#### `date()` {.collection-method}

`date` 메소드는 `DATE`에 해당하는 컬럼을 생성합니다.

    $table->date('created_at');

<a name="column-method-decimal"></a>
#### `decimal()` {.collection-method}

`decimal` 메소드는 주어진 정밀도(총 자릿수) 및 스케일(십진 자릿수)을 사용하여 `DECIMAL`에 해당하는 컬럼을 생성합니다.

    $table->decimal('amount', $precision = 8, $scale = 2);

<a name="column-method-double"></a>
#### `double()` {.collection-method}

`double` 메소드는 주어진 정밀도(총 자릿수)와 스케일(십진수)을 사용하여 `DOUBLE`에 해당하는 컬럼을 생성합니다.

    $table->double('amount', 8, 2);

<a name="column-method-enum"></a>
#### `enum()` {.collection-method}

`enum` 메소드는 주어진 유효한 값으로 `ENUM`에 해당하는 컬럼을 생성합니다.

    $table->enum('difficulty', ['easy', 'hard']);

<a name="column-method-float"></a>
#### `float()` {.collection-method}

`float` 메소드는 주어진 정밀도(총 자릿수)와 스케일(십진수)로 `FLOAT`에 해당하는 컬럼을 생성합니다.

    $table->float('amount', 8, 2);

<a name="column-method-foreignId"></a>
#### `foreignId()` {.collection-method}

`foreignId` 메소드는 `UNSIGNED BIGINT`에 해당하는 컬럼을 생성합니다.

    $table->foreignId('user_id');

<a name="column-method-foreignIdFor"></a>
#### `foreignIdFor()` {.collection-method}

`foreignIdFor` 메소드는 주어진 모델 클래스에 대해 `{column}_id UNSIGNED BIGINT`에 해당하는 컬럼을 추가합니다.

    $table->foreignIdFor(User::class);

<a name="column-method-foreignUuid"></a>
#### `foreignUuid()` {.collection-method}

`foreignUuid` 메소드는 `UUID`에 해당하는 컬럼을 생성합니다.

    $table->foreignUuid('user_id');

<a name="column-method-geometryCollection"></a>
#### `geometryCollection()` {.collection-method}

`geometryCollection` 메소드는 `GEOMETRYCOLLECTION`에 해당하는 컬럼을 생성합니다.

    $table->geometryCollection('positions');

<a name="column-method-geometry"></a>
#### `geometry()` {.collection-method}

`geometry` 메소드는 `GEOMETRY`에 해당하는 컬럼을 생성합니다.

    $table->geometry('positions');

<a name="column-method-id"></a>
#### `id()` {.collection-method}

`id` 메소드는 `bigIncrements` 메소드의 별칭입니다. 기본적으로 이 메소드는 `id` 컬럼을 생성합니다. 그러나 컬럼에 다른 이름을 지정하려면 컬럼 이름을 전달할 수 있습니다.

    $table->id();

<a name="column-method-increments"></a>
#### `increments()` {.collection-method}

`increments` 메소드는 기본 키로 자동 증가하는 `UNSIGNED INTEGER`에 해당하는 컬럼을 생성합니다.

    $table->increments('id');

<a name="column-method-integer"></a>
#### `integer()` {.collection-method}

`integer` 메소드는 `INTEGER`에 해당하는 컬럼을 생성합니다.

    $table->integer('votes');

<a name="column-method-ipAddress"></a>
#### `ipAddress()` {.collection-method}

`ipAddress` 메소드는 `VARCHAR`에 해당하는 컬럼을 생성합니다.

    $table->ipAddress('visitor');

<a name="column-method-json"></a>
#### `json()` {.collection-method}

`json` 메소드는 `JSON`에 해당하는 컬럼을 생성합니다.

    $table->json('options');

<a name="column-method-jsonb"></a>
#### `jsonb()` {.collection-method}

`jsonb` 메소드는 `JSONB`에 해당하는 컬럼을 생성합니다.

    $table->jsonb('options');

<a name="column-method-lineString"></a>
#### `lineString()` {.collection-method}

`lineString` 메소드는 `LINESTRING`에 해당하는 컬럼을 생성합니다.

    $table->lineString('positions');

<a name="column-method-longText"></a>
#### `longText()` {.collection-method}

`longText` 메소드는 `LONGTEXT`에 해당하는 컬럼을 생성합니다.

    $table->longText('description');

<a name="column-method-macAddress"></a>
#### `macAddress()` {.collection-method}

`macAddress` 메소드는 맥어드레스를 담기 위한 컬럼을 생성합니다. PostgreSQL과 같은 일부 데이터베이스 시스템에는 이러한 유형의 데이터에 대한 전용 컬럼 유형이 있습니다. 다른 데이터베이스 시스템은 문자열에 해당하는 컬럼을 사용합니다.

    $table->macAddress('device');

<a name="column-method-mediumIncrements"></a>
#### `mediumIncrements()` {.collection-method}

`mediumIncrements` 메소드는 기본 키로 자동 증가하는 `UNSIGNED MEDIUMINT`에 해당하는 컬럼을 생성합니다.

    $table->mediumIncrements('id');

<a name="column-method-mediumInteger"></a>
#### `mediumInteger()` {.collection-method}

`mediumInteger` 메소드는 `MEDIUMINT`에 해당하는 컬럼을 생성합니다.

    $table->mediumInteger('votes');

<a name="column-method-mediumText"></a>
#### `mediumText()` {.collection-method}

`mediumText` 메소드는 `MEDIUMTEXT`에 해당하는 컬럼을 생성합니다.

    $table->mediumText('description');

<a name="column-method-morphs"></a>
#### `morphs()` {.collection-method}

`morphs` 메소드는 `{column}_id` `UNSIGNED BIGINT`에 해당하는 컬럼과 `{column}_type` `VARCHAR`에 해당하는 컬럼을 추가하는 편리한 메소드입니다.

이 방법은 다형성 [Relationships - 관계](/docs/{{version}}/eloquent-relationships)에 필요한 컬럼을 정의할 때 사용하기 위한 것입니다. 다음 예에서는 `taggable_id` 및 `taggable_type` 컬럼이 생성됩니다.

    $table->morphs('taggable');

<a name="column-method-multiLineString"></a>
#### `multiLineString()` {.collection-method}

`multiLineString` 메소드는 `MULTILINESTRING`에 해당하는 컬럼을 생성합니다.

    $table->multiLineString('positions');

<a name="column-method-multiPoint"></a>
#### `multiPoint()` {.collection-method}

`multiPoint` 메소드는 `MULTIPOINT`에 해당하는 컬럼을 생성합니다.

    $table->multiPoint('positions');

<a name="column-method-multiPolygon"></a>
#### `multiPolygon()` {.collection-method}

`multiPolygon` 메소드는 `MULTIPOLYGON`에 해당하는 컬럼을 생성합니다.

    $table->multiPolygon('positions');

<a name="column-method-nullableTimestamps"></a>
#### `nullableTimestamps()` {.collection-method}

`nullableTimestamps` 메소드는 [timestamps](#column-method-timestamps) 메소드의 별칭입니다.

    $table->nullableTimestamps(0);

<a name="column-method-nullableMorphs"></a>
#### `nullableMorphs()` {.collection-method}

이 방법은 [morphs](#column-method-morphs) 방법과 유사합니다. 그러나 생성되는 컬럼은 "nullable"이 됩니다.

    $table->nullableMorphs('taggable');

<a name="column-method-nullableUuidMorphs"></a>
#### `nullableUuidMorphs()` {.collection-method}

이 방법은 [uuidMorphs](#column-method-uuidMorphs) 방법과 유사합니다. 그러나 생성되는 컬럼은 "nullable"이 됩니다.

    $table->nullableUuidMorphs('taggable');

<a name="column-method-point"></a>
#### `point()` {.collection-method}

`point` 메소드는 `POINT`에 해당하는 컬럼을 생성합니다.

    $table->point('position');

<a name="column-method-polygon"></a>
#### `polygon()` {.collection-method}

`polygon` 메소드는 `POLYGON`에 해당하는 컬럼을 생성합니다.

    $table->polygon('position');

<a name="column-method-rememberToken"></a>
#### `rememberToken()` {.collection-method}

`rememberToken` 메소드는 현재 "remember me"[authentication token](/docs/{{version}}/authentication#remembering-users)를 저장하기 위한 null 허용 `VARCHAR(100)` 컬럼을 생성합니다.

    $table->rememberToken();

<a name="column-method-set"></a>
#### `set()` {.collection-method}

`set` 메소드는 유효한 값의 주어진 목록으로 `SET`에 해당하는 컬럼을 생성합니다.

    $table->set('flavors', ['strawberry', 'vanilla']);

<a name="column-method-smallIncrements"></a>
#### `smallIncrements()` {.collection-method}

`smallIncrements` 메소드는 기본 키로 자동 증가하는 `UNSIGNED SMALLINT`에 해당하는 컬럼을 생성합니다.

    $table->smallIncrements('id');

<a name="column-method-smallInteger"></a>
#### `smallInteger()` {.collection-method}

`smallInteger` 메소드는 `SMALLINT`에 해당하는 컬럼을 생성합니다.

    $table->smallInteger('votes');

<a name="column-method-softDeletesTz"></a>
#### `softDeletesTz()` {.collection-method}

`softDeletesTz` 메소드는 선택적 정밀도(총 자릿수)가 있는 nullable `deleted_at` `TIMESTAMP`(시간대 포함) 해당 컬럼을 추가합니다. 이 컬럼은 Eloquent의 "soft delete" 기능에 필요한 `deleted_at` 타임스탬프를 저장하기 위한 것입니다.

    $table->softDeletesTz($column = 'deleted_at', $precision = 0);

<a name="column-method-softDeletes"></a>
#### `softDeletes()` {.collection-method}

`softDeletes` 메소드는 선택적 정밀도(총 자릿수)를 사용하여 nullable `deleted_at` `TIMESTAMP`에 해당하는 컬럼을 추가합니다. 이 컬럼은 Eloquent의 "soft delete" 기능에 필요한 `deleted_at` 타임스탬프를 저장하기 위한 것입니다.


    $table->softDeletes($column = 'deleted_at', $precision = 0);

<a name="column-method-string"></a>
#### `string()` {.collection-method}

`string` 메소드는 주어진 길이의 `VARCHAR`에 해당하는 컬럼을 생성합니다.

    $table->string('name', 100);

<a name="column-method-text"></a>
#### `text()` {.collection-method}

`text` 메소드는 `TEXT`에 해당하는 컬럼을 생성합니다.

    $table->text('description');

<a name="column-method-timeTz"></a>
#### `timeTz()` {.collection-method}

`timeTz` 메소드는 선택적 정밀도(총 자릿수)를 사용하여 `TIME`(시간대 포함)에 해당하는 컬럼을 생성합니다.

    $table->timeTz('sunrise', $precision = 0);

<a name="column-method-time"></a>
#### `time()` {.collection-method}

`time` 메소드는 선택적 정밀도(총 자릿수)를 사용하여 `TIME`에 해당하는 컬럼을 생성합니다.

    $table->time('sunrise', $precision = 0);

<a name="column-method-timestampTz"></a>
#### `timestampTz()` {.collection-method}

`timestampTz` 메소드는 선택적 정밀도(총 자릿수)를 사용하여 `TIMESTAMP`(시간대 포함)에 해당하는 컬럼을 생성합니다.

    $table->timestampTz('added_at', $precision = 0);

<a name="column-method-timestamp"></a>
#### `timestamp()` {.collection-method}

`timestamp` 메소드는 선택적 정밀도(총 자릿수)를 사용하여 `TIMESTAMP`에 해당하는 컬럼을 생성합니다.

    $table->timestamp('added_at', $precision = 0);

<a name="column-method-timestampsTz"></a>
#### `timestampsTz()` {.collection-method}

`timestampsTz` 메소드는 선택적 정밀도(총 자릿수)를 사용하여 `created_at` 및 `updated_at` `TIMESTAMP`(시간대 포함)에 해당하는 컬럼을 생성합니다.

    $table->timestampsTz($precision = 0);

<a name="column-method-timestamps"></a>
#### `timestamps()` {.collection-method}

`timestamps` 메소드는 선택적 정밀도(총 자릿수)를 사용하여 `created_at` 및 `updated_at` `TIMESTAMP`에 해당하는 컬럼을 생성합니다.

    $table->timestamps($precision = 0);

<a name="column-method-tinyIncrements"></a>
#### `tinyIncrements()` {.collection-method}

`tinyIncrements` 메소드는 기본 키로 자동 증가하는 `UNSIGNED TINYINT`에 해당하는 컬럼을 생성합니다.

    $table->tinyIncrements('id');

<a name="column-method-tinyInteger"></a>
#### `tinyInteger()` {.collection-method}

`tinyInteger` 메소드는 `TINYINT`에 해당하는 컬럼을 생성합니다.

    $table->tinyInteger('votes');

<a name="column-method-tinyText"></a>
#### `tinyText()` {.collection-method}

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

`unsignedInteger` 메소드는 `UNSIGNED INTEGER`에 해당하는 컬럼을 생성합니다.

    $table->unsignedInteger('votes');

<a name="column-method-unsignedMediumInteger"></a>
#### `unsignedMediumInteger()` {.collection-method}

`unsignedMediumInteger` 메소드는 `UNSIGNED MEDIUMINT`에 해당하는 컬럼을 생성합니다.

    $table->unsignedMediumInteger('votes');

<a name="column-method-unsignedSmallInteger"></a>
#### `unsignedSmallInteger()` {.collection-method}

`unsignedSmallInteger` 메소드는 `UNSIGNED SMALLINT`에 해당하는 컬럼을 생성합니다.

    $table->unsignedSmallInteger('votes');

<a name="column-method-unsignedTinyInteger"></a>
#### `unsignedTinyInteger()` {.collection-method}

`unsignedTinyInteger` 메소드는 `UNSIGNED TINYINT`에 해당하는 컬럼을 생성합니다.

    $table->unsignedTinyInteger('votes');

<a name="column-method-uuidMorphs"></a>
#### `uuidMorphs()` {.collection-method}

`uuidMorphs` 메소드는 `{column}_id` `CHAR(36)`에 해당하는 컬럼과 `{column}_type` `VARCHAR`에 해당하는 컬럼을 추가하는 편리한 메소드입니다.

이 방법은 UUID 식별자를 사용하는 다형성 [Relationships - 관계](/docs/{{version}}/eloquent-relationships)에 필요한 컬럼을 정의할 때 사용하기 위한 것입니다. 다음 예에서는 `taggable_id` 및 `taggable_type` 컬럼이 생성됩니다.

    $table->uuidMorphs('taggable');

<a name="column-method-uuid"></a>
#### `uuid()` {.collection-method}

`uuid` 메소드는 `UUID`에 해당하는 컬럼을 생성합니다.

    $table->uuid('id');

<a name="column-method-year"></a>
#### `year()` {.collection-method}

`year` 메소드는 `YEAR`에 해당하는 컬럼을 생성합니다.

    $table->year('birth_year');

<a name="column-modifiers"></a>
### Column Modifiers

위에 나열된 컬럼 유형 외에도 데이터베이스 테이블에 컬럼을 추가할 때 사용할 수 있는 여러 컬럼 "수정자-modifier"가 있습니다. 예를 들어 컬럼을 "nullable"로 만들려면 `nullable` 메서드를 사용할 수 있습니다.

    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Support\Facades\Schema;

    Schema::table('users', function (Blueprint $table) {
        $table->string('email')->nullable();
    });

다음 표에는 사용 가능한 모든 컬럼 수정자가 포함되어 있습니다. 이 목록에는 [인덱스 생성하기](#creating-indexes)가 포함되어 있지 않습니다.

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
#### 기본 표현식

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

> {note} 기본 표현식 지원은 데이터베이스 드라이버, 데이터베이스 버전 및 필드 유형에 따라 다릅니다. 데이터베이스의 설명서를 참조하십시오.

<a name="column-order"></a>
#### 컬럼 순서

MySQL 데이터베이스를 사용할 때 `after` 메서드를 사용하여 스키마의 기존 컬럼 뒤에 컬럼을 추가할 수 있습니다.

    $table->after('password', function ($table) {
        $table->string('address_line1');
        $table->string('address_line2');
        $table->string('city');
    });

<a name="modifying-columns"></a>
### 컬럼 수정

<a name="prerequisites"></a>
#### 전제 조건

열을 수정하기 전에 Composer 패키지 관리자를 사용하여 `doctrine/dbal` 패키지를 설치해야 합니다. Doctrine DBAL 라이브러리는 컬럼의 현재 상태를 확인하고 컬럼에 요청된 변경을 수행하는 데 필요한 SQL 쿼리를 만드는 데 사용됩니다.

    composer require doctrine/dbal

`timestamp` 메소드를 사용하여 생성된 컬럼을 수정하려는 경우 애플리케이션의 `config/database.php` 구성 파일에 다음 구성도 추가해야 합니다.

```php
use Illuminate\Database\DBAL\TimestampType;

'dbal' => [
    'types' => [
        'timestamp' => TimestampType::class,
    ],
],
```

> {참고} 응용 프로그램이 Microsoft SQL Server를 사용하는 경우 `doctrine/dbal:^3.0`을 설치해야 합니다.

<a name="updating-column-attributes"></a>
#### 컬럼의 속성 변경하기

`change` 메소드는 이미 존재하는 컬럼 타입을 유형과 속성을 변경합니다. 예를 들어, `string` 컬럼의 사이즈를 늘이고 싶을 수 있습니다. `change` 메소드가 어떻게 작동하는지 `name` 컬럼 사이즈를 25에서 50으로 늘여서 확인해 보겠습니다. 이를 수행하기 위해 컬럼의 새 상태를 정의한 다음 `change` 메서드를 호출하기만 하면 됩니다.

    Schema::table('users', function (Blueprint $table) {
        $table->string('name', 50)->change();
    });

컬럼을 nullable로 수정할 수도 있습니다.

    Schema::table('users', function (Blueprint $table) {
        $table->string('name', 50)->nullable()->change();
    });

> {note} 수정할 수 있는 컬럼 유형은 `bigInteger`, `binary`, `boolean`, `date`, `dateTime`, `dateTimeTz`, `decimal`, `integer`, `json`, `longText입니다. `, `mediumText`, `smallInteger`, `string`, `text`, `time`, `unsignedBigInteger`, `unsignedInteger`, `unsignedSmallInteger` 및 `uuid`. `timestamp` 컬럼 유형을 수정하려면 [Doctrine 타입 등록 필요](#prerequisites).

<a name="renaming-columns"></a>
### 인덱스 이름 변경하기

컬럼의 이름을 바꾸려면 스키마 빌더 청사진(blueprint)에서 제공하는 `renameColumn` 메서드를 사용할 수 있습니다. 컬럼 이름을 바꾸기 전에 Composer 패키지 관리자를 통해 `doctrine/dbal` 라이브러리를 설치했는지 확인하세요:

    Schema::table('users', function (Blueprint $table) {
        $table->renameColumn('from', 'to');
    });

> {note} `enum` 컬럼의 이름을 바꾸는 것은 현재 지원되지 않습니다.

<a name="dropping-columns"></a>
### Dropping Columns

컬럼을 삭제하려면 스키마 빌더 청사진(blueprint)에서 `dropColumn` 메서드를 사용할 수 있습니다. 애플리케이션이 SQLite 데이터베이스를 사용하는 경우 `dropColumn` 메소드를 사용하기 전에 Composer 패키지 관리자를 통해 `doctrine/dbal` 패키지를 설치해야 합니다.

    Schema::table('users', function (Blueprint $table) {
        $table->dropColumn('votes');
    });

컬럼 이름의 배열을 `dropColumn` 메서드에 전달하여 테이블에서 여러 컬럼을 삭제할 수 있습니다.

    Schema::table('users', function (Blueprint $table) {
        $table->dropColumn(['votes', 'avatar', 'location']);
    });

> {note} SQLite 데이터베이스를 사용하는 동안 단일 마이그레이션 내에서 여러 컬럼을 삭제하거나 수정하는 것은 지원되지 않습니다.

<a name="available-command-aliases"></a>
#### 사용가능한 명령어 alias(별칭)

라라벨은 일반적인 유형의 컬럼 삭제와 관련된 몇 가지 편리한 방법을 제공합니다. 이러한 각 방법은 아래 표에 설명되어 있습니다.

명령어  |  설명
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

    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Support\Facades\Schema;

    Schema::table('users', function (Blueprint $table) {
        $table->string('email')->unique();
    });

또는 컬럼을 정의한 후 인덱스를 생성할 수 있습니다. 그렇게 하려면 스키마 빌더 청사진(blueprint)에서 `unique` 메서드를 호출해야 합니다. 이 메서드는 고유 인덱스를 받아야 하는 컬럼의 이름을 수락합니다.

    $table->unique('email');

인덱스 메소드에 컬럼의 배열을 전달하여 결합(복합) 인덱스를 생성할 수도 있습니다.

    $table->index(['account_id', 'created_at']);

When creating an index, Laravel will automatically generate an index name based on the table, column names, and the index type, but you may pass a second argument to the method to specify the index name yourself:

    $table->unique('email', 'unique_email');

라라벨은 테이블, 컬럼 이름 및 인덱스 유형을 기반으로 인덱스 이름을 자동으로 생성하지만 메소드의 두번째 인자로 인덱스 이름을 지정할 수도 있습니다.

<a name="available-index-types"></a>
#### 사용가능한 인덱스 타입

라라벨의 스키마 빌더 청사진(blueprint) 클래스는 라라벨이 지원하는 각 유형의 인덱스를 생성하기 위한 메소드를 제공합니다. 각 인덱스 메서드는 인덱스 이름을 지정하기 위해 선택적 두 번째 인수를 허용합니다. 생략하면 이름은 인덱스 유형뿐만 아니라 인덱스에 사용된 테이블 및 컬럼의 이름에서 파생됩니다. 사용 가능한 각 인덱스 방법은 아래 표에 설명되어 있습니다.

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
#### 인덱스 길이 & MySQL / MariaDB

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

또는 데이터베이스에 `innodb_large_prefix` 옵션을 활성화 할 수도 있습니다. 이 옵션을 제대로 사용하는 방법은 데이터베이스 매뉴얼을 참고하십시오.

<a name="renaming-indexes"></a>
### 인덱스 이름 변경하기

인덱스의 이름을 변경하기 위해서는 `renameIndex` 메소드를 사용하면 됩니다. 이 메소드는 현재의 인덱스 이름을 첫번째 인자로, 변경하고자 하는 새이름을 두번째 인자로 전달받습니다.

    $table->renameIndex('from', 'to')

<a name="dropping-indexes"></a>
### 인덱스 삭제하기

인덱스를 삭제하기 위해서는 인덱스의 이름을 지정해야 합니다. 기본적으로 라라벨은 테이블 이름, 인덱스된 컬럼의 이름, 그리고 인덱스 타입을 기반으로 자동으로 인덱스의 이름을 부여하도록 설정되어 있습니다. 다음은 몇 개의 예제 입니다.

명령어  |  설명
-------------  |  -------------
`$table->dropPrimary('users_id_primary');`  |  "users" 테이블에서 프라이머리 키 지우기.
`$table->dropUnique('users_email_unique');`  |  "users" 테이블에서 유니크 인덱스 지우기.
`$table->dropIndex('geo_state_index');`  |  "geo" 테이블에서 기본적인 인덱스 지우기.
`$table->dropSpatialIndex('geo_location_spatialindex');`  |  "geo" 테이블에서 공간(spatial) 인덱스 지우기(SQLite 제외).

인덱스들을 삭제하기 위해서 메소드에 컬럼의 배열을 전달하게 되면 인덱스의 이름은 테이블명, 컬럼명 그리고 키의 타입을 기반으로 컨벤션에 의해서 인덱스 이름을 추정할 것입니다.

    Schema::table('geo', function (Blueprint $table) {
        $table->dropIndex(['state']); // Drops index 'geo_state_index'
    });

<a name="foreign-key-constraints"></a>
### 외래키 제약조건(Constraints)

라라벨은 데이터베이스에서 또한 참조 무결성을 강제하는 외래 키 제약조건을 생성하는 것을 제공합니다. 예를 들어 `users` 테이블의 `id` 컬럼을 참조하는 `posts` 테이블에 `user_id` 컬럼을 정의해보겠습니다.

    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Support\Facades\Schema;

    Schema::table('posts', function (Blueprint $table) {
        $table->unsignedBigInteger('user_id');

        $table->foreign('user_id')->references('id')->on('users');
    });

이 구문은 다소 장황하기 때문에 라라벨은 더 나은 개발자 경험을 제공하기 위해 규칙을 사용하는 간결한 추가 메서드를 제공합니다. `foreignId` 메소드를 사용하여 컬럼을 생성할 때 위의 예는 다음과 같이 다시 작성할 수 있습니다.

    Schema::table('posts', function (Blueprint $table) {
        $table->foreignId('user_id')->constrained();
    });

`foreignId` 메서드는 `UNSIGNED BIGINT`에 해당하는 컬럼을 생성하는 반면, `constrained` 메서드는 규칙을 사용하여 참조되는 테이블과 컬럼 이름을 결정합니다. 테이블 이름이 라라벨의 규칙과 일치하지 않으면 `constrained` 메소드에 인수로 전달하여 테이블 이름을 지정할 수 있습니다.

    Schema::table('posts', function (Blueprint $table) {
        $table->foreignId('user_id')->constrained('users');
    });

제약조건(constraint)의 "on delete"와 "on update" 속성에 원하는 동작을 지정할 수도 있습니다.

    $table->foreignId('user_id')
          ->constrained()
          ->onUpdate('cascade')
          ->onDelete('cascade');

다음 작업에 대한 대안적인 표현 구문도 제공됩니다.

메소드  |  설명
-------  |  -----------
`$table->cascadeOnUpdate();` | 참조하는 테이블의 수정이 같이
`$table->restrictOnUpdate();`| 참조하는 테이블이 있으면 수정 불가
`$table->cascadeOnDelete();` | 참조하는 테이블의 삭제가 같이
`$table->restrictOnDelete();`| 참조하는 테이블이 있으면 삭제 불가
`$table->nullOnDelete();`    | 참조하는 테이블의 데이타 NULL로 변경

모든 추가 [컬럼 수정자-modifiers](#column-modifiers)는 `constrained` 메서드보다 먼저 호출되어야 합니다.

    $table->foreignId('user_id')
          ->nullable()
          ->constrained();

<a name="dropping-foreign-keys"></a>
#### 외래 키 삭제

외래 키를 삭제하려면 삭제할 외래 키 제약 조건의 이름을 인수로 전달하여 `dropForeign` 메서드를 사용할 수 있습니다. 외래 키 제약 조건은 인덱스와 동일한 명명 규칙을 사용합니다. 즉, 외래 키 제약 조건 이름은 테이블 이름과 제약 조건에 있는 컬럼을 기반으로 하며 그 뒤에 "\_foreign" 접미사가 붙습니다.

    $table->dropForeign('posts_user_id_foreign');

또는 외래 키를 포함하는 컬럼 이름을 포함하는 배열을 `dropForeign` 메서드에 전달할 수 있습니다. 배열은 라라벨의 제약 조건 명명 규칙을 사용하여 외래 키 제약 조건 이름으로 변환됩니다.

    $table->dropForeign(['user_id']);

<a name="toggling-foreign-key-constraints"></a>
#### 외래 키 제약 조건 토글

다음 방법을 사용하여 마이그레이션 내에서 외래 키 제약 조건을 활성화하거나 비활성화할 수 있습니다.

    Schema::enableForeignKeyConstraints();

    Schema::disableForeignKeyConstraints();

> {note} SQLite는 기본적으로 외래 키 제약 조건을 비활성화합니다. SQLite를 사용하는 경우 마이그레이션에서 생성을 시도하기 전에 데이터베이스 구성에서 [외래 키 지원 활성화](/docs/{{version}}/database#configuration)를 확인하십시오. 또한 SQLite는 테이블 생성 시에만 외래 키를 지원하며 [테이블이 변경되는 경우는 지원하지 않음](https://www.sqlite.org/omitted.html).

<a name="events"></a>
## 이벤트

편의를 위해 각 마이그레이션 작업은 [이벤트](/docs/{{version}}/events)를 전달합니다. 다음의 모든 이벤트는 기본 `Illuminate\Database\Events\MigrationEvent` 클래스를 확장합니다.

 클래스 | 설명               
-------|-------
| `Illuminate\Database\Events\MigrationsStarted` | 일괄 마이그레이션 실행 시작시 |
| `Illuminate\Database\Events\MigrationsEnded` | 일괄 마이그레이션 실행 종료후 |
| `Illuminate\Database\Events\MigrationStarted` | 단일 마이그레이션 실행 시작시 |
| `Illuminate\Database\Events\MigrationEnded` | 단일 마이그레이션 실행 종료후 |