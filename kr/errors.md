# Error Handling
# 에러 핸들링

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Configuration](#configuration)
- [설정하기](#configuration)
- [The Exception Handler](#the-exception-handler)
- [Exception 핸들러](#the-exception-handler)
    - [Reporting Exceptions](#reporting-exceptions)
    - [Reporting Exceptions](#reporting-exceptions)
    - [Rendering Exceptions](#rendering-exceptions)
    - [Rendering Exceptions](#rendering-exceptions)
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

<a name="reporting-exceptions"></a>
### Reporting Exceptions
### Reporting Exceptions

All exceptions are handled by the `App\Exceptions\Handler` class. This class contains a `register` method where you may register custom exception reporter and renderer callbacks. We'll examine each of these concepts in detail. Exception reporting is used to log exceptions or send them to an external service like [Flare](https://flareapp.io), [Bugsnag](https://bugsnag.com) or [Sentry](https://github.com/getsentry/sentry-laravel). By default, exceptions will be logged based on your [logging](/docs/{{version}}/logging) configuration. However, you are free to log exceptions however you wish.

모든 예외-exception는 `App\Exceptions\Handler` 클래스에 의해서 처리됩니다. 이 클래스에는 커스텀 예외 리포터와 렌더러 콜백을 등록할 수 있는 `register` 메소드가 있습니다. 각 개념을 자세히 살펴 보겠습니다. Exception Reporting는 예외를 기록하거나 [Flare](https://flareapp.io), [Bugsnag](https://bugsnag.com) 또는 [Sentry](https://github.com/getsentry/sentry-laravel)와 같은 외부 서비스로 전송하는 데 사용됩니다. 기본적으로 예외는 설정 [logging](/docs/{{version}}/logging) 설정에 따라 기록됩니다. 그러나 원하는 경우에만 예외를 기록 할 수도 있습니다.

For example, if you need to report different types of exceptions in different ways, you may use the `reportable` method to register a Closure that should be executed when an exception of a given type needs to be reported. Laravel will deduce what type of exception the Closure reports by examining the type-hint of the Closure:

예를 들어 각기 다른 유형의 예외-exception을 다른 방법 보고해야한다면, instanceof 비교 연산자를 사용하면 됩니다.

    use App\Exceptions\CustomException;

    /**
     * Register the exception handling callbacks for the application.
     *
     * @return void
     */
    public function register()
    {
        $this->reportable(function (CustomException $e) {
            //
        });
    }

When you register a custom exception reporting callback using the `reportable` method, Laravel will still log the exception using the default logging configuration for the application. If you wish to stop the propagation of the exception to the default logging stack, you may use the `stop` method when defining your reporting callback:

`reportable` 메소드를 사용하여 사용자 정의 예외보고 콜백을 등록할 때 Laravel은 애플리케이션의 기본 로깅 구성을 사용하여 예외를 계속 기록합니다. 기본 로깅 스택으로의 예외 전파를 중지하려면 보고 콜백을 정의할 때 `stop` 메소드를 사용할 수 있습니다.

    $this->reportable(function (CustomException $e) {
        //
    })->stop();

> {tip} To customize the exception reporting for a given exception, you may also consider using [reportable exceptions](/docs/{{version}}/errors#renderable-exceptions)

> {tip} 특정 예외에 대한 예외보고를 사용자 지정하려면 [reportable exceptions](/docs/{{version}}/errors#renderable-exceptions)를 사용하는 것도 고려할 수 있습니다.

#### Global Log Context
#### 글로벌 로그 컨텍스트

If available, Laravel automatically adds the current user's ID to every exception's log message as contextual data. You may define your own global contextual data by overriding the `context` method of your application's `App\Exceptions\Handler` class. This information will be included in every exception's log message written by your application:

가능한경우, 라라벨은 현재의 사용자 ID를 자동으로 모든 exception 로그 메세지에 컨텍스트 데이터로 추가합니다. 애플리케이션의 `App\Exceptions\Handler` 클래스에 `context` 메소드를 오버라이딩 해서 글로벌에서 추가하고자 하는 고유한 컨텍스트 데이터를 정의할 수 있습니다. 이 정보는 애플리케이션에서 기록되는 모든 exception 로그에 포함됩니다.

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

Sometimes you may need to report an exception but continue handling the current request. The `report` helper function allows you to quickly report an exception using your exception handler without rendering an error page:

때때로, 현재 발생한 exception-예외를 report-보고해야 하지만, 현재 request 처리를 계속하기를 원할 수도 있습니다. `report` 헬퍼 함수는 에러 페이지를 렌더링 하지 않으면서, exception 핸들러를 사용하여 보다 빠르게 예외사항을 보고합니다.

    public function isValid($value)
    {
        try {
            // Validate the value...
        } catch (Throwable $e) {
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

<a name="rendering-exceptions"></a>
### Rendering Exceptions
### Rendering Exceptions

By default, the Laravel exception handler will convert exceptions into an HTTP response for you. However, you are free to register a custom rendering Closure for exceptions of a given type. You may accomplish this via the `renderable` method of your exception handler. Laravel will deduce what type of exception the Closure renders by examining the type-hint of the Closure:

기본적으로 Laravel 예외 핸들러는 예외를 HTTP 응답으로 변환합니다. 그러나 주어진 유형의 예외에 대해 사용자 지정 렌더링 클로저(Closure)를 자유롭게 등록할 수 있습니다. exception 핸들러의 `renderable` 메소드를 통하여 이 작업을 수행할 수 있습니다. Laravel은 클로저(Closure)의 유형 힌트를 검사하여 클로저(Closure)가 렌더링하는 예외 유형을 추론합니다.

    use App\Exceptions\CustomException;

    /**
     * Register the exception handling callbacks for the application.
     *
     * @return void
     */
    public function register()
    {
        $this->renderable(function (CustomException $e, $request) {
            return response()->view('errors.custom', [], 500);
        });
    }

<a name="renderable-exceptions"></a>
### Reportable & Renderable Exceptions
### Reportable & Renderable Exceptions

Instead of type-checking exceptions in the exception handler's `report` and `render` methods, you may define `report` and `render` methods directly on your custom exception. When these methods exist, they will be called automatically by the framework:

Exception 핸들러의 `report` 그리고 `render` 메소드에서 exception의 타입을 확인하는 대신에 여러분이 정의한 고유한 exception에 `report` 와 `render` 메소드를 정의할 수 있습니다. 이 메소드가 존재한다면 프레임워크는 자동으로 이 메소드를 호출합니다.

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
         * @param  \Illuminate\Http\Request  $request
         * @return \Illuminate\Http\Response
         */
        public function render($request)
        {
            return response(...);
        }
    }

If your exception contains custom reporting logic that only occurs when certain conditions are met, you may need to instruct Laravel to report the exception using the default exception handling configuration. To accomplish this, you may return `false` from the exception's `report` method:

특정 조건에 충족 될 때만 발생하는 사용자 정의보고 로직이 예외에 포함된 경우 기본 예외 처리 구성을 사용하여 예외를 보고하도록 Laravel에 지시해야 할 수 있습니다. 이를 위해 예의의 `report` 메소드에서 `false`를 반환할 수 있습니다.

    /**
     * Report the exception.
     *
     * @return bool|void
     */
    public function report()
    {
        // Determine if the exception needs custom reporting...

        return false;
    }

> {tip} You may type-hint any required dependencies of the `report` method and they will automatically be injected into the method by Laravel's [service container](/docs/{{version}}/container).

> {tip} `report` 메소드에 필요한 의존성을 타입힌트하면 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)가 자동으로 이를 주입해줍니다.

<a name="http-exceptions"></a>
## HTTP Exceptions
## HTTP Exceptions

Some exceptions describe HTTP error codes from the server. For example, this may be a "page not found" error (404), an "unauthorized error" (401) or even a developer generated 500 error. In order to generate such a response from anywhere in your application, you may use the `abort` helper:

어떤 예외들-exceptions은 서버에서 발생하는 HTTP 에러 코드를 나타냅니다. 예를 들어, "페이지를 찾을 수 없습니다." 에러(404)나, 인증 오류 (401) 또는 개발자가 유발하는 500 오류 같은 것들입니다. 애플리케이션의 어느 곳에서든 이 같은 응답을 반환하려면 `abort` 헬퍼 함수를 사용하면 됩니다.

    abort(404);

<a name="custom-http-error-pages"></a>
### Custom HTTP Error Pages
### 사용자 지정 HTTP 에러 페이지

Laravel makes it easy to display custom error pages for various HTTP status codes. For example, if you wish to customize the error page for 404 HTTP status codes, create a `resources/views/errors/404.blade.php`. This file will be served on all 404 errors generated by your application. The views within this directory should be named to match the HTTP status code they correspond to. The `HttpException` instance raised by the `abort` function will be passed to the view as an `$exception` variable:

라라벨에서는 HTTP 응답 코드에 따른 다양한 사용자 지정 에러 페이지를 표시할 수 있습니다. 예를 들어 404 HTTP 응답 코드에 대한 에러 페이지를 수정하고자 한다면, `resources/views/errors/404.blade.php` 파일을 생성하면 됩니다. 이 파일은 애플리케이션에서 404 에러가 발생했을 때 사용자에게 보여질 것입니다. 이 디렉토리안에 있는 뷰의 이름은 해당 HTTP 상태 코드와 일치해야 합니다. `abort` 함수에 의해서 발생한 `HttpException` 인스턴스는 뷰에 `$exception`변수로 전달될 것입니다.

    <h2>{{ $exception->getMessage() }}</h2>

You may publish Laravel's error page templates using the `vendor:publish` Artisan command. Once the templates have been published, you may customize them to your liking:

`vendor:publish` Artisan 명령을 사용하여 Laravel의 오류 페이지 템플릿을 퍼블리싱 할 수 있습니다. 템플릿이 퍼블리싱되면 원하는대로 사용자 정의 할 수 있습니다.

    php artisan vendor:publish --tag=laravel-errors
