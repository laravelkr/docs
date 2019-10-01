# 라라벨 Scout-스카우트

- [시작하기](#introduction)
- [설치하기](#installation)
    - [큐 사용하기](#queueing)
    - [드라이버 사전준비사항](#driver-prerequisites)
- [설정하기](#configuration)
    - [모델 인덱스 설정하기](#configuring-model-indexes)
    - [검색 데이터 설정하기](#configuring-searchable-data)
    - [모델 ID 설정하기](#configuring-the-model-id)
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

라라벨 스카우트는 [Eloquent 모델](/docs/{{version}}/eloquent)에 full-text 검색 기능을 장착할 수 있는 드라이버 기반의 단순한 솔루션을 제공합니다. 스카우트는 모델 옵저버를 사용하여 엘로퀀트 레코드와 동기화된 검색 인덱스를 자동으로 유지합니다.

현재, 스카우트는 [Algolia](https://www.algolia.com/) 드라이버를 내장하고 있습니다; 하지만, 간단하게 커스텀 드라이버를 작성할 수 있으므로, 자유롭게 스카우트를 확장하여 여러분 자신의 검색 기능을 구현할 수 있습니다.

<a name="installation"></a>
## 설치하기

먼저, 컴포저 패키지 매니저를 사용하여 스카우트를 설치하십시오:

    composer require laravel/scout

스카우트를 설치한 다음, `vendor:publish` 아티즌 명령어를 사용하여 스카우트 설정 파일을 퍼블리싱 해야합니다. 이 명령을 실행하면 `config` 디렉토리에 `scout.php` 설정파일이 생성됩니다.

    php artisan vendor:publish --provider="Laravel\Scout\ScoutServiceProvider"

마지막으로, `Laravel\Scout\Searchable` trait를 검색 기능을 장착할 모델에 추가합니다. 이 trait는 모델과 검색 드라이버의 동기화를 유지하기 위한 모델 옵저버를 등록할 것입니다.

    <?php

    namespace App;

    use Laravel\Scout\Searchable;
    use Illuminate\Database\Eloquent\Model;

    class Post extends Model
    {
        use Searchable;
    }

<a name="queueing"></a>
### 큐 사용하기

스카우트를 사용하기 위한 필수 요구사항은 아니지만, 스카우트를 사용하기에 앞서 [큐 드라이버](/docs/{{version}}/queues)의 구성을 고려해 보아야 합니다. 큐를 사용하면 스카우트는 모델 정보와 검색 인덱스를 싱크하는 모든 작업을 큐에 등록할 것이고, 이는 여러분의 웹사이트가 더 나은 응답시간을 제공할 수 있도록 할 것입니다.

일단 큐 드라이버를 구성한 다음, `config/scout.php` 설정 파일의 `queue` 옵션을 `true`로 지정하십시오.

    'queue' => true,

<a name="driver-prerequisites"></a>
### 드라이버 사전준비사항

#### Algolia

Algolia 드라이버를 사용할 때, Algolia 계정의 `id`와 `secret` 정보를 `config/scout.php` 설정 파일에 입력해야 합니다. 계정 정보를 설정한 다음에는, 컴포저를 통해 Algolia PHP SDK를 설치해야 합니다.

    composer require algolia/algoliasearch-client-php:^2.2

<a name="configuration"></a>
## 환경 설정

<a name="configuring-model-indexes"></a>
### 모델 인덱스 설정하기

각각의 Eloquent 모델은 해당 모델에 대한 모든 검색 레코드를 가지고 있는 검색 "인덱스"와 동기화 됩니다. 다시 말해, 여러분은 각각의 인덱스를 Mysql 테이블처럼 생각할 수 있습니다. 기본적으로, 각각의 모델은 모들의 "테이블" 명과 매칭되는 인덱스에 저장됩니다. 일반적으로, 인덱스 명은 모델 이름의 복수형입니다; 그렇치만 모델의 `searchableAs` 메소드를 오버라이드 해서 인덱스명을 자유롭게 변경할 수 있습니다.

    <?php

    namespace App;

    use Laravel\Scout\Searchable;
    use Illuminate\Database\Eloquent\Model;

    class Post extends Model
    {
        use Searchable;

        /**
         * Get the index name for the model.
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

기본적으로, 주어진 모델의 `toArray` 형식 그대로가 검색 인덱스로 유지됩니다. 만약 당신이 검색 인덱스와 동기화되는 데이터를 변경하고 싶다면, 모델의 `toSearchableArray` 메소드를 오버라이드 할 수 있습니다.

    <?php

    namespace App;

    use Laravel\Scout\Searchable;
    use Illuminate\Database\Eloquent\Model;

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

            // Customize array...

            return $array;
        }
    }

<a name="configuring-the-model-id"></a>
### 모델 ID 설정하기

기본적으로 Scout-스카우트는 모델의 primary 키를 검색 인덱스에 저장되는 고유한 ID로 사용합니다. 이 동작을 커스터마이징 하고자 한다면, 모들의 `getScoutKey` 메소드를 오버라이드 하면 됩니다.

    <?php

    namespace App;

    use Laravel\Scout\Searchable;
    use Illuminate\Database\Eloquent\Model;

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
    }

<a name="indexing"></a>
## 인덱싱

<a name="batch-import"></a>
### 임포트 일괄처리

만약 이미 존재하는 프로젝트에 스카우트를 설치했다면, 당신은 이미 검색 드라이버로 임포트할 데이터베이스 레코드를 가지고 있을 것입니다. 스카우트는 존재하는 모든 데이터를 검색 인덱스로 임포트할 때 사용할 수 있는 `import` 아티즌 명령을 제공합니다.

    php artisan scout:import "App\Post"

`flush` 명령어는 검색 인덱스에서 모델의 모든 레코드를 삭제하는데 사용합니다.

    php artisan scout:flush "App\Post"

<a name="adding-records"></a>
### 레코드 추가하기

모델에 `Laravel\Scout\Searchable` trait를 추가했다면, 당신은 모델 인스턴스의 `save`를 실행하기만 하면 됩니다. 그러면 자동으로 검색 인덱스에 추가될 것입니다. 만약 스카우트에 [큐 사용](#queueing) 설정을 했다면, 이 작업은 당신의 큐 워커에 의해 백그라운드에서 실행될 것입니다.

    $order = new App\Order;

    // ...

    $order->save();

#### 쿼리를 통해 추가하기

만약 당신이 엘로퀀트 쿼리를 통해 조회된 모델의 모음을 추가하고 싶다면, `searchable` 메소드를 엘로퀀트 쿼리에 연결지어 쓸 수 있습니다. `searchable` 메소드는 쿼리의 [결과를 묶음으로 처리](/docs/{{version}}/eloquent#chunking-results)하여 검색 인덱스에 레코드를 추가할 것입니다. 앞서와 마찬가지로 만약 당신이 스카우트에서 큐를 사용하기로 설정했다면, 모든 묶음은 큐 워커에 의해 백그라운드에서 추가될 것입니다.

    // Adding via Eloquent query...
    App\Order::where('price', '>', 100)->searchable();

    // You may also add records via relationships...
    $user->orders()->searchable();

    // You may also add records via collections...
    $orders->searchable();

`searchable` 메소드를 "upsert" 작업으로 생각할 수 있습니다. 다시 말해, 모델 레코드가 이미 인덱스에 존재한다면, 업데이트될 것입니다. 검색 인덱스에 존재하지 않는다면, 인덱스에 추가될 것입니다.

<a name="updating-records"></a>
### 레코드 업데이트하기

모델을 업데이트하기 위해서는, 모델 인스턴스의 프로퍼티를 업데이트하고, 데이터베이스에 `save` 하기만 하면 됩니다. 스카우트는 자동으로 변경 사항을 검색 인덱스에 적용할 것입니다.

    $order = App\Order::find(1);

    // Update the order...

    $order->save();

여러분은 엘로퀀트 쿼리의 결과로 조회된 모델들을 업데이트할 때에도 `searchable` 메소드를 사용할 수 있습니다. 만약 모델이 검색 인덱스에 존재하지 않는다면, 새로 생성될 것입니다.

    // Updating via Eloquent query...
    App\Order::where('price', '>', 100)->searchable();

    // You may also update via relationships...
    $user->orders()->searchable();

    // You may also update via collections...
    $orders->searchable();

<a name="removing-records"></a>
### 레코드 삭제하기

인덱스에서 레코드를 삭제하기 위해서는, 데이터베이스에서 해당 모델을 `delete`하면 됩니다. 이 삭제 방법은 [soft deleted](/docs/{{version}}/eloquent#soft-deleting)가 적용된 모델에서도 작동합니다.

    $order = App\Order::find(1);

    $order->delete();

만약 레코드를 삭제하기 전에 모델을 조회하고 싶지 않다면, 엘로퀀트 쿼리에 `unsearchable` 메소드를 사용할 수 있습니다.

    // Removing via Eloquent query...
    App\Order::where('price', '>', 100)->unsearchable();

    // You may also remove via relationships...
    $user->orders()->unsearchable();

    // You may also remove via collections...
    $orders->unsearchable();

<a name="pausing-indexing"></a>
### 인덱싱 일시 멈춤

가끔 검색 인덱스와 모델 데이터를 동기화하지 않으면서 모델에 엘로퀀트 작업을 일괄처리할 필요가 있습니다. 이때 `withoutSyncingToSearch` 메소드를 사용하면 됩니다. 이 메소드는 하나의 콜백 함수를 받아서 바로 실행합니다. 이 콜백 함수 안에서 실행되는 어떤 모델 작업이든 모델의 인덱스와 동기화 되지 않을 것입니다.

    App\Order::withoutSyncingToSearch(function () {
        // Perform model actions...
    });

<a name="conditionally-searchable-model-instances"></a>
### 조건에 따른 검색 모델 인스턴스

때로는 특정한 조건에 맞는 경우에만 모델을 검색되도록 만들 수도 있습니다. 예를 들어, `App\Post` 모델이 "작업중" 과 "발행됨" 두가지 상태를 가지는 경우를 생각해 보겠습니다. "발행됨" 상태의 글만 검색이 가능하기를 원할 수 있습니다. 이를 위해서는, 모델에 `shouldBeSearchable` 메소드를 정의하면 됩니다.

    public function shouldBeSearchable()
    {
        return $this->isPublished();
    }

`shouldBeSearchable` 메소드는 `save` 메소드, 쿼리 또는 관계 모델을 통해서 모델을 조작한 경우에만 적용됩니다. `searchable` 메소드를 사용하여 모델 또는 컬렉션을 직접적으로 검색 가능하게 하면, `shouldBeSearchable` 메소드의 결과를 덮어씌게 됩니다.

    // Will respect "shouldBeSearchable"...
    App\Order::where('price', '>', 100)->searchable();

    $user->orders()->searchable();

    $order->save();

    // Will override "shouldBeSearchable"...
    $orders->searchable();

    $order->searchable();

<a name="searching"></a>
## 검색하기

`search` 메소드를 사용하여 모델을 검색할 수 있습니다. search 메소드는 모델을 검색할 때 사용될 단일 스트링을 파라메터로 가집니다. 그런 다음, 주어진 검색 쿼리와 일치하는 엘로퀀트 모델을 조회하기 위하여 `get` 메소드를 연결해서 사용해야 합니다.

    $orders = App\Order::search('Star Trek')->get();

스카우트는 엘로퀀트 모델의 모음을 반환하기 때문에, 여러분은 라우트나 컨트롤러에서 결과를 바로 반환할 수 있으며, 그 결과는 JSON으로 자동 변환될 것입니다.

    use Illuminate\Http\Request;

    Route::get('/search', function (Request $request) {
        return App\Order::search($request->search)->get();
    });

결과가 Eloquent 모델로 변환되기 전의 상태를 얻고자 한다면, `raw` 메소드를 사용하십시오:

    $orders = App\Order::search('Star Trek')->raw();

검색 쿼리는 일반적으로 모델의 [`searchableAs`](#configuring-model-indexes) 메소드에 의한 인덱스가 지정되어 실행됩니다. 대신에 `within` 메소드를 사용하여 검색하고자 하는 커스텀 인덱스를 지정할 수 있습니다.

    $orders = App\Order::search('Star Trek')
        ->within('tv_shows_popularity_desc')
        ->get();

<a name="where-clauses"></a>
### Where 클로저

여러분은 검색 쿼리를 위해 "where" 클로저를 스카우트에 추가할 수 있습니다. 현재로서는, 그 클로저는 오직 기본적인 수식을 지원하며, 주로 tenant ID에 의해 검색 쿼리를 한정할 때 유용합니다. 검색 인덱스는 관계형 데이터베이스가 아니기 때문에, 더 고수준의 "where" 클로저는 아직 지원하지 않습니다.

    $orders = App\Order::search('Star Trek')->where('user_id', 1)->get();

<a name="pagination"></a>
### 페이지네이션

모델의 검색과 함께, `paginate` 메소드를 사용하여 검색결과를 페이징할 수도 있습니다. [엘로퀀트 쿼리에서 페이징](/docs/{{version}}/pagination)을 했던 것처럼, 이 메소드는 `Paginator` 인스턴스를 반환합니다.

    $orders = App\Order::search('Star Trek')->paginate();

`paginate` 메소드의 첫번째 파라메터를 사용하여 한 페이지에 검색할 모델의 수량을 지정할 수도 있습니다.

    $orders = App\Order::search('Star Trek')->paginate(15);

결과를 검색한 다음에, 여러분은 엘로퀀트 쿼리에서 페이징했던 것처럼 검색 결과를 출력하고 [블레이드](/docs/{{version}}/blade)를 사용하여 페이지 링크를 출력할 수 있습니다.

    <div class="container">
        @foreach ($orders as $order)
            {{ $order->price }}
        @endforeach
    </div>

    {{ $orders->links() }}

<a name="soft-deleting"></a>
### 소프트 삭제

인덱싱되는 모델이 [소프트 삭제](/docs/{{version}}/eloquent#soft-deleting)된 상태이고, 소프트 삭제된 모델을 검색할 필요가 있다면, `config/scout.php` 설정 파일의 `soft_delete` 옵션을 `true`로 지정하면 됩니다.

    'soft_delete' => true,

이 설정 값이 `true` 인 경우, Scout-스카우트는 소프트 삭제된 모델을 검색 인덱스에서 삭제 하지 않습니다. 대신에, 인덱스된 레코드에 `__soft_deleted` 라는 숨겨진 속성을 설정합니다. 그러면 검색할 때 `withTrashed` 또는 `onlyTrashed` 메소드를 사용하여 소프트 삭제된 레코드를 조회할 수 있습니다.

    // Include trashed records when retrieving results...
    $orders = App\Order::search('Star Trek')->withTrashed()->get();

    // Only include trashed records when retrieving results...
    $orders = App\Order::search('Star Trek')->onlyTrashed()->get();

> {tip} `forceDelete` 메소드를 사용하여 소프트 삭제된 모델을 완전히 삭제할 때, Scout-스카우트는 검색 인덱스에서 자동으로 이를 제거합니다.

<a name="customizing-engine-searches"></a>
### 엔진의 검색 커스터마이징

엔진의 검색 동작을 커스터마이징 할 필요가있는 경우, 콜백을 `search` 메소드의 두 번째 인수로 전달할 수 있습니다. 예를 들어 검색어를 Algolia에 전달하기 전에 이 콜백을 사용하여 검색 옵션에 지리적 위치 데이터를 추가 할 수 있습니다.

    use Algolia\AlgoliaSearch\SearchIndex;

    App\Order::search('Star Trek', function (SearchIndex $algolia, string $query, array $options) {
        $options['body']['query']['bool']['filter']['geo_distance'] = [
            'distance' => '1000km',
            'location' => ['lat' => 36, 'lon' => 111],
        ];

        return $algolia->search($query, $options);
    })->get();

<a name="custom-engines"></a>
## 커스텀 엔진

#### 엔진 구현하기

만약 내장된 스카우트 검색 엔진들 중 하나도 여러분의 요구사항을 충족하지 못한다면, 여러분은 여러분만의 커스텀 엔진을 구현하고 스카우트에 등록할 수 있습니다. 여러분이 구현하는 엔진은 `Laravel\Scout\Engines\Engine` 추상클래스를 상속받아야 합니다. 이 추상클래스는 반드시 구현해야하는 7개의 메소드를 포함하고 있습니다.

    use Laravel\Scout\Builder;

    abstract public function update($models);
    abstract public function delete($models);
    abstract public function search(Builder $builder);
    abstract public function paginate(Builder $builder, $perPage, $page);
    abstract public function mapIds($results);
    abstract public function map($results, $model);
    abstract public function getTotalCount($results);
    abstract public function flush($model);

`Laravel\Scout\Engines\AlgoliaEngine` 클래스에 구현된 그 메소드들을 살펴보는 것이 여러분에게 도움이 될 것입니다. 이 클래스는 여러분만의 엔진에 그 메소드를을 어떻게 구현해야 할지 배울 수 있는 좋은 출발점이 될 것입니다.

#### 엔진 등록하기

커스텀엔진을 작성했다면, 스카우트 엔진 매니저의 `extend` 메소드를 사용하여 그 엔진을 등록할 수 있습니다. 여러분은 이 `extend` 메소드를 `AppServiceProvider`나 여러분의 애플리케이션에서 사용되는 다른 서비스 프로바이더의 `boot` 메소드에서 호출해야 합니다. 예를 들어, 만약 여러분이 `MySqlSearchEngine`을 구현했다면, 다음과 같이 등록할 수 있습니다.

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

여러분의 엔진을 등록한 즉시, `config/scout.php` 설정 파일의 `driver`를 변경하여 스카우트의 기본 드라이버를 그것으로 지정하십시오.

    'driver' => 'mysql',

<a name="builder-macros"></a>
## 빌더 매크로

커스텀 빌더 메소드를 정의하고 싶다면 `Laravel\Scout\Builder` 클래스에 `macro` 메소드를 사용할 수 있습니다. 일반적으로 "매크로"는 [서비스 프로바이더](/docs/{{version}}/providers)의 `boot` 메소드 내에 정의되어야합니다.

    <?php

    namespace App\Providers;

    use Laravel\Scout\Builder;
    use Illuminate\Support\ServiceProvider;
    use Illuminate\Support\Facades\Response;

    class ScoutMacroServiceProvider extends ServiceProvider
    {
        /**
         * Register the application's scout macros.
         *
         * @return void
         */
        public function boot()
        {
            Builder::macro('count', function () {
                return $this->engine->getTotalCount(
                    $this->engine()->search($this)
                );
            });
        }
    }

`macro` 함수는 첫 번째 인수로 이름을 받아들입니다. 두 번째 인수는 Closure입니다. 매크로의 클로저는 `Laravel\Scout\Builder` 구현에서 매크로 이름을 호출 할 때 실행됩니다 :

    App\Order::search('Star Trek')->count();
