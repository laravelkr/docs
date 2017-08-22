# 파사드

- [소개하기](#introduction)
- [언제 파사드를 사용하는가](#when-to-use-facades)
    - [파사드 Vs. 의존성 주입](#facades-vs-dependency-injection)
    - [파사드 Vs. 헬퍼 함수](#facades-vs-helper-functions)
- [파사드가 어떻게 동작하는가](#how-facades-work)
- [파사드 클래스 목록](#facade-class-reference)

<a name="introduction"></a>
## 소개하기

파사드는 어플리케이션의 [서비스 컨테이너](/docs/{{version}}/container)에서 사용가능한 클래스들에 대한 "정적" 인터페이스를 제공합니다. 라라벨은 대부분의 라라벨의 기능에 엑세스하는 많은 파사드들을 제공합니다. 라라벨의 파사드 는 서비스 컨테이너에 등록된 클래스들에 대한 일종의 "정적 프록시" 역할을 수행하는데, 이를 통해서 전통적인 정적 메소드 보다 간결한 문법과 테스트의 용이성 그리고 코드의 유연성을 유지하는 이점을 제공합니다.

모든 라라벨의 파사드는 `Illuminate\Support\Facades` 네임스페이스 안에 정의되어 있습니다. 따라서 다음처럼 손쉽게 파사드에 엑세스 할 수 있습니다:

    use Illuminate\Support\Facades\Cache;

    Route::get('/cache', function () {
        return Cache::get('key');
    });

라라벨 문서상에서 프레임 워크의 다양한 기능의 사용법을 익히기 위해서 파사드를 사용하는 예제를 많이 확인할 수 있습니다.

<a name="when-to-use-facades"></a>
## 언제 파사드를 사용하는가?

파사드는 다양한 장점이 있습니다. 파사드는 의존성으로 주입되거나 직접 설정해야 하는 긴 이름의 클래스들을 일일이 기억하지 않고서도 알 수 있는, 간결하고 기억하기 쉬운 문법을 제공합니다. 또한 PHP의 독특한 다이나믹 메소드의 사용 덕분에, 테스트도 쉬워집니다.

그러나 파사드를 사용하는데 몇 가지 주의해야 할 점도 존재합니다. 파사드의 가장 주요한 위험은 클래스의 책임 범위의 잘못된 사용입니다. 파사드는 사용하기가 매우 쉽고, 의존성 주입도 필요하지 않기 때문에, 하나의 클래스 파일 안에서 여러개의 파사드를 사용하는 코드가 자꾸 늘어나도록 클래스를 구성하기가 매우 쉽습니다. 의존성 주입을 사용하면 클래스가 커짐에 따라서 생성자가 비대해진다는 시각적인 피드백을 주게 되어 이러한 잠재적 위험이 억제됩니다. 따라서 파사드를 사용할 때에는, 클래스의 책임 범위를 작게 유지하도록 클래스의 사이즈를 구성하는데 특별한 주의가 필요합니다.

> {tip} 라라벨과 연관된 써드파티 패키지를 구성할 때, 파사드 사용하는 대신에 [라라벨 contracts](/docs/{{version}}/contracts)를 주입하는 것이 더 나은 방법입니다. 패키지는 라라벨의 외부에서 구성되기 때문에, 라라벨의 파사드 테스팅 헬퍼에 엑세스할 필요는 없습니다.

<a name="facades-vs-dependency-injection"></a>
### 파사드 Vs. 의존성 주입

의존성 주입의 주요한 장점중 하나는 주입된 클래스의 구현체를 변경할 수 있다는 특성입니다. 이는 테스팅을 수행하는 동안 모킹 객체과 스터브를 주입할 수 있게 하고, 다양한 메소드가 호출되는 것을 확인할 수 있게 하여 유용합니다.

일반적으로, 정적 클래스 메소드에 대해서는 모킹(mock) 이나 스터가 사용가능하지 않습니다. 하지만 파사드는 서비스 컨테이너에 의해서 의존성이 해결되는 클래스 객체의 프록시 메소드로 다이나믹 메소드를 사용하기 때문에, 실제 주입된 클래스 인스턴스를 테스트하는 것과 마찬가지로 파사드를 테스트할 수 있습니다. 예를 들어 다음의 주어진 라우트를 보겠습니다:

    use Illuminate\Support\Facades\Cache;

    Route::get('/cache', function () {
        return Cache::get('key');
    });

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
### 파사드 Vs. 헬퍼 함수

파사드 뿐만 아니라, 라라벨은 뷰 파일을 생성하거나, 이벤트를 발생시키거나, Job을 실행시키거나 또는 HTTP 응답을 반환하는등의 공통된 작업을 수행하는 다양한 "헬퍼" 함수를 포함하고 있습니다. 이러한 다수의 헬퍼 함수들은 파사드와 일치하는 동일한 동작들을 수행합니다. 예를 들어 다음의 파사드 호출과 헬퍼 함수 호출은 동일합니다:

    return View::make('profile');

    return view('profile');

이 파사드와 헬퍼함수 사이에는 차이점이 전혀 없습니다. 헬퍼함수를 사용하는 경우에도 일치하는 파사드와 같이 동일하게 테스트 할 수 있습니다. 예를 들어 다음의 주어진 라우트를 보겠습니다:

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

        $this->visit('/cache')
             ->see('value');
    }

<a name="how-facades-work"></a>
## 파사드는 어떻게 동작하는가

라라벨 어플리케이션에서, 파사드는 컨테이너의 객체에 엑세스하는 방법을 제공하는 클래스라고 할 수 있습니다. 이 작업을 수행하는 주요 매커니즘이 파사드 클래스안에 있습니다. 라라벨의 파사드들과 여러분이 작성한 파사드들은 기본 `Illuminate\Support\Facades\Facade` 클래스를 상속받습니다.

`Facade` 기본 클래스는 `__callStatic()` 매직 매소드를 사용하여 여러분이 작성한 파사드에 대한 호출을 컨테이너에서 의존성이 해결된 객체로 전달합니다. 다음의 예제에서 라라벨의 캐시 시스템을 호출합니다. 이 코드를 보자면, 아마 `Cache` 클래스의 `get` static 메소드를 호출한다고 생각할 수 있습니다.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Support\Facades\Cache;
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

파일의 상단에 `Cache` 파사드를 사용하고 있는 부분에 주목해 주십시오. 이 파사드는 `Illuminate\Contracts\Cache\Factory` 인터페이스의 구현체에 접속할 수 있는 프록시로 동작합니다. 파사드를 사용한 어떠한 호출이라도 라라벨의 캐시 서비스의 구현체에 전달됩니다.

실제로 `Illuminate\Support\Facades\Cache`를 살펴보면 `get`이라는 static 메소드는 찾을 수가 없습니다:

    class Cache extends Facade
    {
        /**
         * Get the registered name of the component.
         *
         * @return string
         */
        protected static function getFacadeAccessor() { return 'cache'; }
    }

대신에, `Cache` 파사드는 기본 `Facade` 클래스를 상속하고 `getFacadeAccessor()` 메소드를 정의하고 있습니다. 이 메소드의 역할이 서비스 컨테이너의 바인딩 이름을 반환한다는 것입니다. 사용자가 `Cache` 파사드의 어떤 스태틱 메소드를 참조하려고 할 때 라라벨은 [서비스 컨테이너]로 부터 `cache` 로 이름지어진 바인딩 객체를 찾아 메소드 호출을 요청할 것입니다(이 경우 `get` 메소드)

<a name="facade-class-reference"></a>
## 파사드 클래스 목록

다음은 모든 파사드 와 실제 클래스에 대한 목록입니다. 여러분이 특정한 파사드를 기준으로 API 문서를 빠르게 찾고자 하는 경우에 도움이 될것입니다. 응용이 가능하도록 [서비스 컨테이너 바인딩](/docs/{{version}}/container)도 포함되어 있습니다. 

Facade  |  Class  |  Service Container Binding
------------- | ------------- | -------------
App  |  [Illuminate\Foundation\Application](https://laravel.com/api/{{version}}/Illuminate/Foundation/Application.html)  | `app`
Artisan  |  [Illuminate\Contracts\Console\Kernel](https://laravel.com/api/{{version}}/Illuminate/Contracts/Console/Kernel.html)  |  `artisan`
Auth  |  [Illuminate\Auth\AuthManager](https://laravel.com/api/{{version}}/Illuminate/Auth/AuthManager.html)  |  `auth`
Blade  |  [Illuminate\View\Compilers\BladeCompiler](https://laravel.com/api/{{version}}/Illuminate/View/Compilers/BladeCompiler.html)  |  `blade.compiler`
Bus  |  [Illuminate\Contracts\Bus\Dispatcher](https://laravel.com/api/{{version}}/Illuminate/Contracts/Bus/Dispatcher.html)  |  &nbsp;
Cache  |  [Illuminate\Cache\Repository](https://laravel.com/api/{{version}}/Illuminate/Cache/Repository.html)  |  `cache`
Config  |  [Illuminate\Config\Repository](https://laravel.com/api/{{version}}/Illuminate/Config/Repository.html)  |  `config`
Cookie  |  [Illuminate\Cookie\CookieJar](https://laravel.com/api/{{version}}/Illuminate/Cookie/CookieJar.html)  |  `cookie`
Crypt  |  [Illuminate\Encryption\Encrypter](https://laravel.com/api/{{version}}/Illuminate/Encryption/Encrypter.html)  |  `encrypter`
DB  |  [Illuminate\Database\DatabaseManager](https://laravel.com/api/{{version}}/Illuminate/Database/DatabaseManager.html)  |  `db`
DB (Instance)  |  [Illuminate\Database\Connection](https://laravel.com/api/{{version}}/Illuminate/Database/Connection.html)  |  &nbsp;
Event  |  [Illuminate\Events\Dispatcher](https://laravel.com/api/{{version}}/Illuminate/Events/Dispatcher.html)  |  `events`
File  |  [Illuminate\Filesystem\Filesystem](https://laravel.com/api/{{version}}/Illuminate/Filesystem/Filesystem.html)  |  `files`
Gate  |  [Illuminate\Contracts\Auth\Access\Gate](https://laravel.com/api/{{version}}/Illuminate/Contracts/Auth/Access/Gate.html)  |  &nbsp;
Hash  |  [Illuminate\Contracts\Hashing\Hasher](https://laravel.com/api/{{version}}/Illuminate/Contracts/Hashing/Hasher.html)  |  `hash`
Lang  |  [Illuminate\Translation\Translator](https://laravel.com/api/{{version}}/Illuminate/Translation/Translator.html)  |  `translator`
Log  |  [Illuminate\Log\Writer](https://laravel.com/api/{{version}}/Illuminate/Log/Writer.html)  |  `log`
Mail  |  [Illuminate\Mail\Mailer](https://laravel.com/api/{{version}}/Illuminate/Mail/Mailer.html)  |  `mailer`
Notification  |  [Illuminate\Notifications\ChannelManager](https://laravel.com/api/{{version}}/Illuminate/Notifications/ChannelManager.html)  |  &nbsp;
Password  |  [Illuminate\Auth\Passwords\PasswordBrokerManager](https://laravel.com/api/{{version}}/Illuminate/Auth/Passwords/PasswordBrokerManager.html)  |  `auth.password`
Queue  |  [Illuminate\Queue\QueueManager](https://laravel.com/api/{{version}}/Illuminate/Queue/QueueManager.html)  |  `queue`
Queue (Instance)  |  [Illuminate\Contracts\Queue\Queue](https://laravel.com/api/{{version}}/Illuminate/Contracts/Queue/Queue.html)  |  `queue`
Queue (Base Class) |  [Illuminate\Queue\Queue](https://laravel.com/api/{{version}}/Illuminate/Queue/Queue.html)  |  &nbsp;
Redirect  |  [Illuminate\Routing\Redirector](https://laravel.com/api/{{version}}/Illuminate/Routing/Redirector.html)  |  `redirect`
Redis  |  [Illuminate\Redis\Database](https://laravel.com/api/{{version}}/Illuminate/Redis/Database.html)  |  `redis`
Request  |  [Illuminate\Http\Request](https://laravel.com/api/{{version}}/Illuminate/Http/Request.html)  |  `request`
Response  |  [Illuminate\Contracts\Routing\ResponseFactory](https://laravel.com/api/{{version}}/Illuminate/Contracts/Routing/ResponseFactory.html)  |  &nbsp;
Route  |  [Illuminate\Routing\Router](https://laravel.com/api/{{version}}/Illuminate/Routing/Router.html)  |  `router`
Schema  |  [Illuminate\Database\Schema\Blueprint](https://laravel.com/api/{{version}}/Illuminate/Database/Schema/Blueprint.html)  |  &nbsp;
Session  |  [Illuminate\Session\SessionManager](https://laravel.com/api/{{version}}/Illuminate/Session/SessionManager.html)  |  `session`
Session (Instance)  |  [Illuminate\Session\Store](https://laravel.com/api/{{version}}/Illuminate/Session/Store.html)  |  &nbsp;
Storage  |  [Illuminate\Contracts\Filesystem\Factory](https://laravel.com/api/{{version}}/Illuminate/Contracts/Filesystem/Factory.html)  |  `filesystem`
URL  |  [Illuminate\Routing\UrlGenerator](https://laravel.com/api/{{version}}/Illuminate/Routing/UrlGenerator.html)  |  `url`
Validator  |  [Illuminate\Validation\Factory](https://laravel.com/api/{{version}}/Illuminate/Validation/Factory.html)  |  `validator`
Validator (Instance)  |  [Illuminate\Validation\Validator](https://laravel.com/api/{{version}}/Illuminate/Validation/Validator.html) |  &nbsp;
View  |  [Illuminate\View\Factory](https://laravel.com/api/{{version}}/Illuminate/View/Factory.html)  |  `view`
View (Instance)  |  [Illuminate\View\View](https://laravel.com/api/{{version}}/Illuminate/View/View.html)  |  &nbsp;
