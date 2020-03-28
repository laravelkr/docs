# Facades
# 파사드

- [Introduction](#introduction)
- [시작하기](#introduction)
- [When To Use Facades](#when-to-use-facades)
- [언제 파사드를 사용하는가](#when-to-use-facades)
    - [Facades Vs. Dependency Injection](#facades-vs-dependency-injection)
    - [파사드 Vs. 의존성 주입](#facades-vs-dependency-injection)
    - [Facades Vs. Helper Functions](#facades-vs-helper-functions)
    - [파사드 Vs. 헬퍼 함수](#facades-vs-helper-functions)
- [How Facades Work](#how-facades-work)
- [파사드가 어떻게 동작하는가](#how-facades-work)
- [Real-Time Facades](#real-time-facades)
- [실시간 파사드](#real-time-facades)
- [Facade Class Reference](#facade-class-reference)
- [파사드 클래스 목록](#facade-class-reference)

<a name="introduction"></a>
## Introduction
## 시작하기

Facades provide a "static" interface to classes that are available in the application's [service container](/docs/{{version}}/container). Laravel ships with many facades which provide access to almost all of Laravel's features. Laravel facades serve as "static proxies" to underlying classes in the service container, providing the benefit of a terse, expressive syntax while maintaining more testability and flexibility than traditional static methods.

파사드는 애플리케이션의 [서비스 컨테이너](/docs/{{version}}/container)에서 사용가능한 클래스들에 대한 "정적" 인터페이스를 제공합니다. 라라벨은 대부분의 라라벨의 기능에 엑세스하는 많은 파사드들을 제공합니다. 라라벨의 파사드 는 서비스 컨테이너에 등록된 클래스들에 대한 일종의 "정적 프록시" 역할을 수행하는데, 이를 통해서 전통적인 정적 메소드 보다 간결한 문법과 테스트의 용이성 그리고 코드의 유연성을 유지하는 이점을 제공합니다.

All of Laravel's facades are defined in the `Illuminate\Support\Facades` namespace. So, we can easily access a facade like so:

모든 라라벨의 파사드는 `Illuminate\Support\Facades` 네임스페이스 안에 정의되어 있습니다. 따라서 다음처럼 손쉽게 파사드에 엑세스 할 수 있습니다.

    use Illuminate\Support\Facades\Cache;

    Route::get('/cache', function () {
        return Cache::get('key');
    });

Throughout the Laravel documentation, many of the examples will use facades to demonstrate various features of the framework.

라라벨 문서상에서 프레임 워크의 다양한 기능의 사용법을 익히기 위해서 파사드를 사용하는 예제를 많이 확인할 수 있습니다.

<a name="when-to-use-facades"></a>
## When To Use Facades
## 언제 파사드를 사용하는가?

Facades have many benefits. They provide a terse, memorable syntax that allows you to use Laravel's features without remembering long class names that must be injected or configured manually. Furthermore, because of their unique usage of PHP's dynamic methods, they are easy to test.

파사드는 다양한 장점이 있습니다. 파사드는 의존성으로 주입되거나 직접 설정해야 하는 긴 이름의 클래스들을 일일이 기억하지 않고서도 알 수 있는, 간결하고 기억하기 쉬운 문법을 제공합니다. 또한 PHP의 독특한 다이나믹 메소드의 사용 덕분에, 테스트도 쉬워집니다.

However, some care must be taken when using facades. The primary danger of facades is class scope creep. Since facades are so easy to use and do not require injection, it can be easy to let your classes continue to grow and use many facades in a single class. Using dependency injection, this potential is mitigated by the visual feedback a large constructor gives you that your class is growing too large. So, when using facades, pay special attention to the size of your class so that its scope of responsibility stays narrow.

그러나 파사드를 사용하는데 몇 가지 주의해야 할 점도 존재합니다. 파사드의 가장 주요한 위험은 클래스의 책임 범위의 잘못된 사용입니다. 파사드는 사용하기가 매우 쉽고, 의존성 주입도 필요하지 않기 때문에, 하나의 클래스 파일 안에서 여러개의 파사드를 사용하는 코드가 자꾸 늘어나도록 클래스를 구성하기가 매우 쉽습니다. 의존성 주입을 사용하면 클래스가 커짐에 따라서 생성자가 비대해진다는 시각적인 피드백을 주게 되어 이러한 잠재적 위험이 억제됩니다. 따라서 파사드를 사용할 때에는, 클래스의 책임 범위를 작게 유지하도록 클래스의 사이즈를 구성하는데 특별한 주의가 필요합니다.

> {tip} When building a third-party package that interacts with Laravel, it's better to inject [Laravel contracts](/docs/{{version}}/contracts) instead of using facades. Since packages are built outside of Laravel itself, you will not have access to Laravel's facade testing helpers.

> {tip} 라라벨과 연관된 써드파티 패키지를 구성할 때, 파사드 사용하는 대신에 [라라벨 contracts](/docs/{{version}}/contracts)를 주입하는 것이 더 나은 방법입니다. 패키지는 라라벨의 외부에서 구성되기 때문에, 라라벨의 파사드 테스팅 헬퍼에 엑세스할 필요는 없습니다.

<a name="facades-vs-dependency-injection"></a>
### Facades Vs. Dependency Injection
### 파사드 Vs. 의존성 주입

One of the primary benefits of dependency injection is the ability to swap implementations of the injected class. This is useful during testing since you can inject a mock or stub and assert that various methods were called on the stub.

의존성 주입의 주요한 장점중 하나는 주입된 클래스의 구현체를 변경할 수 있다는 특성입니다. 이는 테스팅을 수행하는 동안 모킹 객체(mock) 과 스터브(stub) 를 주입할 수 있게 하고, 다양한 메소드가 호출되는 것을 확인할 수 있게 하여 유용합니다.

Typically, it would not be possible to mock or stub a truly static class method. However, since facades use dynamic methods to proxy method calls to objects resolved from the service container, we actually can test facades just as we would test an injected class instance. For example, given the following route:

일반적으로, 정적 클래스 메소드에 대해서는 모킹 객체(mock) 나 스터브(stub) 사용이 불가합니다. 하지만 파사드는 서비스 컨테이너에 의해서 의존성이 해결되는 클래스 객체의 프록시 메소드로 다이나믹 메소드를 사용하기 때문에, 실제 주입된 클래스 인스턴스를 테스트하는 것과 마찬가지로 파사드를 테스트할 수 있습니다. 예를 들어 다음의 주어진 라우트를 보겠습니다.

    use Illuminate\Support\Facades\Cache;

    Route::get('/cache', function () {
        return Cache::get('key');
    });

We can write the following test to verify that the `Cache::get` method was called with the argument we expected:

우리는 `Cache::get` 메소드가 원하는 인자와 함께 호출되었는지 확인하기 위해서 다음의 테스트를 작성할 수 있습니다.

    use Illuminate\Support\Facades\Cache;

    /**
     * A basic functional test example.
     *
     * @return void
     */
    public function testBasicExample()
    {
        Cache::shouldReceive('get')
             ->with('key')
             ->andReturn('value');

        $this->visit('/cache')
             ->see('value');
    }

<a name="facades-vs-helper-functions"></a>
### Facades Vs. Helper Functions
### 파사드 Vs. 헬퍼 함수

In addition to facades, Laravel includes a variety of "helper" functions which can perform common tasks like generating views, firing events, dispatching jobs, or sending HTTP responses. Many of these helper functions perform the same function as a corresponding facade. For example, this facade call and helper call are equivalent:

파사드 뿐만 아니라, 라라벨은 뷰 파일을 생성하거나, 이벤트를 발생시키거나, Job을 실행시키거나 또는 HTTP 응답을 반환하는등의 공통된 작업을 수행하는 다양한 "헬퍼" 함수를 포함하고 있습니다. 이러한 다수의 헬퍼 함수들은 파사드와 일치하는 동일한 동작들을 수행합니다. 예를 들어 다음의 파사드 호출과 헬퍼 함수 호출은 동일합니다.

    return View::make('profile');

    return view('profile');

There is absolutely no practical difference between facades and helper functions. When using helper functions, you may still test them exactly as you would the corresponding facade. For example, given the following route:

이 파사드와 헬퍼함수 사이에는 차이점이 전혀 없습니다. 헬퍼함수를 사용하는 경우에도 일치하는 파사드와 같이 동일하게 테스트 할 수 있습니다. 예를 들어 다음의 주어진 라우트를 보겠습니다.

    Route::get('/cache', function () {
        return cache('key');
    });

Under the hood, the `cache` helper is going to call the `get` method on the class underlying the `Cache` facade. So, even though we are using the helper function, we can write the following test to verify that the method was called with the argument we expected:

위의 코드에서 `cache` 헬퍼 함수는 `Cache` 파사드의 `get` 메소드를 호출합니다. 따라서 헬퍼 함수를 사용하더라도, 기대되는 인자와 함께 메소드가 호출되었는지 확인하기 위해서 다음의 테스트를 작성할 수 있습니다.

    use Illuminate\Support\Facades\Cache;

    /**
     * A basic functional test example.
     *
     * @return void
     */
    public function testBasicExample()
    {
        Cache::shouldReceive('get')
             ->with('key')
             ->andReturn('value');

        $this->visit('/cache')
             ->see('value');
    }

<a name="how-facades-work"></a>
## How Facades Work
## 파사드는 어떻게 동작하는가

In a Laravel application, a facade is a class that provides access to an object from the container. The machinery that makes this work is in the `Facade` class. Laravel's facades, and any custom facades you create, will extend the base `Illuminate\Support\Facades\Facade` class.

라라벨 애플리케이션에서, 파사드는 컨테이너의 객체에 엑세스하는 방법을 제공하는 클래스라고 할 수 있습니다. 이 작업을 수행하는 주요 매커니즘이 파사드 클래스안에 있습니다. 라라벨의 파사드들과 여러분이 작성한 파사드들은 기본 `Illuminate\Support\Facades\Facade` 클래스를 상속받습니다.

The `Facade` base class makes use of the `__callStatic()` magic-method to defer calls from your facade to an object resolved from the container. In the example below, a call is made to the Laravel cache system. By glancing at this code, one might assume that the static method `get` is being called on the `Cache` class:

`Facade` 기본 클래스는 `__callStatic()` 매직 메소드를 사용하여 여러분이 작성한 파사드에 대한 호출을 컨테이너에서 의존성이 해결된 객체로 전달합니다. 다음의 예제에서 라라벨의 캐시 시스템을 호출합니다. 이 코드를 보자면, 아마 `Cache` 클래스의 `get` static 메소드를 호출한다고 생각할 수 있습니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Support\Facades\Cache;

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

Instead, the `Cache` facade extends the base `Facade` class and defines the method `getFacadeAccessor()`. This method's job is to return the name of a service container binding. When a user references any static method on the `Cache` facade, Laravel resolves the `cache` binding from the [service container](/docs/{{version}}/container) and runs the requested method (in this case, `get`) against that object.

대신에, `Cache` 파사드는 기본 `Facade` 클래스를 상속하고 `getFacadeAccessor()` 메소드를 정의하고 있습니다. 이 메소드의 역할이 서비스 컨테이너의 바인딩 이름을 반환한다는 것입니다. 사용자가 `Cache` 파사드의 어떤 스태틱 메소드를 참조하려고 할 때 라라벨은 [서비스 컨테이너]로 부터 `cache` 로 이름지어진 바인딩 객체를 찾아 메소드 호출을 요청할 것입니다(이 경우 `get` 메소드)

<a name="real-time-facades"></a>
## Real-Time Facades
## 실시간 파사드

Using real-time facades, you may treat any class in your application as if it were a facade. To illustrate how this can be used, let's examine an alternative. For example, let's assume our `Podcast` model has a `publish` method. However, in order to publish the podcast, we need to inject a `Publisher` instance:

실시간 파사드를 사용하여 애플리케이션의 모든 클래스를 파사드처럼 취급 할 수 있습니다. 어떻게 이를 사용할 수 있는지 알기 위해서, 다음의 경우를 살펴보겠습니다. 예를 들어 `Podcast` 모델이 `publish` 메소드를 가지고 있다고 가정해보겠습니다. 이때, podcast를 publish 하기 위해서는 `Publisher` 인스턴스를 주입해야 합니다.

    <?php

    namespace App;

    use App\Contracts\Publisher;
    use Illuminate\Database\Eloquent\Model;

    class Podcast extends Model
    {
        /**
         * Publish the podcast.
         *
         * @param  Publisher  $publisher
         * @return void
         */
        public function publish(Publisher $publisher)
        {
            $this->update(['publishing' => now()]);

            $publisher->publish($this);
        }
    }

Injecting a publisher implementation into the method allows us to easily test the method in isolation since we can mock the injected publisher. However, it requires us to always pass a publisher instance each time we call the `publish` method. Using real-time facades, we can maintain the same testability while not being required to explicitly pass a `Publisher` instance. To generate a real-time facade, prefix the namespace of the imported class with `Facades`:

메소드에 publisher 구현체를 주입하면 주입된 publisher 는 mock 할 수 있기 때문에, 메소드를 분리시켜 테스트를 용이하게 합니다. 그렇지만, `publish` 메소드를 호출할 때마다 매번 publisher 인스턴스를 전달할 필요가 있습니다. 리얼타임 파사드를 사용하면, 동일한 테스트 유효성을 유지하면서도, 명시적으로 `Publisher` 인스턴스를 전달하지 않아도 됩니다. 리얼타임 파사드를 생성하기 위해서는 Import 한 클래스 이름 앞에 `Facades` 를 붙이면 됩니다.

    <?php

    namespace App;

    use Facades\App\Contracts\Publisher;
    use Illuminate\Database\Eloquent\Model;

    class Podcast extends Model
    {
        /**
         * Publish the podcast.
         *
         * @return void
         */
        public function publish()
        {
            $this->update(['publishing' => now()]);

            Publisher::publish($this);
        }
    }

When the real-time facade is used, the publisher implementation will be resolved out of the service container using the portion of the interface or class name that appears after the `Facades` prefix. When testing, we can use Laravel's built-in facade testing helpers to mock this method call:

리얼타임 파사드가 사용되면, publisher 구현체는 `Facades` 클래스네임 뒤에 정의된 인터페이스 또는 클래스 네임을 사용하여 서비스 컨테이너에서 의존성이 해결됩니다. 테스트를 진행할 때에는, 이 메소드 호출을 mock 하기 위해서 라라벨에 내장된 파사드 테스팅 헬퍼를 사용할 수 있습니다.

    <?php

    namespace Tests\Feature;

    use App\Podcast;
    use Facades\App\Contracts\Publisher;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Tests\TestCase;

    class PodcastTest extends TestCase
    {
        use RefreshDatabase;

        /**
         * A test example.
         *
         * @return void
         */
        public function test_podcast_can_be_published()
        {
            $podcast = factory(Podcast::class)->create();

            Publisher::shouldReceive('publish')->once()->with($podcast);

            $podcast->publish();
        }
    }

<a name="facade-class-reference"></a>
## Facade Class Reference
## 파사드 클래스 목록

Below you will find every facade and its underlying class. This is a useful tool for quickly digging into the API documentation for a given facade root. The [service container binding](/docs/{{version}}/container) key is also included where applicable.

다음은 모든 파사드 와 실제 클래스에 대한 목록입니다. 여러분이 특정한 파사드를 기준으로 API 문서를 빠르게 찾고자 하는 경우에 도움이 될것입니다. 응용이 가능하도록 [서비스 컨테이너 바인딩](/docs/{{version}}/container)도 포함되어 있습니다.

Facade  |  Class  |  Service Container Binding
------------- | ------------- | -------------
App  |  [Illuminate\Foundation\Application](https://laravel.com/api/{{version}}/Illuminate/Foundation/Application.html)  |  `app`
Artisan  |  [Illuminate\Contracts\Console\Kernel](https://laravel.com/api/{{version}}/Illuminate/Contracts/Console/Kernel.html)  |  `artisan`
Auth  |  [Illuminate\Auth\AuthManager](https://laravel.com/api/{{version}}/Illuminate/Auth/AuthManager.html)  |  `auth`
Auth (Instance)  |  [Illuminate\Contracts\Auth\Guard](https://laravel.com/api/{{version}}/Illuminate/Contracts/Auth/Guard.html)  |  `auth.driver`
Blade  |  [Illuminate\View\Compilers\BladeCompiler](https://laravel.com/api/{{version}}/Illuminate/View/Compilers/BladeCompiler.html)  |  `blade.compiler`
Broadcast  |  [Illuminate\Contracts\Broadcasting\Factory](https://laravel.com/api/{{version}}/Illuminate/Contracts/Broadcasting/Factory.html)  |  &nbsp;
Broadcast (Instance)  |  [Illuminate\Contracts\Broadcasting\Broadcaster](https://laravel.com/api/{{version}}/Illuminate/Contracts/Broadcasting/Broadcaster.html)  |  &nbsp;
Bus  |  [Illuminate\Contracts\Bus\Dispatcher](https://laravel.com/api/{{version}}/Illuminate/Contracts/Bus/Dispatcher.html)  |  &nbsp;
Cache  |  [Illuminate\Cache\CacheManager](https://laravel.com/api/{{version}}/Illuminate/Cache/CacheManager.html)  |  `cache`
Cache (Instance)  |  [Illuminate\Cache\Repository](https://laravel.com/api/{{version}}/Illuminate/Cache/Repository.html)  |  `cache.store`
Config  |  [Illuminate\Config\Repository](https://laravel.com/api/{{version}}/Illuminate/Config/Repository.html)  |  `config`
Cookie  |  [Illuminate\Cookie\CookieJar](https://laravel.com/api/{{version}}/Illuminate/Cookie/CookieJar.html)  |  `cookie`
Crypt  |  [Illuminate\Encryption\Encrypter](https://laravel.com/api/{{version}}/Illuminate/Encryption/Encrypter.html)  |  `encrypter`
DB  |  [Illuminate\Database\DatabaseManager](https://laravel.com/api/{{version}}/Illuminate/Database/DatabaseManager.html)  |  `db`
DB (Instance)  |  [Illuminate\Database\Connection](https://laravel.com/api/{{version}}/Illuminate/Database/Connection.html)  |  `db.connection`
Event  |  [Illuminate\Events\Dispatcher](https://laravel.com/api/{{version}}/Illuminate/Events/Dispatcher.html)  |  `events`
File  |  [Illuminate\Filesystem\Filesystem](https://laravel.com/api/{{version}}/Illuminate/Filesystem/Filesystem.html)  |  `files`
Gate  |  [Illuminate\Contracts\Auth\Access\Gate](https://laravel.com/api/{{version}}/Illuminate/Contracts/Auth/Access/Gate.html)  |  &nbsp;
Hash  |  [Illuminate\Contracts\Hashing\Hasher](https://laravel.com/api/{{version}}/Illuminate/Contracts/Hashing/Hasher.html)  |  `hash`
Lang  |  [Illuminate\Translation\Translator](https://laravel.com/api/{{version}}/Illuminate/Translation/Translator.html)  |  `translator`
Log  |  [Illuminate\Log\LogManager](https://laravel.com/api/{{version}}/Illuminate/Log/LogManager.html)  |  `log`
Mail  |  [Illuminate\Mail\Mailer](https://laravel.com/api/{{version}}/Illuminate/Mail/Mailer.html)  |  `mailer`
Notification  |  [Illuminate\Notifications\ChannelManager](https://laravel.com/api/{{version}}/Illuminate/Notifications/ChannelManager.html)  |  &nbsp;
Password  |  [Illuminate\Auth\Passwords\PasswordBrokerManager](https://laravel.com/api/{{version}}/Illuminate/Auth/Passwords/PasswordBrokerManager.html)  |  `auth.password`
Password (Instance)  |  [Illuminate\Auth\Passwords\PasswordBroker](https://laravel.com/api/{{version}}/Illuminate/Auth/Passwords/PasswordBroker.html)  |  `auth.password.broker`
Queue  |  [Illuminate\Queue\QueueManager](https://laravel.com/api/{{version}}/Illuminate/Queue/QueueManager.html)  |  `queue`
Queue (Instance)  |  [Illuminate\Contracts\Queue\Queue](https://laravel.com/api/{{version}}/Illuminate/Contracts/Queue/Queue.html)  |  `queue.connection`
Queue (Base Class)  |  [Illuminate\Queue\Queue](https://laravel.com/api/{{version}}/Illuminate/Queue/Queue.html)  |  &nbsp;
Redirect  |  [Illuminate\Routing\Redirector](https://laravel.com/api/{{version}}/Illuminate/Routing/Redirector.html)  |  `redirect`
Redis  |  [Illuminate\Redis\RedisManager](https://laravel.com/api/{{version}}/Illuminate/Redis/RedisManager.html)  |  `redis`
Redis (Instance)  |  [Illuminate\Redis\Connections\Connection](https://laravel.com/api/{{version}}/Illuminate/Redis/Connections/Connection.html)  |  `redis.connection`
Request  |  [Illuminate\Http\Request](https://laravel.com/api/{{version}}/Illuminate/Http/Request.html)  |  `request`
Response  |  [Illuminate\Contracts\Routing\ResponseFactory](https://laravel.com/api/{{version}}/Illuminate/Contracts/Routing/ResponseFactory.html)  |  &nbsp;
Response (Instance)  |  [Illuminate\Http\Response](https://laravel.com/api/{{version}}/Illuminate/Http/Response.html)  |  &nbsp;
Route  |  [Illuminate\Routing\Router](https://laravel.com/api/{{version}}/Illuminate/Routing/Router.html)  |  `router`
Schema  |  [Illuminate\Database\Schema\Builder](https://laravel.com/api/{{version}}/Illuminate/Database/Schema/Builder.html)  |  &nbsp;
Session  |  [Illuminate\Session\SessionManager](https://laravel.com/api/{{version}}/Illuminate/Session/SessionManager.html)  |  `session`
Session (Instance)  |  [Illuminate\Session\Store](https://laravel.com/api/{{version}}/Illuminate/Session/Store.html)  |  `session.store`
Storage  |  [Illuminate\Filesystem\FilesystemManager](https://laravel.com/api/{{version}}/Illuminate/Filesystem/FilesystemManager.html)  |  `filesystem`
Storage (Instance)  |  [Illuminate\Contracts\Filesystem\Filesystem](https://laravel.com/api/{{version}}/Illuminate/Contracts/Filesystem/Filesystem.html)  |  `filesystem.disk`
URL  |  [Illuminate\Routing\UrlGenerator](https://laravel.com/api/{{version}}/Illuminate/Routing/UrlGenerator.html)  |  `url`
Validator  |  [Illuminate\Validation\Factory](https://laravel.com/api/{{version}}/Illuminate/Validation/Factory.html)  |  `validator`
Validator (Instance)  |  [Illuminate\Validation\Validator](https://laravel.com/api/{{version}}/Illuminate/Validation/Validator.html)  |  &nbsp;
View  |  [Illuminate\View\Factory](https://laravel.com/api/{{version}}/Illuminate/View/Factory.html)  |  `view`
View (Instance)  |  [Illuminate\View\View](https://laravel.com/api/{{version}}/Illuminate/View/View.html)  |  &nbsp;
