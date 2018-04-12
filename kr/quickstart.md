# Basic Task List
# 기본적인 할일 목록 구성하기

- [Introduction](#introduction)
- [소개](#introduction)
- [Installation](#installation)
- [설치](#installation)
- [Prepping The Database](#prepping-the-database)
- [데이터베이스 준비](#prepping-the-database)
	- [데이터베이스 마이그레이션](#database-migrations)
	- [Eloquent Models](#eloquent-models)
	- [Eloquent 모델](#eloquent-models)
- [Routing](#routing)
- [라우팅](#routing)
	- [Stubbing The Routes](#stubbing-the-routes)
	- [기본적인 라우트 구성하기](#stubbing-the-routes)
	- [Displaying A View](#displaying-a-view)
	- [뷰-View 연결하기](#displaying-a-view)
- [Building Layouts & Views](#building-layouts-and-views)
- [레이아웃과 뷰 구성하기](#building-layouts-and-views)
	- [Defining The Layout](#defining-the-layout)
	- [레이아웃 정의하기](#defining-the-layout)
	- [Defining The Child View](#defining-the-child-view)
	- [자식 뷰 정의하기](#defining-the-child-view)
- [Adding Tasks](#adding-tasks)
- [할일 추가하기](#adding-tasks)
	- [Validation](#validation)
	- [입력값 검증](#validation)
	- [Creating The Task](#creating-the-task)
	- [할일 생성하기](#creating-the-task)
	- [Displaying Existing Tasks](#displaying-existing-tasks)
	- [존재하는 할일들 보여주기](#displaying-existing-tasks)
- [Deleting Tasks](#deleting-tasks)
- [할일 삭제하기](#deleting-tasks)
	- [Adding The Delete Button](#adding-the-delete-button)
	- [삭제 버튼 추가하기](#adding-the-delete-button)
	- [Deleting The Task](#deleting-the-task)
	- [할일 삭제하기](#deleting-the-task)

<a name="introduction"></a>
## Introduction
## 소개

This quickstart guide provides a basic introduction to the Laravel framework and includes content on database migrations, the Eloquent ORM, routing, validation, views, and Blade templates. This is a great starting point if you are brand new to the Laravel framework or PHP frameworks in general. If you have already used Laravel or other PHP frameworks, you may wish to consult one of our more advanced quickstarts.

이 퀵스타트 가이드는 라라벨 프레임워크에 대한 기본적인 소개와 데이터베이스 마이그레이션, Eloquent ORM, 라우팅, Validation-검증, View-뷰, 블레이드 템플릿과 관련한 내용을 포함하고 있습니다. 라라벨 프레임워크나 PHP 프레임워크 자체를 처음 접하는 것이라면 이 가이드는 좋은 출발점이 될 것입니다. 만약 라라벨 프레임워크나 다른 PHP 프레임워크를 써본 적이 있다면 더 높은 단계의 퀵스타트 또한 이용하실 수 있습니다.

To sample a basic selection of Laravel features, we will build a simple task list we can use to track all of the tasks we want to accomplish (the typical "to-do list" example). The complete, finished source code for this project is [available on GitHub](http://github.com/laravel/quickstart-basic).

라라벨의 기본 기능들을 이해하기 위해서 단순한 To do 리스트를 만들어서 이루고 싶은 모든 과제를 확인하는 예제를 확인 할 것입니다. ("해야 할 일" 목록) 이 프로젝트의 완성된 소스 코드는 [GitHub에 공개](http://github.com/laravel/quickstart-basic)되어 있습니다.

<a name="installation"></a>
## Installation
## 설치

Of course, first you will need a fresh installation of the Laravel framework. You may use the [Homestead virtual machine](/docs/{{version}}/homestead) or the local PHP environment of your choice to run the framework. Once your local environment is ready, you may install the Laravel framework using Composer:

가장 먼저 라라벨 프레임워크를 새로 설치해야 합니다. [홈스테드 가상머신](/docs/{{version}}/homestead)이나 원하는 로컬 PHP 환경을 선택하여 프레임워크를 실행할 수 있습니다. 로컬 환경이 준비되었다면 Composer를 이용해 라라벨 프레임워크를 설치할 수 있습니다.

	composer create-project laravel/laravel quickstart --prefer-dist

You're free to just read along for the remainder of this quickstart; however, if you would like to download the source code for this quickstart and run it on your local machine, you may clone its Git repository and install its dependencies:

이제부터 나오는 내용은 눈으로 보기만 하셔도 됩니다; 하지만 이 퀵스타트의 소스 코드를 다운받아서 로컬 머신에서 실행하고 싶으시다면 Git 저장소를 복제하여 다음과 같이 Composer를 설치하면 됩니다.

	git clone https://github.com/laravel/quickstart-basic quickstart
	cd quickstart
	composer install
	php artisan migrate

For more complete documentation on building a local Laravel development environment, check out the full [Homestead](/docs/{{version}}/homestead) and [installation](/docs/{{version}}/installation) documentation.

라라벨의 로컬 개발 환경 구성에 대해 더 많은 정보를 얻기 원하신다면 [홈스테드](/docs/{{version}}/homestead)와 [설치](/docs/{{version}}/installation) 메뉴얼을 확인하십시오.

<a name="prepping-the-database"></a>
## Prepping The Database
## 데이터베이스 준비

<a name="database-migrations"></a>
### Database Migrations
### 데이터베이스 마이그레이션

First, let's use a migration to define a database table to hold all of our tasks. Laravel's database migrations provide an easy way to define your database table structure and modifications using fluent, expressive PHP code. Instead of telling your team members to manually add columns to their local copy of the database, your teammates can simply run the migrations you push into source control.

먼저 마이그레이션을 이용하여 모든 해야할 일들을 저장할 데이터베이스를 정의합니다. 라라벨의 데이터베이스 마이그레이션은 유연하고 다양한 PHP 코드를 사용하여 데이터베이스 테이블 구조를 정의하고 변경하는 방법을 제공합니다. 팀 멤버들이 로컬 데이터베이스에 수동으로 컬럼을 추가하도록 하는 대신에 소스 컨트롤(gir과 같은 SCM)에 넣은 마이그레이션을 통해서 실행하면 됩니다.

So, let's build a database table that will hold all of our tasks. The [Artisan CLI](/docs/{{version}}/artisan) can be used to generate a variety of classes and will save you a lot of typing as you build your Laravel projects. In this case, let's use the `make:migration` command to generate a new database migration for our `tasks` table:

그럼 이제 모든 해야할 일들을 저장할 데이터베이스 테이블을 만듭니다. [아티즌 CLI](/docs/{{version}}/artisan)를 이용하여 다양한 클래스를 생성하면 라라벨 프로젝트들을 구성할 때 매번 작성할 필요가 없어 효율성을 높일 수 있습니다. 지금은 `make:migration`커맨드를 이용하여 `tasks` 테이블을 구성할 새 데이터베이스 마이그레이션을 생성합니다.

	php artisan make:migration create_tasks_table --create=tasks

The migration will be placed in the `database/migrations` directory of your project. As you may have noticed, the `make:migration` command already added an auto-incrementing ID and timestamps to the migration file. Let's edit this file and add an additional `string` column for the name of our tasks:

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

To run our migration, we will use the `migrate` Artisan command. If you are using Homestead, you should run this command from within your virtual machine, since your host machine will not have direct access to the database:

`migrate` 아티즌 커맨드를 이용하여 마이그레이션을 실행합니다. 홈스테드를 이용하고 있다면 호스트 머신에서 데이터베이스에 직접 접속할 수 없기 때문에 홈스테드 가상 머신 안에서 커맨드를 실행해야 합니다.

	php artisan migrate

This command will create all of our database tables. If you inspect the database tables using the database client of your choice, you should see a new `tasks` table which contains the columns defined in our migration. Next, we're ready to define an Eloquent ORM model for our tasks!

이 커맨드는 모든 데이터베이스 테이블을 생성합니다. 특정 데이터베이스 클라이언트를 사용하여 데이터베이스를 확인한면 마이그레이션 테이블에 추가한 컬럼들을 포함한 새 `tasks` 테이블을 확인할 수 있을 것입니다. 이제 할일을 구성하는 Eloquent ORM 모델을 정의할 수 있습니다.

<a name="eloquent-models"></a>
### Eloquent Models
### Eloquent 모델

[Eloquent](/docs/{{version}}/eloquent) is Laravel's default ORM (object-relational mapper). Eloquent makes it painless to retrieve and store data in your database using clearly defined "models". Usually, each Eloquent model corresponds directly with a single database table.

[Eloquent](/docs/{{version}}/eloquent)은 라라벨의 기본 ORM입니다. Eloquent를 사용하면 명료하게 정의된 "모델"들을 이용하여 데이터베이스에 데이터를 저장하고 조회하기 용이합니다. 보통 각 Eloquent 모델은 하나의 데이터베이스 테이블에 대응합니다.

So, let's define a `Task` model that corresponds to our `tasks` database table we just created. Again, we can use an Artisan command to generate this model. In this case, we'll use the `make:model` command:

이제 `tasks` 데이터베이스 테이블에 대응하는 `Task` 모델을 정의합니다. 이번에도 아티즌 커맨드를 이용해서 모델을 생성할 수 있습니다. 이 경우에는 `make:model` 커맨드를 사용합니다.

	php artisan make:model Task

The model will be placed in the `app` directory of your application. By default, the model class is empty. We do not have to explicitly tell the Eloquent model which table it corresponds to because it will assume the database table is the plural form of the model name. So, in this case, the `Task` model is assumed to correspond with the `tasks` database table. Here is what our empty model should look like:

이 모델은 어플리케이션의 `app` 디렉토리에 위치할 것입니다. 모델 클래스는 기본적으로 비어있습니다. Eloquent 모델은 데이터베이스 테이블이 모델 이름의 복수형태라 추정하기 때문에 직접적으로 어느 테이블에 대응하는지 명시할 필요는 없습니다. 따라서 이 경우 `Task` Eloquent 모델은 `tasks` 데이터베이스 테이블에 대응할 것으로 간주됩니다. 다음은 빈 모델이 어떻게 보여질지 예시 입니다:

	<?php

	namespace App;

	use Illuminate\Database\Eloquent\Model;

	class Task extends Model
	{
		//
	}

We'll learn more about how to use Eloquent models as we add routes to our application. Of course, feel free to consult the [complete Eloquent documentation](/docs/{{version}}/eloquent) for more information.

Eloquent 모델의 사용법은 어플리케이션에 라우팅을 추가하면서 차차 알아가게 될 것입니다. 더 많은 정보를 얻고 싶으시다면 [Eloquent 문서](/docs/{{version}}/eloquent)를 참고하십시오.

<a name="routing"></a>
## Routing
## 라우팅

<a name="stubbing-the-routes"></a>
### Stubbing The Routes
### 기본적인 라우트 구성하기

Next, we're ready to add a few routes to our application. Routes are used to point URLs to controllers or anonymous functions that should be executed when a user accesses a given page. By default, all Laravel routes are defined in the `app/Http/routes.php` file that is included in every new project.

이제 어플리케이션에 몇개의 라우트(route)를 추가할 수 있습니다. 라우트는 사용자가 특정 페이지에 접속할 때 실행되어야 하는 컨트롤러나 익명함수에 대한 URL을 구성하는 역할을 합니다. 모든 라라벨 라우트들은 기본적으로 `app/Http/routes.php` 파일에 정의되어 있습니다.

For this application, we know we will need at least three routes: a route to display a list of all of our tasks, a route to add new tasks, and a route to delete existing tasks. So, let's stub all of these routes in the `app/Http/routes.php` file:

이 어플리케이션에서는 최소 3개의 라우트가 필요합니다: 모든 할일들을 목록화하는 라우트, 새로운 할일을 추가하는 라우트, 그리고 이미 존재하는 할일를 삭제하는 라우트입니다. 그럼 이 라우트들을 아래와 같이 `app/Http/routes.php` 파일에 구성합니다.

	<?php

	use App\Task;
	use Illuminate\Http\Request;

	/**
	 * Display All Tasks
	 */
	Route::get('/', function () {
		//
	});

	/**
	 * Add A New Task
	 */
	Route::post('/task', function (Request $request) {
		//
	});

	/**
	 * Delete An Existing Task
	 */
	Route::delete('/task/{id}', function ($id) {
		//
	});

<a name="displaying-a-view"></a>
### Displaying A View
### 뷰-View 연결하기

Next, let's fill out our `/` route. From this route, we want to render an HTML template that contains a form to add new tasks, as well as a list of all current tasks.

이제 `/` 라우트를 구성합니다. 이 라우트에서 현재 할일 목록 뿐만 아니라 새 할일을 추가할 수 있는 폼을 가진 HTML 템플릿을 화면에 구성하려고 합니다.

In Laravel, all HTML templates are stored in the `resources/views` directory, and we can use the `view` helper to return one of these templates from our route:

라라벨에서 모든 HTML 템플릿은 `resources/views` 디렉토리에 저장되어 있으며 `view` 헬퍼를 이용해서 하나의 템플릿을 라우트에서 반환할 수 있습니다.

	Route::get('/', function () {
		return view('tasks');
	});

Of course, we need to actually define this view, so let's do that now!

물론, 이제 이 뷰파일을 정의해야 합니다.

<a name="building-layouts-and-views"></a>
## Building Layouts & Views
## 레이아웃과 뷰 구성하기

This application only has a single view which contains a form for adding new tasks as well as a listing of all current tasks. To help you visualize the view, here is a screenshot of the finished application with basic Bootstrap CSS styling applied:

이 어플리케이션에서는 현재 할일들의 목록과 새 할일를 추가하는 형식을 가진 view를 하나만 구성합니다. 화면을 구성하는데 도움을 주기 위해 아래에 기본적인 부트스트랩 CSS 스타일링을 적용한 완성된 어플리케이션의 스크린샷을 제공합니다.

![Application Image](https://laravel.com/assets/img/quickstart/basic-overview.png)
![완성된 어플리케이션 스크린샷](https://laravel.com/assets/img/quickstart/basic-overview.png)

<a name="defining-the-layout"></a>
### Defining The Layout
### 레이아웃 정의하기

Almost all web applications share the same layout across pages. For example, this application has a top navigation bar that would be typically present on every page (if we had more than one). Laravel makes it easy to share these common features across every page using Blade **layouts**.

거의 모든 웹애플리케이션은 모든 페이지에 동일한 레이아웃을 사용합니다. 예로 들어 이 애플리케이션은 페이지가 하나 이상일 경우 모든 페이지 위에 검색바가 표시됩니다. 라라벨은 블레이드 **Layouts**를 통해 이런 공통의 기능을 모든 페이지에 적용하기 쉽게 해줍니다.

As we discussed earlier, all Laravel views are stored in `resources/views`. So, let's define a new layout view in `resources/views/layouts/app.blade.php`. The `.blade.php` extension instructs the framework to use the [Blade templating engine](/docs/{{version}}/blade) to render the view. Of course, you may use plain PHP templates with Laravel. However, Blade provides convenient short-cuts for writing cleaner, terse templates.

앞서 이야기한 바와 같이 라라벨의 모든 view 파일들은 `resources/views`에 저장되어 있습니다. 그럼 이제 `resources/views/layouts/app.blade.php`에 새 레이아웃 view를 정의합니다. `.blade.php` 확장자는 뷰를 렌더링하기 위해 [블레이드 템플릿 엔진](/docs/{{version}}/blade)를 이용하라고 프레임워크에 알려줍니다. 물론 라라벨에서는 순수 PHP 코드 템플릿을 사용하는 것이 가능합니다. 하지만 블레이드는 더 편리하고, 작성하기 깔끔한 방법을 제공해줍니다.

Our `app.blade.php` view should look like the following:

`app.blade.php` 뷰는 다음과 같습니다:

    // resources/views/layouts/app.blade.php

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

Note the `@yield('content')` portion of the layout. This is a special Blade directive that specifies where all child pages that extend the layout can inject their own content. Next, let's define the child view that will use this layout and provide its primary content.

레이아웃의 `@yield('content')` 부분은 레이아웃을 확장하는 모든 자식 페이지들이 어디에 자신의 콘텐츠를 작성 할 수 있는지 지정해주는 특별한 블레이드 지시어를 말합니다. 이제 이 레이아웃을 사용하고 주요 컨텐츠를 제공할 자식 뷰를 정의합니다.

<a name="defining-the-child-view"></a>
### Defining The Child View
### 자식 뷰 정의하기

Great, our application layout is finished. Next, we need to define a view that contains a form to create a new task as well as a table that lists all existing tasks. Let's define this view in `resources/views/tasks.blade.php`.

이제 애플리케이션 레이아웃이 완성되었습니다. 다음은 현재 할일 리스트 테이블과 새 할일을 생성하는 폼을 가진 뷰를 정의해야 합니다. `resources/views/tasks.blade.php`에서 뷰를 정의합니다.

We'll skip over some of the Bootstrap CSS boilerplate and only focus on the things that matter. Remember, you can download the full source for this application on [GitHub](https://github.com/laravel/quickstart-basic):

몇몇 부트스트랩 CSS 보일러플레이트는 넘어가고 중요한 부분에만 집중할 것입니다. [GitHub](https://github.com/laravel/quickstart-basic)에서 이 어플리케이션을 위한 전체 소스를 다운받으실 수 있습니다:

    // resources/views/tasks.blade.php

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

#### A Few Notes Of Explanation
#### 몇가지 설명 추가

Before moving on, let's talk about this template a bit. First, the `@extends` directive informs Blade that we are using the layout we defined at `resources/views/layouts/app.blade.php`. All of the content between `@section('content')` and `@endsection` will be injected into the location of the `@yield('content')` directive within the `app.blade.php` layout.

다음으로 넘어가기 전에 이 템플릿에 대하여 잠시 알아보겠습니다. 먼저 `@extends` 명령은 `resources/views/layouts/app.blade.php`에서 정의한 레이아웃을 사용한다는 것을 블레이드에 알려줍니다. `@section('content')` 와 `@endsection` 사이의 모든 콘텐츠는 `app.blade.php` 레이아웃 안에 있는 `@yield('content')`에 구성 될 것입니다.

Now we have defined a basic layout and view for our application. Remember, we are returning this view from our `/` route like so:

이제 어플리케이션을 위한 기본적인 레이아웃과 뷰가 정의되었습니다. 이 뷰는 다음과 같이 `/` 라우트에서 반환합니다:

	Route::get('/', function () {
		return view('tasks');
	});

Next, we're ready to add code to our `POST /task` route to handle the incoming form input and add a new task to the database.

이제 form 에서 넘어오는 데이터들을 처리하고 새 할일을 데이터베이스에 추가하기 위해 `POST /task`에 해당하는 라우트에 코드를 추가할 수 있습니다.

> **Note:** The `@include('common.errors')` directive will load the template located at `resources/views/common/errors.blade.php`. We haven't defined this template, but we will soon!

> **참고:** `@include('common.errors')` 구문은 `resources/views/common/errors.blade.php`에 위치한 템플릿을 로드할 것입니다. 이 템플릿은 아직 정의되지 않았으며 좀 더 뒤에서 정의할 예정입니다.

<a name="adding-tasks"></a>
## Adding Tasks
## 할일 추가하기

<a name="validation"></a>
### Validation
### 입력값 검증

Now that we have a form in our view, we need to add code to our `POST /task` route to validate the incoming form input and create a new task. First, let's validate the input.

이제 뷰에 Form 이 구성되어 있으며 전달되는 입력값을 확인하고 새 할일을 생성하기 위해서 `POST /task` 라우트에 코드를 추가해야 합니다. 먼저 입력값을 검증합니다.

For this form, we will make the `name` field required and state that it must contain less than `255` characters. If the validation fails, we will redirect the user back to the `/` URL, as well as flash the old input and errors into the [session](/docs/{{version}}/session):

이 Form에서는 `name` 필드를 필수로 하고 `255` 자 미만이어야 한다는 내용을 담도록 할 것입니다. 입력값 검증 실패 시에 사용자는 `/` URL로 돌아가게 되며 이전에 입력한 값들과 에러는 [세션](/docs/{{version}}/session)에 기록해 둡니다.

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

#### The `$errors` Variable
#### `$errors` 변수

Let's take a break for a moment to talk about the `->withErrors($validator)` portion of this example. The `->withErrors($validator)` call will flash the errors from the given validator instance into the session so that they can be accessed via the `$errors` variable in our view.

잠시 이 예제의 `->withErrors($validator)` 부분에 대해 알려드리겠습니다. `>withErrors($validator)` 호출은 주어진 입력값 검증의 결과를 세션에 기록하여 뷰파일에서 `$errors` 변수을 통해 접근할 수 있도록 해줍니다.

Remember that we used the `@include('common.errors')` directive within our view to render the form's validation errors. The `common.errors` will allow us to easily show validation errors in the same format across all of our pages. Let's define the contents of this view now:

앞서 뷰 파일을 구성할 때 `@include('common.errors')` 지시어를 사용해 입력값 검증 오류를 표시하도록 하였습니다. `common.errors`는 모든 페이지에서 같은 형식으로 입력값 검증 오류를 쉽게 보여줄 수 있게 해줍니다.

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


> **Note:** The `$errors` variable is available in **every** Laravel view. It will simply be an empty instance of `ViewErrorBag` if no validation errors are present.

> **참고:** `$errors` 변수은 **모든** 라라벨 뷰에서 제공됩니다. 따라서 입력값 검증 오류가 존재하지 않는다면 변수는 단순히 'ViewErrorBag' 형태의 비어있는 인스턴스일 것입니다.

<a name="creating-the-task"></a>
### Creating The Task
### 할일 생성하기

Now that input validation is handled, let's actually create a new task by continuing to fill out our route. Once the new task has been created, we will redirect the user back to the `/` URL. To create the task, we may use the `save` method after creating and setting properties on a new Eloquent model:

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

Great! We can now successfully create tasks. Next, let's continue adding to our view by building a list of all existing tasks.

좋습니다. 이제 성공적으로 할일을 생성할 수 있습니다. 다음으로 존재하는 모든 할일 목록을 만들어 뷰에 계속 추가하도록 합니다.

<a name="displaying-existing-tasks"></a>
### Displaying Existing Tasks
### 존재하는 할일들 보여주기

First, we need to edit our `/` route to pass all of the existing tasks to the view. The `view` function accepts a second argument which is an array of data that will be made available to the view, where each key in the array will become a variable within the view:

먼저 존재하는 모든 할일들을 뷰에 전달하기 위해서 `/` 라우트를 변경해야 합니다. `view` 기능은 뷰에게 제공되어질 데이터의 배열을 가진 두번째 argument를 전달 받습니다. 배열의 각 키는 뷰 내에서 변수가 됩니다:

	Route::get('/', function () {
		$tasks = Task::orderBy('created_at', 'asc')->get();

		return view('tasks', [
			'tasks' => $tasks
		]);
	});

Once the data is passed, we can spin through the tasks in our `tasks.blade.php` view and display them in a table. The `@foreach` Blade construct allows us to write concise loops that compile down into blazing fast plain PHP code:

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

Our task application is almost complete. But, we have no way to delete our existing tasks when they're done. Let's add that next!

할일 어플리케이션이 거의 완성되었습니다. 하지만 할일이 수행된 후 삭제하는 방법이 아직 없습니다. 다음은 이를 추가합니다.

<a name="deleting-tasks"></a>
## Deleting Tasks
## 할일 삭제하기

<a name="adding-the-delete-button"></a>
### Adding The Delete Button
### 삭제 버튼 추가하기

We left a "TODO" note in our code where our delete button is supposed to be. So, let's add a delete button to each row of our task listing within the `tasks.blade.php` view. We'll create a small single-button form for each task in the list. When the button is clicked, a `DELETE /task` request will be sent to the application:

"할일" 이 출력되는 곳 바로 옆에 삭제 버튼을 나타내는 코드를 구성할 것입니다. `tasks.blade.php` 뷰파일 안에 있는 할일 목록의 각 행에 삭제 버튼을 추가합니다. 각각의 할일 옆에 작은 버튼을 구성할 것입니다. 버튼을 누르면 `DELETE /task` 요청이 어플리케이션에 전달될 것입니다.

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
#### A Note On Method Spoofing
#### 메소드 스푸핑에 대해서 알기

Note that the delete button's form `method` is listed as `POST`, even though we are responding to the request using a `Route::delete` route. HTML forms only allow the `GET` and `POST` HTTP verbs, so we need a way to spoof a `DELETE` request from the form.

`Route::delete` 라우트를 이용해서 요청에 대응할 때에도 삭제 버튼의 폼 `method`는 `POST`로 구성되어 있습니다. HTML 폼은 `GET` 과 `POST` HTTP 메소드만을 허용하기 때문에 폼에 `DELETE` 요청을 나타낼 수 있는 방법이 필요합니다.

We can spoof a `DELETE` request by outputting the results of the `method_field('DELETE')` function within our form. This function generates a hidden form input that Laravel recognizes and will use to override the actual HTTP request method. The generated field will look like the following:

폼을 구성하는 내부에 `method_field('DELETE')` 핼퍼 함수의 결과를 출력해서 `DELETE` 요청을 흉내낼 수 있습니다. 이 핼퍼 함수는 숨겨진 형식의 인풋을 생성하고 라라벨은 이를 인식하여 실제 HTTP 요청 메소드를 대체할 것입니다. 생성된 필드는 다음과 같을 것입니다:

	<input type="hidden" name="_method" value="DELETE">

<a name="deleting-the-task"></a>
### Deleting The Task
### 할일 삭제하기

Finally, let's add logic to our route to actually delete the given task. We can use the Eloquent `findOrFail` method to retrieve a model by ID or throw a 404 exception if the model does not exist. Once we retrieve the model, we will use the `delete` method to delete the record. Once the record is deleted, we will redirect the user back to the `/` URL:

마지막으로, 실제로 할일를 삭제할 수 있도록 라우트에 로직을 추가합니다. Eloquent의 `findOrFail` 메소드를 통해 ID로 모델을 찾거나 모델이 존재하지 않을 경우 404 예외를 던질 수 있습니다. 모델을 찾았을 경우 `delete` 메소드로 할일을 삭제합니다. 할일이 삭제되면 사용자는 `/` URL로 다시 돌아갑니다.:

	Route::delete('/task/{id}', function ($id) {
		Task::findOrFail($id)->delete();

		return redirect('/');
	});
