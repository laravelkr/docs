# Laravel Airlock
# 라라벨 에어락

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [How It Works](#how-it-works)
    - [작동 원리](#how-it-works)
- [Installation](#installation)
- [설치하기](#installation)
- [API Token Authentication](#api-token-authentication)
- [API 토큰 인증](#api-token-authentication)
    - [Issuing API Tokens](#issuing-api-tokens)
    - [API 토큰 발행하기](#issuing-api-tokens)
    - [Token Abilities](#token-abilities)
    - [토큰 기능(Token Abilities)](#token-abilities)
    - [Protecting Routes](#protecting-routes)
    - [라우트 보호하기](#protecting-routes)
    - [Revoking Tokens](#revoking-tokens)
    - [토큰 해지하기](#revoking-tokens)
- [SPA Authentication](#spa-authentication)
- [SPA 인증](#spa-authentication)
    - [Configuration](#spa-configuration)
    - [설정](#spa-configuration)
    - [Authenticating](#spa-authenticating)
    - [인증하기](#spa-authenticating)
    - [Protecting Routes](#protecting-spa-routes)
    - [라우트 보호하기](#protecting-spa-routes)
    - [Authorizing Private Broadcast Channels](#authorizing-private-broadcast-channels)
    - [비공개 브로드캐스트 채널 승인하기](#authorizing-private-broadcast-channels)
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

Laravel Airlock provides a featherweight authentication system for SPAs (single page applications), mobile applications, and simple, token based APIs. Airlock allows each user of your application to generate multiple API tokens for their account. These tokens may be granted abilities / scopes which specify which actions the tokens are allowed to perform.

라라벨 에어락은 SPAs(single page applications), 모바일 애플리케이션과 간단한 토큰 베이스의 API에서 사용될 수 있는 경량 인증 시스템을 제공합니다. 에어락은 애플리케이션 사용자들에게 계정(account)을 위한 복수의 API 토큰을 생성하도록 허용합니다.

<a name="how-it-works"></a>
### How It Works
### 작동 원리

#### API Tokens
#### API 토큰

Laravel Airlock exists to solve two separate problems. First, it is a simple package to issue API tokens to your users without the complication of OAuth. This feature is inspired by GitHub "access tokens". For example, imagine the "account settings" of your application has a screen where a user may generate an API token for their account. You may use Airlock to generate and manage those tokens. These tokens typically have a very long expiration time (years), but may be manually revoked by the user at anytime.

라라벨 에어락은 두 가지 분리 문제를 해결하기 위해서 제작되었습니다. 첫 번째, 에어락은 OAuth의 복잡함 없이 사용자에게 API 토큰을 발행하기 위한 단순한 패키지입니다. 이 특징은 깃헙 "access tokens"에서 영감을 받았습니다. 예를 들어서,  계정에 필요한 API 토큰을 생성하는 사용자의 어떤 화면을 상상해 보세요. 그 토큰들은 매우 긴 만료일(몇 년)을 가지고 있지만, 어느 때라도 사용자에 의해서 재발행(revoke) 될 수도 있습니다.

Laravel Airlock offers this feature by storing user API tokens in a single database table and authenticating incoming requests via the `Authorization` header which should contain a valid API token.

라라벨 에어락은 단일 데이터베이스 테이블 내에 사용자 API 토큰을 저장하고, 이 API 토큰을 포함하는 `Authorization` 헤더를 통해 오는 요청(request) 인증함으로써 이 기능을 제공합니다.

#### SPA Authentication
#### SPA 인증

> {tip} It is perfectly fine to use Airlock only for API token authentication or only for SPA authentication. Just because you use Airlock does not mean you are required to use both features it offers.

> {팁} 오롯이 API 토큰 인증 혹은 SPA 인증만을 위해서 에어락을 사용하는 것은 확실히 괜찮습니다. 이것은 단지 에어락을 사용하기 때문에 위에서 언급한 두 가지 특징 특징을 모두 사용해야 한다는 것을 의미하는 것은 아닙니다.

Second, Airlock exists to offer a simple way to authenticate single page applications (SPAs) that need to communicate with a Laravel powered API. These SPAs might exist in the same repository as your Laravel application or might be an entirely separate repository, such as a SPA created using Vue CLI.

두번째, 에어락은 라라벨로 만들어진 API와 통신하기 위해서 필요한 싱글 페이지 애플리케이션(SPAs)을 인증하는 간단한 방법을 제공하기 위해서 존재합니다. 이런 SPAs는 라라벨 애플리케이션과 같은 저장소(repository)에 있거나, Vue CLI를 사용해 만들어진 SPA처럼 전체가 분리된 저장소에 존재하게 됩니다.

For this feature, Airlock does not use tokens of any kind. Instead, Airlock uses Laravel's built-in cookie based session authentication services. This provides the benefits of CSRF protection, session authentication, as well as protects against leakage of the authentication credentials via XSS. Airlock will only attempt to authenticate using cookies when the incoming request originates from your own SPA frontend.

이 기능을 위해서 에어락은 어떤 종류의 토큰도 사용하지 않습니다. 대신, 에어락은 라라벨에 내장(built-in) 된 쿠키 기반의 세션 인증 서비스를 사용합니다. 이것은 인증 CSRF 보호, 세션 인증이라는 장점과 더불어 XSS를 통한 인증 자격 증명(authentication credentials)의 누출을 방어합니다. 에어락은 단지 어떤 요청이 여러분 자신의 SPA 프론트엔드로부터 비롯될 때 쿠키를 사용한 인증을 시도할 것입니다.

<a name="installation"></a>
## Installation
## 설치하기

You may install Laravel Airlock via Composer:

여러분은 컴포져를 통해 라라벨 에어락을 설치할 수 있습니다:

    composer require laravel/airlock

Next, you should publish the Airlock configuration and migration files using the `vendor:publish` Artisan command. The `airlock` configuration file will be placed in your `config` directory:

다음으로, 에어락의 설정을 내보내고(publish), `vendor:publish` 아티즌 명령어를 사용해서 파일을 마이그레이션 합니다. `airlock` 설정 파일은 `config` 디렉토리에 위치하게 됩니다.

    php artisan vendor:publish --provider="Laravel\Airlock\AirlockServiceProvider"

Finally, you should run your database migrations. Airlock will create one database table in which to store API tokens:

마지막으로, 데이터베이스 마이그레이션을 실행해야 합니다. 에어락은 API 토큰을 저장하기 위한 데이터베이스 테이블 하나를 생성합니다.

    php artisan migrate

Next, if you plan to utilize Airlock to authenticate an SPA, you should add Airlock's middleware to your `api` middleware group within your `app/Http/Kernel.php` file:

다음으로, 여러분이 SPA 인증으로 Airlock을 활용할 계획이라면, `app/Http/Kernel.php` 파일에 있는 `api` 미들웨어 그룹에 에어락의 미들웨어를 넣어야 합니다.

    use Laravel\Airlock\Http\Middleware\EnsureFrontendRequestsAreStateful;

    'api' => [
        EnsureFrontendRequestsAreStateful::class,
        'throttle:60,1',
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
    ],

<a name="api-token-authentication"></a>
## API Token Authentication
## API 토큰 인증

> {tip} You should not use API tokens to authenticate your own first-party SPA. Instead, use Airlock's built-in [SPA authentication](#spa-authentication).

> {팁} 여러분의 자사 SPA를 인증하기 위해서 API 토큰을 사용하지 마세요. 대신, 에어락에 내장된 [SPA 인증](#spa-authentication)을 사용하세요.

<a name="issuing-api-tokens"></a>
### Issuing API Tokens
### API 토큰 발급하기

Airlock allows you to issue API tokens / personal access tokens that may be used to authenticate API requests. When making requests using API tokens, the token should be included in the `Authorization` header as a `Bearer` token.

에어락은 여러분이 API 요청을 인증하기 위해서 사용되는 API 토큰 / 개인 액세스 토큰을 발행할 수 있도록 허용합니다. API 토큰을 이용한 요청이 올 때, 토큰은 `Authorization` 헤더 안에 `Bearer` 토큰 형식으로 포함되어야 합니다.

To begin issuing tokens for users, your User model should use the `HasApiTokens` trait:

사용자에게 토큰 발행을 시작하기 위해서, 여러분은 User 모델에 `HasApiTokens` 트레잇(trait)을 use에 넣어야 합니다.

    use Laravel\Airlock\HasApiTokens;

    class User extends Authenticatable
    {
        use HasApiTokens, Notifiable;
    }

To issue a token, you may use the `createToken` method. The `createToken` method returns a `Laravel\Airlock\NewAccessToken` instance. API tokens are hashed using SHA-256 hashing before being stored in your database, but you may access the plain-text value of the token using the `plainTextToken` property of the `NewAccessToken` instance. You should display this value to the user immediately after the token has been created:

여러분은 토큰을 발행하기 위해서 `createToken` 메소드를 사용할지도 모릅니다. `createToken` 메소드는 `Laravel\Airlock\NewAccessToken` 인스턴스를 반환합니다. API 토큰은 여러분의 데이터베이스에 저장되기 전에 SHA-256 해싱을 이용해서 해시 되지만, `NewAccessToken` 인스턴스의 `plainTextToken` 속성을 이용해서 평문으로 액세스할 수 있습니다.

    $token = $user->createToken('token-name');

    return $token->plainTextToken;

You may access all of the user's tokens using the `tokens` Eloquent relationship provided by the `HasApiTokens` trait:

여러분은 `HasApiTokens` 트레잇이 제공하는 `tokens` 엘로퀀트 관계(relationship)를 이용해서 사용자의 토큰 전체에 액세스할 수 있습니다.

    foreach ($user->tokens as $token) {
        //
    }

<a name="token-abilities"></a>
### Token Abilities
### 토큰 기능

Airlock allows you to assign "abilities" to tokens, similar to OAuth "scopes". You may pass an array of string abilities as the second argument to the `createToken` method:

에어락은 토큰에 OAuth "스코프"와 유사한 "권한(abilities)"을 부여하도록 허용합니다. 여러분은 `createToken` 메소드의 두 번째 인자로 강력한 권한을 배열로 넘기게 됩니다.

    return $user->createToken('token-name', ['server:update'])->plainTextToken;

When handling an incoming request authenticated by Airlock, you may determine if the token has a given ability using the `tokenCan` method:

에어락에 의해 들어오는 요청 인증이 핸들링 될 때, 여러분은 `tokenCan` 메소드를 사용해서 그 토큰이 주어진 권한이 있는지를 판단합니다.

    if ($user->tokenCan('server:update')) {
        //
    }

> {tip} For convenience, the `tokenCan` method will always return `true` if the incoming authenticated request was from your first-party SPA and you are using Airlock's built-in [SPA authentication](#spa-authentication).

> {팁} 편의를 위해서, `tokenCan` 메소드는 들어오는 인증 요청이 여러분의 자사 SPA이고, 에어락 내장 [SPA 인증](#spa-authentication)을 사용한다면, 항상 `true`를 반환합니다.

<a name="protecting-routes"></a>
### Protecting Routes
### 라우트 보호하기

To protect routes so that all incoming requests must be authenticated, you should attach the `airlock` authentication guard to your API routes within your `routes/api.php` file. This guard will ensure that incoming requests are authenticated as either a stateful authenticated requests from your SPA or contain a valid API token header if the request is from a third party:

모든 들어오는 요청을 인증하여 라우트를 보호하기 위해서 여러분은 `airlock` 인증 가드를 `routes/api.php` 파일 안에 있는 API 라우트에 추가해야 합니다. 이 가드는 들어오는 요청이 SPA로부터 상태 저장(stateful) 인증 요청이 인증되었는지 혹은 요청이 제3자로부터 들어올 때 어떤 확인된 API 토큰 헤더를 가지고 있는지를 확인합니다.

    Route::middleware('auth:airlock')->get('/user', function (Request $request) {
        return $request->user();
    });

<a name="revoking-tokens"></a>
### Revoking Tokens
### 토큰 해지하기

You may "revoke" tokens by deleting them from your database using the `tokens` relationship that is provided by the `HasApiTokens` trait:

여러분은 `HasApiTokens` 트레잇을 넣어서 `tokens` 관계(relationship)을 이용하여 데이터베이스에서 그 값을 지움으로서 토큰을 해지합니다.

    // Revoke all tokens...
    $user->tokens()->delete();

    // Revoke a specific token...
    $user->tokens()->where('id', $id)->delete();

    // 모든 토큰 해지...
    $user->tokens()->delete();

    // 지정된 토큰 해지...
    $user->tokens()->where('id', $id)->delete();


<a name="spa-authentication"></a>
## SPA Authentication
## SPA 인증

Airlock exists to offer a simple way to authenticate single page applications (SPAs) that need to communicate with a Laravel powered API. These SPAs might exist in the same repository as your Laravel application or might be an entirely separate repository, such as a SPA created using Vue CLI.

에어락은 라라벨로 제작된 API와 통신이 필요한 싱글 페이지 애플리케이션(SPAs)을 인증하는 간단한 방법을 제공하기 위해서 만들어졌습니다. 이런 SPA들은 여러분의 라라벨 애플리케이션과 같은 저장소(repository)에 있을 수도 있고, Vue CLI를 이용에서 제작된 SPA와 같이 전체가 전혀 다른 저장소에 있을 수도 있습니다.

For this feature, Airlock does not use tokens of any kind. Instead, Airlock uses Laravel's built-in cookie based session authentication services. This provides the benefits of CSRF protection, session authentication, as well as protects against leakage of the authentication credentials via XSS. Airlock will only attempt to authenticate using cookies when the incoming request originates from your own SPA frontend.

이 기능을 위해서 에어락은 어떤 종류의 토큰도 사용하지 않습니다. 대신, 에어락은 라라벨에 내장(built-in) 된 쿠키 기반의 세션 인증 서비스를 사용합니다. 이것은 인증 CSRF 보호, 세션 인증이라는 장점과 더불어 XSS를 통한 인증 자격 증명(authentication credentials)의 누출을 방어합니다. 에어락은 단지 어떤 요청이 여러분 자신의 SPA 프론트엔드로부터 비롯될 때 쿠키를 사용한 인증을 시도할 것입니다.

<a name="spa-configuration"></a>
### Configuration
### 설정

#### Configuring Your First-Party Domains
#### 자사(First-Party) 도메인 설정

First, you should configure which domains your SPA will be making requests from. You may configure these domains using the `stateful` configuration option in your `airlock` configuration file. This configuration setting determines which domains will maintain "stateful" authentication using Laravel session cookies when making requests to your API.

우선, 여러분은 어떤 도메인에서 SPA가 요청을 보낼지를 설정해야 합니다. `airlock` 설정 파일 안에 `stateful` 설정 옵션을 이용해서 이들 도메인을 설정합니다. 이 설정은 어떤 도메인이 API에 요청에 대한 라라벨 세션 쿠키를 이용한 "상태 유지(stateful)" 인증 유지시킬지 결정합니다.

#### Airlock Middleware
#### 에어락 미들웨어

Next, you should add Airlock's middleware to your `api` middleware group within your `app/Http/Kernel.php` file. This middleware is responsible for ensuring that incoming requests from your SPA can authenticate using Laravel's session cookies, while still allowing requests from third parties or mobile applications to authenticate using API tokens:

다음으로, 여러분은 `app/Http/Kernel.php` 파일 내에 있는 `api` 미들웨어 그룹에 에어락 미들웨어를 넣어야 합니다. 이 미들웨어는 들어오는 요청이 여러분의 SPA가 라라벨 세션 쿠키를 이용해서 인증할 수 있는지 보장할 반면 여전히 제3자 혹은 모바일 애플리케이션으로부터 API 토큰을 사용한 인증된 요청을 여전히 이용할 책임이 있습니다.

    use Laravel\Airlock\Http\Middleware\EnsureFrontendRequestsAreStateful;

    'api' => [
        EnsureFrontendRequestsAreStateful::class,
        'throttle:60,1',
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
    ],

<a name="cors-and-cookies"></a>
#### CORS & Cookies
#### CORS 와 쿠키

If you are having trouble authenticating with your application from an SPA that executes on a separate subdomain, you have likely misconfigured your CORS (Cross-Origin Resource Sharing) or session cookie settings.

만약 SPA에서 분리된 서브도메인을 실행할 때 애플리케이션 인증에 문제를 겪는다면, CORS (Cross-Origin Resource Sharing) 혹은 세션 쿠키 세팅의 설정이 잘못되었을 것입니다.

You should ensure that your application's CORS configuration is returning the `Access-Control-Allow-Credentials` header with a value of `True` by setting the `supports_credentials` option within your application's `cors` configuration file to `true`.

여러분은 애플리케이션의 `cors` 설정 파일 안에 `supports_credentials` 옵션을 `true`로 세팅해서 `True` 값이 포함된 `Access-Control-Allow-Credentials` 헤더를 리턴하도록 애플리케이션의 CORS 설정을 확인해야 합니다.

In addition, you should enable the `withCredentials` option on your global `axios` instance. Typically, this should be performed in your `resources/js/bootstrap.js` file:

추가적으로, 여러분은 전역 `axios` 인스턴스의 `withCredentials` 옵션을 활성화해야 합니다. 일반적으로, 이것은 `resources/js/bootstrap.js` 파일 안에서 수행되어야 합니다.

    axios.defaults.withCredentials = true;

Finally, you should ensure your application's session cookie domain configuration supports any subdomain of your root domain. You may do this by prefixing the domain with a leading `.` within your `session` configuration file:

마지막으로, 여러분은 애플리케이션의 세션 쿠키 도메인 설정이 루트 도메인의 서브도메인을 지원하는지를 확인해야 합니다. `session` 설정 파일 안에 `.`으로 시작하는 접두사를 도메인을 붙임으로써 설정할 수 있습니다.

    'domain' => '.domain.com',

<a name="spa-authenticating"></a>
### Authenticating
### 인증하기

To authenticate your SPA, your SPA's login page should first make a request to the `/airlock/csrf-cookie` route to initialize CSRF protection for the application:

SPA를 인증하기 위해서는 SPA 로그인 페이지에서 `/airlock/csrf-cookie` 라우트를 가장 먼저 요청함으로써 애플리케이션을 위한 CSRF 보호(protection)를 초기화해야 합니다.

    axios.get('/airlock/csrf-cookie').then(response => {
        // Login...
    });

Once CSRF protection has been initialized, you should make a `POST` request to the typical Laravel `/login` route. This `/login` route may be provided by the `laravel/ui` [authentication scaffolding](/docs/{{version}}/authentication#introduction) package.

한번 CSRF 보호가 초기화되면, 여러분은 일반적으로  라라벨 `/login` 라우트에 `POST` 요청을 만들어야 합니다. `/login` 라우트는 `laravel/ui` [인증 스케폴딩](/docs/{{version}}/authentication#introduction) 패키지에서 제공합니다.

If the login request is successful, you will be authenticated and subsequent requests to your API routes will automatically be authenticated via the session cookie that the Laravel backend issued to your client.

만약 로그인 요청이 성공한다면 인증이 되었을 것이며, API 라우트로 보내는 다음 요청은 라라벨 백엔드에서 발행한 세션 쿠키를 통해 자동으로 인증됩니다.

> {tip} You are free to write your own `/login` endpoint; however, you should ensure that it authenticates the user using the standard, [session based authentication services that Laravel provides](/docs/{{version}}/authentication#authenticating-users).

> {팁} 여러분은 자신만의 `/login` 엔드포인트를 만드는 것은 자유입니다; 하지만, [라라벨이 제공하는 세션 기반의 인증 서비스](/docs/{{version}}/authentication#authenticating-users) 라는 표준을 사용해서 사용자를 확실하게 인증해야 합니다.

<a name="protecting-spa-routes"></a>
### Protecting Routes
### 라우트 보호하기

To protect routes so that all incoming requests must be authenticated, you should attach the `airlock` authentication guard to your API routes within your `routes/api.php` file. This guard will ensure that incoming requests are authenticated as either a stateful authenticated requests from your SPA or contain a valid API token header if the request is from a third party:

모든 들어오는 요청을 인증하여 라우트를 보호하기 위해서 여러분은 `airlock` 인증 가드를 `routes/api.php` 파일 안에 있는 API 라우트에 추가해야 합니다. 이 가드는 들어오는 요청이 SPA로부터 상태 저장(stateful) 인증 요청이 인증되었는지 혹은 요청이 제3자로부터 들어올 때 어떤 확인된 API 토큰 헤더를 가지고 있는지를 확인합니다.

    Route::middleware('auth:airlock')->get('/user', function (Request $request) {
        return $request->user();
    });

<a name="authorizing-private-broadcast-channels"></a>
### Authorizing Private Broadcast Channels
### 비공개 브로드캐스트 채널 승인하기

If your SPA needs to authenticate with [private / presence broadcast channels](/docs/{{version}}/broadcasting#authorizing-channels), you should place the `Broadcast::routes` method call within your `routes/api.php` file:

만약 당신의 SPA가 [비밀 / 공개 브로드캐스트 채널](/docs/{{version}}/broadcasting#authorizing-channels)로 인증될 필요가 있다면, `routes/api.php` 파일 내에 `Broadcast::routes` 메소드 콜(method call)을 넣어야 합니다.

    Broadcast::routes(['middleware' => ['auth:airlock']]);

Next, in order for Pusher's authorization requests to succeed, you will need to provide a custom Pusher `authorizer` when initializing [Laravel Echo](/docs/{{version}}/broadcasting#installing-laravel-echo). This allows your application to configure Pusher to use the `axios` instance that is [properly configured for cross-domain requests](#cors-and-cookies):

다음으로, 푸셔(Pusher) 인증 요청에 성공하기 위해서, 당신은 [라라벨 에코](/docs/{{version}}/broadcasting#installing-laravel-echo)를 초기화할 때, 커스텀 푸셔 `authorizer`를 제공해야 합니다.

    window.Echo = new Echo({
        broadcaster: "pusher",
        cluster: process.env.MIX_PUSHER_APP_CLUSTER,
        encrypted: true,
        key: process.env.MIX_PUSHER_APP_KEY,
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

<a name="mobile-application-authentication"></a>
## Mobile Application Authentication
## 모바일 애플리케이션 인증

You may use Airlock tokens to authenticate your mobile application's requests to your API. The process for authenticating mobile application requests is similar to authenticating third-party API requests; however, there are small differences in how you will issue the API tokens.

여러분은 모바일 애플리케이션의 API 요청을 인증하는 에어락 토큰을 사용할 수도 있습니다. 모바일 애플리케이션 요청을 인증하는 프로세스는 제3자 API 요청을 인증하는 것과 비슷합니다. 그러나, API 토큰을 어떻게 발행할 것인지에 대한 방법에 조금 차이가 있습니다.

<a name="issuing-mobile-api-tokens"></a>
### Issuing API Tokens
### API 토큰 발급하기

To get started, create a route that accepts the user's email / username, password, and device name, then exchanges those credentials for a new Airlock token. The endpoint will return the plain-text Airlock token which may then be stored on the mobile device and used to make additional API requests:

시작하려면, 사용자의 이메일 / 사용자 이름, 비밀번호 및 장치 이름을 허용하는 라우트를 만든 다음, 해당 자격 증명을 새로운 에어락 토큰으로 교환하십시오. 엔드포인트는 평문으로 된 에어락 토큰을 반환한 다음 모바일 장치에 저장하여 추가 API 요청을 수행하는 데 사용할 수 있습니다.

    use App\User;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Hash;
    use Illuminate\Validation\ValidationException;

    Route::post('/airlock/token', function (Request $request) {
        $request->validate([
            'email' => 'required|email',
            'password' => 'required',
            'device_name' => 'required'
        ]);

        $user = User::where('email', $request->email)->first();

        if (! $user || ! Hash::check($request->password, $user->password)) {
            throw ValidationException::withMessages([
                'email' => ['The provided credentials are incorrect.'],
            ]);
        }

        return $user->createToken($request->device_name)->plainTextToken;
    });

When the mobile device uses the token to make an API request to your application, it should pass the token in the `Authorization` header as a `Bearer` token.

모바일 디바이스가 토큰을 사용하여 애플리케이션에 API 요청을 하는 경우, `Authorization` 헤더에 `Bearer` 토큰으로 전달해야 합니다.

> {tip} When issuing tokens for a mobile application, you are also free to specify [token abilities](#token-abilities)


> {팁} 모바일 애플리케이션을 위한 토큰을 발행할 때 [토큰 기능](#token-abilities)를 자유롭게 지정할 수도 있습니다

<a name="protecting-mobile-api-routes"></a>
### Protecting Routes
### 라우트 보호하기

As previously documented, you may protect routes so that all incoming requests must be authenticated by attaching the `airlock` authentication guard to the routes. Typically, you will attach this guard to the routes defined within your `routes/api.php` file:

앞서 문서화 한 바와 같이, 경로에 `airlock` 인증 가드를 연결하여 들어오는 모든 요청을 인증해야 하는 경로를 보호할 수 있습니다. 일반적으로`routes/api.php` 파일에 정의된 라우트에 이 가드를 연결합니다:

    Route::middleware('auth:airlock')->get('/user', function (Request $request) {
        return $request->user();
    });

<a name="revoking-mobile-api-tokens"></a>
### Revoking Tokens
### 토큰 해지하기

To allow users to revoke API tokens issued to mobile devices, you may list them by name, along with a "Revoke" button, within an "account settings" portion of your web application's UI. When the user clicks the "Revoke" button, you can delete the token from the database. Remember, you can access a user's API tokens via the `tokens` relationship provided by the `HasApiTokens` trait:

사용자가 모바일 장치에 발급된 API 토큰을 해지할 수 있도록 웹 응용 프로그램 UI의 "계정 설정"부분에 "해지"버튼과 함께 이름별로 토큰을 나열할 수 있습니다. 사용자가 "해지"버튼을 클릭하면 데이터베이스에서 토큰을 삭제할 수 있습니다. `HasApiTokens` 트레잇이 제공하는`tokens`관계를 통해 사용자의 API 토큰에 액세스할 수 있습니다.

    // Revoke all tokens...
    $user->tokens()->delete();

    // Revoke a specific token...
    $user->tokens()->where('id', $id)->delete();

    // 모든 토큰 해지...
    $user->tokens()->delete();

    // 지정된 토큰 해지...
    $user->tokens()->where('id', $id)->delete();


<a name="testing"></a>
## Testing
## 테스트하기

While testing, the `Airlock::actingAs` method may be used to authenticate a user and specify which abilities are granted to their token:

테스트하는 동안 `Airlock::actingAs` 메소드를 사용하여 사용자를 인증하고 토큰에 부여할 기능을 지정할 수 있습니다.

    use App\User;
    use Laravel\Airlock\Airlock;

    public function test_task_list_can_be_retrieved()
    {
        Airlock::actingAs(
            factory(User::class)->create(),
            ['view-tasks']
        );

        $response = $this->get('/api/task');

        $response->assertOk();
    }

If you would like to grant all abilities to the token, you should include `*` in the ability list provided to the `actingAs` method:

모든 기능을 토큰에 부여하려면 `actingAs` 메소드에서 제공한 기능 목록에`*`를 포함시켜야 합니다.

    Airlock::actingAs(
        factory(User::class)->create(),
        ['*']
    );
