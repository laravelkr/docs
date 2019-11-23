# 설정

- [시작하기](#introduction)
- [구동환경 설정](#environment-configuration)
    - [환경 변수의 타입](#environment-variable-types)
    - [구동환경 조회하기](#retrieving-environment-configuration)
    - [현재 구동환경 결정하기](#determining-the-current-environment)
    - [디버그 페이지에서 환경 변수 숨기기](#hiding-environment-variables-from-debug)
- [설정 값에 엑세스 하기](#accessing-configuration-values)
- [설정 캐시](#configuration-caching)
- [점검(공사중) 모드](#maintenance-mode)

<a name="introduction"></a>
## 시작하기

라라벨의 모든 설정 파일은 `config` 디렉토리에 저장됩니다. 모든 파일에는 개별 옵션에 대한 설명이 들어 있으니 해당 옵션에 대해 이해하는데 도움을 받을 수 있습니다.

<a name="environment-configuration"></a>
## 구동환경 설정

프로그램이 실행되는 구동환경에따라 다른 설정값을 사용할 수 있으면 많은 이점이 있습니다. 예를 들어 실제 제품 서버와 로컬 개발 서버에 각각 다른 캐시 드라이버를 사용하려고 하는 경우가 그렇습니다.

보다 확실하게 하기 위해서 라라벨은 Vance Lucas가 만든 PHP 라이브러리 [DotEnv](https://github.com/vlucas/phpdotenv)을 이용합니다. 새롭게 라라벨을 인스톨 한경우에 애플리케이션의 루트 디렉토리에 `.env.example` 파일이 포함되어 있습니다. 컴포저를 통해서 설치한 경우에는 이 파일이 자동으로 `.env`파일로 변경됩니다. 컴포저를 사용하지 않는다면 직접 파일을 변경하십시오.

개별 개발자와 서버에서 애플리케이션별로 다른 구동 환경 설정을 필요로 하기 때문에, `.env` 파일을 애플리케이션의 소스 컨트롤 시스템에 커밋하지 않아야 합니다. 또한 이는 공격자가 소스 컨트롤 저장소에 엑세스 권한을 얻게 되는 경우에, 민감한 계정정보가 노출될 위험이 있어 보안 취약점이 될 수도 있습니다.

팀으로 개발을 하는 경우라면 `.env.example` 파일을 애플리케이션에 포함할 수 있습니다. example 설정 파일에 적절한 기본값을 넣어두면 다른 개발자들이 애플리케이션을 실행하는 데 어떤 환경 변수 설정이 필요한지 명확하게 이해할 수 있습니다. 또한 `.env.testing` 파일을 생성할 수 있습니다. 이 파일은 PHPUnit 테스트나 아티즌 명령어가 `--env=testing` 옵션과 함께 실행될 때 `.env`를 오버라이드 합니다.

> {tip} `.env` 파일의 어떤 변수는 서버의 또는 시스템의 환경 변수와 같은 외부 환경 변수에 의해서 무시될 수도 있습니다.

<a name="environment-variable-types"></a>
### 환경 변수의 타입

`.env` 파일의 모든 변수들은 문자열로 파싱되기 때문에, 몇몇의 예약된 값들이 다른 타입의 값들을 표현할 수 있도록 추가되었습니다.

`.env` Value  | `env()` Value
------------- | -------------
true | (bool) true
(true) | (bool) true
false | (bool) false
(false) | (bool) false
empty | (string) ''
(empty) | (string) ''
null | (null) null
(null) | (null) null

공백이 포함 된 값으로 환경 변수를 정의해야하는 경우 값을 쌍따옴표로 묶어 환경 변수를 정의 할 수 있습니다.

    APP_NAME="My Application"

<a name="retrieving-environment-configuration"></a>
### 구동환경 조회하기

이 파일의 목록에 있는 모든 변수는 애플리케이션이 요청을 받을 때 `$_ENV` PHP 슈퍼 전역변수에 로드됩니다. `env` 헬퍼 함수를 통해서 설정 파일안에 있는 이러한 변수값들을 조회 할 수 있습니다. 만약 여러분이 라라벨 설정 파일들을 살펴보았다면 이미 여러 옵션 설정에 헬퍼 함수가 사용되었다는 것을 알 수 있을 것입니다!

    'debug' => env('APP_DEBUG', false),

`env` 함수에 전달되는 두 번째 인자는 "기본값" 입니다. 이 값은 주어진 키를 위한 환경 변수가 존재하지 않을 때 사용될 것입니다.

개별 개발자와 서버에서 애플리케이션별로 다른 구동 환경 설정을 필요로 하기 때문에, `.env` 파일을 애플리케이션의 소스 컨트롤 시스템에 커밋하지 않아야 합니다.

<a name="determining-the-current-environment"></a>
### 현재 구동환경 결정하기

현재의 애플리케이션 구동 환경은 `.env` 파일의 `APP_ENV` 변수를 통해서 결정됩니다. `App` [파사드](/docs/{{version}}/facades)의 `environment` 메소드를 통해서 이 값에 접근 할 수 있습니다.

    $environment = App::environment();

또한 `environment` 메소드에 확인하고자 하는 구동 환경에 대한 값을 인자로 전달할 수도 있습니다. 메소드는 주어진 값들에 일치하는 게 하나라도 있다면 `true`를 반환할 것입니다.

    if (App::environment('local')) {
        // The environment is local
    }

    if (App::environment(['local', 'staging'])) {
        // The environment is either local OR staging...
    }

> {tip} 현재의 애플리케이션 구동 환경의 감지하는 것은 서버의 APP_ENV 환경 변수에 의해서 오버라이딩 될 수 있습니다. 이 기능은 여러개의 환경 구성에 동일한 애플리케이션을 공유해야 할 때 유용하며, 따라서 여러분은 주어진 호스트가 주어진 환경과 매칭되도록 설정할 수 있습니다.

<a name="hiding-environment-variables-from-debug"></a>
### 디버그 페이지에서 환경 변수 숨기기

예외가 발생하지 않고 `APP_DEBUG` 환경 변수가 `true` 라면, 디버그 페이지는 모든 환경 변수와 그 내용을 보여줍니다. 경우에 따라 특정 변수를 가릴 수 있습니다. `config/app.php` 설정 파일에서 `debug_blacklist` 옵션을 업데이트하면됩니다.

일부 변수는 환경 변수와 서버 / 요청 데이터에서 모두 사용할 수 있습니다. 그러므로 `$_ENV` 와 `$_SERVER` 둘 다 블랙리스트에 올릴 필요가 있습니다.

    return [

        // ...

        'debug_blacklist' => [
            '_ENV' => [
                'APP_KEY',
                'DB_PASSWORD',
            ],

            '_SERVER' => [
                'APP_KEY',
                'DB_PASSWORD',
            ],

            '_POST' => [
                'password',
            ],
        ],
    ];

<a name="accessing-configuration-values"></a>
## 설정 값에 엑세스 하기

애플리케이션의 어디에서라도 `config` 헬퍼 함수를 사용하여 손쉽게 설정 값에 엑세스 할 수 있습니다. 설정된 값은 파일의 이름과 엑세스 하고자 하는 옵션에 대해서, ".(점)" 문법을 사용하여 엑세스 할 수 있습니다. 설정된 옵션값이 존재하지 않았을 때 반환될 기본값을 지정할 수 있습니다.

    $value = config('app.timezone');

애플리케이션의 실행중에 설정값을 변경하려면 `config` 헬퍼에 배열을 전달하십시오:

    config(['app.timezone' => 'America/Chicago']);

<a name="configuration-caching"></a>
## 설정 캐시

애플리케이션의 구동 속도를 더 빠르게 하려면, `config:cache` 아티즌 명령어를 통해서 설정 파일들을 하나로 묶어 캐싱해야 합니다. 이 명령어는 모든 설정 옵션을 하나의 파일로 묶어 애플리케이션이 빠르게 로드할 수 있게 해줍니다.

일반적으로 `php artisan config:cache` 명령어를 애플리케이션 배포 루틴의 일부에서 실행하도록 해야 합니다. 애플리케이션 개발 중에는 설정 옵션값이 자주 바뀔 필요가 있기 때문에, 로컬 개발 환경에서는 이 명령어를 실행하지 말아야 합니다.

> {note} 배포가 진행 되는 도중에 `config:cache` 명령어를 실행한다면, 설정 파일안에서 `env` 함수만 호출된다는 것을 확인해야 합니다. 한번, 설정이 캐싱되고 나면, `.env` 파일은 로딩되지 않고, 모든 `env` 함수는 `null`을 반환합니다.

<a name="maintenance-mode"></a>
## 점검(공사중) 모드

애플리케이션이 점검 모드일 경우 애플리케이션에서 지정한 모든 라우팅이 특정한 화면으로 보여지게 됩니다. 따라서 애플리케이션을 업데이트 하거나 튜닝하는 중에 손쉽게 "사용 정지"시킬 수 있습니다. 점검 모드를 확인하는 부분은 애플리케이션의 미들웨어 스택에 기본적으로 포함되어 있습니다. 만약 애플리케이션이 점검 모드에 있다면 503 상태 코드와 함께 `MaintenanceModeException`이 발생합니다.

점검 모드를 활성화 하려면 아티즌 명령어 `down`을 실행시키면 됩니다.

    php artisan down

또한 `down` 명령어에 `message` 와 `retry` 옵션을 지정할 수 있습니다. `message` 값은 화면에 보여주거나, 로그에 사용자 메세지를 남기는데 사용되고, `retry` 값은 HTTP 헤더의 `Retry-After` 를 설정하는데 사용될 것입니다.

    php artisan down --message="Upgrading Database" --retry=60

점검 모드 중이지만, 지정된 IP나 네트워크 대역에 대해서는 애플리케이션에 접속할 수 있게 하고자 한다면, 명령어에 `allow` 옵션을 사용하면 됩니다.

    php artisan down --allow=127.0.0.1 --allow=192.168.0.0/16

점검 모드를 해제하려면 아티즌 명령어 `up`을 실행시키면 됩니다.

    php artisan up

> {tip} `resources/views/errors/503.blade.php` 파일을 정의해서 점검모드의 응답페이지 템플릿을 커스터마이징 할 수 있습니다.

#### 점검모드 & 큐-Queue

애플리케이션이 점검 모드인 동안에는 [큐 작업](/docs/{{version}}/queues)은 동작하지 않습니다. 큐 작업들은 점검 모드가 해제된 애플리케이션이 일반 모드로 전환된 시점에서 계속 처리됩니다.

#### 점검 모드의 대안

점검 모드 동안에는 애플리케이션이 얼마동안 완전히 다운되어 있게 되므로, 라라벨의 제로-타임 배포를 위해서 [Envoyer](https://envoyer.io)같은 대안을 고려할 수도 있습니다.
