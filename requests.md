# HTTP Requests

- [HTTP Requests](#http-requests)
  - [Introduction](#introduction)
  - [소개](#소개)
  - [Interacting With The Request](#interacting-with-the-request)
  - [요청과 상호작용](#요청과-상호작용)
    - [Accessing The Request](#accessing-the-request)
    - [요청에 접근](#요청에-접근)
      - [Dependency Injection \& Route Parameters](#dependency-injection--route-parameters)
      - [의존성 주입과 라우트 파라미터](#의존성-주입과-라우트-파라미터)
    - [Request Path, Host, \& Method](#request-path-host--method)
    - [요청 경로, 호스트, 메소드](#요청-경로-호스트-메소드)
      - [Retrieving The Request Path](#retrieving-the-request-path)
      - [요청 경로 가져오기](#요청-경로-가져오기)
      - [Inspecting The Request Path / Route](#inspecting-the-request-path--route)
      - [요청 경로 / 라우트 검사](#요청-경로--라우트-검사)
      - [Retrieving The Request URL](#retrieving-the-request-url)
      - [요청 URL 가져오기](#요청-url-가져오기)
      - [Retrieving The Request Host](#retrieving-the-request-host)
      - [요청 호스트 가져오기](#요청-호스트-가져오기)
      - [Retrieving The Request Method](#retrieving-the-request-method)
      - [요청 메소드 가져오기](#요청-메소드-가져오기)
    - [Request Headers](#request-headers)
    - [요청 헤더](#요청-헤더)
    - [Request IP Address](#request-ip-address)
    - [요청 IP 주소](#요청-ip-주소)
    - [Content Negotiation](#content-negotiation)
    - [콘텐츠 협상](#콘텐츠-협상)
    - [PSR-7 Requests](#psr-7-requests)
    - [PSR-7 요청](#psr-7-요청)
  - [Input](#input)
  - [입력](#입력)
    - [Retrieving Input](#retrieving-input)
    - [입력 가져오기](#입력-가져오기)
      - [Retrieving All Input Data](#retrieving-all-input-data)
      - [모든 입력 데이터 가져오기](#모든-입력-데이터-가져오기)
      - [Retrieving An Input Value](#retrieving-an-input-value)
      - [입력 값 가져오기](#입력-값-가져오기)
      - [Retrieving Input From The Query String](#retrieving-input-from-the-query-string)
      - [쿼리 문자열에서 입력 가져오기](#쿼리-문자열에서-입력-가져오기)
      - [Retrieving JSON Input Values](#retrieving-json-input-values)
      - [JSON 입력 값 가져오기](#json-입력-값-가져오기)
      - [Retrieving Stringable Input Values](#retrieving-stringable-input-values)
      - [Stringable 입력 값 가져오기](#stringable-입력-값-가져오기)
      - [Retrieving Boolean Input Values](#retrieving-boolean-input-values)
      - [Boolean 입력 값 가져오기](#boolean-입력-값-가져오기)
      - [Retrieving Date Input Values](#retrieving-date-input-values)
      - [날짜 입력 값 가져오기](#날짜-입력-값-가져오기)
      - [Retrieving Enum Input Values](#retrieving-enum-input-values)
      - [Enum 입력 값 가져오기](#enum-입력-값-가져오기)
      - [Retrieving Input Via Dynamic Properties](#retrieving-input-via-dynamic-properties)
      - [동적 속성을 통한 입력 가져오기](#동적-속성을-통한-입력-가져오기)
      - [Retrieving A Portion Of The Input Data](#retrieving-a-portion-of-the-input-data)
      - [입력 데이터의 일부 가져오기](#입력-데이터의-일부-가져오기)
    - [Determining If Input Is Present](#determining-if-input-is-present)
    - [입력이 있는지 확인](#입력이-있는지-확인)
    - [Merging Additional Input](#merging-additional-input)
    - [추가 입력 병합](#추가-입력-병합)
    - [Old Input](#old-input)
    - [이전 입력](#이전-입력)
      - [Flashing Input To The Session](#flashing-input-to-the-session)
      - [세션에 입력 플래싱](#세션에-입력-플래싱)
      - [Flashing Input Then Redirecting](#flashing-input-then-redirecting)
      - [입력 플래싱 후 리디렉션](#입력-플래싱-후-리디렉션)
      - [Retrieving Old Input](#retrieving-old-input)
      - [이전 입력 가져오기](#이전-입력-가져오기)
    - [Cookies](#cookies)
    - [쿠키](#쿠키)
      - [Retrieving Cookies From Requests](#retrieving-cookies-from-requests)
      - [요청에서 쿠키 가져오기](#요청에서-쿠키-가져오기)
  - [Input Trimming \& Normalization](#input-trimming--normalization)
  - [입력 자르기 \& 정규화](#입력-자르기--정규화)
      - [Disabling Input Normalization](#disabling-input-normalization)
      - [입력 정규화 비활성화](#입력-정규화-비활성화)
  - [Files](#files)
  - [파일](#파일)
    - [Retrieving Uploaded Files](#retrieving-uploaded-files)
    - [업로드된 파일 가져오기](#업로드된-파일-가져오기)
      - [Validating Successful Uploads](#validating-successful-uploads)
      - [성공적인 업로드 유효성 검사](#성공적인-업로드-유효성-검사)
      - [File Paths \& Extensions](#file-paths--extensions)
      - [파일 경로 \& 확장자](#파일-경로--확장자)
      - [Other File Methods](#other-file-methods)
      - [다른 파일 메소드](#다른-파일-메소드)
    - [Storing Uploaded Files](#storing-uploaded-files)
    - [업로드된 파일 저장](#업로드된-파일-저장)
  - [Configuring Trusted Proxies](#configuring-trusted-proxies)
  - [신뢰할 수 있는 프록시 설정](#신뢰할-수-있는-프록시-설정)
      - [Trusting All Proxies](#trusting-all-proxies)
      - [모든 프록시 신뢰](#모든-프록시-신뢰)
  - [Configuring Trusted Hosts](#configuring-trusted-hosts)
  - [신뢰할 수 있는 호스트 설정](#신뢰할-수-있는-호스트-설정)

<a name="introduction"></a>
## Introduction
## 소개

Laravel's `Illuminate\Http\Request` class provides an object-oriented way to interact with the current HTTP request being handled by your application as well as retrieve the input, cookies, and files that were submitted with the request.

Laravel의 `Illuminate\Http\Request` 클래스는 현재 애플리케이션에서 처리되는 HTTP 요청과 상호작용하고 요청에 함께 제출된 입력, 쿠키 및 파일을 검색하는 객체 지향적인 방법을 제공합니다.

<a name="interacting-with-the-request"></a>
## Interacting With The Request
## 요청과 상호작용

<a name="accessing-the-request"></a>
### Accessing The Request
### 요청에 접근

To obtain an instance of the current HTTP request via dependency injection, you should type-hint the `Illuminate\Http\Request` class on your route closure or controller method. The incoming request instance will automatically be injected by the Laravel [service container](/docs/{{version}}/container):

종속성 주입을 통해 현재 HTTP 요청의 인스턴스를 얻으려면 라우트 클로저 또는 컨트롤러 메소드에 `Illuminate\Http\Request` 클래스를 타입 힌트해야 합니다. Laravel [서비스 컨테이너](/docs/{{version}}/container)가 자동으로 주입합니다:

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\RedirectResponse;
    use Illuminate\Http\Request;

    class UserController extends Controller
    {
        /**
         * Store a new user.
         */
        public function store(Request $request): RedirectResponse
        {
            $name = $request->input('name');

            // Store the user...

            return redirect('/users');
        }
    }

As mentioned, you may also type-hint the `Illuminate\Http\Request` class on a route closure. The service container will automatically inject the incoming request into the closure when it is executed:

위에서 언급했듯이 라우트 클로저에 `Illuminate\Http\Request` 클래스를 타입 힌트할 수도 있습니다. 서비스 컨테이너는 실행될 때 자동으로 클로저에 들어오는 요청을 주입합니다:

    use Illuminate\Http\Request;

    Route::get('/', function (Request $request) {
        // ...
    });

<a name="dependency-injection-route-parameters"></a>
#### Dependency Injection & Route Parameters
#### 의존성 주입과 라우트 파라미터

If your controller method is also expecting input from a route parameter you should list your route parameters after your other dependencies. For example, if your route is defined like so:

컨트롤러 메소드가 라우트 파라미터에서 입력을 기대하는 경우 다른 의존성 뒤에 라우트 파라미터를 나열해야 합니다. 예를 들어 라우트가 다음과 같이 정의되어 있다고 가정합시다:

    use App\Http\Controllers\UserController;

    Route::put('/user/{id}', [UserController::class, 'update']);

You may still type-hint the `Illuminate\Http\Request` and access your `id` route parameter by defining your controller method as follows:

여전히 `Illuminate\Http\Request`를 타입 힌트하고 다음과 같이 컨트롤러 메소드를 정의하여 `id` 라우트 파라미터에 액세스할 수 있습니다:

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\RedirectResponse;
    use Illuminate\Http\Request;

    class UserController extends Controller
    {
        /**
         * Update the specified user.
         */
        public function update(Request $request, string $id): RedirectResponse
        {
            // Update the user...

            return redirect('/users');
        }
    }

<a name="request-path-and-method"></a>
### Request Path, Host, & Method
### 요청 경로, 호스트, 메소드

The `Illuminate\Http\Request` instance provides a variety of methods for examining the incoming HTTP request and extends the `Symfony\Component\HttpFoundation\Request` class. We will discuss a few of the most important methods below.

`Illuminate\Http\Request` 인스턴스는 들어오는 HTTP 요청을 검사하는 다양한 메소드를 제공하며 `Symfony\Component\HttpFoundation\Request` 클래스를 확장합니다. 아래에서 가장 중요한 몇 가지 메소드에 대해 설명합니다.

<a name="retrieving-the-request-path"></a>
#### Retrieving The Request Path
#### 요청 경로 가져오기

The `path` method returns the request's path information. So, if the incoming request is targeted at `http://example.com/foo/bar`, the `path` method will return `foo/bar`:

`path` 메소드는 요청의 경로 정보를 반환합니다. 따라서 들어오는 요청이 `http://example.com/foo/bar`에 대해 타겟팅되면 `path` 메소드는 `foo/bar`를 반환합니다:

    $uri = $request->path();

<a name="inspecting-the-request-path"></a>
#### Inspecting The Request Path / Route
#### 요청 경로 / 라우트 검사

The `is` method allows you to verify that the incoming request path matches a given pattern. You may use the `*` character as a wildcard when utilizing this method:

`is` 메소드를 사용하면 들어오는 요청 경로가 주어진 패턴과 일치하는지 확인할 수 있습니다. 이 메소드를 사용할 때 `*` 문자를 와일드카드로 사용할 수 있습니다:

    if ($request->is('admin/*')) {
        // ...
    }

Using the `routeIs` method, you may determine if the incoming request has matched a [named route](/docs/{{version}}/routing#named-routes):

`routeIs` 메소드를 사용하여 들어오는 요청이 [이름을 가진 라우트](/docs/{{version}}/routing#named-routes)와 일치하는지 확인할 수 있습니다:

    if ($request->routeIs('admin.*')) {
        // ...
    }

<a name="retrieving-the-request-url"></a>
#### Retrieving The Request URL
#### 요청 URL 가져오기

To retrieve the full URL for the incoming request you may use the `url` or `fullUrl` methods. The `url` method will return the URL without the query string, while the `fullUrl` method includes the query string:

들어오는 요청의 전체 URL을 가져오려면 `url` 또는 `fullUrl` 메소드를 사용할 수 있습니다. `url` 메소드는 쿼리 문자열 없이 URL을 반환하고 `fullUrl` 메소드는 쿼리 문자열을 포함합니다:

    $url = $request->url();

    $urlWithQueryString = $request->fullUrl();

If you would like to append query string data to the current URL, you may call the `fullUrlWithQuery` method. This method merges the given array of query string variables with the current query string:

현재 URL에 쿼리 문자열 데이터를 추가하려면 `fullUrlWithQuery` 메소드를 호출할 수 있습니다. 이 메소드는 쿼리 문자열 변수 배열을 현재 쿼리 문자열과 병합합니다:

    $request->fullUrlWithQuery(['type' => 'phone']);

<a name="retrieving-the-request-host"></a>
#### Retrieving The Request Host
#### 요청 호스트 가져오기

You may retrieve the "host" of the incoming request via the `host`, `httpHost`, and `schemeAndHttpHost` methods:

`host`, `httpHost`, `schemeAndHttpHost` 메소드를 사용하여 들어오는 요청의 "호스트"를 가져올 수 있습니다:

    $request->host();
    $request->httpHost();
    $request->schemeAndHttpHost();

<a name="retrieving-the-request-method"></a>
#### Retrieving The Request Method
#### 요청 메소드 가져오기

The `method` method will return the HTTP verb for the request. You may use the `isMethod` method to verify that the HTTP verb matches a given string:

`method` 메소드는 요청의 HTTP 동사를 반환합니다. `isMethod` 메소드를 사용하여 HTTP 동사가 주어진 문자열과 일치하는지 확인할 수 있습니다:

    $method = $request->method();

    if ($request->isMethod('post')) {
        // ...
    }

<a name="request-headers"></a>
### Request Headers
### 요청 헤더

You may retrieve a request header from the `Illuminate\Http\Request` instance using the `header` method. If the header is not present on the request, `null` will be returned. However, the `header` method accepts an optional second argument that will be returned if the header is not present on the request:

`Illuminate\Http\Request` 인스턴스에서 `header` 메소드를 사용하여 요청 헤더를 검색할 수 있습니다. 요청에 헤더가 없으면 `null`이 반환됩니다. 그러나 `header` 메소드는 요청에 헤더가 없으면 반환할 선택적 두 번째 인수를 허용합니다:

    $value = $request->header('X-Header-Name');

    $value = $request->header('X-Header-Name', 'default');

The `hasHeader` method may be used to determine if the request contains a given header:

`hasHeader` 메소드를 사용하여 요청에 특정 헤더가 있는지 확인할 수 있습니다:

    if ($request->hasHeader('X-Header-Name')) {
        // ...
    }

For convenience, the `bearerToken` method may be used to retrieve a bearer token from the `Authorization` header. If no such header is present, an empty string will be returned:

편의를 위해, `bearerToken` 메소드를 사용하여 `Authorization` 헤더에서 bearer 토큰을 검색할 수 있습니다. 헤더가 없으면 빈 문자열이 반환됩니다:

    $token = $request->bearerToken();

<a name="request-ip-address"></a>
### Request IP Address
### 요청 IP 주소

The `ip` method may be used to retrieve the IP address of the client that made the request to your application:

`ip` 메소드를 사용하여 애플리케이션에 요청을 보낸 클라이언트의 IP 주소를 검색할 수 있습니다:

    $ipAddress = $request->ip();

<a name="content-negotiation"></a>
### Content Negotiation
### 콘텐츠 협상

Laravel provides several methods for inspecting the incoming request's requested content types via the `Accept` header. First, the `getAcceptableContentTypes` method will return an array containing all of the content types accepted by the request:

Laravel은 `Accept` 헤더를 통해 들어오는 요청의 요청 콘텐츠 유형을 검사하는 여러 가지 방법을 제공합니다. 먼저, `getAcceptableContentTypes` 메소드는 요청에 의해 허용된 모든 콘텐츠 유형을 포함하는 배열을 반환합니다:

    $contentTypes = $request->getAcceptableContentTypes();

The `accepts` method accepts an array of content types and returns `true` if any of the content types are accepted by the request. Otherwise, `false` will be returned:

`accepts` 메소드는 콘텐츠 유형 배열을 허용하고 요청에 의해 허용된 콘텐츠 유형 중 하나라도 있으면 `true`를 반환합니다. 그렇지 않으면 `false`가 반환됩니다:

    if ($request->accepts(['text/html', 'application/json'])) {
        // ...
    }

You may use the `prefers` method to determine which content type out of a given array of content types is most preferred by the request. If none of the provided content types are accepted by the request, `null` will be returned:

`prefers` 메소드를 사용하여 요청에 의해 가장 선호되는 콘텐츠 유형 배열 중 하나를 결정할 수 있습니다. 요청에 의해 제공된 콘텐츠 유형 중 하나도 허용되지 않으면 `null`이 반환됩니다:

    $preferred = $request->prefers(['text/html', 'application/json']);

Since many applications only serve HTML or JSON, you may use the `expectsJson` method to quickly determine if the incoming request expects a JSON response:

많은 애플리케이션은 HTML 또는 JSON만 제공하므로, `expectsJson` 메소드를 사용하여 들어오는 요청이 JSON 응답을 기대하는지 빠르게 확인할 수 있습니다:

    if ($request->expectsJson()) {
        // ...
    }

<a name="psr7-requests"></a>
### PSR-7 Requests
### PSR-7 요청

The [PSR-7 standard](https://www.php-fig.org/psr/psr-7/) specifies interfaces for HTTP messages, including requests and responses. If you would like to obtain an instance of a PSR-7 request instead of a Laravel request, you will first need to install a few libraries. Laravel uses the *Symfony HTTP Message Bridge* component to convert typical Laravel requests and responses into PSR-7 compatible implementations:

[PSR-7 표준](https://www.php-fig.org/psr/psr-7/)은 요청 및 응답을 포함한 HTTP 메시지에 대한 인터페이스를 지정합니다. Laravel 요청 대신 PSR-7 요청 인스턴스를 얻으려면 먼저 몇 가지 라이브러리를 설치해야 합니다. Laravel은 일반적인 Laravel 요청 및 응답을 PSR-7 호환 구현으로 변환하는 데 *Symfony HTTP Message Bridge* 구성 요소를 사용합니다:

```shell
composer require symfony/psr-http-message-bridge
composer require nyholm/psr7
```

Once you have installed these libraries, you may obtain a PSR-7 request by type-hinting the request interface on your route closure or controller method:

이러한 라이브러리를 설치하면 라우트 클로저 또는 컨트롤러 메소드에서 요청 인터페이스를 타입 힌트하여 PSR-7 요청을 얻을 수 있습니다:

    use Psr\Http\Message\ServerRequestInterface;

    Route::get('/', function (ServerRequestInterface $request) {
        // ...
    });

> **Note**  
> If you return a PSR-7 response instance from a route or controller, it will automatically be converted back to a Laravel response instance and be displayed by the framework.
>
> **참고**
> 라우트 또는 컨트롤러에서 PSR-7 응답 인스턴스를 반환하면 프레임워크에 의해 자동으로 다시 Laravel 응답 인스턴스로 변환되어 표시됩니다.

<a name="input"></a>
## Input
## 입력

<a name="retrieving-input"></a>
### Retrieving Input
### 입력 가져오기

<a name="retrieving-all-input-data"></a>
#### Retrieving All Input Data
#### 모든 입력 데이터 가져오기

You may retrieve all of the incoming request's input data as an `array` using the `all` method. This method may be used regardless of whether the incoming request is from an HTML form or is an XHR request:

`all` 메소드를 사용하여 들어오는 요청의 모든 입력 데이터를 `array`로 가져올 수 있습니다. 이 메소드는 들어오는 요청이 HTML 양식에서 오는지 XHR 요청에서 오는지에 관계없이 사용할 수 있습니다:    

    $input = $request->all();

Using the `collect` method, you may retrieve all of the incoming request's input data as a [collection](/docs/{{version}}/collections):

`collect` 메소드를 사용하여 들어오는 요청의 모든 입력 데이터를 [컬렉션](/docs/{{version}}/collections)으로 가져올 수 있습니다:

    $input = $request->collect();

The `collect` method also allows you to retrieve a subset of the incoming request input as a collection:

`collect` 메소드는 또한 들어오는 요청 입력의 하위 집합을 컬렉션으로 가져올 수 있습니다:

    $request->collect('users')->each(function (string $user) {
        // ...
    });

<a name="retrieving-an-input-value"></a>
#### Retrieving An Input Value
#### 입력 값 가져오기

Using a few simple methods, you may access all of the user input from your `Illuminate\Http\Request` instance without worrying about which HTTP verb was used for the request. Regardless of the HTTP verb, the `input` method may be used to retrieve user input:

몇 가지 간단한 메소드를 사용하면 `Illuminate\Http\Request` 인스턴스에서 사용자 입력을 가져올 수 있으므로 요청에 사용된 HTTP 동사에 대해 걱정할 필요가 없습니다. HTTP 동사와 관계없이 `input` 메소드를 사용하여 사용자 입력을 가져올 수 있습니다:

    $name = $request->input('name');

You may pass a default value as the second argument to the `input` method. This value will be returned if the requested input value is not present on the request:

`input` 메소드의 두 번째 인수로 기본값을 전달할 수 있습니다. 요청에 요청된 입력 값이 없는 경우 이 값이 반환됩니다:

    $name = $request->input('name', 'Sally');

When working with forms that contain array inputs, use "dot" notation to access the arrays:

배열 입력을 포함하는 양식을 작업할 때는 배열에 액세스하려면 "dot" 표기법을 사용합니다:

    $name = $request->input('products.0.name');

    $names = $request->input('products.*.name');

You may call the `input` method without any arguments in order to retrieve all of the input values as an associative array:

입력 값을 연관 배열로 가져오려면 `input` 메소드를 인수 없이 호출할 수 있습니다:

    $input = $request->input();

<a name="retrieving-input-from-the-query-string"></a>
#### Retrieving Input From The Query String
#### 쿼리 문자열에서 입력 가져오기

While the `input` method retrieves values from the entire request payload (including the query string), the `query` method will only retrieve values from the query string:

`input` 메소드는 전체 요청 페이로드(쿼리 문자열 포함)에서 값을 가져오지만 `query` 메소드는 쿼리 문자열에서만 값을 가져옵니다:

    $name = $request->query('name');

If the requested query string value data is not present, the second argument to this method will be returned:

요청된 쿼리 문자열 값 데이터가 없으면 이 메소드의 두 번째 인수가 반환됩니다:

    $name = $request->query('name', 'Helen');

You may call the `query` method without any arguments in order to retrieve all of the query string values as an associative array:

쿼리 문자열 값을 연관 배열로 가져오려면 `query` 메소드를 인수 없이 호출할 수 있습니다:

    $query = $request->query();

<a name="retrieving-json-input-values"></a>
#### Retrieving JSON Input Values
#### JSON 입력 값 가져오기

When sending JSON requests to your application, you may access the JSON data via the `input` method as long as the `Content-Type` header of the request is properly set to `application/json`. You may even use "dot" syntax to retrieve values that are nested within JSON arrays / objects:

JSON 요청을 애플리케이션에 보낼 때 요청의 `Content-Type` 헤더가 올바르게 `application/json`으로 설정되어 있는 한 `input` 메소드를 통해 JSON 데이터에 액세스할 수 있습니다. JSON 배열 / 객체 내에 중첩된 값을 가져오려면 "dot" 구문을 사용할 수도 있습니다:

    $name = $request->input('user.name');

<a name="retrieving-stringable-input-values"></a>
#### Retrieving Stringable Input Values
#### Stringable 입력 값 가져오기

Instead of retrieving the request's input data as a primitive `string`, you may use the `string` method to retrieve the request data as an instance of [`Illuminate\Support\Stringable`](/docs/{{version}}/helpers#fluent-strings):

요청의 입력 데이터를 원시 `string`으로 가져오는 대신 `string` 메소드를 사용하여 요청 데이터를 [`Illuminate\Support\Stringable`](/docs/{{version}}/helpers#fluent-strings) 인스턴스로 가져올 수 있습니다:

    $name = $request->string('name')->trim();

<a name="retrieving-boolean-input-values"></a>
#### Retrieving Boolean Input Values
#### Boolean 입력 값 가져오기

When dealing with HTML elements like checkboxes, your application may receive "truthy" values that are actually strings. For example, "true" or "on". For convenience, you may use the `boolean` method to retrieve these values as booleans. The `boolean` method returns `true` for 1, "1", true, "true", "on", and "yes". All other values will return `false`:

체크박스와 같은 HTML 요소를 처리할 때 애플리케이션은 실제로 문자열인 "truthy" 값을 수신할 수 있습니다. 예를 들어 "true" 또는 "on"입니다. 편의를 위해 `boolean` 메소드를 사용하여 이러한 값을 불리언으로 가져올 수 있습니다. `boolean` 메소드는 1, "1", true, "true", "on", "yes"에 대해 true를 반환합니다. 모든 다른 값은 false를 반환합니다:

    $archived = $request->boolean('archived');

<a name="retrieving-date-input-values"></a>
#### Retrieving Date Input Values
#### 날짜 입력 값 가져오기

For convenience, input values containing dates / times may be retrieved as Carbon instances using the `date` method. If the request does not contain an input value with the given name, `null` will be returned:

편의를 위해 날짜 / 시간을 포함하는 입력 값을 `date` 메소드를 사용하여 Carbon 인스턴스로 가져올 수 있습니다. 요청에 지정된 이름의 입력 값이 없는 경우 null이 반환됩니다:

    $birthday = $request->date('birthday');

The second and third arguments accepted by the `date` method may be used to specify the date's format and timezone, respectively:

`date` 메소드가 받는 두 번째 및 세 번째 인수는 날짜의 형식 및 시간대를 각각 지정하는 데 사용할 수 있습니다:

    $elapsed = $request->date('elapsed', '!H:i', 'Europe/Madrid');

If the input value is present but has an invalid format, an `InvalidArgumentException` will be thrown; therefore, it is recommended that you validate the input before invoking the `date` method.

입력 값이 존재하지만 잘못된 형식인 경우 `InvalidArgumentException`이 발생하므로 `date` 메소드를 호출하기 전에 입력을 유효성 검사하는 것이 좋습니다.

<a name="retrieving-enum-input-values"></a>
#### Retrieving Enum Input Values
#### Enum 입력 값 가져오기

Input values that correspond to [PHP enums](https://www.php.net/manual/en/language.types.enumerations.php) may also be retrieved from the request. If the request does not contain an input value with the given name or the enum does not have a backing value that matches the input value, `null` will be returned. The `enum` method accepts the name of the input value and the enum class as its first and second arguments:

[PHP 열거형](https://www.php.net/manual/en/language.types.enumerations.php)에 해당하는 입력 값도 요청에서 가져올 수 있습니다. 요청에 지정된 이름의 입력 값이 없거나 열거형에 입력 값과 일치하는 백업 값이 없는 경우 null이 반환됩니다. `enum` 메소드는 입력 값의 이름과 열거형 클래스를 첫 번째 및 두 번째 인수로 받습니다:

    use App\Enums\Status;

    $status = $request->enum('status', Status::class);

<a name="retrieving-input-via-dynamic-properties"></a>
#### Retrieving Input Via Dynamic Properties
#### 동적 속성을 통한 입력 가져오기

You may also access user input using dynamic properties on the `Illuminate\Http\Request` instance. For example, if one of your application's forms contains a `name` field, you may access the value of the field like so:

`Illuminate\Http\Request` 인스턴스의 동적 속성을 사용하여 사용자 입력에도 액세스할 수 있습니다. 예를 들어 애플리케이션의 양식 중 하나에 `name` 필드가 포함되어 있으면 다음과 같이 필드의 값을 액세스할 수 있습니다:

    $name = $request->name;

When using dynamic properties, Laravel will first look for the parameter's value in the request payload. If it is not present, Laravel will search for the field in the matched route's parameters.

동적 속성을 사용할 때 Laravel은 먼저 요청 페이로드에서 매개 변수의 값을 찾습니다. 그렇지 않으면 Laravel은 일치하는 경로의 매개 변수에서 필드를 검색합니다.

<a name="retrieving-a-portion-of-the-input-data"></a>
#### Retrieving A Portion Of The Input Data
#### 입력 데이터의 일부 가져오기

If you need to retrieve a subset of the input data, you may use the `only` and `except` methods. Both of these methods accept a single `array` or a dynamic list of arguments:

입력 데이터의 하위 집합을 검색해야 하는 경우 `only` 및 `except` 메소드를 사용할 수 있습니다. 이 두 메소드는 단일 `array` 또는 동적 인수 목록을 받습니다:

    $input = $request->only(['username', 'password']);

    $input = $request->only('username', 'password');

    $input = $request->except(['credit_card']);

    $input = $request->except('credit_card');

> **Warning**  
> The `only` method returns all of the key / value pairs that you request; however, it will not return key / value pairs that are not present on the request.
>
> **경고**
> `only` 메소드는 요청한 모든 키 / 값 쌍을 반환하지만 요청에 존재하지 않는 키 / 값 쌍은 반환하지 않습니다.

<a name="determining-if-input-is-present"></a>
### Determining If Input Is Present
### 입력이 있는지 확인

You may use the `has` method to determine if a value is present on the request. The `has` method returns `true` if the value is present on the request:

`has` 메소드를 사용하여 요청에 값이 있는지 확인할 수 있습니다. 값이 요청에 있으면 `has` 메소드는 true를 반환합니다:

    if ($request->has('name')) {
        // ...
    }

When given an array, the `has` method will determine if all of the specified values are present:

배열을 전달하면 `has` 메소드는 지정된 모든 값이 존재하는지 확인합니다:

    if ($request->has(['name', 'email'])) {
        // ...
    }

The `whenHas` method will execute the given closure if a value is present on the request:

`whenHas` 메소드는 요청에 값이 있으면 주어진 클로저를 실행합니다:

    $request->whenHas('name', function (string $input) {
        // ...
    });

A second closure may be passed to the `whenHas` method that will be executed if the specified value is not present on the request:

`whenHas` 메소드에 두 번째 클로저를 전달하여 지정된 값이 요청에 없는 경우 실행할 수 있습니다:

    $request->whenHas('name', function (string $input) {
        // The "name" value is present...
    }, function () {
        // The "name" value is not present...
    });

The `hasAny` method returns `true` if any of the specified values are present:

`hasAny` 메소드는 지정된 값 중 하나라도 존재하면 true를 반환합니다:

    if ($request->hasAny(['name', 'email'])) {
        // ...
    }

If you would like to determine if a value is present on the request and is not an empty string, you may use the `filled` method:

요청에 값이 있고 빈 문자열이 아닌지 확인하려면 `filled` 메소드를 사용할 수 있습니다:

    if ($request->filled('name')) {
        // ...
    }

The `whenFilled` method will execute the given closure if a value is present on the request and is not an empty string:

`whenFilled` 메소드는 요청에 값이 있고 빈 문자열이 아닌 경우 주어진 클로저를 실행합니다:

    $request->whenFilled('name', function (string $input) {
        // ...
    });

A second closure may be passed to the `whenFilled` method that will be executed if the specified value is not "filled":

`whenFilled` 메소드에 두 번째 클로저를 전달하여 지정된 값이 "filled"가 아닌 경우 실행할 수 있습니다:

    $request->whenFilled('name', function (string $input) {
        // The "name" value is filled...
    }, function () {
        // The "name" value is not filled...
    });

To determine if a given key is absent from the request, you may use the `missing` and `whenMissing` methods:

주어진 키가 요청에서 누락되었는지 확인하려면 `missing` 및 `whenMissing` 메소드를 사용할 수 있습니다:

    if ($request->missing('name')) {
        // ...
    }

    $request->whenMissing('name', function (array $input) {
        // The "name" value is missing...
    }, function () {
        // The "name" value is present...
    });

<a name="merging-additional-input"></a>
### Merging Additional Input
### 추가 입력 병합

Sometimes you may need to manually merge additional input into the request's existing input data. To accomplish this, you may use the `merge` method. If a given input key already exists on the request, it will be overwritten by the data provided to the `merge` method:

가끔은 요청의 기존 입력 데이터에 수동으로 추가 입력을 병합해야 할 수도 있습니다. 이를 수행하려면 `merge` 메소드를 사용할 수 있습니다. 주어진 입력 키가 요청에 이미 존재하는 경우 `merge` 메소드에 제공된 데이터로 덮어씌워집니다:

    $request->merge(['votes' => 0]);

The `mergeIfMissing` method may be used to merge input into the request if the corresponding keys do not already exist within the request's input data:

`mergeIfMissing` 메소드는 해당 키가 요청의 입력 데이터에 이미 존재하지 않는 경우 입력을 요청에 병합할 수 있습니다:

    $request->mergeIfMissing(['votes' => 0]);

<a name="old-input"></a>
### Old Input
### 이전 입력

Laravel allows you to keep input from one request during the next request. This feature is particularly useful for re-populating forms after detecting validation errors. However, if you are using Laravel's included [validation features](/docs/{{version}}/validation), it is possible that you will not need to manually use these session input flashing methods directly, as some of Laravel's built-in validation facilities will call them automatically.

Laravel은 다음 요청 동안 한 요청의 입력을 유지할 수 있습니다. 이 기능은 유효성 검사 오류를 감지한 후 폼을 다시 채우는 데 특히 유용합니다. 그러나 Laravel의 [유효성 검사 기능](/docs/{{version}}/validation)을 사용하는 경우 이 세션 입력 플래싱 메소드를 직접 사용할 필요가 없을 수 있습니다. Laravel의 내장 유효성 검사 기능 중 일부는 자동으로 이러한 메소드를 호출하기 때문입니다.

<a name="flashing-input-to-the-session"></a>
#### Flashing Input To The Session
#### 세션에 입력 플래싱

The `flash` method on the `Illuminate\Http\Request` class will flash the current input to the [session](/docs/{{version}}/session) so that it is available during the user's next request to the application:

`Illuminate\Http\Request` 클래스의 `flash` 메소드는 현재 입력을 [세션](/docs/{{version}}/session)에 플래싱하여 사용자가 응용 프로그램에 대한 다음 요청 동안 사용할 수 있도록 합니다:

    $request->flash();

You may also use the `flashOnly` and `flashExcept` methods to flash a subset of the request data to the session. These methods are useful for keeping sensitive information such as passwords out of the session:

또한 `flashOnly` 및 `flashExcept` 메소드를 사용하여 요청 데이터의 하위 집합을 세션에 플래싱할 수 있습니다. 이러한 메소드는 비밀번호와 같은 민감한 정보를 세션에서 유지하는 데 유용합니다:

    $request->flashOnly(['username', 'email']);

    $request->flashExcept('password');

<a name="flashing-input-then-redirecting"></a>
#### Flashing Input Then Redirecting
#### 입력 플래싱 후 리디렉션

Since you often will want to flash input to the session and then redirect to the previous page, you may easily chain input flashing onto a redirect using the `withInput` method:

세션에 입력을 플래싱하고 이전 페이지로 리디렉션하려면 `withInput` 메소드를 사용하여 입력 플래싱을 리디렉션에 연결할 수 있습니다:

    return redirect('form')->withInput();

    return redirect()->route('user.create')->withInput();

    return redirect('form')->withInput(
        $request->except('password')
    );

<a name="retrieving-old-input"></a>
#### Retrieving Old Input
#### 이전 입력 가져오기

To retrieve flashed input from the previous request, invoke the `old` method on an instance of `Illuminate\Http\Request`. The `old` method will pull the previously flashed input data from the [session](/docs/{{version}}/session):

이전 요청에서 플래시 된 입력을 검색하려면 `Illuminate\Http\Request`의 인스턴스에서 `old` 메소드를 호출합니다. `old` 메소드는 [세션](/docs/{{version}}/session)에서 이전에 플래시 된 입력 데이터를 가져옵니다:

    $username = $request->old('username');

Laravel also provides a global `old` helper. If you are displaying old input within a [Blade template](/docs/{{version}}/blade), it is more convenient to use the `old` helper to repopulate the form. If no old input exists for the given field, `null` will be returned:

Laravel은 전역 `old` 헬퍼도 제공합니다. [Blade 템플릿](/docs/{{version}}/blade) 내에서 이전 입력을 표시하는 경우 폼을 다시 채우기 위해 `old` 헬퍼를 사용하는 것이 더 편리합니다. 주어진 필드에 이전 입력이 없으면 `null`이 반환됩니다:

    <input type="text" name="username" value="{{ old('username') }}">

<a name="cookies"></a>
### Cookies
### 쿠키

<a name="retrieving-cookies-from-requests"></a>
#### Retrieving Cookies From Requests
#### 요청에서 쿠키 가져오기

All cookies created by the Laravel framework are encrypted and signed with an authentication code, meaning they will be considered invalid if they have been changed by the client. To retrieve a cookie value from the request, use the `cookie` method on an `Illuminate\Http\Request` instance:

Laravel 프레임워크가 생성한 모든 쿠키는 인증 코드로 암호화되고 서명되므로 클라이언트가 변경한 경우 유효하지 않다고 간주됩니다. 요청에서 쿠키 값을 검색하려면 `Illuminate\Http\Request` 인스턴스의 `cookie` 메소드를 사용합니다:

    $value = $request->cookie('name');

<a name="input-trimming-and-normalization"></a>
## Input Trimming & Normalization
## 입력 자르기 & 정규화

By default, Laravel includes the `App\Http\Middleware\TrimStrings` and `Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull` middleware in your application's global middleware stack. These middleware are listed in the global middleware stack by the `App\Http\Kernel` class. These middleware will automatically trim all incoming string fields on the request, as well as convert any empty string fields to `null`. This allows you to not have to worry about these normalization concerns in your routes and controllers.

기본적으로 Laravel은 응용 프로그램의 전역 미들웨어 스택에 `App\Http\Middleware\TrimStrings` 및 `Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull` 미들웨어를 포함합니다. 이러한 미들웨어는 `App\Http\Kernel` 클래스에 의해 전역 미들웨어 스택에 나열됩니다. 이러한 미들웨어는 요청의 모든 수신 문자열 필드를 자동으로 자르고 빈 문자열 필드를 `null`로 변환합니다. 이렇게하면 라우트 및 컨트롤러에서 이러한 정규화 문제를 걱정할 필요가 없습니다.

#### Disabling Input Normalization
#### 입력 정규화 비활성화

If you would like to disable this behavior for all requests, you may remove the two middleware from your application's middleware stack by removing them from the `$middleware` property of your `App\Http\Kernel` class.

모든 요청에 대해이 동작을 비활성화하려면 `App\Http\Kernel` 클래스의 `$middleware` 속성에서 두 미들웨어를 제거하여 응용 프로그램의 미들웨어 스택에서 제거할 수 있습니다.

If you would like to disable string trimming and empty string conversion for a subset of requests to your application, you may use the `skipWhen` method offered by both middleware. This method accepts a closure which should return `true` or `false` to indicate if input normalization should be skipped. Typically, the `skipWhen` method should be invoked in the `boot` method of your application's `AppServiceProvider`.

응용 프로그램의 요청의 하위 집합에 대해 문자열 자르기 및 빈 문자열 변환을 비활성화하려면 두 미들웨어가 제공하는 `skipWhen` 메소드를 사용할 수 있습니다. 이 메소드는 입력 정규화를 건너 뛸지 여부를 나타내는 `true` 또는 `false`를 반환하는 클로저를 받습니다. 일반적으로 `skipWhen` 메소드는 응용 프로그램의 `AppServiceProvider`의 `boot` 메소드에서 호출되어야합니다.

```php
use App\Http\Middleware\TrimStrings;
use Illuminate\Http\Request;
use Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull;

/**
 * Bootstrap any application services.
 */
public function boot(): void
{
    TrimStrings::skipWhen(function (Request $request) {
        return $request->is('admin/*');
    });

    ConvertEmptyStringsToNull::skipWhen(function (Request $request) {
        // ...
    });
}
```

<a name="files"></a>
## Files
## 파일

<a name="retrieving-uploaded-files"></a>
### Retrieving Uploaded Files
### 업로드된 파일 가져오기

You may retrieve uploaded files from an `Illuminate\Http\Request` instance using the `file` method or using dynamic properties. The `file` method returns an instance of the `Illuminate\Http\UploadedFile` class, which extends the PHP `SplFileInfo` class and provides a variety of methods for interacting with the file:

`Illuminate\Http\Request` 인스턴스에서 `file` 메소드 또는 동적 속성을 사용하여 업로드된 파일을 검색할 수 있습니다. `file` 메소드는 PHP `SplFileInfo` 클래스를 확장하고 파일과 상호 작용하는 다양한 메소드를 제공하는 `Illuminate\Http\UploadedFile` 클래스의 인스턴스를 반환합니다:

    $file = $request->file('photo');

    $file = $request->photo;

You may determine if a file is present on the request using the `hasFile` method:

`hasFile` 메소드를 사용하여 요청에 파일이 있는지 확인할 수 있습니다:

    if ($request->hasFile('photo')) {
        // ...
    }

<a name="validating-successful-uploads"></a>
#### Validating Successful Uploads
#### 성공적인 업로드 유효성 검사

In addition to checking if the file is present, you may verify that there were no problems uploading the file via the `isValid` method:

파일이 존재하는지 확인하는 것 외에도 `isValid` 메소드를 통해 파일 업로드에 문제가 없는지 확인할 수 있습니다:

    if ($request->file('photo')->isValid()) {
        // ...
    }

<a name="file-paths-extensions"></a>
#### File Paths & Extensions
#### 파일 경로 & 확장자

The `UploadedFile` class also contains methods for accessing the file's fully-qualified path and its extension. The `extension` method will attempt to guess the file's extension based on its contents. This extension may be different from the extension that was supplied by the client:

`UploadedFile` 클래스에는 파일의 완전한 경로와 확장자에 대한 액세스를위한 메소드도 포함되어 있습니다. `extension` 메소드는 파일의 내용을 기반으로 파일의 확장자를 추측합니다. 이 확장자는 클라이언트가 제공 한 확장자와 다를 수 있습니다:

    $path = $request->photo->path();

    $extension = $request->photo->extension();

<a name="other-file-methods"></a>
#### Other File Methods
#### 다른 파일 메소드

There are a variety of other methods available on `UploadedFile` instances. Check out the [API documentation for the class](https://github.com/symfony/symfony/blob/6.0/src/Symfony/Component/HttpFoundation/File/UploadedFile.php) for more information regarding these methods.

`UploadedFile` 인스턴스에서 사용할 수있는 다양한 다른 메소드가 있습니다. 이러한 메소드에 대한 자세한 내용은 [클래스 API 문서](https://github.com/symfony/symfony/blob/6.0/src/Symfony/Component/HttpFoundation/File/UploadedFile.php) 참조.

<a name="storing-uploaded-files"></a>
### Storing Uploaded Files
### 업로드된 파일 저장

To store an uploaded file, you will typically use one of your configured [filesystems](/docs/{{version}}/filesystem). The `UploadedFile` class has a `store` method that will move an uploaded file to one of your disks, which may be a location on your local filesystem or a cloud storage location like Amazon S3.

업로드된 파일을 저장하려면 일반적으로 구성된 [파일 시스템](/docs/{{version}}/filesystem) 중 하나를 사용합니다. `UploadedFile` 클래스에는 업로드 된 파일을 로컬 파일 시스템 또는 Amazon S3와 같은 클라우드 저장소 위치에 저장하는 `store` 메소드가 있습니다.

The `store` method accepts the path where the file should be stored relative to the filesystem's configured root directory. This path should not contain a filename, since a unique ID will automatically be generated to serve as the filename.

`store` 메소드는 파일 시스템의 구성된 루트 디렉토리에 대한 파일이 저장되어야하는 경로를 인수로 받습니다. 이 경로에는 파일 이름이 포함되어서는 안되며 고유한 ID가 자동으로 생성되어 파일 이름으로 사용됩니다.

The `store` method also accepts an optional second argument for the name of the disk that should be used to store the file. The method will return the path of the file relative to the disk's root:

`store` 메소드는 또한 파일을 저장하는 데 사용해야 하는 디스크의 이름에 대한 선택적 두 번째 인수를 허용합니다. 이 메소드는 디스크의 루트에 대한 파일의 경로를 반환합니다:

    $path = $request->photo->store('images');

    $path = $request->photo->store('images', 's3');

If you do not want a filename to be automatically generated, you may use the `storeAs` method, which accepts the path, filename, and disk name as its arguments:

파일 이름을 자동으로 생성하지 않으려면 `storeAs` 메소드를 사용할 수 있습니다. 이 메소드는 경로, 파일 이름 및 디스크 이름을 인수로 받습니다:

    $path = $request->photo->storeAs('images', 'filename.jpg');

    $path = $request->photo->storeAs('images', 'filename.jpg', 's3');

> **Note**  
> For more information about file storage in Laravel, check out the complete [file storage documentation](/docs/{{version}}/filesystem).
>
> **참고**
> Laravel에서 파일 저장에 대한 자세한 내용은 완전한 [파일 저장 문서](/docs/{{version}}/filesystem)를 참조하세요.

<a name="configuring-trusted-proxies"></a>
## Configuring Trusted Proxies
## 신뢰할 수 있는 프록시 설정

When running your applications behind a load balancer that terminates TLS / SSL certificates, you may notice your application sometimes does not generate HTTPS links when using the `url` helper. Typically this is because your application is being forwarded traffic from your load balancer on port 80 and does not know it should generate secure links.

TLS / SSL 인증서를 종료하는 로드 밸런서 뒤에서 애플리케이션을 실행할 때 `url` 헬퍼를 사용할 때 애플리케이션에서 HTTPS 링크를 생성하지 않는 경우가 있습니다. 일반적으로 이는 애플리케이션이 80 포트의 로드 밸런서에서 트래픽을 전달받고 있고 안전한 링크를 생성해야한다는 것을 알지 못하기 때문입니다.

To solve this, you may use the `App\Http\Middleware\TrustProxies` middleware that is included in your Laravel application, which allows you to quickly customize the load balancers or proxies that should be trusted by your application. Your trusted proxies should be listed as an array on the `$proxies` property of this middleware. In addition to configuring the trusted proxies, you may configure the proxy `$headers` that should be trusted:

이를 해결하려면 Laravel 애플리케이션에 포함된 `App\Http\Middleware\TrustProxies` 미들웨어를 사용하여 애플리케이션에서 신뢰해야하는 로드 밸런서 또는 프록시를 빠르게 사용자 정의할 수 있습니다. 신뢰할 수있는 프록시는 이 미들웨어의 `$proxies` 속성에 배열로 나열되어야합니다. 신뢰할 수있는 프록시를 구성하는 것 외에도 신뢰해야하는 프록시 `$headers`를 구성할 수 있습니다.

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

> **Note**  
> If you are using AWS Elastic Load Balancing, your `$headers` value should be `Request::HEADER_X_FORWARDED_AWS_ELB`. For more information on the constants that may be used in the `$headers` property, check out Symfony's documentation on [trusting proxies](https://symfony.com/doc/current/deployment/proxies.html).
>
> **참고**
> AWS Elastic Load Balancing을 사용하는 경우 `$headers` 값은 `Request::HEADER_X_FORWARDED_AWS_ELB` 여야합니다. `$headers` 속성에서 사용할 수있는 상수에 대한 자세한 내용은 Symfony의 [프록시 신뢰](https://symfony.com/doc/current/deployment/proxies.html) 문서를 참조하세요.

<a name="trusting-all-proxies"></a>
#### Trusting All Proxies
#### 모든 프록시 신뢰

If you are using Amazon AWS or another "cloud" load balancer provider, you may not know the IP addresses of your actual balancers. In this case, you may use `*` to trust all proxies:

Amazon AWS 또는 다른 "클라우드" 로드 밸런서 공급자를 사용하는 경우 실제 로드 밸런서의 IP 주소를 모를 수 있습니다. 이 경우 모든 프록시를 신뢰하기 위해 `*`를 사용할 수 있습니다:

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

기본적으로 Laravel는 HTTP 요청의 `Host` 헤더의 내용과 관계없이 수신한 모든 요청에 응답합니다. 또한 웹 요청 중 애플리케이션에 대한 절대 URL을 생성할 때 `Host` 헤더의 값이 사용됩니다.

Typically, you should configure your web server, such as Nginx or Apache, to only send requests to your application that match a given host name. However, if you do not have the ability to customize your web server directly and need to instruct Laravel to only respond to certain host names, you may do so by enabling the `App\Http\Middleware\TrustHosts` middleware for your application.

일반적으로 Nginx 또는 Apache와 같은 웹 서버를 구성하여 특정 호스트 이름과 일치하는 요청만 애플리케이션으로 전송하도록 설정해야합니다. 그러나 웹 서버를 직접 사용자 정의할 수 없고 특정 호스트 이름에만 응답하도록 Laravel에 지시해야하는 경우 애플리케이션에 `App\Http\Middleware\TrustHosts` 미들웨어를 활성화하여 수행할 수 있습니다.

The `TrustHosts` middleware is already included in the `$middleware` stack of your application; however, you should uncomment it so that it becomes active. Within this middleware's `hosts` method, you may specify the host names that your application should respond to. Incoming requests with other `Host` value headers will be rejected:

`TrustHosts` 미들웨어는 이미 애플리케이션의 `$middleware` 스택에 포함되어 있습니다. 그러나 활성화되도록 해제해야합니다. 이 미들웨어의 `hosts` 메소드에서 응답해야하는 애플리케이션의 호스트 이름을 지정할 수 있습니다. 다른 `Host` 값 헤더를 가진 수신 요청은 거부됩니다.

    /**
     * Get the host patterns that should be trusted.
     *
     * @return array<int, string>
     */
    public function hosts(): array
    {
        return [
            'laravel.test',
            $this->allSubdomainsOfApplicationUrl(),
        ];
    }

The `allSubdomainsOfApplicationUrl` helper method will return a regular expression matching all subdomains of your application's `app.url` configuration value. This helper method provides a convenient way to allow all of your application's subdomains when building an application that utilizes wildcard subdomains.

`allSubdomainsOfApplicationUrl` 헬퍼 메소드는 애플리케이션의 `app.url` 구성 값의 모든 하위 도메인과 일치하는 정규 표현식을 반환합니다. 이 헬퍼 메소드는 와일드 카드 하위 도메인을 사용하는 애플리케이션을 구축할 때 모든 애플리케이션의 하위 도메인을 허용하는 편리한 방법을 제공합니다.
