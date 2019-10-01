# HTTP Requests
# HTTP Requests-요청

- [Accessing The Request](#accessing-the-request)
- [Request-요청 엑세스 하기](#accessing-the-request)
    - [Request Path & Method](#request-path-and-method)
    - [Request-요청 경로 & 메소드](#request-path-and-method)
    - [PSR-7 Requests](#psr7-requests)
    - [PSR-7 Requests-요청](#psr7-requests)
- [Input Trimming & Normalization](#input-trimming-and-normalization)
- [입력값 Trim 처리 & 일반화처리](#input-trimming-and-normalization)
- [Retrieving Input](#retrieving-input)
- [입력값 조회하기](#retrieving-input)
    - [Old Input](#old-input)
    - [이전 입력값 확인하기](#old-input)
    - [Cookies](#cookies)
    - [쿠키](#cookies)
- [Files](#files)
- [파일처리](#files)
    - [Retrieving Uploaded Files](#retrieving-uploaded-files)
    - [업로드된 파일 조회하기](#retrieving-uploaded-files)
    - [Storing Uploaded Files](#storing-uploaded-files)
    - [업로드된 파일 저장하기](#storing-uploaded-files)
- [Configuring Trusted Proxies](#configuring-trusted-proxies)
- [신뢰할 수 있는 프록시 설정하기](#configuring-trusted-proxies)

<a name="accessing-the-request"></a>
## Accessing The Request
## Request 엑세스 하기

To obtain an instance of the current HTTP request via dependency injection, you should type-hint the `Illuminate\Http\Request` class on your controller method. The incoming request instance will automatically be injected by the [service container](/docs/{{version}}/container):

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

#### Dependency Injection & Route Parameters
#### 의존성 주입 & 라우트 파라미터

If your controller method is also expecting input from a route parameter you should list your route parameters after your other dependencies. For example, if your route is defined like so:

만약 컨트롤러 메소드에서 라우트 파라미터로 부터 입력값을 받아야 한다면, 다른 의존성을 지정한 뒤에 라우트 파라미터를 나열해야 합니다. 예를 들어 라우트는 다음과 같이 정의될 수 있습니다.

    Route::put('user/{id}', 'UserController@update');

You may still type-hint the `Illuminate\Http\Request` and access your route parameter `id` by defining your controller method as follows:

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

#### Accessing The Request Via Route Closures
#### 라우트 클로저를 통해서 Request 엑세스하기

You may also type-hint the `Illuminate\Http\Request` class on a route Closure. The service container will automatically inject the incoming request into the Closure when it is executed:

또한 라우트 클로저에서도 `Illuminate\Http\Request` 클래스를 타입힌트 할 수 있습니다. 서비스 컨테이너는 클로저가 실행될 때 자동으로 유입된 request 를 주입할 것입니다.

    use Illuminate\Http\Request;

    Route::get('/', function (Request $request) {
        //
    });

<a name="request-path-and-method"></a>
### Request Path & Method
### Request 경로 & 메소드

The `Illuminate\Http\Request` instance provides a variety of methods for examining the HTTP request for your application and extends the `Symfony\Component\HttpFoundation\Request` class. We will discuss a few of the most important methods below.

`Illuminate\Http\Request` 인스턴스는 애플리케이션의 HTTP request를 검사할 수 있는 다양한 메소드를 제공하며 `Symfony\Component\HttpFoundation\Request` 클래스를 상속 받고 있습니다. 몇가지 가장 중요한 메소드를 알아보겠습니다.

#### Retrieving The Request Path
#### Request 경로 조회하기

The `path` method returns the request's path information. So, if the incoming request is targeted at `http://domain.com/foo/bar`, the `path` method will return `foo/bar`:

`path` 메소드는 request의 경로정보를 반환합니다. 따라서 들어오는 request가 `http://domain.com/foo/bar`를 대상으로 한다면 `path` 메소드는 `foo/bar`를 반환합니다.

    $uri = $request->path();

The `is` method allows you to verify that the incoming request path matches a given pattern. You may use the `*` character as a wildcard when utilizing this method:

`is` 메소드는 들어오는 request가 특정 패턴에 상응한다는 것을 확인할 수 있게 해줍니다. 이 메소드를 활용할 때 `*` 기호를 와일드카드로 쓸 수 있습니다.

    if ($request->is('admin/*')) {
        //
    }

#### Retrieving The Request URL
#### Request URI 조회하기

To retrieve the full URL for the incoming request you may use the `url` or `fullUrl` methods. The `url` method will return the URL without the query string, while the `fullUrl` method includes the query string:

유입되는 request 의 전체 URL을 조회하기 위해서 `url` 또는 `fullUrl` 메소드를 사용할 수 있습니다. `url` 메소드는 쿼리 스트링 없는 URL을, `fullUrl` 은 쿼리 스트링을 포함한 URL을 반환합니다.

    // Without Query String...
    $url = $request->url();

    // With Query String...
    $url = $request->fullUrl();

#### Retrieving The Request Method
#### Request HTTP 메소드(verb) 조회하기

The `method` method will return the HTTP verb for the request. You may use the `isMethod` method to verify that the HTTP verb matches a given string:

`method` 메소드는 request에 대해 HTTP 메소드를 반환합니다. HTTP 메소드가 특정 문자열에 대응하는 것을 확인하기 위해 `isMethod` 메소드를 사용할 수 있습니다.

    $method = $request->method();

    if ($request->isMethod('post')) {
        //
    }

<a name="psr7-requests"></a>
### PSR-7 Requests
### PSR-7 Requests

The [PSR-7 standard](https://www.php-fig.org/psr/psr-7/) specifies interfaces for HTTP messages, including requests and responses. If you would like to obtain an instance of a PSR-7 request instead of a Laravel request, you will first need to install a few libraries. Laravel uses the *Symfony HTTP Message Bridge* component to convert typical Laravel requests and responses into PSR-7 compatible implementations:

[PSR-7 표준](https://www.php-fig.org/psr/psr-7/)은 요청과 응답을 포함한 HTTP 메세지들에 대한 인터페이스를 지정합니다. 라라벨의 request 대신 PSR-7 요청의 인스턴스를 획득하기 위해서는 우선 몇 개의 라이브러리를 설치해야 합니다. 라라벨은 *Symfony HTTP Message Bridge* 컴포넌트를 사용하여 일반적인 라라벨의 request-요청과 response-응답을 PSR-7에 맞는 구현체로 변환합니다.

    composer require symfony/psr-http-message-bridge
    composer require zendframework/zend-diactoros

Once you have installed these libraries, you may obtain a PSR-7 request by type-hinting the request interface on your route Closure or controller method:

이 라이브러리들을 설치하였다면 라우트 클로저나 컨트롤러 메소드에 request를 타입힌트하여 PSR-7 request를 얻을 수 있습니다.

    use Psr\Http\Message\ServerRequestInterface;

    Route::get('/', function (ServerRequestInterface $request) {
        //
    });

> {tip} If you return a PSR-7 response instance from a route or controller, it will automatically be converted back to a Laravel response instance and be displayed by the framework.

> {tip} 라우트나 컨트롤러에서 반환된 PSR-7 response 인스턴스는 자동으로 라라벨 response 인스턴스로 변환되어 프레임워크에 표시될 것입니다.

<a name="input-trimming-and-normalization"></a>
## Input Trimming & Normalization
## 입력값 Trim 처리 & 일반화처리

By default, Laravel includes the `TrimStrings` and `ConvertEmptyStringsToNull` middleware in your application's global middleware stack. These middleware are listed in the stack by the `App\Http\Kernel` class. These middleware will automatically trim all incoming string fields on the request, as well as convert any empty string fields to `null`. This allows you to not have to worry about these normalization concerns in your routes and controllers.

기본적으로 라라벨은 애플리케이션의 글로벌 미들웨어 스택에 `TrimStrings` 그리고 `ConvertEmptyStringsToNull` 미들웨어를 포함하고 있습니다. 이 미들웨어는 `App\Http\Kernel` 클래스의 미들웨어 스택에 나열되어 있습니다. 이 미들웨어는 request-요청에 유입되는 모든 문자 필드들을 자동으로 trim 처리하고, 빈 문자필드는 `null`로 변환합니다. 따라서 라우트와 컨트롤러에서 이러한 일반화 문제를 걱정할 필요가 없습니다.

If you would like to disable this behavior, you may remove the two middleware from your application's middleware stack by removing them from the `$middleware` property of your `App\Http\Kernel` class.

이 동작을 비활성화 시키려면, `App\Http\Kernel` 클래스의 `$middleware` 속성에서 두 미들웨어를 제거하면 됩니다.

<a name="retrieving-input"></a>
## Retrieving Input
## 입력값 조회하기

#### Retrieving All Input Data
#### 모든 입력값 조회하기

You may also retrieve all of the input data as an `array` using the `all` method:

`all` 메소드를 사용하여 모든 입력데이터를 `배열` 로 조회할 수 있습니다.

    $input = $request->all();

#### Retrieving An Input Value
#### 입력값 조회하기

Using a few simple methods, you may access all of the user input from your `Illuminate\Http\Request` instance without worrying about which HTTP verb was used for the request. Regardless of the HTTP verb, the `input` method may be used to retrieve user input:

몇 개의 단순한 메소드들을 사용하면 어떤 HTTP verb 가 request 에 사용되었는지에 대한 걱정없이 `Illuminate\Http\Request` 인스턴스에서 모든 사용자 입력에 접근할 수 있습니다. HTTP verb에 관계없이 `input` 메서드는 사용자 입력을 조회하는데 사용됩니다.

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

#### Retrieving Input From The Query String
#### 쿼리 스트링에서 입력값 조회하기

While the `input` method retrieves values from entire request payload (including the query string), the `query` method will only retrieve values from the query string:

`input` 메소드가 요청-request 전체의 payload(쿼리 스트링을 포함하여)에서 값을 조회한다면, `query` 메소드는 쿼리 스트링에서만 값을 조회합니다.

    $name = $request->query('name');

If the requested query string value data is not present, the second argument to this method will be returned:

찾고자 하는 데이터가 존재하지 않을 때를 위해서 메소드의 두번째 인자로 기본값을 전달할 수 있습니다.

    $name = $request->query('name', 'Helen');

You may call the `query` method without any arguments in order to retrieve all of the query string values as an associative array:

`query` 메소드를 호출할 때 아무런 인자를 전달하지 않는다면, 전체 쿼리 스트링의 값들을 배열 형태로 반환합니다.

    $query = $request->query();

#### Retrieving Input Via Dynamic Properties
#### 동적 속성을 통한 입력값 조회하기

You may also access user input using dynamic properties on the `Illuminate\Http\Request` instance. For example, if one of your application's forms contains a `name` field, you may access the value of the field like so:

`Illuminate\Http\Request` 인스턴스의 동적 속성을 사용하여 사용자 입력에 엑세스할 수도 있습니다. 예를 들어 애플리케이션의 form 중에 하나가 `name` 필드를 가지고 있다면, 다음과 같이 필드의 값에 엑세스 할 수 있습니다.

    $name = $request->name;

When using dynamic properties, Laravel will first look for the parameter's value in the request payload. If it is not present, Laravel will search for the field in the route parameters.

동적 속성을 사용할 때, 라라벨은 먼저 request payload 안에 있는 파라미터의 값을 찾습니다. 만약 값이 없다면 라라벨은 라우트 파라미터 안에 있는 필드를 찾을 것입니다.

#### Retrieving JSON Input Values
#### JSON 입력 값 조회하기

When sending JSON requests to your application, you may access the JSON data via the `input` method as long as the `Content-Type` header of the request is properly set to `application/json`. You may even use "dot" syntax to dig into JSON arrays:

애플리케이션에 JSON 요청이 전달되어 `Content-Type` 헤더 속성이 `application/json` 으로 지정되어 있다면 `input` 메소드를 통해서 JSON 데이터에 접근할 수 있습니다. 또한 "점" 문법을 통해서 JSON 배열에 접근할 수도 있습니다.

    $name = $request->input('user.name');

#### Retrieving A Portion Of The Input Data
#### 입력 데이터의 한 부분 조회하기

If you need to retrieve a subset of the input data, you may use the `only` and `except` methods. Both of these methods accept a single `array` or a dynamic list of arguments:

입력 데이터의 일부분만 조회하기 위해서 `only`와 `except` 메소드를 사용할 수 있습니다. 이 두 메소드 모두 하나의 `배열` 또는 동적인 인자의 목록을 받아 들입니다.

    $input = $request->only(['username', 'password']);

    $input = $request->only('username', 'password');

    $input = $request->except(['credit_card']);

    $input = $request->except('credit_card');

> {tip} The `only` method returns all of the key / value pairs that you request; however, it will not return key / value pairs that are not present on the request.

> {tip} `only` 메소드는 유입되는 request-요청에서 입력한 키 / 값 쌍을 반환합니다. 그렇지만 현재 request 에서 존재하지 않는 키/값은 반환하지 않습니다.

    $input = $request->intersect(['username', 'password']);

#### Determining If An Input Value Is Present
#### 입력값이 존재하는지 확인하기

You should use the `has` method to determine if a value is present on the request. The `has` method returns `true` if the value is present on the request:

Request에 어떤 값이 존재하는지 확인하기 위해서 `has` 메소드를 사용해야 합니다. `has` 메소드는 현재 request 에서 값이 존재할 때 `true`를 반환합니다.

    if ($request->has('name')) {
        //
    }

When given an array, the `has` method will determine if all of the specified values are present:

`has` 메소드에 배열이 주어지면, 지정된 모든 값이 존재하는지 확인하게 됩니다.

    if ($request->has(['name', 'email'])) {
        //
    }

If you would like to determine if a value is present on the request and is not empty, you may use the `filled` method:

주어진 변수값이 현재 request 에 존재하고 비어 있지 않은 것을 확인하려면 `filled` 메소드를 사용하면 됩니다.

    if ($request->filled('name')) {
        //
    }

<a name="old-input"></a>
### Old Input
### 이전 입력값 확인하기

Laravel allows you to keep input from one request during the next request. This feature is particularly useful for re-populating forms after detecting validation errors. However, if you are using Laravel's included [validation features](/docs/{{version}}/validation), it is unlikely you will need to manually use these methods, as some of Laravel's built-in validation facilities will call them automatically.

라라벨은 한 request의 입력을 다음 request 중에도 유지할 수 있도록 해줍니다. 이 기능은 특히 유효성 검사 오류를 감지한 후 폼을 다시 채워 넣을 때 유용합니다. 하지만 라라벨에 포함된 [유효성 검사 기능](/docs/{{version}}/validation)를 이용한다면 몇몇 유효성 검사 기능들이 자동으로 이 기능을 호출하기 때문에 수동으로 이 메소드들을 사용해야 할 가능성은 낮습니다.

#### Flashing Input To The Session
#### 입력값을 세션에 임시 저장하기

The `flash` method on the `Illuminate\Http\Request` class will flash the current input to the [session](/docs/{{version}}/session) so that it is available during the user's next request to the application:

`Illuminate\Http\Request` 클래스의 `flash` 메소드는 현재의 입력들을 [세션](/docs/{{version}}/session)에 저장하여, 사용자의 다음 request 동안에도 값을 사용할 수 있게 해줍니다.

    $request->flash();

You may also use the `flashOnly` and `flashExcept` methods to flash a subset of the request data to the session. These methods are useful for keeping sensitive information such as passwords out of the session:

`flashOnly`와 `flashExcept` 메소드를 이용하여 request 데이터의 일부분을 세션에 임시 저장(flash)할 수 있습니다. 이 메소드들은 패스워드와 같은 민감한 정보들을 세션에 포함하지 않도록 하는데 유용합니다.

    $request->flashOnly(['username', 'email']);

    $request->flashExcept('password');

#### Flashing Input Then Redirecting
#### 입력값을 임시저장한 후 리다이렉트하기

Since you often will want to flash input to the session and then redirect to the previous page, you may easily chain input flashing onto a redirect using the `withInput` method:

종종 입력값을 세션에 임시 저장 하고 이전 페이지로 리다이렉트 하기를 원할 수 있는데, `withInput` 메소드를 이용하여 리다이렉트시 입력값 임시 저장이 되도록 간단하게 메소드 체이닝할 수 있습니다.

    return redirect('form')->withInput();

    return redirect('form')->withInput(
        $request->except('password')
    );

#### Retrieving Old Input
#### 이전 입력값 조회하기

To retrieve flashed input from the previous request, use the `old` method on the `Request` instance. The `old` method will pull the previously flashed input data from the [session](/docs/{{version}}/session):

이전 request 에서 저장된 입력값을 조회하기 위해서는 `Request` 인스턴스의 `old` 메소드를 사용하면 됩니다. `old` 메소드는 [세션](/docs/{{version}}/session)에 저장된 입력 데이터를 꺼낼 것입니다.

    $username = $request->old('username');

Laravel also provides a global `old` helper. If you are displaying old input within a [Blade template](/docs/{{version}}/blade), it is more convenient to use the `old` helper. If no old input exists for the given field, `null` will be returned:

라라벨은 글로벌 `old` 헬퍼 함수도 제공합니다. [블레이드 템플릿](/docs/{{version}}/blade) 안에서 지난 입력값을 보여주려면 `old` 헬퍼 함수를 사용하는 것이 보다 편리합니다. 주어진 필드에 대한 이전 입력값이 존재하지 않는다면, `null` 이 반환될 것입니다.

    <input type="text" name="username" value="{{ old('username') }}">

<a name="cookies"></a>
### Cookies
### 쿠키

#### Retrieving Cookies From Requests
#### Request 에서 쿠키 조회하기

All cookies created by the Laravel framework are encrypted and signed with an authentication code, meaning they will be considered invalid if they have been changed by the client. To retrieve a cookie value from the request, use the `cookie` method on a `Illuminate\Http\Request` instance:

라라벨 프레임워크에서 생성된 모든 쿠키는 인증 코드와 함께 암호화 됩니다. 이것은 쿠키 정보가 클라이언트에 의해 변경되었을 경우 유효하지 않은 것으로 간주됨을 의미합니다. Request에서 쿠키 값을 가져오기 위해서는 `Illuminate\Http\Request` 인스턴스에서 `cookie` 메소드를 사용하십시오:

    $value = $request->cookie('name');

Alternatively, you may use the `Cookie` facade to access cookie values:

또한, 쿠키에 엑세스 하기 위해서 `Cookie` 파사드를 사용할 수 있습니다.

    use Illuminate\Support\Facades\Cookie;

    $value = Cookie::get('name');

#### Attaching Cookies To Responses
#### 쿠키를 Response 에 추가하기

You may attach a cookie to an outgoing `Illuminate\Http\Response` instance using the `cookie` method. You should pass the name, value, and number of minutes the cookie should be considered valid to this method:

외부로 나가는 `Illuminate\Http\Response`인스턴스에 `cookie` 메소드를 사용하여 쿠키를 추가할 수 있습니다. 메소드에는 이름과 값 그리고 쿠키가 얼마나 유효한지 결정하는 분단위의 값이 전달되어야 합니다.

    return response('Hello World')->cookie(
        'name', 'value', $minutes
    );

The `cookie` method also accepts a few more arguments which are used less frequently. Generally, these arguments have the same purpose and meaning as the arguments that would be given to PHP's native [setcookie](https://secure.php.net/manual/en/function.setcookie.php) method:

`cookie` 메소드는 또한 자주 사용되지 않는 몇가지 인자를 더 받아들입니다. 일반적으로 이 인자들은 PHP의 내장된 [setcookie](https://secure.php.net/manual/en/function.setcookie.php) 메소드에 제공되는 인자들과 동일한 목적과 의미가 있습니다.

    return response('Hello World')->cookie(
        'name', 'value', $minutes, $path, $domain, $secure, $httpOnly
    );

Alternatively, you can use the `Cookie` facade to "queue" cookies for attachment to the outgoing response from your application. The `queue` method accepts a `Cookie` instance or the arguments needed to create a `Cookie` instance. These cookies will be attached to the outgoing response before it is sent to the browser:

또는, 애플리케이션의 response에 쿠키를 추가하기 위해 `Cookie` 파사드를 이용하여 "queue"할 수 있습니다. `queue` 메소드는 `Cookie` 인스턴스나 `Cookie` 인스턴스를 생성하는데 필요한 인자를 받습니다. 이 쿠키는 response가 브라우저로 전달되기 전에 추가됩니다.

    Cookie::queue(Cookie::make('name', 'value', $minutes));

    Cookie::queue('name', 'value', $minutes);

#### Generating Cookie Instances
#### 쿠키 인스턴스 생성하기

If you would like to generate a `Symfony\Component\HttpFoundation\Cookie` instance that can be given to a response instance at a later time, you may use the global `cookie` helper. This cookie will not be sent back to the client unless it is attached to a response instance:

나중에 response 인스턴스에 넣을 수 있는 `Symfony\Component\HttpFoundation\Cookie`인스턴스를 생성하려면, 글로벌 `cookie` 헬퍼 함수를 사용할 수 있습니다. 이 쿠키는 response 인스턴스에 첨부하지 않는 한 클라이언트에게 다시 보내지지 않습니다.

    $cookie = cookie('name', 'value', $minutes);

    return response('Hello World')->cookie($cookie);

<a name="files"></a>
## Files
## 파일처리

<a name="retrieving-uploaded-files"></a>
### Retrieving Uploaded Files
### 업로드된 파일 조회하기

You may access uploaded files from a `Illuminate\Http\Request` instance using the `file` method or using dynamic properties. The `file` method returns an instance of the `Illuminate\Http\UploadedFile` class, which extends the PHP `SplFileInfo` class and provides a variety of methods for interacting with the file:

`Illuminate\Http\Request` 인스턴스에서 `file` 메소드를 사용하거나 동적 속성을 사용하여 업로드된 파일에 엑세스 할 수 있습니다. `file` 메소드는 PHP `SplFileInfo`클래스를 상속한 `Illuminate\Http\UploadedFile` 클래스의 인스턴스를 반환하고 파일과 상호작용할 수 있는 다양한 메소드를 제공합니다.

    $file = $request->file('photo');

    $file = $request->photo;

You may determine if a file is present on the request using the `hasFile` method:

`hasFile` 메소드를 사용하면 request에 파일을 가지고 있는지 확인할 수 있습니다.

    if ($request->hasFile('photo')) {
        //
    }

#### Validating Successful Uploads
#### 성공적으로 업로드 되었는지 확인하기

In addition to checking if the file is present, you may verify that there were no problems uploading the file via the `isValid` method:

파일이 현재 존재하는지 확인하는데 더하여, `isValid` 메소드를 사용하여 업로드된 파일에 아무런 문제가 없는지 확인할 수 있습니다.

    if ($request->file('photo')->isValid()) {
        //
    }

#### File Paths & Extensions
#### 파일 경로 & 확장자

The `UploadedFile` class also contains methods for accessing the file's fully-qualified path and its extension. The `extension` method will attempt to guess the file's extension based on its contents. This extension may be different from the extension that was supplied by the client:

`UploadedFile` 클래스는 파일의 전체 경로와 확장자에 엑세스 할 수 있는 메소드를 가지고 있습니다. `extension` 메소드는 그 내용에 따라 파일의 확장자를 추측하려고 시도합니다. 이 확장자는 클라이언트가 제공한 확장자와는 다를 수 있습니다.

    $path = $request->photo->path();

    $extension = $request->photo->extension();

#### Other File Methods
#### 기타 파일 관련 메소드들

There are a variety of other methods available on `UploadedFile` instances. Check out the [API documentation for the class](https://api.symfony.com/3.0/Symfony/Component/HttpFoundation/File/UploadedFile.html) for more information regarding these methods.

`UploadedFile` 인스턴스에 다양한 다른 메소드들이 제공되어 있습니다. 이 메소드들에 대해 더 많은 정보를 얻으려면 [클래스의 API documentation](https://api.symfony.com/3.0/Symfony/Component/HttpFoundation/File/UploadedFile.html)을 확인해보십시오.

<a name="storing-uploaded-files"></a>
### Storing Uploaded Files
### 업로드된 파일 저장하기

To store an uploaded file, you will typically use one of your configured [filesystems](/docs/{{version}}/filesystem). The `UploadedFile` class has a `store` method which will move an uploaded file to one of your disks, which may be a location on your local filesystem or even a cloud storage location like Amazon S3.

업로드된 파일을 저장하려면, 일반적으로 설정된 [파일시스템](/docs/{{version}}/filesystem)중 하나를 사용합니다. `UploadedFile` 클래스는 업로드된 파일을 로컬 파일 시스템이나 아마존 S3 와 같은 클라우드 스토리지 디스크 중에 하나로 이동 시킬 수 있는 `store` 메소드를 가지고 있습니다.

The `store` method accepts the path where the file should be stored relative to the filesystem's configured root directory. This path should not contain a file name, since a unique ID will automatically be generated to serve as the file name.

`store` 메소드는 파일 시스템에 구성된 루트 디렉토리로 부터 파일이 어디에 저장되어야 할지에 대한 경로를 전달 받습니다. 파일의 이름은 자동으로 고유한 ID로 생성되므로 이 경로에는 파일 이름을 포함하지 않아야 합니다.

The `store` method also accepts an optional second argument for the name of the disk that should be used to store the file. The method will return the path of the file relative to the disk's root:

`store` 메소드는 파일이 저장되는데 사용될 디스크 이름을 두 번째 선택 인자로 허용합니다. 메소드는 디스크의 루트를 기준으로 파일의 경로를 반환합니다.

    $path = $request->photo->store('images');

    $path = $request->photo->store('images', 's3');

If you do not want a file name to be automatically generated, you may use the `storeAs` method, which accepts the path, file name, and disk name as its arguments:

파일 이름이 자동으로 생성되지 않기를 원한다면, 경로와, 파일이름 그리고 디스크 이름을 인자로 받아 들이는 `storeAs` 메소드를 사용할 수 있습니다.

    $path = $request->photo->storeAs('images', 'filename.jpg');

    $path = $request->photo->storeAs('images', 'filename.jpg', 's3');


<a name="configuring-trusted-proxies"></a>
## Configuring Trusted Proxies
## 신뢰할 수 있는 프록시 설정하기

When running your applications behind a load balancer that terminates TLS / SSL certificates, you may notice your application sometimes does not generate HTTPS links. Typically this is because your application is being forwarded traffic from your load balancer on port 80 and does not know it should generate secure links.

TLS / SSL 인증서가 적용된 로드 밸런서 뒤에서 애플리케이션을 실행할 때 애플리케이션에서 HTTPS 링크가 생성되지 않는 경우가 있습니다. 일반적으로 이는 애플리케이션이 포트 80의 로드 밸런서에서 전송되는 트래픽 뒤에 위치해서, HTTPS 링크를 생성해야 한다는 것을 알지 못하기 때문입니다.

To solve this, you may use the `App\Http\Middleware\TrustProxies` middleware that is included in your Laravel application, which allows you to quickly customize the load balancers or proxies that should be trusted by your application. Your trusted proxies should be listed as an array on the `$proxies` property of this middleware. In addition to configuring the trusted proxies, you may configure the proxy `$headers` that should be trusted:

이 문제를 해결하기 위해서, 라라벨 애플리케이션에는 `App\Http\Middleware\TrustProxies` 미들웨어를 사용하여 손쉽게 애플리케이션에서 신뢰할 수 있는 로드밸런서 또는 프록시를 설정할 수 있습니다. 신뢰할 수 있는 프록시들은 이 미들웨어의 `$proxies` 속성 배열에 지정해놓으면 됩니다. 신뢰할 수 있는 프록시를 구성하는 것 이외에도 신뢰해야 하는 프록시 헤더를 설정 할 수 있습니다.

    <?php

    namespace App\Http\Middleware;

    use Illuminate\Http\Request;
    use Fideloper\Proxy\TrustProxies as Middleware;

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

> {tip} If you are using AWS Elastic Load Balancing, your `$headers` value should be `Request::HEADER_X_FORWARDED_AWS_ELB`. For more information on the constants that may be used in the `$headers` property, check out Symfony's documentation on [trusting proxies](https://symfony.com/doc/current/deployment/proxies.html).

> {팁} 만약 여러분이 AWS Elastic 로드 발란싱을 사용하고 있다면, 여러분의 `$headers` 값은 `Request::HEADER_X_FORWARDED_AWS_ELB`이 되어야 합니다. `$headers`에 사용될 수 있는 상수에 대한 다른 정보는, Symfony 문서의 [trusting proxies](https://symfony.com/doc/current/deployment/proxies.html)를 참고하십시오.

#### Trusting All Proxies
#### 모든 프록시 신뢰하기

If you are using Amazon AWS or another "cloud" load balancer provider, you may not know the IP addresses of your actual balancers. In this case, you may use `*` to trust all proxies:

아마존 AWS 또는 다른 "클라우드" 로드밸런서를 사용하는 경우에는, 실제 로드밸런서의 IP를 알 수가 없습니다. 이 경우, 모든 프록시를 신뢰할 수 있도록 하기 위해서 `*` 를 사용할 수 있습니다.

    /**
     * The trusted proxies for this application.
     *
     * @var array
     */
    protected $proxies = '*';
