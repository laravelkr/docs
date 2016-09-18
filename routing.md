# HTTP 라우팅(HTTP Routing)

- [기본적인 라우팅](#basic-routing)
- [CSRF 보호하기](#csrf-protection)
- [메소드 속이기-Spoofing](#method-spoofing)
- [라우트 파라미터](#route-parameters)
- [이름이 지정된 라우트](#named-routes)
- [라우트 그룹](#route-groups)
- [라우트 모델 바인딩](#route-model-binding)
- [404 에러 처리](#throwing-404-errors)

<a name="basic-routing"></a>
## 기본적인 라우팅

어플리케이션에서 사용하는 대부분의 라우트는 `app/Http/routes.php` 파일안에 정의합니다. 이 파일은 `App\Providers\RouteServiceProvider` 클래스에 의해서 로딩됩니다. 가장 기본적인 라라벨의 라우트는 URI와 `Closure` 하나로 지정됩니다:

#### 기본적인 GET 라우트

	Route::get('/', function()
	{
		return 'Hello World';
	});

#### 기타 기본 라우트

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

#### 여러 HTTP 메소드에 라우트 등록하기

	Route::match(['get', 'post'], '/', function()
	{
		return 'Hello World';
	});

#### 어떠한 HTTP 메소드에도 응답하는 라우트 등록하기

	Route::any('foo', function()
	{
		return 'Hello World';
	});

라우트를 위한 URL을 생성할 필요가 많은데 이때는 `url` 헬퍼함수를 사용하면 됩니다:

	$url = url('foo');

<!--chak-comment-HTTP-라우팅(HTTP-Routing)-기본적인-라우팅-->

<a name="csrf-protection"></a>
## CSRF 보호하기

라라벨에서는 크로스 사이트 요청 위조 [cross-site request forgeries](http://en.wikipedia.org/wiki/Cross-site_request_forgery)으로부터 응용 프로그램을 쉽게 보호할 수 있습니다. 크로스 사이트 요청 위조는 악의적인 공격의 하나이며 인증받은 사용자를 대신하여 허가 받지 않은 명령을 수행합니다.

라라벨은 어플리케이션에 의해서 관리되고 있는 각각의 사용자별 CSRF "토큰"을 자동으로 생성합니다. 이 토큰은 인증된 사용자가 실제로 어플리케이션에 요청을 보내고 있는지 식별하는 데 사용됩니다.

#### Form에 CSRF 토큰 삽입하기

    <input type="hidden" name="_token" value="<?php echo csrf_token(); ?>">

다음처럼 Blade [템플릿 엔진](/docs/5.0/templates)을 사용할 수 있습니다.

	<input type="hidden" name="_token" value="{{ csrf_token() }}">

일일이 수동으로 POST, PUT 또는 DELETE 요청에 대한 CSRF 토큰을 확인할 필요가 없습니다. `VerifyCsrfToken` [HTTP 미들웨어](/docs/5.0/middleware)가 요청중인 토큰을 세션에 저장되어 있는 토큰과 일치하는지 확인할 것입니다.

#### X-CSRF-TOKEN

덧붙여 미들웨어는 "POST" 파라미터로 CSRF 토큰을 찾기 위해서 `X-CSRF-TOKEN` 요청 헤더(request header)도 확인합니다. 사용자는 예를 들어, "메타" 태그에 토큰을 저장하고 모든 요청 헤더(request header)에 추가하도록 jQuery를 설정 할 수 있습니다.


	<meta name="csrf-token" content="{{ csrf_token() }}" />

	$.ajaxSetup({
            headers: {
                'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
            }
        });

이제 모든 AJAX 요청은 자동으로 CSRF 토큰을 포함하게 됩니다.

	$.ajax({
	   url: "/foo/bar",
	})

#### X-XSRF-TOKEN

또한, 라라벨은 CSRF 토큰을 `XSRF-TOKEN` 쿠키에 저장합니다. 이 쿠키값을 요청 헤더(request header)에 `X-XSRF-TOKEN`을 설정하는 데 사용할 수 있습니다. Angular와 같은 몇몇 자바스크립트 프레임워크는 자동으로 이 값을 사용합니다.

> 참고: `X-CSRF-TOKEN`와 `X-XSRF-TOKEN`의 차이점은 전자는 일반적인 텍스트를 사용한다면 후자는 암호화된 값을 사용한다는 것인데, 이는 라라벨에서는 쿠키를 항상 암호화 된 값으로 사용하기 때문입니다. 여러분이 토큰 값을 제공하기 위해`csrf_token ()`함수를 사용하는 경우는, 아마 `X-CSRF-TOKEN` 헤더를 사용하게 되는 경우일것입니다.

<!--chak-comment-HTTP-라우팅(HTTP-Routing)-CSRF-보호하기-->

<a name="method-spoofing"></a>
## 메소드 Spoofing-속이기

HTML form은 실제로 `PUT`, `PATCH`와 `DELETE` 액션을 지원하지 않습니다. 따라서 `PUT`, `PATCH` 이나 `DELETE`로 지정된 라우트를 호출하는 HTML form을 정의한다면 `_method`의 숨겨진 필드를 지정해야 합니다.

`_method` 필드로 보내진 값은 HTTP 요청 메소드를 구분하는 데 사용됩니다. 다음 예를 참조하십시오:

	<form action="/foo/bar" method="POST">
		<input type="hidden" name="_method" value="PUT">
    	<input type="hidden" name="_token" value="<?php echo csrf_token(); ?>">
    </form>

<!--chak-comment-HTTP-라우팅(HTTP-Routing)-메소드-Spoofing-속이기-->

<a name="route-parameters"></a>
## 라우트 파라미터

당연하게도 라우트에서 요청된 URI 세그먼트를 얻을 수 있습니다:

#### 기본적인 라우트 파라미터

	Route::get('user/{id}', function($id)
	{
		return 'User '.$id;
	});

> **주의:** 라우트 파라미터는 `-` 문자를 포함하면 안됩니다. (`_`)를 사용하십시오.

#### 선택적인 라우트 파라미터

	Route::get('user/{name?}', function($name = null)
	{
		return $name;
	});

#### 기본값을 가진 선택적인 라우트 파라미터

	Route::get('user/{name?}', function($name = 'John')
	{
		return $name;
	});

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

#### 파리미터 제약을 배열로 전달하기

	Route::get('user/{id}/{name}', function($id, $name)
	{
		//
	})
	->where(['id' => '[0-9]+', 'name' => '[a-z]+']);

#### 글로벌 패턴 지정하기

`pattern` 메소드를 사용하면 라우트 파리미터 변수를 지정한 정규 표현식에서 항상 제약을 할 수 있습니다. 이들은 `RouteServiceProvider`의 `before` 메소드로 정의하는 것을 권장합니다:

	$router->pattern('id', '[0-9]+');

한번 패턴이 지정되고 나면 모든 라우트에서 사용하는 파라미터에 대해서 적용됩니다:

	Route::get('user/{id}', function($id)
	{
		// Only called if {id} is numeric.
	});

#### 라우트 파라미터 값에 엑세스하기

라우트 밖에서 라우트 파라미터 값에 엑세스할 필요가 있는 경우 `input` 메소드를 사용합니다:

	if ($route->input('id') == 1)
	{
		//
	}

또한, `Illuminate\Http\Request` 인스턴스를 통해서 현재의 라우트 파라미터에 엑세스 할 수 있습니다. 현재 요청에 대한 인스턴스는 `Illuminate\Http\Request` 타입힌트를 하거나, `Request` 파사드를 사용하면 의존성 주입을 통해서 엑세스 할 수 있습니다:

	use Illuminate\Http\Request;

	Route::get('user/{id}', function(Request $request, $id)
	{
		if ($request->route('id'))
		{
			//
		}
	});

<!--chak-comment-HTTP-라우팅(HTTP-Routing)-라우트-파라미터-->

<a name="named-routes"></a>
## 이름이 지정된 라우트

이름이 지정된 라우트는 지정된 라우트에 대한 URL을 생성하거나 Redirect를 할 때 편리함을 제공합니다. `as` 배열 키를 통해 라우트에 이름을 지정할 수 있습니다.

	Route::get('user/profile', ['as' => 'profile', function()
	{
		//
	}]);

컨트롤러 액션에 대해서도 라우트 이름을 지정할 수 있습니다.

	Route::get('user/profile', [
        'as' => 'profile', 'uses' => 'UserController@showProfile'
	]);

이제 URL을 생성하거나 Redirect를 하는 데 라우트 이름을 사용할 수 있습니다.

	$url = route('profile');

	$redirect = redirect()->route('profile');

`currentRouteName` 메소드는 현재의 요청에 대한 라우트 이름을 반환합니다.

	$name = Route::currentRouteName();

<!--chak-comment-HTTP-라우팅(HTTP-Routing)-이름이-지정된-라우트-->

<a name="route-groups"></a>
## Route Groups
## 라우트 그룹

때때로 많은 라우트들이 URL 세그먼트, 미들웨어, 네임스페이스 등과 같은 공통의 요구사항을 공유하고자 하는 경우가 있습니다. 이러한 옵션들을 모든 라우트에 개별로 각각 지정하는 대신에 라우트 그룹을 통해서 다수의 라우트에 속성을 지정할 수가 있습니다.

속성값들을 공유하는 것은 `Route::group` 메소드의 첫 번째 인자로 배열을 지정하면 됩니다.

<a name="route-group-middleware"></a>
### 미들웨어

라우트 그룹에 지정하는 배열의 `middleware` 값에 미들웨어의 목록을 정의함으로써 그룹내의 모든 라우트에 미들웨어가 적용됩니다. 미들웨어는 배열에 정의된 순서대로 실행될것입니다:

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
### 네임스페이스

그룹의 속성 배열에 `namespace` 파라미터를 사용하여 가룹의 모든 컨트롤러에 네임스페이스를 지정할 수 있습니다:

	Route::group(['namespace' => 'Admin'], function()
	{
		// Controllers Within The "App\Http\Controllers\Admin" Namespace

		Route::group(['namespace' => 'User'], function()
		{
			// Controllers Within The "App\Http\Controllers\Admin\User" Namespace
		});
	});

> **참고:** 기본적으로 `RouteServiceProvider`에서 포함하고 있는 `routes.php` 파일에는 라우트 컨트롤들을 위해서 네임스페이스가 지정되어 있습니다. 따라서 `App\Http\Controllers`의 전체 네임스페이스를 따로 지정할 필요는 없습니다.

<a name="sub-domain-routing"></a>
#### 서브 도메인 라우팅

라라벨 라우트에서는 와일드 파라미터 형태의 도메인 값을 설정하여 서브 도메인을 처리할 수 있습니다:

#### 서브 도메인 라우트 등록하기

	Route::group(['domain' => '{account}.myapp.com'], function()
	{

		Route::get('user/{id}', function($account, $id)
		{
			//
		});

	});

<a name="route-prefixing"></a>
### 라우트 접두어 지정하기

라우트 그룹의 접두어는 그룹의 속성 배열에 `prefix` 옵션을 사용하여 지정합니다:

	Route::group(['prefix' => 'admin'], function()
	{
		Route::get('users', function()
		{
			// Matches The "/admin/users" URL
		});
	});

또한, `prefix` 파라미터를 라우트들의 공통 파라미터로 지정할 수 있습니다:

#### 라우트 prefix 안에서 URL 파라미터 등록하기

	Route::group(['prefix' => 'accounts/{account_id}'], function()
	{
		Route::get('detail', function($account_id)
		{
			//
		});
	});

또한, 지정된 파라미터 변수의 제약 사항을 정의할 수도 있습니다:

	Route::group([
		'prefix' => 'accounts/{account_id}',
		'where' => ['account_id' => '[0-9]+'],
	], function() {

		// Define Routes Here
	});

<!--chak-comment-HTTP-라우팅(HTTP-Routing)-Route-Groups-->

<a name="route-model-binding"></a>
## 라우트 모델 바인딩

라라벨의 모델 바인딩은 라우트에 클래스 인스턴스를 주입할 수 있는 편리한 방법을 제공합니다. 예를 들어, 사용자의 ID를 넘기는 대신에 주어진 ID에 해당하는 User 클래스 인스턴스를 주입할 수 있습니다.

먼저 주어진 파라미터에 대한 클래스를 지정하기 위해서 라우트의 `model` 메소드를 사용하여야 합니다. 이 모델 바인딩은 `RouteServiceProvider::boot` 안에서 정의되어야 합니다.

#### 모델과 파라미터 바인딩하기

	public function boot(Router $router)
	{
		parent::boot($router);

		$router->model('user', 'App\User');
	}

다음으로, `{user}` 파라미터를 포함한 라우트를 정의합니다:

	Route::get('profile/{user}', function(App\User $user)
	{
		//
	});

`{user}` 파라미터와 `App\User` 모델이 바인딩되어 있으므로 라우트에는 `User` 인스턴스가 주입 될것입니다. 예를 들어, `profile/`으로 요청이 들어오면 ID가 1인 `User`의 인스턴스가 주입됩니다.

> **주의:** 만약 데이터베이스에서 일치하는 모델 인스턴스를 찾이 못하는 경우 404 에러가 발생합니다.

만약 "찾지 못함"의 동작을 지정하고 싶다면 세 번째 인자로 클로저를 전달하면 됩니다.

	Route::model('user', 'User', function()
	{
		throw new NotFoundHttpException;
	});

만약 고유한 의존성 검색 로직을 사용하려면 `Route::bind` 메소드를 사용해야 합니다. `bind` 메소드에 전달되는 클로저에는 URI 세그먼트에 해당하는 값이 전달되고 라우트에 주입할 클래스의 인스턴스를 반환해야 합니다:

	Route::bind('user', function($value)
	{
		return User::where('name', $value)->first();
	});

<!--chak-comment-HTTP-라우팅(HTTP-Routing)-라우트-모델-바인딩-->

<a name="throwing-404-errors"></a>
## 404 에러 발생시키기

라우트에서 404 에러를 발생시키는 방법은 2가지가 있습니다. 첫 번째로 `abort` 헬퍼함수를 사용하는 것입니다:

	abort(404);

`abort` 헬퍼함수는 지정된 상태 코드와 함께 `Symfony\Component\HttpKernel\Exception\HttpException`를 던집니다.

두 번째로 직접으로 `Symfony\Component\HttpKernel\Exception\NotFoundHttpException`을 던지는 것입니다.

404 예외 처리와 오류의 커스텀한 처리를 사용하는 보다 자세한 내용은 다음 문서의 [errors](/docs/5.0/errors#http-exceptions) 부분을 참조하십시오.

<!--chak-comment-HTTP-라우팅(HTTP-Routing)-404-에러-발생시키기-->
