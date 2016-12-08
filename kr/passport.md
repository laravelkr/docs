# API Authentication (Passport)
# API 인증(Passport)

- [Introduction](#introduction)
- [소개하기](#introduction)
- [Installation](#installation)
- [설치하기](#installation)
    - [Frontend Quickstart](#frontend-quickstart)
    - [프론트 엔드 빠른시작](#frontend-quickstart)
- [Configuration](#configuration)
- [설정하기](#configuration)
    - [Token Lifetimes](#token-lifetimes)
    - [Token Lifetimes](#token-lifetimes)
- [Issuing Access Tokens](#issuing-access-tokens)
- [Issuing Access Tokens](#issuing-access-tokens)
    - [Managing Clients](#managing-clients)
    - [Managing Clients](#managing-clients)
    - [Requesting Tokens](#requesting-tokens)
    - [Requesting Tokens](#requesting-tokens)
    - [Refreshing Tokens](#refreshing-tokens)
    - [Refreshing Tokens](#refreshing-tokens)
- [Password Grant Tokens](#password-grant-tokens)
- [Password Grant Tokens](#password-grant-tokens)
    - [Creating A Password Grant Client](#creating-a-password-grant-client)
    - [Creating A Password Grant Client](#creating-a-password-grant-client)
    - [Requesting Tokens](#requesting-password-grant-tokens)
    - [Requesting Tokens](#requesting-password-grant-tokens)
    - [Requesting All Scopes](#requesting-all-scopes)
    - [Requesting All Scopes](#requesting-all-scopes)
- [Implicit Grant Tokens](#implicit-grant-tokens)
- [Implicit Grant Tokens](#implicit-grant-tokens)
- [Personal Access Tokens](#personal-access-tokens)
- [Personal Access Tokens](#personal-access-tokens)
    - [Creating A Personal Access Client](#creating-a-personal-access-client)
    - [Creating A Personal Access Client](#creating-a-personal-access-client)
    - [Managing Personal Access Tokens](#managing-personal-access-tokens)
    - [Managing Personal Access Tokens](#managing-personal-access-tokens)
- [Protecting Routes](#protecting-routes)
- [Protecting Routes](#protecting-routes)
    - [Via Middleware](#via-middleware)
    - [Via Middleware](#via-middleware)
    - [Passing The Access Token](#passing-the-access-token)
    - [Passing The Access Token](#passing-the-access-token)
- [Token Scopes](#token-scopes)
- [Token Scopes](#token-scopes)
    - [Defining Scopes](#defining-scopes)
    - [Defining Scopes](#defining-scopes)
    - [Assigning Scopes To Tokens](#assigning-scopes-to-tokens)
    - [Assigning Scopes To Tokens](#assigning-scopes-to-tokens)
    - [Checking Scopes](#checking-scopes)
    - [Checking Scopes](#checking-scopes)
- [Consuming Your API With JavaScript](#consuming-your-api-with-javascript)
- [Consuming Your API With JavaScript](#consuming-your-api-with-javascript)
- [Events](#events)
- [이벤트](#events)

<a name="introduction"></a>
## Introduction
## 소개하기

Laravel already makes it easy to perform authentication via traditional login forms, but what about APIs? APIs typically use tokens to authenticate users and do not maintain session state between requests. Laravel makes API authentication a breeze using Laravel Passport, which provides a full OAuth2 server implementation for your Laravel application in a matter of minutes. Passport is built on top of the [League OAuth2 server](https://github.com/thephpleague/oauth2-server) that is maintained by Alex Bilbie.

라라벨은 이미 전통적인 로그인 폼을 통한 사용자 인증을 손쉽게 사용할 수 있는 방법을 가지고 있습니다. 하지만 API의 경우에는 어떨까요? API는 일반적으로 사용자 인증을 위해서 토큰을 사용하고 request-요청에서는 세션을 사용하지 않습니다. 라라벨은 어플리케이션에 Full OAuth2 서버 구현을 제공하는 Passport를 사용하여 API 인증을 용이하게 합니다. Passport 는 Alex Bilbie에 의해서 관리되고 있는 [League OAuth2 server](https://github.com/thephpleague/oauth2-server) 위에 구성되어 있습니다.

> {note} This documentation assumes you are already familiar with OAuth2. If you do not know anything about OAuth2, consider familiarizing yourself with the general terminology and features of OAuth2 before continuing.

> {note} 이 문서는 여러분이 OAuth2에 익숙하다고 전제하고 있습니다. OAuth2 대해 잘 모르겠다면, 문서를 읽기 전에 OAuth2에서 사용되는 일반적인 용어와 기능들을 숙지해 주십시오.

<a name="installation"></a>
## Installation
## 설치하기

To get started, install Passport via the Composer package manager:

컴포저를 통해서 Passport를 설치하는 것부터 시작해보겠습니다:

    composer require laravel/passport

Next, register the Passport service provider in the `providers` array of your `config/app.php` configuration file:

다음으로 Passport 서비스 프로바이더를 `config/app.php` 설정 파일의 `providers` 배열에 등록합니다:

    Laravel\Passport\PassportServiceProvider::class,

The Passport service provider registers its own database migration directory with the framework, so you should migrate your database after registering the provider. The Passport migrations will create the tables your application needs to store clients and access tokens:

Passport 서비스 프로바이더는 고유한 데이터베이스 마이그레이션 디렉토리를 등록하기 때문에, 서비스 프로바이더를 등록한 뒤에 데이터베이스 마이그레이션을 실행해야 합니다. Passport 마이그레이션을 실행하면 어플리케이션에서 필요한 클라이언트와 엑세스 토큰을 저장하는 테이블이 생성됩니다:

    php artisan migrate

Next, you should run the `passport:install` command. This command will create the encryption keys needed to generate secure access tokens. In addition, the command will create "personal access" and "password grant" clients which will be used to generate access tokens:

다음으로, `passport:install` 명령어를 실행해야합니다. 이 명령어는 안전한 엑세스 토큰을 생성하는데 필요한 암호화 키를 생성합니다. 추가적으로, 명령어는 엑세스 토큰을 생성하는데 사용되는 "personal access" 그리고 "password grant" 클라이언트를 생성합니다:

    php artisan passport:install

After running this command, add the `Laravel\Passport\HasApiTokens` trait to your `App\User` model. This trait will provide a few helper methods to your model which allow you to inspect the authenticated user's token and scopes:

이 명령어를 실행한 후에, `App\User` 모델에 `Laravel\Passport\HasApiTokens` 트레이트-trait 를 추가하십시오. 이 트레이트-trait는 모델에 인증된 사용자의 토큰과 범위를 확인하기 위한 몇가지 헬퍼 메소드를 제공합니다:

    <?php

    namespace App;

    use Laravel\Passport\HasApiTokens;
    use Illuminate\Notifications\Notifiable;
    use Illuminate\Foundation\Auth\User as Authenticatable;

    class User extends Authenticatable
    {
        use HasApiTokens, Notifiable;
    }

Next, you should call the `Passport::routes` method within the `boot` method of your `AuthServiceProvider`. This method will register the routes necessary to issue access tokens and revoke access tokens, clients, and personal access tokens:

다음으로, `AuthServiceProvider` 의 `boot` 메소드에서 `Passport::routes` 메소드를 호출해야 합니다. 이 메소드는 엑세스 토큰을 발급하는 라우트와 엑세스 토큰, 클라이언트 그리고 개인용 엑세스 토큰을 해제하는 라우트를 등록합니다:

    <?php

    namespace App\Providers;

    use Laravel\Passport\Passport;
    use Illuminate\Support\Facades\Gate;
    use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;

    class AuthServiceProvider extends ServiceProvider
    {
        /**
         * The policy mappings for the application.
         *
         * @var array
         */
        protected $policies = [
            'App\Model' => 'App\Policies\ModelPolicy',
        ];

        /**
         * Register any authentication / authorization services.
         *
         * @return void
         */
        public function boot()
        {
            $this->registerPolicies();

            Passport::routes();
        }
    }

Finally, in your `config/auth.php` configuration file, you should set the `driver` option of the `api` authentication guard to `passport`. This will instruct your application to use Passport's `TokenGuard` when authenticating incoming API requests:

마지막으로, `config/auth.php` 설정 파일에서 guard `api` 인증 `driver` 옵션을 `passport` 로 변경해야 합니다. 이렇게 하면, 인증 API request이 유입될 때 어플리케이션이 Passport의 `TokenGuard` 를 사용합니다:

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

<a name="frontend-quickstart"></a>
### Frontend Quickstart
### 빠른 프론트엔드 시작하기

> {note} In order to use the Passport Vue components, you must be using the [Vue](https://vuejs.org) JavaScript framework. These components also use the Bootstrap CSS framework. However, even if you are not using these tools, the components serve as a valuable reference for your own frontend implementation.

> {note} Passport 의 Vue 컴포넌트를 사용하려면, 여러분은 [Vue](https://vuejs.org) 자바스크립트 프레임워크를 사용해야만 합니다. 또한 이 컴포넌트는 부트스트랩 CSS 프레임워크도 사용합니다. 이러한 툴들을 사용하지 않더라도, 여러분의 고유한 프론트엔드 구현을 위한 주요한 참고 자료로 사용될 수 있습니다.

Passport ships with a JSON API that you may use to allow your users to create clients and personal access tokens. However, it can be time consuming to code a frontend to interact with these APIs. So, Passport also includes pre-built [Vue](https://vuejs.org) components you may use as an example implementation or starting point for your own implementation.

Passport 는 여러분의 사용자가 클라이언트와 개인용 엑세스 토큰을 생성하는 것을 가능하게 하는 JSON API를 제공합니다. 하지만 이런 API에 대한 프론트엔드를 구성하는데는 시간이 필요합니다. 그래서 Pssport는 여러분이 사용할 수 있는 구현 예제 또는 고유한 구현을 위한 참고가 될 수 있도록 사전에 작성해놓은 [Vue](https://vuejs.org) 컴포넌트를 포함하고 있습니다. 

To publish the Passport Vue components, use the `vendor:publish` Artisan command:

Passport Vue 컴포넌트를 퍼블리싱 하려면, `vendor:publish` 아티즌 명령어를 사용하면 됩니다:

    php artisan vendor:publish --tag=passport-components

The published components will be placed in your `resources/assets/js/components` directory. Once the components have been published, you should register them in your `resources/assets/js/app.js` file:

퍼블리싱이 끝난 컴포넌트는 `resources/assets/js/components` 디렉토리에 설치됩니다. 컴포넌트가 퍼블리싱되고 나면, `resources/assets/js/app.js` 파일에 등록해야합니다:

    Vue.component(
        'passport-clients',
        require('./components/passport/Clients.vue')
    );

    Vue.component(
        'passport-authorized-clients',
        require('./components/passport/AuthorizedClients.vue')
    );

    Vue.component(
        'passport-personal-access-tokens',
        require('./components/passport/PersonalAccessTokens.vue')
    );

Once the components have been registered, you may drop them into one of your application's templates to get started creating clients and personal access tokens:

컴포넌트를 등록하고나면, 클라이언트와 개인용 엑세스 토큰을 생성하기 위해서 어플리케이션의 템플릿에 다음 코드를 복사하십시오:

    <passport-clients></passport-clients>
    <passport-authorized-clients></passport-authorized-clients>
    <passport-personal-access-tokens></passport-personal-access-tokens>

<a name="configuration"></a>
## Configuration
## 설정하기

<a name="token-lifetimes"></a>
### Token Lifetimes
### 토큰 지속시간

By default, Passport issues long-lived access tokens that never need to be refreshed. If you would like to configure a shorter token lifetime, you may use the `tokensExpireIn` and `refreshTokensExpireIn` methods. These methods should be called from the `boot` method of your `AuthServiceProvider`:

기본적으로 Passport 는 다시 생성할 필요없도록 오래동안 지속되는 엑세스 토큰을 발급합니다. 토큰의 지속시간을 더 짧게 줄이려면, `tokensExpireIn` 그리고 `refreshTokensExpireIn` 메소드를 사용하면 됩니다. 이 메소드는 `AuthServiceProvider` 의 `boot` 메소드에서 호출되어야 합니다:

    use Carbon\Carbon;

    /**
     * Register any authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();

        Passport::routes();

        Passport::tokensExpireIn(Carbon::now()->addDays(15));

        Passport::refreshTokensExpireIn(Carbon::now()->addDays(30));
    }

<a name="issuing-access-tokens"></a>
## Issuing Access Tokens
## 엑세스 토큰 발급하기

Using OAuth2 with authorization codes is how most developers are familiar with OAuth2. When using authorization codes, a client application will redirect a user to your server where they will either approve or deny the request to issue an access token to the client.

승인 코드와 함께 OAuth2를 사용하는 것은 대부분의 개발자가 OAuth2를 사용하는 가장 익숙한 방법입니다. 승인 코드를 사용할 때, 클라이언트 어플리케이션은 사용자를 서버로 리다이렉션 시켜서, 클라이언트에 대한 엑세스 토큰을 발급하는 요청-request를 승인하거나, 거부하게 됩니다.

<a name="managing-clients"></a>
### Managing Clients
### 클라이언트 관리

First, developers building applications that need to interact with your application's API will need to register their application with yours by creating a "client". Typically, this consists of providing the name of their application and a URL that your application can redirect to after users approve their request for authorization.

먼저, 어플리케이션의 API와 인터렉션을 해야하는 어플리케이션을 작성하는 개발자는 하나의 "클라이언트"를 만들어 어플리케이션에 등록해야 합니다. 일반적으로 이것은 어플리케이션의 이름과 사용자가 reqeust을 승인한 뒤에 어플리케이션이 리다렉션 할 수 있는 URL을 제공하여 구성합니다.

#### The `passport:client` Command
#### `passport:client` 명령어

The simplest way to create a client is using the `passport:client` Artisan command. This command may be used to create your own clients for testing your OAuth2 functionality. When you run the `client` command, Passport will prompt you for more information about your client and will provide you with a client ID and secret:

클라이언틀르 만드는 가장 간단한 방법은 `passport:client` 아티즌 명령어를 사용하는 것입니다. 이 명령어는 OAuth2 기능을 테스트하는 여러분의 고유한 클라이언트를 생성하는데 사용될 수 있습니다. 여러분이 `client` 명령어를 실행하면, Passport는 클리이언트에 대한 보다 자세한 정보를 물어보는 메세지를 표시하고 클라이언트의 ID 와 암호를 제공합니다:

    php artisan passport:client

#### JSON API
#### JSON API

Since your users will not be able to utilize the `client` command, Passport provides a JSON API that you may use to create clients. This saves you the trouble of having to manually code controllers for creating, updating, and deleting clients.

여러분의 사용자는 `client` 명령어를 사용할 수 없기 때문에, Passport 는 클라이언트를 생성하는데 사용할 수 있는 JSON API를 제공합니다. 이렇게 하면, 일일이 클라이언트를 생성하고, 수정하고, 삭제하는 컨트롤러 코드를 구성해야 하는 문제에서 벗어날 수 있습니다. 

However, you will need to pair Passport's JSON API with your own frontend to provide a dashboard for your users to manage their clients. Below, we'll review all of the API endpoints for managing clients. For convenience, we'll use [Vue](https://vuejs.org) to demonstrate making HTTP requests to the endpoints.

하지만 Passport JSON API와 짝을 이루어 사용자가 클라이언트를 관리할 수 있는 대쉬보드를 제공하는 여러분 고유의 프론트 엔드를 구성해야 합니다. 다음에서, 클라이언트를 관리하는 모든 API 엔드포인트를 확인해보겠습니다. 편의를 위해서, 엔드 포인트에 대한 HTTP request-요청을 만드는 데모를 위해서 [Vue](https://vuejs.org)를 사용하겠습니다.

> {tip} If you don't want to implement the entire client management frontend yourself, you can use the [frontend quickstart](#frontend-quickstart) to have a fully functional frontend in a matter of minutes.

> {tip} 만약 클라인터 관리용 전체 프론트 엔드를 자체적으로 구현하는 것을 원하지 않는다면, [빠르게 프론트 엔드 시작하기](#frontend-quickstart)를 사용하여, 빠르게 프론트엔드 전체 기능을 구성할 수 있습니다.

#### `GET /oauth/clients`

This route returns all of the clients for the authenticated user. This is primarily useful for listing all of the user's clients so that they may edit or delete them:

    this.$http.get('/oauth/clients')
        .then(response => {
            console.log(response.data);
        });

#### `POST /oauth/clients`

This route is used to create new clients. It requires two pieces of data: the client's `name` and a `redirect` URL. The `redirect` URL is where the user will be redirected after approving or denying a request for authorization.

When a client is created, it will be issued a client ID and client secret. These values will be used when requesting access tokens from your application. The client creation route will return the new client instance:

    const data = {
        name: 'Client Name',
        redirect: 'http://example.com/callback'
    };

    this.$http.post('/oauth/clients', data)
        .then(response => {
            console.log(response.data);
        })
        .catch (response => {
            // List errors on response...
        });

#### `PUT /oauth/clients/{client-id}`

This route is used to update clients. It requires two pieces of data: the client's `name` and a `redirect` URL. The `redirect` URL is where the user will be redirected after approving or denying a request for authorization. The route will return the updated client instance:

    const data = {
        name: 'New Client Name',
        redirect: 'http://example.com/callback'
    };

    this.$http.put('/oauth/clients/' + clientId, data)
        .then(response => {
            console.log(response.data);
        })
        .catch (response => {
            // List errors on response...
        });

#### `DELETE /oauth/clients/{client-id}`

This route is used to delete clients:

    this.$http.delete('/oauth/clients/' + clientId)
        .then(response => {
            //
        });

<a name="requesting-tokens"></a>
### Requesting Tokens

#### Redirecting For Authorization

Once a client has been created, developers may use their client ID and secret to request an authorization code and access token from your application. First, the consuming application should make a redirect request to your application's `/oauth/authorize` route like so:

    Route::get('/redirect', function () {
        $query = http_build_query([
            'client_id' => 'client-id',
            'redirect_uri' => 'http://example.com/callback',
            'response_type' => 'code',
            'scope' => '',
        ]);

        return redirect('http://your-app.com/oauth/authorize?'.$query);
    });

> {tip} Remember, the `/oauth/authorize` route is already defined by the `Passport::routes` method. You do not need to manually define this route.

#### Approving The Request

When receiving authorization requests, Passport will automatically display a template to the user allowing them to approve or deny the authorization request. If they approve the request, they will be redirected back to the `redirect_uri` that was specified by the consuming application. The `redirect_uri` must match the `redirect` URL that was specified when the client was created.

If you would like to customize the authorization approval screen, you may publish Passport's views using the `vendor:publish` Artisan command. The published views will be placed in `resources/views/vendor/passport`:

    php artisan vendor:publish --tag=passport-views

#### Converting Authorization Codes To Access Tokens

If the user approves the authorization request, they will be redirected back to the consuming application. The consumer should then issue a `POST` request to your application to request an access token. The request should include the authorization code that was issued by your application when the user approved the authorization request. In this example, we'll use the Guzzle HTTP library to make the `POST` request:

    Route::get('/callback', function (Request $request) {
        $http = new GuzzleHttp\Client;

        $response = $http->post('http://your-app.com/oauth/token', [
            'form_params' => [
                'grant_type' => 'authorization_code',
                'client_id' => 'client-id',
                'client_secret' => 'client-secret',
                'redirect_uri' => 'http://example.com/callback',
                'code' => $request->code,
            ],
        ]);

        return json_decode((string) $response->getBody(), true);
    });

This `/oauth/token` route will return a JSON response containing `access_token`, `refresh_token`, and `expires_in` attributes. The `expires_in` attribute contains the number of seconds until the access token expires.

> {tip} Like the `/oauth/authorize` route, the `/oauth/token` route is defined for you by the `Passport::routes` method. There is no need to manually define this route.

<a name="refreshing-tokens"></a>
### Refreshing Tokens

If your application issues short-lived access tokens, users will need to refresh their access tokens via the refresh token that was provided to them when the access token was issued. In this example, we'll use the Guzzle HTTP library to refresh the token:

    $http = new GuzzleHttp\Client;

    $response = $http->post('http://your-app.com/oauth/token', [
        'form_params' => [
            'grant_type' => 'refresh_token',
            'refresh_token' => 'the-refresh-token',
            'client_id' => 'client-id',
            'client_secret' => 'client-secret',
            'scope' => '',
        ],
    ]);

    return json_decode((string) $response->getBody(), true);

This `/oauth/token` route will return a JSON response containing `access_token`, `refresh_token`, and `expires_in` attributes. The `expires_in` attribute contains the number of seconds until the access token expires.

<a name="password-grant-tokens"></a>
## Password Grant Tokens

The OAuth2 password grant allows your other first-party clients, such as a mobile application, to obtain an access token using an e-mail address / username and password. This allows you to issue access tokens securely to your first-party clients without requiring your users to go through the entire OAuth2 authorization code redirect flow.

<a name="creating-a-password-grant-client"></a>
### Creating A Password Grant Client

Before your application can issue tokens via the password grant, you will need to create a password grant client. You may do this using the `passport:client` command with the `--password` option. If you have already run the `passport:install` command, you do not need to run this command:

    php artisan passport:client --password

<a name="requesting-password-grant-tokens"></a>
### Requesting Tokens

Once you have created a password grant client, you may request an access token by issuing a `POST` request to the `/oauth/token` route with the user's email address and password. Remember, this route is already registered by the `Passport::routes` method so there is no need to define it manually. If the request is successful, you will receive an `access_token` and `refresh_token` in the JSON response from the server:

    $http = new GuzzleHttp\Client;

    $response = $http->post('http://your-app.com/oauth/token', [
        'form_params' => [
            'grant_type' => 'password',
            'client_id' => 'client-id',
            'client_secret' => 'client-secret',
            'username' => 'taylor@laravel.com',
            'password' => 'my-password',
            'scope' => '',
        ],
    ]);

    return json_decode((string) $response->getBody(), true);

> {tip} Remember, access tokens are long-lived by default. However, you are free to [configure your maximum access token lifetime](#configuration) if needed.

<a name="requesting-all-scopes"></a>
### Requesting All Scopes

When using the password grant, you may wish to authorize the token for all of the scopes supported by your application. You can do this by requesting the `*` scope. If you request the `*` scope, the `can` method on the token instance will always return `true`. This scope may only be assigned to a token that is issued using the `password` grant:

    $response = $http->post('http://your-app.com/oauth/token', [
        'form_params' => [
            'grant_type' => 'password',
            'client_id' => 'client-id',
            'client_secret' => 'client-secret',
            'username' => 'taylor@laravel.com',
            'password' => 'my-password',
            'scope' => '*',
        ],
    ]);

<a name="implicit-grant-tokens"></a>
## Implicit Grant Tokens

The implicit grant is similar to the authorization code grant; however, the token is returned to the client without exchanging an authorization code. This grant is most commonly used for JavaScript or mobile applications where the client credentials can't be securely stored. To enable the grant, call the `enableImplicitGrant` method in your `AuthServiceProvider`:

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

Once a grant has been enabled, developers may use their client ID to request an access token from your application. The consuming application should make a redirect request to your application's `/oauth/authorize` route like so:

    Route::get('/redirect', function () {
        $query = http_build_query([
            'client_id' => 'client-id',
            'redirect_uri' => 'http://example.com/callback',
            'response_type' => 'token',
            'scope' => '',
        ]);

        return redirect('http://your-app.com/oauth/authorize?'.$query);
    });

> {tip} Remember, the `/oauth/authorize` route is already defined by the `Passport::routes` method. You do not need to manually define this route.

<a name="personal-access-tokens"></a>
## Personal Access Tokens

Sometimes, your users may want to issue access tokens to themselves without going through the typical authorization code redirect flow. Allowing users to issue tokens to themselves via your application's UI can be useful for allowing users to experiment with your API or may serve as a simpler approach to issuing access tokens in general.

> {note} Personal access tokens are always long-lived. Their lifetime is not modified when using the `tokensExpireIn` or `refreshTokensExpireIn` methods.

<a name="creating-a-personal-access-client"></a>
### Creating A Personal Access Client

Before your application can issue personal access tokens, you will need to create a personal access client. You may do this using the `passport:client` command with the `--personal` option. If you have already run the `passport:install` command, you do not need to run this command:

    php artisan passport:client --personal

<a name="managing-personal-access-tokens"></a>
### Managing Personal Access Tokens

Once you have created a personal access client, you may issue tokens for a given user using the `createToken` method on the `User` model instance. The `createToken` method accepts the name of the token as its first argument and an optional array of [scopes](#token-scopes) as its second argument:

    $user = App\User::find(1);

    // Creating a token without scopes...
    $token = $user->createToken('Token Name')->accessToken;

    // Creating a token with scopes...
    $token = $user->createToken('My Token', ['place-orders'])->accessToken;

#### JSON API

Passport also includes a JSON API for managing personal access tokens. You may pair this with your own frontend to offer your users a dashboard for managing personal access tokens. Below, we'll review all of the API endpoints for managing personal access tokens. For convenience, we'll use [Vue](https://vuejs.org) to demonstrate making HTTP requests to the endpoints.

> {tip} If you don't want to implement the personal access token frontend yourself, you can use the [frontend quickstart](#frontend-quickstart) to have a fully functional frontend in a matter of minutes.

#### `GET /oauth/scopes`

This route returns all of the [scopes](#token-scopes) defined for your application. You may use this route to list the scopes a user may assign to a personal access token:

    this.$http.get('/oauth/scopes')
        .then(response => {
            console.log(response.data);
        });

#### `GET /oauth/personal-access-tokens`

This route returns all of the personal access tokens that the authenticated user has created. This is primarily useful for listing all of the user's token so that they may edit or delete them:

    this.$http.get('/oauth/personal-access-tokens')
        .then(response => {
            console.log(response.data);
        });

#### `POST /oauth/personal-access-tokens`

This route creates new personal access tokens. It requires two pieces of data: the token's `name` and the `scopes` that should be assigned to the token:

    const data = {
        name: 'Token Name',
        scopes: []
    };

    this.$http.post('/oauth/personal-access-tokens', data)
        .then(response => {
            console.log(response.data.accessToken);
        })
        .catch (response => {
            // List errors on response...
        });

#### `DELETE /oauth/personal-access-tokens/{token-id}`

This route may be used to delete personal access tokens:

    this.$http.delete('/oauth/personal-access-tokens/' + tokenId);

<a name="protecting-routes"></a>
## Protecting Routes

<a name="via-middleware"></a>
### Via Middleware

Passport includes an [authentication guard](/docs/{{version}}/authentication#adding-custom-guards) that will validate access tokens on incoming requests. Once you have configured the `api` guard to use the `passport` driver, you only need to specify the `auth:api` middleware on any routes that require a valid access token:

    Route::get('/user', function () {
        //
    })->middleware('auth:api');

<a name="passing-the-access-token"></a>
### Passing The Access Token

When calling routes that are protected by Passport, your application's API consumers should specify their access token as a `Bearer` token in the `Authorization` header of their request. For example, when using the Guzzle HTTP library:

    $response = $client->request('GET', '/api/user', [
        'headers' => [
            'Accept' => 'application/json',
            'Authorization' => 'Bearer '.$accessToken,
        ],
    ]);

<a name="token-scopes"></a>
## Token Scopes


<a name="defining-scopes"></a>
### Defining Scopes

Scopes allow your API clients to request a specific set of permissions when requesting authorization to access an account. For example, if you are building an e-commerce application, not all API consumers will need the ability to place orders. Instead, you may allow the consumers to only request authorization to access order shipment statuses. In other words, scopes allow your application's users to limit the actions a third-party application can perform on their behalf.

You may define your API's scopes using the `Passport::tokensCan` method in the `boot` method of your `AuthServiceProvider`. The `tokensCan` method accepts an array of scope names and scope descriptions. The scope description may be anything you wish and will be displayed to users on the authorization approval screen:

    use Laravel\Passport\Passport;

    Passport::tokensCan([
        'place-orders' => 'Place orders',
        'check-status' => 'Check order status',
    ]);

<a name="assigning-scopes-to-tokens"></a>
### Assigning Scopes To Tokens

#### When Requesting Authorization Codes

When requesting an access token using the authorization code grant, consumers should specify their desired scopes as the `scope` query string parameter. The `scope` parameter should be a space-delimited list of scopes:

    Route::get('/redirect', function () {
        $query = http_build_query([
            'client_id' => 'client-id',
            'redirect_uri' => 'http://example.com/callback',
            'response_type' => 'code',
            'scope' => 'place-orders check-status',
        ]);

        return redirect('http://your-app.com/oauth/authorize?'.$query);
    });

#### When Issuing Personal Access Tokens

If you are issuing personal access tokens using the `User` model's `createToken` method, you may pass the array of desired scopes as the second argument to the method:

    $token = $user->createToken('My Token', ['place-orders'])->accessToken;

<a name="checking-scopes"></a>
### Checking Scopes

Passport includes two middleware that may be used to verify that an incoming request is authenticated with a token that has been granted a given scope. To get started, add the following middleware to the `$routeMiddleware` property of your `app/Http/Kernel.php` file:

    'scopes' => \Laravel\Passport\Http\Middleware\CheckScopes::class,
    'scope' => \Laravel\Passport\Http\Middleware\CheckForAnyScope::class,

#### Check For All Scopes

The `scopes` middleware may be assigned to a route to verify that the incoming request's access token has *all* of the listed scopes:

    Route::get('/orders', function () {
        // Access token has both "check-status" and "place-orders" scopes...
    })->middleware('scopes:check-status,place-orders');

#### Check For Any Scopes

The `scope` middleware may be assigned to a route to verify that the incoming request's access token has *at least one* of the listed scopes:

    Route::get('/orders', function () {
        // Access token has either "check-status" or "place-orders" scope...
    })->middleware('scope:check-status,place-orders');

#### Checking Scopes On A Token Instance

Once an access token authenticated request has entered your application, you may still check if the token has a given scope using the `tokenCan` method on the authenticated `User` instance:

    use Illuminate\Http\Request;

    Route::get('/orders', function (Request $request) {
        if ($request->user()->tokenCan('place-orders')) {
            //
        }
    });

<a name="consuming-your-api-with-javascript"></a>
## Consuming Your API With JavaScript

When building an API, it can be extremely useful to be able to consume your own API from your JavaScript application. This approach to API development allows your own application to consume the same API that you are sharing with the world. The same API may be consumed by your web application, mobile applications, third-party applications, and any SDKs that you may publish on various package managers.

Typically, if you want to consume your API from your JavaScript application, you would need to manually send an access token to the application and pass it with each request to your application. However, Passport includes a middleware that can handle this for you. All you need to do is add the `CreateFreshApiToken` middleware to your `web` middleware group:

    'web' => [
        // Other middleware...
        \Laravel\Passport\Http\Middleware\CreateFreshApiToken::class,
    ],

This Passport middleware will attach a `laravel_token` cookie to your outgoing responses. This cookie contains an encrypted JWT that Passport will use to authenticate API requests from your JavaScript application. Now, you may make requests to your application's API without explicitly passing an access token:

    this.$http.get('/user')
        .then(response => {
            console.log(response.data);
        });

When using this method of authentication, you will need to send the CSRF token with every request via the `X-CSRF-TOKEN` header. Laravel will automatically send this header if you are using the default [Vue](https://vuejs.org) configuration that is included with the framework:

    Vue.http.interceptors.push((request, next) => {
        request.headers.set('X-CSRF-TOKEN', Laravel.csrfToken);

        next();
    });

> {note} If you are using a different JavaScript framework, you should make sure it is configured to send this header with every outgoing request.


<a name="events"></a>
## Events

Passport raises events when issuing access tokens and refresh tokens. You may use these events to prune or revoke other access tokens in your database. You may attach listeners to these events in your application's `EventServiceProvider`:

```php
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
```
