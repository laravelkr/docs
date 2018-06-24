# HTTP Routing
# HTTP 라우팅

- [Basic Routing](#basic-routing)
- [기본적인 라우팅](#basic-routing)
- [Route Parameters](#route-parameters)
- [라우트 파라미터](#route-parameters)
    - [Required Parameters](#required-parameters)
    - [필수 파라미터](#required-parameters)
    - [Optional Parameters](#parameters-optional-parameters)
    - [선택적인 파라미터](#parameters-optional-parameters)
    - [Regular Expression Constraints](#parameters-regular-expression-constraints)
    - [정규표현식을 통한 제한](#parameters-regular-expression-constraints)
- [Named Routes](#named-routes)
- [이름이 지정된 라우트](#named-routes)
- [Route Groups](#route-groups)
- [라우트 그룹](#route-groups)
    - [Middleware](#route-group-middleware)
    - [미들웨어](#route-group-middleware)
    - [Namespaces](#route-group-namespaces)
    - [네임스페이스](#route-group-namespaces)
    - [Sub-Domain Routing](#route-group-sub-domain-routing)
    - [서브 도메인 라우팅](#route-group-sub-domain-routing)
    - [Route Prefixes](#route-group-prefixes)
    - [라우트 Prefixes](#route-group-prefixes)
- [CSRF Protection](#csrf-protection)
- [CSRF 보호하기](#csrf-protection)
    - [Introduction](#csrf-introduction)
    - [소개 ](#csrf-introduction)
    - [Excluding URIs](#csrf-excluding-uris)
    - [URI 제외하기](#csrf-excluding-uris)
    - [X-CSRF-Token](#csrf-x-csrf-token)
    - [X-CSRF-Token](#csrf-x-csrf-token)
    - [X-XSRF-Token](#csrf-x-xsrf-token)
    - [X-XSRF-Token](#csrf-x-xsrf-token)
- [Route Model Binding](#route-model-binding)
- [라우트 모델 바인딩](#route-model-binding)
- [Form Method Spoofing](#form-method-spoofing)
- [Form Method Spoofing](#form-method-spoofing)
- [Throwing 404 Errors](#throwing-404-errors)
- [404 에러 처리](#throwing-404-errors)

<a name="basic-routing"></a>
## Basic Routing
## 기본적인 라우팅

You will define most of the routes for your application in the `app/Http/routes.php` file, which is loaded by the `App\Providers\RouteServiceProvider` class. The most basic Laravel routes simply accept a URI and a `Closure`:

대부분의 라우트는 `App\Providers\RouteServiceProvider` 클래스에 의해서 로드 되는 `app/Http/routes.php` 파일에 정의 되어 있습니다. 가장 기본적인 라라벨 라우트는 URI와 `Closure` 를 인자로 전달 받습니다: 

    Route::get('/', function () {
        return 'Hello World';
    });

    Route::post('foo/bar', function () {
        return 'Hello World';
    });

    Route::put('foo/bar', function () {
        //
    });

    Route::delete('foo/bar', function () {
        //
    });

#### Registering A Route For Multiple Verbs
#### 다수의 form 메소드에 대한 라우트 등록하기

Sometimes you may need to register a route that responds to multiple HTTP verbs. You may do so using the `match` method on the `Route` [facade](/docs/{{version}}/facades):

다수의 HTTP 메소드에 대응하는 라우트를 등록하고자 할 수도 있습니다. 이 경우 `Route` [파사드](/docs/{{version}}/facades)의 `match` 메소드를 사용하면 됩니다. 

    Route::match(['get', 'post'], '/', function () {
        return 'Hello World';
    });

Or, you may even register a route that responds to all HTTP verbs using the `any` method:

또는, 전체 HTTP 메소드에 대응하는 `any` 메소드를 사용하여 라우트를 등록할 수도 있습니다. 

    Route::any('foo', function () {
        return 'Hello World';
    });

#### Generating URLs To Routes
#### 라우트에 대응하는 URL 생성하기

You may generate URLs to your application's routes using the `url` helper:

`url` 헬퍼를 사용하여 애플리케이션의 라우트에 대응하는 URL을 생성할 수 있습니다: 

    $url = url('foo');

<a name="route-parameters"></a>
## Route Parameters
## 라우트 파라미터

<a name="required-parameters"></a>
### Required Parameters
### 필수 파라미터

Of course, sometimes you will need to capture segments of the URI within your route. For example, you may need to capture a user's ID from the URL. You may do so by defining route parameters:

라우트중에 URI 세그먼트를 필요로 할 수도 있습니다. 다음과 같이 URL 에서 사용자의 ID를 확인하고자 하는 경우 입니다. 이 경우 라우트 파라미터를 정의할 수 있습니다. 

    Route::get('user/{id}', function ($id) {
        return 'User '.$id;
    });

You may define as many route parameters as required by your route:

라우트에서는 여러개의 라우트 파라미터를 정의할 수도 있습니다: 

    Route::get('posts/{post}/comments/{comment}', function ($postId, $commentId) {
        //
    });

Route parameters are always encased within "curly" braces. The parameters will be passed into your route's `Closure` when the route is executed.

라우트 파라미터는 항상 "중괄호"로 쌓여져 있습니다. 파라미터는 라우트가 실행될 때 `Closure`로 전달 될것입니다. 

> **Note:** Route parameters cannot contain the `-` character. Use an underscore (`_`) instead.

> **주의:** 라우트 파라미터는 `-` 문자열을 포함할 수 없습니다. 언더스코어 (`_`)를 사용하십시오. 

<a name="parameters-optional-parameters"></a>
### Optional Parameters
### 선택적 파라미터

Occasionally you may need to specify a route parameter, but make the presence of that route parameter optional. You may do so by placing a `?` mark after the parameter name:

때로는, 라우트 파라미터를 지정하긴 하지만, 파라미터가 선택적으로 존재하기를 원할수도 있습니다. 이 경우 파라미터 이름뒤에 `?` 를 표시하면 됩니다. 

    Route::get('user/{name?}', function ($name = null) {
        return $name;
    });

    Route::get('user/{name?}', function ($name = 'John') {
        return $name;
    });

<a name="parameters-regular-expression-constraints"></a>
### Regular Expression Constraints
### 정규표현식을 통한 제한

You may constrain the format of your route parameters using the `where` method on a route instance. The `where` method accepts the name of the parameter and a regular expression defining how the parameter should be constrained:

라우트 인스턴스에 `where` 메소드를 사용하여 라우트 파라미터의 형식을 제한할 수도 있습니다. `where` 메소드는 파라미터의 이름과 파라미터가 제한되어야 할 정규표현식을 인자로 받습니다. 

    Route::get('user/{name}', function ($name) {
        //
    })
    ->where('name', '[A-Za-z]+');

    Route::get('user/{id}', function ($id) {
        //
    })
    ->where('id', '[0-9]+');

    Route::get('user/{id}/{name}', function ($id, $name) {
        //
    })
    ->where(['id' => '[0-9]+', 'name' => '[a-z]+']);

<a name="parameters-global-constraints"></a>
#### Global Constraints
#### 전역 제한 

If you would like a route parameter to always be constrained by a given regular expression, you may use the `pattern` method. You should define these patterns in the `boot` method of your `RouteServiceProvider`:

주어진 정규표현식으로 라우트 파라미터를 항상 제한하고자 한다면, `pattern` 메소드를 사용할 수 있습니다. 이 패턴들은 `RouteServiceProvider` 의 `boot` 메소드에 정의되어야 합니다. 

    /**
     * Define your route model bindings, pattern filters, etc.
     *
     * @param  \Illuminate\Routing\Router  $router
     * @return void
     */
    public function boot(Router $router)
    {
        $router->pattern('id', '[0-9]+');

        parent::boot($router);
    }

Once the pattern has been defined, it is automatically applied to all routes using that parameter name:

패턴을 정의하고 나면, 해당 파라미터 이름을 사용하는 모든 라우트에 자동으로 적용될 것입니다. 

    Route::get('user/{id}', function ($id) {
        // Only called if {id} is numeric.
    });

<a name="named-routes"></a>
## Named Routes
## 이름이 지정된 라우트

Named routes allow you to conveniently generate URLs or redirects for a specific route. You may specify a name for a route using the `as` array key when defining the route:

이름이 지정된 라우트는 지정된 라우트에 대한 URL 이나, 리다이렉트를 생성하기 편리합니다. 라우트에 이름을 지정하려면 라우트를 정의할 때 `as` 배열 키를 사용하면 됩니다. 

    Route::get('user/profile', ['as' => 'profile', function () {
        //
    }]);

You may also specify route names for controller actions:

컨트롤러의 액션에도 라우트 이름을 지정할 수 있습니다. 

    Route::get('user/profile', [
        'as' => 'profile', 'uses' => 'UserController@showProfile'
    ]);

Instead of specifying the route name in the route array definition, you may chain the `name` method onto the end of the route definition:

라우트를 정의하는 배열에 이름을 지정하는 대신에, 라우트의 정의 부분 마지막에 `name` 메소드로 체이닝할 수도 있습니다:

    Route::get('user/profile', 'UserController@showProfile')->name('profile');

#### Route Groups & Named Routes
#### 라우트 그룹 & 이름이 지정된 라우트들

If you are using [route groups](#route-groups), you may specify an `as` keyword in the route group attribute array, allowing you to set a common route name prefix for all routes within the group:

[라우트 그룹](#route-groups)을 사용한다면, 라우트 그룹의 속성 배열에 `as` 키워드를 사용하여 그룹 안의 모든 라우트들이 공통으로 사용하는 라우트 prefix 이름을 설정할 수 있습니다. 

    Route::group(['as' => 'admin::'], function () {
        Route::get('dashboard', ['as' => 'dashboard', function () {
            // Route named "admin::dashboard"
        }]);
    });

#### Generating URLs To Named Routes
#### 이름이 지정된 라우트들에 대한 URL 생성하기

Once you have assigned a name to a given route, you may use the route's name when generating URLs or redirects via the `route` function:

주어진 라우트에 대한 이름이 할당되면, `route` 함수를 통해서 URL 또는 리다이렉션을 생성할 때 라우트 이름을 사용할 수 있습니다. 

    $url = route('profile');

    $redirect = redirect()->route('profile');

If the route defines parameters, you may pass the parameters as the second argument to the `route` method. The given parameters will automatically be inserted into the URL:

라우트에 파라미터를 정의하였다면, `route` 메소드의 두번째 인자로 파라미터를 전달할 수 있습니다. 주어진 파라미터는 자동으로 URL 에 삽입될 것입니다:

    Route::get('user/{id}/profile', ['as' => 'profile', function ($id) {
        //
    }]);

    $url = route('profile', ['id' => 1]);

<a name="route-groups"></a>
## Route Groups
## 라우트 그룹

Route groups allow you to share route attributes, such as middleware or namespaces, across a large number of routes without needing to define those attributes on each individual route. Shared attributes are specified in an array format as the first parameter to the `Route::group` method.

라우트 그룹을 사용하면 미들웨어나, 네임스페이스와 같은 라우트 속성을 공유할 수 있어, 많은 수의 라우트를 등록할 때 각각의 개별 라우트에 매번 속성들을 정의하지 않아도 되게 해줍니다. 공유하려는 속성은 배열 형식으로 지정되어 `Route::group` 메소드의 첫번째 인자로 전달됩니다. 

To learn more about route groups, we'll walk through several common use-cases for the feature.

라우트 그룹에 대해 상세하게 알아보기 위해서, 몇가지 공통적인 사용자 예시를 확인해 보겠습니다. 

<a name="route-group-middleware"></a>
### Middleware
### 미들웨어

To assign middleware to all routes within a group, you may use the `middleware` key in the group attribute array. Middleware will be executed in the order you define this array:

그룹 안의 모든 라우트에 미들웨어를 할당하기 위해서는, 그룹 속성 배열에 `middleware` 키를 사용하면 됩니다. 미들웨어는 이 배열에 정의된 순서대로 실행될 것입니다:

    Route::group(['middleware' => 'auth'], function () {
        Route::get('/', function ()    {
            // Uses Auth Middleware
        });

        Route::get('user/profile', function () {
            // Uses Auth Middleware
        });
    });

<a name="route-group-namespaces"></a>
### Namespaces
### 네임스페이스

Another common use-case for route groups is assigning the same PHP namespace to a group of controllers. You may use the `namespace` parameter in your group attribute array to specify the namespace for all controllers within the group:

라우트 그룹을 사용하는 다른 사용예로는 그룹의 컨트롤러의 그룹 안에 같은 PHP 네임스페이스를 할당하는 경우입니다. 그룹안에 있는 모든 컨트롤러에 네임스페이스를 지정하기 위해서 그룹 속성 배열에 `namespace` 파라미터를 사용하면 됩니다. 

    Route::group(['namespace' => 'Admin'], function()
    {
        // Controllers Within The "App\Http\Controllers\Admin" Namespace

        Route::group(['namespace' => 'User'], function()
        {
            // Controllers Within The "App\Http\Controllers\Admin\User" Namespace
        });
    });

Remember, by default, the `RouteServiceProvider` includes your `routes.php` file within a namespace group, allowing you to register controller routes without specifying the full `App\Http\Controllers` namespace prefix. So, we only need to specify the portion of the namespace that comes after the base `App\Http\Controllers` namespace root.

주의할점은, 기본적으로 `RouteServiceProvider` 는 `App\Http\Controllers` 네임스페이스를 접두사로 굳지 지정하지 않아도 컨트롤러가 등록되도록, 네임스페이스 그룹 안에서 `routes.php` 파일을 로드한다는 것입니다. 따라서 네임스페이스에서 필요한 부분은 `App\Http\Controllers` 루트 네임스페이스 뒷부분만 지정하면 됩니다. 

<a name="route-group-sub-domain-routing"></a>
### Sub-Domain Routing
### 서브 도메인 라우팅

Route groups may also be used to route wildcard sub-domains. Sub-domains may be assigned route parameters just like route URIs, allowing you to capture a portion of the sub-domain for usage in your route or controller. The sub-domain may be specified using the `domain` key on the group attribute array:

라우트 그룹은 또한 와일드 카드형태의 서브 도메인을 정의하는데 사용할 수도 있습니다. 서브 도메인은 라우트 URI와 같이 서브 도메인의 일부를 추출하여, 라우트 파라미터로 할당할 수 있습니다. 서브 도메인은 그룹의 속성 배열에서 `domain` 키로 지정되어집니다.

    Route::group(['domain' => '{account}.myapp.com'], function () {
        Route::get('user/{id}', function ($account, $id) {
            //
        });
    });

<a name="route-group-prefixes"></a>
### Route Prefixes
### 라우트 Prefix

The `prefix` group array attribute may be used to prefix each route in the group with a given URI. For example, you may want to prefix all route URIs within the group with `admin`:

`prefix` 그룹 배열 속성은 그룹안의 라우트에 특정 URI을 접두어로 지정할 때 사용되어집니다. 그룹의 모든 라우트 URI에 `admin` 을 붙이고 싶다면 다음과 같이 지정하면 됩니다:

    Route::group(['prefix' => 'admin'], function () {
        Route::get('users', function ()    {
            // Matches The "/admin/users" URL
        });
    });

You may also use the `prefix` parameter to specify common parameters for your grouped routes:

그룹의 라우트에 공통된 파라미터를 지정하는데 `prefix` 를 사용할 수도 있습니다.

    Route::group(['prefix' => 'accounts/{account_id}'], function () {
        Route::get('detail', function ($account_id)    {
            // Matches The accounts/{account_id}/detail URL
        });
    });

<a name="csrf-protection"></a>
## CSRF Protection
## CSRF 보호하기

<a name="csrf-introduction"></a>
### Introduction
### 소개

Laravel makes it easy to protect your application from [cross-site request forgeries](http://en.wikipedia.org/wiki/Cross-site_request_forgery). Cross-site request forgeries are a type of malicious exploit whereby unauthorized commands are performed on behalf of the authenticated user.

라라벨은 크로스 사이트 요청 위조 [cross-site request forgeries](http://en.wikipedia.org/wiki/Cross-site_request_forgery)에서 응용 프로그램을 쉽게 지킬 수 있도록 합니다. 크로스 사이트 요청 위조는 악의적인 공격의 하나이며 인증받은 사용자를 대신하여 허가 받지 않은 명령을 수행합니다. 

Laravel automatically generates a CSRF "token" for each active user session managed by the application. This token is used to verify that the authenticated user is the one actually making the requests to the application. To generate a hidden input field `_token` containing the CSRF token, you may use the `csrf_field` helper function:

라라벨은 애플리케이션에 의해서 관리되고 있는 각각의 사용자별 CSRF "토큰"을 자동으로 생성합니다. 이 토큰은 인증된 사용자가 실제로 애플리케이션에 요청을 보내고 있는지 식별하는데 사용됩니다. CSRF 토큰을 포함하는 hidden 입력 필드 _token을 생성하려면 csrf_field 헬퍼 함수를 사용하면 됩니다.

Laravel automatically generates a CSRF "token" for each active user session managed by the application. This token is used to verify that the authenticated user is the one actually making the requests to the application. CSRF 토큰을 포함하는 hidden 입력 필드를 생성하려면 `csrf_field` 헬퍼 함수를 사용하면 됩니다. 

    <?php echo csrf_field(); ?>

The `csrf_field` helper function generates the following HTML:

`csrf_field` 헬퍼 함수는 다음과 같은 HTML을 생성합니다: 

    <input type="hidden" name="_token" value="<?php echo csrf_token(); ?>">

Of course, using the Blade [templating engine](/docs/{{version}}/blade):

다음처럼 Blade [템플릿 엔진](/docs/{{version}}/blade)에서 사용할 수 있습니다.

    {{ csrf_field() }}

You do not need to manually verify the CSRF token on POST, PUT, or DELETE requests. The `VerifyCsrfToken` [HTTP middleware](/docs/{{version}}/middleware) will verify that the token in the request input matches the token stored in the session.

일일이 수동으로 POST, PUT 또는 DELETE 요청에 대한 CSRF 토큰을 확인할 필요가 없습니다. `VerifyCsrfToken` [HTTP 미들웨어](/docs/{{version}}/middleware)가 요청중인 토큰을 세션에 저장되어 있는 토큰과 일치하는지 확인할 것입니다. 

<a name="csrf-excluding-uris"></a>
### Excluding URIs From CSRF Protection
### CSRF 보호로 부터 URI 제외하기 

Sometimes you may wish to exclude a set of URIs from CSRF protection. For example, if you are using [Stripe](https://stripe.com) to process payments and are utilizing their webhook system, you will need to exclude your webhook handler route from Laravel's CSRF protection.

때때로, 특정 URI들을 CSRF 보호로 부터 제외하고자 할 수도 있습니다. 예를 들어, [Stripe](https://stripe.com)을 통해서 결제하고 웹 후킹 시스템을 사용하는 경우, 라라벨의 CSRF 보호로 부터 웹 후킹을 처리하는 라우트를 제외할 필요가 있을 것입니다.

You may exclude URIs by adding them to the `$except` property of the `VerifyCsrfToken` middleware:

`VerifyCsrfToken` 미들웨어의 `$except` 속성에 제외하고자 하는 URI들을 추가할 수 있습니다: 

    <?php

    namespace App\Http\Middleware;

    use Illuminate\Foundation\Http\Middleware\VerifyCsrfToken as BaseVerifier;

    class VerifyCsrfToken extends BaseVerifier
    {
        /**
         * The URIs that should be excluded from CSRF verification.
         *
         * @var array
         */
        protected $except = [
            'stripe/*',
        ];
    }

<a name="csrf-x-csrf-token"></a>
### X-CSRF-TOKEN
### X-CSRF-TOKEN

In addition to checking for the CSRF token as a POST parameter, the Laravel `VerifyCsrfToken` middleware will also check for the `X-CSRF-TOKEN` request header. You could, for example, store the token in a "meta" tag:

덧붙여 라라벨의 `VerifyCsrfToken` 미들웨어는 "POST" 파라미터로 CSRF 토큰을 찾기 위해서 `X-CSRF-TOKEN` 요청 헤더(request header)도 확인합니다. 사용자는 예를 들어, "메타" 태그에 토큰을 저장할 수 있습니다.

    <meta name="csrf-token" content="{{ csrf_token() }}">

Once you have created the `meta` tag, you can instruct a library like jQuery to add the token to all request headers. This provides simple, convenient CSRF protection for your AJAX based applications:

`meta` 태그를 생성하고, 이를 Jquery와 같은 라이브러릴 사용하여, 모든 request 헤더에 토큰을 추가하도록 설정할 수 있습니다. 이를 통해 AJAX 기반 애플리케이션에서 간단하고 편리한 CSRF 보호를 제공 할 수 있습니다.

    $.ajaxSetup({
            headers: {
                'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
            }
    });

<a name="csrf-x-xsrf-token"></a>
### X-XSRF-TOKEN
#### X-XSRF-TOKEN

Laravel also stores the CSRF token in a `XSRF-TOKEN` cookie. You can use the cookie value to set the `X-XSRF-TOKEN` request header. Some JavaScript frameworks, like Angular, do this automatically for you. It is unlikely that you will need to use this value manually.

또한 라라벨은 CSRF 토큰을 `XSRF-TOKEN` 쿠키에 저장합니다. 이 쿠키값을 요청 헤더(request header)에 `X-XSRF-TOKEN`을 설정하는데 사용할 수 있습니다. Angular와 같은 몇몇 자바스크립트 프레임워크는 자동으로 이 값을 사용합니다. 이렇게 하지 않으려면 수동으로 값을 설정해야합니다.

<a name="route-model-binding"></a>
## Route Model Binding
## 라우트 모델 바인딩

Laravel route model binding provides a convenient way to inject class instances into your routes. For example, instead of injecting a user's ID, you can inject the entire `User` class instance that matches the given ID.

라라벨의 라우트 모델 바인딩은 라우트에 클래스 인스턴스를 주입할 수 있는 편리한 방법을 제공합니다. 예를 들어 사용자의 ID를 넘기는 대신에 주어진 ID에 해당하는 `User` 클래스 인스턴스를 주입할 수 있습니다. 

First, use the router's `model` method to specify the class for a given parameter. You should define your model bindings in the `RouteServiceProvider::boot` method:

먼저 주어진 파라미터에 대한 클래스를 지정하기 위해서 라우트의 `model` 메소드를 사용하여야 합니다. 이 모델 바인딩은 `RouteServiceProvider::boot` 안에서 정의되어야 합니다. 

#### Binding A Parameter To A Model
#### 모델과 파라미터 바인딩하기

    public function boot(Router $router)
    {
        parent::boot($router);

        $router->model('user', 'App\User');
    }

Next, define a route that contains a `{user}` parameter:

다음으로, `{user}` 파라미터를 포함한 라우트를 정의합니다. 

    $router->get('profile/{user}', function(App\User $user) {
        //
    });

Since we have bound the `{user}` parameter to the `App\User` model, a `User` instance will be injected into the route. So, for example, a request to `profile/1` will inject the `User` instance which has an ID of 1.

`{user}` 파라미터와 `App\User` 모델이 바인딩되어 있기 때문에 라우트에는 `User` 인스턴스가 주입 될것입니다. 예를 들어 `profile/`으로 요청이 들어오면 ID가 1인 `User`의 인스턴스가 주입됩니다. 

> **Note:** If a matching model instance is not found in the database, a 404 exception will be thrown automatically.

> **주의:** 만약 데이터베이스에서 일치하는 모델 인스턴스를 찾지 못하는 경우 404 exception이 자동으로 발생합니다. 

If you wish to specify your own "not found" behavior, pass a Closure as the third argument to the `model` method:

만약 "찾지 못함"의 동작을 지정하고 싶다면 세번째 인자로 클로저를 전달하면 됩니다. 

    $router->model('user', 'App\User', function() {
        throw new NotFoundHttpException;
    });

If you wish to use your own resolution logic, you should use the `Route::bind` method. The Closure you pass to the `bind` method will receive the value of the URI segment, and should return an instance of the class you want to be injected into the route:

만약 고유한 의존성 검색 로직을 사용하려면 `Route::bind` 메소드를 사용해야 합니다. `bind` 메소드에 전달되는 클로저에는 URI 세그먼트에 해당하는 값이 전달되고 라우트에 주입할 클래스의 인스턴스를 반환해야 합니다. 

    $router->bind('user', function($value) {
        return App\User::where('name', $value)->first();
    });

<a name="form-method-spoofing"></a>
## Form Method Spoofing
## Form 메소드 Spoofing-속이기

HTML forms do not support `PUT`, `PATCH` or `DELETE` actions. So, when defining `PUT`, `PATCH` or `DELETE` routes that are called from an HTML form, you will need to add a hidden `_method` field to the form. The value sent with the `_method` field will be used as the HTTP request method:

HTML form은 `PUT`, `PATCH` 와 `DELETE` 액션을 지원하지 않습니다. 따라서 `PUT`, `PATCH` 이나 `DELETE` 로 지정된 라우트를 호출하는 HTML form을 정의한다면 `_method` 의 숨겨진 필드를 지정해야합니다. `_method` 필드로 보내진 값은 HTTP request 메소드를 판별하는데 사용됩니다. 

    <form action="/foo/bar" method="POST">
        <input type="hidden" name="_method" value="PUT">
        <input type="hidden" name="_token" value="{{ csrf_token() }}">
    </form>

To generate the hidden input field `_method`, you may also use the `method_field` helper function:

input hidden 의 `_method` 를 생성하기 위해서 `method_field` 헬퍼 함수를 사용할수도 있습니다. 

    <?php echo method_field('PUT'); ?>

Of course, using the Blade [templating engine](/docs/{{version}}/blade):

[템플릿 엔진](/docs/{{version}}/blade)에서 사용은 다음처럼 하면 됩니다:

    {{ method_field('PUT') }}

<a name="throwing-404-errors"></a>
## Throwing 404 Errors

There are two ways to manually trigger a 404 error from a route. First, you may use the `abort` helper. The `abort` helper simply throws a `Symfony\Component\HttpFoundation\Exception\HttpException` with the specified status code:

라우트에서 404 에러를 발생시키는 방법은 2가지가 있습니다. 첫번째로 `abort` 헬퍼 함수를 사용하는 것입니다. `aboard` 헬퍼는 지정된 상태 코드와 함께 `Symfony\Component\HttpFoundation\Exception\HttpException`을 발생시킵니다: 

    abort(404);

Secondly, you may manually throw an instance of `Symfony\Component\HttpKernel\Exception\NotFoundHttpException`.

두번째로 직접으로 `Symfony\Component\HttpKernel\Exception\NotFoundHttpException`을 던지는 것입니다. 

More information on handling 404 exceptions and using custom responses for these errors may be found in the [errors](/docs/{{version}}/errors#http-exceptions) section of the documentation.

404 예외 처리와 오류의 커스텀한 처리를 사용하는 보다 자세한 내용은 다음 문서의 [errors](/docs/{{version}}/errors#http-exceptions) 부분을 참조하십시오.
