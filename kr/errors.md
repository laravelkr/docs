# Error Handling
# 에러 핸들링

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
    - [Reportable & Renderable Exceptions](#renderable-exceptions)
    - [Reportable & Renderable Exceptions](#renderable-exceptions)
- [HTTP Exceptions](#http-exceptions)
- [HTTP Exceptions](#http-exceptions)
    - [Custom HTTP Error Pages](#custom-http-error-pages)
    - [커스텀-사용자 지정 HTTP 에러 페이지](#custom-http-error-pages)

<a name="introduction"></a>
## Introduction
## 시작하기

When you start a new Laravel project, error and exception handling is already configured for you. The `App\Exceptions\Handler` class is where all exceptions triggered by your application are logged and then rendered back to the user. We'll dive deeper into this class throughout this documentation.

새로운 라라벨 프로젝트가 시작될 때, 기본적인 오류 및 예외 처리는 모두 설정되어 있습니다. 애플리케이션에서 발생하는 모든 예외-exceptions 들은 `App\Exceptions\Handler` 클래스에 의해서 로그를 남기고 사용자에게 응답을 보여주게 됩니다. 이 문서에서는 이 클래스에 대해서 좀 더 자세히 알아보겠습니다.

For logging, Laravel utilizes the [Monolog](https://github.com/Seldaek/monolog) library, which provides support for a variety of powerful log handlers. Laravel configures several of these handlers for you, allowing you to choose between a single log file, rotating log files, or writing error information to the system log.

라라벨은 로그를 남기기 위해서 강력하고 다양한 로그 핸들러를 지원하는 [Monolog](https://github.com/Seldaek/monolog) 라이브러리를 사용합니다. 라라벨은 하나의 파일에 로그를 남기거나, 로그 파일을 일정 주기에 따라서 바꾸는, 또는 시스템 로그에 에러를 기록하도록 하는 여러 핸들러를 설정할 수 있습니다.

<a name="configuration"></a>
## Configuration
## 설정하기

The `debug` option in your `config/app.php` configuration file determines how much information about an error is actually displayed to the user. By default, this option is set to respect the value of the `APP_DEBUG` environment variable, which is stored in your `.env` file.

`config/app.php` 설정 파일의 `debug` 옵션은 에러애 대한 얼마나 많은 정보를 사용자에게 노출할 것인지 결정합니다. 기본적으로 이 옵션은 `.env` 파일에 있는 `APP_DEBUG` 환경 변수에 따라서 결정됩니다.

For local development, you should set the `APP_DEBUG` environment variable to `true`. In your production environment, this value should always be `false`. If the value is set to `true` in production, you risk exposing sensitive configuration values to your application's end users.

로컬 개발 환경에서는, `APP_DEBUG` 환경 변수 값을 `true` 로 설정해야 합니다. 실제 서비스 프로덕션 환경에서는 이 값은 `false` 로 설정되어야 합니다. 만약 실 서버 환경에서 이 값을 `true` 로 설정한다면, 애플리케이션의 민감한 환경 설정 값이 사용자에게 표시되는 위험이 있습니다.

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

        parent::report($exception);
    }

> {tip} Instead of making a lot of `instanceof` checks in your `report` method, consider using [reportable exceptions](/docs/{{version}}/errors#renderable-exceptions)

> {tip} `report` 메소드 안에서 수많은 `instanceof` 체크를 하는 대신에 [reportable exceptions](/docs/{{version}}/errors#renderable-exceptions)을 사용하도록 하십시오.

#### Global Log Context
#### 글로벌 로그 컨텍스트

If available, Laravel automatically adds the current user's ID to every exception's log message as contextual data. You may define your own global contextual data by overriding the `context` method of your application's `App\Exceptions\Handler` class. This information will be included in every exception's log message written by your application:

가능한경우, 라라벨은 현재의 사용자 ID를 자동으로 모든 exception 로그 메세지에 컨텍스트 데이터로 추가합니다. 애플리케이션의 `App\Exceptions\Handler` 클래스에 `context` 메소드를 오버라이딩 해서 글로벌에서 추가하고자 하는 고유한 컨텍스트 데이터를 정의할 수 있습낟. 이 정보는 애플리케이션에서 기록되는 모든 exception 로그에 포함됩니다:

    /**
     * Get the default context variables for logging.
     *
     * @return array
     */
    protected function context()
    {
        return array_merge(parent::context(), [
            'foo' => 'bar',
        ]);
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

Exception 핸들러의 `report` 그리고 `render` 메소드에서 exception의 타입을 확인하는 대신에 여러분이 정의한 고유한 exception에 `report` 와 `render` 메소드를 정의할 수 있습니다. 이 메소드가 존재한다면 프레임워크는 자동으로 이 메소드를 호출합니다:

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

> {tip} You may type-hint any required dependencies of the `report` method and they will automatically be injected into the method by Laravel's [service container](/docs/{{version}}/container).

> {tip} `report` 메소드에 필요한 의존성을 타입힌트하면 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)가 자동으로 이를 주입해줍니다.

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

You may publish Laravel's error page templates using the `vendor:publish` Artisan command. Once the templates have been published, you may customize them to your liking:

`vendor:publish` Artisan 명령을 사용하여 Laravel의 오류 페이지 템플릿을 퍼블리싱 할 수 있습니다. 템플릿이 퍼블리싱되면 원하는대로 사용자 정의 할 수 있습니다.

    php artisan vendor:publish --tag=laravel-errors