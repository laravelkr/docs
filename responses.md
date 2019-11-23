# HTTP Responses

- [Responses 생성하기](#creating-responses)
    - [Responses에 헤더 추가하기](#attaching-headers-to-responses)
    - [Responses에 쿠키 추가하기](#attaching-cookies-to-responses)
    - [쿠키 & 암호화](#cookies-and-encryption)
- [리다이렉트](#redirects)
    - [이름이 지정된 라우트로 리다이렉트 하기](#redirecting-named-routes)
    - [컨트롤러 액션으로 리다이렉트 하기](#redirecting-controller-actions)
    - [외부 도메인으로 리다이렉트 하기](#redirecting-external-domains)
    - [세션의 임시 데이터와 함께 리다이렉트 하기](#redirecting-with-flashed-session-data)
- [기타 Response 타입들](#other-response-types)
    - [View Responses](#view-responses)
    - [JSON Responses](#json-responses)
    - [File Downloads](#file-downloads)
    - [File Responses](#file-responses)
- [Response 매크로](#response-macros)

<a name="creating-responses"></a>
## Response 생성하기

#### 문자열 & 배열

모든 라우트와 컨트롤러는 response 를 사용자 브라우저로 다시 반환해야 합니다. 라라벨은 response 를 반환하기 위한 몇가지 다른 방법을 제공합니다. 가장 기본적인 response 는 라우트나 컨트롤러에서 문자열을 반환하는 것입니다. 프레임워크는 자동으로 문자열을 전체 HTTP response로 변환할 것입니다.

    Route::get('/', function () {
        return 'Hello World';
    });

라우트나 컨트롤러에서 문자열을 반환하는것에 더하여, 배열을 반환할 수도 있습니다. 프레임워크는 자동으로 배열을 JSON response로 변환할 것입니다.

    Route::get('/', function () {
        return [1, 2, 3];
    });

> {tip} 라우트나 컨트롤러에서 [Eloquent 컬렉션](/docs/{{version}}/eloquent-collections) 또한 반환할 수 있다는 것을 알고 있습니까? 이는 자동적으로 JSON으로 변환됩니다. 한번 해보세요!

#### Response 객체

일반적으로, 라우트 액션에서 단순히 문자열이나 배열만 반환하지는 않습니다. 대신에 `Illuminate\Http\Response` 전체 인스턴스 또는 [views](/docs/{{version}}/views)을 반환할 수도 있습니다.

`Response` 인스턴스를 반환하는 것은 여러분이 response 의 HTTP 상태 코드나 헤더를 변경할 수 있도록 지원합니다. `Symfony\Component\HttpFoundation\Response` 클래스를 구현하고 있는 `Response` 인스턴스는 HTTP response 를 만들기 위한 다양한 메소드를 제공합니다.

    Route::get('home', function () {
        return response('Hello World', 200)
                      ->header('Content-Type', 'text/plain');
    });

<a name="attaching-headers-to-responses"></a>
#### Response에 헤더 추가하기

대부분의 response 메소드는 response 인스턴스의 유연한 생성을 위해서 체이닝이 가능합니다. 예를 들어서 사용자에게 response를 되돌려 주기 전에 `header` 메소드를 통해서 header를 추가할 수 있습니다.

    return response($content)
                ->header('Content-Type', $type)
                ->header('X-Header-One', 'Header Value')
                ->header('X-Header-Two', 'Header Value');

또는, response 에 추가하고자 하는 헤더의 배열을 지정하기 위해서 `withHeaders` 메소드를 사용할 수 있습니다.

    return response($content)
                ->withHeaders([
                    'Content-Type' => $type,
                    'X-Header-One' => 'Header Value',
                    'X-Header-Two' => 'Header Value',
                ]);

##### Cache Control 미들웨어

Laravel에는 `cache.headers` 미들웨어가 포함되어 있습니다. 이 미들웨어는 라우트 그룹에 `Cache-Control` 헤더를 빠르게 설정하는 데 사용할 수 있습니다. 지시어 목록에 `etag`가 지정되면 응답 내용의 MD5 해시가 자동으로 ETag 식별자로 설정됩니다.

    Route::middleware('cache.headers:public;max_age=2628000;etag')->group(function() {
        Route::get('privacy', function () {
            // ...
        });

        Route::get('terms', function () {
            // ...
        });
    });

<a name="attaching-cookies-to-responses"></a>
#### Responses에 Cookies 추가하기

response 인스턴스의 `cookie` 메소드는 response 에 쿠키를 손쉽게 추가할 수 있게 해줍니다. 예를 들어 다음과 같이 response 인스턴스에서 `cookie` 메소드를 사용하여 쿠키를 생성하고 우아하게 이를 response 인스턴스에 추가할 수 있습니다.

    return response($content)
                    ->header('Content-Type', $type)
                    ->cookie('name', 'value', $minutes);

`cookie` 메소드는 또한 자주 사용되지 않는 몇가지 인자를 더 받아들입니다. 일반적으로 이 인자들은 PHP의 내장된 [setcookie](https://secure.php.net/manual/en/function.setcookie.php) 메소드에 제공되는 인자들과 동일한 목적과 의미가 있습니다.

    ->cookie($name, $value, $minutes, $path, $domain, $secure, $httpOnly)

또는, 애플리케이션의 response에 쿠키를 추가하기 위해 `Cookie` 파사드를 이용하여 "queue"할 수 있습니다. `queue` 메소드는 `Cookie` 인스턴스나 `Cookie` 인스턴스를 생성하는데 필요한 인자를 받습니다. 이 쿠키는 response가 브라우저로 전달되기 전에 추가됩니다.

    Cookie::queue(Cookie::make('name', 'value', $minutes));

    Cookie::queue('name', 'value', $minutes);

<a name="cookies-and-encryption"></a>
#### 쿠키 & 암호화

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
## 리다이렉트

리다이렉트 response는 `Illuminate\Http\RedirectResponse` 클래스의 인스턴스이며 사용자를 다른 URL로 리다이렉트 시키기 위한 준비된 헤더를 포함하고 있습니다. `RedirectResponse` 인스턴스를 생성하기 위해서는 몇 가지 방법이 있습니다. 가장 간단한 방법은 글로벌 `redirect` 헬퍼를 사용하는 것입니다.

    Route::get('dashboard', function () {
        return redirect('home/dashboard');
    });

때때로, 전송된 form 양식이 유효하지 않은 경우와 같이 사용자를 이전 위치로 리다이렉트 시키고자 할 수 있습니다. 글로벌 `back` 헬퍼 함수를 사용하여 이렇게 할 수 있습니다. 이 기능은 [세션](/docs/{{version}}/session)을 사용하기 때문에 `back` 함수를 사용하는 라우트 호출은 `web` 미들웨어 그룹 안에 있거나 세션 미들웨어가 적용되어 있어야 합니다.

    Route::post('user/profile', function () {
        // Validate the request...

        return back()->withInput();
    });

<a name="redirecting-named-routes"></a>
### 이름이 지정된 라우트로 리다이렉트 하기

`redirect` 헬퍼가 아무런 인자 없이 호출될 때에는, `Illuminate\Routing\Redirector` 인스턴스가 반환되어, `Redirector` 인스턴스의 메소드를 사용할 수 있습니다. 다음과 같이 이름이 지정된 라우트에 대한 `RedirectResponse` 를 생성하려면 `route` 메소드를 사용할 수 있습니다.

    return redirect()->route('login');

라우트가 인자를 받아야 한다면, `route` 메소드의 두번째 인자로 이를 전달할 수 있습니다.

    // For a route with the following URI: profile/{id}

    return redirect()->route('profile', ['id' => 1]);

#### Eloquent 모델을 통한 파라미터 채우기

Eloquent 모델에 의해서 채워지는 "ID" 파라미터를 가진 라우트로 리다이렉트 하는 경우, 모델 그 자체를 전달할 수 있습니다. ID 는 자동으로 추출됩니다.

    // For a route with the following URI: profile/{id}

    return redirect()->route('profile', [$user]);

만약 라우트 파라미터에 저장되는 값을 커스터마이징 하려면, Eloquent 모델의 `getRouteKey` 메소드를 오버라이드해야 합니다.

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
### 컨트롤러 액션으로 리다이렉트 하기

또한 [컨트롤러 액션](/docs/{{version}}/controllers)으로 리다이렉트하는 응답을 생성할 수도 있습니다. 이렇게 하기 위해서는, 컨트롤러와 액션의 이름을 `action` 메소드에 전달하면 됩니다. 주의할 것은 라라벨이 자동으로 베이스 컨트롤러 네임스페이스를 설정하기 때문에 전체 네임스페이스를 지정할 필요가 없다는 것입니다.

    return redirect()->action('HomeController@index');

컨트롤러 라우트에 파라미터가 필요한 경우, `action` 메소드의 두 번째 인자로 전달하면 됩니다.

    return redirect()->action(
        'UserController@profile', ['id' => 1]
    );

<a name="redirecting-external-domains"></a>
### 외부 도메인으로 리다이렉트 하기

때로는 애플리케이션의 외부 도메인으로 리다이렉트 해야 하기도 합니다. 추가적인 URL 인코딩, 유효성 검사와 확인 과정 없이 `RedirectResponse`을 만드는 `away` 메소드를 호출해서 이렇게 할 수 있습니다.

    return redirect()->away('https://www.google.com');

<a name="redirecting-with-flashed-session-data"></a>
### 세션의 임시 데이터와 함께 리다이렉트 하기

새로운 URL로 리다이렉팅 되는 것과 [세션에 데이터를 임시 저장하는것](/docs/{{version}}/session#flash-data)은 일반적으로 동시에 완료됩니다. 일반적으로, 이 작업은 여러분이 성공 메세지를 세션에 임시 저장할 때 작업을 성공적으로 액션을 수행한 다음에 완료됩니다. 보다 간편하게, 한번에 `RedirectResponse` 인스턴스를 생성하고 데이터를 세션에 임시저장하는 유연한 메소드 체이닝을 할 수 있습니다.

    Route::post('user/profile', function () {
        // Update the user's profile...

        return redirect('dashboard')->with('status', 'Profile updated!');
    });

사용자가 리다이렉션 된 이후에, [세션](/docs/{{version}}/session)에서 임시 저장된 데이터를 표시할 수 있습니다. 예를 들어 [블레이드 문법](/docs/{{version}}/blade)을 사용해 보겠습니다.

    @if (session('status'))
        <div class="alert alert-success">
            {{ session('status') }}
        </div>
    @endif

<a name="other-response-types"></a>
## 다른 Response 타입들

`response` 헬퍼 함수는 다른 타입의 response 인스턴스를 생성하는데 사용될 수 있습니다. `response` 헬퍼 함수가 인자 없이 호출되면, `Illuminate\Contracts\Routing\ResponseFactory` [contract](/docs/{{version}}/contracts)의 구현체가 반환됩니다. 이 contract response를 생성하는데 몇가지 유용한 메소드를 제공합니다.

<a name="view-responses"></a>
### View Responses

response 의 status 와 header 뿐만 아니라, response 내용으로 [view](/docs/{{version}}/views)를 반환하고자 한다면, `view` 메소드를 사용해야 합니다.

    return response()
                ->view('hello', $data, 200)
                ->header('Content-Type', $type);

사용자가 직접 HTTP 상태 코드나, 특정 헤더 값을 전달할 필요는 없다면, 글로벌 `view` 헬퍼 함수를 사용해야 합니다.

<a name="json-responses"></a>
### JSON Responses

`json` 메소드는 자동으로 `Content-Type` 헤더를 `application/json` 으로 설정하고, PHP `json_encode` 함수를 사용하여 주어진 배열을 JSON으로 변환할 것입니다.

    return response()->json([
        'name' => 'Abigail',
        'state' => 'CA'
    ]);

JSONP response 를 생성하고자 한다면, `json` 메소드와 `setCallback`를 조합하면 됩니다.

    return response()
                ->json(['name' => 'Abigail', 'state' => 'CA'])
                ->withCallback($request->input('callback'));

<a name="file-downloads"></a>
### 파일 다운로드

`download` 메소드는 사용자의 브라우저가 주어진 경로에 해당하는 파일을 다운로드 하게 하는 response를 생성하는 데 사용됩니다. `download` 메소드는 사용자가 다운로드 하는 파일의 이름을 두번째 인자로 받습니다. 마지막으로 HTTP 헤더의 배열을 세번째 인자로 전달할 수 있습니다.

    return response()->download($pathToFile);

    return response()->download($pathToFile, $name, $headers);

    return response()->download($pathToFile)->deleteFileAfterSend();

> {note} 파일 다운로드를 관리하는 Symfony HttpFoundation 클래스는 ASCII 형식의 파일 이름을 지정해야 합니다.

#### 스트리밍 다운로드

때로는, 특정 동작의 결과 내용을 디스크에 저장하지 않고 바로 다운로드 가능한 응답으로 반환하고자 할 수 있습니다. 이러한 경우 `streamDownload` 메소드를 사용할 수 있습니다. 이 메소드는 콜백과, 파일이름 그리고 옵션값으로 헤더 배열을 인자로 전달받습니다.

    return response()->streamDownload(function () {
        echo GitHub::api('repo')
                    ->contents()
                    ->readme('laravel', 'laravel')['contents'];
    }, 'laravel-readme.md');

<a name="file-responses"></a>
#### 파일 Responses

`file` 메소드는 파일을 다운로드 하는 대신에, 브라우저에 이미지나 PDF 와 같은 파일을 표시하는데 사용됩니다. 이 메소드는 첫번째 인자로 파일의 경로를, 두번째 인자로 헤더의 배열을 전달 받습니다.

    return response()->file($pathToFile);

    return response()->file($pathToFile, $headers);

<a name="response-macros"></a>
## Response 매크로

다양한 라우트와 컨트롤러에서 재사용할 수 있는 커스텀 response를 정의하려면 `Response` 파사드의 `macro` 메소드를 사용하면 됩니다. 예를 들어 [서비스 프로바이더](/docs/{{version}}/providers)의 `boot` 메소드를 살펴보겠습니다.

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\Response;
    use Illuminate\Support\ServiceProvider;

    class ResponseMacroServiceProvider extends ServiceProvider
    {
        /**
         * Register the application's response macros.
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

`macro` 메소드는 매크로로 지정할 이름을 첫 번째 인자로, 클로저를 두 번째 인자로 전달 받습니다. 매크로로 등록된 클로저는 `response` 헬퍼 함수를 통해서 `ResponseFactory` 구현 객체에서 호출될것 입니다.

    return response()->caps('foo');
