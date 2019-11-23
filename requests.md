# HTTP Requests-요청

- [Request-요청 엑세스 하기](#accessing-the-request)
    - [Request-요청 경로 & 메소드](#request-path-and-method)
    - [PSR-7 Requests-요청](#psr7-requests)
- [입력값 Trim 처리 & 일반화처리](#input-trimming-and-normalization)
- [입력값 조회하기](#retrieving-input)
    - [이전 입력값 확인하기](#old-input)
    - [쿠키](#cookies)
- [파일처리](#files)
    - [업로드된 파일 조회하기](#retrieving-uploaded-files)
    - [업로드된 파일 저장하기](#storing-uploaded-files)
- [신뢰할 수 있는 프록시 설정하기](#configuring-trusted-proxies)

<a name="accessing-the-request"></a>
## Request 엑세스 하기

의존성 주입을 통해서 현재 HTTP request의 인스턴스를 획득하기 위해서는 컨트롤러 메소드에서 `Illuminate\Http\Request` 클래스를 타입힌트해야 합니다. 유입된 request의 인스턴스는 [서비스 컨테이너](/docs/{{version}}/container)에 의해서 자동으로 주입될 것입니다.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;

    class UserController extends Controller
    {
        /**
         * Store a new user.
         *
         * @param  Request  $request
         * @return Response
         */
        public function store(Request $request)
        {
            $name = $request->input('name');

            //
        }
    }

#### 의존성 주입 & 라우트 파라미터

만약 컨트롤러 메소드에서 라우트 파라미터로 부터 입력값을 받아야 한다면, 다른 의존성을 지정한 뒤에 라우트 파라미터를 나열해야 합니다. 예를 들어 라우트는 다음과 같이 정의될 수 있습니다.

    Route::put('user/{id}', 'UserController@update');

다음과 같이 컨트롤러 메소드를 정의하여 `Illuminate\Http\Request`를 타입힌트하면서 동시에 라우트 파라미터 `id`에 접근할 수 있습니다.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;

    class UserController extends Controller
    {
        /**
         * Update the specified user.
         *
         * @param  Request  $request
         * @param  string  $id
         * @return Response
         */
        public function update(Request $request, $id)
        {
            //
        }
    }

#### 라우트 클로저를 통해서 Request 엑세스하기

또한 라우트 클로저에서도 `Illuminate\Http\Request` 클래스를 타입힌트 할 수 있습니다. 서비스 컨테이너는 클로저가 실행될 때 자동으로 유입된 request 를 주입할 것입니다.

    use Illuminate\Http\Request;

    Route::get('/', function (Request $request) {
        //
    });

<a name="request-path-and-method"></a>
### Request 경로 & 메소드

`Illuminate\Http\Request` 인스턴스는 애플리케이션의 HTTP request를 검사할 수 있는 다양한 메소드를 제공하며 `Symfony\Component\HttpFoundation\Request` 클래스를 상속 받고 있습니다. 몇가지 가장 중요한 메소드를 알아보겠습니다.

#### Request 경로 조회하기

`path` 메소드는 request의 경로정보를 반환합니다. 따라서 들어오는 request가 `http://domain.com/foo/bar`를 대상으로 한다면 `path` 메소드는 `foo/bar`를 반환합니다.

    $uri = $request->path();

`is` 메소드는 들어오는 request가 특정 패턴에 상응한다는 것을 확인할 수 있게 해줍니다. 이 메소드를 활용할 때 `*` 기호를 와일드카드로 쓸 수 있습니다.

    if ($request->is('admin/*')) {
        //
    }

#### Request URI 조회하기

유입되는 request 의 전체 URL을 조회하기 위해서 `url` 또는 `fullUrl` 메소드를 사용할 수 있습니다. `url` 메소드는 쿼리 스트링 없는 URL을, `fullUrl` 은 쿼리 스트링을 포함한 URL을 반환합니다.

    // Without Query String...
    $url = $request->url();

    // With Query String...
    $url = $request->fullUrl();

#### Request HTTP 메소드(verb) 조회하기

`method` 메소드는 request에 대해 HTTP 메소드를 반환합니다. HTTP 메소드가 특정 문자열에 대응하는 것을 확인하기 위해 `isMethod` 메소드를 사용할 수 있습니다.

    $method = $request->method();

    if ($request->isMethod('post')) {
        //
    }

<a name="psr7-requests"></a>
### PSR-7 Requests

[PSR-7 표준](https://www.php-fig.org/psr/psr-7/)은 요청과 응답을 포함한 HTTP 메세지들에 대한 인터페이스를 지정합니다. 라라벨의 request 대신 PSR-7 요청의 인스턴스를 획득하기 위해서는 우선 몇 개의 라이브러리를 설치해야 합니다. 라라벨은 *Symfony HTTP Message Bridge* 컴포넌트를 사용하여 일반적인 라라벨의 request-요청과 response-응답을 PSR-7에 맞는 구현체로 변환합니다.

    composer require symfony/psr-http-message-bridge
    composer require zendframework/zend-diactoros

이 라이브러리들을 설치하였다면 라우트 클로저나 컨트롤러 메소드에 request를 타입힌트하여 PSR-7 request를 얻을 수 있습니다.

    use Psr\Http\Message\ServerRequestInterface;

    Route::get('/', function (ServerRequestInterface $request) {
        //
    });

> {tip} 라우트나 컨트롤러에서 반환된 PSR-7 response 인스턴스는 자동으로 라라벨 response 인스턴스로 변환되어 프레임워크에 표시될 것입니다.

<a name="input-trimming-and-normalization"></a>
## 입력값 Trim 처리 & 일반화처리

기본적으로 라라벨은 애플리케이션의 글로벌 미들웨어 스택에 `TrimStrings` 그리고 `ConvertEmptyStringsToNull` 미들웨어를 포함하고 있습니다. 이 미들웨어는 `App\Http\Kernel` 클래스의 미들웨어 스택에 나열되어 있습니다. 이 미들웨어는 request-요청에 유입되는 모든 문자 필드들을 자동으로 trim 처리하고, 빈 문자필드는 `null`로 변환합니다. 따라서 라우트와 컨트롤러에서 이러한 일반화 문제를 걱정할 필요가 없습니다.

이 동작을 비활성화 시키려면, `App\Http\Kernel` 클래스의 `$middleware` 속성에서 두 미들웨어를 제거하면 됩니다.

<a name="retrieving-input"></a>
## 입력값 조회하기

#### 모든 입력값 조회하기

`all` 메소드를 사용하여 모든 입력데이터를 `배열` 로 조회할 수 있습니다.

    $input = $request->all();

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

#### 쿼리 스트링에서 입력값 조회하기

`input` 메소드가 요청-request 전체의 payload(쿼리 스트링을 포함하여)에서 값을 조회한다면, `query` 메소드는 쿼리 스트링에서만 값을 조회합니다.

    $name = $request->query('name');

찾고자 하는 데이터가 존재하지 않을 때를 위해서 메소드의 두번째 인자로 기본값을 전달할 수 있습니다.

    $name = $request->query('name', 'Helen');

`query` 메소드를 호출할 때 아무런 인자를 전달하지 않는다면, 전체 쿼리 스트링의 값들을 배열 형태로 반환합니다.

    $query = $request->query();

#### 동적 속성을 통한 입력값 조회하기

`Illuminate\Http\Request` 인스턴스의 동적 속성을 사용하여 사용자 입력에 엑세스할 수도 있습니다. 예를 들어 애플리케이션의 form 중에 하나가 `name` 필드를 가지고 있다면, 다음과 같이 필드의 값에 엑세스 할 수 있습니다.

    $name = $request->name;

동적 속성을 사용할 때, 라라벨은 먼저 request payload 안에 있는 파라미터의 값을 찾습니다. 만약 값이 없다면 라라벨은 라우트 파라미터 안에 있는 필드를 찾을 것입니다.

#### JSON 입력 값 조회하기

애플리케이션에 JSON 요청이 전달되어 `Content-Type` 헤더 속성이 `application/json` 으로 지정되어 있다면 `input` 메소드를 통해서 JSON 데이터에 접근할 수 있습니다. 또한 "점" 문법을 통해서 JSON 배열에 접근할 수도 있습니다.

    $name = $request->input('user.name');

#### 입력 데이터의 한 부분 조회하기

입력 데이터의 일부분만 조회하기 위해서 `only`와 `except` 메소드를 사용할 수 있습니다. 이 두 메소드 모두 하나의 `배열` 또는 동적인 인자의 목록을 받아 들입니다.

    $input = $request->only(['username', 'password']);

    $input = $request->only('username', 'password');

    $input = $request->except(['credit_card']);

    $input = $request->except('credit_card');

> {tip} `only` 메소드는 유입되는 request-요청에서 입력한 키 / 값 쌍을 반환합니다. 그렇지만 현재 request 에서 존재하지 않는 키/값은 반환하지 않습니다.

    $input = $request->intersect(['username', 'password']);

#### 입력값이 존재하는지 확인하기

Request에 어떤 값이 존재하는지 확인하기 위해서 `has` 메소드를 사용해야 합니다. `has` 메소드는 현재 request 에서 값이 존재할 때 `true`를 반환합니다.

    if ($request->has('name')) {
        //
    }

`has` 메소드에 배열이 주어지면, 지정된 모든 값이 존재하는지 확인하게 됩니다.

    if ($request->has(['name', 'email'])) {
        //
    }

`hasAny` 메소드는 지정된 값이 존재하면 `true`를 반환합니다.

    if ($request->hasAny(['name', 'email'])) {
        //
    }

주어진 변수값이 현재 request 에 존재하고 비어 있지 않은 것을 확인하려면 `filled` 메소드를 사용하면 됩니다.

    if ($request->filled('name')) {
        //
    }

주어진 키가 request 에 없는지 확인하려면 `missing` 메소드를 사용할 수 있습니다.

    if ($request->missing('name')) {
        //
    }

<a name="old-input"></a>
### 이전 입력값 확인하기

라라벨은 한 request의 입력을 다음 request 중에도 유지할 수 있도록 해줍니다. 이 기능은 특히 유효성 검사 오류를 감지한 후 폼을 다시 채워 넣을 때 유용합니다. 하지만 라라벨에 포함된 [유효성 검사 기능](/docs/{{version}}/validation)를 이용한다면 몇몇 유효성 검사 기능들이 자동으로 이 기능을 호출하기 때문에 수동으로 이 메소드들을 사용해야 할 가능성은 낮습니다.

#### 입력값을 세션에 임시 저장하기

`Illuminate\Http\Request` 클래스의 `flash` 메소드는 현재의 입력들을 [세션](/docs/{{version}}/session)에 저장하여, 사용자의 다음 request 동안에도 값을 사용할 수 있게 해줍니다.

    $request->flash();

`flashOnly`와 `flashExcept` 메소드를 이용하여 request 데이터의 일부분을 세션에 임시 저장(flash)할 수 있습니다. 이 메소드들은 패스워드와 같은 민감한 정보들을 세션에 포함하지 않도록 하는데 유용합니다.

    $request->flashOnly(['username', 'email']);

    $request->flashExcept('password');

#### 입력값을 임시저장한 후 리다이렉트하기

종종 입력값을 세션에 임시 저장 하고 이전 페이지로 리다이렉트 하기를 원할 수 있는데, `withInput` 메소드를 이용하여 리다이렉트시 입력값 임시 저장이 되도록 간단하게 메소드 체이닝할 수 있습니다.

    return redirect('form')->withInput();

    return redirect('form')->withInput(
        $request->except('password')
    );

#### 이전 입력값 조회하기

이전 request 에서 저장된 입력값을 조회하기 위해서는 `Request` 인스턴스의 `old` 메소드를 사용하면 됩니다. `old` 메소드는 [세션](/docs/{{version}}/session)에 저장된 입력 데이터를 꺼낼 것입니다.

    $username = $request->old('username');

라라벨은 글로벌 `old` 헬퍼 함수도 제공합니다. [블레이드 템플릿](/docs/{{version}}/blade) 안에서 지난 입력값을 보여주려면 `old` 헬퍼 함수를 사용하는 것이 보다 편리합니다. 주어진 필드에 대한 이전 입력값이 존재하지 않는다면, `null` 이 반환될 것입니다.

    <input type="text" name="username" value="{{ old('username') }}">

<a name="cookies"></a>
### 쿠키

#### Request 에서 쿠키 조회하기

라라벨 프레임워크에서 생성된 모든 쿠키는 인증 코드와 함께 암호화 됩니다. 이것은 쿠키 정보가 클라이언트에 의해 변경되었을 경우 유효하지 않은 것으로 간주됨을 의미합니다. Request에서 쿠키 값을 가져오기 위해서는 `Illuminate\Http\Request` 인스턴스에서 `cookie` 메소드를 사용하십시오:

    $value = $request->cookie('name');

또한, 쿠키에 엑세스 하기 위해서 `Cookie` 파사드를 사용할 수 있습니다.

    use Illuminate\Support\Facades\Cookie;

    $value = Cookie::get('name');

#### 쿠키를 Response 에 추가하기

외부로 나가는 `Illuminate\Http\Response`인스턴스에 `cookie` 메소드를 사용하여 쿠키를 추가할 수 있습니다. 메소드에는 이름과 값 그리고 쿠키가 얼마나 유효한지 결정하는 분단위의 값이 전달되어야 합니다.

    return response('Hello World')->cookie(
        'name', 'value', $minutes
    );

`cookie` 메소드는 또한 자주 사용되지 않는 몇가지 인자를 더 받아들입니다. 일반적으로 이 인자들은 PHP의 내장된 [setcookie](https://secure.php.net/manual/en/function.setcookie.php) 메소드에 제공되는 인자들과 동일한 목적과 의미가 있습니다.

    return response('Hello World')->cookie(
        'name', 'value', $minutes, $path, $domain, $secure, $httpOnly
    );

또는, 애플리케이션의 response에 쿠키를 추가하기 위해 `Cookie` 파사드를 이용하여 "queue"할 수 있습니다. `queue` 메소드는 `Cookie` 인스턴스나 `Cookie` 인스턴스를 생성하는데 필요한 인자를 받습니다. 이 쿠키는 response가 브라우저로 전달되기 전에 추가됩니다.

    Cookie::queue(Cookie::make('name', 'value', $minutes));

    Cookie::queue('name', 'value', $minutes);

#### 쿠키 인스턴스 생성하기

나중에 response 인스턴스에 넣을 수 있는 `Symfony\Component\HttpFoundation\Cookie`인스턴스를 생성하려면, 글로벌 `cookie` 헬퍼 함수를 사용할 수 있습니다. 이 쿠키는 response 인스턴스에 첨부하지 않는 한 클라이언트에게 다시 보내지지 않습니다.

    $cookie = cookie('name', 'value', $minutes);

    return response('Hello World')->cookie($cookie);

<a name="files"></a>
## 파일처리

<a name="retrieving-uploaded-files"></a>
### 업로드된 파일 조회하기

`Illuminate\Http\Request` 인스턴스에서 `file` 메소드를 사용하거나 동적 속성을 사용하여 업로드된 파일에 엑세스 할 수 있습니다. `file` 메소드는 PHP `SplFileInfo`클래스를 상속한 `Illuminate\Http\UploadedFile` 클래스의 인스턴스를 반환하고 파일과 상호작용할 수 있는 다양한 메소드를 제공합니다.

    $file = $request->file('photo');

    $file = $request->photo;

`hasFile` 메소드를 사용하면 request에 파일을 가지고 있는지 확인할 수 있습니다.

    if ($request->hasFile('photo')) {
        //
    }

#### 성공적으로 업로드 되었는지 확인하기

파일이 현재 존재하는지 확인하는데 더하여, `isValid` 메소드를 사용하여 업로드된 파일에 아무런 문제가 없는지 확인할 수 있습니다.

    if ($request->file('photo')->isValid()) {
        //
    }

#### 파일 경로 & 확장자

`UploadedFile` 클래스는 파일의 전체 경로와 확장자에 엑세스 할 수 있는 메소드를 가지고 있습니다. `extension` 메소드는 그 내용에 따라 파일의 확장자를 추측하려고 시도합니다. 이 확장자는 클라이언트가 제공한 확장자와는 다를 수 있습니다.

    $path = $request->photo->path();

    $extension = $request->photo->extension();

#### 기타 파일 관련 메소드들

`UploadedFile` 인스턴스에 다양한 다른 메소드들이 제공되어 있습니다. 이 메소드들에 대해 더 많은 정보를 얻으려면 [클래스의 API documentation](https://api.symfony.com/3.0/Symfony/Component/HttpFoundation/File/UploadedFile.html)을 확인해보십시오.

<a name="storing-uploaded-files"></a>
### 업로드된 파일 저장하기

업로드된 파일을 저장하려면, 일반적으로 설정된 [파일시스템](/docs/{{version}}/filesystem)중 하나를 사용합니다. `UploadedFile` 클래스는 업로드된 파일을 로컬 파일 시스템이나 아마존 S3 와 같은 클라우드 스토리지 디스크 중에 하나로 이동 시킬 수 있는 `store` 메소드를 가지고 있습니다.

`store` 메소드는 파일 시스템에 구성된 루트 디렉토리로 부터 파일이 어디에 저장되어야 할지에 대한 경로를 전달 받습니다. 파일의 이름은 자동으로 고유한 ID로 생성되므로 이 경로에는 파일 이름을 포함하지 않아야 합니다.

`store` 메소드는 파일이 저장되는데 사용될 디스크 이름을 두 번째 선택 인자로 허용합니다. 메소드는 디스크의 루트를 기준으로 파일의 경로를 반환합니다.

    $path = $request->photo->store('images');

    $path = $request->photo->store('images', 's3');

파일 이름이 자동으로 생성되지 않기를 원한다면, 경로와, 파일이름 그리고 디스크 이름을 인자로 받아 들이는 `storeAs` 메소드를 사용할 수 있습니다.

    $path = $request->photo->storeAs('images', 'filename.jpg');

    $path = $request->photo->storeAs('images', 'filename.jpg', 's3');


<a name="configuring-trusted-proxies"></a>
## 신뢰할 수 있는 프록시 설정하기

TLS / SSL 인증서가 적용된 로드 밸런서 뒤에서 애플리케이션을 실행할 때 애플리케이션에서 HTTPS 링크가 생성되지 않는 경우가 있습니다. 일반적으로 이는 애플리케이션이 포트 80의 로드 밸런서에서 전송되는 트래픽 뒤에 위치해서, HTTPS 링크를 생성해야 한다는 것을 알지 못하기 때문입니다.

이 문제를 해결하기 위해서, 라라벨 애플리케이션에는 `App\Http\Middleware\TrustProxies` 미들웨어를 사용하여 손쉽게 애플리케이션에서 신뢰할 수 있는 로드밸런서 또는 프록시를 설정할 수 있습니다. 신뢰할 수 있는 프록시들은 이 미들웨어의 `$proxies` 속성 배열에 지정해놓으면 됩니다. 신뢰할 수 있는 프록시를 구성하는 것 이외에도 신뢰해야 하는 프록시 헤더를 설정 할 수 있습니다.

    <?php

    namespace App\Http\Middleware;

    use Fideloper\Proxy\TrustProxies as Middleware;
    use Illuminate\Http\Request;

    class TrustProxies extends Middleware
    {
        /**
         * The trusted proxies for this application.
         *
         * @var array
         */
        protected $proxies = [
            '192.168.1.1',
            '192.168.1.2',
        ];

        /**
         * The headers that should be used to detect proxies.
         *
         * @var string
         */
        protected $headers = Request::HEADER_X_FORWARDED_ALL;
    }

> {팁} 만약 여러분이 AWS Elastic 로드 발란싱을 사용하고 있다면, 여러분의 `$headers` 값은 `Request::HEADER_X_FORWARDED_AWS_ELB`이 되어야 합니다. `$headers`에 사용될 수 있는 상수에 대한 다른 정보는, Symfony 문서의 [trusting proxies](https://symfony.com/doc/current/deployment/proxies.html)를 참고하십시오.

#### 모든 프록시 신뢰하기

아마존 AWS 또는 다른 "클라우드" 로드밸런서를 사용하는 경우에는, 실제 로드밸런서의 IP를 알 수가 없습니다. 이 경우, 모든 프록시를 신뢰할 수 있도록 하기 위해서 `*` 를 사용할 수 있습니다.

    /**
     * The trusted proxies for this application.
     *
     * @var array
     */
    protected $proxies = '*';
