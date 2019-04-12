# Upgrade Guide
# 업그레이드 가이드

- [Upgrading To 5.6.30 From 5.6](#upgrade-5.6.30)
- [5.6에서 5.6.30 으로 업그레이드 하기](#upgrade-5.6.30)
- [Upgrading To 5.6.0 From 5.5](#upgrade-5.6.0)
- [5.5에서 5.6.0 으로 업그레이드 하기](#upgrade-5.6.0)

<a name="upgrade-5.6.30"></a>
## Upgrading To 5.6.30 From 5.6 (Security Release)
## 5.6에서 5.6.30 으로 업그레이드 하기 (보안 릴리즈)

Laravel 5.6.30 is a security release of Laravel and is recommended as an immediate upgrade for all users. Laravel 5.6.30 also contains a breaking change to cookie encryption and serialization logic, so please read the following notes carefully when upgrading your application.

라라벨 5.6.30은 모든 사용자가 즉시 업그레이드해야할 라라벨의 보안 릴리즈입니다. 라라벨 5.6.30은 또한 쿠키 암호화와 시리얼라이제이션과 관련된 하위호환되지 않는 변경사항을 포함하고 있습니다. 따라서 다음의 사항을 잘 확인하고 애플리케이션의 업그레이드에 주의를 기울이십시오.

**This vulnerability may only be exploited if your application encryption key (`APP_KEY` environment variable) has been accessed by a malicious user.** Typically, it is not possible for users of your application to gain access to this value. However, ex-employees that had access to the encryption key may be able to use the key to attack your applications. If you have any reason to believe your encryption key is in the hands of a malicious party, you should **always** rotate the key to a new value.

**이 취약점은 악의적인 사용자가 애플리케이션의 암호화 키 ('APP_KEY` 환경 변수)에 엑세스 한 경우에만 악용 될 수 있습니다. ** 일반적으로 애플리케이션 사용자가 이 값에 접근 할 수는 없습니다. 그러나 암호화 키에 접근 할 수 있는 (전) 직원이 키를 사용하여 애플리케이션을 공격 할 수 있습니다. 여러분의 암호화 키가 악의적 인 공격자에게 알려졌다고 생각된다면, **항상** 새로운 값으로 키를 변경해야합니다.

### Cookie Serialization
### 쿠키 시리얼라이제이션

Laravel 5.6.30 disables all serialization / unserialization of cookie values. Since all Laravel cookies are encrypted and signed, cookie values are typically considered safe from client tampering. **However, if your application's encryption key is in the hands of a malicious party, that party could craft cookie values using the encryption key and exploit vulnerabilities inherent to PHP object serialization / unserialization, such as calling arbitrary class methods within your application.**

라라벨 5.6.30 버전은 모든 쿠키값에 대한 시리얼라이제이션 / 언시리얼라이제이션을 비활성화 시킵니다. 모든 라라벨 쿠키는 암호화되어 서명되기 때문에 일반적으로 쿠키 값은 클라이언트의 변조로 부터 안전하다고 생각됩니다. **그러나, 애플리케이션의 암호화키가 악의적인 사용자에게 넘어갔다면 해당 사용자가 암호화 키를 사용하여 쿠키값을 생성하고 취약적을 이용하여 PHP 객체의 시리얼라이제이션 / 언시리얼라이제이션을 통해서 객체를 상속받아 애플리케이션에 임의의 클래스 메소드를 호출할 수도 있습니다.**

Disabling serialization on all cookie values will invalidate all of your application's sessions and users will need to log into the application again (unless they have a `remember_token` set, in which case the user will be logged into a new session automatically). In addition, any other encrypted cookies your application is setting will have invalid values. For this reason, you may wish to add additional logic to your application to validate that your custom cookie values match an expected list of values; otherwise, you should discard them.

모든 쿠키 값에 대한 시리얼라이제이션을 비활성화하면 애플리케이션의 모든 세션이 유효하지 않게 되므로 사용자는 (`remember_token` 을 설정하지 않은 경우라면) 애플리케이션에 다시 로그인해야합니다. (이 경우에는 사용자는 자동으로 새로운 세션으로 로그인합니다.) 또한 애플리케이션에서 설정한 다른 암호화 된 쿠키가 유효하지 않다고 판단됩니다. 이 때문에, 애플리케이션에 로직을 추가하여 사용자가 정의한 쿠키 값이 예상한 값의 리스트와 일치하는지 확인하도록 하십시오. 그렇지 않다면 이 값들은 무효처리 해야합니다.

#### Configuring Cookie Serialization
#### 쿠키 시리얼라이제이션 설정하기

Since this vulnerability is not able to be exploited without access to your application's encryption key, we have chosen to provide a way to re-enable encrypted cookie serialization while you make your application compatible with these changes. To enable / disable cookie serialization, you may change the static `serialize` property of the `App\Http\Middleware\EncryptCookies` [middleware](https://github.com/laravel/laravel/blob/master/app/Http/Middleware/EncryptCookies.php):

이 취약점은 애플리케이션의 암호화 키에 대한 접근 없이는 악용될 수 없으므로, 애플리케이션이 이러한 변경사항과 호환되도록 암호화된 쿠키의 시리얼라이제이션을 다시 활성화 하는 방법이 있습니다. 쿠키의 시리얼라이제이션 사용을 활성화 / 비활성화 하려면 `App\Http\Middleware\EncryptCookies` [미들웨어](https://github.com/laravel/laravel/blob/master/app/Http/Middleware/EncryptCookies.php)의 `serialize` 속성을 static 으로 변경하면 됩니다:

    /**
     * Indicates if cookies should be serialized.
     *
     * @var bool
     */
    protected static $serialize = true;

> **Note:** When encrypted cookie serialization is enabled, your application will be vulnerable to attack if its encryption key is accessed by a malicious party. If you believe your key may be in the hands of a malicious party, you should rotate the key to a new value before enabling encrypted cookie serialization.

> **Note:** 암호화된 쿠키의 시리얼라이제이션을 활성화 했을 때, 악의적인 사용자가 암호화 키에 접근할 수 있게 되면 애플리케이션은 공격 받기 쉽습니다. 키가 악의적인 사용자에게 노출되어 있다고 생각되면 암호화된 쿠키 시리얼라이제이션 기능을 사용하기 전에 키를 새로운 값으로 변경하십시오.

### Dusk 4.0.0
### Dusk 4.0.0

Dusk 4.0.0 has been released and does not serialize cookies. If you choose to enable cookie serialization, you should continue to use Dusk 3.0.0. Otherwise, you should upgrade to Dusk 4.0.0.

쿠키를 시리얼라즈 하지 않는 Dusk 4.0.0 가 릴리즈되었습니다. 쿠키 시리얼라이제이션을 활성화 하고자 한다면, Dusk 3.0.0 을 계속 사용하십시오. 그렇지 않다면 Dusk 4.0.0 으로 업그레이드 하십시오.

### Passport 6.0.7
### Passport 6.0.7

Passport 6.0.7 has been released with a new `Laravel\Passport\Passport::withoutCookieSerialization()` method. Once you have disabled cookie serialization, you should call this method within your application's `AppServiceProvider`.

새로운 `Laravel\Passport\Passport::withoutCookieSerialization()` 메소드를 제공하는 Passport 6.0.7 이 릴리즈 되었습니다. 이제 쿠키 시리얼라이제이션을 비활성화 한 뒤에, 이 메소드를 애플리케이션의 `AppServiceProvider` 안에서 호출해야 합니다.

<a name="upgrade-5.6.0"></a>
## Upgrading To 5.6.0 From 5.5
## 5.5에서 5.6.0 으로 업그레이드 하기

#### Estimated Upgrade Time: 10 - 30 Minutes
#### 업그레이드 예상 시간 : 약 10-30분

> {note} We attempt to document every possible breaking change. Since some of these breaking changes are in obscure parts of the framework only a portion of these changes may actually affect your application.

> {note} 가능한 모든 변경 내용을 기록하려고 했습니만, 변경 사항 중 일부는 프레임워크의 명확하지 않은 부분에서 이루어 지기 때문에 이중 일부가 실제 애플리케이션에 영향을 끼칠 수도 있습니다.

### PHP
### PHP

Laravel 5.6 requires PHP 7.1.3 or higher.

라라벨 5.6은 PHP 7.1.3 이상을 필요로합니다.

### Updating Dependencies
### 의존성 업데이트

Update your `laravel/framework` dependency to `5.6.*` and your `fideloper/proxy` dependency to `^4.0` in your `composer.json` file.

`composer.json` 파일에 있는 `laravel/framework` 의존성을 `5.6.*`으로 변경하고, `fideloper/proxy` 의존성을 `^4.0` 으로 업데이트 합니다.

In addition, if you are using the following first-party Laravel packages, you should upgrade them to their latest release:

또한, 다양한 라라벨 추가 패키지들을 사용하고 있다면, 최신버전으로 업그레이드 해야 합니다:

- Dusk (Upgrade To `^3.0`)
- Passport (Upgrade To `^6.0`)
- Scout (Upgrade To `^4.0`)

- Dusk (`^3.0`으로 업그레이드)
- Passport (`^6.0` 으로 업그레이드)
- Scout (`^4.0` 으로 업그레이드)


Of course, don't forget to examine any 3rd party packages consumed by your application and verify you are using the proper version for Laravel 5.6 support.

또한, 애플리케이션에서 사용하는 써드파티 패키지를 확인하고 라라벨 5.6를 지원하는 적절한 버전을 사용하고 있는지 확인하십시오.

#### Symfony 4
#### Symfony 4

All of the underlying Symfony components used by Laravel have been upgraded to the Symfony `^4.0` release series. If you are directly interacting with Symfony components within your application, you should review the [Symfony change log](https://github.com/symfony/symfony/blob/master/UPGRADE-4.0.md).

라라벨에서 사용되는 모든 Symfony 컴포넌트가 `^4.0` 리리즈 시리즈로 업그레이드되었습니다. 애플리케이션에서 Symfony 컴포넌트를 직접 다루는 경우 [Symfony 변경 사항](https://github.com/symfony/symfony/blob/master/UPGRADE-4.0.md)을 확인해야 합니다.

#### PHPUnit
#### PHPUnit

You should update the `phpunit/phpunit` dependency of your application to `^7.0`.

`phpunit/phpunit`의존성을 `^7.0` 으로 업데이트 해야 합니다.

### Arrays
### 배열

#### The `Arr::wrap` Method
#### `Arr::wrap` 메소드

Passing `null` to the `Arr::wrap` method will now return an empty array.

`null`을 반환하던 `Arr::wrap` 메소드는 이제 빈 배열을 반환합니다.

### Artisan
### Artisan 명령어

#### The `optimize` Command
#### `optimize` 명령어

The previously deprecated `optimize` Artisan command has been removed. With recent improvements to PHP itself including the OPcache, the `optimize` command no longer provides any relevant performance benefit. Therefore, you may remove `php artisan optimize` from the `scripts` within your `composer.json` file.

이전 버전에서 deprecated 되었던 `optimize` 아티즌 명령어가 이제 제거되었습니다. OPCache를 포함한 PHP 자체의 최근의 개선으로 인해서 `optimize` 명령어는 더 이상 관련 성능상의 이점을 제공하지 않습니다. 따라서 따라서 `composer.json` 파일에서 `scripts` 옵션에서 `php artisan optimize` 를 제거 할 수 있습니다.

### Blade
### Blade

#### HTML Entity Encoding
#### HTML Entity 인코딩

In previous versions of Laravel, Blade (and the `e` helper) would not double encode HTML entities. This was not the default behavior of the underlying `htmlspecialchars` function and could lead to unexpected behavior when rendering content or passing in-line JSON content to JavaScript frameworks.

이전 버전의 라라벨에서는, 블레이드 템플릿(그리고 `e` 헬퍼 메소드에서는) HTML 엔티티를 이중으로 인코딩 하지 않았습니다. 이는 `htmlspecialchars` 함수의 기본 동작이 아니었으며, 콘텐츠를 렌더링 할 때나, 자바스크립트 프레임워크에 JSON 데이터를 전달할 때 예기치 않은 동작을 일으킬 수 있습니다.

In Laravel 5.6, Blade and the `e` helper will double encode special characters by default. This brings these features into alignment with the default behavior of the underlying `htmlspecialchars` PHP function. If you would like to maintain the previous behavior of preventing double encoding, you may use the `Blade::withoutDoubleEncoding` method:

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

### Cache
### 캐시

#### The Rate Limiter `tooManyAttempts` Method
#### `tooManyAttempts` 메소드를 사용한 속도 제한

The unused `$decayMinutes` parameter was removed from this method's signature. If you were overriding this method with your own implementation, you should also remove the argument from your method's signature.

사용되지 않는 `$decayMinutes` 파라미터가 메소드에서 제거되었습니다. 캐시를 직접 구현한 경우, 이 메소드의 오버라이딩 하였다면, 메소드 선언 부분에서 파라미터 선언부분을 제거해야합니다.

### Database
### 데이터베이스

#### Index Order Of Morph Columns
#### Index Order Of Morph Columns

The indexing of the columns built by the `morphs` migration method has been reversed for better performance. If you are using the `morphs` method in one of your migrations, you may receive an error when attempting to run the migration's `down` method. If the application is still in development, you may use the `migrate:fresh` command to rebuild the database from scratch. If the application is in production, you should pass an explicit index name to the `morphs` method.

성능향상을 위해서 `morphs` 마이그레이션 메소드를 통해 생성되는 인덱스 내 컬럼 순서가 변경 되었습니다. 데이터베이스 마이그레이션 도중에 `morphs` 메소드를 사용하는 경우, 마이그레이션의 `down` 메소드를 실행하도록 시도하면 에러가 확인됩니다. 애플리케이션이 아직 개발중이라면, `migrate:fresh` 명령어를 사용하여 데이터베이스의 스키마를 새롭게 구성할 수 있습니다. 애플리케이션이 프로덕션 환경에 있다면, `morphs` 메소드에 명시적으로 인덱스 이름을 전달해야 합니다.

#### `MigrationRepositoryInterface` Method Addition
#### `MigrationRepositoryInterface` 메소드 추가

A new `getMigrationsBatches` method has been added to the `MigrationRepositoryInterface`. In the very unlikely event that you were defining your own implementation of this class, you should add this method to your implementation. You may view the default implementation in the framework as an example.

`MigrationRepositoryInterface` 에 `getMigrationsBatches` 메소드가 추가되었습니다. 인터페이스를 직접 구현한 경우라면, 이 메소드를 추가해야 합니다. 프레임워크에 구현된 기본사항을 참고하십시오.

### Eloquent
### Eloquent

#### The `getDateFormat` Method
#### `getDateFormat` 메소드

This `getDateFormat` method is now `public` instead of `protected`.

`getDateFormat` 메소드는 이제 `protected`에서 `public`으로 변경되었습니다.

### Hashing
### 해싱

#### New Configuration File
#### 새로운 설정 파일

All hashing configuration is now housed in its own `config/hashing.php` configuration file. You should place a copy of the [default configuration file](https://github.com/laravel/laravel/blob/master/config/hashing.php) in your own application. Most likely, you should maintain the `bcrypt` driver as your default driver. However, `argon` is also supported.

모든 해싱 설정은 이제 `config/hashing.php` 설정 파일에 지정됩니다. 애플리케이션에 [기본 설정 파일](https://github.com/laravel/laravel/blob/master/config/hashing.php)을 복사해서 넣으십시오. Most likely, you should maintain the `bcrypt` driver as your default driver. However, `argon` is also supported.

### Helpers
### 헬퍼

#### The `e` Helper
#### `e` 헬퍼 함수

In previous versions of Laravel, Blade (and the `e` helper) would not double encode HTML entities. This was not the default behavior of the underlying `htmlspecialchars` function and could lead to unexpected behavior when rendering content or passing in-line JSON content to JavaScript frameworks.

이전 버전의 라라벨에서는, 블레이드 템플릿(그리고 `e` 헬퍼 메소드에서는) HTML 엔티티를 이중으로 인코딩 하지 않았습니다. 이는 `htmlspecialchars` 함수의 기본 동작이 아니었으며, 콘텐츠를 렌더링 할 때나, 자바스크립트 프레임워크에 JSON 데이터를 전달할 때 예기치 않은 동작을 일으킬 수 있습니다.

In Laravel 5.6, Blade and the `e` helper will double encode special characters by default. This brings these features into alignment with the default behavior of the underlying `htmlspecialchars` PHP function. If you would like to maintain the previous behavior of preventing double encoding, you may pass `false` as the second argument to the `e` helper:

라라벨 5.6에서는 블레이드 템플릿과 `e` 헬퍼는 기본적으로 특수 문자를 이중으로 인코딩 합니다. 이로 인해서 이 기능의 기본 동작이 `htmlspecialchars` 함수 동작과 달라집니다. 이중 인코딩을 원하지 않고 이전의 동작을 유지하려면, `e` 헬퍼 함수의 두번째 인자에 `false`를 전달하면 됩니다:

    <?php echo e($string, false); ?>

### Logging
### 로깅

#### New Configuration File
#### 새로운 설정 파일

All logging configuration is now housed in its own `config/logging.php` configuration file. You should place a copy of the [default configuration file](https://github.com/laravel/laravel/blob/master/config/logging.php) in your own application and tweak the settings based on your application's needs.

로깅과 관련된 모든 설정은 이제 `config/logging.php` 설정 파일에 지정됩니다. 애플리케이션에 [기본 설정 파일](https://github.com/laravel/laravel/blob/master/config/logging.php)을 복사해서 넣고, 필요한 설정을 구성하십시오.

The `log` and `log_level` configuration options may be removed from the `config/app.php` configuration file.

`config/app.php` 설정 파일의 `log` 와 `log_level` 설정 옵션은 제거되었습니다.

#### The `configureMonologUsing` Method
#### `configureMonologUsing` 메소드

If you were using the `configureMonologUsing` method to customize the Monolog instance for your application, you should now create a `custom` Log channel. For more information on how to create custom channels, check out the [full logging documentation](/docs/5.6/logging#creating-custom-channels).

애플리케이션에서 Monolog 인스턴스를 커스터마이징 하기 위해서 `configureMonologUsing` 메소드를 사용했다면, `custom` 로그 채널을 생성하십시오. 커스텀 채널을 생성하기 위한 보다 자세한 내용은 [로깅 문서](/docs/5.6/logging#creating-custom-channels)를 참고하십시오.

#### The Log `Writer` Class
#### Log `Writer` 클래스

The `Illuminate\Log\Writer` class has been renamed to `Illuminate\Log\Logger`. If you were explicitly type-hinting this class as a dependency of one of your application's classes, you should update the class reference to the new name. Or, alternatively, you should strongly consider type-hinting the standardized `Psr\Log\LoggerInterface` interface instead.

`Illuminate\Log\Writer` 클래스는 `Illuminate\Log\Logger` 으로 이름이 변경되었습니다. 애플리케이션의 클래스중 하나에서 이 클래스를 의존하고 있었다면, 새로운 이름으로 변경해야 합니다. 또는, 대신에, `Psr\Log\LoggerInterface` 인터페이스를 사용하는 것을 고려해보십시오.

#### The `Illuminate\Contracts\Logging\Log` Interface
#### `Illuminate\Contracts\Logging\Log` 인터페이스

This interface has been removed since this interface was a total duplication of the `Psr\Log\LoggerInterface` interface. You should type-hint the `Psr\Log\LoggerInterface` interface instead.

이 인터페이스는 `Psr\Log\LoggerInterface` 인터페이스의 복제본과 같으므로, 제거되었습니다. 대신에 `Psr\Log\LoggerInterface` 인터페이스를 사용하십시오.

### Mail
### 메일

#### `withSwiftMessage` Callbacks
#### `withSwiftMessage` 콜백

In previous releases of Laravel, Swift Messages customization callbacks registered using `withSwiftMessage` were called _after_ the content was already encoded and added to the message. These callbacks are now called _before_ the content is added, which allows you to customize the encoding or other message options as needed.

이전 버전의 라라벨에서는 `withSwiftMessage` 메소드를 호출하여 등록된 Swift 메세지를 커스터마이징 하는 콜백은 내용이 이미 인코딩되어 메세지에 추가된 _다음에_ 호출되었습니다. 이 콜백은 이제 콘텐츠가 추가되기 _전에_ 호출되므로, 필요한 경우 인코딩이나, 기타 메세지 옵션을 커스터마이징 할 수 있습니다.

### Pagination
### 페이지네이션

#### Bootstrap 4
#### Bootstrap 4

The pagination links generated by the paginator now default to Bootstrap 4. To instruct the paginator to generate Bootstrap 3 links, call the `Paginator::useBootstrapThree` method from the `boot` method of your `AppServiceProvider`:

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
### Resources

#### The `original` Property
#### `original` 속성

The `original` property of [resource responses](/docs/5.6/eloquent-resources) is now set to the original model instead of a JSON string / array. This allows for easier inspection of the response's model during testing.

[resource responses-응답](/docs/5.6/eloquent-resources)의 `original` 속성은 이제 JSON 문자열 / 배열 대신에 원래의 모델로 설정됩니다. 이를 통해서 테스팅 과정에서 response-응답의 모델을 확인하기가 수월해집니다.

### Routing
### 라우팅

#### Returning Newly Created Models
#### 새롭게 생성된 모델 반환하기

When returning a newly created Eloquent model directly from a route, the response status will now automatically be set to `201` instead of `200`. If any of your application's tests were explicitly expecting a `200` response, those tests should be updated to expect `201`.

새롭게 생성 된 Eloquent 모델을 라우트에서 직접 반환하게 되면 response-응답의 상태코드가 자동으로 `200` 대신 `201` 로 설정됩니다. 애플리케이션의 테스트에서 명시적으로 `200` response-응답을 기대하는 경우에 해당 테스트가 `201`을 예상하도록 수정해야합니다.

### Trusted Proxies
### 신뢰할 수 있는 프록시 설정

Due to underlying changes in the trusted proxy functionality of Symfony HttpFoundation, slight changes must be made to your application's `App\Http\Middleware\TrustProxies` middleware.

Symfony HttpFoundation의 신뢰할 수 있는 프록시 기능이 변경되었기 때문에, 애플리케이션의 `App\Http\Middleware\TrustProxies` 미들웨어를 조금 수정해야합니다.

The `$headers` property, which was previously an array, is now a bit property that accepts several different values. For example, to trust all forwarded headers, you may update your `$headers` property to the following value:

이전버전에는 배열이었던 `$headers` 속성은 이제, 몇가지의 서로 다는 유형의 값을 지정가능합니다. 예를 들어 모든 헤더를 신뢰하려면 `$headers` 속성을 다음처럼 수정하면 됩니다:

    use Illuminate\Http\Request;

    /**
     * The headers that should be used to detect proxies.
     *
     * @var int
     */
    protected $headers = Request::HEADER_X_FORWARDED_ALL;

For more information on the available `$headers` values, check out the full documentation on [trusting proxies](/docs/5.6/requests#configuring-trusted-proxies).

사용가능한 `$headers` 값에 대한 보다 다세한 사항은, [신뢰가능한 프록시](/docs/5.6/requests#configuring-trusted-proxies) 문서를 참고하십시오.

### Validation
### Validation-유효성 검사

#### The `ValidatesWhenResolved` Interface
#### `ValidatesWhenResolved` 인터페이스

The `validate` method of the `ValidatesWhenResolved` interface / trait has been renamed to `validateResolved` in order to avoid conflicts with the `$request->validate()` method.

`$request->validate()` 메소드와의 충돌을 피하기 위해서 `ValidatesWhenResolved` 인터페이스와 / trait-트레이트의 `validate` 메소드는 `validateResolved` 으로 이름이 변경되었습니다.

### Miscellaneous
### 기타

We also encourage you to view the changes in the `laravel/laravel` [GitHub repository](https://github.com/laravel/laravel). While many of these changes are not required, you may wish to keep these files in sync with your application. Some of these changes will be covered in this upgrade guide, but others, such as changes to configuration files or comments, will not be. You can easily view the changes with the [GitHub comparison tool](https://github.com/laravel/laravel/compare/5.5...5.6) and choose which updates are important to you.

또한 `laravel/laravel` [GitHub repository](https://github.com/laravel/laravel) GitHub 저장소에서 변경사항을 확인하는 것이 좋습니다. 이러한 변경사항이 꼭 필요하지는 않지만, 여러분의 애플리케이션을 이 변경사항들에 맞추어 항상 최신의 상태로 유지하고자 할 수도 있습니다. 변경사항 중 일부는 이 업그레이드 가이드에서 다루지만, 설정 파일이나, 설명의 변경같은 경우 일부는 문서에서 기술하지 않을 수도 있습니다. [GitHub 에서 Diff 툴](https://github.com/laravel/laravel/compare/5.5...5.6)을 사용하여 변경사항을 보다 쉽게 확인하고, 필요한 업데이트를 적용할 수도 있습니다.
