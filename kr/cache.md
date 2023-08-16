# Cache
# 캐시

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Configuration](#configuration)
- [설정하기](#configuration)
    - [Driver Prerequisites](#driver-prerequisites)
    - [드라이버 사전 준비사항](#driver-prerequisites)
- [Cache Usage](#cache-usage)
- [캐시 사용법](#cache-usage)
    - [Obtaining A Cache Instance](#obtaining-a-cache-instance)
    - [캐시 인스턴스 획득하기](#obtaining-a-cache-instance)
    - [Retrieving Items From The Cache](#retrieving-items-from-the-cache)
    - [캐시에서 아이템 찾기](#retrieving-items-from-the-cache)
    - [Storing Items In The Cache](#storing-items-in-the-cache)
    - [캐시에 아이템 저장하기](#storing-items-in-the-cache)
    - [Removing Items From The Cache](#removing-items-from-the-cache)
    - [캐시에서 아이템 삭제하기](#removing-items-from-the-cache)
    - [The Cache Helper](#the-cache-helper)
    - [캐시 헬퍼 함수](#the-cache-helper)
- [Cache Tags](#cache-tags)
- [캐시 태그](#cache-tags)
    - [Storing Tagged Cache Items](#storing-tagged-cache-items)
    - [태그가 추가된 캐시 아이템 저장하기](#storing-tagged-cache-items)
    - [Accessing Tagged Cache Items](#accessing-tagged-cache-items)
    - [태그로 캐시 아이템 엑세스하기](#accessing-tagged-cache-items)
    - [Removing Tagged Cache Items](#removing-tagged-cache-items)
    - [태그가 추가된 캐시 아이템 삭제하기](#removing-tagged-cache-items)
    - [Pruning Stale Cache Tags](#pruning-stale-cache-tags)
    - [오래된 캐시 태그 정리](#pruning-stale-cache-tags)
- [Atomic Locks](#atomic-locks)
- [원자 잠금장치](#atomic-locks)
    - [Driver Prerequisites](#lock-driver-prerequisites)
    - [드라이버 전제 조건](#lock-driver-prerequisites)
    - [Managing Locks](#managing-locks)
    - [잠금 관리](#managing-locks)
    - [Managing Locks Across Processes](#managing-locks-across-processes)
    - [프로세스 간 잠금 관리](#managing-locks-across-processes)
- [Adding Custom Cache Drivers](#adding-custom-cache-drivers)
- [사용자 정의 캐시 드라이버 추가하기](#adding-custom-cache-drivers)
    - [Writing The Driver](#writing-the-driver)
    - [드라이버 작성하기](#writing-the-driver)
    - [Registering The Driver](#registering-the-driver)
    - [드라이버 등록하기](#registering-the-driver)
- [Events](#events)
- [이벤트](#events)

<a name="introduction"></a>
## Introduction
## 시작하기

Some of the data retrieval or processing tasks performed by your application could be CPU intensive or take several seconds to complete. When this is the case, it is common to cache the retrieved data for a time so it can be retrieved quickly on subsequent requests for the same data. The cached data is usually stored in a very fast data store such as [Memcached](https://memcached.org) or [Redis](https://redis.io).

애플리케이션에서 수행하는 일부 데이터 검색 또는 처리 작업은 CPU를 많이 사용하거나 완료하는 데 몇 초가 걸릴 수 있습니다. 이 경우 동일한 데이터에 대한 후속 요청에서 빠르게 검색할 수 있도록 검색된 데이터를 일정 시간 캐시하는 것이 일반적입니다. 캐시된 데이터는 일반적으로 [Memcached](https://memcached.org) 또는 [Redis](https://redis.io)와 같은 매우 빠른 데이터 저장소에 저장됩니다.

Thankfully, Laravel provides an expressive, unified API for various cache backends, allowing you to take advantage of their blazing fast data retrieval and speed up your web application.

고맙게도 라라벨은 다양한 캐시 백엔드를 위한 표현력이 뛰어난 통합 API를 제공하므로 사용자가 초고속 데이터 검색을 활용하고 웹 애플리케이션의 속도를 높일 수 있습니다.

<a name="configuration"></a>
## Configuration
## 설정하기

Your application's cache configuration file is located at `config/cache.php`. In this file, you may specify which cache driver you would like to be used by default throughout your application. Laravel supports popular caching backends like [Memcached](https://memcached.org), [Redis](https://redis.io), [DynamoDB](https://aws.amazon.com/dynamodb), and relational databases out of the box. In addition, a file based cache driver is available, while `array` and "null" cache drivers provide convenient cache backends for your automated tests.

애플리케이션의 캐시 설정 파일은 `config/cache.php`에 있습니다. 이 파일에서 애플리케이션 전체에서 기본적으로 사용하려는 캐시 드라이버를 지정할 수 있습니다. 라라벨은 [Memcached](https://memcached.org), [Redis](https://redis.io), [DynamoDB](https://aws.amazon.com/dynamodb) 및 관계형 데이터베이스와 같은 인기 있는 캐싱 백엔드를 바로 지원합니다. 또한 파일 기반 캐시 드라이버를 사용할 수 있으며 `array` 및 "null" 캐시 드라이버같은 자동화된 테스트를 위한 편리한 캐시 백엔드를 제공합니다.

The cache configuration file also contains various other options, which are documented within the file, so make sure to read over these options. By default, Laravel is configured to use the `file` cache driver, which stores the serialized, cached objects on the server's filesystem. For larger applications, it is recommended that you use a more robust driver such as Memcached or Redis. You may even configure multiple cache configurations for the same driver.

캐시 설정 파일에는 파일 내에 문서화되어 있는 다양한 다른 옵션도 포함되어 있으므로 이러한 옵션을 자세히 읽어 보십시오. 기본적으로 라라벨은 직렬화되고 캐시된 객체를 서버의 파일 시스템에 저장하는 `file` 캐시 드라이버를 사용하도록 설정되어 있습니다. 더 큰 애플리케이션의 경우 Memcached 또는 Redis와 같은 보다 강력한 드라이버를 사용하는 것이 좋습니다. 동일한 드라이버에 대해 여러 캐시 설정을 구성할 수도 있습니다.

<a name="driver-prerequisites"></a>
### Driver Prerequisites
### 드라이버 사전 준비사항

<a name="prerequisites-database"></a>
#### Database
#### 데이터베이스

When using the `database` cache driver, you will need to set up a table to contain the cache items. You'll find an example `Schema` declaration for the table below:

`database` 캐시 드라이버를 사용할 때에는, 캐시 정보를 담아둘 테이블을 설정할 필요가 있습니다. 다음과 같이 테이블을 설정하는 `Scheme`를 확인하십시오.

    Schema::create('cache', function (Blueprint $table) {
        $table->string('key')->unique();
        $table->text('value');
        $table->integer('expiration');
    });

> **Note**  
> You may also use the `php artisan cache:table` Artisan command to generate a migration with the proper schema.

> **Note**  
> 적절한 스키마 마이그레이션을 생성하기 위해 `php artisan cache:table` Artisan 명령어를 사용할 수도 있습니다.

<a name="memcached"></a>
#### Memcached
#### Memcached

Using the Memcached driver requires the [Memcached PECL package](https://pecl.php.net/package/memcached) to be installed. You may list all of your Memcached servers in the `config/cache.php` configuration file. This file already contains a `memcached.servers` entry to get you started:

Memcached 드라이버를 사용하려면 [Memcached PECL 패키지](https://pecl.php.net/package/memcached) 가 설치되어 있어야 합니다. `config/cache.php` 설정 파일안에서 Memcache 서버 목록을 나열하면 됩니다. `config/cache.php` 설정 파일에 모든 Memcached 서버를 나열할 수 있습니다. 이 파일에는 시작하기 위한 `memcached.servers` 항목이 이미 포함되어 있습니다.

    'memcached' => [
        'servers' => [
            [
                'host' => env('MEMCACHED_HOST', '127.0.0.1'),
                'port' => env('MEMCACHED_PORT', 11211),
                'weight' => 100,
            ],
        ],
    ],

If needed, you may set the `host` option to a UNIX socket path. If you do this, the `port` option should be set to `0`:

필요한 경우 `host` 옵션을 UNIX 소켓 경로로 설정할 수 있습니다. 이렇게 하면 `port` 옵션을 `0`으로 설정해야 합니다.

    'memcached' => [
        [
            'host' => '/var/run/memcached/memcached.sock',
            'port' => 0,
            'weight' => 100
        ],
    ],

<a name="redis"></a>
#### Redis
#### Redis

Before using a Redis cache with Laravel, you will need to either install the PhpRedis PHP extension via PECL or install the `predis/predis` package (~1.0) via Composer. [Laravel Sail](/docs/{{version}}/sail) already includes this extension. In addition, official Laravel deployment platforms such as [Laravel Forge](https://forge.laravel.com) and [Laravel Vapor](https://vapor.laravel.com) have the PhpRedis extension installed by default.

라라벨과 함께 Redis 캐시를 사용하기 전에 PECL을 통해 PhpRedis PHP 확장을 설치하거나 Composer를 통해 `predis/predis` 패키지(~1.0)를 설치해야 합니다. [Laravel Sail](/docs/{{version}}/sail)에는 이미 이 확장이 포함되어 있습니다. 또한 [Laravel Forge](https://forge.laravel.com) 및 [Laravel Vapor](https://vapor.laravel.com)와 같은 공식 라라벨 배포 플랫폼에는 기본적으로 PhpRedis 확장이 설치되어 있습니다.

For more information on configuring Redis, consult its [Laravel documentation page](/docs/{{version}}/redis#configuration).

Redis 설정에 대한 자세한 내용은 [Laravel 문서 페이지](/docs/{{version}}/redis#configuration)를 참조하세요.

<a name="dynamodb"></a>
#### DynamoDB
#### DynamoDB

Before using the [DynamoDB](https://aws.amazon.com/dynamodb) cache driver, you must create a DynamoDB table to store all of the cached data. Typically, this table should be named `cache`. However, you should name the table based on the value of the `stores.dynamodb.table` configuration value within your application's `cache` configuration file.

[DynamoDB](https://aws.amazon.comdynamodb) 캐시 드라이버를 사용하기 전에, 캐시된 모든 데이터를 저장할 DynamoDB 테이블을 생성해야 합니다. 일반적으로 이 테이블의 이름은 `cache`로 지정해야 합니다. 그러나 애플리케이션의 `cache` 설정 파일에 있는 `stores.dynamodb.table` 설정 값에 따라 테이블 이름을 지정해야 합니다.

This table should also have a string partition key with a name that corresponds to the value of the `stores.dynamodb.attributes.key` configuration item within your application's `cache` configuration file. By default, the partition key should be named `key`.

이 테이블에는 애플리케이션의 `cache` 설정 파일에 있는 `stores.dynamodb.attributes.key` 설정 항목의 값에 해당하는 이름을 가진 문자열 파티션 키도 있어야 합니다. 기본적으로 파티션 키는 `key`로 이름을 지정해야 합니다.

<a name="cache-usage"></a>
## Cache Usage
## 캐시 사용법

<a name="obtaining-a-cache-instance"></a>
### Obtaining A Cache Instance
### 캐시 인스턴스 획득하기

To obtain a cache store instance, you may use the `Cache` facade, which is what we will use throughout this documentation. The `Cache` facade provides convenient, terse access to the underlying implementations of the Laravel cache contracts:

캐시 저장소 인스턴스를 얻으려면 이 문서 전체에서 사용할 `Cache` 파사드를 사용할 수 있습니다. `Cache` 파사드는 라라벨 캐시 규약-contract의 기본 구현에 대한 편리하고 간결한 액세스를 제공합니다.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Support\Facades\Cache;

    class UserController extends Controller
    {
        /**
         * Show a list of all users of the application.
         */
        public function index(): array
        {
            $value = Cache::get('key');

            return [
                // ...
            ];
        }
    }

<a name="accessing-multiple-cache-stores"></a>
#### Accessing Multiple Cache Stores
#### 여러 개의 캐시 Store 사용하기

Using the `Cache` facade, you may access various cache stores via the `store` method. The key passed to the `store` method should correspond to one of the stores listed in the `stores` configuration array in your `cache` configuration file:

`Cache` 파사드를 사용하면서 `store` 메소드를 통해서 여러 개의 캐시 Store 에 엑세스 할 수 있습니다. `store` 메소드에 전달되는 키는 `cache` 설정 파일에서 `stores` 배열에 들어 있는 store 중 하나여야 합니다.

    $value = Cache::store('file')->get('foo');

    Cache::store('redis')->put('bar', 'baz', 600); // 10 Minutes

<a name="retrieving-items-from-the-cache"></a>
### Retrieving Items From The Cache
### 캐시에서 아이템 조회하기

The `Cache` facade's `get` method is used to retrieve items from the cache. If the item does not exist in the cache, `null` will be returned. If you wish, you may pass a second argument to the `get` method specifying the default value you wish to be returned if the item doesn't exist:

`Cache` 파사드의 `get` 메소드는 캐시에서 항목을 검색하는 데 사용됩니다. 캐시에 항목이 없으면 `null`이 반환됩니다. 원하는 경우 항목이 존재하지 않는 경우 반환하려는 기본값을 지정하는 두 번째 인수를 `get` 메서드에 전달할 수 있습니다.

    $value = Cache::get('key');

    $value = Cache::get('key', 'default');

You may even pass a closure as the default value. The result of the closure will be returned if the specified item does not exist in the cache. Passing a closure allows you to defer the retrieval of default values from a database or other external service:

클로저를 기본값으로 전달할 수도 있습니다. 지정된 항목이 캐시에 없으면 클로저 결과가 반환됩니다. 클로저를 전달하면 데이터베이스 또는 기타 외부 서비스에서 기본값 검색을 연기할 수 있습니다.

    $value = Cache::get('key', function () {
        return DB::table(/* ... */)->get();
    });

<a name="checking-for-item-existence"></a>
#### Checking For Item Existence
#### 아이템이 존재하는지 확인하기

The `has` method may be used to determine if an item exists in the cache. This method will also return `false` if the item exists but its value is `null`:

`has` 메서드는 캐시에 항목이 있는지 확인하는 데 사용할 수 있습니다. 이 메서드는 항목이 존재하지만 값이 `null`인 경우에도 `false`를 반환합니다.

    if (Cache::has('key')) {
        // ...
    }

<a name="incrementing-decrementing-values"></a>
#### Incrementing / Decrementing Values
#### 증가 / 감소

The `increment` and `decrement` methods may be used to adjust the value of integer items in the cache. Both of these methods accept an optional second argument indicating the amount by which to increment or decrement the item's value:

`increment` 와 `decrement` 메소드는 캐시에 들어 있는 정수형 아이템의 값을 변경하는데 사용합니다. 두 메소드는 모두 해당 아이템의 값을 얼마나 증가 또는 감소 시킬지 결정하는 두 번째 인자를 선택적으로 전달할 수 있습니다.

    // Initialize the value if it does not exist...
    Cache::add('key', 0, now()->addHours(4));

    // Increment or decrement the value...
    Cache::increment('key');
    Cache::increment('key', $amount);
    Cache::decrement('key');
    Cache::decrement('key', $amount);

<a name="retrieve-store"></a>
#### Retrieve & Store
#### 조회 & 저장

Sometimes you may wish to retrieve an item from the cache, but also store a default value if the requested item doesn't exist. For example, you may wish to retrieve all users from the cache or, if they don't exist, retrieve them from the database and add them to the cache. You may do this using the `Cache::remember` method:

때로는 캐시로부터 아이템을 찾는 것 뿐만 아니라, 찾으려는 아이템이 존재하지 않는 경우 기본값을 저장하고자 할 수도 있습니다. 예를 들어 캐시에서 모든 사용자를 찾으려 할 때 아무 것도 없다면, 데이터베이스로부터 정보를 획득하여 캐시에 추가하고자 할 수도 있습니다. 이 경우에 `Cache::remember` 메소드를 사용하면 됩니다.

    $value = Cache::remember('users', $seconds, function () {
        return DB::table('users')->get();
    });

If the item does not exist in the cache, the closure passed to the `remember` method will be executed and its result will be placed in the cache.

캐시에 항목이 없으면 `remember` 메서드에 전달된 클로저가 실행되고 그 결과가 캐시에 저장됩니다.

You may use the `rememberForever` method to retrieve an item from the cache or store it forever if it does not exist:

`rememberForever` 메소드를 사용하여 캐시에서 항목을 검색하거나 존재하지 않는 경우 영원히 저장할 수 있습니다.

    $value = Cache::rememberForever('users', function () {
        return DB::table('users')->get();
    });

<a name="retrieve-delete"></a>
#### Retrieve & Delete
#### 조회 & 삭제하기

If you need to retrieve an item from the cache and then delete the item, you may use the `pull` method. Like the `get` method, `null` will be returned if the item does not exist in the cache:

캐시에서 아이템을 찾은 뒤에 해당 아이템을 삭제할 필요가 있는 경우에는 `pull` 메소드를 사용하면 됩니다. `get` 메소드와 마찬가지로, 캐시에서 아이템이 존재하지 않는 경우에는 `null` 이 반환됩니다.

    $value = Cache::pull('key');

<a name="storing-items-in-the-cache"></a>
### Storing Items In The Cache
### 캐시에 아이템 저장하기

You may use the `put` method on the `Cache` facade to store items in the cache:

`Cache` 파사드의 `put` 메소드를 사용하여 캐시에 아이템을 저장할 수 있습니다.

    Cache::put('key', 'value', $seconds = 10);

If the storage time is not passed to the `put` method, the item will be stored indefinitely:

저장 시간이 `put` 메소드에 전달되지 않으면 그 아이템은 무기한 저장 될 것입니다.

    Cache::put('key', 'value');

Instead of passing the number of seconds as an integer, you may also pass a `DateTime` instance representing the desired expiration time of the cached item:

초-second 수를 정수로 전달하는 대신 캐시된 항목의 원하는 만료 시간을 나타내는 `DateTime` 인스턴스를 전달할 수도 있습니다.

    Cache::put('key', 'value', now()->addMinutes(10));

<a name="store-if-not-present"></a>
#### Store If Not Present
#### 존재하지 않으면 저장하기

The `add` method will only add the item to the cache if it does not already exist in the cache store. The method will return `true` if the item is actually added to the cache. Otherwise, the method will return `false`. The `add` method is an atomic operation:

`add` 메서드는 캐시 저장소에 항목이 아직 없는 경우에만 캐시에 항목을 추가합니다. 항목이 실제로 캐시에 추가되면 메서드는 `true`를 반환합니다. 그렇지 않으면 메서드는 `false`을 반환합니다. `add` 메서드는 원자적 연산입니다.

    Cache::add('key', 'value', $seconds);

<a name="storing-items-forever"></a>
#### Storing Items Forever
#### 아이템들을 영구적으로 저장하기

The `forever` method may be used to store an item in the cache permanently. Since these items will not expire, they must be manually removed from the cache using the `forget` method:

`forever` 메소드는 캐시에 아이템을 영구적으로 저장하고자 할 때 사용됩니다. 이 아이템들은 자동으로 만료되지 않기 때문에, `forget` 메소드를 사용하여 캐시에서 수동으로 삭제해주어야만 합니다.

    Cache::forever('key', 'value');

> **Note**  
> If you are using the Memcached driver, items that are stored "forever" may be removed when the cache reaches its size limit.

> **Note**  
> 만약 Memcached 드라이버를 사용중이라면, "영구적으로" 저장된 아이템들은 캐시의 사이즈 제한에 도달한 경우 제거될 것입니다.

<a name="removing-items-from-the-cache"></a>
### Removing Items From The Cache
### 캐시에서 아이템 삭제하기

You may remove items from the cache using the `forget` method:

`forget` 메소드를 사용하여 캐시에서 아이템을 삭제할 수 있습니다.

    Cache::forget('key');

You may also remove items by providing a zero or negative number of expiration seconds:

0 또는 음수로 된 만료 시간(초)을 입력하여 항목을 제거할 수도 있습니다.

    Cache::put('key', 'value', 0);

    Cache::put('key', 'value', -5);

You may clear the entire cache using the `flush` method:

`flush` 메소드를 사용하면 전체 캐시를 삭제합니다.

    Cache::flush();

> **Warning**  
> Flushing the cache does not respect your configured cache "prefix" and will remove all entries from the cache. Consider this carefully when clearing a cache which is shared by other applications.

> **Warning**  
> 캐시를 플러시하면 설정된 캐시 "접두사"가 적용되지 않으며, 캐시에서 모든 항목이 제거됩니다. 다른 애플리케이션에서 공유하는 캐시를 지울 때 이 점을 주의 깊게 고려하십시오.

<a name="the-cache-helper"></a>
### The Cache Helper
### 캐시 헬퍼

In addition to using the `Cache` facade, you may also use the global `cache` function to retrieve and store data via the cache. When the `cache` function is called with a single, string argument, it will return the value of the given key:

`Cache` 파사드를 사용하는 것 외에도 전역 `cache` 함수를 사용하여 캐시를 통해 데이터를 검색하고 저장할 수도 있습니다. `cache` 함수가 단일 문자열 인수로 호출되면 지정된 키의 값을 반환합니다.

    $value = cache('key');

If you provide an array of key / value pairs and an expiration time to the function, it will store values in the cache for the specified duration:

키 / 값 쌍의 배열과 만료 시간을 함수에 제공하면 지정된 기간 동안 값을 캐시에 저장합니다.

    cache(['key' => 'value'], $seconds);

    cache(['key' => 'value'], now()->addMinutes(10));

When the `cache` function is called without any arguments, it returns an instance of the `Illuminate\Contracts\Cache\Factory` implementation, allowing you to call other caching methods:

인수없이 `cache` 함수를 호출하면 `Illuminate\Contracts\Cache\Factory`를 구현한 인스턴스를 반환하여 다른 캐싱 메서드를 호출 할 수 있습니다.

    cache()->remember('users', $seconds, function () {
        return DB::table('users')->get();
    });

> **Note**  
> When testing call to the global `cache` function, you may use the `Cache::shouldReceive` method just as if you were [testing the facade](/docs/{{version}}/mocking#mocking-facades).

> **Note**  
> 전역 `cache` 함수 호출을 테스트 할 때 [파사드 테스트](/docs/{{version}}/mocking#mocking-facades )처럼 `Cache::shouldReceive` 메소드를 사용할 수 있습니다.

<a name="cache-tags"></a>
## Cache Tags
## 캐시 태그

> **Warning**  
> Cache tags are not supported when using the `file`, `dynamodb`, or `database` cache drivers. Furthermore, when using multiple tags with caches that are stored "forever", performance will be best with a driver such as `memcached`, which automatically purges stale records.

> **Warning**  
> `file`, `dynamodb` 또는 `database` 캐시 드라이버를 사용하는 경우 캐시 태그가 지원되지 않습니다. 또한 "forever"로 저장된 캐시와 함께 여러 태그를 사용할 때 만료된 데이터를 자동으로 제거하는 `memcached`와 같은 드라이버를 사용하면 성능이 가장 좋습니다.

<a name="storing-tagged-cache-items"></a>
### Storing Tagged Cache Items
### 태그가 지정된 캐시 항목 저장

Cache tags allow you to tag related items in the cache and then flush all cached values that have been assigned a given tag. You may access a tagged cache by passing in an ordered array of tag names. For example, let's access a tagged cache and `put` a value into the cache:

캐시 태그를 사용하면 캐시의 관련 항목에 태그를 지정한 다음 지정된 태그가 할당 된 모든 캐시 된 값을 플러시 할 수 있습니다. 태그 이름의 정렬 된 배열을 전달하여 태그가 지정된 캐시에 액세스 할 수 있습니다. 예를 들어, 태그가 지정된 캐시에 액세스하고 캐시의 `put`값에 액세스 해 보겠습니다.

    Cache::tags(['people', 'artists'])->put('John', $john, $seconds);

    Cache::tags(['people', 'authors'])->put('Anne', $anne, $seconds);

<a name="accessing-tagged-cache-items"></a>
### Accessing Tagged Cache Items
### 태그가 지정된 캐시 항목 액세스

Items stored via tags may not be accessed without also providing the tags that were used to store the value. To retrieve a tagged cache item, pass the same ordered list of tags to the `tags` method and then call the `get` method with the key you wish to retrieve:

태그를 통해 저장된 항목은 값을 저장하는 데 사용된 태그를 제공하지 않으면 액세스할 수 없습니다. 태그가 지정된 캐시 항목을 검색하려면 동일한 순서의 태그 목록을 `tags` 메서드에 전달한 다음 검색하려는 키로 `get` 메서드를 호출합니다.

    $john = Cache::tags(['people', 'artists'])->get('John');

    $anne = Cache::tags(['people', 'authors'])->get('Anne');

<a name="removing-tagged-cache-items"></a>
### Removing Tagged Cache Items
### 태그가 지정된 캐시 항목 제거

You may flush all items that are assigned a tag or list of tags. For example, this statement would remove all caches tagged with either `people`, `authors`, or both. So, both `Anne` and `John` would be removed from the cache:

태그 또는 태그 목록이 할당 된 모든 항목을 플러시 할 수 있습니다. 예를 들어, 이 문은 `people`, `authors` 또는 둘 다로 태그가 지정된 모든 캐시를 제거합니다. 따라서 `Anne`과` John`은 모두 캐시에서 제거됩니다.

    Cache::tags(['people', 'authors'])->flush();

In contrast, this statement would remove only cached values tagged with `authors`, so `Anne` would be removed, but not `John`:

대조적으로 이 명령문은 `authors`로 태그가 지정된 캐시된 값만 제거하므로 `Anne`은 제거되지만 `John`은 제거되지 않습니다.

    Cache::tags('authors')->flush();

<a name="pruning-stale-cache-tags"></a>
### Pruning Stale Cache Tags
### 오래된 캐시 태그 정리

> **Warning**  
> Pruning stale cache tags is only necessary when using Redis as your application's cache driver.

> **Warning**  
> 오래된 캐시 태그 정리는 애플리케이션의 캐시 드라이버로 Redis를 사용할 때만 필요합니다.

In order to properly prune stale cache tag entries when using the Redis cache driver, Laravel's `cache:prune-stale-tags` Artisan command should be [scheduled](/docs/{{version}}/scheduling) in your application's `App\Console\Kernel` class:

Redis 캐시 드라이버를 사용할 때 오래된 캐시 태그 항목을 적절하게 제거하려면 Laravel의 `cache:prune-stale-tags` 아티즌 명령이 애플리케이션의 `App\Console\Kernel` 클래스에서 [스케줄링](/docs/{{version}}/scheduling)되어야 합니다.

    $schedule->command('cache:prune-stale-tags')->hourly();

<a name="atomic-locks"></a>
## Atomic Locks
## 원자 잠금장치(Atomic-locks)

> **Warning**  
> To utilize this feature, your application must be using the `memcached`, `redis`, `dynamodb`, `database`, `file`, or `array` cache driver as your application's default cache driver. In addition, all servers must be communicating with the same central cache server.

> **Warning**  
> 이 기능을 사용하려면 애플리케이션에서 `memcached`, `redis`, `redis`, `database`, `file` 또는 `array` 캐시 드라이버를 애플리케이션의 기본 캐시 드라이버로 사용해야합니다. 또한 모든 서버는 동일한 중앙 캐시 서버와 통신해야합니다.

<a name="lock-driver-prerequisites"></a>
### Driver Prerequisites
### 드라이버 전제 조건

<a name="atomic-locks-prerequisites-database"></a>
#### Database
#### Database

When using the `database` cache driver, you will need to setup a table to contain your application's cache locks. You'll find an example `Schema` declaration for the table below:

`database` 캐시 드라이버를 사용하는 경우 캐시 잠금을 포함하도록 테이블을 설정해야합니다. 아래에서 대한 테이블의 `Schema` 선언 예제를 찾을 수 있습니다.

    Schema::create('cache_locks', function (Blueprint $table) {
        $table->string('key')->primary();
        $table->string('owner');
        $table->integer('expiration');
    });

> **Note**  
> If you used the `cache:table` Artisan command to create the database driver's cache table, the migration created by that command already includes a definition for the `cache_locks` table.

> **Note**  
> `cache:table` 아티즌 명령어를 사용하여 데이터베이스 캐시 드라이버 용도의 테이블을 생성한 경우라면 이미 `cache_locks` 테이블이 포함되어 있습니다.

<a name="managing-locks"></a>
### Managing Locks
### 잠금 관리

Atomic locks allow for the manipulation of distributed locks without worrying about race conditions. For example, [Laravel Forge](https://forge.laravel.com) uses atomic locks to ensure that only one remote task is being executed on a server at a time. You may create and manage locks using the `Cache::lock` method:

원자 잠금장치(Atomic-locks)은 경쟁 조건에 대한 걱정없이 분산 잠금장치(lock)를 조작 할 수있게합니다. 예를 들어 [라라벨 Forge](https://forge.laravel.com)는 원자 잠금장치(Atomic-locks)을 사용하여 한 번에 하나의 원격 작업 만 서버에서 실행되도록합니다. `Cache::lock` 메소드를 사용하여 잠금장치(lock)을 생성하고 관리 할 수 있습니다.

    use Illuminate\Support\Facades\Cache;

    $lock = Cache::lock('foo', 10);

    if ($lock->get()) {
        // Lock acquired for 10 seconds...

        $lock->release();
    }

The `get` method also accepts a closure. After the closure is executed, Laravel will automatically release the lock:

`get` 메소드는 Closure를 사용할 수 있습니다. Closure가 실행 된 후 라라벨은 자동으로 잠금장치(lock)을 해제합니다.

    Cache::lock('foo', 10)->get(function () {
        // Lock acquired for 10 seconds and automatically released...
    });

If the lock is not available at the moment you request it, you may instruct Laravel to wait for a specified number of seconds. If the lock can not be acquired within the specified time limit, an `Illuminate\Contracts\Cache\LockTimeoutException` will be thrown:

요청한 순간에 잠금 장치를 사용할 수없는 경우 라라벨에 지정된 시간 (초) 동안 대기하도록 지시 할 수 있습니다. 지정된 제한 시간 내에 잠금을 획득 할 수 없으면 `Illuminate\Contracts\Cache\LockTimeoutException` 이 발생합니다.

    use Illuminate\Contracts\Cache\LockTimeoutException;

    $lock = Cache::lock('foo', 10);

    try {
        $lock->block(5);

        // Lock acquired after waiting a maximum of 5 seconds...
    } catch (LockTimeoutException $e) {
        // Unable to acquire lock...
    } finally {
        $lock?->release();
    }

The example above may be simplified by passing a closure to the `block` method. When a closure is passed to this method, Laravel will attempt to acquire the lock for the specified number of seconds and will automatically release the lock once the closure has been executed:

위의 예제에서 `block` 메서드에 클로저를 전달하여 단순화할 수 있습니다. 클로저가 이 메소드에 전달되면 라라벨은 지정된 시간(초) 동안 잠금을 획득하려고 시도하고 클로저가 실행되면 자동으로 잠금을 해제합니다.

    Cache::lock('foo', 10)->block(5, function () {
        // Lock acquired after waiting a maximum of 5 seconds...
    });

<a name="managing-locks-across-processes"></a>
### Managing Locks Across Processes
### 프로세스 간 잠금 관리

Sometimes, you may wish to acquire a lock in one process and release it in another process. For example, you may acquire a lock during a web request and wish to release the lock at the end of a queued job that is triggered by that request. In this scenario, you should pass the lock's scoped "owner token" to the queued job so that the job can re-instantiate the lock using the given token.

때로는 한 프로세스에서 잠금을 획득하고 다른 프로세스에서 잠금을 해제하고자 할 수 있습니다. 예를 들어, 웹 요청 중에 잠금을 획득하고 해당 요청으로 인해 대기중인 작업이 끝날 때 잠금을 해제하고자 할 수 있습니다. 이 시나리오에서는 잠금 범위가 지정된 "소유자 토큰"을 대기중인 작업에 전달하여 작업이 주어진 토큰을 사용하여 잠금을 다시 인스턴스화 할 수 있도록해야합니다.

In the example below, we will dispatch a queued job if a lock is successfully acquired. In addition, we will pass the lock's owner token to the queued job via the lock's `owner` method:

아래 예제에서는 잠금을 성공적으로 획득하면 대기 중인 작업을 전달합니다. 또한 잠금의 `owner` 메서드를 통해 잠금의 소유자 토큰을 대기열에 있는 작업에 전달합니다.

    $podcast = Podcast::find($id);

    $lock = Cache::lock('processing', 120);

    if ($lock->get()) {
        ProcessPodcast::dispatch($podcast, $lock->owner());
    }

Within our application's `ProcessPodcast` job, we can restore and release the lock using the owner token:

애플리케이션의 `ProcessPodcast` 작업 내에서 소유자 토큰을 사용하여 잠금을 복원하고 해제할 수 있습니다.

    Cache::restoreLock('processing', $this->owner)->release();

If you would like to release a lock without respecting its current owner, you may use the `forceRelease` method:

현재 소유자를 무시하고 잠금을 해제하려면 `forceRelease` 메소드를 사용할 수 있습니다.

    Cache::lock('processing')->forceRelease();

<a name="adding-custom-cache-drivers"></a>
## Adding Custom Cache Drivers
## 사용자 정의 캐시 드라이버 추가하기

<a name="writing-the-driver"></a>
### Writing The Driver
### 드라이버 작성하기

To create our custom cache driver, we first need to implement the `Illuminate\Contracts\Cache\Store` [contract](/docs/{{version}}/contracts). So, a MongoDB cache implementation might look something like this:

캐시 드라이버를 직접 만들기 위해서는 먼저 `Illuminate\Contracts\Cache\Store` [contract](/docs/{{version}}/contracts) 을 구현해야 합니다. 따라서 생성하려는 MongoDB 캐시는 다음과 같은 형태가 될 것입니다.

    <?php

    namespace App\Extensions;

    use Illuminate\Contracts\Cache\Store;

    class MongoStore implements Store
    {
        public function get($key) {}
        public function many(array $keys) {}
        public function put($key, $value, $seconds) {}
        public function putMany(array $values, $seconds) {}
        public function increment($key, $value = 1) {}
        public function decrement($key, $value = 1) {}
        public function forever($key, $value) {}
        public function forget($key) {}
        public function flush() {}
        public function getPrefix() {}
    }

We just need to implement each of these methods using a MongoDB connection. For an example of how to implement each of these methods, take a look at the `Illuminate\Cache\MemcachedStore` in the [Laravel framework source code](https://github.com/laravel/framework). Once our implementation is complete, we can finish our custom driver registration by calling the `Cache` facade's `extend` method:

MongoDB 연결을 사용하여 각각의 메소드를 구현해야 합니다. 각각의 메소드가 어떻게 구현되는가에 대한 예시로, [라라벨 프레임워크 소스코드](https://github.com/laravel/framework) 상에서 `Illuminate\Cache\MemcachedStore` 를 참고하세요. 구현을 완료하면 `Cache` 파사드의 `extend` 메소드를 호출하여 사용자 정의 드라이버 등록을 완료할 수 있습니다.

    Cache::extend('mongo', function (Application $app) {
        return Cache::repository(new MongoStore);
    });

> **Note**  
> If you're wondering where to put your custom cache driver code, you could create an `Extensions` namespace within your `app` directory. However, keep in mind that Laravel does not have a rigid application structure and you are free to organize your application according to your preferences.

> **Note**  
> 만약 여러분이 만든 캐시 드라이버 코드를 어디에 놓아둘지 고민된다면, `app` 디렉토리 안에 `Extensions` 네임스페이스를 만들 수도 있습니다. 하지만, 라라벨은 엄격한 애플리케이션 구조를 가지고 있지 않기 때문에, 어느 곳이든 여러분이 설정하고자 하는 곳에 코드를 둘 수 있다는 점을 기억하세요.

<a name="registering-the-driver"></a>
### Registering The Driver
### 드라이버 등록하기

To register the custom cache driver with Laravel, we will use the `extend` method on the `Cache` facade. Since other service providers may attempt to read cached values within their `boot` method, we will register our custom driver within a `booting` callback. By using the `booting` callback, we can ensure that the custom driver is registered just before the `boot` method is called on our application's service providers but after the `register` method is called on all of the service providers. We will register our `booting` callback within the `register` method of our application's `App\Providers\AppServiceProvider` class:

커스텀 캐시 드라이버를 라라벨에 등록하기 위해, 우리는 `Cache` 파사드에서 `extend` 메소드를 사용할 것입니다. 다른 서비스 제공자는 `boot` 메소드 내에서 캐시된 값을 읽으려고 시도할 수 있으므로 `booting` 콜백 내에서 사용자 정의 드라이버를 등록합니다. `booting` 콜백을 사용하면 애플리케이션의 서비스 제공자에서 `boot` 메소드가 호출되기 직전과 모든 서비스 제공자에서 `register` 메소드가 호출된 후에 사용자 정의 드라이버가 등록되도록 할 수 있습니다. 애플리케이션의 `App\Providers\AppServiceProvider` 클래스의 `register` 메소드 내에 `booting` 콜백을 등록합니다.

    <?php

    namespace App\Providers;

    use App\Extensions\MongoStore;
    use Illuminate\Contracts\Foundation\Application;
    use Illuminate\Support\Facades\Cache;
    use Illuminate\Support\ServiceProvider;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Register any application services.
         */
        public function register(): void
        {
            $this->app->booting(function () {
                 Cache::extend('mongo', function (Application $app) {
                     return Cache::repository(new MongoStore);
                 });
             });
        }

        /**
         * Bootstrap any application services.
         */
        public function boot(): void
        {
            // ...
        }
    }

The first argument passed to the `extend` method is the name of the driver. This will correspond to your `driver` option in the `config/cache.php` configuration file. The second argument is a closure that should return an `Illuminate\Cache\Repository` instance. The closure will be passed an `$app` instance, which is an instance of the [service container](/docs/{{version}}/container).

`extend` 메소드에 전달되는 첫 번째 인자는 드라이버의 이름입니다. 이 이름은 `config/cache.php` 설정 파일의 `driver` 옵션에 해당합니다. 두 번째 인자는 `Illuminate\Cache\Repository` 인스턴스를 반환하는 클로저가 됩니다. 클로저에는 `$app` 인스턴스가 전달되는데, 이 인자는 [서비스 컨테이너](/docs/{{version}}/container)의 인스턴스입니다.

Once your extension is registered, update your `config/cache.php` configuration file's `driver` option to the name of your extension.

extension이 등록되고 나면, `config/cache.php` 설정 파일의 `driver` 옵션을 추가한 extension의 이름으로 변경하면 됩니다.

<a name="events"></a>
## Events
## 이벤트

To execute code on every cache operation, you may listen for the [events](/docs/{{version}}/events) fired by the cache. Typically, you should place these event listeners within your application's `App\Providers\EventServiceProvider` class:

캐시가 동작할 때에 특정한 코드를 실행하기 위해서는 캐시에 의해 실행되는 [이벤트](/docs/{{version}}/events) 리스너를 등록해야 합니다. 일반적으로 이벤트 리스너에 대한 코드는 애플리케이션의 `App\Providers\EventServiceProvider` 클래스 안에 구성합니다.

    
    use App\Listeners\LogCacheHit;
    use App\Listeners\LogCacheMissed;
    use App\Listeners\LogKeyForgotten;
    use App\Listeners\LogKeyWritten;
    use Illuminate\Cache\Events\CacheHit;
    use Illuminate\Cache\Events\CacheMissed;
    use Illuminate\Cache\Events\KeyForgotten;
    use Illuminate\Cache\Events\KeyWritten;
    
    /**
     * The event listener mappings for the application.
     *
     * @var array
     */
    protected $listen = [
        CacheHit::class => [
            LogCacheHit::class,
        ],

        CacheMissed::class => [
            LogCacheMissed::class,
        ],

        KeyForgotten::class => [
            LogKeyForgotten::class,
        ],

        KeyWritten::class => [
            LogKeyWritten::class,
        ],
    ];
