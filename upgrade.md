# 업그레이드 가이드

- [9.x 버전에서 10.0으로 업그레이드](#upgrade-10.0)

<a name="high-impact-changes"></a>

## High Impact Changes

<div class="content-list" markdown="1">

- [의존성 업데이트](#updating-dependencies)
- [Minimum Stability 업데이트](#updating-minimum-stability)

</div>

<a name="medium-impact-changes"></a>

## Medium Impact Changes

<div class="content-list" markdown="1">

- [데이터베이스 구문](#database-expressions)
- ["Dates" 모델 프로퍼티](#model-dates-property)
- [Monolog 3](#monolog-3)
- [Redis 캐시 태그](#redis-cache-tags)
- [서비스 모킹](#service-mocking)
- [언어](#language-directory)

</div>

<a name="low-impact-changes"></a>

## Low Impact Changes

<div class="content-list" markdown="1">

- [클로저 검증 규칙 메시지](#closure-validation-rule-messages)
- [Form 요청 `after` 메소드](#form-request-after-method)
- [Public 경로 바인딩](#public-path-binding)
- [쿼리 예외 생성자](#query-exception-constructor)
- [Rate Limiter 반환 값](#rate-limiter-return-values)
- [Relation `getBaseQuery` 메소드](#relation-getbasequery-method)
- [`Redirect::home` 메소드](#redirect-home)
- [`Bus::dispatchNow` 메소드](#dispatch-now)
- [`registerPolicies` 메소드](#register-policies)
- [ULID 컬럼](#ulid-columns)

</div>

<a name="upgrade-10.0"></a>

## Upgrading To 10.0 From 9.x

<a name="estimated-upgrade-time-??-minutes"></a>

#### 예상 소요 시간 : 10분

> **Note**
> 모든 주요 변경 사항을 문서화하고자 했습니다. 몇몇 변경 사항은 프레임워크의 모호한 부분에 있기 때문에 일정 부분 당신의 애플리케이션에 실질적인 영향을 줄 수 있습니다. 시간을 절약하고 싶으신가요? 업그레이드 자동화를 위해 [Laravel Shift](https://laravelshift.com/)를 이용할 수 있습니다.

<a name="updating-dependencies"></a>

### 의존성 업데이트

**영향도 : 높음**

#### PHP 8.1.0 필요

Laravel은 이제 PHP 8.1 이상을 요구합니다.

#### Composer 2.2.0 필요

라라벨은 [Composer](https://getcomposer.org) 2.2.0 버전 이상을 요구합니다.

#### Composer 의존성

`composer.json` 파일에 기재된 애플리케이션의 의존성을 업데이트 해야 합니다.

<div class="content-list" markdown="1">

- `laravel/framework` to `^10.0`
- `laravel/sanctum` to `^3.2`
- `doctrine/dbal` to `^3.0`
- `spatie/laravel-ignition` to `^2.0`
- `laravel/passport` to `^11.0` ([가이드](https://github.com/laravel/passport/blob/11.x/UPGRADE.md))

</div>

Sanctum 2.x 버전에서 3.x 버전으로 업데이트 하자고 한다면, [Sanctum upgrade guide](https://github.com/laravel/sanctum/blob/3.x/UPGRADE.md)를 참고하세요.

나아가 [PHPUnit 10](https://phpunit.de/announcements/phpunit-10.html)를 사용하고자 한다면, `phpunit.xml` 설정 파일에 있는 `<coverage>` 섹션에서 `processUncoveredFiles` 속성을 삭제하세요. 그런 다음 `composer.json` 파일 내 아래 의존성을 업데이트 하세요.

<div class="content-list" markdown="1">

- `nunomaduro/collision` to `^7.0`
- `phpunit/phpunit` to `^10.0`

</div>

마지막으로 서드파티 패키지를 검사하고 해당 패키지들이 Laravel 10을 지원하는지 확인하세요

<a name="updating-minimum-stability"></a>

#### Minimum Stability

`composer.json` 파일 내에 `minimum-stability` 속성을 `stable`로 바꿔주어야 합니다. `minimum-stability`의 기본값이 `stable`이므로 해당 설정을 삭제하셔도 됩니다.

```json
"minimum-stability": "stable",
```

### Application

<a name="public-path-binding"></a>

#### Public 경로 바인딩

**Likelihood Of Impact: Low**
**영향도 : 낮음**

If your application is customizing its "public path" by binding `path.public` into the container, you should instead update your code to invoke the `usePublicPath` method offered by the `Illuminate\Foundation\Application` object:
애플리케이션에 `path.public`를 바인딩하여 "public 경로"를 수정하고 싶다면, `Illuminate\Foundation\Application`가 제공하는 `usePublicPath` 메서드를 호출하여야 합니다.

```php
app()->usePublicPath(__DIR__.'/public');
```

### 인증

<a name="register-policies"></a>

### `registerPolicies` 메소드

**Likelihood Of Impact: Low**
**영향도 : 낮음**

`AuthServiceProvider` 내 `registarPolicies` 메소드는 프레임워크에 의해 자동으로 호출됩니다. 그러므로 `AuthServiceProvider`의 `boot` 메서드에서 해당 메서드 호출은 삭제해주세요.

### 캐시

<a name="redis-cache-tags"></a>

#### Redis 캐시 태그

**Likelihood Of Impact: Medium**
**영향도 : 중간**

Redis [cache tag](/docs/{{version}}/cache#cache-tags) 지원이 더 나은 성능과 저장 효율을 위해 다시 짜여졌습니다. 이전 라라벨 버전에서는, Redis를 캐시 드라이버로 사용하는 경우 stale 캐시도 누적되었습니다.

그러나 stale 캐시를 적절하게 삭제하기 위해 새로운 `cache:prune-stale-tags` 아티잔 커맨드가 `App\Console\Kernel` 클래스 내에 실행 예정되어 있습니다.

    $schedule->command('cache:prune-stale-tags')->hourly();

### 데이터베이스

<a name="database-expressions"></a>

#### 데이터베이스 구문

**Likelihood Of Impact: Medium**
**영향도: 중간**

(보통 `DB::raw` 형태로 생성되는) 데이터베이스 "구문"이 추가적인 기능을 제공하기 위해 새로 짜여졌습니다. 특히 `getValue(Grammar $grammer)` 메소드 구문을 통해 DB 문법을 따르는 문자열이 제공되어야 한다는 점입니다. `(string)`을 사용해 데이터베이스 표현을 제공하는 방식은 더 이상 지원되지 않습니다.

**일반적으로 최종 사용자용 애플리케이션에는 영향을 주지 않습니다**; 그러나, 당신의 애플리케이션이 `(string)` 으로 수동으로 데이터베이스 구문을 제공하고 있거나 `__toString`을 호출하여 데이터베이스 구문을 직접 사용하고 있다면, `getValue` 메소드를 호출하도록 코드를 수정해야 합니다.

```php
use Illuminate\Support\Facades\DB;

$expression = DB::raw('select 1');

$string = $expression->getValue(DB::connection()->getQueryGrammar());
```

<a name="query-exception-constructor"></a>

#### 쿼리 예외 생성자

**Likelihood Of Impact: Very Low**
**영향도 : 매우 낮음**

The `Illuminate\Database\QueryException` constructor now accepts a string connection name as its first argument. If your application is manually throwing this exception, you should adjust your code accordingly.
`Illuminate\Database\QueryException` 생성자는 첫 번째 인자로 문자열 타입 연결명을 받습니다. 수동으로 해당 예외를 던지고 있다면, 이에 맞게 코드를 수정해주세요.

<a name="ulid-columns"></a>

#### ULID Columns

**Likelihood Of Impact: Low**
**영향도 : 낮음**

`ulid` 메소드를 인자 없이 호출할 경우, 컬럼명은 `ulid`가 될 것입니다. 이전 라라벨 버전에서는 인자 없이 이 함수를 호출하면 `uuid`로 틀리게 컬럼이 생성되었습니다.

    $table->ulid();

`ulid` 함수 호출 시 컬럼명을 명시하고자 한다면, 컬럼명을 인자로 던져주세요.

    $table->ulid('ulid');

### Eloquent

<a name="model-dates-property"></a>

#### "Dates" 모델 속성

**영향도 : 중간**

삭제된 Eloquent 모델의 `$dates` 속성이 삭제되었습니다. 애플리케이션은 `$casts` 속성을 사용해야 합니다.

```php
protected $casts = [
    'deployed_at' => 'datetime',
];
```

<a name="relation-getbasequery-method"></a>

#### `getBaseQuery` Relation 메소드

**영향도 : 매우 낮음**

`Illuminate\Database\Eloquent\Relations\Relation` 클래스 내 `getBaseQuery` 메소드는 `toBase`로 함수명이 변경되었습니다.

### Localization

<a name="language-directory"></a>

#### Language 디렉토리

**영향도 : 낮음**

이미 생성된 애플리케이션과는 무관하지만, 라라벨 애플리케이션 뼈대는 더 이상 `lang` 디렉토리를 포함하지 않습니다. 대신 새 라라벨 애플리케이션을 생성하는 경우 `lang:publish` 아티잔 커맨드를 이용하여 생성될 수 있습니다.

```shell
php artisan lang:publish
```

### Logging

<a name="monolog-3"></a>

#### Monolog 3

**영향도 : 중간**

라라벨의 Monolog 의존성은 Monolog 3.x 버전으로 업데이트 되었습니다. Monolog와 직접 상호작용한다면, Monolog의 [upgrade guide](https://github.com/Seldaek/monolog/blob/main/UPGRADE.md) 살펴보시기를 권장합니다.

만약 BugSnag 이나 Rollbar와 같은 서드 파티 로깅 서비스를 이용하고 있다면 해당 서드파티 패키지를 Monolog 3.x 및 Laravel 10.x 버전에 맞게 업그레이드 해야할 수 있습니다.

### Queues

<a name="dispatch-now"></a>

#### `Bus::dispatchNow` 메소드

**영향도 : 낮음**

`Bus::dispatchNow`와 `dispatch_now` 메소드가 삭제되었습니다. 대신 `Bus::dispatchSync`와 `dispatch_sync` 메소드를 사용하세요.

### Routing

<a name="middleware-aliases"></a>

#### 미들웨어 별칭

**영향도 : 선택적**

새로운 라라벨 애플리케이션에서 `App\Http\Kernel` 클래스 내 `$routeMiddleware` 속성이 목적에 맞게 `$middlewareAliases`로 변경되었습니다. 사용중인 애플리케이션에서도 속성명을 바꾸는 것이 좋지만, 꼭 필요한 것은 아닙니다.

<a name="rate-limiter-return-values"></a>

#### Rate Limiter 반환 값

**영향도 : 낮음**

When invoking the `RateLimiter::attempt` method, the value returned by the provided closure will now be returned by the method. If nothing or `null` is returned, the `attempt` method will return `true`:
`RateLimiter::attempt` 메소드를 호출할 때 인자로 제공된 클로저에 의해 반환된 값은 메소드로 반환됩니다. 반환된 값이 없거나 `null`인 경우, `attempt` 메소드는 `true`를 반환합니다.

```php
$value = RateLimiter::attempt('key', 10, fn () => ['example'], 1);

$value; // ['example']
```

<a name="redirect-home"></a>

#### `Redirect::home` 메소드

**Likelihood Of Impact: Very Low**
**영향도 : 매우 낮음**

`Redirect::home`은 삭제되었습니다. 대신 명시적으로 명명된 경로로 리다이렉트 될 것입니다.

```php
return Redirect::route('home');
```

### Testing

<a name="service-mocking"></a>

#### 서비스 모킹

**영향도 : 중간**

`MocksApplicationServices` 트레이트가 삭제되었습니다. 해당 트레이트는 `expectsEvents`, `expectsJobs`, `expectsNotifications` 등의 메소드를 제공했습니다.

위와 같은 메소드를 사용하고자 한다면, `Event::fake`, `Bus::fake`, `Notification::fake`로 전환할 것을 권장합니다. 각 컴포넌트 문서를 통해 임의값을 만들어 대체하는 법에 대해서 배울 수 있습니다.

### Validation

<a name="closure-validation-rule-messages"></a>

#### 클로저 유효성 검사 메시지

**영향도 : 매우 낮음**

클로저 기반의 유효성 검사 규칙을 만드는 경우, `$fail` 콜백 함수를 한 번 이상 호출하게 된다면 이전 메시지를 덮어쓰는 대신 배열에 메시지가 추가됩니다. 일반적으로 당신의 애플리케이션에 영향을 주지는 않습니다.

이에 더해, `$fail` 콜백 함수는 객체를 반환합니다. 유효성 검사 클로저에 반환값 타입을 기재하였다면, 타입 힌트를 업데이트 해야합니다.

```php
public function rules()
{
    'name' => [
        function ($attribute, $value, $fail) {
            $fail('validation.translation.key')->translate();
        },
    ],
}
```

<a name="form-request-after-method"></a>

### Form 요청 After 메소드

**영향도 : 매우 낮음**

form 요청 내 `after` 메소드는 이제 [라라벨이 사전 정의](https://github.com/laravel/framework/pull/46757)합니다. form 요청이 `after` 메소드를 정의하고 있다면, 해당 메소드는 이름을 바꾸거나 라라벨의 새로운 "유효성 검사 다음" 기능을 활용하기 위해 수정되어야 합니다.

<a name="miscellaneous"></a>

### 기타

`laravel/laravel` [GitHub repository](https://github.com/laravel/laravel) 변경 사항을 보시는 것을 권장합니다. 모든 변경 사항이 필요한 것은 아니지만, 일부 파일은 당신의 애플리케이션과 동기화되어야 할 수 있습니다. 어떤 변경 사항은 이 가이드에 의해 다뤄지지만 설정 파일, 주석 등은 그렇지 않을 수 있습니다.
[GitHub comparison tool](https://github.com/laravel/laravel/compare/9.x...10.x)를 통해 변경 사항과 어떤 업데이트가 중요한지 볼 수 있습니다. 그러나 Github 비교 도구에 의해 보여지는 변경 내용은 우리 조직의 PHP 고유 타입 적용에 의한 것입니다. 라라벨 10 마이그레이션 중 발생하는 이러한 변경은 되돌릴 수 있으며 선택 사항입니다.
