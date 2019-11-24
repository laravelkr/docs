# Eloquent: 시작하기

- [시작하기](#introduction)
- [모델 정의하기](#defining-models)
    - [Eloquent 모델 컨벤션](#eloquent-model-conventions)
    - [기본 속성 값](#default-attribute-values)
- [모델 조회하기](#retrieving-models)
    - [컬렉션](#collections)
    - [결과 분할하기](#chunking-results)
    - [고급 서브쿼리](#advanced-subqueries)
- [하나의 모델 / 합계를 찾기](#retrieving-single-models)
    - [합계 가져오기](#retrieving-aggregates)
- [모델을 통한 추가 및 삭제하기](#inserting-and-updating-models)
    - [Inserts](#inserts)
    - [Updates](#updates)
    - [대량 할당 - Mass Assignment](#mass-assignment)
    - [기타 생성을 위한 메소드들](#other-creation-methods)
- [모델 삭제하기](#deleting-models)
    - [소프트 삭제하기](#soft-deleting)
    - [소프트 삭제된 모델 쿼리하기](#querying-soft-deleted-models)
- [쿼리 스코프](#query-scopes)
    - [글로벌 스코프](#global-scopes)
    - [로컬 스코프](#local-scopes)
- [모델의 비교](#comparing-models)
- [이벤트](#events)
    - [옵저버](#observers)

<a name="introduction"></a>
## 시작하기

라라벨에 포함된 Eloquent ORM은 여러분의 데이터베이스에서 동작하는 아름답고 심플한 액티브 레코드를 제공합니다. 각각의 데이터베이스 테이블은 이에 해당하는 "모델"을 가지고 있습니다.

시작하기에 앞서 `config/database.php` 에 데이터베이스 커넥션이 설정되어 있는지 확인하십시오. 데이터베이스 설정에 대한 자세한 내용은 [문서](/docs/{{version}}/database#configuration)를 확인합니다.

<a name="defining-models"></a>
## 모델 정의하기

시작하기 위해서 Eloquent 모델 하나를 생성합니다. 일반적으로 모델은 `app` 디렉토리에 존재하지만, `composer.json` 파일에 의해서 오토로드 되는 곳이라면 어느곳에든 위치해도 상관없습니다. 모든 Eloquent 모델은 `Illuminate\Database\Eloquent\Model`을 상속받습니다.

모델 인스턴스를 생성하는 가장 쉬운 방법은 `make:model` [아티즌 커맨드](/docs/{{version}}/artisan)를 사용하는 것입니다.

    php artisan make:model Flight

모델을 생성할 때 [데이터 마이그레이션](/docs/{{version}}/migrations)을 생성하고 싶다면 `--migration` 혹은 `-m` 옵션을 사용할 수 있습니다.

    php artisan make:model Flight --migration

    php artisan make:model Flight -m

<a name="eloquent-model-conventions"></a>
### Eloquent 모델 컨벤션

이제 `flights` 데이터베이스 테이블에서 정보를 찾거나 저장할 때 쓸 `Flight` 모델의 예를 보겠습니다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Flight extends Model
    {
        //
    }

#### 테이블 이름

생성한 `Flight` 모델에서 어떠한 테이블을 사용해야할지 Elquent 에게 알려주지 않았다는 점을 주목하십시오. 관례적으로 연관된 테이블이 별도로 지정되지 않는다면 클래스의 "스네이크 케이스" 로 표시된 복수형의 이름이 사용되어 집니다. 따라서 이 예제에서는 Eloquent는 `Flight` 모델은 `flights` 테이블에 레코드를 저장한다고 추정할 것입니다. 여러분은 모델의 `table` 속성을 통해서 고유한 테이블을 지정할 수 있습니다.

    <?php

    namespace App;

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

#### Primary Keys

Eloquent는 테이블의 primary key 컬럼의 이름을 `id`로 추정합니다. protected `$primaryKey` 속성을 통해서 이 컬럼명을 재정의할 수 있습니다.

    <?php

    namespace App;

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

추가적으로, Eloquent 는 primary key가 증가하는 정수값(incrementing)이라고 추정합니다. 이는 기본적으로 primary key를 `int`로 자동 캐스팅 한다는 것을 의미합니다. 증가하지 않는(non-incrementing) 또는 숫자형이 아닌 primary key를 사용하고자 한다면, 모델의 public `$incrementing` 속성을 `false` 로 설정해야 합니다. primary key가 정수값(integer)이 아니라면, 모델의 protected `$keyType` 속성을 `string`으로 지정하십시오. 증가하지 않거나 숫자가 아닌 기본 키를 사용하려면 모델의 공개 `$incrementing` 속성을 `false`로 설정해야합니다.

    <?php

    class Flight extends Model
    {
        /**
         * Indicates if the IDs are auto-incrementing.
         *
         * @var bool
         */
        public $incrementing = false;
    }

기본 키가 정수가 아닌 경우 모델의 protected `$keyType` 속성을 `string`으로 설정해야합니다.

    <?php

    class Flight extends Model
    {
        /**
         * The "type" of the auto-incrementing ID.
         *
         * @var string
         */
        protected $keyType = 'string';
    }

#### 타임스탬프

기본적으로, Eloquent는 테이블에 `created_at` 과 `updated_at` 컬럼이 존재한다고 생각합니다. 자동으로 이 컬럼값이 채워지기를 원하지 않는다면 `$timestamps` 속성을 `false` 로 지정하십시오:

    <?php

    namespace App;

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

타임스탬프의 포맷을 변경하여야 한다면 모델에 `$dateFormat` 속성을 지정하면 됩니다. 이 속성은 날짜 속성이 데이터베이스에 저장될 때의 형식과 모델이 배열이나 JSON으로 직렬화-serialization되었을 때의 형식을 결정합니다.

    <?php

    namespace App;

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

만약 타임스탬프를 저장하는 필드의 이름을 수정하고자 하는 경우, 모델에 `CREATED_AT` 그리고 `UPDATED_AT` 상수를 설정하면 됩니다.

    <?php

    class Flight extends Model
    {
        const CREATED_AT = 'creation_date';
        const UPDATED_AT = 'last_update';
    }

#### 데이터베이스 커넥션

기본적으로 모든 Eloquent 모델은 애플리케이션에서 기본으로 셋팅되어 있는 커넥션을 사용합니다. 다른 커넥션을 지정하고 싶다면 `$connection` 속성을 이용하면 됩니다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Flight extends Model
    {
        /**
         * The connection name for the model.
         *
         * @var string
         */
        protected $connection = 'connection-name';
    }

<a name="default-attribute-values"></a>
### 기본 속성 값

일부 모델의 속성에 대한 기본값을 정의하고 싶다면 모델의 `$attributes`에 속성을 정의 할 수 있습니다.

    <?php

    namespace App;

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

하나의 모델과 [해당 모델에 지정된 데이터베이스 테이블](/docs/{{version}}/migrations#writing-migrations)을 생성하였다면, 이제 데이터베이스에서 데이터를 조회할 수 있습니다. 각 Eloquent 모델을 데이터베이스 쿼리를 용이하게 해주는 강력한 [쿼리 빌더](/docs/{{version}}/queries)로 생각하십시오. 예를 들자면:

    <?php

    $flights = App\Flight::all();

    foreach ($flights as $flight) {
        echo $flight->name;
    }

#### 추가적인 제약조건 추가하기

Eloquent의 `all` 메소드는 모델의 테이블에서 모든 결과를 반환할 것입니다. 각 Eloquent 모델은 [쿼리 빌더](/docs/{{version}}/queries)의 역할을 하기 때문에 쿼리에 다양한 조건들을 추가할 수 있고, 마지막에 `get` 메소드를 사용하여 결과를 조회할 수 있습니다.

    $flights = App\Flight::where('active', 1)
                   ->orderBy('name', 'desc')
                   ->take(10)
                   ->get();

> {tip} Eloquent 모델은 모두가 쿼리 빌더이기 때문에, [쿼리 빌더](/docs/{{version}}/queries)에서 제공되는 모든 메소드를 살펴 보시기 바랍니다. 여기의 어떠한 메소드라도 Eloquent 쿼리에서 사용할 수 있습니다.

#### 모델의 리프레쉬

`fresh` 와 `refresh` 메소드를 사용하여 모델을 리프레쉬 할 수 있습니다. `fresh` 메소드는 데이터베이스로부터 모델을 다시 검색 할 것입니다. 기존 모델 인스턴스는 영향을받지 않습니다.

    $flight = App\Flight::where('number', 'FR 900')->first();

    $freshFlight = $flight->fresh();

`refresh` 메소드는 데이터베이스의 새로운 데이터를 사용하여 기존 모델을 갱신합니다. 또한 로드 된 모든 관계가 새로 고쳐집니다.

    $flight = App\Flight::where('number', 'FR 900')->first();

    $flight->number = 'FR 456';

    $flight->refresh();

    $flight->number; // "FR 900"

<a name="collections"></a>
### 컬렉션

`all`과 `get` 같이 여러개의 결과를 가져오는 Eloquent 메소드의 경우 `Illuminate\Database\Eloquent\Collection` 인스턴스가 반환됩니다. `Collection` 클래스는 Eloquent 결과에 사용할 수 있는 [다양한 메소드](/docs/{{version}}/eloquent-collections#available-methods)들을 제공합니다.

    $flights = $flights->reject(function ($flight) {
        return $flight->cancelled;
    });

배열과 동일하게 또한 이 컬렉션을 반복문에서 사용 할 수 있습니다.

    foreach ($flights as $flight) {
        echo $flight->name;
    }

<a name="chunking-results"></a>
### 결과 분할하기

수천개의 Eloquent 모델이 필요 할 때에는 `chunk` 명령어를 사용하십시오. `chunk` 메소드는 "분할된" Eloquent 모델들을 가져올 것이며 주어진 `Closure`에 의해서 처리될 것입니다. `chunk` 메소드를 이용하면 결과가 아주 큰 경우 메모리를 절약할 수 있을 것입니다.

    Flight::chunk(200, function ($flights) {
        foreach ($flights as $flight) {
            //
        }
    });

메소드의 첫번째 인자는 "chunk" 메소드에서 받아 들일 레코드의 개수 입니다. 두번째 인자는 클로저로 데이터베이스로 부터 분할된 데이터들을 전달 받습니다. 데이터베이스 쿼리는 각가의 분할된 레코드들을 조회하여 클로저에 전달하도록 실행될 것입니다.

#### 커서 사용하기

`cursor` 메소드는 단 하나의 쿼리를 실행하는 커서를 사용하여 데이터베이스 레코드 전체를 반복할 수 있게 합니다. 대량의 데이터를 처리하는 경우에, `cursor` 메소드는 메모리 사용량을 크게 줄여줍니다.

    foreach (Flight::where('foo', 'bar')->cursor() as $flight) {
        //
    }

`cursor` 는 `Illuminate\Support\LazyCollection` 인스턴스를 반환합니다. [Lazy collections](/docs/{{version}}/collections#lazy-collections) 을 사용하면 하나의 모델을 메모리에 불러오는동안 일반적인 많은 라라벨 컬렉션 메소드를 사용할 수 있습니다.

    $users = App\User::cursor()->filter(function ($user) {
        return $user->id > 500;
    });

    foreach ($users as $user) {
        echo $user->id;
    }

<a name="advanced-subqueries"></a>
### 고급 서브쿼리

#### 서브쿼리 선택

Eloquent 는 고급 서브쿼리 기능을 지원하므로 단일 쿼리에서 관련 테이블에 있는 정보를 가져올 수 있습니다. 예를 들어, 우리는 목적지까지 가기 위해 `destinations` 테이블과 `flights` 테이블이 있다고 가정 해봅시다. `flights` 테이블에는 항공편이 목적지에 도착한 시점을 나타내는 `arrived_at` 열이 있습니다.

서브쿼리 메소드 중에 `select` 와 `addSelect` 는 단일 쿼리를 사용하여 가장 최근에 목적지에 도착한 항공편의 이름과 목적지를 모두 선택할 수 있습니다.

    use App\Destination;
    use App\Flight;

    return Destination::addSelect(['last_flight' => Flight::select('name')
        ->whereColumn('destination_id', 'destinations.id')
        ->orderBy('arrived_at', 'desc')
        ->limit(1)
    ])->get();

#### 서브쿼리 정렬

그 외에, 서브쿼리는 쿼리 빌더의 `orderBy` 함수를 지원합니다. 이 기능을 사용하여 마지막 항공편이 목적지에 도착한 시간을 기준으로 모든 목적지를 정렬 할 수 있습니다. 데이터베이스에 단일 쿼리를 실행하는 동안 이 작업이 수행될 수 있습니다.

    return Destination::orderByDesc(
        Flight::select('arrived_at')
            ->whereColumn('destination_id', 'destinations.id')
            ->orderBy('arrived_at', 'desc')
            ->limit(1)
    )->get();

<a name="retrieving-single-models"></a>
## 하나의 모델 / 집계 조회하기

테이블에서 모든 정보를 조회하는 것 외에도 `find` 또는 `first`를 이용해서 하나의 레코드를 찾을 수 있습니다. 이 메소드들은 모델 컬렉션을 반환하는 대신 모델 인스턴스 하나를 반환합니다.

    // Retrieve a model by its primary key...
    $flight = App\Flight::find(1);

    // Retrieve the first model matching the query constraints...
    $flight = App\Flight::where('active', 1)->first();

또한 `find` 메소드를 primary key 의 배열과 함께 사용하여 매칭되는 레코드들의 컬렉션을 반환받을 수 있습니다.

    $flights = App\Flight::find([1, 2, 3]);

#### Not Found Exceptions

모델을 찾지 못했을 때에는 Exception을 던지고 싶을 수도 있으며 특히 라우트나 컨트롤러에서 유용합니다. `findOrFail`와 `firstOrFail` 메소드는 쿼리의 첫번째 결과를 반환하지만 결과를 찾을 수 없을 때에는 `Illuminate\Database\Eloquent\ModelNotFoundException`가 던져질 것입니다.

    $model = App\Flight::findOrFail(1);

    $model = App\Flight::where('legs', '>', 100)->firstOrFail();

예외를 처리하지 않는다면 `404` HTTP 응답이 자동으로 사용자에게 보내집니다. 이 메소드들을 사용할 때 `404` 응답을 반환하는 것을 명시적으로 선언할 필요는 없습니다.

    Route::get('/api/flights/{id}', function ($id) {
        return App\Flight::findOrFail($id);
    });

<a name="retrieving-aggregates"></a>
### 합계 조회하기

[쿼리 빌더](/docs/{{version}}/queries)가 제공하는 `count`, `sum`, `max`을 비롯한 [집계 메소드](/docs/{{version}}/queries#aggregates)를 이용할 수 있습니다. 이 메소드들은 모델의 인스턴스 대신 적절한 스칼라 값을 반환합니다.

    $count = App\Flight::where('active', 1)->count();

    $max = App\Flight::where('active', 1)->max('price');

<a name="inserting-and-updating-models"></a>
## 모델 추가하기 & 수정하기

<a name="inserts"></a>
### Inserts

데이터베이스에 새로운 레코드를 생성하기 위해는, 새 모델의 인스턴스를 생성하고 모델의 속성을 설정하여, `save` 메소드를 호출하면 됩니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Flight;
    use Illuminate\Http\Request;

    class FlightController extends Controller
    {
        /**
         * Create a new flight instance.
         *
         * @param  Request  $request
         * @return Response
         */
        public function store(Request $request)
        {
            // Validate the request...

            $flight = new Flight;

            $flight->name = $request->name;

            $flight->save();
        }
    }

이 예제에서 HTTP 요청에서 확인된 `name` 파라미터를 `App\Flight` 모델 인스턴스의 `name` 속성에 지정합니다. `save` 메소드를 호출하면 데이터베이스에 레코드가 추가 될 것입니다. `save` 메소드를 호출하면 `created_at`와 `updated_at` 타임스탬프가 자동으로 설정되며 수동으로 지정할 필요가 없습니다.

<a name="updates"></a>
### Updates

`save` 메소드는 데이터베이스에 이미 존재하는 모델들을 업데이트 하기 위해 사용될 수 있습니다. 모델을 업데이트하기 위해서는 모델을 조회한 다음, 업데이트하기 원하는 속성을 수정한 뒤 `save` 메소드를 호출합니다. 이 때에도 `updated_at` 타임스탬프는 자동으로 설정되며 수동으로 값을 지정할 필요가 없습니다.

    $flight = App\Flight::find(1);

    $flight->name = 'New Flight Name';

    $flight->save();

#### 여러개의 모델 Update

주어진 쿼리에 일치하는 여러개의 모델들에 대해서 업데이트를 할 수 있습니다. 다음의 예제에서는 `active` 하면서, `destination` 이 `San Diego` 인 모든 비행편들이 연기되었다고 표시될 것입니다.

    App\Flight::where('active', 1)
              ->where('destination', 'San Diego')
              ->update(['delayed' => 1]);

`update` 메소드는 컬럼의 정렬과 업데이트 될 컬럼을 대표하는 값의 배열을 필요로 합니다.

> {note} Eloquent를 통해서 여러개의 모델을 업데이트 할 때, 변경되는 모델에 대한 `saving`, `saved`, `updating` 및 `updated` 모델 이벤트는 발생되지 않습니다. 이 이유는 여러개의 모델을 업데이트 할 때 실제로 모델이 조회되는 것이 아니기 때문입니다.

<a name="mass-assignment"></a>
### 대량 할당 - Mass Assignment

`create` 메소드를 통해 한줄에서 바로 새로운 모델을 추가할 수도 있습니다. 메소드를 통해 추가된 모델 인스턴스가 결과로 반환될 것입니다. 하지만 기본적으로 모든 Eloquent 모델은 대량 할당-Mass Assignment 으로부터 보호되기 때문에, 이렇게 하기 전에 모델의 `fillable`나 `guarded` 속성을 지정해야 주어야 합니다.

대량 할당(Mass Assignment)의 취약성은 사용자가 예상치 못한 HTTP 요청 파라미터를 전달했을 때 발생하며, 해당 파라미터는 데이터베이스의 예상하지 못한 컬럼을 변경하게 됩니다. 예를 들어 악의적인 사용자는 HTTP 요청을 통해 `is_admin`을 전달할 수 있으며 이 파라미터는 모델의 `create` 메소드에 전달되어 사용자를 관리자로 승격할 수 있습니다.

따라서 시작하기 위해서는 대량 할당(Mass Assignment)할 모델의 속성을 정의해야 하며 이는 모델에 `$fillable` 속성을 사용해서 할 수 있습니다. 예를 들어 `Flight` 모델에서 `name` 속성을 대량 할당(Mass Assignment)될 수 있도록 만들 수 있습니다.

    <?php

    namespace App;

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

속성을 대량 할당될(mass assignable) 수 있도록 만든 뒤에는 `create` 메소드로 데이터베이스에 새로운 레코드을 추가할 수 있습니다. `create` 메소드는 저장된 모델 인스턴스를 반환합니다.

    $flight = App\Flight::create(['name' => 'Flight 10']);

이미 모델 인스턴스를 가지고 있다면, `fill` 메소드에 배열을 전달하여 속성을 구성할 수 있습니다.

    $flight->fill(['name' => 'Flight 22']);

#### 속성들 보호하기

`$fillable`는 대량 할당(mass assign)될 수 있는 속성들의 화이트 리스트로써 동작 하지만, `$guarded`를 사용할 수도 있습니다. `$guarded` 속성은 대량 할당(mass assign)하고 싶지 않은 속성들의 배열을 가지고 있을 것입니다. 이 배열에 포함되지 않은 모든 속성들은 대량 할당될 수 있을 것입니다. 따라서 `$guarded`는 블랙리스트와 같은 기능을 합니다. `$fillable`나 `$guarded` 둘 중 하나만을 사용해야 합니다. 다음 예제에서 **`price`**를 제외한 모든 속성들은 대량 할당이 가능합니다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Flight extends Model
    {
        /**
         * The attributes that aren't mass assignable.
         *
         * @var array
         */
        protected $guarded = ['price'];
    }

모든 속성들이 대량 할당이 가능하게 하고자 한다면, `$guarded` 프로퍼티를 빈 배열로 정의하면 됩니다.

    /**
     * The attributes that aren't mass assignable.
     *
     * @var array
     */
    protected $guarded = [];

<a name="other-creation-methods"></a>
### 기타 생성을 위한 메소드들

#### `firstOrCreate`/ `firstOrNew`

속성을 대량 할당(mass assign) 하여 모델을 생성하는 또다른 방법은 `firstOrCreate`와 `firstOrNew`의 두 가지가 있습니다. `firstOrCreate` 메소드는 주어진 컬럼 / 값의 쌍을 이용하여 데이터베이스 레코드를 찾으려고 시도할 것입니다. 데이터베이스에서 모델을 찾을 수 없으면 주어진 첫번째 속성과 두번째 속성을 기반으로 새로운 레코드를 입력합니다.

`firstOrCreate`와 같이 `firstOrNew` 메소드도 주어진 속성들에 해당하는 레코드를 데이터베이스에서 찾으려고 시도할 것입니다. 하지만 모델을 찾을 수 없으면 새로운 모델 인스턴스가 반환될 것입니다. `firstOrNew`에 의해 반환된 모델은 아직 데이터베이스에서 저장되지 않았다는 점에 주의하십시오. 모델을 저장하기 위해서는 `save`를 수동으로 호출해야 합니다.

    // Retrieve flight by name, or create it if it doesn't exist...
    $flight = App\Flight::firstOrCreate(['name' => 'Flight 10']);

    // Retrieve flight by name, or create it with the name, delayed, and arrival_time attributes...
    $flight = App\Flight::firstOrCreate(
        ['name' => 'Flight 10'],
        ['delayed' => 1, 'arrival_time' => '11:30']
    );

    // Retrieve by name, or instantiate...
    $flight = App\Flight::firstOrNew(['name' => 'Flight 10']);

    // Retrieve by name, or instantiate with the name, delayed, and arrival_time attributes...
    $flight = App\Flight::firstOrNew(
        ['name' => 'Flight 10'],
        ['delayed' => 1, 'arrival_time' => '11:30']
    );

#### `updateOrCreate`

또한 모델이 존재하는 경우에 이를 업데이트하고, 존재하지 않는 경우에는 새로운 모델을 생성할 수도 있습니다. 라라벨은 이런 경우 한번에 처리할 수 있는 `updateOrCreate` 메소드를 제공합니다. `firstOrCreate`메소드 처럼 `updateOrCreate` 모델을 직접 저장하므로, `save()` 메소드를 호출할 필요가 없습니다.

    // If there's a flight from Oakland to San Diego, set the price to $99.
    // If no matching model exists, create one.
    $flight = App\Flight::updateOrCreate(
        ['departure' => 'Oakland', 'destination' => 'San Diego'],
        ['price' => 99, 'discounted' => 1]
    );

<a name="deleting-models"></a>
## 모델 삭제하기

모델을 삭제하기 위해서는 모델 인스턴스에 `delete` 메소드를 호출하면 됩니다.

    $flight = App\Flight::find(1);

    $flight->delete();

#### 키를 통해서 이미 존재하는 모델 삭제하기

위의 예제에서는 `delete` 메소드를 호출하기 전에 데이터베이스에서 모델을 조회합니다. 하지만 모델의 기본 키를 알고 있다면 모델을 조회하지 않고 바로 삭제할 수 있습니다. `destroy` 메소드는 단일 기본 키를 인수로 사용하는 것 이외에도 여러개의 기본 키, 기본 키의 배열 또는 기본키의 [collection](/docs/{{version}}/collections)를 허용합니다.

    App\Flight::destroy(1);

    App\Flight::destroy(1, 2, 3);

    App\Flight::destroy([1, 2, 3]);

    App\Flight::destroy(collect([1, 2, 3]));

#### 쿼리를 통해서 모델 삭제하기

모델들에 대해서 삭제 구문을 실행할 수도 있습니다. 아래의 예제는 비활성으로 표시된 모든 항공편들을 삭제할 것입니다. 대량 수정과 같이 대량으로 삭제하는 것은 삭제된 모델에 대한 어떠한 모델 이벤트도 발생시키지 않을 것입니다.

    $deletedRows = App\Flight::where('active', 0)->delete();

> {note} Eloquent 를 통해서 대량으로 삭제 구문을 실행할 때, 삭제되는 모델에 대한 `deleting` 및  `deleted` 모델 이벤트는 발생되지 않습니다. 이 이유는 삭제 구문을 실행할 때 실제로 모델이 조회 되는 것이 아니기 때문입니다.

<a name="soft-deleting"></a>
### 소프트 삭제하기

실제로 데이터베이스에서 기록을 삭제하는 것 외에도 Eloquent는 모델을 "소프트 삭제(일종의 임시 삭제)"할 수 있습니다. 소프트 삭제된 모델은 실제로 데이터베이스에서 삭제된 것은 아닙니다. 대신 모델에 `deleted_at` 속성이 지정되어 데이터베이스에 입력됩니다. 모델이 null이 아닌 `deleted_at` 값을 가진다면 그 모델은 소프트 삭제된 것입니다. 모델이 소프트 삭제되는 것을 허용하기 위해 모델에 `Illuminate\Database\Eloquent\SoftDeletes` 속성을 사용하세요:

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;
    use Illuminate\Database\Eloquent\SoftDeletes;

    class Flight extends Model
    {
        use SoftDeletes;
    }

> {tip} `소프트 삭제` 의 특성은 자동으로 `deleted_at` 의 속성을 `DateTime` / `Carbon` 인스턴스에 반영됩니다.

데이터베이스 테이블에 `deleted_at` 컬럼을 추가해야 합니다. 라라벨의 [스키마 빌더](/docs/{{version}}/migrations)는 이 컬럼을 생성하는 도우미 메소드를 가지고 있습니다.

    Schema::table('flights', function (Blueprint $table) {
        $table->softDeletes();
    });

이제 모델에 `delete` 메소드를 호출하면 `deleted_at` 컬럼은 현재 날짜와 시간에 맞춰질 것입니다. 또한 소프트 삭제된 모델을 쿼리할 때 소프트 삭제된 모든 모델은 자동적으로 쿼리 결과에서 제외됩니다.

주어지 모델 인스턴스가 소프트 삭제되었는지 확인하려면 `trashed` 메소드를 사용하세요:

    if ($flight->trashed()) {
        //
    }

<a name="querying-soft-deleted-models"></a>
### 소프트 삭제된 모델 쿼리하기

#### 소프트 삭제된 모델 포함하기

위에서 본 바와 같이, 소프트 삭제된 모델들은 쿼리 결과에서 자동으로 제외됩니다. 하지만 쿼리에 `withTrashed` 메소드를 쓰면 결과 세트에 소프트 삭제된 모델도 나타나도록 강제할 수 있습니다.

    $flights = App\Flight::withTrashed()
                    ->where('account_id', 1)
                    ->get();

`withTrashed` 메소드는 [관계](/docs/{{version}}/eloquent-relationships) 쿼리에서도 사용될 수 있습니다.

    $flight->history()->withTrashed()->get();

#### 소프트 삭제된 모델만 가져오기

`onlyTrashed` 메소드는 소프트 삭제된 모델만 가져옵니다.

    $flights = App\Flight::onlyTrashed()
                    ->where('airline_id', 1)
                    ->get();

#### 소프트 삭제된 모델 복구하기

때로는 소프트 삭제된 모델의 삭제를 취소하고 싶을 수도 있습니다. 소프트 삭제된 모델을 활성화 상태로 복구하려면 모델 인스턴스에 `restore` 메소드를 사용하면 됩니다.

    $flight->restore();

여러 개의 모델을 빠르게 복구할 때도 `restore` 메소드를 쿼리에 사용할 수 있습니다. 다시한번 말하지만, 다른 "대량" 실행들처럼, 복구되는 모델에 대한 어떠한 모델 이벤트도 발생하지 않습니다.

    App\Flight::withTrashed()
            ->where('airline_id', 1)
            ->restore();

`withTrashed` 메소드 같이 `restore` 메소드도 [관계](/docs/{{version}}/eloquent-relationships)에 쓰일 수 있습니다.

    $flight->history()->restore();

#### 모델을 영구적으로 삭제하기

데이터베이스에서 완전히 모델을 삭제해야 할 때가 있을 것입니다. 데이터베이스에서 소프트 삭제된 모델을 영구적으로 삭제하기 위해서는 `forceDelete` 모델을 사용하면 됩니다.

    // Force deleting a single model instance...
    $flight->forceDelete();

    // Force deleting all related models...
    $flight->history()->forceDelete();

<a name="query-scopes"></a>
## 쿼리 스코프

<a name="global-scopes"></a>
### 글로벌 스코프

글로벌 스코프는 주어진 모델의 모든 쿼리에 범위 제한을 추가할 수 있도록 해줍니다. 라라벨의 고유한 [소프트 삭제](#soft-deleting) 기능은 데이터베이스에서 "삭제되지 않은" 모델에 대해서 글로벌 스코프를 사용합니다. 여러분의 고유한 글로벌 스코프를 작성하는 것은 주어진 모델이 특정 제한을 전달 받을 수 있도록 모든 쿼리에 추가하는 보다 편리하고 손쉬운 방법을 제공할 수 있습니다.

#### 글로벌 스코프 작성하기

글로벌 스코프를 작성하는 것은 쉽습니다. `Illuminate\Database\Eloquent\Scope` 인터페이스의 구현 클래스를 정의합니다. 이 인터페이스는 `apply` 메소드를 구현해야 합니다. `apply` 메소드는 필요한 `where` 조건을 쿼리에 추가할 수 있습니다.

    <?php

    namespace App\Scopes;

    use Illuminate\Database\Eloquent\Builder;
    use Illuminate\Database\Eloquent\Model;
    use Illuminate\Database\Eloquent\Scope;

    class AgeScope implements Scope
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
            $builder->where('age', '>', 200);
        }
    }

> {tip} 글로벌 스코프가 쿼리의 select 절에 컬럼을 추가하는 경우 select 대신 `addSelect` 메소드를 사용해야 합니다. 이렇게 하면 쿼리의 기존 `select` 절이 교체되는 실수를 방지할 수 있습니다.

#### 글로벌 스코프 적용하기

글로벌 스코프를 모델에 할당하려면, 주어진 모델의 `boot` 메소드를 오버라이딩 하여 `addGlobalScope` 메소드를 사용해야 합니다.

    <?php

    namespace App;

    use App\Scopes\AgeScope;
    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * The "booting" method of the model.
         *
         * @return void
         */
        protected static function boot()
        {
            parent::boot();

            static::addGlobalScope(new AgeScope);
        }
    }

스코프가 추가되면, `User::all()`은 자동으로 다음과 같은 SQL을 생성할 것입니다.

    select * from `users` where `age` > 200

#### 익명의 글로벌 스코프

Eloquent는 또한 별도의 분리된 클래스로 구성하지 않아도 될만큼 간단한 스코프를 구성할 때 유용하도록, 특별히 클로저를 사용하여 글로벌 스코프를 정의할 수도 있습니다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Builder;
    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * The "booting" method of the model.
         *
         * @return void
         */
        protected static function boot()
        {
            parent::boot();

            static::addGlobalScope('age', function (Builder $builder) {
                $builder->where('age', '>', 200);
            });
        }
    }

#### 글로벌 스코프 삭제하기

주어진 쿼리에 대해서 글로벌 스코프를 제거하고자 한다면, `withoutGlobalScope` 메소드를 사용하면 됩니다. 이 메소드는 글로벌 스코프의 클래스 이름을 인자로 받아들입니다.

    User::withoutGlobalScope(AgeScope::class)->get();

혹은, closure를 이용하여 글로벌 스코프를 정의할 수 있습니다.

    User::withoutGlobalScope('age')->get();

몇몇 또는 모든 글로벌 스코프를 제거하고자 한다면, 다음처럼 `withoutGlobalScopes` 메소드를 사용할 수 있습니다.

    // Remove all of the global scopes...
    User::withoutGlobalScopes()->get();

    // Remove some of the global scopes...
    User::withoutGlobalScopes([
        FirstScope::class, SecondScope::class
    ])->get();

<a name="local-scopes"></a>
### 로컬 스코프

로컬 스코프는 애플리케이션에서 손쉽게, 반복적으로 사용할 수 있는 공통의 범위 제한을 정의할 수 있게 해줍니다. 예로 들어 여러분은 종종 "인기가 높은" 것으로 생각되는 사용자를 조회해야 한다고 합시다. 스코프를 정의하기 위해서는 Eloquent 메소드의 이름에 `scope` 를 접두어로 붙이면 됩니다.

스코프는 항상 쿼리 빌더 인스턴스를 반환할 것입니다.

    <?php

    namespace App;

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
         * @return \Illuminate\Database\Eloquent\Builder
         */
        public function scopeActive($query)
        {
            return $query->where('active', 1);
        }
    }

#### 로컬 스코프 활용하기

스코프가 정의되면 모델을 질의할 때 스코프 메소드를 호출할 수 있습니다. 하지만 메소드를 호출할 때는 `scope` 접두어를 포함하면 안됩니다. 또한 다음의 예에서 볼 수 있듯이 다양한 스코프를 연결하여 호출할 수도 있습니다.

    $users = App\User::popular()->active()->orderBy('created_at')->get();

`or` 쿼리 연산자를 통해 여러개의 Eloquent 모델 범위를 결합하려면 다음과 같은 클로져 콜백을 사용합니다.

    $users = App\User::popular()->orWhere(function (Builder $query) {
        $query->active();
    })->get();

그러나, 이것은 번거로울 수 있기 때문에, 라라벨은 클로져를 사용하지 않고도 이러한 스코프를 함께 유용하게 사용할 수 있는 `orWhere` 메소드를 제공합니다.

    $users = App\User::popular()->orWhere->active()->get();


#### 다이나믹 스코프

때로는 파라미터를 수용하는 스코프를 정의하고자 할 수도 있습니다. 이를 위해서는 먼저 간단하게 스코프에 새 파라미터들을 추가하십시오. 스코프 파라미터는 `$query` 인자 뒤에 정의될 것입니다.

    <?php

    namespace App;

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

이제 스코프를 호출할 때 파라미터를 전달할 수 있습니다.

    $users = App\User::ofType('admin')->get();

<a name="comparing-models"></a>
## 모델의 비교

때로는 두개의 모델이 "동일한지" 판단할 필요가 있을 수도 있습니다. `is` 메소드는 두개의 모델이 동일한 primary key, 테이블, 데이터베이스 커넥션을 가지고 있는지 확인하는데 사용할 수 있습니다.

    if ($post->is($anotherPost)) {
        //
    }

<a name="events"></a>
## 이벤트

Eloquent 모델은 여러 이벤트들을 발생시켜 모델의 라이프사이클의 다양한 지점에 후킹할 수 있도록 합니다. `retrieved`, `creating`, `created`, `updating`, `updated`, `saving`, `saved`, `deleting`, `deleted`, `restoring`, `restored`. 이벤트들은 특정 모델 클래스가 데이터베이스에 저장되거나 업데이트될 때마다 코드를 실행하기 용이하게 해줍니다. 각 이벤트는 생성자를 통해 모델의 인스턴스를 받습니다.

데이터베이스에서 모델이 존재하고 조회가 되었을때 `retrieved` 이벤트가 발생합니다. 새로운 모델이 처음으로 저장되었을 때 `creating`과 `created` 이벤트가 발생합니다. 모델이 이미 데이터베이스에 존재할 때 `save` 메소드가 호출된다면 `updating` / `updated` 이벤트가 발생합니다. 하지만 이 두 경우 모두 `saving` / `saved` 이벤트가 발생할 것입니다.

> {note} Eloquent를 통해 대량 업데이트 또는 삭제를 할 때 변경된 된 모델의 `saved`, `updated`, `deleting` 및 `deleted` 모델 이벤트는 실행되지 않습니다. 이것은 대량 업데이트를 발행 할 때 모델이 실제로 검색되지 않기 때문입니다.

이렇게 하기 위해서, Eloquent 모델의 라이프사이클의 다양한 지점을 고유한 이벤트 클래스에 매핑하는 `$dispatchesEvents` 속성을 Eloquent 모델에 정의하면 됩니다.

    <?php

    namespace App;

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

Eloquent 이벤트를 정의하고 매핑 한 후 [이벤트 리스너](/docs/{{version}}/events#defining-listeners)를 사용하여 이벤트를 처리 할 수 있습니다.

<a name="observers"></a>
### 옵저버

#### 옵저버 객체 정의하기

주어진 모델을 여러 이벤트들을 수신하고자 하는 경우, 옵저버를 사용하여 모든 리스너를 하나의 클래스로 구성할 수 있습니다. 옵저버 클래스는 수신하고자 하는 Eloquent 이벤트에 대항하는 메소드 이름을 가집니다. 각각의 이 메소드들은 인자로 모델을 전달 받습니다. `make:observer` 아티즌 명령어는 새로운 옵저버 클래스를 생성하는 가장 쉬운 방법입니다.

    php artisan make:observer UserObserver --model=User

이 명령어는 새로운 옵저버 클래스를 `App/Observers` 디렉토리에 생성합니다. 이 디렉토리가 존재하지 않는다면, 아티즌은 이 디렉토리를 자동으로 생성합니다. 새로운 옵저버 클래스는 다음과 같은 형태입니다.

    <?php

    namespace App\Observers;

    use App\User;

    class UserObserver
    {
        /**
         * Handle the User "created" event.
         *
         * @param  \App\User  $user
         * @return void
         */
        public function created(User $user)
        {
            //
        }

        /**
         * Handle the User "updated" event.
         *
         * @param  \App\User  $user
         * @return void
         */
        public function updated(User $user)
        {
            //
        }

        /**
         * Handle the User "deleted" event.
         *
         * @param  \App\User  $user
         * @return void
         */
        public function deleted(User $user)
        {
            //
        }
    }

옵저버를 등록하기 위해서는, 관찰할 모델에 대해 `observe` 메소드를 사용하면 됩니다. 서비스 프로바이더의 `boot` 메소드안에서 옵저버를 등록할 수 있습니다. 다음은 예제는 `AppServiceProvider` 에서 옵저버를 등록하는 예제 입니다.

    <?php

    namespace App\Providers;

    use App\Observers\UserObserver;
    use App\User;
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
            User::observe(UserObserver::class);
        }
    }

