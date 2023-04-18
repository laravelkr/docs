# Routing

- [Routing](#routing)
  - [Basic Routing](#basic-routing)
  - [기본 라우팅](#기본-라우팅)
      - [The Default Route Files](#the-default-route-files)
      - [기본 라우트 파일](#기본-라우트-파일)
      - [Available Router Methods](#available-router-methods)
      - [사용 가능한 라우터 메소드](#사용-가능한-라우터-메소드)
      - [Dependency Injection](#dependency-injection)
      - [의존성 주입](#의존성-주입)
      - [CSRF Protection](#csrf-protection)
      - [CSRF 보호](#csrf-보호)
    - [Redirect Routes](#redirect-routes)
    - [리다이렉트 라우트](#리다이렉트-라우트)
    - [View Routes](#view-routes)
    - [뷰 라우트](#뷰-라우트)
    - [The Route List](#the-route-list)
    - [라우트 목록](#라우트-목록)
  - [Route Parameters](#route-parameters)
  - [라우트 파라미터](#라우트-파라미터)
    - [Required Parameters](#required-parameters)
    - [필수 파라미터](#필수-파라미터)
      - [Parameters \& Dependency Injection](#parameters--dependency-injection)
      - [파라미터 \& 의존성 주입](#파라미터--의존성-주입)
    - [Optional Parameters](#optional-parameters)
    - [선택적 파라미터](#선택적-파라미터)
    - [Regular Expression Constraints](#regular-expression-constraints)
    - [정규식 제약조건](#정규식-제약조건)
      - [Global Constraints](#global-constraints)
      - [전역 제약조건](#전역-제약조건)
      - [Encoded Forward Slashes](#encoded-forward-slashes)
      - [인코딩된 슬래시](#인코딩된-슬래시)
  - [Named Routes](#named-routes)
  - [이름있는 라우트](#이름있는-라우트)
      - [Generating URLs To Named Routes](#generating-urls-to-named-routes)
      - [이름있는 라우트에 대한 URL 생성](#이름있는-라우트에-대한-url-생성)
      - [Inspecting The Current Route](#inspecting-the-current-route)
      - [현재 라우트 검사](#현재-라우트-검사)
  - [Route Groups](#route-groups)
  - [라우트 그룹](#라우트-그룹)
    - [Middleware](#middleware)
    - [미들웨어](#미들웨어)
    - [Controllers](#controllers)
    - [컨트롤러](#컨트롤러)
    - [Subdomain Routing](#subdomain-routing)
    - [하위 도메인 라우팅](#하위-도메인-라우팅)
    - [Route Prefixes](#route-prefixes)
    - [라우트 접두사](#라우트-접두사)
    - [Route Name Prefixes](#route-name-prefixes)
    - [라우트 이름 접두사](#라우트-이름-접두사)
  - [Route Model Binding](#route-model-binding)
  - [라우트 모델 바인딩](#라우트-모델-바인딩)
    - [Implicit Binding](#implicit-binding)
    - [암시적 바인딩](#암시적-바인딩)
      - [Soft Deleted Models](#soft-deleted-models)
      - [소프트 삭제된 모델](#소프트-삭제된-모델)
      - [Customizing The Key](#customizing-the-key)
      - [키 커스터마이징](#키-커스터마이징)
      - [Custom Keys \& Scoping](#custom-keys--scoping)
      - [커스텀 키 \& 스코핑](#커스텀-키--스코핑)
      - [Customizing Missing Model Behavior](#customizing-missing-model-behavior)
      - [누락된 모델 동작 커스터마이징](#누락된-모델-동작-커스터마이징)
    - [Implicit Enum Binding](#implicit-enum-binding)
    - [암시적 열거형 바인딩](#암시적-열거형-바인딩)
    - [Explicit Binding](#explicit-binding)
    - [명시적 바인딩](#명시적-바인딩)
      - [Customizing The Resolution Logic](#customizing-the-resolution-logic)
      - [해석 로직 커스터마이징](#해석-로직-커스터마이징)
  - [Fallback Routes](#fallback-routes)
  - [대체 라우트](#대체-라우트)
  - [Rate Limiting](#rate-limiting)
  - [비율 제한](#비율-제한)
    - [Defining Rate Limiters](#defining-rate-limiters)
    - [비율 제한자 정의](#비율-제한자-정의)
      - [Segmenting Rate Limits](#segmenting-rate-limits)
      - [비율 제한 세분화](#비율-제한-세분화)
      - [Multiple Rate Limits](#multiple-rate-limits)
      - [여러 비율 제한](#여러-비율-제한)
    - [Attaching Rate Limiters To Routes](#attaching-rate-limiters-to-routes)
    - [라우트에 비율 제한자 연결](#라우트에-비율-제한자-연결)
      - [Throttling With Redis](#throttling-with-redis)
      - [Redis로 제한](#redis로-제한)
  - [Form Method Spoofing](#form-method-spoofing)
  - [폼 메소드 스누핑](#폼-메소드-스누핑)
  - [Accessing The Current Route](#accessing-the-current-route)
  - [현재 라우트에 접근](#현재-라우트에-접근)
  - [Cross-Origin Resource Sharing (CORS)](#cross-origin-resource-sharing-cors)
  - [교차 출처 리소스 공유 (CORS)](#교차-출처-리소스-공유-cors)
  - [Route Caching](#route-caching)
  - [라우트 캐싱](#라우트-캐싱)

<a name="basic-routing"></a>
## Basic Routing
## 기본 라우팅

The most basic Laravel routes accept a URI and a closure, providing a very simple and expressive method of defining routes and behavior without complicated routing configuration files:

가장 기본적인 Laravel 라우트는 URI와 클로저를 받아들이며, 복잡한 라우팅 설정 파일 없이 간단하고 표현적인 방법으로 라우트와 동작을 정의합니다:

    use Illuminate\Support\Facades\Route;

    Route::get('/greeting', function () {
        return 'Hello World';
    });

<a name="the-default-route-files"></a>
#### The Default Route Files
#### 기본 라우트 파일

All Laravel routes are defined in your route files, which are located in the `routes` directory. These files are automatically loaded by your application's `App\Providers\RouteServiceProvider`. The `routes/web.php` file defines routes that are for your web interface. These routes are assigned the `web` middleware group, which provides features like session state and CSRF protection. The routes in `routes/api.php` are stateless and are assigned the `api` middleware group.

모든 Laravel 라우트는 라우트 파일에 정의되며, `routes` 디렉토리에 위치합니다. 이 파일들은 어플리케이션의 `App\Providers\RouteServiceProvider`에 의해 자동으로 로드됩니다. `routes/web.php` 파일은 웹 인터페이스를 위한 라우트를 정의합니다. 이 라우트들은 세션 상태와 CSRF 보호와 같은 기능을 제공하는 `web` 미들웨어 그룹에 할당됩니다. `routes/api.php`의 라우트는 무상태이며 `api` 미들웨어 그룹에 할당됩니다.

For most applications, you will begin by defining routes in your `routes/web.php` file. The routes defined in `routes/web.php` may be accessed by entering the defined route's URL in your browser. For example, you may access the following route by navigating to `http://example.com/user` in your browser:

대부분의 어플리케이션에서는 `routes/web.php` 파일에서 라우트를 정의하기 시작합니다. `routes/web.php`에 정의된 라우트는 브라우저에서 정의된 라우트의 URL을 입력하여 접근할 수 있습니다. 예를 들어, 브라우저에서 `http://example.com/user`로 이동하여 다음 라우트에 접근할 수 있습니다:

    use App\Http\Controllers\UserController;

    Route::get('/user', [UserController::class, 'index']);

Routes defined in the `routes/api.php` file are nested within a route group by the `RouteServiceProvider`. Within this group, the `/api` URI prefix is automatically applied so you do not need to manually apply it to every route in the file. You may modify the prefix and other route group options by modifying your `RouteServiceProvider` class.

`routes/api.php` 파일에 정의된 라우트는 `RouteServiceProvider`에 의해 라우트 그룹 내에 중첩됩니다. 이 그룹 내에서 `/api` URI 접두사가 자동으로 적용되므로 파일의 모든 라우트에 수동으로 적용할 필요가 없습니다. `RouteServiceProvider` 클래스를 수정하여 접두사와 다른 라우트 그룹 옵션을 수정할 수 있습니다.

<a name="available-router-methods"></a>
#### Available Router Methods
#### 사용 가능한 라우터 메소드

The router allows you to register routes that respond to any HTTP verb:

라우터는 HTTP 메소드에 응답하는 라우트를 등록할 수 있습니다:

    Route::get($uri, $callback);
    Route::post($uri, $callback);
    Route::put($uri, $callback);
    Route::patch($uri, $callback);
    Route::delete($uri, $callback);
    Route::options($uri, $callback);

Sometimes you may need to register a route that responds to multiple HTTP verbs. You may do so using the `match` method. Or, you may even register a route that responds to all HTTP verbs using the `any` method:

때때로 여러 HTTP 메소드에 응답하는 라우트를 등록해야 할 수도 있습니다. `match` 메소드를 사용하여 이를 수행할 수 있습니다. 또는 `any` 메소드를 사용하여 모든 HTTP 메소드에 응답하는 라우트를 등록할 수도 있습니다:

    Route::match(['get', 'post'], '/', function () {
        // ...
    });

    Route::any('/', function () {
        // ...
    });

> **Note**  
> When defining multiple routes that share the same URI, routes using the `get`, `post`, `put`, `patch`, `delete`, and `options` methods should be defined before routes using the `any`, `match`, and `redirect` methods. This ensures the incoming request is matched with the correct route.
>
> **참고**
> 동일한 URI를 공유하는 여러 라우트를 정의할 때 `get`, `post`, `put`, `patch`, `delete`, `options` 메소드를 사용하는 라우트는 `any`, `match`, `redirect` 메소드를 사용하는 라우트보다 먼저 정의되어야 합니다. 이렇게 하면 들어오는 요청이 올바른 라우트와 일치하도록 보장됩니다.

<a name="dependency-injection"></a>
#### Dependency Injection
#### 의존성 주입

You may type-hint any dependencies required by your route in your route's callback signature. The declared dependencies will automatically be resolved and injected into the callback by the Laravel [service container](/docs/{{version}}/container). For example, you may type-hint the `Illuminate\Http\Request` class to have the current HTTP request automatically injected into your route callback:

라우트 콜백 서명에 라우트에 의해 필요로 하는 모든 의존성을 타입 힌트할 수 있습니다. 선언된 의존성은 Laravel [서비스 컨테이너](/docs/{{version}}/container)에 의해 자동으로 해결되고 콜백에 주입됩니다. 예를 들어, `Illuminate\Http\Request` 클래스를 타입 힌트하여 현재 HTTP 요청을 라우트 콜백에 자동으로 주입할 수 있습니다:

    use Illuminate\Http\Request;

    Route::get('/users', function (Request $request) {
        // ...
    });

<a name="csrf-protection"></a>
#### CSRF Protection
#### CSRF 보호

Remember, any HTML forms pointing to `POST`, `PUT`, `PATCH`, or `DELETE` routes that are defined in the `web` routes file should include a CSRF token field. Otherwise, the request will be rejected. You can read more about CSRF protection in the [CSRF documentation](/docs/{{version}}/csrf):

`web` 라우트 파일에 정의된 `POST`, `PUT`, `PATCH`, `DELETE` 라우트를 가리키는 모든 HTML 폼에는 CSRF 토큰 필드가 포함되어야 한다는 것을 기억하세요. 그렇지 않으면 요청이 거부됩니다. CSRF 보호에 대한 자세한 내용은 [CSRF 문서](/docs/{{version}}/csrf)에서 읽을 수 있습니다:

    <form method="POST" action="/profile">
        @csrf
        ...
    </form>

<a name="redirect-routes"></a>
### Redirect Routes
### 리다이렉트 라우트

If you are defining a route that redirects to another URI, you may use the `Route::redirect` method. This method provides a convenient shortcut so that you do not have to define a full route or controller for performing a simple redirect:

다른 URI로 리다이렉트하는 라우트를 정의하는 경우 `Route::redirect` 메소드를 사용할 수 있습니다. 이 메소드는 간단한 리다이렉트를 수행하기 위해 전체 라우트 또는 컨트롤러를 정의할 필요가 없도록 편리한 단축키를 제공합니다:

    Route::redirect('/here', '/there');

By default, `Route::redirect` returns a `302` status code. You may customize the status code using the optional third parameter:

기본적으로, `Route::redirect`는 `302` 상태 코드를 반환합니다. 선택적인 세 번째 매개변수를 사용하여 상태 코드를 사용자 정의할 수 있습니다:

    Route::redirect('/here', '/there', 301);

Or, you may use the `Route::permanentRedirect` method to return a `301` status code:

또는, `Route::permanentRedirect` 메소드를 사용하여 `301` 상태 코드를 반환할 수 있습니다:

    Route::permanentRedirect('/here', '/there');

> **Warning**  
> When using route parameters in redirect routes, the following parameters are reserved by Laravel and cannot be used: `destination` and `status`.
>
> **경고**
> 리다이렉션 라우트에서 라우트 매개변수를 사용할 때, Laravel에서 사용할 수 없는 매개변수는 `destination`과 `status`입니다.

<a name="view-routes"></a>
### View Routes
### 뷰 라우트

If your route only needs to return a [view](/docs/{{version}}/views), you may use the `Route::view` method. Like the `redirect` method, this method provides a simple shortcut so that you do not have to define a full route or controller. The `view` method accepts a URI as its first argument and a view name as its second argument. In addition, you may provide an array of data to pass to the view as an optional third argument:

라우트가 [뷰](/docs/{{version}}/views)만 반환해야 하는 경우 `Route::view` 메소드를 사용할 수 있습니다. `redirect` 메소드와 마찬가지로 이 메소드는 전체 라우트 또는 컨트롤러를 정의할 필요가 없도록 간단한 단축키를 제공합니다. `view` 메소드는 첫 번째 인수로 URI를, 두 번째 인수로 뷰 이름을 받습니다. 또한, 선택적으로 세 번째 인수로 뷰에 전달할 데이터 배열을 제공할 수 있습니다:

    Route::view('/welcome', 'welcome');

    Route::view('/welcome', 'welcome', ['name' => 'Taylor']);

> **Warning**  
> When using route parameters in view routes, the following parameters are reserved by Laravel and cannot be used: `view`, `data`, `status`, and `headers`.
>
> **경고**
> 뷰 라우트에서 라우트 매개변수를 사용할 때, Laravel에서 사용할 수 없는 매개변수는 `view`, `data`, `status`, `headers`입니다.

<a name="the-route-list"></a>
### The Route List
### 라우트 목록

The `route:list` Artisan command can easily provide an overview of all of the routes that are defined by your application:

`route:list` Artisan 명령은 어플리케이션에서 정의된 모든 라우트의 개요를 쉽게 제공할 수 있습니다:

```shell
php artisan route:list
```

By default, the route middleware that are assigned to each route will not be displayed in the `route:list` output; however, you can instruct Laravel to display the route middleware by adding the `-v` option to the command:

기본적으로, 각 라우트에 할당된 라우트 미들웨어는 `route:list` 출력에 표시되지 않습니다. 하지만, `-v` 옵션을 명령에 추가하여 Laravel에게 라우트 미들웨어를 표시하도록 지시할 수 있습니다:

```shell
php artisan route:list -v
```

You may also instruct Laravel to only show routes that begin with a given URI:

또한, Laravel에게 주어진 URI로 시작하는 라우트만 표시하도록 지시할 수 있습니다:

```shell
php artisan route:list --path=api
```

In addition, you may instruct Laravel to hide any routes that are defined by third-party packages by providing the `--except-vendor` option when executing the `route:list` command:

또한, `route:list` 명령을 실행할 때 `--except-vendor` 옵션을 제공하여 Laravel에게 제 3자 패키지에서 정의된 모든 라우트를 숨기도록 지시할 수 있습니다:

```shell
php artisan route:list --except-vendor
```

Likewise, you may also instruct Laravel to only show routes that are defined by third-party packages by providing the `--only-vendor` option when executing the `route:list` command:

동일하게, `route:list` 명령을 실행할 때 `--only-vendor` 옵션을 제공하여 Laravel에게 제 3자 패키지에서 정의된 라우트만 표시하도록 지시할 수 있습니다:

```shell
php artisan route:list --only-vendor
```

<a name="route-parameters"></a>
## Route Parameters
## 라우트 파라미터

<a name="required-parameters"></a>
### Required Parameters
### 필수 파라미터

Sometimes you will need to capture segments of the URI within your route. For example, you may need to capture a user's ID from the URL. You may do so by defining route parameters:

가끔 라우트 내에서 URI의 일부를 캡처해야 할 때가 있습니다. 예를 들어, URL에서 사용자 ID를 캡처해야 할 수 있습니다. 라우트 파라미터를 정의하여 수행할 수 있습니다:

    Route::get('/user/{id}', function (string $id) {
        return 'User '.$id;
    });

You may define as many route parameters as required by your route:

라우트에 필요한 만큼 많은 라우트 파라미터를 정의할 수 있습니다:

    Route::get('/posts/{post}/comments/{comment}', function (string $postId, string $commentId) {
        // ...
    });

Route parameters are always encased within `{}` braces and should consist of alphabetic characters. Underscores (`_`) are also acceptable within route parameter names. Route parameters are injected into route callbacks / controllers based on their order - the names of the route callback / controller arguments do not matter.

라우트 파라미터는 항상 `{}` 중괄호로 감싸져 있으며 알파벳 문자로 구성되어야 합니다. 라우트 파라미터 이름에 밑줄(`_`)도 허용됩니다. 라우트 파라미터는 순서에 따라 라우트 콜백/컨트롤러에 주입됩니다. 라우트 콜백/컨트롤러 인수의 이름은 중요하지 않습니다.

<a name="parameters-and-dependency-injection"></a>
#### Parameters & Dependency Injection
#### 파라미터 & 의존성 주입

If your route has dependencies that you would like the Laravel service container to automatically inject into your route's callback, you should list your route parameters after your dependencies:

만약 라우트에 Laravel 서비스 컨테이너가 자동으로 라우트 콜백에 주입할 의존성이 있다면, 의존성 다음에 라우트 파라미터를 나열해야 합니다:

    use Illuminate\Http\Request;

    Route::get('/user/{id}', function (Request $request, string $id) {
        return 'User '.$id;
    });

<a name="parameters-optional-parameters"></a>
### Optional Parameters
### 선택적 파라미터

Occasionally you may need to specify a route parameter that may not always be present in the URI. You may do so by placing a `?` mark after the parameter name. Make sure to give the route's corresponding variable a default value:

가끔 URI에 항상 존재하지 않는 라우트 파라미터를 지정해야 할 때가 있습니다. 파라미터 이름 뒤에 `?` 마크를 놓음으로써 수행할 수 있습니다. 라우트에 해당하는 변수에 기본값을 지정해야 합니다:

    Route::get('/user/{name?}', function (string $name = null) {
        return $name;
    });

    Route::get('/user/{name?}', function (string $name = 'John') {
        return $name;
    });

<a name="parameters-regular-expression-constraints"></a>
### Regular Expression Constraints
### 정규식 제약조건

You may constrain the format of your route parameters using the `where` method on a route instance. The `where` method accepts the name of the parameter and a regular expression defining how the parameter should be constrained:

라우트 인스턴스의 `where` 메소드를 사용하여 라우트 파라미터의 형식을 제한할 수 있습니다. `where` 메소드는 파라미터 이름과 파라미터를 제한하는 정규식을 인수로 받습니다:

    Route::get('/user/{name}', function (string $name) {
        // ...
    })->where('name', '[A-Za-z]+');

    Route::get('/user/{id}', function (string $id) {
        // ...
    })->where('id', '[0-9]+');

    Route::get('/user/{id}/{name}', function (string $id, string $name) {
        // ...
    })->where(['id' => '[0-9]+', 'name' => '[a-z]+']);

For convenience, some commonly used regular expression patterns have helper methods that allow you to quickly add pattern constraints to your routes:

편의를 위해, 일반적으로 사용되는 정규식 패턴에는 라우트에 패턴 제약조건을 빠르게 추가할 수 있는 도우미 메소드가 있습니다:

    Route::get('/user/{id}/{name}', function (string $id, string $name) {
        // ...
    })->whereNumber('id')->whereAlpha('name');

    Route::get('/user/{name}', function (string $name) {
        // ...
    })->whereAlphaNumeric('name');

    Route::get('/user/{id}', function (string $id) {
        // ...
    })->whereUuid('id');

    Route::get('/user/{id}', function (string $id) {
        //
    })->whereUlid('id');

    Route::get('/category/{category}', function (string $category) {
        // ...
    })->whereIn('category', ['movie', 'song', 'painting']);

If the incoming request does not match the route pattern constraints, a 404 HTTP response will be returned.

입력된 요청이 라우트 패턴 제약조건과 일치하지 않으면, 404 HTTP 응답이 반환됩니다.

<a name="parameters-global-constraints"></a>
#### Global Constraints
#### 전역 제약조건

If you would like a route parameter to always be constrained by a given regular expression, you may use the `pattern` method. You should define these patterns in the `boot` method of your `App\Providers\RouteServiceProvider` class:

만약 라우트 파라미터가 항상 주어진 정규식으로 제한되도록 하려면, `pattern` 메소드를 사용할 수 있습니다. 이러한 패턴은 `App\Providers\RouteServiceProvider` 클래스의 `boot` 메소드에서 정의해야 합니다:

    /**
     * Define your route model bindings, pattern filters, etc.
     */
    public function boot(): void
    {
        Route::pattern('id', '[0-9]+');
    }

Once the pattern has been defined, it is automatically applied to all routes using that parameter name:

패턴이 정의되면, 그 패턴은 자동으로 해당 파라미터 이름을 사용하는 모든 라우트에 적용됩니다:

    Route::get('/user/{id}', function (string $id) {
        // Only executed if {id} is numeric...
    });

<a name="parameters-encoded-forward-slashes"></a>
#### Encoded Forward Slashes
#### 인코딩된 슬래시

The Laravel routing component allows all characters except `/` to be present within route parameter values. You must explicitly allow `/` to be part of your placeholder using a `where` condition regular expression:

Laravel 라우팅 컴포넌트는 `/`를 제외한 모든 문자를 라우트 파라미터 값에 포함할 수 있습니다. `where` 조건 정규식을 사용하여 플레이스홀더의 일부로 `/`를 허용해야 합니다:

    Route::get('/search/{search}', function (string $search) {
        return $search;
    })->where('search', '.*');

> **Warning**  
> Encoded forward slashes are only supported within the last route segment.
>
> **경고**
> 인코딩된 슬래시는 마지막 라우트 세그먼트 내에서만 지원됩니다.

<a name="named-routes"></a>
## Named Routes
## 이름있는 라우트

Named routes allow the convenient generation of URLs or redirects for specific routes. You may specify a name for a route by chaining the `name` method onto the route definition:

이름있는 라우트는 특정 라우트에 대한 URL이나 리다이렉트를 편리하게 생성할 수 있도록 합니다. 라우트 정의에 `name` 메소드를 체인으로 연결하여 라우트에 이름을 지정할 수 있습니다:

    Route::get('/user/profile', function () {
        // ...
    })->name('profile');

You may also specify route names for controller actions:

컨트롤러 액션에 대한 라우트 이름을 지정할 수도 있습니다:

    Route::get(
        '/user/profile',
        [UserProfileController::class, 'show']
    )->name('profile');

> **Warning**  
> Route names should always be unique.
>
> **경고**
> 라우트 이름은 항상 고유해야 합니다.

<a name="generating-urls-to-named-routes"></a>
#### Generating URLs To Named Routes
#### 이름있는 라우트에 대한 URL 생성

Once you have assigned a name to a given route, you may use the route's name when generating URLs or redirects via Laravel's `route` and `redirect` helper functions:

주어진 라우트에 이름을 지정하면, Laravel의 `route` 및 `redirect` 헬퍼 함수를 통해 URL이나 리다이렉트를 생성할 때 라우트의 이름을 사용할 수 있습니다:

    // Generating URLs...
    $url = route('profile');

    // Generating Redirects...
    return redirect()->route('profile');

    return to_route('profile');

If the named route defines parameters, you may pass the parameters as the second argument to the `route` function. The given parameters will automatically be inserted into the generated URL in their correct positions:

이름있는 라우트가 파라미터를 정의하면, `route` 함수의 두 번째 인자로 파라미터를 전달할 수 있습니다. 주어진 파라미터는 자동으로 생성된 URL에 올바른 위치에 삽입됩니다:

    Route::get('/user/{id}/profile', function (string $id) {
        // ...
    })->name('profile');

    $url = route('profile', ['id' => 1]);

If you pass additional parameters in the array, those key / value pairs will automatically be added to the generated URL's query string:

배열에 추가 파라미터를 전달하면, 해당 키/값 쌍은 자동으로 생성된 URL의 쿼리 문자열에 추가됩니다:

    Route::get('/user/{id}/profile', function (string $id) {
        // ...
    })->name('profile');

    $url = route('profile', ['id' => 1, 'photos' => 'yes']);

    // /user/1/profile?photos=yes

> **Note**  
> Sometimes, you may wish to specify request-wide default values for URL parameters, such as the current locale. To accomplish this, you may use the [`URL::defaults` method](/docs/{{version}}/urls#default-values).
>
> **참고**
> 때때로, 현재 로케일과 같은 URL 파라미터에 대한 요청 전체의 기본값을 지정하고자 할 수 있습니다. 이를 위해 [`URL::defaults` 메소드](/docs/{{version}}/urls#default-values)를 사용할 수 있습니다.

<a name="inspecting-the-current-route"></a>
#### Inspecting The Current Route
#### 현재 라우트 검사

If you would like to determine if the current request was routed to a given named route, you may use the `named` method on a Route instance. For example, you may check the current route name from a route middleware:

현재 요청이 주어진 이름있는 라우트에 라우팅되었는지 확인하려면, Route 인스턴스의 `named` 메소드를 사용할 수 있습니다. 예를 들어, 라우트 미들웨어에서 현재 라우트 이름을 확인할 수 있습니다:

    use Closure;
    use Illuminate\Http\Request;
    use Symfony\Component\HttpFoundation\Response;

    /**
     * Handle an incoming request.
     *
     * @param  \Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response)  $next
     */
    public function handle(Request $request, Closure $next): Response
    {
        if ($request->route()->named('profile')) {
            // ...
        }

        return $next($request);
    }

<a name="route-groups"></a>
## Route Groups
## 라우트 그룹

Route groups allow you to share route attributes, such as middleware, across a large number of routes without needing to define those attributes on each individual route.

라우트 그룹을 사용하면 미들웨어와 같은 라우트 속성을 개별 라우트에 속성을 정의하지 않고도 많은 라우트에 공유할 수 있습니다.

Nested groups attempt to intelligently "merge" attributes with their parent group. Middleware and `where` conditions are merged while names and prefixes are appended. Namespace delimiters and slashes in URI prefixes are automatically added where appropriate.

중첩된 그룹은 부모 그룹과 "합병"을 시도합니다. 미들웨어와 `where` 조건은 병합되지만 이름과 접두사는 추가됩니다. 네임스페이스 구분자와 URI 접두사의 슬래시는 적절한 위치에 자동으로 추가됩니다.

<a name="route-group-middleware"></a>
### Middleware
### 미들웨어

To assign [middleware](/docs/{{version}}/middleware) to all routes within a group, you may use the `middleware` method before defining the group. Middleware are executed in the order they are listed in the array:

그룹 내의 모든 라우트에 [미들웨어](/docs/{{version}}/middleware)를 할당하려면, 그룹을 정의하기 전에 `middleware` 메소드를 사용할 수 있습니다. 미들웨어는 배열에 나열된 순서대로 실행됩니다:

    Route::middleware(['first', 'second'])->group(function () {
        Route::get('/', function () {
            // Uses first & second middleware...
        });

        Route::get('/user/profile', function () {
            // Uses first & second middleware...
        });
    });

<a name="route-group-controllers"></a>
### Controllers
### 컨트롤러

If a group of routes all utilize the same [controller](/docs/{{version}}/controllers), you may use the `controller` method to define the common controller for all of the routes within the group. Then, when defining the routes, you only need to provide the controller method that they invoke:

라우트 그룹이 모두 동일한 [컨트롤러](/docs/{{version}}/controllers)를 사용하는 경우, `controller` 메소드를 사용하여 그룹 내의 모든 라우트에 대한 공통 컨트롤러를 정의할 수 있습니다. 그런 다음 라우트를 정의할 때 호출하는 컨트롤러 메소드만 제공하면 됩니다:

    use App\Http\Controllers\OrderController;

    Route::controller(OrderController::class)->group(function () {
        Route::get('/orders/{id}', 'show');
        Route::post('/orders', 'store');
    });

<a name="route-group-subdomain-routing"></a>
### Subdomain Routing
### 하위 도메인 라우팅

Route groups may also be used to handle subdomain routing. Subdomains may be assigned route parameters just like route URIs, allowing you to capture a portion of the subdomain for usage in your route or controller. The subdomain may be specified by calling the `domain` method before defining the group:

라우트 그룹은 하위 도메인 라우팅을 처리하는 데에도 사용할 수 있습니다. 하위 도메인은 라우트 URI와 마찬가지로 라우트 파라미터에 할당할 수 있으며, 라우트 또는 컨트롤러에서 사용할 하위 도메인의 일부를 캡처할 수 있습니다. 하위 도메인은 그룹을 정의하기 전에 `domain` 메소드를 호출하여 지정할 수 있습니다:

    Route::domain('{account}.example.com')->group(function () {
        Route::get('user/{id}', function (string $account, string $id) {
            // ...
        });
    });

> **Warning**  
> In order to ensure your subdomain routes are reachable, you should register subdomain routes before registering root domain routes. This will prevent root domain routes from overwriting subdomain routes which have the same URI path.
>
> **경고**
> 하위 도메인 라우트에 도달할 수 있도록 하기 위해서는, 하위 도메인 라우트를 루트 도메인 라우트보다 먼저 등록해야 합니다. 이렇게 하면 루트 도메인 라우트가 동일한 URI 경로를 가진 하위 도메인 라우트를 덮어쓰는 것을 방지할 수 있습니다.

<a name="route-group-prefixes"></a>
### Route Prefixes
### 라우트 접두사

The `prefix` method may be used to prefix each route in the group with a given URI. For example, you may want to prefix all route URIs within the group with `admin`:

`prefix` 메소드는 주어진 URI로 그룹 내의 각 라우트를 접두사로 사용할 수 있습니다. 예를 들어, 그룹 내의 모든 라우트 URI를 `admin`으로 접두사로 사용하려면 다음과 같이 할 수 있습니다:

    Route::prefix('admin')->group(function () {
        Route::get('/users', function () {
            // Matches The "/admin/users" URL
        });
    });

<a name="route-group-name-prefixes"></a>
### Route Name Prefixes
### 라우트 이름 접두사

The `name` method may be used to prefix each route name in the group with a given string. For example, you may want to prefix all of the grouped route's names with `admin`. The given string is prefixed to the route name exactly as it is specified, so we will be sure to provide the trailing `.` character in the prefix:

`name` 메소드는 주어진 문자열로 그룹 내의 각 라우트 이름을 접두사로 사용할 수 있습니다. 예를 들어, 그룹화된 라우트의 모든 이름을 `admin`으로 접두사로 사용하려면 다음과 같이 할 수 있습니다. 주어진 문자열은 정확히 지정된 라우트 이름과 동일하게 접두사로 붙으므로 접두사 끝에 `.` 문자를 제공할 것입니다:

    Route::name('admin.')->group(function () {
        Route::get('/users', function () {
            // Route assigned name "admin.users"...
        })->name('users');
    });

<a name="route-model-binding"></a>
## Route Model Binding
## 라우트 모델 바인딩

When injecting a model ID to a route or controller action, you will often query the database to retrieve the model that corresponds to that ID. Laravel route model binding provides a convenient way to automatically inject the model instances directly into your routes. For example, instead of injecting a user's ID, you can inject the entire `User` model instance that matches the given ID.

모델 ID를 라우트 또는 컨트롤러 액션에 주입할 때, 해당 ID에 해당하는 모델을 데이터베이스에서 검색하는 일이 종종 있습니다. Laravel 라우트 모델 바인딩은 모델 인스턴스를 라우트에 직접 주입하는 데 편리한 방법을 제공합니다. 예를 들어, 사용자 ID를 주입하는 대신 주어진 ID와 일치하는 전체 `User` 모델 인스턴스를 주입할 수 있습니다.

<a name="implicit-binding"></a>
### Implicit Binding
### 암시적 바인딩

Laravel automatically resolves Eloquent models defined in routes or controller actions whose type-hinted variable names match a route segment name. For example:

Laravel은 라우트 또는 컨트롤러 액션에 정의된 Eloquent 모델을 자동으로 해결합니다. 타입 힌트 변수 이름이 라우트 세그먼트 이름과 일치하는 경우 말이죠. 예를 들어:

    use App\Models\User;

    Route::get('/users/{user}', function (User $user) {
        return $user->email;
    });

Since the `$user` variable is type-hinted as the `App\Models\User` Eloquent model and the variable name matches the `{user}` URI segment, Laravel will automatically inject the model instance that has an ID matching the corresponding value from the request URI. If a matching model instance is not found in the database, a 404 HTTP response will automatically be generated.

`$user` 변수가 `App\Models\User` Eloquent 모델로 타입 힌트가 지정되어 있고 변수 이름이 `{user}` URI 세그먼트와 일치하기 때문에 Laravel은 요청 URI에서 해당 값과 일치하는 ID를 가진 모델 인스턴스를 자동으로 주입합니다. 일치하는 모델 인스턴스가 데이터베이스에 없으면 404 HTTP 응답이 자동으로 생성됩니다.

Of course, implicit binding is also possible when using controller methods. Again, note the `{user}` URI segment matches the `$user` variable in the controller which contains an `App\Models\User` type-hint:

물론, 컨트롤러 메소드를 사용할 때 암시적 바인딩도 가능합니다. 다시 말하지만, `{user}` URI 세그먼트가 `App\Models\User` 타입 힌트를 포함하는 컨트롤러의 `$user` 변수와 일치함을 알아두세요:

    use App\Http\Controllers\UserController;
    use App\Models\User;

    // Route definition...
    Route::get('/users/{user}', [UserController::class, 'show']);

    // Controller method definition...
    public function show(User $user)
    {
        return view('user.profile', ['user' => $user]);
    }

<a name="implicit-soft-deleted-models"></a>
#### Soft Deleted Models
#### 소프트 삭제된 모델

Typically, implicit model binding will not retrieve models that have been [soft deleted](/docs/{{version}}/eloquent#soft-deleting). However, you may instruct the implicit binding to retrieve these models by chaining the `withTrashed` method onto your route's definition:

일반적으로, 암시적 모델 바인딩은 [소프트 삭제](/docs/{{version}}/eloquent#soft-deleting)된 모델을 검색하지 않습니다. 그러나, 라우트 정의에 `withTrashed` 메소드를 체인으로 연결하여 이러한 모델을 검색하도록 지시할 수 있습니다:

    use App\Models\User;

    Route::get('/users/{user}', function (User $user) {
        return $user->email;
    })->withTrashed();

<a name="customizing-the-key"></a>
<a name="customizing-the-default-key-name"></a>
#### Customizing The Key
#### 키 커스터마이징

Sometimes you may wish to resolve Eloquent models using a column other than `id`. To do so, you may specify the column in the route parameter definition:

때때로 `id`가 아닌 다른 컬럼을 사용하여 Eloquent 모델을 해결하고자 할 수 있습니다. 이를 위해 라우트 파라미터 정의에서 컬럼을 지정할 수 있습니다:

    use App\Models\Post;

    Route::get('/posts/{post:slug}', function (Post $post) {
        return $post;
    });

If you would like model binding to always use a database column other than `id` when retrieving a given model class, you may override the `getRouteKeyName` method on the Eloquent model:

주어진 모델 클래스를 검색할 때 `id`가 아닌 다른 데이터베이스 컬럼을 항상 사용하도록 모델 바인딩을 설정하려면 Eloquent 모델의 `getRouteKeyName` 메소드를 오버라이드할 수 있습니다:

    /**
     * Get the route key for the model.
     */
    public function getRouteKeyName(): string
    {
        return 'slug';
    }

<a name="implicit-model-binding-scoping"></a>
#### Custom Keys & Scoping
#### 커스텀 키 & 스코핑

When implicitly binding multiple Eloquent models in a single route definition, you may wish to scope the second Eloquent model such that it must be a child of the previous Eloquent model. For example, consider this route definition that retrieves a blog post by slug for a specific user:

단일 라우트 정의에서 여러 Eloquent 모델을 암시적으로 바인딩할 때 이전 Eloquent 모델의 자식이어야 하는 두 번째 Eloquent 모델을 스코프하는 것이 좋습니다. 예를 들어, 특정 사용자의 슬러그로 블로그 포스트를 검색하는 라우트 정의를 고려해보세요:

    use App\Models\Post;
    use App\Models\User;

    Route::get('/users/{user}/posts/{post:slug}', function (User $user, Post $post) {
        return $post;
    });

When using a custom keyed implicit binding as a nested route parameter, Laravel will automatically scope the query to retrieve the nested model by its parent using conventions to guess the relationship name on the parent. In this case, it will be assumed that the `User` model has a relationship named `posts` (the plural form of the route parameter name) which can be used to retrieve the `Post` model.

중첩 라우트 파라미터로 커스텀 키를 사용하는 경우 Laravel은 부모의 관계 이름을 추측하여 부모를 사용하여 중첩된 모델을 검색하는 쿼리를 자동으로 스코프합니다. 이 경우, `User` 모델이 `posts` (라우트 파라미터 이름의 복수형)라는 관계를 가지고 있고 이 관계를 사용하여 `Post` 모델을 검색할 수 있다고 가정합니다.

If you wish, you may instruct Laravel to scope "child" bindings even when a custom key is not provided. To do so, you may invoke the `scopeBindings` method when defining your route:

원한다면, 커스텀 키가 제공되지 않더라도 Laravel에게 "자식" 바인딩을 스코프하도록 지시할 수 있습니다. 이를 위해 라우트를 정의할 때 `scopeBindings` 메소드를 호출할 수 있습니다:

    use App\Models\Post;
    use App\Models\User;

    Route::get('/users/{user}/posts/{post}', function (User $user, Post $post) {
        return $post;
    })->scopeBindings();

Or, you may instruct an entire group of route definitions to use scoped bindings:

또는, 라우트 정의 그룹 전체에 스코프 바인딩을 사용하도록 지시할 수 있습니다:

    Route::scopeBindings()->group(function () {
        Route::get('/users/{user}/posts/{post}', function (User $user, Post $post) {
            return $post;
        });
    });

Similarly, you may explicitly instruct Laravel to not scope bindings by invoking the `withoutScopedBindings` method:

비슷하게, `withoutScopedBindings` 메소드를 호출하여 Laravel에게 바인딩을 스코프하지 않도록 지시할 수 있습니다:

    Route::get('/users/{user}/posts/{post:slug}', function (User $user, Post $post) {
        return $post;
    })->withoutScopedBindings();

<a name="customizing-missing-model-behavior"></a>
#### Customizing Missing Model Behavior
#### 누락된 모델 동작 커스터마이징

Typically, a 404 HTTP response will be generated if an implicitly bound model is not found. However, you may customize this behavior by calling the `missing` method when defining your route. The `missing` method accepts a closure that will be invoked if an implicitly bound model can not be found:

일반적으로, 암시적으로 바인딩된 모델이 발견되지 않으면 404 HTTP 응답이 생성됩니다. 그러나 라우트를 정의할 때 `missing` 메소드를 호출하여 이 동작을 커스터마이징할 수 있습니다. `missing` 메소드는 암시적으로 바인딩된 모델을 찾을 수 없는 경우 호출되는 클로저를 받습니다:

    use App\Http\Controllers\LocationsController;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Redirect;

    Route::get('/locations/{location:slug}', [LocationsController::class, 'show'])
            ->name('locations.view')
            ->missing(function (Request $request) {
                return Redirect::route('locations.index');
            });

<a name="implicit-enum-binding"></a>
### Implicit Enum Binding
### 암시적 열거형 바인딩

PHP 8.1 introduced support for [Enums](https://www.php.net/manual/en/language.enumerations.backed.php). To compliment this feature, Laravel allows you to type-hint a [string-backed Enum](https://www.php.net/manual/en/language.enumerations.backed.php) on your route definition and Laravel will only invoke the route if that route segment corresponds to a valid Enum value. Otherwise, a 404 HTTP response will be returned automatically. For example, given the following Enum:

PHP 8.1은 [열거형](https://www.php.net/manual/en/language.enumerations.backed.php)을 지원합니다. 이 기능을 보완하기 위해 Laravel은 라우트 정의에서 [문자열 기반 열거형](https://www.php.net/manual/en/language.enumerations.backed.php)을 타입 힌트할 수 있도록 허용하고 Laravel은 라우트 세그먼트가 유효한 열거형 값에 해당하는 경우에만 라우트를 호출합니다. 그렇지 않으면 404 HTTP 응답이 자동으로 반환됩니다. 예를 들어 다음과 같은 열거형이 있다고 가정합니다:

```php
<?php

namespace App\Enums;

enum Category: string
{
    case Fruits = 'fruits';
    case People = 'people';
}
```

You may define a route that will only be invoked if the `{category}` route segment is `fruits` or `people`. Otherwise, Laravel will return a 404 HTTP response:

`category` 라우트 세그먼트가 `fruits` 또는 `people`인 경우에만 호출되는 라우트를 정의할 수 있습니다. 그렇지 않으면 Laravel은 404 HTTP 응답을 반환합니다:

```php
use App\Enums\Category;
use Illuminate\Support\Facades\Route;

Route::get('/categories/{category}', function (Category $category) {
    return $category->value;
});
```

<a name="explicit-binding"></a>
### Explicit Binding
### 명시적 바인딩

You are not required to use Laravel's implicit, convention based model resolution in order to use model binding. You can also explicitly define how route parameters correspond to models. To register an explicit binding, use the router's `model` method to specify the class for a given parameter. You should define your explicit model bindings at the beginning of the `boot` method of your `RouteServiceProvider` class:

모델 바인딩을 사용하기 위해 Laravel의 암시적인 관례 기반 모델 해석을 사용할 필요는 없습니다. 라우트 파라미터가 모델에 어떻게 대응하는지 명시적으로 정의할 수도 있습니다. 명시적 바인딩을 등록하려면 라우터의 `model` 메소드를 사용하여 주어진 파라미터에 대한 클래스를 지정하십시오. `RouteServiceProvider` 클래스의 `boot` 메소드의 시작 부분에서 명시적 모델 바인딩을 정의해야 합니다:

    use App\Models\User;
    use Illuminate\Support\Facades\Route;

    /**
     * Define your route model bindings, pattern filters, etc.
     */
    public function boot(): void
    {
        Route::model('user', User::class);

        // ...
    }

Next, define a route that contains a `{user}` parameter:

다음으로, `{user}` 파라미터가 포함된 라우트를 정의합니다:

    use App\Models\User;

    Route::get('/users/{user}', function (User $user) {
        // ...
    });

Since we have bound all `{user}` parameters to the `App\Models\User` model, an instance of that class will be injected into the route. So, for example, a request to `users/1` will inject the `User` instance from the database which has an ID of `1`.

`{user}` 파라미터를 모두 `App\Models\User` 모델에 바인딩했으므로 해당 클래스의 인스턴스가 라우트에 주입됩니다. 따라서 예를 들어 `users/1`에 대한 요청은 ID가 `1`인 데이터베이스의 `User` 인스턴스를 주입합니다.

If a matching model instance is not found in the database, a 404 HTTP response will be automatically generated.

데이터베이스에서 일치하는 모델 인스턴스가 발견되지 않으면 404 HTTP 응답이 자동으로 생성됩니다.

<a name="customizing-the-resolution-logic"></a>
#### Customizing The Resolution Logic
#### 해석 로직 커스터마이징

If you wish to define your own model binding resolution logic, you may use the `Route::bind` method. The closure you pass to the `bind` method will receive the value of the URI segment and should return the instance of the class that should be injected into the route. Again, this customization should take place in the `boot` method of your application's `RouteServiceProvider`:

모델 바인딩 해석 로직을 직접 정의하려면 `Route::bind` 메소드를 사용할 수 있습니다. `bind` 메소드에 전달하는 클로저는 URI 세그먼트의 값과 라우트에 주입되어야 하는 클래스의 인스턴스를 반환해야 합니다. 다시 말해, 이 커스터마이징은 응용 프로그램의 `RouteServiceProvider`의 `boot` 메소드에서 이루어져야 합니다:

    use App\Models\User;
    use Illuminate\Support\Facades\Route;

    /**
     * Define your route model bindings, pattern filters, etc.
     */
    public function boot(): void
    {
        Route::bind('user', function (string $value) {
            return User::where('name', $value)->firstOrFail();
        });

        // ...
    }

Alternatively, you may override the `resolveRouteBinding` method on your Eloquent model. This method will receive the value of the URI segment and should return the instance of the class that should be injected into the route:

또는, Eloquent 모델의 `resolveRouteBinding` 메소드를 재정의할 수 있습니다. 이 메소드는 URI 세그먼트의 값과 라우트에 주입되어야 하는 클래스의 인스턴스를 반환해야 합니다:

    /**
     * Retrieve the model for a bound value.
     *
     * @param  mixed  $value
     * @param  string|null  $field
     * @return \Illuminate\Database\Eloquent\Model|null
     */
    public function resolveRouteBinding($value, $field = null)
    {
        return $this->where('name', $value)->firstOrFail();
    }

If a route is utilizing [implicit binding scoping](#implicit-model-binding-scoping), the `resolveChildRouteBinding` method will be used to resolve the child binding of the parent model:

라우트가 [암시적 바인딩 범위](#implicit-model-binding-scoping)를 사용하는 경우, `resolveChildRouteBinding` 메소드가 부모 모델의 자식 바인딩을 해석하는 데 사용됩니다:

    /**
     * Retrieve the child model for a bound value.
     *
     * @param  string  $childType
     * @param  mixed  $value
     * @param  string|null  $field
     * @return \Illuminate\Database\Eloquent\Model|null
     */
    public function resolveChildRouteBinding($childType, $value, $field)
    {
        return parent::resolveChildRouteBinding($childType, $value, $field);
    }

<a name="fallback-routes"></a>
## Fallback Routes
## 대체 라우트

Using the `Route::fallback` method, you may define a route that will be executed when no other route matches the incoming request. Typically, unhandled requests will automatically render a "404" page via your application's exception handler. However, since you would typically define the `fallback` route within your `routes/web.php` file, all middleware in the `web` middleware group will apply to the route. You are free to add additional middleware to this route as needed:

`Route::fallback` 메소드를 사용하면, 다른 라우트와 일치하지 않는 요청이 들어올 때 실행될 라우트를 정의할 수 있습니다. 일반적으로 처리되지 않은 요청은 응용 프로그램의 예외 처리기를 통해 자동으로 "404" 페이지를 렌더링합니다. 그러나, 일반적으로 `fallback` 라우트를 `routes/web.php` 파일 내에서 정의하므로 `web` 미들웨어 그룹의 모든 미들웨어가 라우트에 적용됩니다. 필요에 따라 이 라우트에 추가 미들웨어를 자유롭게 추가할 수 있습니다:

    Route::fallback(function () {
        // ...
    });

> **Warning**  
> The fallback route should always be the last route registered by your application.
>
> **경고**
> 대체 라우트는 응용 프로그램에 의해 등록된 마지막 라우트여야 합니다.

<a name="rate-limiting"></a>
## Rate Limiting
## 비율 제한

<a name="defining-rate-limiters"></a>
### Defining Rate Limiters
### 비율 제한자 정의

Laravel includes powerful and customizable rate limiting services that you may utilize to restrict the amount of traffic for a given route or group of routes. To get started, you should define rate limiter configurations that meet your application's needs. Typically, this should be done within the `configureRateLimiting` method of your application's `App\Providers\RouteServiceProvider` class, which already includes a rate limiter definition that is applied to the routes in your application's `routes/api.php` file:

Laravel에는 특정 라우트 또는 라우트 그룹의 트래픽 양을 제한하기 위해 사용할 수 있는 강력하고 사용자 정의 가능한 비율 제한 서비스가 포함되어 있습니다. 시작하려면, 응용 프로그램의 요구 사항을 충족하는 비율 제한자 구성을 정의해야 합니다. 일반적으로, 이는 응용 프로그램의 `App\Providers\RouteServiceProvider` 클래스의 `configureRateLimiting` 메소드 내에서 수행되어야 합니다. 이 메소드는 이미 응용 프로그램의 `routes/api.php` 파일의 라우트에 적용되는 비율 제한자 정의를 포함하고 있습니다:

```php
use Illuminate\Cache\RateLimiting\Limit;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\RateLimiter;

/**
 * Configure the rate limiters for the application.
 */
protected function configureRateLimiting(): void
{
    RateLimiter::for('api', function (Request $request) {
        return Limit::perMinute(60)->by($request->user()?->id ?: $request->ip());
    });
}
```

Rate limiters are defined using the `RateLimiter` facade's `for` method. The `for` method accepts a rate limiter name and a closure that returns the limit configuration that should apply to routes that are assigned to the rate limiter. Limit configuration are instances of the `Illuminate\Cache\RateLimiting\Limit` class. This class contains helpful "builder" methods so that you can quickly define your limit. The rate limiter name may be any string you wish:

비율 제한자는 `RateLimiter` facade의 `for` 메소드를 사용하여 정의됩니다. `for` 메소드는 비율 제한자 이름과 비율 제한자에 할당된 라우트에 적용되어야 하는 제한 구성을 반환하는 클로저를 받습니다. 제한 구성은 `Illuminate\Cache\RateLimiting\Limit` 클래스의 인스턴스입니다. 이 클래스에는 도움이 되는 "빌더" 메소드가 포함되어 있으므로 제한을 빠르게 정의할 수 있습니다. 비율 제한자 이름은 원하는 문자열일 수 있습니다:

    use Illuminate\Cache\RateLimiting\Limit;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\RateLimiter;

    /**
     * Configure the rate limiters for the application.
     */
    protected function configureRateLimiting(): void
    {
        RateLimiter::for('global', function (Request $request) {
            return Limit::perMinute(1000);
        });
    }

If the incoming request exceeds the specified rate limit, a response with a 429 HTTP status code will automatically be returned by Laravel. If you would like to define your own response that should be returned by a rate limit, you may use the `response` method:

입력된 요청이 지정된 비율 제한을 초과하면, Laravel에 의해 429 HTTP 상태 코드로 응답하는 응답이 자동으로 반환됩니다. 비율 제한에 의해 반환되어야 하는 자체 응답을 정의하려면 `response` 메소드를 사용할 수 있습니다:

    RateLimiter::for('global', function (Request $request) {
        return Limit::perMinute(1000)->response(function (Request $request, array $headers) {
            return response('Custom response...', 429, $headers);
        });
    });

Since rate limiter callbacks receive the incoming HTTP request instance, you may build the appropriate rate limit dynamically based on the incoming request or authenticated user:

비율 제한자 콜백은 입력된 HTTP 요청 인스턴스를 받으므로, 입력된 요청 또는 인증된 사용자에 따라 동적으로 적절한 비율 제한을 구축할 수 있습니다:

    RateLimiter::for('uploads', function (Request $request) {
        return $request->user()->vipCustomer()
                    ? Limit::none()
                    : Limit::perMinute(100);
    });

<a name="segmenting-rate-limits"></a>
#### Segmenting Rate Limits
#### 비율 제한 세분화

Sometimes you may wish to segment rate limits by some arbitrary value. For example, you may wish to allow users to access a given route 100 times per minute per IP address. To accomplish this, you may use the `by` method when building your rate limit:

때때로 임의의 값에 의해 비율 제한을 세분화하려고 할 수 있습니다. 예를 들어, 사용자가 IP 주소 당 분당 100회 특정 라우트에 액세스할 수 있도록 허용하려고 할 수 있습니다. 이를 수행하려면 비율 제한을 구축할 때 `by` 메소드를 사용할 수 있습니다:

    RateLimiter::for('uploads', function (Request $request) {
        return $request->user()->vipCustomer()
                    ? Limit::none()
                    : Limit::perMinute(100)->by($request->ip());
    });

To illustrate this feature using another example, we can limit access to the route to 100 times per minute per authenticated user ID or 10 times per minute per IP address for guests:

다른 예를 사용하여 이 기능을 설명하면, 인증된 사용자 ID 당 분당 100회 또는 게스트의 IP 주소 당 분당 10회 라우트에 액세스하는 것을 제한할 수 있습니다:

    RateLimiter::for('uploads', function (Request $request) {
        return $request->user()
                    ? Limit::perMinute(100)->by($request->user()->id)
                    : Limit::perMinute(10)->by($request->ip());
    });

<a name="multiple-rate-limits"></a>
#### Multiple Rate Limits
#### 여러 비율 제한

If needed, you may return an array of rate limits for a given rate limiter configuration. Each rate limit will be evaluated for the route based on the order they are placed within the array:

필요한 경우, 주어진 비율 제한 구성에 대해 여러 비율 제한을 반환할 수 있습니다. 각 비율 제한은 배열 내에 배치된 순서에 따라 라우트에 대해 평가됩니다:

    RateLimiter::for('login', function (Request $request) {
        return [
            Limit::perMinute(500),
            Limit::perMinute(3)->by($request->input('email')),
        ];
    });

<a name="attaching-rate-limiters-to-routes"></a>
### Attaching Rate Limiters To Routes
### 라우트에 비율 제한자 연결

Rate limiters may be attached to routes or route groups using the `throttle` [middleware](/docs/{{version}}/middleware). The throttle middleware accepts the name of the rate limiter you wish to assign to the route:

비율 제한자는 `throttle` [미들웨어](/docs/{{version}}/middleware)를 사용하여 라우트 또는 라우트 그룹에 연결할 수 있습니다. throttle 미들웨어는 라우트에 할당하려는 비율 제한자의 이름을 받습니다:

    Route::middleware(['throttle:uploads'])->group(function () {
        Route::post('/audio', function () {
            // ...
        });

        Route::post('/video', function () {
            // ...
        });
    });

<a name="throttling-with-redis"></a>
#### Throttling With Redis
#### Redis로 제한

Typically, the `throttle` middleware is mapped to the `Illuminate\Routing\Middleware\ThrottleRequests` class. This mapping is defined in your application's HTTP kernel (`App\Http\Kernel`). However, if you are using Redis as your application's cache driver, you may wish to change this mapping to use the `Illuminate\Routing\Middleware\ThrottleRequestsWithRedis` class. This class is more efficient at managing rate limiting using Redis:

일반적으로 `throttle` 미들웨어는 `Illuminate\Routing\Middleware\ThrottleRequests` 클래스에 매핑됩니다. 이 매핑은 응용 프로그램의 HTTP 커널 (`App\Http\Kernel`)에서 정의됩니다. 그러나 응용 프로그램의 캐시 드라이버로 Redis를 사용하는 경우, 이 매핑을 `Illuminate\Routing\Middleware\ThrottleRequestsWithRedis` 클래스를 사용하도록 변경할 수 있습니다. 이 클래스는 Redis를 사용하여 비율 제한을 관리하는 데 더 효율적입니다:

    'throttle' => \Illuminate\Routing\Middleware\ThrottleRequestsWithRedis::class,

<a name="form-method-spoofing"></a>
## Form Method Spoofing
## 폼 메소드 스누핑

HTML forms do not support `PUT`, `PATCH`, or `DELETE` actions. So, when defining `PUT`, `PATCH`, or `DELETE` routes that are called from an HTML form, you will need to add a hidden `_method` field to the form. The value sent with the `_method` field will be used as the HTTP request method:

HTML 폼은 `PUT`, `PATCH`, 또는 `DELETE` 액션을 지원하지 않습니다. 따라서 HTML 폼에서 호출되는 `PUT`, `PATCH`, 또는 `DELETE` 라우트를 정의할 때 폼에 숨겨진 `_method` 필드를 추가해야 합니다. `_method` 필드로 전송된 값은 HTTP 요청 메소드로 사용됩니다:

    <form action="/example" method="POST">
        <input type="hidden" name="_method" value="PUT">
        <input type="hidden" name="_token" value="{{ csrf_token() }}">
    </form>

For convenience, you may use the `@method` [Blade directive](/docs/{{version}}/blade) to generate the `_method` input field:

편의를 위해, `_method` 입력 필드를 생성하기 위해 `@method` [Blade 지시자](/docs/{{version}}/blade)를 사용할 수 있습니다:

    <form action="/example" method="POST">
        @method('PUT')
        @csrf
    </form>

<a name="accessing-the-current-route"></a>
## Accessing The Current Route
## 현재 라우트에 접근

You may use the `current`, `currentRouteName`, and `currentRouteAction` methods on the `Route` facade to access information about the route handling the incoming request:

`Route` facade의 `current`, `currentRouteName`, `currentRouteAction` 메소드를 사용하여 들어오는 요청을 처리하는 라우트에 대한 정보에 액세스할 수 있습니다:

    use Illuminate\Support\Facades\Route;

    $route = Route::current(); // Illuminate\Routing\Route
    $name = Route::currentRouteName(); // string
    $action = Route::currentRouteAction(); // string

You may refer to the API documentation for both the [underlying class of the Route facade](https://laravel.com/api/{{version}}/Illuminate/Routing/Router.html) and [Route instance](https://laravel.com/api/{{version}}/Illuminate/Routing/Route.html) to review all of the methods that are available on the router and route classes.

라우터 및 라우트 클래스에서 사용할 수 있는 모든 메소드를 검토하려면 [Route facade의 기본 클래스](https://laravel.com/api/{{version}}/Illuminate/Routing/Router.html) 및 [Route 인스턴스](https://laravel.com/api/{{version}}/Illuminate/Routing/Route.html)의 API 문서를 참조하십시오.

<a name="cors"></a>
## Cross-Origin Resource Sharing (CORS)
## 교차 출처 리소스 공유 (CORS)

Laravel can automatically respond to CORS `OPTIONS` HTTP requests with values that you configure. All CORS settings may be configured in your application's `config/cors.php` configuration file. The `OPTIONS` requests will automatically be handled by the `HandleCors` [middleware](/docs/{{version}}/middleware) that is included by default in your global middleware stack. Your global middleware stack is located in your application's HTTP kernel (`App\Http\Kernel`).

Laravel은 구성에서 지정한 값을 사용하여 CORS `OPTIONS` HTTP 요청에 대한 응답을 자동으로 생성할 수 있습니다. 모든 CORS 설정은 응용 프로그램의 `config/cors.php` 구성 파일에서 구성할 수 있습니다. `OPTIONS` 요청은 기본적으로 전역 미들웨어 스택에 포함된 `HandleCors` [미들웨어](/docs/{{version}}/middleware)에 의해 자동으로 처리됩니다. 전역 미들웨어 스택은 응용 프로그램의 HTTP 커널 (`App\Http\Kernel`)에 있습니다.

> **Note**  
> For more information on CORS and CORS headers, please consult the [MDN web documentation on CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS#The_HTTP_response_headers).
>
> **참고**
> CORS 및 CORS 헤더에 대한 자세한 내용은 [MDN 웹 문서 CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS#The_HTTP_response_headers)를 참조하십시오.

<a name="route-caching"></a>
## Route Caching
## 라우트 캐싱

When deploying your application to production, you should take advantage of Laravel's route cache. Using the route cache will drastically decrease the amount of time it takes to register all of your application's routes. To generate a route cache, execute the `route:cache` Artisan command:

응용 프로그램을 프로덕션에 배포할 때, Laravel의 라우트 캐시를 활용해야 합니다. 라우트 캐시를 사용하면 응용 프로그램의 모든 라우트를 등록하는 데 걸리는 시간을 크게 줄일 수 있습니다. 라우트 캐시를 생성하려면 `route:cache` 아티즌 명령을 실행하십시오:

```shell
php artisan route:cache
```

After running this command, your cached routes file will be loaded on every request. Remember, if you add any new routes you will need to generate a fresh route cache. Because of this, you should only run the `route:cache` command during your project's deployment.

이 명령을 실행한 후, 캐시된 라우트 파일은 모든 요청에서 로드됩니다. 새로운 라우트를 추가하면 새로운 라우트 캐시를 생성해야 한다는 점을 기억하십시오. 이러한 이유로, 프로젝트 배포 중에만 `route:cache` 명령을 실행해야 합니다.

You may use the `route:clear` command to clear the route cache:

`route:clear` 명령을 사용하여 라우트 캐시를 지울 수 있습니다:

```shell
php artisan route:clear
```
