# Release Notes
# 릴리즈 노트

- [Versioning Scheme](#versioning-scheme)
- [버전 관리 체계](#versioning-scheme)
- [Support Policy](#support-policy)
- [지원 정책](#support-policy)
- [Laravel 5.5](#laravel-5.5)
- [라라벨 5.5](#laravel-5.5)

<a name="versioning-scheme"></a>
## Versioning Scheme
## 버전 관리 체계

Laravel's versioning scheme maintains the following convention: `paradigm.minor.patch`. Minor framework releases are released every six months (January and July), while patch releases may be released as often as every week. Patch releases should **never** contain breaking changes.

라라벨의 버전 관리 체계는 다음의 컨벤션을 유지합니다: `paradigm.minor.patch`. 마이너 프레임워크 릴리즈는 6 개월마다 (1월, 7월) 릴리즈되며, 패치 릴리스는 매주 여러번 릴리즈 될 수 있습니다. 패치 릴리즈에는 이전 버전의 호환성을 깨뜨리는 변경 사항이 **없어야** 합니다.

When referencing the Laravel framework or its components from your application or package, you should always use a version constraint such as `5.4.*`, since minor releases of Laravel do include breaking changes. However, we strive to always ensure you may update to a new minor release in one day or less.

어플리케이션에서 라라벨 프레임워크, 라라벨의 컴포넌트 또는 패키지를 참조할 때에, 라라벨의 마이너 릴리즈가 이전 버전과 호환성을 유지하지 못하는 변경사항을 포함하고 있기 때문에 항상 `5.4.*` 와 같이 참조하도록 해야 합니다.

Paradigm shifting releases are separated by many years and represent fundamental shifts in the framework's architecture and conventions. Currently, there is no paradigm shifting release under development.

패러다임 변경 릴리즈는 몇년간 구분되어 있으며, 프레임워크의 아키텍처와 컨벤션에 대한 근본적인 변경을 나타냅니다. 현재까지는 개발중인 버전에 대한 변경 발표는 없습니다.

#### Why Doesn't Laravel Use Semantic Versioning?
#### 왜 라라벨은 시멘틱 버저닝을 사용하지 않습니까?

On one hand, all optional components of Laravel (Cashier, Dusk, Valet, Socialite, etc.) **do** use semantic versioning. However, the Laravel framework itself does not. The reason for this is because semantic versioning is a "reductionist" way of determining if two pieces of code are compatible. Even when using semantic versioning, you still must install the upgraded package and run your automated test suite to know if anything is *actually* incompatible with your code base.

한편, 라라벨의 모든 선택가능한 추가 컴포넌트들(Cashier, Dusk, Valet, Socialite, 등)은 시멘틱 버저닝을 **사용**합니다. 그렇지만 라라벨 프레임워크 자체는 그렇지 않습니다.

그 이유는 시멘틱 버저닝이 코드의 두개의 피스가 호환되는지 결정하는 "reductionist" 방법이기 때문입니다. 시멘틱 버저닝을 사용하는 경우라도, 실제로 업그레이드된 패키지를 설치하고 자동화된 테스트를 통해서 *실제* 코드와 호환되지 않는 것이 있는지 확인해야 합니다.

So, instead, the Laravel framework uses a versioning scheme that is more communicative of the actual scope of the release.

Furthermore, since patch releases **never** contain intentional breaking changes,

you should never receive a breaking change as long as your version constraints follow the `paradigm.minor.*` convention.

따라서, 그 대신에, 라라벨 프레임워크는 릴리즈의 실제 범위와 관련된 더 많은 정보를 제공하는 버전 관리 체계를 사용합니다. 또한 패치 릴리즈에는 **절대로** 이전 버전과 호환성을 깨뜨리는 변경사항을 포함하고 있지 않습니다. 여러분이 `paradigm.minor.*` 컨벤션을 따르는 한 절대로 이전 버전과 호환되지 않는 사항을 포함하는 변경사항을 적용받지 않습니다.

<a name="support-policy"></a>
## Support Policy
## 지원 정책

For LTS releases, such as Laravel 5.1, bug fixes are provided for 2 years and security fixes are provided for 3 years. These releases provide the longest window of support and maintenance. For general releases, bug fixes are provided for 6 months and security fixes are provided for 1 year.

라라벨 5.1과 같은 LTS 릴리즈 동안에는, 2년간의 버그 픽스와 3년동안의 보안 패치가 지원됩니다. 이러한 릴리즈는 장기간에 걸친 지원과 유지보수를 제공합니다. 일반적인 릴리즈에서는 버그 픽스는 6개월, 보안 패치는 1년동안 제공됩니다.

<a name="laravel-5.5"></a>
## Laravel 5.5
## 라라벨 5.5

<a name="laravel-5.5"></a>
## Laravel 5.5

Laravel 5.5 continues the improvements made in Laravel 5.4 by adding package auto-detection, auto-registration of console commands, queued job chaining, renderable mailables, renderable and reportable exceptions, more consistent exception handling, database testing improvements, simpler custom validation rules, React front-end presets, `Route::view` and `Route::redirect` methods, "locks" for the Memcached and Redis cache drivers, on-demand notifications, headless Chrome support in Dusk, convenient Blade shortcuts, improved trusted proxy support, and more.

라라벨 5.5는 패키지 자동 감지, 콘솔 명령어의 자동 등록, queued job 체이닝, 브라우저에서 렌더링이 가능한 maialble, exception-예외에 대한 renderable, reportable, 보다 편리한 exceptioin 핸들링, 데이터베이스 테스팅의 강화, 보다 간단해진 커스텀 유효성 검사 rule 등록, React 프론트엔드 preset, `Route::view` 와 `Route::redirect` 메소드, Memcache 와 Redis 캐시의 "잠금" 기능, 일회성 알림 기능, Dusk 에서 headless Chrome 지원, 편리한 블레이드 단축 기능, 신뢰가능한 프록시 지원 향상등 라라벨 5.4에 비해서 기능이 향상되었습니다.

In addition, Laravel 5.5 coincides with the release of [Laravel Horizon](http://horizon.laravel.com), a beautiful new queue dashboard and configuration system for your Redis based Laravel queues.

또한 라라벨 5.5는 Redis 기반의 라라벨 queue를 위한 새로운 큐-queue 대시보드및 설정 시스템인 [라라벨 Horizon] (http://horizon.laravel.com)와 함께 출시되었습니다.

> {tip} This documentation summarizes the most notable improvements to the framework; however, more thorough change logs are always available [on GitHub](https://github.com/laravel/framework/blob/5.5/CHANGELOG-5.5.md).

> {tip} 이 문서는 프레임워크에서 주요한 개선사항들에 대한 요약입니다. 보다 자세한 변경사항은 [GitHub](https://github.com/laravel/framework/blob/5.5/CHANGELOG-5.5.md)에서 확인할 수 있습니다.

### Laravel Horizon
### 라라벨 Horizon

Horizon provides a beautiful dashboard and code-driven configuration for your Laravel powered Redis queues. Horizon allows you to easily monitor key metrics of your queue system such as job throughput, runtime, and job failures.

Horizon 은 라라벨의 Redis 큐를 위한 멋진 대시보드와 코드 기반의 설정 시스템을 제공합니다. Horizon을 사용하면 작업 처리량, 실행 시간 및 작업 실패와 같은 queue-큐 시스템의 주요 메트릭을 쉽게 모니터링 할 수 있습니다.

All of your worker configuration is stored in a single, simple configuration file, allowing your configuration to stay in source control where your entire team can collaborate.

모든 워커 설정은 하나의 파일에 저장되므로, 전체 팀이 협업할 수 있는 소스제어시스템을 설정할 수 있습니다.

For more information on Horizon, check out the [full Horizon documentation](/docs/{{version}}/horizon)

Horizon 에 대한 보다 자세한 내용은 [전체 Horizon 문서](/docs/{{version}}/horizon)를 참고하십시오.

### Package Discovery
### 패키지 Discovery

In previous versions of Laravel, installing a package typically required several additional steps such as adding the service provider to your `app` configuration file and registering any relevant facades. However, beginning with Laravel 5.5, Laravel can automatically detect and register service providers and facades for you.

이전버전의 라라벨에서는 패키지를 설치하려면, 일반적으로 서비스 프로바이더와 파사드를 등록하기 위해서 `app` 설정파일에 몇가지 추가적인 작업들을 진행해야만 했습니다. 하지만 라라벨 5.5 에서는 라라벨이 자동으로 서비스 프로바이더와 파사드를 감지하고, 등록합니다.

For example, you can experience this by installing the popular `barryvdh/laravel-debugbar` package into your Laravel application. Once the package is installed via Composer, the debug bar will be available to your application with no additional configuration:

예를 들어, 라라벨 어플리케이션에 `barryvdh/laravel-debugbar`를 설치하는 것을 예로 들어 보겠습니다. 컴포저를 통해서 패키지를 설치하고 나면, 추가적인 설정 없이도 어플리케이션에 디버그-바 가 활성화 됩니다:

    composer require barryvdh/laravel-debugbar

Package developers only need to add their service providers and facades to their package's `composer.json` file:

패키지 개발자에게 필요한 것은 패키지의 `composer.json` 파일에 서비스 프로바이더와 파사드에 대한 내용을 추가하는 것입니다:

    "extra": {
        "laravel": {
            "providers": [
                "Laravel\\Tinker\\TinkerServiceProvider"
            ]
        }
    },

For more information on updating your packages to use service provider and facade discovery, check out the full documentation on [package development](/docs/{{version}}/packages).

패키지가 서비스 프로바이더와 파사드를 자동으로 감지하도록 업데이트하기 위한 보다 자세한 내용은 [패키지 개발](/docs/{{version}}/packages)에 대한 전체 문서를 확인하시기 바랍니다.

### Console Command Auto-Registration
### 콘솔 명령어 자동 등록

When creating new console commands, you no longer are required to manually list them in the `$commands` property of your Console kernel. Instead, a new `load` method is called from the `commands` method of your kernel, which will scan the given directory for any console commands and register them automatically:

새로운 콘솔 명령어를 생성할 때, 더이상 콘솔 커널의 `$commands` 속성에 수동으로 이를 추가해줄 필요가 없어졌습니다. 대신에 커널의 `commands` 메소드가 새로운 `load` 메소드를 호출하여 주어진 디렉토리에서 콘솔 명령어객체를 찾고 자동으로 등록합니다:

    /**
     * Register the commands for the application.
     *
     * @return void
     */
    protected function commands()
    {
        $this->load(__DIR__.'/Commands');

        // ...
    }

### New Frontend Presets
### 새로운 프론트엔드 Presets

While the basic Vue scaffolding is still included in Laravel 5.5, several new frontend preset options are now available. In a fresh Laravel application, you can swap the Vue scaffolding for React scaffolding using the `preset` command:

라라벨에 여전히 포함되어 있는 기본적인 Vue 스캐폴딩과 같이 몇가지 새로운 프론트 엔드 preset 옵션이 가능해졌습니다. 새롭게 라라벨을 설치하고 나서, `preset` 명령어를 통해서 Vue 스캐폴딩을 React 스캐폴딩으로 변경할 수 있습니다:

    php artisan preset react

Or, you can remove the JavaScript and CSS framework scaffolding entirely using the `none` preset. This preset will leave your application with a plain Sass file and a few simple JavaScript utilities:

또는 `none` preset을 사용하여 자바스크립트와 CSS 프레임워크 스캐폴딩을 완전히 삭제할 수도 있습니다. 이 경우 Sass 파일 하나와 몇가지 자바스크립트 유틸리티를 남겨놓습니다:

    php artisan preset none

> {note} These commands are only intended to be run on fresh Laravel installations. They should not be used on existing applications.

> {note} 이 명령어는 새롭게 설치한 라라벨에서만 실행됩니다. 기존 어플리케이션에서는 사용해서는 안됩니다.

### Queued Job Chaining
### 큐-Queued Job 체이닝

Job chaining allows you to specify a list of queued jobs that should be run in sequence. If one job in the sequence fails, the rest of the jobs will not be run. To execute a queued job chain, you may use the `withChain` method on any of your dispatchable jobs:

Job 체이닝은 여러분이 queued 로 입력된 job이 순차적으로 실행되도록 목록을 지정할 수 있게 해줍니다. 이 순차적인 목록에서 하나의 job 이 실패하면, 나머지 job은 실행되지 않습니다. job 체이닝을 실행하려면, jobs 에 `withChain` 메소드를 사용하면 됩니다:

    ProvisionServer::withChain([
        new InstallNginx,
        new InstallPhp
    ])->dispatch();

### Validation Rule Objects
### 유효성 검사 Rule 객체

Validation rule objects provide a new, compact way of adding custom validation rules to your application. In previous versions of Laravel, the `Validator::extend` method was used to add custom validation rules via Closures. However, this can grow cumbersome. In Laravel 5.5, a new `make:rule` Artisan command will generate a new validation rule in the `app/Rules` directory:

    php artisan make:rule ValidName

A rule object only has two methods: `passes` and `message`. The `passes` method receives the attribute value and name, and should return `true` or `false` depending on whether the attribute value is valid or not. The `message` method should return the validation error message that should be used when validation fails:

    <?php

    namespace App\Rules;

    use Illuminate\Contracts\Validation\Rule;

    class ValidName implements Rule
    {
        /**
         * Determine if the validation rule passes.
         *
         * @param  string  $attribute
         * @param  mixed  $value
         * @return bool
         */
        public function passes($attribute, $value)
        {
            return strlen($value) === 6;
        }

        /**
         * Get the validation error message.
         *
         * @return string
         */
        public function message()
        {
            return 'The name must be six characters long.';
        }
    }

Once the rule has been defined, you may use it by simply passing an instance of the rule object with your other validation rules:

    use App\Rules\ValidName;

    $this->validate($request, [
        'name' => ['required', new ValidName],
    ]);

### Trusted Proxy Integration

When running applications behind a load balancer that terminates TLS / SSL certificates, you may notice your application sometimes does not generate HTTPS links. Typically this is because your application is being forwarded traffic from your load balancer on port 80 and does not know it should generate secure links.

To solve this, many Laravel users install the [Trusted Proxies](https://github.com/fideloper/TrustedProxy) package by Chris Fidao. Since this is such a common use case, Chris' package now ships with Laravel 5.5 by default.

A new `App\Http\Middleware\TrustProxies` middleware is included in the default Laravel 5.5 application. This middleware allows you to quickly customize the proxies that should be trusted by your application:

    <?php

    namespace App\Http\Middleware;

    use Illuminate\Http\Request;
    use Fideloper\Proxy\TrustProxies as Middleware;

    class TrustProxies extends Middleware
    {
        /**
         * The trusted proxies for this application.
         *
         * @var array
         */
        protected $proxies;

        /**
         * The current proxy header mappings.
         *
         * @var array
         */
        protected $headers = [
            Request::HEADER_FORWARDED => 'FORWARDED',
            Request::HEADER_X_FORWARDED_FOR => 'X_FORWARDED_FOR',
            Request::HEADER_X_FORWARDED_HOST => 'X_FORWARDED_HOST',
            Request::HEADER_X_FORWARDED_PORT => 'X_FORWARDED_PORT',
            Request::HEADER_X_FORWARDED_PROTO => 'X_FORWARDED_PROTO',
        ];
    }

### On-Demand Notifications

Sometimes you may need to send a notification to someone who is not stored as a "user" of your application. Using the new `Notification::route` method, you may specify ad-hoc notification routing information before sending the notification:

    Notification::route('mail', 'taylor@laravel.com')
                ->route('nexmo', '5555555555')
                ->send(new InvoicePaid($invoice));

### Renderable Mailables

Mailables can now be returned directly from routes, allowing you to quickly preview your mailable's designs in the browser:

    Route::get('/mailable', function () {
        $invoice = App\Invoice::find(1);

        return new App\Mail\InvoicePaid($invoice);
    });

### Renderable & Reportable Exceptions

In previous versions of Laravel, you may have had to resort to "type checking" in your exception handler in order to render a custom response for a given exception. For instance, you may have written code like this in your exception handler's `render` method:

    /**
     * Render an exception into an HTTP response.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Exception  $exception
     * @return \Illuminate\Http\Response
     */
    public function render($request, Exception $exception)
    {
        if ($exception instanceof SpecialException) {
            return response(...);
        }

        return parent::render($request, $exception);
    }

In Laravel 5.5, you may now define a `render` method directly on your exceptions. This allows you to place the custom response rendering logic directly on the exception, which helps avoid conditional logic accumulation in your exception handler. If you would like to also customize the reporting logic for the exception, you may define a `report` method on the class:

    <?php

    namespace App\Exceptions;

    use Exception;

    class SpecialException extends Exception
    {
        /**
         * Report the exception.
         *
         * @return void
         */
        public function report()
        {
            //
        }

        /**
         * Report the exception.
         *
         * @param  \Illuminate\Http\Request
         * @return void
         */
        public function render($request)
        {
            return response(...);
        }
    }

### Request Validation

The `Illuminate\Http\Request` object now provides a `validate` method, allowing you to quickly validate an incoming request from a route Closure or controller:

    use Illuminate\Http\Request;

    Route::get('/comment', function (Request $request) {
        $request->validate([
            'title' => 'required|string',
            'body' => 'required|string',
        ]);

        // ...
    });

### Consistent Exception Handling

Validation exception handling is now consistent throughout the framework. Previously, there were multiple locations in the framework that required customization to change the default format for JSON validation error responses. In addition, the default format for JSON validation responses in Laravel 5.5 now adheres the following convention:

    {
        "message": "The given data was invalid.",
        "errors": {
            "field-1": [
                "Error 1",
                "Error 2"
            ],
            "field-2": [
                "Error 1",
                "Error 2"
            ],
        }
    }

All JSON validation error formatting can be controlled by defining a single method on your `App\Exceptions\Handler` class. For example, the following customization will format JSON validation responses using the Laravel 5.4 convention.

    use Illuminate\Validation\ValidationException;

    /**
     * Convert a validation exception into a JSON response.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Illuminate\Validation\ValidationException  $exception
     * @return \Illuminate\Http\JsonResponse
     */
    protected function invalidJson($request, ValidationException $exception)
    {
        return response()->json($exception->errors(), $exception->status);
    }

### Cache Locks

The Redis and Memcached cache drivers now have support for obtaining and releasing atomic "locks". This provides a simple method of obtaining arbitrary locks without worrying about race conditions. For example, before performing a task, you may wish to obtain a lock so no other processes attempt the same task that is already in progress:

    if (Cache::lock('lock-name', 60)->get()) {
        // Lock obtained for 60 seconds, continue processing...

        Cache::lock('lock-name')->release();
    } else {
        // Lock was not able to be obtained...
    }

Or, you may pass a Closure to the `get` method. The Closure will only be executed if the lock can be obtained and the lock will automatically be released after the Closure is executed:

    Cache::lock('lock-name', 60)->get(function () {
        // Lock obtained for 60 seconds...
    });

In addition, you may "block" until the lock becomes available:

    if (Cache::lock('lock-name', 60)->block(10)) {
        // Wait for a maximum of 10 seconds for the lock to become available...
    }

### Blade Improvements

Programming a custom directive is sometimes more complex than necessary when defining simple, custom conditional statements. For that reason, Blade now provides a `Blade::if` method which allows you to quickly define custom conditional directives using Closures. For example, let's define a custom conditional that checks the current application environment. We may do this in the `boot` method of our `AppServiceProvider`:

    use Illuminate\Support\Facades\Blade;

    /**
     * Perform post-registration booting of services.
     *
     * @return void
     */
    public function boot()
    {
        Blade::if('env', function ($environment) {
            return app()->environment($environment);
        });
    }

Once the custom conditional has been defined, we can easily use it on our templates:

    @env('local')
        // The application is in the local environment...
    @else
        // The application is not in the local environment...
    @endenv

In addition to the ability to easily define custom Blade conditional directives, new shortcuts have been added to quickly check the authentication status of the current user:

    @auth
        // The user is authenticated...
    @endauth

    @guest
        // The user is not authenticated...
    @endguest

### New Routing Methods

If you are defining a route that redirects to another URI, you may now use the `Route::redirect` method. This method provides a convenient shortcut so that you do not have to define a full route or controller for performing a simple redirect:

    Route::redirect('/here', '/there', 301);

If your route only needs to return a view, you may now use the `Route::view` method. Like the `redirect` method, this method provides a simple shortcut so that you do not have to define a full route or controller. The `view` method accepts a URI as its first argument and a view name as its second argument. In addition, you may provide an array of data to pass to the view as an optional third argument:

    Route::view('/welcome', 'welcome');

    Route::view('/welcome', 'welcome', ['name' => 'Taylor']);
