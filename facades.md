# 파사드(Facades)

- [소개](#introduction)
- [설명 ](#explanation)
- [실제 사용법](#practical-usage)
- [파사드 생성하기](#creating-facades)
- [파사드 Mocking](#mocking-facades)
- [파사드 참조 목록](#facade-class-reference)

<a name="introduction"></a>
## 소개

파사드는 어플리케이션의 [서비스 컨테이너](/docs/5.0/container)에서 사용 가능한 클래스들에 대한 "정적" 인터페이스를 제공합니다. 라라벨은 많은 파사드를 가지고 있으며 여러분이 알지 못하는 사이에도 이미 사용하고 있을 것입니다. 라라벨 “파사드”는 서비스 컨테이너에 등록된 클래스들에 대한 일종의 “정적 프록시” 역할을 수행하는 데, 이를 통해서 전통적인 정적 메소드 보다 간결한 문법과 테스트의 용이성 그리고 코드의 유연성을 유지하는 이점을 제공합니다.

때로는 여러분의 어플리케이션이나 패키지에서도  파사드를 만들고 싶어질 것입니다. 따라서 파사드의 개념과 개발 방법, 이러한 클래스들의 사용방법에 대해서 알아보겠습니다.

> **주의** 파사드에 대해서 알아보기 전에 라라벨의 [서비스 컨테이너](/docs/5.0/container)에 익숙질 것을 적극 권장합니다.

<!--chak-comment-파사드(Facades)-소개-->

<a name="explanation"></a>
## 설명

라라벨 어플리케이션에의 전체 맥락에서 보자면 파사드는 컨테이너의 객체에 엑세스하는 방법을 제공하는 클래스라고 할 수 있습니다. 이 작업을 수행하는 주요 매커니즘이 파사드 클래스안에 있습니다. 라라벨의 파사드들과 여러분이 작성한 파사드들은 기본 `Facade` 클래스를 상속받습니다.

여러분이 만드는 파사드 클래스는 `getFacadeAccessor`라는 하나의 메소드를 구현해야 합니다. `getFacadeAccessor`메소드의 역할은 컨테이너로부터 어떤 의존성을 해결하는지에 대해서 정의하는 것입니다. `Facade` 기본 클래스는 `__callStatic()` 매직 메소드를 사용하여 여러분이 작성한 파사드에 대한 호출을 의존성이 해결된 객체로 전달합니다.

따라서 여러분이 `Cache::get`과 같이 파사드를 호출할 때, 라라벨은 서비스 컨테이너로부터 의존성을 해결하여 캐시 매니저 획득하고 이 캐시 매니저의 `get` 메소드를 호출해줍니다. 기술적으로 라라벨의 파사드는 라라벨의 서비스 컨테이너를 하나의 서비스로서 사용하기 위한 편리한 기법입니다.

<!--chak-comment-파사드(Facades)-설명-->

<a name="practical-usage"></a>
## 실제 사용법

다음의 예제에서 라라벨 캐시 시스템을 호출합니다. 이 코드를 보자면, 아마 `Cache` 클래스의 `get` static 메소드를 호출한다고 생각할 수 있습니다.

	$value = Cache::get('key');

그러나 실제로 `Illuminate\Support\Facades\Cache`를 살펴보면 `get`이라는 static 메소드는 찾을 수가 없습니다.

	class Cache extends Facade {

		/**
		 * Get the registered name of the component.
		 *
		 * @return string
		 */
		protected static function getFacadeAccessor() { return 'cache'; }

	}

캐시 클래스는 기존 `Facade` 클래스를 상속하고 `getFacadeAccessor()` 메소드를 정의하고 있습니다. 유념할 것은 이 메소드의 역할은 컨테이너에 등록된 서비스의 이름을 반환한다는 것입니다.

사용자가 `Cache` 파사드의 어떤 static 메소드를 사용할 때 라라벨은 서비스 컨테이너에 `cache`로 바인딩된 객체의 인스턴스에게 요청된 메소드(여기에서는 `get`)을 수행합니다.

따라서 `Cache::get` 호출은 다음처럼 다시 쓸 수 있습니다.

	$value = $app->make('cache')->get('key');

#### 파사드 가져오기

네임스페이스가 지정된 컨트롤러 안에서 파사드를 사용할 경우 파사드를 use 하도록 해야한다는 것을 기억하십시오. 모든 파사드는 글로벌 네임스페이스에 지정되어 있습니다.

	<?php namespace App\Http\Controllers;

	use Cache;

	class PhotosController extends Controller {

		/**
		 * Get all of the application photos.
		 *
		 * @return Response
		 */
		public function index()
		{
			$photos = Cache::get('photos');

			//
		}

	}

<!--chak-comment-파사드(Facades)-실제-사용법-->

<a name="creating-facades"></a>
## 파사드 생성하기

어플리케이션이나 패키지에 파사드를 생성하는 것은 어렵지 않습니다. 3가지만 기억하십시오.

- 서비스 컨테이너 바인딩
- 파사드 클래스
- 파사드 별칭 설정

다음의 예제를 살펴보겠습니다. `PaymentGateway\Payment`라는 클래스를 정의하고 있습니다.

	namespace PaymentGateway;

	class Payment {

		public function process()
		{
			//
		}

	}

이 클래스를 서비스 컨테이너에 의해서 의존성이 해결될 수 있도록 해야 합니다. 따라서 서비스 프로바이더에 바인딩을 추가합니다.

	App::bind('payment', function()
	{
		return new \PaymentGateway\Payment;
	});

바인딩을 추가하는 좋은 방법은 `PaymentServiceProvider`라는 [서비스 프로바이더](/docs/5.0/container#service-providers)를 생성하고 `register` 메소드에서 바인딩을 추가하는 것입니다.  그 다음에 `config/app.php` 설정 파일에서 라라벨에서 로딩하는 서비스 프로바이더 항목을 설정합니다.

이제 파사드 클래스를 생성합니다.

	use Illuminate\Support\Facades\Facade;

	class Payment extends Facade {

		protected static function getFacadeAccessor() { return 'payment'; }

	}

마지막으로 원할 경우, `config/app.php` 설정 파일의 `aliases` 배열을 통해서 파사드의 별칭을 지정 할 수 있습니다. 이제 `Payment` 클래스 인스턴스의 `process` 메소드를 호출 할 수 있게 되었습니다.

	Payment::process();

### 오토로딩 별칭에 대한 주의사항

[PHP는 타입힌트가 정의되지 않은 유형의 클래스들에 대한 오토로드를 시도 하지 않으므로](https://bugs.php.net/bug.php?id=39003) `aliases` 배열 안에 클래스가 몇몇 인스턴스들 안에서는 사용 가능하지 않습니다. 만약 `\ServiceWrapper\ApiTimeoutException`이 `ApiTimeoutException` 라는 별칭으로 지정되었고, `\ServiceWrapper` 외부 네임 스페이스에서 `catch (ApiTimeoutException $e)`를 하려 해도 던져진 예외는 포착되지 않습니다. 별칭으로 타입힌트가 설정된 클래스들 사이에서도 비슷한 문제는 발생됩니다. 이에대한 유일한 해결책은 타입힌트를 사용하려는 클래스들 제일 앞부분에 `use`를 사용해서 미리 지정해두는 방법입니다.

<!--chak-comment-파사드(Facades)-파사드-생성하기-->

<a name="mocking-facades"></a>
## 파사드 Mocking

단위 테스트는 왜 파사드가 저렇게 동작하는지에 대한 매우 중요한 이유입니다. 사실 테스트의 용이성은 파사드가 존재하는 주요한 이유입니다. 보다 자세한 내용에 대해서는 문서의 [mocking facades](/docs/testing#mocking-facades) 섹션을 참고하십시오.

<!--chak-comment-파사드(Facades)-파사드-Mocking-->

<a name="facade-class-reference"></a>
## 파사드 참조 목록

다음은 모든 파사드와 실제 클래스에 대한 목록입니다. 여러분이 특정한 파사드를 기준으로 API 문서를 빠르게 찾고자 하는 경우에 도움이 될것입니다. 응용이 가능하도록 [서비스 컨테이너 바인딩](/docs/5.0/container)도 포함되어 있습니다.

Facade  |  Class  |  Service Container Binding
------------- | ------------- | -------------
App  |  [Illuminate\Foundation\Application](http://laravel.com/api/5.0/Illuminate/Foundation/Application.html)  | `app`
Artisan  |  [Illuminate\Console\Application](http://laravel.com/api/5.0/Illuminate/Console/Application.html)  |  `artisan`
Auth  |  [Illuminate\Auth\AuthManager](http://laravel.com/api/5.0/Illuminate/Auth/AuthManager.html)  |  `auth`
Auth (Instance)  |  [Illuminate\Auth\Guard](http://laravel.com/api/5.0/Illuminate/Auth/Guard.html)  |
Blade  |  [Illuminate\View\Compilers\BladeCompiler](http://laravel.com/api/5.0/Illuminate/View/Compilers/BladeCompiler.html)  |  `blade.compiler`
Bus  |  [Illuminate\Contracts\Bus\Dispatcher](http://laravel.com/api/5.0/Illuminate/Contracts/Bus/Dispatcher.html)  |
Cache  |  [Illuminate\Cache\CacheManager](http://laravel.com/api/5.0/Illuminate/Cache/Repository.html)  |  `cache`
Config  |  [Illuminate\Config\Repository](http://laravel.com/api/5.0/Illuminate/Config/Repository.html)  |  `config`
Cookie  |  [Illuminate\Cookie\CookieJar](http://laravel.com/api/5.0/Illuminate/Cookie/CookieJar.html)  |  `cookie`
Crypt  |  [Illuminate\Encryption\Encrypter](http://laravel.com/api/5.0/Illuminate/Encryption/Encrypter.html)  |  `encrypter`
DB  |  [Illuminate\Database\DatabaseManager](http://laravel.com/api/5.0/Illuminate/Database/DatabaseManager.html)  |  `db`
DB (Instance)  |  [Illuminate\Database\Connection](http://laravel.com/api/5.0/Illuminate/Database/Connection.html)  |
Event  |  [Illuminate\Events\Dispatcher](http://laravel.com/api/5.0/Illuminate/Events/Dispatcher.html)  |  `events`
File  |  [Illuminate\Filesystem\Filesystem](http://laravel.com/api/5.0/Illuminate/Filesystem/Filesystem.html)  |  `files`
Hash  |  [Illuminate\Contracts\Hashing\Hasher](http://laravel.com/api/5.0/Illuminate/Contracts/Hashing/Hasher.html)  |  `hash`
Input  |  [Illuminate\Http\Request](http://laravel.com/api/5.0/Illuminate/Http/Request.html)  |  `request`
Lang  |  [Illuminate\Translation\Translator](http://laravel.com/api/5.0/Illuminate/Translation/Translator.html)  |  `translator`
Log  |  [Illuminate\Log\Writer](http://laravel.com/api/5.0/Illuminate/Log/Writer.html)  |  `log`
Mail  |  [Illuminate\Mail\Mailer](http://laravel.com/api/5.0/Illuminate/Mail/Mailer.html)  |  `mailer`
Password  |  [Illuminate\Auth\Passwords\PasswordBroker](http://laravel.com/api/5.0/Illuminate/Auth/Passwords/PasswordBroker.html)  |  `auth.password`
Queue  |  [Illuminate\Queue\QueueManager](http://laravel.com/api/5.0/Illuminate/Queue/QueueManager.html)  |  `queue`
Queue (Instance) |  [Illuminate\Queue\QueueInterface](http://laravel.com/api/5.0/Illuminate/Queue/QueueInterface.html)  |
Queue (Base Class) |  [Illuminate\Queue\Queue](http://laravel.com/api/5.0/Illuminate/Queue/Queue.html)  |
Redirect  |  [Illuminate\Routing\Redirector](http://laravel.com/api/5.0/Illuminate/Routing/Redirector.html)  |  `redirect`
Redis  |  [Illuminate\Redis\Database](http://laravel.com/api/5.0/Illuminate/Redis/Database.html)  |  `redis`
Request  |  [Illuminate\Http\Request](http://laravel.com/api/5.0/Illuminate/Http/Request.html)  |  `request`
Response  |  [Illuminate\Contracts\Routing\ResponseFactory](http://laravel.com/api/5.0/Illuminate/Contracts/Routing/ResponseFactory.html)  |
Route  |  [Illuminate\Routing\Router](http://laravel.com/api/5.0/Illuminate/Routing/Router.html)  |  `router`
Schema  |  [Illuminate\Database\Schema\Blueprint](http://laravel.com/api/5.0/Illuminate/Database/Schema/Blueprint.html)  |
Session  |  [Illuminate\Session\SessionManager](http://laravel.com/api/5.0/Illuminate/Session/SessionManager.html)  |  `session`
Session (Instance)  |  [Illuminate\Session\Store](http://laravel.com/api/5.0/Illuminate/Session/Store.html)  |
Storage  |  [Illuminate\Contracts\Filesystem\Factory](http://laravel.com/api/5.0/Illuminate/Contracts/Filesystem/Factory.html)  |  `filesystem`
URL  |  [Illuminate\Routing\UrlGenerator](http://laravel.com/api/5.0/Illuminate/Routing/UrlGenerator.html)  |  `url`
Validator  |  [Illuminate\Validation\Factory](http://laravel.com/api/5.0/Illuminate/Validation/Factory.html)  |  `validator`
Validator (Instance)  |  [Illuminate\Validation\Validator](http://laravel.com/api/5.0/Illuminate/Validation/Validator.html) |
View  |  [Illuminate\View\Factory](http://laravel.com/api/5.0/Illuminate/View/Factory.html)  |  `view`
View (Instance)  |  [Illuminate\View\View](http://laravel.com/api/5.0/Illuminate/View/View.html)  |

<!--chak-comment-파사드(Facades)-파사드-참조-목록-->
