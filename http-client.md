# HTTP 클라이언트

- [소개](#introduction)
- [Request 만들기](#making-requests)
    - [Request 데이터](#request-data)
    - [헤더](#headers)
    - [인증](#authentication)
    - [시간초과](#timeout)
    - [재시도](#retries)
    - [에러 처리](#error-handling)
    - [Guzzle 미들웨어](#guzzle-middleware)
    - [Guzzle 옵션](#guzzle-options)
- [동시 요청](#concurrent-requests)
- [매크로](#macros)
- [테스팅](#testing)
    - [응답 속이기](#faking-responses)
    - [Request 검사하기](#inspecting-requests)
    - [스트레이 요청 방지](#preventing-stray-requests)
- [Events](#events)
- [이벤트](#events)

<a name="introduction"></a>
## 소개

라라벨은 [Guzzle HTTP 클라이언트](http://docs.guzzlephp.org/en/stable/)를 기반으로 다른 웹 애플리케이션과 소통하기 위해 외부로 보내는 HTTP Request를 신속히 만들 수 있는 표현력 있고 미니멀한 API를 제공합니다. 라라벨 HTTP 클라이언트는 가장 흔한 사용 사례와 좋은 개발자 경험에 촛점을 맞추고 있습니다.

시작하기에 앞서 HTTP 클라이언트가 의존하는 Guzzle 패키지가 애플리케이션에 설치되어 있는지 확인해야 합니다. 기본적으로 라라벨은 이 의존성을 자동으로 불러들입니다. 이전에 패키지를 제거했다면 Composer를 통해 다시 설치할 수 있습니다.

```shell
composer require guzzlehttp/guzzle
```

<a name="making-requests"></a>
## Request 만들기

Request를 만드는데 `Http` 파사드에서 제공하는 `head`, `get`, `post`, `put`, `patch`, `delete` 메서드를 사용할 수 있습니다. 우선 다른 URL에 기본적인 `GET` Request를 하는 방법을 알아봅시다.

    use Illuminate\Support\Facades\Http;

    $response = Http::get('http://example.com');

`get` 메소드는 응답을 검사하는 데 사용할 수 있는 다양한 메소드를 제공하는 `Illuminate\Http\Client\Response`의 인스턴스를 반환합니다.

    $response->body() : string;
    $response->json($key = null, $default = null) : array|mixed;
    $response->object() : object;
    $response->collect($key = null) : Illuminate\Support\Collection;
    $response->status() : int;
    $response->successful() : bool;
    $response->redirect(): bool;
    $response->failed() : bool;
    $response->clientError() : bool;
    $response->header($header) : string;
    $response->headers() : array;

`Illuminate\Http\Client\Response` 객체는 PHP `ArrayAccess` 인터페이스도 구현하고 있기 때문에 응답에서 JSON 응답 데이터에 바로 접근할 수 있습니다.

    return Http::get('http://example.com/users/1')['name'];

위의 목록에 표시된 메서드 이외에도 다음의 메서드를 사용하여 응답에 주어진 상태코드가 있는지 확인할 수 있습니다. 

    $response->ok() : bool;                  // 200 OK
    $response->created() : bool;             // 201 Created
    $response->accepted() : bool;            // 202 Accepted
    $response->noContent() : bool;           // 204 No Content
    $response->movedPermanently() : bool;    // 301 Moved Permanently
    $response->found() : bool;               // 302 Found
    $response->badRequest() : bool;          // 400 Bad Request
    $response->unauthorized() : bool;        // 401 Unauthorized
    $response->paymentRequired() : bool;     // 402 Payment Required
    $response->forbidden() : bool;           // 403 Forbidden
    $response->notFound() : bool;            // 404 Not Found
    $response->requestTimeout() : bool;      // 408 Request Timeout
    $response->conflict() : bool;            // 409 Conflict
    $response->unprocessableEntity() : bool; // 422 Unprocessable Entity
    $response->tooManyRequests() : bool;     // 429 Too Many Requests
    $response->serverError() : bool;         // 500 Internal Server Error

<a name="uri-templates"></a>
#### URI Templates
#### URI Templates

The HTTP client also allows you to construct request URLs using the [URI template specification](https://www.rfc-editor.org/rfc/rfc6570). To define the URL parameters that can be expanded by your URI template, you may use the `withUrlParameters` method:

HTTP 클라이언트를 사용하면 [URI 템플릿 스펙](https://www.rfc-editor.org/rfc/rfc6570)을 사용해 HTTP 요청 URL을 생성할 수도 있습니다. URI 템플릿으로 확장할 수 있는 URL 파라미터를 정의하려면 다음과 같이 할 수 있습니다. 

```php
Http::withUrlParameters([
    'endpoint' => 'https://laravel.com',
    'page' => 'docs',
    'version' => '9.x',
    'topic' => 'validation',
])->get('{+endpoint}/{page}/{version}/{topic}');
```

<a name="dumping-requests"></a>
#### Request의 dump하기

외부로 Request를 보내기 전에 dump 후 스크립트 실행을 종료하려면 Request 정의의 시작 부분에 `dd` 메서드를 추가할 수 있습니다.

    return Http::dd()->get('http://example.com');

<a name="request-data"></a>
### Request 데이터

당연한 이야기이지만, 보통 `POST`, `PUT`, `PATCH`를 사용할 때 Request에 추가적인 데이터를 함께 보냅니다. 그래서 이 메서드들은 두 번째 인자로 데이터의 배열을 받습니다. 기본적으로 데이터는 `application/json` 컨텐츠 타입으로 전송됩니다.

    use Illuminate\Support\Facades\Http;

    $response = Http::post('http://example.com/users', [
        'name' => 'Steve',
        'role' => 'Network Administrator',
    ]);

<a name="get-request-query-parameters"></a>
#### GET 리퀘스트 쿼리 파라메터

`GET` Request를 할 때 쿼리스트링을 URL에 직접 추가하거나 키 / 값 쌍의 배열을 `get` 메소드의 두 번째 인수로 전달할 수 있습니다.

    $response = Http::get('http://example.com/users', [
        'name' => 'Taylor',
        'page' => 1,
    ]);

<a name="sending-form-url-encoded-requests"></a>
#### 폼 URL 인코디드 Request 전송하기

`application/x-www-form-urlencoded` 컨텐츠 타입으로 데이터를 전송하고 싶으면 Request를 만들기 전에 `asForm` 메서드를 호출해야 합니다.

    $response = Http::asForm()->post('http://example.com/users', [
        'name' => 'Sara',
        'role' => 'Privacy Consultant',
    ]);

<a name="sending-a-raw-request-body"></a>
#### Raw Body Request 보내기

Request할 때 raw request body를 발송하려면 `withBody` 메서드를 사용할 수 있습니다. 콘텐츠 유형은 메소드의 두 번째 인수를 통해 지정할 수 있습니다.

    $response = Http::withBody(
        base64_encode($photo), 'image/jpeg'
    )->post('http://example.com/photo');

<a name="multi-part-requests"></a>
#### 멀티-파트 Request

파일을 멀티-파트 Request로 보내고 싶으면 Request를 만들기 전에 `attach` 메서드를 호출해야 합니다. 이 메서드는 파일의 이름과 내용을 인자로 받습니다. 필요하면 세 번째 인자로 파일명을 넘겨줄 수도 있습니다.

    $response = Http::attach(
        'attachment', file_get_contents('photo.jpg'), 'photo.jpg'
    )->post('http://example.com/attachments');

파일의 원시 내용을 전달하는 대신 스트림 리소스를 전달할 수 있습니다.

    $photo = fopen('photo.jpg', 'r');

    $response = Http::attach(
        'attachment', $photo, 'photo.jpg'
    )->post('http://example.com/attachments');

<a name="headers"></a>
### 헤더

`withHeaders` 메서드를 이용해서 헤더를 추가할 수 있습니다. `withHeaders` 메서드는 키 / 값 쌍으로 이뤄진 배열을 받습니다.

    $response = Http::withHeaders([
        'X-First' => 'foo',
        'X-Second' => 'bar'
    ])->post('http://example.com/users', [
        'name' => 'Taylor',
    ]);

`accept` 메소드를 사용하여 Request에 대한 응답으로 애플리케이션이 기대하는 콘텐츠 유형을 지정할 수 있습니다.

    $response = Http::accept('application/json')->get('http://example.com/users');

편의를 위해 `acceptJson` 메소드를 사용하여 애플리케이션이 Request에 대한 응답으로 `application/json` 콘텐츠 유형을 예상하도록 빠르게 지정할 수 있습니다.

    $response = Http::acceptJson()->get('http://example.com/users');

<a name="authentication"></a>
### 인증

`withBasicAuth`와 `withDigestAuth` 메서드를 이용해서 기본 인증을 사용할 건지 다이제스트 인증을 사용할 건지 지정할 수 있습니다.

    // Basic authentication...
    $response = Http::withBasicAuth('taylor@laravel.com', 'secret')->post(/* ... */);

    // Digest authentication...
    $response = Http::withDigestAuth('taylor@laravel.com', 'secret')->post(/* ... */);

<a name="bearer-tokens"></a>
#### Bearer 토큰

Request 헤더에 `Authorization` bearer 토큰을 추가하고 싶으면 `withToken` 메서드를 사용하면 됩니다.

    $response = Http::withToken('token')->post(/* ... */);

<a name="timeout"></a>
### 시간초과

`timeout` 메소드는 응답을 기다리는 최대 시간 (초)을 지정하는 데 사용될 수 있습니다.

    $response = Http::timeout(3)->get(/* ... */);

주어진 타임 아웃이 초과되면 `Illuminate\Http\Client\ConnectionException`의 인스턴스가 발생합니다.

`connectTimeout` 메소드를 사용하면 서버에 연결을 시도하는 동안 대기할 최대 시간(초)을 지정할 수 있습니다.

    $response = Http::connectTimeout(3)->get(/* ... */);

<a name="retries"></a>
### 재시도

클라이언트나 서버 에러 발생 시 자동으로 재시도하고 싶으면 `retry` 메서드를 사용하면 됩니다. `retry` 메서드는 두 개의 인자를 받습니다. 하나는 재시도할 횟수이고 다른 하나는 재시도 간 간격(밀리초)입니다.


    $response = Http::retry(3, 100)->post(/* ... */);

필요한 경우 `retry` 메서드에 세 번째 인수를 전달할 수 있습니다. 세 번째 인수는 재시도가 실제로 시도되어야 하는지 여부를 결정하는 callable이어야 합니다. 예를 들어, 초기 Request에서 `ConnectionException`이 발생한 경우에만 Request를 재시도할 수 있습니다.

    $response = Http::retry(3, 100, function ($exception, $request) {
        return $exception instanceof ConnectionException;
    })->post(/* ... */);

요청이 실패하는 경우 새 시도를 하기 전에 변화를 주고 싶을 수 있습니다. `retry` 메서드에 제공한 콜러블에 제공한 요청 인자를 수정하여 이를 달성할 수 있습니다. 예를 들어, 첫 번째 시도가 인증 에러를 반환하면 새 인증 토큰으로 재시도 할 수 있습니다.

    $response = Http::withToken($this->getToken())->retry(2, 0, function ($exception, $request) {
        if (! $exception instanceof RequestException || $exception->response->status() !== 401) {
            return false;
        }

        $request->withToken($this->getNewToken());

        return true;
    })->post(/* ... */);

모든 Request가 실패하면 `Illuminate\Http\Client\RequestException` 인스턴스를 던집니다. 이 동작을 비활성화하려면 `throw` 인자를 `false` 로 지정하면 됩니다. 이 동작이 비활성화되면 모든 재시도가 시도된 후 클라이언트가 수신한 마지막 응답이 반환됩니다.

    $response = Http::retry(3, 100, throw: false)->post(/* ... */);

> **Warning**
> 커넥션 문제로 모든 요청이 실패하는 경우 `throw` 인자가 `false`로 설정되어 있어도 `Illuminate\Http\Client\ConnectionException` 가 던져질 것입니다.

<a name="error-handling"></a>
### 에러 처리

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

    // Immediately execute the given callback if there was a client or server error...
    // 클라이언트 또는 서버 오류가 발생한 경우 지정된 콜백을 즉시 실행...
    $response->onError(callable $callback);

<a name="throwing-exceptions"></a>
#### 예외 던지기

응답이 클라이언트나 서버 에러일 경우 `Illuminate\Http\Client\RequestException` 인스턴스를 던지고 싶으면 `throw` 메서드를 사용하면 됩니다.

    $response = Http::post(/* ... */);

    // Throw an exception if a client or server error occurred...
    // 클라이언트나 서버 에러가 발생하면 예외를 던진다...
    $response->throw();

    // Throw an exception if an error occurred and the given condition is true...
    // 오류가 발생하고 조건에 해당하는 경우 예외를 던진다...
    $response->throwIf($condition);
    
    // Throw an exception if an error occurred and the given closure resolves to true...
    // 오류가 발생하고 클로저가 true를 반환하는 경우 예외를 던진다...
    $response->throwIf(fn ($response) => true);

    // Throw an exception if an error occurred and the given condition is false...
    // 오류가 발생하고 조건에 해당하지 않는 경우 예외를 던진다...
    $response->throwUnless($condition);

    // Throw an exception if an error occurred and the given closure resolves to false...
    // 오류가 발생하고 클로저가 false를 반환하는 경우 예외를 던진다...
    $response->throwUnless(fn ($response) => false);

    // Throw an exception if the response has a specific status code...
    // 응답이 지정된 상태코드를 가진다면 예외를 던진다...
    $response->throwIfStatus(403);

    // Throw an exception unless the response has a specific status code...
    // 응답이 지정된 상태코드가 아니라면 예외를 던진다...
    $response->throwUnlessStatus(200);

    return $response['user']['id'];

`Illuminate\Http\Client\RequestException` 인스턴스는 반환된 응답을 검사하는데 사용할 수 있는 `$response`라는 공개 속성을 가지고 있습니다.

`throw` 메서드를 써도 실제로 에러가 발생하지 않으면 응답 인스턴스를 반환하기 때문에 `throw` 메서드에 다른 작업을 연결할 수 있습니다.

    return Http::post(/* ... */)->throw()->json();

예외가 발생하기 전에 몇 가지 추가 로직을 수행하려면 `throw` 메소드에 클로저를 전달할 수 있습니다. 클로저가 호출된 후 예외가 자동으로 throw되므로 클로저 내에서 예외를 다시 throw할 필요가 없습니다.

    return Http::post(/* ... */)->throw(function ($response, $e) {
        //
    })->json();

<a name="guzzle-middleware"></a>
### Guzzle 미들웨어

라라벨의 HTTP 클라이언트가 Guzzle에 기반하기 때문에 밖으로 나가는 요청을 조작하거나 들어오는 요청을 검사하는데 [Guzzle 미들웨어](https://docs.guzzlephp.org/en/stable/handlers-and-middleware.html)을 활용할 수 있습니다. 밖으로 나가는 요청을 조작하려면 Guzzle의 `mapRequest` 미들웨어 팩토리와 함께 `withMiddleware` 메서드를 사용해 Guzzle 미들웨어를 등록하세요.

    use GuzzleHttp\Middleware;
    use Illuminate\Support\Facades\Http;
    use Psr\Http\Message\RequestInterface;

    $response = Http::withMiddleware(
        Middleware::mapRequest(function (RequestInterface $request) {
            $request = $request->withHeader('X-Example', 'Value');
            
            return $request;
        })
    )->get('http://example.com');

마찬가지로 들어오는 HTTP 응답을 `mapResponse` 미들웨어 팩토리와 함께 `withMiddleware` 메서드를 통해 미들웨어를 등록하여 검사할 수 있습니다.

    use GuzzleHttp\Middleware;
    use Illuminate\Support\Facades\Http;
    use Psr\Http\Message\ResponseInterface;

    $response = Http::withMiddleware(
        Middleware::mapResponse(function (ResponseInterface $response) {
            $header = $response->getHeader('X-Example');

            // ...
            
            return $response;
        })
    )->get('http://example.com');

<a name="guzzle-options"></a>
### Guzzle Options

`withOptions` 메소드를 사용하여 추가 [Guzzle request options](http://docs.guzzlephp.org/en/stable/request-options.html) 를 지정할 수 있습니다. `withOptions` 메소드는 키 / 값 쌍의 배열을 받습니다.

    $response = Http::withOptions([
        'debug' => true,
    ])->get('http://example.com/users');

<a name="concurrent-requests"></a>
## 동시 요청

여러 HTTP Request를 동시에 보내고 싶을 수도 있습니다. 즉, Request를 순차적으로 보내는 대신 여러 Request를 동시에 보내려고 하는 것입니다. 이는 느린 HTTP API와 상호 작용할 때 높은 성능 향상으로 이어질 수 있습니다.

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
## 매크로

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

매크로가 구성되면 애플리케이션의 어디에서나 매크로를 호출하여 지정된 구성으로 대기 중인 Request를 생성할 수 있습니다.

```php
$response = Http::github()->get('/');
```

<a name="testing"></a>
## 테스팅

많은 라라벨 서비스가 테스트 작성에 도움을 주는 기능을 제공하는데, 라라벨의 HTTP 클라이언트도 예외가 아닙니다. `Http` 퍼사드의 `fake` 메서드를 사용하면 Request가 만들어졌을 때 스텁 / 더미 응답을 반환하도록 HTTP 클라이언트에게 지시할 수 있습니다.

<a name="faking-responses"></a>
### 응답 속이기

예를 들어, 모든 Request에 대해 아무것도 리턴하지 않고 `200` 상태 코드를 응답하도록 HTTP 클라이언트에게 지시하려면 아무 인자 없이 `fake` 메서드를 호출하면 됩니다.

    use Illuminate\Support\Facades\Http;

    Http::fake();

    $response = Http::post(/* ... */);

<a name="faking-specific-urls"></a>
#### 특정 URL 속이기

한편, `fake` 메서드에 배열을 넘겨줄 수도 있습니다. 배열의 키는 속이고자 하는 URL 패턴을 나타내고 값은 응답을 나타냅니다. `*` 문자는 와일드 카드로 사용할 수 있습니다. 속이지 않은 모든 URL은 실제로 수행됩니다. 스텁 / 가짜 응답을 만드는데는 `Http` 파사드의 `response` 메서드를 사용합니다.

    Http::fake([
        // Stub a JSON response for GitHub endpoints...
        // 깃헙 엔드포인트용 JSON 응답 스텁...
        'github.com/*' => Http::response(['foo' => 'bar'], 200, $headers),

        // Stub a string response for Google endpoints...
        // 구글 엔드포인트용 문자열 응답 스텁...
        'google.com/*' => Http::response('Hello World', 200, $headers),
    ]);

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
#### 응답 시퀀스 속이기

단일 URL이 여러 가짜 응답을 특정한 순서로 반환할 필요가 있을 때가 있습니다. 이를 위해서는 `Http::sequence` 메서드를 사용해서 응답을 만들면 됩니다.

    Http::fake([
        // Stub a series of responses for GitHub endpoints...
        // 깃헙 엔드포인트용 다중 응답 스텁...
        'github.com/*' => Http::sequence()
                                ->push('Hello World', 200)
                                ->push(['foo' => 'bar'], 200)
                                ->pushStatus(404),
    ]);

응답 시퀀스에 있는 모든 응답이 소비되고 나면 그 이후에 응답 시퀀스로 보내는 Request는 예외가 발생합니다. 시퀀스가 비어있을 때의 기본 응답을 지정하고 싶으면 `whenEmpty` 메서드를 사용하면 됩니다.

    Http::fake([
        // Stub a series of responses for GitHub endpoints...
        // 깃헙 엔드포인트용 다중 응답 스텁...
        'github.com/*' => Http::sequence()
                                ->push('Hello World', 200)
                                ->push(['foo' => 'bar'], 200)
                                ->whenEmpty(Http::response()),
    ]);

응답 시퀀스를 속이되 URL 패턴을 지정할 필요가 없을 때는 `Http::fakeSequence` 메서드를 사용하면 됩니다.

    Http::fakeSequence()
            ->push('Hello World', 200)
            ->whenEmpty(Http::response());

<a name="fake-callback"></a>
#### 가짜 콜백

특정 엔드포인트가 어떤 응답을 반환해야하는지 결정하는데 더욱 더 복잡한 로직이 필요하다면 `fake` 메서드에 콜백을 넘겨줄 수 있습니다. 이 콜백은 `Illuminate\Http\Client\Request` 인스턴스를 받고 응답 인스턴스를 반환해야 합니다.

    use Illuminate\Http\Client\Request;

    Http::fake(function (Request $request) {
        return Http::response('Hello World', 200);
    });

<a name="preventing-stray-requests"></a>
### 스트레이 요청 방지

HTTP 클라이언트를 통해 보내지는 모든 요청을 개별 테스트나 전체 테스트 스위트에서 위조된 것으로 한정하고 싶은 경우 `preventStrayRequests` 메서드를 호출하면 됩니다. 이 메서드를 호출하고 나면 가짜 응답을 갖지 않은 모든 요청은 실제 HTTP 요청을 만드는 대신 예외를 던지게 됩니다.

    use Illuminate\Support\Facades\Http;

    Http::preventStrayRequests();

    Http::fake([
        'github.com/*' => Http::response('ok'),
    ]);

    // An "ok" response is returned...
    Http::get('https://github.com/laravel/framework');

    // An exception is thrown...
    Http::get('https://laravel.com');

<a name="inspecting-requests"></a>
### Request 검사하기

응답을 속일 때 제대로 된 데이터와 헤더를 보내는지 확인하기 위해 클라이언트가 받는 Request를 검사하고 싶을 때가 있습니다. `Http::fake`를 호출한 이후에 `Http::assertSent` 메서드를 호출해서 검사할 수 있습니다.

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

`assertSentCount` 메소드를 사용하여 테스트 중에 "보낸" 요청 수를 확인할 수 있습니다.

    Http::fake();

    Http::assertSentCount(5);

또는 Request가 전송되지 않았는지 검증하려면 `assertNothingSent` 메소드를 사용할 수 있습니다.

    Http::fake();

    Http::assertNothingSent();

<a name="recording-requests-and-responses"></a>
#### 요청 / 응답 기록하기

모든 요청과 그에 해당하는 응답을 모으기 위해 `recorded` 메서드를 사용할 수 있습니다. `recorded` 메서드는 `Illuminate\Http\Client\Request` 와 `Illuminate\Http\Client\Response` 인스턴스를 담은 배열의 컬렉션을 반환합니다.

```php
Http::fake([
    'https://laravel.com' => Http::response(status: 500),
    'https://nova.laravel.com/' => Http::response(),
]);

Http::get('https://laravel.com');
Http::get('https://nova.laravel.com/');

$recorded = Http::recorded();

[$request, $response] = $recorded[0];
```

추가적으로 `recorded` 메서드는 `Illuminate\Http\Client\Request` 와 `Illuminate\Http\Client\Response` 인스턴스를 받고 기대에 따라 요청 / 응답 쌍을 필터링하는 데 사용할 수 있는 클로저를 허용합니다.

```php
use Illuminate\Http\Client\Request;
use Illuminate\Http\Client\Response;

Http::fake([
    'https://laravel.com' => Http::response(status: 500),
    'https://nova.laravel.com/' => Http::response(),
]);

Http::get('https://laravel.com');
Http::get('https://nova.laravel.com/');

$recorded = Http::recorded(function (Request $request, Response $response) {
    return $request->url() !== 'https://laravel.com' &&
           $response->successful();
});
```

<a name="events"></a>
## 이벤트

Laravel은 HTTP Request를 보내는 과정에서 세 가지 이벤트를 발생시킵니다. `RequestSending` 이벤트는 Request가 전송되기 전에 발생하는 반면, `ResponseReceived` 이벤트는 주어진 Request에 대한 응답이 수신된 후에 발생합니다. 주어진 Request에 대한 응답이 수신되지 않으면 `ConnectionFailed` 이벤트가 발생합니다.

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
