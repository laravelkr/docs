# URL 생성

- [시작하기](#introduction)
- [기본적인 내용](#the-basics)
    - [기본 URL 생성하기](#generating-basic-urls)
    - [현재 URL 엑세스하기](#accessing-the-current-url)
- [이름이 지정된 라우트 URL](#urls-for-named-routes)
    - [Signed URLs](#signed-urls)
- [컨트롤러 액션 URL](#urls-for-controller-actions)
- [기본값](#default-values)

<a name="introduction"></a>
## 시작하기

라라벨은 애플리케이션에서 URL을 생성하는 것을 도와주는 몇가지 헬퍼 함수를 제공합니다. 당연하게도, 이는 템플릿 안에서 API 응답을 위한 링크를 생성하거나, 애플리케이션의 다른 곳으로 이동하는 리다이렉트 응답-response를 생성할 때 유용하게 사용됩니다.

<a name="the-basics"></a>
## 기본적인 내용

<a name="generating-basic-urls"></a>
### 기본 URL 생성하기

`url` 헬퍼는 애플리케이션에서 사용하는 임의의 URL을 생성하는데 사용합니다. 생성된 URL은 자동으로 현재 요청-request의 (HTTP 또는 HTTPS) 스키마와 호스트를 사용합니다:

    $post = App\Post::find(1);

    echo url("/posts/{$post->id}");

    // http://example.com/posts/1

<a name="accessing-the-current-url"></a>
### 현재 URL 엑세스하기

`url` 헬퍼에 아무런 경로를 지정하지 않는다면, `Illuminate\Routing\UrlGenerator` 인스턴스가 반환되며, 현재 URL에 대한 정보에 엑세스 할 수 있습니다:

    // Get the current URL without the query string...
    echo url()->current();

    // Get the current URL including the query string...
    echo url()->full();

    // Get the full URL for the previous request...
    echo url()->previous();

각각의 메소드는 `URL` [파사드](/docs/{{version}}/facades)를 통해서도 엑세스 할 수 있습니다:

    use Illuminate\Support\Facades\URL;

    echo URL::current();

<a name="urls-for-named-routes"></a>
## 이름이 지정된 라우트 URL

`route` 헬퍼 함수는 이름이 지정된 라우트에 대한 URL을 생성하는데 사용할 수 있습니다. 라우트에 이름을 지정하여 사용하면, 라우트에 정의된 실제 URL에 구애받지 않고서도 URL을 생성할 수 있습니다. 따라서 라우트의 URL이 변경되었다고 해서 `route` 함수를 호출한 곳을 모두 수정할 필요가 없습니다. 예를 들자면 애플리케이션에서 다음과 같이 정의된 라우트를 가지고 있다고 가정해보십시오:

    Route::get('/post/{post}', function () {
        //
    })->name('post.show');

이 라우트에 대한 URL을 생성하려면 `route` 헬퍼 함수를 다음과 같이 사용하면 됩니다:

    echo route('post.show', ['post' => 1]);

    // http://example.com/post/1

여러분은 종종 [Eloquent 모델](/docs/{{version}}/eloquent)의 기본 키를 사용하여 URL을 생성하게 될 겁니다. 이러한 이유로, 파라미터 값으로 Eloquent 모델을 전달할 수도 있습니다. `route` 헬퍼 함수는 자동으로 모델의 기본 키를 추출하여 사용합니다:

    echo route('post.show', ['post' => $post]);

<a name="signed-urls"></a>
### 서명이 적용된 (signed) URL

라라벨에서는 이름이 지정된 라우트(named routed)에 "서명이 적용된(signed)" URL을 손쉽게 만들 수 있습니다. 이러한 URL에는 "서명이 적용된" 해쉬가 쿼리 스트링뒤에 추가되어 URL이 생성된 이후에 수정되지 않았음을 확인할 수 있게 합니다. 서명이 적용된 URL은 공개적으로 엑세스가 가능하지만 조작에 대한 보호 레이어가 필요한 라우트에 특별히 유용합니다

예를 들어, 여러분은 서명이 적용된 URL을 고객들이 이메일을 보내는 공개된 "구독 취소" 링크를 구현하는데 사용할 수 있습니다. 이름이 지정된 라우트에 서명이 적용된 URL을 구성하려면, `URL` 파사드에 `signedRoute` 메소드를 사용하면 됩니다:

    use Illuminate\Support\Facades\URL;

    return URL::signedRoute('unsubscribe', ['user' => 1]);

여러분은 `temporarySignedRoute` 메소드를 사용하여, 임시적으로 생성되어 만료되는, 서명이 적용된 라우트 URL을 생성할 수 있습니다:

    use Illuminate\Support\Facades\URL;

    return URL::temporarySignedRoute(
        'unsubscribe', now()->addMinutes(30), ['user' => 1]
    );

#### 서명이 적용된 라우트 Request-요청 Validting-유효성검사하기

유입되는 request-요청이 유효한 서명이 있는지 확인하기 위해서는 유입되는 `Request` 에 `hasValidSignature` 메소드를 호출해야 합니다:

    use Illuminate\Http\Request;

    Route::get('/unsubscribe/{user}', function (Request $request) {
        if (! $request->hasValidSignature()) {
            abort(401);
        }

        // ...
    })->name('unsubscribe');

또는, 라우트에 `Illuminate\Routing\Middleware\ValidateSignature` 미들웨어를 지정할 수도 있습니다. 이 미들웨어가 존재하지 않는다면, HTTP 커널의 `routeMiddleware` 배열에 이 미들웨어를 지정해야 합니다:

    /**
     * The application's route middleware.
     *
     * These middleware may be assigned to groups or used individually.
     *
     * @var array
     */
    protected $routeMiddleware = [
        'signed' => \Illuminate\Routing\Middleware\ValidateSignature::class,
    ];

커널에 미들웨어를 등록한다음, 라우트에 이 미들웨어를 추가해야 합니다. 유입되는 request-요청에 유효한 서명이 없다면, 미들웨어는 자동으로 `403` 오류 response-응답을 반환합니다:

    Route::post('/unsubscribe/{user}', function (Request $request) {
        // ...
    })->name('unsubscribe')->middleware('signed');

<a name="urls-for-controller-actions"></a>
## 컨트롤러 액션 URL

`action` 함수는 주어진 컨트롤러 액션에 대한 URL을 생성합니다. 컨트롤러의 전체 네임스페이스를 전달할 필요는 없습니다. 대신에 `App\Http\Controllers` 네임스페이스로부터 시작하는 컨트롤러 클래스 이름을 전달하십시오:

    $url = action('HomeController@index');

"callable" 배열 문법을 통해서 액션을 참조하도록 할 수 있습니다:

    use App\Http\Controllers\HomeController;

    $url = action([HomeController::class, 'index']);

컨트롤러 메소드가 라우트 파라미터 인자를 필요로 한다면, 함수의 두번째 인자로 이를 전달할 수 있습니다:

    $url = action('UserController@profile', ['id' => 1]);

<a name="default-values"></a>
## 기본값

몇몇 애플리케이션에서는 특정 URL 파라미터에 대해 요청-reqeust 전의 기본값을 지정할 수 있습니다. 예를 들어, 다수의 라우트에서 `{locale}` 파라미터를 정의한다고 가정해보겠습니다:

    Route::get('/{locale}/posts', function () {
        //
    })->name('post.index');

`route` 헬퍼를 호출할 때마다 `locale` 을 전달해야 하는 것은 번거로운 일일 수 있습니다. 따라서 `URL::defaults` 메소드를 사용하여 현재 요청-request 중에서 항상 적용될 파라미터의 기본값을 정의 할 수 있습니다. 라우트 미들웨어에서 이 메소드를 호출하여 현재의 요청-request에 엑세스 할 수 있습니다:

    <?php

    namespace App\Http\Middleware;

    use Closure;
    use Illuminate\Support\Facades\URL;

    class SetDefaultLocaleForUrls
    {
        public function handle($request, Closure $next)
        {
            URL::defaults(['locale' => $request->user()->locale]);

            return $next($request);
        }
    }

`locale` 파라미터의 기본값이 설정되면 `rotue` 헬퍼를 통해서 URL을 생성 할 때 더 이상 매번 값을 전달하지 않아도 됩니다.
