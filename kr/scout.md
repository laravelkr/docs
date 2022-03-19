# Laravel Scout
# 라라벨 Scout-스카우트

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Installation](#installation)
- [설치하기](#installation)
    - [Driver Prerequisites](#driver-prerequisites)
    - [드라이버 사전준비사항](#driver-prerequisites)
    - [Queueing](#queueing)
    - [큐 사용하기](#queueing)
- [Configuration](#configuration)
- [설정하기](#configuration)
    - [Configuring Model Indexes](#configuring-model-indexes)
    - [모델 인덱스 설정하기](#configuring-model-indexes)
    - [Configuring Searchable Data](#configuring-searchable-data)
    - [검색 데이터 설정하기](#configuring-searchable-data)
    - [Configuring The Model ID](#configuring-the-model-id)
    - [모델 ID 설정하기](#configuring-the-model-id)
    - [Identifying Users](#identifying-users)
    - [사용자 식별](#identifying-users)
- [Database / Collection Engines](#database-and-collection-engines)
- [데이터베이스 / 컬렉션 엔진](#database-and-collection-engines)
    - [Database Engine](#database-engine)
    - [데이터베이스 엔진](#database-engine)
    - [Collection Engine](#collection-engine)
    - [컬렉션 엔진](#collection-engine)
- [Indexing](#indexing)
- [인덱싱](#indexing)
    - [Batch Import](#batch-import)
    - [임포트 일괄처리](#batch-import)
    - [Adding Records](#adding-records)
    - [레코드 추가하기](#adding-records)
    - [Updating Records](#updating-records)
    - [레코드 업데이트 하기](#updating-records)
    - [Removing Records](#removing-records)
    - [레코드 삭제하기](#removing-records)
    - [Pausing Indexing](#pausing-indexing)
    - [인덱싱 일시 멈춤](#pausing-indexing)
    - [Conditionally Searchable Model Instances](#conditionally-searchable-model-instances)
    - [조건에 따른 검색 모델 인스턴스](#conditionally-searchable-model-instances)
- [Searching](#searching)
- [검색하기](#searching)
    - [Where Clauses](#where-clauses)
    - [Where 클로저](#where-clauses)
    - [Pagination](#pagination)
    - [페이지네이션](#pagination)
    - [Soft Deleting](#soft-deleting)
    - [소프트 삭제](#soft-deleting)
    - [Customizing Engine Searches](#customizing-engine-searches)
    - [엔진의 검색 커스터마이징](#customizing-engine-searches)
- [Custom Engines](#custom-engines)
- [커스텀 엔진](#custom-engines)
- [Builder Macros](#builder-macros)
- [빌더 매크로](#builder-macros)

<a name="introduction"></a>
## Introduction
## 시작하기

[Laravel Scout](https://github.com/laravel/scout) provides a simple, driver based solution for adding full-text search to your [Eloquent models](/docs/{{version}}/eloquent). Using model observers, Scout will automatically keep your search indexes in sync with your Eloquent records.

[라라벨 스카우트](https://github.com/laravel/scout)는 [Eloquent 모델](/docs/{{version}}/eloquent)에 full-text 검색 기능을 장착할 수 있는 드라이버 기반의 단순한 솔루션을 제공합니다. 스카우트는 모델 옵저버를 사용하여 엘로퀀트 레코드와 동기화된 검색 인덱스를 자동으로 유지합니다.

Currently, Scout ships with [Algolia](https://www.algolia.com/), [MeiliSearch](https://www.meilisearch.com), and MySQL / PostgreSQL (`database`) drivers. In addition, Scout includes a "collection" driver that is designed for local development usage and does not require any external dependencies or third-party services. Furthermore, writing custom drivers is simple and you are free to extend Scout with your own search implementations.

현재 스카우트는  [Algolia](https://www.algolia.com/)와 [MeiliSearch](https://www.meilisearch.com), MySQL / PostgreSQL (`database`) 드라이버를 내장하고 있습니다. 여기에 더해 추가적인 의존성이 필요하지 않은 로컬 개발환경용 "컬렉션" 드라이버도 제공합니다. 그리고 간단하게 커스텀 드라이버를 작성할 수 있으므로, 자유롭게 스카우트를 확장하여 여러분 자신의 검색 기능을 구현할 수 있습니다. 

<a name="installation"></a>
## Installation
## 설치하기

First, install Scout via the Composer package manager:

먼저, 컴포저 패키지 매니저를 사용하여 스카우트를 설치하십시오:

```shell
composer require laravel/scout
```

After installing Scout, you should publish the Scout configuration file using the `vendor:publish` Artisan command. This command will publish the `scout.php` configuration file to your application's `config` directory:

스카우트를 설치한 다음, `vendor:publish` 아티즌 명령어를 사용하여 스카우트 설정 파일을 퍼블리싱 해야합니다. 이 명령을 실행하면 애플리케이션의 `config` 디렉토리에 `scout.php` 설정파일이 생성됩니다.

```shell
php artisan vendor:publish --provider="Laravel\Scout\ScoutServiceProvider"
```

Finally, add the `Laravel\Scout\Searchable` trait to the model you would like to make searchable. This trait will register a model observer that will automatically keep the model in sync with your search driver:

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
### Driver Prerequisites
### 드라이버 사전 준비사항

<a name="algolia"></a>
#### Algolia
#### Algolia

When using the Algolia driver, you should configure your Algolia `id` and `secret` credentials in your `config/scout.php` configuration file. Once your credentials have been configured, you will also need to install the Algolia PHP SDK via the Composer package manager:

Algolia 드라이버를 사용할 때, Algolia 계정의 `id`와 `secret` 정보를 `config/scout.php` 설정 파일에 입력해야 합니다. 계정 정보를 설정한 다음에는, 컴포저를 통해 Algolia PHP SDK를 설치해야 합니다.

```shell
composer require algolia/algoliasearch-client-php
```

<a name="meilisearch"></a>
#### MeiliSearch
#### MeiliSearch

[MeiliSearch](https://www.meilisearch.com) is a blazingly fast and open source search engine. If you aren't sure how to install MeiliSearch on your local machine, you may use [Laravel Sail](/docs/{{version}}/sail#meilisearch), Laravel's officially supported Docker development environment.

[MeiliSearch](https://www.meilisearch.com)는 엄청 빠른 속도를 자랑하는 오픈소스 검색엔진입니다. 로컬머신에서 MeiliSearch 를 설치하기 어렵다면 라라벨에서 공식적으로 지원하는 도커 개발환경인 [라라벨 Sail](/docs/{{version}}/sail#meilisearch)을 사용할 수 있습니다.

When using the MeiliSearch driver you will need to install the MeiliSearch PHP SDK via the Composer package manager:

MeiliSearch 드라이버를 사용하려면 컴포저 패키지 매니저를 통해서 다음과 같이 MeiliSearch PHP SDK 를 설치해야 합니다. 

```shell
composer require meilisearch/meilisearch-php http-interop/http-factory-guzzle
```

Then, set the `SCOUT_DRIVER` environment variable as well as your MeiliSearch `host` and `key` credentials within your application's `.env` file:

그 다음에 애플리케이션의 `.env` 파일에 다음과 같이 드라이버를 지정하고 MeiliSearch `host`와 `key` 자격증명값을 설정해야합니다. 

```ini
SCOUT_DRIVER=meilisearch
MEILISEARCH_HOST=http://127.0.0.1:7700
MEILISEARCH_KEY=masterKey
```

For more information regarding MeiliSearch, please consult the [MeiliSearch documentation](https://docs.meilisearch.com/learn/getting_started/quick_start.html).

보다 자세한 내용은 [MeiliSearch 공식문서](https://docs.meilisearch.com/learn/getting_started/quick_start.html)를 참고하십시오

In addition, you should ensure that you install a version of `meilisearch/meilisearch-php` that is compatible with your MeiliSearch binary version by reviewing [MeiliSearch's documentation regarding binary compatibility](https://github.com/meilisearch/meilisearch-php#-compatibility-with-meilisearch).

추가로, [MeiliSearch 바이너리 호환성 문서](https://github.com/meilisearch/meilisearch-php#-compatibility-with-meilisearch)를 확인하여 MeiliSearch 바이너리 버전과 호환되는 `meilisearch/meilisearch-php` 버전을 설치했는지 확인하십시오. 

> {note} When upgrading Scout on an application that utilizes MeiliSearch, you should always [review any additional breaking changes](https://github.com/meilisearch/MeiliSearch/releases) to the MeiliSearch service itself.

> {note} MeiliSearch를 사용하는 애플리케이션에서 스카우트를 업그레이드 할 때는 항상 MeiliSearch 서비스의 [추가적인 변경사항 리뷰](https://github.com/meilisearch/MeiliSearch/releases)를 확인하십시오.

<a name="queueing"></a>
### Queueing
### 큐 사용하기

While not strictly required to use Scout, you should strongly consider configuring a [queue driver](/docs/{{version}}/queues) before using the library. Running a queue worker will allow Scout to queue all operations that sync your model information to your search indexes, providing much better response times for your application's web interface.

스카우트를 사용하기 위한 필수 요구사항은 아니지만, 스카우트를 사용하기에 앞서 [큐 드라이버](/docs/{{version}}/queues)의 구성을 고려해 보아야 합니다. 큐를 사용하면 스카우트는 모델 정보와 검색 인덱스를 싱크하는 모든 작업을 큐에 등록할 것이고, 이는 여러분의 웹사이트가 더 나은 응답시간을 제공할 수 있도록 할 것입니다.

Once you have configured a queue driver, set the value of the `queue` option in your `config/scout.php` configuration file to `true`:

일단 큐 드라이버를 구성한 다음, `config/scout.php` 설정 파일의 `queue` 옵션을 `true`로 지정하십시오.

    'queue' => true,

Even when the `queue` option is set to `false`, it's important to remember that some Scout drivers like Algolia and Meilisearch always index records asynchronously. Meaning, even though the index operation has completed within your Laravel application, the search engine itself may not reflect the new and updated records immediately.

`queue` 옵션이 `false`로 설정된 경우에도 Algolia 와 Meilisearch 같은 일부 Scout 드라이버는 레코드를 항상 비동기식으로 인덱싱 한다는 점을 기억하십시오. 즉, 라라벨 애플리케이션 안에서 인덱스 작업이 완료되었더라도 검색 엔진 자체가 새로운 레코드 및 업데이트된 레코드를 반영하는데는 시간이 필요할 수 있습니다.

<a name="configuration"></a>
## Configuration
## 환경 설정

<a name="configuring-model-indexes"></a>
### Configuring Model Indexes
### 모델 인덱스 설정하기

Each Eloquent model is synced with a given search "index", which contains all of the searchable records for that model. In other words, you can think of each index like a MySQL table. By default, each model will be persisted to an index matching the model's typical "table" name. Typically, this is the plural form of the model name; however, you are free to customize the model's index by overriding the `searchableAs` method on the model:

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
### Configuring Searchable Data
### 검색 데이터 설정하기

By default, the entire `toArray` form of a given model will be persisted to its search index. If you would like to customize the data that is synchronized to the search index, you may override the `toSearchableArray` method on the model:

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

<a name="configuring-the-model-id"></a>
### Configuring The Model ID
### 모델 ID 설정하기

By default, Scout will use the primary key of the model as model's unique ID / key that is stored in the search index. If you need to customize this behavior, you may override the `getScoutKey` and the `getScoutKeyName` methods on the model:

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

<a name="identifying-users"></a>
### Identifying Users
### 사용자 식별

Scout also allows you to auto identify users when using [Algolia](https://algolia.com). Associating the authenticated user with search operations may be helpful when viewing your search analytics within Algolia's dashboard. You can enable user identification by defining a `SCOUT_IDENTIFY` environment variable as `true` in your application's `.env` file:

Scout를 사용하면 [Algolia](https://algolia.com)를 사용할 때 사용자를 자동으로 식별 할 수도 있습니다. 인증 된 사용자를 검색 작업과 연결하면 Algolia의 대시 보드 내에서 검색 분석을 볼 때 도움이 될 수 있습니다. 애플리케이션의 `.env` 파일에서 `SCOUT_IDENTIFY`를 `true`로 정의하면 사용자 식별을 활성화 할 수 있습니다.

```ini
SCOUT_IDENTIFY=true
```

Enabling this feature this will also pass the request's IP address and your authenticated user's primary identifier to Algolia so this data is associated with any search request that is made by the user.

이 기능을 활성화하면 요청의 IP 주소와 인증 된 사용자의 기본 식별자도 Algolia에 전달되므로이 데이터는 사용자가 만든 모든 검색 요청과 연결됩니다.

<a name="database-and-collection-engines"></a>
## Database / Collection Engines
## 데이터베이스 / 컬렉션 엔진

<a name="database-engine"></a>
### Database Engine
### 데이터베이스 엔진

> {note} The database engine currently supports MySQL and PostgreSQL.

> {note} 데이터베이스 엔진은 현제 MySQL 및 PostgreSQL을 지원합니다.

If your application interacts with small to medium sized databases or has a light workload, you may find it more convenient to get started with Scout's "database" engine. The database engine will use "where like" clauses and full text indexes when filtering results from your existing database to determine the applicable search results for your query.

애플리케이션이 크지 않은 데이터베이스에서 동작하고, 부하가 가벼운 경우에는 Scout 의 "데이터베이스" 엔진을 사용하는 것이 편할 수 있습니다. 데이터베이스 엔진은 검색 결과를 얻기 위해서 "where like"을 통해서 Full Text 인덱싱을 사용합니다.  

To use the database engine, you may simply set the value of the `SCOUT_DRIVER` environment variable to `database`, or specify the `database` driver directly in your application's `scout` configuration file:

데이터베이스 엔진을 사용하려면 `SCOUT_DRIVER` 환경변수 값을 `database`으로 지정하거나 애플리케이션의 `scout` 설정파일에서 `database` 드라이버로 지정하면 됩니다.

```ini
SCOUT_DRIVER=database
```

Once you have specified the database engine as your preferred driver, you must [configure your searchable data](#configuring-searchable-data). Then, you may start [executing search queries](#searching) against your models. Search engine indexing, such as the indexing needed to seed Algolia or MeiliSearch indexes, is unnecessary when using the database engine.

데이터베이스 엔진을 기본 검색 드라이버로 지정한 뒤에 [검색 가능한 데이터 설정](#configuring-searchable-data)을 완료해야합니다. 그 다음에 모델에 대한 [검색 쿼리 실행](#searching)을 시작할 수 있습니다. 데이터베이스 엔진에서는 Algolia, MeiliSearch와 같은 데이터 인덱싱작업은 필요하지 않습니다.

#### Customizing Database Searching Strategies
#### 데이터베이스 엔진의 검색 방식 커스터마이징

By default, the database engine will execute a "where like" query against every model attribute that you have [configured as searchable](#configuring-searchable-data). However, in some situations, this may result in poor performance. Therefore, the database engine's search strategy can be configured so that some specified columns utilize full text search queries or only use "where like" constraints to search the prefixes of strings (`example%`) instead of searching within the entire string (`%example%`).

기본적으로 데이터베이스 엔진은 [검색 가능하다고 설정된](#configuring-searchable-data) 모델에 대해서 "where like" 쿼리를 실행합니다. 하지만 몇몇의 경우에서는 이로 인해서 성능이 낮게 나올 수 있습니다. 따라서 데이터베이스 엔진에서 사용하는 검색 방식을 커스터마이징 할 수 있습니다. Full text 검색을 해야하는 컬럼의 일부가 (`%example%`) 쿼리를 사용하는 대신 (`example%`)를 사용하는 형식으로 지정이 가능합니다. 

To define this behavior, you may assign PHP attributes to your model's `toSearchableArray` method. Any columns that are not assigned additional search strategy behavior will continue to use the default "where like" strategy:

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

> {note} Before specifying that a column should use full text query constraints, ensure that the column has been assigned a [full text index](/docs/{{version}}/migrations#available-index-types).

> {note} 컬럼이 full text 쿼리 조건을 사용한다고 지정하기 전에 컬럼에 [full text index](/docs/{{version}}/migrations#available-index-types)가 생성되어 있는지 확인하십시오.

<a name="collection-engine"></a>
### Collection Engine
### 컬렉션 엔진

While you are free to use the Algolia or MeiliSearch search engines during local development, you may find it more convenient to get started with the "collection" engine. The collection engine will use "where" clauses and collection filtering on results from your existing database to determine the applicable search results for your query. When using this engine, it is not necessary to "index" your searchable models, as they will simply be retrieved from your local database.

로컬 개발을 진행할 때도 Algolia 또는 MeiliSearch 검색 엔진을 사용할 수 있지만 "컬렉션" 엔진을 사용하는 것이 더 편리할 수 있습니다. 컬렉션 엔진은 "where" 절과 기존 데이터베이스의 결과에 대한 컬렉션 필터링을 사용하여 쿼리에 적용 가능한 검색 결과를 결정합니다. 이 엔진을 사용할 때 검색 가능한 모델을 "인덱싱"할 필요가 없습니다. 로컬 데이터베이스에서 간단히 검색되기 때문입니다.

To use the collection engine, you may simply set the value of the `SCOUT_DRIVER` environment variable to `collection`, or specify the `collection` driver directly in your application's `scout` configuration file:

컬렉션 엔진을 사용하려면 `SCOUT_DRIVER` 환경 변수의 값을 `collection`으로 설정하거나 애플리케이션의 `scout` 설정 파일에서 직접 `collection` 드라이버를 지정할 수 있습니다.

```ini
SCOUT_DRIVER=collection
```

Once you have specified the collection driver as your preferred driver, you may start [executing search queries](#searching) against your models. Search engine indexing, such as the indexing needed to seed Algolia or MeiliSearch indexes, is unnecessary when using the collection engine.

컬렉션 드라이버를 기본 드라이버로 지정했으면 모델에 대해 [검색 쿼리 실행](#searching)을 시작할 수 있습니다. Algolia 또는 MeiliSearch 색인을 시드하는 데 필요한 색인과 같은 검색 엔진 색인은 수집 엔진을 사용할 때 필요하지 않습니다.

#### Differences From Database Engine
#### 데이터베이스 엔진과의 차이점

On first glance, the "database" and "collections" engines are fairly similar. They both interact directly with your database to retrieve search results. However, the collection engine does not utilize full text indexes or `LIKE` clauses to find matching records. Instead, it pulls all possible records and uses Laravel's `Str::is` helper to determine if the search string exists within the model attribute values.

언뜻보기에는, "데이터베이스" 엔진과 "컬렉션" 엔진이 상당히 유사해보입니다. 두개의 엔진 모두 데이터베이스에서 검색 결과를 조회합니다. 하지만 컬렉션 엔진은 일치하는 레코드를 찾기 위해서 Full Text 인덱스나, `LIKE` 절을 사용하지 않습니다. 그 대신에, 모든 레코드를 가져와서 라라벨이 제공하는 `Str::is` 헬퍼 함수를 사용하여 모델의 속성값 안에 원하는 검색 문자열이 포함되어 있는지 확인합니다.

The collection engine is the most portable search engine as it works across all relational databases supported by Laravel (including SQLite and SQL Server); however, it is less efficient than Scout's database engine.

컬렉션 엔진은 라라벨이 지원하는 모든 관계형 데이터베이스(SQLite, SQL Server를 포함)에서 동작하기 때문에 가장 호환성이 좋은 엔진입니다. 하지만 데이터베이스 엔진보다 성능이 좋지 않습니다.

<a name="indexing"></a>
## Indexing
## 인덱싱

<a name="batch-import"></a>
### Batch Import
### 배치작업을 통한 데이터 임포트

If you are installing Scout into an existing project, you may already have database records you need to import into your indexes. Scout provides a `scout:import` Artisan command that you may use to import all of your existing records into your search indexes:

만약 기존에 존재하는 프로젝트에 스카우트를 추가로 설치했다면, 검색 드라이버로 인덱싱할 데이터베이스 데이터 레코드가 존재할 것입니다. 스카우트는 `scout:import` 아티즌 명령어를 사용하여 기존에 존재하는 모든 데이터베이스 레코드를 검색 인덱스로 가져오게 할 수 있습니다. 

```shell
php artisan scout:import "App\Models\Post"
```

The `flush` command may be used to remove all of a model's records from your search indexes:

`flush` 명령어는 검색 인덱스에서 모델의 모든 레코드를 삭제하는데 사용합니다.

```shell
php artisan scout:flush "App\Models\Post"
```

<a name="modifying-the-import-query"></a>
#### Modifying The Import Query
#### 임포트 쿼리 수정하기

If you would like to modify the query that is used to retrieve all of your models for batch importing, you may define a `makeAllSearchableUsing` method on your model. This is a great place to add any eager relationship loading that may be necessary before importing your models:

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
### Adding Records
### 레코드 추가하기

Once you have added the `Laravel\Scout\Searchable` trait to a model, all you need to do is `save` or `create` a model instance and it will automatically be added to your search index. If you have configured Scout to [use queues](#queueing) this operation will be performed in the background by your queue worker:

모델에 `Laravel\Scout\Searchable` trait를 추가했다면, 당신은 모델 인스턴스의 `save` 또는 `create` 메서드를 호출하기만 하면 됩니다. 그러면 자동으로 검색 인덱스에 추가될 것입니다. 만약 스카우트에 [큐 사용](#queueing) 설정을 했다면, 이 작업은 당신의 큐 워커에 의해 백그라운드에서 실행될 것입니다.

    use App\Models\Order;

    $order = new Order;

    // ...

    $order->save();

<a name="adding-records-via-query"></a>
#### Adding Records Via Query
#### 쿼리를 통해 레코드 추가하기

If you would like to add a collection of models to your search index via an Eloquent query, you may chain the `searchable` method onto the Eloquent query. The `searchable` method will [chunk the results](/docs/{{version}}/eloquent#chunking-results) of the query and add the records to your search index. Again, if you have configured Scout to use queues, all of the chunks will be imported in the background by your queue workers:

만약 당신이 엘로퀀트 쿼리를 통해 조회된 모델의 컬렉션을 검색 인덱스에 추가하고 싶다면, 엘로퀀트 쿼리 인스턴스에 `searchable` 메서드를 체이닝하여 호출하면 됩니다. `searchable` 메서드는 쿼리의 [결과를 묶음으로 처리](/docs/{{version}}/eloquent#chunking-results)하여 검색 인덱스에 레코드를 추가할 것입니다. 앞서와 마찬가지로 만약 당신이 스카우트에서 큐를 사용하기로 설정했다면, 모든 묶음은 큐 워커에 의해 백그라운드에서 추가될 것입니다.

    use App\Models\Order;

    Order::where('price', '>', 100)->searchable();

You may also call the `searchable` method on an Eloquent relationship instance:

연관관계에 모델 인스턴스에서도 `searchable` 메서드를 호출할 수 있습니다.

    $user->orders()->searchable();

Or, if you already have a collection of Eloquent models in memory, you may call the `searchable` method on the collection instance to add the model instances to their corresponding index:

또는 메모리에 엘로퀀트 모델 컬렉션을 획득한 다음, 컬렉션 인스턴스에서 `searchable` 메서드를 호출하여 모델에 해당하는 검색 인덱스에서 레코드를 추가 할 수 있습니다.

    $orders->searchable();

> {tip} The `searchable` method can be considered an "upsert" operation. In other words, if the model record is already in your index, it will be updated. If it does not exist in the search index, it will be added to the index.

> {tip} `searchable` 메서드를 "upsert" 작업으로 생각할 수도 있습니다. 다시 말해, 모델 레코드가 이미 인덱스에 존재한다면, 업데이트될 것입니다. 검색 인덱스에 존재하지 않는다면, 인덱스에 추가될 것입니다.

<a name="updating-records"></a>
### Updating Records
### 레코드 업데이트하기

To update a searchable model, you only need to update the model instance's properties and `save` the model to your database. Scout will automatically persist the changes to your search index:

모델을 업데이트하기 위해서는, 모델 인스턴스의 프로퍼티를 업데이트하고, 데이터베이스에 `save` 하기만 하면 됩니다. 스카우트는 자동으로 변경 사항을 검색 인덱스에 적용할 것입니다.

    use App\Models\Order;

    $order = Order::find(1);

    // Update the order...

    $order->save();

You may also invoke the `searchable` method on an Eloquent query instance to update a collection of models. If the models do not exist in your search index, they will be created:

여러분은 모델 컬렉션을 업데이트 하는데 엘로퀀트 쿼리 인스턴스의 `searchable` 메서드를 호출할 수 있습니다. 만약 모델이 검색 인덱스에 존재하지 않는다면, 인덱스는 새로 생성됩니다.

    Order::where('price', '>', 100)->searchable();

If you would like to update the search index records for all of the models in a relationship, you may invoke the `searchable` on the relationship instance:

연관관계에 모델에 대한 검색 인덱스 레코드를 업데이트 하려면 연관관계 인스턴스에서 `searchable` 메서드를 호출하면 됩니다. 

    $user->orders()->searchable();

Or, if you already have a collection of Eloquent models in memory, you may call the `searchable` method on the collection instance to update the model instances in their corresponding index:

또는 메모리에 엘로퀀트 모델 컬렉션을 획득한 다음, 컬렉션 인스턴스에서 `searchable` 메서드를 호출하여 모델에 해당하는 검색 인덱스에서 레코드를 업데이트 할 수 있습니다.

    $orders->searchable();

<a name="removing-records"></a>
### Removing Records
### 레코드 삭제하기

To remove a record from your index you may simply `delete` the model from the database. This may be done even if you are using [soft deleted](/docs/{{version}}/eloquent#soft-deleting) models:

인덱스에서 레코드를 삭제하기 위해서는, 데이터베이스에서 해당 모델의 `delete` 메서드를 호출하면 됩니다. 이 삭제 방법은 [soft deleted](/docs/{{version}}/eloquent#soft-deleting)가 적용된 모델에서도 작동합니다.

    use App\Models\Order;

    $order = Order::find(1);

    $order->delete();

If you do not want to retrieve the model before deleting the record, you may use the `unsearchable` method on an Eloquent query instance:

만약 레코드를 삭제하기 전에 모델을 조회하고 싶지 않다면, 엘로퀀트 쿼리 인스턴스의 `unsearchable` 메서드를 사용할 수 있습니다.

    Order::where('price', '>', 100)->unsearchable();

If you would like to remove the search index records for all of the models in a relationship, you may invoke the `unsearchable` on the relationship instance:

연관관계의 모든 모델에 대한 검색 인덱스 레코드를 삭제하려면 연관관계 인스턴스에서 `unsearchable` 메서드를 호출하면 됩니다.

    $user->orders()->unsearchable();

Or, if you already have a collection of Eloquent models in memory, you may call the `unsearchable` method on the collection instance to remove the model instances from their corresponding index:

또는 메모리에 엘로퀀트 모델 컬렉션을 획득한 다음, 컬렉션 인스턴스에서 `unsearchable` 메서드를 호출하여 모델에 해당하는 검색 인덱스에서 레코드를 삭제할 수 있습니다.

    $orders->unsearchable();

<a name="pausing-indexing"></a>
### Pausing Indexing
### 인덱싱 일시 멈춤

Sometimes you may need to perform a batch of Eloquent operations on a model without syncing the model data to your search index. You may do this using the `withoutSyncingToSearch` method. This method accepts a single closure which will be immediately executed. Any model operations that occur within the closure will not be synced to the model's index:

가끔 검색 인덱스와 모델 데이터를 동기화하지 않으면서 모델에 엘로퀀트 작업을 일괄처리할 필요가 있습니다. 이때 `withoutSyncingToSearch` 메서드를 사용하면 됩니다. 이 메서드는 하나의 클로저를 인자로 받아서 바로 실행합니다. 이 클로저 안에서 실행되는 어떤 모델 작업이든 모델의 인덱스와 동기화 되지 않을 것입니다.

    use App\Models\Order;

    Order::withoutSyncingToSearch(function () {
        // Perform model actions...
    });

<a name="conditionally-searchable-model-instances"></a>
### Conditionally Searchable Model Instances
### 조건에 따른 검색 모델 인스턴스

Sometimes you may need to only make a model searchable under certain conditions. For example, imagine you have `App\Models\Post` model that may be in one of two states: "draft" and "published". You may only want to allow "published" posts to be searchable. To accomplish this, you may define a `shouldBeSearchable` method on your model:

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

The `shouldBeSearchable` method is only applied when manipulating models through the `save` and `create` methods, queries, or relationships. Directly making models or collections searchable using the `searchable` method will override the result of the `shouldBeSearchable` method.

`shouldBeSearchable` 메서드는 `save` 와 `create` 메서드, 쿼리 또는 관계 모델을 통해서 모델을 조작한 경우에만 적용됩니다. `searchable` 메서드를 사용하여 모델 또는 컬렉션을 직접적으로 검색 가능하게 하면, `shouldBeSearchable` 메서드의 결과를 덮어씌게 됩니다.

> {note} The `shouldBeSearchable` method is not applicable when using Scout's "database" engine, as all searchable data is always stored in the database. To achieve similar behavior when using the database engine, you should use [where clauses](#where-clauses) instead.

> {note} `shouldBeSearchable` 메소드는 "데이터베이스" 엔진을 사용할 때는 활용할 수 없습니다. 검색 가능한 모든 데이터가 항상 데이터베이스에 저장되기 때문입니다. 데이터베이스 엔진을 사용할 때 유사항 동작을 수행하려면 [where 절](#where-clauses)을 사용해야합니다.

<a name="searching"></a>
## Searching
## 검색하기

You may begin searching a model using the `search` method. The search method accepts a single string that will be used to search your models. You should then chain the `get` method onto the search query to retrieve the Eloquent models that match the given search query:

`search` 메서드를 사용하여 모델을 검색할 수 있습니다. search 메서드는 모델을 검색할 때 사용될 단일 스트링을 파라미터로 가집니다. 그런 다음, 주어진 검색 쿼리와 일치하는 엘로퀀트 모델을 조회하기 위하여 `get` 메서드를 연결해서 사용해야 합니다.

    use App\Models\Order;

    $orders = Order::search('Star Trek')->get();

Since Scout searches return a collection of Eloquent models, you may even return the results directly from a route or controller and they will automatically be converted to JSON:

스카우트는 엘로퀀트 모델의 모음을 반환하기 때문에, 여러분은 라우트나 컨트롤러에서 결과를 바로 반환할 수 있으며, 그 결과는 JSON으로 자동 변환될 것입니다.

    use App\Models\Order;
    use Illuminate\Http\Request;

    Route::get('/search', function (Request $request) {
        return Order::search($request->search)->get();
    });

If you would like to get the raw search results before they are converted to Eloquent models, you may use the `raw` method:

검색 결과가 Eloquent 모델로 변환되기 전의 상태를 확인하고 싶다면, `raw` 메서드를 사용하면 됩니다.

    $orders = Order::search('Star Trek')->raw();

<a name="custom-indexes"></a>
#### Custom Indexes
#### 커스텀 인덱스

Search queries will typically be performed on the index specified by the model's [`searchableAs`](#configuring-model-indexes) method. However, you may use the `within` method to specify a custom index that should be searched instead:

검색 쿼리는 일반적으로 모델의 [`searchableAs`](#configuring-model-indexes) 메서드에 의한 인덱스가 지정되어 실행됩니다. 대신에 `within` 메서드를 사용하여 검색하고자 하는 커스텀 인덱스를 지정할 수 있습니다.

    $orders = Order::search('Star Trek')
        ->within('tv_shows_popularity_desc')
        ->get();

<a name="where-clauses"></a>
### Where Clauses
### Where 클로저

Scout allows you to add simple "where" clauses to your search queries. Currently, these clauses only support basic numeric equality checks and are primarily useful for scoping search queries by an owner ID:

여러분은 검색 쿼리를 위해 "where" 클로저를 스카우트에 추가할 수 있습니다. 현재로서는, 그 클로저는 오직 기본적인 수식을 지원하며, 주로 고유한 ID에 의해 검색 쿼리를 한정할 때 유용합니다.

    use App\Models\Order;

    $orders = Order::search('Star Trek')->where('user_id', 1)->get();

You may use the `whereIn` method to constrain results against a given set of values:

`whereIn` 메서드를 사용하여 검색결과에 조건을 추가할 수 있습니다.

    $orders = Order::search('Star Trek')->whereIn(
        'status', ['paid', 'open']
    )->get();

Since a search index is not a relational database, more advanced "where" clauses are not currently supported.

<a name="pagination"></a>
### Pagination
### 페이지네이션

In addition to retrieving a collection of models, you may paginate your search results using the `paginate` method. This method will return an `Illuminate\Pagination\LengthAwarePaginator` instance just as if you had [paginated a traditional Eloquent query](/docs/{{version}}/pagination):

모델 컬렉션의 조회에 더해, `paginate` 메서드를 사용하여 검색결과를 페이징할 수 있습니다. [엘로퀀트 쿼리에서 페이징](/docs/{{version}}/pagination)을 했던 것처럼, 이 메서드는 `Illuminate\Pagination\LengthAwarePaginator` 인스턴스를 반환합니다. 

    use App\Models\Order;

    $orders = Order::search('Star Trek')->paginate();

You may specify how many models to retrieve per page by passing the amount as the first argument to the `paginate` method:

`paginate` 메서드의 첫번째 파라미터를 사용하여 한 페이지에 검색할 모델의 수량을 지정할 수도 있습니다.

    $orders = Order::search('Star Trek')->paginate(15);

Once you have retrieved the results, you may display the results and render the page links using [Blade](/docs/{{version}}/blade) just as if you had paginated a traditional Eloquent query:

결과를 검색한 다음에, 여러분은 엘로퀀트 쿼리에서 페이징했던 것처럼 검색 결과를 출력하고 [블레이드](/docs/{{version}}/blade)를 사용하여 페이지 링크를 출력할 수 있습니다.

```html
<div class="container">
    @foreach ($orders as $order)
        {{ $order->price }}
    @endforeach
</div>

{{ $orders->links() }}
```

Of course, if you would like to retrieve the pagination results as JSON, you may return the paginator instance directly from a route or controller:

물론, 페이징이 적용된 결과를 JSON으로 조회하려면, 라우트나 컨트롤러에서 직접 Paginator 인스턴스를 반환할수도 있습니다

    use App\Models\Order;
    use Illuminate\Http\Request;

    Route::get('/orders', function (Request $request) {
        return Order::search($request->input('query'))->paginate(15);
    });

<a name="soft-deleting"></a>
### Soft Deleting
### 소프트 삭제

If your indexed models are [soft deleting](/docs/{{version}}/eloquent#soft-deleting) and you need to search your soft deleted models, set the `soft_delete` option of the `config/scout.php` configuration file to `true`:

인덱싱되는 모델이 [소프트 삭제](/docs/{{version}}/eloquent#soft-deleting)된 상태이고, 소프트 삭제된 모델을 검색할 필요가 있다면, `config/scout.php` 설정 파일의 `soft_delete` 옵션을 `true`로 지정하면 됩니다.

    'soft_delete' => true,

When this configuration option is `true`, Scout will not remove soft deleted models from the search index. Instead, it will set a hidden `__soft_deleted` attribute on the indexed record. Then, you may use the `withTrashed` or `onlyTrashed` methods to retrieve the soft deleted records when searching:

이 설정 값이 `true` 인 경우, Scout-스카우트는 소프트 삭제된 모델을 검색 인덱스에서 삭제 하지 않습니다. 대신에, 인덱스된 레코드에 `__soft_deleted` 라는 숨겨진 속성을 설정합니다. 그러면 검색할 때 `withTrashed` 또는 `onlyTrashed` 메서드를 사용하여 소프트 삭제된 레코드를 조회할 수 있습니다.

    use App\Models\Order;

    // Include trashed records when retrieving results...
    $orders = Order::search('Star Trek')->withTrashed()->get();

    // Only include trashed records when retrieving results...
    $orders = Order::search('Star Trek')->onlyTrashed()->get();

> {tip} When a soft deleted model is permanently deleted using `forceDelete`, Scout will remove it from the search index automatically.

> {tip} `forceDelete` 메서드를 사용하여 소프트 삭제된 모델을 완전히 삭제할 때, Scout-스카우트는 검색 인덱스에서 자동으로 이를 제거합니다.

<a name="customizing-engine-searches"></a>
### Customizing Engine Searches
### 엔진의 검색 커스터마이징

If you need to perform advanced customization of the search behavior of an engine you may pass a closure as the second argument to the `search` method. For example, you could use this callback to add geo-location data to your search options before the search query is passed to Algolia:

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
#### Customizing The Eloquent Results Query
#### 엘로퀀트 결과 커리 커스터마이징 하기

After Scout retrieves a list of matching Eloquent models from your application's search engine, Eloquent is used to retrieve all of the matching models by their primary keys. You may customize this query by invoking the `query` method. The `query` method accepts a closure that will receive the Eloquent query builder instance as an argument:

Scout이 애플리케이션의 검색 엔진에서 매칭되는 엘로퀀트 모델 목록을 조회한 다음에, 엘로퀀트는 검색에 기본키를 사용합니다. `query` 메소드를 사용하면 이 쿼리를 커스터마이징 할 수 있습니다. `query` 메소드는 엘로퀀트 쿼리 빌더 인스턴스를 인자로 받는 클로저를 넘겨받아야 합니다.

```php
use App\Models\Order;

$orders = Order::search('Star Trek')
    ->query(fn ($query) => $query->with('invoices'))
    ->get();
```

Since this callback is invoked after the relevant models have already been retrieved from your application's search engine, the `query` method should not be used for "filtering" results. Instead, you should use [Scout where clauses](#where-clauses).

이 콜백은 관련 모델이 애플리케이션의 검색엔진에서 조회된 이후에 호출되기 때문에, `query` 메소드에는 결과를 "필터링" 하는용도로 사용하면 안됩니다. 그 대신에 [Scout where 절](#where-clauses)을 사용하십시오.

<a name="custom-engines"></a>
## Custom Engines
## 커스텀 엔진

<a name="writing-the-engine"></a>
#### Writing The Engine
#### 엔진 구현하기

If one of the built-in Scout search engines doesn't fit your needs, you may write your own custom engine and register it with Scout. Your engine should extend the `Laravel\Scout\Engines\Engine` abstract class. This abstract class contains eight methods your custom engine must implement:

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

You may find it helpful to review the implementations of these methods on the `Laravel\Scout\Engines\AlgoliaEngine` class. This class will provide you with a good starting point for learning how to implement each of these methods in your own engine.

`Laravel\Scout\Engines\AlgoliaEngine` 클래스에 구현된 그 메서드들을 살펴보는 것이 여러분에게 도움이 될 것입니다. 이 클래스는 여러분만의 엔진에 그 메서드를을 어떻게 구현해야 할지 배울 수 있는 좋은 출발점이 될 것입니다.

<a name="registering-the-engine"></a>
#### Registering The Engine
#### 엔진 등록하기

Once you have written your custom engine, you may register it with Scout using the `extend` method of the Scout engine manager. Scout's engine manager may be resolved from the Laravel service container. You should call the `extend` method from the `boot` method of your `App\Providers\AppServiceProvider` class or any other service provider used by your application:

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

Once your engine has been registered, you may specify it as your default Scout `driver` in your application's `config/scout.php` configuration file:

여러분의 엔진을 등록한 즉시, 애플리케이션의 `config/scout.php` 설정 파일의 `driver`를 변경하여 스카우트의 기본 드라이버를 그것으로 지정하십시오.

    'driver' => 'mysql',

<a name="builder-macros"></a>
## Builder Macros
## 빌더 매크로

If you would like to define a custom Scout search builder method, you may use the `macro` method on the `Laravel\Scout\Builder` class. Typically, "macros" should be defined within a [service provider's](/docs/{{version}}/providers) `boot` method:

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

The `macro` function accepts a macro name as its first argument and a closure as its second argument. The macro's closure will be executed when calling the macro name from a `Laravel\Scout\Builder` implementation:

`macro` 함수는 첫 번째 인수로 매크로의 이름을 받아들입니다. 두 번째 인수는 클로저입니다. 매크로의 클로저는 `Laravel\Scout\Builder` 구현에서 매크로 이름을 호출 할 때 실행됩니다.

    use App\Models\Order;

    Order::search('Star Trek')->count();
