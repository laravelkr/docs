# Laravel Horizon
# 라라벨 Horizon

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Installation](#installation)
- [설치하기](#installation)
    - [Configuration](#configuration)
    - [설정하기](#configuration)
    - [Balancing Strategies](#balancing-strategies)
    - [밸런싱 전략](#balancing-strategies)
    - [Dashboard Authorization](#dashboard-authorization)
    - [Dashboard 권한부여](#dashboard-authorization)
- [Upgrading Horizon](#upgrading-horizon)
- [Horizon 업그레이드](#upgrading-horizon)
- [Running Horizon](#running-horizon)
- [Horizon 실행하기](#running-horizon)
    - [Deploying Horizon](#deploying-horizon)
    - [Horizon 배포하기](#deploying-horizon)
- [Tags](#tags)
- [태그](#tags)
- [Notifications](#notifications)
- [알림](#notifications)
- [Metrics](#metrics)
- [메트릭](#metrics)
- [Deleting Failed Jobs](#deleting-failed-jobs)
- [실패한 작업 삭제](#deleting-failed-jobs)
- [Clearing Jobs From Queues](#clearing-jobs-from-queues)
- [대기열에서 작업 지우기](#clearing-jobs-from-queues)

<a name="introduction"></a>
## Introduction
## 시작하기

> {tip} Before digging into Laravel Horizon, you should familiarize yourself with Laravel's base [queue services](/docs/{{version}}/queues). Horizon augments Laravel's queue with additional features that may be confusing if you are not already familiar with the basic queue features offered by Laravel.

> {tip} 라라벨 Horizon에 대해 알아보기 전에 라라벨의 기본 [queue services](/docs/{{version}}/queues)에 익숙해져야 합니다. Horizon은 라라벨에서 제공하는 기본 대기열 기능에 아직 익숙하지 않은 경우 혼동될 수 있는 추가 기능으로 라라벨의 대기열을 보강합니다.

[Laravel Horizon](https://github.com/laravel/horizon) provides a beautiful dashboard and code-driven configuration for your Laravel powered [Redis queues](/docs/{{version}}/queues). Horizon allows you to easily monitor key metrics of your queue system such as job throughput, runtime, and job failures.

[라라벨 Horizon](https://github.com/laravel/horizon) 은 라라벨 기반 [Redis 대기열-queue](/docs/{{version}}/queues)을 위한 아름다운 대시보드 및 코드 기반 설정을 제공합니다. Horizon을 사용하면 작업 처리량, 런타임 및 작업 실패와 같은 대기열 시스템의 주요 메트릭을 쉽게 모니터링할 수 있습니다.

When using Horizon, all of your queue worker configuration is stored in a single, simple configuration file. By defining your application's worker configuration in a version controlled file, you may easily scale or modify your application's queue workers when deploying your application.

Horizon을 사용하는 경우, 모든 대기열 작업자 설정은 하나의 간단한 설정 파일에 저장됩니다. 버전 제어 파일에서 애플리케이션의 작업자 설정을 정의하면 애플리케이션을 배포할 때 애플리케이션의 대기열 작업자를 쉽게 확장하거나 수정할 수 있습니다.

<img src="https://laravel.com/img/docs/horizon-example.png">

<a name="installation"></a>
## Installation
## 설치하기

> {note} Laravel Horizon requires that you use [Redis](https://redis.io) to power your queue. Therefore, you should ensure that your queue connection is set to `redis` in your application's `config/queue.php` configuration file.

> {note} 라라벨 Horizon을 사용하려면 [Redis](https://redis.io) 를 사용해야 합니다. 따라서 애플리케이션의 `config/queue.php` 설정 파일에서 대기열 연결이 `redis`로 설정되어 있는지 확인해야 합니다.

You may install Horizon into your project using the Composer package manager:

Composer 패키지 관리자를 사용하여 프로젝트에 Horizon을 설치할 수 있습니다.

```shell
composer require laravel/horizon
```

After installing Horizon, publish its assets using the `horizon:install` Artisan command:

Horizon을 설치한 후 `horizon:install` Artisan 명령을 사용하여 자산을 게시합니다.

```shell
php artisan horizon:install
```

<a name="configuration"></a>
### Configuration
### 설정하기

After publishing Horizon's assets, its primary configuration file will be located at `config/horizon.php`. This configuration file allows you to configure the queue worker options for your application. Each configuration option includes a description of its purpose, so be sure to thoroughly explore this file.

Horizon 자산을 게시하면 기본 설정 파일이 `config/horizon.php`로 생성됩니다. 이 설정 파일을 사용하면 애플리케이션에 대한 대기열 작업자 옵션을 설정할 수 있습니다. 각 설정 옵션에는 용도에 대한 설명이 포함되어 있으므로 이 파일을 꼼꼼하게 살펴보십시오.

> {note} Horizon uses a Redis connection named `horizon` internally. This Redis connection name is reserved and should not be assigned to another Redis connection in the `database.php` configuration file or as the value of the `use` option in the `horizon.php` configuration file.

> {note} Horizon은 내부적으로 `horizon`이라는 Redis 연결을 사용합니다. 이 Redis 연결 이름은 예약되어 있으므로 `database.php` 설정 파일의 다른 Redis 연결에 할당하거나 `horizon.php` 설정 파일의 `use` 옵션 값으로 할당해서는 안 됩니다.

<a name="environments"></a>
#### Environments
#### 환경설정

After installation, the primary Horizon configuration option that you should familiarize yourself with is the `environments` configuration option. This configuration option is an array of environments that your application runs on and defines the worker process options for each environment. By default, this entry contains a `production` and `local` environment. However, you are free to add more environments as needed:

설치 후엔 기본 Horizon 설정 옵션 중 `environments` 옵션에 익숙해져야 합니다. 이 설정 옵션은 애플리케이션이 실행되는 환경의 배열이며 각 환경에 대한 작업자 프로세스 옵션을 정의합니다. 기본적으로 이 항목에는 `production` 및 `local` 환경이 포함되어 있습니다. 그러나 필요에 따라 자유롭게 환경을 추가할 수 있습니다.

    'environments' => [
        'production' => [
            'supervisor-1' => [
                'maxProcesses' => 10,
                'balanceMaxShift' => 1,
                'balanceCooldown' => 3,
            ],
        ],

        'local' => [
            'supervisor-1' => [
                'maxProcesses' => 3,
            ],
        ],
    ],

When you start Horizon, it will use the worker process configuration options for the environment that your application is running on. Typically, the environment is determined by the value of the `APP_ENV` [environment variable](/docs/{{version}}/configuration#determining-the-current-environment). For example, the default `local` Horizon environment is configured to start three worker processes and automatically balance the number of worker processes assigned to each queue. The default `production` environment is configured to start a maximum of 10 worker processes and automatically balance the number of worker processes assigned to each queue.

Horizon을 시작하면 애플리케이션이 실행되는 환경에 대한 작업자 프로세스 설정 옵션이 사용됩니다. 일반적으로 환경은 `APP_ENV` [환경 변수](/docs/{{version}}/configuration#determining-the-current-environment)의 값에 의해 결정됩니다. 예를 들어 기본 `local` Horizon 환경은 3개의 작업자 프로세스를 시작하고 각 대기열에 할당된 작업자 프로세스 수의 균형을 자동으로 조정하도록 설정됩니다. 기본 `production` 환경은 최대 10개의 작업자 프로세스를 시작하고 각 대기열에 할당된 작업자 프로세스 수의 균형을 자동으로 조정하도록 설정됩니다.

> {note} You should ensure that the `environments` portion of your `horizon` configuration file contains an entry for each [environment](/docs/{{version}}/configuration#environment-configuration) on which you plan to run Horizon.

> {note} `horizon` 설정 파일의 `environments` 부분에 Horizon을 실행할 각 [environment](/docs/{{version}}/configuration#environment-configuration) 에 대한 항목이 포함되어 있는지 확인해야 합니다.

<a name="supervisors"></a>
#### Supervisors
#### Supervisor

As you can see in Horizon's default configuration file. Each environment can contain one or more "supervisors". By default, the configuration file defines this supervisor as `supervisor-1`; however, you are free to name your supervisors whatever you want. Each supervisor is essentially responsible for "supervising" a group of worker processes and takes care of balancing worker processes across queues.

Horizon의 기본 설정 파일에서 볼 수 있듯이. 각 환경에는 하나 이상의 "supervisors"가 포함될 수 있습니다. 기본적으로 설정 파일은 이 수퍼바이저를 `supervisor-1`로 정의합니다. 그러나 supervisor의 이름은 원하는 대로 지정할 수 있습니다. 각 supervisor는 기본적으로 작업자 프로세스 그룹을 "감독"할 책임이 있으며 대기열 전체에서 작업자 프로세스의 균형을 조정합니다.

You may add additional supervisors to a given environment if you would like to define a new group of worker processes that should run in that environment. You may choose to do this if you would like to define a different balancing strategy or worker process count for a given queue used by your application.

해당 환경에서 실행해야 하는 작업자 프로세스의 새 그룹을 정의하려는 경우 지정된 환경에 supervisor를 추가할 수 있습니다. 애플리케이션에서 사용하는 주어진 대기열에 대해 다른 밸런싱 전략 또는 작업자 프로세스 수를 정의하려는 경우 이 작업을 수행하도록 선택할 수 있습니다.

<a name="default-values"></a>
#### Default Values
#### 기본값

Within Horizon's default configuration file, you will notice a `defaults` configuration option. This configuration option specifies the default values for your application's [supervisors](#supervisors). The supervisor's default configuration values will be merged into the supervisor's configuration for each environment, allowing you to avoid unnecessary repetition when defining your supervisors.

Horizon의 기본 설정 파일 내에서 `defaults` 설정 옵션을 확인할 수 있습니다. 이 설정 옵션은 애플리케이션의 [supervisors](#supervisors)에 대한 기본값을 지정합니다. supervisor의 기본 설정 값은 각 환경에 대한 supervisor의 설정에 병합되므로 supervisor를 정의할 때 불필요한 반복을 피할 수 있습니다.

<a name="balancing-strategies"></a>
### Balancing Strategies
### 밸런싱 전략

Unlike Laravel's default queue system, Horizon allows you to choose from three worker balancing strategies: `simple`, `auto`, and `false`. The `simple` strategy, which is the configuration file's default, splits incoming jobs evenly between worker processes:

라라벨의 기본 대기열 시스템과 달리 Horizon에서는 `simple`, `auto` 및 `false`의 세 가지 작업자 밸런싱 전략 중에서 선택할 수 있습니다. 설정 파일의 기본값인 `simple` 전략은 들어오는 작업을 작업자 프로세스 간에 균등하게 분할합니다.

    'balance' => 'simple',

The `auto` strategy adjusts the number of worker processes per queue based on the current workload of the queue. For example, if your `notifications` queue has 1,000 pending jobs while your `render` queue is empty, Horizon will allocate more workers to your `notifications` queue until the queue is empty.

`auto` 전략은 대기열의 현재 작업 부하를 기반으로 대기열당 작업자 프로세스 수를 조정합니다. 예를 들어 `notifications` 대기열에 1,000개의 보류 중인 작업이 있고 `render` 대기열은 비어 있는 경우 Horizon은 대기열이 비어 있을 때까지 `notifications` 대기열에 더 많은 작업자를 할당합니다.

When using the `auto` strategy, you may define the `minProcesses` and `maxProcesses` configuration options to control the minimum and the maximum number of worker processes Horizon should scale up and down to:

`auto` 전략을 사용할 때 `minProcesses` 및 `maxProcesses` 설정 옵션을 정의하여 Horizon이 확장 및 축소해야 하는 작업자 프로세스의 최소 및 최대 수를 제어할 수 있습니다.

    'environments' => [
        'production' => [
            'supervisor-1' => [
                'connection' => 'redis',
                'queue' => ['default'],
                'balance' => 'auto',
                'minProcesses' => 1,
                'maxProcesses' => 10,
                'balanceMaxShift' => 1,
                'balanceCooldown' => 3,
                'tries' => 3,
            ],
        ],
    ],

The `balanceMaxShift` and `balanceCooldown` configuration values to determine how quickly Horizon will scale to meet worker demand. In the example above, a maximum of one new process will be created or destroyed every three seconds. You are free to tweak these values as necessary based on your application's needs.

`balanceMaxShift` 및 `balanceCooldown` 설정 값은 작업자 수요를 충족하기 위해 Horizon이 얼마나 빨리 확장되는지 결정합니다. 위의 예에서 최대 하나의 새 프로세스가 3초마다 생성되거나 소멸됩니다. 애플리케이션의 필요에 따라 필요에 따라 이러한 값을 자유롭게 조정할 수 있습니다.

When the `balance` option is set to `false`, the default Laravel behavior will be used, which processes queues in the order they are listed in your configuration.

`balance` 옵션이 `false`로 설정되면 설정에 나열된 순서대로 대기열을 처리하는 기본 라라벨 동작이 사용됩니다.

<a name="dashboard-authorization"></a>
### Dashboard Authorization
### Dashboard 권한 부여

Horizon exposes a dashboard at the `/horizon` URI. By default, you will only be able to access this dashboard in the `local` environment. However, within your `app/Providers/HorizonServiceProvider.php` file, there is an [authorization gate](/docs/{{version}}/authorization#gates) definition. This authorization gate controls access to Horizon in **non-local** environments. You are free to modify this gate as needed to restrict access to your Horizon installation:

Horizon은 `/horizon` URI에서 대시보드를 노출합니다. 기본적으로 `local` 환경에서만 이 대시보드에 액세스할 수 있습니다. 그러나 `app/Providers/HorizonServiceProvider.php` 파일에는 [authorization gate](/docs/{{version}}/authorization/gates) 정의가 있습니다. 이 권한 부여 게이트는 로컬이 아닌 환경에서 Horizon에 대한 액세스를 제어합니다. Horizon 설치에 대한 액세스를 제한하기 위해 필요에 따라 이 게이트를 자유롭게 수정할 수 있습니다.

    /**
     * Register the Horizon gate.
     *
     * This gate determines who can access Horizon in non-local environments.
     *
     * @return void
     */
    protected function gate()
    {
        Gate::define('viewHorizon', function ($user) {
            return in_array($user->email, [
                'taylor@laravel.com',
            ]);
        });
    }

<a name="alternative-authentication-strategies"></a>
#### Alternative Authentication Strategies
#### 대체 인증 전략

Remember that Laravel automatically injects the authenticated user into the gate closure. If your application is providing Horizon security via another method, such as IP restrictions, then your Horizon users may not need to "login". Therefore, you will need to change `function ($user)` closure signature above to `function ($user = null)` in order to force Laravel to not require authentication.

라라벨은 인증된 사용자를 자동으로 게이트 클로저에 주입한다는 것을 기억하십시오. 애플리케이션이 IP 제한과 같은 다른 방법을 통해 Horizon 보안을 제공하는 경우, Horizon 사용자는 "로그인"할 필요가 없습니다. 따라서 라라벨이 인증을 요구하지 않도록 하려면 위 `function($user)` 클로저를 `function($user = null)`으로 변경해야 합니다.

<a name="upgrading-horizon"></a>
## Upgrading Horizon
## Horizon 업그레이드

When upgrading to a new major version of Horizon, it's important that you carefully review [the upgrade guide](https://github.com/laravel/horizon/blob/master/UPGRADE.md). In addition, when upgrading to any new Horizon version, you should re-publish Horizon's assets:

Horizon의 새로운 메이저 버전으로 업그레이드할 때, [업그레이드 가이드](https://github.com/laravel/horizon/blob/master/UPGRADE.md) 를 자세히 살펴보세요. 또한 새 Horizon 버전으로 업그레이드할 때 Horizon 자산을 다시 게시해야 합니다.

```shell
php artisan horizon:publish
```

To keep the assets up-to-date and avoid issues in future updates, you may add the `horizon:publish` command to the `post-update-cmd` scripts in your application's `composer.json` file:

자산을 최신 상태로 유지하고 향후 업데이트에서 문제를 방지하려면, 애플리케이션의 `composer.json` 파일에 있는 `post-update-cmd` 스크립트에 `horizon:publish` 명령을 추가할 수 있습니다.

```json
{
    "scripts": {
        "post-update-cmd": [
            "@php artisan horizon:publish --ansi"
        ]
    }
}
```

<a name="running-horizon"></a>
## Running Horizon
## Horizon 실행하기

Once you have configured your supervisors and workers in your application's `config/horizon.php` configuration file, you may start Horizon using the `horizon` Artisan command. This single command will start all of the configured worker processes for the current environment:

애플리케이션의 `config/horizon.php` 설정 파일에서 supervisor와 작업자를 설정한 후에는 `horizon` Artisan 명령을 사용하여 Horizon을 시작할 수 있습니다. 이 명령은 현재 환경에 대해 설정된 모든 작업자 프로세스를 시작합니다.

```shell
php artisan horizon
```

You may pause the Horizon process and instruct it to continue processing jobs using the `horizon:pause` and `horizon:continue` Artisan commands:

Horizon 프로세스를 일시 중지하고 `horizon:pause` 및 `horizon:continue` Artisan 명령을 사용하여 작업을 계속 처리하도록 지시할 수 있습니다.

```shell
php artisan horizon:pause

php artisan horizon:continue
```

You may also pause and continue specific Horizon [supervisors](#supervisors) using the `horizon:pause-supervisor` and `horizon:continue-supervisor` Artisan commands:

`horizon:pause-supervisor` 및 `horizon:continue-supervisor` Artisan 명령을 사용하여 특정 Horizon [supervisor](#supervisors)를 일시 중지하고 계속할 수도 있습니다.

```shell
php artisan horizon:pause-supervisor supervisor-1

php artisan horizon:continue-supervisor supervisor-1
```

You may check the current status of the Horizon process using the `horizon:status` Artisan command:

`horizon:status` Artisan 명령을 사용하여 Horizon 프로세스의 현재 상태를 확인할 수 있습니다.

```shell
php artisan horizon:status
```

You may gracefully terminate the Horizon process using the `horizon:terminate` Artisan command. Any jobs that are currently being processed by will be completed and then Horizon will stop executing:

`horizon:terminate` Artisan 명령을 사용하여 Horizon 프로세스를 정상적으로 종료할 수 있습니다. 현재 처리 중인 모든 작업이 완료되면 Horizon이 실행을 중지합니다.

```shell
php artisan horizon:terminate
```

<a name="deploying-horizon"></a>
### Deploying Horizon
### Horizon 배포하기

When you're ready to deploy Horizon to your application's actual server, you should configure a process monitor to monitor the `php artisan horizon` command and restart it if it exits unexpectedly. Don't worry, we'll discuss how to install a process monitor below.

애플리케이션의 실제 서버에 Horizon을 배포할 준비가 되면 `php artisan horizon` 명령을 모니터링하도록 프로세스 모니터를 설정하고, 예기치 않게 종료되는 경우 다시 시작해야 합니다. 걱정하지 마십시오. 아래에서 프로세스 모니터를 설치하는 방법에 대해 설명합니다.

During your application's deployment process, you should instruct the Horizon process to terminate so that it will be restarted by your process monitor and receive your code changes:

애플리케이션 배포 프로세스 중에 Horizon 프로세스를 종료하도록, 지시하여 프로세스 모니터에서 다시 시작하고 코드 변경 사항을 수신하도록 해야 합니다.

```shell
php artisan horizon:terminate
```

<a name="installing-supervisor"></a>
#### Installing Supervisor
#### Supervisor 설치

Supervisor is a process monitor for the Linux operating system and will automatically restart your `horizon` process if it stops executing. To install Supervisor on Ubuntu, you may use the following command. If you are not using Ubuntu, you can likely install Supervisor using your operating system's package manager:

Supervisor는 Linux 운영 체제용 프로세스 모니터이며, 실행이 중지되면 `horizon` 프로세스를 자동으로 다시 시작합니다. Ubuntu에 Supervisor를 설치하려면 다음 명령을 사용할 수 있습니다. Ubuntu를 사용하지 않는 경우 운영 체제의 패키지 관리자를 사용하여 Supervisor를 설치할 수 있습니다.

```shell
sudo apt-get install supervisor
```

> {tip} If configuring Supervisor yourself sounds overwhelming, consider using [Laravel Forge](https://forge.laravel.com), which will automatically install and configure Supervisor for your Laravel projects.

> {tip} Supervisor를 직접 설정하는 것이 어렵게 들린다면 [라라벨 Forge](https://forge.laravel.com)를 사용해보십시오. 그러면 라라벨 프로젝트에 대해 Supervisor가 자동으로 설치 및 설정됩니다.

<a name="supervisor-configuration"></a>
#### Supervisor Configuration
#### Supervisor 설정하기

Supervisor configuration files are typically stored within your server's `/etc/supervisor/conf.d` directory. Within this directory, you may create any number of configuration files that instruct supervisor how your processes should be monitored. For example, let's create a `horizon.conf` file that starts and monitors a `horizon` process:

supervisor 설정 파일은 일반적으로 서버의 `/etc/supervisor/conf.d` 디렉토리에 저장됩니다. 이 디렉토리 내에서 supervisor에게 프로세스를 모니터링하는 방법을 지시하는 설정 파일을 원하는 만큼 생성할 수 있습니다. 예를 들어 `horizon` 프로세스를 시작하고 모니터링하는 `horizon.conf` 파일을 생성해 보겠습니다.

```ini
[program:horizon]
process_name=%(program_name)s
command=php /home/forge/example.com/artisan horizon
autostart=true
autorestart=true
user=forge
redirect_stderr=true
stdout_logfile=/home/forge/example.com/horizon.log
stopwaitsecs=3600
```

When defining your Supervisor configuration, you should ensure that the value of `stopwaitsecs` is greater than the number of seconds consumed by your longest running job. Otherwise, Supervisor may kill the job before it is finished processing.

Supervisor 설정을 정의할 때, `stopwaitsecs` 값이 가장 긴 실행 작업에서 소비하는 시간(초)보다 큰지 확인해야합니다. 그렇지 않으면 작업이 완료되기 전에 Supervisor 가 프로세스를 종료시킬 수 있습니다. 

> {note} While the examples above are valid for Ubuntu based servers, the location and file extension expected of Supervisor configuration files may vary between other server operating systems. Please consult your server's documentation for more information.

> {note} 위의 예시는 Ubuntu 기반 서버에서는 유효하지만, Supervisor 설정 파일의 위치및 확장자는 서버 운영체제에 따라 다를 수 있습니다. 보다 자세한 내용은 서버 매뉴얼을 참고하십시오.

<a name="starting-supervisor"></a>
#### Starting Supervisor
#### Supervisor 시작하기

Once the configuration file has been created, you may update the Supervisor configuration and start the monitored processes using the following commands:

설정 파일이 생성되면 다음 명령을 사용하여 Supervisor 설정을 업데이트하고 모니터링되는 프로세스를 시작할 수 있습니다.

```shell
sudo supervisorctl reread

sudo supervisorctl update

sudo supervisorctl start horizon
```

> {tip} For more information on running Supervisor, consult the [Supervisor documentation](http://supervisord.org/index.html).

> {tip} Supervisor 실행에 대한 자세한 내용은 [Supervisor 문서](http://supervisord.org/index.html) 를 참조하세요.

<a name="tags"></a>
## Tags
## 태그

Horizon allows you to assign “tags” to jobs, including mailables, broadcast events, notifications, and queued event listeners. In fact, Horizon will intelligently and automatically tag most jobs depending on the Eloquent models that are attached to the job. For example, take a look at the following job:

Horizon을 사용하면 메일링, 브로드캐스트 이벤트, 알림 및 대기 중인 이벤트 리스너를 비롯한 작업에 "태그"를 할당할 수 있습니다. 실제로 Horizon은 작업에 연결된 Eloquent 모델에 따라 대부분의 작업에 지능적이고 자동으로 태그를 지정합니다. 예를 들어 다음 작업을 살펴보십시오.

    <?php

    namespace App\Jobs;

    use App\Models\Video;
    use Illuminate\Bus\Queueable;
    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Foundation\Bus\Dispatchable;
    use Illuminate\Queue\InteractsWithQueue;
    use Illuminate\Queue\SerializesModels;

    class RenderVideo implements ShouldQueue
    {
        use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

        /**
         * The video instance.
         *
         * @var \App\Models\Video
         */
        public $video;

        /**
         * Create a new job instance.
         *
         * @param  \App\Models\Video  $video
         * @return void
         */
        public function __construct(Video $video)
        {
            $this->video = $video;
        }

        /**
         * Execute the job.
         *
         * @return void
         */
        public function handle()
        {
            //
        }
    }

If this job is queued with an `App\Models\Video` instance that has an `id` attribute of `1`, it will automatically receive the tag `App\Models\Video:1`. This is because Horizon will search the job's properties for any Eloquent models. If Eloquent models are found, Horizon will intelligently tag the job using the model's class name and primary key:

이 작업은 `id`의 속성이 `1`인 `App\Models\Video` 인스턴스가 대기열에 들어올 경우, 자동으로 `App\Models\Video:1` 태그를 생성합니다. Horizon이 Eloquent 모델에 대한 작업 속성을 검색하기 때문입니다. Eloquent 모델이 발견되면 Horizon은 모델의 클래스 이름과 기본 키를 사용하여 작업에 지능적으로 태그를 지정합니다.

    use App\Jobs\RenderVideo;
    use App\Models\Video;

    $video = Video::find(1);

    RenderVideo::dispatch($video);

<a name="manually-tagging-jobs"></a>
#### Manually Tagging Jobs
#### 수동으로 태그 지정하기

If you would like to manually define the tags for one of your queueable objects, you may define a `tags` method on the class:

queueable objects에 수동으로 태그를 정하고 싶은 경우 클래스의 `tags` 메소드를 정의 하면 됩니다.

    class RenderVideo implements ShouldQueue
    {
        /**
         * Get the tags that should be assigned to the job.
         *
         * @return array
         */
        public function tags()
        {
            return ['render', 'video:'.$this->video->id];
        }
    }

<a name="notifications"></a>
## Notifications
## 알림

> {note} When configuring Horizon to send Slack or SMS notifications, you should review the [prerequisites for the relevant notification channel](/docs/{{version}}/notifications).

> {note} Slack 또는 SMS 알림을 보내도록 Horizon을 설정할 때 [해당 알림 채널의 전제 조건](/docs/{{version}}/notifications)을 검토해야 합니다.

If you would like to be notified when one of your queues has a long wait time, you may use the `Horizon::routeMailNotificationsTo`, `Horizon::routeSlackNotificationsTo`, and `Horizon::routeSmsNotificationsTo` methods. You may call these methods from the `boot` method of your application's `App\Providers\HorizonServiceProvider`:

대기열 중 하나가 긴 대기 시간을 가질 때 알림을 받고 싶다면 `Horizon::routeMailNotificationsTo`, `Horizon::routeSlackNotificationsTo` 및 `Horizon::routeSmsNotificationsTo` 메소드를 사용할 수 있습니다. 애플리케이션의 `App\Providers\HorizonServiceProvider`의 `boot` 메소드에서 다음 메소드를 호출할 수 있습니다.

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        parent::boot();

        Horizon::routeSmsNotificationsTo('15556667777');
        Horizon::routeMailNotificationsTo('example@example.com');
        Horizon::routeSlackNotificationsTo('slack-webhook-url', '#channel');
    }

<a name="configuring-notification-wait-time-thresholds"></a>
#### Configuring Notification Wait Time Thresholds
#### 알림 대기 시간의 임계값 설정하기

You may configure how many seconds are considered a "long wait" within your application's `config/horizon.php` configuration file. The `waits` configuration option within this file allows you to control the long wait threshold for each connection / queue combination:

애플리케이션의 `config/horizon.php` 설정 파일 내에서 "긴 대기"로 간주되는 시간(초)을 설정할 수 있습니다. 이 파일의 `waits` 설정 옵션을 사용하면 각 연결/대기열 조합에 대한 긴 대기 임계값을 제어할 수 있습니다.

    'waits' => [
        'redis:default' => 60,
        'redis:critical,high' => 90,
    ],

<a name="metrics"></a>
## Metrics
## 메트릭

Horizon includes a metrics dashboard which provides information regarding your job and queue wait times and throughput. In order to populate this dashboard, you should configure Horizon's `snapshot` Artisan command to run every five minutes via your application's [scheduler](/docs/{{version}}/scheduling):

Horizon에는 작업 및 대기열 대기 시간 및 처리량에 대한 정보를 제공하는 메트릭 대시보드가 포함되어 있습니다. 이 대시보드를 채우려면 애플리케이션의 [스케줄러](/docs/{{version}}/scheduling)를 통해 5분마다 실행되도록 Horizon의 `snapshot` Artisan 명령을 설정해야 합니다.

    /**
     * Define the application's command schedule.
     *
     * @param  \Illuminate\Console\Scheduling\Schedule  $schedule
     * @return void
     */
    protected function schedule(Schedule $schedule)
    {
        $schedule->command('horizon:snapshot')->everyFiveMinutes();
    }

<a name="deleting-failed-jobs"></a>
## Deleting Failed Jobs
## 실패한 작업 삭제

If you would like to delete a failed job, you may use the `horizon:forget` command. The `horizon:forget` command accepts the ID or UUID of the failed job as its only argument:

실패한 작업을 삭제하려면 `horizon:forget` 명령을 사용할 수 있습니다. `horizon:forget` 명령은 실패한 작업의 ID 또는 UUID를 유일한 인수로 받아들입니다.

```shell
php artisan horizon:forget 5
```

<a name="clearing-jobs-from-queues"></a>
## Clearing Jobs From Queues
## 대기열에서 작업 지우기

If you would like to delete all jobs from your application's default queue, you may do so using the `horizon:clear` Artisan command:

애플리케이션의 기본 대기열에서 모든 작업을 삭제하려면 `horizon:clear` Artisan 명령을 사용하여 삭제할 수 있습니다.

```shell
php artisan horizon:clear
```

You may provide the `queue` option to delete jobs from a specific queue:

특정 대기열에서 작업을 삭제하기 위해 `queue` 옵션을 추가할 수도 있습니다.

```shell
php artisan horizon:clear --queue=emails
```
