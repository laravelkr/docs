# Installation
# 설치하기

- [Installation](#installation)
- [설치하기](#installation)
- [Configuration](#configuration)
- [설정하기](#configuration)
    - [Basic Configuration](#basic-configuration)
    - [기본적인 설정](#basic-configuration)
    - [Environment Configuration](#environment-configuration)
    - [구동 환경 설정하기](#environment-configuration)
    - [Configuration Caching](#configuration-caching)
    - [설정 캐싱하기](#configuration-caching)
    - [Accessing Configuration Values](#accessing-configuration-values)
    - [접근 설정 값](#accessing-configuration-values)
    - [Naming Your Application](#naming-your-application)
    - [애플리케이션의 이름 지정](#naming-your-application)
- [Maintenance Mode](#maintenance-mode)
- [점검(공사중) 모드](#maintenance-mode)

<a name="installation"></a>
## Installation
## 설치하기

### Server Requirements
### 서버 요구사항

The Laravel framework has a few system requirements. Of course, all of these requirements are satisfied by the [Laravel Homestead](/docs/{{version}}/homestead) virtual machine:

라라벨은 몇가지 시스템 요구사항을 필요로 합니다. 물론, [라라벨 홈스테드](/docs/{{version}}/homestead)은 모든 요구사항을 만족합니다. 

- PHP >= 5.5.9
- OpenSSL PHP Extension
- PDO PHP Extension
- Mbstring PHP Extension
- Tokenizer PHP Extension

<a name="install-laravel"></a>
### Installing Laravel
### 라라벨 설치하기

Laravel utilizes [Composer](http://getcomposer.org) to manage its dependencies. So, before using Laravel, make sure you have Composer installed on your machine.

라라벨은 의존성 관리를 위해 [컴포저](http://getcomposer.org)를 활용합니다. 라라벨을 사용하기 전에, 여러분들은 라라벨을 설치할 서버(머신)에 Composer를 설치해야 합니다.

#### Via Laravel Installer
#### 라라벨 인스톨러로 사용하기

First, download the Laravel installer using Composer:
먼저, 라라벨 인스톨러를 컴포저를 사용하여 다운로드 합니다.

    composer global require "laravel/installer"

Make sure to place the `~/.composer/vendor/bin` directory in your PATH so the `laravel` executable can be located by your system.
`laravel`이 여러분의 시스템에서 실행 가능하도록 `~/.composer/vendor/bin` 폴더를 여러분의 경로(PATH)에 추가 해야합니다. 

Once installed, the simple `laravel new` command will create a fresh Laravel installation in the directory you specify. For instance, `laravel new blog` will create a directory named `blog` containing a fresh Laravel installation with all of Laravel's dependencies already installed. This method of installation is much faster than installing via Composer:

라라벨 인스톨러가 설치 된 후, 간단하게 `laravel new` 명령어를 입력하면 여러분이 입력한 폴더안에 깨끗한(fresh) 상태의 라라벨 파일을 생성할 것입니다. 예를들어 `laravel new blog` 명령어는 `blog`라는 폴더를 생성 할것이며 라라벨이 필요로 하는 모든 의존 패키지들의 설치와 함께 깨끗한(fresh) 상태의 라라벨 파일들을 이 폴더에 설치합니다.

    laravel new blog

#### Via Composer Create-Project
### Composer Create-Project 통한 설치

Alternatively, you may also install Laravel by issuing the Composer `create-project` command in your terminal:

그렇지 않으면, 터미널에서 컴포저의 `create-project` 커멘드를 사용하여 라라벨을 설치할 수도 있습니다

    composer create-project laravel/laravel blog "5.1.*"

<a name="configuration"></a>
## Configuration
## 설정하기

<a name="basic-configuration"></a>
### Basic Configuration
### 기본적인 설정하기

All of the configuration files for the Laravel framework are stored in the `config` directory. Each option is documented, so feel free to look through the files and get familiar with the options available to you.

라라벨 프레임워크의 모든 설정 파일들은 `config` 디렉토리에 위치합니다. 각각의 옵션들은 문서가 작성되어 있으며, 사용 가능한 옵션들을 이해하기 위해서 전체 문서들을 둘러보는 것이 좋습니다. 

#### Directory Permissions
#### 디렉토리 권한 설정

After installing Laravel, you may need to configure some permissions. Directories within the `storage` and the `bootstrap/cache` directories should be writable by your web server. If you are using the [Homestead](/docs/{{version}}/homestead) virtual machine, these permissions should already be set.

라라벨을 설치한 뒤에, 몇몇 권한을 설정해야될 필요가 있습니다. `storage` 와 `bootstrap/cache` 디렉토리 안에 들어 있는 디렉토리들은 모두 웹 서버에 의해서 쓰기가 가능해야 합니다. [홈스테드](/docs/{{version}}/homestead)를 사용중이라면 이미 권한이 설정되어 있을 것입니다. 

#### Application Key
#### 애플리케이션 키 설정

The next thing you should do after installing Laravel is set your application key to a random string. If you installed Laravel via Composer or the Laravel installer, this key has already been set for you by the `key:generate` command. Typically, this string should be 32 characters long. The key can be set in the `.env` environment file. If you have not renamed the `.env.example` file to `.env`, you should do that now. **If the application key is not set, your user sessions and other encrypted data will not be secure!**

라라벨 설치 후 해야할 일은 여러분의 애플리케이션 키를 랜덤한 문자열로 세팅하는 것입니다. 만약 라라벨이 Composer 를 통해서 설치되었다면 키는 `key:generate` 명령어가 실행되어 이미 설정되어 있을 것입니다. 일반적으로, 이 키는 32자리의 문자열입니다. 이 키는 `.env` 환경설정 파일에서 세팅할 수 있습니다. 아직 `.env.example` 파일을 `.env` 로 변경하지 않았다면 지금 변경합니다. **만약 애플리케이션 키가 세팅되어있지 않다면, 여러분의 사용자 세션과 그밖의 암호화된 데이터들이 안전하지 않습니다!**

#### Additional Configuration
#### 추가적인 설정 

Laravel needs almost no other configuration out of the box. You are free to get started developing! However, you may wish to review the `config/app.php` file and its documentation. It contains several options such as `timezone` and `locale` that you may wish to change according to your application.

라라벨은 앞의 내용 이외의 다른 세팅은 거의 필요 하지 않습니다. 이제 편하게 개발을 시작하면 됩니다! 그러나 가급적 `config/app.php` 파일의 내용을 살펴보길 권장합니다. `timezone`과 `locale`과 같이 여러분의 애플리케이션을 따라 바꾸기를 원하는 몇가지 옵션들을 포함하고 있습니다.

You may also want to configure a few additional components of Laravel, such as:

다음과 같은 라라벨의 컴포넌트들에 대한 몇가지 설정들을 해둘 수도 있습니다. 

- [Cache](/docs/{{version}}/cache#configuration)
- [Database](/docs/{{version}}/database#configuration)
- [Session](/docs/{{version}}/session#configuration)

Once Laravel is installed, you should also [configure your local environment](/docs/{{version}}/installation#environment-configuration).

라라벨이 설치하면 이 문서 [로컬 환경 설정하기](/docs/{{version}}/configuration#environment-configuration)  역시 참고하시기 바랍니다.

<a name="pretty-urls"></a>
#### Pretty URLs
#### Pretty URLs

**Apache**
**Apache**

The framework ships with a `public/.htaccess` file that is used to allow URLs without `index.php`. If you use Apache to serve your Laravel application, be sure to enable the `mod_rewrite` module.

라라벨 프레임워크는 `index.php`없는 URLs를 허용하는 `public/.htaccess` 파일이 함께 설치됩니다. 만약 라라벨 애플리케이션을 구동하기 위해 아파치 서버를 사용한다면, `mod_rewrite` 모듈을 활성화 하도록 하시기 바랍니다.

If the `.htaccess` file that ships with Laravel does not work with your Apache installation, try this one:

라라벨과 함께 설치된 `.htaccess` 파일이 여러분의 아파치에서 동작하지 않는다면 아래의 것을 시도해보시기 바랍니다:

    Options +FollowSymLinks
    RewriteEngine On

    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^ index.php [L]

**Nginx**

On Nginx, the following directive in your site configuration will allow "pretty" URLs:

Nginx에서 여러분의 서버 설정이 아래의 지시를 따른다면 “pretty” URLs를 사용할 수 있습니다:

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

Of course, when using [Homestead](/docs/{{version}}/homestead), pretty URLs will be configured automatically.

물론 [Homestead](/docs/{{version}}/homestead)를 사용하신다면 pretty URLs 는 자동으로 설정되어 있을 것입니다.

<a name="environment-configuration"></a>
### Environment Configuration
### 구동환경 설정

It is often helpful to have different configuration values based on the environment the application is running in. For example, you may wish to use a different cache driver locally than you do on your production server. It's easy using environment based configuration. 

프로그램이 실행되는  구동환경에 따라 다른 설정값을 사용할 수 있으면 많은 이점이 있습니다. 예를 들어 실제 제품 서버와 로컬 개발 서버에 각각 다른 캐시 드라이버를 사용하려고 하는 경우가 그렇습니다. 이러한 경우 구동환경 설정을 통해서 손쉽게 해결 할 수 있습니다.

To make this a cinch, Laravel utilizes the [DotEnv](https://github.com/vlucas/phpdotenv) PHP library by Vance Lucas. In a fresh Laravel installation, the root directory of your application will contain a `.env.example` file. If you install Laravel via Composer, this file will automatically be renamed to `.env`. Otherwise, you should rename the file manually.

보다 확실하게 하기 위해서 라라벨은 Vance Lucas가 만든 PHP 라이브러리 [DotEnv](https://github.com/vlucas/phpdotenv)을 이용합니다. 새롭게 라라벨을 인스톨 한경우에 애플리케이션의 루트 디렉토리에 `.env.example` 파일이 포함되어 있습니다. 컴포저를 통해서 사용한 경우에는 이 파일이 자동으로 `.env`파일로 변경됩니다. 컴포저를 사용하지 않는다면 직접 파일을 변경하십시오. 

All of the variables listed in this file will be loaded into the `$_ENV` PHP super-global when your application receives a request. You may use the `env` helper to retrieve values from these variables. In fact, if you review the Laravel configuration files, you will notice several of the options already using this helper!

이 파일의 목록에 있는 모든 변수들은 애플리케이션이 요청을 받으면 `$_ENV` PHP 슈퍼 전역변수에 로드됩니다. `env` 헬퍼 함수를 통해서 이러한 변수값들을 검색 할수 있습니다. 만약 여러분이 라라벨 설정 파일들을 살펴보았다면 이미 여러 옵션 설정에 헬퍼 함수가 사용되었다는 것을 알 수 있을 것입니다!

Feel free to modify your environment variables as needed for your own local server, as well as your production environment. However, your `.env` file should not be committed to your application's source control, since each developer / server using your application could require a different environment configuration.

실제 제품 환경(production)과 마찬가지로 로컬 서버의 필요에 맞게 환경 변수를 변경하십시오. 하지만 여러분의 개발자 / 서버들이 각기 다르 른 개발환경 구성을 필요로 할 수도 있기 때문에, `.env` 파일을 애플리케이션의 소스에 커밋하지 마십시오. 

If you are developing with a team, you may wish to continue including a `.env.example` file with your application. By putting place-holder values in the example configuration file, other developers on your team can clearly see which environment variables are needed to run your application.

팀으로 개발을 하는 경우라면 `.env.example` 파일을 애플리케이션에 포함할 수 있습니다. example 설정 파일에 적절한 기본값을 넣어두면 다른 개발자들이 응용 프로그램을 실행하는 데 어떤 환경 변수 설정이 필요한지 명확하게 이해할 수 있습니다.

If you are developing with a team, you may wish to continue including a `.env.example` file with your application. By putting place-holder values in the example configuration file, other developers on your team can clearly see which environment variables are needed to run your application. 

#### Accessing The Current Application Environment
#### 현재의 애플리케이션 구동환경에 엑세스

The current application environment is determined via the `APP_ENV` variable from your `.env` file. You may access this value via the `environment` method on the `App` [facade](/docs/{{version}}/facades):

현재의 구동환경 값은 `.env` 파일의 `APP_ENV` 값에 의해서 결정됩니다. 또한 `App` [파사드](/docs/{{version}}/facades)의 `environment` 메소드를 통해서도 확인할 수 있습니다. 

    $environment = App::environment();

You may also pass arguments to the `environment` method to check if the environment matches a given value. You may even pass multiple values if necessary:

또는 `environment` 메소드에 매개변수를 넘겨주어 해당 구동환경을 확인하는 형태로 사용할 수도 있습니다. 필요하다면 여러개의 값을 넘겨줄 수도 있습니다. 

    if (App::environment('local')) {
        // The environment is local
    }

    if (App::environment('local', 'staging')) {
        // The environment is either local OR staging...
    }

An application instance may also be accessed via the `app` helper method:

또한 애플리케이션의 인스턴스는 `app` 헬퍼 함수를 통해서도 엑세스 할 수 있습니다:

    $environment = app()->environment();

<a name="configuration-caching"></a>
### Configuration Caching
### 설정 캐싱하기

To give your application a speed boost, you should cache all of your configuration files into a single file using the `config:cache` Artisan command. This will combine all of the configuration options for your application into a single file which can be loaded quickly by the framework.

애플리케이션을 보다 빠르게 구동하기 위해서는, 여러분은 `config:cache` 아티즌 명령어를 사용하여 모든 설정 파일들을 하나의 파일로 캐싱해야 합니다. 이렇게 하면 애플리케이션의 모든 설정 옵션들이 하나의 파일로 합쳐져 프레임워크 구동이 훨씬 빨라질 것입니다. 

You should typically run the `php artisan config:cache` command as part of your production deployment routine. The command should not be run during local development as configuration options will frequently need to be changed during the course of your application's development.

일반적으로 `php artisan config:cache` 명령어는 실 서버 환경에서 실행합니다. 애플리케이션 개발중에는 이 설정 옵션들이 빈번하게 변경되기 때문에, 이 명령어는 개발 환경에서는 실행시키지 말아야 합니다. 

<a name="accessing-configuration-values"></a>
### Accessing Configuration Values
### 설정 값 확인하기

You may easily access your configuration values using the global `config` helper function. The configuration values may be accessed using "dot" syntax, which includes the name of the file and option you wish to access. A default value may also be specified and will be returned if the configuration option does not exist:

`config` 헬퍼 함수를 사용하면 손쉽게 설정값을 가져올 수 있습니다. 설정 값은 파일 이름과 옵션 값에 해당하는 "점" 으로 구분되는 문법을 통해서 엑세스 할 수 있습니다. 만약 설정 옵션값이 존재하지 않는다면 지정된 기본값을 반환합니다.

    $value = config('app.timezone');

To set configuration values at runtime, pass an array to the `config` helper:

애플리케이션이 구동중에 설정 값을 지정하려면 `config` 헬퍼 함수에 배열을 전달하면 됩니다. 

    config(['app.timezone' => 'America/Chicago']);

<a name="naming-your-application"></a>
### Naming Your Application
### 애플리케이션에 이름 지정하기

After installing Laravel, you may wish to "name" your application. By default, the `app` directory is namespaced under `App`, and autoloaded by Composer using the [PSR-4 autoloading standard](http://www.php-fig.org/psr/psr-4/). However, you may change the namespace to match the name of your application, which you can easily do via the `app:name` Artisan command.

라라벨을 설치한 뒤에 애플리케이션의 이름을 지정할 수도 있습니다. 기본적으로 `app`디렉토리는 컴포저의 [PSR-4 autoloading standard](http://www.php-fig.org/psr/psr-4/)로 오토로딩되는 `App`이라는 네임스페이스를 가집니다. 하지만 이것을 애플리케이션을 위한 고유한 네임스페이스로 변경하길 원할수도 있기 때문에 이러한 경우 `app:name` 이라는 아티즌 명령어를 통해서 손쉽게 변경할 수 있습니다. 

For example, if your application is named "Horsefly", you could run the following command from the root of your installation:

예를 들어 애플리케이션을 "Horsefly"라고 이름을 지정한다고 하면 설치한 루트 디렉토리에서 다음과 같이 명령어를 실행하면 됩니다. 

    php artisan app:name Horsefly

Renaming your application is entirely optional, and you are free to keep the `App` namespace if you wish.

애플리케이션의 이름을 재지정하는 것은 선택사항입니다. `App`이라는 네임스페이스를 그냥 사용해도 무방합니다. 

<a name="maintenance-mode"></a>
## Maintenance Mode
## 점검 모드(유지보수 모드 - 공사중)

When your application is in maintenance mode, a custom view will be displayed for all requests into your application. This makes it easy to "disable" your application while it is updating or when you are performing maintenance. A maintenance mode check is included in the default middleware stack for your application. If the application is in maintenance mode, an `HttpException` will be thrown with a status code of 503.

애플리케이션이 점검 모드일 경우 애플리케이션에서 지정한 모든 라우팅이 특정한 화면으로 보여지게 됩니다. 따라서 애플리케이션을 업데이트 하거나 튜닝하는 중에 손쉽게 "사용 정지"시킬 수 있습니다. 점검 모드를 확인하는 부분은 애플리케이션의 미들웨어 스택에 기본적으로 포함되어 있습니다. 만약 애플리케이션이 점검 모드에 있다면 503 상태 코드와 함께 `HttpException`이 발생합니다. 

To enable maintenance mode, simply execute the `down` Artisan command:

점검 모드를 활성화 하려면 아티즌 명령어 `down`을 실행시키면 됩니다:

    php artisan down

To disable maintenance mode, use the `up` command:

점검 모드를 해제하려면 아티즌 명령어 `up`을 실행시키면 됩니다:

    php artisan up

### Maintenance Mode Response Template
### 점검모드에서 응답 화면 템플릿

The default template for maintenance mode responses is located in `resources/views/errors/503.blade.php`.

점검모드의 응답페이지는 `resources/views/errors/503.blade.php` 파일에 위치하고 있습니다. 

### Maintenance Mode & Queues
### 점검 모드와 큐 

While your application is in maintenance mode, no [queued jobs](/docs/{{version}}/queues) will be handled. The jobs will continue to be handled as normal once the application is out of maintenance mode.

애플리케이션이 점검 모드인 동안에는 [큐 작업](/docs/{{version}}/queues)은 동작하지 않습니다. 큐 작업들은 점검 모드가 해제된 애플리케이션이 일반 모드로 전환된 시점에서 계속 처리됩니다. 
