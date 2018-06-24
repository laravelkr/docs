# Installation
# 설치하기

- [Install Composer 컴포저 설치하기](#install-composer)
- [Install Laravel 라라벨 설치하기](#install-laravel)
- [Server Requirements 서버 요구사항](#server-requirements)

<a name="install-composer"></a>
## Install Composer
## 컴포저 설치하기

Laravel utilizes [Composer](http://getcomposer.org) to manage its dependencies. So, before using Laravel, you will need to make sure you have Composer installed on your machine.
라라벨은 의존성 관리를 위해 [컴포저](http://getcomposer.org)를 활용합니다. 따라서 라라벨을 사용하기전 여러분들은 라라벨을 설치할 서버(머신)에 Composer-컴포저를 설치할 필요가 있습니다.

<a name="install-laravel"></a>
## Install Laravel
## 라라벨 설치하기

### Via Laravel Installer
### Laravel 인스톨러로 설치하기

First, download the Laravel installer using Composer.
먼저, 라라벨 인스톨러를 컴포저를 사용하여 다운로드 합니다.

	composer global require "laravel/installer=~1.1"

Make sure to place the `~/.composer/vendor/bin` directory in your PATH so the `laravel` executable can be located by your system.
`laravel`이 여러분의 시스템에서 실행 가능하도록 `~/.composer/vendor/bin` 폴더를 여러분의 경로(PATH)에 추가 해야합니다. 

Once installed, the simple `laravel new` command will create a fresh Laravel installation in the directory you specify. For instance, `laravel new blog` would create a directory named `blog` containing a fresh Laravel installation with all dependencies installed. This method of installation is much faster than installing via Composer:
라라벨 인스톨러가 설치 된 후, 간단하게 `laravel new` 명령어를 입력하면 여러분이 입력한 폴더안에 깨끗한(fresh) 상태의 라라벨 파일을 생성할 것입니다. 예를들어 `laravel new blog` 명령어는 `blog`라는 폴더를 생성하며 모든 의존 패키지들의 설치와 함께 깨끗한(fresh) 상태의 라라벨 파일들을 이 폴더에 설치합니다.

	laravel new blog

### Via Composer Create-Project
### Composer Create-Project 통한 설치

You may also install Laravel by issuing the Composer `create-project` command in your terminal:
터미널에서 컴포저의 `create-project` 커멘드를 사용하여 라라벨을 설치할 수도 있습니다

	composer create-project laravel/laravel {directory} "5.0.0" --prefer-dist

Once installed, you should upgrade to the latest packages. First, remove `{directory}/vendor/compiled.php` file then change your current directory to `{directory}` and issue `composer update` command.
설치 후에는 최신의 패키지로 업그레이드 해야 합니다. 먼저 `{directory}/vendor/compiled.php` 파일을 삭제하고 설치된 현재 `{directory}` 에서 `composer update` 명령어를 입력하십시오.


### Scaffolding
### 스캐폴딩

Laravel ships with scaffolding for user registration and authentication. If you would like to remove this scaffolding, use the `fresh` Artisan command:
라라벨은 회원가입과 인증을 위한 scaffolding(임시 가설물, 기본 세팅된 임시 프로그램)이 세팅(ship, 운반)되어 있습니다. 만약 이 스캐폴딩(임시 셋팅파일들)을 삭제하기 위해서는 `fresh` 아티즌 커멘드를 사용할 수 있습니다.

	php artisan fresh

<a name="server-requirements"></a>
## Server Requirements
## 서버 요구사항

The Laravel framework has a few system requirements:
라라벨 프레임워크는 다음과 같은 시스템 요구사항이 있습니다.

- PHP >= 5.4, PHP < 7
- Mcrypt PHP Extension
- OpenSSL PHP Extension
- Mbstring PHP Extension
- Tokenizer PHP Extension

As of PHP 5.5, some OS distributions may require you to manually install the PHP JSON extension. When using Ubuntu, this can be done via `apt-get install php5-json`.
일부 OS에서 PHP 5.5 버전부터 직접 수동으로 PHP JSON extension을 설치를 요구하기도 합니다. 여러분이 Ubuntu를 사용하신다면 `apt-get install php5-json` 명령어로 설치할 수 있습니다.

<a name="configuration"></a>
## Configuration
## 설정하기

The first thing you should do after installing Laravel is set your application key to a random string. 
If you installed Laravel via Composer, this key has probably already been set for you by the `key:generate` command.
라라벨 설치 후 가장 먼저 해야할 것은 여러분의 애플리케이션 키를 랜덤한 문자열로 세팅하는 것입니다. 만약 라라벨이 컴포저를 통해 설치되었다면 이 키는 `key:generate` 커멘드에 의해 자동적으로 세팅되어졌을 것입니다. 

Typically, this string should be 32 characters long. The key can be set in the `.env` environment file. 
일반적으로, 이 키는 32자로 긴 문자열입니다. 이 키는 `.env` 환경설정 파일에서 세팅할 수 있습니다.
**If the application key is not set, your user sessions and other encrypted data will not be secure!**
**만약 애플리케이션 키가 세팅되어있지 않다면, 여러분의 사용자 세션과 그밖의 암호화된 데이터들이 안전하지 않습니다!**

Laravel needs almost no other configuration out of the box. You are free to get started developing! However, you may wish to review the `config/app.php` file and its documentation. It contains several options such as `timezone` and `locale` that you may wish to change according to your application.
라라벨은 앞의 내용 이외의 다른 세팅은 필요 하지 않습니다. 이제 편하게 개발을 시작하면 됩니다! 그러나 가급적 `config/app.php` 파일의 내용을 살펴보길 권장합니다. `timezone`과 `locale`과 같이 여러분의 애플리케이션을 따라 바꾸기를 원하는 몇가지 옵션들을 포함하고 있습니다.

Once Laravel is installed, you should also [configure your local environment](/docs/{{version}}/configuration#environment-configuration).
라라벨이 설치하면 이 문서 [로컬 환경 설정하기](/docs/{{version}}/configuration#environment-configuration)  역시 참고하시기 바랍니다.

> **Note:** You should never have the `app.debug` configuration option set to `true` for a production application.
> **주의**  실제 운영중인 애플리케이션에서는 절대로 설정의 `app.debug` 옵션을 `true`로 설정하지 마시기 바랍니다. 

<a name="permissions"></a>
### Permissions
### 권한

Laravel may require one set of permissions to be configured: folders within `storage` and `vendor` require write access by the web server.

라라벨을 구성하기 위해 일부 폴더의 권한을 요구합니다: 웹서버에서 `sotrage`와 `vendor` 폴더에 대한 쓰기권한 허용하십시오.

<a name="pretty-urls"></a>
## Pretty URLs

### Apache
### 아파치

The framework ships with a `public/.htaccess` file that is used to allow URLs without `index.php`. If you use Apache to serve your Laravel application, be sure to enable the `mod_rewrite` module.
라라벨 프레임워크는 `index.php`없는 URLs를 허용하는 `public/.htaccess` 파일이 함께 설치됩니다. 만약 라라벨 애플리케이션을 구동하기 위해 아파치 서버를 사용한다면, `mod_rewrite` 모듈을 활성화 하도록 하시기 바랍니다.

If the `.htaccess` file that ships with Laravel does not work with your Apache installation, try this one:
라라벨과 함께 설치된 `.htaccess` 파일이 여러분의 아파치에서 동작하지 않는다면 아래의 것을 시도해보시기 바랍니다:

	Options +FollowSymLinks
	RewriteEngine On

	RewriteCond %{REQUEST_FILENAME} !-d
	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteRule ^ index.php [L]

### Nginx

On Nginx, the following directive in your site configuration will allow "pretty" URLs:
Nginx에서 여러분의 서버 설정이 아래의 지시를 따른다면 “pretty” URLs를 사용할 수 있습니다:

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

Of course, when using [Homestead](/docs/{{version}}/homestead), pretty URLs will be configured automatically.
물론 [Homestead](/docs/{{version}}/homestead)를 사용하신다면 pretty URLs 는 자동으로 설정되어 있을 것입니다.
