# 할일 목록 구성하기(중급)

- [시작하기](#introduction)
- [설치하기](#installation)
- [데이터베이스 준비사항](#prepping-the-database)
	- [데이터베이스 마이그레이션](#database-migrations)
	- [Eloquent 모델](#eloquent-models)
	- [Eloquent 관계 정의](#eloquent-relationships)
- [Routing-라우팅](#routing)
	- [뷰 연결하기](#displaying-a-view)
	- [사용자 인증](#authentication-routing)
	- [Task 컨트롤러](#the-task-controller)
- [레이아웃 & 뷰 구성하기](#building-layouts-and-views)
	- [레이아웃 정의하기](#defining-the-layout)
	- [자식 뷰 파일 정의하기](#defining-the-child-view)
- [할일 추가하기](#adding-tasks)
	- [입력값 유효성 검증하기](#validation)
	- [할일 생성하기](#creating-the-task)
- [추가된 할일들 표시하기](#displaying-existing-tasks)
	- [의존성 주입](#dependency-injection)
	- [할일들 보여주기](#displaying-the-tasks)
- [할일 삭제하기](#deleting-tasks)
	- [삭제 버튼 추가하기](#adding-the-delete-button)
	- [라우트 모델 바인딩](#route-model-binding)
	- [Authorization-승인](#authorization)
	- [할일 삭제하기](#deleting-the-task)

<a name="introduction"></a>
## Introduction
## 소개

이 퀵스타트 가이드는 중급 이상의 사용자들을 위해서 라라벨 프레임워크에 대한 소개와 데이터베이스 마이그레이션, Eloquent ORM, 라우팅, validation-검증, views-뷰, 블레이드 템플릿과 관련한 내용을 포함하고 있습니다. 라라벨 프레임워크와 PHP 프레임워크에 대한 기본적인 지식을 가지고 있다면 좋은 출발점이 될 것입니다.

라라벨의 기본 기능들에 대한 샘플을 확인하기 위해서 간단하게 할일 목록을 만들고, 진행하고자 하는 모든 할일들을 트래킹할 수 있게 할 것입니다. ("해야 할 일" 목록) "기본" 퀵스타트와 달리 이 튜토리얼은 사용자들이 계정을 생성하고 애플리케이션을 통해 인증을 할 수 있도록 할것입니다. 이 프로젝트의 완성된 소스 코드는 [Github 에 공개되어 있습니다](https://github.com/laravel/quickstart-intermediate).

<a name="installation"></a>
## 설치하기

가장 먼저 라라벨 프레임워크를 새롭게 설치해야 합니다. [홈스테드 가상머신](/docs/{{version}}/homestead)을 사용하거나, 또는 여러분이 원하는 로컬 PHP 환경을 선택하여 프레임워크를 실행할 수 있습니다. 로컬 개발 환경이 준비되면 Composer를 이용해 라라벨 프레임워크를 설치할 수 있습니다.

	composer create-project laravel/laravel quickstart --prefer-dist

이제부터 나오는 내용은 눈으로 보기만 하셔도 됩니다. 하지만 이 퀵스타트의 소스 코드를 다운받아서 로컬 개발 머신에 실행하고 싶으시다면 Git 저장소를 복제하여 의존 패키지들을 설치하면 됩니다.

	git clone https://github.com/laravel/quickstart-intermediate quickstart
	cd quickstart
	composer install
	php artisan migrate

로컬 라라벨 개발 환경 구성에 대해 더 많은 정보를 얻기 원하신다면 [홈스테드](/docs/{{version}}/homestead)와 [설치하기](/docs/{{version}}/installation) 메뉴얼을 확인하십시오.

<a name="prepping-the-database"></a>
## 데이터베이스 준비사항

<a name="database-migrations"></a>
### 데이터베이스 마이그레이션

먼저 마이그레이션을 이용하여 모든 할일 목록을 저장할 데이터베이스를 정의합니다. 라라벨의 데이터베이스 마이그레이션은 유연하고 다양한 표현이 가능한 PHP 코드를 사용하여 데이터베이스 테이블 구조를 정의하고, 또 수정하기에 용이합니다. 협업을 진행하는 팀 멤버들에게 데이터베이스의 복사본에 복사하여 수동으로 컬럼을 추가하라고 하는 대신에, 여러분이 소스 제어에 추가한 마이그레이션 파일을 그냥 실행하기만하면 됩니다.

#### `users` 테이블

사용자들이 애플리케이션 내에서 계정을 생성할 수 있도록 할 것이기 때문에, 모든 사용자 정보를 저장할 테이블이 필요합니다. 다행히 라라벨은 기본적인 `users` 테이블을 생성하는 마이그레이션 파일을 이미 가지고 있기 때문에, 수동으로 생성할 필요가 없습니다. `users` 테이블을 생성하기 위한 기본 마이그레이션은 `database/migrations` 디렉토리에서 찾을 수 있습니다.

#### `tasks` 테이블

그럼 이제 모든 할일들을 저장할 데이터베이스 테이블을 만들겠습니다. [아티즌 CLI](/docs/{{version}}/artisan)을 사용하여 다양한 클래스를 생성하면 라라벨 프로젝트들을 구성할 때, 매번 타이핑할 필요가 없어서 효율성을 높일 수 있습니다. 이 경우에서는 `make:migration`명령어를 사용하여 `tasks` 테이블을 위한 새로운 데이터베이스 마이그레이션 파일을 생성합니다:

	php artisan make:migration create_tasks_table --create=tasks

마이그레이션 파일은 프로젝트의 `database/migrations` 디렉토리에 놓일 것입니다. 이미 알고 있을 수도 있듯이 `make:migration` 커맨드는 마이그레이션 파일에 auto-increment ID와 타임스탬프를 추가하였습니다. 이 파일을 수정하여 추가적으로 해야할 일들의 이름을 위한 `string` 컬럼과 `tasks` 와 `users` 테이블을 연결하는 `user_id` 컬럼을 추가합시다.

	<?php

	use Illuminate\Database\Schema\Blueprint;
	use Illuminate\Database\Migrations\Migration;

	class CreateTasksTable extends Migration
	{
	    /**
	     * Run the migrations.
	     *
	     * @return void
	     */
	    public function up()
	    {
	        Schema::create('tasks', function (Blueprint $table) {
	            $table->increments('id');
	            $table->integer('user_id')->index();
	            $table->string('name');
	            $table->timestamps();
	        });
	    }

	    /**
	     * Reverse the migrations.
	     *
	     * @return void
	     */
	    public function down()
	    {
	        Schema::drop('tasks');
	    }
	}

`migrate` 아티즌 커맨드를 이용하여 마이그레이션들을 실행합니다. 홈스테드를 이용하고 있다면 호스트 머신에서 데이터베이스에 직접 접속할 수 없기 때문에 홈스테드 가상 머신 안에서 커맨드를 실행해야 합니다.

	php artisan migrate

이 커맨드는 모든 데이터베이스 테이블을 생성합니다. 특정 데이터베이스 클라이언트를 사용하여 데이터베이스를 확인하면 마이그레이션 테이블에 추가한 컬럼들을 포함한 새 `tasks` 테이블과 `users` 테이블을 확인할 수 있을 것입니다. 이제 Eloquent ORM 모델을 정의할 수 있습니다.

<a name="eloquent-models"></a>
### Eloquent 모델

[Eloquent](/docs/{{version}}/eloquent)은 라라벨의 기본 ORM입니다. Eloquent를 사용하면 명료하게 정의된 "모델"들을 이용하여 데이터베이스에 데이터를 저장하고 조회하기 용이합니다. 보통 각 Eloquent 모델은 하나의 데이터베이스 테이블에 대응합니다.

#### `User` 모델

먼저 `users` 데이터베이스 테이블에 대응하는 모델이 필요합니다. 그러나, `app` 디렉토리를 확인해 보면면 라라벨이 이미 `User` 모델을 제공한다는 것을 알 수 있으며 그렇기 때문에 수동적으로 생성하지 않아도 됩니다.

#### `Task` 모델

이제 방금 생성한 `tasks` 데이터베이스 테이블에 대응하는 `Task` 모델을 정의합니다. 이번에도 아티즌 커맨드를 통해 이 모델을 생성할 수 있습니다. 이 경우 `make:model` 명령어를 사용합니다:

	php artisan make:model Task

이 모델은 애플리케이션의 `app` 디렉토리에 위치할 것입니다. 모델 클래스는 기본적으로 비어있습니다. Eloquent 모델은 데이터베이스 테이블이 모델 이름의 복수형태라 추정하기 때문에 직접적으로 어느 테이블에 대응하는지 명시할 필요가 없습니다. 그렇기 때문에 이 경우 `Task` 모델은 `tasks` 데이터베이스 테이블에 대응할 것으로 추정됩니다. 아래는 빈 모델이 어떻게 나타나야 하는지 보여줍니다.

모델에 몇 가지를 추가해 보겠습니다. 먼저 모델의 `name` 속성을 "mass-assignable"이라고 명시합니다.

	<?php

	namespace App;

	use Illuminate\Database\Eloquent\Model;

	class Task extends Model
	{
	    /**
	     * The attributes that are mass assignable.
	     *
	     * @var array
	     */
	    protected $fillable = ['name'];
	}

모델을 애플리케이션에 라우트에 추가하는 것과 같이 Eloquent 모델의 사용법에 대해서 배워볼 것입니다. 더 많은 정보를 얻고 싶으시다면 [Eloquent 문서](/docs/{{version}}/eloquent)를 참고 하십시오.

<a name="eloquent-relationships"></a>
### Eloquent 관계 정의하기

모델들을 정의하고 나면, 이것들을 서로 연결시켜야 합니다. 예를 들어서 `User`는 많은 `Task`를 가질 수 있지만 하나의 `Task`는 하나의 `User`에만 할당될 수 있습니다. 하나의 관계를 정의하는 것은 다음 처럼 모델에서 관계를 통해서 유연한 접근이 가능하게 할것입니다:

	$user = App\User::find(1);

	foreach ($user->tasks as $task) {
		echo $task->name;
	}

#### `tasks` 관계 정의하기

먼저 `User` 모델에 `tasks`과의 관계들을 정의해 보겠습니다. Eloquent 관계들은 모델에 대한 메소드로써 정의됩니다. Eloquent는 여러종류의 관계들을 표현할 수 있도록 지원하고 있으니 반드시 [Eloquent 문서](/docs/{{version}}/eloquent-relationships)에서 더 많은 정보를 확인하시기 바랍니다. 이 경우에서는, Eloquent가 제공하는 `hasMany` 메소드를 호출하는 `tasks` 함수를 `User` 모델에 정의할 것입니다:

	<?php

	namespace App;

	// Namespace Imports...

	class User extends Model implements AuthenticatableContract,
	                                    AuthorizableContract,
	                                    CanResetPasswordContract
	{
	    use Authenticatable, Authorizable, CanResetPassword;

	    // Other Eloquent Properties...

	    /**
	     * Get all of the tasks for the user.
	     */
	    public function tasks()
	    {
	        return $this->hasMany(Task::class);
	    }
	}

#### `user` 관계 정의하기

다음으로 `Task` 모델에 `user`와의 관계를 정의해 보겠습니다. 이번에도 관계를 모델에 대한 메소드로써 정의할 것입니다. 이 경우에서, Eloquent가 제공하는 `belongTo` 메소드를 통해 관계를 정의할 것입니다:

	<?php

	namespace App;

	use App\User;
	use Illuminate\Database\Eloquent\Model;

	class Task extends Model
	{
	    /**
	     * The attributes that are mass assignable.
	     *
	     * @var array
	     */
	    protected $fillable = ['name'];

	    /**
	     * Get the user that owns the task.
	     */
	    public function user()
	    {
	        return $this->belongsTo(User::class);
	    }
	}

좋습니다! 관계들이 정의되었으니, 이제 컨트롤러 구성을 시작할 수 있습니다.

<a name="routing"></a>
## 라우팅

할일 목록 애플리케이션의 [기본 버전](/docs/{{version}}/quickstart)에서는 모든 로직을 `routes.php` 파일의 클로저들을 이용해 정의했습니다. 이 어플레케이션에서는 라우트를 생성할 때 거의 대부분 [컨트롤러](/docs/{{version}}/controllers)를 사용할 것입니다. 컨트롤러를 통해서 HTTP request-요청을 처리하는 로직을 여러개의 파일로 쪼갤 수 있기 때문에, 더 나은 체계를 가능하게 해줍니다.

<a name="displaying-a-view"></a>
### 뷰 연결하기

`/`를 위한 라우트는 Closure를 사용하는 하나의 라우트로, 단순히 애플리케이션 방문자들의 랜딩 페이지가 될것입니다. 이제 `/` 라우트를 채우도록 하겠습니다. 이 라우트에서 "웰컴" 페이지를 가지는 HTML 템플릿을 렌더링할 수 있도록 합니다.

라라벨에서는 `resources/views` 디렉토리에 모든 HTML 템플릿들이 저장되어 있으며 `view` 헬퍼를 통해 라우트에서 이 템플릿들을 반환할 수 있습니다:

	Route::get('/', function () {
		return view('welcome');
	});

물론 먼저 이 뷰 파일을 실제로 정의해야 합니다. 조금 있다가 이것을 할것입니다.

<a name="authentication-routing"></a>
### 사용자 인증

사용자들이 계정을 생성하고 애플리케이션에 로그인할 수 있도록 하는 것 또한 필요합니다. 보통 인증 레이어 전체를 웹 애플리케이션에 구성하려면, 지겨운 과정을 거쳐야 합니다. 하지만 공통적으로 필요한 사항이기 때문에, 라라벨은 이러한 작업을 전혀 고통스럽지 않게 처리해줍니다.

우선 라라벨 애플리케이션에 이미 `app/Http/Controllers/Auth/AuthController`가 포함된 것을 확인할 수 있습니다. 이 컨트롤러는 사용자의 계정을 생성하고 승인하는데 필요한 모든 로직을 포함한 `AuthenticatesAndRegistersUsers` 트레이트-trait 을 이용합니다.

#### 인증 라우트

이제 남은 작업이 무엇일까요? 아직 사용자 등록과 로그인 템플릿을 생성하고 사용자 인증 컨트롤러를 위한 라우트를 정의해야 합니다. 먼저 `app/Http/routes.php` 파일에 필요한 라우트들을 추가해보겠습니다:

	// Authentication Routes...
	Route::get('auth/login', 'Auth\AuthController@getLogin');
	Route::post('auth/login', 'Auth\AuthController@postLogin');
	Route::get('auth/logout', 'Auth\AuthController@getLogout');

	// Registration Routes...
	Route::get('auth/register', 'Auth\AuthController@getRegister');
	Route::post('auth/register', 'Auth\AuthController@postRegister');

#### 인증 뷰-views 파일 구성하기

사용자 인증을 위해서는 `resources/views/auth` 디렉토리 내에 `login.blade.php` 와 `register.blade.php`을 생성해야 합니다. 물론 이 뷰들의 디자인과 스타일은 중요하지 않습니다. 하지만 최소한 몇가지 기본적인 필드를 가지고 있어야만 합니다.

`register.blade.php` 파일은 `name`, `email`, `password`, and `password_confirmation` 필드들을 포함하고 `/auth/register` 라트에 `POST` 요청을 하는 form을 가지고 있어야 합니다.

`login.blade.php` 파일은 `email`과 `password` 필드들을 포함하고 `/auth/login` 에 `POST` 요청을 하는 form을 가지고 있어야 합니다.

> **Note:** 이 뷰파일들의 완성된 예제들을 보고 싶으시다면 [GitHub](https://github.com/laravel/quickstart-intermediate)에서 전체 소스 코드를 보실 수 있습니다.

<a name="the-task-controller"></a>
### Task 컨트롤러

할일들을 획득하고 저장하기 위해서 아티즌 CLI를 이용해 `TaskController` 를 생성해야 하며 이를 통해 생성된 컨트롤러는 `app/Http/Controllers` 에 위치하게 됩니다.

	php artisan make:controller TaskController --plain

이제 컨트롤러가 생성되었으니 `app/Http/routes.php` 파일에 컨트롤러를 가리키는 라우트들을 생성해보겠습니다:

	Route::get('/tasks', 'TaskController@index');
	Route::post('/task', 'TaskController@store');
	Route::delete('/task/{task}', 'TaskController@destroy');

#### 모든 할일 관련 라우트들에 대한 인증

이 애플리케이션에서는 모든 할일 라우트들이 사용자들의 인증을 요구하도록 합니다. 즉, 할일를 생성하기 위해서, 사용자는 애플리케이션에 "로그인한" 상태여야만 합니다. 그렇기 때문에 승인된 사용자들에만 할일 라우트에 접근할 수 있도록 제한을 해야 합니다. 라라벨은 [미들웨어](/docs/{{version}}/middleware)을 통해 이 과정을 손쉽게 만듭니다.

컨트롤러의 모든 동작이 사용자들이 승인되었음을 조건으로 가능하게 하려면, 컨트롤러의 생성자에 `middleware` 메소드 호출을 추가할 수 있습니다. 사용할 수 있는 모든 라우트 미들웨어는 `app/Http/Kernel.php` 에 정의되어 있습니다. 이 경우 컨트롤러의 모든 동작에 `auth` 미들웨어를 할당합니다:

	<?php

	namespace App\Http\Controllers;

	use App\Http\Requests;
	use Illuminate\Http\Request;
	use App\Http\Controllers\Controller;

	class TaskController extends Controller
	{
	    /**
	     * Create a new controller instance.
	     *
	     * @return void
	     */
	    public function __construct()
	    {
	        $this->middleware('auth');
	    }
	}

<a name="building-layouts-and-views"></a>
## 레이아웃 & 뷰 구성하기

이 애플리케이션은 현재의 할일 목록과 새 할일를 추가하는 형식을 가진 view를 하나만 가지고 있습니다. View를 시각화하는데 도움을 주기 위해 아래에 기본적인 부트스트랩 CSS 스타일을 적용한 완성된 애플리케이션의 스크린샷을 제공합니다:

![Application Image](https://laravel.com/assets/img/quickstart/basic-overview.png)

<a name="defining-the-layout"></a>
### 레이아웃 정의하기

거의 모든 웹애플리케이션은 모든 페이지에 동일한 레이아웃을 사용합니다. 예를 들어 이 애플리케이션은 모든 페이지 위에(페이지가 하나 이상일 경우) 탐색 바가 표시됩니다. 라라벨은 블레이드 **layouts**를 통해 이런 공통의 구성을 모든 페이지에 적용하기 쉽게 해줍니다.

앞서 얘기한 바와 같이, 라라벨의 모든 뷰 파일들은 `resources/views`에 저장되어 있습니다. 그럼 이제 `resources/views/layouts/app.blade.php`에 새 레이아웃 뷰 파일를 정의하겠습니다. `.blade.php` 익스텐션은 뷰를 렌더링하기 위해 [블레이드 템플릿 엔진](/docs/{{version}}/blade)를 이용하라고 프레임워크에 지시합니다. 물론 라라벨은 순수 PHP 템플릿들을 사용할 수 있습니다. 하지만 블레이드는 더 깨끗하고 간략한 템플릿을 쓸 수 있는 편리한 방법입니다.

`app.blade.php` 뷰 파일은 다음과 같습니다:

    // resources/views/layouts/app.blade.php

	<!DOCTYPE html>
	<html lang="en">
		<head>
			<title>Laravel Quickstart - Intermediate</title>

			<!-- CSS And JavaScript -->
		</head>

		<body>
			<div class="container">
				<nav class="navbar navbar-default">
					<!-- Navbar Contents -->
				</nav>
			</div>

			@yield('content')
		</body>
	</html>

레이아웃의 `@yield('content')` 부분을 주목하십시오. 이것은 레이아웃을 상속받는 모든 자식 페이지들이 어느 곳에 자신의 콘텐츠를 주입할 수 있는지 지정해주는 특별한 블레이드 지시어 입니다. 이제 이 레이아웃을 사용하고 주요 콘텐츠를 제공할 자식 뷰를 정의해 보겠습니다.

<a name="defining-the-child-view"></a>
### 자식 뷰 파일 정의하기

이제 애플리케이션의 레이아웃이 완성되었습니다. 다음은 현재 할일 목록 테이블과 새로운 할일을 생성하는 form을 가진 뷰를 정의해야 합니다. `resources/views/tasks.blade.php`에서 뷰파일을 정의합니다.

몇몇 부트스트랩 CSS 보일러플레이트는 넘어가고 중요한 부분에만 집중하겠습니다. [GitHub](https://github.com/laravel/quickstart-intermediate)에서 이 애플리케이션을 위한 전체 소스를 다운받으실 수 있습니다:

    // resources/views/tasks/index.blade.php

	@extends('layouts.app')

	@section('content')

        <!-- Bootstrap Boilerplate... -->

		<div class="panel-body">
            <!-- Display Validation Errors -->
			@include('common.errors')

			<!-- New Task Form -->
			<form action="/task" method="POST" class="form-horizontal">
				{{ csrf_field() }}

                <!-- Task Name -->
				<div class="form-group">
					<label for="task-name" class="col-sm-3 control-label">Task</label>

					<div class="col-sm-6">
						<input type="text" name="name" id="task-name" class="form-control">
					</div>
				</div>

                <!-- Add Task Button -->
				<div class="form-group">
					<div class="col-sm-offset-3 col-sm-6">
						<button type="submit" class="btn btn-default">
							<i class="fa fa-plus"></i> Add Task
						</button>
					</div>
				</div>
			</form>
		</div>

		<!-- TODO: Current Tasks -->
	@endsection

#### 간단한 설명

다음으로 넘어가기 전에 이 템플릿에 대하여 잠시 알아보겠습니다. 먼저 `@extends` 명령은 `resources/views/layouts/app.blade.php`에서 정의한 레이아웃을 사용한다는 것을 블레이드에 알려줍니다. `@section('content')` 와 `@endsection` 사이에 존재하는 모든 콘텐츠는 `app.blade.php` 레이아웃 안에 있는 `@yield('content')`에 주입될 것입니다.

애플리케이션을 위한 기본적인 레이아웃과 뷰가 정의되었습니다. 이제 `TaskController`의 `index` 메소드에서 이 뷰를 반환합니다.

    /**
     * Display a list of all of the user's task.
     *
     * @param  Request  $request
     * @return Response
     */
	public function index(Request $request)
	{
		return view('tasks.index');
	}

이제, 넘어오는 form 입력값들을 처리하고 새 할일을 데이터베이스에 추가하기 위해 `POST /task` 라우트에 코드를 추가할 수 있습니다.

> **주의:** `@include('common.errors')` 지시어는 `resources/views/common/errors.blade.php`에 위치한 템플릿을 로드할 것입니다. 이 템플릿은 아직 정의되지 않았으며 다음에서 정의하겠습니다.

<a name="adding-tasks"></a>
## 할일 추가하기

<a name="validation"></a>
### 입력값 유효성 검증하기

이제 뷰에 form이 있으며 유입되는 form 입력값을 검증하고 새로운 할일를 생성하기 위해서 `POST /task` 라우트에 코드를 추가해야 합니다. 먼저 입력값을 검증하겠습니다.

이 form은 `name` 필드를 필수값으로 하고 `255` 자 미만이어야 한다는 내용을 담도록 할 것입니다. 입력값 검증이 실패 시에 사용자는 `/` URL로 리다이렉트 되며 이전에 입력한 값들과 에러는 [session](/docs/{{version}}/session)에 임시 저장합니다.

    /**
     * Create a new task.
     *
     * @param  Request  $request
     * @return Response
     */
	public function store(Request $request)
	{
		$this->validate($request, [
			'name' => 'required|max:255',
		]);

		// Create The Task...
	}

만약에 [기본적인 퀵스타트](/docs/{{version}}/quickstart)를 함께 보고 계셨다면 이 입력값 검증 코드가 다르다는 것을 알 수 있을 것입니다. 컨트롤러 내에 있기 때문에 기본 라라벨 컨트롤러에 포함되어 있는 `ValidatesRequests` 트레이트-trait의 편리함을 이용할 수 있습니다. 이 트레이트-trait은 request-요청과 입력값 검증 규칙들의 배열을 받아들이는 단순한 `validate` 메소드를 제공합니다.

수동으로 입력값 검증이 실패했는지 판단하거나 수동으로 리다이렉트 할 필요도 없습니다. 주어진 규칙에 대해서 입력값 검증이 실패한다면 사용자는 자동으로 시작한 지점으로 돌아가게 되고 오류들이 자동으로 세션에 저장되어질 것입니다.

#### `$errors` 변수

form의 입력값 검증 오류를 뷰 안에서 표현하기 위해 `@include('common.errors')` 지시어를 사용하였습니다. `common.errors` 는 입력값 검증의 오류가 동일한 형식으로 모든 페이지에 나타나도록 해줍니다. 이제 이 뷰의 컨텐츠를 정의하겠습니다:

    // resources/views/common/errors.blade.php

    @if (count($errors) > 0)
        <!-- Form Error List -->
        <div class="alert alert-danger">
            <strong>Whoops! Something went wrong!</strong>

            <br><br>

            <ul>
                @foreach ($errors->all() as $error)
                    <li>{{ $error }}</li>
                @endforeach
            </ul>
        </div>
    @endif


> **주의:** `$errors` 변수는 **모든** 라라벨 뷰에서 사용 가능합니다. 입력값 검증의 오류가 존재하지 않는 경우에는, 에러 변수는 단순히 `ViewErrorBag`의 비어 있는 인스턴스일 것입니다.

<a name="creating-the-task"></a>
### 할일 생성하기

이제, 입력값 검증 처리되었으니, 라우트를 계속해서 채워나가면서 새로운 할일를 생성해 보겠습니다. 새로운 할일이 생성되면 사용자는 `/` URL로 리다이렉트 될것입니다. 할일를 생성하기 위해 Eloquent의 관계를 설정한 기능을 활용하겠습니다.

대부분의 라라벨의 관계-relationships들은 `create` 메소드를 제공하며, 이 메소드는 속성값들의 배열을 전달 받아 데이터베이스에 저장하기 전에 자동으로 관련 모델에 외래 키(foreign key) 값을 설정합니다. 이 경우 `create` 메소드는 자동으로 현재 승인된 사용자의 ID에 할일의 `user_id` 속성을 설정할 것입니다. 승인된 사용자의 아이디는 `$request->user()`를 통해 접근합니다:

    /**
     * Create a new task.
     *
     * @param  Request  $request
     * @return Response
     */
    public function store(Request $request)
    {
        $this->validate($request, [
            'name' => 'required|max:255',
        ]);

        $request->user()->tasks()->create([
            'name' => $request->name,
        ]);

        return redirect('/tasks');
    }

좋습니다. 이제 성공적으로 할일를 생성할 수 있습니다. 다음으로, 존재하는 모든 할일 목록을 뷰에 계속 추가하도록 하겠습니다.

<a name="displaying-existing-tasks"></a>
## 추가된 할일들 표시하기

먼저 모든 할일들을 뷰에 전달하기 위해서 `TaskController@index` 메소드를 수정해야 합니다. `view` 함수는 두번째 인자로 뷰에서 사용되어질 데이터의 배열을 전달 받고,  배열의 각 키는 뷰에서 변수가 될것입니다. 예를 들어 다음처럼 할 수 있습니다:

    /**
     * Display a list of all of the user's task.
     *
     * @param  Request  $request
     * @return Response
     */
    public function index(Request $request)
    {
    	$tasks = Task::where('user_id', $request->user()->id)->get();

        return view('tasks.index', [
            'tasks' => $tasks,
        ]);
    }

하지만 그전에, `TaskController`에, 모든 데이터를 접근하기 위해서 사용될 `TaskRepository`를 주입하도록 라라벨의 의존성 주입에 대해서 알아보도록 하겠습니다.

<a name="dependency-injection"></a>
### 의존성 주입

[서비스 컨테이너](/docs/{{version}}/container)는 라라벨 프레임워크의 가장 강력한 기능 중 하나입니다. 이 퀵스타트를 읽은 뒤 컨테이너에 대한 문서를 꼭 읽어 보시기 바랍니다.

#### Repository 생성하기

앞서 이야기한것처럼, `Task` 모델의 모든 데이터에 엑세스 할 수 있는 로직을 포함하는 `TaskRepository`를 정의해야 합니다. 이렇게 하는 것은 애플리케이션의 규모가 커지거나, Eloqent 쿼리를 애플리케이션 전반 걸쳐서 공유해야 할 때 특히 유용합니다.

그럼 이제, `app/Repositories` 디렉토리를 생성하고 `TaskRepository` 클래스를 추가합니다. 라라벨의 모든 `app` 폴더들은 PSR-4 기준을 적용하여 오토로딩이 설정 되어 있기 때문에, 추가적으로 필요한 디렉토리들을 자유롭게 생성할 수 있습니다.

	<?php

	namespace App\Repositories;

	use App\User;
	use App\Task;

	class TaskRepository
	{
	    /**
	     * Get all of the tasks for a given user.
	     *
	     * @param  User  $user
	     * @return Collection
	     */
	    public function forUser(User $user)
	    {
	        return Task::where('user_id', $user->id)
	                    ->orderBy('created_at', 'asc')
	                    ->get();
	    }
	}

#### Repository 주입하기

Repository가 정의되었다면 이를 `TaskController` 의 컨트롤러에 단순히 "타입-힌트"하고 `index` 라우트 안에서 활용할 수 있습니다. 라라벨은 모든 컨트롤러의 의존성을 해결하기 위해서 컨테이너를 사용하기 때문에 의존 객체들은 컨트롤러 인스턴스에 자동으로 주입될 것입니다.:

	<?php

	namespace App\Http\Controllers;

	use App\Task;
	use App\Http\Requests;
	use Illuminate\Http\Request;
	use App\Http\Controllers\Controller;
	use App\Repositories\TaskRepository;

	class TaskController extends Controller
	{
	    /**
	     * The task repository instance.
	     *
	     * @var TaskRepository
	     */
	    protected $tasks;

	    /**
	     * Create a new controller instance.
	     *
	     * @param  TaskRepository  $tasks
	     * @return void
	     */
	    public function __construct(TaskRepository $tasks)
	    {
	        $this->middleware('auth');

	        $this->tasks = $tasks;
	    }

	    /**
	     * Display a list of all of the user's task.
	     *
	     * @param  Request  $request
	     * @return Response
	     */
	    public function index(Request $request)
	    {
	        return view('tasks.index', [
	            'tasks' => $this->tasks->forUser($request->user()),
	        ]);
	    }
	}

<a name="displaying-the-tasks"></a>
### 할일들 보여주기

데이터가 전달되고 나면 `tasks.blade.php` 뷰에 있는 할일들을 테이블에 나타낼 수 있습니다. `@foreach` 블레이드 지시어는 빠르고 단순한 PHP 코드로 컴파일되는 간결한 반복문을 사용할 수 있도록 해줍니다:

	@extends('layouts.app')

	@section('content')
        <!-- Create Task Form... -->

        <!-- Current Tasks -->
        @if (count($tasks) > 0)
            <div class="panel panel-default">
                <div class="panel-heading">
                    Current Tasks
                </div>

                <div class="panel-body">
                    <table class="table table-striped task-table">

                        <!-- Table Headings -->
                        <thead>
                            <th>Task</th>
                            <th>&nbsp;</th>
                        </thead>

                        <!-- Table Body -->
                        <tbody>
                            @foreach ($tasks as $task)
                                <tr>
                                    <!-- Task Name -->
                                    <td class="table-text">
                                        <div>{{ $task->name }}</div>
                                    </td>

                                    <td>
                                        <!-- TODO: Delete Button -->
                                    </td>
                                </tr>
                            @endforeach
                        </tbody>
                    </table>
                </div>
            </div>
        @endif
	@endsection

할일 관리 애플리케이션이 이제 거의 완성되었습니다. 하지만 할일이 완료된 후 삭제하는 방법이 아직 없습니다. 다음은 이를 추가합니다.

<a name="deleting-tasks"></a>
## 할일 삭제하기

<a name="adding-the-delete-button"></a>
### 삭제 버튼 추가하기

코드에서 삭제 버튼이 있어야할 곳에 "TODO" 라고 표시해놓았었습니다. 이제, `tasks.blade.php` 뷰 안에 있는 할일 리스트의 각 열에 작은 삭제 버튼을 생성합니다. 목록의 각 할일에 작은 버튼 form을 만들 것입니다. 버튼을 클릭하면 `DELETE /task` request-요청이 애플리케이션에 전달되어 `TaskController@destroy` 메소드를 실행할 것입니다:

    <tr>
        <!-- Task Name -->
        <td class="table-text">
            <div>{{ $task->name }}</div>
        </td>

        <!-- Delete Button -->
        <td>
            <form action="/task/{{ $task->id }}" method="POST">
                {{ csrf_field() }}
                {{ method_field('DELETE') }}

                <button>Delete Task</button>
            </form>
        </td>
    </tr>

<a name="a-note-on-method-spoofing"></a>
#### 메소드 Spoofing-속이기

`Route::delete` 라우트를 이용해서 request요청을 처리하지만, 삭제 버튼의 form `method`가 `POST`로 되어 있다는 것에 주의하십시오. HTML form은 단지 `GET` 과 `POST` HTTP 메소드만을 허용하기 때문에 form에 `DELETE` 요청을 흉내낼 수 있는 방법이 필요합니다.

form 안에서 `method_field('DELETE')` 함수의 결과를 출력하여 `DELETE` request-요청으로 흉내낼 수 있습니다. 이 기능은 hidden 형식의 input을 생성하고 라라벨은 이를 인식하여 실제 HTTP 요청 메소드를 대체할 것입니다. 생성된 필드는 다음과 같을 것입니다:

	<input type="hidden" name="_method" value="DELETE">

<a name="route-model-binding"></a>
### 라우트 모델 바인딩

이제 `TaskController` 에 `destroy` 를 정의할 준비가 거의 다 되었습니다. 하지만, 우선 이 라우트의 선언부분을 다시 확인해봅시다:

	Route::delete('/task/{task}', 'TaskController@destroy');

아무런 코드의 추가 없이도, 라라벨은 `TaskController@destroy` 메소드에 주어진 할일의 ID를 다음과 같이 주입합니다:

    /**
     * Destroy the given task.
     *
     * @param  Request  $request
     * @param  string  $taskId
     * @return Response
     */
	public function destroy(Request $request, $taskId)
	{
		//
	}

하지만, 이 메소드가 가장 우선적으로 하는 일은 주어진 ID를 이용해서 데이터베이스에서 `Task` 인스턴스를 찾는 것입니다. 이런 경우, 라라벨이 처음부터 ID에 맞는 `Task` 인스턴스를 주입하게 된다면 편리하지 않을까요? 이렇게 할 수 있습니다.

`app/Providers/RouteServiceProvider.php` 파일의 `boot` 메소드에 다음 코드를 추가합니다:

	$router->model('task', 'App\Task');

이 짧은 코드 라인의 추가는 라라벨이 라우트 선언부분에서 `{task}`를 확인할 때마다 주어진 ID에 상응하는 `Task` 모델을 찾도록 지시할 것입니다. 이제 다음과 같이 destroy 메소드를 정의할 수 있습니다:

    /**
     * Destroy the given task.
     *
     * @param  Request  $request
     * @param  Task  $task
     * @return Response
     */
    public function destroy(Request $request, Task $task)
    {
        //
    }

<a name="authorization"></a>
### Authorization-승인

이제 `Task` 인스턴스가 `destroy`메소드에 됩니다: 하지만 인증된 사용자가 주어진 할일을 실제로 "소유" 하고 있는지에 대한 보장이 없습니다. 예를 들어, `/tasks/{task}` URL에 랜덤한 할일 ID를 전달하여 다른 사용자의 할일을 삭제하려는 악의적인 요청이 있었을 수도 있습니다. 그렇기 때문에 라라벨의 authorization-승인 기능을 사용하여 승인된 사용자가 실제로 라우트에 주입된 `Task` 인스턴스를 소유하고 있도록 보장해야 합니다.

#### Policy 생성하기

라라벨은 authorization-승인 로직을 간단하고 작은 클래스들로 구성하기 위하여 "policy-정책"들을 이용합니다. 보통 각 policy는 하나의 모델에 대응합니다. 아티즌 CLI를 이용하여 `TaskPolicy`를 생성하고 `app/Policies/TaskPolicy.php` 에 생성된 파일이 위치하도록 합니다.

	php artisan make:policy TaskPolicy

다음으로 policy-정책에 `destroy` 메소드를 추가합니다. 이 메소드는 `User` 인스턴스와 `Task` 인스턴스를 전달 받을 것입니다. 메소드는 단순히 사용자 아이디가 할일의 `user_id`와 일치하는지 확인합니다. 모든 policy-정책 메소드는 `true` 나 `false` 를 반환합니다:

	<?php

	namespace App\Policies;

	use App\User;
	use App\Task;
	use Illuminate\Auth\Access\HandlesAuthorization;

	class TaskPolicy
	{
	    use HandlesAuthorization;

	    /**
	     * Determine if the given user can delete the given task.
	     *
	     * @param  User  $user
	     * @param  Task  $task
	     * @return bool
	     */
	    public function destroy(User $user, Task $task)
	    {
	        return $user->id === $task->user_id;
	    }
	}

마지막으로 `Task` 모델을 `TaskPolicy`에 할당 해야합니다. 이 작업은 `app/Providers/AuthServiceProvider.php` 파일의 `$policies` 속성에 한 줄을 추가하는 것으로 할 수 있습니다. 이렇게 하면 `Task` 인스턴스에 동작을 authorize-승인할 때마다 라라벨이 어떤 policy-정책을 사용해야 하는지 알려줄 것입니다.

    /**
     * The policy mappings for the application.
     *
     * @var array
     */
    protected $policies = [
        Task::class => TaskPolicy::class,
    ];


#### 액션을 Authorizing-승인하기

이제 policy-정책을 작성하였으니, `destroy` 메소드를 사용해 보겠습니다. 모든 라라벨 컨트롤러는 `AuthorizesRequest` 트레이트-trait에 의해 제공되는 `authorize` 메소드를 호출할 수 있습니다.

    /**
     * Destroy the given task.
     *
     * @param  Request  $request
     * @param  Task  $task
     * @return Response
     */
    public function destroy(Request $request, Task $task)
    {
        $this->authorize('destroy', $task);

        // Delete The Task...
    }

잠시 이 메소드의 호출에 대해 생각해보겠습니다. `authorize` 메소드에 전달된 첫번째 인자는 호출하고자 하는 policy-정책 메소드의 이름입니다. 두 번째 인자는 현재 다루어야 할 모델의 인스턴스입니다. 앞서 이미 라라벨에 `Task` 모델이 `TaskPolicy` 에 대응한다는 것을 알려주었기 때문에, 프레임워크는 `destroy` 메소드가 실행되어야 하는 policy-정책을 알고 있습니다. 현재 사용자는 자동으로 policy-정책 메소드로 전달되기 때문에 수동으로 전달할 필요가 없습니다.

액션이 승인되면 코드는 계속해서 정상적으로 실행될 것입니다. 하지만 액션이 승인되지 않을 경우 (정책의 `destroy` 메소드가 `false` 를 반환한다면), 403 예외가 던져질 것이며 사용자는 에러 페이지를 보게 될 것입니다.

> **참고:** 라라벨이 제공하는 승인 서비스를 사용하는 여러 방법이 있습니다. [authorization-승인 문서](/docs/{{version}}/authorization)을 참고하십시오.

<a name="deleting-the-task"></a>
### 할일 삭제하기

마지막으로 실제 할일을 삭제하기 위해서 `destroy` 메소드에 로직을 추가합니다. Eloquent의 `delete` 메소드를 사용하여 데이터베이스의 모델 인스턴스를 삭제할 수 있습니다. 레코드가 삭제되면 사용자는 다시 `/tasks` URL로 리다이렉트 됩니다.

    /**
     * Destroy the given task.
     *
     * @param  Request  $request
     * @param  Task  $task
     * @return Response
     */
    public function destroy(Request $request, Task $task)
    {
        $this->authorize('destroy', $task);

        $task->delete();

        return redirect('/tasks');
    }
