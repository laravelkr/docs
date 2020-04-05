# HTTP 클라이언트

- [소개](#introduction)
- [요청 만들기](#making-requests)
    - [요청 데이터](#request-data)
    - [헤더](#headers)
    - [인증](#authentication)
    - [시간초과](#timeout)
    - [재시도](#retries)
    - [에러 처리](#error-handling)
    - [Guzzle Options](#guzzle-options)
- [Testing](#testing)
    - [응답 속이기](#faking-responses)
    - [요청 검사하기](#inspecting-requests)

<a name="introduction"></a>
## 소개

라라벨은 [Guzzle HTTP 클라이언트](http://docs.guzzlephp.org/en/stable/)를 기반으로 다른 웹 애플리케이션과 소통하기 위해 외부로 보내는 HTTP 요청을 신속히 만들 수 있는 표현력 있고 미니멀한 API를 제공합니다. 라라벨 HTTP 클라이언트는 가장 흔한 사용 사례와 좋은 개발자 경험에 촛점을 맞추고 있습니다.

시작하기에 앞서 HTTP 클라이언트가 의존하는 Guzzle 패키지가 애플리케이션에 설치되어 있는지 확인해야 합니다. 기본적으로 라라벨은 이 의존성을 자동으로 불러들입니다.

    composer require guzzlehttp/guzzle

<a name="making-requests"></a>
## 요청 만들기

요청을 만드는데 `get`, `post`, `put`, `patch`, `delete` 메서드를 사용할 수 있습니다. 우선 기본적인 `GET` 요청부터 알아봅시다.

    use Illuminate\Support\Facades\Http;

    $response = Http::get('http://test.com');

`get` 메서드는 `Illuminate\Http\Client\Response` 인스턴스를 반환합니다. 이 인스턴스는 응답을 검사할 때 사용하는 다양한 메서드를 제공합니다.

    $response->body() : string;
    $response->json() : array;
    $response->status() : int;
    $response->ok() : bool;
    $response->successful() : bool;
    $response->serverError() : bool;
    $response->clientError() : bool;
    $response->header($header) : string;
    $response->headers() : array;

`Illuminate\Http\Client\Response` 객체는 PHP `ArrayAccess` 인터페이스도 구현하고 있기 때문에 응답에서 JSON 응답 데이터에 바로 접근할 수 있습니다.

    return Http::get('http://test.com/users/1')['name'];

<a name="request-data"></a>
### 요청 데이터

당연한 이야기이지만, 보통 `POST`, `PUT`, `PATCH`를 사용할 때 요청에 추가적인 데이터를 함께 보냅니다. 그래서 이 메서드들은 두 번째 인자로 데이터의 배열을 받습니다. 기본적으로 데이터는 `application/json` 컨텐트 타입으로 전송됩니다.

    $response = Http::post('http://test.com/users', [
        'name' => 'Steve',
        'role' => 'Network Administrator',
    ]);

#### 폼 URL 인코디드 요청 전송하기

`application/x-www-form-urlencoded` 컨텐트 타입으로 데이터를 전송하고 싶으면 요청을 만들기 전에 `asForm` 메서드를 호출해야 합니다.

    $response = Http::asForm()->post('http://test.com/users', [
        'name' => 'Sara',
        'role' => 'Privacy Consultant',
    ]);

#### 멀티-파트 요청

파일을 멀티-파트 요청으로 보내고 싶으면 요청을 만들기 전에 `attach` 메서드를 호출해야 합니다. 이 메서드는 파일의 이름과 내용을 인자로 받습니다. 필요하면 세 번째 인자로 파일명을 넘겨줄 수도 있습니다.

    $response = Http::attach(
        'attachment', file_get_contents('photo.jpg'), 'photo.jpg'
    )->post('http://test.com/attachments');

파일의 원시 내용을 전달하는 대신 스트림 리소스를 전달할 수도 있습니다.

    $photo = fopen('photo.jpg', 'r');

    $response = Http::attach(
        'attachment', $photo, 'photo.jpg'
    )->post('http://test.com/attachments');

<a name="headers"></a>
### 헤더

`withHeaders` 메서드를 이용해서 헤더를 추가할 수 있습니다. `withHeaders` 메서드는 키 / 값 쌍으로 이뤄진 배열을 받습니다.

    $response = Http::withHeaders([
        'X-First' => 'foo',
        'X-Second' => 'bar'
    ])->post('http://test.com/users', [
        'name' => 'Taylor',
    ]);

<a name="authentication"></a>
### 인증

`withBasicAuth`와 `withDigestAuth` 메서드를 이용해서 기본 인증을 사용할 건지 다이제스트 인증을 사용할 건지 지정할 수 있습니다.

    // Basic authentication...
    $response = Http::withBasicAuth('taylor@laravel.com', 'secret')->post(...);

    // Digest authentication...
    $response = Http::withDigestAuth('taylor@laravel.com', 'secret')->post(...);

#### Bearer 토큰

요청 헤더에 `Authorization` bearer 토큰을 추가하고 싶으면 `withToken` 메서드를 사용하면 됩니다.

    $response = Http::withToken('token')->post(...);

<a name="timeout"></a>
### 시간초과

`timeout` 메소드는 응답을 기다리는 최대 시간 (초)을 지정하는 데 사용될 수 있습니다.

    $response = Http::timeout(3)->get(...);

주어진 타임 아웃이 초과되면 `Illuminate\Http\Client\ConnectionException`의 인스턴스가 발생합니다.

<a name="retries"></a>
### 재시도

클라이언트나 서버 에러 발생 시 자동으로 재시도하고 싶으면 `retry` 메서드를 사용하면 됩니다. `retry` 메서드는 두 개의 인자를 받습니다. 하나는 재시도할 횟수이고 다른 하나는 재시도 간 간격(밀리초)입니다.

    $response = Http::retry(3, 100)->post(...);

모든 요청이 실패하면 `Illuminate\Http\Client\RequestException` 인스턴스를 던집니다.

<a name="error-handling"></a>
### 에러 처리

Guzzle의 기본 동작과는 달리 라라벨 HTTP 클라이언트는 클라이언트나 서버 에러에 대해 예외(서버에서 보내는 `400`과 `500`대의 응답)를 던지지 않습니다. `successful`, `clientError`, `serverError` 메서드를 이용해서 에러가 반환되는지 판단할 수 있습니다.

    // 상태 코드가 200 이상 300 미만인지 판단...
    $response->successful();

    // 응답 코드가 400대인지 판단...
    $response->clientError();

    // 응답 코드가 500대인지 판단...
    $response->serverError();

#### 예외 던지기

응답이 클라이언트나 서버 에러일 경우 `Illuminate\Http\Client\RequestException` 인스턴스를 던지고 싶으면 `throw` 메서드를 사용하면 됩니다.

    $response = Http::post(...);

    // 클라이언트나 서버 에러가 발생하면 예외를 던진다...
    $response->throw();

    return $response['user']['id'];

`Illuminate\Http\Client\RequestException` 인스턴스는 반환된 응답을 검사하는데 사용할 수 있는 `$response`라는 공개 속성을 가지고 있습니다.

`throw` 메서드를 써도 실제로 에러가 발생하지 않으면 응답 인스턴스를 반환하기 때문에 `throw` 메서드에 다른 작업을 연결할 수 있습니다.

    return Http::post(...)->throw()->json();

<a name="guzzle-options"></a>
### Guzzle Options

`withOptions` 메소드를 사용하여 추가 [Guzzle request options](http://docs.guzzlephp.org/en/stable/request-options.html) 를 지정할 수 있습니다. `withOptions` 메소드는 키 / 값 쌍의 배열을 받습니다.

    $response = Http::withOptions([
        'debug' => true,
    ])->get('http://test.com/users');

<a name="testing"></a>
## 테스트

많은 라라벨 서비스가 테스트 작성에 도움을 주는 기능을 제공하는데, 라라벨의 HTTP 클라이언트도 예외가 아닙니다. `Http` 퍼사드의 `fake` 메서드를 사용하면 요청이 만들어졌을 때 스텁 / 더미 응답을 반환하도록 HTTP 클라이언트에게 지시할 수 있습니다.

<a name="faking-responses"></a>
### 응답 속이기

예를 들어, 모든 요청에 대해 아무것도 리턴하지 않고 `200` 상태 코드를 응답하도록 HTTP 클라이언트에게 지시하려면 아무 인자 없이 `fake` 메서드를 호출하면 됩니다.

    use Illuminate\Support\Facades\Http;

    Http::fake();

    $response = Http::post(...);

#### 특정 URL 속이기

한편, `fake` 메서드에 배열을 넘겨줄 수도 있습니다. 배열의 키는 속이고자 하는 URL 패턴을 나타내고 값은 응답을 나타냅니다. `*` 문자는 와일드 카드로 사용할 수 있습니다. 속이지 않은 모든 URL은 실제로 수행됩니다. 스텁 / 가짜 응답을 만드는데는 `response` 메서드를 사용합니다.

    Http::fake([
        // 깃헙 엔드포인트용 JSON 응답 스텁...
        'github.com/*' => Http::response(['foo' => 'bar'], 200, ['Headers']),

        // 구글 엔드포인트용 문자열 응답 스텁...
        'google.com/*' => Http::response('Hello World', 200, ['Headers']),
    ]);

일치하지 않는 모든 URL용 대체 URL 패턴을 지정하고 싶으면 `*` 문자를 사용하면 됩니다.

    Http::fake([
        // 깃헙 엔드포인트용 JSON 응답 스텁...
        'github.com/*' => Http::response(['foo' => 'bar'], 200, ['Headers']),

        // 다른 모든 엔드 포인트용 문자열 응답 스텁...
        '*' => Http::response('Hello World', 200, ['Headers']),
    ]);

#### 응답 시퀀스 속이기

단일 URL이 여러 가짜 응답을 특정한 순서로 반환할 필요가 있을 때가 있습니다. 이를 위해서는 `Http::sequence` 메서드를 사용해서 응답을 만들면 됩니다.

    Http::fake([
        // 깃헙 엔드포인트용 다중 응답 스텁...
        'github.com/*' => Http::sequence()
                                ->push('Hello World', 200)
                                ->push(['foo' => 'bar'], 200)
                                ->pushStatus(404),
    ]);

응답 시퀀스에 있는 모든 응답이 소비되고 나면 그 이후에 응답 시퀀스로 보내는 요청은 예외가 발생합니다. 시퀀스가 비어있을 때의 기본 응답을 지정하고 싶으면 `whenEmpty` 메서드를 사용하면 됩니다.

    Http::fake([
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

#### 가짜 콜백

특정 엔드포인트가 어떤 응답을 반환해야하는지 결정하는데 더욱 더 복잡한 로직이 필요하다면 `fake` 메서드에 콜백을 넘겨줄 수 있습니다. 이 콜백은 `Illuminate\Http\Client\Request` 인스턴스를 받고 응답 인스턴스를 반환해야 합니다.

    Http::fake(function ($request) {
        return Http::response('Hello World', 200);
    });

<a name="inspecting-requests"></a>
### 요청 검사하기

응답을 속일 때 제대로 된 데이터와 헤더를 보내는지 확인하기 위해 클라이언트가 받는 요청을 검사하고 싶을 때가 있습니다. `Http::fake`를 호출한 이후에 `Http::assertSent` 메서드를 호출해서 검사할 수 있습니다.

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

필요한 경우 `assertNotSent` 메소드를 사용하여 특정 요청을 전송하지 않았는지 검증 할 수 있습니다.

    Http::fake();
    
    Http::post('http://test.com/users', [
        'name' => 'Taylor',
        'role' => 'Developer',
    ]); 
       
    Http::assertNotSent(function (Request $request) {
        return $request->url() === 'http://test.com/posts';
    });

또는 요청이 전송되지 않았는지 검증하려면 `assertNothingSent` 메소드를 사용할 수 있습니다.

    Http::fake();
    
    Http::assertNothingSent();
