# HTTP Responses

- [기본적인 Responses](#basic-responses)
- [리다이렉트](#redirects)
- [기타 Responses](#other-responses)
- [Response 매크로](#response-macros)

<a name="basic-responses"></a>
## 기본적인 Responses

#### 라우트로부터 문자열 반환

가장 기본적인 Response는 라우트로부터 반환되는 문자열입니다:

	Route::get('/', function()
	{
		return 'Hello World';
	});

#### 사용자 지정 Response 생성하기

대부분의 라우트나 컨트롤러 액션에서는 `Illuminate\Http\Response`의 인스턴스나 [뷰](/docs/5.0/views)를 반환합니다. `Response` 인스턴스를 반환하는 것은 여러분이 response의 HTTP 상태 코드나 헤더를 변경할 수 있도록 지원합니다. `Response`는 `Symfony\Component\HttpFoundation\Response` 클래스를 상속받고 있으며 HTTP response를 만들기 위한 다양한 메소드를 제공합니다.

	use Illuminate\Http\Response;

	return (new Response($content, $status))
	              ->header('Content-Type', $value);

좀 더 쉬운 방법으로 `response` 헬퍼함수를 사용할 수 있습니다.

	return response($content, $status)
	              ->header('Content-Type', $value);

> **참고:** `Response` 객체의 사용 가능한 전체 메소드 목록은 [API 문서](http://laravel.com/api/master/Illuminate/Http/Response.html)와 [Symfony API 문서](http://api.symfony.com/2.5/Symfony/Component/HttpFoundation/Response.html)를 참고하십시오.

#### Response에서 뷰파일 내보내기

`Response` 클래스 메소드에 액세스 할 필요가 있지만, Response의 컨텐츠 내용으로 뷰를 반환하고 싶다면, `view` 메소드를 사용하면 됩니다.

	return response()->view('hello')->header('Content-Type', $type);

#### Response에 Cookie 추가하기

	return response($content)->withCookie(cookie('name', 'value'));

#### 메소드 체이닝

대부분의 `Response` 메소드는 유연하게 response를 만들 수 있도록 체이닝이 가능합니다:

	return response()->view('hello')->header('Content-Type', $type)
                     ->withCookie(cookie('name', 'value'));

<!--chak-comment-HTTP-Responses-기본적인-Responses-->

<a name="redirects"></a>
## 리다이렉트

일반적으로 리다이렉트 Response는 `Illuminate\Http\RedirectResponse` 클래스의 인스턴스이며, 사용자를 다른 URL로 리다이렉트하는 데 필요한 적절한 헤더를 포함하고 있습니다.

#### 리다이렉트 반환하기

`RedirectResponse` 인스턴스를 생성하는 데는 몇 가지 방법이 있습니다. 가장 간단한 방법은 `redirect` 헬퍼 함수를 사용하는 것입니다. 테스트를 진행할 때 리다이렉트 Response를 생성하는 모킹(Mock)은 일반적으로 잘 하지 않기 때문에, 대부분의 경우에 헬퍼 함수를 사용하게 됩니다.

	return redirect('user/login');

#### 리다이렉트에 플래시 데이터와 함께 반환하기

새로운 URL로 리다이렉트 이동하고 [플래시 데이터를 세션에 저장](/docs/5.0/session) 하는 것은 일반적으로 동시에 진행됩니다. 따라서 편의성을 높이기 위해 `RedirectResponse` 인스턴스를 생성하고 **동시에** 메소드 체인을 통해 플래시 데이터를 세션에 저장할 수 있습니다:

	return redirect('user/login')->with('message', 'Login Failed');

#### 이전 URL로 리다이렉트

예를 들어, 폼 전송 후에, 사용자를 이전 URL로 리다이렉트 시키고자 하는 경우가 있을 수 있습니다. 이런 경우에는 `back` 메소드를 사용하면 됩니다:

	return redirect()->back();

	return redirect()->back()->withInput();

#### 이름이 지정된 라우트로 리다이렉트 하기

전달 인자 없이 `redirect` 헬퍼 함수를 호출할 때에는 `Illuminate\Routing\Redirector`의 인스턴스가 반환됩니다. 따라서 `Redirector` 인스턴스의 메소드를 사용할 수 있습니다. 예를 들어, 이름지 지정된 라우트로 이동하는 `RedirectResponse`를 생성하고자 한다면 `route` 메소드를 사용할 수 있습니다:

	return redirect()->route('login');

#### 이름이 지정된 라우트로 파라미터와 함께 리다이렉트 하기

라우트에 전달해야 할 파라미터가 있다면 `route` 메소드의 두 번째 인자로 전달하면 됩니다.

	// For a route with the following URI: profile/{id}

	return redirect()->route('profile', [1]);

라우트에 “ID” 파라미터를 따라 Eloquent 모델을 획득하는 라우트로 리디렉션하는 경우, 모델 그 자신을 그대로 전달할 수도 있습니다. ID는 자동으로 추출되어 질것입니다:

	return redirect()->route('profile', [$user]);

#### 이름지 지정된 라우트로 파라미터 이름과 함께 리다이렉트 하기

	// For a route with the following URI: profile/{user}

	return redirect()->route('profile', ['user' => 1]);

#### 컨트롤러 액션으로 리다이렉트 하기

이름이 지정된 라우트로 이동하는 `RedirectResponse` 인스턴스를 생성하는것과 비슷하게 [컨트롤러 액션](/docs/5.0/controllers)으로 리다이렉션 할 수 있습니다.

	return redirect()->action('App\Http\Controllers\HomeController@index');

> **주의:** `URL:setRootControllerNamespace`를 통해서 컨트롤러의 루트 네임스페이스가 지정되었다면, 전체 네임 스페이스를 지정할 필요가 없습니다.

#### 컨트롤러 액션으로 파라미터와 함께 리다이렉트 하기

	return redirect()->action('App\Http\Controllers\UserController@profile', [1]);

#### 컨트롤러 액션으로 파라미터 이름과 함께 리다이렉트 하기

	return redirect()->action('App\Http\Controllers\UserController@profile', ['user' => 1]);

<!--chak-comment-HTTP-Responses-리다이렉트-->

<a name="other-responses"></a>
## 기타 Response

`response` 헬퍼 함수를 사용하여 편리하게 다른 타입의 response 인스턴스를 생성할 수도 있습니다. `response` 헬퍼함수를 인자없이 호출하게 되면 `Illuminate\Contracts\Routing\ResponseFactory` [contract](/docs/5.0/contracts)를 반환합니다. 이 contract는 response를 생성하기 위한 다양한 메소드를 제공합니다.

#### JSON response 생성하기

`json` 메소드는 헤더의 `Content-Type`을 자동으로 `application/json`으로 지정합니다:

	return response()->json(['name' => 'Abigail', 'state' => 'CA']);

#### JSONP Response 생성하기

	return response()->json(['name' => 'Abigail', 'state' => 'CA'])
	                 ->setCallback($request->input('callback'));

#### 파일 다운로드 Response 생성하기

	return response()->download($pathToFile);

	return response()->download($pathToFile, $name, $headers);

	return response()->download($pathToFile)->deleteFileAfterSend(true);

> **참고:** 파일 다운로드를 관리하는 Symfony의 HttpFoundation에서 다운로드 할 파일의 이름이 ASCII 파일 이름임을 필요로 하고 있습니다.

<!--chak-comment-HTTP-Responses-기타-Response-->

<a name="response-macros"></a>
## Response 매크로

다양한 라우트와 컨트롤러에서 재사용할 수 있는 사용자 정의 Response를 정의하려면 `Illuminate\Contracts\Routing\ResponseFactory` 구현 클래스의 `macro` 메소드를 사용할 수 있습니다.

다음은 [service provider's](/docs/5.0/providers)의 `boot` 메소드에서 정의하는 예제입니다:

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

`macro` 메소드는 매크로로 지정할 이름을 첫 번째 인자로, 클로저를 두 번째 인자로 전달받습니다. 매크로로 등록된 클로저는 `response` 헬퍼 함수를 통해서 `ResponseFactory` 구현 객체에서 호출될것입니다.

	return response()->caps('foo');

<!--chak-comment-HTTP-Responses-Response-매크로-->
