# Errors & Logging 에러와 로깅

- [Configuration 설정](#configuration)
- [Handling Errors 에러 처리](#handling-errors)
- [HTTP Exceptions HTTP 예외](#http-exceptions)
- [Logging 로깅](#logging)

<a name="configuration"></a>
## Configuration 설정

The logging facilities for your application are configured in the `Illuminate\Foundation\Bootstrap\ConfigureLogging` bootstrapper class. 애플리케이션의 로그 기능은 `Illuminate\Foundation\Bootstrap\ConfigureLogging` 부트스트랩 클래스에서 설정됩니다. This class utilizes the `log` configuration option from your `config/app.php` configuration file. 이 클래스는 `config/app.php` 설정 파일의 `log` 옵션을 참조합니다. 

By default, the logger is configured to use daily log files; however, you may customize this behavior as needed. 기본적으로 로거는 일단위 로그 파일을 기록하도록 설정되어 있습니다만 필요한경우 이 주기를 변경할 수 있습니다. Since Laravel uses the popular [Monolog](https://github.com/Seldaek/monolog) logging library, you can take advantage of the variety of handlers that Monolog offers. 라라벨은 인기있는 [Monolog](https://github.com/Seldaek/monolog) 로깅 라이브러리를 사용하고 있기 때문에, Monolog 가 제공하는 다양한 핸들러를 이용할 수 있습니다. 

For example, if you wish to use a single log file instead of daily files, you can make the following change to your `config/app.php` configuration file: 예를들어 일별로 기록되는 로그 파일 대신 하나의 로그 파일을 구성하고 싶다면 `config/app.php` 설정파일을 다음과 같이 변경하면 됩니다. 

	'log' => 'single'

Out of the box, Laravel supported `single`, `daily`, `syslog` and `errorlog` logging modes. 별다른 구성이 없이도 라라벨은 `single`, `daily`, `syslog` 그리고 `errorlog` 로깅 모드를 지원합니다. However, you are free to customize the logging for your application as you wish by overriding the `ConfigureLogging` bootstrapper class. 그러나 응용 프로그램에 대한 로깅을 원하는대로 사용자 정의하고자 한다면, `ConfigureLogging` 부트스트랩 클래스를 재정의 하여 사용할 수도 있습니다. 

### Error Detail 오류의 상세정보

The amount of error detail your application displays through the browser is controlled by the `app.debug` configuration option in your `config/app.php` configuration file. `config/app.php` 설정 파일의 `app.debug` 설정 옵션을 통해서 브라우저에 출력되는 애플리케이션의 오류 정보를 제어 할 수 있습니다. By default, this configuration option is set to respect the `APP_DEBUG` environment variable, which is stored in your `.env` file. 기본적으로 이 옵션은 `.env` 파일에 설정되어있는 `APP_DEBUG` 환경 변수를 반영하도록 설정되어 있습니다.

For local development, you should set the `APP_DEBUG` environment variable to `true`. 로컬 개발환경을 구성중이라면 `APP_DEBUG` 환경 변수를 `true`로 구성해야 할것입니다. 
**In your production environment, this value should always be `false`.**
**실제 제품 구동 환경이라면, 이 값은 항상 `false`가 되어야 할것입니다. **

<a name="handling-errors"></a>
## Handling Errors 에러 처리

All exceptions are handled by the `App\Exceptions\Handler` class. 모든 예외-exceptions은 `App\Exceptions\Handler` 클래스에 의해서 처리됩니다. This class contains two methods: `report` and `render`. 이 클래스는 `report`와 `render` 메소드로 구성되어 있습니다. 

The `report` method is used to log exceptions or send them to an external service like [BugSnag](https://bugsnag.com). `report`메소드는 예외-exceptions를 로깅하거나 [BugSnag](https://bugsnag.com)와 같은 별도의 외부 서비스로 보내는데 사용되어 집니다. By default, the `report` method simply passes the exception to the base implementation on the parent class where the exception is logged. 기본적으로 `report` 메소드는 예외-exception가 기록되는 경우 간단하게 부모 클래스의 기본 구현체에 예외-exception를 전달합니다. However, you are free to log exceptions however you wish. 그렇지만 예외-exceptions을 원하는대로 로깅해도 됩니다. If you need to report different types of exceptions in different ways, you may use the PHP `instanceof` comparison operator: 만약 각 예외-exception 유형을 각기 다른 방법으로 기록 할 필요가 있다면, PHP의 `instanceof` 비교 연산자를 사용하면 됩니다. 

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
		if ($e instanceof CustomException)
		{
			//
		}

		return parent::report($e);
	}

The `render` method is responsible for converting the exception into an HTTP response that should be sent back to the browser. `render` 메소드는 예외-exception를 HTTP 응답으로 변환하고 브라우저에게 보내는 역할을 담당합니다. By default, the exception is passed to the base class which generates a response for you. However, you are free to check the exception type or return your own custom response. 기본적으로 예외-exception는 응답을 생성하는 기본 클래스로 전달됩니다. 하지만 예외-exception의 유형을 파악하고 사용자 지정된 응답을 반환 것도 자유롭습니다. 

The `dontReport` property of the exception handler contains an array of exception types that will not be logged. 예외-exception 처리 클래스의 `dontReport` 속성에 배열로 지정된 유형들은 로그를 남기지 않습니다. By default, exceptions resulting from 404 errors are not written to your log files. 기본적으로 404 에러는 로그파일에 기록되지 않습니다. You may add other exception types to this array as needed. 필요한 경우에 다른 예외-exception 유형들도 이 배열에 추가할 수 있습니다. 

<a name="http-exceptions"></a>
## HTTP Exceptions HTTP 예외-exceptions

Some exceptions describe HTTP error codes from the server. 어떤 예외들-exceptions은 서버에서 발생하는 HTTP 에러 코드를 나타냅니다. For example, this may be a "page not found" error (404), an "unauthorized error" (401) or even a developer generated 500 error. 예를 들어, "페이지를 찾을 수 없습니다." 에러(404)나, 인증 오류 (401) 또는 개발자가 유발하는 500 오류 같은 것들입니다. In order to return such a response, use the following: 이 같은 응답을 반환하려면 다음과 같이 수행하십시오:

	abort(404);

Optionally, you may provide a response: 선택적으로, 응답을 제공할 수 있습니다.

	abort(403, 'Unauthorized action.');

This method may be used at any time during the request's lifecycle. 이 메소드는 요청의 라이프사이클 동안이면 언제든지 사용할 수 있습니다.

### Custom 404 Error Page 404 에러의 사용자 정의 페이지

To return a custom view for all 404 errors, create a `resources/views/errors/404.blade.php` file. This view will be served on all 404 errors generated by your application. 모든 404에러들을 위한 사용자 정의 뷰를 반환하기 위해서는 `resources/views/errors/404.blade.php` 파일을 만들어야 합니다. 이 뷰파일은 애플리케이션에 의해 발생하는 모든 404 에러에 사용됩니다.

<a name="logging"></a>
## Logging 로깅

The Laravel logging facilities provide a simple layer on top of the powerful [Monolog](http://github.com/seldaek/monolog) library. 라라벨의 로그 기능은 강력한 [Monolog](http://github.com/seldaek/monolog) 라이브러리위에 간단한 레이어를 제공합니다. By default, Laravel is configured to create daily log files for your application which are stored in the `storage/logs` directory. 기본적으로 라라벨은 일별 로그 파일을 생성하도록 설정되어 있고, 로그 파일들은 `storage/logs` 에 저장됩니다. You may write information to the log like so:  다음과 같이 하여 로그에 정보를 기록할 수도 있습니다:

	Log::info('This is some useful information.');

	Log::warning('Something could be going wrong.');

	Log::error('Something is really going wrong.');

The logger provides the seven logging levels defined in [RFC 5424](http://tools.ietf.org/html/rfc5424): **debug**, **info**, **notice**, **warning**, **error**, **critical**, and **alert**. 로그는 [RFC 5424](http://tools.ietf.org/html/rfc5424) 에 정의되어있는 **debug**, **info**, **notice**, **warning**, **error**, **critical**, 그리고 **alert**의 7가지 로깅 레벨을 제공합니다.

An array of contextual data may also be passed to the log methods: 컨텍스트 데이터 배열도 로그 메소드에 전달할 수 있습니다.

	Log::info('Log message', ['context' => 'Other helpful information']);

Monolog has a variety of additional handlers you may use for logging. If needed, you may access the underlying Monolog instance being used by Laravel: Monolog에는 로그에 사용할 수있는 기타 다양한 핸들러가 준비되어 있습니다. 필요하다면 라라벨이 내부에서 사용하는 Monolog 인스턴스에 액세스 할 수 있습니다.

	$monolog = Log::getMonolog();

You may also register an event to catch all messages passed to the log: 로그에 전달되는 모든 메시지를 잡기 위해 이벤트를 등록 할 수도 있습니다.

#### Registering A Log Event Listener
#### 로그 리스너 등록하기

	Log::listen(function($level, $message, $context)
	{
		//
	});
