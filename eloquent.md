# Eloquent: 시작하기

- [시작하기](#introduction)
- [모델 클래스 생성](#generating-model-classes)
- [Eloquent 모델 컨벤션](#eloquent-model-conventions)
    - [테이블 이름](#table-names)
    - [기본 키](#primary-keys)
    - [타임스탬프](#timestamps)
    - [데이터베이스 연결](#database-connections)
    - [기본 속성 값](#default-attribute-values)
- [모델 조회하기](#retrieving-models)
    - [컬렉션](#collections)
    - [결과 분할하기](#chunking-results)
    - [스트리밍 결과 지연](#streaming-results-lazily)
    - [커서](#cursors)
    - [고급 서브쿼리](#advanced-subqueries)
- [하나의 모델 / 집계 조회하기](#retrieving-single-models)
    - [모델 검색 또는 생성](#retrieving-or-creating-models)
    - [합계 조회하기](#retrieving-aggregates)
- [모델을 통한 추가 및 수정하기](#inserting-and-updating-models)
    - [Inserts](#inserts)
    - [Updates](#updates)
    - [대량 할당 - Mass Assignment](#mass-assignment)
    - [Upserts](#upserts)
- [모델 삭제하기](#deleting-models)
    - [소프트 삭제하기](#soft-deleting)
    - [소프트 삭제된 모델 쿼리하기](#querying-soft-deleted-models)
- [가지치기 모델](#pruning-models)
- [모델 복제](#replicating-models)
- [쿼리 스코프](#query-scopes)
    - [글로벌 스코프](#global-scopes)
    - [로컬 스코프](#local-scopes)
- [모델의 비교](#comparing-models)
- [이벤트](#events)
    - [클로저 사용하기](#events-using-closures)
    - [옵저버](#observers)
    - [이벤트 끄기(Muting Events)](#muting-events)

<a name="introduction"></a>
## 시작하기

라라벨에는 데이터베이스와 상호작용하는 것을 즐겁게 해주는 객체 관계 매퍼(ORM)인 Eloquent가 포함되어 있습니다. Eloquent를 사용할 때, 각 데이터베이스 테이블은 해당 테이블과 상호 작용하기 위해 사용되는 "모델(Model)"을 가집니다. 데이터베이스 테이블에서 레코드를 검색할 뿐만 아니라 Eloquent 모델을 사용하면 테이블에 레코드를 추가, 업데이트 및 삭제를 할 수 있습니다.

> {tip} 시작하기 전에, 애플리케이션의 `config/database.php` 구성 파일에서 데이터베이스 연결을 구성해야 합니다. 데이터베이스 구성에 대한 자세한 내용은 [데이터베이스 구성 문서](/docs/{{version}}/database#configuration)를 확인하세요.

<a name="generating-model-classes"></a>
## 모델 클래스 생성

시작하기 위해 Eloquent 모델을 만들어 봅시다. 모델은 일반적으로 `app\Models` 디렉토리에 있으며 `Illuminate\Database\Eloquent\Model` 클래스를 확장합니다. `make:model` [Artisan 명령](/docs/{version}/artisan)을 사용하여 새 모델을 생성할 수 있습니다.


    php artisan make:model Flight

모델을 생성할 때 [데이터베이스 마이그레이션](/docs/{{version}}/migrations)을 생성하고 싶다면 `--migration` 혹은 `-m` 옵션을 사용할 수 있습니다.

    php artisan make:model Flight --migration

모델을 생성할 때 팩토리, 시더, 정책, 컨트롤러 및 Form 요청-request과 같이 다양하고 서로 다른 유형의 클래스를 생성할 수 있습니다. 또한 다음 옵션을 결합하여 한 번에 여러 클래스를 만들 수 있습니다.

```bash
# Generate a model and a FlightFactory class...
php artisan make:model Flight --factory
php artisan make:model Flight -f

# Generate a model and a FlightSeeder class...
php artisan make:model Flight --seed
php artisan make:model Flight -s

# Generate a model and a FlightController class...
php artisan make:model Flight --controller
php artisan make:model Flight -c

# Generate a model, FlightController resource class, and form request classes...
php artisan make:model Flight --controller --resource --requests
php artisan make:model Flight -crR

# Generate a model and a FlightPolicy class...
php artisan make:model Flight --policy

# Generate a model and a migration, factory, seeder, and controller...
php artisan make:model Flight -mfsc

# Shortcut to generate a model, migration, factory, seeder, policy, controller, and form requests...
php artisan make:model Flight --all

# Generate a pivot model...
php artisan make:model Member --pivot
```

<a name="eloquent-model-conventions"></a>
## Eloquent 모델 컨벤션

`make:model` 명령으로 생성된 모델은 `app/Models` 디렉토리에 배치됩니다. 기본 모델 클래스를 살펴보고 Eloquent의 주요 규칙에 대해 논의해 보겠습니다.


    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class Flight extends Model
    {
        //
    }

<a name="table-names"></a>
### 테이블 이름

위의 예를 보면, 우리가 Eloquent에게 우리의 `Flight` 모델에 해당하는 데이터베이스 테이블의 이름을 알려주지 않았다는 것을 눈치채셨을 것입니다. 규칙에 따라 다른 이름이 명시적으로 지정되지 않는 한 클래스의 복수 이름인 "snake case"가 테이블 이름으로 사용됩니다. 따라서 이 경우 Eloquent는 `Flight` 모델이 `flights` 테이블에 레코드를 저장하는 반면 `AirTrafficController` 모델은 `air_traffic_controllers` 테이블에 레코드를 저장한다고 가정합니다.

모델의 해당 데이터베이스 테이블이 이 규칙에 맞지 않으면 모델에 `table` 속성을 정의하여 모델의 테이블 이름을 수동으로 지정할 수 있습니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class Flight extends Model
    {
        /**
         * The table associated with the model.
         *
         * @var string
         */
        protected $table = 'my_flights';
    }

<a name="primary-keys"></a>
### 기본 키

Eloquent 또한 각 모델의 해당 데이터베이스 테이블에 `id`라는 이름의 기본키 컬럼이 있다고 가정합니다. 필요한 경우 모델에 보호된 `$primaryKey` 속성을 정의하여 모델의 기본키로 사용되는 다른 칼럼ㅁ을 지정할 수 있습니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class Flight extends Model
    {
        /**
         * The primary key associated with the table.
         *
         * @var string
         */
        protected $primaryKey = 'flight_id';
    }

또한 Eloquent는 기본키가 증가하는 정수 값이라고 가정합니다. 이는 Eloquent가 자동으로 기본키를 정수로 변환한다는 것을 의미합니다. 증가하지 않거나 숫자가 아닌 기본키를 사용하려면 모델에서 `false`로 설정된 공개 `$incrementing` 속성을 정의해야 합니다.

    <?php

    class Flight extends Model
    {
        /**
         * Indicates if the model's ID is auto-incrementing.
         *
         * @var bool
         */
        public $incrementing = false;
    }

모델의 기본키가 정수가 아닌 경우 모델에 보호된 `$keyType` 속성을 정의해야 합니다. 이 속성은 `string` 값을 가져야 합니다.

    <?php

    class Flight extends Model
    {
        /**
         * The data type of the auto-incrementing ID.
         *
         * @var string
         */
        protected $keyType = 'string';
    }

<a name="composite-primary-keys"></a>
#### "Composite" 기본 키

Eloquent는 각 모델에 기본키 역할을 할 수 있는 고유하게 식별되는 "ID"가 하나 이상 있어야 합니다. "결합(Composite)" 기본키는 Eloquent 모델에서 지원되지 않습니다. 그러나 테이블을 고유하게 식별하는 기본키 외에 여러 열의 고유 인덱스를 데이터베이스 테이블에 자유롭게 추가할 수 있습니다.

<a name="timestamps"></a>
### 타임스탬프

기본적으로 Eloquent는 `created_at` 및 `updated_at` 컬럼이 모델의 해당 데이터베이스 테이블에 존재할 것으로 예상합니다. Eloquent는 모델이 생성되거나 업데이트될 때 이 컬럼의 값을 자동으로 설정합니다. 이러한 컬럼이 Eloquent에 의해 자동으로 관리되는 것을 원하지 않으면 모델에 `$timestamps` 속성을 `false` 값으로 정의해야 합니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class Flight extends Model
    {
        /**
         * Indicates if the model should be timestamped.
         *
         * @var bool
         */
        public $timestamps = false;
    }

모델의 타임스탬프 형식을 커스텀해야 하는 경우 모델에서 `$dateFormat` 속성을 설정하세요. 이 속성은 모델이 배열 또는 JSON으로 직렬화될 때 날짜 속성이 데이터베이스에 저장되는 방식과 형식을 결정합니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class Flight extends Model
    {
        /**
         * The storage format of the model's date columns.
         *
         * @var string
         */
        protected $dateFormat = 'U';
    }

타임스탬프를 저장하는 데 사용되는 컬럼의 이름을 커스텀 해야 하는 경우 모델에 `CREATED_AT` 및 `UPDATED_AT` 상수를 정의할 수 있습니다.

    <?php

    class Flight extends Model
    {
        const CREATED_AT = 'creation_date';
        const UPDATED_AT = 'updated_date';
    }

<a name="database-connections"></a>
### 데이터베이스 연결

기본적으로 모든 Eloquent 모델은 애플리케이션에 대해 구성된 데이터베이스를 기본으로 연결하여 사용합니다. 특정 모델과 상호 작용할 때 사용할 다른 데이터베이스를 연결하려면 모델에 `$connection` 속성을 정의해야 합니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class Flight extends Model
    {
        /**
         * The database connection that should be used by the model.
         *
         * @var string
         */
        protected $connection = 'sqlite';
    }

<a name="default-attribute-values"></a>
### 기본 속성 값

기본적으로 새로 인스턴스화된 모델 인스턴스에는 속성 값이 포함되지 않습니다. 일부 모델 속성에 대한 기본값을 정의하려면 모델에 `$attributes` 속성을 정의할 수 있습니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class Flight extends Model
    {
        /**
         * The model's default values for attributes.
         *
         * @var array
         */
        protected $attributes = [
            'delayed' => false,
        ];
    }

<a name="retrieving-models"></a>
## 모델 조회하기

모델과 [관련 데이터베이스 테이블](/docs/{{version}}/migrations#writing-migrations)을 생성했으면 데이터베이스에서 데이터 검색을 시작할 준비가 된 것입니다. 각 Eloquent 모델을 강력한 [쿼리 빌더](/docs/{{version}}/queries)로 생각하면 모델과 관련된 데이터베이스 테이블을 강력하게 쿼리할 수 있습니다. 모델의 `all` 메소드는 모델의 연결된 데이터베이스 테이블에서 모든 레코드를 검색합니다.

    use App\Models\Flight;

    foreach (Flight::all() as $flight) {
        echo $flight->name;
    }

<a name="building-queries"></a>
#### 쿼리 작성

Eloquent의 `all` 메소드는 모델의 테이블에 있는 모든 결과를 반환합니다. 그러나 각 Eloquent 모델은 [쿼리 빌더](/docs/{{version}}/queries) 역할을 하기 때문에 쿼리에 추가 제약 조건을 추가한 다음 `get` 메소드를 호출하여 결과를 검색할 수 있습니다.

    $flights = Flight::where('active', 1)
                   ->orderBy('name')
                   ->take(10)
                   ->get();

> {tip} Eloquent 모델은 쿼리 빌더이므로 라라벨의 [쿼리 빌더](/docs/{{version}}/queries)에서 제공하는 모든 메소드를 검토해야 합니다. Eloquent 쿼리를 작성할 때 이러한 방법 중 하나를 사용할 수 있습니다.

<a name="refreshing-models"></a>
#### 모델의 리프레쉬

데이터베이스에서 가져온 Eloquent 모델의 인스턴스가 이미 있는 경우 `fresh` 및 `refresh` 메소드를 사용하여 모델을 "리프레쉬"할 수 있습니다. `fresh` 메소드는 데이터베이스에서 모델을 다시 검색합니다. 기존 모델 인스턴스는 영향을 받지 않습니다.

    $flight = Flight::where('number', 'FR 900')->first();

    $freshFlight = $flight->fresh();

`refresh` 메소드는 데이터베이스의 최신 데이터를 사용하여 기존 모델을 다시 갱신합니다. 또한 로드된 모든 관계도 새로 초기화 됩니다.

    $flight = Flight::where('number', 'FR 900')->first();

    $flight->number = 'FR 456';

    $flight->refresh();

    $flight->number; // "FR 900"

<a name="collections"></a>
### 컬렉션

우리가 보았듯이 `all` 및 `get`과 같은 Eloquent 메소드는 데이터베이스에서 여러 레코드를 검색합니다. 그러나 이러한 메소드는 일반 PHP 배열을 반환하지 않습니다. 대신 `Illuminate\Database\Eloquent\Collection`의 인스턴스가 반환됩니다.

Eloquent `Collection` 클래스는 데이터 컬렉션과 상호작용하기 위해 [다양하고 유용한 메소드](/docs/{{version}}/collections#available-methods)를 제공하는 라라벨의 기본 `Illuminate\Support\Collection` 클래스를 확장합니다. 예를 들어, `reject` 메소드는 호출된 클로저의 결과를 기반으로 컬렉션에서 모델을 제거하는 데 사용할 수 있습니다.

```php
$flights = Flight::where('destination', 'Paris')->get();

$flights = $flights->reject(function ($flight) {
    return $flight->cancelled;
});
```

라라벨의 기본 컬렉션 클래스에서 제공하는 메소드 외에도 Eloquent 컬렉션 클래스는 Eloquent 모델 컬렉션과 상호 작용하기 위해 특별히 고안된 [몇 가지 추가 메소드](/docs/{{version}}/eloquent-collections#available-methods)를 제공합니다.

라라벨의 모든 컬렉션은 PHP의 반복 가능한 인터페이스를 구현하므로 컬렉션을 배열인 것처럼 반복할 수 있습니다.

```php
foreach ($flights as $flight) {
    echo $flight->name;
}
```

<a name="chunking-results"></a>
### 결과 분할하기

`all` 또는 `get` 메소드를 통해 수만 개의 Eloquent 레코드를 로드하려고 하면 애플리케이션의 메모리가 부족할 수 있습니다. 이러한 방법을 사용하는 대신 `chunk` 메소드를 사용하여 많은 수의 모델을 보다 효율적으로 처리할 수 있습니다.

`chunk` 메소드는 Eloquent 모델의 하위 집합을 검색하여 처리하기 위해 클로저에 전달합니다. Eloquent 모델의 현재 청크(chunk)만 한 번에 검색되기 때문에 `chunk` 메소드는 많은 수의 모델로 작업할 때 메모리 사용량을 크게 줄입니다.

```php
use App\Models\Flight;

Flight::chunk(200, function ($flights) {
    foreach ($flights as $flight) {
        //
    }
});
```

`chunk` 메소드에 전달된 첫 번째 인수는 "청크" 당 수신하려는 레코드 수 입니다. 두 번째 인수로 전달된 클로저는 데이터베이스에서 검색된 각 청크에 대해 호출됩니다. 데이터베이스 쿼리가 실행되어 클로저에 전달된 각 레코드 청크를 검색합니다.

결과를 반복하는 동안 업데이트할 컬럼을 기반으로 `chunk` 메소드의 결과를 필터링하는 경우 `chunkById` 메소드를 사용해야 합니다. 이러한 시나리오에서 `chunk` 메소드를 사용하면 예상치 못한 일관성 없는 결과가 발생할 수 있습니다. 내부적으로 `chunkById` 메소드는 항상 이전 청크의 마지막 모델보다 큰 `id` 컬럼을 가진 모델을 검색합니다.

```php
Flight::where('departed', true)
    ->chunkById(200, function ($flights) {
        $flights->each->update(['departed' => false]);
    }, $column = 'id');
```

<a name="streaming-results-lazily"></a>
### 스트리밍 결과 지연

`lazy` 메소드는 배후에서 청크로 쿼리를 실행한다는 점에서 [`chunk` 메소드](#chunking-results)와 유사하게 작동합니다. 그러나 각 청크를 그대로 콜백에 전달하는 대신, `lazy` 메소드는 Eloquent 모델의 평평한 [`LazyCollection`](/docs/{{version}}/collections#lazy-collections)을 반환하고 결과와 단일 스트림으로 상호 작용할 수 있습니다.

```php
use App\Models\Flight;

foreach (Flight::lazy() as $flight) {
    //
}
```

결과를 반복하는 동안 업데이트할 칼럼을 기반으로 `lazy` 메소드의 결과를 필터링하는 경우 `lazyById` 메소드를 사용해야 합니다. 내부적으로 `lazyById` 메소드는 항상 이전 청크의 마지막 모델보다 큰 `id` 컬럼을 가진 모델을 검색합니다.

```php
Flight::where('departed', true)
    ->lazyById(200, $column = 'id')
    ->each->update(['departed' => false]);
```

`lazyByIdDesc` 메소드를 사용하여 `id`의 내림차순으로 결과를 필터링할 수 있습니다.

<a name="cursors"></a>
### 커서

`lazy` 메소드와 유사하게 `cursor` 메소드는 수만 개의 Eloquent 모델 레코드를 반복할 때 애플리케이션의 메모리 소비를 크게 줄이는 데 사용할 수 있습니다.

`cursor` 메소드는 단일 데이터베이스 쿼리만 실행합니다. 그러나 개별 Eloquent 모델은 실제로 반복될 때까지 수화되지 않습니다. 따라서 커서를 반복하는 동안 주어진 시간에 하나의 Eloquent 모델만 메모리에 유지됩니다.

> {note} `cursor` 메소드는 한 번에 하나의 Eloquent 모델만 메모리에 보유하므로 관계-relationships를 바로 로드할 수 없습니다. 관계를 즉시 로드해야 하는 경우 [`lazy` 메소드](#streaming-results-lazily)를 대신 사용하는 것이 좋습니다.

내부적으로 `cursor` 메소드는 PHP [generators](https://www.php.net/manual/en/language.generators.overview.php)를 사용하여 이 기능을 구현합니다.

```php
use App\Models\Flight;

foreach (Flight::where('destination', 'Zurich')->cursor() as $flight) {
    //
}
```

`cursor`는 `Illuminate\Support\LazyCollection` 인스턴스를 반환합니다. [Lazy collections](/docs/{{version}}/collections#lazy-collections)를 사용하면 한 번에 단일 모델만 메모리에 로드하면서 일반적인 라라벨 컬렉션에서 사용할 수 있는 많은 컬렉션 메소드를 사용할 수 있습니다.

```php
use App\Models\User;

$users = User::cursor()->filter(function ($user) {
    return $user->id > 500;
});

foreach ($users as $user) {
    echo $user->id;
}
```

비록 `cursor` 메소드가 일반 쿼리보다 훨씬 적은 메모리를 사용하지만(한 번에 하나의 Eloquent 모델만 메모리에 유지함으로써), 결국에는 여전히 메모리가 부족합니다. 이는 [PHP의 PDO 드라이버가 내부적으로 모든 원시 쿼리 결과를 버퍼에 캐싱하기 때문입니다](https://www.php.net/manual/en/mysqlinfo.concepts.buffering.php). 대신 매우 많은 수의 Eloquent 레코드를 처리하는 경우 [`lazy` 메소드](#streaming-results-lazily) 사용을 고려하십시오.

<a name="advanced-subqueries"></a>
### 고급 서브쿼리

<a name="subquery-selects"></a>
#### 서브쿼리 선택

Eloquent 는 고급 서브쿼리 기능을 지원하므로 단일 쿼리에서 관련 테이블에 있는 정보를 가져올 수 있습니다. 예를 들어, 우리는 목적지까지 가기 위해 `destinations` 테이블과 `flights` 테이블이 있다고 가정 해봅시다. `flights` 테이블에는 항공편이 목적지에 도착한 시점을 나타내는 `arrived_at` 컬럼이 있습니다.

쿼리 빌더의 `select` 및 `addSelect` 메소드에 사용할 수 있는 서브 쿼리 기능을 사용하면 단일 쿼리를 사용하여 모든 `destinations`와 해당 목적지에 가장 최근에 도착한 항공편 이름을 선택할 수 있습니다.

    use App\Models\Destination;
    use App\Models\Flight;

    return Destination::addSelect(['last_flight' => Flight::select('name')
        ->whereColumn('destination_id', 'destinations.id')
        ->orderByDesc('arrived_at')
        ->limit(1)
    ])->get();

<a name="subquery-ordering"></a>
#### 서브쿼리 정렬

또한 쿼리 빌더의 `orderBy` 기능은 서브 쿼리를 지원합니다. 비행 예제를 계속 사용하면 이 기능을 사용하여 마지막 항공편이 해당 목적지에 도착한 시간을 기준으로 모든 목적지를 정렬할 수 있습니다. 다시 말하지만, 이것은 단일 데이터베이스 쿼리를 실행하는 동안 수행될 수 있습니다.

    return Destination::orderByDesc(
        Flight::select('arrived_at')
            ->whereColumn('destination_id', 'destinations.id')
            ->orderByDesc('arrived_at')
            ->limit(1)
    )->get();

<a name="retrieving-single-models"></a>
## 하나의 모델 / 집계 조회하기

주어진 쿼리와 일치하는 모든 레코드를 검색하는 것 외에도 `find`, `first` 또는 `firstWhere` 메소드를 사용하여 단일 레코드를 검색할 수도 있습니다. 모델 컬렉션을 반환하는 대신 다음 메소드는 단일 모델 인스턴스를 반환합니다.

    use App\Models\Flight;

    // Retrieve a model by its primary key...
    $flight = Flight::find(1);

    // Retrieve the first model matching the query constraints...
    $flight = Flight::where('active', 1)->first();

    // Alternative to retrieving the first model matching the query constraints...
    $flight = Flight::firstWhere('active', 1);

때로는 쿼리의 첫 번째 결과를 검색하거나 결과가 없는 경우 다른 작업을 수행하고자 할 수 있습니다. `firstOr` 메소드는 쿼리와 일치하는 첫 번째 결과를 반환하거나 결과가 없으면 지정된 클로저를 실행합니다. 클로저에서 반환된 값은 `firstOr` 메소드의 결과로 간주됩니다.

    $model = Flight::where('legs', '>', 3)->firstOr(function () {
        // ...
    });

<a name="not-found-exceptions"></a>
#### Not Found Exceptions

때때로 모델을 찾을 수 없는 경우 예외를 throw하고 싶을 수 있습니다. 이것은 라우트나 컨트롤러에서 특히 유용합니다. `findOrFail` 및 `firstOrFail` 메소드는 쿼리의 첫 번째 결과를 검색합니다. 그러나 결과가 없으면 `Illuminate\Database\Eloquent\ModelNotFoundException`이 발생합니다.

    $flight = Flight::findOrFail(1);

    $flight = Flight::where('legs', '>', 3)->firstOrFail();

`ModelNotFoundException`이 포착되지 않으면 404 HTTP 응답이 자동으로 클라이언트로 다시 전송됩니다.

    use App\Models\Flight;

    Route::get('/api/flights/{id}', function ($id) {
        return Flight::findOrFail($id);
    });

<a name="retrieving-or-creating-models"></a>
### 모델 검색 또는 생성

`firstOrCreate` 메서드는 주어진 컬럼/값 쌍을 사용하여 데이터베이스 레코드를 찾습니다. 데이터베이스에서 모델을 찾을 수 없는 경우 첫 번째 배열 인수와 선택적 두 번째 배열 인수를 병합하여 얻은 특성을 가진 레코드가 삽입됩니다.

`firstOrCreate`와 같은 `firstOrNew` 메소드는 데이터베이스에서 주어진 속성과 일치하는 레코드를 찾으려고 시도합니다. 그러나 모델을 찾을 수 없는 경우 새 모델의 인스턴스가 반환됩니다. `firstOrNew`에 의해 반환된 모델은 아직 데이터베이스에 유지되지 않았습니다. 이를 유지하려면 `save` 메소드를 수동으로 호출해야 합니다.

    use App\Models\Flight;

    // Retrieve flight by name or create it if it doesn't exist...
    $flight = Flight::firstOrCreate([
        'name' => 'London to Paris'
    ]);

    // Retrieve flight by name or create it with the name, delayed, and arrival_time attributes...
    $flight = Flight::firstOrCreate(
        ['name' => 'London to Paris'],
        ['delayed' => 1, 'arrival_time' => '11:30']
    );

    // Retrieve flight by name or instantiate a new Flight instance...
    $flight = Flight::firstOrNew([
        'name' => 'London to Paris'
    ]);

    // Retrieve flight by name or instantiate with the name, delayed, and arrival_time attributes...
    $flight = Flight::firstOrNew(
        ['name' => 'Tokyo to Sydney'],
        ['delayed' => 1, 'arrival_time' => '11:30']
    );

<a name="retrieving-aggregates"></a>
### 합계 조회하기

Eloquent 모델과 상호 작용할 때 라라벨 [쿼리 빌더](/docs/{{version}}/queries#aggregates)에서 제공하는 `count`, `sum`, `max` 및 기타 집계 메소드를 사용할 수도 있습니다. 예상대로 이 메서드는 Eloquent 모델 인스턴스 대신 스칼라 값을 반환합니다.

    $count = Flight::where('active', 1)->count();

    $max = Flight::where('active', 1)->max('price');

<a name="inserting-and-updating-models"></a>
## 모델을 통한 추가 및 수정하기

<a name="inserts"></a>
### Inserts

물론 Eloquent를 사용할 때 데이터베이스에서 모델을 검색할 필요만 있는 것은 아닙니다. 또한 새 레코드를 삽입해야 합니다. 고맙게도 Eloquent는 이를 간단하게 만듭니다. 데이터베이스에 새 레코드를 삽입하려면 새 모델 인스턴스를 인스턴스화하고 모델에 속성을 설정해야 합니다. 그런 다음 모델 인스턴스에서 `save` 메소드를 호출합니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Models\Flight;
    use Illuminate\Http\Request;

    class FlightController extends Controller
    {
        /**
         * Store a new flight in the database.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return \Illuminate\Http\Response
         */
        public function store(Request $request)
        {
            // Validate the request...

            $flight = new Flight;

            $flight->name = $request->name;

            $flight->save();
        }
    }

위의 예제에서는 들어오는 HTTP 요청의 `name` 필드를 `App\Models\Flight` 모델 인스턴스의 `name` 속성에 할당합니다. `save` 메소드를 호출하면 레코드가 데이터베이스에 삽입됩니다. 모델의 `created_at` 및 `updated_at` 타임스탬프는 `save` 메소드가 호출될 때 자동으로 설정되므로 수동으로 설정할 필요가 없습니다.

또는 `create` 메소드를 사용하여 단일 PHP 문을 사용하여 새 모델을 "저장"할 수 있습니다. 삽입된 모델 인스턴스는 `create` 메소드에 의해 반환됩니다.

    use App\Models\Flight;

    $flight = Flight::create([
        'name' => 'London to Paris',
    ]);

그러나 `create` 메소드를 사용하기 전에 모델 클래스에 `fillable` 또는 `guarded` 속성을 지정해야 합니다. 모든 Eloquent 모델은 기본적으로 대량 할당 취약성으로부터 보호되기 때문에 이러한 속성이 필요합니다. 대량 할당에 대한 자세한 내용은 [대량 할당 문서](#mass-assignment)를 참조하세요.

<a name="updates"></a>
### Updates

`save` 메소드는 데이터베이스에 이미 존재하는 모델을 업데이트하는 데에도 사용할 수 있습니다. 모델을 업데이트하려면 모델을 검색하고 업데이트하려는 속성을 설정해야 합니다. 그런 다음 모델의 `save` 메소드를 호출해야 합니다. 이 때에도 `updated_at` 타임스탬프는 자동으로 업데이트 되어 설정되며 수동으로 값을 지정할 필요가 없습니다.

    use App\Models\Flight;

    $flight = Flight::find(1);

    $flight->name = 'Paris to London';

    $flight->save();

<a name="mass-updates"></a>
#### 여러개의 모델 Update

주어진 쿼리와 일치하는 모델에 대해 업데이트를 수행할 수도 있습니다. 이 예에서 `active` 상태이고 `destination` 컬럼의 값이 `San Diego`인 모든 항공편은 지연된 것으로 표시됩니다.

    Flight::where('active', 1)
          ->where('destination', 'San Diego')
          ->update(['delayed' => 1]);

`update` 메소드는 업데이트되어야 하는 컬럼을 나타내는 컬럼과 값 쌍의 배열을 예상합니다. `update` 메소드는 영향을 받는 행의 수를 반환합니다.

> {note} Eloquent를 통해 대량 업데이트를 발행할 때, `saving`, `saved`, `updating`, `updated` 모델 이벤트는 업데이트된 모델에 대해 실행되지 않습니다. 이는 대량 업데이트를 발행할 때 모델이 실제로 검색되지 않기 때문입니다.

<a name="examining-attribute-changes"></a>
#### 속성 변경 검토

Eloquent는 `isDirty`, `isClean`, `wasChanged` 메소드를 제공하여 모델의 내부 상태를 검사하고 모델이 처음 검색되었을 때부터 속성이 어떻게 변경되었는지 확인합니다.

`isDirty` 메소드는 모델이 검색된 이후 모델의 속성이 변경되었는지 확인합니다. 특정 속성이 더러운(dirty)지 확인하기 위해 `isDirty` 메소드에 특정 속성 이름을 전달할 수 있습니다. `isClean`은 모델이 검색된 이후 속성이 변경되지 않은 상태로 남아 있는지 확인합니다. 이 메소드는 선택적 속성 인수도 허용합니다.

    use App\Models\User;

    $user = User::create([
        'first_name' => 'Taylor',
        'last_name' => 'Otwell',
        'title' => 'Developer',
    ]);

    $user->title = 'Painter';

    $user->isDirty(); // true
    $user->isDirty('title'); // true
    $user->isDirty('first_name'); // false

    $user->isClean(); // false
    $user->isClean('title'); // false
    $user->isClean('first_name'); // true

    $user->save();

    $user->isDirty(); // false
    $user->isClean(); // true

`wasChanged` 메소드는 현재 요청 주기 내에서 모델이 마지막으로 저장되었을 때 속성이 변경되었는지 확인합니다. 필요한 경우 속성 이름을 전달하여 특정 속성이 변경되었는지 확인할 수 있습니다.

    $user = User::create([
        'first_name' => 'Taylor',
        'last_name' => 'Otwell',
        'title' => 'Developer',
    ]);

    $user->title = 'Painter';

    $user->save();

    $user->wasChanged(); // true
    $user->wasChanged('title'); // true
    $user->wasChanged('first_name'); // false

`getOriginal` 메소드는 검색된 이후의 모델 변경 사항에 관계없이 모델의 원래 속성을 포함하는 배열을 반환합니다. 필요한 경우 특정 속성 이름을 전달하여 특정 속성의 원래 값을 얻을 수 있습니다.

    $user = User::find(1);

    $user->name; // John
    $user->email; // john@example.com

    $user->name = "Jack";
    $user->name; // Jack

    $user->getOriginal('name'); // John
    $user->getOriginal(); // Array of original attributes...

<a name="mass-assignment"></a>
### 대량 할당 - Mass Assignment

단일 PHP 문을 사용하여 새 모델을 "저장"하기 위해 `create` 메소드를 사용할 수 있습니다. 삽입된 모델 인스턴스는 다음 방법으로 반환됩니다.

    use App\Models\Flight;

    $flight = Flight::create([
        'name' => 'London to Paris',
    ]);

그러나 `create` 메소드를 사용하기 전에 모델 클래스에 `fillable` 또는 `guarded` 속성을 지정해야 합니다. 모든 Eloquent 모델은 기본적으로 대량 할당 취약성으로부터 보호되기 때문에 이러한 속성이 필요합니다.

대량 할당 취약점은 사용자가 예기치 않은 HTTP 요청 필드를 전달하고 해당 필드가 데이터베이스에서 예상하지 못한 열을 변경할 때 발생합니다. 예를 들어, 악의적인 사용자는 HTTP 요청을 통해 `is_admin` 매개변수를 보낼 수 있으며, 이 매개변수는 모델의 `create` 메소드로 전달되어 사용자 자신을 관리자로 상승할 수 있습니다.

따라서 시작하기 위해서는 대량 할당(Mass Assignment)할 모델의 속성을 정의해야 하며 이는 모델에 `$fillable` 속성을 사용해서 할 수 있습니다. 예를 들어 `Flight` 모델에서 `name` 속성을 대량 할당(Mass Assignment)될 수 있도록 만들 수 있습니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class Flight extends Model
    {
        /**
         * The attributes that are mass assignable.
         *
         * @var array
         */
        protected $fillable = ['name'];
    }

대량 할당 가능한 속성을 지정했으면 `create` 메소드를 사용하여 데이터베이스에 새 레코드를 삽입할 수 있습니다. `create` 메소드는 새로 생성된 모델 인스턴스를 반환합니다.

    $flight = Flight::create(['name' => 'London to Paris']);

이미 모델 인스턴스를 가지고 있다면, `fill` 메소드에 배열을 전달하여 속성을 구성할 수 있습니다.

    $flight->fill(['name' => 'Amsterdam to Frankfurt']);

<a name="mass-assignment-json-columns"></a>
#### 대량 할당 및 JSON 열 - Mass Assignment & JSON Columns

JSON 컬럼을 할당할 때 각 컬럼의 대량 할당이 가능한 키를 모델의 `$fillable` 배열에 지정해야 합니다. 보안을 위해 라라벨은 `guarded` 속성을 사용할 때 중첩된 JSON 속성 업데이트를 지원하지 않습니다.

    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = [
        'options->enabled',
    ];

<a name="allowing-mass-assignment"></a>
#### 대량 할당 허용

모든 속성을 대량 할당 가능하게 만들고 싶다면 모델의 `$guarded` 속성을 빈 배열로 정의할 수 있습니다. 모델의 보호를 해제하기로 선택했다면 항상 Eloquent의 `fill`, `create`, `update` 메소드에 전달되는 배열을 손수 제작하도록 특별한 주의를 기울여야 합니다.

    /**
     * The attributes that aren't mass assignable.
     *
     * @var array
     */
    protected $guarded = [];

<a name="upserts"></a>
### Upserts

경우에 따라 기존 모델을 업데이트하거나 일치하는 모델이 없는 경우 새 모델을 생성해야 할 수 있습니다. `firstOrCreate` 메소드와 마찬가지로 `updateOrCreate` 메소드는 모델을 유지하므로 `save` 메소드를 수동으로 호출할 필요가 없습니다.

아래 예에서 `departure` 위치가 `Oakland`이고 `destination` 위치가 `San Diego`인 항공편이 있는 경우 해당 `price` 및 `discounted` 컬럼이 업데이트됩니다. 그러한 항공편이 존재하지 않으면 첫 번째 인수 배열을 두 번째 인수 배열과 병합한 결과 속성이 있는 새 항공편이 생성됩니다.

    $flight = Flight::updateOrCreate(
        ['departure' => 'Oakland', 'destination' => 'San Diego'],
        ['price' => 99, 'discounted' => 1]
    );

단일 쿼리에서 여러 "upsert"를 수행하려면 `upsert` 메소드를 사용해야 합니다. 메소드의 첫 번째 인수는 삽입하거나 업데이트할 값으로 구성되는 반면 두 번째 인수는 연결된 테이블 내에서 레코드를 고유하게 식별하는 컬럼을 나열합니다. 메소드의 세 번째이자 마지막 인수는 일치하는 레코드가 데이터베이스에 이미 있는 경우 업데이트해야 하는 컬럼의 배열입니다. `upsert` 메소드는 모델에서 타임스탬프가 활성화된 경우 `created_at` 및 `updated_at` 타임스탬프를 자동으로 설정합니다.

    Flight::upsert([
        ['departure' => 'Oakland', 'destination' => 'San Diego', 'price' => 99],
        ['departure' => 'Chicago', 'destination' => 'New York', 'price' => 150]
    ], ['departure', 'destination'], ['price']);

<a name="deleting-models"></a>
## 모델 삭제하기

모델을 삭제하려면 모델 인스턴스에서 `delete` 메소드를 호출하면 됩니다.

    use App\Models\Flight;

    $flight = Flight::find(1);

    $flight->delete();

모델과 관련된 모든 데이터베이스 레코드를 삭제하려면 `truncate` 메소드를 호출할 수 있습니다. `truncate` 작업은 모델의 연결된 테이블에서 자동 증가 ID도 재설정합니다.

    Flight::truncate();

<a name="deleting-an-existing-model-by-its-primary-key"></a>
#### 기본 키로 기존 모델 삭제

위의 예에서 우리는 `delete` 메소드를 호출하기 전에 데이터베이스에서 모델을 검색하고 있습니다. 그러나 모델의 기본 키를 알고 있는 경우 `destroy` 메소드를 호출하여 명시적으로 검색하지 않고 모델을 삭제할 수 있습니다. 단일 기본 키를 허용하는 것 외에도 `destroy` 메소드는 여러 기본 키, 기본 키 배열 또는 기본 키의 [컬렉션]](/docs/{{version}}/collections)을 허용합니다.

    Flight::destroy(1);

    Flight::destroy(1, 2, 3);

    Flight::destroy([1, 2, 3]);

    Flight::destroy(collect([1, 2, 3]));

> {note} `destroy` 메소드는 각 모델을 개별적으로 로드하고 `delete` 메소드를 호출하여 `deleting` 및 `deleted` 이벤트가 각 모델에 대해 적절하게 전달되도록 합니다.

<a name="deleting-models-using-queries"></a>
#### 쿼리를 사용하여 모델 삭제

물론, 쿼리 기준과 일치하는 모든 모델을 삭제하기 위해 Eloquent 쿼리를 작성할 수 있습니다. 이 예에서는 비활성으로 표시된 모든 항공편을 삭제합니다. 대량 업데이트와 마찬가지로 대량 삭제는 삭제된 모델에 대한 모델 이벤트를 전달하지 않습니다.

    $deleted = Flight::where('active', 0)->delete();

> {note} Eloquent를 통해 대량 삭제 명령문을 실행할 때 `deleting` 및 `deleted` 모델 이벤트는 삭제된 모델에 대해 전달되지 않습니다. 이는 delete 문을 실행할 때 모델이 실제로 검색되지 않기 때문입니다.

<a name="soft-deleting"></a>
### 소프트 삭제하기

실제로 데이터베이스에서 레코드를 제거하는 것 외에도 Eloquent는 모델을 소프트 삭제(일종의 임시 삭제)할 수도 있습니다. 모델이 일시 삭제되면 실제로 데이터베이스에서 제거되지 않습니다. 대신 모델이 "삭제"된 날짜와 시간을 나타내는 `deleted_at` 속성이 모델에 설정됩니다. 모델에 대한 일시 삭제를 활성화하려면 모델에 `Illuminate\Database\Eloquent\SoftDeletes` 특성을 추가하십시오:

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;
    use Illuminate\Database\Eloquent\SoftDeletes;

    class Flight extends Model
    {
        use SoftDeletes;
    }

> {tip} `SoftDeletes` 의 특성은 자동으로 `deleted_at` 의 속성을 `DateTime` / `Carbon` 인스턴스에 반영됩니다.

데이터베이스 테이블에 `deleted_at` 컬럼을 추가해야 합니다. 라라벨의 [스키마 빌더](/docs/{{version}}/migrations)는 이 컬럼을 생성하는 헬퍼 메소드를 가지고 있습니다.

    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Support\Facades\Schema;

    Schema::table('flights', function (Blueprint $table) {
        $table->softDeletes();
    });

    Schema::table('flights', function (Blueprint $table) {
        $table->dropSoftDeletes();
    });

이제 모델에서 `delete` 메소드를 호출하면 `deleted_at` 컬럼이 현재 날짜와 시간으로 설정됩니다. 그러나 모델의 데이터베이스 레코드는 테이블에 남습니다. 일시 삭제를 사용하는 모델을 쿼리할 때 일시 삭제된 모델은 모든 쿼리 결과에서 자동으로 제외됩니다.

주어진 모델 인스턴스가 일시 삭제되었는지 확인하려면 `trashed` 메소드를 사용할 수 있습니다.

    if ($flight->trashed()) {
        //
    }

<a name="restoring-soft-deleted-models"></a>
#### 일시 삭제된 모델 복원

경우에 따라 일시 삭제된 모델을 "삭제 취소"를 하고 싶을 수 있습니다. 일시 삭제된 모델을 복원하려면 모델 인스턴스에서 `restore` 메소드를 호출하면 됩니다. `restore` 메소드는 모델의 `deleted_at` 열을 `null`로 설정합니다.

    $flight->restore();

또한 쿼리에서 `restore` 메소드를 사용하여 여러 모델을 복원할 수 있습니다. 다시 말하지만, 다른 "대량" 작업과 마찬가지로 복원된 모델에 대한 모델 이벤트를 전달하지 않습니다.

    Flight::withTrashed()
            ->where('airline_id', 1)
            ->restore();

`restore` 메소드는 [관계(relationship)](/docs/{{version}}/eloquent-relationships) 쿼리를 작성할 때도 사용할 수 있습니다.

    $flight->history()->restore();

<a name="permanently-deleting-models"></a>
#### 영구 모델 삭제

때로는 데이터베이스에서 모델을 완전히 제거해야 할 수도 있습니다. `forceDelete` 메소드를 사용하여 데이터베이스 테이블에서 일시 삭제된 모델을 영구적으로 제거할 수 있습니다.

    $flight->forceDelete();

Eloquent 관계 쿼리를 작성할 때 `forceDelete` 메소드를 사용할 수도 있습니다.

    $flight->history()->forceDelete();

<a name="querying-soft-deleted-models"></a>
### 소프트 삭제된 모델 쿼리하기

<a name="including-soft-deleted-models"></a>
#### 소프트 삭제된 모델 포함하기

위에서 언급했듯이 일시 삭제된 모델은 쿼리 결과에서 자동으로 제외됩니다. 그러나 쿼리에서 `withTrashed` 메소드를 호출하여 일시 삭제된 모델을 쿼리 결과에 강제로 포함할 수 있습니다.

    use App\Models\Flight;

    $flights = Flight::withTrashed()
                    ->where('account_id', 1)
                    ->get();

`withTrashed` 메소드는 [관계(relationship)](/docs/{{version}}/eloquent-relationships) 쿼리를 빌드할 때도 호출될 수 있습니다.

    $flight->history()->withTrashed()->get();

<a name="retrieving-only-soft-deleted-models"></a>
#### 소프트 삭제된 모델만 가져오기

`onlyTrashed` 메소드는 **오직** 소프트 삭제 모델을 검색합니다.

    $flights = Flight::onlyTrashed()
                    ->where('airline_id', 1)
                    ->get();

<a name="pruning-models"></a>
## 가지치기 모델

더 이상 필요하지 않은 모델을 주기적으로 삭제하려는 경우가 있습니다. 이를 수행하기 위해 주기적으로 가지치기를 원하는 모델에 `Illuminate\Database\Eloquent\Prunable` 또는 `Illuminate\Database\Eloquent\MassPrunable` 특성을 추가할 수 있습니다. 모델에 특성 중 하나를 추가한 후, 더 이상 필요하지 않은 모델을 해결하는 Eloquent 쿼리 빌더를 반환하는 `prunable` 메소드를 구현하십시오.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;
    use Illuminate\Database\Eloquent\Prunable;

    class Flight extends Model
    {
        use Prunable;

        /**
         * Get the prunable model query.
         *
         * @return \Illuminate\Database\Eloquent\Builder
         */
        public function prunable()
        {
            return static::where('created_at', '<=', now()->subMonth());
        }
    }

모델을 `Prunable`로 표시할 때 모델에 `pruning` 메소드를 정의할 수도 있습니다. 이 메소드는 모델이 삭제되기 전에 호출됩니다. 이 메소드는 모델이 데이터베이스에서 영구적으로 제거되기 전에 저장된 파일처림 모델과 관련된 추가 리소스를 삭제하는 데 유용할 수 있습니다.

    /**
     * Prepare the model for pruning.
     *
     * @return void
     */
    protected function pruning()
    {
        //
    }

정리 가능한 모델을 구성한 후 애플리케이션의 `App\Console\Kernel` 클래스에서 `model:prune` Artisan 명령을 예약해야 합니다. 이 명령을 실행해야 하는 적절한 간격을 자유롭게 선택할 수 있습니다.

    /**
     * Define the application's command schedule.
     *
     * @param  \Illuminate\Console\Scheduling\Schedule  $schedule
     * @return void
     */
    protected function schedule(Schedule $schedule)
    {
        $schedule->command('model:prune')->daily();
    }

내부적인 면에서 `model:prune` 명령은 애플리케이션의 `app/Models` 디렉토리에서 "Prunable" 모델을 자동으로 감지합니다. 모델이 다른 위치에 있는 경우 `--model` 옵션을 사용하여 모델 클래스 이름을 지정할 수 있습니다.

    $schedule->command('model:prune', [
        '--model' => [Address::class, Flight::class],
    ])->daily();

감지된 모든 모델을 정리하는 동안 특정 모델을 정리에서 제외하려면 `--except` 옵션을 사용할 수 있습니다.

    $schedule->command('model:prune', [
        '--except' => [Address::class, Flight::class],
    ])->daily();

`--pretend` 옵션과 함께 `model:prune` 명령을 실행하여 `prunable` 쿼리를 테스트할 수 있습니다. pretend 옵션을 사용했을 때 `model:prune` 명령은 명령이 실제로 실행되는 경우 제거될 레코드 수를 단순히 보고합니다.

    php artisan model:prune --pretend

> {note} 소프트 삭제 모델은 정리 가능한 쿼리와 일치하는 경우 영구적으로 삭제됩니다(`forceDelete`).

<a name="mass-pruning"></a>
#### 대량으로 비우기

모델이 `Illuminate\Database\Eloquent\MassPrunable` 특성으로 표시되면 대량 삭제 쿼리를 사용하여 데이터베이스에서 모델이 삭제됩니다. 따라서 `pruning` 메소드가 호출되지 않으며 `deleting` 및 `deleted` 모델 이벤트가 전달되지 않습니다. 이는 모델이 삭제되기 전에 실제로 검색되지 않으므로 가지치기 프로세스가 훨씬 더 효율적입니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;
    use Illuminate\Database\Eloquent\MassPrunable;

    class Flight extends Model
    {
        use MassPrunable;

        /**
         * Get the prunable model query.
         *
         * @return \Illuminate\Database\Eloquent\Builder
         */
        public function prunable()
        {
            return static::where('created_at', '<=', now()->subMonth());
        }
    }

<a name="replicating-models"></a>
## 모델 복제

`replicate` 메소드를 사용하여 모델 인스턴스의 저장되지 않은 사본을 생성 할 수 있습니다. 이것은 많은 동일한 속성을 공유하는 모델 인스턴스가 있을 때 특히 유용합니다.

    use App\Models\Address;

    $shipping = Address::create([
        'type' => 'shipping',
        'line_1' => '123 Example Street',
        'city' => 'Victorville',
        'state' => 'CA',
        'postcode' => '90001',
    ]);

    $billing = $shipping->replicate()->fill([
        'type' => 'billing'
    ]);

    $billing->save();

하나 이상의 속성이 새 모델에 복제되는 것을 제외하려면 `replicate` 메소드에 배열을 전달할 수 있습니다.

    $flight = Flight::create([
        'destination' => 'LAX',
        'origin' => 'LHR',
        'last_flown' => '2020-03-04 11:00:00',
        'last_pilot_id' => 747,
    ]);

    $flight = $flight->replicate([
        'last_flown',
        'last_pilot_id'
    ]);

<a name="query-scopes"></a>
## 쿼리 스코프

<a name="global-scopes"></a>
### 글로벌 스코프

글로벌 스코프를 사용하면 주어진 모델에 대한 모든 쿼리에 제약 조건을 추가할 수 있습니다. 라라벨의 자체 [소프트 삭제](#soft-deleting) 기능은 글로벌 스코프를 활용하여 데이터베이스에서 "삭제되지 않은" 모델만 검색합니다. 고유한 글로벌 스코프를 작성하면 주어진 모델에 대한 모든 쿼리가 특정 제약 조건을 받도록 하는 편리하고 쉬운 방법을 제공할 수 있습니다.

<a name="writing-global-scopes"></a>
#### 글로벌 스코프 작성하기

글로벌 스코프를 작성하는 것은 간단합니다. 먼저 `Illuminate\Database\Eloquent\Scope` 인터페이스를 구현하는 클래스를 정의합니다. 라라벨에는 스코프 클래스를 배치해야 하는 일반적인 위치가 없으므로 이 클래스를 원하는 디렉토리에 자유롭게 배치할 수 있습니다.

`Scope` 인터페이스를 사용하려면 `apply`이라는 한 가지 메소드를 구현해야 합니다. `apply` 메소드는 필요에 따라 `where` 제약 조건 또는 다른 유형의 절을 쿼리에 추가할 수 있습니다.

    <?php

    namespace App\Scopes;

    use Illuminate\Database\Eloquent\Builder;
    use Illuminate\Database\Eloquent\Model;
    use Illuminate\Database\Eloquent\Scope;

    class AncientScope implements Scope
    {
        /**
         * Apply the scope to a given Eloquent query builder.
         *
         * @param  \Illuminate\Database\Eloquent\Builder  $builder
         * @param  \Illuminate\Database\Eloquent\Model  $model
         * @return void
         */
        public function apply(Builder $builder, Model $model)
        {
            $builder->where('created_at', '<', now()->subYears(2000));
        }
    }

> {tip} 글로벌 스코프가 쿼리의 select 절에 컬럼을 추가하는 경우 select 대신 `addSelect` 메소드를 사용해야 합니다. 이렇게 하면 쿼리의 기존 `select` 절이 교체되는 실수를 방지할 수 있습니다.

<a name="applying-global-scopes"></a>
#### 글로벌 스코프 적용하기

모델에 글로벌 스코프를 할당하려면 모델의 `booted` 메소드를 재정의하고 모델의 `addGlobalScope` 메소드를 호출해야 합니다. `addGlobalScope` 메소드는 사용자 스코프의 인스턴스를 유일한 인수로 허용합니다.

    <?php

    namespace App\Models;

    use App\Scopes\AncientScope;
    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * The "booted" method of the model.
         *
         * @return void
         */
        protected static function booted()
        {
            static::addGlobalScope(new AncientScope);
        }
    }

위 예제의 스코프를 `App\Models\User` 모델에 추가한 후 `User::all()` 메소드를 호출하면 다음 SQL 쿼리가 실행됩니다.

```sql
select * from `users` where `created_at` < 0021-02-18 00:00:00
```

<a name="anonymous-global-scopes"></a>
#### 익명의 글로벌 스코프

Eloquent는 또한 클로저를 사용하여 글로벌 스코프를 정의할 수 있는데, 이는 자체적으로 별도의 클래스가 없는 단순한 스코프에 특히 유용합니다. 클로저를 사용하여 글로벌 스코프를 정의할 때는 `addGlobalScope` 메소드의 첫 번째 인수로 사용자가 선택한 스코프 이름을 제공해야 합니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Builder;
    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * The "booted" method of the model.
         *
         * @return void
         */
        protected static function booted()
        {
            static::addGlobalScope('ancient', function (Builder $builder) {
                $builder->where('created_at', '<', now()->subYears(2000));
            });
        }
    }

<a name="removing-global-scopes"></a>
#### 글로벌 스코프 삭제하기

주어진 쿼리에 대한 글로벌 스코프를 제거하려면 `withoutGlobalScope` 메소드를 사용할 수 있습니다. 이 메소드는 글로벌 스코프의 클래스 이름을 유일한 인수로 받아들입니다.

    User::withoutGlobalScope(AncientScope::class)->get();

또는 클로저를 사용하여 글로벌 스코프를 정의한 경우 글로벌 스코프에 할당한 문자열 이름을 전달해야 합니다.

    User::withoutGlobalScope('ancient')->get();

쿼리의 글로벌 스코프 일부 또는 전체를 제거하려면 `withoutGlobalScopes` 메소드를 사용할 수 있습니다.

    // Remove all of the global scopes...
    User::withoutGlobalScopes()->get();

    // Remove some of the global scopes...
    User::withoutGlobalScopes([
        FirstScope::class, SecondScope::class
    ])->get();

<a name="local-scopes"></a>
### 로컬 스코프

로컬 스코프를 사용하면 애플리케이션 전체에서 쉽게 재사용할 수 있는 공통 쿼리 제약 조건 집합을 정의할 수 있습니다. 예를 들어, "인기 있는" 것으로 간주되는 모든 사용자를 자주 검색해야 할 수 있습니다. 스코프를 정의하려면 Eloquent 모델 메소드에 `scope`를 접두사로 붙입니다.

스코프는 항상 동일한 쿼리 빌더 인스턴스 또는 `void`를 반환해야 합니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * Scope a query to only include popular users.
         *
         * @param  \Illuminate\Database\Eloquent\Builder  $query
         * @return \Illuminate\Database\Eloquent\Builder
         */
        public function scopePopular($query)
        {
            return $query->where('votes', '>', 100);
        }

        /**
         * Scope a query to only include active users.
         *
         * @param  \Illuminate\Database\Eloquent\Builder  $query
         * @return void
         */
        public function scopeActive($query)
        {
            $query->where('active', 1);
        }
    }

<a name="utilizing-a-local-scope"></a>
#### 로컬 스코프 활용하기

스코프가 정의되면 모델을 쿼리할 때 스코프 메소드를 호출할 수 있습니다. 그러나 메소드를 호출할 때 `scope` 접두사를 포함하면 안 됩니다. 다양한 스코프로 호출을 연결할 수도 있습니다.

    use App\Models\User;

    $users = User::popular()->active()->orderBy('created_at')->get();

`or` 쿼리 연산자를 통해 여러 Eloquent 모델 스코프를 결합하려면 올바른 [논리적 그룹화](/docs/{{version}}/queries#logical-grouping)를 달성하기 위해 클로저를 사용해야 할 수 있습니다.

    $users = User::popular()->orWhere(function (Builder $query) {
        $query->active();
    })->get();

그러나 이것이 번거로울 수 있으므로 라라벨은 클로저를 사용하지 않고도 스코프를 유창하게 연결할 수 있는 "고차" `orWhere` 메소드를 제공합니다.

    $users = App\Models\User::popular()->orWhere->active()->get();

<a name="dynamic-scopes"></a>
#### 다이나믹 스코프

때로는 매개변수를 허용하는 스코프를 정의해야 하는 경우가 있습니다. 시작하려면, 스코프 메소드의 서명에 추가 매개변수를 추가하기만 하면 됩니다. 스코프 매개변수는 `$query` 매개변수 다음에 정의되어야 합니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * Scope a query to only include users of a given type.
         *
         * @param  \Illuminate\Database\Eloquent\Builder  $query
         * @param  mixed  $type
         * @return \Illuminate\Database\Eloquent\Builder
         */
        public function scopeOfType($query, $type)
        {
            return $query->where('type', $type);
        }
    }

스코프 메소드의 서명에 예상 인수가 추가되면 스코프를 호출할 때 인수를 전달할 수 있습니다.

    $users = User::ofType('admin')->get();

<a name="comparing-models"></a>
## 모델의 비교

때로는 두 모델이 "동일한"지 여부를 결정해야 할 수도 있습니다. `is` 및 `isNot` 메소드는 두 모델이 동일한 기본키, 테이블 및 데이터베이스 연결을 가지고 있는지 여부를 빠르게 확인하는 데 사용할 수 있습니다.

    if ($post->is($anotherPost)) {
        //
    }

    if ($post->isNot($anotherPost)) {
        //
    }

`is` 및 `isNot` 메소드는 `belongsTo`, `hasOne`, `morphTo` 및 `morphOne` [관계-relationships](/docs/{{version}}/eloquent-relationships)을 사용할 때도 사용할 수 있습니다. 이 방법은 해당 모델을 검색하기 위해 쿼리를 실행하지 않고 관련 모델을 비교하려는 경우에 특히 유용합니다.

    if ($post->author()->is($user)) {
        //
    }

<a name="events"></a>
## 이벤트

> {tip} Eloquent 이벤트를 클라이언트 측 애플리케이션에 직접 브로드캐스트하고 싶으십니까? 라라벨의 [모델 이벤트 브로드캐스트](/docs/{{version}}/broadcasting#model-broadcasting)을 확인하세요.

Eloquent 모델은 여러 이벤트를 전달하여 모델 수명 주기의 `retrieved`, `creating`, `created`, `updating`, `updated`, `saving`, `saved`, `deleting`, `deleted`, `restoring`, `restored`, `replicating` 와 같은 순간에 연결할 수 있습니다.

`retrieved` 이벤트는 데이터베이스에서 기존 모델을 검색할 때 전달됩니다. 새 모델이 처음 저장되면 `creating` 및 `created` 이벤트가 전달됩니다. `updating` `updated` 이벤트는 기존 모델이 수정되고 `save` 메소드가 호출될 때 전달됩니다. `saving` / `saved` 이벤트는 모델의 속성이 변경되지 않은 경우에도 모델이 생성되거나 업데이트될 때 전달됩니다. `-ing`으로 끝나는 이벤트 이름은 모델에 대한 변경 사항이 지속되기 전에 전달되는 반면, `-ed`로 끝나는 이벤트는 모델에 대한 변경 사항이 지속된 후에 전달됩니다.

모델 이벤트 청취를 시작하려면 Eloquent 모델에 `$dispatchesEvents` 속성을 정의하십시오. 이 속성은 Eloquent 모델 라이프사이클의 다양한 지점을 자신의 [이벤트 클래스](/docs/{{version}}/events)에 매핑합니다. 각 모델 이벤트 클래스는 해당 생성자를 통해 영향을 받는 모델의 인스턴스를 수신해야 합니다.

    <?php

    namespace App\Models;

    use App\Events\UserDeleted;
    use App\Events\UserSaved;
    use Illuminate\Foundation\Auth\User as Authenticatable;

    class User extends Authenticatable
    {
        use Notifiable;

        /**
         * The event map for the model.
         *
         * @var array
         */
        protected $dispatchesEvents = [
            'saved' => UserSaved::class,
            'deleted' => UserDeleted::class,
        ];
    }

Eloquent 이벤트를 정의하고 매핑한 후 [이벤트 리스너](/docs/{{version}}/events#defining-listeners)를 사용하여 이벤트를 처리할 수 있습니다.

> {note} Eloquent를 통해 대량 업데이트 또는 삭제 쿼리를 실행할 때 `saved`, `updated`, `deleting` 및 `deleted` 모델 이벤트는 영향을 받는 모델에 대해 전달되지 않습니다. 이는 대량 업데이트 또는 삭제를 수행할 때 모델이 실제로 검색되지 않기 때문입니다.

<a name="events-using-closures"></a>
### 클로저 사용하기

커스텀 이벤트 클래스를 사용하는 대신 다양한 모델 이벤트가 전달될 때 실행되는 클로저를 등록할 수 있습니다. 일반적으로 모델의 `booted` 메소드에 이러한 클로저를 등록해야 합니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * The "booted" method of the model.
         *
         * @return void
         */
        protected static function booted()
        {
            static::created(function ($user) {
                //
            });
        }
    }

필요한 경우 모델 이벤트를 등록할 때 [대기열에 올릴 수 있는 익명 이벤트 리스너](/docs/{{version}}/events#queuable-anonymous-event-listeners)를 활용할 수 있습니다. 이것은 애플리케이션의 [큐]](/docs/{{version}}/queues)를 사용하여 백그라운드에서 모델 이벤트 리스너를 실행하도록 라라벨에 지시합니다.

    use function Illuminate\Events\queueable;

    static::created(queueable(function ($user) {
        //
    }));

<a name="observers"></a>
### 옵저버

<a name="defining-observers"></a>
#### 옵저버 객체 정의하기

주어진 모델에서 많은 이벤트를 수신하는 경우 옵저버를 사용하여 모든 리스너를 단일 클래스로 그룹화할 수 있습니다. 옵저버 클래스에는 듣고자 하는 Eloquent 이벤트를 반영하는 메소드 이름이 있습니다. 이러한 각 메소드는 영향을 받는 모델을 유일한 인수로 받습니다. `make:observer` Artisan 명령은 새로운 옵저버 클래스를 생성하는 가장 쉬운 방법입니다.

    php artisan make:observer UserObserver --model=User

이 명령어는 새로운 옵저버 클래스를 `App/Observers` 디렉토리에 생성합니다. 이 디렉토리가 존재하지 않는다면, 아티즌은 이 디렉토리를 자동으로 생성합니다. 새로운 옵저버 클래스는 다음과 같은 형태입니다.

    <?php

    namespace App\Observers;

    use App\Models\User;

    class UserObserver
    {
        /**
         * Handle the User "created" event.
         *
         * @param  \App\Models\User  $user
         * @return void
         */
        public function created(User $user)
        {
            //
        }

        /**
         * Handle the User "updated" event.
         *
         * @param  \App\Models\User  $user
         * @return void
         */
        public function updated(User $user)
        {
            //
        }

        /**
         * Handle the User "deleted" event.
         *
         * @param  \App\Models\User  $user
         * @return void
         */
        public function deleted(User $user)
        {
            //
        }

        /**
         * Handle the User "forceDeleted" event.
         *
         * @param  \App\Models\User  $user
         * @return void
         */
        public function forceDeleted(User $user)
        {
            //
        }
    }

옵저버를 등록하려면 관찰하려는 모델에서 `observe` 메소드를 호출해야 합니다. 애플리케이션의 `App\Providers\EventServiceProvider` 서비스 제공자의 `boot` 메소드에서 옵저버를 등록할 수 있습니다.

    use App\Models\User;
    use App\Observers\UserObserver;

    /**
     * Register any events for your application.
     *
     * @return void
     */
    public function boot()
    {
        User::observe(UserObserver::class);
    }

> {tip} 관찰자가 들을 수 있는 추가 이벤트(예: `saving` 및 `retrieved`)가 있습니다. 이러한 이벤트는 [events](#events) 문서에 설명되어 있습니다.

<a name="observers-and-database-transactions"></a>
#### 옵저버 및 데이터베이스 트랜잭션

데이터베이스 트랜잭션 내에서 모델이 생성될 때 데이터베이스 트랜잭션이 커밋된 후에만 이벤트 핸들러를 실행하도록 옵저버에게 지시할 수 있습니다. 옵저버에서 `$afterCommit` 속성을 정의하여 이를 수행할 수 있습니다. 데이터베이스 트랜잭션이 진행 중이 아니면 이벤트 핸들러가 즉시 실행됩니다.

    <?php

    namespace App\Observers;

    use App\Models\User;

    class UserObserver
    {
        /**
         * Handle events after all transactions are committed.
         *
         * @var bool
         */
        public $afterCommit = true;

        /**
         * Handle the User "created" event.
         *
         * @param  \App\Models\User  $user
         * @return void
         */
        public function created(User $user)
        {
            //
        }
    }

<a name="muting-events"></a>
### 이벤트 끄기(Muting Events)

때때로 모델에서 발생한 모든 이벤트를 일시적으로 "mute"해야 할 수도 있습니다. `withoutEvents` 메소드를 사용하여 이를 달성할 수 있습니다. `withoutEvents` 메소드는 클로저를 유일한 인수로 받아들입니다. 이 클로저 내에서 실행되는 모든 코드는 모델 이벤트를 전달하지 않으며 클로저에서 반환된 모든 값은 `withoutEvents` 메소드에 의해 반환됩니다.

    use App\Models\User;

    $user = User::withoutEvents(function () use () {
        User::findOrFail(1)->delete();

        return User::find(2);
    });

<a name="saving-a-single-model-without-events"></a>
#### 이벤트 없이 단일 모델 저장하기

때때로 이벤트를 전달하지 않고 주어진 모델을 "저장"하고 싶을 수 있습니다. `saveQuietly` 메소드를 사용하여 이 작업을 수행할 수 있습니다.

    $user = User::findOrFail(1);

    $user->name = 'Victoria Faith';

    $user->saveQuietly();
