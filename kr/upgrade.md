# Upgrade Guide
# 업그레이드 가이드

- [Upgrading To 5.8.0 From 5.7](#upgrade-5.8.0)
- [5.7에서 5.8.0 으로 업그레이드 하기](#upgrade-5.8.0)

<a name="high-impact-changes"></a>
## High Impact Changes
## 매우 중요한 변경사항

- [Cache TTL In Seconds](#cache-ttl-in-seconds)
- [Cache Lock Safety Improvements](#cache-lock-safety-improvements)
- [Markdown File Directory Change](#markdown-file-directory-change)
- [Nexmo / Slack Notification Channels](#nexmo-slack-notification-channels)
- [New Default Password Length](#new-default-password-length)

- [초단위의 Cache TTL](#cache-ttl-in-seconds)
- [Cache Lock 안정성 개선](#cache-lock-safety-improvements)
- [Markdown File 디렉토리 변경](#markdown-file-directory-change)
- [Nexmo / Slack Notification Channels](#nexmo-slack-notification-channels)
- [새로운 기본 패스워드 길이](#new-default-password-length)


<a name="medium-impact-changes"></a>
## Medium Impact Changes
## 중요한 변경사항

- [Container Generators & Tagged Services](#container-generators)
- [SQLite Version Constraints](#sqlite)
- [Prefer String And Array Classes Over Helpers](#string-and-array-helpers)
- [Deferred Service Providers](#deferred-service-providers)
- [PSR-16 Conformity](#psr-16-conformity)
- [Model Names Ending With Irregular Plurals](#model-names-ending-with-irregular-plurals)
- [Custom Pivot Models With Incrementing IDs](#custom-pivot-models-with-incrementing-ids)
- [Pheanstalk 4.0](#pheanstalk-4)

- [컨테이너 제네레이터와 태그된 서비스들](#container-generators)
- [SQLite 버전 제약 조건](#sqlite)
- [헬퍼보다 String과 Array Classes 선호](#string-and-array-helpers)
- [지연된 서비스 프로바이더](#deferred-service-providers)
- [PSR-16 적합성](#psr-16-conformity)
- [불규칙한 복수형으로 끝나는 모델 이름](#model-names-ending-with-irregular-plurals)
- [ID가 증가하는 사용자 지정 피벗 모델](#custom-pivot-models-with-incrementing-ids)
- [Pheanstalk 4.0](#pheanstalk-4)

<a name="upgrade-5.8.0"></a>
## Upgrading To 5.8.0 From 5.7
## 5.7에서 5.8.0 으로 업그레이드 하기

#### Estimated Upgrade Time: 1 Hour
#### 예상 업그레이드 시간 : 1 시간

> {note} We attempt to document every possible breaking change. Since some of these breaking changes are in obscure parts of the framework only a portion of these changes may actually affect your application.

> {note} 가능한 모든 변경 내용을 기록하려고 했습니만, 변경 사항 중 일부는 프레임워크의 명확하지 않은 부분에서 이루어 지기 때문에 이중 일부가 실제 애플리케이션에 영향을 끼칠 수도 있습니다.

<a name="updating-dependencies"></a>
### Updating Dependencies
### 의존성 업데이트

Update your `laravel/framework` dependency to `5.8.*` in your `composer.json` file.

`composer.json` 파일에 있는 `laravel/framework` 의존성을 `5.8.*`으로 업데이트합니다.

Next, examine any 3rd party packages consumed by your application and verify you are using the proper version for Laravel 5.8 support.

그리고 애플리케이션에서 사용하는 써드파티 패키지를 확인하고 라라벨 5.8를 지원하는 적절한 버전을 사용하고 있는지 확인하십시오.

<a name="the-application-contract"></a>
### The `Application` Contract
### `Application` Contract

#### The `environment` Method
#### `environment` 메소드

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The `environment` method signature of the `Illuminate\Contracts\Foundation\Application` contract [has changed](https://github.com/laravel/framework/pull/26296). If you are implementing this contract in your application, you should update the method signature:

`Illuminate\Contracts\Foundation\Application` contract의 `environment` 메소드의 사용법이 [변경되었습니다](https://github.com/laravel/framework/pull/26296). 애플리케이션에서 이 contract를 구현하는 경우 메소드를 수정해야합니다.

    /**
     * Get or check the current application environment.
     *
     * @param  string|array  $environments
     * @return string|bool
     */
    public function environment(...$environments);

#### Added Methods
#### 추가된 메소드들

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The `bootstrapPath`, `configPath`, `databasePath`, `environmentPath`, `resourcePath`, `storagePath`, `resolveProvider`, `bootstrapWith`, `configurationIsCached`, `detectEnvironment`, `environmentFile`, `environmentFilePath`, `getCachedConfigPath`, `getCachedRoutesPath`, `getLocale`, `getNamespace`, `getProviders`, `hasBeenBootstrapped`, `loadDeferredProviders`, `loadEnvironmentFrom`, `routesAreCached`, `setLocale`, `shouldSkipMiddleware` and `terminate`  methods [were added to the `Illuminate\Contracts\Foundation\Application` contract](https://github.com/laravel/framework/pull/26477).

`bootstrapPath`, `configPath`, `databasePath`, `environmentPath`, `resourcePath`, `storagePath`, `resolveProvider`, `bootstrapWith`, `configurationIsCached`, `detectEnvironment`, `environmentFile`, `environmentFilePath`, `getCachedConfigPath`, `getCachedRoutesPath`, `getLocale`, `getNamespace`, `getProviders`, `hasBeenBootstrapped`, `loadDeferredProviders`, `loadEnvironmentFrom`, `routesAreCached`, `setLocale`, `shouldSkipMiddleware` 그리고 `terminate`  메소드가 [`Illuminate\Contracts\Foundation\Application` contract에 추가되었습니다](https://github.com/laravel/framework/pull/26477).

In the very unlikely event you are implementing this interface, you should add these methods to your implementation.

매우 드물지만 이 인터페이스를 구현하는 경우 이 메소드를 구현체에 추가해야합니다.

<a name="authentication"></a>
### Authentication
### 인증

#### Password Reset Notification Route Parameter
#### 비밀번호 재설정 알림 라우트의 파라메터

**Likelihood Of Impact: Low**
**영향 가능성 : 낮음**

When a user requests a link to reset their password, Laravel generates the URL using the `route` helper to create a URL to the `password.reset` named route. When using Laravel 5.7, the token is passed to the `route` helper without an explicit name, like so:

사용자가 비밀번호 재설정을 위한 링크를 요청하면, Laravel은 `route` 헬퍼를 사용하여 `password.reset` 라우트에 대한 URL을 생성합니다. Laravel 5.7을 사용할 때 토큰은 다음과 같이 명시적 이름없이 `route`헬퍼로 전달합니다

    route('password.reset', $token);

When using Laravel 5.8, the token is passed to the `route` helper as an explicit parameter:

Laravel 5.8을 사용할 때는 토큰은 명시적 파라메터로 `route`헬퍼로 전달합니다.

    route('password.reset', ['token' => $token]);

Therefore, if you are defining your own `password.reset` route, you should ensure that it contains a `{token}` parameter in its URI.

그러므로 자신만의 `password.reset` 라우트를 정의할 경우 URI에 `{token}` 파라메터가 들어 있는지 확인해야합니다.

<a name="new-default-password-length"></a>
#### New Default Password Length
#### 새로운 기본 비밀번호 길이

**Likelihood Of Impact: High**
**영향 가능성 : 높음**

The required password length when choosing or resetting a password was [changed to eight characters](https://github.com/laravel/framework/pull/25957). You should update any validation rules or logic within your application to match this new eight character default.

비밀번호를 선택하거나 재설정 할 때 필요한 비밀번호 길이는 [8글자로 변경되었습니다](https://github.com/laravel/framework/pull/25957). 이 새로운 기본값인 8글자와 일치하도록 애플리케이션 내의 모든 유효성 검증 룰 또는 로직을 변경해야합니다.

If you need to preserve the previous six character length or a different length, you may extend the `Illuminate\Auth\Passwords\PasswordBroker` class and overwrite the `validatePasswordWithDefaults` method with custom logic.

이전 6글자 길이 또는 다른 길이를 보존해야한다면 `Illuminate\Auth\Passwords\PasswordBroker` 클래스를 확장하고 `validatePasswordWithDefaults` 메소드를 커스텀 로직으로 덮어 쓸 수 있습니다.

<a name="cache"></a>
### Cache
### 캐시

<a name="cache-ttl-in-seconds"></a>
#### TTL in seconds
#### 초 단위의 TTL

**Likelihood Of Impact: Very High**
**영향 가능성 : 매우 높음**

In order to allow a more granular expiration time when storing items, the cache item time-to-live has changed from minutes to seconds. The `put`, `putMany`, `add`, `remember` and `setDefaultCacheTime` methods of the `Illuminate\Cache\Repository` class and its extended classes, as well as the `put` method of each cache store were updated with this changed behavior. See [the related PR](https://github.com/laravel/framework/pull/27276) for more info.

항목을 저장할 때, 보다 세분화 된 만료시간을 사용하기 위해 캐시의 만료시간이 분단위에서 초단위로 변경되었습니다. `Illuminate\Cache\Repository` 클래스와 확장 클래스의 `put`,`putMany`,`add`,`remember`,`setDefaultCacheTime` 메소드, 각 캐시 스토어의 `put` 메소드와 동작들이 변경되었습니다. 자세한 내용은 [관련 PR](https://github.com/laravel/framework/pull/27276)을 참조하십시오.

If you are passing an integer to any of these methods, you should update your code to ensure you are now passing the number of seconds you wish the item to remain in the cache. Alternatively, you may pass a `DateTime` instance indicating when the item should expire:

이 메소드들에게 정수를 전달하는 경우, 캐시에 보관할 시간(초단위)을 전달하도록 코드를 업데이트해야합니다. 또는 항목이 만료되어야하는 시점를 나타내는 `DateTime` 인스턴스를 전달할 수도 있습니다.

    // Laravel 5.7 - Store item for 30 minutes...
    Cache::put('foo', 'bar', 30);

    // Laravel 5.8 - Store item for 30 seconds...
    Cache::put('foo', 'bar', 30);

    // Laravel 5.7 / 5.8 - Store item for 30 seconds...
    Cache::put('foo', 'bar', now()->addSeconds(30));

> {tip} This change makes the Laravel cache system fully compliant with the [PSR-16 caching library standard](https://www.php-fig.org/psr/psr-16/).

> {tip} 이 변경으로 Laravel 캐시 시스템은 [PSR-16 캐싱 라이브러리 표준](https://www.php-fig.org/psr/psr-16/)을 완벽하게 준수합니다.

<a name="psr-16-conformity"></a>
#### PSR-16 Conformity
#### PSR-16 적합성

**Likelihood Of Impact: Medium**
**영향 가능성 : 보통**

In addition to [the return value changes from below](#the-repository-and-store-contracts), the TTL argument of the `put`, `putMany` and `add` method's of the `Illuminate\Cache\Repository` class was updated to conform better with the PSR-16 spec. The new behavior provides a default of `null` so a call without specifying a TTL will result in storing the cache item forever. Additionally, storing cache items with a TTL of 0 or lower will remove items from the cache. See [the related PR](https://github.com/laravel/framework/pull/27217) for more info.

[하단의 반환 값이 변경됨](#the-repository-and-store-contracts) 외에도 `put`, `putMany` 및 `add` 메소드의 TTL 인수는 `Illuminate\Cache\Repository` 클래스가 PSR-16 스펙과 보다 잘 일치하도록 업데이트되었습니다. 새로운 동작은 기본값이 `null`이므로 TTL을 지정하지 않고 호출하면 캐시 항목이 영원히 저장됩니다. 또한 캐시 항목을 TTL 0 이하로 저장하면 캐시에서 항목이 제거됩니다. 자세한 내용은 [관련 PR](https://github.com/laravel/framework/pull/27217)을 참조하십시오.

The `KeyWritten` event [was also updated](https://github.com/laravel/framework/pull/27265) with these changes.

이 변경 사항으로 `KeyWritten` 이벤트 [또한 업데이트되었습니다](https://github.com/laravel/framework/pull/27265).

<a name="cache-lock-safety-improvements"></a>
#### Lock Safety Improvements
#### Lock 안정성 개선

**Likelihood Of Impact: High**
**영향 가능성 : 높음**

In Laravel 5.7 and prior versions of Laravel, the "atomic lock" feature provided by some cache drivers could have unexpected behavior leading to the early release of locks.

Laravel 5.7 및 이전 버전의 Laravel에서는 일부 캐시 드라이버가 제공하는 "원자 잠금"기능이 예상치 못한 동작을 하여 초기 잠금 해제로 이어질 수 있었습니다.

For example: **client A** acquires lock `foo` with a 10 second expiration. **Client A** actually takes 20 seconds to finish its task. The lock is released automatically by the cache system 10 seconds into **Client A's** processing time. **Client B** acquires lock `foo`. **Client A** finally finishes its task and releases lock `foo`, inadvertently releasing **Client B's** hold on the lock. **Client C** is now able to acquire the lock.

예를 들면 다음과 같습니다. **Client A**는 만료가 10초인 Lock `foo`를 획득합니다. 이때 **Client A**는 실제로 작업을 완료하는 데 20초가 걸린다고 가정합니다. Lock `foo`는 캐시 시스템에 의해 **Client A**의 작업이 처리되는 도중 10초 동안 자동으로 해제됩니다. **Client B**가 이때 Lock `foo`를 획득합니다. **Client A**의 작업이 끝나고 Lock `foo`를 해제하여 의도치 않게 **Client B**가 가진 Lock을 해제됩니다. **Client C**는 이제 Lock을 획득 할 수 있게 되었습니다.
(역자주: 원문이 너무 딱딱하다고 느껴져 임의로 문장을 다듬었습니다. 만약 표현이 이상하다고 느껴지신다면 원문을 보신 후 PR부탁드립니다)

In order to mitigate this scenario, locks are now generated with an embedded "scope token" which allows the framework to ensure that, under normal circumstances, only the proper owner of a lock can release a lock.

이 시나리오를 완화하기 위해 프레임워크가 정상적인 상황에서 잠금의 올바른 소유자만 잠금을 해제 할 수 있도록하는 "범위 토큰"이 포함 된 잠금이 생성됩니다.

If you are using the `Cache::lock()->get(Closure)` method of interacting with locks, no changes are required:

만약 Lock과 상호 작용하는 `Cache::lock()->get(Closure)`메소드를 사용하고 있다면, 아무런 변경이 필요 없습니다.

    Cache::lock('foo', 10)->get(function () {
        // Lock will be released safely automatically...
    });

However, if you are manually calling `Cache::lock()->release()`, you must update your code to maintain an instance of the lock. Then, after you are done performing your task, you may call the `release` method on **the same lock instance**. For example:

그러나 수동으로 `Cache::lock()->release()`를 호출하는 경우 Lock 인스턴스를 유지하도록 코드를 업데이트해야합니다. 그런 다음 작업 수행을 마친 후에 **동일한 Lock 인스턴스**에 대해`release` 메소드를 호출 할 수 있습니다. 예 :

    if (($lock = Cache::lock('foo', 10))->get()) {
        // Perform task...

        $lock->release();
    }

Sometimes, you may wish to acquire a lock in one process and release it in another process. For example, you may acquire a lock during a web request and wish to release the lock at the end of a queued job that is triggered by that request. In this scenario, you should pass the lock's scoped "owner token" to the queued job so that the job can re-instantiate the lock using the given token:

어쩔땐 한 프로세스에서 잠금을 획득하고 다른 프로세스에서 잠금을 해제하고자 할 수 있습니다. 예를 들어, 웹 요청 중에 Lock을 획득하고 해당 요청으로 인해 대기중인 작업이 끝날 때 Lock을 해제하고자 할 수 있습니다. 이 시나리오에서는 Lock 범위가 지정된 "소유자 토큰"을 대기중인 작업에 전달하여 작업이 주어진 토큰을 사용하여 Lock을 다시 인스턴스화 할 수 있도록해야합니다.

    // Within Controller...
    $podcast = Podcast::find(1);

    if (($lock = Cache::lock('foo', 120))->get()) {
        ProcessPodcast::dispatch($podcast, $lock->owner());
    }

    // Within ProcessPodcast Job...
    Cache::restoreLock('foo', $this->owner)->release();

If you would like to release a lock without respecting its current owner, you may use the `forceRelease` method:

만약 현재 소유자를 무시하고 잠금을 해제하려면 `forceRelease` 메소드를 사용할 수 있습니다 :

    Cache::lock('foo')->forceRelease();

<a name="the-repository-and-store-contracts"></a>
#### The `Repository` and `Store` Contracts
####`Repository`와 `Store` Contracts

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

In order to be fully compliant with `PSR-16` the return values of the `put` and `forever` methods of the `Illuminate\Contracts\Cache\Repository` contract and the return values of the `put`, `putMany` and `forever` methods of the `Illuminate\Contracts\Cache\Store` contract [have been changed](https://github.com/laravel/framework/pull/26726) from `void` to `bool`.

`PSR-16`을 준수하기 위해 `Illuminate\Contracts\Cache\Repository` contract의 `put` 및 `forever` 메소드의 반환 값과 `put`, `putMany` `Illuminate\Contracts\Cache\Store` contract의 `forever`메소드가 `void`에서 `bool`으로 [변경되었습니다](https://github.com/laravel/framework/pull/26726).

<a name="collections"></a>
### Collections
### 컬렉션

#### The `firstWhere` Method

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The `firstWhere` method signature [has changed](https://github.com/laravel/framework/pull/26261) to match the `where` method's signature. If you are overriding this method, you should update the method signature to match its parent:

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
### Console
### 콘솔

#### The `Kernel` Contract
#### `Kernel` Contract

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The `terminate` method [has been added to the `Illuminate\Contracts\Console\Kernel` contract](https://github.com/laravel/framework/pull/26393). If you are implementing this interface, you should add this method to your implementation.

`terminate` 메소드가 [`Illuminate\Contracts\Console\Kernel` 계약에 추가되었습니다](https://github.com/laravel/framework/pull/26393). 이 인터페이스를 구현하는 경우이 메소드를 구현에 추가해야합니다.

<a name="container"></a>
### Container
### 컨테이너

<a name="container-generators"></a>
#### Generators & Tagged Services
#### 제네레이터와 태그된 서비스들

**Likelihood Of Impact: Medium**
**영향 가능성 : 보통**

The container's `tagged` method now utilizes PHP generators to lazy-instantiate the services with a given tag. This provides a performance improvement if you are not utilizing every tagged service.

컨테이너의 `tagged` 메소드는 PHP의 제네레이터를 사용하여 주어진 태그로 서비스를 지연시켜서 인스턴스화합니다. 모든 태그 된 서비스를 사용하지 않으면 성능이 향상됩니다.

Because of this change, the `tagged` method now returns an `iterable` instead of an `array`. If you are type-hinting the return value of this method, you should ensure that your type-hint is changed to `iterable`.

이 변경 때문에 `tagged` 메소드는 이제 `array` 대신에 `iterable`을 리턴합니다. 이 메소드의 반환 값을 타입 힌팅하는 경우, 타입 힌트가 `iterable`로 변경되었는지 확인해야합니다.

In addition, it is no longer possible to directly access a tagged service by its array offset value, such as `$container->tagged('foo')[0]`.

또한 `$container->tagged('foo')[0]`과 같은 배열 오프셋 값을 사용하여 태그가 있는 서비스에 직접 액세스하는 것은 더 이상 불가능합니다.

#### The `resolve` Method
#### `resolve` 메소드

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The `resolve` method [now accepts](https://github.com/laravel/framework/pull/27066) a new boolean parameter which indicates whether events (resolving callbacks) should be raised/executed during the instantiation of an object. If you are overriding this method, you should update the method signature to match its parent.

`resolve` 메소드는 이제 객체의 인스턴스 생성 중에 이벤트(resolving callbacks)가 발생 / 실행되어야 하는지를 지정는 새로운 부울-boolean 파라미터를 [받습니다](https://github.com/laravel/framework/pull/27066). 이 메서드를 재정의하는 경우 부모와 일치하도록 메서드 사용법을 수정해야합니다.

#### The `addContextualBinding` Method
#### `addContextualBinding` 메소드

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The `addContextualBinding` method [was added to the `Illuminate\Contracts\Container\Container` contract](https://github.com/laravel/framework/pull/26551). If you are implementing this interface, you should add this method to your implementation.

`addContextualBinding`메소드가 [`Illuminate\Contracts\Container\Container` contract에 추가되었습니다](https://github.com/laravel/framework/pull/26551). 이 인터페이스를 구현하는 경우이 메소드를 구현에 추가해야합니다.

#### The `tagged` Method
#### `tagged` 메소드

**Likelihood Of Impact: Low**
**영향 가능성 : 낮음**

The `tagged` method signature [has been changed](https://github.com/laravel/framework/pull/26953) and it now returns an `iterable` instead of an `array`. If you have type-hinted in your code some parameter which gets the return value of this method with `array`, you should modify the type-hint to `iterable`.

`tagged` 메소드 사용법이 [변경되었습니다](https://github.com/laravel/framework/pull/26953). 이제 `array` 대신에 `iterable`을 리턴합니다. 코드에서 이 메소드의 반환 값을 `array`로 얻는 매개 변수 힌트가 있다면 타입 힌트를 `iterable`으로 수정해야합니다.

#### The `flush` Method
#### `flush` 메소드

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The `flush` method [was added to the `Illuminate\Contracts\Container\Container` contract](https://github.com/laravel/framework/pull/26477). If you are implementing this interface, you should add this method to your implementation.

`flush` 메소드가 [`Illuminate\Contracts\Container\Container` contract에 추가되었습니다](https://github.com/laravel/framework/pull/26477). 이 인터페이스를 구현하는 경우이 메소드를 구현에 추가해야합니다.

<a name="database"></a>
### Database
### Database

#### Unquoted MySQL JSON Values
#### 따옴표가 없는 MySQL JSON 값

**Likelihood Of Impact: Low**
**영향 가능성 : 낮음**

The query builder will now return unquoted JSON values when using MySQL and MariaDB. This behavior is consistent with the other supported databases:

쿼리 빌더는 MySQL과 MariaDB를 사용할 때 따옴표가 없는 JSON 값을 반환합니다. 이 동작은 지원하는 다른 데이터베이스와 일치합니다.

    $value = DB::table('users')->value('options->language');

    dump($value);

    // Laravel 5.7...
    '"en"'

    // Laravel 5.8...
    'en'

As a result, the `->>` operator is no longer supported or necessary.

결과적으로 `->>` 연산자는 더 이상 지원되거나 필요하지 않습니다.

<a name="sqlite"></a>
#### SQLite
#### SQLite

**Likelihood Of Impact: Medium**
**영향 가능성 : 보통**

As of Laravel 5.8 the [oldest supported SQLite version](https://github.com/laravel/framework/pull/25995) is SQLite 3.7.11. If you are using an older SQLite version, you should update it (SQLite 3.8.8+ is recommended).

Laravel 5.8부터 [지원하는 가장 오래된 SQLite 버전](https://github.com/laravel/framework/pull/25995)은 SQLite 3.7.11입니다. 이전 SQLite 버전을 사용하는 경우 SQLite 버전을 업데이트해야합니다 (SQLite 3.8.8 이상 권장).

#### Migrations & `bigIncrements`
#### 마이그레이션과 `bigIncrements`

**Likelihood Of Impact: None**
**영향 가능성 : 보통**

[As of Laravel 5.8](https://github.com/laravel/framework/pull/26472), migration stubs use the `bigIncrements` method on ID columns by default. Previously, ID columns were created using the `increments` method.

[Laravel 5.8 현재](https://github.com/laravel/framework/pull/26472), 마이그레이션 스텁은 기본적으로 ID 열에 대해 `bigIncrements` 메소드를 사용합니다. 이전에는 `incrementments` 메소드를 사용하여 ID 열을 만들었습니다.

This will not affect any existing code in your project; however, be aware that foreign key columns must be of the same type. Therefore, a column created using the `increments` method can not reference a column created using the `bigIncrements` method.

프로젝트의 기존 코드에는 영향을주지 않습니다. 그러나 외래 키 열은 동일한 유형이어야 하므로 `incrementments` 메소드를 사용하여 생성 된 컬럼은 `bigIncrements` 메소드를 사용하여 생성 된 컬럼을 참조 할 수 없습니다.

<a name="eloquent"></a>
### Eloquent
### Eloquent

<a name="model-names-ending-with-irregular-plurals"></a>
#### Model Names Ending With Irregular Plurals
#### 불규칙한 복수형으로 끝나는 모델 이름

**Likelihood of Impact: Medium**
**영향 가능성 : 보통**

As of Laravel 5.8, multi-word model names ending in a word with an irregular plural [are now correctly pluralized](https://github.com/laravel/framework/pull/26421).

Laravel 5.8부터는 불규칙 복수형 단어로 끝나는 다중 단어 모델 이름이 [올바르게 복수화되었습니다](https://github.com/laravel/framework/pull/26421).

    // Laravel 5.7...
    App\Feedback.php -> feedback (correctly pluralized)
    App\UserFeedback.php -> user_feedbacks (incorrectly pluralized)

    // Laravel 5.8
    App\Feedback.php -> feedback (correctly pluralized)
    App\UserFeedback.php -> user_feedback (correctly pluralized)

If you have a model that was incorrectly pluralized, you may continue using the old table name by defining a `$table` property on your model:

잘못 복수화 된 모델을 가지고 있다면 모델에 `$table` 속성을 정의하여 이전 테이블 이름을 계속 사용할 수 있습니다 :

    /**
     * The table associated with the model.
     *
     * @var string
     */
    protected $table = 'user_feedbacks';

<a name="custom-pivot-models-with-incrementing-ids"></a>
#### Custom Pivot Models With Incrementing IDs
#### ID가 증가하는 사용자 지정 피벗 모델

If you have defined a many-to-many relationship that uses a custom pivot model, and that pivot model has an auto-incrementing primary key, you should ensure your custom pivot model class defines an `incrementing` property that is set to `true`:

커스텀 피벗 모델을 사용하는 다 대다 관계를 정의하고, 그 피벗 모델이 자동 증가하는 기본 키를 가지고 있다면, 커스텀 피벗 모델 클래스가 `incrementing` 속성을 `true`로 정의하는지 확인해야합니다.

    /**
     * Indicates if the IDs are auto-incrementing.
     *
     * @var bool
     */
    public $incrementing = true;

#### The `loadCount` Method
#### `loadCount` 메소드

**Likelihood Of Impact: Low**
**영향 가능성 : 낮음**

A `loadCount` method has been added to the base `Illuminate\Database\Eloquent\Model` class. If your application also defines a `loadCount` method, it may conflict with Eloquent's definition.

`Illuminate\Database\Eloquent\Model` 클래스에 `loadCount` 메소드가 추가되었습니다. 애플리케이션이 `loadCount` 메소드도 정의한다면, Eloquent의 정의와 충돌 할 수 있습니다.

#### The `originalIsEquivalent` Method
#### `originalIsEquivalent` 메소드

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The `originalIsEquivalent` method of the `Illuminate\Database\Eloquent\Concerns\HasAttributes` trait [has been changed](https://github.com/laravel/framework/pull/26391) from `protected` to `public`.

`Illuminate\Database\Eloquent\Concerns\HasAttributes` 특성의 `originalIsEquivalent` 메소드가 `protected`에서 `public`으로 [변경되었습니다](https://github.com/laravel/framework/pull/26391).

#### Automatic Soft-Deleted Casting Of `deleted_at` Property
####`deleted_at` 속성의 자동 소프트삭제 된 형태 변환

**Likelihood Of Impact: Low**
**영향 가능성 : 낮음**

The `deleted_at` property [will now be automatically casted](https://github.com/laravel/framework/pull/26985) to a `Carbon` instance when your Eloquent model uses the `Illuminate\Database\Eloquent\SoftDeletes` trait. You can override this behavior by writing your custom accessor for that property or by manually adding it to the `casts` attribute:

Eloquent 모델이 `Illuminate\Database\Eloquent\SoftDeletes` trait을 사용할 때 `deleted_at` 속성이 `Carbon` 인스턴스로 [이제 자동으로 캐스팅됩니다](https://github.com/laravel/framework/pull/26985). 이 동작을 무시하려면 해당 속성에 대한 사용자 지정 접근자를 작성하거나 수동으로 `casts` 특성에 추가하면됩니다.

    protected $casts = ['deleted_at' => 'string'];

#### BelongsTo `getForeignKey` Method
#### `getForeignKey` 메소드

**Likelihood Of Impact: Low**
**영향 가능성 : 낮음**

The `getForeignKey` and `getQualifiedForeignKey` methods of the `BelongsTo` relationship have been renamed to `getForeignKeyName` and `getQualifiedForeignKeyName` respectively, making the method names consistent with the other relationships offered by Laravel.

`BelongsTo` 관계의 `getForeignKey` 메소드와 `getQualifiedForeignKey` 메소드는 각각 `getForeignKeyName`과 `getQualifiedForeignKeyName`으로 이름이 바뀌 었습니다. 메소드 이름은 Laravel이 제공하는 다른 관계와 일치합니다.

### Environment
### Environment

**Likelihood Of Impact: Low**
**영향 가능성 : 낮음**

The [phpdotenv](https://github.com/vlucas/phpdotenv) package that is used to parse `.env` files has released a new major version, which may impact the results returned from the `env` helper. Specifically, the `#` character in an unquoted value will now be considered a comment instead of part of the value:

`.env` 파일을 파싱하는 데 사용되는 [phpdotenv](https://github.com/vlucas/phpdotenv) 패키지는 새로운 메이저 버전을 발표했는데 이것은 `env` 헬퍼로부터 반환 된 결과에 영향을 줄 수 있습니다. 특히, 따옴표로 묶이지 않은 값의 `#`문자는 이제 값의 일부가 아닌 주석으로 간주됩니다.

Previous behavior:

이전 동작:

    ENV_VALUE=foo#bar

    env('ENV_VALUE'); // foo#bar

New behavior:

신규 동작:

    ENV_VALUE=foo#bar
    env('ENV_VALUE'); // foo

To preserve the previous behavior, you may wrap the environment values in quotes:

이전 동작을 유지하려면 환경 값을 따옴표로 묶어야합니다.

    ENV_VALUE="foo#bar"

    env('ENV_VALUE'); // foo#bar

For more information, please refer to the [phpdotenv upgrade guide](https://github.com/vlucas/phpdotenv/blob/master/UPGRADING.md).

자세한 내용은 [phpdotenv 업그레이드 안내서](https://github.com/vlucas/phpdotenv/blob/master/UPGRADING.md)를 참조하십시오.

<a name="events"></a>
### Events
### Events

#### The `fire` Method
#### `fire` 메소드

**Likelihood Of Impact: Low**
**영향 가능성 : 낮음**

The `fire` method (which was deprecated in Laravel 5.4) of the `Illuminate\Events\Dispatcher` class [has been removed](https://github.com/laravel/framework/pull/26392).
You should use the `dispatch` method instead.

`Illuminate\Events\Dispatcher` 클래스의 `fire` 메소드 (Laravel 5.4부터 deprecated되었습니다)가 [삭제되었습니다](https://github.com/laravel/framework/pull/26392). 대신에 `dispatch` 메소드를 사용해야합니다.

<a name="exception-handling"></a>
### Exception Handling
### 예외 처리

#### The `ExceptionHandler` Contract
#### `ExceptionHandler` Contract

**Likelihood Of Impact: Low**
**영향 가능성 : 낮음**

The `shouldReport` method [has been added to the `Illuminate\Contracts\Debug\ExceptionHandler` contract](https://github.com/laravel/framework/pull/26193). If you are implementing this interface, you should add this method to your implementation.

`shouldReport` 메소드는 [`Illuminate\Contracts\Debug\ExceptionHandler` contract에 추가되었습니다](https://github.com/laravel/framework/pull/26193). 이 인터페이스를 구현하는 경우 이 메소드를 구현에 추가해야합니다.

#### The `renderHttpException` Method
#### `renderHttpException` 메소드

**Likelihood Of Impact: Low**
**영향 가능성 : 낮음**

The `renderHttpException` method signature of the `Illuminate\Foundation\Exceptions\Handler` class [has changed](https://github.com/laravel/framework/pull/25975). If you are overriding this method in your exception handler, you should update the method signature to match its parent:

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
### Mail

<a name="markdown-file-directory-change"></a>
<a name="markdown-file-directory-change"></a>
### Markdown File Directory Change
### Markdown 파일 디렉토리 변경

**Likelihood Of Impact: High**
**영향 가능성 : 높음**

If you have published Laravel's Markdown mail components using the `vendor:publish` command, you should rename the `/resources/views/vendor/mail/markdown` directory to `text`.

`vendor:publish` 명령을 사용하여 Laravel의 Markdown 메일 컴포넌트를 퍼블리싱 한 경우`/resources/views/vendor/mail/markdown` 디렉토리의 이름을 `text`로 변경해야합니다.

In addition, the `markdownComponentPaths` method [has been renamed](https://github.com/laravel/framework/pull/26938) to `textComponentPaths`. If you are overriding this method, you should update the method name to match its parent.

또한 `markdownComponentPaths` 메소드는 `textComponentPaths`로 [이름이 변경되었습니다](https://github.com/laravel/framework/pull/26938). 이 메소드를 재정의하는 경우 부모와 일치하도록 메서드 이름을 업데이트해야합니다.

#### Method Signature Changes In The `PendingMail` Class
#### `PendingMail` 클래스의 메소드 사용법 변경사항

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The `send`, `sendNow`, `queue`, `later` and `fill` methods of the `Illuminate\Mail\PendingMail` class [have been changed](https://github.com/laravel/framework/pull/26790) to accept an `Illuminate\Contracts\Mail\Mailable` instance instead of `Illuminate\Mail\Mailable`. If you are overriding some of these methods, you should update their signature to match its parent.

`Illuminate\Mail\PendingMail` 클래스의 `send`, `sendNow`, `queue`, `later` 및 `fill` 메소드가 [변경되었습니다](https://github.com/laravel/framework/pull/26790). `Illuminate\Mail\Mailable` 대신 `Illuminate\Contracts\Mail\Mailable` 인스턴스를 허용합니다. 이러한 메소드 중 일부를 재정의하는 경우 부모와 일치하도록 사용법을 수정해야합니다.

<a name="queue"></a>
### Queue
### 큐

<a name="pheanstalk-4"></a>
#### Pheanstalk 4.0
#### Pheanstalk 4.0

**Likelihood Of Impact: Medium**
**영향 가능성 : 보통**

Laravel 5.8 provides support for the `~4.0` release of the Pheanstalk queue library. If you are using Pheanstalk library in your application, please upgrade your library to the `~4.0` release via Composer.

Laravel 5.8은 Pheanstalk 큐 라이브러리의 `~ 4.0` 버전을 지원합니다. 애플리케이션에 Pheanstalk 라이브러리를 사용하고 있다면, Composer를 통해 라이브러리를 `~ 4.0` 버전으로 업그레이드하십시오.

#### The `Job` Contract
#### `Job` Contract

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The `isReleased`, `hasFailed` and `markAsFailed` methods [have been added to the `Illuminate\Contracts\Queue\Job` contract](https://github.com/laravel/framework/pull/26908). If you are implementing this interface, you should add these methods to your implementation.

`isReleased`, `hasFailed` 및 `markAsFailed` 메소드는 [`Illuminate\Contracts\Queue\Job` contract에 추가되었습니다](https://github.com/laravel/framework/pull/26908). 이 인터페이스를 구현하는 경우 이러한 메소드를 구현에 추가해야합니다.

#### The `Job::failed` & `FailingJob` Class
#### `Job::failed`와 `FailingJob` 클래스

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

When a queued job failed in Laravel 5.7, the queue worker executed the `FailingJob::handle` method. In Laravel 5.8, the logic contained in the `FailingJob` class has been moved to a `fail` method directly on the job class itself. Because of this, a `fail` method has been added to the `Illuminate\Contracts\Queue\Job` contract.

Laravel 5.7에서 대기중인 작업이 실패한 경우 큐 워커는 `FailingJob::handle` 메소드를 실행했습니다. Laravel 5.8에서는 `FailingJob` 클래스에 포함 된 로직이 작업 클래스의 `fail` 메소드로 옮겨졌습니다. 이것때문에 `Illuminate\Contracts\Queue\Job` contract에 `fail` 메소드가 추가되었습니다.

The base `Illuminate\Queue\Jobs\Job` class contains the implementation of `fail` and no code changes should be required by typical application code. However, if you are building custom queue driver which utilizes a job class that **does not** extend the base job class offered by Laravel, you should implement the `fail` method manually in your custom job class. You may refer to Laravel's base job class as a reference implementation.

베이스가 되는 `Illuminate\Queue\Jobs\Job` 클래스는 `fail` 구현을 포함하고 있으며 일반적인 애플리케이션 코드에서는 코드 변경이 필요하지 않습니다. 그러나 Laravel이 제공하는 기본 작업 클래스를 확장하지 **않는** 작업 클래스를 사용하여 커스텀 큐 드라이버를 작성하는 경우 커스텀 작업 클래스에서 직접 `fail` 메소드를 구현해야합니다. 이를 위해 Laravel의 기본 작업 클래스를 구현을 참고 할 수 있습니다.

This change allows custom queue drivers to have more control over the job deletion process.

이 변경으로 인해 커스텀 큐 드라이버는 작업 삭제 처리를보다 효율적으로 제어 할 수 있습니다.

#### Redis Blocking Pop
#### Redis Blocking Pop

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

Using the "blocking pop" feature of the Redis queue driver is now safe. Previously, there was a small chance that a queued job could be lost if the Redis server or worker crashed at the same time the job was retrieved. In order to make blocking pops safe, a new Redis list with suffix `:notify` is created for each Laravel queue.

Redis 큐 드라이버의 "블로킹 팝"기능 사용이 이제 안전해졌습니다. 이전에는 작업이 검색된 것과 동시에 Redis 서버 나 워커에서 문제가 발생 할 경우 대기열에 있는 작업이 손실 될 수 있는 작은 가능성이 있었습니다. 블로킹 팝을 안전하게하기 위해 각 Laravel 큐에 `: notify`라는 접미사가 붙은 새로운 Redis리스트가 생성됩니다.

<a name="requests"></a>
### Requests
### Requests

#### The `TransformsRequest` Middleware
#### `TransformsRequest` 미들웨어

**Likelihood Of Impact: Low**
**영향 가능성 : 낮음**

The `transform` method of the `Illuminate\Foundation\Http\Middleware\TransformsRequest` middleware now receives the "fully-qualified" request input key when the input is an array:

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
### Routing
### 라우팅

#### The `UrlGenerator` Contract
#### `UrlGenerator` Contract

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The `previous` method [has been added to the `Illuminate\Contracts\Routing\UrlGenerator` contract](https://github.com/laravel/framework/pull/25616). If you are implementing this interface, you should add this method to your implementation.

`previous` 메소드는 [`Illuminate\Contracts\Routing\UrlGenerator` contract에 추가되었습니다](https://github.com/laravel/framework/pull/25616). 이 인터페이스를 구현하는 경우이 메소드를 구현에 추가해야합니다.

#### The `cachedSchema` Property Of `Illuminate\Routing\UrlGenerator`
####  `Illuminate\Routing\UrlGenerator` 의 `cachedSchema` 프로퍼티

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The `$cachedSchema` property name (which has been deprecated in Laravel `5.7`) of `Illuminate\Routing\UrlGenerator` [has been changed to](https://github.com/laravel/framework/pull/26728) `$cachedScheme`.

`Illuminate\Routing\UrlGenerator`의 `$cachedSchema` 프로퍼티명은 (Laravel`5.7`에서 deprecated되었습니다) `$cachedScheme`로 [변경되었습니다](https://github.com/laravel/framework/pull/26728).

<a name="sessions"></a>
### Sessions
### 세션

#### The `StartSession` Middleware
#### `StartSession` 미들웨어

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The session persistence logic has been [moved from the `terminate()` method to the `handle()` method](https://github.com/laravel/framework/pull/26410). If you are overriding one or both of these methods, you should update them to reflect these changes.

세션 퍼시스턴스 로직은 [`terminate()`메소드에서 `handle()`메소드로 옮겨졌습니다](https://github.com/laravel/framework/pull/26410). 이 방법 중 하나 또는 둘 다를 무시하는 경우 이러한 변경 사항을 반영하도록 업데이트해야합니다.

<a name="support"></a>
### Support
### Support

<a name="string-and-array-helpers"></a>
#### Prefer String And Array Classes Over Helpers
#### 헬퍼보다 String과 Array Classes 선호

**Likelihood Of Impact: Medium**
**영향 가능성 : 보통**

All `array_*` and `str_*` global helpers [have been deprecated](https://github.com/laravel/framework/pull/26898). You should use the `Illuminate\Support\Arr` and `Illuminate\Support\Str` methods directly.

모든 `array_ *`와 `str_ *` 글로벌 헬퍼는 [더 이상 사용되지 않습니다](https://github.com/laravel/framework/pull/26898). `Illuminate\Support\Arr` 및 `Illuminate\Support\Str` 메소드를 직접 사용해야합니다.

The impact of this change has been marked as `medium` since the helpers have been moved to the new [laravel/helpers](https://github.com/laravel/helpers) package which offers a backwards compatibility layer for all of the global array and string functions.

이 변경의 영향은 전역 배열 및 문자열 함수가 새로운 헬퍼인 [laravel/helpers](https://github.com/laravel/helpers) 패키지로 이동했기 때문에 `보통`으로 표시되었습니다.

<a name="deferred-service-providers"></a>
#### Deferred Service Providers
#### 지연된 서비스 프로바이더

**Likelihood Of Impact: Medium**
**영향 가능성 : 보통**

The `defer` boolean property on the service provider which is/was used to indicate if a provider is deferred [has been deprecated](https://github.com/laravel/framework/pull/27067). In order to mark the service provider as deferred it should implement the `Illuminate\Contracts\Support\DeferrableProvider` contract.

서비스 프로바이더의 `defer` boolean 프로퍼티는 프로바이더가 연기-defer 되어야 하는 지를 표현하기 위해서 사용했었지만 [deprecated 되었습니다](https://github.com/laravel/framework/pull/27067). 서비스 프로바이더를 연기-defer 할 것으로 표시하려면 `Illuminate\Contracts\Support\DeferrableProvider` contract을 구현해야합니다.

#### Read-Only `env` Helper
#### 읽기전용 `env` 헬퍼

**Likelihood Of Impact: Low**
**영향 가능성 : 낮음**

Previously, the `env` helper could retrieve values from environment variables which were changed at runtime. In Laravel 5.8, the `env` helper treats environment variables as immutable. If you would like to change an environment variable at runtime, consider using a configuration value that can be retrieved using the `config` helper:

이전에는 `env` 헬퍼가 런타임에 변경된 환경 변수에서 값을 검색 할 수있었습니다. Laravel 5.8에서는 `env` 헬퍼가 환경 변수를 불변으로 취급합니다. 런타임에 환경 변수를 변경하려면 `config` 헬퍼를 사용하여 검색 할 수있는 설정 값을 사용하는 것을 고려하십시오 :

Previous behavior:

이전 동작:

    dump(env('APP_ENV')); // local

    putenv('APP_ENV=staging');

    dump(env('APP_ENV')); // staging

New behavior:

신규 동작:

    dump(env('APP_ENV')); // local

    putenv('APP_ENV=staging');

    dump(env('APP_ENV')); // local

<a name="testing"></a>
### Testing
### 테스팅

#### The `setUp` & `tearDown` Methods
#### `setUp` 과 `tearDown` 메소드

The `setUp` and `tearDown` methods now require a void return type:

이제 `setUp`과 `tearDown` 메소드는 void 리턴 타입을 필요로합니다 :

    protected function setUp(): void
    protected function tearDown(): void

#### PHPUnit 8
#### PHPUnit 8

**Likelihood Of Impact: Optional**
**영향 가능성 : 선택**

By default, Laravel 5.8 uses PHPUnit 7. However, you may optionally upgrade to PHPUnit 8, which requires PHP >= 7.2. In addition, please read through the entire list of changes in [the PHPUnit 8 release announcement](https://phpunit.de/announcements/phpunit-8.html).

기본적으로 Laravel 5.8은 PHPUnit 7을 사용합니다. 그러나 선택적으로 PHP> = 7.2가 필요한 PHPUnit 8로 업그레이드 할 수 있습니다. 추가적으로 [PHPUnit 8 릴리스 발표](https://phpunit.de/announcements/phpunit-8.html)의 전체 변경 사항을 읽어보십시오.

<a name="validation"></a>
### Validation
### Validation

#### The `Validator` Contract
#### `Validator` Contract

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The `validated` method [was added to the `Illuminate\Contracts\Validation\Validator` contract](https://github.com/laravel/framework/pull/26419):

`validated` 메소드가 [`Illuminate\Contracts\Validation\Validator` contract에 추가되었습니다](https://github.com/laravel/framework/pull/26419). :

    /**
     * Get the attributes and values that were validated.
     *
     * @return array
     */
    public function validated();

If you are implementing this interface, you should add this method to your implementation.

이 인터페이스를 구현하는 경우 이 메소드를 구현에 추가해야합니다.

#### The `ValidatesAttributes` Trait
#### `ValidatesAttributes` Trait

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The `parseTable`, `getQueryColumn` and `requireParameterCount` methods of the `Illuminate\Validation\Concerns\ValidatesAttributes` trait have been changed from `protected` to `public`.

`Illuminate\Validation\Concerns\ValidatesAttributes` trait의 `parseTable`, `getQueryColumn` 및 `requireParameterCount` 메소드가 `protected`에서 `public`으로 변경되었습니다.

#### The `DatabasePresenceVerifier` Class
#### `DatabasePresenceVerifier` 클래스

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The `table` method of the `Illuminate\Validation\DatabasePresenceVerifier` class has been changed from `protected` to `public`.

`Illuminate\Validation\DatabasePresenceVerifier` 클래스의 `table` 메소드가 `protected`에서 `public`으로 변경되었습니다.

#### The `Validator` Class
#### `Validator` 클래스

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The `getPresenceVerifierFor` method of the `Illuminate\Validation\Validator` class [has been changed](https://github.com/laravel/framework/pull/26717) from `protected` to `public`.

`Illuminate\Validation\Validator` 클래스의 `getPresenceVerifierFor` 메소드가 `protected`에서 `public`으로 [변경되었습니다](https://github.com/laravel/framework/pull/26717).

#### Email Validation
#### Email 검증

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The email validation rule now checks if the email is [RFC5630](https://tools.ietf.org/html/rfc6530) compliant, making the validation logic consistent with the logic used by SwiftMailer. In Laravel `5.7`, the `email` rule only verified that the email was [RFC822](https://tools.ietf.org/html/rfc822) compliant.

이메일 검증 규정은 이제 이메일이 [RFC5630](https://tools.ietf.org/html/rfc6530)을 준수하는지 확인하며, 검증 로직을 SwiftMailer가 사용하는 로직과 일치하도록 만들었습니다. Laravel `5.7`에서는 `email` 규칙은 이메일이 [RFC822](https://tools.ietf.org/html/rfc822)와 호환된다는 것을 확인했습니다.

Therefore, when using Laravel 5.8, emails that were previously incorrectly considered invalid will now be considered valid (e.g `hej@bär.se`).  Generally, this should be considered a bug fix; however, it is listed as a breaking change out of caution. [Please let us know if you encounter any issues surrounding this change](https://github.com/laravel/framework/pull/26503).

따라서 Laravel 5.8을 사용할 때 이전에 잘못되었다고 잘못 판단 되었던 이메일이 이제는 유효한 것이라고 판별합니다 (예 :`hej@bär.se`). 일반적으로 이것은 버그 수정으로 봐야하지만 주의해야하는 변경사항으로 분류합니다. [이 변경과 관련하여 문제가 발생하면 알려주십시오.](https://github.com/laravel/framework/pull/26503).

<a name="view"></a>
### View
### 뷰

#### The `getData` Method
#### `getData` 메소드

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The `getData` method [was added to the `Illuminate\Contracts\View\View` contract](https://github.com/laravel/framework/pull/26754). If you are implementing this interface, you should add this method to your implementation.

`getData` 메소드는 [`Illuminate\Contracts\View\View` 계약에 추가되었습니다](https://github.com/laravel/framework/pull/26754). 이 인터페이스를 구현하는 경우 이 메소드를 구현에 추가해야합니다.

<a name="notifications"></a>
### Notifications
### 알림

<a name="nexmo-slack-notification-channels"></a>
#### Nexmo / Slack Notification Channels
#### Nexmo / Slack Notification Channels

**Likelihood Of Impact: High**
**영향 가능성 : 높음**

The Nexmo and Slack Notification channels have been extracted into first-party packages. To use these channels in your application, require the following packages:

Nexmo 및 Slack Notification 채널은 자체 패키지로 분리되었습니다. 애플리케이션에서 이러한 채널을 사용하려면 다음 패키지가 필요합니다.

    composer require laravel/nexmo-notification-channel
    composer require laravel/slack-notification-channel

<a name="miscellaneous"></a>
### Miscellaneous
### 기타

We also encourage you to view the changes in the `laravel/laravel` [GitHub repository](https://github.com/laravel/laravel). While many of these changes are not required, you may wish to keep these files in sync with your application. Some of these changes will be covered in this upgrade guide, but others, such as changes to configuration files or comments, will not be. You can easily view the changes with the [GitHub comparison tool](https://github.com/laravel/laravel/compare/5.7...master) and choose which updates are important to you.

또한 `laravel/laravel` [GitHub 저장소](https://github.com/laravel/laravel)에서 변경 사항을 확인하는 것이 좋습니다. 이러한 변경 사항이 많이 필요하지는 않지만 이러한 파일을 애플리케이션과 동기화 된 상태로 유지할 수 있습니다. 이러한 변경 사항 중 일부는 이 업그레이드 가이드에서 다루지만 설정 파일이나 설명의 변경 사항은 변경되지 않습니다. [GitHub 비교 도구](https://github.com/laravel/laravel/compare/5.7...master)를 사용하여 변경 사항을 쉽게보고, 중요한 업데이트를 선택할 수 있습니다.
