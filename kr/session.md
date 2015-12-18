# Session
# 세션

- [Introduction](#introduction)
- [소개](#introduction)
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

HTTP 기반의 어플리케이션은 상태를 저장할수 없기 때문에, HTTP 요청들에 관계없이 사용자의 정보를 저장하기위해서 세션이 사용됩니다. 라라벨은 다양한 벡엔드 세션들에 관계없이 간결하고 통일된 API를 제공합니다. 많이 알려진  [Memcached](http://memcached.org), [Redis](http://redis.io) 그리고 데이터베이스를 별다른 설정 없이 세션 시스템으로 사용할 수 있습니다. 

### Configuration
### 설정하기

The session configuration file is stored at `config/session.php`. Be sure to review the well documented options available to you in this file. By default, Laravel is configured to use the `file` session driver, which will work well for many applications. In production applications, you may consider using the `memcached` or `redis` drivers for even faster session performance.

세션의 설정파일은 `config/session.php`로 저장되어 있습니다. 이 파일에는 각각의 옵션에 대한 정리된 문서가 포함되어 있으므로 잘 확인하시기 바랍니다. 많은 어플리케이션에서 작동이 가능하도록 라라벨에서는 기본적으로 `file` 세션 드라이버를 사용합니다. 실서비스용 어플리케이션에서는 보다 나은 세션 처리 성능을 위해서 `memcached` 나 `redis` 드라이버를 사용하는 것을 고려하십시오. 

The session `driver` defines where session data will be stored for each request. Laravel ships with several great drivers out of the box:

세션 `driver` 정의는 각각의 요청에 따른 세션을 어디에 저장할 것인지 정의합니다. 라라벨은 별다른 설정 없이도 다양한 드라이버를 제공합니다. 

<div class="content-list" markdown="1">
- `file` - sessions are stored in `storage/framework/sessions`.
- `cookie` - sessions are stored in secure, encrypted cookies.
- `database` - sessions are stored in a database used by your application.
- `memcached` / `redis` - sessions are stored in one of these fast, cache based stores.
- `array` - sessions are stored in a simple PHP array and will not be persisted across requests.
</div>

<div class="content-list" markdown="1">
- `file` - `storage/framework/sessions` 디렉토리에 세션을 저장합니다.
- `cookie` - 암호화된 쿠키를 사용하여 안전하게 세션을 저장할 것입니다.
- `database` - 어플리케이션이 사용하는 데이터베이스에 저장합니다. .
- `memcached` / `redis` - 빠르고, 캐시를 기반으로한 memcached, redis 에 저장합니다.
- `array` - PHP 배열에 세션을 저장합니다. 이경우 세션이 요청에 대해서 지속적으로 유지 되지 않습니다. 
</div>

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
### 

The Laravel framework uses the `flash` session key internally, so you should not add an item to the session by that name.

If you need all stored session data to be encrypted, set the `encrypt` configuration option to `true`.

<a name="basic-usage"></a>
## Basic Usage

#### Accessing The Session

First, let's access the session. We can access the session instance via the HTTP request, which can be type-hinted on a controller method. Remember, controller method dependencies are injected via the Laravel [service container](/docs/{{version}}/container):

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

    $value = $request->session()->get('key', 'default');

    $value = $request->session()->get('key', function() {
        return 'default';
    });

If you would like to retrieve all data from the session, you may use the `all` method:

    $data = $request->session()->all();

You may also use the global `session` PHP function to retrieve and store data in the session:

    Route::get('home', function () {
        // Retrieve a piece of data from the session...
        $value = session('key');

        // Store a piece of data in the session...
        session(['key' => 'value']);
    });

#### Determining If An Item Exists In The Session

The `has` method may be used to check if an item exists in the session. This method will return `true` if the item exists:

    if ($request->session()->has('users')) {
        //
    }

#### Storing Data In The Session

Once you have access to the session instance, you may call a variety of functions to interact with the underlying data. For example, the `put` method stores a new piece of data in the session:

    $request->session()->put('key', 'value');

#### Pushing To Array Session Values

The `push` method may be used to push a new value onto a session value that is an array. For example, if the `user.teams` key contains an array of team names, you may push a new value onto the array like so:

    $request->session()->push('user.teams', 'developers');

#### Retrieving And Deleting An Item

The `pull` method will retrieve and delete an item from the session:

    $value = $request->session()->pull('key', 'default');

#### Deleting Items From The Session

The `forget` method will remove a piece of data from the session. If you would like to remove all data from the session, you may use the `flush` method:

    $request->session()->forget('key');

    $request->session()->flush();

#### Regenerating The Session ID

If you need to regenerate the session ID, you may use the `regenerate` method:

    $request->session()->regenerate();

<a name="flash-data"></a>
### Flash Data

Sometimes you may wish to store items in the session only for the next request. You may do so using the `flash` method. Data stored in the session using this method will only be available during the subsequent HTTP request, and then will be deleted. Flash data is primarily useful for short-lived status messages:

    $request->session()->flash('status', 'Task was successful!');

If you need to keep your flash data around for even more requests, you may use the `reflash` method, which will keep all of the flash data around for an additional request. If you only need to keep specific flash data around, you may use the `keep` method:

    $request->session()->reflash();

    $request->session()->keep(['username', 'email']);

<a name="adding-custom-session-drivers"></a>
## Adding Custom Session Drivers

To add additional drivers to Laravel's session back-end, you may use the `extend` method on the `Session` [facade](/docs/{{version}}/facades). You can call the `extend` method from the `boot` method of a [service provider](/docs/{{version}}/providers):

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

<div class="content-list" markdown="1">
- The `open` method would typically be used in file based session store systems. Since Laravel ships with a `file` session driver, you will almost never need to put anything in this method. You can leave it as an empty stub. It is simply a fact of poor interface design (which we'll discuss later) that PHP requires us to implement this method.
- The `close` method, like the `open` method, can also usually be disregarded. For most drivers, it is not needed.
- The `read` method should return the string version of the session data associated with the given `$sessionId`. There is no need to do any serialization or other encoding when retrieving or storing session data in your driver, as Laravel will perform the serialization for you.
- The `write` method should write the given `$data` string associated with the `$sessionId` to some persistent storage system, such as MongoDB, Dynamo, etc.
- The `destroy` method should remove the data associated with the `$sessionId` from persistent storage.
- The `gc` method should destroy all session data that is older than the given `$lifetime`, which is a UNIX timestamp. For self-expiring systems like Memcached and Redis, this method may be left empty.
</div>

Once the session driver has been registered, you may use the `mongo` driver in your `config/session.php` configuration file.
