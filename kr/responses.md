# HTTP Responses
# HTTP Responses

- [Creating Responses](#creating-responses)
- [Responses 생성하기](#creating-responses)
    - [Attaching Headers To Responses](#attaching-headers-to-responses)
    - [Responses에 헤더 추가하기](#attaching-headers-to-responses)
    - [Attaching Cookies To Responses](#attaching-cookies-to-responses)
    - [Responses에 쿠키 추가하기](#attaching-cookies-to-responses)
    - [Cookies & Encryption](#cookies-and-encryption)
    - [쿠키 & 암호화](#cookies-and-encryption)
- [Redirects](#redirects)
- [리다이렉트](#redirects)
    - [Redirecting To Named Routes](#redirecting-named-routes)
    - [이름이 지정된 라우트로 리다이렉트 하기](#redirecting-named-routes)
    - [Redirecting To Controller Actions](#redirecting-controller-actions)
    - [컨트롤러 액션으로 리다이렉트 하기](#redirecting-controller-actions)
    - [Redirecting To External Domains](#redirecting-external-domains)
    - [외부 도메인으로 리다이렉트 하기](#redirecting-external-domains)
    - [Redirecting With Flashed Session Data](#redirecting-with-flashed-session-data)
    - [세션의 임시 데이터와 함께 리다이렉트 하기](#redirecting-with-flashed-session-data)
- [Other Response Types](#other-response-types)
- [기타 Response 타입들](#other-response-types)
    - [View Responses](#view-responses)
    - [View Responses](#view-responses)
    - [JSON Responses](#json-responses)
    - [JSON Responses](#json-responses)
    - [File Downloads](#file-downloads)
    - [File Downloads](#file-downloads)
    - [File Responses](#file-responses)
    - [File Responses](#file-responses)
- [Response Macros](#response-macros)
- [Response 매크로](#response-macros)

<a name="creating-responses"></a>
## Creating Responses
## Response 생성하기

<a name="strings-arrays"></a>
#### Strings & Arrays
#### 문자열 & 배열

All routes and controllers should return a response to be sent back to the user's browser. Laravel provides several different ways to return responses. The most basic response is returning a string from a route or controller. The framework will automatically convert the string into a full HTTP response:

모든 라우트와 컨트롤러는 response 를 사용자 브라우저로 다시 반환해야 합니다. 라라벨은 response 를 반환하기 위한 몇가지 다른 방법을 제공합니다. 가장 기본적인 response 는 라우트나 컨트롤러에서 문자열을 반환하는 것입니다. 프레임워크는 자동으로 문자열을 전체 HTTP response로 변환할 것입니다.

    Route::get('/', function () {
        return 'Hello World';
    });

In addition to returning strings from your routes and controllers, you may also return arrays. The framework will automatically convert the array into a JSON response:

라우트나 컨트롤러에서 문자열을 반환하는것에 더하여, 배열을 반환할 수도 있습니다. 프레임워크는 자동으로 배열을 JSON response로 변환할 것입니다.

    Route::get('/', function () {
        return [1, 2, 3];
    });

> {tip} Did you know you can also return [Eloquent collections](/docs/{{version}}/eloquent-collections) from your routes or controllers? They will automatically be converted to JSON. Give it a shot!

> {tip} 라우트나 컨트롤러에서 [Eloquent 컬렉션](/docs/{{version}}/eloquent-collections) 또한 반환할 수 있다는 것을 알고 있습니까? 이는 자동적으로 JSON으로 변환됩니다. 한번 해보세요!

<a name="response-objects"></a>
#### Response Objects
#### Response 객체

Typically, you won't just be returning simple strings or arrays from your route actions. Instead, you will be returning full `Illuminate\Http\Response` instances or [views](/docs/{{version}}/views).

일반적으로, 라우트 액션에서 단순히 문자열이나 배열만 반환하지는 않습니다. 대신에 `Illuminate\Http\Response` 전체 인스턴스 또는 [views](/docs/{{version}}/views)을 반환할 수도 있습니다.

Returning a full `Response` instance allows you to customize the response's HTTP status code and headers. A `Response` instance inherits from the `Symfony\Component\HttpFoundation\Response` class, which provides a variety of methods for building HTTP responses:

`Response` 인스턴스를 반환하는 것은 여러분이 response 의 HTTP 상태 코드나 헤더를 변경할 수 있도록 지원합니다. `Symfony\Component\HttpFoundation\Response` 클래스를 구현하고 있는 `Response` 인스턴스는 HTTP response 를 만들기 위한 다양한 메소드를 제공합니다.

    Route::get('/home', function () {
        return response('Hello World', 200)
                      ->header('Content-Type', 'text/plain');
    });

<a name="eloquent-models-and-collections"></a>
#### Eloquent Models & Collections
#### Eloquent 모델 및 컬렉션

You may also return [Eloquent ORM](/docs/{{version}}/eloquent) models and collections directly from your routes and controllers. When you do, Laravel will automatically convert the models and collections to JSON responses while respecting the model's [hidden attributes](/docs/{{version}}/eloquent-serialization#hiding-attributes-from-json):

경로 및 컨트롤러에서 직접 [Eloquent ORM](/docs/{{version}}/eloquent) 모델 및 컬렉션을 반환할 수도 있습니다. 그렇게 하면 라라벨은 모델의 [hidden attributes](/docs/{{version}}/eloquent-serialization#hiding-attributes-from-json)를 준수하면서 모델과 컬렉션을 JSON 응답으로 자동 변환합니다.

    use App\Models\User;

    Route::get('/user/{user}', function (User $user) {
        return $user;
    });

<a name="attaching-headers-to-responses"></a>
### Attaching Headers To Responses
### Response에 헤더 추가하기

Keep in mind that most response methods are chainable, allowing for the fluent construction of response instances. For example, you may use the `header` method to add a series of headers to the response before sending it back to the user:

대부분의 response 메소드는 response 인스턴스의 유연한 생성을 위해서 체이닝이 가능합니다. 예를 들어서 사용자에게 response를 되돌려 주기 전에 `header` 메소드를 통해서 header를 추가할 수 있습니다.

    return response($content)
                ->header('Content-Type', $type)
                ->header('X-Header-One', 'Header Value')
                ->header('X-Header-Two', 'Header Value');

Or, you may use the `withHeaders` method to specify an array of headers to be added to the response:

또는, response 에 추가하고자 하는 헤더의 배열을 지정하기 위해서 `withHeaders` 메소드를 사용할 수 있습니다.

    return response($content)
                ->withHeaders([
                    'Content-Type' => $type,
                    'X-Header-One' => 'Header Value',
                    'X-Header-Two' => 'Header Value',
                ]);

<a name="cache-control-middleware"></a>
#### Cache Control Middleware
#### Cache Control 미들웨어

Laravel includes a `cache.headers` middleware, which may be used to quickly set the `Cache-Control` header for a group of routes. Directives should be provided using the "snake case" equivalent of the corresponding cache-control directive and should be separated by a semicolon. If `etag` is specified in the list of directives, an MD5 hash of the response content will automatically be set as the ETag identifier:

라라벨에는 라우트 그룹에 대한 `Cache-Control` 헤더를 빠르게 설정하는 데 사용할 수 있는 `cache.headers` 미들웨어가 포함되어 있습니다. 지시문은 해당 캐시 제어 지시문에 해당하는 "snake case"를 사용하여 제공해야 하며 세미콜론으로 구분해야 합니다. 지시문 목록에 `etag`가 지정되면 응답 내용의 MD5 해시가 자동으로 ETag 식별자로 설정됩니다.

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
### Responses에 Cookie 추가하기

You may attach a cookie to an outgoing `Illuminate\Http\Response` instance using the `cookie` method. You should pass the name, value, and the number of minutes the cookie should be considered valid to this method:

`cookie` 메소드를 사용하여 나가는 `Illuminate\Http\Response` 인스턴스에 쿠키를 첨부할 수 있습니다. 이름, 값 및 쿠키가 이 메서드에 유효한 것으로 간주되어야 하는 시간(분)을 전달해야 합니다.

    return response('Hello World')->cookie(
        'name', 'value', $minutes
    );

The `cookie` method also accepts a few more arguments which are used less frequently. Generally, these arguments have the same purpose and meaning as the arguments that would be given to PHP's native [setcookie](https://secure.php.net/manual/en/function.setcookie.php) method:

`cookie` 메소드는 또한 자주 사용되지 않는 몇가지 인자를 더 받아들입니다. 일반적으로 이 인자들은 PHP의 내장된 [setcookie](https://secure.php.net/manual/en/function.setcookie.php) 메소드에 제공되는 인자들과 동일한 목적과 의미가 있습니다.

    return response('Hello World')->cookie(
        'name', 'value', $minutes, $path, $domain, $secure, $httpOnly
    );

If you would like to ensure that a cookie is sent with the outgoing response but you do not yet have an instance of that response, you can use the `Cookie` facade to "queue" cookies for attachment to the response when it is sent. The `queue` method accepts the arguments needed to create a cookie instance. These cookies will be attached to the outgoing response before it is sent to the browser:

나가는 응답과 함께 쿠키가 전송되도록 하고 싶지만, 해당 응답의 인스턴스가 아직 없는 경우 `Cookie` 파사드를 사용하여 응답이 전송될 때 응답에 첨부할 쿠키를 "대기열"에 넣을 수 있습니다. `queue` 메소드는 쿠키 인스턴스를 생성하는 데 필요한 인수를 입력받습니다. 이러한 쿠키는 브라우저로 보내기 전에 나가는 응답에 첨부됩니다.

    use Illuminate\Support\Facades\Cookie;

    Cookie::queue('name', 'value', $minutes);

<a name="generating-cookie-instances"></a>
#### Generating Cookie Instances
#### 쿠키 인스턴스 생성

If you would like to generate a `Symfony\Component\HttpFoundation\Cookie` instance that can be attached to a response instance at a later time, you may use the global `cookie` helper. This cookie will not be sent back to the client unless it is attached to a response instance:

나중에 응답 인스턴스에 첨부할 수 있는 `Symfony\Component\HttpFoundation\Cookie` 인스턴스를 생성하려면, 전역 `cookie` 헬퍼를 사용할 수 있습니다. 이 쿠키는 응답 인스턴스에 연결되지 않는 한 클라이언트로 다시 전송되지 않습니다.

    $cookie = cookie('name', 'value', $minutes);

    return response('Hello World')->cookie($cookie);

<a name="expiring-cookies-early"></a>
#### Expiring Cookies Early
#### 쿠키 조기 만료

You may remove a cookie by expiring it via the `withoutCookie` method of an outgoing response:

나가는 응답의 `withoutCookie` 메서드를 통해 쿠키를 만료시켜 쿠키를 제거할 수 있습니다.

    return response('Hello World')->withoutCookie('name');

If you do not yet have an instance of the outgoing response, you may use the `Cookie` facade's `expire` method to expire a cookie:

아직 나가는 응답의 인스턴스가 없다면 `Cookie` 파사드의 `expire` 메소드를 사용하여 쿠키를 만료시킬 수 있습니다.

    Cookie::expire('name');

<a name="cookies-and-encryption"></a>
### Cookies & Encryption
### 쿠키 & 암호화

By default, all cookies generated by Laravel are encrypted and signed so that they can't be modified or read by the client. If you would like to disable encryption for a subset of cookies generated by your application, you may use the `$except` property of the `App\Http\Middleware\EncryptCookies` middleware, which is located in the `app/Http/Middleware` directory:

기본적으로, 라라벨에서 생성되는 모든 쿠키는 암호화 되고, 서명이 적용되어 클리이언트에서는 수정하거나 확인할 수 없습니다. 애플리케이션에서 의해서 생성되는 쿠키의 일부분에서 암호화를 비활성화 하고자 한다면, `app/Http/Middleware` 디렉토리 안에 들어 있는 `App\Http\Middleware\EncryptCookies` 미들웨어의 `$except` 속성을 사용할 수 있습니다.

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
## 리다이렉트

Redirect responses are instances of the `Illuminate\Http\RedirectResponse` class, and contain the proper headers needed to redirect the user to another URL. There are several ways to generate a `RedirectResponse` instance. The simplest method is to use the global `redirect` helper:

리다이렉트 response는 `Illuminate\Http\RedirectResponse` 클래스의 인스턴스이며 사용자를 다른 URL로 리다이렉트 시키기 위한 준비된 헤더를 포함하고 있습니다. `RedirectResponse` 인스턴스를 생성하기 위해서는 몇 가지 방법이 있습니다. 가장 간단한 방법은 글로벌 `redirect` 헬퍼를 사용하는 것입니다.

    Route::get('/dashboard', function () {
        return redirect('home/dashboard');
    });

Sometimes you may wish to redirect the user to their previous location, such as when a submitted form is invalid. You may do so by using the global `back` helper function. Since this feature utilizes the [session](/docs/{{version}}/session), make sure the route calling the `back` function is using the `web` middleware group:

제출된 양식-form이 유효하지 않은 경우와 같이, 사용자를 이전 위치로 리디렉션하려는 경우가 있습니다. 전역 `back` 헬퍼 기능을 사용하여 그렇게 할 수 있습니다. 이 기능은 [세션](/docs/{{version}}/session)을 활용하므로 `back` 함수를 호출하는 경로가 `web` 미들웨어 그룹을 사용하는지 확인하세요.

    Route::post('/user/profile', function () {
        // Validate the request...

        return back()->withInput();
    });

<a name="redirecting-named-routes"></a>
### Redirecting To Named Routes
### 이름이 지정된 라우트로 리다이렉트 하기

When you call the `redirect` helper with no parameters, an instance of `Illuminate\Routing\Redirector` is returned, allowing you to call any method on the `Redirector` instance. For example, to generate a `RedirectResponse` to a named route, you may use the `route` method:

`redirect` 헬퍼가 아무런 인자 없이 호출될 때에는, `Illuminate\Routing\Redirector` 인스턴스가 반환되어, `Redirector` 인스턴스의 메소드를 사용할 수 있습니다. 다음과 같이 이름이 지정된 라우트에 대한 `RedirectResponse` 를 생성하려면 `route` 메소드를 사용할 수 있습니다.

    return redirect()->route('login');

If your route has parameters, you may pass them as the second argument to the `route` method:

라우트가 인자를 받아야 한다면, `route` 메소드의 두번째 인자로 이를 전달할 수 있습니다.

    // For a route with the following URI: /profile/{id}

    return redirect()->route('profile', ['id' => 1]);

<a name="populating-parameters-via-eloquent-models"></a>
#### Populating Parameters Via Eloquent Models
#### Eloquent 모델을 통한 파라미터 채우기

If you are redirecting to a route with an "ID" parameter that is being populated from an Eloquent model, you may pass the model itself. The ID will be extracted automatically:

Eloquent 모델에 의해서 채워지는 "ID" 파라미터를 가진 라우트로 리다이렉트 하는 경우, 모델 그 자체를 전달할 수 있습니다. ID 는 자동으로 추출됩니다.

    // For a route with the following URI: /profile/{id}

    return redirect()->route('profile', [$user]);

If you would like to customize the value that is placed in the route parameter, you can specify the column in the route parameter definition (`/profile/{id:slug}`) or you can override the `getRouteKey` method on your Eloquent model:

라우트 파라메터에 지정된 값을 커스터마이징하려면 라우트 파라메터 정의 (`/profile/{id:slug}`)에서 컬럼을 지정하거나 Eloquent 모델에서 `getRouteKey` 메소드를 통해 재정의 할 수 있습니다.

    /**
     * Get the value of the model's route key.
     *
     * @return mixed
     */
    public function getRouteKey()
    {
        return $this->slug;
    }

<a name="redirecting-controller-actions"></a>
### Redirecting To Controller Actions
### 컨트롤러 액션으로 리다이렉트 하기

You may also generate redirects to [controller actions](/docs/{{version}}/controllers). To do so, pass the controller and action name to the `action` method:

또한 [컨트롤러 액션](/docs/{{version}}/controllers)으로 리다이렉트하는 응답을 생성할 수도 있습니다. 이렇게 하기 위해서는, 컨트롤러와 액션의 이름을 `action` 메소드에 전달하면 됩니다.

    use App\Http\Controllers\UserController;

    return redirect()->action([UserController::class, 'index']);

If your controller route requires parameters, you may pass them as the second argument to the `action` method:

컨트롤러 라우트에 파라미터가 필요한 경우, `action` 메소드의 두 번째 인자로 전달하면 됩니다.

    return redirect()->action(
        [UserController::class, 'profile'], ['id' => 1]
    );

<a name="redirecting-external-domains"></a>
### Redirecting To External Domains
### 외부 도메인으로 리다이렉트 하기

Sometimes you may need to redirect to a domain outside of your application. You may do so by calling the `away` method, which creates a `RedirectResponse` without any additional URL encoding, validation, or verification:

때로는 애플리케이션의 외부 도메인으로 리다이렉트 해야 하기도 합니다. 추가적인 URL 인코딩, 유효성 검사와 확인 과정 없이 `RedirectResponse`을 만드는 `away` 메소드를 호출해서 이렇게 할 수 있습니다.

    return redirect()->away('https://www.google.com');

<a name="redirecting-with-flashed-session-data"></a>
### Redirecting With Flashed Session Data
### 세션의 임시 데이터와 함께 리다이렉트 하기

Redirecting to a new URL and [flashing data to the session](/docs/{{version}}/session#flash-data) are usually done at the same time. Typically, this is done after successfully performing an action when you flash a success message to the session. For convenience, you may create a `RedirectResponse` instance and flash data to the session in a single, fluent method chain:

새로운 URL로 리다이렉팅 되는 것과 [세션에 데이터를 임시 저장하는것](/docs/{{version}}/session#flash-data)은 일반적으로 동시에 완료됩니다. 일반적으로, 이 작업은 여러분이 성공 메세지를 세션에 임시 저장할 때 작업을 성공적으로 액션을 수행한 다음에 완료됩니다. 보다 간편하게, 한번에 `RedirectResponse` 인스턴스를 생성하고 데이터를 세션에 임시저장하는 유연한 메소드 체이닝을 할 수 있습니다.

    Route::post('/user/profile', function () {
        // ...

        return redirect('dashboard')->with('status', 'Profile updated!');
    });

After the user is redirected, you may display the flashed message from the [session](/docs/{{version}}/session). For example, using [Blade syntax](/docs/{{version}}/blade):

사용자가 리다이렉션 된 이후에, [세션](/docs/{{version}}/session)에서 임시 저장된 데이터를 표시할 수 있습니다. 예를 들어 [블레이드 문법](/docs/{{version}}/blade)을 사용해 보겠습니다.

    @if (session('status'))
        <div class="alert alert-success">
            {{ session('status') }}
        </div>
    @endif

<a name="redirecting-with-input"></a>
#### Redirecting With Input
#### 입력값과 함께 리디렉션

You may use the `withInput` method provided by the `RedirectResponse` instance to flash the current request's input data to the session before redirecting the user to a new location. This is typically done if the user has encountered a validation error. Once the input has been flashed to the session, you may easily [retrieve it](/docs/{{version}}/requests#retrieving-old-input) during the next request to repopulate the form:

사용자를 새 위치로 리디렉션하기 전에 `RedirectResponse` 인스턴스에서 제공하는 `withInput` 메서드를 사용하여, 현재 요청의 입력 데이터를 세션으로 플래시할 수 있습니다. 이는 일반적으로 사용자에게 유효성 검사 오류가 발생한 경우 수행됩니다. 입력이 세션에 플래시되면 다음 요청에서 양식 다시 채우기를 쉽게 [검색](/docs/{{version}}/requests#retriving-old-input)할 수 있습니다.

    return back()->withInput();

<a name="other-response-types"></a>
## Other Response Types
## 다른 Response 타입들

The `response` helper may be used to generate other types of response instances. When the `response` helper is called without arguments, an implementation of the `Illuminate\Contracts\Routing\ResponseFactory` [contract](/docs/{{version}}/contracts) is returned. This contract provides several helpful methods for generating responses.

`response` 헬퍼 함수는 다른 타입의 response 인스턴스를 생성하는데 사용될 수 있습니다. `response` 헬퍼 함수가 인자 없이 호출되면, `Illuminate\Contracts\Routing\ResponseFactory` [contract](/docs/{{version}}/contracts)의 구현체가 반환됩니다. 이 contract response를 생성하는데 몇가지 유용한 메소드를 제공합니다.

<a name="view-responses"></a>
### View Responses
### View Responses

If you need control over the response's status and headers but also need to return a [view](/docs/{{version}}/views) as the response's content, you should use the `view` method:

response 의 status 와 header 뿐만 아니라, response 내용으로 [view](/docs/{{version}}/views)를 반환하고자 한다면, `view` 메소드를 사용해야 합니다.

    return response()
                ->view('hello', $data, 200)
                ->header('Content-Type', $type);

Of course, if you do not need to pass a custom HTTP status code or custom headers, you may use the global `view` helper function.

사용자가 직접 HTTP 상태 코드나, 특정 헤더 값을 전달할 필요는 없다면, 글로벌 `view` 헬퍼 함수를 사용해야 합니다.

<a name="json-responses"></a>
### JSON Responses
### JSON Responses

The `json` method will automatically set the `Content-Type` header to `application/json`, as well as convert the given array to JSON using the `json_encode` PHP function:

`json` 메소드는 자동으로 `Content-Type` 헤더를 `application/json` 으로 설정하고, PHP `json_encode` 함수를 사용하여 주어진 배열을 JSON으로 변환할 것입니다.

    return response()->json([
        'name' => 'Abigail',
        'state' => 'CA',
    ]);

If you would like to create a JSONP response, you may use the `json` method in combination with the `withCallback` method:

JSONP response 를 생성하고자 한다면, `json` 메소드와 `setCallback`를 조합하면 됩니다.

    return response()
                ->json(['name' => 'Abigail', 'state' => 'CA'])
                ->withCallback($request->input('callback'));

<a name="file-downloads"></a>
### File Downloads
### 파일 다운로드

The `download` method may be used to generate a response that forces the user's browser to download the file at the given path. The `download` method accepts a filename as the second argument to the method, which will determine the filename that is seen by the user downloading the file. Finally, you may pass an array of HTTP headers as the third argument to the method:

`download` 메소드는 사용자의 브라우저가 주어진 경로에 해당하는 파일을 다운로드 하게 하는 response를 생성하는 데 사용됩니다. `download` 메소드는 사용자가 다운로드 하는 파일의 이름을 두번째 인자로 받습니다. 마지막으로 HTTP 헤더의 배열을 세번째 인자로 전달할 수 있습니다.

    return response()->download($pathToFile);

    return response()->download($pathToFile, $name, $headers);

> {note} Symfony HttpFoundation, which manages file downloads, requires the file being downloaded to have an ASCII filename.

> {note} 파일 다운로드를 관리하는 Symfony HttpFoundation 클래스는 ASCII 형식의 파일 이름을 지정해야 합니다.

<a name="streamed-downloads"></a>
#### Streamed Downloads
#### 스트리밍 다운로드

Sometimes you may wish to turn the string response of a given operation into a downloadable response without having to write the contents of the operation to disk. You may use the `streamDownload` method in this scenario. This method accepts a callback, filename, and an optional array of headers as its arguments:

때로는, 특정 동작의 결과 내용을 디스크에 저장하지 않고 바로 다운로드 가능한 응답으로 반환하고자 할 수 있습니다. 이러한 경우 `streamDownload` 메소드를 사용할 수 있습니다. 이 메소드는 콜백과, 파일이름 그리고 옵션값으로 헤더 배열을 인자로 전달받습니다.

    use App\Services\GitHub;

    return response()->streamDownload(function () {
        echo GitHub::api('repo')
                    ->contents()
                    ->readme('laravel', 'laravel')['contents'];
    }, 'laravel-readme.md');

<a name="file-responses"></a>
### File Responses
#### 파일 Responses

The `file` method may be used to display a file, such as an image or PDF, directly in the user's browser instead of initiating a download. This method accepts the path to the file as its first argument and an array of headers as its second argument:

`file` 메소드는 파일을 다운로드 하는 대신에, 브라우저에 이미지나 PDF 와 같은 파일을 표시하는데 사용됩니다. 이 메소드는 첫번째 인자로 파일의 경로를, 두번째 인자로 헤더의 배열을 전달 받습니다.

    return response()->file($pathToFile);

    return response()->file($pathToFile, $headers);

<a name="response-macros"></a>
## Response Macros
## Response 매크로

If you would like to define a custom response that you can re-use in a variety of your routes and controllers, you may use the `macro` method on the `Response` facade. Typically, you should call this method from the `boot` method of one of your application's [service providers](/docs/{{version}}/providers), such as the `App\Providers\AppServiceProvider` service provider:

다양한 경로와 컨트롤러에서 재사용할 수 있는 사용자 정의 응답을 정의하려면 `Response` 파사드에서 `macro` 메소드를 사용할 수 있습니다. 일반적으로 `App\Providers\AppServiceProvider` 서비스 프로바이더와 같은 애플리케이션의 [서비스 프로바이더](/docs/{{version}}/providers) 중 하나의 `boot` 메소드에서 이 메소드를 호출해야 합니다.

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\Response;
    use Illuminate\Support\ServiceProvider;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            Response::macro('caps', function ($value) {
                return Response::make(strtoupper($value));
            });
        }
    }

The `macro` function accepts a name as its first argument and a closure as its second argument. The macro's closure will be executed when calling the macro name from a `ResponseFactory` implementation or the `response` helper:

`macro` 메소드는 매크로로 지정할 이름을 첫 번째 인자로, 클로저를 두 번째 인자로 전달 받습니다. 매크로로 등록된 클로저는 `response` 헬퍼 함수를 통해서 `ResponseFactory` 구현 객체에서 호출될것 입니다.

    return response()->caps('foo');
