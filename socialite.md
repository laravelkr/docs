# 라라벨 Socialite(소셜로그인)

- [시작하기](#introduction)
- [설치하기](#installation)
- [Socialite 업그레이드하기](#upgrading-socialite)
- [설정하기](#configuration)
- [인증](#authentication)
    - [라우팅](#routing)
    - [옵션 파라미터](#optional-parameters)
    - [엑세스 스코프](#access-scopes)
- [상태를 유지하지 않는 인증](#stateless-authentication)
- [사용자 상세정보 조회하기](#retrieving-user-details)

## 시작하기

일반적인 Form을 기반으로한 인증에 더해서, 라라벨은 [라라벨 소셜라이트-Socialite](https://github.com/laravel/socialite)를 사용하여 OAuth 인증을 간단하고 편리하게 제공합니다. Socialite는 현재 페이스북, 트위터, 링크드인, 구글, 깃허브, 깃랩 그리고 Bitbucket을 기본적으로 지원하고 있습니다.

> {tip} 다른 플랫폼을 위한 어댑터는 커뮤니티에서 주도하는 [Socialite Providers](https://socialiteproviders.com/) 웹사이트에서 확인할 수 있습니다. (한국 사용자들이 많이 사용하는 카카오, 네이버, 라인등도 제공됩니다)

## 설치하기

Socialite를 사용하기 위해서는 컴포저를 사용하여 프로젝트에 의존성 패키지를 추가하십시오:

    composer require laravel/socialite

## Socialite 업그레이드하기

새로운 메이저 버전의 Socialite를 업그레이드 한다면, [업그레이드 가이드](https://github.com/laravel/socialite/blob/master/UPGRADE.md) 를 꼭 확인하시기 바랍니다.

## 설정하기

Socialite를 사용하기 전에, 애플리케이션에서 사용할 OAuth 공급자의 인증 정보를 추가해야합니다. 이 인증 정보는 `config/services.php` 설정 파일에서 추가하면 되며, 애플리케이션에서 필요한 서비스에 따라서, `facebook`, `twitter`, `linkedin`, `google`, `github`, `gitlab` 그리고 `bitbucket` 키를 사용해야 합니다. 다음의 예제를 보십시오.

    'github' => [
        'client_id' => env('GITHUB_CLIENT_ID'),
        'client_secret' => env('GITHUB_CLIENT_SECRET'),
        'redirect' => 'http://example.com/callback-url',
    ],

> {tip} `redirect` 옵션값에 상대경로가 포함된경우, 자동으로 Full URL로 인식됩니다.

## 인증

### 라우팅

OAuth 공급자를 사용하여 사용자를 인증하려면 두 가지 경로가 필요합니다. 하나는 사용자를 OAuth 공급자로 리다이렉션하기 위한 것이고 다른 하나는 인증 후 공급자로부터 콜백을 수신하기 위한 것입니다. 아래 예제 컨트롤러는 두 경로의 구현을 보여줍니다.

    use Laravel\Socialite\Facades\Socialite;

    Route::get('/auth/redirect', function () {
        return Socialite::driver('github')->redirect();
    });

    Route::get('/auth/callback', function () {
        $user = Socialite::driver('github')->user();

        // $user->token
    });

`Socialite` 파사드가 제공하는 `redirect` 메소드는 사용자를 OAuth 공급자로 리디이렉션하는 작업을 처리하는 반면 `user` 메소드는 수신 요청을 읽고 인증된 후 제공자로부터 사용자 정보를 검색합니다.

### 옵션 파라미터

몇몇 OAuth 공급자는 리다이렉트 요청에서 옵션 파라미터를 지원합니다. request에 옵션 파라미터를 포함하도록 하려면, 연관된 값을 배열로 `with` 메소드와 함께 호출하면 됩니다.

    use Laravel\Socialite\Facades\Socialite;

    return Socialite::driver('google')
        ->with(['hd' => 'example.com'])
        ->redirect();

> {note} `with` 메소드를 사용할 때, `state` 와 `response_type` 같은 예약된 키워드를 사용하지 않도록 주의하십시오.

### 엑세스 스코프

사용자를 리디렉션하기 전에 `scopes` 메소드를 사용하여 인증 요청에 추가 "scopes"를 추가할 수도 있습니다. 이 방법은 기존의 모든 스코프를 사용자가 제공하는 스코프와 병합(merge)합니다.

    use Laravel\Socialite\Facades\Socialite;

    return Socialite::driver('github')
        ->scopes(['read:user', 'public_repo'])
        ->redirect();

`setScopes` 메소드를 사용하면 인증요청의 기존에 존재하는 모든 스코프를 덮어 쓸 수 있습니다.

    return Socialite::driver('github')
        ->setScopes(['read:user', 'public_repo'])
        ->redirect();

## 사용자의 상세정보 조회하기

사용자 인스턴스를 획득하고 나면, 여기에서 몇가지 상세 정보를 획득할 수 있습니다.

사용자가 인증 콜백 경로로 다시 리다이렉션된 후 Socialite의 `user` 메서드를 사용하여 사용자의 세부 정보를 검색할 수 있습니다. `user` 메소드에 의해 반환된 사용자 객체는 사용자에 대한 정보를 자신의 데이터베이스에 저장하는 데 사용할 수 있는 다양한 속성과 메소드를 제공합니다. 인증하는 OAuth 공급자가 OAuth 1.0 또는 OAuth 2.0을 지원하는지 여부에 따라 다른 속성과 메서드를 사용할 수 있습니다.

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

#### 토큰으로 부터 사용자 상세정보 조회하기 (OAuth2)

사용자에 대한 유요한 엑세스 토큰을 가지고 있는 경우 Socialite의 `userFromToken` 메소드를 사용하여 상세 정보를 조회할 수 있습니다.

    use Laravel\Socialite\Facades\Socialite;

    $user = Socialite::driver('github')->userFromToken($token);

#### 토큰과 비밀번호를 사용하여 사용자 정보 조회하기 (OAuth1)

사용자의 유효한 토큰과 비밀번호를 가지고 있다면, `userFromTokenAndSecret` 메소드를 사용하여 사용자 정보를 조회할 수 있습니다.

    use Laravel\Socialite\Facades\Socialite;

    $user = Socialite::driver('twitter')->userFromTokenAndSecret($token, $secret);

#### 상태를 유지하지 않는 인증

`stateless` 메소드는 세션의 상태를 확인하지 않게 하도록 하기 위해 사용될 수 있습니다. 이는 소셜 로그인을 API에 추가할 때 유용합니다.

    use Laravel\Socialite\Facades\Socialite;

    return Socialite::driver('google')->stateless()->user();

> {note} OAuth 1.0을 인증에 사용하는 Twitter 드라이버에는 상태 비 저장 인증을 사용할 수 없습니다.