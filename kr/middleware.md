# Middleware
# 미들웨어

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Defining Middleware](#defining-middleware)
- [미들웨어 정의하기](#defining-middleware)
- [Registering Middleware](#registering-middleware)
- [미들웨어 등록하기](#registering-middleware)
    - [Global Middleware](#global-middleware)
    - [글로벌-전역 미들웨어](#global-middleware)
    - [Assigning Middleware To Routes](#assigning-middleware-to-routes)
    - [라우트에 미들웨어 지정하기](#assigning-middleware-to-routes)
    - [Middleware Groups](#middleware-groups)
    - [미들웨어 그룹](#middleware-groups)
    - [Sorting Middleware](#sorting-middleware)
    - [미들웨어 순서](#sorting-middleware)
- [Middleware Parameters](#middleware-parameters)
- [미들웨어 파라미터](#middleware-parameters)
- [Terminable Middleware](#terminable-middleware)
- [종료시 동작하는 미들웨어](#terminable-middleware)

<a name="introduction"></a>
## Introduction
## 시작하기

Middleware provide a convenient mechanism for filtering HTTP requests entering your application. For example, Laravel includes a middleware that verifies the user of your application is authenticated. If the user is not authenticated, the middleware will redirect the user to the login screen. However, if the user is authenticated, the middleware will allow the request to proceed further into the application.

미들웨어는 애플리케이션으로 들어온 HTTP 요청을 간편하게 필터링할 수 있는 방법을 제공합니다. 예를 들어, 라라벨은 애플리케이션의 사용자가 인증되었는지 검사하는 미들웨어를 내장하고 있습니다. 만약 인증되지 않은 사용자라면 미들웨어는 그 사용자를 로그인 화면으로 리다이렉트 합니다. 반대로 인증된 사용자라면, 미들웨어는 애플리케이션에서 HTTP 요청이 계속 처리되도록 합니다.

Additional middleware can be written to perform a variety of tasks besides authentication. A CORS middleware might be responsible for adding the proper headers to all responses leaving your application. A logging middleware might log all incoming requests to your application.

인증 이외에도 다양한 작업을 수행하는 미들웨어를 추가로 작성할 수도 있습니다. CORS 미들웨어는 애플리케이션에서 내보내는 모든 응답에 적절한 헤더들을 추가할 수도 있습니다. 로깅 미들웨어는 애플리케이션으로 들어오는 모든 요청을 기록할 수도 있습니다.

There are several middleware included in the Laravel framework, including middleware for authentication and CSRF protection. All of these middleware are located in the `app/Http/Middleware` directory.

라라벨 프레임워크에는 인증(authentication)과 CSRF 보안을 위한 미들웨어가 포함되어 있습니다. 이러한 미들웨어는 모두 `app/Http/Middleware` 디렉토리 안에 존재합니다.

<a name="defining-middleware"></a>
## Defining Middleware
## 미들웨어 정의하기

To create a new middleware, use the `make:middleware` Artisan command:

새로운 미들웨어를 생성하려면 `make:middleware` 아티즌 명령을 사용하세요.

    php artisan make:middleware CheckAge

This command will place a new `CheckAge` class within your `app/Http/Middleware` directory. In this middleware, we will only allow access to the route if the supplied `age` is greater than 200. Otherwise, we will redirect the users back to the `home` URI:

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

As you can see, if the given `age` is less than or equal to `200`, the middleware will return an HTTP redirect to the client; otherwise, the request will be passed further into the application. To pass the request deeper into the application (allowing the middleware to "pass"), call the `$next` callback with the `$request`.

위 코드에서 볼 수 있듯이, 주어진 `age`가 200보다 작거나 같으면 미들웨어는 HTTP 리다이렉트를 클라이언트에게 반환합니다. 그렇지 않다면 HTTP 요청은 (미들웨어를 지나) 애플리케이션 안으로 계속 진행될 것입니다. (미들웨어가 "통과"를 허용하는) HTTP 요청을 애플리케이션 안으로 계속 전달하기 원한다면, `$next` 콜백함수에 `$request`인자를 넣어 호출하면 됩니다.

It's best to envision middleware as a series of "layers" HTTP requests must pass through before they hit your application. Each layer can examine the request and even reject it entirely.

미들웨어를 HTTP 요청이 애플리케이션에 도달하기 전에 반드시 통과해야 하는 일종의 "단계(layers)"라고 생각하는 것이 가장 좋습니다. 각 단계에서 요청을 검사할 수 있고 요청을 완전히 거절할 수도 있습니다.

> {tip} All middleware are resolved via the [service container](/docs/{{version}}/container), so you may type-hint any dependencies you need within a middleware's constructor.

> {tip} 모든 미들웨어는 [서비스 컨테이너](/docs/{{version}}/container)를 통해 처리되므로 미들웨어의 생성자에 필요한 모든 의존성을 입력 할 수 있습니다.

### Before & After Middleware
### Before & After 미들웨어

Whether a middleware runs before or after a request depends on the middleware itself. For example, the following middleware would perform some task **before** the request is handled by the application:

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

However, this middleware would perform its task **after** the request is handled by the application:

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
## Registering Middleware
## 미들웨어 등록하기

<a name="global-middleware"></a>
### Global Middleware
### 글로벌-전역 미들웨어

If you want a middleware to run during every HTTP request to your application, list the middleware class in the `$middleware` property of your `app/Http/Kernel.php` class.

만약 애플리케이션의 모든 HTTP 요청에 대하여 미들웨어가 작동되기를 원한다면,  `app/Http/Kernel.php` 클래스의 `$middleware` 속성에 미들웨어를 등록하면 됩니다.

<a name="assigning-middleware-to-routes"></a>
### Assigning Middleware To Routes
### 라우트에 미들웨어 지정하기

If you would like to assign middleware to specific routes, you should first assign the middleware a key in your `app/Http/Kernel.php` file. By default, the `$routeMiddleware` property of this class contains entries for the middleware included with Laravel. To add your own, append it to this list and assign it a key of your choosing:

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

Once the middleware has been defined in the HTTP kernel, you may use the `middleware` method to assign middleware to a route:

미들웨어를 HTTP 커널에 등록했다면, 라우트에 `middleware` 메소드를 사용하여 미들웨어를 지정할 수 있습니다.

    Route::get('admin/profile', function () {
        //
    })->middleware('auth');

You may also assign multiple middleware to the route:

라우트에 여러개의 미들웨어를 지정할 수도 있습니다.
    Route::get('/', function () {
        //
    })->middleware('first', 'second');

When assigning middleware, you may also pass the fully qualified class name:

미들웨어를 지정할 때, 전체 클래스 이름을 전달할 수도 있습니다.

    use App\Http\Middleware\CheckAge;

    Route::get('admin/profile', function () {
        //
    })->middleware(CheckAge::class);

<a name="middleware-groups"></a>
### Middleware Groups
### 미들웨어 그룹

Sometimes you may want to group several middleware under a single key to make them easier to assign to routes. You may do this using the `$middlewareGroups` property of your HTTP kernel.

여러 개의 미들웨어를 하나의 이름으로 묶어서 라우트에 할당하고 싶을 수도 있습니다. 이 경우 HTTP 커널의 `$middlewareGroups` 속성을 사용하면 됩니다.

Out of the box, Laravel comes with `web` and `api` middleware groups that contain common middleware you may want to apply to your web UI and API routes:

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

Middleware groups may be assigned to routes and controller actions using the same syntax as individual middleware. Again, middleware groups make it more convenient to assign many middleware to a route at once:

미들웨어 그룹은 개별 미들웨어와 동일한 방식으로 라우트와 컨트롤러 액션에 지정할 수 있습니다. 즉, 미들웨어 그룹은 보다 편리하게 많은 미들웨어를 한번에 라우트에 할당할 수 있게 해줍니다.

    Route::get('/', function () {
        //
    })->middleware('web');

    Route::group(['middleware' => ['web']], function () {
        //
    });

> {tip} Out of the box, the `web` middleware group is automatically applied to your `routes/web.php` file by the `RouteServiceProvider`.

> {tip} 별다른 설정없이도, `web` 미들웨어 그룹이 자동으로 `RouteServiceProvider`의 `routes/web.php` 파일에 지정되어 있습니다.

<a name="sorting-middleware"></a>
### Sorting Middleware
### 미들웨어 순서

Rarely, you may need your middleware to execute in a specific order but not have control over their order when they are assigned to the route. In this case, you may specify your middleware priority using the `$middlewarePriority` property of your `app/Http/Kernel.php` file:

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
## Middleware Parameters
## 미들웨어 파라미터

Middleware can also receive additional parameters. For example, if your application needs to verify that the authenticated user has a given "role" before performing a given action, you could create a `CheckRole` middleware that receives a role name as an additional argument.

미들웨어는 인자를 전달 받을 수도 있습니다. 예를 들어 로그인 사용자가 적절한 "역할(role)"을 가지고 있는지 확인해야 할 때, 역할의 이름을 인자로 전달받는 `CheckRole` 미들웨어를 만들 수 있습니다.

Additional middleware parameters will be passed to the middleware after the `$next` argument:

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

Middleware parameters may be specified when defining the route by separating the middleware name and parameters with a `:`. Multiple parameters should be delimited by commas:

라우트를 정의 할 때 미들웨어 이름과 인자를 `:` 로 구분합니다. 여러 개의 인자는 쉼표로 구분합니다.

    Route::put('post/{id}', function ($id) {
        //
    })->middleware('role:editor');

<a name="terminable-middleware"></a>
## Terminable Middleware
## 종료시 동작하는 미들웨어

Sometimes a middleware may need to do some work after the HTTP response has been sent to the browser. For example, the "session" middleware included with Laravel writes the session data to storage after the response has been sent to the browser. If you define a `terminate` method on your middleware and your web server is using FastCGI, the `terminate` method will automatically be called after the response is sent to the browser.

때로는 미들웨어가 HTTP 응답이 브라우저로 전송 된 후 일부 작업을 수행해야 할 수도 있습니다. 예를 들어 Laravel에 포함 된 "세션" 미들웨어는 응답이 브라우저로 전송 된 후 세션 데이터를 저장소에 기록합니다. 미들웨어에서 `terminate` 메소드를 정의하고 웹 서버가 FastCGI를 사용한다면, 응답이 브라우저로 보내진 후 `terminate` 메소드가 자동으로 호출됩니다.

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

The `terminate` method should receive both the request and the response. Once you have defined a terminable middleware, you should add it to the list of route or global middleware in the `app/Http/Kernel.php` file.

`terminate` 메소드는 Http 요청과 응답의 두 가지를 전달 받는 구조여야 합니다. 종료시 동작하는 미들웨어는 `app/Http/Kernel.php` 파일의 라우트나 글로벌 미들웨어 목록에 추가해야 합니다.

When calling the `terminate` method on your middleware, Laravel will resolve a fresh instance of the middleware from the [service container](/docs/{{version}}/container). If you would like to use the same middleware instance when the `handle` and `terminate` methods are called, register the middleware with the container using the container's `singleton` method.

미들웨어의 `terminate` 메소드를 호출하면, 라라벨은 [서비스 컨테이너](/docs/{{version}}/container)를 통해 새로운 미들웨어 인스턴스를 생성합니다. `handle` 과 `terminate` 메소드를 호출할 때, 동일한 미들웨어 인스턴스를 사용하려면 컨테이너의 `singleton` 메소드를 사용하여 미들웨어를 등록하십시오.
