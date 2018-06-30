# HTTP 라우팅

- [기본적인 라우팅](#basic-routing)
- [라우트 파라미터](#route-parameters)
    - [필수 파라미터](#required-parameters)
    - [선택적인 파라미터](#parameters-optional-parameters)
    - [정규표현식 제약](#parameters-regular-expression-constraints)
- [이름이 지정된 라우트](#named-routes)
- [라우트 그룹](#route-groups)
    - [미들웨어](#route-group-middleware)
    - [네임스페이스](#route-group-namespaces)
    - [서브 도메인 라우팅](#route-group-sub-domain-routing)
    - [라우트 Prefixes](#route-group-prefixes)
- [CSRF 보호하기](#csrf-protection)
    - [소개 ](#csrf-introduction)
    - [URI 제외하기](#csrf-excluding-uris)
    - [X-CSRF-Token](#csrf-x-csrf-token)
    - [X-XSRF-Token](#csrf-x-xsrf-token)
- [라우트 모델 바인딩](#route-model-binding)
- [Form Method Spoofing](#form-method-spoofing)
- [현재 라우트에 엑세스하기](#accessing-the-current-route)

<a name="basic-routing"></a>
## 기본적인 라우팅

모든 라라벨의 라우트는 프레임워크에 의해서 자동으로 로딩되는 `app/Http/routes.php` 파일에 선언되어 있습니다. 가장 기본적인 라라벨 라우트는 하나의 URI를 받아들여 가장 간단하고 알기쉽게 정의된 `Closure` 라우트 입니다.

    Route::get('foo', function () {
        return 'Hello World';
    });

#### 기본적인 라우트 파일

기본적인 `routes.php` 파일은 `RouteServiceProvider` 에 의해서 로딩되어지고 자동으로 `web` 미들웨어 그룹에 포함되어 세션과 CSRF 보호 기능이 제공됩니다. 애플리케이션의 대부분의 라우트들은 이 파일에 정의되어 질 것입니다.

#### 사용가능한 라우터 메소드들

라우터는 다음의 HTTP 메소드에 해당하는 응답을 위한 라우트를 등록할 수 있습니다:

    Route::get($uri, $callback);
    Route::post($uri, $callback);
    Route::put($uri, $callback);
    Route::patch($uri, $callback);
    Route::delete($uri, $callback);
    Route::options($uri, $callback);

때로는 여러개의 HTTP 메소드에 응답하는 라우트를 등록해야 할 수도 있습니다. 이경우 `match` 메소드를 사용하면 됩니다. 또는 `any` 메소드를 사용하여 모든 HTTP 메소드에 응답하는 라우트를 등록할 수도 있습니다.

    Route::match(['get', 'post'], '/', function () {
        //
    });

    Route::any('foo', function () {
        //
    });

<a name="route-parameters"></a>
## 라우트 파라미터

<a name="required-parameters"></a>
### 필수 파라미터

라우트중에 URI 세그먼트를 필요로 할 수도 있습니다. 다음과 같이 URL 에서 사용자의 ID를 확인하고자 하는 경우 입니다. 이 경우 라우트 파라미터를 정의할 수 있습니다.

    Route::get('user/{id}', function ($id) {
        return 'User '.$id;
    });

라우트에서는 여러개의 라우트 파라미터를 정의할 수도 있습니다:

    Route::get('posts/{post}/comments/{comment}', function ($postId, $commentId) {
        //
    });

라우트 파라미터는 항상 "중괄호"로 싸여져 있습니다. 파라미터는 라우트가 실행될 때 `Closure`로 전달 될것입니다.

> **주의:** 라우트 파라미터는 `-` 문자열을 포함할 수 없습니다. 언더스코어 (`_`)를 사용하십시오.

<a name="parameters-optional-parameters"></a>
### 선택적 파라미터

때로는, 라우트 파라미터를 지정하긴 하지만, 파라미터가 선택적으로 존재하기를 원할수도 있습니다. 이 경우 파라미터 이름뒤에 `?` 를 표시하면 됩니다. 라우트 파라미터와 일치하는 변수가 기본값을 가지는지 확인하십시오:

    Route::get('user/{name?}', function ($name = null) {
        return $name;
    });

    Route::get('user/{name?}', function ($name = 'John') {
        return $name;
    });

<a name="parameters-regular-expression-constraints"></a>
### 정규 표현식 제약

라우트 인스턴스에 `where` 메소드를 사용하여 라우트 파라미터들의 포맷을 제한할 수 있습니다. `where` 메소드는 파라미터의 이름과 파라미터가 어떻게 규정되어야 하는지 나타내는 정규표현식을 인자로 전달 받습니다:

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
#### 글로벌 제약

라우트 파라미터가 항상 주어진 정규표현식으로 제약을 가지게 된다면, `pattern` 메소드를 사용할 수 있습니다. 이 패턴들은 `RouteServiceProvider`의 `boot` 메소드 안에서 사용되어야 합니다:

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

패턴을 한번 정의하고나면, 해당 파라미터 이름을 사용하는 모든 라우트들에 자동으로 적용됩니다:

    Route::get('user/{id}', function ($id) {
        // Only called if {id} is numeric.
    });

<a name="named-routes"></a>
## 이름이 지정된 라우트

이름이 지정된 라우트는 지정된 라우트에 대한 URL 이나, 리다이렉트를 생성하기 편리합니다. 라우트에 이름을 지정하려면 라우트를 정의할 때 `as` 배열 키를 사용하면 됩니다.

    Route::get('user/profile', ['as' => 'profile', function () {
        //
    }]);

컨트롤러의 액션에도 라우트 이름을 지정할 수 있습니다.

    Route::get('user/profile', [
        'as' => 'profile', 'uses' => 'UserController@showProfile'
    ]);

라우트를 정의하는 배열에 이름을 지정하는 대신에, 라우트의 정의 부분 마지막에 `name` 메소드로 체이닝할 수도 있습니다:

    Route::get('user/profile', 'UserController@showProfile')->name('profile');

#### 라우트 그룹 & 이름이 지정된 라우트들

[라우트 그룹](#route-groups)을 사용한다면, 라우트 그룹의 속성 배열에 `as` 키워드를 사용하여 그룹 안의 모든 라우트들이 공통으로 사용하는 라우트 prefix 이름을 설정할 수 있습니다.

    Route::group(['as' => 'admin::'], function () {
        Route::get('dashboard', ['as' => 'dashboard', function () {
            // Route named "admin::dashboard"
        }]);
    });

#### 이름이 지정된 라우트들에 대한 URL 생성하기

주어진 라우트에 대한 이름이 할당되면, 전역 `route` 함수를 통해서 URL 또는 리다이렉션을 생성할 때 라우트 이름을 사용할 수 있습니다.

    // Generating URLs...
    $url = route('profile');

    // Generating Redirects...
    return redirect()->route('profile');

이름이 지정된 라우트에 파라미터를 정의하였다면, `route` 메소드의 두번째 인자로 파라미터를 전달할 수 있습니다. 주어진 파라미터는 자동으로 올바른 위치에 있는 URL 에 삽입될 것입니다:

    Route::get('user/{id}/profile', ['as' => 'profile', function ($id) {
        //
    }]);

    $url = route('profile', ['id' => 1]);

<a name="route-groups"></a>
## 라우트 그룹

라우트 그룹을 사용하면 미들웨어나, 네임스페이스와 같은 라우트 속성을 공유할 수 있어, 많은 수의 라우트를 등록할 때 각각의 개별 라우트에 매번 속성들을 정의하지 않아도 되게 해줍니다. 공유하려는 속성은 배열 형식으로 지정되어 `Route::group` 메소드의 첫번째 인자로 전달됩니다.

라우트 그룹에 대해 상세하게 알아보기 위해서, 몇가지 공통적인 사용자 예시를 확인해 보겠습니다.

<a name="route-group-middleware"></a>
### 미들웨어

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
### 네임스페이스

라우트 그룹을 사용하는 다른 사용예로는 그룹의 컨트롤러의 그룹 안에 같은 PHP 네임스페이스를 할당하는 경우입니다. 그룹안에 있는 모든 컨트롤러에 네임스페이스를 지정하기 위해서 그룹 속성 배열에 `namespace` 파라미터를 사용하면 됩니다.

    Route::group(['namespace' => 'Admin'], function()
    {
        // Controllers Within The "App\Http\Controllers\Admin" Namespace

        Route::group(['namespace' => 'User'], function() {
            // Controllers Within The "App\Http\Controllers\Admin\User" Namespace
        });
    });

주의할점은, 기본적으로 `RouteServiceProvider` 는 `App\Http\Controllers` 네임스페이스를 접두사로 굳이 지정하지 않아도 컨트롤러가 등록되도록, 네임스페이스 그룹 안에서 `routes.php` 파일을 로드한다는 것입니다. 따라서 네임스페이스에서 필요한 부분은 `App\Http\Controllers` 네임스페이스 뒷부분만 지정하면 됩니다.

<a name="route-group-sub-domain-routing"></a>
### 서브 도메인 라우팅

라우트 그룹은 또한 와일드 카드형태의 서브 도메인을 정의하는데 사용할 수도 있습니다. 서브 도메인은 라우트 URI와 같이 서브 도메인의 일부를 추출하여, 라우트 파라미터로 할당할 수 있습니다. 서브 도메인은 그룹의 속성 배열에서 `domain` 키로 지정되어집니다.

    Route::group(['domain' => '{account}.myapp.com'], function () {
        Route::get('user/{id}', function ($account, $id) {
            //
        });
    });

<a name="route-group-prefixes"></a>
### 라우트 Prefix

`prefix` 그룹 속성은 그룹안의 라우트에 특정 URI을 접두어로 지정할 때 사용되어집니다. 그룹의 모든 라우트 URI에 `admin` 을 붙이고 싶다면 다음과 같이 지정하면 됩니다:

    Route::group(['prefix' => 'admin'], function () {
        Route::get('users', function ()    {
            // Matches The "/admin/users" URL
        });
    });

그룹의 라우트에 공통된 파라미터를 지정하는데 `prefix` 를 사용할 수도 있습니다.

    Route::group(['prefix' => 'accounts/{account_id}'], function () {
        Route::get('detail', function ($accountId)    {
            // Matches The "/accounts/{account_id}/detail" URL
        });
    });

<a name="csrf-protection"></a>
## CSRF 보호하기

<a name="csrf-introduction"></a>
### 소개

라라벨은 크로스 사이트 요청 위조 [cross-site request forgeries](http://en.wikipedia.org/wiki/Cross-site_request_forgery) (CSRF) 공격으로 부터 응용 프로그램을 쉽게 지킬 수 있도록 합니다. 크로스 사이트 요청 위조는 악의적인 공격의 하나이며 인증받은 사용자를 대신하여 허가 받지 않은 명령을 수행합니다.

라라벨은 애플리케이션에 의해서 관리되고 있는 각각의 사용자별 CSRF "토큰"을 자동으로 생성합니다. 이 토큰은 인증된 사용자가 실제로 애플리케이션에 요청을 보내고 있는지 식별하는데 사용됩니다.

애플리케이션에서 HTML form을 정의할 때는, form에서 이 숨겨진 CSRF 토큰 필드를 포함하도록 해야하고, 이에 따라서 CSRF 보호 미들웨어가 요청이 유효한지 확인할 것입니다. CSRF 토큰값을 가지는 `_token`라는 이름의 숨겨진 입력 필드를 생성하려면 `csrf_field` 헬퍼 함수를 사용하면 됩니다:

    // Vanilla PHP
    <?php echo csrf_field(); ?>

    // Blade Template Syntax
    {{ csrf_field() }}

`csrf_field` 헬퍼 함수는 다음과 같은 HTML을 생성합니다:

    <input type="hidden" name="_token" value="<?php echo csrf_token(); ?>">


일일이 수동으로 POST, PUT 또는 DELETE 요청에 대한 CSRF 토큰을 확인할 필요가 없습니다. `web` 미들웨어 그룹안에 포함되어 있는 `VerifyCsrfToken` [미들웨어](/docs/{{version}}/middleware)가 자동으로 요청중인 토큰이 세션에 저장되어 있는 토큰과 일치하는지 확인할 것입니다.

<a name="csrf-excluding-uris"></a>
### CSRF 보호로 부터 URI 제외하기

때때로, 특정 URI들을 CSRF 보호로 부터 제외하고자 할 수도 있습니다. 예를 들어, [Stripe](https://stripe.com)을 통해서 결제하고 웹 후킹 시스템을 사용하는 경우, 라라벨의 CSRF 보호로 부터 웹 후킹을 처리하는 라우트를 제외할 필요가 있을 것입니다.

여러분은 `routes.php` 파일 안에서 제외하고자 하는 URI를 선언한 라우트를 `web` 미들웨어 그룹의 밖에 선언하거나, `VerifyCsrfToken` 미들웨어의 `$except` 속성에 제외하고자 하는 URI들을 추가할 수 있습니다:

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

덧붙여 라라벨의 `VerifyCsrfToken` 미들웨어는 "POST" 파라미터로 CSRF 토큰을 찾기 위해서 `X-CSRF-TOKEN` 요청 헤더(request header)도 확인합니다. 사용자는 예를 들어, "메타" 태그에 토큰을 저장할 수 있습니다.

    <meta name="csrf-token" content="{{ csrf_token() }}">

`meta` 태그를 생성하고, 이를 Jquery와 같은 라이브러릴 사용하여, 모든 request 헤더에 토큰을 추가하도록 설정할 수 있습니다. 이를 통해 AJAX 기반 애플리케이션에서 간단하고 편리한 CSRF 보호를 제공 할 수 있습니다.

    $.ajaxSetup({
            headers: {
                'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
            }
    });

<a name="csrf-x-xsrf-token"></a>
### X-XSRF-TOKEN

또한 라라벨은 CSRF 토큰을 `XSRF-TOKEN` 쿠키에 저장합니다. 이 쿠키값을 요청 헤더(request header)에 `X-XSRF-TOKEN`을 설정하는데 사용할 수 있습니다. Angular와 같은 몇몇 자바스크립트 프레임워크는 자동으로 이 값을 사용합니다. 이렇게 하지 않으려면 수동으로 값을 설정해야합니다.

<a name="route-model-binding"></a>
## 라우트 모델 바인딩

라라벨의 라우트 모델 바인딩은 라우트에 모델 인스턴스를 주입할 수 있는 편리한 방법을 제공합니다. 예를 들어 사용자의 ID를 넘기는 대신에 주어진 ID에 해당하는 `User` 모델 인스턴스를 주입할 수 있습니다.

먼저 주어진 파라미터에 대한 클래스를 지정하기 위해서 라우트의 `model` 메소드를 사용하여야 합니다. 이 모델 바인딩은 `RouteServiceProvider::boot` 안에서 정의되어야 합니다.

### 묵시적 바인딩

라라벨은 자동으로 라우트나 컨트롤러 액션에서 라우트 세그먼트이름과 일치하는 변수에 해당하는 타입-힌트가 되어있는 Eloquent 모델을 의존성 해결해줄 것입니다. 예를 들면:

    Route::get('api/users/{user}', function (App\User $user) {
        return $user->email;
    });

이 예제에서, 라우트에 정의된 Eloquent 타입-힌트가 되어 있는 `$user` 변수가 라우트 URI의 `{user}` 세그먼트와 일치하면, 라라벨은 자동으로 요청된 URI에서 일치하는 ID 값에 대한 모델 인스턴스를 주입할 것입니다.

데이터베이스에서 일치하는 모델 인스턴스를 찾지 못한다면, 404 HTTP 응답이 자동으로 생성됩니다.

#### 키의 이름을 변경하기

모델을 찾을 때 `id` 와는 다른 데이터베이스 컬럼을 사용하는 묵시적 모델 바인딩을 하고자 한다면, Eloquent 모델의 `getRouteKeyName` 메소드를 재지정하면 됩니다:

    /**
     * Get the route key for the model.
     *
     * @return string
     */
    public function getRouteKeyName()
    {
        return 'slug';
    }

### 명시적 바인딩

명시적 바인딩을 등록하기 위해서, 주어진 파라미터에 대한 클래스를 지정하려면 라우터의 `model` 메소드를 사용하십시오. `RouteServiceProvider::boot` 메소드 안에서 모델 바인딩을 등록해야 합니다.

#### 모델과 파라미터 바인딩하기

    public function boot(Router $router)
    {
        parent::boot($router);

        $router->model('user', 'App\User');
    }

다음으로, `{user}` 파라미터를 포함한 라우트를 정의합니다.

    $router->get('profile/{user}', function(App\User $user) {
        //
    });

`{user}` 파라미터와 `App\User` 모델이 바인딩되어 있기 때문에 라우트에는 `User` 인스턴스가 주입 될것입니다. 예를 들어 `profile/`으로 요청이 들어오면 ID가 1인 `User`의 인스턴스가 주입됩니다.

만약 데이터베이스에서 일치하는 모델 인스턴스를 찾지 못하는 경우, 404 응답이 자동으로 생성될 것입니다.

#### 의존성 해결 로직 커스터마이징하기

만약 고유한 의존성 검색 로직을 사용하려면 `Route::bind` 메소드를 사용해야 합니다. `bind` 메소드에 전달되는 `클로저`에는 URI 세그먼트에 해당하는 값이 전달되고 라우트에 주입할 클래스의 인스턴스를 반환해야 합니다.

    $router->bind('user', function ($value) {
        return App\User::where('name', $value)->first();
    });

#### 모델을 "찾지 못했을 때" 어떻게 할지 커스터마이징하기

만약 모델을 "찾지 못함"의 동작을 지정하고 싶다면 세번째 인자로 `클로저`를 전달하면 됩니다.

    $router->model('user', 'App\User', function () {
        throw new NotFoundHttpException;
    });

<a name="form-method-spoofing"></a>
## Form 메소드 Spoofing-속이기

HTML form은 `PUT`, `PATCH` 와 `DELETE` 액션을 지원하지 않습니다. 따라서 `PUT`, `PATCH` 나 `DELETE` 로 지정된 라우트를 호출하는 HTML form을 정의한다면 `_method` 의 숨겨진 필드를 지정해야합니다. `_method` 필드로 보내진 값은 HTTP request 메소드를 판별하는데 사용됩니다:

    <form action="/foo/bar" method="POST">
        <input type="hidden" name="_method" value="PUT">
        <input type="hidden" name="_token" value="{{ csrf_token() }}">
    </form>

input hidden 의 `_method` 를 생성하기 위해서 `method_field` 헬퍼 함수를 사용할수도 있습니다.

    <?php echo method_field('PUT'); ?>

[템플릿 엔진](/docs/{{version}}/blade)에서 사용은 다음처럼 하면 됩니다:

    {{ method_field('PUT') }}

<a name="accessing-the-current-route"></a>
## 현재 라우트에 엑세스하기

`Route::current()` 메소드는 현재 HTTP 요청에 대해서 처리되는 라우트를 반환하여, `Illuminate\Routing\Route` 인스턴스를 확인할 수 있도록 해줍니다:

    $route = Route::current();

    $name = $route->getName();

    $actionName = $route->getActionName();

`Route` 파사드의 `currentRouteName` 와 `currentRouteAction` 헬퍼 메소드를 사용할 수도 있으며, 이를 통해서 현재 라우트의 이름과 액션에 엑세스 할 수 있습니다:

    $name = Route::currentRouteName();

    $action = Route::currentRouteAction();

모든 메소드를 확인하고자 한다면 [Route 파사드 뒤에서 동작하는 클래스](http://laravel.com/api/{{version}}/Illuminate/Routing/Router.html) 와 [Route 인스턴스](http://laravel.com/api/{{version}}/Illuminate/Routing/Route.html) API 문서를 참고하십시오.
