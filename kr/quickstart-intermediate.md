# Intermediate Task List
# 할일 목록 구성하기(중급)

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Installation](#installation)
- [설치하기](#installation)
- [Prepping The Database](#prepping-the-database)
- [Prepping The Database](#prepping-the-database)
	- [Database Migrations](#database-migrations)
	- [Database Migrations](#database-migrations)
	- [Eloquent Models](#eloquent-models)
	- [Eloquent Models](#eloquent-models)
	- [Eloquent Relationships](#eloquent-relationships)
	- [Eloquent Relationships](#eloquent-relationships)
- [Routing](#routing)
- [Routing](#routing)
	- [Displaying A View](#displaying-a-view)
	- [Displaying A View](#displaying-a-view)
	- [Authentication](#authentication-routing)
	- [Authentication](#authentication-routing)
	- [The Task Controller](#the-task-controller)
	- [The Task Controller](#the-task-controller)
- [Building Layouts & Views](#building-layouts-and-views)
- [Building Layouts & Views](#building-layouts-and-views)
	- [Defining The Layout](#defining-the-layout)
	- [Defining The Layout](#defining-the-layout)
	- [Defining The Child View](#defining-the-child-view)
	- [Defining The Child View](#defining-the-child-view)
- [Adding Tasks](#adding-tasks)
- [Adding Tasks](#adding-tasks)
	- [Validation](#validation)
	- [Validation](#validation)
	- [Creating The Task](#creating-the-task)
	- [Creating The Task](#creating-the-task)
- [Displaying Existing Tasks](#displaying-existing-tasks)
- [Displaying Existing Tasks](#displaying-existing-tasks)
	- [Dependency Injection](#dependency-injection)
	- [Dependency Injection](#dependency-injection)
	- [Displaying The Tasks](#displaying-the-tasks)
	- [Displaying The Tasks](#displaying-the-tasks)
- [Deleting Tasks](#deleting-tasks)
- [Deleting Tasks](#deleting-tasks)
	- [Adding The Delete Button](#adding-the-delete-button)
	- [Adding The Delete Button](#adding-the-delete-button)
	- [Route Model Binding](#route-model-binding)
	- [Route Model Binding](#route-model-binding)
	- [Authorization](#authorization)
	- [Authorization](#authorization)
	- [Deleting The Task](#deleting-the-task)
	- [Deleting The Task](#deleting-the-task)

<a name="introduction"></a>
## Introduction
## 시작하기

This quickstart guide provides an intermediate introduction to the Laravel framework and includes content on database migrations, the Eloquent ORM, routing, authentication, authorization, dependency injection, validation, views, and Blade templates. This is a great starting point if you are familiar with the basics of the Laravel framework or PHP frameworks in general.

이 퀵스타트 가이드는 중급 이상의 사용자들을 위해서 라라벨 프레임워크에 대한 소개와 데이터베이스 마이그레이션, Eloquent ORM, 라우팅, validation-검증, views-뷰, 블레이드 템플릿과 관련한 내용을 포함하고 있습니다. 라라벨 프레임워크와 PHP 프레임워크에 대한 기본적인 지식을 가지고 있다면 좋은 출발점이 될 것입니다.

To sample a basic selection of Laravel features, we will build a task list we can use to track all of the tasks we want to accomplish (the typical "to-do list" example). In contrast to the "basic" quickstart, this tutorial will allow users to create accounts and authenticate with the application. The complete, finished source code for this project is [available on GitHub](https://github.com/laravel/quickstart-intermediate).

라라벨의 기본 기능들에 대한 샘플을 확인하기 위해서 간단하게 할일 목록을 만들고, 진행하고자 하는 모든 할일들을 트래킹할 수 있게 할 것입니다. ("해야 할 일" 목록) "기본" 퀵스타트와 달리 이 튜토리얼은 사용자들이 계정을 생성하고 애플리케이션을 통해 인증을 할 수 있도록 할것입니다. 이 프로젝트의 완성된 소스 코드는 [Github 에 공개되어 있습니다](https://github.com/laravel/quickstart-intermediate).

<a name="installation"></a>
## Installation
## 설치하기

Of course, first you will need a fresh installation of the Laravel framework. You may use the [Homestead virtual machine](/docs/{{version}}/homestead) or the local PHP environment of your choice to run the framework. Once your local environment is ready, you may install the Laravel framework using Composer:

가장 먼저 라라벨 프레임워크를 새롭게 설치해야 합니다. [홈스테드 가상머신](/docs/{{version}}/homestead)을 사용하거나, 또는 여러분이 원하는 로컬 PHP 환경을 선택하여 프레임워크를 실행할 수 있습니다. 로컬 개발 환경이 준비되면 Composer를 이용해 라라벨 프레임워크를 설치할 수 있습니다.

	composer create-project laravel/laravel quickstart --prefer-dist

You're free to just read along for the remainder of this quickstart; however, if you would like to download the source code for this quickstart and run it on your local machine, you may clone its Git repository and install its dependencies:

이제부터 나오는 내용은 눈으로 보기만 하셔도 됩니다. 하지만 이 퀵스타트의 소스 코드를 다운받아서 로컬 개발 머신에 실행하고 싶으시다면 Git 저장소를 복제하여 의존 패키지들을 설치하면 됩니다.

	git clone https://github.com/laravel/quickstart-intermediate quickstart
	cd quickstart
	composer install
	php artisan migrate

For more complete documentation on building a local Laravel development environment, check out the full [Homestead](/docs/{{version}}/homestead) and [installation](/docs/{{version}}/installation) documentation.

로컬 라라벨 개발 환경 구성에 대해 더 많은 정보를 얻기 원하신다면 [홈스테드](/docs/{{version}}/homestead)와 [설치하기](/docs/{{version}}/installation) 메뉴얼을 확인하십시오.

<a name="prepping-the-database"></a>
## Prepping The Database
## 데이터베이스 준비사항

<a name="database-migrations"></a>
### Database Migrations
### 데이터베이스 마이그레이션

First, let's use a migration to define a database table to hold all of our tasks. Laravel's database migrations provide an easy way to define your database table structure and modifications using fluent, expressive PHP code. Instead of telling your team members to manually add columns to their local copy of the database, your teammates can simply run the migrations you push into source control.

먼저 마이그레이션을 이용하여 모든 할일 목록을 저장할 데이터베이스를 정의합니다. 라라벨의 데이터베이스 마이그레이션은 유연하고 다양한 표현이 가능한 PHP 코드를 사용하여 데이터베이스 테이블 구조를 정의하고, 또 수정하기에 용이합니다. 협업을 진행하는 팀 멤버들에게 데이터베이스의 복사본에 복사하여 수동으로 컬럼을 추가하라고 하는 대신에, 여러분이 소스 제어에 추가한 마이그레이션 파일을 그냥 실행하기만하면 됩니다.

#### The `users` Table
#### `users` 테이블

Since we are going to allow users to create their accounts within the application, we will need a table to store all of our users. Thankfully, Laravel already ships with a migration to create a basic `users` table, so we do not need to manually generate one. The default migration for the `users` table is located in the `database/migrations` directory.

사용자들이 애플리케이션 내에서 계정을 생성할 수 있도록 할 것이기 때문에, 모든 사용자 정보를 저장할 테이블이 필요합니다. 다행히 라라벨은 기본적인 `users` 테이블을 생성하는 마이그레이션 파일을 이미 가지고 있기 때문에, 수동으로 생성할 필요가 없습니다. `users` 테이블을 생성하기 위한 기본 마이그레이션은 `database/migrations` 디렉토리에서 찾을 수 있습니다.

#### The `tasks` Table
#### `tasks` 테이블

Next, let's build a database table that will hold all of our tasks. The [Artisan CLI](/docs/{{version}}/artisan) can be used to generate a variety of classes and will save you a lot of typing as you build your Laravel projects. In this case, let's use the `make:migration` command to generate a new database migration for our `tasks` table:

그럼 이제 모든 할일들을 저장할 데이터베이스 테이블을 만들겠습니다. [아티즌 CLI](/docs/{{version}}/artisan)을 사용하여 다양한 클래스를 생성하면 라라벨 프로젝트들을 구성할 때, 매번 타이핑할 필요가 없어서 효율성을 높일 수 있습니다. 이 경우에서는 `make:migration`명령어를 사용하여 `tasks` 테이블을 위한 새로운 데이터베이스 마이그레이션 파일을 생성합니다:

	php artisan make:migration create_tasks_table --create=tasks

The migration will be placed in the `database/migrations` directory of your project. As you may have noticed, the `make:migration` command already added an auto-incrementing ID and timestamps to the migration file. Let's edit this file and add an additional `string` column for the name of our tasks, as well as a `user_id` column which will link our `tasks` and `users` tables:

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

To run our migrations, we will use the `migrate` Artisan command. If you are using Homestead, you should run this command from within your virtual machine, since your host machine will not have direct access to the database:

`migrate` 아티즌 커맨드를 이용하여 마이그레이션들을 실행합니다. 홈스테드를 이용하고 있다면 호스트 머신에서 데이터베이스에 직접 접속할 수 없기 때문에 홈스테드 가상 머신 안에서 커맨드를 실행해야 합니다.

	php artisan migrate

This command will create all of our database tables. If you inspect the database tables using the database client of your choice, you should see new `tasks` and `users` tables which contains the columns defined in our migration. Next, we're ready to define our Eloquent ORM models!

이 커맨드는 모든 데이터베이스 테이블을 생성합니다. 특정 데이터베이스 클라이언트를 사용하여 데이터베이스를 확인하면 마이그레이션 테이블에 추가한 컬럼들을 포함한 새 `tasks` 테이블과 `users` 테이블을 확인할 수 있을 것입니다. 이제 Eloquent ORM 모델을 정의할 수 있습니다.

<a name="eloquent-models"></a>
### Eloquent Models

[Eloquent](/docs/{{version}}/eloquent) is Laravel's default ORM (object-relational mapper). Eloquent makes it painless to retrieve and store data in your database using clearly defined "models". Usually, each Eloquent model corresponds directly with a single database table.

[Eloquent](/docs/{{version}}/eloquent)은 라라벨의 기본 ORM입니다. Eloquent를 사용하면 명료하게 정의된 "모델"들을 이용하여 데이터베이스에 데이터를 저장하고 조회하기 용이합니다. 보통 각 Eloquent 모델은 하나의 데이터베이스 테이블에 대응합니다.

#### The `User` Model
#### `User` 모델

First, we need a model that corresponds to our `users` database table. However, if you look in the `app` directory of your project, you will see that Laravel already ships with a `User` model, so we do not need to generate one manually.

먼저 `users` 데이터베이스 테이블에 대응하는 모델이 필요합니다. 그러나, `app` 디렉토리를 확인해 보면면 라라벨이 이미 `User` 모델을 제공한다는 것을 알 수 있으며 그렇기 때문에 수동적으로 생성하지 않아도 됩니다.

#### The `Task` Model
#### `Task` 모델

So, let's define a `Task` model that corresponds to our `tasks` database table we just created. Again, we can use an Artisan command to generate this model. In this case, we'll use the `make:model` command:

이제 방금 생성한 `tasks` 데이터베이스 테이블에 대응하는 `Task` 모델을 정의합니다. 이번에도 아티즌 커맨드를 통해 이 모델을 생성할 수 있습니다. 이 경우 `make:model` 명령어를 사용합니다:

	php artisan make:model Task

The model will be placed in the `app` directory of your application. By default, the model class is empty. We do not have to explicitly tell the Eloquent model which table it corresponds to because it will assume the database table is the plural form of the model name. So, in this case, the `Task` model is assumed to correspond with the `tasks` database table.

이 모델은 애플리케이션의 `app` 디렉토리에 위치할 것입니다. 모델 클래스는 기본적으로 비어있습니다. Eloquent 모델은 데이터베이스 테이블이 모델 이름의 복수형태라 추정하기 때문에 직접적으로 어느 테이블에 대응하는지 명시할 필요가 없습니다. 그렇기 때문에 이 경우 `Task` 모델은 `tasks` 데이터베이스 테이블에 대응할 것으로 추정됩니다. 아래는 빈 모델이 어떻게 나타나야 하는지 보여줍니다.

Let's add a few things to this model. First, we will state that the `name` attribute on the model should be "mass-assignable":

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

We'll learn more about how to use Eloquent models as we add routes to our application. Of course, feel free to consult the [complete Eloquent documentation](/docs/{{version}}/eloquent) for more information.

모델을 애플리케이션에 라우트에 추가하는 것과 같이 Eloquent 모델의 사용법에 대해서 배워볼 것입니다. 더 많은 정보를 얻고 싶으시다면 [Eloquent 문서](/docs/{{version}}/eloquent)를 참고 하십시오.

<a name="eloquent-relationships"></a>
### Eloquent Relationships
### Eloquent 관계 정의하기

Now that our models are defined, we need to link them. For example, our `User` can have many `Task` instances, while a `Task` is assigned to one `User`. Defining a relationship will allow us to fluently walk through our relations like so:

모델들을 정의하고 나면, 이것들을 서로 연결시켜야 합니다. 예를 들어서 `User`는 많은 `Task`를 가질 수 있지만 하나의 `Task`는 하나의 `User`에만 할당될 수 있습니다. 하나의 관계를 정의하는 것은 다음 처럼 모델에서 관계를 통해서 유연한 접근이 가능하게 할것입니다:

	$user = App\User::find(1);

	foreach ($user->tasks as $task) {
		echo $task->name;
	}

#### The `tasks` Relationship
#### `tasks` 관계 정의하기

First, let's define the `tasks` relationship on our `User` model. Eloquent relationships are defined as methods on models. Eloquent supports several different types of relationships, so be sure to consult the [full Eloquent documentation](/docs/{{version}}/eloquent-relationships) for more information. In this case, we will define a `tasks` function on the `User` model which calls the `hasMany` method provided by Eloquent:

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

#### The `user` Relationship
#### `user` 관계 정의하기

Next, let's define the `user` relationship on the `Task` model. Again, we will define the relationship as a method on the model. In this case, we will use the `belongsTo` method provided by Eloquent to define the relationship:

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

Wonderful! Now that our relationships are defined, we can start building our controllers!

좋습니다! 관계들이 정의되었으니, 이제 컨트롤러 구성을 시작할 수 있습니다.

<a name="routing"></a>
## Routing
## 라우팅

In the [basic version](/docs/{{version}}/quickstart) of our task list application, we defined all of our logic using Closures within our `routes.php` file. For the majority of this application, we will use [controllers](/docs/{{version}}/controllers) to organize our routes. Controllers will allow us to break out HTTP request handling logic across multiple files for better organization.

할일 목록 애플리케이션의 [기본 버전](/docs/{{version}}/quickstart)에서는 모든 로직을 `routes.php` 파일의 클로저들을 이용해 정의했습니다. 이 어플레케이션에서는 라우트를 생성할 때 거의 대부분 [컨트롤러](/docs/{{version}}/controllers)를 사용할 것입니다. 컨트롤러를 통해서 HTTP request-요청을 처리하는 로직을 여러개의 파일로 쪼갤 수 있기 때문에, 더 나은 체계를 가능하게 해줍니다.

<a name="displaying-a-view"></a>
### Displaying A View

We will have a single route that uses a Closure: our `/` route, which will simply be a landing page for application guests. So, let's fill out our `/` route. From this route, we want to render an HTML template that contains the "welcome" page:

`/`를 위한 라우트는 Closure를 사용하는 하나의 라우트로, 단순히 애플리케이션 방문자들의 랜딩 페이지가 될것입니다. 이제 `/` 라우트를 채우도록 하겠습니다. 이 라우트에서 "웰컴" 페이지를 가지는 HTML 템플릿을 렌더링할 수 있도록 합니다.

In Laravel, all HTML templates are stored in the `resources/views` directory, and we can use the `view` helper to return one of these templates from our route:

라라벨에서는 `resources/views` 디렉토리에 모든 HTML 템플릿들이 저장되어 있으며 `view` 헬퍼를 통해 라우트에서 이 템플릿들을 반환할 수 있습니다:

	Route::get('/', function () {
		return view('welcome');
	});

Of course, we need to actually define this view. We'll do that in a bit!

물론 먼저 이 뷰 파일을 실제로 정의해야 합니다. 조금 있다가 이것을 할것입니다.

<a name="authentication-routing"></a>
### Authentication
### 사용자 인증

Remember, we also need to let users create accounts and login to our application. Typically, it can be a tedious task to build an entire authentication layer into a web application. However, since it is such a common need, Laravel attempts to make this procedure totally painless.

사용자들이 계정을 생성하고 애플리케이션에 로그인할 수 있도록 하는 것 또한 필요합니다. 보통 인증 레이어 전체를 웹 애플리케이션에 구성하려면, 지겨운 과정을 거쳐야 합니다. 하지만 공통적으로 필요한 사항이기 때문에, 라라벨은 이러한 작업을 전혀 고통스럽지 않게 처리해줍니다.

First, notice that there is already a `app/Http/Controllers/Auth/AuthController` included in your Laravel application. This controller uses a special `AuthenticatesAndRegistersUsers` trait which contains all of the necessary logic to create and authenticate users.

우선 라라벨 애플리케이션에 이미 `app/Http/Controllers/Auth/AuthController`가 포함된 것을 확인할 수 있습니다. 이 컨트롤러는 사용자의 계정을 생성하고 승인하는데 필요한 모든 로직을 포함한 `AuthenticatesAndRegistersUsers` 트레이트-trait 을 이용합니다.

#### Authentication Routes
#### 인증 라우트

So, what's left for us to do? Well, we still need to create the registration and login templates as well as define the routes to point to the authentication controller. First, let's add the routes we need to our `app/Http/routes.php` file:

이제 남은 작업이 무엇일까요? 아직 사용자 등록과 로그인 템플릿을 생성하고 사용자 인증 컨트롤러를 위한 라우트를 정의해야 합니다. 먼저 `app/Http/routes.php` 파일에 필요한 라우트들을 추가해보겠습니다:

	// Authentication Routes...
	Route::get('auth/login', 'Auth\AuthController@getLogin');
	Route::post('auth/login', 'Auth\AuthController@postLogin');
	Route::get('auth/logout', 'Auth\AuthController@getLogout');

	// Registration Routes...
	Route::get('auth/register', 'Auth\AuthController@getRegister');
	Route::post('auth/register', 'Auth\AuthController@postRegister');

#### Authentication Views
#### 인증 뷰-views 파일 구성하기

Authentication requires us to create `login.blade.php` and `register.blade.php` within the `resources/views/auth` directory. Of course, the design and styling of these views is unimportant; however, they should at least contain some basic fields.

사용자 인증을 위해서는 `resources/views/auth` 디렉토리 내에 `login.blade.php` 와 `register.blade.php`을 생성해야 합니다. 물론 이 뷰들의 디자인과 스타일은 중요하지 않습니다. 하지만 최소한 몇가지 기본적인 필드를 가지고 있어야만 합니다.

The `register.blade.php` file should contain a form that includes `name`, `email`, `password`, and `password_confirmation` fields and makes a `POST` request to the `/auth/register` route.

`register.blade.php` 파일은 `name`, `email`, `password`, and `password_confirmation` 필드들을 포함하고 `/auth/register` 라트에 `POST` 요청을 하는 form을 가지고 있어야 합니다.

The `login.blade.php` file should contain a form that includes `email` and `password` fields and makes a `POST` request to `/auth/login`.

`login.blade.php` 파일은 `email`과 `password` 필드들을 포함하고 `/auth/login` 에 `POST` 요청을 하는 form을 가지고 있어야 합니다.

> **Note:** If you would like to view complete examples for these views, remember that the entire application's source code is [available on GitHub](https://github.com/laravel/quickstart-intermediate).

> **Note:** 이 뷰파일들의 완성된 예제들을 보고 싶으시다면 [GitHub](https://github.com/laravel/quickstart-intermediate)에서 전체 소스 코드를 보실 수 있습니다.

<a name="the-task-controller"></a>
### The Task Controller
### Task 컨트롤러

Since we know we're going to need to retrieve and store tasks, let's create a `TaskController` using the Artisan CLI, which will place the new controller in the `app/Http/Controllers` directory:

할일들을 획득하고 저장하기 위해서 아티즌 CLI를 이용해 `TaskController` 를 생성해야 하며 이를 통해 생성된 컨트롤러는 `app/Http/Controllers` 에 위치하게 됩니다.

	php artisan make:controller TaskController --plain

Now that the controller has been generated, let's go ahead and stub out some routes in our `app/Http/routes.php` file to point to the controller:

이제 컨트롤러가 생성되었으니 `app/Http/routes.php` 파일에 컨트롤러를 가리키는 라우트들을 생성해보겠습니다:

	Route::get('/tasks', 'TaskController@index');
	Route::post('/task', 'TaskController@store');
	Route::delete('/task/{task}', 'TaskController@destroy');

#### Authenticating All Task Routes
#### 모든 할일 관련 라우트들에 대한 인증

For this application, we want all of our task routes to require an authenticated user. In other words, the user must be "logged into" the application in order to create a task. So, we need to restrict access to our task routes to only authenticated users. Laravel makes this a cinch using [middleware](/docs/{{version}}/middleware).

이 애플리케이션에서는 모든 할일 라우트들이 사용자들의 인증을 요구하도록 합니다. 즉, 할일를 생성하기 위해서, 사용자는 애플리케이션에 "로그인한" 상태여야만 합니다. 그렇기 때문에 승인된 사용자들에만 할일 라우트에 접근할 수 있도록 제한을 해야 합니다. 라라벨은 [미들웨어](/docs/{{version}}/middleware)을 통해 이 과정을 손쉽게 만듭니다.

To require an authenticated users for all actions on the controller, we can add a call to the `middleware` method from the controller's constructor. All available route middleware are defined in the `app/Http/Kernel.php` file. In this case, we want to assign the `auth` middleware to all actions on the controller:

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
## Building Layouts & Views
## 레이아웃 & 뷰 구성하기

This application only has a single view which contains a form for adding new tasks as well as a listing of all current tasks. To help you visualize the view, here is a screenshot of the finished application with basic Bootstrap CSS styling applied:

이 애플리케이션은 현재의 할일 목록과 새 할일를 추가하는 형식을 가진 view를 하나만 가지고 있습니다. View를 시각화하는데 도움을 주기 위해 아래에 기본적인 부트스트랩 CSS 스타일을 적용한 완성된 애플리케이션의 스크린샷을 제공합니다:

![Application Image](https://laravel.com/assets/img/quickstart/basic-overview.png)

<a name="defining-the-layout"></a>
### Defining The Layout
### 레이아웃 정의하기

Almost all web applications share the same layout across pages. For example, this application has a top navigation bar that would be typically present on every page (if we had more than one). Laravel makes it easy to share these common features across every page using Blade **layouts**.

거의 모든 웹애플리케이션은 모든 페이지에 동일한 레이아웃을 사용합니다. 예를 들어 이 애플리케이션은 모든 페이지 위에(페이지가 하나 이상일 경우) 탐색 바가 표시됩니다. 라라벨은 블레이드 **layouts**를 통해 이런 공통의 구성을 모든 페이지에 적용하기 쉽게 해줍니다.

As we discussed earlier, all Laravel views are stored in `resources/views`. So, let's define a new layout view in `resources/views/layouts/app.blade.php`. The `.blade.php` extension instructs the framework to use the [Blade templating engine](/docs/{{version}}/blade) to render the view. Of course, you may use plain PHP templates with Laravel. However, Blade provides convenient short-cuts for writing cleaner, terse templates.

앞서 얘기한 바와 같이, 라라벨의 모든 뷰 파일들은 `resources/views`에 저장되어 있습니다. 그럼 이제 `resources/views/layouts/app.blade.php`에 새 레이아웃 뷰 파일를 정의하겠습니다. `.blade.php` 익스텐션은 뷰를 렌더링하기 위해 [블레이드 템플릿 엔진](/docs/{{version}}/blade)를 이용하라고 프레임워크에 지시합니다. 물론 라라벨은 순수 PHP 템플릿들을 사용할 수 있습니다. 하지만 블레이드는 더 깨끗하고 간략한 템플릿을 쓸 수 있는 편리한 방법입니다.

Our `app.blade.php` view should look like the following:

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

Note the `@yield('content')` portion of the layout. This is a special Blade directive that specifies where all child pages that extend the layout can inject their own content. Next, let's define the child view that will use this layout and provide its primary content.

레이아웃의 `@yield('content')` 부분을 주목하십시오. 이것은 레이아웃을 상속받는 모든 자식 페이지들이 어느 곳에 자신의 콘텐츠를 주입할 수 있는지 지정해주는 특별한 블레이드 지시어 입니다. 이제 이 레이아웃을 사용하고 주요 콘텐츠를 제공할 자식 뷰를 정의해 보겠습니다.

<a name="defining-the-child-view"></a>
### Defining The Child View
### 자식 뷰 파일 정의하기

Great, our application layout is finished. Next, we need to define a view that contains a form to create a new task as well as a table that lists all existing tasks. Let's define this view in `resources/views/tasks/index.blade.php`, which will correspond to the `index` method in our `TaskController`.

이제 애플리케이션의 레이아웃이 완성되었습니다. 다음은 현재 할일 목록 테이블과 새로운 할일을 생성하는 form을 가진 뷰를 정의해야 합니다. `resources/views/tasks.blade.php`에서 뷰파일을 정의합니다.

We'll skip over some of the Bootstrap CSS boilerplate and only focus on the things that matter. Remember, you can download the full source for this application on [GitHub](https://github.com/laravel/quickstart-intermediate):

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

#### A Few Notes Of Explanation
#### 간단한 설명

Before moving on, let's talk about this template a bit. First, the `@extends` directive informs Blade that we are using the layout we defined at `resources/views/layouts/app.blade.php`. All of the content between `@section('content')` and `@endsection` will be injected into the location of the `@yield('content')` directive within the `app.blade.php` layout.

다음으로 넘어가기 전에 이 템플릿에 대하여 잠시 알아보겠습니다. 먼저 `@extends` 명령은 `resources/views/layouts/app.blade.php`에서 정의한 레이아웃을 사용한다는 것을 블레이드에 알려줍니다. `@section('content')` 와 `@endsection` 사이에 존재하는 모든 콘텐츠는 `app.blade.php` 레이아웃 안에 있는 `@yield('content')`에 주입될 것입니다.

Now we have defined a basic layout and view for our application. Let's go ahead and return this view from the `index` method of our `TaskController`:

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

Next, we're ready to add code to our `POST /task` route's controller method to handle the incoming form input and add a new task to the database.

이제, 넘어오는 form 입력값들을 처리하고 새 할일을 데이터베이스에 추가하기 위해 `POST /task` 라우트에 코드를 추가할 수 있습니다.

> **Note:** The `@include('common.errors')` directive will load the template located at `resources/views/common/errors.blade.php`. We haven't defined this template, but we will soon!

> **주의:** `@include('common.errors')` 지시어는 `resources/views/common/errors.blade.php`에 위치한 템플릿을 로드할 것입니다. 이 템플릿은 아직 정의되지 않았으며 다음에서 정의하겠습니다.

<a name="adding-tasks"></a>
## Adding Tasks
## 할일 추가하기

<a name="validation"></a>
### Validation
### 입력값 검증하기

Now that we have a form in our view, we need to add code to our `TaskController@store` method to validate the incoming form input and create a new task. First, let's validate the input.

이제 뷰에 form이 있으며 유입되는 form 입력값을 검증하고 새로운 할일를 생성하기 위해서 `POST /task` 라우트에 코드를 추가해야 합니다. 먼저 입력값을 검증하겠습니다.

For this form, we will make the `name` field required and state that it must contain less than `255` characters. If the validation fails, we want to redirect the user back to the `/tasks` URL, as well as flash the old input and errors into the [session](/docs/{{version}}/session):

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

If you followed along with the [basic quickstart](/docs/{{version}}/quickstart), you'll notice this validation code looks quite a bit different! Since we are in a controller, we can leverage the convenience of the `ValidatesRequests` trait that is included in the base Laravel controller. This trait exposes a simple `validate` method which accepts a request and an array of validation rules.

만약에 [기본적인 퀵스타트](/docs/{{version}}/quickstart)를 함께 보고 계셨다면 이 입력값 검증 코드가 다르다는 것을 알 수 있을 것입니다. 컨트롤러 내에 있기 때문에 기본 라라벨 컨트롤러에 포함되어 있는 `ValidatesRequests` 트레이트-trait의 편리함을 이용할 수 있습니다. 이 트레이트-trait은 request-요청과 입력값 검증 규칙들의 배열을 받아들이는 단순한 `validate` 메소드를 제공합니다.

We don't even have to manually determine if the validation failed or do manual redirection. If the validation fails for the given rules, the user will automatically be redirected back to where they came from and the errors will automatically be flashed to the session. Nice!

수동으로 입력값 검증이 실패했는지 판단하거나 수동으로 리다이렉트 할 필요도 없습니다. 주어진 규칙에 대해서 입력값 검증이 실패한다면 사용자는 자동으로 시작한 지점으로 돌아가게 되고 오류들이 자동으로 세션에 저장되어질 것입니다.

#### The `$errors` Variable
#### `$errors` 변수

Remember that we used the `@include('common.errors')` directive within our view to render the form's validation errors. The `common.errors` will allow us to easily show validation errors in the same format across all of our pages. Let's define the contents of this view now:

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


> **Note:** The `$errors` variable is available in **every** Laravel view. It will simply be an empty instance of `ViewErrorBag` if no validation errors are present.

> **주의:** `$errors` 변수는 **모든** 라라벨 뷰에서 사용 가능합니다. 입력값 검증의 오류가 존재하지 않는 경우에는, 에러 변수는 단순히 `ViewErrorBag`의 비어 있는 인스턴스일 것입니다.

<a name="creating-the-task"></a>
### Creating The Task
### 할일 생성하기

Now that input validation is handled, let's actually create a new task by continuing to fill out our route. Once the new task has been created, we will redirect the user back to the `/tasks` URL. To create the task, we are going to leverage the power of Eloquent's relationships.

이제, 입력값 검증 처리되었으니, 라우트를 계속해서 채워나가면서 새로운 할일를 생성해 보겠습니다. 새로운 할일이 생성되면 사용자는 `/` URL로 리다이렉트 될것입니다. 할일를 생성하기 위해 Eloquent의 관계를 설정한 기능을 활용하겠습니다.

Most of Laravel's relationships expose a `create` method, which accepts an array of attributes and will automatically set the foreign key value on the related model before storing it in the database. In this case, the `create` method will automatically set the `user_id` property of the given task to the ID of the currently authenticated user, which we are accessing using `$request->user()`:

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

Great! We can now successfully create tasks. Next, let's continue adding to our view by building a list of all existing tasks.

좋습니다. 이제 성공적으로 할일를 생성할 수 있습니다. 다음으로, 존재하는 모든 할일 목록을 뷰에 계속 추가하도록 하겠습니다.

<a name="displaying-existing-tasks"></a>
## Displaying Existing Tasks
## 존재하는 할일들 표시하기

First, we need to edit our `TaskController@index` method to pass all of the existing tasks to the view. The `view` function accepts a second argument which is an array of data that will be made available to the view, where each key in the array will become a variable within the view. For example, we could do this:

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

However, let's explore some of the dependency injection capabilities of Laravel to inject a `TaskRepository` into our `TaskController`, which we will use for all of our data access.

하지만 그전에, `TaskController`에, 모든 데이터를 접근하기 위해서 사용될 `TaskRepository`를 주입하도록 라라벨의 의존성 주입에 대해서 알아보도록 하겠습니다.

<a name="dependency-injection"></a>
### Dependency Injection
### 의존성 주입

Laravel's [service container](/docs/{{version}}/container) is one of the most powerful features of the entire framework. After reading this quickstart, be sure to read over all of the container's documentation.

[서비스 컨테이너](/docs/{{version}}/container)는 라라벨 프레임워크의 가장 강력한 기능 중 하나입니다. 이 퀵스타트를 읽은 뒤 컨테이너에 대한 문서를 꼭 읽어 보시기 바랍니다.

#### Creating The Repository
#### Repository 생성하기

As we mentioned earlier, we want to define a `TaskRepository` that holds all of our data access logic for the `Task` model. This will be especially useful if the application grows and you need to share some Eloquent queries across the application.

앞서 이야기한것처럼, `Task` 모델의 모든 데이터에 엑세스 할 수 있는 로직을 포함하는 `TaskRepository`를 정의해야 합니다. 이렇게 하는 것은 애플리케이션의 규모가 커지거나, Eloqent 쿼리를 애플리케이션 전반 걸쳐서 공유해야 할 때 특히 유용합니다.

So, let's create an `app/Repositories` directory and add a `TaskRepository` class. Remember, all Laravel `app` folders are auto-loaded using the PSR-4 auto-loading standard, so you are free to create as many extra directories as needed:

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

#### Injecting The Repository
#### Repository 주입하기

Once our repository is defined, we can simply "type-hint" it in the constructor of our `TaskController` and utilize it within our `index` route. Since Laravel uses the container to resolve all controllers, our dependencies will automatically be injected into the controller instance:

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
### Displaying The Tasks
### 할일들 보여주기

Once the data is passed, we can spin through the tasks in our `tasks/index.blade.php` view and display them in a table. The `@foreach` Blade construct allows us to write concise loops that compile down into blazing fast plain PHP code:

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

Our task application is almost complete. But, we have no way to delete our existing tasks when they're done. Let's add that next!

할일 관리 애플리케이션이 이제 거의 완성되었습니다. 하지만 할일이 완료된 후 삭제하는 방법이 아직 없습니다. 다음은 이를 추가합니다.

<a name="deleting-tasks"></a>
## Deleting Tasks
## 할일 삭제하기

<a name="adding-the-delete-button"></a>
### Adding The Delete Button
### 삭제 버튼 추가하기

We left a "TODO" note in our code where our delete button is supposed to be. So, let's add a delete button to each row of our task listing within the `tasks/index.blade.php` view. We'll create a small single-button form for each task in the list. When the button is clicked, a `DELETE /task` request will be sent to the application which will trigger our `TaskController@destroy` method:

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
#### A Note On Method Spoofing
#### 메소드 Spoofing-속이기

Note that the delete button's form `method` is listed as `POST`, even though we are responding to the request using a `Route::delete` route. HTML forms only allow the `GET` and `POST` HTTP verbs, so we need a way to spoof a `DELETE` request from the form.

`Route::delete` 라우트를 이용해서 request요청을 처리하지만, 삭제 버튼의 form `method`가 `POST`로 되어 있다는 것에 주의하십시오. HTML form은 단지 `GET` 과 `POST` HTTP 메소드만을 허용하기 때문에 form에 `DELETE` 요청을 흉내낼 수 있는 방법이 필요합니다.

We can spoof a `DELETE` request by outputting the results of the `method_field('DELETE')` function within our form. This function generates a hidden form input that Laravel recognizes and will use to override the actual HTTP request method. The generated field will look like the following:

form 안에서 `method_field('DELETE')` 함수의 결과를 출력하여 `DELETE` request-요청으로 흉내낼 수 있습니다. 이 기능은 hidden 형식의 input을 생성하고 라라벨은 이를 인식하여 실제 HTTP 요청 메소드를 대체할 것입니다. 생성된 필드는 다음과 같을 것입니다:

	<input type="hidden" name="_method" value="DELETE">

<a name="route-model-binding"></a>
### Route Model Binding
### 라우트 모델 바인딩

Now, we're almost ready to define the `destroy` method on our `TaskController`. But, first, let's revisit our route declaration for this route:

이제 `TaskController` 에 `destroy` 를 정의할 준비가 거의 다 되었습니다. 하지만, 우선 이 라우트의 선언부분을 다시 확인해봅시다:

	Route::delete('/task/{task}', 'TaskController@destroy');

Without adding any additional code, Laravel would inject the given task ID into the `TaskController@destroy` method, like so:

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

However, the very first thing we will need to do in this method is retrieve the `Task` instance from the database using the given ID. So, wouldn't it be nice if Laravel could just inject the `Task` instance that matches the ID in the first place? Let's make it happen!

하지만, 이 메소드가 가장 우선적으로 하는 일은 주어진 ID를 이용해서 데이터베이스에서 `Task` 인스턴스를 찾는 것입니다. 이런 경우, 라라벨이 처음부터 ID에 맞는 `Task` 인스턴스를 주입하게 된다면 편리하지 않을까요? 이렇게 할 수 있습니다.

In your `app/Providers/RouteServiceProvider.php` file's `boot` method, let's add the following line of code:

`app/Providers/RouteServiceProvider.php` 파일의 `boot` 메소드에 다음 코드를 추가합니다:

	$router->model('task', 'App\Task');

This small line of code will instruct Laravel to retrieve the `Task` model that corresponds to a given ID whenever it sees `{task}` in a route declaration. Now we can define our destroy method like so:

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
### Authorization
### Authorization-승인

Now, we have a `Task` instance injected into our `destroy` method; however, we have no guarantee that the authenticated user actually "owns" the given task. For example, a malicious request could have been concocted in an attempt to delete another user's tasks by passing a random task ID to the `/tasks/{task}` URL. So, we need to use Laravel's authorization capabilities to make sure the authenticated user actually owns the `Task` instance that was injected into the route.

이제 `Task` 인스턴스가 `destroy`메소드에 됩니다: 하지만 인증된 사용자가 주어진 할일을 실제로 "소유" 하고 있는지에 대한 보장이 없습니다. 예를 들어, `/tasks/{task}` URL에 랜덤한 할일 ID를 전달하여 다른 사용자의 할일을 삭제하려는 악의적인 요청이 있었을 수도 있습니다. 그렇기 때문에 라라벨의 authorization-승인 기능을 사용하여 승인된 사용자가 실제로 라우트에 주입된 `Task` 인스턴스를 소유하고 있도록 보장해야 합니다.

#### Creating A Policy
#### Policy 생성하기

Laravel uses "policies" to organize authorization logic into simple, small classes. Typically, each policy corresponds to a model. So, let's create a `TaskPolicy` using the Artisan CLI, which will place the generated file in `app/Policies/TaskPolicy.php`:

라라벨은 authorization-승인 로직을 간단하고 작은 클래스들로 구성하기 위하여 "policy-정책"들을 이용합니다. 보통 각 policy는 하나의 모델에 대응합니다. 아티즌 CLI를 이용하여 `TaskPolicy`를 생성하고 `app/Policies/TaskPolicy.php` 에 생성된 파일이 위치하도록 합니다.

	php artisan make:policy TaskPolicy

Next, let's add a `destroy` method to the policy. This method will receive a `User` instance and a `Task` instance. The method should simply check if the user's ID matches the `user_id` on the task. In fact, all policy methods should either return `true` or `false`:

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

Finally, we need to associate our `Task` model with our `TaskPolicy`. We can do this by adding a line in the `app/Providers/AuthServiceProvider.php` file's `$policies` property. This will inform Laravel which policy should be used whenever we try to authorize an action on a `Task` instance:

마지막으로 `Task` 모델을 `TaskPolicy`에 할당 해야합니다. 이 작업은 `app/Providers/AuthServiceProvider.php` 파일의 `$policies` 속성에 한 줄을 추가하는 것으로 할 수 있습니다. 이렇게 하면 `Task` 인스턴스에 동작을 authorize-승인할 때마다 라라벨이 어떤 policy-정책을 사용해야 하는지 알려줄 것입니다.

    /**
     * The policy mappings for the application.
     *
     * @var array
     */
    protected $policies = [
        Task::class => TaskPolicy::class,
    ];


#### Authorizing The Action
#### 액션을 Authorizing-승인하기

Now that our policy is written, let's use it in our `destroy` method. All Laravel controllers may call an `authorize` method, which is exposed by the `AuthorizesRequest` trait:

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

Let's examine this method call for a moment. The first argument passed to the `authorize` method is the name of the policy method we wish to call. The second argument is the model instance that is our current concern. Remember, we recently told Laravel that our `Task` model corresponds to our `TaskPolicy`, so the framework knows on which policy to fire the `destroy` method. The current user will automatically be sent to the policy method, so we do not need to manually pass it here.

잠시 이 메소드의 호출에 대해 생각해보겠습니다. `authorize` 메소드에 전달된 첫번째 인자는 호출하고자 하는 policy-정책 메소드의 이름입니다. 두 번째 인자는 현재 다루어야 할 모델의 인스턴스입니다. 앞서 이미 라라벨에 `Task` 모델이 `TaskPolicy` 에 대응한다는 것을 알려주었기 때문에, 프레임워크는 `destroy` 메소드가 실행되어야 하는 policy-정책을 알고 있습니다. 현재 사용자는 자동으로 policy-정책 메소드로 전달되기 때문에 수동으로 전달할 필요가 없습니다.

If the action is authorized, our code will continue executing normally. However, if the action is not authorized (meaning the policy's `destroy` method returned `false`), a 403 exception will be thrown and an error page will be displayed to the user.

액션이 승인되면 코드는 계속해서 정상적으로 실행될 것입니다. 하지만 액션이 승인되지 않을 경우 (정책의 `destroy` 메소드가 `false` 를 반환한다면), 403 예외가 던져질 것이며 사용자는 에러 페이지를 보게 될 것입니다.

> **Note:** There are several other ways to interact with the authorization services Laravel provides. Be sure to browse the complete [authorization documentation](/docs/{{version}}/authorization).

> **참고:** 라라벨이 제공하는 승인 서비스를 사용하는 여러 방법이 있습니다. [authorization-승인 문서](/docs/{{version}}/authorization)을 참고하십시오.

<a name="deleting-the-task"></a>
### Deleting The Task
### 할일 삭제하기

Finally, let's finish adding the logic to our `destroy` method to actually delete the given task. We can use Eloquent's `delete` method to delete the given model instance in the database. Once the record is deleted, we will redirect the user back to the `/tasks` URL:

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
