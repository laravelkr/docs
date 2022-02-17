# Eloquent: Getting Started
# Eloquent: 시작하기

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Generating Model Classes](#generating-model-classes)
- [모델 클래스 생성](#generating-model-classes)
- [Eloquent Model Conventions](#eloquent-model-conventions)
- [Eloquent 모델 컨벤션](#eloquent-model-conventions)
    - [Table Names](#table-names)
    - [테이블 이름](#table-names)
    - [Primary Keys](#primary-keys)
    - [기본 키](#primary-keys)
    - [Timestamps](#timestamps)
    - [타임스탬프](#timestamps)
    - [Database Connections](#database-connections)
    - [데이터베이스 연결](#database-connections)
    - [Default Attribute Values](#default-attribute-values)
    - [기본 속성 값](#default-attribute-values)
- [Retrieving Models](#retrieving-models)
- [모델 조회하기](#retrieving-models)
    - [Collections](#collections)
    - [컬렉션](#collections)
    - [Chunking Results](#chunking-results)
    - [결과 분할하기](#chunking-results)
    - [Streaming Results Lazily](#streaming-results-lazily)
    - [스트리밍 결과 지연](#streaming-results-lazily)
    - [Cursors](#cursors)
    - [커서](#cursors)
    - [Advanced Subqueries](#advanced-subqueries)
    - [고급 서브쿼리](#advanced-subqueries)
- [Retrieving Single Models / Aggregates](#retrieving-single-models)
- [하나의 모델 / 집계 조회하기](#retrieving-single-models)
    - [Retrieving Or Creating Models](#retrieving-or-creating-models)
    - [모델 검색 또는 생성](#retrieving-or-creating-models)
    - [Retrieving Aggregates](#retrieving-aggregates)
    - [합계 조회하기](#retrieving-aggregates)
- [Inserting & Updating Models](#inserting-and-updating-models)
- [모델을 통한 추가 및 수정하기](#inserting-and-updating-models)
    - [Inserts](#inserts)
    - [Inserts](#inserts)
    - [Updates](#updates)
    - [Updates](#updates)
    - [Mass Assignment](#mass-assignment)
    - [대량 할당 - Mass Assignment](#mass-assignment)
    - [Upserts](#upserts)
    - [Upserts](#upserts)
- [Deleting Models](#deleting-models)
- [모델 삭제하기](#deleting-models)
    - [Soft Deleting](#soft-deleting)
    - [소프트 삭제하기](#soft-deleting)
    - [Querying Soft Deleted Models](#querying-soft-deleted-models)
    - [소프트 삭제된 모델 쿼리하기](#querying-soft-deleted-models)
- [Pruning Models](#pruning-models)
- [가지치기 모델](#pruning-models)
- [Replicating Models](#replicating-models)
- [모델 복제](#replicating-models)
- [Query Scopes](#query-scopes)
- [쿼리 스코프](#query-scopes)
    - [Global Scopes](#global-scopes)
    - [글로벌 스코프](#global-scopes)
    - [Local Scopes](#local-scopes)
    - [로컬 스코프](#local-scopes)
- [Comparing Models](#comparing-models)
- [모델의 비교](#comparing-models)
- [Events](#events)
- [이벤트](#events)
    - [Using Closures](#events-using-closures)
    - [클로저 사용하기](#events-using-closures)
    - [Observers](#observers)
    - [옵저버](#observers)
    - [Muting Events](#muting-events)
    - [이벤트 끄기(Muting Events)](#muting-events)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel includes Eloquent, an object-relational mapper (ORM) that makes it enjoyable to interact with your database. When using Eloquent, each database table has a corresponding "Model" that is used to interact with that table. In addition to retrieving records from the database table, Eloquent models allow you to insert, update, and delete records from the table as well.

라라벨에는 데이터베이스와 상호작용하는 것을 즐겁게 해주는 객체 관계 매퍼(ORM)인 Eloquent가 포함되어 있습니다. Eloquent를 사용할 때, 각 데이터베이스 테이블은 해당 테이블과 상호 작용하기 위해 사용되는 "모델(Model)"을 가집니다. 데이터베이스 테이블에서 레코드를 검색할 뿐만 아니라 Eloquent 모델을 사용하면 테이블에 레코드를 추가, 업데이트 및 삭제를 할 수 있습니다.

> {tip} Before getting started, be sure to configure a database connection in your application's `config/database.php` configuration file. For more information on configuring your database, check out [the database configuration documentation](/docs/{{version}}/database#configuration).

> {tip} 시작하기 전에, 애플리케이션의 `config/database.php` 구성 파일에서 데이터베이스 연결을 구성해야 합니다. 데이터베이스 구성에 대한 자세한 내용은 [데이터베이스 구성 문서](/docs/{{version}}/database#configuration)를 확인하세요.

<a name="generating-model-classes"></a>
## Generating Model Classes
## 모델 클래스 생성

To get started, let's create an Eloquent model. Models typically live in the `app\Models` directory and extend the `Illuminate\Database\Eloquent\Model` class. You may use the `make:model` [Artisan command](/docs/{{version}}/artisan) to generate a new model:

시작하기 위해 Eloquent 모델을 만들어 봅시다. 모델은 일반적으로 `app\Models` 디렉토리에 있으며 `Illuminate\Database\Eloquent\Model` 클래스를 확장합니다. `make:model` [Artisan 명령](/docs/{version}/artisan)을 사용하여 새 모델을 생성할 수 있습니다.


    php artisan make:model Flight

If you would like to generate a [database migration](/docs/{{version}}/migrations) when you generate the model, you may use the `--migration` or `-m` option:

모델을 생성할 때 [데이터베이스 마이그레이션](/docs/{{version}}/migrations)을 생성하고 싶다면 `--migration` 혹은 `-m` 옵션을 사용할 수 있습니다.

    php artisan make:model Flight --migration

You may generate various other types of classes when generating a model, such as factories, seeders, policies, controllers, and form requests. In addition, these options may be combined to create multiple classes at once:

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
## Eloquent Model Conventions
## Eloquent 모델 컨벤션

Models generated by the `make:model` command will be placed in the `app/Models` directory. Let's examine a basic model class and discuss some of Eloquent's key conventions:

`make:model` 명령으로 생성된 모델은 `app/Models` 디렉토리에 배치됩니다. 기본 모델 클래스를 살펴보고 Eloquent의 주요 규칙에 대해 논의해 보겠습니다.


    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class Flight extends Model
    {
        //
    }

<a name="table-names"></a>
### Table Names
### 테이블 이름

After glancing at the example above, you may have noticed that we did not tell Eloquent which database table corresponds to our `Flight` model. By convention, the "snake case", plural name of the class will be used as the table name unless another name is explicitly specified. So, in this case, Eloquent will assume the `Flight` model stores records in the `flights` table, while an `AirTrafficController` model would store records in an `air_traffic_controllers` table.

위의 예를 보면, 우리가 Eloquent에게 우리의 `Flight` 모델에 해당하는 데이터베이스 테이블의 이름을 알려주지 않았다는 것을 눈치채셨을 것입니다. 규칙에 따라 다른 이름이 명시적으로 지정되지 않는 한 클래스의 복수 이름인 "snake case"가 테이블 이름으로 사용됩니다. 따라서 이 경우 Eloquent는 `Flight` 모델이 `flights` 테이블에 레코드를 저장하는 반면 `AirTrafficController` 모델은 `air_traffic_controllers` 테이블에 레코드를 저장한다고 가정합니다.

If your model's corresponding database table does not fit this convention, you may manually specify the model's table name by defining a `table` property on the model:

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
### Primary Keys
### 기본 키

Eloquent will also assume that each model's corresponding database table has a primary key column named `id`. If necessary, you may define a protected `$primaryKey` property on your model to specify a different column that serves as your model's primary key:

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

In addition, Eloquent assumes that the primary key is an incrementing integer value, which means that Eloquent will automatically cast the primary key to an integer. If you wish to use a non-incrementing or a non-numeric primary key you must define a public `$incrementing` property on your model that is set to `false`:

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

If your model's primary key is not an integer, you should define a protected `$keyType` property on your model. This property should have a value of `string`:

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
#### "Composite" Primary Keys
#### "Composite" 기본 키

Eloquent requires each model to have at least one uniquely identifying "ID" that can serve as its primary key. "Composite" primary keys are not supported by Eloquent models. However, you are free to add additional multi-column, unique indexes to your database tables in addition to the table's uniquely identifying primary key.

Eloquent는 각 모델에 기본키 역할을 할 수 있는 고유하게 식별되는 "ID"가 하나 이상 있어야 합니다. "결합(Composite)" 기본키는 Eloquent 모델에서 지원되지 않습니다. 그러나 테이블을 고유하게 식별하는 기본키 외에 여러 열의 고유 인덱스를 데이터베이스 테이블에 자유롭게 추가할 수 있습니다.

<a name="timestamps"></a>
### Timestamps
### 타임스탬프

By default, Eloquent expects `created_at` and `updated_at` columns to exist on your model's corresponding database table.  Eloquent will automatically set these column's values when models are created or updated. If you do not want these columns to be automatically managed by Eloquent, you should define a `$timestamps` property on your model with a value of `false`:

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

If you need to customize the format of your model's timestamps, set the `$dateFormat` property on your model. This property determines how date attributes are stored in the database as well as their format when the model is serialized to an array or JSON:

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

If you need to customize the names of the columns used to store the timestamps, you may define `CREATED_AT` and `UPDATED_AT` constants on your model:

타임스탬프를 저장하는 데 사용되는 컬럼의 이름을 커스텀 해야 하는 경우 모델에 `CREATED_AT` 및 `UPDATED_AT` 상수를 정의할 수 있습니다.

    <?php

    class Flight extends Model
    {
        const CREATED_AT = 'creation_date';
        const UPDATED_AT = 'updated_date';
    }

<a name="database-connections"></a>
### Database Connections
### 데이터베이스 연결

By default, all Eloquent models will use the default database connection that is configured for your application. If you would like to specify a different connection that should be used when interacting with a particular model, you should define a `$connection` property on the model:

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
### Default Attribute Values
### 기본 속성 값

By default, a newly instantiated model instance will not contain any attribute values. If you would like to define the default values for some of your model's attributes, you may define an `$attributes` property on your model:

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
## Retrieving Models
## 모델 조회하기

Once you have created a model and [its associated database table](/docs/{{version}}/migrations#writing-migrations), you are ready to start retrieving data from your database. You can think of each Eloquent model as a powerful [query builder](/docs/{{version}}/queries) allowing you to fluently query the database table associated with the model. The model's `all` method will retrieve all of the records from the model's associated database table:

모델과 [관련 데이터베이스 테이블](/docs/{{version}}/migrations#writing-migrations)을 생성했으면 데이터베이스에서 데이터 검색을 시작할 준비가 된 것입니다. 각 Eloquent 모델을 강력한 [쿼리 빌더](/docs/{{version}}/queries)로 생각하면 모델과 관련된 데이터베이스 테이블을 강력하게 쿼리할 수 있습니다. 모델의 `all` 메소드는 모델의 연결된 데이터베이스 테이블에서 모든 레코드를 검색합니다.

    use App\Models\Flight;

    foreach (Flight::all() as $flight) {
        echo $flight->name;
    }

<a name="building-queries"></a>
#### Building Queries
#### 쿼리 작성

The Eloquent `all` method will return all of the results in the model's table. However, since each Eloquent model serves as a [query builder](/docs/{{version}}/queries), you may add additional constraints to queries and then invoke the `get` method to retrieve the results:

Eloquent의 `all` 메소드는 모델의 테이블에 있는 모든 결과를 반환합니다. 그러나 각 Eloquent 모델은 [쿼리 빌더](/docs/{{version}}/queries) 역할을 하기 때문에 쿼리에 추가 제약 조건을 추가한 다음 `get` 메소드를 호출하여 결과를 검색할 수 있습니다.

    $flights = Flight::where('active', 1)
                   ->orderBy('name')
                   ->take(10)
                   ->get();

> {tip} Since Eloquent models are query builders, you should review all of the methods provided by Laravel's [query builder](/docs/{{version}}/queries). You may use any of these methods when writing your Eloquent queries.

> {tip} Eloquent 모델은 쿼리 빌더이므로 라라벨의 [쿼리 빌더](/docs/{{version}}/queries)에서 제공하는 모든 메소드를 검토해야 합니다. Eloquent 쿼리를 작성할 때 이러한 방법 중 하나를 사용할 수 있습니다.

<a name="refreshing-models"></a>
#### Refreshing Models
#### 모델의 리프레쉬

If you already have an instance of an Eloquent model that was retrieved from the database, you can "refresh" the model using the `fresh` and `refresh` methods. The `fresh` method will re-retrieve the model from the database. The existing model instance will not be affected:

데이터베이스에서 가져온 Eloquent 모델의 인스턴스가 이미 있는 경우 `fresh` 및 `refresh` 메소드를 사용하여 모델을 "리프레쉬"할 수 있습니다. `fresh` 메소드는 데이터베이스에서 모델을 다시 검색합니다. 기존 모델 인스턴스는 영향을 받지 않습니다.

    $flight = Flight::where('number', 'FR 900')->first();

    $freshFlight = $flight->fresh();

The `refresh` method will re-hydrate the existing model using fresh data from the database. In addition, all of its loaded relationships will be refreshed as well:

`refresh` 메소드는 데이터베이스의 최신 데이터를 사용하여 기존 모델을 다시 갱신합니다. 또한 로드된 모든 관계도 새로 초기화 됩니다.

    $flight = Flight::where('number', 'FR 900')->first();

    $flight->number = 'FR 456';

    $flight->refresh();

    $flight->number; // "FR 900"

<a name="collections"></a>
### Collections
### 컬렉션

As we have seen, Eloquent methods like `all` and `get` retrieve multiple records from the database. However, these methods don't return a plain PHP array. Instead, an instance of `Illuminate\Database\Eloquent\Collection` is returned.

우리가 보았듯이 `all` 및 `get`과 같은 Eloquent 메소드는 데이터베이스에서 여러 레코드를 검색합니다. 그러나 이러한 메소드는 일반 PHP 배열을 반환하지 않습니다. 대신 `Illuminate\Database\Eloquent\Collection`의 인스턴스가 반환됩니다.

The Eloquent `Collection` class extends Laravel's base `Illuminate\Support\Collection` class, which provides a [variety of helpful methods](/docs/{{version}}/collections#available-methods) for interacting with data collections. For example, the `reject` method may be used to remove models from a collection based on the results of an invoked closure:

Eloquent `Collection` 클래스는 데이터 컬렉션과 상호작용하기 위해 [다양하고 유용한 메소드](/docs/{{version}}/collections#available-methods)를 제공하는 라라벨의 기본 `Illuminate\Support\Collection` 클래스를 확장합니다. 예를 들어, `reject` 메소드는 호출된 클로저의 결과를 기반으로 컬렉션에서 모델을 제거하는 데 사용할 수 있습니다.

```php
$flights = Flight::where('destination', 'Paris')->get();

$flights = $flights->reject(function ($flight) {
    return $flight->cancelled;
});
```

In addition to the methods provided by Laravel's base collection class, the Eloquent collection class provides [a few extra methods](/docs/{{version}}/eloquent-collections#available-methods) that are specifically intended for interacting with collections of Eloquent models.

라라벨의 기본 컬렉션 클래스에서 제공하는 메소드 외에도 Eloquent 컬렉션 클래스는 Eloquent 모델 컬렉션과 상호 작용하기 위해 특별히 고안된 [몇 가지 추가 메소드](/docs/{{version}}/eloquent-collections#available-methods)를 제공합니다.

Since all of Laravel's collections implement PHP's iterable interfaces, you may loop over collections as if they were an array:

라라벨의 모든 컬렉션은 PHP의 반복 가능한 인터페이스를 구현하므로 컬렉션을 배열인 것처럼 반복할 수 있습니다.

```php
foreach ($flights as $flight) {
    echo $flight->name;
}
```

<a name="chunking-results"></a>
### Chunking Results
### 결과 분할하기

Your application may run out of memory if you attempt to load tens of thousands of Eloquent records via the `all` or `get` methods. Instead of using these methods, the `chunk` method may be used to process large numbers of models more efficiently.

`all` 또는 `get` 메소드를 통해 수만 개의 Eloquent 레코드를 로드하려고 하면 애플리케이션의 메모리가 부족할 수 있습니다. 이러한 방법을 사용하는 대신 `chunk` 메소드를 사용하여 많은 수의 모델을 보다 효율적으로 처리할 수 있습니다.

The `chunk` method will retrieve a subset of Eloquent models, passing them to a closure for processing. Since only the current chunk of Eloquent models is retrieved at a time, the `chunk` method will provide significantly reduced memory usage when working with a large number of models:

`chunk` 메소드는 Eloquent 모델의 하위 집합을 검색하여 처리하기 위해 클로저에 전달합니다. Eloquent 모델의 현재 청크(chunk)만 한 번에 검색되기 때문에 `chunk` 메소드는 많은 수의 모델로 작업할 때 메모리 사용량을 크게 줄입니다.

```php
use App\Models\Flight;

Flight::chunk(200, function ($flights) {
    foreach ($flights as $flight) {
        //
    }
});
```

The first argument passed to the `chunk` method is the number of records you wish to receive per "chunk". The closure passed as the second argument will be invoked for each chunk that is retrieved from the database. A database query will be executed to retrieve each chunk of records passed to the closure.

`chunk` 메소드에 전달된 첫 번째 인수는 "청크" 당 수신하려는 레코드 수 입니다. 두 번째 인수로 전달된 클로저는 데이터베이스에서 검색된 각 청크에 대해 호출됩니다. 데이터베이스 쿼리가 실행되어 클로저에 전달된 각 레코드 청크를 검색합니다.

If you are filtering the results of the `chunk` method based on a column that you will also be updating while iterating over the results, you should use the `chunkById` method. Using the `chunk` method in these scenarios could lead to unexpected and inconsistent results. Internally, the `chunkById` method will always retrieve models with an `id` column greater than the last model in the previous chunk:

결과를 반복하는 동안 업데이트할 컬럼을 기반으로 `chunk` 메소드의 결과를 필터링하는 경우 `chunkById` 메소드를 사용해야 합니다. 이러한 시나리오에서 `chunk` 메소드를 사용하면 예상치 못한 일관성 없는 결과가 발생할 수 있습니다. 내부적으로 `chunkById` 메소드는 항상 이전 청크의 마지막 모델보다 큰 `id` 컬럼을 가진 모델을 검색합니다.

```php
Flight::where('departed', true)
    ->chunkById(200, function ($flights) {
        $flights->each->update(['departed' => false]);
    }, $column = 'id');
```

<a name="streaming-results-lazily"></a>
### Streaming Results Lazily
### 스트리밍 결과 지연

The `lazy` method works similarly to [the `chunk` method](#chunking-results) in the sense that, behind the scenes, it executes the query in chunks. However, instead of passing each chunk directly into a callback as is, the `lazy` method returns a flattened [`LazyCollection`](/docs/{{version}}/collections#lazy-collections) of Eloquent models, which lets you interact with the results as a single stream:

`lazy` 메소드는 배후에서 청크로 쿼리를 실행한다는 점에서 [`chunk` 메소드](#chunking-results)와 유사하게 작동합니다. 그러나 각 청크를 그대로 콜백에 전달하는 대신, `lazy` 메소드는 Eloquent 모델의 평평한 [`LazyCollection`](/docs/{{version}}/collections#lazy-collections)을 반환하고 결과와 단일 스트림으로 상호 작용할 수 있습니다.

```php
use App\Models\Flight;

foreach (Flight::lazy() as $flight) {
    //
}
```

If you are filtering the results of the `lazy` method based on a column that you will also be updating while iterating over the results, you should use the `lazyById` method. Internally, the `lazyById` method will always retrieve models with an `id` column greater than the last model in the previous chunk:

결과를 반복하는 동안 업데이트할 칼럼을 기반으로 `lazy` 메소드의 결과를 필터링하는 경우 `lazyById` 메소드를 사용해야 합니다. 내부적으로 `lazyById` 메소드는 항상 이전 청크의 마지막 모델보다 큰 `id` 컬럼을 가진 모델을 검색합니다.

```php
Flight::where('departed', true)
    ->lazyById(200, $column = 'id')
    ->each->update(['departed' => false]);
```

You may filter the results based on the descending order of the `id` using the `lazyByIdDesc` method.

`lazyByIdDesc` 메소드를 사용하여 `id`의 내림차순으로 결과를 필터링할 수 있습니다.

<a name="cursors"></a>
### Cursors
### 커서

Similar to the `lazy` method, the `cursor` method may be used to significantly reduce your application's memory consumption when iterating through tens of thousands of Eloquent model records.

`lazy` 메소드와 유사하게 `cursor` 메소드는 수만 개의 Eloquent 모델 레코드를 반복할 때 애플리케이션의 메모리 소비를 크게 줄이는 데 사용할 수 있습니다.

The `cursor` method will only execute a single database query; however, the individual Eloquent models will not be hydrated until they are actually iterated over. Therefore, only one Eloquent model is kept in memory at any given time while iterating over the cursor.

`cursor` 메소드는 단일 데이터베이스 쿼리만 실행합니다. 그러나 개별 Eloquent 모델은 실제로 반복될 때까지 수화되지 않습니다. 따라서 커서를 반복하는 동안 주어진 시간에 하나의 Eloquent 모델만 메모리에 유지됩니다.

> {note} Since the `cursor` method only ever holds a single Eloquent model in memory at a time, it cannot eager load relationships. If you need to eager load relationships, consider using [the `lazy` method](#streaming-results-lazily) instead.

> {note} `cursor` 메소드는 한 번에 하나의 Eloquent 모델만 메모리에 보유하므로 관계-relationships를 바로 로드할 수 없습니다. 관계를 즉시 로드해야 하는 경우 [`lazy` 메소드](#streaming-results-lazily)를 대신 사용하는 것이 좋습니다.

Internally, the `cursor` method uses PHP [generators](https://www.php.net/manual/en/language.generators.overview.php) to implement this functionality:

내부적으로 `cursor` 메소드는 PHP [generators](https://www.php.net/manual/en/language.generators.overview.php)를 사용하여 이 기능을 구현합니다.

```php
use App\Models\Flight;

foreach (Flight::where('destination', 'Zurich')->cursor() as $flight) {
    //
}
```

The `cursor` returns an `Illuminate\Support\LazyCollection` instance. [Lazy collections](/docs/{{version}}/collections#lazy-collections) allow you to use many of the collection methods available on typical Laravel collections while only loading a single model into memory at a time:

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

Although the `cursor` method uses far less memory than a regular query (by only holding a single Eloquent model in memory at a time), it will still eventually run out of memory. This is [due to PHP's PDO driver internally caching all raw query results in its buffer](https://www.php.net/manual/en/mysqlinfo.concepts.buffering.php). If you're dealing with a very large number of Eloquent records, consider using [the `lazy` method](#streaming-results-lazily) instead.

비록 `cursor` 메소드가 일반 쿼리보다 훨씬 적은 메모리를 사용하지만(한 번에 하나의 Eloquent 모델만 메모리에 유지함으로써), 결국에는 여전히 메모리가 부족합니다. 이는 [PHP의 PDO 드라이버가 내부적으로 모든 원시 쿼리 결과를 버퍼에 캐싱하기 때문입니다](https://www.php.net/manual/en/mysqlinfo.concepts.buffering.php). 대신 매우 많은 수의 Eloquent 레코드를 처리하는 경우 [`lazy` 메소드](#streaming-results-lazily) 사용을 고려하십시오.

<a name="advanced-subqueries"></a>
### Advanced Subqueries
### 고급 서브쿼리

<a name="subquery-selects"></a>
#### Subquery Selects
#### 서브쿼리 선택

Eloquent also offers advanced subquery support, which allows you to pull information from related tables in a single query. For example, let's imagine that we have a table of flight `destinations` and a table of `flights` to destinations. The `flights` table contains an `arrived_at` column which indicates when the flight arrived at the destination.

Eloquent 는 고급 서브쿼리 기능을 지원하므로 단일 쿼리에서 관련 테이블에 있는 정보를 가져올 수 있습니다. 예를 들어, 우리는 목적지까지 가기 위해 `destinations` 테이블과 `flights` 테이블이 있다고 가정 해봅시다. `flights` 테이블에는 항공편이 목적지에 도착한 시점을 나타내는 `arrived_at` 컬럼이 있습니다.

Using the subquery functionality available to the query builder's `select` and `addSelect` methods, we can select all of the `destinations` and the name of the flight that most recently arrived at that destination using a single query:

쿼리 빌더의 `select` 및 `addSelect` 메소드에 사용할 수 있는 서브 쿼리 기능을 사용하면 단일 쿼리를 사용하여 모든 `destinations`와 해당 목적지에 가장 최근에 도착한 항공편 이름을 선택할 수 있습니다.

    use App\Models\Destination;
    use App\Models\Flight;

    return Destination::addSelect(['last_flight' => Flight::select('name')
        ->whereColumn('destination_id', 'destinations.id')
        ->orderByDesc('arrived_at')
        ->limit(1)
    ])->get();

<a name="subquery-ordering"></a>
#### Subquery Ordering
#### 서브쿼리 정렬

In addition, the query builder's `orderBy` function supports subqueries. Continuing to use our flight example, we may use this functionality to sort all destinations based on when the last flight arrived at that destination. Again, this may be done while executing a single database query:

또한 쿼리 빌더의 `orderBy` 기능은 서브 쿼리를 지원합니다. 비행 예제를 계속 사용하면 이 기능을 사용하여 마지막 항공편이 해당 목적지에 도착한 시간을 기준으로 모든 목적지를 정렬할 수 있습니다. 다시 말하지만, 이것은 단일 데이터베이스 쿼리를 실행하는 동안 수행될 수 있습니다.

    return Destination::orderByDesc(
        Flight::select('arrived_at')
            ->whereColumn('destination_id', 'destinations.id')
            ->orderByDesc('arrived_at')
            ->limit(1)
    )->get();

<a name="retrieving-single-models"></a>
## Retrieving Single Models / Aggregates
## 하나의 모델 / 집계 조회하기

In addition to retrieving all of the records matching a given query, you may also retrieve single records using the `find`, `first`, or `firstWhere` methods. Instead of returning a collection of models, these methods return a single model instance:

주어진 쿼리와 일치하는 모든 레코드를 검색하는 것 외에도 `find`, `first` 또는 `firstWhere` 메소드를 사용하여 단일 레코드를 검색할 수도 있습니다. 모델 컬렉션을 반환하는 대신 다음 메소드는 단일 모델 인스턴스를 반환합니다.

    use App\Models\Flight;

    // Retrieve a model by its primary key...
    $flight = Flight::find(1);

    // Retrieve the first model matching the query constraints...
    $flight = Flight::where('active', 1)->first();

    // Alternative to retrieving the first model matching the query constraints...
    $flight = Flight::firstWhere('active', 1);

Sometimes you may wish to retrieve the first result of a query or perform some other action if no results are found. The `firstOr` method will return the first result matching the query or, if no results are found, execute the given closure. The value returned by the closure will be considered the result of the `firstOr` method:

때로는 쿼리의 첫 번째 결과를 검색하거나 결과가 없는 경우 다른 작업을 수행하고자 할 수 있습니다. `firstOr` 메소드는 쿼리와 일치하는 첫 번째 결과를 반환하거나 결과가 없으면 지정된 클로저를 실행합니다. 클로저에서 반환된 값은 `firstOr` 메소드의 결과로 간주됩니다.

    $model = Flight::where('legs', '>', 3)->firstOr(function () {
        // ...
    });

<a name="not-found-exceptions"></a>
#### Not Found Exceptions
#### Not Found Exceptions

Sometimes you may wish to throw an exception if a model is not found. This is particularly useful in routes or controllers. The `findOrFail` and `firstOrFail` methods will retrieve the first result of the query; however, if no result is found, an `Illuminate\Database\Eloquent\ModelNotFoundException` will be thrown:

때때로 모델을 찾을 수 없는 경우 예외를 throw하고 싶을 수 있습니다. 이것은 라우트나 컨트롤러에서 특히 유용합니다. `findOrFail` 및 `firstOrFail` 메소드는 쿼리의 첫 번째 결과를 검색합니다. 그러나 결과가 없으면 `Illuminate\Database\Eloquent\ModelNotFoundException`이 발생합니다:

    $flight = Flight::findOrFail(1);

    $flight = Flight::where('legs', '>', 3)->firstOrFail();

If the `ModelNotFoundException` is not caught, a 404 HTTP response is automatically sent back to the client:

`ModelNotFoundException`이 포착되지 않으면 404 HTTP 응답이 자동으로 클라이언트로 다시 전송됩니다.

    use App\Models\Flight;

    Route::get('/api/flights/{id}', function ($id) {
        return Flight::findOrFail($id);
    });

<a name="retrieving-or-creating-models"></a>
### Retrieving Or Creating Models
### 모델 검색 또는 생성

The `firstOrCreate` method will attempt to locate a database record using the given column / value pairs. If the model can not be found in the database, a record will be inserted with the attributes resulting from merging the first array argument with the optional second array argument:

`firstOrCreate` 메서드는 주어진 컬럼/값 쌍을 사용하여 데이터베이스 레코드를 찾습니다. 데이터베이스에서 모델을 찾을 수 없는 경우 첫 번째 배열 인수와 선택적 두 번째 배열 인수를 병합하여 얻은 특성을 가진 레코드가 삽입됩니다.

The `firstOrNew` method, like `firstOrCreate`, will attempt to locate a record in the database matching the given attributes. However, if a model is not found, a new model instance will be returned. Note that the model returned by `firstOrNew` has not yet been persisted to the database. You will need to manually call the `save` method to persist it:

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
### Retrieving Aggregates
### 합계 조회하기

When interacting with Eloquent models, you may also use the `count`, `sum`, `max`, and other [aggregate methods](/docs/{{version}}/queries#aggregates) provided by the Laravel [query builder](/docs/{{version}}/queries). As you might expect, these methods return a scalar value instead of an Eloquent model instance:

Eloquent 모델과 상호 작용할 때 라라벨 [쿼리 빌더](/docs/{{version}}/queries#aggregates)에서 제공하는 `count`, `sum`, `max` 및 기타 집계 메소드를 사용할 수도 있습니다. 예상대로 이 메서드는 Eloquent 모델 인스턴스 대신 스칼라 값을 반환합니다.

    $count = Flight::where('active', 1)->count();

    $max = Flight::where('active', 1)->max('price');

<a name="inserting-and-updating-models"></a>
## Inserting & Updating Models
## 모델을 통한 추가 및 수정하기

<a name="inserts"></a>
### Inserts
### Inserts

Of course, when using Eloquent, we don't only need to retrieve models from the database. We also need to insert new records. Thankfully, Eloquent makes it simple. To insert a new record into the database, you should instantiate a new model instance and set attributes on the model. Then, call the `save` method on the model instance:

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

In this example, we assign the `name` field from the incoming HTTP request to the `name` attribute of the `App\Models\Flight` model instance. When we call the `save` method, a record will be inserted into the database. The model's `created_at` and `updated_at` timestamps will automatically be set when the `save` method is called, so there is no need to set them manually.

위의 예제에서는 들어오는 HTTP 요청의 `name` 필드를 `App\Models\Flight` 모델 인스턴스의 `name` 속성에 할당합니다. `save` 메소드를 호출하면 레코드가 데이터베이스에 삽입됩니다. 모델의 `created_at` 및 `updated_at` 타임스탬프는 `save` 메소드가 호출될 때 자동으로 설정되므로 수동으로 설정할 필요가 없습니다.

Alternatively, you may use the `create` method to "save" a new model using a single PHP statement. The inserted model instance will be returned to you by the `create` method:

또는 `create` 메소드를 사용하여 단일 PHP 문을 사용하여 새 모델을 "저장"할 수 있습니다. 삽입된 모델 인스턴스는 `create` 메소드에 의해 반환됩니다.

    use App\Models\Flight;

    $flight = Flight::create([
        'name' => 'London to Paris',
    ]);

However, before using the `create` method, you will need to specify either a `fillable` or `guarded` property on your model class. These properties are required because all Eloquent models are protected against mass assignment vulnerabilities by default. To learn more about mass assignment, please consult the [mass assignment documentation](#mass-assignment).

그러나 `create` 메소드를 사용하기 전에 모델 클래스에 `fillable` 또는 `guarded` 속성을 지정해야 합니다. 모든 Eloquent 모델은 기본적으로 대량 할당 취약성으로부터 보호되기 때문에 이러한 속성이 필요합니다. 대량 할당에 대한 자세한 내용은 [대량 할당 문서](#mass-assignment)를 참조하세요.

<a name="updates"></a>
### Updates
### Updates

The `save` method may also be used to update models that already exist in the database. To update a model, you should retrieve it and set any attributes you wish to update. Then, you should call the model's `save` method. Again, the `updated_at` timestamp will automatically be updated, so there is no need to manually set its value:

`save` 메소드는 데이터베이스에 이미 존재하는 모델을 업데이트하는 데에도 사용할 수 있습니다. 모델을 업데이트하려면 모델을 검색하고 업데이트하려는 속성을 설정해야 합니다. 그런 다음 모델의 `save` 메소드를 호출해야 합니다. 이 때에도 `updated_at` 타임스탬프는 자동으로 업데이트 되어 설정되며 수동으로 값을 지정할 필요가 없습니다.

    use App\Models\Flight;

    $flight = Flight::find(1);

    $flight->name = 'Paris to London';

    $flight->save();

<a name="mass-updates"></a>
#### Mass Updates
#### 여러개의 모델 Update

Updates can also be performed against models that match a given query. In this example, all flights that are `active` and have a `destination` of `San Diego` will be marked as delayed:

주어진 쿼리와 일치하는 모델에 대해 업데이트를 수행할 수도 있습니다. 이 예에서 `active` 상태이고 `destination` 컬럼의 값이 `San Diego`인 모든 항공편은 지연된 것으로 표시됩니다.

    Flight::where('active', 1)
          ->where('destination', 'San Diego')
          ->update(['delayed' => 1]);

The `update` method expects an array of column and value pairs representing the columns that should be updated. The `update` method returns the number of affected rows.

`update` 메소드는 업데이트되어야 하는 컬럼을 나타내는 컬럼과 값 쌍의 배열을 예상합니다. `update` 메소드는 영향을 받는 행의 수를 반환합니다.

> {note} When issuing a mass update via Eloquent, the `saving`, `saved`, `updating`, and `updated` model events will not be fired for the updated models. This is because the models are never actually retrieved when issuing a mass update.

> {note} Eloquent를 통해 대량 업데이트를 발행할 때, `saving`, `saved`, `updating`, `updated` 모델 이벤트는 업데이트된 모델에 대해 실행되지 않습니다. 이는 대량 업데이트를 발행할 때 모델이 실제로 검색되지 않기 때문입니다.

<a name="examining-attribute-changes"></a>
#### Examining Attribute Changes
#### 속성 변경 검토

Eloquent provides the `isDirty`, `isClean`, and `wasChanged` methods to examine the internal state of your model and determine how its attributes have changed from when the model was originally retrieved.

Eloquent는 `isDirty`, `isClean`, `wasChanged` 메소드를 제공하여 모델의 내부 상태를 검사하고 모델이 처음 검색되었을 때부터 속성이 어떻게 변경되었는지 확인합니다.

The `isDirty` method determines if any of the model's attributes have been changed since the model was retrieved. You may pass a specific attribute name to the `isDirty` method to determine if a particular attribute is dirty. The `isClean` will determine if an attribute has remained unchanged since the model was retrieved. This method also accepts an optional attribute argument:

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

The `wasChanged` method determines if any attributes were changed when the model was last saved within the current request cycle. If needed, you may pass an attribute name to see if a particular attribute was changed:

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

The `getOriginal` method returns an array containing the original attributes of the model regardless of any changes to the model since it was retrieved. If needed, you may pass a specific attribute name to get the original value of a particular attribute:

`getOriginal` 메소드는 검색된 이후의 모델 변경 사항에 관계없이 모델의 원래 속성을 포함하는 배열을 반환합니다. 필요한 경우 특정 속성 이름을 전달하여 특정 속성의 원래 값을 얻을 수 있습니다.

    $user = User::find(1);

    $user->name; // John
    $user->email; // john@example.com

    $user->name = "Jack";
    $user->name; // Jack

    $user->getOriginal('name'); // John
    $user->getOriginal(); // Array of original attributes...

<a name="mass-assignment"></a>
### Mass Assignment
### 대량 할당 - Mass Assignment

You may use the `create` method to "save" a new model using a single PHP statement. The inserted model instance will be returned to you by the method:

단일 PHP 문을 사용하여 새 모델을 "저장"하기 위해 `create` 메소드를 사용할 수 있습니다. 삽입된 모델 인스턴스는 다음 방법으로 반환됩니다.

    use App\Models\Flight;

    $flight = Flight::create([
        'name' => 'London to Paris',
    ]);

However, before using the `create` method, you will need to specify either a `fillable` or `guarded` property on your model class. These properties are required because all Eloquent models are protected against mass assignment vulnerabilities by default.

그러나 `create` 메소드를 사용하기 전에 모델 클래스에 `fillable` 또는 `guarded` 속성을 지정해야 합니다. 모든 Eloquent 모델은 기본적으로 대량 할당 취약성으로부터 보호되기 때문에 이러한 속성이 필요합니다.

A mass assignment vulnerability occurs when a user passes an unexpected HTTP request field and that field changes a column in your database that you did not expect. For example, a malicious user might send an `is_admin` parameter through an HTTP request, which is then passed to your model's `create` method, allowing the user to escalate themselves to an administrator.

대량 할당 취약점은 사용자가 예기치 않은 HTTP 요청 필드를 전달하고 해당 필드가 데이터베이스에서 예상하지 못한 열을 변경할 때 발생합니다. 예를 들어, 악의적인 사용자는 HTTP 요청을 통해 `is_admin` 매개변수를 보낼 수 있으며, 이 매개변수는 모델의 `create` 메소드로 전달되어 사용자 자신을 관리자로 상승할 수 있습니다.

So, to get started, you should define which model attributes you want to make mass assignable. You may do this using the `$fillable` property on the model. For example, let's make the `name` attribute of our `Flight` model mass assignable:

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

Once you have specified which attributes are mass assignable, you may use the `create` method to insert a new record in the database. The `create` method returns the newly created model instance:

대량 할당 가능한 속성을 지정했으면 `create` 메소드를 사용하여 데이터베이스에 새 레코드를 삽입할 수 있습니다. `create` 메소드는 새로 생성된 모델 인스턴스를 반환합니다:

    $flight = Flight::create(['name' => 'London to Paris']);

If you already have a model instance, you may use the `fill` method to populate it with an array of attributes:

이미 모델 인스턴스를 가지고 있다면, `fill` 메소드에 배열을 전달하여 속성을 구성할 수 있습니다.

    $flight->fill(['name' => 'Amsterdam to Frankfurt']);

<a name="mass-assignment-json-columns"></a>
#### Mass Assignment & JSON Columns
#### 대량 할당 및 JSON 열 - Mass Assignment & JSON Columns

When assigning JSON columns, each column's mass assignable key must be specified in your model's `$fillable` array. For security, Laravel does not support updating nested JSON attributes when using the `guarded` property:

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
#### Allowing Mass Assignment
#### 대량 할당 허용

If you would like to make all of your attributes mass assignable, you may define your model's `$guarded` property as an empty array. If you choose to unguard your model, you should take special care to always hand-craft the arrays passed to Eloquent's `fill`, `create`, and `update` methods:

모든 속성을 대량 할당 가능하게 만들고 싶다면 모델의 `$guarded` 속성을 빈 배열로 정의할 수 있습니다. 모델의 보호를 해제하기로 선택했다면 항상 Eloquent의 `fill`, `create`, `update` 메소드에 전달되는 배열을 손수 제작하도록 특별한 주의를 기울여야 합니다.

    /**
     * The attributes that aren't mass assignable.
     *
     * @var array
     */
    protected $guarded = [];

<a name="upserts"></a>
### Upserts
### Upserts

Occasionally, you may need to update an existing model or create a new model if no matching model exists. Like the `firstOrCreate` method, the `updateOrCreate` method persists the model, so there's no need to manually call the `save` method.

경우에 따라 기존 모델을 업데이트하거나 일치하는 모델이 없는 경우 새 모델을 생성해야 할 수 있습니다. `firstOrCreate` 메소드와 마찬가지로 `updateOrCreate` 메소드는 모델을 유지하므로 `save` 메소드를 수동으로 호출할 필요가 없습니다.

In the example below, if a flight exists with a `departure` location of `Oakland` and a `destination` location of `San Diego`, its `price` and `discounted` columns will be updated. If no such flight exists, a new flight will be created which has the attributes resulting from merging the first argument array with the second argument array:

아래 예에서 `departure` 위치가 `Oakland`이고 `destination` 위치가 `San Diego`인 항공편이 있는 경우 해당 `price` 및 `discounted` 컬럼이 업데이트됩니다. 그러한 항공편이 존재하지 않으면 첫 번째 인수 배열을 두 번째 인수 배열과 병합한 결과 속성이 있는 새 항공편이 생성됩니다.

    $flight = Flight::updateOrCreate(
        ['departure' => 'Oakland', 'destination' => 'San Diego'],
        ['price' => 99, 'discounted' => 1]
    );

If you would like to perform multiple "upserts" in a single query, then you should use the `upsert` method instead. The method's first argument consists of the values to insert or update, while the second argument lists the column(s) that uniquely identify records within the associated table. The method's third and final argument is an array of the columns that should be updated if a matching record already exists in the database. The `upsert` method will automatically set the `created_at` and `updated_at` timestamps if timestamps are enabled on the model:

단일 쿼리에서 여러 "upsert"를 수행하려면 `upsert` 메소드를 사용해야 합니다. 메소드의 첫 번째 인수는 삽입하거나 업데이트할 값으로 구성되는 반면 두 번째 인수는 연결된 테이블 내에서 레코드를 고유하게 식별하는 컬럼을 나열합니다. 메소드의 세 번째이자 마지막 인수는 일치하는 레코드가 데이터베이스에 이미 있는 경우 업데이트해야 하는 컬럼의 배열입니다. `upsert` 메소드는 모델에서 타임스탬프가 활성화된 경우 `created_at` 및 `updated_at` 타임스탬프를 자동으로 설정합니다.

    Flight::upsert([
        ['departure' => 'Oakland', 'destination' => 'San Diego', 'price' => 99],
        ['departure' => 'Chicago', 'destination' => 'New York', 'price' => 150]
    ], ['departure', 'destination'], ['price']);

<a name="deleting-models"></a>
## Deleting Models
## 모델 삭제하기

To delete a model, you may call the `delete` method on the model instance:

모델을 삭제하려면 모델 인스턴스에서 `delete` 메소드를 호출하면 됩니다.

    use App\Models\Flight;

    $flight = Flight::find(1);

    $flight->delete();

You may call the `truncate` method to delete all of the model's associated database records. The `truncate` operation will also reset any auto-incrementing IDs on the model's associated table:

모델과 관련된 모든 데이터베이스 레코드를 삭제하려면 `truncate` 메소드를 호출할 수 있습니다. `truncate` 작업은 모델의 연결된 테이블에서 자동 증가 ID도 재설정합니다.

    Flight::truncate();

<a name="deleting-an-existing-model-by-its-primary-key"></a>
#### Deleting An Existing Model By Its Primary Key
#### 기본 키로 기존 모델 삭제

In the example above, we are retrieving the model from the database before calling the `delete` method. However, if you know the primary key of the model, you may delete the model without explicitly retrieving it by calling the `destroy` method.  In addition to accepting the single primary key, the `destroy` method will accept multiple primary keys, an array of primary keys, or a [collection](/docs/{{version}}/collections) of primary keys:

위의 예에서 우리는 `delete` 메소드를 호출하기 전에 데이터베이스에서 모델을 검색하고 있습니다. 그러나 모델의 기본 키를 알고 있는 경우 `destroy` 메소드를 호출하여 명시적으로 검색하지 않고 모델을 삭제할 수 있습니다. 단일 기본 키를 허용하는 것 외에도 `destroy` 메소드는 여러 기본 키, 기본 키 배열 또는 기본 키의 [컬렉션]](/docs/{{version}}/collections)을 허용합니다.

    Flight::destroy(1);

    Flight::destroy(1, 2, 3);

    Flight::destroy([1, 2, 3]);

    Flight::destroy(collect([1, 2, 3]));

> {note} The `destroy` method loads each model individually and calls the `delete` method so that the `deleting` and `deleted` events are properly dispatched for each model.

> {note} `destroy` 메소드는 각 모델을 개별적으로 로드하고 `delete` 메소드를 호출하여 `deleting` 및 `deleted` 이벤트가 각 모델에 대해 적절하게 전달되도록 합니다.

<a name="deleting-models-using-queries"></a>
#### Deleting Models Using Queries
#### 쿼리를 사용하여 모델 삭제

Of course, you may build an Eloquent query to delete all models matching your query's criteria. In this example, we will delete all flights that are marked as inactive. Like mass updates, mass deletes will not dispatch model events for the models that are deleted:

물론, 쿼리 기준과 일치하는 모든 모델을 삭제하기 위해 Eloquent 쿼리를 작성할 수 있습니다. 이 예에서는 비활성으로 표시된 모든 항공편을 삭제합니다. 대량 업데이트와 마찬가지로 대량 삭제는 삭제된 모델에 대한 모델 이벤트를 전달하지 않습니다.

    $deleted = Flight::where('active', 0)->delete();

> {note} When executing a mass delete statement via Eloquent, the `deleting` and `deleted` model events will not be dispatched for the deleted models. This is because the models are never actually retrieved when executing the delete statement.

> {note} Eloquent를 통해 대량 삭제 명령문을 실행할 때 `deleting` 및 `deleted` 모델 이벤트는 삭제된 모델에 대해 전달되지 않습니다. 이는 delete 문을 실행할 때 모델이 실제로 검색되지 않기 때문입니다.

<a name="soft-deleting"></a>
### Soft Deleting
### 소프트 삭제하기

In addition to actually removing records from your database, Eloquent can also "soft delete" models. When models are soft deleted, they are not actually removed from your database. Instead, a `deleted_at` attribute is set on the model indicating the date and time at which the model was "deleted". To enable soft deletes for a model, add the `Illuminate\Database\Eloquent\SoftDeletes` trait to the model:

실제로 데이터베이스에서 레코드를 제거하는 것 외에도 Eloquent는 모델을 소프트 삭제(일종의 임시 삭제)할 수도 있습니다. 모델이 일시 삭제되면 실제로 데이터베이스에서 제거되지 않습니다. 대신 모델이 "삭제"된 날짜와 시간을 나타내는 `deleted_at` 속성이 모델에 설정됩니다. 모델에 대한 일시 삭제를 활성화하려면 모델에 `Illuminate\Database\Eloquent\SoftDeletes` 특성을 추가하십시오:

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;
    use Illuminate\Database\Eloquent\SoftDeletes;

    class Flight extends Model
    {
        use SoftDeletes;
    }

> {tip} The `SoftDeletes` trait will automatically cast the `deleted_at` attribute to a `DateTime` / `Carbon` instance for you.

> {tip} `SoftDeletes` 의 특성은 자동으로 `deleted_at` 의 속성을 `DateTime` / `Carbon` 인스턴스에 반영됩니다.

You should also add the `deleted_at` column to your database table. The Laravel [schema builder](/docs/{{version}}/migrations) contains a helper method to create this column:

데이터베이스 테이블에 `deleted_at` 컬럼을 추가해야 합니다. 라라벨의 [스키마 빌더](/docs/{{version}}/migrations)는 이 컬럼을 생성하는 헬퍼 메소드를 가지고 있습니다.

    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Support\Facades\Schema;

    Schema::table('flights', function (Blueprint $table) {
        $table->softDeletes();
    });

    Schema::table('flights', function (Blueprint $table) {
        $table->dropSoftDeletes();
    });

Now, when you call the `delete` method on the model, the `deleted_at` column will be set to the current date and time. However, the model's database record will be left in the table. When querying a model that uses soft deletes, the soft deleted models will automatically be excluded from all query results.

이제 모델에서 `delete` 메소드를 호출하면 `deleted_at` 컬럼이 현재 날짜와 시간으로 설정됩니다. 그러나 모델의 데이터베이스 레코드는 테이블에 남습니다. 일시 삭제를 사용하는 모델을 쿼리할 때 일시 삭제된 모델은 모든 쿼리 결과에서 자동으로 제외됩니다.

To determine if a given model instance has been soft deleted, you may use the `trashed` method:

주어진 모델 인스턴스가 일시 삭제되었는지 확인하려면 `trashed` 메소드를 사용할 수 있습니다.

    if ($flight->trashed()) {
        //
    }

<a name="restoring-soft-deleted-models"></a>
#### Restoring Soft Deleted Models
#### 일시 삭제된 모델 복원

Sometimes you may wish to "un-delete" a soft deleted model. To restore a soft deleted model, you may call the `restore` method on a model instance. The `restore` method will set the model's `deleted_at` column to `null`:

경우에 따라 일시 삭제된 모델을 "삭제 취소"를 하고 싶을 수 있습니다. 일시 삭제된 모델을 복원하려면 모델 인스턴스에서 `restore` 메소드를 호출하면 됩니다. `restore` 메소드는 모델의 `deleted_at` 열을 `null`로 설정합니다.

    $flight->restore();

You may also use the `restore` method in a query to restore multiple models. Again, like other "mass" operations, this will not dispatch any model events for the models that are restored:

또한 쿼리에서 `restore` 메소드를 사용하여 여러 모델을 복원할 수 있습니다. 다시 말하지만, 다른 "대량" 작업과 마찬가지로 복원된 모델에 대한 모델 이벤트를 전달하지 않습니다.

    Flight::withTrashed()
            ->where('airline_id', 1)
            ->restore();

The `restore` method may also be used when building [relationship](/docs/{{version}}/eloquent-relationships) queries:

`restore` 메소드는 [관계(relationship)](/docs/{{version}}/eloquent-relationships) 쿼리를 작성할 때도 사용할 수 있습니다.

    $flight->history()->restore();

<a name="permanently-deleting-models"></a>
#### Permanently Deleting Models
#### 영구 모델 삭제

Sometimes you may need to truly remove a model from your database. You may use the `forceDelete` method to permanently remove a soft deleted model from the database table:

때로는 데이터베이스에서 모델을 완전히 제거해야 할 수도 있습니다. `forceDelete` 메소드를 사용하여 데이터베이스 테이블에서 일시 삭제된 모델을 영구적으로 제거할 수 있습니다.

    $flight->forceDelete();

You may also use the `forceDelete` method when building Eloquent relationship queries:

Eloquent 관계 쿼리를 작성할 때 `forceDelete` 메소드를 사용할 수도 있습니다.

    $flight->history()->forceDelete();

<a name="querying-soft-deleted-models"></a>
### Querying Soft Deleted Models
### 소프트 삭제된 모델 쿼리하기

<a name="including-soft-deleted-models"></a>
#### Including Soft Deleted Models
#### 소프트 삭제된 모델 포함하기

As noted above, soft deleted models will automatically be excluded from query results. However, you may force soft deleted models to be included in a query's results by calling the `withTrashed` method on the query:

위에서 언급했듯이 일시 삭제된 모델은 쿼리 결과에서 자동으로 제외됩니다. 그러나 쿼리에서 `withTrashed` 메소드를 호출하여 일시 삭제된 모델을 쿼리 결과에 강제로 포함할 수 있습니다.

    use App\Models\Flight;

    $flights = Flight::withTrashed()
                    ->where('account_id', 1)
                    ->get();

The `withTrashed` method may also be called when building a [relationship](/docs/{{version}}/eloquent-relationships) query:

`withTrashed` 메소드는 [관계(relationship)](/docs/{{version}}/eloquent-relationships) 쿼리를 빌드할 때도 호출될 수 있습니다.

    $flight->history()->withTrashed()->get();

<a name="retrieving-only-soft-deleted-models"></a>
#### Retrieving Only Soft Deleted Models
#### 소프트 삭제된 모델만 가져오기

The `onlyTrashed` method will retrieve **only** soft deleted models:

`onlyTrashed` 메소드는 **오직** 소프트 삭제 모델을 검색합니다.

    $flights = Flight::onlyTrashed()
                    ->where('airline_id', 1)
                    ->get();

<a name="pruning-models"></a>
## Pruning Models
## 가지치기 모델

Sometimes you may want to periodically delete models that are no longer needed. To accomplish this, you may add the `Illuminate\Database\Eloquent\Prunable` or `Illuminate\Database\Eloquent\MassPrunable` trait to the models you would like to periodically prune. After adding one of the traits to the model, implement a `prunable` method which returns an Eloquent query builder that resolves the models that are no longer needed:

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

When marking models as `Prunable`, you may also define a `pruning` method on the model. This method will be called before the model is deleted. This method can be useful for deleting any additional resources associated with the model, such as stored files, before the model is permanently removed from the database:

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

After configuring your prunable model, you should schedule the `model:prune` Artisan command in your application's `App\Console\Kernel` class. You are free to choose the appropriate interval at which this command should be run:

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

Behind the scenes, the `model:prune` command will automatically detect "Prunable" models within your application's `app/Models` directory. If your models are in a different location, you may use the `--model` option to specify the model class names:

내부적인 면에서 `model:prune` 명령은 애플리케이션의 `app/Models` 디렉토리에서 "Prunable" 모델을 자동으로 감지합니다. 모델이 다른 위치에 있는 경우 `--model` 옵션을 사용하여 모델 클래스 이름을 지정할 수 있습니다.

    $schedule->command('model:prune', [
        '--model' => [Address::class, Flight::class],
    ])->daily();

If you wish to exclude certain models from being pruned while pruning all other detected models, you may use the `--except` option:

감지된 모든 모델을 정리하는 동안 특정 모델을 정리에서 제외하려면 `--except` 옵션을 사용할 수 있습니다.

    $schedule->command('model:prune', [
        '--except' => [Address::class, Flight::class],
    ])->daily();

You may test your `prunable` query by executing the `model:prune` command with the `--pretend` option. When pretending, the `model:prune` command will simply report how many records would be pruned if the command were to actually run:

`--pretend` 옵션과 함께 `model:prune` 명령을 실행하여 `prunable` 쿼리를 테스트할 수 있습니다. pretend 옵션을 사용했을 때 `model:prune` 명령은 명령이 실제로 실행되는 경우 제거될 레코드 수를 단순히 보고합니다.

    php artisan model:prune --pretend

> {note} Soft deleting models will be permanently deleted (`forceDelete`) if they match the prunable query.

> {note} 소프트 삭제 모델은 정리 가능한 쿼리와 일치하는 경우 영구적으로 삭제됩니다(`forceDelete`).

<a name="mass-pruning"></a>
#### Mass Pruning
#### 대량으로 비우기

When models are marked with the `Illuminate\Database\Eloquent\MassPrunable` trait, models are deleted from the database using mass-deletion queries. Therefore, the `pruning` method will not be invoked, nor will the `deleting` and `deleted` model events be dispatched. This is because the models are never actually retrieved before deletion, thus making the pruning process much more efficient:

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
## Replicating Models
## 모델 복제

You may create an unsaved copy of an existing model instance using the `replicate` method. This method is particularly useful when you have model instances that share many of the same attributes:

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

To exclude one or more attributes from being replicated to the new model, you may pass an array to the `replicate` method:

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
## Query Scopes
## 쿼리 스코프

<a name="global-scopes"></a>
### Global Scopes
### 글로벌 스코프

Global scopes allow you to add constraints to all queries for a given model. Laravel's own [soft delete](#soft-deleting) functionality utilizes global scopes to only retrieve "non-deleted" models from the database. Writing your own global scopes can provide a convenient, easy way to make sure every query for a given model receives certain constraints.

글로벌 스코프를 사용하면 주어진 모델에 대한 모든 쿼리에 제약 조건을 추가할 수 있습니다. 라라벨의 자체 [소프트 삭제](#soft-deleting) 기능은 글로벌 스코프를 활용하여 데이터베이스에서 "삭제되지 않은" 모델만 검색합니다. 고유한 글로벌 스코프를 작성하면 주어진 모델에 대한 모든 쿼리가 특정 제약 조건을 받도록 하는 편리하고 쉬운 방법을 제공할 수 있습니다.

<a name="writing-global-scopes"></a>
#### Writing Global Scopes
#### 글로벌 스코프 작성하기

Writing a global scope is simple. First, define a class that implements the `Illuminate\Database\Eloquent\Scope` interface. Laravel does not have a conventional location that you should place scope classes, so you are free to place this class in any directory that you wish.

글로벌 스코프를 작성하는 것은 간단합니다. 먼저 `Illuminate\Database\Eloquent\Scope` 인터페이스를 구현하는 클래스를 정의합니다. 라라벨에는 스코프 클래스를 배치해야 하는 일반적인 위치가 없으므로 이 클래스를 원하는 디렉토리에 자유롭게 배치할 수 있습니다.

The `Scope` interface requires you to implement one method: `apply`. The `apply` method may add `where` constraints or other types of clauses to the query as needed:

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

> {tip} If your global scope is adding columns to the select clause of the query, you should use the `addSelect` method instead of `select`. This will prevent the unintentional replacement of the query's existing select clause.

> {tip} 글로벌 스코프가 쿼리의 select 절에 컬럼을 추가하는 경우 select 대신 `addSelect` 메소드를 사용해야 합니다. 이렇게 하면 쿼리의 기존 `select` 절이 교체되는 실수를 방지할 수 있습니다.

<a name="applying-global-scopes"></a>
#### Applying Global Scopes
#### 글로벌 스코프 적용하기

To assign a global scope to a model, you should override the model's `booted` method and invoke the model's `addGlobalScope` method. The `addGlobalScope` method accepts an instance of your scope as its only argument:

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

After adding the scope in the example above to the `App\Models\User` model, a call to the `User::all()` method will execute the following SQL query:

위 예제의 스코프를 `App\Models\User` 모델에 추가한 후 `User::all()` 메소드를 호출하면 다음 SQL 쿼리가 실행됩니다.

```sql
select * from `users` where `created_at` < 0021-02-18 00:00:00
```

<a name="anonymous-global-scopes"></a>
#### Anonymous Global Scopes
#### 익명의 글로벌 스코프

Eloquent also allows you to define global scopes using closures, which is particularly useful for simple scopes that do not warrant a separate class of their own. When defining a global scope using a closure, you should provide a scope name of your own choosing as the first argument to the `addGlobalScope` method:

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
#### Removing Global Scopes
#### 글로벌 스코프 삭제하기

If you would like to remove a global scope for a given query, you may use the `withoutGlobalScope` method. This method accepts the class name of the global scope as its only argument:

주어진 쿼리에 대한 글로벌 스코프를 제거하려면 `withoutGlobalScope` 메소드를 사용할 수 있습니다. 이 메소드는 글로벌 스코프의 클래스 이름을 유일한 인수로 받아들입니다.

    User::withoutGlobalScope(AncientScope::class)->get();

Or, if you defined the global scope using a closure, you should pass the string name that you assigned to the global scope:

또는 클로저를 사용하여 글로벌 스코프를 정의한 경우 글로벌 스코프에 할당한 문자열 이름을 전달해야 합니다.

    User::withoutGlobalScope('ancient')->get();

If you would like to remove several or even all of the query's global scopes, you may use the `withoutGlobalScopes` method:

쿼리의 글로벌 스코프 일부 또는 전체를 제거하려면 `withoutGlobalScopes` 메소드를 사용할 수 있습니다.

    // Remove all of the global scopes...
    User::withoutGlobalScopes()->get();

    // Remove some of the global scopes...
    User::withoutGlobalScopes([
        FirstScope::class, SecondScope::class
    ])->get();

<a name="local-scopes"></a>
### Local Scopes
### 로컬 스코프

Local scopes allow you to define common sets of query constraints that you may easily re-use throughout your application. For example, you may need to frequently retrieve all users that are considered "popular". To define a scope, prefix an Eloquent model method with `scope`.

로컬 스코프를 사용하면 애플리케이션 전체에서 쉽게 재사용할 수 있는 공통 쿼리 제약 조건 집합을 정의할 수 있습니다. 예를 들어, "인기 있는" 것으로 간주되는 모든 사용자를 자주 검색해야 할 수 있습니다. 스코프를 정의하려면 Eloquent 모델 메소드에 `scope`를 접두사로 붙입니다.

Scopes should always return the same query builder instance or `void`:

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
#### Utilizing A Local Scope
#### 로컬 스코프 활용하기

Once the scope has been defined, you may call the scope methods when querying the model. However, you should not include the `scope` prefix when calling the method. You can even chain calls to various scopes:

스코프가 정의되면 모델을 쿼리할 때 스코프 메소드를 호출할 수 있습니다. 그러나 메소드를 호출할 때 `scope` 접두사를 포함하면 안 됩니다. 다양한 스코프로 호출을 연결할 수도 있습니다.

    use App\Models\User;

    $users = User::popular()->active()->orderBy('created_at')->get();

Combining multiple Eloquent model scopes via an `or` query operator may require the use of closures to achieve the correct [logical grouping](/docs/{{version}}/queries#logical-grouping):

`or` 쿼리 연산자를 통해 여러 Eloquent 모델 스코프를 결합하려면 올바른 [논리적 그룹화](/docs/{{version}}/queries#logical-grouping)를 달성하기 위해 클로저를 사용해야 할 수 있습니다.

    $users = User::popular()->orWhere(function (Builder $query) {
        $query->active();
    })->get();

However, since this can be cumbersome, Laravel provides a "higher order" `orWhere` method that allows you to fluently chain scopes together without the use of closures:

그러나 이것이 번거로울 수 있으므로 라라벨은 클로저를 사용하지 않고도 스코프를 유창하게 연결할 수 있는 "고차" `orWhere` 메소드를 제공합니다.

    $users = App\Models\User::popular()->orWhere->active()->get();

<a name="dynamic-scopes"></a>
#### Dynamic Scopes
#### 다이나믹 스코프

Sometimes you may wish to define a scope that accepts parameters. To get started, just add your additional parameters to your scope method's signature. Scope parameters should be defined after the `$query` parameter:

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

Once the expected arguments have been added to your scope method's signature, you may pass the arguments when calling the scope:

스코프 메소드의 서명에 예상 인수가 추가되면 스코프를 호출할 때 인수를 전달할 수 있습니다.

    $users = User::ofType('admin')->get();

<a name="comparing-models"></a>
## Comparing Models
## 모델의 비교

Sometimes you may need to determine if two models are the "same" or not. The `is` and `isNot` methods may be used to quickly verify two models have the same primary key, table, and database connection or not:

때로는 두 모델이 "동일한"지 여부를 결정해야 할 수도 있습니다. `is` 및 `isNot` 메소드는 두 모델이 동일한 기본키, 테이블 및 데이터베이스 연결을 가지고 있는지 여부를 빠르게 확인하는 데 사용할 수 있습니다.

    if ($post->is($anotherPost)) {
        //
    }

    if ($post->isNot($anotherPost)) {
        //
    }

The `is` and `isNot` methods are also available when using the `belongsTo`, `hasOne`, `morphTo`, and `morphOne` [relationships](/docs/{{version}}/eloquent-relationships). This method is particularly helpful when you would like to compare a related model without issuing a query to retrieve that model:

`is` 및 `isNot` 메소드는 `belongsTo`, `hasOne`, `morphTo` 및 `morphOne` [관계-relationships](/docs/{{version}}/eloquent-relationships)을 사용할 때도 사용할 수 있습니다. 이 방법은 해당 모델을 검색하기 위해 쿼리를 실행하지 않고 관련 모델을 비교하려는 경우에 특히 유용합니다.

    if ($post->author()->is($user)) {
        //
    }

<a name="events"></a>
## Events
## 이벤트

> {tip} Want to broadcast your Eloquent events directly to your client-side application? Check out Laravel's [model event broadcasting](/docs/{{version}}/broadcasting#model-broadcasting).

> {tip} Eloquent 이벤트를 클라이언트 측 애플리케이션에 직접 브로드캐스트하고 싶으십니까? 라라벨의 [모델 이벤트 브로드캐스트](/docs/{{version}}/broadcasting#model-broadcasting)을 확인하세요.

Eloquent models dispatch several events, allowing you to hook into the following moments in a model's lifecycle: `retrieved`, `creating`, `created`, `updating`, `updated`, `saving`, `saved`, `deleting`, `deleted`, `restoring`, `restored`, and `replicating`.

Eloquent 모델은 여러 이벤트를 전달하여 모델 수명 주기의 `retrieved`, `creating`, `created`, `updating`, `updated`, `saving`, `saved`, `deleting`, `deleted`, `restoring`, `restored`, `replicating` 와 같은 순간에 연결할 수 있습니다.

The `retrieved` event will dispatch when an existing model is retrieved from the database. When a new model is saved for the first time, the `creating` and `created` events will dispatch. The `updating` / `updated` events will dispatch when an existing model is modified and the `save` method is called. The `saving` / `saved` events will dispatch when a model is created or updated - even if the model's attributes have not been changed. Event names ending with `-ing` are dispatched before any changes to the model are persisted, while events ending with `-ed` are dispatched after the changes to the model are persisted.

`retrieved` 이벤트는 데이터베이스에서 기존 모델을 검색할 때 전달됩니다. 새 모델이 처음 저장되면 'creating' 및 'created' 이벤트가 전달됩니다. `updating` `updated` 이벤트는 기존 모델이 수정되고 `save` 메소드가 호출될 때 전달됩니다. `saving` / `saved` 이벤트는 모델의 속성이 변경되지 않은 경우에도 모델이 생성되거나 업데이트될 때 전달됩니다. `-ing`으로 끝나는 이벤트 이름은 모델에 대한 변경 사항이 지속되기 전에 전달되는 반면, `-ed`로 끝나는 이벤트는 모델에 대한 변경 사항이 지속된 후에 전달됩니다.

To start listening to model events, define a `$dispatchesEvents` property on your Eloquent model. This property maps various points of the Eloquent model's lifecycle to your own [event classes](/docs/{{version}}/events). Each model event class should expect to receive an instance of the affected model via its constructor:

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

After defining and mapping your Eloquent events, you may use [event listeners](/docs/{{version}}/events#defining-listeners) to handle the events.

Eloquent 이벤트를 정의하고 매핑한 후 [이벤트 리스너](/docs/{{version}}/events#defining-listeners)를 사용하여 이벤트를 처리할 수 있습니다.

> {note} When issuing a mass update or delete query via Eloquent, the `saved`, `updated`, `deleting`, and `deleted` model events will not be dispatched for the affected models. This is because the models are never actually retrieved when performing mass updates or deletes.

> {note} Eloquent를 통해 대량 업데이트 또는 삭제 쿼리를 실행할 때 `saved`, `updated`, `deleting` 및 `deleted` 모델 이벤트는 영향을 받는 모델에 대해 전달되지 않습니다. 이는 대량 업데이트 또는 삭제를 수행할 때 모델이 실제로 검색되지 않기 때문입니다.

<a name="events-using-closures"></a>
### Using Closures
### 클로저 사용하기

Instead of using custom event classes, you may register closures that execute when various model events are dispatched. Typically, you should register these closures in the `booted` method of your model:

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

If needed, you may utilize [queueable anonymous event listeners](/docs/{{version}}/events#queuable-anonymous-event-listeners) when registering model events. This will instruct Laravel to execute the model event listener in the background using your application's [queue](/docs/{{version}}/queues):

필요한 경우 모델 이벤트를 등록할 때 [대기열에 올릴 수 있는 익명 이벤트 리스너](/docs/{{version}}/events#queuable-anonymous-event-listeners)를 활용할 수 있습니다. 이것은 애플리케이션의 [큐]](/docs/{{version}}/queues)를 사용하여 백그라운드에서 모델 이벤트 리스너를 실행하도록 라라벨에 지시합니다.

    use function Illuminate\Events\queueable;

    static::created(queueable(function ($user) {
        //
    }));

<a name="observers"></a>
### Observers
### 옵저버

<a name="defining-observers"></a>
#### Defining Observers
#### 옵저버 객체 정의하기

If you are listening for many events on a given model, you may use observers to group all of your listeners into a single class. Observer classes have method names which reflect the Eloquent events you wish to listen for. Each of these methods receives the affected model as their only argument. The `make:observer` Artisan command is the easiest way to create a new observer class:

주어진 모델에서 많은 이벤트를 수신하는 경우 옵저버를 사용하여 모든 리스너를 단일 클래스로 그룹화할 수 있습니다. 옵저버 클래스에는 듣고자 하는 Eloquent 이벤트를 반영하는 메소드 이름이 있습니다. 이러한 각 메소드는 영향을 받는 모델을 유일한 인수로 받습니다. `make:observer` Artisan 명령은 새로운 옵저버 클래스를 생성하는 가장 쉬운 방법입니다:

    php artisan make:observer UserObserver --model=User

This command will place the new observer in your `App/Observers` directory. If this directory does not exist, Artisan will create it for you. Your fresh observer will look like the following:

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

To register an observer, you need to call the `observe` method on the model you wish to observe. You may register observers in the `boot` method of your application's `App\Providers\EventServiceProvider` service provider:

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

> {tip} There are additional events an observer can listen to, such as `saving` and `retrieved`. These events are described within the [events](#events) documentation.

> {tip} 관찰자가 들을 수 있는 추가 이벤트(예: `saving` 및 `retrieved`)가 있습니다. 이러한 이벤트는 [events](#events) 문서에 설명되어 있습니다.

<a name="observers-and-database-transactions"></a>
#### Observers & Database Transactions
#### 옵저버 및 데이터베이스 트랜잭션

When models are being created within a database transaction, you may want to instruct an observer to only execute its event handlers after the database transaction is committed. You may accomplish this by defining an `$afterCommit` property on the observer. If a database transaction is not in progress, the event handlers will execute immediately:

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
### Muting Events
### 이벤트 끄기(Muting Events)

You may occasionally need to temporarily "mute" all events fired by a model. You may achieve this using the `withoutEvents` method. The `withoutEvents` method accepts a closure as its only argument. Any code executed within this closure will not dispatch model events, and any value returned by the closure will be returned by the `withoutEvents` method:

때때로 모델에서 발생한 모든 이벤트를 일시적으로 "mute"해야 할 수도 있습니다. `withoutEvents` 메소드를 사용하여 이를 달성할 수 있습니다. `withoutEvents` 메소드는 클로저를 유일한 인수로 받아들입니다. 이 클로저 내에서 실행되는 모든 코드는 모델 이벤트를 전달하지 않으며 클로저에서 반환된 모든 값은 `withoutEvents` 메소드에 의해 반환됩니다.

    use App\Models\User;

    $user = User::withoutEvents(function () use () {
        User::findOrFail(1)->delete();

        return User::find(2);
    });

<a name="saving-a-single-model-without-events"></a>
#### Saving A Single Model Without Events
#### 이벤트 없이 단일 모델 저장하기

Sometimes you may wish to "save" a given model without dispatching any events. You may accomplish this using the `saveQuietly` method:

때때로 이벤트를 전달하지 않고 주어진 모델을 "저장"하고 싶을 수 있습니다. `saveQuietly` 메소드를 사용하여 이 작업을 수행할 수 있습니다.

    $user = User::findOrFail(1);

    $user->name = 'Victoria Faith';

    $user->saveQuietly();
