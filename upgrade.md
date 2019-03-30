# 업그레이드 가이드

- [5.7에서 5.8.0 으로 업그레이드 하기](#upgrade-5.8.0)

<a name="high-impact-changes"></a>
## 매우 중요한 변경사항

- [초단위의 Cache TTL](#cache-ttl-in-seconds)
- [Cache Lock 안정성 개선](#cache-lock-safety-improvements)
- [Markdown File 디렉토리 변경](#markdown-file-directory-change)
- [Nexmo / Slack Notification Channels](#nexmo-slack-notification-channels)


<a name="medium-impact-changes"></a>
## 중요한 변경사항

- [컨테이너 제네레이터와 태그된 서비스들](#container-generators)
- [SQLite 버전 제약 조건](#sqlite)
- [헬퍼보다 String과 Array Classes 선호](#string-and-array-helpers)
- [지연된 서비스 프로바이더](#deferred-service-providers)
- [PSR-16 적합성](#psr-16-conformity)
- [불규칙한 복수형으로 끝나는 모델 이름](#model-names-ending-with-irregular-plurals)
- [ID가 증가하는 사용자 지정 피벗 모델](#custom-pivot-models-with-incrementing-ids)
- [Pheanstalk 4.0](#pheanstalk-4)

<a name="upgrade-5.8.0"></a>
## 5.7에서 5.8.0 으로 업그레이드 하기

#### 예상 업그레이드 시간 : 1 시간

> {note} 가능한 모든 변경 내용을 기록하려고 했습니만, 변경 사항 중 일부는 프레임워크의 명확하지 않은 부분에서 이루어 지기 때문에 이중 일부가 실제 애플리케이션에 영향을 끼칠 수도 있습니다.

<a name="updating-dependencies"></a>
### 의존성 업데이트

`composer.json` 파일에 있는 `laravel/framework` 의존성을 `5.8.*`으로 업데이트합니다.

그리고 애플리케이션에서 사용하는 써드파티 패키지를 확인하고 라라벨 5.8를 지원하는 적절한 버전을 사용하고 있는지 확인하십시오.

<a name="the-application-contract"></a>
### `Application` Contract

#### `environment` 메소드

**영향 가능성 : 매우 낮음**

`Illuminate/Contracts/Foundation/Application` contract의 `environment` 메소드의 사용법이 [변경되었습니다](https://github.com/laravel/framework/pull/26296). 애플리케이션에서 이 contract를 구현하는 경우 메소드를 수정해야합니다.

    /**
     * Get or check the current application environment.
     *
     * @param  string|array  $environments
     * @return string|bool
     */
    public function environment(...$environments);

#### 추가된 메소드들

**영향 가능성 : 매우 낮음**

`bootstrapPath`, `configPath`, `databasePath`, `environmentPath`, `resourcePath`, `storagePath`, `resolveProvider`, `bootstrapWith`, `configurationIsCached`, `detectEnvironment`, `environmentFile`, `environmentFilePath`, `getCachedConfigPath`, `getCachedRoutesPath`, `getLocale`, `getNamespace`, `getProviders`, `hasBeenBootstrapped`, `loadDeferredProviders`, `loadEnvironmentFrom`, `routesAreCached`, `setLocale`, `shouldSkipMiddleware` 그리고 `terminate`  메소드가 [`Illuminate/Contracts/Foundation/Application` contract에 추가되었습니다](https://github.com/laravel/framework/pull/26477).

매우 드물지만 이 인터페이스를 구현하는 경우 이 메소드를 구현체에 추가해야합니다.

<a name="authentication"></a>
### 인증

#### 비밀번호 재설정 알림 라우트의 파라메터

**영향 가능성 : 낮음**

사용자가 비밀번호 재설정을 위한 링크를 요청하면, Laravel은 `route` 헬퍼를 사용하여 `password.reset` 라우트에 대한 URL을 생성합니다. Laravel 5.7을 사용할 때 토큰은 다음과 같이 명시적 이름없이 `route`헬퍼로 전달합니다

    route('password.reset', $token);

Laravel 5.8을 사용할 때는 토큰은 명시적 파라메터로 `route`헬퍼로 전달합니다.

    route('password.reset', ['token' => $token]);

그러므로 자신만의 `password.reset` 라우트를 정의할 경우 URI에 `{token}` 파라메터가 들어 있는지 확인해야합니다.


#### 새로운 기본 비밀번호 길이

**영향 가능성 : 낮음**

비밀번호를 선택하거나 재설정 할 때 필요한 비밀번호 길이는 [최소 8 자 이상으로 변경되었습니다](https://github.com/laravel/framework/pull/25957).

<a name="cache"></a>
### 캐시

<a name="cache-ttl-in-seconds"></a>
#### 초 단위의 TTL

**영향 가능성 : 매우 높음**

항목을 저장할 때, 보다 세분화 된 만료시간을 사용하기 위해 캐시의 만료시간이 분단위에서 초단위로 변경되었습니다. `Illuminate\Cache\Repository` 클래스와 확장 클래스의 `put`,`putMany`,`add`,`remember`,`setDefaultCacheTime` 메소드, 각 캐시 스토어의 `put` 메소드와 동작들이 변경되었습니다. 자세한 내용은 [관련 PR](https://github.com/laravel/framework/pull/27276)을 참조하십시오.

이 메소드들에게 정수를 전달하는 경우, 캐시에 보관할 시간(초단위)을 전달하도록 코드를 업데이트해야합니다. 또는 항목이 만료되어야하는 시점를 나타내는 `DateTime` 인스턴스를 전달할 수도 있습니다.

    // Laravel 5.7 - Store item for 30 minutes...
    Cache::put('foo', 'bar', 30);

    // Laravel 5.8 - Store item for 30 seconds...
    Cache::put('foo', 'bar', 30);

    // Laravel 5.7 / 5.8 - Store item for 30 seconds...
    Cache::put('foo', 'bar', now()->addSeconds(30));

> {tip} 이 변경으로 Laravel 캐시 시스템은 [PSR-16 캐싱 라이브러리 표준](https://www.php-fig.org/psr/psr-16/)을 완벽하게 준수합니다.

<a name="psr-16-conformity"></a>
#### PSR-16 적합성

**영향 가능성 : 보통**

[하단의 반환 값이 변경됨](#the-repository-and-store-contracts) 외에도 `put`, `putMany` 및 `add` 메소드의 TTL 인수는 `Illuminate\Cache\Repository` 클래스가 PSR-16 스펙과 보다 잘 일치하도록 업데이트되었습니다. 새로운 동작은 기본값이 `null`이므로 TTL을 지정하지 않고 호출하면 캐시 항목이 영원히 저장됩니다. 또한 캐시 항목을 TTL 0 이하로 저장하면 캐시에서 항목이 제거됩니다. 자세한 내용은 [관련 PR](https://github.com/laravel/framework/pull/27217)을 참조하십시오.

이 변경 사항으로 `KeyWritten` 이벤트 [또한 업데이트되었습니다](https://github.com/laravel/framework/pull/27265).

<a name="cache-lock-safety-improvements"></a>
#### Lock 안정성 개선

**영향 가능성 : 높음**

Laravel 5.7 및 이전 버전의 Laravel에서는 일부 캐시 드라이버가 제공하는 "원자 잠금"기능이 예상치 못한 동작을 하여 초기 잠금 해제로 이어질 수 있었습니다.

예를 들면 다음과 같습니다. **Client A**는 만료가 10초인 Lock `foo`를 획득합니다. 이때 **Client A**는 실제로 작업을 완료하는 데 20초가 걸린다고 가정합니다. Lock `foo`는 캐시 시스템에 의해 **Client A**의 작업이 처리되는 도중 10초 동안 자동으로 해제됩니다. **Client B**가 이때 Lock `foo`를 획득합니다. **Client A**의 작업이 끝나고 Lock `foo`를 해제하여 의도치 않게 **Client B**가 가진 Lock을 해제됩니다. **Client C**는 이제 Lock을 획득 할 수 있게 되었습니다.
(역자주: 원문이 너무 딱딱하다고 느껴져 임의로 문장을 다듬었습니다. 만약 표현이 이상하다고 느껴지신다면 원문을 보신 후 PR부탁드립니다)

이 시나리오를 완화하기 위해 프레임워크가 정상적인 상황에서 잠금의 올바른 소유자만 잠금을 해제 할 수 있도록하는 "범위 토큰"이 포함 된 잠금이 생성됩니다.

만약 Lock과 상호 작용하는 `Cache::lock()->get(Closure)`메소드를 사용하고 있다면, 아무런 변경이 필요 없습니다.

    Cache::lock('foo', 10)->get(function () {
        // Lock will be released safely automatically...
    });

그러나 수동으로 `Cache::lock()->release()`를 호출하는 경우 Lock 인스턴스를 유지하도록 코드를 업데이트해야합니다. 그런 다음 작업 수행을 마친 후에 **동일한 Lock 인스턴스**에 대해`release` 메소드를 호출 할 수 있습니다. 예 :

    if ($lock = Cache::lock('foo', 10)->get()) {
        // Perform task...

        $lock->release();
    }

어쩔땐 한 프로세스에서 잠금을 획득하고 다른 프로세스에서 잠금을 해제하고자 할 수 있습니다. 예를 들어, 웹 요청 중에 Lock을 획득하고 해당 요청으로 인해 대기중인 작업이 끝날 때 Lock을 해제하고자 할 수 있습니다. 이 시나리오에서는 Lock 범위가 지정된 "소유자 토큰"을 대기중인 작업에 전달하여 작업이 주어진 토큰을 사용하여 Lock을 다시 인스턴스화 할 수 있도록해야합니다.

    // Within Controller...
    $podcast = Podcast::find(1);

    if ($lock = Cache::lock('foo', 120)->get()) {
        ProcessPodcast::dispatch($podcast, $lock->owner());
    }

    // Within ProcessPodcast Job...
    Cache::restoreLock('foo', $this->owner)->release();

만약 현재 소유자를 무시하고 잠금을 해제하려면 `forceRelease` 메소드를 사용할 수 있습니다 :

    Cache::lock('foo')->forceRelease();

<a name="the-repository-and-store-contracts"></a>
####`Repository`와 `Store` Contracts

**영향 가능성 : 매우 낮음**

`PSR-16`을 준수하기 위해 `Illuminate\Contracts\Cache\Repository` contract의 `put` 및 `forever` 메소드의 반환 값과 `put`, `putMany` `Illuminate\Contracts\Cache\Store` contract의 `forever`메소드가 `void`에서 `bool`으로 [변경되었습니다](https://github.com/laravel/framework/pull/26726).

<a name="collections"></a>
### 컬렉션

#### The `firstWhere` Method

**영향 가능성 : 매우 낮음**

`firstWhere`메소드의 사용법은 `where`메소드의 사용법과 일치하도록 [변경되었습니다](https://github.com/laravel/framework/pull/26261). 이 메서드를 재정의하는 경우 부모 메서드와 일치하도록 메서드 사용법을 수정해야합니다.

    /**
     * Get the first item by the given key value pair.
     *
     * @param  string  $key
     * @param  mixed  $operator
     * @param  mixed  $value
     * @return mixed
     */
    public function firstWhere($key, $operator = null, $value = null);

<a name="console"></a>
### 콘솔

#### `Kernel` Contract

**영향 가능성 : 매우 낮음**

`terminate` 메소드가 [`Illuminate/Contracts/Console/Kernel` 계약에 추가되었습니다](https://github.com/laravel/framework/pull/26393). 이 인터페이스를 구현하는 경우이 메소드를 구현에 추가해야합니다.

<a name="container"></a>
### 컨테이너

<a name="container-generators"></a>
#### 제네레이터와 태그된 서비스들

**영향 가능성 : 보통**

컨테이너의 `tagged` 메소드는 PHP의 제네레이터를 사용하여 주어진 태그로 서비스를 지연시켜서 인스턴스화합니다. 모든 태그 된 서비스를 사용하지 않으면 성능이 향상됩니다.

이 변경 때문에 `tagged` 메소드는 이제 `array` 대신에 `iterable`을 리턴합니다. 이 메소드의 반환 값을 타입 힌팅하는 경우, 타입 힌트가 `iterable`로 변경되었는지 확인해야합니다.

또한 `$container->tagged('foo')[0]`과 같은 배열 오프셋 값을 사용하여 태그가 있는 서비스에 직접 액세스하는 것은 더 이상 불가능합니다.

#### `resolve` 메소드

**영향 가능성 : 매우 낮음**

`resolve` 메소드는 이제 객체의 인스턴스 생성 중에 이벤트(resolving callbacks)가 발생 / 실행되어야 하는지를 지정는 새로운 부울-boolean 파라미터를 [받습니다](https://github.com/laravel/framework/pull/27066). 이 메서드를 재정의하는 경우 부모와 일치하도록 메서드 사용법을 수정해야합니다.

#### `addContextualBinding` 메소드

**영향 가능성 : 매우 낮음**

`addContextualBinding`메소드가 [`Illuminate\Contracts\Container\Container` contract에 추가되었습니다](https://github.com/laravel/framework/pull/26551). 이 인터페이스를 구현하는 경우이 메소드를 구현에 추가해야합니다.

#### `tagged` 메소드

**영향 가능성 : 낮음**

`tagged` 메소드 사용법이 [변경되었습니다](https://github.com/laravel/framework/pull/26953). 이제 `array` 대신에 `iterable`을 리턴합니다. 코드에서 이 메소드의 반환 값을 `array`로 얻는 매개 변수 힌트가 있다면 타입 힌트를 `iterable`으로 수정해야합니다.

#### `flush` 메소드

**영향 가능성 : 매우 낮음**

`flush` 메소드가 [`Illuminate\Contracts\Container\Container` contract에 추가되었습니다](https://github.com/laravel/framework/pull/26477). 이 인터페이스를 구현하는 경우이 메소드를 구현에 추가해야합니다.

<a name="database"></a>
### Database

#### 따옴표가 없는 MySQL JSON 값

**영향 가능성 : 낮음**

쿼리 빌더는 MySQL과 MariaDB를 사용할 때 따옴표가 없는 JSON 값을 반환합니다. 이 동작은 지원하는 다른 데이터베이스와 일치합니다.

    $value = DB::table('users')->value('options->language');

    dump($value);

    // Laravel 5.7...
    '"en"'

    // Laravel 5.8...
    'en'

결과적으로 `->>` 연산자는 더 이상 지원되거나 필요하지 않습니다.

<a name="sqlite"></a>
#### SQLite

**영향 가능성 : 보통**

Laravel 5.8부터 [지원하는 가장 오래된 SQLite 버전](https://github.com/laravel/framework/pull/25995)은 SQLite 3.7.11입니다. 이전 SQLite 버전을 사용하는 경우 SQLite 버전을 업데이트해야합니다 (SQLite 3.8.8 이상 권장).

<a name="eloquent"></a>
### Eloquent

<a name="model-names-ending-with-irregular-plurals"></a>
#### 불규칙한 복수형으로 끝나는 모델 이름

**영향 가능성 : 보통**

Laravel 5.8부터는 불규칙 복수형 단어로 끝나는 다중 단어 모델 이름이 [올바르게 복수화되었습니다](https://github.com/laravel/framework/pull/26421).

    // Laravel 5.7...
    App\Feedback.php -> feedback (correctly pluralized)
    App\UserFeedback.php -> user_feedbacks (incorrectly pluralized)

    // Laravel 5.8
    App\Feedback.php -> feedback (correctly pluralized)
    App\UserFeedback.php -> user_feedback (correctly pluralized)

잘못 복수화 된 모델을 가지고 있다면 모델에 `$table` 속성을 정의하여 이전 테이블 이름을 계속 사용할 수 있습니다 :

    /**
     * The table associated with the model.
     *
     * @var string
     */
    protected $table = 'user_feedbacks';

<a name="custom-pivot-models-with-incrementing-ids"></a>
#### ID가 증가하는 사용자 지정 피벗 모델

커스텀 피벗 모델을 사용하는 다 대다 관계를 정의하고, 그 피벗 모델이 자동 증가하는 기본 키를 가지고 있다면, 커스텀 피벗 모델 클래스가 `incrementing` 속성을 `true`로 정의하는지 확인해야합니다.

    /**
     * Indicates if the IDs are auto-incrementing.
     *
     * @var bool
     */
    public $incrementing = true;

#### `loadCount` 메소드

**영향 가능성 : 낮음**

`Illuminate\Database\Eloquent\Model` 클래스에 `loadCount` 메소드가 추가되었습니다. 애플리케이션이 `loadCount` 메소드도 정의한다면, Eloquent의 정의와 충돌 할 수 있습니다.

#### `originalIsEquivalent` 메소드

**영향 가능성 : 매우 낮음**

`Illuminate\Database\Eloquent\Concerns\HasAttributes` 특성의 `originalIsEquivalent` 메소드가 `protected`에서 `public`으로 [변경되었습니다](https://github.com/laravel/framework/pull/26391).

####`deleted_at` 속성의 자동 소프트삭제 된 형태 변환

**영향 가능성 : 낮음**

Eloquent 모델이 `Illuminate\Database\Eloquent\SoftDeletes` trait을 사용할 때 `deleted_at` 속성이 `Carbon` 인스턴스로 [이제 자동으로 캐스팅됩니다](https://github.com/laravel/framework/pull/26985). 이 동작을 무시하려면 해당 속성에 대한 사용자 지정 접근자를 작성하거나 수동으로 `casts` 특성에 추가하면됩니다.

    protected $casts = ['deleted_at' => 'string'];

#### `getForeignKey` 메소드

**영향 가능성 : 낮음**

`BelongsTo` 관계의 `getForeignKey` 메소드와 `getQualifiedForeignKey` 메소드는 각각 `getForeignKeyName`과 `getQualifiedForeignKeyName`으로 이름이 바뀌 었습니다. 메소드 이름은 Laravel이 제공하는 다른 관계와 일치합니다.

### Environment

**영향 가능성 : 낮음**

`.env` 파일을 파싱하는 데 사용되는 [phpdotenv](https://github.com/vlucas/phpdotenv) 패키지는 새로운 메이저 버전을 발표했는데 이것은 `env` 헬퍼로부터 반환 된 결과에 영향을 줄 수 있습니다. 특히, 따옴표로 묶이지 않은 값의 `#`문자는 이제 값의 일부가 아닌 주석으로 간주됩니다.

이전 동작:

    ENV_VALUE=foo#bar

    env('ENV_VALUE'); // foo#bar

신규 동작:

    ENV_VALUE=foo#bar
    env('ENV_VALUE'); // foo

이전 동작을 유지하려면 환경 값을 따옴표로 묶어야합니다.

    ENV_VALUE="foo#bar"

    env('ENV_VALUE'); // foo#bar

자세한 내용은 [phpdotenv 업그레이드 안내서](https://github.com/vlucas/phpdotenv/blob/master/UPGRADING.md)를 참조하십시오.

<a name="events"></a>
### Events

#### `fire` 메소드

**영향 가능성 : 낮음**

`Illuminate/Events/Dispatcher` 클래스의 `fire` 메소드 (Laravel 5.4부터 deprecated되었습니다)가 [삭제되었습니다](https://github.com/laravel/framework/pull/26392). 대신에 `dispatch` 메소드를 사용해야합니다.

<a name="exception-handling"></a>
### 예외 처리

#### `ExceptionHandler` Contract

**영향 가능성 : 낮음**

`shouldReport` 메소드는 [`Illuminate\Contracts\Debug\ExceptionHandler` contract에 추가되었습니다](https://github.com/laravel/framework/pull/26193). 이 인터페이스를 구현하는 경우 이 메소드를 구현에 추가해야합니다.

#### `renderHttpException` 메소드

**영향 가능성 : 낮음**

`Illuminate\Foundation\Exceptions\Handler` 클래스의 `renderHttpException` 메소드 사용법이 [변경되었습니다](https://github.com/laravel/framework/pull/25975). exception handler에서 이 메소드를 재정의하는 경우 부모와 일치하도록 메서드 사용법을 업데이트해야합니다.

    /**
     * Render the given HttpException.
     *
     * @param  \Symfony\Component\HttpKernel\Exception\HttpExceptionInterface  $e
     * @return \Symfony\Component\HttpFoundation\Response
     */
    protected function renderHttpException(HttpExceptionInterface $e);

<a name="mail"></a>
### Mail

<a name="markdown-file-directory-change"></a>
<a name="markdown-file-directory-change"></a>
### Markdown 파일 디렉토리 변경

**영향 가능성 : 높음**

`vendor:publish` 명령을 사용하여 Laravel의 Markdown 메일 컴포넌트를 퍼블리싱 한 경우`/resources/views/vendor/mail/markdown` 디렉토리의 이름을 `text`로 변경해야합니다.

또한 `markdownComponentPaths` 메소드는 `textComponentPaths`로 [이름이 변경되었습니다](https://github.com/laravel/framework/pull/26938). 이 메소드를 재정의하는 경우 부모와 일치하도록 메서드 이름을 업데이트해야합니다.

#### `PendingMail` 클래스의 메소드 사용법 변경사항

**영향 가능성 : 매우 낮음**

`Illuminate\Mail\PendingMail` 클래스의 `send`, `sendNow`, `queue`, `later` 및 `fill` 메소드가 [변경되었습니다](https://github.com/laravel/framework/pull/26790). `Illuminate\Mail\Mailable` 대신 `Illuminate\Contracts\Mail\Mailable` 인스턴스를 허용합니다. 이러한 메소드 중 일부를 재정의하는 경우 부모와 일치하도록 사용법을 수정해야합니다.

<a name="queue"></a>
### 큐

<a name="pheanstalk-4"></a>
#### Pheanstalk 4.0

**영향 가능성 : 보통**

Laravel 5.8은 Pheanstalk 큐 라이브러리의 `~ 4.0` 버전을 지원합니다. 애플리케이션에 Pheanstalk 라이브러리를 사용하고 있다면, Composer를 통해 라이브러리를 `~ 4.0` 버전으로 업그레이드하십시오.

#### `Job` Contract

**영향 가능성 : 매우 낮음**

`isReleased`, `hasFailed` 및 `markAsFailed` 메소드는 [`Illuminate\Contracts\Queue\Job` contract에 추가되었습니다](https://github.com/laravel/framework/pull/26908). 이 인터페이스를 구현하는 경우 이러한 메소드를 구현에 추가해야합니다.

#### `Job::failed`와 `FailingJob` 클래스

**영향 가능성 : 매우 낮음**

Laravel 5.7에서 대기중인 작업이 실패한 경우 큐 워커는 `FailingJob::handle` 메소드를 실행했습니다. Laravel 5.8에서는 `FailingJob` 클래스에 포함 된 로직이 작업 클래스의 `fail` 메소드로 옮겨졌습니다. 이것때문에 `Illuminate\Contracts\Queue\Job` contract에 `fail` 메소드가 추가되었습니다.

베이스가 되는 `Illuminate\Queue\Jobs\Job` 클래스는 `fail` 구현을 포함하고 있으며 일반적인 애플리케이션 코드에서는 코드 변경이 필요하지 않습니다. 그러나 Laravel이 제공하는 기본 작업 클래스를 확장하지 **않는** 작업 클래스를 사용하여 커스텀 큐 드라이버를 작성하는 경우 커스텀 작업 클래스에서 직접 `fail` 메소드를 구현해야합니다. 이를 위해 Laravel의 기본 작업 클래스를 구현을 참고 할 수 있습니다.

이 변경으로 인해 커스텀 큐 드라이버는 작업 삭제 처리를보다 효율적으로 제어 할 수 있습니다.

#### Redis Blocking Pop

**영향 가능성 : 매우 낮음**

Redis 큐 드라이버의 "블로킹 팝"기능 사용이 이제 안전해졌습니다. 이전에는 작업이 검색된 것과 동시에 Redis 서버 나 워커에서 문제가 발생 할 경우 대기열에 있는 작업이 손실 될 수 있는 작은 가능성이 있었습니다. 블로킹 팝을 안전하게하기 위해 각 Laravel 큐에 `: notify`라는 접미사가 붙은 새로운 Redis리스트가 생성됩니다.

<a name="requests"></a>
### Requests

#### `TransformsRequest` 미들웨어

**영향 가능성 : 낮음**

`Illuminate\Foundation\Http\Middleware\TransformsRequest` 미들웨어의 `transform` 메소드는 이제 입력이 배열 일 때 "완전한" input 키를 요청 받습니다 :

    'employee' => [
        'name' => 'Taylor Otwell',
    ],

    /**
     * Transform the given value.
     *
     * @param  string  $key
     * @param  mixed  $value
     * @return mixed
     */
    protected function transform($key, $value)
    {
        dump($key); // 'employee.name' (Laravel 5.8)
        dump($key); // 'name' (Laravel 5.7)
    }

<a name="routing"></a>
### 라우팅

#### `UrlGenerator` Contract

**영향 가능성 : 매우 낮음**

`previous` 메소드는 [`Illuminate\Contracts\Routing\UrlGenerator` contract에 추가되었습니다](https://github.com/laravel/framework/pull/25616). 이 인터페이스를 구현하는 경우이 메소드를 구현에 추가해야합니다.

####  `Illuminate\Routing\UrlGenerator` 의 `cachedSchema` 프로퍼티

**영향 가능성 : 매우 낮음**

`Illuminate\Routing\UrlGenerator`의 `$cachedSchema` 프로퍼티명은 (Laravel`5.7`에서 deprecated되었습니다) `$cachedScheme`로 [변경되었습니다](https://github.com/laravel/framework/pull/26728).

<a name="sessions"></a>
### 세션

#### `StartSession` 미들웨어

**영향 가능성 : 매우 낮음**

세션 퍼시스턴스 로직은 [`terminate()`메소드에서 `handle()`메소드로 옮겨졌습니다](https://github.com/laravel/framework/pull/26410). 이 방법 중 하나 또는 둘 다를 무시하는 경우 이러한 변경 사항을 반영하도록 업데이트해야합니다.

<a name="support"></a>
### Support

<a name="string-and-array-helpers"></a>
#### 헬퍼보다 String과 Array Classes 선호

**영향 가능성 : 보통**

모든 `array_ *`와 `str_ *` 글로벌 헬퍼는 [더 이상 사용되지 않습니다](https://github.com/laravel/framework/pull/26898). `Illuminate\Support\Arr` 및 `Illuminate\Support\Str` 메소드를 직접 사용해야합니다.

이 변경의 영향은 전역 배열 및 문자열 함수가 새로운 헬퍼인 [laravel/helpers](https://github.com/laravel/helpers) 패키지로 이동했기 때문에 `보통`으로 표시되었습니다.

<a name="deferred-service-providers"></a>
#### 지연된 서비스 프로바이더

**영향 가능성 : 보통**

서비스 프로바이더의 `defer` boolean 프로퍼티는 프로바이더가 연기-defer 되어야 하는 지를 표현하기 위해서 사용했었지만 [deprecated 되었습니다](https://github.com/laravel/framework/pull/27067). 서비스 프로바이더를 연기-defer 할 것으로 표시하려면 `Illuminate\Contracts\Support\DeferrableProvider` contract을 구현해야합니다.

#### 읽기전용 `env` 헬퍼

**영향 가능성 : 낮음**

이전에는 `env` 헬퍼가 런타임에 변경된 환경 변수에서 값을 검색 할 수있었습니다. Laravel 5.8에서는 `env` 헬퍼가 환경 변수를 불변으로 취급합니다. 런타임에 환경 변수를 변경하려면 `config` 헬퍼를 사용하여 검색 할 수있는 설정 값을 사용하는 것을 고려하십시오 :

이전 동작:

    dump(env('APP_ENV')); // local

    putenv('APP_ENV=staging');

    dump(env('APP_ENV')); // staging

신규 동작:

    dump(env('APP_ENV')); // local

    putenv('APP_ENV=staging');

    dump(env('APP_ENV')); // local

<a name="testing"></a>
### 테스팅

#### `setUp` 과 `tearDown` 메소드

이제 `setUp`과 `tearDown` 메소드는 void 리턴 타입을 필요로합니다 :

    protected function setUp(): void
    protected function tearDown(): void

#### PHPUnit 8

**영향 가능성 : 선택**

기본적으로 Laravel 5.8은 PHPUnit 7을 사용합니다. 그러나 선택적으로 PHP> = 7.2가 필요한 PHPUnit 8로 업그레이드 할 수 있습니다. 추가적으로 [PHPUnit 8 릴리스 발표](https://phpunit.de/announcements/phpunit-8.html)의 전체 변경 사항을 읽어보십시오.

<a name="validation"></a>
### Validation

#### `Validator` Contract

**영향 가능성 : 매우 낮음**

`validated` 메소드가 [`Illuminate\Contracts\Validation\Validator` contract에 추가되었습니다](https://github.com/laravel/framework/pull/26419). :

    /**
     * Get the attributes and values that were validated.
     *
     * @return array
     */
    public function validated();

이 인터페이스를 구현하는 경우 이 메소드를 구현에 추가해야합니다.

#### `ValidatesAttributes` Trait

**영향 가능성 : 매우 낮음**

`Illuminate\Validation\Concerns\ValidatesAttributes` trait의 `parseTable`, `getQueryColumn` 및 `requireParameterCount` 메소드가 `protected`에서 `public`으로 변경되었습니다.

#### `DatabasePresenceVerifier` 클래스

**영향 가능성 : 매우 낮음**

`Illuminate\Validation\DatabasePresenceVerifier` 클래스의 `table` 메소드가 `protected`에서 `public`으로 변경되었습니다.

#### `Validator` 클래스

**영향 가능성 : 매우 낮음**

`Illuminate\Validation\Validator` 클래스의 `getPresenceVerifierFor` 메소드가 `protected`에서 `public`으로 [변경되었습니다](https://github.com/laravel/framework/pull/26717).

#### Email 검증

**영향 가능성 : 매우 낮음**

이메일 검증 규정은 이제 이메일이 [RFC5630](https://tools.ietf.org/html/rfc6530)을 준수하는지 확인하며, 검증 로직을 SwiftMailer가 사용하는 로직과 일치하도록 만들었습니다. Laravel `5.7`에서는 `email` 규칙은 이메일이 [RFC822](https://tools.ietf.org/html/rfc822)와 호환된다는 것을 확인했습니다.

따라서 Laravel 5.8을 사용할 때 이전에 잘못되었다고 잘못 판단 되었던 이메일이 이제는 유효한 것이라고 판별합니다 (예 :`hej@bär.se`). 일반적으로 이것은 버그 수정으로 봐야하지만 주의해야하는 변경사항으로 분류합니다. [이 변경과 관련하여 문제가 발생하면 알려주십시오.](https://github.com/laravel/framework/pull/26503).

<a name="view"></a>
### 뷰

#### `getData` 메소드

**영향 가능성 : 매우 낮음**

`getData` 메소드는 [`Illuminate\Contracts\View\View` 계약에 추가되었습니다](https://github.com/laravel/framework/pull/26754). 이 인터페이스를 구현하는 경우 이 메소드를 구현에 추가해야합니다.

<a name="notifications"></a>
### 알림

<a name="nexmo-slack-notification-channels"></a>
#### Nexmo / Slack Notification Channels

**영향 가능성 : 높음**

Nexmo 및 Slack Notification 채널은 자체 패키지로 분리되었습니다. 애플리케이션에서 이러한 채널을 사용하려면 다음 패키지가 필요합니다.

    composer require laravel/nexmo-notification-channel
    composer require laravel/slack-notification-channel

<a name="miscellaneous"></a>
### 기타

또한 `laravel/laravel` [GitHub 저장소](https://github.com/laravel/laravel)에서 변경 사항을 확인하는 것이 좋습니다. 이러한 변경 사항이 많이 필요하지는 않지만 이러한 파일을 애플리케이션과 동기화 된 상태로 유지할 수 있습니다. 이러한 변경 사항 중 일부는 이 업그레이드 가이드에서 다루지만 설정 파일이나 설명의 변경 사항은 변경되지 않습니다. [GitHub 비교 도구](https://github.com/laravel/laravel/compare/5.7...master)를 사용하여 변경 사항을 쉽게보고, 중요한 업데이트를 선택할 수 있습니다.
