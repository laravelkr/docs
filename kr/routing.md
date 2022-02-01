# Routing
# 라우팅

- [Basic Routing](#basic-routing)
- [기본적인 라우팅](#basic-routing)
    - [Redirect Routes](#redirect-routes)
    - [리다이렉트 라우트](#redirect-routes)
    - [View Routes](#view-routes)
    - [뷰-View 라우트](#view-routes)
- [Route Parameters](#route-parameters)
- [라우트 파라미터](#route-parameters)
    - [Required Parameters](#required-parameters)
    - [필수 파라미터](#required-parameters)
    - [Optional Parameters](#parameters-optional-parameters)
    - [선택적인 파라미터](#parameters-optional-parameters)
    - [Regular Expression Constraints](#parameters-regular-expression-constraints)
    - [정규표현식 제약](#parameters-regular-expression-constraints)
- [Named Routes](#named-routes)
- [이름이 지정된 라우트](#named-routes)
- [Route Groups](#route-groups)
- [라우트 그룹](#route-groups)
    - [Middleware](#route-group-middleware)
    - [미들웨어](#route-group-middleware)
- [Subdomain Routing](#route-group-subdomain-routing)
    - [서브 도메인 라우팅](#route-group-subdomain-routing)
    - [Route Prefixes](#route-group-prefixes)
    - [라우트 접두사](#route-group-prefixes)
    - [Route Name Prefixes](#route-group-name-prefixes)
    - [라우트 이름 접두사](#route-group-name-prefixes)
- [Route Model Binding](#route-model-binding)
- [라우트 모델 바인딩](#route-model-binding)
    - [Implicit Binding](#implicit-binding)
    - [묵시적 바인딩](#implicit-binding)
    - [Explicit Binding](#explicit-binding)
    - [명시적 바인딩](#explicit-binding)
- [Fallback Routes](#fallback-routes)
- [대체 라우트](#fallback-routes)
- [Rate Limiting](#rate-limiting)
- [Rate 제한](#rate-limiting)
- [Form Method Spoofing](#form-method-spoofing)
- [Form-폼 메소드 Sppring-스푸핑](#form-method-spoofing)
- [Accessing The Current Route](#accessing-the-current-route)
- [현재 라우트에 엑세스하기](#accessing-the-current-route)
- [Cross-Origin Resource Sharing (CORS)](#cors)
- [크로스 오리진 리소스 쉐어링 (CORS)](#cors)

<a name="basic-routing"></a>
## Basic Routing
## 기본적인 라우팅

The most basic Laravel routes accept a URI and a `Closure`, providing a very simple and expressive method of defining routes:

가장 기본적인 라라벨 라우트는 URI와 `클로저`를 전달 받아, 라우팅을 정의하는 간단하고 쉽게 이해할 수 있는 방법을 제공합니다.

    Route::get('foo', function () {
        return 'Hello World';
    });

#### The Default Route Files
#### 기본 라우트 파일

All Laravel routes are defined in your route files, which are located in the `routes` directory. These files are automatically loaded by the framework. The `routes/web.php` file defines routes that are for your web interface. These routes are assigned the `web` middleware group, which provides features like session state and CSRF protection. The routes in `routes/api.php` are stateless and are assigned the `api` middleware group.

모든 라라벨의 라우트는 `route` 디렉토리 안에 들어 있는 라우트 파일에 정의되어 있습니다. 이 파일들은 프레임워크에 의해서 자동으로 로드됩니다. `routes/web.php` 파일은 웹 인터페이스를 위한 라우트들을 정의합니다. 이 라우트들에는 세션 상태와 CSRF 보호와 같은 기능을 제공하는 `web` 미들웨어 그룹이 할당되어 있습니다. `routes/api.php` 안에 들어 있는 라우트들은 stateless 하고 `api` 미들웨어 그룹이 할당되어 있습니다.

For most applications, you will begin by defining routes in your `routes/web.php` file. The routes defined in `routes/web.php` may be accessed by entering the defined route's URL in your browser. For example, you may access the following route by navigating to `http://your-app.test/user` in your browser:

대부분의 애플리케이션에서, 여러분은 `routes/web.php` 파일에 라우트를 정의하여 시작할 수 있습니다. `routes/web.php` 에 정의된 라우트는 브라우저를 통해서 유입되는 라우트 URL을 정의하는데 사용됩니다. 예를 들어 브라우저에서 `http://your-app.test/user`와 같이 접속하기 위해서 다음의 라우트를 정의할 수 있습니다.

    use App\Http\Controllers\UserController;

    Route::get('/user', [UserController::class, 'index']);

Routes defined in the `routes/api.php` file are nested within a route group by the `RouteServiceProvider`. Within this group, the `/api` URI prefix is automatically applied so you do not need to manually apply it to every route in the file. You may modify the prefix and other route group options by modifying your `RouteServiceProvider` class.

`routes/api.php` 파일은 `RouteServiceProvider`의 라우트 그룹안에 중첩되어 정의되어 있습니다. 이 그룹을에 의해서 `/api` URI가 자동으로 앞에 붙게 되므로, 이 파일에 정의한 모든 라우트에 일일이 적용하지 않아도 됩니다. `RouteServiceProvider` 파일의 라우트 그룹 옵션을 수정하면, 다른 prefix를 붙일 수도 있습니다.

#### Available Router Methods
#### 가능한 라우터 메소드

The router allows you to register routes that respond to any HTTP verb:

라우터는 다음의 HTTP 메소드에 해당하는 응답을 위한 라우트를 등록할 수 있습니다.

    Route::get($uri, $callback);
    Route::post($uri, $callback);
    Route::put($uri, $callback);
    Route::patch($uri, $callback);
    Route::delete($uri, $callback);
    Route::options($uri, $callback);

Sometimes you may need to register a route that responds to multiple HTTP verbs. You may do so using the `match` method. Or, you may even register a route that responds to all HTTP verbs using the `any` method:

때로는 여러개의 HTTP 메소드에 응답하는 라우트를 등록해야 할 수도 있습니다. 이경우 `match` 메소드를 사용하면 됩니다. 또는 `any` 메소드를 사용하여 모든 HTTP 메소드에 응답하는 라우트를 등록할 수도 있습니다.

    Route::match(['get', 'post'], '/', function () {
        //
    });

    Route::any('/', function () {
        //
    });

#### CSRF Protection
#### CSRF 보호하기

Any HTML forms pointing to `POST`, `PUT`, `PATCH`, or `DELETE` routes that are defined in the `web` routes file should include a CSRF token field. Otherwise, the request will be rejected. You can read more about CSRF protection in the [CSRF documentation](/docs/{{version}}/csrf):

`web` 라우트 파일 안에 정의된 `POST`, `PUT`, `PATCH` 또는 `DELETE` 를 가리키는 라우트들은 모두 CSRF 토큰 필드를 포함해야 합니다. 그렇지 않으면, 이 request들은 거부될 것입니다. CSRF 보호에 대해서 더 알아보려면 [CSRF 문서](/docs/{{version}}/csrf)를 읽어보십시오.

    <form method="POST" action="/profile">
        @csrf
        ...
    </form>

<a name="redirect-routes"></a>
### Redirect Routes
### 리다이렉트 라우트

If you are defining a route that redirects to another URI, you may use the `Route::redirect` method. This method provides a convenient shortcut so that you do not have to define a full route or controller for performing a simple redirect:

다른 URI로 리다이렉트 시키는 라우트를 정의하려면, `Route::redirect` 메소드를 사용할 수 있습니다. 이 메소드는 간단한 리다이렉트를 위해서 복잡한 라우트나 컨트롤러 전체를 정의하지 않아도 되는 편리한 방법을 제공합니다.

    Route::redirect('/here', '/there');

By default, `Route::redirect` returns a `302` status code. You may customize the status code using the optional third parameter:

기본적으로, `Route::redirect` 는 `302` 상태코드를 반환합니다. 만약 다른 상태코드를 반환하도록 커스터마이징 하고자 한다면, 세번째 인자를 옵션으로 전달하십시오.

    Route::redirect('/here', '/there', 301);

You may use the `Route::permanentRedirect` method to return a `301` status code:

`Route::permanentRedirect` 메소드를 사용하여 `301` 상태 코드를 반환 할 수 있습니다.

    Route::permanentRedirect('/here', '/there');

<a name="view-routes"></a>
### View Routes
### 뷰-View 라우트

If your route only needs to return a view, you may use the `Route::view` method. Like the `redirect` method, this method provides a simple shortcut so that you do not have to define a full route or controller. The `view` method accepts a URI as its first argument and a view name as its second argument. In addition, you may provide an array of data to pass to the view as an optional third argument:

단지 뷰를 반환하기만 하는 라우트가 필요하다면, `Route::view` 메소드를 사용할 수 있습니다. `redirect` 메소드와 같이 이 메소드는 간단한 리다이렉트를 위해서 복잡한 라우트나 컨트롤러 전체를 정의하지 않아도 되는 편리한 방법을 제공합니다. `view` 메소드는 첫번째 인자로 URI를, 두번째 인자로 뷰 파일의 이름을 전달 받습니다. 또한 이에 더해, 세번째 인자로 view 에 제공할 데이터들의 배열을 제공할 수도 있습니다.

    Route::view('/welcome', 'welcome');

    Route::view('/welcome', 'welcome', ['name' => 'Taylor']);

<a name="route-parameters"></a>
## Route Parameters
## 라우트 파라미터

<a name="required-parameters"></a>
### Required Parameters
### 필수 파라미터

Sometimes you will need to capture segments of the URI within your route. For example, you may need to capture a user's ID from the URL. You may do so by defining route parameters:

라우트중에 URI 세그먼트를 필요로 할 수도 있습니다. 다음과 같이 URL 에서 사용자의 ID를 확인하고자 하는 경우 입니다. 이 경우 라우트 파라미터를 정의할 수 있습니다.

    Route::get('user/{id}', function ($id) {
        return 'User '.$id;
    });

You may define as many route parameters as required by your route:

라우트에서는 여러개의 라우트 파라미터를 정의할 수도 있습니다.

    Route::get('posts/{post}/comments/{comment}', function ($postId, $commentId) {
        //
    });

Route parameters are always encased within `{}` braces and should consist of alphabetic characters, and may not contain a `-` character. Instead of using the `-` character, use an underscore (`_`). Route parameters are injected into route callbacks / controllers based on their order - the names of the callback / controller arguments do not matter.

라우트 파라미터는 항상 "{}"(중괄호)로 싸여져 있고, `-` 문자를 포함하지 않은 알파벳 문자로 구성되어 있어야합니다. `-` 문자를 사용하기 보다는 언더스코어 (`_`) 를 사용하십시오. 라우트 파라미터는 라우트 콜백 / 컨트롤러에 주입되는데 이때 사용되는 콜백 / 컨트롤러 인자에서 문제가 되지 않는 이름이어야 합니다.

<a name="parameters-optional-parameters"></a>
### Optional Parameters
### 선택적 파라미터

Occasionally you may need to specify a route parameter, but make the presence of that route parameter optional. You may do so by placing a `?` mark after the parameter name. Make sure to give the route's corresponding variable a default value:

때로는, 라우트 파라미터를 지정하긴 하지만, 파라미터가 선택적으로 존재하기를 원할수도 있습니다. 이 경우 파라미터 이름뒤에 `?` 를 표시하면 됩니다. 라우트 파라미터와 일치하는 변수가 기본값을 가지는지 확인하십시오.

    Route::get('user/{name?}', function ($name = null) {
        return $name;
    });

    Route::get('user/{name?}', function ($name = 'John') {
        return $name;
    });

<a name="parameters-regular-expression-constraints"></a>
### Regular Expression Constraints
### 정규표현식 제약

You may constrain the format of your route parameters using the `where` method on a route instance. The `where` method accepts the name of the parameter and a regular expression defining how the parameter should be constrained:

라우트 인스턴스에 `where` 메소드를 사용하여 라우트 파라미터들의 포맷을 제한할 수 있습니다. `where` 메소드는 파라미터의 이름과 파라미터가 어떻게 규정되어야 하는지 나타내는 정규표현식을 인자로 전달 받습니다.

    Route::get('user/{name}', function ($name) {
        //
    })->where('name', '[A-Za-z]+');

    Route::get('user/{id}', function ($id) {
        //
    })->where('id', '[0-9]+');

    Route::get('user/{id}/{name}', function ($id, $name) {
        //
    })->where(['id' => '[0-9]+', 'name' => '[a-z]+']);

<a name="parameters-global-constraints"></a>
#### Global Constraints
#### 글로벌 제약

If you would like a route parameter to always be constrained by a given regular expression, you may use the `pattern` method. You should define these patterns in the `boot` method of your `RouteServiceProvider`:

라우트 파라미터가 항상 주어진 정규표현식으로 제약을 가지게 된다면, `pattern` 메소드를 사용할 수 있습니다. 이 패턴들은 `RouteServiceProvider`의 `boot` 메소드 안에서 사용해야 합니다.

    /**
     * Define your route model bindings, pattern filters, etc.
     *
     * @return void
     */
    public function boot()
    {
        Route::pattern('id', '[0-9]+');

        parent::boot();
    }

Once the pattern has been defined, it is automatically applied to all routes using that parameter name:

패턴을 한번 정의하고나면, 해당 파라미터 이름을 사용하는 모든 라우트들에 자동으로 적용됩니다.

    Route::get('user/{id}', function ($id) {
        // Only executed if {id} is numeric...
    });

<a name="parameters-encoded-forward-slashes"></a>
#### Encoded Forward Slashes
#### 인코딩 된 슬래시


The Laravel routing component allows all characters except `/`. You must explicitly allow `/` to be part of your placeholder using a `where` condition regular expression:

Laravel 라우팅 구성 요소는 `/`를 제외한 모든 문자를 허용합니다. `where` 조건 정규식을 사용하여 `/`를 명시적으로 플레이스홀더의 일부로 허용해야합니다.


    Route::get('search/{search}', function ($search) {
        return $search;
    })->where('search', '.*');

> {note} Encoded forward slashes are only supported within the last route segment.

> {note} 인코딩 된 슬래시는 마지막 경로 세그먼트 내에서만 지원됩니다.

<a name="named-routes"></a>
## Named Routes
## 이름이 지정된 라우트

Named routes allow the convenient generation of URLs or redirects for specific routes. You may specify a name for a route by chaining the `name` method onto the route definition:

이름이 지정된 라우트는 URL의 생성이나 지정된 라우트로의 리다이렉션을 편리하게 해줍니다. 라우트 정의에 `name` 메소드를 체이닝 하여 라우트에 이름을 지정할 수 있습니다.

    Route::get('user/profile', function () {
        //
    })->name('profile');

You may also specify route names for controller actions:

컨트롤러의 액션에도 라우트 이름을 지정할 수 있습니다.

    Route::get('user/profile', [UserProfileController::class, 'show'])->name('profile');

> {note} Route names should always be unique.

> {note} 라우트 이름은 언제나 고유해야 합니다.

#### Generating URLs To Named Routes
#### 이름이 지정된 라우트들에 대한 URL 생성하기

Once you have assigned a name to a given route, you may use the route's name when generating URLs or redirects via the global `route` function:

주어진 라우트에 대한 이름이 할당되면, 전역 `route` 함수를 통해서 URL 또는 리다이렉션을 생성할 때 라우트 이름을 사용할 수 있습니다.

    // Generating URLs...
    $url = route('profile');

    // Generating Redirects...
    return redirect()->route('profile');

If the named route defines parameters, you may pass the parameters as the second argument to the `route` function. The given parameters will automatically be inserted into the URL in their correct positions:

이름이 지정된 라우트에 파라미터를 정의하였다면, `route` 메소드의 두번째 인자로 파라미터를 전달할 수 있습니다. 주어진 파라미터는 자동으로 올바른 위치에 있는 URL 에 삽입될 것입니다.

    Route::get('user/{id}/profile', function ($id) {
        //
    })->name('profile');

    $url = route('profile', ['id' => 1]);

If you pass additional parameters in the array, those key / value pairs will automatically be added to the generated URL's query string:

배열에 추가 파라메터를 전달하면 해당하는 키/값의 쌍이 생성된 URL의 쿼리 문자열에 자동으로 추가됩니다.

    Route::get('user/{id}/profile', function ($id) {
        //
    })->name('profile');

    $url = route('profile', ['id' => 1, 'photos' => 'yes']);

    // /user/1/profile?photos=yes

> {tip} Sometimes, you may wish to specify request-wide default values for URL parameters, such as the current locale. To accomplish this, you may use the [`URL::defaults` method](/docs/{{version}}/urls#default-values).

> {tip} 때때로, 현재 로케일과 같은 URL 파라메터에 대한 요청 전체 기본값을 지정할 수 있습니다. 이를 위해 [`URL::defaults` 메서드](/docs/{{version}}/urls#default-values)을 사용할 수 있습니다.

#### Inspecting The Current Route
#### 현재의 라우트 검사하기

If you would like to determine if the current request was routed to a given named route, you may use the `named` method on a Route instance. For example, you may check the current route name from a route middleware:

현재의 request 가 주어진 이름의 라우트가 맞는지 확인하고자 한다면, Route 인스턴스의 `named` 메소드를 사용하면 됩니다. 예를 들어 라우트 미들웨어에서 현재 라우트의 이름을 확인할 수 있습니다.

    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        if ($request->route()->named('profile')) {
            //
        }

        return $next($request);
    }

<a name="route-groups"></a>
## Route Groups
## 라우트 그룹

Route groups allow you to share route attributes, such as middleware, across a large number of routes without needing to define those attributes on each individual route. Shared attributes are specified in an array format as the first parameter to the `Route::group` method.

라우트 그룹을 사용하면 미들웨어와 같은 라우트 속성을 공유할 수 있어, 많은 수의 라우트를 등록할 때 각각의 개별 라우트에 매번 속성들을 정의하지 않아도 되게 해줍니다. 공유하려는 속성은 배열 형식으로 지정되어 `Route::group` 메소드의 첫번째 인자로 전달됩니다.

Nested groups attempt to intelligently "merge" attributes with their parent group. Middleware and `where` conditions are merged while names and prefixes are appended. Namespace delimiters and slashes in URI prefixes are automatically added where appropriate.

중첩 된 그룹은 속성을 상위 그룹과 지능적으로 "병합"합니다. 미들웨어와 이름 및 접두사가 추가되는 동안 `where` 조건이 병합됩니다. 적절한 경우 URI 접두사의 네임 스페이스 구분 기호와 슬래시가 자동으로 추가됩니다.

<a name="route-group-middleware"></a>
### Middleware
### 미들웨어

To assign middleware to all routes within a group, you may use the `middleware` method before defining the group. Middleware are executed in the order they are listed in the array:

그룹 안의 모든 라우트에 미들웨어를 할당하기 위해서는, 그룹을 정의하기 전에 `middleware` 메소드를 사용하면 됩니다. 미들웨어는 배열에 나열된 순서대로 실행될 것입니다.

    Route::middleware(['first', 'second'])->group(function () {
        Route::get('/', function () {
            // Uses first & second middleware...
        });

        Route::get('user/profile', function () {
            // Uses first & second middleware...
        });
    });

<a name="route-group-subdomain-routing"></a>
### Subdomain Routing
### 서브 도메인 라우팅

Route groups may also be used to handle subdomain routing. Subdomains may be assigned route parameters just like route URIs, allowing you to capture a portion of the subdomain for usage in your route or controller. The subdomain may be specified by calling the `domain` method before defining the group:

라우트 그룹은 또한 카드형태의 서브 도메인을 처리하는데 사용할 수도 있습니다. 서브 도메인은 라우트 URI와 같이 서브 도메인의 일부를 추출하여, 라우트 파라미터로 할당할 수 있습니다. 서브 도메인은 그룹을 정의하기 전에 `domain` 메소드를 호출하여 지정할 수 있습니다.

    Route::domain('{account}.myapp.com')->group(function () {
        Route::get('user/{id}', function ($account, $id) {
            //
        });
    });

> {note} In order to ensure your subdomain routes are reachable, you should register subdomain routes before registering root domain routes. This will prevent root domain routes from overwriting subdomain routes which have the same URI path.

> {note} 서브 도메인 라우트가 동작하도록하려면 루트 도메인 라우트를 등록하기 전에 서브 도메인 라우트를 등록해야합니다. 이렇게하면 루트 도메인 라우트가 동일한 URI 라우트를 가진 서브 도메인 라우트를 덮어 쓰지 않습니다.

<a name="route-group-prefixes"></a>
### Route Prefixes
### 라우트 Prefix

The `prefix` method may be used to prefix each route in the group with a given URI. For example, you may want to prefix all route URIs within the group with `admin`:

`prefix` 메소드는 그룹안의 라우트에 특정 URI을 접두어로 지정할 때 사용합니다. 그룹의 모든 라우트 URI 앞에 `admin` 을 붙이고 싶다면 다음과 같이 지정하면 됩니다.

    Route::prefix('admin')->group(function () {
        Route::get('users', function () {
            // Matches The "/admin/users" URL
        });
    });

<a name="route-group-name-prefixes"></a>
### Route Name Prefixes
### 라우트 이름 접두사

The `name` method may be used to prefix each route name in the group with a given string. For example, you may want to prefix all of the grouped route's names with `admin`. The given string is prefixed to the route name exactly as it is specified, so we will be sure to provide the trailing `.` character in the prefix:

`name` 메소드는 주어진 문자열을 가진 그룹 내의 각각의 라우트 이름에 접두사를 붙이는데 사용됩니다. 예를 들어, 그룹화 된 라우트들 전부에 `admin` 접두사를 붙일 수 있습니다. 전달된 문자열 그대로 라우트 이름 앞에 붙기 때문에, 접두사는 뒤에 `.` 문자를 포함하여 전달해야하는 것을 잊지말아야 합니다.

    Route::name('admin.')->group(function () {
        Route::get('users', function () {
            // Route assigned name "admin.users"...
        })->name('users');
    });

<a name="route-model-binding"></a>
## Route Model Binding
## 라우트 모델 바인딩

When injecting a model ID to a route or controller action, you will often query to retrieve the model that corresponds to that ID. Laravel route model binding provides a convenient way to automatically inject the model instances directly into your routes. For example, instead of injecting a user's ID, you can inject the entire `User` model instance that matches the given ID.

모델 ID 를 라우트나 컨트롤러 액션에 주입한 경우, 여러분은 곧잘 ID와 일치하는 모델을 조회하기 위해서 쿼리를 수행할 것입니다. 라라벨 라우트 모델 바인딩은 여러분의 라우트에 자동으로 모델 인스턴스를 직접 주입하는 편리한 방법을 제공합니다. 예를 들어 사용자의 ID를 주입하는 대신 주어진 ID와 매칭되는 전체 `User` 모델 인스턴스를 주입할 수 있습니다.

<a name="implicit-binding"></a>
### Implicit Binding
### 묵시적 바인딩

Laravel automatically resolves Eloquent models defined in routes or controller actions whose type-hinted variable names match a route segment name. For example:

라라벨은 자동으로 라우트나 컨트롤러 액션에서 정의되어 있는 라우트 세그먼트 이름과 일치하는 타입힌트된 변수에 해당하는 Eloquent 모델을 의존성 해결합니다. 예를 들어:

    Route::get('api/users/{user}', function (App\User $user) {
        return $user->email;
    });

Since the `$user` variable is type-hinted as the `App\User` Eloquent model and the variable name matches the `{user}` URI segment, Laravel will automatically inject the model instance that has an ID matching the corresponding value from the request URI. If a matching model instance is not found in the database, a 404 HTTP response will automatically be generated.

`App\User` Eloquent 모델로 타입힌트된 `$user` 변수와 `{user}` 세그먼트가 일치하기 때문에, 라라벨은 자동으로 request URI 로 부터 일치하는 ID 값을 가진 모델 인스턴스를 주입할것입니다. 만약 데이터베이스에서 매칭되는 모델 인스턴스를 찾을 수 없으면, 자동으로 404 HTTP response 가 생성됩니다.

#### Customizing The Key
#### 키 커스터마이징

Sometimes you may wish to resolve Eloquent models using a column other than `id`. To do so, you may specify the column in the route parameter definition:

엘로퀜트 모델들을 의존성을 `id` 키 대신 다른 컬럼을 사용하여 해결하기 원한다면, 라우트 파라미터 정의 안에 그 컬럼을 명시할 수 있습니다.

    Route::get('api/posts/{post:slug}', function (App\Post $post) {
        return $post;
    });

<a name="implicit-model-binding-scoping"></a>
#### Custom Keys & Scoping

Sometimes, when implicitly binding multiple Eloquent models in a single route definition, you may wish to scope the second Eloquent model such that it must be a child of the first Eloquent model. For example, consider this situation that retrieves a blog post by slug for a specific user:

하나의 라우트 명세에 다양한 엘로퀜트 모델을 묵시적으로 바인드 할때, 첫번째 엘로퀜트 모델의 자식이 되어야 하는 두번째 엘로퀜트 모델의 범위(Scope)를 바랄 수도 있다.  특정 유저를 위한 Slug 에 관련된 블로그 글를 찾는 다음의 예를 고려해보자.

    use App\Post;
    use App\User;

    Route::get('api/users/{user}/posts/{post:slug}', function (User $user, Post $post) {
        return $post;
    });

When using a custom keyed implicit binding as a nested route parameter, Laravel will automatically scope the query to retrieve the nested model by its parent using conventions to guess the relationship name on the parent. In this case, it will be assumed that the `User` model has a relationship named `posts` (the plural of the route parameter name) which can be used to retrieve the `Post` model.

사용자 정의 키로 묵시적 바인딩을 중첩된 라우트 매개 변수로 사용하는 경우, Laravel 은 부모의 관계 이름을 추측하는 규칙을 사용하여 부모에 의해 중첩된 모델을 검색하도록 쿼리의 범위를 자동으로 지정합니다. 이 경우, `User` 모델은 `post` 모델을 검색하는 데 사용할 수있는 `posts` (경로 매개 변수 이름의 복수형)라는 관계가 있다고 가정합니다.

#### Customizing The Default Key Name
#### 기본 키의 이름을 변경하기

If you would like model binding to use a default database column other than `id` when retrieving a given model class, you may override the `getRouteKeyName` method on the Eloquent model:

주어진 모델의 클래스를 찾을 때 `id` 와는 다른 기본 데이터베이스 컬럼을 사용하는 모델 바인딩을 하고자 한다면, Eloquent 모델의 `getRouteKeyName` 메소드를 재지정하면 됩니다.

    /**
     * Get the route key for the model.
     *
     * @return string
     */
    public function getRouteKeyName()
    {
        return 'slug';
    }

<a name="explicit-binding"></a>
### Explicit Binding
### 명시적 바인딩

To register an explicit binding, use the router's `model` method to specify the class for a given parameter. You should define your explicit model bindings at the beginning of the `boot` method of your `RouteServiceProvider` class:

명시적 바인딩을 등록하기 위해서, 주어진 파라미터에 대한 클래스를 지정하려면 라우터의 `model` 메소드를 사용하십시오. 여러분은 `RouteServiceProvider` 클래스의 `boot` 메소드의 시작부분에 명시적 모델 바인딩을 정의해야 합니다.

    /**
     * Define your route model bindings, pattern filters, etc.
     *
     * @return void
     */
    public function boot()
    {
        Route::model('user', App\Models\User::class);

        // ...
    }

Next, define a route that contains a `{user}` parameter:

다음으로, `{user}` 파라미터를 포함한 라우트를 정의합니다.

    Route::get('profile/{user}', function (App\User $user) {
        //
    });

Since we have bound all `{user}` parameters to the `App\User` model, a `User` instance will be injected into the route. So, for example, a request to `profile/1` will inject the `User` instance from the database which has an ID of `1`.

`{user}` 파라미터와 `App\User` 모델이 바인딩되어 있기 때문에 라우트에는 `User` 인스턴스가 주입 될것입니다. 예를 들어 `profile/1`으로 요청이 들어오면 데이터베이스로 부터 ID가 `1`인 `User`의 인스턴스가 주입됩니다.

If a matching model instance is not found in the database, a 404 HTTP response will be automatically generated.

만약 데이터베이스에서 일치하는 모델 인스턴스를 찾지 못하는 경우, 404 응답이 자동으로 생성될 것입니다.

#### Customizing The Resolution Logic
#### 의존성 해결 로직 커스터마이징하기

If you wish to use your own resolution logic, you may use the `Route::bind` method. The `Closure` you pass to the `bind` method will receive the value of the URI segment and should return the instance of the class that should be injected into the route:

만약 여러분의 고유한 의존성 해결 로직을 사용하려면 `Route::bind` 메소드를 사용할 수 있습니다. `bind` 메소드에 전달되는 `클로저`에는 URI 세그먼트에 해당하는 값이 전달되고 라우트에 주입되어야 하는 클래스의 인스턴스를 반환해야 합니다.

    /**
     * Define your route model bindings, pattern filters, etc.
     *
     * @return void
     */
    public function boot()
    {
        Route::bind('user', function ($value) {
            return App\Models\User::where('name', $value)->firstOrFail();
        });

        // ...
    }

Alternatively, you may override the `resolveRouteBinding` method on your Eloquent model. This method will receive the value of the URI segment and should return the instance of the class that should be injected into the route:

또는 Eloquent 모델에서 `resolveRouteBinding` 메소드를 오버라이드 할 수 있습니다. 이 메소드는 URI 세그먼트의 값을 받고 라우트에 삽입되어야하는 클래스의 인스턴스를 리턴해야 합니다.

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

<a name="fallback-routes"></a>
## Fallback Routes
## 대체 라우트

Using the `Route::fallback` method, you may define a route that will be executed when no other route matches the incoming request. Typically, unhandled requests will automatically render a "404" page via your application's exception handler. However, since you may define the `fallback` route within your `routes/web.php` file, all middleware in the `web` middleware group will apply to the route. You are free to add additional middleware to this route as needed:

`Route::fallback` 메소드를 사용하면 들어오는 요청과 일치하는 라우트가 없을 때 실행 할 라우트를 정의 할 수 있습니다. 일반적으로 처리하지 못한 요청은 애플리케이션의 exception 핸들러를 통해 자동으로 "404" 페이지를 렌더링합니다. 그러나 `routes/web.php` 파일에서 `fallback` 라우트를 정의 할 경우 `web` 미들웨어 그룹의 모든 미들웨어가 라우트에 적용됩니다. 필요할 경우 얼마든지 이 라우트에 미들웨어를 추가 할 수 있습니다.

    Route::fallback(function () {
        //
    });

> {note} The fallback route should always be the last route registered by your application.

> {note} 대체 라우트는 항상 애플리케이션에서 등록한 마지막 라우트 여야합니다.

<a name="rate-limiting"></a>
## Rate Limiting
## Rate 제한

Laravel includes a [middleware](/docs/{{version}}/middleware) to rate limit access to routes within your application. To get started, assign the `throttle` middleware to a route or a group of routes. The `throttle` middleware accepts two parameters that determine the maximum number of requests that can be made in a given number of minutes. For example, let's specify that an authenticated user may access the following group of routes 60 times per minute:

라라벨은 라우트 접속을 제한하는 [미들웨어](/docs/{{version}}/middleware) 를 포함하고 있습니다. 이를 시작하려면, `throttle` 미들웨어를 라우트나 라우트 그룹에 지정해야 합니다. `throttle` 미들웨어는 지정된 분 동안의 최대 리퀘스트 수를 정하는 2개의 파라미터를 받습니다. 예를 들어, 인증된 유저가 아래의 라우트 그룹에 1분 당 60번까지 접속을 제한할 수 있습니다.

    Route::middleware('auth:api', 'throttle:60,1')->group(function () {
        Route::get('/user', function () {
            //
        });
    });

#### Dynamic Rate Limiting
#### 동적인 Rate 제한

You may specify a dynamic request maximum based on an attribute of the authenticated `User` model. For example, if your `User` model contains a `rate_limit` attribute, you may pass the name of the attribute to the `throttle` middleware so that it is used to calculate the maximum request count:

인증된 `User` 모델의 attribute 를 기반으로 동적인 Request-요청의 최대치를 정할 수 있습니다. 예를 들어, `User` 모델이 `rate_limit` 라는 attribute 를 가지고 있다고 할 때, 최대 request-요청 수를 계산하기 위해  attribute 의 이름을 `throttle` 미들웨어에 전달 할 수 있습니다.

    Route::middleware('auth:api', 'throttle:rate_limit,1')->group(function () {
        Route::get('/user', function () {
            //
        });
    });

#### Distinct Guest & Authenticated User Rate Limits
#### 게스트 사용자와 인증 된 사용자의 다른 Rate 제한

You may specify different rate limits for guest and authenticated users. For example, you may specify a maximum of `10` requests per minute for guests `60` for authenticated users:

게스트 사용자와 인증된 사용자들을 위해 다른 rate 제한을 명시할 수 있습니다. 예를 들어, 게스트 사용자에게는 분당 최대 `10` 번의 요청, 그리고 인증된 사용자에겐 `60` 번으로 요청을 제한할 수 있습니다.

    Route::middleware('throttle:10|60,1')->group(function () {
        //
    });

You may also combine this functionality with dynamic rate limits. For example, if your `User` model contains a `rate_limit` attribute, you may pass the name of the attribute to the `throttle` middleware so that it is used to calculate the maximum request count for authenticated users:

또한 이 기능과 동적 rate 제한을 함께 사용할 수 있습니다. 예를 들어, `User` 모델이 `rate_limit` 속성을 포함하고 있다면, 인증 된 사용자의 최대 요청 수를 제한 하는 `throttle` 미들웨어에 속성의 이름을 전달해줄 수 있습니다.

    Route::middleware('auth:api', 'throttle:10|rate_limit,1')->group(function () {
        Route::get('/user', function () {
            //
        });
    });


#### Rate Limit Segments
#### 속도 제한 세그먼트

Typically, you will probably specify one rate limit for your entire API. However, your application may require different rate limits for different segments of your API. If this is the case, you will need to pass a segment name as the third argument to the `throttle` middleware:

일반적으로 전체 API에 대해 하나의 비율 제한을 지정합니다. 그러나 애플리케이션에 따라 API의 각 세그먼트마다 다른 속도 제한이 필요할 수 있습니다. 이 경우, `throttle` 미들웨어에 세 번째 인수로 세그먼트 이름을 전달해야합니다.

    Route::middleware('auth:api')->group(function () {
        Route::middleware('throttle:60,1,default')->group(function () {
            Route::get('/servers', function () {
                //
            });
        });

        Route::middleware('throttle:60,1,deletes')->group(function () {
            Route::delete('/servers/{id}', function () {
                //
            });
        });
    });

<a name="form-method-spoofing"></a>
## Form Method Spoofing
## Form-폼 메소드 Spoofing-스푸핑

HTML forms do not support `PUT`, `PATCH` or `DELETE` actions. So, when defining `PUT`, `PATCH` or `DELETE` routes that are called from an HTML form, you will need to add a hidden `_method` field to the form. The value sent with the `_method` field will be used as the HTTP request method:

HTML form은 `PUT`, `PATCH` 와 `DELETE` 액션을 지원하지 않습니다. 따라서 `PUT`, `PATCH` 나 `DELETE` 로 지정된 라우트를 호출하는 HTML form을 정의한다면 `_method` 의 숨겨진 필드를 지정해야합니다. `_method` 필드로 보내진 값은 HTTP request 메소드를 판별하는데 사용됩니다.

    <form action="/foo/bar" method="POST">
        <input type="hidden" name="_method" value="PUT">
        <input type="hidden" name="_token" value="{{ csrf_token() }}">
    </form>

You may use the `@method` Blade directive to generate the `_method` input:

`_method` 입력을 생성하기 위해서 `@method` 블레이드 지시어를 사용할 수 있습니다.

    <form action="/foo/bar" method="POST">
        @method('PUT')
        @csrf
    </form>

<a name="accessing-the-current-route"></a>
## Accessing The Current Route
## 현재 라우트에 엑세스하기

You may use the `current`, `currentRouteName`, and `currentRouteAction` methods on the `Route` facade to access information about the route handling the incoming request:

`Route` 파사드의 `current`, `currentRouteName` 그리고 `currentRouteAction` 메소드를 사용하여, 유입된 request-요청을 처리하는 라우트에 대한 정보에 엑세스 할 수 있습니다.

    $route = Route::current();

    $name = Route::currentRouteName();

    $action = Route::currentRouteAction();

Refer to the API documentation for both the [underlying class of the Route facade](https://laravel.com/api/{{version}}/Illuminate/Routing/Router.html) and [Route instance](https://laravel.com/api/{{version}}/Illuminate/Routing/Route.html) to review all accessible methods.

모든 메소드를 확인하고자 한다면 [Route 파사드 뒤에서 동작하는 클래스](https://laravel.com/api/{{version}}/Illuminate/Routing/Router.html) 와 [Route 인스턴스](https://laravel.com/api/{{version}}/Illuminate/Routing/Route.html) API 문서를 참고하십시오.

<a name="cors"></a>
## Cross-Origin Resource Sharing (CORS)
## 크로스-오리진 리소스 쉐어링 (CORS)

Laravel can automatically respond to CORS OPTIONS requests with values that you configure. All CORS settings may be configured in your `cors` configuration file and OPTIONS requests will automatically be handled by the `HandleCors` middleware that is included by default in your global middleware stack.

Laravel 은 사용자가 구성한 값으로 CORS OPTIONS 요청에 자동으로 응답 할 수 있습니다. 모든 CORS 설정은 `cors` 구성 파일에서 구성 될 수 있으며 OPTIONS 요청은 전역 미들웨어 스택에 기본적으로 포함 된 `HandleCors '미들웨어에 의해 자동으로 처리됩니다.

> {tip} For more information on CORS and CORS headers, please consult the [MDN web documentation on CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS#The_HTTP_response_headers).

> {tip} CORS 및 CORS 헤더에 대한 자세한 내용은 [MDN web documentation on CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS#The_HTTP_response_headers) 를 참조하십시오.
