# 아티즌 CLI(Artisan CLI)

- [소개](#introduction)
- [사용법](#usage)
- [CLI 외부에서 명령어 호출하는 법](#calling-commands-outside-of-cli)
- [아티즌 명령어 스케줄링](#scheduling-artisan-commands)

<a name="introduction"></a>
## 소개

아티즌(Artisan)은 라라벨에 포함된 커맨드라인 인터페이스(CLI)의 이름입니다. 아티즌은 어플리케이션 개발에 도움을 주는 많은 명령어를 제공합니다. 아티즌은 강력한 Symfony 콘솔 컴포넌트에서 동작합니다.

<a name="usage"></a>
## 사용법

#### 사용 가능한 전체 아티즌 명령어 목록 확인

사용 가능한 아티즌의 전체 명령어를 확인하려면 `list` 명령어를 입력하면 됩니다:

	php artisan list

#### 명령어의 도움말 화면 보기

모든 명령어는 각각의 설명과 사용 가능한 인수 및 옵션을 보여주는 "도움말" 화면을 포함하고 있습니다. 도움말 화면을 표시하려면 명령어 앞에 `help`를 입력하십시오:

	php artisan help migrate

#### 명령어의 구동 환경 지정하기

`--env` 스위치 옵션을 통해서 명령어를 구동하는 환경을 지정해 줄 수 있습니다: 

	php artisan migrate --env=local

#### 설치된 현재 라라벨 버전 확인하기

설치된 라라벨의 버전을 확인하려면 `--version` 옵션을 입력하면 됩니다:

	php artisan --version

<!--chak-comment-아티즌CLI(ArtisanCLI)사용법-->
<a name="calling-commands-outside-of-cli"></a>
## CLI 외부에서 명령어 호출하기

때로는 CLI 외부에서 아티즌 명령어를 실행하기를 원할 수도 있습니다. 예를 들어, HTTP 라우트를 통해서 아티즘 명령어를 실행하기를 원할 수도 있습니다. 이럴 때는 그냥 `Artisan` 파사드를 사용하면 됩니다. 

	Route::get('/foo', function()
	{
		$exitCode = Artisan::call('command:name', ['--option' => 'foo']);

		//
	});

[queue workers](/docs/5.0/queues)를 통해서 아티즌 명령어가 큐를 통해서 백그라운드에서 동작하도록 할 수도 있습니다:

	Route::get('/foo', function()
	{
		Artisan::queue('command:name', ['--option' => 'foo']);

		//
	});

<!--chak-comment-CLI-외부에서-명령어-호출하기-->
<a name="scheduling-artisan-commands"></a>
## 아티즌 명령어 스케줄링

이전에는 개발자가 스케줄링을 위한 크론(Cron) 작업을 등록하기 위해서 매번 콘솔에 명령어를 입력해야 했습니다. 하지만 이런 작업은 골치 아픈 일입니다. 콘솔 스케줄 작업은 소스가 관리되지도 않고 크론에 작업을 추가하기 위해서는 SSH로 서버에 접속해야만 했습니다. 좀 더 쉬운 방법을 찾아봅시다. 라라벨 명령 스케줄러를 사용하게되면 서버에 크론 항목을 단 하나 추가하는 것만으로도, 우아하고 좀 더 다양한 명령어 스케줄 실행을 정의할 수 있게 해줍니다. 

여러분의 명령어 스케줄은 `app/Console/Kernel.php`파일에 있습니다. 이 클래스에서 `schedule`메소드를 볼 수 있을 것입니다. 좀 더 쉽게 이해하기 위해서 메소드에는 간단한 예제가 포함되어 있습니다. 원하는 만큼 `Schedule` 오브젝트를 사용하여 스케줄링들을 추가하면 됩니다. 서버에서 크론에 추가해야 할 하나의 작업은 다음과 같습니다:

	* * * * * php /path/to/artisan schedule:run 1>> /dev/null 2>&1

이 크론 작업은 매분 라라벨 명령 스케줄러를 호출합니다. 그러면 라라벨은 등록된 스케줄 작업들을 계산하여 실행하게 됩니다. 정말 참 쉽죠!

### 스케줄링 예제

다음의 몇 가지 스케줄링 예제를 살펴보겠습니다:

#### 클로저를 통한 스케줄링

	$schedule->call(function()
	{
		// Do some task...

	})->hourly();

#### 터미널을 실행하는 스케줄링

	$schedule->exec('composer self-update')->daily();

#### 직접 크론 표현식으로 등록하는 법

	$schedule->command('foo')->cron('* * * * *');

#### 일정한 주기로 실행되는 작업

	$schedule->command('foo')->everyFiveMinutes();

	$schedule->command('foo')->everyTenMinutes();

	$schedule->command('foo')->everyThirtyMinutes();

#### 매일 실행되는 작업

	$schedule->command('foo')->daily();

#### 특정 시각에 실행되는 작업(24시 기준)

	$schedule->command('foo')->dailyAt('15:00');

#### 매일 두 번씩 실행되는 작업

	$schedule->command('foo')->twiceDaily();

#### 평일에 실행되는 작업

	$schedule->command('foo')->weekdays();

#### 매주 실행되는 작업

	$schedule->command('foo')->weekly();

	// Schedule weekly job for specific day (0-6) and time...
	$schedule->command('foo')->weeklyOn(1, '8:00');

#### 매월 실행되는 작업

	$schedule->command('foo')->monthly();

#### 특정 요일에 실행되는 작업

	$schedule->command('foo')->mondays();
	$schedule->command('foo')->tuesdays();
	$schedule->command('foo')->wednesdays();
	$schedule->command('foo')->thursdays();
	$schedule->command('foo')->fridays();
	$schedule->command('foo')->saturdays();
	$schedule->command('foo')->sundays();

#### 중복 작업 방지하기

기본적으로 예약된 작업은 이전 인스턴스가 여전히 실행 중이더라도 실행됩니다. 이를 방지하려면 `withoutOverlapping` 메소드를 사용하면 됩니다:

	$schedule->command('foo')->withoutOverlapping();

이 예제에서 `foo` 명령어는 이미 실행되고 있지 않은 경우에 매분 실행됩니다. 

#### 구동 환경에 따라서 작업 실행해야 할 때

	$schedule->command('foo')->monthly()->environments('production');

#### 공사 중 모드일 때 실행하도록 지정하기

	$schedule->command('foo')->monthly()->evenInMaintenanceMode();

#### 콜백 결과가 True 일 때만 실행

	$schedule->command('foo')->monthly()->when(function()
	{
		return true;
	});

#### 스케줄 작업 결과를 이메일로 받아보기

	$schedule->command('foo')->sendOutputTo($filePath)->emailOutputTo('foo@example.com');

> **주의:** 이메일을 보내기 전에 파일로 결과를 저장해야 합니다. 

#### 특정 파일로 스케줄 작업 결과를 저장하기

	$schedule->command('foo')->sendOutputTo($filePath);

#### 작업이 실행된 후에 특정 URL 접속하기

	$schedule->command('foo')->thenPing($url);

`thenPing($url)`기능을 사용하려면 Guzzle HTTP 라이브러리가 필요합니다. `composer.json` 파일에 다음 라인을 추가하여 Guzzle 5를 추가할 수 있습니다. 

	"guzzlehttp/guzzle": "~5.0"

<!--chak-comment-아티즌-명령어-스케줄링-->
