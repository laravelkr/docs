# HTTP Session
# HTTP 세션

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [Configuration](#configuration)
    - [설정하기](#configuration)
    - [Driver Prerequisites](#driver-prerequisites)
    - [드라이버 사전준비사항](#driver-prerequisites)
- [Using The Session](#using-the-session)
- [세션 사용하기](#using-the-session)
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
- [Adding Custom Session Drivers](#adding-custom-session-drivers)
- [사용자 정의 세션 드라이버 추가하기](#adding-custom-session-drivers)
    - [Implementing The Driver](#implementing-the-driver)
    - [드라이버 구현하기](#implementing-the-driver)
    - [Registering The Driver](#registering-the-driver)
    - [드라이버 등록하기](#registering-the-driver)

<a name="introduction"></a>
## Introduction
## 시작하기

Since HTTP driven applications are stateless, sessions provide a way to store information about the user across multiple requests. Laravel ships with a variety of session backends that are accessed through an expressive, unified API. Support for popular backends such as [Memcached](https://memcached.org), [Redis](https://redis.io), and databases is included out of the box.

HTTP 기반의 애플리케이션은 상태를 저장할수 없기 때문에, HTTP 여러 요청들에 관계없이 사용자의 정보를 저장하기위해서 세션이 사용됩니다. 라라벨은 풍부한 표현이 가능하며 일관된 API를 통해서 엑세스 되는 다양한 세션 백엔드를 제공합니다. 별다른 설정 없이도, 많이 알려진 [Memcached](https://memcached.org), [Redis](https://redis.io) 그리고 데이터베이스를 지원합니다.

<a name="configuration"></a>
### Configuration
### 설정하기

The session configuration file is stored at `config/session.php`. Be sure to review the options available to you in this file. By default, Laravel is configured to use the `file` session driver, which will work well for many applications.

세션의 설정파일은 `config/session.php`로 저장되어 있습니다. 이 파일에서 사용 가능한 옵션을 잘 살펴보십시오. 기본적으로 라라벨은 대부분의 애플리케이션에서 잘 작동 할 수 있도록 `file` 세션 드라이버를 사용하도록 설정되어 있습니다.

The session `driver` configuration option defines where session data will be stored for each request. Laravel ships with several great drivers out of the box:

세션 `driver` 설정 옵션은 각각의 요청-request에 따른 세션을 어디에 저장할 것인지 정의합니다. 라라벨은 별다른 설정 없이도 다양한 드라이버를 제공합니다.


- `file` - sessions are stored in `storage/framework/sessions`.
- `cookie` - sessions are stored in secure, encrypted cookies.
- `database` - sessions are stored in a relational database.
- `memcached` / `redis` - sessions are stored in one of these fast, cache based stores.
- `array` - sessions are stored in a PHP array and will not be persisted.



- `file` - `storage/framework/sessions` 디렉토리에 세션을 저장합니다.
- `cookie` - 암호화된 쿠키를 사용하여 안전하게 세션을 저장할 것입니다.
- `database` - 세션이 관계형 데이터베이스에 저장된다.
- `memcached` / `redis` - 빠르고, 캐시를 기반으로한 memcached, redis 에 저장합니다.
- `array` - 세션은 PHP 배열에 저장되며 지속되지 않습니다.


> {tip} The array driver is used during [testing](/docs/{{version}}/testing) and prevents the data stored in the session from being persisted.

> {tip} array 드라이버는 [테스트](/docs/{{version}}/testing)가 진행되는 동안 사용되고, 세션이 지속적으로 유지되지 않습니다.

<a name="driver-prerequisites"></a>
### Driver Prerequisites
### 드라이버의 사전준비사항

#### Database
#### 데이터베이스

When using the `database` session driver, you will need to create a table to contain the session items. Below is an example `Schema` declaration for the table:

`database` 세션 드라이버를 사용하는 경우, 세션을 저장할 수 있는 테이블을 생성할 필요가 있습니다. 다음의 `Schema` 예제를 통해서 테이블을 생성할 수 있습니다.

    Schema::create('sessions', function ($table) {
        $table->string('id')->unique();
        $table->unsignedInteger('user_id')->nullable();
        $table->string('ip_address', 45)->nullable();
        $table->text('user_agent')->nullable();
        $table->text('payload');
        $table->integer('last_activity');
    });

You may use the `session:table` Artisan command to generate this migration:

`session:table` 아티즌 명령어를 통해서 이 마이그레이션을 생성할 수 있습니다.

    php artisan session:table

    php artisan migrate

#### Redis
#### Redis

Before using Redis sessions with Laravel, you will need to either install the PhpRedis PHP extension via PECL or install the `predis/predis` package (~1.0) via Composer. For more information on configuring Redis, consult its [Laravel documentation page](/docs/{{version}}/redis#configuration).

Laravel과 함께 Redis 세션을 사용하기 전에 PECL을 통해 PhpRedis PHP 확장모듈을 설치하거나 Composer를 통해 `predis/predis` 패키지 (~ 1.0)를 설치해야합니다. Redis 설정에 대한 자세한 내용은 [Laravel의 Redis 설정](/docs/{{version}}/redis#configuration)을 참조하십시오.

> {tip} In the `session` configuration file, the `connection` option may be used to specify which Redis connection is used by the session.

> {tip} `session` 설정 파일에서 `connection` 옵션을 사용하여 세션에서 어떤 Redis 연결을 사용할지 지정할 수 있습니다.

<a name="using-the-session"></a>
## Using The Session
## 세션 사용하기

<a name="retrieving-data"></a>
### Retrieving Data
### 데이터 조회하기

There are two primary ways of working with session data in Laravel: the global `session` helper and via a `Request` instance. First, let's look at accessing the session via a `Request` instance, which can be type-hinted on a controller method. Remember, controller method dependencies are automatically injected via the Laravel [service container](/docs/{{version}}/container):

라라벨 안에서 세션 데이터를 조작하려면 두 가지 주요한 방법이 있습니다. 글로벌 `session` 헬퍼를 사용하는 것과 `Request` 인스턴스를 통한 방법입니다. 먼저 컨트롤러 메소드에서 타입-힌트 할 수 있는 `Request` 인스턴스를 통해서 세션에 엑세스 하는 것을 살펴보겠습니다. 유의할 것은, 컨트롤러 메소드는 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)를 통해서 자동으로 의존성 주입된다는 것입니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Http\Request;

    class UserController extends Controller
    {
        /**
         * Show the profile for the given user.
         *
         * @param  Request  $request
         * @param  int  $id
         * @return Response
         */
        public function show(Request $request, $id)
        {
            $value = $request->session()->get('key');

            //
        }
    }

When you retrieve an item from the session, you may also pass a default value as the second argument to the `get` method. This default value will be returned if the specified key does not exist in the session. If you pass a `Closure` as the default value to the `get` method and the requested key does not exist, the `Closure` will be executed and its result returned:

세션에서 특정 아이템을 찾을 때, `get` 메소드의 두번째 인자로 기본 값을 전달 할 수 있습니다. 이 기본값은 지정된 키가 세션 안에서 존재하지 않을 경우 반환될 것입니다. `get` 메소드의 기본값으로 `클로저`를 전달하는 경우 요청받은 키가 존재하지 않는다면, `클로저`가 실행결과를 반환할 것입니다.

    $value = $request->session()->get('key', 'default');

    $value = $request->session()->get('key', function () {
        return 'default';
    });

#### The Global Session Helper
#### 글로벌 세션 헬퍼

You may also use the global `session` PHP function to retrieve and store data in the session. When the `session` helper is called with a single, string argument, it will return the value of that session key. When the helper is called with an array of key / value pairs, those values will be stored in the session:

또한 세션에서 데이터를 찾거나, 저장하기 위해서 글로벌 `session` PHP 함수를 사용할 수도 있습니다. `session` 헬퍼를 하나의 문자열을 인자로 호출하게 되면, 해당하는 세션 키에 대한 값을 반환합니다. 헬퍼가 키 / 값 쌍으로 구성된 배열과 함께 호출되면 해당 세션 값이 세션에 저장됩니다.

    Route::get('home', function () {
        // Retrieve a piece of data from the session...
        $value = session('key');

        // Specifying a default value...
        $value = session('key', 'default');

        // Store a piece of data in the session...
        session(['key' => 'value']);
    });

> {tip} There is little practical difference between using the session via an HTTP request instance versus using the global `session` helper. Both methods are [testable](/docs/{{version}}/testing) via the `assertSessionHas` method which is available in all of your test cases.

> {tip} 글로벌 `session` 헬퍼를 사용하는 것에 비해서 HTTP 요청-request 인스턴스에서 세션을 사용하는 것에는 약간의 실질적인 차이가 있습니다. 두가지 메소드는 테스트 케이스 안에서 사용가능한 `assertSessionHas` 메소드를 통해서 [테스트가 가능합니다](/docs/{{version}}/testing).

#### Retrieving All Session Data
#### 모든 세션 데이터 조회하기

If you would like to retrieve all the data in the session, you may use the `all` method:

세션에서 모든 데이터를 찾고자 한다면 `all` 메소드를 사용하면 됩니다.

    $data = $request->session()->all();

#### Determining If An Item Exists In The Session
#### 세션에 아이템이 존재하는지 확인하기

To determine if an item is present in the session, you may use the `has` method. The `has` method returns `true` if the item is present and is not `null`:

세션안에 아이템이 존재하는지 확인하려면 `has` 메소드를 사용하면 됩니다. `has` 메소드는 아이템이 현재 존재하고 `null`이 아니라면 `true`를 반환합니다.

    if ($request->session()->has('users')) {
        //
    }

To determine if an item is present in the session, even if its value is `null`, you may use the `exists` method. The `exists` method returns `true` if the item is present:

값이 `null`이더라도 세션안에 아이템이 들어 있는지 확인하려면 `exists` 메소드를 사용할 수 있습니다. `exists` 메소드는 아이템이 존재한다면 `true`를 반환합니다.

    if ($request->session()->exists('users')) {
        //
    }

<a name="storing-data"></a>
### Storing Data
### 데이터 저장하기

To store data in the session, you will typically use the `put` method or the `session` helper:

세션에 데이터를 저장하기 위해서는, 일반적으로 `put` 메소드나 `session` 헬퍼를 사용합니다.

    // Via a request instance...
    $request->session()->put('key', 'value');

    // Via the global helper...
    session(['key' => 'value']);

#### Pushing To Array Session Values
#### 세션에 들어 있는 배열에 값 추가하기

The `push` method may be used to push a new value onto a session value that is an array. For example, if the `user.teams` key contains an array of team names, you may push a new value onto the array like so:

`push` 메소드는 세션에 들어 있는 배열에 새로운 값을 추가할 때 사용할 수 있습니다. 예를 들어 어떤 팀의 이름들에 대한 배열을 나타내는 `user.teams` 키가 있을 때, 다음과 같이 값을 추가할 수 있습니다.

    $request->session()->push('user.teams', 'developers');

#### Retrieving & Deleting An Item
#### 아이템을 조회 & 삭제하기

The `pull` method will retrieve and delete an item from the session in a single statement:

하나의 구문안에서 `pull` 메소드는 세션에서 아이템을 가져오면서 삭제합니다.

    $value = $request->session()->pull('key', 'default');

<a name="flash-data"></a>
### Flash Data
### 데이터 임시저장하기

Sometimes you may wish to store items in the session only for the next request. You may do so using the `flash` method. Data stored in the session using this method will be available immediately and during the subsequent HTTP request. After the subsequent HTTP request, the flashed data will be deleted. Flash data is primarily useful for short-lived status messages:

때때로 다음 요청에서만 사용하기 위한 값을 세션에 저장하고자 할 수 있습니다.  이럴때는 `flash` 메소드를 사용하면 됩니다. 이 메소드을 사용하여 세션에 저장된 데이터는 즉시 및 후속 HTTP 요청에서만 사용할 수 있고 후속 HTTP 요청이 완료 후 플래시 된 데이터는 삭제됩니다. 플래시 데이터는 주로 단기 상태 메시지에 유용합니다.

    $request->session()->flash('status', 'Task was successful!');

If you need to keep your flash data around for several requests, you may use the `reflash` method, which will keep all of the flash data for an additional request. If you only need to keep specific flash data, you may use the `keep` method:

여러 요청-request에 대해서 임시 데이터를 보다 오랫동안 유지할 필요가 있을 때, `reflash` 메소드를 사용하면 임시 데이터가 추가 요청-request에 대해서도 계속 유지됩니다. 지정한 임시 데이터만을 계속 유지하고자 한다면, `keep` 메소드를 사용하면 됩니다.

    $request->session()->reflash();

    $request->session()->keep(['username', 'email']);

<a name="deleting-data"></a>
### Deleting Data
### 데이터 삭제하기

The `forget` method will remove a piece of data from the session. If you would like to remove all data from the session, you may use the `flush` method:

`forget` 메소드는 세션에서 데이터를 삭제합니다. 세션에서 모든 데이터를 삭제하기를 원한다면 `flush` 메소드를 사용하면 됩니다.

    // Forget a single key...
    $request->session()->forget('key');

    // Forget multiple keys...
    $request->session()->forget(['key1', 'key2']);

    $request->session()->flush();

<a name="regenerating-the-session-id"></a>
### Regenerating The Session ID
### 세션 ID 다시 생성하기

Regenerating the session ID is often done in order to prevent malicious users from exploiting a [session fixation](https://owasp.org/www-community/attacks/Session_fixation) attack on your application.

세션 ID를 다시 생성하는 것은 종종 악의적인 사용자의 애플리케이션에 대한 [세션 fixation](https://owasp.org/www-community/attacks/Session_fixation) 공격을 방지하기 위해서 수행합니다.

Laravel automatically regenerates the session ID during authentication if you are using the built-in `LoginController`; however, if you need to manually regenerate the session ID, you may use the `regenerate` method.

라라벨에 포함된 `LoginController` 사용하는 경우 인증 과정에서 세션 ID가 자동으로 재생성됩니다; 그렇지만 세션 ID를 수동으로 다시 생성할 필요가 있다면 `regenerate` 메소드를 사용할 수 있습니다.

    $request->session()->regenerate();

<a name="adding-custom-session-drivers"></a>
## Adding Custom Session Drivers
## 사용자 정의 세션 드라이버 추가하기

<a name="implementing-the-driver"></a>
#### Implementing The Driver
#### 드라이버 구현하기

Your custom session driver should implement the `SessionHandlerInterface`. This interface contains just a few simple methods we need to implement. A stubbed MongoDB implementation looks something like this:

여러분의 사용자 정의 세션 드라이버는 `SessionHandlerInterface`를 구현해야 합니다. 이 인터페이스는 구현해야할 필요가 있는 몇가지 간단한 메소드를 포함하고 있습니다. MongoDB를 사용하는 구현체라면 다음과 같이 될 것입니다.

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

> {tip} Laravel does not ship with a directory to contain your extensions. You are free to place them anywhere you like. In this example, we have created an `Extensions` directory to house the `MongoSessionHandler`.

> {tip} 라라벨은 이러한 확장 기능을 담아둘 디렉토리를 제공하지는 않습니다. 원하는 곳 어디에든 자유롭게 구성할 수 있습니다. 이 예제에서는, `MongoSessionHandler`를 저장하기 위해서 `Extensions` 디렉토리를 만들었습니다.

Since the purpose of these methods is not readily understandable, let's quickly cover what each of the methods do:

이 메소드들의 목적을 쉽게 이해하기 어렵기 때문에, 각각의 메소드를 빠르게 살펴보겠습니다.


- The `open` method would typically be used in file based session store systems. Since Laravel ships with a `file` session driver, you will almost never need to put anything in this method. You can leave it as an empty stub. It is a fact of poor interface design (which we'll discuss later) that PHP requires us to implement this method.
- The `close` method, like the `open` method, can also usually be disregarded. For most drivers, it is not needed.
- The `read` method should return the string version of the session data associated with the given `$sessionId`. There is no need to do any serialization or other encoding when retrieving or storing session data in your driver, as Laravel will perform the serialization for you.
- The `write` method should write the given `$data` string associated with the `$sessionId` to some persistent storage system, such as MongoDB, Dynamo, etc.  Again, you should not perform any serialization - Laravel will have already handled that for you.
- The `destroy` method should remove the data associated with the `$sessionId` from persistent storage.
- The `gc` method should destroy all session data that is older than the given `$lifetime`, which is a UNIX timestamp. For self-expiring systems like Memcached and Redis, this method may be left empty.



- `open` 메소드는 일반적으로 파일 기반의 세션 저장 시스템에서 사용됩니다. 라라벨은 `file` 세션 드라이버를 제공하고 있기 때문에, 여러분은 거의 해당 메소드에 추가할 것이 없습니다. 이 메소드는 비어 있는 형태로 구성해도 됩니다. 이것은 좋지않은 인터페이스 디자인의 경우가 됩니다만 (나중에 설명합니다), PHP가 이 메소드를 구현하게끔 요구하고 있습니다.
- `close` 메소드역시 `open` 메소드와 마찬가지로 무시할 수 있습니다. 대부분의 드라이버에서는 필요가 없습니다.
- `read`  메소드는 주어진 `$sessionId` 에 해당하는 문자열의 세션 데이터의 문자열 버전을 반환해야합니다. 라라벨이 시리얼라이즈(직렬화)를 수행해주기 때문에 여러분이 작성한 드라이버에서 세션 데이터를 탐색하거나 저장하는데 시리얼라이즈나 다른 인코딩을 처리할 필요는 없습니다.
- `write` 메소드는 `$sessionId` 에 해당하는 `$data` 문자열을 MongoDB, Dynamo 등과 같은 시스템에 저장해야 합니다. 다시 말하지만, 라라벨이 이미 처리하기 때문에, 여러분은 어떠한 시리얼라이제이션-직렬화도 수행하지 말아야 합니다.
- `destory` 메소드는 저장소에서 주어진 `$sessionId` 에 해당하는 데이터를 삭제해야 합니다.
- `gc` 메소드는 UNIX 타임스탬프로 주어진 `$lifetime` 보다 오래된 모든 세션 데이터들을 제거해야합니다. Memcached와 Redis처럼 스스로 오래된 데이터를 삭제하는 시스템에서는, 이 메소드는 비워 둡니다.


<a name="registering-the-driver"></a>
#### Registering The Driver
#### 드라이버 등록하기

Once your driver has been implemented, you are ready to register it with the framework. To add additional drivers to Laravel's session backend, you may use the `extend` method on the `Session` [facade](/docs/{{version}}/facades). You should call the `extend` method from the `boot` method of a [service provider](/docs/{{version}}/providers). You may do this from the existing `AppServiceProvider` or create an entirely new provider:

드라이버를 구현하였다면, 프레임워크에 등록할 준비가 되었습니다. 라라벨의 세션 벡엔드에 추가적인 드라이버를 추가하려면 `Session` [파사드](/docs/{{version}}/facades)의 `extens` 메소드를 사용할 수 있습니다. [서비스 프로바이더](/docs/{{version}}/providers)의 `boot` 메소드에서 `extend` 메소드를 호출해야 합니다. 이미 존재하는 `AppServiceProvider` 또는 완전히 새로운 프로바이더를 생성하여 수행할 수 있습니다.

    <?php

    namespace App\Providers;

    use App\Extensions\MongoSessionHandler;
    use Illuminate\Support\Facades\Session;
    use Illuminate\Support\ServiceProvider;

    class SessionServiceProvider extends ServiceProvider
    {
        /**
         * Register any application services.
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
            Session::extend('mongo', function ($app) {
                // Return implementation of SessionHandlerInterface...
                return new MongoSessionHandler;
            });
        }
    }

Once the session driver has been registered, you may use the `mongo` driver in your `config/session.php` configuration file.

세션 드라이버를 등록했으면 `mongo` 드라이버를 `config/session.php` 설정 파일에서 사용할 수 있습니다.
