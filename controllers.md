# 컨트롤러

- [시작하기](#introduction)
- [기본적인 컨트롤서](#basic-controllers)
    - [컨트롤러 정의](#defining-controllers)
    - [컨트롤러 & 네임스페이스](#controllers-and-namespaces)
    - [단일 동작 컨트롤러](#single-action-controllers)
- [컨트롤러 미들웨어](#controller-middleware)
- [리소스 컨트롤러](#resource-controllers)
    - [Resource 라우트의 일부만 지정하기](#restful-partial-resource-routes)
    - [중첩된 Resources](#restful-nested-resources)
    - [리소스 라우트 이름 지정하기](#restful-naming-resource-routes)
    - [리소스 라우트 파라미터 이름 지정하기](#restful-naming-resource-route-parameters)
    - [리소스 URI의 지역화(다국어 동사처리)](#restful-localizing-resource-uris)
    - [Resource 컨트롤러 라우트에 추가하기](#restful-supplementing-resource-controllers)
- [의존성 주입 & 컨트롤러](#dependency-injection-and-controllers)
- [라우트 캐싱](#route-caching)

<a name="introduction"></a>
## 시작하기

애플리케이션의 요청에 대한 모든 처리 로직을 하나의 `routes.php` 파일에 정의하는 것 보다 별도의 컨트롤러 클래스를 통해서 구성할 수도 있습니다. 컨트롤러는 클래스를 구성하여 HTTP 요청에 대한 그룹을 지정합니다. 컨트롤러는 `app/Http/Controllers` 디렉토리에 저장 됩니다.

<a name="basic-controllers"></a>
## 기본적인 컨트롤러

<a name="defining-controllers"></a>
### 컨트롤러 정의

아래는 기본 컨트롤러 클래스의 예입니다. 컨트롤러는 Laravel에 포함 된 기본 컨트롤러 클래스들을 확장합니다. 기본 클래스는 미들웨어를 컨트롤러 액션에 연결하는 데 사용할 수 있는 `middleware`메소드와 같은 몇 가지 편리한 메소드를 제공합니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\User;

    class UserController extends Controller
    {
        /**
         * Show the profile for the given user.
         *
         * @param  int  $id
         * @return View
         */
        public function show($id)
        {
            return view('user.profile', ['user' => User::findOrFail($id)]);
        }
    }

여러분은 다음과 같이 컨트롤러의 액션에 라우트를 지정할 수 있습니다.

    Route::get('user/{id}', 'UserController@show');

이제 사용자의 요청이 지정된 라우트의 URI와 일치할 때 `UserController` 클래스의 `show` 메소드가 실행될것입니다. 이때, 라우트의 파라미터들 또한 메소드에 전달될 것입니다.

> {tip} 컨트롤러는 기본 클래스를 확장하기 위해 **필수**가 아닙니다. 그러나 `middleware`, `validate`, `dispatch` 함수와 같은 편리한 기능을 사용할 수는 없습니다.

<a name="controllers-and-namespaces"></a>
### 컨트롤러 & 네임스페이스

컨트롤러에 대응하는 라우트를 정의 할 때 전체 컨트롤러의 전체 네임 스페이스를 지정할 필요가 없다는 점에 유의해야합니다. `RouteServiceProvider`는 네임 스페이스를 포함하는 라우트 그룹 내에서 라우트 파일을 로드하기 때문에 네임 스페이스의 `App\Http\Controllers` 부분의 뒤에 오는 클래스 이름부분만 지정했습니다.

컨트롤러를 `App\Http\Controllers` 디렉토리내에 위치시키려면 `App\Http\Controllers` 루트 네임 스페이스와 관련된 특정 클래스 이름을 사용하기 만하면됩니다. 따라서 만약 컨트롤러가 `App\Http\Controllers\Photos\AdminController` 처럼 구성되어 있다면 다음처럼 라우트를 구성하면 됩니다.

    Route::get('foo', 'Photos\AdminController@method');

<a name="single-action-controllers"></a>
### 단일 동작 컨트롤러

단일 액션만을 처리하는 컨트롤러를 정의하고 싶다면 컨트롤러에 하나의`__invoke` 메소드를 넣을 수 있습니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\User;

    class ShowProfile extends Controller
    {
        /**
         * Show the profile for the given user.
         *
         * @param  int  $id
         * @return View
         */
        public function __invoke($id)
        {
            return view('user.profile', ['user' => User::findOrFail($id)]);
        }
    }

단일 액션 컨트롤러에 대한 경로를 등록 할 때 함수를 지정할 필요가 없습니다.

    Route::get('user/{id}', 'ShowProfile');

Artisan 커맨드 `make:controller` 에 `--invokable` 옵션을 사용하여 호출 가능한 컨트롤러를 생성 할 수 있습니다.

    php artisan make:controller ShowProfile --invokable

<a name="controller-middleware"></a>
## 컨트롤러 미들웨어

[미들웨어](/docs/{{version}}/middleware)는 다음과 같이 컨트롤러 라우트에 지정할 수 있습니다.

    Route::get('profile', 'UserController@show')->middleware('auth');

하지만 보다 편리한 방법은 컨트롤러의 생성자에서 미들웨어를 지정하는 것입니다. 컨트롤러의 생성자에서 `middleware` 메소드를 사용하여 여러분은 손쉽게 컨트롤러에서 사용할 미들웨어를 지정할 수 있습니다. 컨트롤러의 몇몇 메소드에서만 제한하여 미들웨어를 지정할 수도 있습니다.

    class UserController extends Controller
    {
        /**
         * Instantiate a new controller instance.
         *
         * @return void
         */
        public function __construct()
        {
            $this->middleware('auth');

            $this->middleware('log')->only('index');

            $this->middleware('subscribed')->except('store');
        }
    }

컨트롤러를 사용하면 Closure를 사용하여 미들웨어를 등록 할 수 있습니다. 이는 전체 미들웨어 클래스를 정의하지 않고 단일 컨트롤러에 대한 미들웨어를 정의하는 편리한 방법을 제공합니다.

    $this->middleware(function ($request, $next) {
        // ...

        return $next($request);
    });

> {tip} 컨트롤러 액션의 하위 집합에 미들웨어를 할당 할 수 있습니다. 그러나 컨트롤러가 너무 커질 수 있음을 인지하여야 합니다. 대신 컨트롤러를 여러 개의 작은 컨트롤러로 나누는 것을 고려하세요.

<a name="resource-controllers"></a>
## 리소스 컨트롤러

Laravel 리소스 라우팅은 일반적인 "CRUD" 경로를 한 줄의 코드로 컨트롤러에 할당합니다. 예를 들어, 애플리케이션에서 저장 한 "사진"에 대한 모든 HTTP 요청을 처리하는 컨트롤러를 만들 수 있습니다. `make:controller` Artisan 명령을 사용하여, 우리는 그러한 컨트롤러를 빠르게 만들 수 있습니다.

    php artisan make:controller PhotoController --resource

아티즌 명령어는 `app/Http/Controllers/PhotoController.php` 파일을 생성할 것입니다. 이 컨트롤러는 각각의 resource 에 해당하는 메소드들을 가지고 있을 것입니다.

이제 생성된 컨트롤러에 resourceful 라우트를 등록하면 됩니다.

    Route::resource('photos', 'PhotoController');

한번의 선언만으로 photo 를 구성하는 RESTful 한 액션에 대한 다양한 라우트를 설정할 수 있습니다. 앞에서 직접 개별 메소드를 구성한것과 마찬가지로 생성된 컨트롤러는 각각의 메소드가 처리하는 URI와 액션에 대한 메모와 함께 구성됩니다.

`resources` 메소드에 배열을 전달하여 한번에 여러개의 리소스 컨트롤러를 등록할 수 있습니다.

    Route::resources([
        'photos' => 'PhotoController',
        'posts' => 'PostController'
    ]);

#### 리소스풀 컨트롤러에 의해서 구성된 액션들

Verb      | URI                  | Action       | Route Name
----------|-----------------------|--------------|---------------------
GET       | `/photos`              | index        | photos.index
GET       | `/photos/create`       | create       | photos.create
POST      | `/photos`              | store        | photos.store
GET       | `/photos/{photo}`      | show         | photos.show
GET       | `/photos/{photo}/edit` | edit         | photos.edit
PUT/PATCH | `/photos/{photo}`      | update       | photos.update

#### 리소스 모델 지정하기

라우트 모델 바인딩을 사용하고 있고, 리소스 컨트롤러의 메소드가 모델 인스턴스에 대한 타입힌트를 하도록 원한다면 컨트롤러를 생성할 대 `--model` 옵션을 사용할 수 있습니다.

    php artisan make:controller PhotoController --resource --model=Photo

#### 스푸핑 폼 함수

HTML 폼은 `PUT`, `PATCH` 또는 `DELETE` 요청을 만들 수 없으므로 숨겨진 `_method` 필드를 추가하여 이들 HTTP 문법을 인용해야합니다. `@method` 블레이드 디렉티브로 필드를 생성 할 수 있습니다.

    <form action="/foo/bar" method="POST">
        @method('PUT')
    </form>

<a name="restful-partial-resource-routes"></a>
### Resource 라우트의 일부만 지정하기

resource 라우트를 선언할 때, 액션의 일부만을 지정할 수도 있습니다.

    Route::resource('photos', 'PhotoController')->only([
        'index', 'show'
    ]);

    Route::resource('photos', 'PhotoController')->except([
        'create', 'store', 'update', 'destroy'
    ]);

#### API 리소스 라우트

API에서 사용할 리소스 라우트를 선언하는 경우, 일반적으로 `create`, `edit`와 같은 HTML 템플릿을 표시하는 라우트는 제외하기를 원합니다. 편의를 위해서 `apiResource`를 사용하면 이 두가지의 라우트를 제외할 수 있습니다.

    Route::apiResource('photos', 'PhotoController');

`apiResources` 메소드에 배열형태의 API 리소스 컨트롤러를 전달하여 여러개를 한번에 등록할 수 있습니다.

    Route::apiResources([
        'photos' => 'PhotoController',
        'posts' => 'PostController'
    ]);

빠르게 `create` 혹은 `edit` 메소드들을 포함하지 않는 API 리소스 컨트롤러 생성을 원하신다면, `make:controller` 커맨드 명령에 `--api` 옵션을 사용하시면 됩니다.

    php artisan make:controller API/PhotoController --api

<a name="restful-nested-resources"></a>
### 중첩된 Resources

때때로 "중첩 된" 리소스에 대한 라우트를 정의해야 할 수도 있습니다. 예를 들어, 사진 리소스는 사진에 첨부 될 수있는 다수의 "코멘트"를 가질 수 있습니다. 리소스 컨트롤러를 "중첩"하려면 경로 선언에서 "점-dot"표기법을 사용하십시오.

    Route::resource('photos.comments', 'PhotoCommentController');

이 라우트는 photos/{photos}/comments/{comments}와 같은 URL로 액세스 할 수있는 "중첩 된" 리소스를 등록합니다.

<a name="restful-naming-resource-routes"></a>
### 리소스 라우트 이름 지정하기

기본적으로 모든 리소스 컨트롤러 액션은 라우트 이름을 가지고 있습니다. 그러나 `names` 옵션 배열을 전달하여 이름을 덮어씌울 수 있습니다.

    Route::resource('photos', 'PhotoController')->names([
        'create' => 'photos.build'
    ]);

<a name="restful-naming-resource-route-parameters"></a>
### 리소스 라우트 파리미터 이름 지정하기

기본적으로 `Route::resource` 는 리소스 라우트들을 위한 리소스 이름을 "단일화된" 버전을 기반으로 라우트 파라미터들을 생성합니다. 사용자는 각각의 리소스마다 `parameters` 메소드를 사용하여 손쉽게 이를 덮어쓸 수 있습니다. `parameters` 메소드로 전달 된 배열은 리소스의 이름과 파라미터 이름의 연관 배열이어야합니다.

    Route::resource('users', 'AdminUserController')->parameters([
        'users' => 'admin_user'
    ]);

 The example above generates the following URIs for the resource's `show` route:

 위의 예제는 리소스의 `show` 라우트에서 다음의 URI를 생성합니다.

    /users/{admin_user}

<a name="restful-localizing-resource-uris"></a>
### 리소스 URI의 지역화(다국어 동사처리)

기본적으로 `Route::resource` 는 영어 동사형태로 된 리소스 URI를 구성합니다. 만약 `create`와 `edit` 액션 동사를 지역화 하고자 한다면, `Route::resourceVerbs` 메소드를 사용하면 됩니다. 이 작업은 `AppServiceProvider` 파일의 `boot` 메소드에서 수행해야 합니다.

    use Illuminate\Support\Facades\Route;

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        Route::resourceVerbs([
            'create' => 'crear',
            'edit' => 'editar',
        ]);
    }

액션 동사를 지역화되도록 설정하고 나면, `Route::resource('fotos', 'PhotoController')`와 같은  리소스 라우트는 다음의 URI를 구성하게 됩니다.

    /fotos/crear

    /fotos/{foto}/editar

<a name="restful-supplementing-resource-controllers"></a>
### Resource 컨트롤러 라우트에 추가하기

만약 리소스 컨트롤러에 추가적으로 라우팅을 구성해야할 필요가 있다면 `Route::resource`가 호출되기 전에 등록해야합니다. 그렇지 않으면 `resource` 메소드에 의해서 정의된 라우트들이 추가한 라우트들 보다 우선하게 되어 버립니다.

    Route::get('photos/popular', 'PhotoController@method');

    Route::resource('photos', 'PhotoController');

> {tip} 컨트롤러를 집중 관리하는 것을 잊지 마십시오. 일반적인 리소스 행동 세트 이외의 방법을 빈번하게 필요로하는 경우 컨트롤러를 두 개의 작은 컨트롤러로 분할하는 것을 고려하십시오.

<a name="dependency-injection-and-controllers"></a>
## 의존성 주입 & 컨트롤러

#### 생성자 주입

라라벨의 [서비스 컨테이너](/docs/{{version}}/container)는 모든 라라벨 컨트롤러의 의존성을 해결하기 위해서 사용됩니다. 그 결과 컨트롤러가 필요로 하는 의존 객체들에 대해서 생성자에서 타입힌트로 지정할 수 있게 됩니다. 의존성은 자동으로 해결되어 컨트롤러 인스턴스에 주입됩니다.

    <?php

    namespace App\Http\Controllers;

    use App\Repositories\UserRepository;

    class UserController extends Controller
    {
        /**
         * The user repository instance.
         */
        protected $users;

        /**
         * Create a new controller instance.
         *
         * @param  UserRepository  $users
         * @return void
         */
        public function __construct(UserRepository $users)
        {
            $this->users = $users;
        }
    }

[라라벨 contract](/docs/{{version}}/contracts)의 형태도 타입 힌트로 지정할 수 있습니다. 컨테이너가 의존성 해결을 할 수 있다면 타입 힌트에 지정할 수는 있습니다.

#### 메소드 주입

생성자 주입과 더불어 컨트롤러의 액션 메소드에서도 타입힌트를 통한 의존성 주입을 할 수 있습니다. 예를 들어 메소드에서 `Illuminate\Http\Request` 인스턴스를 타입힌트를 통해서 주입할 수 있습니다.

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
            $name = $request->name;

            //
        }
    }

컨트롤러 메소드가 라우트 인자로 부터 입력값을 받아야 한다면 간단하게 라우트 인자를 지정하면 됩니다. 예를 들어 다음과 같이 정의할 수 있습니다.

    Route::put('user/{id}', 'UserController@update');

아래와 같이 `Illuminate\Http\Request` 를 타입힌트 하면서, 컨트롤러 메소드에서 정의하고있는 `id`에 해당하는 라우트 파라미터에 액세스 할 수도 있습니다.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;

    class UserController extends Controller
    {
        /**
         * Update the given user.
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

<a name="route-caching"></a>
## 라우트 캐시

> {note} 라우트 캐시는 클로저를 기반으로한 라우트에서는 동작하지 않습니다. 라우트 캐시를 사용하기 위해서는 모든 클로저 기반의 라우트를 컨트롤러를 사용하도록 변경해야 합니다.

애플리케이션이 컨트롤러 기반의 라우트만을 사용하고 있다면 라라벨의 라우트를 캐시하는 장점을 사용해야 합니다. 라우트 캐시를 사용하면 애플리케이션의 전체 라우트를 등록하는 데 걸리는 시간의 양을 크게 감소합니다. 경우에 따라서는 라우트 등록이 100배나 빨라질 수도 있습니다! 라우트 캐시를 생성하기 위해서는 `route:cache` 아티즌 명령어를 실행하면 됩니다.

    php artisan route:cache

이 명령을 실행하면 캐시 된 라우트 파일이 모든 요청에 로드됩니다. 새로운 라우트를 추가하는 경우 새로운 라우트 캐시를 생성해야합니다. 이 때문에 프로젝트 배포 중에 `route:cache` 명령 만 실행하면 됩니다.

캐시를 재생성하는것 말고 캐시를 제거하기 위해서는 `route:clear` 명령어를 실행하면 됩니다. 캐시를 재생성하는것 말고 캐시를 제거하기 위해서는 `route:clear` 명령어를 실행하면 됩니다.

    php artisan route:clear
