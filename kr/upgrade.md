# Upgrade Guide
# 업그레이드 가이드
- [Upgrading To 6.0 From 5.8](#upgrade-6.0)
- [5.8에서 6.0으로 업그레이드](#upgrade-6.0)
<a name="high-impact-changes"></a>
## High Impact Changes
## 매우 중요한 변경사항

<!-- <div class="content-list" markdown="1"> -->
- [Authorized Resources & `viewAny`](#authorized-resources)
- [String & Array Helpers](#helpers)
- [리소스 인가와 `viewAny`](#authorized-resources)
- [문자열 & 배열 헬퍼](#helpers)
<!-- </div> -->

<!-- <a name="medium-impact-changes"></a> -->
## Medium Impact Changes
## 중요한 변경사항

<!-- <div class="content-list" markdown="1"> -->
- [Authentication `RegisterController`](#the-register-controller)
- [Carbon 1.x No Longer Supported](#carbon-support)
- [Database `Capsule::table` Method](#capsule-table)
- [Eloquent Arrayable & `toArray`](#eloquent-to-array)
- [Eloquent `BelongsTo::update` Method](#belongs-to-update)
- [Eloquent Primary Key Types](#eloquent-primary-key-type)
- [Localization `Lang::trans` and `Lang::transChoice` Methods](#trans-and-trans-choice)
- [Localization `Lang::getFromJson` Method](#get-from-json)
- [Queue Retry Limit](#queue-retry-limit)
- [Resend Email Verification Route](#email-verification-route)
- [The `Input` Facade](#the-input-facade)
- [인증 `RegisterController`](#the-register-controller)
- [Carbon 1.x 지원 종료](#carbon-support)
- [데이터베이스 `Capsule::table` 메소드](#capsule-table)
- [Eloquent Arrayable & `toArray`](#eloquent-to-array)
- [Eloquent `BelongsTo::update` Method](#belongs-to-update)
- [Eloquent Primary Key 타입](#eloquent-primary-key-type)
- [Localization `Lang::trans` 메소드와 `Lang::transChoice` 메소드](#trans-and-trans-choice)
- [Localization `Lang::getFromJson` 메소드](#get-from-json)
- [큐 재시도 횟수 제한](#queue-retry-limit)
- [이메일 인증 재발송 라우트](#email-verification-route)
- [`Input` 파사드](#the-input-facade)
<!-- </div> -->

<a name="upgrade-6.0"></a>
## Upgrading To 6.0 From 5.8
## 5.8에서 6.0으로 업그레이드 하기

#### Estimated Upgrade Time: One Hour
#### 예상 소요 시간: 1시간
> {note} We attempt to document every possible breaking change. Since some of these breaking changes are in obscure parts of the framework only a portion of these changes may actually affect your application.
> {note} 이전 버전과 호환되지 않는 모든 변경사항을 기록했습니다만 변경사항들 중 일부는 프레임워크의 모호한 부분에 있기 때문에 실제로는 여러분의 어플리케이션에 영향을 끼치지 않을 수도 있습니다.
### PHP 7.2 Required
### PHP 7.2 필요

**Likelihood Of Impact: Medium**
**영향 가능성: 중간**
PHP 7.1 will no longer be actively maintained as of December 2019. Therefore, Laravel 6.0 requires PHP 7.2 or greater.
PHP 7.1 버전은 2019년 12월 이후로 더 이상 활발하게 지원되지 않습니다. 그렇기 때문에 라라벨 6.0은 PHP 7.2버전 이상을 필요로 합니다. 

<a name="updating-dependencies"></a>
### Updating Dependencies
### 의존성 업데이트

Update your `laravel/framework` dependency to `^6.0` in your `composer.json` file.
`composer.json` 파일을 열어 `laravel/framework` 의존성을 `^6.0` 으로 업데이트 하세요.
Next, examine any 3rd party packages consumed by your application and verify you are using the proper version for Laravel 6 support.
그리고, 어플리케이션에서 사용 중인 모든 3rd party 패키지들을 살펴보고 라라벨 6 버전을 지원하는 버전을 사용중인지 확인하세요. 

### Authorization
### 인가

<a name="authorized-resources"></a>
#### Authorized Resources & `viewAny`
#### 인가된 리소스 & `viewAny`

**Likelihood Of Impact: High**
**영향 가능성: 높음**

Authorization policies attached to controllers using the `authorizeResource` method should now define a `viewAny` method, which will be called when a user accesses the controller's `index` method. Otherwise, calls to the `index` method of the controller will be rejected as unauthorized.
`authorizeResource` 함수를 사용해 컨트롤러에 붙여진 인가 정책은 이제 `viewAny` 메소드를 정의해야만 합니다. 이 메소드는 유저가 컨트롤러의 `index` 메소드에 접근할 때 호출됩니다. 이 메소드를 정의하지 않으면, 컨트롤러의 `index` 메소드에 대한 호출이 거부되거나 비인가 처리됩니다. 


<a name="the-register-controller"></a>
#### The `RegisterController` Controller
#### `RegisterController` 컨트롤러

**Likelihood Of Impact: Medium**
**영향 가능성: 중간**
If you are overriding the `register` or `registered` methods of Laravel's built-in `RegisterController`, you should ensure that you are calling `parent::register` and `parent::registered` from within your respective methods. The dispatching of the `Illuminate\Auth\Events\Registered` event and the logging in of the newly registered user has been moved to the `registered` method, so if you are overriding this method and not calling the parent method, the user registration process will fail.
만약 라라벨에 내장된 `RegisterController` 의 `register` 혹은 `registered` 메소드를 오버라이딩 해서 사용하고 있다면 상속받은 함수에서 `parent::register` 와 `parent::registered` 메소드를 호출하는지 확인해야 합니다. `Illuminate\Auth\Events\Registered` 이벤트를 발생시키는 부분과 새로 등록한 유저을 로그인 처리하는 부분을 `registered` 메소드로 옮겼기 때문에, 이 메소드를 오버라이딩 해서 사용할 때 부모 메소드를 호출해주지 않으면 유저 등록이 실패하게 됩니다.

#### Authorization Responses
#### Authorization Response 클래스

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**
The constructor signature of the `Illuminate\Auth\Access\Response` class has changed. You should update your code accordingly. If you are not constructing authorization responses manually and are only using the `allow` and `deny` instance methods within your policies, no change is required:
`Illuminate\Auth\Access\Response` 클래스 생성자의 인자 구성이 변경되었습니다. 그에 따라 코드를 알맞게 변경해야 합니다. 만약 인증에 대한 응답을 수동으로 생성하고 있지 않고, 정책들에서 `allow` 와 `deny` 메소드만 사용하고 있다면 변경할 필요 없습니다.

    /**
     * Create a new response.
     *
     * @param  bool  $allowed
     * @param  string  $message
     * @param  mixed  $code
     * @return void
     */
    public function __construct($allowed, $message = '', $code = null)


<a name="auth-access-gate-contract"></a>
#### The `Illuminate\Contracts\Auth\Access\Gate` Contract
#### `Illuminate\Contracts\Auth\Access\Gate` 인터페이스
**Likelihood Of Impact: Low**
**영향 가능성: 낮음**
The `Illuminate\Contracts\Auth\Access\Gate` contract has received a new `inspect` method. If you are implementing this interface manually, you should add this method to your implementation.
`Illuminate\Contracts\Auth\Access\Gate` 인터페이스에 `inspect` 라는 새로운 메소드가 추가되었습니다. 만약 이 인터페이스를 직접 구현하고 있다면 구현체에 이 메소드를 추가해야 합니다.

### Carbon
### Carbon

<a name="carbon-support"></a>
#### Carbon 1.x No Longer Supported
#### Carbon 1.x 버전은 더 이상 지원되지 않습니다.

**Likelihood Of Impact: Medium**
**영향 가능성: 중간**

Carbon 1.x [is no longer supported](https://github.com/laravel/framework/pull/28683) since it is nearing its maintenance end of life. Please upgrade your application to Carbon 2.0.
Carbon 1.x 버전의 지원 기간이 종료되었기 때문에 [더 이상 지원되지 않습니다](https://github.com/laravel/framework/pull/28683). 어플리케이션을 Carbon 2.0 버전을 사용하도록 업그레이드 하세요.

### Configuration

#### The `AWS_REGION` Environment Variable

**Likelihood Of Impact: Optional**

If you plan to utilize [Laravel Vapor](https://vapor.laravel.com), you should update all occurrences of `AWS_REGION` within your `config` directory to `AWS_DEFAULT_REGION`. In addition, you should update this environment variable's name in your `.env` file.

### Database

<a name="capsule-table"></a>
#### The Capsule `table` Method

**Likelihood Of Impact: Medium**

> {note} This change only applies to non-Laravel applications that are using `illuminate/database` as a dependency.

The signature of the `Illuminate\Database\Capsule\Manager` class' `table` method has 
updated to accept a table alias as its second argument. If you are using `illuminate/database` outside of a Laravel application, you should update any calls to this method accordingly:

    /**
     * Get a fluent query builder instance.
     *
     * @param  \Closure|\Illuminate\Database\Query\Builder|string  $table
     * @param  string|null  $as
     * @param  string|null  $connection
     * @return \Illuminate\Database\Query\Builder
     */
    public static function table($table, $as = null, $connection = null)

#### The `cursor` Method

**Likelihood Of Impact: Low**

The `cursor` method now returns an instance of `Illuminate\Support\LazyCollection` instead of a `Generator` The `LazyCollection` may be iterated just like a generator:

    $users = App\User::cursor();

    foreach ($users as $user) {
        //
    }

<a name="eloquent"></a>
### Eloquent

<a name="belongs-to-update"></a>
#### The `BelongsTo::update` Method

**Likelihood Of Impact: Medium**

For consistency, the `update` method of the `BelongsTo` relationship now functions as an ad-hoc update query, meaning it does not provide mass assignment protection or fire Eloquent events. This makes the relationship consistent with the `update` methods on all other types of relationships.

If you would like to update a model attached via a `BelongsTo` relationship and receive mass assignment update protection and events, you should call the `update` method on the model itself:

    // Ad-hoc query... no mass assignment protection or events...
    $post->user()->update(['foo' => 'bar']);

    // Model update... provides mass assignment protection and events...
    $post->user->update(['foo' => 'bar']);

<a name="eloquent-to-array"></a>
#### Arrayable & `toArray`

**Likelihood Of Impact: Medium**

The Eloquent model's `toArray` method will now cast any attributes that implement `Illuminate\Contracts\Support\Arrayable` to an array.

<a name="eloquent-primary-key-type"></a>
#### Declaration Of Primary Key Type

**Likelihood Of Impact: Medium**

Laravel 6.0 has received [performance optimizations](https://github.com/laravel/framework/pull/28153) for integer key types. If you are using a string as your model's primary key, you should declare the key type using the `$keyType` property on your model:

    /**
     * The "type" of the primary key ID.
     *
     * @var string
     */
    protected $keyType = 'string';

### Email Verification

<a name="email-verification-route"></a>
#### Resend Verification Route HTTP Method

**Likelihood Of Impact: Medium**

To prevent possible CSRF attacks, the `email/resend` route registered by the router when using Laravel's built-in email verification has been updated from a `GET` route to a `POST` route. Therefore, you will need to update your frontend to send the proper request type to this route. For example, if you are using the built-in email verification template scaffolding:

    {{ __('Before proceeding, please check your email for a verification link.') }}
    {{ __('If you did not receive the email') }},

    <form class="d-inline" method="POST" action="{{ route('verification.resend') }}">
        @csrf

        <button type="submit" class="btn btn-link p-0 m-0 align-baseline">
            {{ __('click here to request another') }}
        </button>.
    </form>

<a name="mustverifyemail-contract"></a>
#### The `MustVerifyEmail` Contract

**Likelihood Of Impact: Low**

A new `getEmailForVerification` method has been added to the `Illuminate\Contracts\Auth\MustVerifyEmail` contract. If you are manually implementing this contract, you should implement this method. This method should return the object's associated email address. If your `App\User` model is using the `Illuminate\Auth\MustVerifyEmail` trait, no changes are required, as this trait implements this method for you.

<a name="helpers"></a>
### Helpers

#### String & Array Helpers Package

**Likelihood Of Impact: High**

All `str_` and `array_` helpers have been moved to the new `laravel/helpers` Composer package and removed from the framework. If desired, you may update all calls to these helpers to use the `Illuminate\Support\Str` and `Illuminate\Support\Arr` classes. Alternatively, you can add the new `laravel/helpers` package to your application to continue using these helpers:

    composer require laravel/helpers

### Localization

<a name="trans-and-trans-choice"></a>
#### The `Lang::trans` & `Lang::transChoice` Methods

**Likelihood Of Impact: Medium**

The `Lang::trans` and `Lang::transChoice` methods of the translator have been renamed to `Lang::get` and `Lang::choice`.

In addition, if you are manually implementing the `Illuminate\Contracts\Translation\Translator` contract, you should update your implementation's `trans` and `transChoice` methods to `get` and `choice`.

<a name="get-from-json"></a>
#### The `Lang::getFromJson` Method

**Likelihood Of Impact: Medium**

The `Lang::get` and `Lang::getFromJson` methods have been consolidated. Calls to the `Lang::getFromJson` method should be updated to call `Lang::get`.

### Mail

#### Mandrill & SparkPost Drivers Removed

**Likelihood Of Impact: Low**

The `mandrill` and `sparkpost` mail drivers have been removed. If you would like to continue using either of these drivers, we encourage you to adopt a community maintained package of your choice that provides the driver.

### Notifications

#### Nexmo Routing Removed

**Likelihood Of Impact: Low**

A lingering part of the Nexmo notification channel was removed from the core of the framework. If you're relying on routing Nexmo notifications you should manually implement the `routeNotificationForNexmo` method on your notifiable entity [as described in the documentation](/docs/{{version}}/notifications#routing-sms-notifications).

### Password Reset

#### Password Validation

**Likelihood Of Impact: Low**

The `PasswordBroker` no longer restricts or validates passwords. Password validation was already being handled by the `ResetPasswordController` class, making the broker's validations redundant and impossible to customize. If you are manually using the `PasswordBroker` (or `Password` facade) outside of the built-in `ResetPasswordController`, you should validate all passwords before passing them to the broker.

### Queues

<a name="queue-retry-limit"></a>
#### Queue Retry Limit

**Likelihood Of Impact: Medium**

In previous releases of Laravel, the `php artisan queue:work` command would retry jobs indefinitely. Beginning with Laravel 6.0, this command will now try a job one time by default. If you would like to force jobs to be tried indefinitely, you may pass `0` to the `--tries` option:

    php artisan queue:work --tries=0

In addition, please ensure your application's database contains a `failed_jobs` table. You can generate a migration for this table using the `queue:failed-table` Artisan command:

    php artisan queue:failed-table

### Requests

<a name="the-input-facade"></a>
#### The `Input` Facade

**Likelihood Of Impact: Medium**

The `Input` facade, which was primarily a duplicate of the `Request` facade, has been removed. If you are using the `Input::get` method, you should now call the `Request::input` method. All other calls to the `Input` facade may simply be updated to use the `Request` facade.

### Scheduling

#### The `between` Method

**Likelihood Of Impact: Low**

In previous releases of Laravel, the scheduler's `between` method exhibited confusing behavior across date boundaries. For example:

    $schedule->command('list')->between('23:00', '4:00');

For most users, the expected behavior of this method would be to run the `list` command every minute for all minutes between 23:00 and 4:00. However, in previous releases of Laravel, the scheduler ran the `list` command every minute between 4:00 and 23:00, essentially swapping the time thresholds. In Laravel 6.0, this behavior has been corrected.

### Storage

<a name="rackspace-storage-driver"></a>
#### Rackspace Storage Driver Removed

**Likelihood Of Impact: Low**

The `rackspace` storage driver has been removed. If you would like to continue using Rackspace as a storage provider, we encourage you to adopt a community maintained package of your choice that provides this driver.

### URL Generation

#### Route URL Generation & Extra Parameters

In previous releases of Laravel, passing associative array parameters to the `route` helper or `URL::route` method would occasionally use these parameters as URI values when generating URLs for routes with optional parameters, even if the parameter value had no matching key within the route path. Beginning in Laravel 6.0, these values will be attached to the query string instead. For example, consider the following route:

    Route::get('/profile/{location?}', function ($location = null) {
        //
    })->name('profile');

    // Laravel 5.8: http://example.com/profile/active
    echo route('profile', ['status' => 'active']);

    // Laravel 6.0: http://example.com/profile?status=active
    echo route('profile', ['status' => 'active']);    

<a name="miscellaneous"></a>
### Miscellaneous

We also encourage you to view the changes in the `laravel/laravel` [GitHub repository](https://github.com/laravel/laravel). While many of these changes are not required, you may wish to keep these files in sync with your application. Some of these changes will be covered in this upgrade guide, but others, such as changes to configuration files or comments, will not be. You can easily view the changes with the [GitHub comparison tool](https://github.com/laravel/laravel/compare/5.8...master) and choose which updates are important to you.
