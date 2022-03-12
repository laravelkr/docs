# 컨트롤러

- [시작하기](#introduction)
- [컨트롤러 작성하기](#writing-controllers)
    - [기본 컨트롤러](#basic-controllers)
    - [단일 액션 컨트롤러](#single-action-controllers)
- [컨트롤러 미들웨어](#controller-middleware)
- [리소스 컨트롤러](#resource-controllers)
    - [Resource 라우트의 일부만 지정하기](#restful-partial-resource-routes)
    - [중첩된 Resources](#restful-nested-resources)
    - [리소스 라우트 이름 지정하기](#restful-naming-resource-routes)
    - [리소스 라우트 파라미터 이름 지정하기](#restful-naming-resource-route-parameters)
    - [리소스 라우트 범위 지정](#restful-scoping-resource-routes)
    - [리소스 URI의 지역화(다국어 동사처리)](#restful-localizing-resource-uris)
    - [Resource 컨트롤러 라우트에 추가하기](#restful-supplementing-resource-controllers)
- [의존성 주입 & 컨트롤러](#dependency-injection-and-controllers)

<a name="introduction"></a>
## 시작하기

리퀘스트 처리에 관한 논리는 라우트 파일에서 클로저로 정의 할 수 있습니다. 클로저로 정의하는 대신 "컨트롤러" 클래스를 사용하여 같은 동작을 설정할 수 있습니다. 예를 들어 `UserController` 클래스는 사용자 표시(showing), 생성(creating), 업데이트(updating) 및 삭제(deleting)를 포함하여 사용자와 관련된 모든 수신 요청을 처리할 수 있습니다. 기본적으로 컨트롤러 클래스 파일은 `app/Http/Controllers` 디렉토리에 저장됩니다.

<a name="writing-controllers"></a>
## 컨트롤러 작성하기

<a name="basic-controllers"></a>
### 기본 컨트롤러

기본 컨트롤러의 예를 살펴 봅시다. 컨트롤러는 Laravel에 포함된 기본 컨트롤러 클래스를 확장합니다. 기본 컨트롤러 클래스는 `App\Http\Controllers\Controller` 경로에 위치합니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Models\User;

    class UserController extends Controller
    {
        /**
         * Show the profile for a given user.
         *
         * @param  int  $id
         * @return \Illuminate\View\View
         */
        public function show($id)
        {
            return view('user.profile', [
                'user' => User::findOrFail($id)
            ]);
        }
    }

다음과 같이 위 컨트롤러 메서드에 대한 경로를 정의할 수 있습니다.

    use App\Http\Controllers\UserController;

    Route::get('/user/{id}', [UserController::class, 'show']);

들어오는 요청(request)이 지정된 라우트(route)의 URI와 일치하면 `App\Http\Controllers\UserController` 클래스의 `show` 메소드가 호출되고 라우트(route)의 파라메터가 `show` 메소드에 전달됩니다.

> {tip} 컨트롤러는 기본 컨트롤러 클래스를 **필수**로 상속 받지 않아도 작동합니다. 하지만 기본 컨트롤러 클래스를 상속하지 않는다면 `미들웨어(middleware)` 및 `권한 부여(authorize)` 메서드와 같은 편리한 기능을 사용하기 위한 접근을 할 수 없습니다.

<a name="single-action-controllers"></a>
### 단일 액션 컨트롤러

컨트롤러 작업이 특히 복잡한 경우, 하나의 컨트롤러 클래스 전체를 단일 액션으로 설정하는 것이 편리할 수 있습니다. 이를 수행하기 위해 컨트롤러 내에서 단일 `__invoke` 메서드를 정의할 수 있습니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Models\User;

    class ProvisionServer extends Controller
    {
        /**
         * Provision a new web server.
         *
         * @return \Illuminate\Http\Response
         */
        public function __invoke()
        {
            // ...
        }
    }

단일 액션 컨트롤러를 라우트에 등록할 때는 라우트에 컨트롤러의 메소드를 지정할 필요가 없습니다. 컨트롤러의 이름을 라우터에 전달하기만 하면 됩니다.

    use App\Http\Controllers\ShowProfile;

    Route::get('user/{id}', ShowProfile::class);

Artisan 컨멘드를 사용하면 `make:controller` 에 `--invokable` 옵션을 사용하여 `__invoke` 함수가 내장된 컨트롤러(invokable controller)를 생성 할 수 있습니다.

    php artisan make:controller ProvisionServer --invokable

> {tip} [stub publishing](/docs/{{version}}/artisan#stub-customization)를 사용하여 controller stub을 사용자가 정의할 수 있습니다.

<a name="controller-middleware"></a>
## 컨트롤러 미들웨어

[미들웨어](/docs/{{version}}/middleware)는 다음과 같이 컨트롤러 라우트에 지정할 수 있습니다.

    Route::get('profile', [UserController::class, 'show'])->middleware('auth');

또는 컨트롤러 클래스의 생성자 내에서 미들웨어를 지정하는 것이 편리할 수 있습니다. 컨트롤러의 생성자 내에서 `middleware` 메서드를 사용하여 컨트롤러의 엑션에 미들웨어를 할당할 수 있습니다.

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

컨트롤러를 사용하면 Closure를 사용하여 미들웨어를 등록 할 수 있습니다. 이는 미들웨어 클래스를 따로 정의하지 않고 단일 컨트롤러에 대한 미들웨어를 Closure로 정의하는 편리한 방법을 제공합니다.

    $this->middleware(function ($request, $next) {
        return $next($request);
    });


<a name="resource-controllers"></a>
## 리소스 컨트롤러

애플리케이션의 각 Eloquent 모델을 "리소스"로 생각한다면 애플리케이션의 각 리소스에 대해 동일 유형(typical)의 같은 작업을 수행하는 것이 일반적입니다. 예를 들어 애플리케이션에 `Photo` 모델과 `Movie` 모델이 포함되어 있다고 가정해 보겠습니다. 사용자는 Photo 리소스에 대해서도 Movie 리소스에 대해서도 동일하게 생성, 조회, 업데이트 또는 삭제하는 동일한 유형의 작업을 합니다.

이와 같은 일반적인 사용 사례(use case) 때문에 라라벨 리소스 라우팅은 동일 유형의 생성, 읽기, 업데이트 및 삭제("CRUD")를 여러줄의 라우터로 각각 선언하지 않고 한 줄의 코드로 컨트롤러의 기본 엑션들을 라우트에 할당하는 방법을 제공합니다. `make:controller` Artisan 명령의 `--resource` 옵션을 사용하여 모델에 대한 생성, 읽기, 업데이트 및 삭제를 처리하는 컨트롤러를 빠르게 생성할 수 있습니다.

    php artisan make:controller PhotoController --resource

Artisan 명령어는 `app/Http/Controllers/PhotoController.php` 파일을 생성합니다. 생성된 컨트롤러는 리소스의 이용 가능한 동작(operations)에 해당하는 엑션 메소드를 포함합니다.

    use App\Http\Controllers\PhotoController;

    Route::resource('photos', PhotoController::class);

단일한 라우트의 선언으로 리소스에 대한 다양한 엑션을 다루기 위한 라우터를 생성할 수 있습니다. 생성된 컨트롤러에는 미리 생성된 코드(stub)를 가진 엑션 메소드를 가집니다. `route:list` Artisan 명령어를 실행하여 여러분 애플리케이션의 라우트에 대한 개략적인 코드(overview)를 빠르게 얻을 수 있다는 것을 알아 두세요.

`resources` 메소드에 배열을 전달하여 한번에 여러개의 리소스 컨트롤러를 등록할 수 있습니다.

    Route::resources([
        'photos' => PhotoController::class,
        'posts' => PostController::class,
    ]);

<a name="actions-handled-by-resource-controller"></a>
#### 리소스풀 컨트롤러에 의해서 설정된 액션들

Verb      | URI                    | Action       | Route Name
----------|------------------------|--------------|---------------------
GET       | `/photos`              | index        | photos.index
GET       | `/photos/create`       | create       | photos.create
POST      | `/photos`              | store        | photos.store
GET       | `/photos/{photo}`      | show         | photos.show
GET       | `/photos/{photo}/edit` | edit         | photos.edit
PUT/PATCH | `/photos/{photo}`      | update       | photos.update
DELETE    | `/photos/{photo}`      | destroy      | photos.destroy

<a name="customizing-missing-model-behavior"></a>
#### 누락된 모델 동작 사용자 지정

일반적으로 바인딩된 리소스 모델을 찾을 수 없는 경우 404 HTTP 응답을 라라벨은 생성하도록 구현되어 있습니다. 리소스 라우트를 정의할 때 `missing` 메서드를 호출하여 자동으로 404 HTTP응답 생성 동작을 커스터마이징할 수 있습니다. `missing` 메소드는 리소스 경로에 대해 내부에 바인딩된 모델을 찾을 수 없는 경우 호출되는 클로저를 사용합니다.

    use App\Http\Controllers\PhotoController;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Redirect;

    Route::resource('photos', PhotoController::class)
            ->missing(function (Request $request) {
                return Redirect::route('photos.index');
            });


<a name="specifying-the-resource-model"></a>
#### 리소스 모델 지정하기

[라우트 모델 바인딩](/docs/{{version}}/routing#route-model-binding)을 사용하고 있고, 리소스 컨트롤러의 메소드가 모델 인스턴스에 대한 타입힌트를 하도록 원한다면 컨트롤러를 생성할 때 `--model` 옵션을 사용할 수 있습니다.

    php artisan make:controller PhotoController --resource --model=Photo

<a name="restful-partial-resource-routes"></a>
### Resource 라우트의 일부만 지정하기

리소스 라우트를 선언할 때, 컨트롤러가 다루고 있는 엑션의 전체(full set)를 사용하도록 하는 것 대신 일부(subset)만 사용하도록 지정할 수 있습니다. 

    use App\Http\Controllers\PhotoController;

    Route::resource('photos', PhotoController::class)->only([
        'index', 'show'
    ]);

    Route::resource('photos', PhotoController::class)->except([
        'create', 'store', 'update', 'destroy'
    ]);

<a name="api-resource-routes"></a>
#### API 리소스 라우트

API에서 사용할 리소스 경로를 선언할 때 일반적으로 `create` 및 `edit`와 같은 HTML 템플릿을 표시하는 경로를 제외하고 싶을 것입니다. 편의를 위해 `apiResource` 메소드를 사용하여 다음 두 경로를 자동으로 제외할 수 있습니다.

    use App\Http\Controllers\PhotoController;

    Route::apiResource('photos', PhotoController::class);

배열을 `apiResources` 메소드에 전달하여 한 번에 많은 API 리소스 컨트롤러를 등록할 수 있습니다.

    use App\Http\Controllers\PhotoController;
    use App\Http\Controllers\PostController;

    Route::apiResources([
        'photos' => PhotoController::class,
        'posts' => PostController::class,
    ]);

`create` 또는 `edit` 메소드가 포함되지 않은 API 리소스 컨트롤러를 빠르게 생성하려면 `make:controller` 명령을 실행할 때 `--api` 옵션(switch)을 사용하세요.

    php artisan make:controller API/PhotoController --api

<a name="restful-nested-resources"></a>
### 중첩된(Nested) Resources

때로는 엘로퀀트 모델의 중첩된(Nested) 리소스에 대한 라우트를 정의해야 할 수도 있습니다. 예를 들어, 게제할 사진 정보를 저장하는 사진 리소스는 사진에 관한 여러 글(comments)을 가질 수 있습니다. 사진 리소스를 사용하는 리소스 컨트롤러에 사진에 관한 글(comments)을 중첩(nest)하려면 라우트를 선언할 때 엘로퀀트 모델간의 연결 관계를 "dot" 기호를 사용해서 나타냅니다.

    use App\Http\Controllers\PhotoCommentController;

    Route::resource('photos.comments', PhotoCommentController::class);

이 라우트는 다음과 같은 URI로 접근 할 수있는 중첩 된 리소스를 등록합니다.

    /photos/{photo}/comments/{comment}

<a name="scoping-nested-resources"></a>
### 중첩(Nested) 리소스 범위(Scoping) 지정

라라벨의 [암시적 모델 바인딩](/docs/{{version}}/routing#implicit-model-binding-scoping) 기능(feature)은 컨트롤러에서 사용할(resolved) 모델을 자식 모델이라고 할 때 부모 모델에 속해 있는지 (belong to 관계인지) 확인하는 기능을 제공합니다. 중첩된 리소스를 정의할 때 `scoped` 메서드를 사용하면 자동 범위 지정을 활성화할 수 있을 뿐만 아니라 자식 리소스가 회수(retrieved) 해야 할 필드를 라라벨에 지시할 수 있습니다. 이를 수행하는 방법에 대한 자세한 내용은 [자원 경로 범위 지정](#restful-scoping-resource-routes)에 대한 설명서를 읽어 보세요.

    Route::resource('photos.comments', PhotoCommentController::class)->scoped([
        'comment' => 'slug',
    ]);

<a name="shallow-nesting"></a>
#### 얕은 중첩 (Shallow Nesting)

자식 ID는 이미 고유 식별자이므로 URI 내에 부모 ID와 자식 ID를 모두 가질 필요는 없습니다. URI 단위(segments)에서 모델을 식별하기 위해 기본키의 auto-incrementing 과 같은 고유 식별자를 사용하는 경우 "얕은 중첩"을 사용하도록 선택할 수 있습니다.

    use App\Http\Controllers\CommentController;

    Route::resource('photos.comments', CommentController::class)->shallow();

위의 라우트 정의는 다음 라우트를 정의합니다.

Verb      | URI                               | Action       | Route Name
----------|-----------------------------------|--------------|---------------------
GET       | `/photos/{photo}/comments`        | index        | photos.comments.index
GET       | `/photos/{photo}/comments/create` | create       | photos.comments.create
POST      | `/photos/{photo}/comments`        | store        | photos.comments.store
GET       | `/comments/{comment}`             | show         | comments.show
GET       | `/comments/{comment}/edit`        | edit         | comments.edit
PUT/PATCH | `/comments/{comment}`             | update       | comments.update
DELETE    | `/comments/{comment}`             | destroy      | comments.destroy

<a name="restful-naming-resource-routes"></a>
### 리소스 라우트 이름 지정하기

기본적으로 모든 리소스 컨트롤러 액션은 라우트 이름을 가지고 있습니다. 그러나 `names` 옵션 배열을 전달하여 라우트 이름을 덮어씌울 수 있습니다.

    Route::resource('photos', PhotoController::class)->names([
        'create' => 'photos.build'
    ]);

<a name="restful-naming-resource-route-parameters"></a>
### 리소스 라우트 파리미터 이름 지정하기

기본적으로 `Route::resource`가 라우트 파라미터들을 생성할 때는 리소스 이름의 "단일화된(singularized)" 버전을 기반으로 생성합니다. 사용자는 각각의 리소스마다 `parameters` 메소드를 사용하여 손쉽게 이를 덮어쓸 수 있습니다. `parameters` 메소드로 전달 된 배열은 리소스의 이름과 파라미터 이름의 연관 배열이어야합니다.

    use App\Http\Controllers\AdminUserController;

    Route::resource('users', AdminUserController::class)->parameters([
        'users' => 'admin_user'
    ]);

 The example above generates the following URIs for the resource's `show` route:

 위의 예제는 리소스의 `show` 라우트에서 다음의 URI를 생성합니다.

    /users/{admin_user}

<a name="restful-scoping-resource-routes"></a>
### 리소스 라우트 스코프 지정

Laravel의 [범위가 지정된 암시적 모델 바인딩](/docs/{{version}}/routing#implicit-model-binding-scoping) 기능(feature)은 컨트롤러에서 사용할(resolved) 모델을 자식 모델이라고 할 때 부모 모델에 속해 있는지 (belong to 관계인지) 확인하는 기능을 제공합니다. 중첩된 리소스를 정의할 때 `scoped` 메서드를 사용하면 자동 범위 지정을 활성화할 수 있을 뿐만 아니라 자식 리소스가 회수(retrieved) 해야 할 필드를 라라벨에 지시할 수 있습니다.

    use App\Http\Controllers\PhotoCommentController;

    Route::resource('photos.comments', PhotoCommentController::class)->scoped([
        'comment' => 'slug',
    ]);

이 라우트는 다음과 같은 URI로 액세스할 수 있는 범위가 지정된 중첩 리소스를 등록합니다.

    /photos/{photo}/comments/{comment:slug}

중첩된 라우트의 파라메터로 커스텀 키가 있는 암시적(implicit) 바인딩을 사용할 때, Laravel은 자동으로 쿼리의 스코프를 지정하여 부모의 관계 이름을 추축하는 규칙을 사용하여 부모 모델에 중첩되어 있는 모델인지 확인합니다. 이 경우 부모 모델인 `Photo`는 자식 모델인 `Comment`를 검색하는 데 사용할 수있는 `comments`(라우트 파라메터 이름의 복수)라는 엘로퀀트 릴레이션 관계가있는 것으로 가정합니다.

<a name="restful-localizing-resource-uris"></a>
### 리소스 URI의 지역화(다국어 동사처리)

기본적으로 `Route::resource` 는 영어 동사형태로 된 리소스 URI를 설정합니다. 만약 `create`와 `edit` 액션 동사를 지역화 하고자 한다면, `Route::resourceVerbs` 메소드를 사용하면 됩니다. 이 작업은 `AppServiceProvider` 파일의 `boot` 메소드에서 수행해야 합니다.

    /**
     * Define your route model bindings, pattern filters, etc.
     *
     * @return void
     */
    public function boot()
    {
        Route::resourceVerbs([
            'create' => 'crear',
            'edit' => 'editar',
        ]);

        // ...
    }

액션 동사를 지역화되도록 설정하고 나면, `Route::resource('fotos', 'PhotoController')`와 같은 리소스 라우트는 다음의 URI를 설정하게 됩니다.

    /fotos/crear

    /fotos/{foto}/editar

<a name="restful-supplementing-resource-controllers"></a>
### Resource 컨트롤러 라우트에 추가하기

만약 리소스 컨트롤러에 추가적으로 라우팅을 설정해야할 필요가 있다면 `Route::resource`가 호출되기 전에 추가 엑션을 지정하는 라우트를 등록해야합니다. 그렇지 않으면 `resource` 메소드에 의해서 정의된 라우트들이 추가한 라우트들 보다 우선하게 되어 버립니다.

    use App\Http\Controller\PhotoController;

    Route::get('/photos/popular', [PhotoController::class, 'popular']);
    Route::resource('photos', PhotoController::class);

> {tip} 컨트롤러에 포커스를 맞춰야 한다는 것을 기억하세요. 기본 유형(the typical set)의 리소스 엑션 세트 이외의 빈번하게 사용할 엑션이 필요한 경우 컨트롤러를 두 개의 컨트롤러로 분할하는 것, 컨트롤러를 작게 만드는 것을 고려하세요.

<a name="dependency-injection-and-controllers"></a>
## 의존성 주입 & 컨트롤러

<a name="constructor-injection"></a>
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

<a name="method-injection"></a>
#### 메소드 인젝션

생성자 주입 외에도 컨트롤러의 메소드에 대한 유형 힌트 종속성을 사용할 수도 있습니다. 메소드 주입의 일반적인 사용 사례는 `Illuminate\Http\Request` 인스턴스를 컨트롤러 메소드에 주입하는 것입니다.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;

    class UserController extends Controller
    {
        /**
         * Store a new user.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return \Illuminate\Http\Response
         */
        public function store(Request $request)
        {
            $name = $request->name;

            //
        }
    }

컨트롤러의 메소드가 라우트 파라메터의 전달도 고려해야 하는 경우, 의존성 주입 파라메터 다음에 라우터 파라메터를 나열합니다. 예를 들어 경로가 다음과 같이 정의된 경우

    use App\Http\Controllers\UserController;

    Route::put('/user/{id}', [UserController::class, 'update']);

다음과 같은 코드를 통해 동일하게 타입힌트로 지정된 `Illuminate\Http\Request` 의존성 주입 대상과 컨트롤러 메소드에 정의된 `id` 파라메터에 접근할 수 있습니다.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;

    class UserController extends Controller
    {
        /**
         * Update the given user.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  string  $id
         * @return \Illuminate\Http\Response
         */
        public function update(Request $request, $id)
        {
            //
        }
    }
