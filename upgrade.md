# 업그레이드 가이드

- [5.6에서 5.6.30 으로 업그레이드 하기](#upgrade-5.6.30)
- [5.5에서 5.6.0 으로 업그레이드 하기](#upgrade-5.6.0)

<a name="upgrade-5.6.30"></a>
## 5.6에서 5.6.30 으로 업그레이드 하기 (보안 릴리즈)

라라벨 5.6.30은 모든 사용자가 즉시 업그레이드해야할 라라벨의 보안 릴리즈입니다. 라라벨 5.6.30은 또한 쿠키 암호화와 시리얼라이제이션과 관련된 하위호환되지 않는 변경사항을 포함하고 있습니다. 따라서 다음의 사항을 잘 확인하고 애플리케이션의 업그레이드에 주의를 기울이십시오.

**이 취약점은 악의적인 사용자가 애플리케이션의 암호화 키 ('APP_KEY` 환경 변수)에 엑세스 한 경우에만 악용 될 수 있습니다. ** 일반적으로 애플리케이션 사용자가 이 값에 접근 할 수는 없습니다. 그러나 암호화 키에 접근 할 수 있는 (전) 직원이 키를 사용하여 애플리케이션을 공격 할 수 있습니다. 여러분의 암호화 키가 악의적 인 공격자에게 알려졌다고 생각된다면, **항상** 새로운 값으로 키를 변경해야합니다.

### 쿠키 시리얼라이제이션

라라벨 5.6.30 버전은 모든 쿠키값에 대한 시리얼라이제이션 / 언시리얼라이제이션을 비활성화 시킵니다. 모든 라라벨 쿠키는 암호화되어 서명되기 때문에 일반적으로 쿠키 값은 클라이언트의 변조로 부터 안전하다고 생각됩니다. **그러나, 애플리케이션의 암호화키가 악의적인 사용자에게 넘어갔다면 해당 사용자가 암호화 키를 사용하여 쿠키값을 생성하고 취약적을 이용하여 PHP 객체의 시리얼라이제이션 / 언시리얼라이제이션을 통해서 객체를 상속받아 애플리케이션에 임의의 클래스 메소드를 호출할 수도 있습니다.**

모든 쿠키 값에 대한 시리얼라이제이션을 비활성화하면 애플리케이션의 모든 세션이 유효하지 않게 되므로 사용자는 (`remember_token` 을 설정하지 않은 경우라면) 애플리케이션에 다시 로그인해야합니다. (이 경우에는 사용자는 자동으로 새로운 세션으로 로그인합니다.) 또한 애플리케이션에서 설정한 다른 암호화 된 쿠키가 유효하지 않다고 판단됩니다. 이 때문에, 애플리케이션에 로직을 추가하여 사용자가 정의한 쿠키 값이 예상한 값의 리스트와 일치하는지 확인하도록 하십시오. 그렇지 않다면 이 값들은 무효처리 해야합니다.

#### 쿠키 시리얼라이제이션 설정하기

이 취약점은 애플리케이션의 암호화 키에 대한 접근 없이는 악용될 수 없으므로, 애플리케이션이 이러한 변경사항과 호환되도록 암호화된 쿠키의 시리얼라이제이션을 다시 활성화 하는 방법이 있습니다. 쿠키의 시리얼라이제이션 사용을 활성화 / 비활성화 하려면 `App\Http\Middleware\EncryptCookies` [미들웨어](https://github.com/laravel/laravel/blob/master/app/Http/Middleware/EncryptCookies.php)의 `serialize` 속성을 static 으로 변경하면 됩니다:

    /**
     * Indicates if cookies should be serialized.
     *
     * @var bool
     */
    protected static $serialize = true;

> **Note:** 암호화된 쿠키의 시리얼라이제이션을 활성화 했을 때, 악의적인 사용자가 암호화 키에 접근할 수 있게 되면 애플리케이션은 공격 받기 쉽습니다. 키가 악의적인 사용자에게 노출되어 있다고 생각되면 암호화된 쿠키 시리얼라이제이션 기능을 사용하기 전에 키를 새로운 값으로 변경하십시오.

### Dusk 4.0.0

쿠키를 시리얼라즈 하지 않는 Dusk 4.0.0 가 릴리즈되었습니다. 쿠키 시리얼라이제이션을 활성화 하고자 한다면, Dusk 3.0.0 을 계속 사용하십시오. 그렇지 않다면 Dusk 4.0.0 으로 업그레이드 하십시오.

### Passport 6.0.7

새로운 `Laravel\Passport\Passport::withoutCookieSerialization()` 메소드를 제공하는 Passport 6.0.7 이 릴리즈 되었습니다. 이제 쿠키 시리얼라이제이션을 비활성화 한 뒤에, 이 메소드를 애플리케이션의 `AppServiceProvider` 안에서 호출해야 합니다.

<a name="upgrade-5.6.0"></a>
## 5.5에서 5.6.0 으로 업그레이드 하기

#### 업그레이드 예상 시간 : 약 10-30분

> {note} 가능한 모든 변경 내용을 기록하려고 했습니만, 변경 사항 중 일부는 프레임워크의 명확하지 않은 부분에서 이루어 지기 때문에 이중 일부가 실제 애플리케이션에 영향을 끼칠 수도 있습니다.

### PHP

라라벨 5.6은 PHP 7.1.3 이상을 필요로합니다.

### 의존성 업데이트

`composer.json` 파일에 있는 `laravel/framework` 의존성을 `5.6.*`으로 변경하고, `fideloper/proxy` 의존성을 `^4.0` 으로 업데이트 합니다.

또한, 다양한 라라벨 추가 패키지들을 사용하고 있다면, 최신버전으로 업그레이드 해야 합니다:

- Dusk (`^3.0`으로 업그레이드)
- Passport (`^6.0` 으로 업그레이드)
- Scout (`^4.0` 으로 업그레이드)


또한, 애플리케이션에서 사용하는 써드파티 패키지를 확인하고 라라벨 5.6를 지원하는 적절한 버전을 사용하고 있는지 확인하십시오.

#### Symfony 4

라라벨에서 사용되는 모든 Symfony 컴포넌트가 `^4.0` 리리즈 시리즈로 업그레이드되었습니다. 애플리케이션에서 Symfony 컴포넌트를 직접 다루는 경우 [Symfony 변경 사항](https://github.com/symfony/symfony/blob/master/UPGRADE-4.0.md)을 확인해야 합니다.

#### PHPUnit

`phpunit/phpunit`의존성을 `^7.0` 으로 업데이트 해야 합니다.

### 배열

#### `Arr::wrap` 메소드

`null`을 반환하던 `Arr::wrap` 메소드는 이제 빈 배열을 반환합니다.

### Artisan 명령어

#### `optimize` 명령어

이전 버전에서 deprecated 되었던 `optimize` 아티즌 명령어가 이제 제거되었습니다. OPCache를 포함한 PHP 자체의 최근의 개선으로 인해서 `optimize` 명령어는 더 이상 관련 성능상의 이점을 제공하지 않습니다. 따라서 따라서 `composer.json` 파일에서 `scripts` 옵션에서 `php artisan optimize` 를 제거 할 수 있습니다.

### Blade

#### HTML Entity 인코딩

이전 버전의 라라벨에서는, 블레이드 템플릿(그리고 `e` 헬퍼 메소드에서는) HTML 엔티티를 이중으로 인코딩 하지 않았습니다. 이는 `htmlspecialchars` 함수의 기본 동작이 아니었으며, 콘텐츠를 렌더링 할 때나, 자바스크립트 프레임워크에 JSON 데이터를 전달할 때 예기치 않은 동작을 일으킬 수 있습니다.

라라벨 5.6에서는 블레이드 템플릿과 `e` 헬퍼는 기본적으로 특수 문자를 이중으로 인코딩 합니다. 이로 인해서 이 기능의 기본 동작이 `htmlspecialchars` 함수 동작과 달라집니다. 이중 인코딩을 원하지 않고 이전의 동작을 유지하려면, `Blade::withoutDoubleEncoding` 메소드를 사용하면 됩니다:

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\Blade;
    use Illuminate\Support\ServiceProvider;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            Blade::withoutDoubleEncoding();
        }
    }

### 캐시

#### `tooManyAttempts` 메소드를 사용한 속도 제한

사용되지 않는 `$decayMinutes` 파라미터가 메소드에서 제거되었습니다. 캐시를 직접 구현한 경우, 이 메소드의 오버라이딩 하였다면, 메소드 선언 부분에서 파라미터 선언부분을 제거해야합니다.

### 데이터베이스

#### Index Order Of Morph Columns

성능향상을 위해서 `morphs` 마이그레이션 메소드를 통해 생성되는 인덱스 내 컬럼 순서가 변경 되었습니다. 데이터베이스 마이그레이션 도중에 `morphs` 메소드를 사용하는 경우, 마이그레이션의 `down` 메소드를 실행하도록 시도하면 에러가 확인됩니다. 애플리케이션이 아직 개발중이라면, `migrate:fresh` 명령어를 사용하여 데이터베이스의 스키마를 새롭게 구성할 수 있습니다. 애플리케이션이 프로덕션 환경에 있다면, `morphs` 메소드에 명시적으로 인덱스 이름을 전달해야 합니다.

#### `MigrationRepositoryInterface` 메소드 추가

`MigrationRepositoryInterface` 에 `getMigrationsBatches` 메소드가 추가되었습니다. 인터페이스를 직접 구현한 경우라면, 이 메소드를 추가해야 합니다. 프레임워크에 구현된 기본사항을 참고하십시오.

### Eloquent

#### `getDateFormat` 메소드

`getDateFormat` 메소드는 이제 `protected`에서 `public`으로 변경되었습니다.

### 해싱

#### 새로운 설정 파일

모든 해싱 설정은 이제 `config/hashing.php` 설정 파일에 지정됩니다. 애플리케이션에 [기본 설정 파일](https://github.com/laravel/laravel/blob/master/config/hashing.php)을 복사해서 넣으십시오. Most likely, you should maintain the `bcrypt` driver as your default driver. However, `argon` is also supported.

### 헬퍼

#### `e` 헬퍼 함수

이전 버전의 라라벨에서는, 블레이드 템플릿(그리고 `e` 헬퍼 메소드에서는) HTML 엔티티를 이중으로 인코딩 하지 않았습니다. 이는 `htmlspecialchars` 함수의 기본 동작이 아니었으며, 콘텐츠를 렌더링 할 때나, 자바스크립트 프레임워크에 JSON 데이터를 전달할 때 예기치 않은 동작을 일으킬 수 있습니다.

라라벨 5.6에서는 블레이드 템플릿과 `e` 헬퍼는 기본적으로 특수 문자를 이중으로 인코딩 합니다. 이로 인해서 이 기능의 기본 동작이 `htmlspecialchars` 함수 동작과 달라집니다. 이중 인코딩을 원하지 않고 이전의 동작을 유지하려면, `e` 헬퍼 함수의 두번째 인자에 `false`를 전달하면 됩니다:

    <?php echo e($string, false); ?>

### 로깅

#### 새로운 설정 파일

로깅과 관련된 모든 설정은 이제 `config/logging.php` 설정 파일에 지정됩니다. 애플리케이션에 [기본 설정 파일](https://github.com/laravel/laravel/blob/master/config/logging.php)을 복사해서 넣고, 필요한 설정을 구성하십시오.

`config/app.php` 설정 파일의 `log` 와 `log_level` 설정 옵션은 제거되었습니다.

#### `configureMonologUsing` 메소드

애플리케이션에서 Monolog 인스턴스를 커스터마이징 하기 위해서 `configureMonologUsing` 메소드를 사용했다면, `custom` 로그 채널을 생성하십시오. 커스텀 채널을 생성하기 위한 보다 자세한 내용은 [로깅 문서](/docs/5.6/logging#creating-custom-channels)를 참고하십시오.

#### Log `Writer` 클래스

`Illuminate\Log\Writer` 클래스는 `Illuminate\Log\Logger` 으로 이름이 변경되었습니다. 애플리케이션의 클래스중 하나에서 이 클래스를 의존하고 있었다면, 새로운 이름으로 변경해야 합니다. 또는, 대신에, `Psr\Log\LoggerInterface` 인터페이스를 사용하는 것을 고려해보십시오.

#### `Illuminate\Contracts\Logging\Log` 인터페이스

이 인터페이스는 `Psr\Log\LoggerInterface` 인터페이스의 복제본과 같으므로, 제거되었습니다. 대신에 `Psr\Log\LoggerInterface` 인터페이스를 사용하십시오.

### 메일

#### `withSwiftMessage` 콜백

이전 버전의 라라벨에서는 `withSwiftMessage` 메소드를 호출하여 등록된 Swift 메세지를 커스터마이징 하는 콜백은 내용이 이미 인코딩되어 메세지에 추가된 _다음에_ 호출되었습니다. 이 콜백은 이제 콘텐츠가 추가되기 _전에_ 호출되므로, 필요한 경우 인코딩이나, 기타 메세지 옵션을 커스터마이징 할 수 있습니다.

### 페이지네이션

#### Bootstrap 4

페이지네이터의 의해서 생성되는 페이지 링크는 이제 기본적으로 부트스트랩 4버전에 호환됩니다. 페이지네이터가 부트스트랩 3 링크를 생성하도록 하려면, `AppServiceProvider` 의 `boot` 메소드에서 `Paginator::useBootstrapThree` 메소드를 호출하십시오:

    <?php

    namespace App\Providers;

    use Illuminate\Pagination\Paginator;
    use Illuminate\Support\ServiceProvider;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            Paginator::useBootstrapThree();
        }
    }

### Resources

#### `original` 속성

[resource responses-응답](/docs/5.6/eloquent-resources)의 `original` 속성은 이제 JSON 문자열 / 배열 대신에 원래의 모델로 설정됩니다. 이를 통해서 테스팅 과정에서 response-응답의 모델을 확인하기가 수월해집니다.

### 라우팅

#### 새롭게 생성된 모델 반환하기

새롭게 생성 된 Eloquent 모델을 라우트에서 직접 반환하게 되면 response-응답의 상태코드가 자동으로 `200` 대신 `201` 로 설정됩니다. 애플리케이션의 테스트에서 명시적으로 `200` response-응답을 기대하는 경우에 해당 테스트가 `201`을 예상하도록 수정해야합니다.

### 신뢰할 수 있는 프록시 설정

Symfony HttpFoundation의 신뢰할 수 있는 프록시 기능이 변경되었기 때문에, 애플리케이션의 `App\Http\Middleware\TrustProxies` 미들웨어를 조금 수정해야합니다.

이전버전에는 배열이었던 `$headers` 속성은 이제, 몇가지의 서로 다는 유형의 값을 지정가능합니다. 예를 들어 모든 헤더를 신뢰하려면 `$headers` 속성을 다음처럼 수정하면 됩니다:

    use Illuminate\Http\Request;

    /**
     * The headers that should be used to detect proxies.
     *
     * @var int
     */
    protected $headers = Request::HEADER_X_FORWARDED_ALL;

사용가능한 `$headers` 값에 대한 보다 다세한 사항은, [신뢰가능한 프록시](/docs/5.6/requests#configuring-trusted-proxies) 문서를 참고하십시오.

### Validation-유효성 검사

#### `ValidatesWhenResolved` 인터페이스

`$request->validate()` 메소드와의 충돌을 피하기 위해서 `ValidatesWhenResolved` 인터페이스와 / trait-트레이트의 `validate` 메소드는 `validateResolved` 으로 이름이 변경되었습니다.

### 기타

또한 `laravel/laravel` [GitHub repository](https://github.com/laravel/laravel) GitHub 저장소에서 변경사항을 확인하는 것이 좋습니다. 이러한 변경사항이 꼭 필요하지는 않지만, 여러분의 애플리케이션을 이 변경사항들에 맞추어 항상 최신의 상태로 유지하고자 할 수도 있습니다. 변경사항 중 일부는 이 업그레이드 가이드에서 다루지만, 설정 파일이나, 설명의 변경같은 경우 일부는 문서에서 기술하지 않을 수도 있습니다. [GitHub 에서 Diff 툴](https://github.com/laravel/laravel/compare/5.5...5.6)을 사용하여 변경사항을 보다 쉽게 확인하고, 필요한 업데이트를 적용할 수도 있습니다.
