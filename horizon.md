# 라라벨 Horizon

- [시작하기](#introduction)
- [설치하기](#installation)
  - [설정하기](#configuration)
  - [밸런싱 전략](#balancing-strategies)
  - [Dashboard 권한부여](#dashboard-authorization)
- [Horizon 업그레이드](#upgrading-horizon)
- [Horizon 실행하기](#running-horizon)
  - [Horizon 배포하기](#deploying-horizon)
- [태그](#tags)
- [알림](#notifications)
- [메트릭](#metrics)
- [실패한 작업 삭제](#deleting-failed-jobs)
- [대기열에서 작업 지우기](#clearing-jobs-from-queues)

<a name="introduction"></a>
## 시작하기

> {tip} 라라벨 Horizon에 대해 알아보기 전에 라라벨의 기본 [queue services](/docs/{{version}}/queues)에 익숙해져야 합니다. Horizon은 라라벨에서 제공하는 기본 대기열 기능에 아직 익숙하지 않은 경우 혼동될 수 있는 추가 기능으로 라라벨의 대기열을 보강합니다.

[라라벨 Horizon](https://github.com/laravel/horizon) 은 라라벨 기반 [Redis 대기열-queue](/docs/{{version}}/queues)을 위한 아름다운 대시보드 및 코드 기반 설정을 제공합니다. Horizon을 사용하면 작업 처리량, 런타임 및 작업 실패와 같은 대기열 시스템의 주요 메트릭을 쉽게 모니터링할 수 있습니다.

Horizon을 사용하는 경우, 모든 대기열 작업자 설정은 하나의 간단한 설정 파일에 저장됩니다. 버전 제어 파일에서 애플리케이션의 작업자 설정을 정의하면 애플리케이션을 배포할 때 애플리케이션의 대기열 작업자를 쉽게 확장하거나 수정할 수 있습니다.

<img src="https://laravel.com/img/docs/horizon-example.png">

<a name="installation"></a>
## 설치하기

> {note} 라라벨 Horizon을 사용하려면 [Redis](https://redis.io) 를 사용해야 합니다. 따라서 애플리케이션의 `config/queue.php` 설정 파일에서 대기열 연결이 `redis`로 설정되어 있는지 확인해야 합니다.

Composer 패키지 관리자를 사용하여 프로젝트에 Horizon을 설치할 수 있습니다.

    composer require laravel/horizon

Horizon을 설치한 후 `horizon:install` Artisan 명령을 사용하여 자산을 게시합니다.

    php artisan horizon:install

<a name="configuration"></a>
### 설정하기

Horizon 자산을 게시하면 기본 설정 파일이 `config/horizon.php`로 생성됩니다. 이 설정 파일을 사용하면 애플리케이션에 대한 대기열 작업자 옵션을 설정할 수 있습니다. 각 설정 옵션에는 용도에 대한 설명이 포함되어 있으므로 이 파일을 꼼꼼하게 살펴보십시오.

> {note} Horizon은 내부적으로 `horizon`이라는 Redis 연결을 사용합니다. 이 Redis 연결 이름은 예약되어 있으므로 `database.php` 설정 파일의 다른 Redis 연결에 할당하거나 `horizon.php` 설정 파일의 `use` 옵션 값으로 할당해서는 안 됩니다.

<a name="environments"></a>
#### 환경설정

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

Horizon을 시작하면 애플리케이션이 실행되는 환경에 대한 작업자 프로세스 설정 옵션이 사용됩니다. 일반적으로 환경은 `APP_ENV` [환경 변수](/docs/{{version}}/configuration#determining-the-current-environment)의 값에 의해 결정됩니다. 예를 들어 기본 `local` Horizon 환경은 3개의 작업자 프로세스를 시작하고 각 대기열에 할당된 작업자 프로세스 수의 균형을 자동으로 조정하도록 설정됩니다. 기본 `production` 환경은 최대 10개의 작업자 프로세스를 시작하고 각 대기열에 할당된 작업자 프로세스 수의 균형을 자동으로 조정하도록 설정됩니다.

> {note} `horizon` 설정 파일의 `environments` 부분에 Horizon을 실행할 각 [environment](/docs/{{version}}/configuration#environment-configuration) 에 대한 항목이 포함되어 있는지 확인해야 합니다.

<a name="supervisors"></a>
#### Supervisor

Horizon의 기본 설정 파일에서 볼 수 있듯이. 각 환경에는 하나 이상의 "supervisors"가 포함될 수 있습니다. 기본적으로 설정 파일은 이 수퍼바이저를 `supervisor-1`로 정의합니다. 그러나 supervisor의 이름은 원하는 대로 지정할 수 있습니다. 각 supervisor는 기본적으로 작업자 프로세스 그룹을 "감독"할 책임이 있으며 대기열 전체에서 작업자 프로세스의 균형을 조정합니다.

해당 환경에서 실행해야 하는 작업자 프로세스의 새 그룹을 정의하려는 경우 지정된 환경에 supervisor를 추가할 수 있습니다. 애플리케이션에서 사용하는 주어진 대기열에 대해 다른 밸런싱 전략 또는 작업자 프로세스 수를 정의하려는 경우 이 작업을 수행하도록 선택할 수 있습니다.

<a name="default-values"></a>
#### 기본값

Horizon의 기본 설정 파일 내에서 `defaults` 설정 옵션을 확인할 수 있습니다. 이 설정 옵션은 애플리케이션의 [supervisors](#supervisors)에 대한 기본값을 지정합니다. supervisor의 기본 설정 값은 각 환경에 대한 supervisor의 설정에 병합되므로 supervisor를 정의할 때 불필요한 반복을 피할 수 있습니다.

<a name="balancing-strategies"></a>
### 밸런싱 전략

라라벨의 기본 대기열 시스템과 달리 Horizon에서는 `simple`, `auto` 및 `false`의 세 가지 작업자 밸런싱 전략 중에서 선택할 수 있습니다. 설정 파일의 기본값인 `simple` 전략은 들어오는 작업을 작업자 프로세스 간에 균등하게 분할합니다.

    'balance' => 'simple',

`auto` 전략은 대기열의 현재 작업 부하를 기반으로 대기열당 작업자 프로세스 수를 조정합니다. 예를 들어 `notifications` 대기열에 1,000개의 보류 중인 작업이 있고 `render` 대기열은 비어 있는 경우 Horizon은 대기열이 비어 있을 때까지 `notifications` 대기열에 더 많은 작업자를 할당합니다.

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

`balanceMaxShift` 및 `balanceCooldown` 설정 값은 작업자 수요를 충족하기 위해 Horizon이 얼마나 빨리 확장되는지 결정합니다. 위의 예에서 최대 하나의 새 프로세스가 3초마다 생성되거나 소멸됩니다. 애플리케이션의 필요에 따라 필요에 따라 이러한 값을 자유롭게 조정할 수 있습니다.

`balance` 옵션이 `false`로 설정되면 설정에 나열된 순서대로 대기열을 처리하는 기본 라라벨 동작이 사용됩니다.

<a name="dashboard-authorization"></a>
### Dashboard 권한 부여

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
#### 대체 인증 전략

라라벨은 인증된 사용자를 자동으로 게이트 클로저에 주입한다는 것을 기억하십시오. 애플리케이션이 IP 제한과 같은 다른 방법을 통해 Horizon 보안을 제공하는 경우, Horizon 사용자는 "로그인"할 필요가 없습니다. 따라서 라라벨이 인증을 요구하지 않도록 하려면 위 `function($user)` 클로저를 `function($user = null)`으로 변경해야 합니다.

<a name="upgrading-horizon"></a>
## Horizon 업그레이드

Horizon의 새로운 메이저 버전으로 업그레이드할 때, [업그레이드 가이드](https://github.com/laravel/horizon/blob/master/UPGRADE.md) 를 자세히 살펴보세요. 또한 새 Horizon 버전으로 업그레이드할 때 Horizon 자산을 다시 게시해야 합니다.

    php artisan horizon:publish

자산을 최신 상태로 유지하고 향후 업데이트에서 문제를 방지하려면, 애플리케이션의 `composer.json` 파일에 있는 `post-update-cmd` 스크립트에 `horizon:publish` 명령을 추가할 수 있습니다.

    {
        "scripts": {
            "post-update-cmd": [
                "@php artisan horizon:publish --ansi"
            ]
        }
    }

<a name="running-horizon"></a>
## Horizon 실행하기

애플리케이션의 `config/horizon.php` 설정 파일에서 supervisor와 작업자를 설정한 후에는 `horizon` Artisan 명령을 사용하여 Horizon을 시작할 수 있습니다. 이 명령은 현재 환경에 대해 설정된 모든 작업자 프로세스를 시작합니다.

    php artisan horizon

Horizon 프로세스를 일시 중지하고 `horizon:pause` 및 `horizon:continue` Artisan 명령을 사용하여 작업을 계속 처리하도록 지시할 수 있습니다.

    php artisan horizon:pause

    php artisan horizon:continue

`horizon:pause-supervisor` 및 `horizon:continue-supervisor` Artisan 명령을 사용하여 특정 Horizon [supervisor](#supervisors)를 일시 중지하고 계속할 수도 있습니다.

    php artisan horizon:pause-supervisor supervisor-1

    php artisan horizon:continue-supervisor supervisor-1

`horizon:status` Artisan 명령을 사용하여 Horizon 프로세스의 현재 상태를 확인할 수 있습니다.

    php artisan horizon:status

`horizon:terminate` Artisan 명령을 사용하여 Horizon 프로세스를 정상적으로 종료할 수 있습니다. 현재 처리 중인 모든 작업이 완료되면 Horizon이 실행을 중지합니다.

    php artisan horizon:terminate

<a name="deploying-horizon"></a>
### Horizon 배포하기

애플리케이션의 실제 서버에 Horizon을 배포할 준비가 되면 `php artisan horizon` 명령을 모니터링하도록 프로세스 모니터를 설정하고, 예기치 않게 종료되는 경우 다시 시작해야 합니다. 걱정하지 마십시오. 아래에서 프로세스 모니터를 설치하는 방법에 대해 설명합니다.

애플리케이션 배포 프로세스 중에 Horizon 프로세스를 종료하도록, 지시하여 프로세스 모니터에서 다시 시작하고 코드 변경 사항을 수신하도록 해야 합니다.

    php artisan horizon:terminate

<a name="installing-supervisor"></a>
#### Supervisor 설치

Supervisor는 Linux 운영 체제용 프로세스 모니터이며, 실행이 중지되면 `horizon` 프로세스를 자동으로 다시 시작합니다. Ubuntu에 Supervisor를 설치하려면 다음 명령을 사용할 수 있습니다. Ubuntu를 사용하지 않는 경우 운영 체제의 패키지 관리자를 사용하여 Supervisor를 설치할 수 있습니다.

    sudo apt-get install supervisor

> {tip} Supervisor를 직접 설정하는 것이 어렵게 들린다면 [라라벨 Forge](https://forge.laravel.com)를 사용해보십시오. 그러면 라라벨 프로젝트에 대해 Supervisor가 자동으로 설치 및 설정됩니다.

<a name="supervisor-configuration"></a>
#### Supervisor 설정하기

supervisor 설정 파일은 일반적으로 서버의 `/etc/supervisor/conf.d` 디렉토리에 저장됩니다. 이 디렉토리 내에서 supervisor에게 프로세스를 모니터링하는 방법을 지시하는 설정 파일을 원하는 만큼 생성할 수 있습니다. 예를 들어 `horizon` 프로세스를 시작하고 모니터링하는 `horizon.conf` 파일을 생성해 보겠습니다.

    [program:horizon]
    process_name=%(program_name)s
    command=php /home/forge/example.com/artisan horizon
    autostart=true
    autorestart=true
    user=forge
    redirect_stderr=true
    stdout_logfile=/home/forge/example.com/horizon.log
    stopwaitsecs=3600

> {note} `stopwaitsecs`의 값이 가장 긴 실행 작업에서 소비하는 시간(초)보다 큰지 확인해야합니다. 그렇지 않으면 supervisor가 작업을 처리를 완료하기 전에 종료 할 수 있습니다.

<a name="starting-supervisor"></a>
#### Supervisor 시작하기

설정 파일이 생성되면 다음 명령을 사용하여 Supervisor 설정을 업데이트하고 모니터링되는 프로세스를 시작할 수 있습니다.

    sudo supervisorctl reread

    sudo supervisorctl update

    sudo supervisorctl start horizon

> {tip} Supervisor 실행에 대한 자세한 내용은 [Supervisor 문서](http://supervisord.org/index.html) 를 참조하세요.

<a name="tags"></a>
## 태그

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

이 작업은 `id`의 속성이 `1`인 `App\Models\Video` 인스턴스가 대기열에 들어올 경우, 자동으로 `App\Models\Video:1` 태그를 생성합니다. Horizon이 Eloquent 모델에 대한 작업 속성을 검색하기 때문입니다. Eloquent 모델이 발견되면 Horizon은 모델의 클래스 이름과 기본 키를 사용하여 작업에 지능적으로 태그를 지정합니다.

    use App\Jobs\RenderVideo;
    use App\Models\Video;

    $video = Video::find(1);

    RenderVideo::dispatch($video);

<a name="manually-tagging-jobs"></a>
#### 수동으로 태그 지정하기

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
## 알림

> {note} Slack 또는 SMS 알림을 보내도록 Horizon을 설정할 때 [해당 알림 채널의 전제 조건](/docs/{{version}}/notifications)을 검토해야 합니다.

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
#### 알림 대기 시간의 임계값 설정하기

애플리케이션의 `config/horizon.php` 설정 파일 내에서 "긴 대기"로 간주되는 시간(초)을 설정할 수 있습니다. 이 파일의 `waits` 설정 옵션을 사용하면 각 연결/대기열 조합에 대한 긴 대기 임계값을 제어할 수 있습니다.

    'waits' => [
        'redis:default' => 60,
        'redis:critical,high' => 90,
    ],

<a name="metrics"></a>
## 메트릭

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
## 실패한 작업 삭제

실패한 작업을 삭제하려면 `horizon:forget` 명령을 사용할 수 있습니다. `horizon:forget` 명령은 실패한 작업의 ID 또는 UUID를 유일한 인수로 받아들입니다.

    php artisan horizon:forget 5

<a name="clearing-jobs-from-queues"></a>
## 대기열에서 작업 지우기

애플리케이션의 기본 대기열에서 모든 작업을 삭제하려면 `horizon:clear` Artisan 명령을 사용하여 삭제할 수 있습니다.

    php artisan horizon:clear

특정 대기열에서 작업을 삭제하기 위해 `queue` 옵션을 추가할 수도 있습니다.

    php artisan horizon:clear --queue=emails
