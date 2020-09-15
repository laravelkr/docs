# HTTP Client
# HTTP 클라이언트

- [Introduction](#introduction)
- [소개](#introduction)
- [Making Requests](#making-requests)
- [요청 만들기](#making-requests)
    - [Request Data](#request-data)
    - [요청 데이터](#request-data)
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
- [테스트](#testing)
- [Testing](#testing)
    - [Faking Responses](#faking-responses)
    - [응답 속이기](#faking-responses)
    - [Inspecting Requests](#inspecting-requests)
    - [요청 검사하기](#inspecting-requests)

<a name="introduction"></a>
## Introduction
## 소개

Laravel provides an expressive, minimal API around the [Guzzle HTTP client](http://docs.guzzlephp.org/en/stable/), allowing you to quickly make outgoing HTTP requests to communicate with other web applications. Laravel's wrapper around Guzzle is focused on its most common use cases and a wonderful developer experience.

라라벨은 [Guzzle HTTP 클라이언트](http://docs.guzzlephp.org/en/stable/)를 기반으로 다른 웹 애플리케이션과 소통하기 위해 외부로 보내는 HTTP 요청을 신속히 만들 수 있는 표현력 있고 미니멀한 API를 제공합니다. 라라벨 HTTP 클라이언트는 가장 흔한 사용 사례와 좋은 개발자 경험에 촛점을 맞추고 있습니다.

Before getting started, you should ensure that you have installed the Guzzle package as a dependency of your application. By default, Laravel automatically includes this dependency:

시작하기에 앞서 HTTP 클라이언트가 의존하는 Guzzle 패키지가 애플리케이션에 설치되어 있는지 확인해야 합니다. 기본적으로 라라벨은 이 의존성을 자동으로 불러들입니다.

    composer require guzzlehttp/guzzle

<a name="making-requests"></a>
## Making Requests
## 요청 만들기

To make requests, you may use the `get`, `post`, `put`, `patch`, and `delete` methods. First, let's examine how to make a basic `GET` request:

요청을 만드는데 `get`, `post`, `put`, `patch`, `delete` 메서드를 사용할 수 있습니다. 우선 기본적인 `GET` 요청부터 알아봅시다.

    use Illuminate\Support\Facades\Http;

    $response = Http::get('http://test.com');

The `get` method returns an instance of `Illuminate\Http\Client\Response`, which provides a variety of methods that may be used to inspect the response:

`get` 메서드는 `Illuminate\Http\Client\Response` 인스턴스를 반환합니다. 이 인스턴스는 응답을 검사할 때 사용하는 다양한 메서드를 제공합니다.

    $response->body() : string;
    $response->json() : array|mixed;
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

    return Http::get('http://test.com/users/1')['name'];

<a name="request-data"></a>
### Request Data
### 요청 데이터

Of course, it is common when using `POST`, `PUT`, and `PATCH` to send additional data with your request. So, these methods accept an array of data as their second argument. By default, data will be sent using the `application/json` content type:

당연한 이야기이지만, 보통 `POST`, `PUT`, `PATCH`를 사용할 때 요청에 추가적인 데이터를 함께 보냅니다. 그래서 이 메서드들은 두 번째 인자로 데이터의 배열을 받습니다. 기본적으로 데이터는 `application/json` 컨텐트 타입으로 전송됩니다.

    $response = Http::post('http://test.com/users', [
        'name' => 'Steve',
        'role' => 'Network Administrator',
    ]);

#### GET Request Query Parameters
#### GET 리퀘스트 쿼리 파라메터

When making `GET` requests, you may either append a query string to the URL directly or pass an array of key / value pairs as the second argument to the `get` method:

`GET` 요청을 할 때 쿼리스트링을 URL에 직접 추가하거나 키 / 값 쌍의 배열을 `get` 메소드의 두 번째 인수로 전달할 수 있습니다.

    $response = Http::get('http://test.com/users', [
        'name' => 'Taylor',
        'page' => 1,
    ]);

#### Sending Form URL Encoded Requests
#### 폼 URL 인코디드 요청 전송하기

If you would like to send data using the `application/x-www-form-urlencoded` content type, you should call the `asForm` method before making your request:

`application/x-www-form-urlencoded` 컨텐트 타입으로 데이터를 전송하고 싶으면 요청을 만들기 전에 `asForm` 메서드를 호출해야 합니다.

    $response = Http::asForm()->post('http://test.com/users', [
        'name' => 'Sara',
        'role' => 'Privacy Consultant',
    ]);

#### Sending A Raw Request Body
#### Raw Body Request 보내기

You may use the `withBody` method if you would like to provide a raw request body when making a request:

요청할 때 raw request body를 발송하려면 `withBody` 메서드를 사용할 수 있습니다.

    $response = Http::withBody(
        base64_encode($photo), 'image/jpeg'
    )->post('http://test.com/photo');

#### Multi-Part Requests
#### 멀티-파트 요청

If you would like to send files as multi-part requests, you should call the `attach` method before making your request. This method accepts the name of the file and its contents. Optionally, you may provide a third argument which will be considered the file's filename:

파일을 멀티-파트 요청으로 보내고 싶으면 요청을 만들기 전에 `attach` 메서드를 호출해야 합니다. 이 메서드는 파일의 이름과 내용을 인자로 받습니다. 필요하면 세 번째 인자로 파일명을 넘겨줄 수도 있습니다.

    $response = Http::attach(
        'attachment', file_get_contents('photo.jpg'), 'photo.jpg'
    )->post('http://test.com/attachments');

Instead of passing the raw contents of a file, you may also pass a stream resource:

파일의 원시 내용을 전달하는 대신 스트림 리소스를 전달할 수도 있습니다.

    $photo = fopen('photo.jpg', 'r');

    $response = Http::attach(
        'attachment', $photo, 'photo.jpg'
    )->post('http://test.com/attachments');

<a name="headers"></a>
### Headers
### 헤더

Headers may be added to requests using the `withHeaders` method. This `withHeaders` method accepts an array of key / value pairs:

`withHeaders` 메서드를 이용해서 헤더를 추가할 수 있습니다. `withHeaders` 메서드는 키 / 값 쌍으로 이뤄진 배열을 받습니다.

    $response = Http::withHeaders([
        'X-First' => 'foo',
        'X-Second' => 'bar'
    ])->post('http://test.com/users', [
        'name' => 'Taylor',
    ]);

<a name="authentication"></a>
### Authentication
### 인증

You may specify basic and digest authentication credentials using the `withBasicAuth` and `withDigestAuth` methods, respectively:

`withBasicAuth`와 `withDigestAuth` 메서드를 이용해서 기본 인증을 사용할 건지 다이제스트 인증을 사용할 건지 지정할 수 있습니다.

    // Basic authentication...
    $response = Http::withBasicAuth('taylor@laravel.com', 'secret')->post(...);

    // Digest authentication...
    $response = Http::withDigestAuth('taylor@laravel.com', 'secret')->post(...);

#### Bearer Tokens
#### Bearer 토큰

If you would like to quickly add an `Authorization` bearer token header to the request, you may use the `withToken` method:

요청 헤더에 `Authorization` bearer 토큰을 추가하고 싶으면 `withToken` 메서드를 사용하면 됩니다.

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

If you would like HTTP client to automatically retry the request if a client or server error occurs, you may use the `retry` method. The `retry` method accepts two arguments: the number of times the request should be attempted and the number of milliseconds that Laravel should wait in between attempts:

클라이언트나 서버 에러 발생 시 자동으로 재시도하고 싶으면 `retry` 메서드를 사용하면 됩니다. `retry` 메서드는 두 개의 인자를 받습니다. 하나는 재시도할 횟수이고 다른 하나는 재시도 간 간격(밀리초)입니다.

    $response = Http::retry(3, 100)->post(...);

If all of the requests fail, an instance of `Illuminate\Http\Client\RequestException` will be thrown.

모든 요청이 실패하면 `Illuminate\Http\Client\RequestException` 인스턴스를 던집니다.

<a name="error-handling"></a>
### Error Handling
### 에러 처리

Unlike Guzzle's default behavior, Laravel's HTTP client wrapper does not throw exceptions on client or server errors (`400` and `500` level responses from servers). You may determine if one of these errors was returned using the `successful`, `clientError`, or `serverError` methods:

Guzzle의 기본 동작과는 달리 라라벨 HTTP 클라이언트는 클라이언트나 서버 에러에 대해 예외(서버에서 보내는 `400`과 `500`대의 응답)를 던지지 않습니다. `successful`, `clientError`, `serverError` 메서드를 이용해서 에러가 반환되는지 판단할 수 있습니다.

    // 상태 코드가 200 이상 300 미만인지 판단...
    $response->successful();

    // 상태 코드가 400이상 인지 판단...
    $response->failed();

    // 응답 코드가 400대인지 판단...
    $response->clientError();

    // 응답 코드가 500대인지 판단...
    $response->serverError();

#### Throwing Exceptions
#### 예외 던지기

If you have a response instance and would like to throw an instance of `Illuminate\Http\Client\RequestException` if the response is a client or server error, you may use the `throw` method:

응답이 클라이언트나 서버 에러일 경우 `Illuminate\Http\Client\RequestException` 인스턴스를 던지고 싶으면 `throw` 메서드를 사용하면 됩니다.

    $response = Http::post(...);

    // 클라이언트나 서버 에러가 발생하면 예외를 던진다...
    $response->throw();

    return $response['user']['id'];

The `Illuminate\Http\Client\RequestException` instance has a public `$response` property which will allow you to inspect the returned response.

`Illuminate\Http\Client\RequestException` 인스턴스는 반환된 응답을 검사하는데 사용할 수 있는 `$response`라는 공개 속성을 가지고 있습니다.

The `throw` method returns the response instance if no error occurred, allowing you to chain other operations onto the `throw` method:

`throw` 메서드를 써도 실제로 에러가 발생하지 않으면 응답 인스턴스를 반환하기 때문에 `throw` 메서드에 다른 작업을 연결할 수 있습니다.

    return Http::post(...)->throw()->json();

<a name="guzzle-options"></a>
### Guzzle Options
### Guzzle Options

You may specify additional [Guzzle request options](http://docs.guzzlephp.org/en/stable/request-options.html) using the `withOptions` method. The `withOptions` method accepts an array of key / value pairs:

`withOptions` 메소드를 사용하여 추가 [Guzzle request options](http://docs.guzzlephp.org/en/stable/request-options.html) 를 지정할 수 있습니다. `withOptions` 메소드는 키 / 값 쌍의 배열을 받습니다.

    $response = Http::withOptions([
        'debug' => true,
    ])->get('http://test.com/users');

<a name="testing"></a>
## Testing
## 테스트

Many Laravel services provide functionality to help you easily and expressively write tests, and Laravel's HTTP wrapper is no exception. The `Http` facade's `fake` method allows you to instruct the HTTP client to return stubbed / dummy responses when requests are made.

많은 라라벨 서비스가 테스트 작성에 도움을 주는 기능을 제공하는데, 라라벨의 HTTP 클라이언트도 예외가 아닙니다. `Http` 퍼사드의 `fake` 메서드를 사용하면 요청이 만들어졌을 때 스텁 / 더미 응답을 반환하도록 HTTP 클라이언트에게 지시할 수 있습니다.

<a name="faking-responses"></a>
### Faking Responses
### 응답 속이기

For example, to instruct the HTTP client to return empty, `200` status code responses for every request, you may call the `fake` method with no arguments:

예를 들어, 모든 요청에 대해 아무것도 리턴하지 않고 `200` 상태 코드를 응답하도록 HTTP 클라이언트에게 지시하려면 아무 인자 없이 `fake` 메서드를 호출하면 됩니다.

    use Illuminate\Support\Facades\Http;

    Http::fake();

    $response = Http::post(...);

#### Faking Specific URLs
#### 특정 URL 속이기

Alternatively, you may pass an array to the `fake` method. The array's keys should represent URL patterns that you wish to fake and their associated responses. The `*` character may be used as a wildcard character. Any requests made to URLs that have not been faked will actually be executed. You may use the `response` method to construct stub / fake responses for these endpoints:

한편, `fake` 메서드에 배열을 넘겨줄 수도 있습니다. 배열의 키는 속이고자 하는 URL 패턴을 나타내고 값은 응답을 나타냅니다. `*` 문자는 와일드 카드로 사용할 수 있습니다. 속이지 않은 모든 URL은 실제로 수행됩니다. 스텁 / 가짜 응답을 만드는데는 `response` 메서드를 사용합니다.

    Http::fake([
        // 깃헙 엔드포인트용 JSON 응답 스텁...
        'github.com/*' => Http::response(['foo' => 'bar'], 200, ['Headers']),

        // 구글 엔드포인트용 문자열 응답 스텁...
        'google.com/*' => Http::response('Hello World', 200, ['Headers']),
    ]);

If you would like to specify a fallback URL pattern that will stub all unmatched URLs, you may use a single `*` character:

일치하지 않는 모든 URL용 대체 URL 패턴을 지정하고 싶으면 `*` 문자를 사용하면 됩니다.

    Http::fake([
        // 깃헙 엔드포인트용 JSON 응답 스텁...
        'github.com/*' => Http::response(['foo' => 'bar'], 200, ['Headers']),

        // 다른 모든 엔드 포인트용 문자열 응답 스텁...
        '*' => Http::response('Hello World', 200, ['Headers']),
    ]);

#### Faking Response Sequences
#### 응답 시퀀스 속이기

Sometimes you may need to specify that a single URL should return a series of fake responses in a specific order. You may accomplish this using the `Http::sequence` method to build the responses:

단일 URL이 여러 가짜 응답을 특정한 순서로 반환할 필요가 있을 때가 있습니다. 이를 위해서는 `Http::sequence` 메서드를 사용해서 응답을 만들면 됩니다.

    Http::fake([
        // 깃헙 엔드포인트용 다중 응답 스텁...
        'github.com/*' => Http::sequence()
                                ->push('Hello World', 200)
                                ->push(['foo' => 'bar'], 200)
                                ->pushStatus(404),
    ]);

When all of the responses in a response sequence have been consumed, any further requests will cause the response sequence to throw an exception. If you would like to specify a default response that should be returned when a sequence is empty, you may use the `whenEmpty` method:

응답 시퀀스에 있는 모든 응답이 소비되고 나면 그 이후에 응답 시퀀스로 보내는 요청은 예외가 발생합니다. 시퀀스가 비어있을 때의 기본 응답을 지정하고 싶으면 `whenEmpty` 메서드를 사용하면 됩니다.

    Http::fake([
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

#### Fake Callback
#### 가짜 콜백

If you require more complicated logic to determine what responses to return for certain endpoints, you may pass a callback to the `fake` method. This callback will receive an instance of `Illuminate\Http\Client\Request` and should return a response instance:

특정 엔드포인트가 어떤 응답을 반환해야하는지 결정하는데 더욱 더 복잡한 로직이 필요하다면 `fake` 메서드에 콜백을 넘겨줄 수 있습니다. 이 콜백은 `Illuminate\Http\Client\Request` 인스턴스를 받고 응답 인스턴스를 반환해야 합니다.

    Http::fake(function ($request) {
        return Http::response('Hello World', 200);
    });

<a name="inspecting-requests"></a>
### Inspecting Requests
### 요청 검사하기

When faking responses, you may occasionally wish to inspect the requests the client receives in order to make sure your application is sending the correct data or headers. You may accomplish this by calling the `Http::assertSent` method after calling `Http::fake`.

응답을 속일 때 제대로 된 데이터와 헤더를 보내는지 확인하기 위해 클라이언트가 받는 요청을 검사하고 싶을 때가 있습니다. `Http::fake`를 호출한 이후에 `Http::assertSent` 메서드를 호출해서 검사할 수 있습니다.

The `assertSent` method accepts a callback which will be given an `Illuminate\Http\Client\Request` instance and should return a boolean value indicating if the request matches your expectations. In order for the test to pass, at least one request must have been issued matching the given expectations:

`assertSent` 메서드는 콜백을 받는데, 이 콜백은 `Illuminate\Http\Client\Request` 인스턴스를 인자로 받고 요청이 기대한 것과 일치하는지를 나타내는 불리언 값을 반환합니다. 테스트가 통과하기 위해서는 주어진 기대와 일치하는 요청이 최소한 한 번은 발행되어야 합니다.

    Http::fake();

    Http::withHeaders([
        'X-First' => 'foo',
    ])->post('http://test.com/users', [
        'name' => 'Taylor',
        'role' => 'Developer',
    ]);

    Http::assertSent(function ($request) {
        return $request->hasHeader('X-First', 'foo') &&
               $request->url() == 'http://test.com/users' &&
               $request['name'] == 'Taylor' &&
               $request['role'] == 'Developer';
    });

If needed, you may assert that a specific request was not sent using the `assertNotSent` method:

필요한 경우 `assertNotSent` 메소드를 사용하여 특정 요청을 전송하지 않았는지 검증 할 수 있습니다.

    Http::fake();

    Http::post('http://test.com/users', [
        'name' => 'Taylor',
        'role' => 'Developer',
    ]); 

    Http::assertNotSent(function (Request $request) {
        return $request->url() === 'http://test.com/posts';
    });

Or, if you would like to assert that no requests were sent, you may use the `assertNothingSent` method:

또는 요청이 전송되지 않았는지 검증하려면 `assertNothingSent` 메소드를 사용할 수 있습니다.

    Http::fake();

    Http::assertNothingSent();
