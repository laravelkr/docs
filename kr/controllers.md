# HTTP Controllers HTTP 컨트롤러

- [Introduction 소개](#introduction)
- [Basic Controllers 기본 사용법](#basic-controllers)
- [Controller Middleware 컨트롤러 미들웨어](#controller-middleware)
- [Implicit Controllers 암시적 컨트롤러](#implicit-controllers)
- [RESTful Resource Controllers RESTful 리소스 컨트롤러](#restful-resource-controllers)
- [Dependency Injection & Controllers 의존성 주입과 컨트롤러](#dependency-injection-and-controllers)
- [Route Caching 라우트 캐싱](#route-caching)

<a name="introduction"></a>
## Introduction 소개

Instead of defining all of your request handling logic in a single `routes.php` file, you may wish to organize this behavior using Controller classes. 요청에 대한 모든 처리 로직을 하나의 `routes.php` 파일에 정의하는 것 보다 별도의 컨트롤러 클래스를 통해서 구성할 수도 있습니다. Controllers can group related HTTP request handling logic into a class. 컨트롤러는 클래스를 구성하여 HTTP 요청에 대한 그룹을 지정합니다. Controllers are typically stored in the `app/Http/Controllers` directory. 컨트롤러는 일반적으로 `app/Http/Controllers` 디렉토리에 저장합니다. 

<a name="basic-controllers"></a>
## Basic Controllers 기본 컨트롤러

Here is an example of a basic controller class:
다음은 기본적인 컨트롤러 클래스의 예제 입니다. 

	<?php namespace App\Http\Controllers;

	use App\Http\Controllers\Controller;

	class UserController extends Controller {

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

> **Note주의:** All controllers should extend the base controller class. 모든 컨트롤러는 base 컨트롤러를 상속받아야 합니다. 

#### Controllers & Namespaces 컨트롤러 & 네임스페이스

It is very important to note that we did not need to specify the full controller namespace, only the portion of the class name that comes after the `App\Http\Controllers` namespace "root". 한가지 반드시 알고 있어야 할 부분은 컨트롤러의 네임스페이스를 지정할 때 전체 네임스페이스를 다 쓸 필요가 없다는 것입니다. 클래스의 이름은 `App\Http\Controllers` 네임스페이스를 “root” 인식하게 됩니다. By default, the `RouteServiceProvider` will load the `routes.php` file within a route group containing the root controller namespace. 기본적으로 `RouteServiceProvider` 가 `routes.php` 파일의 라우트들을 로드할 때 자동으로 이 “root” 네임스페이스를 포함하는 것으로 인식합니다. 

If you choose to nest or organize your controllers using PHP namespaces deeper into the `App\Http\Controllers` directory, simply use the specific class name relative to the `App\Http\Controllers` root namespace. 만약 `App\Http\Controllers` 하위에 중첩된 디렉토리를 구성하여 네임스페이스를 부여할 경우에는 단순히 `App\Http\Controllers`를 루트 네임 스페이스를 기준으로 하여 특정 클래스 이름을 사용하면 됩니다. So, if your full controller class is `App\Http\Controllers\Photos\AdminController`, you would register a route like so: 따라서 만약 컨트롤러가 `App\Http\Controllers\Photos\AdminController` 처럼 구성되어 있다면 다음처럼 라우트를 구성하면 됩니다. 

	Route::get('foo', 'Photos\AdminController@method');

#### Naming Controller Routes 이름이 지정된 컨트롤러 라우트

Like Closure routes, you may specify names on controller routes: 클로저 라우트와 같이 컨트롤러 라우트에 이름을 지정할 수 있습니다. 

	Route::get('foo', ['uses' => 'FooController@method', 'as' => 'name']);

#### URLs To Controller Actions 컨트롤러 액션의 URL 구하기

To generate a URL to a controller action, use the `action` helper method:
컨트롤러 액션에 대한 URL을 생성하기 위해서 `action` 헬퍼 함수를 사용합니다. 

	$url = action('App\Http\Controllers\FooController@method');

If you wish to generate a URL to a controller action while using only the portion of the class name relative to your controller namespace, register the root controller namespace with the URL generator: 단순히 컨트롤러의 전체 네임스페이스의 대신 클래스명만으로 URL 을 생성하고 싶은 경우에는 root 컨트롤러 네임스페이스를 URL 제너레이터에 등록하면 됩니다. 

	URL::setRootControllerNamespace('App\Http\Controllers');

	$url = action('FooController@method');

You may access the name of the controller action being run using the `currentRouteAction` method: 실행중인 컨트롤러 액션의 이름을 찾고자 한다면 `currentRouteAction` 메소드를 사용하면 됩니다. 

	$action = Route::currentRouteAction();

<a name="controller-middleware"></a>
## Controller Middleware 컨트롤러 미들웨어 

[Middleware](/docs/{{version}}/middleware) may be specified on controller routes like so:
[미들웨어](/docs/{{version}}/middleware)는 다음과 같이 컨트롤러 라우트에 지정합니다. 

	Route::get('profile', [
		'middleware' => 'auth',
		'uses' => 'UserController@showProfile'
	]);

Additionally, you may specify middleware within your controller's constructor:
덧붙여 미들웨어를 컨트롤러의 생성자에서 지정할수도 있습니다. 

	class UserController extends Controller {

		/**
		 * Instantiate a new UserController instance.
		 */
		public function __construct()
		{
			$this->middleware('auth');

			$this->middleware('log', ['only' => ['fooAction', 'barAction']]);

			$this->middleware('subscribed', ['except' => ['fooAction', 'barAction']]);
		}

	}

<a name="implicit-controllers"></a>
## Implicit Controllers

Laravel allows you to easily define a single route to handle every action in a controller. 라라벨에서는 한번의 라우팅 등록으로 컨트롤러를 통해 모든 액션들을 처리할 수 있는 손쉬운 방법을 제공합니다. First, define the route using the `Route::controller` method: 먼저 `Route::controller` 메소드를 사용하여 경로를 지정합니다. 

	Route::controller('users', 'UserController');

The `controller` method accepts two arguments. `controller` 메소드는 두개의 인자를 넘겨 받도록 되어 있습니다. The first is the base URI the controller handles, while the second is the class name of the controller. 첫번째 인자는 컨트롤러로 제어할 URI이고, 두번째는 컨트롤러의 클래스명을 의미합니다. Next, just add methods to your controller, prefixed with the HTTP verb they respond to: 이어서 해당하는 HTTP 메소드 이름을 접두어로 (get, post..) 사용하는 형태로 컨트롤러의 메소드를 추가합니다:

	class UserController extends Controller {

		public function getIndex()
		{
			//
		}

		public function postProfile()
		{
			//
		}

		public function anyLogin()
		{
			//
		}

	}

The `index` methods will respond to the root URI handled by the controller, which, in this case, is `users`. 위의 경우에 컨트롤러의 `index` 메소드는 `users` URI에 대한 루트 주소에 대한 결과를 반환합니다. 

If your controller action contains multiple words, you may access the action using "dash" syntax in the URI. 만약 컨트롤러의 메소드가 여러개의 단어로 구성되어 진 형태라면 "-"을 통해서 접속할 수 있는 URI를 제공하게 됩니다. For example, the following controller action on our `UserController` would respond to the `users/admin-profile` URI: 예를들어 `UserController`에 다음과 같은 액션이 정의되었다면 URI는 `users/admin-profile` 과 같이 구성됩니다. 

	public function getAdminProfile() {}

#### Assigning Route Names 
#### 라우트에 이름 지정하기
If you would like to "name" some of the routes on the controller, you may pass a third argument to the `controller` method: 컨트롤러 라우트에 어떤 “이름”을 지정하고자 한다면 `controller` 메소드의 세번째 인자를 통해서 지정할 수 있습니다. 

	Route::controller('users', 'UserController', [
		'anyLogin' => 'user.login',
	]);

<a name="restful-resource-controllers"></a>
## RESTful Resource Controllers
## RESTful 리소스 컨트롤러

Resource controllers make it easier to build RESTful controllers around resources. 리소스 컨트롤러는 리소스에 대한 RESTful 컨트롤러를 손쉽게 구성할 수 있게 해줍니다. For example, you may wish to create a controller that handles HTTP requests regarding "photos" stored by your application. 예를 들어 여러분은 애플리케이션에서 "photos" 를 처리하는 컨트롤러를 생성할 수 있습니다. Using the `make:controller` Artisan command, we can quickly create such a controller: `make:controller` 아티즌 명령어를 통해서 새로운 컨트롤러를 빠르게 생성할 수 있습니다. 

	php artisan make:controller PhotoController

Next, we register a resourceful route to the controller:
이제 생성된 컨트롤러에 리소스풀 라우트를 등록하면 됩니다. 

	Route::resource('photo', 'PhotoController');

This single route declaration creates multiple routes to handle a variety of RESTful actions on the photo resource. 한번의 선언만으로 photo 를 구성하는 RESTful 한 액션에 대한 다양한 라우트를 설정할 수 있습니다. Likewise, the generated controller will already have methods stubbed for each of these actions, including notes informing you which URIs and verbs they handle. 앞에서 직접 개별 메소드를 구성한것과 마찬가지로 생성된 컨트롤러는 각각의 메소드가 처리하는 URI와 액션에 대한 메모와 함께 구성됩니다. 

#### Actions Handled By Resource Controller
#### 리소스풀 컨트롤러에 의해서 구성된 액션들

Verb      | Path                  | Action       | Route Name
----------|-----------------------|--------------|---------------------
GET       | /photo                | index        | photo.index
GET       | /photo/create         | create       | photo.create
POST      | /photo                | store        | photo.store
GET       | /photo/{photo}        | show         | photo.show
GET       | /photo/{photo}/edit   | edit         | photo.edit
PUT/PATCH | /photo/{photo}        | update       | photo.update
DELETE    | /photo/{photo}        | destroy      | photo.destroy

#### Customizing Resource Routes 리소스 라우트 사용자 지정하기

Additionally, you may specify only a subset of actions to handle on the route:
추가적으로, 전체 액션의 일부만을 라우트에서 처리할 수 있게 지정할 수 있습니다. 

	Route::resource('photo', 'PhotoController',
					['only' => ['index', 'show']]);

	Route::resource('photo', 'PhotoController',
					['except' => ['create', 'store', 'update', 'destroy']]);

By default, all resource controller actions have a route name; however, you can override these names by passing a `names` array with your options: 기본적으로 모든 리루스 컨트롤러 액션은 라우트 이름을 가지고 있습니다. 그러나 `names` 옵션 배열을 전달하여 이름을 덮어씌울 수 있습니다. 

	Route::resource('photo', 'PhotoController',
					['names' => ['create' => 'photo.build']]);

#### Handling Nested Resource Controllers
#### 중첩된 리소스 컨트롤러

To "nest" resource controllers, use "dot" notation in your route declaration:
중첩된 리소스를 컨트롤링 하고자 한다면 "."을 이용해서 리소스를 선언하면 됩니다. 

	Route::resource('photos.comments', 'PhotoCommentController');

This route will register a "nested" resource that may be accessed with URLs like the following: `photos/{photos}/comments/{comments}`. 라우터는 URL을 `photos/{photos}/comments/{comments}`와 같은 형태로 중첩되어 리소스를 관리할 수 있게 생성해줍니다. 

	class PhotoCommentController extends Controller {

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

#### Adding Additional Routes To Resource Controllers
#### 리소스 컨트롤러에 추가적인 라우팅 구성하기 

If it becomes necessary to add additional routes to a resource controller beyond the default resource routes, you should define those routes before your call to `Route::resource`:만약 리소스 컨트롤러에 추가적으로 라우팅을 구성해야할 필요가 있다면 `Route::resource`가 호출되기 전에 등록해야합니다. 

	Route::get('photos/popular', 'PhotoController@method');

	Route::resource('photos', 'PhotoController');

<a name="dependency-injection-and-controllers"></a>
## Dependency Injection & Controllers 의존성 주입 & 컨트롤러

#### Constructor Injection 생성자 주입

The Laravel [service container](/docs/{{version}}/container) is used to resolve all Laravel controllers. 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)는 모든 라라벨 컨트롤러의 의존성을 해결하기 위해서 사용됩니다. As a result, you are able to type-hint any dependencies your controller may need in its constructor: 그 결과 컨트롤러가 필요로 하는 의존 객체들에 대해서 생성자에서 타입힌트로 지정할 수 있게 됩니다. 

	<?php namespace App\Http\Controllers;

	use Illuminate\Routing\Controller;
	use App\Repositories\UserRepository;

	class UserController extends Controller {

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

Of course, you may also type-hint any [Laravel contract](/docs/{{version}}/contracts). If the container can resolve it, you can type-hint it. 당연하게도 [라라벨 contract](/docs/{{version}}/contracts)의 형태도 타입 힌트로 지정할 수 있습니다. 컨테이너가 의존성 해결을 할 수 있다면 타입 힌트에 지정할 수는 있습니다. 

#### Method Injection 메소드 인젝션-주입

In addition to constructor injection, you may also type-hint dependencies on your controller's methods. For example, let's type-hint the `Request` instance on one of our methods: 생성자 주입과 더불어 컨트롤러의 메소드에서도 타입힌트를 통한 의존성 주입을 할 수 있습니다. 예를 들어 메소드에서 `Request` 인스턴스를 타입힌트를 통해서 주입할 수 있습니다. 

	<?php namespace App\Http\Controllers;

	use Illuminate\Http\Request;
	use Illuminate\Routing\Controller;

	class UserController extends Controller {

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

If your controller method is also expecting input from a route parameter, simply list your route arguments after your other dependencies: 컨트롤러 메소드가 라우트 인자로 부터 입력값을 받아야 한다면 간단하게 의존성 지정 뒤에 인자를 지정하면 됩니다. 

	<?php namespace App\Http\Controllers;

	use Illuminate\Http\Request;
	use Illuminate\Routing\Controller;

	class UserController extends Controller {

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

> **Note주의:** Method injection is fully compatible with [model binding](/docs/{{version}}/routing#route-model-binding). 메소드 인젝션-주입은 [모델 바인딩](/docs/{{version}}/routing#route-model-binding)에서도 마찬가지로 동작합니다. The container will intelligently determine which arguments are model bound and which arguments should be injected. 컨테이너는어느  인자가 모델 바인딩 되었고 어떤 인자가 주입될것인지 영리하게 판단할 것입니다.

<a name="route-caching"></a>
## Route Caching 라우트 캐시

If your application is exclusively using controller routes, you may take advantage of Laravel's route cache. 애플리케이션이 컨트롤러 라우트만을 사용하고 있다면 라라벨의 라우트 캐시의 이점을 가질 수 있습니다. Using the route cache will drastically decrease the amount of time it take to register all of your application's routes. 라우트 캐시를 사용하면 응용 프로그램의 전체 라우트를 등록하는 데 걸리는 시간의 양을 크게 감소합니다. In some cases, your route registration may even be up to 100x faster! 경우에 따라서는 라우트 등록이 100배나 빨라집니다! To generate a route cache, just execute the `route:cache` Artisan command: 라우트 캐시를 생성하기 위해서는 `route:cache` 아티즌 명령어를 실행하면 됩니다. 

	php artisan route:cache

That's all there is to it! 저게 전부입니다. Your cached routes file will now be used instead of your `app/Http/routes.php` file. 캐시된 라우트 파일이 `app/Http/routes.php` 파일 대신에 사용될것 입니다. Remember, if you add any new routes you will need to generate a fresh route cache. 명심할 것은 새로운 라우트를 추가하면 라우트 캐시를 다시 생성해줘야 한다는 것입니다. Because of this, you may wish to only run the `route:cache` command during your project's deployment. 이러한점 때문에 `route:cache` 명령어는 프로젝트가 배포되고 나서 실행하는 것이 좋습니다. 

To remove the cached routes file without generating a new cache, use the `route:clear` command: 캐시를 재생성하는것 말고 캐시를 제거하기 위해서는 `route:clear` 명령어를 실행하면 됩니다. 

	php artisan route:clear
