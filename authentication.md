# 인증

- [소개](#introduction)
    - [데이터베이스 유의사항](#introduction-database-considerations)
- [빠르게 인증 살펴보기](#authentication-quickstart)
    - [Routing-라우팅](#included-routing)
    - [Views-뷰](#included-views)
    - [Authenticating](#included-authenticating)
    - [인증된 사용자 획득하기](#retrieving-the-authenticated-user)
    - [라우트 제한하기](#protecting-routes)
    - [인증 시도 횟수 제한](#authentication-throttling)
- [수동으로 사용자 인증하기](#authenticating-users)
    - [사용자 기억하기](#remembering-users)
    - [그 외 인증 메소드](#other-authentication-methods)
- [HTTP 기본 인증](#http-basic-authentication)
    - [상태를 유지하지 않는 HTTP 기본 인증](#stateless-http-basic-authentication)
- [패스워드 재설정](#resetting-passwords)
    - [데이터베이스 고려사항](#resetting-database)
    - [Routing](#resetting-routing)
    - [Views](#resetting-views)
    - [패스워드 재설정 후](#after-resetting-passwords)
- [소셜 인증](#social-authentication)
- [사용자 정의 인증 드라이버 추가하기](#adding-custom-authentication-drivers)
- [이벤트](#events)

<a name="introduction"></a>
## 소개

라라벨은 인증기능을 구현하는 것을 매우 쉽게 해줍니다. 기본적으로 별도의 설정 없이도 대부분이 이미 준비되어 있습니다. 인증에 대한 설정 파일은 `config/auth.php` 으로 인증 서비스의 동작을 제어할 수 있는 옵션들이 자세한 설명과 함께 제공됩니다.

<a name="introduction-database-considerations"></a>
### 데이터베이스 유의사항

기본적으로 라라벨은 `app` 디렉토리에 `App\User` 모델을 포함하고 있습니다. 이 모델은 기본적인 Eloquent 인증 드라이버와 함께 사용하게 됩니다. 어플리케이션이 Eloquent를 사용하고 있지 않다면 라라벨 쿼리 빌더를 사용하는 `database` 인증 드라이버를 이용하면 됩니다. 

`App\User` 모델을 위한 데이터베이스 스키마를 구성할 때, 유의할 것은 패스워드 컬럼이 최소 60 자가 되어야 한다는 것입니다. 

또한 `users` (또는 동일한) 테이블이 NULL을 허용하는 100자리 문자열의 `remember_token` 컬럼을 포함하고 있는지 확인하십시오. 이 컬럼은 어플리케이션에서 관리하는 "remember me" 세션의 토큰을 저장하는데 사용됩니다.  마이그레이션에서 $table->rememberToken();를 사용하면 추가 할 수 있습니다. 

<a name="authentication-quickstart"></a>
## 빠르게 인증 살펴보기

라라벨은 별도의 설정 없이도 `App\Http\Controllers\Auth` 네임스페이스에 위치한 두가지 인증 컨트롤러를 제공하고 있습니다. `AuthController`는 새로운 사용자의 등록과 로그인을 처리하고 `PasswordController`는 암호 분실시에 사용자 암호 재설정을 처리합니다. 각각의 컨트롤러는 필요한 메서드를 포함하기 위해 트레이트-trait를 사용합니다. 대부분의 어플리케이션에서 여러분은 이 컨트롤러들을 수정할 필요가 없을 것입니다. 

<a name="included-routing"></a>
### Routing-라우팅

기본적으로, 인증 컨트롤러를 향한 request을 가리키는 [라우트](/docs/{{version}}/routing)는 포함되어 있지 않습니다. 여러분은 `app/Http/routes.php` 파일에 수동으로 라우트들을 추가할 수 있습니다. 

    // Authentication routes...
    Route::get('auth/login', 'Auth\AuthController@getLogin');
    Route::post('auth/login', 'Auth\AuthController@postLogin');
    Route::get('auth/logout', 'Auth\AuthController@getLogout');

    // Registration routes...
    Route::get('auth/register', 'Auth\AuthController@getRegister');
    Route::post('auth/register', 'Auth\AuthController@postRegister');

<a name="included-views"></a>
### Views-뷰

프레임워크에 인증 컨트롤러를 포함되어 있긴 하지만 이 컨트롤러가 렌더링할 수 있는 [views-뷰](/docs/{{version}}/views)를 제공할 필요가 있습니다. 뷰는 `resources/views/auth` 디렉토리에 위치해야 합니다. 이 뷰들은 마음대로 수정할 수 있습니다. 로그인 뷰는 `resources/views/auth/login.blade.php`에 위치해야 하며 회원 가입 뷰는 `resources/views/auth/register.blade.php`에 위치해야 합니다. 

#### 인증 Form 샘플

    <!-- resources/views/auth/login.blade.php -->

    <form method="POST" action="/auth/login">
        {!! csrf_field() !!}

        <div>
            Email
            <input type="email" name="email" value="{{ old('email') }}">
        </div>

        <div>
            Password
            <input type="password" name="password" id="password">
        </div>

        <div>
            <input type="checkbox" name="remember"> Remember Me
        </div>

        <div>
            <button type="submit">Login</button>
        </div>
    </form>

#### 회원가입 Form 샘플

    <!-- resources/views/auth/register.blade.php -->

    <form method="POST" action="/auth/register">
        {!! csrf_field() !!}

        <div>
            Name
            <input type="text" name="name" value="{{ old('name') }}">
        </div>

        <div>
            Email
            <input type="email" name="email" value="{{ old('email') }}">
        </div>

        <div>
            Password
            <input type="password" name="password">
        </div>

        <div>
            Confirm Password
            <input type="password" name="password_confirmation">
        </div>

        <div>
            <button type="submit">Register</button>
        </div>
    </form>

<a name="included-authenticating"></a>
### 인증하기

이제 인증 컨트롤러에 대한 라우트와 뷰가 설정되었으니, 어플리케이션에 새로운 사용자를 등록하거나 인증할 준비가 되었습니다. 간단하게는 브라우저에서 정의된 라우트를 접근할 수 있습니다. 인증 컨트롤러는 존재하는 사용자를 인증하고 새로운 사용자를 데이터베이스에 저장하는 이미 로직을(트레이트-trait를 통해) 가지고 있습니다. 

사용자가 인증에 성공하면 `/home` URI로 리다이렉트될 것이며 이 URI는 라우트를 등록해야 이용할 수 있습니다. `AuthController`에 `redirectPath` 속성을 정의하면 사용자가 인증 후 옮겨지는 위치를 변경할 수 있습니다: 

    protected $redirectPath = '/dashboard';

사용자가 인증에 성공하지 못한다면 `/auth/login` URI로 리다이렉트 될것입니다. `AuthController`에 `loginPath` 속성을 정의하면 인증 실패 후 이동되는 위치를 변경할 수 있습니다: 

    protected $loginPath = '/login';

사용자가 보호된 라우트에 접근하려다 튕겨져도 `loginPath`는 변하지 않을 것입니다. 이는 `App\Http\Middleware\Authenticate` 미들웨어의 `handle` 메소드가 조정합니다. 

#### 사용자에 의한 수정

새로운 사용자를 등록하는 폼의 필수 입력 항목들을 변경 하고자 하거나, 또는 새로운 레코드가 데이터베이스에 입력되는 방법을 변경하고자 한다면, `AuthController` 클래스를 수정하면 됩니다. 이 클래스는 어플리케이션에서 새로운 사용자를 검증하고 생성하는 역할을 합니다.

`AuthController` 클래스의 `validator` 메소드는 어플리케이션의 새로운 사용자에 대한 폼 검증 규칙을 포함하고 있으며, 여러분은 원하는대로 이 메소드들을 수정해도 됩니다. 

`AuthController`의 `create` 메소드는 [Eloquent ORM](/docs/{{version}}/eloquent)을 이용하여 데이터베이스에 새 `App\User` 기록을 생성합니다. 데이터베이스의 필요에 따라 이 메소드를 수정할 수 있습니다. 

<a name="retrieving-the-authenticated-user"></a>
### 승인된 사용자 조회하기

`Auth` 파사드를 통해 인증된 사용자에게 접근할 수 있습니다: 

    $user = Auth::user();

대체적인 방법으로는, 사용자가 승인된 후 `Illuminate\Http\Request` 인스턴스를 통해 승인된 사용자에게 접근하는 방법이 있습니다. 

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;
    use Illuminate\Routing\Controller;

    class ProfileController extends Controller
    {
        /**
         * Update the user's profile.
         *
         * @param  Request  $request
         * @return Response
         */
        public function updateProfile(Request $request)
        {
            if ($request->user()) {
                // $request->user() returns an instance of the authenticated user...
            }
        }
    }

#### 현재 사용자의 승인 여부 결정하기 

사용자가 이미 어플리케이션에 로그인했는지 판별하려면, `Auth` 파사드에 `check` 메소드를 사용할 수 있습니다. 사용자가 인증되었다면 `true`를 반환합니다: 

    if (Auth::check()) {
        // The user is logged in...
    }

이와 다르게, 특정 라우트 및 컨트롤러에 사용자가 엑세스 하기 전에, 사용자가 인증되었는지 확인하기 위해서 미들웨어를 사용할 수 있습니다. 이 방법에 대해 더 알아보려면 [라우트 보호하기](/docs/{{version}}/authentication#protecting-routes) 문서를 확인하십시오. 

<a name="protecting-routes"></a>
### 라우트 보호하기

[라우트 미들웨어](/docs/{{version}}/middleware)는 인증된 사용자에게만 주어진 라우트에 접근하는 것을 허용도록 할 수 있습니다. 라라벨은 `app\Http\Middleware\Authenticate.php`에 정의된 `auth` 미들웨어를 제공하고 있습니다. 이제 할일은 라우트가 정의된 부분에 미들웨어를 추가하면 됩니다: 

    // Using A Route Closure...

    Route::get('profile', ['middleware' => 'auth', function() {
        // Only authenticated users may enter...
    }]);

    // Using A Controller...

    Route::get('profile', [
        'middleware' => 'auth',
        'uses' => 'ProfileController@show'
    ]);

물론 [컨트롤러 클래스](/docs/{{version}}/controllers)를 사용하고 있다면, 라우트 정의부분에 직접 추가하는 것 대신 컨트롤러의 생성자에서 `middleware` 메소드를 호출할 수 있습니다: 

    public function __construct()
    {
        $this->middleware('auth');
    }

<a name="authentication-throttling"></a>
### 인증 시도 횟수 제한

만약 라라벨에 포함된 `AuthController` 클래스를 사용한다면 `Illuminate\Foundation\Auth\ThrottlesLogins` 트레이트-trait을 사용하여 로그인 시도를 제한할 수 있습니다. 기본적으로, 사용자가 여러 번에 걸쳐 정확한 정보를 입력하지 못한다면, 1분 동안 로그인하지 못하도록 설정되어 있습니다. 제한은 사용자의 사용자 이름 / 이메일 주소와 IP 주소를 고유하게 취급합니다.

    <?php

    namespace App\Http\Controllers\Auth;

    use App\User;
    use Validator;
    use App\Http\Controllers\Controller;
    use Illuminate\Foundation\Auth\ThrottlesLogins;
    use Illuminate\Foundation\Auth\AuthenticatesAndRegistersUsers;

    class AuthController extends Controller
    {
        use AuthenticatesAndRegistersUsers, ThrottlesLogins;

        // Rest of AuthController class...
    }

<a name="authenticating-users"></a>
## 수동으로 사용자 인증하기 

물론 라라벨에 포함된 인증 컨트롤러를 꼭 써야하지는 않습니다. 이 컨트롤러들을 삭제한다면 라라벨의 인증 클래스를 사용하여 사용자 인증을 직접 관리해야 합니다. 아주 쉬우니까 걱정하지 마세요!

라라벨의 인증 서비스를 `Auth` [파사드](/docs/{{version}}/facades)를 통해 접근할 것이기 때문에 클래스의 가장 상단 부분에 `Auth` 파사드를 사용하도록 선언하는 것이 필수입니다. 다음은 `attempt` 메소드를 확인해 보겠습니다:

    <?php

    namespace App\Http\Controllers;

    use Auth;
    use Illuminate\Routing\Controller;

    class AuthController extends Controller
    {
        /**
         * Handle an authentication attempt.
         *
         * @return Response
         */
        public function authenticate()
        {
            if (Auth::attempt(['email' => $email, 'password' => $password])) {
                // Authentication passed...
                return redirect()->intended('dashboard');
            }
        }
    }

`attempt` 메소드는 키 / 값의 쌍으로 이루어진 배열을 첫번째 인자로 전달 받습니다. 배열의 값들은 데이터베이스 테이블에서 사용자를 찾는데 사용될 것입니다. 따라서 위의 예제에서는, `email` 컬럼을 통해서 사용자를 찾게됩니다. 사용자를 찾았다면, 해시처리되어 데이터베이스에 저장된 패스워드와 매소드에 전달받은 배열의 해시처리된 `password` 값을 비교할 것입니다. 두개의 해시처리된 패스워드가 일치한다면 해당 사용자의 새로운 인증 세션이 시작됩니다. 

`attempt` 메소드는 인증이 성공하면 `true` 를 반환합니다. 실패시 `false` 를 반환합니다.

리다이렉터의 `intended` 메소드는 사용자가 인증 필터에 잡히기 전에 원래 엑세스 하려고 시도했었던 URL로 사용자를 리다이렉트 시킵니다. 해당 리다이렉트가 불가능한 경우 주어진 대체 URI를 메소드에 지정할 수 있습니다. 

원한다면, 사용자의 이메일과 패스워드 외에도 부가적인 조건들을 인증 쿼리에 추가할 수 있습니다. 예를 들어 사용자가 "활성화"로 표시되어있는지 확인할 수 있습니다: 

    if (Auth::attempt(['email' => $email, 'password' => $password, 'active' => 1])) {
        // The user is active, not suspended, and exists.
    }

어플리케이션에서 사용자를 로그아웃 시키려면 `Auth` 파사드의 `logout` 메소드를 사용하면 됩니다. 그러면 사용자 세션에서 인증 정보가 제거될 것입니다: 

    Auth::logout();

> **주의:** 이 예제에서 `email` 은 필수 옵션이 아니며 단지 예제로 사용되었습니다. 여러분은 데이터베이스에서 "username"에 해당하는 어떠한 컬럼이라도 사용할 수 있습니다. 

<a name="remembering-users"></a>
### 사용자 기억하기

여러분의 어플리케이션에 "기억하기" 기능을 제공하고자 한다면, `attempt` 메소드의 두번째 인자로 사용자의 인증을 무기한 계속 유지할지, 아니면 수동으로 로그아웃 할때까지 유지할지 결정하는 boolean 값을 전달하면 됩니다. 이를 위해서는 `users` 테이블은 "기억하기" 토근을 저장하는데 사용되는 `remember_token` 컬럼을 가지고 있어야만 합니다. 

    if (Auth::attempt(['email' => $email, 'password' => $password], $remember)) {
        // The user is being remembered...
    }

사용자들을 "기억" 해내는 중이라면, `viaRemember` 메소드를 사용하여 해당 사용자가 "기억 하기" 쿠키를 사용하여 인증이 되었는지 확인 할 수 있습니다:

    if (Auth::viaRemember()) {
        //
    }

<a name="other-authentication-methods"></a>
### 그 외 인증 메소드

#### 사용자 인스턴스를 통해서 인증하기

애플리케이션에 이미 존재하는 사용자 인스턴스를 통해서 로그인을 하려면 사용자 인스턴스의 `login` 메소드를 호출할 수 있습니다. 주어진 객체는 `Illuminate\Contracts\Auth\Authenticatable` [contract](/docs/{{version}}/contracts)를 구현해야 합니다. 물론 라라벨에 포함된 `App\User` 모델은 이미 이 인터페이스를 구현합니다: 

    Auth::login($user);

#### Id를 기반으로 사용자 인증하기

사용자 ID를 통하여 사용자를 어플리케이션에 로그인 시키려면, `loginUsingId` 메소드를 사용하면 됩니다. 이 메소드는 단순히 인증하고자 하는 사용자의 프라이머리 키를 전달 받습니다: 

    Auth::loginUsingId(1);

#### 사용자 인증 한 번 하기 

하나의 request에 대해서 `once` 메소드로 사용자를 로그인시킬 수 있습니다. 세션과 쿠키는 활용되지 않을 것이며 이는 상태를 유지하지 않는 API를 만드는데 도움이 됩니다. `once` 메소드의 서명은 `attempt` 메소드의 서명과 같습니다: 

    if (Auth::once($credentials)) {
        //
    }

<a name="http-basic-authentication"></a>
## HTTP 기본 인증

[HTTP 기본 인증](http://en.wikipedia.org/wiki/Basic_access_authentication)은 어플리케이션에 별도의 "login" 페이지 설정없이도 사용자 인증을 할 수 있는 손쉬운 방법을 제공합니다. 이를 위해서는 `auth.basic` [미들웨어](/docs/{{version}}/middleware)를 라우트에 추가하면 됩니다. `auth.basic` 미들웨어는 라라벨에 포함되어 있기 때문에 따로 정의할 필요가 없습니다: 

    Route::get('profile', ['middleware' => 'auth.basic', function() {
        // Only authenticated users may enter...
    }]);

미들웨어가 라우트에 추가되면 브라우저에서 라우트에 접근할 때 자동으로 인증 자격을 증명할 것을 요구받을 것입니다. `auth.basic` 미들웨어는 기본적으로 사용자 레코드의 `email` 컬럼을 "사용자 이름"으로 사용합니다. 

#### FastCGI에 대한 참고사항

PHP FastCGI를 사용하는 경우, HTTP 기본 인증이 제대로 작동하지 않을 것입니다. 다음 라인들을 `.htaccess` 파일에 추가하십시오:

    RewriteCond %{HTTP:Authorization} ^(.+)$
    RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

<a name="stateless-http-basic-authentication"></a>
### 상태를 유지하지 않는 HTTP 기본 인증

여러분은 또한 API 인증에 유용한, 세션의 사용자 식별 쿠키가 없는 HTTP 기본 인증을 사용할 수도 있습니다. 이렇게 하기 위해서는 `onceBasic` 메소드를 호출하는 [미들웨어](/docs/{{version}}/middleware)를 정의하면 됩니다. `onceBasic` 메소드가 아무런 응답을 반환하지 않는다면 이 요청은 애플리케이션 안에서 계속 진행될 것입니다:

    <?php

    namespace Illuminate\Auth\Middleware;

    use Auth;
    use Closure;

    class AuthenticateOnceWithBasicAuth
    {
        /**
         * Handle an incoming request.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  \Closure  $next
         * @return mixed
         */
        public function handle($request, Closure $next)
        {
            return Auth::onceBasic() ?: $next($request);
        }

    }

다음으로, [라우트 미들웨어를 등록하고](/docs/{{version}}/middleware#registering-middleware) 라우트에 추가하십시오:

    Route::get('api/user', ['middleware' => 'auth.basic.once', function() {
        // Only authenticated users may enter...
    }]);

<a name="resetting-passwords"></a>
## 패스워드 재설정

<a name="resetting-database"></a>
### 데이터베이스 고려사항

대부분의 웹 어플리케이션은 사용자가 잊어버린 패스워드를 재설정 하는 방법을 제공합니다. 라라벨은 각각의 어플리케이션에서 직접 이 기능을 다시 구현하는 하도록 하기 보다, 패스워드 알림을 보내고 패스워드를 재설정을 수행하는 편리한 메소드를 제공합니다.

이를 이용하려면 `App\User` 모델이 `Illuminate\Contracts\Auth\CanResetPassword` contract을 구현하고 있는지 확인하십시오. 물론 프레임워크에 포함된 `App\User` 모델은 이미 이 인터페이스를 구현하고 있으며, 인터페이스 구현에 필요한 메소드를 포함한 `Illuminate\Auth\Passwords\CanResetPassword` 트레이트-trait을 사용하고 있습니다.

#### 재설정 토큰을 저장하는 테이블 마이그레이션 생성하기 

다음으로 패스워드 리셋 토큰을 저장하기 위한 테이블을 생성해야합니다. 이 테이블의 마이그레이션은 별다른 설정 없이도 라라벨에 기본적으로 포함되어 있으며 `database/migrations` 디렉토리에 존재합니다. 따라서 다음처럼 마이그레이션을 실행하면 됩니다:

    php artisan migrate

<a name="resetting-routing"></a>
### ROuting-라우팅

라라벨은 사용자 패스워드 재설정에 필요한 로직을 포함하는 `Auth\PasswordController`를 가지고 있습니다. 하지만 request-요청을 이 컨트롤러로 향하도록 하려면 라우트를 정의해야 합니다: 

    // Password reset link request routes...
    Route::get('password/email', 'Auth\PasswordController@getEmail');
    Route::post('password/email', 'Auth\PasswordController@postEmail');

    // Password reset routes...
    Route::get('password/reset/{token}', 'Auth\PasswordController@getReset');
    Route::post('password/reset', 'Auth\PasswordController@postReset');

<a name="resetting-views"></a>
### Views-뷰

`PasswordController`를 위해 라우트를 정의하는 것 외에도 컨트롤러가 반환할 뷰를 제공해야 합니다. 걱정하지 마십시오. 도움을 드리기 위하여 샘플 뷰들을 제공할 것입니다. 물론 이 형식들은 원하는대로 스타일을 변경 할 수 있습니다. 

#### 패스워드 재설정 링크를 요청하는 Form 샘플

패스워드의 재설정을 요청하는 Form을 위해서 HTML 뷰를 제공해야 합니다. 이 뷰는 `resources/views/auth/password.blade.php`에 위치할 것입니다. 이 Form은 사용자의 이메일 주소 입력을 위한 하나의 필드를 제공하여 패스워드 재설정 링크를 요청할 수 있도록 합니다:

    <!-- resources/views/auth/password.blade.php -->

    <form method="POST" action="/password/email">
        {!! csrf_field() !!}

        @if (count($errors) > 0)
            <ul>
                @foreach ($errors->all() as $error)
                    <li>{{ $error }}</li>
                @endforeach
            </ul>
        @endif

        <div>
            Email
            <input type="email" name="email" value="{{ old('email') }}">
        </div>

        <div>
            <button type="submit">
                Send Password Reset Link
            </button>
        </div>
    </form>

사용자가 패스워드 재설정을 요청한다면(submit) `PasswordController`의 `getReset` 메소드(일반적으로 `/password/reset`에 라우트된)에 해당하는 링크가 포함된 이메일을 받게 됩니다. `resources/views/emails/password.blade.php`에서 이 이메일을 위한 뷰 파일을 생성해야 합니다. 이 뷰는 사용자를 패스워드 재설정 요청과 대응시킬 패스워드 재설정 토큰을 포함하는 `$token` 변수를 전달 받습니다. 다음은 이메일 뷰의 예시입니다: 

    <!-- resources/views/emails/password.blade.php -->

    Click here to reset your password: {{ url('http://example.com/password/reset/'.$token) }}

#### 패스워드 재설정 Form 샘플

사용자가 이메일의 패스워드 재설정 링크를 클릭하면 패스워드 재설정 Form이 나타납니다. 이 뷰는 `resources/views/auth/reset.blade.php`에 위치해야 합니다. 

다음은 패스워드 재설정 형식의 예시입니다: 

    <!-- resources/views/auth/reset.blade.php -->

    <form method="POST" action="/password/reset">
        {!! csrf_field() !!}
        <input type="hidden" name="token" value="{{ $token }}">

        @if (count($errors) > 0)
            <ul>
                @foreach ($errors->all() as $error)
                    <li>{{ $error }}</li>
                @endforeach
            </ul>
        @endif

        <div>
            Email
            <input type="email" name="email" value="{{ old('email') }}">
        </div>

        <div>
            Password
            <input type="password" name="password">
        </div>

        <div>
            Confirm Password
            <input type="password" name="password_confirmation">
        </div>

        <div>
            <button type="submit">
                Reset Password
            </button>
        </div>
    </form>

<a name="after-resetting-passwords"></a>
### 패스워드 재설정 후

사용자의 패스워드를 재설정하기 위해 라우트와 뷰를 정의하였다면, 이제 브라우저에서 간단히 라우트에 접근할 수 있습니다. 프레임워크에 포함된 `PasswordController`는 이미 패스워드 재설정 이메일을 보내고, 데이터베이스에 패스워드를 업데이트하는 로직을 가지고 있습니다.

패스워드가 재설정 되고나면, 사용자는 자동으로 어플리케이션에 로그인 처리되고 `/home` 으로 리다이렉트 됩니다. 여러분은 `PasswordController`의 `redirectTo` 속성을 정의하여 사용자의 패스워드가 재설정 된 이후에 리다이렉트할 경로를 변경할 수 있습니다:

    protected $redirectTo = '/dashboard';

> **참고:**기본적으로 패스워드 재설정 토큰은 1시간 동안만 유효합니다. `config/auth.php` 파일의 `reminder.expire` 옵션을 통해서 이를 변경할 수 있습니다. 

<a name="social-authentication"></a>
## 소셜 인증

기본적인 form 기반의 인증과 더불어 라라벨은 [라라벨 Socialite](https://github.com/laravel/socialite)를 사용하여 간단하고 편리한 OAuth 인증을 제공합니다. Socialite 는 현재 페이스북, 트위터, 링크드인, 구글, Github 과 Bitbucket 인증을 지원합니다. 

Socialite를 시작하기 위해서는 `composer.json` 파일에 의존성을 추가해야 합니다. 

    composer require laravel/socialite

### 설정하기

Socialite 라이브러리를 설치한 후, `config/app.php` 설정 파일에 `Laravel\Socialite\SocialiteServiceProvider`를 등록하십시오: 

    'providers' => [
        // Other service providers...

        Laravel\Socialite\SocialiteServiceProvider::class,
    ],

또한 `app` 설정 파일의 `aliases` 배열에 `Socialite` 파사드를 추가하십시오:

    'Socialite' => Laravel\Socialite\Facades\Socialite::class,

이제 어플리케이션에서 사용할 OAuth 서비스를 위한 인증 정보를 추가할 필요가 있습니다. 이 인증 정보들은 `config/services.php` 설정 파일에 지정되어야 하며, 어플리케이션이 필요로 하는 `facebook`, `twitter`,`linkedin`, `google`, `github` 또는 `bitbucket` 서비스에 맞게 키가 지정 되어야 합니다. 예를 들어:

    'github' => [
        'client_id' => 'your-github-app-id',
        'client_secret' => 'your-github-app-secret',
        'redirect' => 'http://your-callback-url',
    ],

### 기본적인 사용법

이제 사용자 인증을 위한 준비가 되었습니다. 이제 두가지의 라우트가 필요합니다: 하나는 OAuth를 사용하기 위한 리다이렉트와 다른 하나는 인증 후 결과를 콜백으로 받기 위한 라우트입니다. `Socialite` [파사드](/docs/{{version}}/facades)를 통해 Socialite에 엑세스할 것입니다:

    <?php

    namespace App\Http\Controllers;

    use Socialite;
    use Illuminate\Routing\Controller;

    class AuthController extends Controller
    {
        /**
         * Redirect the user to the GitHub authentication page.
         *
         * @return Response
         */
        public function redirectToProvider()
        {
            return Socialite::driver('github')->redirect();
        }

        /**
         * Obtain the user information from GitHub.
         *
         * @return Response
         */
        public function handleProviderCallback()
        {
            $user = Socialite::driver('github')->user();

            // $user->token;
        }
    }

`redirect` 메소드는 사용자를 OAuth 제공자로 보내는 일을 처리하고 `user` 메소드는 프로바이더로 부터 전송 받은 요청을 읽고 사용자 정보를 조회하는 일을 수행합니다. 사용자를 리다이렉트 하기전에, "범위"를 지정 할 수도 있습니다. 이 메소드는 존재하는 모든 범위들을 덮어쓸 것입니다:

    return Socialite::driver('github')
                ->scopes(['scope1', 'scope2'])->redirect();

물론 컨트롤러 메소드로 향하는 라우트를 정의해야 합니다: 

    Route::get('auth/github', 'Auth\AuthController@redirectToProvider');
    Route::get('auth/github/callback', 'Auth\AuthController@handleProviderCallback');

몇몇 OAuth 프로바이더는 리다이렉트 요청에 부가적인 파라미터를 지원합니다. request-요청에 부가적인 파라미터를 포함하려면 연관 배열을 통해 `with` 메소드를 호출하면 됩니다: 

    return Socialite::driver('google')
                ->with(['hd' => 'example.com'])->redirect();

#### 사용자의 상세정보 조회하기

사용자의 인스턴스를 획득하면, 사용자에 대한 보다 자세한 정보를 조회 할 수 있습니다:

    $user = Socialite::driver('github')->user();

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

<a name="adding-custom-authentication-drivers"></a>
## 사용자 정의 인증 드라이버 추가하기

사용자들을 저장하기 위해 전통적인 관계형 데이터베이스를 이용하지 않는다면 사용자 정의 인증 드라이버를 통해 라라벨을 확장해야 합니다. 사용자 정의 드라이버를 정의하기 위해 `Auth` 파사드에 `extend` 메소드를 사용할 것입니다. 이 메소드는 [서비스 제공자](/docs/{{version}}/providers) 안에서의 `extend` 호출 되어야 합니다: 

    <?php

    namespace App\Providers;

    use Auth;
    use App\Extensions\RiakUserProvider;
    use Illuminate\Support\ServiceProvider;

    class AuthServiceProvider extends ServiceProvider
    {
        /**
         * Perform post-registration booting of services.
         *
         * @return void
         */
        public function boot()
        {
            Auth::extend('riak', function($app) {
                // Return an instance of Illuminate\Contracts\Auth\UserProvider...
                return new RiakUserProvider($app['riak.connection']);
            });
        }

        /**
         * Register bindings in the container.
         *
         * @return void
         */
        public function register()
        {
            //
        }
    }

`extend` 메소드로 사용자 정의 드라이버를 등록한 뒤에, `config/auth.php` 설정 파일에서 새 드라이버로 변경할 수 있습니다. 

### 사용자 프로바이더 Contract

`Illuminate\Contracts\Auth\UserProvider`의 구현체는 MySQL, Riak 등과 같은 지속적인 스토리지 시스템에서 `Illuminate\Contracts\Auth\Authenticatable`을 불러오는 것만을 담당합니다. 이 두 인터페이스는 사용자 데이터가 어떻게 저장되었는지 또는 이를 위해 어떤 클래스가 사용되고 있는지 여부에 상관없이 라라벨 인증 메커니즘이 작동하도록 해줍니다. 

`Illuminate\Contracts\Auth\UserProvider` contract를 살펴보겠습니다: 

    <?php

    namespace Illuminate\Contracts\Auth;

    interface UserProvider {

        public function retrieveById($identifier);
        public function retrieveByToken($identifier, $token);
        public function updateRememberToken(Authenticatable $user, $token);
        public function retrieveByCredentials(array $credentials);
        public function validateCredentials(Authenticatable $user, array $credentials);

    }

`retrieveById` 함수는 일반적으로 MySQL 데이터베이스의 자동 증가 ID와 같은 사용자를 대표하는 키를 전달 받습니다. ID에 상응하는 `Authenticatable` 구현이 메소드를 통해 조회되거나 반환됩니다. 

`retrieveByToken` 함수는 고유의 `retrieveByToken`와 `remember_token`에 저장된 "remember me" `$token`에 따라 사용자를 조회합니다. 이전의 메소드와 같이 `Authenticatable` 구현이 반환됩니다. 

`updateRememberToken` 메소드는 새로운 `$token`으로 `$user`의 `remember_token`필드를 업데이트합니다. 토큰의 값은 정상적으로 "remember me"을 통해서 로그인을 시도하기 위해서 할당된 새로운 값이거나, 아니면 사용자가 로그아웃되었을 때를 위한 null값일 수 있습니다. 

`retrieveByCredentials` 메소드는 어플리케이션에 로그인하려고 시도할 때 `Auth::attempt` 메소드로 전달되는 인증 정보 배열을 전달 받습니다. 그러면 메소드는 그 인증 정보에 맞는 사용자들을 위한 기반에 있는 지속적 저장소(persistent storage)에 "질의"해야 합니다. 일반적으로 이 메소드는 `$credentials['username']`에 맞는 "where" 쿼리를 실행할 것입니다. 그뒤에 메소드는 `UserInterface`의 구현체를 반환해야 합니다. **이 메소드는 패스워드 확인이나 인증을 시도해서는 안됩니다.**

`validateCredentials` 메소드는 사용자를 인증하기 위하여 `$user`와 `$credentials`를 비교해야 합니다. 예를 들어 이 메소드는 `$user->getAuthPassword()`의 문자열을 `$credentials['password']`의 `Hash::make`값과 비교할 수 있습니다. 이 메소드는 사용자의 인증 정보만 확인하고 boolean 값을 반환합니다.

### Authenticatable Contract 살펴보기

`UserProvider`의 각 메소드에 대해 알아보았으니 이제 `Authenticatable` contract를 살펴 보도록 하겠습니다. 프로바이더는 `retrieveById`와 `retrieveByCredentials` 메소드에서 이 인터페이스의 구현을 반환합니다:

    <?php

    namespace Illuminate\Contracts\Auth;

    interface Authenticatable {

        public function getAuthIdentifier();
        public function getAuthPassword();
        public function getRememberToken();
        public function setRememberToken($value);
        public function getRememberTokenName();

    }

이 인터페이스는 간단합니다. `getAuthIdentifier` 메소드는 사용자의 "프라이머리 키"를 반환합니다. MySQL의 경우 auto-incrementing primary key에 해당합니다. `getAuthPassword`는 사용자의 해시된 패스워드를 반환합니다. 이 인터페이스는 어떤 ORM 혹은 저장소 추상화 계층을 사용하든지에 상관 없이 인증 시스템이 어떤 사용자 클래스에도 적용 될수 있도록 해줍니다. 라라벨은 기본적으로 이 인터페이스를 구현하는 `app` 디렉토리에 `User` 클래스를 포함하기 때문에 구현된 예를 보기 위해 이 클래스를 이용면 됩니다. 

<a name="events"></a>
## 이벤트

라라벨은 인증 과정 중에 다양한 [이벤트](/docs/{{version}}/events)들을 발생시킵니다. `EventServiceProvider`에서 이 이벤트들을 위한 listener들을 추가할 수 있습니다.

    /**
     * Register any other events for your application.
     *
     * @param  \Illuminate\Contracts\Events\Dispatcher  $events
     * @return void
     */
    public function boot(DispatcherContract $events)
    {
        parent::boot($events);

        // Fired on each authentication attempt...
        $events->listen('auth.attempt', function ($credentials, $remember, $login) {
            //
        });

        // Fired on successful logins...
        $events->listen('auth.login', function ($user, $remember) {
            //
        });

        // Fired on logouts...
        $events->listen('auth.logout', function ($user) {
            //
        });
    }
