# Controllers
# 컨트롤러

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Writing Controllers](#writing-controllers)
- [컨트롤러 작성하기](#writing-controllers)
    - [Basic Controllers](#basic-controllers)
    - [기본 컨트롤러](#basic-controllers)
    - [Single Action Controllers](#single-action-controllers)
    - [단일 액션 컨트롤러](#single-action-controllers)
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
    - [리소스 컨트롤러에 라우트 추가하기](#restful-supplementing-resource-controllers)
    - [Singleton Resource Controllers](#singleton-resource-controllers)
    - [싱글턴 리소스 컨트롤러](#singleton-resource-controllers)
    - [Resource 컨트롤러 라우트에 추가하기](#dependency-injection-and-controllers)
- [Dependency Injection & Controllers](#dependency-injection-and-controllers)
- [의존성 주입 & 컨트롤러](#dependency-injection-and-controllers)

<a name="introduction"></a>
## Introduction
## 시작하기

Instead of defining all of your request handling logic as closures in your route files, you may wish to organize this behavior using "controller" classes. Controllers can group related request handling logic into a single class. For example, a `UserController` class might handle all incoming requests related to users, including showing, creating, updating, and deleting users. By default, controllers are stored in the `app/Http/Controllers` directory.

리퀘스트 처리에 관한 논리는 라우트 파일에서 클로저로 정의 할 수 있지만, 클로저로 정의하지 않고 "컨트롤러" 클래스를 사용하여 리퀘스트 처리를 할 수도 있습니다. 예를 들어 `UserController` 클래스는 사용자 표시(showing), 생성(creating), 업데이트(updating) 및 삭제(deleting)를 포함하여 사용자와 관련된 모든 수신되는 요청을 처리할 수 있습니다. 기본적으로 컨트롤러 클래스 파일은 `app/Http/Controllers` 디렉토리에 저장됩니다.

<a name="writing-controllers"></a>
## Writing Controllers
## 컨트롤러 작성하기

<a name="basic-controllers"></a>
### Basic Controllers
### 기본 컨트롤러

To quickly generate a new controller, you may run the `make:controller` Artisan command. By default, all of the controllers for your application are stored in the `app/Http/Controllers` directory:

새로운 컨트롤러를 빠르게 생성해 봅시다. `make:controller` 아티산 명령을 실행하면 됩니다. 기본적으로 애플리케이션의 모든 컨트롤러는 `app/Http/Controllers` 디렉터리에 위치합니다.:

```shell
php artisan make:controller UserController
```

Let's take a look at an example of a basic controller. A controller may have any number of public methods which will respond to incoming HTTP requests:

기본 컨트롤러의 예를 살펴 봅시다. 컨트롤러는 전달되는 HTTP 요청에 대해 응답하는 여러 퍼블릭 메소드를 가질 수 있습니다.

    <?php

    namespace App\Http\Controllers;
    
    use App\Models\User;
    use Illuminate\View\View;

    class UserController extends Controller
    {
        /**
         * Show the profile for a given user.
         */
        public function show(string $id): View
        {
            return view('user.profile', [
                'user' => User::findOrFail($id)
            ]);
        }
    }

Once you have written a controller class and method, you may define a route to the controller method like so:

컨트롤러 클래스와 메소드를 작성했다면, 다음과 같이 위 컨트롤러 메서드에 대한 경로를 정의할 수 있습니다.

    use App\Http\Controllers\UserController;

    Route::get('/user/{id}', [UserController::class, 'show']);

When an incoming request matches the specified route URI, the `show` method on the `App\Http\Controllers\UserController` class will be invoked and the route parameters will be passed to the method.

들어오는 요청(request)이 지정된 라우트(route)의 URI와 일치하면 `App\Http\Controllers\UserController` 클래스의 `show` 메소드가 호출되고 라우트(route)의 파라메터가 `show` 메소드에 전달됩니다.

> **Note**  
> Controllers are not **required** to extend a base class. However, you will not have access to convenient features such as the `middleware` and `authorize` methods.

> **Note**  
> 컨트롤러는 기본 컨트롤러 클래스를 **필수**로 상속 받지 않아도 작동합니다. 하지만 기본 컨트롤러 클래스를 상속하지 않는다면 `미들웨어(middleware)` 및 `권한 부여(authorize)` 메서드와 같은 편리한 기능을 사용하기 위한 접근을 할 수 없습니다.

<a name="single-action-controllers"></a>
### Single Action Controllers
### 단일 액션 컨트롤러

If a controller action is particularly complex, you might find it convenient to dedicate an entire controller class to that single action. To accomplish this, you may define a single `__invoke` method within the controller:

컨트롤러 작업이 특히 복잡한 경우, 하나의 컨트롤러 클래스 전체를 단일 액션으로 설정하는 것이 편리할 수 있습니다. 이를 수행하기 위해 컨트롤러 내에서 단일 `__invoke` 메서드를 정의할 수 있습니다.

    <?php

    namespace App\Http\Controllers;
    
    use App\Models\User;
    use Illuminate\Http\Response;

    class ProvisionServer extends Controller
    {
        /**
         * Provision a new web server.
         */
        public function __invoke()
        {
            // ...
        }
    }

When registering routes for single action controllers, you do not need to specify a controller method. Instead, you may simply pass the name of the controller to the router:

단일 액션 컨트롤러를 라우트에 등록할 때는 라우트에 컨트롤러의 메소드를 지정할 필요가 없습니다. 컨트롤러의 이름을 라우터에 전달하기만 하면 됩니다.

    use App\Http\Controllers\ProvisionServer;

    Route::post('/server', ProvisionServer::class);

You may generate an invokable controller by using the `--invokable` option of the `make:controller` Artisan command:

Artisan 컨멘드를 사용하면 `make:controller` 에 `--invokable` 옵션을 사용하여 `__invoke` 함수가 내장된 컨트롤러(invokable controller)를 생성 할 수 있습니다.

```shell
php artisan make:controller ProvisionServer --invokable
```

> **Note**  
> Controller stubs may be customized using [stub publishing](/docs/{{version}}/artisan#stub-customization).

> **Note**  
> [stub publishing](/docs/{{version}}/artisan#stub-customization)를 사용하여 controller stub을 사용자가 정의할 수 있습니다.

<a name="controller-middleware"></a>
## Controller Middleware
## 컨트롤러 미들웨어

[Middleware](/docs/{{version}}/middleware) may be assigned to the controller's routes in your route files:

[미들웨어](/docs/{{version}}/middleware)는 다음과 같이 컨트롤러 라우트에 지정할 수 있습니다.

    Route::get('profile', [UserController::class, 'show'])->middleware('auth');

Or, you may find it convenient to specify middleware within your controller's constructor. Using the `middleware` method within your controller's constructor, you can assign middleware to the controller's actions:

또는 컨트롤러 클래스의 생성자 내에서 미들웨어를 지정하는 것이 편리할 수 있습니다. 컨트롤러의 생성자 내에서 `middleware` 메서드를 사용하여 컨트롤러의 엑션에 미들웨어를 할당할 수 있습니다.

    class UserController extends Controller
    {
        /**
         * Instantiate a new controller instance.
         */
        public function __construct()
        {
            $this->middleware('auth');
            $this->middleware('log')->only('index');
            $this->middleware('subscribed')->except('store');
        }
    }

Controllers also allow you to register middleware using a closure. This provides a convenient way to define an inline middleware for a single controller without defining an entire middleware class:

컨트롤러를 사용하면 Closure를 사용하여 미들웨어를 등록 할 수 있습니다. 이는 미들웨어 클래스를 따로 정의하지 않고 단일 컨트롤러에 대한 미들웨어를 Closure로 정의하는 편리한 방법을 제공합니다.

    use Closure;
    use Illuminate\Http\Request;

    $this->middleware(function (Request $request, Closure $next) {
        return $next($request);
    });


<a name="resource-controllers"></a>
## Resource Controllers
## 리소스 컨트롤러

If you think of each Eloquent model in your application as a "resource", it is typical to perform the same sets of actions against each resource in your application. For example, imagine your application contains a `Photo` model and a `Movie` model. It is likely that users can create, read, update, or delete these resources.

애플리케이션의 각 Eloquent 모델을 "리소스"로 생각한다면 애플리케이션의 각 리소스에 대해 동일 유형(typical)의 같은 작업을 수행하는 것이 일반적입니다. 예를 들어 애플리케이션에 `Photo` 모델과 `Movie` 모델이 포함되어 있다고 가정해 보겠습니다. 사용자는 Photo 리소스에 대해서도 Movie 리소스에 대해서도 동일하게 생성, 조회, 업데이트 또는 삭제하는 동일한 유형의 작업을 합니다.

Because of this common use case, Laravel resource routing assigns the typical create, read, update, and delete ("CRUD") routes to a controller with a single line of code. To get started, we can use the `make:controller` Artisan command's `--resource` option to quickly create a controller to handle these actions:

이와 같은 일반적인 사용 사례(use case) 때문에 라라벨 리소스 라우팅은 동일 유형의 생성, 읽기, 업데이트 및 삭제("CRUD")를 여러줄의 라우터로 각각 선언하지 않고 한 줄의 코드로 컨트롤러의 기본 엑션들을 라우트에 할당하는 방법을 제공합니다. `make:controller` Artisan 명령의 `--resource` 옵션을 사용하여 모델에 대한 생성, 읽기, 업데이트 및 삭제를 처리하는 컨트롤러를 빠르게 생성할 수 있습니다.

```shell
php artisan make:controller PhotoController --resource
```

This command will generate a controller at `app/Http/Controllers/PhotoController.php`. The controller will contain a method for each of the available resource operations. Next, you may register a resource route that points to the controller:

이 명령어는 `app/Http/Controllers/PhotoController.php` 파일을 생성합니다. 생성된 컨트롤러 파일에는 리소스 작업에 해당하는 엑션 메소드를 포함합니다. 다음과 같이 컨트롤러를 지정하는 리소스 라우트를 등록할 수 있습니다.

    use App\Http\Controllers\PhotoController;

    Route::resource('photos', PhotoController::class);

This single route declaration creates multiple routes to handle a variety of actions on the resource. The generated controller will already have methods stubbed for each of these actions. Remember, you can always get a quick overview of your application's routes by running the `route:list` Artisan command.

단일한 라우트의 선언으로 다양한 엑션을 다루기 위한 리소스 라우터를 생성할 수 있습니다. 생성된 컨트롤러는 미리 생성된 코드(stub)를 가진 엑션 메소드를 가집니다. `route:list` Artisan 명령어를 실행하여 여러분 애플리케이션의 라우트에 대한 개략적인 코드(overview)를 빠르게 얻을 수 있다는 것을 알아 두세요.

You may even register many resource controllers at once by passing an array to the `resources` method:

`resources` 메소드에 배열을 전달하여 한번에 여러개의 리소스 컨트롤러를 등록할 수도 있습니다.

    Route::resources([
        'photos' => PhotoController::class,
        'posts' => PostController::class,
    ]);

<a name="actions-handled-by-resource-controller"></a>
#### Actions Handled By Resource Controller
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
#### Customizing Missing Model Behavior
#### 모델을 찾을 수 없는 경우의 동작 커스터마이징 하기

Typically, a 404 HTTP response will be generated if an implicitly bound resource model is not found. However, you may customize this behavior by calling the `missing` method when defining your resource route. The `missing` method accepts a closure that will be invoked if an implicitly bound model can not be found for any of the resource's routes:

일반적으로 바인딩된 리소스 모델을 찾을 수 없는 경우 라라벨은 404 HTTP 응답을 생성하도록 구현되어 있습니다. 리소스 라우트를 정의할 때 `missing` 메서드를 호출하여 자동으로 생성되는 404 HTTP응답 동작을 커스터마이징할 수 있습니다. `missing` 메소드는 리소스 경로에 대해 내부에 바인딩된 모델을 찾을 수 없는 경우 호출되는 클로저를 사용합니다.

    use App\Http\Controllers\PhotoController;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Redirect;

    Route::resource('photos', PhotoController::class)
            ->missing(function (Request $request) {
                return Redirect::route('photos.index');
            });

<a name="soft-deleted-models"></a>
#### Soft Deleted Models
#### 소프트 삭제된 모델

Typically, implicit model binding will not retrieve models that have been [soft deleted](/docs/{{version}}/eloquent#soft-deleting), and will instead return a 404 HTTP response. However, you can instruct the framework to allow soft deleted models by invoking the `withTrashed` method when defining your resource route:

기본적으로 소프트 삭제된 모델은 묵시적 바인딩으로 조회되지 않고 404 HTTP 응답이 반환됩니다. 하지만 리소스 라우트를 정의할 때 `withTrashed` 메서드를 호출하여 소프트 삭제된 모델도 허용하도록 프레임워크에 지시할 수 있습니다.

    use App\Http\Controllers\PhotoController;

    Route::resource('photos', PhotoController::class)->withTrashed();

Calling `withTrashed` with no arguments will allow soft deleted models for the `show`, `edit`, and `update` resource routes. You may specify a subset of these routes by passing an array to the `withTrashed` method:

인자 없이 `withTrashed`를 호출하면 `show`, `edit`, `update` 리소스 라우트에 대해 소프트 삭제된 모델을 허용합니다. `withTrashed` 메서드에 배열을 전달하여 라우트 하위 집합을 지정할 수 있습니다.

    Route::resource('photos', PhotoController::class)->withTrashed(['show']);

<a name="specifying-the-resource-model"></a>
#### Specifying The Resource Model
#### 리소스 모델 지정하기

If you are using [route model binding](/docs/{{version}}/routing#route-model-binding) and would like the resource controller's methods to type-hint a model instance, you may use the `--model` option when generating the controller:

[라우트 모델 바인딩](/docs/{{version}}/routing#route-model-binding)을 사용하고 있고, 리소스 컨트롤러의 메소드가 모델 인스턴스를 타입힌트를 하도록 원한다면 컨트롤러를 생성할 때 `--model` 옵션을 사용할 수 있습니다.

```shell
php artisan make:controller PhotoController --model=Photo --resource
```

<a name="generating-form-requests"></a>
#### Generating Form Requests
#### Form Request 생성하기

You may provide the `--requests` option when generating a resource controller to instruct Artisan to generate [form request classes](/docs/{{version}}/validation#form-request-validation) for the controller's storage and update methods:

아티즌을 사용하여 리소스 컨트롤러를 생성할 때 `--requests` 옵션을 지정하면, 컨트롤러의 모델 저장 및 업데이트 메서드에 필요한 [Form reqeust 클래스](/docs/{{버전}}/validation#form-request-validation)를 생성할 수 있습니다. 


```shell
php artisan make:controller PhotoController --model=Photo --resource --requests
```

<a name="restful-partial-resource-routes"></a>
### Partial Resource Routes
### Resource 라우트의 일부만 지정하기

When declaring a resource route, you may specify a subset of actions the controller should handle instead of the full set of default actions:

리소스 라우트를 선언할 때, 컨트롤러가 다루고 있는 엑션의 전체(full set)를 사용하도록 하는 것 대신 일부(subset)만 사용하도록 지정할 수 있습니다. 

    use App\Http\Controllers\PhotoController;

    Route::resource('photos', PhotoController::class)->only([
        'index', 'show'
    ]);

    Route::resource('photos', PhotoController::class)->except([
        'create', 'store', 'update', 'destroy'
    ]);

<a name="api-resource-routes"></a>
#### API Resource Routes
#### API 리소스 라우트

When declaring resource routes that will be consumed by APIs, you will commonly want to exclude routes that present HTML templates such as `create` and `edit`. For convenience, you may use the `apiResource` method to automatically exclude these two routes:

API에서 사용할 리소스 경로를 선언할 때 일반적으로 `create` 및 `edit`와 같은 HTML 템플릿을 표시하는 경로를 제외하고 싶을 것입니다. 편의를 위해 `apiResource` 메소드를 사용하여 다음 두 경로를 자동으로 제외할 수 있습니다.

    use App\Http\Controllers\PhotoController;

    Route::apiResource('photos', PhotoController::class);

You may register many API resource controllers at once by passing an array to the `apiResources` method:

배열을 `apiResources` 메소드에 전달하여 한 번에 많은 API 리소스 컨트롤러를 등록할 수 있습니다.

    use App\Http\Controllers\PhotoController;
    use App\Http\Controllers\PostController;

    Route::apiResources([
        'photos' => PhotoController::class,
        'posts' => PostController::class,
    ]);

To quickly generate an API resource controller that does not include the `create` or `edit` methods, use the `--api` switch when executing the `make:controller` command:

`create` 또는 `edit` 메소드가 포함되지 않은 API 리소스 컨트롤러를 빠르게 생성하려면 `make:controller` 명령을 실행할 때 `--api` 옵션(switch)을 사용하세요.

```shell
php artisan make:controller PhotoController --api
```

<a name="restful-nested-resources"></a>
### Nested Resources
### 중첩된(Nested) Resources

Sometimes you may need to define routes to a nested resource. For example, a photo resource may have multiple comments that may be attached to the photo. To nest the resource controllers, you may use "dot" notation in your route declaration:

때로는 엘로퀀트 모델의 중첩된(Nested) 리소스에 대한 라우트를 정의해야 할 수도 있습니다. 예를 들어, 게제할 사진 정보를 저장하는 사진 리소스는 사진에 관한 여러 글(comments)을 가질 수 있습니다. 사진 리소스를 사용하는 리소스 컨트롤러에 사진에 관한 글(comments)을 중첩(nest)하려면 라우트를 선언할 때 엘로퀀트 모델간의 연결 관계를 "dot" 기호를 사용해서 나타냅니다.

    use App\Http\Controllers\PhotoCommentController;

    Route::resource('photos.comments', PhotoCommentController::class);

This route will register a nested resource that may be accessed with URIs like the following:

이 라우트는 다음과 같은 URI로 접근 할 수있는 중첩 된 리소스를 등록합니다.

    /photos/{photo}/comments/{comment}

<a name="scoping-nested-resources"></a>
#### Scoping Nested Resources
### 중첩(Nested) 리소스 범위(Scoping) 지정

Laravel's [implicit model binding](/docs/{{version}}/routing#implicit-model-binding-scoping) feature can automatically scope nested bindings such that the resolved child model is confirmed to belong to the parent model. By using the `scoped` method when defining your nested resource, you may enable automatic scoping as well as instruct Laravel which field the child resource should be retrieved by. For more information on how to accomplish this, please see the documentation on [scoping resource routes](#restful-scoping-resource-routes).

라라벨의 [암시적 모델 바인딩](/docs/{{version}}/routing#implicit-model-binding-scoping) 기능(feature)은 컨트롤러에서 사용할(resolved) 모델을 자식 모델이라고 할 때 부모 모델에 속해 있는지 (belong to 관계인지) 확인하는 기능을 제공합니다. 중첩된 리소스를 정의할 때 `scoped` 메서드를 사용하면 자동 범위 지정을 활성화할 수 있을 뿐만 아니라 자식 리소스가 취득(retrieved)해야 할 필드를 라라벨에 지시할 수 있습니다. 이를 수행하는 방법에 대한 자세한 내용은 [자원 경로 범위 지정](#restful-scoping-resource-routes)에 대한 설명서를 읽어 보세요.

<a name="shallow-nesting"></a>
#### Shallow Nesting
#### 얕은 중첩 (Shallow Nesting)

Often, it is not entirely necessary to have both the parent and the child IDs within a URI since the child ID is already a unique identifier. When using unique identifiers such as auto-incrementing primary keys to identify your models in URI segments, you may choose to use "shallow nesting":

자식 ID는 이미 고유 식별자이므로 URI 내에 부모 ID와 자식 ID를 모두 가질 필요는 없습니다. URI 단위(segments)에서 모델을 식별하기 위해 기본키의 auto-incrementing 과 같은 고유 식별자를 사용하는 경우 "얕은 중첩"을 사용하도록 선택할 수 있습니다.

    use App\Http\Controllers\CommentController;

    Route::resource('photos.comments', CommentController::class)->shallow();

This route definition will define the following routes:

위와 같이 라우트를 정의하면 아래 리스트에 해당하는 라우트가 등록됩니다.


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

By default, all resource controller actions have a route name; however, you can override these names by passing a `names` array with your desired route names:

기본적으로 모든 리소스 컨트롤러 액션은 라우트 이름을 가지고 있습니다. 그러나 `names` 옵션 배열을 전달하여 원하는 라우트 이름으로 덮어씌울 수 있습니다.

    use App\Http\Controllers\PhotoController;

    Route::resource('photos', PhotoController::class)->names([
        'create' => 'photos.build'
    ]);

<a name="restful-naming-resource-route-parameters"></a>
### Naming Resource Route Parameters
### 리소스 라우트 파리미터 이름 지정하기

By default, `Route::resource` will create the route parameters for your resource routes based on the "singularized" version of the resource name. You can easily override this on a per resource basis using the `parameters` method. The array passed into the `parameters` method should be an associative array of resource names and parameter names:

기본적으로 `Route::resource`가 라우트 파라미터들을 생성할 때는 리소스 이름의 "단일화된(singularized)" 버전을 기반으로 생성합니다. 사용자는 각각의 리소스마다 `parameters` 메소드를 사용하여 손쉽게 라우팅 엑세스 경로의 파라메터를 오버라이딩할 수 있습니다. `parameters` 메소드로 전달 된 배열은 리소스의 이름과 파라미터 이름의 연관 배열이어야합니다.

    use App\Http\Controllers\AdminUserController;

    Route::resource('users', AdminUserController::class)->parameters([
        'users' => 'admin_user'
    ]);

 The example above generates the following URI for the resource's `show` route:

 위의 예제는 리소스의 `show` 라우트에서 다음의 URI를 생성합니다.

    /users/{admin_user}

<a name="restful-scoping-resource-routes"></a>
### Scoping Resource Routes
### 리소스 라우트 스코프 지정

Laravel's [scoped implicit model binding](/docs/{{version}}/routing#implicit-model-binding-scoping) feature can automatically scope nested bindings such that the resolved child model is confirmed to belong to the parent model. By using the `scoped` method when defining your nested resource, you may enable automatic scoping as well as instruct Laravel which field the child resource should be retrieved by:

Laravel의 [범위가 지정된 암시적 모델 바인딩](/docs/{{version}}/routing#implicit-model-binding-scoping) 기능(feature)은 컨트롤러에서 사용할(resolved) 모델을 자식 모델이라고 할 때 부모 모델에 속해 있는지 (belong to 관계인지) 확인하는 기능을 제공합니다. 중첩된 리소스를 정의할 때 `scoped` 메서드를 사용하면 자동 범위 지정을 활성화할 수 있을 뿐만 아니라 자식 리소스가 취득(retrieved)해야 할 필드를 라라벨에 지시할 수 있습니다.

    use App\Http\Controllers\PhotoCommentController;

    Route::resource('photos.comments', PhotoCommentController::class)->scoped([
        'comment' => 'slug',
    ]);

This route will register a scoped nested resource that may be accessed with URIs like the following:

이 라우트는 다음과 같은 URI로 액세스할 수 있는 범위가 지정된 중첩 리소스를 등록합니다.

    /photos/{photo}/comments/{comment:slug}

When using a custom keyed implicit binding as a nested route parameter, Laravel will automatically scope the query to retrieve the nested model by its parent using conventions to guess the relationship name on the parent. In this case, it will be assumed that the `Photo` model has a relationship named `comments` (the plural of the route parameter name) which can be used to retrieve the `Comment` model.

중첩된 라우트의 파라메터로 커스텀 키가 있는 암시적(implicit) 바인딩을 사용할 때, 라라벨은 자동으로 쿼리의 스코프를 지정하여 부모의 관계 이름을 추축하는 규칙을 사용하여 부모 모델에 중첩되어 있는 모델인지 확인합니다. 이 경우 부모 모델인 `Photo`는 자식 모델인 `Comment`를 검색하는 데 사용할 수있는 `comments`(라우트 파라메터 이름의 복수)라는 엘로퀀트 릴레이션 관계가있는 것으로 가정합니다.

<a name="restful-localizing-resource-uris"></a>
### Localizing Resource URIs
### 리소스 URI의 지역화(다국어 동사처리)

By default, `Route::resource` will create resource URIs using English verbs and plural rules. If you need to localize the `create` and `edit` action verbs, you may use the `Route::resourceVerbs` method. This may be done at the beginning of the `boot` method within your application's `App\Providers\RouteServiceProvider`:

기본적으로 `Route::resource` 는 영어 동사와 복수형 규칙을 사용하여 리소스 URI를 만듭니다. 만약 `create`와 `edit`와 같은 이름 대신 현지화된 동사 이름을 사용하고자 한다면 `Route::resourceVerbs` 메소드를 사용하면 됩니다. 이 작업은 애플리케이션의 `App\Providers\RouteServiceProvider`의 `boot` 메소드에서 수행해야 합니다.

    /**
     * Define your route model bindings, pattern filters, etc.
     */
    public function boot(): void
    {
        Route::resourceVerbs([
            'create' => 'crear',
            'edit' => 'editar',
        ]);

        // ...
    }

Laravel's pluralizer supports [several different languages which you may configure based on your needs](/docs/{{version}}/localization#pluralization-language). Once the verbs and pluralization language have been customized, a resource route registration such as `Route::resource('publicacion', PublicacionController::class)` will produce the following URIs:

라라벨 플로러라이저(pluralizer)는 [여러분의 필요에 따라 구성할 수 있는 여러 언어](/docs/{{version}}/localization#pluralization-language)를 지원합니다. 한 번, 동사와 '복수형으로 설정된'(pluralization) 언어를 사용자정의 하게 되면, `Route::resource('publicacion', PublicacionController::class)`와 같은 리소스 라우트는 다음의 URI를 설정하게 됩니다.

    /publicacion/crear

    /publicacion/{publicaciones}/editar

<a name="restful-supplementing-resource-controllers"></a>
### Supplementing Resource Controllers
### Resource 컨트롤러 라우트에 추가하기

If you need to add additional routes to a resource controller beyond the default set of resource routes, you should define those routes before your call to the `Route::resource` method; otherwise, the routes defined by the `resource` method may unintentionally take precedence over your supplemental routes:

만약 리소스 컨트롤러에 라우트를 추가해야 한다면 `Route::resource`메서드를 호출하기 전에 추가하려는 라우트를 정의해야합니다. 그렇지 않으면 `resource` 메소드에 의해서 정의된 라우트들이 추가한 라우트들 보다 우선하게 되어 버립니다.

    use App\Http\Controller\PhotoController;

    Route::get('/photos/popular', [PhotoController::class, 'popular']);
    Route::resource('photos', PhotoController::class);

> **Note**  
> Remember to keep your controllers focused. If you find yourself routinely needing methods outside of the typical set of resource actions, consider splitting your controller into two, smaller controllers.

> **Note**  
> 컨트롤러에 포커스를 맞춰야 한다는 것을 기억하세요. 기본 유형(the typical set)의 리소스 엑션 세트 이외의 빈번하게 사용할 엑션이 필요한 경우 컨트롤러를 두 개의 컨트롤러로 분할하는 것, 컨트롤러를 작게 만드는 것을 고려하세요.

<a name="singleton-resource-controllers"></a>
### Singleton Resource Controllers
### 싱글톤 리소스 컨트롤러

Sometimes, your application will have resources that may only have a single instance. For example, a user's "profile" can be edited or updated, but a user may not have more than one "profile". Likewise, an image may have a single "thumbnail". These resources are called "singleton resources", meaning one and only one instance of the resource may exist. In these scenarios, you may register a "singleton" resource controller:

때로는 애플리케이션에는 단일 인스턴스만 존재할 수 있는 리소스가 있습니다. 예를 들어, 사용자의 "프로필"은 수정하거나 업데이트 할 수 있지만, 사용자는 하나 이상의 "프로필"을 가질 수 없습니다. 마찬가지로, 이미지는 하나의 "썸네일"을 가질 수 있습니다. 이러한 리소스는 "싱글톤 리소스"라고 부르며, 리소스의 인스턴스는 하나만 존재할 수 있습니다. 이러한 시나리오에서 "싱글톤" 리소스 컨트롤러를 등록할 수 있습니다.

```php
use App\Http\Controllers\ProfileController;
use Illuminate\Support\Facades\Route;

Route::singleton('profile', ProfileController::class);
```

The singleton resource definition above will register the following routes. As you can see, "creation" routes are not registered for singleton resources, and the registered routes do not accept an identifier since only one instance of the resource may exist:

위의 싱글톤 리소스 정의는 다음 라우트를 등록합니다. 보시다시피, "생성" 라우트는 싱글톤 리소스에 대해 등록되지 않으며, 등록된 라우트는 리소스의 인스턴스가 하나만 존재할 수 있으므로 식별자를 허용하지 않습니다.

Verb      | URI                               | Action       | Route Name
----------|-----------------------------------|--------------|---------------------
GET       | `/profile`                        | show         | profile.show
GET       | `/profile/edit`                   | edit         | profile.edit
PUT/PATCH | `/profile`                        | update       | profile.update

Singleton resources may also be nested within a standard resource:

싱글톤 리소스는 표준 리소스 내에 중첩될 수도 있습니다.

```php
Route::singleton('photos.thumbnail', ThumbnailController::class);
```

In this example, the `photos` resource would receive all of the [standard resource routes](#actions-handled-by-resource-controller); however, the `thumbnail` resource would be a singleton resource with the following routes:

이 예에서는 `photos` 리소스가 [표준 리소스 라우트](#actions-handled-by-resource-controller)를 모두 받습니다. 그러나 `thumbnail` 리소스는 다음 라우트를 가진 싱글톤 리소스가 됩니다.

| Verb      | URI                              | Action  | Route Name               |
|-----------|----------------------------------|---------|--------------------------|
| GET       | `/photos/{photo}/thumbnail`      | show    | photos.thumbnail.show    |
| GET       | `/photos/{photo}/thumbnail/edit` | edit    | photos.thumbnail.edit    |
| PUT/PATCH | `/photos/{photo}/thumbnail`      | update  | photos.thumbnail.update  |

<a name="creatable-singleton-resources"></a>
#### Creatable Singleton Resources
#### 생성가능한 싱글톤 리소스

Occasionally, you may want to define creation and storage routes for a singleton resource. To accomplish this, you may invoke the `creatable` method when registering the singleton resource route:

가끔은 싱글톤 리소스에 대한 생성 및 저장 라우트를 정의하고 싶을 수 있습니다. 이를 위해 싱글톤 리소스 라우트를 등록할 때 `creatable` 메소드를 호출할 수 있습니다.

```php
Route::singleton('photos.thumbnail', ThumbnailController::class)->creatable();
```

In this example, the following routes will be registered. As you can see, a `DELETE` route will also be registered for creatable singleton resources:

이 예에서는 다음 라우트가 등록됩니다. 보시다시피, 생성 가능한 싱글톤 리소스에 대한 `DELETE` 라우트도 등록됩니다.

| Verb      | URI                                | Action  | Route Name               |
|-----------|------------------------------------|---------|--------------------------|
| GET       | `/photos/{photo}/thumbnail/create` | create  | photos.thumbnail.create  |
| POST      | `/photos/{photo}/thumbnail`        | store   | photos.thumbnail.store   |
| GET       | `/photos/{photo}/thumbnail`        | show    | photos.thumbnail.show    |
| GET       | `/photos/{photo}/thumbnail/edit`   | edit    | photos.thumbnail.edit    |
| PUT/PATCH | `/photos/{photo}/thumbnail`        | update  | photos.thumbnail.update  |
| DELETE    | `/photos/{photo}/thumbnail`        | destroy | photos.thumbnail.destroy |

If you would like Laravel to register the `DELETE` route for a singleton resource but not register the creation or storage routes, you may utilize the `destroyable` method:

라라벨이 싱글톤 리소스에 대해 `DELETE` 라우트를 등록하되 생성 또는 저장 라우트를 등록하지 않으려면 `destroyable` 메소드를 사용할 수 있습니다.

```php
Route::singleton(...)->destroyable();
```

<a name="api-singleton-resources"></a>
#### API Singleton Resources
#### API 싱글톤 리소스

The `apiSingleton` method may be used to register a singleton resource that will be manipulated via an API, thus rendering the `create` and `edit` routes unnecessary:

`apiSingleton` 메소드는 API를 통해 조작되는 싱글톤 리소스를 등록하는 데 사용할 수 있으므로 `create` 및 `edit` 라우트가 불필요합니다.

```php
Route::apiSingleton('profile', ProfileController::class);
```

Of course, API singleton resources may also be `creatable`, which will register `store` and `destroy` routes for the resource:

물론 API 싱글톤 리소스는 `creatable`이 될 수 있으며, 이는 리소스에 대한 `store` 및 `destroy` 라우트를 등록합니다.

```php
Route::apiSingleton('photos.thumbnail', ProfileController::class)->creatable();
```

<a name="dependency-injection-and-controllers"></a>
## Dependency Injection & Controllers
## 의존성 주입 & 컨트롤러

<a name="constructor-injection"></a>
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
         * Create a new controller instance.
         */
        public function __construct(
            protected UserRepository $users,
        ) {}
    }

<a name="method-injection"></a>
#### Method Injection
#### 메소드 인젝션

In addition to constructor injection, you may also type-hint dependencies on your controller's methods. A common use-case for method injection is injecting the `Illuminate\Http\Request` instance into your controller methods:

생성자 주입 외에도 컨트롤러의 메소드에 대한 타입힌트 의존성을 사용할 수도 있습니다. 메소드 주입의 일반적인 사용 사례는 `Illuminate\Http\Request` 인스턴스를 컨트롤러 메소드에 주입하는 것입니다.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\RedirectResponse;
    use Illuminate\Http\Request;

    class UserController extends Controller
    {
        /**
         * Store a new user.
         */
        public function store(Request $request): RedirectResponse
        {
            $name = $request->name;

            // Store the user...

            return redirect('/users');
        }
    }

If your controller method is also expecting input from a route parameter, list your route arguments after your other dependencies. For example, if your route is defined like so:

컨트롤러의 메소드가 라우트 파라메터의 전달도 고려해야 하는 경우, 의존성 주입 파라메터 다음에 라우터 파라메터를 나열합니다. 예를 들어 경로가 다음과 같이 정의된 경우

    use App\Http\Controllers\UserController;

    Route::put('/user/{id}', [UserController::class, 'update']);

You may still type-hint the `Illuminate\Http\Request` and access your `id` parameter by defining your controller method as follows:

다음과 같은 코드를 통해 동일하게 타입힌트로 지정된 `Illuminate\Http\Request` 의존성 주입 대상과 컨트롤러 메소드에 정의된 `id` 파라메터에 접근할 수 있습니다.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\RedirectResponse;
    use Illuminate\Http\Request;

    class UserController extends Controller
    {
        /**
         * Update the given user.
         */
        public function update(Request $request, string $id): RedirectResponse
        {
            // Update the user...

            return redirect('/users');
        }
    }
