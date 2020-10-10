# Upgrade Guide
# 업그레이드 가이드

- [Upgrading To 8.0 From 7.x](#upgrade-8.0)
- [7.x에서 8.0으로 업그레이드](#upgrade-8.0)

<a name="high-impact-changes"></a>
## High Impact Changes
## 영향도 높은 변경사항들

<div class="content-list" markdown="1">
- [Model Factories](#model-factories)
- [모델 팩토리](#model-factories)
- [Queue `retryAfter` Method](#queue-retry-after-method)
- [큐 `retryAfter` 메소드](#queue-retry-after-method)
- [Queue `timeoutAt` Property](#queue-timeout-at-property)
- [큐 `timeoutAt` 속성](#queue-timeout-at-property)
- [Queue `allOnQueue` and `allOnConnection`](#queue-allOnQueue-allOnConnection)
- [큐 `allOnQueue` 와 `allOnConnection`](#queue-allOnQueue-allOnConnection)
- [Pagination Defaults](#pagination-defaults)
- [Pagination 기본설정](#pagination-defaults)
- [Seeder & Factory Namespaces](#seeder-factory-namespaces)
- [Seeder와 Factory 네임스페이스](#seeder-factory-namespaces)
</div>

<a name="medium-impact-changes"></a>
## Medium Impact Changes
## 영향도 중간의 변경사항들

<div class="content-list" markdown="1">
- [PHP 7.3.0 Required](#php-7.3.0-required)
- [PHP 7.3.0 필요](#php-7.3.0-required)
- [Failed Jobs Table Batch Support](#failed-jobs-table-batch-support)
- [Failed Jobs 테이블 배치 지원](#failed-jobs-table-batch-support)
- [Maintenance Mode Updates](#maintenance-mode-updates)
- [점검 모드 업데이트](#maintenance-mode-updates)
- [The `php artisan down --message` Option](#artisan-down-message)
- [`php artisan down --message` 옵션](#artisan-down-message)
- [The `assertExactJson` Method](#assert-exact-json-method)
- [`assertExactJson` 메소드](#assert-exact-json-method)
</div>

<a name="upgrade-8.0"></a>
## Upgrading To 8.0 From 7.x
## 7.x에서 8.0으로 업그레이드

#### Estimated Upgrade Time: 15 Minutes
#### 예상되는 업그레이드 시간: 15분

> {note} We attempt to document every possible breaking change. Since some of these breaking changes are in obscure parts of the framework only a portion of these changes may actually affect your application.

> {note} 이전 버전과 호환되지 않는 모든 변경사항을 기록했습니다만 변경사항들 중 일부는 프레임워크의 모호한 부분에 있기 때문에 실제로는 여러분의 어플리케이션에 영향을 끼치지 않을 수도 있습니다.

<a name="php-7.3.0-required"></a>
### PHP 7.3.0 Required
### PHP 7.3.0 필요

**Likelihood Of Impact: Medium**
**영향 가능성: 중간**

The new minimum PHP version is now 7.3.0.

이제 새로운 PHP 최소 지원 버전은 7.3.0 입니다.

<a name="updating-dependencies"></a>
### Updating Dependencies
### 의존성 업데이트

Update the following dependencies in your `composer.json` file:

여러분의 `composer.json` 에 있는 아래의 의존성들을 업데이트 하세요.

<div class="content-list" markdown="1">
- `guzzlehttp/guzzle` to `^7.0.1`
- `facade/ignition` to `^2.3.6`
- `laravel/framework` to `^8.0`
- `laravel/ui` to `^3.0`
- `nunomaduro/collision` to `^5.0`
- `phpunit/phpunit` to `^9.0`
</div>

The following first-party packages have new major releases to support Laravel 8. If applicable, you should read their individual upgrade guides before upgrading:

아래의 프레임워크 패키지들은 라라벨 8을 지원하기 위한 새로운 메이저 릴리즈를 갖고 있습니다. 가능하면 업그레이드를 진행하기 전에 여러분은 개별 업그레이드 가이드를 읽어보십시오:

<div class="content-list" markdown="1">
- [Horizon v5.0](https://github.com/laravel/horizon/blob/master/UPGRADE.md)
- [Passport v10.0](https://github.com/laravel/passport/blob/master/UPGRADE.md)
- [Socialite v5.0](https://github.com/laravel/socialite/blob/master/UPGRADE.md)
- [Telescope v4.0](https://github.com/laravel/telescope/releases)
</div>

In addition, the Laravel installer has been updated to support `composer create-project` and Laravel Jetstream. Any installer older than 4.0 will cease to work after October 2020. You should upgrade your global installer to `^4.0` as soon as possible.

추가로, 라라벨 인스톨러는 `composer create-project` 와 라라벨 Jetstream을 지원하도록 업데이트 되었습니다. 4.0 버전보다 오래된 인스톨러는 2020년 10월 이후로 작동이 중단될 것입니다. 여러분의 라라벨 글로벌 인스톨러를 가능한 빨리 `^4.0` 버전으로 업그레이드 하세요. 

Finally, examine any other third-party packages consumed by your application and verify you are using the proper version for Laravel 8 support.

마지막으로, 여러분의 어플리케이션에서 사용중인 다른 3rd-party 패키지들을 확인해보고, 라라벨 8을 지원하는 제대로된 버전을 사용중인지 확인하세요.

### Collections
### 컬렉션

#### The `isset` Method
#### `isset` 메소드

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

To be consistent with typical PHP behavior, the `offsetExists` method of `Illuminate\Support\Collection` has been updated to use `isset` instead of `array_key_exists`. This may present a change in behavior when dealing with collection items that have a value of `null`:

일반적인 PHP의 동작과 일관성을 유지하기 위해, `Illuminate\Support\Collection`의 `offsetExists` 메소드는 `array_key_exists` 대신 `isset` 함수를 사용하도록 업데이트 되었습니다. 이 변경사항은 컬렉션에서 `null` 값을 가진 아이템들을 다룰 때 동작의 변화를 발생시킵니다.

    $collection = collect([null]);

    // Laravel 7.x - true
    isset($collection[0]);

    // Laravel 8.x - false
    isset($collection[0]);

### Database
### 데이터베이스

<a name="seeder-factory-namespaces"></a>
#### Seeder & Factory Namespaces
#### Seeder와 Factory 네임스페이스

**Likelihood Of Impact: High**
**영향 가능성: 높음**

Seeders and factories are now namespaced. To accommodate for these changes, add the `Database\Seeders` namespace to your seeder classes. In addition, the previous `database/seeds` directory should be renamed to `database/seeders`:

Seeder와 Factory들은 이제 네임스페이스를 갖습니다. 이 변경사항을 대응하기 위해 `Database\Seeders` 네임스페이스를 여러분의 Seeder 클래스들에 추가하세요. 그리고, 이전의 `database/seeds` 디렉토리는 `database/seeders` 로 이름이 변경되어야 합니다. 

    <?php

    namespace Database\Seeders;

    use App\Models\User;
    use Illuminate\Database\Seeder;

    class DatabaseSeeder extends Seeder
    {
        /**
         * Seed the application's database.
         *
         * @return void
         */
        public function run()
        {
            ...
        }
    }

If you are choosing to use the `laravel/legacy-factories` package, no changes to your factory classes are required. However, if you are upgrading your factories, you should add the `Database\Factories` namespace to those classes.

만약 여러분이 `laravel/legacy-factories` 패키지를 사용하기로 결정했다면, 여러분의 팩토리 클래스를 변경할 필요는 없습니다. 하지만, 만약 여러분이 팩토리 클래스들을 업그레이드 하려고 한다면 클래스들에 `Database\Factories` 네임스페이스를 추가해야 합니다.

Next, in your `composer.json` file, remove `classmap` block from the `autoload` section and add the new namespaced class directory mappings:

그 다음, 여러분의 `composer.json` 파일에서,  `autoload` 섹션의 `classmap` 블록을 제거하고 새로운 네임스페이스의 디렉토리 매핑을 추가하세요:

    "autoload": {
        "psr-4": {
            "App\\": "app/",
            "Database\\Factories\\": "database/factories/",
            "Database\\Seeders\\": "database/seeders/"
        }
    },

### Eloquent
### 엘로퀀트

<a name="model-factories"></a>
#### Model Factories
#### 모델 팩토리

**Likelihood Of Impact: High**
**영향 가능성: 높음**

Laravel's [model factories](/docs/{{version}}/database-testing#creating-factories) feature has been totally rewritten to support classes and is not compatible with Laravel 7.x style factories. However, to ease the upgrade process, a new `laravel/legacy-factories` package has been created to continue using your existing factories with Laravel 8.x. You may install this package via Composer:

라라벨의 [모델 팩토리](/docs/{{version}}/database-testing#creating-factories) 기능은 이제 클래스를 지원하기 위해 완전히 재작성 되었으며 더이상 라라벨 7.x 스타일의 팩토리와 호환되지 않습니다. 하지만 업그레이드 과정을 쉽게 하기 위해, 이전 스타일의 팩토리들을 라라벨 8.x 버전과 함께 사용할 수 있도록 `laravel/legacy-factories` 패키지가 지원됩니다. 여러분은 컴포저를 통해 이 패키지를 설치할 수 있습니다.

    composer require laravel/legacy-factories

#### The `Castable` Interface
#### `Castable` 인터페이스

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The `castUsing` method of the `Castable` interface has been updated to accept an array of arguments. If you are implementing this interface you should update your implementation accordingly:

`Castable` 인터페이스의 `castUsing` 메소드가 배열을 인자로 넘겨받도록 업데이트 되었습니다. 만약 여러분이 이 인터페이스를 구현한 부분이 있다면, 여러분의 구현체를 적절하게 수정해야 합니다.

    public static function castUsing(array $arguments);

#### Increment / Decrement Events
#### 증가 / 감소 이벤트

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

Proper "update" and "save" related model events will now be dispatched when executing the `increment` or `decrement` methods on Eloquent model instances.

이제 엘로퀀트 모델 인스턴스에 `increment` 혹은 `decrement` 메소드가 실행될때도 적절한 "업데이트" 와 "저장" 관련 모델 이벤트가 발생됩니다. 

### Events
### 이벤트

#### The `Dispatcher` Contract
#### `Dispatcher` 컨트랙트

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The `listen` method of the `Illuminate\Contracts\Events\Dispatcher` contract has been updated to make the `$listener` property optional. This change was made to support automatic detection of handled event types via reflection. If you are manually implementing this interface, you should update your implementation accordingly:

`Illuminate\Contracts\Events\Dispatcher`의 `listen` 메소드는 이제 $listener 인자를 선택적으로 받도록 업데이트 되었습니다. 이 변경사항은 리플렉션을 통해 처리할 수 있는 이벤트 타입들을 자동으로 감지할 수 있도록 지원해주기 위한 것입니다. 만약 여러분이 이 인터페이스를 직접 구현하고 있다면, 여러분의 구현체를 적절히 업데이트 해주어야 합니다.

    public function listen($events, $listener = null);

### Framework
### 프레임워크

<a name="maintenance-mode-updates"></a>
#### Maintenance Mode Updates
#### 점검 모드 업데이트

**Likelihood Of Impact: Optional**
**영향 가능성: 선택적**

The [maintenance mode](/docs/{{version}}/configuration#maintenance-mode) feature of Laravel has been improved in Laravel 8.x. Pre-rendering the maintenance mode template is now supported and eliminates the chances of end users encountering errors during maintenance mode. However, to support this, the following lines must be added to your `public/index.php` file. These lines should be placed directly under the existing `LARAVEL_START` constant definition:

라라벨의 [점검 모드](/docs/{{version}}/configuration#maintenance-mode) 기능이 라라벨 8.x 버전에서 향상되었습니다. 점검 모드 템플릿의 Pre-rendering 기능이 이제 지원되어 유저들이 더 이상 점검모드에서 에러를 겪을 가능성을 완전히 차단됩니다. 하지만 이 기능을 사용하기 위해서는 여러분의 `public/index.php` 파일에 아래의 라인들이 추가되어야 합니다. 아래의 라인들이 `LARAVEL_START` 상수 선언 아래에 위치해야 합니다.

    define('LARAVEL_START', microtime(true));

    if (file_exists(__DIR__.'/../storage/framework/maintenance.php')) {
        require __DIR__.'/../storage/framework/maintenance.php';
    }

<a name="artisan-down-message"></a>
#### The `php artisan down --message` Option
#### `php artisan down --message` 옵션

**Likelihood Of Impact: Medium**
**영향 가능성: 중간**

The `--message` option of the `php artisan down` command has been removed. As an alternative, consider [pre-rendering your maintenance mode views](/docs/{{version}}/configuration#maintenance-mode) with the message of your choice.

`php artisan down` 커맨드의 `--message` 옵션이 제거되었습니다. 대안으로, [점검 모드 뷰 Pre-rendering]](/docs/{{version}}/configuration#maintenance-mode) 기능과 함께 여러분의 메시지를 표시할 수 있습니다.

#### Manager `$app` Property
#### Manager `$app` 속성

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The previously deprecated `$app` property of the `Illuminate\Support\Manager` class has been removed. If you were relying on this property, you should use the `$container` property instead.

이전에 deprecate 되었던  `Illuminate\Support\Manager` 클래스의 `$app` 속성이 제거되었습니다. 만약 여러분이 이 속성을 사용하는 부분이 있다면 `$container` 속성을 대신 사용해야 합니다.

#### The `elixir` Helper
#### `elixir` 헬퍼

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The previously deprecated `elixir` helper has been removed. Applications still using this method are encouraged to upgrade to [Laravel Mix](https://github.com/JeffreyWay/laravel-mix).

이전에 deprecate 되었던 `elixer` 헬퍼가 제거되었습니다. 여전히 이 메서드를 사용하는 어플리케이션들은 [라라벨 Mix](https://github.com/JeffreyWay/laravel-mix)로 업그레이드 하길 권장합니다.

### Mail
### 메일

#### The `sendNow` Method
#### `sendNow` 메서드

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The previously deprecated `sendNow` method has been removed. Instead, please use the `send` method.

이전에 depreacte 되었던 `sendNow` 메서드가 제거되었습니다. 대신, `send` 메서드를 사용하세요.

### Pagination
### Pagination

<a name="pagination-defaults"></a>
#### Pagination Defaults
#### Pagination 기본설정

**Likelihood Of Impact: High**
**영향 가능성: 높음**

The paginator now uses the [Tailwind CSS framework](https://tailwindcss.com) for its default styling. In order to keep using Bootstrap, you should add the following method call to the `boot` method of your application's `AppServiceProvider`:

Paginator는 이제 기본 스타일링을 위해 [Tailwind CSS 프레임워크](https://tailwindcss.com)를 사용합니다. 부트스트랩 기반의 스타일을 유지하고 싶다면, `AppServiceProvider`의 `boot` 메서드에 아래의 함수 호출을 추가해야 합니다.

    use Illuminate\Pagination\Paginator;

    Paginator::useBootstrap();

### Queue
### 큐

<a name="queue-retry-after-method"></a>
#### The `retryAfter` Method
#### `retryAfter` 메서드

**Likelihood Of Impact: High**
**영향 가능성: 높음**

For consistency with other features of Laravel, the `retryAfter` method and `retryAfter` property of queued jobs, mailers, notifications, and listeners has been renamed to `backoff`. You should update the name of this method / property in the relevant classes in your application.

라라벨의 다른 기능들과 일관성을 유지하기 위해, Queued jobs, Mailer, Notification, 그리고 Listener의 `retryAfter` 메서드와 `retryAfter` 속성은 `backoff` 로 이름이 변경되었습니다. 여러분은 어플리케이션의 이 메서드와 속성에 관련된 부분의 이름을 업데이트 해야합니다.

<a name="queue-timeout-at-property"></a>
#### The `timeoutAt` Property
#### `timeoutAt` 속성

**Likelihood Of Impact: High**
**영향 가능성: 높음**

The `timeoutAt` property of queued jobs, notifications, and listeners has been renamed to `retryUntil`. You should update the name of this property in the relevant classes in your application.

Queued jobs, Notification, 그리고 Listener의 `timeoutAt` 속성은 `retryUntil` 로 이름이 변경되었습니다. 여러분은 어플리케이션의 이 메서드와 속성에 관련된 부분의 이름을 업데이트 해야합니다.

<a name="queue-allOnQueue-allOnConnection"></a>
#### The `allOnQueue()` / `allOnConnection()` Methods
#### `allOnQueue()` / `allOnConnection()` 메서드

**Likelihood Of Impact: High**
**영향 가능성: 높음**

For consistency with other dispatching methods, the `allOnQueue()` and `allOnConnection()` methods used with job chaining have been removed. You may use the `onQueue()` and `onConnection()` methods instead. These methods should be called before calling the `dispatch` method:

다른 dispatching 메서드들과 일관성을 유지하기 위해 작업 체이닝과 함께 사용되던 `allOnQueue()` 와 `allOnConnection()` 메서드는 제거되었습니다. 여러분은 이제 `onQueue()` 메서드와 `onConnection()` 메서드를 대신 사용하세요. 이 메서드들은 `dispatch` 메서드를 호출하기 전에 호출되어야 합니다.

    ProcessPodcast::withChain([
        new OptimizePodcast,
        new ReleasePodcast
    ])->onConnection('redis')->onQueue('podcasts')->dispatch();

<a name="failed-jobs-table-batch-support"></a>
#### Failed Jobs Table Batch Support
#### Failed Jobs 테이블 배치 지원

**Likelihood Of Impact: Optional**
**영향 가능성: 선택적**

If you plan to use the [job batching](/docs/{{version}}/queues#job-batching) features of Laravel 8.x, your `failed_jobs` database table will need to be updated. First, a new `uuid` column should be added to your table:

만약 라라벨 8.x의 [배치 잡](/docs/{{version}}/queues#job-batching) 기능을 사용할 계획이 있다면, 여러분의 `failed_jobs` 데이터베이스 테이블이 업데이트 될 필요가 있습니다. 먼저, 테이블에 새로운 `uuid` 컬럼을 만들어야 합니다.

    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Support\Facades\Schema;

    Schema::table('failed_jobs', function (Blueprint $table) {
        $table->string('uuid')->after('id')->nullable()->unique();
    });

Next, the `failed.driver` configuration option within your `queue` configuration file should be updated to `database-uuids`.

다음으로, 여러분의 `queue` 설정 파일에 있는 `failed.driver` 옵션을 `database-uuids` 로 업데이트 해야 합니다. 

### Routing
### 라우팅

#### Automatic Controller Namespace Prefixing
#### 컨트롤러 네임스페이스 자동 접두

**Likelihood Of Impact: Optional**
**영향 가능성: 선택적**

In previous releases of Laravel, the `RouteServiceProvider` class contained a `$namespace` property with a value of `App\Http\Controllers`. This value of this property was used to automatically prefix controller route declarations and controller route URL generation such as when calling the `action` helper.

이전 버전의 라라벨에서는 `RouteServiceProvider` 클래스가 `App\Http\Controllers` 값을 가진 `$namespace` 속성을 포함하고 있었습니다. 이 속성은 컨트롤러 라우트 선언부 혹은 `action` 헬퍼와 같은 메소드 호출을 통한 URL 생성부에서 자동 접두어로 사용되었습니다.

In Laravel 8, this property is set to `null` by default. This allows your controller route declarations to use the standard PHP callable syntax, which provides better support for jumping to the controller class in many IDEs:

라라벨 8에서는, 이 속성은 기본적으로 `null` 로 설정됩니다. 이것은 여러분의 컨트롤러 라우트 선언이 PHP 표준 callable 문법을 따르도록 하여, 여러 IDE에서 컨트롤러 클래스로의 jump 를 제대로 지원받기 위한 것입니다.

    use App\Http\Controllers\UserController;

    // Using PHP callable syntax...
    Route::get('/users', [UserController::class, 'index']);

    // Using string syntax...
    Route::get('/users', 'App\Http\Controllers\UserController@index');

In most cases this won't impact applications that are being upgraded because your `RouteServiceProvider` will still contain the `$namespace` property with its previous value. However, if you upgrade your application by creating a brand new Laravel project, you may encounter this as a breaking change.

`RouteServiceProvider` 클래스의  `$namespace` 속성이 업그레이드 이전의 값을 갖고 있을 것이기 때문에 대부분의 케이스에서 이 변경사항은 어플리케이션에 영향을 끼치지 않을것 입니다. 하지만, 여러분이 새로운 라라벨 프로젝트를 생성한다면, 이 변경사항은 더욱 큰 변경사항으로 느껴질 것입니다.

If you would like to continue using the original auto-prefixed controller routing, you can simply set the value of the `$namespace` property within your `RouteServiceProvider` and update the route registrations within the `boot` method to use the `$namespace` property:

만약 여러분이 자동 접두되는 컨트롤러 라우팅을 계속해서 사용하고 싶다면, `RouteServiceProvider`에 간단히 `$namespace` 속성을 추가하고, `boot` 메서드에서 `$namespace` 속성을 사용하도록 라우트 등록 과정을 업데이트 하세요: 

    class RouteServiceProvider extends ServiceProvider
    {
        /**
         * This namespace is applied to your controller routes.
         *
         * In addition, it is set as the URL generator's root namespace.
         *
         * @var string
         */
        protected $namespace = 'App\Http\Controllers';

        /**
         * Define your route model bindings, pattern filters, etc.
         *
         * @return void
         */
        public function boot()
        {
            $this->configureRateLimiting();

            $this->routes(function () {
                Route::middleware('web')
                    ->namespace($this->namespace)
                    ->group(base_path('routes/web.php'));

                Route::prefix('api')
                    ->middleware('api')
                    ->namespace($this->namespace)
                    ->group(base_path('routes/api.php'));
        });
    }

### Scheduling
### 스케줄링

#### The `cron-expression` Library
#### `cron-expression` 라이브러리

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

Laravel's dependency on `dragonmantank/cron-expression` has been updated from `2.x` to `3.x`. This should not cause any breaking change in your application unless you are interacting with the `cron-expression` library directly. If you are interacting with this library directly, please review its [change log](https://github.com/dragonmantank/cron-expression/blob/master/CHANGELOG.md).

라라벨이 의존적인 `dragonmantank/cron-expression` 패키지가 `2.x` 에서 `3.x` 버전으로 업데이트 되었습니다. 여러분이 `cron-expression` 라이브러리를 직접적으로 사용하고 있지만 않다면 이 변경사항은 여러분의 어플리케이션에 영향을 주지 않을 것입니다. 만약 여러분이 이 라이브러리를 직접적으로 사용하는 부분이 있다면, [change log](https://github.com/dragonmantank/cron-expression/blob/master/CHANGELOG.md)를 확인하세요.

### Session
### 세션

#### The `Session` Contract
#### `Session` 컨트랙트

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The `Illuminate\Contracts\Session\Session` contract has received a new `pull` method. If you are implementing this contract manually, you should update your implementation accordingly:

`Illuminate\Contracts\Session\Session` 컨트랙트에 새로운 `pull` 메서드가 추가되었습니다. 만약 이 컨트랙트를 직접 구현하고 있다면, 여러분의 구현체를 적절하게 업데이트 하세요: 

    /**
     * Get the value of a given key and then forget it.
     *
     * @param  string  $key
     * @param  mixed  $default
     * @return mixed
     */
    public function pull($key, $default = null);

### Testing
### 테스팅

<a name="assert-exact-json-method"></a>
#### The `assertExactJson` Method
#### `assertExactJson` 메서드

**Likelihood Of Impact: Medium**
**영향 가능성: 중간**

The `assertExactJson` method now requires numeric keys of compared arrays to match and be in the same order. If you would like to compare JSON against an array without requiring numerically keyed arrays to have the same order, you may use the `assertSimilarJson` method instead.

`assertExactJson` 메서드는 이제 같은 순서로 매치하기 위해 비교할 배열에 숫자로 된 키를 필요로 합니다. (숫자 키를 통해)순서와 상관 없이 JSON을 배열과 비교하려면 `assertSimilarJson` 메서드를 대신 사용할 수 있습니다.  

### Validation
### 검증

### Database Rule Connections
### 데이터베이스 룰 커넥션

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The `unique` and `exists` rules will now respect the specified connection name (accessed via the model's `getConnectionName` method) of Eloquent models when performing queries.

`unique` 와 `exists` 룰은 이제 쿼리를 실행할 때 엘로퀀트 모델에 지정된 커넥션 이름(모델의 `getConnectionName` 메서드로 접근되는)을 따릅니다.

<a name="miscellaneous"></a>
### Miscellaneous

We also encourage you to view the changes in the `laravel/laravel` [GitHub repository](https://github.com/laravel/laravel). While many of these changes are not required, you may wish to keep these files in sync with your application. Some of these changes will be covered in this upgrade guide, but others, such as changes to configuration files or comments, will not be. You can easily view the changes with the [GitHub comparison tool](https://github.com/laravel/laravel/compare/7.x...master) and choose which updates are important to you.

우리는 또한 `laravel/laravel` [GitHub 저장소](https://github.com/laravel/laravel)에서 변경사항들을 확인해보시길 권합니다. 변경사항들 중 많은 부분들은 필수는 아니지만, 업데이트 된 파일들을 여러분의 어플리케이션에 동기화 해두는게 좋습니다. 변경사항들 중 일부는 이 업그레이드 가이드에서 설명되었지만 설정 파일이나 코멘트같은 다른 변경사항들은 그렇지 않았습니다. [GitHub comparison tool](https://github.com/laravel/laravel/compare/7.x...master)을 이용해 변경사항들을 쉽게 살펴보고 어떤 변경사항이 여러분에게 중요한지 살펴보세요.
