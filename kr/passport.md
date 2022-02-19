# Laravel Passport
# 라라벨 Passport

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [Passport Or Sanctum?](#passport-or-sanctum)
    - [Passport Or Sanctum?](#passport-or-sanctum)
- [Installation](#installation)
- [설치하기](#installation)
    - [Deploying Passport](#deploying-passport)
    - [Passport 배포하기](#deploying-passport)
    - [Migration Customization](#migration-customization)
    - [마이그레이션 커스터마이징](#migration-customization)
    - [Upgrading Passport](#upgrading-passport)
    - [Passport 업그레이드](#upgrading-passport)
- [Configuration](#configuration)
- [설정하기](#configuration)
    - [Client Secret Hashing](#client-secret-hashing)
    - [클라이언트 비밀키 해싱](#client-secret-hashing)
    - [Token Lifetimes](#token-lifetimes)
    - [토큰 지속시간](#token-lifetimes)
    - [Overriding Default Models](#overriding-default-models)
    - [기본모델 덮어쓰기](#overriding-default-models)
- [Issuing Access Tokens](#issuing-access-tokens)
- [엑세스 토큰 발급하기](#issuing-access-tokens)
    - [Managing Clients](#managing-clients)
    - [클라이언트 관리](#managing-clients)
    - [Requesting Tokens](#requesting-tokens)
    - [토큰 요청](#requesting-tokens)
    - [Refreshing Tokens](#refreshing-tokens)
    - [토큰 갱신하기](#refreshing-tokens)
    - [Revoking Tokens](#revoking-tokens)
    - [토큰 취소](#revoking-tokens)
    - [Purging Tokens](#purging-tokens)
    - [토큰 제거](#purging-tokens)
- [Authorization Code Grant with PKCE](#code-grant-pkce)
- [PKCE를 통한 인증 코드 부여](#code-grant-pkce)
    - [Creating The Client](#creating-a-auth-pkce-grant-client)
    - [클라이언트 생성](#creating-a-auth-pkce-grant-client)
    - [Requesting Tokens](#requesting-auth-pkce-grant-tokens)
    - [토큰 요청](#requesting-auth-pkce-grant-tokens)
- [Password Grant Tokens](#password-grant-tokens)
- [패스워드 Grant 토큰](#password-grant-tokens)
    - [Creating A Password Grant Client](#creating-a-password-grant-client)
    - [패스워드 Grant 클라이언트 생성하기](#creating-a-password-grant-client)
    - [Requesting Tokens](#requesting-password-grant-tokens)
    - [토큰 요청](#requesting-password-grant-tokens)
    - [Requesting All Scopes](#requesting-all-scopes)
    - [모든 범위에 대하여 요청하기](#requesting-all-scopes)
    - [Customizing The User Provider](#customizing-the-user-provider)
    - [사용자 공급자 커스터마이징](#customizing-the-user-provider)
    - [Customizing The Username Field](#customizing-the-username-field)
    - [사용자 이름 필드 사용자 정의하기](#customizing-the-username-field)
    - [Customizing The Password Validation](#customizing-the-password-validation)
    - [패스워드 유효성 검사 커스터마이징](#customizing-the-password-validation)
- [Implicit Grant Tokens](#implicit-grant-tokens)
- [묵시적 grant 토큰](#implicit-grant-tokens)
- [Client Credentials Grant Tokens](#client-credentials-grant-tokens)
- [클라이언트의 자격증명을 위한 Grant 토큰](#client-credentials-grant-tokens)
- [Personal Access Tokens](#personal-access-tokens)
- [개인용 엑세스 토큰](#personal-access-tokens)
    - [Creating A Personal Access Client](#creating-a-personal-access-client)
    - [개인용 엑세스 클라이언트 생성하기](#creating-a-personal-access-client)
    - [Managing Personal Access Tokens](#managing-personal-access-tokens)
    - [개인용 엑세스 토큰 관리하기](#managing-personal-access-tokens)
- [Protecting Routes](#protecting-routes)
- [라우트 보호하기](#protecting-routes)
    - [Via Middleware](#via-middleware)
    - [미들웨어를 통해서](#via-middleware)
    - [Passing The Access Token](#passing-the-access-token)
    - [엑세스 토큰 전달하기](#passing-the-access-token)
- [Token Scopes](#token-scopes)
- [토큰 스코프(범위)](#token-scopes)
    - [Defining Scopes](#defining-scopes)
    - [스코프 정의하기](#defining-scopes)
    - [Default Scope](#default-scope)
    - [기본 스코프](#default-scope)
    - [Assigning Scopes To Tokens](#assigning-scopes-to-tokens)
    - [토큰에 스코프 할당하기](#assigning-scopes-to-tokens)
    - [Checking Scopes](#checking-scopes)
    - [Scope 확인하기](#checking-scopes)
- [Consuming Your API With JavaScript](#consuming-your-api-with-javascript)
- [자바스크립트로 API 사용하기](#consuming-your-api-with-javascript)
- [Events](#events)
- [이벤트](#events)
- [Testing](#testing)
- [테스팅](#testing)

<a name="introduction"></a>
## Introduction
## 시작하기

[Laravel Passport](https://github.com/laravel/passport) provides a full OAuth2 server implementation for your Laravel application in a matter of minutes. Passport is built on top of the [League OAuth2 server](https://github.com/thephpleague/oauth2-server) that is maintained by Andy Millington and Simon Hamp.

[라라벨 Passport](https://github.com/laravel/passport) 는 몇 분 만에 라라벨 애플리케이션을 위한 전체 OAuth2 서버 구현을 제공합니다. Passport는 Andy Millington과 Simon Hamp가 관리하는 [League OAuth2 서버](https://github.com/thephpleague/oauth2-server) 위에 구축됩니다.

> {note} This documentation assumes you are already familiar with OAuth2. If you do not know anything about OAuth2, consider familiarizing yourself with the general [terminology](https://oauth2.thephpleague.com/terminology/) and features of OAuth2 before continuing.

> {note} 이 문서는 귀하가 이미 OAuth2에 익숙하다고 가정합니다. OAuth2에 대해 잘 모르는 경우 계속하기 전에 일반 [용어](https://oauth2.thephpleague.com/terminology/) 및 OAuth2의 기능을 숙지하십시오.

<a name="passport-or-sanctum"></a>
### Passport Or Sanctum?
### Passport Or Sanctum?

Before getting started, you may wish to determine if your application would be better served by Laravel Passport or [Laravel Sanctum](/docs/{{version}}/sanctum). If your application absolutely needs to support OAuth2, then you should use Laravel Passport.

시작하기 전에 애플리케이션이 라라벨 Passport 또는 [라라벨 Sanctum](/docs/{{version}}/sanctum) 에서 더 나은 서비스를 제공하는지 결정할 수 있습니다. 애플리케이션이 절대적으로 OAuth2를 지원해야, 하는 경우 Laravel Passport를 사용해야 합니다.

However, if you are attempting to authenticate a single-page application, mobile application, or issue API tokens, you should use [Laravel Sanctum](/docs/{{version}}/sanctum). Laravel Sanctum does not support OAuth2; however, it provides a much simpler API authentication development experience.

그러나, single-page 애플리케이션, 모바일 애플리케이션을 인증하거나 API 토큰을 발급하려는 경우 [라라벨 Sanctum](/docs/{{version}}/sanctum)을 사용해야 합니다. 라라벨 Sanctum은 OAuth2를 지원하지 않습니다. 그러나, 훨씬 더 간단한 API 인증 개발 경험을 제공합니다.

<a name="installation"></a>
## Installation
## 설치하기

To get started, install Passport via the Composer package manager:

컴포저를 통해서 Passport를 설치하는 것부터 시작해보겠습니다.

    composer require laravel/passport

Passport's [service provider](/docs/{{version}}/providers) registers its own database migration directory, so you should migrate your database after installing the package. The Passport migrations will create the tables your application needs to store OAuth2 clients and access tokens:

Passport의 [서비스 프로바이더](/docs/{{version}}/providers)는 고유한 데이터베이스 마이그레이션 디렉터리를 등록하므로 패키지를 설치한 후 데이터베이스를 마이그레이션해야 합니다. Passport 마이그레이션을 실행하면 애플리케이션이 OAuth2 클라이언트 및 액세스 토큰을 저장하는 데 필요한 테이블을 생성합니다.

    php artisan migrate

Next, you should execute the `passport:install` Artisan command. This command will create the encryption keys needed to generate secure access tokens. In addition, the command will create "personal access" and "password grant" clients which will be used to generate access tokens:

다음으로 `passport:install` 아티산 명령어를 실행해야 합니다. 이 명령어는 보안 액세스 토큰을 생성하는 데 필요한 암호화 키를 생성합니다. 또한 이 명령어는 액세스 토큰을 생성하는 데 사용할 "개인 액세스(personal access)" 및 "암호 부여(password grant)" 클라이언트를 생성합니다.

    php artisan passport:install

> {tip} If you would like to use UUIDs as the primary key value of the Passport `Client` model instead of auto-incrementing integers, please install Passport using [the `uuids` option](#client-uuids).

> {tip} 자동으로 증가하는 정수 대신 Passport `Client` 모델의 기본 키 값으로 UUID를 사용하려면 [`uuids` 옵션](#client-uuids)를 사용하여 Passport를 설치하십시오.

After running the `passport:install` command, add the `Laravel\Passport\HasApiTokens` trait to your `App\Models\User` model. This trait will provide a few helper methods to your model which allow you to inspect the authenticated user's token and scopes. If your model is already using the `Laravel\Sanctum\HasApiTokens` trait, you may remove that trait:

`passport:install` 명령어를 실행한 후에, `App\Models\User` 모델에 `Laravel\Passport\HasApiTokens` 트레이트-trait 를 추가하십시오. 이 트레이트-trait는 모델에 인증된 사용자의 토큰과 범위를 확인하기 위한 몇가지 헬퍼 메소드를 제공합니다. 모델이 이미 `Laravel\Sanctum\HasApiTokens` 트레이트-trait를 사용하고 있다면 해당 특성을 제거할 수 있습니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Factories\HasFactory;
    use Illuminate\Foundation\Auth\User as Authenticatable;
    use Illuminate\Notifications\Notifiable;
    use Laravel\Passport\HasApiTokens;

    class User extends Authenticatable
    {
        use HasApiTokens, HasFactory, Notifiable;
    }

Next, you should call the `Passport::routes` method within the `boot` method of your `App\Providers\AuthServiceProvider`. This method will register the routes necessary to issue access tokens and revoke access tokens, clients, and personal access tokens:

다음으로, `App\Providers\AuthServiceProvider` 의 `boot` 메소드에서 `Passport::routes` 메소드를 호출해야 합니다. 이 메소드는 엑세스 토큰을 발급하는 라우트와 엑세스 토큰, 클라이언트 그리고 개인용 엑세스 토큰을 해제하는 라우트를 등록합니다.

    <?php

    namespace App\Providers;

    use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;
    use Illuminate\Support\Facades\Gate;
    use Laravel\Passport\Passport;

    class AuthServiceProvider extends ServiceProvider
    {
        /**
         * The policy mappings for the application.
         *
         * @var array
         */
        protected $policies = [
            'App\Models\Model' => 'App\Policies\ModelPolicy',
        ];

        /**
         * Register any authentication / authorization services.
         *
         * @return void
         */
        public function boot()
        {
            $this->registerPolicies();

            if (! $this->app->routesAreCached()) {
                Passport::routes();
            }
        }
    }

Finally, in your application's `config/auth.php` configuration file, you should set the `driver` option of the `api` authentication guard to `passport`. This will instruct your application to use Passport's `TokenGuard` when authenticating incoming API requests:

마지막으로, 애플리케이션의 `config/auth.php` 설정 파일에서 guard `api` 인증 `driver` 옵션을 `passport` 로 변경해야 합니다. 이렇게 하면, 인증 API request이 유입될 때 애플리케이션이 Passport의 `TokenGuard` 를 사용합니다.

    'guards' => [
        'web' => [
            'driver' => 'session',
            'provider' => 'users',
        ],

        'api' => [
            'driver' => 'passport',
            'provider' => 'users',
        ],
    ],

<a name="client-uuids"></a>
#### Client UUIDs
#### Client UUIDs

You may also run the `passport:install` command with the `--uuids` option present. This option will instruct Passport that you would like to use UUIDs instead of auto-incrementing integers as the Passport `Client` model's primary key values. After running the `passport:install` command with the `--uuids` option, you will be given additional instructions regarding disabling Passport's default migrations:

`--uuids` 옵션이 있는 상태에서 `passport:install` 명령을 실행할 수 있습니다. 이 옵션은 Passport `Client` 모델의 기본 키 값으로 정수를 자동 증가시키는 대신 UUID를 사용하도록 Passport에 지시합니다. `--uuids` 옵션과 함께 `passport:install` 명령을 실행하면 Passport의 기본 마이그레이션 비활성화에 대한 추가 지침이 제공됩니다.

    php artisan passport:install --uuids

<a name="deploying-passport"></a>
### Deploying Passport
### Passport 배포하기

When deploying Passport to your application's servers for the first time, you will likely need to run the `passport:keys` command. This command generates the encryption keys Passport needs in order to generate access tokens. The generated keys are not typically kept in source control:

Passport를 애플리케이션 서버에 맨 처음 배포할 때, `passport:keys` 명령어가 필요할 수 있을것입니다. 이 명령어는 Passport가 액세스 토큰을 생성하는 데 필요한 암호화 키를 생성합니다. 생성 된 키는 일반적으로 소스 컨트롤에 유지되지 않습니다.

    php artisan passport:keys

If necessary, you may define the path where Passport's keys should be loaded from. You may use the `Passport::loadKeysFrom` method to accomplish this. Typically, this method should be called from the `boot` method of your application's `App\Providers\AuthServiceProvider` class:

필요한 경우 Passport의 키를 로드해야 하는 경로를 정의 할 수 있습니다. 이를 실행하기 위해 `Passport::loadKeysFrom` 메소드를 사용할 수 있습니다. 일반적으로 이 메소드는 애플리케이션의 `App\Providers\AuthServiceProvider` 클래스의 `boot` 메소드에서 호출해야 합니다.

    /**
     * Register any authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();

        Passport::routes();

        Passport::loadKeysFrom(__DIR__.'/../secrets/oauth');
    }

<a name="loading-keys-from-the-environment"></a>
#### Loading Keys From The Environment
#### 환경변수에서 Key 로드하기

Alternatively, you may publish Passport's configuration file using the `vendor:publish` Artisan command:

대안으로, `vendor:publish` 아티산 명령어을 사용하여 Passport의 설정 파일을 사용할 수 있습니다.

    php artisan vendor:publish --tag=passport-config

After the configuration file has been published, you may load your application's encryption keys by defining them as environment variables:

설정 파일 이후 출시 되었다면, 환경 변수로 정의하여 애플리케이션의 암호화 키를 어플리케이션에서 로드할 수 있습니다.

```bash
PASSPORT_PRIVATE_KEY="-----BEGIN RSA PRIVATE KEY-----
<private key here>
-----END RSA PRIVATE KEY-----"

PASSPORT_PUBLIC_KEY="-----BEGIN PUBLIC KEY-----
<public key here>
-----END PUBLIC KEY-----"
```

<a name="migration-customization"></a>
### Migration Customization
### 마이그레이션 커스터마이징

If you are not going to use Passport's default migrations, you should call the `Passport::ignoreMigrations` method in the `register` method of your `App\Providers\AppServiceProvider` class. You may export the default migrations using the `vendor:publish` Artisan command:

Passport의 기본 마이그레이션을 사용하지 않으려면, `App\Providers\AppServiceProvider` 클래스의 `register` 메소드에서 `Passport::ignoreMigrations` 메소드를 호출해야 합니다. `vendor:publish` 아티산 명령어를 사용하여 기본 마이그레이션을 내보낼 수 있습니다.

    php artisan vendor:publish --tag=passport-migrations

<a name="upgrading-passport"></a>
### Upgrading Passport
### Passport 업그레이드

When upgrading to a new major version of Passport, it's important that you carefully review [the upgrade guide](https://github.com/laravel/passport/blob/master/UPGRADE.md).

Passport의 새로운 메이저 버전으로 업그레이드할 때, [업그레이드 가이드](https://github.com/laravel/passport/blob/master/UPGRADE.md) 를 검토하는 것이 중요합니다.

<a name="configuration"></a>
## Configuration
## 설정하기

<a name="client-secret-hashing"></a>
### Client Secret Hashing
### 클라이언트 비밀키 해싱

If you would like your client's secrets to be hashed when stored in your database, you should call the `Passport::hashClientSecrets` method in the `boot` method of your `App\Providers\AuthServiceProvider` class:

클라이언트의 비밀키가 데이터베이스에 저장 될 때 해시되기를 원한다면 `App\Providers\AuthServiceProvider` 클래스의 `boot` 메소드에서 `Passport::hashClientSecrets` 메소드를 호출해야합니다.

    use Laravel\Passport\Passport;

    Passport::hashClientSecrets();

Once enabled, all of your client secrets will only be displayable to the user immediately after they are created. Since the plain-text client secret value is never stored in the database, it is not possible to recover the secret's value if it is lost.

활성화되면 모든 클라이언트 비밀키가 생성된 이후에만 사용자에게 표시될 수 있습니다. 일반 플레인-텍스트 클라이언트 비밀키는 데이터베이스에 저장되지 않으므로 시크릿 값이 손상된 경우 시크릿 값을 복구할 수 없습니다.

<a name="token-lifetimes"></a>
### Token Lifetimes
### 토큰 지속시간

By default, Passport issues long-lived access tokens that expire after one year. If you would like to configure a longer / shorter token lifetime, you may use the `tokensExpireIn`, `refreshTokensExpireIn`, and `personalAccessTokensExpireIn` methods. These methods should be called from the `boot` method of your application's `App\Providers\AuthServiceProvider` class:

기본적으로 Passport는 엑세스 토큰을 오랫동안 유지합니다.(일년 후에 만료됩니다) 토큰의 지속시간을 늘리거나 줄이려면 `tokensExpireIn`, `refreshTokensExpireIn` 그리고 `personalAccessTokensExpireIn` 메서드를 사용할 수 있습니다. 이러한 메소드는 애플리케이션의 `App\Providers\AuthServiceProvider` 클래스의 `boot` 메소드에서 호출해야 합니다.

    /**
     * Register any authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();

        Passport::routes();

        Passport::tokensExpireIn(now()->addDays(15));
        Passport::refreshTokensExpireIn(now()->addDays(30));
        Passport::personalAccessTokensExpireIn(now()->addMonths(6));
    }

> {note} The `expires_at` columns on Passport's database tables are read-only and for display purposes only. When issuing tokens, Passport stores the expiration information within the signed and encrypted tokens. If you need to invalidate a token you should [revoke it](#revoking-tokens).

> {note} Passport 데이터베이스 테이블의 `expires_at` 컬럼은 읽기 전용이며 표시 전용입니다. 토큰을 발행 할 때 Passport는 서명되고 암호화 된 토큰 내에 만료 정보를 저장합니다. 토큰을 무효화해야하는 경우 [취소](#revoking-tokens)해야 합니다.

<a name="overriding-default-models"></a>
### Overriding Default Models
### 기본모델 오버라이딩

You are free to extend the models used internally by Passport by defining your own model and extending the corresponding Passport model:

고유한 모델을 정의하고 해당 Passport 모델을 확장하여 Passport가 내부적으로 사용하는 모델을 자유롭게 확장할 수 있습니다.

    use Laravel\Passport\Client as PassportClient;

    class Client extends PassportClient
    {
        // ...
    }

After defining your model, you may instruct Passport to use your custom model via the `Laravel\Passport\Passport` class. Typically, you should inform Passport about your custom models in the `boot` method of your application's `App\Providers\AuthServiceProvider` class:

모델을 정의한 후 `Laravel\Passport\Passport` 클래스를 통해 커스텀 모델을 사용하도록 Passport에 지시할 수 있습니다. 일반적으로 애플리케이션의 `App\Providers\AuthServiceProvider` 클래스의 `boot` 메소드에서 커스텀 모델에 대해 Passport에 알려줘야 합니다.
 
    use App\Models\Passport\AuthCode;
    use App\Models\Passport\Client;
    use App\Models\Passport\PersonalAccessClient;
    use App\Models\Passport\Token;

    /**
     * Register any authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();

        Passport::routes();

        Passport::useTokenModel(Token::class);
        Passport::useClientModel(Client::class);
        Passport::useAuthCodeModel(AuthCode::class);
        Passport::usePersonalAccessClientModel(PersonalAccessClient::class);
    }

<a name="issuing-access-tokens"></a>
## Issuing Access Tokens
## 엑세스 토큰 발급하기

Using OAuth2 via authorization codes is how most developers are familiar with OAuth2. When using authorization codes, a client application will redirect a user to your server where they will either approve or deny the request to issue an access token to the client.

승인 코드를 통해 OAuth2를 사용하는 것은 대부분의 개발자가 OAuth2를 사용하는 가장 익숙한 방식입니다. 승인 코드를 사용할 때, 클라이언트 애플리케이션은 사용자를 서버로 리다이렉션하여, 클라이언트에 대한 액세스 토큰을 발급하는 요청-request를 승인하거나, 거부하게 됩니다.

<a name="managing-clients"></a>
### Managing Clients
### 클라이언트 관리

First, developers building applications that need to interact with your application's API will need to register their application with yours by creating a "client". Typically, this consists of providing the name of their application and a URL that your application can redirect to after users approve their request for authorization.

먼저, 애플리케이션의 API와 인터렉션을 해야하는 애플리케이션을 작성하는 개발자는 하나의 "클라이언트"를 만들어 애플리케이션에 등록해야 합니다. 일반적으로 이것은 애플리케이션의 이름과 사용자가 reqeust을 승인한 뒤에 애플리케이션이 리다렉션 할 수 있는 URL을 제공하여 구성합니다.

<a name="the-passportclient-command"></a>
#### The `passport:client` Command
#### `passport:client` 명령어

The simplest way to create a client is using the `passport:client` Artisan command. This command may be used to create your own clients for testing your OAuth2 functionality. When you run the `client` command, Passport will prompt you for more information about your client and will provide you with a client ID and secret:

클라이언트를 만드는 가장 간단한 방법은 `passport:client` 아티산 명령어를 사용하는 것입니다. 이 명령어는 OAuth2 기능을 테스트하는 여러분의 고유한 클라이언트를 생성하는데 사용될 수 있습니다. 여러분이 `client` 명령어를 실행하면, Passport는 클리이언트에 대한 보다 자세한 정보를 물어보는 메세지를 표시하고 클라이언트의 ID 와 암호를 제공합니다.

    php artisan passport:client

**Redirect URLs**

**리다이렉션-Redirect URLs**

If you would like to allow multiple redirect URLs for your client, you may specify them using a comma-delimited list when prompted for the URL by the `passport:client` command. Any URLs which contain commas should be URL encoded:

클라이언트에 여러 개의 리다이렉션 URL을 허용하려면 `passport:client` 명령으로 URL을 입력하라는 메시지가 표시될 때 쉼표로 구분된 목록을 사용하여 지정할 수 있습니다. 쉼표가 포함된 모든 URL은 URL로 인코딩되어야 합니다.

```bash
http://example.com/callback,http://examplefoo.com/callback
```

<a name="clients-json-api"></a>
#### JSON API
#### JSON API

Since your application's users will not be able to utilize the `client` command, Passport provides a JSON API that you may use to create clients. This saves you the trouble of having to manually code controllers for creating, updating, and deleting clients.

애플리케이션의 사용자는 `client` 명령어를 사용할 수 없기 때문에, Passport는 클라이언트를 생성하는데 사용할 수 있는 JSON API를 제공합니다. 이렇게 하면 클라이언트를 생성, 업데이트 및 삭제하기를 하기 위해 컨트롤러를 수동으로 코딩해야 하는 문제를 줄일 수 있습니다.

However, you will need to pair Passport's JSON API with your own frontend to provide a dashboard for your users to manage their clients. Below, we'll review all of the API endpoints for managing clients. For convenience, we'll use [Axios](https://github.com/axios/axios) to demonstrate making HTTP requests to the endpoints.

그렇지만, Passport JSON API에 대응하는 대시보드를 통해서 사용자가 클라이언트를 관리할 수 있도록 프론트 엔드를 구성해야 합니다. 아래에서, 클라이언트를 관리하는 모든 API 엔드포인트를 확인해보겠습니다. 편의를 위해서, 엔드 포인트에 대한 HTTP request-요청을 만드는 데모에는 [Axios](https://github.com/axios/axios)를 사용하겠습니다.

The JSON API is guarded by the `web` and `auth` middleware; therefore, it may only be called from your own application. It is not able to be called from an external source.

JSON API는 `web` 및 `auth` 미들웨어에 의해 보호됩니다. 따라서 자신의 애플리케이션에서만 호출 할 수 있습니다. 외부 소스에서는 호출 할 수 없습니다.


<a name="get-oauthclients"></a>
#### `GET /oauth/clients`
#### `GET /oauth/clients`

This route returns all of the clients for the authenticated user. This is primarily useful for listing all of the user's clients so that they may edit or delete them:

이 라우트는 인증된 사용자의 모든 클라이언트들을 반환합니다. 이는 주로 사용자의 모든 클라이언트 목록을 표시하고 수정이나 삭제하고자 할 때 유용합니다.

    axios.get('/oauth/clients')
        .then(response => {
            console.log(response.data);
        });

<a name="post-oauthclients"></a>
#### `POST /oauth/clients`
#### `POST /oauth/clients`

This route is used to create new clients. It requires two pieces of data: the client's `name` and a `redirect` URL. The `redirect` URL is where the user will be redirected after approving or denying a request for authorization.

이 라우트는 새로운 클라이언트를 생성하는데 사용됩니다. 여기에는 두개의 데이터가 필요합니다. 클라이언트의 `name`과 한개의 `redirect` URL입니다. `redirect` URL은 request-요청에 대한 접근이 승인 또는 거부된 뒤에 사용자가 리다이렉션 되는 곳입니다.

When a client is created, it will be issued a client ID and client secret. These values will be used when requesting access tokens from your application. The client creation route will return the new client instance:

클라이언트가 생성되면, 클라이언트의 ID 와 암호키가 발급됩니다. 이 정보들은 애플리케이션에서 엑세스 토큰을 요청할 때 사용됩니다. 클라이언트 생성 라우트는 그 결과로 새로운 클라이언트의 인스턴스를 반환합니다.

    const data = {
        name: 'Client Name',
        redirect: 'http://example.com/callback'
    };

    axios.post('/oauth/clients', data)
        .then(response => {
            console.log(response.data);
        })
        .catch (response => {
            // List errors on response...
        });

<a name="put-oauthclientsclient-id"></a>
#### `PUT /oauth/clients/{client-id}`
#### `PUT /oauth/clients/{client-id}`

This route is used to update clients. It requires two pieces of data: the client's `name` and a `redirect` URL. The `redirect` URL is where the user will be redirected after approving or denying a request for authorization. The route will return the updated client instance:

이 라우트는 클라이언트를 수정하는데 사용됩니다. 여기에는 두개의 데이터가 필요합니다. 클라이언트의 `name`과 한개의 `redirect` URL입니다. `redirect` URL은 request-요청에 대한 접근이 승인 또는 거부된 뒤에 사용자가 리다이렉션 되는 곳입니다. 이 라우트는 수정된 클라이언트의 인스턴스를 반환합니다.

    const data = {
        name: 'New Client Name',
        redirect: 'http://example.com/callback'
    };

    axios.put('/oauth/clients/' + clientId, data)
        .then(response => {
            console.log(response.data);
        })
        .catch (response => {
            // List errors on response...
        });

<a name="delete-oauthclientsclient-id"></a>
#### `DELETE /oauth/clients/{client-id}`
#### `DELETE /oauth/clients/{client-id}`

This route is used to delete clients:

이 라우트는 클라이언트를 삭제하는 데 사용됩니다.

    axios.delete('/oauth/clients/' + clientId)
        .then(response => {
            //
        });

<a name="requesting-tokens"></a>
### Requesting Tokens
### 토큰 요청

<a name="requesting-tokens-redirecting-for-authorization"></a>
#### Redirecting For Authorization
#### 권한승인을 위한 리다이렉팅

Once a client has been created, developers may use their client ID and secret to request an authorization code and access token from your application. First, the consuming application should make a redirect request to your application's `/oauth/authorize` route like so:

클라이언트가 생성되고 나서, 개발자는 클라이언트 ID와 암호키를를 사용하여 권한 승인을 요청하고 애플리케이션의 토큰에 엑세스 할 수 있습니다. 먼저 API를 사용하는 애플리케이션이 다음의 `/oauth/authorize` 라우트로 리다이렉트 요청을 하도록 만들어야 합니다.

    use Illuminate\Http\Request;
    use Illuminate\Support\Str;

    Route::get('/redirect', function (Request $request) {
        $request->session()->put('state', $state = Str::random(40));

        $query = http_build_query([
            'client_id' => 'client-id',
            'redirect_uri' => 'http://third-party-app.com/callback',
            'response_type' => 'code',
            'scope' => '',
            'state' => $state,
        ]);

        return redirect('http://passport-app.test/oauth/authorize?'.$query);
    });

> {tip} Remember, the `/oauth/authorize` route is already defined by the `Passport::routes` method. You do not need to manually define this route.

> {tip} `/oauth/authorize` 라우트는 `Passport::routes` 메소드에서 이미 정의되어 있습니다. 이 라우트를 수동으로 등록할 필요가 없습니다.

<a name="approving-the-request"></a>
#### Approving The Request
#### Request-요청 승인

When receiving authorization requests, Passport will automatically display a template to the user allowing them to approve or deny the authorization request. If they approve the request, they will be redirected back to the `redirect_uri` that was specified by the consuming application. The `redirect_uri` must match the `redirect` URL that was specified when the client was created.

권한 승인 요청을 받으면, Passport는 자동으로 사용자가 템플릿을 표시하여 승인 요청을 수락하거나 거부할 수 있게 합니다. 요청이 승인되면, 애플리케이션에 의해서 지정된 `redirect_uri` 로 리다이렉션 됩니다. `redirect_uri` 는 클라이언트가 생성될 때 지정되었던 `redirect` URL과 일치해야 합니다.

If you would like to customize the authorization approval screen, you may publish Passport's views using the `vendor:publish` Artisan command. The published views will be placed in the `resources/views/vendor/passport` directory:

권한 승인 화면을 사용자가 커스터마이징을 하고싶다면, `vendor:publish` 아티산 명령어를 사용하여 Passport의 뷰-View 를 퍼블리싱할 수 있습니다. 퍼블리싱된 뷰파일은 `resources/views/vendor/passport` 디렉토리에 위치합니다.

    php artisan vendor:publish --tag=passport-views

Sometimes you may wish to skip the authorization prompt, such as when authorizing a first-party client. You may accomplish this by [extending the `Client` model](#overriding-default-models) and defining a `skipsAuthorization` method. If `skipsAuthorization` returns `true` the client will be approved and the user will be redirected back to the `redirect_uri` immediately:

때로는 자체 클라이언트에 권한 부여 할 때와 같이 권한 부여 입력창를 건너 뛰고 싶을 수 있습니다. [`Client` 모델](#overriding-default-models)을 확장하고 `skipsAuthorization` 메소드를 정의하여 이를 수행 할 수 있습니다. `skipsAuthorization`가 `true`를 반환하면 클라이언트는 승인되고 즉시 `redirect_uri`로 리다이렉션됩니다.

    <?php

    namespace App\Models\Passport;

    use Laravel\Passport\Client as BaseClient;

    class Client extends BaseClient
    {
        /**
         * Determine if the client should skip the authorization prompt.
         *
         * @return bool
         */
        public function skipsAuthorization()
        {
            return $this->firstParty();
        }
    }

<a name="requesting-tokens-converting-authorization-codes-to-access-tokens"></a>
#### Converting Authorization Codes To Access Tokens
#### 승인 코드를 엑세스 토큰으로 변환하기

If the user approves the authorization request, they will be redirected back to the consuming application. The consumer should first verify the `state` parameter against the value that was stored prior to the redirect. If the state parameter matches then the consumer should issue a `POST` request to your application to request an access token. The request should include the authorization code that was issued by your application when the user approved the authorization request:

사용자가 인증 요청을 승인하면, 해당 인증을 사용 할 애플리케이션으로 다시 리다이렉션됩니다. 사용자는 먼저 리다이렉션 이전에 저장된 값에 대해 `state` 파라미터를 확인해야 합니다. `state` 파라미터가 일치하면 사용자는 액세스 토큰을 요청하기 위해 애플리케이션에 'POST' 요청을 보내야 합니다. 요청에는 사용자가 인증 요청을 승인할 때 애플리케이션에서 발행한 승인 코드가 포함되어야 합니다.

    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Http;

    Route::get('/callback', function (Request $request) {
        $state = $request->session()->pull('state');

        throw_unless(
            strlen($state) > 0 && $state === $request->state,
            InvalidArgumentException::class
        );

        $response = Http::asForm()->post('http://passport-app.test/oauth/token', [
            'grant_type' => 'authorization_code',
            'client_id' => 'client-id',
            'client_secret' => 'client-secret',
            'redirect_uri' => 'http://third-party-app.com/callback',
            'code' => $request->code,
        ]);

        return $response->json();
    });

This `/oauth/token` route will return a JSON response containing `access_token`, `refresh_token`, and `expires_in` attributes. The `expires_in` attribute contains the number of seconds until the access token expires.

`/oauth/token` 라우트는 `access_token`, `refresh_token`, 그리고 `expires_in`을 포함하는 JSON 응답-response를 반환합니다. `expires_in` 속성은 엑세스 토큰이 만료되기까지의 (초)를 가지고 있습니다.

> {tip} Like the `/oauth/authorize` route, the `/oauth/token` route is defined for you by the `Passport::routes` method. There is no need to manually define this route.

> {tip} `/oauth/authorize` 라우트와 같이 `/oauth/token` 라우트는 `Passport::routes` 메소드에 의해서 정의됩니다. 이 라우트를 수동으로 등록할 필요가 없습니다.

<a name="tokens-json-api"></a>
#### JSON API
#### JSON API

Passport also includes a JSON API for managing authorized access tokens. You may pair this with your own frontend to offer your users a dashboard for managing access tokens. For convenience, we'll use [Axios](https://github.com/mzabriskie/axios) to demonstrate making HTTP requests to the endpoints. The JSON API is guarded by the `web` and `auth` middleware; therefore, it may only be called from your own application.

Passport에는 승인 된 액세스 토큰을 관리하기위한 JSON API도 포함되어 있습니다. 이를 자체 프런트엔드와 페어링하여 사용자에게 액세스 토큰 관리를 위한 대시 보드를 제공 할 수 있습니다. 편의를 위해 [Axios](https://github.com/mzabriskie/axios)를 사용하여 엔드포인트에 대한 HTTP 요청을 시연합니다. JSON API는 `web` 및 `auth` 미들웨어에 의해 보호됩니다. 따라서 자신의 애플리케이션에서만 호출 할 수 있습니다.

<a name="get-oauthtokens"></a>
#### `GET /oauth/tokens`
#### `GET /oauth/tokens`

This route returns all of the authorized access tokens that the authenticated user has created. This is primarily useful for listing all of the user's tokens so that they can revoke them:

이 라우트는 인증 된 사용자가 생성 한 모든 승인 된 액세스 토큰을 반환합니다. 이는 주로 사용자의 모든 토큰을 나열하여 취소 할 수 있도록하는 데 유용합니다.

    axios.get('/oauth/tokens')
        .then(response => {
            console.log(response.data);
        });

<a name="delete-oauthtokenstoken-id"></a>
#### `DELETE /oauth/tokens/{token-id}`
#### `DELETE /oauth/tokens/{token-id}`

This route may be used to revoke authorized access tokens and their related refresh tokens:

이 라우트는 승인 된 액세스 토큰 및 관련 새로 고침 토큰을 취소하는 데 사용할 수 있습니다.

    axios.delete('/oauth/tokens/' + tokenId);

<a name="refreshing-tokens"></a>
### Refreshing Tokens
### 토큰 갱신하기

If your application issues short-lived access tokens, users will need to refresh their access tokens via the refresh token that was provided to them when the access token was issued:

애플리케이션에서 사용기간이 짧은 엑세스 토큰을 발급한다면, 사용자는 엑세스 토큰을 발급할 때 제공된 리프레쉬 토큰을 사용하여 엑세스 토큰을 새롭게 갱신해야 합니다.

    use Illuminate\Support\Facades\Http;

    $response = Http::asForm()->post('http://passport-app.test/oauth/token', [
        'grant_type' => 'refresh_token',
        'refresh_token' => 'the-refresh-token',
        'client_id' => 'client-id',
        'client_secret' => 'client-secret',
        'scope' => '',
    ]);

    return $response->json();

This `/oauth/token` route will return a JSON response containing `access_token`, `refresh_token`, and `expires_in` attributes. The `expires_in` attribute contains the number of seconds until the access token expires.

`/oauth/token` 라우트는 `access_token`, `refresh_token`, 그리고 `expires_in`을 포함하는 JSON 응답-response를 반환합니다. `expires_in` 속성에는 엑세스 토큰이 만료되기까지의 (초)를 가지고 있습니다.

<a name="revoking-tokens"></a>
### Revoking Tokens
### 토큰 취소

You may revoke a token by using the `revokeAccessToken` method on the `Laravel\Passport\TokenRepository`. You may revoke a token's refresh tokens using the `revokeRefreshTokensByAccessTokenId` method on the `Laravel\Passport\RefreshTokenRepository`. These classes may be resolved using Laravel's [service container](/docs/{{version}}/container):

`Laravel\Passport\TokenRepository`에서 `revokeAccessToken` 메소드를 사용하여 토큰을 취소할 수 있습니다. `Laravel\Passport\RefreshTokenRepository`의 `revokeRefreshTokensByAccessTokenId` 메소드를 사용하여 토큰의 갱신 토큰을 취소할 수 있습니다. 이러한 클래스는 Laravel의 [서비스 컨테이너](/docs/{{version}}/container)를 사용하여 해결할 수 있습니다.

    use Laravel\Passport\TokenRepository;
    use Laravel\Passport\RefreshTokenRepository;

    $tokenRepository = app(TokenRepository::class);
    $refreshTokenRepository = app(RefreshTokenRepository::class);

    // Revoke an access token...
    $tokenRepository->revokeAccessToken($tokenId);

    // Revoke all of the token's refresh tokens...
    $refreshTokenRepository->revokeRefreshTokensByAccessTokenId($tokenId);

<a name="purging-tokens"></a>
### Purging Tokens
### 토큰 제거

When tokens have been revoked or expired, you might want to purge them from the database. Passport's included `passport:purge` Artisan command can do this for you:

토큰이 취소되거나 만료되면 데이터베이스에서 제거 할 수 있습니다. Passport에 포함된 `passport:purge` 아티산 명령어는 다음을 수행할 수 있습니다.

    # Purge revoked and expired tokens and auth codes...
    php artisan passport:purge

    # Only purge revoked tokens and auth codes...
    php artisan passport:purge --revoked

    # Only purge expired tokens and auth codes...
    php artisan passport:purge --expired

You may also configure a [scheduled job](/docs/{{version}}/scheduling) in your application's `App\Console\Kernel` class to automatically prune your tokens on a schedule:

애플리케이션의 `App\Console\Kernel` 클래스에서 [스케줄 된 job](/docs/{{version}}/scheduling)을 설정하여 스케줄에 따라 토큰을 자동으로 정리할 수도 있습니다.

    /**
     * Define the application's command schedule.
     *
     * @param  \Illuminate\Console\Scheduling\Schedule  $schedule
     * @return void
     */
    protected function schedule(Schedule $schedule)
    {
        $schedule->command('passport:purge')->hourly();
    }

<a name="code-grant-pkce"></a>
## Authorization Code Grant with PKCE
## PKCE를 통한 인증 코드 부여

The Authorization Code grant with "Proof Key for Code Exchange" (PKCE) is a secure way to authenticate single page applications or native applications to access your API. This grant should be used when you can't guarantee that the client secret will be stored confidentially or in order to mitigate the threat of having the authorization code intercepted by an attacker. A combination of a "code verifier" and a "code challenge" replaces the client secret when exchanging the authorization code for an access token.

"PKCE(Proof Key for Code Exchange)"가 포함 된 인증 코드 부여는 SPA(single page applications) 또는 네이티브 애플리케이션이 인증하여 API에 액세스하는 안전한 방법입니다. 이 권한 부여는 클라이언트 암호가 비밀로 저장되도록하거나 공격자가 인증 코드를 가로채는 위협을 완화하기 위해 사용할 수 없는 경우에 사용해야합니다. "코드 검증기"와 "코드 챌린지"의 조합은 액세스 토큰의 인증 코드를 교환 할 때 클라이언트 암호를 대체합니다.

<a name="creating-a-auth-pkce-grant-client"></a>
### Creating The Client
### 클라이언트 생성

Before your application can issue tokens via the authorization code grant with PKCE, you will need to create a PKCE-enabled client. You may do this using the `passport:client` Artisan command with the `--public` option:

애플리케이션이 PKCE로 인증 코드 부여를 통해 토큰을 발급하려면 먼저 PKCE 지원 클라이언트를 생성해야 합니다. `--public` 옵션과 함께 `passport:client` 아타산 명령어을 사용하여 이 작업을 수행할 수 있습니다.

    php artisan passport:client --public

<a name="requesting-auth-pkce-grant-tokens"></a>
### Requesting Tokens
### 토큰 요청

<a name="code-verifier-code-challenge"></a>
#### Code Verifier & Code Challenge
#### 코드 검증기 및 코드 챌린지

As this authorization grant does not provide a client secret, developers will need to generate a combination of a code verifier and a code challenge in order to request a token.

이 권한 부여는 클라이언트 시크릿을 제공하지 않으므로 개발자는 토큰을 요청하기 위해 코드 검증기와 코드 챌린지의 조합을 생성해야합니다.

The code verifier should be a random string of between 43 and 128 characters containing letters, numbers, and  `"-"`, `"."`, `"_"`, `"~"` characters, as defined in the [RFC 7636 specification](https://tools.ietf.org/html/rfc7636).

코드 검증기는 [RFC 7636 사양](https://tools.ietf.org/html/rfc7636)에 정의 된대로 문자, 숫자 및 `"-"`, `"."`, `"_"`, `"~"` 캐릭터-characters 를 포함하는 43 ~ 128 자의 임의 문자열이어야합니다.

The code challenge should be a Base64 encoded string with URL and filename-safe characters. The trailing `'='` characters should be removed and no line breaks, whitespace, or other additional characters should be present.

코드 챌린지는 URL 및 파일 이름이 안전한 문자가 포함 된 Base64 인코딩 문자열이어야합니다. `'='`문자로 끝나는 것은 제거해야하며 줄 바꿈, 공백 또는 기타 추가 문자가 없어야합니다.

    $encoded = base64_encode(hash('sha256', $code_verifier, true));

    $codeChallenge = strtr(rtrim($encoded, '='), '+/', '-_');

<a name="code-grant-pkce-redirecting-for-authorization"></a>
#### Redirecting For Authorization
#### 권한 재 지정

Once a client has been created, you may use the client ID and the generated code verifier and code challenge to request an authorization code and access token from your application. First, the consuming application should make a redirect request to your application's `/oauth/authorize` route:

클라이언트가 작성되면 클라이언트 ID와 생성 된 코드 검증기 및 코드 챌린지를 사용하여 애플리케이션에서 인증 코드 및 액세스 토큰을 요청할 수 있습니다. 먼저, 소비 애플리케이션은 애플리케이션의 `/oauth/authorize` 경로로 리다이렉션을 요청해야합니다.

    use Illuminate\Http\Request;
    use Illuminate\Support\Str;

    Route::get('/redirect', function (Request $request) {
        $request->session()->put('state', $state = Str::random(40));

        $request->session()->put(
            'code_verifier', $code_verifier = Str::random(128)
        );

        $codeChallenge = strtr(rtrim(
            base64_encode(hash('sha256', $code_verifier, true))
        , '='), '+/', '-_');

        $query = http_build_query([
            'client_id' => 'client-id',
            'redirect_uri' => 'http://third-party-app.com/callback',
            'response_type' => 'code',
            'scope' => '',
            'state' => $state,
            'code_challenge' => $codeChallenge,
            'code_challenge_method' => 'S256',
        ]);

        return redirect('http://passport-app.test/oauth/authorize?'.$query);
    });

<a name="code-grant-pkce-converting-authorization-codes-to-access-tokens"></a>
#### Converting Authorization Codes To Access Tokens
#### 토큰에 액세스하기 위해 권한 부여 코드 변환

If the user approves the authorization request, they will be redirected back to the consuming application. The consumer should verify the `state` parameter against the value that was stored prior to the redirect, as in the standard Authorization Code Grant.

사용자가 승인 요청을 승인하면 애플리케이션으로 다시 리다이렉션됩니다. 사용자는 표준 Authorization Code Grant에서와 같이 경로 재 지정 전에 저장된 값에 대해`state` 파라메터를 확인해야합니다.

If the state parameter matches, the consumer should issue a `POST` request to your application to request an access token. The request should include the authorization code that was issued by your application when the user approved the authorization request along with the originally generated code verifier:

state 파라메터가 일치하면 사용자는 애플리케이션에 `POST` 요청으로 액세스 토큰을 요청해야합니다. 요청에는 사용자가 원래 생성 된 코드 검증기와 함께 권한 부여 요청을 승인 할 때 애플리케이션에서 발행 한 권한 부여 코드가 포함되어야합니다.

    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Http;

    Route::get('/callback', function (Request $request) {
        $state = $request->session()->pull('state');

        $codeVerifier = $request->session()->pull('code_verifier');

        throw_unless(
            strlen($state) > 0 && $state === $request->state,
            InvalidArgumentException::class
        );

        $response = Http::asForm()->post('http://passport-app.test/oauth/token', [
            'grant_type' => 'authorization_code',
            'client_id' => 'client-id',
            'redirect_uri' => 'http://third-party-app.com/callback',
            'code_verifier' => $codeVerifier,
            'code' => $request->code,
        ]);

        return $response->json();
    });

<a name="password-grant-tokens"></a>
## Password Grant Tokens
## 패스워드 Grant 토큰

The OAuth2 password grant allows your other first-party clients, such as a mobile application, to obtain an access token using an email address / username and password. This allows you to issue access tokens securely to your first-party clients without requiring your users to go through the entire OAuth2 authorization code redirect flow.

OAuth2 패스워드 그랜트-grant는 모바일 애플리케이션과 같은 여러분의 다른 클라이언트가 이메일 주소와 / 사용자 이름 및 암호를 사용하여 엑세스 토큰을 획득할 수 있도록 해줍니다. 이를 통해 OAuth2의 승인 코드 리다이렉션 request를 필요로 하지 않고도 엑세스 토큰을 안전하게 발급할 수 있도록 해줍니다.

<a name="creating-a-password-grant-client"></a>
### Creating A Password Grant Client
### 패스워드 Grant 클라이언트 생성하기

Before your application can issue tokens via the password grant, you will need to create a password grant client. You may do this using the `passport:client` Artisan command with the `--password` option. **If you have already run the `passport:install` command, you do not need to run this command:**

패스워드 grant를 통해서 애플리케이션에서 토큰을 발급하기 전에, 패스워드 grant 클라이언트를 생성해야 합니다. `--password` 옵션과 함께 `passport:client` 아티산 명령을 사용하여 이 작업을 수행할 수 있습니다. 이미 `passport:install` 명령어를 실행했다면, 이 명령어를 실행할 필요가 없습니다.

    php artisan passport:client --password

<a name="requesting-password-grant-tokens"></a>
### Requesting Tokens
### 토큰 요청하기

Once you have created a password grant client, you may request an access token by issuing a `POST` request to the `/oauth/token` route with the user's email address and password. Remember, this route is already registered by the `Passport::routes` method so there is no need to define it manually. If the request is successful, you will receive an `access_token` and `refresh_token` in the JSON response from the server:

패스워드 grant 클라이언트가 생성되면, 사용자의 이메일과 패스워드와 함께 `/oauth/token` 라우트에 엑세스 토큰 발급 `POST` request-요청을 보낼 수 있습니다. 기억할점은, 이 라우트는 `Passport::routes`메소드에 의해서 이미 등록되어 있기 때문에, 직접 라우트를 등록할 필요가 없다는 것입니다. 요청-request가 성공적이라면, 서버로 부터 `access_token` 과 `refresh_token` 가 담긴 JSON 응답-response를 받습니다.

    use Illuminate\Support\Facades\Http;

    $response = Http::asForm()->post('http://passport-app.test/oauth/token', [
        'grant_type' => 'password',
        'client_id' => 'client-id',
        'client_secret' => 'client-secret',
        'username' => 'taylor@laravel.com',
        'password' => 'my-password',
        'scope' => '',
    ]);

    return $response->json();

> {tip} Remember, access tokens are long-lived by default. However, you are free to [configure your maximum access token lifetime](#configuration) if needed.

> {tip} 엑세스 토큰은 기본적으로 오랜시간 지속됩니다. 그렇지만 필요하다면, 자유롭게 [엑세스 토큰 지속시간을 설정](#configuration) 할 수 있습니다.

<a name="requesting-all-scopes"></a>
### Requesting All Scopes
### 모든 범위에 대하여 요청하기

When using the password grant or client credentials grant, you may wish to authorize the token for all of the scopes supported by your application. You can do this by requesting the `*` scope. If you request the `*` scope, the `can` method on the token instance will always return `true`. This scope may only be assigned to a token that is issued using the `password` or `client_credentials` grant:

패스워드 grant 또는 client 자격증명 grant를 사용할 때, 여러분은 애플리케이션에서 지원하는 모든 범위의 토큰을 승인 하고자 할수 있습니다. 이렇게 하려면 `*` 범위로 요청하면 됩니다. `*` 범위를 요청하면 토큰 인스턴스의 `can` 메소드가 항상 `true` 를 반환할 것입니다. 이 스코프는 `password` grant를 통해 발행 된 토큰에만 할당하는 것이 좋습니다.

    use Illuminate\Support\Facades\Http;

    $response = Http::asForm()->post('http://passport-app.test/oauth/token', [
        'grant_type' => 'password',
        'client_id' => 'client-id',
        'client_secret' => 'client-secret',
        'username' => 'taylor@laravel.com',
        'password' => 'my-password',
        'scope' => '*',
    ]);

<a name="customizing-the-user-provider"></a>
### Customizing The User Provider
### 사용자 공급자 커스터마이징

If your application uses more than one [authentication user provider](/docs/{{version}}/authentication#introduction), you may specify which user provider the password grant client uses by providing a `--provider` option when creating the client via the `artisan passport:client --password` command. The given provider name should match a valid provider defined in your application's `config/auth.php` configuration file. You can then [protect your route using middleware](#via-middleware) to ensure that only users from the guard's specified provider are authorized.

애플리케이션에서 둘 이상의 [인증 사용자 공급자](/docs/{{version}}/authentication#introduction) 를 사용하는 경우 암호 부여 클라이언트가 사용할 사용자 공급자를 지정할 때 `--provider` 옵션을 제공하여 `artisan passport:client --password` 명령을 통해 클라이언트. 제공된 공급자 이름은 어플리케이션의 `config/auth.php` 구성 파일에 정의 된 유효한 공급자와 일치해야합니다. 그런 다음 [미들웨어를 사용하여 경로를 보호](#via-middleware)하여 가드가 지정한 공급자의 사용자 만 인증되도록 할 수 있습니다.

<a name="customizing-the-username-field"></a>
### Customizing The Username Field
### 사용자 이름 필드 사용자 정의하기

When authenticating using the password grant, Passport will use the `email` attribute of your authenticatable model as the "username". However, you may customize this behavior by defining a `findForPassport` method on your model:

암호 부여를 사용하여 인증할 때 Passport는 인증 가능한 모델의 `email` 속성을 "username" 으로 사용합니다. 그러나 모델에서 `findForPassport` 메소드를 정의함으로써 이 동작을 커스터마이징 할 수 있습니다.

    <?php

    namespace App\Models;

    use Illuminate\Foundation\Auth\User as Authenticatable;
    use Illuminate\Notifications\Notifiable;
    use Laravel\Passport\HasApiTokens;

    class User extends Authenticatable
    {
        use HasApiTokens, Notifiable;

        /**
         * Find the user instance for the given username.
         *
         * @param  string  $username
         * @return \App\Models\User
         */
        public function findForPassport($username)
        {
            return $this->where('username', $username)->first();
        }
    }

<a name="customizing-the-password-validation"></a>
### Customizing The Password Validation
### 패스워드 유효성 검사 커스터마이징

When authenticating using the password grant, Passport will use the `password` attribute of your model to validate the given password. If your model does not have a `password` attribute or you wish to customize the password validation logic, you can define a `validateForPassportPasswordGrant` method on your model:

패스워드를 사용하여 인증 할 때 Passport는 모델의 `password` 속성을 사용하여 주어진 패스워드의 유효성을 검사합니다. 모델에 `password` 속성이 없거나 패스워드 유효성 검사 로직을 커스터마이징 하고자 하는 경우 모델에 `validateForPassportPasswordGrant` 메소드를 정의 할 수 있습니다.

    <?php

    namespace App\Models;

    use Illuminate\Foundation\Auth\User as Authenticatable;
    use Illuminate\Notifications\Notifiable;
    use Illuminate\Support\Facades\Hash;
    use Laravel\Passport\HasApiTokens;

    class User extends Authenticatable
    {
        use HasApiTokens, Notifiable;

        /**
         * Validate the password of the user for the Passport password grant.
         *
         * @param  string  $password
         * @return bool
         */
        public function validateForPassportPasswordGrant($password)
        {
            return Hash::check($password, $this->password);
        }
    }

<a name="implicit-grant-tokens"></a>
## Implicit Grant Tokens
## 묵시적 grant 토큰

The implicit grant is similar to the authorization code grant; however, the token is returned to the client without exchanging an authorization code. This grant is most commonly used for JavaScript or mobile applications where the client credentials can't be securely stored. To enable the grant, call the `enableImplicitGrant` method in the `boot` method of your application's `App\Providers\AuthServiceProvider` class:

묵시적 grant는 승인 코드 grant와 비슷합니다. 그렇지만, 클라이언트에서 반환되는 토큰은 승인 코드를 교환하지 않습니다. 이 grant는 클라이언트 자격증명을 안전하게 저장할 수 없는 자바스크립트 또는 모바일 애플리케이션에서 가장 일반적으로 사용됩니다. 권한 부여를 활성화하려면 애플리케이션의 `App\Providers\AuthServiceProvider` 클래스의 `boot` 메소드에서 `enableImplicitGrant` 메소드를 호출하세요.

    /**
     * Register any authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();

        Passport::routes();

        Passport::enableImplicitGrant();
    }

Once the grant has been enabled, developers may use their client ID to request an access token from your application. The consuming application should make a redirect request to your application's `/oauth/authorize` route like so:

grant가 활성화 되면, 개발자는 클라이언트 ID를 사용하여 애플리케이션에서 액세스 토큰을 요청할 수 있습니다. 이를 처리하는 애플리케이션은 다음과 같이 애플리케이션의 `/oauth/authorize` 라우트로 리디렉션 요청을 해야 합니다.

    use Illuminate\Http\Request;

    Route::get('/redirect', function (Request $request) {
        $request->session()->put('state', $state = Str::random(40));

        $query = http_build_query([
            'client_id' => 'client-id',
            'redirect_uri' => 'http://third-party-app.com/callback',
            'response_type' => 'token',
            'scope' => '',
            'state' => $state,
        ]);

        return redirect('http://passport-app.test/oauth/authorize?'.$query);
    });

> {tip} Remember, the `/oauth/authorize` route is already defined by the `Passport::routes` method. You do not need to manually define this route.

> {tip} `/oauth/authorize` 라우트는 `Passport::routes` 메소드에 의해서 정의된다는 것을 기억하십시오. 이 라우트를 수동으로 등록할 필요가 없습니다.

<a name="client-credentials-grant-tokens"></a>
## Client Credentials Grant Tokens
## 클라이언트의 자격증명을 위한 Grant 토큰

The client credentials grant is suitable for machine-to-machine authentication. For example, you might use this grant in a scheduled job which is performing maintenance tasks over an API.

클라이언트의 자격증명을 위한 Grant 는 시스템간의 인증에 적합합니다. 예를 들어, API를 통해서 관리 작업을 수행하도록 예약된 스케줄링 job에서 이 grant를 사용할 수 있습니다.

Before your application can issue tokens via the client credentials grant, you will need to create a client credentials grant client. You may do this using the `--client` option of the `passport:client` Artisan command:

애플리케이션이 클라이언트의 자격증명 권한 인증를 통해 토큰을 발급하려면 먼저, 사용자는 클라이언트의 자격증명 인증 클라이언트를 생성해야합니다. `passport:client` 명령의 `--client` 옵션을 사용하면 됩니다.

    php artisan passport:client --client

Next, to use this grant type, you need to add the `CheckClientCredentials` middleware to the `$routeMiddleware` property of your `app/Http/Kernel.php` file:

다음으로, 이 권한 유형을 사용하려면 `CheckClientCredentials` 미들웨어를 `app/Http/Kernel.php` 파일의 `$routeMiddleware` 프로퍼티에 추가해야합니다.

    use Laravel\Passport\Http\Middleware\CheckClientCredentials;

    protected $routeMiddleware = [
        'client' => CheckClientCredentials::class,
    ];

Then, attach the middleware to a route:

그 다음 이 미들웨어를 라우트에 추가하십시오:

    Route::get('/orders', function (Request $request) {
        ...
    })->middleware('client');

To restrict access to the route to specific scopes, you may provide a comma-delimited list of the required scopes when attaching the `client` middleware to the route:

특정 범위의 라우트에 대한 액세스를 제한하기 위해 `client` 미들웨어를 라우트에 연결할 때, 쉼표로 분리 된 필수 범위의 목록을 지정 할 수 있습니다.

    Route::get('/orders', function (Request $request) {
        ...
    })->middleware('client:check-status,your-scope');

<a name="retrieving-tokens"></a>
### Retrieving Tokens
### Retrieving Tokens

To retrieve a token using this grant type, make a request to the `oauth/token` endpoint:

이 권한 유형을 사용하여 토큰을 검색하려면 `oauth/token`의 엔드 포인트에 요청하십시오 :

    use Illuminate\Support\Facades\Http;

    $response = Http::asForm()->post('http://passport-app.test/oauth/token', [
        'grant_type' => 'client_credentials',
        'client_id' => 'client-id',
        'client_secret' => 'client-secret',
        'scope' => 'your-scope',
    ]);

    return $response->json()['access_token'];

<a name="personal-access-tokens"></a>
## Personal Access Tokens
## 개인용 엑세스 토큰

Sometimes, your users may want to issue access tokens to themselves without going through the typical authorization code redirect flow. Allowing users to issue tokens to themselves via your application's UI can be useful for allowing users to experiment with your API or may serve as a simpler approach to issuing access tokens in general.

때로는, 사용자가 일반적인 승인 코드 리다이렉션 플로우를 거치지 않고 엑세스 토큰을 발급하기를 원할 수도 있습니다. 사용자가 애플리케이션의 UI를 통해 자신에게 토큰을 발행 할 수 있게 하면, 사용자가 API를 테스트해 볼 수도 있고, 일반적으로 액세스 토큰을 발행하기 위한 더 간단한 방법으로도 사용할 수 있습니다.

<a name="creating-a-personal-access-client"></a>
### Creating A Personal Access Client
### 개인용 엑세스 클라이언트 생성하기

Before your application can issue personal access tokens, you will need to create a personal access client. You may do this by executing the `passport:client` Artisan command with the `--personal` option. If you have already run the `passport:install` command, you do not need to run this command:

애플리케이션에서 개인용 액세스 토큰을 발급하려면 먼저 개인용 액세스 클라이언트를 생성해야 합니다. `--personal` 옵션과 함께 `passport:client` 아티산 명령을 실행하면 됩니다. 이미 `passport:install` 명령을 실행했다면 다음 명령을 실행할 필요가 없습니다.
애플리케이션에서 개인용 액세스 토큰을 발급하려면 먼저 개인용 액세스 클라이언트를 생성해야 합니다. `--personal` 옵션과 함께 `passport:client` 아티산 명령을 실행하면 됩니다. 이미 `--personal` 옵션을 사용하면 됩니다. 만약 이미 `passport:install` 명령어를 실행했다면, 이 명령어를 실행할 필요는 없습니다.

    php artisan passport:client --personal

After creating your personal access client, place the client's ID and plain-text secret value in your application's `.env` file:

개인 액세스 클라이언트를 만든 후 클라이언트의 ID와 일반 텍스트 비밀 값을 애플리케이션의 `.env` 파일에 추가합니다.

```bash
PASSPORT_PERSONAL_ACCESS_CLIENT_ID="client-id-value"
PASSPORT_PERSONAL_ACCESS_CLIENT_SECRET="unhashed-client-secret-value"
```

<a name="managing-personal-access-tokens"></a>
### Managing Personal Access Tokens
### 개인용 엑세스 토큰 관리하기

Once you have created a personal access client, you may issue tokens for a given user using the `createToken` method on the `App\Models\User` model instance. The `createToken` method accepts the name of the token as its first argument and an optional array of [scopes](#token-scopes) as its second argument:

개인용 액세스 클라이언트를 생성하고 나면, `App\Models\User` 모델 인스턴스에 `createToken` 메소드를 사용하여 주어진 사용자를 위한 토큰을 발급할 수 있습니다. `createToken` 메소드는 토큰의 이름을 첫번째 인자로 받아들이고, [스코프-범위](token-scopes)의 배열을 선택적으로 두번째 인자로 받습니다.

    use App\Models\User;

    $user = User::find(1);

    // Creating a token without scopes...
    $token = $user->createToken('Token Name')->accessToken;

    // Creating a token with scopes...
    $token = $user->createToken('My Token', ['place-orders'])->accessToken;

<a name="personal-access-tokens-json-api"></a>
#### JSON API
#### JSON API

Passport also includes a JSON API for managing personal access tokens. You may pair this with your own frontend to offer your users a dashboard for managing personal access tokens. Below, we'll review all of the API endpoints for managing personal access tokens. For convenience, we'll use [Axios](https://github.com/mzabriskie/axios) to demonstrate making HTTP requests to the endpoints.

passport는 개인용 엑세스 토큰을 관리하는 위한 JSON API를 포함하고 있습니다. 이 API와 함께 여러분의 고유한 프론트 엔드를 구성하여 사용자에게 개인용 엑세스 토큰을 관리할 수 있는 대시보드를 제공할 수 있습니다. 아래에서 개인용 엑세스 토큰을 관리하는 모든 API 엔드포인트를 확인해보겠습니다. 편의를 위해서, 엔드포인트에 대한 HTTP request-요청을 만드는 데모에는 [Axios](https://github.com/mzabriskie/axios)를 사용하겠습니다.

The JSON API is guarded by the `web` and `auth` middleware; therefore, it may only be called from your own application. It is not able to be called from an external source.

JSON API는 `web` 및 `auth` 미들웨어에 의해 보호됩니다. 따라서 자신의 애플리케이션에서만 호출 할 수 있습니다. 외부 소스에서는 호출 할 수 없습니다.

<a name="get-oauthscopes"></a>
#### `GET /oauth/scopes`
#### `GET /oauth/scopes`

This route returns all of the [scopes](#token-scopes) defined for your application. You may use this route to list the scopes a user may assign to a personal access token:

이 라우트는 애플리케이션에서 정의된 모든 [스코프-범위](#token-scopes)를 반환합니다. 이 라우트를 사용자가 개인용 엑세스 토큰에 할당된 범위를 나열하는데 사용할 수 있습니다.

    axios.get('/oauth/scopes')
        .then(response => {
            console.log(response.data);
        });

<a name="get-oauthpersonal-access-tokens"></a>
#### `GET /oauth/personal-access-tokens`
#### `GET /oauth/personal-access-tokens`

This route returns all of the personal access tokens that the authenticated user has created. This is primarily useful for listing all of the user's tokens so that they may edit or revoke them:

이 라우트는 인증된 사용자가 생성한 모든 개인용 엑세스 토큰을 반환합니다. 모든 사용자 토큰을 목록을 확인하는데 유용하여, 주로 이를 수정하거나, 제거할 수 있습니다.

    axios.get('/oauth/personal-access-tokens')
        .then(response => {
            console.log(response.data);
        });

<a name="post-oauthpersonal-access-tokens"></a>
#### `POST /oauth/personal-access-tokens`
#### `POST /oauth/personal-access-tokens`

This route creates new personal access tokens. It requires two pieces of data: the token's `name` and the `scopes` that should be assigned to the token:

이 라우트는 새로운 개인용 엑세스 토큰을 생성합니다. 여기에는 두개의 데이터가 필요합니다. 토큰의 `name` 과 토큰에 할당되는 `scopes` 입니다.

    const data = {
        name: 'Token Name',
        scopes: []
    };

    axios.post('/oauth/personal-access-tokens', data)
        .then(response => {
            console.log(response.data.accessToken);
        })
        .catch (response => {
            // List errors on response...
        });

<a name="delete-oauthpersonal-access-tokenstoken-id"></a>
#### `DELETE /oauth/personal-access-tokens/{token-id}`
#### `DELETE /oauth/personal-access-tokens/{token-id}`

This route may be used to revoke personal access tokens:

이 라우트는 개인용 엑세스 토큰을 제거하는데 사용됩니다.

    axios.delete('/oauth/personal-access-tokens/' + tokenId);

<a name="protecting-routes"></a>
## Protecting Routes
## 라우트 보호하기

<a name="via-middleware"></a>
### Via Middleware
### 미들웨어를 통해서

Passport includes an [authentication guard](/docs/{{version}}/authentication#adding-custom-guards) that will validate access tokens on incoming requests. Once you have configured the `api` guard to use the `passport` driver, you only need to specify the `auth:api` middleware on any routes that should require a valid access token:

Passport는 유입되는 request-요청에 대한 엑세스 토큰을 검증하는 [사용자 승인 guard](/docs/{{version}}/authentication#adding-custom-guards)를 포함하고 있습니다. `api` guard가 `passport` 드라이버를 사용하도록 설정하였다면, 엑세스 토큰 검사가 필요한 라우트에 `auth:api` 미들웨어를 지정하면 됩니다.

    Route::get('/user', function () {
        //
    })->middleware('auth:api');

> {note} If you are using the [client credentials grant](#client-credentials-grant-tokens), you should use [the `client` middleware](#client-credentials-grant-tokens) to protect your routes instead of the `auth:api` middleware.

> {note} [클라이언트 자격 증명 grant](#client-credentials-grant-tokens)를 사용하는 경우, [`client` 미들웨어](#client-credentials-grant-tokens)의 `auth:api` 미들웨어를 사용하여 라우트를 보호해야 합니다.

<a name="multiple-authentication-guards"></a>
#### Multiple Authentication Guards
#### 다중 인증 가드

If your application authenticates different types of users that perhaps use entirely different Eloquent models, you will likely need to define a guard configuration for each user provider type in your application. This allows you to protect requests intended for specific user providers. For example, given the following guard configuration the `config/auth.php` configuration file:

애플리케이션이 완전히 다른 Eloquent 모델을 사용하는 여러 유형의 사용자가 인증하는 경우 애플리케이션의 각 사용자 공급자 유형에 대한 가드 설정을 정의해야 할 수 있습니다. 이를 통해 특정 사용자 공급자를 위한 요청을 보호 할 수 있습니다. 예를 들어 다음 가드 설정의 경우 `config/auth.php` 설정 파일이 있습니다.

    'api' => [
        'driver' => 'passport',
        'provider' => 'users',
    ],

    'api-customers' => [
        'driver' => 'passport',
        'provider' => 'customers',
    ],

The following route will utilize the `api-customers` guard, which uses the `customers` user provider, to authenticate incoming requests:

다음 `customers` 라우트는 사용자 프로바이더를 사용하는 `api-customers` 가드를 사용하여 들어오는 요청을 인증합니다.

    Route::get('/customer', function () {
        //
    })->middleware('auth:api-customers');

> {tip} For more information on using multiple user providers with Passport, please consult the [password grant documentation](#customizing-the-user-provider).

> {tip} Passport에서 여러 사용자 프로바이더를 사용하는 방법에 대한 자세한 내용은 [암호 부여 문서](#customizing-the-user-provider)를 참조하십시오.

<a name="passing-the-access-token"></a>
### Passing The Access Token
### 엑세스 토큰 전달하기

When calling routes that are protected by Passport, your application's API consumers should specify their access token as a `Bearer` token in the `Authorization` header of their request. For example, when using the Guzzle HTTP library:

Passport에 의해서 보호되는 라우트를 호출할 때, 애플리케이션의 API 사용자는 그들의 요청-request의 `Authorization` 헤더에 `Bearer` 토큰으로 엑세스 토큰을 지정해야 합니다. 예를 들어 Guzzle HTTP 라이브러리를 사용해보겠습니다.

    use Illuminate\Support\Facades\Http;

    $response = Http::withHeaders([
        'Accept' => 'application/json',
        'Authorization' => 'Bearer '.$accessToken,
    ])->get('https://passport-app.test/api/user');

    return $response->json();

<a name="token-scopes"></a>
## Token Scopes
## 토큰 스코프(범위)

Scopes allow your API clients to request a specific set of permissions when requesting authorization to access an account. For example, if you are building an e-commerce application, not all API consumers will need the ability to place orders. Instead, you may allow the consumers to only request authorization to access order shipment statuses. In other words, scopes allow your application's users to limit the actions a third-party application can perform on their behalf.

스코프는 계정에 대한 엑세스 승인을 요청할 때, 여러분의 API 클라이언트가 제한된 권한을 지정하여 요청하도록 합니다. 예를 들어, e-커머스 애플리케이션을 구성한다면, 전체 API 사용자에게 주문을 할 수 있는 권한을 줄 필요는 없을것입니다. 대신에, 사용자에게 주문 배송상황에 엑세스 할 수 있는 권한을 주면 됩니다. 다시 말해 스코프는 여러분의 애플리케이션 사용자가 써드파티 애플리케이션을 통해서 실행할 수 있는 액션을 제한할 수 있습니다.

<a name="defining-scopes"></a>
### Defining Scopes
### 스코프 정의하기

You may define your API's scopes using the `Passport::tokensCan` method in the `boot` method of your application's `App\Providers\AuthServiceProvider` class. The `tokensCan` method accepts an array of scope names and scope descriptions. The scope description may be anything you wish and will be displayed to users on the authorization approval screen:

API의 범위(scope)는 애플리케이션의 `App\Providers\AuthServiceProvider` 클래스의 `boot` 메소드에서 `Passport::tokensCan` 메소드를 사용하여 정의할 수 있습니다. `tokensCan` 메소드는 스코프의 이름과, 설명에 대한 배열을 인자로 받습니다. 스코프 설명은 권한 승인 페이지에서 사용자에게 보여주려는 어떠한 내용도 가능합니다.

    /**
     * Register any authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();

        Passport::routes();

        Passport::tokensCan([
            'place-orders' => 'Place orders',
            'check-status' => 'Check order status',
        ]);
    }

<a name="default-scope"></a>
### Default Scope
### 기본 스코프

If a client does not request any specific scopes, you may configure your Passport server to attach default scope(s) to the token using the `setDefaultScope` method. Typically, you should call this method from the `boot` method of your application's `App\Providers\AuthServiceProvider` class:

클라이언트가 특정한 스코프를 요청하지 않으면 `setDefaultScope` 메소드를 사용하여 기본 스코프를 토큰에 연결하도록 Passport 서버를 구성할 수 있습니다. 일반적으로 애플리케이션의 `App\Providers\AuthServiceProvider` 클래스의 `boot` 메소드에서 호출해야 합니다.

    use Laravel\Passport\Passport;

    Passport::tokensCan([
        'place-orders' => 'Place orders',
        'check-status' => 'Check order status',
    ]);

    Passport::setDefaultScope([
        'check-status',
        'place-orders',
    ]);

<a name="assigning-scopes-to-tokens"></a>
### Assigning Scopes To Tokens
### 토큰에 스코프 할당하기

<a name="when-requesting-authorization-codes"></a>
#### When Requesting Authorization Codes
#### 승인 코드를 요청할 때

When requesting an access token using the authorization code grant, consumers should specify their desired scopes as the `scope` query string parameter. The `scope` parameter should be a space-delimited list of scopes:

승인 코드 grant를 사용하여 액세스 토큰을 요청할 때, 사용자는 `scope` 쿼리 스트링 인자를 통해서 원하는 scope-범위를 지정해야 합니다. `scope` 인자는 scope-범위의 목록들을 공백으로 구분된 scope-범위의 목록이어야 합니다.

    Route::get('/redirect', function () {
        $query = http_build_query([
            'client_id' => 'client-id',
            'redirect_uri' => 'http://example.com/callback',
            'response_type' => 'code',
            'scope' => 'place-orders check-status',
        ]);

        return redirect('http://passport-app.test/oauth/authorize?'.$query);
    });

<a name="when-issuing-personal-access-tokens"></a>
#### When Issuing Personal Access Tokens
#### 개인용 엑세스 토큰 발급할 때

If you are issuing personal access tokens using the `App\Models\User` model's `createToken` method, you may pass the array of desired scopes as the second argument to the method:

`App\Models\User` 모델의 `createToken` 메소드를 사용하여 개인용 엑세스 토큰을 발급하고자 한다면, 메소드의 두번째 인자로 원하는 scope를 배열로 전달할 수 있습니다.

    $token = $user->createToken('My Token', ['place-orders'])->accessToken;

<a name="checking-scopes"></a>
### Checking Scopes
### Scope 확인하기

Passport includes two middleware that may be used to verify that an incoming request is authenticated with a token that has been granted a given scope. To get started, add the following middleware to the `$routeMiddleware` property of your `app/Http/Kernel.php` file:

Passport 는 유입되는 request-요청이 주어진 스코프가 승인된 토큰으로 인증되었는지 확인하는 데 사용할 수 있는 두 개의 미들웨어가 포함되어 있습니다. 이를 사용하려면 `app/Http/Kernel.php` 파일의 `$routeMiddleware` 속성에 다음 미들웨어를 추가하세요.

    'scopes' => \Laravel\Passport\Http\Middleware\CheckScopes::class,
    'scope' => \Laravel\Passport\Http\Middleware\CheckForAnyScope::class,

<a name="check-for-all-scopes"></a>
#### Check For All Scopes
#### 전체 Scope-범위 확인하기

The `scopes` middleware may be assigned to a route to verify that the incoming request's access token has all of the listed scopes:

`scopes` 미들웨어는 라우트에 할당하여 유입되는 request-요청의 액세스 토큰에 나열된 스코프가 모두 있는지 확인하기 위해 사용할 수 있습니다.

    Route::get('/orders', function () {
        // Access token has both "check-status" and "place-orders" scopes...
    })->middleware(['auth:api', 'scopes:check-status,place-orders']);

<a name="check-for-any-scopes"></a>
#### Check For Any Scopes
#### 특정 범위 확인하기

The `scope` middleware may be assigned to a route to verify that the incoming request's access token has *at least one* of the listed scopes:

`scope` 미들웨어는 라우트에 할당하여 유입되는 request-요청의 엑세스 토큰이 *최소한 하나*의 scope에 해당하는지 확인하는데 사용할 수 있습니다.

    Route::get('/orders', function () {
        // Access token has either "check-status" or "place-orders" scope...
    })->middleware(['auth:api', 'scope:check-status,place-orders']);

<a name="checking-scopes-on-a-token-instance"></a>
#### Checking Scopes On A Token Instance
#### 토큰 인스턴스에서 scope-범위 확인하기

Once an access token authenticated request has entered your application, you may still check if the token has a given scope using the `tokenCan` method on the authenticated `App\Models\User` instance:

엑세스 토큰이 인증된 request-요청이 애플리케이션에 전달되면, 인증된 `App\Models\User` 인스턴스의 `tokenCan` 메소드를 사용하여 토큰이 주어진 scope에 해당하는지 확인할 수 있습니다.

    use Illuminate\Http\Request;

    Route::get('/orders', function (Request $request) {
        if ($request->user()->tokenCan('place-orders')) {
            //
        }
    });

<a name="additional-scope-methods"></a>
#### Additional Scope Methods
#### 추가 스코프 메소드

The `scopeIds` method will return an array of all defined IDs / names:

`scopeIds` 메소드는 정의 된 모든 ID / 이름의 배열을 반환합니다.

    use Laravel\Passport\Passport;

    Passport::scopeIds();

The `scopes` method will return an array of all defined scopes as instances of `Laravel\Passport\Scope`:

`scopes` 메소드는 정의 된 모든 범위의 배열을 `Laravel\Passport\Scope` 의 인스턴스로 반환합니다.

    Passport::scopes();

The `scopesFor` method will return an array of `Laravel\Passport\Scope` instances matching the given IDs / names:

`scopesFor` 메소드는 주어진 ID / 이름과 일치하는 `Laravel\Passport\Scope` 인스턴스의 배열을 반환합니다.

    Passport::scopesFor(['place-orders', 'check-status']);

You may determine if a given scope has been defined using the `hasScope` method:

`hasScope` 메소드를 사용하여 주어진 스코프가 정의되었는지를 확인 할 수 있습니다.

    Passport::hasScope('place-orders');

<a name="consuming-your-api-with-javascript"></a>
## Consuming Your API With JavaScript
## 자바스크립트로 API 사용하기

When building an API, it can be extremely useful to be able to consume your own API from your JavaScript application. This approach to API development allows your own application to consume the same API that you are sharing with the world. The same API may be consumed by your web application, mobile applications, third-party applications, and any SDKs that you may publish on various package managers.

API를 빌드할 때 자바스크립트 애플리케이션에서 고유한 API를 사용할 수 있다는 것은, 매우 편리합니다. API 개발 방식을 사용하면 여러분의 애플리케이션이 전세계로 공유되는 것과 동일한 API를 사용할 수 있게 됩니다. 웹 애플리케이션, 모바일 애플리케이션, 써드파티 애플리케이션 및 다양한 패키지 관리자를 통해 퍼블리싱 할 수 있는 SDK에서 동일한 API를 사용할 수 있습니다.

Typically, if you want to consume your API from your JavaScript application, you would need to manually send an access token to the application and pass it with each request to your application. However, Passport includes a middleware that can handle this for you. All you need to do is add the `CreateFreshApiToken` middleware to your `web` middleware group in your `app/Http/Kernel.php` file:

일반적으로, 여러분의 API를 자바스크립트 애플리케이션에서 사용하고자 한다면, 애플리케이션에 엑세스 토큰을 수동으로 보내고, 매번 애플리케이션에 요청-request 할때 마다 이 토큰을 전달해야 합니다. 그렇지만 Passport는 이미 이를 처리하는 미들웨어를 포함하고 있습니다. 여러분에게 필요한 것은 `app/Http/Kernel.php` 파일의 `web` 미들웨어 그룹에 `CreateFreshApiToken` 미들웨어를 추가하는 것 뿐입니다.

    'web' => [
        // Other middleware...
        \Laravel\Passport\Http\Middleware\CreateFreshApiToken::class,
    ],

> {note} You should ensure that the `CreateFreshApiToken` middleware is the last middleware listed in your middleware stack.

> {note} `CreateFreshApiToken` 미들웨어가 미들웨어 스택에 나열된 마지막 미들웨어인지 확인해야합니다.

This middleware will attach a `laravel_token` cookie to your outgoing responses. This cookie contains an encrypted JWT that Passport will use to authenticate API requests from your JavaScript application. The JWT has a lifetime equal to your `session.lifetime` configuration value. Now, since the browser will automatically send the cookie with all subsequent requests, you may make requests to your application's API without explicitly passing an access token:

이 미들웨어는 나가는 응답-responses에 `laravel_token` 쿠키를 추가합니다. 이 쿠키는 Passport 가 자바스크립트 애플리케이션의 API 요청-request을 인증하는 데 사용할 암호화된 JWT가 포함되어 있습니다. JWT의 수명은 `session.lifetime` 설정 값과 같습니다. 이제 브라우저는 모든 후속 요청과 함께 쿠키를 자동으로 보내므로 액세스 토큰을 명시적으로 전달하지 않고 애플리케이션에 API를 요청-request할 수 있습니다.

    axios.get('/api/user')
        .then(response => {
            console.log(response.data);
        });

<a name="customizing-the-cookie-name"></a>
#### Customizing The Cookie Name
#### 쿠키 이름의 커스터마이징

If needed, you can customize the `laravel_token` cookie's name using the `Passport::cookie` method. Typically, this method should be called from the `boot` method of your application's `App\Providers\AuthServiceProvider` class:

필요한 경우 `Passport::cookie` 메소드를 사용하여 `laravel_token` 쿠키의 이름을 커스터마이징 할 수 있습니다. 일반적으로 이 메소드는 애플리케이션의 `App\Providers\AuthServiceProvider` 클래스의 `boot` 메소드에서 호출해야 합니다.

    /**
     * Register any authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();

        Passport::routes();

        Passport::cookie('custom_name');
    }

<a name="csrf-protection"></a>
#### CSRF Protection
#### CSRF 보호하기

When using this method of authentication, you will need to ensure a valid CSRF token header is included in your requests. The default Laravel JavaScript scaffolding includes an Axios instance, which will automatically use the encrypted `XSRF-TOKEN` cookie value to send an `X-XSRF-TOKEN` header on same-origin requests.

이 인증 메소드를 사용할 때, 유효한 CSRF 토큰 헤더가 요청-request에 포함되어 있는지 확인해야합니다. 라라벨의 기본 자바스크립트 스캐폴딩은 Axios 인스턴스를 포함하는데, Axios 인스턴스는 자동으로 암호화 된 `XSRF-TOKEN` 쿠키의 값을 자동으로 사용하여 same-origin 요청에 대해 `X-XSRF-TOKEN` 헤더를 포함하여 보냅니다.

> {tip} If you choose to send the `X-CSRF-TOKEN` header instead of `X-XSRF-TOKEN`, you will need to use the unencrypted token provided by `csrf_token()`.

> {tip} `X-XSRF-TOKEN` 대신 `X-CSRF-TOKEN` 헤더를 보내려면 `csrf_token()`에서 제공한 암호화되지 않은 토큰을 사용해야합니다.

<a name="events"></a>
## Events
## 이벤트

Passport raises events when issuing access tokens and refresh tokens. You may use these events to prune or revoke other access tokens in your database. If you would like, you may attach listeners to these events in your application's `App\Providers\EventServiceProvider` class:

Passport 는 엑세스 토큰과 리프레쉬 토큰을 발급할 때 이벤트를 발생시킵니다. 이 이벤트를 사용하여 데이터베이스의 다른 액세스 토큰을 제거하거나 취소 할 수 있습니다. 여러분의 애플리케이션의 `App\Providers\EventServiceProvider` 클래스 안에서 다음 이벤트에 리스너를 추가할 수 있습니다.

    /**
     * The event listener mappings for the application.
     *
     * @var array
     */
    protected $listen = [
        'Laravel\Passport\Events\AccessTokenCreated' => [
            'App\Listeners\RevokeOldTokens',
        ],

        'Laravel\Passport\Events\RefreshTokenCreated' => [
            'App\Listeners\PruneOldTokens',
        ],
    ];

<a name="testing"></a>
## Testing
## 테스팅

Passport's `actingAs` method may be used to specify the currently authenticated user as well as its scopes. The first argument given to the `actingAs` method is the user instance and the second is an array of scopes that should be granted to the user's token:

Passport의 `actingAs` 메소드는 현재 인증된 사용자와 스코프를 지정하는 데 사용할 수 있습니다. `actingAs` 메소드에 전달되는 첫번째 인자는 사용자 인스턴스이고, 두번째 인자는 사용자의 토큰에 허용되어야 하는 스코프 배열입니다.

    use App\Models\User;
    use Laravel\Passport\Passport;

    public function test_servers_can_be_created()
    {
        Passport::actingAs(
            User::factory()->create(),
            ['create-servers']
        );

        $response = $this->post('/api/create-server');

        $response->assertStatus(201);
    }

Passport's `actingAsClient` method may be used to specify the currently authenticated client as well as its scopes. The first argument given to the `actingAsClient` method is the client instance and the second is an array of scopes that should be granted to the client's token:

Passport의 `actingAsClient` 메소드를 사용하여 현재 인증 된 클라이언트와 해당 범위를 지정할 수 있습니다. `actingAsClient` 메소드에 주어진 첫 번째 인수는 클라이언트 인스턴스이고 두 번째 인수는 클라이언트의 토큰에 부여해야하는 스코프의 배열입니다.

    use Laravel\Passport\Client;
    use Laravel\Passport\Passport;

    public function test_orders_can_be_retrieved()
    {
        Passport::actingAsClient(
            Client::factory()->create(),
            ['check-status']
        );

        $response = $this->get('/api/orders');

        $response->assertStatus(200);
    }
