# HTTP Responses
# HTTP Responses

- [Basic Responses 기본적인 Responses](#basic-responses)
- [Redirects 리다이렉트](#redirects)
- [Other Responses 기타 Responses](#other-responses)
- [Response Macros Response 매크로](#response-macros)

<a name="basic-responses"></a>
## Basic Responses
## 기본적인 Responses

#### Returning Strings From Routes
#### 라우트로 부터 문자열 반환

The most basic response from a Laravel route is a string:
가장 기본적인 Response 는 라우트로 부터 반환되는 문자열입니다. 

	Route::get('/', function()
	{
		return 'Hello World';
	});

#### Creating Custom Responses
#### 사용자 지정 Response 생성하기

However, for most routes and controller actions, you will be returning a full `Illuminate\Http\Response` instance or a [view](/docs/{{version}}/views). 대부분의 라우트나 컨트롤러 액션에서는 `Illuminate\Http\Response`의 인스턴스나  [뷰](/docs/{{version}}/views)를 반환합니다. Returning a full `Response` instance allows you to customize the response's HTTP status code and headers. `Response` 인스턴스를 반환하는 것은 여러분이 response 의 HTTP 상태 코드나 헤더를 변경할 수 있도록 지원합니다. A `Response` instance inherits from the `Symfony\Component\HttpFoundation\Response` class, providing a variety of methods for building HTTP responses: `Response` 는 `Symfony\Component\HttpFoundation\Response` 클래스를 상속받고 있으며 HTTP response 를 만들기 위한 다양한 메소드를 제공합니다. 

	use Illuminate\Http\Response;

	return (new Response($content, $status))
	              ->header('Content-Type', $value);

For convenience, you may also use the `response` helper:
좀 더 쉬운 방법으로 `response` 헬퍼 함수를 사용할 수 있습니다. 

	return response($content, $status)
	              ->header('Content-Type', $value);

> **Note 참고:** For a full list of available `Response` methods, check out its [API documentation](http://laravel.com/api/{{version}}/Illuminate/Http/Response.html) and the [Symfony API documentation](http://api.symfony.com/2.5/Symfony/Component/HttpFoundation/Response.html).
 `Response` 객체의 사용가능한 전체 메소드 목록은 [API 문서](http://laravel.com/api/{{version}}/Illuminate/Http/Response.html)와 [Symfony API 문서](http://api.symfony.com/2.5/Symfony/Component/HttpFoundation/Response.html) 를 참고하십시오. 

#### Sending A View In A Response
#### Response 에서 뷰파일 내보내기

If you need access to the `Response` class methods, but want to return a view as the response content, you may use the `view` method for convenience: `Response` 클래스 메서드에 액세스 할 필요가 있지만, Response 의 컨텐츠 내용으로 뷰를 반환하고 싶다면, `view` 메소드를 사용하면 됩니다. 

	return response()->view('hello')->header('Content-Type', $type);

#### Attaching Cookies To Responses
#### Response에 Cookie 추가하기

	return response($content)->withCookie(cookie('name', 'value'));

#### Method Chaining
#### 메소드 체이닝

Keep in mind that most `Response` methods are chainable, allowing for the fluent building of responses: 대부분의 `Response` 메소드는 유연하게 response 를 만들 수 있도록 체이닝이 가능합니다. 

	return response()->view('hello')->header('Content-Type', $type)
                     ->withCookie(cookie('name', 'value'));

<a name="redirects"></a>
## Redirects
## 리다이렉트

Redirect responses are typically instances of the `Illuminate\Http\RedirectResponse` class, and contain the proper headers needed to redirect the user to another URL. 일반적으로 리다이렉트 Response 는 `Illuminate\Http\RedirectResponse` 클래스의 인스턴스이며, 사용자를 다른 URL로 리다이렉트하는데 필요한 적절한 헤더를 포함하고 있습니다. 

#### Returning A Redirect
#### 리다이렉트 반환하기 

There are several ways to generate a `RedirectResponse` instance. `RedirectResponse` 인스턴스를 생성하는데는 몇가지 방법이 있습니다. The simplest method is to use the `redirect` helper method. 가장 간단한 방법은 `redirect` 헬퍼 함수를 사용하는 것입니다. When testing, it is not common to mock the creation of a redirect response, so using the helper method is almost always acceptable: 테스트를 진행할 때 리다이렉트 Response를 생성하는 모킹(Mock)은 일반적으로 잘 하지 않기 때문에, 대부분의 경우에 헬퍼 함수를 사용하게 됩니다. 

	return redirect('user/login');

#### Returning A Redirect With Flash Data
#### 리다이렉트에 플래시 데이터와 함께 반환하기

Redirecting to a new URL and [flashing data to the session](/docs/{{version}}/session) are typically done at the same time. 새로운 URL로 리다이렉트 이동하고 [플래시 데이터를 세션에 저장](/docs/{{version}}/session) 하는 것은 일반적으로 동시에 진행됩니다. So, for convenience, you may create a `RedirectResponse` instance **and** flash data to the session in a single method chain: 따라서 편의성을 높이기 위해 `RedirectResponse` 인스턴스를 생성하고 **동시에** 메소드 체인을 통해 플래시 데이터를 세션에 저장할 수 있습니다.

	return redirect('user/login')->with('message', 'Login Failed');

#### Redirecting To The Previous URL
#### 이전 URL로 리다이렉트

You may wish to redirect the user to their previous location, for example, after a form submission. 예를 들어 폼 전송 후에, 사용자를 이전 URL로 리다이렉트 시키고자 하는 경우가 있을 수 있습니다. You can do so by using the `back` method:  이런 경우에는 `back` 메소드를 사용하면 됩니다.

	return redirect()->back();

	return redirect()->back()->withInput();

#### Returning A Redirect To A Named Route
#### 이름이 지정된 라우트로 리다이렉트 하기

When you call the `redirect` helper with no parameters, an instance of `Illuminate\Routing\Redirector` is returned, allowing you to call any method on the `Redirector` instance. 전달 인자 없이 `redirect` 헬퍼 함수를 호출할 때에는 `Illuminate\Routing\Redirector`의 인스턴스가 반환됩니다. 따라서 `Redirector` 인스턴스의 메소드를 사용할 수 있습니다. For example, to generate a `RedirectResponse` to a named route, you may use the `route` method: 예를 들어 이름지 지정된 라우트로 이동하는 `RedirectResponse`를 생성하고자 한다면 `route` 메소드를 사용할 수 있습니다. 

	return redirect()->route('login');

#### Returning A Redirect To A Named Route With Parameters
#### 이름이 지정된 라우트로 파라미터와 함께 리다이렉트 하기

If your route has parameters, you may pass them as the second argument to the `route` method. 라우트에 전달해야할 파라미터가 있다면 `route` 메소드의 두번째 인자로 전달하면 됩니다. 

	// For a route with the following URI: profile/{id}

	return redirect()->route('profile', [1]);

If you are redirecting to a route with an "ID" parameter that is being populated from an Eloquent model, you may simply pass the model itself. The ID will be extracted automatically: 라우트에 “ID” 파라미터를 따라 Eloquent 모델을 획득하는 라우트로 리디렉션하는 경우, 모델 그 자신을 그대로 전달할 수도 있습니다. ID는 자동으로 추출되어 질것입니다. 

	return redirect()->route('profile', [$user]);

#### Returning A Redirect To A Named Route Using Named Parameters
#### 이름지 지정된 라우트로 파라미터 이름과 함께 리다이렉트 하기

	// For a route with the following URI: profile/{user}

	return redirect()->route('profile', ['user' => 1]);

#### Returning A Redirect To A Controller Action
#### 컨트롤러 액션으로 리다이렉트 하기

Similarly to generating `RedirectResponse` instances to named routes, you may also generate redirects to [controller actions](/docs/{{version}}/controllers): 이름이 지정된 라우트로 이동하는 `RedirectResponse` 인스턴스를 생성하는것과 비슷하게 [컨트롤러 액션](/docs/{{version}}/controllers) 으로 리다이렉션 할 수 있습니다. 

	return redirect()->action('App\Http\Controllers\HomeController@index');

> **Note 주의:** You do not need to specify the full namespace to the controller if you have registered a root controller namespace via `URL::setRootControllerNamespace`. `URL:setRootControllerNamespace` 를 통해서 컨트롤러의 루트 네임스페이스가 지정되었다면, 전체 네임 스페이스를 지정할 필요가 없습니다.

#### Returning A Redirect To A Controller Action With Parameters
#### 컨트롤러 액션으로 파라미터와 함께 리다이렉트 하기 

	return redirect()->action('App\Http\Controllers\UserController@profile', [1]);

#### Returning A Redirect To A Controller Action Using Named Parameters
#### 컨트롤러 액션으로 파라미터 이름과 함께 리다이렉트 하기 

	return redirect()->action('App\Http\Controllers\UserController@profile', ['user' => 1]);

<a name="other-responses"></a>
## Other Responses
## 기타 Response

The `response` helper may be used to conveniently generate other types of response instances. `response` 헬퍼 함수를 사용하여 편리하게 다른 타입의 response 인스턴스를 생성할 수도 있습니다. When the `response` helper is called without arguments, an implementation of the `Illuminate\Contracts\Routing\ResponseFactory` [contract](/docs/{{version}}/contracts) is returned. `response` 헬퍼함수를 인자없이 호출하게 되면 `Illuminate\Contracts\Routing\ResponseFactory` [contract](/docs/{{version}}/contracts) 를 반환합니다. This contract provides several helpful methods for generating responses. 이 contract 는 response 를 생성하기 위한 다양한 메소드를 제공합니다. 

#### Creating A JSON Response
#### JSON response 생성하기

The `json` method will automatically set the `Content-Type` header to `application/json`: `json` 메소드는 헤더의 `Content-Type` 을 자동으로 `application/json` 으로 지정합니다. 

	return response()->json(['name' => 'Abigail', 'state' => 'CA']);

#### Creating A JSONP Response
#### JSONP Response 생성하기 

	return response()->json(['name' => 'Abigail', 'state' => 'CA'])
	                 ->setCallback($request->input('callback'));

#### Creating A File Download Response
#### 파일 다운로드 Response 생성하기

	return response()->download($pathToFile);

	return response()->download($pathToFile, $name, $headers);

	return response()->download($pathToFile)->deleteFileAfterSend(true);

> **Note 참고:** Symfony HttpFoundation, which manages file downloads, requires the file being downloaded to have an ASCII file name. 파일 다운로드를 관리하는 Symfony의 HttpFoundation에서 다운로드 할 파일의 이름이 ASCII 파일 이름임을 필요로 하고 있습니다.

<a name="response-macros"></a>
## Response Macros
## Response 매크로

If you would like to define a custom response that you can re-use in a variety of your routes and controllers, you may use the `macro` method on an implementation of `Illuminate\Contracts\Routing\ResponseFactory`. 다양한 라우트와 컨트롤러에서 재사용할 수 있는 사용자 정의 Response를 정의하려면 `Illuminate\Contracts\Routing\ResponseFactory` 구현 클래스의 `macro` 메소드를 사용할 수 있습니다.

For example, from a [service provider's](/docs/{{version}}/providers) `boot` method: 다음은  [service provider's](/docs/{{version}}/providers)의 `boot` 메소드에서 정의하는 예제입니다. 

	<?php namespace App\Providers;

	use Response;
	use Illuminate\Support\ServiceProvider;

	class ResponseMacroServiceProvider extends ServiceProvider {

		/**
		 * Perform post-registration booting of services.
		 *
		 * @return void
		 */
		public function boot()
		{
			Response::macro('caps', function($value)
			{
				return Response::make(strtoupper($value));
			});
		}

	}

The `macro` function accepts a name as its first argument, and a Closure as its second. `macro` 메소드는 매크로로 지정할 이름을 첫 번째 인자로, 클로저를 두 번째 인자로 전달 받습니다. The macro's Closure will be executed when calling the macro name from a `ResponseFactory` implementation or the `response` helper: 매크로로 등록된 클로저는 `response` 헬퍼 함수를 통해서 `ResponseFactory` 구현 객체에서 호출될것 입니다. 

	return response()->caps('foo');
