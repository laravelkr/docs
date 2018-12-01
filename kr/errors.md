# Errors & Logging
# 에러와 로깅

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Configuration](#configuration)
- [설정하기](#configuration)
- [The Exception Handler](#the-exception-handler)
- [Exception 핸들러](#the-exception-handler)
    - [Report Method](#report-method)
    - [Report 메소드](#report-method)
    - [Render Method](#render-method)
    - [Render 메소드](#render-method)
- [HTTP Exceptions](#http-exceptions)
- [HTTP Exceptions](#http-exceptions)
    - [Custom HTTP Error Pages](#custom-http-error-pages)
    - [사용자 지정 HTTP 에러 페이지](#custom-http-error-pages)
- [Logging](#logging)
- [로깅](#logging)

<a name="introduction"></a>
## Introduction
## 시작하기

When you start a new Laravel project, error and exception handling is already configured for you. In addition, Laravel is integrated with the [Monolog](https://github.com/Seldaek/monolog) logging library, which provides support for a variety of powerful log handlers.

새로운 라라벨 프로젝트가 시작되는 시점에서, 오류 및 예외 처리는 모두 설정되어 있습니다. 게다가 라라벨은 강력하고 다양한 로그 핸들러를 지원하는 [Monolog](https://github.com/Seldaek/monolog) 로그 라이브러리가 통합되어 있습니다. 

<a name="configuration"></a>
## Configuration
## 설정하기

#### Error Detail
#### 에러의 상세 정보

The amount of error detail your application displays through the browser is controlled by the `debug` configuration option in your `config/app.php` configuration file. By default, this configuration option is set to respect the `APP_DEBUG` environment variable, which is stored in your `.env` file.

`config/app.php` 설정 파일의 `app.debug` 설정 옵션을 통해서 브라우저에 출력되는 애플리케이션의 오류 정보를 제어 할 수 있습니다. 기본적으로 이 옵션은 `.env` 파일에 설정되어있는 `APP_DEBUG` 환경 변수를 반영하도록 설정되어 있습니다.

For local development, you should set the `APP_DEBUG` environment variable to `true`. In your production environment, this value should always be `false`.

로컬 개발환경을 구성중이라면 `APP_DEBUG` 환경 변수를 `true`로 구성해야 할것입니다. 실제 제품 구동 환경이라면, 이 값은 항상 `false`가 되어야 합니다.

#### Log Modes
#### 로그 모드

Out of the box, Laravel supports `single`, `daily`, `syslog` and `errorlog` logging modes. For example, if you wish to use daily log files instead of a single file, you should simply set the `log` value in your `config/app.php` configuration file:

별다른 설정 없이도, 라라벨은 `single`, `dayly`, `syslog` 그리고 `errorlog` 의 로그 모드를 지원합니다. 예를 들어 여러분이 하나의 로그 파일 대신에 일별로 저장되는 로그파일을 사용하고자 한다면 `config/app.php` 설정 파일의 `log` 값을 다음과 같이 설정하면 됩니다. 

    'log' => 'daily'
    
When using the `daily` log mode, Laravel will only retain five days of log files by default. If you want to adjust the number of retained files, you may add an optional `log_max_files` configuration value to your `app.php` configuration file:

`daily` 모드를 사용하는 경우, 라라벨은 기존적으로 5일치의 로그 파일들을 유지할 것입니다. 유지되는 파일의 갯수를 조절하고자 한다면 `app.php` 설정파일에 `log_max_files` 설정을 추가하십시오.  

    'log_max_files' => 30

#### Custom Monolog Configuration
#### 사용자 지정 Monolog 설정하기

If you would like to have complete control over how Monolog is configured for your application, you may use the application's `configureMonologUsing` method. You should place a call to this method in your `bootstrap/app.php` file right before the `$app` variable is returned by the file:

여러분이 애플리케이션에서 Monolog가 설정된 내용을 완전히 제어하기를 원한다면, 애플리케이션의 `configureMonologUsing` 메소드를 사용하면 됩니다. `bootstrap/app.php` 파일에서 `$app` 변수가 반환되기 전에 이 메소드를 호출 해야 합니다.

    $app->configureMonologUsing(function($monolog) {
        $monolog->pushHandler(...);
    });

    return $app;
    
By default, Laravel writes all log levels. In your production environment, you may wish to configure the default log level by adding the `log_level` option to your `app.php` configuration file. Laravel will then log all levels greater than or equal to the specified severity level. For example, a default `log_level` of `error` will log **error**, **critical**, **alert**, and **emergency** messages:

기본적으로, 라라벨은 모든 로그 레벨을 기록합니다. `app.php` 설정 파일에 `log_level` 옵션을 추가함으로써, 실서버 환경에서의 기본 로그 레벨을 설정할 수 있습니다. 라라벨은 지정된 로그레벨 수준 이상인 모든 로그를 기록합니다. 예를 들어 기본 `log_level` 이 `error` 인경우라면  **error**, **critical**, **alert**, 그리고 **emergency** 메세지를 기록합니다: 

    'log_level' => env('APP_LOG_LEVEL', 'debug'),

<a name="the-exception-handler"></a>
## The Exception Handler
## Exception 핸들러

All exceptions are handled by the `App\Exceptions\Handler` class. This class contains two methods: `report` and `render`. We'll examine each of these methods in detail.

모든 예외들은 `App\Exceptions\Handler` 클래스에 의해서 처리됩니다. 이 클래스는 `report` 과 `render` 두 메소드를 포함하고 있습니다. 두 메소드를 조금 더 자세히 살펴보겠습니다.

<a name="report-method"></a>
### The Report Method
### Report 메소드

The `report` method is used to log exceptions or send them to an external service like [BugSnag](https://bugsnag.com) or [Sentry](https://github.com/getsentry/sentry-laravel). By default, the `report` method simply passes the exception to the base class where the exception is logged. However, you are free to log exceptions however you wish.

`report` 메소드는 예외-exceptions를 로깅하거나 [BugSnag](https://bugsnag.com) 또는 [Sentry](https://github.com/getsentry/sentry-laravel) 같은 별도의 외부 서비스로 보내는데 사용되어 집니다. 기본적으로 `report` 메소드는 예외-exception가 기록되는 경우, 베이스 클래스로 예외-exception을 전달합니다. 

For example, if you need to report different types of exceptions in different ways, you may use the PHP `instanceof` comparison operator:

예를 들어 각기 다른 유형의 예외-exception을 다른 방법 보고해야한다면, PHP의 instanceof 비교 연산자를 사용하면 됩니다.

    /**
     * Report or log an exception.
     *
     * This is a great spot to send exceptions to Sentry, Bugsnag, etc.
     *
     * @param  \Exception  $e
     * @return void
     */
    public function report(Exception $e)
    {
        if ($e instanceof CustomException) {
            //
        }

        return parent::report($e);
    }

#### Ignoring Exceptions By Type
#### 유형에 따라서 예외-exception 무시하기

The `$dontReport` property of the exception handler contains an array of exception types that will not be logged. By default, exceptions resulting from 404 errors are not written to your log files. You may add other exception types to this array as needed.

Exception 핸들러의 `$dontReport` 속성에 포함되어 있는 유형의 예외-exception 들은 로그에 기록되지 않습니다. 기본적으로 404 에러는 로그파일에 기록되지 않습니다. 필요한 경우에 다른 예외-exception 유형들도 이 배열에 추가할 수 있습니다. 

<a name="render-method"></a>
### The Render Method
### Render 메소드

The `render` method is responsible for converting a given exception into an HTTP response that should be sent back to the browser. By default, the exception is passed to the base class which generates a response for you. However, you are free to check the exception type or return your own custom response:

`render` 메소드는 주어진 예외-exception를 HTTP 응답으로 변환하고 브라우저에게 보내는 역할을 담당합니다. 기본적으로 예외-exception는 응답을 생성하는 기본 클래스로 전달됩니다. 하지만 예외-exception의 유형을 파악하고 사용자 지정된 응답을 반환 것도 여러분의 자유입니다.

    /**
     * Render an exception into an HTTP response.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Exception  $e
     * @return \Illuminate\Http\Response
     */
    public function render($request, Exception $e)
    {
        if ($e instanceof CustomException) {
            return response()->view('errors.custom', [], 500);
        }

        return parent::render($request, $e);
    }

<a name="http-exceptions"></a>
## HTTP Exceptions
## HTTP 예외-exception

Some exceptions describe HTTP error codes from the server. For example, this may be a "page not found" error (404), an "unauthorized error" (401) or even a developer generated 500 error. In order to generate such a response from anywhere in your application, use the following:

어떤 예외들-exceptions은 서버에서 발생하는 HTTP 에러 코드를 나타냅니다. 예를 들어, "페이지를 찾을 수 없습니다." 에러(404)나, 인증 오류 (401) 또는 개발자가 유발하는 500 오류 같은 것들입니다. 애플리케이션의 어느 곳에서든 이 같은 응답을 반환하려면 다음과 같이 실행하십시오:

    abort(404);

The `abort` method will immediately raise an exception which will be rendered by the exception handler. Optionally, you may provide the response text:

`abort` 메소드는 즉시 예외-exception을 발생시키고, 그 예외는 Exception 핸들러에 의해 렌더링될 것입니다. 선택적으로 응답의 텍스트를 지정할 수도 있습니다.

    abort(403, 'Unauthorized action.');

This method may be used at any time during the request's lifecycle.

이 메소드는 요청의 라이프사이클 안에서라면 언제든지 사용할 수 있습니다.

<a name="custom-http-error-pages"></a>
### Custom HTTP Error Pages
### 사용자 지정 HTTP 에러 페이지

Laravel makes it easy to return custom error pages for various HTTP status codes. For example, if you wish to customize the error page for 404 HTTP status codes, create a `resources/views/errors/404.blade.php`. This file will be served on all 404 errors generated by your application.

라라벨에서는 HTTP 응답 코드에 따른 다양한 사용자 지정 에러 페이지를 반환할 수 있습니다. 예를 들어 404 HTTP 응답 코드에 대한 에러 페이지를 수정하고자 한다면, `resources/views/errors/404.blade.php` 파일을 생성하면 됩니다. 이 파일은 애플리케이션에서 404 에러가 발생했을 때 사용자에게 보여질 것입니다. 

The exception raised by the abort method will be passed to the view as $exception, which allows you to present the user with the error message if needed. e.g.

abort 메소드에 의해 예외-exception 가 전달되는 뷰에서 $exception 변수로 전해집며, 필요한 경우 에러 메시지를 사용자에게 표시하는 데 사용할 수 있습니다.

    $exception->getMessage()

The views within this directory should be named to match the HTTP status code they correspond to.

이 디렉토리 안에 들어 있는 뷰 파일들은 파일의 이름이 HTTP 응답 코드와 일치해야만 합니다. 

<a name="logging"></a>
## Logging
## 로깅

The Laravel logging facilities provide a simple layer on top of the powerful [Monolog](http://github.com/seldaek/monolog) library. By default, Laravel is configured to create a log file for your application in the `storage/logs` directory. You may write information to the logs using the `Log` [facade](/docs/{{version}}/facades):

라라벨의 로그 기능은 강력한 [Monolog](http://github.com/seldaek/monolog) 라이브러리위에 간단한 레이어를 제공합니다. 기본적으로 라라벨은 `storage/logs` 디렉토리에 하나의 로그 파일을 생성하도록 설정되어 있습니다. `Log` [파사드](/docs/{{version}}/facades)를 사용하여, 로그 파일에 정보를 기록할 수도 있습니다:

    <?php

    namespace App\Http\Controllers;

    use Log;
    use App\User;
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

The logger provides the eight logging levels defined in [RFC 5424](http://tools.ietf.org/html/rfc5424): **emergency**, **alert**, **critical**, **error**, **warning**, **notice**, **info** and **debug**.

로그는 [RFC 5424](http://tools.ietf.org/html/rfc5424) 에 정의되어있는 **emergency**, **alert**, **critical**, **error**, **warning**, **notice**, **info** 그리고 **debug**의 8가지 로깅 레벨을 제공합니다.

    Log::emergency($error);
    Log::alert($error);
    Log::critical($error);
    Log::error($error);
    Log::warning($error);
    Log::notice($error);
    Log::info($error);
    Log::debug($error);

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
