# 라라벨 Telescope

- [시작하기](#introduction)
- [설치하기](#installation)
    - [로컬 전용으로 설치하기](#local-only-installation)
    - [환경설정](#configuration)
    - [데이터 정리](#data-pruning)
    - [Dashboard 권한 부여](#dashboard-authorization)
- [Telescope 업그레이드](#upgrading-telescope)
- [Filtering](#filtering)
    - [Entries](#filtering-entries)
    - [Batches](#filtering-batches)
- [Tagging](#tagging)
- [사용가능한 watcher](#available-watchers)
    - [Batch Watcher](#batch-watcher)
    - [Cache Watcher](#cache-watcher)
    - [Command Watcher](#command-watcher)
    - [Dump Watcher](#dump-watcher)
    - [Event Watcher](#event-watcher)
    - [Exception Watcher](#exception-watcher)
    - [Gate Watcher](#gate-watcher)
    - [HTTP Client Watcher](#http-client-watcher)
    - [Job Watcher](#job-watcher)
    - [Log Watcher](#log-watcher)
    - [Mail Watcher](#mail-watcher)
    - [Model Watcher](#model-watcher)
    - [Notification Watcher](#notification-watcher)
    - [Query Watcher](#query-watcher)
    - [Redis Watcher](#redis-watcher)
    - [Request Watcher](#request-watcher)
    - [Schedule Watcher](#schedule-watcher)
    - [View Watcher](#view-watcher)
- [사용자 아바타 표시](#displaying-user-avatars)

<a name="introduction"></a>
## 시작하기

[라라벨 Telescope](https://github.com/laravel/telescope) 는 로컬 라라벨 개발 환경의 훌륭한 안내서입니다. Telescope는 애플리케이션으로 들어오는 요청, 예외, 로그 항목, 데이터베이스 쿼리, queue-큐 작업, 메일, 알림, 캐시 작업, 스케줄링 작업, 변수 덤프 등에 대한 분석을 제공합니다.

<img src="https://laravel.com/img/docs/telescope-example.png">

<a name="installation"></a>
## 설치하기

컴포저 패키지 관리자를 사용하여 라라벨 프로젝트에 Telescope를 설치할 수 있습니다.

```shell
composer require laravel/telescope
```

Telescope를 설치한 후 `telescope:install` Artisan 명령을 사용하여 자산-assets을 게시합니다. Telescope를 설치한 후 Telescope의 데이터를 저장하는 데 필요한 테이블을 생성하기 위해 `migrate` 명령어도 실행해야 합니다.

```shell
php artisan telescope:install

php artisan migrate
```

<a name="migration-customization"></a>
#### 사용자 정의 마이그레이션

Telescope의 기본 마이그레이션을 사용하지 않으려면 애플리케이션의 `App\Providers\AppServiceProvider` 클래스의 `register` 메소드에서 `Telescope::ignoreMigrations` 메소드를 호출해야 합니다. 다음 명령어을 사용하여 기본 마이그레이션을 내보낼 수 있습니다. `php artisan vendor:publish --tag=telescope-migrations`

<a name="local-only-installation"></a>
### 로컬 전용 설치

로컬 개발환경에서만 Telescope를 사용할 계획이라면 `--dev` 플래그를 사용하여 Telescope를 설치할 수 있습니다.

```shell
composer require laravel/telescope --dev

php artisan telescope:install

php artisan migrate
```

`telescope:install`을 실행한 후 애플리케이션의 `config/app.php` 설정 파일에서 `TelescopeServiceProvider` 서비스 프로바이더 등록을 제거해야 합니다. 대신 `App\Providers\AppServiceProvider` 클래스의 `register` 메소드에 Telescope의 서비스 프로바이더를 수동으로 등록하세요. 프로바이를 등록하기 전에 현재 환경변수가 `local`인지 확인합니다.

    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        if ($this->app->environment('local')) {
            $this->app->register(\Laravel\Telescope\TelescopeServiceProvider::class);
            $this->app->register(TelescopeServiceProvider::class);
        }
    }

마지막으로 `composer.json` 파일에 다음을 추가하여 Telescope 패키지가 [자동 검색](/docs/{{version}}/packages#package-discovery)되지 않도록 해야 합니다.

```json
"extra": {
    "laravel": {
        "dont-discover": [
            "laravel/telescope"
        ]
    }
},
```

<a name="configuration"></a>
### 환경설정

Telescope의 자산-assets을 퍼블리싱하면 기본 설정 파일 `config/telescope.php`이 생성됩니다. 이 설정 파일을 사용하면 [감시자(watcher) 옵션](#available-watchers)을 변경 할 수 있으며 각 설정 옵션에는 용도에 대한 설명이 포함되므로 이 파일을 철저히 확인하십시오.

원하는 경우, `enabled` 설정 옵션을 사용하여 Telescope의 데이터 수집을 완전히 비활성화 할 수 있습니다.

    'enabled' => env('TELESCOPE_ENABLED', true),

<a name="data-pruning"></a>
### 데이터 정리

데이터 정리를 하지 않는 다면, `telescope_entries` 테이블은 레코드가 빨리 누적 될 수 있습니다. 이것을 줄이기 위해 `telescope:prune` 아티즌 명령을 매일 실행되도록 [스케줄링](/docs/{{version}}/scheduling)에 등록 해야합니다.

    $schedule->command('telescope:prune')->daily();

기본적으로 24시간이 넘은 항목은 모두 제거됩니다. 명령을 호출 할 때 `hours` 옵션을 사용하여 Telescope 데이터를 얼마나 오래 저장할지를 결정할 수 있습니다. 예를 들어, 다음 명령은 48 시간 전에 생성 된 모든 레코드를 삭제합니다.

    $schedule->command('telescope:prune --hours=48')->daily();

<a name="dashboard-authorization"></a>
### Dashboard 권한 부여

Telescope 대시보드는 `/telescope` 으로 접속 가능합니다. 기본적으로 `local` 환경에서만 이 대시보드에 접근이 가능합니다. `app/Providers/TelescopeServiceProvider.php` 파일에는 [인증 게이트](/docs/{{version}}/authorization#gates) 정의가 있습니다. 이 인증 게이트는 **로컬이 아닌** 환경에서 Telescope에 대한 액세스를 제어합니다. Telescope 설치에 대한 액세스를 제한하기 위해 필요에 따라 이 게이트를 자유롭게 수정할 수 있습니다.

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

> **Warning**
> 프로덕션 환경에서 `APP_ENV` 환경 변수를 `production`으로 변경해야합니다. 그렇지 않으면 Telescope가 공개적으로 설치됩니다.

<a name="upgrading-telescope"></a>
## Telescope 업그레이드

새로운 주요 버전의 Telescope로 업그레이드 할 때 [업그레이드 가이드](https://github.com/laravel/telescope/blob/master/UPGRADE.md) 를 주의 깊게 검토하는 것이 중요합니다.

또한 새로운 Telescope 버전으로 업그레이드 할 때 Telescope의 자산-assets을 다시 게시해야합니다.

```shell
php artisan telescope:publish
```

자산-assets을 최신 상태로 유지하고 향후 업데이트에서 문제를 방지하려면 애플리케이션의 `composer.json` 파일에있는 `post-update-cmd` 스크립트에 `vendor:publish --tag=laravel-assets` 명령을 추가하면 됩니다.

```json
{
    "scripts": {
        "post-update-cmd": [
            "@php artisan vendor:publish --tag=laravel-assets --ansi --force"
        ]
    }
}
```

<a name="filtering"></a>
## 필터링

<a name="filtering-entries"></a>
### Entries

`App\Providers\TelescopeServiceProvider` 클래스에 정의된 `filter` 클로저를 통해 Telescope에 의해 기록된 데이터를 필터링할 수 있습니다. 기본적으로 이 클로저는 `local` 환경의 모든 데이터와 exceptions-예외, 실패한 job, 스케줄링 작업 및 모니터링 태깅된 데이터를 기록합니다.

    use Laravel\Telescope\IncomingEntry;
    use Laravel\Telescope\Telescope;

    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        $this->hideSensitiveRequestDetails();

        Telescope::filter(function (IncomingEntry $entry) {
            if ($this->app->environment('local')) {
                return true;
            }

            return $entry->isReportableException() ||
                $entry->isFailedJob() ||
                $entry->isScheduledTask() ||
                $entry->isSlowQuery() ||
                $entry->hasMonitoredTag();
        });
    }

<a name="filtering-batches"></a>
### Batches

`filter` 클로저는 개별 항목에 대한 데이터를 필터링하는 동안, `filterBatch` 메소드를 사용하여 주어진 request-요청 또는 콘솔 명령어에 대한 모든 데이터를 필터링 하는 클로저를 등록할 수 있습니다. 클로저가 `true`를 반환하면 모든 항목이 Telescope에 의해 기록됩니다.

    use Illuminate\Support\Collection;
    use Laravel\Telescope\Telescope;

    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        $this->hideSensitiveRequestDetails();

        Telescope::filterBatch(function (Collection $entries) {
            if ($this->app->environment('local')) {
                return true;
            }

            return $entries->contains(function ($entry) {
                return $entry->isReportableException() ||
                    $entry->isFailedJob() ||
                    $entry->isScheduledTask() ||
                    $entry->isSlowQuery() ||
                    $entry->hasMonitoredTag();
                });
        });
    }

<a name="tagging"></a>
## Tagging

Telescope을 사용하면 "태그"로 항목을 검색할 수 있습니다. 종종 태그는 Eloquent 모델 클래스 이름이거나 Telescope가 항목에 자동으로 추가하는 인증 된 사용자 ID입니다. 경우에 따라 entries에 고유한 커스텀 태그를 첨부할 수 있습니다. 이를 위해 `Telescope::tag` 메소드를 사용할 수 있습니다. `tag` 메소드는 태그 배열을 반환해야 하는 클로저를 허용합니다. 클로저에 의해 반환 된 태그는 Telescope가 자동으로 항목에 첨부 하는 모든 태그와 합쳐집니다. 일반적으로 `App\Providers\TelescopeServiceProvider` 클래스의 `register` 메소드 내에서 `tag` 메소드를 호출해야 합니다.

    use Laravel\Telescope\IncomingEntry;
    use Laravel\Telescope\Telescope;

    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        $this->hideSensitiveRequestDetails();

        Telescope::tag(function (IncomingEntry $entry) {
            return $entry->type === 'request'
                        ? ['status:'.$entry->content['response_status']]
                        : [];
        });
     }

<a name="available-watchers"></a>
## 사용가능한 와처

Telescope 와처는 유입되는 request-요청이나 콘솔 명령어가 실행될때 애플리케이션 데이터를 수집합니다. `config/telescope.php` 설정 파일에서 활성화하려는 와처 리스트를 변경할 수 있습니다.

    'watchers' => [
        Watchers\CacheWatcher::class => true,
        Watchers\CommandWatcher::class => true,
        ...
    ],

일부 와처에는 추가적으로 지정할 수 있는 사용자 정의 옵션을 제공하기도 합니다.

    'watchers' => [
        Watchers\QueryWatcher::class => [
            'enabled' => env('TELESCOPE_QUERY_WATCHER', true),
            'slow' => 100,
        ],
        ...
    ],

<a name="batch-watcher"></a>
### Batch Watcher

배치 와처는 작업-job 및 연결-connection 정보를 포함하여 대기 중인 [배치](/docs/{{version}}/queues#job-batching)에 대한 정보를 기록합니다.

<a name="cache-watcher"></a>
### Cache Watcher

캐시 와처는 캐시 키가 hit, miss, update, forget 할 때 데이터를 기록합니다.

<a name="command-watcher"></a>
### Command Watcher

커맨드 와처는 아티즌 명령어가 실행될 때마다 인수, 옵션, exit code 및 출력을 기록합니다. 와처가 기록하는 특정 커맨드을 제외하려면 `config/telescope.php` 파일의 `ignore` 옵션에 명령을 지정할 수 있습니다.

    'watchers' => [
        Watchers\CommandWatcher::class => [
            'enabled' => env('TELESCOPE_COMMAND_WATCHER', true),
            'ignore' => ['key:generate'],
        ],
        ...
    ],

<a name="dump-watcher"></a>
### Dump Watcher

덤프 와처는 변수를 dump 한것을 Telescope 에 기록하고 표시합니다. 라라벨을 사용할 때 글로벌 `dump` 함수를 사용하여 변수를 덤프할 수 있습니다. 덤프를 기록하려면 덤프 와처 탭이 브라우저에서 열려 있어야 합니다. 그렇지 않으면 와처가 덤프를 무시합니다.

<a name="event-watcher"></a>
### Event Watcher

이벤트 와처는 애플리케이션에서 전달한 모든 [이벤트](/docs/{{version}}/events)에 대한 페이로드, 리스너 및 브로드캐스트 데이터를 기록합니다. 라라벨 프레임워크의 내부 이벤트는 이벤트 와처에 의해 무시됩니다.

<a name="exception-watcher"></a>
### Exception Watcher

예외-exception 와처는 애플리케이션에서 발생하는 보고 가능한 모든 예외에 대한 데이터 및 스택 트레이스를 기록합니다.

<a name="gate-watcher"></a>
### Gate Watcher

게이트 와처는 애플리케이션의 [게이트 및 정책(policy)](/docs/{{version}}/authorization) 검사 데이터와 결과를 기록합니다. 와처가 특정 기능을 실행하는지 기록히자 않으려면 `config/telescope.php` 설정 파일의 `ignore_abilities` 옵션에서 지정할 수 있습니다.

    'watchers' => [
        Watchers\GateWatcher::class => [
            'enabled' => env('TELESCOPE_GATE_WATCHER', true),
            'ignore_abilities' => ['viewNova'],
        ],
        ...
    ],

<a name="http-client-watcher"></a>
### HTTP Client Watcher

HTTP 클라이언트 와처는 애플리케이션에서 보내는 [HTTP 클라이언트 요청-requests](/docs/{{version}}/http-client)을 기록합니다.

<a name="job-watcher"></a>
### Job Watcher

작업-job 와처는 애플리케이션에서 [jobs](/docs/{{version}}/queues)이 처리하는 데이터와 상태를 기록합니다.

<a name="log-watcher"></a>
### Log Watcher

로그 와처는 애플리케이션에서 작성하는 모든 [로그 데이터](/docs/{{version}}/logging)를 기록합니다.

<a name="mail-watcher"></a>
### Mail Watcher

메일 와처를 사용하면 관련 데이터와 함께 애플리케이션에서 보낸 [emails](/docs/{{version}}/mail)을 브라우저에서 미리보기를 할 수 있습니다. 이메일 내역을 `.eml` 파일로 다운로드 할 수도 있습니다.

<a name="model-watcher"></a>
### Model Watcher

모델 와처는 Eloquent [모델 이벤트](/docs/{{version}}/eloquent#events)가 발생할 때 모델이 변경되는 내역을 기록합니다. 와처의 `events` 옵션을 통해서 어떤 모델의 이벤트가 기록되어야 하는지 지정할 수 있습니다.

    'watchers' => [
        Watchers\ModelWatcher::class => [
            'enabled' => env('TELESCOPE_MODEL_WATCHER', true),
            'events' => ['eloquent.created*', 'eloquent.updated*'],
        ],
        ...
    ],

주어진 요청-request 동안 공급된-hydrations 모델의 수를 기록하려면 `hydrations` 옵션을 활성화하십시오.

    'watchers' => [
        Watchers\ModelWatcher::class => [
            'enabled' => env('TELESCOPE_MODEL_WATCHER', true),
            'events' => ['eloquent.created*', 'eloquent.updated*'],
            'hydrations' => true,
        ],
        ...
    ],

<a name="notification-watcher"></a>
### Notification Watcher

알림 와처는 애플리케이션에서 전달하는 모든 [알림](/docs/{{version}}/notifications)을 기록합니다. 알림이 이메일을 전송하고, 메일 와처가 활성화 된다면, 이메일은 메일 와처를 통해서 내용을 확인할 수 있습니다.

<a name="query-watcher"></a>
### Query Watcher

쿼리 와처는 애플리케이션에서 실행되는 모든 쿼리에 대한 raw SQL 과 바딩인 파라미터, 실행 시각을 기록합니다. 와처는 쿼리가 100ms 이상 느려질때 `slow` 태그를 붙입니다. `slow` 옵션을 사용해서 슬로우 쿼리 기준 시각을 변경할 수 있습니다.

    'watchers' => [
        Watchers\QueryWatcher::class => [
            'enabled' => env('TELESCOPE_QUERY_WATCHER', true),
            'slow' => 50,
        ],
        ...
    ],

<a name="redis-watcher"></a>
### Redis Watcher

Redis 와처는 애플리케이션에서 실행되는 모든 [Redis](/docs/{{version}}/redis) 명령어를 기록합니다. 캐시를 위해서 Redis 를 사용중이라면 캐시 명령어 또한 Redis 와처에 의해서 기록합니다.

<a name="request-watcher"></a>
### Request Watcher

요청-request 와처는 애플리케이션에서 처리하는 request, 헤더, 세션, 그리고 응답 데이터를 기록합니다. 또한 `size_limit` (킬로바이트 단위) 옵션을 통해 기록된 응답 데이터를 제한할 수 있습니다.

    'watchers' => [
        Watchers\RequestWatcher::class => [
            'enabled' => env('TELESCOPE_REQUEST_WATCHER', true),
            'size_limit' => env('TELESCOPE_RESPONSE_SIZE_LIMIT', 64),
        ],
        ...
    ],

<a name="schedule-watcher"></a>
### Schedule Watcher

스케줄러 와처는 애플리케이션에서 실행되는 모든 [scheduled tasks](/docs/{{version}}/scheduling)명령어와 그 결과를 기록합니다.

<a name="view-watcher"></a>
### View Watcher

뷰-view 와처는 뷰를 렌더링할 때 사용된 [뷰-view](/docs/{{version}}/views) 이름, 경로, 데이터 및 "composers"를 기록합니다.

<a name="displaying-user-avatars"></a>
## 사용자 아바타 표시

Telescope 대시보드에는 주어진 항목이 저장될 때 로그인 한 사용자의 사용자 아바타를 표시합니다. 기본적으로 Telescope는 Gravatar 웹 서비스를 사용하여 아바타를 검색합니다. 그러나 `App\Providers\TelescopeServiceProvider` 클래스에 콜백을 등록하여 아바타 URL을 맞춤 설정할 수 있습니다. 콜백은 사용자의 ID와 이메일 주소를 수신하고 사용자의 아바타 이미지 URL을 반환해야 합니다.

    use App\Models\User;
    use Laravel\Telescope\Telescope;

    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        // ...

        Telescope::avatar(function ($id, $email) {
            return '/avatars/'.User::find($id)->avatar_path;
        });
    }
