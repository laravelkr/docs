# Laravel Pennant
# 라라벨 Pennant

- [Introduction](#introduction)
- [소개하기](#introduction)
- [Installation](#installation)
- [설치하기](#installation)
- [Configuration](#configuration)
- [설정하기](#configuration)
- [Defining Features](#defining-features)
- [기능-Feature 정의](#defining-features)
    - [Class Based Features](#class-based-features)
    - [기능-Feature을 클래스를 기반으로 정의하기](#class-based-features)
- [Checking Features](#checking-features)
- [기능-Feature의 활성화 여부 확인하기](#checking-features)
    - [Conditional Execution](#conditional-execution)
    - [조건에 따른 실행여부 결정하기](#conditional-execution)
    - [The `HasFeatures` Trait](#the-has-features-trait)
    - [`HasFeatures` 트레이트-Trait](#the-has-features-trait)
    - [Blade Directive](#blade-directive)
    - [Blade 지시어](#blade-directive)
    - [Middleware](#middleware)
    - [미들웨어](#middleware)
    - [In-Memory Cache](#in-memory-cache)
    - [인메모리 캐시](#in-memory-cache)
- [Scope](#scope)
- [Scope-범위](#scope)
    - [Specifying The Scope](#specifying-the-scope)
    - [Scope-범위 지정하기](#specifying-the-scope)
    - [Default Scope](#default-scope)
    - [기본 Scope-범위](#default-scope)
    - [Nullable Scope](#nullable-scope)
    - [Nullable Scope](#nullable-scope)
    - [Identifying Scope](#identifying-scope)
    - [Scope-범위 식별하기](#identifying-scope)
- [Rich Feature Values](#rich-feature-values)
- [기능-feature의 값이 여러가지인 경우](#rich-feature-values)
- [Retrieving Multiple Features](#retrieving-multiple-features)
- [여러개의 기능-feature 조회하기](#retrieving-multiple-features)
- [Eager Loading](#eager-loading)
- [Eager 로딩](#eager-loading)
- [Updating Values](#updating-values)
- [설정값 업데이트](#updating-values)
    - [Bulk Updates](#bulk-updates)
    - [Bulk 업데이트](#bulk-updates)
    - [Purging Features](#purging-features)
    - [퍼지 기능-Features](#purging-features)
- [Testing](#testing)
- [테스팅](#testing)
- [Adding Custom Pennant Drivers](#adding-custom-pennant-drivers)
- [커스텀 Pennant 드라이버 추가힉](#adding-custom-pennant-drivers)
    - [Implementing The Driver](#implementing-the-driver)
    - [드라이버 구현하기](#implementing-the-driver)
    - [Registering The Driver](#registering-the-driver)
    - [드라이버 등록하기](#registering-the-driver)
- [Events](#events)
- [이벤트](#events)

<a name="introduction"></a>
## Introduction
## 시작하기

[Laravel Pennant](https://github.com/laravel/pennant) is a simple and light-weight feature flag package - without the cruft. Feature flags enable you to incrementally roll out new application features with confidence, A/B test new interface designs, compliment a trunk-based development strategy, and much more.

[라라벨 Pennant](https://github.com/laravel/pennant)는 불필요한 부분이 제거된 간결하면서도 경량화된 기능-Feature 플래그 관리 패키지입니다. 기능-Feature 플래그를 통해 애플리케이션의 새로운 기능을 점진적이고 안정적으로 출시하거나, 새로운 인터페이스 디자인에 대한 A/B 테스트를 진행하며, 트렁크 기반의 개발 전략을 더욱 효과적으로 수행할 수 있습니다.

역자주) 트렁크 기반 개발 전략(Trunk-based Development Strategy)은 애플리케이션의 버전을 관리하는 전략 중 하나입니다. 모든 개발자들이 같은 브랜치(주로 'main')에서 짧은 주기로 코드를 커밋하고 통합하는 방식을 의미합니다. 이 전략을 사용할 때에는, 특정 기능 또는 변경사항을 개발하기 위한 별도의 브랜치를 만들지 않습니다. 대신, 코드 변경사항은 빠르게 메인 브랜치에 머지(merge)하고, 대신 기능 토글(feature toggle)과 같은 방법을 사용하여 아직 완료되지 않은 기능을 사용자로부터 숨겨놓습니다.

<a name="installation"></a>
## Installation
## 설치하기

First, install Pennant into your project using the Composer package manager:

컴포저 패키지 매니저를 사용하여 Pennant를 설치합니다.

```shell
composer require laravel/pennant
```

Next, you should publish the Pennant configuration and migration files using the `vendor:publish` Artisan command:

그 다음, `vendor:publish` 명령어를 사용하여 Pennant 설정 파일과 마이그레이션 파일이 복사됩니다.  

```shell
php artisan vendor:publish --provider="Laravel\Pennant\PennantServiceProvider"
```

Finally, you should run your application's database migrations. This will create a `features` table that Pennant uses to power its `database` driver:

마지막으로 애플리케이션의 데이터베이스 마이그레이션을 실행 합니다. 이 과정에서 `features` 테이블이 생성됩니다. Pennant가 `database` 드라이버인 경우 이 테이블을 사용합니다.

```shell
php artisan migrate
```

<a name="configuration"></a>
## Configuration
## 설정하기

After publishing Pennant's assets, its configuration file will be located at `config/pennant.php`. This configuration file allows you to specify the default storage mechanism that will be used by Pennant to store resolved feature flag values.

Pennant 의 asset 을 복사하고 나면, `config/pennant.php` 설정 파일이 생성됩니다. 이 설정파일에서 Pennant가 기능-feature 플래그 값을 저장하는 데 사용하는 저장 매커니즘을 지정할 수 있습니다.

Pennant includes support for storing resolved feature flag values in an in-memory array via the `array` driver. Or, Pennant can store resolved feature flag values persistently in a relational database via the `database` driver, which is the default storage mechanism used by Pennant.

Pennant에는 `array` 드라이버를 통해서 인-메모리 배열에 기능-feature 플래그 값을 저장하는 기능을 지원합니다. 다른 `database` 드라이버를 사용하면 Pennant가 기능-feature 플래그 값을 관계형 데이터베이스에 영구적으로 저장할 수 있습니다.

<a name="defining-features"></a>
## Defining Features
## 기능-Feature 정의하기

To define a feature, you may use the `define` method offered by the `Feature` facade. You will need to provide a name for the feature, as well as a closure that will be invoked to resolve the feature's initial value.

기능-Feature을 정의하려면 `Feature` 파사드의 `define` 메서드를 사용하면 됩니다. 메서드에 기능-feature의 이름과 기능의 초기 값을 확인하기 위해 호출될 클로저를 제공해야 합니다.

일반적으로 기능은 Facade를 사용하여 서비스 제공자에서 정의됩니다 Feature. 클로저는 기능 확인을 위한 "scope-범위"를 받습니다. 가장 일반적으로 scope-범위는 현재 인증된 사용자입니다. 이 예에서는 애플리케이션 사용자에게 새로운 API를 점진적으로 출시하는 기능을 정의합니다.


Typically, features are defined in a service provider using the `Feature` facade. The closure will receive the "scope" for the feature check. Most commonly, the scope is the currently authenticated user. In this example, we will define a feature for incrementally rolling out a new API to our application's users:

일반적으로 기능-feature은 서비스 프로바이더 안에서 `Feature` 파사드를 사용하여 정의합니다. 클로저는 기능-feature을 확인하기 위한 "scope-범위"를 인자로 받습니다. 가장 일반적으로 scope-범위는 현재 인증된 사용자를 의미합니다. 다음 예제에서는 새로운 API를 애플리케이션 사용자에게 점진적으로 출시하기 위한 기능-feature을 정의합니다.

```php
<?php

namespace App\Providers;

use App\Models\User;
use Illuminate\Support\Lottery;
use Illuminate\Support\ServiceProvider;
use Laravel\Pennant\Feature;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Bootstrap any application services.
     */
    public function boot(): void
    {
        Feature::define('new-api', fn (User $user) => match (true) {
            $user->isInternalTeamMember() => true,
            $user->isHighTrafficCustomer() => false,
            default => Lottery::odds(1 / 100),
        });
    }
}
```

As you can see, we have the following rules for our feature:

코드에서 볼 수 있듯이 정의한 기능-feature에는 다음과 같은 규칙이 적용됩니다. 

- All internal team members should be using the new API.
- Any high traffic customers should not be using the new API.
- Otherwise, the feature should be randomly assigned to users with a 1 in 100 chance of being active.

- 모든 내부 팀 멤버들은 새로운 API를 사용하게 됩니다.
- 트래픽이 많은 고객은 새로운 API를 사용하게 하지 않습니다.
- 나머지 사용자들은 100분의 1 확률로 랜덤하게 새로운 API를 사용하게 됩니다.

The first time the `new-api` feature is checked for a given user, the result of the closure will be stored by the storage driver. The next time the feature is checked against the same user, the value will be retrieved from storage and the closure will not be invoked.

처음으로 특정 사용자에 대해 `new-api` 기능-feature를 사용할지 확인하고 나면 클로저의 결과가 스토리지 드라이버에 저장됩니다. 그 다음번에 동일한 사용자에 대해 기능-feature을 사용할지 확인을 시도하면 클로저가 실행되는 대신 스토리지에 값을 찾아 반환합니다.

For convenience, if a feature definition only returns a lottery, you may omit the closure completely:

편의를 위해, 기능-feature 를 사용할지 결정하는 로직이 랜덤으로 결정되는 경우에는 클로저를 생략할 수 있습니다. 

    Feature::define('site-redesign', Lottery::odds(1, 1000));

<a name="class-based-features"></a>
### Class Based Features
### 기능-Feature을 클래스를 기반으로 정의하기

Pennant also allows you to define class based features. Unlike closure based feature definitions, there is no need to register a class based feature in a service provider. To create a class based feature, you may invoke the `pennant:feature` Artisan command. By default the feature class will be placed in your application's `app/Features` directory:

Pennant를 사용하면 기능-feature을 클래스 기반으로 정의할 수도 있습니다. 클로저를 기반으로 정의하는 것과 달리 서비스 프로바이더에 따로 등록과정을 거칠 필요가 없습니다. 기능-feature을 클래스 기반으로 생성하려면 `pennant:feature` Artisan 명령어를 사용하면 됩니다. 기본적으로 기능-feature 클래스는 `app/Features` 디렉토리에 생성됩니다.

```shell
php artisan pennant:feature NewApi
```

When writing a feature class, you only need to define a `resolve` method, which will be invoked to resolve the feature's initial value for a given scope. Again, the scope will typically be the currently authenticated user:

기능-feature 클래스를 작성할 때는 지정된 scope-범위에 대한 기능-feature의 초기값을 확인하기 위해 호출하는 `resolve` 메서드 하나만 정의하면 됩니다. 다시 말하지만, scope-범위는 일반적으로 현재 인증된 사용자입니다.

```php
<?php

namespace App\Features;

use Illuminate\Support\Lottery;

class NewApi
{
    /**
     * Resolve the feature's initial value.
     */
    public function resolve(User $user): mixed
    {
        return match (true) {
            $user->isInternalTeamMember() => true,
            $user->isHighTrafficCustomer() => false,
            default => Lottery::odds(1 / 100),
        };
    }
}
```

> **Note** Feature classes are resolved via the [container](/docs/{{version}}/container), so you may inject dependencies into the feature class's constructor when needed.

> **Note** 기능-feature 클래스는 [컨테이너](/docs/{{version}}/container)를 통해서 의존성이 해결됩니다. 따라서 기능-feature을 사용하고자 하는 클래스의 생성자에 의존성 주입할 수 있습니다.

<a name="checking-features"></a>
## Checking Features
## 기능-Feature의 활성화 여부 확인하기

To determine if a feature is active, you may use the `active` method on the `Feature` facade. By default, features are checked against the currently authenticated user:

현재 기능-feature이 활성화 되어 있는지 확인하려면 `Feature` 파사드의 `active` 메서드를 사용하면 됩니다. 기본적으로 기능-feature은 현재 인증된 사용자를 기준으로 활성여부를 확인합니다. 

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Http\Response;
use Laravel\Pennant\Feature;

class PodcastController
{
    /**
     * Display a listing of the resource.
     */
    public function index(Request $request): Response
    {
        return Feature::active('new-api')
                ? $this->resolveNewApiResponse($request)
                : $this->resolveLegacyApiResponse($request);
    }

    // ...
}
```

Although features are checked against the currently authenticated user by default, you may easily check the feature against another user or [scope](#scope). To accomplish this, use the `for` method offered by the `Feature` facade:

기본적으로 기능-feature의 활성여부를 확인할 때 현재 인증된 사용자를 기준으로 하지만 다른 사용자에 대해서 활성여부를 확인하거나 또는 다른 [scope-범위](#scope)를 기준으로 활성여부를 확인할 수도 있습니다. 다음과 같이 `Feature` 파사드의 `for` 메서드를 사용하면 됩니다.

```php
return Feature::for($user)->active('new-api')
        ? $this->resolveNewApiResponse($request)
        : $this->resolveLegacyApiResponse($request);
```

Pennant also offers some additional convenience methods that may prove useful when determining if a feature is active or not:

Pennant는 기능-feature이 활성화되었는지 여부를 확인하는데 유용하게 사용할 수 있는 추가적인 편의 메서드를 제공합니다.

```php
// Determine if all of the given features are active...
Feature::allAreActive(['new-api', 'site-redesign']);

// Determine if any of the given features are active...
Feature::someAreActive(['new-api', 'site-redesign']);

// Determine if a feature is inactive...
Feature::inactive('new-api');

// Determine if all of the given features are inactive...
Feature::allAreInactive(['new-api', 'site-redesign']);

// Determine if any of the given features are inactive...
Feature::someAreInactive(['new-api', 'site-redesign']);
```

> **Note**
> When using Pennant outside of an HTTP context, such as in an Artisan command or a queued job, you should typically [explicitly specify the feature's scope](#specifying-the-scope). Alternatively, you may define a [default scope](#default-scope) that accounts for both authenticated HTTP contexts and unauthenticated contexts.

> **Note**
> Artisan 명령어나 큐-queue 작업과 같은 HTTP가 아닌 상황에서 Pennant를 사용할 때에는 일반적으로 [기능-feature의 scope-범위를 명시적으로 지정](#specifying-the-scope)해야 합니다. 그렇지 않으면 인증된 사용자를 기반으로 하는 HTTP의 상황과 인증되지 않은 다른 상황을 모두 지원하는 [기본 scope-범위](#default-scope)를 정의할 수 있습니다. 

<a name="checking-class-based-features"></a>
#### Checking Class Based Features
#### 클래스 기반 기능-feature에서 활성화 확인하기

For class based features, you should provide the class name when checking the feature:

클래스 기반 기능-feature의 활성화를 확인하려면 클래스 이름을 사용하면 됩니다.  

```php
<?php

namespace App\Http\Controllers;

use App\Features\NewApi;
use Illuminate\Http\Request;
use Illuminate\Http\Response;
use Laravel\Pennant\Feature;

class PodcastController
{
    /**
     * Display a listing of the resource.
     */
    public function index(Request $request): Response
    {
        return Feature::active(NewApi::class)
                ? $this->resolveNewApiResponse($request)
                : $this->resolveLegacyApiResponse($request);
    }

    // ...
}
```

<a name="conditional-execution"></a>
### Conditional Execution
### 조건에 따른 실행여부 결정하기

The `when` method may be used to fluently execute a given closure if a feature is active. Additionally, a second closure may be provided and will be executed if the feature is inactive:

`when` 메소드를 사용하여 기능-feature이 활성화 된 경우와 그렇지 않은 경우에 따라 각각 다른 클로저 로직을 실핼할 수 있습니다. 메서드에 주어지는 두 번째 클로저는 해당 기능이 비활성화 된 경우에 실행됩니다.

    <?php

    namespace App\Http\Controllers;

    use App\Features\NewApi;
    use Illuminate\Http\Request;
    use Illuminate\Http\Response;
    use Laravel\Pennant\Feature;

    class PodcastController
    {
        /**
         * Display a listing of the resource.
         */
        public function index(Request $request): Response
        {
            return Feature::when(NewApi::class,
                fn () => $this->resolveNewApiResponse($request),
                fn () => $this->resolveLegacyApiResponse($request),
            );
        }

        // ...
    }

The `unless` method serves as the inverse of the `when` method, executing the first closure if the feature is inactive:

`unless` 메서드는 `when` 메서드의 반대로, 기능-feature이 비활성화 된 경우에 첫 번째 클로저를 실행합니다.

    return Feature::unless(NewApi::class,
        fn () => $this->resolveLegacyApiResponse($request),
        fn () => $this->resolveNewApiResponse($request),
    );

<a name="the-has-features-trait"></a>
### The `HasFeatures` Trait
### `HasFeatures` 트레이트-Trait

Pennant's `HasFeatures` trait may be added to your application's `User` model (or any other model that has features) to provide a fluent, convenient way to check features directly from the model:

애플리케이션의 User모델(또는 기능-feature 을 판별하는 기준이 되는 모델)에 Pennant가 제공하는 `HasFeatures` 트레이트-trait를 추가할 수 있습니다. 이렇게 하면 모델에서 직접 기능-feature의 활성화 여부를 확인하는 편리한 방법을 사용할 수 있습니다.

```php
<?php

namespace App\Models;

use Illuminate\Foundation\Auth\User as Authenticatable;
use Laravel\Pennant\Concerns\HasFeatures;

class User extends Authenticatable
{
    use HasFeatures;

    // ...
}
```

Once the trait has been added to your model, you may easily check features by invoking the `features` method:

모델에 트레이트-trait을 추가하고 나면, 다음과 같이 `feature` 메서드를 사용하여 기능-feature의 활성화 여부를 손쉽게 확인할 수 있습니다.

```php
if ($user->features()->active('new-api')) {
    // ...
}
```

Of course, the `features` method provides access to many other convenient methods for interacting with features:

그리고 `features` 메서드를 사용하면 기능-feature에 대해서 추가적인 메서드를 호출하는 편리한 방법을 사용할 수 있습니다.

```php
// Values...
$value = $user->features()->value('purchase-button')
$values = $user->features()->values(['new-api', 'purchase-button']);

// State...
$user->features()->active('new-api');
$user->features()->allAreActive(['new-api', 'server-api']);
$user->features()->someAreActive(['new-api', 'server-api']);

$user->features()->inactive('new-api');
$user->features()->allAreInactive(['new-api', 'server-api']);
$user->features()->someAreInactive(['new-api', 'server-api']);

// Conditional execution...
$user->features()->when('new-api',
    fn () => /* ... */,
    fn () => /* ... */,
);

$user->features()->unless('new-api',
    fn () => /* ... */,
    fn () => /* ... */,
);
```

<a name="blade-directive"></a>
### Blade Directive
### Blade 지시어

To make checking features in Blade a seamless experience, Pennant offers a `@feature` directive:

Blade 에서 기능-feature 확인을 쉽게 하기 위해서 Pennant는 `@feature` 지시어를 제공합니다.

```blade
@feature('site-redesign')
    <!-- 'site-redesign' is active -->
@else
    <!-- 'site-redesign' is inactive -->
@endfeature
```

<a name="middleware"></a>
### Middleware
### 미들웨어

Pennant also includes a [middleware](/docs/{{version}}/middleware) that may be used to verify the currently authenticated user has access to a feature before a route is even invoked. To get started, you should add a middleware alias for the `EnsureFeaturesAreActive` middleware to your application's `app/Http/Kernel.php` file:

Pennant에는 또한 라우트가 호출되기 전에 현재 인증된 사용자가 해당 기능-feature에 접근할 수 있는지 확인하는 데 사용할 수 있는 미들웨어도 제공합니다. 이 미들웨어를 사용하려면 `app/Http/Kernel.php` 파일에 `EnsureFeaturesAreActive` 미들웨어에 대한 미들웨어 별칭을 추가해야합니다.

```php
use Laravel\Pennant\Middleware\EnsureFeaturesAreActive;

protected $middlewareAliases = [
    // ...
    'features' => EnsureFeaturesAreActive::class,
];
```

Next, you may assign the middleware to a route and specify the features that are required to access the route. If any of the specified features are inactive for the currently authenticated user, a `400 Bad Request` HTTP response will be returned by the route. Multiple features may be specified using a comma-delimited list:

그 다음에, 이 미들웨어를 라우트에 할당하고 이 라우트에 접근하기 위해서 필요한 기능-feature을 지정하면 됩니다. 현재 인증된 사용자에 대해서 어느 하나의 기능-feature에 대해서라도 활성화 되지 않았다고 판단되면 `400 Bad Request` HTTP 응답이 반환됩니다. 미들웨어에 여러개의 기능-feature을 지정하려면 콤마(,)로 구분하여 지정하면 됩니다. 

```php
Route::get('/api/servers', function () {
    // ...
})->middleware(['features:new-api,servers-api']);
```

<a name="customizing-the-response"></a>
#### Customizing The Response
#### Response-응답 커스터마이징

If you would like to customize the response that is returned by the middleware when one of the listed features is inactive, you may use the `whenInactive` method provided by the `EnsureFeaturesAreActive` middleware. Typically, this method should be invoked within the `boot` method of one of your application's service providers:

미들웨어로 지정한 기능-feature이 비활성화 되어 있다면 400 response-응답이 반환된다. 이 때 반환되는 응답을 커스터마이징하고 싶다면 `EnsureFeaturesAreActive` 미들웨어의 `whenInactive` 메서드를 호출하면 된다. 일반적으로 이 메서드는 애플리케이션 서비스 프로바이더의 `boot` 메서드 안에서 호출해야한다.  

```php
use Illuminate\Http\Request;
use Illuminate\Http\Response;
use Laravel\Pennant\Middleware\EnsureFeaturesAreActive;

/**
 * Bootstrap any application services.
 */
public function boot(): void
{
    EnsureFeaturesAreActive::whenInactive(
        function (Request $request, array $features) {
            return new Response(status: 403);
        }
    );

    // ...
}
```

<a name="in-memory-cache"></a>
### In-Memory Cache
### 인-메모리 캐시

When checking a feature, Pennant will create an in-memory cache of the result. If you are using the `database` driver, this means that re-checking the same feature flag within a single request will not trigger additional database queries. This also ensures that the feature has a consistent result for the duration of the request.

기능-feature이 활성화 되어 있는지 확인할 때, Pennant 는 결과를 인-메모리에 캐싱합니다. `database` 드라이버를 사용하는 경우, 이러한 동작은 하나의 request-요청안에서 동일한 기능-feature에 대한 확인요청이 발생해도 추가적으로 데이터베이스에 쿼리를 질의하지 않는 다는 것을 의미합니다. 이 동작은 또한 request-요청 기간동안 기능-feature이 일관된 결과를 반환함을 보장합니다. 

If you need to manually flush the in-memory cache, you may use the `flushCache` method offered by the `Feature` facade:

코드에서 직접 수동으로 이 캐시를 제거하려면 `Feature` 파사드의 `flushCache` 메서드를 사용하면 됩니다.

    Feature::flushCache();

<a name="scope"></a>
## Scope
## Scope-범위

<a name="specifying-the-scope"></a>
### Specifying The Scope
### Scope-범위 지정하기

As discussed, features are typically checked against the currently authenticated user. However, this may not always suit your needs. Therefore, it is possible to specify the scope you would like to check a given feature against via the `Feature` facade's `for` method:

앞서 언급한대로 기능-feature에 대한 확인은 현재의 인증된 사용자를 기준으로 합니다. 하지만 이러한 동작이 항상 요구사항에 부합하지는 않습니다. 따라서 필요한 경우 `Feature` 파사드의 `for` 메서드를 사용하여 기능-feature을 확인하고자 하는 scope-범위를 지정할 수 있습니다.

```php
return Feature::for($user)->active('new-api')
        ? $this->resolveNewApiResponse($request)
        : $this->resolveLegacyApiResponse($request);
```

Of course, feature scopes are not limited to "users". Imagine you have built a new billing experience that you are rolling out to entire teams rather than individual users. Perhaps you would like the oldest teams to have a slower rollout than the newer teams. Your feature resolution closure might look something like the following:

당연하게도 기능-feature의 scope-범위는 "사용자"로 제한되지 않습니다. 개발 사용자가 아니라 전체 팀을 대상으로 하는 새로운 결제 시스템을 제공하려한다고 가정해보겠습니다. 아마도 새로운 팀에 먼저 적용하고, 가장 오래된 팀에 가장 늦게 적용 하길 원할 것입니다. 이런 경우라면 기능-feature을 정의할 때 클로저는 다음과 같은 형태일 수 있습니다.

```php
use App\Models\Team;
use Carbon\Carbon;
use Illuminate\Support\Lottery;
use Laravel\Pennant\Feature;

Feature::define('billing-v2', function (Team $team) {
    if ($team->created_at->isAfter(new Carbon('1st Jan, 2023'))) {
        return true;
    }

    if ($team->created_at->isAfter(new Carbon('1st Jan, 2019'))) {
        return Lottery::odds(1 / 100);
    }

    return Lottery::odds(1 / 1000);
});
```

You will notice that the closure we have defined is not expecting a `User`, but is instead expecting a `Team` model. To determine if this feature is active for a user's team, you should pass the team to the `for` method offered by the `Feature` facade:

코드에서 볼 수 있듯이 클로저는 `User` 대신 `Team` 모델을 전달 받기를 기대한다는 것을 알 수 있습니다. 이 기능-feature이 사용자 팀에 대해 활성화되어 있는지 확인하려면 `Feature` 파사드의 `for` 메서드에 사용자 대신 팀을 전달해야합니다. 

```php
if (Feature::for($user->team)->active('billing-v2')) {
    return redirect()->to('/billing/v2');
}

// ...
```

<a name="default-scope"></a>
### Default Scope
### 기본 Scope-범위

It is also possible to customize the default scope Pennant uses to check features. For example, maybe all of your features are checked against the currently authenticated user's team instead of the user. Instead of having to call `Feature::for($user->team)` every time you check a feature, you may instead specify the team as the default scope. Typically, this should be done in one of your application's service providers:

기능-feature을 확인하기 위해 사용하는 scope-범위의 기본값을 커스터마이징 하는 것도 가능합니다. 예를 들어 사용자가 아니라 현재 인증된 사용자가 소속된 팀을 기준으로 기능-feature을 확인해야할 수 있습니다. 기능-feature 활성화를 확인하기 위해서 매번 `Feature::for($user->team)`를 호출하는 대신에 다음과 같이 팀을 기본 scope-범위로 지정할 수 있습니다. 일반적으로 이 작업은 애플리케이션의 서비스 프로바이더 중 하나에서 수행해야합니다.

```php
<?php

namespace App\Providers;

use Illuminate\Support\Facades\Auth;
use Illuminate\Support\ServiceProvider;
use Laravel\Pennant\Feature;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Bootstrap any application services.
     */
    public function boot(): void
    {
        Feature::resolveScopeUsing(fn ($driver) => Auth::user()?->team);

        // ...
    }
}
```

If no scope is explicitly provided via the `for` method, the feature check will now use the currently authenticated user's team as the default scope:

`for` 메서드를 호출할 때 scope-범위가 명시적으로 제공되지 않는다면, 기능-feature은 기본 scope-범위를 사용하여 현재 인증된 사용자의 팀을 기준으로 활성화 여부를 확인합니다.

```php
Feature::active('billing-v2');

// Is now equivalent to...

Feature::for($user->team)->active('billing-v2');
```

<a name="nullable-scope"></a>
### Nullable Scope
### null이 가능한 Scope-범위

If the scope you provide when checking a feature is `null` and the feature's definition does not support `null` via a nullable type or by including `null` in a union type, Pennant will automatically return `false` as the feature's result value.

기능-feature을 확인할 때 제공한 scope-범위가 `null`이고 기능-feature의 정의에서 scope-범위가 `null` 허용 타입이나 유니온 타입을 포함을 통해 `null`을 지원하지 않는 타입인 경우라면 Pennant는 자동으로 기능-feature의 확인 결과를 `false` 로 반환합니다. 

So, if the scope you are passing to a feature is potentially `null` and you want the feature's value resolver to be invoked, you should account for that in your feature's definition. A `null` scope may occur if you check a feature within an Artisan command, queued job, or unauthenticated route. Since there is usually not an authenticated user in these contexts, the default scope will be `null`.

따라서 기능-feature 에 전달하는 scope-범위의 값이 잠재적으로 `null` 일 수 있다면 활성화 여부를 확인하기 전에 이를 고려해야 합니다. scope-범위가 `null` 이 될 수 있는 경우는 Artisan 명령어, 큐-queue 작업을 처리하는 도중, 인증되지 않은 라우트를 처리하는 도중일 수 있습니다. 일반적으로 이러한 경우들은 인증된 사용자가 없으므로 기본 scope-범위는 `null` 이 됩니다.   

If you do not always [explicitly specify your feature scope](#specifying-the-scope) then you should ensure the scope's type is "nullable" and handle the `null` scope value within your feature definition logic:

[기능-feature의 scope-범위를 항상 명시적으로 지정](#specifying-the-scope)하지 않는 경우, scope-범위의 타입이 "nullable"인지 확인해야합니다. 그리고 기능-feature을 정의할 때 scope-범위가 `null` 값인 경우를 처리해야 합니다.

```php
use App\Models\User;
use Illuminate\Support\Lottery;
use Laravel\Pennant\Feature;

Feature::define('new-api', fn (User $user) => match (true) {// [tl! remove]
Feature::define('new-api', fn (User|null $user) => match (true) {// [tl! add]
    $user === null => true,// [tl! add]
    $user->isInternalTeamMember() => true,
    $user->isHighTrafficCustomer() => false,
    default => Lottery::odds(1 / 100),
});
```

<a name="identifying-scope"></a>
### Identifying Scope
### Scope-범위의 식별

Pennant's built-in `array` and `database` storage drivers know how to properly store scope identifiers for all PHP data types as well as Eloquent models. However, if your application utilizes a third-party Pennant driver, that driver may not know how to properly store an identifier for an Eloquent model or other custom types in your application.

Pennant에 내장된 `array`, `database` 스토리지 드라이버는 Eloquent 모델과 여타 모든 PHP의 데이터 타입에 대한 scope-범위 식별자를 저장하는 방법을 가지고 있습니다. 만약 애플리케이션이 다른 써드파티 Pennant 드라이버를 사용하는 경우 Eloquent 모델 및 애플리케이션의 기타 커스텀 타입에 대한 식별자를 적절하게 저장하는 방법을 알지 못할 수도 있습니다. 

(역자주 : 스토리지에 scope-범위에 해당하는 User, Team 을 저장할 때 데이터 식별을 위한 식별자로 id 를 저장한 다는 것을 생각해보십시오.)

In light of this, Pennant allows you to format scope values for storage by implementing the `FeatureScopeable` contract on the objects in your application that are used as Pennant scopes.

이런 점을 고려하여 Pennant scope-범위를 사용하는 애플리케이션에서 `FeatureScopeable` contract를 구현하는 객체는 scope-범위를 저장할 때 어떤 포맷으로 저장할 지 지정할 수 있습니다.

For example, imagine you are using two different feature drivers in a single application: the built-in `database` driver and a third-party "Flag Rocket" driver. The "Flag Rocket" driver does not know how to properly store an Eloquent model. Instead, it requires a `FlagRocketUser` instance. By implementing the `toFeatureIdentifier` defined by the `FeatureScopeable` contract, we can customize the storable scope value provided to each driver used by our application:

예를 들어, 하나의 애플리케이션에서 기본적으로 제공되는 `database` 드라이버와 다른 "Flag Rocket" 드라이버라는 두 가지 기능-feature 드라이버를 사용한다고 가정해 보겠습니다. "Flag Rocket" 드라이버는 Eloquent 모델의 식별자를 적절하게 저장하는 방법을 알지 못합니다. 대신 `FlagRocketUser` 인스턴스를 필요로 합니다. `FeatureScopeable` contract를 구현하여 `toFeatureIdentifier`를 정의하면 애플리케이션에서 사용하는 각 드라이버에 제공되는 저장 가능한 scope-범위 값을 커스터마이징할 수 있습니다.

```php
<?php

namespace App\Models;

use FlagRocket\FlagRocketUser;
use Illuminate\Database\Eloquent\Model;
use Laravel\Pennant\Contracts\FeatureScopeable;

class User extends Model implements FeatureScopeable
{
    /**
     * Cast the object to a feature scope identifier for the given driver.
     */
    public function toFeatureIdentifier(string $driver): mixed
    {
        return match($driver) {
            'database' => $this,
            'flag-rocket' => FlagRocketUser::fromId($this->flag_rocket_id),
        };
    }
}
```

<a name="rich-feature-values"></a>
## Rich Feature Values
## 기능-feature의 값이 여러가지인 경우

Until now, we have primarily shown features as being in a binary state, meaning they are either "active" or "inactive", but Pennant also allows you to store rich values as well.

지금까지는 기능-feature의 값이 "활성" "비활성" 상태인 경우만 살펴보았지만, Pennant 에서는 좀 더 다양한 값을 사용할 수도 있습니다.

For example, imagine you are testing three new colors for the "Buy now" button of your application. Instead of returning `true` or `false` from the feature definition, you may instead return a string:

예를 들어 애플리케이션의 "지금 구매" 버튼을 표시할 때 세 가지 새로운 색상을 테스트한다고 가정해 보겠습니다. 기능-feature을 정의할 때 `true` 또는 `false` 를 반환하는 대신 문자열을 반환할 수도 있습니다.

```php
use Illuminate\Support\Arr;
use Laravel\Pennant\Feature;

Feature::define('purchase-button', fn (User $user) => Arr::random([
    'blue-sapphire',
    'seafoam-green',
    'tart-orange',
]));
```

You may retrieve the value of the `purchase-button` feature using the `value` method:

그 다음 `value` 메서드를 사용하여 `purchase-button` 기능-feature 의 값을 조죄할 수 있습니다. 

```php
$color = Feature::value('purchase-button');
```

Pennant's included Blade directive also makes it easy to conditionally render content based on the current value of the feature:

Pennant에 포함된 Blade 지시문을 사용하면 기능-feature의 현재 값을 기반으로 콘텐츠를 조건에 따라 렌더링 할 수 있습니다.

```blade
@feature('purchase-button', 'blue-sapphire')
    <!-- 'blue-sapphire' is active -->
@elsefeature('purchase-button', 'seafoam-green')
    <!-- 'seafoam-green' is active -->
@elsefeature('purchase-button', 'tart-orange')
    <!-- 'tart-orange' is active -->
@endfeature
```

> **Note** When using rich values, it is important to know that a feature is considered "active" when it has any value other than `false`.

> **Note** 여러가지 값을 허용하는 경우, 기능-feature 은 `false` 이외의 값은 모두 "활성"으로 간주됩니다. 

When calling the [conditional `when`](#conditional-execution) method, the feature's rich value will be provided to the first closure:

[`when`](#conditional-execution) 메서드를 호출하면 기능-feature의 첫 번째 클로저에 값이 인자로 전달됩니다.

    Feature::when('purchase-button',
        fn ($color) => /* ... */,
        fn () => /* ... */,
    );

Likewise, when calling the conditional `unless` method, the feature's rich value will be provided to the optional second closure:

`unless` 메서드를 호출할 때는 기능-feature의 두 번째 클로저에 값이 인자로 전달됩니다.

    Feature::unless('purchase-button',
        fn () => /* ... */,
        fn ($color) => /* ... */,
    );

<a name="retrieving-multiple-features"></a>
## Retrieving Multiple Features
## 여러개의 기능-feature 조회하기

The `values` method allows the retrieval of multiple features for a given scope:

`values` 메서드를 사용하면 주어진 scope-범위에 대한 여러개의 기능-feature을 조회할 수 있습니다.

```php
Feature::values(['billing-v2', 'purchase-button']);

// [
//     'billing-v2' => false,
//     'purchase-button' => 'blue-sapphire',
// ]
```

Or, you may use the `all` method to retrieve the values of all defined features for a given scope:

`all` 메서드를 사용하면 주어진 scope-범위에 대한 정의된 모든 기능-feature의 값을 조회할 수 있습니다.

```php
Feature::all();

// [
//     'billing-v2' => false,
//     'purchase-button' => 'blue-sapphire',
//     'site-redesign' => true,
// ]
```

However, class based features are dynamically registered and are not known by Pennant until they are explicitly checked. This means your application's class based features may not appear in the results returned by the `all` method if they have not already been checked during the current request.

그런데, 클래스 기반으로 기능-feature을 정의하면, 이 클래스는 동적으로 등록되어 명시적으로 확인하기 전까지 Pennant에서 알 수 없습니다. 이 말은 `all` 메서드에서 반환되는 결과에 클래스를 기반으로 정의한 기능-feature은 나타나지 않을 수 있다는 것을 의미합니다. 

If you would like to ensure that feature classes are always included when using the `all` method, you may use Pennant's feature discovery capabilities. To get started, invoke the `discover` method in one of your application's service providers:

따라서 `all` 메서드를 사용할 때 클래스를 기반으로한 기능-feature 의 확인여부가 항상 포함되게 하려면 Pennant의 기능-feature 디스커버리 기능을 사용하면 됩니다. 애플리케이션의 서비스프로바이더 에서 `discover` 메서드를 호출하십시오.

    <?php

    namespace App\Providers;

    use Illuminate\Support\ServiceProvider;
    use Laravel\Pennant\Feature;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Bootstrap any application services.
         */
        public function boot(): void
        {
            Feature::discover();

            // ...
        }
    }

The `discover` method will register all of the feature classes in your application's `app/Features` directory. The `all` method will now include these classes in its results, regardless of whether they have been checked during the current request:

`discover` 메소드는 애플리케이션 `app/Features` 디렉토리에 있는 모든 기능-feature 클래스를 등록합니다. 이후에 `all` 메서드를 호출하면 클래스를 기반으로한 기능-feature에 대한 확인도 결과에 포함됩니다.

```php
Feature::all();

// [
//     'App\Features\NewApi' => true,
//     'billing-v2' => false,
//     'purchase-button' => 'blue-sapphire',
//     'site-redesign' => true,
// ]
```

<a name="eager-loading"></a>
## Eager Loading
## Eager 로딩

Although Pennant keeps an in-memory cache of all resolved features for a single request, it is still possible to encounter performance issues. To alleviate this, Pennant offers the ability to eager load feature values.

Pennant는 하나의 요청에 대해 확인된 모든 기능-feature의 결과를 인-메모리 캐시에 유지하지만, 이러한 동작은 성능 문제가 발생할 수 있습니다. 이 문제를 완화하기 위해 Pennant는 기능-feature의 값을 eager 로드하는 방법을 제공합니다.

To illustrate this, imagine that we are checking if a feature is active within a loop:

좀 더 자세한 설명을 위해, 반복문 안에서 기능-feature이 활성화되어 있는지 확인하는 코드를 살펴보겠습니다.

```php
use Laravel\Pennant\Feature;

foreach ($users as $user) {
    if (Feature::for($user)->active('notifications-beta')) {
        $user->notify(new RegistrationSuccess);
    }
}
```

Assuming we are using the database driver, this code will execute a database query for every user in the loop - executing potentially hundreds of queries. However, using Pennant's `load` method, we can remove this potential performance bottleneck by eager loading the feature values for a collection of users or scopes:

데이터베이스 드라이버를 사용한다고 가정하면, 이 코드는 반복문 안에서 모든 사용자에 대해 데이터베이스 쿼리를 실행하게 되며 잠재적으로는 수백 개의 쿼리를 실행합니다. 이 경우 Pennant의 eager 로딩을 적용하면 사용자 또는 scope-범위에 해당하는 컬렉션에 대한 기능-feature의 결괏값을 eagger 로드하여 이러한 잠재적인 성능 병목 현상을 제거할 수 있습니다.

```php
Feature::for($users)->load(['notifications-beta']);

foreach ($users as $user) {
    if (Feature::for($user)->active('notifications-beta')) {
        $user->notify(new RegistrationSuccess);
    }
}
```

To load feature values only when they have not already been loaded, you may use the `loadMissing` method:

아직 확인되지 않은 경우에만 기능-feature의 값을 로딩하려면 `loadMissing` 메소드를 사용할 수 있습니다.

```php
Feature::for($users)->loadMissing([
    'new-api',
    'purchase-button',
    'notifications-beta',
]);
```

<a name="updating-values"></a>
## Updating Values
## 설정값 업데이트

When a feature's value is resolved for the first time, the underlying driver will store the result in storage. This is often necessary to ensure a consistent experience for your users across requests. However, at times, you may want to manually update the feature's stored value.

기능-feature에 설정된 값이 한번 확인되면 기본 드라이버는 이 결과를 스토리지에 저장합니다. 이런 동작은 request-요청을 처리하는 과정에서 사용자에게 일관된 환경을 보장하기 위해 필요합니다. 하지만 때로는 이 값을 직접 업데이트 하고 싶을 수도 있습니다.

To accomplish this, you may use the `activate` and `deactivate` methods to toggle a feature "on" or "off":

수동으로 이 값을 수정하려면 `activate`, `deactivate` 메서드를 사용하여 기능의 활성화 여부를 "on", "off" 할 수 있습니다.

```php
use Laravel\Pennant\Feature;

// Activate the feature for the default scope...
Feature::activate('new-api');

// Deactivate the feature for the given scope...
Feature::for($user->team)->deactivate('billing-v2');
```

It is also possible to manually set a rich value for a feature by providing a second argument to the `activate` method:

기능-feature 이 지정된 값이 단순한 활성, 비활성이 아니라 다양한 값을 지정하였다면 `activate` 메서드의 두 번째 인자로 저장하려는 값을 전달하면 됩니다.

```php
Feature::activate('purchase-button', 'seafoam-green');
```

To instruct Pennant to forget the stored value for a feature, you may use the `forget` method. When the feature is checked again, Pennant will resolve the feature's value from its feature definition:

Pennant 가 저장한 값을 잊어버리도록 하려면 `forget` 메서드를 사용할 수 있습니다. 이 메서드가 호출한 뒤에 기능-feature의 활성화 여부를 다시 확인하면 저장된 값이 없기 때문에, 기능-feature을 정의한 코드가 다시 실행됩니다.  

```php
Feature::forget('purchase-button');
```

<a name="bulk-updates"></a>
### Bulk Updates
### Bulk 업데이트

To update stored feature values in bulk, you may use the `activateForEveryone` and `deactivateForEveryone` methods.

기능-feature의 값 여러개를 한번에 업데이트 하려면 `activateForEveryone`, `deactivateForEveryone` 두 개의 메서드를 사용할 수 있습니다. 

For example, imagine you are now confident in the `new-api` feature's stability and have landed on the best `'purchase-button'` color for your checkout flow - you can update the stored value for all users accordingly:

예를 들어, 특정 사용자에게만 허용하던 `new-api` 기능-feature이 이제 안정성을 충분히 검증했다고 생각하고 또 다른 기능인 결제시 버튼의 색상을 변경하는 `'purchase-button'` 기능-feature을 활성화 한다고 가정해보겠습니다.  

```php
use Laravel\Pennant\Feature;

Feature::activateForEveryone('new-api');

Feature::activateForEveryone('purchase-button', 'seafoam-green');
```

Alternatively, you may deactivate the feature for all users:

또는 모든 사용자들을 대상으로 기능-feature을 비활성화 할 수도 있습니다. 


```php
Feature::deactivateForEveryone('new-api');
```

> **Note** This will only update the resolved feature values that have been stored by Pennant's storage driver. You will also need to update the feature definition in your application.

> **Note** 이 동작은 Pennant 의 스토리지 드라이버에 의해서 저장된 값을 업데이트 하는 기능입니다. 따라서 모든 사용자들을 대상으로 기능-feature을 허용, 비허용하는 방식이 필요하다면, 이 메서드 호출 이후 애플리케이션의 기능-feature을 정의한 코드를 수정하시기 바랍니다.

<a name="purging-features"></a>
### Purging Features
### 퍼지 기능-Features

Sometimes, it can be useful to purge an entire feature from storage. This is typically necessary if you have removed the feature from your application or you have made adjustments to the feature's definition that you would like to rollout to all users.

기능-feature의 테스트가 충분히 진행되었거나, 또는 사용자에게 적용하려던 계획을 철회하여 더 이상 저장된 값이 필요가 없어진다면, 이 값을 제거하는게 좋습니다. 

You may remove all stored values for a feature using the `purge` method:

`purge` 메서드를 사용하면 저장된 모든 값을 제거할 수 있습니다.

```php
// Purging a single feature...
Feature::purge('new-api');

// Purging multiple features...
Feature::purge(['new-api', 'purchase-button']);
```

If you would like to purge _all_ features from storage, you may invoke the `purge` method without any arguments:

모든 기능-feature에 대해서 저장된 값을 제거하려면 인자없이 `purge` 메서드를 호출하면 됩니다.

```php
Feature::purge();
```

As it can be useful to purge features as part of your application's deployment pipeline, Pennant includes a `pennant:purge` Artisan command:

애플리케이션을 배포하는 시점에 기능-feature의 저장된 값을 제거하는게 유용한 경우가 있기 때문에 Pennant 는 `pennant:purge` Argisan 명령어를 제공합니다. 

```sh
php artisan pennant:purge new-api

php artisan pennant:purge new-api purchase-button
```

<a name="testing"></a>
## Testing
## 테스팅

When testing code that interacts with feature flags, the easiest way to control the feature flag's returned value in your tests is to simply re-define the feature. For example, imagine you have the following feature defined in one of your application's service provider:

기능-feature과 연관된 코드를 테스트 하려고 할 때, 기능-feature이 활성화 여부를 원하는대로 제어하는 가장 쉬운 방법은 기능-feature응 다시 정의하는 것입니다. 예를 들어, 애플리케이션 서비스 프로바이더 중 하나에서 다음과 같이 코드를 정의하였다고 가정해보겠습니다.  

```php
use Illuminate\Support\Arr;
use Laravel\Pennant\Feature;

Feature::define('purchase-button', fn () => Arr::random([
    'blue-sapphire',
    'seafoam-green',
    'tart-orange',
]));
```

To modify the feature's returned value in your tests, you may re-define the feature at the beginning of the test. The following test will always pass, even though the `Arr::random()` implementation is still present in the service provider:

테스트에서 기능-feature이 반환하는 값을 변경하려면 테스트를 시작할 때 기능-feature을 다시 정의하면 됩니다. 기존의 코드가 `Arr::random()` 로 구현되어 있더라도, 다음의 테스트에서는 항상 고정된 값이 반환되어 테스트가 통과합니다.

```php
use Laravel\Pennant\Feature;

public function test_it_can_control_feature_values()
{
    Feature::define('purchase-button', 'seafoam-green');

    $this->assertSame('seafoam-green', Feature::value('purchase-button'));
}
```

The same approach may be used for class based features:

클래스를 기반으로한 기능-feature의 경우에도 동일한 방식을 사용할 수 있습니다.

```php
use App\Features\NewApi;
use Laravel\Pennant\Feature;

public function test_it_can_control_feature_values()
{
    Feature::define(NewApi::class, true);

    $this->assertTrue(Feature::value(NewApi::class));
}
```

If your feature is returning a `Lottery` instance, there are a handful of useful [testing helpers available](/docs/{{version}}/helpers#testing-lotteries).

기능-feature이 `Lottery` 인스턴스를 반환하는 경우라면, 유용하게 사용할 수 있는 [테스팅 헬퍼](/docs/{{version}}/helpers#testing-lotteries)가 있습니다. 

<a name="store-configuration"></a>
#### Store Configuration
#### 저장소 설정하기

You may configure the store that Pennant will use during testing by defining the `PENNANT_STORE` environment variable in your application's `phpunit.xml` file:

테스팅하는 동안 Pennant 가 값을 저장할 저장소를 지정할 수 있습니다. 다음과 같이 `phpunit.xml` 파일의 `PENNANT_STORE` 환경변수를 정의하면 됩니다.  

```xml
<?xml version="1.0" encoding="UTF-8"?>
<phpunit colors="true">
    <!-- ... -->
    <php>
        <env name="PENNANT_STORE" value="array"/>
        <!-- ... -->
    </php>
</phpunit>
```

<a name="adding-custom-pennant-drivers"></a>
## Adding Custom Pennant Drivers
## 커스텀 Pennant 드라이버 추가하기

<a name="implementing-the-driver"></a>
#### Implementing The Driver
#### 드라이버 구현하기

If none of Pennant's existing storage drivers fit your application's needs, you may write your own storage driver. Your custom driver should implement the `Laravel\Pennant\Contracts\Driver` interface:

애플리케이션에 적합한 Pennant 스토리지 드라이버가 없다면, 직접 드라이버를 구현할 수도 있습니다. 커스텀 드라이버는 `Laravel\Pennant\Contracts\Driver` 인터페이스를 구현하면 됩니다. 

```php
<?php

namespace App\Extensions;

use Laravel\Pennant\Contracts\Driver;

class RedisFeatureDriver implements Driver
{
    public function define(string $feature, callable $resolver): void {}
    public function defined(): array {}
    public function getAll(array $features): array {}
    public function get(string $feature, mixed $scope): mixed {}
    public function set(string $feature, mixed $scope, mixed $value): void {}
    public function setForAllScopes(string $feature, mixed $value): void {}
    public function delete(string $feature, mixed $scope): void {}
    public function purge(array|null $features): void {}
}
```

Now, we just need to implement each of these methods using a Redis connection. For an example of how to implement each of these methods, take a look at the `Laravel\Pennant\Drivers\DatabaseDriver` in the [Pennant source code](https://github.com/laravel/pennant/blob/1.x/src/Drivers/DatabaseDriver.php)

정보를 Redis 에 저장하는 메서드를 구현한다고 가정하겠습니다. 메서드를 구현하는 자세한 방법은 [Pennant 소스 코드](https://github.com/laravel/pennant/blob/1.x/src/Drivers/DatabaseDriver.php)에 있는 `Laravel\Pennant\Drivers\DatabaseDriver` 를 참고하면 도움이 됩니다. 

> **Note**
> Laravel does not ship with a directory to contain your extensions. You are free to place them anywhere you like. In this example, we have created an `Extensions` directory to house the `RedisFeatureDriver`.

> **Note**
> 라라벨은 이런 드라이버 확장을 구현할 때 어디에 위치해야할지 가이드를 두지는 않습니다. 원하는 곳 어디에든 파일을 작성해도 됩니다. 위의 예시에서는 `RedisFeatureDriver` 클래스를 `Extensions` 디렉토리에 생성하였습니다.

<a name="registering-the-driver"></a>
#### Registering The Driver
#### 드라이버 등록하기

Once your driver has been implemented, you are ready to register it with Laravel. To add additional drivers to Pennant, you may use the `extend` method provided by the `Feature` facade. You should call the `extend` method from the `boot` method of one of your application's [service provider](/docs/{{version}}/providers):

드라이버를 구현했다면, 라라벨에 등록할 수 있습니다. Penant 에 드라이버를 추가하려면 `Feature` 파사드의 `extend` 메서드를 사용하면 됩니다. 이 `extend` 메서드는 [서비스프로바이더](/docs/{{version}}/providers)의 `boot` 메서드 안에서 호출해야합니다. 

```php
<?php

namespace App\Providers;

use App\Extensions\RedisFeatureDriver;
use Illuminate\Contracts\Foundation\Application;
use Illuminate\Support\ServiceProvider;
use Laravel\Pennant\Feature;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Register any application services.
     */
    public function register(): void
    {
        // ...
    }

    /**
     * Bootstrap any application services.
     */
    public function boot(): void
    {
        Feature::extend('redis', function (Application $app) {
            return new RedisFeatureDriver($app->make('redis'), $app->make('events'), []);
        });
    }
}
```

Once the driver has been registered, you may use the `redis` driver in your application's `config/pennant.php` configuration file:

드라이버를 등록한 뒤에는, `config/pennant.php` 설정파일에서 드라이버를 `redis` 로 변경할 수 있습니다.

    'stores' => [

        'redis' => [
            'driver' => 'redis',
            'connection' => null,
        ],

        // ...

    ],

<a name="events"></a>
## Events
## 이벤트

Pennant dispatches a variety of events that can be useful when tracking feature flags throughout your application.

Pennant는 애플리케이션 전체에서 기능-feature 플래그를 추적할 때 유용하게 사용할 수 있는 이벤트를 제공합니다.

### `Laravel\Pennant\Events\RetrievingKnownFeature`
### `Laravel\Pennant\Events\RetrievingKnownFeature`

This event is dispatched the first time a known feature is retrieved during a request for a specific scope. This event can be useful to create and track metrics against the feature flags that are being used throughout your application.

이 이벤트는 특정 scope-범위에 대한 요청-request을 처리하면서 알려진 기능-feature이 처음 조회될 때 발생합니다. 이 이벤트는 전체 애플리케이션에서 기능-feature에 대한 메트릭(metric)을 생성하고 추적하는데 유용하게 사용할 수 있습니다.

### `Laravel\Pennant\Events\RetrievingUnknownFeature`
### `Laravel\Pennant\Events\RetrievingUnknownFeature`

This event is dispatched the first time an unknown feature is retrieved during a request for a specific scope. This event can be useful if you have intended to remove a feature flag, but may have accidentally left some stray references to it throughout your application.

이 이벤트는 특정 scope-범위에 대한 요청-request을 처리하면서 알 수 없는 기능-feature이 처음 조회될 때 발생합니다. 이 이벤트는 기능-feature 플래그를 제거했지만 실수로 애플리케이션 일부에 참조가 남아 있는것을 알아내는데 유용하게 사용할 수 있습니다.

For example, you may find it useful to listen for this event and `report` or throw an exception when it occurs:

예를 들어, 이 이벤트를 감지되면 `report` 메서드를 호출하거나 예외(exception)를 발생시키는 방식으로 사용할 수 있습니다.

```php
<?php

namespace App\Providers;

use Illuminate\Foundation\Support\Providers\EventServiceProvider as ServiceProvider;
use Illuminate\Support\Facades\Event;
use Laravel\Pennant\Events\RetrievingUnknownFeature;

class EventServiceProvider extends ServiceProvider
{
    /**
     * Register any other events for your application.
     */
    public function boot(): void
    {
        Event::listen(function (RetrievingUnknownFeature $event) {
            report("Resolving unknown feature [{$event->feature}].");
        });
    }
}
```

### `Laravel\Pennant\Events\DynamicallyDefiningFeature`
### `Laravel\Pennant\Events\DynamicallyDefiningFeature`

This event is dispatched when a class based feature is being dynamically checked for the first time during a request.

이 이벤트는 요청-request을 처리하는 중에 클래스 기반 기능-feature을 동적으로 처음 확인할 때 발생합니다.
