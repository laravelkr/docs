# 업그레이드 가이드

- [5.8에서 6.0으로 업그레이드](#upgrade-6.0)

<a name="high-impact-changes"></a>
## 매우 중요한 변경사항

<!-- <div class="content-list" markdown="1"> -->
- [String & Array Helpers](#helpers)
- [문자열 & 배열 헬퍼](#helpers)
<!-- </div> -->

<!-- <a name="medium-impact-changes"></a> -->
## 중요한 변경사항

<!-- <div class="content-list" markdown="1"> -->
- [Carbon 1.x 지원 종료](#carbon-support)
- [Redis 기본 클라이언트](#redis-default-client)
- [데이터베이스 `Capsule::table` 메소드](#capsule-table)
- [Eloquent Arrayable & `toArray`](#eloquent-to-array)
- [Eloquent `BelongsTo::update` Method](#belongs-to-update)
- [Eloquent Primary Key 타입](#eloquent-primary-key-type)
- [Localization `Lang::trans` 메소드와 `Lang::transChoice` 메소드](#trans-and-trans-choice)
- [Localization `Lang::getFromJson` 메소드](#get-from-json)
- [큐 재시도 횟수 제한](#queue-retry-limit)
- [이메일 인증 재발송 라우트](#email-verification-route)
- [Email 확인 경로 변경](#email-verification-route-change)
- [`Input` 파사드](#the-input-facade)
<!-- </div> -->

<a name="upgrade-6.0"></a>
## 5.8에서 6.0으로 업그레이드 하기

#### 예상 소요 시간: 1시간

> {note} 이전 버전과 호환되지 않는 모든 변경사항을 기록했습니다만 변경사항들 중 일부는 프레임워크의 모호한 부분에 있기 때문에 실제로는 여러분의 어플리케이션에 영향을 끼치지 않을 수도 있습니다.

### PHP 7.2 필요

**영향 가능성: 중간**

PHP 7.1 버전은 2019년 12월 이후로 더 이상 활발하게 지원되지 않습니다. 그렇기 때문에 라라벨 6.0은 PHP 7.2버전 이상을 필요로 합니다. 

<a name="updating-dependencies"></a>
### 의존성 업데이트

`composer.json` 파일을 열어 `laravel/framework` 의존성을 `^6.0` 으로 업데이트 하세요.

그리고, 어플리케이션에서 사용 중인 모든 3rd party 패키지들을 살펴보고 라라벨 6 버전을 지원하는 버전을 사용중인지 확인하세요. 

### 인가

<a name="authorized-resources"></a>
#### 인가된 리소스 & `viewAny`

**영향 가능성: 높음**

`authorizeResource` 함수를 사용해 컨트롤러에 붙여진 인가 정책은 이제 `viewAny` 메소드를 정의해야만 합니다. 이 메소드는 유저가 컨트롤러의 `index` 메소드에 접근할 때 호출됩니다. 이 메소드를 정의하지 않으면, 컨트롤러의 `index` 메소드에 대한 호출이 거부되거나 비인가 처리됩니다. 

#### Authorization Response 클래스

**영향 가능성: 낮음**

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

#### "Deny" 응답 반환

**영향 가능성: 낮음**

Laravel의 이전 릴리스에서는 예외가 즉시 발생했기 때문에 정책-policy 메소드에서 `deny` 메소드의 값을 반환 할 필요가 없었습니다. 그러나 Laravel 문서에 따라 이제 정책-policy에서 `deny` 메소드의 값을 반환해야합니다.

    public function update(User $user, Post $post)
    {
        if (! $user->role->isEditor()) {
            return $this->deny("You must be an editor to edit this post.")
        }

        return $user->id === $post->user_id;
    }

<a name="auth-access-gate-contract"></a>
#### `Illuminate\Contracts\Auth\Access\Gate` 인터페이스

**영향 가능성: 낮음**

`Illuminate\Contracts\Auth\Access\Gate` 인터페이스에 `inspect` 라는 새로운 메소드가 추가되었습니다. 만약 이 인터페이스를 직접 구현하고 있다면 구현체에 이 메소드를 추가해야 합니다.

### Carbon

<a name="carbon-support"></a>
#### Carbon 1.x 버전은 더 이상 지원되지 않습니다.

**영향 가능성: 중간**

Carbon 1.x 버전의 지원 기간이 종료되었기 때문에 [더 이상 지원되지 않습니다](https://github.com/laravel/framework/pull/28683). 어플리케이션을 Carbon 2.0 버전을 사용하도록 업그레이드 하세요.

### 설정

#### `AWS_REGION` 환경 변수

**영향 가능성: 선택적**

만약 [라라벨 Vapor](https://vapor.laravel.com)를 사용하려 한다면 `config` 디렉토리의 모든 `AWS_REGION` 문자열을 `AWS_DEFAULT_REGION` 으로 변경해야 합니다. 또한, `.env` 파일의 이 환경변수 이름을 변경해야 합니다.

<a name="redis-default-client"></a>
#### Redis 기본 클라이언트

Redis 기본 클라이언트가 `predis`에서 `phpredis`로 변경되었습니다. `predis`를 계속 사용하려면 `config/database.php` 설정 파일에서 `redis.client` 설정 옵션이 `predis`로 되어 있는지 확인하십시오.

### 데이터베이스

<a name="capsule-table"></a>
#### Capsule `table` 메소드

**영향 가능성: 중간**

> {note} 이 변경사항은 `illuminate/database` 패키지를 라라벨과 별개로 사용중인 어플리케이션에만 해당됩니다.

`Illuminate\Database\Capsule\Manager` 클래스의 `table` 메소드가 두번째 인자로 테이블의 alias를 받도록 변경되었습니다. 만약 `illuminate/database` 패키지를 라라벨과 별개로 사용중이라면 이 메소드를 호출하는 부분들을 따라 수정해야 합니다.

    /**
     * Get a fluent query builder instance.
     *
     * @param  \Closure|\Illuminate\Database\Query\Builder|string  $table
     * @param  string|null  $as
     * @param  string|null  $connection
     * @return \Illuminate\Database\Query\Builder
     */
    public static function table($table, $as = null, $connection = null)

#### `cursor` 메소드

**영향 가능성: 낮음**

`cursor` 메소드는 이제 `Generator` 객체 대신 `Illuminate\Support\LazyCollection` 객체를 리턴합니다. `LazyCollection` 은 기존의 제너레이터와 동일하게 반복할 수 있습니다.

    $users = App\User::cursor();

    foreach ($users as $user) {
        //
    }

<a name="eloquent"></a>
### Eloquent

<a name="belongs-to-update"></a>
#### `BelongsTo::update` 메소드

**영향 가능성: 중간**

통일성을 위해 `BelongsTo` 관계의 `update` 메소드는 ad-hoc 업데이트 쿼리로 작동합니다. 즉 이제 더 이상 대량할당으로부터 보호되거나, 엘로퀀트 이벤트를 발생시키지 않는다는 것을 의미합니다. 다른 타입의 관계들의 `update` 메소드와 동일하게 동작하게 된 것입니다.

만약 `BelongsTo` 관계에 있는 모델을 업데이트 하면서 대량 할당 보호 기능, 엘로퀀트 이벤트 기능을 사용하고 싶다면 모델 자체의 `update` 메소드를 호출해야 합니다.

    // Ad-hoc query... no mass assignment protection or events...
    $post->user()->update(['foo' => 'bar']);

    // Model update... provides mass assignment protection and events...
    $post->user->update(['foo' => 'bar']);

<a name="eloquent-to-array"></a>
#### Arrayable & `toArray`

**영향 가능성: 중간**

엘로퀀트 모델의 `toArray` 메소드는 이제 `Illuminate\Contracts\Support\Arrayable` 를 구현하는 속성을 모두 배열로 캐스팅합니다.

<a name="eloquent-primary-key-type"></a>
#### Primary Key 타입 정의

**영향 가능성: 중간**

라라벨 6.0에는 integer 키 타입을 위한 [성능 향상](https://github.com/laravel/framework/pull/28153)이 추가되었습니다. 만약 모델의 Primary Key로 문자열 타입을 사용중이라면, `$keyType` 프로퍼티를 모델에 선언해 주어야 합니다.

    /**
     * The "type" of the primary key ID.
     *
     * @var string
     */
    protected $keyType = 'string';

### 이메일 인증

<a name="email-verification-route"></a>
#### 이메일 인증 재전송 Route HTTP 메소드

**영향 가능성: 중간**

가능한 CSRF 공격을 방지하기 위하여 라라벨의 내장된 이메일 인증 기능의 `email/resend` 라우트가 `GET` 라우트에서 `POST` 라우트로 변경되었습니다. 그러므로 여러분은 이 라우트로 알맞은 메소드로 리퀘스트를 보내도록 프론트엔드 코드를 수정해야 합니다. 예를 들어, 만약 내장된 이메일 인증 기능을 수정하지 않고 사용중이라면: 

    {{ __('Before proceeding, please check your email for a verification link.') }}
    {{ __('If you did not receive the email') }},

    <form class="d-inline" method="POST" action="{{ route('verification.resend') }}">
        @csrf

        <button type="submit" class="btn btn-link p-0 m-0 align-baseline">
            {{ __('click here to request another') }}
        </button>.
    </form>

<a name="mustverifyemail-contract"></a>
#### `MustVerifyEmail` 인터페이스

**영향 가능성: 낮음**

새로운 `getEmailForVerification` 메소드가 `Illuminate\Contracts\Auth\MustVerifyEmail` 인터페이스에 추가되었습니다. 만약 이 인터페이스를 직접 구현해서 사용하고 있다면, 이 메소드도 구현해야 합니다. 만약 `App\User` 모델이 `Illuminate\Auth\MustVerifyEmail` trait을 사용중이라면 이 trait이 앞서 언급한 메소드를 구현하고 있기 때문에 별도의 수정사항이 필요하지 않습니다.

<a name="email-verification-route-change"></a>
#### Email 확인 경로 변경

**영향 가능성: 중간**

이메일 확인 경로가 `/email/verify/{id}`에서 `/email/verify/{id}/{hash}`로 변경되었습니다. Laravel 6.x로 업그레이드하기 전에 전송 된 이메일 확인 이메일은 더 이상 유효하지 않으며 404 페이지를 표시합니다. 원하는 경우 이전 확인 URL 경로와 일치하는 경로를 정의하고 사용자에게 이메일 주소를 다시 확인하도록 요청하는 정보 메시지를 표시 할 수 있습니다.

<a name="helpers"></a>
### 헬퍼들

#### 문자열 & 배열 헬퍼 패키지

**영향 가능성: 높음**

모든 `str_` 과 `array_` 헬퍼들이 새로운 `laravel/helpers` 컴포저 패키지로 이동되었고 프레임워크에서는 제거되었습니다. 원한다면 이 헬퍼들에 대한 호출을 `Illuminate\Support\Str`과 `Illuminate\Support\Arr` 클래스로 변경해도 됩니다. 아니면 `laravel/helpers` 패키지를 어플리케이션에 추가하고 헬퍼들을 게속 사용 할 수 있습니다.

    composer require laravel/helpers

### 다국어

<a name="trans-and-trans-choice"></a>
#### `Lang::trans` & `Lang::transChoice` 메소드들

**영향 가능성: 중간**

번역 기능의 `Lang::trans` 와 `Lang::transChoice` 메소드의 이름이 `Lang::get` 과 `Lang::choice` 로 변경되었습니다.

또한, 만약 `Illuminate\Contracts\Translation\Translator` 인터페이스를 직접 구현해서 사용중이라면 구현체의 `trans` 와 `transChoice` 메소드의 이름을 `get` 과 `choice`로 변경해주세요.

<a name="get-from-json"></a>
#### `Lang::getFromJson` 메소드

**영향 가능성: 중간**

`Lang::get` 과 `Lang::getFromJson` 메소드가 통합되었습니다. `Lang::getFromJson` 메소드를 호출하는 부분은 모두 `Lang::get` 를 호출하도록 변경되어야 합니다.

> {note} `Lang::transChoice`, `Lang::trans` 및 `Lang::getFromJson` 제거와 관련된 블레이드 오류를 피하려면 `php artisan view:clear` 아티즌 명령을 실행해야합니다.

### 메일

#### Mandrill & SpartPost 드라이버 삭제

**영향 가능성: 낮음**

`mandrill` 과 `sparkpost` 메일 드라이버들이 삭제되었습니다. 만약 이 드라이버들을 계속 사용하고 싶다면 해당 드라이버들을 제공하는 커뮤니티 기반의 패키지를 사용하길 권합니다.

### 알림

#### Nexmo 라우팅 삭제

**영향 가능성: 낮음**

Nexmo 알림 채널이 마침내 프레임워크의 코어에서 제거되었습니다. 만약 Nexmo 라우팅에 의존하고 있다면 `routeNotificationForNexmo` 메소드를 [문서에 설명된 대로](/docs/{{version}}/notifications#routing-sms-notifications) notifiable entity에 구현하세요.

### 비밀번호 초기화

#### 비밀번호 검증

**영향 가능성: 낮음**

`PasswordBroker`는 이제 비밀번호를 검증하지 않습니다. `ResetPasswordController`가 이미 비밀번호 검증을 처리해주기 때문에, 브로커의 검증과정은 중복이며 커스터마이징이 불가능합니다. 만약 내장된 `ResetPasswordController` 와는 별도로 `PasswordBroker` (혹은 `Password` 파사드)를 사용하고 있다면 브로커로 넘기기 전에 검증해야 합니다.

### 큐

<a name="queue-retry-limit"></a>
#### 큐 재시도 횟수 제한

**영향 가능성: 중간**

라라벨의 이전 릴리즈에서는, `php artisan queue:work` 명령어가 Job 처리를 무제한으로 재시도했습니다. 라라벨 6.0 부터는 Job 처리를 한번만 시도합니다. 만약 무제한으로 재시도 하도록 강제하고 싶다면 `--tries` 옵션에 `0` 을 넣으세요:

    php artisan queue:work --tries=0

또한, 어플리케이션의 데이터베이스에 `failed_jobs` 테이블이 있는지 확인하세요. 이 테이블에 대한 마이그레이션은 `queue:failed-table` 아티즌 커맨드로 생성할 수 있습니다.

    php artisan queue:failed-table

### 리퀘스트

<a name="the-input-facade"></a>
#### `Input` 파사드

**영향 가능성: 중간**

`Request` 파사드와 중복되었던 `Input` 파사드가 삭제되었습니다. 만약 `Input::get` 메소드를 사용중이라면 `Request::input` 메소드를 호출하도록 변경해야 합니다. `Input` 파사드의 다른 메소드들은 그냥 `Request` 파사드를 사용하도록 변경하면 됩니다.

### 스케줄링

#### `between` 메소드

**영향 가능성: 낮음**

라라벨의 이전 버전에서는 스케줄러의 `between` 메소드가 날짜 형식을 다루는데에 있어 혼란스럽게 동작했습니다. 예를 들면:

    $schedule->command('list')->between('23:00', '4:00');

대부분의 사용자는 이 메소드가 23시와 4시 사이의 매 분마다 `list` 커맨드를 실행할 거라고 예상합니다. 하지만 라라벨의 이전 버전에서 이 스케줄러는 4시와 23시 사이의 매 분마다라 `list` 함수를 호출했습니다. 이는 본질적으로 지정한 시간이 뒤바뀌는 것입니다. 라라벨 6.0 버전에서는 이 동작이 수정되었습니다.

### 스토리지

<a name="rackspace-storage-driver"></a>
#### Rackspace 스토리지 드라이버 삭제

**영향 가능성: 낮음**

`rackspace` 스토리지 드라이버가 삭제되었습니다. Rackspace를 스토리지 프로바이더로 계속 사용하려면 커뮤니티에서 개발되는 패키지들 중 이 드라이버를 제공하는 것을 사용하길 권합니다.

### URL 생성

#### 라우트 URL 생성 & 추가 파라미터

라라벨의 이전 릴리스에서는 연관 배열 파라메터를 `route` 헬퍼 또는 `URL::route` 메소드에 전달하면 파라메터 값에 라우트 내에 일치하는 키가 없는 경우에도 라우트에 대한 URL을 생성 할 때 이러한 파라메터를 URI 값으로 사용하는 경우가 있었습니다. Laravel 6.0부터는 이러한 값이 대신 쿼리 문자열에 첨부됩니다. 예를 들어 다음 경로를 고려하십시오.

    Route::get('/profile/{location}', function ($location = null) {
        //
    })->name('profile');

    // Laravel 5.8: http://example.com/profile/active
    echo route('profile', ['status' => 'active']);

    // Laravel 6.0: http://example.com/profile?status=active
    echo route('profile', ['status' => 'active']);    

`action` 헬퍼와 `URL::action` 메소드도 이 변경의 영향을 받습니다.

    Route::get('/profile/{id}', 'ProfileController@show');

    // Laravel 5.8: http://example.com/profile/1
    echo action('ProfileController@show', ['profile' => 1]);

    // Laravel 6.0: http://example.com/profile?profile=1
    echo action('ProfileController@show', ['profile' => 1]);   

### 검증

#### FormRequest `validationData` 메소드

**영향 가능성: 낮음**

폼 요청-request의 `validationData` 메소드가 `protected`에서 `public`으로 변경되었습니다. 구현에서 이 메소드를 재정의하는 경우 가시성을 `public`으로 업데이트해야합니다.

<a name="miscellaneous"></a>
### 기타

`laravel/laravel` [GitHub 저장소](https://github.com/laravel/laravel)에서 변경사항들을 확인해보시길 권합니다. 변경사항들 중 많은 부분들은 필수는 아니지만, 업데이트 된 파일들을 여러분의 어플리케이션에 동기화 해두는게 좋습니다. 변경사항들 중 일부는 이 업그레이드 가이드에서 설명되었지만 설정 파일이나 코멘트같은 다른 변경사항들은 그렇지 않았습니다. [GitHub comparison tool](https://github.com/laravel/laravel/compare/5.8...master)을 이용해 변경사항들을 쉽게 살펴보고 어떤 변경사항이 여러분에게 중요한지 살펴보세요.
