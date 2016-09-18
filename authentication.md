# 인증 (Authentication)

- [소개](#introduction)
- [사용자 인증](#authenticating-users)
- [인증된 사용자 검색](#retrieving-the-authenticated-user)
- [라우트 보호](#protecting-routes)
- [HTTP 기본인증](#http-basic-authentication)
- [패스워드 알림 & 재설정](#password-reminders-and-reset)
- [소셜 인증](#social-authentication)

<a name="introduction"></a>
## 소개

라라벨은 인증 구현을 매우 쉽게 해줍니다. 기본적으로 별도의 설정 없이도 대부분이 준비되어 있습니다. 인증에 대한 설정 파일은 `config/auth.php`으로 인증 서비스의 동작을 조정할 수 있는 옵션들이 자세한 설명과 함께 제공됩니다.  

기본적으로 라라벨은 `app` 디렉토리에 `App\User` 모델을 포함하고 있습니다. 이 모델은 기본적인 Eloquent 인증 드라이버와 함께 사용하게 됩니다. 

유념할 것은 이 모델에 대한 데이터베이스 스키마를 작성할 때, 패스워드 컬럼이 최소 60자가 되어야 한다는 것입니다. 또한, 사용하기 전에 빈 문자열을 허용하는(nullable) 100자리 문자열의 `remember_token` 컬럼이 `users` (또는 같은) 테이블에 포함되어 있는지 확인하십시오. 이 컬럼은 어플리케이션에서 관리하는 “remember me“ 세션의 토큰을 저장하는 데 사용됩니다. 마이그레이션에서 `$table->rememberToken();`을 사용하면 추가할 수 있습니다. 물론 라라벨 5는 이러한 컬럼에 대한 마이그레이션이 별도의 설정 없이도 구성되어 있습니다!

만약 어플리케이션이 Eloquent를 사용하지 않는다면 라라벨 쿼리 빌더를 사용하는 `database` 인증 드라이버를 사용할 수도 있습니다.

<!--chak-comment-인증-(Authentication)-소개-->
<a name="authenticating-users"></a>
## 사용자 인증

라라벨은 별도의 설정 없이도 컨트롤러와 연동된 두 가지 인증을 제공하고 있습니다. `AuthController`는 새로운 사용자의 등록과 로그인을 처리하고 `PasswordController`는 암호 분실 시에 사용자 암호 재설정을 처리합니다. 

각각의 컨트롤러는 필요한 메소드를 포함하기 위해 trait를 사용합니다. 많은 어플리케이션에서 여러분은 이 컨트롤러들을 한번에 수정할 필요가 없을 것입니다. 이 컨트롤러들이 렌더링하는 뷰파일들은 `resources/views/auth` 디렉토리에 있습니다. 원한다면 자유롭게 이 뷰 파일들을 수정할 수 있습니다. 

### 사용자 등록

새로운 사용자를 등록하는 폼의 필수 입력 항목들을 변경하고자 한다면, `App\Services\Registrar` 클래스를 수정하면 됩니다. 이 클래스는 어플리케이션에서 새로운 사용자를 검증하고 생성하는 역할을 합니다.

`Registrar` 클래스의 `validator` 메소드는 어플리케이션의 새로운 사용자에 대한 폼 검증 규칙을 포함하고 있으며, `create` 메소드는 데이터베이스에 새로운 `User` 레코드를 생성하는 역할을 합니다. 여러분은 원하는대로 이 메소드들을 수정해도 됩니다. `AuthController`의 `AuthenticatesAndRegistersUsers` trait에 포함된 메소드에서 `Registrar`를 호출합니다. 

#### 수동으로 인증

만약 제공된 `AuthController` 구현 클래스를 사용하지 않는다면, 직접 라라벨의 인증 클래스를 사용하여 사용자 인증을 관리할 필요가 있습니다. 걱정 마십시오. 아주 쉽습니다. 먼저 `attempt` 메소드를 살펴봅시다:

	<?php namespace App\Http\Controllers;

	use Auth;
	use Illuminate\Routing\Controller;

	class AuthController extends Controller {

		/**
		 * Handle an authentication attempt.
		 *
		 * @return Response
		 */
		public function authenticate()
		{
			if (Auth::attempt(['email' => $email, 'password' => $password]))
			{
				return redirect()->intended('dashboard');
			}
		}

	}

`attempt` 메소드는 키 / 값의 쌍으로 이루어진 배열을 첫 번째 인자로 전달받습니다. `password` 값은 [해시처리](/docs/5.0/hashing)될 것입니다. 배열의 다른 값들은 데이터베이스 테이블에서 사용자를 찾는데 사용될 것입니다. 따라서 위의 예제에서는, `email` 컬럼을 통해서 사용자를 찾게 됩니다. 사용자를 찾았다면, 해시처리되어 데이터베이스에 저장된 패스워드와 메소드에 전달받은 배열의 해시처리된 `password` 값을 비교할 것입니다. 두 개의 해시처리된 패스워드가 일치한다면 해당 사용자의 새로운 인증 세션이 시작됩니다. 

`attempt` 메소드는 인증이 성공하면 `true`를 반환합니다. 실패 시 `false`를 반환합니다.

> **참고:** 이 예제에서 `email`은 필수 옵션이 아니며 단지 예제로 사용되었습니다. 여러분은 데이터베이스에서 "username"에 해당하는 어떠한 컬럼을 사용할 수 있습니다. 

`intended` 리다이렉트 함수는 사용자가 인증 필터에 잡히기 전에 원래 엑세스 하려고 시도했었던 URL로 사용자를 리다이렉트 합니다. 해당 리다이렉트가 불가능한 경우 대체 URI를 메소드에 지정할 수 있습니다. 

#### 조건에 따른 사용자 인증

인증 쿼리에 추가적인 조건을 더할 수도 있습니다:

    if (Auth::attempt(['email' => $email, 'password' => $password, 'active' => 1]))
    {
        // The user is active, not suspended, and exists.
    }

#### 사용자가 인증되었는지 확인하기

사용자가 어플리케이션에 이미 로그인되었는지 확인하기 위해서는 `check` 메소드를 사용하면 됩니다:

	if (Auth::check())
	{
		// The user is logged in...
	}

#### 사용자를 인증하고 "기억하기"

여러분의 어플리케이션에 “기억하기” 기능을 제공하고자 한다면, `attempt` 메소드의 두 번째 인자로 사용자의 인증을 무기한 계속 유지할지, 아니면 수동으로 로그아웃 할 때까지 유지할지 결정하는 boolean 값을 전달하면 됩니다. 이를 위해서는 `users` 테이블은 “기억하기” 토큰을 저장하는 데 사용되는 `remember_token` 컬럼을 가지고 있어야만 합니다. 

	if (Auth::attempt(['email' => $email, 'password' => $password], $remember))
	{
		// The user is being remembered...
	}

만약 여러분이 사용자들을 "기억"하고 있다면, `viaRemember` 메소드를 사용하여 해당 사용자가 "remember me(기억하기)" 쿠키를 사용하여 인증이 되었는지 확인할 수 있습니다:

	if (Auth::viaRemember())
	{
		//
	}

#### ID를 통해서 인증하기

사용자 ID를 통하여 사용자를 어플리케이션에 로그인 시키려면, `loginUsingId` 메소드를 사용하면 됩니다. 

	Auth::loginUsingId(1);

#### 로그인 없이 사용자 인증정보 검증하기

`validate` 메소드는 실제로 사용자를 어플리케이션에 로그인 시키지 않고 사용자의 인증 정보를 검증할 수 있도록 해줍니다:

	if (Auth::validate($credentials))
	{
		//
	}

#### 단일 요청(request)을 위한 사용자 로그인하기

어플리케이션의 단일 요청(request)에 대해서만 로그인 할 수 있도록 `once` 메소드를 사용할 수 있습니다. 세션이나 쿠키는 사용되지 않을 것입니다:

	if (Auth::once($credentials))
	{
		//
	}

#### 수동으로 사용자 로그인하기

이미 존재하는 사용자 인스턴스를 기준으로 로그인 해야 될 때에는 사용자 인스턴스와 함께 `login` 메소드를 호출하면 됩니다:

	Auth::login($user);

이것은 사용자 인증정보를 사용하여 로그인하는 `attempt` 메소드와 동일합니다.

#### 어플리케이션에서 사용자 로그아웃

	Auth::logout();

여러분이 라라벨에 내장된 라라벨의 인증 컨트롤러를 사용하고 있다면, 사용자를 어플리케이션에서 로그아웃 시키는 메소드가 별도의 설정 없이도 기본으로 제공됩니다.

#### 인증 이벤트 

`attempt` 메소드가 호출될 때에는 `auth.attempt` [이벤트](/docs/5.0/events)가 발생합니다. 사용자 인증이 성공적이고 사용자가 로그인되었다면, 마찬가지로 `auth.login` 이벤트가 발생합니다. 

<!--chak-comment-인증-(Authentication)-사용자-인증-->
<a name="retrieving-the-authenticated-user"></a>
## 인증된 사용자 조회하기

사용자가 인증이 되고 나면, 사용자의 인스턴스를 얻는 방법에는 여러 가지가 있습니다.

첫 번째로, `Auth` 파사드를 사용하여 사용자를 액세스 할 수 있습니다.

	<?php namespace App\Http\Controllers;

    use Auth;
	use Illuminate\Routing\Controller;

	class ProfileController extends Controller {

		/**
		 * Update the user's profile.
		 *
		 * @return Response
		 */
		public function updateProfile()
		{
			if (Auth::user())
			{
				// Auth::user() returns an instance of the authenticated user...
			}
		}

	}

두 번째로는, `Illuminate\Http\Request` 인스턴스를 사용하여 인증된 사용자를 액세스 할 수 있습니다:

	<?php namespace App\Http\Controllers;

	use Illuminate\Http\Request;
	use Illuminate\Routing\Controller;

	class ProfileController extends Controller {

		/**
		 * Update the user's profile.
		 *
		 * @return Response
		 */
		public function updateProfile(Request $request)
		{
			if ($request->user())
			{
				// $request->user() returns an instance of the authenticated user...
			}
		}

	}

세 번째로는, `Illuminate\Contracts\Auth\Authenticatable` contract를 타입힌트로 지정하는 것입니다.  이 타입힌트는 [서비스 컨테이너](/docs/5.0/container)에 의해서 의존성이 해결되는 컨트롤러의 생성자, 컨트롤러 메소드 또는 다른 어떤 클래스의 생성자에서 추가될 수 있습니다:

	<?php namespace App\Http\Controllers;

	use Illuminate\Routing\Controller;
	use Illuminate\Contracts\Auth\Authenticatable;

	class ProfileController extends Controller {

		/**
		 * Update the user's profile.
		 *
		 * @return Response
		 */
		public function updateProfile(Authenticatable $user)
		{
			// $user is an instance of the authenticated user...
		}

	}
<!--chak-comment-인증-(Authentication)-인증된-사용자-조회하기-->
<a name="protecting-routes"></a>
## 라우트 제한하기

[라우트 미들웨어](/docs/5.0/middleware)는 해당 라우트에 인증된 사용자만 액세스가 가능하도록 할 수 있습니다. 라라벨은 기본적으로 `app\Http\Middleware\Authenticate.php`로 정의되어 있는 `auth` 미들웨어를 제공합니다. 여러분은 필요한 라우트를 추가하기만 하면 됩니다. 

	// With A Route Closure...

	Route::get('profile', ['middleware' => 'auth', function()
	{
		// Only authenticated users may enter...
	}]);

	// With A Controller...

	Route::get('profile', ['middleware' => 'auth', 'uses' => 'ProfileController@show']);

<!--chak-comment-인증-(Authentication)-라우트-제한하기-->
<a name="http-basic-authentication"></a>
## HTTP 기본 인증

HTTP 기본 인증은 어플리케이션에 별도의 “login” 페이지 설정 없이도 사용자 인증을 할 수 있는 손쉬운 방법을 제공합니다. 이를 위해서는 `auth.basic` 미들웨어를 라우트에 추가하면 됩니다:

#### HTTP 기본 인증을 사용해 라우트 제한하기

	Route::get('profile', ['middleware' => 'auth.basic', function()
	{
		// Only authenticated users may enter...
	}]);

기본적으로 `auth.basic` 미들웨어는 사용자 레코드의 `email` 컬럼을 "username"으로 사용합니다.

#### 상태를 유지하지 않는 HTTP 기본 필터 설정하기

여러분은 또한, API 인증에 유용한, 세션의 사용자 식별 쿠키 없는 HTTP 기본 인증을 사용할 수 있습니다. 이렇게 하기 위해서는 `onceBasic` 메소드를 호출하는 [미들웨어를 정의](/docs/5.0/middleware)하면 됩니다. 

	public function handle($request, Closure $next)
	{
		return Auth::onceBasic() ?: $next($request);
	}

PHP FastCGI를 사용하는 경우 HTTP 기본 인증이 제대로 작동하지 않을 것입니다. 다음 행을 `.htaccess` 파일에 추가하십시오.

	RewriteCond %{HTTP:Authorization} ^(.+)$
	RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

<!--chak-comment-인증-(Authentication)-HTTP-기본-인증-->
<a name="password-reminders-and-reset"></a>
## 패스워드 알림 & 재설정

### 모델 & 테이블

대부분의 웹 어플리케이션은 사용자가 잊어버린 패스워드를 재설정하는 방법을 제공합니다. 라라벨은 각각의 어플리케이션에서 직접 이 기능을 다시 구현하도록 하기 보다, 패스워드 알림을 보내고 패스워드를 재설정하도록 하는 편리한 메소드를 제공합니다. 

이를 이용하려면 `User` 모델이 `Illuminate\Contracts\Auth\CanResetPassword` contract을 구현하고 있는지 확인하십시오. 물론 프레임워크에 포함된 `User` 모델은 이미 이 인터페이스를 구현하고 있으며, 인터페이스 구현에 필요한 메소드를 포함한 `Illuminate\Auth\Passwords\CanResetPassword` trait을 사용하고 있습니다. 

#### 알림 테이블 마이그레이션 생성하기

다음으로 패스워드 재설정 토큰을 저장하기 위한 테이블을 생성해야 합니다. 이 테이블의 마이그레이션은 별다른 설정 없이도 라라벨에 기본적으로 포함하고 있으며 `database/migrations` 디렉토리에 있습니다. 따라서 다음처럼 마이그레이션을 실행하면 됩니다. 

	php artisan migrate

### 패스워드알림 컨트롤러

라라벨은 사용자 패스워드를 재설정하는 데 필요한 로직을 포함하는 `Auth\PasswordController`를 가지고 있습니다. 이 뷰 파일들은 `resources/views/auth` 디렉토리에 있습니다. 어플리케이션의 디자인에 맞게 자유롭게 변경해도 됩니다. 

사용자는 `PasswordController`의 `getReset` 메소드에 해당하는 링크가 포함된 이메일을 수신할 수 있습니다. 이 메소드는 패스워드를 재설정하는 폼을 표시하고, 사용자가 패스워드를 재설정할 수 있도록 합니다. 패스워드가 재설정되면 사용자는 자동으로 어플리케이션에 로그인 처리되고 `/home`으로 리다이렉트 됩니다. 여러분은 `PasswordController`의 `redirectTo` 속성을 정의하여 사용자의 패스워드가 재설정 된 이후에 리다이렉트할 경로를 변경할 수 있습니다:

	protected $redirectTo = '/dashboard';

> **참고:** 기본적으로 패스워드 재설정 토큰은 1시간 동안만 유효합니다. `config/auth.php` 파일의 `reminder.expire` 옵션에서 변경할 수 있습니다. 

<!--chak-comment-인증-(Authentication)-패스워드-알림---재설정-->
<a name="social-authentication"></a>

## 소셜 인증

기본적인 인증과 더불어 라라벨은 [Laravel Socialite](https://github.com/laravel/socialite)를 사용하여 간단하고 편리한 OAuth 인증을 제공합니다. **Socialite는 현재 페이스북, 트위터, 구글, Github와 Bitbucket 인증을 지원합니다** 

Socialite를 시작하기 위해서는 `composer.json` 파일에 다음 패키지를 추가하십시오:

	"laravel/socialite": "~2.0"

다음으로 `config/app.php` 설정 파일에 `Laravel\Socialite\SocialiteServiceProvider`를 등록하십시오. [파사드](/docs/5.0/facades) 또한 등록해야 합니다:

	'Socialize' => 'Laravel\Socialite\Facades\Socialite',

이제 어플리케이션에서 사용할 OAuth 서비스를 위한 인증 정보를 추가해야 합니다. 이 인증 정보들은 `config/services.php` 설정 파일에 지정되어야 하며, 어플리케이션이 필요로 하는 `facebook`, `twitter`, `google`, 또는 `github` 서비스에 맞게 키가 지정되어야 합니다. 예를 들어:

	'github' => [
		'client_id' => 'your-github-app-id',
		'client_secret' => 'your-github-app-secret',
		'redirect' => 'http://your-callback-url',
	],

이제 사용자 인증을 위한 준비가 되었습니다! 이제 두 가지의 라우트가 필요합니다: 하나는 OAuth를 사용하기 위한 리다이렉트와 다른 하나는 인증 후 결과를 콜백으로 받기 위한 라우트입니다. 다음은 `Socialize` 파사드를 사용하는 예제입니다: 

	public function redirectToProvider()
	{
		return Socialize::with('github')->redirect();
	}

	public function handleProviderCallback()
	{
		$user = Socialize::with('github')->user();

		// $user->token;
	}

`redirect` 메소드는 사용자를 OAuth 제공자로 보내는 일을 처리하고 `user` 메소드는 제공자로부터 전송받은 요청을 읽고 사용자 정보를 조회하는 일을 합니다. 사용자를 리다이렉트 하기 전에, “범위”를 지정할 수 있습니다:

	return Socialize::with('github')->scopes(['scope1', 'scope2'])->redirect();

사용자의 인스턴스를 획득하면, 사용자에 대한 보다 자세한 정보를 조회할 수 있습니다:

#### 사용자의 상세 정보 탐색하기

	$user = Socialize::with('github')->user();

	// OAuth Two Providers
	$token = $user->token;

	// OAuth One Providers
	$token = $user->token;
	$tokenSecret = $user->tokenSecret;

	// All Providers
	$user->getId();
	$user->getNickname();
	$user->getName();
	$user->getEmail();
	$user->getAvatar();

<!--chak-comment-인증-(Authentication)-소셜인증-->
