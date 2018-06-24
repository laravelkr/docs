# HTTP Controllers
# HTTP 컨트롤러

- [Introduction](#introduction)
- [소개](#introduction)
- [Basic Controllers](#basic-controllers)
- [기본적인 컨트롤서](#basic-controllers)
- [Controller Middleware](#controller-middleware)
- [컨트롤러 미들웨어](#controller-middleware)
- [RESTful Resource Controllers](#restful-resource-controllers)
- [RESTful 리소스 컨트롤러](#restful-resource-controllers)
    - [Partial Resource Routes](#restful-partial-resource-routes)
    - [Resource 라우트의 일부만 지정하기](#restful-partial-resource-routes)
    - [Naming Resource Routes](#restful-naming-resource-routes)
    - [resource 라우트 이름 지정하기](#restful-naming-resource-routes)
    - [Nested Resources](#restful-nested-resources)
    - [중첩된 Resource](#restful-nested-resources)
    - [Supplementing Resource Controllers](#restful-supplementing-resource-controllers)
    - [Resource 컨트롤러 라우트에 추가하기](#restful-supplementing-resource-controllers)
- [Implicit Controllers](#implicit-controllers)
- [묵시적 컨트롤러](#implicit-controllers)
- [Dependency Injection & Controllers](#dependency-injection-and-controllers)
- [의존성 주입 & 컨트롤러](#dependency-injection-and-controllers)
- [Route Caching](#route-caching)
- [라우트 캐싱](#route-caching)

<a name="introduction"></a>
## Introduction
## 소개

Instead of defining all of your request handling logic in a single `routes.php` file, you may wish to organize this behavior using Controller classes. Controllers can group related HTTP request handling logic into a class. Controllers are typically stored in the `app/Http/Controllers` directory.

애플리케이션의 요청에 대한 모든 처리 로직을 하나의 `routes.php` 파일에 정의하는 것 보다 별도의 컨트롤러 클래스를 통해서 구성할 수도 있습니다. 컨트롤러는 클래스를 구성하여 HTTP 요청에 대한 그룹을 지정합니다. 컨트롤러는 일반적으로 `app/Http/Controllers` 디렉토리에 저장합니다. 

<a name="basic-controllers"></a>
## Basic Controllers
## 기본적인 컨트롤러

Here is an example of a basic controller class. All Laravel controllers should extend the base controller class included with the default Laravel installation:

다음은 기본적인 컨트롤러 클래스의 예제 입니다. 모든 라라벨의 컨트롤러는 라라벨 설치후 들어 있는 base 컨트롤러를 상속받아야만 합니다.

    <?php

    namespace App\Http\Controllers;

    use App\User;
    use App\Http\Controllers\Controller;

    class UserController extends Controller
    {
        /**
         * Show the profile for the given user.
         *
         * @param  int  $id
         * @return Response
         */
        public function showProfile($id)
        {
            return view('user.profile', ['user' => User::findOrFail($id)]);
        }
    }

We can route to the controller action like so:

다음과 같이 컨트롤러의 액션에 라우트를 지정할 수 있습니다. 

    Route::get('user/{id}', 'UserController@showProfile');

Now, when a request matches the specified route URI, the `showProfile` method on the `UserController` class will be executed. Of course, the route parameters will also be passed to the method.

이제 사용자의 요청이 지정된 라우트의 URI와 일치할 때 `UserController` 클래스의 `showProfile` 메소드가 실행될것입니다. 이때, 라우트의 파라미터들 또한 메소드에 전달될 것입니다.

#### Controllers & Namespaces
#### 컨트롤러 & 네임스페이스

It is very important to note that we did not need to specify the full controller namespace when defining the controller route. We only defined the portion of the class name that comes after the `App\Http\Controllers` namespace "root". By default, the `RouteServiceProvider` will load the `routes.php` file within a route group containing the root controller namespace. 

컨트롤러에 대응하는 라우트를 정의 할 때 컨트롤러의 전체 네임스페이스를 지정할 필요가 없다는 점에 유의하십시오. 클래스를 지정할 때에는, `App\Http\Controllers` 네임스페이스를 "root" 로 인식하게 됩니다. 기본적으로 `RouteServiceProvider` 가 `routes.php` 파일의 라우트들을 로드할 때 자동으로 이 "root" 네임스페이스를 포함하는 것으로 인식합니다. 

If you choose to nest or organize your controllers using PHP namespaces deeper into the `App\Http\Controllers` directory, simply use the specific class name relative to the `App\Http\Controllers` root namespace. So, if your full controller class is `App\Http\Controllers\Photos\AdminController`, you would register a route like so:

만약 `App\Http\Controllers` 하위에 중첩된 디렉토리를 구성하여 네임스페이스를 부여할 경우에는 단순히 `App\Http\Controllers`를 루트 네임 스페이스를 기준으로 하여 특정 클래스 이름을 사용하면 됩니다. 따라서 만약 컨트롤러가 `App\Http\Controllers\Photos\AdminController` 처럼 구성되어 있다면 다음처럼 라우트를 구성하면 됩니다. 

    Route::get('foo', 'Photos\AdminController@method');

#### Naming Controller Routes
#### 이름이 지정된 컨트롤러 라우트

Like Closure routes, you may specify names on controller routes: 

클로저 라우트와 같이 컨트롤러 라우트에 이름을 지정할 수 있습니다. 

    Route::get('foo', ['uses' => 'FooController@method', 'as' => 'name']);

#### URLs To Controller Actions 
#### 컨트롤러 액션의 URL 구하기

You may also use the `route` helper to generate a URL to a named controller route:

이름이 지정된 컨트롤러의 라우트에 대한 URL을 생성하기 위해서 `route` 헬퍼 함수를 사용할 수 있습니다.

    $url = route('name');

You may also use the `action` helper method to generate a URL using the controller's class and method names. Again, we only need to specify the part of the controller class name that comes after the base `App\Http\Controllers` namespace:

또한 컨트롤러의 클래스와 메소드 네임을 사용하여 URL을 생성하기 위해서 `action` 헬퍼 함수를 사용할 수도 있습니다. 다시 한번 말하지만 이 경우에도 `App\Http\Controllers` 네임스페이스 이후의 컨트롤러 클래스 이름만을 지정하면 됩니다. 

    $url = action('FooController@method');

You may access the name of the controller action being run using the `currentRouteAction` method on the `Route` facade:

실행중인 컨트롤러 액션의 이름을 찾고자 한다면 `Route` 파사드의 `currentRouteAction` 메소드를 사용하면 됩니다. 

	$action = Route::currentRouteAction();

<a name="controller-middleware"></a>
## Controller Middleware
## 컨트롤러 미들웨어

[Middleware](/docs/{{version}}/middleware) may be assigned to the controller's routes like so:

[미들웨어](/docs/{{version}}/middleware)는 다음과 같이 컨트롤러 라우트에 지정할 수 있습니다. 

    Route::get('profile', [
        'middleware' => 'auth',
        'uses' => 'UserController@showProfile'
    ]);

However, it is more convenient to specify middleware within your controller's constructor. Using the `middleware` method from your controller's constructor, you may easily assign middleware to the controller. You may even restrict the middleware to only certain methods on the controller class:

하지만 보다 편리한 방법은 컨트롤러의 생성자에서 미들웨어를 지정하는 것입니다. 컨트롤러의 생성자에서 `middleware` 메소드를 사용하여 여러분은 손쉽게 컨트롤러에서 사용할 미들웨어를 지정할 수 있습니다. 컨트롤러의 몇몇 메소드에서만 제한하여 미들웨어를 지정할 수도 있습니다. 

    class UserController extends Controller
    {
        /**
         * Instantiate a new UserController instance.
         *
         * @return void
         */
        public function __construct()
        {
            $this->middleware('auth');

            $this->middleware('log', ['only' => ['fooAction', 'barAction']]);

            $this->middleware('subscribed', ['except' => ['fooAction', 'barAction']]);
        }
    }

<a name="restful-resource-controllers"></a>
## RESTful Resource Controllers
## RESTful 리소스 컨트롤러

Resource controllers make it painless to build RESTful controllers around resources. For example, you may wish to create a controller that handles HTTP requests regarding "photos" stored by your application. Using the `make:controller` Artisan command, we can quickly create such a controller:

리소스 컨트롤러는 리소스에 대한 RESTful 컨트롤러를 어려움없이 구성할 수 있게 해줍니다. 예를 들어 여러분은 애플리케이션에서 "photos" 를 처리하는 컨트롤러를 다음과 같이 생성할 수 있습니다. `make:controller` 아티즌 명령어를 통해서 새로운 컨트롤러를 빠르게 생성할 수 있습니다. 

    php artisan make:controller PhotoController

The Artisan command will generate a controller file at `app/Http/Controllers/PhotoController.php`. The controller will contain a method for each of the available resource operations.

아티즌 명령어는 `app/Http/Controllers/PhotoController.php` 파일을 생성할 것입니다. 이 컨트롤러는 각각의 resource 에 해당하는 메소드들을 가지고 있을 것입니다. 

Next, you may register a resourceful route to the controller:

이제 생성된 컨트롤러에 resourceful 라우트를 등록하면 됩니다. 

    Route::resource('photo', 'PhotoController');

This single route declaration creates multiple routes to handle a variety of RESTful actions on the photo resource. Likewise, the generated controller will already have methods stubbed for each of these actions, including notes informing you which URIs and verbs they handle. 

한번의 선언만으로 photo 를 구성하는 RESTful 한 액션에 대한 다양한 라우트를 설정할 수 있습니다. 앞에서 직접 개별 메소드를 구성한것과 마찬가지로 생성된 컨트롤러는 각각의 메소드가 처리하는 URI와 액션에 대한 메모와 함께 구성됩니다. 

#### Actions Handled By Resource Controller
#### 리소스풀 컨트롤러에 의해서 구성된 액션들

Verb      | Path                  | Action       | Route Name
----------|-----------------------|--------------|---------------------
GET       | `/photo`              | index        | photo.index
GET       | `/photo/create`       | create       | photo.create
POST      | `/photo`              | store        | photo.store
GET       | `/photo/{photo}`      | show         | photo.show
GET       | `/photo/{photo}/edit` | edit         | photo.edit
PUT/PATCH | `/photo/{photo}`      | update       | photo.update
DELETE    | `/photo/{photo}`      | destroy      | photo.destroy

<a name="restful-partial-resource-routes"></a>
#### Partial Resource Routes
#### Resource 라우트의 일부만 지정하기

When declaring a resource route, you may specify a subset of actions to handle on the route:

resource 라우트를 선언할 때, 액션의 일부만을 지정할 수도 있습니다.

    Route::resource('photo', 'PhotoController',
                    ['only' => ['index', 'show']]);

    Route::resource('photo', 'PhotoController',
                    ['except' => ['create', 'store', 'update', 'destroy']]);

<a name="restful-naming-resource-routes"></a>
#### Naming Resource Routes
#### resource 라우트 이름 지정하기

By default, all resource controller actions have a route name; however, you can override these names by passing a `names` array with your options: 

기본적으로 모든 리루스 컨트롤러 액션은 라우트 이름을 가지고 있습니다. 그러나 `names` 옵션 배열을 전달하여 이름을 덮어씌울 수 있습니다. 

    Route::resource('photo', 'PhotoController',
                    ['names' => ['create' => 'photo.build']]);

<a name="restful-nested-resources"></a>
#### Nested Resources
#### 중첩된 Resource

Sometimes you may need to define routes to a "nested" resource. For example, a photo resource may have multiple "comments" that may be attached to the photo. To "nest" resource controllers, use "dot" notation in your route declaration:

중첩된 리소스를 컨트롤링 하고자 한다면 "."을 이용해서 리소스를 선언하면 됩니다. 

어떠한 경우 여러분은 "중첩된" resource 에 대한 라우트를 정의해야될 수도 있습니다. 예를 들어 사진에 첨부되는 여러개의 "댓글"을 가진 photo resource 를 구성할 수도 있습니다. 중첩된 resource를 컨트롤러를 통해서 지정 하고자 한다면 "."을 이용해서 리소스를 선언하면 됩니다. 

    Route::resource('photos.comments', 'PhotoCommentController');

This route will register a "nested" resource that may be accessed with URLs like the following: `photos/{photos}/comments/{comments}`.

라우터는 URL을 `photos/{photos}/comments/{comments}`와 같은 형태로 중첩되어 리소스를 관리할 수 있게 생성해줍니다. 

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;

    class PhotoCommentController extends Controller
    {
        /**
         * Show the specified photo comment.
         *
         * @param  int  $photoId
         * @param  int  $commentId
         * @return Response
         */
        public function show($photoId, $commentId)
        {
            //
        }
    }

<a name="restful-supplementing-resource-controllers"></a>
#### Supplementing Resource Controllers
#### Resource 컨트롤러 라우트에 추가하기

If it becomes necessary to add additional routes to a resource controller beyond the default resource routes, you should define those routes before your call to `Route::resource`; otherwise, the routes defined by the `resource` method may unintentionally take precedence over your supplemental routes:

만약 리소스 컨트롤러에 추가적으로 라우팅을 구성해야할 필요가 있다면 `Route::resource`가 호출되기 전에 등록해야합니다. 그렇지 않으면 `resource` 메소드에 의해서 정의된 라우트들이 추가한 라우트들 보다 우선하게 되어 버립니다. 

    Route::get('photos/popular', 'PhotoController@method');

    Route::resource('photos', 'PhotoController');

<a name="implicit-controllers"></a>
## Implicit Controllers
## 묵시적 컨트롤러

Laravel allows you to easily define a single route to handle every action in a controller class. First, define the route using the `Route::controller` method. The `controller` method accepts two arguments. The first is the base URI the controller handles, while the second is the class name of the controller:

라라벨에서는 한번의 라우팅 등록으로 컨트롤러 클래스를 통해 모든 액션들을 처리할 수 있는 손쉬운 방법을 제공합니다. 먼저 `Route::controller` 메소드를 사용하여 라우트를 지정합니다. `controller` 메소드는 두가지의 인자를 전달 받습니다. 첫번째는 컨트롤러가 처리할 베이스 URI를 의미하고 두번째 인자는 컨트롤러 클래스의 이름을 의미합니다:

    Route::controller('users', 'UserController');

 Next, just add methods to your controller. The method names should begin with the HTTP verb they respond to followed by the title case version of the URI:

 다음으로 컨트롤러에 메소드를 추가하면 됩니다. 메소드의 이름은 HTTP 메소드 이름형태로 시작해야 하고 URI를 표시하는 대문자로 시작하는 형태로 구성하면 됩니다. 

    <?php

    namespace App\Http\Controllers;

    class UserController extends Controller
    {
        /**
         * Responds to requests to GET /users
         */
        public function getIndex()
        {
            //
        }

        /**
         * Responds to requests to GET /users/show/1
         */
        public function getShow($id)
        {
            //
        }

        /**
         * Responds to requests to GET /users/admin-profile
         */
        public function getAdminProfile()
        {
            //
        }

        /**
         * Responds to requests to POST /users/profile
         */
        public function postProfile()
        {
            //
        }
    }

As you can see in the example above, `index` methods will respond to the root URI handled by the controller, which, in this case, is `users`.

예제에서 보다 시피, `index` 메소드는 `users` URI에 대한 라우트 주소에 대한 결과를 반환할 것입니다. 

#### Assigning Route Names
#### 라우트에 이름 지정하기

If you would like to [name](/docs/{{version}}/routing#named-routes) some of the routes on the controller, you may pass an array of names as the third argument to the `controller` method:

컨트롤러 라우트에 어떤 [name](/docs/{{version}}/routing#named-routes)을 지정하고자 한다면 `controller` 메소드의 세번째 인자로 라우트 이름을 나타내는 배열을 전달하여 지정할 수 있습니다. 

    Route::controller('users', 'UserController', [
        'getShow' => 'user.show',
    ]);

<a name="dependency-injection-and-controllers"></a>
## Dependency Injection & Controllers
## 의존성 주입 & 컨트롤러

#### Constructor Injection
#### 생성자 주입

The Laravel [service container](/docs/{{version}}/container) is used to resolve all Laravel controllers. As a result, you are able to type-hint any dependencies your controller may need in its constructor. The dependencies will automatically be resolved and injected into the controller instance:

라라벨의 [서비스 컨테이너](/docs/{{version}}/container)는 모든 라라벨 컨트롤러의 의존성을 해결하기 위해서 사용됩니다. 그 결과 컨트롤러가 필요로 하는 의존 객체들에 대해서 생성자에서 타입힌트로 지정할 수 있게 됩니다. 의존성은 자동으로 해결되어 컨트롤러가 인스턴스에 주입될 것입니다.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Routing\Controller;
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

Of course, you may also type-hint any [Laravel contract](/docs/{{version}}/contracts). If the container can resolve it, you can type-hint it. 

당연하게도 [라라벨 contract](/docs/{{version}}/contracts)의 형태도 타입 힌트로 지정할 수 있습니다. 컨테이너가 의존성 해결을 할 수 있다면 타입 힌트에 지정할 수는 있습니다. 

#### Method Injection
#### 메소드 주입

In addition to constructor injection, you may also type-hint dependencies on your controller's action methods. For example, let's type-hint the `Illuminate\Http\Request` instance on one of our methods:

생성자 주입과 더불어 컨트롤러의 액션 메소드에서도 타입힌트를 통한 의존성 주입을 할 수 있습니다. 예를 들어 메소드에서 `Illuminate\Http\Request` 인스턴스를 타입힌트를 통해서 주입할 수 있습니다. 

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;
    use Illuminate\Routing\Controller;

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
            $name = $request->input('name');

            //
        }
    }

If your controller method is also expecting input from a route parameter, simply list your route arguments after your other dependencies. For example, if your route is defined like so:

컨트롤러 메소드가 라우트 인자로 부터 입력값을 받아야 한다면 간단하게 라우트 인자를 지정하면 됩니다. 예를 들어 다음과 같이 정의할 수 있습니다.

    Route::put('user/{id}', 'UserController@update');

You may still type-hint the `Illuminate\Http\Request` and access your route parameter `id` by defining your controller method like the following:

아래와 같이 `Illuminate\Http\Request` 를 타입힌트 하면서, 컨트롤러 메소드에서 정의하고있는 `id`에 해당하는 라우트 매개 변수에 액세스 할 수도 있습니다.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;
    use Illuminate\Routing\Controller;

    class UserController extends Controller
    {
        /**
         * Update the specified user.
         *
         * @param  Request  $request
         * @param  int  $id
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

> **Note:** Route caching does not work with Closure based routes. To use route caching, you must convert any Closure routes to use controller classes.

> **주의:** 라우트 캐시는 클로저를 기반으로한 라우트에서는 동작하지 않습니다. 라우트 캐시를 사용하기 위해서는 모든 클로저 기반의 라우트를 컨트롤러를 사용하도록 변경해야 합니다. 

If your application is exclusively using controller based routes, you may take advantage of Laravel's route cache. Using the route cache will drastically decrease the amount of time it takes to register all of your application's routes. In some cases, your route registration may even be up to 100x faster! To generate a route cache, just execute the `route:cache` Artisan command:

애플리케이션이 컨트롤러 기반의 라우트만을 사용하고 있다면 라라벨의 라우트를 캐시하는 장점을 사용할 수 있습니다. 라우트 캐시를 사용하면 애플리케이션의 전체 라우트를 등록하는 데 걸리는 시간의 양을 크게 감소합니다. 경우에 따라서는 라우트 등록이 100배나 빨라질 수도 있습니다! 라우트 캐시를 생성하기 위해서는 `route:cache` 아티즌 명령어를 실행하면 됩니다. 

    php artisan route:cache

That's all there is to it! Your cached routes file will now be used instead of your `app/Http/routes.php` file. Remember, if you add any new routes you will need to generate a fresh route cache. Because of this, you may wish to only run the `route:cache` command during your project's deployment.

저게 전부입니다. 캐시된 라우트 파일이 `app/Http/routes.php` 파일 대신에 사용될것 입니다. 명심할 것은 새로운 라우트를 추가하면 라우트 캐시를 다시 생성해줘야 한다는 것입니다. 이러한점 때문에 `route:cache` 명령어는 프로젝트가 배포되고 나서 실행하는 것이 좋습니다. 

To remove the cached routes file without generating a new cache, use the `route:clear` command: 

캐시를 재생성하는것 말고 캐시를 제거하기 위해서는 `route:clear` 명령어를 실행하면 됩니다. 

    php artisan route:clear
