# Configuration
# 설정

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Environment Configuration](#environment-configuration)
- [구동환경 설정](#environment-configuration)
    - [Environment Variable Types](#environment-variable-types)
    - [환경 변수의 타입](#environment-variable-types)
    - [Retrieving Environment Configuration](#retrieving-environment-configuration)
    - [구동환경 조회하기](#retrieving-environment-configuration)
    - [Determining The Current Environment](#determining-the-current-environment)
    - [현재 구동환경 결정하기](#determining-the-current-environment)
- [Accessing Configuration Values](#accessing-configuration-values)
- [설정 값에 엑세스 하기](#accessing-configuration-values)
- [Configuration Caching](#configuration-caching)
- [설정 캐시](#configuration-caching)
- [Debug Mode](#debug-mode)
- [디버그 모드](#debug-mode)
- [Maintenance Mode](#maintenance-mode)
- [점검(공사중) 모드](#maintenance-mode)

<a name="introduction"></a>
## Introduction
## 시작하기

All of the configuration files for the Laravel framework are stored in the `config` directory. Each option is documented, so feel free to look through the files and get familiar with the options available to you.

라라벨의 모든 설정 파일은 `config` 디렉토리에 저장됩니다. 모든 파일에는 개별 옵션에 대한 설명이 들어 있으니 해당 옵션에 대해 이해하는데 도움을 받을 수 있습니다.

These configuration files allow you to configure things like your database connection information, your mail server information, as well as various other core configuration values such as your application timezone and encryption key.

이러한 구성 파일을 사용하면 데이터베이스 연결 정보, 메일 서버 정보는 물론 애플리케이션 시간대 및 암호화 키와 같은 다양한 기타 핵심 구성 값을 구성할 수 있습니다.

<a name="environment-configuration"></a>
## Environment Configuration
## 구동환경 설정

It is often helpful to have different configuration values based on the environment where the application is running. For example, you may wish to use a different cache driver locally than you do on your production server.

프로그램이 실행되는 구동환경에따라 다른 설정값을 사용할 수 있으면 많은 이점이 있습니다. 예를 들어 실제 제품 서버와 로컬 개발 서버에 각각 다른 캐시 드라이버를 사용하려고 하는 경우가 그렇습니다.

To make this a cinch, Laravel utilizes the [DotEnv](https://github.com/vlucas/phpdotenv) PHP library. In a fresh Laravel installation, the root directory of your application will contain a .env.example file that defines many common environment variables. During the Laravel installation process, this file will automatically be copied to .env.

이것을 간단하게 만들기 위해 라라벨은 [DotEnv](https://github.com/vlucas/phpdotenv) PHP 라이브러리를 사용합니다. 새롭게 라라벨을 인스톨 한 경우 애플리케이션의 루트 디렉토리에는 많은 공통 환경 변수를 정의하는 .env.example 파일이 포함됩니다. 라라벨 설치 과정에서 이 파일은 자동으로 .env에 복사됩니다.

Laravel's default `.env` file contains some common configuration values that may differ based on whether your application is running locally or on a production web server. These values are then retrieved from various Laravel configuration files within the `config` directory using Laravel's `env` function.

라라벨의 기본 `.env` 파일에는 프로그램이 로컬 개발 서버에서 실행 중인지 아니면 실제 제품 서버에서 실행 중인지에 따라 다를 수 있는 몇 가지 공통 구성 값이 포함되어 있습니다. 이 값은 라라벨의 `env` 기능을 사용하여 `config` 디렉토리 내의 다양한 라라벨 설정 파일에서 쓰입니다.

If you are developing with a team, you may wish to continue including a `.env.example` file with your application. By putting placeholder values in the example configuration file, other developers on your team can clearly see which environment variables are needed to run your application. You may also create a `.env.testing` file. This file will override the `.env` file when running PHPUnit tests or executing Artisan commands with the `--env=testing` option.

팀으로 개발을 하는 경우라면 `.env.example` 파일을 애플리케이션에 포함할 수 있습니다. example 설정 파일에 적절한 기본값을 넣어두면 다른 개발자들이 애플리케이션을 실행하는 데 어떤 환경 변수 설정이 필요한지 명확하게 이해할 수 있습니다. 또한 `.env.testing` 파일을 생성할 수 있습니다. 이 파일은 PHPUnit 테스트나 아티즌 명령어가 `--env=testing` 옵션과 함께 실행될 때 `.env`를 오버라이드 합니다.

> {tip} Any variable in your `.env` file can be overridden by external environment variables such as server-level or system-level environment variables.

> {tip} `.env` 파일의 어떤 변수는 서버의 또는 시스템의 환경 변수와 같은 외부 환경 변수에 의해서 무시될 수도 있습니다.

<a name="environment-file-security"></a>
#### Environment File Security
#### env 파일 보안

Your `.env` file should not be committed to your application's source control, since each developer / server using your application could require a different environment configuration. Furthermore, this would be a security risk in the event an intruder gains access to your source control repository, since any sensitive credentials would get exposed.

개별 개발자와 서버에서 애플리케이션별로 다른 구동 환경 설정을 필요로 하기 때문에, `.env` 파일을 애플리케이션의 소스 컨트롤 시스템에 커밋해서는 안 됩니다. 이는 공격자가 소스 컨트롤 저장소에 엑세스 권한을 얻게 되는 경우에, 민감한 계정정보가 노출될 위험이 있어 보안 취약점이 될 수도 있습니다.

<a name="additional-environment-files"></a>
#### Additional Environment Files
#### 추가 환경 파일

Before loading your application's environment variables, Laravel determines if either the `APP_ENV` environment variable has been externally provided or if the `--env` CLI argument has been specified. If so, Laravel will attempt to load an `.env.[APP_ENV]` file if it exists. If it does not exist, the default `.env` file will be loaded.

프로그램의 환경 변수를 로드하기 전에 라라벨은 `APP_ENV` 환경 변수가 외부에서 제공되었는지 또는 `--env` CLI 인수가 지정되었는지 확인합니다. 만약 CLI 인수가 지정되었다면 라라벨은 `.env.[APP_ENV]` 파일이 존재하는 경우 로드를 시도합니다. 존재하지 않으면 기본 `.env` 파일이 로드됩니다.

<a name="environment-variable-types"></a>
### Environment Variable Types
### 환경 변수의 타입

All variables in your `.env` files are typically parsed as strings, so some reserved values have been created to allow you to return a wider range of types from the `env()` function:

`.env` 파일의 모든 변수들은 일반적으로 문자열로 파싱되기 때문에, `env()` 함수에서 더 넓은 범위의 유형을 반환할 수 있도록 일부 예약된 값이 추가되었습니다.

| `.env` Value | `env()` Value |
|--------------|---------------|
| true         | (bool) true   |
| (true)       | (bool) true   |
| false        | (bool) false  |
| (false)      | (bool) false  |
| empty        | (string) ''   |
| (empty)      | (string) ''   |
| null         | (null) null   |
| (null)       | (null) null   |

If you need to define an environment variable with a value that contains spaces, you may do so by enclosing the value in double quotes.

공백이 포함 된 값으로 환경 변수를 정의해야하는 경우 값을 쌍따옴표로 묶어 환경 변수를 정의 할 수 있습니다.

```ini
APP_NAME="My Application"
```

<a name="retrieving-environment-configuration"></a>
### Retrieving Environment Configuration
### 구동환경 조회하기

All of the variables listed in this file will be loaded into the `$_ENV` PHP super-global when your application receives a request. However, you may use the `env` helper to retrieve values from these variables in your configuration files. In fact, if you review the Laravel configuration files, you will notice many of the options are already using this helper:

이 파일의 목록에 있는 모든 변수는 애플리케이션이 요청을 받을 때 `$_ENV` PHP 슈퍼 전역변수에 로드됩니다. `env` 헬퍼 함수를 통해서 설정 파일안에 있는 이러한 변수값들을 조회 할 수 있습니다. 만약 여러분이 라라벨 설정 파일들을 살펴보았다면 이미 여러 옵션 설정에 헬퍼 함수가 사용되었다는 것을 알 수 있을 것입니다!

    'debug' => env('APP_DEBUG', false),

The second value passed to the `env` function is the "default value". This value will be returned if no environment variable exists for the given key.

`env` 함수에 전달된 두 번째 인자는 "기본값"입니다. 주어진 키에 대한 환경 변수가 없으면 이 값이 반환됩니다.

<a name="determining-the-current-environment"></a>
### Determining The Current Environment
### 현재 구동환경 결정하기

The current application environment is determined via the `APP_ENV` variable from your `.env` file. You may access this value via the `environment` method on the `App` [facade](/docs/{{version}}/facades):

현재의 애플리케이션 구동 환경은 `.env` 파일의 `APP_ENV` 변수를 통해서 결정됩니다. `App` [파사드](/docs/{{version}}/facades)의 `environment` 메소드를 통해서 이 값에 접근 할 수 있습니다.

    use Illuminate\Support\Facades\App;

    $environment = App::environment();

You may also pass arguments to the `environment` method to determine if the environment matches a given value. The method will return `true` if the environment matches any of the given values:

또한 `environment` 메소드에 확인하고자 하는 구동 환경에 대한 값을 인자로 전달할 수도 있습니다. 메소드는 주어진 값 중 하나와 일치하는 경우 `true`를 반환할 것입니다.

    if (App::environment('local')) {
        // The environment is local
    }

    if (App::environment(['local', 'staging'])) {
        // The environment is either local OR staging...
    }

> {tip} The current application environment detection can be overridden by defining a server-level `APP_ENV` environment variable.
> {tip} 현재 애플리케이션의 구동 환경은 서버의 'APP_ENV' 환경 변수를 정의하여 재정의할 수 있습니다.

<a name="accessing-configuration-values"></a>
## Accessing Configuration Values
## 설정 값에 엑세스 하기

You may easily access your configuration values using the global `config` helper function from anywhere in your application. The configuration values may be accessed using "dot" syntax, which includes the name of the file and option you wish to access. A default value may also be specified and will be returned if the configuration option does not exist:

애플리케이션의 어디에서라도 `config` 헬퍼 함수를 사용하여 손쉽게 설정 값에 엑세스 할 수 있습니다. 설정된 값은 파일의 이름과 엑세스 하고자 하는 옵션에 대해서, ".(점)" 문법을 사용하여 엑세스 할 수 있습니다. 설정된 옵션값이 존재하지 않았을 때 반환될 기본값을 지정할 수 있습니다.

    $value = config('app.timezone');

    // Retrieve a default value if the configuration value does not exist...
    $value = config('app.timezone', 'Asia/Seoul');

To set configuration values at runtime, pass an array to the `config` helper:

애플리케이션의 실행중에 설정값을 변경하려면 `config` 헬퍼에 배열을 전달하십시오:

    config(['app.timezone' => 'America/Chicago']);

<a name="configuration-caching"></a>
## Configuration Caching
## 설정 캐시

To give your application a speed boost, you should cache all of your configuration files into a single file using the `config:cache` Artisan command. This will combine all of the configuration options for your application into a single file which can be quickly loaded by the framework.

애플리케이션의 구동 속도를 높이려면 `config:cache` 아티즌 명령어를 통해서 설정 파일들을 하나로 묶어 캐싱해야 합니다. 이 명령어는 모든 설정 옵션을 하나의 파일로 묶어 애플리케이션이 빠르게 로드될 수 있게 해줍니다.

You should typically run the `php artisan config:cache` command as part of your production deployment process. The command should not be run during local development as configuration options will frequently need to be changed during the course of your application's development.

일반적으로 `php artisan config:cache` 명령어를 애플리케이션 배포 프로세스의 일부에서 실행하도록 해야 합니다. 애플리케이션 개발 중에는 설정 옵션값이 자주 바뀔 필요가 있기 때문에, 로컬 개발 환경에서는 이 명령어를 실행하지 말아야 합니다.

> {note} If you execute the `config:cache` command during your deployment process, you should be sure that you are only calling the `env` function from within your configuration files. Once the configuration has been cached, the `.env` file will not be loaded; therefore, the `env` function will only return external, system level environment variables.
> {note} 배포가 진행 되는 도중에 `config:cache` 명령어를 실행한다면, 설정 파일안에서 `env` 함수만 호출된다는 것을 확인해야 합니다. 한번, 설정이 캐싱되고 나면, `.env` 파일은 로딩되지 않고, 모든 `env` 함수는 `null`을 반환합니다.

<a name="debug-mode"></a>
## Debug Mode
## 디버그 모드

The `debug` option in your `config/app.php` configuration file determines how much information about an error is actually displayed to the user. By default, this option is set to respect the value of the `APP_DEBUG` environment variable, which is stored in your `.env` file.

`config/app.php` 파일의 `debug` 옵션은 사용자에게 실제로 표시되는 오류 정보의 양을 결정합니다. 기본적으로 이 옵션은 `.env` 파일에 저장된 `APP_DEBUG` 환경 변수의 값을 따르도록 설정됩니다.

For local development, you should set the `APP_DEBUG` environment variable to `true`. **In your production environment, this value should always be `false`. If the variable is set to `true` in production, you risk exposing sensitive configuration values to your application's end users.**

로컬 개발의 경우 `APP_DEBUG` 환경 변수를 `true`로 설정해야 합니다. 프로덕션 환경에서 이 값은 항상 `false`여야 합니다. 프로덕션에서 `APP_DEBUG`값이 `true`로 설정되면 민감한 설정 값이 프로그램의 최종 사용자에게 노출될 위험이 있습니다.

<a name="maintenance-mode"></a>
## Maintenance Mode
## 점검(공사중) 모드

When your application is in maintenance mode, a custom view will be displayed for all requests into your application. This makes it easy to "disable" your application while it is updating or when you are performing maintenance. A maintenance mode check is included in the default middleware stack for your application. If the application is in maintenance mode, a `Symfony\Component\HttpKernel\Exception\HttpException` instance will be thrown with a status code of 503.

애플리케이션이 점검 모드일 경우 애플리케이션에서 지정한 모든 라우팅이 특정한 화면으로 보여지게 됩니다. 따라서 애플리케이션을 업데이트 하거나 튜닝하는 중에 손쉽게 "사용 정지"시킬 수 있습니다. 점검 모드를 확인하는 부분은 애플리케이션의 미들웨어 스택에 기본적으로 포함되어 있습니다. 만약 애플리케이션이 점검 모드에 있다면 503 상태 코드와 함께 `Symfony\Component\HttpKernel\Exception\HttpException`이 발생합니다.

To enable maintenance mode, execute the `down` Artisan command:

점검 모드를 활성화 하려면 아티즌 명령어 `down`을 실행시키면 됩니다.

```shell
php artisan down
```

If you would like the `Refresh` HTTP header to be sent with all maintenance mode responses, you may provide the `refresh` option when invoking the `down` command. The `Refresh` header will instruct the browser to automatically refresh the page after the specified number of seconds:

모든 유지보수 모드 응답과 함께 `Refresh` HTTP 헤더를 보내려면 `down` 명령을 호출할 때 `refresh` 옵션을 제공할 수 있습니다. '새로 고침' 헤더는 지정된 시간(초) 후에 페이지를 자동으로 새로 고치도록 브라우저에 지시합니다.

```shell
php artisan down --refresh=15
```

You may also provide a `retry` option to the `down` command, which will be set as the `Retry-After` HTTP header's value, although browsers generally ignore this header:

또한 retry 옵션을 down 명령에 제공할 수 있으며, 이 옵션은 HTTP 헤더에 Retry-After 값을 추가합니다.

```shell
php artisan down --retry=60
```

<a name="bypassing-maintenance-mode"></a>
#### Bypassing Maintenance Mode
#### 점검 모드 우회

Even while in maintenance mode, you may use the `secret` option to specify a maintenance mode bypass token:

점검 모드인 경우에도 secret 옵션을 사용하여 점검 모드 우회 토큰을 지정할 수 있습니다.

```shell
php artisan down --secret="1630542a-246b-4b66-afa1-dd72a4c43515"
```

After placing the application in maintenance mode, you may navigate to the application URL matching this token and Laravel will issue a maintenance mode bypass cookie to your browser:

애플리케이션을 점검 모드로 설정한 후 이 토큰과 일치하는 애플리케이션 URL로 이동하면 Laravel이 브라우저에 점검 모드 우회 쿠키를 발행합니다.

```shell
https://example.com/1630542a-246b-4b66-afa1-dd72a4c43515
```

When accessing this hidden route, you will then be redirected to the `/` route of the application. Once the cookie has been issued to your browser, you will be able to browse the application normally as if it was not in maintenance mode.

이 숨겨진 경로에 엑세스하면 애플리케이션의 / 경로로 리디렉션 될 것입니다. 쿠키가 브라우저에 발급되면 점검 모드가 아닌 것처럼 정상적으로 애플리케이션을 탐색할 수 있습니다.

> {tip} Your maintenance mode secret should typically consist of alpha-numeric characters and, optionally, dashes. You should avoid using characters that have special meaning in URLs such as `?`.
> {tip} 점검 모드 암호는 일반적으로 영숫자 및 선택적으로 대시로 구성되어야 합니다. URL에서 `?`와 같은 특별한 의미를 갖는 문자는 사용하지 않아야 합니다.

<a name="pre-rendering-the-maintenance-mode-view"></a>
#### Pre-Rendering The Maintenance Mode View
#### 점검 모드 사전 렌더링 View

If you utilize the `php artisan down` command during deployment, your users may still occasionally encounter errors if they access the application while your Composer dependencies or other infrastructure components are updating. This occurs because a significant part of the Laravel framework must boot in order to determine your application is in maintenance mode and render the maintenance mode view using the templating engine.

배포 중에 php artisan down 명령을 사용하는 경우 Composer 종속성 또는 기타 인프라 구성 요소가 업데이트되는 동안 사용자가 애플리케이션에 액세스하면 오류가 발생할 수 있습니다. 이는 애플리케이션이 점검 모드에 있는지 확인하고 템플릿 엔진을 사용하여 점검 모드 view를 렌더링하기 위해 Laravel 프레임워크의 상당 부분 부팅되어야하기 때문에 발생합니다.

For this reason, Laravel allows you to pre-render a maintenance mode view that will be returned at the very beginning of the request cycle. This view is rendered before any of your application's dependencies have loaded. You may pre-render a template of your choice using the `down` command's `render` option:

이러한 이유로 Laravel은 요청 사이클의 시작 부분에 반환 될 점검 모드 view를 미리 렌더링할 수 있습니다. 이 view는 애플리케이션의 종속성이 로드되기 전에 렌더링됩니다. down 명령의 렌더 옵션을 사용하여 선택한 템플릿을 미리 렌더링할 수 있습니다.

```shell
php artisan down --render="errors::503"
```

<a name="redirecting-maintenance-mode-requests"></a>
#### Redirecting Maintenance Mode Requests
#### 점검 모드 요청 리디렉션

While in maintenance mode, Laravel will display the maintenance mode view for all application URLs the user attempts to access. If you wish, you may instruct Laravel to redirect all requests to a specific URL. This may be accomplished using the `redirect` option. For example, you may wish to redirect all requests to the `/` URI:

점검 모드에 있는 동안 Laravel은 사용자가 액세스하려는 모든 애플리케이션 URL에 대한 점검 view를 표시합니다. 원하는 경우 Laravel에 모든 요청을 특정 URL로 리디렉션하도록 지시할 수 있습니다. 리디렉션 옵션을 사용하여 이 작업을 수행할 수 있습니다. 예를 들어 모든 요청을 / URI로 리디렉션할 수 있습니다.

```shell
php artisan down --redirect=/
```

<a name="disabling-maintenance-mode"></a>
#### Disabling Maintenance Mode
#### 점검모드 비활성화

To disable maintenance mode, use the `up` command:

점검 모드를 해제하려면 아티즌 명령어 `up`을 실행시키면 됩니다.

```shell
php artisan up
```

> {tip} You may customize the default maintenance mode template by defining your own template at `resources/views/errors/503.blade.php`.

> {tip} `resources/views/errors/503.blade.php` 파일을 정의해서 점검모드의 응답페이지 템플릿을 커스터마이징 할 수 있습니다.

<a name="maintenance-mode-queues"></a>
#### Maintenance Mode & Queues
#### 점검모드 & 큐-Queue

While your application is in maintenance mode, no [queued jobs](/docs/{{version}}/queues) will be handled. The jobs will continue to be handled as normal once the application is out of maintenance mode.

애플리케이션이 점검 모드인 동안에는 [큐 작업](/docs/{{version}}/queues)은 동작하지 않습니다. 큐 작업들은 점검 모드가 해제된 애플리케이션이 일반 모드로 전환된 시점에서 계속 처리됩니다.

<a name="alternatives-to-maintenance-mode"></a>
#### Alternatives To Maintenance Mode
#### 점검 모드의 대안

Since maintenance mode requires your application to have several seconds of downtime, consider alternatives like [Envoyer](https://envoyer.io) to accomplish zero-downtime deployment with Laravel.

점검 모드 동안에는 애플리케이션이 얼마동안 완전히 다운되어 있게 되므로, 라라벨의 제로-타임 배포를 위해서 [Envoyer](https://envoyer.io)같은 대안을 고려할 수도 있습니다.
