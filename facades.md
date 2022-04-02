# 파사드

- [시작하기](#introduction)
- [언제 파사드를 사용하나요](#when-to-use-facades)
    - [파사드 Vs. 의존성 주입](#facades-vs-dependency-injection)
    - [파사드 Vs. 헬퍼 함수](#facades-vs-helper-functions)
- [파사드는 어떻게 동작하나요](#how-facades-work)
- [실시간 파사드](#real-time-facades)
- [파사드 클래스 목록](#facade-class-reference)

<a name="introduction"></a>
## 시작하기

라라벨 문서 전체에서 "파사드"를 통해 라라벨의 기능을 동작시키는 코드 예제를 볼 수 있습니다. 파사드는 애플리케이션의 [서비스 컨테이너](/docs/{{version}}/container)에서 사용할 수 있는 클래스에 "정적" 인터페이스를 제공합니다. 라라벨은 라라벨의 거의 모든 기능에 대한 액세스를 제공하는 많은 파사드를 함께 제공됩니다.

라라벨 파사드는 서비스 컨테이너의 기본 클래스에 대한 "정적 프록시" 역할을 하여, 간결하고 표현적인 구문의 이점을 제공하는 동시에 기존의 정적 방법보다 더 많은 테스트 가능성과 유연성을 유지합니다. 파사드가 후드 아래에서 어떻게 작동하는지 완전히 이해하지 못해도 괜찮습니다. 흐름을 따라가며 라라벨에 대해 계속 배우십시오.

모든 라라벨의 파사드는 `Illuminate\Support\Facades` 네임스페이스 안에 정의되어 있습니다. 따라서 다음처럼 손쉽게 파사드에 엑세스 할 수 있습니다.

    use Illuminate\Support\Facades\Cache;
    use Illuminate\Support\Facades\Route;

    Route::get('/cache', function () {
        return Cache::get('key');
    });

라라벨 문서상에서 프레임 워크의 다양한 기능의 사용법을 익히기 위해서 파사드를 사용하는 예제를 많이 확인할 수 있습니다.

<a name="helper-functions"></a>
#### 헬퍼 함수

파사드를 보완하기 위해 라라벨은 일반적인 라라벨 기능과 더욱 쉽게 상호 작용할 수 있도록 하는 다양한 글로벌 "헬퍼 기능"을 제공합니다. 상호 작용할 수 있는 일반적인 헬퍼 기능 중 일부는 `view`, `response`, `url`, `config` 등 입니다. 라라벨에서 제공하는 각 헬퍼 기능은 해당 기능과 함께 문서화되어 있습니다. 그러나 전체 목록은 전용 [헬퍼 문서](/docs/{{version}}/helpers)에서 확인 할 수 있습니다.

예를 들어 JSON 응답을 생성하기 위해 `Illuminate\Support\Facades\Response` 파사드를 사용하는 대신 단순히 `response` 기능을 사용할 수 있습니다. 헬퍼 함수는 전역적으로 사용할 수 있으므로 사용하기 위해 클래스를 가져올 필요가 없습니다.

    use Illuminate\Support\Facades\Response;

    Route::get('/users', function () {
        return Response::json([
            // ...
        ]);
    });

    Route::get('/users', function () {
        return response()->json([
            // ...
        ]);
    });

<a name="when-to-use-facades"></a>
## 언제 파사드를 사용하나요

파사드는 다양한 장점이 있습니다. 파사드는 의존성으로 주입되거나 직접 설정해야 하는 긴 이름의 클래스들을 일일이 기억하지 않고서도 알 수 있는, 간결하고 기억하기 쉬운 문법을 제공합니다. 또한 PHP의 독특한 다이나믹 메소드의 사용 덕분에, 테스트도 쉬워집니다.

그러나 파사드를 사용할 때는 약간의 주의가 필요합니다. 파사드의 중요한 위험요소는 클래스 "스코프 크립-scope creep"입니다. 파사드는 사용하기 쉽고 주입이 필요하지 않기 때문에 클래스를 계속 작성해 나가게 되고, 한 클래스에서 많은 파사드를 사용하게 되기 쉽습니다. 의존성 주입을 사용하면, 이 가능성이 시각적 피드백에 의해 완화됩니다. 너무 큰 생성자는 클래스가 너무 커지고 있다는 시각적 피드백을 제공합니다. 따라서 파사드를 사용할 때 책임 범위가 좁게 유지되도록 클래스 크기에 각별한 주의를 기울이십시오. 클래스가 너무 커지면 여러 개의 작은 클래스로 나누는 것을 고려하십시오.

<a name="facades-vs-dependency-injection"></a>
### 파사드 Vs. 의존성 주입

의존성 주입의 주요한 장점중 하나는 주입된 클래스의 구현체를 변경할 수 있다는 특성입니다. 이는 테스팅을 수행하는 동안 모킹 객체(mock) 과 스터브(stub) 를 주입할 수 있게 하고, 다양한 메소드가 호출되는 것을 확인할 수 있게 하여 유용합니다.

일반적으로, 정적 클래스 메소드에 대해서는 모킹 객체(mock) 나 스터브(stub) 사용이 불가합니다. 하지만 파사드는 서비스 컨테이너에 의해서 의존성이 해결되는 클래스 객체의 프록시 메소드로 다이나믹 메소드를 사용하기 때문에, 실제 주입된 클래스 인스턴스를 테스트하는 것과 마찬가지로 파사드를 테스트할 수 있습니다. 예를 들어 다음의 주어진 라우트를 보겠습니다.

    use Illuminate\Support\Facades\Cache;

    Route::get('/cache', function () {
        return Cache::get('key');
    });

우리는 라라벨의 파사드 테스팅 메소드를 사용하여,  `Cache::get` 메소드가 우리가 예상한 인수로 호출되는지 확인하기 위해 다음과 같이 테스트를 작성할 수 있습니다.

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

        $response = $this->get('/cache');

        $response->assertSee('value');
    }

<a name="facades-vs-helper-functions"></a>
### 파사드 Vs. 헬퍼 함수

파사드 뿐만 아니라, 라라벨은 뷰 파일을 생성하거나, 이벤트를 발생시키거나, Job을 실행시키거나 또는 HTTP 응답을 반환하는등의 공통된 작업을 수행하는 다양한 "헬퍼" 함수를 포함하고 있습니다. 이러한 다수의 헬퍼 함수들은 파사드와 일치하는 동일한 동작들을 수행합니다. 예를 들어 다음의 파사드 호출과 헬퍼 함수 호출은 동일합니다.

    return Illuminate\Support\Facades\View::make('profile');

    return view('profile');

이 파사드와 헬퍼함수 사이에는 차이점이 전혀 없습니다. 헬퍼함수를 사용하는 경우에도 일치하는 파사드와 같이 동일하게 테스트 할 수 있습니다. 예를 들어 다음의 주어진 라우트를 보겠습니다.

    Route::get('/cache', function () {
        return cache('key');
    });

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

        $response = $this->get('/cache');

        $response->assertSee('value');
    }

<a name="how-facades-work"></a>
## 파사드는 어떻게 동작하나요

라라벨 애플리케이션에서, 파사드는 컨테이너의 객체에 엑세스하는 방법을 제공하는 클래스라고 할 수 있습니다. 이 작업을 수행하는 주요 매커니즘이 파사드 클래스안에 있습니다. 라라벨의 파사드들과 여러분이 작성한 파사드들은 기본 `Illuminate\Support\Facades\Facade` 클래스를 상속받습니다.

`Facade` 기본 클래스는 `__callStatic()` 매직 메서드를 사용하여 파사드에서 컨테이너에서 확인된 객체로의 호출을 연기합니다. 아래 예제에서는 라라벨 캐시 시스템을 호출합니다. 이 코드를 보면 `Cache` 클래스에  정적-static `get` 메소드가 호출되고 있다고 가정할 수 있습니다.

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

파일의 상단에 `Cache` 파사드를 "불러오는" 부분에 주목해 주십시오. 이 파사드는 `Illuminate\Contracts\Cache\Factory` 인터페이스의 구현체에 접속할 수 있는 프록시로 동작합니다. 파사드를 사용한 어떠한 호출이라도 라라벨의 캐시 서비스의 구현체에 전달됩니다.

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

대신에, `Cache` 파사드는 기본 `Facade` 클래스를 상속하고 `getFacadeAccessor()` 메소드를 정의하고 있습니다. 이 메소드의 역할이 서비스 컨테이너의 바인딩 이름을 반환한다는 것입니다. 사용자가 `Cache` 파사드의 어떤 스태틱 메소드를 참조하려고 할 때 라라벨은 [서비스 컨테이너](/docs/{{version}}/container)로 부터 `cache` 로 이름지어진 바인딩 객체를 찾아 메소드 호출을 요청할 것입니다(이 경우 `get` 메소드).

<a name="real-time-facades"></a>
## 실시간 파사드

실시간 파사드를 사용하면 애플리케이션의 모든 클래스를 마치 파사드처럼 취급할 수 있습니다. 이것이 어떻게 사용되는지 설명하기 위해 먼저 실시간 파사드를 사용하지 않는 일부 코드를 살펴보겠습니다. 예를 들어 `Podcast` 모델에 `publish` 메서드가 있다고 가정해 보겠습니다. 그러나 팟캐스트를 게시하려면 `Publisher` 인스턴스를 주입해줘야 합니다.

    <?php

    namespace App\Models;

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

메소드에 publisher 구현체를 주입하면 주입된 publisher 는 mock 할 수 있기 때문에, 메소드를 분리시켜 테스트를 용이하게 합니다. 그렇지만, `publish` 메소드를 호출할 때마다 매번 publisher 인스턴스를 전달할 필요가 있습니다. 리얼타임 파사드를 사용하면, 동일한 테스트 유효성을 유지하면서도, 명시적으로 `Publisher` 인스턴스를 전달하지 않아도 됩니다. 리얼타임 파사드를 생성하기 위해서는 Import 한 클래스 이름 앞에 `Facades` 를 붙이면 됩니다.

    <?php

    namespace App\Models;

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

리얼타임 파사드가 사용되면, publisher 구현체는 `Facades` 클래스네임 뒤에 정의된 인터페이스 또는 클래스 네임을 사용하여 서비스 컨테이너에서 의존성이 해결됩니다. 테스트를 진행할 때에는, 이 메소드 호출을 mock 하기 위해서 라라벨에 내장된 파사드 테스팅 헬퍼를 사용할 수 있습니다.

    <?php

    namespace Tests\Feature;

    use App\Models\Podcast;
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
            $podcast = Podcast::factory()->create();

            Publisher::shouldReceive('publish')->once()->with($podcast);

            $podcast->publish();
        }
    }

<a name="facade-class-reference"></a>
## 파사드 클래스 목록

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
Date  |  [Illuminate\Support\DateFactory](https://laravel.com/api/{{version}}/Illuminate/Support/DateFactory.html)  |  `date`
DB  |  [Illuminate\Database\DatabaseManager](https://laravel.com/api/{{version}}/Illuminate/Database/DatabaseManager.html)  |  `db`
DB (Instance)  |  [Illuminate\Database\Connection](https://laravel.com/api/{{version}}/Illuminate/Database/Connection.html)  |  `db.connection`
Event  |  [Illuminate\Events\Dispatcher](https://laravel.com/api/{{version}}/Illuminate/Events/Dispatcher.html)  |  `events`
File  |  [Illuminate\Filesystem\Filesystem](https://laravel.com/api/{{version}}/Illuminate/Filesystem/Filesystem.html)  |  `files`
Gate  |  [Illuminate\Contracts\Auth\Access\Gate](https://laravel.com/api/{{version}}/Illuminate/Contracts/Auth/Access/Gate.html)  |  &nbsp;
Hash  |  [Illuminate\Contracts\Hashing\Hasher](https://laravel.com/api/{{version}}/Illuminate/Contracts/Hashing/Hasher.html)  |  `hash`
Http  |  [Illuminate\Http\Client\Factory](https://laravel.com/api/{{version}}/Illuminate/Http/Client/Factory.html)  |  &nbsp;
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
