# 설정(Configuration)

- [소개](#introduction)
- [설치 후 할 일](#after-installation)
- [설정값 조회하기](#accessing-configuration-values)
- [구동환경 설정](#environment-configuration)
- [설정값 캐시](#configuration-caching)
- [점검(공사중) 모드](#maintenance-mode)
- [깔끔한 URL](#pretty-urls)

<a name="introduction"></a>
## 소개

라라벨의 모든 설정 파일은 `config` 디렉토리에 저장됩니다. 모든 파일에는 개별 옵션에 대한 설명이 들어 있으니 해당 옵션에 대해 이해하는 데 도움을 받을 수 있습니다.

<!--chak-comment-설정(Configuration)-소개-->

<a name="after-installation"></a>
## 설치 후 할 일

### 어플리케이션 네임 지정하기

라라벨을 설치한 뒤에 어플리케이션의 이름을 지정하길 원할 수도 있습니다. 기본적으로 `app`디렉토리는 컴포저의 [PSR-4 autoloading standard](http://www.php-fig.org/psr/psr-4/)로 오토로딩되는 `App`이라는 네임스페이스를 가집니다. 하지만 지정된 이름을 어플리케이션을 위한 고유한 네임스페이스로 변경하길 원할 수도 있으므로 이러한 경우 `app:name`이라는 아티즌 명령어를 통해서 손쉽게 변경할 수 있습니다.

예를 들어, 어플리케이션을 "Horsefly"라고 이름을 지정한다고 하면, 설치한 루트 디렉토리에서 다음과 같이 명령어를 실행하면 됩니다:

	php artisan app:name Horsefly

어플리케이션의 이름을 재지정하는 것은 선택사항입니다. `App`이라는 네임스페이스를 그냥 사용해도 무방합니다.

### 다른 설정들

라라벨은 구동에 필요한 설정이 그리 많지는 않습니다. 하지만 `config/app.php` 파일과 그 주석들을 읽어두는 것이 좋습니다. 거기에는 여러분의 지역에 따라 변경하고 싶어할만한 `timezone`과 `locale`과 같은 옵션들을 포함하고 있습니다.

라라벨이 설치된 이후에 여러분의 [로컬 개발환경을 설정](/docs/5.0/configuration#environment-configuration) 하기를 바랍니다.

> **주의:** 실서버(production)에서 구동하는 경우에는 `app.debug` 설정을 `true`로 설정하지 마십시오.

<a name="permissions"></a>
### 권한

라라벨은 한 가지 권한설정을 해야 합니다: `storage` 폴더와 `vendor` 폴더는 웹서버에서 쓰기접근이 가능해야 합니다.

<!--chak-comment-설정(Configuration)-설치-후-할-일-->

<a name="accessing-configuration-values"></a>
## 환경설정값 가져오기

`Config` 파사드를 사용하면 손쉽게 설정값을 가져올 수 있습니다:

	$value = Config::get('app.timezone');

	Config::set('app.timezone', 'America/Chicago');

또한, `config` 헬퍼 함수를 사용할 수도 있습니다:

	$value = config('app.timezone');

<!--chak-comment-설정(Configuration)-환경설정값-가져오기-->

<a name="environment-configuration"></a>
## 구동환경 설정

프로그램이 실행되는 구동환경에 따라 다른 설정 값을 사용할 수 있으면 많은 이점이 있습니다. 예를 들어, 실제 제품 서버와 로컬 개발 서버에 각각 다른 캐시 드라이버를 사용하려고 하는 경우가 그렇습니다. 이럴 때 구동환경 설정을 통해서 손쉽게 해결할 수 있습니다.

보다 구분이 쉽게 하기 위해서 라라벨은 Vance Lucas가 만든 PHP 라이브러리 [DotEnv](https://github.com/vlucas/phpdotenv)을 이용합니다. 새롭게 라라벨을 인스톨 한 경우라면 어플리케이션의 루트 디렉토리에 `.env.example` 파일이 포함되어 있습니다. 컴포저를 통해서 사용한 경우에는 이 파일이 자동으로 `.env`파일로 변경됩니다. 컴포저를 사용하지 않는다면 직접 파일을 변경하십시오.

이 파일의 목록에 있는 모든 변수는 어플리케이션이 요청을 받으면 `$_ENV` PHP 슈퍼 전역변수에 로드됩니다. `env` 헬퍼 함수를 통해서 이러한 변수 값들을 검색 할수 있습니다. 만약 여러분이 라라벨 설정 파일들을 살펴보았다면 이미 여러 옵션 설정에 헬퍼 함수가 사용되었다는 것을 알 수 있을 것입니다!

실제 제품 환경(production)과 마찬가지로 로컬 서버의 필요에 맞게 환경 변수를 변경하십시오. 하지만 여러분의 개발자 / 서버들이 각기 다른 개발환경 구성이 필요할 수 있으므로, `.env` 파일을 어플리케이션의 소스에 커밋하지 마십시오.

팀으로 개발하는 경우라면 `.env.example` 파일을 어플리케이션에 포함할 수 있습니다. 샘플 설정 파일에 적절한 기본값을 넣어두면 다른 개발자들이 응용 프로그램을 실행하는 데 어떤 환경 변수 설정이 필요한지 명확하게 이해할 수 있습니다.

#### 현재의 어플리케이션 구동환경에 엑세스

`Application` 인스턴스의 `environment` 메소드를 통해서 현재의 어플리케이션 구동환경에 액세스 할 수도 있습니다:

	$environment = $app->environment();

또는 `environment` 메소드에 매개변수를 넘겨주어 해당 구동환경을 확인하는 형태로 사용할 수도 있습니다:

	if ($app->environment('local'))
	{
		// The environment is local
	}

	if ($app->environment('local', 'staging'))
	{
		// The environment is either local OR staging...
	}

어플리케이션의 인스턴스를 획득하기 위해서 [서비스 컨테이너](/docs/5.0/container)를 통해서 `Illuminate\Contracts\Foundation\Application` contract에 대한 의존 객체를 불러옵니다. 만약 [서비스 컨테이너](/docs/5.0/providers)에서라면 어플리케이션의 인스턴스는 `$this->app` 변수를 통해서도 사용할 수 있습니다.

또한, 어플리케이션의 인스턴스는 `app` 헬퍼 함수 또는 `App` 파사드를 통해서도 엑세스 할 수 있습니다:

	$environment = app()->environment();

	$environment = App::environment();

<!--chak-comment-설정(Configuration)-구동환경-설정-->

<a name="configuration-caching"></a>
## 설정 캐시

어플리케이션의 속도를 더 빠르게 하려면 `config:cache` 아티즌 명령어를 통해서 설정 파일들을 하나로 묶어 캐싱할 수 있습니다. 이 명령어는 모든 설정 옵션들을 하나의 파일로 묶어 어플리케이션이 빠르게 로드할 수 있게 해줍니다.

일반적으로 배포하는 경우라면 `config:cache` 명령을 실행할 것입니다.

<!--chak-comment-설정(Configuration)-설정-캐시-->

<a name="maintenance-mode"></a>
## 점검 모드(유지보수 모드 - 공사중)

어플리케이션이 점검 모드일 경우 어플리케이션에서 지정한 모든 라우팅이 특정한 화면으로 보여지게 됩니다. 따라서 어플리케이션을 업데이트 하거나 튜닝하는 중에 손쉽게 "사용 정지"시킬 수 있습니다. 점검 모드를 확인하는 부분은 어플리케이션의 미들웨어 스택에 기본적으로 포함되어 있습니다. 만약 어플리케이션이 점검 모드에 있다면 503 상태 코드와 함께 `HttpException`이 발생합니다.

점검 모드를 활성화 하려면 아티즌 명령어 `down`을 실행 시키면 됩니다:

	php artisan down

점검 모드를 해제하려면 아티즌 명령어 `up`을 실행 시키면 됩니다:

	php artisan up

### 점검모드의 템플릿

점검모드의 응답페이지는 `resources/views/errors/503.blade.php` 파일에 있습니다.

### 점검 모드에서의 큐

어플리케이션이 점검 모드인 동안에는 [큐 작업](/docs/5.0/queues)은 동작하지 않습니다. 큐 작업들은 점검 모드가 해제된 어플리케이션이 일반 모드로 전환된 시점에서 계속 처리됩니다.

<!--chak-comment-설정(Configuration)-점검-모드(유지보수-모드---공사중)-->

<a name="pretty-urls"></a>
## 깔끔한 URL

### Apache

프레임워크에 포함된 `public/.htaccess` 파일은 `index.php`가 URL에 포함되어 있지 않아도 어플리케이션을 사용할 수게 해줍니다. 라라벨 어플리케이션의 웹서버로 Apache를 사용하는 경우 `mod_rewrite` 모듈을 활성화 하십시오.

만약 프레임워크에 포함된 `.htaccess` 파일이 동작하지 않는 경우 다음과 같은 설정을 시도해보십시오:

	Options +FollowSymLinks
	RewriteEngine On

	RewriteCond %{REQUEST_FILENAME} !-d
	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteRule ^ index.php [L]

### Nginx

Nginx의 경우라면 아래처럼 사이트 설정에서 지정하면 "깔끔한" URL을 사용할 수 있습니다:

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

당연하게도 [Homestead](/docs/5.0/homestead)를 사용하고 있다면 이미 이러한 설정이 적용되어 있습니다.

<!--chak-comment-설정(Configuration)-깔끔한-URL-->
