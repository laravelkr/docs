# 라라벨 Scout-스카우트

- [시작하기](#introduction)
- [설치하기](#installation)
    - [드라이버 사전준비사항](#driver-prerequisites)
    - [큐 사용하기](#queueing)
- [설정하기](#configuration)
    - [모델 인덱스 설정하기](#configuring-model-indexes)
    - [검색 데이터 설정하기](#configuring-searchable-data)
    - [모델 ID 설정하기](#configuring-the-model-id)
    - [모델별로 검색 엔진 설정하기](#configuring-search-engines-per-model)
    - [사용자 식별](#identifying-users)
- [데이터베이스 / 컬렉션 엔진](#database-and-collection-engines)
    - [데이터베이스 엔진](#database-engine)
    - [컬렉션 엔진](#collection-engine)
- [인덱싱](#indexing)
    - [임포트 일괄처리](#batch-import)
    - [레코드 추가하기](#adding-records)
    - [레코드 업데이트 하기](#updating-records)
    - [레코드 삭제하기](#removing-records)
    - [인덱싱 일시 멈춤](#pausing-indexing)
    - [조건에 따른 검색 모델 인스턴스](#conditionally-searchable-model-instances)
- [검색하기](#searching)
    - [Where 클로저](#where-clauses)
    - [페이지네이션](#pagination)
    - [소프트 삭제](#soft-deleting)
    - [엔진의 검색 커스터마이징](#customizing-engine-searches)
- [커스텀 엔진](#custom-engines)
- [빌더 매크로](#builder-macros)

<a name="introduction"></a>
## 시작하기

[라라벨 스카우트](https://github.com/laravel/scout)는 [Eloquent 모델](/docs/{{version}}/eloquent)에 full-text 검색 기능을 장착할 수 있는 드라이버 기반의 단순한 솔루션을 제공합니다. 스카우트는 모델 옵저버를 사용하여 엘로퀀트 레코드와 동기화된 검색 인덱스를 자동으로 유지합니다.

현재 스카우트는  [Algolia](https://www.algolia.com/)와 [MeiliSearch](https://www.meilisearch.com), MySQL / PostgreSQL (`database`) 드라이버를 내장하고 있습니다. 여기에 더해 추가적인 의존성이 필요하지 않은 로컬 개발환경용 "컬렉션" 드라이버도 제공합니다. 그리고 간단하게 커스텀 드라이버를 작성할 수 있으므로, 자유롭게 스카우트를 확장하여 여러분 자신의 검색 기능을 구현할 수 있습니다. 

<a name="installation"></a>
## 설치하기

먼저, 컴포저 패키지 매니저를 사용하여 스카우트를 설치하십시오:

```shell
composer require laravel/scout
```

스카우트를 설치한 다음, `vendor:publish` 아티즌 명령어를 사용하여 스카우트 설정 파일을 퍼블리싱 해야합니다. 이 명령을 실행하면 애플리케이션의 `config` 디렉토리에 `scout.php` 설정파일이 생성됩니다.

```shell
php artisan vendor:publish --provider="Laravel\Scout\ScoutServiceProvider"
```

마지막으로, `Laravel\Scout\Searchable` trait를 검색 기능을 장착할 모델에 추가합니다. 이 trait는 모델과 검색 드라이버의 동기화를 자동으로 유지하기 위한 모델 옵저버를 등록할 것입니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;
    use Laravel\Scout\Searchable;

    class Post extends Model
    {
        use Searchable;
    }

<a name="driver-prerequisites"></a>
### 드라이버 사전 준비사항

<a name="algolia"></a>
#### Algolia

Algolia 드라이버를 사용할 때, Algolia 계정의 `id`와 `secret` 정보를 `config/scout.php` 설정 파일에 입력해야 합니다. 계정 정보를 설정한 다음에는, 컴포저를 통해 Algolia PHP SDK를 설치해야 합니다.

```shell
composer require algolia/algoliasearch-client-php
```

<a name="meilisearch"></a>
#### MeiliSearch

[MeiliSearch](https://www.meilisearch.com)는 엄청 빠른 속도를 자랑하는 오픈소스 검색엔진입니다. 로컬머신에서 MeiliSearch 를 설치하기 어렵다면 라라벨에서 공식적으로 지원하는 도커 개발환경인 [라라벨 Sail](/docs/{{version}}/sail#meilisearch)을 사용할 수 있습니다.

MeiliSearch 드라이버를 사용하려면 컴포저 패키지 매니저를 통해서 다음과 같이 MeiliSearch PHP SDK 를 설치해야 합니다. 

```shell
composer require meilisearch/meilisearch-php http-interop/http-factory-guzzle
```

그 다음에 애플리케이션의 `.env` 파일에 다음과 같이 드라이버를 지정하고 MeiliSearch `host`와 `key` 자격증명값을 설정해야합니다. 

```ini
SCOUT_DRIVER=meilisearch
MEILISEARCH_HOST=http://127.0.0.1:7700
MEILISEARCH_KEY=masterKey
```

보다 자세한 내용은 [MeiliSearch 공식문서](https://docs.meilisearch.com/learn/getting_started/quick_start.html)를 참고하십시오

추가로, [MeiliSearch 바이너리 호환성 문서](https://github.com/meilisearch/meilisearch-php#-compatibility-with-meilisearch)를 확인하여 MeiliSearch 바이너리 버전과 호환되는 `meilisearch/meilisearch-php` 버전을 설치했는지 확인하십시오. 

> **Warning**
> MeiliSearch를 사용하는 애플리케이션에서 스카우트를 업그레이드 할 때는 항상 MeiliSearch 서비스의 [추가적인 변경사항 리뷰](https://github.com/meilisearch/MeiliSearch/releases)를 확인하십시오.

<a name="queueing"></a>
### 큐 사용하기

스카우트를 사용하기 위한 필수 요구사항은 아니지만, 스카우트를 사용하기에 앞서 [큐 드라이버](/docs/{{version}}/queues)의 구성을 고려해 보아야 합니다. 큐를 사용하면 스카우트는 모델 정보와 검색 인덱스를 싱크하는 모든 작업을 큐에 등록할 것이고, 이는 여러분의 웹사이트가 더 나은 응답시간을 제공할 수 있도록 할 것입니다.

일단 큐 드라이버를 구성한 다음, `config/scout.php` 설정 파일의 `queue` 옵션을 `true`로 지정하십시오.

    'queue' => true,

`queue` 옵션이 `false`로 설정된 경우에도 Algolia 와 Meilisearch 같은 일부 Scout 드라이버는 레코드를 항상 비동기식으로 인덱싱 한다는 점을 기억하십시오. 즉, 라라벨 애플리케이션 안에서 인덱스 작업이 완료되었더라도 검색 엔진 자체가 새로운 레코드 및 업데이트된 레코드를 반영하는데는 시간이 필요할 수 있습니다.

스카우트 잡이 활용할 커넥션과 큐를 지정하려면 `queue` 설정 옵션을 배열로 정의하면 됩니다.

    'queue' => [
        'connection' => 'redis',
        'queue' => 'scout'
    ],

<a name="configuration"></a>
## 환경 설정

<a name="configuring-model-indexes"></a>
### 모델 인덱스 설정하기

각각의 Eloquent 모델은 해당 모델에 대한 모든 검색 레코드를 가지고 있는 검색 "인덱스"와 동기화 됩니다. 다시 말해, 여러분은 각각의 인덱스를 Mysql 테이블처럼 생각할 수 있습니다. 기본적으로, 각각의 모델은 모델의 "테이블" 명과 매칭되는 인덱스에 저장됩니다. 일반적으로, 인덱스 명은 모델 이름의 복수형입니다; 그렇지만 모델의 `searchableAs` 메서드를 오버라이드 해서 인덱스명을 자유롭게 변경할 수 있습니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;
    use Laravel\Scout\Searchable;

    class Post extends Model
    {
        use Searchable;

        /**
         * Get the name of the index associated with the model.
         *
         * @return string
         */
        public function searchableAs()
        {
            return 'posts_index';
        }
    }

<a name="configuring-searchable-data"></a>
### 검색 데이터 설정하기

기본적으로, 주어진 모델의 `toArray` 형식 그대로가 검색 인덱스로 유지됩니다. 만약 당신이 검색 인덱스와 동기화되는 데이터를 변경하고 싶다면, 모델의 `toSearchableArray` 메서드를 오버라이드 할 수 있습니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;
    use Laravel\Scout\Searchable;

    class Post extends Model
    {
        use Searchable;

        /**
         * Get the indexable data array for the model.
         *
         * @return array
         */
        public function toSearchableArray()
        {
            $array = $this->toArray();

            // Customize the data array...

            return $array;
        }
    }

MeiliSearch와 같은 일부 검색 엔진은 올바른 타입의 데이터에 대해서만 필터링 연산(`>`, `<`, 등)을 수행합니다. 그래서 이러한 검색 엔진을 사용하고 검색 데이터를 커스터마이징 할 때는, 숫자 값이 올바른 타입으로 변환되어 있는지 확인해야 합니다.

    public function toSearchableArray()
    {
        return [
            'id' => (int) $this->id,
            'name' => $this->name,
            'price' => (float) $this->price,
        ];
    }

<a name="configuring-filterable-data-for-meilisearch"></a>
#### 필터링 가능한 데이터 설정하기(MeiliSearch)

다른 스카우트 드라이버와 달리, MeiliSearch는 필터링 가능한 속성, 정렬 가능한 속성, 그리고 [다른 지원되는 설정 필드](https://docs.meilisearch.com/reference/api/settings.html)를 미리 정의해야 합니다.

필터링 가능한 속성은 스카우트의 `where` 메서드를 호출할 때 필터링할 속성이고, 정렬 가능한 속성은 스카우트의 `orderBy` 메서드를 호출할 때 정렬할 속성입니다. 인덱스 설정을 정의하려면, 애플리케이션의 `scout` 설정 파일에서 `meilisearch` 설정 항목의 `index-settings` 부분을 조정하세요.

```php
use App\Models\User;
use App\Models\Flight;

'meilisearch' => [
    'host' => env('MEILISEARCH_HOST', 'http://localhost:7700'),
    'key' => env('MEILISEARCH_KEY', null),
    'index-settings' => [
        User::class => [
            'filterableAttributes'=> ['id', 'name', 'email'],
            'sortableAttributes' => ['created_at'],
            // Other settings fields...
        ],
        Flight::class => [
            'filterableAttributes'=> ['id', 'destination'],
            'sortableAttributes' => ['updated_at'],
        ],
    ],
],
```

만약 주어진 인덱스의 모델이 소프트 삭제가 가능하고 `index-settings` 배열에 포함되어 있다면, 스카우트는 해당 인덱스에서 소프트 삭제된 모델에 대한 필터링을 자동으로 지원합니다. 소프트 삭제가 가능한 모델 인덱스에 필터링이나 정렬 가능한 속성을 정의할 필요가 없다면, 해당 모델을 위한 `index-settings` 배열에 빈 항목을 추가할 수 있습니다.

```php
'index-settings' => [
    Flight::class => []
],
```

애플리케이션의 인덱스 설정을 구성한 후, `scout:sync-index-settings` 아티즌 명령을 실행해야 합니다. 이 명령은 현재 구성된 인덱스 설정을 MeiliSearch에 알려줍니다. 편의를 위해, 이 명령을 배포 프로세스의 일부로 만들 수 있습니다.

```shell
php artisan scout:sync-index-settings
```

<a name="configuring-the-model-id"></a>
### 모델 ID 설정하기

기본적으로 Scout-스카우트는 모델의 primary 키를 검색 인덱스에 저장되는 고유한 ID/KEY 로 사용합니다. 이 동작을 커스터마이징 하고자 한다면, 모들의 `getScoutKey`와 `getScoutKeyName` 메서드를 오버라이드 하면 됩니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;
    use Laravel\Scout\Searchable;

    class User extends Model
    {
        use Searchable;

        /**
         * Get the value used to index the model.
         *
         * @return mixed
         */
        public function getScoutKey()
        {
            return $this->email;
        }

        /**
         * Get the key name used to index the model.
         *
         * @return mixed
         */
        public function getScoutKeyName()
        {
            return 'email';
        }
    }

<a name="configuring-search-engines-per-model"></a>
### 모델별 검색 엔진 설정

검색할 때 스카우트는 일반적으로 여러분 애플리케이션의 `scout` 설정 파일에 지정한 기본 검색 엔진을 사용합니다. 하지만 특정 모델을 위한 검색 엔진은 모델의 `searchableUsing` 메서드를 오버라이드해서 변경할 수 있습니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;
    use Laravel\Scout\EngineManager;
    use Laravel\Scout\Searchable;

    class User extends Model
    {
        use Searchable;

        /**
         * Get the engine used to index the model.
         *
         * @return \Laravel\Scout\Engines\Engine
         */
        public function searchableUsing()
        {
            return app(EngineManager::class)->engine('meilisearch');
        }
    }

<a name="identifying-users"></a>
### 사용자 식별

Scout를 사용하면 [Algolia](https://algolia.com)를 사용할 때 사용자를 자동으로 식별 할 수도 있습니다. 인증 된 사용자를 검색 작업과 연결하면 Algolia의 대시 보드 내에서 검색 분석을 볼 때 도움이 될 수 있습니다. 애플리케이션의 `.env` 파일에서 `SCOUT_IDENTIFY`를 `true`로 정의하면 사용자 식별을 활성화 할 수 있습니다.

```ini
SCOUT_IDENTIFY=true
```

이 기능을 활성화하면 요청의 IP 주소와 인증 된 사용자의 기본 식별자도 Algolia에 전달되므로이 데이터는 사용자가 만든 모든 검색 요청과 연결됩니다.

<a name="database-and-collection-engines"></a>
## 데이터베이스 / 컬렉션 엔진

<a name="database-engine"></a>
### 데이터베이스 엔진

> **Warning**
> 데이터베이스 엔진은 현제 MySQL 및 PostgreSQL을 지원합니다.

애플리케이션이 크지 않은 데이터베이스에서 동작하고, 부하가 가벼운 경우에는 Scout 의 "데이터베이스" 엔진을 사용하는 것이 편할 수 있습니다. 데이터베이스 엔진은 검색 결과를 얻기 위해서 "where like"을 통해서 Full Text 인덱싱을 사용합니다.  

데이터베이스 엔진을 사용하려면 `SCOUT_DRIVER` 환경변수 값을 `database`으로 지정하거나 애플리케이션의 `scout` 설정파일에서 `database` 드라이버로 지정하면 됩니다.

```ini
SCOUT_DRIVER=database
```

데이터베이스 엔진을 기본 검색 드라이버로 지정한 뒤에 [검색 가능한 데이터 설정](#configuring-searchable-data)을 완료해야합니다. 그 다음에 모델에 대한 [검색 쿼리 실행](#searching)을 시작할 수 있습니다. 데이터베이스 엔진에서는 Algolia, MeiliSearch와 같은 데이터 인덱싱작업은 필요하지 않습니다.

#### 데이터베이스 엔진의 검색 방식 커스터마이징

기본적으로 데이터베이스 엔진은 [검색 가능하다고 설정된](#configuring-searchable-data) 모델에 대해서 "where like" 쿼리를 실행합니다. 하지만 몇몇의 경우에서는 이로 인해서 성능이 낮게 나올 수 있습니다. 따라서 데이터베이스 엔진에서 사용하는 검색 방식을 커스터마이징 할 수 있습니다. Full text 검색을 해야하는 컬럼의 일부가 (`%example%`) 쿼리를 사용하는 대신 (`example%`)를 사용하는 형식으로 지정이 가능합니다. 

이 설정을 하기 위해서는 모델의 `toSearchableArray` 메소드에 PHP attribute를 할당하면 됩니다. 여기에 할당되지 않은 컬럼은 기본값 "where like" 방식이 사용됩니다.

```php
use Laravel\Scout\Attributes\SearchUsingFullText;
use Laravel\Scout\Attributes\SearchUsingPrefix;

/**
 * Get the indexable data array for the model.
 *
 * @return array
 */
#[SearchUsingPrefix(['id', 'email'])]
#[SearchUsingFullText(['bio'])]
public function toSearchableArray()
{
    return [
        'id' => $this->id,
        'name' => $this->name,
        'email' => $this->email,
        'bio' => $this->bio,
    ];
}
```

(역자주) 위의 예제를 확인하면 "id", "email" 은 prefix (`example%`), "bio"는 full text (`%example%`) 형태로 where like 쿼리가 생성됩니다.

> **Warning**
> 컬럼이 full text 쿼리 조건을 사용한다고 지정하기 전에 컬럼에 [full text index](/docs/{{version}}/migrations#available-index-types)가 생성되어 있는지 확인하십시오.

<a name="collection-engine"></a>
### 컬렉션 엔진

로컬 개발을 진행할 때도 Algolia 또는 MeiliSearch 검색 엔진을 사용할 수 있지만 "컬렉션" 엔진을 사용하는 것이 더 편리할 수 있습니다. 컬렉션 엔진은 "where" 절과 기존 데이터베이스의 결과에 대한 컬렉션 필터링을 사용하여 쿼리에 적용 가능한 검색 결과를 결정합니다. 이 엔진을 사용할 때 검색 가능한 모델을 "인덱싱"할 필요가 없습니다. 로컬 데이터베이스에서 간단히 검색되기 때문입니다.

컬렉션 엔진을 사용하려면 `SCOUT_DRIVER` 환경 변수의 값을 `collection`으로 설정하거나 애플리케이션의 `scout` 설정 파일에서 직접 `collection` 드라이버를 지정할 수 있습니다.

```ini
SCOUT_DRIVER=collection
```

컬렉션 드라이버를 기본 드라이버로 지정했으면 모델에 대해 [검색 쿼리 실행](#searching)을 시작할 수 있습니다. Algolia 또는 MeiliSearch 색인을 시드하는 데 필요한 색인과 같은 검색 엔진 색인은 수집 엔진을 사용할 때 필요하지 않습니다.

#### 데이터베이스 엔진과의 차이점

언뜻보기에는, "데이터베이스" 엔진과 "컬렉션" 엔진이 상당히 유사해보입니다. 두개의 엔진 모두 데이터베이스에서 검색 결과를 조회합니다. 하지만 컬렉션 엔진은 일치하는 레코드를 찾기 위해서 Full Text 인덱스나, `LIKE` 절을 사용하지 않습니다. 그 대신에, 모든 레코드를 가져와서 라라벨이 제공하는 `Str::is` 헬퍼 함수를 사용하여 모델의 속성값 안에 원하는 검색 문자열이 포함되어 있는지 확인합니다.

컬렉션 엔진은 라라벨이 지원하는 모든 관계형 데이터베이스(SQLite, SQL Server를 포함)에서 동작하기 때문에 가장 호환성이 좋은 엔진입니다. 하지만 데이터베이스 엔진보다 성능이 좋지 않습니다.

<a name="indexing"></a>
## 인덱싱

<a name="batch-import"></a>
### 배치작업을 통한 데이터 임포트

만약 기존에 존재하는 프로젝트에 스카우트를 추가로 설치했다면, 검색 드라이버로 인덱싱할 데이터베이스 데이터 레코드가 존재할 것입니다. 스카우트는 `scout:import` 아티즌 명령어를 사용하여 기존에 존재하는 모든 데이터베이스 레코드를 검색 인덱스로 가져오게 할 수 있습니다. 

```shell
php artisan scout:import "App\Models\Post"
```

`flush` 명령어는 검색 인덱스에서 모델의 모든 레코드를 삭제하는데 사용합니다.

```shell
php artisan scout:flush "App\Models\Post"
```

<a name="modifying-the-import-query"></a>
#### 임포트 쿼리 수정하기

배치 작업에서 사용하는 임포트 작업에서 사용되는 쿼리를 수정하려면 모델에 `makeAllSearchableUsing` 메서드를 정의하면 됩니다. 이 메서드는 모델을 임포팅 하기 전에 필요한 연관관계를 Eager 로딩하도록 지정하기 좋은 곳입니다. 

    /**
     * Modify the query used to retrieve models when making all of the models searchable.
     *
     * @param  \Illuminate\Database\Eloquent\Builder  $query
     * @return \Illuminate\Database\Eloquent\Builder
     */
    protected function makeAllSearchableUsing($query)
    {
        return $query->with('author');
    }

<a name="adding-records"></a>
### 레코드 추가하기

모델에 `Laravel\Scout\Searchable` trait를 추가했다면, 당신은 모델 인스턴스의 `save` 또는 `create` 메서드를 호출하기만 하면 됩니다. 그러면 자동으로 검색 인덱스에 추가될 것입니다. 만약 스카우트에 [큐 사용](#queueing) 설정을 했다면, 이 작업은 당신의 큐 워커에 의해 백그라운드에서 실행될 것입니다.

    use App\Models\Order;

    $order = new Order;

    // ...

    $order->save();

<a name="adding-records-via-query"></a>
#### 쿼리를 통해 레코드 추가하기

만약 당신이 엘로퀀트 쿼리를 통해 조회된 모델의 컬렉션을 검색 인덱스에 추가하고 싶다면, 엘로퀀트 쿼리 인스턴스에 `searchable` 메서드를 체이닝하여 호출하면 됩니다. `searchable` 메서드는 쿼리의 [결과를 묶음으로 처리](/docs/{{version}}/eloquent#chunking-results)하여 검색 인덱스에 레코드를 추가할 것입니다. 앞서와 마찬가지로 만약 당신이 스카우트에서 큐를 사용하기로 설정했다면, 모든 묶음은 큐 워커에 의해 백그라운드에서 추가될 것입니다.

    use App\Models\Order;

    Order::where('price', '>', 100)->searchable();

연관관계에 모델 인스턴스에서도 `searchable` 메서드를 호출할 수 있습니다.

    $user->orders()->searchable();

또는 메모리에 엘로퀀트 모델 컬렉션을 획득한 다음, 컬렉션 인스턴스에서 `searchable` 메서드를 호출하여 모델에 해당하는 검색 인덱스에서 레코드를 추가 할 수 있습니다.

    $orders->searchable();

> **Note**
> `searchable` 메서드를 "upsert" 작업으로 생각할 수도 있습니다. 다시 말해, 모델 레코드가 이미 인덱스에 존재한다면, 업데이트될 것입니다. 검색 인덱스에 존재하지 않는다면, 인덱스에 추가될 것입니다.

<a name="updating-records"></a>
### 레코드 업데이트하기

모델을 업데이트하기 위해서는, 모델 인스턴스의 프로퍼티를 업데이트하고, 데이터베이스에 `save` 하기만 하면 됩니다. 스카우트는 자동으로 변경 사항을 검색 인덱스에 적용할 것입니다.

    use App\Models\Order;

    $order = Order::find(1);

    // Update the order...

    $order->save();

여러분은 모델 컬렉션을 업데이트 하는데 엘로퀀트 쿼리 인스턴스의 `searchable` 메서드를 호출할 수 있습니다. 만약 모델이 검색 인덱스에 존재하지 않는다면, 인덱스는 새로 생성됩니다.

    Order::where('price', '>', 100)->searchable();

연관관계에 모델에 대한 검색 인덱스 레코드를 업데이트 하려면 연관관계 인스턴스에서 `searchable` 메서드를 호출하면 됩니다. 

    $user->orders()->searchable();

또는 메모리에 엘로퀀트 모델 컬렉션을 획득한 다음, 컬렉션 인스턴스에서 `searchable` 메서드를 호출하여 모델에 해당하는 검색 인덱스에서 레코드를 업데이트 할 수 있습니다.

    $orders->searchable();

<a name="removing-records"></a>
### 레코드 삭제하기

인덱스에서 레코드를 삭제하기 위해서는, 데이터베이스에서 해당 모델의 `delete` 메서드를 호출하면 됩니다. 이 삭제 방법은 [soft deleted](/docs/{{version}}/eloquent#soft-deleting)가 적용된 모델에서도 작동합니다.

    use App\Models\Order;

    $order = Order::find(1);

    $order->delete();

만약 레코드를 삭제하기 전에 모델을 조회하고 싶지 않다면, 엘로퀀트 쿼리 인스턴스의 `unsearchable` 메서드를 사용할 수 있습니다.

    Order::where('price', '>', 100)->unsearchable();

연관관계의 모든 모델에 대한 검색 인덱스 레코드를 삭제하려면 연관관계 인스턴스에서 `unsearchable` 메서드를 호출하면 됩니다.

    $user->orders()->unsearchable();

또는 메모리에 엘로퀀트 모델 컬렉션을 획득한 다음, 컬렉션 인스턴스에서 `unsearchable` 메서드를 호출하여 모델에 해당하는 검색 인덱스에서 레코드를 삭제할 수 있습니다.

    $orders->unsearchable();

<a name="pausing-indexing"></a>
### 인덱싱 일시 멈춤

가끔 검색 인덱스와 모델 데이터를 동기화하지 않으면서 모델에 엘로퀀트 작업을 일괄처리할 필요가 있습니다. 이때 `withoutSyncingToSearch` 메서드를 사용하면 됩니다. 이 메서드는 하나의 클로저를 인자로 받아서 바로 실행합니다. 이 클로저 안에서 실행되는 어떤 모델 작업이든 모델의 인덱스와 동기화 되지 않을 것입니다.

    use App\Models\Order;

    Order::withoutSyncingToSearch(function () {
        // Perform model actions...
    });

<a name="conditionally-searchable-model-instances"></a>
### 조건에 따른 검색 모델 인스턴스

때로는 특정한 조건에 맞는 경우에만 모델을 검색되도록 만들 수도 있습니다. 예를 들어, `App\Models\Post` 모델이 "작업중" 과 "발행됨" 두가지 상태를 가지는 경우를 생각해 보겠습니다. "발행됨" 상태의 글만 검색이 가능하기를 원할 수 있습니다. 이를 위해서는, 모델에 `shouldBeSearchable` 메서드를 정의하면 됩니다.

    /**
     * Determine if the model should be searchable.
     *
     * @return bool
     */
    public function shouldBeSearchable()
    {
        return $this->isPublished();
    }

`shouldBeSearchable` 메서드는 `save` 와 `create` 메서드, 쿼리 또는 관계 모델을 통해서 모델을 조작한 경우에만 적용됩니다. `searchable` 메서드를 사용하여 모델 또는 컬렉션을 직접적으로 검색 가능하게 하면, `shouldBeSearchable` 메서드의 결과를 덮어씌게 됩니다.

> **Warning**
> `shouldBeSearchable` 메소드는 "데이터베이스" 엔진을 사용할 때는 활용할 수 없습니다. 검색 가능한 모든 데이터가 항상 데이터베이스에 저장되기 때문입니다. 데이터베이스 엔진을 사용할 때 유사항 동작을 수행하려면 [where 절](#where-clauses)을 사용해야합니다.

<a name="searching"></a>
## 검색하기

`search` 메서드를 사용하여 모델을 검색할 수 있습니다. search 메서드는 모델을 검색할 때 사용될 단일 스트링을 파라미터로 가집니다. 그런 다음, 주어진 검색 쿼리와 일치하는 엘로퀀트 모델을 조회하기 위하여 `get` 메서드를 연결해서 사용해야 합니다.

    use App\Models\Order;

    $orders = Order::search('Star Trek')->get();

스카우트는 엘로퀀트 모델의 모음을 반환하기 때문에, 여러분은 라우트나 컨트롤러에서 결과를 바로 반환할 수 있으며, 그 결과는 JSON으로 자동 변환될 것입니다.

    use App\Models\Order;
    use Illuminate\Http\Request;

    Route::get('/search', function (Request $request) {
        return Order::search($request->search)->get();
    });

검색 결과가 Eloquent 모델로 변환되기 전의 상태를 확인하고 싶다면, `raw` 메서드를 사용하면 됩니다.

    $orders = Order::search('Star Trek')->raw();

<a name="custom-indexes"></a>
#### 커스텀 인덱스

검색 쿼리는 일반적으로 모델의 [`searchableAs`](#configuring-model-indexes) 메서드에 의한 인덱스가 지정되어 실행됩니다. 대신에 `within` 메서드를 사용하여 검색하고자 하는 커스텀 인덱스를 지정할 수 있습니다.

    $orders = Order::search('Star Trek')
        ->within('tv_shows_popularity_desc')
        ->get();

<a name="where-clauses"></a>
### Where 클로저

여러분은 검색 쿼리를 위해 "where" 클로저를 스카우트에 추가할 수 있습니다. 현재로서는, 그 클로저는 오직 기본적인 수식을 지원하며, 주로 고유한 ID에 의해 검색 쿼리를 한정할 때 유용합니다.

    use App\Models\Order;

    $orders = Order::search('Star Trek')->where('user_id', 1)->get();

`whereIn` 메서드를 사용하여 검색결과에 조건을 추가할 수 있습니다.

    $orders = Order::search('Star Trek')->whereIn(
        'status', ['paid', 'open']
    )->get();

검색 인덱스는 관계형 데이터베이스가 아니므로 현재 고급 "where" 절이 지원되지 않습니다.

> **Warning**
> 만약 여러분의 애플리케이션이 MeiliSearch를 사용하고 있다면, 여러분의 애플리케이션의 [필터링 가능한 속성들](#configuring-filterable-data-for-meilisearch)을 설정해야 합니다.

<a name="pagination"></a>
### 페이지네이션

모델 컬렉션의 조회에 더해, `paginate` 메서드를 사용하여 검색결과를 페이징할 수 있습니다. [엘로퀀트 쿼리에서 페이징](/docs/{{version}}/pagination)을 했던 것처럼, 이 메서드는 `Illuminate\Pagination\LengthAwarePaginator` 인스턴스를 반환합니다. 

    use App\Models\Order;

    $orders = Order::search('Star Trek')->paginate();

`paginate` 메서드의 첫번째 파라미터를 사용하여 한 페이지에 검색할 모델의 수량을 지정할 수도 있습니다.

    $orders = Order::search('Star Trek')->paginate(15);

결과를 검색한 다음에, 여러분은 엘로퀀트 쿼리에서 페이징했던 것처럼 검색 결과를 출력하고 [블레이드](/docs/{{version}}/blade)를 사용하여 페이지 링크를 출력할 수 있습니다.

```html
<div class="container">
    @foreach ($orders as $order)
        {{ $order->price }}
    @endforeach
</div>

{{ $orders->links() }}
```

물론, 페이징이 적용된 결과를 JSON으로 조회하려면, 라우트나 컨트롤러에서 직접 Paginator 인스턴스를 반환할수도 있습니다

    use App\Models\Order;
    use Illuminate\Http\Request;

    Route::get('/orders', function (Request $request) {
        return Order::search($request->input('query'))->paginate(15);
    });

> **Warning**  
> 검색 엔진은 엘로퀀트 모델의 글로벌 스코프 정의를 알지 못하므로 스카우트 페이지네이션을 활용하는 글로벌 스코프를 사용해서는 안됩니다. 아니면 스카우트를 통해 검색할 때는 글로벌 스코프의 제약조건을 다시 만들어야 합니다.

<a name="soft-deleting"></a>
### 소프트 삭제

인덱싱되는 모델이 [소프트 삭제](/docs/{{version}}/eloquent#soft-deleting)된 상태이고, 소프트 삭제된 모델을 검색할 필요가 있다면, `config/scout.php` 설정 파일의 `soft_delete` 옵션을 `true`로 지정하면 됩니다.

    'soft_delete' => true,

이 설정 값이 `true` 인 경우, Scout-스카우트는 소프트 삭제된 모델을 검색 인덱스에서 삭제 하지 않습니다. 대신에, 인덱스된 레코드에 `__soft_deleted` 라는 숨겨진 속성을 설정합니다. 그러면 검색할 때 `withTrashed` 또는 `onlyTrashed` 메서드를 사용하여 소프트 삭제된 레코드를 조회할 수 있습니다.

    use App\Models\Order;

    // Include trashed records when retrieving results...
    $orders = Order::search('Star Trek')->withTrashed()->get();

    // Only include trashed records when retrieving results...
    $orders = Order::search('Star Trek')->onlyTrashed()->get();

> **Note**
> `forceDelete` 메서드를 사용하여 소프트 삭제된 모델을 완전히 삭제할 때, Scout-스카우트는 검색 인덱스에서 자동으로 이를 제거합니다.

<a name="customizing-engine-searches"></a>
### 엔진의 검색 커스터마이징

엔진의 검색 동작을 커스터마이징 할 필요가있는 경우, 클로저를 `search` 메서드의 두 번째 인수로 전달할 수 있습니다. 예를 들어 검색어를 Algolia에 전달하기 전에 이 콜백을 사용하여 검색 옵션에 지리적 위치 데이터를 추가 할 수 있습니다.

    use Algolia\AlgoliaSearch\SearchIndex;
    use App\Models\Order;

    Order::search(
        'Star Trek',
        function (SearchIndex $algolia, string $query, array $options) {
            $options['body']['query']['bool']['filter']['geo_distance'] = [
                'distance' => '1000km',
                'location' => ['lat' => 36, 'lon' => 111],
            ];

            return $algolia->search($query, $options);
        }
    )->get();

<a name="customizing-the-eloquent-results-query"></a>
#### 엘로퀀트 결과 커리 커스터마이징 하기

Scout이 애플리케이션의 검색 엔진에서 매칭되는 엘로퀀트 모델 목록을 조회한 다음에, 엘로퀀트는 검색에 기본키를 사용합니다. `query` 메소드를 사용하면 이 쿼리를 커스터마이징 할 수 있습니다. `query` 메소드는 엘로퀀트 쿼리 빌더 인스턴스를 인자로 받는 클로저를 넘겨받아야 합니다.

```php
use App\Models\Order;

$orders = Order::search('Star Trek')
    ->query(fn ($query) => $query->with('invoices'))
    ->get();
```

이 콜백은 관련 모델이 애플리케이션의 검색엔진에서 조회된 이후에 호출되기 때문에, `query` 메소드에는 결과를 "필터링" 하는용도로 사용하면 안됩니다. 그 대신에 [Scout where 절](#where-clauses)을 사용하십시오.

<a name="custom-engines"></a>
## 커스텀 엔진

<a name="writing-the-engine"></a>
#### 엔진 구현하기

만약 내장된 스카우트 검색 엔진들 중 하나도 여러분의 요구사항을 충족하지 못한다면, 여러분은 여러분만의 커스텀 엔진을 구현하고 스카우트에 등록할 수 있습니다. 여러분이 구현하는 엔진은 `Laravel\Scout\Engines\Engine` 추상클래스를 상속받아야 합니다. 이 추상클래스는 반드시 구현해야하는 7개의 메서드를 포함하고 있습니다.

    use Laravel\Scout\Builder;

    abstract public function update($models);
    abstract public function delete($models);
    abstract public function search(Builder $builder);
    abstract public function paginate(Builder $builder, $perPage, $page);
    abstract public function mapIds($results);
    abstract public function map(Builder $builder, $results, $model);
    abstract public function getTotalCount($results);
    abstract public function flush($model);

`Laravel\Scout\Engines\AlgoliaEngine` 클래스에 구현된 그 메서드들을 살펴보는 것이 여러분에게 도움이 될 것입니다. 이 클래스는 여러분만의 엔진에 그 메서드를을 어떻게 구현해야 할지 배울 수 있는 좋은 출발점이 될 것입니다.

<a name="registering-the-engine"></a>
#### 엔진 등록하기

커스텀엔진을 작성했다면, 스카우트 엔진 매니저의 `extend` 메서드를 사용하여 그 엔진을 등록할 수 있습니다. 스카우트 엔진 매니저는 라라벨 서비스 컨테이너에서 확인할 수 있습니다. 여러분은 이 `extend` 메서드를 `App\Providers\AppServiceProvider` 클래스 또는 애플리케이션에서 사용되는 다른 서비스 프로바이더의 `boot` 메서드에서 `extend` 메서드를 호출해야 합니다. 예를 들어, 만약 여러분이 `MySqlSearchEngine`을 구현했다면, 다음과 같이 등록할 수 있습니다.

    use App\ScoutExtensions\MySqlSearchEngine
    use Laravel\Scout\EngineManager;

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        resolve(EngineManager::class)->extend('mysql', function () {
            return new MySqlSearchEngine;
        });
    }

여러분의 엔진을 등록한 즉시, 애플리케이션의 `config/scout.php` 설정 파일의 `driver`를 변경하여 스카우트의 기본 드라이버를 그것으로 지정하십시오.

    'driver' => 'mysql',

<a name="builder-macros"></a>
## 빌더 매크로

커스텀 스카운 검색 빌더 메서드를 정의하고 싶다면 `Laravel\Scout\Builder` 클래스에 `macro` 메서드를 사용할 수 있습니다. 일반적으로 "매크로"는 [서비스 프로바이더](/docs/{{version}}/providers)의 `boot` 메서드 내에 정의되어야합니다.


    use Illuminate\Support\Facades\Response;
    use Illuminate\Support\ServiceProvider;
    use Laravel\Scout\Builder;

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        Builder::macro('count', function () {
            return $this->engine()->getTotalCount(
                $this->engine()->search($this)
            );
        });
    }

`macro` 함수는 첫 번째 인수로 매크로의 이름을 받아들입니다. 두 번째 인수는 클로저입니다. 매크로의 클로저는 `Laravel\Scout\Builder` 구현에서 매크로 이름을 호출 할 때 실행됩니다.

    use App\Models\Order;

    Order::search('Star Trek')->count();
