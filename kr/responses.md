# HTTP Responses
# HTTP Responses

- [Basic Responses](#basic-responses)
- [기본적인 Responses](#basic-responses)
    - [Attaching Headers To Responses](#attaching-headers-to-responses)
    - [Responses에 header 추가하기](#attaching-headers-to-responses)
    - [Attaching Cookies To Responses](#attaching-cookies-to-responses)
    - [Responses에 Cookies 추가하기](#attaching-cookies-to-responses)
- [Other Response Types](#other-response-types)
- [다른 Response 타입들](#other-response-types)
    - [View Responses](#view-responses)
    - [View Responses](#view-responses)
    - [JSON Responses](#json-responses)
    - [JSON Responses](#json-responses)
    - [File Downloads](#file-downloads)
    - [파일 다운로드](#file-downloads)
- [Redirects](#redirects)
- [리다이렉트](#redirects)
    - [Redirecting To Named Routes](#redirecting-named-routes)
    - [이름이 지정된 라우트로 리다이렉트 하기](#redirecting-named-routes)
    - [Redirecting To Controller Actions](#redirecting-controller-actions)
    - [컨트롤러 액션으로 리다이렉트 하기](#redirecting-controller-actions)
    - [Redirecting With Flashed Session Data](#redirecting-with-flashed-session-data)
    - [세션의 임시 데이터와 함께 리다이렉트 하기](#redirecting-with-flashed-session-data)
- [Response Macros](#response-macros)
- [Response 매크로](#response-macros)

<a name="basic-responses"></a>
## Basic Responses
## 기본적인 Responses

Of course, all routes and controllers should return some kind of response to be sent back to the user's browser. Laravel provides several different ways to return responses. The most basic response is simply returning a string from a route or controller:

기본적으로 모든 라우터와 컨트롤러는 어떤 종류의 HTTP 응답을 사용자의 브라우저로 반환해야 합니다. 라라벨은 response 를 반환하는 다양한 방법을 제공합니다. 가장 기본적인 response는 라우트나 컨트롤러에서 문자열을 반환하는 것입니다. 

    Route::get('/', function () {
        return 'Hello World';
    });

The given string will automatically be converted into an HTTP response by the framework.

주어진 문자열은 프레임워크에 의해서 자동으로 HTTP response 로 변환되어질 것입니다. 

However, for most routes and controller actions, you will be returning a full `Illuminate\Http\Response` instance or a [view](/docs/{{version}}/views). Returning a full `Response` instance allows you to customize the response's HTTP status code and headers. A `Response` instance inherits from the `Symfony\Component\HttpFoundation\Response` class, providing a variety of methods for building HTTP responses:

대부분의 라우트나 컨트롤러 액션에서는 `Illuminate\Http\Response`의 인스턴스나  [뷰](/docs/{{version}}/views)를 반환합니다. `Response` 인스턴스를 반환하는 것은 여러분이 response 의 HTTP 상태 코드나 헤더를 변경할 수 있도록 지원합니다. `Response` 는 `Symfony\Component\HttpFoundation\Response` 클래스를 상속받고 있으며 HTTP response 를 만들기 위한 다양한 메소드를 제공합니다. 

    use Illuminate\Http\Response;

    Route::get('home', function () {
        return (new Response($content, $status))
                      ->header('Content-Type', $value);
    });

For convenience, you may also use the `response` helper:

좀 더 쉬운 방법으로 `response` 헬퍼 함수를 사용할 수 있습니다. 

    Route::get('home', function () {
        return response($content, $status)
                      ->header('Content-Type', $value);
    });

> **Note:** For a full list of available `Response` methods, check out its [API documentation](http://laravel.com/api/master/Illuminate/Http/Response.html) and the [Symfony API documentation](http://api.symfony.com/2.7/Symfony/Component/HttpFoundation/Response.html).

> **참고:** `Response` 객체의 사용가능한 전체 메소드 목록은 [API 문서](http://laravel.com/api/master/Illuminate/Http/Response.html)와 [Symfony API 문서](http://api.symfony.com/2.7/Symfony/Component/HttpFoundation/Response.html) 를 참고하십시오. 

<a name="attaching-headers-to-responses"></a>
#### Attaching Headers To Responses
#### Response 에 Header 추가하기

Keep in mind that most response methods are chainable, allowing for the fluent building of responses. For example, you may use the `header` method to add a series of headers to the response before sending it back to the user:

대부분의 response 메소드는 유연하나 구성을 위해서 체이닝이 가능합니다. 예를 들어서 사용자에게 response를 되돌려 주기 전에 `header` 메소드를 통해서 header를 추가할 수 있습니다. 

    return response($content)
                ->header('Content-Type', $type)
                ->header('X-Header-One', 'Header Value')
                ->header('X-Header-Two', 'Header Value');


<a name="attaching-cookies-to-responses"></a>
#### Attaching Cookies To Responses
#### Responses에 Cookies 추가하기

The `withCookie` helper method on the response instance allows you to easily attach cookies to the response. For example, you may use the `withCookie` method to generate a cookie and attach it to the response instance:

response 인스턴스의 `withCookie` 헬퍼 메소드는 response 에 쿠키를 손쉽게 추가할 수 있게 해줍니다. 다음처럼 response 인스턴스에서 `withCookie` 메소드를 사용하여 쿠키를 추가할 수 있습니다. 

    return response($content)->header('Content-Type', $type)
                     ->withCookie('name', 'value');

The `withCookie` method accepts additional optional arguments which allow you to further customize your cookie's properties:

`withCookie` 메소드는 다음처럼 선택적으로 추가 인자를 받아 들일 수 있으며, 쿠키의 속성을 사용자 정의할 수 있도록 합니다. 

    ->withCookie($name, $value, $minutes, $path, $domain, $secure, $httpOnly)

By default, all cookies generated by Laravel are encrypted and signed so that they can't be modified or read by the client. If you would like to disable encryption for a certain subset of cookies generated by your application, you may use the `$except` property of the `App\Http\Middleware\EncryptCookies` middleware:

기본적으로, 라라벨에서 생성되는 모든 쿠키는 암호화 되고, 서명이 적용되어 클리이언트에서는 수정하거나 확인할 수 없습니다. 만약 애플리케이션에서 생성하는 특정 쿠키를 암호화 처리 하지 않도록 하고자 한다면, `App\Http\Middleware\EncryptCookies` 미들웨어의 `$except` 속성을 사용하면 됩니다.

    /**
     * The names of the cookies that should not be encrypted.
     *
     * @var array
     */
    protected $except = [
        'cookie_name',
    ];

<a name="other-response-types"></a>
## Other Response Types
## 다른 Response 타입들 

The `response` helper may be used to conveniently generate other types of response instances. When the `response` helper is called without arguments, an implementation of the `Illuminate\Contracts\Routing\ResponseFactory` [contract](/docs/{{version}}/contracts) is returned. This contract provides several helpful methods for generating responses.

`response` 헬퍼 함수는 편리하게 다른 유형의 response 인스턴스를 생성하는 데 사용되어집니다. 전달되는 인자 없이 `response` 헬퍼가 호출될 때에는 `Illuminate\Contracts\Routing\ResponseFactory` [contract](/docs/{{version}}/contracts) 구현체가 반환될 것입니다. contract 는 response 를 생성하는 데 유용하고 다양한 메소드를 제공합니다. 

<a name="view-responses"></a>
#### View Responses
#### View Responses

If you need control over the response status and headers, but also need to return a [view](/docs/{{version}}/views) as the response content, you may use the `view` method:

response 의 status 와 header 뿐만 아니라, response 내용으로 [view](/docs/{{version}}/views)를 반환하고자 한다면, `view` 메소드를 사용하면 됩니다. 

    return response()->view('hello', $data)->header('Content-Type', $type);

Of course, if you do not need to pass a custom HTTP status code or custom headers, you may simply use the global `view` helper function.

사용자가 직접 HTTP 상태 코드나, 특정 헤더 값을 전달할 필요는 없습니다. 간단하게 `view` 헬퍼 함수를 사용할 수 있습니다. 

<a name="json-responses"></a>
#### JSON Responses
#### JSON Responses

The `json` method will automatically set the `Content-Type` header to `application/json`, as well as convert the given array into JSON using the `json_encode` PHP function:

`json` 메소드는 자동으로 `Content-Type` 헤더를 `application/json` 으로 설정하고, 주어진 배열을 PHP `json_encode` 함수를 사용하여 JSON으로 변환할 것입니다. 

    return response()->json(['name' => 'Abigail', 'state' => 'CA']);

If you would like to create a JSONP response, you may use the `json` method in addition to `setCallback`:

JSONP response 를 생성하고자 한다면, `json` 메소드에 추가적으로 `setCallback` 사용하면 됩니다. 

    return response()->json(['name' => 'Abigail', 'state' => 'CA'])
                     ->setCallback($request->input('callback'));

<a name="file-downloads"></a>
#### File Downloads
#### 파일 다운로드

The `download` method may be used to generate a response that forces the user's browser to download the file at the given path. The `download` method accepts a file name as the second argument to the method, which will determine the file name that is seen by the user downloading the file. Finally, you may pass an array of HTTP headers as the third argument to the method:

`download` 메소드는 사용자의 브라우저가 주어진 경로에 해당하는 파일을 다운로드 하게 하는 response를 생성하는데 사용됩니다. `download` 메소드는 사용자가 다운로드 하는 파일의 이름을 두번째 인자로 받습니다. 마지막으로 HTTP 헤더의 배열을 세번째 인자로 전달할 수 있습니다. 

    return response()->download($pathToFile);

    return response()->download($pathToFile, $name, $headers);

> **Note:** Symfony HttpFoundation, which manages file downloads, requires the file being downloaded to have an ASCII file name.

> **주의:** 파일 다운로드를 관리하는 Symfony HttpFoundation 클래스는 ASCII 형식의 파일 이름을 지정해야 합니다. 

<a name="redirects"></a>
## Redirects
## 리다이렉트

Redirect responses are instances of the `Illuminate\Http\RedirectResponse` class, and contain the proper headers needed to redirect the user to another URL. There are several ways to generate a `RedirectResponse` instance. The simplest method is to use the global `redirect` helper method:

리다이렉트 Response 는 `Illuminate\Http\RedirectResponse` 클래스의 인스턴스이며, 사용자를 다른 URL로 리다이렉트하는데 필요한 적절한 헤더를 포함하고 있습니다. `RedirectResponse` 인스턴스를 생성하려면 여러가지 방법이 있습니다. 가장 쉬운 방법은 글로벌 `redirect` 헬퍼 메소드를 사용하는 것입니다. 

    Route::get('dashboard', function () {
        return redirect('home/dashboard');
    });

Sometimes you may wish to redirect the user to their previous location, for example, after a form submission that is invalid. You may do so by using the global `back` helper function:

예를 들어서, form 의 submit 을 처리하는데 검증이 일치하지 않아 사용자를 이전 위치로 리다이렉트 시키고자 할 수 있습니다. 이 경우에는 `back` 헬퍼 함수를 사용하면 됩니다. 

    Route::post('user/profile', function () {
        // Validate the request...

        return back()->withInput();
    });

<a name="redirecting-named-routes"></a>
#### Redirecting To Named Routes
#### 이름이 지정된 라우트로 리다이렉트 하기

When you call the `redirect` helper with no parameters, an instance of `Illuminate\Routing\Redirector` is returned, allowing you to call any method on the `Redirector` instance. For example, to generate a `RedirectResponse` to a named route, you may use the `route` method:

`redirect` 헬퍼가 아무런 인자 없이 호출될 때에는, `Illuminate\Routing\Redirector` 인스턴스가 반환되어, `Redirector` 인스턴스의 메소드를 사용할 수 있습니다. 다음과 같이 이름이 지정된 라우트에 대한 `RedirectResponse` 를 생성하려면 `route` 메소드를 사용할 수 있습니다. 

    return redirect()->route('login');

If your route has parameters, you may pass them as the second argument to the `route` method:

라우트가 인자를 받아야 한다면, `route` 메소드의 두번째 인자로 이를 전달할 수 있습니다. 

    // For a route with the following URI: profile/{id}

    return redirect()->route('profile', [1]);

If you are redirecting to a route with an "ID" parameter that is being populated from an Eloquent model, you may simply pass the model itself. The ID will be extracted automatically:

라우트에 “ID” 파라미터를 따라 Eloquent 모델을 획득하는 라우트로 리디렉션하는 경우, 모델 그 자신을 그대로 전달할 수도 있습니다. ID는 자동으로 추출되어 질것입니다. 

    return redirect()->route('profile', [$user]);

<a name="redirecting-controller-actions"></a>
#### Redirecting To Controller Actions
#### 컨트롤러 액션으로 리다이렉트 하기

You may also generate redirects to [controller actions](/docs/{{version}}/controllers). To do so, simply pass the controller and action name to the `action` method. Remember, you do not need to specify the full namespace to the controller since Laravel's `RouteServiceProvider` will automatically set the default controller namespace:

또한 [컨트롤러 액션]으로 리다이렉트하는 응답을 생성할 수도 있습니다. 이렇게 하기 위해서는, 간단하게 컨트로러와 액션의 이름을 `action` 메소드에 전달하면 됩니다. 주의할 것은 라라벨이 자동으로 기본 컨트롤러 네임스페이스를 설정하기 때문에 전체 네임스페이스를 지정할 필요가 없다는 것입니다. 

    return redirect()->action('HomeController@index');

Of course, if your controller route requires parameters, you may pass them as the second argument to the `action` method:

컨트롤러 라우트에 파라미터가 필요한 경우, `action` 메소드의 두번째 인자로 전달하면 됩니다: 

    return redirect()->action('UserController@profile', [1]);

<a name="redirecting-with-flashed-session-data"></a>
#### Redirecting With Flashed Session Data

Redirecting to a new URL and [flashing data to the session](/docs/{{version}}/session#flash-data) are typically done at the same time. So, for convenience, you may create a `RedirectResponse` instance **and** flash data to the session in a single method chain. This is particularly convenient for storing status messages after an action:

새로운 URL로 리다이렉트 이동하고 [플래시 데이터를 세션에 저장](/docs/{{version}}/session) 하는 것은 일반적으로 동시에 진행됩니다. 따라서 편의성을 높이기 위해 `RedirectResponse` 인스턴스를 생성하고 **동시에** 메소드 체인을 통해 플래시 데이터를 세션에 저장할 수 있습니다. 주로 어떤 동작을 수행하고 상태 메세지를 저장할 때 편리합니다. 

    Route::post('user/profile', function () {
        // Update the user's profile...

        return redirect('dashboard')->with('status', 'Profile updated!');
    });

Of course, after the user is redirected to a new page, you may retrieve and display the flashed message from the [session](/docs/{{version}}/session). For example, using [Blade syntax](/docs/{{version}}/blade):

새로운 페이지로 리다이렉트 되고 난 뒤에, 임시 저장된 메세지를 [세션](/docs/{{version}}/session) 에서 찾아 표시할 수 있습니다. 다음은 [블레이드 문법](/docs/{{version}}/blade)을 사용한 방법입니다. 

    @if (session('status'))
        <div class="alert alert-success">
            {{ session('status') }}
        </div>
    @endif

<a name="response-macros"></a>
## Response Macros
## Response 매크로

If you would like to define a custom response that you can re-use in a variety of your routes and controllers, you may use the `macro` method on an implementation of `Illuminate\Contracts\Routing\ResponseFactory`.

다양한 라우트와 컨트롤러에서 재사용할 수 있는 사용자 정의 Response를 정의하려면 `Illuminate\Contracts\Routing\ResponseFactory` 구현 클래스의 `macro` 메소드를 사용할 수 있습니다.

For example, from a [service provider's](/docs/{{version}}/providers) `boot` method:

다음은 [서비스 프로바이더](/docs/{{version}}/providers)의 `boot` 메소드에서 정의하는 예제입니다. 

    <?php

    namespace App\Providers;

    use Illuminate\Support\ServiceProvider;
    use Illuminate\Contracts\Routing\ResponseFactory;

    class ResponseMacroServiceProvider extends ServiceProvider
    {
        /**
         * Perform post-registration booting of services.
         *
         * @param  ResponseFactory  $factory
         * @return void
         */
        public function boot(ResponseFactory $factory)
        {
            $factory->macro('caps', function ($value) use ($factory) {
                return $factory->make(strtoupper($value));
            });
        }
    }

The `macro` function accepts a name as its first argument, and a Closure as its second. The macro's Closure will be executed when calling the macro name from a `ResponseFactory` implementation or the `response` helper:

`macro` 메소드는 매크로로 지정할 이름을 첫 번째 인자로, 클로저를 두 번째 인자로 전달 받습니다. 매크로로 등록된 클로저는 `response` 헬퍼 함수를 통해서 `ResponseFactory` 구현 객체에서 호출될것 입니다. 

    return response()->caps('foo');
