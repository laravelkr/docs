# Laravel Socialite
# 라라벨 Socialite(소셜로그인)

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Installation](#installation)
- [설치하기](#installation)
- [Upgrading Socialite](#upgrading-socialite)
- [Socialite 업그레이드하기](#upgrading-socialite)
- [Configuration](#configuration)
- [설정하기](#configuration)
- [Authentication](#authentication)
- [인증](#authentication)
    - [Routing](#routing)
    - [라우팅](#routing)
    - [Authentication & Storage](#authentication-and-storage)
    - [Authentication & Storage](#authentication-and-storage)
    - [Access Scopes](#access-scopes)
    - [스코프 접근하기](#access-scopes)
    - [Optional Parameters](#optional-parameters)
    - [옵션 파라미터](#optional-parameters)
- [Retrieving User Details](#retrieving-user-details)
- [사용자 상세정보 조회하기](#retrieving-user-details)

## Introduction
## 시작하기

In addition to typical, form based authentication, Laravel also provides a simple, convenient way to authenticate with OAuth providers using [Laravel Socialite](https://github.com/laravel/socialite). Socialite currently supports authentication via Facebook, Twitter, LinkedIn, Google, GitHub, GitLab, and Bitbucket.

일반적인 Form을 기반으로한 인증에 더해서, 라라벨은 [라라벨 소셜라이트-Socialite](https://github.com/laravel/socialite)를 사용하여 OAuth 인증을 간단하고 편리하게 제공합니다. Socialite는 현재 페이스북, 트위터, 링크드인, 구글, 깃허브, 깃랩 그리고 Bitbucket을 기본적으로 지원하고 있습니다.

> {tip} Adapters for other platforms are available via the community driven [Socialite Providers](https://socialiteproviders.com/) website.

> {tip} 다른 플랫폼을 위한 어댑터는 커뮤니티에서 주도하는 [Socialite Providers](https://socialiteproviders.com/) 웹사이트에서 확인할 수 있습니다. (한국 사용자들이 많이 사용하는 카카오, 네이버, 라인등도 제공됩니다)

## Installation
## 설치하기

To get started with Socialite, use Composer to add the package to your project's dependencies:

Socialite를 사용하기 위해서는 컴포저를 사용하여 프로젝트에 의존성 패키지를 추가하십시오:

```shell
composer require laravel/socialite
```

## Upgrading Socialite
## Socialite 업그레이드하기

When upgrading to a new major version of Socialite, it's important that you carefully review [the upgrade guide](https://github.com/laravel/socialite/blob/master/UPGRADE.md).

새로운 메이저 버전의 Socialite를 업그레이드 한다면, [업그레이드 가이드](https://github.com/laravel/socialite/blob/master/UPGRADE.md) 를 꼭 확인하시기 바랍니다.

## Configuration
## 설정하기

Before using Socialite, you will need to add credentials for the OAuth providers your application utilizes. Typically, these credentials may be retrieved by creating a "developer application" within the dashboard of the service you will be authenticating with.

Socialite를 사용하기 전에 애플리케이션에서 사용할 OAuth 공급자의 인증 정보를 추가해야합니다. 일반적으로 이 인증정보는 인증 서비스의 대시보드의 "개발 애플리케이션" 부분에서 찾을 수 있습니다. 

These credentials should be placed in your application's `config/services.php` configuration file, and should use the key `facebook`, `twitter` (OAuth 1.0), `twitter-oauth-2` (OAuth 2.0), `linkedin`, `google`, `github`, `gitlab`, or `bitbucket`, depending on the providers your application requires:

인증 정보는 `config/services.php` 설정 파일에서 추가하면 되며, 애플리케이션에서 필요한 서비스에 따라서, `facebook`, `twitter` (OAuth 1.0), `twitter-oauth-2` (OAuth 2.0), `linkedin`, `google`, `github`, `gitlab`, `bitbucket` 키를 사용해야 합니다. 다음의 예제를 보십시오.

    'github' => [
        'client_id' => env('GITHUB_CLIENT_ID'),
        'client_secret' => env('GITHUB_CLIENT_SECRET'),
        'redirect' => 'http://example.com/callback-url',
    ],

> {tip} If the `redirect` option contains a relative path, it will automatically be resolved to a fully qualified URL.

> {tip} `redirect` 옵션값에 상대경로가 포함된경우, 자동으로 Full URL로 인식됩니다.

## Authentication
## 인증

### Routing
### 라우팅

To authenticate users using an OAuth provider, you will need two routes: one for redirecting the user to the OAuth provider, and another for receiving the callback from the provider after authentication. The example routes below demonstrate the implementation of both routes:

OAuth 공급자를 사용하여 사용자를 인증하려면 두 가지 경로가 필요합니다. 하나는 사용자를 OAuth 공급자로 리다이렉션하기 위한 것이고 다른 하나는 인증 후 공급자로부터 콜백을 수신하기 위한 것입니다. 다음의 예시는 두개의 라우트를 경로한 것입니다.

    use Laravel\Socialite\Facades\Socialite;

    Route::get('/auth/redirect', function () {
        return Socialite::driver('github')->redirect();
    });

    Route::get('/auth/callback', function () {
        $user = Socialite::driver('github')->user();

        // $user->token
    });

The `redirect` method provided by the `Socialite` facade takes care of redirecting the user to the OAuth provider, while the `user` method will examine the incoming request and retrieve the user's information from the provider after they have approved the authentication request.

`Socialite` 파사드가 제공하는 `redirect` 메소드는 사용자를 OAuth 공급자의 페이지로 리디이렉션하는 작업을 처리하는 반면 `user` 메소드는 유입되는 요청을 읽어들여 인증을 처리한 뒤에 공급자로부터 사용자 정보를 조회합니다.

<a name="authentication-and-storage"></a>
### Authentication & Storage
### 인증과 저장

Once the user has been retrieved from the OAuth provider, you may determine if the user exists in your application's database and [authenticate the user](/docs/{{version}}/authentication#authenticate-a-user-instance). If the user does not exist in your application's database, you will typically create a new record in your database to represent the user:

OAuth 공급자로부터 사용자를 조회한 뒤에, 애플리케이션 데이터베이스에 사용자 데이터가 존재하는지 확인하고 [사용자 인증](/docs/{{version}}/authentication#authenticate-a-user-instance)을 처리할 수 있습니다. 사용자 데이터가 데이터베이스에서 찾을 수 없다면 이 데이터를 기반으로 데이터베이스에 새로운 레코드를 추가합니다.  

    use App\Models\User;
    use Illuminate\Support\Facades\Auth;
    use Laravel\Socialite\Facades\Socialite;

    Route::get('/auth/callback', function () {
        $githubUser = Socialite::driver('github')->user();

        $user = User::updateOrCreate([
            'github_id' => $githubUser->id,
        ], [
            'name' => $githubUser->name,
            'email' => $githubUser->email,
            'github_token' => $githubUser->token,
            'github_refresh_token' => $githubUser->refreshToken,
        ]);

        Auth::login($user);

        return redirect('/dashboard');
    });

> {tip} For more information regarding what user information is available from specific OAuth providers, please consult the documentation on [retrieving user details](#retrieving-user-details).

> {tip} 특정 OAuth 공급자가 제공하는 사용자의 정보에 대한 보다 자세한 내용은 [사용자의 상세정보 조회하기](#retrieving-user-details) 문서를 참고하십시오.

<a name="access-scopes"></a>
### Access Scopes
### 스코프 접근하기

Before redirecting the user, you may use the `scopes` method to specify the "scopes" that should be included in the authentication request. This method will merge all previously specified scopes with the scopes that you specify:

사용자를 리다이렉션 하기 전에 `scopes` 메소드를 사용하여 인증 요청에 포함되어야할 "scopes"를 지정할 수 있습니다. 이 메소드는 이전에 지정된 모든 scope 와 병합(merge)됩니다.

    use Laravel\Socialite\Facades\Socialite;

    return Socialite::driver('github')
        ->scopes(['read:user', 'public_repo'])
        ->redirect();

You can overwrite all existing scopes on the authentication request using the `setScopes` method:

`setScopes` 메소드를 사용하면 인증요청의 기존에 존재하는 모든 스코프를 덮어 쓸 수 있습니다.

    return Socialite::driver('github')
        ->setScopes(['read:user', 'public_repo'])
        ->redirect();

### Optional Parameters
### 옵션 파라미터

A number of OAuth providers support other optional parameters in the redirect request. To include any optional parameters in the request, call the `with` method with an associative array:

몇몇 OAuth 공급자는 리다이렉트 요청에서 다른 옵션 파라미터를 지원합니다. Oauth 공급자에게 보내는 요청에 옵션 파라미터를 포함하려면, `with` 메소드에 필요한 값을 배열로 호출하면 됩니다.

    use Laravel\Socialite\Facades\Socialite;

    return Socialite::driver('google')
        ->with(['hd' => 'example.com'])
        ->redirect();

> {note} When using the `with` method, be careful not to pass any reserved keywords such as `state` or `response_type`.

> {note} `with` 메소드를 사용할 때, `state` 와 `response_type` 같이 사전에 예약된 키워드를 사용하지 않도록 주의하십시오.

## Retrieving User Details
## 사용자의 상세정보 조회하기

After the user is redirected back to your application's authentication callback route, you may retrieve the user's details using Socialite's `user` method. The user object returned by the `user` method provides a variety of properties and methods you may use to store information about the user in your own database.

사용자가 애플리케이션의 인증 콜백 라우트로 다시 리다이렉트 된 다음에 Socialite 의 `user` 메소드를 사용해서 사용자의 상세정보를 조회할 수 있습니다. `user` 메소드는 사용자 객체를 반환합니다. 이 객체는 사용자 정보를 데이터베이스에 저장하는데 쓸 수 있는 메소드를 가지고 있고, 상세성조를 조회할 수 있는 다양한 속성을 가지고 있습니다.

Differing properties and methods may be available on this object depending on whether the OAuth provider you are authenticating with supports OAuth 1.0 or OAuth 2.0:

인증하는 OAuth 공급자가 OAuth 1.0 을 지원하는지, OAuth 2.0 을 지원하는지에 따라 이 객체에서 사용할 수 있는 속성과 메소드가 다릅니다. 

    use Laravel\Socialite\Facades\Socialite;

    Route::get('/auth/callback', function () {
        $user = Socialite::driver('github')->user();

        // OAuth 2.0 providers...
        $token = $user->token;
        $refreshToken = $user->refreshToken;
        $expiresIn = $user->expiresIn;

        // OAuth 1.0 providers...
        $token = $user->token;
        $tokenSecret = $user->tokenSecret;

        // All providers...
        $user->getId();
        $user->getNickname();
        $user->getName();
        $user->getEmail();
        $user->getAvatar();
    });

#### Retrieving User Details From A Token (OAuth2)
#### 토큰으로 부터 사용자 상세정보 조회하기 (OAuth2)

If you already have a valid access token for a user, you can retrieve their user details using Socialite's `userFromToken` method:

사용자에 대한 유요한 엑세스 토큰을 가지고 있는 경우 Socialite의 `userFromToken` 메소드를 사용하여 사용자의 상세 정보를 조회할 수 있습니다.

    use Laravel\Socialite\Facades\Socialite;

    $user = Socialite::driver('github')->userFromToken($token);

#### Retrieving User Details From A Token And Secret (OAuth1)
#### 토큰과 비밀번호를 사용하여 사용자 정보 조회하기 (OAuth1)

If you already have a valid token and secret for a user, you can retrieve their user details using Socialite's `userFromTokenAndSecret` method:

사용자의 유효한 토큰과 비밀번호를 가지고 있다면, `userFromTokenAndSecret` 메소드를 사용하여 사용자의 상세 정보를 조회할 수 있습니다.

    use Laravel\Socialite\Facades\Socialite;

    $user = Socialite::driver('twitter')->userFromTokenAndSecret($token, $secret);

#### Stateless Authentication
#### 상태를 유지하지 않는 인증

The `stateless` method may be used to disable session state verification. This is useful when adding social authentication to a stateless API that does not utilize cookie based sessions:

`stateless` 메소드는 세션의 상태를 확인하지 않게 하도록 하기 위해 사용될 수 있습니다. 이는 소셜 로그인을 세션을 기반으로한 쿠키를 사용하지 않는 상태를 유지하지 않는 API에 추가할 때 유용합니다.

    use Laravel\Socialite\Facades\Socialite;

    return Socialite::driver('google')->stateless()->user();

> {note} Stateless authentication is not available for the Twitter OAuth 1.0 driver.
> 
> {note} Twitter OAuth 1.0 드라이버에는 상태 비 저장 인증을 사용할 수 없습니다.