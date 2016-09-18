# 에러와 로깅(Errors & Logging)

- [설정](#configuration)
- [에러 처리](#handling-errors)
- [HTTP 예외](#http-exceptions)
- [로깅](#logging)

<a name="configuration"></a>
## 설정

어플리케이션의 로그 기능은 `Illuminate\Foundation\Bootstrap\ConfigureLogging` 부트스트랩 클래스에서 설정됩니다. 이 클래스는 `config/app.php` 설정 파일의 `log` 옵션을 참조합니다. 

기본적으로 로거는 일단위 로그 파일을 기록하도록 설정되어 있습니다만 필요한경우 이 주기를 변경할 수 있습니다. 라라벨은 인기있는 [Monolog](https://github.com/Seldaek/monolog) 로깅 라이브러리를 사용하고 있으므로, Monolog가 제공하는 다양한 핸들러를 이용할 수 있습니다. 

예를 들어, 일별로 기록되는 로그 파일 대신 하나의 로그 파일을 구성하고 싶다면 `config/app.php` 설정파일을 다음과 같이 변경하면 됩니다:

	'log' => 'single'

별다른 구성이 없이도 라라벨은 `single`, `daily`, `syslog` 그리고 `errorlog` 로깅 모드를 지원합니다. 그러나 응용 프로그램에 대한 로깅을 원하는대로 사용자 정의하고자 한다면, `ConfigureLogging` 부트스트랩 클래스를 재정의 하여 사용할 수도 있습니다. 

### 오류의 상세정보

`config/app.php` 설정 파일의 `app.debug` 설정 옵션을 통해서 브라우저에 출력되는 어플리케이션의 오류 정보를 제어 할 수 있습니다. 기본적으로 이 옵션은 `.env` 파일에 설정되어있는 `APP_DEBUG` 환경 변수를 반영하도록 설정되어 있습니다.

로컬 개발환경을 구성중이라면 `APP_DEBUG` 환경 변수를 `true`로 구성해야 할것입니다. 
**실제 제품 구동 환경이라면, 이 값은 항상 `false`가 되어야 할것입니다. **

<!--chak-comment-에러와-로깅(Errors---Logging)-설정-->

<a name="handling-errors"></a>
## 에러 처리

모든 예외-exceptions은 `App\Exceptions\Handler` 클래스에 의해서 처리됩니다. 이 클래스는 `report`와 `render` 메소드로 구성되어 있습니다. 

`report`메소드는 예외-exceptions를 로깅하거나 [BugSnag](https://bugsnag.com)와 같은 별도의 외부 서비스로 보내는데 사용되어 집니다. 기본적으로 `report` 메소드는 예외-exception가 기록되는 경우 간단하게 부모 클래스의 기본 구현체에 예외-exception를 전달합니다. 그렇지만 예외-exceptions을 원하는대로 로깅해도 됩니다. 만약 각 예외-exception 유형을 각기 다른 방법으로 기록 할 필요가 있다면, PHP의 `instanceof` 비교 연산자를 사용하면 됩니다:

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

`render` 메소드는 예외-exception를 HTTP 응답으로 변환하고 브라우저에게 보내는 역할을 담당합니다. 기본적으로 예외-exception는 응답을 생성하는 기본 클래스로 전달됩니다. 하지만 예외-exception의 유형을 파악하고 사용자 지정된 응답을 반환 것도 자유롭습니다. 

예외-exception 처리 클래스의 `dontReport` 속성에 배열로 지정된 유형들은 로그를 남기지 않습니다. 기본적으로 404 에러는 로그파일에 기록되지 않습니다. 필요한 경우에 다른 예외-exception 유형들도 이 배열에 추가할 수 있습니다. 

<!--chak-comment-에러와-로깅(Errors---Logging)-에러-처리-->

<a name="http-exceptions"></a>
## HTTP 예외-exceptions

어떤 예외들-exceptions은 서버에서 발생하는 HTTP 에러 코드를 나타냅니다. 예를 들어, "페이지를 찾을 수 없습니다." 에러(404)나, 인증 오류 (401) 또는 개발자가 유발하는 500 오류 같은 것들입니다. 이 같은 응답을 반환하려면 다음과 같이 수행하십시오:

	abort(404);

선택적으로, 응답을 제공할 수 있습니다.

	abort(403, 'Unauthorized action.');

이 메소드는 요청의 라이프사이클 동안이면 언제든지 사용할 수 있습니다.

### 404 에러의 사용자 정의 페이지

모든 404에러들을 위한 사용자 정의 뷰를 반환하기 위해서는 `resources/views/errors/404.blade.php` 파일을 만들어야 합니다. 이 뷰파일은 어플리케이션에 의해 발생하는 모든 404 에러에 사용됩니다.

<!--chak-comment-에러와-로깅(Errors---Logging)-HTTP-예외-exceptions-->

<a name="logging"></a>
## 로깅

라라벨의 로그 기능은 강력한 [Monolog](http://github.com/seldaek/monolog) 라이브러리위에 간단한 레이어를 제공합니다. 기본적으로 라라벨은 일별 로그 파일을 생성하도록 설정되어 있고, 로그 파일들은 `storage/logs`에 저장됩니다. 다음과 같이 하여 로그에 정보를 기록할 수도 있습니다:

	Log::info('This is some useful information.');

	Log::warning('Something could be going wrong.');

	Log::error('Something is really going wrong.');

로그는 [RFC 5424](http://tools.ietf.org/html/rfc5424)에 정의되어있는 **debug**, **info**, **notice**, **warning**, **error**, **critical**, 그리고 **alert**의 7가지 로깅 레벨을 제공합니다.

컨텍스트 데이터 배열도 로그 메소드에 전달할 수 있습니다.

	Log::info('Log message', ['context' => 'Other helpful information']);

Monolog에는 로그에 사용할 수있는 기타 다양한 핸들러가 준비되어 있습니다. 필요하다면 라라벨이 내부에서 사용하는 Monolog 인스턴스에 액세스 할 수 있습니다:

	$monolog = Log::getMonolog();

로그에 전달되는 모든 메시지를 잡기 위해 이벤트를 등록 할 수도 있습니다:

#### 로그 리스너 등록하기

	Log::listen(function($level, $message, $context)
	{
		//
	});

<!--chak-comment-에러와-로깅(Errors---Logging)-로깅-->
