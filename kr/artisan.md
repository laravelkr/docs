# Artisan CLI 아티즌 CLI

- [introduction 소개](#introduction)
- [usage 사용법](#usage)
- [Calling Commands Outside Of CLI CLI 외부에서 명령어 호출하는 법](#calling-commands-outside-of-cli)
- [Scheduling Artisan Commands 아티즌 명령어 스케줄링](#scheduling-artisan-commands)

<a name="introduction"></a>
## Introduction 소개

Artisan is the name of the command-line interface included with Laravel. 아티즌(Artisan)은 라라벨에 포함된 커맨드라인 인터페이스(CLI)의 이름입니다. It provides a number of helpful commands for your use while developing your application. 아티즌은 애플리케이션 개발에 도움을 주는 많은 명령어들을 제공합니다. It is driven by the powerful Symfony Console component. 아티즌은 강력한 Symfony 콘솔 콤포넌트에서 동작합니다.

<a name="usage"></a>
## Usage 사용법

#### Listing All Available Commands
#### 사용가능한 전체 아티즌 명령어 목록 확인

To view a list of all available Artisan commands, you may use the `list` command: 사용가능한 아티즌의 전체 명령어들을 확인하려면 `list` 명령어를 입력하면 됩니다:


	php artisan list

#### Viewing The Help Screen For A Command
#### 명령어의 도움말 화면 보기

Every command also includes a "help" screen which displays and describes the command's available arguments and options. 모든 명령어는 각각의 설명과 사용 가능한 인수및 옵션을 보여주는 "도움말" 화면을 포함하고 있습니다. To view a help screen, simply precede the name of the command with `help`: 도움말 화면을 표시하려면 명령어 앞에 `help`를 입력하십시오.

	php artisan help migrate

#### Specifying The Configuration Environment
#### 명령어의 구동 환경 지정하기

You may specify the configuration environment that should be used while running a command using the `--env` switch: `--env` 스위치 옵션을 통해서 명령어가 구동되는 환경을 지정해 줄 수 있습니다: 

	php artisan migrate --env=local

#### Displaying Your Current Laravel Version
#### 설치된 현재 라라벨 버전 확인하기

 You may also view the current version of your Laravel installation using the `--version` option: 설치되어 있는 라라벨의 버전을 확인하려면 `--version` 옵션을 입력하면 됩니다:

	php artisan --version

<a name="calling-commands-outside-of-cli"></a>
## Calling Commands Outside Of CLI CLI 외부에서 명령어 호출하기

Sometimes you may wish to execute an Artisan command outside of the CLI. 때로는 CLI 외부에서 아티즌 명령어를 실행하기를 원할 수도 있습니다. For example, you may wish to fire an Artisan command from an HTTP route. 예를 들어 HTTP 라우트를 통해서 아티즘 명령어를 실행하기를 원할 수도 있습니다. Just use the `Artisan` facade: 이럴 때에는 그냥 `Artisan` 파사드를 사용하면 됩니다. 

	Route::get('/foo', function()
	{
		$exitCode = Artisan::call('command:name', ['--option' => 'foo']);

		//
	});

You may even queue Artisan commands so they are processed in the background by your [queue workers](/docs/{{version}}/queues): [queue workers](/docs/version/queues)를 통해서 아티즌 명령어가 큐를 통해서 백그라운드에서 동작하도록 할 수도 있습니다. 

	Route::get('/foo', function()
	{
		Artisan::queue('command:name', ['--option' => 'foo']);

		//
	});

<a name="scheduling-artisan-commands"></a>
## Scheduling Artisan Commands 아티즌 명령어 스케줄링

In the past, developers have generated a Cron entry for each console command they wished to schedule. 이전에는 개발자가 스케줄링을 위한 크론(Cron) 작업을 등록하기 위해서 매번 콘솔에 명령어를 입력해야 했습니다. However, this is a headache. 하지만 이런 작업은 골치 아픈일입니다. Your console schedule is no longer in source control, and you must SSH into your server to add the Cron entries. 콘솔 스케줄 작업은 소스가 관리되지도 않고 크론에 작업을 추가하기 위해서는 SSH 로 서버에 접속해야만 했습니다. Let's make our lives easier. 좀 더 쉬운 방법을 찾아 봅시다. The Laravel command scheduler allows you to fluently and expressively define your command schedule within Laravel itself, and only a single Cron entry is needed on your server. 라라벨 명령 스케줄러를 사용하게되면 서버에 크론 항목을 단 하나 추가하는 것만으로도, 우아하고 좀 더 다양한 명령어 스케줄 실행 을 정의할 수 있게 해줍니다. 

Your command schedule is stored in the `app/Console/Kernel.php` file. 여러분의 명령어 스케줄은 `app/Console/Kernel.php`파일에 위치하고 있습니다. Within this class you will see a `schedule` method. 이 클래스에서 `schedule`메소드를 볼 수 있을 것입니다. To help you get started, a simple example is included with the method. 좀 더 쉽게 이해하기 위해서 메소드에는 간단한 예제가 포함되어 있습니다. You are free to add as many scheduled jobs as you wish to the `Schedule` object. 원하는 만큼 `Schedule` 오브젝트를 사용하여 스케줄링들을 추가하면 됩니다. The only Cron entry you need to add to your server is this: 서버에서 크론에 추가해야할 하나의 작업은 다음과 같습니다. 

	* * * * * php /path/to/artisan schedule:run 1>> /dev/null 2>&1

This Cron will call the Laravel command scheduler every minute. 이 크론 작업은 매분마다 라라벨 명령 스케줄러를 호출합니다. Then, Laravel evaluates your scheduled jobs and runs the jobs that are due. It couldn't be easier! 그러면 라라벨은 등록된 스케줄 작업들을 계산하여 실행하게 됩니다. 정말 참 쉽죠!

### More Scheduling Examples 스케줄링 예제

Let's look at a few more scheduling examples:
다음의 몇가지 스케줄링 예제를 살펴보십시오. 

#### Scheduling Closures 클로저를 통한 스케줄링

	$schedule->call(function()
	{
		// Do some task...

	})->hourly();

#### Scheduling Terminal Commands 터미널을 실행하는 스케줄링

	$schedule->exec('composer self-update')->daily();

#### Manual Cron Expression 직접 크론 표현식으로 등록하는 법

	$schedule->command('foo')->cron('* * * * *');

#### Frequent Jobs 일정한 주기로 실행되는 작업

	$schedule->command('foo')->everyFiveMinutes();

	$schedule->command('foo')->everyTenMinutes();

	$schedule->command('foo')->everyThirtyMinutes();

#### Daily Jobs 매일 실행되는 작업

	$schedule->command('foo')->daily();

#### Daily Jobs At A Specific Time (24 Hour Time) 특정 시작에 실행되는 작업(24시 기준)

	$schedule->command('foo')->dailyAt('15:00');

#### Twice Daily Jobs 매일 2번씩 실행되는 작업

	$schedule->command('foo')->twiceDaily();

#### Job That Runs Every Weekday 평일에 실행되는 작업

	$schedule->command('foo')->weekdays();

#### Weekly Jobs 매주 실행되는 작업

	$schedule->command('foo')->weekly();

	// Schedule weekly job for specific day (0-6) and time...
	$schedule->command('foo')->weeklyOn(1, '8:00');

#### Monthly Jobs 매월 실행되는 작업

	$schedule->command('foo')->monthly();

#### Job That Runs On Specific Days 특정 요일에 실행되는 작업

	$schedule->command('foo')->mondays();
	$schedule->command('foo')->tuesdays();
	$schedule->command('foo')->wednesdays();
	$schedule->command('foo')->thursdays();
	$schedule->command('foo')->fridays();
	$schedule->command('foo')->saturdays();
	$schedule->command('foo')->sundays();

#### Prevent Jobs From Overlapping
#### 중복 작업 방지하기

By default, scheduled jobs will be run even if the previous instance of the job is still running. 기본적으로 예약된 작업은 이전 인스턴스가 여전히 실행중이더라도 실행됩니다. To prevent this, you may use the `withoutOverlapping` method: 이를 방지하려면 `withoutOverlapping` 메소드를 사용하면 됩니다. 

	$schedule->command('foo')->withoutOverlapping();

In this example, the `foo` command will be run every minute if it is not already running. 이 예제에서 `foo` 명령어는 이미 실행되고 있지 않은 경우에 매분마다 실행됩니다. 

#### Limit The Environment The Jobs Should Run In 구동 환경에 따라서 작업 실행해야 할 때

	$schedule->command('foo')->monthly()->environments('production');

#### Indicate The Job Should Run Even When Application Is In Maintenance Mode 공사중모드일때 실행되도록 지정하기

	$schedule->command('foo')->monthly()->evenInMaintenanceMode();

#### Only Allow Job To Run When Callback Is True 콜백 결과가 True 일때만 실행

	$schedule->command('foo')->monthly()->when(function()
	{
		return true;
	});

#### E-mail The Output Of A Scheduled Job 스케줄 작업 결과를 이메일로 받아보기

	$schedule->command('foo')->sendOutputTo($filePath)->emailOutputTo('foo@example.com');

> **Note주의:** You must send the output to a file before it can be mailed. 이메일을 보내기 전에 파일로 결과를 저장해야 합니다. 

#### Send The Output Of The Scheduled Job To A Given Location 특정 파일로 스케줄 작업 결과 저장하기

	$schedule->command('foo')->sendOutputTo($filePath);

#### Ping A Given URL After The Job Runs 작업이 실행된 후에 특정 URL 접속하기

	$schedule->command('foo')->thenPing($url);

Using the `thenPing($url)` feature requires the Guzzle HTTP library. You can add Guzzle 5 to your project by adding the following line to your `composer.json` file:

`thenPing($url)`기능을 사용하려면 Guzzle HTTP 라이브러리가 필요합니다. `composer.json` 파일에 다음 라인을 추가하여 Guzzle 5 를 추가할 수 있습니다. 

	"guzzlehttp/guzzle": "~5.0"

