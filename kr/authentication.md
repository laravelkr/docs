# Authentication
# 인증

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [Database Considerations](#introduction-database-considerations)
    - [데이터베이스 유의사항](#introduction-database-considerations)
- [Authentication Quickstart](#authentication-quickstart)
- [빠르게 인증 살펴보기](#authentication-quickstart)
    - [Routing](#included-routing)
    - [Routing-라우팅](#included-routing)
    - [Views](#included-views)
    - [Views-뷰](#included-views)
    - [Authenticating](#included-authenticating)
    - [인증하기](#included-authenticating)
    - [Retrieving The Authenticated User](#retrieving-the-authenticated-user)
    - [인증된 사용자 획득하기](#retrieving-the-authenticated-user)
    - [Protecting Routes](#protecting-routes)
    - [라우트 제한하기](#protecting-routes)
    - [Login Throttling](#login-throttling)
    - [로그인 횟수 제한](#authentication-throttling)
- [Manually Authenticating Users](#authenticating-users)
- [수동으로 사용자 인증하기](#authenticating-users)
    - [Remembering Users](#remembering-users)
    - [사용자 기억하기](#remembering-users)
    - [Other Authentication Methods](#other-authentication-methods)
    - [그외 인증 메소드](#other-authentication-methods)
- [HTTP Basic Authentication](#http-basic-authentication)
- [HTTP 기본 인증](#http-basic-authentication)
    - [Stateless HTTP Basic Authentication](#stateless-http-basic-authentication)
    - [상태를 유지하지 않는 HTTP 기본 인증](#stateless-http-basic-authentication)
- [Logging Out](#logging-out)
- [로그아웃](#logging-out)
    - [Invalidating Sessions On Other Devices](#invalidating-sessions-on-other-devices)
    - [다른 디바이스의 세션 무효화](#invalidating-sessions-on-other-devices)
- [Social Authentication](https://github.com/laravel/socialite)
- [소셜 인증](https://github.com/laravel/socialite)
- [Adding Custom Guards](#adding-custom-guards)
- [사용자 정의 Guards 추가하기](#adding-custom-guards)
    - [Closure Request Guards](#closure-request-guards)
    - [클로저 형태의 Request Guards](#closure-request-guards)
- [Adding Custom User Providers](#adding-custom-user-providers)
- [사용자 정의 User 프로바이더 추가하기](#adding-custom-user-providers)
    - [The User Provider Contract](#the-user-provider-contract)
    - [사용자 프로바이더 Contract](#the-user-provider-contract)
    - [The Authenticatable Contract](#the-authenticatable-contract)
    - [인증가능 계약](#the-authenticatable-contract)
- [Events](#events)
- [이벤트](#events)


<a name="introduction"></a>
## Introduction
## 시작하기

> {tip} **Want to get started fast?** Install the `laravel/ui` Composer package and run `php artisan ui vue --auth` in a fresh Laravel application. After migrating your database, navigate your browser to `http://your-app.test/register` or any other URL that is assigned to your application. These commands will take care of scaffolding your entire authentication system!

> {tip} ** 빠르게 시작하길 원하십니까? ** 새로이 생성한 Laravel 애플리케이션에 `laravel/ui` 컴포저 패키지를 설치하고 `php artisan ui vue --auth` 명령어를 실행하십시오. 그 다음, 브라우저에서 `http://your-app.test/register` 또는 다른 URL로 이동하세요. 이 명령어는 전체적인 인증 시스템을 스캐폴딩합니다.

Laravel makes implementing authentication very simple. In fact, almost everything is configured for you out of the box. The authentication configuration file is located at `config/auth.php`, which contains several well documented options for tweaking the behavior of the authentication services.

라라벨은 인증기능 구현을 매우 쉽게 해줍니다. 기본적으로 별도의 설정 없이도 대부분 이미 준비되어 있습니다. 인증에 대한 설정 파일은 `config/auth.php`으로 인증 서비스의 동작을 제어할 수 있는 옵션들이 자세한 설명과 함께 제공됩니다.

At its core, Laravel's authentication facilities are made up of "guards" and "providers". Guards define how users are authenticated for each request. For example, Laravel ships with a `session` guard which maintains state using session storage and cookies.

내부 시스템에서, 라라벨의 인증 기능은 "guards" 와 "프로바이더"로 구성되어 있습니다. Guard는 사용자가 각각의 요청-request마다 어떻게 인증되는지 정의합니다. 예를 들어 라라벨은 세션 스토리지와 쿠키를 사용하여 상태를 유지하는 `session` guard와, 각 요청-request와 함께 전달되는 "API 토큰"을 사용하여 사용자를 인증하는 `token` guard를 제공합니다.

Providers define how users are retrieved from your persistent storage. Laravel ships with support for retrieving users using Eloquent and the database query builder. However, you are free to define additional providers as needed for your application.

프로바이더는 저장소에서 사용자를 어떻게 찾을 수 있는지 정의합니다. 라라벨은 Eloquent와 데이터베이스 쿼리 빌더를 사용하여 사용자를 찾을 수 있도록 지원합니다. 또한, 애플리케이션에서 필요로 하는 추가적인 프로바이더를 자유롭게 정의할 수 있습니다.

Don't worry if this all sounds confusing now! Many applications will never need to modify the default authentication configuration.

지금 이러한 이야기가 혼란스럽더라도 걱정하지 마십시오. 많은 애플리케이션은 기본 인증 설정을 변경할 필요가 전혀 없습니다.

<a name="introduction-database-considerations"></a>
### Database Considerations
### 데이터베이스 유의사항

By default, Laravel includes an `App\User` [Eloquent model](/docs/{{version}}/eloquent) in your `app` directory. This model may be used with the default Eloquent authentication driver. If your application is not using Eloquent, you may use the `database` authentication driver which uses the Laravel query builder.

기본적으로 라라벨은 `app` 디렉토리에 `App\User` [Eloquent 모델](/docs/{{version}}/eloquent) 모델을 포함하고 있습니다. 이 모델은 기본적인 Eloquent 인증 드라이버와 함께 사용하게 됩니다. 애플리케이션이 Eloquent를 사용하고 있지 않다면 라라벨 쿼리 빌더를 사용하는 `database` 인증 드라이버를 이용하면 됩니다.

When building the database schema for the `App\User` model, make sure the password column is at least 60 characters in length. Maintaining the default string column length of 255 characters would be a good choice.

`App\User` 모델을 위한 데이터베이스 스키마를 구성할 때, 패스워드 컬럼은 최소 60자가 되어야 하는 것을 명심하십시오. 기본값인 255을 유지하는 것도 좋은 선택입니다.

Also, you should verify that your `users` (or equivalent) table contains a nullable, string `remember_token` column of 100 characters. This column will be used to store a token for users that select the "remember me" option when logging into your application.

또한 `users` (또는 동일한) 테이블이 NULL을 허용하는 100자리 문자열의 `remember_token` 컬럼을 포함하고 있는지 확인하십시오. 이 컬럼은 애플리케이션에 로그인할 때 "remember me" 옵션을 선택한 사용자의 토큰을 저장하는 데 사용됩니다.

<a name="authentication-quickstart"></a>
## Authentication Quickstart
## 빠르게 인증 살펴보기

Laravel ships with several pre-built authentication controllers, which are located in the `App\Http\Controllers\Auth` namespace. The `RegisterController` handles new user registration, the `LoginController` handles authentication, the `ForgotPasswordController` handles e-mailing links for resetting passwords, and the `ResetPasswordController` contains the logic to reset passwords. Each of these controllers uses a trait to include their necessary methods. For many applications, you will not need to modify these controllers at all.

라라벨은 별도의 설정 없이도 `App\Http\Controllers\Auth` 네임스페이스에 위치한 몇가지 인증 컨트롤러를 제공하고 있습니다. `RegisterController`는 새로운 사용자의 등록을, `LoginController`는 인증을 처리하고, `ForgotPasswordController`는 암호 재설정을 위한 링크 생성을, `ResetPasswordController`는 암호를 재설정하는 로직이 들어 있습니다. 각각의 컨트롤러는 필요한 메서드를 포함하기 위해 트레이트-trait를 사용합니다. 대부분의 애플리케이션에서 여러분은 이 컨트롤러들을 수정할 필요가 없을 것입니다.

<a name="included-routing"></a>
### Routing
### Routing-라우팅

Laravel's `laravel/ui` package provides a quick way to scaffold all of the routes and views you need for authentication using a few simple commands:

라라벨의 `laravel/ui` 패키지는 다음의 간단한 명령어들을 통해서 인증에 필요한 모든 라우트와 뷰를 스캐폴딩 할 수 있는 손쉬운 방법을 제공합니다.

    composer require laravel/ui

    php artisan ui vue --auth

This command should be used on fresh applications and will install a layout view, registration and login views, as well as routes for all authentication end-points. A `HomeController` will also be generated to handle post-login requests to your application's dashboard.

이 명령어는 새로운 애플리케이션에서 사용되어야 하며, 레이아웃 뷰, 등록과 로그인 뷰, 모든 인증의 진입점을 위한 라우팅 기능을 설치할 것입니다. 또한 로그인 후 여러분의 대시보드 페이지를 요청할 수 있는 `HomeController`도 함께 설치됩니다.

> {tip} If your application doesn’t need registration, you may disable it by removing the newly created `RegisterController` and modifying your route declaration: `Auth::routes(['register' => false]);`.

> {tip} 여러분의 애플리케이션에서 회원가입을 필요로 하지 않는다면, 새롭게 생성되는 `RegisterController` 파일을 삭제하고 라우트 정의 파일에서 `Auth::routes(['register' => false]);`를 수정하면 됩니다.

<a name="included-views"></a>
### Views
### Views-뷰

As mentioned in the previous section, the `laravel/ui` package's `php artisan ui vue --auth` command will create all of the views you need for authentication and place them in the `resources/views/auth` directory.
앞서 언급하였듯이, `laravel/ui` 패키지의  `php artisan ui vue --auth` 명령어는 인증에서 필요로 하는 모든 뷰를 생성하여 `resources/views/auth` 디렉토리에 위치시킬 것입니다.

The `ui` command will also create a `resources/views/layouts` directory containing a base layout for your application. All of these views use the Bootstrap CSS framework, but you are free to customize them however you wish.

`ui` 명령어는 또한 애플리케이션의 베이스 레이아웃을 포함하는 `resources/views/layouts` 디렉토리를 생성할 것입니다. 이 모든 뷰 파일들은 Bootstrap CSS 프레임워크를 사용하지만, 여러분이 원하는 경우 자유롭게 변경할 수 있습니다.

<a name="included-authenticating"></a>
### Authenticating
### 인증하기

Now that you have routes and views setup for the included authentication controllers, you are ready to register and authenticate new users for your application! You may access your application in a browser since the authentication controllers already contain the logic (via their traits) to authenticate existing users and store new users in the database.

이제 인증 컨트롤러에 대한 라우트와 뷰가 설정되었으니, 애플리케이션에 새로운 사용자를 등록하거나 인증할 준비가 되었습니다! 기존 사용자를 인증하거나 새로운 사용자를 데이터베이스에 저장하는 (트레이트-trait를 통한) 로직은 이미 인증 컨트롤러에 포함되어 있으므로, 브라우저로 여러분의 애플리케이션에 접근하기만 하면 됩니다.

#### Path Customization
#### 리다이렉트 경로 수정하기

When a user is successfully authenticated, they will be redirected to the `/home` URI. You can customize the post-authentication redirect location by defining a `redirectTo` property on the `LoginController`, `RegisterController`, `ResetPasswordController`, and `VerificationController`:

사용자가 성공적으로 인증되면, `/home` URI로 리다이렉트 될 것입니다. 여러분은 `LoginController`, `RegisterController`, `ResetPasswordController` 그리고 `VerificationController` 의 `redirectTo` 속성을 정의하여, 인증 이후의 리다이렉트 경로를 커스터마이징 할 수 있습니다.

    protected $redirectTo = '/';

Next, you should modify the `RedirectIfAuthenticated` middleware's `handle` method to use your new URI when redirecting the user.

그리고, 사용자가 리다이렉팅 될 때 사용하는 새로운 URI를 사용하도록 `RedirectIfAuthenticated` 미들웨어의 `handle` 메소드를 수정해야 합니다.

If the redirect path needs custom generation logic you may define a `redirectTo` method instead of a `redirectTo` property:

리다이렉트 경로가 커스텀 생성 로직을 필요로 한다면 `redirectTo` 속성 대신 `redirectTo` 메소드를 정의할 수 있습니다.

    protected function redirectTo()
    {
        return '/path';
    }

> {tip} The `redirectTo` method will take precedence over the `redirectTo` property.

> {tip} `redirectTo` 메소드는 `redirectTo` 속성보다 우선합니다.

#### Username Customization
#### 어떤 사용자이름을 사용할지 결정하기

By default, Laravel uses the `email` field for authentication. If you would like to customize this, you may define a `username` method on your `LoginController`:

기본적으로 라라벨은 인증에 `email` 필드를 사용합니다. 이를 커스터마이징 하려면, `LoginController`의 `username` 메소드를 정의하면 됩니다.

    public function username()
    {
        return 'username';
    }

#### Guard Customization
#### Guard 커스터마이징

You may also customize the "guard" that is used to authenticate and register users. To get started, define a `guard` method on your `LoginController`, `RegisterController`, and `ResetPasswordController`. The method should return a guard instance:

또한 여러분은 사용자 인증과 등록에 사용되는 "guard"를 커스터마이징 할 수 있습니다. 시작하기에 앞서, `LoginController`, `RegisterController`, 그리고 `ResetPasswordController`에 `guard` 메소드를 정의하시기 바랍니다. 이 메소드는 guard 인스턴스를 리턴해야만 합니다.

    use Illuminate\Support\Facades\Auth;

    protected function guard()
    {
        return Auth::guard('guard-name');
    }

#### Validation / Storage Customization
#### Validation-유효성 검사 / 스토리지 커스터마이징

To modify the form fields that are required when a new user registers with your application, or to customize how new users are stored into your database, you may modify the `RegisterController` class. This class is responsible for validating and creating new users of your application.

새로운 사용자를 등록할 때 필수로 입력해야 하는 form 항목을 변경하려면, 또는 새로운 사용자가 데이터베이스에 입력되는 방식을 커스터마이징 하려면, `RegisterController` 클래스를 수정하면 됩니다. 이 클래스는 애플리케이션에서 새로운 사용자를 검증하고 생성하는 역할을 합니다.

The `validator` method of the `RegisterController` contains the validation rules for new users of the application. You are free to modify this method as you wish.

`RegisterController` 클래스의 `validator` 메소드는 애플리케이션의 새로운 사용자에 대한 폼 검증 규칙을 포함하고 있으며, 여러분은 원하는대로 이 메소드를 수정해도 됩니다.

The `create` method of the `RegisterController` is responsible for creating new `App\User` records in your database using the [Eloquent ORM](/docs/{{version}}/eloquent). You are free to modify this method according to the needs of your database.

`RegisterController`의 `create` 메소드는 [Eloquent ORM](/docs/{{version}}/eloquent)을 이용하여 데이터베이스에 새 `App\User` 레코드를 생성합니다. 여러분은 데이터베이스의 필요에 따라 이 메소드를 수정해도 됩니다.

<a name="retrieving-the-authenticated-user"></a>
### Retrieving The Authenticated User
### 승인된 사용자 조회하기

You may access the authenticated user via the `Auth` facade:

`Auth` 파사드를 통해 인증된 사용자에게 접근할 수 있습니다.

    use Illuminate\Support\Facades\Auth;

    // Get the currently authenticated user...
    $user = Auth::user();

    // Get the currently authenticated user's ID...
    $id = Auth::id();

Alternatively, once a user is authenticated, you may access the authenticated user via an `Illuminate\Http\Request` instance. Remember, type-hinted classes will automatically be injected into your controller methods:

또는, 사용자가 인증되면, `Illuminate\Http\Request` 인스턴스를 통해 인증된 사용자에게 접근할 수 있습니다. 타입힌트된 클래스는 컨트롤러 메소드에 자동으로 주입된다는 것을 기억하십시오.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;

    class ProfileController extends Controller
    {
        /**
         * Update the user's profile.
         *
         * @param  Request  $request
         * @return Response
         */
        public function update(Request $request)
        {
            // $request->user() returns an instance of the authenticated user...
        }
    }

#### Determining If The Current User Is Authenticated
#### 현재 사용자의 승인 여부 결정하기

To determine if the user is already logged into your application, you may use the `check` method on the `Auth` facade, which will return `true` if the user is authenticated:

사용자가 이미 애플리케이션에 로그인했는지 판별하려면, `Auth` 파사드의 `check` 메소드를 사용할 수 있습니다. 사용자가 인증되었다면 `true`를 반환합니다.

    use Illuminate\Support\Facades\Auth;

    if (Auth::check()) {
        // The user is logged in...
    }

> {tip} Even though it is possible to determine if a user is authenticated using the `check` method, you will typically use a middleware to verify that the user is authenticated before allowing the user access to certain routes / controllers. To learn more about this, check out the documentation on [protecting routes](/docs/{{version}}/authentication#protecting-routes).

> {tip} 사용자가 인증되었는지 `check` 함수로 판단할 수 있긴 해도, 여러분은 사용자의 특정 라우트 / 컨트롤러 접근을 허용하기 전에 그 사용자가 인증 되었는지 확인할 때 보통 미들웨어를 사용하게 될 것입니다. 이에 대한 자세한 내용은 [라우트 보호하기](/docs/{{version}}/authentication#protecting-routes) 문서를 확인하십시오.

<a name="protecting-routes"></a>
### Protecting Routes
### 라우트 보호하기

[Route middleware](/docs/{{version}}/middleware) can be used to only allow authenticated users to access a given route. Laravel ships with an `auth` middleware, which is defined at `Illuminate\Auth\Middleware\Authenticate`. Since this middleware is already registered in your HTTP kernel, all you need to do is attach the middleware to a route definition:

[라우트 미들웨어](/docs/{{version}}/middleware)는 인증된 사용자에게만 주어진 라우트에 접근하도록 허용하는데 사용될 수 있습니다. 라라벨은 `Illuminate\Auth\Middleware\Authenticate`에 정의된 `auth` 미들웨어를 제공하고 있습니다. 이제 여러분이 할 일은 라우트가 정의된 부분에 미들웨어를 추가하는 것 뿐입니다.

    Route::get('profile', function () {
        // Only authenticated users may enter...
    })->middleware('auth');

If you are using [controllers](/docs/{{version}}/controllers), you may call the `middleware` method from the controller's constructor instead of attaching it in the route definition directly:

만약 [컨트롤러 클래스](/docs/{{version}}/controllers)를 사용하고 있다면, 라우트 정의 부분에 직접 추가하는 대신 컨트롤러의 생성자에서 `middleware` 메소드를 호출할 수 있습니다.

    public function __construct()
    {
        $this->middleware('auth');
    }

#### Redirecting Unauthenticated Users
#### 인증되지 않는 사용자 리다이렉팅하기

When the `auth` middleware detects an unauthorized user, it will redirect the user to the `login` [named route](/docs/{{version}}/routing#named-routes). You may modify this behavior by updating the `redirectTo` function in your `app/Http/Middleware/Authenticate.php` file:

사용자가 인증되지 않은 경우, `auth` 미들웨어는 `login` [이라는 이름이 지정된 라우트](/docs/{{version}}/routing#named-routes) 으로 사용자를 리다이렉트 됩니다. `app/Http/Middleware/Authenticate.php` 파일의 `redirectTo` 함수를 수정해서 이 동작을 변경 할 수 있습니다.

    /**
     * Get the path the user should be redirected to.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return string
     */
    protected function redirectTo($request)
    {
        return route('login');
    }

#### Specifying A Guard
#### Guard 지정하기

When attaching the `auth` middleware to a route, you may also specify which guard should be used to authenticate the user. The guard specified should correspond to one of the keys in the `guards` array of your `auth.php` configuration file:

`auth` 미들웨어를 라우트에 추가할 때, 여러분은 또한 어떤 guard가 인증에 사용되어야 하는지 지정할 수 있습니다. 지정된 guard는 `auth.php` 설정 파일의 `guards` 배열에 있는 키 중 하나와 일치해야 합니다.

    public function __construct()
    {
        $this->middleware('auth:api');
    }

<a name="login-throttling"></a>
### Login Throttling
### 로그인 횟수 제한

If you are using Laravel's built-in `LoginController` class, the `Illuminate\Foundation\Auth\ThrottlesLogins` trait will already be included in your controller. By default, the user will not be able to login for one minute if they fail to provide the correct credentials after several attempts. The throttling is unique to the user's username / e-mail address and their IP address.

만약 라라벨에 포함된 `LoginController` 클래스를 사용한다면 `Illuminate\Foundation\Auth\ThrottlesLogins` 트레이트-trait가 이미 포함되어 있을 겁니다. 기본적으로, 사용자가 정확한 계정 정보를 입력하는 데 여러번 실패하면, 1분 동안 로그인하지 못하게 설정되어 있습니다. 이 제한은 사용자의 이름 / 이메일 주소와 IP 주소에 대해 고유하게 동작합니다.

<a name="authenticating-users"></a>
## Manually Authenticating Users
## 수동으로 사용자 인증하기

Note that you are not required to use the authentication controllers included with Laravel. If you choose to remove these controllers, you will need to manage user authentication using the Laravel authentication classes directly. Don't worry, it's a cinch!

라라벨에 포함된 인증 컨트롤러를 꼭 써야하지는 않습니다. 이 컨트롤러들을 삭제한다면 라라벨의 인증 클래스를 사용하여 사용자 인증을 직접 관리해야 합니다. 아주 쉬우니까 걱정하지 마세요!

We will access Laravel's authentication services via the `Auth` [facade](/docs/{{version}}/facades), so we'll need to make sure to import the `Auth` facade at the top of the class. Next, let's check out the `attempt` method:

라라벨의 인증 서비스를 `Auth` [파사드](/docs/{{version}}/facades)를 통해 접근할 것이기 때문에 클래스의 가장 상단 부분에 `Auth` 파사드를 사용하도록 선언하는 것이 필수입니다. 다음은 `attempt` 메소드를 확인해 보겠습니다.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Auth;

    class LoginController extends Controller
    {
        /**
         * Handle an authentication attempt.
         *
         * @param  \Illuminate\Http\Request $request
         *
         * @return Response
         */
        public function authenticate(Request $request)
        {
            $credentials = $request->only('email', 'password');

            if (Auth::attempt($credentials)) {
                // Authentication passed...
                return redirect()->intended('dashboard');
            }
        }
    }

The `attempt` method accepts an array of key / value pairs as its first argument. The values in the array will be used to find the user in your database table. So, in the example above, the user will be retrieved by the value of the `email` column. If the user is found, the hashed password stored in the database will be compared with the `password` value passed to the method via the array. You should not hash the password specified as the `password` value, since the framework will automatically hash the value before comparing it to the hashed password in the database. If the two hashed passwords match an authenticated session will be started for the user.

`attempt` 메소드는 키 / 값의 쌍으로 이루어진 배열을 첫번째 인자로 전달 받습니다. 배열의 값들은 데이터베이스 테이블에서 사용자를 찾는데 사용될 것입니다. 따라서 위의 예제에서는, `email` 컬럼을 통해서 사용자를 찾게됩니다. 사용자를 찾았다면, 해시 처리되어 데이터베이스에 저장된 패스워드와 매소드에 전달받은 배열의 `password` 값을 비교할 것입니다. 프레임워크가 데이터베이스에서 해시 처리된 암호와 비교하기 전에 값을 자동으로 해시처리 하기 때문에, `password` 로 지정된 값을 해시처리해서는 안됩니다. 두개의 해시처리된 패스워드가 일치한다면 해당 사용자의 새로운 인증 세션이 시작됩니다.

The `attempt` method will return `true` if authentication was successful. Otherwise, `false` will be returned.

`attempt` 메소드는 인증이 성공하면 `true` 를 반환합니다. 실패 시 `false` 를 반환합니다.

The `intended` method on the redirector will redirect the user to the URL they were attempting to access before being intercepted by the authentication middleware. A fallback URI may be given to this method in case the intended destination is not available.

리다이렉터의 `intended` 메소드는 사용자가 인증 필터에 잡히기 전에 원래 엑세스 하려고 시도했었던 URL로 사용자를 리다이렉트 시킵니다. 해당 리다이렉트가 불가능한 경우 주어진 대체 URI를 메소드에 지정할 수 있습니다.

#### Specifying Additional Conditions
#### 추가적인 조건 지정하기

If you wish, you may also add extra conditions to the authentication query in addition to the user's e-mail and password. For example, we may verify that user is marked as "active":

원한다면, 사용자의 이메일과 패스워드 외에도 부가적인 조건들을 인증 쿼리에 추가할 수 있습니다. 예를 들어 사용자가 "활성화"로 표시되어있는지 확인할 수 있습니다.

    if (Auth::attempt(['email' => $email, 'password' => $password, 'active' => 1])) {
        // The user is active, not suspended, and exists.
    }

> {note} In these examples, `email` is not a required option, it is merely used as an example. You should use whatever column name corresponds to a "username" in your database.

> {note} 이 예제에서, `email` 은 필수 옵션이 아니라, 그냥 예제에서 사용된것입니다. 여러분은 데이터베이스 안에 있는 "username"과 일치하는 어떠한 컬럼 이름을 사용해야 합니다.

#### Accessing Specific Guard Instances
#### 지정된 Guard 인스턴스에 엑세스하기

You may specify which guard instance you would like to utilize using the `guard` method on the `Auth` facade. This allows you to manage authentication for separate parts of your application using entirely separate authenticatable models or user tables.

여러분은 `Auth` 파사드에 `guard` 메소드를 사용하여 어떤 guard 인스턴스를 사용하고자 하는지 지정할 수 있습니다. 이것은 여러분이 완전히 분리된 인증 모델 또는 사용자 테이블을 사용하여 애플리케이션의 개별 파트에 대한 인증을 관리할 수 있도록 합니다.

The guard name passed to the `guard` method should correspond to one of the guards configured in your `auth.php` configuration file:

`guard` 메소드에 전달되는 guard의 이름은 `auth.php` 설정 파일에 설정된 guard 중 하나와 일치 해야합니다.

    if (Auth::guard('admin')->attempt($credentials)) {
        //
    }

#### Logging Out
#### 로그아웃

To log users out of your application, you may use the `logout` method on the `Auth` facade. This will clear the authentication information in the user's session:

애플리케이션에서 사용자를 로그아웃 시키려면 `Auth` 파사드의 `logout` 메소드를 사용하면 됩니다. 그러면 사용자 세션에서 인증 정보가 제거될 것입니다.

    Auth::logout();

<a name="remembering-users"></a>
### Remembering Users
### 사용자 기억하기

If you would like to provide "remember me" functionality in your application, you may pass a boolean value as the second argument to the `attempt` method, which will keep the user authenticated indefinitely, or until they manually logout. Your `users` table must include the string `remember_token` column, which will be used to store the "remember me" token.

여러분의 애플리케이션에 "기억하기" 기능을 제공하고자 한다면, `attempt` 메소드의 두번째 인자로 사용자의 인증을 무기한 계속 유지할지, 아니면 수동으로 로그아웃 할때까지 유지할지 결정하는 boolean 값을 전달하면 됩니다. 이를 위해서는 `users` 테이블은 "기억하기" 토근을 저장하는데 사용되는 `remember_token` 컬럼을 가지고 있어야만 합니다.

    if (Auth::attempt(['email' => $email, 'password' => $password], $remember)) {
        // The user is being remembered...
    }

> {tip} If you are using the built-in `LoginController` that is shipped with Laravel, the proper logic to "remember" users is already implemented by the traits used by the controller.

> {tip} Laravel에 기본 제공되는 `LoginController`를 사용하는 경우, "remember"하는 사용자를 처리하는 적절한 로직은 이미 컨트롤러에서 사용하는 트레이트-trait에 구현되어 있습니다.

If you are "remembering" users, you may use the `viaRemember` method to determine if the user was authenticated using the "remember me" cookie:

여러분이 "remember"하는 사용자라면, `viaRemember` 메소드를 통해 해당 사용자가 "remember me" 쿠키로 인증이 되었는 지 확인 할 수 있습니다.

    if (Auth::viaRemember()) {
        //
    }

<a name="other-authentication-methods"></a>
### Other Authentication Methods
### 그 외 인증 메소드

#### Authenticate A User Instance
#### 사용자 인스턴스를 통해서 인증하기

If you need to log an existing user instance into your application, you may call the `login` method with the user instance. The given object must be an implementation of the `Illuminate\Contracts\Auth\Authenticatable` [contract](/docs/{{version}}/contracts). The `App\User` model included with Laravel already implements this interface:

애플리케이션에 이미 존재하는 사용자 인스턴스를 통해서 로그인을 하려면 사용자 인스턴스의 `login` 메소드를 호출할 수 있습니다. 주어진 객체는 `Illuminate\Contracts\Auth\Authenticatable` [contract](/docs/{{version}}/contracts)를 구현해야 합니다. 라라벨에 포함된 `App\User` 모델은 이미 이 인터페이스를 구현합니다.

    Auth::login($user);

    // Login and "remember" the given user...
    Auth::login($user, true);

You may specify the guard instance you would like to use:

사용하고자 하는 가드 인스턴스를 지정할 수도 있습니다.

    Auth::guard('admin')->login($user);

#### Authenticate A User By ID
#### ID를 통해서 사용자 인증하기

To log a user into the application by their ID, you may use the `loginUsingId` method. This method accepts the primary key of the user you wish to authenticate:

사용자를 ID를 통해 애플리케이션에 로그인 시키려면, `loginUsingId` 메소드를 사용하면 됩니다. 이 메소드는 인증하고자 하는 사용자의 프라이머리 키를 전달 받습니다.

    Auth::loginUsingId(1);

    // Login and "remember" the given user...
    Auth::loginUsingId(1, true);

#### Authenticate A User Once
#### 사용자 인증 한 번 하기

You may use the `once` method to log a user into the application for a single request. No sessions or cookies will be utilized, which means this method may be helpful when building a stateless API:

하나의 request에 대해서 `once` 메소드로 사용자를 로그인시킬 수 있습니다. 세션과 쿠키는 활용되지 않을 것이며 이는 상태를 유지하지 않는 API를 만드는데 도움이 됩니다.

    if (Auth::once($credentials)) {
        //
    }

<a name="http-basic-authentication"></a>
## HTTP Basic Authentication
## HTTP 기본 인증

[HTTP Basic Authentication](https://en.wikipedia.org/wiki/Basic_access_authentication) provides a quick way to authenticate users of your application without setting up a dedicated "login" page. To get started, attach the `auth.basic` [middleware](/docs/{{version}}/middleware) to your route. The `auth.basic` middleware is included with the Laravel framework, so you do not need to define it:

[HTTP 기본 인증](https://en.wikipedia.org/wiki/Basic_access_authentication)은 애플리케이션에 별도의 "login" 페이지 설정없이도 사용자 인증을 할 수 있는 손쉬운 방법을 제공합니다. 이를 위해서는 `auth.basic` [미들웨어](/docs/{{version}}/middleware)를 라우트에 추가하면 됩니다. `auth.basic` 미들웨어는 라라벨에 포함되어 있기 때문에 따로 정의할 필요가 없습니다.

    Route::get('profile', function () {
        // Only authenticated users may enter...
    })->middleware('auth.basic');

Once the middleware has been attached to the route, you will automatically be prompted for credentials when accessing the route in your browser. By default, the `auth.basic` middleware will use the `email` column on the user record as the "username".

미들웨어가 라우트에 추가되면 브라우저에서 라우트에 접근할 때 자동으로 인증 자격을 증명할 것을 요구받을 것입니다. `auth.basic` 미들웨어는 기본적으로 사용자 레코드의 `email` 컬럼을 "username"으로 사용합니다.

#### A Note On FastCGI
#### FastCGI에 대한 참고사항

If you are using PHP FastCGI, HTTP Basic authentication may not work correctly out of the box. The following lines should be added to your `.htaccess` file:

PHP FastCGI를 사용하는 경우, HTTP 기본 인증이 제대로 작동하지 않을 것입니다. 다음을 `.htaccess` 파일에 추가하십시오.

    RewriteCond %{HTTP:Authorization} ^(.+)$
    RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

<a name="stateless-http-basic-authentication"></a>
### Stateless HTTP Basic Authentication
### 상태를 유지하지 않는 HTTP 기본 인증

You may also use HTTP Basic Authentication without setting a user identifier cookie in the session, which is particularly useful for API authentication. To do so, [define a middleware](/docs/{{version}}/middleware) that calls the `onceBasic` method. If no exception is thrown by the `onceBasic` method, the request may be passed further into the application:

여러분은 또한 API 인증에 유용한, 세션의 사용자 식별 쿠키가 없는 HTTP 기본 인증을 사용할 수도 있습니다. 이렇게 하기 위해서는 `onceBasic` 메소드를 호출하는 [미들웨어](/docs/{{version}}/middleware)를 정의하면 됩니다. `onceBasic` 메소드가 아무런 예외를 던지지 않는다면 이 요청은 애플리케이션 안에서 계속 진행될 것입니다.

    <?php

    namespace App\Http\Middleware;

    use Illuminate\Support\Facades\Auth;

    class AuthenticateOnceWithBasicAuth
    {
        /**
         * Handle an incoming request.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  \Closure  $next
         * @return mixed
         */
        public function handle($request, $next)
        {
            return Auth::onceBasic() ?: $next($request);
        }

    }

Next, [register the route middleware](/docs/{{version}}/middleware#registering-middleware) and attach it to a route:

다음으로, [라우트 미들웨어를 등록하고](/docs/{{version}}/middleware#registering-middleware) 이를 라우트에 추가하십시오.

    Route::get('api/user', function () {
        // Only authenticated users may enter...
    })->middleware('auth.basic.once');

<a name="logging-out"></a>
## Logging Out
## 로그아웃

To manually log users out of your application, you may use the `logout` method on the `Auth` facade. This will clear the authentication information in the user's session:

애플리케이션에서 사용자를 로그아웃 시키려면, `Auth` 파사드의 `logout` 메소드를 사용하면 됩니다. 이 메소드는 사용자의 세션에서 인증 정보를 삭제할 것입니다.

    use Illuminate\Support\Facades\Auth;

    Auth::logout();

<a name="invalidating-sessions-on-other-devices"></a>
### Invalidating Sessions On Other Devices
### 다른 디바이스의 세션 무효화

Laravel also provides a mechanism for invalidating and "logging out" a user's sessions that are active on other devices without invalidating the session on their current device. Before getting started, you should make sure that the `Illuminate\Session\Middleware\AuthenticateSession` middleware is present and un-commented in your `app/Http/Kernel.php` class' `web` middleware group:

라라벨은 현재 접속한 디바이스의 세션은 유지하면서 다른 디바이스의 사용자 세션을 무효화하고 "로그아웃" 시키는 기능을 제공합니다. 이를 위해서 `app/Http/Kernel.php` 클래스의 `web` 미들웨어 그룹의 코멘트가 해제되었는지 확인하십시오.

    'web' => [
        // ...
        \Illuminate\Session\Middleware\AuthenticateSession::class,
        // ...
    ],

Then, you may use the `logoutOtherDevices` method on the `Auth` facade. This method requires the user to provide their current password, which your application should accept through an input form:

이렇게 하면, `Auth` 파사드의 `logoutOtherDevices` 메소드를 사용할 수 있습니다. 이 메소드를 사용하려면 사용자가 입력폼을 통해서 패스워드를 입력하도록 해야합니다.

    use Illuminate\Support\Facades\Auth;

    Auth::logoutOtherDevices($password);

> {note} When the `logoutOtherDevices` method is invoked, the user's other sessions will be invalidated entirely, meaning they will be "logged out" of all guards they were previously authenticated by.

> {note} `logoutOtherDevices` 메소드가 호출될 때, 사용자의 다른 세션은 완전히 무효화됩니다. 이는, 이전에 인증되었던 모든 사용자 정보가 "로그아웃"됨을 의미합니다.

<a name="adding-custom-guards"></a>
## Adding Custom Guards
## 사용자정의 Guard 추가

You may define your own authentication guards using the `extend` method on the `Auth` facade. You should place this call to `extend` within a [service provider](/docs/{{version}}/providers). Since Laravel already ships with an `AuthServiceProvider`, we can place the code in that provider:

여러분은 `Auth` 파사드의 `extend` 메소드를 사용하여 고유한 인증 guard를 정의할 수 있습니다. 이 메소드는 [서비스 프로바이더](/docs/{{version}}/providers) 중 하나의 `extend`에서 호출해야 합니다. 라라벨은 이미 `AuthServiceProvider`를 통해 제공하고 있으므로, 우린 그 프로바이더 안에 코드만 넣으면 됩니다.

    <?php

    namespace App\Providers;

    use App\Services\Auth\JwtGuard;
    use Illuminate\Support\Facades\Auth;
    use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;

    class AuthServiceProvider extends ServiceProvider
    {
        /**
         * Register any application authentication / authorization services.
         *
         * @return void
         */
        public function boot()
        {
            $this->registerPolicies();

            Auth::extend('jwt', function ($app, $name, array $config) {
                // Return an instance of Illuminate\Contracts\Auth\Guard...

                return new JwtGuard(Auth::createUserProvider($config['provider']));
            });
        }
    }

As you can see in the example above, the callback passed to the `extend` method should return an implementation of `Illuminate\Contracts\Auth\Guard`. This interface contains a few methods you will need to implement to define a custom guard. Once your custom guard has been defined, you may use this guard in the `guards` configuration of your `auth.php` configuration file:

이 예제에서 볼 수 있듯이, `extend` 메소드에 전달된 콜백은 `Illuminate\Contracts\Auth\Guard` 구현체를 반환해야 합니다. 이 인터페이스는 사용자 정의 guard를 구현하는데 필요한 몇가지 메소드를 가지고 있습니다. 사용자 정의 guard를 정의하고나면, `guards` 설정에서 정의한 guard 를 사용할 수 있습니다.

    'guards' => [
        'api' => [
            'driver' => 'jwt',
            'provider' => 'users',
        ],
    ],

<a name="closure-request-guards"></a>
### Closure Request Guards
### 클로저 형태의 Request Guards

The simplest way to implement a custom, HTTP request based authentication system is by using the `Auth::viaRequest` method. This method allows you to quickly define your authentication process using a single Closure.

인증 시스템을 기반으로한 커스텀 HTTP request-요청을 구현하는 가장간단한 방법은 `Auth::viaRequest` 메소드를 사용하는 것 입니다. 이 메소드는 하나의 클로저를 사용하여 빠르기 인증을 정의할 수 있게 해줍니다.

To get started, call the `Auth::viaRequest` method within the `boot` method of your `AuthServiceProvider`. The `viaRequest` method accepts an authentication driver name as its first argument. This name can be any string that describes your custom guard. The second argument passed to the method should be a Closure that receives the incoming HTTP request and returns a user instance or, if authentication fails, `null`:

시작하려면, `AuthServiceProvider` 의 `boot` 메소드에서 `Auth::viaRequest` 메소드를 호출하면 됩니다. `viaRequest` 메소드는 인증 드라이버 이름을 첫번째 인자로 전달 받습니다. 이 이름은 커스텀 guard를 표현하는 문자열이 될 수도 있습니다. 메소드에 전달되는 두번째 인자는 유입되는 HTTP request-요청을 전달받아 사용자 인스턴스를 반환하는 클로저여야하며, 인증에 실패한다면, `null` 을 반환해야 합니다.

    use App\User;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Auth;

    /**
     * Register any application authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();

        Auth::viaRequest('custom-token', function ($request) {
            return User::where('token', $request->token)->first();
        });
    }

Once your custom authentication driver has been defined, you use it as a driver within `guards` configuration of your `auth.php` configuration file:

커스텀 인증 드라이버 를 정의했다면, `auth.php` 설정 파일의 `guards` 설정 안에서 사용할 수 있습니다.

    'guards' => [
        'api' => [
            'driver' => 'custom-token',
        ],
    ],

<a name="adding-custom-user-providers"></a>
## Adding Custom User Providers
## 사용자 정의 User 프로바이더 추가하기

If you are not using a traditional relational database to store your users, you will need to extend Laravel with your own authentication user provider. We will use the `provider` method on the `Auth` facade to define a custom user provider:

사용자들을 저장하는 데 전통적인 관계형 데이터베이스를 이용하지 않는다면 사용자 정의 User 프로바이더를 통해 라라벨을 확장해야 합니다. 우린 사용자 정의 User 프로바이더를 정의하기 위해 `Auth` 파사드의 `provider` 메소드를 사용할 것입니다.

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\Auth;
    use App\Extensions\RiakUserProvider;
    use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;

    class AuthServiceProvider extends ServiceProvider
    {
        /**
         * Register any application authentication / authorization services.
         *
         * @return void
         */
        public function boot()
        {
            $this->registerPolicies();

            Auth::provider('riak', function ($app, array $config) {
                // Return an instance of Illuminate\Contracts\Auth\UserProvider...

                return new RiakUserProvider($app->make('riak.connection'));
            });
        }
    }

After you have registered the provider using the `provider` method, you may switch to the new user provider in your `auth.php` configuration file. First, define a `provider` that uses your new driver:

`provider` 메소드로 프로바이더를 등록한 뒤, `auth.php` 설정 파일에서 새 User 프로바이더로 변경할 수 있습니다. 먼저 새로운 드라이버를 사용하는 `provider`를 정의합시다.

    'providers' => [
        'users' => [
            'driver' => 'riak',
        ],
    ],

Finally, you may use this provider in your `guards` configuration:

마지막으로, `guards` 설정 에서 이 프로바이더를 사용할 수 있습니다.

    'guards' => [
        'web' => [
            'driver' => 'session',
            'provider' => 'users',
        ],
    ],

<a name="the-user-provider-contract"></a>
### The User Provider Contract
### 사용자 프로바이더 Contract

The `Illuminate\Contracts\Auth\UserProvider` implementations are only responsible for fetching a `Illuminate\Contracts\Auth\Authenticatable` implementation out of a persistent storage system, such as MySQL, Riak, etc. These two interfaces allow the Laravel authentication mechanisms to continue functioning regardless of how the user data is stored or what type of class is used to represent it.

`Illuminate\Contracts\Auth\UserProvider`의 구현체는 MySQL, Riak 등의 지속적인 스토리지 시스템에서 `Illuminate\Contracts\Auth\Authenticatable`을 불러오는 것만 담당합니다. 이 두 인터페이스는 사용자 데이터가 어떻게 저장되었는지 또는 이를 위해 어떤 클래스가 사용되고 있는지 여부에 상관없이 라라벨 인증 메커니즘이 작동하도록 해줍니다.

Let's take a look at the `Illuminate\Contracts\Auth\UserProvider` contract:

`Illuminate\Contracts\Auth\UserProvider` contract를 살펴보겠습니다.

    <?php

    namespace Illuminate\Contracts\Auth;

    interface UserProvider {

        public function retrieveById($identifier);
        public function retrieveByToken($identifier, $token);
        public function updateRememberToken(Authenticatable $user, $token);
        public function retrieveByCredentials(array $credentials);
        public function validateCredentials(Authenticatable $user, array $credentials);

    }

The `retrieveById` function typically receives a key representing the user, such as an auto-incrementing ID from a MySQL database. The `Authenticatable` implementation matching the ID should be retrieved and returned by the method.

`retrieveById` 함수는 일반적으로 MySQL 데이터베이스의 자동 증가 ID와 같은 사용자를 대표하는 키를 전달 받습니다. ID에 상응하는 `Authenticatable` 구현체가 조회되고 그 메소드를 통해 반환됩니다.

The `retrieveByToken` function retrieves a user by their unique `$identifier` and "remember me" `$token`, stored in a field `remember_token`. As with the previous method, the `Authenticatable` implementation should be returned.

`retrieveByToken`와 `remember_token`에 저장된 "remember me" `$token`에 따라 사용자를 조회합니다. 이전의 메소드와 같이 `Authenticatable` 구현체가 반환됩니다.

The `updateRememberToken` method updates the `$user` field `remember_token` with the new `$token`. A fresh token is assigned on a successful "remember me" login attempt or when the user is logging out.

`updateRememberToken` 메소드는 새로운 `$token`으로 `$user`의 `remember_token`필드를 업데이트합니다. 새로운 토큰값은 정상적으로 "remember me"을 통해서 로그인을 시도하기 위해서 할당된 값이거나, 사용자가 로그아웃되었을 때를 위한 값일 수 있습니다.

The `retrieveByCredentials` method receives the array of credentials passed to the `Auth::attempt` method when attempting to sign into an application. The method should then "query" the underlying persistent storage for the user matching those credentials. Typically, this method will run a query with a "where" condition on `$credentials['username']`. The method should then return an implementation of `Authenticatable`. **This method should not attempt to do any password validation or authentication.**

`retrieveByCredentials` 메소드는 애플리케이션에 로그인하려고 시도할 때 `Auth::attempt` 메소드로 전달되는 인증 정보 배열을 전달 받습니다. 그러면 메소드는 그 인증 정보에 맞는 사용자들을 기반에 있는 지속적 저장소(persistent storage)에서 "질의"해야 합니다. 일반적으로 이 메소드는 `$credentials['username']`에 맞는 "where" 쿼리를 실행할 것입니다. 그 뒤에 메소드는 `Authenticatable`의 구현체를 반환해야 합니다. **이 메소드는 패스워드 확인이나 인증을 시도해서는 안됩니다.**

The `validateCredentials` method should compare the given `$user` with the `$credentials` to authenticate the user. For example, this method should probably use `Hash::check` to compare the value of `$user->getAuthPassword()` to the value of `$credentials['password']`. This method should return `true` or `false` indicating on whether the password is valid.

`validateCredentials` 메소드는 사용자를 인증하기 위하여 주어진 `$user`를 `$credentials`에 비교해야 합니다. 예를 들자면, 이 메소드는 `$user->getAuthPassword()`의 값을 `$credentials['password']`와 비교하기 위해 `Hash::check`를 사용하게 될 것 입니다. 이 메소드는 패스워드가 유효한 지를 나타내는 `true` 혹은 `false` 값을 반환합니다.

<a name="the-authenticatable-contract"></a>
### The Authenticatable Contract
### Authenticatable Contract 살펴보기

Now that we have explored each of the methods on the `UserProvider`, let's take a look at the `Authenticatable` contract. Remember, the provider should return implementations of this interface from the `retrieveById`, `retrieveByToken`, and `retrieveByCredentials` methods:

`UserProvider`의 각 메소드에 대해 알아보았으니 이제 `Authenticatable` contract를 살펴 보도록 하겠습니다. 프로바이더는 `retrieveById`, `retrieveByToken`, 그리고 `retrieveByCredentials` 메소드에서 이 인터페이스의 구현을 반환해야 한다는 것을 기억하십시오..

    <?php

    namespace Illuminate\Contracts\Auth;

    interface Authenticatable {

        public function getAuthIdentifierName();
        public function getAuthIdentifier();
        public function getAuthPassword();
        public function getRememberToken();
        public function setRememberToken($value);
        public function getRememberTokenName();

    }

This interface is simple. The `getAuthIdentifierName` method should return the name of the "primary key" field of the user and the `getAuthIdentifier` method should return the "primary key" of the user. In a MySQL back-end, again, this would be the auto-incrementing primary key. The `getAuthPassword` should return the user's hashed password. This interface allows the authentication system to work with any User class, regardless of what ORM or storage abstraction layer you are using. By default, Laravel includes a `User` class in the `app` directory which implements this interface, so you may consult this class for an implementation example.

이 인터페이스는 간단합니다. `getAuthIdentifierName` 메소드는 사용자의 "프라이머리 키" 필드의 이름을 반환해야하며 `getAuthIdentifier` 메소드는 사용자의 "프라이머리 키"를 반환합니다. MySQL의 경우 auto-incrementing primary key에 해당합니다. `getAuthPassword`는 사용자의 해시된 패스워드를 반환합니다. 이 인터페이스는 어떤 ORM 혹은 저장소 추상화 계층을 사용하든지에 상관없이 인증 시스템이 어떤 사용자 클래스에도 적용 될수 있도록 해줍니다. 라라벨은 기본적으로 이 인터페이스를 구현하는 `app` 디렉토리에 `User` 클래스를 포함하기 때문에 구현된 예를 보기 위해 이 클래스를 이용면 됩니다.

<a name="events"></a>
## Events
## 이벤트

Laravel raises a variety of [events](/docs/{{version}}/events) during the authentication process. You may attach listeners to these events in your `EventServiceProvider`:

라라벨은 인증 과정 중에 다양한 [이벤트](/docs/{{version}}/events)들을 발생시킵니다. `EventServiceProvider`에서 이 이벤트들을 위한 listener를 추가할 수 있습니다.

    /**
     * The event listener mappings for the application.
     *
     * @var array
     */
    protected $listen = [
        'Illuminate\Auth\Events\Registered' => [
            'App\Listeners\LogRegisteredUser',
        ],

        'Illuminate\Auth\Events\Attempting' => [
            'App\Listeners\LogAuthenticationAttempt',
        ],

        'Illuminate\Auth\Events\Authenticated' => [
            'App\Listeners\LogAuthenticated',
        ],

        'Illuminate\Auth\Events\Login' => [
            'App\Listeners\LogSuccessfulLogin',
        ],

        'Illuminate\Auth\Events\Failed' => [
            'App\Listeners\LogFailedLogin',
        ],

        'Illuminate\Auth\Events\Logout' => [
            'App\Listeners\LogSuccessfulLogout',
        ],

        'Illuminate\Auth\Events\Lockout' => [
            'App\Listeners\LogLockout',
        ],
        'Illuminate\Auth\Events\PasswordReset' => [
            'App\Listeners\LogPasswordReset',
        ],
    ];
