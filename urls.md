# URL 생성

- [시작하기](#introduction)
- [기본적인 내용](#the-basics)
    - [URL 생성하기](#generating-urls)
    - [현재 URL에 접근하기](#accessing-the-current-url)
- [명명된 경로의 URL](#urls-for-named-routes)
    - [서명된 URL](#signed-urls)
    - [유효하지 않은 서명된 경로에 대한 응답](#responding-to-invalid-signed-routes)
- [Controller 작업용 URL](#urls-for-controller-actions)
- [기본값](#default-values)

<a name="introduction"></a>
## 시작하기

라라벨은 어플리케이션에서 URL을 생성하는것을 도와주는 몇가지 helper 함수를 제공합니다. 헬퍼 함수는 템플릿과 API responses에 link를 연결하거나 어플리케이션의 다른 부분에 대한 redirect response를 생성하는 데에 주로 유용합니다.

<a name="the-basics"></a>
## 기본적인 내용

<a name="generating-urls"></a>
### URL 생성하기

`url` 헬퍼를 사용하여 어플리케이션에 대한 임의의 URL을 생성할 수 있습니다. 생성된 URL은 자동으로 HTTP나 HTTPS와 같은 스키마를 사용하고 어플리케이션에 의해 처리중인 현재 request의 host를 사용합니다.


    $post = App\Models\Post::find(1);

    echo url("/posts/{$post->id}");

    // http://example.com/posts/1

<a name="accessing-the-current-url"></a>
### 현재 URL에 접근하기

만약 `url` 헬퍼에 아무런 경로를 지정하지 않는다면, `Illuminate\Routing\UrlGenerator` 인스턴스가 반환되며, 현재 URL 정보에 access할 수 있습니다.

    // Get the current URL without the query string...
    echo url()->current();

    // Get the current URL including the query string...
    echo url()->full();

    // Get the full URL for the previous request...
    echo url()->previous();

각각의  메소드는 또한 'URL' [파사드](/docs/{{version}}/facades)를 통해서도 엑세스 할 수 있습니다.

    use Illuminate\Support\Facades\URL;

    echo URL::current();

<a name="urls-for-named-routes"></a>
## 이름이 지정된 라우트 URL

'route' 헬퍼는 [이름이 지정된 라우트](/docs/{{version}}/routing#named-routes) URL을 생성하는데 사용될 수 있습니다. 라우트에 이름을 지정하여 사용하면, 라우트에 정의된 실제 URL에 구애받지 않고서도 URL을 생성할 수 있습니다. 따라서 라우트의 URL이 변경되었다고 해서 `route` 함수를 호출한 곳을 모두 수정할 필요가 없습니다. 예를 들자면 애플리케이션에서 다음과 같이 정의된 라우트를 가지고 있다고 가정해보십시오:

    Route::get('/post/{post}', function (Post $post) {
        //
    })->name('post.show');

이 라우트에 대한 URL을 생성하려면 `route` 헬퍼 함수를 다음과 같이 사용하면 됩니다.

    echo route('post.show', ['post' => 1]);

    // http://example.com/post/1

물론, 'route' 헬퍼는 또한 여러개의 파라미터를 가진 라우트 URL을 생성할 수도 있습니다.

    Route::get('/post/{post}/comment/{comment}', function (Post $post, Comment $comment) {
        //
    })->name('comment.show');

    echo route('comment.show', ['post' => 1, 'comment' => 3]);

    // http://example.com/post/1/comment/3

라우트 정의 매개변수에 해당하지 않는 추가적 배열 요소는 URL의 쿼리 문자열에 추가됩니다.

    echo route('post.show', ['post' => 1, 'search' => 'rocket']);

    // http://example.com/post/1?search=rocket

<a name="eloquent-models"></a>
#### 엘로퀀트 모델

여러분은 종종 [Eloquent 모델](/docs/{{version}}/eloquent)의 라우트 키(전형적으로 기본 키)를 사용하여 URL을 생성하게 될 겁니다. 이러한 이유로, 파라미터 값으로 Eloquent 모델을 전달할 수도 있습니다. `route` 헬퍼 함수는 자동으로 모델의 라우트 키를 추출하여 사용할 것입니다.

    echo route('post.show', ['post' => $post]);

<a name="signed-urls"></a>
### 서명이 적용된 (signed) URL

라라벨에서는 이름이 지정된 라우트(named routed)에 "서명이 적용된(signed)" URL을 손쉽게 만들 수 있습니다. 이러한 URL에는 "서명이 적용된" 해쉬가 쿼리 스트링 뒤에 추가되어 URL이 생성된 이후에 수정되지 않았음을 확인할 수 있게 합니다. 서명이 적용된 URL은 공개적으로 엑세스가 가능하지만 여전히 조작에 대한 보호 레이어가 필요한 라우트에 특별히 유용합니다

예를 들어, 여러분은 서명이 적용된 URL을 고객들이 이메일을 보내는 공개된 "구독 취소" 링크를 구현하는데 사용할 수 있습니다. 이름이 지정된 라우트에 서명이 적용된 URL을 구성하려면, `URL` 파사드에 `signedRoute` 메소드를 사용하면 됩니다.

    use Illuminate\Support\Facades\URL;

    return URL::signedRoute('unsubscribe', ['user' => 1]);

지정된 시간 후에 만료되는 임시 서명된 경로 URL을 생성하려면 `temporarySignedRoute` 메소드를 사용할 수 있습니다. Laravel이 서명된 임시 경로 URL의 유효성을 검사할 때 서명된 URL에 인코딩된 만료 타임스탬프가 경과되지 않았는지 확인합니다.

    use Illuminate\Support\Facades\URL;

    return URL::temporarySignedRoute(
        'unsubscribe', now()->addMinutes(30), ['user' => 1]
    );

<a name="validating-signed-route-requests"></a>
#### 서명이 적용된 라우트 Request-요청 Validting-유효성검사하기

들어되는 Request에 유효한 서명이 있는지 확인하기 위해서는, 들어오는 `Request` 에 `hasValidSignature` 메소드를 호출해야 합니다.

    use Illuminate\Http\Request;

    Route::get('/unsubscribe/{user}', function (Request $request) {
        if (! $request->hasValidSignature()) {
            abort(401);
        }

        // ...
    })->name('unsubscribe');

또는, `Illuminate\Routing\Middleware\ValidateSignature` [미들웨어](/docs/{{version}}/middleware)를 경로에 지정할 수도 있습니다. 아직 존재하지 않는다면, 이 미들웨어를 HTTP 커널의 `routeMiddleware` 배열에 있는 키로 지정해야 합니다.

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

커널에 미들웨어를 등록하고 나면, 라우트에 이 미들웨어를 추가할 수 있습니다. 들어오는 요청에 유효한 서명이 없으면, 미들웨어는 자동으로 `403` HTTP Response를 반환합니다.

    Route::post('/unsubscribe/{user}', function (Request $request) {
        // ...
    })->name('unsubscribe')->middleware('signed');

<a name="responding-to-invalid-signed-routes"></a>
#### 유효하지 않은 서명된 경로에 대한 응답

누군가 만료된 서명의 URL을 방문하면 '403' HTTP 상태 코드에 대한 일반 오류 페이지가 표시됩니다. 그러나 예외 처리기에서 `InvalidSignatureException` 예외에 대해 "렌더링 가능한" 사용자 정의 클로저를 정의하여 이 동작을 사용자 정의할 수 있습니다. 이 클로저는 HTTP 응답을 반환해야 합니다.

    use Illuminate\Routing\Exceptions\InvalidSignatureException;

    /**
     * Register the exception handling callbacks for the application.
     *
     * @return void
     */
    public function register()
    {
        $this->renderable(function (InvalidSignatureException $e) {
            return response()->view('error.link-expired', [], 403);
        });
    }

<a name="urls-for-controller-actions"></a>
## 컨트롤러 액션 URL

`action` 함수는 주어진 컨트롤러 액션에 대한 URL을 생성합니다.

    use App\Http\Controllers\HomeController;

    $url = action([HomeController::class, 'index']);

컨트롤러 메소드가 라우트 파라미터 인자를 필요로 한다면, 함수의 두번째 인자로 이를 전달할 수 있습니다.

    $url = action([UserController::class, 'profile'], ['id' => 1]);

<a name="default-values"></a>
## 기본값

몇몇 애플리케이션에서는 특정 URL 파라미터에 대해 요청-reqeust 전의 기본값을 지정할 수 있습니다. 예를 들어, 다수의 라우트에서 `{locale}` 파라미터를 정의한다고 가정해보겠습니다.

    Route::get('/{locale}/posts', function () {
        //
    })->name('post.index');

`route` 헬퍼를 호출할 때마다 항상 `locale`을 전달하는 것은 번거롭습니다. 따라서 `URL::defaults` 메소드를 사용하여 현재 요청 중에 항상 적용될 이 매개변수의 기본값을 정의할 수 있습니다. 현재 요청에 액세스할 수 있도록 [라우트 미들웨어](/docs/{{version}}/middleware#assigning-middleware-to-routes)에서 이 메서드를 호출할 수 있습니다

    <?php

    namespace App\Http\Middleware;

    use Closure;
    use Illuminate\Support\Facades\URL;

    class SetDefaultLocaleForUrls
    {
        /**
         * Handle the incoming request.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  \Closure  $next
         * @return \Illuminate\Http\Response
         */
        public function handle($request, Closure $next)
        {
            URL::defaults(['locale' => $request->user()->locale]);

            return $next($request);
        }
    }

`locale` 매개변수의 기본값이 설정되면 `route` 도우미를 통해 URL을 생성할 때 더 이상 값을 전달할 필요가 없습니다.

<a name="url-defaults-middleware-priority"></a>
#### URL 기본값 & 미들웨어 우선 순위

URL 기본값을 설정하면 Laravel의 암시적 모델 바인딩 처리를 방해할 수 있습니다. 따라서 라라벨 고유의 `SubstituteBindings` 미들웨어보다 먼저 실행되도록 URL 기본값을 설정한 [미들웨어를 우선적으로 처리](/docs/{{version}}/middleware#sorting-middleware)해야 합니다. 애플리케이션 HTTP 커널의 `$middlewarePriority` 속성 내에서 `SubstituteBindings` 미들웨어보다 먼저 미들웨어가 발생하도록 하여 이를 수행할 수 있습니다.

`$middlewarePriority` 속성은 기본 `Illuminate\Foundation\Http\Kernel` 클래스에 정의되어 있습니다. 해당 클래스에서 정의를 복사하고 이를 수정하기 위해 애플리케이션의 HTTP 커널에서 덮어쓸 수 있습니다.

    /**
     * The priority-sorted list of middleware.
     *
     * This forces non-global middleware to always be in the given order.
     *
     * @var array
     */
    protected $middlewarePriority = [
        // ...
         \App\Http\Middleware\SetDefaultLocaleForUrls::class,
         \Illuminate\Routing\Middleware\SubstituteBindings::class,
         // ...
    ];
