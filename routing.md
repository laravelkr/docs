# 라우팅

- [기본적인 라우팅](#basic-routing)
    - [리다이렉트 라우트](#redirect-routes)
    - [뷰-View 라우트](#view-routes)
- [라우트 파라미터](#route-parameters)
    - [필수 파라미터](#required-parameters)
    - [선택적인 파라미터](#parameters-optional-parameters)
    - [정규표현식 제약](#parameters-regular-expression-constraints)
- [이름이 지정된 라우트](#named-routes)
- [라우트 그룹](#route-groups)
    - [미들웨어](#route-group-middleware)
    - [서브 도메인 라우팅](#route-group-subdomain-routing)
    - [라우트 접두사](#route-group-prefixes)
    - [라우트 이름 접두사](#route-group-name-prefixes)
- [라우트 모델 바인딩](#route-model-binding)
    - [묵시적 바인딩](#implicit-binding)
    - [명시적 바인딩](#explicit-binding)
- [대체 라우트](#fallback-routes)
- [Rate 제한](#rate-limiting)
    - [Rate Limiter 정의하기](#defining-rate-limiters)
    - [Rate Limiter를 라우트에 연결하기](#attaching-rate-limiters-to-routes)
- [Form-폼 메서드 Sppring-스푸핑](#form-method-spoofing)
- [현재 라우트에 엑세스하기](#accessing-the-current-route)
- [크로스 오리진 리소스 쉐어링 (CORS)](#cors)
- [라우트 캐싱](#route-caching)

<a name="basic-routing"></a>
## 기본적인 라우팅

가장 기본적인 라라벨 라우트는 URI와 클로저로 정의할 수 있으며, 복잡한 라우팅 설정 파일 없이도 라우팅을 정의하는 간단하고 쉽게 이해할 수 있는 방법을 제공합니다.

    use Illuminate\Support\Facades\Route;

    Route::get('/greeting', function () {
        return 'Hello World';
    });

<a name="the-default-route-files"></a>
#### 기본 라우트 파일

모든 라라벨의 라우트는 `route` 디렉토리 안에 들어 있는 라우트 파일에 정의되어 있습니다. 이 파일은 애플리케이션의 `App\Providers\RouteServiceProvider`에 의해서 자동으로 로드됩니다. `routes/web.php` 파일은 웹 인터페이스를 위한 라우트들을 정의합니다. 이 라우트들에는 세션 상태와 CSRF 보호와 같은 기능을 제공하는 `web` 미들웨어 그룹이 할당되어 있습니다. `routes/api.php` 안에 들어 있는 라우트들은 stateless 하고 `api` 미들웨어 그룹이 할당되어 있습니다.

대부분의 애플리케이션에서, 여러분은 `routes/web.php` 파일에 라우트를 정의하여 시작할 수 있습니다. `routes/web.php` 에 정의된 라우트는 브라우저를 통해서 유입되는 라우트 URL을 정의하는데 사용됩니다. 예를 들어 브라우저에서 `http://example.com/user`와 같이 접속하기 위해서 다음의 라우트를 정의할 수 있습니다.

    use App\Http\Controllers\UserController;

    Route::get('/user', [UserController::class, 'index']);

`routes/api.php` 파일은 `RouteServiceProvider`의 라우트 그룹안에 중첩되어 정의되어 있습니다. 이 그룹을에 의해서 `/api` URI가 자동으로 앞에 붙게 되므로, 이 파일에 정의한 모든 라우트에 일일이 적용하지 않아도 됩니다. `RouteServiceProvider` 파일의 라우트 그룹 옵션을 수정하면, 다른 prefix를 붙일 수도 있습니다.

<a name="available-router-methods"></a>
#### 가능한 라우터 메서드

라우터는 다음의 HTTP 메서드에 해당하는 응답을 위한 라우트를 등록할 수 있습니다.

    Route::get($uri, $callback);
    Route::post($uri, $callback);
    Route::put($uri, $callback);
    Route::patch($uri, $callback);
    Route::delete($uri, $callback);
    Route::options($uri, $callback);

때로는 여러개의 HTTP 메서드에 응답하는 라우트를 등록해야 할 수도 있습니다. 이경우 `match` 메서드를 사용하면 됩니다. 또는 `any` 메서드를 사용하여 모든 HTTP 메서드에 응답하는 라우트를 등록할 수도 있습니다.

    Route::match(['get', 'post'], '/', function () {
        //
    });

    Route::any('/', function () {
        //
    });

> {tip} 동일한 URI를 공유하는 여러개의 라우트를 정의해야할 때에는 `get`, `post`, `put`, `patch`, `delete`, `options` 메서드를 `any`, `match`, `redirect` 메서드보다 먼저 정의해야합니다. 이렇게 해야 의도대로 올바른 라우트가 일치하는지 확인할 수 있습니다. 

<a name="dependency-injection"></a>
#### 의존성 주입

라우트 클로저를 정의할 때 필요한 의존성을 타입힌트할 수 있습니다. 선언되어 있는 의존객체는 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)에 의해서 자동으로 주입됩니다. 예를 들어 `Illuminate\Http\Request` 클래스를 타입힌트 하여 현재 HTTP 요청객체를 라우트 클로저에 자동으로 전달되게 할 수 있습니다. 

    use Illuminate\Http\Request;

    Route::get('/users', function (Request $request) {
        // ...
    });

<a name="csrf-protection"></a>
#### CSRF 보호하기

명심하십시오. `web` 라우트 파일 안에 정의된 `POST`, `PUT`, `PATCH` 또는 `DELETE` 를 가리키는 라우트들은 모두 CSRF 토큰 필드를 포함해야 합니다. 그렇지 않으면, 이 요청들은 거부될 것입니다. CSRF 보호에 대해서 더 알아보려면 [CSRF 문서](/docs/{{version}}/csrf)를 읽어보십시오.

    <form method="POST" action="/profile">
        @csrf
        ...
    </form>

<a name="redirect-routes"></a>
### 리다이렉트 라우트

다른 URI로 리다이렉트 시키는 라우트를 정의하려면, `Route::redirect` 메서드를 사용할 수 있습니다. 이 메서드는 간단한 리다이렉트를 위해서 복잡한 라우트나 컨트롤러 전체를 정의하지 않아도 되는 편리한 방법을 제공합니다.

    Route::redirect('/here', '/there');

기본적으로, `Route::redirect` 는 `302` 상태코드를 반환합니다. 만약 다른 상태코드를 반환하도록 커스터마이징 하고자 한다면, 세번째 인자를 옵션으로 전달하십시오.

    Route::redirect('/here', '/there', 301);

또는 `Route::permanentRedirect` 메서드를 사용하여 `301` 상태 코드를 반환 할 수 있습니다.

    Route::permanentRedirect('/here', '/there');

> {note} 리다이렉트 라우트에서 라우트 파라미터를 사용할 때에는 사용할 수 없는 매개변수명이 있습니다. 라라벨에 의해서 예약된 `destination` 그리고 `status` 라는 이름을 사용할 수 없습니다.

<a name="view-routes"></a>
### 뷰-View 라우트

단지 [뷰](/docs/{{version}}/views)를 반환하기만 하는 라우트가 필요하다면, `Route::view` 메서드를 사용할 수 있습니다. `redirect` 메서드와 같이 이 메서드는 간단한 리다이렉트를 위해서 복잡한 라우트나 컨트롤러 전체를 정의하지 않아도 되는 편리한 방법을 제공합니다. `view` 메서드는 첫번째 인자로 URI를, 두번째 인자로 뷰 파일의 이름을 전달 받습니다. 또한 이에 더해, 세번째 인자로 view 에 제공할 데이터들의 배열을 제공할 수도 있습니다.

    Route::view('/welcome', 'welcome');

    Route::view('/welcome', 'welcome', ['name' => 'Taylor']);

> {note} 뷰-View 라우트에서 라우트 파라미터를 사용할 떼에는 사용할 수 없는 매개변수명이 있습니다. 라라벨에 의해서 예약된 `view`, `data`, `status`, `headers` 라는 이름은 사용할 수 없습니다.

<a name="route-parameters"></a>
## 라우트 파라미터

<a name="required-parameters"></a>
### 필수 파라미터

라우트중에 URI 세그먼트를 필요로 할 수도 있습니다. 다음과 같이 URL 에서 사용자의 ID를 확인하고자 하는 경우 입니다. 이 경우 라우트 파라미터를 정의할 수 있습니다.

    Route::get('/user/{id}', function ($id) {
        return 'User '.$id;
    });

라우트에서는 여러개의 라우트 파라미터를 정의할 수도 있습니다.

    Route::get('/posts/{post}/comments/{comment}', function ($postId, $commentId) {
        //
    });

라우트 파라미터는 항상 "{}"(중괄호)로 싸여져 있고, `-` 문자를 포함하지 않은 알파벳 문자로 구성되어 있어야합니다. 언더스코어 (`_`)는 라우트 파라미터 이름으로 사용가능합니다. 라우트 파라미터는 순서에 따라서 라우트 콜백 / 컨트롤러에 주입됩니다. 라우트 콜백 / 컨트롤러 인자는 관계 없습니다. 

<a name="parameters-and-dependency-injection"></a>
#### 파라미터와 의존성 주입

라우트에 라라벨의 서비스 컨테이너가 주입해주는 의존성이 존재하는 경우, 의존 객체를 뒤에 라우트 파라미터를 나열해야합니다.

    use Illuminate\Http\Request;

    Route::get('/user/{id}', function (Request $request, $id) {
        return 'User '.$id;
    });

<a name="parameters-optional-parameters"></a>
### 선택적 파라미터

때로는, 라우트 파라미터를 지정하긴 하지만, 파라미터가 선택적으로 존재하기를 원할수도 있습니다. 이 경우 파라미터 이름뒤에 `?` 를 표시하면 됩니다. 라우트 파라미터와 일치하는 변수가 기본값을 가지는지 확인하십시오.

    Route::get('/user/{name?}', function ($name = null) {
        return $name;
    });

    Route::get('/user/{name?}', function ($name = 'John') {
        return $name;
    });

<a name="parameters-regular-expression-constraints"></a>
### 정규표현식 제약

라우트 인스턴스에 `where` 메서드를 사용하여 라우트 파라미터들의 포맷을 제한할 수 있습니다. `where` 메서드는 파라미터의 이름과 파라미터가 어떻게 규정되어야 하는지 나타내는 정규표현식을 인자로 전달 받습니다.

    Route::get('/user/{name}', function ($name) {
        //
    })->where('name', '[A-Za-z]+');

    Route::get('/user/{id}', function ($id) {
        //
    })->where('id', '[0-9]+');

    Route::get('/user/{id}/{name}', function ($id, $name) {
        //
    })->where(['id' => '[0-9]+', 'name' => '[a-z]+']);

보다 편리한 방법으로, 자주 사용되는 정규표현식 패턴을 라우트 파라미터 제약조건으로 추가할 수 있는 헬퍼 메서드가 존재합니다.

    Route::get('/user/{id}/{name}', function ($id, $name) {
        //
    })->whereNumber('id')->whereAlpha('name');

    Route::get('/user/{name}', function ($name) {
        //
    })->whereAlphaNumeric('name');

    Route::get('/user/{id}', function ($id) {
        //
    })->whereUuid('id');

유입된 요청과 라우트 패턴 조건이 일치하지 않으면 404 HTTP 응답이 반환됩니다. 

<a name="parameters-global-constraints"></a>
#### 글로벌 제약

라우트 파라미터가 항상 주어진 정규표현식으로 제약을 가지게 된다면, `pattern` 메서드를 사용할 수 있습니다. 이 패턴들은 `App\Providers\RouteServiceProvider` 클래스의 `boot` 메서드 안에서 사용해야 합니다.

    /**
     * Define your route model bindings, pattern filters, etc.
     *
     * @return void
     */
    public function boot()
    {
        Route::pattern('id', '[0-9]+');

        parent::boot();
    }

패턴을 한번 정의하고나면, 해당 파라미터 이름을 사용하는 모든 라우트들에 자동으로 적용됩니다.

    Route::get('/user/{id}', function ($id) {
        // Only executed if {id} is numeric...
    });

<a name="parameters-encoded-forward-slashes"></a>
#### 인코딩 된 슬래시

라라벨 라우팅 컴포넌트는 라우트 파라미터값에 `/`를 제외한 모든 문자를 허용합니다. `where` 조건 정규식을 사용하여 `/`를 명시적으로 플레이스홀더의 일부로 허용해야합니다.

    Route::get('/search/{search}', function ($search) {
        return $search;
    })->where('search', '.*');

> {note} 인코딩 된 슬래시는 마지막 경로 세그먼트 내에서만 지원됩니다.

<a name="named-routes"></a>
## 이름이 지정된 라우트

이름이 지정된 라우트는 URL의 생성이나 지정된 라우트로의 리다이렉션을 편리하게 해줍니다. 라우트 정의에 `name` 메서드를 체이닝 하여 라우트에 이름을 지정할 수 있습니다.

    Route::get('/user/profile', function () {
        //
    })->name('profile');

컨트롤러의 액션에도 라우트 이름을 지정할 수 있습니다.

    Route::get(
        '/user/profile',
        [UserProfileController::class, 'show']
    )->name('profile');

> {note} 라우트 이름은 언제나 고유해야 합니다.

<a name="generating-urls-to-named-routes"></a>
#### 이름이 지정된 라우트들에 대한 URL 생성하기

주어진 라우트에 대한 이름이 할당되면, 라라벨의 `route` 와 `redirect` 헬퍼함수를 통해서 URL 또는 리다이렉션을 생성할 때 라우트 이름을 사용할 수 있습니다.

    // Generating URLs...
    $url = route('profile');

    // Generating Redirects...
    return redirect()->route('profile');

이름이 지정된 라우트에 파라미터를 정의하였다면, `route` 메서드의 두번째 인자로 파라미터를 전달할 수 있습니다. 주어진 파라미터는 자동으로 올바른 위치에 있는 URL 에 삽입될 것입니다.

    Route::get('/user/{id}/profile', function ($id) {
        //
    })->name('profile');

    $url = route('profile', ['id' => 1]);

배열에 추가 파라메터를 전달하면 해당하는 키/값의 쌍이 생성된 URL의 쿼리 문자열에 자동으로 추가됩니다.

    Route::get('/user/{id}/profile', function ($id) {
        //
    })->name('profile');

    $url = route('profile', ['id' => 1, 'photos' => 'yes']);

    // /user/1/profile?photos=yes

> {tip} 때때로, 현재 로케일과 같은 URL 파라메터에 대한 요청 전체 기본값을 지정할 수 있습니다. 이를 위해 [`URL::defaults` 메서드](/docs/{{version}}/urls#default-values)을 사용할 수 있습니다.

<a name="inspecting-the-current-route"></a>
#### 현재의 라우트 검사하기

현재의 request 가 주어진 이름의 라우트가 맞는지 확인하고자 한다면, Route 인스턴스의 `named` 메서드를 사용하면 됩니다. 예를 들어 라우트 미들웨어에서 현재 라우트의 이름을 확인할 수 있습니다.

    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        if ($request->route()->named('profile')) {
            //
        }

        return $next($request);
    }

<a name="route-groups"></a>
## 라우트 그룹

라우트 그룹을 사용하면 미들웨어와 같은 라우트 속성을 공유할 수 있어, 많은 수의 라우트를 등록할 때 각각의 개별 라우트에 매번 속성들을 정의하지 않아도 되게 해줍니다.

중첩 된 그룹은 속성을 상위 그룹과 지능적으로 "병합"합니다. 미들웨어와 이름 및 접두사가 추가되는 동안 `where` 조건이 병합됩니다. 적절한 경우 URI 접두사의 네임 스페이스 구분 기호와 슬래시가 자동으로 추가됩니다.

<a name="route-group-middleware"></a>
### 미들웨어

그룹 안의 모든 라우트에 [미들웨어](/docs/{{version}}/middleware)를 할당하기 위해서는, 그룹을 정의하기 전에 `middleware` 메서드를 사용하면 됩니다. 미들웨어는 배열에 나열된 순서대로 실행될 것입니다.

    Route::middleware(['first', 'second'])->group(function () {
        Route::get('/', function () {
            // Uses first & second middleware...
        });

        Route::get('/user/profile', function () {
            // Uses first & second middleware...
        });
    });

<a name="route-group-subdomain-routing"></a>
### 서브 도메인 라우팅

라우트 그룹은 또한 카드형태의 서브 도메인을 처리하는데 사용할 수도 있습니다. 서브 도메인은 라우트 URI와 같이 서브 도메인의 일부를 추출하여, 라우트 파라미터로 할당할 수 있습니다. 서브 도메인은 그룹을 정의하기 전에 `domain` 메서드를 호출하여 지정할 수 있습니다.

    Route::domain('{account}.example.com')->group(function () {
        Route::get('user/{id}', function ($account, $id) {
            //
        });
    });

> {note} 서브 도메인 라우트가 동작하도록하려면 루트 도메인 라우트를 등록하기 전에 서브 도메인 라우트를 등록해야합니다. 이렇게하면 루트 도메인 라우트가 동일한 URI 라우트를 가진 서브 도메인 라우트를 덮어 쓰지 않습니다.

<a name="route-group-prefixes"></a>
### 라우트 Prefix

`prefix` 메서드는 그룹안의 라우트에 특정 URI을 접두어로 지정할 때 사용합니다. 그룹의 모든 라우트 URI 앞에 `admin` 을 붙이고 싶다면 다음과 같이 지정하면 됩니다.

    Route::prefix('admin')->group(function () {
        Route::get('/users', function () {
            // Matches The "/admin/users" URL
        });
    });

<a name="route-group-name-prefixes"></a>
### 라우트 이름 접두사

`name` 메서드는 주어진 문자열을 가진 그룹 내의 각각의 라우트 이름에 접두사를 붙이는데 사용됩니다. 예를 들어, 그룹화 된 라우트들 전부에 `admin` 접두사를 붙일 수 있습니다. 전달된 문자열 그대로 라우트 이름 앞에 붙기 때문에, 접두사는 뒤에 `.` 문자를 포함하여 전달해야하는 것을 잊지말아야 합니다.

    Route::name('admin.')->group(function () {
        Route::get('/users', function () {
            // Route assigned name "admin.users"...
        })->name('users');
    });

<a name="route-model-binding"></a>
## 라우트 모델 바인딩

모델 ID 를 라우트나 컨트롤러 액션에 주입하는 경우, 여러분은 이 ID와 일치하는 모델을 데이터베이스에서 조회하기 위해서 쿼리를 수행할 것입니다. 라라벨 라우트 모델 바인딩은 여러분의 라우트에 자동으로 모델 인스턴스를 직접 주입하는 편리한 방법을 제공합니다. 예를 들어 사용자의 ID를 주입하는 대신 주어진 ID와 매칭되는 전체 `User` 모델 인스턴스를 주입할 수 있습니다.

<a name="implicit-binding"></a>
### 묵시적 바인딩

라라벨은 자동으로 라우트나 컨트롤러 액션에서 정의되어 있는 라우트 세그먼트 이름과 일치하는 타입힌트된 변수에 해당하는 Eloquent 모델을 의존성 해결합니다. 예를 들어:

    use App\Models\User;

    Route::get('/users/{user}', function (User $user) {
        return $user->email;
    });

`App\Models\User` Eloquent 모델로 타입힌트된 `$user` 변수와 `{user}` 세그먼트가 일치하기 때문에, 라라벨은 자동으로 request URI 로 부터 일치하는 ID 값을 가진 모델 인스턴스를 주입할것입니다. 만약 데이터베이스에서 매칭되는 모델 인스턴스를 찾을 수 없으면, 자동으로 404 HTTP response 가 생성됩니다.

물론 컨트롤러 메서드를 사용할 때에도 묵시적 바인딩을 사용할 수 있습니다. 한번 더 강조하지만 `{user}` URI 세그먼트는 `App\Models\User` 타입힌트된 컨트롤러 `$user` 변수와 매칭됩니다.  

    use App\Http\Controllers\UserController;
    use App\Models\User;

    // Route definition...
    Route::get('/users/{user}', [UserController::class, 'show']);

    // Controller method definition...
    public function show(User $user)
    {
        return view('user.profile', ['user' => $user]);
    }

<a name="implicit-soft-deleted-models"></a>
#### 소프트 삭제 모델

일반적으로 북시적 모델 바인딩은 [소프트 삭제](/docs/{{version}}/eloquent#soft-deleting)된 모델은 찾을 수 없습니다. 그렇지만 라우트를 정의할 때 `withTrashed` 메서드를 연결하면 묵시적 모델을 조회할 때 소프트 삭제된 모델도 찾을 수 있습니다.

    use App\Models\User;

    Route::get('/users/{user}', function (User $user) {
        return $user->email;
    })->withTrashed();

<a name="customizing-the-key"></a>
<a name="customizing-the-default-key-name"></a>
#### 키 커스터마이징

엘로퀜트 모델들을 의존성을 `id` 키 대신 다른 컬럼을 사용하여 해결하기 원한다면, 라우트 파라미터 정의 안에 그 컬럼을 명시할 수 있습니다.

    use App\Models\Post;

    Route::get('/posts/{post:slug}', function (Post $post) {
        return $post;
    });

모델을 찾기 위해서 모델바인딩에서 사용하는 `id` 이외의 컬럼을 사용하려면, 엘로퀀트 모델의 `getRouteKeyName` 메서드를 오버라이드 하면 됩니다.

    /**
     * Get the route key for the model.
     *
     * @return string
     */
    public function getRouteKeyName()
    {
        return 'slug';
    }

<a name="implicit-model-binding-scoping"></a>
#### 커스텀 키 & 스코프-scope

하나의 라우트를 정의하면서 여러개의 엘로퀀트 모델을 묵시적 바인딩 할 때, 두 번째 엘로퀀트 모델이 앞에 있는 엘로퀀트 모델의 자식모델이도록 범위를 지정할 수 있습니다. 예를 들어 특정 사용자에대한 슬러그-slug 별로 블로그 게시물을 검색하는 라우트를 정의할 때는 다음과 같이 할 수 있습니다.  

    use App\Models\Post;
    use App\Models\User;

    Route::get('/users/{user}/posts/{post:slug}', function (User $user, Post $post) {
        return $post;
    });

커스텀 키로 묵시적 바인딩을 중첩된 라우트 매개 변수로 사용하는 경우, 라라벨은 부모의 관계 이름을 추측하는 규칙을 사용하여 부모에 의해 중첩된 모델을 검색하도록 쿼리의 범위를 자동으로 지정합니다. 이 경우, `User` 모델은 `post` 모델을 검색하는 데 사용할 수있는 `posts` (경로 매개 변수 이름의 복수형)라는 관계가 있다고 가정합니다.

원한다면 커스텀 키가 제공되지 않은 경우에도 "자식" 바인딩의 범위(scope)를 지정할 수 있습니다. 이렇게 하기 위해서는 라우트를 정의할 때 `scopeBindings` 메서드를 호출하면 됩니다.

    use App\Models\Post;
    use App\Models\User;

    Route::get('/users/{user}/posts/{post}', function (User $user, Post $post) {
        return $post;
    })->scopeBindings();

또는 라우트 정의를 하나의 그룹으로 묶어 스코프 바인딩을 추가할 수 있습니다.

    Route::scopeBindings()->group(function () {
        Route::get('/users/{user}/posts/{post}', function (User $user, Post $post) {
            return $post;
        });
    });

<a name="customizing-missing-model-behavior"></a>
#### 모델을 찾을 수 없는 경우의 동작 커스터마이징 하기

일반적으로 묵시적 모델 바인딩에서 모델을 찾을 수 없는 경우에는 404 HTTP 응답이 반환됩니다. 그렇지만 필요한경우 라우트에 `missing` 메서드를 호출하여 이 동작을 커스터마이징 할 수 있습니다. `missing` 메서드는 묵시적 모델 바인딩이 인스턴스를 찾을 수 없는 경우 이 클로저를 호출합니다.

    use App\Http\Controllers\LocationsController;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Redirect;

    Route::get('/locations/{location:slug}', [LocationsController::class, 'show'])
            ->name('locations.view')
            ->missing(function (Request $request) {
                return Redirect::route('locations.index');
            });


<a name="explicit-binding"></a>
### 명시적 바인딩

모델 바인딩을 사용할 때 꼭 라라벨의 묵시적 바인딩을 사용해야할 필요는 없습니다. 라우트 파라미터가 대응하는 모델을 명시적으로 정의할 수도 있습니다. 명시적 바인딩을 사용하려면 라우터의 `model` 메서드를 사용하여 주어진 파라미터에 대한 클래스를 지정하면 됩니다. `RouteServiceProvider` 클래스의 `boot` 메서드의 시작 부분에서 명시적 모델 바인딩을 정의하면 됩니다. 

    use App\Models\User;
    use Illuminate\Support\Facades\Route;

    /**
     * Define your route model bindings, pattern filters, etc.
     *
     * @return void
     */
    public function boot()
    {
        Route::model('user', User::class);

        // ...
    }

다음으로, `{user}` 파라미터를 포함한 라우트를 정의합니다.

    use App\Models\User;

    Route::get('/users/{user}', function (User $user) {
        //
    });

`{user}` 파라미터와 `App\Models\User` 모델이 바인딩되어 있기 때문에 라우트에는 클래스 인스턴스가 주입 될것입니다. 예를 들어 `users/1`으로 요청이 들어오면 데이터베이스로 부터 ID가 `1`인 `User`의 인스턴스가 주입됩니다.

만약 데이터베이스에서 일치하는 모델 인스턴스를 찾지 못하는 경우, 404 응답이 자동으로 생성될 것입니다.

#### 의존성 해결 로직 커스터마이징하기

만약 의존성 해결 로직을 커스터마이징 하려면 `Route:bind` 메서드를 사용할 수 있습니다. `bind` 메서드에 전달되는 클로저는 URI 세그먼트 값을 수신하고 라우트에 삽입되어야 하는 클래스의 인스턴스를 반환해야 합니다. 다시한번 이야기 하지만 이 커스터마이징은 `RouteServiceProvider` 의 `boot` 메서드에서 정의해야합니다.

    use App\Models\User;
    use Illuminate\Support\Facades\Route;

    /**
     * Define your route model bindings, pattern filters, etc.
     *
     * @return void
     */
    public function boot()
    {
        Route::bind('user', function ($value) {
            return User::where('name', $value)->firstOrFail();
        });

        // ...
    }

또는 Eloquent 모델에서 `resolveRouteBinding` 메서드를 오버라이드 할 수 있습니다. 이 메서드는 URI 세그먼트의 값을 받고 라우트에 삽입되어야하는 클래스의 인스턴스를 리턴해야 합니다.

    /**
     * Retrieve the model for a bound value.
     *
     * @param  mixed  $value
     * @param  string|null  $field
     * @return \Illuminate\Database\Eloquent\Model|null
     */
    public function resolveRouteBinding($value, $field = null)
    {
        return $this->where('name', $value)->firstOrFail();
    }

라우트가 [묵시적 바인딩 스코핑](#implicit-model-binding-scoping)을 사용하는 경우 `resolveChildRouteBinding` 메서드를 사용하여 상위 모델의 자식 바인딩을 해결할 수 있습니다.

    /**
     * Retrieve the child model for a bound value.
     *
     * @param  string  $childType
     * @param  mixed  $value
     * @param  string|null  $field
     * @return \Illuminate\Database\Eloquent\Model|null
     */
    public function resolveChildRouteBinding($childType, $value, $field)
    {
        return parent::resolveChildRouteBinding($childType, $value, $field);
    }

<a name="fallback-routes"></a>
## 대체 라우트

`Route::fallback` 메서드를 사용하면 들어오는 요청과 일치하는 라우트가 없을 때 실행 할 라우트를 정의 할 수 있습니다. 일반적으로 처리하지 못한 요청은 애플리케이션의 exception 핸들러를 통해 자동으로 "404" 페이지를 렌더링합니다. 그러나 `routes/web.php` 파일에서 `fallback` 라우트를 정의 할 경우 `web` 미들웨어 그룹의 모든 미들웨어가 라우트에 적용됩니다. 필요할 경우 얼마든지 이 라우트에 미들웨어를 추가 할 수 있습니다.

    Route::fallback(function () {
        //
    });

> {note} 대체 라우트는 항상 애플리케이션에서 등록한 마지막 라우트 여야합니다.

<a name="rate-limiting"></a>
## 접속 속도 제한

<a name="defining-rate-limiters"></a>
### 접속 속도 리미터 정의하기

라라벨에는 주어진 라우트 또는 라우트 그룹에 대한 트래픽 Rate를 제한하는 강력하고 쉽게 커스터마이징이 가능한 접속 속도 제한 기능(Rate Limit)을 제공합니다. 이 기능을 사용하려면 먼저 애플리케이션의 요구사항을 정의한 사용량 제한 설정을 정의해야합니다. 일반적으로 이 설정은 `App\Providers\RouteServiceProvider` 클래스의 `configureRateLimiting` 메서드에서 정의합니다.

접속 속도 리미터는 `RateLimiter` 파사드의 `for` 메서드를 사용하여 정의합니다. `for` 메서드는 Rate limit 을 정의하는 이름과 제한설정을 담고 있는 클로저를 인자로 전달받는다. 제한에 대한 설정은 `Illuminate\Cache\RateLimiting\Limit` 인스턴스를 사용합니다. 이 클래스는 제한을 설정할 수 있도록 "builder" 메서드를 가지고 있습니다. 접속 속도 리미터의 이름은 원하는 문자열로 지정할 수 있습니다.   

    use Illuminate\Cache\RateLimiting\Limit;
    use Illuminate\Support\Facades\RateLimiter;

    /**
     * Configure the rate limiters for the application.
     *
     * @return void
     */
    protected function configureRateLimiting()
    {
        RateLimiter::for('global', function (Request $request) {
            return Limit::perMinute(1000);
        });
    }

유입된 요청이 제한을 초과하면 자동으로 라라벨에서는 429 Http 상태 코드가 지정된 응답을 반환합니다. 접속 속도 제한이 넘었을 때 반환하는 응답을 커스터마이징하려면 `response` 메서드를 사용하면 됩니다. 

    RateLimiter::for('global', function (Request $request) {
        return Limit::perMinute(1000)->response(function () {
            return response('Custom response...', 429);
        });
    });

접속 속도 리미터의 콜백 함수에서는 유입되는 HTTP 요청 인스턴스를 인자로 전달 받습니다. 따라서 인증된 사용자를 기반으로 적절하게 접속 속도를 제한하도록 동적으로 처리할 수 있습니다.

    RateLimiter::for('uploads', function (Request $request) {
        return $request->user()->vipCustomer()
                    ? Limit::none()
                    : Limit::perMinute(100);
    });

<a name="segmenting-rate-limits"></a>
#### 접속 속도 제한 분할적용

경우에 따라서는 전혀 다른 값을 기준으로 속도 제한을 구분하여 지정할 수도 있습니다. 예를 들어 사용자별로 IP 주소당 주어진 경로에 분당 100번씩 접근을 허용할 수 있습니다. 이 경우 `by` 메서드를 사용하면 됩니다.  

    RateLimiter::for('uploads', function (Request $request) {
        return $request->user()->vipCustomer()
                    ? Limit::none()
                    : Limit::perMinute(100)->by($request->ip());
    });

또 다른 예시로는 인증된 사용자의 경우에는 분당 100회, 인증되지 않은 방문자의 경우에는 분당 10회로 제한을 지정할 수 있습니다. 

    RateLimiter::for('uploads', function (Request $request) {
        return $request->user()
                    ? Limit::perMinute(100)->by($request->user()->id)
                    : Limit::perMinute(10)->by($request->ip());
    });

<a name="multiple-rate-limits"></a>
#### 다중 접속 속도 제한 - (속도제한 중첩)

필요한 경우, 속도 제한지정의 반환값을 리미터의 배열 지정할 수 있습니다. 이 경우 배열의 순서대로 라우트에 대한 제한을 확인합니다.

    RateLimiter::for('login', function (Request $request) {
        return [
            Limit::perMinute(500),
            Limit::perMinute(3)->by($request->input('email')),
        ];
    });

<a name="attaching-rate-limiters-to-routes"></a>
### 접속 속도 리미터를 라우트에 연결하기

속도 제한 리미터는 `throttle` [미들웨어](/docs/{{version}}/middleware)를 사용하여 라우트나 라우트 그룹에 연결할 수 있습니다. 스로틀-throttle 미들웨어는 라우트에 연결하는 속도 리미터의 이름을 인자로 전달받습니다.

    Route::middleware(['throttle:uploads'])->group(function () {
        Route::post('/audio', function () {
            //
        });

        Route::post('/video', function () {
            //
        });
    });

<a name="throttling-with-redis"></a>
#### Redis를 사용하여 속도 제한 조절하기

일반적으로 `throttle` 미들웨어는 `Illuminate\Routing\Middleware\ThrottleRequests` 클래스에 매핑되어 있습니다. 이 매핑은 HTTP 커널(`App\Http\Kernel`)에 정의되어 있습니다. 그렇지만 Redis 를 애플리케이션의 캐시 드라이버로 사용하는 경우 이 매핑을 변경할 수 있습니다. 다음과 같이 Redis 를 사용하여 속도 제한을 조절하는 것이 보다 효율적입니다.  

    'throttle' => \Illuminate\Routing\Middleware\ThrottleRequestsWithRedis::class,

<a name="form-method-spoofing"></a>
## Form-폼 메서드 Spoofing-스푸핑

HTML form은 `PUT`, `PATCH` 와 `DELETE` 액션을 지원하지 않습니다. 따라서 `PUT`, `PATCH` 나 `DELETE` 로 지정된 라우트를 호출하는 HTML form을 정의한다면 `_method` 의 숨겨진 필드를 지정해야합니다. `_method` 필드로 보내진 값은 HTTP request 메서드를 판별하는데 사용됩니다.

    <form action="/example" method="POST">
        <input type="hidden" name="_method" value="PUT">
        <input type="hidden" name="_token" value="{{ csrf_token() }}">
    </form>

편의를 위해서 `@method` [블레이드 지시어](/docs/{{version}}/blade)를 사용하여 `_method` 인풋 필드를 생성할 수 있습니다.

    <form action="/example" method="POST">
        @method('PUT')
        @csrf
    </form>

<a name="accessing-the-current-route"></a>
## 현재 라우트에 엑세스하기

`Route` 파사드의 `current`, `currentRouteName` 그리고 `currentRouteAction` 메서드를 사용하여, 유입된 request-요청을 처리하는 라우트에 대한 정보에 엑세스 할 수 있습니다.

    use Illuminate\Support\Facades\Route;

    $route = Route::current(); // Illuminate\Routing\Route
    $name = Route::currentRouteName(); // string
    $action = Route::currentRouteAction(); // string

라우터와 라우트 클래스에서 사용할 수 있는 모든 메서드를 확인하고자 한다면 [Route 파사드 뒤에서 동작하는 클래스](https://laravel.com/api/{{version}}/Illuminate/Routing/Router.html) 와 [Route 인스턴스](https://laravel.com/api/{{version}}/Illuminate/Routing/Route.html) API 문서를 참고하십시오.

<a name="cors"></a>
## 크로스-오리진 리소스 쉐어링 (CORS)

라라벨은 사용자가 지정한 값으로 CORS `OPTION` HTTP 요청에 자동으로 응답할 수 있습니다. 모든 CORS 설정은 애플리케이션의 `config/cors.php` 파일에서 설정할 수 있습니다. `OPTION` 요청은 글로벌 미들웨어에 포함되어 있는 `HandleCors` [미들웨어](/docs/{{version}}/middleware)에 의해서 자동으로 처리됩니다. 글로벌 미들웨어는 애플리케이션의 HTTP 커널(`App\Http\Kernel`) 에서 확인할 수 있습니다.

> {tip} CORS 및 CORS 헤더에 대한 자세한 내용은 [MDN web documentation on CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS#The_HTTP_response_headers) 를 참조하십시오.

<a name="route-caching"></a>
## 라우트 캐싱

프로덕션에서 애플리케이션을 배포할 때에는 라라벨의 라우트 캐싱 기능을 사용해야합니다. 라우트 캐싱을 사용하면 애플리케이션에서 모든 라우트를 등록하는 시간이 크게 줄어듭니다. 라우트 캐시를 생성하려면 `route:cache` 아티즌 명령어를 실행하면 됩니다.

    php artisan route:cache

이 명령어를 실행한 뒤에는, 모든 요청을 처리할 때 캐시된 라우트 파일이 로딩됩니다. 새로운 라우트를 추가하는 경우 이 캐시를 재생성해야합니다. 이런 이유로 `route:cache` 명령어는 프로젝트를 배포하는 과정에서만 사용해야 합니다.

다음과 같이 `route:clear` 명령어를 사용하면 라우트 캐시를 제거할 수 있습니다.

    php artisan route:clear
