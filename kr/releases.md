# Release Notes
# 릴리즈 노트

- [Versioning Scheme](#versioning-scheme)
- [버전 관리 체계](#versioning-scheme)
- [Support Policy](#support-policy)
- [지원 정책](#support-policy)
- [Laravel 5.8](#laravel-5.8)
- [라라벨 5.8](#laravel-5.8)

<a name="versioning-scheme"></a>
## Versioning Scheme
## 버전 관리 체계

Laravel's versioning scheme maintains the following convention: `paradigm.major.minor`. Major framework releases are released every six months (February and August), while minor releases may be released as often as every week. Minor releases should **never** contain breaking changes.

라라벨의 버전 관리 체계는 다음의 컨벤션을 유지합니다: `paradigm.major.minor`. 메이저 프레임워크 릴리즈는 6 개월마다 (2월, 8월) 릴리즈되며, 마이너 릴리스는 매주 여러번 릴리즈 될 수 있습니다. 마이너 릴리즈에는 이전 버전의 호환성을 깨뜨리는 변경 사항이 **없어야** 합니다.

When referencing the Laravel framework or its components from your application or package, you should always use a version constraint such as `5.8.*`, since major releases of Laravel do include breaking changes. However, we strive to always ensure you may update to a new major release in one day or less.

애플리케이션에서 라라벨 프레임워크, 라라벨의 컴포넌트 또는 패키지를 참조할 때에, 라라벨의 메이저 릴리즈가 이전 버전과 호환성을 유지하지 못하는 변경사항을 포함하고 있기 때문에 항상 `5.8.*` 와 같이 참조하도록 해야 합니다. 변경사항에 대해서는 하루 안에 새로운 릴리즈를 업데이트 할 수 있도록 노력하고 있습니다.

Paradigm shifting releases are separated by many years and represent fundamental shifts in the framework's architecture and conventions. Currently, there is no paradigm shifting release under development.

패러다임 변경 릴리즈는 몇년간 구분되어 있으며, 프레임워크의 아키텍처와 컨벤션에 대한 근본적인 변경을 나타냅니다. 현재까지는 개발중인 버전에 대한 변경 발표는 없습니다.

<a name="support-policy"></a>
## Support Policy
## 지원 정책

For LTS releases, such as Laravel 5.5, bug fixes are provided for 2 years and security fixes are provided for 3 years. These releases provide the longest window of support and maintenance. For general releases, bug fixes are provided for 6 months and security fixes are provided for 1 year. For all additional libraries, including Lumen, only the latest release receives bug fixes.

라라벨 5.5과 같은 LTS 릴리즈 동안에는, 2년간의 버그 픽스와 3년동안의 보안 패치가 지원됩니다. 이러한 릴리즈는 장기간에 걸친 지원과 유지보수를 제공합니다. 일반적인 릴리즈에서는 버그 픽스는 6개월, 보안 패치는 1년동안 제공됩니다. Lumen을 포함한 모든 추가 라이브러리의 경우 최신 릴리스에서만 버그 수정을받습니다.

| Version | Release | Bug Fixes Until | Security Fixes Until |
| --- | --- | --- | --- |
| 5.0 | February 4th, 2015 | August 4th, 2015 | February 4th, 2016 |
| 5.1 (LTS) | June 9th, 2015 | June 9th, 2017 | June 9th, 2018 |
| 5.2 | December 21st, 2015 | June 21st, 2016 | December 21st, 2016 |
| 5.3 | August 23rd, 2016 | February 23rd, 2017 | August 23rd, 2017 |
| 5.4 | January 24th, 2017 | July 24th, 2017 | January 24th, 2018 |
| 5.5 (LTS) | August 30th, 2017 | August 30th, 2019 | August 30th, 2020 |
| 5.6 | February 7th, 2018 | August 7th, 2018 | February 7th, 2019 |
| 5.7 | September 4th, 2018 | March 4th, 2019 | September 4th, 2019 |
| 5.8 | February 26th, 2019 | August 26th, 2019 | February 26th, 2020 |

| 버전 | 릴리즈 | 버그픽스 지원기간| 보안 패치 지원기간|
| --- | --- | --- | --- |
| 5.0 | 2015년 2월 4일 | 2015년 8월 4일 | 2016년 2월 4일 |
| 5.1 (LTS) | 2015년 6월 9일 | 2017년 6월 9일 | 2018년 6월 9일 |
| 5.2 | 2015년 12월 21일 | 2016년 6월 21일 | 2016년 12월 21일 |
| 5.3 | 2016년 8월 23일 | 2017년 2월 23일 | 2017년 8월 23일 |
| 5.4 | 2017년 1월 24일 | 2017년 7월 24일 | 2018년 1월 24일 |
| 5.5 (LTS) | 2017년 8월 30일 | 2019년 8월 30일  | 2020년 8월 30일 |
| 5.6 | 2018년 2월 7일 | 2018년 8월 7일 | 2019년 2월 7일 |
| 5.7 | 2018년 9월 4일 | 2019년 3월 4일 | 2019년 9월 4일 |
| 5.8 | 2019년 2월 26일 | 2019년 8월 26일 | 2020년 2월 26일 |

<a name="laravel-5.8"></a>
## Laravel 5.8
## 라라벨 5.8

Laravel 5.8 continues the improvements made in Laravel 5.7 by introducing has-one-through Eloquent relationships, improved email validation, convention based automatic registration of authorization policies, DynamoDB cache and session drivers, improved scheduler timezone configuration, support for assigning multiple authentication guards to broadcast channels, PSR-16 cache driver compliance, improvements to the `artisan serve` command, PHPUnit 8.0 support, Carbon 2.0 support, Pheanstalk 4.0 support, and a variety of other bug fixes and usability improvements.

라라벨 5.8은 일대일 Eloquent 관계, 향상된 이메일 유효성 검증, 컨벤션 기반 권한 부여 정책 자동 등록, DynamoDB 캐시 및 세션 드라이버, 향상된 스케줄러 시간대 구성, 브로드캐스트에 여러 인증 가드 할당 지원, PSR-16 캐시 드라이버 준수, `artisan serve` 커맨드 개선, PHPUnit 8.0 지원, Carbon 2.0 지원, Pheanstalk 4.0 지원 및 다양한 버그 수정 및 유용성 개선 등을 통해 라라벨5.7의 향상된 기능을 지속적으로 제공합니다.

### Eloquent `HasOneThrough` Relationship
### Eloquent `HasOneThrough` 관계

Eloquent now provides support for the `hasOneThrough` relationship type. For example, imagine a Supplier model `hasOne` Account model, and an Account model has one AccountHistory model. You may use a `hasOneThrough` relationship to access a supplier's account history through the account model:

Eloquent는 이제 `hasOneThrough` 관계 타입을 지원합니다. 예를 들어 Supplier 모델의 `hasOne` 관계인 Account 모델을 상상해보십시오. 그리고 Account 모델에는 AccountHistory 모델이 하나 있습니다. 이때 Supplier는 Account 모델을 통한 `hasOneThrough` 관계를 사용하여 AccountHistory에 접근 할 수 있습니다.

    /**
     * Get the account history for the supplier.
     */
    public function accountHistory()
    {
        return $this->hasOneThrough(AccountHistory::class, Account::class);
    }

### Auto-Discovery Of Model Policies
### 모델 정책-Policies의 자동 발견

When using Laravel 5.7, each model's corresponding [authorization policy](/docs/{{version}}/authorization#creating-policies) needed to be explicitly registered in your application's `AuthServiceProvider`:

라라벨 5.7을 사용할 때는 각각 모델의 [승인 정책-policy](/docs/{{version}}/authorization#creating-policies)을 애플리케이션의 `AuthServiceProvider`에 명시적으로 등록해야했습니다.

    /**
     * The policy mappings for the application.
     *
     * @var array
     */
    protected $policies = [
        'App\User' => 'App\Policies\UserPolicy',
    ];

Laravel 5.8 introduces auto-discovery of model policies as long as the model and policy follow standard Laravel naming conventions. Specifically, the policies must be in a `Policies` directory below the directory that contains the models. So, for example, the models may be placed in the `app` directory while the policies may be placed in the `app/Policies` directory. In addition, the policy name must match the model name and have a `Policy` suffix. So, a `User` model would correspond to a `UserPolicy` class.

라라벨 5.8에서는 모델과 정책-policy이 라라벨의 표준 네이밍 컨벤션을 따르는 경우 모델 정책-policy를 자동 찾아주게 됩니다. 이 경우를 위해 정채-policy 클래스는 모델이 포함되어 있는 디렉토리 하위의 `Policies` 디렉토리 안에 있어야합니다. 예를 들어, 모델이 `app` 디렉토리에 존재한다면, 정책-policy는 `app/Policies` 디렉토리에 존재해야 합니다. 또한 정책 이름은 모델 이름과 일치해야하고 `Policy`라는 접미사가 있어야합니다. 즉 `User` 모델을 위한 `UserPolicy` 클래스가 되어야 합니다.

If you would like to provide your own policy discovery logic, you may register a custom callback using the `Gate::guessPolicyNamesUsing` method. Typically, this method should be called from your application's `AuthServiceProvider`:

여러분의 고유한 정책-policy 발견 로직을 만들고자 한다면 `Gate::guessPolicyNamesUsing` 메소드를 사용하여 커스텀 콜백을 등록 할 수 있습니다. 일반적으로 이 메소드는 애플리케이션의 `AuthServiceProvider`에서 호출해야합니다.

    use Illuminate\Support\Facades\Gate;

    Gate::guessPolicyNamesUsing(function ($modelClass) {
        // return policy class name...
    });

> {note} Any policies that are explicitly mapped in your `AuthServiceProvider` will take precedence over any potential auto-discovered policies.

> {note} `AuthServiceProvider`에 명시적으로 매핑 된 정책-policy는 자동으로 발견 된 모든 정책-policy보다 우선합니다.

### PSR-16 Cache Compliance
### PSR-16 캐시 규약 준수

In order to allow a more granular expiration time when storing items and provide compliance with the PSR-16 caching standard, the cache item time-to-live has changed from minutes to seconds. The `put`, `putMany`, `add`, `remember` and `setDefaultCacheTime` methods of the `Illuminate\Cache\Repository` class and its extended classes, as well as the `put` method of each cache store were updated with this changed behavior. See [the related PR](https://github.com/laravel/framework/pull/27276) for more info.

아이템을 저장할 때보다 보다 세밀한 캐시 만료 시간을 가능하게 하고, PSR-16 캐싱 표준을 준수하기 위해 캐시 아이템의 유효시간이 분단위 에서 초단위로 변경되었습니다. `Illuminate\Cache\Repository` 클래스와 확장 클래스의 `put`, `putMany`, `add`, `remember`, `setDefaultCacheTime` 메소드와 각 캐시 스토어의 `put` 메소드가 수정되고 동작이 변경되었습니다. 자세한 내용은 [관련 PR](https://github.com/laravel/framework/pull/27276)을 참조하십시오.

If you are passing an integer to any of these methods, you should update your code to ensure you are now passing the number of seconds you wish the item to remain in the cache. Alternatively, you may pass a `DateTime` instance indicating when the item should expire:

이 메소드들에게 정수값을 전달하는 경우, 캐시에 보관할 시간(초단위)을 전달하도록 코드를 업데이트해야합니다. 또는 캐시 아이템이 만료되어야하는 시점를 나타내는 `DateTime` 인스턴스를 전달할 수도 있습니다.

    // Laravel 5.7 - Store item for 30 minutes...
    Cache::put('foo', 'bar', 30);

    // Laravel 5.8 - Store item for 30 seconds...
    Cache::put('foo', 'bar', 30);

    // Laravel 5.7 / 5.8 - Store item for 30 seconds...
    Cache::put('foo', 'bar', now()->addSeconds(30));

### Multiple Broadcast Authentication Guards
### 다중 브로드 캐스트 인증 가드

In previous releases of Laravel, private and presence broadcast channels authenticated the user via your application's default authentication guard. Beginning in Laravel 5.8, you may now assign multiple guards that should authenticate the incoming request:

라라벨의 이전 릴리스에서 개인 및 현재 브로드캐스트 채널은 애플리케이션의 기본 인증 가드를 통해 사용자를 인증했습니다. 라라벨 5.8부터 유입되는 요청을 인증해야만 여러개의 가드를 지정할 수 있습니다:

    Broadcast::channel('channel', function() {
        // ...
    }, ['guards' => ['web', 'admin']])

### Token Guard Token Hashing
### 토큰 가드 토큰 해싱

Laravel's `token` guard, which provides basic API authentication, now supports storing API tokens as SHA-256 hashes. This provides improved security over storing plain-text tokens. To learn more about hashed tokens, please review the full [API authentication documentation](/docs/{{version}}/api-authentication).

기본적인 API 인증을 제공하는 라라벨의 `token` 가드는 이제 SHA-256 해시처럼 API 토큰을 저장하는 것을 지원합니다. 이는 일반 텍스트 토큰을 저장하는 것보다 향상된 보안을 제공합니다. 해시 토큰에 대한 자세한 내용은 전체 [API 인증 문서](/docs/{{version}}/api-authentication)을 참조하십시오.

> **Note:** While Laravel ships with a simple, token based authentication guard, we strongly recommend you consider using [Laravel Passport](/docs/{{version}}/passport) for robust, production applications that offer API authentication.

> **참고:** 라라벨은 간단한 토큰 기반 인증 위젯과 함께 제공되지만 API 인증을 제공하는 강력한 프로덕션 애플리케이션의 경우 [Laravel Passport](/docs/{{version}}/passport)를 사용하는 것이 좋습니다 .

### Improved Email Validation
### 향상된 이메일 검증

Laravel 5.8 introduces improvements to the validator's underlying email validation logic by adopting the `egulias/email-validator` package utilized by SwiftMailer. Laravel's previous email validation logic occasionally considered valid emails, such as `example@bär.se`, to be invalid.

라라벨 5.8은 SwiftMailer가 사용하는 `egulias/email-validator` 패키지를 적용하여 유효성 검사기의 기본 이메일 유효성 검사 로직를 개선했습니다. 라라벨의 이전 이메일 검증 로직에서는 이따금씩 `example@bär.se`와 같은 유효한 이메일을 유효하지 않은 것으로 판별하였습니다.

### Default Scheduler Timezone
### 스케줄러 기본 타임존-Timezone

Laravel allows you to customize the timezone of a scheduled task using the `timezone` method:

라라벨을 사용하면 `timezone` 메소드를 사용하여 예약 된 작업의 시간대를 커스터마이징 할 수 있습니다:

    $schedule->command('inspire')
             ->hourly()
             ->timezone('America/Chicago');

However, this can become cumbersome and repetitive if you are specifying the same timezone for all of your scheduled tasks. For that reason, you may now define a `scheduleTimezone` method in your `app/Console/Kernel.php` file. This method should return the default timezone that should be assigned to all scheduled tasks:

그러나 예약 된 모든 작업에 동일한 시간대를 지정 할 경우 이 작업이 번거로운 반복적 작업이 될 수 있습니다. 이럴 떈 `app/Console/Kernel.php` 파일에 `scheduleTimezone` 메소드를 정의 하면 됩니다. 이 메소드는 모든 예약 된 작업에 할당될 기본 표준 시간대를 반환해야합니다.

    /**
     * Get the timezone that should be used by default for scheduled events.
     *
     * @return \DateTimeZone|string|null
     */
    protected function scheduleTimezone()
    {
        return 'America/Chicago';
    }

### Intermediate Table / Pivot Model Events
### 중간 테이블 / 피벗 모델 이벤트

In previous versions of Laravel, [Eloquent model events](/docs/{{version}}/eloquent#events) were not dispatched when attaching, detaching, or syncing custom intermediate table / "pivot" models of a many-to-many relationship. When using [custom intermediate table models](/docs/{{version}}/eloquent-relationships#defining-custom-intermediate-table-models) in Laravel 5.8, the applicable model events will now be dispatched.

이전 버전의 라라벨에서는 다 대다(*:*) 관계의 커스텀 중간 테이블 / "피벗"모델을 연결, 분리 또는 동기화 할 때 [Eloquent 모델 이벤트](/docs/{{version}}/eloquent#events)가 전달되지 않았습니다. 라라벨 5.8에서 [custom intermediate table models](/docs/{{version}}/eloquent-relationships#define-custom-intermediate-table-models)를 사용하면 애플리케이션 모델 이벤트가 전달됩니다.

### Artisan Call Improvements
### 아티즌 요청 개선

Laravel allows you to invoke Artisan via the `Artisan::call` method. In previous releases of Laravel, the command's options are passed via an array as the second argument to the method:

라라벨은 `Artisan::call` 메소드를 통해 Artisan을 호출 할 수있게 해줍니다. 라라벨의 이전 릴리스에서는 명령의 옵션이 배열의 두 번째 인수로 전달되었습니다.

    use Illuminate\Support\Facades\Artisan;

    Artisan::call('migrate:install', ['database' => 'foo']);

However, Laravel 5.8 allows you to pass the entire command, including options, as the first string argument to the method:

그러나 라라벨 5.8을 사용하면 옵션을 포함한 전체 명령을 메서드의 첫 번째 문자열 인수로 전달할 수 있습니다.

    Artisan::call('migrate:install --database=foo');

### Mock / Spy Testing Helper Methods
### 목-Mock / 스파이 테스트 헬퍼 메소드

In order to make mocking objects more convenient, new `mock` and `spy` methods have been added to the base Laravel test case class. These methods automatically bind the mocked class into the container. For example:

모킹(mocking)하는 객체를 더욱 편리하게 만들기 위해 새로운 `mock` 과 `spy`메소드가 기본 라라벨의 테스트 케이스 클래스에 추가되었습니다. 이러한 메소드는 자동으로 모킹 된 클래스를 컨테이너에 바인딩합니다. 예 :

    // Laravel 5.7
    $this->instance(Service::class, Mockery::mock(Service::class, function ($mock) {
        $mock->shouldReceive('process')->once();
    }));

    // Laravel 5.8
    $this->mock(Service::class, function ($mock) {
        $mock->shouldReceive('process')->once();
    });

### Eloquent Resource Key Preservation
### Eloquent Resource Key 보존

When returning an [Eloquent resource collection](/docs/{{version}}/eloquent-resources) from a route, Laravel resets the collection's keys so that they are in simple numerical order:

라우트에서 [Eloquent resource collection](/docs/{{version}}/eloquent-resources)을 반환할 때, 라라벨은 컬렉션의 키를 숫자의 순서 형태로 재지정합니다.

    use App\User;
    use App\Http\Resources\User as UserResource;

    Route::get('/user', function () {
        return UserResource::collection(User::all());
    });

When using Laravel 5.8, you may now add a `preserveKeys` property to your resource class indicating if collection keys should be preserved. By default, and to maintain consistency with previous Laravel releases, the keys will be reset by default:

라라벨 5.8을 사용할 경우, 리소스 클래스에 `preserveKeys` 속성을 추가하여 콜렉션 키를 보존해야하는지 여부를 나타낼 수 있습니다. 이전 라라벨 릴리스와의 일관성을 유지하기 위해 키는 기본적으로 재설정해줘야 합니다:

    <?php

    namespace App\Http\Resources;

    use Illuminate\Http\Resources\Json\JsonResource;

    class User extends JsonResource
    {
        /**
         * Indicates if the resource's collection keys should be preserved.
         *
         * @var bool
         */
        public $preserveKeys = true;
    }

When the `preserveKeys` property is set to `true`, collection keys will be preserved:

`preserveKeys` 속성이 `true` 로 지정되면, 컬렉션의 키가 유지됩니다:

    use App\User;
    use App\Http\Resources\User as UserResource;

    Route::get('/user', function () {
        return UserResource::collection(User::all()->keyBy->id);
    });

### Higher Order `orWhere` Eloquent Method
### `orWhere` Eloquent의 고차(Higher Order) 메서드 

In previous releases of Laravel, combining multiple Eloquent model scopes via an `or` query operator required the use of Closure callbacks:

라라벨의 이전 릴리스에서는 `or` 쿼리 연산자를 통해 여러 가지 Eloquent 모델 범위를 결합하려면 Closure 콜백을 사용해야했습니다:

    // scopePopular and scopeActive methods defined on the User model...
    $users = App\User::popular()->orWhere(function (Builder $query) {
        $query->active();
    })->get();

Laravel 5.8 introduces a "higher order" `orWhere` method that allows you to fluently chain these scopes together without the use of Closures:

라라벨 5.8에서는 클로저를 사용하지 않고 이러한 범위를 유연하게 연결할 수있는 "higher order-고차" `orWhere` 메서드를 도입했습니다:

    $users = App\User::popular()->orWhere->active()->get();

### Artisan Serve Improvements
### 향상된 Artisan의 Serve

In previous releases of Laravel, Artisan's `serve` command would serve your application on port `8000`. If another `serve` command process was already listening on this port, an attempt to serve a second application via `serve` would fail. Beginning in Laravel 5.8, `serve` will now scan for available ports up to port `8009`, allowing you to serve multiple applications at once.

라라벨 이전 릴리스에서는 Artisan의 `serve` 명령이 `8000`번 포트에서 애플리케이션의 실행했습니다. 이때 만약 다른 `serve` 명령 프로세스가 이 포트에서 이미 실행 중이면 `serve`를 통해 두 번째 애플리케이션을 실행하려는 시도가 실패하였니다. 라라벨 5.8부터 `serve`는 이제 사용 가능한 포트를 `8009`번 포트까지 스캔하며, 한 번에 여러 애플리케이션을 실행 할 수 있습니다.

### Blade File Mapping
### Blade 파일 매핑

When compiling Blade templates, Laravel now adds a comment to the top of the compiled file which contains the path to the original Blade template.

Blade 템플릿을 컴파일 할 때, 라라벨은 컴파일 된 파일의 맨 위에 원래 Blade 템플릿에 대한 경로가 포함 된 주석을 추가합니다.

### DynamoDB Cache / Session Drivers
### DynamoDB 캐시 / 세션 드라이버

Laravel 5.8 introduces [DynamoDB](https://aws.amazon.com/dynamodb/) cache and session drivers. DynamoDB is a serverless NoSQL database provided by Amazon Web Services. The default configuration for the `dynamodb` cache driver can be found in the Laravel 5.8 [cache configuration file](https://github.com/laravel/laravel/blob/master/config/cache.php).

라라벨 5.8에서는 [DynamoDB](https://aws.amazon.com/dynamodb/) 캐시 및 세션 드라이버를 제공합니다. DynamoDB는 Amazon Web Services에서 제공하는 서버가 없는 NoSQL 데이터베이스입니다. `dynamodb` 캐시 드라이버의 기본 설정은 라라벨 5.8 [캐시 설정 파일](https://github.com/laravel/laravel/blob/master/config/cache.php)에서 확인 할 수 있습니다.

### Carbon 2.0 Support
### Carbon 2.0 지원

Laravel 5.8 provides support for the `~2.0` release of the Carbon date manipulation library.
 
라라벨 5.8은 Carbon 날짜 조작 라이브러리의 `~ 2.0` 릴리스에 대한 지원을 제공합니다.

### Pheanstalk 4.0 Support
### Pheanstalk 4.0 지원

Laravel 5.8 provides support for the `~4.0` release of the Pheanstalk queue library. If you are using Pheanstalk library in your application, please upgrade your library to the `~4.0` release via Composer.

라라벨 5.8은 Pheanstalk 큐 라이브러리의 `~4.0` 버전에 대해 지원합니다. 애플리케이션에 Pheanstalk 라이브러리를 사용하고 있다면, Composer를 통해 라이브러리를 `~4.0` 버전으로 업그레이드하십시오.
