# Laravel Socialite
# 라라벨 소셜라이트(소셜로그인)

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Upgrading Socialite](#upgrading-socialite)
- [Socialite 업그레이드하기](#upgrading-socialite)
- [Installation](#installation)
- [설치하기](#installation)
- [Configuration](#configuration)
- [설정하기](#configuration)
- [Routing](#routing)
- [라우팅](#routing)
- [Optional Parameters](#optional-parameters)
- [옵션 파라미터](#optional-parameters)
- [Access Scopes](#access-scopes)
- [엑세스 스코프](#access-scopes)
- [Stateless Authentication](#stateless-authentication)
- [상태를 유지하지 않는 인증](#stateless-authentication)
- [Retrieving User Details](#retrieving-user-details)
- [사용자 상세정보 조회하기](#retrieving-user-details)

<a name="introduction"></a>
## Introduction
## 시작하기

In addition to typical, form based authentication, Laravel also provides a simple, convenient way to authenticate with OAuth providers using [Laravel Socialite](https://github.com/laravel/socialite). Socialite currently supports authentication with Facebook, Twitter, LinkedIn, Google, GitHub, GitLab and Bitbucket.

일반적인 Form을 기반으로한 인증에 더해서, 라라벨은 [라라벨 소셜라이트-Socialite](https://github.com/laravel/socialite)를 사용하여 OAuth 인증을 간단하고 편리하게 제공합니다. Socialite는 현재 페이스북, 트위터, 링크드인, 구글, 깃허브, 깃랩 그리고 Bitbucket을 기본적으로 지원하고 있습니다.

> {tip} Adapters for other platforms are listed at the community driven [Socialite Providers](https://socialiteproviders.com/) website.

> {tip} 다른 플랫폼을 위한 어댑터는 커뮤니티에서 주도하는 [Socialite Providers](https://socialiteproviders.com/) 웹사이트에서 확인할 수 있습니다. (한국 사용자들이 많이 사용하는 카카오, 네이버, 라인등도 제공됩니다)

<a name="upgrading-socialite"></a>
## Upgrading Socialite
## Socialite 업그레이드하기

When upgrading to a new major version of Socialite, it's important that you carefully review [the upgrade guide](https://github.com/laravel/socialite/blob/master/UPGRADE.md).

새로운 메이저 버전의 Socialite 를 업그레이드 한다면, [업그레이드 가이드](https://github.com/laravel/socialite/blob/master/UPGRADE.md)를 꼭 확인하시기 바랍니다.

<a name="installation"></a>
## Installation
## 설치하기

To get started with Socialite, use Composer to add the package to your project's dependencies:

Socialite를 사용하기 위해서는 컴포저를 사용하여 프로젝트에 의존성 패키지를 추가하십시오:

    composer require laravel/socialite

<a name="configuration"></a>
## Configuration
## 설정하기

Before using Socialite, you will also need to add credentials for the OAuth services your application utilizes. These credentials should be placed in your `config/services.php` configuration file, and should use the key `facebook`, `twitter`, `linkedin`, `google`, `github`, `gitlab` or `bitbucket`, depending on the providers your application requires. For example:

Socialite를 사용하기 전에, 애플리케이션에서 사용할 OAuth서비스의 인증 정보를 추가해야합니다. 이 인증 정보는 `config/services.php` 설정 파일에서 추가하면 되며, 애플리케이션에서 필요한 서비스에 따라서, `facebook`, `twitter`, `linkedin`, `google`, `github`, `gitlab` 그리고 `bitbucket` 처럼 사용되야 합니다. 다음의 예제를 보십시오:

    'github' => [
        'client_id' => env('GITHUB_CLIENT_ID'),
        'client_secret' => env('GITHUB_CLIENT_SECRET'),
        'redirect' => 'http://your-callback-url',
    ],

> {tip} If the `redirect` option contains a relative path, it will automatically be resolved to a fully qualified URL.

> {tip} `redirect` 옵션값에 상대경로가 포함된경우, 자동으로 Full URL로 인식됩니다.

<a name="routing"></a>
## Routing
## 라우팅

Next, you are ready to authenticate users! You will need two routes: one for redirecting the user to the OAuth provider, and another for receiving the callback from the provider after authentication. We will access Socialite using the `Socialite` facade:

이제 사용자를 인증할 준비가 되었습니다! 이제 두개의 라우트가 필요합니다. 하나는 사용자를 OAuth 서비스로 리다이렉팅 하는 라우팅이고, 다른 하나는 인증후 받는 콜백입니다. `Socialite` 파사드를 사용하여 Socialite에 엑세스할 수 있습니다.

    <?php

    namespace App\Http\Controllers\Auth;

    use App\Http\Controllers\Controller;
    use Laravel\Socialite\Facades\Socialite;

    class LoginController extends Controller
    {
        /**
         * Redirect the user to the GitHub authentication page.
         *
         * @return \Illuminate\Http\Response
         */
        public function redirectToProvider()
        {
            return Socialite::driver('github')->redirect();
        }

        /**
         * Obtain the user information from GitHub.
         *
         * @return \Illuminate\Http\Response
         */
        public function handleProviderCallback()
        {
            $user = Socialite::driver('github')->user();

            // $user->token;
        }
    }

The `redirect` method takes care of sending the user to the OAuth provider, while the `user` method will read the incoming request and retrieve the user's information from the provider.

`redirect` 메소드는 사용자를 OAuth 서비스로 이동시키고, `user` 메소드는 유입되는 request를 읽어들여, 사용자 정보를 조회합니다.

You will need to define routes to your controller methods:

다음처럼 컨트롤러 메소드를 위한 라우를 정의해야 합니다.

    Route::get('login/github', 'Auth\LoginController@redirectToProvider');
    Route::get('login/github/callback', 'Auth\LoginController@handleProviderCallback');

<a name="optional-parameters"></a>
## Optional Parameters
## 옵션 파라미터

A number of OAuth providers support optional parameters in the redirect request. To include any optional parameters in the request, call the `with` method with an associative array:

몇몇 OAuth 프로바이더는 리다이렉트 요청에서 옵션 파라미터를 지원합니다. request에 옵션 파라미터를 포함하도록 하려면, 연관된 값을 배열로 `with` 메소드와 함께 호출하면 됩니다.

    return Socialite::driver('google')
        ->with(['hd' => 'example.com'])
        ->redirect();

> {note} When using the `with` method, be careful not to pass any reserved keywords such as `state` or `response_type`.

> {note} `with` 메소드를 사용할 때, `state` 와 `response_type` 같은 예약된 키워드를 사용하지 않도록 주의하십시오.

<a name="access-scopes"></a>
## Access Scopes
## 엑세스 스코프

Before redirecting the user, you may also add additional "scopes" on the request using the `scopes` method. This method will merge all existing scopes with the ones you supply:

사용자를 리다이렉팅하기 전에, `scopes` 메소드를 사용하여 request 에 "스코프"를 추가할 수 있습니다. 이 메소드는 기존에 존재하는 모든 스코프를 사용자가 제공하는 스코프와 머지(merge)합니다.

    return Socialite::driver('github')
        ->scopes(['read:user', 'public_repo'])
        ->redirect();

You can overwrite all existing scopes using the `setScopes` method:

`setScopes` 메소드를 사용하면 기존에 존재하는 모든 스코프를 덮어 쓸 수 있습니다.

    return Socialite::driver('github')
        ->setScopes(['read:user', 'public_repo'])
        ->redirect();

<a name="stateless-authentication"></a>
## Stateless Authentication
## 상태를 유지하지 않는 인증

The `stateless` method may be used to disable session state verification. This is useful when adding social authentication to an API:

`stateless` 메소드는 세션의 상태를 확인하지 않게 하도록 하기 위해 사용될 수 있습니다. 이는 소셜 로그인을 API에 추가할 때 유용합니다.

    return Socialite::driver('google')->stateless()->user();

> {note} Stateless authentication is not available for the Twitter driver, which uses OAuth 1.0 for authentication.

> {note} OAuth 1.0을 인증에 사용하는 Twitter 드라이버에는 상태 비 저장 인증을 사용할 수 없습니다.

<a name="retrieving-user-details"></a>
## Retrieving User Details
## 사용자의 상세정보 조회하기

Once you have a user instance, you can grab a few more details about the user:

사용자 인스턴스를 획득하고 나면, 여기에서 몇가지 상세 정보를 획득할 수 있습니다.

    $user = Socialite::driver('github')->user();

    // OAuth Two Providers
    $token = $user->token;
    $refreshToken = $user->refreshToken; // not always provided
    $expiresIn = $user->expiresIn;

    // OAuth One Providers
    $token = $user->token;
    $tokenSecret = $user->tokenSecret;

    // All Providers
    $user->getId();
    $user->getNickname();
    $user->getName();
    $user->getEmail();
    $user->getAvatar();

#### Retrieving User Details From A Token (OAuth2)
#### 토큰으로 부터 사용자 상세정보 조회하기 (OAuth2)

If you already have a valid access token for a user, you can retrieve their details using the `userFromToken` method:

사용자에 대한 유요한 엑세스 토큰을 가지고 있는 경우 `userFromToken` 메소드를 사용하여 상세 정보를 조회할 수 있습니다.

    $user = Socialite::driver('github')->userFromToken($token);

#### Retrieving User Details From A Token And Secret (OAuth1)
#### 토큰과 비밀번호를 사용하여 사용자 정보 조회하기 (OAuth1)

If you already have a valid pair of token / secret for a user, you can retrieve their details using the `userFromTokenAndSecret` method:

사용자의 유효한 토큰과 비밀번호를 가지고 있다면, `userFromTokenAndSecret` 메소드를 사용하여 사용자 정보를 조회할 수 있습니다.

    $user = Socialite::driver('twitter')->userFromTokenAndSecret($token, $secret);
