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
    - [공사중 모드](#maintenance-mode)
- [Task Output](#task-output)
- [작업 출력](#task-output)
- [Task Hooks](#task-hooks)
- [작업 후킹](#task-hooks)

<a name="introduction"></a>
## Introduction
## 시작하기

In the past, you may have generated a Cron entry for each task you needed to schedule on your server. However, this can quickly become a pain, because your task schedule is no longer in source control and you must SSH into your server to add additional Cron entries.

이전까지는, 여러분은 서버에서 스케줄링이 필요한 각각의 작업들을 위해서 Cron 항목를 생성해야 했습니다. 그러나 작업 스케줄은 소스 컨트롤로 관리되지도 않고, 서버에 SSH로 접속해서 Cron 항목을 추가해야 하기 때문에. 이 작업은 금방 어려워 집니다.

Laravel's command scheduler allows you to fluently and expressively define your command schedule within Laravel itself. When using the scheduler, only a single Cron entry is needed on your server. Your task schedule is defined in the `app/Console/Kernel.php` file's `schedule` method. To help you get started, a simple example is defined within the method.

라라벨의 명령어 스케줄러는 라라벨 안에서 유연하고 풍부한 표현이 가능한 명령어 스케줄을 정의할 수 있게 해줍니다. 스케줄러를 사용할 때에는 단지, 서버에 Cron 항목이 하나만 필요합니다. `app/Console/Kernel.php` 파일의 `schedule` 메소드안에 여러분의 작업 스케줄을 정의합니다. 시작을 돕기 위해서 이 메소드 안에는 간단한 예제가 정의되어 있습니다.

### Starting The Scheduler
### 작업 스케줄러 시작하기

When using the scheduler, you only need to add the following Cron entry to your server. If you do not know how to add Cron entries to your server, consider using a service such as [Laravel Forge](https://forge.laravel.com) which can manage the Cron entries for you:

스케줄러를 사용할 때에는, 다음의 Cron 항목을 서버에 추가하기만 하면 됩니다. 만약 여러분이 어떻게 Cron 항목을 서버에 추가하는지에 대해서 알지 못한다면, Cron 항목들을 관리해 줄 수 있는 [라라벨 Forge](https://forge.laravel.com)와 같은 서비스를 사용하는 것을 고려해 보십시오:

    * * * * * cd /path-to-your-project && php artisan schedule:run >> /dev/null 2>&1

This Cron will call the Laravel command scheduler every minute. When the `schedule:run` command is executed, Laravel will evaluate your scheduled tasks and runs the tasks that are due.

이 Cron 은 라라벨 명령어 스케줄러를 매분마다 호출할것입니다. `schedule:run` 명령어가 실행될 때, 라라벨은 여러분의 스케줄에 포함된 작업들을 계산하고 맞춰진 시간에 따라 작업들을 수행합니다.

<a name="defining-schedules"></a>
## Defining Schedules
## 스케줄 정의하기

You may define all of your scheduled tasks in the `schedule` method of the `App\Console\Kernel` class. To get started, let's look at an example of scheduling a task. In this example, we will schedule a `Closure` to be called every day at midnight. Within the `Closure` we will execute a database query to clear a table:

`App\Console\Kernel` 의 `schedule` 메소드에서 모든 스케줄된 작업들을 정의할 수 있습니다. 시작하기 전에 작업을 스케줄하는 예제 하나를 보겠습니다. 이 예제에서는 `Closure`가 매일밤 자정에 호출되도록 스케줄을 지정합니다. `Closure` 내에 테이블을 정리할 데이터베이스 쿼리를 실행합니다.

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

In addition to scheduling using Closures, you may also use [invokable objects](https://secure.php.net/manual/en/language.oop5.magic.php#object.invoke). Invokable objects are simple PHP classes that contain an `__invoke` method:

클로저를 사용하여 스케쥴링 하는 것 외에도 [invokable objects](https://secure.php.net/manual/en/language.oop5.magic.php#object.invoke)를 사용할 수 있습니다. 호출 가능한 객체는 `__invoke` 메소드를 포함하는 간단한 PHP 클래스입니다.

    $schedule->call(new DeleteRecentUsers)->daily();

<a name="scheduling-artisan-commands"></a>
### Scheduling Artisan Commands
### 아티즌 명령어 스케줄링

In addition to scheduling Closure calls, you may also schedule [Artisan commands](/docs/{{version}}/artisan) and operating system commands. For example, you may use the `command` method to schedule an Artisan command using either the command's name or class:

클로저 호출 외에도 [아티즌 명령어](/docs/{{version}}/artisan)와 os 명령어도 스케줄링 할 수 있습니다. 예를 들어 `command` 메소드로 다른 명령어의 이름이나 클래스를 사용하는 아티즌 커맨드를 스케줄링할 수 있습니다.

    $schedule->command('emails:send Taylor --force')->daily();

    $schedule->command(EmailsCommand::class, ['Taylor', '--force'])->daily();

<a name="scheduling-queued-jobs"></a>
### Scheduling Queued Jobs
### Queued Jobs 스케줄링

The `job` method may be used to schedule a [queued job](/docs/{{version}}/queues). This method provides a convenient way to schedule jobs without using the `call` method to manually create Closures to queue the job:

`job` 메소드는 [queued job](/docs/{{version}}/queues)을 스케줄링 하는데 사용됩니다. 이 메소드는 queue job 을 위한 클로저를 직접 생성하고 `call` 메소드를 사용하지 않고도 job을 스케줄링 할 수 있도록 하는 편리한 방법을 제공합니다.

    $schedule->job(new Heartbeat)->everyFiveMinutes();

    // Dispatch the job to the "heartbeats" queue...
    $schedule->job(new Heartbeat, 'heartbeats')->everyFiveMinutes();

<a name="scheduling-shell-commands"></a>
### Scheduling Shell Commands
### 쉘 명령어 스케줄링

The `exec` method may be used to issue a command to the operating system:

`exec` 메소드는 커맨드는 OS에 직접 명령어를 실행하는데 사용됩니다.

    $schedule->exec('node /home/forge/script.js')->daily();

<a name="schedule-frequency-options"></a>
### Schedule Frequency Options
### 스케줄링 주기 관련 옵션

There are a variety of schedules you may assign to your task:

작업을 스케줄링 할 때 다양한 옵션을 부여할 수 있습니다.

Method  | Description
------------- | -------------
`->cron('* * * * *');`  |  Run the task on a custom Cron schedule
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
`->lastDayOfMonth('15:00');` | Run the task on the last day of the month at 15:00
`->quarterly();` |  Run the task on the first day of every quarter at 00:00
`->yearly();`  |  Run the task on the first day of every year at 00:00
`->timezone('America/New_York');` | Set the timezone

메소드  | 설명
------------- | -------------
`->cron('* * * * *');`  |  지정한 Cron 형태로 작업 실행
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
`->lastDayOfMonth('15:00');` | 매월 말일 15:00에 작업 실행
`->quarterly();` |  분기별 첫번째 날 00:00 에 작업 실행
`->yearly();`  |  매년 1월1일 00:00 에 작업 실행
`->timezone('America/New_York');` | 타임존 지정

These methods may be combined with additional constraints to create even more finely tuned schedules that only run on certain days of the week. For example, to schedule a command to run weekly on Monday:

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

Below is a list of the additional schedule constraints:

아래는 추가적인 스케줄을 제한하는 옵션입니다.

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
`->between($start, $end);`  |  Limit the task to run between start and end times
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
`->between($start, $end);`  |  시작과 종료 시간 사이에 작업 실행을 제한
`->when(Closure);`  |  클로저 결과에 따라서 수행
`->environments($env);`  |  특정 환경으로 작업 제한

#### Day Constraints
#### 요일 제약

The `days` method may be used to limit the execution of a task to specific days of the week. For example, you may schedule a command to run hourly on Sundays and Wednesdays:

`days`메소드는 특정 요일로 작업 실행을 제한하는 데 사용할 수 있습니다. 예를 들어, 일요일과 수요일에 매시간 실행되도록 명령을 예약 할 수 있습니다.

    $schedule->command('reminders:send')
                    ->hourly()
                    ->days([0, 3]);

#### Between Time Constraints
#### Between 시간 제한

The `between` method may be used to limit the execution of a task based on the time of day:

`between` 메소드는 하루중에 시간에 따라 실행 시간을 제한하기 위해 사용될 수 있습니다.

    $schedule->command('reminders:send')
                        ->hourly()
                        ->between('7:00', '22:00');

Similarly, the `unlessBetween` method can be used to exclude the execution of a task for a period of time:

마찬가지로, `unlessBetween` 메소드는 해당 기간 동안의 작업 실행을 제외하는데 사용될 수 있습니다.

    $schedule->command('reminders:send')
                        ->hourly()
                        ->unlessBetween('23:00', '4:00');

#### Truth Test Constraints
#### 테스트 조건 제한

The `when` method may be used to limit the execution of a task based on the result of a given truth test. In other words, if the given `Closure` returns `true`, the task will execute as long as no other constraining conditions prevent the task from running:

`when` 메소드는 참/거짓 결과에 따라 작업의 실행을 제한하는 데에 사용 될 수 있습니다. 즉, `Closure` 가 `true`를 반환한다면 다른 제한 조건들이 없는 경우 작업이  실행될 것입니다.

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

#### Environment Constraints
#### 환경 제약

The `environments` method may be used to execute tasks only on the given environments:

`environments` 메소드는 주어진 환경에서만 태스크를 실행하는 데 사용될 수 있습니다.

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

If you are assigning the same timezone to all of your scheduled tasks, you may wish to define a `scheduleTimezone` method in your `app/Console/Kernel.php` file. This method should return the default timezone that should be assigned to all scheduled tasks:

만약, 모든 스케줄링 작업에 동일한 타임존을 지정하고자 한다면, `app/Console/Kernel.php` 파일에 `scheduleTimezone` 메소드를 정의하면 됩니다. 이 메소드는 스케줄링 작업들에 할당하고자 하는 기본 타임존을 반환해야 합니다.

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

> {note} To utilize this feature, your application must be using the `memcached` or `redis` cache driver as your application's default cache driver. In addition, all servers must be communicating with the same central cache server.

> {note} 이 기능을 사용하기 위해서는, 애플리케이션이 기본 캐시 드라이버로 반드시 `database`, `memcached` 또는 `redis` 캐시 드라이버를 사용해야 합니다. 또한 모든 서버는 하나의 중앙 캐시 서버와 통신해야 합니다.

If your application is running on multiple servers, you may limit a scheduled job to only execute on a single server. For instance, assume you have a scheduled task that generates a new report every Friday night. If the task scheduler is running on three worker servers, the scheduled task will run on all three servers and generate the report three times. Not good!

애플리케이션이 다수의 서버에서 실행된다면, 예약된 작업이 하나의 서버에서만 실행되도록 제한할 수 있습니다. 예를 들어 금요일 저녁마다 새로운 리포트를 생성하는 예약된 작업이 있다고 가정해 보겠습니다. 작업 스케줄러가 세개의 워커 서버에서 실행중이라면, 예약된 작업은 새 대의 서버 모두에서 실행되고, 리포트는 세번 생성됩니다. 이런 상황은 좋지 않습니다!

To indicate that the task should run on only one server, use the `onOneServer` method when defining the scheduled task. The first server to obtain the task will secure an atomic lock on the job to prevent other servers from running the same task at the same time:

작업이 하나의 서버에서만 실행되도록 하려면, 예약된 작업을 정의할 때 `onOneServer` 메소드를 사용하면 됩니다. 작업을 수행하는 첫번째 서버는 다른 서버가 동일한 작업을 동시에 실행하지 못하도록 lock을 통해서 보호하게 됩니다.

    $schedule->command('report:generate')
                    ->fridays()
                    ->at('17:00')
                    ->onOneServer();

<a name="background-tasks"></a>
### Background Tasks
### 백그라운드 작업

By default, multiple commands scheduled at the same time will execute sequentially. If you have long-running commands, this may cause subsequent commands to start much later than anticipated. If you would like to run commands in the background so that they may all run simultaneously, you may use the `runInBackground` method:

기본적으로, 동시에 예약 된 여러 명령은 순차적으로 실행됩니다. 장시간 실행되는 명령을 사용하면 뒤에 실행되는 명령이 예상보다 늦게 시작될 수 있습니다. 백그라운드에서 명령을 실행하여 모든 명령이 동시에 실행되게 하려면 `runInBackground` 메소드를 사용할 수 있습니다.

    $schedule->command('analytics:report')
             ->daily()
             ->runInBackground();

> {note} The `runInBackground` method may only be used when scheduling tasks via the `command` and `exec` methods.

> {note} `runInBackground` 메소드는 `command`와 `exec` 메소드를 통해 작업을 스케쥴 할 때만 사용될 수 있습니다.

<a name="maintenance-mode"></a>
### Maintenance Mode
### 공사중 모드

Laravel's scheduled tasks will not run when Laravel is in [maintenance mode](/docs/{{version}}/configuration#maintenance-mode), since we don't want your tasks to interfere with any unfinished maintenance you may be performing on your server. However, if you would like to force a task to run even in maintenance mode, you may use the `evenInMaintenanceMode` method:

라라벨에서 스케줄링되는 작업들은 라라벨이 [공사중 모드](/docs/{{version}}/configuration#maintenance-mode)일 때는 실행되지 않습니다. 유지 보수가 완료되지 않는 공사중인 서버에서 작업이 실행되지 않게 하기 위해서입니다. 그렇지만 공사중 모드에서도 실행이 되도록 강제하려면 `evenInMaintenanceMode` 사용하면 됩니다.

    $schedule->command('emails:send')->evenInMaintenanceMode();

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

Using the `emailOutputTo` method, you may e-mail the output to an e-mail address of your choice. Before e-mailing the output of a task, you should configure Laravel's [e-mail services](/docs/{{version}}/mail):

`emailOutputTo` 메소드를 사용하면 원하는 이메일 주소로 출력을 전달할 수 있습니다. 작업의 출력을 이메일로 보내기 전에 라라벨의 [이메일 서비스](/docs/{{version}}/mail)를 설정해 놓아야만 합니다.

    $schedule->command('foo')
             ->daily()
             ->sendOutputTo($filePath)
             ->emailOutputTo('foo@example.com');

If you only want to e-mail the output if the command fails, use the `emailOutputOnFailure` method:

명령이 실패 할 경우에만 출력을 메일로 보내려면 `emailOutputOnFailure` 메소드를 사용하십시오.

    $schedule->command('foo')
             ->daily()
             ->emailOutputOnFailure('foo@example.com');

> {note} The `emailOutputTo`, `emailOutputOnFailure`, `sendOutputTo`, and `appendOutputTo` methods are exclusive to the `command` and `exec` methods.

> {note} `emailOutputTo`, `emailOutputOnFailure`, `sendOutputTo` 그리고 `appendOutputTo` 메소드는 `command` 와 `exec` 메소드에서만 사용가능합니다.

<a name="task-hooks"></a>
## Task Hooks
## 작업 후킹

Using the `before` and `after` methods, you may specify code to be executed before and after the scheduled task is complete:

`before`와 `after` 메소드들을 이용하면 스케줄된 작업이 실행되기 전과 후에 지정한 코드가 실행되도록 설정할 수 있습니다.

    $schedule->command('emails:send')
             ->daily()
             ->before(function () {
                 // Task is about to start...
             })
             ->after(function () {
                 // Task is complete...
             });

The `onSuccess` and `onFailure` methods allow you to specify code to be executed if the scheduled task succeeds or fails:

`onSuccess`와 `onFailure` 메소드로 예약 된 작업이 성공하거나 실패 할 경우 실행될 코드를 지정할 수 있습니다.

    $schedule->command('emails:send')
             ->daily()
             ->onSuccess(function () {
                 // The task succeeded...
             })
             ->onFailure(function () {
                 // The task failed...
             });

If output is available from your command, you may access it in your `after`, `onSuccess` or `onFailure` hooks by type-hinting an `Illuminate\Support\Stringable` instance as `$output` on your hook's Closure definition:

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

#### Pinging URLs
#### URL Ping 실행

Using the `pingBefore` and `thenPing` methods, the scheduler can automatically ping a given URL before or after a task is complete. This method is useful for notifying an external service, such as [Laravel Envoyer](https://envoyer.io), that your scheduled task is commencing or has finished execution:

`pingBefore`와 `thenPing` 메소드들을 이용하면 작업이 완료되기 전이나 후에 스케줄러가 URL을 ping할 수 있습니다. 이 메소드는 [Laravel Envoyer](https://envoyer.io)와 같은 외부 서비스에 스케줄된 작업의 시작이나 완료를 알리는 데 유용합니다.

    $schedule->command('emails:send')
             ->daily()
             ->pingBefore($url)
             ->thenPing($url);

The `pingBeforeIf` and `thenPingIf` methods may be used to ping a given URL only if the given condition is `true`:

`pingBeforeIf` 와 `thenPingIf` 메소드는 주어진 조건이 `true` 일 때만 주어진 URL에 ping을 보내는하는데 사용할 수 있습니다.

    $schedule->command('emails:send')
             ->daily()
             ->pingBeforeIf($condition, $url)
             ->thenPingIf($condition, $url);

The `pingOnSuccess` and `pingOnFailure` methods may be used to ping a given URL only if the task succeeds or fails:

`pingOnSuccess`와 `pingOnFailure` 메소드는 작업이 성공하거나 실패 할 때 주어진 URL을 ping하는데 사용할 수 있습니다.

    $schedule->command('emails:send')
             ->daily()
             ->pingOnSuccess($successUrl)
             ->pingOnFailure($failureUrl);

All of the ping methods require the Guzzle HTTP library. You can add Guzzle to your project using the Composer package manager:

모든 ping 메소드에는 Guzzle HTTP 라이브러리가 필요합니다. Composer 패키지 매니저를 사용하여 Guzzle을 프로젝트에 추가 할 수 있습니다.

    composer require guzzlehttp/guzzle
