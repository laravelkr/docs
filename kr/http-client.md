# HTTP Client
# HTTP 클라이언트

- [Introduction](#introduction)
- [소개](#introduction)
- [Making Requests](#making-requests)
- [Request 만들기](#making-requests)
    - [Request Data](#request-data)
    - [Request 데이터](#request-data)
    - [Headers](#headers)
    - [헤더](#headers)
    - [Authentication](#authentication)
    - [인증](#authentication)
    - [Timeout](#timeout)
    - [시간초과](#timeout)
    - [Retries](#retries)
    - [재시도](#retries)
    - [Error Handling](#error-handling)
    - [에러 처리](#error-handling)
    - [Guzzle Options](#guzzle-options)
    - [Guzzle Options](#guzzle-options)
- [Concurrent Requests](#concurrent-requests)
- [동시 Request](#concurrent-requests)
- [Macros](#macros)
- [매크로](#macros)
- [Testing](#testing)
- [Testing](#testing)
    - [Faking Responses](#faking-responses)
    - [응답 속이기](#faking-responses)
    - [Inspecting Requests](#inspecting-requests)
    - [Request 검사하기](#inspecting-requests)
- [Events](#events)
- [이벤트](#events)

<a name="introduction"></a>
## Introduction
## 소개

Laravel provides an expressive, minimal API around the [Guzzle HTTP client](http://docs.guzzlephp.org/en/stable/), allowing you to quickly make outgoing HTTP requests to communicate with other web applications. Laravel's wrapper around Guzzle is focused on its most common use cases and a wonderful developer experience.

라라벨은 [Guzzle HTTP 클라이언트](http://docs.guzzlephp.org/en/stable/)를 기반으로 다른 웹 애플리케이션과 소통하기 위해 외부로 보내는 HTTP Request를 신속히 만들 수 있는 표현력 있고 미니멀한 API를 제공합니다. 라라벨 HTTP 클라이언트는 가장 흔한 사용 사례와 좋은 개발자 경험에 촛점을 맞추고 있습니다.

Before getting started, you should ensure that you have installed the Guzzle package as a dependency of your application. By default, Laravel automatically includes this dependency. However, if you have previously removed the package, you may install it again via Composer:

시작하기에 앞서 HTTP 클라이언트가 의존하는 Guzzle 패키지가 애플리케이션에 설치되어 있는지 확인해야 합니다. 기본적으로 라라벨은 이 의존성을 자동으로 불러들입니다. 이전에 패키지를 제거했다면 Composer를 통해 다시 설치할 수 있습니다.

    composer require guzzlehttp/guzzle

<a name="making-requests"></a>
## Making Requests
## Request 만들기

To make requests, you may use the `get`, `post`, `put`, `patch`, and `delete` methods provided by the `Http` facade. First, let's examine how to make a basic `GET` request to another URL:

Request를 만드는데 `Http` 파사드에서 제공하는 `get`, `post`, `put`, `patch`, `delete` 메서드를 사용할 수 있습니다. 우선 다른 URL에 기본적인 `GET` Request를 하는 방법을 알아봅시다.

    use Illuminate\Support\Facades\Http;

    $response = Http::get('http://example.com');

The `get` method returns an instance of `Illuminate\Http\Client\Response`, which provides a variety of methods that may be used to inspect the response:

    $response->body() : string;
    $response->json() : array|mixed;
    $response->object() : object;
    $response->collect() : Illuminate\Support\Collection;
    $response->status() : int;
    $response->ok() : bool;
    $response->successful() : bool;
    $response->failed() : bool;
    $response->serverError() : bool;
    $response->clientError() : bool;
    $response->header($header) : string;
    $response->headers() : array;

The `Illuminate\Http\Client\Response` object also implements the PHP `ArrayAccess` interface, allowing you to access JSON response data directly on the response:

`Illuminate\Http\Client\Response` 객체는 PHP `ArrayAccess` 인터페이스도 구현하고 있기 때문에 응답에서 JSON 응답 데이터에 바로 접근할 수 있습니다.

    return Http::get('http://example.com/users/1')['name'];

<a name="dumping-requests"></a>
#### Dumping Requests
#### Request의 dump하기

If you would like to dump the outgoing request instance before it is sent and terminate the script's execution, you may add the `dd` method to the beginning of your request definition:

외부로 Request를 보내기 전에 dump 후 스크립트 실행을 종료하려면 Request 정의의 시작 부분에 `dd` 메서드를 추가할 수 있습니다.

    return Http::dd()->get('http://example.com');

<a name="request-data"></a>
### Request Data
### Request 데이터

Of course, it is common when making `POST`, `PUT`, and `PATCH` requests to send additional data with your request, so these methods accept an array of data as their second argument. By default, data will be sent using the `application/json` content type:

당연한 이야기이지만, 보통 `POST`, `PUT`, `PATCH`를 사용할 때 Request에 추가적인 데이터를 함께 보냅니다. 그래서 이 메서드들은 두 번째 인자로 데이터의 배열을 받습니다. 기본적으로 데이터는 `application/json` 컨텐츠 타입으로 전송됩니다.

    use Illuminate\Support\Facades\Http;

    $response = Http::post('http://example.com/users', [
        'name' => 'Steve',
        'role' => 'Network Administrator',
    ]);

<a name="get-request-query-parameters"></a>
#### GET Request Query Parameters
#### GET 리퀘스트 쿼리 파라메터

When making `GET` requests, you may either append a query string to the URL directly or pass an array of key / value pairs as the second argument to the `get` method:

`GET` Request를 할 때 쿼리스트링을 URL에 직접 추가하거나 키 / 값 쌍의 배열을 `get` 메소드의 두 번째 인수로 전달할 수 있습니다.

    $response = Http::get('http://example.com/users', [
        'name' => 'Taylor',
        'page' => 1,
    ]);

<a name="sending-form-url-encoded-requests"></a>
#### Sending Form URL Encoded Requests
#### 폼 URL 인코디드 Request 전송하기

If you would like to send data using the `application/x-www-form-urlencoded` content type, you should call the `asForm` method before making your request:

`application/x-www-form-urlencoded` 컨텐츠 타입으로 데이터를 전송하고 싶으면 Request를 만들기 전에 `asForm` 메서드를 호출해야 합니다.

    $response = Http::asForm()->post('http://example.com/users', [
        'name' => 'Sara',
        'role' => 'Privacy Consultant',
    ]);

<a name="sending-a-raw-request-body"></a>
#### Sending A Raw Request Body
#### Raw Body Request 보내기

You may use the `withBody` method if you would like to provide a raw request body when making a request. The content type may be provided via the method's second argument:

Request할 때 raw request body를 발송하려면 `withBody` 메서드를 사용할 수 있습니다. 콘텐츠 유형은 메소드의 두 번째 인수를 통해 지정할 수 있습니다.

    $response = Http::withBody(
        base64_encode($photo), 'image/jpeg'
    )->post('http://example.com/photo');

<a name="multi-part-requests"></a>
#### Multi-Part Requests
#### 멀티-파트 Request

If you would like to send files as multi-part requests, you should call the `attach` method before making your request. This method accepts the name of the file and its contents. If needed, you may provide a third argument which will be considered the file's filename:

파일을 멀티-파트 Request로 보내고 싶으면 Request를 만들기 전에 `attach` 메서드를 호출해야 합니다. 이 메서드는 파일의 이름과 내용을 인자로 받습니다. 필요하면 세 번째 인자로 파일명을 넘겨줄 수도 있습니다.

    $response = Http::attach(
        'attachment', file_get_contents('photo.jpg'), 'photo.jpg'
    )->post('http://example.com/attachments');

Instead of passing the raw contents of a file, you may pass a stream resource:

파일의 원시 내용을 전달하는 대신 스트림 리소스를 전달할 수 있습니다.

    $photo = fopen('photo.jpg', 'r');

    $response = Http::attach(
        'attachment', $photo, 'photo.jpg'
    )->post('http://example.com/attachments');

<a name="headers"></a>
### Headers
### 헤더

Headers may be added to requests using the `withHeaders` method. This `withHeaders` method accepts an array of key / value pairs:

`withHeaders` 메서드를 이용해서 헤더를 추가할 수 있습니다. `withHeaders` 메서드는 키 / 값 쌍으로 이뤄진 배열을 받습니다.

    $response = Http::withHeaders([
        'X-First' => 'foo',
        'X-Second' => 'bar'
    ])->post('http://example.com/users', [
        'name' => 'Taylor',
    ]);

You may use the `accept` method to specify the content type that your application is expecting in response to your request:

`accept` 메소드를 사용하여 Request에 대한 응답으로 애플리케이션이 기대하는 콘텐츠 유형을 지정할 수 있습니다.

    $response = Http::accept('application/json')->get('http://example.com/users');

For convenience, you may use the `acceptJson` method to quickly specify that your application expects the `application/json` content type in response to your request:

편의를 위해 `acceptJson` 메소드를 사용하여 애플리케이션이 Request에 대한 응답으로 `application/json` 콘텐츠 유형을 예상하도록 빠르게 지정할 수 있습니다.

    $response = Http::acceptJson()->get('http://example.com/users');

<a name="authentication"></a>
### Authentication
### 인증

You may specify basic and digest authentication credentials using the `withBasicAuth` and `withDigestAuth` methods, respectively:

`withBasicAuth`와 `withDigestAuth` 메서드를 이용해서 기본 인증을 사용할 건지 다이제스트 인증을 사용할 건지 지정할 수 있습니다.

    // Basic authentication...
    $response = Http::withBasicAuth('taylor@laravel.com', 'secret')->post(...);

    // Digest authentication...
    $response = Http::withDigestAuth('taylor@laravel.com', 'secret')->post(...);

<a name="bearer-tokens"></a>
#### Bearer Tokens
#### Bearer 토큰

If you would like to quickly add a bearer token to the request's `Authorization` header, you may use the `withToken` method:

Request 헤더에 `Authorization` bearer 토큰을 추가하고 싶으면 `withToken` 메서드를 사용하면 됩니다.

    $response = Http::withToken('token')->post(...);

<a name="timeout"></a>
### Timeout
### 시간초과

The `timeout` method may be used to specify the maximum number of seconds to wait for a response:

`timeout` 메소드는 응답을 기다리는 최대 시간 (초)을 지정하는 데 사용될 수 있습니다.

    $response = Http::timeout(3)->get(...);

If the given timeout is exceeded, an instance of `Illuminate\Http\Client\ConnectionException` will  be thrown.

주어진 타임 아웃이 초과되면 `Illuminate\Http\Client\ConnectionException`의 인스턴스가 발생합니다.

<a name="retries"></a>
### Retries
### 재시도

If you would like HTTP client to automatically retry the request if a client or server error occurs, you may use the `retry` method. The `retry` method accepts the maximum number of times the request should be attempted and the number of milliseconds that Laravel should wait in between attempts:

클라이언트나 서버 에러 발생 시 자동으로 재시도하고 싶으면 `retry` 메서드를 사용하면 됩니다. `retry` 메서드는 두 개의 인자를 받습니다. 하나는 재시도할 횟수이고 다른 하나는 재시도 간 간격(밀리초)입니다.


    $response = Http::retry(3, 100)->post(...);

If needed, you may pass a third argument to the `retry` method. The third argument should be a callable that determines if the retries should actually be attempted. For example, you may wish to only retry the request if the initial request encounters an `ConnectionException`:

필요한 경우 `retry` 메서드에 세 번째 인수를 전달할 수 있습니다. 세 번째 인수는 재시도가 실제로 시도되어야 하는지 여부를 결정하는 callable이어야 합니다. 예를 들어, 초기 Request에서 `ConnectionException`이 발생한 경우에만 Request를 재시도할 수 있습니다.

    $response = Http::retry(3, 100, function ($exception) {
        return $exception instanceof ConnectionException;
    })->post(...);

If all of the requests fail, an instance of `Illuminate\Http\Client\RequestException` will be thrown.

모든 Request가 실패하면 `Illuminate\Http\Client\RequestException` 인스턴스를 던집니다.

<a name="error-handling"></a>
### Error Handling
### 에러 처리

Unlike Guzzle's default behavior, Laravel's HTTP client wrapper does not throw exceptions on client or server errors (`400` and `500` level responses from servers). You may determine if one of these errors was returned using the `successful`, `clientError`, or `serverError` methods:

Guzzle의 기본 동작과는 달리 라라벨 HTTP 클라이언트는 클라이언트나 서버 에러에 대해 예외(서버에서 보내는 `400`과 `500`대의 응답)를 던지지 않습니다. `successful`, `clientError`, `serverError` 메서드를 이용해서 에러가 반환되는지 판단할 수 있습니다.

    // Determine if the status code is >= 200 and < 300...
    // 상태 코드가 200 이상 300 미만인지 판단...
    $response->successful();

    // Determine if the status code is >= 400...
    // 상태 코드가 400이상 인지 판단...
    $response->failed();

    // Determine if the response has a 400 level status code...
    // 응답 코드가 400대인지 판단...
    $response->clientError();

    // Determine if the response has a 500 level status code...
    // 응답 코드가 500대인지 판단...
    $response->serverError();

<a name="throwing-exceptions"></a>
#### Throwing Exceptions
#### 예외 던지기

If you have a response instance and would like to throw an instance of `Illuminate\Http\Client\RequestException` if the response status code indicates a client or server error, you may use the `throw` or `throwIf` methods:

응답이 클라이언트나 서버 에러일 경우 `Illuminate\Http\Client\RequestException` 인스턴스를 던지고 싶으면 `throw` 메서드를 사용하면 됩니다.

    $response = Http::post(...);

    // Throw an exception if a client or server error occurred...
    // 클라이언트나 서버 에러가 발생하면 예외를 던진다...
    $response->throw();

    // Throw an exception if an error occurred and the given condition is true...
    // 오류가 발생하고 조건에 해당하는 경우 예외를 던진다...
    $response->throwIf($condition);

    return $response['user']['id'];

The `Illuminate\Http\Client\RequestException` instance has a public `$response` property which will allow you to inspect the returned response.

`Illuminate\Http\Client\RequestException` 인스턴스는 반환된 응답을 검사하는데 사용할 수 있는 `$response`라는 공개 속성을 가지고 있습니다.

The `throw` method returns the response instance if no error occurred, allowing you to chain other operations onto the `throw` method:

`throw` 메서드를 써도 실제로 에러가 발생하지 않으면 응답 인스턴스를 반환하기 때문에 `throw` 메서드에 다른 작업을 연결할 수 있습니다.

    return Http::post(...)->throw()->json();

If you would like to perform some additional logic before the exception is thrown, you may pass a closure to the `throw` method. The exception will be thrown automatically after the closure is invoked, so you do not need to re-throw the exception from within the closure:

예외가 발생하기 전에 몇 가지 추가 로직을 수행하려면 `throw` 메소드에 클로저를 전달할 수 있습니다. 클로저가 호출된 후 예외가 자동으로 throw되므로 클로저 내에서 예외를 다시 throw할 필요가 없습니다.

    return Http::post(...)->throw(function ($response, $e) {
        //
    })->json();

<a name="guzzle-options"></a>
### Guzzle Options
### Guzzle Options

You may specify additional [Guzzle request options](http://docs.guzzlephp.org/en/stable/request-options.html) using the `withOptions` method. The `withOptions` method accepts an array of key / value pairs:

`withOptions` 메소드를 사용하여 추가 [Guzzle request options](http://docs.guzzlephp.org/en/stable/request-options.html) 를 지정할 수 있습니다. `withOptions` 메소드는 키 / 값 쌍의 배열을 받습니다.

    $response = Http::withOptions([
        'debug' => true,
    ])->get('http://example.com/users');

<a name="concurrent-requests"></a>
## Concurrent Requests
## 동시 Request

Sometimes, you may wish to make multiple HTTP requests concurrently. In other words, you want several requests to be dispatched at the same time instead of issuing the requests sequentially. This can lead to substantial performance improvements when interacting with slow HTTP APIs.

여러 HTTP Request를 동시에 보내고 싶을 수도 있습니다. 즉, Request를 순차적으로 보내는 대신 여러 Request를 동시에 보내려고 하는 것입니다. 이는 느린 HTTP API와 상호 작용할 때 높은 성능 향상으로 이어질 수 있습니다.

Thankfully, you may accomplish this using the `pool` method. The `pool` method accepts a closure which receives an `Illuminate\Http\Client\Pool` instance, allowing you to easily add requests to the request pool for dispatching:

고맙게도 `pool` 메서드를 사용하여 이 작업을 수행할 수 있습니다. `pool` 메소드는 `Illuminate\Http\Client\Pool` 인스턴스를 수신하는 클로저를 허용하므로 발송을 위해 Request 풀에 Request를 쉽게 추가할 수 있습니다.

    use Illuminate\Http\Client\Pool;
    use Illuminate\Support\Facades\Http;

    $responses = Http::pool(fn (Pool $pool) => [
        $pool->get('http://localhost/first'),
        $pool->get('http://localhost/second'),
        $pool->get('http://localhost/third'),
    ]);

    return $responses[0]->ok() &&
           $responses[1]->ok() &&
           $responses[2]->ok();

As you can see, each response instance can be accessed based on the order it was added to the pool. If you wish, you can name the requests using the `as` method, which allows you to access the corresponding responses by name:

보시다시피 각 응답 인스턴스는 풀에 추가된 순서에 따라 액세스할 수 있습니다. 원하는 경우 `as` 메서드를 사용하여 Request의 이름을 지정할 수 있습니다. 그러면 이름으로 해당 응답에 액세스할 수 있습니다.

    use Illuminate\Http\Client\Pool;
    use Illuminate\Support\Facades\Http;

    $responses = Http::pool(fn (Pool $pool) => [
        $pool->as('first')->get('http://localhost/first'),
        $pool->as('second')->get('http://localhost/second'),
        $pool->as('third')->get('http://localhost/third'),
    ]);

    return $responses['first']->ok();

<a name="macros"></a>
## Macros
## 매크로

The Laravel HTTP client allows you to define "macros", which can serve as a fluent, expressive mechanism to configure common request paths and headers when interacting with services throughout your application. To get started, you may define the macro within the `boot` method of your application's `App\Providers\AppServiceProvider` class:

Laravel HTTP 클라이언트를 사용하면 "매크로"를 정의할 수 있습니다. 이는 애플리케이션 전체에서 서비스와 상호 작용할 때 공통 Request 경로와 헤더를 구성하기 위한 유연하고 풍부한 메커니즘 역할을 할 수 있습니다. 시작하려면 애플리케이션의 `App\Providers\AppServiceProvider` 클래스의 `boot` 메소드 내에서 매크로를 정의할 수 있습니다.

```php
use Illuminate\Support\Facades\Http;

/**
 * Bootstrap any application services.
 *
 * @return void
 */
public function boot()
{
    Http::macro('github', function () {
        return Http::withHeaders([
            'X-Example' => 'example',
        ])->baseUrl('https://github.com');
    });
}
```

Once your macro has been configured, you may invoke it from anywhere in your application to create a pending request with the specified configuration:

매크로가 구성되면 애플리케이션의 어디에서나 매크로를 호출하여 지정된 구성으로 대기 중인 Request를 생성할 수 있습니다.

```php
$response = Http::github()->get('/');
```

<a name="testing"></a>
## Testing
## 테스트

Many Laravel services provide functionality to help you easily and expressively write tests, and Laravel's HTTP wrapper is no exception. The `Http` facade's `fake` method allows you to instruct the HTTP client to return stubbed / dummy responses when requests are made.

많은 라라벨 서비스가 테스트 작성에 도움을 주는 기능을 제공하는데, 라라벨의 HTTP 클라이언트도 예외가 아닙니다. `Http` 퍼사드의 `fake` 메서드를 사용하면 Request가 만들어졌을 때 스텁 / 더미 응답을 반환하도록 HTTP 클라이언트에게 지시할 수 있습니다.

<a name="faking-responses"></a>
### Faking Responses
### 응답 속이기

For example, to instruct the HTTP client to return empty, `200` status code responses for every request, you may call the `fake` method with no arguments:

예를 들어, 모든 Request에 대해 아무것도 리턴하지 않고 `200` 상태 코드를 응답하도록 HTTP 클라이언트에게 지시하려면 아무 인자 없이 `fake` 메서드를 호출하면 됩니다.

    use Illuminate\Support\Facades\Http;

    Http::fake();

    $response = Http::post(...);

> {note} When faking requests, HTTP client middleware are not executed. You should define expectations for faked responses as if these middleware have run correctly.

> {note} 가짜 Request 시 HTTP 클라이언트 미들웨어는 실행되지 않습니다. 이러한 미들웨어가 올바르게 실행된 것처럼 가짜 응답에 대한 기대치를 정의해야 합니다.

<a name="faking-specific-urls"></a>
#### Faking Specific URLs
#### 특정 URL 속이기

Alternatively, you may pass an array to the `fake` method. The array's keys should represent URL patterns that you wish to fake and their associated responses. The `*` character may be used as a wildcard character. Any requests made to URLs that have not been faked will actually be executed. You may use the `Http` facade's `response` method to construct stub / fake responses for these endpoints:

한편, `fake` 메서드에 배열을 넘겨줄 수도 있습니다. 배열의 키는 속이고자 하는 URL 패턴을 나타내고 값은 응답을 나타냅니다. `*` 문자는 와일드 카드로 사용할 수 있습니다. 속이지 않은 모든 URL은 실제로 수행됩니다. 스텁 / 가짜 응답을 만드는데는 `Http` 파사드의 `response` 메서드를 사용합니다.

    Http::fake([
        // Stub a JSON response for GitHub endpoints...
        // 깃헙 엔드포인트용 JSON 응답 스텁...
        'github.com/*' => Http::response(['foo' => 'bar'], 200, $headers),

        // Stub a string response for Google endpoints...
        // 구글 엔드포인트용 문자열 응답 스텁...
        'google.com/*' => Http::response('Hello World', 200, $headers),
    ]);

If you would like to specify a fallback URL pattern that will stub all unmatched URLs, you may use a single `*` character:

일치하지 않는 모든 URL용 대체 URL 패턴을 지정하고 싶으면 `*` 문자를 사용하면 됩니다.

    Http::fake([
        // Stub a JSON response for GitHub endpoints...
        // 깃헙 엔드포인트용 JSON 응답 스텁...
        'github.com/*' => Http::response(['foo' => 'bar'], 200, ['Headers']),

        // Stub a string response for all other endpoints...
        // 다른 모든 엔드 포인트용 문자열 응답 스텁...
        '*' => Http::response('Hello World', 200, ['Headers']),
    ]);

<a name="faking-response-sequences"></a>
#### Faking Response Sequences
#### 응답 시퀀스 속이기

Sometimes you may need to specify that a single URL should return a series of fake responses in a specific order. You may accomplish this using the `Http::sequence` method to build the responses:

단일 URL이 여러 가짜 응답을 특정한 순서로 반환할 필요가 있을 때가 있습니다. 이를 위해서는 `Http::sequence` 메서드를 사용해서 응답을 만들면 됩니다.

    Http::fake([
        // Stub a series of responses for GitHub endpoints...
        // 깃헙 엔드포인트용 다중 응답 스텁...
        'github.com/*' => Http::sequence()
                                ->push('Hello World', 200)
                                ->push(['foo' => 'bar'], 200)
                                ->pushStatus(404),
    ]);

When all of the responses in a response sequence have been consumed, any further requests will cause the response sequence to throw an exception. If you would like to specify a default response that should be returned when a sequence is empty, you may use the `whenEmpty` method:

응답 시퀀스에 있는 모든 응답이 소비되고 나면 그 이후에 응답 시퀀스로 보내는 Request는 예외가 발생합니다. 시퀀스가 비어있을 때의 기본 응답을 지정하고 싶으면 `whenEmpty` 메서드를 사용하면 됩니다.

    Http::fake([
        // Stub a series of responses for GitHub endpoints...
        // 깃헙 엔드포인트용 다중 응답 스텁...
        'github.com/*' => Http::sequence()
                                ->push('Hello World', 200)
                                ->push(['foo' => 'bar'], 200)
                                ->whenEmpty(Http::response()),
    ]);

If you would like to fake a sequence of responses but do not need to specify a specific URL pattern that should be faked, you may use the `Http::fakeSequence` method:

응답 시퀀스를 속이되 URL 패턴을 지정할 필요가 없을 때는 `Http::fakeSequence` 메서드를 사용하면 됩니다.

    Http::fakeSequence()
            ->push('Hello World', 200)
            ->whenEmpty(Http::response());

<a name="fake-callback"></a>
#### Fake Callback
#### 가짜 콜백

If you require more complicated logic to determine what responses to return for certain endpoints, you may pass a closure to the `fake` method. This closure will receive an instance of `Illuminate\Http\Client\Request` and should return a response instance. Within your closure, you may perform whatever logic is necessary to determine what type of response to return:

특정 엔드포인트가 어떤 응답을 반환해야하는지 결정하는데 더욱 더 복잡한 로직이 필요하다면 `fake` 메서드에 콜백을 넘겨줄 수 있습니다. 이 콜백은 `Illuminate\Http\Client\Request` 인스턴스를 받고 응답 인스턴스를 반환해야 합니다.

    Http::fake(function ($request) {
        return Http::response('Hello World', 200);
    });

<a name="inspecting-requests"></a>
### Inspecting Requests
### Request 검사하기

When faking responses, you may occasionally wish to inspect the requests the client receives in order to make sure your application is sending the correct data or headers. You may accomplish this by calling the `Http::assertSent` method after calling `Http::fake`.

응답을 속일 때 제대로 된 데이터와 헤더를 보내는지 확인하기 위해 클라이언트가 받는 Request를 검사하고 싶을 때가 있습니다. `Http::fake`를 호출한 이후에 `Http::assertSent` 메서드를 호출해서 검사할 수 있습니다.

The `assertSent` method accepts a closure which will receive an `Illuminate\Http\Client\Request` instance and should return a boolean value indicating if the request matches your expectations. In order for the test to pass, at least one request must have been issued matching the given expectations:

`assertSent` 메서드는 콜백을 받는데, 이 콜백은 `Illuminate\Http\Client\Request` 인스턴스를 인자로 받고 Request가 기대한 것과 일치하는지를 나타내는 불리언 값을 반환합니다. 테스트가 통과하기 위해서는 주어진 기대와 일치하는 Request가 최소한 한 번은 발행되어야 합니다.

    use Illuminate\Http\Client\Request;
    use Illuminate\Support\Facades\Http;

    Http::fake();

    Http::withHeaders([
        'X-First' => 'foo',
    ])->post('http://example.com/users', [
        'name' => 'Taylor',
        'role' => 'Developer',
    ]);

    Http::assertSent(function (Request $request) {
        return $request->hasHeader('X-First', 'foo') &&
               $request->url() == 'http://example.com/users' &&
               $request['name'] == 'Taylor' &&
               $request['role'] == 'Developer';
    });

If needed, you may assert that a specific request was not sent using the `assertNotSent` method:

필요한 경우 `assertNotSent` 메소드를 사용하여 특정 Request를 전송하지 않았는지 검증 할 수 있습니다.

    use Illuminate\Http\Client\Request;
    use Illuminate\Support\Facades\Http;

    Http::fake();

    Http::post('http://example.com/users', [
        'name' => 'Taylor',
        'role' => 'Developer',
    ]); 

    Http::assertNotSent(function (Request $request) {
        return $request->url() === 'http://example.com/posts';
    });

Or, you may use the `assertNothingSent` method to assert that no requests were sent during the test:

또는 Request가 전송되지 않았는지 검증하려면 `assertNothingSent` 메소드를 사용할 수 있습니다.

    Http::fake();

    Http::assertNothingSent();

<a name="events"></a>
## Events
## 이벤트

Laravel fires three events during the process of sending HTTP requests. The `RequestSending` event is fired prior to a request being sent, while the `ResponseReceived` event is fired after a response is received for a given request. The `ConnectionFailed` event is fired if no response is received for a given request.

Laravel은 HTTP Request를 보내는 과정에서 세 가지 이벤트를 발생시킵니다. `RequestSending` 이벤트는 Request가 전송되기 전에 발생하는 반면, `ResponseReceived` 이벤트는 주어진 Request에 대한 응답이 수신된 후에 발생합니다. 주어진 Request에 대한 응답이 수신되지 않으면 `ConnectionFailed` 이벤트가 발생합니다.

The `RequestSending` and `ConnectionFailed` events both contain a public `$request` property that you may use to inspect the `Illuminate\Http\Client\Request` instance. Likewise, the `ResponseReceived` event contains a `$request` property as well as a `$response` property which may be used to inspect the `Illuminate\Http\Client\Response` instance. You may register event listeners for this event in your `App\Providers\EventServiceProvider` service provider:

`RequestSending` 및 `ConnectionFailed` 이벤트에는 모두 `Illuminate\Http\Client\Request` 인스턴스를 검사하는 데 사용할 수 있는 공개 `$request` 속성이 포함되어 있습니다. 마찬가지로 `ResponseReceived` 이벤트에는 `$request` 속성과 `Illuminate\Http\Client\Response` 인스턴스를 검사하는 데 사용할 수 있는 `$response` 속성이 포함되어 있습니다. `App\Providers\EventServiceProvider` 서비스 제공자에서 이 이벤트에 대한 이벤트 리스너를 등록할 수 있습니다.

    /**
     * The event listener mappings for the application.
     *
     * @var array
     */
    protected $listen = [
        'Illuminate\Http\Client\Events\RequestSending' => [
            'App\Listeners\LogRequestSending',
        ],
        'Illuminate\Http\Client\Events\ResponseReceived' => [
            'App\Listeners\LogResponseReceived',
        ],
        'Illuminate\Http\Client\Events\ConnectionFailed' => [
            'App\Listeners\LogConnectionFailed',
        ],
    ];
