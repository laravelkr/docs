# 설정

- [소개](#introduction)
- [설정 값에 엑세스 하기](#accessing-configuration-values)
- [구동환경 설정](#environment-configuration)
    - [현재 구동환경 결정하기](#determining-the-current-environment)
- [설정 캐시](#configuration-caching)
- [점검(공사중) 모드](#maintenance-mode)

<a name="introduction"></a>
## 소개

라라벨의 모든 설정 파일은 `config` 디렉토리에 저장됩니다. 모든 파일에는 개별 옵션에 대한 설명이 들어 있으니 해당 옵션에 대해 이해하는데 도움을 받을 수 있습니다. 

<a name="accessing-configuration-values"></a>
## 설정 값에 엑세스 하기

애플리케이션의 어디에서라도 `config` 헬퍼 함수를 사용하여 손쉽게 설정 값에 엑세스 할 수 있습니다. 설정된 값은 파일의 이름과 엑세스 하고자 하는 옵션에 대해서, ".(점)" 문법을 사용하여 엑세스 할 수 있습니다. 설정된 옵션 값이 존재하지 않았을 때 반환될 기본 값을 지정할 수도 있습니다. 

    $value = config('app.timezone');

애플리케이션의 실행중에 설정 값을 변경하려면 `config` 헬퍼에 배열을 전달 하십시오:

    config(['app.timezone' => 'America/Chicago']);

<a name="environment-configuration"></a>
## 구동환경 설정

프로그램이 실행되는  구동환경에 따라 다른 설정값을 사용할 수 있으면 많은 이점이 있습니다. 예를 들어 실제 제품 서버와 로컬 개발 서버에 각각 다른 캐시 드라이버를 사용하려고 하는 경우가 그렇습니다. 이러한 경우 구동환경 설정을 통해서 손쉽게 해결 할 수 있습니다.

보다 확실하게 하기 위해서 라라벨은 Vance Lucas가 만든 PHP 라이브러리 [DotEnv](https://github.com/vlucas/phpdotenv)을 이용합니다. 새롭게 라라벨을 인스톨 한경우에 애플리케이션의 루트 디렉토리에 `.env.example` 파일이 포함되어 있습니다. 컴포저를 통해서 설치한 경우에는 이 파일이 자동으로 `.env`파일로 변경됩니다. 컴포저를 사용하지 않는다면 직접 파일을 변경하십시오. 

이 파일의 목록에 있는 모든 변수들은 애플리케이션이 요청을 받으면 `$_ENV` PHP 슈퍼 전역변수에 로드됩니다. `env` 헬퍼 함수를 통해서 설정 파일안에 있는 이러한 변수값들을 조회 할 수 있습니다. 만약 여러분이 라라벨 설정 파일들을 살펴보았다면 이미 여러 옵션 설정에 헬퍼 함수가 사용되었다는 것을 알 수 있을 것입니다!

    'debug' => env('APP_DEBUG', false),

`env` 함수에 전달되는 두번째 인자는 "기본값" 입니다. 이 값은 주어진 환경 이름이 존재하지 않을 경우에 사용될것입니다. 

개별 개발자와 서버에서 애플리케이션별로 다른 구동 환경 설정을 필요로 하기 때문에, `.env` 파일을 애플리케이션의 소스 컨트롤 시스템에 커밋하지 않아야 합니다. 

팀으로 개발을 하는 경우라면 `.env.example` 파일을 애플리케이션에 포함할 수 있습니다. example 설정 파일에 적절한 기본값을 넣어두면 다른 개발자들이 응용 프로그램을 실행하는 데 어떤 환경 변수 설정이 필요한지 명확하게 이해할 수 있습니다.

<a name="determining-the-current-environment"></a>
### 현재 구동환경 결정하기

현재의 애플리케이션 구동 환경은 `.env` 파일의 `APP_ENV` 변수를 통해서 결정됩니다. `App` [파사드](/docs/{{version}}/facades)의 `environment` 메소드를 통해서 이 값에 접근 할 수 있습니다: 

    $environment = App::environment();

또한 `environment` 메소드에 확인하고자 하는 구동 환경에 대한 값을 인자로 전달 할 수도 있습니다. 필요한 경우, `environment` 메소드에 여러개의 값을 전달할 수도 있고, 메소드는 `true` 를 반환할 것입니다. 

    if (App::environment('local')) {
        // The environment is local
    }

    if (App::environment('local', 'staging')) {
        // The environment is either local OR staging...
    }

애플리케이션 인스턴스는 `app` 헬퍼 메소드를 통해서도 접근할 수 있습니다:

    $environment = app()->environment();

<a name="configuration-caching"></a>
## 설정 캐시

애플리케이션의 구동 속도를 더 빠르게 하려면, `config:cache` 아티즌 명령어를 통해서 설정 파일들을 하나로 묶어 캐싱해야 합니다. 이 명령어는 모든 설정 옵션들을 하나의 파일로 묶어 애플리케이션이 빠르게 로드할 수 있게 해줍니다.

일반적으로 `php artisan config:cache` 명령어를 애플리케이션 배포 루틴의 일부에서 실행하도록 해야 합니다. 애플리케이션 개발 중에는 설정 옵션 값이 자주 바뀔 필요가 있기 때문에, 로컬 개발 환경에서는 이 명령어를 실행하지 말아야 합니다. 

<a name="maintenance-mode"></a>
## 점검(공사중) 모드

애플리케이션이 점검 모드일 경우 애플리케이션에서 지정한 모든 라우팅이 특정한 화면으로 보여지게 됩니다. 따라서 애플리케이션을 업데이트 하거나 튜닝하는 중에 손쉽게 "사용 정지"시킬 수 있습니다. 점검 모드를 확인하는 부분은 애플리케이션의 미들웨어 스택에 기본적으로 포함되어 있습니다. 만약 애플리케이션이 점검 모드에 있다면 503 상태 코드와 함께 `HttpException`이 발생합니다. 

점검 모드를 활성화 하려면 아티즌 명령어 `down`을 실행시키면 됩니다:

    php artisan down

점검 모드를 해제하려면 아티즌 명령어 `up`을 실행시키면 됩니다:

    php artisan up

#### 점검모드의 템플릿

점검모드의 응답페이지는 `resources/views/errors/503.blade.php` 파일에 위치하고 있습니다. 애플리케이션에서 필요한 경우 이 뷰 파일을 자유롭게 수정할 수 있습니다. 

#### 점검모드 & 큐-Queue

애플리케이션이 점검 모드인 동안에는 [큐 작업](/docs/{{version}}/queues)은 동작하지 않습니다. 큐 작업들은 점검 모드가 해제된 애플리케이션이 일반 모드로 전환된 시점에서 계속 처리됩니다. 

#### 점검 모드의 대안

점검 모드 동안에는 애플리케이션이 얼마동안 완전히 다운되어 있게 되므로, 라라벨의 제로-타임 배포를 위해서 [Envoyer](https://envoyer.io)같은 대안을 고려할 수도 있습니다. 