# HTTP Requests-요청

- [소개하기](#introduction)
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
- [파일](#files)
   - [Retrieving Uploaded Files](#retrieving-uploaded-files)
   - [업로드 파일 검색](#retrieving-uploaded-files)
   - [Storing Uploaded Files](#storing-uploaded-files)
   - [업로드 파일 수납](#storing-uploaded-files)
- [신뢰할 수 있는 프록시 설정](#configuring-trusted-proxies)
- [신뢰할 수 있는 호스트 설정](#configuring-trusted-hosts)

<a name="introduction"></a>
## 소개하기

라라벨의 `Illuminate\Http\Request` 클래스는 요청과 함께 제출된 입력, 쿠키 및 파일을 검색할 뿐만 아니라 애플리케이션에서 처리 중인 현재 HTTP 요청과 상호 작용하는 객체 지향 방식을 제공합니다.

<a name="interacting-with-the-request"></a>
## Request와 상호작용

<a name="accessing-the-request"></a>
### Request 엑세스 하기

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

언급된 것 처럼 route 클로저에서 `Illuminate\Http\Request` 클래스를 입력할 수도 있습니다. 서비스 컨테이너는 실행될 때 들어오는 요청을 클로저에 자동으로 주입합니다.

    use Illuminate\Http\Request;

    Route::get('/', function (Request $request) {
        //
    });

<a name="dependency-injection-route-parameters"></a>
#### 의존성 주입 & 라우트 파라미터

만약 컨트롤러 메소드에서 라우트 파라미터로 부터 입력값을 받아야 한다면, 다른 의존성을 지정한 뒤에 라우트 파라미터를 나열해야 합니다. 예를 들어 라우트는 다음과 같이 정의될 수 있습니다.

    use App\Http\Controllers\UserController;

    Route::put('/user/{id}', [UserController::class, 'update']);

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
### Request 경로 & 메소드

`Illuminate\Http\Request` 인스턴스는 애플리케이션에 유입되는 HTTP 요청을 검사하기 위해 다양한 메소드를 제공하고 `Symfony\Component\HttpFoundation\Request` 클래스를 상속합니다. 아래에서 몇가지 가장 중요한 메소드를 알아보겠습니다.

<a name="retrieving-the-request-path"></a>
#### Request 경로 조회하기

`path` 메소드는 request의 경로정보를 반환합니다. 따라서 들어오는 request가 `http://example.com/foo/bar` 를 대상으로 한다면 `path` 메소드는 `foo/bar`를 반환합니다.

    $uri = $request->path();

<a name="inspecting-the-request-path"></a>
#### Request 경로 검사

`is` 메소드는 들어오는 request가 특정 패턴에 상응한다는 것을 확인 할 수 있게 해줍니다. 이 메소드를 활용할 때 `*` 기호를 와일드카드로 쓸 수 있습니다.

    if ($request->is('admin/*')) {
        //
    }

`routeIs` 메소드를 사용하면 request 요청이 [named route](/docs/{{version}}/routing#named-routes)와 일치하는지 확인할 수 있습니다.

    if ($request->routeIs('admin.*')) {
        //
    }

<a name="retrieving-the-request-url"></a>
#### Request URI 조회하기

유입되는 request 의 전체 URL을 조회하기 위해서 `url` 또는 `fullUrl` 메소드를 사용할 수 있습니다. `url` 메소드는 쿼리 스트링 없는 URL을, `fullUrl` 은 쿼리 스트링을 포함한 URL을 반환합니다.

    $url = $request->url();

    $urlWithQueryString = $request->fullUrl();

현재 URL에 query string 데이터를 추가하려면 `fullUrlWithQuery` 메서드를 호출하면 됩니다. 이 메서드는 주어진 query string 변수 배열을 현재 query string과 병합합니다.

    $request->fullUrlWithQuery(['type' => 'phone']);

<a name="retrieving-the-request-method"></a>
#### Request HTTP 메소드(verb) 조회하기

`method` 메소드는 request에 대해 HTTP 메소드를 반환합니다. HTTP 메소드가 특정 문자열에 대응하는 것을 확인하기 위해 `isMethod` 메소드를 사용할 수 있습니다.

    $method = $request->method();

    if ($request->isMethod('post')) {
        //
    }

<a name="request-headers"></a>
### Request 헤더

`header` 메소드를 사용하여 `Illuminate\Http\Request` 인스턴스에서 요청 헤더를 검색할 수 있습니다. 요청에 헤더가 없으면 `null`이 반환됩니다. 그러나 `header` 메서드는 요청에 헤더가 없으면 반환되는 선택적 두 번째 인수를 허용합니다.

    $value = $request->header('X-Header-Name');

    $value = $request->header('X-Header-Name', 'default');

`hasHeader` 메소드는 요청에 지정된 헤더가 포함되어 있는지 확인하는 데 사용할 수 있습니다.

    if ($request->hasHeader('X-Header-Name')) {
        //
    }

편의상 `Authorization` 헤더에서 bearer 토큰을 검색하기 위해 `bearerToken` 메소드를 사용할 수 있습니다. 만약 헤더가 없으면 빈 문자열이 반환됩니다.

    $token = $request->bearerToken();

<a name="request-ip-address"></a>
### Request IP 주소

`ip` 메소드를 사용하여 애플리케이션에 요청한 클라이언트의 IP 주소를 검색할 수 있습니다.

    $ipAddress = $request->ip();

<a name="content-negotiation"></a>
### Content 협상

라라벨은 `Accept` 헤더를 통해 들어오는 요청의 요청된 Content 유형을 검사하는 여러 방법을 제공합니다. 먼저 `getAcceptableContentTypes` 메소드는 요청에 의해 수락된 모든 Content 유형을 포함하는 배열을 반환합니다.

    $contentTypes = $request->getAcceptableContentTypes();

`accepts` 메소드는 Content 유형의 배열을 허용하고 요청에 의해 content 유형이 수락되면 `true`를 반환합니다. 그렇지 않으면 `false`가 반환됩니다.

    if ($request->accepts(['text/html', 'application/json'])) {
        // ...
    }

`prefers` 메소드를 사용하여 주어진 콘텐츠 유형 배열 중에서 요청이 가장 선호하는 content 유형을 결정할 수 있습니다. 제공된 콘텐츠 유형 중 어느 것도 요청에 의해 수락되지 않으면 `null` 이 반환됩니다.

    $preferred = $request->prefers(['text/html', 'application/json']);

많은 애플리케이션이 HTML이나 JSON만 제공하기 때문에 `expectsJson` 메소드를 사용하여 들어오는 요청이 JSON 응답을 예상하는지 빠르게 결정할 수 있습니다.

    if ($request->expectsJson()) {
        // ...
    }

<a name="psr7-requests"></a>
### PSR-7 Requests

[PSR-7 표준](https://www.php-fig.org/psr/psr-7/) 은 요청과 응답을 포함한 HTTP 메세지들에 대한 인터페이스를 지정합니다. 라라벨의 request 대신 PSR-7 요청의 인스턴스를 획득하기 위해서는 우선 몇 개의 라이브러리를 설치해야 합니다. 라라벨은 *Symfony HTTP Message Bridge* 컴포넌트를 사용하여 일반적인 라라벨의 request-요청과 response-응답을 PSR-7에 맞는 구현체로 변환합니다.

    composer require symfony/psr-http-message-bridge
    composer require nyholm/psr7

이 라이브러리들을 설치하였다면 라우트 클로저나 컨트롤러 메소드에 request를 타입힌트하여 PSR-7 request를 얻을 수 있습니다.

    use Psr\Http\Message\ServerRequestInterface;

    Route::get('/', function (ServerRequestInterface $request) {
        //
    });

> {tip} 라우트나 컨트롤러에서 반환된 PSR-7 response 인스턴스는 자동으로 라라벨 response 인스턴스로 변환되어 프레임워크에 표시될 것입니다.

<a name="input"></a>
## 입력

<a name="retrieving-input"></a>
### 입력값 조회하기

<a name="retrieving-all-input-data"></a>
#### 모든 입력값 조회하기

`all` 메서드를 사용하여 들어오는 요청의 모든 입력 데이터를 `array`로 검색할 수 있습니다. 이 메서드는 들어오는 요청이 HTML 양식에서 온 것인지 XHR 요청에서 온 것인지에 관계없이 사용할 수 있습니다.

    $input = $request->all();

`collect` 메소드를 사용하면 들어오는 요청의 모든 입력 데이터를 [collection](/docs/{{version}}/collections)으로 검색할 수 있습니다.

    $input = $request->collect();

`collect` 메소드를 사용하면 들어오는 요청 input의 하위 집합을 collection으로 검색할 수도 있습니다.

    $request->collect('users')->each(function ($user) {
        // ...
    });

<a name="retrieving-an-input-value"></a>
#### 입력값 조회하기

몇 개의 단순한 메소드들을 사용하면 어떤 HTTP verb 가 request 에 사용되었는지에 대한 걱정없이 `Illuminate\Http\Request` 인스턴스에서 모든 사용자 입력에 접근할 수 있습니다. HTTP verb에 관계없이 `input` 메소드는 사용자 입력을 조회하는데 사용됩니다.

    $name = $request->input('name');

`input` 메소드에 두번째 인자로 기본값을 전달할 수 있습니다. Request에 요청된 입력 값이 존재하지 않는다면 기본값이 반환됩니다.

    $name = $request->input('name', 'Sally');

배열 입력을 가진 폼에서 동작할 때에는, 배열에 접근하기 위하여 "점" 표기법을 사용할 수 있습니다.

    $name = $request->input('products.0.name');

    $names = $request->input('products.*.name');

모든 입력 값을 연관 배열로 검색하기 위해 인자없이 `input` 메소드를 호출 할 수 있습니다.

    $input = $request->input();

<a name="retrieving-input-from-the-query-string"></a>
#### 쿼리 스트링에서 입력값 조회하기

`input` 메소드가 요청-request 전체의 payload(쿼리 스트링을 포함하여)에서 값을 조회한다면, `query` 메소드는 쿼리 스트링에서만 값을 조회합니다.

    $name = $request->query('name');

찾고자 하는 데이터가 존재하지 않을 때를 위해서 메소드의 두번째 인자로 기본값을 전달할 수 있습니다.

    $name = $request->query('name', 'Helen');

`query` 메소드를 호출할 때 아무런 인자를 전달하지 않는다면, 전체 쿼리 스트링의 값들을 배열 형태로 반환합니다.

    $query = $request->query();

<a name="retrieving-json-input-values"></a>
#### JSON 입력값 조회하기

JSON 요청을 애플리케이션에 보낼 때 요청의 `Content-Type` 헤더가 `application/json`으로 올바르게 설정되어 있으면 `input` 메소드를 통해 JSON 데이터에 액세스할 수 있습니다. "dot" 구문을 사용하여 JSON 배열 내에 중첩된 값을 검색할 수도 있습니다.

    $name = $request->input('user.name');

<a name="retrieving-boolean-input-values"></a>
#### Boolean 입력값 조회하기

체크박스와 같은 HTML 요소를 처리할 때 애플리케이션은 실제로 문자열인 "truthy" 값을 수신할 수 있습니다. 예를 들어 "true" 또는 "on"입니다. 편의를 위해 `boolean` 메서드를 사용하여 이러한 값을 부울로 검색할 수 있습니다. `boolean` 메소드는 1, "1", true, "true", "on" 및 "yes"에 대해 `true`를 반환합니다. 다른 모든 값은 `false`를 반환합니다.

    $archived = $request->boolean('archived');

<a name="retrieving-date-input-values"></a>
#### Date 입력값 조회하기

편의를 위해 날짜/시간을 포함하는 입력 값은 `date` 메서드를 사용하여 Carbon 인스턴스로 검색할 수 있습니다. 요청에 주어진 이름의 입력 값이 없으면 `null`이 반환됩니다.

    $birthday = $request->date('birthday');

`date` 메서드가 허용하는 두 번째 및 세 번째 인수는 각각 날짜 형식과 시간대를 지정하는 데 사용할 수 있습니다.

    $elapsed = $request->date('elapsed', '!H:i', 'Europe/Madrid');

입력 값이 있지만 형식이 잘못된 경우 `InvalidArgumentException`이 발생합니다. 따라서 `date` 메소드를 호출하기 전에 입력을 검증하는 것이 좋습니다.

<a name="retrieving-input-via-dynamic-properties"></a>
#### 동적 속성을 통한 입력값 조회하기

`Illuminate\Http\Request` 인스턴스의 동적 속성을 사용하여 사용자 입력에 엑세스할 수도 있습니다. 예를 들어 애플리케이션의 form 중에 하나가 `name` 필드를 가지고 있다면, 다음과 같이 필드의 값에 엑세스 할 수 있습니다.

    $name = $request->name;

동적 속성을 사용할 때, 라라벨은 먼저 request payload 안에 있는 파라미터의 값을 찾습니다. 만약 값이 없다면 라라벨은 라우트 파라미터 안에 있는 필드를 찾을 것입니다.

<a name="retrieving-a-portion-of-the-input-data"></a>
#### 입력 데이터의 한 부분 조회하기

입력 데이터의 일부분만 조회하기 위해서 `only`와 `except` 메소드를 사용할 수 있습니다. 이 두 메소드 모두 하나의 `배열` 또는 동적인 인자의 목록을 받아 들입니다.

    $input = $request->only(['username', 'password']);

    $input = $request->only('username', 'password');

    $input = $request->except(['credit_card']);

    $input = $request->except('credit_card');

> {note} `only` 메소드는 유입되는 request-요청에서 입력한 키 / 값 쌍을 반환합니다. 그렇지만 현재 request 에서 존재하지 않는 키/값은 반환하지 않습니다.

<a name="determining-if-input-is-present"></a>
### 입력이 있는지 확인하기

Request에 어떤 값이 존재하는지 확인하기 위해서 `has` 메소드를 사용해야 합니다. `has` 메소드는 현재 request 에서 값이 존재할 때 `true`를 반환합니다.

    if ($request->has('name')) {
        //
    }

`has` 메소드에 배열이 주어지면, 지정된 모든 값이 존재하는지 확인합니다.

    if ($request->has(['name', 'email'])) {
        //
    }

`whenHas` 메소드는 요청에 값이 있으면 주어진 클로저를 실행합니다.

    $request->whenHas('name', function ($input) {
        //
    });

두 번째 클로저는 요청에 지정된 값이 없으면 실행되는 `whenHas` 메서드로 전달될 수 있습니다.

    $request->whenHas('name', function ($input) {
        // The "name" value is present...
    }, function () {
        // The "name" value is not present...
    });

`hasAny` 메소드는 지정된 값이 존재하면 `true`를 반환합니다.

    if ($request->hasAny(['name', 'email'])) {
        //
    }

주어진 변수값이 현재 request 에 존재하고 비어 있지 않은 것을 확인하려면 `filled` 메소드를 사용하면 됩니다.

    if ($request->filled('name')) {
        //
    }

`whenFilled` 메소드는 값이 요청에 있고 그 값이 비어 있지 않은 경우 주어진 클로저를 실행합니다.

    $request->whenFilled('name', function ($input) {
        //
    });

두 번째 클로저는 지정된 값이 "채워지지" 않은 경우 실행되는 `whenFilled` 메서드로 전달될 수 있습니다.

    $request->whenFilled('name', function ($input) {
        // The "name" value is filled...
    }, function () {
        // The "name" value is not filled...
    });

주어진 키가 request 에 없는지 확인하려면 `missing` 메소드를 사용할 수 있습니다.

    if ($request->missing('name')) {
        //
    }

<a name="merging-additional-input"></a>
### 추가 입력 병합

때때로 추가 입력을 요청의 기존 입력 데이터에 수동으로 병합해야 할 수도 있습니다. 이를 위해 `merge` 메소드를 사용할 수 있습니다.

    $request->merge(['votes' => 0]);

`mergeIfMissing` 메소드는 해당 키가 요청의 입력 데이터 내에 이미 존재하지 않는 경우, 입력을 요청에 병합하는 데 사용할 수 있습니다.

    $request->mergeIfMissing(['votes' => 0]);

<a name="old-input"></a>
### 이전 입력값 확인하기

라라벨은 한 request의 입력을 다음 request 중에도 유지할 수 있도록 해줍니다. 이 기능은 특히 유효성 검사 오류를 감지한 후 폼을 다시 채워 넣을 때 유용합니다. 하지만 라라벨에 포함된 [유효성 검사 기능](/docs/{{version}}/validation)를 이용한다면 몇몇 유효성 검사 기능들이 자동으로 이 기능을 호출하기 때문에 수동으로 이 메소드들을 사용해야 할 가능성은 낮습니다.

<a name="flashing-input-to-the-session"></a>
#### 입력값을 세션에 임시 저장하기

`Illuminate\Http\Request` 클래스의 `flash` 메소드는 현재의 입력들을 [세션](/docs/{{version}}/session)에 저장하여, 사용자의 다음 request 동안에도 값을 사용할 수 있게 해줍니다.

    $request->flash();

`flashOnly`와 `flashExcept` 메소드를 이용하여 request 데이터의 일부분을 세션에 임시 저장(flash)할 수 있습니다. 이 메소드들은 패스워드와 같은 민감한 정보들을 세션에 포함하지 않도록 하는데 유용합니다.

    $request->flashOnly(['username', 'email']);

    $request->flashExcept('password');

<a name="flashing-input-then-redirecting"></a>
#### 입력값을 임시저장한 후 리다이렉트하기

종종 입력값을 세션에 임시 저장 하고 이전 페이지로 리다이렉트 하기를 원할 수 있는데, `withInput` 메소드를 이용하여 리다이렉트시 입력값 임시 저장이 되도록 간단하게 메소드 체이닝할 수 있습니다.

    return redirect('form')->withInput();

    return redirect()->route('user.create')->withInput();

    return redirect('form')->withInput(
        $request->except('password')
    );

<a name="retrieving-old-input"></a>
#### 이전 입력값 조회하기

이전 요청에서 플래시 입력을 검색하려면 `Illuminate\Http\Request` 인스턴스에서 `old` 메서드를 호출하세요. `old` 메소드는 [세션](/docs/{{version}}/session)에서 이전에 플래시된 입력 데이터를 가져옵니다.

    $username = $request->old('username');

라라벨은 글로벌 `old` 헬퍼 함수도 제공합니다. [블레이드 템플릿](/docs/{{version}}/blade) 안에서 지난 입력값에서 양식을 다시 채우려면 `old` 헬퍼 함수를 사용하는 것이 보다 편리합니다. 주어진 필드에 대한 이전 입력값이 존재하지 않는다면, `null` 이 반환될 것입니다.

    <input type="text" name="username" value="{{ old('username') }}">

<a name="cookies"></a>
### 쿠키

<a name="retrieving-cookies-from-requests"></a>
#### Request 에서 쿠키 조회하기

라라벨 프레임워크에서 생성된 모든 쿠키는 인증 코드와 함께 암호화 됩니다. 이것은 쿠키 정보가 클라이언트에 의해 변경되었을 경우 유효하지 않은 것으로 간주됨을 의미합니다. Request에서 쿠키 값을 가져오기 위해서는 `Illuminate\Http\Request` 인스턴스에서 `cookie` 메소드를 사용하십시오.

    $value = $request->cookie('name');

<a name="input-trimming-and-normalization"></a>
## 입력 Trimming & 표준회

기본적으로 라라벨은 애플리케이션의 글로벌 미들웨어 스택에 `App\Http\Middleware\TrimStrings` 및 `App\Http\Middleware\ConvertEmptyStringsToNull` 미들웨어를 포함합니다. 이런 미들웨어는 `App\Http\Kernel` 클래스에 의해 글로벌 미들웨어 스택에 나열됩니다. 이러한 미들웨어는 요청 시 들어오는 모든 문자열 필드를 자동으로 트리밍하고 빈 문자열 필드를 `null`로 변환합니다. 이를 통해 경로 및 컨트롤러에서 이러한 정규화 문제에 대해 걱정할 필요가 없습니다.

비활성화하려면 `App\Http\Kernel` 클래스의 `$middleware` 속성에서 두 미들웨어를 제거하여 애플리케이션의 미들웨어 스택에서 두 미들웨어를 제거할 수 있습니다.

<a name="files"></a>
## 파일처리

<a name="retrieving-uploaded-files"></a>
### 업로드된 파일 조회하기

`file` 메소드를 사용하거나 동적 속성을 사용하여 `Illuminate\Http\Request` 인스턴스에서 업로드된 파일을 검색할 수 있습니다. `file` 메소드는 PHP `SplFileInfo` 클래스를 확장하고 파일과 상호작용하기 위한 다양한 메소드를 제공하는 `Illuminate\Http\UploadedFile` 클래스의 인스턴스를 반환합니다.

    $file = $request->file('photo');

    $file = $request->photo;

`hasFile` 메소드를 사용하면 request에 파일을 가지고 있는지 확인할 수 있습니다.

    if ($request->hasFile('photo')) {
        //
    }

<a name="validating-successful-uploads"></a>
#### 성공적으로 업로드 되었는지 확인하기

파일이 현재 존재하는지 확인하는데 더하여, `isValid` 메소드를 사용하여 업로드된 파일에 아무런 문제가 없는지 확인할 수 있습니다.

    if ($request->file('photo')->isValid()) {
        //
    }

<a name="file-paths-extensions"></a>
#### 파일 경로 & 확장자

`UploadedFile` 클래스는 파일의 전체 경로와 확장자에 엑세스 할 수 있는 메소드를 가지고 있습니다. `extension` 메소드는 그 내용에 따라 파일의 확장자를 추측하려고 시도합니다. 이 확장자는 클라이언트가 제공한 확장자와는 다를 수 있습니다.

    $path = $request->photo->path();

    $extension = $request->photo->extension();

<a name="other-file-methods"></a>
#### 기타 파일 관련 메소드들

`UploadedFile` 인스턴스에 다양한 다른 메소드들이 제공되어 있습니다. 이 메소드들에 대해 더 많은 정보를 얻으려면 [클래스의 API documentation](https://api.symfony.com/master/Symfony/Component/HttpFoundation/File/UploadedFile.html) 을 확인해보십시오.

<a name="storing-uploaded-files"></a>
### 업로드된 파일 저장하기

업로드된 파일을 저장하려면, 일반적으로 설정된 [파일시스템](/docs/{{version}}/filesystem)중 하나를 사용합니다. `UploadedFile` 클래스는 업로드된 파일을 로컬 파일 시스템이나 아마존 S3 와 같은 클라우드 스토리지 디스크 중에 하나로 이동 시킬 수 있는 `store` 메소드를 가지고 있습니다.

`store` 메소드는 파일 시스템에 설정된 루트 디렉토리로 부터 파일이 어디에 저장되어야 할지에 대한 경로를 전달 받습니다. 파일의 이름은 자동으로 고유한 ID로 생성되므로 이 경로에는 파일 이름을 포함하지 않아야 합니다.

`store` 메소드는 파일이 저장되는데 사용될 디스크 이름을 두 번째 선택 인자로 허용합니다. 메소드는 디스크의 루트를 기준으로 파일의 경로를 반환합니다.

    $path = $request->photo->store('images');

    $path = $request->photo->store('images', 's3');

파일 이름이 자동으로 생성되지 않기를 원한다면, 경로와, 파일이름 그리고 디스크 이름을 인자로 받아 들이는 `storeAs` 메소드를 사용할 수 있습니다.

    $path = $request->photo->storeAs('images', 'filename.jpg');

    $path = $request->photo->storeAs('images', 'filename.jpg', 's3');

> {tip} 라라벨의 파일 스토리지에 대한 자세한 내용은 전체 [파일 스토리지 문서](/docs/{{version}}/filesystem)를 확인하세요.

<a name="configuring-trusted-proxies"></a>
## 신뢰할 수 있는 프록시 설정하기

TLS / SSL 인증서가 적용된 로드 밸런서 뒤에서 애플리케이션을 실행할 때 애플리케이션에서 HTTPS 링크가 생성되지 않는 경우가 있습니다. 일반적으로 이는 애플리케이션이 포트 80의 로드 밸런서에서 전송되는 트래픽 뒤에 위치해서, `url` 헬퍼를 사용할 때 HTTPS 링크를 생성해야 한다는 것을 알지 못하기 때문입니다.

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

> {팁} 만약 여러분이 AWS Elastic 로드 발란싱을 사용하고 있다면, 여러분의 `$headers` 값은 `Request::HEADER_X_FORWARDED_AWS_ELB`이 되어야 합니다. `$headers`에 사용될 수 있는 상수에 대한 다른 정보는, Symfony 문서의 [trusting proxies](https://symfony.com/doc/current/deployment/proxies.html)를 참고하십시오.

<a name="trusting-all-proxies"></a>
#### 모든 프록시 신뢰하기

아마존 AWS 또는 다른 "클라우드" 로드밸런서를 사용하는 경우에는, 실제 로드밸런서의 IP를 알 수가 없습니다. 이 경우, 모든 프록시를 신뢰할 수 있도록 하기 위해서 `*` 를 사용할 수 있습니다.

    /**
     * The trusted proxies for this application.
     *
     * @var string|array
     */
    protected $proxies = '*';

<a name="configuring-trusted-hosts"></a>
## 신뢰할 수 있는 호스트 설정

기본적으로 라라벨은 HTTP 요청의 `Host` 헤더 내용에 관계없이 수신하는 모든 요청에 응답합니다. 또한 웹 요청 중에 애플리케이션에 대한 절대 URL을 생성할 때 `Host` 헤더 값이 사용됩니다.

일반적으로 지정된 호스트 이름과 일치하는 요청만 애플리케이션에 보내도록 Nginx 또는 Apache와 같은 웹 서버를 설정해야 합니다. 그러나 웹 서버를 직접 사용자 정의할 수 없고, 특정 호스트 이름에만 응답하도록 라라벨에 지시해야 하는 경우 애플리케이션에 대해 `App\Http\Middleware\TrustHosts` 미들웨어를 활성화하여 그렇게 할 수 있습니다.

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

`allSubdomainsOfApplicationUrl` 도우미 메소드는 애플리케이션의 `app.url` 설정 값의 모든 하위 도메인과 일치하는 정규식을 반환합니다. 이 도우미 메서드는 와일드카드 하위 도메인을 사용하는 애플리케이션을 구축할 때 애플리케이션의 모든 하위 도메인을 허용하는 편리한 방법을 제공합니다.