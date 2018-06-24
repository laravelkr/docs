# HTTP Routing HTTP 라우팅

- [Basic Routing 기본적인 라우팅](#basic-routing)
- [CSRF Protection CSRF 보호하기](#csrf-protection)
- [Method Spoofing 메소드 속이기-Spoofing](#method-spoofing)
- [Route Parameters 라우트 파라미터](#route-parameters)
- [Named Routes 이름이 지정된 라우트](#named-routes)
- [Route Groups 라우트 그룹](#route-groups)
- [Route Model Binding 라우트 모델 바인딩](#route-model-binding)
- [Throwing 404 Errors 404 에러 처리](#throwing-404-errors)

<a name="basic-routing"></a>
## Basic Routing 기본적인 라우팅

You will define most of the routes for your application in the `app/Http/routes.php` file, which is loaded by the `App\Providers\RouteServiceProvider` class. 애플리케이션에서 사용하는 대부분의 라우트는 `app/Http/routes.php` 파일안에 정의합니다. 이 파일은 `App\Providers\RouteServiceProvider` 클래스에 의해서 로딩됩니다. The most basic Laravel routes simply accept a URI and a `Closure`: 가장 기본적인 라라벨의 라우트는 URI와 `Closure` 하나로 지정됩니다. 

#### Basic GET Route 기본적인 GET 라우트

	Route::get('/', function()
	{
		return 'Hello World';
	});

#### Other Basic Routes 기타 기본 라우트

	Route::post('foo/bar', function()
	{
		return 'Hello World';
	});

	Route::put('foo/bar', function()
	{
		//
	});

	Route::delete('foo/bar', function()
	{
		//
	});

#### Registering A Route For Multiple Verbs 
#### 여러 HTTP 메소드에 라우트 등록하기

	Route::match(['get', 'post'], '/', function()
	{
		return 'Hello World';
	});

#### Registering A Route That Responds To Any HTTP Verb
#### 어떠한 HTTP 메소드에도 응답하는 라우트 등록하기

	Route::any('foo', function()
	{
		return 'Hello World';
	});

Often, you will need to generate URLs to your routes, you may do so using the `url` helper: 라우트를 위한 URL을 생성할 필요가 많은데 이때는 `url` 헬퍼 함수를 사용하면 됩니다. 

	$url = url('foo');

<a name="csrf-protection"></a>
## CSRF Protection 
## CSRF 보호하기

Laravel makes it easy to protect your application from [cross-site request forgeries](http://en.wikipedia.org/wiki/Cross-site_request_forgery). 라라벨은 크로스 사이트 요청 위조 [cross-site request forgeries](http://en.wikipedia.org/wiki/Cross-site_request_forgery)에서 응용 프로그램을 쉽게 지킬 수 있도록 합니다. Cross-site request forgeries are a type of malicious exploit whereby unauthorized commands are performed on behalf of the authenticated user. 크로스 사이트 요청 위조는 악의적인 공격의 하나이며 인증받은 사용자를 대신하여 허가 받지 않은 명령을 수행합니다. 

Laravel automatically generates a CSRF "token" for each active user session managed by the application. 라라벨은 애플리케이션에 의해서 관리되고 있는 각각의 사용자별 CSRF "토큰"을 자동으로 생성합니다. This token is used to verify that the authenticated user is the one actually making the requests to the application. 이 토큰은 인증된 사용자가 실제로 애플리케이션에 요청을 보내고 있는지 식별하는데 사용됩니다. 

#### Insert The CSRF Token Into A Form 
#### Form 에 CSRF 토큰 삽입하기

    <input type="hidden" name="_token" value="<?php echo csrf_token(); ?>">

Of course, using the Blade [templating engine](/docs/{{version}}/templates):
다음처럼 Blade [템플릿 엔진](/docs/{{version}}/templates)을 사용할 수 있습니다.

	<input type="hidden" name="_token" value="{{ csrf_token() }}">

You do not need to manually verify the CSRF token on POST, PUT, or DELETE requests. 일일이 수동으로 POST, PUT 또는 DELETE 요청에 대한 CSRF 토큰을 확인할 필요가 없습니다. The `VerifyCsrfToken` [HTTP middleware](/docs/{{version}}/middleware) will verify token in the request input matches the token stored in the session. `VerifyCsrfToken` [HTTP 미들웨어](/docs/{{version}}/middleware)가 요청중인 토큰을 세션에 저장되어 있는 토큰과 일치하는지 확인할 것입니다. 

#### X-CSRF-TOKEN
#### X-CSRF-TOKEN

In addition to looking for the CSRF token as a "POST" parameter, the middleware will also check for the `X-CSRF-TOKEN` request header. 덧붙여 미들웨어는 "POST" 파라미터로 CSRF 토큰을 찾기 위해서 `X-CSRF-TOKEN` 요청 헤더(request header)도 확인합니다. You could, for example, store the token in a "meta" tag and instruct jQuery to add it to all request headers: 사용자는 예를 들어, "메타" 태그에 토큰을 저장하고 모든 요청 헤더(request header)에 추가하도록 jQuery를 설정 할 수 있습니다. 


	<meta name="csrf-token" content="{{ csrf_token() }}" />

	$.ajaxSetup({
            headers: {
                'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
            }
        });

Now all AJAX requests will automatically include the CSRF token: 이제 모든 AJAX 요청은 자동으로 CSRF 토큰을 포함하게 됩니다. 

	$.ajax({
	   url: "/foo/bar",
	})

#### X-XSRF-TOKEN
#### X-XSRF-TOKEN

Laravel also stores the CSRF token in a `XSRF-TOKEN` cookie. 또한 라라벨은 CSRF 토큰을 `XSRF-TOKEN` 쿠키에 저장합니다. You can use the cookie value to set the `X-XSRF-TOKEN` request header. 이 쿠키값을 요청 헤더(request header)에 `X-XSRF-TOKEN`을 설정하는데 사용할 수 있습니다. Some JavaScript frameworks, like Angular, do this automatically for you. Angular와 같은 몇몇 자바스크립트 프레임워크는 자동으로 이 값을 사용합니다. 

> Note 참고: The difference between the `X-CSRF-TOKEN` and `X-XSRF-TOKEN` is that the first uses a plain text value and the latter uses an encrypted value, because cookies in Laravel are always encrypted. `X-CSRF-TOKEN`와 `X-XSRF-TOKEN`의 차이점은 전자는 일반적인 텍스트를 사용한다면 후자는 암호화된 값을 사용한다는 것인데, 이는 라라벨에서는 쿠키를 항상 암호화 된 값으로 사용하기 때문입니다. If you use the `csrf_token()` function to supply the token value, you probably want to use the `X-CSRF-TOKEN` header. 여러분이 토큰 값을 제공하기 위해`csrf_token ()`함수를 사용하는 경우는, 아마 `X-CSRF-TOKEN` 헤더를 사용하게 되는 경우일것입니다. 

<a name="method-spoofing"></a>
## Method Spoofing
## 메소드 Spoofing-속이기

HTML forms do not support `PUT`, `PATCH` or `DELETE` actions. HTML form은 `PUT`, `PATCH` 와 `DELETE` 액션을 지원하지 않습니다. So, when defining `PUT`, `PATCH` or `DELETE` routes that are called from an HTML form, you will need to add a hidden `_method` field to the form. 따라서 `PUT`, `PATCH` 이나 `DELETE` 로 지정된 라우트를 호출하는 HTML form을 정의한다면 `_method` 의 숨겨진 필드를 지정해야합니다. 

The value sent with the `_method` field will be used as the HTTP request method. For example: `_method` 필드로 보내진 값은 HTTP 요청 메소드를 구분하는데 사용됩니다. 다음 예를 참조하십시오.

	<form action="/foo/bar" method="POST">
		<input type="hidden" name="_method" value="PUT">
    	<input type="hidden" name="_token" value="<?php echo csrf_token(); ?>">
    </form>

<a name="route-parameters"></a>
## Route Parameters
## 라우트 파라미터

Of course, you can capture segments of the request URI within your route:
당연하게도 라우트에서 요청된 URI 세그먼트를 얻을 수 있습니다.

#### Basic Route Parameter 
#### 기본적인 라우트 파라미터

	Route::get('user/{id}', function($id)
	{
		return 'User '.$id;
	});

> **Note:** Route parameters cannot contain the `-` character. Use an underscore (`_`) instead.
> **주의:** 라우트 파라미터는 `-` 문자를 포함하면 안됩니다. (`_`)를 사용하십시오. 

#### Optional Route Parameters 
#### 선택적인 라우트 파라미터

	Route::get('user/{name?}', function($name = null)
	{
		return $name;
	});

#### Optional Route Parameters With Default Value 
#### 기본값을 가진 선택적인 라우트 파라미터

	Route::get('user/{name?}', function($name = 'John')
	{
		return $name;
	});

#### Regular Expression Parameter Constraints
#### 정규표현식로 파라미터 제약하기

	Route::get('user/{name}', function($name)
	{
		//
	})
	->where('name', '[A-Za-z]+');

	Route::get('user/{id}', function($id)
	{
		//
	})
	->where('id', '[0-9]+');

#### Passing An Array Of Constraints
#### 파리미터 제약을 배열로 전달하기

	Route::get('user/{id}/{name}', function($id, $name)
	{
		//
	})
	->where(['id' => '[0-9]+', 'name' => '[a-z]+']);

#### Defining Global Patterns
#### 글로벌 패턴 지정하기

If you would like a route parameter to always be constrained by a given regular expression, you may use the `pattern` method. `pattern` 메소드를 사용하면 라우트 파리미터 변수를 지정한 정규 표현식에서 항상 제약을 할 수 있습니다. You should define these patterns in the `boot` method of your `RouteServiceProvider`: 이들은 `RouteServiceProvider` 의 `before` 메소드로 정의하는 것을 권장합니다.

	$router->pattern('id', '[0-9]+');

Once the pattern has been defined, it is applied to all routes using that parameter:
한번 패턴이 지정되고 나면 모든 라우트에서 사용하는 파라미터에 대해서 적용됩니다. 

	Route::get('user/{id}', function($id)
	{
		// Only called if {id} is numeric.
	});

#### Accessing A Route Parameter Value
#### 라우트 파라미터 값에 엑세스하기

If you need to access a route parameter value outside of a route, use the `input` method: 라우트 밖에서 라우트 파라미터 값에 엑세스할 필요가 있는 경우 `input` 메소드를 사용합니다. 

	if ($route->input('id') == 1)
	{
		//
	}

You may also access the current route parameters via the `Illuminate\Http\Request` instance. 또한 `Illuminate\Http\Request` 인스턴스를 통해서 현재의 라우트 파라미터에 엑세스 할 수 있습니다. The request instance for the current request may be accessed via the `Request` facade, or by type-hinting the `Illuminate\Http\Request` where dependencies are injected: 현재 요청에 대한 인스턴스는 `Illuminate\Http\Request` 타입힌트를 하거나, `Request` 파사드를 사용하면 의존성 주입을 통해서 엑세스 할 수 있습니다. 

	use Illuminate\Http\Request;

	Route::get('user/{id}', function(Request $request, $id)
	{
		if ($request->route('id'))
		{
			//
		}
	});

<a name="named-routes"></a>
## Named Routes 이름이 지정된 라우트

Named routes allow you to conveniently generate URLs or redirects for a specific route. 이름이 지정된 라우트는 지정된 라우트에 대한 URL을 생성하거나 Redirect를 할 때 편리함을 제공합니다. You may specify a name for a route with the `as` array key: `as` 배열 키를 통해 라우트에 이름을 지정할 수 있습니다. 

	Route::get('user/profile', ['as' => 'profile', function()
	{
		//
	}]);

You may also specify route names for controller actions:
컨트롤러 액션에 대해서도 라우트 이름을 지정할 수 있습니다. 

	Route::get('user/profile', [
        'as' => 'profile', 'uses' => 'UserController@showProfile'
	]);

Now, you may use the route's name when generating URLs or redirects:
이제 URL을 생성하거나 Redirect를 하는데 라우트 이름을 사용할 수 있습니다. 

	$url = route('profile');

	$redirect = redirect()->route('profile');

The `currentRouteName` method returns the name of the route handling the current request: `currentRouteName` 메소드는 현재의 요청에 대한 라우트 이름을 반환합니다. 

	$name = Route::currentRouteName();

<a name="route-groups"></a>
## Route Groups
## 라우트 그룹

Sometimes many of your routes will share common requirements such as URL segments, middleware, namespaces, etc. 때때로 많은 라우트들이 URL 세그먼트, 미들웨어, 네임스페이스 등과 같은 공통의 요구사항을 공유하고자 하는 경우가 있습니다. Instead of specifying each of these options on every route individually, you may use a route group to apply attributes to many routes. 이러한 옵션들을 모든 라우트에 개별로 각각 지정하는 대신에 라우트 그룹을 통해서 다수의 라우트에 속성을 지정할 수가 있습니다. 

Shared attributes are specified in an array format as the first parameter to the `Route::group` method. 속성값들을 공유하는 것은 `Route::group` 메소드의 첫번째 인자로 배열을 지정하면 됩니다. 

<a name="route-group-middleware"></a>
### Middleware
### 미들웨어

Middleware are applied to all routes within the group by defining the list of middleware with the `middleware` parameter on the group attribute array. 라우트 그룹에 지정하는 배열의 `middleware` 값에 미들웨어들의 목록을 정의함으로써 그룹내의 모든 라우트에 미들웨어가 적용됩니다. Middleware will be executed in the order you define this array: 미들웨어는 배열에 정의된 순서대로 실행될것입니다. 

	Route::group(['middleware' => ['foo','bar']], function()
	{
		Route::get('/', function()
		{
			// Has Foo And Bar Middleware
		});

		Route::get('user/profile', function()
		{
			// Has Foo And Bar Middleware
		});
	});

<a name="route-group-namespace"></a>
### Namespaces
### 네임스페이스

You may use the `namespace` parameter in your group attribute array to specify the namespace for all controllers within the group: 그룹의 속성 배열에 `namespace` 파라미터를 사용하여 가룹의 모든 컨트롤러에 네임스페이스를 지정할 수 있습니다. 

	Route::group(['namespace' => 'Admin'], function()
	{
		// Controllers Within The "App\Http\Controllers\Admin" Namespace

		Route::group(['namespace' => 'User'], function()
		{
			// Controllers Within The "App\Http\Controllers\Admin\User" Namespace
		});
	});

> **Note 참고:** By default, the `RouteServiceProvider` includes your `routes.php` file within a namespace group, allowing you to register controller routes without specifying the full `App\Http\Controllers` namespace prefix. 기본적으로 `RouteServiceProvider` 에서 포함하고 있는 `routes.php` 파일에는 라우트 컨트롤들을 위해서 네임스페이스가 지정되어 있습니다. 따라서 `App\Http\Controllers`의 전체 네임스페이스를 따로 지정할 필요는 없습니다. 

<a name="sub-domain-routing"></a>
### Sub-Domain Routing
#### 서브 도메인 라우팅

Laravel routes also handle wildcard sub-domains, and will pass your wildcard parameters from the domain: 라라벨 라우트에서는 와일드 파라미터 형태의 도메인 값을 설정하여 서브 도메인을 처리할 수 있습니다. 

#### Registering Sub-Domain Routes
#### 서브 도메인 라우트 등록하기 

	Route::group(['domain' => '{account}.myapp.com'], function()
	{

		Route::get('user/{id}', function($account, $id)
		{
			//
		});

	});

<a name="route-prefixing"></a>
### Route Prefixing
### 라우트 접두어 지정하기

A group of routes may be prefixed by using the `prefix` option in the attributes array of a group: 라우트 그룹의 접두어는 그룹의 속성 배열에 `prefix` 옵션을 사용하여 지정합니다.

	Route::group(['prefix' => 'admin'], function()
	{
		Route::get('users', function()
		{
			// Matches The "/admin/users" URL
		});
	});

You can also utilize the `prefix` parameter to pass common parameters to your routes: 또한 `prefix` 파라미터를 라우트들의 공통 파라미터로 지정할 수 있습니다.

#### Registering a URL parameter in a route prefix
#### 라우트 prefix 안에서 URL 파라미터 등록하기

	Route::group(['prefix' => 'accounts/{account_id}'], function()
	{
		Route::get('detail', function($account_id)
		{
			//
		});
	});

You can even define parameter constraints for the named parameters in your prefix: 또한 지정된 파라미터 변수의 제약 사항을 정의할 수도 있습니다. 

	Route::group([
		'prefix' => 'accounts/{account_id}',
		'where' => ['account_id' => '[0-9]+'],
	], function() {

		// Define Routes Here
	});

<a name="route-model-binding"></a>
## Route Model Binding
## 라우트 모델 바인딩

Laravel model binding provides a convenient way to inject class instances into your routes. 라라벨의 모델 바인딩은 라우트에 클래스 인스턴스를 주입할 수 있는 편리한 방법을 제공합니다. For example, instead of injecting a user's ID, you can inject the entire User class instance that matches the given ID. 예를 들어 사용자의 ID를 넘기는 대신에 주어진 ID에 해당하는 User 클래스 인스턴스를 주입할 수 있습니다. 

First, use the router's `model` method to specify the class for a given parameter. method: 먼저 주어진 파라미터에 대한 클래스를 지정하기 위해서 라우트의 `model` 메소드를 사용하여야 합니다. You should define your model bindings in the `RouteServiceProvider::boot` 이 모델 바인딩은 `RouteServiceProvider::boot` 안에서 정의되어야 합니다. 

#### Binding A Parameter To A Model
#### 모델과 파라미터 바인딩하기

	public function boot(Router $router)
	{
		parent::boot($router);

		$router->model('user', 'App\User');
	}

Next, define a route that contains a `{user}` parameter:
다음으로, `{user}` 파라미터를 포함한 라우트를 정의합니다. 

	Route::get('profile/{user}', function(App\User $user)
	{
		//
	});

Since we have bound the `{user}` parameter to the `App\User` model, a `User` instance will be injected into the route. `{user}` 파라미터와 `App\User` 모델이 바인딩되어 있기 때문에 라우트에는 `User` 인스턴스가 주입 될것입니다. So, for example, a request to `profile/1` will inject the `User` instance which has an ID of 1. 예를 들어 `profile/`으로 요청이 들어오면 ID가 1인 `User`의 인스턴스가 주입됩니다. 

> **Note 주의:** If a matching model instance is not found in the database, a 404 error will be thrown. 만약 데이터베이스에서 일치하는 모델 인스턴스를 찾이 못하는 경우 404 에러가 발생합니다. 

If you wish to specify your own "not found" behavior, pass a Closure as the third argument to the `model` method: 만약 "찾지 못함"의 동작을 지정하고 싶다면 세번째 인자로 클로저를 전달하면 됩니다. 

	Route::model('user', 'User', function()
	{
		throw new NotFoundHttpException;
	});

If you wish to use your own resolution logic, you should use the `Route::bind` method. 만약 고유한 의존성 검색 로직을 사용하려면 `Route::bind` 메소드를 사용해야 합니다. The Closure you pass to the `bind` method will receive the value of the URI segment, and should return an instance of the class you want to be injected into the route: `bind` 메소드에 전달되는 클로저에는 URI 세그먼트에 해당하는 값이 전달되고 라우트에 주입할 클래스의 인스턴스를 반환해야 합니다. 

	Route::bind('user', function($value)
	{
		return User::where('name', $value)->first();
	});

<a name="throwing-404-errors"></a>
## Throwing 404 Errors
## 404 에러 발생시키기

There are two ways to manually trigger a 404 error from a route. First, you may use the `abort` helper: 라우트에서 404 에러를 발생시키는 방법은 2가지가 있습니다. 첫번째로 `abort` 헬퍼 함수를 사용하는 것입니다. 

	abort(404);

The `abort` helper simply throws a `Symfony\Component\HttpKernel\Exception\HttpException` with the specified status code. `abort` 헬퍼함수는 지정된 상태 코드와 함께 `Symfony\Component\HttpKernel\Exception\HttpException` 를 던집니다.

Secondly, you may manually throw an instance of `Symfony\Component\HttpKernel\Exception\NotFoundHttpException`. 두번째로 직접으로 `Symfony\Component\HttpKernel\Exception\NotFoundHttpException`을 던지는 것입니다. 

More information on handling 404 exceptions and using custom responses for these errors may be found in the [errors](/docs/{{version}}/errors#http-exceptions) section of the documentation. 404 예외 처리와 오류의 커스텀한 처리를 사용하는 보다 자세한 내용은 다음 문서의 [errors](/docs/{{version}}/errors#http-exceptions) 부분을 참조하십시오.
