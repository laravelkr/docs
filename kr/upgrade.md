# Upgrade Guide
# 업그레이드 가이드

- [Upgrading To 5.3.0 From 5.2](#upgrade-5.3.0)
- [5.2에서 5.3.0 으로 업그레이드 하기](#upgrade-5.3.0)
- [Upgrading To 5.2.0 From 5.1](#upgrade-5.2.0)
- [5.1에서 5.2.0 으로 업그레이드 하기](#upgrade-5.2.0)
- [Upgrading To 5.1.11](#upgrade-5.1.11)
- [5.1.11 으로 업그레이드 하기](#upgrade-5.1.11)
- [Upgrading To 5.1.0](#upgrade-5.1.0)
- [5.1.0 으로 업그레이드 하기](#upgrade-5.1.0)
- [Upgrading To 5.0.16](#upgrade-5.0.16)
- [5.0.16 으로 업그레이드 하기](#upgrade-5.0.16)
- [Upgrading To 5.0 From 4.2](#upgrade-5.0)
- [4.2에서 5.0 으로 업그레이드 하기](#upgrade-5.0)
- [Upgrading To 4.2 From 4.1](#upgrade-4.2)
- [4.1에서 4.2 으로 업그레이드 하기](#upgrade-4.2)
- [Upgrading To 4.1.29 From <= 4.1.x](#upgrade-4.1.29)
- [4.1.x 이하에서 4.1.29 으로 업그레이드 하기](#upgrade-4.1.29)
- [Upgrading To 4.1.26 From <= 4.1.25](#upgrade-4.1.26)
- [4.1.25 이하에서 4.1.26 으로 업그레이드 하기](#upgrade-4.1.26)
- [Upgrading To 4.1 From 4.0](#upgrade-4.1)
- [4.0에서 4.1 으로 업그레이드 하기](#upgrade-4.1)

<a name="upgrade-5.3.0"></a>
## Upgrading To 5.3.0 From 5.2
## 5.2에서 5.3.0 으로 업그레이드 하기

#### Estimated Upgrade Time: 2-3 Hours
#### 업그레이드 예상 시간 : 2-3시간 이하

> {note} We attempt to document every possible breaking change. Since some of these breaking changes are in obscure parts of the framework only a portion of these changes may actually affect your application.

> {note} 가능한 모든 변경 내용을 기록하려고 했습니다. 그렇지만 변경 사항 중 일부는 프레임워크의 명확하지 않은 부분에서 이루어 지기 때문에 이중 일부가 실제 애플리케이션에 영향을 끼칠 수도 있습니다.

### Updating Dependencies
### 의존성 업데이트

Update your `laravel/framework` dependency to `5.3.*` in your `composer.json` file.

`composer.json`파일에 있는 `laravel/framework` 의존성을 `5.3.*` 로 변경합니다.

You should also upgrade your `symfony/css-selector` and `symfony/dom-crawler` dependencies to `3.1.*` in the `require-dev` section of your `composer.json` file.

또한 `composer.json` 파일의 `require-dev` 섹션에 있는 `symfony/css-selector` 와 `symfony/dom-crawler` 의 의존성을 `3.1.*`으로 업그레이드 해야합니다.

### PHP & HHVM
### PHP & HHVM

Laravel 5.3 requires PHP 5.6.4 or higher. HHVM is no longer officially supported as it does not contain the same language features as PHP 5.6+.

라라벨 5.3은 PHP 5.6.4 이상을 필요로 합니다. HHVM은 PHP 5.6 이상과 동일한 언어 기능을 포함하지 않으므로 더 이상 공식적으로 지원되지 않습니다.

### Deprecations
### 비추천 사항들(deprecated)

All of the deprecations listed in the [Laravel 5.2 upgrade guide](#5.2-deprecations) have been removed from the framework. You should review this list to verify you are no longer using these deprecated features.

[라라벨 5.2 업그레이드 가이드](#5.2-deprecations)에서 나열되었던 비추천(deprecated) 사항들이 프레임워크에서 제거되었습니다. 이 목록을 확인하고 deprecated 기능들을 더 이상 사용하지 않는지 확인해야합니다.

### Application Service Providers
### 애플리케이션 서비스 프로바이더

You may remove the arguments from the `boot` method on the `EventServiceProvider`, `RouteServiceProvider`, and `AuthServiceProvider` classes. Any calls to the given arguments may be converted to use the equivalent [facade](/docs/5.3/facades) instead. So, for example, instead of calling methods on the `$dispatcher` argument, you may simply call the `Event` facade. Likewise, instead of making method calls to the `$router` argument, you may make calls to the `Route` facade, and instead of making method calls to the `$gate` argument, you may make calls to the `Gate` facade.

`EventServiceProvider`, `RouteServiceProvider`, 그리고 `AuthServiceProvider` 클래스의 `boot` 메소드에서 인자를 제거하십시오. 주어진 인자에 대한 호출은 동일한 [파사드](/docs/5.3/facades)기능으로 대채하여 사용하십시오. 예를 들어, `$dispatcher` 인자의 메소드를 호출하는 대신에 `Event` 파사드를 호출하십시오. 마찬가지로 `$router` 인자의 메소드 호출 대신에 `Route` 파사드 호출을 사용하십시오. 또한 `$gate` 인자의 메소드 호출을 `Gate` 파사드를 사용하도록 변경하십시오.

> {note} When converting method calls to facades, be sure to import the facade class into your service provider.

> {note} 메소드 호출을 파사드로 변경할 때, 서비스 프로바이더 상단에 파사드를 import(사용하도록 선언하는것) 해야합니다.

### Arrays
### 배열

#### Key / Value Order Change
#### 키 / 값 순서 변경

The `first`, `last`, and `where` methods on the `Arr` class, in addition to their associated global helper functions, now pass the "value" as the first parameter to the given callback Closure. For example:

`Arr` 클래스의 `first`, `last`, 그리고  `where` 메소드, 그리고 이와 연관된 글로벌 헬퍼 함수들은 이제 "값-value"을 주어진 콜백 클로저의 첫번째 인자로 전달 받습니다. 예를 들어:

    Arr::first($array, function ($value, $key) {
        return ! is_null($value);
    });

In previous versions of Laravel, the `$key` was passed first. Since most use cases are only interested in the `$value` it is now passed first. You should do a "global find" in your application for these methods to verify that you are expecting the `$value` to be passed as the first argument to your Closure.

이번 버전의 라라벨에서는 `$key` 가 먼저 사용되었습니다. 대부분의 사용예에서는 `$value` 가 주요하게 관심대상이기 때문에, 이 순서가 변경되었습니다. 애플리케이션의 "전체 검색"을 통해서 이 메소드를 사용한 곳을 찾아서 첫번째 인자로 `$value`를 전달받도록 하십시오.

### Artisan
### 아티즌

##### The `make:console` Command
##### `make:console` 명령어

The `make:console` command has been renamed to `make:command`.

`make:console` 명령어는 `make:command`으로 이름이 변경되었습니다.

### Authentication
### 사용자 인증

#### Authentication Scaffolding
#### 인증 스캐폴딩

The two default authentication controllers provided with the framework have been split into four smaller controllers. This change provides cleaner, more focused authentication controllers by default. The easiest way to upgrade your application to the new authentication controllers is to [grab a fresh copy of each controller from GitHub](https://github.com/laravel/laravel/tree/5.3/app/Http/Controllers/Auth) and place them into your application.

기본적으로 프레임워크에서 제공되던 두개의 기본 인증 컨트롤러는 보다 작은 네 개의 컨트롤러로 분리되었습니다. 이 변경사항을 통해서 인증 컨트롤러는 보다 깔끔하고 그 역할 자체에 집중하도록 하였습니다. 애플리케이션이 새로운 인증 컨트롤러를 사용하도록 업그레이드 하는 가장 쉬운 방법은 [Github 에서 각각의 컨트롤러를 복사하고 애플리케이션에 복사하는 방법](https://github.com/laravel/laravel/tree/5.3/app/Http/Controllers/Auth)입니다.

You should also make sure that you are calling the `Auth::routes()` method in your `routes/web.php` file. This method will register the proper routes for the new authentication controllers.

`routes/web.php` 파일에서 `Auth::routes()` 메소드를 호출되고 있는지 확인해야 합니다. 이 메소드는 새로운 인증 컨트롤러에 대한 적절한 라우트를 등록합니다.

Once these controllers have been placed into your application, you may need to re-implement any customizations you made to these controllers. For example, if you are customizing the authentication guard that is used for authentication, you may need to override the controller's `guard` method. You can examine each authentication controller's trait to determine which methods to override.

새로운 컨트롤러를 애플리케이션에 복사하고 난 뒤에는, 커스터마이징한 부분들을 다시 구현해야 합니다. 예를 들어, 인증에 사용되는 인증 Guard를 수정했었다면, 컨트롤러의 `guard` 메소드를 오버라이딩 해야합니다. 각각의 인증 컨트롤러의 트레이트-trait를 살펴보고 오버라이드가 필요한지 확인할 수 있습니다.

> {tip} If you were not customizing the authentication controllers, you should just be able to drop in fresh copies of the controllers from GitHub and verify that you are calling the `Auth::routes` method in your `routes/web.php` file.

> {tip} 인증 컨트롤러를 특별히 커스터마이징 한적이 없다면, Github에서 컨트롤러를 복사하고 `routes/web.php` 파일에서 `Auth::routes` 메소드가 호출되는 것만 확인하면 됩니다.

#### Password Reset Emails
#### 패스워드 재설정 이메일

Password reset emails now use the new Laravel notifications feature. If you would like to customize the notification sent when sending password reset links, you should override the `sendPasswordResetNotification` method of the `Illuminate\Auth\Passwords\CanResetPassword` trait.

패스워드 재설정 이메일을 보내는 기능은 이제 새로운 라라벨의 알림 기능을 사용합니다. 패스워드 재설정 링크를 보낼 때, 알림을 사용하도록 커스터마이징 하려는 경우 `Illuminate\Auth\Passwords\CanResetPassword` 트레이트-trait 의 `sendPasswordResetNotification` 메소드를 오버라이딩 해야합니다.

Your `User` model **must** use the new `Illuminate\Notifications\Notifiable` trait in order for password reset link emails to be delivered:

패스워드 재설정 링크 이메일이 정상적으로 발송되게 하려면 `User` 모델이 반드시 `Illuminate\Notifications\Notifiable` trait-트레이트를 사용해야합니다:

    <?php

    namespace App;

    use Illuminate\Notifications\Notifiable;
    use Illuminate\Foundation\Auth\User as Authenticatable;

    class User extends Authenticatable
    {
        use Notifiable;
    }

> {note} Don't forget to register the `Illuminate\Notifications\NotificationServiceProvider` in the `providers` array of your `config/app.php` configuration file.

> {note} `config/app.php` 설정 파일의 `providers` 배열에 `Illuminate\Notifications\NotificationServiceProvider`를 등록하는 것을 잊지 마십시오.

#### POST To Logout
#### 로그아웃을 할 때 POST 사용

The `Auth::routes` method now registers a `POST` route for `/logout` instead of a `GET` route. This prevents other web applications from logging your users out of your application. To upgrade, you should either convert your logout requests to use the `POST` verb or register your own `GET` route for the `/logout` URI:

`Auth:routes` 메소드는 이제 `/logout` 라우트를 `GET` 이 아닌 `POST` 로 등록합니다. 이렇게 하면 다른 웹 애플리케이션이 여러분의 애플리케이션에서 사용자를 로그아웃 시키는 것을 방지합니다. 업그레이드 하려면, 로그아웃 request-요청이 `POST` verb를 사용하도록 수정하거나, `/logout` UIR에 대한 `GET` 라우트를 등록하십시오:

    Route::get('/logout', 'Auth\LoginController@logout');

### Authorization
### Authorization-승인

#### Calling Policy Methods With Class Names
#### 클래스 이름으로 Policy 메소드 호출

Some policy methods only receive the currently authenticated user and not an instance of the model they authorize. This situation is most common when authorizing `create` actions. For example, if you are creating a blog, you may wish to check if a user is authorized to create any posts at all.

일부 정책-policy 메소드는 승인된 모델의 인스턴스가 아닌 현재 인증 된 사용자만을 전달 받습니다. 이러한 상황은 `create`액션을 승인 할 때가 가장 일반적인 경우입니다. 예를 들어, 블로그를 만드는 경우 사용자가 게시물을 만들 권한이 있는지 확인할 수 있습니다.

When defining policy methods that will not receive a model instance, such as a `create` method, the class name will no longer be passed as the second argument to the method. Your method should just expect the authenticated user instance:

모델 인스턴스를 받지 않는 `create` 메소드와 같은 policy-정책 메소드를 정의할 때, 메소드의 두번째 인자로 클래스 이름은 전달되지 않습니다. 메소드는 인증된 사용자 인스턴스만을 받을 수 있도록 되어 있습니다.

    /**
     * Determine if the given user can create posts.
     *
     * @param  \App\User  $user
     * @return bool
     */
    public function create(User $user)
    {
        //
    }

#### The `AuthorizesResources` Trait
#### `AuthorizesResources` 트레이트-trait

The `AuthorizesResources` trait has been merged with the `AuthorizesRequests` trait. You should remove the `AuthorizesResources` trait from your `app/Http/Controllers/Controller.php` file.

`AuthorizesResources` 트레이트-trait는 `AuthorizesRequests` 트레이트-trait에 통합되었습니다. `app/Http/Controllers/Controller.php` 파일에서 `AuthorizesResources` 트레이트-trait은 제거해야합니다.

### Blade
### 블레이드

#### Custom Directives
#### 사용자 정의 지시어

In prior versions of Laravel, when registering custom Blade directives using the `directive` method, the `$expression` passed to your directive callback contained the outer-most parenthesis. In Laravel 5.3, these outer-most parenthesis are not included in the expression passed to your directive callback. Be sure to review the [Blade extension](/docs/5.3/blade#extending-blade) documentation and verify your custom Blade directives are still working properly.

이전 버전의 라라벨에서는 `directive` 메소드를 사용하여 사용자 정의 블레이드 지시어를 등록할 때, 지시어 콜백에 전달된 `$expression`에는 가장 바깥 쪽 괄호가 포함되어 있습니다. 라라벨 5.3에서는 지시어의 콜백에 전달되는 expressiond에는 가장 바깥쪽 괄호가 포함되어 있지 않습니다. [블레이드 확장](/docs/5.3/blade#extending-blade) 문서를 확인하고 여러분의 커스텀 블레이드 지시어가 여전히 동작하는지 점검하십시오.

(역자주 : `directive` 메소드에 전달되는 `$expression`의 형태 또는 값이 변경되었다는 의미입니다. 스코프가 달라졌으니. 업그레이드 이후 정상적으로 동작하는지 확인하십시오.)

### Broadcasting
### 브로드캐스팅

#### Service Provider
#### 서비스 프로바이더

Laravel 5.3 includes significant improvements to [event broadcasting](/docs/{{version}}/broadcasting). You should add the new `BroadcastServiceProvider` to your `app/Providers` directory by [grabbing a fresh copy of the source from GitHub](https://raw.githubusercontent.com/laravel/laravel/5.3/app/Providers/BroadcastServiceProvider.php). Once you have defined the new service provider, you should add it to the `providers` array of your `config/app.php` configuration file.

라라벨 5.3에서는 [이벤트 브로드캐스팅](/docs/{{version}}/broadcasting)에 대한 많은 개선이 있었습니다. `app/Providers` 디렉토리에 `BroadcastServiceProvider` 를 [GitHub에서 소스를 복사하여](https://raw.githubusercontent.com/laravel/laravel/develop/app/Providers/BroadcastServiceProvider.php) 새로 추가해야합니다. 새로운 서비스 프로바이더를 정의하고 나면 `config/app.php` 설정 파일에서 `providers` 배열에 이를 추가해야 합니다.

Next, add the new `broadcasting.php` configuration file to your `app/config` directory by [grabbing a fresh copy of the source from GitHub](https://raw.githubusercontent.com/laravel/laravel/5.3/config/broadcasting.php).

다음으로, `app/config` 디렉토리에 [GitHub 소스에서 복사한](https://raw.githubusercontent.com/laravel/laravel/5.3/config/broadcasting.php) `broadcasting.php` 설정 파일을 새로 추가하십시오.

### Cache
### 캐시

#### Extension Closure Binding & `$this`
#### 클로저 바인딩 확장 & `$this`

When calling the `Cache::extend` method with a Closure, `$this` will be bound to the `CacheManager` instance, allowing you to call its methods from within your extension Closure:

`Cache::extend` 메소드를 클로저와 함께 호출할 때, `$this` 는 `CacheManager` 인스턴스에 바인딩 되어 있기 때문에, 클로저 안에서 해당 매니저의 메소드를 호출할 수 있습니다:

    Cache::extend('memcached', function ($app, $config) {
        try {
            return $this->createMemcachedDriver($config);
        } catch (Exception $e) {
            return $this->createNullDriver($config);
        }
    });

### Cashier
### 캐셔

If you are using Cashier, you should upgrade your `laravel/cashier` package to the `~7.0` release. This release of Cashier only upgrades a few internal methods to be compatible with Laravel 5.3 and is not a breaking change.

캐셔를 사용중이라면, `laravel/cashier` 패키지를 `~7.0`릴리즈로 업그레이드 해야합니다. 캐셔의 이번 릴리즈는 몇가지 내부 메소드가 라라벨 5.3에 맞추어 호환되도록 변경되었습니다.

### Collections
### 컬렉션

#### Key / Value Order Change
#### 키 / 값 순서 변경

The `first`, `last`, and `contains` collection methods all pass the "value" as the first parameter to their given callback Closure. For example:

`first`, `last`, 그리고 `contains` 컬렉션 메소드는 이제 "값"을 주어진 콜백 클로저의 첫번째 인자로 전달 받습니다. 예를 들어:

    $collection->first(function ($value, $key) {
        return ! is_null($value);
    });

In previous versions of Laravel, the `$key` was passed first. Since most use cases are only interested in the `$value` it is now passed first. You should do a "global find" in your application for these methods to verify that you are expecting the `$value` to be passed as the first argument to your Closure.

이번 버전의 라라벨에서는 `$key` 가 먼저 사용되었습니다. 대부분의 사용예에서는 `$value` 가 주요하게 관심대상이기 때문에, 이 순서가 변경되었습니다. 애플리케이션의 "전체 검색"을 통해서 이 메소드를 사용한 곳을 찾아서 첫번째 인자로 `$value`를 전달받도록 하십시오.

#### Collection `where` Comparison Methods Are "Loose" By Default
#### 컬렉션 `where` 메소드는 기본적으로 "느슨한" 비교를 수행합니다

A collection's `where` method now performs a "loose" comparison by default instead of a strict comparison. If you would like to perform a strict comparison, you may use the `whereStrict` method.

컬렉션의 `where` 메소드는 이제 기본적으로 엄격한 비교 대신에 "느슨한" 비교를 수행합니다. 엄격한 비교를 수행하고자 한다면, `whereStrict` 메소드를 사용할 수 있습니다.

Due to this change, the `whereLoose` method was removed from the collection class.

이 변경사항으로 인해서 컬렉션 클래스에서 `whereLoose` 메서드가 제거되었습니다.

The `where` method also no longer accepts a third parameter to indicate "strictness". You should explicitly call either `where` or `whereStrict` depending on your application's needs.

`where` 메소드는 더이상 "엄격함"을 나타내는 세번째 인자를 받지 않습니다. 애플리케이션에서 필요에 따라 `where` 또는 `whereStrict` 메소드를 명확하게 호출해야합니다.

### Configuration
### 설정

#### Application Name
#### 애플리케이션 이름

In the `config/app.php` configuration file, add the following configuration option:

`config/app.php` 설정 파일에 다음의 설정 옵션을 추가하십시오:

    'name' => 'Your Application Name',

### Controllers
### 컨트롤러

<a name="5.3-session-in-constructors"></a>
#### Session In The Constructor
#### 생성자에서 세션

In previous versions of Laravel, you could access session variables or the authenticated user in your controller's constructor. This was never intended to be an explicit feature of the framework. In Laravel 5.3, you can't access the session or authenticated user in your controller's constructor because the middleware has not run yet.

이전 버전의 라라벨에서, 컨트롤러의 생성자 안에서 세션 변수나, 인증된 사용자에 엑세스 할 수 있었습니다. 이것은 프레임워크의 기능으로 명확하게 의도된 것은 아닙니다. 라라벨 5.3에서는 미들웨어가 아직 실행되지 않았기 때문에, 컨트롤러의 생성자에서 세션이나 인증된 사용자에 엑세스 할 수 없습니다.

As an alternative, you may define a Closure based middleware directly in your controller's constructor. Before using this feature, make sure that your application is running Laravel `5.3.4` or above:

이 대신에, 클로저 기반의 미들웨어를 컨트롤러의 생성자에 직접 정의할 수 있습니다. 이 기능을 사용하기 전에, 애플리케이션이 라라벨 `5.3.4` 이상에서 동작하고 있는지 확인하십시오:

    <?php

    namespace App\Http\Controllers;

    use App\User;
    use Illuminate\Support\Facades\Auth;
    use App\Http\Controllers\Controller;

    class ProjectController extends Controller
    {
        /**
         * All of the current user's projects.
         */
        protected $projects;

        /**
         * Create a new controller instance.
         *
         * @return void
         */
        public function __construct()
        {
            $this->middleware(function ($request, $next) {
                $this->projects = Auth::user()->projects;

                return $next($request);
            });
        }
    }

Of course, you may also access the request session data or authenticated user by type-hinting the `Illuminate\Http\Request` class on your controller action:

물론, 컨트롤러 액션에서 `Illuminate\Http\Request` 클래스를 타입 힌트로 지정하여 request-요청의 세션 데이터나 인증된 사용자에 엑세스 할 수 있습니다:

    /**
     * Show all of the projects for the current user.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return Response
     */
    public function index(Request $request)
    {
        $projects = $request->user()->projects;

        $value = $request->session()->get('key');

        //
    }

### Database
### 데이터베이스

#### Collections
#### 컬렉션

The [fluent query builder](/docs/{{version}}/queries) now returns `Illuminate\Support\Collection` instances instead of plain arrays. This brings consistency to the result types returned by the fluent query builder and Eloquent.

[쿼리 빌더](/docs/{{version}}/queries)는 이제 순수 배열 대신에 `Illuminate\Support\Collection` 인스턴스를 반환합니다. 이렇게 함으로써, 쿼리 빌더와 Eloquent에 의해서 반환되는 결과 타입의 일관성을 획득 할 수 있습니다.

If you do not want to migrate your query builder results to `Collection` instances, you may chain the `all` method onto your calls to the query builder's `get` or `pluck` methods. This will return a plain PHP array of the results, allowing you to maintain backwards compatibility:

쿼리빌더가 `Collection` 인스턴스로 결과를 반환하도록 마이그레이션하는 것을 원하지 않는다면, 쿼리 빌더의 `get` 또는 `pluck` 메소드에 `all` 메소드를 체이닝하여 사용할 수 있습니다. 이렇게하면 결과의 일반 PHP 배열이 반환되므로 이전 버전과의 호환성을 유지할 수 있습니다:

    $users = DB::table('users')->get()->all();

    $usersIds = DB::table('users')->pluck('id')->all();

#### Eloquent `getRelation` Method
#### Eloquent `getRelation` 메소드

The Eloquent `getRelation` method no longer throws a `BadMethodCallException` if the relation can't be loaded. Instead, it will throw an `Illuminate\Database\Eloquent\RelationNotFoundException`. This change will only affect your application if you were manually catching the `BadMethodCallException`.

Eloquent의 `getRelation` 메소드는 relation이 로드 할 수 없는 경우에 `BadMethodCallException`를 발생시키지 않습니다. 그 대신에, `Illuminate\Database\Eloquent\RelationNotFoundException`를 발생시킵니다. 이 변경사항은 애플리케이션에서 직접 `BadMethodCallException`을 catch 한 경우에만 영향을 받습니다.

#### Eloquent `$morphClass` Property
#### Eloquent `$morphClass` 속성

The `$morphClass` property that could be defined on Eloquent models has been removed in favor of defining a "morph map". Defining a morph map provides support for eager loading and resolves additional bugs with polymorphic relations. If you were previously relying on the `$morphClass` property, you should migrate to `morphMap` using the following syntax:

Eloquent 모델에 정의할 수 있는 `$morphClass` 속성은 "morph map"을 정의할 수 있게 되면서 제거되었습니다. morph map을 정의하는 것은 eager 로딩과 다형성 관계-relation의 불필요한 버그들을 해결하기 위해서 마련되었습니다. 이전의 `$morphClass` 속성을 사용했다면, 다음 문법을 사용하여 `morphMap` 을 이용하도록 전환해야합니다:

```php
Relation::morphMap([
    'YourCustomMorphName' => YourModel::class,
]);
```

For example, if you previously defined the following `$morphClass`:

예를 들어, 이전버전에서 다음과 같은 `$morphClass` 정의를하고 있었다면:

```php
class User extends Model
{
    protected $morphClass = 'user'
}
```

You should define the following `morphMap` in the `boot` method of your `AppServiceProvider`:

`AppServiceProvider`의 `boot` 메소드에서 다음과 같이 `morphMap`을 정의해야합니다.

```php
use Illuminate\Database\Eloquent\Relations\Relation;

Relation::morphMap([
    'user' => User::class,
]);
```

#### Eloquent Scopes
#### Eloquent 스코프

Eloquent scopes now respect the leading boolean of scope constraints. For example, if you are starting your scope with an `orWhere` constraint it will no longer be converted to normal `where`. If you were relying on this feature (e.g. adding multiple `orWhere` constraints within a loop), you should verify that the first condition is a normal `where` to avoid any boolean logic issues.

Eloquent 스코프는 이제 범위 제약 조건의 첫번째 불리언 값을 확인합니다. 예를 들어 `orWhere` 제한으로 스코프를 시작하는 경우, 이는 더이상 일반적인 `where` 로 변환되지 않습니다. 이 기능을 이전과 같이 사용하려면 (예를 들어, 반복문 안에서 여러개의 `orWhere` 제한을 추가하는 경우와 같은), 불리언 논리 문제를 발생시키지 않게 하기 위해서, 첫번째 조건이 일반적인 `where` 인지 확인해야 합니다.

If your scopes begin with `where` constraints no action is required. Remember, you can verify your query SQL using the `toSql` method of a query:

스코프가 `where` 제약조건으로 시작하는 경우에는 변경이 필요하지 않습니다. 쿼리에서 `toSql` 메소드를 사용하여 실제 쿼리 SQL이 어떠한지 확인할 수 있다는 것을 기억하십시오:

    User::where('foo', 'bar')->toSql();

#### Join Clause
#### Join-조인 구문

The `JoinClause` class has been rewritten to unify its syntax with the query builder. The optional `$where` parameter of the `on` clause has been removed. To add a "where" conditions you should explicitly use one of the `where` methods offered by the [query builder](/docs/{{version}}/queries#where-clauses):

`JoinClause` 클래스는 쿼리빌더와 문법을 통합하기 위해서 새롭게 작성되었습니다. `on` 구문에서 선택적으로 전달되던 `$where`은 제거되었습니다. "where" 조건을 추가하려면 [쿼리 빌더](/docs/{{version}}/queries#where-clauses)에서 제공하는 `where` 메소드를 명시적으로 사용해야 합니다:

    $query->join('table', function ($join) {
        $join->on('foo', 'bar')->where('bar', 'baz');
    });

The operator of the `on` clause is now validated and can no longer contain invalid values. If you were relying on this feature (e.g. `$join->on('foo', 'in', DB::raw('("bar")'))`) you should rewrite the condition using the appropriate where clause:

`on` 구문의 연산자는 이제 유효해야하며, 유효하지 않은 값은 더이상 포함할 수 없습니다. 이전버전과 동일하게 사용하기 위해서는 (예를 들어, `$join->on('foo', 'in', DB::raw('("bar")'))`), 적절한 where 절을 사용하여 조건구문을 다시 작성해야 합니다:

    $join->whereIn('foo', ['bar']);

The `$bindings` property was also removed. To manipulate join bindings directly you may use the `addBinding` method:

`$bindings`속성은 삭제되었습니다. JOIN 조인 바인딩을 직접 조작하려면 `addBinding` 메소드를 사용하십시오:

    $query->join(DB::raw('('.$subquery->toSql().') table'), function ($join) use ($subquery) {
        $join->addBinding($subquery->getBindings(), 'join');
    });

### Encryption
### 암호화

#### Mcrypt Encrypter Has Been Removed
#### Mcrypt 제거

The Mcrypt encrypter was deprecated during the Laravel 5.1.0 release in June 2015. This encrypter has been totally removed in the 5.3.0 release in favor of the newer encryption implementation based on OpenSSL, which has been the default encryption scheme for all releases since Laravel 5.1.0.

Mcrypt encrypter 는 2015년 6월 릴리즈된 라라벨 5.1.0에서 부터 deprecated 되었습니다. 이 encrypter 는 OpenSSL 기반의 새로운 암호화 구현을 위해서 라라벨 5.3.0 릴리즈에서 완전히 삭제되었습니다. 라라벨 5.1.0 이후 모든 릴리즈는 이 암호화 스키마가 기본이었습니다.

If you are still using an Mcrypt based `cipher` in your `config/app.php` configuration file, you should update the cipher to `AES-256-CBC` and set your key to a random 32 byte string which may be securely generated using `php artisan key:generate`.

`config/app.php` 설정 파일에서, 아직 Mcrypt를 기반으로한 `chiper`을 사용중이라면, 이를 `AES-256-CBC` 으로 변경하고 `php artisan key:generate` 명령어를 사용하여 생성할 수 있는 보다 안전한 랜덤 32바이트 문자열을 설정하도록 해야합니다.

If you are storing encrypted data in your database using the Mcrypt encrypter, you may install the `laravel/legacy-encrypter` [package](https://github.com/laravel/legacy-encrypter) which includes the legacy Mcrypt encrypter implementation. You should use this package to decrypt your encrypted data and re-encrypt it using the new OpenSSL encrypter. For example, you may do something like the following in a [custom Artisan command](/docs/{{version}}/artisan):

만약 Mcrypt encrypter 를 사용하여 암호화한 데이터를 데이터베이스에 저장하여 사용중이라면, 레거시 Mcrypt 암호화 구현을 위한 `laravel/legacy-encrypter` [패키지](https://github.com/laravel/legacy-encrypter)를 설치할 수 있습니다. 이 패키지를 사용하여 암호화된 데이터를 복호한다음에 새로운 OpenSSL encrypter 를 사용하여 다시 암호화를 수행해야합니다. 예를 들어 다음의 [사용자 정의 아티즌 명령어](/docs/{{version}}/artisan)를 사용하여 처리할 수 있습니다:

    $legacy = new McryptEncrypter($encryptionKey);

    foreach ($records as $record) {
        $record->encrypted = encrypt(
            $legacy->decrypt($record->encrypted)
        );

        $record->save();
    }

### Exception Handler
### 예외-Exception 핸들러

#### Constructor
#### 생성자

The base exception handler class now requires a `Illuminate\Container\Container` instance to be passed to its constructor. This change will only affect your application if you have defined a custom `__construct` method in your `app/Exceptions/Handler.php` file. If you have done this, you should pass a container instance into the `parent::__construct` method:

베이스 exception handler 클래스는 이제 생성자에서 `Illuminate\Container\Container` 인스턴스를 필요로 합니다. 이 변경사항은 여러분이 `app/Exceptions/Handler.php` 파일에서 직접 `__construct` 메소드를 정의하였다면 영향을 받을 수 있습니다. 만약 그렇다면, `parent::__construct` 메소드에 컨테이너 인스턴스를 전달하도록 해야합니다:

    parent::__construct(app());

#### Unauthenticated Method
#### `Unauthenticated` 메소드

You should add the `unauthenticated` method to your `App\Exceptions\Handler` class. This method will convert authentication exceptions into HTTP responses:

`App\Exceptions\Handler` 클래스에 `unauthenticated` 메소드를 추가해야 합니다. 이 메소드는 인증 관련 exception을 HTTP 응답-response로 변환합니다:

    /**
     * Convert an authentication exception into an unauthenticated response.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Illuminate\Auth\AuthenticationException  $exception
     * @return \Illuminate\Http\Response
     */
    protected function unauthenticated($request, AuthenticationException $exception)
    {
        if ($request->expectsJson()) {
            return response()->json(['error' => 'Unauthenticated.'], 401);
        }

        return redirect()->guest('login');
    }

### Middleware
### 미들웨어

#### `can` Middleware Namespace Change
#### `can` 미들웨어 네임스페이스 변경

The `can` middleware listed in the `$routeMiddleware` property of your HTTP kernel should be updated to the following class:

HTTP 커널의 `$routeMiddleware` 속성에 나열되어 있던 `can` 미들웨어는 다음 클래스처럼 변경 되어야 합니다:

    'can' => \Illuminate\Auth\Middleware\Authorize::class,

#### `can` Middleware Authentication Exception
#### `can` 미들웨어 사용자 인증 Exception

The `can` middleware will now throw an instance of `Illuminate\Auth\AuthenticationException` if the user is not authenticated. If you were manually catching a different exception type, you should update your application to catch this exception. In most cases, this change will not affect your application.

`can` 미들웨어는 이제, 사용자가 인정되지 않았으면 `Illuminate\Auth\AuthenticationException` 을 발생시킵니다. 수동으로 다른 유형의 exception을 catch하고 있었다면, 애플리케이션이 이 exception을 catch 하도록 변경해야 합니다. 대부분의 경우에는 이 변경사항은 애플리케이션에 영향을 끼치지 않을 것입니다.

#### Binding Substitution Middleware
#### 바인딩 대체 미들웨어

Route model binding is now accomplished using middleware. All applications should add the `Illuminate\Routing\Middleware\SubstituteBindings` to your `web` middleware group in your `app/Http/Kernel.php` file:

라우트 모델 바인딩은 이제 미들웨어를 사용하여 처리됩니다. 모든 애플리케이션은 `app/Http/Kernel.php` 파일 의 `web` 미들웨어 그룹에 `Illuminate\Routing\Middleware\SubstituteBindings` 을 추가해야 합니다:

    \Illuminate\Routing\Middleware\SubstituteBindings::class,

You should also register a route middleware for binding substitution in the `$routeMiddleware` property of your HTTP kernel:

또한, 바인딩을 대체하기 위해서 HTTP Kernel의 `$routeMiddleware` 속성에 라우트 미들웨어를 등록해야 합니다:

    'bindings' => \Illuminate\Routing\Middleware\SubstituteBindings::class,

Once this route middleware has been registered, you should add it to the `api` middleware group:

라우트 미들웨어를 등록하고 나면, `api` 미들웨어 그룹에 다음과 같이 추가하십시오:

    'api' => [
        'throttle:60,1',
        'bindings',
    ],

### Notifications
### 알림-Notifications

#### Installation
#### 설치하기

Laravel 5.3 includes a new, driver based notification system. You should register the `Illuminate\Notifications\NotificationServiceProvider` in the `providers` array of your `config/app.php` configuration file.

라라벨 5.3은 새로운 드라이버 기반의 알림 시스템을 포함하고 있습니다. `config/app.php` 설정 파일의 `providers` 배열에 `Illuminate\Notifications\NotificationServiceProvider`를 등록해야합니다.

You should also add the `Illuminate\Support\Facades\Notification` facade to the `aliases` array of your `config/app.php` configuration file.

또한 `config/app.php` 설정 파일의 `aliases` 배열에 `Illuminate\Support\Facades\Notification` 파사드를 추가해야합니다.

Finally, you may use the `Illuminate\Notifications\Notifiable` trait on your `User` model or any other model you wish to receive notifications.

마지막으로, `User` 모델 또는 알림을 수신할 다른 모델에 `Illuminate\Notifications\Notifiable` 트레이트-trait을 사용하도록 해야합니다.

### Pagination
### 페이지네이션

#### Customization
#### 사용자 정의

Customizing the paginator's generated HTML is much easier in Laravel 5.3 compared to previous Laravel 5.x releases. Instead of defining a "Presenter" class, you only need to define a simple Blade template. The easiest way to customize the pagination views is by exporting them to your `resources/views/vendor` directory using the `vendor:publish` command:

라라벨 5.3에서는 이전의 라라벨 5.x 릴리즈와 비교하여 페이지네이터가 생성하는 HTML을 수정하기가 훨씬 수월해졌습니다. "Presener" 클래스를 정의하는 대신에, 간단한 블레이드 템플릿 하나만 정의하면 됩니다. 페이지네이션 뷰를 커스터마이징 하는 가장 쉬운 방법은 `vendor:publish` 명령어를 사용하여 `resources/views/vendor` 디렉토리를 구성하는 것입니다:

    php artisan vendor:publish --tag=laravel-pagination

This command will place the views in the `resources/views/vendor/pagination` directory. The `default.blade.php` file within this directory corresponds to the default pagination view. Simply edit this file to modify the pagination HTML.

이 명령어는 `resources/views/vendor/pagination` 디렉토리 안에 뷰 파일들을 구성합니다. 이 디렉토리 안에 `default.blade.php` 파일은 기본적인 페이지네이션 뷰와 일치합니다. 간단하게 이 파일을 변경하여 페이지네이션 HTMLd을 수정할 수 있습니다.

Be sure to review the full [pagination documentation](/docs/{{version}}/pagination) for more information.

보다 자세한 사항은 [페이지네이션 문서](/docs/{{version}}/pagination)를 확인하십시오.

### Queue
### Queue-큐

#### Configuration
#### 설정하기

In your queue configuration, all `expire` configuration items should be renamed to `retry_after`. Likewise, the Beanstalk configuration's `ttr` item should be renamed to `retry_after`. This name change provides more clarity on the purpose of this configuration option.

큐 설정에서 모든 `expire` 설정 부분은 `retry_after` 로 이름이 변경되었습니다. 마찬가지로, Beanstalk 설정의 `ttr` 부분도 `retry_after` 으로 이름이 변경되었습니다. 이렇게 이름이 변경되어 설정 옵션의 목적을 보다 분명히 할 수 있습니다.

#### Closures
#### 클로저

Queueing Closures is no longer supported. If you are queueing a Closure in your application, you should convert the Closure to a class and queue an instance of the class:

클로저를 큐를 통해서 실행하는 것은 더이상 지원되지 않습니다. 애플리케이션에서 클로저를 큐를 통해서 실행했었다면, 클로저를 클래스로 변경하고 대신에 클래스 인스턴스를 큐에 전달해야합니다:

    dispatch(new ProcessPodcast($podcast));

#### Collection Serialization
#### 컬렉션 Serialization

The `Illuminate\Queue\SerializesModels` trait now properly serializes instances of `Illuminate\Database\Eloquent\Collection`. This will most likely not be a breaking change for the vast majority of applications; however, if your application is absolutely dependent on collections not being re-retrieved from the database by queued jobs, you should verify that this change does not negatively affect your application.

`Illuminate\Queue\SerializesModels` 트레이트-trait은 이제 `Illuminate\Database\Eloquent\Collection` 인스턴스를 serialize 할 수 있게 되었습니다. 이 변경사항은 애플리케이션의 대부분에서 호환성을 깨뜨리는 변화는 가져오지 않습니다; 그렇지만, 애플리케이션의 대기열에 있는 작업이 데이터베이스에서 다시 획득할 수 없는 컬렉션에 의존하는 경우에 이 변경사항이 애플리케이션에 영향을 끼치지 않는 것을 확인하도록 하십시오.

#### Daemon Workers
#### 데몬 워커

It is no longer necessary to specify the `--daemon` option when calling the `queue:work` Artisan command. Running the `php artisan queue:work` command will automatically assume that you want to run the worker in daemon mode. If you would like to process a single job, you may use the `--once` option on the command:

`queue:work` 아티즌 명령어를 호출할 때 `--daemon` 옵션은 더이상 필요하지 않습니다. `php artisan queue:work` 명령어는 자동으로 여러분이 워커를 데몬 모드로 수행하길 원한다고 가정합니다. 하나의 작업을 처리하려는 경우에는, 여러분은 명령어에 `--once` 옵션을 사용할 수 있습니다:

    // Start a daemon queue worker...
    php artisan queue:work

    // Process a single job...
    php artisan queue:work --once

#### Database Driver Changes
#### 데이터베이스 드라이버 변경사항

If you are using the `database` driver to store your queued jobs, you should drop the `jobs_queue_reserved_reserved_at_index` index then drop the `reserved` column from your `jobs` table. This column is no longer required when using the `database` driver. Once you have completed these changes, you should add a new compound index on the `queue` and `reserved_at` columns.

큐-queue 작업을 저장하기 위해서 `database` 드라이버를 사용하고 있다면, `jobs` 테이블의 `jobs_queue_reserved_reserved_at_index`를 drop 하고 `reserved` 컬럼을 drop 해야 합니다. 이 컬럼은 `database` 드라이버를 사용할 때 더이상 필요하지 않습니다. 이 변경사항들을 적용하고 나서, `queue` 와 `reserved_at` 컬럼에 대한 복합 인덱스를 추가해야합니다.

Below is an example migration you may use to perform the necessary changes:

다음은 피요한 변경사항을 수행할 수 있는 마이그레이션 파일의 예제입니다:

    public function up()
    {
        Schema::table('jobs', function (Blueprint $table) {
            $table->dropIndex('jobs_queue_reserved_reserved_at_index');
            $table->dropColumn('reserved');
            $table->index(['queue', 'reserved_at']);
        });

        Schema::table('failed_jobs', function (Blueprint $table) {
            $table->longText('exception')->after('payload');
        });
    }

    public function down()
    {
        Schema::table('jobs', function (Blueprint $table) {
            $table->tinyInteger('reserved')->unsigned();
            $table->index(['queue', 'reserved', 'reserved_at']);
            $table->dropIndex('jobs_queue_reserved_at_index');
        });

        Schema::table('failed_jobs', function (Blueprint $table) {
            $table->dropColumn('exception');
        });
    }

#### Event Data Changes
#### 이벤트 데이터 변경사항

Various queue job events such as `JobProcessing` and `JobProcessed` no longer contain the `$data` property. You should update your application to call `$event->job->payload()` to get the equivalent data.

`JobProcessing`, 그리고 `JobProcessed`와 같은 다양한 큐-queue 작업 이벤트들은 어디상 `$data` 속성을 가지고 있지 않습니다. 데이터를 가져오기 위해서는 애플리케이션이 `$event->job->payload()` 를 호출하도록 변경해야합니다.

#### Failed Job Events
#### 실패한 Job 에 대한 이벤트

If you are calling the `Queue::failing` method in your `AppServiceProvider`, you should update the method signature to the following:

`AppServiceProvider` 에서 `Queue:failing` 메소드를 호출한다면, 다음처럼 메소드를 수정해야 합니다:

    use Illuminate\Queue\Events\JobFailed;

    Queue::failing(function (JobFailed $event) {
        // $event->connectionName
        // $event->job
        // $event->exception
    });

#### Process Control Extension
#### 프로세스 컨트롤 확장-extension

If your application makes use of the `--timeout` option for queue workers, you'll need to verify that the [pcntl extension](https://secure.php.net/manual/en/pcntl.installation.php) is installed.

애플리케이션에서 큐 워커에 `--timeout` 옵션을 사용하게 되면, [pcntl 확장기능](https://secure.php.net/manual/en/pcntl.installation.php)이 설치되어 있는지 확인해야 합니다.

#### Serializing Models On Legacy Style Queue Jobs
#### 레거시 스타일의 큐-queue 작업에서 모델 serializing

Typically, jobs in Laravel are queued by passing a new job instance to the `Queue::push` method. However, some applications may be queuing jobs using the following legacy syntax:

일반적으로, Laravel의 작업은 새 작업 인스턴스를 전달하여 대기중인 Queue::push 방법. 그러나 일부 응용 프로그램은 다음 레거시 구문을 사용하여 작업을 대기시킬 수 있습니다:

    Queue::push('ClassName@method');

If you are queueing jobs using this syntax, Eloquent models will no longer be automatically serialized and re-retrieved by the queue. If you would like your Eloquent models to be automatically serialized by the queue, you should use the `Illuminate\Queue\SerializesModels` trait on your job class and queue the job using the new `push` syntax:

이 구문을 사용하여 작업을 대기중인 경우 Eloquent 모델은 더 이상 자동으로 직렬화되어 대기열에서 다시 검색되지 않습니다. 자동 큐으로 직렬화하기 위해 웅변 모델을하려는 경우, 당신은 사용해야  Illuminate\Queue\SerializesModels 작업 클래스에 특성을 새로운 사용하여 작업을 큐에 push 구문 :

    Queue::push(new ClassName);

### Routing
### 라우팅

#### Resource Parameters Are Singular By Default
#### 리소스 파라미터는 단수를 기본으로 합니다.

In previous versions of Laravel, route parameters registered using `Route::resource` were not "singularized". This could lead to some unexpected behavior when registering route model bindings. For example, given the following `Route::resource` call:

라라벨의 이전버전에서는 `Route::resource` 를 사용해서 등록된 라우트 파라미터는 "단수"가 아니였습니다. 이는 라우트 모델 바인딩을 등록할 때 예시치 않은 동작을 발생시키기도 했습니다. 예를 들어, 다음의 `Route::resource` 를 호출하면:

    Route::resource('photos', 'PhotoController');

The URI for the `show` route would be defined as follows:

`show` 라우트에 대한 URI는 다음과 같이 정의됩니다:

    /photos/{photos}

In Laravel 5.3, all resource route parameters are singularized by default. So, the same call to `Route::resource` would register the following URI:

라라벨 5.3에서 모든 리소스 라우트 파라미터는 단수를 기본으로 합니다. 따라서 `Route::resource`와 동일한 호출은 다음과 같은 URI를 등록합니다:

    /photos/{photo}

If you would like to maintain the previous behavior instead of automatically singularizing resource route parameters, you may make the following call to the `singularResourceParameters` method in your `AppServiceProvider`:

리소스 라우트 파리미터가 자동으로 단수로 처리되는 것 대신에, 이전처럼 동작하도록 유지하고자 한다면, 다음과 같이 `AppServiceProvider` 의 `singularResourceParameters` 메소드를 호출하면 됩니다:

    use Illuminate\Support\Facades\Route;

    Route::singularResourceParameters(false);

#### Resource Route Names No Longer Affected By Prefixes
#### 리소스 라우트 이름은 더이상 prefix 에 영향을 받지 않습니다.

URL prefixes no longer affect the route names assigned to routes when using `Route::resource`, since this behavior defeated the entire purpose of using route names in the first place.

URL prefix는 더이상 `Route::resource` 를 사용해서 할당된 라우트 이름에 영향을 주지 않습니다. 이 동작은 애초에 라우트 이름을 사용하는 목적을 완전히 망쳐버렸습니다.

If your application is using `Route::resource` within a `Route::group` call that specified a `prefix` option, you should examine all of your `route` helper and `UrlGenerator::route` calls to verify that you are no longer appending this URI prefix to the route name.

만약 `prefix` 옵션이 지저된 `Route::group` 호출 안에서 `Route::resource`를 사용한다면, 라우트 이름앞에 prefix URL이 붙어 있지 않는지 모든 `route` 헬퍼와 `UrlGenerator::route` 호출을 살펴보아야 합니다.

If this change causes you to have two routes with the same name, you have two options. First, you may use the `names` option when calling `Route::resource` to specify a custom name for a given route. Refer to the [resource routing documentation](/docs/5.3/controllers#resource-controllers) for more information. Alternatively, you may add the `as` option on your route group:

이 변경사항은 동일한 이름을 가지는 두개의 라우트를 갖게하는 문제를 발생시키는데, 여기에는 두가지 대안이 있습니다. 먼저 `Route::resource` 를 호출할 때 주어진 라우트에 대한 사용자 정의 이름을 지정하는 `names` 옵션을 사용할 수 있습니다. 자세한 사항은 [리소스 라우팅 문서](/docs/5.3/controllers#resource-controllers)를 참고하십시오. 다른 방법은 라우트 그룹에 `as` 옵션을 추가하는 것입니다:

    Route::group(['as' => 'admin.', 'prefix' => 'admin'], function () {
        //
    });

### Validation
### 유효성 검사

#### Form Request Exceptions
#### Form Request-요청 Exception-예외

If a form request's validation fails, Laravel will now throw an instance of `Illuminate\Validation\ValidationException` instead of an instance of `HttpException`. If you are manually catching the `HttpException` instance thrown by a form request, you should update your `catch` blocks to catch the `ValidationException` instead.

form request 에서 유효성 검사가 실패하면, 라라벨은 `HttpException` 인스턴스 대신에 `Illuminate\Validation\ValidationException` 인스턴스를 throw 합니다. 만약 form request 에서 직접 `HttpException`을 catch 하고 있었다면, `cache` 부분에서 `ValidationException` 를 받을 수 있도록 수정해야합니다.

#### The Message Bag
#### 메세지 백

If you were previously using the `has` method to determine if an `Illuminate\Support\MessageBag` instance contained any messages, you should use the `count` method instead. The `has` method now requires a parameter and only determines if a specific key exists in the message bag.

이전 버전에서 `Illuminate\Support\MessageBag` 인스턴스가 메세지를 가지고 있는지에 대해서 확인하기 위해서 `has` 메소드를 사용했다면, 이제 그 대신에 `cout` 메소드를 사용해야 합니다. `has` 메소드는 이제 메세지 백안에 있는 지정된를 확인하기 위해서 인자를 필요로 합니다.

#### Nullable Primitives
#### Nullable 기본타입

When validating arrays, booleans, integers, numerics, and strings, `null` will no longer be considered a valid value unless the rule set contains the new `nullable` rule:

배열, 블리언, 정수값, 숫자 그리고 문자열에 대한 유효성 검사를 할 때, `nullable` 규칙을 추가하지 않는 이상, `null` 은 더이상 유효한 값이라고 판단되지 않습니다:

    Validator::make($request->all(), [
        'field' => 'nullable|max:5',
    ]);

<a name="upgrade-5.2.0"></a>
## Upgrading To 5.2.0 From 5.1
## 5.1에서 5.2.0 으로 업그레이드 하기

#### Estimated Upgrade Time: Less Than 1 Hour
#### 업그레이드 예상 시간 : 1시간 이하

> {note} We attempt to provide a very comprehensive listing of every possible breaking change made to the framework. However, many of these changes may not apply to your own application.

> {note} 프레임워크에 에러가 발생할만한 모든 포괄적인 목록을 제공하려고 합니다. 그렇지만 이러한 변경사항들의 대부분은 여러분의 애플리케이션에 적용되지 않을 수도 있습니다.

### Updating Dependencies
### 패키지 의존성 업데이트

Update your `composer.json` file to point to `laravel/framework 5.2.*`.

`composer.json` 파일에서 `laravel/framework 5.2.*`를 변경하십시오.

Add `"symfony/dom-crawler": "~3.0"` and `"symfony/css-selector": "~3.0"` to the `require-dev` section of your `composer.json` file.

`composer.json` 파일의 `require-dev` 섹션에 `"symfony/dom-crawler": "~3.0"` 와 `"symfony/css-selector": "~3.0"`를 추가하십시오.

### Authentication
### 인증

#### Configuration File
#### 설정 파일

You should update your `config/auth.php` configuration file with the following: [https://github.com/laravel/laravel/blob/5.2/config/auth.php](https://github.com/laravel/laravel/blob/5.2/config/auth.php)

`config/auth.php` 설정 파일을 다음의 [https://github.com/laravel/laravel/blob/5.2/config/auth.php](https://github.com/laravel/laravel/blob/5.2/config/auth.php)와 같이 변경해야 합니다.

Once you have updated the file with a fresh copy, set your authentication configuration options to their desired value based on your old configuration file. If you were using the typical, Eloquent based authentication services available in Laravel 5.1, most values should remain the same.

새로운 복사본으로 파일을 덮어씌우고, 이전 설정 파일의 옵션값을 참고하여 인증을 설정하십시오. 라라벨 5.1에서 일반적인 Eloquent 기반의 인증 서비스를 이용 중이었다면, 대부분의 값들은 동일할 것입니다.

Take special note of the `passwords.users.email` configuration option in the new `auth.php` configuration file and verify that the view path matches the actual view path for your application, as the default path to this view was changed in Laravel 5.2. If the default value in the new configuration file does not match your existing view, update the configuration option.

새로운 `auth.php` 설정 파일안에 있는 `passwords.users.email` 설정 옵션의 특별한 사항를 확인하고, 뷰 파일의 경로가 애플리케이션의 실제의 뷰 파일 경로와 일치하는지 확인하십시오. 라라벨 5.2에서 이 뷰에 대한 기본 경로가 변경되었습니다. 새로운 설정 파일의 기본 값이 가지고 있는 뷰의 경로와 일치하지 않는다면 설정 옵션을 변경하십시오.

#### Contracts
#### Contracts

If you are implementing the `Illuminate\Contracts\Auth\Authenticatable` contract but are **not** using the `Authenticatable` trait, you should add a new `getAuthIdentifierName` method to your contract implementation. Typically, this method will return the column name of the "primary key" of your authenticatable entity. For example: `id`.

`Illuminate\Contracts\Auth\Authenticatable` contract를 구현하고 있었다면, 더 이상 `Authenticatable` trait-트레이트를 사용하지 **않기 때문에*, 여러분은 contract 구현 클래스에 `getAuthIdentifierName` 메소드를 추가해야만 합니다. 일반적으로 이 메소드는 인증 엔티티의 "primary key" 컬럼 이름을 반환할 것입니다. 예를 들면 `id`와 같습니다.

This is unlikely to affect your application unless you were manually implementing this interface.

이 인터페이스를 구현하지 않았었다면, 애플리케이션에 영향을 미치지 않을 수 있습니다.

#### Custom Drivers
#### 사용자 정의 드라이버

If you are using the `Auth::extend` method to define a custom method of retrieving users, you should now use `Auth::provider` to define your custom user provider. Once you have defined the custom provider, you may configure it in the `providers` array of your new `auth.php` configuration file.

사용자를 식별하기 위한 사용자 정의 메소드를 정의하는데 `Auth::extend` 메소드를 사용하고 있었다면, 이제 사용자 정의 User 프로바이더를 정의하기 위해서 `Auth::provider` 를 사용해야 합니다. 사용자 정의 프로바이더를 정의하고 나서, 새로운 `auth.php` 설정 파일의 `providers` 배열 안에 이를 설정해야 합니다.

For more information on custom authentication providers, consult the [full authentication documentation](/docs/{{version}}/authentication).

사용자 정의 인증 프로바이더에 대한 보다 자세한 내용은 [인증 문서](/docs/{{version}}/authentication)를 참고하십시오.

#### Redirection
#### 리다이렉트

The `loginPath()` method has been removed from `Illuminate\Foundation\Auth\AuthenticatesUsers`, so placing a `$loginPath` variable in your `AuthController` is no longer required. By default, the trait will always redirect users back to their previous location on authentication errors.

`Illuminate\Foundation\Auth\AuthenticatesUsers`의 `loginPath()` 메소드가 제거되었기 때문에, `AuthController` 에서 `$loginPath` 변수는 더이상 필요하지 않습니다. 기본적으로, 트레이트-trait 은 인증에서 에러가 발생하면 자동으로 사용자를 이전 위치로 리다이렉트 시킬 것입니다.

### Authorization
### 승인-Authorization

The `Illuminate\Auth\Access\UnauthorizedException` has been renamed to `Illuminate\Auth\Access\AuthorizationException`. This is unlikely to affect your application if you are not manually catching this exception.

`Illuminate\Auth\Access\UnauthorizedException`이 `Illuminate\Auth\Access\AuthorizationException`으로 이름이 변경되었습니다. 이 exception 을 수동으로 처리하도록 하지 않았었다면, 애플리케이션에 영향을 미치지 않을 수 있습니다.

### Collections
### 컬렉션

#### Eloquent Base Collections
#### Eloquent 베이스 컬렉션

The Eloquent collection instance now returns a base Collection (`Illuminate\Support\Collection`) for the following methods: `pluck`, `keys`, `zip`, `collapse`, `flatten`, `flip`.

Eloquent 컬렉션 인스턴스는 이제, 다음 메소드: `pluck`, `keys`, `zip`, `collapse`, `flatten`, `flip`를 위한 베이스 컬렉션 (`Illuminate\Support\Collection`)을 반환합니다.

#### Key Preservation
#### 키값의 유지

The `slice`, `chunk`, and `reverse` methods now preserve keys on the collection. If you do not want these methods to preserve keys, use the `values` method on the `Collection` instance.

`slice`, `chunk`, and `reverse` 메소드들은 이제 컬렉션의 키 값을 유지합니다. 이 메소드들이 키를 유지하는 것을 원하지 않는다면, `Collection` 인스턴스의 `values` 메소드를 사용하십시오.

### Composer Class
### 컴포저 클래스

The `Illuminate\Foundation\Composer` class has been moved to `Illuminate\Support\Composer`. This is unlikely to affect your application if you were not manually using this class.

`Illuminate\Foundation\Composer` 클래스는 `Illuminate\Support\Composer`으로 이동되었습니다. 수동으로 이 클래스를 사용하지 않았었다면, 애플리케이션에 영향을 미치지 않을 수 있습니다.

### Commands And Handlers
### 명령어와 핸들러

#### Self-Handling Commands
#### Self-Handling 명령어

You no longer need to implement the `SelfHandling` contract on your jobs / commands. All jobs are now self-handling by default, so you can remove this interface from your classes.

jobs / commands 클래스에서 더이상 `SelfHandling` contract를 구현할 필요가 없습니다. 모든 job 클래스는 기본적으로 self-handling 되며, 여러분의 클래스들에서 이 이터페이스를 제거할 수 있습니다.

#### Separate Commands & Handlers
#### 명령과 핸들러의 구분

The Laravel 5.2 command bus now only supports self-handling commands and no longer supports separate commands and handlers.

라라벨 5.2 command bus 는 혼자서 처리되는 명령어만 지원하며 더이상 명령어와 핸들러를 구분하는 것을 지원을 하지 않습니다.

If you would like to continue using separate commands and handlers, you may install a Laravel Collective package which provides backwards-compatible support for this: [https://github.com/LaravelCollective/bus](https://github.com/laravelcollective/bus)

별도의 명령어와 핸들러를 계속 사용하고 싶은 경우에는, 이에 대한 하위 호환성을 지원하는 라라벨 Collective 패키지를 설치 할 수 있습니다. [https://github.com/LaravelCollective/bus](https://github.com/laravelcollective/bus)

### Configuration
### 설정

#### Environment Value
#### 구동환경 변수값

Add an `env` configuration option to your `app.php` configuration file that looks like the following:

`app.php` 설정 파일의 `env` 설정 옵션값이 다음과 같이 추가되었습니다.

    'env' => env('APP_ENV', 'production'),

#### Caching And Env
#### 캐싱과 Env

If you are using the `config:cache` command during deployment, you **must** make sure that you are only calling the `env` function from within your configuration files, and not from anywhere else in your application.

배포 진행중에 `config:cache`명령어를 사용한다면, 여러분은 **반드시** 애플리케이션의 다른 곳이 아닌, 설정 파일 안에서만 `env` 함수를 호출 하는 것을 확인해야 합니다.

If you are calling `env` from within your application, it is strongly recommended you add proper configuration values to your configuration files and call `env` from that location instead, allowing you to convert your `env` calls to `config` calls.

만약 애플리케이션에서 `env` 를 호출한다면, 여러분의 설정 파일에 해당 설정 값을 추가하고, 해당 위치에서는 `env` 를 호출하도록 하여 `env` 가 `config`를 호출하도록 변경할 것을 강력하게 권고합니다.

#### Compiled Classes
#### 컴파일된 클래스들

If present, remove the following lines from `config/compile.php` in the `files` array:

존재하는 경우에는 `config/compile.php` 안에 있는 `files` 배열안에서 다음 라인들을 제거하십시오:

    realpath(__DIR__.'/../app/Providers/BusServiceProvider.php'),
    realpath(__DIR__.'/../app/Providers/ConfigServiceProvider.php'),

Not doing so can trigger an error when running `php artisan optimize` if the service providers listed here do not exist.

이렇게 하지 않으면 `php artisan optimize`가 실행될 때 서비스 프로바이더가 존재하지 않는다고 에러가 발생합니다.

### CSRF Verification
### CSRF 확인

CSRF verification is no longer automatically performed when running unit tests. This is unlikely to affect your application.

단위 테스트가 실행중에 더이상 CSRF 확인이 자동으로 수행되지 않습니다. 이 변경사항은 애플리케이션에는 영향을 미치지 않을 수 있습니다.

### Database
### 데이터베이스

#### MySQL Dates
#### MySQL 날짜지원

Starting with MySQL 5.7, `0000-00-00 00:00:00` is no longer considered a valid date, since `strict` mode is enabled by default. All timestamp columns should receive a valid default value when you insert records into your database. You may use the `useCurrent` method in your migrations to default the timestamp columns to the current timestamps, or you may make the timestamps `nullable` to allow `null` values:

MySQL 5.7 에서 부터는 기본적으로 `stric` 모드가 사용되기 때문에, `0000-00-00 00:00:00` 가 더이상 유효한 날짜로 인식되지 않습니다. 데이터베이스에 레코드를 추가할 때 모든 타임스탬프 컬럼은 유효한 기본값을 받아야 합니다. 여러분은 마이그레이션 파일에서 `useCurrent` 메소드를 사용하여 타임스탬프 컬럼에 현재의 타임스탬프 값을 기본으로 지정하거나, `null` 값을 허용하도록 `nullable` 로 만들 수 있습니다:

    $table->timestamp('foo')->nullable();

    $table->timestamp('foo')->useCurrent();

    $table->nullableTimestamps();

#### MySQL JSON Column Type
#### MySQL JSON 컬럼 타입

The `json` column type now creates actual JSON columns when used by the MySQL driver. If you are not running MySQL 5.7 or above, this column type will not be available to you. Instead, use the `text` column type in your migration.

이제 MySQL 드라이버를 사용할 때, `json` 컬럼 타입은 실제 JSON 컬럼을 생성합니다. MySQL 5.7 이상을 구동하지 않는다면, 이 컬럼 타입은 사용이 불가능합니다. 대신에 마이그레이션 파일에서 `text` 컬럼 타입을 사용하십시오.

#### Seeding
#### Seeding-시딩

When running database seeds, all Eloquent models are now unguarded by default. Previously a call to `Model::unguard()` was required. You can call `Model::reguard()` at the top of your `DatabaseSeeder` class if you would like models to be guarded during seeding.

이제 데이터베이스 시딩이 실행될 때, 모든 Eloquent 모델은 기본적으로 모든 속성이 바로 접근할 수 있습니다. 이전까지는 `Model::unguard()`를 호출해야만 했습니다. 시딩이 진행되는 동안 속성들을 보호하고자(guard) 한다면, `DatabaseSeeder` 클래스의 상단 부분에서 `Model::reguard()`를 호출할 수 있습니다.

### Eloquent
### Eloquent

#### Date Casts
#### 날짜 캐스팅

Any attributes that have been added to your `$casts` property as `date` or `datetime` will now be converted to a string when `toArray` is called on the model or collection of models. This makes the date casting conversion consistent with dates specified in your `$dates` array.

모델의 `$casts` 속성에 추가된 `date` 나 `datetime` 과 같은 속성들은 모델이나 모델의 컬렉션에서 `toArray`가 호출될 때 문자로 변환될 것입니다. 이를 통해서 `$dates` 배열에 지정된 날짜와 일치하는 날짜 캐스팅 변환이 됩니다.

#### Global Scopes
#### 글로벌 스코프

The global scopes implementation has been re-written to be much easier to use. Your global scopes no longer need a `remove` method, so it may be removed from any global scopes you have written.

글로벌 스코프 구현이 보다 손쉽게 사용될 수 있도록 재작성되었습니다. 글로벌 스코프는 더이상 `remove` 메소드가 필요하지 않으며, 여러분이 작성한 글로벌 스코프에서 제거할 수 있습니다.

If you were calling `getQuery` on an Eloquent query builder to access the underlying query builder instance, you should now call `toBase`.

Eloquent 쿼리 빌더에서 쿼리 빌더 인스턴스에 엑세스 하고자 `getQueyt`를 호출하였었다면, 이제 `toBase` 메소드를 호출해야 합니다.

If you were calling the `remove` method directly for any reason, you should change this call to `$eloquentBuilder->withoutGlobalScope($scope)`.

어떤 이유던지 `remove` 메소드를 직접 호출했었다면, 이를 `$eloquentBuilder->withoutGlobalScope($scope)`을 호출하도록 변경해야 합니다.

New methods `withoutGlobalScope` and `withoutGlobalScopes` have been added to the Eloquent query builder. Any calls to `$model->removeGlobalScopes($builder)` may be changed to simply `$builder->withoutGlobalScopes()`.

Eloquent 쿼리 빌더에 새로운 `withoutGlobalScope` 와 `withoutGlobalScopes` 메소드가 추가되었습니다. `$model->removeGlobalScopes($builder)`의 호출은 간단하게 `$builder->withoutGlobalScopes()`으로 변경할 수 있습니다.

#### Primary keys
#### Primary kyes

By default, Eloquent assumes your primary keys are integers and will automatically cast them to integers. For any primary key that is not an integer you should override the `$incrementing` property on your Eloquent model to `false`:

기본적으로 Eloquent는 여러분의 primary key 가 정수형이라고 추정하여 자동으로 정수 타입으로 캐스팅 할 것입니다. 정수형이 아닌 primary key를 사용한다면, Eloquent 모델의 `$incrementing` 속성을 `false` 로 오버라이딩 해야합니다:

    /**
     * Indicates if the IDs are auto-incrementing.
     *
     * @var bool
     */
    public $incrementing = true;

### Events
### 이벤트

#### Core Event Objects
#### 코어 이벤트 객체들

Some of the core events fired by Laravel now use event objects instead of string event names and dynamic parameters. Below is a list of the old event names and their new object based counterparts:

이제 라라벨에 의해서 발생하는 몇몇 코어 이벤트들은 이벤트 이름과 동적 파라미터 문자열 대신에 이벤트 객체를 사용합니다. 다음은 이전의 이벤트 이름과 새로운 객체의 대응 목록입니다 :

Old  | New
이전  | 새로운
------------- | -------------
`artisan.start`  |  `Illuminate\Console\Events\ArtisanStarting`
`auth.attempting`  |  `Illuminate\Auth\Events\Attempting`
`auth.login`  |  `Illuminate\Auth\Events\Login`
`auth.logout`  |  `Illuminate\Auth\Events\Logout`
`cache.missed`  |  `Illuminate\Cache\Events\CacheMissed`
`cache.hit`  |  `Illuminate\Cache\Events\CacheHit`
`cache.write`  |  `Illuminate\Cache\Events\KeyWritten`
`cache.delete`  |  `Illuminate\Cache\Events\KeyForgotten`
`connection.{name}.beginTransaction`  |  `Illuminate\Database\Events\TransactionBeginning`
`connection.{name}.committed`  |  `Illuminate\Database\Events\TransactionCommitted`
`connection.{name}.rollingBack`  |  `Illuminate\Database\Events\TransactionRolledBack`
`illuminate.query`  |  `Illuminate\Database\Events\QueryExecuted`
`illuminate.queue.before`  |  `Illuminate\Queue\Events\JobProcessing`
`illuminate.queue.after`  |  `Illuminate\Queue\Events\JobProcessed`
`illuminate.queue.failed`  |  `Illuminate\Queue\Events\JobFailed`
`illuminate.queue.stopping`  |  `Illuminate\Queue\Events\WorkerStopping`
`mailer.sending`  |  `Illuminate\Mail\Events\MessageSending`
`router.matched`  |  `Illuminate\Routing\Events\RouteMatched`

Each of these event objects contains **exactly** the same parameters that were passed to the event handler in Laravel 5.1. For example, if you were using `DB::listen` in 5.1.*, you may update your code like so for 5.2.*:

각각의 이 이벤트 객체들은 라라벨 5.1의 이벤트 핸들러에 전달 되었던 파라미터들과 **완전히** 동일한 파라미터를 가지고 있습니다. 예를 들어 5.1.* 에서 `DB::listen` 을 사용중이었다면, 5.2.* 에서는 다음과 같이 변경할 수 있습니다:

    DB::listen(function ($event) {
        dump($event->sql);
        dump($event->bindings);
    });

You may check out each of the new event object classes to see their public properties.

각각의 새로운 이벤트 객체 클래스를 살펴보고, 각각의 프로퍼티들을 확인하십시오.

### Exception Handling
### Exception 핸들링

Your `App\Exceptions\Handler` class' `$dontReport` property should be updated to include at least the following exception types:

`App\Exceptions\Handler` 클래스의 `$dontReport` 속성은 다음과 같은 exception 타입들을 포함하도록 수정되어야 합니다:

    use Illuminate\Validation\ValidationException;
    use Illuminate\Auth\Access\AuthorizationException;
    use Illuminate\Database\Eloquent\ModelNotFoundException;
    use Symfony\Component\HttpKernel\Exception\HttpException;

    /**
     * A list of the exception types that should not be reported.
     *
     * @var array
     */
    protected $dontReport = [
        AuthorizationException::class,
        HttpException::class,
        ModelNotFoundException::class,
        ValidationException::class,
    ];

### Helper Functions
### 헬퍼 함수

The `url()` helper function now returns a `Illuminate\Routing\UrlGenerator` instance when no path is provided.

이제 `url()` 헬퍼 함수는 경로를 지정하지 않으면 `Illuminate\Routing\UrlGenerator` 인스턴스를 반환합니다.

### Implicit Model Binding
### 묵시적 모델 바인딩

Laravel 5.2 includes "implicit model binding", a convenient new feature to automatically inject model instances into routes and controllers based on the identifier present in the URI. However, this does change the behavior of routes and controllers that type-hint model instances.

라라벨 5.2에서는 자동으로 URI의 식별자를 기반으로 라우트와 컨트롤러에 모델 인스턴스를 주입해주는 편리한 기능을 제공하는 "묵시적 모델 바인딩"이 도입되었습니다. 하지만, 이러한 변경사항은 라우트나 컨트롤러에서 사용된 타입힌트 모델 인스턴스의 동작을 변경하게 됩니다.

If you were type-hinting a model instance in your route or controller and were expecting an **empty** model instance to be injected, you should remove this type-hint and create an empty model instance directly within your route or controller; otherwise, Laravel will attempt to retrieve an existing model instance from the database based on the identifier present in the route's URI.

타입-힌트되는 모델 인스턴스가 여러분의 라우트 또는 컨트롤러에 있고, **비어있는**모델 인스턴스가 주입되도록 구성하였었다면, 라우트나 컨트롤러에서 이 타입-힌트를 제거하고, 빈 모델 인스턴스를 직접 생성해야 합니다. 다시말해 라라벨은 URI의 식별자를 기반으로 데이터베이스에 존재하는 모델 인스턴스를 찾으려고 시도할 것입니다.

### IronMQ
### IronMQ

The IronMQ queue driver has been moved into its own package and is no longer shipped with the core framework.

IronMQ 큐 드라이버가 별도의 고유 패키지로 이동되었으며, 코어 프레임워크에서 더이상 제공되지 않습니다.

[https://github.com/LaravelCollective/iron-queue](https://github.com/laravelcollective/iron-queue)

### Jobs / Queue
### Jobs / Queue

The `php artisan make:job` command now creates a "queued" job class definition by default. If you would like to create a "sync" job, use the `--sync` option when issuing the command.

이제 `php artisan make:job` 명령어는 기본적으로 "queued-큐를 통해서 처리되는" job 클래스를 생성합니다. "sync-동기적으로 처리되는" job을 생성하고자 한다면, 명령어를 실행할 때 `--sync` 옵션을 사용하십시오.

### Mail
### 메일

The `pretend` mail configuration option has been removed. Instead, use the `log` mail driver, which performs the same function as `pretend` and logs even more information about the mail message.

`pretend` 메일 설정 옵션은 제거되었습니다. 대신에, `pretent` 함수와 동일한 역할을 수행하며 메일 메시지에 대한 보다 많은 정보를 기록하는, `log` 메일 드라이버를 사용하십시오.

### Pagination
### Pagination

To be consistent with other URLs generated by the framework, the paginator URLs no longer contain a trailing slash. This is unlikely to affect your application.

프레임워크에서 생성되는 다른 URL들과 동일한 URL을 생성하기 위해서 paginator URL은 더이상 마지막에 슬래쉬(/)를 포함하지 않습니다. 이 수정사항은 애플리케이션에 영향을 미치지 않을 수 있습니다.

### Service Providers
### 서비스 프로바이더

The `Illuminate\Foundation\Providers\ArtisanServiceProvider` should be removed from your service provider list in your `app.php` configuration file.

`app.php` 설정 파일의 서비스 프로바이더 부분에서 `Illuminate\Foundation\Providers\ArtisanServiceProvider`가 제거되어 졌어야합니다.

The `Illuminate\Routing\ControllerServiceProvider` should be removed from your service provider list in your `app.php` configuration file.

`app.php` 설정 파일의 서비스 프로바이더 부분에서 `Illuminate\Routing\ControllerServiceProvider`가 제거되어 졌어야 합니다.

### Sessions
### 세션

Because of changes to the authentication system, any existing sessions will be invalidated when you upgrade to Laravel 5.2.

인증 시스템의 변경으로 인해서, 라라벨 5.2로 업그레이드 하면 기존에 존재하던 세션들은 모두 유효하지 않게 됩니다.

#### Database Session Driver
#### 데이터베이스 세션 드라이버

A new `database` session driver has been written for the framework which includes more information about the user such as their user ID, IP address, and user-agent. If you would like to continue using the old driver you may specify the `legacy-database` driver in your `session.php` configuration file.

사용자의 ID, IP, 그리고 user-agent와 같은 보다 자세한 사용자 정보를 포함하는 새로운 `database` 세션 드라이버가 프레임워크에 새롭게 작성되었습니다. 기존의 드라이버를 계속 사용하고자 한다면, `session.php` 설정 파일에서 `legacy-database` 드라이버를 사용하십시오.

If you would like to use the new driver, you should add the `user_id (nullable integer)`, `ip_address (nullable string)`, and `user_agent (text)` columns to your session database table.

새로운 드라이버를 사용하고자 한다면, 세션 데이터베이스 테이블에 `user_id (nullable integer)`, `ip_address (nullable string)`, 그리고 `user_agent (text)` 컬럼을 추가해야만 합니다.

### Stringy
### Stringy

The "Stringy" library is no longer included with the framework. You may install it manually via Composer if you wish to use it in your application.

프레임워크에 "Stringy" 라이브러리가 더이상 포함되지 않습니다. 애플리케이션에서 이를 사용하려면 Composer를 통해서 수동으로 설치하면 됩니다.

### Validation
### 유효성 검사

#### Exception Types
#### Exception 타입들

The `ValidatesRequests` trait now throws an instance of `Illuminate\Foundation\Validation\ValidationException` instead of throwing an instance of `Illuminate\Http\Exception\HttpResponseException`. This is unlikely to affect your application unless you were manually catching this exception.

`ValidatesRequests` 트레이트-trait은 이제 `Illuminate\Http\Exception\HttpResponseException` 인스턴스 대신 `Illuminate\Foundation\Validation\ValidationException` 인스턴스를 예외로 던집니다. 이 exception을 수동으로 처리하지 않았었다면 애플리케이션에 영향을 끼치지 않을 수 있습니다.

<a name="5.2-deprecations"></a>
### Deprecations
### 비추천 사항들(deprecated)

The following features are deprecated in 5.2 and will be removed in the 5.3 release in June 2016:

다음의 기능들은 5.2에서 사용을 권장하지 않는 기능들이 되었으며, 2016년 6월에 릴리즈되는 5.3에서는 제거될것입니다.

- `Illuminate\Contracts\Bus\SelfHandling` contract. Can be removed from jobs.
- The `lists` method on the Collection, query builder and Eloquent query builder objects has been renamed to `pluck`. The method signature remains the same.
- Implicit controller routes using `Route::controller` have been deprecated. Please use explicit route registration in your routes file. This will likely be extracted into a package.
- The `get`, `post`, and other route helper functions have been removed. You may use the `Route` facade instead.
- The `database` session driver from 5.1 has been renamed to `legacy-database` and will be removed. Consult notes on the "database session driver" above for more information.
- The `Str::randomBytes` function has been deprecated in favor of the `random_bytes` native PHP function.
- The `Str::equals` function has been deprecated in favor of the `hash_equals` native PHP function.
- `Illuminate\View\Expression` has been deprecated in favor of `Illuminate\Support\HtmlString`.
- The `WincacheStore` cache driver has been removed.

- `Illuminate\Contracts\Bus\SelfHandling` contract 는 job 에서 삭제될 수 있습니다.
- 컬렉션, 쿼리빌더 그리고 Eloquent 쿼리 빌더 객체에서 `lists` 메소드는 `pluck`으로 이름이 변경되었습니다. 메소드의 사용법은 동일합니다.
- `Route::controller`를 사용한 묵시적 컨트롤러 라우트는 사용을 권장하지 않게 되었습니다. 라우트 파일에서 명시적으로 라우트를 등록하십시오. 이 기능은 아마도 별도 패키지로 분리될 것입니다.
- `get`, `post` 그리고 다른 라우트 핼퍼 함수들은 제거되었습니다. 대신에 `Route` 파사드를 사용할 수 있습니다.
- 5.1의 `database` 세션 드라이버는 `legacy-database`으로 이름이 변경되었으며, 나중에는 제거될것입니다. 보다 자세한 내용은 "데이터베이스 세션 드라이버" 부분을 참고하십시오.
- `random_bytes` 네이티브 PHP 함수가 사용되도록 `Str::randomBytes` 함수는 더이상 사용을 권장하지 않습니다.
- `hash_equals` 네이티브 PHP 함수가 사용되도록 `Str::equals` 함수는 더이상 사용을 권장하지 않습니다.
- `Illuminate\Support\HtmlString` 가 사용되도록 `Illuminate\View\Expression`는 더이상 사용을 권장하지 않습니다.
- `WincacheStore` 캐시 드라이버는 제거되었습니다.

<a name="upgrade-5.1.11"></a>
## Upgrading To 5.1.11
## 5.1.11 로 업그레이드 하기

Laravel 5.1.11 includes support for [authorization](/docs/{{version}}/authorization) and [policies](/docs/{{version}}/authorization#policies). Incorporating these new features into your existing Laravel 5.1 applications is simple.

라라벨 5.1.11 에서는 [authorization-승인](/docs/{{version}}/authorization) 과 [policies](/docs/{{version}}/authorization#policies)를 지원합니다. 새로운 기능들과 기존의 라라벨 5.1 애플리케이션은 손쉽게 통합됩니다.

> {note} These upgrades are **optional**, and ignoring them will not affect your application.

> {note} 이러한 업그레이드는 **선택 사항**이기 때문에, 이 기능을 사용하지 않아도, 애플리케이션의 실행에는 영향을 주지 않습니다.

#### Create The Policies Directory
#### Policy-정책 디렉토리 생성하기

First, create an empty `app/Policies` directory within your application.

먼저 애플리케이션 안에 `app/Policies` 라는 빈 디렉토리를 생성합니다.

#### Create / Register The AuthServiceProvider & Gate Facade
#### AuthServiceProvider 와 Gate 파사드를 생성하고 등록하기

Create a `AuthServiceProvider` within your `app/Providers` directory. You may copy the contents of the default provider [from GitHub](https://raw.githubusercontent.com/laravel/laravel/5.1/app/Providers/AuthServiceProvider.php). Remember to change the provider's namespace if your application is using a custom namespace. After creating the provider, be sure to register it in your `app.php` configuration file's `providers` array.

`app/Providers` 디렉토리 안에 `AuthServiceProvider` 파일을 생성합니다. [GitHub 에서](https://raw.githubusercontent.com/laravel/laravel/5.1/app/Providers/AuthServiceProvider.php) 파일을 복사할 수도 있습니다. 유의할 것은 프로바이더의 네임스페이스를 여러분의 애플리케이션에서 사용하는 네임스페이스로 변경해야 한다는 것입니다. 프로바이더를 생성한 뒤에, `app.php` 설정 파일의 `providers` 배열에 등록해야 합니다.

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

### Migrations
### 마이그레이션

If you have any migrations that rename a column or any migrations that drop columns from a SQLite database, you will need to add the `doctrine/dbal` dependency to your `composer.json` file and run the `composer update` command in your terminal to install the library.

만약 SQLite 데이터베이스에서 컬럼 이름을 변경하거나, 컬럼을 삭제하는 마이그레이션을 가지고 있다면, 터미널에서 `composer.json` 파일에 `doctrine/dbal` 의존성을 추가하고 `composer update` 명령어를 실행할 필요가 있습니다.

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

    $collection->groupBy('type')->map(function ($item)
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

라라벨 5.0의 폴더 구조와의 호환성을 제공하기 때문에, 편리한 시간에 이벤트들과 명령어들을 새로운 위치로 구성하고 애플리케이션을 5.1로 천천히 업그레이드 할 수 있습니다.

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
### 비추천 사항들(deprecated)

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


### Service Providers
### 서비스 프로바이더

The `App\Providers\BusServiceProvider` may be removed from your service provider list in your `app.php` configuration file.

`app.php` 설정 파일의 서비스 프로바이더 목록에서 `App\Providers\BusServiceProvider` 를 제거하십시오.

The `App\Providers\ConfigServiceProvider` may be removed from your service provider list in your `app.php` configuration file.

`app.php` 설정 파일의 서비스 프로바이더 목록에서 `App\Providers\ConfigServiceProvider` 를 제거하십시오.

<a name="upgrade-5.0"></a>
## Upgrading To 5.0 From 4.2
## 4.2 에서 5.0으로 업그레이드

### Fresh Install, Then Migrate
### 새로 설치하고, 마이그레이션하기

The recommended method of upgrading is to create a new Laravel `5.0` install and then to copy your `4.2` site's unique application files into the new application. This would include controllers, routes, Eloquent models, Artisan commands, assets, and other code specific files to your application.

업그레이드에 대한 권장사항은 새롭게 라라벨 `5.0` 버전을 인스톨하고 `4.2` 버전의 사이트의 고유한 애플리케이션 파일들을 새로운 애플리케이션에 복사하라는 것입니다. 여기에는 컨트롤러, 라우트, 엘로퀀트 모델들, 아티즌 명령어들, asset 파일들 그리고 애플리케이션에 지정된 기타 코드들을 포함합니다.

To start, [install a new Laravel 5.0 application](/docs/5.0/installation) into a fresh directory in your local environment.  Do not install any versions newer than 5.0 yet, since we need to complete the migration steps for 5.0 first. We'll discuss each piece of the migration process in further detail below.

업그레이드를 시작하기 위해서 로컬 환경의 새로운 디렉토리에 [라라벨 5.0 애플리케이션을 설치](/docs/5.0/installation)합니다. 아직 5.0 이후 버전은 설치하지 마십시오. 먼저 5.0을 완전히 마이그레이션 해야합니다. 업그레이드를 위한 각 단계의 진행에 대해서 차근차근 알아보겠습니다.

### Composer Dependencies & Packages
### 컴포저 의존성 & 패키지

Don't forget to copy any additional Composer dependencies into your 5.0 application. This includes third-party code such as SDKs.

설치된 5.0 버전의 애플리케이션에 추가적인 컴포저 의존 패키지들을 복사해 넣는 것을 잊지 마십시오. 여기에는 SDK와 같은 서드 파티 코드도 포함됩니다.

Some Laravel-specific packages may not be compatible with Laravel 5 on initial release. Check with your package's maintainer to determine the proper version of the package for Laravel 5. Once you have added any additional Composer dependencies your application needs, run `composer update`.

라라벨5가 릴리즈 된 이후 바로는 라라벨과 연동되는 일부 패키지는 라라벨5와 호환이 되지 않는수도 있습니다. 패키지의 제작자에게 라라벨5 버전에 대한 대응계획을 확인합니다. 컴포저에 애플리케이션을 위한 추가적인 의존 패키지를 추가하였다면 `composer updata` 를 실행 해야 합니다.

### Namespacing
### 네임스페이스

By default, Laravel 4 applications did not utilize namespacing within your application code. So, for example, all Eloquent models and controllers simply lived in the "global" namespace. For a quicker migration, you can simply leave these classes in the global namespace in Laravel 5 as well.

라라벨 4에서는 기본적으로 애플리케이션의 코드에 네임스페이스가 구성되어 있지 않았습니다. 그래서 예를 들면 모든 Eloquent 모델들과 컨트롤러는 "글로벌" 네임스페이스 영역에 해당되었습니다. 빠른 전환을 위해서는 라라벨5에서도 마찬가지로 글로벌 네임스페이스 안에 이 클래스들을 지정되도록 합니다.

### Configuration
### 설정

#### Migrating Environment Variables
#### 마이그레이션 환경 변수

Copy the new `.env.example` file to `.env`, which is the `5.0` equivalent of the old `.env.php` file. Set any appropriate values there, like your `APP_ENV` and `APP_KEY` (your encryption key), your database credentials, and your cache and session drivers.

`.env.example` 파일을 복사하여 이전 버전에서 `.env.php` 파일과 동일한 역할을 하는 `5.0의 `.env` 파일을 생성합니다. `APP_ENV` 와 `APP_KEY` (암호화 키), 데이터 베이스 연결 정보, 캐시 그리고 세션 드라이버와 같은 애플리케이션에서 사용가능항 설정 값들을 지정할 수 있습니다.

Additionally, copy any custom values you had in your old `.env.php` file and place them in both `.env` (the real value for your local environment) and `.env.example` (a sample instructional value for other team members).

추가적으로 이전버전의 `.env.php` 파일에 들어 있던 사용자가 지정한 값들을 `.env` 파일(실제 로컬 환경을 위한 값 설정) 과 `.env.example` 파일 (다른 팀 구성원들이 참고할 샘플 값)에 복사합니다.)에 복사해 넣습니다.

For more information on environment configuration, view the [full documentation](/docs/{{version}}/installation#environment-configuration).

환경 설정과 관련된 보다 자세한 내용은 [관련 문서](/docs/{{version}}/configuration#environment-configuration)를 확인하십시오.

> {note} You will need to place the appropriate `.env` file and values on your production server before deploying your Laravel 5 application.

> {note} 라라벨5 애플리케이션을 실제 제품 서버에 배포하기 전에 적절한 값을 지정한 `.env` 파일을 준비해 둘 필요가 있습니다.

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

Then, copy your list of Artisan commands from `start/artisan.php` into the `commands` array of the `app/Console/Kernel.php` file.

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

보안을 기본적으로 강화하기 위해서 라라벨5에서는 `{{ }}` 와 `{{{ }}}` 구문에서 모든 출력을 escape 합니다. 새로운 `{!! !!}` 구문이 escape 되지 않은 출력을 위해서 사용되어 집니다. 애플리케이션을 업데이트 할 때 가장 안전한 선택은 이전처럼 출력하기 위해서 기존 구문들을 `{!! !!}` 표기 방식으로 사용하는 것입니다.

However, if you **must** use the old Blade syntax, add the following lines at the bottom of `AppServiceProvider@register`:

하지만 여러분이 **반드시** 이전 버전의 블레이드 문법을 사용해야 한다면 `AppServiceProvider@register` 의 마지막에 다음 라인들을 추가하면 됩니다.

```php
\Blade::setRawTags('{{', '}}');
\Blade::setContentTags('{{{', '}}}');
\Blade::setEscapedContentTags('{{{', '}}}');
```

This should not be done lightly, and may make your application more vulnerable to XSS exploits. Also, comments with `{{--` will no longer work.

이렇게 하는 것이 편리한것만은 아니며 XSS 공격에 대해서 애플리케이션이 취약해질 수도 있습니다.
또한 코멘트와 `{{--` 는 더이상 동작하지 않습니다.

### Translation Files
### 다국어 파일

Move your language files from `app/lang` to the new `resources/lang` directory.

`app/lang` 디렉토리의 언어 파일들을 `resources/lang` 디렉토리로 옮깁니다

### Public Directory
### public 디렉토리

Copy your application's public assets from your `4.2` application's `public` directory to your new application's `public` directory. Be sure to keep the `5.0` version of `index.php`.

`4.2` 애플리케이션의 `public` 디렉토리에 들어 있던 asset 파일들을 새로운 애플리케이션의 `public` 디렉토리로 복사해 넣습니다. `index.php`의 `5.0` 버전을 유지해야 합니다.

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

애플리케이션에서 라라벨 캐시를 파사드가 아닌 `Illuminate\Cache\CacheManager` 형태로 주입했었다면 이제는 그 대신에 `Illuminate\Contracts\Cache\Repository`를 주입하도록 합니다.

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

> {note} In Laravel 4.2, the default cipher is `MCRYPT_RIJNDAEL_128` (AES), which is considered to be the most secure cipher. Changing the cipher back to `MCRYPT_RIJNDAEL_256` is required to decrypt cookies/values that were encrypted in Laravel <= 4.1

> {note} 라라벨 4.2에서는 기본 암호화 옵션은 가장 안전한 암호화 방식으로 알려진 `MCRYPT_RIJNDAEL_128` (AES)입니다.  암호화 방식을 `MCRYPT_RIJNDAEL_256`으로 되돌릴 필요가 있는 경우는 라라벨 4.1 이전 버전에서 암호화 된 쿠키 / 값을 해독해야 하는 경우입니다.

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

> {note} The `SoftDeletingTrait` can not be applied on a base model. It must be used on an actual model class.

> {note} `SoftDeletingTrait` 는 기본 모델에 사용해서는 안됩니다. 실제 모델 클래스에서만 사용하도록 합니다.

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

라라벨 4.1.29에서는 모든 데이터베이스 드라이버에 대한 컬럼 인용 부분이 향상되었습니다. 모델에서 `fillable` 속성을 사용하지 않은 경우 여러가지 컬럼의 대량 할당에 관한(msass assignment) 취약점으로 부터 애플리케이션을 보호할 수 있습니다. 모델에서 `fillable` 속성을 사용하여 데이터를 할당하는 경우 애플리케이션이 보다 안전해 집니다. 그러나 여러분이 `guarded` 속성을 사용하고 있고 사용자로 부터 전달된 배열을 “update” 나 “save” 유형의 기능에 전달하는 경우라면 애플리케이션이 mass assignment 위험에 노출되어 있으므로 즉시 4.1.29로 업그레이드 해야 합니다.

To upgrade to Laravel 4.1.29, simply `composer update`. No breaking changes are introduced in this release.

라라벨4.1.29로 업그레이드 하기 위해서는 `composer update` 를 실행하면 됩니다. 이 업데이트에서는 소스 수정이 필요한 사항들은 포함되어 있지 않습니다.

<a name="upgrade-4.1.26"></a>
## Upgrading To 4.1.26 From <= 4.1.25
## 4.1.25 이하에서 4.1.26으로 업그레이드 하기

Laravel 4.1.26 introduces security improvements for "remember me" cookies. Before this update, if a remember cookie was hijacked by another malicious user, the cookie would remain valid for a long period of time, even after the true owner of the account reset their password, logged out, etc.

라라벨 4.1.26에서는 “remember me” 쿠키와 관련된 보안이 강화되었습니다. 이 업데이트 이전에는 remember 쿠키가 악의적인 사용자에 의해서 탈취되어 사용자 계정의 암호를 재설정하거나, 로그아웃 해도 장기간 사용이 가능한 상태로 남아 있었습니다.

This change requires the addition of a new `remember_token` column to your `users` (or equivalent) database table. After this change, a fresh token will be assigned to the user each time they login to your application. The token will also be refreshed when the user logs out of the application. The implications of this change are: if a "remember me" cookie is hijacked, simply logging out of the application will invalidate the cookie.

이번 변경사항에서는 `users` 테이블(또는 사용자 관리랄 위한 테이블)에 새로운 `remember_token` 컬럼을 추가해야 합니다. 이 변경으로 인해서 사용자가 애플리케이션에 로그인 할 때마다 새로운 토큰이 할당됩니다. 이 토큰은 사용자가 애플리케이션에서 로그아웃 할때마다 다시 생성됩니다. 이 구현은 만약 “remember me” 쿠키가 탈취 당하더라도 애플리케이션에서 로그아웃 하면 해당 쿠키는 사용할 수 없게 된다는 것을 의미합니다.

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

> {note} All existing "remember me" sessions will be invalidated by this change, so all users will be forced to re-authenticate with your application.

> {note} 이 변경으로 인해 현재 사용중인 "Remember me" 세션이 무효화되기 때문에 모든 사용자는 애플리케이션에 접속할 때 강제로 다시 인증을 해야되게 됩니다.

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

라라벨 애플리케이션을 4.1로 업그레이드 하기 위해서는 `composer.json` 파일에서 `laravel/framework`의 버전을 `4.1.*`으로 변경해야 합니다.

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

패스워드 리마인더는 유연성을 높이기 위해서 많은 변경이 있었습니다. `php artisan auth:reminders-controller` 아티즌 명령어를 실행하여 작성되는 새로운 컨트롤러를 살펴보십시오. 혹은 [변경 관련 문서](/docs/4.1/security#password-reminders-and-reset)를 확인하고 그에 따라 애플리케이션을 업데이트 하십시오.

Update your `app/lang/en/reminders.php` language file to match [this updated file](https://github.com/laravel/laravel/blob/v4.1.0/app/lang/en/reminders.php).

`app/lang/en/reminders.php` 언어 파일을 [새로운 파일](https://github.com/laravel/laravel/blob/v4.1.0/app/lang/en/reminders.php)에 맞게 변경하십시오.

### Environment Detection Updates
### 구동환경 감지 업데이트

For security reasons, URL domains may no longer be used to detect your application environment. These values are easily spoofable and allow attackers to modify the environment for a request. You should convert your environment detection to use machine host names (`hostname` command on Mac, Linux, and Windows).

보안의 이유로 애플리케이션의 구동 환경을 감지 하기 위해서 URL 도메인은 더 이상 사용되지 않습니다. 이 값은 손쉽게 요청시에 변경이 가능하여 공격을 가능하게 합니다. 머신의 호스트 네임 (맥, 리눅스, 윈도우 에서 `hostname` 명령어를 사용하십시오) 을 사용하도록 구동 환경 감지 로직을 변경해야 합니다.

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

변경사항 적용을 완료하면 `composer update` 를 실행하여 애플리케이션 코어 파일을 업데이트 할 수 있습니다. 로딩과 관련된 에러가 발생한다면 다음처럼 `--no-scripts` 옵션과 함께 `update` 명령어를 실행하십시오 `composer update --no-scripts`.

### Wildcard Event Listeners
### 와일드카드 이벤트 리스너

The wildcard event listeners no longer append the event to your handler functions parameters. If you require finding the event that was fired you should use `Event::firing()`.

와일드 카드 이벤트 리스너는 더이상 핸들러 함수의 인자에 이벤트를 전달하지 않습니다.  발행된 이벤트를 확인해야 할 필요가 다면, `Event::firing()` 를 사용하십시오.
