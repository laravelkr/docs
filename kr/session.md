# HTTP Session
# HTTP 세션

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [Configuration](#configuration)
    - [설정하기](#configuration)
    - [Driver Prerequisites](#driver-prerequisites)
    - [드라이버 사전준비사항](#driver-prerequisites)
- [Interacting With The Session](#interacting-with-the-session)
- [세션 상호작용](#interacting-with-the-session)
    - [Retrieving Data](#retrieving-data)
    - [데이터 조회하기](#retrieving-data)
    - [Storing Data](#storing-data)
    - [데이터 저장하기](#storing-data)
    - [Flash Data](#flash-data)
    - [데이터 임시저장하기](#flash-data)
    - [Deleting Data](#deleting-data)
    - [데이터 삭제하기](#deleting-data)
    - [Regenerating The Session ID](#regenerating-the-session-id)
    - [세션 ID 다시 생성하기](#regenerating-the-session-id)
- [Session Blocking](#session-blocking)
- [세션 블로킹](#session-blocking)
- [Adding Custom Session Drivers](#adding-custom-session-drivers)
- [사용자 정의 세션 드라이버 추가하기](#adding-custom-session-drivers)
    - [Implementing The Driver](#implementing-the-driver)
    - [드라이버 구현하기](#implementing-the-driver)
    - [Registering The Driver](#registering-the-driver)
    - [드라이버 등록하기](#registering-the-driver)

<a name="introduction"></a>
## Introduction
## 시작하기

Since HTTP driven applications are stateless, sessions provide a way to store information about the user across multiple requests. That user information is typically placed in a persistent store / backend that can be accessed from subsequent requests.

HTTP 기반의 애플리케이션은 상태를 저장할수 없기 때문에, HTTP 여러 요청들에 관계없이 사용자의 정보를 저장하기위해서 세션이 사용됩니다. 해당 사용자 정보는 일반적으로 후속 요청에서 엑세스 할 수 있는 영구 저장소 백엔드에 저장됩니다.

Laravel ships with a variety of session backends that are accessed through an expressive, unified API. Support for popular backends such as [Memcached](https://memcached.org), [Redis](https://redis.io), and databases is included.

라라벨은 풍부한 표현이 가능하며 일관된 API를 통해서 엑세스 되는 다양한 세션 백엔드를 제공합니다. 다음과 같이 많이 알려진 [Memcached](https://memcached.org), [Redis](https://redis.io) 및 데이터베이스를 지원합니다.

<a name="configuration"></a>
### Configuration
### 설정하기

Your application's session configuration file is stored at `config/session.php`. Be sure to review the options available to you in this file. By default, Laravel is configured to use the `file` session driver, which will work well for many applications. If your application will be load balanced across multiple web servers, you should choose a centralized store that all servers can access, such as Redis or a database.

세션 설정파일은 `config/session.php`에 저장되어 있습니다. 이 파일에서 사용 가능한 옵션을 잘 살펴보십시오. 기본적으로 라라벨은 대부분의 애플리케이션에서 잘 작동 할 수 있도록 `file` 세션 드라이버를 사용하도록 설정되어 있습니다. 애플리케이션이 다양한 웹 서버를 통해 로드 밸런싱되는 경우 Redis 또는 데이터베이스와 같이 모든 서버가 액세스할 수 있는 중앙 저장소를 선택해야 합니다.

The session `driver` configuration option defines where session data will be stored for each request. Laravel ships with several great drivers out of the box:

세션 `driver` 설정 옵션은 각각의 요청-request에 따른 세션을 어디에 저장할 것인지 정의합니다. 라라벨은 별다른 설정 없이도 다양한 드라이버를 제공합니다.

<div class="content-list" markdown="1">

- `file` - sessions are stored in `storage/framework/sessions`.
- `cookie` - sessions are stored in secure, encrypted cookies.
- `database` - sessions are stored in a relational database.
- `memcached` / `redis` - sessions are stored in one of these fast, cache based stores.
- `dynamodb` - sessions are stored in AWS DynamoDB.
- `array` - sessions are stored in a PHP array and will not be persisted.

</div>

<div class="content-list" markdown="1">

- `file` - `storage/framework/sessions` 디렉토리에 세션을 저장합니다.
- `cookie` - 암호화된 쿠키를 사용하여 안전하게 세션을 저장할 것입니다.
- `database` - 세션이 관계형 데이터베이스에 저장됩니다.
- `dynamodb` - 세션이 AWS DynamoDB에 저장됩니다.
- `memcached` / `redis` - 빠르고, 캐시를 기반으로한 memcached, redis 에 저장합니다.
- `array` - 세션은 PHP 배열에 저장되며 지속되지 않습니다.

</div>

> **Note**  
> The array driver is primarily used during [testing](/docs/{{version}}/testing) and prevents the data stored in the session from being persisted.

> **Note**  
> array 드라이버는 주로 [테스트](/docs/{{version}}/testing)가 진행되는 동안 사용되고, 세션이 지속적으로 유지되지 않습니다.

<a name="driver-prerequisites"></a>
### Driver Prerequisites
### 드라이버의 사전준비사항

<a name="database"></a>
#### Database
#### 데이터베이스

When using the `database` session driver, you will need to create a table to contain the session records. An example `Schema` declaration for the table may be found below:

`database` 세션 드라이버를 사용하는 경우, 세션 레코드를 저장할 수 있는 테이블을 생성해야 합니다. 다음의 `Schema` 예제를 통해 테이블을 생성할 수 있습니다.

    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Support\Facades\Schema;

    Schema::create('sessions', function (Blueprint $table) {
        $table->string('id')->primary();
        $table->foreignId('user_id')->nullable()->index();
        $table->string('ip_address', 45)->nullable();
        $table->text('user_agent')->nullable();
        $table->text('payload');
        $table->integer('last_activity')->index();
    });

You may use the `session:table` Artisan command to generate this migration. To learn more about database migrations, you may consult the complete [migration documentation](/docs/{{version}}/migrations):

`session:table` 아티즌 명령어를 통해서 마이그레이션을 생성할 수 있습니다. 데이터베이스 마이그레이션에 대해 자세히 알아보려면 [마이그레이션 문서](/docs/{{version}}/migrations)를 참조하세요.

```shell
php artisan session:table

php artisan migrate
```

<a name="redis"></a>
#### Redis
#### Redis

Before using Redis sessions with Laravel, you will need to either install the PhpRedis PHP extension via PECL or install the `predis/predis` package (~1.0) via Composer. For more information on configuring Redis, consult Laravel's [Redis documentation](/docs/{{version}}/redis#configuration).

Laravel과 함께 Redis 세션을 사용하기 전에 PECL을 통해 PhpRedis PHP 확장모듈을 설치하거나 Composer를 통해 `predis/predis` 패키지 (~ 1.0)를 설치해야합니다. Redis 설정에 대한 자세한 내용은 [Redis 문서](/docs/{{version}}/redis#configuration)을 참조하세요.

> **Note**  
> In the `session` configuration file, the `connection` option may be used to specify which Redis connection is used by the session.

> **Note**  
> `session` 설정 파일에서 `connection` 옵션을 사용하여 세션에서 어떤 Redis 연결을 사용할지 지정할 수 있습니다.

<a name="interacting-with-the-session"></a>
## Interacting With The Session
## 세션 상호작용

<a name="retrieving-data"></a>
### Retrieving Data
### 데이터 조회하기

There are two primary ways of working with session data in Laravel: the global `session` helper and via a `Request` instance. First, let's look at accessing the session via a `Request` instance, which can be type-hinted on a route closure or controller method. Remember, controller method dependencies are automatically injected via the Laravel [service container](/docs/{{version}}/container):

라라벨 안에서 세션 데이터를 조작하려면 두 가지 주요한 방법이 있습니다. 글로벌 `session` 헬퍼를 사용하는 것과 `Request` 인스턴스를 통한 방법입니다. 먼저 라우트 클로저 또는 컨트롤러 메소드에서 타입-힌트 할 수 있는 `Request` 인스턴스를 통해서 세션에 엑세스 하는 것을 살펴보겠습니다. 유의할 것은, 컨트롤러 메소드는 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)를 통해서 자동으로 의존성 주입된다는 것입니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Http\Request;
    use Illuminate\View\View;

    class UserController extends Controller
    {
        /**
         * Show the profile for the given user.
         */
        public function show(Request $request, string $id): View
        {
            $value = $request->session()->get('key');

            // ...

            $user = $this->users->find($id);

            return view('user.profile', ['user' => $user]);
        }
    }

When you retrieve an item from the session, you may also pass a default value as the second argument to the `get` method. This default value will be returned if the specified key does not exist in the session. If you pass a closure as the default value to the `get` method and the requested key does not exist, the closure will be executed and its result returned:

세션에서 특정 아이템을 찾을 때, `get` 메소드의 두번째 인자로 기본 값을 전달 할 수 있습니다. 이 기본값은 지정된 키가 세션 안에서 존재하지 않을 경우 반환될 것입니다. `get` 메소드의 기본값으로 클로저를 전달하는 경우 요청받은 키가 존재하지 않는다면, 클로저가 실행결과를 반환할 것입니다.

    $value = $request->session()->get('key', 'default');

    $value = $request->session()->get('key', function () {
        return 'default';
    });

<a name="the-global-session-helper"></a>
#### The Global Session Helper
#### 글로벌 세션 헬퍼

You may also use the global `session` PHP function to retrieve and store data in the session. When the `session` helper is called with a single, string argument, it will return the value of that session key. When the helper is called with an array of key / value pairs, those values will be stored in the session:

또한 세션에서 데이터를 찾거나, 저장하기 위해서 글로벌 `session` PHP 함수를 사용할 수도 있습니다. `session` 헬퍼를 하나의 문자열을 인자로 호출하게 되면, 해당하는 세션 키에 대한 값을 반환합니다. 헬퍼가 키 / 값 쌍으로 구성된 배열과 함께 호출되면 해당 세션 값이 세션에 저장됩니다.

    Route::get('/home', function () {
        // Retrieve a piece of data from the session...
        $value = session('key');

        // Specifying a default value...
        $value = session('key', 'default');

        // Store a piece of data in the session...
        session(['key' => 'value']);
    });

> **Note**  
> There is little practical difference between using the session via an HTTP request instance versus using the global `session` helper. Both methods are [testable](/docs/{{version}}/testing) via the `assertSessionHas` method which is available in all of your test cases.

> **Note**  
> 글로벌 `session` 헬퍼를 사용하는 것에 비해서 HTTP 요청-request 인스턴스에서 세션을 사용하는 것에는 약간의 실질적인 차이가 있습니다. 두가지 메소드는 테스트 케이스 안에서 사용가능한 `assertSessionHas` 메소드를 통해서 [테스트가 가능합니다](/docs/{{version}}/testing).

<a name="retrieving-all-session-data"></a>
#### Retrieving All Session Data
#### 모든 세션 데이터 조회하기

If you would like to retrieve all the data in the session, you may use the `all` method:

세션에서 모든 데이터를 찾고자 한다면 `all` 메소드를 사용하면 됩니다.

    $data = $request->session()->all();

<a name="determining-if-an-item-exists-in-the-session"></a>
#### Determining If An Item Exists In The Session
#### 세션에 아이템이 존재하는지 확인하기

To determine if an item is present in the session, you may use the `has` method. The `has` method returns `true` if the item is present and is not `null`:

세션안에 아이템이 존재하는지 확인하려면 `has` 메소드를 사용하면 됩니다. `has` 메소드는 아이템이 현재 존재하고 `null`이 아니라면 `true`를 반환합니다.

    if ($request->session()->has('users')) {
        // ...
    }

To determine if an item is present in the session, even if its value is `null`, you may use the `exists` method:

값이 `null`이더라도 세션안에 아이템이 들어 있는지 확인하려면 `exists` 메소드를 사용할 수 있습니다.

    if ($request->session()->exists('users')) {
        // ...
    }

To determine if an item is not present in the session, you may use the `missing` method. The `missing` method returns `true` if the item is not present:

세션에 항목이 없는지 확인하려면 `missing` 메소드를 사용할 수 있습니다. 아이템이 없는 경우 `missing` 메소드는 `true`를 반환 합니다.

    if ($request->session()->missing('users')) {
        // ...
    }

<a name="storing-data"></a>
### Storing Data
### 데이터 저장하기

To store data in the session, you will typically use the request instance's `put` method or the global `session` helper:

세션에 데이터를 저장하기 위해서는, 일반적으로 요청-request 인스턴스의 `put` 메소드 또는 글로벌 `session` 헬퍼를 사용합니다.

    // Via a request instance...
    $request->session()->put('key', 'value');

    // Via the global "session" helper...
    session(['key' => 'value']);

<a name="pushing-to-array-session-values"></a>
#### Pushing To Array Session Values
#### 세션에 들어 있는 배열에 값 추가하기

The `push` method may be used to push a new value onto a session value that is an array. For example, if the `user.teams` key contains an array of team names, you may push a new value onto the array like so:

`push` 메소드는 세션에 들어 있는 배열에 새로운 값을 추가할 때 사용할 수 있습니다. 예를 들어 어떤 팀의 이름들에 대한 배열을 나타내는 `user.teams` 키가 있을 때, 다음과 같이 값을 추가할 수 있습니다.

    $request->session()->push('user.teams', 'developers');

<a name="retrieving-deleting-an-item"></a>
#### Retrieving & Deleting An Item
#### 아이템 조회 & 삭제하기

The `pull` method will retrieve and delete an item from the session in a single statement:

하나의 구문안에서 `pull` 메소드는 세션에서 아이템을 가져오면서 삭제합니다.

    $value = $request->session()->pull('key', 'default');

<a name="#incrementing-and-decrementing-session-values"></a>
#### Incrementing & Decrementing Session Values
#### 세션 값 증가 & 감소

If your session data contains an integer you wish to increment or decrement, you may use the `increment` and `decrement` methods:

세션 데이터의 수를 증가 시키거나 감소 시키기 위해서는 `increment` 및 `decrement` 메소드를 사용할 수 있습니다.

    $request->session()->increment('count');

    $request->session()->increment('count', $incrementBy = 2);

    $request->session()->decrement('count');

    $request->session()->decrement('count', $decrementBy = 2);

<a name="flash-data"></a>
### Flash Data
### 데이터 임시저장하기

Sometimes you may wish to store items in the session for the next request. You may do so using the `flash` method. Data stored in the session using this method will be available immediately and during the subsequent HTTP request. After the subsequent HTTP request, the flashed data will be deleted. Flash data is primarily useful for short-lived status messages:

때때로 다음 요청에서만 사용하기 위한 값을 세션에 저장하고자 할 수 있습니다. 이럴때는 `flash` 메소드를 사용하면 됩니다. 이 메소드을 사용하여 세션에 저장된 데이터는 즉시 및 후속 HTTP 요청에서만 사용할 수 있고 후속 HTTP 요청이 완료 후 시 된 데이터는 삭제됩니다. 플래시 데이터는 주로 단기 상태 메시지에 유용합니다.

    $request->session()->flash('status', 'Task was successful!');

If you need to persist your flash data for several requests, you may use the `reflash` method, which will keep all of the flash data for an additional request. If you only need to keep specific flash data, you may use the `keep` method:

여러 요청-request에 대해서 임시 데이터를 유지할 필요가 있을 때, `reflash` 메소드를 사용하면 임시 데이터가 추가 요청-request에 대해서도 계속 유지됩니다. 지정한 임시 데이터만을 계속 유지하고자 한다면, `keep` 메소드를 사용하면 됩니다.

    $request->session()->reflash();

    $request->session()->keep(['username', 'email']);

To persist your flash data only for the current request, you may use the `now` method:

현재 요청에 대해서만 임시 데이터를 유지하려면 `now` 메소드를 사용할 수 있습니다.

    $request->session()->now('status', 'Task was successful!');

<a name="deleting-data"></a>
### Deleting Data
### 데이터 삭제하기

The `forget` method will remove a piece of data from the session. If you would like to remove all data from the session, you may use the `flush` method:

`forget` 메소드는 세션에서 데이터를 삭제합니다. 세션에서 모든 데이터를 삭제하기를 원한다면 `flush` 메소드를 사용하면 됩니다.

    // Forget a single key...
    $request->session()->forget('name');

    // Forget multiple keys...
    $request->session()->forget(['name', 'status']);

    $request->session()->flush();

<a name="regenerating-the-session-id"></a>
### Regenerating The Session ID
### 세션 ID 다시 생성하기

Regenerating the session ID is often done in order to prevent malicious users from exploiting a [session fixation](https://owasp.org/www-community/attacks/Session_fixation) attack on your application.

세션 ID를 다시 생성하는 것은 종종 악의적인 사용자의 애플리케이션에 대한 [세션 fixation](https://owasp.org/www-community/attacks/Session_fixation) 공격을 방지하기 위해서 수행합니다.

Laravel automatically regenerates the session ID during authentication if you are using one of the Laravel [application starter kits](/docs/{{version}}/starter-kits) or [Laravel Fortify](/docs/{{version}}/fortify); however, if you need to manually regenerate the session ID, you may use the `regenerate` method:

라라벨 [application starter kits](/docs/{{version}}/starter-kits) 및 [라라벨 Fortify](/docs/{{version}}/fortify) 중 하나를 사용하는 경우 라라벨은 인증 중에 세션 ID를 자동으로 재생성합니다. 그러나 세션 ID를 수동으로 다시 생성해야 하는 경우 `regenerate` 메소드를 사용할 수 있습니다.

    $request->session()->regenerate();

If you need to regenerate the session ID and remove all data from the session in a single statement, you may use the `invalidate` method:

세션 ID를 다시 생성하고 하나의 구문에서 세션의 모든 데이터를 제거해야 하는 경우 `invalidate` 메소드를 사용할 수 있습니다.

    $request->session()->invalidate();

<a name="session-blocking"></a>
## Session Blocking
## 세션 블로킹

> **Warning**  
> To utilize session blocking, your application must be using a cache driver that supports [atomic locks](/docs/{{version}}/cache#atomic-locks). Currently, those cache drivers include the `memcached`, `dynamodb`, `redis`, and `database` drivers. In addition, you may not use the `cookie` session driver.

> **Warning**  
> 세션 블로킹을 활용하려면 애플리케이션에서 [atomic locks](/docs/{{version}}/cache#atomic-locks)를 지원하는 캐시 드라이버를 사용해야합니다. 현재 이러한 캐시 드라이버에는 `memcached`, `dynamodb`, `redis` 및 `database` 드라이버가 포함됩니다. 또한 `cookie`세션 드라이버를 사용할 수 없습니다.

By default, Laravel allows requests using the same session to execute concurrently. So, for example, if you use a JavaScript HTTP library to make two HTTP requests to your application, they will both execute at the same time. For many applications, this is not a problem; however, session data loss can occur in a small subset of applications that make concurrent requests to two different application endpoints which both write data to the session.

기본적으로 라라벨은 동일한 세션을 사용하는 요청이 동시에 실행되도록 허용합니다. 예를 들어 자바 스크립트 HTTP 라이브러리를 사용하여 애플리케이션에 두 개의 HTTP 요청을 수행하면 두 요청이 동시에 실행됩니다. 많은 애플리케이션에서 이것이 문제가 되지 않습니다. 그러나 세션 데이터 손실은 둘 다 세션에 데이터를 쓰는 두 개의 서로 다른 애플리케이션 엔드포인트에 동시에 요청하는 애플리케이션의 작은 하위 집합에서 발생할 수 있습니다.

(역자주: php는 기본 세션을 사용할 때 동시에 여러 요청이 들어올 경우 세션데이터의 정합성을 위해 세션락이 걸리며 뒤에 실행되는 요청들은 모두 대기가 됩니다. 추가적으로 [제니퍼소프트의 관련글](https://jennifersoft.com/ko/blog/tech/2019-03-21/)을 읽어보시면 좋을 것 같습니다. 라라벨은 반대로 세션락을 걸지 않아서 여기서는 락을 걸어주는 방식을 안내하고 있습니다)

To mitigate this, Laravel provides functionality that allows you to limit concurrent requests for a given session. To get started, you may simply chain the `block` method onto your route definition. In this example, an incoming request to the `/profile` endpoint would acquire a session lock. While this lock is being held, any incoming requests to the `/profile` or `/order` endpoints which share the same session ID will wait for the first request to finish executing before continuing their execution:

이를 완화하기 위해 라라벨은 주어진 세션에 대한 동시 요청을 제한 할 수있는 기능을 제공합니다. 시작하려면 단순히 `block` 메소드를 라우트 정의에 연결하면됩니다. 다음 예제에서 `/profile` 엔드 포인트에 대한 수신 요청은 세션 잠금을 획득합니다. 이 잠금이 유지되는 동안 동일한 세션 ID를 공유하는 `/profile` 또는 `/order` 엔드 포인트로 들어오는 모든 요청은 실행을 계속하기 전에 첫 번째 요청의 실행이 완료 될 때까지 기다립니다.

    Route::post('/profile', function () {
        // ...
    })->block($lockSeconds = 10, $waitSeconds = 10)

    Route::post('/order', function () {
        // ...
    })->block($lockSeconds = 10, $waitSeconds = 10)

The `block` method accepts two optional arguments. The first argument accepted by the `block` method is the maximum number of seconds the session lock should be held for before it is released. Of course, if the request finishes executing before this time the lock will be released earlier.

`block` 메소드는 두 개의 선택적 인수를 허용합니다. `block`메소드에서 허용하는 첫 번째 인수는 세션 잠금이 해제되기 전에 유지되어야하는 최대 시간 (초)입니다. 물론, 시간 전에 요청 실행이 완료되면 잠금이 더 일찍 해제됩니다.

The second argument accepted by the `block` method is the number of seconds a request should wait while attempting to obtain a session lock. An `Illuminate\Contracts\Cache\LockTimeoutException` will be thrown if the request is unable to obtain a session lock within the given number of seconds.

`block`메소드에서 허용하는 두 번째 인수는 세션 잠금을 얻으려고 시도하는 동안 요청이 기다려야하는 시간 (초)입니다. 요청이 주어진 시간 (초) 내에 세션 잠금을 얻을 수 없으면 `Illuminate\Contracts\Cache\LockTimeoutException`이 발생합니다.

If neither of these arguments is passed, the lock will be obtained for a maximum of 10 seconds and requests will wait a maximum of 10 seconds while attempting to obtain a lock:

이 인수 중 어느 것도 전달되지 않으면 최대 10 초 동안 잠금을 획득하고 요청은 잠금 획득을 시도하는 동안 최대 10 초 동안 대기합니다.

    Route::post('/profile', function () {
        // ...
    })->block()

<a name="adding-custom-session-drivers"></a>
## Adding Custom Session Drivers
## 사용자 정의 세션 드라이버 추가하기

<a name="implementing-the-driver"></a>
#### Implementing The Driver
#### 드라이버 구현하기

If none of the existing session drivers fit your application's needs, Laravel makes it possible to write your own session handler. Your custom session driver should implement PHP's built-in `SessionHandlerInterface`. This interface contains just a few simple methods. A stubbed MongoDB implementation looks like the following:

기존 세션 드라이버가 애플리케이션의 요구 사항에 맞지 않는 경우 라라벨은 고유한 세션 핸들러를 작성할 수 있습니다. 사용자 정의 세션 드라이버는 PHP의 내장된 `SessionHandlerInterface`를 구현해야 합니다. 이 인터페이스에는 몇 가지 간단한 메소드만 포함되어 있습니다. 스텁된 MongoDB 구현체는 다음과 같습니다.

    <?php

    namespace App\Extensions;

    class MongoSessionHandler implements \SessionHandlerInterface
    {
        public function open($savePath, $sessionName) {}
        public function close() {}
        public function read($sessionId) {}
        public function write($sessionId, $data) {}
        public function destroy($sessionId) {}
        public function gc($lifetime) {}
    }

> **Note**  
> Laravel does not ship with a directory to contain your extensions. You are free to place them anywhere you like. In this example, we have created an `Extensions` directory to house the `MongoSessionHandler`.

> **Note**  
> 라라벨은 이러한 확장 기능을 담아둘 디렉토리를 제공하지는 않습니다. 원하는 곳 어디에든 자유롭게 구성할 수 있습니다. 이 예제에서는, `MongoSessionHandler`를 저장하기 위해서 `Extensions` 디렉토리를 만들었습니다.

Since the purpose of these methods is not readily understandable, let's quickly cover what each of the methods do:

이 메소드들의 목적을 쉽게 이해하기 어렵기 때문에, 각각의 메소드를 빠르게 살펴보겠습니다.

<div class="content-list" markdown="1">

- The `open` method would typically be used in file based session store systems. Since Laravel ships with a `file` session driver, you will rarely need to put anything in this method. You can simply leave this method empty.
- The `close` method, like the `open` method, can also usually be disregarded. For most drivers, it is not needed.
- The `read` method should return the string version of the session data associated with the given `$sessionId`. There is no need to do any serialization or other encoding when retrieving or storing session data in your driver, as Laravel will perform the serialization for you.
- The `write` method should write the given `$data` string associated with the `$sessionId` to some persistent storage system, such as MongoDB or another storage system of your choice.  Again, you should not perform any serialization - Laravel will have already handled that for you.
- The `destroy` method should remove the data associated with the `$sessionId` from persistent storage.
- The `gc` method should destroy all session data that is older than the given `$lifetime`, which is a UNIX timestamp. For self-expiring systems like Memcached and Redis, this method may be left empty.

</div>

- `open` 메소드는 일반적으로 파일 기반의 세션 저장 시스템에서 사용됩니다. 라라벨은 `file` 세션 드라이버를 제공하고 있기 때문에, 여러분은 해당 메소드에 추가할 것이 없습니다. 이 메소드는 비어 있는 형태로 구성해도 됩니다. 
- `close` 메소드 역시 `open` 메소드와 마찬가지로 무시할 수 있습니다. 대부분의 드라이버에서는 필요가 없습니다.
- `read`  메소드는 주어진 `$sessionId` 에 해당하는 문자열의 세션 데이터의 문자열 버전을 반환해야합니다. 라라벨이 시리얼라이즈(직렬화)를 수행해주기 때문에 여러분이 작성한 드라이버에서 세션 데이터를 탐색하거나 저장하는데 시리얼라이즈나 다른 인코딩을 처리할 필요는 없습니다.
- `write` 메소드는 `$sessionId` 에 해당하는 `$data` 문자열을 MongoDB 등과 같은 시스템에 저장해야 합니다. 다시 말하지만, 라라벨이 이미 처리하기 때문에, 여러분은 어떠한 시리얼라이제이션-직렬화도 수행하지 말아야 합니다.
- `destory` 메소드는 저장소에서 주어진 `$sessionId` 에 해당하는 데이터를 삭제해야 합니다.
- `gc` 메소드는 UNIX 타임스탬프로 주어진 `$lifetime` 보다 오래된 모든 세션 데이터들을 제거해야합니다. Memcached와 Redis처럼 스스로 오래된 데이터를 삭제하는 시스템에서는, 이 메소드는 비워 둡니다.

<a name="registering-the-driver"></a>
#### Registering The Driver
#### 드라이버 등록하기

Once your driver has been implemented, you are ready to register it with Laravel. To add additional drivers to Laravel's session backend, you may use the `extend` method provided by the `Session` [facade](/docs/{{version}}/facades). You should call the `extend` method from the `boot` method of a [service provider](/docs/{{version}}/providers). You may do this from the existing `App\Providers\AppServiceProvider` or create an entirely new provider:

드라이버를 구현하였다면, 라라벨에 등록할 준비가 되었습니다. 라라벨의 세션 벡엔드에 추가적인 드라이버를 추가하려면 `Session` [파사드](/docs/{{version}}/facades)의 `extend` 메소드를 사용할 수 있습니다.[서비스 프로바이더](/docs/{{version}}/providers)의 `boot` 메소드에서 `extend` 메소드를 호출해야 합니다. 이미 존재하는 `App\Providers\AppServiceProvider` 또는 완전히 새로운 프로바이더를 생성하여 수행할 수 있습니다.

    <?php

    namespace App\Providers;

    use App\Extensions\MongoSessionHandler;
    use Illuminate\Contracts\Foundation\Application;
    use Illuminate\Support\Facades\Session;
    use Illuminate\Support\ServiceProvider;

    class SessionServiceProvider extends ServiceProvider
    {
        /**
         * Register any application services.
         */
        public function register(): void
        {
            // ...
        }

        /**
         * Bootstrap any application services.
         */
        public function boot(): void
        {
            Session::extend('mongo', function (Application $app) {
                // Return an implementation of SessionHandlerInterface...
                return new MongoSessionHandler;
            });
        }
    }

Once the session driver has been registered, you may use the `mongo` driver in your `config/session.php` configuration file.

세션 드라이버를 등록했으면 `mongo` 드라이버를 `config/session.php` 설정 파일에서 사용할 수 있습니다.
