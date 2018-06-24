# HTTP 컨트롤러

- [소개](#introduction)
- [기본적인 컨트롤서](#basic-controllers)
- [컨트롤러 미들웨어](#controller-middleware)
- [RESTful 리소스 컨트롤러](#restful-resource-controllers)
    - [Resource 라우트의 일부만 지정하기](#restful-partial-resource-routes)
    - [리소스 라우트 이름 지정하기](#restful-naming-resource-routes)
    - [리소스 라우트 파라미터 이름 지정하기](#restful-naming-resource-route-parameters)
    - [Resource 컨트롤러 라우트에 추가하기](#restful-supplementing-resource-controllers)
- [의존성 주입 & 컨트롤러](#dependency-injection-and-controllers)
- [라우트 캐싱](#route-caching)

<a name="introduction"></a>
## 소개

애플리케이션의 요청에 대한 모든 처리 로직을 하나의 `routes.php` 파일에 정의하는 것 보다 별도의 컨트롤러 클래스를 통해서 구성할 수도 있습니다. 컨트롤러는 클래스를 구성하여 HTTP 요청에 대한 그룹을 지정합니다. 컨트롤러는 `app/Http/Controllers` 디렉토리에 저장 됩니다. 

<a name="basic-controllers"></a>
## 기본적인 컨트롤러

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

다음과 같이 컨트롤러의 액션에 라우트를 지정할 수 있습니다. 

    Route::get('user/{id}', 'UserController@showProfile');

이제 사용자의 요청이 지정된 라우트의 URI와 일치할 때 `UserController` 클래스의 `showProfile` 메소드가 실행될것입니다. 이때, 라우트의 파라미터들 또한 메소드에 전달될 것입니다.

#### 컨트롤러 & 네임스페이스

컨트롤러에 대응하는 라우트를 정의 할 때 컨트롤러의 전체 네임스페이스를 지정할 필요가 없다는 점에 유의하십시오. 클래스를 지정할 때에는, `App\Http\Controllers` 네임스페이스를 "root" 로 인식하게 됩니다. 기본적으로 `RouteServiceProvider` 가 `routes.php` 파일의 라우트들을 로드할 때 자동으로 이 "root" 네임스페이스를 포함하는 것으로 인식합니다. 

만약 `App\Http\Controllers` 하위에 중첩된 디렉토리를 구성하여 네임스페이스를 부여할 경우에는 단순히 `App\Http\Controllers`를 루트 네임 스페이스를 기준으로 하여 특정 클래스 이름을 사용하면 됩니다. 따라서 만약 컨트롤러가 `App\Http\Controllers\Photos\AdminController` 처럼 구성되어 있다면 다음처럼 라우트를 구성하면 됩니다. 

    Route::get('foo', 'Photos\AdminController@method');

#### 이름이 지정된 컨트롤러 라우트

클로저 라우트와 같이 컨트롤러 라우트에 이름을 지정할 수 있습니다. 

    Route::get('foo', ['uses' => 'FooController@method', 'as' => 'name']);

이름이 지정된 컨트롤러의 라우트에 대한 URL을 생성하기 위해서 `route` 헬퍼 함수를 사용할 수 있습니다.

    $url = route('name');

<a name="controller-middleware"></a>
## 컨트롤러 미들웨어

[미들웨어](/docs/{{version}}/middleware)는 다음과 같이 컨트롤러 라우트에 지정할 수 있습니다. 

    Route::get('profile', [
        'middleware' => 'auth',
        'uses' => 'UserController@showProfile'
    ]);

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

            $this->middleware('log', ['only' => [
                'fooAction',
                'barAction',
            ]]);

            $this->middleware('subscribed', ['except' => [
                'fooAction',
                'barAction',
            ]]);
        }
    }

<a name="restful-resource-controllers"></a>
## RESTful 리소스 컨트롤러

리소스 컨트롤러는 리소스에 대한 RESTful 컨트롤러를 어려움없이 구성할 수 있게 해줍니다. 예를 들어 여러분은 애플리케이션에서 "photos" 를 처리하는 컨트롤러를 다음과 같이 생성할 수 있습니다. `make:controller` 아티즌 명령어를 통해서 새로운 컨트롤러를 빠르게 생성할 수 있습니다. 

    php artisan make:controller PhotoController --resource

아티즌 명령어는 `app/Http/Controllers/PhotoController.php` 파일을 생성할 것입니다. 이 컨트롤러는 각각의 resource 에 해당하는 메소드들을 가지고 있을 것입니다. 

이제 생성된 컨트롤러에 resourceful 라우트를 등록하면 됩니다. 

    Route::resource('photo', 'PhotoController');

한번의 선언만으로 photo 를 구성하는 RESTful 한 액션에 대한 다양한 라우트를 설정할 수 있습니다. 앞에서 직접 개별 메소드를 구성한것과 마찬가지로 생성된 컨트롤러는 각각의 메소드가 처리하는 URI와 액션에 대한 메모와 함께 구성됩니다. 

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

HTML form은 PUT, PATCH 또는 DELETE 요청을 만들지 못하기 때문에, hidden 유형의 `_method` 필드를 통해서 HTTP 메소드를 속이도록 해야합니다. 

    <input type="hidden" name="_method" value="PUT">

<a name="restful-partial-resource-routes"></a>
#### Resource 라우트의 일부만 지정하기

resource 라우트를 선언할 때, 액션의 일부만을 지정할 수도 있습니다.

    Route::resource('photo', 'PhotoController', ['only' => [
        'index', 'show'
    ]]);

    Route::resource('photo', 'PhotoController', ['except' => [
        'create', 'store', 'update', 'destroy'
    ]]);

<a name="restful-naming-resource-routes"></a>
#### resource 라우트 이름 지정하기

기본적으로 모든 리루스 컨트롤러 액션은 라우트 이름을 가지고 있습니다. 그러나 `names` 옵션 배열을 전달하여 이름을 덮어씌울 수 있습니다. 

    Route::resource('photo', 'PhotoController', ['names' => [
        'create' => 'photo.build'
    ]]);

<a name="restful-naming-resource-route-parameters"></a>
#### 리소스 라우트 파리미터 이름 지정하기

기본적으로 `Route::resource` 는 리소스 이름을 기반으로한 리소스 라우트들을 위한 라우트 파라미터들을 생성합니다. 여러분은 옵션 배열에 `parameters` 를 전달하여 손쉽게 각각의 리소스 마다 이를 덮어쓸 수 있습니다:   

    Route::resource('user', 'AdminUserController', ['parameters' => [
        'user' => 'admin_user'
    ]]);

위의 예제는 리소스의 `show` 라우트에서 다음의 URI를 생성합니다:

    /user/{admin_user}

파라미터의 이름 배열을 전달하는 대신에 `singular`을 지정하면 라라벨은 기본 파라미터의 이름에 "단수"를 사용합니다:

    Route::resource('users.photos', 'PhotoController', [
        'parameters' => 'singular'
    ]);

    // /users/{user}/photos/{photo}

또는 리소스 라우트 파라미터를 전역으로 단수 설정하거나 글로벌 매핑 할 수 있습니다.

    Route::singularResourceParameters();

    Route::resourceParameters([
        'user' => 'person', 'photo' => 'image'
    ]);

리소스 파라미터들을 커스터마이징 하는 경우, 이름지정의 우선 순위를 인식하는 것이 중요합니다.

1. `Route::resource` 에 명시적으로 전달된 파라미터들.
2. `Route::resourceParameters` 를 통해서 글로벌 파라미터 매핑된 것.
3. `Route::resource` 에 `parameters` 배열을 통해서 `singular` 설정되었거나 `Route::singularResourceParameters` 된것. 
4. 기본 동작.

<a name="restful-supplementing-resource-controllers"></a>
#### Resource 컨트롤러 라우트에 추가하기

만약 리소스 컨트롤러에 추가적으로 라우팅을 구성해야할 필요가 있다면 `Route::resource`가 호출되기 전에 등록해야합니다. 그렇지 않으면 `resource` 메소드에 의해서 정의된 라우트들이 추가한 라우트들 보다 우선하게 되어 버립니다. 

    Route::get('photos/popular', 'PhotoController@method');

    Route::resource('photos', 'PhotoController');

<a name="dependency-injection-and-controllers"></a>
## 의존성 주입 & 컨트롤러

#### 생성자 주입

라라벨의 [서비스 컨테이너](/docs/{{version}}/container)는 모든 라라벨 컨트롤러의 의존성을 해결하기 위해서 사용됩니다. 그 결과 컨트롤러가 필요로 하는 의존 객체들에 대해서 생성자에서 타입힌트로 지정할 수 있게 됩니다. 의존성은 자동으로 해결되어 컨트롤러가 인스턴스에 주입될 것입니다.

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

당연하게도 [라라벨 contract](/docs/{{version}}/contracts)의 형태도 타입 힌트로 지정할 수 있습니다. 컨테이너가 의존성 해결을 할 수 있다면 타입 힌트에 지정할 수는 있습니다. 

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
            $name = $request->input('name');

            //
        }
    }

컨트롤러 메소드가 라우트 인자로 부터 입력값을 받아야 한다면 간단하게 라우트 인자를 지정하면 됩니다. 예를 들어 다음과 같이 정의할 수 있습니다.

    Route::put('user/{id}', 'UserController@update');

아래와 같이 `Illuminate\Http\Request` 를 타입힌트 하면서, 컨트롤러 메소드에서 정의하고있는 `id`에 해당하는 라우트 매개 변수에 액세스 할 수도 있습니다.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;

    class UserController extends Controller
    {
        /**
         * Update the specified user.
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

> **주의:** 라우트 캐시는 클로저를 기반으로한 라우트에서는 동작하지 않습니다. 라우트 캐시를 사용하기 위해서는 모든 클로저 기반의 라우트를 컨트롤러를 사용하도록 변경해야 합니다. 

애플리케이션이 컨트롤러 기반의 라우트만을 사용하고 있다면 라라벨의 라우트를 캐시하는 장점을 사용해야 합니다. 라우트 캐시를 사용하면 애플리케이션의 전체 라우트를 등록하는 데 걸리는 시간의 양을 크게 감소합니다. 경우에 따라서는 라우트 등록이 100배나 빨라질 수도 있습니다! 라우트 캐시를 생성하기 위해서는 `route:cache` 아티즌 명령어를 실행하면 됩니다. 

    php artisan route:cache

저게 전부입니다. 캐시된 라우트 파일이 `app/Http/routes.php` 파일 대신에 사용될것 입니다. 명심할 것은 새로운 라우트를 추가하면 라우트 캐시를 다시 생성해줘야 한다는 것입니다. 이러한점 때문에 `route:cache` 명령어는 프로젝트가 배포될 때 실행해야 합니다.

캐시를 재생성하는것 말고 캐시를 제거하기 위해서는 `route:clear` 명령어를 실행하면 됩니다. 

    php artisan route:clear
