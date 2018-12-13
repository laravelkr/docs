# 미들웨어

- [시작하기](#introduction)
- [미들웨어 정의하기](#defining-middleware)
- [미들웨어 등록하기](#registering-middleware)
    - [글로벌-전역 미들웨어](#global-middleware)
    - [라우트에 미들웨어 지정하기](#assigning-middleware-to-routes)
    - [미들웨어 그룹](#middleware-groups)
- [미들웨어 파라미터](#middleware-parameters)
- [종료시 동작하는 미들웨어](#terminable-middleware)

<a name="introduction"></a>
## 시작하기

미들웨어는 애플리케이션으로 들어온 HTTP 요청을 간편하게 필터링할 수 있는 방법을 제공합니다. 예를 들어, 라라벨은 애플리케이션의 사용자가 인증되었는지 검사하는 미들웨어를 내장하고 있습니다. 만약 인증되지 않은 사용자라면, 미들웨어는 그 사용자를 로그인 화면으로 리다이렉트 시킬 것입니다. 반대로, 인증된 사용자라면, 미들웨어는 애플리케이션에서 HTTP 요청이 계속해서 더 처리되도록 허용할 것입니다.

물론, 인증이외에도 다양한 작업을 수행하는 추가적인 미들웨어를 작성할 수도 있습니다. CORS 미들웨어는 애플리케이션에서 내보내는 모든 응답에 적절한 헤더들을 추가하는 역할을 담당할 수도 있습니다. 로깅 미들웨어는 애플리케이션으로 들어오는 모든 요청을 기록할 수도 있습니다.

라라벨 프레임워크에는 인증(authentication)과 CSRF 보안을 위한 미들웨어들이 포함되어 있습니다. 이러한 미들웨어들은 모두 `app/Http/Middleware` 디렉토리 안에 위치하고 있습니다.

<a name="defining-middleware"></a>
## 미들웨어 정의하기

새로운 미들웨어를 생성하기 위하여 `make:middleware` 아티즌 명령을 사용할 수 있습니다:

    php artisan make:middleware CheckAge

이 명령은 `CheckAge ` 클래스를 생성하여 `app/Http/Middleware` 디렉토리에 위치시킬 것입니다. 이 미들웨어 에서 우리는 입력받은 `age`가 200보다 클 때에만 요청된 주소에 접근할 수 있도록 허용하려고 합니다. 그렇지 않은경우 사용자를 `home` URI 으로 리다이렉트할 것입니다:

    <?php

    namespace App\Http\Middleware;

    use Closure;

    class CheckAge
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
            if ($request->age <= 200) {
                return redirect('home');
            }

            return $next($request);
        }
    }

위 코드에서 볼 수 있듯이, 주어진 `age`가 200보다 작거나 같으면 미들웨어는 HTTP 리다이렉트를 클라이언트에게 반환할 것입니다; 그렇지 않다면 HTTP 요청은 (미들웨어를 지나) 애플리케이션 안으로 계속 진행될 것입니다. (미들웨어가 "통과"를 허용하는) HTTP 요청을 애플리케이션 안으로 계속 전달하기 원한다면, `$next` 콜백함수에 `$request`인자를 넣어 호출하면 됩니다.

미들웨어를 HTTP request들이 애플리케이션에 도달하기 전에 반드시 통과해야 하는 일련의 "레이어"라고 생각하는 것이 가장 좋습니다. 각각의 레이어는 요청을 검사할 수 있고 완벽하게 요청을 거절할 수도 있습니다.

> {tip} 모든 미들웨어는 [서비스 컨테이너](/docs/{{version}}/container)를 통해 처리되므로 미들웨어의 생성자에 필요한 모든 의존성을 입력 할 수 있습니다.

### Before & After 미들웨어

애플리케이션이 HTTP 요청을 미들웨어가 처리하기 전에 실행될지 처리한 후에 실행될지는 미들웨어 자신이 결정할 수 있습니다. 예를 들어 다음의 미들웨어는 애플리케이션에서 HTTP 요청이 처리되기 **이전** 에 실행됩니다.

    <?php

    namespace App\Http\Middleware;

    use Closure;

    class BeforeMiddleware
    {
        public function handle($request, Closure $next)
        {
            // Perform action

            return $next($request);
        }
    }

반대로, 아래의 경우에서 미들웨어는 요청이 애플리케이션에 의해 처리된 **이후**에 실행될 것입니다:

    <?php

    namespace App\Http\Middleware;

    use Closure;

    class AfterMiddleware
    {
        public function handle($request, Closure $next)
        {
            $response = $next($request);

            // Perform action

            return $response;
        }
    }

<a name="registering-middleware"></a>
## 미들웨어 등록하기

<a name="global-middleware"></a>
### 글로벌-전역 미들웨어

만약 애플리케이션의 모든 HTTP 요청에 대하여 미들웨어가 작동되기를 원한다면,  `app/Http/Kernel.php` 클래스의 `$middleware` 프로퍼티에 미들웨어를 등록하시면 됩니다.

<a name="assigning-middleware-to-routes"></a>
### 라우트에 미들웨어 지정하기

미들웨어를 특정 라우트에만 할당하고 싶을 때에는 우선 `app/Http/Kernel.php` 파일에 그 미들웨어의 키를 지정해야 합니다. 기본적으로, 이 Kernel 클래스의 `$routeMiddleware` 속성은 라라벨에 포함된 미들웨어들의 목록을 가지고 있습니다. 추가하려는 미들웨어에 원하는 키를 지정하고 이 목록에 붙여넣으십시오. 예를 들면:

    // Within App\Http\Kernel Class...

    protected $routeMiddleware = [
        'auth' => \Illuminate\Auth\Middleware\Authenticate::class,
        'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
        'bindings' => \Illuminate\Routing\Middleware\SubstituteBindings::class,
        'can' => \Illuminate\Auth\Middleware\Authorize::class,
        'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
        'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
    ];

미들웨어를 HTTP 커널에 등록했다면, 라우트에 `middleware` 메소드를 사용하여 미들웨어를 지정할 수 있습니다:

    Route::get('admin/profile', function () {
        //
    })->middleware('auth');

라우트에 여러개의 미들웨어를 지정할 수도 있습니다:
    Route::get('/', function () {
        //
    })->middleware('first', 'second');

미들웨어를 지정할 때, 전체 클래스 이름을 전달할 수도 있습니다:

    use App\Http\Middleware\CheckAge;

    Route::get('admin/profile', function () {
        //
    })->middleware(CheckAge::class);

<a name="middleware-groups"></a>
### 미들웨어 그룹

때로는 몇몇 미들웨어를 하나의 키로 묶어서 손쉽게 라우트에 할당하도록 하고자 할 수도 있습니다. HTTP 커널의 `$middlewareGroups` 속성을 사용해서 이를 지정할 수 있습니다.

별다른 설정 없이도, 라라벨은 웹 UI 와 API 라우트에 적용할 수 있는 일반적인 미들웨어를 포함하는 `web` 그리고 `api` 의 미들웨어 그룹을 제공합니다:

    /**
     * The application's route middleware groups.
     *
     * @var array
     */
    protected $middlewareGroups = [
        'web' => [
            \App\Http\Middleware\EncryptCookies::class,
            \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
            \Illuminate\Session\Middleware\StartSession::class,
            \Illuminate\View\Middleware\ShareErrorsFromSession::class,
            \App\Http\Middleware\VerifyCsrfToken::class,
            \Illuminate\Routing\Middleware\SubstituteBindings::class,
        ],

        'api' => [
            'throttle:60,1',
            'auth:api',
        ],
    ];

미들웨어 그룹은 개별 미들웨어와 동일한 문법으로 라우트들과 컨트롤러 액션들에 할당될 수 있습니다. 다시 한번, 미들웨어 그룹은 보다 편리하게 많은 미들웨어를 한번에 라우트에 할당할 수 있게 해줍니다:

    Route::get('/', function () {
        //
    })->middleware('web');

    Route::group(['middleware' => ['web']], function () {
        //
    });

> {tip} 별다른 설정없이도, `web` 미들웨어 그룹이 자동으로 `RouteServiceProvider`의 `routes/web.php` 파일에 지정되어 있습니다.

<a name="middleware-parameters"></a>
## 미들웨어 파라미터

미들웨어에서는 추가적으로 파라미터를 전달 받을 수 있습니다. 예를 들어 애플리케이션이 인증된 사용자인지 확인하기 위해서 사용자의 주어진 "역할(role)"을 가진 인증 된 사용자인지 응용 프로그램에서 확인해야하는 경우 역할의 이름을 추가 인자로 전달 받는 `CheckRole` 을 만들 수 있습니다.

추가적인 미들웨어 파라미터는 미들웨어에 `$next` 인자 뒤에 전달 될것입니다:

    <?php

    namespace App\Http\Middleware;

    use Closure;

    class CheckRole
    {
        /**
         * Handle the incoming request.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  \Closure  $next
         * @param  string  $role
         * @return mixed
         */
        public function handle($request, Closure $next, $role)
        {
            if (! $request->user()->hasRole($role)) {
                // Redirect...
            }

            return $next($request);
        }

    }

미들웨어 파라미터는 라우트를 정의 할 때 지정된 미들웨어 이름과 파라미터를 `:` 로 구분하여 지정합니다. 여러개의 파라미터는 쉼표로 구분합니다:

    Route::put('post/{id}', function ($id) {
        //
    })->middleware('role:editor');

<a name="terminable-middleware"></a>
## 종료시 동작하는 미들웨어

때로는 미들웨어는 HTTP response-응답이 준비된 이 후에 어떤 작업을 수행할 필요가 있을지도 모릅니다. 예를 들어, 라라벨에 내장된 "session" 미들웨어는 response-응답이 완전히 준비된 이후에 세션데이터를 저장소에 저장합니다. 만약 미들웨어에 `terminate` 메소드를 정의했다면, response-응답이 준비된 뒤에 자동으로 호출될 것입니다.

    <?php

    namespace Illuminate\Session\Middleware;

    use Closure;

    class StartSession
    {
        public function handle($request, Closure $next)
        {
            return $next($request);
        }

        public function terminate($request, $response)
        {
            // Store the session data...
        }
    }

`terminate` 메소드는 Http 요청과 응답의 두가지를 전달 받는 구조여야 합니다. 종료시 동작하는 미들웨어를 정의하고나면, 이 미들웨어를 `app/Http/Kernel.php` 파일의 라우트 또는 글로벌 미들웨어 목록에 추가해 주어야 합니다.

미들웨어의 `terminate` 메소드가 호출될 때, 라라벨은 [서비스 컨테이너](/docs/{{version}}/container)에서 미들웨어의 새로운 인스턴스를 의존성 해결 하여 획득 할 것입니다. `handle` 과 `terminate` 메소드를 호출할 때, 동일한 미들웨어 인스턴스를 사용하려면 컨테이너의 `singleton` 메소드를 사용하여 미들웨어를 등록하십시오.
