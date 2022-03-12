# 라라벨 Sanctum

- [시작하기](#introduction)
    - [작동 원리](#how-it-works)
- [설치하기](#installation)
- [설정](#configuration)
- [기본 모델 재정의](#overriding-default-models)
- [API 토큰 인증](#api-token-authentication)
    - [API 토큰 발행하기](#issuing-api-tokens)
    - [토큰 권한(Token Abilities)](#token-abilities)
    - [라우트 보호하기](#protecting-routes)
    - [토큰 해지하기](#revoking-tokens)
- [SPA 인증](#spa-authentication)
    - [설정](#spa-configuration)
    - [인증하기](#spa-authenticating)
    - [라우트 보호하기](#protecting-spa-routes)
    - [Private 브로드캐스트 채널 승인하기](#authorizing-private-broadcast-channels)
- [모바일 어플리케이션 인증](#mobile-application-authentication)
    - [API 토큰 발행하기](#issuing-mobile-api-tokens)
    - [라우트 보호하기](#protecting-mobile-api-routes)
    - [토큰 해지하기](#revoking-mobile-api-tokens)
- [테스트하기](#testing)

<a name="introduction"></a>
## 시작하기

[라라벨 Sanctum](https://github.com/laravel/sanctum) 은 SPAs(싱글 페이지 애플리케이션), 모바일 애플리케이션과 간단한 토큰 기반의 API를 위한 패더급(경량) 인증 시스템을 제공합니다. Sanctum을 사용하면 애플리케이션의 각 사용자가 자신의 계정에 대해 여러 개의 API 토큰을 생성 할 수 있습니다. 이 토큰에는 토큰이 수행할 수 있는 권한 / 범위가 부여될 수 있습니다.

<a name="how-it-works"></a>
### 작동 원리

라라벨 Sanctum은 두 가지 문제를 해결하기 위해 존재합니다. 라이브러리에 대해 더 자세히 알아보기 전에 각각에 대해 살펴 보겠습니다.

<a name="how-it-works-api-tokens"></a>
#### API 토큰

첫 번째, Sanctum은 OAuth의 복잡함 없이 사용자에게 API 토큰을 발행하는 데 사용할 수 있는 단순한 패키지입니다. 이 기능은 "개인 액세스 토큰(personal access tokens)"을 발행하는 GitHub 및 기타 애플리케이션에서 영감을 얻었습니다. 예를 들어서, "계정 설정(account settings)"에 사용자가 자신의 계정에 대한 API 토큰을 생성할 수 있는 어떤 화면을 상상해 보세요. Sanctum을 사용하여 해당 토큰을 생성하고 관리 할 수 있습니다. 이러한 토큰들은 매우 긴 만료일(몇 년)을 가지고 있지만, 어느 때라도 사용자에 의해서 해지(revoke) 할 수도 있습니다.

라라벨 Sanctum은 단일 데이터베이스 테이블 내에 사용자 API 토큰을 저장하고, 유효한 API 토큰을 포함해야하는 `Authorization` 헤더를 통해 오는 HTTP 요청들(requests)을 인증함으로써 이 기능을 제공합니다.

<a name="how-it-works-spa-authentication"></a>
#### SPA 인증

두번째, Sanctum은 라라벨 기반 API와 통신해야하는 싱글 페이지 애플리케이션(SPAs)을 인증하는 간단한 방법을 제공하기 위해 만들어졌습니다. SPA는 라라벨 애플리케이션과 같은 저장소(repository)에 있거나, Vue CLI 또는 Next.js 애플리케이션을 사용해 생성 된 SPA처럼 전체가 분리된 저장소에 있을 수도 있습니다.

이 기능을 위해서 Sanctum은 어떤 종류의 토큰도 사용하지 않습니다. 대신, Sanctum은 라라벨에 내장(built-in) 된 쿠키 기반의 세션 인증 서비스를 사용합니다. 일반적으로 Sanctum은 이를 수행하기 위해 라라벨 `web` 인증 가드를 사용합니다. 이것은 CSRF 보호, 세션 인증이라는 이점을 제공할 뿐만 아니라 XSS를 통한 인증 자격 증명(authentication credentials)의 유출을 방어합니다.

Sanctum은 들어오는 요청이 자신의 SPA 프론트엔드에서 시작된 경우에만 쿠키를 사용하여 인증을 시도합니다. Sanctum은 들어오는 HTTP 요청을 검사할 때 먼저 인증 쿠키를 확인하고, 쿠키가 없으면 Sanctum은 유효한 API 토큰에 대한 `Authorization` 헤더를 검사합니다.

> {tip} Sanctum은 API 토큰 인증에만 사용하거나 SPA 인증에만 사용하는 것이 좋습니다. Sanctum을 사용한다고해서 제공되는 두 기능을 모두 사용해야하는 것은 아닙니다.

<a name="installation"></a>
## 설치하기

> {tip} 최신 버전의 라라벨에는 이미 라라벨 Sanctum이 포함되어 있습니다. 그러나 애플리케이션의 `composer.json` 파일에 `laravel/sanctum`이 포함되어 있지 않은 경우 아래 설치 지침을 따를 수 있습니다.

여러분은 컴포져 패키지 관리자를 통해 라라벨 Sanctum을 설치할 수 있습니다.

    composer require laravel/sanctum

다음으로, Sanctum의 설정을 내보내고(publish), `vendor:publish` 아티즌 명령어를 사용해서 파일을 마이그레이션 합니다. `sanctum` 설정 파일은 애플리케이션의 `config` 디렉토리에 위치하게 됩니다.

    php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"

마지막으로, 데이터베이스 마이그레이션을 실행해야 합니다. Sanctum은 API 토큰을 저장하기 위한 데이터베이스 테이블 하나를 생성합니다.

    php artisan migrate

다음으로, 여러분이 SPA 인증으로 Sanctum을 활용할 계획이라면, 애플리케이션의 `app/Http/Kernel.php` 파일에 있는 `api` 미들웨어 그룹에 Sanctum 미들웨어를 추가해야 합니다.

    'api' => [
        \Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful::class,
        'throttle:api',
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
    ],

<a name="migration-customization"></a>
#### 마이그레이션 커스터마이징

Sanctum의 기본 마이그레이션을 사용하지 않으려면 `App\Providers\AppServiceProvider` 클래스의 `register` 메소드에서 `Sanctum::ignoreMigrations` 메소드를 호출해야 합니다. `php artisan vendor:publish --tag=sanctum-migrations` 명령어를 실행하여 기본 마이그레이션을 내보낼 수 있습니다.

<a name="configuration"></a>
## 설정

<a name="overriding-default-models"></a>
### 기본 모델 재정의

일반적으로 필수는 아니지만 Sanctum 에서 내부적으로 사용하는 `PersonalAccessToken` 모델을 자유롭게 확장할 수 있습니다.

    use Laravel\Sanctum\PersonalAccessToken as SanctumPersonalAccessToken;

    class PersonalAccessToken extends SanctumPersonalAccessToken
    {
        // ...
    }

그런 다음 Sanctum 에서 제공하는 `usePersonalAccessTokenModel` 메소드를 통해 사용자 정의 모델을 사용하도록 Sanctum에 지시할 수 있습니다. 일반적으로 애플리케이션 서비스 프로바이더 중 하나의 `boot` 메소드에서 이 메소드를 호출해야 합니다.

    use App\Models\Sanctum\PersonalAccessToken;
    use Laravel\Sanctum\Sanctum;

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        Sanctum::usePersonalAccessTokenModel(PersonalAccessToken::class);
    }

<a name="api-token-authentication"></a>
## API 토큰 인증

> {tip} 여러분의 자사 SPA를 인증하기 위해서 API 토큰을 사용하지 마세요. 대신, Sanctum에 내장된 [SPA 인증 기능](#spa-authentication)을 사용하세요.

<a name="issuing-api-tokens"></a>
### API 토큰 발급하기

Sanctum을 사용하면 애플리케이션에 대한 API 요청을 인증할 때 사용할 수 있는 API 토큰 / 개인 액세스 토큰을 발행할 수 있습니다. API 토큰을 사용한 요청이 올 때, 토큰은 `Authorization` 헤더 안에 `Bearer` 토큰 형식으로 포함되어야 합니다.

사용자에게 토큰 발행을 시작하려면 User 모델에 `Laravel\Sanctum\HasApiTokens` 트레잇(trait)을 use에 넣어야 합니다.

    use Laravel\Sanctum\HasApiTokens;

    class User extends Authenticatable
    {
        use HasApiTokens, HasFactory, Notifiable;
    }

토큰을 발행하기 위해서 `createToken` 메소드를 사용할 수 있습니다. `createToken` 메소드는 `Laravel\Sanctum\NewAccessToken` 인스턴스를 반환합니다. API 토큰은 데이터베이스에 저장되기 전에 SHA-256 해싱을 이용해서 해시 되지만, `NewAccessToken` 인스턴스의 `plainTextToken` 속성을 사용해서 평문으로 액세스할 수 있습니다. 토큰이 생성된 직후 이 값을 사용자에게 표시해야 합니다.

    use Illuminate\Http\Request;

    Route::post('/tokens/create', function (Request $request) {
        $token = $request->user()->createToken($request->token_name);

        return ['token' => $token->plainTextToken];
    });

`HasApiTokens` 트레잇이 제공하는 `tokens` 엘로퀀트 관계(relationship)를 이용해서 모든 사용자의 토큰에 액세스할 수 있습니다.

    foreach ($user->tokens as $token) {
        //
    }

<a name="token-abilities"></a>
### 토큰 권한

Sanctum을 사용하면 토큰에 "권한(abilities)"을 할당할 수 있습니다. 권한은 OAuth의 "스코프(scopes)"와 유사한 목적으로 사용됩니다. `createToken` 메소드의 두 번째 인수로 문자열로 된 권한(abilities)의 배열을 넘기게 됩니다.

    return $user->createToken('token-name', ['server:update'])->plainTextToken;

Sanctum에 의해 들어오는 요청 인증이 핸들링 될 때, 여러분은 `tokenCan` 메소드를 사용해서 그 토큰이 주어진 권한이 있는지를 판단합니다.

    if ($user->tokenCan('server:update')) {
        //
    }

<a name="token-ability-middleware"></a>
#### 토큰 권한 미들웨어

Sanctum은 또한 들어오는 요청이 주어진 권한에 부여된 토큰으로 인증되었는지 확인하는 데 사용할 수 있는 두 개의 미들웨어를 포함합니다. 시작하려면, 애플리케이션 `app/Http/Kernel.php` 파일의 `$routeMiddleware` 속성에 다음 미들웨어를 추가하세요.

    'abilities' => \Laravel\Sanctum\Http\Middleware\CheckAbilities::class,
    'ability' => \Laravel\Sanctum\Http\Middleware\CheckForAnyAbility::class,

들어오는 요청의 토큰에 나열된 모든 권한이 있는지 확인하기 위해 `권한(abilities)` 미들웨어를 라우트에 할당할 수 있습니다.

    Route::get('/orders', function () {
        // Token has both "check-status" and "place-orders" abilities...
    })->middleware(['auth:sanctum', 'abilities:check-status,place-orders']);

`권한(ability)` 미들웨어는 들어오는 요청의 토큰에 나열된 권한(abilities) 중 *하나 이상*이 있는지 확인하기 위해 라우트에 할당될 수 있습니다.

    Route::get('/orders', function () {
        // Token has the "check-status" or "place-orders" ability...
    })->middleware(['auth:sanctum', 'ability:check-status,place-orders']);

<a name="first-party-ui-initiated-requests"></a>
#### 자사 UI 시작 요청

편의상, `tokenCan` 메소드는 들어오는 인증 요청이 자사 SPA에서 온 것이고 Sanctum의 내장 된 [SPA 인증](#spa-authentication)을 사용하는 경우 항상 `true`를 반환합니다.

그러나, 이것이 반드시 애플리케이션이 사용자가 작업을 수행하도록 허용해야 한다는 의미는 아닙니다. 일반적으로 애플리케이션의 [권한 부여 정책](/docs/{{version}}/authorization#creating-policies)은 토큰에 기능을 수행할 수 있는 권한이 부여되었는지 확인하고 사용자 인스턴스 자체가 작업을 수행하도록 허용되어야 하는지 확인합니다.

예를 들어서, 서버를 관리하는 애플리케이션을 상상한다면, 이는 토큰이 서버를 업데이트할 권한이 **있고** 서버가 사용자에게 속해 있는지 확인하는 것을 의미할 수 있습니다.

```php
return $request->user()->id === $server->user_id &&
       $request->user()->tokenCan('server:update')
```

처음에는, `tokenCan` 메소드가 호출되도록 허용하고 자사 UI 시작 요청에 대해 항상 `true`를 반환하는 것이 이상하게 보일 수 있습니다. 그러나, API 토큰을 항상 사용할 수 있고 `tokenCan` 메소드를 통해 검사할 수 있다고 가정할 수 있는 것이 편리합니다. 이 접근 방식을 사용하면, 요청이 애플리케이션의 UI에서 트리거되었는지 아니면 API의 타사 소비자 중 하나가 시작했는지에 대해 걱정하지 않고 애플리케이션의 권한 부여 정책 내에서 항상 `tokenCan` 메소드를 호출할 수 있습니다.

<a name="protecting-routes"></a>
### 라우트 보호하기

들어오는 모든 요청이 인증되도록 라우트를 보호하려면 `routes/web.php` 및 `routes/api.php` 라우트 파일내의 보호된 경로에 `sanctum` 인증 가드를 연결해야 합니다. 이 가드는 들어오는 요청이 상태 저장(stateful) 쿠키 인증 요청으로 인증되었는지 혹은 제3자로부터의 요청인 경우 유효한 API 토큰 헤더를 가지고 있는지를 확인합니다.

`sanctum` 가드를 사용하여 애플리케이션의 `routes/web.php` 파일 내에서 경로를 인증 해야 하는 이유가 궁금할 것입니다. 먼저, Sanctum은 라라벨의 일반적인 세션 인증 쿠키를 사용하여 들어오는 요청을 인증하려고 시도합니다. 해당 쿠키가 없으면 Sanctum은 요청의 `Authorization` 헤더에 있는 토큰을 사용하여 요청을 인증하려고 시도합니다. 또한 Sanctum을 사용하여 모든 요청을 인증하면 현재 인증된 사용자 인스턴스에서 항상 `tokenCan` 메소드를 호출할 수 있습니다.

    use Illuminate\Http\Request;

    Route::middleware('auth:sanctum')->get('/user', function (Request $request) {
        return $request->user();
    });

<a name="revoking-tokens"></a>
### 토큰 해지하기

`Laravel\Sanctum\HasApiTokens` 트레잇이 제공하는 `tokens` 관계(relationship)를 사용하여 데이터베이스에서 삭제함으로서 토큰을 해지합니다.

    // Revoke all tokens...
    // 모든 토큰 해지...
    $user->tokens()->delete();

    // Revoke the token that was used to authenticate the current request...
    // 현재 요청을 인증하는 데 사용된 토큰을 취소...
    $request->user()->currentAccessToken()->delete();

    // Revoke a specific token...
    // 지정된 토큰 해지...
    $user->tokens()->where('id', $tokenId)->delete();

<a name="spa-authentication"></a>
## SPA 인증

Sanctum은 또한 라라벨 기반 API와 통신해야하는 싱글 페이지 애플리케이션(SPAs)을 인증하는 간단한 방법을 제공하기 위해 만들어졌습니다. SPA는 라라벨 애플리케이션과 같은 저장소(repository)에 있거나, Vue CLI를 사용해 생성 된 SPA처럼 전체가 분리된 저장소에 있을 수도 있습니다.

이 기능을 위해서 Sanctum은 어떤 종류의 토큰도 사용하지 않습니다. 대신, Sanctum은 라라벨에 내장(built-in) 된 쿠키 기반의 세션 인증 서비스를 사용합니다. 이것은 CSRF 보호, 세션 인증이라는 이점을 제공할 뿐만 아니라 XSS를 통한 인증 자격 증명(authentication credentials)의 유출을 방어합니다. 

> {note} 인증을 받으려면 SPA와 API가 동일한 최상위 도메인을 공유해야합니다. 그러나 다른 하위 도메인에 배치 될 수 있습니다. 또한 요청과 함께 `Accept: application/json` 헤더를 보내야 합니다.

<a name="spa-configuration"></a>
### 설정

<a name="configuring-your-first-party-domains"></a>
#### 자사(First-Party) 도메인 설정

먼저 SPA가 요청할 도메인을 설정해야 합니다. `sanctum` 설정 파일 안에 `stateful` 설정 옵션을 사용해서 이러한 도메인을 설정합니다. 이 설정 세팅은 어떤 도메인이 API에 요청할 때 라라벨 세션 쿠키를 사용하여 "상태 유지(stateful)" 인증을 유지할 도메인을 결정합니다.

> {note} 포트 (`127.0.0.1:8000`)가 포함 된 URL을 통해 애플리케이션에 액세스하는 경우 도메인에 포트 번호를 포함해야합니다.

<a name="sanctum-middleware"></a>
#### Sanctum 미들웨어

다음으로, Sanctum 미들웨어를 `app/Http/Kernel.php` 파일 내에 있는 `api` 미들웨어 그룹에 추가해야 합니다. 이 미들웨어는 SPA에서 들어오는 요청이 라라벨의 세션 쿠키를 사용해서 인증할 수 있도록 보장하는 동시에, 여전히 제3자 혹은 모바일 애플리케이션으로부터 API 토큰을 사용하여 인증할 수 있도록 합니다.

    'api' => [
        \Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful::class,
        'throttle:api',
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
    ],

<a name="cors-and-cookies"></a>
#### CORS 와 쿠키

별도의 서브도메인에서 실행되는 SPA에서 애플리케이션을 인증하는데 문제가 있는 경우, CORS (Cross-Origin Resource Sharing) 또는 세션 쿠키 세팅이 잘못 설정되었을 수 있습니다.

애플리케이션의 CORS 설정 값이 `True`인 `Access-Control-Allow-Credentials` 헤더를 리턴하는지 확인해야 합니다. 이것은 애플리케이션의 `config/cors.php` 설정 파일에서 `support_credentials` 옵션을 `true`로 세팅하여 수행할 수 있습니다.

또한 애플리케이션의 전역 `axios` 인스턴스에서 `withCredentials` 옵션을 활성화해야 합니다. 일반적으로 이것은 `resources/js/bootstrap.js` 파일에서 수행되어야 합니다. Axios를 사용하여 프론트엔드에서 HTTP 요청을 생성하지 않는 경우, 자체 HTTP 클라이언트에서 Axios와 동일한 설정으로 수행해야 합니다.

    axios.defaults.withCredentials = true;

마지막으로 애플리케이션의 세션 쿠키 도메인 설정이 루트 도메인의 서브도메인을 지원하는지를 확인해야 합니다. 애플리케이션의 `config/session.php` 마지막으로 애플리케이션의 세션 쿠키 도메인 설정이 루트 도메인의 서브도메인을 지원하는지를 확인해야 합니다. `config/session.php` 설정 파일 안에 `.`을 붙이면 됩니다.

    'domain' => '.domain.com',

<a name="spa-authenticating"></a>
### 인증하기

<a name="csrf-protection"></a>
#### CSRF 보호

SPA를 인증하려면 먼저 SPA 로그인 페이지에서 애플리케이션에 대한 CSRF 보호(protection)를 초기화하기 위해서 `/sanctum/csrf-cookie` 엔드포인트를 가장 먼저 요청해야 합니다.

    axios.get('/sanctum/csrf-cookie').then(response => {
        // Login...
    });

이 요청 중에, 라라벨은 현재 CSRF 토큰을 포함하는 `XSRF-TOKEN` 쿠키를 설정합니다. 이 토큰은 후속 요청에서 `X-XSRF-TOKEN` 헤더로 전달되어야하며, Axios 및 Angular HttpClient와 같은 라이브러리가 자동으로 수행합니다. JavaScript HTTP 라이브러리가 값을 설정하지 않으면 이 경로에 의해 설정된 `XSRF-TOKEN` 쿠키의 값과 일치하도록 `X-XSRF-TOKEN` 헤더를 수동으로 설정해야 합니다.

<a name="logging-in"></a>
#### 로그인

한번 CSRF 보호가 초기화되면, 여러분은 일반적인 라라벨 애플리케이션의 `/login` 경로에 `POST` 요청을 해야 합니다. 이 `/login` 경로는 [수동 구현](/docs/{{version}}/authentication#authenticating-users)하거나 [라라벨 Fortify](/docs/{{version}}/fortify)와 같은 헤드리스 인증 패키지를 사용할 수 있습니다.

만약 로그인 요청이 성공한다면 라라벨 애플리케이션이 클라이언트에 발급 한 세션 쿠키를 통해 인증되고, 애플리케이션 라우트로 보내는 후속 요청이 자동으로 인증됩니다.또한 애플리케이션이 이미 `/sanctum/csrf-cookie` 라우트를 요청했으므로 JavaScript HTTP 클라이언트가 `X-XSRF-TOKEN`에서 `XSRF-TOKEN` 쿠키 값을 보내는 한 후속 요청은 자동으로 CSRF 보호를 받아야 합니다.

물론 사용자의 세션이 활동 부족으로 만료되면 라라벨 애플리케이션에 대한 후속 요청에서 401 또는 419 HTTP 오류 응답을 받을 수 있습니다. 이 경우 사용자를 SPA의 로그인 페이지로 리디렉션해야 합니다.

> {note} 자신만의 `/login` 엔드포인트를 자유롭게 작성할 수 있습니다; 그러나 [라라벨이 제공하는 세션 기반의 인증 서비스](/docs/{{version}}/authentication#authenticating-users)을 사용해서 사용자를 인증해야 합니다. 일반적으로 이것은 `web` 인증 가드를 사용하는 것을 의미합니다


<a name="protecting-spa-routes"></a>
### 라우트 보호하기

들어오는 모든 요청이 인증되도록 라우트를 보호하려면 `routes/api.php` 파일의 API 라우트에 `sanctum` 인증 가드를 연결해야 합니다. 이 가드는 들어오는 요청이 SPA로부터 상태 저장(stateful) 인증 요청으로 인증되었는지 혹은 제3자로부터의 요청인 경우 유효한 API 토큰 헤더를 가지고 있는지를 확인합니다.

    use Illuminate\Http\Request;

    Route::middleware('auth:sanctum')->get('/user', function (Request $request) {
        return $request->user();
    });

<a name="authorizing-private-broadcast-channels"></a>
### Private 브로드캐스트 채널 승인하기

만약 SPA가 [Private / Presence 브로드캐스트 채널](/docs/{{version}}/broadcasting#authorizing-channels)로 인증될 필요가 있다면, `routes/api.php` 파일 내에 `Broadcast::routes` 메소드 호출(method call)을 넣어야 합니다.

    Broadcast::routes(['middleware' => ['auth:sanctum']]);

다음으로, 푸셔(Pusher)의 승인 요청에 성공하려면, [라라벨 에코](/docs/{{version}}/broadcasting#client-side-installation)를 초기화할 때, 커스텀 푸셔 `authorizer`를 제공해야 합니다. 이를 통해 애플리케이션은 [크로스 도메인 요청에 대해 올바르게 설정된](#cors-and-cookies) `axios` 인스턴스를 사용하도록 푸셔를 설정할 수 있습니다.

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
## 모바일 애플리케이션 인증

Sanctum 토큰을 사용하여 모바일 애플리케이션의 API 요청을 인증할 수 있습니다. 모바일 애플리케이션 요청을 인증하는 프로세스는 제3자 API 요청을 인증하는 것과 유사합니다. 그러나, API 토큰을 발행하는 방법에는 약간의 차이가 있습니다.

<a name="issuing-mobile-api-tokens"></a>
### API 토큰 발급하기

시작하려면, 사용자의 이메일 / 사용자 이름, 비밀번호 및 장치 이름을 허용하는 라우트를 만든 다음, 해당 자격 증명을 새로운 Sanctum 토큰으로 교환하십시오. 엔드포인트에 제공된 "장치 이름"은 정보 제공용이며 원하는 값일 수 있습니다. 일반적으로 장치 이름 값은 "Nuno의 iPhone 12"와 같이 사용자가 인식할 수 있는 이름이어야 합니다.

일반적으로 모바일 애플리케이션의 "로그인" 화면에서 토큰 엔드포인트에 요청합니다. 엔드포인트는 평문으로 된 API 토큰을 반환한 다음 모바일 장치에 저장하여 추가 API 요청을 수행하는 데 사용할 수 있습니다.

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

모바일 디바이스가 토큰을 사용하여 애플리케이션에 API 요청을 하는 경우, `Authorization` 헤더에 `Bearer` 토큰으로 전달해야 합니다.

> {tip} 모바일 애플리케이션에 대한 토큰을 발행할 때 [토큰 권한](#token-abilities)을 자유롭게 지정할 수도 있습니다

<a name="protecting-mobile-api-routes"></a>
### 라우트 보호하기

앞서 문서화 한 바와 같이, 경로에 `sanctum` 인증 가드를 연결하여 들어오는 모든 요청을 인증해야 하는 경로를 보호할 수 있습니다.

    Route::middleware('auth:sanctum')->get('/user', function (Request $request) {
        return $request->user();
    });

<a name="revoking-mobile-api-tokens"></a>
### 토큰 해지하기

사용자가 모바일 장치에 발급된 API 토큰을 해지할 수 있도록 웹 애플리케이션 UI의 "계정 설정"부분에 "해지"버튼과 함께 이름별로 토큰을 나열할 수 있습니다. 사용자가 "해지"버튼을 클릭하면 데이터베이스에서 토큰을 삭제할 수 있습니다. `Laravel\Sanctum\HasApiTokens` 트레잇이 제공하는`tokens`관계를 통해 사용자의 API 토큰에 액세스할 수 있습니다.

    // Revoke all tokens...
    // 모든 토큰 해지...
    $user->tokens()->delete();

    // Revoke a specific token...
    // 지정된 토큰 해지...
    $user->tokens()->where('id', $tokenId)->delete();

<a name="testing"></a>
## 테스트하기

테스트하는 동안 `Sanctum::actingAs` 메소드를 사용하여 사용자를 인증하고 토큰에 부여할 권한을 지정할 수 있습니다.

    use App\Models\User;
    use Laravel\Sanctum\Sanctum;

    public function test_task_list_can_be_retrieved()
    {
        Sanctum::actingAs(
            User::factory()->create(),
            ['view-tasks']
        );

        $response = $this->get('/api/task');

        $response->assertOk();
    }

모든 권한(abilities)을 토큰에 부여하려면 `actingAs` 메소드에서 제공한 권한 목록에`*`를 포함시켜야 합니다.

    Sanctum::actingAs(
        User::factory()->create(),
        ['*']
    );
