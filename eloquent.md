# Eloquent: 시작하기

- [소개](#introduction)
- [모델 정의하기](#defining-models)
    - [Eloquent 모델 컨벤션](#eloquent-model-conventions)
- [여러개의 모델 가져오기](#retrieving-multiple-models)
- [하나의 모델 / 합계를 찾기](#retrieving-single-models)
    - [합계 가져오기](#retrieving-aggregates)
- [모델을 통한 추가 및 삭제하기](#inserting-and-updating-models)
    - [기본적인 추가](#basic-inserts)
    - [기본적인 업데이트](#basic-updates)
    - [대량 할당 - Mass Assignment](#mass-assignment)
- [모델 삭제하기](#deleting-models)
    - [소프트 삭제하기](#soft-deleting)
    - [소프트 삭제된 모델 쿼리하기](#querying-soft-deleted-models)
- [쿼리 스코프](#query-scopes)
    - [글로벌 스코프](#global-scopes)
    - [로컬 스코프](#local-scopes)
- [이벤트](#events)

<a name="introduction"></a>
## 소개

라라벨에 포함된 Eloquent ORM은 여러분의 데이터베이스에서 동작하는 아름답고 심플한 액티브 레코드를 제공합니다. 각각의 데이터베이스 테이블은 이에 해당하는 "모델"을 가지고 있습니다.

시작하기에 앞서 `config/database.php` 에 데이터베이스 커넥션이 설정되어 있는지 확인하십시오.

<a name="defining-models"></a>
## 모델 정의하기 

시작하기 위해서 Eloquent 모델 하나를 생성합니다. 일반적으로 모델은 `app` 디렉토리에 존재하지만, `composer.json` 파일에 의해서 오토로드 되는 곳이라면 어느곳에든 위치해도 상관없습니다. 모든 Eloquent 모델은 `Illuminate\Database\Eloquent\Model`을 상속받습니다.

모델 인스턴스를 생성하는 가장 쉬운 방법은 `make:model` [아티즌 커맨드](/docs/{{version}}/artisan)를 사용하는 것입니다: 

    php artisan make:model User

모델을 생성할 때 [데이터 마이그레이션](/docs/{{version}}/migrations)을 생성하고 싶다면 `--migration` 혹은 `-m` 옵션을 사용할 수 있습니다. 

    php artisan make:model User -m

<a name="eloquent-model-conventions"></a>
### Eloquent 모델 컨벤션

이제 `flights` 데이터베이스 테이블에서 정보를 찾거나 저장할 때 쓸 `Flight` 모델 클래스의 예를 보겠습니다: 

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Flight extends Model
    {
        //
    }

#### 테이블 이름

생성한 `Flight` 모델에서 어떠한 테이블을 사용해야할지 Elquent 에게 알려주지 않았다는 점을 주목하십시오. 관련된 테이블이 별도로 지정되지 않는다면 클래스의 "스네이크 케이스" 로 표시된 복수형의 이름이 사용되어 집니다. 따라서 이 예제에서는 Eloquent는 `Flight` 모델은 'flights` 테이블에 레코드를 저장한다고 추정할 것입니다. 여러분은 모델의 `table` 속성을 통해서 고유한 테이블을 지정할 수 있습니다:

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

Eloquent는 테이블의 primary key 컬럼의 이름을 `id`로 추정합니다. `$primaryKey` 속성을 통해서 이 컬럼명을 재정의할 수 있습니다. 

추가적으로, Eloquent 는 primary key가 증가하는 정수값(incrementing)이라고 추정합니다. 이는 기본적으로 primary Key를 `int`로 자동 캐시팅 한다는 것을 의미합니다. 증가하지 않는(non-incrementing) 또는 숫자형이 아닌 primary key를 사용하고자 한다면, 모델의 public `$incrementing` 속성을 `false` 로 설정해야 합니다. 

#### Timestamps

기본적으로, Eloquent는 테이블에 `created_at` 과 `updated_at` 컬럼이 존재한다고 생각합니다. 자동으로 이 컬럼값이 채워지기를 원하지 않는다면 `$timestamps` 속성을 `false` 로 지정하십시오.

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

Timestamp의 포맷을 변경하여야 한다면 모델에 `$dateFormat` 속성을 지정하면 됩니다. 이 속성은 날짜 속성이 데이터베이스에 저장될 때의 형식과 모델이 배열이나 JSON으로 직렬화되었을 때의 형식을 결정합니다. 

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

#### 데이터베이스 커넥션

기본적으로 모든 Eloquent 모델은 애플리케이션에서 기본으로 셋팅되어 있는 커넥션을 사용합니다. 다른 커넥션을 지정하고 싶다면 `$connection` 속성을 이용하면 됩니다:

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

<a name="retrieving-multiple-models"></a>
## 여러개의 모델 가져오기

하나의 모델과 [해당 모델에 지정된 데이터베이스 테이블](/docs/{{version}}/migrations#writing-migrations)을 생성하였다면, 이제 데이터베이스에서 데이터를 조회할 수 있습니다. 각 Eloquent 모델을 데이터베이스 쿼리를 용이하게 해주는 강력한 [쿼리 빌더](/docs/{{version}}/queries)로 생각하십시오. 예를 들자면: 

    <?php

    namespace App\Http\Controllers;

    use App\Flight;
    use App\Http\Controllers\Controller;

    class FlightController extends Controller
    {
        /**
         * Show a list of all available flights.
         *
         * @return Response
         */
        public function index()
        {
            $flights = Flight::all();

            return view('flight.index', ['flights' => $flights]);
        }
    }

#### 컬럼의 값 보기

Eloqent 모델의 인스턴스를 획득했다면 컬럼의 값을 얻기 위해서 대응되는 속성에 엑세스 하면 됩니다. 예를 들어 다음은 쿼리를 통해 반환된 `Flight` 인스턴스들에서 반복문을 통해 `name` 컬럼의 값을 출력합니다: 

    foreach ($flights as $flight) {
        echo $flight->name;
    }

#### 조건들 추가하기

Eloquent의 `all` 메소드는 모델의 테이블에서 모든 결과를 반환할 것입니다. 각 Eloquent 모델은 [쿼리 빌더](/docs/{{version}}/queries)의 역할을 하기 때문에 쿼리에 다양한 조건들을 추가할 수 있고, 마지막에 `get` 메소드를 사용하여 결과를 조회할 수 있습니다: 

    $flights = App\Flight::where('active', 1)
                   ->orderBy('name', 'desc')
                   ->take(10)
                   ->get();

> **주의:** Eloquent 모델은 모두가 쿼리 빌더이기 때문에, [쿼리 빌더](/docs/{{version}}/queries)에서 제공되는 모든 메소드를 살펴 보시기 바랍니다. 여기의 어떠한 메소드라도 Eloquent 쿼리에서 사용할 수 있습니다. 

#### 컬렉션

`all`과 `get` 같이 여러개의 결과를 가져오는 Eloquent 메소드의 경우 `Illuminate\Database\Eloquent\Collection` 인스턴스가 반환됩니다. `Collection` 클래스는 Eloquent 결과에 사용할 수 있는 [다양한 메소드](/docs/{{version}}/eloquent-collections#available-methods)들을 제공합니다. 물론 배열과 동일하게 이 컬렉션을 반복문에서 사용 할 수 있습니다: 

    foreach ($flights as $flight) {
        echo $flight->name;
    }

#### 결과 분할하기

수천개의 Eloquent 모델이 필요 할 때에는 `chunk` 명령어를 사용하십시오. `chunk` 메소드는 "분할된" Eloquent 모델들을 가져올 것이며 주어진 `Closure`에 의해서 처리될 것입니다. `chunk` 메소드를 이용하면 결과가 아주 큰 경우 메모리를 절약할 수 있을 것입니다. 

    Flight::chunk(200, function ($flights) {
        foreach ($flights as $flight) {
            //
        }
    });

메소드의 첫번째 인자는 "chunk" 메소드에서 받아 들일 레코드의 갯수 입니다. 두번째 인자는 클로저로 데이터베이스로 부터 분할된 데이터들을 전달 받습니다.

> **주의:** 데이터베이스 쿼리는 매번마다 다시 실행됩니다.

#### 커서 사용하기

`cursor` 메소드는 커서를 사용하여 데이터베이스 레코드 전체를 반복할 수 있게 합니다. 대량의 데이터럴 처리하는 경우에, `cursor` 메소드는 메모리 사용량을 크게 줄여줍니다:

    foreach (Flight::where('foo', 'bar')->cursor() as $flight) {
        //
    }

<a name="retrieving-single-models"></a>
## 하나의 모델 / 합계를 찾기

테이블에서 모든 정보를 조회하는 것 외에도 `find`와 `first`를 이용해서 하나의 레코드를 찾을 수 있습니다. 이 메소드들은 모델 컬렉션을 반환하는 대신 모델 인스턴스 하나를 반환합니다: 

    // Retrieve a model by its primary key...
    $flight = App\Flight::find(1);

    // Retrieve the first model matching the query constraints...
    $flight = App\Flight::where('active', 1)->first();

또한 `find` 메소드를 primary key 의 배열과 함께 사용하여 매칭되는 레코드들의 컬렉션을 반환받을 수 있습니다:

    $flights = App\Flight::find([1, 2, 3]);

#### Not Found Exceptions 

모델을 찾지 못했을 때에는 Exception을 던지고 싶을 수도 있으며 특히 라우트나 컨트롤러에서 유용합니다. `findOrFail`와 `firstOrFail` 메소드는 쿼리의 첫번째 결과를 반환합니다. 하지만 결과를 찾을 수 없을 때에는 `Illuminate\Database\Eloquent\ModelNotFoundException`가 던져질 것입니다:

    $model = App\Flight::findOrFail(1);

    $model = App\Flight::where('legs', '>', 100)->firstOrFail();

예외를 처리하지 않는다면 `404` HTTP 응답이 자동으로 사용자에게 보내질 것이기 때문에, 이 메소드들을 쓸 때 `404` 응답을 반환하는 것을 명시적으로 선언할 필요는 없습니다. 

    Route::get('/api/flights/{id}', function ($id) {
        return App\Flight::findOrFail($id);
    });

<a name="retrieving-aggregates"></a>
### 합계 가져오기

또한 [쿼리 빌더](/docs/{{version}}/queries)가 제공하는 `count`, `sum`, `max`을 비롯한 [합계 함수](/docs/{{version}}/queries#aggregates)를 이용할 수 있습니다. 이 메소드들은 모델의 인스턴스 대신 적절한 스칼라 값을 반환합니다. 

    $count = App\Flight::where('active', 1)->count();

    $max = App\Flight::where('active', 1)->max('price');

<a name="inserting-and-updating-models"></a>
## 모델을 통한 추가 및 수정하기 

<a name="basic-inserts"></a>
### 기본적인 추가

데이터베이스에 새로운 레코드를 생성하기 위해는, 단순히 새 모델의 인스턴스를 생성하고 모델의 속성을 설정하여, `save` 메소드를 호출하면 됩니다: 

    <?php

    namespace App\Http\Controllers;

    use App\Flight;
    use Illuminate\Http\Request;
    use App\Http\Controllers\Controller;

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

<a name="basic-updates"></a>
### 기본적인 업데이트

`save` 메소드는 데이터베이스에 이미 존재하는 모델들을 업데이트 하기 위해 사용될 수 있습니다. 모델을 업데이트하기 위해서는 모델을 조회한 다음, 업데이트하기 원하는 속성을 수정한 뒤 `save` 메소드를 호출합니다. 이 때에도 `updated_at` 타임스탬프는 자동으로 설정되며 수동으로 값을 지정할 필요가 없습니다: 

    $flight = App\Flight::find(1);

    $flight->name = 'New Flight Name';

    $flight->save();

주어진 쿼리에 일치하는 여러개의 모델들에 대해서 업데이트를 할 수 있습니다. 다음의 예제에서는 `active` 하면서, `destination` 이 `San Diego` 인 모든 비행편들이 연기되었다고 표시될 것입니다:

    App\Flight::where('active', 1)
              ->where('destination', 'San Diego')
              ->update(['delayed' => 1]);

`update` 메소드는 컬럼의 정렬과 업데이트 될 컬럼을 대표하는 값의 쌍을 필요로 합니다. 

<a name="mass-assignment"></a>
### 대량 할당 - Mass Assignment 

`create` 메소드를 통해 한번에 새로운 모델을 추가할 수도 있습니다. 메소드를 통해 추가된 모델 인스턴스가 결과로 반환될 것입니다. 하지만 모든 Eloquent 모델은 대량 할당-Mass Assignment 으로부터 보호되기 때문에, 이렇게 하기 전에 모델의 `fillable`나 `guarded` 속성을 지정해야 주어야 합니다. 

대량 할당(Mass Assignment)의 취약성은 사용자가 예상치 못한 HTTP 요청 파라미터를 전달했을 때 발생하며, 해당 파라미터는 데이터베이스의 예상하지 못한 컬럼을 변경하게 됩니다. 예를 들어 악의적인 사용자는 HTTP 요청을 통해 `is_admin`을 전달할 수 있으며 이 파라미터는 모델의 `create` 메소드에 매핑되어 사용자를 관리자로 승격할 수 있습니다. 

따라서 시작하기 위해서는 대량 할당(Mass Assignment)할 모델의 속성을 정의해야 하며 이는 모델에 `$fillable` 속성을 사용해서 할 수 있습니다. 예를 들어 `Flight` 모델에서 `name` 속성을 대량 할당(Mass Assignment)될 수 있도록 만들 수 있습니다: 

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

속성을 대량 할당될(mass assignable) 수 있도록 만든 뒤에는 `create` 메소드로 데이터베이스에 새로운 레코드을 추가할 수 있습니다. `create` 메소드는 저장된 모델 인스턴스를 반환합니다: 

    $flight = App\Flight::create(['name' => 'Flight 10']);

`$fillable`는 대량 할당(mass assign)될 수 있는 속성들의 화이트 리스트로써 동작 하지만, `$guarded`를 사용할 수도 있습니다. `$guarded` 속성은 대량 할당(mass assign)하고 싶지 않은 속성들의 배열을 가지고 있을 것입니다. 이 배열에 포함되지 않은 모든 속성들은 대량 할당될 수 있을 것입니다. 따라서 `$guarded`는 블랙리스트와 같은 기능을 합니다. 물론 `$fillable`나 `$guarded` 둘 중 하나만을 사용해야 합니다: 

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

위의 예에서 **`price`를 제외한** 모든 속성들은 대량 할당(mass assign)될 수 있습니다.  

#### 기타 생성을 위한 메소드들

속성을 대량 할당(mass assign) 하여 모델을 생성하는 또다른 방법은 `firstOrCreate`와 `firstOrNew`의 두 가지가 있습니다. `firstOrCreate` 메소드는 주어진 컬럼 / 값의 쌍을 이용하여 데이터베이스 레코드를 찾으려고 시도할 것입니다. 데이터베이스에서 모델을 찾을 수 없으면 주어진 속성들을 통해 새로운 레코드가 입력될 것입니다. 

`firstOrCreate`와 같이 `firstOrNew` 메소드도 주어진 속성들에 해당하는 레코드를 데이터베이스에서 찾으려고 시도할 것입니다. 하지만 모델을 찾을 수 없으면 새로운 모델 인스턴스가 반환될 것입니다. `firstOrNew`에 의해 반환된 모델은 아직 데이터베이스에서 저장되지 않았다는 점에 주의하십시오. 모델을 저장하기 위해서는 `save`를 수동으로 호출해야 합니다: 

    // Retrieve the flight by the attributes, or create it if it doesn't exist...
    $flight = App\Flight::firstOrCreate(['name' => 'Flight 10']);

    // Retrieve the flight by the attributes, or instantiate a new instance...
    $flight = App\Flight::firstOrNew(['name' => 'Flight 10']);

<a name="deleting-models"></a>
## 모델 삭제하기 

모델을 삭제하기 위해서는 모델 인스턴스에 `delete` 메소드를 호출하면 됩니다:

    $flight = App\Flight::find(1);

    $flight->delete();

#### 키를 통해서 이미 존재하는 모델 삭제하기 

위의 예제에서는 `delete` 메소드를 호출하기 전에 데이터베이스에서 모델을 조회합니다. 하지만 모델의 프라이머리 키를 알고 있다면 모델을 조회하지 않고 바로 삭제할 수 있습니다. 이렇게 하기 위해서는 `destroy` 메소드를 호출합니다: 

    App\Flight::destroy(1);

    App\Flight::destroy([1, 2, 3]);

    App\Flight::destroy(1, 2, 3);

#### 쿼리를 통해서 모델 삭제하기

물론 모델들에 대해서 삭제 쿼리를 실행할 수도 있습니다. 아래의 예제는 비활성으로 표시된 항공편을 모두 삭제할 것입니다: 

    $deletedRows = App\Flight::where('active', 0)->delete();

<a name="soft-deleting"></a>
### 소프트 삭제하기

실제로 데이터베이스에서 기록을 삭제하는 것 외에도 Eloquent는 모델을 "소프트 삭제(일종의 임시 삭제)"할 수 있습니다. 소프트 삭제된 모델은 실제로 데이터베이스에서 삭제된 것은 아닙니다. 대신 모델에 `deleted_at` 속성이 지정되어 데이터베이스에 입력됩니다. 모델이 null이 아닌 `deleted_at` 값을 가진다면 그 모델은 소프트 삭제된 것입니다. 모델이 소프트 삭제되는 것을 허용하기 위해 모델에 `Illuminate\Database\Eloquent\SoftDeletes` 속성을 이용하고 `$dates` 속성에 `deleted_at` 컬럼을 추가하세요: 

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;
    use Illuminate\Database\Eloquent\SoftDeletes;

    class Flight extends Model
    {
        use SoftDeletes;

        /**
         * The attributes that should be mutated to dates.
         *
         * @var array
         */
        protected $dates = ['deleted_at'];
    }

물론 데이터베이스 테이블에 `deleted_at` 컬럼을 추가해야 합니다. 라라벨의 [스키마 빌더](/docs/{{version}}/migrations)는 이 컬럼을 생성하는 도우미 메소드를 가지고 있습니다: 

    Schema::table('flights', function ($table) {
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

위에서 본 바와 같이, 소프트 삭제된 모델들은 쿼리 결과에서 자동으로 제외됩니다. 하지만 쿼리에 `withTrashed` 메소드를 쓰면 결과 세트에 소프트 삭제된 모델도 나타나도록 강제할 수 있습니다: 

    $flights = App\Flight::withTrashed()
                    ->where('account_id', 1)
                    ->get();

`withTrashed` 메소드는 [관계](/docs/{{version}}/eloquent-relationships) 쿼리에서도 사용될 수 있습니다: 

    $flight->history()->withTrashed()->get();

#### 소프트 삭제된 모델만 가져오기

`onlyTrashed` 메소드는 소프트 삭제된 모델만 가져옵니다: 

    $flights = App\Flight::onlyTrashed()
                    ->where('airline_id', 1)
                    ->get();

#### 소프트 삭제된 모델 복구하기 

때로는 소프트 삭제된 모델의 삭제를 취소하고 싶을 수도 있습니다. 소프트 삭제된 모델을 활성화 상태로 복구하려면 모델 인스턴스에 `restore` 메소드를 사용하면 됩니다: 

    $flight->restore();

여러 개의 모델을 빠르게 복구할 때도 `restore` 메소드를 쿼리에 사용할 수 있습니다. 

    App\Flight::withTrashed()
            ->where('airline_id', 1)
            ->restore();

`withTrashed` 메소드 같이 `restore` 메소드도 [관계](/docs/{{version}}/eloquent-relationships)에 쓰일 수 있습니다: 

    $flight->history()->restore();

#### 모델을 영구적으로 삭제하기

데이터베이스에서 완전히 모델을 삭제해야 할 때가 있을 것입니다. 데이터베이스에서 소프트 삭제된 모델을 영구적으로 삭제하기 위해서는 `forceDelete` 모델을 사용하면 됩니다: 

    // Force deleting a single model instance...
    $flight->forceDelete();

    // Force deleting all related models...
    $flight->history()->forceDelete();

<a name="query-scopes"></a>
## 쿼리 스코프

<a name="global-scopes"></a>
### 글로벌 스코프

글로벌 스코프는 주어진 모델의 **모든** 쿼리에 범위 제한을 추가할 수 있도록 해줍니다. 라라벨의 고유한 [소프트 삭제](#soft-deleting) 기능은 데이터베이스에서 "삭제되지 않은" 모델에 대해서 글로벌 스코프를 사용합니다. 여러분의 고유한 글로벌 스코프를 작성하는 것은 주어진 모델이 특정 제한을 전달 받을 수 있도록 모든 쿼리에 추가하는 보다 편리하고 손쉬운 방법을 제공할 수 있습니다. 

#### 글로벌 스코프 작성하기

글로벌 스코프를 작성하는 것은 쉽습니다. `Illuminate\Database\Eloquent\Scope` 인터페이스의 구현 클래스를 정의합니다. 이 인터페이스는 `apply` 메소드를 구현해야 합니다. `apply` 메소드는 필요한 `where` 조건을 쿼리에 추가할 수 있습니다:

    <?php

    namespace App\Scopes;

    use Illuminate\Database\Eloquent\Scope;
    use Illuminate\Database\Eloquent\Model;
    use Illuminate\Database\Eloquent\Builder;

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
            return $builder->where('age', '>', 200);
        }
    }

라라벨 애플리케이션에서 기본적으로 지정된 스코프 폴더가 없기 때문에, 라라벨 애플리케이션의 `app` 디렉토리에 자유롭게 `Scopes` 폴더를 만들 수 있습니다. 

#### 글로벌 스코프 적용하기

글로벌 스코프를 모델에 할당하려면, 주어진 모델의 `boot` 메소드를 오버라이딩 하여 `addGlobalScope` 메소드를 사용해야 합니다:

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

스코프가 추가되고나면, `User::all()`는 자동으로 다음과 같은 SQL을 생성할 것입니다:

    select * from `users` where `age` > 200

#### 익명의 글로벌 스코프

Eloquent는 또한 별도의 분리된 클래스로 구성하지 않아도 될만큼 간단한 스코프를 구성할 때 유용하도록, 특별히 클로저를 사용하여 글로벌 스코프를 정의할 수도 있습니다. 

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;
    use Illuminate\Database\Eloquent\Builder;

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

            static::addGlobalScope('age', function(Builder $builder) {
                $builder->where('age', '>', 200);
            });
        }
    }

`addGlobalScope()`의 첫번째 인자는 스코프를 삭제할 수 있는 식별자를 제공합니다:

    User::withoutGlobalScope('age')->get();

#### 글로벌 스코프 삭제하기 

주어진 쿼리에 대해서 글로벌 스코프를 제거하고자 한다면, `withoutGlobalScope` 메소드를 사용하면 됩니다:

    User::withoutGlobalScope(AgeScope::class)->get();

몇몇 또는 모든 글로벌 스코프를 제거하고자 한다면, 다음처럼 `withoutGlobalScopes` 메소드를 사용할 수 있습니다:

    User::withoutGlobalScopes()->get();

    User::withoutGlobalScopes([FirstScope::class, SecondScope::class])->get();

<a name="local-scopes"></a>
### 로컬 스코프

로컬 스코프는 애플리케이션에서 손쉽게, 반복적으로 사용할 수 있는 공통의 범위 제한을 정의할 수 있게 해줍니다. 예로 들어 여러분은 종종 "인기가 높은" 것으로 생각되는 사용자를  조회해야 한다고 합시다. 스코프를 정의하기 위해서는 간단하게 Eloquent 메소드의 이름에 `scope` 를 접두어로 붙이면 됩니다.

스코프는 항상 쿼리 빌더 인스턴스를 반환할 것입니다: 

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * Scope a query to only include popular users.
         *
         * @return \Illuminate\Database\Eloquent\Builder
         */
        public function scopePopular($query)
        {
            return $query->where('votes', '>', 100);
        }

        /**
         * Scope a query to only include active users.
         *
         * @return \Illuminate\Database\Eloquent\Builder
         */
        public function scopeActive($query)
        {
            return $query->where('active', 1);
        }
    }

#### 쿼리 스코프 활용하기

스코프가 정의되면 모델을 질의할 때 스코프 메소드를 호출할 수 있습니다. 하지만 메소드를 호출할 때는 `scope` 접두어를 포함할 필요는 없습니다. 또한 다음의 예에서 볼 수 있듯이 다양한 스코프를 연결하여 호출할 수도 있습니다: 

    $users = App\User::popular()->active()->orderBy('created_at')->get();

#### 다이나믹 스코프

때로는 파라미터를 수용하는 스코프를 정의하고자 할 수도 있습니다. 이를 위해서는 먼저 간단하게 스코프에 새 파라미터들을 추가하십시오. 스코프 파라미터는 `$query` 인자 뒤에 정의될 것입니다: 

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * Scope a query to only include users of a given type.
         *
         * @return \Illuminate\Database\Eloquent\Builder
         */
        public function scopeOfType($query, $type)
        {
            return $query->where('type', $type);
        }
    }


이제 스코프를 호출할 때 파라미터를 전달할 수 있습니다: 

    $users = App\User::ofType('admin')->get();

<a name="events"></a>
## 이벤트 

Eloquent 모델은 여러 이벤트들을 발생시켜 다음의 메소드들을 통해 모델의 라이프사이클의 다양한 지점에 후킹할 수 있도록 합니다: `creating`, `created`, `updating`, `updated`, `saving`, `saved`, `deleting`, `deleted`, `restoring`, `restored`. 이벤트들은 특정 모델 클래스가 데이터베이스에 저장되거나 업데이트될 때마다 코드를 실행하기 용이하게 해줍니다. 

<a name="basic-usage"></a>
### 기본적인 사용

새로운 모델이 처음으로 저장될 때마다 `creating`과 `created` 이벤트가 발생할 것입니다. 모델이 이미 데이터베이스에 존재할 때 `save` 메소드가 호출된다면 `updating` / `updated` 이벤트가 발생할 것입니다. 하지만 이 두 경우 모두 `saving` / `saved` 이벤트가 발생할 것입니다. 

예로 들어 [서비스 프로바이더](/docs/{{version}}/providers)에 Eloquent 이벤트 리스너를 정의할 수 있습니다. 이벤트 리스너 안에서 주어진 모델에 `isValid` 메소드를 호출하여 모델이 유효하지 않을 경우 `false`를 반환할 것입니다. Eloquent 이벤트 리스너로부터 `false`가 반환된다면 `save` / `update` 작업은 취소됩니다: 

    <?php

    namespace App\Providers;

    use App\User;
    use Illuminate\Support\ServiceProvider;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            User::creating(function ($user) {
                if ( ! $user->isValid()) {
                    return false;
                }
            });
        }

        /**
         * Register the service provider.
         *
         * @return void
         */
        public function register()
        {
            //
        }
    }
