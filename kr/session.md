# Session
# 세션

- [Introduction](#introduction)
- [소개](#introduction)
- [Configuration](#configuration)
- [설정하기](#configuration)
- [Basic Usage](#basic-usage)
- [기본적인 사용법](#basic-usage)
    - [Flash Data](#flash-data)
    - [임시 데이터](#flash-data)
- [Adding Custom Session Drivers](#adding-custom-session-drivers)
- [사용자 정의 세션 드라이버 추가하기](#adding-custom-session-drivers)

<a name="introduction"></a>
## Introduction
## 소개

Since HTTP driven applications are stateless, sessions provide a way to store information about the user across requests. Laravel ships with a variety of session back-ends available for use through a clean, unified API. Support for popular back-ends such as [Memcached](http://memcached.org), [Redis](http://redis.io), and databases is included out of the box.

HTTP 기반의 애플리케이션은 상태를 저장할수 없기 때문에, HTTP 요청들에 관계없이 사용자의 정보를 저장하기위해서 세션이 사용됩니다. 라라벨은 다양한 벡엔드 세션들에 관계없이 간결하고 통일된 API를 제공합니다. 많이 알려진  [Memcached](http://memcached.org), [Redis](http://redis.io) 그리고 데이터베이스를 별다른 설정 없이 세션 시스템으로 사용할 수 있습니다.

<a name="configuration"></a>
### Configuration
### 설정하기

The session configuration file is stored at `config/session.php`. Be sure to review the well documented options available to you in this file. By default, Laravel is configured to use the `file` session driver, which will work well for many applications. In production applications, you may consider using the `memcached` or `redis` drivers for even faster session performance.

세션의 설정파일은 `config/session.php`로 저장되어 있습니다. 이 파일에는 각각의 옵션에 대한 정리된 문서가 포함되어 있으므로 잘 확인하시기 바랍니다. 많은 애플리케이션에서 작동이 가능하도록 라라벨에서는 기본적으로 `file` 세션 드라이버를 사용합니다. 실서비스용 애플리케이션에서는 보다 나은 세션 처리 성능을 위해서 `memcached` 나 `redis` 드라이버를 사용하는 것을 고려하십시오.

The session `driver` defines where session data will be stored for each request. Laravel ships with several great drivers out of the box:

세션 `driver` 정의는 각각의 요청에 따른 세션을 어디에 저장할 것인지 정의합니다. 라라벨은 별다른 설정 없이도 다양한 드라이버를 제공합니다.

- `file` - sessions are stored in `storage/framework/sessions`.
- `cookie` - sessions are stored in secure, encrypted cookies.
- `database` - sessions are stored in a database used by your application.
- `memcached` / `redis` - sessions are stored in one of these fast, cache based stores.
- `array` - sessions are stored in a simple PHP array and will not be persisted across requests.

- `file` - `storage/framework/sessions` 디렉토리에 세션을 저장합니다.
- `cookie` - 암호화된 쿠키를 사용하여 안전하게 세션을 저장할 것입니다.
- `database` - 애플리케이션이 사용하는 데이터베이스에 저장합니다. .
- `memcached` / `redis` - 빠르고, 캐시를 기반으로한 memcached, redis 에 저장합니다.
- `array` - PHP 배열에 세션을 저장합니다. 이경우 세션이 요청에 대해서 지속적으로 유지 되지 않습니다.

> **Note:** The array driver is typically used for running [tests](/docs/{{version}}/testing) to prevent session data from persisting.

> **주의:** array 드라이버는 일반적으로 [테스트](/docs/{{version}}/testing)용으로 사용되고, 세션이 유지되지 않습니다.

### Driver Prerequisites
### 드라이버별 요구사항

#### Database
#### 데이터베이스

When using the `database` session driver, you will need to setup a table to contain the session items. Below is an example `Schema` declaration for the table:

`database` 세션 드라이버를 사용하는 경우, 세션을 저장할 수 있는 테이블을 구성할 필요가 있을 것입니다. 다음의 `Schema` 예제를 통해서 테이블을 생성할 수 있습니다.

    Schema::create('sessions', function ($table) {
        $table->string('id')->unique();
        $table->integer('user_id')->nullable();
        $table->string('ip_address', 45)->nullable();
        $table->text('user_agent')->nullable();
        $table->text('payload');
        $table->integer('last_activity');
    });

You may use the `session:table` Artisan command to generate this migration for you!

`session:table` 아티즌 명령어를 통해서 이 마이그레이션을 생성할 수 있습니다.

    php artisan session:table

    composer dump-autoload

    php artisan migrate

#### Redis
#### Redis

Before using Redis sessions with Laravel, you will need to install the `predis/predis` package (~1.0) via Composer.

라라벨에서 Redis 세션을 사용하기 전에, Composer 를 통해서 `predis/predis` 패키지 (~1.0)을 설치할 필요가 있습니다.

### Other Session Considerations
### 다른 세션에서의 고려사항

The Laravel framework uses the `flash` session key internally, so you should not add an item to the session by that name.

라라벨 프레임워크는 내부적으로 `flash`라는 세션키를 사용하고 있어서, 이 이름을 아이템 이름으로 하여 세션에 추가해서는 안됩니다.

If you need all stored session data to be encrypted, set the `encrypt` configuration option to `true`.

만약 세션에 저장하는 모든 데이터를 암호화 할 필요가 있다면, `encrypt` 옵션을 `true` 로 설정하면 됩니다.

<a name="basic-usage"></a>
## Basic Usage
## 기본적인 사용법

#### Accessing The Session
#### 세션에 엑세스 하기

First, let's access the session. We can access the session instance via the HTTP request, which can be type-hinted on a controller method. Remember, controller method dependencies are injected via the Laravel [service container](/docs/{{version}}/container):

먼저, 세션에 엑세스해 보겠습니다. 컨트롤러 메소드에서 타입-힌트를 사용하여 HTTP request 를 통해서 세션 인스턴스에 엑세스 할 수 있습니다. 유의할 것은, 컨트롤러 메소드는 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)를 통한 의존성 주입된다는 것입니다.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;
    use App\Http\Controllers\Controller;

    class UserController extends Controller
    {
        /**
         * Show the profile for the given user.
         *
         * @param  Request  $request
         * @param  int  $id
         * @return Response
         */
        public function showProfile(Request $request, $id)
        {
            $value = $request->session()->get('key');

            //
        }
    }

When you retrieve a value from the session, you may also pass a default value as the second argument to the `get` method. This default value will be returned if the specified key does not exist in the session. If you pass a `Closure` as the default value to the `get` method, the `Closure` will be executed and its result returned:

세션에서 특정 값을 찾을 때, `get` 메소드의 두번째 인자로 기본 값을 전달 할 수 있습니다. 이 기본값은 지정된 키가 세션 안에서 존재하지 않을 경우 반환될 것입니다. `get` 메소드의 기본값으로 `Closure`를 전달한다면, `Closure` 는 실행 결과를 반환할 것입니다.

    $value = $request->session()->get('key', 'default');

    $value = $request->session()->get('key', function() {
        return 'default';
    });

If you would like to retrieve all data from the session, you may use the `all` method:

세션에서 모든 데이터를 찾고자 한다면 `all` 메소드를 사용하면 됩니다.

    $data = $request->session()->all();

You may also use the global `session` PHP function to retrieve and store data in the session:

또한 세션에서 데이터를 찾거나, 저장하기 위해서 글로벌 `session` PHP 함수를 사용할 수도 있습니다.

    Route::get('home', function () {
        // Retrieve a piece of data from the session...
        $value = session('key');

        // Store a piece of data in the session...
        session(['key' => 'value']);
    });

#### Determining If An Item Exists In The Session
#### 세션에 아이템이 존재하는지 확인하기

The `has` method may be used to check if an item exists in the session. This method will return `true` if the item exists:

`has` 메소드는 세션안에 아이템이 존재하는지 확인하는데 사용됩니다. 이 메소드는 아이템이 존재하는 경우 `true` 를 반환합니다.

    if ($request->session()->has('users')) {
        //
    }

#### Storing Data In The Session
#### 세션에 데이터 저장하기

Once you have access to the session instance, you may call a variety of functions to interact with the underlying data. For example, the `put` method stores a new piece of data in the session:

세션 인스턴스에 엑세스 한 뒤에, 데이터를 처리하기 위한 다양한 함수들을 호출할 수 있습니다. 예를 들어 `put` 메소드는 세션에 새로운 데이터를 저장합니다.

    $request->session()->put('key', 'value');

#### Pushing To Array Session Values
#### 세션에 들어 있는 배열에 값 추가하기

The `push` method may be used to push a new value onto a session value that is an array. For example, if the `user.teams` key contains an array of team names, you may push a new value onto the array like so:

`push` 메소드는 세션에 들어 있는 배열에 새로운 값을 추가할 때 사용할 수 있습니다. 예를 들어 어떤 팀의 이름들에 대한 배열을 나타내는 `user.teams` 키가 있을 때, 다음과 같이 값을 추가할 수 있습니다.

    $request->session()->push('user.teams', 'developers');

#### Retrieving And Deleting An Item
#### 아이템을 가져오면서 삭제하기

The `pull` method will retrieve and delete an item from the session:

`pull` 메소드는 세션에서 아이템을 가져오면서 삭제합니다.

    $value = $request->session()->pull('key', 'default');

#### Deleting Items From The Session
#### 세션에서 아이템 삭제하기 =

The `forget` method will remove a piece of data from the session. If you would like to remove all data from the session, you may use the `flush` method:

`forget` 메소드는 세션에서 데이터를 삭제합니다. 세션에서 모든 데이터를 삭제하기를 원한다면 `flush` 메소드를 사용하면 됩니다:

    $request->session()->forget('key');

    $request->session()->flush();

#### Regenerating The Session ID
#### 세션 ID 다시 생성하기=

If you need to regenerate the session ID, you may use the `regenerate` method:

세션 ID를 다시 생성할 필요가 있다면, `regenerate` 메소드를 사용하면 됩니다:

    $request->session()->regenerate();

<a name="flash-data"></a>
### Flash Data
### 임시 데이터

Sometimes you may wish to store items in the session only for the next request. You may do so using the `flash` method. Data stored in the session using this method will only be available during the subsequent HTTP request, and then will be deleted. Flash data is primarily useful for short-lived status messages:

때로는 바로 다음번의 요청에서만 사용하기 위해서 세션에 아이템을 저장할 수 있습니다. 바로 `flash` 메소드를 사용하는 것입니다. 이 메소드를 사용하여 세션에 저장된 데이터는 바로 이어지는 HTTP 요청에 대해서만 사용이 가능하고, 이후에는 삭제됩니다. 임시 데이터는 주로 상태 메세지등 잠깐동안 사용하데 유용합니다:

    $request->session()->flash('status', 'Task was successful!');

If you need to keep your flash data around for even more requests, you may use the `reflash` method, which will keep all of the flash data around for an additional request. If you only need to keep specific flash data around, you may use the `keep` method:

임시 데이터를 보다 오랫동안 유지할 필요가 있을 때, `reflash` 메소드를 사용하면 임시 디이터가 추가적인 요청에 대해서도 계속 유지됩니다. 지정한 임시 데이터를 계속 유지하고자 한다면, `keep` 메소드를 사용하면 됩니다:

    $request->session()->reflash();

    $request->session()->keep(['username', 'email']);

<a name="adding-custom-session-drivers"></a>
## Adding Custom Session Drivers
## 사용자 정의 세션 드라이버 추가하기

To add additional drivers to Laravel's session back-end, you may use the `extend` method on the `Session` [facade](/docs/{{version}}/facades). You can call the `extend` method from the `boot` method of a [service provider](/docs/{{version}}/providers):

라라벨의 세션기능에 추가적인 드라이버를 더하고자 하려면, `Session` [파사드](/docs/{{version}}/facades)에 `extend` 메소드를 사용하면 됩니다. [서비스 프로바이더](/docs/{{version}}/providers)의 `boot` 메소드에서 `extend` 메소드를 호출할 수 있습니다:

    <?php

    namespace App\Providers;

    use Session;
    use App\Extensions\MongoSessionStore;
    use Illuminate\Support\ServiceProvider;

    class SessionServiceProvider extends ServiceProvider
    {
        /**
         * Perform post-registration booting of services.
         *
         * @return void
         */
        public function boot()
        {
            Session::extend('mongo', function($app) {
                // Return implementation of SessionHandlerInterface...
                return new MongoSessionStore;
            });
        }

        /**
         * Register bindings in the container.
         *
         * @return void
         */
        public function register()
        {
            //
        }
    }

Note that your custom session driver should implement the `SessionHandlerInterface`. This interface contains just a few simple methods we need to implement. A stubbed MongoDB implementation looks something like this:

사용자 지정 세션 드라이버는 `SessionHandlerInterface` 를 구현한다는 것을 기억하십시오. 이 인터페이스에는 구현해야하는 간단한 몇 가지 메소드가 포함되어 있습니다. MongoDB를 사용하는 구현체라면 다음과 같이 될 것입니다.

    <?php

    namespace App\Extensions;

    class MongoHandler implements SessionHandlerInterface
    {
        public function open($savePath, $sessionName) {}
        public function close() {}
        public function read($sessionId) {}
        public function write($sessionId, $data) {}
        public function destroy($sessionId) {}
        public function gc($lifetime) {}
    }

Since these methods are not as readily understandable as the cache `StoreInterface`, let's quickly cover what each of the methods do:

이 메소드들은 캐시의 `StoreInterface` 처럼 쉽게 이해 수 없기 때문에 각각의 메소드들에 대해서 간단하게 설명합니다.

- The `open` method would typically be used in file based session store systems. Since Laravel ships with a `file` session driver, you will almost never need to put anything in this method. You can leave it as an empty stub. It is simply a fact of poor interface design (which we'll discuss later) that PHP requires us to implement this method.
- The `close` method, like the `open` method, can also usually be disregarded. For most drivers, it is not needed.
- The `read` method should return the string version of the session data associated with the given `$sessionId`. There is no need to do any serialization or other encoding when retrieving or storing session data in your driver, as Laravel will perform the serialization for you.
- The `write` method should write the given `$data` string associated with the `$sessionId` to some persistent storage system, such as MongoDB, Dynamo, etc.
- The `destroy` method should remove the data associated with the `$sessionId` from persistent storage.
- The `gc` method should destroy all session data that is older than the given `$lifetime`, which is a UNIX timestamp. For self-expiring systems like Memcached and Redis, this method may be left empty.

- `open` 메소드는 일반적으로 파일 기반의 세션 저장 시스템에서 사용됩니다. 라라벨은 `file` 세션 드라이버를 제공하고 있기 때문에, 여러분은 거의 해당 메소드에 추가할 것이 없습니다. 이 메소드는 비어 있는 형태로 구성해도 됩니다. 이것은 좋지않은 인터페이스 디자인의 경우가 됩니다만 (나중에 설명합니다), PHP가 이 메소드를 구현하게끔 요구하고 있습니다.
- The `close` method, like the `open` method, can also usually be disregarded. For most drivers, it is not needed.
- `close` 메소드역시 `open` 메소드와 마찬가지로 무시할 수 있습니다. 대부분의 드라이버에서는 필요가 없습니다.
- `read`  메소드는 주어진 `$sessionId` 에 해당하는 문자열의 세션 데이터의 문자열 버전을 반환해야합니다. 라라벨이 시리얼라이즈(직렬화)를 수행해주기 때문에 여러분이 작성한 드라이버에서 세션 데이터를 탐색하거나 저장하는데 시리얼라이즈나 다른 인코딩을 처리할 필요는 없습니다.
- `write` 메소드는 `$sessionId` 에 해당하는 `$data` 문자열을 MongoDB, Dynamo 등과 같은 시스템에 저장해야 합니다.
- The `destroy` method should remove the data associated with the `$sessionId` from persistent storage.
- `destory` 메소드는 저장소에서 주어진 `$sessionId` 에 해당하는 데이터를 삭제해야 합니다.
- `gc` 메소드는 UNIX 타임스탬프로 주어진 `$lifetime` 보다 오래된 모든 세션 데이터들을 제거해야합니다. Memcached와 Redis처럼 스스로 오래된 데이터를 삭제하는 시스템에서는, 이 메소드는 비워 둡니다.

Once the session driver has been registered, you may use the `mongo` driver in your `config/session.php` configuration file.

세션 드라이버를 등록했으면 `mongo` 드라이버를 `config/session.php` 설정 파일에서 사용할 수 있습니다.
