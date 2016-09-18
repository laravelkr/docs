# HTTP 컨트롤러(HTTP Controllers)

- [소개](#introduction)
- [기본 사용법](#basic-controllers)
- [컨트롤러 미들웨어](#controller-middleware)
- [암시적 컨트롤러](#implicit-controllers)
- [RESTful 리소스 컨트롤러](#restful-resource-controllers)
- [의존성 주입과 컨트롤러](#dependency-injection-and-controllers)
- [라우트 캐싱](#route-caching)

<a name="introduction"></a>
## 소개

요청에 대한 모든 처리 로직을 하나의 `routes.php` 파일에 정의하는 것보다 별도의 컨트롤러 클래스를 통해서 구성할 수도 있습니다. 컨트롤러는 클래스를 구성하여 HTTP 요청에 대한 그룹을 지정합니다. 컨트롤러는 일반적으로 `app/Http/Controllers` 디렉토리에 저장합니다.

<!--chak-comment-HTTP-컨트롤러(HTTP-Controllers)-소개-->

<a name="basic-controllers"></a>
## 기본 컨트롤러

다음은 기본적인 컨트롤러 클래스의 예제입니다:

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

다음과 같이 컨트롤러의 액션에 라우트를 지정할 수 있습니다:

	Route::get('user/{id}', 'UserController@showProfile');

> **주의:** 모든 컨트롤러는 base 컨트롤러를 상속받아야 합니다.

#### 컨트롤러 & 네임스페이스

한 가지 반드시 알고 있어야 할 부분은 컨트롤러의 네임스페이스를 지정할 때 전체 네임스페이스를 다 쓸 필요가 없다는 것입니다. 클래스의 이름은 `App\Http\Controllers` 네임스페이스를 “root”로 인식하게 됩니다. 기본적으로 `RouteServiceProvider`가 `routes.php` 파일의 라우트들을 로드할 때 자동으로 이 “root” 네임스페이스를 포함하는 것으로 인식합니다.

만약 `App\Http\Controllers` 하위에 중첩된 디렉토리를 구성하여 네임스페이스를 부여할 경우에는 단순히 `App\Http\Controllers`를 루트 네임 스페이스를 기준으로 하여 특정 클래스 이름을 사용하면 됩니다. 따라서 만약 컨트롤러가 `App\Http\Controllers\Photos\AdminController`처럼 구성되어 있다면 다음처럼 라우트를 구성하면 됩니다.

	Route::get('foo', 'Photos\AdminController@method');

#### 이름이 지정된 컨트롤러 라우트

클로저 라우트와 같이 컨트롤러 라우트에 이름을 지정할 수 있습니다.

	Route::get('foo', ['uses' => 'FooController@method', 'as' => 'name']);

#### 컨트롤러 액션의 URL 구하기

컨트롤러 액션에 대한 URL을 생성하기 위해서 `action` 헬퍼함수를 사용합니다:

	$url = action('App\Http\Controllers\FooController@method');

단순히 컨트롤러의 전체 네임스페이스의 대신 클래스명만으로 URL을 생성하고 싶은 경우에는 root 컨트롤러 네임스페이스를 URL 제너레이터에 등록하면 됩니다:

	URL::setRootControllerNamespace('App\Http\Controllers');

	$url = action('FooController@method');

실행중인 컨트롤러 액션의 이름을 찾고자 한다면 `currentRouteAction` 메소드를 사용하면 됩니다:

	$action = Route::currentRouteAction();

<!--chak-comment-HTTP-컨트롤러(HTTP-Controllers)-기본-컨트롤러-->

<a name="controller-middleware"></a>
## 컨트롤러 미들웨어

[미들웨어](/docs/5.0/middleware)는 다음과 같이 컨트롤러 라우트에 지정합니다.

	Route::get('profile', [
		'middleware' => 'auth',
		'uses' => 'UserController@showProfile'
	]);

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

<!--chak-comment-HTTP-컨트롤러(HTTP-Controllers)-컨트롤러-미들웨어--->

<a name="implicit-controllers"></a>
## 암시적 컨트롤러

라라벨에서는 한번의 라우팅 등록으로 컨트롤러를 통해 모든 액션들을 처리할 수 있는 손쉬운 방법을 제공합니다. 먼저 `Route::controller` 메소드를 사용하여 경로를 지정합니다:

	Route::controller('users', 'UserController');

`controller` 메소드는 두 개의 인자를 넘겨 받도록 되어 있습니다. 첫 번째 인자는 컨트롤러로 제어할 URI이고, 두 번째는 컨트롤러의 클래스명을 의미합니다. 이어서 해당하는 HTTP 메소드 이름을 접두어로 (get, post..) 사용하는 형태로 컨트롤러의 메소드를 추가합니다:

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

위의 경우에 컨트롤러의 `index` 메소드는 `users` URI에 대한 루트 주소에 대한 결과를 반환합니다.

만약 컨트롤러의 메소드가 여러개의 단어로 구성되어 진 형태라면 "-"을 통해서 접속할 수 있는 URI를 제공하게 됩니다. 예를 들어, `UserController`에 다음과 같은 액션이 정의되었다면 URI는 `users/admin-profile`과 같이 구성됩니다:

	public function getAdminProfile() {}

#### 라우트에 이름 지정하기

컨트롤러 라우트에 어떤 “이름”을 지정하고자 한다면 `controller` 메소드의 세 번째 인자를 통해서 지정할 수 있습니다:

	Route::controller('users', 'UserController', [
		'anyLogin' => 'user.login',
	]);

<!--chak-comment-HTTP-컨트롤러(HTTP-Controllers)-암시적-컨트롤러-->

<a name="restful-resource-controllers"></a>
## RESTful 리소스 컨트롤러

리소스 컨트롤러는 리소스에 대한 RESTful 컨트롤러를 손쉽게 구성할 수 있게 해줍니다. 예를 들어, 여러분은 어플리케이션에서 "photos"를 처리하는 컨트롤러를 생성할 수 있습니다. `make:controller` 아티즌 명령어를 통해서 새로운 컨트롤러를 빠르게 생성할 수 있습니다:

	php artisan make:controller PhotoController

이제 생성된 컨트롤러에 리소스풀 라우트를 등록하면 됩니다:

	Route::resource('photo', 'PhotoController');

한번의 선언만으로 photo를 구성하는 RESTful 한 액션에 대한 다양한 라우트를 설정할 수 있습니다. 앞에서 직접 개별 메소드를 구성한것과 마찬가지로 생성된 컨트롤러는 각각의 메소드가 처리하는 URI와 액션에 대한 메모와 함께 구성됩니다.

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

#### 리소스 라우트 사용자 지정하기

추가로, 전체 액션의 일부만을 라우트에서 처리할 수 있게 지정할 수 있습니다:

	Route::resource('photo', 'PhotoController',
					['only' => ['index', 'show']]);

	Route::resource('photo', 'PhotoController',
					['except' => ['create', 'store', 'update', 'destroy']]);

기본적으로 모든 리소스 컨트롤러 액션은 라우트 이름을 가지고 있습니다. 그러나 `names` 옵션 배열을 전달하여 이름을 덮어씌울 수 있습니다.

	Route::resource('photo', 'PhotoController',
					['names' => ['create' => 'photo.build']]);

#### 중첩된 리소스 컨트롤러

중첩된 리소스를 컨트롤링 하고자 한다면 "."을 이용해서 리소스를 선언하면 됩니다:

	Route::resource('photos.comments', 'PhotoCommentController');

라우터는 URL을 `photos/{photos}/comments/{comments}`와 같은 형태로 중첩되어 리소스를 관리할 수 있게 생성해줍니다.

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

#### 리소스 컨트롤러에 추가적인 라우팅 구성하기

만약 리소스 컨트롤러에 추가로 라우팅을 구성해야 할 필요가 있다면 `Route::resource`가 호출되기 전에 등록해야 합니다:

	Route::get('photos/popular', 'PhotoController@method');

	Route::resource('photos', 'PhotoController');

<!--chak-comment-HTTP-컨트롤러(HTTP-Controllers)-RESTful-리소스-컨트롤러-->

<a name="dependency-injection-and-controllers"></a>
## 의존성 주입 & 컨트롤러

#### 생성자 주입

라라벨의 [서비스 컨테이너](/docs/5.0/container)는 모든 라라벨 컨트롤러의 의존성을 해결하기 위해서 사용됩니다. 그 결과 컨트롤러가 필요로 하는 의존 객체들에 대해서 생성자에서 타입힌트로 지정할 수 있게 됩니다:

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

당연하게도 [라라벨 contract](/docs/5.0/contracts)의 형태도 타입 힌트로 지정할 수 있습니다. 컨테이너가 의존성 해결을 할 수 있다면 타입 힌트에 지정할 수는 있습니다.

#### 메소드 인젝션-주입

생성자 주입과 더불어 컨트롤러의 메소드에서도 타입힌트를 통한 의존성 주입을 할 수 있습니다. 예를 들어, 메소드에서 `Request` 인스턴스를 타입힌트를 통해서 주입할 수 있습니다:

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

컨트롤러 메소드가 라우트 인자로부터 입력값을 받아야 한다면 간단하게 의존성 지정 뒤에 인자를 지정하면 됩니다:

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

> **주의:** 메소드 인젝션-주입은 [모델 바인딩](/docs/5.0/routing#route-model-binding)에서도 마찬가지로 동작합니다. 컨테이너는 어느 인자가 모델 바인딩 되었고 어떤 인자가 주입될것인지 영리하게 판단할 것입니다.

<!--chak-comment-HTTP-컨트롤러(HTTP-Controllers)-의존성-주입---컨트롤러-->

<a name="route-caching"></a>
## 라우트 캐시

어플리케이션이 컨트롤러 라우트만을 사용하고 있다면 라라벨의 라우트 캐시의 이점을 가질 수 있습니다. 라우트 캐시를 사용하면 응용 프로그램의 전체 라우트를 등록하는 데 걸리는 시간의 양을 크게 감소합니다. 경우에 따라서는 라우트 등록이 100배나 빨라집니다! 라우트 캐시를 생성하기 위해서는 `route:cache` 아티즌 명령어를 실행하면 됩니다.

	php artisan route:cache

저게 전부입니다. 캐시된 라우트 파일이 `app/Http/routes.php` 파일 대신에 사용될 것입니다. 명심할 것은 새로운 라우트를 추가하면 라우트 캐시를 다시 생성해줘야 한다는 것입니다. 이러한점 때문에 `route:cache` 명령어는 프로젝트가 배포되고 나서 실행하는 것이 좋습니다.

캐시를 재생성하는것 말고 캐시를 제거하기 위해서는 `route:clear` 명령어를 실행하면 됩니다:

	php artisan route:clear

<!--chak-comment-HTTP-컨트롤러(HTTP-Controllers)-라우트-캐시-->
