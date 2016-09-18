# 업그레이드 가이드(Upgrade Guide)

- [Upgrading To 5.0.16](#upgrade-5.0.16)
- [Upgrading To 5.0 From 4.2](#upgrade-5.0)
- [Upgrading To 4.2 From 4.1](#upgrade-4.2)
- [Upgrading To 4.1.29 From <= 4.1.x](#upgrade-4.1.29)
- [Upgrading To 4.1.26 From <= 4.1.25](#upgrade-4.1.26)
- [Upgrading To 4.1 From 4.0](#upgrade-4.1)

<a name="upgrade-5.0.16"></a>
## 5.0.16으로 업그레이드

`bootstrap/autoload` 파일에서 `$compiledPath` 변수를 다음처럼 변경합니다:

    $compiledPath = __DIR__.'/../vendor/compiled.php';

<!--chak-comment-업그레이드-가이드(Upgrade-Guide)-5-0-16으로-업그레이드-->

<a name="upgrade-5.0"></a>
## 4.2에서 5.0으로 업그레이드

### 새로 설치 후 마이그레이션 하기

업그레이드에 대한 권장사항은 새롭게 라라벨 `5.0` 버전을 인스톨하고 `4.2` 버전의 사이트의 고유한 어플리케이션 파일들을 새로운 어플리케이션에 복사하라는 것입니다. 여기에는 컨트롤러, 라우트, 엘로퀀트 모델들, 아티즌 명령어들, asset 파일들 그리고 어플리케이션에 지정된 기타 코드들을 포함합니다.

업그레이드를 시작하기 위해서 로컬 환경의 새로운 디렉토리에 [라라벨 5 어플리케이션을 설치](/docs/5.0/installation)합니다. 업그레이드를 위한 각 단계의 진행에 대해서 차근차근 알아보겠습니다.

### 컴포저 의존성 & 패키지

설치된 5.0 버전의 어플리케이션에 추가적인 컴포저 의존 패키지들을 복사해 넣는 것을 잊지 마십시오. 여기에는 SDK와 같은 서드 파티 코드도 포함됩니다.

라라벨5가 릴리즈 된 이후 바로는 라라벨과 연동되는 일부 패키지는 라라벨5와 호환이 되지 않는수도 있습니다. 패키지의 제작자에게 라라벨5 버전에 대한 대응계획을 확인합니다. 컴포저에 어플리케이션을 위한 추가적인 의존 패키지를 추가하였다면 `composer updata`를 실행 해야 합니다.

### 네임스페이스

라라벨 4에서는 기본적으로 어플리케이션의 코드에 네임스페이스가 구성되어 있지 않았습니다. 그래서 예를 들면 모든 Eloquent 모델들과 컨트롤러는 "글로벌" 네임스페이스 영역에 해당되었습니다. 빠른 전환을 위해서는 라라벨5에서도 마찬가지로 글로벌 네임스페이스 안에 이 클래스들을 지정되도록 합니다.

### 설정

#### 마이그레이션 환경 변수

`.env.example` 파일을 복사하여 이전 버전에서 `.env.php` 파일과 같은 역할을 하는 `5.0의 `.env` 파일을 생성합니다. `APP_ENV`와 `APP_KEY` (암호화 키), 데이터베이스 연결 정보, 캐시 그리고 세션 드라이버와 같은 어플리케이션에서 사용가능항 설정 값들을 지정할 수 있습니다.

추가로 이전버전의 `.env.php` 파일에 들어 있던 사용자가 지정한 값들을 `.env` 파일(실제 로컬 환경을 위한 값 설정)과 `.env.example` 파일 (다른 팀 구성원들이 참고할 샘플 값)에 복사합니다.)에 복사해 넣습니다.

환경 설정과 관련된 보다 자세한 내용은 [관련 문서](/docs/5.0/configuration#environment-configuration)를 확인하십시오.

> **주의** 라라벨5 어플리케이션을 실제 제품 서버에 배포하기 전에 적절한 값을 지정한 `.env` 파일을 준비해 둘 필요가 있습니다.

#### 설정 파일들

라라벨 5.0은 더 이상 `app/config/{구동환경의이름}/` 디렉토리를 주어진 구동 환경에 대한 설정파일로 사용하지 않습니다. 대신에  구동 환경에 의해 달라지는 설정 값들을 `.env` 파일로 이동 시켰습니다. 그리고 나서 설정 파일 안에서 `env(‘키’, ‘기본값’)`의 형태로 해당 값들에 엑세스 합니다. `config/database.php` 설정 파일에서 이러한 사용 예를 확인할 수 있습니다.

구동환경과 관계없이 변하지 않는 값들과 또는 `env()`를 통해서 여러분의 로컬 환경에 의해서 변화하는 값 두 가지들에 대한 설정 값들을 `config/` 디렉토리안에 들어 있는 설정 파일들에 지정합니다.

기억하십시오 만약 여러분이 `.env` 파일에 추가적인 키들을 추가하였다면, 마찬가지로 `.env.exampl` 파일에도 추가합니다. 여러분의 팀 동료들이 이를 참고하여 손쉽게 자신의 `.env` 파일을 생성하는 데 도움이 될 것입니다.

### 라우트

이전 버전의 `routes.php` 파일의 내용을 새로운 `app/Http/routes.php` 파일에 복사해 넣습니다.

### 컨트롤러

다음으로 모든 컨트롤러들을 `app/Http/Controllers` 디렉토리로 옮깁니다. 이 가이드에서는 네임스페이스를 사용하도록 변경하지 않기 때문에, `app/Http/Controllers` 디렉토리를 `composer.json` 파일의 `classmap` 여역에 추가합니다. 그런 다음 `app/Http/Controllers/Controller.php` 추상  클래스에서 네임스페이스를 제거합니다. 마이그레이션된 컨트롤러가 기본 클래스를 상속 받고 있는지 확인합니다.

`app/Providers/RouteServiceProvider.php` 파일 내부에서 `namespace` 속성을 `null`로 설정합니다.

### 라우트 필터

`app/filters.php` 파일에 있던 필터 바인딩들을 `app/Providers/RouteServiceProvider.php`파일의 `boot()` 메소드에 복사합니다. `app/Providers/RouteServiceProvider.php` 파일에 `use Illuminate\Support\Facades\Route;`를 추가하여 `Route` 파사드를 사용할 수 있도록 합니다.

`auth`와 `csrf`와 같은 기본적인 라라벨 4.0의 필터는 미들웨어로 변경되었기 때문에 따로 옮길 필요가 없습니다. 이전의 기본 필터들(예를 들어, `['before' => 'auth']`)을 참조하는 라우트나 컨트롤러들을 수정하여 새로운 미들웨어(예를 들어, `['middleware' => 'auth’].`)를 참조 하도록 변경합니다.

필터는 라라벨5에서 삭제되지 않았습니다. 여러분은 여전히 `before`와 `after`를 사용하여 사용자 정의 필터를 지정하고 사용할 수 있습니다.

### 전역 CSRF

기본적으로 [CSRF 방지](/docs/5.0/routing#csrf-protection)가 전체 라우트에서 활성화되어 있습니다. 이를 비활성화 시키거나 특정 라우트에서만 수동으로 활성화하고자한다면 `App\Http\Kernel`파일의  `middleware` 배열에서 다음 미들웨어를 삭제합니다.

    'App\Http\Middleware\VerifyCsrfToken',

다른곳에서 사용하기 위해서 삭제한 라인을 `$routeMiddleware`에 추가합니다:

    'csrf' => 'App\Http\Middleware\VerifyCsrfToken',

이제 개별 라우트 / 컨트롤러에 대해서 `['middleware' => 'csrf']`와 같이 미들웨어를 지정할 수 있습니다. 미들웨어에 대한 보다 자세한 사항은 [미들웨어 문서](/docs/5.0/middleware)를 참고하십시오.

### Eloquent 모델

Eloquent 모델을 모아두기 위한 `app/Models` 디렉토리를 생성합니다. 그런다음에 이 디렉토리를 `composer.json` 파일의 `classmap`에 추가합니다.

`SoftDeletingTrait`을 사용하는 모델은  `Illuminate\Database\Eloquent\SoftDeletes`을 사용하도록 변경합니다.

#### Eloquent 캐싱

Eloquent는 더 이상 쿼리 캐시를 위한 `remember` 메소드를 제공하지 않습니다. 여러분은 이제 `Cache::remember` 함수를 사용하여 수동으로 쿼리를 캐싱해야 합니다. 캐시와 관련된 보다 자세한 정보는 [해당 문서](/docs/5.0/cache)를 참고하십시오.

### 사용자 인증 모델

`User` 모델을 라라벨5의 인증 시스템 용으로 업그레이드 하려면 다음과 같이 하면 됩니다:

** 다음의 `use` 블럭을 삭제합니다 :**

```php
use Illuminate\Auth\UserInterface;
use Illuminate\Auth\Reminders\RemindableInterface;
```

** 다음의 `use` 블럭을 추가합니다:**

```php
use Illuminate\Auth\Authenticatable;
use Illuminate\Auth\Passwords\CanResetPassword;
use Illuminate\Contracts\Auth\Authenticatable as AuthenticatableContract;
use Illuminate\Contracts\Auth\CanResetPassword as CanResetPasswordContract;
```

**UserInterface와 RemindableInterface 인터페이스를 제거합니다.

**클래스가 다음의 인터페이스들을 구현하도록 선언합니다:**

```php
implements AuthenticatableContract, CanResetPasswordContract
```

**클래스 선언부분 안에서 다음의 trait들을 사용하도록 합니다:**

```php
use Authenticatable, CanResetPassword;
```

**이 방법을 사용하면, 클래스의 선언부분의 use 블록에서 `Illuminate\Auth\Reminders\RemindableTrait`와 `Illuminate\Auth\UserTrait`를 제거합니다.

### 캐셔 변경사항

[라라벨 캐셔](/docs/5.0/billing)의 trait과 인터페이스의 이름이 변경되었습니다.  `BillableTrait` 대신 `Laravel\Cashier\Billable` trait을 사용합니다. 그리고 `Laravel\Cashier\BillableInterface` 대신에 `Laravel\Cashier\Contracts\Billable` 인터페이스를 사용합니다. 다른 메소드는 변경되지 않았습니다.

### 아티즌 명령어들

`app/commands` 디렉토리의 모든 커맨드 클래스드들을 새로운 `app/Console/Commands` 디렉토리로 이옮깁니다. 그다음에 `composer.json` 파일의 `classmap`에 `app/Console/Commands`을 추가합니다.

그리고 `start/artisan.php` 파일의 아티즌 명령어 리스트를 `app/Console/Kernel.php` 파일의 `commands` 배열에 복사합니다.

### 데이터베이스 마이그레이션 & 시딩

데이터베이스에 이미 user 테이블에 존재하기 때문에 라라벨 5.0에 포함된 두 개의 마이그레이션 파일을 삭제 합니다.

이전 `app/database/migrations` 디렉토리의 모든 마이그레이션 클래스들을 새로운 `database/migrations` 디렉토리로 옮깁니다. 시드 파일들은 `app/database/seeds`에서 `database/seeds`로 옮깁니다.

### 글로벌 IoC 바인딩

만약 `start/global.php` 파일에 [IoC](/docs/5.0/container) 바인딩들을 가지고 있었다면, `app/Providers/AppServiceProvider.php`의 `register` 메소드로 옮깁니다. `App` 파사드를 사용해야 할 것입니다.

옵션으로, 바인딩들을 해당하는 개별 서비스 프로바이더에 나누어 옮길 수도있습니다.

### 뷰

`app/views` 디렉토리의 뷰 파일들을 `resources/views` 디렉토리로 옮깁니다

### 블레이드 태그 변경

보안을 기본적으로 강화하기 위해서 라라벨5에서는 `{{ }}`와 `{{{ }}}` 구문에서 모든 출력을 escape 합니다. 새로운 `{!! !!}` 구문이 escape 되지 않은 출력을 위해서 사용되어 집니다. 어플리케이션을 업데이트 할 때 가장 안전한 선택은 이전처럼 출력하기 위해서 기존 구문들을 `{!! !!}` 표기 방식으로 사용하는 것입니다.

하지만 여러분이 **반드시** 이전 버전의 블레이드 문법을 사용해야 한다면 `AppServiceProvider@register`의 마지막에 다음 라인들을 추가하면 됩니다.

```php
\Blade::setRawTags('{{', '}}');
\Blade::setContentTags('{{{', '}}}');
\Blade::setEscapedContentTags('{{{', '}}}');
```

이렇게 하는 것이 편리한것만은 아니며 XSS 공격에 대해서 어플리케이션이 취약해질 수도 있습니다.
또한, 코멘트와 `{{--`는 더 이상 동작하지 않습니다.

### 다국어 파일

`app/lang` 디렉토리의 언어 파일들을 `resources/lang` 디렉토리로 옮깁니다

###  public 디렉토리

`4.2` 어플리케이션의 `public` 디렉토리에 들어 있던 asset 파일들을 새로운 어플리케이션의 `public` 디렉토리로 복사해 넣습니다. `index.php`의 `5.0` 버전을 유지해야 합니다.

### 테스트파일

`app/tests` 폴더의 테스트 파일들을 `tests` 디렉토리로 옮깁니다.

### 기타 파일들

프로젝트의 다른 파일들을 복사합니다. 예를 들어, `.scrutinizer.yml`, `bower.json` 그리고 다를 비슷한 도구 관련 설정 파일 등입니다.

Sass 나 Less, CoffeeScript 파일들을 원하는 위치로 이동합시다. `resources/assets` 디렉토리가 기본 위치로 적당할 것입니다.

### Form & HTML 헬퍼

만약 Form 이나 HTML 헬퍼를 사용중이었다면 `class 'Form' not found` 또는 `class 'Html' not found`와 같은 에러 메세지를 확인할 수 있을 것입니다. Form과 HTML 헬퍼는 라라벨 5.0에서 더 이상 제공하지 않습니다. 하지만 별도의 [Laravel Collective](http://laravelcollective.com/docs/5.0/html) 커뮤니티가 주관하는 대체 시스템이 있습니다.

예를 들자면 `composer.json`의 `require` 영역에 `"laravelcollective/html": "~5.0"`를 추가할 수도 있습니다 .

또한, Form과 HTML 파사드와 서비스 프로바이더가 필요할 것입니다. `config/app.php`를 수정하여 'providers' 배열에 다음 라인을 추가하십시오.

	'Collective\Html\HtmlServiceProvider',

다음으로 'aliases' 배열에 다음라인들을 추가합니다.

	'Form' => 'Collective\Html\FormFacade',
	'Html' => 'Collective\Html\HtmlFacade',

### 캐시 매니저

어플리케이션에서 라라벨 캐시를 파사드가 아닌 `Illuminate\Cache\CacheManager` 형태로 주입했었다면 이제는 그 대신에 `Illuminate\Contracts\Cache\Repository`를 주입하도록 합니다.

### 페이지네이션

`$paginator->links()`로 호출되던 부분을 `$paginator->render()`으로 교체하십시오.

`$paginator->getFrom()`와 `$paginator->getTo()`로 호출되던 부분을 각각 `$paginator->firstItem()`와 `$paginator->lastItem()`로 교체합니다.

`$paginator->getPerPage()`, `$paginator->getCurrentPage()`, `$paginator->getLastPage()` 그리고 `$paginator->getTotal()`메소드 앞에 접두어로 붙던 “get”을 제거 하십시오(예. `$paginator->perPage()`)

### Beanstalk 큐

라라벨 5.0에서는 `"pda/pheanstalk": "~2.1"` 대신에 `"pda/pheanstalk": "~3.0”`을 필요로 합니다.

### Remote

Remote 컴포넌트는 더 이상 제공하지 않습니다.

### Workbench

Workbench 컴포넌트는 더 이상 제공하지 않습니다.

<!--chak-comment-업그레이드-가이드(Upgrade-Guide)-4-2에서-5-0으로-업그레이드-->

<a name="upgrade-4.2"></a>
## 4.1에서 4.2로 업그레이드 하기

### PHP 5.4 이상

라라벨 4.2는 PHP 5.4.0 이상을 필요로 합니다.

### 기본 암호화 방식

`app/config/app.php` 설정 파일에 새로운 `chpher` 옵션을 추가하십시오. 이 옵션값은 `MCRYPT_RIJNDAEL_256`가 되어야 합니다.

    'cipher' => MCRYPT_RIJNDAEL_256

이 설정은 라라벨의 암호화 기능에 의해 사용되는 기본 암호화를 제어하는​​ 데 사용됩니다.

> **참고:** 라라벨 4.2에서는 기본 암호화 옵션은 가장 안전한 암호화 방식으로 알려진 `MCRYPT_RIJNDAEL_128` (AES)입니다.  암호화 방식을 `MCRYPT_RIJNDAEL_256`으로 되돌릴 필요가 있는 경우는 라라벨 4.1 이전 버전에서 암호화 된 쿠키 / 값을 해독해야 하는 경우입니다.

### Soft 삭제 모델은 Traits를 사용합니다.

만약 모델의 soft 삭제를 사용하였었따면 `softDeletes` 속성은 제거되었습니다. 이제 다음처럼 `SoftDeletingTrait`을 사용해야 합니다.

	use Illuminate\Database\Eloquent\SoftDeletingTrait;

	class User extends Eloquent {
		use SoftDeletingTrait;
	}

또한, 수동으로 `deleted_at` 컬럼을  `dates` 속성에 추가해주어야 합니다.

	class User extends Eloquent {
		use SoftDeletingTrait;

		protected $dates = ['deleted_at'];
	}

모든 soft 삭제 작업을 위한 API는 동일합니다.

> **주의:** `SoftDeletingTrait`는 기본 모델에 사용해서는 안됩니다. 실제 모델 클래스에서만 사용하도록 합니다.

### 뷰 / 페이지네이션 Environment 이름 변경

코드상에서 `Illuminate\View\Environment` 클래스 또는 `Illuminate\Pagination\Environment` 클래스를 참조하도록 되어 있었다면 그 대신에 `Illuminate\View\Factory`와 `Illuminate\Pagination\Factory`를 참조하도록 변경합니다. 이 두 개의 클래스는 기능을 보다 잘 나타내기 위해서 이름이 변경되었습니다.

### 페이지네이션 프리젠터에 파라미터 추가

`Illuminate\Pagination\Presenter`클래스를 확장하는 경우 `getPageLinkWrapper` 추상 메소드에 `rel` 인자가 추가되도록 변경되었습니다.

    abstract public function getPageLinkWrapper($url, $page, $rel = null);

### Iron.Io 큐 암호화

Iron.io 큐 드라이버를 사용하는 경우 큐 설정 파일에서 새롭게 `encrypt` 옵션을 추가합니다.

	'encrypt' => true
<!--chak-comment-업그레이드-가이드(Upgrade-Guide)-4-1에서-4-2로-업그레이드-하기-->

<a name="upgrade-4.1.29"></a>
## 4.1.X 이하에서 4.1.29로 업그레이드 하기

라라벨 4.1.29에서는 모든 데이터베이스 드라이버에 대한 컬럼 인용 부분이 향상되었습니다. 모델에서 `fillable` 속성을 사용하지 않은 경우 여러 가지 컬럼의 대량 할당에 관한(msass assignment) 취약점으로부터 어플리케이션을 보호할 수 있습니다. 모델에서 `fillable` 속성을 사용하여 데이터를 할당하는 경우 어플리케이션이 보다 안전해 집니다. 그러나 여러분이 `guarded` 속성을 사용하고 있고 사용자로부터 전달된 배열을 “update” 나 “save” 유형의 기능에 전달하는 경우라면 어플리케이션이 mass assignment 위험에 노출되어 있으므로 즉시 4.1.29로 업그레이드 해야 합니다.

라라벨4.1.29로 업그레이드 하기 위해서는 `composer update`를 실행하면 됩니다. 이 업데이트에서는 소스 수정이 필요한 사항들은 포함되어 있지 않습니다.

<!--chak-comment-업그레이드-가이드(Upgrade-Guide)-4-1-X-이하에서-4-1-29로-업그레이드-하기-->

<a name="upgrade-4.1.26"></a>
## 4.1.25 이하에서 4.1.26으로 업그레이드 하기

라라벨 4.1.26에서는 “remember me” 쿠키와 관련된 보안이 강화되었습니다. 이 업데이트 이전에는 remember 쿠키가 악의적인 사용자에 의해서 탈취되어 사용자 계정의 암호를 재설정하거나, 로그아웃 해도 장기간 사용이 가능한 상태로 남아 있었습니다.

이번 변경사항에서는 `users` 테이블(또는 사용자 관리랄 위한 테이블)에 새로운 `remember_token` 컬럼을 추가해야 합니다. 이 변경으로 인해서 사용자가 어플리케이션에 로그인 할 때마다 새로운 토큰이 할당됩니다. 이 토큰은 사용자가 어플리케이션에서 로그아웃 할때마다 다시 생성됩니다. 이 구현은 만약 “remember me” 쿠키가 탈취 당하더라도 어플리케이션에서 로그아웃 하면 해당 쿠키는 사용할 수 없게 된다는 것을 의미합니다.

### 업그레이드 방법

먼저 `users` 테이블에 null을 허용하는 VARCHAR(100) 또는 TEXT 형식의 `remember_token` 컬럼을 추가합니다.

다음으로 Eloquent 인증 드라이버를 사용하고 있는 경우에, User 클래스에 다음 세 메소드를 추가하십시오.

	public function getRememberToken()
	{
		return $this->remember_token;
	}

	public function setRememberToken($value)
	{
		$this->remember_token = $value;
	}

	public function getRememberTokenName()
	{
		return 'remember_token';
	}

> **주의:** 이 변경으로 인해 현재 사용중인 "Remember me" 세션이 무효화되기 때문에 모든 사용자는 어플리케이션에 접속할 때 강제로 다시 인증을 해야되게 됩니다.

### 패키지 개발자에게.

`Illuminate\Auth\UserProviderInterface` 인터페이스에 새로운 2개의 메소드가 추가되었습니다. 기본 드라이버에 간단한 구현예제를 확인하실 수 있습니다.

	public function retrieveByToken($identifier, $token);

	public function updateRememberToken(UserInterface $user, $token);

`Illuminate\Auth\UserInterface`에서도 “업데이트 방법”에서 설명하고 있는 새로운 메소드 3개가 추가되었습니다.

<!--chak-comment-업그레이드-가이드(Upgrade-Guide)-4-1-25-이하에서-4-1-26으로-업그레이드-하기-->

<a name="upgrade-4.1"></a>
## 4.0에서 4.1로 업그레이드

### 컴포저 의존성 업그레이드 하기

라라벨 어플리케이션을 4.1로 업그레이드 하기 위해서는 `composer.json` 파일에서 `laravel/framework`의 버전을 `4.1.*`으로 변경해야 합니다.

### 파일 교체

`public/index.php` 파일을 [저장소의 새로운 버전](https://github.com/laravel/laravel/blob/v4.1.0/public/index.php)으로 교체 합니다.

`artisan` 파일을 [저장소의 새로운 버전](https://github.com/laravel/laravel/blob/v4.1.0/artisan)으로 교체 합니다.

### 설정 파일과 옵션 추가

`app/config/app.php` 설정 파일에서 `aliases`와 `providers` 배열을 업데이트 합니다. 배열에서 업데이트 해야될 값은 [이 파일](https://github.com/laravel/laravel/blob/v4.1.0/app/config/app.php)에서 확인할 수 있습니다. 사용자가 별도로 지정한 패키지 서비스 프로바이더와 별칭을 따로 추가하는 것을 잊지 마십시오.

새로운 `app/config/remote.php` 설정 파일을 [저장소](https://github.com/laravel/laravel/blob/v4.1.0/app/config/remote.php)로부터 추가합니다.

새로운 `app/config/session.php` 파일에서 새로운 `expire_on_close` 설정 옵션을 추가합니다. 기본값은 `false`입니다.

`app/config/queue.php` 파일에서 새로운 `failed` 설정 섹션을 추가합니다. 이 섹션의 기본값은 다음과 같습니다.

	'failed' => [
		'database' => 'mysql', 'table' => 'failed_jobs',
	],

**(선택사항)** `app/config/view.php` 파일에서 `pagination` 설정 옵션을 `pagination::slider-3`으로 업데이트 합니다.

### 컨트롤러 업데이트

`app/controllers/BaseController.php` 파일의 최 상단 부분에 `use`가 사용되었다면 `use Illuminate\Routing\Controllers\Controller;`를 `use Illuminate\Routing\Controller;`으로 변경합니다.

### 패스워드 리마인더(알리미) 업데이트

패스워드 리마인더는 유연성을 높이기 위해서 많은 변경이 있었습니다. `php artisan auth:reminders-controller` 아티즌 명령어를 실행하여 작성되는 새로운 컨트롤러를 살펴보십시오. 혹은 [변경 관련 문서](/docs/security#password-reminders-and-reset)를 확인하고 그에 따라 어플리케이션을 업데이트 하십시오.

`app/lang/en/reminders.php` 언어 파일을 [새로운 파일](https://github.com/laravel/laravel/blob/v4.1.0/app/lang/en/reminders.php)에 맞게 변경하십시오.

### 구동환경 감지 업데이트

보안의 이유로 어플리케이션의 구동 환경을 감지 하기 위해서 URL 도메인은 더 이상 사용되지 않습니다. 이 값은 손쉽게 요청시에 변경이 가능하여 공력을 가능하게 합니다. 머신의 호스트 네임 (맥, 리눅스, 윈도우에서 `hostname` 명령어를 사용하십시오)을 사용하도록 구동 환경 감지 로직을 변경해야 합니다.

### 로그 파일 간략화.

라라벨은 이제 `app/storage/logs/laravel.log` 라는 하나의 파일만을 생성합니다. 그러나 이 동작은 `app/start/global.php` 파일에서 설정이 가능합니다.

### Trailing Slash 리다이렉션 제거

`bootstrap/start.php` 파일안에서 `$app->redirectIfTrailingSlash()` 호출을 제거 하십시오. 이 기능은 프레임워크에 포함되어 있는 `.htaccess` 파일에서 처리되어 더 이상 메소드가 필요하지 않습니다.

다음으로 `public/.htaccess` 파일을 [새버전의 파일](https://github.com/laravel/laravel/blob/v4.1.0/public/.htaccess)으로 교체합니다.

### 현재 라우트 엑세스하기

`Route::getCurrentRoute()` 대신에 `Route::current()`를 통해서 현재 라우트에 엑세스 할 수 있습니다.
### 컴포저 업데이트

변경사항 적용을 완료하면 `composer update`를 실행하여 어플리케이션 코어 파일을 업데이트 할 수 있습니다. 로딩과 관련된 에러가 발생한다면 다음처럼 `--no-scripts` 옵션과 함께 `update` 명령어를 실행하십시오 `composer update --no-scripts`.

### 와일드카드 이벤트 리스너

와일드 카드 이벤트 리스너는 더 이상 핸들러 함수의 인자에 이벤트를 전달하지 않습니다.  발행된 이벤트를 확인해야 할 필요가 다면, `Event::firing()`를 사용하십시오.

<!--chak-comment-업그레이드-가이드(Upgrade-Guide)-4-0에서-4-1로-업그레이드-->
