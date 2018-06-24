# Errors & Logging
# 에러와 로깅

- [Introduction](#introduction)
- [소개하기](#introduction)
- [Configuration](#configuration)
- [설정하기](#configuration)
    - [Error Detail](#error-detail)
    - [에러의 상세정보](#error-detail)
    - [Log Storage](#log-storage)
    - [로그 스토리지](#log-storage)
    - [Log Severity Levels](#log-severity-levels)
    - [Log Severity Levels](#log-severity-levels)
    - [Custom Monolog Configuration](#custom-monolog-configuration)
    - [사용자 지정 Monolog 설정](#custom-monolog-configuration)
- [The Exception Handler](#the-exception-handler)
- [Exception 핸들러](#the-exception-handler)
    - [Report Method](#report-method)
    - [Report 메소드](#report-method)
    - [Render Method](#render-method)
    - [Render 메소드](#render-method)
    - [Reportable & Renderable Exceptions](#renderable-exceptions)
    - [Reportable & Renderable Exceptions](#renderable-exceptions)
- [HTTP Exceptions](#http-exceptions)
- [HTTP Exceptions](#http-exceptions)
    - [Custom HTTP Error Pages](#custom-http-error-pages)
    - [사용자 지정 HTTP 에러 페이지](#custom-http-error-pages)
- [Logging](#logging)
- [로깅](#logging)

<a name="introduction"></a>
## Introduction
## 소개하기

When you start a new Laravel project, error and exception handling is already configured for you. The `App\Exceptions\Handler` class is where all exceptions triggered by your application are logged and then rendered back to the user. We'll dive deeper into this class throughout this documentation.

새로운 라라벨 프로젝트가 시작될 때, 기본적인 오류 및 예외 처리는 모두 설정되어 있습니다. 애플리케이션에서 발생하는 모든 예외-exceptions 들은 `App\Exceptions\Handler` 클래스에 의해서 로그를 남기고 사용자에게 응답을 보여주게 됩니다. 이 문서에서는 이 클래스에 대해서 좀 더 자세히 알아보겠습니다.

For logging, Laravel utilizes the [Monolog](https://github.com/Seldaek/monolog) library, which provides support for a variety of powerful log handlers. Laravel configures several of these handlers for you, allowing you to choose between a single log file, rotating log files, or writing error information to the system log.

라라벨은 로그를 남기기 위해서 강력하고 다양한 로그 핸들러를 지원하는 [Monolog](https://github.com/Seldaek/monolog) 라이브러리를 사용합니다. 라라벨은 하나의 파일에 로그를 남기거나, 로그 파일을 일정 주기에 따라서 바꾸는, 또는 시스템 로그에 에러를 기록하도록 하는 여러 핸들러를 설정할 수 있습니다.

<a name="configuration"></a>
## Configuration
## 설정하기

<a name="error-detail"></a>
### Error Detail
### 에러의 상세 정보

The `debug` option in your `config/app.php` configuration file determines how much information about an error is actually displayed to the user. By default, this option is set to respect the value of the `APP_DEBUG` environment variable, which is stored in your `.env` file.

`config/app.php` 설정 파일의 `debug` 옵션은 에러애 대한 얼마나 많은 정보를 사용자에게 노출할 것인지 결정합니다. 기본적으로 이 옵션은 `.env` 파일에 있는 `APP_DEBUG` 환경 변수에 따라서 결정됩니다.

For local development, you should set the `APP_DEBUG` environment variable to `true`. In your production environment, this value should always be `false`. If the value is set to `true` in production, you risk exposing sensitive configuration values to your application's end users.

로글 개발 환경에서는, `APP_DEBUG` 환경 변수 값을 `true` 로 설정해야 합니다. 실제 서비스 프로덕션 환경에서는 이 값은 `false` 로 설정되어야 합니다. 만약 실 서버 환경에서 이 값을 `true` 로 설정한다면, 애플리케이션의 민감한 환경 설정 값이 사용자에게 표시되는 위험이 있습니다. 

<a name="log-storage"></a>
### Log Storage
### 로그 스토리지

Out of the box, Laravel supports writing log information to `single` files, `daily` files, the `syslog`, and the `errorlog`. To configure which storage mechanism Laravel uses, you should modify the `log` option in your `config/app.php` configuration file. For example, if you wish to use daily log files instead of a single file, you should set the `log` value in your `app` configuration file to `daily`:

별다른 설정 없이도, 라라벨은 `single` 파일, `daily` 파일, `syslog` 그리고 `errorlog` 의 로그 모드를 지원합니다. 라라벨이 사용할 스토리지 방식을 설정하기 위해서는`config/app.php` 설정 파일의 `log` 옵션을 수정해야 합니다. 예를 들어 여러분이 하나의 로그 파일 대신에 일별로 저장되는 로그 파일을 사용하고자 한다면, `app` 설정 파일의 `log` 값을 `daily` 로 설정해야 합니다:

    'log' => 'daily'

#### Maximum Daily Log Files
#### 일별 로그 파일의 최대 기간

When using the `daily` log mode, Laravel will only retain five days of log files by default. If you want to adjust the number of retained files, you may add a `log_max_files` configuration value to your `app` configuration file:

`daily` 모드를 사용하는 경우, 라라벨은 기존적으로 5일치의 로그 파일들을 유지할 것입니다. 유지되는 파일의 갯수를 조절하고자 한다면 `app.php` 설정파일에 `log_max_files` 설정을 추가하십시오.  

    'log_max_files' => 30

<a name="log-severity-levels"></a>
### Log Severity Levels
### 로그의 표현 레벨

When using Monolog, log messages may have different levels of severity. By default, Laravel writes all log levels to storage. However, in your production environment, you may wish to configure the minimum severity that should be logged by adding the `log_level` option to your `app.php` configuration file.

Monolog 를 사용할 때 로그 메세지는 각기 다른 수준의 레벨을 가지고 있습니다. 기본적으로 라라벨은 모든 로그 레벨을 스토리지에 기록할 것입니다. 하지만, 실 서버 환경에서 최소한의 레벨을 설정하기를 원한다면, `app.php` 설정 파일에 `log_leve` 을 추가해야 합니다. 

Once this option has been configured, Laravel will log all levels greater than or equal to the specified severity. For example, a default `log_level` of `error` will log **error**, **critical**, **alert**, and **emergency** messages:

이 옵션이 설정되고 나면, 라라벨은 지정된 레벨과 동일하거나, 더 높은 레벨의 모든 로그를 기록합니다. 예를 들어 기본 `log_level`이 `error` 인 경우라면 **error**, **critical**, **alert**, 그리고 **emergency** 메세지를 기록합니다: 

    'log_level' => env('APP_LOG_LEVEL', 'error'),

> {tip} Monolog recognizes the following severity levels - from least severe to most severe: `debug`, `info`, `notice`, `warning`, `error`, `critical`, `alert`, `emergency`.

> {tip} Monolog 는 다음의 로그 레벨을 취급합니다. - 가장 낮은 레벨 부터 높은 레벨 순서 :`debug`, `info`, `notice`, `warning`, `error`, `critical`, `alert`, `emergency`.

<a name="custom-monolog-configuration"></a>
### Custom Monolog Configuration
### 사용자 지정 Monolog 설정하기

If you would like to have complete control over how Monolog is configured for your application, you may use the application's `configureMonologUsing` method. You should place a call to this method in your `bootstrap/app.php` file right before the `$app` variable is returned by the file:

만약 애플리케이션에서 Monolog 어떻게 설정할 것인지 완전히 제어하고자 한다면, 애플리케이션의 `configureMonologUsing` 메소드를 사용하면 됩니다. 이 메소드는 `bootstrap/app.php` 파일에서 `$app` 변수가 반환되기 전에 사용되어야만 합니다:

    $app->configureMonologUsing(function ($monolog) {
        $monolog->pushHandler(...);
    });

    return $app;

#### Customizing The Channel Name
#### 채널 이름 커스터마이징 하기

By default, Monolog is instantiated with name that matches the current environment, such as `production` or `local`. To change this value, add the `log_channel` option to your `app.php` configuration file:

기본적으로, Monolog는 `production` 또는 `local`과 같은 현재 환경과 일치하는 이름으로 인스턴스가 됩니다. 이 값을 변경하려면, `app.php` 설정 파일에 `log_channel` 옵션을 추가하면됩니다:

    'log_channel' => env('APP_LOG_CHANNEL', 'my-app-name'),

<a name="the-exception-handler"></a>
## The Exception Handler
## Exception 핸들러

<a name="report-method"></a>
### The Report Method
### Report 메소드

All exceptions are handled by the `App\Exceptions\Handler` class. This class contains two methods: `report` and `render`. We'll examine each of these methods in detail. The `report` method is used to log exceptions or send them to an external service like [Bugsnag](https://bugsnag.com) or [Sentry](https://github.com/getsentry/sentry-laravel). By default, the `report` method passes the exception to the base class where the exception is logged. However, you are free to log exceptions however you wish.

모든 예외-exception는 `App\Exceptions\Handler` 클래스에 의해서 처리됩니다. `report` 메소드는 예외-exceptions를 로깅하거나 [Bugsnag](https://bugsnag.com) 또는 [Sentry](https://github.com/getsentry/sentry-laravel) 같은 별도의 외부 서비스로 보내는데 사용되어 집니다. 기본적으로 `report` 메소드는 예외-exception가 기록되는 경우, 베이스 클래스로 예외-exception을 전달합니다.

For example, if you need to report different types of exceptions in different ways, you may use the PHP `instanceof` comparison operator:

예를 들어 각기 다른 유형의 예외-exception을 다른 방법 보고해야한다면, PHP의 instanceof 비교 연산자를 사용하면 됩니다.

    /**
     * Report or log an exception.
     *
     * This is a great spot to send exceptions to Sentry, Bugsnag, etc.
     *
     * @param  \Exception  $exception
     * @return void
     */
    public function report(Exception $exception)
    {
        if ($exception instanceof CustomException) {
            //
        }

        return parent::report($exception);
    }

#### The `report` Helper
#### `report` 헬퍼 함수

Sometimes you may need to report an exception but continue handling the current request. The `report` helper function allows you to quickly report an exception using your exception handler's `report` method without rendering an error page:

때때로, 현재 발생한 exception-예외를 report-보고해야 하지만, 현재 request 처리를 계속하기를 원할 수도 있습니다. `report` 헬퍼 함수는 에러 페이지를 렌더링 하지 않으면서, exception 핸들러의 `report` 메소드를 사용하여 보다 빠르게 예외사항을 보고합니다:

    public function isValid($value)
    {
        try {
            // Validate the value...
        } catch (Exception $e) {
            report($e);

            return false;
        }
    }

#### Ignoring Exceptions By Type
#### 유형에 따라서 예외-exception 무시하기

The `$dontReport` property of the exception handler contains an array of exception types that will not be logged. For example, exceptions resulting from 404 errors, as well as several other types of errors, are not written to your log files. You may add other exception types to this array as needed:

Exception 핸들러의 `$dontReport` 속성 배열에 포함되어 있는 유형의 예외-exception 들은 로그에 기록되지 않습니다. 예를 들어 404 에러뿐만 아니라 다른 유형의 몇몇 에러들은 로그파일에 기록되지 않습니다. 필요한 경우에 다른 예외-exception 유형들도 이 배열에 추가할 수 있습니다.

    /**
     * A list of the exception types that should not be reported.
     *
     * @var array
     */
    protected $dontReport = [
        \Illuminate\Auth\AuthenticationException::class,
        \Illuminate\Auth\Access\AuthorizationException::class,
        \Symfony\Component\HttpKernel\Exception\HttpException::class,
        \Illuminate\Database\Eloquent\ModelNotFoundException::class,
        \Illuminate\Validation\ValidationException::class,
    ];

<a name="render-method"></a>
### The Render Method
### Render 메소드

The `render` method is responsible for converting a given exception into an HTTP response that should be sent back to the browser. By default, the exception is passed to the base class which generates a response for you. However, you are free to check the exception type or return your own custom response:

`render` 메소드는 주어진 예외-exception를 HTTP 응답으로 변환하고 브라우저에게 보내는 역할을 담당합니다. 기본적으로 예외-exception는 응답을 생성하는 기본 클래스로 전달됩니다. 하지만 예외-exception의 유형을 파악하고 사용자 지정된 응답을 반환 것도 여러분의 자유입니다.

    /**
     * Render an exception into an HTTP response.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Exception  $exception
     * @return \Illuminate\Http\Response
     */
    public function render($request, Exception $exception)
    {
        if ($exception instanceof CustomException) {
            return response()->view('errors.custom', [], 500);
        }

        return parent::render($request, $exception);
    }

<a name="renderable-exceptions"></a>
### Reportable & Renderable Exceptions
### Reportable & Renderable Exceptions

Instead of type-checking exceptions in the exception handler's `report` and `render` methods, you may define `report` and `render` methods directly on your custom exception. When these methods exist, they will be called automatically by the framework:

Exception 핸들러의 `report` 그리고 `render` 메소드에서 exception의 타입을 확인하는 대신에 여러분이 정의한 고유한 exception에 `report` 와 `render` 메소드르르 정의할 수 있습니다. 이 메소드가 존재한다면 프레임워크는 자동으로 이 메소드를 호출합니다:

    <?php

    namespace App\Exceptions;

    use Exception;

    class RenderException extends Exception
    {
        /**
         * Report the exception.
         *
         * @return void
         */
        public function report()
        {
            //
        }

        /**
         * Render the exception into an HTTP response.
         *
         * @param  \Illuminate\Http\Request
         * @return \Illuminate\Http\Response
         */
        public function render($request)
        {
            return response(...);
        }
    }

<a name="http-exceptions"></a>
## HTTP Exceptions
## HTTP 예외-exception

Some exceptions describe HTTP error codes from the server. For example, this may be a "page not found" error (404), an "unauthorized error" (401) or even a developer generated 500 error. In order to generate such a response from anywhere in your application, you may use the `abort` helper:

어떤 예외들-exceptions은 서버에서 발생하는 HTTP 에러 코드를 나타냅니다. 예를 들어, "페이지를 찾을 수 없습니다." 에러(404)나, 인증 오류 (401) 또는 개발자가 유발하는 500 오류 같은 것들입니다. 애플리케이션의 어느 곳에서든 이 같은 응답을 반환하려면 `abort` 헬퍼 함수를 사용하면 됩니다:

    abort(404);

The `abort` helper will immediately raise an exception which will be rendered by the exception handler. Optionally, you may provide the response text:

`abort` 헬퍼함수는 즉시 예외-exception을 발생시키고, 그 예외는 Exception 핸들러에 의해 렌더링될 것입니다. 선택적으로 응답의 텍스트를 지정할 수도 있습니다.

    abort(403, 'Unauthorized action.');

<a name="custom-http-error-pages"></a>
### Custom HTTP Error Pages
### 사용자 지정 HTTP 에러 페이지

Laravel makes it easy to display custom error pages for various HTTP status codes. For example, if you wish to customize the error page for 404 HTTP status codes, create a `resources/views/errors/404.blade.php`. This file will be served on all 404 errors generated by your application. The views within this directory should be named to match the HTTP status code they correspond to. The `HttpException` instance raised by the `abort` function will be passed to the view as an `$exception` variable:

라라벨에서는 HTTP 응답 코드에 따른 다양한 사용자 지정 에러 페이지를 표시할 수 있습니다. 예를 들어 404 HTTP 응답 코드에 대한 에러 페이지를 수정하고자 한다면, `resources/views/errors/404.blade.php` 파일을 생성하면 됩니다. 이 파일은 애플리케이션에서 404 에러가 발생했을 때 사용자에게 보여질 것입니다. 이 디렉토리안에 있는 뷰의 이름은 해당 HTTP 상태 코드와 일치해야 합니다. `abort` 함수에 의해서 발생한 `HttpException` 인스턴스는 뷰에 `$exception`변수로 전달될 것입니다.

    <h2>{{ $exception->getMessage() }}</h2>

<a name="logging"></a>
## Logging
## 로깅

Laravel provides a simple abstraction layer on top of the powerful [Monolog](https://github.com/seldaek/monolog) library. By default, Laravel is configured to create a log file for your application in the `storage/logs` directory. You may write information to the logs using the `Log` [facade](/docs/{{version}}/facades):

라라벨은 강력한 [Monolog](https://github.com/seldaek/monolog) 라이브러리 위에 간단한 추상화 레이어를 제공합니다. 기본적으로 라라벨은 `storage/logs` 디렉토리에 하나의 로그 파일을 생성하도록 설정되어 있습니다. `Log` [파사드](/docs/{{version}}/facades)를 사용하여, 로그 파일에 정보를 기록할 수도 있습니다:

    <?php

    namespace App\Http\Controllers;

    use App\User;
    use Illuminate\Support\Facades\Log;
    use App\Http\Controllers\Controller;

    class UserController extends Controller
    {
        /**
         * Show the profile for the given user.
         *
         * @param  int  $id
         * @return Response
         */
        public function showProfile($id)
        {
            Log::info('Showing user profile for user: '.$id);

            return view('user.profile', ['user' => User::findOrFail($id)]);
        }
    }

The logger provides the eight logging levels defined in [RFC 5424](https://tools.ietf.org/html/rfc5424): **emergency**, **alert**, **critical**, **error**, **warning**, **notice**, **info** and **debug**.

로그는 [RFC 5424](https://tools.ietf.org/html/rfc5424) 에 정의되어있는 **emergency**, **alert**, **critical**, **error**, **warning**, **notice**, **info** 그리고 **debug**의 8가지 로깅 레벨을 제공합니다.

    Log::emergency($message);
    Log::alert($message);
    Log::critical($message);
    Log::error($message);
    Log::warning($message);
    Log::notice($message);
    Log::info($message);
    Log::debug($message);

#### Contextual Information
#### 컨텍스트 정보

An array of contextual data may also be passed to the log methods. This contextual data will be formatted and displayed with the log message:

컨텍스트 데이터는 로그 메소드에 배열로 전달될 수 있습니다. 이 컨텍스트 데이터는 로그 메세지에 포맷에 맞게 표시될 것입니다. 

    Log::info('User failed to login.', ['id' => $user->id]);

#### Accessing The Underlying Monolog Instance
#### 기반이 되는 모노로그 인스턴스에 엑세스하기

Monolog has a variety of additional handlers you may use for logging. If needed, you may access the underlying Monolog instance being used by Laravel:

Monolog에는 로그에 사용할 수있는 기타 다양한 핸들러가 준비되어 있습니다. 필요하다면 라라벨이 내부에서 사용하는 Monolog 인스턴스에 액세스 할 수 있습니다.

    $monolog = Log::getMonolog();
