# 에러와 로깅

- [시작하기](#introduction)
- [설정하기](#configuration)
    - [에러의 상세정보](#error-detail)
    - [로그 스토리지](#log-storage)
    - [Log Severity Levels](#log-severity-levels)
    - [사용자 지정 Monolog 설정](#custom-monolog-configuration)
- [Exception 핸들러](#the-exception-handler)
    - [Report 메소드](#report-method)
    - [Render 메소드](#render-method)
    - [Reportable & Renderable Exceptions](#renderable-exceptions)
- [HTTP Exceptions](#http-exceptions)
    - [사용자 지정 HTTP 에러 페이지](#custom-http-error-pages)
- [로깅](#logging)

<a name="introduction"></a>
## 시작하기

새로운 라라벨 프로젝트가 시작될 때, 기본적인 오류 및 예외 처리는 모두 설정되어 있습니다. 애플리케이션에서 발생하는 모든 예외-exceptions 들은 `App\Exceptions\Handler` 클래스에 의해서 로그를 남기고 사용자에게 응답을 보여주게 됩니다. 이 문서에서는 이 클래스에 대해서 좀 더 자세히 알아보겠습니다.

라라벨은 로그를 남기기 위해서 강력하고 다양한 로그 핸들러를 지원하는 [Monolog](https://github.com/Seldaek/monolog) 라이브러리를 사용합니다. 라라벨은 하나의 파일에 로그를 남기거나, 로그 파일을 일정 주기에 따라서 바꾸는, 또는 시스템 로그에 에러를 기록하도록 하는 여러 핸들러를 설정할 수 있습니다.

<a name="configuration"></a>
## 설정하기

<a name="error-detail"></a>
### 에러의 상세 정보

`config/app.php` 설정 파일의 `debug` 옵션은 에러애 대한 얼마나 많은 정보를 사용자에게 노출할 것인지 결정합니다. 기본적으로 이 옵션은 `.env` 파일에 있는 `APP_DEBUG` 환경 변수에 따라서 결정됩니다.

로글 개발 환경에서는, `APP_DEBUG` 환경 변수 값을 `true` 로 설정해야 합니다. 실제 서비스 프로덕션 환경에서는 이 값은 `false` 로 설정되어야 합니다. 만약 실 서버 환경에서 이 값을 `true` 로 설정한다면, 애플리케이션의 민감한 환경 설정 값이 사용자에게 표시되는 위험이 있습니다.

<a name="log-storage"></a>
### 로그 스토리지

별다른 설정 없이도, 라라벨은 `single` 파일, `daily` 파일, `syslog` 그리고 `errorlog` 의 로그 모드를 지원합니다. 라라벨이 사용할 스토리지 방식을 설정하기 위해서는`config/app.php` 설정 파일의 `log` 옵션을 수정해야 합니다. 예를 들어 여러분이 하나의 로그 파일 대신에 일별로 저장되는 로그 파일을 사용하고자 한다면, `app` 설정 파일의 `log` 값을 `daily` 로 설정해야 합니다:

    'log' => 'daily'

#### 일별 로그 파일의 최대 기간

`daily` 모드를 사용하는 경우, 라라벨은 기존적으로 5일치의 로그 파일들을 유지할 것입니다. 유지되는 파일의 갯수를 조절하고자 한다면 `app.php` 설정파일에 `log_max_files` 설정을 추가하십시오.

    'log_max_files' => 30

<a name="log-severity-levels"></a>
### 로그의 표현 레벨

Monolog 를 사용할 때 로그 메세지는 각기 다른 수준의 레벨을 가지고 있습니다. 기본적으로 라라벨은 모든 로그 레벨을 스토리지에 기록할 것입니다. 하지만, 실 서버 환경에서 최소한의 레벨을 설정하기를 원한다면, `app.php` 설정 파일에 `log_leve` 을 추가해야 합니다.

이 옵션이 설정되고 나면, 라라벨은 지정된 레벨과 동일하거나, 더 높은 레벨의 모든 로그를 기록합니다. 예를 들어 기본 `log_level`이 `error` 인 경우라면 **error**, **critical**, **alert**, 그리고 **emergency** 메세지를 기록합니다:

    'log_level' => env('APP_LOG_LEVEL', 'error'),

> {tip} Monolog 는 다음의 로그 레벨을 취급합니다. - 가장 낮은 레벨 부터 높은 레벨 순서 :`debug`, `info`, `notice`, `warning`, `error`, `critical`, `alert`, `emergency`.

<a name="custom-monolog-configuration"></a>
### 사용자 지정 Monolog 설정하기

만약 애플리케이션에서 Monolog 어떻게 설정할 것인지 완전히 제어하고자 한다면, 애플리케이션의 `configureMonologUsing` 메소드를 사용하면 됩니다. 이 메소드는 `bootstrap/app.php` 파일에서 `$app` 변수가 반환되기 전에 사용되어야만 합니다:

    $app->configureMonologUsing(function ($monolog) {
        $monolog->pushHandler(...);
    });

    return $app;

#### 채널 이름 커스터마이징 하기

기본적으로, Monolog는 `production` 또는 `local`과 같은 현재 환경과 일치하는 이름으로 인스턴스가 됩니다. 이 값을 변경하려면, `app.php` 설정 파일에 `log_channel` 옵션을 추가하면됩니다:

    'log_channel' => env('APP_LOG_CHANNEL', 'my-app-name'),

<a name="the-exception-handler"></a>
## Exception 핸들러

<a name="report-method"></a>
### Report 메소드

모든 예외-exception는 `App\Exceptions\Handler` 클래스에 의해서 처리됩니다. `report` 메소드는 예외-exceptions를 로깅하거나 [Bugsnag](https://bugsnag.com) 또는 [Sentry](https://github.com/getsentry/sentry-laravel) 같은 별도의 외부 서비스로 보내는데 사용되어 집니다. 기본적으로 `report` 메소드는 예외-exception가 기록되는 경우, 베이스 클래스로 예외-exception을 전달합니다.

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

#### `report` 헬퍼 함수

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
         * Report the exception.
         *
         * @param  \Illuminate\Http\Request
         * @return void
         */
        public function render($request)
        {
            return response(...);
        }
    }

<a name="http-exceptions"></a>
## HTTP 예외-exception

어떤 예외들-exceptions은 서버에서 발생하는 HTTP 에러 코드를 나타냅니다. 예를 들어, "페이지를 찾을 수 없습니다." 에러(404)나, 인증 오류 (401) 또는 개발자가 유발하는 500 오류 같은 것들입니다. 애플리케이션의 어느 곳에서든 이 같은 응답을 반환하려면 `abort` 헬퍼 함수를 사용하면 됩니다:

    abort(404);

`abort` 헬퍼함수는 즉시 예외-exception을 발생시키고, 그 예외는 Exception 핸들러에 의해 렌더링될 것입니다. 선택적으로 응답의 텍스트를 지정할 수도 있습니다.

    abort(403, 'Unauthorized action.');

<a name="custom-http-error-pages"></a>
### 사용자 지정 HTTP 에러 페이지

라라벨에서는 HTTP 응답 코드에 따른 다양한 사용자 지정 에러 페이지를 표시할 수 있습니다. 예를 들어 404 HTTP 응답 코드에 대한 에러 페이지를 수정하고자 한다면, `resources/views/errors/404.blade.php` 파일을 생성하면 됩니다. 이 파일은 애플리케이션에서 404 에러가 발생했을 때 사용자에게 보여질 것입니다. 이 디렉토리안에 있는 뷰의 이름은 해당 HTTP 상태 코드와 일치해야 합니다. `abort` 함수에 의해서 발생한 `HttpException` 인스턴스는 뷰에 `$exception`변수로 전달될 것입니다.

    <h2>{{ $exception->getMessage() }}</h2>

<a name="logging"></a>
## 로깅

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

로그는 [RFC 5424](https://tools.ietf.org/html/rfc5424) 에 정의되어있는 **emergency**, **alert**, **critical**, **error**, **warning**, **notice**, **info** 그리고 **debug**의 8가지 로깅 레벨을 제공합니다.

    Log::emergency($message);
    Log::alert($message);
    Log::critical($message);
    Log::error($message);
    Log::warning($message);
    Log::notice($message);
    Log::info($message);
    Log::debug($message);

#### 컨텍스트 정보

컨텍스트 데이터는 로그 메소드에 배열로 전달될 수 있습니다. 이 컨텍스트 데이터는 로그 메세지에 포맷에 맞게 표시될 것입니다.

    Log::info('User failed to login.', ['id' => $user->id]);

#### 기반이 되는 모노로그 인스턴스에 엑세스하기

Monolog에는 로그에 사용할 수있는 기타 다양한 핸들러가 준비되어 있습니다. 필요하다면 라라벨이 내부에서 사용하는 Monolog 인스턴스에 액세스 할 수 있습니다.

    $monolog = Log::getMonolog();
