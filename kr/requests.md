# HTTP Requests
# HTTP Requests-요청

- [Introduction](#introduction)
- [소개하기](#introduction)
- [Interacting With The Request](#interacting-with-the-request)
- [Request와 상호작용](#interacting-with-the-request)
   - [Accessing The Request](#accessing-the-request)
   - [Request 접근](#accessing-the-request)
   - [Request Path & Method](#request-path-and-method)
   - [Request 경로 & 메소드](#request-path-and-method)
   - [Request Headers](#request-headers)
   - [Request 헤더](#request-headers)
   - [Request IP Address](#request-ip-address)
   - [Request IP 주소](#request-ip-address)
   - [Content Negotiation](#content-negotiation)
   - [Content 협상](#content-negotiation)
   - [PSR-7 Requests](#psr7-requests)
   - [PSR-7 Requests](#psr7-requests)
- [Input](#input)
- [입력](#input)
   - [Retrieving Input](#retrieving-input)
   - [검색값 조회](#retrieving-input)
   - [Determining If Input Is Present](#determining-if-input-is-present)
   - [입력이 있는지 확인하기](#determining-if-input-is-present)
   - [Merging Additional Input](#merging-additional-input)
   - [추가 입력 병합](#merging-additional-input)
   - [Old Input](#old-input)
   - [이전 입력](#old-input)
   - [Cookies](#cookies)
   - [쿠키](#cookies)
   - [Input Trimming & Normalization](#input-trimming-and-normalization)
   - [입력 Trimming & 표준회](#input-trimming-and-normalization)
- [Files](#files)
- [파일](#files)
   - [Retrieving Uploaded Files](#retrieving-uploaded-files)
   - [업로드 파일 검색](#retrieving-uploaded-files)
   - [Storing Uploaded Files](#storing-uploaded-files)
   - [업로드 파일 수납](#storing-uploaded-files)
- [Configuring Trusted Proxies](#configuring-trusted-proxies)
- [신뢰할 수 있는 프록시 설정](#configuring-trusted-proxies)
- [Configuring Trusted Hosts](#configuring-trusted-hosts)
- [신뢰할 수 있는 호스트 설정](#configuring-trusted-hosts)

<a name="introduction"></a>
## Introduction
## 소개하기

Laravel's `Illuminate\Http\Request` class provides an object-oriented way to interact with the current HTTP request being handled by your application as well as retrieve the input, cookies, and files that were submitted with the request.

라라벨의 `Illuminate\Http\Request` 클래스는 요청과 함께 제출된 입력, 쿠키 및 파일을 검색할 뿐만 아니라 애플리케이션에서 처리 중인 현재 HTTP 요청과 상호 작용하는 객체 지향 방식을 제공합니다.

<a name="interacting-with-the-request"></a>
## Interacting With The Request
## Request와 상호작용

<a name="accessing-the-request"></a>
### Accessing The Request
### Request 엑세스 하기

To obtain an instance of the current HTTP request via dependency injection, you should type-hint the `Illuminate\Http\Request` class on your route closure or controller method. The incoming request instance will automatically be injected by the Laravel [service container](/docs/{{version}}/container):

의존성 주입을 통해 현재 HTTP 요청의 인스턴스를 얻으려면 route 클로저 또는 컨트롤러 메서드에서 `Illuminate\Http\Request` 클래스를 입력해야 합니다. 유입된 request 인스턴스는 라라벨 [service container](/docs/{{version}}/container)에 의해 자동으로 주입됩니다.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;

    class UserController extends Controller
    {
        /**
         * Store a new user.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return \Illuminate\Http\Response
         */
        public function store(Request $request)
        {
            $name = $request->input('name');

            //
        }
    }

As mentioned, you may also type-hint the `Illuminate\Http\Request` class on a route closure. The service container will automatically inject the incoming request into the closure when it is executed:

언급된 것 처럼 route 클로저에서 `Illuminate\Http\Request` 클래스를 입력할 수도 있습니다. 서비스 컨테이너는 실행될 때 들어오는 요청을 클로저에 자동으로 주입합니다.

    use Illuminate\Http\Request;

    Route::get('/', function (Request $request) {
        //
    });

<a name="dependency-injection-route-parameters"></a>
#### Dependency Injection & Route Parameters
#### 의존성 주입 & 라우트 파라미터

If your controller method is also expecting input from a route parameter you should list your route parameters after your other dependencies. For example, if your route is defined like so:

만약 컨트롤러 메소드에서 라우트 파라미터로 부터 입력값을 받아야 한다면, 다른 의존성을 지정한 뒤에 라우트 파라미터를 나열해야 합니다. 예를 들어 라우트는 다음과 같이 정의될 수 있습니다.

    use App\Http\Controllers\UserController;

    Route::put('/user/{id}', [UserController::class, 'update']);

You may still type-hint the `Illuminate\Http\Request` and access your `id` route parameter by defining your controller method as follows:

다음과 같이 컨트롤러 메소드를 정의하여 `Illuminate\Http\Request`를 타입힌트하면서 동시에 라우트 파라미터 `id`에 접근할 수 있습니다.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;

    class UserController extends Controller
    {
        /**
         * Update the specified user.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  string  $id
         * @return \Illuminate\Http\Response
         */
        public function update(Request $request, $id)
        {
            //
        }
    }

<a name="request-path-and-method"></a>
### Request Path & Method
### Request 경로 & 메소드

The `Illuminate\Http\Request` instance provides a variety of methods for examining the incoming HTTP request and extends the `Symfony\Component\HttpFoundation\Request` class. We will discuss a few of the most important methods below.

`Illuminate\Http\Request` 인스턴스는 애플리케이션에 유입되는 HTTP 요청을 검사하기 위해 다양한 메소드를 제공하고 `Symfony\Component\HttpFoundation\Request` 클래스를 상속합니다. 아래에서 몇가지 가장 중요한 메소드를 알아보겠습니다.

<a name="retrieving-the-request-path"></a>
#### Retrieving The Request Path
#### Request 경로 조회하기

The `path` method returns the request's path information. So, if the incoming request is targeted at `http://example.com/foo/bar`, the `path` method will return `foo/bar`:

`path` 메소드는 request의 경로정보를 반환합니다. 따라서 들어오는 request가 `http://example.com/foo/bar` 를 대상으로 한다면 `path` 메소드는 `foo/bar`를 반환합니다.

    $uri = $request->path();

<a name="inspecting-the-request-path"></a>
#### Inspecting The Request Path / Route
#### Request 경로 검사

The `is` method allows you to verify that the incoming request path matches a given pattern. You may use the `*` character as a wildcard when utilizing this method:

`is` 메소드는 들어오는 request가 특정 패턴에 상응한다는 것을 확인 할 수 있게 해줍니다. 이 메소드를 활용할 때 `*` 기호를 와일드카드로 쓸 수 있습니다.

    if ($request->is('admin/*')) {
        //
    }

Using the `routeIs` method, you may determine if the incoming request has matched a [named route](/docs/{{version}}/routing#named-routes):

`routeIs` 메소드를 사용하면 request 요청이 [named route](/docs/{{version}}/routing#named-routes)와 일치하는지 확인할 수 있습니다.

    if ($request->routeIs('admin.*')) {
        //
    }

<a name="retrieving-the-request-url"></a>
#### Retrieving The Request URL
#### Request URI 조회하기

To retrieve the full URL for the incoming request you may use the `url` or `fullUrl` methods. The `url` method will return the URL without the query string, while the `fullUrl` method includes the query string:

유입되는 request 의 전체 URL을 조회하기 위해서 `url` 또는 `fullUrl` 메소드를 사용할 수 있습니다. `url` 메소드는 쿼리 스트링 없는 URL을, `fullUrl` 은 쿼리 스트링을 포함한 URL을 반환합니다.

    $url = $request->url();

    $urlWithQueryString = $request->fullUrl();

If you would like to append query string data to the current URL, you may call the `fullUrlWithQuery` method. This method merges the given array of query string variables with the current query string:

현재 URL에 query string 데이터를 추가하려면 `fullUrlWithQuery` 메서드를 호출하면 됩니다. 이 메서드는 주어진 query string 변수 배열을 현재 query string과 병합합니다.

    $request->fullUrlWithQuery(['type' => 'phone']);

<a name="retrieving-the-request-method"></a>
#### Retrieving The Request Method
#### Request HTTP 메소드(verb) 조회하기

The `method` method will return the HTTP verb for the request. You may use the `isMethod` method to verify that the HTTP verb matches a given string:

`method` 메소드는 request에 대해 HTTP 메소드를 반환합니다. HTTP 메소드가 특정 문자열에 대응하는 것을 확인하기 위해 `isMethod` 메소드를 사용할 수 있습니다.

    $method = $request->method();

    if ($request->isMethod('post')) {
        //
    }

<a name="request-headers"></a>
### Request Headers
### Request 헤더

You may retrieve a request header from the `Illuminate\Http\Request` instance using the `header` method. If the header is not present on the request, `null` will be returned. However, the `header` method accepts an optional second argument that will be returned if the header is not present on the request:

`header` 메소드를 사용하여 `Illuminate\Http\Request` 인스턴스에서 요청 헤더를 검색할 수 있습니다. 요청에 헤더가 없으면 `null`이 반환됩니다. 그러나 `header` 메서드는 요청에 헤더가 없으면 반환되는 선택적 두 번째 인수를 허용합니다.

    $value = $request->header('X-Header-Name');

    $value = $request->header('X-Header-Name', 'default');

The `hasHeader` method may be used to determine if the request contains a given header:

`hasHeader` 메소드는 요청에 지정된 헤더가 포함되어 있는지 확인하는 데 사용할 수 있습니다.

    if ($request->hasHeader('X-Header-Name')) {
        //
    }

For convenience, the `bearerToken` method may be used to retrieve a bearer token from the `Authorization` header. If no such header is present, an empty string will be returned:

편의상 `Authorization` 헤더에서 bearer 토큰을 검색하기 위해 `bearerToken` 메소드를 사용할 수 있습니다. 만약 헤더가 없으면 빈 문자열이 반환됩니다.

    $token = $request->bearerToken();

<a name="request-ip-address"></a>
### Request IP Address
### Request IP 주소

The `ip` method may be used to retrieve the IP address of the client that made the request to your application:

`ip` 메소드를 사용하여 애플리케이션에 요청한 클라이언트의 IP 주소를 검색할 수 있습니다.

    $ipAddress = $request->ip();

<a name="content-negotiation"></a>
### Content Negotiation
### Content 협상

Laravel provides several methods for inspecting the incoming request's requested content types via the `Accept` header. First, the `getAcceptableContentTypes` method will return an array containing all of the content types accepted by the request:

라라벨은 `Accept` 헤더를 통해 들어오는 요청의 요청된 Content 유형을 검사하는 여러 방법을 제공합니다. 먼저 `getAcceptableContentTypes` 메소드는 요청에 의해 수락된 모든 Content 유형을 포함하는 배열을 반환합니다.

    $contentTypes = $request->getAcceptableContentTypes();

The `accepts` method accepts an array of content types and returns `true` if any of the content types are accepted by the request. Otherwise, `false` will be returned:

`accepts` 메소드는 Content 유형의 배열을 허용하고 요청에 의해 content 유형이 수락되면 `true`를 반환합니다. 그렇지 않으면 `false`가 반환됩니다.

    if ($request->accepts(['text/html', 'application/json'])) {
        // ...
    }

You may use the `prefers` method to determine which content type out of a given array of content types is most preferred by the request. If none of the provided content types are accepted by the request, `null` will be returned:

`prefers` 메소드를 사용하여 주어진 콘텐츠 유형 배열 중에서 요청이 가장 선호하는 content 유형을 결정할 수 있습니다. 제공된 콘텐츠 유형 중 어느 것도 요청에 의해 수락되지 않으면 `null` 이 반환됩니다.

    $preferred = $request->prefers(['text/html', 'application/json']);

Since many applications only serve HTML or JSON, you may use the `expectsJson` method to quickly determine if the incoming request expects a JSON response:

많은 애플리케이션이 HTML이나 JSON만 제공하기 때문에 `expectsJson` 메소드를 사용하여 들어오는 요청이 JSON 응답을 예상하는지 빠르게 결정할 수 있습니다.

    if ($request->expectsJson()) {
        // ...
    }

<a name="psr7-requests"></a>
### PSR-7 Requests
### PSR-7 Requests

The [PSR-7 standard](https://www.php-fig.org/psr/psr-7/) specifies interfaces for HTTP messages, including requests and responses. If you would like to obtain an instance of a PSR-7 request instead of a Laravel request, you will first need to install a few libraries. Laravel uses the *Symfony HTTP Message Bridge* component to convert typical Laravel requests and responses into PSR-7 compatible implementations:

[PSR-7 표준](https://www.php-fig.org/psr/psr-7/) 은 요청과 응답을 포함한 HTTP 메세지들에 대한 인터페이스를 지정합니다. 라라벨의 request 대신 PSR-7 요청의 인스턴스를 획득하기 위해서는 우선 몇 개의 라이브러리를 설치해야 합니다. 라라벨은 *Symfony HTTP Message Bridge* 컴포넌트를 사용하여 일반적인 라라벨의 request-요청과 response-응답을 PSR-7에 맞는 구현체로 변환합니다.

```shell
composer require symfony/psr-http-message-bridge
composer require nyholm/psr7
```

Once you have installed these libraries, you may obtain a PSR-7 request by type-hinting the request interface on your route closure or controller method:

이 라이브러리들을 설치하였다면 라우트 클로저나 컨트롤러 메소드에 request를 타입힌트하여 PSR-7 request를 얻을 수 있습니다.

    use Psr\Http\Message\ServerRequestInterface;

    Route::get('/', function (ServerRequestInterface $request) {
        //
    });

> {tip} If you return a PSR-7 response instance from a route or controller, it will automatically be converted back to a Laravel response instance and be displayed by the framework.

> {tip} 라우트나 컨트롤러에서 반환된 PSR-7 response 인스턴스는 자동으로 라라벨 response 인스턴스로 변환되어 프레임워크에 표시될 것입니다.

<a name="input"></a>
## Input
## 입력

<a name="retrieving-input"></a>
### Retrieving Input
### 입력값 조회하기

<a name="retrieving-all-input-data"></a>
#### Retrieving All Input Data
#### 모든 입력값 조회하기

You may retrieve all of the incoming request's input data as an `array` using the `all` method. This method may be used regardless of whether the incoming request is from an HTML form or is an XHR request:

`all` 메서드를 사용하여 들어오는 요청의 모든 입력 데이터를 `array`로 검색할 수 있습니다. 이 메서드는 들어오는 요청이 HTML 양식에서 온 것인지 XHR 요청에서 온 것인지에 관계없이 사용할 수 있습니다.

    $input = $request->all();

Using the `collect` method, you may retrieve all of the incoming request's input data as a [collection](/docs/{{version}}/collections):

`collect` 메소드를 사용하면 들어오는 요청의 모든 입력 데이터를 [collection](/docs/{{version}}/collections)으로 검색할 수 있습니다.

    $input = $request->collect();

The `collect` method also allows you to retrieve a subset of the incoming request input as a collection:

`collect` 메소드를 사용하면 들어오는 요청 input의 하위 집합을 collection으로 검색할 수도 있습니다.

    $request->collect('users')->each(function ($user) {
        // ...
    });

<a name="retrieving-an-input-value"></a>
#### Retrieving An Input Value
#### 입력값 조회하기

Using a few simple methods, you may access all of the user input from your `Illuminate\Http\Request` instance without worrying about which HTTP verb was used for the request. Regardless of the HTTP verb, the `input` method may be used to retrieve user input:

몇 개의 단순한 메소드들을 사용하면 어떤 HTTP verb 가 request 에 사용되었는지에 대한 걱정없이 `Illuminate\Http\Request` 인스턴스에서 모든 사용자 입력에 접근할 수 있습니다. HTTP verb에 관계없이 `input` 메소드는 사용자 입력을 조회하는데 사용됩니다.

    $name = $request->input('name');

You may pass a default value as the second argument to the `input` method. This value will be returned if the requested input value is not present on the request:

`input` 메소드에 두번째 인자로 기본값을 전달할 수 있습니다. Request에 요청된 입력 값이 존재하지 않는다면 기본값이 반환됩니다.

    $name = $request->input('name', 'Sally');

When working with forms that contain array inputs, use "dot" notation to access the arrays:

배열 입력을 가진 폼에서 동작할 때에는, 배열에 접근하기 위하여 "점" 표기법을 사용할 수 있습니다.

    $name = $request->input('products.0.name');

    $names = $request->input('products.*.name');

You may call the `input` method without any arguments in order to retrieve all of the input values as an associative array:

모든 입력 값을 연관 배열로 검색하기 위해 인자없이 `input` 메소드를 호출 할 수 있습니다.

    $input = $request->input();

<a name="retrieving-input-from-the-query-string"></a>
#### Retrieving Input From The Query String
#### 쿼리 스트링에서 입력값 조회하기

While the `input` method retrieves values from the entire request payload (including the query string), the `query` method will only retrieve values from the query string:

`input` 메소드가 요청-request 전체의 payload(쿼리 스트링을 포함하여)에서 값을 조회한다면, `query` 메소드는 쿼리 스트링에서만 값을 조회합니다.

    $name = $request->query('name');

If the requested query string value data is not present, the second argument to this method will be returned:

찾고자 하는 데이터가 존재하지 않을 때를 위해서 메소드의 두번째 인자로 기본값을 전달할 수 있습니다.

    $name = $request->query('name', 'Helen');

You may call the `query` method without any arguments in order to retrieve all of the query string values as an associative array:

`query` 메소드를 호출할 때 아무런 인자를 전달하지 않는다면, 전체 쿼리 스트링의 값들을 배열 형태로 반환합니다.

    $query = $request->query();

<a name="retrieving-json-input-values"></a>
#### Retrieving JSON Input Values
#### JSON 입력값 조회하기

When sending JSON requests to your application, you may access the JSON data via the `input` method as long as the `Content-Type` header of the request is properly set to `application/json`. You may even use "dot" syntax to retrieve values that are nested within JSON arrays:

JSON 요청을 애플리케이션에 보낼 때 요청의 `Content-Type` 헤더가 `application/json`으로 올바르게 설정되어 있으면 `input` 메소드를 통해 JSON 데이터에 액세스할 수 있습니다. "dot" 구문을 사용하여 JSON 배열 내에 중첩된 값을 검색할 수도 있습니다.

    $name = $request->input('user.name');

<a name="retrieving-boolean-input-values"></a>
#### Retrieving Boolean Input Values
#### Boolean 입력값 조회하기

When dealing with HTML elements like checkboxes, your application may receive "truthy" values that are actually strings. For example, "true" or "on". For convenience, you may use the `boolean` method to retrieve these values as booleans. The `boolean` method returns `true` for 1, "1", true, "true", "on", and "yes". All other values will return `false`:

체크박스와 같은 HTML 요소를 처리할 때 애플리케이션은 실제로 문자열인 "truthy" 값을 수신할 수 있습니다. 예를 들어 "true" 또는 "on"입니다. 편의를 위해 `boolean` 메서드를 사용하여 이러한 값을 부울로 검색할 수 있습니다. `boolean` 메소드는 1, "1", true, "true", "on" 및 "yes"에 대해 `true`를 반환합니다. 다른 모든 값은 `false`를 반환합니다.

    $archived = $request->boolean('archived');

<a name="retrieving-date-input-values"></a>
#### Retrieving Date Input Values
#### Date 입력값 조회하기

For convenience, input values containing dates / times may be retrieved as Carbon instances using the `date` method. If the request does not contain an input value with the given name, `null` will be returned:

편의를 위해 날짜/시간을 포함하는 입력 값은 `date` 메서드를 사용하여 Carbon 인스턴스로 검색할 수 있습니다. 요청에 주어진 이름의 입력 값이 없으면 `null`이 반환됩니다.

    $birthday = $request->date('birthday');

The second and third arguments accepted by the `date` method may be used to specify the date's format and timezone, respectively:

`date` 메서드가 허용하는 두 번째 및 세 번째 인수는 각각 날짜 형식과 시간대를 지정하는 데 사용할 수 있습니다.

    $elapsed = $request->date('elapsed', '!H:i', 'Europe/Madrid');

If the input value is present but has an invalid format, an `InvalidArgumentException` will be thrown; therefore, it is recommended that you validate the input before invoking the `date` method.

입력 값이 있지만 형식이 잘못된 경우 `InvalidArgumentException`이 발생합니다. 따라서 `date` 메소드를 호출하기 전에 입력을 검증하는 것이 좋습니다.

<a name="retrieving-input-via-dynamic-properties"></a>
#### Retrieving Input Via Dynamic Properties
#### 동적 속성을 통한 입력값 조회하기

You may also access user input using dynamic properties on the `Illuminate\Http\Request` instance. For example, if one of your application's forms contains a `name` field, you may access the value of the field like so:

`Illuminate\Http\Request` 인스턴스의 동적 속성을 사용하여 사용자 입력에 엑세스할 수도 있습니다. 예를 들어 애플리케이션의 form 중에 하나가 `name` 필드를 가지고 있다면, 다음과 같이 필드의 값에 엑세스 할 수 있습니다.

    $name = $request->name;

When using dynamic properties, Laravel will first look for the parameter's value in the request payload. If it is not present, Laravel will search for the field in the matched route's parameters.

동적 속성을 사용할 때, 라라벨은 먼저 request payload 안에 있는 파라미터의 값을 찾습니다. 만약 값이 없다면 라라벨은 라우트 파라미터 안에 있는 필드를 찾을 것입니다.

<a name="retrieving-a-portion-of-the-input-data"></a>
#### Retrieving A Portion Of The Input Data
#### 입력 데이터의 한 부분 조회하기

If you need to retrieve a subset of the input data, you may use the `only` and `except` methods. Both of these methods accept a single `array` or a dynamic list of arguments:

입력 데이터의 일부분만 조회하기 위해서 `only`와 `except` 메소드를 사용할 수 있습니다. 이 두 메소드 모두 하나의 `배열` 또는 동적인 인자의 목록을 받아 들입니다.

    $input = $request->only(['username', 'password']);

    $input = $request->only('username', 'password');

    $input = $request->except(['credit_card']);

    $input = $request->except('credit_card');

> {note} The `only` method returns all of the key / value pairs that you request; however, it will not return key / value pairs that are not present on the request.

> {note} `only` 메소드는 유입되는 request-요청에서 입력한 키 / 값 쌍을 반환합니다. 그렇지만 현재 request 에서 존재하지 않는 키/값은 반환하지 않습니다.

<a name="determining-if-input-is-present"></a>
### Determining If Input Is Present
### 입력이 있는지 확인하기

You may use the `has` method to determine if a value is present on the request. The `has` method returns `true` if the value is present on the request:

Request에 어떤 값이 존재하는지 확인하기 위해서 `has` 메소드를 사용해야 합니다. `has` 메소드는 현재 request 에서 값이 존재할 때 `true`를 반환합니다.

    if ($request->has('name')) {
        //
    }

When given an array, the `has` method will determine if all of the specified values are present:

`has` 메소드에 배열이 주어지면, 지정된 모든 값이 존재하는지 확인합니다.

    if ($request->has(['name', 'email'])) {
        //
    }

The `whenHas` method will execute the given closure if a value is present on the request:

`whenHas` 메소드는 요청에 값이 있으면 주어진 클로저를 실행합니다.

    $request->whenHas('name', function ($input) {
        //
    });

A second closure may be passed to the `whenHas` method that will be executed if the specified value is not present on the request:

두 번째 클로저는 요청에 지정된 값이 없으면 실행되는 `whenHas` 메서드로 전달될 수 있습니다.

    $request->whenHas('name', function ($input) {
        // The "name" value is present...
    }, function () {
        // The "name" value is not present...
    });

The `hasAny` method returns `true` if any of the specified values are present:

`hasAny` 메소드는 지정된 값이 존재하면 `true`를 반환합니다.

    if ($request->hasAny(['name', 'email'])) {
        //
    }

If you would like to determine if a value is present on the request and is not empty, you may use the `filled` method:

주어진 변수값이 현재 request 에 존재하고 비어 있지 않은 것을 확인하려면 `filled` 메소드를 사용하면 됩니다.

    if ($request->filled('name')) {
        //
    }

The `whenFilled` method will execute the given closure if a value is present on the request and is not empty:

`whenFilled` 메소드는 값이 요청에 있고 그 값이 비어 있지 않은 경우 주어진 클로저를 실행합니다.

    $request->whenFilled('name', function ($input) {
        //
    });

A second closure may be passed to the `whenFilled` method that will be executed if the specified value is not "filled":

두 번째 클로저는 지정된 값이 "채워지지" 않은 경우 실행되는 `whenFilled` 메서드로 전달될 수 있습니다.

    $request->whenFilled('name', function ($input) {
        // The "name" value is filled...
    }, function () {
        // The "name" value is not filled...
    });

To determine if a given key is absent from the request, you may use the `missing` method:

주어진 키가 request 에 없는지 확인하려면 `missing` 메소드를 사용할 수 있습니다.

    if ($request->missing('name')) {
        //
    }

<a name="merging-additional-input"></a>
### Merging Additional Input
### 추가 입력 병합

Sometimes you may need to manually merge additional input into the request's existing input data. To accomplish this, you may use the `merge` method:

때때로 추가 입력을 요청의 기존 입력 데이터에 수동으로 병합해야 할 수도 있습니다. 이를 위해 `merge` 메소드를 사용할 수 있습니다.

    $request->merge(['votes' => 0]);

The `mergeIfMissing` method may be used to merge input into the request if the corresponding keys do not already exist within the request's input data:

`mergeIfMissing` 메소드는 해당 키가 요청의 입력 데이터 내에 이미 존재하지 않는 경우, 입력을 요청에 병합하는 데 사용할 수 있습니다.

    $request->mergeIfMissing(['votes' => 0]);

<a name="old-input"></a>
### Old Input
### 이전 입력값 확인하기

Laravel allows you to keep input from one request during the next request. This feature is particularly useful for re-populating forms after detecting validation errors. However, if you are using Laravel's included [validation features](/docs/{{version}}/validation), it is possible that you will not need to manually use these session input flashing methods directly, as some of Laravel's built-in validation facilities will call them automatically.

라라벨은 한 request의 입력을 다음 request 중에도 유지할 수 있도록 해줍니다. 이 기능은 특히 유효성 검사 오류를 감지한 후 폼을 다시 채워 넣을 때 유용합니다. 하지만 라라벨에 포함된 [유효성 검사 기능](/docs/{{version}}/validation)를 이용한다면 몇몇 유효성 검사 기능들이 자동으로 이 기능을 호출하기 때문에 수동으로 이 메소드들을 사용해야 할 가능성은 낮습니다.

<a name="flashing-input-to-the-session"></a>
#### Flashing Input To The Session
#### 입력값을 세션에 임시 저장하기

The `flash` method on the `Illuminate\Http\Request` class will flash the current input to the [session](/docs/{{version}}/session) so that it is available during the user's next request to the application:

`Illuminate\Http\Request` 클래스의 `flash` 메소드는 현재의 입력들을 [세션](/docs/{{version}}/session)에 저장하여, 사용자의 다음 request 동안에도 값을 사용할 수 있게 해줍니다.

    $request->flash();

You may also use the `flashOnly` and `flashExcept` methods to flash a subset of the request data to the session. These methods are useful for keeping sensitive information such as passwords out of the session:

`flashOnly`와 `flashExcept` 메소드를 이용하여 request 데이터의 일부분을 세션에 임시 저장(flash)할 수 있습니다. 이 메소드들은 패스워드와 같은 민감한 정보들을 세션에 포함하지 않도록 하는데 유용합니다.

    $request->flashOnly(['username', 'email']);

    $request->flashExcept('password');

<a name="flashing-input-then-redirecting"></a>
#### Flashing Input Then Redirecting
#### 입력값을 임시저장한 후 리다이렉트하기

Since you often will want to flash input to the session and then redirect to the previous page, you may easily chain input flashing onto a redirect using the `withInput` method:

종종 입력값을 세션에 임시 저장 하고 이전 페이지로 리다이렉트 하기를 원할 수 있는데, `withInput` 메소드를 이용하여 리다이렉트시 입력값 임시 저장이 되도록 간단하게 메소드 체이닝할 수 있습니다.

    return redirect('form')->withInput();

    return redirect()->route('user.create')->withInput();

    return redirect('form')->withInput(
        $request->except('password')
    );

<a name="retrieving-old-input"></a>
#### Retrieving Old Input
#### 이전 입력값 조회하기

To retrieve flashed input from the previous request, invoke the `old` method on an instance of `Illuminate\Http\Request`. The `old` method will pull the previously flashed input data from the [session](/docs/{{version}}/session):

이전 요청에서 플래시 입력을 검색하려면 `Illuminate\Http\Request` 인스턴스에서 `old` 메서드를 호출하세요. `old` 메소드는 [세션](/docs/{{version}}/session)에서 이전에 플래시된 입력 데이터를 가져옵니다.

    $username = $request->old('username');

Laravel also provides a global `old` helper. If you are displaying old input within a [Blade template](/docs/{{version}}/blade), it is more convenient to use the `old` helper to repopulate the form. If no old input exists for the given field, `null` will be returned:

라라벨은 글로벌 `old` 헬퍼 함수도 제공합니다. [블레이드 템플릿](/docs/{{version}}/blade) 안에서 지난 입력값에서 양식을 다시 채우려면 `old` 헬퍼 함수를 사용하는 것이 보다 편리합니다. 주어진 필드에 대한 이전 입력값이 존재하지 않는다면, `null` 이 반환될 것입니다.

    <input type="text" name="username" value="{{ old('username') }}">

<a name="cookies"></a>
### Cookies
### 쿠키

<a name="retrieving-cookies-from-requests"></a>
#### Retrieving Cookies From Requests
#### Request 에서 쿠키 조회하기

All cookies created by the Laravel framework are encrypted and signed with an authentication code, meaning they will be considered invalid if they have been changed by the client. To retrieve a cookie value from the request, use the `cookie` method on an `Illuminate\Http\Request` instance:

라라벨 프레임워크에서 생성된 모든 쿠키는 인증 코드와 함께 암호화 됩니다. 이것은 쿠키 정보가 클라이언트에 의해 변경되었을 경우 유효하지 않은 것으로 간주됨을 의미합니다. Request에서 쿠키 값을 가져오기 위해서는 `Illuminate\Http\Request` 인스턴스에서 `cookie` 메소드를 사용하십시오.

    $value = $request->cookie('name');

<a name="input-trimming-and-normalization"></a>
## Input Trimming & Normalization
## 입력 Trimming & 표준회

By default, Laravel includes the `App\Http\Middleware\TrimStrings` and `App\Http\Middleware\ConvertEmptyStringsToNull` middleware in your application's global middleware stack. These middleware are listed in the global middleware stack by the `App\Http\Kernel` class. These middleware will automatically trim all incoming string fields on the request, as well as convert any empty string fields to `null`. This allows you to not have to worry about these normalization concerns in your routes and controllers.

기본적으로 라라벨은 애플리케이션의 글로벌 미들웨어 스택에 `App\Http\Middleware\TrimStrings` 및 `App\Http\Middleware\ConvertEmptyStringsToNull` 미들웨어를 포함합니다. 이런 미들웨어는 `App\Http\Kernel` 클래스에 의해 글로벌 미들웨어 스택에 나열됩니다. 이러한 미들웨어는 요청 시 들어오는 모든 문자열 필드를 자동으로 트리밍하고 빈 문자열 필드를 `null`로 변환합니다. 이를 통해 경로 및 컨트롤러에서 이러한 정규화 문제에 대해 걱정할 필요가 없습니다.

If you would like to disable this behavior, you may remove the two middleware from your application's middleware stack by removing them from the `$middleware` property of your `App\Http\Kernel` class.

비활성화하려면 `App\Http\Kernel` 클래스의 `$middleware` 속성에서 두 미들웨어를 제거하여 애플리케이션의 미들웨어 스택에서 두 미들웨어를 제거할 수 있습니다.

<a name="files"></a>
## Files
## 파일처리

<a name="retrieving-uploaded-files"></a>
### Retrieving Uploaded Files
### 업로드된 파일 조회하기

You may retrieve uploaded files from an `Illuminate\Http\Request` instance using the `file` method or using dynamic properties. The `file` method returns an instance of the `Illuminate\Http\UploadedFile` class, which extends the PHP `SplFileInfo` class and provides a variety of methods for interacting with the file:

`file` 메소드를 사용하거나 동적 속성을 사용하여 `Illuminate\Http\Request` 인스턴스에서 업로드된 파일을 검색할 수 있습니다. `file` 메소드는 PHP `SplFileInfo` 클래스를 확장하고 파일과 상호작용하기 위한 다양한 메소드를 제공하는 `Illuminate\Http\UploadedFile` 클래스의 인스턴스를 반환합니다.

    $file = $request->file('photo');

    $file = $request->photo;

You may determine if a file is present on the request using the `hasFile` method:

`hasFile` 메소드를 사용하면 request에 파일을 가지고 있는지 확인할 수 있습니다.

    if ($request->hasFile('photo')) {
        //
    }

<a name="validating-successful-uploads"></a>
#### Validating Successful Uploads
#### 성공적으로 업로드 되었는지 확인하기

In addition to checking if the file is present, you may verify that there were no problems uploading the file via the `isValid` method:

파일이 현재 존재하는지 확인하는데 더하여, `isValid` 메소드를 사용하여 업로드된 파일에 아무런 문제가 없는지 확인할 수 있습니다.

    if ($request->file('photo')->isValid()) {
        //
    }

<a name="file-paths-extensions"></a>
#### File Paths & Extensions
#### 파일 경로 & 확장자

The `UploadedFile` class also contains methods for accessing the file's fully-qualified path and its extension. The `extension` method will attempt to guess the file's extension based on its contents. This extension may be different from the extension that was supplied by the client:

`UploadedFile` 클래스는 파일의 전체 경로와 확장자에 엑세스 할 수 있는 메소드를 가지고 있습니다. `extension` 메소드는 그 내용에 따라 파일의 확장자를 추측하려고 시도합니다. 이 확장자는 클라이언트가 제공한 확장자와는 다를 수 있습니다.

    $path = $request->photo->path();

    $extension = $request->photo->extension();

<a name="other-file-methods"></a>
#### Other File Methods
#### 기타 파일 관련 메소드들

There are a variety of other methods available on `UploadedFile` instances. Check out the [API documentation for the class](https://github.com/symfony/symfony/blob/6.0/src/Symfony/Component/HttpFoundation/File/UploadedFile.php) for more information regarding these methods.

`UploadedFile` 인스턴스에 다양한 다른 메소드들이 제공되어 있습니다. 이 메소드들에 대해 더 많은 정보를 얻으려면 [클래스의 API documentation](https://github.com/symfony/symfony/blob/6.0/src/Symfony/Component/HttpFoundation/File/UploadedFile.php) 을 확인해보십시오.

<a name="storing-uploaded-files"></a>
### Storing Uploaded Files
### 업로드된 파일 저장하기

To store an uploaded file, you will typically use one of your configured [filesystems](/docs/{{version}}/filesystem). The `UploadedFile` class has a `store` method that will move an uploaded file to one of your disks, which may be a location on your local filesystem or a cloud storage location like Amazon S3.

업로드된 파일을 저장하려면, 일반적으로 설정된 [파일시스템](/docs/{{version}}/filesystem)중 하나를 사용합니다. `UploadedFile` 클래스는 업로드된 파일을 로컬 파일 시스템이나 아마존 S3 와 같은 클라우드 스토리지 디스크 중에 하나로 이동 시킬 수 있는 `store` 메소드를 가지고 있습니다.

The `store` method accepts the path where the file should be stored relative to the filesystem's configured root directory. This path should not contain a filename, since a unique ID will automatically be generated to serve as the filename.

`store` 메소드는 파일 시스템에 설정된 루트 디렉토리로 부터 파일이 어디에 저장되어야 할지에 대한 경로를 전달 받습니다. 파일의 이름은 자동으로 고유한 ID로 생성되므로 이 경로에는 파일 이름을 포함하지 않아야 합니다.

The `store` method also accepts an optional second argument for the name of the disk that should be used to store the file. The method will return the path of the file relative to the disk's root:

`store` 메소드는 파일이 저장되는데 사용될 디스크 이름을 두 번째 선택 인자로 허용합니다. 메소드는 디스크의 루트를 기준으로 파일의 경로를 반환합니다.

    $path = $request->photo->store('images');

    $path = $request->photo->store('images', 's3');

If you do not want a filename to be automatically generated, you may use the `storeAs` method, which accepts the path, filename, and disk name as its arguments:

파일 이름이 자동으로 생성되지 않기를 원한다면, 경로와, 파일이름 그리고 디스크 이름을 인자로 받아 들이는 `storeAs` 메소드를 사용할 수 있습니다.

    $path = $request->photo->storeAs('images', 'filename.jpg');

    $path = $request->photo->storeAs('images', 'filename.jpg', 's3');

> {tip} For more information about file storage in Laravel, check out the complete [file storage documentation](/docs/{{version}}/filesystem).

> {tip} 라라벨의 파일 스토리지에 대한 자세한 내용은 전체 [파일 스토리지 문서](/docs/{{version}}/filesystem)를 확인하세요.

<a name="configuring-trusted-proxies"></a>
## Configuring Trusted Proxies
## 신뢰할 수 있는 프록시 설정하기

When running your applications behind a load balancer that terminates TLS / SSL certificates, you may notice your application sometimes does not generate HTTPS links when using the `url` helper. Typically this is because your application is being forwarded traffic from your load balancer on port 80 and does not know it should generate secure links.

TLS / SSL 인증서가 적용된 로드 밸런서 뒤에서 애플리케이션을 실행할 때 애플리케이션에서 HTTPS 링크가 생성되지 않는 경우가 있습니다. 일반적으로 이는 애플리케이션이 포트 80의 로드 밸런서에서 전송되는 트래픽 뒤에 위치해서, `url` 헬퍼를 사용할 때 HTTPS 링크를 생성해야 한다는 것을 알지 못하기 때문입니다.

To solve this, you may use the `App\Http\Middleware\TrustProxies` middleware that is included in your Laravel application, which allows you to quickly customize the load balancers or proxies that should be trusted by your application. Your trusted proxies should be listed as an array on the `$proxies` property of this middleware. In addition to configuring the trusted proxies, you may configure the proxy `$headers` that should be trusted:

이 문제를 해결하기 위해서, 라라벨 애플리케이션에는 `App\Http\Middleware\TrustProxies` 미들웨어를 사용하여 손쉽게 애플리케이션에서 신뢰할 수 있는 로드밸런서 또는 프록시를 설정할 수 있습니다. 신뢰할 수 있는 프록시들은 이 미들웨어의 `$proxies` 속성 배열에 지정해놓으면 됩니다. 신뢰할 수 있는 프록시를 설정하는 것 이외에도 신뢰해야 하는 프록시 헤더를 설정 할 수 있습니다.

    <?php

    namespace App\Http\Middleware;

    use Illuminate\Http\Middleware\TrustProxies as Middleware;
    use Illuminate\Http\Request;

    class TrustProxies extends Middleware
    {
        /**
         * The trusted proxies for this application.
         *
         * @var string|array
         */
        protected $proxies = [
            '192.168.1.1',
            '192.168.1.2',
        ];

        /**
         * The headers that should be used to detect proxies.
         *
         * @var int
         */
        protected $headers = Request::HEADER_X_FORWARDED_FOR | Request::HEADER_X_FORWARDED_HOST | Request::HEADER_X_FORWARDED_PORT | Request::HEADER_X_FORWARDED_PROTO;
    }

> {tip} If you are using AWS Elastic Load Balancing, your `$headers` value should be `Request::HEADER_X_FORWARDED_AWS_ELB`. For more information on the constants that may be used in the `$headers` property, check out Symfony's documentation on [trusting proxies](https://symfony.com/doc/current/deployment/proxies.html).

> {팁} 만약 여러분이 AWS Elastic 로드 발란싱을 사용하고 있다면, 여러분의 `$headers` 값은 `Request::HEADER_X_FORWARDED_AWS_ELB`이 되어야 합니다. `$headers`에 사용될 수 있는 상수에 대한 다른 정보는, Symfony 문서의 [trusting proxies](https://symfony.com/doc/current/deployment/proxies.html)를 참고하십시오.

<a name="trusting-all-proxies"></a>
#### Trusting All Proxies
#### 모든 프록시 신뢰하기

If you are using Amazon AWS or another "cloud" load balancer provider, you may not know the IP addresses of your actual balancers. In this case, you may use `*` to trust all proxies:

아마존 AWS 또는 다른 "클라우드" 로드밸런서를 사용하는 경우에는, 실제 로드밸런서의 IP를 알 수가 없습니다. 이 경우, 모든 프록시를 신뢰할 수 있도록 하기 위해서 `*` 를 사용할 수 있습니다.

    /**
     * The trusted proxies for this application.
     *
     * @var string|array
     */
    protected $proxies = '*';

<a name="configuring-trusted-hosts"></a>
## Configuring Trusted Hosts
## 신뢰할 수 있는 호스트 설정

By default, Laravel will respond to all requests it receives regardless of the content of the HTTP request's `Host` header. In addition, the `Host` header's value will be used when generating absolute URLs to your application during a web request.

기본적으로 라라벨은 HTTP 요청의 `Host` 헤더 내용에 관계없이 수신하는 모든 요청에 응답합니다. 또한 웹 요청 중에 애플리케이션에 대한 절대 URL을 생성할 때 `Host` 헤더 값이 사용됩니다.

Typically, you should configure your web server, such as Nginx or Apache, to only send requests to your application that match a given host name. However, if you do not have the ability to customize your web server directly and need to instruct Laravel to only respond to certain host names, you may do so by enabling the `App\Http\Middleware\TrustHosts` middleware for your application.

일반적으로 지정된 호스트 이름과 일치하는 요청만 애플리케이션에 보내도록 Nginx 또는 Apache와 같은 웹 서버를 설정해야 합니다. 그러나 웹 서버를 직접 사용자 정의할 수 없고, 특정 호스트 이름에만 응답하도록 라라벨에 지시해야 하는 경우 애플리케이션에 대해 `App\Http\Middleware\TrustHosts` 미들웨어를 활성화하여 그렇게 할 수 있습니다.

The `TrustHosts` middleware is already included in the `$middleware` stack of your application; however, you should uncomment it so that it becomes active. Within this middleware's `hosts` method, you may specify the host names that your application should respond to. Incoming requests with other `Host` value headers will be rejected:

`TrustHosts` 미들웨어는 이미 애플리케이션의 `$middleware` 스택에 포함되어 있습니다. 그러나 활성화하려면 주석 처리를 제거해야 합니다. 이 미들웨어의 `hosts` 메소드 내에서 애플리케이션이 응답해야 하는 호스트 이름을 지정할 수 있습니다. 다른 `Host` 값 헤더가 있는 수신 요청은 거부됩니다.

    /**
     * Get the host patterns that should be trusted.
     *
     * @return array
     */
    public function hosts()
    {
        return [
            'laravel.test',
            $this->allSubdomainsOfApplicationUrl(),
        ];
    }

The `allSubdomainsOfApplicationUrl` helper method will return a regular expression matching all subdomains of your application's `app.url` configuration value. This helper method provides a convenient way to allow all of your application's subdomains when building an application that utilizes wildcard subdomains.

`allSubdomainsOfApplicationUrl` 도우미 메소드는 애플리케이션의 `app.url` 설정 값의 모든 하위 도메인과 일치하는 정규식을 반환합니다. 이 도우미 메서드는 와일드카드 하위 도메인을 사용하는 애플리케이션을 구축할 때 애플리케이션의 모든 하위 도메인을 허용하는 편리한 방법을 제공합니다.