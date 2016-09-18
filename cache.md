# 캐시(Cache)

- [설정](#configuration)
- [캐시 사용법](#cache-usage)
- [증감 조작하기](#increments-and-decrements)
- [캐시 태그](#cache-tags)
- [캐시 이벤트](#cache-events)
- [데이터베이스 캐시](#database-cache)
- [Memcached 캐시](#memcached-cache)
- [Redis 캐시](#redis-cache)


<a name="configuration"></a>
## 설정

라라벨은 다양한 캐시 시스템을 위한 통일된 API를 제공합니다. 캐시 설정은 `config/cache.php` 파일에 있습니다. 이 파일에서 전체 응용프로그램에서 기본적으로 사용하는 캐시 드라이버를 지정할 수 있습니다. 라라벨은 [Memcached](http://memcached.org)나 [Redis](http://redis.io)와 같은 인기 있는 백엔드 캐시 시스템을 지원합니다.

캐시 설정 파일은 다양한 옵션을 제공하고 있습니다. 각각의 옵션에 설명이 있으니 참고하시기 바랍니다. 라라벨의 기본 캐시 드라이버는 직렬화(serialize)된 데이터를 파일 시스템에 저장하는 `file` 캐시 드라이버로 설정되어 있습니다.  보다 큰 응용프로그램은 Memcached나 APC와 같은 in-memory 캐시를 사용하기를 권장합니다.

Redis 캐시를 사용하려면 컴포저로 `predis/predis`(~1.0) 패키지를 설치해야 합니다.

<!--chak-comment-캐시(Cache)-설정-->

<a name="cache-usage"></a>
## 캐시 사용법

#### 캐시에 하나의 아이템 저장하기

	Cache::put('key', 'value', $minutes);

#### 객체를 사용하여 만료시간 설정하기

	$expiresAt = Carbon::now()->addMinutes(10);

	Cache::put('key', 'value', $expiresAt);

#### 캐시에 존재하지 않는 아이템이라면 저장하기

	Cache::add('key', 'value', $minutes);

`add` 메소드는 항목이 실제로 캐시에 **추가된** 경우에만 true를 반환합니다. 그렇지 않다면 `false`를 반환합니다.

#### 캐시에 존재하는지 확인하기

	if (Cache::has('key'))
	{
		//
	}

#### 캐시에서 하나의 항목을 찾기

	$value = Cache::get('key');

#### 찾는 항목이 없으면 기본값을 반환하기

	$value = Cache::get('key', 'default');

	$value = Cache::get('key', function() { return 'default'; });

#### 캐시에 영구적으로 저장하기

	Cache::forever('key', 'value');

간혹 캐시에서 항목을 조회할 때뿐만 아니라, 값이 없으면 기본값을 캐시에 저장하고 싶은 경우가 있습니다. 이럴 때 `Cache::remember` 메소드를 사용하면 됩니다:

	$value = Cache::remember('users', $minutes, function()
	{
		return DB::table('users')->get();
	});

`remember`와 `forever`를 결합하여 사용할 수도 있습니다:

	$value = Cache::rememberForever('users', function()
	{
		return DB::table('users')->get();
	});

캐시에 저장되는 모든 항목은 직렬화되고, 따라서 어떤 유형의 데이터도 자유롭게 저장할 수 있습니다.

#### 캐시에 저장된 아이템 가져오기

캐시에서 항목을 조회한 후에 삭제를 할 필요가 있으면, `pull` 메소드를 사용합니다:

	$value = Cache::pull('key');

#### 캐시에서 아이템 삭제하기

	Cache::forget('key');

#### 특정 저장소에 접근하기

다양한 캐시 저장소를 가지고 있다면, `store` 메소드를 통해서 특정 저장소에서 캐시를 가져올 수 있습니다:

	$value = Cache::store('foo')->get('key');

<!--chak-comment-캐시(Cache)-캐시-사용법-->

<a name="increments-and-decrements"></a>
## 증감 조작하기

`database`를 제외한 드라이버는 `increment` 및 `decrement` 조작을 지원하고 있습니다:

#### 값 증가시키기

	Cache::increment('key');

	Cache::increment('key', $amount);

#### 값 감소시키기

	Cache::decrement('key');

	Cache::decrement('key', $amount);

<!--chak-comment-캐시(Cache)-증감-조작하기-->

<a name="cache-tags"></a>
## 캐시 태그

> **참고:** 캐시 태그(cache tags)는 `file`과 `database` 드라이버를 사용하는 경우 지원되지 않습니다. 또한, "forever"로 저장되는 캐시에 많은 태그를 사용하면 기존 레코드를 자동으로 삭제하는 memcached와 같은 드라이버에서 최상의 성능을 낼 것입니다.

#### 태그된 캐시에 엑세스하기

캐시 태그는 캐시에 있는 관련된 아이템들을 태그 할 수 있도록 해주고, 같은 태그가 지정된 전체 캐시를 지울 수도 있습니다. 태그된 캐시에 액세스하려면 `tags` 메소드를 사용하십시오.

태그 이름 목록을 인수로 전달하거나, 배열로 만들어 전달하여 태그된 캐시를 저장할 수 있습니다:

	Cache::tags('people', 'authors')->put('John', $john, $minutes);

	Cache::tags(['people', 'artists'])->put('Anne', $anne, $minutes);

`remember`, `forever`, `rememberForever` 및 이들을 포함한 모든 캐시 저장 방법 및 태그를 조합하여 사용할 수 있습니다. 또한, increment 및 decrement 같은 다른 캐시 메소드도 마찬가지로 태그를 캐시 항목에 액세스 할 수 있습니다.

#### 태그된 캐시에 있는 아이템에 접근하기

태그가 지정된 캐쉬에 액세스하려면 저장된 것과 같은 순서로 태그의 목록을 전달합니다.

	$anne = Cache::tags('people', 'artists')->get('Anne');

	$john = Cache::tags(['people', 'authors'])->get('John');

이름 또는 이름의 목록을 지정하고 태그된 항목을 모두 지울 수 있습니다. 예를 들어, 다음 코드는 `people` 또는 `authors` 또는 둘 모두에 태그 된 모든 캐시 된 항목이 삭제됩니다. 그러므로 "Anne"과 "John"두 아이템은 캐시에서 제거됩니다:

	Cache::tags('people', 'authors')->flush();

한편, 다음 코드에서는 `authors` 태그된 캐시만 삭제하기 때문에 "John"은 삭제되지만 "Anne"는 남아 있습니다.

	Cache::tags('authors')->flush();

<!--chak-comment-캐시(Cache)-캐시-태그-->

<a name="cache-events"></a>
## 캐시 이벤트

캐시가 동작할 때 특정한 코드를 실행하기 위해서는 캐시에 의해서 실행되는 이벤트 리스너를 등록해야 합니다:

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

<!--chak-comment-캐시(Cache)-캐시-이벤트-->

<a name="database-cache"></a>
## 데이터베이스 캐시

`database` 캐시 드라이버를 사용할 때는 캐시 항목을 저장할 테이블을 준비해야 합니다. 아래에서 테이블을 만드는 `Schema`의 예를 확인할 수 있습니다:

	Schema::create('cache', function($table)
	{
		$table->string('key')->unique();
		$table->text('value');
		$table->integer('expiration');
	});

<a name="memcached-cache"></a>
#### Memcached Cache

Memcached 캐시를 사용하기 위해서는 [Memcached PECL package](http://pecl.php.net/package/memcached)가 설치되어 있어야 합니다.

기본 [설정](#configuration)은 TCP/IP 기반의 [Memcached::addServer](http://php.net/manual/en/memcached.addserver.php)을 사용합니다:

	'memcached' => array(
		array('host' => '127.0.0.1', 'port' => 11211, 'weight' => 100),
	),

`host` 옵션에 UNIX 소켓을 사용하도록 설정할 수 있으며 이 경우 `port`는 `0`으로 설정되어 있어야 합니다:

	'memcached' => array(
		array('host' => '/var/run/memcached/memcached.sock', 'port' => 0, 'weight' => 100),
	),

<a name="redis-cache"></a>
#### Redis Cache

[Redis 설정](/docs/redis#configuration)을 참고하십시오.

<!--chak-comment-캐시(Cache)-데이터베이스-캐시-->
