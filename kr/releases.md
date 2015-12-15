# Release Notes
# 릴리즈 노트

- [Support Policy](#support-policy)
- [Laravel 5.1.11](#laravel-5.1.11)
- [Laravel 5.1.4](#laravel-5.1.4)
- [Laravel 5.1](#laravel-5.1)
- [Laravel 5.0](#laravel-5.0)
- [Laravel 4.2](#laravel-4.2)
- [Laravel 4.1](#laravel-4.1)

<a name="support-policy"></a>
## Support Policy
## 지원 정책

For LTS releases, such as Laravel 5.1, bug fixes are provided for 2 years and security fixes are provided for 3 years. These releases provide the longest window of support and maintenance.

라라벨 5.1과 같은 LTS 릴리즈 동안에는 2년간의 버그 픽스와 3년동안의 보안 패치가 지원됩니다. 이러한 릴리즈는 장기간에 걸친 지원과 유지보수를 제공합니다. 

For general releases, bug fixes are provided for 6 months and security fixes are provided for 1 year.

일반적인 릴리즈에서는 버그 픽스는 6개월 보안 패치는 1년동안 제공됩니다.

<a name="laravel-5.1.11"></a>
## Laravel 5.1.11
## 라라벨 5.1.11

Laravel 5.1.11 introduces [authorization](/docs/{{version}}/authorization) support out of the box! Conveniently organize your application's authorization logic using simple callbacks or policy classes, and authorize actions using simple, expressive methods.

라라벨 5.1.11 에서는 처음부터 [authorization](/docs/{{version}}/authorization)이 지원됩니다. 

For more information, please refer to the [authorization documentation](/docs/{{version}}/authorization).

보다 자세한 사항은 [authorization 문서](/docs/{{version}}/authorization)를 참고하십시오.

<a name="laravel-5.1.4"></a>
## Laravel 5.1.4
## 라라벨 5.1.4

Laravel 5.1.4 introduces simple login throttling to the framework. Consult the [authentication documentation](/docs/{{version}}/authentication#authentication-throttling) for more information.

라라벨 5.1.4 에서는 로그인 제한이 프레임워크에 도입되었습니다. 자세한 내용은 [인증 문서](/docs/{{version}}/authentication#authentication-throttling)를 참고하십시오.

<a name="laravel-5.1"></a>
## Laravel 5.1
## 라라벨 5.1

Laravel 5.1 continues the improvements made in Laravel 5.0 by adopting PSR-2 and adding event broadcasting, middleware parameters, Artisan improvements, and more.

라라벨 5.1은 PSR-2를 적용하고 이벤트 브로드캐스팅, 미들웨어 파라미터, 아티즌 개선등 라라벨 5.0으로 부터 계속 개선되었습니다. 

### PHP 5.5.9+
### PHP 5.5.9이상

Since PHP 5.4 will enter "end of life" in September and will no longer receive security updates from the PHP development team, Laravel 5.1 requires PHP 5.5.9 or greater. PHP 5.5.9 allows compatibility with the latest versions of popular PHP libraries such as Guzzle and the AWS SDK.

PHP5.4가 2015년 9월에 "종료"되고, PHP 개발팀에서 보안 업데이트를 받을 수 없게 되므로 라라벨 5.1은 PHP 5.5.9 버전 이상의 환경을 필요로 합니다. PHP 5.5.9는 Guzzle와 AWS SDK 처럼 인기있는 PHP 라이브러리의 최신 버전과 호환됩니다.

### LTS
### LTS

Laravel 5.1 is the first release of Laravel to receive **long term support**. Laravel 5.1 will receive bug fixes for 2 years and security fixes for 3 years. This support window is the largest ever provided for Laravel and provides stability and peace of mind for larger, enterprise clients and customers.

라라벨 5.1 은 첫번째 **장기 지원** 릴리즈 입니다. 라라벨 5.1은 2년간 버그 픽스와 3년의 보안 패치를 지원합니다. 이 지원 기간은 지금까지 제공된 릴리즈 중에서 가장 긴 지원이고, 기업 고객과 그 사용자들에게 안정성과 편안함을 제공할 것입니다. 

### PSR-2
### PSR-2

The [PSR-2 coding style guide](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md) has been adopted as the default style guide for the Laravel framework. Additionally, all generators have been updated to generate PSR-2 compatible syntax.

[PSR-2 코딩 스타일 가이드](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md)가 라라벨 프레임워크의 표준 스타일 가이드로 적용되었습니다. 또한 모든 문법이 PSR-2와 호환되도록 재작성 되어 업데이트되었습니다.

### Documentation
### 문서

Every page of the Laravel documentation has been meticulously reviewed and dramatically improved. All code examples have also been reviewed and expanded to provide more relevance and context.

모든 라라벨 문서의 페이지들이 재검토되고, 크게 향상되었습니다. 모든 코드 예제들 또한 다시 확인하고 더 적절한 문맥에 맞게 수정되었습니다. 

### Event Broadcasting
### 이벤트 브로드캐스팅

In many modern web applications, web sockets are used to implement real-time, live-updating user interfaces. When some data is updated on the server, a message is typically sent over a websocket connection to be handled by the client.

많은 모던 Web 어플리케이션들에서, 실시간 처리와, 라이브 업데이트를 지원하는 사용자 인터페이스를 구현하기 위해서 웹 소켓을 사용하고 있습니다. 서버에서 데이터가 업데이트 되면, 메세지가 웹 소켓 커넥션을 통해서 클라이언트에서 처리되도록 전달됩니다. 

To assist you in building these types of applications, Laravel makes it easy to "broadcast" your events over a websocket connection. Broadcasting your Laravel events allows you to share the same event names between your server-side code and your client-side JavaScript framework.

이러한 유형의 어플리케이션을 구축하는 데 도움이 되도록 라라벨은 이벤트를 웹 소켓 연결 상에 쉽게 "브로드 캐스트"할 수 있도록 했습니다. 라라벨 이벤트를 브로드 캐스트하는 것은  같은 이벤트 이름을 서버 사이드의 코드와 클라이언트 사이드 JavaScript 프레임 워크와 공유 할 수 있게 해줍니다.

To learn more about event broadcasting, check out the [event documentation](/docs/{{version}}/events#broadcasting-events).

이벤트 브로드캐스팅에 대한 더 자세한 사항은, [이벤트 문서](/docs/{{version}}/events#broadcasting-events)를 참고하십시오.

### Middleware Parameters
### 미들웨어 파라미터

Middleware can now receive additional custom parameters. For example, if your application needs to verify that the authenticated user has a given "role" before performing a given action, you could create a `RoleMiddleware` that receives a role name as an additional argument:

미들웨어는 이제, 추가적인 사용자 지정 파라미터를 받을 수 있습니다. 예를 들어 어플리케이션에서 인증된 사용자가 주어진 액션을 처리할 수 있는 "역할"을 가지고 있는지 확인할 필요가 있다면, 추가적인 인자로 롤의 이름을 전달받는 `RoleMiddleware`를 생성할 수 있습니다. 

    <?php

    namespace App\Http\Middleware;

    use Closure;

    class RoleMiddleware
    {
        /**
         * Run the request filter.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  \Closure  $next
         * @param  string  $role
         * @return mixed
         */
        public function handle($request, Closure $next, $role)
        {
            if (! $request->user()->hasRole($role)) {
                // Redirect...
            }

            return $next($request);
        }

    }

Middleware parameters may be specified when defining the route by separating the middleware name and parameters with a `:`. Multiple parameters should be delimited by commas:

미들웨어 파라미터는 라우트를 정의 할 때 지정된 미들웨어 이름과 파라미터를 `:` 로 구분하여 지정합니다. 여러개의 파라미터는 쉼표로 구분합니다.

    Route::put('post/{id}', ['middleware' => 'role:editor', function ($id) {
        //
    }]);

For more information on middleware, check out the [middleware documentation](/docs/{{version}}/middleware).

미들웨어에 대한 보자 자세한 정보는 [미들웨어 문서](/docs/{{version}}/middleware)를 확인하십시오. 

### Testing Overhaul
### 

The built-in testing capabilities of Laravel have been dramatically improved. A variety of new methods provide a fluent, expressive interface for interacting with your application and examining its responses. For example, check out the following test:

    public function testNewUserRegistration()
    {
        $this->visit('/register')
             ->type('Taylor', 'name')
             ->check('terms')
             ->press('Register')
             ->seePageIs('/dashboard');
    }

For more information on testing, check out the [testing documentation](/docs/{{version}}/testing).

### Model Factories

Laravel now ships with an easy way to create stub Eloquent models using [model factories](/docs/{{version}}/testing#model-factories). Model factories allow you to easily define a set of "default" attributes for your Eloquent model, and then generate test model instances for your tests or database seeds. Model factories also take advantage of the powerful [Faker](https://github.com/fzaninotto/Faker) PHP library for generating random attribute data:

    $factory->define(App\User::class, function ($faker) {
        return [
            'name' => $faker->name,
            'email' => $faker->email,
            'password' => str_random(10),
            'remember_token' => str_random(10),
        ];
    });

For more information on model factories, check out [the documentation](/docs/{{version}}/testing#model-factories).

### Artisan Improvements

Artisan commands may now be defined using a simple, route-like "signature", which provides an extremely simple interface for defining command line arguments and options. For example, you may define a simple command and its options like so:

    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'email:send {user} {--force}';

For more information on defining Artisan commands, consult the [Artisan documentation](/docs/{{version}}/artisan).

### Folder Structure

To better express intent, the `app/Commands` directory has been renamed to `app/Jobs`. Additionally, the `app/Handlers` directory has been consolidated into a single `app/Listeners` directory which simply contains event listeners. However, this is not a breaking change and you are not required to update to the new folder structure to use Laravel 5.1.

### Encryption

In previous versions of Laravel, encryption was handled by the `mcrypt` PHP extension. However, beginning in Laravel 5.1, encryption is handled by the `openssl` extension, which is more actively maintained.

<a name="laravel-5.0"></a>
## Laravel 5.0

Laravel 5.0 introduces a fresh application structure to the default Laravel project. This new structure serves as a better foundation for building a robust application in Laravel, as well as embraces new auto-loading standards (PSR-4) throughout the application. First, let's examine some of the major changes:

### New Folder Structure

The old `app/models` directory has been entirely removed. Instead, all of your code lives directly within the `app` folder, and, by default, is organized to the `App` namespace. This default namespace can be quickly changed using the new `app:name` Artisan command.

Controllers, middleware, and requests (a new type of class in Laravel 5.0) are now grouped under the `app/Http` directory, as they are all classes related to the HTTP transport layer of your application. Instead of a single, flat file of route filters, all middleware are now broken into their own class files.

A new `app/Providers` directory replaces the `app/start` files from previous versions of Laravel 4.x. These service providers provide various bootstrapping functions to your application, such as error handling, logging, route loading, and more. Of course, you are free to create additional service providers for your application.

Application language files and views have been moved to the `resources` directory.

### Contracts

All major Laravel components implement interfaces which are located in the `illuminate/contracts` repository. This repository has no external dependencies. Having a convenient, centrally located set of interfaces you may use for decoupling and dependency injection will serve as an easy alternative option to Laravel Facades.

For more information on contracts, consult the [full documentation](/docs/{{version}}/contracts).

### Route Cache

If your application is made up entirely of controller routes, you may utilize the new `route:cache` Artisan command to drastically speed up the registration of your routes. This is primarily useful on applications with 100+ routes and will **drastically** speed up this portion of your application.

### Route Middleware

In addition to Laravel 4 style route "filters", Laravel 5 now supports HTTP middleware, and the included authentication and CSRF "filters" have been converted to middleware. Middleware provides a single, consistent interface to replace all types of filters, allowing you to easily inspect, and even reject, requests before they enter your application.

For more information on middleware, check out [the documentation](/docs/{{version}}/middleware).

### Controller Method Injection

In addition to the existing constructor injection, you may now type-hint dependencies on controller methods. The [service container](/docs/{{version}}/container) will automatically inject the dependencies, even if the route contains other parameters:

    public function createPost(Request $request, PostRepository $posts)
    {
        //
    }

### Authentication Scaffolding

User registration, authentication, and password reset controllers are now included out of the box, as well as simple corresponding views, which are located at `resources/views/auth`. In addition, a "users" table migration has been included with the framework. Including these simple resources allows rapid development of application ideas without bogging down on authentication boilerplate. The authentication views may be accessed on the `auth/login` and `auth/register` routes. The `App\Services\Auth\Registrar` service is responsible for user validation and creation.

### Event Objects

You may now define events as objects instead of simply using strings. For example, check out the following event:

    <?php

    class PodcastWasPurchased
    {
        public $podcast;

        public function __construct(Podcast $podcast)
        {
            $this->podcast = $podcast;
        }
    }

The event may be dispatched like normal:

    Event::fire(new PodcastWasPurchased($podcast));

Of course, your event handler will receive the event object instead of a list of data:

    <?php

    class ReportPodcastPurchase
    {
        public function handle(PodcastWasPurchased $event)
        {
            //
        }
    }

For more information on working with events, check out the [full documentation](/docs/{{version}}/events).

### Commands / Queueing

In addition to the queue job format supported in Laravel 4, Laravel 5 allows you to represent your queued jobs as simple command objects. These commands live in the `app/Commands` directory. Here's a sample command:

    <?php

    class PurchasePodcast extends Command implements SelfHandling, ShouldBeQueued
    {
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

Of course, you may also use commands for tasks that are executed synchronously (are not queued). In fact, using commands is a great way to encapsulate complex tasks your application needs to perform. For more information, check out the [command bus](/docs/{{version}}/bus) documentation.

### Database Queue

A `database` queue driver is now included in Laravel, providing a simple, local queue driver that requires no extra package installation beyond your database software.

### Laravel Scheduler

In the past, developers have generated a Cron entry for each console command they wished to schedule. However, this is a headache. Your console schedule is no longer in source control, and you must SSH into your server to add the Cron entries. Let's make our lives easier. The Laravel command scheduler allows you to fluently and expressively define your command schedule within Laravel itself, and only a single Cron entry is needed on your server.

It looks like this:

    $schedule->command('artisan:command')->dailyAt('15:00');

Of course, check out the [full documentation](/docs/{{version}}/scheduling) to learn all about the scheduler!

### Tinker / Psysh

The `php artisan tinker` command now utilizes [Psysh](https://github.com/bobthecow/psysh) by Justin Hileman, a more robust REPL for PHP. If you liked Boris in Laravel 4, you're going to love Psysh. Even better, it works on Windows! To get started, just try:

    php artisan tinker

### DotEnv

Instead of a variety of confusing, nested environment configuration directories, Laravel 5 now utilizes [DotEnv](https://github.com/vlucas/phpdotenv) by Vance Lucas. This library provides a super simple way to manage your environment configuration, and makes environment detection in Laravel 5 a breeze. For more details, check out the full [configuration documentation](/docs/{{version}}/installation#environment-configuration).

### Laravel Elixir

Laravel Elixir, by Jeffrey Way, provides a fluent, expressive interface to compiling and concatenating your assets. If you've ever been intimidated by learning Grunt or Gulp, fear no more. Elixir makes it a cinch to get started using Gulp to compile your Less, Sass, and CoffeeScript. It can even run your tests for you!

For more information on Elixir, check out the [full documentation](/docs/{{version}}/elixir).

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

No more spending hours writing OAuth authentication flows. Get started in minutes! The [full documentation](/docs/{{version}}/authentication#social-authentication) has all the details.

### Flysystem Integration

Laravel now includes the powerful [Flysystem](https://github.com/thephpleague/flysystem) filesystem abstraction library, providing pain free integration with local, Amazon S3, and Rackspace cloud storage - all with one, unified and elegant API! Storing a file in Amazon S3 is now as simple as:

    Storage::put('file.txt', 'contents');

For more information on the Laravel Flysystem integration, consult the [full documentation](/docs/{{version}}/filesystem).

### Form Requests

Laravel 5.0 introduces **form requests**, which extend the `Illuminate\Foundation\Http\FormRequest` class. These request objects can be combined with controller method injection to provide a boiler-plate free method of validating user input. Let's dig in and look at a sample `FormRequest`:

    <?php

    namespace App\Http\Requests;

    class RegisterRequest extends FormRequest
    {
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

When the Laravel service container identifies that the class it is injecting is a `FormRequest` instance, the request will **automatically be validated**. This means that if your controller action is called, you can safely assume the HTTP request input has been validated according to the rules you specified in your form request class. Even more, if the request is invalid, an HTTP redirect, which you may customize, will automatically be issued, and the error messages will be either flashed to the session or converted to JSON. **Form validation has never been more simple.** For more information on `FormRequest` validation, check out the [documentation](/docs/{{version}}/validation#form-request-validation).

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

For more information on this new method, check out [the documentation](/docs/{{version}}/validation#validation-quickstart).

### New Generators

To complement the new default application structure, new Artisan generator commands have been added to the framework. See `php artisan list` for more details.

### Configuration Cache

You may now cache all of your configuration in a single file using the `config:cache` command.

### Symfony VarDumper

The popular `dd` helper function, which dumps variable debug information, has been upgraded to use the amazing Symfony VarDumper. This provides color-coded output and even collapsing of arrays. Just try the following in your project:

    dd([1, 2, 3]);

<a name="laravel-4.2"></a>
## Laravel 4.2

The full change list for this release by running the `php artisan changes` command from a 4.2 installation, or by [viewing the change file on Github](https://github.com/laravel/framework/blob/4.2/src/Illuminate/Foundation/changes.json). These notes only cover the major enhancements and changes for the release.

> **Note:** During the 4.2 release cycle, many small bug fixes and enhancements were incorporated into the various Laravel 4.1 point releases. So, be sure to check the change list for Laravel 4.1 as well!

### PHP 5.4 Requirement
### PHP 5.4 요구사항

Laravel 4.2 requires PHP 5.4 or greater. This upgraded PHP requirement allows us to use new PHP features such as traits to provide more expressive interfaces for tools like [Laravel Cashier](/docs/billing). PHP 5.4 also brings significant speed and performance improvements over PHP 5.3.

라라벨 4.2는 PHP 5.4 또는 그 이상을 필요로 합니다. 

### Laravel Forge
### 라라벨 Forge

Laravel Forge, a new web based application, provides a simple way to create and manage PHP servers on the cloud of your choice, including Linode, DigitalOcean, Rackspace, and Amazon EC2. Supporting automated Nginx configuration, SSH key access, Cron job automation, server monitoring via NewRelic & Papertrail, "Push To Deploy", Laravel queue worker configuration, and more, Forge provides the simplest and most affordable way to launch all of your Laravel applications.

The default Laravel 4.2 installation's `app/config/database.php` configuration file is now configured for Forge usage by default, allowing for more convenient deployment of fresh applications onto the platform.

More information about Laravel Forge can be found on the [official Forge website](https://forge.laravel.com).

### Laravel Homestead
### 라라벨 홈스테드

Laravel Homestead is an official Vagrant environment for developing robust Laravel and PHP applications. The vast majority of the boxes' provisioning needs are handled before the box is packaged for distribution, allowing the box to boot extremely quickly. Homestead includes Nginx 1.6, PHP 5.6, MySQL, Postgres, Redis, Memcached, Beanstalk, Node, Gulp, Grunt, & Bower. Homestead includes a simple `Homestead.yaml` configuration file for managing multiple Laravel applications on a single box.

The default Laravel 4.2 installation now includes an `app/config/local/database.php` configuration file that is configured to use the Homestead database out of the box, making Laravel initial installation and configuration more convenient.

The official documentation has also been updated to include [Homestead documentation](/docs/homestead).

### Laravel Cashier
### 라라벨 캐셔

Laravel Cashier is a simple, expressive library for managing subscription billing with Stripe. With the introduction of Laravel 4.2, we are including Cashier documentation along with the main Laravel documentation, though installation of the component itself is still optional. This release of Cashier brings numerous bug fixes, multi-currency support, and compatibility with the latest Stripe API.

### Daemon Queue Workers

The Artisan `queue:work` command now supports a `--daemon` option to start a worker in "daemon mode", meaning the worker will continue to process jobs without ever re-booting the framework. This results in a significant reduction in CPU usage at the cost of a slightly more complex application deployment process.

More information about daemon queue workers can be found in the [queue documentation](/docs/queues#daemon-queue-worker).

### Mail API Drivers
### 메일 API 드라이버

Laravel 4.2 introduces new Mailgun and Mandrill API drivers for the `Mail` functions. For many applications, this provides a faster and more reliable method of sending e-mails than the SMTP options. The new drivers utilize the Guzzle 4 HTTP library.

### Soft Deleting Traits
### Soft 삭제 Trait

A much cleaner architecture for "soft deletes" and other "global scopes" has been introduced via PHP 5.4 traits. This new architecture allows for the easier construction of similar global traits, and a cleaner separation of concerns within the framework itself.

More information on the new `SoftDeletingTrait` may be found in the [Eloquent documentation](/docs/eloquent#soft-deleting).

### Convenient Auth & Remindable Traits

The default Laravel 4.2 installation now uses simple traits for including the needed properties for the authentication and password reminder user interfaces. This provides a much cleaner default `User` model file out of the box.

### "Simple Paginate"
### "쉬운 페이징"

A new `simplePaginate` method was added to the query and Eloquent builder which allows for more efficient queries when using simple "Next" and "Previous" links in your pagination view.

### Migration Confirmation
### 마이그레이션 확인

In production, destructive migration operations will now ask for confirmation. Commands may be forced to run without any prompts using the `--force` command.

<a name="laravel-4.1"></a>
## Laravel 4.1
## 라라벨 4.1

### Full Change List
### 전체 변경 목록

The full change list for this release by running the `php artisan changes` command from a 4.1 installation, or by [viewing the change file on Github](https://github.com/laravel/framework/blob/4.1/src/Illuminate/Foundation/changes.json). These notes only cover the major enhancements and changes for the release.

### New SSH Component
### 새로운 SSH 컴포넌트

An entirely new `SSH` component has been introduced with this release. This feature allows you to easily SSH into remote servers and run commands. To learn more, consult the [SSH component documentation](/docs/ssh).

The new `php artisan tail` command utilizes the new SSH component. For more information, consult the `tail` [command documentation](http://laravel.com/docs/ssh#tailing-remote-logs).

### Boris In Tinker

The `php artisan tinker` command now utilizes the [Boris REPL](https://github.com/d11wtq/boris) if your system supports it. The `readline` and `pcntl` PHP extensions must be installed to use this feature. If you do not have these extensions, the shell from 4.0 will be used.

### Eloquent Improvements

A new `hasManyThrough` relationship has been added to Eloquent. To learn how to use it, consult the [Eloquent documentation](/docs/eloquent#has-many-through).

A new `whereHas` method has also been introduced to allow [retrieving models based on relationship constraints](/docs/eloquent#querying-relations).

### Database Read / Write Connections

Automatic handling of separate read / write connections is now available throughout the database layer, including the query builder and Eloquent. For more information, consult [the documentation](/docs/database#read-write-connections).

### Queue Priority

Queue priorities are now supported by passing a comma-delimited list to the `queue:listen` command.

### Failed Queue Job Handling

The queue facilities now include automatic handling of failed jobs when using the new `--tries` switch on `queue:listen`. More information on handling failed jobs can be found in the [queue documentation](/docs/queues#failed-jobs).

### Cache Tags

Cache "sections" have been superseded by "tags". Cache tags allow you to assign multiple "tags" to a cache item, and flush all items assigned to a single tag. More information on using cache tags may be found in the [cache documentation](/docs/cache#cache-tags).

### Flexible Password Reminders
### 보다 유연해진 패스워드 리마인더

The password reminder engine has been changed to provide greater developer flexibility when validating passwords, flashing status messages to the session, etc. For more information on using the enhanced password reminder engine, [consult the documentation](/docs/security#password-reminders-and-reset).

패스워드를 검증하고, 상태 메세지를 세션에서 지우는등, 패스워드 리마인더 엔진이 개발자들이 보다 유연하게 사용할 수 있도록 변경되었습니다. 강화된 패스워드 리마인더 엔진에 대한 자세한 정보는 [문서](/docs/security#password-reminders-and-reset)를 참고하십시오

### Improved Routing Engine
### 라우팅 엔진 개선

Laravel 4.1 features a totally re-written routing layer. The API is the same; however, registering routes is a full 100% faster compared to 4.0. The entire engine has been greatly simplified, and the dependency on Symfony Routing has been minimized to the compiling of route expressions.

라라벨 4.1에서는 라우틍 레이어를 완전히 새로 작성하였습니다. API는 동일합니다; 하지만 라우터를 등록하는 것이 4.0에 비해서 100% 빨라졌습니다. 엔진 전체는 보다 단순해지고, 라우트의 해석시 Symfony Routing에 대한 의존도가 줄어 들었습니다.

### Improved Session Engine
### 세션 엔진 개선

With this release, we're also introducing an entirely new session engine. Similar to the routing improvements, the new session layer is leaner and faster. We are no longer using Symfony's (and therefore PHP's) session handling facilities, and are using a custom solution that is simpler and easier to maintain.

이 릴리즈에서 완전히 새로운 세션 엔진을 소개하였습니다. 라우팅의 개선과 비슷하게 새로운 세션 레이어는 더 작고 빨라졌습니다. 더 이상 Symfony의 (그리고 PHP의) 세션 핸들링 기능을 사용하지 않고, 더 간단하고 유지 보수하기 쉬운 사용자 정의 처리를 사용하고 있습니다. 

### Doctrine DBAL
### 독트린 DBAL

If you are using the `renameColumn` function in your migrations, you will need to add the `doctrine/dbal` dependency to your `composer.json` file. This package is no longer included in Laravel by default.

마이그레이션 작업에서 `renameColumn` 함수를 사용하고 있다면 `composer.json` 파일에 `doctrine/dbal` 의존성을 추가해야할 필요가 있습니다. 이 패키지는 라라벨에서 더이상 기본으로 포함하고 있지 않습니다. 
