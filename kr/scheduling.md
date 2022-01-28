# Task Scheduling
# 작업 스케줄링하기

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Defining Schedules](#defining-schedules)
- [스케줄 정의하기](#defining-schedules)
    - [Scheduling Artisan Commands](#scheduling-artisan-commands)
    - [아티즌 명령어 스케줄링](#scheduling-artisan-commands)
    - [Scheduling Queued Jobs](#scheduling-queued-jobs)
    - [Queued Jobs 스케줄링](#scheduling-queued-jobs)
    - [Scheduling Shell Commands](#scheduling-shell-commands)
    - [쉘 명령어 스케줄링](#scheduling-shell-commands)
    - [Schedule Frequency Options](#schedule-frequency-options)
    - [스케줄링 주기 관련 옵션](#schedule-frequency-options)
    - [Timezones](#timezones)
    - [타임존](#timezones)
    - [Preventing Task Overlaps](#preventing-task-overlaps)
    - [작업의 중복 방지](#preventing-task-overlaps)
    - [Running Tasks On One Server](#running-tasks-on-one-server)
    - [한 서버에서 작업 실행하기](#running-tasks-on-one-server)
    - [Background Tasks](#background-tasks)
    - [백그라운드 작업](#background-tasks)
    - [Maintenance Mode](#maintenance-mode)
    - [점검 모드](#maintenance-mode)
- [Running The Scheduler](#running-the-scheduler)
- [스케줄러 실행하기](#running-the-scheduler)
    - [Running The Scheduler Locally](#running-the-scheduler-locally)
    - [로컬에서 스케줄러 실행하기](#running-the-scheduler-locally)
- [Task Output](#task-output)
- [작업 출력](#task-output)
- [Task Hooks](#task-hooks)
- [작업 후킹](#task-hooks)
- [Events](#events)
- [이벤트](#events)

<a name="introduction"></a>
## Introduction
## 시작하기

In the past, you may have written a cron configuration entry for each task you needed to schedule on your server. However, this can quickly become a pain because your task schedule is no longer in source control and you must SSH into your server to view your existing cron entries or add additional entries.

이전까지는, 서버에서 예약해야 하는 각 작업에 대해 Cron 구성 항목을 생성해야 했습니다. 그러나 작업 스케쥴은 더 이상 소스 컨트롤로 관리되지 않고, 기존 Cron 항목을 보거나 추가 항목을 추가하려면 SSH를 통해 서버에 연결해야 하기 때문에 이 작업은 문제가 될 수도 있습니다.

Laravel's command scheduler offers a fresh approach to managing scheduled tasks on your server. The scheduler allows you to fluently and expressively define your command schedule within your Laravel application itself. When using the scheduler, only a single cron entry is needed on your server. Your task schedule is defined in the `app/Console/Kernel.php` file's `schedule` method. To help you get started, a simple example is defined within the method.

라라벨의 명령 스케줄러는 서버에서 예약된 작업을 관리하는 새로운 접근 방식을 제공합니다. 스케줄러를 사용하면 라라벨 애플리케이션 자체 내에서 명령 스케줄을 유창하고 표현적으로 정의할 수 있습니다. 스케줄러를 사용할 때 서버에 하나의 Cron 항목만 필요합니다. 작업 일정은 `app/Console/Kernel.php` 파일의 `schedule` 메소드에 정의되어 있습니다. 시작하는 데 도움이 되도록 메서드 내에 간단한 예제가 정의되어 있습니다.

<a name="defining-schedules"></a>
## Defining Schedules
## 스케줄 정의하기

You may define all of your scheduled tasks in the `schedule` method of your application's `App\Console\Kernel` class. To get started, let's take a look at an example. In this example, we will schedule a closure to be called every day at midnight. Within the closure we will execute a database query to clear a table:

애플리케이션의 `App\Console\Kernel` 클래스의 `schedule` 메소드에서 모든 스케줄링된 작업을 정의할 수 있습니다. 시작하기 위해 예제를 살펴보겠습니다. 이 예에서는 매일 자정에 클로저가 호출되도록 스케줄링합니다. 클로저 내에서 우리는 테이블을 지우기 위해 데이터베이스 쿼리를 실행할 것입니다:

    <?php

    namespace App\Console;

    use Illuminate\Console\Scheduling\Schedule;
    use Illuminate\Foundation\Console\Kernel as ConsoleKernel;
    use Illuminate\Support\Facades\DB;

    class Kernel extends ConsoleKernel
    {
        /**
         * The Artisan commands provided by your application.
         *
         * @var array
         */
        protected $commands = [
            //
        ];

        /**
         * Define the application's command schedule.
         *
         * @param  \Illuminate\Console\Scheduling\Schedule  $schedule
         * @return void
         */
        protected function schedule(Schedule $schedule)
        {
            $schedule->call(function () {
                DB::table('recent_users')->delete();
            })->daily();
        }
    }

In addition to scheduling using closures, you may also schedule [invokable objects](https://secure.php.net/manual/en/language.oop5.magic.php#object.invoke). Invokable objects are simple PHP classes that contain an `__invoke` method:

클로저를 사용하여 스케줄링 하는 것 외에도 [호출 가능한 개체](https://secure.php.net/manual/en/language.oop5.magic.php#object.invoke)를 예약할 수도 있습니다. 호출 가능한 객체는 `__invoke` 메서드가 포함된 간단한 PHP 클래스입니다.

    $schedule->call(new DeleteRecentUsers)->daily();

If you would like to view an overview of your scheduled tasks and the next time they are scheduled to run, you may use the `schedule:list` Artisan command:

스케줄링된 작업의 개요와 다음에 실행하도록 스케줄링된 시간을 보려면 `schedule:list` 아티즌 명령을 사용할 수 있습니다.

```nothing
php artisan schedule:list
```

<a name="scheduling-artisan-commands"></a>
### Scheduling Artisan Commands
### 아티즌 명령어 스케줄링

In addition to scheduling closures, you may also schedule [Artisan commands](/docs/{{version}}/artisan) and system commands. For example, you may use the `command` method to schedule an Artisan command using either the command's name or class.

클로저 호출 외에도 [아티즌 명령어](/docs/{{version}}/artisan) 및 시스템 명령을 스케줄링할 수도 있습니다. 예를 들어 `command` 메소드를 사용하여 커맨드의 이름이나 클래스를 사용하여 아티즌 커멘드을 스케줄링할 수 있습니다.

When scheduling Artisan commands using the command's class name, you may pass an array of additional command-line arguments that should be provided to the command when it is invoked:

커멘드의 클래스 이름을 사용하여 아티즌 명령을 스케줄링할 때 커멘드가 호출될 때 커멘드에 제공되어야 하는 추가 명령줄 인수의 배열을 전달할 수 있습니다.

    use App\Console\Commands\SendEmailsCommand;

    $schedule->command('emails:send Taylor --force')->daily();

    $schedule->command(SendEmailsCommand::class, ['Taylor', '--force'])->daily();

<a name="scheduling-queued-jobs"></a>
### Scheduling Queued Jobs
### Queued Jobs 스케줄링

The `job` method may be used to schedule a [queued job](/docs/{{version}}/queues). This method provides a convenient way to schedule queued jobs without using the `call` method to define closures to queue the job:

`job` 메소드는 [대기 중인 작업](/docs/{{버전}}/queues)을 스케줄링하는 데 사용됩니다. 이 메서드는 'call' 메서드를 사용하여 작업을 대기열에 추가할 클로저를 정의하지 않고 대기열에 있는 작업을 스케줄링하는 편리한 방법을 제공합니다.

    use App\Jobs\Heartbeat;

    $schedule->job(new Heartbeat)->everyFiveMinutes();

Optional second and third arguments may be provided to the `job` method which specifies the queue name and queue connection that should be used to queue the job:

선택적인 두 번째 및 세 번째 인수는 작업을 대기열에 넣는 데 사용해야 하는 대기열 이름과 대기열 연결을 지정하는 `job` 메서드에 제공됩니다.

    use App\Jobs\Heartbeat;

    // Dispatch the job to the "heartbeats" queue on the "sqs" connection...
    // 작업을 "sqs" 연결의 "heartbeats" 대기열로 디스패치합니다...

    $schedule->job(new Heartbeat, 'heartbeats', 'sqs')->everyFiveMinutes();

<a name="scheduling-shell-commands"></a>
### Scheduling Shell Commands
### 쉘 명령어 스케줄링

The `exec` method may be used to issue a command to the operating system:

`exec` 메소드는 커맨드는 OS에 직접 명령어를 실행하는데 사용됩니다.

    $schedule->exec('node /home/forge/script.js')->daily();

<a name="schedule-frequency-options"></a>
### Schedule Frequency Options
### 스케줄링 주기 관련 옵션

We've already seen a few examples of how you may configure a task to run at specified intervals. However, there are many more task schedule frequencies that you may assign to a task:

지정된 간격으로 실행되도록 작업을 구성하는 방법에 대한 몇 가지 예를 이미 보았습니다. 그러나 작업에 할당할 수 있는 작업 일정 빈도가 더 많습니다.

Method  | Description
------------- | -------------
`->cron('* * * * *');`  |  Run the task on a custom cron schedule
`->everyMinute();`  |  Run the task every minute
`->everyTwoMinutes();`  |  Run the task every two minutes
`->everyThreeMinutes();`  |  Run the task every three minutes
`->everyFourMinutes();`  |  Run the task every four minutes
`->everyFiveMinutes();`  |  Run the task every five minutes
`->everyTenMinutes();`  |  Run the task every ten minutes
`->everyFifteenMinutes();`  |  Run the task every fifteen minutes
`->everyThirtyMinutes();`  |  Run the task every thirty minutes
`->hourly();`  |  Run the task every hour
`->hourlyAt(17);`  |  Run the task every hour at 17 minutes past the hour
`->everyTwoHours();`  |  Run the task every two hours
`->everyThreeHours();`  |  Run the task every three hours
`->everyFourHours();`  |  Run the task every four hours
`->everySixHours();`  |  Run the task every six hours
`->daily();`  |  Run the task every day at midnight
`->dailyAt('13:00');`  |  Run the task every day at 13:00
`->twiceDaily(1, 13);`  |  Run the task daily at 1:00 & 13:00
`->weekly();`  |  Run the task every sunday at 00:00
`->weeklyOn(1, '8:00');`  |  Run the task every week on Monday at 8:00
`->monthly();`  |  Run the task on the first day of every month at 00:00
`->monthlyOn(4, '15:00');`  |  Run the task every month on the 4th at 15:00
`->twiceMonthly(1, 16, '13:00');`  |  Run the task monthly on the 1st and 16th at 13:00
`->lastDayOfMonth('15:00');` | Run the task on the last day of the month at 15:00
`->quarterly();` |  Run the task on the first day of every quarter at 00:00
`->yearly();`  |  Run the task on the first day of every year at 00:00
`->yearlyOn(6, 1, '17:00');`  |  Run the task every year on June 1st at 17:00
`->timezone('America/New_York');` | Set the timezone

메소드  | 설명
------------- | -------------
`->cron('* * * * *');`  |  지정한 cron 형태로 작업 실행
`->everyMinute();`  |  매분 마다 작업 실행
`->everyTwoMinutes();`  |  2분마다 작업 실행
`->everyThreeMinutes();`  |  3분마다 작업 실행
`->everyFourMinutes();`  |  4분마다 작업 실행
`->everyFiveMinutes();`  |  5분 간격으로 작업 실행
`->everyTenMinutes();`  |  10분 간격으로 작업 실행
`->everyFifteenMinutes();`  |  15분 간격으로 작업 실행
`->everyThirtyMinutes();`  |  30분 간격으로 작업 실행
`->hourly();`  |  1시간 간격으로 작업 실행
`->hourlyAt(17);`  |  매시간 17분에 실행
`->everyTwoHours();`  |  2시간마다 작업 실행
`->everyThreeHours();`  |  3시간마다 작업 실행
`->everyFourHours();`  |  4시간마다 작업 실행
`->everySixHours();`  |  6시간마다 작업 실행
`->daily();`  |  한밤중을 기준으로 하루에 한번 작업 실행
`->dailyAt('13:00');`  |  매일 13:00에 작업 실행
`->twiceDaily(1, 13);`  |  하루중 1:00 & 13:00 에 작업 실행(총2번)
`->weekly();`  |  매주 일요일 00:00 에 작업 실행
`->weeklyOn(1, '8:00');`  |  매주 월요일 8시에 작업 실행
`->monthly();`  |  매달 1일 00:00 에 작업 실행
`->monthlyOn(4, '15:00');`  |  매달 4일 15:00분에 작업 실행
`->twiceMonthly(1, 16, '13:00');`  |  매월 1일과 16일 13시에 작업 실행
`->lastDayOfMonth('15:00');` | 매월 말일 15:00에 작업 실행
`->quarterly();` |  분기별 첫번째 날 00:00 에 작업 실행
`->yearly();`  |  매년 1월1일 00:00 에 작업 실행
`->yearlyOn(6, 1, '17:00');`  |  매년 6월 1일 17:00에 작업 실행
`->timezone('America/New_York');` | 타임존 지정

These methods may be combined with additional constraints to create even more finely tuned schedules that only run on certain days of the week. For example, you may schedule a command to run weekly on Monday:

이 메소드와 추가적인 제한들을 조합하면 특정 요일에만 실행하는 세밀한 스케줄을 생성할 수 있습니다. 예를 들어 매주 월요일에 커맨드가 실행하도록 스케줄링을 지정 할 수 있습니다.

    // Run once per week on Monday at 1 PM...
    $schedule->call(function () {
        //
    })->weekly()->mondays()->at('13:00');

    // Run hourly from 8 AM to 5 PM on weekdays...
    $schedule->command('foo')
              ->weekdays()
              ->hourly()
              ->timezone('America/Chicago')
              ->between('8:00', '17:00');

A list of additional schedule constraints may be found below:

추가 일정 제약 목록은 아래에서 찾을 수 있습니다.

Method  | Description
------------- | -------------
`->weekdays();`  |  Limit the task to weekdays
`->weekends();`  |  Limit the task to weekends
`->sundays();`  |  Limit the task to Sunday
`->mondays();`  |  Limit the task to Monday
`->tuesdays();`  |  Limit the task to Tuesday
`->wednesdays();`  |  Limit the task to Wednesday
`->thursdays();`  |  Limit the task to Thursday
`->fridays();`  |  Limit the task to Friday
`->saturdays();`  |  Limit the task to Saturday
`->days(array|mixed);`  |  Limit the task to specific days
`->between($startTime, $endTime);`  |  Limit the task to run between start and end times
`->unlessBetween($startTime, $endTime);`  |  Limit the task to not run between start and end times
`->when(Closure);`  |  Limit the task based on a truth test
`->environments($env);`  |  Limit the task to specific environments

메소드  | 설명
------------- | -------------
`->weekdays();`  |  평일로 제한
`->weekends();`  |  주말로 제한
`->sundays();`  |  일요일로 제한
`->mondays();`  |  월요일로 제한
`->tuesdays();`  |  화요일로 제한
`->wednesdays();`  |  수요일로 제한
`->thursdays();`  |  목요일로 제한
`->fridays();`  |  금요일로 제한
`->saturdays();`  |  토요일로 제한
`->days(array|mixed);`  |  특정 요일로 제한
`->between($startTime, $endTime);`  |  시작과 종료 시간 사이에 작업 실행을 제한
`->unlessBetween($startTime, $endTime);`  |  시작 시간과 종료 시간 사이에 작업이 실행되지 않도록 제한
`->when(Closure);`  |  클로저 결과에 따라서 수행
`->environments($env);`  |  특정 환경으로 작업 제한

<a name="day-constraints"></a>
#### Day Constraints
#### 요일 제약

The `days` method may be used to limit the execution of a task to specific days of the week. For example, you may schedule a command to run hourly on Sundays and Wednesdays:

`days`메소드는 특정 요일로 작업 실행을 제한하는 데 사용할 수 있습니다. 예를 들어, 일요일과 수요일에 매시간 실행되도록 명령을 예약 할 수 있습니다.

    $schedule->command('emails:send')
                    ->hourly()
                    ->days([0, 3]);

Alternatively, you may use the constants available on the `Illuminate\Console\Scheduling\Schedule` class when defining the days on which a task should run:

또는 작업이 실행되어야 하는 요일을 정의할 때 `Illuminate\Console\Scheduling\Schedule` 클래스에서 사용 가능한 상수를 사용할 수 있습니다.

    use Illuminate\Console\Scheduling\Schedule;

    $schedule->command('emails:send')
                    ->hourly()
                    ->days([Schedule::SUNDAY, Schedule::WEDNESDAY]);

<a name="between-time-constraints"></a>                   
#### Between Time Constraints
#### Between 시간 제한

The `between` method may be used to limit the execution of a task based on the time of day:

`between` 메소드는 하루중에 시간에 따라 실행 시간을 제한하기 위해 사용될 수 있습니다.

    $schedule->command('emails:send')
                        ->hourly()
                        ->between('7:00', '22:00');

Similarly, the `unlessBetween` method can be used to exclude the execution of a task for a period of time:

마찬가지로, `unlessBetween` 메소드는 해당 기간 동안의 작업 실행을 제외하는데 사용될 수 있습니다.

    $schedule->command('emails:send')
                        ->hourly()
                        ->unlessBetween('23:00', '4:00');

<a name="truth-test-constraints"></a>
#### Truth Test Constraints
#### 테스트 조건 제한

The `when` method may be used to limit the execution of a task based on the result of a given truth test. In other words, if the given closure returns `true`, the task will execute as long as no other constraining conditions prevent the task from running:

`when` 메소드는 참/거짓 결과에 따라 작업의 실행을 제한하는 데에 사용 될 수 있습니다. 즉, 클로저가 `true`를 반환한다면 다른 제한 조건들이 없는 경우 작업이  실행될 것입니다.

    $schedule->command('emails:send')->daily()->when(function () {
        return true;
    });

The `skip` method may be seen as the inverse of `when`. If the `skip` method returns `true`, the scheduled task will not be executed:

`skip` 메소드는 `when`의 반대입니다. `skip` 메소드가 `true`를 반환하면, 스케줄링 작업은 실행되지 않을 것입니다.

    $schedule->command('emails:send')->daily()->skip(function () {
        return true;
    });

When using chained `when` methods, the scheduled command will only execute if all `when` conditions return `true`.

연결된 구조로 `when` 메소드를 사용할 경우, 모든 `when` 조건이 `true`를 반환해야만 스케줄된 커맨드가 실행될 것입니다.

<a name="environment-constraints"></a>
#### Environment Constraints
#### 환경 제약

The `environments` method may be used to execute tasks only on the given environments (as defined by the `APP_ENV` [environment variable](/docs/{{version}}/configuration#environment-configuration)):

`environments` 메소드는 주어진 환경에서만 태스크를 실행하는 데 사용될 수 있습니다 (`APP_ENV`에 정의된대로 [환경 변수](/docs/{{version}}/configuration#environment-configuration)).

    $schedule->command('emails:send')
                ->daily()
                ->environments(['staging', 'production']);

<a name="timezones"></a>
### Timezones
### 타임존

Using the `timezone` method, you may specify that a scheduled task's time should be interpreted within a given timezone:

`timezone` 메소드를 사용하면 예약 된 작업의 시간을 주어진 타임존 안에서 실행 되도록 지정할 수 있습니다.

    $schedule->command('report:generate')
             ->timezone('America/New_York')
             ->at('02:00')

If you are repeatedly assigning the same timezone to all of your scheduled tasks, you may wish to define a `scheduleTimezone` method in your `App\Console\Kernel` class. This method should return the default timezone that should be assigned to all scheduled tasks:

만약, 스케줄링된 모든 작업에 동일한 타임존을 반복적으로 할당하는 경우 `App\Console\Kernel` 클래스에서 `scheduleTimezone` 메서드를 정의할 수 있습니다. 이 메서드는 모든 예약된 작업에 할당되어야 하는 기본 타임존을 반환해야 합니다.

    /**
     * Get the timezone that should be used by default for scheduled events.
     *
     * @return \DateTimeZone|string|null
     */
    protected function scheduleTimezone()
    {
        return 'America/Chicago';
    }

> {note} Remember that some timezones utilize daylight savings time. When daylight saving time changes occur, your scheduled task may run twice or even not run at all. For this reason, we recommend avoiding timezone scheduling when possible.

> {note} 일부 타임존에서는 서머타임(daylight savings time)을 사용합니다. 서머타임에 따라 시간이 변경되버리면, 예약 된 작업이 두 번 실행되거나 아예 실행되지 않을 수도 있습니다. 따라서 가능한 경우 작업을 예약할 때 타임존 지정을 사용하지 않는 것이 좋습니다.

<a name="preventing-task-overlaps"></a>
### Preventing Task Overlaps
### 작업의 중복 방지

By default, scheduled tasks will be run even if the previous instance of the task is still running. To prevent this, you may use the `withoutOverlapping` method:

기본적으로는 동일한 작업이 이미 실행되고 있어도 스케줄에 등록된 작업들은 다시 실행될 것입니다. 이를 방지하기 위해 `withoutOverlapping` 메소드를 사용할 수 있습니다.

    $schedule->command('emails:send')->withoutOverlapping();

In this example, the `emails:send` [Artisan command](/docs/{{version}}/artisan) will be run every minute if it is not already running. The `withoutOverlapping` method is especially useful if you have tasks that vary drastically in their execution time, preventing you from predicting exactly how long a given task will take.

이 예제에서 `emails:send` [아티즌 커맨드](/docs/{{version}}/artisan)는 명령이 실행중이 아니라면 매 1분마다 실행될 것입니다. `withoutOverlapping` 메소드는 특히 작업의 예상 실행 시간이 극명하게 다른 경우에 유용하며 특정 작업이 얼마나 오래 걸릴지 매번 예상해야만 하는 일들을 방지 해줍니다.

If needed, you may specify how many minutes must pass before the "without overlapping" lock expires. By default, the lock will expire after 24 hours:

필요한 경우, "중복 방지" 잠금이 얼마나 지나야 하는지 분단위의 시간을 지정할 수 있습니다. 기본적으로, 잠금(lock)은 24시간 후에 만료됩니다.

    $schedule->command('emails:send')->withoutOverlapping(10);

<a name="running-tasks-on-one-server"></a>
### Running Tasks On One Server
### 한 서버에서 작업 실행하기

> {note} To utilize this feature, your application must be using the `database`, `memcached`, `dynamodb`, or `redis` cache driver as your application's default cache driver. In addition, all servers must be communicating with the same central cache server.

> {note} 이 기능을 사용하기 위해서는, 애플리케이션이 기본 캐시 드라이버로 반드시 `database`, `memcached`, `dynamodb` 또는 `redis` 캐시 드라이버를 사용해야 합니다. 또한 모든 서버는 하나의 중앙 캐시 서버와 통신해야 합니다.

If your application's scheduler is running on multiple servers, you may limit a scheduled job to only execute on a single server. For instance, assume you have a scheduled task that generates a new report every Friday night. If the task scheduler is running on three worker servers, the scheduled task will run on all three servers and generate the report three times. Not good!

애플리케이션의 스케줄러가 다수의 서버에서 실행된다면, 예약된 작업이 하나의 서버에서만 실행되도록 제한할 수 있습니다. 예를 들어 금요일 저녁마다 새로운 리포트를 생성하는 예약된 작업이 있다고 가정해 보겠습니다. 작업 스케줄러가 세개의 워커 서버에서 실행중이라면, 예약된 작업은 새 대의 서버 모두에서 실행되고, 리포트는 세번 생성됩니다. 이런 상황은 좋지 않습니다!

To indicate that the task should run on only one server, use the `onOneServer` method when defining the scheduled task. The first server to obtain the task will secure an atomic lock on the job to prevent other servers from running the same task at the same time:

작업이 하나의 서버에서만 실행되도록 하려면, 예약된 작업을 정의할 때 `onOneServer` 메소드를 사용하면 됩니다. 작업을 수행하는 첫번째 서버는 다른 서버가 동일한 작업을 동시에 실행하지 못하도록 lock을 통해서 보호하게 됩니다.

    $schedule->command('report:generate')
                    ->fridays()
                    ->at('17:00')
                    ->onOneServer();

<a name="background-tasks"></a>
### Background Tasks
### 백그라운드 작업

By default, multiple tasks scheduled at the same time will execute sequentially based on the order they are defined in your `schedule` method. If you have long-running tasks, this may cause subsequent tasks to start much later than anticipated. If you would like to run tasks in the background so that they may all run simultaneously, you may use the `runInBackground` method:

기본적으로, 동시에 예약된 여러 작업은 `schedule` 메서드에 정의된 순서에 따라 순차적으로 실행됩니다. 장기 실행 작업이 있는 경우 후속 작업이 예상보다 훨씬 늦게 시작될 수 있습니다. 모든 작업이 동시에 실행되도록 백그라운드에서 작업을 실행하려면 `runInBackground` 메서드를 사용할 수 있습니다.

    $schedule->command('analytics:report')
             ->daily()
             ->runInBackground();

> {note} The `runInBackground` method may only be used when scheduling tasks via the `command` and `exec` methods.

> {note} `runInBackground` 메소드는 `command`와 `exec` 메소드를 통해 작업을 스케쥴 할 때만 사용될 수 있습니다.

<a name="maintenance-mode"></a>
### Maintenance Mode
### 점검 모드

Your application's scheduled tasks will not run when the application is in [maintenance mode](/docs/{{version}}/configuration#maintenance-mode), since we don't want your tasks to interfere with any unfinished maintenance you may be performing on your server. However, if you would like to force a task to run even in maintenance mode, you may call the `evenInMaintenanceMode` method when defining the task:

애플리케이션의 예약된 작업은 애플리케이션이 [점검 모드](/docs/{{version}}/configuration#maintenance-mode)에 있을 때 실행되지 않습니다. 서버에서 수행 중입니다. 그러나 유지 관리 모드에서도 작업을 강제 실행하려면 작업을 정의할 때 'evenInMaintenanceMode' 메서드를 호출할 수 있습니다.

    $schedule->command('emails:send')->evenInMaintenanceMode();

<a name="running-the-scheduler"></a>
## Running The Scheduler
## 스케줄러 실행하기

Now that we have learned how to define scheduled tasks, let's discuss how to actually run them on our server. The `schedule:run` Artisan command will evaluate all of your scheduled tasks and determine if they need to run based on the server's current time.

예약된 작업을 정의하는 방법을 배웠으므로 이제 서버에서 실제로 실행하는 방법에 대해 논의해 보겠습니다. `schedule:run` 아티즌 명령은 예약된 모든 작업을 평가하고 서버의 현재 시간을 기준으로 실행해야 하는지 여부를 결정합니다.

So, when using Laravel's scheduler, we only need to add a single cron configuration entry to our server that runs the `schedule:run` command every minute. If you do not know how to add cron entries to your server, consider using a service such as [Laravel Forge](https://forge.laravel.com) which can manage the cron entries for you:

따라서 라라벨의 스케줄러를 사용할 때 1분마다 `schedule:run` 명령을 실행하는 단일 cron 구성 항목만 서버에 추가하면 됩니다. 서버에 cron 항목을 추가하는 방법을 모르는 경우 cron 항목을 관리할 수 있는 [라라벨 포지](https://forge.laravel.com)와 같은 서비스를 사용하는 것이 좋습니다.

    * * * * * cd /path-to-your-project && php artisan schedule:run >> /dev/null 2>&1

<a name="running-the-scheduler-locally"></a>
## Running The Scheduler Locally
## 로컬에서 스케줄러 실행하기

Typically, you would not add a scheduler cron entry to your local development machine. Instead, you may use the `schedule:work` Artisan command. This command will run in the foreground and invoke the scheduler every minute until you terminate the command:

일반적으로 로컬 개발 시스템에 스케줄러 cron 항목을 추가하지 않습니다. 대신 `schedule:work` 아티즌 명령을 사용할 수 있습니다. 이 명령은 포그라운드에서 실행되고 명령을 종료할 때까지 매분 스케줄러를 호출합니다.

    php artisan schedule:work

<a name="task-output"></a>
## Task Output
## 작업 출력

The Laravel scheduler provides several convenient methods for working with the output generated by scheduled tasks. First, using the `sendOutputTo` method, you may send the output to a file for later inspection:

라라벨 스케줄러는 스케줄된 작업들의 출력을 다루는 편리한 여러 메소드들을 제공합니다. 우선 `sendOutputTo` 메소드를 사용하면 결과를 나중에 확인할 수 있도록 파일로 보낼 수 있습니다.

    $schedule->command('emails:send')
             ->daily()
             ->sendOutputTo($filePath);

If you would like to append the output to a given file, you may use the `appendOutputTo` method:

특정 파일에 출력을 더하는 형태로 저장하기 위해서는 `appendOutputTo` 메소드를 사용하면 됩니다.

    $schedule->command('emails:send')
             ->daily()
             ->appendOutputTo($filePath);

Using the `emailOutputTo` method, you may email the output to an email address of your choice. Before emailing the output of a task, you should configure Laravel's [email services](/docs/{{version}}/mail):

`emailOutputTo` 메소드를 사용하면 원하는 이메일 주소로 출력을 전달할 수 있습니다. 작업의 출력을 이메일로 보내기 전에 라라벨의 [이메일 서비스](/docs/{{version}}/mail)를 설정해 놓아야만 합니다.

    $schedule->command('report:generate')
             ->daily()
             ->sendOutputTo($filePath)
             ->emailOutputTo('taylor@example.com');

If you only want to email the output if the scheduled Artisan or system command terminates with a non-zero exit code, use the `emailOutputOnFailure` method:

스케줄링된 아티즌 또는 시스템 명령이 0이 아닌 종료 코드로 종료되는 경우에만 출력을 이메일로 보내고 싶다면 `emailOutputOnFailure` 메소드를 사용하십시오:

    $schedule->command('report:generate')
             ->daily()
             ->emailOutputOnFailure('taylor@example.com');

> {note} The `emailOutputTo`, `emailOutputOnFailure`, `sendOutputTo`, and `appendOutputTo` methods are exclusive to the `command` and `exec` methods.

> {note} `emailOutputTo`, `emailOutputOnFailure`, `sendOutputTo` 그리고 `appendOutputTo` 메소드는 `command` 와 `exec` 메소드에서만 사용가능합니다.

<a name="task-hooks"></a>
## Task Hooks
## 작업 후킹

Using the `before` and `after` methods, you may specify code to be executed before and after the scheduled task is executed:

`before` 및 `after` 메서드를 사용하여 예약된 작업이 실행되기 전과 후에 실행할 코드를 지정할 수 있습니다.

    $schedule->command('emails:send')
             ->daily()
             ->before(function () {
                 // The task is about to execute...
             })
             ->after(function () {
                 // The task has executed...
             });

The `onSuccess` and `onFailure` methods allow you to specify code to be executed if the scheduled task succeeds or fails. A failure indicates that the scheduled Artisan or system command terminated with a non-zero exit code:

`onSuccess` 및 `onFailure` 메서드를 사용하면 예약된 작업이 성공하거나 실패할 경우 실행할 코드를 지정할 수 있습니다. 실패는 예약된 아티즌 또는 시스템 명령이 0이 아닌 종료 코드로 종료되었음을 나타냅니다.

    $schedule->command('emails:send')
             ->daily()
             ->onSuccess(function () {
                 // The task succeeded...
             })
             ->onFailure(function () {
                 // The task failed...
             });

If output is available from your command, you may access it in your `after`, `onSuccess` or `onFailure` hooks by type-hinting an `Illuminate\Support\Stringable` instance as the `$output` argument of your hook's closure definition:

커맨드에서 출력을 사용해야 할 경우, `after`, `onSuccess` 또는 `onFailure` 훅의 클로저에 `$output`를 `Illuminate\Support\Stringable` 인스턴스로 타입 힌트하여 사용 할 수 있습니다.


    use Illuminate\Support\Stringable;

    $schedule->command('emails:send')
             ->daily()
             ->onSuccess(function (Stringable $output) {
                 // The task succeeded...
             })
             ->onFailure(function (Stringable $output) {
                 // The task failed...
             });

<a name="pinging-urls"></a>
#### Pinging URLs
#### URL Ping 실행

Using the `pingBefore` and `thenPing` methods, the scheduler can automatically ping a given URL before or after a task is executed. This method is useful for notifying an external service, such as [Envoyer](https://envoyer.io), that your scheduled task is beginning or has finished execution:

`pingBefore` 및 `thenPing` 메서드를 사용하여 스케줄러는 작업이 실행되기 전이나 후에 주어진 URL을 자동으로 ping할 수 있습니다. 이 방법은 [Envoyer](https://envoyer.io)와 같은 외부 서비스에 예약된 작업이 시작되거나 실행이 완료되었음을 알리는 데 유용합니다.

    $schedule->command('emails:send')
             ->daily()
             ->pingBefore($url)
             ->thenPing($url);

The `pingBeforeIf` and `thenPingIf` methods may be used to ping a given URL only if a given condition is `true`:

`pingBeforeIf` 와 `thenPingIf` 메소드는 주어진 조건이 `true` 일 때만 주어진 URL에 ping을 보내는하는데 사용할 수 있습니다.

    $schedule->command('emails:send')
             ->daily()
             ->pingBeforeIf($condition, $url)
             ->thenPingIf($condition, $url);

The `pingOnSuccess` and `pingOnFailure` methods may be used to ping a given URL only if the task succeeds or fails. A failure indicates that the scheduled Artisan or system command terminated with a non-zero exit code:

`pingOnSuccess` 및 `pingOnFailure` 메서드는 작업이 성공하거나 실패한 경우에만 주어진 URL을 ping하는 데 사용할 수 있습니다. 실패는 예약된 아티즌 또는 시스템 명령이 0이 아닌 종료 코드로 종료되었음을 나타냅니다.

    $schedule->command('emails:send')
             ->daily()
             ->pingOnSuccess($successUrl)
             ->pingOnFailure($failureUrl);

All of the ping methods require the Guzzle HTTP library. Guzzle is typically installed in all new Laravel projects by default, but, you may manually install Guzzle into your project using the Composer package manager if it has been accidentally removed:

모든 ping 메소드에는 Guzzle HTTP 라이브러리가 필요합니다. Guzzle은 일반적으로 기본적으로 모든 새로운 라라벨 프로젝트에 설치되지만 실수로 제거된 경우 Composer 패키지 관리자를 사용하여 프로젝트에 Guzzle을 수동으로 설치할 수 있습니다.

    composer require guzzlehttp/guzzle

<a name="events"></a>
## Events
## 이벤트

If needed, you may listen to [events](/docs/{{version}}/events) dispatched by the scheduler. Typically, event listener mappings will be defined within your application's `App\Providers\EventServiceProvider` class:

필요한 경우 스케줄러에서 전달한 [events](/docs/{{version}}/events)를 수신할 수 있습니다. 일반적으로 이벤트 리스너 매핑은 애플리케이션의 `App\Providers\EventServiceProvider` 클래스 내에서 정의됩니다.

    /**
     * The event listener mappings for the application.
     *
     * @var array
     */
    protected $listen = [
        'Illuminate\Console\Events\ScheduledTaskStarting' => [
            'App\Listeners\LogScheduledTaskStarting',
        ],

        'Illuminate\Console\Events\ScheduledTaskFinished' => [
            'App\Listeners\LogScheduledTaskFinished',
        ],

        'Illuminate\Console\Events\ScheduledBackgroundTaskFinished' => [
            'App\Listeners\LogScheduledBackgroundTaskFinished',
        ],

        'Illuminate\Console\Events\ScheduledTaskSkipped' => [
            'App\Listeners\LogScheduledTaskSkipped',
        ],

        'Illuminate\Console\Events\ScheduledTaskFailed' => [
            'App\Listeners\LogScheduledTaskFailed',
        ],
    ];
