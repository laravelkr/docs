# Installation
# 설치하기

- [Installation](#installation)
- [설치하기](#installation)
    - [Server Requirements](#server-requirements)
    - [서버 요구사항](#server-requirements)
    - [Installing Laravel](#installing-laravel)
    - [라라벨 설치하기](#installing-laravel)
    - [Configuration](#configuration)
    - [설정하기](#configuration)
- [Web Server Configuration](#web-server-configuration)
- [웹 서버 설정](#web-server-configuration)
    - [Pretty URLs](#pretty-urls)
    - [Pretty URLs](#pretty-urls)

<a name="installation"></a>
## Installation
## 설치하기

<a name="server-requirements"></a>
### Server Requirements
### 서버 요구사항

The Laravel framework has a few system requirements. All of these requirements are satisfied by the [Laravel Homestead](/docs/{{version}}/homestead) virtual machine, so it's highly recommended that you use Homestead as your local Laravel development environment.

라라벨은 몇가지 시스템 요구사항을 필요로 합니다. [라라벨 홈스테드](/docs/{{version}}/homestead)는 모든 요구사항을 만족합니다. 따라서 여러분의 로컬 라라벨 개발 환경에서 홈스테드를 사용할 것을 강력 권장합니다.

However, if you are not using Homestead, you will need to make sure your server meets the following requirements:

하지만, 홈스테드를 사용하지 않는 경우, 서버에서 다음의 요구사항을 만족하는지 확인해야합니다.


- PHP >= 7.2.0
- BCMath PHP Extension
- Ctype PHP Extension
- JSON PHP Extension
- Mbstring PHP Extension
- OpenSSL PHP Extension
- PDO PHP Extension
- Tokenizer PHP Extension
- XML PHP Extension


<a name="installing-laravel"></a>
### Installing Laravel
### 라라벨 설치하기

Laravel utilizes [Composer](https://getcomposer.org) to manage its dependencies. So, before using Laravel, make sure you have Composer installed on your machine.

라라벨은 의존성 관리를 위해 [컴포저](https://getcomposer.org)를 활용합니다. 라라벨을 사용하기 전에, 여러분들은 라라벨을 설치할 서버(머신)에 Composer를 설치해야 합니다.

#### Via Laravel Installer
#### 라라벨 인스톨러로 사용하기

First, download the Laravel installer using Composer:

먼저, 라라벨 인스톨러를 컴포저를 사용하여 다운로드 합니다.

    composer global require laravel/installer

Make sure to place Composer's system-wide vendor bin directory in your `$PATH` so the laravel executable can be located by your system. This directory exists in different locations based on your operating system; however, some common locations include:

laravel 실행 파일을 시스템에서 찾을 수 있도록 컴포저의 시스템 vendor bin 디렉토리가 `$PATH`에 추가되었는지 확인하십시오. 이 디렉토리는 운영체제 별로 다른 위치에 저장됩니다만, 일반적으로는 다음과 같습니다.


- macOS and GNU / Linux Distributions: `$HOME/.composer/vendor/bin`
- Windows: `%USERPROFILE%\AppData\Roaming\Composer\vendor\bin`


Once installed, the `laravel new` command will create a fresh Laravel installation in the directory you specify. For instance, `laravel new blog` will create a directory named `blog` containing a fresh Laravel installation with all of Laravel's dependencies already installed:

라라벨 인스톨러가 설치 된 후, `laravel new` 명령어를 입력하면 여러분이 입력한 폴더안에 깨끗한(fresh) 상태의 라라벨 파일을 생성할 것입니다. 예를들어 `laravel new blog` 명령어는 `blog`라는 폴더를 생성 할것이며 라라벨이 필요로 하는 모든 의존 패키지들의 설치와 함께 깨끗한(fresh) 상태의 라라벨 파일들을 이 폴더에 설치합니다.

    laravel new blog

#### Via Composer Create-Project
#### Composer Create-Project 통한 설치

Alternatively, you may also install Laravel by issuing the Composer `create-project` command in your terminal:

그렇지 않으면, 터미널에서 컴포저의 `create-project` 커멘드를 사용하여 라라벨을 설치할 수도 있습니다

    composer create-project --prefer-dist laravel/laravel blog

#### Local Development Server
#### 로컬 개발 서버

If you have PHP installed locally and you would like to use PHP's built-in development server to serve your application, you may use the `serve` Artisan command. This command will start a development server at `http://localhost:8000`:

로컬환경에 PHP가 설치되어 있다면 PHP의 내장 개발 서버를 사용하여 애플리케이션을 구동하고자 할 수 있는데, 이 경우 `serve` 아티즌 명령어를 사용할 수 있습니다. 이 명령어는 개발서버를 `http://localhost:8000`으로 시작할 것입니다.

    php artisan serve

More robust local development options are available via [Homestead](/docs/{{version}}/homestead) and [Valet](/docs/{{version}}/valet).

보다 강력한 로컬 개발의 대안으로 [홈스테드](/docs/{{version}}/homestead) 와 [발렛](/docs/{{version}}/valet) 도 사용할 수 있습니다.

<a name="configuration"></a>
### Configuration
### 설정하기

#### Public Directory
#### Public 디렉토리

After installing Laravel, you should configure your web server's document / web root to be the `public` directory. The `index.php` in this directory serves as the front controller for all HTTP requests entering your application.

라라벨을 설치후에 애플리케이션의 document / web 루트를 `public` 디렉토리로 설정해야 합니다. 이 디렉토리의 `index.php`는 애플리케이션의 모든 HTTP 요청에 대해서 프론트 컨트롤러로 작동합니다.

#### Configuration Files
#### 설정 파일

All of the configuration files for the Laravel framework are stored in the `config` directory. Each option is documented, so feel free to look through the files and get familiar with the options available to you.

라라벨 프레임워크의 모든 설정 파일들은 `config` 디렉토리에 위치합니다. 각각의 옵션들은 문서가 작성되어 있으며, 사용 가능한 옵션들을 이해하기 위해서 전체 문서들을 둘러보는 것이 좋습니다.

#### Directory Permissions
#### 디렉토리 권한 설정

After installing Laravel, you may need to configure some permissions. Directories within the `storage` and the `bootstrap/cache` directories should be writable by your web server or Laravel will not run. If you are using the [Homestead](/docs/{{version}}/homestead) virtual machine, these permissions should already be set.

라라벨을 설치한 뒤에, 몇몇 권한을 설정해야될 필요가 있습니다. `storage` 와 `bootstrap/cache` 디렉토리 안에 들어 있는 디렉토리들은 모두 웹 서버에 의해서 쓰기가 가능해야 합니다. 그렇지 않으면 라라벨이 실행되지 않습니다. [홈스테드](/docs/{{version}}/homestead)를 사용중이라면 이미 권한이 설정되어 있을 것입니다.

#### Application Key
#### 애플리케이션 키 설정

The next thing you should do after installing Laravel is set your application key to a random string. If you installed Laravel via Composer or the Laravel installer, this key has already been set for you by the `php artisan key:generate` command.

라라벨 설치 후 해야할 일은 여러분의 애플리케이션 키를 랜덤한 문자열로 세팅하는 것입니다. 만약 라라벨이 Composer 를 통해서 설치되었다면 키는 `php artisan key:generate` 명령어가 실행되어 이미 설정되어 있을 것입니다.

Typically, this string should be 32 characters long. The key can be set in the `.env` environment file. If you have not renamed the `.env.example` file to `.env`, you should do that now. **If the application key is not set, your user sessions and other encrypted data will not be secure!**

일반적으로, 이 키는 32자리의 문자열입니다. 이 키는 `.env` 환경설정 파일에서 세팅할 수 있습니다. 아직 `.env.example` 파일을 `.env` 로 변경하지 않았다면 지금 변경합니다. **만약 애플리케이션 키가 세팅되어있지 않다면, 여러분의 사용자 세션과 그밖의 암호화된 데이터들이 안전하지 않습니다!**

#### Additional Configuration
#### 추가적인 설정

Laravel needs almost no other configuration out of the box. You are free to get started developing! However, you may wish to review the `config/app.php` file and its documentation. It contains several options such as `timezone` and `locale` that you may wish to change according to your application.

라라벨은 앞의 내용 이외의 다른 세팅은 거의 필요 하지 않습니다. 이제 편하게 개발을 시작하면 됩니다! 그러나 가급적 `config/app.php` 파일의 내용을 살펴보길 권장합니다. `timezone`과 `locale`과 같이 여러분의 애플리케이션을 따라 바꾸기를 원하는 몇가지 옵션들을 포함하고 있습니다.

You may also want to configure a few additional components of Laravel, such as:

다음과 같은 라라벨의 컴포넌트들에 대한 몇가지 설정들을 해둘 수도 있습니다.


- [Cache](/docs/{{version}}/cache#configuration)
- [Database](/docs/{{version}}/database#configuration)
- [Session](/docs/{{version}}/session#configuration)


<a name="web-server-configuration"></a>
## Web Server Configuration
## 웹 서버 설정

<a name="pretty-urls"></a>
### Pretty URLs
### Pretty URLs

#### Apache
#### Apache

Laravel includes a `public/.htaccess` file that is used to provide URLs without the `index.php` front controller in the path. Before serving Laravel with Apache, be sure to enable the `mod_rewrite` module so the `.htaccess` file will be honored by the server.

라라벨은 주어진 경로에 `index.php` 파일이 없어도 될 수 있도록 URL 프론트 컨트롤링을 제공하는 `public/.htaccess` 파일을 가지고 있습니다. 아파치를 통해서 라라벨 웹서비스를 제공하기 전에, `.htaccess` 파일이 동작할 수 있도록 `mod_rewrite` 모듈을 활성화 하십시오.

If the `.htaccess` file that ships with Laravel does not work with your Apache installation, try this alternative:

아파치에서 라라벨이 제공하는 `.htaccess` 파일이 동작하지 않는다면, 대신 다음의 코드를 시도해보십시오:

    Options +FollowSymLinks -Indexes
    RewriteEngine On

    RewriteCond %{HTTP:Authorization} .
    RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^ index.php [L]

#### Nginx
#### Nginx

If you are using Nginx, the following directive in your site configuration will direct all requests to the `index.php` front controller:

Nginx 를 사용중이라면, 사이트 설정에 다음의 지시어를 설정하여 모든 요청이 `index.php` 프론트 컨트롤러로 향하도록 하십시오:

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

When using [Homestead](/docs/{{version}}/homestead) or [Valet](/docs/{{version}}/valet), pretty URLs will be automatically configured.

[홈스테드](/docs/{{version}}/homestead) 또는 [발렛](/docs/{{version}}/valet)을 사용중이라면 pretty URL을 자동으로 설정되어 있습니다.
