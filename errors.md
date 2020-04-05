# 에러 핸들링

- [시작하기](#introduction)
- [설정하기](#configuration)
- [Exception 핸들러](#the-exception-handler)
    - [Report 메소드](#report-method)
    - [Render 메소드](#render-method)
    - [Reportable & Renderable Exceptions](#renderable-exceptions)
- [HTTP Exceptions](#http-exceptions)
    - [커스텀-사용자 지정 HTTP 에러 페이지](#custom-http-error-pages)

<a name="introduction"></a>
## 시작하기

새로운 라라벨 프로젝트가 시작될 때, 기본적인 오류 및 예외 처리는 모두 설정되어 있습니다. 애플리케이션에서 발생하는 모든 예외-exceptions 들은 `App\Exceptions\Handler` 클래스에 의해서 로그를 남기고 사용자에게 응답을 보여주게 됩니다. 이 문서에서는 이 클래스에 대해서 좀 더 자세히 알아보겠습니다.

<a name="configuration"></a>
## 설정하기

`config/app.php` 설정 파일의 `debug` 옵션은 에러애 대한 얼마나 많은 정보를 사용자에게 노출할 것인지 결정합니다. 기본적으로 이 옵션은 `.env` 파일에 있는 `APP_DEBUG` 환경 변수에 따라서 결정됩니다.

로컬 개발 환경에서는, `APP_DEBUG` 환경 변수 값을 `true` 로 설정해야 합니다. 실제 서비스 프로덕션 환경에서는 이 값은 `false` 로 설정되어야 합니다. 만약 실 서버 환경에서 이 값을 `true` 로 설정한다면, 애플리케이션의 민감한 환경 설정 값이 사용자에게 표시되는 위험이 있습니다.

<a name="the-exception-handler"></a>
## Exception 핸들러

<a name="report-method"></a>
### Report 메소드

모든 예외-exception는 `App\Exceptions\Handler` 클래스에 의해서 처리됩니다. 이 클래스에는 `report`와 `render`의 두 가지 메소드가 있습니다. 각 방법을 자세히 살펴 보겠습니다. `report` 메소드는 예외-exceptions를 로깅하거나 [Flare](https://flareapp.io), [Bugsnag](https://bugsnag.com) 또는 [Sentry](https//github.com/getsentry/sentry-laravel)와 같은 외부 서비스로 전송하는 데 사용됩니다. 기본적으로 `report` 메소드는 예외가 기록되는 기본 클래스에 예외를 전달합니다. 그러나 원하는 경우에만 예외를 기록 할 수도 있습니다.

예를 들어 각기 다른 유형의 예외-exception을 다른 방법 보고해야한다면, PHP의 instanceof 비교 연산자를 사용하면 됩니다.

    /**
     * Report or log an exception.
     *
     * This is a great spot to send exceptions to Flare, Sentry, Bugsnag, etc.
     *
     * @param  \Throwable  $exception
     * @return void
     */
    public function report(Throwable $exception)
    {
        if ($exception instanceof CustomException) {
            //
        }

        parent::report($exception);
    }

> {tip} `report` 메소드 안에서 수많은 `instanceof` 체크를 하는 대신에 [reportable exceptions](/docs/{{version}}/errors#renderable-exceptions)을 사용하도록 하십시오.

#### 글로벌 로그 컨텍스트

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

#### `report` 헬퍼 함수

때때로, 현재 발생한 exception-예외를 report-보고해야 하지만, 현재 request 처리를 계속하기를 원할 수도 있습니다. `report` 헬퍼 함수는 에러 페이지를 렌더링 하지 않으면서, exception 핸들러의 `report` 메소드를 사용하여 보다 빠르게 예외사항을 보고합니다.

    public function isValid($value)
    {
        try {
            // Validate the value...
        } catch (Throwable $e) {
            report($e);

            return false;
        }
    }

#### 유형에 따라서 예외-exception 무시하기

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
### Render 메소드

`render` 메소드는 주어진 예외-exception를 HTTP 응답으로 변환하고 브라우저에게 보내는 역할을 담당합니다. 기본적으로 예외-exception는 응답을 생성하는 기본 클래스로 전달됩니다. 하지만 예외-exception의 유형을 파악하고 사용자 지정된 응답을 반환 것도 여러분의 자유입니다.

    /**
     * Render an exception into an HTTP response.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Throwable  $exception
     * @return \Illuminate\Http\Response
     */
    public function render($request, Throwable $exception)
    {
        if ($exception instanceof CustomException) {
            return response()->view('errors.custom', [], 500);
        }

        return parent::render($request, $exception);
    }

<a name="renderable-exceptions"></a>
### Reportable & Renderable Exceptions

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

> {tip} `report` 메소드에 필요한 의존성을 타입힌트하면 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)가 자동으로 이를 주입해줍니다.

<a name="http-exceptions"></a>
## HTTP 예외-exception

어떤 예외들-exceptions은 서버에서 발생하는 HTTP 에러 코드를 나타냅니다. 예를 들어, "페이지를 찾을 수 없습니다." 에러(404)나, 인증 오류 (401) 또는 개발자가 유발하는 500 오류 같은 것들입니다. 애플리케이션의 어느 곳에서든 이 같은 응답을 반환하려면 `abort` 헬퍼 함수를 사용하면 됩니다.

    abort(404);

`abort` 헬퍼함수는 즉시 예외-exception을 발생시키고, 그 예외는 Exception 핸들러에 의해 렌더링될 것입니다. 선택적으로 응답의 텍스트를 지정할 수도 있습니다.

    abort(403, 'Unauthorized action.');

<a name="custom-http-error-pages"></a>
### 사용자 지정 HTTP 에러 페이지

라라벨에서는 HTTP 응답 코드에 따른 다양한 사용자 지정 에러 페이지를 표시할 수 있습니다. 예를 들어 404 HTTP 응답 코드에 대한 에러 페이지를 수정하고자 한다면, `resources/views/errors/404.blade.php` 파일을 생성하면 됩니다. 이 파일은 애플리케이션에서 404 에러가 발생했을 때 사용자에게 보여질 것입니다. 이 디렉토리안에 있는 뷰의 이름은 해당 HTTP 상태 코드와 일치해야 합니다. `abort` 함수에 의해서 발생한 `HttpException` 인스턴스는 뷰에 `$exception`변수로 전달될 것입니다.

    <h2>{{ $exception->getMessage() }}</h2>

`vendor:publish` Artisan 명령을 사용하여 Laravel의 오류 페이지 템플릿을 퍼블리싱 할 수 있습니다. 템플릿이 퍼블리싱되면 원하는대로 사용자 정의 할 수 있습니다.

    php artisan vendor:publish --tag=laravel-errors