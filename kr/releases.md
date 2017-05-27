# Release Notes
# 릴리즈 노트

- [Support Policy](#support-policy)
- [Laravel 5.2](#laravel-5.2)
- [Laravel 5.1.11](#laravel-5.1.11)
- [Laravel 5.1.4](#laravel-5.1.4)
- [Laravel 5.1](#laravel-5.1)
- [Laravel 5.0](#laravel-5.0)
- [Laravel 4.2](#laravel-4.2)
- [Laravel 4.1](#laravel-4.1)

<a name="support-policy"></a>
## Support Policy
## 지원 정책

For LTS releases, such as Laravel 5.1, bug fixes are provided for 2 years and security fixes are provided for 3 years. These releases provide the longest window of support and maintenance.

라라벨 5.1과 같은 LTS 릴리즈 동안에는, 2년간의 버그 픽스와 3년동안의 보안 패치가 지원됩니다. 이러한 릴리즈는 장기간에 걸친 지원과 유지보수를 제공합니다. 

For general releases, bug fixes are provided for 6 months and security fixes are provided for 1 year.

일반적인 릴리즈에서는 버그 픽스는 6개월 보안 패치는 1년동안 제공됩니다.

<a name="laravel-5.2"></a>
## Laravel 5.2
## 라라벨 5.2

Laravel 5.2 continues the improvements made in Laravel 5.1 by adding multiple authentication driver support, implicit model binding, simplified Eloquent global scopes, opt-in authentication scaffolding, middleware groups, rate limiting middleware, array validation improvements, and more.

라라벨 5.2는 다중 인증 드라이버 지원, 묵시적 모델 바인딩, 간략해진 Eloquent 글로벌 스코프, 내장된 인증 스캐폴딩, 미들웨어 그룹, 접근 속도 제한 미들웨어, 배열 유효성 검증의 개선등 라라벨 5.1에서 기능이 개선되었습니다. 

### Authentication Drivers / "Multi-Auth"
### 인증 드라이버 / "다중-인증"

In previous versions of Laravel, only the default, session-based authentication driver was supported out of the box, and you could not have more than one authenticatable model instance per application.

이번 버전의 라라벨에서는, 별다른 설정을 하지 않은경우, 오로지 기본으로 설정된 세션을 기반으로한 인증 드라이버만 지원되었고, 어플리케이션의 하나 이상의 인증 모델 인스턴스를 가질 수 없었습니다. 

However, in Laravel 5.2, you may define additional authentication drivers as well define multiple authenticatable models or user tables, and control their authentication process separately from each other. For example, if your application has one database table for "admin" users and one database table for "student" users, you may now use the `Auth` methods to authenticate against each of these tables separately.

하지만 라라벨 5.2에서는 사용자 모델 또는 사용자 테이블을 다중으로 정의하는 것처럼 추가적인 인증 드라이버를 정의하여 각각의 경우마다 다른 프로세스로 인증을 수행할 수 있습니다. 예를 들어 어플리케이션이 하나의 "admin" 사용자 테이블을 가지고 하나의 "student" 사용자 테이블을 가지는 경우, 각각의 테이블마다 별도로 `Auth` 메소드를 사용할 수 있습니다. 

### Authentication Scaffolding
### 인증 스캐폴딩

Laravel already makes it easy to handle authentication on the back-end; however, Laravel 5.2 provides a convenient, lightning-fast way to scaffold the authentication views for your front-end. Simply execute the `make:auth` command on your terminal:

라라벨은 이미 인증을 백엔드에서 손쉽게 할 수 있도록 하였지만; 라라벨 5.2에서는 보다 편리하면서 쉽고 빠르게 스캐폴딩을 사용하여 인증에 필요한 프론트엔드 뷰를 생성할 수 있게 제공합니다. 간단하게 `make:auth` 명령어를 터미널에서 실행하면 됩니다: 

    php artisan make:auth

This command will generate plain, Bootstrap compatible views for user login, registration, and password reset. The command will also update your routes file with the appropriate routes.

이 명령어는 사용자의 로그인, 가입, 패스워드 재설정을 위한 순수한 부트스트랩 호환 뷰 파일들을 생성할 것입니다. 이 명령어는 또한 여러분의 라우트 파일에 필요한 라우트를 추가할 것입니다. 

> **Note:** This feature is only meant to be used on new applications, not during application upgrades.

> **참고:** 이 기능은 어플리케이션의 업그레이드가 아니라 새로운 어플리케이션에서 사용되는 것을 의미합니다. 

### Implicit Model Binding
### 묵시적 모델 바인딩

Implicit model binding makes it painless to inject relevant models directly into your routes and controllers. For example, assume you have a route defined like the following:

묵시적 모델 바인딩은, 라우트나 컨트롤러에 연관된 모델을 손쉽게 바로 주입할 수 있도록 합니다. 예를 들어 다음과 같은 라우트가 정의 되어 있다고 생각해 보겠습니다: 

    use App\User;

    Route::get('/user/{user}', function (User $user) {
        return $user;
    });

In Laravel 5.1, you would typically need to use the `Route::model` method to instruct Laravel to inject the `App\User` instance that matches the `{user}` parameter in your route definition. However, in Laravel 5.2, the framework will **automatically** inject this model based on the URI segment, allowing you to quickly gain access to the model instances you need.

라라벨 5.1에서는 일반적으로 라우트에 선언된 `{user}` 파라미터와 일치하는 `App\User` 인스턴스를 라라벨에 주입하도록 지시하기 위해서는 `Route::model` 메소드를 사용할 필요가 있었습니다. 그러나, 라라벨 5.2에서는 프레임워크가 **자동으로** URI 세그먼트를 기반으로 모델을 주입해줄 것이기 때문에, 필요한 모델 인스턴스를 빠르게 획득할 수 있습니다. 

Laravel will automatically inject the model when the route parameter segment (`{user}`) matches the route Closure or controller method's corresponding variable name (`$user`) and the variable is type-hinting an Eloquent model class.

라라벨은 라우트 파라미터 세그먼트가 (`{user}`) 라우트 클로저와 매칭되거나 컨트롤러 메소드의 일치하는 변수 이름(`$user`)과 Eloquent 모델 클래스가 타입힌트되어 있다면 자동으로 모델을 주입할 것입니다. 

### Middleware Groups
### 미들웨어 그룹

Middleware groups allow you to group several route middleware under a single, convenient key, allowing you to assign several middleware to a route at once. For example, this can be useful when building a web UI and an API within the same application. You may group the session and CSRF routes into a `web` group, and perhaps the rate limiter in the `api` group.

미들웨어 그룹은 여러개의 라우트 미들웨어를 간단한 키를 사용하여 하나로 묶고, 이 미들웨어들을 한번에 라우트에 할당할 수 있게 해줍니다. 예를들어, 어플리케이션에서 웹 UI와 API를 구성할 때 유용 할 수 있습니다. 여러분은 세션과 CSRF 라우트 미들웨어를 `web` 그룹으로 묶을 수도 있고, `api` 그룹 안에서는 아마도 접근 속도 제한 미들웨어를 사용할 수도 있습니다. 

In fact, the default Laravel 5.2 application structure takes exactly this approach. For example, in the default `App\Http\Kernel.php` file you will find the following:

사실, 라라벨 5.2의 어플리케이션 구조에서는 기본적으로 이러한 기능을 이미 사용합니다. 예를 들어, `App\Http\Kernel.php`파일에는 기본적으로 다음과 같이 구성된 다음 내용을 확인할 수 있습니다:

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
        ],

        'api' => [
            'throttle:60,1',
        ],
    ];

Then, the `web` group may be assigned to routes like so:

그리고, `web` 그룹은 다음과 같이 라우트에 할당할 수 있습니다:

    Route::group(['middleware' => ['web']], function () {
        //
    });

However, keep in mind the `web` middleware group is *already* applied to your routes by default since the `RouteServiceProvider` includes it in the default middleware group.

다만, `web` 미들웨어 그룹은 `RouteServiceProvider`가 포함하고 있는 기본 미들웨어 그룹에 의해서 *이미* 적용되었다는 것을 주의하십시오. 

### Rate Limiting
### 접근 속도 제한

A new rate limiter middleware is now included with the framework, allowing you to easily limit the number of requests that a given IP address can make to a route over a specified number of minutes. For example, to limit a route to 60 requests every minute from a single IP address, you may do the following:

새로운 접근 속도 제한 미들웨어가 프레임워크에 포함되어 주어진 IP 에 대해서 주어진 시간내에 많은 접속을 요청하는 것을 제한할 수 있게 되었습니다. 예를들어 하나의 IP에 대해서 매분마다 60번의 요청을 할 수 있도록 제한하려면, 다음과 같이 하면 됩니다: 

    Route::get('/api/users', ['middleware' => 'throttle:60,1', function () {
        //
    }]);

### Array Validation
### 배열 유효성 검사

Validating array form input fields is much easier in Laravel 5.2. For example, to validate that each e-mail in a given array input field is unique, you may do the following:

라라벨 5.2에서 배열 form 필드 입력에 대해서 유효성을 검사 하는 것이 보다 손쉬워졌습니다. 예를 들어 주어진 배열 입력필드의 각각의 이메일이 고유한지 검증하려면 다음처럼 하면 됩니다: 

    $validator = Validator::make($request->all(), [
        'person.*.email' => 'email|unique:users'
    ]);

Likewise, you may use the `*` character when specifying your validation messages in your language files, making it a breeze to use a single validation message for array based fields:

마찬가지로, 언어 파일에서 유효성 검사 메세지를 지정하는데, `*` 문자를 사용할 수 있으며, 이는 배열기반의 필드에 대한 유효성 검사 메세지를 손쉽게 구성할 수 있게 만듭니다:

    'custom' => [
        'person.*.email' => [
            'unique' => 'Each person must have a unique e-mail address',
        ]
    ],

### Bail Validation Rule
### Bail 유효성 규칙

A new `bail` validation rule has been added, which instructs the validator to stop validating after the first validation failure for a given rule. For example, you may now prevent the validator from running a `unique` check if an attribute fails an `integer` check:

새로운 `bail` 유효성 규칙이 추가되어 유효성 검사 도중에 주어진 첫번째 규칙이 실패하면 이후의 검사를 중단할 수 있습니다. 예를 들어 `integer` 검사 규칙이 실패하면 `unique` 체크를 하지 않도록 방지할 수 있습니다.

    $this->validate($request, [
        'user_id' => 'bail|integer|unique:users'
    ]);

### Eloquent Global Scope Improvements
### Eloquent 글로벌 스코프 개선

In previous versions of Laravel, global Eloquent scopes were complicated and error-prone to implement; however, in Laravel 5.2, global query scopes only require you to implement a single, simple method: `apply`.

이전 버전의 라라벨에서는 Eloquent 글로벌 스코프는 복잡하고 구현하는데 에러가 발생하기 쉬웠습니다; 하지만 라라벨 5.2에서는 글로벌 쿼리 스코프는 단 하나의 `apply` 메소드만 구현하면 됩니다.

For more information on writing global scopes, check out the full [Eloquent documentation](/docs/{{version}}/eloquent#global-scopes).

글로벌 스코프를 작성하는 보다 자세한 내용은 [Eloquent 문서](/docs/{{version}}/eloquent#global-scopes)를 확인하십시오. 

<a name="laravel-5.1.11"></a>
## Laravel 5.1.11
## 라라벨 5.1.11

Laravel 5.1.11 introduces [authorization](/docs/{{version}}/authorization) support out of the box! Conveniently organize your application's authorization logic using simple callbacks or policy classes, and authorize actions using simple, expressive methods.

라라벨 5.1.11 에서는 [authorization-승인](/docs/{{version}}/authorization)기능이 도입되었습니다.

For more information, please refer to the [authorization documentation](/docs/{{version}}/authorization).

보다 자세한 사항은 [authorization-승인 문서](/docs/{{version}}/authorization)를 참고하십시오.

<a name="laravel-5.1.4"></a>
## Laravel 5.1.4
## 라라벨 5.1.4

Laravel 5.1.4 introduces simple login throttling to the framework. Consult the [authentication documentation](/docs/{{version}}/authentication#authentication-throttling) for more information.

라라벨 5.1.4 에서는 로그인 시도 제한 기능이 프레임워크에 도입되었습니다. 자세한 내용은 [인증 문서](/docs/{{version}}/authentication#authentication-throttling)를 참고하십시오.

<a name="laravel-5.1"></a>
## Laravel 5.1
## 라라벨 5.1

Laravel 5.1 continues the improvements made in Laravel 5.0 by adopting PSR-2 and adding event broadcasting, middleware parameters, Artisan improvements, and more.

라라벨 5.1은 PSR-2를 적용하였고, 라라벨 5.0 버전에서 이벤트 브로드캐스팅, 미들웨어 파라미터, 아티즌 개선등 기능이 개선되었습니다. 

### PHP 5.5.9+
### PHP 5.5.9이상

Since PHP 5.4 will enter "end of life" in September and will no longer receive security updates from the PHP development team, Laravel 5.1 requires PHP 5.5.9 or greater. PHP 5.5.9 allows compatibility with the latest versions of popular PHP libraries such as Guzzle and the AWS SDK.

PHP5.4가 2015년 9월에 "지원종료"되고, PHP 개발팀에서 보안 업데이트를 받을 수 없게 되므로, 라라벨 5.1은 PHP 5.5.9 버전 이상의 환경을 필요로 합니다. PHP 5.5.9는 Guzzle와 AWS SDK 처럼 인기있는 PHP 라이브러리의 최신 버전과 호환됩니다.

### LTS
### LTS

Laravel 5.1 is the first release of Laravel to receive **long term support**. Laravel 5.1 will receive bug fixes for 2 years and security fixes for 3 years. This support window is the largest ever provided for Laravel and provides stability and peace of mind for larger, enterprise clients and customers.

라라벨 5.1 은 첫번째 **장기 지원** 릴리즈 버전입니다. 라라벨 5.1은 2년간 버그 픽스와 3년의 보안 패치를 지원합니다. 이 지원 기간은 지금까지 제공된 릴리즈 중에서 가장 긴 지원 기간이며, 기업 고객과 그 사용자들에게 안정성과 편안함을 제공할 것입니다. 

### PSR-2
### PSR-2

The [PSR-2 coding style guide](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md) has been adopted as the default style guide for the Laravel framework. Additionally, all generators have been updated to generate PSR-2 compatible syntax.

[PSR-2 코딩 스타일 가이드](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md)가 라라벨 프레임워크의 표준 스타일 가이드로 적용되었습니다. 또한 모든 문법이 PSR-2와 호환되도록 재작성 되어 수정되었습니다.

### Documentation
### 문서

Every page of the Laravel documentation has been meticulously reviewed and dramatically improved. All code examples have also been reviewed and expanded to provide more relevance and context.

모든 라라벨 문서의 페이지들이 다시 검토되어, 많은 부분이 개선되었습니다. 모든 코드 예제들 또한 다시 확인되고, 보다 적절한 문맥에 맞게 수정되었습니다. 

### Event Broadcasting
### 이벤트 브로드캐스팅

In many modern web applications, web sockets are used to implement real-time, live-updating user interfaces. When some data is updated on the server, a message is typically sent over a websocket connection to be handled by the client.

많은 모던 Web 어플리케이션들에서, 실시간 처리와, 라이브 업데이트를 지원하는 사용자 인터페이스를 구현하기 위해서 웹 소켓을 사용하고 있습니다. 서버에서 데이터가 업데이트 되면, 메세지가 웹 소켓 커넥션을 통해서 클라이언트에서 처리되도록 전달됩니다. 

To assist you in building these types of applications, Laravel makes it easy to "broadcast" your events over a websocket connection. Broadcasting your Laravel events allows you to share the same event names between your server-side code and your client-side JavaScript framework.

이러한 유형의 어플리케이션을 구축하는 데 도움이 되도록 라라벨은 이벤트를 웹 소켓 연결 상에 쉽게 "브로드 캐스트"할 수 있도록 했습니다. 라라벨 이벤트를 브로드 캐스트하는 것은  같은 이벤트 이름을 서버 사이드의 코드와 클라이언트 사이드 JavaScript 프레임 워크와 공유 할 수 있게 해줍니다.

To learn more about event broadcasting, check out the [event documentation](/docs/{{version}}/events#broadcasting-events).

이벤트 브로드캐스팅에 대한 더 자세한 사항은, [이벤트 문서](/docs/{{version}}/events#broadcasting-events)를 참고하십시오.

### Middleware Parameters
### 미들웨어 파라미터

Middleware can now receive additional custom parameters. For example, if your application needs to verify that the authenticated user has a given "role" before performing a given action, you could create a `RoleMiddleware` that receives a role name as an additional argument:

미들웨어는 이제, 추가적인 사용자 정의 파라미터를 받을 수 있습니다. 예를 들어 어플리케이션에서 인증된 사용자가 주어진 액션을 처리할 수 있는 "역할"을 가지고 있는지 확인할 필요가 있다면, 추가적인 인자로 롤의 이름을 전달받는 `RoleMiddleware`를 생성할 수 있습니다. 

    <?php

    namespace App\Http\Middleware;

    use Closure;

    class RoleMiddleware
    {
        /**
         * Run the request filter.
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

미들웨어 파라미터는 라우트를 정의 할 때 지정된 미들웨어 이름과 파라미터를 `:` 로 구분하여 지정할 수 있습니다. 여러개의 파라미터는 쉼표로 구분합니다.

    Route::put('post/{id}', ['middleware' => 'role:editor', function ($id) {
        //
    }]);

For more information on middleware, check out the [middleware documentation](/docs/{{version}}/middleware).

미들웨어에 대한 보다 자세한 정보는 [미들웨어 문서](/docs/{{version}}/middleware)를 확인하십시오. 

### Testing Overhaul
### 테스팅의 개편

The built-in testing capabilities of Laravel have been dramatically improved. A variety of new methods provide a fluent, expressive interface for interacting with your application and examining its responses. For example, check out the following test:

라라벨에 내장된 테스팅 능력이 크게 향상되었습니다. 어플리케이션 그리고 관련된 응답을들 테스트 하기 위해서 유연하고 보다 풍부한 표현이 가능한 인터페이스를 제공하는 다양한 메소드들이 제공됩니다. 다음의 테스트를 확인해 보겠습니다.

    public function testNewUserRegistration()
    {
        $this->visit('/register')
             ->type('Taylor', 'name')
             ->check('terms')
             ->press('Register')
             ->seePageIs('/dashboard');
    }

For more information on testing, check out the [testing documentation](/docs/{{version}}/testing).

테스팅과 관련된 보다 자세한 사항은 [테스팅 문서](/docs/{{version}}/testing)를 확인하십시오.

### Model Factories
### 모델 팩토리

Laravel now ships with an easy way to create stub Eloquent models using [model factories](/docs/{{version}}/testing#model-factories). Model factories allow you to easily define a set of "default" attributes for your Eloquent model, and then generate test model instances for your tests or database seeds. Model factories also take advantage of the powerful [Faker](https://github.com/fzaninotto/Faker) PHP library for generating random attribute data:

라라벨은 이제, [모델 팩토리](/docs/{{version}}/testing#model-factories)를 사용하여 Eloquent 모델의 스텁을 쉽게 만드는 방법이 제공됩니다. 모델 팩토리는 여러분이 Eloquent 모델을 위한 "기본" 속성들의 세트를 손쉽게 정의하도록 하고, 테스트나 데이터베이스 시드를 위한 테스트용 모델 인스턴스 생성하도록 합니다. 모델 팩토리는 또한 랜덤한 속성 데이터를 생성하는 [Faker](https://github.com/fzaninotto/Faker) PHP 라이브러리를 사용하는 강력한 장점을 가집니다. 

    $factory->define(App\User::class, function ($faker) {
        return [
            'name' => $faker->name,
            'email' => $faker->email,
            'password' => str_random(10),
            'remember_token' => str_random(10),
        ];
    });

For more information on model factories, check out [the documentation](/docs/{{version}}/testing#model-factories).

모델 팩토리에 대한 보다 자세한 사항은 [문서](/docs/{{version}}/testing#model-factories)를 참조하십시오.

### Artisan Improvements
### 아티즌의 향상

Artisan commands may now be defined using a simple, route-like "signature", which provides an extremely simple interface for defining command line arguments and options. For example, you may define a simple command and its options like so:

아티즌 명령어는 이제, 커맨드 라인 인자와 옵션을 정의하기 위한 매우 간단한 인터페이스를 제공하는, 라우트와 비슷한 "시그니처"를 사용하여 손쉽게 정의될 수 있습니다. 예를 들어, 다음처럼 간단한 명령과 옵션을 정의할 수 있습니다.

    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'email:send {user} {--force}';

For more information on defining Artisan commands, consult the [Artisan documentation](/docs/{{version}}/artisan).

아티즌 명령어에 대한 보다 자세한 사항은 [아티즌 문서](/docs/{{version}}/artisan)를 참고하십시오.

### Folder Structure
### 폴더 구조

To better express intent, the `app/Commands` directory has been renamed to `app/Jobs`. Additionally, the `app/Handlers` directory has been consolidated into a single `app/Listeners` directory which simply contains event listeners. However, this is not a breaking change and you are not required to update to the new folder structure to use Laravel 5.1.

보다 의도를 알기 쉽게 하기 위해서, `app/Commands` 디렉토리가 `app/Jobs`로 이름이 ​​변경되었습니다. 또한 `app/Handlers` 디렉토리는 이벤트 리스너 전용 `app/Listeners` 에 통합되었습니다. 그러나 이것은 호환성을 무너 뜨리는 변경이 아니기 때문에, Laravel 5.1의 새로운 폴더 구조로 변경 할 필요는 없습니다.

### Encryption
### 암호화

In previous versions of Laravel, encryption was handled by the `mcrypt` PHP extension. However, beginning in Laravel 5.1, encryption is handled by the `openssl` extension, which is more actively maintained.

이전 버전의 Laravel은 PHP의 mcrypt extension을 통해 암호화가 이루어지고 있었습니다. 그러나, Laravel5.1 에서는 더 활발하게 관리가 이루어지고 있는 openssl extension을 사용하여 암호화됩니다.

<a name="laravel-5.0"></a>
## Laravel 5.0
## 라라벨 5.0

Laravel 5.0 introduces a fresh application structure to the default Laravel project. This new structure serves as a better foundation for building a robust application in Laravel, as well as embraces new auto-loading standards (PSR-4) throughout the application. First, let's examine some of the major changes:

라라벨 5.0 에서는 완전히 새로운 어플리케이션 구조가 구성되었습니다. 이 새로운 구조는 라라벨이 보다 나은 어플리케이션을 구축하기 위한 좋은 기초가 되며, 어플리케이션에는 새로운 auto-loading 기준인 (PSR-4)가 적용되었습니다. 먼저 주요한 변경 사항들을 확인해 보겠습니다:

### New Folder Structure
### 새로운 폴더 구조

The old `app/models` directory has been entirely removed. Instead, all of your code lives directly within the `app` folder, and, by default, is organized to the `App` namespace. This default namespace can be quickly changed using the new `app:name` Artisan command.

이전의 `app/modules` 디렉토리는 완전히 제거되었습니다. 대신에, 여러분의 모든 코드는 `app` 폴더안으로 옮겨졌고, 기본적으로 `App` 네임스페이스 이름으로 구성되었습니다. 이 기본 네임스페이스 는 `app:name` 아티즌 명령어를 통해서 손쉽게 변경가능합니다. 

Controllers, middleware, and requests (a new type of class in Laravel 5.0) are now grouped under the `app/Http` directory, as they are all classes related to the HTTP transport layer of your application. Instead of a single, flat file of route filters, all middleware are now broken into their own class files.

컨트롤러, 미들웨어, 그리고 request-요청 ( 라라벨 5.0 의 새로운 클래스 타입)은 어플리케이션의 HTTP 전송 계층과 관련된 클래스로, `app/Http` 디렉토리 아래에 위치합니다. 구조 없이 하나로 되어 있던 라우트 필터, 전체 미들웨어는 각각의 클래스 파일로 분할되었습니다. 

A new `app/Providers` directory replaces the `app/start` files from previous versions of Laravel 4.x. These service providers provide various bootstrapping functions to your application, such as error handling, logging, route loading, and more. Of course, you are free to create additional service providers for your application.

이전의 라라벨 4.X 버전의 `app/start` 파일들이 새로운 `app/Providers` 디렉토리로 대체되었습니다. 이 서비스 프로바이더들은 에러 핸들링, 로깅, 라우트 로딩등의 어플리케이션의 부트스트래핑을 위한 다양한 기능을 제공합니다. 물론 어플리케이션에서 필요한 추가적인 서비스 프로바이더들을 자유롭게 추가할 수 있습니다. 

Application language files and views have been moved to the `resources` directory.

어플리케이션의 언어 파일과 뷰 파일들은 `resources` 디렉토리로 이동되었습니다. 

### Contracts
### Contracts

All major Laravel components implement interfaces which are located in the `illuminate/contracts` repository. This repository has no external dependencies. Having a convenient, centrally located set of interfaces you may use for decoupling and dependency injection will serve as an easy alternative option to Laravel Facades.

모든 라라벨의 주요 컴포넌트들은 `illuminate/contracts` 저장소에 있는 인터페이스들을 구현하고 있습니다. 이 저장소는 어떠한 외부 의존성도 가지고 있지 않습니다. 보다 편의를 위해서, 인터페이스들이 한곳에 모여서 위치하고, 이 인터페이스들을 사용한 디커플링과 의존성 주입은 라라벨 파사드를 통해서 다른 대안을 손쉽게 사용할 수 있게 합니다 

For more information on contracts, consult the [full documentation](/docs/{{version}}/contracts).

contracts 에 관한 보다 자세한 내용은 [문서](/docs/{{version}}/contracts)를 참고하십시오. 

### Route Cache
### 라우트 캐시

If your application is made up entirely of controller routes, you may utilize the new `route:cache` Artisan command to drastically speed up the registration of your routes. This is primarily useful on applications with 100+ routes and will **drastically** speed up this portion of your application.

어플리케이션이 컨트롤러의 라우트만으로 구성된 경우에, `route:cache` 아티즌 명령어를 사용하여, 라우트 등록 속도를 대대적으로 개선할 수 있습니다. 이 기능은 주로 어플리케이션에 100개 이상의 라우트가 있는 경우 유용하며, 어플리케이션의 라우트 부분을 **극적으로** 단축 할 수 있습니다. 

### Route Middleware
### 라우트 미들웨어

In addition to Laravel 4 style route "filters", Laravel 5 now supports HTTP middleware, and the included authentication and CSRF "filters" have been converted to middleware. Middleware provides a single, consistent interface to replace all types of filters, allowing you to easily inspect, and even reject, requests before they enter your application.

라라벨 4 스타일의 라우트 "필터"에 더하여, 라라벨 5 에서는 HTTP 미들웨어가 지원되고, 사용자 인증과 CSRF "필터"가 미들웨어로 변경되었습니다. 미들웨어는 request 가 어플리케이션에 진입하기 전에 손쉽게 확인하고, 거부할 수 있도록 모든 타입의 필터를 대체하기 위한 하나의 일관된 인터페이스를 제공합니다. 

For more information on middleware, check out [the documentation](/docs/{{version}}/middleware).

미들웨어 대한 보다 자세한 내용은 [문서](/docs/{{version}}/middleware)에서 확인하십시오.

### Controller Method Injection
### 컨트롤러 메소드 주입

In addition to the existing constructor injection, you may now type-hint dependencies on controller methods. The [service container](/docs/{{version}}/container) will automatically inject the dependencies, even if the route contains other parameters:

이미 존재하는 생성자에서의 주입에 더해, 컨트롤러의 메소드에서도 타입-힌트 의존성 주입을 사용할 수 있습니다. [서비스 컨테이너](/docs/{{version}}/container)는 라우트에 다른 파라미터를 포함하고 있더라도, 자동으로 의존성을 주입할 것입니다. 

    public function createPost(Request $request, PostRepository $posts)
    {
        //
    }

### Authentication Scaffolding
### 인증 스캐폴딩

User registration, authentication, and password reset controllers are now included out of the box, as well as simple corresponding views, which are located at `resources/views/auth`. In addition, a "users" table migration has been included with the framework. Including these simple resources allows rapid development of application ideas without bogging down on authentication boilerplate. The authentication views may be accessed on the `auth/login` and `auth/register` routes. The `App\Services\Auth\Registrar` service is responsible for user validation and creation.

사용자의 등록, 인증, 암호 재설정 컨트롤러는 별다른 설정 없이도 포함되어 있습니다. 동시에, 이에 대응하는 간단한 뷰가 `resources/views/auth` 에 위치하고 있습니다. 이에 더해, "users" 테이블 마이그레이션 파일이 프레임워크에 포함되어 있습니다. 이러한 간단한 리소스가 포함되어 있어서, 인증과 관련된 번거로운 작업 없이도 어플리케이션을 빠르게 구성할 수 있습니다. 인증 뷰는 `auth/login` 와 `auth/register` 라우트를 통해서 엑세스 할 수 있습니다. `App\Services\Auth\Registrar` 서비스가 사용자 검증과 생성과 관련된 역할을 합니다. 

### Event Objects
### 이벤트 객체

You may now define events as objects instead of simply using strings. For example, check out the following event:

이벤트는 이제 단순한 문자열 대신 객체로 정의됩니다. 다음의 이벤트 예제를 확인하십시오: 

    <?php

    class PodcastWasPurchased
    {
        public $podcast;

        public function __construct(Podcast $podcast)
        {
            $this->podcast = $podcast;
        }
    }

The event may be dispatched like normal:

다음과 같이 이벤트를 발생 시킬 수 있습니다:

    Event::fire(new PodcastWasPurchased($podcast));

Of course, your event handler will receive the event object instead of a list of data:

물론, 이벤트 핸들러는 데이터 목록 대신 이벤트 객체를 받을 것입니다.

    <?php

    class ReportPodcastPurchase
    {
        public function handle(PodcastWasPurchased $event)
        {
            //
        }
    }

For more information on working with events, check out the [full documentation](/docs/{{version}}/events).

이벤트의 동작과 관련된 보다 자세한 사항은 [문서](/docs/{{version}}/events)를 참고하십시오. 

### Commands / Queueing
### 명령어 / 큐

In addition to the queue job format supported in Laravel 4, Laravel 5 allows you to represent your queued jobs as simple command objects. These commands live in the `app/Commands` directory. Here's a sample command:

라라벨 4 에서 큐 작업 포맷을 지원하는데 더해, 라라벨 5 는 큐 작업을 간단한 명령어 객체로 나타낼 수 있게되었습니다. 이 명령어는 `app/Commands` 디렉토리에 위치합니다. 다음은 간단한 명령어 예입니다. 

    <?php

    class PurchasePodcast extends Command implements SelfHandling, ShouldBeQueued
    {
        use SerializesModels;

        protected $user, $podcast;

        /**
         * Create a new command instance.
         *
         * @return void
         */
        public function __construct(User $user, Podcast $podcast)
        {
            $this->user = $user;
            $this->podcast = $podcast;
        }

        /**
         * Execute the command.
         *
         * @return void
         */
        public function handle()
        {
            // Handle the logic to purchase the podcast...

            event(new PodcastWasPurchased($this->user, $this->podcast));
        }
    }

The base Laravel controller utilizes the new `DispatchesCommands` trait, allowing you to easily dispatch your commands for execution:

여러분의 명령어들을 손쉽게 수행할 수 있도록 라라벨의 기본 컨트롤러에서는 `DispatchesCommands` 트레이트-trait을 사용하고 있습니다. 

    $this->dispatch(new PurchasePodcastCommand($user, $podcast));

Of course, you may also use commands for tasks that are executed synchronously (are not queued). In fact, using commands is a great way to encapsulate complex tasks your application needs to perform. For more information, check out the [command bus](/docs/5.0/bus) documentation.

물론 작업을 동기적으로(큐를 통하지 않고) 실행하는 명령어를 사용할 수도 있습니다. 실제로, 명령어를 사용하는 것 자체는, 어플리케이션에서 수행해야 하는 복잡한 작업을 캡슐화 하기 위한 좋은 방법입니다. 보다 자세한 사항은 [command bus](/docs/5.0/bus) 문서를 확이하십시오. 

### Database Queue
### 데이터베이스 큐

A `database` queue driver is now included in Laravel, providing a simple, local queue driver that requires no extra package installation beyond your database software.

라라벨에 데이터베이스를 통한 간단한, 로컬 큐 드라이버를 제공하는 `database` 큐 드라이버가 포함되었습니다. 

### Laravel Scheduler
### 라라벨 스케줄러

In the past, developers have generated a Cron entry for each console command they wished to schedule. However, this is a headache. Your console schedule is no longer in source control, and you must SSH into your server to add the Cron entries. Let's make our lives easier. The Laravel command scheduler allows you to fluently and expressively define your command schedule within Laravel itself, and only a single Cron entry is needed on your server.

이전에는 개발자는 스케줄링을 원하는 각각의 콘솔 명령어를 Cron 항목으로 생성했었습니다. 그러나 이건 골치 아픈일입니다. 콘솔 스케줄링은 소스 관리되 되지 않고, Cron 항목을 추가하기 위해서는 SSH 를 통해서 서버에 접속해야만 합니다. 보다 손쉽게 하기 위해서 라라벨 명령어 스케줄러는 라라벨 자체에서 명령어 스케줄링을 보다 유연하고, 풍부한 표현이 가능하게 정의할 수 있도록 허용합니다. 서버에 필요한 Cron 항목은 단 한줄이면 충분합니다. 

It looks like this:

다음과 같이 정의할 수 있습니다:

    $schedule->command('artisan:command')->dailyAt('15:00');

Of course, check out the [full documentation](/docs/{{version}}/scheduling) to learn all about the scheduler!

스케줄러에 대해서 자세한 내용은 [문서](/docs/{{version}}/scheduling)를 참고하십시오!

### Tinker / Psysh
### Tinker / Psysh

The `php artisan tinker` command now utilizes [Psysh](https://github.com/bobthecow/psysh) by Justin Hileman, a more robust REPL for PHP. If you liked Boris in Laravel 4, you're going to love Psysh. Even better, it works on Windows! To get started, just try:

이제, `php artisan tinker` 명령어는 PHP REPL 인 Justin Hileman 의 강령한 [Psysh](https://github.com/bobthecow/psysh)를 사용합니다. 
라라벨 4의 Boris를 좋아했다면, Psysh는 더 좋아 하실 것입니다. 심지어, Windows에서도 동작합니다! 바로 사용해 보십시오.

    php artisan tinker

### DotEnv
### DotEnv

Instead of a variety of confusing, nested environment configuration directories, Laravel 5 now utilizes [DotEnv](https://github.com/vlucas/phpdotenv) by Vance Lucas. This library provides a super simple way to manage your environment configuration, and makes environment detection in Laravel 5 a breeze. For more details, check out the full [configuration documentation](/docs/{{version}}/installation#environment-configuration).

다양한 혼란을 가져왔던 중첩된 구동 환경 설정을 위한 디렉토리 구조 대신에, 라라벨 5는 이제 Vance Lucas의 [DotEnv](https://github.com/vlucas/phpdotenv)를 사용합니다. 이 라이브러리는 매우 간단한 방법으로 여러분의 구동 환경 설정을 구성할 수 있도록 하고, 라라벨 5에서 손쉽게 구동 환경을 확인할 수 있도록 만들어 줍니다. 보다 자세한 사항은 [설정 문서](/docs/{{version}}/installation#environment-configuration)를 확인하십시오. 

### Laravel Elixir
### 라라벨 엘릭서

Laravel Elixir, by Jeffrey Way, provides a fluent, expressive interface to compiling and concatenating your assets. If you've ever been intimidated by learning Grunt or Gulp, fear no more. Elixir makes it a cinch to get started using Gulp to compile your Less, Sass, and CoffeeScript. It can even run your tests for you!

Jeffrey Way의 라라벨 엘릭서는 assets 을 컴파일하고 합치기 위해서, 유연하고 풍부한 표현이 가능한 인터페이스를 제공합니다. Grunt 나 Gulp 를 배우는데 애를 먹었었다면, 더 이상 두려워할 필요가 없습니다. 엘릭서는 Gulp를 사용하여 Less, Sass 그리고 CoffeeScript를 컴파일하는것을 매우 쉽게 만들어 줍니다. 테스팅을 위해서 사용할 수도 있습니다! 

For more information on Elixir, check out the [full documentation](/docs/{{version}}/elixir).

엘릭서에 대한 보다 자세한 내용은 [문서](/docs/{{version}}/elixir)를 확인하십시오.

### Laravel Socialite
### 라라벨 Socialite

Laravel Socialite is an optional, Laravel 5.0+ compatible package that provides totally painless authentication with OAuth providers. Currently, Socialite supports Facebook, Twitter, Google, and GitHub. Here's what it looks like:

라라벨 Socialite는 라라벨 5.0+ 에 호환되는 옵션 패키지로, OAuth 프로바이더를 통한 손쉬운 인증을 제공합니다. 현재 Socialite가 지원하고 있는 것은 Facebook, Twitter, Google, GitHub입니다. 다음 예를 참고하십시오.

    public function redirectForAuth()
    {
        return Socialize::with('twitter')->redirect();
    }

    public function getUserFromProvider()
    {
        $user = Socialize::with('twitter')->user();
    }

No more spending hours writing OAuth authentication flows. Get started in minutes! The [full documentation](/docs/{{version}}/authentication#social-authentication) has all the details.

OAuth 인증을 구성하기 위해서 더 이상 시간 낭비할 필요가 없습니다. 몇 문안에 구성할 수 있을 정도입니다! 자세한 사항은 [문서](/docs/{{version}}/authentication#social-authentication)를 참고하십시오. 

### Flysystem Integration
### 파일시스템 통합

Laravel now includes the powerful [Flysystem](https://github.com/thephpleague/flysystem) filesystem abstraction library, providing pain free integration with local, Amazon S3, and Rackspace cloud storage - all with one, unified and elegant API! Storing a file in Amazon S3 is now as simple as:

라라벨은 이제, 로컬, 아마존 S3, 그리고 Rackspace 와 같은 클라우트 스토리지 서비스를 위해서, 일관되고 우아한 API를 제공하는 강력한 파일시스템 추상 라이브러리를 도입하였습니다! 아마존 S3 에 파일을 저장하는 것은 다음처럼 쉬워졌습니다. 

    Storage::put('file.txt', 'contents');

For more information on the Laravel Flysystem integration, consult the [full documentation](/docs/{{version}}/filesystem).

라라벨 파일시스템 통합에 대한 보다 자세한 내용은 [문서](/docs/{{version}}/filesystem)를 확인하십시오. 

### Form Requests
### Form Requests-요청

Laravel 5.0 introduces **form requests**, which extend the `Illuminate\Foundation\Http\FormRequest` class. These request objects can be combined with controller method injection to provide a boiler-plate free method of validating user input. Let's dig in and look at a sample `FormRequest`:

라라벨 5.0 에서는 `Illuminate\Foundation\Http\FormRequest` 클래스를 확장한 **form requests**를 도입하였습니다. 이 request 객체는 컨트롤러의 메소드 주입과 결합되어, 사용자 입력의 유효성을 검증하거는데 별다른 코드를 작성하지 않아도 되도록 합니다. `FormRequest` 샘플을 확인해 보겠습니다. 

    <?php

    namespace App\Http\Requests;

    class RegisterRequest extends FormRequest
    {
        public function rules()
        {
            return [
                'email' => 'required|email|unique:users',
                'password' => 'required|confirmed|min:8',
            ];
        }

        public function authorize()
        {
            return true;
        }
    }

Once the class has been defined, we can type-hint it on our controller action:

클래스를 정의하였다면, 컨트롤러 액션에 타입-힌트를 지정할 수 있습니다.

    public function register(RegisterRequest $request)
    {
        var_dump($request->input());
    }

When the Laravel service container identifies that the class it is injecting is a `FormRequest` instance, the request will **automatically be validated**. This means that if your controller action is called, you can safely assume the HTTP request input has been validated according to the rules you specified in your form request class. Even more, if the request is invalid, an HTTP redirect, which you may customize, will automatically be issued, and the error messages will be either flashed to the session or converted to JSON. **Form validation has never been more simple.** For more information on `FormRequest` validation, check out the [documentation](/docs/{{version}}/validation#form-request-validation).

라라벨의 서비스 컨테이너가 `FromRequest` 인스턴스의 주입을 식별하게 되면, 그 요청은 **자동으로 유효성 검증**이 될 것입니다. 이것은 여러분의 컨트롤러 액션에 호출되면 HTTP request 입력이 안전하게 지정한 form reqeust 클래스에 의해서 유효성이 검증되었다는 것을 의미합니다. 이에 더해서, request 가 유효하지 않다면 사용자가 정의한 HTTP 리다이렉션이 자동으로 발생하여 에러 메세지가 세션에 자동으로 임시 저장되거나, JSON으로 변환된다는 것을 의미합니다. **form 유효성 검사가 이보다 더 간단할 수는 없을 것입니다.** `FormRequest` 유효성 검사에 대한 보다 자세한 사항은 [문서](/docs/{{version}}/validation#form-request-validation)를 확인하시기 바랍니다. 

### Simple Controller Request Validation
### 간단한 컨트롤러 Request 유효성 검증

The Laravel 5 base controller now includes a `ValidatesRequests` trait. This trait provides a simple `validate` method to validate incoming requests. If `FormRequests` are a little too much for your application, check this out:

이제 라라벨 5의 기본 컨트롤러는 `ValidatesRequests` 트레이트-trait을 포함하고 있습니다. 이 트레이트-trait은 유입되는 reqeust-요청을 검사할 수 있는 간단한 `validate` 메소드를 제공합니다. 어플리케이션의 `FormRequest` 가 너무 크다면 다음을 확인하십시오: 

    public function createPost(Request $request)
    {
        $this->validate($request, [
            'title' => 'required|max:255',
            'body' => 'required',
        ]);
    }

If the validation fails, an exception will be thrown and the proper HTTP response will automatically be sent back to the browser. The validation errors will even be flashed to the session! If the request was an AJAX request, Laravel even takes care of sending a JSON representation of the validation errors back to you.

유효성 검사가 실패한다면, exception-예외 가 던져지고 적당한 HTTP response 가 자동으로 브라우저로 보내질 것입니다. 유효성 검사 에러는 세션에 저장될 것입니다! request-요청 이 AJAX request-요청 이라면, 라라벨은 유효성 검사 에러를 JSON으로 구성하여 돌려보낼 것입니다. 

For more information on this new method, check out [the documentation](/docs/{{version}}/validation#validation-quickstart).

이 새로운 메소드에 대한 보다 자세한 내용은 [문서](/docs/{{version}}/validation#validation-quickstart)를 참고하십시오.

### New Generators
### 새로운 Generators

To complement the new default application structure, new Artisan generator commands have been added to the framework. See `php artisan list` for more details.

새로운 기본 어플리케이션의 구조를 구성하기 위한, 새로운 아티즌 generator 명령어가 프레임워크에 추가되었습니다. `php artisan list` 를 통해서 자세한 내용을 확인하십시오. 

### Configuration Cache
### 설정 캐시

You may now cache all of your configuration in a single file using the `config:cache` command.

`config:cache` 명령어를 사용하여 설정을 하나의 파일로 구서하여 캐시할 수 있습니다. 

### Symfony VarDumper
### Symfony VarDumper

The popular `dd` helper function, which dumps variable debug information, has been upgraded to use the amazing Symfony VarDumper. This provides color-coded output and even collapsing of arrays. Just try the following in your project:

변수 디버그 정보를 덤프하는 인기있는 `dd` 헬퍼 함수가 Symfony VarDumper 를 사용하도록 업그레이드 되었습니다. 이를 통해서 색상이 지정된 결과와 열고 닫을 수 있는 배열형태로 출력이 가능합니다. 프로젝트에서 다음과 같이 사용 해보십시오. 

    dd([1, 2, 3]);

<a name="laravel-4.2"></a>
## Laravel 4.2
## 라라벨 4.2

The full change list for this release by running the `php artisan changes` command from a 4.2 installation, or by [viewing the change file on Github](https://github.com/laravel/framework/blob/4.2/src/Illuminate/Foundation/changes.json). These notes only cover the major enhancements and changes for the release.

이번 릴리즈의 전체 변경 목록은 설치한 4.2에서 `php artisan changes` 명령어를 실행하여 표시됩니다. 혹은 [Github에서 변경 내역 파일을 확인하십시오.](https://github.com/laravel/framework/blob/4.2/src/Illuminate/Foundation/changes.json). 이 노트에서는 릴리즈의 주요한 개선사항과 변경사항들만 기록되어 있습니다. 

> **Note:** During the 4.2 release cycle, many small bug fixes and enhancements were incorporated into the various Laravel 4.1 point releases. So, be sure to check the change list for Laravel 4.1 as well!

> **주의:** 4.2 릴리즈 사이클 동안, 다수의 소소한 버그 수정과 개선이 라라벨 4.1에 포함되어 왔습니다. 따라서, 라라벨 4.1의 변경 사항도 주의 깊게 확인해야 합니다

### PHP 5.4 Requirement
### PHP 5.4 이상 필요

Laravel 4.2 requires PHP 5.4 or greater. This upgraded PHP requirement allows us to use new PHP features such as traits to provide more expressive interfaces for tools like [Laravel Cashier](/docs/billing). PHP 5.4 also brings significant speed and performance improvements over PHP 5.3.

라라벨 4.2는 PHP 5.4 또는 그 이상을 필요로 합니다. PHP 필요사항이 업그레이드 된 것은 [라라벨 캐셔](/docs/billing)와 같은 툴에서 보다 유연한 인터페이스를 제공하게 해주는 트레이트-trait와 같은 새로운 PHP 기능들 사용할 수 있도록 해줍니다. PHP 5.4 는 또한 PHP 5.3 보다 유효한 속도와 성능 향상을 가져옵니다. 

### Laravel Forge
### 라라벨 Forge

Laravel Forge, a new web based application, provides a simple way to create and manage PHP servers on the cloud of your choice, including Linode, DigitalOcean, Rackspace, and Amazon EC2. Supporting automated Nginx configuration, SSH key access, Cron job automation, server monitoring via NewRelic & Papertrail, "Push To Deploy", Laravel queue worker configuration, and more, Forge provides the simplest and most affordable way to launch all of your Laravel applications.

라라벨 Forge 는 새로운 웹 기반의 어플리케이션으로 Linode, DigitalOcean, Rackspace, 그리고 아마존 EC2 와 같은 클라우드 에서 PHP 서버를 생성하고 관리하는 간단한 방법을 제공합니다. 자동화된 Nginx 설정, SSH key 엑세스, Cron 작업의 자동화, NewRelic & Papertrail 을 통한 서버 모니터링, "Push To Deploy", 라라벨 큐 worker 설정등 Forge 는 가장 간단하고, 가장 손쉬운 방법으로 라라벨 어플리케이션을 구동할 수 있도록 해줍니다. 

The default Laravel 4.2 installation's `app/config/database.php` configuration file is now configured for Forge usage by default, allowing for more convenient deployment of fresh applications onto the platform.

설치된 라라벨 4.2의 `app/config/database.php` 설정 파일은 보다 편리한 방법으로 어플리케이션을 플랫폼에 배포할 수 있도록 기본적으로 Forge 를 사용하도록 설정되어 있습니다. 

More information about Laravel Forge can be found on the [official Forge website](https://forge.laravel.com).

라라벨 Forge 에 대한 보다 자세한 내용은 [공식 Forge 웹사이트](https://forge.laravel.com) 에서 찾으실 수 있습니다. 

### Laravel Homestead
### 라라벨 홈스테드

Laravel Homestead is an official Vagrant environment for developing robust Laravel and PHP applications. The vast majority of the boxes' provisioning needs are handled before the box is packaged for distribution, allowing the box to boot extremely quickly. Homestead includes Nginx 1.6, PHP 5.6, MySQL, Postgres, Redis, Memcached, Beanstalk, Node, Gulp, Grunt, & Bower. Homestead includes a simple `Homestead.yaml` configuration file for managing multiple Laravel applications on a single box.

라라벨 홈스테드는 강력한 라라벨 및 PHP 어플리케이션을 배포하기 위한 공식 Vagrant 환경입니다. 박스의 프로비저닝의 거대한 다수는 필요는 처리하는 것 박스가 배포를 위해서 패킹되기 전에 필요로 하는 것 박스가 부트 되기 위해서 외부적으로 빠르게 

배포를 위해서 box가 pack 되기 전에 필요한 box의 준비 작업은 처리된 상태이기 때문에, 매우 빠르게 box를 시작할 수 있습니다. 홈스테드는 Nginx 1.6, PHP 5.6 MySQL, Postgres, Redis, Memcached, Beanstalk, Node, Gulp, Grunt & Bower 를 포함하고 있습니다. 홈스테드는 하나의 box 에서 여러개의 라라벨 어플리케이션을 관리하기 위해서 하나의 `Homestead.yaml` 설정 파일을 가지고 있습니다. 

The default Laravel 4.2 installation now includes an `app/config/local/database.php` configuration file that is configured to use the Homestead database out of the box, making Laravel initial installation and configuration more convenient.

설치된 라라벨 4.2는 이제 기본적으로 별다른 설정없이도 홈스테드 데이터베이스를 사용할 수 있도록 하여 라라벨의 초기 설정과 설정이 편리해 질 수 있또록 설정된 `app/config/local/database.php` 설정 파일을 포함하고 있습니다. 

The official documentation has also been updated to include [Homestead documentation](/docs/homestead).

공식 문서는 또한 포함된 [홈스테드 문서](/docs/homestead)를 통해서 업데이트 되었습니다. 

### Laravel Cashier
### 라라벨 캐셔

Laravel Cashier is a simple, expressive library for managing subscription billing with Stripe. With the introduction of Laravel 4.2, we are including Cashier documentation along with the main Laravel documentation, though installation of the component itself is still optional. This release of Cashier brings numerous bug fixes, multi-currency support, and compatibility with the latest Stripe API.

라라벨 캐셔는 Stripe 를 사용하여 구독 결제를 관리하기 위한 간단하고, 풍부한 표현이 가능한 라이브러리 입니다. 라라벨 4.2 에서 도입되고, 캐셔 문서또한 주요 라라벨 메뉴얼에 포함되었지만, 컴포넌트의 설치는 아직 선택적으로 결정해야 합니다. 이번 릴리즈의 캐셔는 많은 버그가 수정되어, 다수의 통화를 지원하고, 최신 Stripe API와 호환됩니다. 

### Daemon Queue Workers
### 데몬 큐 Worker

The Artisan `queue:work` command now supports a `--daemon` option to start a worker in "daemon mode", meaning the worker will continue to process jobs without ever re-booting the framework. This results in a significant reduction in CPU usage at the cost of a slightly more complex application deployment process.

아티즌 `queue:work` 명령어는 이제 worker를 "데몬 모드"로 시작할 수 있는 `--daemon` 옵션을 지원합니다. 이는 worker 가 프레임워크를 재시작 하지 않고도 작업을 계속 처리 할 수 있다는 것을 의미합니다. 그 결과 CPU 사용율이 효과적으로 줄어들지만, 어플리케이션의 배포 프로세스가 다소 복잡해집니다. 

More information about daemon queue workers can be found in the [queue documentation](/docs/queues#daemon-queue-worker).

데몬 큐 worker 에 대한 보다 자세한 정보는 [큐 문서](/docs/queues#daemon-queue-worker)에서 확인할 수 있습니다. 

### Mail API Drivers
### 메일 API 드라이버

Laravel 4.2 introduces new Mailgun and Mandrill API drivers for the `Mail` functions. For many applications, this provides a faster and more reliable method of sending e-mails than the SMTP options. The new drivers utilize the Guzzle 4 HTTP library.

라라벨 4.2는 `Mail` 기능을 위해서 새로운 Mailgun 와 Mandrill API 드라이버를 도입했습니다. 이는 많은 어플리케이션에서 SMTP를 통한것 보다 빠르고, 신뢰할 수 있게 이메일을 보내는 기능을 제공합니다. 새로운 드라이버는 Guzzle 4 HTTP 라이브러리를 사용하고 있습니다.

### Soft Deleting Traits
### Soft 삭제 트레이트-Trait

PHP 5.4 트레이트-trait을 통해서 "soft 삭제" 와 "글로벌 스코프"를 위한 보다 깔끔한 아키텍처가 도입되었습니다. 이 새로운 아키텍처는 비슷한 글로벌 트레이트의 손쉬운 생성과 프레임워크 안에서 깔끔한 관심의 분리를 가능하게 합니다. 

More information on the new `SoftDeletingTrait` may be found in the [Eloquent documentation](/docs/eloquent#soft-deleting).

새로운 `SoftDeletingTrait`에 대한 보다 자세한 정보는 [Eloquent 문서](/docs/eloquent#soft-deleting)에서 확인할 수 있습니다. 

### Convenient Auth & Remindable Traits
### 편리한 인증과 암호 알림 트레이트-trait

The default Laravel 4.2 installation now uses simple traits for including the needed properties for the authentication and password reminder user interfaces. This provides a much cleaner default `User` model file out of the box.

기본적인 라라벨 4.2 설치는 이제 인증과 패스워드 리마인더 유저 인터페이스를 위해서 필요한 속성을 포함하기 위해서 간단한 트레이트를 사용합니다. 이것은 처음부터 포함되어 있는 기본 `User` 모델 파일을 보다 깔끔하게 해줍니다. 

### "Simple Paginate"
### "간단한 페이징"

A new `simplePaginate` method was added to the query and Eloquent builder which allows for more efficient queries when using simple "Next" and "Previous" links in your pagination view.

여러분의 페이지 뷰에서 단순한 "다음"과 "이전" 링크를 사용할 때, 보다 효율적인 쿼리를 위해서, 새로운 `simplePaginate` 메소드가 쿼리와 Eloquent 빌더에 추가되었습니다. 

### Migration Confirmation
### 마이그레이션 확인

In production, destructive migration operations will now ask for confirmation. Commands may be forced to run without any prompts using the `--force` command.

실제 운영 서버에서, 위험한 마이그레이션 작업은 확인 과정을 묻게 되었습니다. `--force` 명령어를 사용하여, 프롬프트 없이 강제로 실행하게 할 수 있습니다. 

<a name="laravel-4.1"></a>
## Laravel 4.1
## 라라벨 4.1

### Full Change List
### 전체 변경 목록

The full change list for this release by running the `php artisan changes` command from a 4.1 installation, or by [viewing the change file on Github](https://github.com/laravel/framework/blob/4.1/src/Illuminate/Foundation/changes.json). These notes only cover the major enhancements and changes for the release.

이번 릴리즈의 전체 변경 목록은 설치한 4.1에서 `php artisan changes` 명령어를 실행하여 표시됩니다. 혹은 [Github에서 변경 내역 파일을 확인하십시오.](https://github.com/laravel/framework/blob/4.1/src/Illuminate/Foundation/changes.json). 이 노트에서는 릴리즈의 주요한 개선사항과 변경사항들만 기록되어 있습니다. 

### New SSH Component
### 새로운 SSH 컴포넌트

An entirely new `SSH` component has been introduced with this release. This feature allows you to easily SSH into remote servers and run commands. To learn more, consult the [SSH component documentation](/docs/4.1/ssh).

이 릴리즈에서 완전히 새로운 `SSH` 컴포넌트가 도입되었습니다. 이 기능은 여러분이 손쉽게 원격의 서버에 SSH 로 접속하고 명령어를 실행할 수 있도록 합니다. 이에 대해서 알아보려면 [SSH 컴포넌트 문서](/docs/4.1/ssh)를 참고하십시오. 

The new `php artisan tail` command utilizes the new SSH component. For more information, consult the `tail` [command documentation](http://laravel.com/docs/4.1/ssh#tailing-remote-logs).

새로운 `php artisan tail` 명령어는 새로운 SSH 컴포넌트를 사용합니다. 보다 자세한 정보는 [명령어 문서](http://laravel.com/docs/4.1/ssh#tailing-remote-logs)를 참고하십시오. 

### Boris In Tinker
### Tinker 에서의 Boris

The `php artisan tinker` command now utilizes the [Boris REPL](https://github.com/d11wtq/boris) if your system supports it. The `readline` and `pcntl` PHP extensions must be installed to use this feature. If you do not have these extensions, the shell from 4.0 will be used.

`php artisan tinker` 명령어는 시스템이 지원하는 경우 [Boris REPL](https://github.com/d11wtq/boris)을 활용합니다. 이 기능을 사용하기 위해서는 `readline` 와 `pcntl` PHP extension을 설치해야만 합니다. 이러한 extension들을 가지고 있지 않다면, 4.0 의 shell 이 사용될 것입니다. 

### Eloquent Improvements
### Eloquent 향상

A new `hasManyThrough` relationship has been added to Eloquent. To learn how to use it, consult the [Eloquent documentation](/docs/eloquent#has-many-through).

새로운 `hasManyThrough` 관계가 Eloquent 에 추가되었습니다. 어떻게 사용하는지 알아보려면 [Eloquent 문서](/docs/eloquent#has-many-through)를 참고하십시오.

A new `whereHas` method has also been introduced to allow [retrieving models based on relationship constraints](/docs/eloquent#querying-relations).

[모델을 관계-relationship의 제약에 따라서 획득하기 위해서](/docs/eloquent#querying-relations) 새로운 `whereHas` 메소드가 또한 도입되었습니다. 

### Database Read / Write Connections
### 데이터베이스 읽기 / 쓰기 커넥션

Automatic handling of separate read / write connections is now available throughout the database layer, including the query builder and Eloquent. For more information, consult [the documentation](/docs/database#read-write-connections).

쿼리빌더와 Eloquent를 포함하여 데이터베이스 레이어에서 자동으로 읽기 / 쓰기의 분리된 커넥션을 처리하는 것이 이제 가능합니다. 보다 자세한 정보는 [문서](/docs/database#read-write-connections)를 참조하십시오.

### Queue Priority
### 큐 우선순위

Queue priorities are now supported by passing a comma-delimited list to the `queue:listen` command.

큐의 우선순위는 `queue:listen` 명령어에 콤마로 구분되는 리스트를 전달하여 지원될수 있습니다. 

### Failed Queue Job Handling
### 실패한 큐 작업 처리하기

The queue facilities now include automatic handling of failed jobs when using the new `--tries` switch on `queue:listen`. More information on handling failed jobs can be found in the [queue documentation](/docs/queues#failed-jobs).

큐 기능은 이제 `queue:listen`의 `--tries` 스위치를 붙이는 것으로, 자동으로 실패된 작업들을 처리하는 기능을 포함하고 있습니다. 실패한 작업들을 처리하는 보다 자세한 정보는 [큐 문서](/docs/queues#failed-jobs)를 참조하십시오. 

### Cache Tags
### 캐시 태그

Cache "sections" have been superseded by "tags". Cache tags allow you to assign multiple "tags" to a cache item, and flush all items assigned to a single tag. More information on using cache tags may be found in the [cache documentation](/docs/cache#cache-tags).

캐시 "section"은 "tags"로 대체되었습니다. 캐시 태그는 여러가지 태그를 캐시 아이템에 붙일 수 있도록 하고, 동일한 태그를 할당한 아이템들을 한번에 삭제할 수 있도록 해줍니다. 캐시 태그를 사용하는 보다 자세한 정보는 [캐시 문서](/docs/cache#cache-tags)에서 찾을 수 있습니다. 

### Flexible Password Reminders
### 보다 유연해진 패스워드 리마인더

The password reminder engine has been changed to provide greater developer flexibility when validating passwords, flashing status messages to the session, etc. For more information on using the enhanced password reminder engine, [consult the documentation](/docs/4.1/security#password-reminders-and-reset).

패스워드를 검증하고, 상태 메세지를 세션에서 지우는등, 패스워드 리마인더 엔진이 개발자들이 보다 유연하게 사용할 수 있도록 변경되었습니다. 강화된 패스워드 리마인더 엔진에 대한 자세한 정보는 [문서](/docs/4.1/security#password-reminders-and-reset)를 참고하십시오

### Improved Routing Engine
### 라우팅 엔진 개선

Laravel 4.1 features a totally re-written routing layer. The API is the same; however, registering routes is a full 100% faster compared to 4.0. The entire engine has been greatly simplified, and the dependency on Symfony Routing has been minimized to the compiling of route expressions.

라라벨 4.1에서는 라우트 레이어를 완전히 새로 작성하였습니다. API는 동일합니다; 하지만 라우터를 등록하는 것이 4.0에 비해서 100% 빨라졌습니다. 엔진 전체는 보다 단순해지고, 라우트의 해석시 Symfony Routing에 대한 의존도가 줄어 들었습니다.

### Improved Session Engine
### 세션 엔진 개선

With this release, we're also introducing an entirely new session engine. Similar to the routing improvements, the new session layer is leaner and faster. We are no longer using Symfony's (and therefore PHP's) session handling facilities, and are using a custom solution that is simpler and easier to maintain.

이 릴리즈에서 완전히 새로운 세션 엔진을 도입되었습니다. 라우팅의 개선과 비슷하게 새로운 세션 레이어는 더 작고 빨라졌습니다. 더 이상 Symfony의 (그리고 PHP의) 세션 핸들링 기능을 사용하지 않고, 더 간단하고 유지 보수하기 쉬운 사용자 정의 처리를 사용하고 있습니다. 

### Doctrine DBAL
### 독트린 DBAL

If you are using the `renameColumn` function in your migrations, you will need to add the `doctrine/dbal` dependency to your `composer.json` file. This package is no longer included in Laravel by default.

마이그레이션 작업에서 `renameColumn` 함수를 사용하고 있다면 `composer.json` 파일에 `doctrine/dbal` 의존성을 추가해야할 필요가 있습니다. 이 패키지는 라라벨에서 더이상 기본으로 포함하고 있지 않습니다. 
