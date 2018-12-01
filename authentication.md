# 인증

- [시작하기](#introduction)
    - [데이터베이스 유의사항](#introduction-database-considerations)
- [빠르게 인증 살펴보기](#authentication-quickstart)
    - [Routing-라우팅](#included-routing)
    - [Views-뷰](#included-views)
    - [인증하기](#included-authenticating)
    - [인증된 사용자 획득하기](#retrieving-the-authenticated-user)
    - [라우트 제한하기](#protecting-routes)
    - [로그인 횟수 제한](#authentication-throttling)
- [수동으로 사용자 인증하기](#authenticating-users)
    - [사용자 기억하기](#remembering-users)
    - [그외 인증 메소드](#other-authentication-methods)
- [HTTP 기본 인증](#http-basic-authentication)
    - [상태를 유지하지 않는 HTTP 기본 인증](#stateless-http-basic-authentication)
- [소셜 인증](https://github.com/laravel/socialite)
- [사용자 정의 Guards 추가하기](#adding-custom-guards)
- [사용자 정의 User 프로바이더 추가하기](#adding-custom-user-providers)
    - [사용자 계약 제공](#the-user-provider-contract)
    - [인증가능 계약](#the-authenticatable-contract)
- [이벤트](#events)

<a name="introduction"></a>
## 시작하기

> {tip} **빠르게 시작 하시는것을 원하십니까?** 새로이 생성한 Laravel 응용 프로그램에서 `php artisan make:auth`와 `php artisan migrate` 를 실행하십시오. 그 다음에, 브라우저를 `http://your-app.dev/register` 또는 다른 URL로 이동하세요. 이 두개의 명령어는 전체적인 인증 시스템을 스캐폴딩합니다.

라라벨은 인증기능을 구현하는 것을 매우 쉽게 해줍니다. 기본적으로 별도의 설정 없이도 대부분이 이미 준비되어 있습니다. 인증에 대한 설정 파일은 `config/auth.php` 으로 인증 서비스의 동작을 제어할 수 있는 옵션들이 자세한 설명과 함께 제공됩니다.

내부 시스템에서, 라라벨의 인증 기능은 "guards" 와 "프로바이더"로 구성되어 있습니다. Guard 는 사용자가 각각의 요청-request 마다 어떻게 인증되는지 정의합니다. 예를 들어 라라벨은 세션 스토리지와 쿠키를 사용하여 상태를 유지하는 `session` guard 와, 각 요청-request와 함께 전달되는 "API 토큰"을 사용하여 사용자를 인증하는`token` guard를 제공합니다.

프로바이더는 저장소에서 사용자를 어떻게 찾을 찾을 수 있는지 정의합니다. 라라벨은 Eloqutn 와 데이터베이스 쿼리 빌더를 사용하여 사용자를 찾을 수 있도록 지원합니다. 또한, 애플리케이션에서 필요로 하는 추가적인 프로바이더를 자유롭게 정의할 수 있습니다.

지금 이러한 이야기가 혼란스럽더라도 걱정하지 마십시오. 많은 애플리케이션은 기본 인증 설정을 변경할 필요가 전혀 없습니다.

<a name="introduction-database-considerations"></a>
### 데이터베이스 유의사항

기본적으로 라라벨은 `app` 디렉토리에 `App\User` 모델을 포함하고 있습니다. 이 모델은 기본적인 Eloquent 인증 드라이버와 함께 사용하게 됩니다. 애플리케이션이 Eloquent를 사용하고 있지 않다면 라라벨 쿼리 빌더를 사용하는 `database` 인증 드라이버를 이용하면 됩니다.

`App\User` 모델을 위한 데이터베이스 스키마를 구성할 때, 유의할 것은 패스워드 컬럼이 최소 60 자가 되어야 하며, 기본값인 255도 좋은 선택이 될 수 있습니다.

또한 `users` (또는 동일한) 테이블이 NULL을 허용하는 100자리 문자열의 `remember_token` 컬럼을 포함하고 있는지 확인하십시오. 이 컬럼은 애플리케이션에서 관리하는 "remember me" 세션의 토큰을 저장하는데 사용됩니다.  마이그레이션에서 $table->rememberToken();를 사용하면 추가 할 수 있습니다.

<a name="authentication-quickstart"></a>
## 빠르게 인증 살펴보기

라라벨은 별도의 설정 없이도 `App\Http\Controllers\Auth` 네임스페이스에 위치한 몇가지 인증 컨트롤러를 제공하고 있습니다. `AuthController`는 새로운 사용자의 등록과 로그인을 처리하고 `PasswordController`는 암호 분실시에 사용자 암호 재설정을 처리합니다. 각각의 컨트롤러는 필요한 메서드를 포함하기 위해 트레이트-trait를 사용합니다. 대부분의 애플리케이션에서 여러분은 이 컨트롤러들을 수정할 필요가 없을 것입니다.

<a name="included-routing"></a>
### Routing-라우팅

라라벨은 다음의 간단한 명령어를 통해서 인증에서 필요한 모든 라우트와 뷰파일을 손쉽게 스캐폴딩 할 수 있는 손쉬운 방법을 제공합니다:

    php artisan make:auth

이 명령어는 새로운 애플리케이션에서 사용되어져야 하며, 모든 인증의 마지막 포인트에 대한 사용자 등록 및 로그인 뷰를 설치할 것입니다. 또한 `HomeController`가 생성되어, 애플리케이션의 post 로그인 요청을 처리할 것입니다. 하지만, 여러분은 애플리케이션에서 필요한 경우 이 컨트롤러를 삭제하거나, 자유롭게 수정 할 수도 있습니다.

<a name="included-views"></a>
### Views-뷰

앞서 언급하였듯이, `php artisan make:auth` 명령어는 인증에서 필요로 하는 모든 뷰를 생성하여 `resources/views/auth` 디렉토리에 위치시킬 것입니다.

`make:auth` 명령어는 또한 애플리케이션의 베이스 레이아웃을 포함하는 `resources/views/layouts` 디렉토리를 생성할 것입니다. 이 모든 뷰 파일들은 Bootstrap CSS 프레임워크를 사용하지만, 여러분이 원하는 경우 자유롭게 변경할 수 있습니다.

<a name="included-authenticating"></a>
### 인증하기

이제 인증 컨트롤러에 대한 라우트와 뷰가 설정되었으니, 애플리케이션에 새로운 사용자를 등록하거나 인증할 준비가 되었습니다. 간단하게는 브라우저에서 정의된 라우트를 접근할 수 있습니다. 인증 컨트롤러는 존재하는 사용자를 인증하고 새로운 사용자를 데이터베이스에 저장하는 이미 로직을(트레이트-trait를 통해) 가지고 있습니다.

#### 경로 수정하기

사용자가 성공적으로 인증되었을 때, 그들은 `/` URI로 리다이렉트 될 것입니다. 여러분은 `AuthController`의 `redirectTo` 속성을 정의하여, 인증 이후 디렉이렉트 되는 경로를 커스터마이징 할 수 있습니다.

    protected $redirectTo = '/';

리다이렉션 로직을 커스텀하고 구성할 필요가 있다면 `redirectTo` 속성 대신에 `redirectTo` 메소드를 정의하면 됩니다:

    protected function redirectTo()
    {
        //
    }

> {tip} `redirectTo` 메소드는 `redirectTo` 속성보다 우선순위가 높습니다.

#### 어떤 사용자이름을 사용할지 결정하기

기본적으로 라라벨은 인증을 위해서 `email` 필드를 사용합니다 만약 이를 수정하고자 한다면, `LoginController`의 `username` 메소드를 정의하면 됩니다: 

    public function username()
    {
        return 'username';
    }

#### Guard 커스터마이징

또한 여러분은 사용자 인증에 사용되는 "guard"를 커스터마이징 할 수 있습니다. 이렇게 하기 위해서, `AuthController`의 `guard` 속성을 정의하면 됩니다. 이 속성의 값은 `auth.php` 설정 파일에 설정된 가드들 중 하나와 일치해야합니다:

    use Illuminate\Support\Facades\Auth;

    protected function guard()
    {
        return Auth::guard('guard-name');
    }

#### Validation-유효성 검사 / 스토리지 커스터마이징

새로운 사용자를 등록하는 폼의 필수 입력 항목들을 변경 하고자 하거나, 또는 새로운 레코드가 데이터베이스에 입력되는 방법을 변경하고자 한다면, `RegisterController` 클래스를 수정하면 됩니다. 이 클래스는 애플리케이션에서 새로운 사용자를 검증하고 생성하는 역할을 합니다.

`AuthController` 클래스의 `validator` 메소드는 애플리케이션의 새로운 사용자에 대한 폼 검증 규칙을 포함하고 있으며, 여러분은 원하는대로 이 메소드들을 수정해도 됩니다.

`AuthController`의 `create` 메소드는 [Eloquent ORM](/docs/{{version}}/eloquent)을 이용하여 데이터베이스에 새 `App\User` 기록을 생성합니다. 데이터베이스의 필요에 따라 이 메소드를 수정할 수 있습니다.

<a name="retrieving-the-authenticated-user"></a>
### 승인된 사용자 조회하기

`Auth` 파사드를 통해 인증된 사용자에게 접근할 수 있습니다:

    use Illuminate\Support\Facades\Auth;

    // Get the currently authenticated user...
    $user = Auth::user();

    // Get the currently authenticated user's ID...
    $id = Auth::id();

대체적인 방법으로는, 사용자가 승인된 후 `Illuminate\Http\Request` 인스턴스를 통해 승인된 사용자에게 접근하는 방법이 있습니다. 타입힌트된 클래스는 컨트롤러 메소드에 자동으로 주입될 것이라는 것을 기억하십시오:

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

사용자가 이미 애플리케이션에 로그인했는지 판별하려면, `Auth` 파사드에 `check` 메소드를 사용할 수 있습니다. 사용자가 인증되었다면 `true`를 반환합니다:

    use Illuminate\Support\Facades\Auth;

    if (Auth::check()) {
        // The user is logged in...
    }

> {tip} 사용자가 `check` 함수를 사용하여 인증되었는지를 판단 할 수는 있지만, 일반적으로 미들웨어를 사용하여 사용자가 특정 라우트 / 컨트롤러에 대한 사용자 액세스를 허용하기 전에 사용자가 인증되었는지 확인합니다. 이에 대한 자세한 내용은 [라우트 보호하기](/docs/{{version}}/authentication#protecting-routes) 문서를 확인하십시오.

<a name="protecting-routes"></a>
### 라우트 보호하기

[라우트 미들웨어](/docs/{{version}}/middleware)는 인증된 사용자에게만 주어진 라우트에 접근하는 것을 허용도록 할 수 있습니다. 라라벨은 `app\Http\Middleware\Authenticate.php`에 정의된 `auth` 미들웨어를 제공하고 있습니다. 이제 할일은 라우트가 정의된 부분에 미들웨어를 추가하면 됩니다:

    Route::get('profile', function () {
        // Only authenticated users may enter...
    })->middleware('auth');

물론 [컨트롤러 클래스](/docs/{{version}}/controllers)를 사용하고 있다면, 라우트 정의부분에 직접 추가하는 것 대신 컨트롤러의 생성자에서 `middleware` 메소드를 호출할 수 있습니다:

    public function __construct()
    {
        $this->middleware('auth');
    }

#### Guard 지정하기

When attaching the `auth` middleware to a route, you may also specify which guard should be used to authenticate the user. The guard specified should correspond to one of the keys in the `guards` array of your `auth.php` configuration file:

`auth` 미들웨어를 라우트에 추가할 때, 여러분은 또한 어떤 guard 가 인증에 사용되어야 하는지 지정할 수 있습니다. 지정된 guard는 `auth.php` 설정 파일의 `guards` 배열에 있는 키와 일치해야 합니다: 

    public function __construct()
    {
        $this->middleware('auth:api');
    }

<a name="login-throttling"></a>
### 로그인 횟수 제한

만약 라라벨에 포함된 `AuthController` 클래스를 사용한다면 `Illuminate\Foundation\Auth\ThrottlesLogins` 트레이트-trait을 사용하여 로그인 시도를 제한할 수 있습니다. 기본적으로, 사용자가 여러 번에 걸쳐 정확한 정보를 입력하지 못한다면, 1분 동안 로그인하지 못하도록 설정되어 있습니다. 제한은 사용자의 사용자 이름 / 이메일 주소와 IP 주소를 고유하게 취급합니다.

<a name="authenticating-users"></a>
## 수동으로 사용자 인증하기

물론 라라벨에 포함된 인증 컨트롤러를 꼭 써야하지는 않습니다. 이 컨트롤러들을 삭제한다면 라라벨의 인증 클래스를 사용하여 사용자 인증을 직접 관리해야 합니다. 아주 쉬우니까 걱정하지 마세요!

라라벨의 인증 서비스를 `Auth` [파사드](/docs/{{version}}/facades)를 통해 접근할 것이기 때문에 클래스의 가장 상단 부분에 `Auth` 파사드를 사용하도록 선언하는 것이 필수입니다. 다음은 `attempt` 메소드를 확인해 보겠습니다:

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Support\Facades\Auth;

    class LoginController extends Controller
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

#### 추가적인 조건 지정하기

원한다면, 사용자의 이메일과 패스워드 외에도 부가적인 조건들을 인증 쿼리에 추가할 수 있습니다. 예를 들어 사용자가 "활성화"로 표시되어있는지 확인할 수 있습니다:

    if (Auth::attempt(['email' => $email, 'password' => $password, 'active' => 1])) {
        // The user is active, not suspended, and exists.
    }

> {note} 이 예제에서, `email` 은 필수 옵션이 아니라, 그냥 예제에서 사용된것입니다. 여러분은 데이터베이스 안에 있는 "username"과 일치하는 어떠한 컬럼 이름을 사용해야 합니다.

#### 지정된 Guard 인스턴스에 엑세스하기

여러분은 `Auth` 파사드 외관에 `guard` 메소드를 사용하여 어떤 guard 인스턴스를 사용하고자 하는지 지정할 수 있습니다. 이것은 여러분이 완전히 분리된 인증 모델 또는 사용자 테이블을 사용하여 애플리케이션의 개별 파트에 대한 인증을 관리할 수 있도록 합니다.

`guard` 메소드에 전달되는 guard의 이름은 `auth.php` 설정 파일에 설정된 guard 중 하나와 일치 해야합니다:

    if (Auth::guard('admin')->attempt($credentials)) {
        //
    }

#### 로그아웃

애플리케이션에서 사용자를 로그아웃 시키려면 `Auth` 파사드의 `logout` 메소드를 사용하면 됩니다. 그러면 사용자 세션에서 인증 정보가 제거될 것입니다:

    Auth::logout();

<a name="remembering-users"></a>
### 사용자 기억하기

여러분의 애플리케이션에 "기억하기" 기능을 제공하고자 한다면, `attempt` 메소드의 두번째 인자로 사용자의 인증을 무기한 계속 유지할지, 아니면 수동으로 로그아웃 할때까지 유지할지 결정하는 boolean 값을 전달하면 됩니다. 이를 위해서는 `users` 테이블은 "기억하기" 토근을 저장하는데 사용되는 `remember_token` 컬럼을 가지고 있어야만 합니다.

    if (Auth::attempt(['email' => $email, 'password' => $password], $remember)) {
        // The user is being remembered...
    }

> {tip} Laravel과 함께 제공되는 기본 제공 'LoginController'를 사용하는 경우, 사용자를 "기억"하는 적절한 논리는 이미 컨트롤러에서 사용되는 특성에 의해 구현됩니다.

사용자들을 "기억" 해내는 중이라면, `viaRemember` 메소드를 사용하여 해당 사용자가 "기억 하기" 쿠키를 사용하여 인증이 되었는지 확인 할 수 있습니다:

    if (Auth::viaRemember()) {
        //
    }

<a name="other-authentication-methods"></a>
### 그 외 인증 메소드

#### 사용자 인스턴스를 통해서 인증하기

애플리케이션에 이미 존재하는 사용자 인스턴스를 통해서 로그인을 하려면 사용자 인스턴스의 `login` 메소드를 호출할 수 있습니다. 주어진 객체는 `Illuminate\Contracts\Auth\Authenticatable` [contract](/docs/{{version}}/contracts)를 구현해야 합니다. 물론 라라벨에 포함된 `App\User` 모델은 이미 이 인터페이스를 구현합니다:

    Auth::login($user);

    // Login and "remember" the given user...
    Auth::login($user, true);

물론, 사용하자 하는 가드의 인스턴스를 지정할 수도 있습니다.

    Auth::guard('admin')->login($user);

#### ID를 통해서 사용자 인증하기

사용자 ID를 통하여 사용자를 애플리케이션에 로그인 시키려면, `loginUsingId` 메소드를 사용하면 됩니다. 이 메소드는 단순히 인증하고자 하는 사용자의 프라이머리 키를 전달 받습니다:

    Auth::loginUsingId(1);

    // Login and "remember" the given user...
    Auth::loginUsingId(1, true);

#### 사용자 인증 한 번 하기

하나의 request에 대해서 `once` 메소드로 사용자를 로그인시킬 수 있습니다. 세션과 쿠키는 활용되지 않을 것이며 이는 상태를 유지하지 않는 API를 만드는데 도움이 됩니다. `once` 메소드의 서명은 `attempt` 메소드의 서명과 같습니다:

    if (Auth::once($credentials)) {
        //
    }

<a name="http-basic-authentication"></a>
## HTTP 기본 인증

[HTTP 기본 인증](https://en.wikipedia.org/wiki/Basic_access_authentication)은 애플리케이션에 별도의 "login" 페이지 설정없이도 사용자 인증을 할 수 있는 손쉬운 방법을 제공합니다. 이를 위해서는 `auth.basic` [미들웨어](/docs/{{version}}/middleware)를 라우트에 추가하면 됩니다. `auth.basic` 미들웨어는 라라벨에 포함되어 있기 때문에 따로 정의할 필요가 없습니다:

    Route::get('profile', function () {
        // Only authenticated users may enter...
    })->middleware('auth.basic');

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

다음으로, [라우트 미들웨어를 등록하고](/docs/{{version}}/middleware#registering-middleware) 라우트에 추가하십시오:

    Route::get('api/user', function () {
        // Only authenticated users may enter...
    })->middleware('auth.basic.once');

<a name="adding-custom-guards"></a>
## 사용자정의 Guard 추가

여러분은 `Auth` 파사드의 `extend` 메소드를 사용하여 고유한 인증 guard를 정의할 수 있습니다. 이 메소드는 서비스 프로바이더 중에 하나의 `provider` 에서 호출해야 합니다.

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

이 예제에서 볼 수 있듯이, `extend` 메소드에 전달된 콜백은 `Illuminate\Contracts\Auth\Guard` 구현체를 반환해야 합니다. 이 인터페이스는 사용자 정의 guard를 구현하는데 필요한 몇가지 메소드를 가지고 있습니다. 사용자 정의 guard 를 정의하고나면, `guards` 설정에서 정의한 guard 를 사용할 수 있습니다: 

    'guards' => [
        'api' => [
            'driver' => 'jwt',
            'provider' => 'users',
        ],
    ],

<a name="adding-custom-user-providers"></a>
## 사용자 정의 User 프로바이더 추가하기

사용자들을 저장하기 위해 전통적인 관계형 데이터베이스를 이용하지 않는다면 사용자 정의 User 프로바이더를 통해 라라벨을 확장해야 합니다. 사용자 정의 User 프로바이더를 정의하기 위해 `Auth` 파사드에 `provider` 메소드를 사용할 것입니다. 이 메소드는 [서비스 제공자](/docs/{{version}}/providers) 안에서의 `provider` 호출 되어야 합니다:

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\Auth;
    use App\Extensions\RiakUserProvider;
    use Illuminate\Support\ServiceProvider;

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

`provider` 메소드로 프로바이더를 등록한 뒤에, `config/auth.php` 설정 파일에서 새 User 프로바이더로 변경할 수 있습니다. 먼저 새로운 드라이버를 사용하는 `provider`를 정의합시다.

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

`retrieveByCredentials` 메소드는 애플리케이션에 로그인하려고 시도할 때 `Auth::attempt` 메소드로 전달되는 인증 정보 배열을 전달 받습니다. 그러면 메소드는 그 인증 정보에 맞는 사용자들을 위한 기반에 있는 지속적 저장소(persistent storage)에 "질의"해야 합니다. 일반적으로 이 메소드는 `$credentials['username']`에 맞는 "where" 쿼리를 실행할 것입니다. 그뒤에 메소드는 `UserInterface`의 구현체를 반환해야 합니다. **이 메소드는 패스워드 확인이나 인증을 시도해서는 안됩니다.**

`validateCredentials` 메소드는 사용자를 인증하기 위하여 `$user`와 `$credentials`를 비교해야 합니다. 예를 들어 이 메소드는 `$user->getAuthPassword()`의 문자열을 `$credentials['password']`의 `Hash::make`값과 비교할 수 있습니다. 이 메소드는 사용자의 인증 정보만 확인하고 boolean 값을 반환합니다.

<a name="the-authenticatable-contract"></a>
### Authenticatable Contract 살펴보기

`UserProvider`의 각 메소드에 대해 알아보았으니 이제 `Authenticatable` contract를 살펴 보도록 하겠습니다. 프로바이더는 `retrieveById`와 `retrieveByCredentials` 메소드에서 이 인터페이스의 구현을 반환합니다:

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

이 인터페이스는 간단합니다. `getAuthIdentifierName` 메소드는 사용자의 "프라이머리 키" 필드의 이름을 반환해야하며 `getAuthIdentifier` 메소드는 사용자의 "프라이머리 키"를 반환합니다. MySQL의 경우 auto-incrementing primary key에 해당합니다. `getAuthPassword`는 사용자의 해시된 패스워드를 반환합니다. 이 인터페이스는 어떤 ORM 혹은 저장소 추상화 계층을 사용하든지에 상관 없이 인증 시스템이 어떤 사용자 클래스에도 적용 될수 있도록 해줍니다. 라라벨은 기본적으로 이 인터페이스를 구현하는 `app` 디렉토리에 `User` 클래스를 포함하기 때문에 구현된 예를 보기 위해 이 클래스를 이용면 됩니다.

<a name="events"></a>
## 이벤트

라라벨은 인증 과정 중에 다양한 [이벤트](/docs/{{version}}/events)들을 발생시킵니다. `EventServiceProvider`에서 이 이벤트들을 위한 listener들을 추가할 수 있습니다:

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
    ];
