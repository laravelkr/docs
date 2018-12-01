# Facades
# 파사드

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Using Facades](#using-facades)
- [파사드 사용하기](#using-facades)
- [Facade Class Reference](#facade-class-reference)
- [Facade 클래스 목록](#facade-class-reference)

<a name="introduction"></a>
## Introduction
## 시작하기

Facades provide a "static" interface to classes that are available in the application's [service container](/docs/{{version}}/container). Laravel ships with many facades, and you have probably been using them without even knowing it! Laravel "facades" serve as "static proxies" to underlying classes in the service container, providing the benefit of a terse, expressive syntax while maintaining more testability and flexibility than traditional static methods.

파사드는 애플리케이션의 [서비스 컨테이너](/docs/{{version}}/container)에서 사용가능한 클래스들에 대한 "정적" 인터페이스를 제공합니다. 라라벨은 많은 파사드를 가지고 있으며 여러분이 알지 못하는 사이에도 이미 사용하고 있을 것입니다. 라라벨 "파사드" 는 서비스 컨테이너에 등록된 클래스들에 대한 일종의 "정적 프록시" 역활을 수행하는데, 이를 통해서 전통적인 정적 메소드 보다 간결한 문법과 테스트의 용이성 그리고 코드의 유연성을 유지하는 이점을 제공합니다. 

<a name="using-facades"></a>
## Using Facades
## 파사드 사용하기

In the context of a Laravel application, a facade is a class that provides access to an object from the container. The machinery that makes this work is in the `Facade` class. Laravel's facades, and any custom facades you create, will extend the base `Illuminate\Support\Facades\Facade` class.

라라벨 애플리케이션에의 전체 맥락에서 보자면 파사드는 컨테이너의 객체에 엑세스하는 방법을 제공하는 클래스라고 할 수 있습니다. 이 작업을 수행하는 주요 매커니즘이 파사드 클래스안에 있습니다. 라라벨의 파사드들과 여러분이 작성한 파사드들은 기본 `Illuminate\Support\Facades\Facade` 클래스를 상속받습니다. 

A facade class only needs to implement a single method: `getFacadeAccessor`. It's the `getFacadeAccessor` method's job to define what to resolve from the container. The `Facade` base class makes use of the `__callStatic()` magic-method to defer calls from your facade to the resolved object. 

파사드 클래스는 `getFacadeAccessor`라는 하나의 메소드를 구현해야 합니다. `getFacadeAccessor`메소드의 역활은 컨테이너로 부터 어떤 의존성을 해결하는지에 대해서 정의하는 것입니다. `Facade` 기본 클래스는 `__callStatic()` 매직 매소드를 사용하여 여러분이 작성한 파사드에 대한 호출을 의존성이 해결된 객체로 전달합니다. 

In the example below, a call is made to the Laravel cache system. By glancing at this code, one might assume that the static method `get` is being called on the `Cache` class.

다음의 예제에서 라라벨 캐시 시스템을 호출합니다. 이 코드를 보자면, 아마 `Cache` 클래스의 `get` static 메소드를 호출한다고 생각할 수 있습니다. 

    <?php

    namespace App\Http\Controllers;

    use Cache;
    use App\Http\Controllers\Controller;

    class UserController extends Controller
    {
        /**
         * Show the profile for the given user.
         *
         * @param  int  $id
         * @return Response
         */
        public function showProfile($id)
        {
            $user = Cache::get('user:'.$id);

            return view('profile', ['user' => $user]);
        }
    }

Notice that near the top of the file we are "importing" the `Cache` facade. This facade serves as a proxy to accessing the underlying implementation of the `Illuminate\Contracts\Cache\Factory` interface. Any calls we make using the facade will be passed to the underlying instance of Laravel's cache service.

파일의 상단에 `Cache` 파사드를 사용하고 있는 부분에 주목해 주십시오. 이 파사드는 `Illuminate\Contracts\Cache\Factory` 인터페이스의 구현체에 접속할 수 있는 프록시로 동작합니다. 파사드를 사용한 어떠한 호출이라도 라라벨의 캐시 서비스의 구현체에 전달됩니다. 

If we look at that `Illuminate\Support\Facades\Cache` class, you'll see that there is no static method `get`:

실제로 `Illuminate\Support\Facades\Cache`를 살펴보면 `get`이라는 static 메소드는 찾을 수가 없습니다. 

    class Cache extends Facade
    {
        /**
         * Get the registered name of the component.
         *
         * @return string
         */
        protected static function getFacadeAccessor() { return 'cache'; }
    }

Instead, the `Cache` facade extends the base `Facade` class and defines the method `getFacadeAccessor()`. Remember, this method's job is to return the name of a service container binding. When a user references any static method on the `Cache` facade, Laravel resolves the `cache` binding from the [service container](/docs/{{version}}/container) and runs the requested method (in this case, `get`) against that object.

대신에, `Cache` 파사드는 기본 `Facade` 클래스를 상속하고 `getFacadeAccessor()` 메소드를 정의하고 있습니다. 기억해야할 것은 이 메소드의 역활이 서비스 컨테이너의 바인딩 이름을 반환한다는 것입니다. 사용자가 `Cache` 파사드의 어떤 스태틱 메소드를 참조하려고 할 때 라라벨은 [서비스 컨테이너]로 부터 `cache` 로 이름지어진 바인딩 객체를 찾아 메소드 호출을 요청할 것입니다(이 경우 `get` 메소드)

<a name="facade-class-reference"></a>
## Facade Class Reference
## 파사드 클래스 목록

Below you will find every facade and its underlying class. This is a useful tool for quickly digging into the API documentation for a given facade root. The [service container binding](/docs/{{version}}/container) key is also included where applicable.

다음은 모든 파사드 와 실제 클래스에 대한 목록입니다. 여러분이 특정한 파사드를 기준으로 API 문서를 빠르게 찾고자 하는 경우에 도움이 될것입니다. 응용이 가능하도록 [서비스 컨테이너 바인딩](/docs/{{version}}/container)도 포함되어 있습니다. 

Facade  |  Class  |  Service Container Binding
------------- | ------------- | -------------
App  |  [Illuminate\Foundation\Application](http://laravel.com/api/{{version}}/Illuminate/Foundation/Application.html)  | `app`
Artisan  |  [Illuminate\Contracts\Console\Kernel](http://laravel.com/api/{{version}}/Illuminate/Contracts/Console/Kernel.html)  |  `artisan`
Auth  |  [Illuminate\Auth\AuthManager](http://laravel.com/api/{{version}}/Illuminate/Auth/AuthManager.html)  |  `auth`
Auth (Instance)  |  [Illuminate\Auth\Guard](http://laravel.com/api/{{version}}/Illuminate/Auth/Guard.html)  |
Blade  |  [Illuminate\View\Compilers\BladeCompiler](http://laravel.com/api/{{version}}/Illuminate/View/Compilers/BladeCompiler.html)  |  `blade.compiler`
Bus  |  [Illuminate\Contracts\Bus\Dispatcher](http://laravel.com/api/{{version}}/Illuminate/Contracts/Bus/Dispatcher.html)  |
Cache  |  [Illuminate\Cache\Repository](http://laravel.com/api/{{version}}/Illuminate/Cache/Repository.html)  |  `cache`
Config  |  [Illuminate\Config\Repository](http://laravel.com/api/{{version}}/Illuminate/Config/Repository.html)  |  `config`
Cookie  |  [Illuminate\Cookie\CookieJar](http://laravel.com/api/{{version}}/Illuminate/Cookie/CookieJar.html)  |  `cookie`
Crypt  |  [Illuminate\Encryption\Encrypter](http://laravel.com/api/{{version}}/Illuminate/Encryption/Encrypter.html)  |  `encrypter`
DB  |  [Illuminate\Database\DatabaseManager](http://laravel.com/api/{{version}}/Illuminate/Database/DatabaseManager.html)  |  `db`
DB (Instance)  |  [Illuminate\Database\Connection](http://laravel.com/api/{{version}}/Illuminate/Database/Connection.html)  |
Event  |  [Illuminate\Events\Dispatcher](http://laravel.com/api/{{version}}/Illuminate/Events/Dispatcher.html)  |  `events`
File  |  [Illuminate\Filesystem\Filesystem](http://laravel.com/api/{{version}}/Illuminate/Filesystem/Filesystem.html)  |  `files`
Gate  |  [Illuminate\Contracts\Auth\Access\Gate](http://laravel.com/api/5.1/Illuminate/Contracts/Auth/Access/Gate.html)  |
Hash  |  [Illuminate\Contracts\Hashing\Hasher](http://laravel.com/api/{{version}}/Illuminate/Contracts/Hashing/Hasher.html)  |  `hash`
Input  |  [Illuminate\Http\Request](http://laravel.com/api/{{version}}/Illuminate/Http/Request.html)  |  `request`
Lang  |  [Illuminate\Translation\Translator](http://laravel.com/api/{{version}}/Illuminate/Translation/Translator.html)  |  `translator`
Log  |  [Illuminate\Log\Writer](http://laravel.com/api/{{version}}/Illuminate/Log/Writer.html)  |  `log`
Mail  |  [Illuminate\Mail\Mailer](http://laravel.com/api/{{version}}/Illuminate/Mail/Mailer.html)  |  `mailer`
Password  |  [Illuminate\Auth\Passwords\PasswordBroker](http://laravel.com/api/{{version}}/Illuminate/Auth/Passwords/PasswordBroker.html)  |  `auth.password`
Queue  |  [Illuminate\Queue\QueueManager](http://laravel.com/api/{{version}}/Illuminate/Queue/QueueManager.html)  |  `queue`
Queue (Instance) |  [Illuminate\Queue\QueueInterface](http://laravel.com/api/{{version}}/Illuminate/Queue/QueueInterface.html)  |
Queue (Base Class) |  [Illuminate\Queue\Queue](http://laravel.com/api/{{version}}/Illuminate/Queue/Queue.html)  |
Redirect  |  [Illuminate\Routing\Redirector](http://laravel.com/api/{{version}}/Illuminate/Routing/Redirector.html)  |  `redirect`
Redis  |  [Illuminate\Redis\Database](http://laravel.com/api/{{version}}/Illuminate/Redis/Database.html)  |  `redis`
Request  |  [Illuminate\Http\Request](http://laravel.com/api/{{version}}/Illuminate/Http/Request.html)  |  `request`
Response  |  [Illuminate\Contracts\Routing\ResponseFactory](http://laravel.com/api/{{version}}/Illuminate/Contracts/Routing/ResponseFactory.html)  |
Route  |  [Illuminate\Routing\Router](http://laravel.com/api/{{version}}/Illuminate/Routing/Router.html)  |  `router`
Schema  |  [Illuminate\Database\Schema\Blueprint](http://laravel.com/api/{{version}}/Illuminate/Database/Schema/Blueprint.html)  |
Session  |  [Illuminate\Session\SessionManager](http://laravel.com/api/{{version}}/Illuminate/Session/SessionManager.html)  |  `session`
Session (Instance)  |  [Illuminate\Session\Store](http://laravel.com/api/{{version}}/Illuminate/Session/Store.html)  |
Storage  |  [Illuminate\Contracts\Filesystem\Factory](http://laravel.com/api/{{version}}/Illuminate/Contracts/Filesystem/Factory.html)  |  `filesystem`
URL  |  [Illuminate\Routing\UrlGenerator](http://laravel.com/api/{{version}}/Illuminate/Routing/UrlGenerator.html)  |  `url`
Validator  |  [Illuminate\Validation\Factory](http://laravel.com/api/{{version}}/Illuminate/Validation/Factory.html)  |  `validator`
Validator (Instance)  |  [Illuminate\Validation\Validator](http://laravel.com/api/{{version}}/Illuminate/Validation/Validator.html) |
View  |  [Illuminate\View\Factory](http://laravel.com/api/{{version}}/Illuminate/View/Factory.html)  |  `view`
View (Instance)  |  [Illuminate\View\View](http://laravel.com/api/{{version}}/Illuminate/View/View.html)  |
