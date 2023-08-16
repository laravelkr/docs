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

Middleware provide a convenient mechanism for inspecting and filtering HTTP requests entering your application. For example, Laravel includes a middleware that verifies the user of your application is authenticated. If the user is not authenticated, the middleware will redirect the user to your application's login screen. However, if the user is authenticated, the middleware will allow the request to proceed further into the application.

미들웨어는 애플리케이션으로 들어온 HTTP 요청을 간편하게 검증하고 필터링할 수 있는 방법을 제공합니다. 예를 들어, 라라벨은 애플리케이션의 사용자가 인증되었는지 검사하는 미들웨어를 내장하고 있습니다. 만약 인증되지 않은 사용자라면 미들웨어는 그 사용자를 애플리케이션의 로그인 화면으로 리다이렉트 합니다. 반대로 인증된 사용자라면, 미들웨어는 애플리케이션에서 HTTP 요청이 계속 처리되도록 합니다.

Additional middleware can be written to perform a variety of tasks besides authentication. For example, a logging middleware might log all incoming requests to your application. There are several middleware included in the Laravel framework, including middleware for authentication and CSRF protection. All of these middleware are located in the `app/Http/Middleware` directory.

인증 이외에도 다양한 작업을 수행하는 미들웨어를 추가로 작성할 수 있습니다. 예를 들어, 로깅 미들웨어는 애플리케이션에 유입되는 모든 요청을 기록할 수 있습니다. 라라벨 프레임워크에는 인증 및 CSRF 보호 미들웨어를 포함해 여러 미들웨어가 존재합니다. 모든 미들웨어는 `app/Http/Middleware` 디렉토리에 있습니다. 

<a name="defining-middleware"></a>
## Defining Middleware
## 미들웨어 정의하기

To create a new middleware, use the `make:middleware` Artisan command:

새로운 미들웨어를 생성하려면 `make:middleware` 아티즌 명령을 사용하면 됩니다.

```shell
php artisan make:middleware EnsureTokenIsValid
```

This command will place a new `EnsureTokenIsValid` class within your `app/Http/Middleware` directory. In this middleware, we will only allow access to the route if the supplied `token` input matches a specified value. Otherwise, we will redirect the users back to the `home` URI:

이 명령은 `app/Http/Middleware` 디렉토리 안에 `EnsureTokenIsValid` 클래스를 생성합니다. 이 미들웨어는 `token`이 지정된 값과 매칭될때만 요청된 주소에 접근할 수 있도록 허용합니다. 그렇지 않은 경우 사용자를 `home` URI 으로 리다이렉트할 것입니다.

    <?php

    namespace App\Http\Middleware;

    use Closure;
    use Illuminate\Http\Request;
    use Symfony\Component\HttpFoundation\Response;

    class EnsureTokenIsValid
    {
        /**
         * Handle an incoming request.
         *
         * @param  \Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response)  $next
         */
        public function handle(Request $request, Closure $next): Response
        {
            if ($request->input('token') !== 'my-secret-token') {
                return redirect('home');
            }

            return $next($request);
        }
    }

As you can see, if the given `token` does not match our secret token, the middleware will return an HTTP redirect to the client; otherwise, the request will be passed further into the application. To pass the request deeper into the application (allowing the middleware to "pass"), you should call the `$next` callback with the `$request`.

위 코드에서 볼 수 있듯이, 주어진 `token` 값이 암호토큰과 매칭되지 않는다면 미들웨어는 HTTP 리다이렉트를 클라이언트에게 반환합니다. 그렇지 않다면 HTTP 요청은 (미들웨어를 지나) 애플리케이션 안으로 계속 진행될 것입니다. (미들웨어가 "통과"를 허용하는) HTTP 요청을 애플리케이션 안으로 계속 전달하기 원한다면, `$next` 콜백함수에 `$request`인자를 넣어 호출하면 됩니다.

It's best to envision middleware as a series of "layers" HTTP requests must pass through before they hit your application. Each layer can examine the request and even reject it entirely.

미들웨어를 HTTP 요청이 애플리케이션에 도달하기 전에 반드시 통과해야 하는 일종의 "단계(layers)"라고 생각하는 것이 가장 좋습니다. 각 단계에서 요청을 검사할 수 있고 요청을 완전히 거절할 수도 있습니다.

> **Note**  
> All middleware are resolved via the [service container](/docs/{{version}}/container), so you may type-hint any dependencies you need within a middleware's constructor.

