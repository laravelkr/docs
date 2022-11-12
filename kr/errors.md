# Error Handling
# 오류 핸들링

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Configuration](#configuration)
- [설정하기](#configuration)
- [The Exception Handler](#the-exception-handler)
- [예외 핸들러](#the-exception-handler)
    - [Reporting Exceptions](#reporting-exceptions)
    - [예외의 보고](#reporting-exceptions)
    - [Exception Log Levels](#exception-log-levels)
    - [예외 로그 수준](#exception-log-levels)
    - [Ignoring Exceptions By Type](#ignoring-exceptions-by-type)
    - [타입별 예외 무시](#ignoring-exceptions-by-type)
    - [Rendering Exceptions](#rendering-exceptions)
    - [예외의 렌더링](#rendering-exceptions)
    - [Reportable & Renderable Exceptions](#renderable-exceptions)
    - [보고 가능한(Reportable) & 렌더링 가능한(Renderable) Exceptions](#renderable-exceptions)
- [HTTP Exceptions](#http-exceptions)
- [HTTP 예외](#http-exceptions)
    - [Custom HTTP Error Pages](#custom-http-error-pages)
    - [커스텀 HTTP 오류 페이지](#custom-http-error-pages)

<a name="introduction"></a>
## Introduction
## 시작하기

When you start a new Laravel project, error and exception handling is already configured for you. The `App\Exceptions\Handler` class is where all exceptions thrown by your application are logged and then rendered to the user. We'll dive deeper into this class throughout this documentation.

새로운 라라벨 프로젝트를 시작하면, 오류 및 예외 처리는 기본적으로 설정되어 있습니다. `App\Exceptions\Handler` 클래스는 애플리케이션에서 발생하는 모든 예외들에 대해 로그를 남기고 사용자에게 그 응답을 렌더링하는 역할을 합니다. 우리는 이 문서 전체를 통해서 이 클래스에 대해서 자세히 살펴볼 것입니다.

<a name="configuration"></a>
## Configuration
## 설정하기

The `debug` option in your `config/app.php` configuration file determines how much information about an error is actually displayed to the user. By default, this option is set to respect the value of the `APP_DEBUG` environment variable, which is stored in your `.env` file.

`config/app.php` 설정파일의 `debug` 옵션은 사용자에게 오류 정보를 어느 정도로 표시할지를 결정합니다. 기본적으로 이 옵션은 `.env` 파일에 담긴 `APP_DEBUG` 환경변수에 따라서 결정됩니다.

During local development, you should set the `APP_DEBUG` environment variable to `true`. **In your production environment, this value should always be `false`. If the value is set to `true` in production, you risk exposing sensitive configuration values to your application's end users.**

로컬 개발 중에는, `APP_DEBUG` 환경변수를 `true`로 설정해야 합니다. **실제 운영환경에서 `APP_DEBUG` 값은 항상 `false`로 되어 있어야 합니다. 만약 실제 운영환경에서 이 값을 `true`로 설정한다면, 애플리케이션의 민감한 설정 값들이 사용자들(end users)에게 노출되어 버립니다.**

<a name="the-exception-handler"></a>
## The Exception Handler
## 예외 핸들러

<a name="reporting-exceptions"></a>
### Reporting Exceptions
### 예외의 보고

All exceptions are handled by the `App\Exceptions\Handler` class. This class contains a `register` method where you may register custom exception reporter and renderer callbacks. We'll examine each of these concepts in detail. Exception reporting is used to log exceptions or send them to an external service like [Flare](https://flareapp.io), [Bugsnag](https://bugsnag.com) or [Sentry](https://github.com/getsentry/sentry-laravel). By default, exceptions will be logged based on your [logging](/docs/{{version}}/logging) configuration. However, you are free to log exceptions however you wish.

모든 예외는 `App\Exceptions\Handler` 클래스가 다룹니다. 이 클래스는 여러분이 사용자 정의 예외 리포터(custom exception reporter)와 렌더러 콜백(renderer callbacks)을 등록할 수 있는 `register` 메소드를 가지고 있습니다. 이 개념을 자세하게 살펴 봅시다. 예외 보고(Exception Reporting)는, 예외에 대해 로그를 남기거나, [Flare](https://flareapp.io), [Bugsnag](https://bugsnag.com) 또는 [Sentry](https://github.com/getsentry/sentry-laravel)와 같은 외부 서비스로 전송할 때 사용합니다. 기본적으로, 예외에 대한 로그는 [logging](/docs/{{version}}/logging) 설정에 따라서 기록 됩니다. 그러나 여러분이 원하는 대로 예외 로그를 남길 수도 있습니다.

For example, if you need to report different types of exceptions in different ways, you may use the `reportable` method to register a Closure that should be executed when an exception of a given type needs to be reported. Laravel will deduce what type of exception the Closure reports by examining the type-hint of the Closure:

예컨데 여러 가지 유형의 예외를 여러 가지 방법에 따라 보고할 필요가 있다면, `reportable` 메소드를 사용해서 클로저를 등록하여 특정 유형의 예외만 보고될 수 있도록 등록한 클로저를 실행할 수 있습니다. 라라벨은 클로저의 타입힌트(type-hint)를 통해서 클로저가 보고할 예외의 유형을 추론합니다.

    use App\Exceptions\InvalidOrderException;

    /**
     * Register the exception handling callbacks for the application.
     *
     * @return void
     */
    public function register()
    {
        $this->reportable(function (InvalidOrderException $e) {
            //
        });
    }

When you register a custom exception reporting callback using the `reportable` method, Laravel will still log the exception using the default logging configuration for the application. If you wish to stop the propagation of the exception to the default logging stack, you may use the `stop` method when defining your reporting callback or return `false` from the callback:

여러분이 `reportable` 메소드를 이용하여 커스텀 예외보고 콜백(custom exception reporting callback) 함수를 등록한 경우에도, 라라벨은 애플리케이션의 기본 로깅 설정에 따라서 예외를 로그에 기록할 것입니다. 만약 여러분이 기본 로깅 스택(default logging stack)으로 더 이상 예외를 전달하지 않도록 하려면 보고 콜백(reporting callback) 함수를 정의할 때 `stop` 메소드를 사용하거나 콜백 함수에서 `false`를 반환하면 됩니다.

    $this->reportable(function (InvalidOrderException $e) {
        //
    })->stop();
    
    $this->reportable(function (InvalidOrderException $e) {
        return false;
    });

> **Note**
> To customize the exception reporting for a given exception, you may also consider using [reportable exceptions](/docs/{{version}}/errors#renderable-exceptions)

> **Note**
> 주어진 예외에 대한 예외 보고(exception reporting)을 커스터마이징하려면, [reportable exceptions](/docs/{{version}}/errors#renderable-exceptions)을 살펴보세요.

<a name="global-log-context"></a>
#### Global Log Context
#### 글로벌 로그 컨텍스트

If available, Laravel automatically adds the current user's ID to every exception's log message as contextual data. You may define your own global contextual data by overriding the `context` method of your application's `App\Exceptions\Handler` class. This information will be included in every exception's log message written by your application:

라라벨은 자동으로 현재의 사용자 ID를 컨텍스트 데이터(contextual data)로써 모든 예외의 로그 메세지에 자동으로 추가합니다. `App\Exceptions\Handler` 클래스의 `context` 메소드를 오버라이딩하여 여러분 자신만의 글로벌 컨텍스트 데이터를 정의할 수 있습니다. 이 정보는 애플리케이션이 기록하는 모든 예외 로그에 포함될 것입니다.

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

<a name="exception-log-context"></a>
#### Exception Log Context
#### 예외 로그 컨텍스트

While adding context to every log message can be useful, sometimes a particular exception may have unique context that you would like to include in your logs. By defining a `context` method on one of your application's custom exceptions, you may specify any data relevant to that exception that should be added to the exception's log entry:

모든 로그 메세지에 컨텍스트를 추가하는 것은 편리하지만, 때때로 특정 예외를 통해서 로그에 포함시키고 싶은 고유의 컨텍스트가 있을 수도 있습니다. 애플리케이션의 커스텀 예외에 `context` 메소드를 정의하여, 예외 로그 엔트리(the exception's log entry)에 추가하기로한 예외와 관련된(relevant) 어떠한 데이터든 지정할 수 있습니다.

    <?php
    
    namespace App\Exceptions;
    
    use Exception;
    
    class InvalidOrderException extends Exception
    {
        // ...
    
        /**
         * Get the exception's context information.
         *
         * @return array
         */
        public function context()
        {
            return ['order_id' => $this->orderId];
        }
    }

<a name="the-report-helper"></a>
#### The `report` Helper
#### `report` 헬퍼 함수

Sometimes you may need to report an exception but continue handling the current request. The `report` helper function allows you to quickly report an exception via the exception handler without rendering an error page to the user:

때때로 발생한 예외를 보고할 필요는 있지만 현재의 요청(request)을 중지하지 않고 계속 처리할 필요가 있는 경우도 있습니다. `report` 헬퍼 함수를 사용하면 오류 페이지를 표시하지 않으면서 예외 핸들러(Exception handler)를 이용해 예외를 보고 할 수 있습니다. 

    public function isValid($value)
    {
        try {
            // Validate the value...
        } catch (Throwable $e) {
            report($e);

            return false;
        }
    }

<a name="exception-log-levels"></a>
### Exception Log Levels
### 예외 로그 수준

When messages are written to your application's [logs](/docs/{{version}}/logging), the messages are written at a specified [log level](/docs/{{version}}/logging#log-levels), which indicates the severity or importance of the message being logged.

메시지가 애플리케이션의 [로그](/docs/{{version}}/logging)에 쓰일 때, 메시지는 심각도와 중요도를 나타내는 [로그 수준](/docs/{{version}}/logging#log-levels)으로 쓰여집니다.

As noted above, even when you register a custom exception reporting callback using the `reportable` method, Laravel will still log the exception using the default logging configuration for the application; however, since the log level can sometimes influence the channels on which a message is logged, you may wish to configure the log level that certain exceptions are logged at.

앞서 언급한 바와 같이 당신이 `reportable` 메서드를 사용하여 커스텀 예외 보고 콜백을 등록했을 때에도 라라벨은 여전히 애플리케이션의 기본 로깅 설정을 사용하여 예외를 기록할 것입니다. 하지만 어떤 메시지가 기록될지에 있어서 로그 수준이 영향을 미치기 때문에 특정 예외가 로그를 남길 때 로그 수준을 설정하고 싶을 수 있습니다. 

To accomplish this, you may define an array of exception types and their associated log levels within the `$levels` property of your application's exception handler:

그렇게 하기 위해서는 애플리케이션의 예외 핸들어에 있는 `$levels` 프로퍼티에 예외 타입과 그에 해당하는 로그 수준을 배열로 정의하면 됩니다. 

    use PDOException;
    use Psr\Log\LogLevel;

    /**
     * A list of exception types with their corresponding custom log levels.
     *
     * @var array<class-string<\Throwable>, \Psr\Log\LogLevel::*>
     */
    protected $levels = [
        PDOException::class => LogLevel::CRITICAL,
    ];

<a name="ignoring-exceptions-by-type"></a>
#### Ignoring Exceptions By Type
#### 유형에 따른 예외 무시

When building your application, there will be some types of exceptions you simply want to ignore and never report. Your application's exception handler contains a `$dontReport` property which is initialized to an empty array. Any classes that you add to this property will never be reported; however, they may still have custom rendering logic:

애플리케이션을 구축할 때, 여러분이 무시하거나 보고하지 않기를 원하는 몇 가지 유형의 예외가 있을 수도 있습니다. 애플리케이션의 예외 핸들러는 빈 배열로 초기화된 `$dontReport` 속성을 가지고 있습니다. 이 속성에 추가된 클래스는 오류를 보고하지 않습니다.(never) 하지만 오류를 보고하지 않더라도 커스텀 렌더링 로직을 가질 수 있습니다.

    use App\Exceptions\InvalidOrderException;

    /**
     * A list of the exception types that are not reported.
     *
     * @var array<int, class-string<\Throwable>>
     */
    protected $dontReport = [
        InvalidOrderException::class,
    ];

> **Note**
> Behind the scenes, Laravel already ignores some types of errors for you, such as exceptions resulting from 404 HTTP "not found" errors or 419 HTTP responses generated by invalid CSRF tokens.

> **Note**
> 내부적으로 라라벨은 편의를 위해 몇몇 타입의 에러를 무시합니다. 404 HTTP "not found" 에러 또는 CSRF 토큰 인증을 통과하지 못한 경우에 발생하는 419 HTTP responses generated 등의 에러 입니다.

<a name="rendering-exceptions"></a>
### Rendering Exceptions
### 예외의 렌더링

By default, the Laravel exception handler will convert exceptions into an HTTP response for you. However, you are free to register a custom rendering Closure for exceptions of a given type. You may accomplish this via the `renderable` method of your exception handler.

기본적으로 라라벨의 예외 핸들러는 예외를 HTTP 응답으로 변환합니다. 그러나 특정한 유형의 예외에 대해 사용자 정의 렌더링 클로저(Closure)를 자유롭게 등록할 수 있습니다. 이는 예외 핸들러의 `renderable` 메소드를 통해 이뤄집니다.

The closure passed to the `renderable` method should return an instance of `Illuminate\Http\Response`, which may be generated via the `response` helper. Laravel will deduce what type of exception the closure renders by examining the type-hint of the closure:

`rednerable` 메소드에 전달된 클로저(Closure)는 `response` 헬퍼 함수를 통해서 생성된 `Illuminate\Http\Response`의 인스턴스를 반환해야 합니다. 라라벨은 클로저의 타입힌트(type-hint)를 통해서 클로저가 예외 유형을 추론하며 예외 유형에 맞는 렌더링 메시지를 표시합니다.

    use App\Exceptions\InvalidOrderException;

    /**
     * Register the exception handling callbacks for the application.
     *
     * @return void
     */
    public function register()
    {
        $this->renderable(function (InvalidOrderException $e, $request) {
            return response()->view('errors.invalid-order', [], 500);
        });
    }

You may also use the `renderable` method to override the rendering behavior for built-in Laravel or Symfony exceptions such as `NotFoundHttpException`. If the closure given to the `renderable` method does not return a value, Laravel's default exception rendering will be utilized:

라라벨 또는 심포니의 내장 렌더링 기능(rendering behavior)을 `renderable` 메소드를 사용하여 재정의 할 수 있습니다. 예를 들어 기본 내장 예외 렌더링 기능에는 `NotFoundHttpException`이란 예외가 있습니다. `renderable` 메소드에서 인자로 할당되는 클로저에 `NotFoundHttpException` 예외를 클로저의 파라메터로 지정한다고 가정해 봅시다. 클로저에서 리턴 값이 반환되지 않는다면 `NotFoundHttpException` 예외일 경우 실행되는 라라벨의 기본 예외 렌더링이 사용됩니다.

    use Symfony\Component\HttpKernel\Exception\NotFoundHttpException;

    /**
     * Register the exception handling callbacks for the application.
     *
     * @return void
     */
    public function register()
    {
        $this->renderable(function (NotFoundHttpException $e, $request) {
            if ($request->is('api/*')) {
                return response()->json([
                    'message' => 'Record not found.'
                ], 404);
            }
        });
    }



<a name="renderable-exceptions"></a>
### Reportable & Renderable Exceptions
### 보고 가능한(Reportable) & 렌더링 가능한(Renderable) 예외들

Instead of type-checking exceptions in the exception handler's `register` method, you may define `report` and `render` methods directly on your custom exceptions. When these methods exist, they will be automatically called by the framework:

예외 핸들러의 `register` 메소드에서 예외의 유형을 확인(type-checking)하는 대신에, 커스텀 예외에 `report` 와 `render` 메소드를 직접 정의할 수 있습니다. 커스텀으로 정의한 `report` 와 `render`가 존재한다면 이들 메소드에 정의된 로직은 프레임워크에 의해 자동으로 호출됩니다.

    <?php

    namespace App\Exceptions;
    
    use Exception;
    
    class InvalidOrderException extends Exception
    {
        /**
        * Report the exception.
        *
        * @return bool|null
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
            return response(/* ... */);
        }
    }

If your exception extends an exception that is already renderable, such as a built-in Laravel or Symfony exception, you may return `false` from the exception's `render` method to render the exception's default HTTP response:

라라벨이나 심포니에 내장된 예외는 에러에 대한 HTTP 리스폰스를 반환합니다. 이들 내장된 예외를 상속하여 사용자 정의 예외를 만드는 경우에는 커스텀 예외의 `render` 메소드에서 `false`를 반환함으로써 기본 예외에서 사용되는 디폴트 HTTP 리스폰스를 렌더링 할 수 있습니다.

    /**
     * Render the exception into an HTTP response.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return \Illuminate\Http\Response
     */
    public function render($request)
    {
        // Determine if the exception needs custom rendering...

        return false;
    }

If your exception contains custom reporting logic that is only necessary when certain conditions are met, you may need to instruct Laravel to sometimes report the exception using the default exception handling configuration. To accomplish this, you may return `false` from the exception's `report` method:

특정한 조건을 충족할 때에만 예외를 보고하는 커스텀 예외를 `report` 메소드 내부에 정의를 할 때 이 커스텀 예외를 라라벨의 기본(default) 예외 처리에 포함되도록 설정할 수 있습니다. 라라벨의 기본 예외 처리에 해당 커스텀 예외가 포함되도록 설정하려면 `report` 메소드에서 커스텀 예외가 발생할 때 `false`를 반환하도록 설정하면 됩니다.

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

> **Note**
> You may type-hint any required dependencies of the `report` method and they will automatically be injected into the method by Laravel's [service container](/docs/{{version}}/container).

> **Note**
> `report` 메소드에 필요한 의존성을 타입힌트하면 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)가 자동으로 이를 주입해줍니다.

<a name="http-exceptions"></a>
## HTTP Exceptions
## HTTP 예외

Some exceptions describe HTTP error codes from the server. For example, this may be a "page not found" error (404), an "unauthorized error" (401) or even a developer generated 500 error. In order to generate such a response from anywhere in your application, you may use the `abort` helper:

일부 예외들은 서버로부터 HTTP 오류 코드를 표시합니다. 이를테면 "page not found" 오류(404)나, "unauthorized error" (401) 또는 개발자가 만들어낸 500 에러입니다. 애플리케이션의 어느 곳에서든 이러한 응답을 생성하고자 한다면, `abort` 헬퍼 함수를 사용하면 됩니다.

    abort(404);

<a name="custom-http-error-pages"></a>
### Custom HTTP Error Pages
### 커스텀 HTTP 오류 페이지

Laravel makes it easy to display custom error pages for various HTTP status codes. For example, if you wish to customize the error page for 404 HTTP status codes, create a `resources/views/errors/404.blade.php` view template. This view will be rendered on all 404 errors generated by your application. The views within this directory should be named to match the HTTP status code they correspond to. The `Symfony\Component\HttpKernel\Exception\HttpException` instance raised by the `abort` function will be passed to the view as an `$exception` variable:

라라벨을 사용하면 다양한 HTTP 응답 코드를 위해 커스텀 오류 페이지를 표시할 수 있습니다. 예를 들어, 404 HTTP 응답 코드에 대한 오류 페이지를 커스터마이징하고 싶다면, `resources/views/errors/404.blade.php` 뷰 템플릿 파일을 생성합니다. 이 뷰는 애플리케이션에서 404 오류가 발생하면 항상 사용자에게 표시됩니다. 같은 디렉토리 안에 있는 뷰 파일의 이름은 대응하는 HTTP 상태 코드와 일치하는 이름을 붙여줘야 해야 합니다. `abort` 함수에 의해서 발생한 `Symfony\Component\HttpKernel\Exception\HttpException` 인스턴스는 `$exception` 변수로 뷰에 전달될 것입니다.

    <h2>{{ $exception->getMessage() }}</h2>

You may publish Laravel's default error page templates using the `vendor:publish` Artisan command. Once the templates have been published, you may customize them to your liking:

`vendor:publish` Artisan 명령을 사용하여 Laravel의 기본 오류 페이지 템플릿을 웹에 띄울 (publish) 수 있습니다. 템플릿을 웹 페이지에 띄웠다면(published), 여러분이 원하는 대로 커스터마이징을 해도 됩니다.

```shell
php artisan vendor:publish --tag=laravel-errors
```

<a name="fallback-http-error-pages"></a>
#### Fallback HTTP Error Pages
#### HTTP 에러 대체 페이지

You may also define a "fallback" error page for a given series of HTTP status codes. This page will be rendered if there is not a corresponding page for the specific HTTP status code that occurred. To accomplish this, define a `4xx.blade.php` template and a `5xx.blade.php` template in your application's `resources/views/errors` directory.

HTTP 상태코드에 대한 "대체" 오류 페이지를 정의할 수 있습니다. 오류가 발생한 HTTP 상태 코드와 일치하는 페이지가 없는 경우에 이 대체 페이지가 렌더링 됩니다. 이를 적용하려면 애플리케이션의 `resources/views/errors` 디렉토리에 `4xx.blade.php`, `5xx.blade.php` 템플릿을 정의하면 됩니다. 
