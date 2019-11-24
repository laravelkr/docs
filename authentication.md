# 인증

- [시작하기](#introduction)
    - [데이터베이스 유의사항](#introduction-database-considerations)
- [빠르게 인증 살펴보기](#authentication-quickstart)
    - [Routing-라우팅](#included-routing)
    - [Views-뷰](#included-views)
    - [인증하기](#included-authenticating)
    - [승인된 사용자 조회하기](#retrieving-the-authenticated-user)
    - [라우트 보호하기](#protecting-routes)
    - [비밀번호 확인](#password-confirmation)
    - [로그인 횟수 제한](#login-throttling)
- [수동으로 사용자 인증하기](#authenticating-users)
    - [사용자 기억하기](#remembering-users)
    - [그외 인증 메소드](#other-authentication-methods)
- [HTTP 기본 인증](#http-basic-authentication)
    - [상태를 유지하지 않는 HTTP 기본 인증](#stateless-http-basic-authentication)
- [로그아웃](#logging-out)
    - [다른 디바이스의 세션 무효화](#invalidating-sessions-on-other-devices)
- [소셜 인증](https://github.com/laravel/socialite)
- [사용자 정의 Guards 추가하기](#adding-custom-guards)
    - [클로저 형태의 Request Guards](#closure-request-guards)
- [사용자 정의 User 프로바이더 추가하기](#adding-custom-user-providers)
    - [사용자 프로바이더 Contract](#the-user-provider-contract)
    - [인증가능 계약](#the-authenticatable-contract)
- [이벤트](#events)


<a name="introduction"></a>
## 시작하기

> {tip} ** 빠르게 시작하길 원하십니까? ** 새로이 생성한 Laravel 애플리케이션에 `laravel/ui` 컴포저 패키지를 설치하고 `php artisan ui vue --auth` 명령어를 실행하십시오. 그 다음, 브라우저에서 `http://your-app.test/register` 또는 다른 URL로 이동하세요. 이 명령어는 전체적인 인증 시스템을 스캐폴딩합니다.

라라벨은 인증기능 구현을 매우 쉽게 해줍니다. 기본적으로 별도의 설정 없이도 대부분 이미 준비되어 있습니다. 인증에 대한 설정 파일은 `config/auth.php`으로 인증 서비스의 동작을 제어할 수 있는 옵션들이 자세한 설명과 함께 제공됩니다.

내부 시스템에서, 라라벨의 인증 기능은 "guards" 와 "프로바이더"로 구성되어 있습니다. Guard는 사용자가 각각의 요청-request마다 어떻게 인증되는지 정의합니다. 예를 들어 라라벨은 세션 스토리지와 쿠키를 사용하여 상태를 유지하는 `session` guard와, 각 요청-request와 함께 전달되는 "API 토큰"을 사용하여 사용자를 인증하는 `token` guard를 제공합니다.

프로바이더는 저장소에서 사용자를 어떻게 찾을 수 있는지 정의합니다. 라라벨은 Eloquent와 데이터베이스 쿼리 빌더를 사용하여 사용자를 찾을 수 있도록 지원합니다. 또한, 애플리케이션에서 필요로 하는 추가적인 프로바이더를 자유롭게 정의할 수 있습니다.

지금 이러한 이야기가 혼란스럽더라도 걱정하지 마십시오. 많은 애플리케이션은 기본 인증 설정을 변경할 필요가 전혀 없습니다.

<a name="introduction-database-considerations"></a>
### 데이터베이스 유의사항

기본적으로 라라벨은 `app` 디렉토리에 `App\User` [Eloquent 모델](/docs/{{version}}/eloquent) 모델을 포함하고 있습니다. 이 모델은 기본적인 Eloquent 인증 드라이버와 함께 사용하게 됩니다. 애플리케이션이 Eloquent를 사용하고 있지 않다면 라라벨 쿼리 빌더를 사용하는 `database` 인증 드라이버를 이용하면 됩니다.

`App\User` 모델을 위한 데이터베이스 스키마를 구성할 때, 패스워드 컬럼은 최소 60자가 되어야 하는 것을 명심하십시오. 기본값인 255을 유지하는 것도 좋은 선택입니다.

또한 `users` (또는 동일한) 테이블이 NULL을 허용하는 100자리 문자열의 `remember_token` 컬럼을 포함하고 있는지 확인하십시오. 이 컬럼은 애플리케이션에 로그인할 때 "remember me" 옵션을 선택한 사용자의 토큰을 저장하는 데 사용됩니다.

<a name="authentication-quickstart"></a>
## 빠르게 인증 살펴보기

라라벨은 별도의 설정 없이도 `App\Http\Controllers\Auth` 네임스페이스에 위치한 몇가지 인증 컨트롤러를 제공하고 있습니다. `RegisterController`는 새로운 사용자의 등록을, `LoginController`는 인증을 처리하고, `ForgotPasswordController`는 암호 재설정을 위한 링크 생성을, `ResetPasswordController`는 암호를 재설정하는 로직이 들어 있습니다. 각각의 컨트롤러는 필요한 메소드를 포함하기 위해 트레이트-trait를 사용합니다. 대부분의 애플리케이션에서 여러분은 이 컨트롤러들을 수정할 필요가 없을 것입니다.

<a name="included-routing"></a>
### Routing-라우팅

라라벨의 `laravel/ui` 패키지는 다음의 간단한 명령어들을 통해서 인증에 필요한 모든 라우트와 뷰를 스캐폴딩 할 수 있는 손쉬운 방법을 제공합니다.

    composer require laravel/ui --dev

    php artisan ui vue --auth

이 명령어는 새로운 애플리케이션에서 사용되어야 하며, 레이아웃 뷰, 등록과 로그인 뷰, 모든 인증의 진입점을 위한 라우팅 기능을 설치할 것입니다. 또한 로그인 후 여러분의 대시보드 페이지를 요청할 수 있는 `HomeController`도 함께 설치됩니다.

> {tip} 여러분의 애플리케이션에서 회원가입을 필요로 하지 않는다면, 새롭게 생성되는 `RegisterController` 파일을 삭제하고 라우트 정의 파일에서 `Auth::routes(['register' => false]);`를 수정하면 됩니다.

#### 인증을 포함한 애플리케이션 만들기

새로운 애플리케이션을 만들때 인증 스캐폴딩을 포함하려면 `--auth`을 사용하면 됩니다. 이 명령은 모든 인증 스캐폴딩을 컴파일하고 설치하여 새 애플리케이션을 만듭니다.

    laravel new blog --auth

<a name="included-views"></a>
### Views-뷰

이전 섹션에서 언급했듯이 `laravel/ui` 패키지의 `php artisan ui vue --auth` 명령은 인증에 필요한 모든 뷰를 `resources/views/auth` 디렉토리에 생성합니다.

`ui` 명령은 또한 애플리케이션의 기본 레이아웃을 포함하는 `resources/views/layouts` 디렉토리를 만듭니다. 이러한 모든 뷰는 Bootstrap CSS 프레임워크를 사용하지만 원하는대로 자유롭게 커스텀 할 수 있습니다.

<a name="included-authenticating"></a>
### 인증하기

이제 인증 컨트롤러에 대한 라우트와 뷰가 설정되었으니, 애플리케이션에 새로운 사용자를 등록하거나 인증할 준비가 되었습니다! 기존 사용자를 인증하거나 새로운 사용자를 데이터베이스에 저장하는 (트레이트-trait를 통한) 로직은 이미 인증 컨트롤러에 포함되어 있으므로, 브라우저로 여러분의 애플리케이션에 접근하기만 하면 됩니다.

#### 리다이렉트 경로 수정하기

사용자가 성공적으로 인증되면, `/home` URI로 리다이렉트 될 것입니다. 여러분은 `LoginController`, `RegisterController`, `ResetPasswordController` 그리고 `VerificationController` 의 `redirectTo` 속성을 정의하여, 인증 이후의 리다이렉트 경로를 커스터마이징 할 수 있습니다.

    protected $redirectTo = '/';

그리고, 사용자가 리다이렉팅 될 때 사용하는 새로운 URI를 사용하도록 `RedirectIfAuthenticated` 미들웨어의 `handle` 메소드를 수정해야 합니다.

리다이렉트 경로가 커스텀 생성 로직을 필요로 한다면 `redirectTo` 속성 대신 `redirectTo` 메소드를 정의할 수 있습니다.

    protected function redirectTo()
    {
        return '/path';
    }

> {tip} `redirectTo` 메소드는 `redirectTo` 속성보다 우선합니다.

#### 어떤 사용자이름을 사용할지 결정하기

기본적으로 라라벨은 인증에 `email` 필드를 사용합니다. 이를 커스터마이징 하려면, `LoginController`의 `username` 메소드를 정의하면 됩니다.

    public function username()
    {
        return 'username';
    }

#### Guard 커스터마이징

또한 여러분은 사용자 인증과 등록에 사용되는 "guard"를 커스터마이징 할 수 있습니다. 시작하기에 앞서, `LoginController`, `RegisterController`, 그리고 `ResetPasswordController`에 `guard` 메소드를 정의하시기 바랍니다. 이 메소드는 guard 인스턴스를 리턴해야만 합니다.

    use Illuminate\Support\Facades\Auth;

    protected function guard()
    {
        return Auth::guard('guard-name');
    }

#### Validation-유효성 검사 / 스토리지 커스터마이징

새로운 사용자를 등록할 때 필수로 입력해야 하는 form 항목을 변경하려면, 또는 새로운 사용자가 데이터베이스에 입력되는 방식을 커스터마이징 하려면, `RegisterController` 클래스를 수정하면 됩니다. 이 클래스는 애플리케이션에서 새로운 사용자를 검증하고 생성하는 역할을 합니다.

`RegisterController` 클래스의 `validator` 메소드는 애플리케이션의 새로운 사용자에 대한 폼 검증 규칙을 포함하고 있으며, 여러분은 원하는대로 이 메소드를 수정해도 됩니다.

`RegisterController`의 `create` 메소드는 [Eloquent ORM](/docs/{{version}}/eloquent)을 이용하여 데이터베이스에 새 `App\User` 레코드를 생성합니다. 여러분은 데이터베이스의 필요에 따라 이 메소드를 수정해도 됩니다.

<a name="retrieving-the-authenticated-user"></a>
### 승인된 사용자 조회하기

`Auth` 파사드를 통해 인증된 사용자에게 접근할 수 있습니다.

    use Illuminate\Support\Facades\Auth;

    // Get the currently authenticated user...
    $user = Auth::user();

    // Get the currently authenticated user's ID...
    $id = Auth::id();

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

#### 현재 사용자의 승인 여부 결정하기

사용자가 이미 애플리케이션에 로그인했는지 판별하려면, `Auth` 파사드의 `check` 메소드를 사용할 수 있습니다. 사용자가 인증되었다면 `true`를 반환합니다.

    use Illuminate\Support\Facades\Auth;

    if (Auth::check()) {
        // The user is logged in...
    }

> {tip} 사용자가 인증되었는지 `check` 함수로 판단할 수 있긴 해도, 여러분은 사용자의 특정 라우트 / 컨트롤러 접근을 허용하기 전에 그 사용자가 인증 되었는지 확인할 때 보통 미들웨어를 사용하게 될 것입니다. 이에 대한 자세한 내용은 [라우트 보호하기](/docs/{{version}}/authentication#protecting-routes) 문서를 확인하십시오.

<a name="protecting-routes"></a>
### 라우트 보호하기

[라우트 미들웨어](/docs/{{version}}/middleware)는 인증된 사용자에게만 주어진 라우트에 접근하도록 허용하는데 사용될 수 있습니다. 라라벨은 `Illuminate\Auth\Middleware\Authenticate`에 정의된 `auth` 미들웨어를 제공하고 있습니다. 이제 여러분이 할 일은 라우트가 정의된 부분에 미들웨어를 추가하는 것 뿐입니다.

    Route::get('profile', function () {
        // Only authenticated users may enter...
    })->middleware('auth');

만약 [컨트롤러 클래스](/docs/{{version}}/controllers)를 사용하고 있다면, 라우트 정의 부분에 직접 추가하는 대신 컨트롤러의 생성자에서 `middleware` 메소드를 호출할 수 있습니다.

    public function __construct()
    {
        $this->middleware('auth');
    }

#### 인증되지 않는 사용자 리다이렉팅하기

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

#### Guard 지정하기

`auth` 미들웨어를 라우트에 추가할 때, 여러분은 또한 어떤 guard가 인증에 사용되어야 하는지 지정할 수 있습니다. 지정된 guard는 `auth.php` 설정 파일의 `guards` 배열에 있는 키 중 하나와 일치해야 합니다.

    public function __construct()
    {
        $this->middleware('auth:api');
    }

<a name="password-confirmation"></a>
### 비밀번호 확인

때로는 애플리케이션의 특정 영역에 액세스하기 전에 사용자에게 암호를 입력하도록 요청할 수 있습니다. 예를 들어, 사용자가 애플리케이션 내에서 결제 설정을 변경하는 곳에서 사용할 수 있습니다.

이를 위해 라라벨은 `password.confirm` 미들웨어를 제공합니다. `password.confirm` 미들웨어를 라우트에 연결하면 사용자가 계속 진행하기 전에 비밀번호를 확인해야하는 화면으로 사용자를 리디렉션합니다.

    Route::get('/settings/security', function () {
        // Users must confirm their password before continuing...
    })->middleware(['auth', 'password.confirm']);

사용자가 자신의 암호를 확인하면 원래 접근하려는 경로로 다시 돌아갑니다. 기본적으로 비밀번호를 확인한 후 사용자는 3시간 동안 비밀번호를 다시 확인 할 필요가 없습니다. `auth.password_timeout` 설정 옵션을 사용하여 사용자가 비밀번호를 다시 확인해야하는 시간을 자유롭게 변경 할 수 있습니다.

<a name="login-throttling"></a>
### 로그인 횟수 제한

만약 라라벨에 포함된 `LoginController` 클래스를 사용한다면 `Illuminate\Foundation\Auth\ThrottlesLogins` 트레이트-trait가 이미 포함되어 있을 겁니다. 기본적으로, 사용자가 정확한 계정 정보를 입력하는 데 여러번 실패하면, 1분 동안 로그인하지 못하게 설정되어 있습니다. 이 제한은 사용자의 이름 / 이메일 주소와 IP 주소에 대해 고유하게 동작합니다.

<a name="authenticating-users"></a>
## 수동으로 사용자 인증하기

라라벨에 포함된 인증 컨트롤러를 꼭 써야하지는 않습니다. 이 컨트롤러들을 삭제한다면 라라벨의 인증 클래스를 사용하여 사용자 인증을 직접 관리해야 합니다. 아주 쉬우니까 걱정하지 마세요!

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

`attempt` 메소드는 키 / 값의 쌍으로 이루어진 배열을 첫번째 인자로 전달 받습니다. 배열의 값들은 데이터베이스 테이블에서 사용자를 찾는데 사용될 것입니다. 따라서 위의 예제에서는, `email` 컬럼을 통해서 사용자를 찾게됩니다. 사용자를 찾았다면, 해시 처리되어 데이터베이스에 저장된 패스워드와 메소드에 전달받은 배열의 `password` 값을 비교할 것입니다. 프레임워크가 데이터베이스에서 해시 처리된 암호와 비교하기 전에 값을 자동으로 해시처리 하기 때문에, `password` 로 지정된 값을 해시처리해서는 안됩니다. 두개의 해시처리된 패스워드가 일치한다면 해당 사용자의 새로운 인증 세션이 시작됩니다.

`attempt` 메소드는 인증이 성공하면 `true` 를 반환합니다. 실패 시 `false` 를 반환합니다.

리다이렉터의 `intended` 메소드는 사용자가 인증 필터에 잡히기 전에 원래 엑세스 하려고 시도했었던 URL로 사용자를 리다이렉트 시킵니다. 해당 리다이렉트가 불가능한 경우 주어진 대체 URI를 메소드에 지정할 수 있습니다.

#### 추가적인 조건 지정하기

원한다면, 사용자의 이메일과 패스워드 외에도 부가적인 조건들을 인증 쿼리에 추가할 수 있습니다. 예를 들어 사용자가 "활성화"로 표시되어있는지 확인할 수 있습니다.

    if (Auth::attempt(['email' => $email, 'password' => $password, 'active' => 1])) {
        // The user is active, not suspended, and exists.
    }

> {note} 이 예제에서, `email` 은 필수 옵션이 아니라, 그냥 예제에서 사용된것입니다. 여러분은 데이터베이스 안에 있는 "username"과 일치하는 어떠한 컬럼 이름을 사용해야 합니다.

#### 지정된 Guard 인스턴스에 엑세스하기

여러분은 `Auth` 파사드에 `guard` 메소드를 사용하여 어떤 guard 인스턴스를 사용하고자 하는지 지정할 수 있습니다. 이것은 여러분이 완전히 분리된 인증 모델 또는 사용자 테이블을 사용하여 애플리케이션의 개별 파트에 대한 인증을 관리할 수 있도록 합니다.

`guard` 메소드에 전달되는 guard의 이름은 `auth.php` 설정 파일에 설정된 guard 중 하나와 일치 해야합니다.

    if (Auth::guard('admin')->attempt($credentials)) {
        //
    }

#### 로그아웃

애플리케이션에서 사용자를 로그아웃 시키려면 `Auth` 파사드의 `logout` 메소드를 사용하면 됩니다. 그러면 사용자 세션에서 인증 정보가 제거될 것입니다.

    Auth::logout();

<a name="remembering-users"></a>
### 사용자 기억하기

여러분의 애플리케이션에 "기억하기" 기능을 제공하고자 한다면, `attempt` 메소드의 두번째 인자로 사용자의 인증을 무기한 계속 유지할지, 아니면 수동으로 로그아웃 할때까지 유지할지 결정하는 boolean 값을 전달하면 됩니다. 이를 위해서는 `users` 테이블은 "기억하기" 토근을 저장하는데 사용되는 `remember_token` 컬럼을 가지고 있어야만 합니다.

    if (Auth::attempt(['email' => $email, 'password' => $password], $remember)) {
        // The user is being remembered...
    }

> {tip} Laravel에 기본 제공되는 `LoginController`를 사용하는 경우, "remember"하는 사용자를 처리하는 적절한 로직은 이미 컨트롤러에서 사용하는 트레이트-trait에 구현되어 있습니다.

여러분이 "remember"하는 사용자라면, `viaRemember` 메소드를 통해 해당 사용자가 "remember me" 쿠키로 인증이 되었는 지 확인 할 수 있습니다.

    if (Auth::viaRemember()) {
        //
    }

<a name="other-authentication-methods"></a>
### 그 외 인증 메소드

#### 사용자 인스턴스를 통해서 인증하기

애플리케이션에 이미 존재하는 사용자 인스턴스를 통해서 로그인을 하려면 사용자 인스턴스의 `login` 메소드를 호출할 수 있습니다. 주어진 객체는 `Illuminate\Contracts\Auth\Authenticatable` [contract](/docs/{{version}}/contracts)를 구현해야 합니다. 라라벨에 포함된 `App\User` 모델은 이미 이 인터페이스를 구현합니다.

    Auth::login($user);

    // Login and "remember" the given user...
    Auth::login($user, true);

사용하고자 하는 가드 인스턴스를 지정할 수도 있습니다.

    Auth::guard('admin')->login($user);

#### ID를 통해서 사용자 인증하기

사용자를 ID를 통해 애플리케이션에 로그인 시키려면, `loginUsingId` 메소드를 사용하면 됩니다. 이 메소드는 인증하고자 하는 사용자의 프라이머리 키를 전달 받습니다.

    Auth::loginUsingId(1);

    // Login and "remember" the given user...
    Auth::loginUsingId(1, true);

#### 사용자 인증 한 번 하기

하나의 request에 대해서 `once` 메소드로 사용자를 로그인시킬 수 있습니다. 세션과 쿠키는 활용되지 않을 것이며 이는 상태를 유지하지 않는 API를 만드는데 도움이 됩니다.

    if (Auth::once($credentials)) {
        //
    }

<a name="http-basic-authentication"></a>
## HTTP 기본 인증

[HTTP 기본 인증](https://en.wikipedia.org/wiki/Basic_access_authentication)은 애플리케이션에 별도의 "login" 페이지 설정없이도 사용자 인증을 할 수 있는 손쉬운 방법을 제공합니다. 이를 위해서는 `auth.basic` [미들웨어](/docs/{{version}}/middleware)를 라우트에 추가하면 됩니다. `auth.basic` 미들웨어는 라라벨에 포함되어 있기 때문에 따로 정의할 필요가 없습니다.

    Route::get('profile', function () {
        // Only authenticated users may enter...
    })->middleware('auth.basic');

미들웨어가 라우트에 추가되면 브라우저에서 라우트에 접근할 때 자동으로 인증 자격을 증명할 것을 요구받을 것입니다. `auth.basic` 미들웨어는 기본적으로 사용자 레코드의 `email` 컬럼을 "username"으로 사용합니다.

#### FastCGI에 대한 참고사항

PHP FastCGI를 사용하는 경우, HTTP 기본 인증이 제대로 작동하지 않을 것입니다. 다음을 `.htaccess` 파일에 추가하십시오.

    RewriteCond %{HTTP:Authorization} ^(.+)$
    RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

<a name="stateless-http-basic-authentication"></a>
### 상태를 유지하지 않는 HTTP 기본 인증

세션에서 사용자 식별자 쿠키를 사용하지 않고 HTTP 기본 인증을 사용할 수도 있습니다. 이것은 API 인증에 특히 유용합니다. 그럴려면 `onceBasic` 메소드를 호출하는 [미들웨어](/docs/{{version}}/middleware)를 정의합니다. `onceBasic`메소드가 응답을 리턴하지 않으면 요청-request가 애플리케이션으로 안쪽으로 전달 될 수 있습니다.

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

다음으로, [라우트 미들웨어를 등록하고](/docs/{{version}}/middleware#registering-middleware) 이를 라우트에 추가하십시오.

    Route::get('api/user', function () {
        // Only authenticated users may enter...
    })->middleware('auth.basic.once');

<a name="logging-out"></a>
## 로그아웃

애플리케이션에서 사용자를 로그아웃 시키려면, `Auth` 파사드의 `logout` 메소드를 사용하면 됩니다. 이 메소드는 사용자의 세션에서 인증 정보를 삭제할 것입니다.

    use Illuminate\Support\Facades\Auth;

    Auth::logout();

<a name="invalidating-sessions-on-other-devices"></a>
### 다른 디바이스의 세션 무효화

라라벨은 현재 접속한 디바이스의 세션은 유지하면서 다른 디바이스의 사용자 세션을 무효화하고 "로그아웃" 시키는 기능을 제공합니다. 이를 위해서 `app/Http/Kernel.php` 클래스의 `web` 미들웨어 그룹의 코멘트가 해제되었는지 확인하십시오.

    'web' => [
        // ...
        \Illuminate\Session\Middleware\AuthenticateSession::class,
        // ...
    ],

이렇게 하면, `Auth` 파사드의 `logoutOtherDevices` 메소드를 사용할 수 있습니다. 이 메소드를 사용하려면 사용자가 입력폼을 통해서 패스워드를 입력하도록 해야합니다.

    use Illuminate\Support\Facades\Auth;

    Auth::logoutOtherDevices($password);

> {note} `logoutOtherDevices` 메소드가 호출될 때, 사용자의 다른 세션은 완전히 무효화됩니다. 이는, 이전에 인증되었던 모든 사용자 정보가 "로그아웃"됨을 의미합니다.

<a name="adding-custom-guards"></a>
## 사용자정의 Guard 추가

여러분은 `Auth` 파사드의 `extend` 메소드를 사용하여 고유한 인증 guard를 정의할 수 있습니다. 이 메소드는 [서비스 프로바이더](/docs/{{version}}/providers) 중 하나의 `extend`에서 호출해야 합니다. 라라벨은 이미 `AuthServiceProvider`를 통해 제공하고 있으므로, 우린 그 프로바이더 안에 코드만 넣으면 됩니다.

    <?php

    namespace App\Providers;

    use App\Services\Auth\JwtGuard;
    use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;
    use Illuminate\Support\Facades\Auth;

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

이 예제에서 볼 수 있듯이, `extend` 메소드에 전달된 콜백은 `Illuminate\Contracts\Auth\Guard` 구현체를 반환해야 합니다. 이 인터페이스는 사용자 정의 guard를 구현하는데 필요한 몇가지 메소드를 가지고 있습니다. 사용자 정의 guard를 정의하고나면, `guards` 설정에서 정의한 guard 를 사용할 수 있습니다.

    'guards' => [
        'api' => [
            'driver' => 'jwt',
            'provider' => 'users',
        ],
    ],

<a name="closure-request-guards"></a>
### 클로저 형태의 Request Guards

인증 시스템을 기반으로한 커스텀 HTTP request-요청을 구현하는 가장간단한 방법은 `Auth::viaRequest` 메소드를 사용하는 것 입니다. 이 메소드는 하나의 클로저를 사용하여 빠르기 인증을 정의할 수 있게 해줍니다.

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

커스텀 인증 드라이버 를 정의했다면, `auth.php` 설정 파일의 `guards` 설정 안에서 사용할 수 있습니다.

    'guards' => [
        'api' => [
            'driver' => 'custom-token',
        ],
    ],

<a name="adding-custom-user-providers"></a>
## 사용자 정의 User 프로바이더 추가하기

사용자들을 저장하는 데 전통적인 관계형 데이터베이스를 이용하지 않는다면 사용자 정의 User 프로바이더를 통해 라라벨을 확장해야 합니다. 우린 사용자 정의 User 프로바이더를 정의하기 위해 `Auth` 파사드의 `provider` 메소드를 사용할 것입니다.

    <?php

    namespace App\Providers;

    use App\Extensions\RiakUserProvider;
    use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;
    use Illuminate\Support\Facades\Auth;

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

`provider` 메소드로 프로바이더를 등록한 뒤, `auth.php` 설정 파일에서 새 User 프로바이더로 변경할 수 있습니다. 먼저 새로운 드라이버를 사용하는 `provider`를 정의합시다.

    'providers' => [
        'users' => [
            'driver' => 'riak',
        ],
    ],

마지막으로, `guards` 설정 에서 이 프로바이더를 사용할 수 있습니다.

    'guards' => [
        'web' => [
            'driver' => 'session',
            'provider' => 'users',
        ],
    ],

<a name="the-user-provider-contract"></a>
### 사용자 프로바이더 Contract

`Illuminate\Contracts\Auth\UserProvider`의 구현체는 MySQL, Riak 등의 지속적인 스토리지 시스템에서 `Illuminate\Contracts\Auth\Authenticatable`을 불러오는 것만 담당합니다. 이 두 인터페이스는 사용자 데이터가 어떻게 저장되었는지 또는 이를 위해 어떤 클래스가 사용되고 있는지 여부에 상관없이 라라벨 인증 메커니즘이 작동하도록 해줍니다.

`Illuminate\Contracts\Auth\UserProvider` contract를 살펴보겠습니다.

    <?php

    namespace Illuminate\Contracts\Auth;

    interface UserProvider
    {
        public function retrieveById($identifier);
        public function retrieveByToken($identifier, $token);
        public function updateRememberToken(Authenticatable $user, $token);
        public function retrieveByCredentials(array $credentials);
        public function validateCredentials(Authenticatable $user, array $credentials);
    }

`retrieveById` 함수는 일반적으로 MySQL 데이터베이스의 자동 증가 ID와 같은 사용자를 대표하는 키를 전달 받습니다. ID에 상응하는 `Authenticatable` 구현체가 조회되고 그 메소드를 통해 반환됩니다.

`retrieveByToken`와 `remember_token`에 저장된 "remember me" `$token`에 따라 사용자를 조회합니다. 이전의 메소드와 같이 `Authenticatable` 구현체가 반환됩니다.

`updateRememberToken` 메소드는 새로운 `$token`으로 `$user`의 `remember_token`필드를 업데이트합니다. 새로운 토큰값은 정상적으로 "remember me"을 통해서 로그인을 시도하기 위해서 할당된 값이거나, 사용자가 로그아웃되었을 때를 위한 값일 수 있습니다.

`retrieveByCredentials` 메소드는 애플리케이션에 로그인하려고 시도할 때 `Auth::attempt` 메소드로 전달되는 인증 정보 배열을 전달 받습니다. 그러면 메소드는 그 인증 정보에 맞는 사용자들을 기반에 있는 지속적 저장소(persistent storage)에서 "질의"해야 합니다. 일반적으로 이 메소드는 `$credentials['username']`에 맞는 "where" 쿼리를 실행할 것입니다. 그 뒤에 메소드는 `Authenticatable`의 구현체를 반환해야 합니다. **이 메소드는 패스워드 확인이나 인증을 시도해서는 안됩니다.**

`validateCredentials` 메소드는 사용자를 인증하기 위하여 주어진 `$user`를 `$credentials`에 비교해야 합니다. 예를 들자면, 이 메소드는 `$user->getAuthPassword()`의 값을 `$credentials['password']`와 비교하기 위해 `Hash::check`를 사용하게 될 것 입니다. 이 메소드는 패스워드가 유효한 지를 나타내는 `true` 혹은 `false` 값을 반환합니다.

<a name="the-authenticatable-contract"></a>
### Authenticatable Contract 살펴보기

`UserProvider`의 각 메소드에 대해 알아보았으니 이제 `Authenticatable` contract를 살펴 보도록 하겠습니다. 프로바이더는 `retrieveById`, `retrieveByToken`, 그리고 `retrieveByCredentials` 메소드에서 이 인터페이스의 구현을 반환해야 한다는 것을 기억하십시오..

    <?php

    namespace Illuminate\Contracts\Auth;

    interface Authenticatable
    {
        public function getAuthIdentifierName();
        public function getAuthIdentifier();
        public function getAuthPassword();
        public function getRememberToken();
        public function setRememberToken($value);
        public function getRememberTokenName();
    }

이 인터페이스는 간단합니다. `getAuthIdentifierName` 메소드는 사용자의 "프라이머리 키" 필드의 이름을 반환해야하며 `getAuthIdentifier` 메소드는 사용자의 "프라이머리 키"를 반환합니다. MySQL의 경우 auto-incrementing primary key에 해당합니다. `getAuthPassword`는 사용자의 해시된 패스워드를 반환합니다. 이 인터페이스는 어떤 ORM 혹은 저장소 추상화 계층을 사용하든지에 상관없이 인증 시스템이 어떤 사용자 클래스에도 적용 될수 있도록 해줍니다. 라라벨은 기본적으로 이 인터페이스를 구현하는 `app` 디렉토리에 `User` 클래스를 포함하기 때문에 구현된 예를 보기 위해 이 클래스를 이용면 됩니다.

<a name="events"></a>
## 이벤트

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
