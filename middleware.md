# 미들웨어

- [시작하기](#introduction)
- [미들웨어 정의하기](#defining-middleware)
- [미들웨어 등록하기](#registering-middleware)
    - [글로벌-전역 미들웨어](#global-middleware)
    - [라우트에 미들웨어 지정하기](#assigning-middleware-to-routes)
    - [미들웨어 그룹](#middleware-groups)
    - [미들웨어 순서](#sorting-middleware)
- [미들웨어 파라미터](#middleware-parameters)
- [종료시 동작하는 미들웨어](#terminable-middleware)

<a name="introduction"></a>
## 시작하기

미들웨어는 애플리케이션으로 들어온 HTTP 요청을 간편하게 필터링할 수 있는 방법을 제공합니다. 예를 들어, 라라벨은 애플리케이션의 사용자가 인증되었는지 검사하는 미들웨어를 내장하고 있습니다. 만약 인증되지 않은 사용자라면 미들웨어는 그 사용자를 로그인 화면으로 리다이렉트 합니다. 반대로 인증된 사용자라면, 미들웨어는 애플리케이션에서 HTTP 요청이 계속 처리되도록 합니다.

인증 이외에도 다양한 작업을 수행하는 미들웨어를 추가로 작성할 수도 있습니다. CORS 미들웨어는 애플리케이션에서 내보내는 모든 응답에 적절한 헤더들을 추가할 수도 있습니다. 로깅 미들웨어는 애플리케이션으로 들어오는 모든 요청을 기록할 수도 있습니다.

라라벨 프레임워크에는 인증(authentication)과 CSRF 보안을 위한 미들웨어가 포함되어 있습니다. 이러한 미들웨어는 모두 `app/Http/Middleware` 디렉토리 안에 존재합니다.

<a name="defining-middleware"></a>
## 미들웨어 정의하기

새로운 미들웨어를 생성하려면 `make:middleware` 아티즌 명령을 사용하세요.

    php artisan make:middleware CheckAge

이 명령은 `app/Http/Middleware` 디렉토리 안에 `CheckAge` 클래스를 생성합니다. 이 미들웨어는 `age`가 200보다 클 때에만 요청된 주소에 접근할 수 있도록 허용합니다. 그렇지 않은 경우 사용자를 `home` URI 으로 리다이렉트할 것입니다.

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

위 코드에서 볼 수 있듯이, 주어진 `age`가 200보다 작거나 같으면 미들웨어는 HTTP 리다이렉트를 클라이언트에게 반환합니다. 그렇지 않다면 HTTP 요청은 (미들웨어를 지나) 애플리케이션 안으로 계속 진행될 것입니다. (미들웨어가 "통과"를 허용하는) HTTP 요청을 애플리케이션 안으로 계속 전달하기 원한다면, `$next` 콜백함수에 `$request`인자를 넣어 호출하면 됩니다.

미들웨어를 HTTP 요청이 애플리케이션에 도달하기 전에 반드시 통과해야 하는 일종의 "단계(layers)"라고 생각하는 것이 가장 좋습니다. 각 단계에서 요청을 검사할 수 있고 요청을 완전히 거절할 수도 있습니다.

> {tip} 모든 미들웨어는 [서비스 컨테이너](/docs/{{version}}/container)를 통해 처리되므로 미들웨어의 생성자에 필요한 모든 의존성을 입력 할 수 있습니다.

#### Before & After 미들웨어

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

반대로, 아래의 경우에서 미들웨어는 요청이 애플리케이션에 의해 처리된 **이후**에 실행됩니다.

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

만약 애플리케이션의 모든 HTTP 요청에 대하여 미들웨어가 작동되기를 원한다면,  `app/Http/Kernel.php` 클래스의 `$middleware` 속성에 미들웨어를 등록하면 됩니다.

<a name="assigning-middleware-to-routes"></a>
### 라우트에 미들웨어 지정하기

미들웨어를 특정 라우트에만 할당하고 싶을 때에는 우선 `app/Http/Kernel.php` 파일에 그 미들웨어의 이름(key)을 지정해야 합니다. 이 클래스의 `$routeMiddleware` 속성에는 라라벨에 포함된 미들웨어 목록이 있습니다. 미들웨어를 추가하려면 미들웨어 이름과 함께 이 목록에 붙여주세요.

    // Within App\Http\Kernel Class...

    protected $routeMiddleware = [
        'auth' => \App\Http\Middleware\Authenticate::class,
        'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
        'bindings' => \Illuminate\Routing\Middleware\SubstituteBindings::class,
        'cache.headers' => \Illuminate\Http\Middleware\SetCacheHeaders::class,
        'can' => \Illuminate\Auth\Middleware\Authorize::class,
        'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
        'signed' => \Illuminate\Routing\Middleware\ValidateSignature::class,
        'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
        'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
    ];

미들웨어를 HTTP 커널에 등록했다면, 라우트에 `middleware` 메소드를 사용하여 미들웨어를 지정할 수 있습니다.

    Route::get('admin/profile', function () {
        //
    })->middleware('auth');

라우트에 여러개의 미들웨어를 지정할 수도 있습니다.

    Route::get('/', function () {
        //
    })->middleware('first', 'second');

미들웨어를 지정할 때, 전체 클래스 이름을 전달할 수도 있습니다.

    use App\Http\Middleware\CheckAge;

    Route::get('admin/profile', function () {
        //
    })->middleware(CheckAge::class);

라우트 그룹에 미들웨어를 할당 할 때 때때로 미들웨어가 그룹 내의 개별 라우트에 적용되는 것을 방지해야 할 수 있습니다. `withoutMiddleware` 메소드를 사용하여 이것을 처리할 수 있습니다.

    use App\Http\Middleware\CheckAge;

    Route::middleware([CheckAge::class])->group(function () {
        Route::get('/', function () {
            //
        });

        Route::get('admin/profile', function () {
            //
        })->withoutMiddleware([CheckAge::class]);
    });

`withoutMiddleware` 메소드는 라우트 미들웨어 만 제거 할 수 있으며 [글로벌 미들웨어](#global-middleware)에는 적용되지 않습니다.

<a name="middleware-groups"></a>
### 미들웨어 그룹

여러 개의 미들웨어를 하나의 이름으로 묶어서 라우트에 할당하고 싶을 수도 있습니다. 이 경우 HTTP 커널의 `$middlewareGroups` 속성을 사용하면 됩니다.

별다른 설정 없이도, 라라벨은 웹 UI 와 API 라우트에 적용할 수 있는 일반적인 미들웨어를 포함하는 `web` 그리고 `api` 의 미들웨어 그룹을 제공합니다.

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

미들웨어 그룹은 개별 미들웨어와 동일한 방식으로 라우트와 컨트롤러 액션에 지정할 수 있습니다. 즉, 미들웨어 그룹은 보다 편리하게 많은 미들웨어를 한번에 라우트에 할당할 수 있게 해줍니다.

    Route::get('/', function () {
        //
    })->middleware('web');

    Route::group(['middleware' => ['web']], function () {
        //
    });

    Route::middleware(['web', 'subscribed'])->group(function () {
        //
    });

> {tip} 별다른 설정없이도, `web` 미들웨어 그룹이 자동으로 `RouteServiceProvider`의 `routes/web.php` 파일에 지정되어 있습니다.

<a name="sorting-middleware"></a>
### 미들웨어 순서

드물게, 미들웨어를 정해진 순서에 따라서 실행하기를 원하지만, 라우트에 할당된 순서를 제어할 수는 없습니다. 이러한 경우, `app/Http/Kernel.php` 파일의 미들웨어의 `$middlewarePriority` 속성을 사용하여 우선순위를 지정할 수 있습니다.

    /**
     * The priority-sorted list of middleware.
     *
     * This forces non-global middleware to always be in the given order.
     *
     * @var array
     */
    protected $middlewarePriority = [
        \Illuminate\Session\Middleware\StartSession::class,
        \Illuminate\View\Middleware\ShareErrorsFromSession::class,
        \App\Http\Middleware\Authenticate::class,
        \Illuminate\Session\Middleware\AuthenticateSession::class,
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
        \Illuminate\Auth\Middleware\Authorize::class,
    ];

<a name="middleware-parameters"></a>
## 미들웨어 파라미터

미들웨어는 인자를 전달 받을 수도 있습니다. 예를 들어 로그인 사용자가 적절한 "역할(role)"을 가지고 있는지 확인해야 할 때, 역할의 이름을 인자로 전달받는 `CheckRole` 미들웨어를 만들 수 있습니다.

미들웨어 인자는 미들웨어의 `$next` 인자 다음에 전달됩니다.

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

라우트를 정의 할 때 미들웨어 이름과 인자를 `:` 로 구분합니다. 여러 개의 인자는 쉼표로 구분합니다.

    Route::put('post/{id}', function ($id) {
        //
    })->middleware('role:editor');

<a name="terminable-middleware"></a>
## 종료시 동작하는 미들웨어

때로는 미들웨어가 HTTP 응답이 브라우저로 전송 된 후 일부 작업을 수행해야 할 수도 있습니다. 미들웨어에서 `terminate` 메소드를 정의하고 웹 서버가 FastCGI를 사용하는 경우 응답이 브라우저로 전송 된 후 `terminate` 메소드가 자동으로 호출됩니다.


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

`terminate` 메소드는 Http 요청과 응답의 두 가지를 전달 받는 구조여야 합니다. 종료시 동작하는 미들웨어는 `app/Http/Kernel.php` 파일의 라우트나 글로벌 미들웨어 목록에 추가해야 합니다.

미들웨어의 `terminate` 메소드를 호출하면, 라라벨은 [서비스 컨테이너](/docs/{{version}}/container)를 통해 새로운 미들웨어 인스턴스를 생성합니다. `handle` 과 `terminate` 메소드를 호출할 때, 동일한 미들웨어 인스턴스를 사용하려면 컨테이너의 `singleton` 메소드를 사용하여 미들웨어를 등록하십시오. 이것은 일반적으로 `AppServiceProvider.php`의 `register` 메소드에서 수행해야합니다.

    use App\Http\Middleware\TerminableMiddleware;

    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        $this->app->singleton(TerminableMiddleware::class);
    }
