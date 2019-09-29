# Cache
# 캐시

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
    - [Atomic Locks](#atomic-locks)
    - [원자 잠금장치](#atomic-locks)
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
- [Adding Custom Cache Drivers](#adding-custom-cache-drivers)
- [사용자 정의 캐시 드라이버 추가하기](#adding-custom-cache-drivers)
    - [Writing The Driver](#writing-the-driver)
    - [드라이버 작성하기](#writing-the-driver)
    - [Registering The Driver](#registering-the-driver)
    - [드라이버 등록하기](#registering-the-driver)
- [Events](#events)
- [이벤트](#events)

<a name="configuration"></a>
## Configuration
## 설정하기

Laravel provides an expressive, unified API for various caching backends. The cache configuration is located at `config/cache.php`. In this file you may specify which cache driver you would like to be used by default throughout your application. Laravel supports popular caching backends like [Memcached](https://memcached.org) and [Redis](https://redis.io) out of the box.

라라벨은 다양한 캐시 시스템을 위해서 풍부한 표현이 가능한 통합 API를 제공합니다. 캐시 설정은 `config/cache.php` 파일에 있습니다. 이 파일에 전체 애플리케이션에서 기본적으로 사용할 캐시 드라이버를 지정할 수 있습니다. 라라벨은 [Memcached](https://memcached.org) 나 [Redis](https://redis.io) 와 같은 인기 있는 백엔드 캐시 시스템을 지원합니다.

The cache configuration file also contains various other options, which are documented within the file, so make sure to read over these options. By default, Laravel is configured to use the `file` cache driver, which stores the serialized, cached objects in the filesystem. For larger applications, it is recommended that you use a more robust driver such as Memcached or Redis. You may even configure multiple cache configurations for the same driver.

캐시 설정 파일은 다양한 여러 옵션을 제공하고 있습니다. 각각의 옵션에 설명이 있으니 참고하시기 바랍니다. 라라벨의 기본 캐시 드라이버는 시리얼라이즈된 데이타를 파일 시스템에 저장하는 `file` 캐시 드라이버로 설정되어 있습니다. 보다 큰 응용프로그램은 Memcached 나 Redis 와 같이 보다 견고한 캐시 드라이버를 사용하기를 권장합니다. 동일한 드라이버를 위한 다수의 캐시 설정을 구성할 수도 있습니다.

<a name="driver-prerequisites"></a>
### Driver Prerequisites
### 드라이버 사전 준비사항

#### Database
#### 데이터베이스

When using the `database` cache driver, you will need to setup a table to contain the cache items. You'll find an example `Schema` declaration for the table below:

`database` 캐시 드라이버를 사용할 때에는, 캐시 정보를 담아둘 테이블을 구성할 필요가 있습니다. 다음과 같이 테이블을 구성하는 `Scheme`를 확인하십시오.

    Schema::create('cache', function ($table) {
        $table->string('key')->unique();
        $table->text('value');
        $table->integer('expiration');
    });

> {tip} You may also use the `php artisan cache:table` Artisan command to generate a migration with the proper schema.

> {tip} 적절한 스키마 마이그레이션을 생성하기 위해 `php artisan cache:tableArtisan` 명령어를 사용할 수도 있습니다.

#### Memcached
#### Memcached

Using the Memcached driver requires the [Memcached PECL package](https://pecl.php.net/package/memcached) to be installed. You may list all of your Memcached servers in the `config/cache.php` configuration file:

Memcached 드라이버를 사용하려면 [Memcached PECL 패키지](https://pecl.php.net/package/memcached)가 설치되어 있어야 합니다. `config/cache.php` 설정 파일안에서 Memcache 서버 목록을 나열하면 됩니다.

    'memcached' => [
        [
            'host' => '127.0.0.1',
            'port' => 11211,
            'weight' => 100
        ],
    ],

You may also set the `host` option to a UNIX socket path. If you do this, the `port` option should be set to `0`:

또한 `host` 옵션으로 UNIX 소켓을 설정할 수 있습니다. 이 경우 `port` 옵션은 `0` 으로 구성해야 합니다.

    'memcached' => [
        [
            'host' => '/var/run/memcached/memcached.sock',
            'port' => 0,
            'weight' => 100
        ],
    ],

#### Redis
#### Redis

Before using a Redis cache with Laravel, you will need to either install the PhpRedis PHP extension via PECL or install the `predis/predis` package (~1.0) via Composer .

라라벨에서 Redis 캐스를 사용하기 전에, PECL을 통해서 PhpRedis PHP Extension을 설치하거나, 컴포저를 통해서 `predis/predis` 패키지(~1.0)를 설치해야 합니다.

For more information on configuring Redis, consult its [Laravel documentation page](/docs/{{version}}/redis#configuration).

Redis 설정과 관련된 보다 자세한 사항은 [라라벨 Redis 문서](/docs/{{version}}/redis#configuration)를 참고하십시오.

<a name="cache-usage"></a>
## Cache Usage
## 캐시 사용법

<a name="obtaining-a-cache-instance"></a>
### Obtaining A Cache Instance
### 캐시 인스턴스 획득하기

The `Illuminate\Contracts\Cache\Factory` and `Illuminate\Contracts\Cache\Repository` [contracts](/docs/{{version}}/contracts) provide access to Laravel's cache services. The `Factory` contract provides access to all cache drivers defined for your application. The `Repository` contract is typically an implementation of the default cache driver for your application as specified by your `cache` configuration file.

`Illuminate\Contracts\Cache\Factory` 와 `Illuminate\Contracts\Cache\Repository` [contracts](/docs/{{version}}/contracts)는 라라벨의 캐시 서비스에 대한 엑세스를 제공합니다. `Factory` contract 는 애플리케이션에서 정의하고 있는 모든 캐시 드라이버를 제공합니다. `Repository` 는 일반적으로 `cache` 설정 파일에서 기본으로 설정된 캐시 드라이버의 구현체입니다.

However, you may also use the `Cache` facade, which is what we will use throughout this documentation. The `Cache` facade provides convenient, terse access to the underlying implementations of the Laravel cache contracts:

하지만 언제라도 `Cache` 파사드를 사용 할 수 있습니다. `Cache` 파사드는 라라벨의 캐시 contract 를 활용하여 캐시에 대한 편리하고 간결한 엑세스를 제공합니다.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Support\Facades\Cache;

    class UserController extends Controller
    {
        /**
         * Show a list of all users of the application.
         *
         * @return Response
         */
        public function index()
        {
            $value = Cache::get('key');

            //
        }
    }

#### Accessing Multiple Cache Stores
#### 여러 개의 캐시 Store 사용하기

Using the `Cache` facade, you may access various cache stores via the `store` method. The key passed to the `store` method should correspond to one of the stores listed in the `stores` configuration array in your `cache` configuration file:

`Cache` 파사드를 사용하면서 `store` 메소드를 통해서 여러 개의 캐시 Store 에 엑세스 할 수 있습니다. `store` 메소드에 전달되는 키는 `cache` 설정 파일에서 `stores` 배열에 들어 있는 store 중 하나여야 합니다.

    $value = Cache::store('file')->get('foo');

    Cache::store('redis')->put('bar', 'baz', 600); // 10 Minutes

<a name="retrieving-items-from-the-cache"></a>
### Retrieving Items From The Cache
### 캐시에서 아이템 조회하기

The `get` method on the `Cache` facade is used to retrieve items from the cache. If the item does not exist in the cache, `null` will be returned. If you wish, you may pass a second argument to the `get` method specifying the default value you wish to be returned if the item doesn't exist:

`Cache` 파사드에서 `get` 메소드를 사용하여 캐시에 들어 있는 아이템을 찾을 수 있습니다. 아이템이 캐시에 없다면, `null` 이 반환될 것입니다. 아이템이 존재하지 않을 때 기본값을 반환할 수 있도록 `get` 메소드의 두번째 인자로 전달할 수 있습니다.

    $value = Cache::get('key');

    $value = Cache::get('key', 'default');


You may even pass a `Closure` as the default value. The result of the `Closure` will be returned if the specified item does not exist in the cache. Passing a Closure allows you to defer the retrieval of default values from a database or other external service:

기본값을 `클로저`로 전달할 수도 있습니다. 캐시에 지정한 아이템이 존재하지 않을 경우에 `클로저`의 반환값이 결과값이 됩니다. 클로저를 전달하면 데이터베이스와 외부 서비스로부터 기본값을 획득할 수 있습니다.

    $value = Cache::get('key', function () {
        return DB::table(...)->get();
    });

#### Checking For Item Existence
#### 아이템이 존재하는지 확인하기

The `has` method may be used to determine if an item exists in the cache. This method will return `false` if the value is `null`:

`has` 메소드는 캐시에 아이템이 존재하는지 확인하는데 사용됩니다. 만약 값이 `null` 인 경우 이 메소드는 `false`를 반환합니다.

    if (Cache::has('key')) {
        //
    }

#### Incrementing / Decrementing Values
#### 증가 / 감소

The `increment` and `decrement` methods may be used to adjust the value of integer items in the cache. Both of these methods accept an optional second argument indicating the amount by which to increment or decrement the item's value:

`increment` 와 `decrement` 메소드는 캐시에 들어 있는 정수형 아이템의 값을 변경하는데 사용합니다. 두 메소드는 모두 해당 아이템의 값을 얼마나 증가 또는 감소 시킬지 결정하는 두 번째 인자를 선택적으로 전달할 수 있습니다.

    Cache::increment('key');
    Cache::increment('key', $amount);
    Cache::decrement('key');
    Cache::decrement('key', $amount);

#### Retrieve & Store
#### 조회 & 저장

Sometimes you may wish to retrieve an item from the cache, but also store a default value if the requested item doesn't exist. For example, you may wish to retrieve all users from the cache or, if they don't exist, retrieve them from the database and add them to the cache. You may do this using the `Cache::remember` method:

때로는 캐시로부터 아이템을 찾는 것 뿐만 아니라, 찾으려는 아이템이 존재하지 않는 경우 기본값을 저장하고자 할 수도 있습니다. 예를 들어 캐시에서 모든 사용자를 찾으려 할 때 아무 것도 없다면, 데이터베이스로부터 정보를 획득하여 캐시에 추가하고자 할 수도 있습니다. 이 경우에 `Cache::remember` 메소드를 사용하면 됩니다.

    $value = Cache::remember('users', $seconds, function () {
        return DB::table('users')->get();
    });

If the item does not exist in the cache, the `Closure` passed to the `remember` method will be executed and its result will be placed in the cache.

캐시에 아이템이 존재하지 않는 경우, `remember` 메소드에 전달된 `클로저` 가 실행되어 캐시에 값을 저장하게 됩니다.

You may use the `rememberForever` method to retrieve an item from the cache or store it forever:

어떤 아이템을 조회하고 이를 영원히 기억하도록 `rememberForever` 메소드를 사용할 수 있습니다.

    $value = Cache::rememberForever('users', function () {
        return DB::table('users')->get();
    });

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

    Cache::put('key', 'value', $seconds);

If the storage time is not passed to the `put` method, the item will be stored indefinitely:

저장 시간이 `put` 메소드에 전달되지 않으면 그 아이템은 무기한 저장 될 것입니다 :

    Cache::put('key', 'value');

Instead of passing the number of seconds as an integer, you may also pass a `DateTime` instance representing the expiration time of the cached item:

정수로 초를 전달하는 대신 캐시 된 항목의 만료 시점을 나타내는 `DateTime` 인스턴스를 전달할 수도 있습니다.

    Cache::put('key', 'value', now()->addMinutes(10));

#### Store If Not Present
#### 존재하지 않으면 저장하기

The `add` method will only add the item to the cache if it does not already exist in the cache store. The method will return `true` if the item is actually added to the cache. Otherwise, the method will return `false`:

`add` 메소드는 캐시에 아이템이 이미 존재하지 않는 경우에만 아이템을 저장합니다. 캐시에 아이템이 잘 저장되었다면 메소드가 `true`를 반환할 것입니다. 그렇지 않은 경우 `false` 를 반환할 것입니다.

    Cache::add('key', 'value', $seconds);

#### Storing Items Forever
#### 아이템들을 영구적으로 저장하기

The `forever` method may be used to store an item in the cache permanently. Since these items will not expire, they must be manually removed from the cache using the `forget` method:

`forever` 메소드는 캐시에 아이템을 영구적으로 저장하고자 할 때 사용됩니다. 이 아이템들은 자동으로 만료되지 않기 때문에, `forget` 메소드를 사용하여 캐시에서 수동으로 삭제해주어야만 합니다.

    Cache::forever('key', 'value');

> {tip} If you are using the Memcached driver, items that are stored "forever" may be removed when the cache reaches its size limit.

> {tip} 만약 Memcached 드라이버를 사용중이라면, "영구적으로" 저장된 아이템들은 캐시의 사이즈 제한에 도달한 경우 제거될 것입니다.

<a name="removing-items-from-the-cache"></a>
### Removing Items From The Cache
### 캐시에서 아이템 삭제하기

You may remove items from the cache using the `forget` method:

`forget` 메소드를 사용하여 캐시에서 아이템을 삭제할 수 있습니다.

    Cache::forget('key');

You may also remove items by providing a zero or negative TTL:

0 또는 음수로 TTL을 입력하여 아이템을 제거 할 수도 있습니다.

    Cache::put('key', 'value', 0);

    Cache::put('key', 'value', -5);

You may clear the entire cache using the `flush` method:

`flush` 메소드를 사용하면 전체 캐시를 삭제합니다.

    Cache::flush();

> {note} Flushing the cache does not respect the cache prefix and will remove all entries from the cache. Consider this carefully when clearing a cache which is shared by other applications.

> {note} 모든 캐시를 비우는 것은 캐시에서 모든 항목이 제거된다는 것을 의미합니다. 애플리케이션의 다른 부분에서 공유하는 캐시를 제거할 때에 주의하십시오.

<a name="atomic-locks"></a>
### Atomic Locks
### 원자 잠금장치(Atomic-locks)

> {note} To utilize this feature, your application must be using the `memcached`, or `redis` cache driver as your application's default cache driver. In addition, all servers must be communicating with the same central cache server.

> {note} 이 기능을 활용하려면, 애플리케이션이 기본 캐시 드라이버로 `memcached`, `dynamodb` 또는 `redis` 캐시 드라이버를 사용해야합니다. 또한 모든 서버는 동일한 중앙 캐시 서버와 통신해야합니다.

Atomic locks allow for the manipulation of distributed locks without worrying about race conditions. For example, [Laravel Forge](https://forge.laravel.com) uses atomic locks to ensure that only one remote task is being executed on a server at a time. You may create and manage locks using the `Cache::lock` method:

원자 잠금장치(Atomic-locks)은 경쟁 조건에 대한 걱정없이 분산 잠금장치(lock)를 조작 할 수있게합니다. 예를 들어 [Laravel Forge](https://forge.laravel.com)는 원자 잠금장치(Atomic-locks)을 사용하여 한 번에 하나의 원격 작업 만 서버에서 실행되도록합니다. `Cache::lock` 메소드를 사용하여 잠금장치(lock)을 생성하고 관리 할 수 있습니다 :

    use Illuminate\Support\Facades\Cache;

    $lock = Cache::lock('foo', 10);

    if ($lock->get()) {
        // Lock acquired for 10 seconds...

        $lock->release();
    }

The `get` method also accepts a Closure. After the Closure is executed, Laravel will automatically release the lock:

`get` 메소드는 Closure를 사용할 수 있습니다. Closure가 실행 된 후 Laravel은 자동으로 잠금장치(lock)을 해제합니다.

    Cache::lock('foo')->get(function () {
        // Lock acquired indefinitely and automatically released...
    });

If the lock is not available at the moment you request it, you may instruct Laravel to wait for a specified number of seconds. If the lock can not be acquired within the specified time limit, an `Illuminate\Contracts\Cache\LockTimeoutException` will be thrown:

요청한 순간에 잠금 장치를 사용할 수없는 경우 Laravel에 지정된 시간 (초) 동안 대기하도록 지시 할 수 있습니다. 지정된 제한 시간 내에 잠금을 획득 할 수 없으면 `Illuminate\Contracts\Cache\LockTimeoutException` 이 발생합니다.

    use Illuminate\Contracts\Cache\LockTimeoutException;

    $lock = Cache::lock('foo', 10);

    try {
        $lock->block(5);

        // Lock acquired after waiting maximum of 5 seconds...
    } catch (LockTimeoutException $e) {
        // Unable to acquire lock...
    } finally {
             optional($lock)->release();
    }

    Cache::lock('foo', 10)->block(5, function () {
        // Lock acquired after waiting maximum of 5 seconds...
    });

#### Managing Locks Across Processes
#### 프로세스 간 잠금 관리

Sometimes, you may wish to acquire a lock in one process and release it in another process. For example, you may acquire a lock during a web request and wish to release the lock at the end of a queued job that is triggered by that request. In this scenario, you should pass the lock's scoped "owner token" to the queued job so that the job can re-instantiate the lock using the given token:

때로는 한 프로세스에서 잠금을 획득하고 다른 프로세스에서 잠금을 해제하고자 할 수 있습니다. 예를 들어, 웹 요청 중에 잠금을 획득하고 해당 요청으로 인해 대기중인 작업이 끝날 때 잠금을 해제하고자 할 수 있습니다. 이 시나리오에서는 잠금 범위가 지정된 "소유자 토큰"을 대기중인 작업에 전달하여 작업이 주어진 토큰을 사용하여 잠금을 다시 인스턴스화 할 수 있도록해야합니다.

    // Within Controller...
    $podcast = Podcast::find($id);

    $lock = Cache::lock('foo', 120);

    if ($result = $lock->get()) {
        ProcessPodcast::dispatch($podcast, $lock->owner());
    }

    // Within ProcessPodcast Job...
    Cache::restoreLock('foo', $this->owner)->release();

If you would like to release a lock without respecting its current owner, you may use the `forceRelease` method:

현재 소유자를 무시하고 잠금을 해제하려면 `forceRelease` 메소드를 사용할 수 있습니다.

    Cache::lock('foo')->forceRelease();

<a name="the-cache-helper"></a>
### The Cache Helper
### 캐시 헬퍼 함수

In addition to using the `Cache` facade or [cache contract](/docs/{{version}}/contracts), you may also use the global `cache` function to retrieve and store data via the cache. When the `cache` function is called with a single, string argument, it will return the value of the given key:

`Cache` 파사드나 [캐시 contract](/docs/{{version}}/contracts)를 사용하는것에 더하여, 글로벌 `cache` 함수를 사용하여 캐시에서 데이터를 조회하거나 저장할 수 있습니다. `cache` 함수에 인자가 하나의 문자열일 때는, 주어진 키에 대한 값을 반환합니다.
    $value = cache('key');

If you provide an array of key / value pairs and an expiration time to the function, it will store values in the cache for the specified duration:

키와 값으로된 배열과 유효시간을 함수에 전달하면, 지정된 기간동안 캐시에 값을 저장하고 있습니다.

    cache(['key' => 'value'], $seconds);

    cache(['key' => 'value'], now()->addMinutes(10));

When the `cache` function is called without any arguments, it returns an instance of the `Illuminate\Contracts\Cache\Factory` implementation, allowing you to call other caching methods:

`cache` 함수가 아무런 인자없이 호출되면 `Illuminate/Contracts/Cache/Factory` 를 구현한 인스턴스를 반환하고 사용자는 이것을 통해 다른 모든 캐싱 메소드를 호출 할 수 있습니다 :

    cache()->remember('users', $seconds, function () {
        return DB::table('users')->get();
    });

> {tip} When testing call to the global `cache` function, you may use the `Cache::shouldReceive` method just as if you were [testing a facade](/docs/{{version}}/mocking#mocking-facades).

> {tip} 글로벌 `cache` 함수 호출을 테스팅할 때에는, [파사드 테스팅](/docs/{{version}}/mocking#mocking-facades)을 참고하여 `Cache::shouldReceive`메소드를 사용할 수 있습니다.

<a name="cache-tags"></a>
## Cache Tags
## 캐시 태그

> {note} Cache tags are not supported when using the `file` or `database` cache drivers. Furthermore, when using multiple tags with caches that are stored "forever", performance will be best with a driver such as `memcached`, which automatically purges stale records.

> {note} 캐시 태그는 `file`과 `database` 드라이버를 사용하는 경우 지원되지 않습니다. 또한 "forever"로 저장되는 캐시에 많은 태그를 사용하면 기존 레코드를 자동으로 삭제하는 `memcached` 와 같은 드라이버에서 최상의 성능을 낼 것입니다.

<a name="storing-tagged-cache-items"></a>
### Storing Tagged Cache Items
### 태그가 추가된 캐시 아이템 저장하기

Cache tags allow you to tag related items in the cache and then flush all cached values that have been assigned a given tag. You may access a tagged cache by passing in an ordered array of tag names. For example, let's access a tagged cache and `put` value in the cache:

캐시 태그는 캐시에 있는 관련된 아이템들을 태그할 수 있도록 해주고, 주어진 태그가 지정된 전체 캐시 항목을 삭제할 수도 있게 해줍니다. 여러분은 순서대로 태그 이름의 배열을 전달하여 태그가 추가된 캐시 아이템에 엑세스할 수 있습니다. 예를 들어, 태그가 지정된 캐시에 엑세스하여, 값을 `저장(put)` 해 보겠습니다.

    Cache::tags(['people', 'artists'])->put('John', $john, $seconds);

    Cache::tags(['people', 'authors'])->put('Anne', $anne, $seconds);

<a name="accessing-tagged-cache-items"></a>
### Accessing Tagged Cache Items
### 태그된 캐시 아이템 엑세스하기

To retrieve a tagged cache item, pass the same ordered list of tags to the `tags` method and then call the `get` method with the key you wish to retrieve:

태그가 지정된 캐시 아이템을 찾으려면 `tags` 메소드에 저장된 것과 동일한 순서로 태그의 목록을 전달하고, 찾고자 하는 키와 함께 `get` 메소드를 호출하면 됩니다.

    $john = Cache::tags(['people', 'artists'])->get('John');

    $anne = Cache::tags(['people', 'authors'])->get('Anne');

<a name="removing-tagged-cache-items"></a>
### Removing Tagged Cache Items
### 태그된 캐시 아이템 제거하기

You may flush all items that are assigned a tag or list of tags. For example, this statement would remove all caches tagged with either `people`, `authors`, or both. So, both `Anne` and `John` would be removed from the cache:

태그 또는 태그 목록이 지정된 아이템을 모두 지울 수 있습니다. 예를 들어 다음 코드는 `people` 또는 `authors` 또는 둘 모두에 태그가 지정된 모든 캐시된 항목이 삭제됩니다. 그러므로 "Anne"과 "John" 두 아이템은 캐시에서 제거됩니다.

    Cache::tags(['people', 'authors'])->flush();

In contrast, this statement would remove only caches tagged with `authors`, so `Anne` would be removed, but not `John`:

한편, 다음 코드에서는 `authors` 태그된 캐시만 삭제되기 때문에 `Anne`은 삭제되지만 `John`는 남아 있습니다.

    Cache::tags('authors')->flush();

<a name="adding-custom-cache-drivers"></a>
## Adding Custom Cache Drivers
## 사용자 정의 캐시 드라이버 추가하기

<a name="writing-the-driver"></a>
### Writing The Driver
### 드라이버 작성하기

To create our custom cache driver, we first need to implement the `Illuminate\Contracts\Cache\Store` [contract](/docs/{{version}}/contracts). So, a MongoDB cache implementation would look something like this:

여러분의 고유한 캐시 드라이버를 생성하기 위해서는 먼저 `Illuminate\Contracts\Cache\Store` [contract](/docs/{{version}}/contracts) 을 구현할 필요가 있습니다. 따라서 생성하려는 MongoDB 캐시는 다음과 같은 형태가 될 것입니다.

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

We just need to implement each of these methods using a MongoDB connection. For an example of how to implement each of these methods, take a look at the `Illuminate\Cache\MemcachedStore` in the framework source code. Once our implementation is complete, we can finish our custom driver registration.

이제 MongoDB 연결을 사용하여 각각의 메소드를 구현해야 합니다. 각각의 메소드가 어떻게 구현되는가에 대한 예시로, 프레임워크 소스 코드 상에서 `Illuminate\Cache\MemcachedStore` 를 살펴보도록 하겠습니다. 구현을 완료하면 사용자 정의 드라이버를 등록할 수 있습니다.

    Cache::extend('mongo', function ($app) {
        return Cache::repository(new MongoStore);
    });

> {tip} If you're wondering where to put your custom cache driver code, you could create an `Extensions` namespace within your `app` directory. However, keep in mind that Laravel does not have a rigid application structure and you are free to organize your application according to your preferences.

> {tip} 만약 여러분이 구성한 캐시 드라이버 코드를 어디에 놓아둘지 고민된다면, `app` 디렉토리 안에 `Extensions` 네임스페이스를 만들 수도 있습니다. 하지만, 라라벨은 엄격한 애플리케이션 구조를 가지고 있지 않기 때문에, 어느 곳이든 여러분이 설정하고자 하는 곳에 코드를 둘 수 있다는 점을 기억하세요.

<a name="registering-the-driver"></a>
### Registering The Driver
### 드라이버 등록하기

To register the custom cache driver with Laravel, we will use the `extend` method on the `Cache` facade. The call to `Cache::extend` could be done in the `boot` method of the default `App\Providers\AppServiceProvider` that ships with fresh Laravel applications, or you may create your own service provider to house the extension - just don't forget to register the provider in the `config/app.php` provider array:

사용자 정의 캐시 드라이버를 라라벨에 등록하기 위해서, `Cache` 파사드의 `extend` 메소드를 사용할 것입니다. `Cache::extend` 호출은 라라벨 애플리케이션 설치시 제공되는 `App\Providers\AppServiceProvider`의 `boot` 메소드를 사용하거나, extension 을 제공하는 고유한 서비스 프로바이더를 생성해서 쓸 수 있습니다. - 프로바이더를 `config/app.php` 파일 안에 있는 프로바이더 배열에 등록하는 것을 잊지 마십시오:

    <?php

    namespace App\Providers;

    use App\Extensions\MongoStore;
    use Illuminate\Support\Facades\Cache;
    use Illuminate\Support\ServiceProvider;

    class CacheServiceProvider extends ServiceProvider
    {
        /**
         * Register bindings in the container.
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
            Cache::extend('mongo', function ($app) {
                return Cache::repository(new MongoStore);
            });
        }
    }

The first argument passed to the `extend` method is the name of the driver. This will correspond to your `driver` option in the `config/cache.php` configuration file. The second argument is a Closure that should return an `Illuminate\Cache\Repository` instance. The Closure will be passed an `$app` instance, which is an instance of the [service container](/docs/{{version}}/container).

`extend` 메소드에 전달되는 첫 번째 인자는 드라이버의 이름입니다. 이 이름은 `config/cache.php` 설정 파일의 `driver` 옵션에 해당합니다. 두 번째 인자는 `Illuminate\Cache\Repository` 인스턴스를 반환하는 클로저가 됩니다. 클로저에는 `$app` 인스턴스가 전달되는데, 이 인자는 [서비스 컨테이너](/docs/{{version}}/container)의 인스턴스입니다.

Once your extension is registered, update your `config/cache.php` configuration file's `driver` option to the name of your extension.

extension이 등록되고 나면, `config/cache.php` 설정 파일의 `driver` 옵션을 추가한 extension의 이름으로 변경하면 됩니다.

<a name="events"></a>
## Events
## 이벤트

To execute code on every cache operation, you may listen for the [events](/docs/{{version}}/events) fired by the cache. Typically, you should place these event listeners within your `EventServiceProvider`:

캐시가 동작할 때에 특정한 코드를 실행하기 위해서는 캐시에 의해 실행되는 이벤트 리스너를 등록해야 합니다. 일반적으로 이벤트 리스너에 대한 코드는 `EventServiceProvider`안에 구성합니다.

    /**
     * The event listener mappings for the application.
     *
     * @var array
     */
    protected $listen = [
        'Illuminate\Cache\Events\CacheHit' => [
            'App\Listeners\LogCacheHit',
        ],

        'Illuminate\Cache\Events\CacheMissed' => [
            'App\Listeners\LogCacheMissed',
        ],

        'Illuminate\Cache\Events\KeyForgotten' => [
            'App\Listeners\LogKeyForgotten',
        ],

        'Illuminate\Cache\Events\KeyWritten' => [
            'App\Listeners\LogKeyWritten',
        ],
    ];
