# Release Notes
# 릴리즈 노트

- [Laravel 5.0](#laravel-5.0)
- [Laravel 4.2](#laravel-4.2)
- [Laravel 4.1](#laravel-4.1)

<a name="laravel-5.0"></a>
## Laravel 5.0

Laravel 5.0 introduces a fresh application structure to the default Laravel project. This new structure serves as a better foundation for building robust application in Laravel, as well as embraces new auto-loading standards (PSR-4) throughout the application. First, let's examine some of the major changes:

### New Folder Structure

The old `app/models` directory has been entirely removed. Instead, all of your code lives directly within the `app` folder, and, by default, is organized to the `App` namespace. This default namespace can be quickly changed using the new `app:name` Artisan command.

Controllers, middleware, and requests (a new type of class in Laravel 5.0) are now grouped under the `app/Http` directory, as they are all classes related to the HTTP transport layer of your application. Instead of a single, flat file of route filters, all middleware are now broken into their own class files.

A new `app/Providers` directory replaces the `app/start` files from previous versions of Laravel 4.x. These service providers provide various bootstrapping functions to your application, such as error handling, logging, route loading, and more. Of course, you are free to create additional service providers for your application.

Application language files and views have been moved to the `resources` directory.

### Contracts

All major Laravel components implement interfaces which are located in the `illuminate/contracts` repository. This repository has no external dependencies. Having a convenient, centrally located set of interfaces you may use for decoupling and dependency injection will serve as an easy alternative option to Laravel Facades.

For more information on contracts, consult the [full documentation](/docs/5.0/contracts).

### Route Cache

If your application is made up entirely of controller routes, you may utilize the new `route:cache` Artisan command to drastically speed up the registration of your routes. This is primarily useful on applications with 100+ routes and will **drastically** speed up this portion of your application.

### Route Middleware

In addition to Laravel 4 style route "filters", Laravel 5 now supports HTTP middleware, and the included authentication and CSRF "filters" have been converted to middleware. Middleware provides a single, consistent interface to replace all types of filters, allowing you to easily inspect, and even reject, requests before they enter your application.

For more information on middleware, check out [the documentation](/docs/5.0/middleware).

### Controller Method Injection

In addition to the existing constructor injection, you may now type-hint dependencies on controller methods. The [service container](/docs/5.0/container) will automatically inject the dependencies, even if the route contains other parameters:

	public function createPost(Request $request, PostRepository $posts)
	{
		//
	}

### Authentication Scaffolding

User registration, authentication, and password reset controllers are now included out of the box, as well as simple corresponding views, which are located at `resources/views/auth`. In addition, a "users" table migration has been included with the framework. Including these simple resources allows rapid development of application ideas without bogging down on authentication boilerplate. The authentication views may be accessed on the `auth/login` and `auth/register` routes. The `App\Services\Auth\Registrar` service is responsible for user validation and creation.

### Event Objects

You may now define events as objects instead of simply using strings. For example, check out the following event:

	class PodcastWasPurchased {

		public $podcast;

		public function __construct(Podcast $podcast)
		{
			$this->podcast = $podcast;
		}

	}

The event may be dispatched like normal:

	Event::fire(new PodcastWasPurchased($podcast));

Of course, your event handler will receive the event object instead of a list of data:

	class ReportPodcastPurchase {

		public function handle(PodcastWasPurchased $event)
		{
			//
		}

	}

For more information on working with events, check out the [full documentation](/docs/5.0/events).

### Commands / Queueing

In addition to the queue job format supported in Laravel 4, Laravel 5 allows you to represent your queued jobs as simple command objects. These commands live in the `app/Commands` directory. Here's a sample command:

	class PurchasePodcast extends Command implements SelfHandling, ShouldBeQueued {

		use SerializesModels;

		protected $user, $podcast;

		/**
		 * Create a new command instance.
		 *
		 * @return void
		 */
		public function __construct(User $user, Podcast $podcast)
		{
			$this->user = $user;
			$this->podcast = $podcast;
		}

		/**
		 * Execute the command.
		 *
		 * @return void
		 */
		public function handle()
		{
			// Handle the logic to purchase the podcast...

			event(new PodcastWasPurchased($this->user, $this->podcast));
		}

	}

The base Laravel controller utilizes the new `DispatchesCommands` trait, allowing you to easily dispatch your commands for execution:

	$this->dispatch(new PurchasePodcastCommand($user, $podcast));

Of course, you may also use commands for tasks that are executed synchonrously (are not queued). In fact, using commands is a great way to encapsulate complex tasks your application needs to perform. For more information, check out the [command bus](/docs/5.0/bus) documentation.

### Database Queue

A `database` queue driver is now included in Laravel, providing a simple, local queue driver that requires no extra package installation beyond your database software.

### Laravel Scheduler

In the past, developers have generated a Cron entry for each console command they wished to schedule. However, this is a headache. Your console schedule is no longer in source control, and you must SSH into your server to add the Cron entries. Let's make our lives easier. The Laravel command scheduler allows you to fluently and expressively define your command schedule within Laravel itself, and only a single Cron entry is needed on your server.

It looks like this:

	$schedule->command('artisan:command')->dailyAt('15:00');

Of course, check out the [full documentation](/docs/5.0/artisan#scheduling-artisan-commands) to learn all about the scheduler!

### Tinker / Psysh

The `php artisan tinker` command now utilizes [Psysh](https://github.com/bobthecow/psysh) by Justin Hileman, a more robust REPL for PHP. If you liked Boris in Laravel 4, you're going to love Psysh. Even better, it works on Windows! To get started, just try:

	php artisan tinker

### DotEnv

Instead of a variety of confusing, nested environment configuration directories, Laravel 5 now utilizes [DotEnv](https://github.com/vlucas/phpdotenv) by Vance Lucas. This library provides a super simple way to manage your environment configuration, and makes environment detection in Laravel 5 a breeze. For more details, check out the full [configuration documentation](/docs/5.0/configuration#environment-configuration).

### Laravel Elixir

Laravel Elixir, by Jeffrey Way, provides a fluent, expressive interface to compiling and concatenating your assets. If you've ever been intimidated by learning Grunt or Gulp, fear no more. Elixir makes it a cinch to get started using Gulp to compile your Less, Sass, and CoffeeScript. It can even run your tests for you!

For more information on Elixir, check out the [full documentation](/docs/5.0/elixir).

### Laravel Socialite

Laravel Socialite is an optional, Laravel 5.0+ compatible package that provides totally painless authentication with OAuth providers. Currently, Socialite supports Facebook, Twitter, Google, and GitHub. Here's what it looks like:

	public function redirectForAuth()
	{
		return Socialize::with('twitter')->redirect();
	}

	public function getUserFromProvider()
	{
		$user = Socialize::with('twitter')->user();
	}

No more spending hours writing OAuth authentication flows. Get started in minutes! The [full documentation](/docs/5.0/authentication#social-authentication) has all the details.

### Flysystem Integration

Laravel now includes the powerful [Flysystem](https://github.com/thephpleague/flysystem) filesystem abstraction library, providing pain free integration with local, Amazon S3, and Rackspace cloud storage - all with one, unified and elegant API! Storing a file in Amazon S3 is now as simple as:

	Storage::put('file.txt', 'contents');

For more information on the Laravel Flysystem integration, consult the [full documentation](/docs/5.0/filesystem).

### Form Requests

Laravel 5.0 introduces **form requests**, which extend the `Illuminate\Foundation\Http\FormRequest` class. These request objects can be combined with controller method injection to provide a boiler-plate free method of validating user input. Let's dig in and look at a sample `FormRequest`:

	<?php namespace App\Http\Requests;

	class RegisterRequest extends FormRequest {

		public function rules()
		{
			return [
				'email' => 'required|email|unique:users',
				'password' => 'required|confirmed|min:8',
			];
		}

		public function authorize()
		{
			return true;
		}

	}

Once the class has been defined, we can type-hint it on our controller action:

	public function register(RegisterRequest $request)
	{
		var_dump($request->input());
	}

When the Laravel service container identifies that the class it is injecting is a `FormRequest` instance, the request will **automatically be validated**. This means that if your controller action is called, you can safely assume the HTTP request input has been validated according to the rules you specified in your form request class. Even more, if the request is invalid, an HTTP redirect, which you may customize, will automatically be issued, and the error messages will be either flashed to the session or converted to JSON. **Form validation has never been more simple.** For more information on `FormRequest` validation, check out the [documentation](/docs/5.0/validation#form-request-validation).

### Simple Controller Request Validation

The Laravel 5 base controller now includes a `ValidatesRequests` trait. This trait provides a simple `validate` method to validate incoming requests. If `FormRequests` are a little too much for your application, check this out:

	public function createPost(Request $request)
	{
		$this->validate($request, [
			'title' => 'required|max:255',
			'body' => 'required',
		]);
	}

If the validation fails, an exception will be thrown and the proper HTTP response will automatically be sent back to the browser. The validation errors will even be flashed to the session! If the request was an AJAX request, Laravel even takes care of sending a JSON representation of the validation errors back to you.

For more information on this new method, check out [the documentation](/docs/5.0/validation#controller-validation).

### New Generators

To complement the new default application structure, new Artisan generator commands have been added to the framework. See `php artisan list` for more details.

### Configuration Cache

You may now cache all of your configuration in a single file using the `config:cache` command.

### Symfony VarDumper

The popular `dd` helper function, which dumps variable debug information, has been upgraded to use the amazing Symfony VarDumper. This provides color-coded output and even collapsing of arrays. Just try the following in your project:

	dd([1, 2, 3]);

<a name="laravel-4.2"></a>
## Laravel 4.2

The full change list for this release by running the `php artisan changes` command from a 4.2 installation, or by [viewing the change file on Github](https://github.com/laravel/framework/blob/4.2/src/Illuminate/Foundation/changes.json). 이번 릴리스의 전체 변경 사항은 설치 한 4.2 폴더에서 `php artisan changes` 명령을 실행하거나 [Github에서 변경사항 확인](https://github.com/laravel/framework/blob/4.2/src/Illuminate/Foundation/changes.json)할 수 있습니다.  These notes only cover the major enhancements and changes for the release. 다음 내용은 릴리즈의 주요 개선사항과 변경점들을 포함하고 있습니다. 

> **Note 참고:** During the 4.2 release cycle, many small bug fixes and enhancements were incorporated into the various Laravel 4.1 point releases. 4.2 버전이 릴리스되기 전까지 많은 다양한 작은 버그 수정 및 개선사힝이 라라벨 4.1에 포함되어 있습니다. So, be sure to check the change list for Laravel 4.1 as well! 따라서 라라벨 4.1의 변경 사항 역시 주의 깊게 확인하십시오.

### PHP 5.4 Requirement
### PHP 5.4 필요

Laravel 4.2 requires PHP 5.4 or greater. 라라벨 4.2는 PHP 5.4 또는 그 이상 버전을 필요로 합니다. This upgraded PHP requirement allows us to use new PHP features such as traits to provide more expressive interfaces for tools like [Laravel Cashier](/docs/billing). PHP 버전이 업그레이드 됨으로써 trait와 같은 새로운 PHP 기능들을 사용할 수 있게되어 [Laravel Cashier](/docs/billing)와 같은 툴을 위한 더 나은 인터페이스를 제공할 수 있게 되었습니다. PHP 5.4 also brings significant speed and performance improvements over PHP 5.3. 또한, PHP5.4은 PHP5.3보다 의미있는 속도와 퍼포먼스 향상을 가져다 줍니다.

### Laravel Forge
### 라라벨 Forge

Laravel Forge, a new web based application, provides a simple way to create and manage PHP servers on the cloud of your choice, including Linode, DigitalOcean, Rackspace, and Amazon EC2. 라라벨 Forge는 새로운 웹 기반 애플리케이션으로 여러분이 Linode, DigitalOcean, Rackspace, Amazon EC2와 같은 클라우드에서 선택한 PHP 서버를 만들고 관리하는 간단한 방법을 제공합니다. Supporting automated Nginx configuration, SSH key access, Cron job automation, server monitoring via NewRelic & Papertrail, "Push To Deploy", Laravel queue worker configuration, and more, Forge provides the simplest and most affordable way to launch all of your Laravel applications. 자동화된 Nginx 설정, SSH 키 엑세스, 크론 작업 자동화, NewRelic 과 Papertrail을 통한 서버 모니터링, “Push를 이용한 배포”, 라라벨 큐 작업 설정등 Forge 는 여러분의 라라벨 어플리케이션을 손쉽게 구성할 수 있는 가장 적당한 방법입니다. 

The default Laravel 4.2 installation's `app/config/database.php` configuration file is now configured for Forge usage by default, allowing for more convenient deployment of fresh applications onto the platform. 기본적으로 라라벨 4.2를 설치했다면 `app/config/database.php` 파일이 Forge 를 사용하도록 설정되어 있을 것입니다. 이처럼 어떤 플랫폼에 편리하게 라라벨 어플리케이션을 배포 할 수 있도록 해줍니다.

More information about Laravel Forge can be found on the [official Forge website](https://forge.laravel.com). 라라벨 Forge 에 대한 보다 자세한 사항은 [공식 Forge 웹사이트](https://forge.laravel.com)에서 찾으실 수 있습니다. 

### Laravel Homestead
### 라라벨 Homestead

Laravel Homestead is an official Vagrant environment for developing robust Laravel and PHP applications. 라라벨 Homestead는 라라벨 및 PHP 어플리케이션을 배포하기위한 강력한 공식 Vagrant 환경입니다. The vast majority of the boxes' provisioning needs are handled before the box is packaged for distribution, allowing the box to boot extremely quickly. Homestead includes Nginx 1.6, PHP 5.6, MySQL, Postgres, Redis, Memcached, Beanstalk, Node, Gulp, Grunt, & Bower. Homestead includes a simple `Homestead.yaml` configuration file for managing multiple Laravel applications on a single box. 배포를 위해 box를 밧케지하기 전에 광대 한 범위에 걸쳐 필요한 box 준비 작업은 처리 된이기 때문에 매우 빠르게 box를 시작 가능합니다. Homestead은 Nginx1.6, PHP5.6, MySQL, Postgres, Redis, Memcached, Beanstalk, Node, Gulp, Grunt, Bower를 준비하고 있습니다. Homestead 간단한 Homestead.yaml 설정 파일을 포함하고 있으며, 여러 Laravel 응용 프로그램을 하나의 box에서 관리하고 있습니다.

The default Laravel 4.2 installation now includes an `app/config/local/database.php` configuration file that is configured to use the Homestead database out of the box, making Laravel initial installation and configuration more convenient. 설치 한 Laravel4.2에 포함되어 있으며, 처음부터 준비되어있다 Homestead 데이터베이스를 사용하도록 설정되어있는 app / config / local / database.php 설정 파일은 Laravel의 초기 설치 및 구성을보다 편리하고 있습니다.

The official documentation has also been updated to include [Homestead documentation](/docs/homestead). 공식 메뉴얼에도 [Homestead 문서](/docs/homestead) 가 추가되었습니다.

### Laravel Cashier

Laravel Cashier is a simple, expressive library for managing subscription billing with Stripe. 라라벨 캐셔는 Stripe를 사용하여 가입 청구서를 관리하기위한 간단하고 유용한 라이브러리입니다. With the introduction of Laravel 4.2, we are including Cashier documentation along with the main Laravel documentation, though installation of the component itself is still optional. 라라벨 4.2에서 소개되는 캐셔의 문서도 라라벨의 기본 문서에 포함되었지만, 구성 요소의 설치는 아직 선택사항입니다. This release of Cashier brings numerous bug fixes, multi-currency support, and compatibility with the latest Stripe API.  이 릴리스의 캐셔기능은 많은 버그가 수정 되었고, 다양한 국가의 통화를 지원하며 최신 Stripe API와 호환됩니다.

### Daemon Queue Workers

The Artisan `queue:work` command now supports a `--daemon` option to start a worker in "daemon mode", meaning the worker will continue to process jobs without ever re-booting the framework. 이제 아티즌의 `queue:work` 명령은 `--daemon` 옵션을 지원하여 작업을 "데몬 모드"로 시작할 수 있습니다. 즉, 큐 작업이 프레임 워크를 다시 시작하지 않고도 계속 처리된다는 것을 의미합니다. This results in a significant reduction in CPU usage at the cost of a slightly more complex application deployment process. 이를 통해서 약간 더 복잡한 응용 프로그램 배포가 필요하지만 CPU 사용량을 크게 감소시킬 수 있는 이점이 있습니다.

More information about daemon queue workers can be found in the [queue documentation](/docs/queues#daemon-queue-worker). 데몬 큐 작업에 대한 보다 자세한 사항은 [queue 문서](/docs/queues#daemon-queue-worker)를 참고하십시오. 

### Mail API Drivers

Laravel 4.2 introduces new Mailgun and Mandrill API drivers for the `Mail` functions. 라라벨 4.2에서는 `Mail` 기능에 새로운 Mailgun과 Mandrill API 드라이버를 지원합니다. For many applications, this provides a faster and more reliable method of sending e-mails than the SMTP options. 많은 어플리케이션에서 SMTP를 사용하는 대신 보다 더 빠르고 신뢰할만한 메일을 보내는 기능을 제공할 것입니다. The new drivers utilize the Guzzle 4 HTTP library. 이 새로운 드라이버는 Guzzle 4 HTTP 라이브러리를 사용하고 있습니다.

### Soft Deleting Traits
### Soft Deleting Traits

A much cleaner architecture for "soft deletes" and other "global scopes" has been introduced via PHP 5.4 traits. “soft deletes” 와 다른 “global scopes” 에서 보다 깔끔한 아키텍처를 구현하기 위해서 PHP 5.4 traits 를 도입하였습니다. This new architecture allows for the easier construction of similar global traits, and a cleaner separation of concerns within the framework itself. 이 새로운 아키텍처는 유사한 글로벌 특성의 생성자와, 프레임 워크 자체의 관계에 대한 분리를 보다 쉽도록 해줍니다. 

More information on the new `SoftDeletingTrait` may be found in the [Eloquent documentation](/docs/eloquent#soft-deleting). 새로운 `SoftDeletingTrait`에 대한 자세한 사항은 [Eloquent 문서](/docs/eloquent#soft-deleting) 를 참고하십시오.

### Convenient Auth & Remindable Traits
### 편리한 인증 & Remindable  Traits

The default Laravel 4.2 installation now uses simple traits for including the needed properties for the authentication and password reminder user interfaces. 라라벨 4.2에서는 인증과 암호 알림 유저 인터페이스에 필요한 속성이 포함된 간단한 trait를 사용합니다. This provides a much cleaner default `User` model file out of the box. . 이를 통해서 기본 `User` 모델이 별다른 설정 없이도 훨씬 간결해 집니다. 

### "Simple Paginate"
### “간단한 페이징”

A new `simplePaginate` method was added to the query and Eloquent builder which allows for more efficient queries when using simple "Next" and "Previous" links in your pagination view. 쿼리와 Eloquent 빌더에 새로운 `simplePaginate` 메소드가 추가되어 페이지 네이션 뷰에서 단순히 '다음'과 '이전’ 링크만 사용한다면 더 효율적인 쿼리를 할 수있습니다.

### Migration Confirmation
### 마이그레이션 확인과정

In production, destructive migration operations will now ask for confirmation. Commands may be forced to run without any prompts using the `--force` command. 실제 제품 서버에서(production) 마이그레이션 작업을 하기 위해서 확인 과정을 거치게 됩니다. 확인과정없이 강제로 실행하려는 경우 `--force` 옵션을 사용하십시오.



<a name="laravel-4.1"></a>
## Laravel 4.1

### Full Change List
### 전체 변경사항

The full change list for this release by running the `php artisan changes` command from a 4.1 installation, or by [viewing the change file on Github](https://github.com/laravel/framework/blob/4.1/src/Illuminate/Foundation/changes.json). 이번 릴리스의 전체 변경 사항은 설치 한 4.1 폴더에서 `php artisan changes` 명령을 실행하거나 [Github에서 변경사항 확인](https://github.com/laravel/framework/blob/4.1/src/Illuminate/Foundation/changes.json)할 수 있습니다.  These notes only cover the major enhancements and changes for the release. 다음 내용은 릴리즈의 주요 개선사항과 변경점들을 포함하고 있습니다. 

### New SSH Component
### 새로운 SSH 컴포넌트

An entirely new `SSH` component has been introduced with this release. 이번 릴리즈에서 완전히 새로운 `SSH` 컴포넌트가 추가되었습니다.  This feature allows you to easily SSH into remote servers and run commands. 이 기능은 손쉽게 원격 서버에 SSH 접속을 하거나, 명령어를 실행할 수 있도록 해줍니다. To learn more, consult the [SSH component documentation](/docs/ssh). 사용법을 익히기 위해서는 [SSH 컴포넌트 문서](/docs/ssh)를 참고하십시오. 

The new `php artisan tail` command utilizes the new SSH component. 새로운 `php artisan tail` 명령어는 새로운 SSH 컴포넌트를 사용합니다. For more information, consult the `tail` [command documentation](http://laravel.com/docs/ssh#tailing-remote-logs). 보다 자세한 사항은  [명령어 문서](http://laravel.com/docs/ssh#tailing-remote-logs) 의 `tail` 항목을 참고하십시오.

### Boris In Tinker

The `php artisan tinker` command now utilizes the [Boris REPL](https://github.com/d11wtq/boris) if your system supports it. `php artisan tinker` 명령은 시스템이 지원하는 경우 [Boris REPL](https://github.com/d11wtq/boris) 을 활용합니다. The `readline` and `pcntl` PHP extensions must be installed to use this feature. 이 기능을 사용하기 위해서는 반드시 `readline` 과 `pcntl` PHP 익스텐션이 설치되어 있어야 합니다. If you do not have these extensions, the shell from 4.0 will be used. 이 익스텐션들이 설치되어 있지 않다면 4.0 기반의 쉘이 사용됩니다. 

### Eloquent Improvements
### Eloquent 향상

A new `hasManyThrough` relationship has been added to Eloquent. Eloquent에 새로운 `hasManyThrough` 관계가 추가되었습니다. To learn how to use it, consult the [Eloquent documentation](/docs/eloquent#has-many-through). 이에대한 사용법은 [Eloquent 문서](/docs/eloquent#has-many-through) 를 참고하십시오.

A new `whereHas` method has also been introduced to allow [retrieving models based on relationship constraints](/docs/eloquent#querying-relations). 또한 새로운 `whereHas` 메소드가 [릴레이션의 조건에 따라 모델을 얻기](/docs/eloquent#querying-relations) 위해 도입되었습니다.

### Database Read / Write Connections
### 데이터베이스 읽기 / 쓰기 커넥션

Automatic handling of separate read / write connections is now available throughout the database layer, including the query builder and Eloquent. 데이터 베이스 레이어에서 쿼리 빌더와 Eloquent 를 포함하여 자동으로 읽기/ 쓰기 커넥션을 구분하여 처리하도록 지원합니다. For more information, consult [the documentation](/docs/database#read-write-connections). 더 자세한 사항은  [문서](/docs/database#read-write-connections)를 참고하십시오. 

### Queue Priority
### 큐 우선순위

Queue priorities are now supported by passing a comma-delimited list to the `queue:listen` command. `queue:listen` 명령어에 쉼표로 구분 된 목록을 지정하여 큐 처리의 우선 순위가 지원됩니다.

### Failed Queue Job Handling
### 실패한 큐 작업 처리하기

The queue facilities now include automatic handling of failed jobs when using the new `--tries` switch on `queue:listen`. `queue:listen` 에 새로운 `--tries` 스위치를 붙여, 실패한 작업을 자동으로 처리하는 기능이 추가되었습니다. More information on handling failed jobs can be found in the [queue documentation](/docs/queues#failed-jobs). 실패한 작업에 대한 자세한 내용은 [큐 문서](/docs/queues#failed-jobs) 를 참고하십시오.

### Cache Tags
### 캐시 태그

Cache "sections" have been superseded by "tags". 캐시의 “sections”은 “tags”로 대체되었습니다. Cache tags allow you to assign multiple "tags" to a cache item, and flush all items assigned to a single tag. 캐시 태그 기능은 각각 캐시 아이템에 여러개의 “태그”를 붙이거나, 전체 아이템에 할당된 태그를 삭제할 수도 있습니다. More information on using cache tags may be found in the [cache documentation](/docs/cache#cache-tags). 캐시 태그를 사용하는 더 자세한 사항은 [캐시 문서](/docs/cache#cache-tags)를 참고하십시오. 

### Flexible Password Reminders
### 유연한 패스워드 알림

The password reminder engine has been changed to provide greater developer flexibility when validating passwords, flashing status messages to the session, etc. 패스워드 알림 엔진은 패스워드를 검증할 때나, 세션에 상태 메세지를 지울 때 더 많은 개발자 유연성을 제공하도록 변경되었습니다. For more information on using the enhanced password reminder engine, [consult the documentation](/docs/security#password-reminders-and-reset). 패스워드 알림 엔진에 대한 더 자세한 사항은 다음 [문서](/docs/security#password-reminders-and-reset)를 참고하십시오.

### Improved Routing Engine
### 라우팅 엔진 향상

Laravel 4.1 features a totally re-written routing layer. 라라벨 4.1에서는 완전히 새롭게 라우팅 레이어를 재작성했습니다. The API is the same; however, registering routes is a full 100% faster compared to 4.0. API는 동일하지만 라우팅 등록과정이 4.0과 비교하여 100% 빨라졌습니다. The entire engine has been greatly simplified, and the dependency on Symfony Routing has been minimized to the compiling of route expressions. 전체 엔진은 매우 간단해 졌으며, 라우트 해석시의 Synfony 라우팅에 대한 종속성은 최소화 되었습니다.

### Improved Session Engine
### 세션 엔진 향상

With this release, we're also introducing an entirely new session engine. 이 릴리스에서 새로운 세션 엔진도 도입하였습니다. Similar to the routing improvements, the new session layer is leaner and faster. 라우팅에서의 향상과 비슷하게 새로운 세션 레이어는 더 작고 빨라졌습니다. We are no longer using Symfony's (and therefore PHP's) session handling facilities, and are using a custom solution that is simpler and easier to maintain.  더이상 Symfony의 (따라서 PHP의) 세션 처리 기능을 사용하지 않고, 더 간단하고 유지 보수하기 쉬운 고유한 처리 방식을 사용하고 있습니다.

### Doctrine DBAL

If you are using the `renameColumn` function in your migrations, you will need to add the `doctrine/dbal` dependency to your `composer.json` file. This package is no longer included in Laravel by default. 만약 마이그레이션에서 `renameColumn` 함수를  사용하는 경우 `composer.json` 파일에 `doctrine/dbal` 패키지를 추가해야합니다. 이 패키지는 기본적으로 라라벨에 포함되지 있지  않습니다.
