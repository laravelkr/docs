# Release Notes
# 릴리즈 노트

- [Versioning Scheme](#versioning-scheme)
- [버전 관리 체계](#versioning-scheme)
- [Support Policy](#support-policy)
- [지원 정책](#support-policy)
- [Laravel 5.6](#laravel-5.6)
- [라라벨 5.6](#laravel-5.6)

<a name="versioning-scheme"></a>
## Versioning Scheme
## 버전 관리 체계

Laravel's versioning scheme maintains the following convention: `paradigm.major.minor`. Major framework releases are released every six months (February and August), while minor releases may be released as often as every week. Minor releases should **never** contain breaking changes.

라라벨의 버전 관리 체계는 다음의 컨벤션을 유지합니다: `paradigm.major.minor`. 메이저 프레임워크 릴리즈는 6 개월마다 (2월, 8월) 릴리즈되며, 마이너 릴리스는 매주 여러번 릴리즈 될 수 있습니다. 마이너 릴리즈에는 이전 버전의 호환성을 깨뜨리는 변경 사항이 **없어야** 합니다.

When referencing the Laravel framework or its components from your application or package, you should always use a version constraint such as `5.5.*`, since major releases of Laravel do include breaking changes. However, we strive to always ensure you may update to a new major release in one day or less.

어플리케이션에서 라라벨 프레임워크, 라라벨의 컴포넌트 또는 패키지를 참조할 때에, 라라벨의 메이저 릴리즈가 이전 버전과 호환성을 유지하지 못하는 변경사항을 포함하고 있기 때문에 항상 `5.5.*` 와 같이 참조하도록 해야 합니다. 변경사항에 대해서는 하루 안에 새로운 릴리즈를 업데이트 할 수 있도록 노력하고 있습니다.

Paradigm shifting releases are separated by many years and represent fundamental shifts in the framework's architecture and conventions. Currently, there is no paradigm shifting release under development.

패러다임 변경 릴리즈는 몇년간 구분되어 있으며, 프레임워크의 아키텍처와 컨벤션에 대한 근본적인 변경을 나타냅니다. 현재까지는 개발중인 버전에 대한 변경 발표는 없습니다.

<a name="support-policy"></a>
## Support Policy
## 지원 정책

For LTS releases, such as Laravel 5.5, bug fixes are provided for 2 years and security fixes are provided for 3 years. These releases provide the longest window of support and maintenance. For general releases, bug fixes are provided for 6 months and security fixes are provided for 1 year.

라라벨 5.5과 같은 LTS 릴리즈 동안에는, 2년간의 버그 픽스와 3년동안의 보안 패치가 지원됩니다. 이러한 릴리즈는 장기간에 걸친 지원과 유지보수를 제공합니다. 일반적인 릴리즈에서는 버그 픽스는 6개월, 보안 패치는 1년동안 제공됩니다.

| Version | Release | Bug Fixes Until | Security Fixes Until |
| --- | --- | --- | --- |
| 5.0 | February 4th, 2015 | August 4th, 2015 | February 4th, 2016 |
| 5.1 (LTS) | June 9th, 2015 | June 9th, 2017 | June 9th, 2018 |
| 5.2 | December 21st, 2015 | June 21st, 2016 | December 21st, 2016 |
| 5.3 | August 23rd, 2016 | February 23rd, 2017 | August 23rd, 2017 |
| 5.4 | January 24th, 2017 | July 24th, 2017 | January 24th, 2018 |
| 5.5 (LTS) | August 30th, 2017 | August 30th, 2019 | August 30th, 2020 |
| 5.6 | February 7th, 2018 | August 7th, 2018 | February 7th, 2019 |

| 버전 | 릴리즈 | 버그픽스 지원기간| 보안 패치 지원기간|
| --- | --- | --- | --- |
| 5.0 | 2015 년 2 월 4 일 | 2015 년 8 월 4 일 | 2016 년 2 월 4 일 |
| 5.1 (LTS) | 2015 년 6 월 9 일 | 2017 년 6 월 9 일 | 2018 년 6 월 9 일 |
| 5.2 | 2015 년 12 월 21 일 | 2016 년 6 월 21 일 | 2016 년 12 월 21 일 |
| 5.3 | 2016 년 8 월 23 일 | 2017 년 2 월 23 일 | 2017 년 8 월 23 일 |
| 5.4 | 2017 년 1 월 24 일 | 2017 년 7 월 24 일 | 2018 년 1 월 24 일 |
| 5.5 (LTS) | 2017 년 8 월 30 일 | 2019 년 8 월 30 일  | 2020 년 8 월 30 일 |
| 5.6 | 2018 년 2 월 7 일 | 2018 년 8 월 7 일 | 2019 년 2 월 7 일 |

<a name="laravel-5.6"></a>
## Laravel 5.6
## 라라벨 5.6

Laravel 5.6 continues the improvements made in Laravel 5.5 by adding an improved logging system, single-server task scheduling, improvements to model serialization, dynamic rate limiting, broadcast channel classes, API resource controller generation, Eloquent date formatting improvements, Blade component aliases, Argon2 password hashing support, inclusion of the Collision package, and more. In addition, all front-end scaffolding has been upgraded to Bootstrap 4.

라라벨 5.6은 로깅 시스템의 향상, 단일-서버 작업 스케줄링, 모델의 serialization 기능 향상, 동적 속도 제한 기능, 브로드캐스트 채널 클래스, API 리소스 컨트롤러 생성, Eloquent 날짜 포맷의 기능 향상, 블레이드 컴포넌트의 별칭 기능, Argon2 암호화 지원, Collision 패키지 포함등 라라벨 5.5에 비해서 기능이 향상되었습니다. 또한 모든 프론트엔드 스캐폴딩은 부트스트랩4 기반으로 업그레이드 되었습니다.

All underlying Symfony components used by Laravel have been upgraded to the Symfony `~4.0` release series.

라라벨에서 사용하는 Symfony 컴포넌트가 Symfony `~4.0` 릴리즈 시리즈로 업그레이드 되었습니다.

The release of Laravel 5.6 coincides with the release of [Spark 6.0](https://spark.laravel.com), the first major upgrade to Laravel Spark since its release. Spark 6.0 introduces per-seat pricing for Stripe and Braintree, localization, Bootstrap 4, an enhanced UI, and Stripe Elements support.

라라벨 5.6 버전의 릴리즈는 [Spark 6.0](https://spark.laravel.com)(릴리즈 이후 첫번째 메이저 업그레이드인)과 동시에 진행되었습니다. Spark 6.0 은 Stripe 와 Braintree 에 대한 가격 정책, 다국어, 부트스트랩 4 와 더 향상된 UI 그리고 Stripe의 개별 요소를 더 잘 지원합니다.

> {tip} This documentation summarizes the most notable improvements to the framework; however, more thorough change logs are always available [on GitHub](https://github.com/laravel/framework/blob/5.6/CHANGELOG-5.6.md).

> {tip} 이 문서는 프레임워크에서 주요한 개선사항들에 대한 요약입니다. 보다 자세한 변경사항은 [GitHub](https://github.com/laravel/framework/blob/5.6/CHANGELOG-5.6.md)에서 확인할 수 있습니다.

### Logging Improvements
### 로깅 시스템의 향상

Laravel 5.6 brings vast improvements to Laravel's logging system. All logging configuration is housed in the new `config/logging.php` configuration file. You may now easily build logging "stacks" that send log messages to multiple handlers. For example, you may send all `debug` level messages to the system log while sending `error` level messages to Slack so that your team can quickly react to errors:

라라벨 5.6 에서 로깅 시스템에 많은 기능향상이 있었습니다. 모든 로깅 설정은 새로운 `config/logging.php` 파일로 지정됩니다. 로그 메세지를 보내는 여러개의 핸들러를 구성하고자 할때 "stacks" 을 사용하면 됩니다. 예를 들어, 모든 `debug` 레벨의 메세지를 시스템 로그로 보내면서, `error` 레벨 메세지를 슬랙으로 보낼 수 있습니다. 이렇게 하면 여러분의 팀에서 에러를 보다 빠르고 즉각적으로 대응할 수 있습니다:

    'channels' => [
        'stack' => [
            'driver' => 'stack',
            'channels' => ['syslog', 'slack'],
        ],
    ],

In addition, it is now easier to customize existing log channels using the logging system's new "tap" functionality. For more information, check out the [full documentation on logging](/docs/{{version}}/logging).

또한 기존의 로그 채널을 새로운 "tap" 기능을 사용하여 손쉽게 커스터마이징 할 수 있습니다. 보다 자세한 내용은 [로깅 문서](/docs/{{version}}/logging)를 참고하시기 바랍니다.

### Single Server Task Scheduling
### 단일 서버에서의 작업 스케줄링

> {note} To utilize this feature, your application must be using the `memcached` or `redis` cache driver as your application's default cache driver. In addition, all servers must be communicating with the same central cache server.

> {note} 이 기능을 사용하기 위해서는, 어플리케이션이 `memcached` 또는 `redis` 캐시 서버를 기본 캐시 서버로 사용하고 있어야 합니다. 또한, 모든 서버는 동일한 중앙 캐시 서버와 통신하고 있어야 합니다.

If your application is running on multiple servers, you may now limit a scheduled job to only execute on a single server. For instance, assume you have a scheduled task that generates a new report every Friday night. If the task scheduler is running on three worker servers, the scheduled task will run on all three servers and generate the report three times. Not good!

어플리케이션이 여러개의 서버에서 동작중이라면, 특정 job을 하나의 단일 서버에서만 실행할 필요가 있을 수 있습니다. 예를 들어 매주 금요일 저녁 새로운 보고서를 생성하는 작업스케줄을 가지고 있다고 가정해 보겠습니다. 만약 세개의 서버가 있고, 스케줄 작업이 서버 모두에서 동작한다면, job은 세번 실행되고, 보고서는 세번 생성되버립니다. 이런건 좋지 않죠!

To indicate that the task should run on only one server, you may use the `onOneServer` method when defining the scheduled task. The first server to obtain the task will secure an atomic lock on the job to prevent other servers from running the same task on the same Cron cycle:

작업이 하나의 서버에서만 실행되도록 하려면, 예약된 작업을 정의할 때 `onOneServer` 메소드를 사용하면 됩니다. 작업을 수행하는 첫번째 서버는 다른 서버가 동일한 크론 사이클 동안 지정된 작업을 동시에 실행하지 못하도록 lock을 통해서 보호하게 됩니다:

    $schedule->command('report:generate')
             ->fridays()
             ->at('17:00')
             ->onOneServer();

### Dynamic Rate Limiting
### 동적 Rate 제한

When specifying a [rate limit](/docs/{{version}}/routing#rate-limiting) on a group of routes in previous releases of Laravel, you were forced to provide a hard-coded number of maximum requests:

이전 버전의 라라벨에서 라우트 그룹에 [접속 속도 제한](/docs/{{version}}/routing#rate-limiting)을 지정할 때에는, 직접 최대 request-요청의 갯수를 지정했어야합니다:

    Route::middleware('auth:api', 'throttle:60,1')->group(function () {
        Route::get('/user', function () {
            //
        });
    });

In Laravel 5.6, you may specify a dynamic request maximum based on an attribute of the authenticated `User` model. For example, if your `User` model contains a `rate_limit` attribute, you may pass the name of the attribute to the `throttle` middleware so that it is used to calculate the maximum request count:

라라벨 5.6에서는 인증된 `User` 모델의 속성을 사용하여, 동적으로 request-요청의 최대값을 지정할 수 있습니다. 예를 들어, `User` 모델이 `rate_limit` 속성을 가지고 있다면, 이 속성의 이름을 `throttle` 미들웨어에 전달해서, 이 값을 가지고 최대 request-요청 횟수를 제한하는데 사용하도록 지정할 수 있습니다:

    Route::middleware('auth:api', 'throttle:rate_limit,1')->group(function () {
        Route::get('/user', function () {
            //
        });
    });

### Broadcast Channel Classes
### 브로드 캐스트 채널 클래스

If your application is consuming many different channels, your `routes/channels.php` file could become bulky. So, instead of using Closures to authorize channels, you may now use channel classes. To generate a channel class, use the `make:channel` Artisan command. This command will place a new channel class in the `App/Broadcasting` directory.

어플리케이션에서 여러개의 다른 채널을 사용하고 있다면, `routes/channels.php` 파일이 매우 커져버릴 수 있습니다. 이때, 채널의 권한을 확인하기 위해서 클로저를 사용하는 대신, 채널 클래스를 사용할 수 있습니다. 채널 클래스를 생성하려면 `make:channel` 아티즌 명령어를 사용하면 됩니다. 이 명령어는 `App/Broadcasting` 디렉토리에 새로운 채널 클래스를 생성합니다.

    php artisan make:channel OrderChannel

Next, register your channel in your `routes/channels.php` file:

다음으로, `routes/channels.php` 파일에 아래와 같이 새로운 채널 클래스를 등록합니다:

    use App\Broadcasting\OrderChannel;

    Broadcast::channel('order.{order}', OrderChannel::class);

Finally, you may place the authorization logic for your channel in the channel class' `join` method. This `join` method will house the same logic you would have typically placed in your channel authorization Closure. Of course, you may also take advantage of channel model binding:

그리고나서, 생성한 채널 클래스의 `join` 메소드안에 권한을 확인하는 로직을 구성하면 됩니다. 이 `join` 메서드는 일반적으로 채널 승인 클로저에 작성하던 로직과 동일한 로직이 작성됩니다. 물론, 채널 모델 바인딩을 활용할 수도 있습니다:

    <?php

    namespace App\Broadcasting;

    use App\User;
    use App\Order;

    class OrderChannel
    {
        /**
         * Create a new channel instance.
         *
         * @return void
         */
        public function __construct()
        {
            //
        }

        /**
         * Authenticate the user's access to the channel.
         *
         * @param  \App\User  $user
         * @param  \App\Order  $order
         * @return array|bool
         */
        public function join(User $user, Order $order)
        {
            return $user->id === $order->user_id;
        }
    }

### API Controller Generation
### API Controller Generation

When declaring resource routes that will be consumed by APIs, you will commonly want to exclude routes that present HTML templates such as `create` and `edit`. To generate a resource controller that does not include these methods, you may now use the `--api` switch when executing the `make:controller` command:

리소스 라우트를 선언할 때 API로 구성하길 원한다면, 일반적으로 HTML 템플릿을 구성하는 `create` 와 `edit` 를 제외하고 생성하게됩니다. 리소스 컨트롤러를 생성할 때, 이 메소드를 포함시키지 않길 원한다면, `make:controller` 커맨드 명령에 `--api` 옵션을 사용하시면 됩니다:

    php artisan make:controller API/PhotoController --api

### Model Serialization Improvements
### 모델 Serialization 기능 향상

In previous releases of Laravel, queued models would not be restored with their loaded relationships intact. In Laravel 5.6, relationships that were loaded on the model when it was queued are automatically re-loaded when the job is processed by the queue.

이전 버전의 라라벨에서는 큐-queue에 모델이 전달될 때, 연결된 관계가 온전하게 복원되지는 않았습니다. 라라벨 5.6에서는 모델에서 로딩된 관계들까지 큐-queue로 전달되어 처리될 때 자동으로 다시 로딩됩니다.

### Eloquent Date Casting
### Eloquent 날짜 변환

You may now individually customize the format of Eloquent date cast columns. To get started, specify the desired date format within the cast declaration. Once specified, this format will be used when serializing the model to an array / JSON:

여러분은 이제 Eloquent의 날짜 컬럼을 각각 다른 포맷으로 커스터마이징 할 수 있습니다. 이를 위해서는 $case 속성에 각각의 날짜 포맷을 지정해야 합니다. 그러고 나면, 배열이나 JSON으로 시리얼라이즈 될때 지정한 포맷으로 변환됩니다:

    protected $casts = [
        'birthday' => 'date:Y-m-d',
        'joined_at' => 'datetime:Y-m-d H:00',
    ];

### Blade Component Aliases
### 블레이드 컴포넌트 별칭 지정

If your Blade components are stored in a sub-directory, you may now alias them for easier access. For example, imagine a Blade component that is stored at `resources/views/components/alert.blade.php`. You may use the `component` method to alias the component from `components.alert` to `alert`:

블레이드 컴포넌트를 하위 디렉토리에 저장했다면, 이제 이들에 별칭을 지정하여 엑세스 할 수 있습니다. 예를 들어, `resources/views/components/alert.blade.php`에 저장된 블레이드 컴포넌트를 생각해 보겠습니다. `component` 메소드를 사용하여 `components.alert`를 `alert`으로 별칭을 지정할 수 있습니다:

    Blade::component('components.alert', 'alert');

Once the component has been aliased, you may render it using a directive:

컴포넌트에 별칭을 부여하고 나면, 지시어를 사용하여 렌더링 할 수 있습니다:

    @alert('alert', ['type' => 'danger'])
        You are not allowed to access this resource!
    @endalert

You may omit the component parameters if it has no additional slots:

추가 슬롯이 없다면 컴포넌트의 파라미터를 생략할 수 있습니다:

    @alert
        You are not allowed to access this resource!
    @endalert

### Argon2 Password Hashing
### Argon2 패스워드 해싱 지원

If you are building an application on PHP 7.2.0 or greater, Laravel now supports password hashing via the Argon2 algorithm. The default hash driver for your application is controlled by a new `config/hashing.php` configuration file.

어플리케이션을 PHP 7.2.0 이상 환경에서 구성중이라면, 라라벨은 이제 Argon2 알고리즘을 사용한 패스워드 해싱을 지원합니다. 기존 해시 드라이버는 새로운 `config/hashing.php` 설정 파일에서 제어할 수 있습니다.

### UUID Methods
### UUID Methods

Laravel 5.6 introduces two new methods for generating UUIDs: `Str::uuid` and `Str::orderedUuid`. The `orderedUuid` method will generate a timestamp first UUID that is more easily and efficiently indexed by databases such as MySQL. Each of these methods returns a `Ramsey\Uuid\Uuid` object:

라라벨 5.6에서는 UUID를 생성하는 두개의 새로운 메소드를 제공합니다: `Str::uuid`와 `Str::orderedUuid`입니다. `orderedUuid` 메소드는 Mysql과 같은 인덱싱된 데이터베이스 컬럼에 효과적으로 저장될 수 있도록 "타임스탬프와 같은 정렬이 가능한" UUID를 생성합니다. (역자주 : 이 메소드의 결과로 생성되는 UUID의 첫번째 부분은 시간에 따라서 증감하는 형태를 보입니다). 각각의 이 메소드들은 `Ramsey\Uuid\Uuid` 객체를 반환합니다:

    use Illuminate\Support\Str;

    return (string) Str::uuid();

    return (string) Str::orderedUuid();

### Collision
### Collision 패키지

The default `laravel/laravel` application now contains a `dev` Composer dependency for the [Collision](https://github.com/nunomaduro/collision) package maintained by Nuno Maduro. This packages provides beautiful error reporting when interacting with your Laravel application on the command line:

`laravel/laravel` 어플리케이션에서는 이제 `dev` 컴포저 의존성으로 Nuno Maduro가 관리하는 [Collision](https://github.com/nunomaduro/collision) 패키지가 추가되었습니다. 이 패키지는 커맨드라인에서 라라벨 어플리케이션을 작동할 때 유용한 에러 리포팅 기능을 제공합니다:

<img src="https://raw.githubusercontent.com/nunomaduro/collision/stable/docs/example.png" width="600" height="388">

### Bootstrap 4
### 부트스트랩 4

All front-end scaffolding such as the authentication boilerplate and example Vue component have been upgraded to [Bootstrap 4](https://blog.getbootstrap.com/2018/01/18/bootstrap-4/). By default, pagination link generation also now defaults to Bootstrap 4.

인증 보일러플레이크 코드 와 뷰 컴포넌트를 구성하는 예제와 같이 모든 프론트엔드 스캐폴딩은 [부트스트랩 4](https://blog.getbootstrap.com/2018/01/18/bootstrap-4/)로 업그레이드 되었습니다. 이에 따라 기본적으로, 페이지네이션의 링크는 부트스트랩4 형식으로 생성됩니다.
