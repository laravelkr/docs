# 설치하기

- [설치하기](#installation)
- [설정하기](#configuration)
    - [기본적인 설정](#basic-configuration)
    - [구동 환경 설정하기](#environment-configuration)
    - [설정 캐싱하기](#configuration-caching)
    - [접근 설정 값](#accessing-configuration-values)
    - [애플리케이션의 이름 지정](#naming-your-application)
- [점검(공사중) 모드](#maintenance-mode)

<a name="installation"></a>
## 설치하기

### 서버 요구사항

라라벨은 몇가지 시스템 요구사항을 필요로 합니다. 물론, [라라벨 홈스테드](/docs/{{version}}/homestead)은 모든 요구사항을 만족합니다. 

- PHP >= 5.5.9
- OpenSSL PHP Extension
- PDO PHP Extension
- Mbstring PHP Extension
- Tokenizer PHP Extension

<a name="install-laravel"></a>
### 라라벨 설치하기

라라벨은 의존성 관리를 위해 [컴포저](http://getcomposer.org)를 활용합니다. 라라벨을 사용하기 전에, 여러분들은 라라벨을 설치할 서버(머신)에 Composer를 설치해야 합니다.

#### 라라벨 인스톨러로 사용하기

먼저, 라라벨 인스톨러를 컴포저를 사용하여 다운로드 합니다.

    composer global require "laravel/installer"

`laravel`이 여러분의 시스템에서 실행 가능하도록 `~/.composer/vendor/bin` 폴더를 여러분의 경로(PATH)에 추가 해야합니다. 

라라벨 인스톨러가 설치 된 후, 간단하게 `laravel new` 명령어를 입력하면 여러분이 입력한 폴더안에 깨끗한(fresh) 상태의 라라벨 파일을 생성할 것입니다. 예를들어 `laravel new blog` 명령어는 `blog`라는 폴더를 생성 할것이며 라라벨이 필요로 하는 모든 의존 패키지들의 설치와 함께 깨끗한(fresh) 상태의 라라벨 파일들을 이 폴더에 설치합니다.

    laravel new blog

#### Composer Create-Project 통한 설치

그렇지 않으면, 터미널에서 컴포저의 `create-project` 커멘드를 사용하여 라라벨을 설치할 수도 있습니다

    composer create-project laravel/laravel blog "5.1.*"

<a name="configuration"></a>
## 설정하기

<a name="basic-configuration"></a>
### 기본적인 설정하기

라라벨 프레임워크의 모든 설정 파일들은 `config` 디렉토리에 위치합니다. 각각의 옵션들은 문서가 작성되어 있으며, 사용 가능한 옵션들을 이해하기 위해서 전체 문서들을 둘러보는 것이 좋습니다. 

#### 디렉토리 권한 설정

라라벨을 설치한 뒤에, 몇몇 권한을 설정해야될 필요가 있습니다. `storage` 와 `bootstrap/cache` 디렉토리 안에 들어 있는 디렉토리들은 모두 웹 서버에 의해서 쓰기가 가능해야 합니다. [홈스테드](/docs/{{version}}/homestead)를 사용중이라면 이미 권한이 설정되어 있을 것입니다. 

#### 애플리케이션 키 설정

라라벨 설치 후 해야할 일은 여러분의 애플리케이션 키를 랜덤한 문자열로 세팅하는 것입니다. 만약 라라벨이 Composer 를 통해서 설치되었다면 키는 `key:generate` 명령어가 실행되어 이미 설정되어 있을 것입니다. 일반적으로, 이 키는 32자리의 문자열입니다. 이 키는 `.env` 환경설정 파일에서 세팅할 수 있습니다. 아직 `.env.example` 파일을 `.env` 로 변경하지 않았다면 지금 변경합니다. **만약 애플리케이션 키가 세팅되어있지 않다면, 여러분의 사용자 세션과 그밖의 암호화된 데이터들이 안전하지 않습니다!**

#### 추가적인 설정 

라라벨은 앞의 내용 이외의 다른 세팅은 거의 필요 하지 않습니다. 이제 편하게 개발을 시작하면 됩니다! 그러나 가급적 `config/app.php` 파일의 내용을 살펴보길 권장합니다. `timezone`과 `locale`과 같이 여러분의 애플리케이션을 따라 바꾸기를 원하는 몇가지 옵션들을 포함하고 있습니다.

다음과 같은 라라벨의 컴포넌트들에 대한 몇가지 설정들을 해둘 수도 있습니다. 

- [Cache](/docs/{{version}}/cache#configuration)
- [Database](/docs/{{version}}/database#configuration)
- [Session](/docs/{{version}}/session#configuration)

라라벨이 설치하면 이 문서 [로컬 환경 설정하기](/docs/{{version}}/configuration#environment-configuration)  역시 참고하시기 바랍니다.

<a name="pretty-urls"></a>
#### Pretty URLs

**Apache**

라라벨 프레임워크는 `index.php`없는 URLs를 허용하는 `public/.htaccess` 파일이 함께 설치됩니다. 만약 라라벨 애플리케이션을 구동하기 위해 아파치 서버를 사용한다면, `mod_rewrite` 모듈을 활성화 하도록 하시기 바랍니다.

라라벨과 함께 설치된 `.htaccess` 파일이 여러분의 아파치에서 동작하지 않는다면 아래의 것을 시도해보시기 바랍니다:

    Options +FollowSymLinks
    RewriteEngine On

    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^ index.php [L]

**Nginx**

Nginx에서 여러분의 서버 설정이 아래의 지시를 따른다면 “pretty” URLs를 사용할 수 있습니다:

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

물론 [Homestead](/docs/{{version}}/homestead)를 사용하신다면 pretty URLs 는 자동으로 설정되어 있을 것입니다.

<a name="environment-configuration"></a>
### 구동환경 설정

프로그램이 실행되는  구동환경에 따라 다른 설정값을 사용할 수 있으면 많은 이점이 있습니다. 예를 들어 실제 제품 서버와 로컬 개발 서버에 각각 다른 캐시 드라이버를 사용하려고 하는 경우가 그렇습니다. 이러한 경우 구동환경 설정을 통해서 손쉽게 해결 할 수 있습니다.

보다 확실하게 하기 위해서 라라벨은 Vance Lucas가 만든 PHP 라이브러리 [DotEnv](https://github.com/vlucas/phpdotenv)을 이용합니다. 새롭게 라라벨을 인스톨 한경우에 애플리케이션의 루트 디렉토리에 `.env.example` 파일이 포함되어 있습니다. 컴포저를 통해서 사용한 경우에는 이 파일이 자동으로 `.env`파일로 변경됩니다. 컴포저를 사용하지 않는다면 직접 파일을 변경하십시오. 

이 파일의 목록에 있는 모든 변수들은 애플리케이션이 요청을 받으면 `$_ENV` PHP 슈퍼 전역변수에 로드됩니다. `env` 헬퍼 함수를 통해서 이러한 변수값들을 검색 할수 있습니다. 만약 여러분이 라라벨 설정 파일들을 살펴보았다면 이미 여러 옵션 설정에 헬퍼 함수가 사용되었다는 것을 알 수 있을 것입니다!

실제 제품 환경(production)과 마찬가지로 로컬 서버의 필요에 맞게 환경 변수를 변경하십시오. 하지만 여러분의 개발자 / 서버들이 각기 다르 른 개발환경 구성을 필요로 할 수도 있기 때문에, `.env` 파일을 애플리케이션의 소스에 커밋하지 마십시오. 

팀으로 개발을 하는 경우라면 `.env.example` 파일을 애플리케이션에 포함할 수 있습니다. example 설정 파일에 적절한 기본값을 넣어두면 다른 개발자들이 응용 프로그램을 실행하는 데 어떤 환경 변수 설정이 필요한지 명확하게 이해할 수 있습니다.

#### 현재의 애플리케이션 구동환경에 엑세스

현재의 구동환경 값은 `.env` 파일의 `APP_ENV` 값에 의해서 결정됩니다. 또한 `App` [파사드](/docs/{{version}}/facades)의 `environment` 메소드를 통해서도 확인할 수 있습니다. 

    $environment = App::environment();

또는 `environment` 메소드에 매개변수를 넘겨주어 해당 구동환경을 확인하는 형태로 사용할 수도 있습니다. 필요하다면 여러개의 값을 넘겨줄 수도 있습니다. 

    if (App::environment('local')) {
        // The environment is local
    }

    if (App::environment('local', 'staging')) {
        // The environment is either local OR staging...
    }

또한 애플리케이션의 인스턴스는 `app` 헬퍼 함수를 통해서도 엑세스 할 수 있습니다:

    $environment = app()->environment();

<a name="configuration-caching"></a>
### 설정 캐싱하기

애플리케이션을 보다 빠르게 구동하기 위해서는, 여러분은 `config:cache` 아티즌 명령어를 사용하여 모든 설정 파일들을 하나의 파일로 캐싱해야 합니다. 이렇게 하면 애플리케이션의 모든 설정 옵션들이 하나의 파일로 합쳐져 프레임워크 구동이 훨씬 빨라질 것입니다. 

일반적으로 `php artisan config:cache` 명령어는 실 서버 환경에서 실행합니다. 애플리케이션 개발중에는 이 설정 옵션들이 빈번하게 변경되기 때문에, 이 명령어는 개발 환경에서는 실행시키지 말아야 합니다. 

<a name="accessing-configuration-values"></a>
### 설정 값 확인하기

`config` 헬퍼 함수를 사용하면 손쉽게 설정값을 가져올 수 있습니다. 설정 값은 파일 이름과 옵션 값에 해당하는 "점" 으로 구분되는 문법을 통해서 엑세스 할 수 있습니다. 만약 설정 옵션값이 존재하지 않는다면 지정된 기본값을 반환합니다.

    $value = config('app.timezone');

애플리케이션이 구동중에 설정 값을 지정하려면 `config` 헬퍼 함수에 배열을 전달하면 됩니다. 

    config(['app.timezone' => 'America/Chicago']);

<a name="naming-your-application"></a>
### 애플리케이션에 이름 지정하기

라라벨을 설치한 뒤에 애플리케이션의 이름을 지정할 수도 있습니다. 기본적으로 `app`디렉토리는 컴포저의 [PSR-4 autoloading standard](http://www.php-fig.org/psr/psr-4/)로 오토로딩되는 `App`이라는 네임스페이스를 가집니다. 하지만 이것을 애플리케이션을 위한 고유한 네임스페이스로 변경하길 원할수도 있기 때문에 이러한 경우 `app:name` 이라는 아티즌 명령어를 통해서 손쉽게 변경할 수 있습니다. 

예를 들어 애플리케이션을 "Horsefly"라고 이름을 지정한다고 하면 설치한 루트 디렉토리에서 다음과 같이 명령어를 실행하면 됩니다. 

    php artisan app:name Horsefly

애플리케이션의 이름을 재지정하는 것은 선택사항입니다. `App`이라는 네임스페이스를 그냥 사용해도 무방합니다. 

<a name="maintenance-mode"></a>
## 점검 모드(유지보수 모드 - 공사중)

애플리케이션이 점검 모드일 경우 애플리케이션에서 지정한 모든 라우팅이 특정한 화면으로 보여지게 됩니다. 따라서 애플리케이션을 업데이트 하거나 튜닝하는 중에 손쉽게 "사용 정지"시킬 수 있습니다. 점검 모드를 확인하는 부분은 애플리케이션의 미들웨어 스택에 기본적으로 포함되어 있습니다. 만약 애플리케이션이 점검 모드에 있다면 503 상태 코드와 함께 `HttpException`이 발생합니다. 

점검 모드를 활성화 하려면 아티즌 명령어 `down`을 실행시키면 됩니다:

    php artisan down

점검 모드를 해제하려면 아티즌 명령어 `up`을 실행시키면 됩니다:

    php artisan up

### 점검모드에서 응답 화면 템플릿

점검모드의 응답페이지는 `resources/views/errors/503.blade.php` 파일에 위치하고 있습니다. 

### 점검 모드와 큐 

애플리케이션이 점검 모드인 동안에는 [큐 작업](/docs/{{version}}/queues)은 동작하지 않습니다. 큐 작업들은 점검 모드가 해제된 애플리케이션이 일반 모드로 전환된 시점에서 계속 처리됩니다. 
