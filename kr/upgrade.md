# Upgrade Guide
# 업그레이드 가이드

- [Upgrading To 5.7.0 From 5.6](#upgrade-5.7.0)
- [5.6에서 5.7.0 으로 업그레이드 하기](#upgrade-5.7.0)

<a name="upgrade-5.7.0"></a>
## Upgrading To 5.7.0 From 5.6
## 5.6에서 5.7.0 으로 업그레이드 하기

#### Estimated Upgrade Time: 10 - 15 Minutes
#### 업그레이드 예상 시간 : 약 10-15분

> {note} We attempt to document every possible breaking change. Since some of these breaking changes are in obscure parts of the framework only a portion of these changes may actually affect your application.

> {note} 가능한 모든 변경 내용을 기록하려고 했습니만, 변경 사항 중 일부는 프레임워크의 명확하지 않은 부분에서 이루어 지기 때문에 이중 일부가 실제 애플리케이션에 영향을 끼칠 수도 있습니다.

### Updating Dependencies
### 의존성 업데이트

Update your `laravel/framework` dependency to `5.7.*` in your `composer.json` file.

`composer.json` 파일에 있는 `laravel/framework` 의존성을 `5.7.*`으로 업데이트합니다.

If you are using Laravel Passport, you should update your `laravel/passport` dependency to `^7.0` in your `composer.json` file.

만약 Laravel Passport를 사용하고 있다면 `composer.json` 파일에 있는 `laravel/passport`의 의존성을 `^7.0`으로 업데이트 합니다

Of course, don't forget to examine any 3rd party packages consumed by your application and verify you are using the proper version for Laravel 5.7 support.

또한, 애플리케이션에서 사용하는 써드파티 패키지를 확인하고 라라벨 5.7를 지원하는 적절한 버전을 사용하고 있는지 확인하십시오.

### Application
### 어플리케이션

#### The `register` Method
#### `register` 메소드

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The unused `options` argument of the `Illuminate\Foundation\Application` class' `register` method has been removed. If you are overriding this method, you should update your method's signature:

`Illuminate\Foundation\Application` 클래스의 `register` 메소드의 사용되지 않는 `options` 인자는 제거되었습니다. 이 메소드를 재정의하는 경우 메소드를 업데이트해야합니다.

    /**
     * Register a service provider with the application.
     *
     * @param  \Illuminate\Support\ServiceProvider|string  $provider
     * @param  bool   $force
     * @return \Illuminate\Support\ServiceProvider
     */
    public function register($provider, $force = false);

### Artisan
### Artisan

#### Scheduled Job Connection & Queues
#### Scheduled Job Connection & Queues

**Likelihood Of Impact: Low**
**영향 가능성 : 낮음**

The `$schedule->job` method now respects the `queue` and `connection` properties on the job class if a connection / job is not explicitly passed into the `job` method.

`$schedule->job` 메소드는 이제 커넥션과 작업이 명시적으로 `job` 메소드로 전달되지 않으면 작업 클래스의 `queue` 와 `connection` 속성을 우선합니다.

Generally, this should be considered a bug fix; however, it is listed as a breaking change out of caution. [Please let us know if you encounter any issues surrounding this change](https://github.com/laravel/framework/pull/25216).

일반적으로 이것은 버그 수정으로 봐야하지만 주의해야 하는 변경사항으로 분류됩니다. [이 변경과 관련하여 문제가 발생하면 알려주십시오](https://github.com/laravel/framework/pull/25216).

### Assets
### Assets

#### Asset Directory Flattened
#### Asset 디렉토리의 병합

**Likelihood Of Impact: None**
**영향 가능성 : 없음**

For new Laravel 5.7 applications, the assets directory that contains the scripts and styles has been flattened into the `resources` directory. This **will not** affect existing applications and does not require changes to your existing applications.

새로운 Laravel 5.7의 경우, 스크립트 및 스타일이 포함 된 assets 디렉토리가 `resources` 디렉토리로 병합되었습니다. 이것은 기존의 어플리케이션에 영향을 주지 않으며 기존 어플리케이션을 **변경할 필요가 없습니다**.

However, if you wish to make this change, you should move all files from the `resources/assets/*` directory up one level:

그러나 이 변경을 반영하기를 원한다면 `resources/assets/*` 디렉토리에 있는 모든 파일을 한 레벨 위로 이동해야합니다 :

- From `resources/assets/js/*` to `resources/js/*`
- From `resources/assets/sass/*` to `resources/sass/*`

- `resources/assets/js/*` 에서 `resources/js/*` 으로 이동
- `resources/assets/sass/*` 에서 `resources/sass/*` 으로 이동

Then, update any reference to the old directories in your `webpack.mix.js` file:

그런 다음 `webpack.mix.js` 파일에서 이전 디렉토리에 대한 경로를 업데이트하십시오.

    mix.js('resources/js/app.js', 'public/js')
       .sass('resources/sass/app.scss', 'public/css');

### Authentication
### 인증(Authentication)

#### The `Authenticate` Middleware
#### `Authenticate` 미들웨어

**Likelihood Of Impact: Low**
**영향 가능성 : 낮음**

The `authenticate` method of the `Illuminate\Auth\Middleware\Authenticate` middleware has been updated to accept the incoming `$request` as its first argument. If you are overriding this method in your own `Authenticate` middleware, you should update your middleware's signature:

`Illuminate\Auth\Middleware\Authenticate` 미들웨어의 `authenticate` 메소드는 `$request` 를 첫 번째 인수로 받도록 업데이트되었습니다. 자신의 `Authenticate` 미들웨어에서 이 메소드를 재정의한다면, 미들웨어를 업데이트해야합니다 :

    /**
     * Determine if the user is logged in to any of the given guards.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  array  $guards
     * @return void
     *
     * @throws \Illuminate\Auth\AuthenticationException
     */
    protected function authenticate($request, array $guards)

#### The `ResetsPasswords` Trait
#### `ResetsPasswords` Trait

**Likelihood Of Impact: Low**
**영향 가능성 : 낮음**

The protected `sendResetResponse` method of the `ResetsPasswords` trait now accepts the incoming `Illuminate\Http\Request` as its first argument. If you are overriding this method, you should update your method's signature:

`ResetsPasswords` Trait의 protected `sendResetResponse` 메소드는 `Illuminate\Http\Request` 를 첫 번째 인수로 받습니다. 이 메소드를 재정의하는 경우 메소드를 업데이트해야합니다.

    /**
     * Get the response for a successful password reset.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  string  $response
     * @return \Illuminate\Http\RedirectResponse|\Illuminate\Http\JsonResponse
     */
    protected function sendResetResponse(Request $request, $response)

#### The `SendsPasswordResetEmails` Trait
#### `SendsPasswordResetEmails` Trait

**Likelihood Of Impact: Low**
**영향 가능성 : 낮음**

The protected `sendResetLinkResponse` method of the `SendsPasswordResetEmails` trait now accepts the incoming `Illuminate\Http\Request` as its first argument. If you are overriding this method, you should update your method's signature:

`SendsPasswordResetEmails` Trait의 protected `sendResetLinkResponse` 메소드는 `Illuminate\Http\Request` 를 첫 번째 인수로 받습니다. 이 메소드를 재정의하는 경우 메소드를 업데이트해야합니다.

    /**
     * Get the response for a successful password reset link.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  string  $response
     * @return \Illuminate\Http\RedirectResponse|\Illuminate\Http\JsonResponse
     */
    protected function sendResetLinkResponse(Request $request, $response)

### Authorization
### 권한 부여(Authorization)

#### The `Gate` Contract
#### `Gate` Contract

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The `raw` method was changed from `protected` to `public` visibility. In addition, it [was added to the `Illuminate\Contracts\Auth\Access\Gate` contract](https://github.com/laravel/framework/pull/25143):

`raw` 메소드는 `protected` 에서 `public` 으로 변경되었습니다. 또한 [`IlluminateContractsAuthAccessGate` Contract 에 추가되었습니다](https://github.com/laravel/framework/pull/25143).

    /**
     * Get the raw result from the authorization callback.
     *
     * @param  string  $ability
     * @param  array|mixed  $arguments
     * @return mixed
     */
    public function raw($ability, $arguments = []);

If you are implementing this interface, you should add this method to your implementation.

만약 이 인터페이스를 구현하는 경우 이 메소드를 구현체에 추가해야합니다.

### Blade
### 블레이드

#### The `or` Operator
#### `or` 연산자

**Likelihood Of Impact: High**
**영향 가능성 : 높음**

The Blade "or" operator has been removed in favor of PHP's built-in `??` "null coalesce" operator, which has the same purpose and functionality:

Blade "or" 연산자는 같은 목적과 기능을 가진 PHP의 내장 된 `??` "null coalesce" 연산자를 위해 제거되었습니다 :

    // Laravel 5.6...
    {{ $foo or 'default' }}

    // Laravel 5.7...
    {{ $foo ?? 'default' }}

### Cache
### 캐시

**Likelihood Of Impact: Very High**
**영향 가능성 : 매우 높음**

A new `data` directory has been added to `storage/framework/cache`. You should create this directory in your own application:

새로운 `data` 디렉토리가 `storage /framework /cache` 에 추가되었습니다. 자신의 어플리케이션에 이 디렉토리를 만들어야합니다 :

    mkdir -p storage/framework/cache/data

Then, add a [.gitignore](https://github.com/laravel/laravel/blob/76369205c8715a4a8d0d73061aa042a74fd402dc/storage/framework/cache/data/.gitignore) file to the newly created `data` directory:

그런 다음 새로 만든 `data` 디렉토리에 [.gitignore](https://github.com/laravel/laravel/blob/76369205c8715a4a8d0d73061aa042a74fd402dc/storage/framework/cache/data/.gitignore) 파일을 추가하십시오.

    cp storage/framework/cache/.gitignore storage/framework/cache/data/.gitignore

Finally, ensure that the [storage/framework/cache/.gitignore](https://github.com/laravel/laravel/blob/76369205c8715a4a8d0d73061aa042a74fd402dc/storage/framework/cache/.gitignore) file is updated as follows:

마지막으로 [storage/framework/cache/.gitignore](https://github.com/laravel/laravel/blob/76369205c8715a4a8d0d73061aa042a74fd402dc/storage/framework/cache/.gitignore) 파일이 다음과 같이 업데이트되었는지 확인하십시오.

    *
    !data/
    !.gitignore

### Carbon
### Carbon

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

Carbon "macros" are now handled by the Carbon library directly instead of Laravel's extension of the library. We do not expect this to break your code; however, [please make us aware of any problems you encounter related to this change](https://github.com/laravel/framework/pull/23938).

Carbon "macros"는 현재 Laravel의 라이브러리 확장기능 대신 Carbon 라이브러리에 의해 직접 처리됩니다. 우리는 이것이 당신의 코드를 망가뜨릴 것이라고 생각하지는 않습니다. 그러나 [이 변경과 관련하여 발생하는 문제가 있다면 알려주십시오](https://github.com/laravel/framework/pull/23938).

### Collections
### 컬렉션(Collections)

#### The `split` Method
#### `split` 메소드

**Likelihood Of Impact: Low**
**영향 가능성 : 낮음**

The `split` method [has been updated to always return the requested number of "groups"](https://github.com/laravel/framework/pull/24088), unless the total number of items in the original collection is less than the requested collection count. Generally, this should be considered a bug fix; however, it is listed as a breaking change out of caution.

원래 컬렉션에있는 항목의 총 수가 요청 된 컬렉션 수보다 작은 경우를 제외하고는 `split` 메소드는 [요청 된 수의 "그룹"을 항상 리턴하도록 업데이트되었습니다](https://github.com/laravel/framework/pull/24088). 일반적으로 이것은 버그 수정으로 봐야하지만 주의해야 하는 변경사항으로 분류됩니다.

### Cookie
### Cookie

#### `Factory` Contract Method Signature
#### `Factory` Contract 메소드 사용법

**Likelihood Of Impact: Very Low**
**영향 가능성 : 낮음**

The signatures of the `make` and `forever` methods of the `Illuminate\Contracts\Cookie\Factory` interface [have been changed](https://github.com/laravel/framework/pull/23200). If you are implementing this interface, you should update these methods in your implementation.

`Illuminate\Contracts\Cookie\Factory` 인터페이스의 `make` 및 `forever` 메소드가 [변경되었습니다](https://github.com/laravel/framework/pull/23200). 이 인터페이스를 구현하는 경우 구현시 이러한 메소드를 업데이트해야합니다.

### Database
### Database

#### The `softDeletesTz` Migration Method
#### `softDeletesTz` 마이그레이션 메소드

**Likelihood Of Impact: Low**
**영향 가능성 : 낮음**

The schema table builder's `softDeletesTz` method now accepts the column name as its first argument, while the `$precision` has been moved to the second argument position:

이제 스키마 테이블 빌더의 `softDeletesTz` 메소드는 기존의 `$precision` 이 두번째 인자 위치로 옮겨지고 첫 번째 인자로 컬럼의 이름을 입력받습니다 :

    /**
     * Add a "deleted at" timestampTz for the table.
     *
     * @param  string  $column
     * @param  int  $precision
     * @return \Illuminate\Support\Fluent
     */
    public function softDeletesTz($column = 'deleted_at', $precision = 0)

#### The `ConnectionInterface` Contract
#### `ConnectionInterface` Contract

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The `Illuminate\Contracts\Database\ConnectionInterface` contract's `select` and `selectOne` method signatures have been updated to accommodate the new `$useReadPdo` argument:

`Illuminate\Contracts\Database\ConnectionInterface` contract의 `select` 및 `selectOne` 메소드가 추가적으로 `$useReadPdo` 인수를 받을 수 있게 업데이트되었습니다 :

    /**
     * Run a select statement and return a single result.
     *
     * @param  string  $query
     * @param  array   $bindings
     * @param  bool  $useReadPdo
     * @return mixed
     */
    public function selectOne($query, $bindings = [], $useReadPdo = true);

    /**
     * Run a select statement against the database.
     *
     * @param  string  $query
     * @param  array   $bindings
     * @param  bool  $useReadPdo
     * @return array
     */
    public function select($query, $bindings = [], $useReadPdo = true);

In addition, the `cursor` method was added to the contract:

또한 `cursor` 메소드가 contract에 추가되었습니다.

    /**
     * Run a select statement against the database and returns a generator.
     *
     * @param  string  $query
     * @param  array  $bindings
     * @param  bool  $useReadPdo
     * @return \Generator
     */
    public function cursor($query, $bindings = [], $useReadPdo = true);

If you are implementing this interface, you should add this method to your implementation.

만약 이 인터페이스를 구현하는 경우 이 메소드를 구현체에 추가해야합니다.

#### Migration Command Output
#### 마이그레이션 명령 출력

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The core migration commands have been [updated to set the output instance on the migrator class](https://github.com/laravel/framework/pull/24811). If you were overriding or extending the migration commands, you should remove references to `$this->migrator->getNotes()` and use `$this->migrator->setOutput($this->output)` instead.

코어 마이그레이션 명령이 [Migrator 클래스의 출력 인스턴스를 설정하도록 업데이트되었습니다](https://github.com/laravel/framework/pull/24811). 마이그레이션 명령을 재정의하거나 확장했다면 `$this->migrator->getNotes()` 에 대한 참조를 제거하고 대신 `$this->migrator->setOutput($this->output)` 를 사용해야 합니다.

#### SQL Server Driver Priority
#### SQL Server 드라이버 우선 순위

**Likelihood Of Impact: Low**
**영향 가능성 : 낮음**

Prior to Laravel 5.7, the `PDO_DBLIB` driver was used as the default SQL Server PDO driver. This driver is considered deprecated by Microsoft. As of Laravel 5.7, `PDO_SQLSRV` will be used as the default driver if it is available. Alternatively, you may choose to use the `PDO_ODBC` driver:

Laravel 5.7 이전에는 `PDO_DBLIB` 드라이버가 기본 SQL Server PDO 드라이버로 사용되었습니다. 이 드라이버는 Microsoft에서 더 이상 사용하지 않는 것으로 간주됩니다. Laravel 5.7부터는 `PDO_SQLSRV` 가 기본 드라이버로 사용됩니다. 또는 `PDO_ODBC` 드라이버를 사용할 수도 있습니다 :

    'sqlsrv' => [
        // ...
        'odbc' => true,
        'odbc_datasource_name' => 'your-odbc-dsn',
    ],

If neither of these drivers are available, Laravel will use the `PDO_DBLIB` driver.

이 두 드라이버를 모두 사용할 수없을 경우 Laravel은 `PDO_DBLIB` 드라이버를 사용합니다.

#### SQLite Foreign Keys
#### SQLite 외래키

**Likelihood Of Impact: Medium**
**영향 가능성 : 보통**

SQLite does not support dropping foreign keys. For that reason, using the `dropForeign` method on a table now throws an exception. Generally, this should be considered a bug fix; however, it is listed as a breaking change out of caution.

SQLite는 외래키 삭제를 지원하지 않습니다. 이런 이유로 테이블에서 `dropForeign` 메소드를 사용하면 예외가 발생합니다. 일반적으로 이것은 버그 수정으로 봐야하지만 주의해야 하는 변경사항으로 분류됩니다.

If you run your migrations on multiple types of databases, consider using `DB::getDriverName()` in your migrations to skip unsupported foreign key methods for SQLite.

여러 유형의 데이터베이스에서 마이그레이션을 실행하는 경우 마이그레이션에서 `DB::getDriverName()` 을 사용하여 SQLite에 대해 지원되지 않는 외래키 메소드를 건너 뛰는 것을 고려하십시오.

### Debug
### 디버그

#### Dumper Classes
#### 덤퍼 클래스

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The `Illuminate\Support\Debug\Dumper` and `Illuminate\Support\Debug\HtmlDumper` classes have been removed in favor of using Symfony's native variable dumpers: `Symfony\Component\VarDumper\VarDumper` and `Symfony\Component\VarDumper\Dumper\HtmlDumper`.

`Illuminate\Support\Debug\Dumper` 와 `Illuminate\Support\Debug\HtmlDumper` 클래스는 Symfony의 기본 변수 덤프 `Symfony\Component\VarDumper\VarDumper` 와 `Symfony\Component\VarDumper\Dumper\HtmlDumper` 를 사용하기 위해 제거되었습니다.

### Eloquent
### Eloquent

#### `latest` / `oldest` 메소드

**Likelihood Of Impact: Low**
**영향 가능성 : 낮음**

The Eloquent query builder's `latest` and `oldest` methods have been updated to respect custom "created at" timestamp columns that may be specified on your Eloquent models. Generally, this should be considered a bug fix; however, it is listed as a breaking change out of caution.

Eloquent 쿼리 빌더의 `latest` 및 `oldest` 메소드는 Eloquent 모델에서 지정할 수있는 사용자 정의 "created at" 타임스탬프 컬럼을 우선시 하도록 업데이트되었습니다. 일반적으로 이것은 버그 수정으로 봐야하지만 주의해야 하는 변경사항으로 분류됩니다.

#### The `wasChanged` Method
#### `wasChanged` 메소드

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

An Eloquent model's changes are now available to the `wasChanged` method **before** firing the `updated` model event. Generally, this should be considered a bug fix; however, it is listed as a breaking change out of caution. [Please let us know if you encounter any issues surrounding this change](https://github.com/laravel/framework/pull/25026).

Eloquent 모델은 `updated` 모델 이벤트가 발생하기 전에 **`wasChanged` 메소드** 를 사용할 수 있게 변경되었습니다. 일반적으로 이것은 버그 수정으로 봐야하지만 주의해야 하는 변경사항으로 분류됩니다. [이 변경 사항과 관련하여 문제가 발생하는 경우 알려주십시오](https://github.com/laravel/framework/pull/25026).

#### PostgreSQL Special Float Values
#### PostgreSQL의 특수 부동 소수점 값

**Likelihood Of Impact: Low**
**영향 가능성 : 낮음**

PostgreSQL supports the float values `Infinity`, `-Infinity` and `NaN`. Prior to Laravel 5.7, these were cast to `0` when the Eloquent casting type for the column was `float`, `double`, or `real`.

PostgreSQL은 부동 소수점 값 `Infinity`, `-Infinity` 와 `NaN` 을 지원합니다. Laravel 5.7 이전에는 컬럼의 Eloquent 캐스팅 타입이 `float`, `double`또는 `real`인 경우 `0`으로 변환되었습니다.

As of Laravel 5.7, these values will be cast to the corresponding PHP constants `INF`, `-INF`, and `NAN`.

Laravel 5.7에서 이 값들은 상응하는 PHP 상수 `INF`, `-INF`, `NAN`으로 변환 될 것입니다.

### Email Verification
### 이메일 검증

**Likelihood Of Impact: Optional**
**영향의 가능성 : 선택사항**

If you choose to use Laravel's new [email verification services](/docs/{{version}}/verification), you will need to add additional scaffolding to your application. First, add the `VerificationController` to your application: [App\Http\Controllers\Auth\VerificationController](https://github.com/laravel/laravel/blob/master/app/Http/Controllers/Auth/VerificationController.php).

Laravel의 새로운 [이메일 검증 서비스](/docs/{{version}}/verification)를 사용하기로 결정하였다면 어플리케이션의 추가 스캐폴딩을 추가해야합니다. 먼저 어플리케이션에 `VerificationController`를 추가하십시오 : [App\Http\Controllers\Auth\VerificationController](https://github.com/laravel/laravel/blob/master/app/Http/Controllers/Auth/VerificationController.php).

You will also need to modify your `App\User` model to implement the `MustVerifyEmail` contract:

또한 `App\User` 모델을 수정하여 `MustVerifyEmail` contract를 구현해야합니다 :

    <?php

    namespace App;

    use Illuminate\Notifications\Notifiable;
    use Illuminate\Contracts\Auth\MustVerifyEmail;
    use Illuminate\Foundation\Auth\User as Authenticatable;

    class User extends Authenticatable implements MustVerifyEmail
    {
        use Notifiable;

        // ...
    }

In order to use the `verified` middleware so that only verified users may access a given route, you will need to update the `$routeMiddleware` property of your `app/Http/Kernel.php` file to include the new middleware:

검증 된 사용자만 지정한 라우트에 접근 할 수 있도록 `verified` 미들웨어를 사용하려면 `app/Http/Kernel.php` 파일의 `$routeMiddleware` 속성을 업데이트하여 새로운 미들웨어를 포함시켜야합니다 :

    // Within App\Http\Kernel Class...

    protected $routeMiddleware = [
        'auth' => \Illuminate\Auth\Middleware\Authenticate::class,
        'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
        'bindings' => \Illuminate\Routing\Middleware\SubstituteBindings::class,
        'can' => \Illuminate\Auth\Middleware\Authorize::class,
        'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
        'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
        'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
    ];

You will also need the verification view stub. This view should be placed at `resources/views/auth/verify.blade.php`. You may obtain the view's contents [on GitHub](https://github.com/laravel/framework/blob/5.7/src/Illuminate/Auth/Console/stubs/make/views/auth/verify.stub).

또한 인증 뷰의 스텁이 필요합니다. 이 뷰는 `resources/views/auth/verify.blade.php` 에 위치해야합니다. [GitHub에서](https://github.com/laravel/framework/blob/5.7/src/Illuminate/Auth/Console/stubs/make/views/auth/verify.stub) 이 뷰의 샘플을 얻을 수 있습니다 .

Next, your user table must contain an `email_verified_at` column to store the date and time that the email address was verified:

다음으로, 사용자 테이블은 이메일 주소가 검증 된 날짜와 시간을 저장하는 `email_verified_at` 컬럼을 포함해야합니다.

    $table->timestamp('email_verified_at')->nullable();

In order to send the email when a user is registered, you should register following events and listeners in your [App\Providers\EventServiceProvider](https://github.com/laravel/laravel/blob/master/app/Providers/EventServiceProvider.php) class:

사용자가 등록 될 때 이메일을 보내려면 [App\Providers\EventServiceProvider](https://github.com/laravel/laravel/blob/master/app/Providers/EventServiceProvider.php) 클래스에 다음 이벤트 및 리스너를 등록해야합니다 :

    use Illuminate\Auth\Events\Registered;
    use Illuminate\Auth\Listeners\SendEmailVerificationNotification;

    /**
     * The event listener mappings for the application.
     *
     * @var array
     */
    protected $listen = [
        Registered::class => [
            SendEmailVerificationNotification::class,
        ],
    ];

Finally, when calling the `Auth::routes` method, you should pass the `verify` option to the method:

마지막으로 `Auth::routes` 메소드를 호출 할 때 메소드에 `verify` 옵션을 전달해야합니다 :

    Auth::routes(['verify' => true]);

### Filesystem
### 파일시스템

#### `Filesystem` Contract Methods
#### `Filesystem` Contract 메소드

**Likelihood Of Impact: Low**
**영향 가능성 : 낮음**

The `readStream` and `writeStream` methods [have been added to the `Illuminate\Contracts\Filesystem\Filesystem` contract](https://github.com/laravel/framework/pull/23755). If you are implementing this interface, you should add these methods to your implementation.

`readStream` 과 `writeStream` 메소드는 `Illuminate\Contracts\Filesystem\Filesystem` [contract에 추가되었습니다](https://github.com/laravel/framework/pull/23755). 이 인터페이스를 구현하는 경우 이러한 메소드를 구현체에 추가해야합니다.

### Hashing
### 해싱

#### `Hash::check` Method
#### `Hash::check` 메소드

**Likelihood Of Impact: None**
**영향 가능성 : 없음**

The `check` method now **optionally** checks if the algorithm of the hash matches the configured algorithm.

이제 `check` 메소드는 해시의 알고리즘이 설정된 알고리즘과 일치하는지 체크합니다.

### Mail
### 메일

#### Mailable Dynamic Variable Casing
#### Mailable 동적 변수 캐스팅

**Likelihood Of Impact: Medium**
**영향 가능성 : 보통**

Variables that are dynamically passed to mailable views [are now automatically "camel cased"](https://github.com/laravel/framework/pull/24232), which makes mailable dynamic variable behavior consistent with dynamic view variables. Dynamic mailable variables are not a documented Laravel feature, so likelihood of impact to your application is low.

동적으로 mailable 뷰에 전달 된 변수는 [자동으로 "camel cased"](https://github.com/laravel/framework/pull/24232)되어 동적 뷰 변수와 일치하는 동적 mailable 변수의 동작을 제공합니다. 동적인 mailable 변수는 문서화 된 Laravel 기능이 아니므로 어플리케이션에 미치는 영향의 가능성이 낮습니다.

#### Template Theme
#### 템플릿 테마

**Likelihood Of Impact: Medium**
**영향 가능성 : 보통**

If you have customized the default theme styles used for Markdown mailable templates, you will need to re-publish and make your customizations again. The button color classes have been renamed from 'blue', 'green', and 'red' to 'primary', 'success', and 'error'.

Markdown Mailable 템플릿에 사용되는 기본 테마 스타일을 커스터마이징한 경우 다시 커스터마이징하여 만들고 재게시(re-publish)해야합니다. 버튼의 색상 클래스 이름이 'blue', 'green'및 'red'에서 'primary', 'success'및 'error'로 변경되었습니다.

### Queue
### 큐

#### `QUEUE_DRIVER` Environment Variable
#### `QUEUE_DRIVER` 환경 설정 변수

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The `QUEUE_DRIVER` environment variable has been renamed to `QUEUE_CONNECTION`. This should not affect existing applications that you are upgrading unless you intentionally modify your `config/queue.php` configuration file to match Laravel 5.7's.

`QUEUE_DRIVER` 환경 변수의 이름이 `QUEUE_CONNECTION` 으로 변경되었습니다. `config/queue.php` 설정 파일을 Laravel 5.7과 일치하도록 고의적으로 수정하지 않는 한, 업그레이드하고있는 기존 어플리케이션에는 영향을 미치지 않습니다.

### Routing
### 라우팅

#### The `Route::redirect` Method
#### `Route::redirect` 메소드

**Likelihood Of Impact: High**
**영향 가능성 : 높음**

The `Route::redirect` method now returns a `302` HTTP status code redirect. The `permanentRedirect` method has been added to allow `301` redirects.

`Route::redirect` 메소드가 이제 `302` HTTP 상태 코드 리다이렉션을 리턴합니다. `301` 리다이렉트를 허용하기 위해 `permanentRedirect` 메소드가 추가되었습니다.

    // Return a 302 redirect...
    Route::redirect('/foo', '/bar');

    // Return a 301 redirect...
    Route::redirect('/foo', '/bar', 301);

    // Return a 301 redirect...
    Route::permanentRedirect('/foo', '/bar');

#### The `addRoute` Method
#### `addRoute` 메소드

**Likelihood Of Impact: Low**
**영향 가능성 : 낮음**

The `addRoute` method of the `Illuminate\Routing\Router` class has been changed from `protected` to `public`.

`Illuminate\Routing\Router` 클래스의 `addRoute` 메소드가 `protected` 에서 `public`으로 변경되었습니다.

### Validation
### 유효성 검사

#### Nested Validation Data
#### 중첩 된 유효성 검사 데이터

**Likelihood Of Impact: Medium**
**영향 가능성 : 보통**


In previous versions of Laravel, the `validate` method did not return the correct data for nested validation rules. This has been corrected in Laravel 5.7:

이전 버전의 Laravel에서는 `validate` 메서드가 중첩 된 유효성 검사 규칙에 대한 올바른 데이터를 반환하지 않았습니다. 이 문제는 Laravel 5.7에서 수정되었습니다 :

    $data = Validator::make([
        'person' => [
            'name' => 'Taylor',
            'job' => 'Developer'
        ]
    ], ['person.name' => 'required'])->validate();

    dump($data);

    // Prior Behavior...
    ['person' => ['name' => 'Taylor', 'job' => 'Developer']]

    // New Behavior...
    ['person' => ['name' => 'Taylor']]

#### The `Validator` Contract
#### `Validator` Contract

**Likelihood Of Impact: Very Low**
**영향 가능성 : 매우 낮음**

The `validate` method [was added to the `Illuminate\Contracts\Validation\Validator` contract](https://github.com/laravel/framework/pull/25128):

`validate` 메소드가 [`Illuminate\Contracts\Validation\Validator` contract에 추가되었습니다](https://github.com/laravel/framework/pull/25128). :

    /**
     * Run the validator's rules against its data.
     *
     * @return array
     */
    public function validate();

If you are implementing this interface, you should add this method to your implementation.

만약 이 인터페이스를 구현하는 경우 이 메소드를 구현체에 추가해야합니다.

### Miscellaneous
### 기타

We also encourage you to view the changes in the `laravel/laravel` [GitHub repository](https://github.com/laravel/laravel). While many of these changes are not required, you may wish to keep these files in sync with your application. Some of these changes will be covered in this upgrade guide, but others, such as changes to configuration files or comments, will not be. You can easily view the changes with the [GitHub comparison tool](https://github.com/laravel/laravel/compare/5.6...master) and choose which updates are important to you.

또한 `laravel/laravel` [GitHub repository](https://github.com/laravel/laravel) GitHub 저장소에서 변경사항을 확인하는 것이 좋습니다. 이러한 변경사항이 꼭 필요하지는 않지만, 여러분의 애플리케이션을 이 변경사항들에 맞추어 항상 최신의 상태로 유지하고자 할 수도 있습니다. 변경사항 중 일부는 이 업그레이드 가이드에서 다루지만, 설정 파일이나, 설명의 변경같은 경우 일부는 문서에서 기술하지 않을 수도 있습니다. [GitHub 에서 Diff 툴](https://github.com/laravel/laravel/compare/5.6...master)을 사용하여 변경사항을 보다 쉽게 확인하고, 필요한 업데이트를 적용할 수도 있습니다.