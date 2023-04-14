# Laravel Sanctum
# 라라벨 Sanctum

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [How It Works](#how-it-works)
    - [작동 원리](#how-it-works)
- [Installation](#installation)
- [설치하기](#installation)
- [Configuration](#configuration)
- [설정](#configuration)
    - [Overriding Default Models](#overriding-default-models)
    - [기본 모델 재정의](#overriding-default-models)
- [API Token Authentication](#api-token-authentication)
- [API 토큰 인증](#api-token-authentication)
    - [Issuing API Tokens](#issuing-api-tokens)
    - [API 토큰 발행하기](#issuing-api-tokens)
    - [Token Abilities](#token-abilities)
    - [토큰 Abilities-어빌리티](#token-abilities)
    - [Protecting Routes](#protecting-routes)
    - [라우트 보호하기](#protecting-routes)
    - [Revoking Tokens](#revoking-tokens)
    - [토큰 해지하기](#revoking-tokens)
    - [Token Expiration](#token-expiration)
    - [토큰 만료](#token-expiration)
- [SPA Authentication](#spa-authentication)
- [SPA 인증](#spa-authentication)
    - [Configuration](#spa-configuration)
    - [설정](#spa-configuration)
    - [Authenticating](#spa-authenticating)
    - [인증하기](#spa-authenticating)
    - [Protecting Routes](#protecting-spa-routes)
    - [라우트 보호하기](#protecting-spa-routes)
    - [Authorizing Private Broadcast Channels](#authorizing-private-broadcast-channels)
    - [Private 브로드캐스트 채널 인증하기](#authorizing-private-broadcast-channels)
- [Mobile Application Authentication](#mobile-application-authentication)
- [모바일 어플리케이션 인증](#mobile-application-authentication)
    - [Issuing API Tokens](#issuing-mobile-api-tokens)
    - [API 토큰 발행하기](#issuing-mobile-api-tokens)
    - [Protecting Routes](#protecting-mobile-api-routes)
    - [라우트 보호하기](#protecting-mobile-api-routes)
    - [Revoking Tokens](#revoking-mobile-api-tokens)
    - [토큰 해지하기](#revoking-mobile-api-tokens)
- [Testing](#testing)
- [테스트하기](#testing)

<a name="introduction"></a>
## Introduction
## 시작하기

[Laravel Sanctum](https://github.com/laravel/sanctum) provides a featherweight authentication system for SPAs (single page applications), mobile applications, and simple, token based APIs. Sanctum allows each user of your application to generate multiple API tokens for their account. These tokens may be granted abilities / scopes which specify which actions the tokens are allowed to perform.

[라라벨 Sanctum](https://github.com/laravel/sanctum) 은 SPAs(싱글 페이지 애플리케이션), 모바일 애플리케이션을 위한 인증 시스템과 간단한 토큰 기반의 API를 위한 인증 시스텡을 제공합니다. 이들 인증 시스템은 OAuth 등의 복잡한 인증 기법과 대비되는 가벼운 패더급 인증 기법입니다. Sanctum을 사용하면 애플리케이션의 각 사용자가 자신의 계정에 대해 여러 개의 API 토큰을 생성 할 수 있습니다. 이 토큰에는 애플리케이션 내부 동작을 수행하는 작업에 대한 권한을 지정할 수 있는 abilities-어빌리티 / scopes-스코프가 부여될 수 있습니다.

<a name="how-it-works"></a>
### How It Works
### 작동 원리

Laravel Sanctum exists to solve two separate problems. Let's discuss each before digging deeper into the library.

라라벨 Sanctum은 두 가지 문제를 해결하기 위해 존재합니다. 라이브러리에 대해 더 자세히 알아보기 전에 각각에 대해 살펴 보겠습니다.

<a name="how-it-works-api-tokens"></a>
#### API Tokens
#### API 토큰

First, Sanctum is a simple package you may use to issue API tokens to your users without the complication of OAuth. This feature is inspired by GitHub and other applications which issue "personal access tokens". For example, imagine the "account settings" of your application has a screen where a user may generate an API token for their account. You may use Sanctum to generate and manage those tokens. These tokens typically have a very long expiration time (years), but may be manually revoked by the user at anytime.

첫 번째, Sanctum은 OAuth와 같은 복잡함 없이 유저에게 API 토큰을 발행할 때 사용할 수 있는 간단하게 패키지입니다. 토큰 기반의 인증 방식은 "개인 액세스 토큰(personal access tokens)"을 발행하는 GitHub 및 기타 애플리케이션에서 영감을 얻었습니다. 예를 들어서, "계정 설정(account settings)"에 사용자가 자신의 계정에 대한 API 토큰을 생성할 수 있는 어떤 화면이 있다고 해 봅시다. 이때 Sanctum을 사용한다면 토큰을 생성하고 관리 할 수 있습니다. 이들 토큰은 매우 긴 만료일(몇 년)을 가지고 있지만, 유저는 언제든지 해지(revoke) 할 수 있습니다.

Laravel Sanctum offers this feature by storing user API tokens in a single database table and authenticating incoming HTTP requests via the `Authorization` header which should contain a valid API token.

라라벨 Sanctum은 단일 데이터베이스 테이블 내에 사용자 API 토큰을 저장하고, 애플리케이션으로 유입되는 HTTP 리퀘스트의 `Authorization` 헤더를 검증하여 인증합니다. `Authorization` 헤더는 유효한 토큰을 포함하고 있어야 합니다.

<a name="how-it-works-spa-authentication"></a>
#### SPA Authentication
#### SPA 인증

Second, Sanctum exists to offer a simple way to authenticate single page applications (SPAs) that need to communicate with a Laravel powered API. These SPAs might exist in the same repository as your Laravel application or might be an entirely separate repository, such as a SPA created using Vue CLI or a Next.js application.

두 번째, Sanctum은 라라벨 기반 API와 통신해야하는 싱글 페이지 애플리케이션(SPAs)을 인증하는 간단한 방법을 제공하기 위해 만들어졌습니다. SPA는 라라벨 애플리케이션과 같은 저장소(repository)에 있거나, Vue CLI 또는 Next.js 애플리케이션을 사용해 생성 된 SPA처럼 전체가 별도의 저장소(repository)에 있을 수도 있습니다.

For this feature, Sanctum does not use tokens of any kind. Instead, Sanctum uses Laravel's built-in cookie based session authentication services. Typically, Sanctum utilizes Laravel's `web` authentication guard to accomplish this. This provides the benefits of CSRF protection, session authentication, as well as protects against leakage of the authentication credentials via XSS.

SPA 인증 기능을 사용할 때, Sanctum은 어떤 종류의 토큰도 사용하지 않습니다. 대신, Sanctum은 라라벨에 내장(built-in) 된 쿠키 기반의 세션 인증 서비스를 사용합니다. 일반적으로 Sanctum은 이를 수행하기 위해 라라벨 `web` 인증 가드를 사용합니다. 이것은 CSRF 보호, 세션 인증이라는 이점을 제공할 뿐만 아니라 XSS를 통한 인증 자격 증명(authentication credentials)의 유출도 방어합니다.

Sanctum will only attempt to authenticate using cookies when the incoming request originates from your own SPA frontend. When Sanctum examines an incoming HTTP request, it will first check for an authentication cookie and, if none is present, Sanctum will then examine the `Authorization` header for a valid API token.

Sanctum은 애플리케이션으로 유입된 리퀘스트가 여러분 소유의 SPA 프론트엔드에서 전송된 경우에만 쿠키를 사용하여 인증을 시도합니다. Sanctum은 애플리케이션으로 유입된 HTTP 리퀘스트를 검사할 때 먼저 인증 쿠키가 있는지 확인하고, 인증된 쿠키가 없으면 'Authorization' 헤더에서 유효한 API 토큰이 있는지 검사합니다.

> **Note**  
> It is perfectly fine to use Sanctum only for API token authentication or only for SPA authentication. Just because you use Sanctum does not mean you are required to use both features it offers.

> **Note**
> Sanctum은 API 토큰 인증에만 사용하거나 SPA 인증에만 사용하는 것이 좋습니다. Sanctum을 사용한다고해서 제공되는 두 기능을 모두 사용해야하는 것은 아닙니다.

<a name="installation"></a>
## Installation
## 설치하기

> **Note**  
> The most recent versions of Laravel already include Laravel Sanctum. However, if your application's `composer.json` file does not include `laravel/sanctum`, you may follow the installation instructions below.

> **Note**
> 최신 버전의 라라벨에는 이미 라라벨 Sanctum이 포함되어 있습니다. 그러나 애플리케이션의 `composer.json` 파일에 `laravel/sanctum`이 포함되어 있지 않은 경우 아래 설치 지침을 따를 수 있습니다.

You may install Laravel Sanctum via the Composer package manager:

여러분은 컴포져 패키지 메니저를 통해 라라벨 Sanctum을 설치할 수 있습니다.

```shell
composer require laravel/sanctum
```

Next, you should publish the Sanctum configuration and migration files using the `vendor:publish` Artisan command. The `sanctum` configuration file will be placed in your application's `config` directory:

다음으로, `vendor:publish` 아티즌 명령어를 사용해서 Sanctum의 설정 파일 및 마이그레이션 파일을 애플리케이션 내부에 배치(publish)합니다. `sanctum` 설정 파일은 애플리케이션의 `config` 디렉토리에 위치하게 됩니다.

```shell
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
```

Finally, you should run your database migrations. Sanctum will create one database table in which to store API tokens:

마지막으로, 데이터베이스 마이그레이션을 실행해야 합니다. Sanctum은 API 토큰을 저장하기 위한 데이터베이스 테이블 하나를 생성합니다.

```shell
php artisan migrate
```

Next, if you plan to utilize Sanctum to authenticate a SPA, you should add Sanctum's middleware to your `api` middleware group within your application's `app/Http/Kernel.php` file:

다음으로, 여러분이 SPA 인증으로 Sanctum을 활용할 계획이라면, 애플리케이션의 `app/Http/Kernel.php` 파일에 있는 `api` 미들웨어 그룹에 Sanctum 미들웨어를 추가해야 합니다.

    'api' => [
        \Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful::class,
        \Illuminate\Routing\Middleware\ThrottleRequests::class.':api',
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
    ],

<a name="migration-customization"></a>
#### Migration Customization
#### 마이그레이션 커스터마이징

If you are not going to use Sanctum's default migrations, you should call the `Sanctum::ignoreMigrations` method in the `register` method of your `App\Providers\AppServiceProvider` class. You may export the default migrations by executing the following command: `php artisan vendor:publish --tag=sanctum-migrations`

Sanctum의 기본 마이그레이션을 사용하지 않으려면 `App\Providers\AppServiceProvider` 클래스의 `register` 메소드에서 `Sanctum::ignoreMigrations` 메소드를 호출해야 합니다. 만약 Sanctum의 전체 구성 파일이 아닌 기본 마이그레이션 파일만 새로 생성하고 싶다면 `php artisan vendor:publish --tag=sanctum-migrations` 명령어를 실행하여 기본 마이그레이션만 배치할 수 있습니다.

<a name="configuration"></a>
## Configuration
## 설정

<a name="overriding-default-models"></a>
### Overriding Default Models
### 기본 모델 재정의(Overriding)

Although not typically required, you are free to extend the `PersonalAccessToken` model used internally by Sanctum:

일반적으로 필수는 아니지만 Sanctum에서 내부적으로 사용하는 `PersonalAccessToken` 모델을 자유롭게 확장할 수 있습니다.

    use Laravel\Sanctum\PersonalAccessToken as SanctumPersonalAccessToken;

    class PersonalAccessToken extends SanctumPersonalAccessToken
    {
        // ...
    }

Then, you may instruct Sanctum to use your custom model via the `usePersonalAccessTokenModel` method provided by Sanctum. Typically, you should call this method in the `boot` method of one of your application's service providers:

그런 다음 Sanctum 에서 제공하는 `usePersonalAccessTokenModel` 메소드를 통해 사용자 정의 모델을 사용하도록 Sanctum에 지시할 수 있습니다. 일반적으로 애플리케이션 서비스 프로바이더 중 하나의 `boot` 메소드에서 이 메소드를 호출해야 합니다.

    use App\Models\Sanctum\PersonalAccessToken;
    use Laravel\Sanctum\Sanctum;

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot(): void
    {
        Sanctum::usePersonalAccessTokenModel(PersonalAccessToken::class);
    }

<a name="api-token-authentication"></a>
## API Token Authentication
## API 토큰 인증

> **Note**  
> You should not use API tokens to authenticate your own first-party SPA. Instead, use Sanctum's built-in [SPA authentication features](#spa-authentication).

> **Note**
> 여러분의 first-party-자사 SPA를 인증하기 위해서 API 토큰을 사용하지 마세요. 대신, Sanctum에 내장된 [SPA 인증 기능](#spa-authentication)을 사용하세요.

<a name="issuing-api-tokens"></a>
### Issuing API Tokens
### API 토큰 발급하기

Sanctum allows you to issue API tokens / personal access tokens that may be used to authenticate API requests to your application. When making requests using API tokens, the token should be included in the `Authorization` header as a `Bearer` token.

Sanctum을 사용하면 애플리케이션에 대한 API 리퀘스트를 인증할 때 사용할 수 있는 API 토큰 / 개인 액세스 토큰(personal access tokens)을 발행할 수 있습니다. API 토큰이 포함된 리퀘스트를 받을 때, 토큰은 `Authorization` 헤더 안에 `Bearer` 토큰 형식으로 `Bearer Xa8FBKtcQss...FBLRZwbTnY`와 같은 형태로 포함되어야 합니다.

To begin issuing tokens for users, your User model should use the `Laravel\Sanctum\HasApiTokens` trait:

사용자에게 토큰 발행을 시작하려면 User 모델에 `Laravel\Sanctum\HasApiTokens` trait-트레잇을 사용해야 합니다.

    use Laravel\Sanctum\HasApiTokens;

    class User extends Authenticatable
    {
        use HasApiTokens, HasFactory, Notifiable;
    }

To issue a token, you may use the `createToken` method. The `createToken` method returns a `Laravel\Sanctum\NewAccessToken` instance. API tokens are hashed using SHA-256 hashing before being stored in your database, but you may access the plain-text value of the token using the `plainTextToken` property of the `NewAccessToken` instance. You should display this value to the user immediately after the token has been created:

토큰을 발행하기 위해서 `createToken` 메소드를 사용할 수 있습니다. `createToken` 메소드는 `Laravel\Sanctum\NewAccessToken` 인스턴스를 반환합니다. API 토큰은 데이터베이스에 저장되기 전에 SHA-256 해싱을 이용해서 해시(hash) 되지만, `NewAccessToken` 인스턴스의 `plainTextToken` 속성을 사용해서 plain-text-평문 값에 액세스할 수 있습니다. 토큰이 생성된 직후 이 값을 사용자에게 표시해야 합니다.

    use Illuminate\Http\Request;

    Route::post('/tokens/create', function (Request $request) {
        $token = $request->user()->createToken($request->token_name);

        return ['token' => $token->plainTextToken];
    });

You may access all of the user's tokens using the `tokens` Eloquent relationship provided by the `HasApiTokens` trait:

`HasApiTokens` 트레잇이 제공하는 `tokens` 엘로퀀트 관계(relationship)를 이용해서 모든 사용자의 토큰에 액세스할 수 있습니다.

    foreach ($user->tokens as $token) {
        // ...
    }

<a name="token-abilities"></a>
### Token Abilities
### 토큰 Abilities-어빌리티

Sanctum allows you to assign "abilities" to tokens. Abilities serve a similar purpose as OAuth's "scopes". You may pass an array of string abilities as the second argument to the `createToken` method:

Sanctum을 사용하면 토큰에 "abilities-어빌리티"을 할당할 수 있습니다. abilities-어빌리티는 OAuth의 "스코프(scopes)"와 유사한 목적으로 사용됩니다. `createToken` 메소드의 두 번째 인수로 문자열로 된 abilities-어빌리티의 배열을 넘기게 됩니다.

    return $user->createToken('token-name', ['server:update'])->plainTextToken;

When handling an incoming request authenticated by Sanctum, you may determine if the token has a given ability using the `tokenCan` method:

Sanctum에 의해 인증된 애플리케이션으로 유입되는 리퀘스트를 다룰 때, 토큰으로 인증하는 경우 토큰이 주어진 ability-어빌리티를 가지고 있는지를 판단하려면 `tokenCan` 메소드를 사용하면 됩니다.

    if ($user->tokenCan('server:update')) {
        // ...
    }

<a name="token-ability-middleware"></a>
#### Token Ability Middleware
#### 토큰 Ability-어빌리티 미들웨어

Sanctum also includes two middleware that may be used to verify that an incoming request is authenticated with a token that has been granted a given ability. To get started, add the following middleware to the `$middlewareAliases` property of your application's `app/Http/Kernel.php` file:

Sanctum은 또한 두 종류의 미들웨어를 포함하고 있습니다. 이 두 미들웨어는 애플리케이션으로 유입된 리퀘스트가 헤더에 포함된 토큰으로 인증되었는지를 확인하는데 사용됩니다. 이 때, 토큰의 인증은 토큰에 부여된 ability-어빌리티를 통해서 판명됩니다. 리퀘스트에 포함된 토큰이 가진 어빌리티의 유효성을 확인하려면, 애플리케이션 `app/Http/Kernel.php` 파일의 `$middlewareAliases` 속성에 다음 미들웨어를 추가해야 합니다.

    'abilities' => \Laravel\Sanctum\Http\Middleware\CheckAbilities::class,
    'ability' => \Laravel\Sanctum\Http\Middleware\CheckForAnyAbility::class,

The `abilities` middleware may be assigned to a route to verify that the incoming request's token has all of the listed abilities:

`abilities` 미들웨어는 애플리케이션에 유입된 리퀘스트를 인증하기 위해 라우트에 할당될 수 있습니다. 이 때, 인증 여부는 리퀘스트에 헤더에 포함된 토큰이 미들웨어에 나열된 어빌리티 모두를 가지고 있는지 확인하는 것을 통해 이뤄집니다. 아래 예시에서 미들웨어에 지정한 `check-status`, `place-orders` 어빌리티는 해당 라우터를 통해 들어오는 리퀘스트의 토큰에 포함되어 있어야 합니다.

    Route::get('/orders', function () {
        // Token has both "check-status" and "place-orders" abilities...
    })->middleware(['auth:sanctum', 'abilities:check-status,place-orders']);

The `ability` middleware may be assigned to a route to verify that the incoming request's token has *at least one* of the listed abilities:

`ability` 미들웨어도 애플리케이션에 유입된 리퀘스트를 인증하기 위해 라우트에 할당될 수 있습니다. `abilities` 미들웨어와 달리 `ability` 미들웨어의 인증 여부는 리퀘스트에 헤더에 포함된 토큰이 미들웨어에 나열된 어빌리티 중 *적어도 하나*를 가지고 있는지 확인하는 것을 통해 이뤄집니다. 아래 예시에서 미들웨어에 지정한 `check-status`, `place-orders` 어빌리티는 해당 라우터를 통해 들어오는 리퀘스트의 토큰에 *적어도 하나*가 포함되어 있어야 합니다.

    Route::get('/orders', function () {
        // Token has the "check-status" or "place-orders" ability...
    })->middleware(['auth:sanctum', 'ability:check-status,place-orders']);

<a name="first-party-ui-initiated-requests"></a>
#### First-Party UI Initiated Requests
#### First-Party-자사 UI 개시 리퀘스트

For convenience, the `tokenCan` method will always return `true` if the incoming authenticated request was from your first-party SPA and you are using Sanctum's built-in [SPA authentication](#spa-authentication).

편의상, 애플리케이션으로 유입되는 인증 리퀘스트가 First-Party-자사 SPA에서 온 것이고 Sanctum의 내장 된 [SPA 인증](#spa-authentication)을 사용하는 경우, `tokenCan` 메소드는 항상 `true`를 반환합니다.

However, this does not necessarily mean that your application has to allow the user to perform the action. Typically, your application's [authorization policies](/docs/{{version}}/authorization#creating-policies) will determine if the token has been granted the permission to perform the abilities as well as check that the user instance itself should be allowed to perform the action.

그러나 유저가 전달한 명령에 따라 애플리케이션 내부의 동작을 수행하도록 허락하는 것이 항상 필수적인 것은 아니며 토큰에 부여된 권한에 따라 거부될 수도 있습니다. 일반적으로 애플리케이션의 [권한 부여 정책](/docs/{{version}}/authorization#creating-policies)은 리퀘스트에 의해 요청된 기능을 수행할 수 있는지를 결정하기 위하여, 리퀘스트에 포함된 토큰이 abilities-어빌리티를 수행할 권한을 부여 받았는지 여부를 판단하고, 유저 인스턴스 자체가 요청한 작업을 애플리케이션 내부에서 수행되도록 허락할 것인지를 확인합니다.

For example, if we imagine an application that manages servers, this might mean checking that token is authorized to update servers **and** that the server belongs to the user:

서버를 관리하는 애플리케이션으로 예를 들어 봅시다. 이 애플리케이션은 여러 서버를 제공하고 있으며, 유저는 서버를 소유하고 소유한 서버에 여러 명령들을 전달할 수 있습니다. 여러 유저들 중에서 특정 서버를 조작할 수 있는 유저는 해당 서버를 다룰 수 있는 권한을 가진 유저일 것입니다. 서버로 리퀘스트를 전달한 유저가 해당 서버를 관리할 수 있는 권한을 가진 유저인지를 확인하기 위해서는, 서버로 전달된 토큰이 서버를 업데이트할 수 있는 권한이 있는지 **그리고** 해당 서버가 리퀘스트를 전달한 유저가 소유하고 있는 서버인지를 확인하는 것을 의미할 것입니다.

```php
return $request->user()->id === $server->user_id &&
       $request->user()->tokenCan('server:update')
```

At first, allowing the `tokenCan` method to be called and always return `true` for first-party UI initiated requests may seem strange; however, it is convenient to be able to always assume an API token is available and can be inspected via the `tokenCan` method. By taking this approach, you may always call the `tokenCan` method within your application's authorizations policies without worrying about whether the request was triggered from your application's UI or was initiated by one of your API's third-party consumers.

처음에는 first-party-자사 UI에서 시작된 리퀘스트에 대해 항상 `true`를 반환하도록 허용하는 것이 이상하게 보일 수 있지만, 항상 API 토큰을 사용할 수 있다고 가정하고 `tokenCan` 메서드를 통해 검사할 수 있게 만드는 것은 편의성을 제공합니다. 이 접근 방식을 사용하면 리퀘스트가 애플리케이션의 UI에서 트리거되었는지 또는 애플리케이션 API의 third-party-타사 이용자의 요청에 의해 시작되었는지 걱정할 필요 없이 애플리케이션의 권한 부여 정책 내에서 항상 `tokenCan` 메서드를 호출할 수 있습니다.


<a name="protecting-routes"></a>
### Protecting Routes
### 라우트 보호하기

To protect routes so that all incoming requests must be authenticated, you should attach the `sanctum` authentication guard to your protected routes within your `routes/web.php` and `routes/api.php` route files. This guard will ensure that incoming requests are authenticated as either stateful, cookie authenticated requests or contain a valid API token header if the request is from a third party.

애플리케이션으로 유입되는 모든 리퀘스트는 인증되어야 합니다. 리퀘스트 인증을 통해 라우트를 보호하기 위해서는 `routes/web.php` 및 `routes/api.php` 라우트 파일 안에서 라우터에 `sanctum` 인증 가드를 붙여야 합니다. 이 가드는 유입된 리퀘스트가 쿠키로 인증되는 스테이트풀(stateful) 인증방식인지 또는 third-party-타사에서 온 리퀘스트라면 유효한 API 토큰 헤더를 포함하고 있는지를 확인하여 인증합니다.

You may be wondering why we suggest that you authenticate the routes within your application's `routes/web.php` file using the `sanctum` guard. Remember, Sanctum will first attempt to authenticate incoming requests using Laravel's typical session authentication cookie. If that cookie is not present then Sanctum will attempt to authenticate the request using a token in the request's `Authorization` header. In addition, authenticating all requests using Sanctum ensures that we may always call the `tokenCan` method on the currently authenticated user instance:

애플리케이션의 `routes/web.php` 파일에서 `sanctum` 가드를 사용하여 라우트를 인증하는 것이 왜 권장되는지 궁금할 것입니다. 먼저, Sanctum은 라라벨에서 발급되는 일반적인 세션 인증 쿠키를 사용하여 애플리케이션으로 유입되는 리퀘스트를 인증하려고 시도합니다. 유입된 리퀘스트에 인증을 통과할 수 있는 쿠키가 없으면 Sanctum은 리퀘스트의 `Authorization` 헤더에 있는 토큰을 사용하여 리퀘스트를 인증하려고 시도합니다. 또한 Sanctum을 사용하여 모든 리퀘스트를 인증하면 현재 인증된 유저 인스턴스에서 항상 `tokenCan` 메소드를 호출할 수 있습니다.

    use Illuminate\Http\Request;

    Route::middleware('auth:sanctum')->get('/user', function (Request $request) {
        return $request->user();
    });

<a name="revoking-tokens"></a>
### Revoking Tokens
### 토큰 해지하기

You may "revoke" tokens by deleting them from your database using the `tokens` relationship that is provided by the `Laravel\Sanctum\HasApiTokens` trait:

`Laravel\Sanctum\HasApiTokens` trait-트레잇이 제공하는 `tokens` 관계(relationship)를 사용하여 데이터베이스에서 토큰을 삭제하여 "revoke-해지"합니다.

    // Revoke all tokens...
    // 모든 토큰 해지...
    $user->tokens()->delete();

    // Revoke the token that was used to authenticate the current request...
    // 현재 리퀘스트를 인증하는 데 사용된 토큰을 취소...
    $request->user()->currentAccessToken()->delete();

    // Revoke a specific token...
    // 지정된 토큰 해지...
    $user->tokens()->where('id', $tokenId)->delete();

<a name="token-expiration"></a>
### Token Expiration
### 토큰 만료

By default, Sanctum tokens never expire and may only be invalidated by [revoking the token](#revoking-tokens). However, if you would like to configure an expiration time for your application's API tokens, you may do so via the `expiration` configuration option defined in your application's `sanctum` configuration file. This configuration option defines the number of minutes until an issued token will be considered expired:

기본 설정으로는 Sanctum 토큰은 만료되지 않으며 [토큰 해지](#revoking-tokens)를 통해서만 무효화됩니다. 하지만 애플리케이션의 API 토큰에 만료 시간을 설정하고 싶으면 `sanctum` 설정 파일에 `expiration` 옵션에 값(분)을 정의해주면 됩니다. 이 옵션은 발행된 토큰이 몇 분 뒤에 만료되는지 정의합니다.

```php
'expiration' => 525600,
```

If you have configured a token expiration time for your application, you may also wish to [schedule a task](/docs/{{version}}/scheduling) to prune your application's expired tokens. Thankfully, Sanctum includes a `sanctum:prune-expired` Artisan command that you may use to accomplish this. For example, you may configure a scheduled tasks to delete all expired token database records that have been expired for at least 24 hours:

애플리케이션에 토큰 만료 시간을 설정한 경우, [작업(task)를 스케줄링하기](/docs/{{version}}/scheduling)을 통해 애플리케이션에서 만료된 토큰을 정리(prune)할 수도 있습니다. 고맙게도, Sanctum에는 이 작업을 수행하는 데 사용할 수 있는 `sanctum:prune-expired` 아티산 명령이 포함되어 있습니다. 예를 들어, 데이터베이스에서 만료된 모든 토큰 레코드를 삭제하는 작업을 스케줄링해 봅시다.

```php
$schedule->command('sanctum:prune-expired --hours=24')->daily();
```

<a name="spa-authentication"></a>
## SPA Authentication
## SPA 인증

Sanctum also exists to provide a simple method of authenticating single page applications (SPAs) that need to communicate with a Laravel powered API. These SPAs might exist in the same repository as your Laravel application or might be an entirely separate repository.

Sanctum은 또한 라라벨 기반 API와 통신해야하는 싱글 페이지 애플리케이션(SPAs)을 인증하는 간단한 방법을 제공하기 위해 만들어졌습니다. SPA는 라라벨 애플리케이션과 같은 저장소(repository)에 있거나, Vue CLI를 사용해 생성 된 SPA처럼 전체가 분리된 저장소에 있을 수도 있습니다.

For this feature, Sanctum does not use tokens of any kind. Instead, Sanctum uses Laravel's built-in cookie based session authentication services. This approach to authentication provides the benefits of CSRF protection, session authentication, as well as protects against leakage of the authentication credentials via XSS.

SPA 인증 기능을 위해서 Sanctum은 어떤 종류의 토큰도 사용하지 않습니다. 대신, Sanctum은 라라벨에 내장(built-in) 된 쿠키 기반의 세션 인증 서비스를 사용합니다. 이것은 CSRF 보호, 세션 인증이라는 이점을 제공할 뿐만 아니라 XSS를 통한 인증 자격 증명(authentication credentials)의 유출을 방어합니다. 

> **Warning**  
> In order to authenticate, your SPA and API must share the same top-level domain. However, they may be placed on different subdomains. Additionally, you should ensure that you send the `Accept: application/json` header with your request.

> **Warning**
> 인증을 받으려면 SPA와 API가 동일한 최상위 도메인을 공유해야합니다. 최상위 도메인은 `https://jetstream.laravel.com/`에서 `laravel.com`을 의미합니다. 그러나 `jetstream.laravel.com`과 같이 다른 서브도메인으로 인증되도록 지정할 수 있습니다. 또한 리퀘스트를 전송할 때는 `Accept: application/json` 헤더를 함께 보내야 합니다.

<a name="spa-configuration"></a>
### Configuration
### 설정

<a name="configuring-your-first-party-domains"></a>
#### Configuring Your First-Party Domains
#### First-Party-자사 도메인 설정

First, you should configure which domains your SPA will be making requests from. You may configure these domains using the `stateful` configuration option in your `sanctum` configuration file. This configuration setting determines which domains will maintain "stateful" authentication using Laravel session cookies when making requests to your API.

먼저 SPA가 보낸 리퀘스트를 받을 도메인을 설정해야 합니다. `sanctum` 설정 파일 안에 `stateful` 옵션에 사용할 도메인을 설정합니다. 이 설정은 설정한 도메인으로 API 리퀘스트를 날릴 때 라라벨 세션 쿠키를 사용하여 애플리케이션의 저장소에 인증 정보를 저장하는 "스테이트풀(stateful)" 인증을 유지할 도메인을 결정합니다.

> **Warning**  
> If you are accessing your application via a URL that includes a port (`127.0.0.1:8000`), you should ensure that you include the port number with the domain.

> **Warning**
> 포트 (`127.0.0.1:8000`)가 포함 된 URL을 통해 애플리케이션에 액세스하는 경우 도메인에 포트 번호를 포함해야합니다.

<a name="sanctum-middleware"></a>
#### Sanctum Middleware
#### Sanctum 미들웨어

Next, you should add Sanctum's middleware to your `api` middleware group within your `app/Http/Kernel.php` file. This middleware is responsible for ensuring that incoming requests from your SPA can authenticate using Laravel's session cookies, while still allowing requests from third parties or mobile applications to authenticate using API tokens:

다음으로, Sanctum 미들웨어를 `app/Http/Kernel.php` 파일 내에 있는 `api` 미들웨어 그룹에 추가해야 합니다. 이 미들웨어는 SPA에서 들어오는 리퀘스트가 라라벨의 세션 쿠키를 사용해서 인증할 수 있도록 보장하는 동시에, 여전히 third-parties-타사 혹은 모바일 애플리케이션으로부터 API 토큰을 사용하여 인증할 수 있도록 합니다.

    'api' => [
        \Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful::class,
        \Illuminate\Routing\Middleware\ThrottleRequests::class.':api',
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
    ],

<a name="cors-and-cookies"></a>
#### CORS & Cookies
#### CORS 와 쿠키

If you are having trouble authenticating with your application from a SPA that executes on a separate subdomain, you have likely misconfigured your CORS (Cross-Origin Resource Sharing) or session cookie settings.

별도의 서브도메인에서 실행되는 SPA에서 애플리케이션을 인증하는데 문제가 있는 경우, CORS (Cross-Origin Resource Sharing) 또는 세션 쿠키 세팅이 잘못 설정되었을 수 있습니다.

You should ensure that your application's CORS configuration is returning the `Access-Control-Allow-Credentials` header with a value of `True`. This may be accomplished by setting the `supports_credentials` option within your application's `config/cors.php` configuration file to `true`.

애플리케이션의 CORS 설정 값이 `True`인 `Access-Control-Allow-Credentials` 헤더를 리턴하는지 확인해야 합니다. 이것은 애플리케이션의 `config/cors.php` 설정 파일에서 `support_credentials` 옵션을 `true`로 세팅하여 수행할 수 있습니다.

In addition, you should enable the `withCredentials` option on your application's global `axios` instance. Typically, this should be performed in your `resources/js/bootstrap.js` file. If you are not using Axios to make HTTP requests from your frontend, you should perform the equivalent configuration on your own HTTP client:

또한 애플리케이션의 전역 `axios` 인스턴스에서 `withCredentials` 옵션을 활성화해야 합니다. 일반적으로 이것은 `resources/js/bootstrap.js` 파일에서 설정할 수 있습니다. 프론트엔드에서 HTTP 리퀘스트를 보낼 때 Axios를 사용하지 않는 경우, 여러분이 소유하고 있는 HTTP 클라이언트에서 Axios와 동일한 CORS 설정이 될 수 있도록 세팅해 줘야 합니다.

```js
axios.defaults.withCredentials = true;
```

Finally, you should ensure your application's session cookie domain configuration supports any subdomain of your root domain. You may accomplish this by prefixing the domain with a leading `.` within your application's `config/session.php` configuration file:

마지막으로 애플리케이션의 세션 쿠키 도메인 설정이 루트 도메인의 모든 서브도메인을 지원하는지를 확인해야 합니다. 애플리케이션의 `config/session.php` 설정 파일에서 도메인 앞에 접두사 '.`를 붙이면 이 작업을 수행할 수 있습니다.

    'domain' => '.domain.com',

<a name="spa-authenticating"></a>
### Authenticating
### 인증하기

<a name="csrf-protection"></a>
#### CSRF Protection
#### CSRF 보호

To authenticate your SPA, your SPA's "login" page should first make a request to the `/sanctum/csrf-cookie` endpoint to initialize CSRF protection for the application:

SPA를 인증하려면 먼저 SPA 로그인 페이지에서 애플리케이션에 대한 CSRF 보호(protection)를 초기화하기 위해서 `/sanctum/csrf-cookie` 엔드포인트에 가장 먼저 리퀘스트를 보내야 합니다.

```js
axios.get('/sanctum/csrf-cookie').then(response => {
    // Login...
});
```

During this request, Laravel will set an `XSRF-TOKEN` cookie containing the current CSRF token. This token should then be passed in an `X-XSRF-TOKEN` header on subsequent requests, which some HTTP client libraries like Axios and the Angular HttpClient will do automatically for you. If your JavaScript HTTP library does not set the value for you, you will need to manually set the `X-XSRF-TOKEN` header to match the value of the `XSRF-TOKEN` cookie that is set by this route.

이 리퀘스트를 처리하면서, 라라벨은 현재 CSRF 토큰을 포함하는 `XSRF-TOKEN` 쿠키를 설정합니다. 이 토큰은 후속 리퀘스트를 전송할 때 `X-XSRF-TOKEN` 헤더로 전달되어야하며, Axios 및 Angular HttpClient와 같은 라이브러리가 자동으로 수행합니다. 자바스크립트의 HTTP 라이브러리가 값을 자동으로 설정해 주지 않으면 `/sanctum/csrf-cookie` 라우터를 통해 반환 받은 `XSRF-TOKEN` 쿠키의 값과 일치하는 값을 후속 리퀘스트의 `X-XSRF-TOKEN` 헤더가 가질 수 있도록 수동으로 설정해야 합니다.

<a name="logging-in"></a>
#### Logging In
#### 로그인

Once CSRF protection has been initialized, you should make a `POST` request to your Laravel application's `/login` route. This `/login` route may be [implemented manually](/docs/{{version}}/authentication#authenticating-users) or using a headless authentication package like [Laravel Fortify](/docs/{{version}}/fortify).

한번 CSRF 보호가 초기화되면, 라라벨 애플리케이션의 `/login` 라우터에 `POST` 리퀘스트를 보내야 합니다. 이 `/login` 라우트를 구현하기 위해서는 [수동으로 구현](/docs/{{version}}/authentication#authenticating-users)하거나 [라라벨 Fortify-포티파이](/docs/{{version}}/fortify)와 같은 헤드리스 인증 패키지를 사용하면 됩니다.

If the login request is successful, you will be authenticated and subsequent requests to your application's routes will automatically be authenticated via the session cookie that the Laravel application issued to your client. In addition, since your application already made a request to the `/sanctum/csrf-cookie` route, subsequent requests should automatically receive CSRF protection as long as your JavaScript HTTP client sends the value of the `XSRF-TOKEN` cookie in the `X-XSRF-TOKEN` header.

로그인 리퀘스트가 성공하면 인증된 것이며 라라벨 애플리케이션은 클라이언트에게 세션 쿠키를 발급합니다. 클라이언트는 발급 받은 세션 쿠키를 담아 후속 리퀘스트를 보내며, 셍텀 가드가 연결된 라우트는 이 리퀘스트를 자동으로 인증합니다. 좀 더 설명하자면, 애플리케이션은 이미 `/sanctum/csrf-cookie` 라우트로 리퀘스트를 전송했으므로, 후속 리퀘스트는 클라이언트의 자바스크립트가 HTTP 리퀘스트의 `X-XSRF-TOKEN` 헤더에 `XSRF-TOKEN` 쿠키 값을 전송하게 되어 자동적으로 CSRF 보호를 받게 됩니다.

Of course, if your user's session expires due to lack of activity, subsequent requests to the Laravel application may receive 401 or 419 HTTP error response. In this case, you should redirect the user to your SPA's login page.

물론 유저가 세션을 갱신하는 요청을 시도하지 않는다면 세션은 만료됩니다. 세션이 만료되면 라라벨 애플리케이션에 후속 리퀘스트를 보낼 때 401 또는 419 HTTP 오류 스테이터스의 리스폰스를 받을 수 있습니다. 이런 경우에는 유저를 SPA의 로그인 페이지로 리디렉션하여 다시 CSRF 보호를 초기화 해야 합니다.

> **Warning**  
> You are free to write your own `/login` endpoint; however, you should ensure that it authenticates the user using the standard, [session based authentication services that Laravel provides](/docs/{{version}}/authentication#authenticating-users). Typically, this means using the `web` authentication guard.

> **Warning**
> 자신만의 `/login` 엔드포인트를 자유롭게 작성할 수 있습니다. 그러나 표준인 [라라벨이 제공하는 세션 기반의 인증 서비스](/docs/{{version}}/authentication#authenticating-users)를 사용해서 유저를 인증할 수 있습니다. 일반적으로 이것은 `web` 인증 가드를 사용하는 것을 의미합니다


<a name="protecting-spa-routes"></a>
### Protecting Routes
### 라우트 보호하기

To protect routes so that all incoming requests must be authenticated, you should attach the `sanctum` authentication guard to your API routes within your `routes/api.php` file. This guard will ensure that incoming requests are authenticated as either a stateful authenticated requests from your SPA or contain a valid API token header if the request is from a third party:

애플리케이션으로 유입되는 모든 리퀘스트가 인증되도록 라우트를 보호하려면 `routes/api.php` 파일의 API 라우트에 `sanctum` 인증 가드를 연결해야 합니다. 이 가드는 애플리케이션으로 유입되는 리퀘스트가 SPA로부터 온 것이고 인증 정보를 저장소에 저장하는 스테이트풀(stateful) 인증 요청으로 인증 되는지 또는 third-party-타사 리퀘스트인 경우 유효한 API 토큰 헤더를 가지고 있는지를 확인합니다.

    use Illuminate\Http\Request;

    Route::middleware('auth:sanctum')->get('/user', function (Request $request) {
        return $request->user();
    });

<a name="authorizing-private-broadcast-channels"></a>
### Authorizing Private Broadcast Channels
### Private 브로드캐스트 채널 인증하기

If your SPA needs to authenticate with [private / presence broadcast channels](/docs/{{version}}/broadcasting#authorizing-channels), you should place the `Broadcast::routes` method call within your `routes/api.php` file:

만약 SPA가 [Private / Presence 브로드캐스트 채널](/docs/{{version}}/broadcasting#authorizing-channels)로 인증될 필요가 있다면, `routes/api.php` 파일 내에 `Broadcast::routes` 메소드를 호출(method call)을 배치해야 합니다.

    Broadcast::routes(['middleware' => ['auth:sanctum']]);

Next, in order for Pusher's authorization requests to succeed, you will need to provide a custom Pusher `authorizer` when initializing [Laravel Echo](/docs/{{version}}/broadcasting#client-side-installation). This allows your application to configure Pusher to use the `axios` instance that is [properly configured for cross-domain requests](#cors-and-cookies):

다음으로, Pusher-푸셔의 인증 리퀘스트를 성공하려면, [라라벨 Echo-에코](/docs/{{version}}/broadcasting#client-side-installation)를 초기화할 때, 커스텀 Pusher-푸셔 `authorizer`를 제공해야 합니다. 이를 통해 애플리케이션은 [크로스 도메인 리퀘스트에 대해 올바르게 설정된](#cors-and-cookies) `axios` 인스턴스를 사용하도록 Pusher-푸셔를 설정할 수 있습니다.

```js
window.Echo = new Echo({
    broadcaster: "pusher",
    cluster: import.meta.env.VITE_PUSHER_APP_CLUSTER,
    encrypted: true,
    key: import.meta.env.VITE_PUSHER_APP_KEY,
    authorizer: (channel, options) => {
        return {
            authorize: (socketId, callback) => {
                axios.post('/api/broadcasting/auth', {
                    socket_id: socketId,
                    channel_name: channel.name
                })
                .then(response => {
                    callback(false, response.data);
                })
                .catch(error => {
                    callback(true, error);
                });
            }
        };
    },
})
```

<a name="mobile-application-authentication"></a>
## Mobile Application Authentication
## 모바일 애플리케이션 인증

You may also use Sanctum tokens to authenticate your mobile application's requests to your API. The process for authenticating mobile application requests is similar to authenticating third-party API requests; however, there are small differences in how you will issue the API tokens.

Sanctum 토큰을 사용하여 모바일 애플리케이션의 API 리퀘스트를 인증할 수 있습니다. 모바일 애플리케이션의 리퀘스트를 인증하는 프로세스는 third-party-타사 API 리퀘스트를 인증하는 것과 유사합니다. 그러나, API 토큰을 발행하는 방법에는 약간의 차이가 있습니다.

<a name="issuing-mobile-api-tokens"></a>
### Issuing API Tokens
### API 토큰 발급하기

To get started, create a route that accepts the user's email / username, password, and device name, then exchanges those credentials for a new Sanctum token. The "device name" given to this endpoint is for informational purposes and may be any value you wish. In general, the device name value should be a name the user would recognize, such as "Nuno's iPhone 12".

시작하려면, 사용자의 이메일 / 사용자 이름, 비밀번호 및 디바이스 이름을 리퀘스트에 포함된 정보로 받는 라우트를 만든 다음, 이 리퀘스트의 자격 증명 방식을 새로운 Sanctum 토큰으로 교환하십시오. 엔드포인트에 지정된 "디바이스 이름"은 정보 제공을 위한 것이며, 원하는 값이면 무엇이든 상관없습니다. 일반적으로 장치 이름 값은 "Nuno의 iPhone 12"와 같이 사용자가 알아볼 수 있는 이름이어야 합니다.

Typically, you will make a request to the token endpoint from your mobile application's "login" screen. The endpoint will return the plain-text API token which may then be stored on the mobile device and used to make additional API requests:

일반적으로는 모바일 애플리케이션의 "로그인" 화면에서 토큰 인증의 엔드포인트에 리퀘스트를 보냅니다. 엔드포인트는 plain-text-평문으로 된 API 토큰을 반환한 다음 이를 모바일 디바이스에 저장하여 추가 API 리퀘스트를 수행할 때는 발급된 API 토큰을 사용하여 매번 인증할 필요없는 편의성을 제공합니다.

    use App\Models\User;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Hash;
    use Illuminate\Validation\ValidationException;

    Route::post('/sanctum/token', function (Request $request) {
        $request->validate([
            'email' => 'required|email',
            'password' => 'required',
            'device_name' => 'required',
        ]);

        $user = User::where('email', $request->email)->first();

        if (! $user || ! Hash::check($request->password, $user->password)) {
            throw ValidationException::withMessages([
                'email' => ['The provided credentials are incorrect.'],
            ]);
        }

        return $user->createToken($request->device_name)->plainTextToken;
    });

When the mobile application uses the token to make an API request to your application, it should pass the token in the `Authorization` header as a `Bearer` token.

모바일 디바이스가 토큰을 사용하여 애플리케이션에 API 리퀘스트를 전송하는 경우, `Authorization` 헤더에 `Bearer` 토큰으로 `Bearer Xa8FBKtcQss...FBLRZwbTnY`와 같은 형태로 전달해야 합니다.

> **Note**  
> When issuing tokens for a mobile application, you are also free to specify [token abilities](#token-abilities).

> **Note**
> 모바일 애플리케이션에 대한 토큰을 발행할 때 [토큰 abilities-어빌리티](#token-abilities)을 자유롭게 지정할 수도 있습니다

<a name="protecting-mobile-api-routes"></a>
### Protecting Routes
### 라우트 보호하기

As previously documented, you may protect routes so that all incoming requests must be authenticated by attaching the `sanctum` authentication guard to the routes:

앞서 설명한 바와 같이, 라우트에 `sanctum` 인증 가드를 연결하여 라우트를 보호할 수 있습니다. 이렇게 하면 애플리케이션으로 유입되는 모든 리퀘스트는 `sanctum` 인증 가드에 의해 인증이 되어야 합니다.

    Route::middleware('auth:sanctum')->get('/user', function (Request $request) {
        return $request->user();
    });

<a name="revoking-mobile-api-tokens"></a>
### Revoking Tokens
### 토큰 해지하기

To allow users to revoke API tokens issued to mobile devices, you may list them by name, along with a "Revoke" button, within an "account settings" portion of your web application's UI. When the user clicks the "Revoke" button, you can delete the token from the database. Remember, you can access a user's API tokens via the `tokens` relationship provided by the `Laravel\Sanctum\HasApiTokens` trait:

어떤 사용자가 여러 디바이스를 사용하고 있다고 가정해 봅시다. 사용자에게 발급된 API 토큰을 해제할 수 있도록하려면 애플리케이션의 UI에서 "계정 설정" 부분에 접근한 유저에게 모바일 디바이스의 이름을 리스트를 표시하고 각각의 디바이스에 대한 "해지" 버튼을 만들어 둡니다. 사용자가 "해지" 버튼을 클릭하면 데이터베이스에서 토큰을 삭제할 수 있습니다. 이때 `Laravel\Sanctum\HasApiTokens` trait-트레잇이 제공하는 `tokens` 관계(relationship)를 통해 사용자의 API 토큰에 액세스할 수 있습니다.

    // Revoke all tokens...
    // 모든 토큰 해지...
    $user->tokens()->delete();

    // Revoke a specific token...
    // 지정된 토큰 해지...
    $user->tokens()->where('id', $tokenId)->delete();

<a name="testing"></a>
## Testing
## 테스트하기

While testing, the `Sanctum::actingAs` method may be used to authenticate a user and specify which abilities should be granted to their token:

테스트하는 동안 `Sanctum::actingAs` 메소드를 사용하여 사용자를 인증하고 토큰에 부여할 abilities-어빌리티를 지정할 수 있습니다.

    use App\Models\User;
    use Laravel\Sanctum\Sanctum;

    public function test_task_list_can_be_retrieved(): void
    {
        Sanctum::actingAs(
            User::factory()->create(),
            ['view-tasks']
        );

        $response = $this->get('/api/task');

        $response->assertOk();
    }

If you would like to grant all abilities to the token, you should include `*` in the ability list provided to the `actingAs` method:

모든 abilities-어빌리티를 토큰 권한에 부여하려면 `actingAs` 메소드에 제공되는 두 번째 인자인 권한 리스트에 `*`를 포함시켜야 합니다.

    Sanctum::actingAs(
        User::factory()->create(),
        ['*']
    );
