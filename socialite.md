# 라라벨 소셜라이트(소셜로그인)

- [시작하기](#introduction)
- [Socialite 업그레이드하기](#upgrading-socialite)
- [설치하기](#installation)
- [설정하기](#configuration)
- [라우팅](#routing)
- [옵션 파라미터](#optional-parameters)
- [엑세스 스코프](#access-scopes)
- [상태를 유지하지 않는 인증](#stateless-authentication)
- [사용자 상세정보 조회하기](#retrieving-user-details)

<a name="introduction"></a>
## 시작하기

일반적인 Form을 기반으로한 인증에 더해서, 라라벨은 [라라벨 소셜라이트-Socialite](https://github.com/laravel/socialite)를 사용하여 OAuth 인증을 간단하고 편리하게 제공합니다. Socialite는 현재 페이스북, 트위터, 링크드인, 구글, 깃허브, 깃랩 그리고 Bitbucket을 기본적으로 지원하고 있습니다.

> {tip} 다른 플랫폼을 위한 어댑터는 커뮤니티에서 주도하는 [Socialite Providers](https://socialiteproviders.com/) 웹사이트에서 확인할 수 있습니다. (한국 사용자들이 많이 사용하는 카카오, 네이버, 라인등도 제공됩니다)

<a name="upgrading-socialite"></a>
## Socialite 업그레이드하기

새로운 메이저 버전의 Socialite 를 업그레이드 한다면, [업그레이드 가이드](https://github.com/laravel/socialite/blob/master/UPGRADE.md)를 꼭 확인하시기 바랍니다.

<a name="installation"></a>
## 설치하기

Socialite를 사용하기 위해서는 컴포저를 사용하여 프로젝트에 의존성 패키지를 추가하십시오:

    composer require laravel/socialite

<a name="configuration"></a>
## 설정하기

Socialite를 사용하기 전에, 애플리케이션에서 사용할 OAuth서비스의 인증 정보를 추가해야합니다. 이 인증 정보는 `config/services.php` 설정 파일에서 추가하면 되며, 애플리케이션에서 필요한 서비스에 따라서, `facebook`, `twitter`, `linkedin`, `google`, `github`, `gitlab` 그리고 `bitbucket` 처럼 사용되야 합니다. 다음의 예제를 보십시오:

    'github' => [
        'client_id' => env('GITHUB_CLIENT_ID'),
        'client_secret' => env('GITHUB_CLIENT_SECRET'),
        'redirect' => 'http://your-callback-url',
    ],

> {tip} `redirect` 옵션값에 상대경로가 포함된경우, 자동으로 Full URL로 인식됩니다.

<a name="routing"></a>
## 라우팅

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

`redirect` 메소드는 사용자를 OAuth 서비스로 이동시키고, `user` 메소드는 유입되는 request를 읽어들여, 사용자 정보를 조회합니다.

다음처럼 컨트롤러 메소드를 위한 라우를 정의해야 합니다.

    Route::get('login/github', 'Auth\LoginController@redirectToProvider');
    Route::get('login/github/callback', 'Auth\LoginController@handleProviderCallback');

<a name="optional-parameters"></a>
## 옵션 파라미터

몇몇 OAuth 프로바이더는 리다이렉트 요청에서 옵션 파라미터를 지원합니다. request에 옵션 파라미터를 포함하도록 하려면, 연관된 값을 배열로 `with` 메소드와 함께 호출하면 됩니다.

    return Socialite::driver('google')
        ->with(['hd' => 'example.com'])
        ->redirect();

> {note} `with` 메소드를 사용할 때, `state` 와 `response_type` 같은 예약된 키워드를 사용하지 않도록 주의하십시오.

<a name="access-scopes"></a>
## 엑세스 스코프

사용자를 리다이렉팅하기 전에, `scopes` 메소드를 사용하여 request 에 "스코프"를 추가할 수 있습니다. 이 메소드는 기존에 존재하는 모든 스코프를 사용자가 제공하는 스코프와 머지(merge)합니다.

    return Socialite::driver('github')
        ->scopes(['read:user', 'public_repo'])
        ->redirect();

`setScopes` 메소드를 사용하면 기존에 존재하는 모든 스코프를 덮어 쓸 수 있습니다.

    return Socialite::driver('github')
        ->setScopes(['read:user', 'public_repo'])
        ->redirect();

<a name="stateless-authentication"></a>
## 상태를 유지하지 않는 인증

`stateless` 메소드는 세션의 상태를 확인하지 않게 하도록 하기 위해 사용될 수 있습니다. 이는 소셜 로그인을 API에 추가할 때 유용합니다.

    return Socialite::driver('google')->stateless()->user();

> {note} OAuth 1.0을 인증에 사용하는 Twitter 드라이버에는 상태 비 저장 인증을 사용할 수 없습니다.

<a name="retrieving-user-details"></a>
## 사용자의 상세정보 조회하기

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

#### 토큰으로 부터 사용자 상세정보 조회하기 (OAuth2)

사용자에 대한 유요한 엑세스 토큰을 가지고 있는 경우 `userFromToken` 메소드를 사용하여 상세 정보를 조회할 수 있습니다.

    $user = Socialite::driver('github')->userFromToken($token);

#### 토큰과 비밀번호를 사용하여 사용자 정보 조회하기 (OAuth1)

사용자의 유효한 토큰과 비밀번호를 가지고 있다면, `userFromTokenAndSecret` 메소드를 사용하여 사용자 정보를 조회할 수 있습니다.

    $user = Socialite::driver('twitter')->userFromTokenAndSecret($token, $secret);
