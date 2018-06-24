# Authentication
# 인증

- [Introduction 소개](#introduction)
- [Authenticating Users 사용자 인증](#authenticating-users)
- [Retrieving The Authenticated User 인증된 사용자 검색](#retrieving-the-authenticated-user)
- [Protecting Routes 라우트 보호](#protecting-routes)
- [HTTP Basic Authentication HTTP 기본인증](#http-basic-authentication)
- [Password Reminders & Reset 패스워드 알림 & 재설정](#password-reminders-and-reset)
- [Social Authentication 소셜 인증](#social-authentication)

<a name="introduction"></a>
## Introduction 소개

Laravel makes implementing authentication very simple. 라라벨은 인증 구현을 매우 쉽게 해줍니다. In fact, almost everything is configured for you out of the box. 기본적으로 별도의 설정 없이도 대부분이 준비되어 있습니다. The authentication configuration file is located at `config/auth.php`, which contains several well documented options for tweaking the behavior of the authentication services. 인증에 대한 설정 파일은 `config/auth.php` 으로 인증 서비스의 동작을 조정할 수 있는 옵션들이 자세한 설명과 함께 제공됩니다.  

By default, Laravel includes an `App\User` model in your `app` directory. 기본적으로 라라벨은 `app` 디렉토리에 `App\User` 모델을 포함하고 있습니다. This model may be used with the default Eloquent authentication driver. 이 모델은 기본적인 Eloquent 인증 드라이버와 함께 사용하게 됩니다. 

Remember: when building the database schema for this model, make the password column at least 60 characters. 유념할 것은 이 모델에 대한 데이터베이스 스키마를 작성 할 때, 패스워드 컬럼이 최소 60 자가 되어야 합니다. Also, before getting started, make sure that your `users` (or equivalent) table contains a nullable, string `remember_token` column of 100 characters.  또한 사용하기 전에 빈 문자열을 허용하는 (NULLABLE) 100자리 문자열의 `remember_token` 컬럼이 `users` (또는 동일한) 테이블에 포함되어 있는지 확인하십시오. This column will be used to store a token for "remember me" sessions being maintained by your application.  이 컬럼은 애플리케이션에서 관리하는 “remember me“ 세션의 토큰을 저장하는데 사용됩니다. This can be done by using `$table->rememberToken();` in a migration. 마이그레이션에서 `$table->rememberToken();`를 사용하면 추가 할 수 있습니다. Of course, Laravel 5 ships migrations for these columns out of the box! 물론 라라벨 5는 이러한 컬럼에 대한 마이그레이션이 별도의 설정 없이도 구성되어 있습니다!

If your application is not using Eloquent, you may use the `database` authentication driver which uses the Laravel query builder. 만약 애플리케이션이 Eloquent 를 사용하지 않는다면 라라벨 쿼리 빌더를 사용하는 `database` 인증 드라이버를 사용 할 수도 있습니다.

<a name="authenticating-users"></a>
## Authenticating Users
## 사용자 인증

Laravel ships with two authentication related controllers out of the box. 라라벨은 별도의 설정 없이도 컨트롤러와 연동된 두가지 인증을 제공하고 있습니다. The `AuthController` handles new user registration and "logging in", while the `PasswordController` contains the logic to help existing users reset their forgotten passwords. `AuthController` 는 새로운 사용자의 등록과 로그인을 처리하고 `PasswordController`는 암호 분실시에 사용자 암호 재설정을 처리합니다. 

Each of these controllers uses a trait to include their necessary methods. 각각의 컨트롤러는 필요한 메서드를 포함하기 위해 trait를 사용합니다. For many applications, you will not need to modify these controllers at all. 많은 애플리케이션에서 여러분은 이 컨트롤러들을 한번에 수정할 필요가 없을 것입니다. The views that these controllers render are located in the `resources/views/auth` directory. 이 컨트롤러들이 렌더링하는 뷰파일들은 `resources/views/auth` 디렉토리에 있습니다. You are free to customize these views however you wish. 원한다면 자유롭게 이 뷰 파일들을 수정할 수 있습니다. 

### The User Registrar
### 사용자 등록

To modify the form fields that are required when a new user registers with your application, you may modify the `App\Services\Registrar` class. 새로운 사용자를 등록하는 폼의 필수 입력 항목들을 변경 하고자 한다면, `App\Services\Registrar` 클래스를 수정하면 됩니다. This class is responsible for validating and creating new users of your application. 이 클래스는 애플리케이션에서 새로운 사용자를 검증하고 생성하는 역할을 합니다.

The `validator` method of the `Registrar` contains the validation rules for new users of the application, while the `create` method of the `Registrar` is responsible for creating new `User` records in your database. `Registrar` 클래스의 `validator` 메소드는 애플리케이션의 새로운 사용자에 대한 폼 검증 규칙을 포함하고 있으며, `create` 메소드는 데이터베이스에 새로운 `User` 레코드를 생성하는 역할을 수행합니다. You are free to modify each of these methods as you wish. 여러분은 원하는대로 이 메소드들을 수정해도 됩니다. The `Registrar` is called by the `AuthController` via the methods contained in the `AuthenticatesAndRegistersUsers` trait. `AuthController`의 `AuthenticatesAndRegistersUsers` trait에 포함된 메서드에서 `Registrar`를 호출합니다. 

#### Manual Authentication
#### 수동 인증

If you choose not to use the provided `AuthController` implementation, you will need to manage the authentication of your users using the Laravel authentication classes directly. 만약 제공된 `AuthController` 구현 클래스를 사용하지 않는다면, 직접 라라벨의 인증 클래스를 사용하여 사용자 인증을 관리할 필요가 있습니다. Don't worry, it's still a cinch! 걱정마십시오. 아주 쉽습니다. First, let's check out the `attempt` method: 먼저 `attempt` 메소드를 살펴봅시다. 

	<?php
	
	namespace App\Http\Controllers;

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

The `attempt` method accepts an array of key / value pairs as its first argument. `attempt` 메소드는 키 / 값의 쌍으로 이루어진 배열을 첫번째 인자로 전달 받습니다. The `password` value will be [hashed](/docs/version/hashing). `password` 값은 [해시처리](/docs/version/hashing)될 것입니다. The other values in the array will be used to find the user in your database table. 배열의 다른 값들은 데이터베이스 테이블에서 사용자를 찾는데 사용될것입니다. So, in the example above, the user will be retrieved by the value of the `email` column. 따라서 위의 예제에서는, `email` 컬럼을 통해서 사용자를 찾게됩니다. If the user is found, the hashed password stored in the database will be compared with the hashed `password` value passed to the method via the array. 사용자를 찾았다면, 해시처리되어 데이터베이스에 저장된 패스워드와 매소드에 전달받은 배열의 해시처리된 `password` 값을 비교할 것입니다. If the two hashed passwords match, a new authenticated session will be started for the user. 두개의 해시처리된 패스워드가 일치한다면 해당 사용자의 새로운 인증 세션이 시작됩니다. 

The `attempt` method will return `true` if authentication was successful. `attempt` 메소드는 인증이 성공하면 `true` 를 반환합니다.  Otherwise, `false` will be returned. 
실패시 `false` 를 반환합니다.

> **Note 참고:** In this example, `email` is not a required option, it is merely used as an example. 이 예제에서 `email` 은 필수 옵션이 아니며 단지 예제로 사용되었습니다. You should use whatever column name corresponds to a "username" in your database. 여러분은 데이터베이스에서 "username"에 해당하는 어떠한 컬럼을 사용할 수 있습니다. 

The `intended` redirect function will redirect the user to the URL they were attempting to access before being caught by the authentication filter. `intended` 리다이렉트 함수는 사용자가 인증 필터에 잡히기 전에 원래 엑세스 하려고 시도했었던 URL로 사용자를 리다이렉트 시킵니다. A fallback URI may be given to this method in case the intended destination is not available. 해당 리다이렉트가 불가능한 경우 대체 URI를 메소드에 지정할 수 있습니다. 

#### Authenticating A User With Conditions
#### 조건에 따른 사용자 인증

You also may add extra conditions to the authentication query:
인증 쿼리에 추가적인 조건을 더할 수도 있습니다:

    if (Auth::attempt(['email' => $email, 'password' => $password, 'active' => 1]))
    {
        // The user is active, not suspended, and exists.
    }

#### Determining If A User Is Authenticated
#### 사용자가 인증되었는지 확인하기

To determine if the user is already logged into your application, you may use the `check` method: 사용자가 애플리케이션에 이미 로그인되었는지 확인하기 위해서는 `check` 메소드를 사용하면 됩니다. 

	if (Auth::check())
	{
		// The user is logged in...
	}

#### Authenticating A User And "Remembering" Them
#### 사용자를 인증하고 "기억 하기"

If you would like to provide "remember me" functionality in your application, you may pass a boolean value as the second argument to the `attempt` method, which will keep the user authenticated indefinitely, or until they manually logout. 여러분의 애플리케이션에 “기억하기” 기능을 제공하고자 한다면, `attempt` 메소드의 두번째 인자로 사용자의 인증을 무기한 계속 유지할지, 아니면 수동으로 로그아웃 할때까지 유지할지 결정하는 boolean 값을 전달하면 됩니다. Of course, your `users` table must include the string `remember_token` column, which will be used to store the "remember me" token. 이를 위해서는 `users` 테이블은 “기억하기” 토근을 저장하는데 사용되는 `remember_token` 컬럼을 가지고 있어야만 합니다. 

	if (Auth::attempt(['email' => $email, 'password' => $password], $remember))
	{
		// The user is being remembered...
	}

If you are "remembering" users, you may use the `viaRemember` method to determine if the user was authenticated using the "remember me" cookie: 만약 여러분이 사용자들을 "기억" 하고 있다면, `viaRemember` 메소드를 사용하여 해당 사용자가 "기억 하기" 쿠키를 사용하여 인증이 되었는지 확인 할 수 있습니다:

	if (Auth::viaRemember())
	{
		//
	}

#### Authenticating Users By ID
#### ID 를 통해서 인증하기

To log a user into the application by their ID, use the `loginUsingId` method: 사용자 ID를 통하여 사용자를 애플리케이션에 로그인 시키려면, `loginUsingId` 메소드를 사용하면 됩니다. 

	Auth::loginUsingId(1);

#### Validating User Credentials Without Login
#### 로그인 없이 사용자 인증정보 검증하기

The `validate` method allows you to validate a user's credentials without actually logging them into the application: `validate` 메소드는 실제로 사용자를 애플리케이션에 로그인 시키지 않고 사용자의 인증 정보를 검증할 수 있도록 해줍니다:

	if (Auth::validate($credentials))
	{
		//
	}

#### Logging A User In For A Single Request
#### 단일 요청(request)을 위한 사용자 로그인하기

You may also use the `once` method to log a user into the application for a single request. 애플리케이션의 단일 요청(request)에 대해서만 로그인 할 수 있도록 `once` 메소드를 사용할 수 있습니다. No sessions or cookies will be utilized: 세션이나 쿠키는 사용되지 않을 것입니다. 

	if (Auth::once($credentials))
	{
		//
	}

#### Manually Logging In A User
#### 수동으로 사용자 로그인하기

If you need to log an existing user instance into your application, you may call the `login` method with the user instance: 이미 존재하는 사용자 인스턴스를 기준으로 로그인 해야될 경우에는 사용자 인스턴스와 함께 `login` 메소드를 호출하면 됩니다. 

	Auth::login($user);

This is equivalent to logging in a user via credentials using the `attempt` method. 이것은 사용자 인증정보를 사용하여 로그인 하는 `attempt` 메서드와 동일합니다.

#### Logging A User Out Of The Application
#### 애플리케이션에서 사용자 로그아웃

	Auth::logout();

Of course, if you are using the built-in Laravel authentication controllers, a controller method that handles logging users out of the application is provided out of the box. 여러분이 라라벨에 내장된 라라벨의 인증 컨트롤러를 사용하고 있다면, 사용자를 애플리케이션에서 로그아웃 시키하는 메서드가 별도의 설정 없이도 기본으로 제공 됩니다.

#### Authentication Events
#### 인증 이벤트 

When the `attempt` method is called, the `auth.attempt` [event](/docs/version/events) will be fired. `attempt` 메소드가 호출될 때에는 `auth.attempt` [이벤트](/docs/version/events)가 발생합니다. If the authentication attempt is successful and the user is logged in, the `auth.login` event will be fired as well. 사용자 인증이 성공적이고 사용자가 로그인되었다면, 마찬가지로 `auth.login` 이벤트가 발생합니다. 

<a name="retrieving-the-authenticated-user"></a>
## Retrieving The Authenticated User
## 인증된 사용자 조회하기

Once a user is authenticated, there are several ways to obtain an instance of the User. 사용자가 인증이 되고 나면, 사용자의 인스턴스를 얻는 방법에는 여러가지가 있습니다.

First, you may access the user from the `Auth` facade:
첫번째로, `Auth` 파사드를 사용하여 사용자를 액세스 할 수 있습니다.

	<?php
	
	namespace App\Http\Controllers;

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

Second, you may access the authenticated user via an `Illuminate\Http\Request` instance: 두번째로는, `Illuminate\Http\Request` 인스턴스를 사용하여 인증된 사용자를 액세스 할 수 있습니다:

	<?php
	
	namespace App\Http\Controllers;

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

Thirdly, you may type-hint the `Illuminate\Contracts\Auth\Authenticatable` contract. 세번째로는,  `Illuminate\Contracts\Auth\Authenticatable` contract를 타입힌트로 지정하는 것입니다. This type-hint may be added to a controller constructor, controller method, or any other constructor of a class resolved by the [service container](/docs/version/container): 이 타입힌트는 [서비스 컨테이너](/docs/version/container)에 의해서 의존성이 해결되는 컨트롤러의 생성자, 컨트롤러 메소드 또는 다른 어떤 클래스의 생성자에서 추가될 수 있습니다. 

	<?php
	
	namespace App\Http\Controllers;

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

<a name="protecting-routes"></a>
## Protecting Routes
## 라우트 제한하기

[Route middleware](/docs/version/middleware) can be used to allow only authenticated users to access a given route. [라우트 미들웨어](/docs/version/middleware)는 해당 라우트에 인증된 사용자들만 액세스가 가능하도록 할 수 있습니다. Laravel provides the `auth` middleware by default, and it is defined in `app\Http\Middleware\Authenticate.php`. 라라벨은 기본적으로 `app\Http\Middleware\Authenticate.php`로 정의되어 있는 `auth` 미들웨어를 제공합니다. All you need to do is attach it to a route definition: 여러분은 필요한 라우트를 추가하기만 하면 됩니다. 

	// With A Route Closure...

	Route::get('profile', ['middleware' => 'auth', function()
	{
		// Only authenticated users may enter...
	}]);

	// With A Controller...

	Route::get('profile', ['middleware' => 'auth', 'uses' => 'ProfileController@show']);

<a name="http-basic-authentication"></a>
## HTTP Basic Authentication
## HTTP 기본 인증

HTTP Basic Authentication provides a quick way to authenticate users of your application without setting up a dedicated "login" page. HTTP 기본 인증은 애플리케이션에 별도의 “login” 페이지 설정없이도 사용자 인증을 할 수 있는 손쉬운 방법을 제공합니다. To get started, attach the `auth.basic` middleware to your route: 이를 위해서는 `auth.basic` 미들웨어를 라우트에 추가하면 됩니다. 

#### Protecting A Route With HTTP Basic
#### HTTP 기본 인증을 사용해 라우트 제한하기

	Route::get('profile', ['middleware' => 'auth.basic', function()
	{
		// Only authenticated users may enter...
	}]);

By default, the `basic` middleware will use the `email` column on the user record as the "username". 기본적으로, `auth.basic` 미들웨어는 사용자 레코드의 `email` 컬럼을 "username"으로 사용합니다.

#### Setting Up A Stateless HTTP Basic Filter
#### 상태를 유지하지 않는 HTTP 기본 필터 설정하기

You may also use HTTP Basic Authentication without setting a user identifier cookie in the session, which is particularly useful for API authentication. To do so, [define a middleware](/docs/version/middleware) that calls the `onceBasic` method: 

여러분은 또한 API 인증에 유용한, 세션의 사용자 식별 쿠키 없는 HTTP 기본 인증을 사용할 수 있습니다. 이렇게 하기 위해서는 `onceBasic` 메소드를 호출하는 [미들웨어를 정의](/docs/version/middleware)하면 됩니다. 

	public function handle($request, Closure $next)
	{
		return Auth::onceBasic() ?: $next($request);
	}

If you are using PHP FastCGI, HTTP Basic authentication may not work correctly out of the box. The following lines should be added to your `.htaccess` file: PHP FastCGI를 사용하는 경우 HTTP 기본 인증이 제대로 작동하지 않을 것입니다. 다음 행을 `.htaccess` 파일에 추가하십시오.

	RewriteCond %{HTTP:Authorization} ^(.+)$
	RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

<a name="password-reminders-and-reset"></a>
## Password Reminders & Reset
## 패스워드 알림 & 재설정

### Model & Table
### 모델 & 테이블

Most web applications provide a way for users to reset their forgotten passwords. Rather than forcing you to re-implement this on each application, Laravel provides convenient methods for sending password reminders and performing password resets.
대부분의 웹 애플리케이션은 사용자가 잊어버린 패스워드를 재설정 하는 방법을 제공합니다. 라라벨은 각각의 애플리케이션에서 직접 이 기능을 다시 구현하는 하도록 하기 보다, 패스워드 알림을 보내고 패스워드를 재설정을 수행하는 편리한 메소드를 제공합니다. 

To get started, verify that your `User` model implements the `Illuminate\Contracts\Auth\CanResetPassword` contract. Of course, the `User` model included with the framework already implements this interface, and uses the `Illuminate\Auth\Passwords\CanResetPassword` trait to include the methods needed to implement the interface. 이를 이용하려면 `User` 모델이 `Illuminate\Contracts\Auth\CanResetPassword` contract을 구현하고 있는지 확인하십시오. 물론 프레임워크에 포함된 `User` 모델은 이미 이 인터페이스를 구현하고 있으며, 인터페이스 구현에 필요한 메소드를 포함한  `Illuminate\Auth\Passwords\CanResetPassword` trait을 사용하고 있습니다. 

#### Generating The Reminder Table Migration
#### 알림 테이블 마이그레이션 생성하기

Next, a table must be created to store the password reset tokens. 다음으로 패스워드 리셋 토큰을 저장하기 위한 테이블을 생성해야합니다. The migration for this table is included with Laravel out of the box, and resides in the `database/migrations` directory. 이 테이블의 마이그레이션은 별다른 설정 없이도 라라벨에 기본적으로 포함되어 있으며 `database/migrations` 디렉토리에 존재합니다. So all you need to do is migrate: 따라서 다음처럼 마이그레이션을 실행하면 됩니다. 

	php artisan migrate

### Password Reminder Controller
### 패스워드알림 컨트롤러

Laravel also includes an `Auth\PasswordController` that contains the logic necessary to reset user passwords. 라라벨은 사용자 패스워드를 재설정 하는데 필요한 로직을 포함하는 `Auth\PasswordController`을 가지고 있습니다. We've even provided views to get you started! 또한 뷰도 제공하고 있습니다. The views are located in the `resources/views/auth` directory. 이 뷰 파일들은 `resources/views/auth` 디렉토리에 있습니다. You are free to modify these views as you wish to suit your own application's design. 애플리케이션의 디자인에 맞게 자유롭게 변경해도 됩니다. 

Your user will receive an e-mail with a link that points to the `getReset` method of the `PasswordController`. 사용자는 `PasswordController`의 `getReset` 메소드에 해당하는 링크가 포함된 이메일을 수신할 수 있습니다. This method will render the password reset form and allow users to reset their passwords. 이 메소드는 패스워드를 재설정하는 폼을 표시하고, 사용자가 패스워드를 재설정 할 수 있도록 합니다. After the password is reset, the user will automatically be logged into the application and redirected to `/home`. 패스워드가 재설정되면 사용자는 자동으로 애플리케이션에 로그인 처리되고 `/home` 으로 리다이렉트 됩니다. You can customize the post-reset redirect location by defining a `redirectTo` property on the `PasswordController`: 여러분은 `PasswordController`의 `redirectTo` 속성을 정의하여 사용자의 패스워드가 재설정 된 이후에 리다이렉트할 경로를 변경할 수 있습니다. 

	protected $redirectTo = '/dashboard';

> **Note 참고:** By default, password reset tokens expire after one hour. 기본적으로 패스워드 재설정 토큰은 1시간 동안만 유효합니다. You may change this via the `reminder.expire` option of your `config/auth.php` file. `config/auth.php` 파일의 `reminder.expire` 옵션에서 변경할 수 있습니다. 

<a name="social-authentication"></a>
## Social Authentication
## 소셜 인증

In addition to typical, form based authentication, Laravel also provides a simple, convenient way to authenticate with OAuth providers using [Laravel Socialite](https://github.com/laravel/socialite). 기본적인 인증과 더불어 라라벨은 [Laravel Socialite](https://github.com/laravel/socialite)를 사용하여 간단하고 편리한 OAuth 인증을 제공합니다. **Socialite currently supports authentication with Facebook, Twitter, Google, GitHub and Bitbucket.** **Socialite 는 현재 페이스북, 트위터, 구글, Github 과 Bitbucket 인증을 지원합니다. 

To get started with Socialite, include the package in your `composer.json` file:
Socialite 를 시작하기 위해서는 `composer.json` 파일에 다음 패키지를 추가하십시오. 

	"laravel/socialite": "~2.0"

Next, register the `Laravel\Socialite\SocialiteServiceProvider` in your `config/app.php` configuration file. 다음으로 `config/app.php` 설정 파일에 `Laravel\Socialite\SocialiteServiceProvider`를 등록하십시오. You may also register a [facade](/docs/version/facades): [파사드](/docs/version/facades) 또한 등록해야 합니다. 

	'Socialize' => 'Laravel\Socialite\Facades\Socialite',

You will need to add credentials for the OAuth services your application utilizes. 이제 애플리케이션에서 사용할 OAuth 서비스를 위한 인증 정보를 추가할 필요가 있습니다. These credentials should be placed in your `config/services.php` configuration file, and should use the key `facebook`, `twitter`, `google`, or `github`, depending on the providers your application requires. 이 인증 정보들은 `config/services.php` 설정 파일에 지정되어야 하며, 애플리케이션이 필요로 하는 `facebook`, `twitter`, `google`, 또는 `github` 서비스에 맞게 키가 지정 되어야 합니다. For example: 예를 들어:

	'github' => [
		'client_id' => 'your-github-app-id',
		'client_secret' => 'your-github-app-secret',
		'redirect' => 'http://your-callback-url',
	],

Next, you are ready to authenticate users! 이제 사용자 인증을 위한 준비가 되었습니다. You will need two routes: 이제 두가지의 라우트가 필요로 합니다. one for redirecting the user to the OAuth provider, and another for receiving the callback from the provider after authentication. 하나는 OAuth를 사용하기 위한 리다이렉트와 다른 하나는 인증 후 결과를 콜백으로 받기 위한 라우트입니다. Here's an example using the `Socialize` facade: 다음은 `Socialize` 파사드를 사용하는 예제 입니다. 

	public function redirectToProvider()
	{
		return Socialize::with('github')->redirect();
	}

	public function handleProviderCallback()
	{
		$user = Socialize::with('github')->user();

		// $user->token;
	}

The `redirect` method takes care of sending the user to the OAuth provider, while the `user` method will read the incoming request and retrieve the user's information from the provider. `redirect` 메소드는 사용자를 OAuth 제공자로 보내는 일을 처리하고 `user` 메서드는 제공자로부터 전송 받은 요청을 읽고 사용자 정보를 조회하는 일을 합니다. Before redirecting the user, you may also set "scopes" on the request: 사용자를 리다이렉트 하기전에, “범위”를 지정 할 수 있습니다:

	return Socialize::with('github')->scopes(['scope1', 'scope2'])->redirect();

Once you have a user instance, you can grab a few more details about the user:
사용자의 인스턴스를 획득하면, 사용자에 대한 보다 자세한 정보를 조회 할 수 있습니다:

#### Retrieving User Details
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
