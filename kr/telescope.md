# Laravel Telescope
# 라라벨 Telescope

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Installation](#installation)
- [설치하기](#installation)
    - [Configuration](#configuration)
    - [환경설정](#configuration)
    - [Data Pruning](#data-pruning)
    - [데이터 정리](#data-pruning)
- [Dashboard Authorization](#dashboard-authorization)
- [Dashboard 권한 부여](#dashboard-authorization)
- [Filtering](#filtering)
    - [Entries](#filtering-entries)
    - [Batches](#filtering-batches)
- [Available Watchers](#available-watchers)
    - [Cache Watcher](#cache-watcher)
    - [Command Watcher](#command-watcher)
    - [Dump Watcher](#dump-watcher)
    - [Event Watcher](#event-watcher)
    - [Exception Watcher](#exception-watcher)
    - [Gate Watcher](#gate-watcher)
    - [Job Watcher](#job-watcher)
    - [Log Watcher](#log-watcher)
    - [Mail Watcher](#mail-watcher)
    - [Model Watcher](#model-watcher)
    - [Notification Watcher](#notification-watcher)
    - [Query Watcher](#query-watcher)
    - [Redis Watcher](#redis-watcher)
    - [Request Watcher](#request-watcher)
    - [Schedule Watcher](#schedule-watcher)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel Telescope is an elegant debug assistant for the Laravel framework. Telescope provides insight into the requests coming into your application, exceptions, log entries, database queries, queued jobs, mail, notifications, cache operations, scheduled tasks, variable dumps and more. Telescope makes a wonderful companion to your local Laravel development environment.

라라벨 Telescope는 라라벨을 위한 우아한 디버깅 도구입니다. Telescope는 어플리케이션에 들어오는 요청, 예외, 로그 항목, 데이터베이스 쿼리, 대기중인 작업, 메일, 알림, 캐시 작업, 예약 된 작업, 변수 덤프 등에 대한 분석을 제공합니다. Telescope는 로컬 라라벨 개발 환경에 훌륭한 동반자입니다.

<p align="center">
<img src="https://res.cloudinary.com/dtfbvvkyp/image/upload/v1539110860/Screen_Shot_2018-10-09_at_1.47.23_PM.png" width="600" height="347">
</p>

<a name="installation"></a>
## Installation
## 설치하기

> {note} Telescope requires Laravel 5.7.7+.
> {note} Telescope는 라라벨 5.7.7 이상을 필요로 합니다.

You may use Composer to install Telescope into your Laravel project:

컴포저를 이용해서 라라벨 프로젝트에 Telescope를 설치 할 수 있습니다:

    composer require laravel/telescope

After installing Telescope, publish its assets using the `telescope:install` Artisan command. After installing Telescope, you should also run the `migrate` command:

Telescope을 설치 한 후 `telescope:install` 아티즌 명령을 사용하여 assets을 퍼블리싱합니다. Telescope를 설치 한 후에 `migrate` 명령 또한 실행해야합니다 :


    php artisan telescope:install

    php artisan migrate

#### Updating Telescope
#### Telescope 업데이트

When updating Telescope, you should re-publish Telescope's assets:

Telescope를 업데이트 할 때 Telescope의 assets을 다시 퍼블리싱해야합니다 :

    php artisan telescope:publish

### Installing Only In Specific Environments
### 특정 환경에서만 설치

If you plan to only use Telescope to assist your local development. You may install Telescope using the `--dev` flag:

로컬 개발에서만 Telescope를 사용할 계획이라면 `--dev` 플래그를 사용하여 Telescope를 설치할 수 있습니다 :

    composer require laravel/telescope --dev

After running `telescope:install`, you should remove the `TelescopeServiceProvider` service provider registration from your `app` configuration file. Instead, manually register the service provider in the `register` method of your `AppServiceProvider`:

`telescope:install` 을 실행 한 후 `app` 설정 파일에서 `TelescopeServiceProvider` 서비스 프로바이더 등록을 제거해야합니다. 대신 직접 `AppServiceProvider` 의 `register` 메소드에 서비스 프로바이더를 등록하십시오 :

    use Laravel\Telescope\TelescopeServiceProvider;

    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        if ($this->app->isLocal()) {
            $this->app->register(TelescopeServiceProvider::class);
        }
    }

<a name="configuration"></a>
### Configuration
### 환경설정

After publishing Telescope's assets, its primary configuration file will be located at `config/telescope.php`. This configuration file allows you to configure your watcher options and each configuration option includes a description of its purpose, so be sure to thoroughly explore this file.

Telescope의 assets을 퍼블리싱하면 기본 설정 파일 `config/telescope.php` 이 생성됩니다. 이 설정 파일을 사용하면 감시자(watcher) 옵션을 변경 할 수 있으며 각 설정 옵션에는 용도에 대한 설명이 포함되므로 이 파일을 철저히 확인하십시오.

If desired, you may disable Telescope's data collection entirely using the `enabled` configuration option:

원하는 경우, `enabled` 설정 옵션을 사용하여 Telescope의 데이터 수집을 완전히 비활성화 할 수 있습니다 :


    'enabled' => env('TELESCOPE_ENABLED', true),

<a name="data-pruning"></a>
### Data Pruning
### 데이터 정리

Without pruning, the `telescope_entries` table can accumulate records very quickly. To mitigate this, you should schedule the `telescope:prune` Artisan command to run daily:

데이터 정리를 하지 않는 다면, `telescope_entries` 테이블은 레코드가 매우 빨리 누적 될 수 있습니다. 이것을 줄이기 위해 `telescope:prune` 아티즌 명령을 매일 실행하도록 예약해야합니다 :

    $schedule->command('telescope:prune')->daily();

By default, all entries older than 24 hours will be pruned. You may use the `hours` option when calling the command to determine how long to retain Telescope data. For example, the following command will delete all records created over 48 hours ago:

기본적으로 24시간이 넘은 항목은 모두 제거됩니다. 명령을 호출 할 때 `hours` 옵션을 사용하여 Telescope 데이터를 얼마나 오래 저장할지를 결정할 수 있습니다. 예를 들어, 다음 명령은 48 시간 전에 생성 된 모든 레코드를 삭제합니다.

    $schedule->command('telescope:prune --hours=48')->daily();

<a name="dashboard-authorization"></a>
## Dashboard Authorization
## Dashboard 권한 부여

Telescope exposes a dashboard at `/telescope`. By default, you will only be able to access this dashboard in the `local` environment. Within your `app/Providers/TelescopeServiceProvider.php` file, there is a `gate` method. This authorization gate controls access to Telescope in **non-local** environments. You are free to modify this gate as needed to restrict access to your Telescope installation:

Telescope Dashboard는 `/telescope` 으로 접속 가능하며, 기본적으로 `local` 환경에서만 접근이 가능합니다. `app/Providers/TelescopeServiceProvider.php` 파일 내에 `gate` 메소드가 있습니다. 이 인증 게이트는 **비 로컬** 환경에서 Telescope에 대한 액세스를 제어합니다. Telescope 대한 액세스를 제한하기 위해 필요에 따라 이 게이트를 자유롭게 수정할 수 있습니다.

    /**
     * Register the Telescope gate.
     *
     * This gate determines who can access Telescope in non-local environments.
     *
     * @return void
     */
    protected function gate()
    {
        Gate::define('viewTelescope', function ($user) {
            return in_array($user->email, [
                'taylor@laravel.com',
            ]);
        });
    }

<a name="filtering"></a>
## Filtering

<a name="filtering-entries"></a>
### Entries

You may filter the data that is recorded by Telescope via the `filter` callback that is registered in your `TelescopeServiceProvider`. By default, this callback records all data in the `local` environment and exceptions, failed jobs, scheduled tasks, and data with monitored tags in all other environments:

    /**
     * Register any application services.
     *
     * @return void
     */
	public function register()
	{
        $this->hideSensitiveRequestDetails();

        Telescope::filter(function (IncomingEntry $entry) {
            if ($this->app->isLocal()) {
                return true;
            }

            return $entry->isReportableException() ||
                $entry->isFailedJob() ||
                $entry->isScheduledTask() ||
                $entry->hasMonitoredTag();
        });
	}

<a name="filtering-batches"></a>
### Batches

While the `filter` callback filters data for individual entries, you may use the `filterBatch` method to register a callback that filters all data for a given request or console command. If the callback returns `true`, all of the entries are recorded by Telescope:

    use Illuminate\Support\Collection;

    /**
     * Register any application services.
     *
     * @return void
     */
	public function register()
	{
        $this->hideSensitiveRequestDetails();

        Telescope::filterBatch(function (Collection $entries) {
            if ($this->app->isLocal()) {
                return true;
            }

            return $entries->contains(function ($entry) {
                return $entry->isReportableException() ||
                    $entry->isFailedJob() ||
                    $entry->isScheduledTask() ||
                    $entry->hasMonitoredTag();
                });
        });
	}

<a name="available-watchers"></a>
## Available Watchers

Telescope watchers gather application data when a request or console command is executed. You may customize the list of watchers that you would like to enable within your `config/telescope.php` configuration file:

    'watchers' => [
        Watchers\CacheWatcher::class => true,
        Watchers\CommandWatcher::class => true,
        ...
    ],

Some watchers also allow you to provide additional customization options:

    'watchers' => [
        Watchers\QueryWatcher::class => [
            'enabled' => env('TELESCOPE_QUERY_WATCHER', true),
            'slow' => 100,
        ],
        ...
    ],

<a name="cache-watcher"></a>
### Cache Watcher

The cache watcher records data when a cache key is hit, missed, updated and forgotten.

<a name="command-watcher"></a>
### Command Watcher

The command watcher records the arguments, options, exit code, and output whenever an Artisan command is executed. If you would like to exclude certain commands from being recorded by the watcher, you may specify the command in the `ignore` option in your `config/telescope.php` file:

    'watchers' => [
        Watchers\CommandWatcher::class => [
            'enabled' => env('TELESCOPE_COMMAND_WATCHER', true),
            'ignore' => ['key:generate'],
        ],
        ...
    ],

<a name="dump-watcher"></a>
### Dump Watcher

The dump watcher records and displays your variable dumps in Telescope. When using Laravel, variables may be dumped using the global `dump` function. The dump watcher tab must be open in a browser for the recording to occur, otherwise the dumps will be ignored by the watcher.

<a name="event-watcher"></a>
### Event Watcher

The event watcher records the payload, listeners, and broadcast data for any events dispatched by your application. The Laravel framework's internal events are ignored by the Event watcher.

<a name="exception-watcher"></a>
### Exception Watcher

The exception watcher records the data and stack trace for any reportable Exceptions that are thrown by your application.

<a name="gate-watcher"></a>
### Gate Watcher

The gate watcher records the data and result of gate and policy checks by your application. If you would like to exclude certain abilities from being recorded by the watcher, you may specify those in the `ignore_abilities` option in your `config/telescope.php` file:

    'watchers' => [
        Watchers\GateWatcher::class => [
            'enabled' => env('TELESCOPE_GATE_WATCHER', true),
            'ignore_abilities' => ['viewNova'],
        ],
        ...
    ],

<a name="job-watcher"></a>
### Job Watcher

The job watcher records the data and status of any jobs dispatched by your application.

<a name="log-watcher"></a>
### Log Watcher

The log watcher records the log data for any logs written by your application.

<a name="mail-watcher"></a>
### Mail Watcher

The mail watcher allows you to view an in-browser preview of the emails along with their associated data. You may also download the email as an `.eml` file.

<a name="model-watcher"></a>
### Model Watcher

The model watcher records model changes whenever an Eloquent `created`, `updated`, `restored`, or `deleted` event is dispatched. You may specify which model events should be recorded via the watcher's `events` option:

    'watchers' => [
        Watchers\ModelWatcher::class => [
            'enabled' => env('TELESCOPE_MODEL_WATCHER', true),
            'events' => ['eloquent.created*', 'eloquent.updated*'],
        ],
        ...
    ],

<a name="notification-watcher"></a>
### Notification Watcher

The notification watcher records all notifications sent by your application. If the notification triggers an email and you have the mail watcher enabled, the email will also be available for preview on the mail watcher screen.

<a name="query-watcher"></a>
### Query Watcher

The query watcher records the raw SQL, bindings, and execution time for all queries that are executed by your application. The watcher also tags any queries slower than 100ms as `slow`. You may customize the slow query threshold using the watcher's `slow` option:

    'watchers' => [
        Watchers\QueryWatcher::class => [
            'enabled' => env('TELESCOPE_QUERY_WATCHER', true),
            'slow' => 50,
        ],
        ...
    ],

<a name="redis-watcher"></a>
### Redis Watcher

> {note} Redis events must be enabled for the Redis watcher to function. You may enable Redis events by calling `Redis::enableEvents()` in the `boot` method of your `app/Providers/AppServiceProvider.php` file.

The Redis watcher records all Redis commands executed by your application. If you are using Redis for caching, cache commands will also be recorded by the Redis Watcher.

<a name="request-watcher"></a>
### Request Watcher

The request watcher records the request, headers, session, and response data associated with any requests handled by the application. You may limit your response data via the `size_limit` (in KB) option:

    'watchers' => [
        Watchers\RequestWatcher::class => [
            'enabled' => env('TELESCOPE_REQUEST_WATCHER', true),
            'size_limit' => env('TELESCOPE_RESPONSE_SIZE_LIMIT', 64),
        ],
        ...
    ],

<a name="schedule-watcher"></a>
### Schedule Watcher

The schedule watcher records the command and output of any scheduled tasks run by your application.
