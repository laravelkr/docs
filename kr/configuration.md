# Configuration 설정

- [Introduction 소개](#introduction)
- [After Installation 설치 후 할일](#after-installation)
- [Accessing Configuration Values 설정값 조회하기](#accessing-configuration-values)
- [Environment Configuration 구동환경 설정](#environment-configuration)
- [Configuration Caching 설정값 캐시](#configuration-caching)
- [Maintenance Mode 점검(공사중) 모드](#maintenance-mode)
- [Pretty URLs 더 나은 URL들](#pretty-urls)

<a name="introduction"></a>
## Introduction 소개

All of the configuration files for the Laravel framework are stored in the `config` directory. 라라벨의 모든 설정 파일은 `config` 디렉토리에 저장됩니다. Each option is documented, so feel free to look through the files and get familiar with the options available to you. 모든 파일에는 개별 옵션에 대한 설명이 들어 있으니 해당 옵션에 대해 이해하는데 도움을 받을 수 있습니다. 

<a name="after-installation"></a>
## After Installation 설치후 할일

### Naming Your Application 어플리케이션 네임 지정하기

After installing Laravel, you may wish to "name" your application. 라라벨을 설치한 뒤에 어플리케이션의 이름을 지정하길 원할 수도 있습니다. By default, the `app` directory is namespaced under `App`, and autoloaded by Composer using the [PSR-4 autoloading standard](http://www.php-fig.org/psr/psr-4/). 기본적으로 `app`디렉토리는 컴포저의 [PSR-4 autoloading standard](http://www.php-fig.org/psr/psr-4/)로 오토로딩되는 `App`이라는 네임스페이스를 가집니다. However, you may change the namespace to match the name of your application, which you can easily do via the `app:name` Artisan command. 하지만 이것을 어플리케이션을 위한 고유한 네임스페이스로 변경하길 원할수도 있기 때문에 이러한 경우 `app:name` 이라는 아티즌 명령어를 통해서 손쉽게 변경할 수 있습니다. 

For example, if your application is named "Horsefly", you could run the following command from the root of your installation: 예를 들어 어플리케이션을 "Horsefly"라고 이름을 지정한다고 하면 설치한 루트 디렉토리에서 다음과 같이 명령어를 실행하면 됩니다. 

	php artisan app:name Horsefly

Renaming your application is entirely optional, and you are free to keep the `App` namespace if you wish. 어플리케이션의 이름을 재지정하는 것은 선택사항입니다. `App`이라는 네임스페이스를 그냥 사용해도 무방합니다. 

### Other Configuration 다른 설정들

Laravel needs very little configuration out of the box. 라라벨은 구동에 필요한 설정이 그리 많지는 않습니다. You are free to get started developing! 바로 개발을 진행해도 됩니다! However, you may wish to review the `config/app.php` file and its documentation. 하지만 `config/app.php` 파일과 그 주석들을 읽어두는 것이 좋습니다. It contains several options such as `timezone` and `locale` that you may wish to change according to your location. 거기에는 여러분의 지역에 따라 변경하고 싶어할만한 `timezone`과 `locale`과 같은 옵션들을 포함하고 있습니다. 

Once Laravel is installed, you should also [configure your local environment](/docs/5.0/configuration#environment-configuration). 라라벨이 설치된 이후에 여러분의 [로컬 개발환경을 설정](/docs/5.0/configuration#environment-configuration) 하기를 바랍니다. 

> **Note 주의:** You should never have the `app.debug` configuration option set to `true` for a production application. 실서버(production)에서 구동하는 경우에는 `app.debug` 설정을 `true`로 설정하지 마십시오. 

<a name="permissions"></a>
### Permissions 권한

Laravel may require one set of permissions to be configured: folders within `storage` require write access by the web server. 라라벨은 한가지의 권한설정을 필요로 합니다: `storage` 폴더는 웹서버가 접근이 가능해야 합니다. 

<a name="accessing-configuration-values"></a>
## Accessing Configuration Values
## 환경설정값 가져오기

You may easily access your configuration values using the `Config` facade:
`Config` 파사드를 사용하면 손쉽게 설정값을 가져올 수 있습니다. 

	$value = Config::get('app.timezone');

	Config::set('app.timezone', 'America/Chicago');

You may also use the `config` helper function:
또한 `config` 헬퍼 함수를 사용할 수도 있습니다. 

	$value = config('app.timezone');

<a name="environment-configuration"></a>
## Environment Configuration 구동환경 설정

It is often helpful to have different configuration values based on the environment the application is running in. 프로그램이 실행되는  구동환경에 따라 다른 설정값을 사용할 수 있으면 많은 이점이 있습니다. For example, you may wish to use a different cache driver locally than you do on your production server. 예를 들어 실제 제품 서버와 로컬 개발 서버에 각각 다른 캐시 드라이버를 사용하려고 하는 경우가 그렇습니다. It's easy using environment based configuration. 이러한 경우 구동환경 설정을 통해서 손쉽게 해결 할 수 있습니다.

To make this a cinch, Laravel utilizes the [DotEnv](https://github.com/vlucas/phpdotenv) PHP library by Vance Lucas. In a fresh Laravel installation, the root directory of your application will contain a `.env.example` file. If you install Laravel via Composer, this file will automatically be renamed to `.env`. Otherwise, you should rename the file manually.

All of the variables listed in this file will be loaded into the `$_ENV` PHP super-global when your application receives a request. You may use the `env` helper to retrieve values from these variables. In fact, if you review the Laravel configuration files, you will notice several of the options already using this helper!

Feel free to modify your environment variables as needed for your own local server, as well as your production environment. However, your `.env` file should not be committed to your application's source control, since each developer / server using your application could require a different environment configuration.

If you are developing with a team, you may wish to continue including a `.env.example` file with your application. By putting place-holder values in the example configuration file, other developers on your team can clearly see which environment variables are needed to run your application.

#### Accessing The Current Application Environment

You may access the current application environment via the `environment` method on the `Application` instance:

	$environment = $app->environment();

You may also pass arguments to the `environment` method to check if the environment matches a given value:

	if ($app->environment('local'))
	{
		// The environment is local
	}

	if ($app->environment('local', 'staging'))
	{
		// The environment is either local OR staging...
	}

To obtain an instance of the application, resolve the `Illuminate\Contracts\Foundation\Application` contract via the [service container](/docs/5.0/container). Of course, if you are within a [service provider](/docs/5.0/providers), the application instance is available via the `$this->app` instance variable.

An application instance may also be accessed via the `app` helper or the `App` facade:

	$environment = app()->environment();

	$environment = App::environment();

<a name="configuration-caching"></a>
## Configuration Caching

To give your application a little speed boost, you may cache all of your configuration files into a single file using the `config:cache` Artisan command. This will combine all of the configuration options for your application into a single file which can be loaded quickly by the framework.

You should typically run the `config:cache` command as part of your deployment routine.

<a name="maintenance-mode"></a>
## Maintenance Mode

When your application is in maintenance mode, a custom view will be displayed for all requests into your application. This makes it easy to "disable" your application while it is updating or when you are performing maintenance. A maintenance mode check is included in the default middleware stack for your application. If the application is in maintenance mode, an `HttpException` will be thrown with a status code of 503.

To enable maintenance mode, simply execute the `down` Artisan command:

	php artisan down

To disable maintenance mode, use the `up` command:

	php artisan up

### Maintenance Mode Response Template

The default template for maintenance mode responses is located in `resources/views/errors/503.blade.php`.

### Maintenance Mode & Queues

While your application is in maintenance mode, no [queued jobs](/docs/5.0/queues) will be handled. The jobs will continue to be handled as normal once the application is out of maintenance mode.

<a name="pretty-urls"></a>
## Pretty URLs

### Apache

The framework ships with a `public/.htaccess` file that is used to allow URLs without `index.php`. If you use Apache to serve your Laravel application, be sure to enable the `mod_rewrite` module.

If the `.htaccess` file that ships with Laravel does not work with your Apache installation, try this one:

	Options +FollowSymLinks
	RewriteEngine On

	RewriteCond %{REQUEST_FILENAME} !-d
	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteRule ^ index.php [L]

### Nginx

On Nginx, the following directive in your site configuration will allow "pretty" URLs:

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

Of course, when using [Homestead](/docs/5.0/homestead), pretty URLs will be configured automatically.
