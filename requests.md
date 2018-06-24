# HTTP Requests

- [Request 엑세스 하기](#accessing-the-request)
    - [기본적인 Request 정보](#basic-request-information)
    - [PSR-7 Requests](#psr7-requests)
- [입력값 조회하기](#retrieving-input)
    - [이전 입력값 확인하기](#old-input)
    - [쿠키](#cookies)
    - [파일처리](#files)

<a name="accessing-the-request"></a>
## Request 엑세스 하기

의존성 주입을 통해서 현재 HTTP request의 인스턴스를 획득하기 위해서는 컨트롤러 생성자나 메소드에서 `Illuminate\Http\Request` 클래스를 타입힌트해야 합니다. 현재 request의 인스턴스는 [서비스 컨테이너](/docs/{{version}}/container)에 의해서 자동으로 주입될 것입니다. 

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

만약 컨트롤러 메소드에서 라우터 파라미터로 부터 값을 받아야 한다면 의존성을 지정한 뒤에 라우트 인자를 나열하면 됩니다. 예를 들어 다음과 같이 라우트가 정의되어 있을 수 있습니다: 

    Route::put('user/{id}', 'UserController@update');

다음과 같이 컨트롤러 메소드를 정의하면 `Illuminate\Http\Request`를 타입힌트하여 라우트 파라미터 `id`에 접근할 수 있습니다: 

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

<a name="basic-request-information"></a>
### 기본적인 Request 정보

`Illuminate\Http\Request` 인스턴스는 애플리케이션의 HTTP request를 검사할 수 있는 다양한 메소드를 제공하며 `Symfony\Component\HttpFoundation\Request` 클래스를 상속 받고 있습니다. 다음은 이 클래스에 사용할 수 있는 몇몇 유용한 메소드입니다. 

#### Request URI 조회하기

`path` 메소드는 request의 URI를 반환합니다. 따라서 들어오는 request가 `http://domain.com/foo/bar`를 대상으로 한다면 `path` 메소드는 `foo/bar`를 반환합니다: 

    $uri = $request->path();

`is` 메소드는 들어오는 request가 특정 패턴에 상응한다는 것을 확인할 수 있게 해줍니다. 이 메소드를 활용할 때 `*` 기호를 와일드카드로 쓸 수 있습니다: 

    if ($request->is('admin/*')) {
        //
    }

경로 정보뿐만 아니라 전체 URL를 가져오려면 request 인스턴스에 `url` 메소드나 `fullUrl` 메소드를 사용하면 됩니다: 

    // Without Query String...
    $url = $request->url();

    // With Query String...
    $url = $request->fullUrl();

또한 전체 URL과 쿼리파라미터 가져올 수도 있습니다. 예를 들어 다음의 `http://domain.com/foo`에 대한 요청의 경우 메소드 결과는 `http://domain.com/foo?bar=baz`를 반환합니다:  

    $url = $request->fullUrlWithQuery(['bar' => 'baz']);

#### Request HTTP 메소드 조회하기

`method` 메소드는 request에 대해 HTTP 메소드를 반환합니다. HTTP 메소드가 특정 문자열에 대응하는 것을 확인하기 위해 `isMethod` 메소드 또한 사용할 수 있습니다: 

    $method = $request->method();

    if ($request->isMethod('post')) {
        //
    }

<a name="psr7-requests"></a>
### PSR-7 Requests

PSR-7 표준은 요청과 응답을 포함한 HTTP 메세지들에 대한 인터페이스를 지정합니다. PSR-7 요청의 인스턴스를 갖기 위해서는 우선 몇 개의 라이브러리를 설치해야 합니다. 라라벨은 Symfony HTTP Message Bridge 컴포넌트를 사용하여 라라벨의 전형적인 요청과 응답을 PSR-7에 맞는 구현체로 변환합니다. 

    composer require symfony/psr-http-message-bridge

    composer require zendframework/zend-diactoros

이 라이브러리들을 설치하였다면 단순히 request를 라우트나 컨트롤러에 타입힌트하는 것만으로도 PSR-7 request를 얻을 수 있습니다. 

    use Psr\Http\Message\ServerRequestInterface;

    Route::get('/', function (ServerRequestInterface $request) {
        //
    });

라우트나 컨트롤러에서 반환된 PSR-7 response 인스턴스는 자동으로 라라벨 response 인스턴스로 변환되어 프레임워크에 표시될 것입니다. 

<a name="retrieving-input"></a>
## 입력값 조회하기

#### 입력값 조회하기

몇 개의 단순한 메소드들을 사용하면 `Illuminate\Http\Request` 인스턴스에서 모든 사용자 입력에 접근할 수 있습니다. 입력은 모든 HTTP 메소드에 대해서 동일한 방식으로 엑세스 되기 때문에 request를 위해 사용된 HTTP 메소드에 대해서는 전혀 걱정할 필요가 없습니다: 

    $name = $request->input('name');

`input` 메소드에 두번째 인자로 기본값을 전달할 수 있습니다. Request에 요청된 입력 값이 존재하지 않는다면 기본값이 반환됩니다: 

    $name = $request->input('name', 'Sally');

배열 입력을 가진 폼에서 동작할 때에는, 배열에 접근하기 위하여 "dot" 표기법을 사용할 수 있습니다: 

    $name = $request->input('products.0.name');

    $names = $request->input('products.*.name');

#### JSON 입력 값 조회하기

애플리케이션에 JSON 요청이 전달되어 `Content-Type` 헤더 속성이 `application/json` 으로 지정되어 있다면 `input` 메소드를 통해서 JSON 데이터에 접근할 수 있습니다. 또한 "점" 문법을 통해서 JSON 배열의 깊이 있는 요소에도 접근할 수 있습니다.   

    $name = $request->input('user.name');

#### 입력값이 존재하는지 확인하기

Request에 어떤 값이 존재하는지 확인하기 위해서 `has` 메소드를 사용할 수 있습니다. 값이 존재하면서 문자열이 비어있지 않을 때에만 `has` 메소드는 `true`를 반환합니다: 

    if ($request->has('name')) {
        //
    }

#### 모든 입력 데이터 조회하기

또한 `all` 메소드를 이용하여 모든 입력 데이터를 `배열` 형태로 조회할 수 있습니다. 

    $input = $request->all();

#### 입력 데이터의 한 부분 조회하기

입력 데이터의 일부분만 조회하기 위해서 `only`와 `except` 메소드를 사용할 수 있습니다. 이 두 메소드 모두 하나의 `배열` 또는 동적인 인자의 목록을 받아 들입니다:

    $input = $request->only(['username', 'password']);

    $input = $request->only('username', 'password');

    $input = $request->except(['credit_card']);

    $input = $request->except('credit_card');

#### 동적 속성

또한 `Illuminate\Http\Request` 인스턴스에서 동적 송석을 사용하여 사용자 입력에 엑세스 할 수 있습니다. 예를 들어, 애플리케이션의 form 중에 하나가 `name` 필드를 가지고 있다면, 다음과 같이 전달된 필드의 값에 엑세스 할 수 있습니다:

    $name = $request->name;

동적 속성을 사용할 때, 라라벨은 먼저 request-요청에서 파라미터의 값을 찾아보고 라우트 파라미터를 찾습니다.

<a name="old-input"></a>
### 이전 입력값 확인하기

라라벨은 한 request의 입력을 다음 request 중에도 유지할 수 있도록 해줍니다. 이 기능은 특히 유효성 검사 오류를 감지한 후 폼을 다시 채워 넣을 때 유용합니다. 하지만 라라벨에 포함된 [유효성 검사 서비스](/docs/{{version}}/validation)를 이용한다면 몇몇 유효성 검사 기능들이 자동으로 이 기능을 호출하기 때문에 수동으로 이 메소드들을 사용해야 할 가능성은 낮습니다: 

#### 입력값을 세션에 임시 저장하기

`Illuminate\Http\Request` 인스턴스의 `flash` 메소드는 현재의 입력들을 [세션](/docs/{{version}}/session)에 저장하여, 사용자의 다음 request 동안에도 값을 사용할 수 있게 해줍니다: 

    $request->flash();

`flashOnly`와 `flashExcept` 메소드를 이용하여 request 데이터의 부분을 세션에 임시 저장(flash)할 수 있습니다. 

    $request->flashOnly(['username', 'email']);

    $request->flashExcept('password');

#### 리다이렉트 할 때 입력값을 세션에 임시저장하기

대부분 이전 페이지로 리다이렉트 하면서 입력값을 임시 저장 하기를 원하기 때문에, 이를 위해 리다이렉트와 함께 입력값 임시 저장을 메소드 체이닝으로 사용할 수 있습니다. 

    return redirect('form')->withInput();

    return redirect('form')->withInput($request->except('password'));

#### 이전 데이터 조회하기

이전 Request 에서 저장된 입력값을 검색하기 위해서는 `Request` 인스턴스의 `old` 메소드를 사용하면 됩니다. `old` 메소드는 [세션](/docs/{{version}}/session)에 저장된 입력 데이터를 불러오기 위한 편리한 헬퍼를 제공합니다:

    $username = $request->old('username');

라라벨은 글로벌 `old` 헬퍼 함수도 제공합니다. 블레이드 템플릿 안에서 지난 입력값을 보여주려면 `old` 헬퍼 함수를 사용하는 것이 보다 편리합니다. 주어진 문자열에 대한 이전 입력값이 존재하지 않는다면, `null` 이 반환될 것입니다: 

    <input type="text" name="username" value="{{ old('username') }}">

<a name="cookies"></a>
### 쿠기

#### Request 에서 쿠키 조회하기

모든 쿠키는 라라벨 프레임워크에서 인증 코드와 함께 암호화 됩니다. 이 것은 클라이언트가 변경되었을 때는, 쿠키가 유효하지 않다는 것을 의미합니다. Request에서 쿠키 값을 가져오기 위해서는 `Illuminate\Http\Request` 인스턴스에서 `cookie` 메소드를 사용하면 됩니다: 

    $value = $request->cookie('name');

#### 새로운 쿠키를 Response 에 추가하기

라라벨은 새로운 `Symfony\Component\HttpFoundation\Cookie` 인스턴스를 생성하기 위한 간단한 팩토리의 역할을 하는 글로벌 `cookie` 헬퍼 함수를 제공합니다. 쿠키를 `Response` 인스턴스에 추가하려면 `withCookie` 메소드를 사용하면 됩니다:

    $response = new Illuminate\Http\Response('Hello World');

    $response->withCookie('name', 'value', $minutes);

    return $response;

5년 동안 오랫동안 유지되는 쿠키를 만드려면 우선 인자 없이 `cookie` 헬퍼를 호출하고 `forever` 메소드를 반환된 쿠키 팩토리에 체이닝하면 됩니다: 

    $response->withCookie(cookie()->forever('name', 'value'));

<a name="files"></a>
### 파일처리

#### 업로드된 파일 조회하기

`file` 메소드를 사용하여 `Illuminate\Http\Request` 인스턴스에 포함된 업로드된 파일에 접근할 수 있습니다. `file` 메소드로 반환된 객체는 파일과 상호작용할 수 있는 다양한 메소드를 제공하는 `Symfony\Component\HttpFoundation\File\UploadedFile` 클래스의 인스턴스이며 PHP `SplFileInfo` 클래스를 상속하고 입니다: 

    $file = $request->file('photo');

`hasFile` 메소드를 사용하면 request에 파일을 가지고 있는지 확인할 수 있습니다. 
    if ($request->hasFile('photo')) {
        //
    }

#### 성공적인 업로드 확인하기

파일의 존재 여부를 확인하는 것에 더하여, `isValid` 메소드를 통해 파일이 문제 없이 업로드 되었다는 것을 확인할 수 있습니다: 

    if ($request->file('photo')->isValid()) {
        //
    }

#### 업로드된 파일 이동하기

업로드된 파일을 새로운 위치로 옮기려면 `move` 메소드를 사용하면 됩니다. 이 메소드는 파일을 (PHP 설정에 따라) 임시업로드 위치에서 사용자가 원하는 위치로 옮겨집니다: 

    $request->file('photo')->move($destinationPath);

    $request->file('photo')->move($destinationPath, $fileName);

#### 기타 파일 관련 메소드들

`UploadedFile` 인스턴스에 다양한 다른 메소드들이 제공되어 있습니다. 이 메소드들에 대해 더 많은 정보를 얻으려면 [클래스의 API documentation](http://api.symfony.com/3.0/Symfony/Component/HttpFoundation/File/UploadedFile.html)을 확인해보십시오.
