# 기본적인 할일 목록 구성하기

- [시작하기](#introduction)
- [설치](#installation)
- [데이터베이스 준비](#prepping-the-database)
	- [Eloquent 모델](#eloquent-models)
- [라우팅](#routing)
	- [기본적인 라우트 구성하기](#stubbing-the-routes)
	- [뷰-View 연결하기](#displaying-a-view)
- [레이아웃과 뷰 구성하기](#building-layouts-and-views)
	- [레이아웃 정의하기](#defining-the-layout)
	- [자식 뷰 정의하기](#defining-the-child-view)
- [할일 추가하기](#adding-tasks)
	- [입력값 검증](#validation)
	- [할일 생성하기](#creating-the-task)
	- [존재하는 할일들 보여주기](#displaying-existing-tasks)
- [할일 삭제하기](#deleting-tasks)
	- [삭제 버튼 추가하기](#adding-the-delete-button)
	- [할일 삭제하기](#deleting-the-task)

<a name="introduction"></a>
## 시작하기

이 퀵스타트 가이드는 라라벨 프레임워크에 대한 기본적인 소개와 데이터베이스 마이그레이션, Eloquent ORM, 라우팅, Validation-검증, View-뷰, 블레이드 템플릿과 관련한 내용을 포함하고 있습니다. 라라벨 프레임워크나 PHP 프레임워크 자체를 처음 접하는 것이라면 이 가이드는 좋은 출발점이 될 것입니다. 만약 라라벨 프레임워크나 다른 PHP 프레임워크를 써본 적이 있다면 더 높은 단계의 퀵스타트 또한 이용하실 수 있습니다.

라라벨의 기본 기능들에 대한 샘플을 확인하기 위해서 간단하게 할일 목록을 만들고, 진행하고자 하는 모든 할일들을 트래킹할 수 있게 할 것입니다. 다시말해 일반적인 "할일" 목록을 표시하는 예제입니다. 이 프로젝트의 완성된 소스 코드는 [Github 에 공개되어 있습니다](https://github.com/laravel/quickstart-basic).

<a name="installation"></a>
## 설치

#### 라라벨 설치하기

가장 먼저 라라벨 프레임워크를 새로 설치해야 합니다. [홈스테드 가상머신](/docs/{{version}}/homestead)이나 원하는 로컬 PHP 환경을 선택하여 프레임워크를 실행할 수 있습니다. 로컬 환경이 준비되었다면 Composer를 이용해 라라벨 프레임워크를 설치할 수 있습니다. 

	composer create-project laravel/laravel quickstart --prefer-dist

#### 퀵스타트 설치하기 (선택사항)

이제부터 나오는 내용은 눈으로 보기만 하셔도 됩니다; 하지만 이 퀵스타트의 소스 코드를 다운받아서 로컬 머신에서 실행하고 싶으시다면 Git 저장소를 복제하여 다음과 같이 Composer를 설치하면 됩니다.

	git clone https://github.com/laravel/quickstart-basic quickstart
	cd quickstart
	composer install
	php artisan migrate

라라벨의 로컬 개발 환경 구성에 대해 더 많은 정보를 얻기 원하신다면 [홈스테드](/docs/{{version}}/homestead)와 [설치](/docs/{{version}}/installation) 메뉴얼을 확인하십시오. 

<a name="prepping-the-database"></a>
## 데이터베이스 준비

<a name="database-migrations"></a>
### 데이터베이스 마이그레이션

먼저 마이그레이션을 이용하여 모든 해야할 일들을 저장할 데이터베이스를 정의합니다. 라라벨의 데이터베이스 마이그레이션은 유연하고 다양한 PHP 코드를 사용하여 데이터베이스 테이블 구조를 정의하고 변경하는 방법을 제공합니다. 팀 멤버들이 로컬 데이터베이스에 수동으로 컬럼을 추가하도록 하는 대신에 소스 컨트롤(gir과 같은 SCM)에 넣은 마이그레이션을 통해서 실행하면 됩니다. 

그럼 이제 모든 해야할 일들을 저장할 데이터베이스 테이블을 만듭니다. [아티즌 CLI](/docs/{{version}}/artisan)를 이용하여 다양한 클래스를 생성하면 라라벨 프로젝트들을 구성할 때 매번 작성할 필요가 없어 효율성을 높일 수 있습니다. 지금은 `make:migration`커맨드를 이용하여 `tasks` 테이블을 구성할 새 데이터베이스 마이그레이션을 생성합니다. 

	php artisan make:migration create_tasks_table --create=tasks

마이그레이션 파일은 프로젝트의 `database/migrations` 디렉토리에 놓일 것입니다. 이미 알고 있을 수도 있듯이 `make:migration` 커맨드는 마이그레이션 파일에 auto-increment ID와 타임스탬프를 추가하였습니다. 이 파일을 수정하여 추가적으로 해야할 일들의 이름을 위한 `string` 컬럼을 추가합시다. 

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

`migrate` 아티즌 커맨드를 이용하여 마이그레이션을 실행합니다. 홈스테드를 이용하고 있다면 호스트 머신에서 데이터베이스에 직접 접속할 수 없기 때문에 홈스테드 가상 머신 안에서 커맨드를 실행해야 합니다. 

	php artisan migrate

이 커맨드는 모든 데이터베이스 테이블을 생성합니다. 특정 데이터베이스 클라이언트를 사용하여 데이터베이스를 확인한면 마이그레이션 테이블에 추가한 컬럼들을 포함한 새 `tasks` 테이블을 확인할 수 있을 것입니다. 이제 할일을 구성하는 Eloquent ORM 모델을 정의할 수 있습니다. 

<a name="eloquent-models"></a>
### Eloquent 모델

[Eloquent](/docs/{{version}}/eloquent)은 라라벨의 기본 ORM입니다. Eloquent를 사용하면 명료하게 정의된 "모델"들을 이용하여 데이터베이스에 데이터를 저장하고 조회하기 용이합니다. 보통 각 Eloquent 모델은 하나의 데이터베이스 테이블에 대응합니다. 

이제 `tasks` 데이터베이스 테이블에 대응하는 `Task` 모델을 정의합니다. 이번에도 아티즌 커맨드를 이용해서 모델을 생성할 수 있습니다. 이 경우에는 `make:model` 커맨드를 사용합니다. 

	php artisan make:model Task

이 모델은 애플리케이션의 `app` 디렉토리에 위치할 것입니다. 모델 클래스는 기본적으로 비어있습니다. Eloquent 모델은 데이터베이스 테이블이 모델 이름의 복수형태라 추정하기 때문에 직접적으로 어느 테이블에 대응하는지 명시할 필요는 없습니다. 따라서 이 경우 `Task` Eloquent 모델은 `tasks` 데이터베이스 테이블에 대응할 것으로 간주됩니다. 다음은 빈 모델이 어떻게 보여질지 예시 입니다:  

	<?php

	namespace App;

	use Illuminate\Database\Eloquent\Model;

	class Task extends Model
	{
		//
	}

Eloquent 모델의 사용법은 애플리케이션에 라우팅을 추가하면서 차차 알아가게 될 것입니다. 더 많은 정보를 얻고 싶으시다면 [Eloquent 문서](/docs/{{version}}/eloquent)를 참고하십시오.

<a name="routing"></a>
## 라우팅

<a name="stubbing-the-routes"></a>
### 기본적인 라우트 구성하기

이제 애플리케이션에 몇개의 라우트(route)를 추가할 수 있습니다. 라우트는 사용자가 특정 페이지에 접속할 때 실행되어야 하는 컨트롤러나 익명함수에 대한 URL을 구성하는 역할을 합니다. 모든 라라벨 라우트들은 기본적으로 `app/Http/routes.php` 파일에 정의되어 있습니다. 

이 애플리케이션에서는 최소 3개의 라우트가 필요합니다: 모든 할일들을 목록화하는 라우트, 새로운 할일을 추가하는 라우트, 그리고 이미 존재하는 할일를 삭제하는 라우트입니다. 그럼 이 라우트들을 아래와 같이 `app/Http/routes.php` 파일에 구성합니다.

	<?php

	use App\Task;
	use Illuminate\Http\Request;

	/**
	 * Show Task Dashboard
	 */
	Route::get('/', function () {
		//
	});

	/**
	 * Add New Task
	 */
	Route::post('/task', function (Request $request) {
		//
	});

	/**
	 * Delete Task
	 */
	Route::delete('/task/{task}', function (Task $task) {
		//
	});

> **주의**: 만약 여러분의 애플리케이션에 있는 `RouteServiceProvider`가 이미 `web` 미들웨어 그룹 안에 있는 기본적인 라우트 파일들을 포함하고 있다면, 수동으로 `routes.php` 파일에 그룹을 추가해야만 합니다.

<a name="displaying-a-view"></a>
### 뷰-View 연결하기

이제 `/` 라우트를 구성합니다. 이 라우트에서 현재 할일 목록 뿐만 아니라 새 할일을 추가할 수 있는 폼을 가진 HTML 템플릿을 화면에 구성하려고 합니다.

라라벨에서 모든 HTML 템플릿은 `resources/views` 디렉토리에 저장되어 있으며 `view` 헬퍼를 이용해서 하나의 템플릿을 라우트에서 반환할 수 있습니다. 

	Route::get('/', function () {
		return view('tasks');
	});


`view` 함수에 전달되는 `tasks`는 `resources/views/tasks.blade.php` 템플릿과 일치하는 뷰 객체 인스턴스를 생성할 것입니다. 물론, 이제 이 뷰파일을 정의해야 합니다. 

<a name="building-layouts-and-views"></a>
## 레이아웃과 뷰 구성하기

이 애플리케이션에서는 현재 할일들의 목록과 새 할일를 추가하는 형식을 가진 view를 하나만 구성합니다. 화면을 구성하는데 도움을 주기 위해 아래에 기본적인 부트스트랩 CSS 스타일링을 적용한 완성된 애플리케이션의 스크린샷을 제공합니다.  

![완성된 애플리케이션 스크린샷](https://laravel.com/assets/img/quickstart/basic-overview.png)

<a name="defining-the-layout"></a>
### 레이아웃 정의하기

거의 모든 웹애플리케이션은 모든 페이지에 동일한 레이아웃을 사용합니다. 예로 들어 이 애플리케이션은 페이지가 하나 이상일 경우 모든 페이지 위에 검색바가 표시됩니다. 라라벨은 블레이드 **Layouts**를 통해 이런 공통의 기능을 모든 페이지에 적용하기 쉽게 해줍니다. 

앞서 이야기한 바와 같이 라라벨의 모든 view 파일들은 `resources/views`에 저장되어 있습니다. 그럼 이제 `resources/views/layouts/app.blade.php`에 새 레이아웃 view를 정의합니다. `.blade.php` 확장자는 뷰를 렌더링하기 위해 [블레이드 템플릿 엔진](/docs/{{version}}/blade)를 이용하라고 프레임워크에 알려줍니다. 물론 라라벨에서는 순수 PHP 코드 템플릿을 사용하는 것이 가능합니다. 하지만 블레이드는 더 편리하고, 작성하기 깔끔한 방법을 제공해줍니다.

`app.blade.php` 뷰는 다음과 같습니다:

    <!-- resources/views/layouts/app.blade.php -->

	<!DOCTYPE html>
	<html lang="en">
		<head>
			<title>Laravel Quickstart - Basic</title>

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

레이아웃의 `@yield('content')` 부분은 레이아웃을 확장하는 모든 자식 페이지들이 어디에 자신의 콘텐츠를 작성 할 수 있는지 지정해주는 특별한 블레이드 지시어를 말합니다. 이제 이 레이아웃을 사용하고 주요 컨텐츠를 제공할 자식 뷰를 정의합니다. 

<a name="defining-the-child-view"></a>
### 자식 뷰 정의하기

다음으로 현재 할일 리스트 테이블과 새 할일을 생성하는 폼을 가진 뷰를 정의해야 합니다. `resources/views/tasks.blade.php`에서 뷰를 정의합니다. 

몇몇 부트스트랩 CSS 보일러플레이트는 넘어가고 중요한 부분에만 집중할 것입니다. [GitHub](https://github.com/laravel/quickstart-basic)에서 이 애플리케이션을 위한 전체 소스를 다운받으실 수 있습니다:

    <!-- resources/views/tasks.blade.php -->

	@extends('layouts.app')

	@section('content')

        <!-- Bootstrap Boilerplate... -->

		<div class="panel-body">
            <!-- Display Validation Errors -->
			@include('common.errors')

			<!-- New Task Form -->
			<form action="{{ url('task') }}" method="POST" class="form-horizontal">
				{{ csrf_field() }}

                <!-- Task Name -->
				<div class="form-group">
					<label for="task" class="col-sm-3 control-label">Task</label>

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

#### 몇가지 설명 추가

다음으로 넘어가기 전에 이 템플릿에 대하여 잠시 알아보겠습니다. 먼저 `@extends` 명령은 `resources/views/layouts/app.blade.php` 안에서 정의한 레이아웃을 사용한다는 것을 블레이드에 알려줍니다. `@section('content')` 와 `@endsection` 사이의 모든 콘텐츠는 `app.blade.php` 레이아웃 안에 있는 `@yield('content')`에 구성 될 것입니다. 

`@include('common.errors')` 지시어는 `resources/views/common/errors.blade.php`에 위치한 템플릿을 로드할 것입니다. 이 템플릿은 아직 정의되지 않았으며 좀 더 뒤에서 정의할 예정입니다. 

이제 애플리케이션을 위한 기본적인 레이아웃과 뷰가 정의되었습니다. 이 뷰는 다음과 같이 `/` 라우트에서 반환합니다:  

	Route::get('/', function () {
		return view('tasks');
	});

이제 form 에서 넘어오는 데이터들을 처리하고 새 할일을 데이터베이스에 추가하기 위해 `POST /task`에 해당하는 라우트에 코드를 추가할 수 있습니다. 

<a name="adding-tasks"></a>
## 할일 추가하기

<a name="validation"></a>
### 입력값 검증

이제 뷰에 Form 이 구성되어 있으며 전달되는 입력값을 확인하고 새 할일을 생성하기 위해서 `app/Http/routes.php` 파일안에 있는 `POST/task` 라우트에 코드를 추가해야 합니다. 먼저 입력값을 검증합니다.

이 Form에서는 `name` 필드를 필수로 하고 `255` 자 미만이어야 한다는 내용을 담도록 할 것입니다. 입력값 검증 실패 시에 사용자는 `/` URL로 돌아가게 되며 이전에 입력한 값들과 에러는 [세션](/docs/{{version}}/session)에 기록해 둡니다. 입력값을 세션에 임시로 저장하면, 유효성 검증이에서 에러가 발생했을 때 사용자의 입력을 확인할 수 있게 해줄 것입니다:

	Route::post('/task', function (Request $request) {
		$validator = Validator::make($request->all(), [
			'name' => 'required|max:255',
		]);

		if ($validator->fails()) {
			return redirect('/')
				->withInput()
				->withErrors($validator);
		}

		// Create The Task...
	});

#### `$errors` 변수

잠시 이 예제의 `->withErrors($validator)` 부분에 대해 알려드리겠습니다. `>withErrors($validator)` 호출은 주어진 입력값 검증의 결과를 세션에 기록하여 뷰파일에서 `$errors` 변수을 통해 접근할 수 있도록 해줍니다. 

앞서 뷰 파일을 구성할 때 `@include('common.errors')` 지시어를 사용해 입력값 검증 오류를 표시하도록 하였습니다. `common.errors`는 모든 페이지에서 같은 형식으로 입력값 검증 오류를 쉽게 보여줄 수 있게 해줍니다.

    <!-- resources/views/common/errors.blade.php -->

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


> **참고:** `$errors` 변수은 **모든** 라라벨 뷰에서 제공됩니다. 따라서 입력값 검증 오류가 존재하지 않는다면 변수는 단순히 'ViewErrorBag' 형태의 비어있는 인스턴스일 것입니다.  

<a name="creating-the-task"></a>
### 할일 생성하기

입력값 검증이 해결되었으니 이제 라우트를 계속해서 채워나가면서 새 할일를 생성합니다. 새 할일이 생성되면 사용자는 `/` URL로 돌아갑니다. 할일를 생성하기 위해 Eloquent 모델에 프로퍼티를 구성하고 설정한 뒤 `save` 메소드를 호출할 수 있습니다.

	Route::post('/task', function (Request $request) {
		$validator = Validator::make($request->all(), [
			'name' => 'required|max:255',
		]);

		if ($validator->fails()) {
			return redirect('/')
				->withInput()
				->withErrors($validator);
		}

		$task = new Task;
		$task->name = $request->name;
		$task->save();

		return redirect('/');
	});

좋습니다. 이제 성공적으로 할일을 생성할 수 있습니다. 다음으로 존재하는 모든 할일 목록을 만들어 뷰에 계속 추가하도록 합니다. 

<a name="displaying-existing-tasks"></a>
### 존재하는 할일들 보여주기

먼저 존재하는 모든 할일들을 뷰에 전달하기 위해서 `/` 라우트를 변경해야 합니다. `view` 기능은 뷰에게 제공되어질 데이터의 배열을 가진 두번째 argument를 전달 받습니다. 배열의 각 키는 뷰 내에서 변수가 됩니다:

	Route::get('/', function () {
		$tasks = Task::orderBy('created_at', 'asc')->get();

		return view('tasks', [
			'tasks' => $tasks
		]);
	});

데이터가 전달되고 나면 `tasks.blade.php` 뷰에 있는 할일들을 테이블에 나타낼 수 있습니다. `@foreach` 블레이드 구성은 빠르고 단순한 PHP 코드로 컴파일되는 간결한 반복문을 사용할 수 있도록 해줍니다: 

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

할일 애플리케이션이 거의 완성되었습니다. 하지만 할일이 수행된 후 삭제하는 방법이 아직 없습니다. 다음은 이를 추가합니다. 

<a name="deleting-tasks"></a>
## 할일 삭제하기

<a name="adding-the-delete-button"></a>
### 삭제 버튼 추가하기

"할일" 이 출력되는 곳 바로 옆에 삭제 버튼을 나타내는 코드를 구성할 것입니다. `tasks.blade.php` 뷰파일 안에 있는 할일 목록의 각 행에 삭제 버튼을 추가합니다. 각각의 할일 옆에 작은 버튼을 구성할 것입니다. 버튼을 누르면 `DELETE /task` 요청이 애플리케이션에 전달될 것입니다. 

    <tr>
        <!-- Task Name -->
        <td class="table-text">
            <div>{{ $task->name }}</div>
        </td>

        <!-- Delete Button -->
        <td>
            <form action="{{ url('task/'.$task->id) }}" method="POST">
                {{ csrf_field() }}
                {{ method_field('DELETE') }}

                <button type="submit" class="btn btn-danger">
                    <i class="fa fa-trash"></i> Delete
                </button>
            </form>
        </td>
    </tr>

<a name="a-note-on-method-spoofing"></a>
#### 메소드 스푸핑에 대해서 알기

`Route::delete` 라우트를 이용해서 요청에 대응할 때에도 삭제 버튼의 폼 `method`는 `POST`로 구성되어 있습니다. HTML 폼은 `GET` 과 `POST` HTTP 메소드만을 허용하기 때문에 폼에 `DELETE` 요청을 나타낼 수 있는 방법이 필요합니다.

폼을 구성하는 내부에 `method_field('DELETE')` 핼퍼 함수의 결과를 출력해서 `DELETE` 요청을 흉내낼 수 있습니다. 이 핼퍼 함수는 숨겨진 형식의 인풋을 생성하고 라라벨은 이를 인식하여 실제 HTTP 요청 메소드를 대체할 것입니다. 생성된 필드는 다음과 같을 것입니다: 

	<input type="hidden" name="_method" value="DELETE">

<a name="deleting-the-task"></a>
### 할일 삭제하기

마지막으로, 실제로 할일를 삭제할 수 있도록 라우트에 로직을 추가합니다. [묵시적 모델 바인딩](/docs/{{version}}/routing#route-model-binding)을 사용하여 자동으로 `{task}`라우트 파라미터와 일치하는 `Task` 모델을 찾을 수 있습니다. 

라우트 콜백 안에서 `delete` 메소드를 사용하여 레코드를 삭제할 것입니다. 레코드가 삭제되고나면, 사용자는 `/` URL로 리다이렉트 될 것입니다. 

	Route::delete('/task/{task}', function (Task $task) {
		$task->delete();

		return redirect('/');
	});
