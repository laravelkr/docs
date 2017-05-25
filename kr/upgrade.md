# Upgrade Guide
# 업그레이드 가이드

- [Upgrading To 5.1.11](#upgrade-5.1.11)
- [Upgrading To 5.1.0](#upgrade-5.1.0)
- [Upgrading To 5.0.16](#upgrade-5.0.16)
- [Upgrading To 5.0 From 4.2](#upgrade-5.0)
- [Upgrading To 4.2 From 4.1](#upgrade-4.2)
- [Upgrading To 4.1.29 From <= 4.1.x](#upgrade-4.1.29)
- [Upgrading To 4.1.26 From <= 4.1.25](#upgrade-4.1.26)
- [Upgrading To 4.1 From 4.0](#upgrade-4.1)

<a name="upgrade-5.1.11"></a>
## Upgrading To 5.1.11
## 5.1.11 로 업그레이드 하기 

Laravel 5.1.11 includes support for [authorization](/docs/{{version}}/authorization) and [policies](/docs/{{version}}/authorization#policies). Incorporating these new features into your existing Laravel 5.1 applications is simple.

라라벨 5.1.11 에서는 [authorization-승인](/docs/{{version}}/authorization) 과 [policies](/docs/{{version}}/authorization#policies)를 지원합니다. 새로운 기능들과 기존의 라라벨 5.1 어플리케이션은 손쉽게 통합됩니다. 

> **Note:** These upgrades are **optional**, and ignoring them will not affect your application.

> **참고:** 이러한 업그레이드는 **선택 사항**이기 때문에, 이 기능을 사용하지 않아도, 어플리케이션의 실행에는 영향을 주지 않습니다.

#### Create The Policies Directory
#### Policy-정책 디렉토리 생성하기

First, create an empty `app/Policies` directory within your application.

먼저 어플리케이션 안에 `app/Policies` 라는 빈 디렉토리를 생성합니다. 

#### Create / Register The AuthServiceProvider & Gate Facade
#### AuthServiceProvider 와 Gate 파사드를 생성하고 등록하기

Create a `AuthServiceProvider` within your `app/Providers` directory. You may copy the contents of the default provider [from GitHub](https://raw.githubusercontent.com/laravel/laravel/5.1/app/Providers/AuthServiceProvider.php). Remember to change the provider's namespace if your application is using a custom namespace. After creating the provider, be sure to register it in your `app.php` configuration file's `providers` array.

`app/Providers` 디렉토리 안에 `AuthServiceProvider` 파일을 생성합니다. [GitHub 에서](https://raw.githubusercontent.com/laravel/laravel/5.1/app/Providers/AuthServiceProvider.php) 파일을 복사할 수도 있습니다. 유의할 것은 프로바이더의 네임스페이스를 여러분의 어플리케이션에서 사용하는 네임스페이스로 변경해야 한다는 것입니다. 프로바이더를 생성한 뒤에, `app.php` 설정 파일의 `providers` 배열에 등록해야 합니다. 

Also, you should register the `Gate` facade in your `app.php` configuration file's `aliases` array:

또한 `Gate` 파사드를 `app.php` 설정 파일의 `aliases` 배열에 등록해야합니다. 

    'Gate' => Illuminate\Support\Facades\Gate::class,

#### Update The User Model
#### User 모델 변경하기 

Secondly, use the `Illuminate\Foundation\Auth\Access\Authorizable` trait and `Illuminate\Contracts\Auth\Access\Authorizable` contract on your `App\User` model:

다음으로, `App\User` 모델에서 `Illuminate\Foundation\Auth\Access\Authorizable` 트레이트와, `Illuminate\Contracts\Auth\Access\Authorizable` contract 를 사용하도록 합니다. 

    <?php

    namespace App;

    use Illuminate\Auth\Authenticatable;
    use Illuminate\Database\Eloquent\Model;
    use Illuminate\Auth\Passwords\CanResetPassword;
    use Illuminate\Foundation\Auth\Access\Authorizable;
    use Illuminate\Contracts\Auth\Authenticatable as AuthenticatableContract;
    use Illuminate\Contracts\Auth\Access\Authorizable as AuthorizableContract;
    use Illuminate\Contracts\Auth\CanResetPassword as CanResetPasswordContract;

    class User extends Model implements AuthenticatableContract,
                                        AuthorizableContract,
                                        CanResetPasswordContract
    {
        use Authenticatable, Authorizable, CanResetPassword;
    }

#### Update The Base Controller
#### Base Controller 변경하기 

Next, update your base `App\Http\Controllers\Controller` controller to use the `Illuminate\Foundation\Auth\Access\AuthorizesRequests` trait:
다음으로  Base Controller `App\Http\Controllers\Controller` 에서 `Illuminate\Foundation\Auth\Access\AuthorizesRequests` 트레이트를 사용하도록 변경합니다:

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Foundation\Bus\DispatchesJobs;
    use Illuminate\Routing\Controller as BaseController;
    use Illuminate\Foundation\Validation\ValidatesRequests;
    use Illuminate\Foundation\Auth\Access\AuthorizesRequests;

    abstract class Controller extends BaseController
    {
        use AuthorizesRequests, DispatchesJobs, ValidatesRequests;
    }

<a name="upgrade-5.1.0"></a>
## Upgrading To 5.1.0
## 5.1.0으로 업그레이드 하기 

#### Estimated Upgrade Time: Less Than 1 Hour
#### 업그레이드 예상 시간 : 1시간 이하 

### Update `bootstrap/autoload.php`
### `bootstrap/autoload.php` 업데이트 하기 

Update the `$compiledPath` variable in `bootstrap/autoload.php` to the following:

`bootstrap/autoload.php`파일의 `$compiledPath` 변수를 다음처럼 변경합니다. 

    $compiledPath = __DIR__.'/cache/compiled.php';

### Create `bootstrap/cache` Directory
### `bootstrap/cache` 디렉토리 생성하기

Within your `bootstrap` directory, create a `cache` directory (`bootstrap/cache`). Place a `.gitignore` file in this directory with the following contents:

`bootstrap` 디렉토리 안에 `cache` 디렉토리를 만들고, (`bootstrap/cache`) 다음 내용을 포함하는 `.gitignore` 파일을 생성합니다. 

    *
    !.gitignore

This directory should be writable, and will be used by the framework to store temporary optimization files like `compiled.php`, `routes.php`, `config.php`, and `services.json`.

이 디렉토리는 프레임워크에서 `compiled.php`, `routes.php`, `config.php` 그리고 `services.json` 파일과 같은 임시 최적화 파일들을 저장할 수 있도록 쓰기가 가능하도록 설정되어야 합니다. 

### Add `BroadcastServiceProvider` Provider
### `BroadcastServiceProvider` 프로바이더 추가하기

Within your `config/app.php` configuration file, add `Illuminate\Broadcasting\BroadcastServiceProvider` to the `providers` array.

`config/app.php` 설정 파일의 `providers` 배열에 `Illuminate\Broadcasting\BroadcastServiceProvider`를 추가합니다. 

### Authentication
### Authentication-인증

If you are using the provided `AuthController` which uses the `AuthenticatesAndRegistersUsers` trait, you will need to make a few changes to how new users are validated and created.

`AuthenticatesAndRegistersUsers` 트레이트-trait 를 사용하고 있는 `AuthController` 를 이용하고 있다면, 새로운 사용자에 대한 검증과 생성 방법에 몇가지 변경사항이 필요합니다.  

First, you no longer need to pass the `Guard` and `Registrar` instances to the base constructor. You can remove these dependencies entirely from your controller's constructor.

먼저 기본 생성자에 더이상 `Guard` 와 `Registrar` 인스턴스를 전달할 필요가 없습니다. 컨트롤러의 생성자 안에서 이러한 의존성을 제거하십시오. 

Secondly, the `App\Services\Registrar` class used in Laravel 5.0 is no longer needed. You can simply copy and paste your `validator` and `create` method from this class directly into your `AuthController`. No other changes should need to be made to these methods; however, you should be sure to import the `Validator` facade and your `User` model at the top of your `AuthController`.

다음으로, 라라벨 5.0의 `App\Services\Registrar`가 더이상 필요하지 않습니다. 이 클래스의 `validator` 와 `create` 메소드를 `AuthController` 로 복사하십시오. 이 메소드 이외의 다른 변경사항은 없지만, `AuthController` 최상단에 `Validator` 파사드와 `User` 모델에 대한 use 선언이 필요할것입니다.

#### Password Controller
#### 패스워드 컨트롤러

The included `PasswordController` no longer requires any dependencies in its constructor. You may remove both of the dependencies that were required under 5.0.

`PasswordController`는 생성자에서 더이상 어떠한 의존성도 필요하지 않습니다. 5.0 이하에서 필요했던 의존성들을 제거할 수 있습니다. 

### Validation
### Validation-검증

If you are overriding the `formatValidationErrors` method on your base controller class, you should now type-hint the `Illuminate\Contracts\Validation\Validator` contract instead of the concrete `Illuminate\Validation\Validator` instance.

base 컨트롤러 클래스에서 `formatValidationErrors` 메소드를 오버라이드 했었다면, `Illuminate\Validation\Validator` 인스턴스 대신에 `Illuminate\Contracts\Validation\Validator` contract 인스턴스를 타입힌트 해야 합니다. 

Likewise, if you are overriding the `formatErrors` method on the base form request class, you should now type-hint `Illuminate\Contracts\Validation\Validator` contract instead of the concrete `Illuminate\Validation\Validator` instance.

마참가지로 base form request 클래스에서 `formatErrors` 메소드를 오버라이드 했었다면, `Illuminate\Validation\Validator` 인스턴스 대신에 `Illuminate\Contracts\Validation\Validator` contract 를 타입힌트 해야 합니다. 

### Eloquent
### Eloquent 

#### The `create` Method
#### `create` 메소드 

Eloquent's `create` method can now be called without any parameters. If you are overriding the `create` method in your own models, set the default value of the `$attributes` parameter to an array:

Eloquent의 `create` 메소드는 인자없이 호출될 수 있게 되었습니다. 만약 고유한 모델의 `create` 메소드를 오버라이드 하는 경우에는, `$attributes` 인자의 기본값으로 배열을 지정하십시오. 

    public static function create(array $attributes = [])
    {
        // Your custom implementation
    }

#### The `find` Method
#### `find` 메소드

If you are overriding the `find` method in your own models and calling `parent::find()` within your custom method, you should now change it to call the `find` method on the Eloquent query builder:

여러분의 고유한 모델에서 `find` 메소드를 오버라이딩 하고, `parent::find()` 메소드를 호출하는 경우, 이를 Eloquent 쿼리 빌더에서 `find` 메소드를 호출하도록 변경해야 합니다.

    public static function find($id, $columns = ['*'])
    {
        $model = static::query()->find($id, $columns);

        // ...

        return $model;
    }

#### The `lists` Method

The `lists` method now returns a `Collection` instance instead of a plain array for Eloquent queries. If you would like to convert the `Collection` into a plain array, use the `all` method:

이제, Eloquent 쿼리의 `lists` 메소드는 배열을 반환하는 대신에 `Collection` 인스턴스를 반환합니다. `Collection`을 배열으로 변환하려면, `all` 메소드를 사용하십시오:

    User::lists('id')->all();

Be aware that the Query Builder `lists` method still returns an array.

쿼리 빌더의 `lists` 메소드는 여전히 배열을 돌려주는 것에 주의하십시오.

#### Date Formatting
#### 날짜 포맷형식

Previously, the storage format for Eloquent date fields could be modified by overriding the `getDateFormat` method on your model. This is still possible; however, for convenience you may simply specify a `$dateFormat` property on the model instead of overriding the method.

지금까지, Eloquent 날짜 필드를 저장하는 포맷은 모델의 `getDateFormat` 메소드를 재정의하여 변경되었습니다. 이 방법은 아직 유효하지만, 메소드를 오버라이딩 하는 대신에, 간단하게 `$dateFormat` 속성을 지정하는 것이 더 편리합니다. 

The date format is also now applied when serializing a model to an `array` or JSON. This may change the format of your JSON serialized date fields when migrating from Laravel 5.0 to 5.1. To set a specific date format for serialized models, you may override the `serializeDate(DateTime $date)` method on your model. This method allows you to have granular control over the formatting of serialized Eloquent date fields without changing their storage format.

또한, 날짜 포맷은 모델을 `배열` 이나 JSON으로 serialize 할때도 적용됩니다. 라라벨 5.0 에서 5.1로 업그레이드 하면 날짜 필드의 JSON serialize 포맷이 바뀌기 때문에, 모델의 `serializeDate(DateTime $date)` 메소드를 오버라이드 할 수 있습니다. 이 메소드는 Eloquent 에서 저장되는 포맷을 변경하지 않고, serialize 되는 포맷을 조정할 수 있습니다. 

### The Collection Class
### Colleciton 클래스 

#### The `sort` Method
#### `sort` 메소드 

The `sort` method now returns a fresh collection instance instead of modifying the existing collection:

`sort` 메소드는 기존의 컬렉션을 수정하는 대신 새로운 컬렉션 인스턴스를 반환합니다. 

    $collection = $collection->sort($callback);

#### The `sortBy` Method
#### `sortBy` 메소드

The `sortBy` method now returns a fresh collection instance instead of modifying the existing collection:

`sortBy` 메소드는 기존의 컬렉션을 수정하는 대신 새로운 컬렉션 인스턴스를 반환합니다. 

    $collection = $collection->sortBy('name');

#### The `groupBy` Method
#### `groupBy` 메소드 

The `groupBy` method now returns `Collection` instances for each item in the parent `Collection`. If you would like to convert all of the items back to plain arrays, you may `map` over them:

`groupBy` 메소드는 부모 `Collection` 의 각각의 아이템마다 `Collection` 인스턴스를 반환합니다. 만약 아이템을 배열로 변환하고자 한다면, `map` 을 다음처럼 사용하면 됩니다. 

    $collection->groupBy('type')->map(function($item)
    {
        return $item->all();
    });

#### The `lists` Method
#### `lists` 메소드

The `lists` method now returns a `Collection` instance instead of a plain array. If you would like to convert the `Collection` into a plain array, use the `all` method:

이제 `lists` 메소드는 배열 대신 `Collection` 인스턴스를 반환합니다. `Collection`을 배열로 변환하고자 한다면, `all` 메소드를 사용하면 됩니다. 

    $collection->lists('id')->all();

### Commands & Handlers
### 명령어 & 핸들러

The `app/Commands` directory has been renamed to `app/Jobs`. However, you are not required to move all of your commands to the new location, and you may continue using the `make:command` and `handler:command` Artisan commands to generate your classes.

`app/Commands` 디렉토리는 `app/Jobs` 로 이름이 변경되었습니다. 모든 명령어들을 새로운 위치로 이동시킬 필요는 없습니다. 새로운 명령어 클래스를 생성하기 위한 `make:command` 와 `handler:command` 아티즌 명령어들은 그대로 계속해서 사용할 수 있습니다. 

Likewise, the `app/Handlers` directory has been renamed to `app/Listeners` and now only contains event listeners. However, you are not required to move or rename your existing command and event handlers, and you may continue to use the `handler:event` command to generate event handlers.

마찬가지로, `app/Handlers` 디렉토리도 `app/Listeners` 로 이름이 변경되었으며, 이벤트 리스너들을 포함하고 있습니다. 이미 존재하는 명령어와 이벤트 핸들러들을 옮기거나, 이름을 변경할 필요는 없으며, 이벤트 핸들러를 생성하기 위한 `handler:event` 명령어도 계속 사용할 수 있습니다. 

By providing backwards compatibility for the Laravel 5.0 folder structure, you may upgrade your applications to Laravel 5.1 and slowly upgrade your events and commands to their new locations when it is convenient for you or your team.

라라벨 5.0의 폴더 구조와의 호환성을 제공하기 때문에, 편리한 시간에 이벤트들과 명령어들을 새로운 위치로 구성하고 어플리케이션을 5.1로 천천히 업그레이드 할 수 있습니다. 

### Blade
### 블레이드

The `createMatcher`, `createOpenMatcher`, and `createPlainMatcher` methods have been removed from the Blade compiler. Use the new `directive` method to create custom directives for Blade in Laravel 5.1. Consult the [extending blade](/docs/{{version}}/blade#extending-blade) documentation for more information.

블레이드 컴파일러에서 `createMatcher`, `createOpenMatcher`, 그리고 `createPlainMatcher` 메소드는 제거되었습니다. 라라벨 5.1의 블레이드에서 새로운 지시어를 생성하기 위해서는 `directive` 메소드를 사용하십시오. 보다 자세한 사항은 [블레이드 확장](/docs/{{version}}/blade#extending-blade) 문서를 참고하십시오. 

### Tests
### 테스트

Add the protected `$baseUrl` property to the `tests/TestCase.php` file:

`tests/TestCase.php` 파일의 protected `$baseUrl` 속성을 추가하십시오: 

    protected $baseUrl = 'http://localhost';

### Translation Files
### 언어 파일

The default directory for published language files for vendor packages has been moved. Move any vendor package language files from `resources/lang/packages/{locale}/{namespace}` to `resources/lang/vendor/{namespace}/{locale}` directory. For example, `Acme/Anvil` package's `acme/anvil::foo` namespaced English language file would be moved from `resources/lang/packages/en/acme/anvil/foo.php` to `resources/lang/vendor/acme/anvil/en/foo.php`. 

벤더 패키지의 언어 파일을 `resources/lang/packages/{locale}/{namespace}` 에서 `resources/lang/vendor/{namespace}/{locale}` 디렉토리로 변경하십시오. 예를 들어 `Acme/Anvil` 패키지의 `acme/anvil::foo` 네임스페이스를 따르는 영어 언어 파일은 `resources/lang/packages/en/acme/anvil/foo.php` 에서 `resources/lang/vendor/acme/anvil/en/foo.php`으로 변경되었습니다. 

벤더 패키지의 언어 파일에 대한 기본 디렉토리가 변경되었습니다. 

### Amazon Web Services SDK
### Amazon Web Services (AWS) SDK

If you are using the AWS SQS queue driver or the AWS SES e-mail driver, you should update your installed AWS PHP SDK to version 3.0.

AWS SQS 큐 드라이버나 AWS SES 이메일 드라이버를 사용중이라면, AWS PHP SDK 를 3.0 으로 업데이트 해야 합니다. 

If you are using the Amazon S3 filesystem driver, you will need to update the corresponding Flysystem package via Composer:

아마존 S3 파일 시스템 드라이버를 사용중이라면, 컴포저를 통해서 파일시스템에 해당하는 다음 패키지를 업데이트 해야 합니다. 

- Amazon S3: `league/flysystem-aws-s3-v3 ~1.0`

### Deprecations
### 비추천 사항들

The following Laravel features have been deprecated and will be removed entirely with the release of Laravel 5.2 in December 2015:

다음의 기능들은 사용을 권장하지 않는 기능들이 되었으며, 2015년 12월에 릴리즈 되는 5.2 에서는 완전히 제거될것입니다. 

- Route filters have been deprecated in preference of [middleware](/docs/{{version}}/middleware).
- The `Illuminate\Contracts\Routing\Middleware` contract has been deprecated. No contract is required on your middleware. In addition, the `TerminableMiddleware` contract has also been deprecated. Instead of implementing the interface, simply define a `terminate` method on your middleware.
- The `Illuminate\Contracts\Queue\ShouldBeQueued` contract has been deprecated in favor of `Illuminate\Contracts\Queue\ShouldQueue`.
- Iron.io "push queues" have been deprecated in favor of typical Iron.io queues and [queue listeners](/docs/{{version}}/queues#running-the-queue-listener).
- The `Illuminate\Foundation\Bus\DispatchesCommands` trait has been deprecated and renamed to `Illuminate\Foundation\Bus\DispatchesJobs`.
- `Illuminate\Container\BindingResolutionException` has been moved to `Illuminate\Contracts\Container\BindingResolutionException`.
- The service container's `bindShared` method has been deprecated in favor of the `singleton` method.
- The Eloquent and query builder `pluck` method has been deprecated and renamed to `value`.
- The collection `fetch` method has been deprecated in favor of the `pluck` method.
- The `array_fetch` helper has been deprecated in favor of the `array_pluck` method.

- 라우트 필터는 더이상 추천하지 않으며 [미들웨어](/docs/{{version}}/middleware)를 사용하십시오. 
- `Illuminate\Contracts\Routing\Middleware` contract 는 더이상 사용하지 말기를 권장합니다. 미들웨어는 어떠한 필요사항도 존재하지 않습니다. 추가적으로 `TerminableMiddleware` contract 또한 권장하지 않습니다. 인터페이스를 구현하는 대신에, 미들웨어에 `terminate` 메소드를 정의하기만 하면 됩니다. 
- The `Illuminate\Contracts\Queue\ShouldBeQueued` contract has been deprecated in favor of `Illuminate\Contracts\Queue\ShouldQueue`.
- `Illuminate\Contracts\Queue\ShouldQueue`를 권장하며, `Illuminate\Contracts\Queue\ShouldBeQueued` contract 는 권장하지 않습니다. 
- Iron.io "push queues" have been deprecated in favor of typical Iron.io queues and [queue listeners](/docs/{{version}}/queues#running-the-queue-listener).
- Iron.io 의 "push queues"는 더이상 권장하지 않고, 일반적인 Iron.io 큐 그리고 [queue listeners](/docs/{{version}}/queues#running-the-queue-listener)를 사용하십시오. 
- The `Illuminate\Foundation\Bus\DispatchesCommands` trait has been deprecated and renamed to `Illuminate\Foundation\Bus\DispatchesJobs`.
- `Illuminate\Foundation\Bus\DispatchesCommands` 트레이트-trait는 권장되지 않고, `Illuminate\Foundation\Bus\DispatchesJobs`으로 이름이 변경되었습니다. 
- `Illuminate\Container\BindingResolutionException` has been moved to `Illuminate\Contracts\Container\BindingResolutionException`.
- `Illuminate\Container\BindingResolutionException`이 `Illuminate\Contracts\Container\BindingResolutionException`으로 변경되었습니다. 
- 서비스 컨테이너의 `bindShared` 메소드는 `singleton` 메소드로 대신합니다.
- Eloquent 와 쿼리 빌더의 `pluck` 메소드는 권장되지 않고, `value` 메소드로 이름이 변경되었습니다.
- collection 의 `fetch` 메소드는 권장되지 않고, `pluck` 메소드를 사용하십시오. .
- The `array_fetch` helper has been deprecated in favor of the `array_pluck` method.
- `array_fetch` 헬퍼 메소드는 권장되지 않습니다. `array_pluck` 메소드를 사용하십시오. 

<a name="upgrade-5.0.16"></a>
## Upgrading To 5.0.16
## 5.0.16으로 업그레이드

In your `bootstrap/autoload.php` file, update the `$compiledPath` variable to:

`bootstrap/autoload` 파일에서 `$compiledPath` 변수를 다음처럼 변경합니다. 

    $compiledPath = __DIR__.'/../vendor/compiled.php';

<a name="upgrade-5.0"></a>
## Upgrading To 5.0 From 4.2
## 4.2 에서 5.0으로 업그레이드

### Fresh Install, Then Migrate
### 새로 설치하고, 마이그레이션하기

The recommended method of upgrading is to create a new Laravel `5.0` install and then to copy your `4.2` site's unique application files into the new application. This would include controllers, routes, Eloquent models, Artisan commands, assets, and other code specific to your application.

업그레이드에 대한 권장사항은 새롭게 라라벨 `5.0` 버전을 인스톨하고 `4.2` 버전의 사이트의 고유한 어플리케이션 파일들을 새로운 어플리케이션에 복사하라는 것입니다. 여기에는 컨트롤러, 라우트, 엘로퀀트 모델들, 아티즌 명령어들, asset 파일들 그리고 어플리케이션에 지정된 기타 코드들을 포함합니다.

To start, [install a new Laravel 5.0 application](/docs/5.0/installation) into a fresh directory in your local environment.  Do not install any versions newer than 5.0 yet, since we need to complete the migration steps for 5.0 first.                       We'll discuss each piece of the migration process in further detail below.

업그레이드를 시작하기 위해서 로컬 환경의 새로운 디렉토리에 [라라벨 5.0 어플리케이션을 설치](/docs/5.0/installation)합니다. 아직 5.0 이후 버전은 설치하지 마십시오. 먼저 5.0을 완전히 마이그레이션 해야합니다. 업그레이드를 위한 각 단계의 진행에 대해서 차근차근 알아보겠습니다. 

### Composer Dependencies & Packages
### 컴포저 의존성 & 패키지

Don't forget to copy any additional Composer dependencies into your 5.0 application. This includes third-party code such as SDKs.

설치된 5.0 버전의 어플리케이션에 추가적인 컴포저 의존 패키지들을 복사해 넣는 것을 잊지 마십시오. 여기에는 SDK와 같은 서드 파티 코드도 포함됩니다.

Some Laravel-specific packages may not be compatible with Laravel 5 on initial release. Check with your package's maintainer to determine the proper version of the package for Laravel 5. Once you have added any additional Composer dependencies your application needs, run `composer update`.

라라벨5가 릴리즈 된 이후 바로는 라라벨과 연동되는 일부 패키지는 라라벨5와 호환이 되지 않는수도 있습니다. 패키지의 제작자에게 라라벨5 버전에 대한 대응계획을 확인합니다. 컴포저에 어플리케이션을 위한 추가적인 의존 패키지를 추가하였다면 `composer updata` 를 실행 해야 합니다. 

### Namespacing
### 네임스페이스

By default, Laravel 4 applications did not utilize namespacing within your application code. So, for example, all Eloquent models and controllers simply lived in the "global" namespace. For a quicker migration, you can simply leave these classes in the global namespace in Laravel 5 as well.

라라벨 4에서는 기본적으로 어플리케이션의 코드에 네임스페이스가 구성되어 있지 않았습니다. 그래서 예를 들면 모든 Eloquent 모델들과 컨트롤러는 "글로벌" 네임스페이스 영역에 해당되었습니다. 빠른 전환을 위해서는 라라벨5에서도 마찬가지로 글로벌 네임스페이스 안에 이 클래스들을 지정되도록 합니다. 

### Configuration
### 설정

#### Migrating Environment Variables
#### 마이그레이션 환경 변수

Copy the new `.env.example` file to `.env`, which is the `5.0` equivalent of the old `.env.php` file. Set any appropriate values there, like your `APP_ENV` and `APP_KEY` (your encryption key), your database credentials, and your cache and session drivers.

`.env.example` 파일을 복사하여 이전 버전에서 `.env.php` 파일과 동일한 역활을 하는 `5.0의 `.env` 파일을 생성합니다. `APP_ENV` 와 `APP_KEY` (암호화 키), 데이터 베이스 연결 정보, 캐시 그리고 세션 드라이버와 같은 어플리케이션에서 사용가능항 설정 값들을 지정할 수 있습니다. 

Additionally, copy any custom values you had in your old `.env.php` file and place them in both `.env` (the real value for your local environment) and `.env.example` (a sample instructional value for other team members).

추가적으로 이전버전의 `.env.php` 파일에 들어 있던 사용자가 지정한 값들을 `.env` 파일(실제 로컬 환경을 위한 값 설정) 과 `.env.example` 파일 (다른 팀 구성원들이 참고할 샘플 값)에 복사합니다.)에 복사해 넣습니다. 

For more information on environment configuration, view the [full documentation](/docs/{{version}}/configuration#environment-configuration).

환경 설정과 관련된 보다 자세한 내용은 [관련 문서](/docs/{{version}}/configuration#environment-configuration)를 확인하십시오. 

> **Note:** You will need to place the appropriate `.env` file and values on your production server before deploying your Laravel 5 application.

> **주의** 라라벨5 어플리케이션을 실제 제품 서버에 배포하기 전에 적절한 값을 지정한 `.env` 파일을 준비해 둘 필요가 있습니다.

#### Configuration Files
#### 설정 파일들

Laravel 5.0 no longer uses `app/config/{environmentName}/` directories to provide specific configuration files for a given environment. Instead, move any configuration values that vary by environment into `.env`, and then access them in your configuration files using `env('key', 'default value')`. You will see examples of this in the `config/database.php` configuration file.

라라벨 5.0은 더이상 `app/config/{구동환경의이름}/` 디렉토리를 주어진 구동 환경에 대한 설정파일로 사용하지 않습니다. 대신에  구동 환경에 의해 달라지는 설정 값들을 `.env` 파일로 이동 시켰습니다. 그리고 나서 설정 파일 안에서 `env(‘키’, ‘기본값’)` 의 형태로 해당 값들에 엑세스 합니다. `config/database.php` 설정 파일에서 이러한 사용예를 확인할 수 있습니다. 

Set the config files in the `config/` directory to represent either the values that are consistent across all of your environments, or set them to use `env()` to load values that vary by environment.

구동환경과 관계 없이 변하지 않는 값들과 또는 `env()` 를 통해서 여러분의 로컬 환경에 의해서 변화하는 값 두가지들에 대한 설정 값들을 `config/` 디렉토리안에 들어 있는 설정 파일들에 지정합니다. 

Remember, if you add more keys to `.env` file, add sample values to the `.env.example` file as well. This will help your other team members create their own `.env` files.

기억하십시오 만약 여러분이 `.env` 파일에 추가적인 키들을 추가하였다면, 마찬가지로 `.env.exampl` 파일에도 추가합니다. 여러분의 팀 동료들이 이를 참고하여 손쉽게 자신의 `.env` 파일을 생성하는데 도움이 될 것입니다. 

### Routes
### 라우트

Copy and paste your old `routes.php` file into your new `app/Http/routes.php`.

이전 버전의 `routes.php` 파일의 내용을 새로운 `app/Http/routes.php` 파일에 복사해 넣습니다. 

### Controllers
### 컨트롤러

Next, move all of your controllers into the `app/Http/Controllers` directory. Since we are not going to migrate to full namespacing in this guide, add the `app/Http/Controllers` directory to the `classmap` directive of your `composer.json` file. Next, you can remove the namespace from the abstract `app/Http/Controllers/Controller.php` base class. Verify that your migrated controllers are extending this base class.

다음으로 모든 컨트롤러들을 `app/Http/Controllers` 디렉토리로 옮깁니다. 이 가이드에서는 네임스페이스를 사용하도록 변경하지 않기 때문에, `app/Http/Controllers` 디렉토리를 `composer.json` 파일의 `classmap` 여역에 추가합니다. 그런 다음 `app/Http/Controllers/Controller.php` 추상  클래스에서 네임스페이스를 제거합니다. 마이그레이션된 컨트롤러가 기본 클래스를 상속 받고 있는지 확인합니다. 

In your `app/Providers/RouteServiceProvider.php` file, set the `namespace` property to `null`.

`app/Providers/RouteServiceProvider.php` 파일 내부에서 `namespace` 속성을 `null` 로 설정합니다.

### Route Filters
### 라우트 필터

Copy your filter bindings from `app/filters.php` and place them into the `boot()` method of `app/Providers/RouteServiceProvider.php`. Add `use Illuminate\Support\Facades\Route;` in the `app/Providers/RouteServiceProvider.php` in order to continue using the `Route` Facade.

`app/filters.php` 파일에 있던 필터 바인딩들을 `app/Providers/RouteServiceProvider.php`파일의 `boot()` 메소드에 복사합니다. `app/Providers/RouteServiceProvider.php` 파일에 `use Illuminate\Support\Facades\Route;`를 추가하여 `Route` 파사드를 사용할 수 있도록 합니다. 

You do not need to move over any of the default Laravel 4.0 filters such as `auth` and `csrf`; they're all here, but as middleware. Edit any routes or controllers that reference the old default filters (e.g. `['before' => 'auth']`) and change them to reference the new middleware (e.g. `['middleware' => 'auth'].`)

`auth` 와 `csrf` 와 같은 기본적인 라라벨 4.0의 필터는 미들웨어로 변경되었기 때문에 따로 옮길 필요가 없습니다. 이전의 기본 필터들(예를 들어 `['before' => 'auth']`) 을 참조하는 라우트나 컨트롤러들을 수정하여 새로운 미들웨어(예를 들어 `['middleware' => 'auth’].`)를 참조 하도록 변경합니다. 

Filters are not removed in Laravel 5. You can still bind and use your own custom filters using `before` and `after`.

필터는 라라벨5에서 삭제되지 않았습니다. 여러분은 여전히 `before` 와 `after` 를 사용하여 사용자 정의 필터를 지정하고 사용할 수 있습니다. 

### Global CSRF
### 전역 CSRF

By default, [CSRF protection](/docs/{{version}}/routing#csrf-protection) is enabled on all routes. If you'd like to disable this, or only manually enable it on certain routes, remove this line from `App\Http\Kernel`'s `middleware` array:

기본적으로 [CSRF 방지](/docs/{{version}}/routing#csrf-protection) 가 전체 라우트에서 활성화되어 있습니다. 이를 비활성화 시키거나 특정 라우트에서만 수동으로 활성화하고자한다면 `App\Http\Kernel`파일의  `middleware` 배열에서 다음 미들웨어를 삭제합니다. 

    'App\Http\Middleware\VerifyCsrfToken',

If you want to use it elsewhere, add this line to `$routeMiddleware`:

다른곳에서 사용하기 위해서 삭제한 라인을 `$routeMiddleware` 에 추가합니다. 

    'csrf' => 'App\Http\Middleware\VerifyCsrfToken',

Now you can add the middleware to individual routes / controllers using `['middleware' => 'csrf']` on the route. For more information on middleware, consult the [full documentation](/docs/{{version}}/middleware).

이제 개별 라우트 / 컨트롤러에 대해서 `['middleware' => 'csrf']` 와 같이 미들웨어를 지정할 수 있습니다. 미들웨어에 대한 보다 자세한 사항은 [미들웨어 문서](/docs/{{version}}/middleware)를 참고하십시오. 

### Eloquent Models
### Eloquent 모델

Feel free to create a new `app/Models` directory to house your Eloquent models. Again, add this directory to the `classmap` directive of your `composer.json` file. 

Eloquent 모델을 모아두기 위한 `app/Models` 디렉토리를 생성합니다. 그런다음에 이 디렉토리를 `composer.json` 파일의 `classmap` 에 추가합니다. 

Update any models using `SoftDeletingTrait` to use `Illuminate\Database\Eloquent\SoftDeletes`.

`SoftDeletingTrait` 을 사용하는 모델은  `Illuminate\Database\Eloquent\SoftDeletes`을 사용하도록 변경합니다. 

#### Eloquent Caching
#### Eloquent 캐싱

Eloquent no longer provides the `remember` method for caching queries. You now are responsible for caching your queries manually using the `Cache::remember` function. For more information on caching, consult the [full documentation](/docs/{{version}}/cache).

Eloquent 는 더이상 쿼리 캐시를 위한 `remember` 메소드를 제공하지 않습니다. 여러분은 이제 `Cache::remember` 함수를 사용하여 수동으로 쿼리를 캐싱해야 합니다. 캐시와 관련된 보다 자세한 정보는 [해당 문서](/docs/{{version}}/cache)를 참고하십시오. 

### User Authentication Model
### 사용자 인증 모델

To upgrade your `User` model for Laravel 5's authentication system, follow these instructions:

`User` 모델을 라라벨5의 인증 시스템 용으로 업그레이드 하려면 다음과 같이 하면 됩니다. 

**Delete the following from your `use` block:**
** 다음의 `use` 블럭을 삭제합니다 :**

```php
use Illuminate\Auth\UserInterface;
use Illuminate\Auth\Reminders\RemindableInterface;
```

**Add the following to your `use` block:**

** 다음의 `use` 블럭을 추가합니다:**

```php
use Illuminate\Auth\Authenticatable;
use Illuminate\Auth\Passwords\CanResetPassword;
use Illuminate\Contracts\Auth\Authenticatable as AuthenticatableContract;
use Illuminate\Contracts\Auth\CanResetPassword as CanResetPasswordContract;
```

**Remove the UserInterface and RemindableInterface interfaces.**

**UserInterface와 RemindableInterface 인터페이스를 제거합니다. 

**Mark the class as implementing the following interfaces:**

**클래스가 다음의 인터페이스들을 구현하도록 선언합니다:**

```php
implements AuthenticatableContract, CanResetPasswordContract
```

**Include the following traits within the class declaration:**

**클래스 선언부분 안에서 다음의 trait들을 사용하도록 합니다:**

```php
use Authenticatable, CanResetPassword;
```

**If you used them, remove `Illuminate\Auth\Reminders\RemindableTrait`  and `Illuminate\Auth\UserTrait` from your use block and your class declaration.**

**이 방법을 사용하면, 클래스의 선언부분의 use 블록에서 `Illuminate\Auth\Reminders\RemindableTrait` 와 `Illuminate\Auth\UserTrait`를 제거합니다.

### Cashier User Changes
### 캐셔 변경사항 

The name of the trait and interface used by [Laravel Cashier](/docs/{{version}}/billing) has changed. Instead of using `BillableTrait`, use the `Laravel\Cashier\Billable` trait. And, instead of `Laravel\Cashier\BillableInterface` implement the `Laravel\Cashier\Contracts\Billable` interface instead. No other method changes are required.

[라라벨 캐셔](/docs/{{version}}/billing)의 trait 과 인터페이스의 이름이 변경되었습니다.  `BillableTrait` 대신 `Laravel\Cashier\Billable` trait을 사용합니다. 그리고 `Laravel\Cashier\BillableInterface` 대신에 `Laravel\Cashier\Contracts\Billable` 인터페이스를 사용합니다. 다른 메소드는 변경되지 않았습니다. 

### Artisan Commands
### 아티즌 명령어들

Move all of your command classes from your old `app/commands` directory to the new `app/Console/Commands` directory. Next, add the `app/Console/Commands` directory to the `classmap` directive of your `composer.json` file.

`app/commands` 디렉토리의 모든 커맨드 클래스드들을 새로운 `app/Console/Commands` 디렉토리로 이옮깁니다. 그다음에 `composer.json` 파일의 `classmap` 에 `app/Console/Commands` 을 추가합니다. 

Then, copy your list of Artisan commands from `start/artisan.php` into the `command` array of the `app/Console/Kernel.php` file.

그리고 `start/artisan.php` 파일의 아티즌 명령어 리스트를 `app/Console/Kernel.php` 파일의 `commands` 배열에 복사합니다. 

### Database Migrations & Seeds
### 데이터베이스 마이그레이션 & 시딩

Delete the two migrations included with Laravel 5.0, since you should already have the users table in your database.

데이터베이스에 이미 user 테이블에 존재하기 때문에 라라벨 5.0에 포함된 두개의 마이그레이션 파일을 삭제 합니다. 

Move all of your migration classes from the old `app/database/migrations` directory to the new `database/migrations`. All of your seeds should be moved from `app/database/seeds` to `database/seeds`.

이전 `app/database/migrations` 디렉토리의 모든 마이그레이션 클래스들을 새로운 `database/migrations` 디렉토리로 옮깁니다. 시드 파일들은 `app/database/seeds` 에서 `database/seeds` 로 옮깁니다. 

### Global IoC Bindings
### 글로벌 IoC 바인딩

If you have any [service container](/docs/{{version}}/container) bindings in `start/global.php`, move them all to the `register` method of the `app/Providers/AppServiceProvider.php` file. You may need to import the `App` facade.

만약 `start/global.php` 파일에 [서비스 컨테이너](/docs/{{version}}/container) 바인딩들을 가지고 있었다면, `app/Providers/AppServiceProvider.php`의 `register` 메소드로 옮깁니다. `App` 파사드를 사용해야할 것입니다. 

Optionally, you may break these bindings up into separate service providers by category.

옵션으로, 바인딩들을 해당하는 개별 서비스 프로바이더에 나누어 옮길 수도있습니다. 

### Views
### View-뷰

Move your views from `app/views` to the new `resources/views` directory.

`app/views` 디렉토리의 뷰 파일들을 `resources/views` 디렉토리로 옮깁니다

### Blade Tag Changes
### 블레이드 태그 변경

For better security by default, Laravel 5.0 escapes all output from both the `{{ }}` and `{{{ }}}` Blade directives. A new `{!! !!}` directive has been introduced to display raw, unescaped output. The most secure option when upgrading your application is to only use the new `{!! !!}` directive when you are **certain** that it is safe to display raw output.

보안을 기본적으로 강화하기 위해서 라라벨5에서는 `{{ }}` 와 `{{{ }}}` 구문에서 모든 출력을 escape 합니다. 새로운 `{!! !!}` 구문이 escape 되지 않은 출력을 위해서 사용되어 집니다. 어플리케이션을 업데이트 할 때 가장 안전한 선택은 이전처럼 출력하기 위해서 기존 구문들을 `{!! !!}` 표기 방식으로 사용하는 것입니다. 

However, if you **must** use the old Blade syntax, add the following lines at the bottom of `AppServiceProvider@register`:

하지만 여러분이 **반드시** 이전 버전의 블레이드 문법을 사용해야 한다면 `AppServiceProvider@register` 의 마지막에 다음 라인들을 추가하면 됩니다. 

```php
\Blade::setRawTags('{{', '}}');
\Blade::setContentTags('{{{', '}}}');
\Blade::setEscapedContentTags('{{{', '}}}');
```

This should not be done lightly, and may make your application more vulnerable to XSS exploits. Also, comments with `{{--` will no longer work.

이렇게 하는 것이 편리한것만은 아니며 XSS 공격에 대해서 어플리케이션이 취약해질 수도 있습니다. 
또한 코멘트와 `{{--` 는 더이상 동작하지 않습니다. 

### Translation Files
### 다국어 파일

Move your language files from `app/lang` to the new `resources/lang` directory.

`app/lang` 디렉토리의 언어 파일들을 `resources/lang` 디렉토리로 옮깁니다

### Public Directory
### public 디렉토리

Copy your application's public assets from your `4.2` application's `public` directory to your new application's `public` directory. Be sure to keep the `5.0` version of `index.php`.

`4.2` 어플리케이션의 `public` 디렉토리에 들어 있던 asset 파일들을 새로운 어플리케이션의 `public` 디렉토리로 복사해 넣습니다. `index.php`의 `5.0` 버전을 유지해야 합니다.

### Tests
### 테스트 파일

Move your tests from `app/tests` to the new `tests` directory.

`app/tests` 폴더의 테스트 파일들을 `tests` 디렉토리로 옮깁니다. 

### Misc. Files
### 기타 파일들

Copy in any other files in your project. For example, `.scrutinizer.yml`, `bower.json` and other similar tooling configuration files.

프로젝트의 다른 파일들을 복사합니다. 예를 들어 `.scrutinizer.yml`, `bower.json` 그리고 다를 비슷한 도구 관련 설정 파일 등입니다. 

You may move your Sass, Less, or CoffeeScript to any location you wish. The `resources/assets` directory could be a good default location.

Sass 나 Less, CoffeeScript 파일들을 원하는 위치로 이동합시다. `resources/assets` 디렉토리가 기본 위치로 적당할 것입니다. 

### Form & HTML Helpers
### Form & HTML 헬퍼

If you're using Form or HTML helpers, you will see an error stating `class 'Form' not found` or `class 'Html' not found`. The Form and HTML helpers have been deprecated in Laravel 5.0; however, there are community-driven replacements such as those maintained by the [Laravel Collective](http://laravelcollective.com/docs/{{version}}/html).

만약 Form 이나 HTML 헬퍼를 사용중이었다면 `class 'Form' not found` 또는 `class 'Html' not found`와 같은 에러 메세지를 확인할 수 있을 것입니다. Form 과 HTML 헬퍼는 라라벨 5.0에서 더이상 제공하지 않습니다. 하지만 별도의 [Laravel Collective](http://laravelcollective.com/docs/{{version}}/html) 커뮤니티가 주관하는 대체 시스템이 있습니다. 

For example, you may add `"laravelcollective/html": "~5.0"` to your `composer.json` file's `require` section.

예를 들자면 `composer.json` 의 `require` 영역에 `"laravelcollective/html": "~5.0"`를 추가할 수도 있습니다 .

You'll also need to add the Form and HTML facades and service provider. Edit `config/app.php` and add this line to the 'providers' array: 

또한 Form 과 HTML 파사드와 서비스 프로바이더가 필요할 것입니다. `config/app.php` 를 수정하여 'providers' 배열에 다음 라인을 추가하십시오. 

    'Collective\Html\HtmlServiceProvider',

Next, add these lines to the 'aliases' array:

다음으로 'aliases' 배열에 다음라인들을 추가합니다. 

    'Form' => 'Collective\Html\FormFacade',
    'Html' => 'Collective\Html\HtmlFacade',

### CacheManager
### 캐시매니저
If your application code was injecting `Illuminate\Cache\CacheManager` to get a non-Facade version of Laravel's cache, inject `Illuminate\Contracts\Cache\Repository` instead.

어플리케이션에서 라라벨 캐시를 파사드가 아닌 `Illuminate\Cache\CacheManager` 형태로 주입했었다면 이제는 그 대신에 `Illuminate\Contracts\Cache\Repository`를 주입하도록 합니다. 

### Pagination
### 페이지네이션

Replace any calls to `$paginator->links()` with `$paginator->render()`.

`$paginator->links()` 로 호출되던 부분을 `$paginator->render()`으로 교체하십시오.

Replace any calls to `$paginator->getFrom()` and `$paginator->getTo()` with `$paginator->firstItem()` and `$paginator->lastItem()` respectively.

`$paginator->getFrom()` 와 `$paginator->getTo()`로 호출되던 부분을 각각 `$paginator->firstItem()` 와 `$paginator->lastItem()` 로 교체합니다. 

Remove the "get" prefix from calls to `$paginator->getPerPage()`, `$paginator->getCurrentPage()`, `$paginator->getLastPage()` and `$paginator->getTotal()` (e.g. `$paginator->perPage()`).

`$paginator->getPerPage()`, `$paginator->getCurrentPage()`, `$paginator->getLastPage()` 그리고 `$paginator->getTotal()`메소드 앞에 접두어로 붙던 “get”을 제거 하십시오(예. `$paginator->perPage()`)

### Beanstalk Queuing
### Beanstalk 큐

Laravel 5.0 now requires `"pda/pheanstalk": "~3.0"` instead of `"pda/pheanstalk": "~2.1"`.

라라벨 5.0에서는 `"pda/pheanstalk": "~2.1"` 대신에 `"pda/pheanstalk": "~3.0”`을 필요로 합니다. 

### Remote
### Remote

The Remote component has been deprecated.

Remote 컴포넌트는 더이상 제공하지 않습니다. 

### Workbench
### Workbench 

The Workbench component has been deprecated.

Workbench 컴포넌트는 더이상 제공하지 않습니다. 

<a name="upgrade-4.2"></a>
## Upgrading To 4.2 From 4.1
## 4.1에서 4.2로 업그레이드 하기

### PHP 5.4+
### PHP 5.4 이상

Laravel 4.2 requires PHP 5.4.0 or greater.

라라벨 4.2는 PHP 5.4.0 이상을 필요로 합니다. 

### Encryption Defaults
### 기본 암호화 방식

Add a new `cipher` option in your `app/config/app.php` configuration file. The value of this option should be `MCRYPT_RIJNDAEL_256`.

`app/config/app.php` 설정 파일에 새로운 `chpher` 옵션을 추가하십시오. 이 옵션값은 `MCRYPT_RIJNDAEL_256`가 되어야 합니다. 

    'cipher' => MCRYPT_RIJNDAEL_256

This setting may be used to control the default cipher used by the Laravel encryption facilities.

이 설정은 라라벨의 암호화 기능에 의해 사용되는 기본 암호화를 제어하는​​ 데 사용됩니다.

> **Note:** In Laravel 4.2, the default cipher is `MCRYPT_RIJNDAEL_128` (AES), which is considered to be the most secure cipher. Changing the cipher back to `MCRYPT_RIJNDAEL_256` is required to decrypt cookies/values that were encrypted in Laravel <= 4.1

> **참고:** 라라벨 4.2에서는 기본 암호화 옵션은 가장 안전한 암호화 방식으로 알려진 `MCRYPT_RIJNDAEL_128` (AES)입니다.  암호화 방식을 `MCRYPT_RIJNDAEL_256`으로 되돌릴 필요가 있는 경우는 라라벨 4.1 이전 버전에서 암호화 된 쿠키 / 값을 해독해야 하는 경우입니다.

### Soft Deleting Models Now Use Traits
### Soft 삭제 모델은 Traits를 사용합니다. 

If you are using soft deleting models, the `softDeletes` property has been removed. You must now use the `SoftDeletingTrait` like so:

만약 모델의 soft 삭제를 사용하였었다면 `softDeletes` 속성은 제거되었습니다. 이제 다음처럼 `SoftDeletingTrait`을 사용해야 합니다. 

    use Illuminate\Database\Eloquent\SoftDeletingTrait;

    class User extends Eloquent
    {
        use SoftDeletingTrait;
    }

You must also manually add the `deleted_at` column to your `dates` property:

또한 수동으로 `deleted_at` 컬럼을  `dates` 속성에 추가해주어야 합니다. 

    class User extends Eloquent
    {
        use SoftDeletingTrait;

        protected $dates = ['deleted_at'];
    }

The API for all soft delete operations remains the same.

모든 soft 삭제 작업을 위한 API는 동일합니다. 

> **Note:** The `SoftDeletingTrait` can not be applied on a base model. It must be used on an actual model class.

> **주의:** `SoftDeletingTrait` 는 기본 모델에 사용해서는 안됩니다. 실제 모델 클래스에서만 사용하도록 합니다.

### View / Pagination Environment Renamed
### 뷰 / 페이지네이션 Environment 이름 변경

If you are directly referencing the `Illuminate\View\Environment` class or `Illuminate\Pagination\Environment` class, update your code to reference `Illuminate\View\Factory` and `Illuminate\Pagination\Factory` instead. These two classes have been renamed to better reflect their function.

코드상에서 `Illuminate\View\Environment` 클래스 또는 `Illuminate\Pagination\Environment` 클래스를 참조하도록 되어 있었다면 그 대신에 `Illuminate\View\Factory` 와 `Illuminate\Pagination\Factory`를 참조하도록 변경합니다. 이 두개의 클래스는 기능을 보다 잘 나타내기 위해서 이름이 변경되었습니다. 

### Additional Parameter On Pagination Presenter
### 페이지네이션 프리젠터에 파라미터 추가

If you are extending the `Illuminate\Pagination\Presenter` class, the abstract method `getPageLinkWrapper` signature has changed to add the `rel` argument:

`Illuminate\Pagination\Presenter`클래스를 확장하는 경우 `getPageLinkWrapper` 추상 메소드에 `rel` 인자가 추가되도록 변경되었습니다. 

    abstract public function getPageLinkWrapper($url, $page, $rel = null);

### Iron.Io Queue Encryption
### Iron.Io 큐 암호화

If you are using the Iron.io queue driver, you will need to add a new `encrypt` option to your queue configuration file:

Iron.io 큐 드라이버를 사용하는 경우 큐 설정 파일에서 새롭게 `encrypt` 옵션을 추가합니다. 

    'encrypt' => true

<a name="upgrade-4.1.29"></a>
## Upgrading To 4.1.29 From <= 4.1.x
## 4.1.X 이하에서 4.1.29로 업그레이드 하기

Laravel 4.1.29 improves the column quoting for all database drivers. This protects your application from some mass assignment vulnerabilities when **not** using the `fillable` property on models. If you are using the `fillable` property on your models to protect against mass assignment, your application is not vulnerable. However, if you are using `guarded` and are passing a user controlled array into an "update" or "save" type function, you should upgrade to `4.1.29` immediately as your application may be at risk of mass assignment.

라라벨 4.1.29에서는 모든 데이터베이스 드라이버에 대한 컬럼 인용 부분이 향상되었습니다. 모델에서 `fillable` 속성을 사용하지 않은 경우 여러가지 컬럼의 대량 할당에 관한(msass assignment) 취약점으로 부터 어플리케이션을 보호할 수 있습니다. 모델에서 `fillable` 속성을 사용하여 데이터를 할당하는 경우 어플리케이션이 보다 안전해 집니다. 그러나 여러분이 `guarded` 속성을 사용하고 있고 사용자로 부터 전달된 배열을 “update” 나 “save” 유형의 기능에 전달하는 경우라면 어플리케이션이 mass assignment 위험에 노출되어 있으므로 즉시 4.1.29로 업그레이드 해야 합니다. 

To upgrade to Laravel 4.1.29, simply `composer update`. No breaking changes are introduced in this release. 

라라벨4.1.29로 업그레이드 하기 위해서는 `composer update` 를 실행하면 됩니다. 이 업데이트에서는 소스 수정이 필요한 사항들은 포함되어 있지 않습니다. 

<a name="upgrade-4.1.26"></a>
## Upgrading To 4.1.26 From <= 4.1.25
## 4.1.25 이하에서 4.1.26으로 업그레이드 하기 

Laravel 4.1.26 introduces security improvements for "remember me" cookies. Before this update, if a remember cookie was hijacked by another malicious user, the cookie would remain valid for a long period of time, even after the true owner of the account reset their password, logged out, etc.

라라벨 4.1.26에서는 “remember me” 쿠키와 관련된 보안이 강화되었습니다. 이 업데이트 이전에는 remember 쿠키가 악의적인 사용자에 의해서 탈취되어 사용자 계정의 암호를 재설정하거나, 로그아웃 해도 장기간 사용이 가능한 상태로 남아 있었습니다. 

This change requires the addition of a new `remember_token` column to your `users` (or equivalent) database table. After this change, a fresh token will be assigned to the user each time they login to your application. The token will also be refreshed when the user logs out of the application. The implications of this change are: if a "remember me" cookie is hijacked, simply logging out of the application will invalidate the cookie.

이번 변경사항에서는 `users` 테이블(또는 사용자 관리랄 위한 테이블)에 새로운 `remember_token` 컬럼을 추가해야 합니다. 이 변경으로 인해서 사용자가 어플리케이션에 로그인 할 때마다 새로운 토큰이 할당됩니다. 이 토큰은 사용자가 어플리케이션에서 로그아웃 할때마다 다시 생성됩니다. 이 구현은 만약 “remember me” 쿠키가 탈취 당하더라도 어플리케이션에서 로그아웃 하면 해당 쿠키는 사용할 수 없게 된다는 것을 의미합니다. 

### Upgrade Path
### 업그레이드 방법

First, add a new, nullable `remember_token` of VARCHAR(100), TEXT, or equivalent to your `users` table.

먼저 `users` 테이블에 null을 허용하는 VARCHAR(100) 또는 TEXT 형식의 `remember_token` 컬럼을 추가합니다. 

Next, if you are using the Eloquent authentication driver, update your `User` class with the following three methods:

다음으로 Eloquent 인증 드라이버를 사용하고 있는 경우에, User 클래스에 다음 세 메소드를 추가하십시오.

    public function getRememberToken()
    {
        return $this->remember_token;
    }

    public function setRememberToken($value)
    {
        $this->remember_token = $value;
    }

    public function getRememberTokenName()
    {
        return 'remember_token';
    }

> **Note:** All existing "remember me" sessions will be invalidated by this change, so all users will be forced to re-authenticate with your application.

> **주의:** 이 변경으로 인해 현재 사용중인 "Remember me" 세션이 무효화되기 때문에 모든 사용자는 어플리케이션에 접속할 때 강제로 다시 인증을 해야되게 됩니다.

### Package Maintainers
### 패키지 개발자에게

Two new methods were added to the `Illuminate\Auth\UserProviderInterface` interface. Sample implementations may be found in the default drivers:

`Illuminate\Auth\UserProviderInterface` 인터페이스에 새로운 2개의 메소드가 추가되었습니다. 기본 드라이버에 간단한 구현예제를 확인하실 수 있습니다. 

    public function retrieveByToken($identifier, $token);

    public function updateRememberToken(UserInterface $user, $token);

The `Illuminate\Auth\UserInterface` also received the three new methods described in the "Upgrade Path".

`Illuminate\Auth\UserInterface`에서도 “업데이트 방법”에서 설명하고 있는 새로운 메소드 3개가 추가되었습니다. 

<a name="upgrade-4.1"></a>
## Upgrading To 4.1 From 4.0
## 4.0에서 4.1로 업그레이드

### Upgrading Your Composer Dependency
### 컴포저 의존성 업그레이드 하기

To upgrade your application to Laravel 4.1, change your `laravel/framework` version to `4.1.*` in your `composer.json` file.

라라벨 어플리케이션을 4.1로 업그레이드 하기 위해서는 `composer.json` 파일에서 `laravel/framework`의 버전을 `4.1.*`으로 변경해야 합니다. 

### Replacing Files
### 파일 교체

Replace your `public/index.php` file with [this fresh copy from the repository](https://github.com/laravel/laravel/blob/v4.1.0/public/index.php).

`public/index.php` 파일을 [저장소의 새로운 버전](https://github.com/laravel/laravel/blob/v4.1.0/public/index.php)으로 교체 합니다.

Replace your `artisan` file with [this fresh copy from the repository](https://github.com/laravel/laravel/blob/v4.1.0/artisan).

`artisan` 파일을 [저장소의 새로운 버전](https://github.com/laravel/laravel/blob/v4.1.0/artisan)으로 교체 합니다.

### Adding Configuration Files & Options
### 설정 파일과 옵션 추가

Update your `aliases` and `providers` arrays in your `app/config/app.php` configuration file. The updated values for these arrays can be found [in this file](https://github.com/laravel/laravel/blob/v4.1.0/app/config/app.php). Be sure to add your custom and package service providers / aliases back to the arrays.

`app/config/app.php` 설정 파일에서 `aliases` 와 `providers` 배열을 업데이트 합니다. 배열에서 업데이트 해야될 값은 [이 파일](https://github.com/laravel/laravel/blob/v4.1.0/app/config/app.php)에서 확인할 수 있습니다. 사용자가 별도로 지정한 패키지 서비스 프로바이더와 별칭을 따로 추가하는 것을 잊지 마십시오. 

Add the new `app/config/remote.php` file [from the repository](https://github.com/laravel/laravel/blob/v4.1.0/app/config/remote.php).

새로운 `app/config/remote.php` 설정 파일을 [저장소](https://github.com/laravel/laravel/blob/v4.1.0/app/config/remote.php)로 부터 추가합니다. 

Add the new `expire_on_close` configuration option to your `app/config/session.php` file. The default value should be `false`.

새로운 `app/config/session.php` 파일에서 새로운 `expire_on_close` 설정 옵션을 추가합니다. 기본값은 `false` 입니다. 

Add the new `failed` configuration section to your `app/config/queue.php` file. Here are the default values for the section:

`app/config/queue.php` 파일에서 새로운 `failed` 설정 섹션을 추가합니다. 이 섹션의 기본값은 다음과 같습니다. 

    'failed' => [
        'database' => 'mysql', 'table' => 'failed_jobs',
    ],

**(Optional)** Update the `pagination` configuration option in your `app/config/view.php` file to `pagination::slider-3`.

**(선택사항)** `app/config/view.php` 파일에서 `pagination` 설정 옵션을 `pagination::slider-3` 으로 업데이트 합니다. 

### Controller Updates
### 컨트롤러 업데이트

If `app/controllers/BaseController.php` has a `use` statement at the top, change `use Illuminate\Routing\Controllers\Controller;` to `use Illuminate\Routing\Controller;`.

`app/controllers/BaseController.php` 파일의 최 상단 부분에 `use` 가 사용되었다면 `use Illuminate\Routing\Controllers\Controller;` 를 `use Illuminate\Routing\Controller;`으로 변경합니다. 

### Password Reminders Updates
### 패스워드 리마인더(알리미) 업데이트

Password reminders have been overhauled for greater flexibility. You may examine the new stub controller by running the `php artisan auth:reminders-controller` Artisan command. You may also browse the [updated documentation](/docs/4.1/security#password-reminders-and-reset) and update your application accordingly.

패스워드 리마인더는 유연성을 높이기 위해서 많은 변경이 있었습니다. `php artisan auth:reminders-controller` 아티즌 명령어를 실행하여 작성되는 새로운 컨트롤러를 살펴보십시오. 혹은 [변경 관련 문서](/docs/4.1/security#password-reminders-and-reset)를 확인하고 그에 따라 어플리케이션을 업데이트 하십시오. 

Update your `app/lang/en/reminders.php` language file to match [this updated file](https://github.com/laravel/laravel/blob/v4.1.0/app/lang/en/reminders.php).

`app/lang/en/reminders.php` 언어 파일을 [새로운 파일](https://github.com/laravel/laravel/blob/v4.1.0/app/lang/en/reminders.php)에 맞게 변경하십시오. 

### Environment Detection Updates
### 구동환경 감지 업데이트 

For security reasons, URL domains may no longer be used to detect your application environment. These values are easily spoofable and allow attackers to modify the environment for a request. You should convert your environment detection to use machine host names (`hostname` command on Mac, Linux, and Windows).

보안의 이유로 어플리케이션의 구동 환경을 감지 하기 위해서 URL 도메인은 더 이상 사용되지 않습니다. 이 값은 손쉽게 요청시에 변경이 가능하여 공격을 가능하게 합니다. 머신의 호스트 네임 (맥, 리눅스, 윈도우 에서 `hostname` 명령어를 사용하십시오) 을 사용하도록 구동 환경 감지 로직을 변경해야 합니다. 

### Simpler Log Files
### 로그 파일 간략화

Laravel now generates a single log file: `app/storage/logs/laravel.log`. However, you may still configure this behavior in your `app/start/global.php` file.

라라벨은 이제 `app/storage/logs/laravel.log` 라는 하나의 파일만을 생성합니다. 그러나 이 동작은 `app/start/global.php` 파일에서 설정이 가능합니다. 

### Removing Redirect Trailing Slash
### Trailing Slash 리다이렉션 제거

In your `bootstrap/start.php` file, remove the call to `$app->redirectIfTrailingSlash()`. This method is no longer needed as this functionality is now handled by the `.htaccess` file included with the framework.

`bootstrap/start.php` 파일안에서 `$app->redirectIfTrailingSlash()` 호출을 제거 하십시오. 이 기능은 프레임워크에 포함되어 있는 `.htaccess` 파일에서 처리되어 더이상 메소드가 필요하지 않습니다. 

Next, replace your Apache `.htaccess` file with [this new one](https://github.com/laravel/laravel/blob/v4.1.0/public/.htaccess) that handles trailing slashes.

다음으로 `public/.htaccess` 파일을 [새버전의 파일](https://github.com/laravel/laravel/blob/v4.1.0/public/.htaccess) 으로 교체합니다.

### Current Route Access
### 현재 라우트에 엑세스하기

The current route is now accessed via `Route::current()` instead of `Route::getCurrentRoute()`.

`Route::getCurrentRoute()` 대신에 `Route::current()`를 통해서 현재 라우트에 엑세스 할 수 있습니다.  

### Composer Update
### 컴포저 업데이트

Once you have completed the changes above, you can run the `composer update` function to update your core application files! If you receive class load errors, try running the `update` command with the `--no-scripts` option enabled like so: `composer update --no-scripts`.

변경사항 적용을 완료하면 `composer update` 를 실행하여 어플리케이션 코어 파일을 업데이트 할 수 있습니다. 로딩과 관련된 에러가 발생한다면 다음처럼 `--no-scripts` 옵션과 함께 `update` 명령어를 실행하십시오 `composer update --no-scripts`.

### Wildcard Event Listeners
### 와일드카드 이벤트 리스너

The wildcard event listeners no longer append the event to your handler functions parameters. If you require finding the event that was fired you should use `Event::firing()`.

와일드 카드 이벤트 리스너는 더이상 핸들러 함수의 인자에 이벤트를 전달하지 않습니다.  발행된 이벤트를 확인해야 할 필요가 다면, `Event::firing()` 를 사용하십시오.
