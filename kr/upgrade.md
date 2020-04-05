# Upgrade Guide
# 업그레이드 가이드

- [Upgrading To 7.0 From 6.x](#upgrade-7.0)
- [6.x에서 7.0으로 업그레이드](#upgrade-7.0)

<a name="high-impact-changes"></a>
## High Impact Changes
## 매우 중요한 변경사항

- [Authentication Scaffolding](#authentication-scaffolding)
- [인증 스캐 폴딩](#authentication-scaffolding)
- [Date Serialization](#date-serialization)
- [날짜 직렬화](#date-serialization)
- [Symfony 5 Related Upgrades](#symfony-5-related-upgrades)
- [Symfony 5 관련 업그레이드](#symfony-5-related-upgrades)

<a name="medium-impact-changes"></a>
## Medium Impact Changes
## 중요한 변경사항

- [Blade Components & "Blade X"](#blade-components-and-blade-x)
- [블레이드 컴포넌트 및 "블레이드 X"](#blade-components-and-blade-x)
- [CORS Support](#cors-support)
- [CORS 지원](#cors-support)
- [Factory Types](#factory-types)
- [Factory Types](#factory-types)
- [Markdown Mail Template Updates](#markdown-mail-template-updates)
- [마크 다운 메일 템플릿 업데이트](#markdown-mail-template-updates)
- [The `Blade::component` Method](#the-blade-component-method)
- [`Blade::component` 메소드](#the-blade-component-method)
- [The `assertSee` Assertion](#assert-see)
- [`assertSee` Assertion](#assert-see)
- [The`different` Validation Rule](#the-different-rule)
- [`different` Validation Rule](#the-different-rule)
- [Unique Route Names](#unique-route-names)
- [고유한 라우트이름](#unique-route-names)

<a name="upgrade-7.0"></a>
## Upgrading To 7.0 From 6.x
## 6.x에서 7.0으로 업그레이드

#### Estimated Upgrade Time: 15 Minutes
#### 예상 업그레이드 시간 : 15 분

> {note} We attempt to document every possible breaking change. Since some of these breaking changes are in obscure parts of the framework only a portion of these changes may actually affect your application.

> {note} 이전 버전과 호환되지 않는 모든 변경사항을 기록했습니다만 변경사항들 중 일부는 프레임워크의 모호한 부분에 있기 때문에 실제로는 여러분의 어플리케이션에 영향을 끼치지 않을 수도 있습니다.

### Symfony 5 Required
### Symfony 5 필요

**Likelihood Of Impact: High**
**영향 가능성: 높음**

Laravel 7 upgraded its underlying Symfony components to the 5.x series, which is now also the new minimum compatible version.

Laravel 7은 기본 Symfony 컴포넌트를 5.x 시리즈로 업그레이드했습니다. 이 시리즈는 이제 새로운 최소 호환 버전이기도 합니다.

### PHP 7.2.5 Required
### PHP 7.2.5 필요

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The new minimum PHP version is now 7.2.5.

이제 최소로 필요한 PHP 버전은 7.2.5입니다.

<a name="updating-dependencies"></a>
### Updating Dependencies
### 의존성 업데이트

Update your `laravel/framework` dependency to `^7.0` in your `composer.json` file. In addition, update your `nunomaduro/collision` dependency to `^4.1`, `phpunit/phpunit` dependency to `^8.5`, `laravel/tinker` dependency to `^2.0`, and `facade/ignition` to `^2.0`.

`composer.json` 파일에서 `laravel/framework` 의존성을 `^ 7.0`로 업데이트하십시오. 또한 `nunomaduro/collision` 의존성을 `^ 4.1`로, `phpunit/phpunit` 의존성을 `^ 8.5`로, `laravel/tinker` 의존성을 `^ 2.0`으로, 그리고 `facade/ignition`을 `^2.0`로 업데이트하십시오.

The following first-party packages have new major releases to support Laravel 7. If there are any, read through their individual upgrade guides before upgrading:

다음 자사 패키지에는 Laravel 7을 지원하기위한 새로운 주요 릴리스가 있습니다. 업그레이드가 있는 경우 개별 업그레이드 안내서를 읽어보십시오.

- [Browser Kit Testing v6.0](https://github.com/laravel/browser-kit-testing/blob/master/UPGRADE.md)
- [Envoy v2.0](https://github.com/laravel/envoy/blob/master/UPGRADE.md)
- [Horizon v4.0](https://github.com/laravel/horizon/blob/master/UPGRADE.md)
- [Nova v3.0](https://nova.laravel.com/releases)
- [Scout v8.0](https://github.com/laravel/scout/blob/master/UPGRADE.md)
- [Telescope v3.0](https://github.com/laravel/telescope/releases)
- UI v2.0 (No changes necessary)

Finally, examine any other third-party packages consumed by your application and verify you are using the proper version for Laravel 7 support.

마지막으로, 애플리케이션에서 사용하는 다른 타사 패키지를 검사하고 Laravel 7 지원에 적합한 버전을 사용하고 있는지 확인하십시오.

<a name="symfony-5-related-upgrades"></a>
### Symfony 5 Related Upgrades
### Symfony 5 관련 업그레이드

**Likelihood Of Impact: High**
**영향 가능성: 높음**

Laravel 7 utilizes the 5.x series of the Symfony components. Some minor changes to your application are required to accommodate this upgrade.

라라벨 7은 5.x 시리즈의 Symfony 컴포넌트를 사용합니다. 이 업그레이드를 하려면 애플리케이션을 약간 변경해야합니다.

First, the `report` and `render` methods of your application's `App\Exceptions\Handler` class should accept instances of the `Throwable` interface instead of `Exception` instances:

먼저, 애플리케이션의 `App\Exceptions\Handler` 클래스의 `report` 및 `render` 메소드는 `Exception` 인스턴스 대신 `Throwable` 인터페이스의 인스턴스를 허용해야합니다.


    use Throwable;

    public function report(Throwable $exception);
    public function render($request, Throwable $exception);

Next, please update your `session` configuration file's `secure` option to have a fallback value of `null`:

다음으로, `session` 설정 파일의 `secure` 옵션을 `null`로 폴백 값을 업데이트하십시오.

    'secure' => env('SESSION_SECURE_COOKIE', null),

### Authentication
### 인증

<a name="authentication-scaffolding"></a>
#### Scaffolding
#### 스캐폴딩

**Likelihood Of Impact: High**
**영향 가능성: 높음**

All authentication scaffolding has been moved to the `laravel/ui` repository. If you are using Laravel's authentication scaffolding, you should install the `^2.0` release of this package and the package should be installed in all environments. If you were previously including this package in the `require-dev` portion of your application's `composer.json` file, you should move it to the `require` section:

모든 인증 스캐 폴딩은 `laravel/ui` 저장소로 옮겨졌습니다. 만약 라라벨의 인증 스캐 폴딩을 사용하고 있다면 이 패키지의 `^2.0` 릴리즈를 설치해야하며 모든 환경에 패키지를 설치해야합니다. 이전에 이 패키지를 애플리케이션의 `composer.json` 파일의 `require-dev` 부분에 포함 시켰다면, `require` 섹션으로 이동해야합니다.

    composer require laravel/ui "^2.0"

#### The `TokenRepositoryInterface`
#### `TokenRepositoryInterface`

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

A `recentlyCreatedToken` method has been added to the `Illuminate\Auth\Passwords\TokenRepositoryInterface` interface. If you are writing a custom implementation of this interface, you should add this method to your implementation.

`recentlyCreatedToken` 메소드가 `Illuminate\Auth\Passwords\TokenRepositoryInterface` 인터페이스에 추가되었습니다. 이 인터페이스의 커스텀을 작성하는 경우 이 메소드를 구현에 추가해야합니다.

### Blade
### Blade

<a name="the-blade-component-method"></a>
#### The `component` Method
#### `component` 메소드

**Likelihood Of Impact: Medium**
**영향 가능성: 중간**

The `Blade::component` method has been renamed to `Blade::aliasComponent`. Please update your calls to this method accordingly.

`Blade::component` 메소드의 이름이 `Blade::aliasComponent`로 변경되었습니다. 이에 따라이 방법에 대한 호출을 업데이트하십시오.

<a name="blade-components-and-blade-x"></a>
#### Blade Components & "Blade X"
#### 블레이드 컴포넌트 및 "블레이드 X"

**Likelihood Of Impact: Medium**
**영향 가능성: 중간**

Laravel 7 includes first-party support for Blade "tag components". If you wish to disable Blade's built-in tag component functionality, you may call the `withoutComponentTags` method from the `boot` method of your `AppServiceProvider`:

Laravel 7에는 블레이드 "태그 컴포넌트"에 대한 자사 지원이 포함되어 있습니다. Blade의 내장 태그 컴포넌트 기능을 비활성화하려면 `AppServiceProvider`의 `boot` 메소드에서 `withoutComponentTags` 메소드를 호출 할 수 있습니다.

    use Illuminate\Support\Facades\Blade;

    Blade::withoutComponentTags();

### Eloquent
### Eloquent

#### The `addHidden` / `addVisible` Methods
#### `addHidden` / `addVisible` 메소드

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The undocumented `addHidden` and `addVisible` methods have been removed. Instead, please use the `makeHidden` and `makeVisible` methods.

문서화되지 않은 `addHidden` 및 `addVisible` 메소드가 제거되었습니다. 대신 `makeHidden` 및 `makeVisible` 메소드를 사용하십시오.

#### The `booting` / `booted` Methods
#### `booting` / `booted` 메소드

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The `booting` and `booted` methods have been added to Eloquent to provide a place to conveniently define any logic that should execute during the model "boot" process. If you already have model methods with these names, you will need to rename your methods so they do not conflict with the newly added methods.

모델 "부팅-boot" 프로세스 중에 실행해야하는 로직을 편리하게 정의 할 수있는 장소를 제공하기 위해 `booting` 및 `booted` 메서드가 Eloquent에 추가되었습니다. 이러한 이름의 모델 메소드가 이미있는 경우 메소드를 새로 추가 한 메소드와 충돌하지 않도록 메소드 이름을 바꿔야합니다.

<a name="date-serialization"></a>
#### Date Serialization
#### 날짜 직렬화

**Likelihood Of Impact: High**
**영향 가능성: 높음**

Laravel 7 uses a new date serialization format when using the `toArray` or `toJson` method on Eloquent models. To format dates for serialization, the framework now uses Carbon's `toJSON` method, which produces an ISO-8601 compatible date including timezone information and fractional seconds. In addition, this change provides better support and integration with client-side date parsing libraries.

Laravel 7은 Eloquent 모델에서 `toArray` 또는 `toJson` 메서드를 사용할 때 새로운 날짜 직렬화 형식을 사용합니다. 직렬화 날짜 형식을 지정하기 위해 이제 프레임워크는 표준 시간대 정보와 세분화된-fractional 초-seconds를 포함하여 ISO-8601 호환 날짜를 생성하는 Carbon의 `toJSON` 메서드를 사용합니다. 또한 이 변경은 클라이언트 측 날짜 구문 분석 라이브러리와의 지원 및 통합을 향상시킵니다.

Previously, dates would be serialized to a format like the following: `2019-12-02 20:01:00`. Dates serialized using the new format will appear like: `2019-12-02T20:01:00.283041Z`. Please note that ISO-8601 dates are always expressed in UTC.

이전에는 날짜가 `2019-12-02 20:01:00`와 같은 형식으로 직렬화되었습니다. 새로운 형식을 사용하여 직렬화 된 날짜는 `2019-12-02T20:01:00.283041Z`와 같이 나타납니다. ISO-8601 날짜는 항상 UTC로 표시됩니다.

If you would like to keep using the previous behavior you can override the `serializeDate` method on your model:

이전 동작을 계속 사용하려면 모델에서 `serializeDate` 메소드를 대체 할 수 있습니다.

    /**
     * Prepare a date for array / JSON serialization.
     *
     * @param  \DateTimeInterface  $date
     * @return string
     */
    protected function serializeDate(DateTimeInterface $date)
    {
        return $date->format('Y-m-d H:i:s');
    }

> {tip} This change only affects serialization of models and model collections to arrays and JSON. This change has no effect on how dates are stored in your database.

> {tip} 이 변경 사항은 모델 및 모델 컬렉션을 배열 및 JSON으로의 직렬화에만 영향을 줍니다. 이 변경 사항은 데이터베이스에 날짜가 저장되는 방식에 영향을 미치지 않습니다.

<a name="factory-types"></a>
#### Factory Types
#### Factory Types

**Likelihood Of Impact: Medium**
**영향 가능성: 중간**

Laravel 7 removes the "factory types" feature. This feature has been undocumented since October 2016. If you are still using this feature, you should upgrade to [factory states](/docs/{{version}}/database-testing#factory-states), which provide more flexibility.

Laravel 7은 "factory types"기능을 제거합니다. 이 기능은 2016 년 10 월 이후로 문서화되지 않았습니다. 이 기능을 계속 사용하는 경우 [factory states](/docs/{{version}}/database-testing#factory-states)로 업그레이드해야합니다.

#### The `getOriginal` Method
#### `getOriginal` 메서드

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The `$model->getOriginal()` method will now respect any casts defined on the model. Previously, this method returned the uncast, raw attributes. If you would like to continue retrieving the raw, uncast values, you may use the `getRawOriginal` method instead.

`$model->getOriginal()` 메소드는 이제 모델에 정의 된 모든 캐스트를 존중합니다. 이전에는 이 ​​메소드가 캐스트되지 않은 원시 속성을 리턴했습니다. 캐스팅되지 않은 원시 값을 계속 검색하려면 `getRawOriginal` 메소드를 대신 사용할 수 있습니다.

#### Route Binding
#### 라우트 바인딩

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The `resolveRouteBinding` method of the `Illuminate\Contracts\Routing\UrlRoutable` interface now accepts a `$field` argument. If you were implementing this interface by hand, you should update your implementation.

`Illuminate\Contracts\Routing\UrlRoutable` 인터페이스의 `resolveRouteBinding` 메소드는 이제 `$field` 인수를 입력받습합니다. 이 인터페이스를 직접 구현 한 경우 구현을 업데이트해야합니다.

In addition, the `resolveRouteBinding` method of the `Illuminate\Database\Eloquent\Model` class also now accepts a `$field` parameter. If you were overriding this method, you should update your method to accept this argument.

또한 `Illuminate\Database\Eloquent\Model`클래스의 `resolveRouteBinding` 메소드도 이제 `$field` 파라메터를 입력받습합니다. 이 메소드를 대체하는 경우이 인수를 입력받도록 메소드를 업데이트해야합니다.

Finally, the `resolveRouteBinding` method of the `Illuminate\Http\Resources\DelegatesToResources` trait also now accepts a `$field` parameter. If you were overriding this method, you should update your method to accept this argument.

마지막으로 `Illuminate\Http\Resources\DelegatesToResources` trait의 `resolveRouteBinding` 메소드도 이제 `$field` 파라메터를 입력받습합니다. 이 메소드를 대체하는 경우이 인수를 입력받도록 메소드를 업데이트해야합니다.

### HTTP
### HTTP

#### PSR-7 Compatibility
#### PSR-7 호환성

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The Zend Diactoros library for generating PSR-7 responses has been deprecated. If you are using this package for PSR-7 compatibility, please install the `nyholm/psr7` Composer package instead. In addition, please install the `^2.0` release of the `symfony/psr-http-message-bridge` Composer package.

PSR-7 응답을 생성하기위한 Zend Diactoros 라이브러리는 더 이상 사용되지 않습니다. PSR-7 호환성을 위해이 패키지를 사용하는 경우, 대신 `nyholm/psr7` Composer 패키지를 설치하십시오. 또한 `symfony/psr-http-message-bridge` Composer 패키지의 `^2.0` 릴리스를 설치하십시오.

### Mail
### Mail

#### Configuration File Changes
#### 설정 파일 변경

**Likelihood Of Impact: Optional**
**영향 가능성: 선택적**

In order to support multiple mailers, the default `mail` configuration file has changed in Laravel 7.x to include an array of `mailers`. However, in order to preserve backwards compatibility, the Laravel 6.x format of this configuration file is still supported. So, no changes are **required** when upgrading to Laravel 7.x; however, you may wish to [examine the new `mail` configuration file](https://github.com/laravel/laravel/blob/develop/config/mail.php) structure and update your file to reflect the changes.

여러 메일러를 지원하기 위해 Laravel 7.x에서 기본 `mail` 설정 파일이 메일러 배열을 포함하도록 변경되었습니다. 그러나 이전 버전과의 호환성을 유지하기 위해이 설정 파일의 Laravel 6.x 형식을 계속 지원합니다. 따라서 Laravel 7.x로 업그레이드 할 때 변경이 필요하지 않습니다. 그러나, 당신은 [새로운 `mail` 설정 파일을 검토](https://github.com/laravel/laravel/blob/develop/config/mail.php) 하여 구조를 변경하고 파일을 업데이트하여 변경 사항을 반영 할 수 있습니다.

<a name="markdown-mail-template-updates"></a>
#### Markdown Mail Template Updates
#### 마크 다운 메일 템플릿 업데이트

**Likelihood Of Impact: Medium**
**영향 가능성: 중간**

The default Markdown mail templates have been refreshed with a more professional and appealing design. In addition, the undocumented `promotion` Markdown mail component has been removed.

보다 전문적이고 매력적인 디자인으로 기본 마크 다운 메일 템플릿이 새로 고쳐졌습니다. 또한 문서화되지 않은 `promotion` 마크 다운 메일 컴포넌트가 제거되었습니다.

Because indentitation has special meaning within Markdown, Markdown mail templates expect unindented HTML. If you've previously published Laravel's default mail templates, you'll need to re-publish your mail templates or manually unindent them:

들여 쓰기는 Markdown 내에서 특별한 의미를 갖기 때문에 Markdown 메일 템플릿에는 들여 쓰기되지 않은 HTML이 필요합니다. 이전에 라라벨의 기본 메일 템플릿을 게시-published 한 경우 메일 템플릿을 다시 게시하거나 수동으로 들여 쓰기를 해제해야합니다.

    php artisan vendor:publish --tag=laravel-mail --force

### Queue
### Queue

#### Deprecated `--daemon` Flag Removed
#### 더 이상 사용되지 않는 `-daemon` 플래그 제거

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The deprecated `--daemon` flag on the `queue:work` command has been removed. This flag is no longer necessary as the worker runs as a daemon by default.

`queue:work` 명령에서 사용되지 않는 `--daemon` 플래그가 제거되었습니다. 워커가 기본적으로 데몬으로 실행되므로이 ​​플래그는 더 이상 필요하지 않습니다.

### Resources
### Resources

#### The `Illuminate\Http\Resources\Json\Resource` Class
#### `Illuminate\Http\Resources\Json\Resource` Class

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The deprecated `Illuminate\Http\Resources\Json\Resource` class has been removed. Your resources should extend the `Illuminate\Http\Resources\Json\JsonResource` class instead.

더 이상 사용되지 않는 `Illuminate\Http\Resources\Json\Resource` 클래스가 제거되었습니다. 리소스는 대신`Illuminate\Http\Resources\Json\JsonResource` 클래스를 확장해야합니다.

### Routing
### Routing

#### The Router `getRoutes` Method
#### 라우터 `getRoutes` 메소드

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The router's `getRoutes` method now returns an instance of `Illuminate\Routing\RouteCollectionInterface` instead of `Illuminate\Routing\RouteCollection`.

라우터의 `getRoutes` 메소드는 이제 `Illuminate\Routing\RouteCollection` 대신 `Illuminate\Routing\RouteCollectionInterface`의 인스턴스를 반환합니다.

<a name="unique-route-names"></a>
#### Unique Route Names
#### 고유한 라우트 이름

**Likelihood Of Impact: Medium**
**영향 가능성: 중간**

Even though never officially documented, previous Laravel releases allow you to define two different routes with the same name. In Laravel 7 this is no longer possible and you should always provide unique names for your routes. Routes with duplicate names can cause unexpected behavior in multiple areas of the framework.

공식적으로 문서화 된 적이 없지만 이전 Laravel 릴리스에서는 동일한 이름으로 두 개의 다른 경로를 정의 할 수 있습니다. Laravel 7에서는 더 이상 가능하지 않으므로 항상 라우트에 고유한 이름을 제공해야합니다. 이름이 중복 된 라우트는 프레임 워크의 여러 영역에서 예기치 않은 동작을 일으킬 수 있습니다.

<a name="cors-support"></a>
#### CORS Support
#### CORS 지원

**Likelihood Of Impact: Medium**
**영향 가능성: 중간**

Cross-Origin Resource Sharing (CORS) support is now integrated by default. If you are using any third-party CORS libraries you are now advised to use the [new `cors` configuration file](https://github.com/laravel/laravel/blob/develop/config/cors.php).

Cross-Origin Resource Sharing (CORS) 지원이 기본적으로 통합되었습니다. 타사 CORS 라이브러리를 사용하는 경우 이제 [새로운 `cors` 설정 파일](https://github.com/laravel/laravel/blob/develop/config/cors.php) 을 사용하는 것이 좋습니다.

Next, install the underlying CORS library as a dependency of your application:

다음으로 기본 CORS 라이브러리를 애플리케이션의 의존성으로 설치하십시오.

    composer require fruitcake/laravel-cors

Finally, add the `\Fruitcake\Cors\HandleCors::class` middleware to your `App\Http\Kernel` global middleware list.

마지막으로 `\Fruitcake\Cors\HandleCors::class` 미들웨어를 `App\Http\Kernel` 글로벌 미들웨어 목록에 추가하십시오.

### Session
### Session

#### The `array` Session Driver
#### `array` Session Driver

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The `array` session driver data is now persistent for the current request. Previously, data stored in the `array` session could not be retrieved even during the current request.

`array`세션 드라이버 데이터는 이제 현재 request에 대해 영속적입니다. 이전에는 현재 request 도중에도 `array`세션에 저장된 데이터를 검색 할 수 없었습니다.

### Testing
### Testing

<a name="assert-see"></a>
#### The `assertSee` Assertion
#### `assertSee` Assertion

**Likelihood Of Impact: Medium**
**영향 가능성: 중간**

The `assertSee` and `assertDontSee` assertions on the `TestResponse` class will now automatically escape values. If you are manually escaping any values passed to these assertions you should no longer do so. If you need to assert unescaped values, you may pass `false` as the second argument to the method.

`TestResponse` 클래스의 `assertSee` 및 `assertDontSee` 검증은 이제 자동으로 값을 이스케이프합니다. 이러한 검증에 전달 된 값을 수동으로 이스케이프 처리하면 더 이상 그렇게하지 않아야합니다. 이스케이프 처리되지 않은 값을 지정해야하는 경우 메소드의 두 번째 인수로 `false`를 전달할 수 있습니다.

<a name="test-response"></a>
#### The `TestResponse` Class
#### `TestResponse` Class

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The `Illuminate\Foundation\Testing\TestResponse` class has been renamed to `Illuminate\Testing\TestResponse`. If you're extending this class, make sure to update the namespace.

`Illuminate\Foundation\Testing\TestResponse` 클래스의 이름이 `Illuminate\Testing\TestResponse`로 변경되었습니다. 이 클래스를 확장하는 경우 네임스페이스를 업데이트하십시오.

<a name="assert-class"></a>
#### The `Assert` Class
#### `Assert` Class

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The `Illuminate\Foundation\Testing\Assert` class has been renamed to `Illuminate\Testing\Assert`. If you're using this class, make sure to update the namespace.

`Illuminate\Foundation\Testing\Assert` 클래스의 이름이 `Illuminate\Testing\Assert`로 변경되었습니다. 이 클래스를 사용하는 경우 네임스페이스를 업데이트하십시오.

### Validation
### 검증

<a name="the-different-rule"></a>
#### The `different` Rule
#### `different` Rule

**Likelihood Of Impact: Medium**
**영향 가능성: 중간**

The `different` rule will now fail if one of the specified parameters is missing from the request.

지정된 파라메터 중 하나가 요청에서 누락되면 `different`규칙이 실패합니다.

<a name="miscellaneous"></a>
### Miscellaneous
### 기타

We also encourage you to view the changes in the `laravel/laravel` [GitHub repository](https://github.com/laravel/laravel). While many of these changes are not required, you may wish to keep these files in sync with your application. Some of these changes will be covered in this upgrade guide, but others, such as changes to configuration files or comments, will not be. You can easily view the changes with the [GitHub comparison tool](https://github.com/laravel/laravel/compare/6.x...master) and choose which updates are important to you.

`laravel/laravel` [GitHub 저장소](https://github.com/laravel/laravel)에서 변경사항들을 확인해보시길 권합니다. 변경사항들 중 많은 부분들은 필수는 아니지만, 업데이트 된 파일들을 여러분의 어플리케이션에 동기화 해두는게 좋습니다. 변경사항들 중 일부는 이 업그레이드 가이드에서 설명되었지만 설정 파일이나 코멘트같은 다른 변경사항들은 그렇지 않았습니다. [GitHub comparison tool](https://github.com/laravel/laravel/compare/6.x...master)을 이용해 변경사항들을 쉽게 살펴보고 어떤 변경사항이 여러분에게 중요한지 살펴보세요.
