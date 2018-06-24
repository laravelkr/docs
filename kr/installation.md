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

<a name="installation"></a>
## Installation
## 설치하기

<a name="server-requirements"></a>
### Server Requirements
### 서버 요구사항

The Laravel framework has a few system requirements. Of course, all of these requirements are satisfied by the [Laravel Homestead](/docs/{{version}}/homestead) virtual machine, so it's highly recommended that you use Homestead as your local Laravel development environment.

라라벨은 몇가지 시스템 요구사항을 필요로 합니다. 물론, [라라벨 홈스테드](/docs/{{version}}/homestead)은 모든 요구사항을 만족합니다. 따라서 여러분의 로컬 라라벨 개발 환경에서 홈스테드를 사용할 것을 강력 권장합니다.

However, if you are not using Homestead, you will need to make sure your server meets the following requirements:

하지만, 홈스테드를 사용하지 않는 경우, 서버에서 다음의 요구사항을 만족하는지 확인해야합니다:

- PHP version between 5.5.9 - 7.1.*
- OpenSSL PHP Extension
- PDO PHP Extension
- Mbstring PHP Extension
- Tokenizer PHP Extension

<a name="installing-laravel"></a>
### Installing Laravel
### 라라벨 설치하기

Laravel utilizes [Composer](http://getcomposer.org) to manage its dependencies. So, before using Laravel, make sure you have Composer installed on your machine.

라라벨은 의존성 관리를 위해 [컴포저](http://getcomposer.org)를 활용합니다. 라라벨을 사용하기 전에, 여러분들은 라라벨을 설치할 서버(머신)에 Composer를 설치해야 합니다.

#### Via Laravel Installer
#### 라라벨 인스톨러로 사용하기

First, download the Laravel installer using Composer:

먼저, 라라벨 인스톨러를 컴포저를 사용하여 다운로드 합니다.

    composer global require "laravel/installer"

Make sure to place the `~/.composer/vendor/bin` directory (or the equivalent directory for your OS) in your PATH so the `laravel` executable can be located by your system.

`laravel`이 여러분의 시스템에서 실행 가능하도록(또는 여러분의 OS에서 동일한 디렉토리) `~/.composer/vendor/bin` 폴더를 여러분의 경로(PATH)에 추가 해야합니다.

Once installed, the `laravel new` command will create a fresh Laravel installation in the directory you specify. For instance, `laravel new blog` will create a directory named `blog` containing a fresh Laravel installation with all of Laravel's dependencies already installed. This method of installation is much faster than installing via Composer:

라라벨 인스톨러가 설치 된 후, `laravel new` 명령어를 입력하면 여러분이 입력한 폴더안에 깨끗한(fresh) 상태의 라라벨 파일을 생성할 것입니다. 예를들어 `laravel new blog` 명령어는 `blog`라는 폴더를 생성 할것이며 라라벨이 필요로 하는 모든 의존 패키지들의 설치와 함께 깨끗한(fresh) 상태의 라라벨 파일들을 이 폴더에 설치합니다.

    laravel new blog

#### Via Composer Create-Project
### Composer Create-Project 통한 설치

Alternatively, you may also install Laravel by issuing the Composer `create-project` command in your terminal:

그렇지 않으면, 터미널에서 컴포저의 `create-project` 커멘드를 사용하여 라라벨을 설치할 수도 있습니다

    composer create-project --prefer-dist laravel/laravel blog "5.2.*"

<a name="configuration"></a>
### Configuration
### 설정하기

After installation, you should configure your application's document / web root to the `public` directory.

설치후에 애플리케이션의 document / web 루트를 `public` 디렉토리로 설정해야 합니다.

All of the configuration files for the Laravel framework are stored in the `config` directory. Each option is documented, so feel free to look through the files and get familiar with the options available to you.

라라벨 프레임워크의 모든 설정 파일들은 `config` 디렉토리에 위치합니다. 각각의 옵션들은 문서가 작성되어 있으며, 사용 가능한 옵션들을 이해하기 위해서 전체 문서들을 둘러보는 것이 좋습니다.

#### Directory Permissions
#### 디렉토리 권한 설정

After installing Laravel, you may need to configure some permissions. Directories within the `storage` and the `bootstrap/cache` directories should be writable by your web server or Laravel will not run. If you are using the [Homestead](/docs/{{version}}/homestead) virtual machine, these permissions should already be set.

라라벨을 설치한 뒤에, 몇몇 권한을 설정해야될 필요가 있습니다. `storage` 와 `bootstrap/cache` 디렉토리 안에 들어 있는 디렉토리들은 모두 웹 서버에 의해서 쓰기가 가능해야 합니다. 그렇지 않으면 라라벨이 실행되지 않습니다. [홈스테드](/docs/{{version}}/homestead)를 사용중이라면 이미 권한이 설정되어 있을 것입니다.

#### Application Key
#### 애플리케이션 키 설정

The next thing you should do after installing Laravel is set your application key to a random string. If you installed Laravel via Composer or the Laravel installer, this key has already been set for you by the `php artisan key:generate` command. Typically, this string should be 32 characters long. The key can be set in the `.env` environment file. If you have not renamed the `.env.example` file to `.env`, you should do that now. **If the application key is not set, your user sessions and other encrypted data will not be secure!**

라라벨 설치 후 해야할 일은 여러분의 애플리케이션 키를 랜덤한 문자열로 세팅하는 것입니다. 만약 라라벨이 Composer 를 통해서 설치되었다면 키는 `php artisan key:generate` 명령어가 실행되어 이미 설정되어 있을 것입니다. 일반적으로, 이 키는 32자리의 문자열입니다. 이 키는 `.env` 환경설정 파일에서 세팅할 수 있습니다. 아직 `.env.example` 파일을 `.env` 로 변경하지 않았다면 지금 변경합니다. **만약 애플리케이션 키가 세팅되어있지 않다면, 여러분의 사용자 세션과 그밖의 암호화된 데이터들이 안전하지 않습니다!**

#### Additional Configuration
#### 추가적인 설정

Laravel needs almost no other configuration out of the box. You are free to get started developing! However, you may wish to review the `config/app.php` file and its documentation. It contains several options such as `timezone` and `locale` that you may wish to change according to your application.

라라벨은 앞의 내용 이외의 다른 세팅은 거의 필요 하지 않습니다. 이제 편하게 개발을 시작하면 됩니다! 그러나 가급적 `config/app.php` 파일의 내용을 살펴보길 권장합니다. `timezone`과 `locale`과 같이 여러분의 애플리케이션을 따라 바꾸기를 원하는 몇가지 옵션들을 포함하고 있습니다.

You may also want to configure a few additional components of Laravel, such as:

다음과 같은 라라벨의 컴포넌트들에 대한 몇가지 설정들을 해둘 수도 있습니다.

- [Cache](/docs/{{version}}/cache#configuration)
- [Database](/docs/{{version}}/database#configuration)
- [Session](/docs/{{version}}/session#configuration)

Once Laravel is installed, you should also [configure your local environment](/docs/{{version}}/configuration#environment-configuration).

라라벨을 설치하면 이 문서 [구동 환경 설정](/docs/{{version}}/configuration#environment-configuration)도 참고하시기 바랍니다.