> **Note**  
> 모든 미들웨어는 [서비스 컨테이너](/docs/{{version}}/container)를 통해 처리되므로 미들웨어의 생성자에 필요한 모든 의존성을 입력 할 수 있습니다.

<a name="before-after-middleware"></a>
<a name="middleware-and-responses"></a>
#### Middleware & Responses
#### 미들웨어와 응답

Of course, a middleware can perform tasks before or after passing the request deeper into the application. For example, the following middleware would perform some task **before** the request is handled by the application:

당연하게도, 미들웨어는 요청을 애플리케이션에게 전달하기 전과 후 둘중 언제 실행될지 자신이 결정할 수 있습니다. 예를 들어 다음의 미들웨어는 애플리케이션에서 HTTP 요청이 처리되기 **이전** 에 실행됩니다.

    <?php

    namespace App\Http\Middleware;

    use Closure;
    use Illuminate\Http\Request;
    use Symfony\Component\HttpFoundation\Response;

    class BeforeMiddleware
    {
        public function handle(Request $request, Closure $next): Response
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
    use Illuminate\Http\Request;
    use Symfony\Component\HttpFoundation\Response;

    class AfterMiddleware
    {
        public function handle(Request $request, Closure $next): Response
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

If you would like to assign middleware to specific routes, you may invoke the `middleware` method when defining the route:

특정 Route에 미들웨어를 할당하려면 Route를 정의할 때 `middleware` 메소드를 호출할 수 있습니다.

    use App\Http\Middleware\Authenticate;

    Route::get('/profile', function () {
        // ...
    })->middleware(Authenticate::class);

You may assign multiple middleware to the route by passing an array of middleware names to the `middleware` method:

`middleware` 메서드에 미들웨어 이름 배열을 전달하여 Route에 여러 미들웨어를 할당할 수 있습니다.

    Route::get('/', function () {
        // ...
    })->middleware([First::class, Second::class]);

For convenience, you may assign aliases to middleware in your application's `app/Http/Kernel.php` file. By default, the `$middlewareAliases` property of this class contains entries for the middleware included with Laravel. You may add your own middleware to this list and assign it an alias of your choosing:

편의를 위해 어플리케이션의 `appHttpKernel.php` 파일에서 미들웨어에 별칭을 지정할 수 있습니다. 기본적으로 이 클래스의 `middlewareAliases` 속성에는 Laravel에 포함된 미들웨어에 대한 항목이 포함되어 있습니다. 이 목록에 자신의 미들웨어를 추가하고 선택한 별칭을 지정할 수 있습니다.

    // Within App\Http\Kernel class...

    protected $middlewareAliases = [
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

Once the middleware alias has been defined in the HTTP kernel, you may use the alias when assigning middleware to routes:

미들웨어 별칭을 HTTP 커널에 등록했다면 미들웨어를 Route에 할당할 때 별칭을 사용할 수 있습니다.

    Route::get('/profile', function () {
        // ...
    })->middleware('auth');

<a name="excluding-middleware"></a>
#### Excluding Middleware
#### 미들웨어 제외하기

When assigning middleware to a group of routes, you may occasionally need to prevent the middleware from being applied to an individual route within the group. You may accomplish this using the `withoutMiddleware` method:

라우트 그룹에 미들웨어를 할당 할 때 때때로 미들웨어가 그룹 내의 개별 라우트에 적용되는 것을 방지해야 할 수 있습니다. `withoutMiddleware` 메소드를 사용하여 이것을 처리할 수 있습니다.

    use App\Http\Middleware\EnsureTokenIsValid;

    Route::middleware([EnsureTokenIsValid::class])->group(function () {
        Route::get('/', function () {
            // ...
        });

        Route::get('/profile', function () {
            // ...
        })->withoutMiddleware([EnsureTokenIsValid::class]);
    });

You may also exclude a given set of middleware from an entire [group](/docs/{{version}}/routing#route-groups) of route definitions:

라우트가 정의된 전체 [그룹](/docs/{{version}}/routing#route-groups)에서 주어진 미들웨어 세트를 제외할 수도 있습니다.

    use App\Http\Middleware\EnsureTokenIsValid;

    Route::withoutMiddleware([EnsureTokenIsValid::class])->group(function () {
        Route::get('/profile', function () {
            // ...
        });
    });

The `withoutMiddleware` method can only remove route middleware and does not apply to [global middleware](#global-middleware).

`withoutMiddleware` 메소드는 라우트 미들웨어 만 제거 할 수 있으며 [글로벌 미들웨어](#global-middleware)에는 적용되지 않습니다.

<a name="middleware-groups"></a>
### Middleware Groups
### 미들웨어 그룹

Sometimes you may want to group several middleware under a single key to make them easier to assign to routes. You may accomplish this using the `$middlewareGroups` property of your HTTP kernel.

여러 개의 미들웨어를 하나의 이름으로 묶어서 라우트에 할당하고 싶을 수도 있습니다. 이 경우 HTTP 커널의 `$middlewareGroups` 속성을 사용하면 됩니다.

Laravel includes predefined `web` and `api` middleware groups that contain common middleware you may want to apply to your web and API routes. Remember, these middleware groups are automatically applied by your application's `App\Providers\RouteServiceProvider` service provider to routes within your corresponding `web` and `api` route files:

라라벨은 웹 UI 와 API 라우트에 적용할 수 있는 일반적인 미들웨어를 포함하는 미리 정의된 `web` 그리고 `api` 의 미들웨어 그룹을 제공합니다. 이 미들웨어 그룹은 애플리케이션의 `App\Providers\RouteServiceProvider` 서비스 프로바이더가 `web` 및 `api` 라우트 파일 안의 경로에 자동으로 적용한다는 것을 기억하십시오.

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
            \Illuminate\Routing\Middleware\ThrottleRequests::class.':api',
            \Illuminate\Routing\Middleware\SubstituteBindings::class,
        ],
    ];

Middleware groups may be assigned to routes and controller actions using the same syntax as individual middleware. Again, middleware groups make it more convenient to assign many middleware to a route at once:

미들웨어 그룹은 개별 미들웨어와 동일한 방식으로 라우트와 컨트롤러 액션에 지정할 수 있습니다. 즉, 미들웨어 그룹은 보다 편리하게 많은 미들웨어를 한번에 라우트에 할당할 수 있게 해줍니다.

    Route::get('/', function () {
        // ...
    })->middleware('web');

    Route::middleware(['web'])->group(function () {
        // ...
    });

> **Note**  
> Out of the box, the `web` and `api` middleware groups are automatically applied to your application's corresponding `routes/web.php` and `routes/api.php` files by the `App\Providers\RouteServiceProvider`.

> **Note**  
> 별다른 설정없이도, `web` 과 `api` 미들웨어 그룹이 `App\Providers\RouteServiceProvider` 에 의해서 자동으로 애플리케이션의 `routes/web.php`과 `routes/api.php` 파일에 적용됩니다.

<a name="sorting-middleware"></a>
### Sorting Middleware
### 미들웨어 순서

Rarely, you may need your middleware to execute in a specific order but not have control over their order when they are assigned to the route. In this case, you may specify your middleware priority using the `$middlewarePriority` property of your `app/Http/Kernel.php` file. This property may not exist in your HTTP kernel by default. If it does not exist, you may copy its default definition below:

드물게, 미들웨어를 정해진 순서에 따라서 실행하기를 원하지만, 라우트에 할당된 순서를 제어할 수는 없습니다. 이러한 경우, `app/Http/Kernel.php` 파일의 미들웨어의 `$middlewarePriority` 속성을 사용하여 우선순위를 지정할 수 있습니다. 이 속성은 HTTP 커널에 기본적으로 존재하지 않을 수도 있습니다. 이 속성이 없다면 다음과 같이 기본 코드를 복사할 수 있습니다.

    /**
     * The priority-sorted list of middleware.
     *
     * This forces non-global middleware to always be in the given order.
     *
     * @var string[]
     */
    protected $middlewarePriority = [
        \Illuminate\Foundation\Http\Middleware\HandlePrecognitiveRequests::class,
        \Illuminate\Cookie\Middleware\EncryptCookies::class,
        \Illuminate\Session\Middleware\StartSession::class,
        \Illuminate\View\Middleware\ShareErrorsFromSession::class,
        \Illuminate\Contracts\Auth\Middleware\AuthenticatesRequests::class,
        \Illuminate\Routing\Middleware\ThrottleRequests::class,
        \Illuminate\Routing\Middleware\ThrottleRequestsWithRedis::class,
        \Illuminate\Contracts\Session\Middleware\AuthenticatesSessions::class,
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
        \Illuminate\Auth\Middleware\Authorize::class,
    ];

<a name="middleware-parameters"></a>
## Middleware Parameters
## 미들웨어 파라미터

Middleware can also receive additional parameters. For example, if your application needs to verify that the authenticated user has a given "role" before performing a given action, you could create an `EnsureUserHasRole` middleware that receives a role name as an additional argument.

미들웨어는 인자를 전달 받을 수도 있습니다. 예를 들어 로그인 사용자가 적절한 "역할(role)"을 가지고 있는지 확인해야 할 때, 역할의 이름을 인자로 전달받는 `EnsureUserHasRole` 미들웨어를 만들 수 있습니다.

Additional middleware parameters will be passed to the middleware after the `$next` argument:

미들웨어 인자는 미들웨어의 `$next` 인자 다음에 전달됩니다.

    <?php

    namespace App\Http\Middleware;

    use Closure;
    use Illuminate\Http\Request;
    use Symfony\Component\HttpFoundation\Response;

    class EnsureUserHasRole
    {
        /**
         * Handle an incoming request.
         *
         * @param  \Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response)  $next
         */
        public function handle(Request $request, Closure $next, string $role): Response
        {
            if (! $request->user()->hasRole($role)) {
                // Redirect...
            }

            return $next($request);
        }

    }

Middleware parameters may be specified when defining the route by separating the middleware name and parameters with a `:`. Multiple parameters should be delimited by commas:

라우트를 정의 할 때 미들웨어 이름과 인자를 `:` 로 구분합니다. 여러 개의 인자는 쉼표로 구분합니다.

    Route::put('/post/{id}', function (string $id) {
        // ...
    })->middleware('role:editor');

<a name="terminable-middleware"></a>
## Terminable Middleware
## 종료시 동작하는 미들웨어

Sometimes a middleware may need to do some work after the HTTP response has been sent to the browser. If you define a `terminate` method on your middleware and your web server is using FastCGI, the `terminate` method will automatically be called after the response is sent to the browser:

때로는 미들웨어가 HTTP 응답이 브라우저로 전송 된 후 일부 작업을 수행해야 할 수도 있습니다. 미들웨어에서 `terminate` 메소드를 정의하고 웹 서버가 FastCGI를 사용하는 경우 응답이 브라우저로 전송 된 후 `terminate` 메소드가 자동으로 호출됩니다.


    <?php

    namespace Illuminate\Session\Middleware;

    use Closure;
    use Illuminate\Http\Request;
    use Symfony\Component\HttpFoundation\Response;

    class TerminatingMiddleware
    {
        /**
         * Handle an incoming request.
         *
         * @param  \Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response)  $next
         */
        public function handle(Request $request, Closure $next): Response
        {
            return $next($request);
        }

        /**
         * Handle tasks after the response has been sent to the browser.
         */
        public function terminate(Request $request, Response $response): void
        {
            // ...
        }
    }

The `terminate` method should receive both the request and the response. Once you have defined a terminable middleware, you should add it to the list of routes or global middleware in the `app/Http/Kernel.php` file.

`terminate` 메소드는 Http 요청과 응답의 두 가지를 전달 받는 구조여야 합니다. 종료시 동작하는 미들웨어는 `app/Http/Kernel.php` 파일의 라우트나 글로벌 미들웨어 목록에 추가해야 합니다.

When calling the `terminate` method on your middleware, Laravel will resolve a fresh instance of the middleware from the [service container](/docs/{{version}}/container). If you would like to use the same middleware instance when the `handle` and `terminate` methods are called, register the middleware with the container using the container's `singleton` method. Typically this should be done in the `register` method of your `AppServiceProvider`:

미들웨어의 `terminate` 메소드를 호출하면, 라라벨은 [서비스 컨테이너](/docs/{{version}}/container)를 통해 새로운 미들웨어 인스턴스를 생성합니다. `handle` 과 `terminate` 메소드를 호출할 때, 동일한 미들웨어 인스턴스를 사용하려면 컨테이너의 `singleton` 메소드를 사용하여 미들웨어를 등록하십시오. 이것은 일반적으로`AppServiceProvider`의 `register` 메소드에서 수행해야합니다.

    use App\Http\Middleware\TerminatingMiddleware;

    /**
     * Register any application services.
     */
    public function register(): void
    {
        $this->app->singleton(TerminatingMiddleware::class);
    }
