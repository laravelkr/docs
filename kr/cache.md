# Cache 캐시

- [Configuration 설정](#configuration)
- [Cache Usage 캐시 사용법](#cache-usage)
- [Increments & Decrements 증감 조작하기](#increments-and-decrements)
- [Cache Tags 캐시 태그](#cache-tags)
- [Cache Events 캐시 이벤트](#cache-events)
- [Database Cache 데이터베이스 캐시](#database-cache)
- [Memcached Cache Memcached 캐시](#memcached-cache)
- [Redis Cache Redis 캐시](#redis-cache)


<a name="configuration"></a>
## Configuration 설정

Laravel provides a unified API for various caching systems. 라라벨은 다양한 캐시 시스템을 위한 통일된 API를 제공합니다. The cache configuration is located at `config/cache.php`. 캐시 설정은 `config/cache.php` 파일에 있습니다. In this file you may specify which cache driver you would like used by default throughout your application. 이 파일에서 전체 응용프로그램에서 기본적으로 사용하는 캐시 드라이버를 지정할 수 있습니다. Laravel supports popular caching backends like [Memcached](http://memcached.org) and [Redis](http://redis.io) out of the box. 라라벨은 [Memcached](http://memcached.org) 나 [Redis](http://redis.io) 와 같은 인기 있는 백엔드 캐시 시스템을 지원합니다. 

The cache configuration file also contains various other options, which are documented within the file, so make sure to read over these options. 캐시 설정 파일은 다양한 여러 옵션을 제공하고 있습니다. 각각의 옵션에 설명이 있으니 참고하시기 바랍니다. By default, Laravel is configured to use the `file` cache driver, which stores the serialized, cached objects in the filesystem. 라라벨의 기본 캐시 드라이버는 시리얼라이즈된 데이타를 파일 시스템에 저장하는 `file` 캐시 드라이버로 설정되어 있습니다. For larger applications, it is recommended that you use an in-memory cache such as Memcached or APC. You may even configure multiple cache configurations for the same driver. 보다 큰 응용프로그램은 Memcached 나 APC 와 같은 in-memory 캐시를 사용하기를 권장합니다. 

Before using a Redis cache with Laravel, you will need to install the `predis/predis` package (~1.0) via Composer. Redis 캐시를 사용하려면 컴포저로 `predis/predis` (~1.0) 패키지를 설치해야 합니다. 

<a name="cache-usage"></a>
## Cache Usage 캐시 사용법

#### Storing An Item In The Cache
#### 캐시에 하나의 아이템 저장하기

	Cache::put('key', 'value', $minutes);

#### Using Carbon Objects To Set Expire Time
#### 객체를 사용하여 만료시간 설정하기

	$expiresAt = Carbon::now()->addMinutes(10);

	Cache::put('key', 'value', $expiresAt);

#### Storing An Item In The Cache If It Doesn't Exist
#### 캐시에 존재하지 않는 아이템이라면 저장하기

	Cache::add('key', 'value', $minutes);

The `add` method will return `true` if the item is actually **added** to the cache. `add` 메소드는 항목이 실제로 캐시에 **추가된** 경우에만 true 를 반환합니다. Otherwise, the method will return `false`. 그렇지 않다면 `false`를 반환합니다. 

#### Checking For Existence In Cache
#### 캐시에 존재하는지 확인하기

	if (Cache::has('key'))
	{
		//
	}

#### Retrieving An Item From The Cache
#### 캐시에서 하나의 항목을 찾기

	$value = Cache::get('key');

#### Retrieving An Item Or Returning A Default Value
#### 찾는 항목이 없으면 기본값을 반환하기

	$value = Cache::get('key', 'default');

	$value = Cache::get('key', function() { return 'default'; });

#### Storing An Item In The Cache Permanently
#### 캐시에 영구적으로 저장하기

	Cache::forever('key', 'value');

Sometimes you may wish to retrieve an item from the cache, but also store a default value if the requested item doesn't exist. 간혹 캐시에서 항목을 조회할때 뿐만 아니라, 값이 없을 경우 기본 값을 캐시에 저장하고 싶은 경우가 있습니다. You may do this using the `Cache::remember` method:  이럴 경우 `Cache::remember` 메소드를 사용하면 됩니다. 

	$value = Cache::remember('users', $minutes, function()
	{
		return DB::table('users')->get();
	});

You may also combine the `remember` and `forever` methods:
`remember` 와 `forever`를 결합하여 사용할 수도 있습니다. 

	$value = Cache::rememberForever('users', function()
	{
		return DB::table('users')->get();
	});

Note that all items stored in the cache are serialized, so you are free to store any type of data. 캐시에 저장되는 모든 항목들은 직렬화-시리얼라이즈 되고, 따라서 어떤 유형의 데이터도 자유롭게 저장할 수 있습니다. 

#### Pulling An Item From The Cache
#### 캐시에 저장된 아이템 가져오기

If you need to retrieve an item from the cache and then delete it, you may use the `pull` method: 캐시에서 항목을 조회한 후에 삭제를 할 필요가 있으면, `pull` 메소드를 사용합니다.

	$value = Cache::pull('key');

#### Removing An Item From The Cache
#### 캐시에서 아이템 삭제하기

	Cache::forget('key');

#### Access Specific Cache Stores
#### 특정 저장소에 접근하기

When using multiple cache stores, you may access them via the `store` method:
다양한 캐시 저장소를 가지고 있다면, `store` 메소드를 통해서 특정 저장소에서 캐시를 가져올 수 있습니다. 

	$value = Cache::store('foo')->get('key');

<a name="increments-and-decrements"></a>
## Increments & Decrements 증감 조작하기

All drivers except `file` and `database` support the `increment` and `decrement` operations: `file`과 `database`를 제외한 드라이버는 `increment` 및 `decrement` 조작을 지원하고 있습니다.

#### Incrementing A Value 값 증가시키기

	Cache::increment('key');

	Cache::increment('key', $amount);

#### Decrementing A Value 값 감소시키기

	Cache::decrement('key');

	Cache::decrement('key', $amount);

<a name="cache-tags"></a>
## Cache Tags 캐시 태그

> **Note참고:** Cache tags are not supported when using the `file` or `database` cache drivers. 캐시 태그는 `file`과 `database` 드라이버를 사용하는 경우 지원되지 않습니다. Furthermore, when using multiple tags with caches that are stored "forever", performance will be best with a driver such as `memcached`, which automatically purges stale records. 또한 "forever"로 저장되는 캐시에 많은 태그를 사용하면 기존 레코드를 자동으로 삭제하는 memcached 와 같은 드라이버에서 최상의 성능을 낼 것입니다.

#### Accessing A Tagged Cache 태그된 캐시에 엑세스하기

Cache tags allow you to tag related items in the cache, and then flush all caches tagged with a given name. 캐시 태그는 캐시에 있는 관련된 아이템들을 태그 할 수 있도록 해주고, 같은 태그가 지정된 전체 캐시를 지울 수도 있습니다. To access a tagged cache, use the `tags` method. 태그된 캐시에 액세스하려면 `tags` 메소드를 사용하십시오.

You may store a tagged cache by passing in an ordered list of tag names as arguments, or as an ordered array of tag names: 태그 이름 목록을 인수로 전달하거나, 배열로 만들어 전달하여 태그된 캐시를 저장할 수 있습니다.

	Cache::tags('people', 'authors')->put('John', $john, $minutes);

	Cache::tags(['people', 'artists'])->put('Anne', $anne, $minutes);

You may use any cache storage method in combination with tags, including `remember`, `forever`, and `rememberForever`. `remember` , `forever` , `rememberForever` 및 이들의 을 포함한 모든 캐시 저장 방법 및 태그를 조합하여 사용할 수 있습니다. You may also access cached items from the tagged cache, as well as use the other cache methods such as `increment` and `decrement`. 또한 increment 및 decrement 같은 다른 캐시 메소드도 마찬가지로 태그를 캐시 항목에 액세스 할 수 있습니다.

#### Accessing Items In A Tagged Cache 태그된 캐시에 있는 아이템에 접근하기

To access a tagged cache, pass the same ordered list of tags used to save it.
태그가 지정된 캐쉬에 액세스하려면 저장된 것과 동일한 순서로 태그의 목록을 전달합니다.

	$anne = Cache::tags('people', 'artists')->get('Anne');

	$john = Cache::tags(['people', 'authors'])->get('John');

You may flush all items tagged with a name or list of names. 이름 또는 이름의 목록을 지정하고 태그 된 항목을 모두 지울 수 있습니다. For example, this statement would remove all caches tagged with either `people`, `authors`, or both. 예를 들어 다음 코드는 `people` 또는 `authors` 또는 둘 모두에 태그 된 모든 캐시 된 항목이 삭제됩니다. So, both "Anne" and "John" would be removed from the cache: 그러므로 "Anne"과 "John"두 아이템은 캐시에서 제거됩니다:

	Cache::tags('people', 'authors')->flush();

In contrast, this statement would remove only caches tagged with `authors`, so "John" would be removed, but not "Anne". 한편, 다음 코드에서는 `authors` 태그 된 캐시 만 삭제되기 때문에 "John"은 삭제되지만 "Anne"는 남아 있습니다.

	Cache::tags('authors')->flush();

<a name="cache-events"></a>
## Cache Events 캐시 이벤트

To execute code on every cache operation, you may listen for the events fired by the cache: 캐시가 동작할 때에 특정한 코드를 실행하기 위해서는 캐시에 의해서 실행되는 이벤트 리스너를 등록해야 합니다. 

	Event::listen('cache.hit', function($key, $value) {
		//
	});

	Event::listen('cache.missed', function($key) {
		//
	});

	Event::listen('cache.write', function($key, $value, $minutes) {
		//
	});

	Event::listen('cache.delete', function($key) {
		//
	});

<a name="database-cache"></a>
## Database Cache 데이터베이스 캐시

When using the `database` cache driver, you will need to setup a table to contain the cache items. `database` 캐시 드라이버를 사용하는 경우 캐시 항목을 저장할 테이블을 준비해야합니다. You'll find an example `Schema` declaration for the table below: 아래에서 테이블을 만드는 `Schema` 의 예를 확인 할 수 있습니다.

	Schema::create('cache', function($table)
	{
		$table->string('key')->unique();
		$table->text('value');
		$table->integer('expiration');
	});
	
<a name="memcached-cache"></a>
#### Memcached Cache

Using the Memcached cache requires the [Memcached PECL package](http://pecl.php.net/package/memcached) to be installed. Memcached 캐시를 사용하기 위해서는 [Memcached PECL package](http://pecl.php.net/package/memcached)가 설치되어 있어야 합니다. 

The default [configuration](#configuration) uses TCP/IP based on [Memcached::addServer](http://php.net/manual/en/memcached.addserver.php):
기본 [설정](#configuration)은 TCP/IP 기반의 [Memcached::addServer](http://php.net/manual/en/memcached.addserver.php)을 사용합니다. 

	'memcached' => array(
		array('host' => '127.0.0.1', 'port' => 11211, 'weight' => 100),
	),

You may also set the `host` option to a UNIX socket path. If you do this, the `port` option should be set to `0`: `host` 옵션을 UNIX 소켓을 사용하도록 설정할 수 있으며 이 경우 `port` 는 `0` 으로 설정되어 있어야 합니다. 

	'memcached' => array(
		array('host' => '/var/run/memcached/memcached.sock', 'port' => 0, 'weight' => 100),
	),

<a name="redis-cache"></a>
#### Redis Cache

See [Redis Configuration](/docs/redis#configuration)
[Redis 설정](/docs/redis#configuration)을 참고하십시오. 

