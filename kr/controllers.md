# Controllers
# 컨트롤러

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Basic Controllers](#basic-controllers)
- [기본적인 컨트롤서](#basic-controllers)
    - [Defining Controllers](#defining-controllers)
    - [컨트롤러 정의](#defining-controllers)
    - [Single Action Controllers](#single-action-controllers)
    - [단일 동작 컨트롤러](#single-action-controllers)
- [Controller Middleware](#controller-middleware)
- [컨트롤러 미들웨어](#controller-middleware)
- [Resource Controllers](#resource-controllers)
- [리소스 컨트롤러](#resource-controllers)
    - [Partial Resource Routes](#restful-partial-resource-routes)
    - [Resource 라우트의 일부만 지정하기](#restful-partial-resource-routes)
    - [Nested Resources](#restful-nested-resources)
    - [중첩된 Resources](#restful-nested-resources)
    - [Naming Resource Routes](#restful-naming-resource-routes)
    - [리소스 라우트 이름 지정하기](#restful-naming-resource-routes)
    - [Naming Resource Route Parameters](#restful-naming-resource-route-parameters)
    - [리소스 라우트 파라미터 이름 지정하기](#restful-naming-resource-route-parameters)
    - [Scoping Resource Routes](#restful-scoping-resource-routes)
    - [리소스 라우트 범위 지정](#restful-scoping-resource-routes)
    - [Localizing Resource URIs](#restful-localizing-resource-uris)
    - [리소스 URI의 지역화(다국어 동사처리)](#restful-localizing-resource-uris)
    - [Supplementing Resource Controllers](#restful-supplementing-resource-controllers)
    - [Resource 컨트롤러 라우트에 추가하기](#restful-supplementing-resource-controllers)
- [Dependency Injection & Controllers](#dependency-injection-and-controllers)
- [의존성 주입 & 컨트롤러](#dependency-injection-and-controllers)
- [Route Caching](#route-caching)
- [라우트 캐싱](#route-caching)

<a name="introduction"></a>
## Introduction
## 시작하기

Instead of defining all of your request handling logic as Closures in route files, you may wish to organize this behavior using Controller classes. Controllers can group related request handling logic into a single class. Controllers are stored in the `app/Http/Controllers` directory.

애플리케이션의 요청에 대한 모든 처리 로직을 하나의 `routes.php` 파일에 정의하는 것 보다 별도의 컨트롤러 클래스를 통해서 구성할 수도 있습니다. 컨트롤러는 클래스를 구성하여 HTTP 요청에 대한 그룹을 지정합니다. 컨트롤러는 `app/Http/Controllers` 디렉토리에 저장 됩니다.

<a name="basic-controllers"></a>
## Basic Controllers
## 기본적인 컨트롤러

<a name="defining-controllers"></a>
### Defining Controllers
### 컨트롤러 정의

Below is an example of a basic controller class. Note that the controller extends the base controller class included with Laravel. The base class provides a few convenience methods such as the `middleware` method, which may be used to attach middleware to controller actions:

아래는 기본 컨트롤러 클래스의 예입니다. 컨트롤러는 Laravel에 포함 된 기본 컨트롤러 클래스들을 확장합니다. 기본 클래스는 미들웨어를 컨트롤러 액션에 연결하는 데 사용할 수 있는 `middleware`메소드와 같은 몇 가지 편리한 메소드를 제공합니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Models\User;

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

You can define a route to this controller action like so:

여러분은 다음과 같이 컨트롤러의 액션에 라우트를 지정할 수 있습니다.

    use App\Http\Controllers\UserController;

    Route::get('user/{id}', [UserController::class, 'show']);

Now, when a request matches the specified route URI, the `show` method on the `UserController` class will be executed. The route parameters will also be passed to the method.

이제 사용자의 요청이 지정된 라우트의 URI와 일치할 때 `UserController` 클래스의 `show` 메소드가 실행될것입니다. 이때, 라우트의 파라미터들 또한 메소드에 전달될 것입니다.

> {tip} Controllers are not **required** to extend a base class. However, you will not have access to convenience features such as the `middleware`, `validate`, and `dispatch` methods.

> {tip} 컨트롤러는 기본 클래스를 확장하기 위해 **필수**가 아닙니다. 그러나 `middleware`, `validate`, `dispatch` 함수와 같은 편리한 기능을 사용할 수는 없습니다.

<a name="single-action-controllers"></a>
### Single Action Controllers
### 단일 동작 컨트롤러

If you would like to define a controller that only handles a single action, you may place a single `__invoke` method on the controller:

단일 액션만을 처리하는 컨트롤러를 정의하고 싶다면 컨트롤러에 하나의 `__invoke` 메소드를 넣을 수 있습니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Models\User;

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

When registering routes for single action controllers, you do not need to specify a method:

단일 액션 컨트롤러에 대한 경로를 등록 할 때 함수를 지정할 필요가 없습니다.

    use App\Http\Controllers\ShowProfile;

    Route::get('user/{id}', ShowProfile::class);

You may generate an invokable controller by using the `--invokable` option of the `make:controller` Artisan command:

Artisan 커맨드 `make:controller` 에 `--invokable` 옵션을 사용하여 호출 가능한 컨트롤러를 생성 할 수 있습니다.

    php artisan make:controller ShowProfile --invokable

> {tip} Controller stubs may be customized using [stub publishing](/docs/{{version}}/artisan#stub-customization)

>> {tip} [stub publishing](/docs/{{version}}/artisan#stub-customization)를 사용하여 controller stub을 사용자가 정의할 수 있습니다.

<a name="controller-middleware"></a>
## Controller Middleware
## 컨트롤러 미들웨어

[Middleware](/docs/{{version}}/middleware) may be assigned to the controller's routes in your route files:

[미들웨어](/docs/{{version}}/middleware)는 다음과 같이 컨트롤러 라우트에 지정할 수 있습니다.

    Route::get('profile', [UserController::class, 'show'])->middleware('auth');

However, it is more convenient to specify middleware within your controller's constructor. Using the `middleware` method from your controller's constructor, you may easily assign middleware to the controller's action. You may even restrict the middleware to only certain methods on the controller class:

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

Controllers also allow you to register middleware using a Closure. This provides a convenient way to define a middleware for a single controller without defining an entire middleware class:

컨트롤러를 사용하면 Closure를 사용하여 미들웨어를 등록 할 수 있습니다. 이는 전체 미들웨어 클래스를 정의하지 않고 단일 컨트롤러에 대한 미들웨어를 정의하는 편리한 방법을 제공합니다.

    $this->middleware(function ($request, $next) {
        // ...

        return $next($request);
    });

> {tip} You may assign middleware to a subset of controller actions; however, it may indicate your controller is growing too large. Instead, consider breaking your controller into multiple, smaller controllers.

> {tip} 컨트롤러 액션의 하위 집합에 미들웨어를 할당 할 수 있습니다. 그러나 컨트롤러가 너무 커질 수 있음을 인지하여야 합니다. 대신 컨트롤러를 여러 개의 작은 컨트롤러로 나누는 것을 고려하세요.

<a name="resource-controllers"></a>
## Resource Controllers
## 리소스 컨트롤러

Laravel resource routing assigns the typical "CRUD" routes to a controller with a single line of code. For example, you may wish to create a controller that handles all HTTP requests for "photos" stored by your application. Using the `make:controller` Artisan command, we can quickly create such a controller:

Laravel 리소스 라우팅은 일반적인 "CRUD" 경로를 한 줄의 코드로 컨트롤러에 할당합니다. 예를 들어, 애플리케이션에서 저장 한 "사진"에 대한 모든 HTTP 요청을 처리하는 컨트롤러를 만들 수 있습니다. `make:controller` Artisan 명령을 사용하여, 우리는 그러한 컨트롤러를 빠르게 만들 수 있습니다.

    php artisan make:controller PhotoController --resource

This command will generate a controller at `app/Http/Controllers/PhotoController.php`. The controller will contain a method for each of the available resource operations.

아티즌 명령어는 `app/Http/Controllers/PhotoController.php` 파일을 생성할 것입니다. 이 컨트롤러는 각각의 resource 에 해당하는 메소드들을 가지고 있을 것입니다.

Next, you may register a resourceful route to the controller:

이제 생성된 컨트롤러에 resourceful 라우트를 등록하면 됩니다.

    Route::resource('photos', PhotoController::class);

This single route declaration creates multiple routes to handle a variety of actions on the resource. The generated controller will already have methods stubbed for each of these actions, including notes informing you of the HTTP verbs and URIs they handle.

한번의 선언만으로 photo 를 구성하는 RESTful 한 액션에 대한 다양한 라우트를 설정할 수 있습니다. 앞에서 직접 개별 메소드를 구성한것과 마찬가지로 생성된 컨트롤러는 각각의 메소드가 처리하는 URI와 액션에 대한 메모와 함께 구성됩니다.

You may register many resource controllers at once by passing an array to the `resources` method:

`resources` 메소드에 배열을 전달하여 한번에 여러개의 리소스 컨트롤러를 등록할 수 있습니다.

    Route::resources([
        'photos' => PhotoController::class,
        'posts' => PostController::class,
    ]);

#### Actions Handled By Resource Controller
#### 리소스풀 컨트롤러에 의해서 구성된 액션들

Verb      | URI                  | Action       | Route Name
----------|-----------------------|--------------|---------------------
GET       | `/photos`              | index        | photos.index
GET       | `/photos/create`       | create       | photos.create
POST      | `/photos`              | store        | photos.store
GET       | `/photos/{photo}`      | show         | photos.show
GET       | `/photos/{photo}/edit` | edit         | photos.edit
PUT/PATCH | `/photos/{photo}`      | update       | photos.update
DELETE    | `/photos/{photo}`      | destroy      | photos.destroy

#### Specifying The Resource Model
#### 리소스 모델 지정하기

If you are using route model binding and would like the resource controller's methods to type-hint a model instance, you may use the `--model` option when generating the controller:

라우트 모델 바인딩을 사용하고 있고, 리소스 컨트롤러의 메소드가 모델 인스턴스에 대한 타입힌트를 하도록 원한다면 컨트롤러를 생성할 대 `--model` 옵션을 사용할 수 있습니다.

    php artisan make:controller PhotoController --resource --model=Photo

<a name="restful-partial-resource-routes"></a>
### Partial Resource Routes
### Resource 라우트의 일부만 지정하기

When declaring a resource route, you may specify a subset of actions the controller should handle instead of the full set of default actions:

resource 라우트를 선언할 때, 액션의 일부만을 지정할 수도 있습니다.

    Route::resource('photos', PhotoController::class)->only([
        'index', 'show'
    ]);

    Route::resource('photos', PhotoController::class)->except([
        'create', 'store', 'update', 'destroy'
    ]);

#### API Resource Routes
#### API 리소스 라우트

When declaring resource routes that will be consumed by APIs, you will commonly want to exclude routes that present HTML templates such as `create` and `edit`. For convenience, you may use the `apiResource` method to automatically exclude these two routes:

API에서 사용할 리소스 라우트를 선언하는 경우, 일반적으로 `create`, `edit`와 같은 HTML 템플릿을 표시하는 라우트는 제외하기를 원합니다. 편의를 위해서 `apiResource`를 사용하면 이 두가지의 라우트를 제외할 수 있습니다.

    Route::apiResource('photos', PhotoController::class);

You may register many API resource controllers at once by passing an array to the `apiResources` method:

`apiResources` 메소드에 배열형태의 API 리소스 컨트롤러를 전달하여 여러개를 한번에 등록할 수 있습니다.

    Route::apiResources([
        'photos' => PhotoController::class,
        'posts' => PostController::class,
    ]);

To quickly generate an API resource controller that does not include the `create` or `edit` methods, use the `--api` switch when executing the `make:controller` command:

빠르게 `create` 혹은 `edit` 메소드들을 포함하지 않는 API 리소스 컨트롤러 생성을 원하신다면, `make:controller` 커맨드 명령에 `--api` 옵션을 사용하시면 됩니다.

    php artisan make:controller API/PhotoController --api

<a name="restful-nested-resources"></a>
### Nested Resources
### 중첩된 Resources

Sometimes you may need to define routes to a nested resource. For example, a photo resource may have multiple comments that may be attached to the photo. To nest the resource controllers, use "dot" notation in your route declaration:

때때로 중첩 된 리소스에 대한 라우트를 정의해야 할 수도 있습니다. 예를 들어, 사진 리소스는 사진에 첨부 될 수있는 다수의 코멘트를 가질 수 있습니다. 리소스 컨트롤러를 중첩하려면 경로 선언에서 "점-dot"표기법을 사용하십시오.

    Route::resource('photos.comments', PhotoCommentController::class);

This route will register a nested resource that may be accessed with URIs like the following:

이 라우트는 다음과 같은 URI로 접근 할 수있는 중첩 된 리소스를 등록합니다.

    /photos/{photo}/comments/{comment}

#### Scoping Nested Resources
### 중첩 리소스 범위 지정

Laravel's [implicit model binding](/docs/{{version}}/routing#implicit-model-binding-scoping) feature can automatically scope nested bindings such that the resolved child model is confirmed to belong to the parent model. By using the `scoped` method when defining your nested resource, you may enabling automatic scoping as well as instruct Laravel which field the child resource should be retrieved by:

Laravel의 [묵시적 모델 바인딩] (/ docs / {{version}} / routing # implicit-model-binding-scoping) 기능은 상위 모델에 속한 해결 된 하위 모델이 확인되도록 중첩 된 바인딩의 범위를 자동으로 지정할 수 있습니다. `scoped` 메서드를 사용해 중첩 된 리소스를 정의 할 때 자동 범위 지정을 활성화 할 수있을뿐만 아니라 Laravel에 하위 리소스를 검색해야하는 필드를 지정할 수 있습니다.
    
    Route::resource('photos.comments', PhotoCommentController::class)->scoped([
        'comment' => 'slug',
    ]);

This route will register a scoped nested resource that may be accessed with URIs like the following:

이 라우트는 다음과 같은 URI로 접근해 중첩된 리소스의 범위를 지정 등록할 수 있습니다. 

    /photos/{photo}/comments/{comment:slug}

#### Shallow Nesting
#### 얕은 중첩

Often, it is not entirely necessary to have both the parent and the child IDs within a URI since the child ID is already a unique identifier. When using unique identifier such as auto-incrementing primary keys to identify your models in URI segments, you may choose to use "shallow nesting":

자식 ID는 이미 고유 식별자이므로 URI 내에 부모 ID와 자식 ID를 모두 가질 필요는 없습니다. URI 단위에서 모델을 식별하기 위해 auto-incrementing 기본 키와 같은 고유 식별자를 사용하는 경우 "얕은 중첩"을 사용하도록 선택할 수 있습니다.

    Route::resource('photos.comments', CommentController::class)->shallow();

The route definition above will define the following routes:

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
### Naming Resource Routes
### 리소스 라우트 이름 지정하기

By default, all resource controller actions have a route name; however, you can override these names by passing a `names` array with your options:

기본적으로 모든 리소스 컨트롤러 액션은 라우트 이름을 가지고 있습니다. 그러나 `names` 옵션 배열을 전달하여 이름을 덮어씌울 수 있습니다.

    Route::resource('photos', PhotoController::class)->names([
        'create' => 'photos.build'
    ]);

<a name="restful-naming-resource-route-parameters"></a>
### Naming Resource Route Parameters
### 리소스 라우트 파리미터 이름 지정하기

By default, `Route::resource` will create the route parameters for your resource routes based on the "singularized" version of the resource name. You can easily override this on a per resource basis by using the `parameters` method. The array passed into the `parameters` method should be an associative array of resource names and parameter names:

기본적으로 `Route::resource` 는 리소스 라우트들을 위한 리소스 이름을 "단일화된" 버전을 기반으로 라우트 파라미터들을 생성합니다. 사용자는 각각의 리소스마다 `parameters` 메소드를 사용하여 손쉽게 이를 덮어쓸 수 있습니다. `parameters` 메소드로 전달 된 배열은 리소스의 이름과 파라미터 이름의 연관 배열이어야합니다.

    Route::resource('users', AdminUserController::class)->parameters([
        'users' => 'admin_user'
    ]);

 The example above generates the following URIs for the resource's `show` route:

 위의 예제는 리소스의 `show` 라우트에서 다음의 URI를 생성합니다.

    /users/{admin_user}

<a name="restful-scoping-resource-routes"></a>
### Scoping Resource Routes
### 리소스 라우트 스코프 지정

Sometimes, when implicitly binding multiple Eloquent models in resource route definitions, you may wish to scope the second Eloquent model such that it must be a child of the first Eloquent model. For example, consider this situation that retrieves a blog post by slug for a specific user:

때로는 리소스 라우트 정의에서 여러 Eloquent 모델을 암시적으로 바인딩 할 때, 두 번째 Eloquent 모델의 스코프를 지정하여 첫 번째 Eloquent 모델의 자식이도록 할 수 있습니다. 예를 들어, 특정 사용자에 대해 슬러그별로 블로그 게시물을 검색하는 다음 상황을 가정해보십시오.

    use App\Http\Controllers\PostsController;

    Route::resource('users.posts', PostsController::class)->scoped();

You may override the default model route keys by passing an array to the `scoped` method:

배열을 `scoped` 메서드에 전달하여 기본 모델 라우트 키를 재정의 할 수 있습니다.

    use App\Http\Controllers\PostsController;

    Route::resource('users.posts', PostsController::class)->scoped([
        'post' => 'slug',
    ]);

When using a custom keyed implicit binding as a nested route parameter, Laravel will automatically scope the query to retrieve the nested model by its parent using conventions to guess the relationship name on the parent. In this case, it will be assumed that the `User` model has a relationship named `posts` (the plural of the route parameter name) which can be used to retrieve the `Post` model.

커스텀 키의 암시적 바인딩을 중첩 라우트 파라메터로 사용할 때 Laravel은 자동으로 쿼리 스코프를 지정하여 부모의 관계 이름을 추측하는 규칙을 사용하여 부모별로 중첩 된 모델을 검색합니다. 이 경우 `User`모델에는 `Post`모델을 검색하는 데 사용할 수있는 `posts`(라우트 매개 변수 이름의 복수)라는 관계가있는 것으로 가정합니다.

<a name="restful-localizing-resource-uris"></a>
### Localizing Resource URIs
### 리소스 URI의 지역화(다국어 동사처리)

By default, `Route::resource` will create resource URIs using English verbs. If you need to localize the `create` and `edit` action verbs, you may use the `Route::resourceVerbs` method. This may be done in the `boot` method of your `AppServiceProvider`:

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

Once the verbs have been customized, a resource route registration such as `Route::resource('fotos', 'PhotoController')` will produce the following URIs:

액션 동사를 지역화되도록 설정하고 나면, `Route::resource('fotos', 'PhotoController')`와 같은  리소스 라우트는 다음의 URI를 구성하게 됩니다.

    /fotos/crear

    /fotos/{foto}/editar

<a name="restful-supplementing-resource-controllers"></a>
### Supplementing Resource Controllers
### Resource 컨트롤러 라우트에 추가하기

If you need to add additional routes to a resource controller beyond the default set of resource routes, you should define those routes before your call to `Route::resource`; otherwise, the routes defined by the `resource` method may unintentionally take precedence over your supplemental routes:

만약 리소스 컨트롤러에 추가적으로 라우팅을 구성해야할 필요가 있다면 `Route::resource`가 호출되기 전에 등록해야합니다. 그렇지 않으면 `resource` 메소드에 의해서 정의된 라우트들이 추가한 라우트들 보다 우선하게 되어 버립니다.

    Route::get('photos/popular', [PhotoController::class, 'popular']);

    Route::resource('photos', PhotoController::class);

> {tip} Remember to keep your controllers focused. If you find yourself routinely needing methods outside of the typical set of resource actions, consider splitting your controller into two, smaller controllers.

> {tip} 컨트롤러를 집중 관리하는 것을 잊지 마십시오. 일반적인 리소스 행동 세트 이외의 방법을 빈번하게 필요로하는 경우 컨트롤러를 두 개의 작은 컨트롤러로 분할하는 것을 고려하십시오.

<a name="dependency-injection-and-controllers"></a>
## Dependency Injection & Controllers
## 의존성 주입 & 컨트롤러

#### Constructor Injection
#### 생성자 주입

The Laravel [service container](/docs/{{version}}/container) is used to resolve all Laravel controllers. As a result, you are able to type-hint any dependencies your controller may need in its constructor. The declared dependencies will automatically be resolved and injected into the controller instance:

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

You may also type-hint any [Laravel contract](/docs/{{version}}/contracts). If the container can resolve it, you can type-hint it. Depending on your application, injecting your dependencies into your controller may provide better testability.

[라라벨 contract](/docs/{{version}}/contracts)의 형태도 타입 힌트로 지정할 수 있습니다. 컨테이너가 의존성 해결을 할 수 있다면 타입 힌트에 지정할 수는 있습니다.

#### Method Injection
#### 메소드 주입

In addition to constructor injection, you may also type-hint dependencies on your controller's methods. A common use-case for method injection is injecting the `Illuminate\Http\Request` instance into your controller methods:

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

If your controller method is also expecting input from a route parameter, list your route arguments after your other dependencies. For example, if your route is defined like so:

컨트롤러 메소드가 라우트 인자로 부터 입력값을 받아야 한다면 간단하게 라우트 인자를 지정하면 됩니다. 예를 들어 다음과 같이 정의할 수 있습니다.

    Route::put('user/{id}', [UserController::class, 'update']);

You may still type-hint the `Illuminate\Http\Request` and access your `id` parameter by defining your controller method as follows:

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
## Route Caching
## 라우트 캐시

If your application is exclusively using controller based routes, you should take advantage of Laravel's route cache. Using the route cache will drastically decrease the amount of time it takes to register all of your application's routes. In some cases, your route registration may even be up to 100x faster. To generate a route cache, just execute the `route:cache` Artisan command:

애플리케이션이 컨트롤러 기반의 라우트만을 사용하고 있다면 라라벨의 라우트를 캐시하는 장점을 사용해야 합니다. 라우트 캐시를 사용하면 애플리케이션의 전체 라우트를 등록하는 데 걸리는 시간의 양을 크게 감소합니다. 경우에 따라서는 라우트 등록이 100배나 빨라질 수도 있습니다! 라우트 캐시를 생성하기 위해서는 `route:cache` 아티즌 명령어를 실행하면 됩니다.

    php artisan route:cache

After running this command, your cached routes file will be loaded on every request. Remember, if you add any new routes you will need to generate a fresh route cache. Because of this, you should only run the `route:cache` command during your project's deployment.

이 명령을 실행하면 캐시 된 라우트 파일이 모든 요청에 로드됩니다. 새로운 라우트를 추가하는 경우 새로운 라우트 캐시를 생성해야합니다. 이 때문에 프로젝트 배포 중에 `route:cache` 명령 만 실행하면 됩니다.

You may use the `route:clear` command to clear the route cache:

캐시를 재생성하는것 말고 캐시를 제거하기 위해서는 `route:clear` 명령어를 실행하면 됩니다.

    php artisan route:clear
