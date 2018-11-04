# 업그레이드 가이드

- [5.6에서 5.7.0 으로 업그레이드 하기](#upgrade-5.7.0)

<a name="upgrade-5.7.0"></a>
## 5.6에서 5.7.0 으로 업그레이드 하기

#### 업그레이드 예상 시간 : 약 10-15분

> {note} 가능한 모든 변경 내용을 기록하려고 했습니만, 변경 사항 중 일부는 프레임워크의 명확하지 않은 부분에서 이루어 지기 때문에 이중 일부가 실제 애플리케이션에 영향을 끼칠 수도 있습니다.

### 의존성 업데이트

`composer.json` 파일에 있는 `laravel/framework` 의존성을 `5.7.*`으로 업데이트합니다.

만약 Laravel Passport를 사용하고 있다면 `composer.json` 파일에 있는 `laravel/passport`의 의존성을 `^7.0`으로 업데이트 합니다

또한, 애플리케이션에서 사용하는 써드파티 패키지를 확인하고 라라벨 5.7를 지원하는 적절한 버전을 사용하고 있는지 확인하십시오.

### 어플리케이션

#### `register` 메소드

**영향 가능성 : 매우 낮음**

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

#### 예약된 Job Connection 과 Queues

**영향 가능성 : 낮음**

`$schedule->job` 메소드는 이제 커넥션과 작업이 명시적으로 `job` 메소드로 전달되지 않으면 작업 클래스의 `queue` 와 `connection` 속성을 우선합니다.

일반적으로 이것은 버그 수정으로 봐야하지만 주의해야 하는 변경사항으로 분류됩니다. [이 변경과 관련하여 문제가 발생하면 알려주십시오](https://github.com/laravel/framework/pull/25216).

### Assets

#### Asset 디렉토리의 병합

**영향 가능성 : 없음**

새로운 Laravel 5.7의 경우, 스크립트 및 스타일이 포함 된 assets 디렉토리가 `resources` 디렉토리로 병합되었습니다. 이것은 기존의 어플리케이션에 영향을 주지 않으며 기존 어플리케이션을 **변경할 필요가 없습니다**.

그러나 이 변경을 반영하기를 원한다면 `resources/assets/*` 디렉토리에 있는 모든 파일을 한 레벨 위로 이동해야합니다 :

- `resources/assets/js/*` 에서 `resources/js/*` 으로 이동
- `resources/assets/sass/*` 에서 `resources/sass/*` 으로 이동

그런 다음 `webpack.mix.js` 파일에서 이전 디렉토리에 대한 경로를 업데이트하십시오.

    mix.js('resources/js/app.js', 'public/js')
       .sass('resources/sass/app.scss', 'public/css');

### 인증(Authentication)

#### `Authenticate` 미들웨어

**영향 가능성 : 낮음**

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

#### `ResetsPasswords` Trait

**영향 가능성 : 낮음**

`ResetsPasswords` Trait의 protected `sendResetResponse` 메소드는 `Illuminate\Http\Request` 를 첫 번째 인수로 받습니다. 이 메소드를 재정의하는 경우 메소드를 업데이트해야합니다.

    /**
     * Get the response for a successful password reset.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  string  $response
     * @return \Illuminate\Http\RedirectResponse|\Illuminate\Http\JsonResponse
     */
    protected function sendResetResponse(Request $request, $response)

#### `SendsPasswordResetEmails` Trait

**영향 가능성 : 낮음**

`SendsPasswordResetEmails` Trait의 protected `sendResetLinkResponse` 메소드는 `Illuminate\Http\Request` 를 첫 번째 인수로 받습니다. 이 메소드를 재정의하는 경우 메소드를 업데이트해야합니다.

    /**
     * Get the response for a successful password reset link.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  string  $response
     * @return \Illuminate\Http\RedirectResponse|\Illuminate\Http\JsonResponse
     */
    protected function sendResetLinkResponse(Request $request, $response)

### 권한 부여(Authorization)

#### `Gate` Contract

**영향 가능성 : 매우 낮음**

`raw` 메소드는 `protected` 에서 `public` 으로 변경되었습니다. 또한 [`IlluminateContractsAuthAccessGate` Contract 에 추가되었습니다](https://github.com/laravel/framework/pull/25143).

    /**
     * Get the raw result from the authorization callback.
     *
     * @param  string  $ability
     * @param  array|mixed  $arguments
     * @return mixed
     */
    public function raw($ability, $arguments = []);

만약 이 인터페이스를 구현하는 경우 이 메소드를 구현체에 추가해야합니다.

### 블레이드

#### `or` 연산자

**영향 가능성 : 높음**

Blade "or" 연산자는 같은 목적과 기능을 가진 PHP의 내장 된 `??` "null coalesce" 연산자를 위해 제거되었습니다 :

    // Laravel 5.6...
    {{ $foo or 'default' }}

    // Laravel 5.7...
    {{ $foo ?? 'default' }}

### 캐시

**영향 가능성 : 매우 높음**

새로운 `data` 디렉토리가 `storage /framework /cache` 에 추가되었습니다. 자신의 어플리케이션에 이 디렉토리를 만들어야합니다 :

    mkdir -p storage/framework/cache/data

그런 다음 새로 만든 `data` 디렉토리에 [.gitignore](https://github.com/laravel/laravel/blob/76369205c8715a4a8d0d73061aa042a74fd402dc/storage/framework/cache/data/.gitignore) 파일을 추가하십시오.

    cp storage/framework/cache/.gitignore storage/framework/cache/data/.gitignore

마지막으로 [storage/framework/cache/.gitignore](https://github.com/laravel/laravel/blob/76369205c8715a4a8d0d73061aa042a74fd402dc/storage/framework/cache/.gitignore) 파일이 다음과 같이 업데이트되었는지 확인하십시오.

    *
    !data/
    !.gitignore

### Carbon

**영향 가능성 : 매우 낮음**

Carbon "macros"는 현재 Laravel의 라이브러리 확장기능 대신 Carbon 라이브러리에 의해 직접 처리됩니다. 우리는 이것이 당신의 코드를 망가뜨릴 것이라고 생각하지는 않습니다. 그러나 [이 변경과 관련하여 발생하는 문제가 있다면 알려주십시오](https://github.com/laravel/framework/pull/23938).

### 컬렉션(Collections)

#### `split` 메소드

**영향 가능성 : 낮음**

원래 컬렉션에있는 항목의 총 수가 요청 된 컬렉션 수보다 작은 경우를 제외하고는 `split` 메소드는 [요청 된 수의 "그룹"을 항상 리턴하도록 업데이트되었습니다](https://github.com/laravel/framework/pull/24088). 일반적으로 이것은 버그 수정으로 봐야하지만 주의해야 하는 변경사항으로 분류됩니다.

### Cookie

#### `Factory` Contract 메소드 사용법

**영향 가능성 : 낮음**

`Illuminate\Contracts\Cookie\Factory` 인터페이스의 `make` 및 `forever` 메소드가 [변경되었습니다](https://github.com/laravel/framework/pull/23200). 이 인터페이스를 구현하는 경우 구현시 이러한 메소드를 업데이트해야합니다.

### Database

#### `softDeletesTz` 마이그레이션 메소드

**영향 가능성 : 낮음**

이제 스키마 테이블 빌더의 `softDeletesTz` 메소드는 기존의 `$precision` 이 두번째 인자 위치로 옮겨지고 첫 번째 인자로 컬럼의 이름을 입력받습니다 :

    /**
     * Add a "deleted at" timestampTz for the table.
     *
     * @param  string  $column
     * @param  int  $precision
     * @return \Illuminate\Support\Fluent
     */
    public function softDeletesTz($column = 'deleted_at', $precision = 0)

#### `ConnectionInterface` Contract

**영향 가능성 : 매우 낮음**

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

만약 이 인터페이스를 구현하는 경우 이 메소드를 구현체에 추가해야합니다.

#### 마이그레이션 명령 출력

**영향 가능성 : 매우 낮음**

코어 마이그레이션 명령이 [Migrator 클래스의 출력 인스턴스를 설정하도록 업데이트되었습니다](https://github.com/laravel/framework/pull/24811). 마이그레이션 명령을 재정의하거나 확장했다면 `$this->migrator->getNotes()` 에 대한 참조를 제거하고 대신 `$this->migrator->setOutput($this->output)` 를 사용해야 합니다.

#### SQL Server 드라이버 우선 순위

**영향 가능성 : 낮음**

Laravel 5.7 이전에는 `PDO_DBLIB` 드라이버가 기본 SQL Server PDO 드라이버로 사용되었습니다. 이 드라이버는 Microsoft에서 더 이상 사용하지 않는 것으로 간주됩니다. Laravel 5.7부터는 `PDO_SQLSRV` 가 기본 드라이버로 사용됩니다. 또는 `PDO_ODBC` 드라이버를 사용할 수도 있습니다 :

    'sqlsrv' => [
        // ...
        'odbc' => true,
        'odbc_datasource_name' => 'your-odbc-dsn',
    ],

이 두 드라이버를 모두 사용할 수없을 경우 Laravel은 `PDO_DBLIB` 드라이버를 사용합니다.

#### SQLite 외래키

**영향 가능성 : 보통**

SQLite는 외래키 삭제를 지원하지 않습니다. 이런 이유로 테이블에서 `dropForeign` 메소드를 사용하면 예외가 발생합니다. 일반적으로 이것은 버그 수정으로 봐야하지만 주의해야 하는 변경사항으로 분류됩니다.

여러 유형의 데이터베이스에서 마이그레이션을 실행하는 경우 마이그레이션에서 `DB::getDriverName()` 을 사용하여 SQLite에 대해 지원되지 않는 외래키 메소드를 건너 뛰는 것을 고려하십시오.

### 디버그

#### 덤퍼 클래스

**영향 가능성 : 매우 낮음**

`Illuminate\Support\Debug\Dumper` 와 `Illuminate\Support\Debug\HtmlDumper` 클래스는 Symfony의 기본 변수 덤프 `Symfony\Component\VarDumper\VarDumper` 와 `Symfony\Component\VarDumper\Dumper\HtmlDumper` 를 사용하기 위해 제거되었습니다.

### Eloquent

#### `latest` / `oldest` 메소드

**영향 가능성 : 낮음**

Eloquent 쿼리 빌더의 `latest` 및 `oldest` 메소드는 Eloquent 모델에서 지정할 수있는 사용자 정의 "created at" 타임스탬프 컬럼을 우선시 하도록 업데이트되었습니다. 일반적으로 이것은 버그 수정으로 봐야하지만 주의해야 하는 변경사항으로 분류됩니다.

#### `wasChanged` 메소드

**영향 가능성 : 매우 낮음**

Eloquent 모델은 `updated` 모델 이벤트가 발생하기 전에 **`wasChanged` 메소드** 를 사용할 수 있게 변경되었습니다. 일반적으로 이것은 버그 수정으로 봐야하지만 주의해야 하는 변경사항으로 분류됩니다. [이 변경 사항과 관련하여 문제가 발생하는 경우 알려주십시오](https://github.com/laravel/framework/pull/25026).

#### PostgreSQL의 특수 부동 소수점 값

**영향 가능성 : 낮음**

PostgreSQL은 부동 소수점 값 `Infinity`, `-Infinity` 와 `NaN` 을 지원합니다. Laravel 5.7 이전에는 컬럼의 Eloquent 캐스팅 타입이 `float`, `double`또는 `real`인 경우 `0`으로 변환되었습니다.

Laravel 5.7에서 이 값들은 상응하는 PHP 상수 `INF`, `-INF`, `NAN`으로 변환 될 것입니다.

### 이메일 검증

**영향의 가능성 : 선택사항**

Laravel의 새로운 [이메일 검증 서비스](/docs/{{version}}/verification)를 사용하기로 결정하였다면 어플리케이션의 추가 스캐폴딩을 추가해야합니다. 먼저 어플리케이션에 `VerificationController`를 추가하십시오 : [App\Http\Controllers\Auth\VerificationController](https://github.com/laravel/laravel/blob/master/app/Http/Controllers/Auth/VerificationController.php).

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

또한 인증 뷰의 스텁이 필요합니다. 이 뷰는 `resources/views/auth/verify.blade.php` 에 위치해야합니다. [GitHub에서](https://github.com/laravel/framework/blob/5.7/src/Illuminate/Auth/Console/stubs/make/views/auth/verify.stub) 이 뷰의 샘플을 얻을 수 있습니다 .

다음으로, 사용자 테이블은 이메일 주소가 검증 된 날짜와 시간을 저장하는 `email_verified_at` 컬럼을 포함해야합니다.

    $table->timestamp('email_verified_at')->nullable();

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

마지막으로 `Auth::routes` 메소드를 호출 할 때 메소드에 `verify` 옵션을 전달해야합니다 :

    Auth::routes(['verify' => true]);

### 파일시스템

#### `Filesystem` Contract 메소드

**영향 가능성 : 낮음**

`readStream` 과 `writeStream` 메소드는 `Illuminate\Contracts\Filesystem\Filesystem` [contract에 추가되었습니다](https://github.com/laravel/framework/pull/23755). 이 인터페이스를 구현하는 경우 이러한 메소드를 구현체에 추가해야합니다.

### 해싱

#### `Hash::check` 메소드

**영향 가능성 : 없음**

이제 `check` 메소드는 해시의 알고리즘이 설정된 알고리즘과 일치하는지 체크합니다.

### 메일

#### Mailable 동적 변수 캐스팅

**영향 가능성 : 보통**

동적으로 mailable 뷰에 전달 된 변수는 [자동으로 "camel cased"](https://github.com/laravel/framework/pull/24232)되어 동적 뷰 변수와 일치하는 동적 mailable 변수의 동작을 제공합니다. 동적인 mailable 변수는 문서화 된 Laravel 기능이 아니므로 어플리케이션에 미치는 영향의 가능성이 낮습니다.

#### 템플릿 테마

**영향 가능성 : 보통**

Markdown Mailable 템플릿에 사용되는 기본 테마 스타일을 커스터마이징한 경우 다시 커스터마이징하여 만들고 재게시(re-publish)해야합니다. 버튼의 색상 클래스 이름이 'blue', 'green'및 'red'에서 'primary', 'success'및 'error'로 변경되었습니다.

### 큐

#### `QUEUE_DRIVER` 환경 설정 변수

**영향 가능성 : 매우 낮음**

`QUEUE_DRIVER` 환경 변수의 이름이 `QUEUE_CONNECTION` 으로 변경되었습니다. `config/queue.php` 설정 파일을 Laravel 5.7과 일치하도록 고의적으로 수정하지 않는 한, 업그레이드하고있는 기존 어플리케이션에는 영향을 미치지 않습니다.

(역자주: 이 옵션은 기본적으로 포함된 phpunit.xml과 .env.example에서도 변경되었습니다. 만약 변경된 내역을 일치하게 다 바꾸시고 싶으시다면 이 파일들도 확인해보시기 바랍니다 )

### 라우팅

#### `Route::redirect` 메소드

**영향 가능성 : 높음**

`Route::redirect` 메소드가 이제 `302` HTTP 상태 코드 리다이렉션을 리턴합니다. `301` 리다이렉트를 허용하기 위해 `permanentRedirect` 메소드가 추가되었습니다.

    // Return a 302 redirect...
    Route::redirect('/foo', '/bar');

    // Return a 301 redirect...
    Route::redirect('/foo', '/bar', 301);

    // Return a 301 redirect...
    Route::permanentRedirect('/foo', '/bar');

#### `addRoute` 메소드

**영향 가능성 : 낮음**

`Illuminate\Routing\Router` 클래스의 `addRoute` 메소드가 `protected` 에서 `public`으로 변경되었습니다.

### 유효성 검사

#### 중첩 된 유효성 검사 데이터

**영향 가능성 : 보통**

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

#### `Validator` Contract

**영향 가능성 : 매우 낮음**

`validate` 메소드가 [`Illuminate\Contracts\Validation\Validator` contract에 추가되었습니다](https://github.com/laravel/framework/pull/25128). :

    /**
     * Run the validator's rules against its data.
     *
     * @return array
     */
    public function validate();

만약 이 인터페이스를 구현하는 경우 이 메소드를 구현체에 추가해야합니다.

### 기타

또한 `laravel/laravel` [GitHub repository](https://github.com/laravel/laravel) GitHub 저장소에서 변경사항을 확인하는 것이 좋습니다. 이러한 변경사항이 꼭 필요하지는 않지만, 여러분의 애플리케이션을 이 변경사항들에 맞추어 항상 최신의 상태로 유지하고자 할 수도 있습니다. 변경사항 중 일부는 이 업그레이드 가이드에서 다루지만, 설정 파일이나, 설명의 변경같은 경우 일부는 문서에서 기술하지 않을 수도 있습니다. [GitHub 에서 Diff 툴](https://github.com/laravel/laravel/compare/5.6...master)을 사용하여 변경사항을 보다 쉽게 확인하고, 필요한 업데이트를 적용할 수도 있습니다.