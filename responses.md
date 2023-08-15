# HTTP Responses

- [HTTP Responses](#http-responses)
  - [Creating Responses](#creating-responses)
  - [응답 생성](#응답-생성)
      - [Strings \& Arrays](#strings--arrays)
      - [문자열 \& 배열](#문자열--배열)
      - [Response Objects](#response-objects)
      - [응답 객체](#응답-객체)
      - [Eloquent Models \& Collections](#eloquent-models--collections)
      - [Eloquent 모델 \& 컬렉션](#eloquent-모델--컬렉션)
    - [Attaching Headers To Responses](#attaching-headers-to-responses)
    - [응답에 헤더 추가](#응답에-헤더-추가)
      - [Cache Control Middleware](#cache-control-middleware)
      - [캐시 제어 미들웨어](#캐시-제어-미들웨어)
    - [Attaching Cookies To Responses](#attaching-cookies-to-responses)
    - [응답에 쿠키 추가](#응답에-쿠키-추가)
      - [Generating Cookie Instances](#generating-cookie-instances)
      - [쿠키 인스턴스 생성](#쿠키-인스턴스-생성)
      - [Expiring Cookies Early](#expiring-cookies-early)
      - [쿠키 조기 만료](#쿠키-조기-만료)
    - [Cookies \& Encryption](#cookies--encryption)
    - [쿠키 \& 암호화](#쿠키--암호화)
  - [Redirects](#redirects)
  - [리디렉션](#리디렉션)
    - [Redirecting To Named Routes](#redirecting-to-named-routes)
    - [이름있는 라우트로 리디렉션](#이름있는-라우트로-리디렉션)
      - [Populating Parameters Via Eloquent Models](#populating-parameters-via-eloquent-models)
      - [Eloquent 모델을 통한 파라미터 채우기](#eloquent-모델을-통한-파라미터-채우기)
    - [Redirecting To Controller Actions](#redirecting-to-controller-actions)
    - [컨트롤러 액션으로 리디렉션](#컨트롤러-액션으로-리디렉션)
    - [Redirecting To External Domains](#redirecting-to-external-domains)
    - [외부 도메인으로 리디렉션](#외부-도메인으로-리디렉션)
    - [Redirecting With Flashed Session Data](#redirecting-with-flashed-session-data)
    - [세션 데이터와 함께 리디렉션](#세션-데이터와-함께-리디렉션)
      - [Redirecting With Input](#redirecting-with-input)
      - [입력값과 함께 리디렉션](#입력값과-함께-리디렉션)
  - [Other Response Types](#other-response-types)
  - [다른 응답 형식](#다른-응답-형식)
    - [View Responses](#view-responses)
    - [뷰 응답](#뷰-응답)
    - [JSON Responses](#json-responses)
    - [JSON 응답](#json-응답)
    - [File Downloads](#file-downloads)
    - [파일 다운로드](#파일-다운로드)
      - [Streamed Downloads](#streamed-downloads)
      - [스트림 다운로드](#스트림-다운로드)
    - [File Responses](#file-responses)
    - [파일 응답](#파일-응답)
  - [Response Macros](#response-macros)
  - [응답 매크로](#응답-매크로)

<a name="creating-responses"></a>
## Creating Responses
## 응답 생성

<a name="strings-arrays"></a>
#### Strings & Arrays
#### 문자열 & 배열

All routes and controllers should return a response to be sent back to the user's browser. Laravel provides several different ways to return responses. The most basic response is returning a string from a route or controller. The framework will automatically convert the string into a full HTTP response:

모든 라우트와 컨트롤러는 사용자 브라우저에게 응답을 반환해야 합니다. Laravel은 여러 가지 방법으로 응답을 반환할 수 있습니다. 가장 기본적인 응답은 라우트나 컨트롤러에서 문자열을 반환하는 것입니다. 프레임워크는 자동으로 문자열을 전체 HTTP 응답으로 변환합니다:

    Route::get('/', function () {
        return 'Hello World';
    });

In addition to returning strings from your routes and controllers, you may also return arrays. The framework will automatically convert the array into a JSON response:

라우트와 컨트롤러에서 문자열을 반환하는 것 외에도 배열을 반환할 수도 있습니다. 프레임워크는 자동으로 배열을 JSON 응답으로 변환합니다:

    Route::get('/', function () {
        return [1, 2, 3];
    });

> **Note**  
> Did you know you can also return [Eloquent collections](/docs/{{version}}/eloquent-collections) from your routes or controllers? They will automatically be converted to JSON. Give it a shot!
>
> **참고**
> 라우트나 컨트롤러에서 [Eloquent 컬렉션](/docs/{{version}}/eloquent-collections)을 반환할 수도 있다는 것을 알고 계셨나요? 자동으로 JSON으로 변환됩니다. 한 번 해보세요!

<a name="response-objects"></a>
#### Response Objects
#### 응답 객체

Typically, you won't just be returning simple strings or arrays from your route actions. Instead, you will be returning full `Illuminate\Http\Response` instances or [views](/docs/{{version}}/views).

일반적으로, 라우트 액션에서는 단순한 문자열이나 배열을 반환하지 않습니다. 대신 `Illuminate\Http\Response` 인스턴스나 [뷰](/docs/{{version}}/views)를 반환합니다.

Returning a full `Response` instance allows you to customize the response's HTTP status code and headers. A `Response` instance inherits from the `Symfony\Component\HttpFoundation\Response` class, which provides a variety of methods for building HTTP responses:

전체 `Response` 인스턴스를 반환하면 응답의 HTTP 상태 코드와 헤더를 커스터마이징할 수 있습니다. `Response` 인스턴스는 `Symfony\Component\HttpFoundation\Response` 클래스를 상속받으며, HTTP 응답을 구축하는 다양한 메소드를 제공합니다:

    Route::get('/home', function () {
        return response('Hello World', 200)
                      ->header('Content-Type', 'text/plain');
    });

<a name="eloquent-models-and-collections"></a>
#### Eloquent Models & Collections
#### Eloquent 모델 & 컬렉션

You may also return [Eloquent ORM](/docs/{{version}}/eloquent) models and collections directly from your routes and controllers. When you do, Laravel will automatically convert the models and collections to JSON responses while respecting the model's [hidden attributes](/docs/{{version}}/eloquent-serialization#hiding-attributes-from-json):

또한 [Eloquent ORM](/docs/{{version}}/eloquent) 모델과 컬렉션을 라우트와 컨트롤러에서 직접 반환할 수 있습니다. 이렇게 하면 Laravel은 모델의 [숨겨진 속성](/docs/{{version}}/eloquent-serialization#hiding-attributes-from-json)을 고려하면서 모델과 컬렉션을 자동으로 JSON 응답으로 변환합니다:

    use App\Models\User;

    Route::get('/user/{user}', function (User $user) {
        return $user;
    });

<a name="attaching-headers-to-responses"></a>
### Attaching Headers To Responses
### 응답에 헤더 추가

Keep in mind that most response methods are chainable, allowing for the fluent construction of response instances. For example, you may use the `header` method to add a series of headers to the response before sending it back to the user:

응답 메소드 대부분은 체이닝이 가능하므로 응답 인스턴스를 플루언트하게 구성할 수 있습니다. 예를 들어, 사용자에게 응답을 보내기 전에 `header` 메소드를 사용하여 응답에 헤더를 추가할 수 있습니다:

    return response($content)
                ->header('Content-Type', $type)
                ->header('X-Header-One', 'Header Value')
                ->header('X-Header-Two', 'Header Value');

Or, you may use the `withHeaders` method to specify an array of headers to be added to the response:

또는, `withHeaders` 메소드를 사용하여 응답에 추가할 헤더 배열을 지정할 수 있습니다:

    return response($content)
                ->withHeaders([
                    'Content-Type' => $type,
                    'X-Header-One' => 'Header Value',
                    'X-Header-Two' => 'Header Value',
                ]);

<a name="cache-control-middleware"></a>
#### Cache Control Middleware
#### 캐시 제어 미들웨어

Laravel includes a `cache.headers` middleware, which may be used to quickly set the `Cache-Control` header for a group of routes. Directives should be provided using the "snake case" equivalent of the corresponding cache-control directive and should be separated by a semicolon. If `etag` is specified in the list of directives, an MD5 hash of the response content will automatically be set as the ETag identifier:

Laravel에는 `cache.headers` 미들웨어가 포함되어 있어, 라우트 그룹에 대한 `Cache-Control` 헤더를 빠르게 설정할 수 있습니다. 지시어는 캐시 제어 지시어에 해당하는 "스네이크 케이스"로 변환한 것을 제공해야 하며 세미콜론으로 구분해야 합니다. 지시어 목록에 `etag`가 지정되면 응답 내용의 MD5 해시가 자동으로 ETag 식별자로 설정됩니다:

    Route::middleware('cache.headers:public;max_age=2628000;etag')->group(function () {
        Route::get('/privacy', function () {
            // ...
        });

        Route::get('/terms', function () {
            // ...
        });
    });

<a name="attaching-cookies-to-responses"></a>
### Attaching Cookies To Responses
### 응답에 쿠키 추가

You may attach a cookie to an outgoing `Illuminate\Http\Response` instance using the `cookie` method. You should pass the name, value, and the number of minutes the cookie should be considered valid to this method:

`cookie` 메소드를 사용하여 나가는 `Illuminate\Http\Response` 인스턴스에 쿠키를 추가할 수 있습니다. 이 메소드에는 쿠키의 이름, 값, 유효한 시간(분)을 전달해야 합니다:

    return response('Hello World')->cookie(
        'name', 'value', $minutes
    );

The `cookie` method also accepts a few more arguments which are used less frequently. Generally, these arguments have the same purpose and meaning as the arguments that would be given to PHP's native [setcookie](https://secure.php.net/manual/en/function.setcookie.php) method:

`cookie` 메소드는 더 많은 인수를 받지만, 덜 자주 사용됩니다. 일반적으로 이 인수들은 PHP의 네이티브 [setcookie](https://secure.php.net/manual/en/function.setcookie.php) 메소드에 전달되는 인수와 같은 목적과 의미를 가집니다:

    return response('Hello World')->cookie(
        'name', 'value', $minutes, $path, $domain, $secure, $httpOnly
    );

If you would like to ensure that a cookie is sent with the outgoing response but you do not yet have an instance of that response, you can use the `Cookie` facade to "queue" cookies for attachment to the response when it is sent. The `queue` method accepts the arguments needed to create a cookie instance. These cookies will be attached to the outgoing response before it is sent to the browser:

만약 나가는 응답과 함께 쿠키가 전송되도록 하려면, 아직 응답 인스턴스가 없다면 `Cookie` facade를 사용하여 쿠키를 "큐"에 추가할 수 있습니다. `queue` 메소드는 쿠키 인스턴스를 생성하는 데 필요한 인수를 받습니다. 이 쿠키들은 브라우저에 전송되기 전에 나가는 응답에 추가됩니다:

    use Illuminate\Support\Facades\Cookie;

    Cookie::queue('name', 'value', $minutes);

<a name="generating-cookie-instances"></a>
#### Generating Cookie Instances
#### 쿠키 인스턴스 생성

If you would like to generate a `Symfony\Component\HttpFoundation\Cookie` instance that can be attached to a response instance at a later time, you may use the global `cookie` helper. This cookie will not be sent back to the client unless it is attached to a response instance:

나중에 응답 인스턴스에 추가할 수 있는 `Symfony\Component\HttpFoundation\Cookie` 인스턴스를 생성하려면, 전역 `cookie` 헬퍼를 사용할 수 있습니다. 이 쿠키는 응답 인스턴스에 추가되지 않으면 클라이언트에 다시 전송되지 않습니다:

    $cookie = cookie('name', 'value', $minutes);

    return response('Hello World')->cookie($cookie);

<a name="expiring-cookies-early"></a>
#### Expiring Cookies Early
#### 쿠키 조기 만료

You may remove a cookie by expiring it via the `withoutCookie` method of an outgoing response:

나가는 응답의 `withoutCookie` 메소드를 사용하여 쿠키를 만료하여 제거할 수 있습니다:

    return response('Hello World')->withoutCookie('name');

If you do not yet have an instance of the outgoing response, you may use the `Cookie` facade's `expire` method to expire a cookie:

나가는 응답의 인스턴스가 없다면, `Cookie` facade의 `expire` 메소드를 사용하여 쿠키를 만료할 수 있습니다:

    Cookie::expire('name');

<a name="cookies-and-encryption"></a>
### Cookies & Encryption
### 쿠키 & 암호화

By default, all cookies generated by Laravel are encrypted and signed so that they can't be modified or read by the client. If you would like to disable encryption for a subset of cookies generated by your application, you may use the `$except` property of the `App\Http\Middleware\EncryptCookies` middleware, which is located in the `app/Http/Middleware` directory:

기본적으로, Laravel이 생성하는 모든 쿠키는 암호화되고 서명되어 클라이언트가 수정하거나 읽을 수 없습니다. 어플리케이션에서 생성된 쿠키의 하위 집합에 대해 암호화를 비활성화하려면, `app/Http/Middleware` 디렉토리에 있는 `App\Http\Middleware\EncryptCookies` 미들웨어의 `$except` 프로퍼티를 사용할 수 있습니다:

    /**
     * The names of the cookies that should not be encrypted.
     *
     * @var array
     */
    protected $except = [
        'cookie_name',
    ];

<a name="redirects"></a>
## Redirects
## 리디렉션

Redirect responses are instances of the `Illuminate\Http\RedirectResponse` class, and contain the proper headers needed to redirect the user to another URL. There are several ways to generate a `RedirectResponse` instance. The simplest method is to use the global `redirect` helper:

리디렉션 응답은 `Illuminate\Http\RedirectResponse` 클래스의 인스턴스이며, 사용자를 다른 URL로 리디렉션하기 위해 필요한 헤더를 포함합니다. `RedirectResponse` 인스턴스를 생성하는 몇 가지 방법이 있습니다. 가장 간단한 방법은 전역 `redirect` 헬퍼를 사용하는 것입니다:

    Route::get('/dashboard', function () {
        return redirect('home/dashboard');
    });

Sometimes you may wish to redirect the user to their previous location, such as when a submitted form is invalid. You may do so by using the global `back` helper function. Since this feature utilizes the [session](/docs/{{version}}/session), make sure the route calling the `back` function is using the `web` middleware group:

때때로 사용자가 이전 위치로 리디렉션하길 원할 수 있습니다. 예를 들어, 제출된 폼이 유효하지 않을 때입니다. 전역 `back` 헬퍼 함수를 사용하여 이를 수행할 수 있습니다. 이 기능은 [세션](/docs/{{version}}/session)을 사용하므로, `back` 함수를 호출하는 라우트가 `web` 미들웨어 그룹을 사용하고 있는지 확인하세요:

    Route::post('/user/profile', function () {
        // Validate the request...

        return back()->withInput();
    });

<a name="redirecting-named-routes"></a>
### Redirecting To Named Routes
### 이름있는 라우트로 리디렉션

When you call the `redirect` helper with no parameters, an instance of `Illuminate\Routing\Redirector` is returned, allowing you to call any method on the `Redirector` instance. For example, to generate a `RedirectResponse` to a named route, you may use the `route` method:

`redirect` 헬퍼를 파라미터 없이 호출하면, `Illuminate\Routing\Redirector` 인스턴스가 반환되며, `Redirector` 인스턴스의 모든 메소드를 호출할 수 있습니다. 예를 들어, 이름있는 라우트로 `RedirectResponse`를 생성하려면, `route` 메소드를 사용할 수 있습니다:

    return redirect()->route('login');

If your route has parameters, you may pass them as the second argument to the `route` method:

라우트에 파라미터가 있다면, `route` 메소드의 두 번째 인자로 전달할 수 있습니다:

    // For a route with the following URI: /profile/{id}

    return redirect()->route('profile', ['id' => 1]);

<a name="populating-parameters-via-eloquent-models"></a>
#### Populating Parameters Via Eloquent Models
#### Eloquent 모델을 통한 파라미터 채우기

If you are redirecting to a route with an "ID" parameter that is being populated from an Eloquent model, you may pass the model itself. The ID will be extracted automatically:

Eloquent 모델에서 채워지는 "ID" 파라미터를 가진 라우트로 리디렉션하는 경우, 모델 자체를 전달할 수 있습니다. ID는 자동으로 추출됩니다:

    // For a route with the following URI: /profile/{id}

    return redirect()->route('profile', [$user]);

If you would like to customize the value that is placed in the route parameter, you can specify the column in the route parameter definition (`/profile/{id:slug}`) or you can override the `getRouteKey` method on your Eloquent model:

라우트 파라미터에 삽입될 값을 사용자 정의하려면, 라우트 파라미터 정의(`/profile/{id:slug}`)에서 컬럼을 지정하거나 Eloquent 모델의 `getRouteKey` 메소드를 오버라이드할 수 있습니다:

    /**
     * Get the value of the model's route key.
     */
    public function getRouteKey(): mixed
    {
        return $this->slug;
    }

<a name="redirecting-controller-actions"></a>
### Redirecting To Controller Actions
### 컨트롤러 액션으로 리디렉션

You may also generate redirects to [controller actions](/docs/{{version}}/controllers). To do so, pass the controller and action name to the `action` method:

또한 [컨트롤러 액션](/docs/{{version}}/controllers)으로 리디렉션을 생성할 수 있습니다. 이를 위해 컨트롤러와 액션 이름을 `action` 메소드에 전달하세요:

    use App\Http\Controllers\UserController;

    return redirect()->action([UserController::class, 'index']);

If your controller route requires parameters, you may pass them as the second argument to the `action` method:

컨트롤러 라우트에 파라미터가 필요한 경우, `action` 메소드의 두 번째 인자로 전달할 수 있습니다:

    return redirect()->action(
        [UserController::class, 'profile'], ['id' => 1]
    );

<a name="redirecting-external-domains"></a>
### Redirecting To External Domains
### 외부 도메인으로 리디렉션

Sometimes you may need to redirect to a domain outside of your application. You may do so by calling the `away` method, which creates a `RedirectResponse` without any additional URL encoding, validation, or verification:

때때로 어플리케이션 외부 도메인으로 리디렉션해야 할 수 있습니다. 이를 위해 추가적인 URL 인코딩, 유효성 검사, 검증 없이 `RedirectResponse`를 생성하는 `away` 메소드를 호출할 수 있습니다:

    return redirect()->away('https://www.google.com');

<a name="redirecting-with-flashed-session-data"></a>
### Redirecting With Flashed Session Data
### 세션 데이터와 함께 리디렉션

Redirecting to a new URL and [flashing data to the session](/docs/{{version}}/session#flash-data) are usually done at the same time. Typically, this is done after successfully performing an action when you flash a success message to the session. For convenience, you may create a `RedirectResponse` instance and flash data to the session in a single, fluent method chain:

새로운 URL로 리디렉션하고 [세션에 데이터를 플래시](/docs/{{version}}/session#flash-data)하는 것은 일반적으로 동시에 수행됩니다. 일반적으로 성공 메시지를 세션에 플래시한 후에 액션을 성공적으로 수행한 후에 이를 수행합니다. 편의를 위해, `RedirectResponse` 인스턴스를 생성하고 세션에 데이터를 플래시하는 단일한 플루언트 메소드 체인을 생성할 수 있습니다:

    Route::post('/user/profile', function () {
        // ...

        return redirect('dashboard')->with('status', 'Profile updated!');
    });

After the user is redirected, you may display the flashed message from the [session](/docs/{{version}}/session). For example, using [Blade syntax](/docs/{{version}}/blade):

사용자가 리디렉션된 후, [세션](/docs/{{version}}/session)에서 플래시된 메시지를 표시할 수 있습니다. 예를 들어, [Blade 문법](/docs/{{version}}/blade)을 사용하는 경우:

    @if (session('status'))
        <div class="alert alert-success">
            {{ session('status') }}
        </div>
    @endif

<a name="redirecting-with-input"></a>
#### Redirecting With Input
#### 입력값과 함께 리디렉션

You may use the `withInput` method provided by the `RedirectResponse` instance to flash the current request's input data to the session before redirecting the user to a new location. This is typically done if the user has encountered a validation error. Once the input has been flashed to the session, you may easily [retrieve it](/docs/{{version}}/requests#retrieving-old-input) during the next request to repopulate the form:

`RedirectResponse` 인스턴스가 제공하는 `withInput` 메소드를 사용하여 사용자를 새 위치로 리디렉션하기 전에 현재 요청의 입력 데이터를 세션에 플래시할 수 있습니다. 이는 일반적으로 사용자가 유효성 검사 오류를 만난 경우에 수행됩니다. 입력이 세션에 플래시된 후, 다음 요청 동안 폼을 다시 채우기 위해 [검색할 수 있습니다](/docs/{{version}}/requests#retrieving-old-input):

    return back()->withInput();

<a name="other-response-types"></a>
## Other Response Types
## 다른 응답 형식

The `response` helper may be used to generate other types of response instances. When the `response` helper is called without arguments, an implementation of the `Illuminate\Contracts\Routing\ResponseFactory` [contract](/docs/{{version}}/contracts) is returned. This contract provides several helpful methods for generating responses.

`response` 헬퍼는 다른 유형의 응답 인스턴스를 생성하는 데 사용될 수 있습니다. `response` 헬퍼가 인수 없이 호출되면, `Illuminate\Contracts\Routing\ResponseFactory` [계약](/docs/{{version}}/contracts)의 구현체가 반환됩니다. 이 계약은 응답을 생성하는 데 도움이 되는 몇 가지 유용한 메소드를 제공합니다.

<a name="view-responses"></a>
### View Responses
### 뷰 응답

If you need control over the response's status and headers but also need to return a [view](/docs/{{version}}/views) as the response's content, you should use the `view` method:

응답의 상태와 헤더를 제어해야 하지만 응답의 내용으로 [뷰](/docs/{{version}}/views)를 반환해야 하는 경우 `view` 메소드를 사용해야 합니다:

    return response()
                ->view('hello', $data, 200)
                ->header('Content-Type', $type);

Of course, if you do not need to pass a custom HTTP status code or custom headers, you may use the global `view` helper function.

물론, 커스텀 HTTP 상태 코드 또는 커스텀 헤더를 전달할 필요가 없는 경우 전역 `view` 헬퍼 함수를 사용할 수 있습니다.

<a name="json-responses"></a>
### JSON Responses
### JSON 응답

The `json` method will automatically set the `Content-Type` header to `application/json`, as well as convert the given array to JSON using the `json_encode` PHP function:

`json` 메소드는 `Content-Type` 헤더를 `application/json`으로 자동으로 설정하고, `json_encode` PHP 함수를 사용하여 주어진 배열을 JSON으로 변환합니다:

    return response()->json([
        'name' => 'Abigail',
        'state' => 'CA',
    ]);

If you would like to create a JSONP response, you may use the `json` method in combination with the `withCallback` method:

JSONP 응답을 생성하려면, `json` 메소드를 `withCallback` 메소드와 결합하여 사용할 수 있습니다:

    return response()
                ->json(['name' => 'Abigail', 'state' => 'CA'])
                ->withCallback($request->input('callback'));

<a name="file-downloads"></a>
### File Downloads
### 파일 다운로드

The `download` method may be used to generate a response that forces the user's browser to download the file at the given path. The `download` method accepts a filename as the second argument to the method, which will determine the filename that is seen by the user downloading the file. Finally, you may pass an array of HTTP headers as the third argument to the method:

`download` 메소드는 사용자의 브라우저가 주어진 경로의 파일을 다운로드하도록 하는 응답을 생성하는 데 사용됩니다. `download` 메소드는 두 번째 인수로 파일명을 받으며, 이는 파일을 다운로드하는 사용자가 볼 파일명을 결정합니다. 마지막으로, 세 번째 인수로 HTTP 헤더의 배열을 전달할 수 있습니다:

    return response()->download($pathToFile);

    return response()->download($pathToFile, $name, $headers);

> **Warning**  
> Symfony HttpFoundation, which manages file downloads, requires the file being downloaded to have an ASCII filename.
>
> **경고**
> 파일 다운로드를 관리하는 Symfony HttpFoundation는 다운로드되는 파일의 파일명이 ASCII여야 합니다.

<a name="streamed-downloads"></a>
#### Streamed Downloads
#### 스트림 다운로드

Sometimes you may wish to turn the string response of a given operation into a downloadable response without having to write the contents of the operation to disk. You may use the `streamDownload` method in this scenario. This method accepts a callback, filename, and an optional array of headers as its arguments:

때때로 주어진 작업의 문자열 응답을 디스크에 작업 내용을 작성하지 않고 다운로드 가능한 응답으로 변환하고자 할 수 있습니다. 이러한 경우 `streamDownload` 메소드를 사용할 수 있습니다. 이 메소드는 콜백, 파일명, 선택적 헤더 배열을 인수로 받습니다:

    use App\Services\GitHub;

    return response()->streamDownload(function () {
        echo GitHub::api('repo')
                    ->contents()
                    ->readme('laravel', 'laravel')['contents'];
    }, 'laravel-readme.md');

<a name="file-responses"></a>
### File Responses
### 파일 응답

The `file` method may be used to display a file, such as an image or PDF, directly in the user's browser instead of initiating a download. This method accepts the path to the file as its first argument and an array of headers as its second argument:

`file` 메소드는 이미지 또는 PDF와 같은 파일을 사용자의 브라우저에서 직접 표시하도록 다운로드를 시작하는 대신 사용할 수 있습니다. 이 메소드는 첫 번째 인수로 파일 경로를 받으며, 두 번째 인수로 헤더 배열을 받습니다:

    return response()->file($pathToFile);

    return response()->file($pathToFile, $headers);

<a name="response-macros"></a>
## Response Macros
## 응답 매크로

If you would like to define a custom response that you can re-use in a variety of your routes and controllers, you may use the `macro` method on the `Response` facade. Typically, you should call this method from the `boot` method of one of your application's [service providers](/docs/{{version}}/providers), such as the `App\Providers\AppServiceProvider` service provider:

여러 라우트 및 컨트롤러에서 재사용할 수 있는 커스텀 응답을 정의하려면, `Response` facade의 `macro` 메소드를 사용할 수 있습니다. 일반적으로, 이 메소드는 `App\Providers\AppServiceProvider` 서비스 프로바이더와 같은 응용 프로그램의 [서비스 프로바이더](/docs/{{version}}/providers)의 `boot` 메소드에서 호출해야 합니다:

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\Response;
    use Illuminate\Support\ServiceProvider;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Bootstrap any application services.
         */
        public function boot(): void
        {
            Response::macro('caps', function (string $value) {
                return Response::make(strtoupper($value));
            });
        }
    }

The `macro` function accepts a name as its first argument and a closure as its second argument. The macro's closure will be executed when calling the macro name from a `ResponseFactory` implementation or the `response` helper:

`macro` 함수는 첫 번째 인수로 이름을, 두 번째 인수로 클로저를 받습니다. 매크로의 클로저는 `ResponseFactory` 구현체 또는 `response` 헬퍼에서 매크로 이름을 호출할 때 실행됩니다:

    return response()->caps('foo');
