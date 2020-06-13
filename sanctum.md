# 라라벨 Sanctum

- [시작하기](#introduction)
    - [작동 원리](#how-it-works)
- [설치하기](#installation)
- [API 토큰 인증](#api-token-authentication)
    - [API 토큰 발행하기](#issuing-api-tokens)
    - [토큰 기능(Token Abilities)](#token-abilities)
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

라라벨 Sanctum은 SPAs(싱글 페이지 애플리케이션), 모바일 애플리케이션과 간단한 토큰 기반의 API를 위한 패더급(경량) 인증 시스템을 제공합니다. Sanctum을 사용하면 애플리케이션의 각 사용자가 자신의 계정에 대해 여러 개의 API 토큰을 생성 할 수 있습니다. 이 토큰에는 토큰이 수행할 수 있는 기능 / 범위가 부여될 수 있습니다.

<a name="how-it-works"></a>
### 작동 원리

#### API 토큰

라라벨 Sanctum은 두 가지 별도의 문제를 해결하기 위해서 제작되었습니다. 첫 번째, Sanctum은 OAuth의 복잡함 없이 사용자에게 API 토큰을 발행하는 단순한 패키지입니다. 이 기능은 깃헙 "액세스 토큰(access tokens)"에서 영감을 얻었습니다. 예를 들어서,  "계정 설정(account settings)"에 사용자가 자신의 계정에 대한 API 토큰을 생성할 수 있는 어떤 화면을 상상해 보세요. Sanctum을 사용하여 해당 토큰을 생성하고 관리 할 수 있습니다. 이러한 토큰들은 매우 긴 만료일(몇 년)을 가지고 있지만, 어느 때라도 사용자에 의해서 해지(revoke) 할 수도 있습니다.

라라벨 Sanctum은 단일 데이터베이스 테이블 내에 사용자 API 토큰을 저장하고, 유효한 API 토큰을 포함해야하는 `Authorization` 헤더를 통해 오는 요청(request) 인증함으로써 이 기능을 제공합니다.

#### SPA 인증

> {팁} Sanctum은 API 토큰 인증 또는 SPA 인증에만 사용하는 것이 좋습니다. Sanctum을 사용한다고해서 제공하는 두 기능 모두를 사용해야한다는 의미는 아닙니다.

두번째, Sanctum은 라라벨 기반 API와 통신해야하는 싱글 페이지 애플리케이션(SPAs)을 인증하는 간단한 방법을 제공하기 위해 만들어졌습니다. 이런 SPA는 라라벨 애플리케이션과 같은 저장소(repository)에 있거나, Vue CLI를 사용해 생성 된 SPA처럼 전체가 분리된 저장소에 있을 수도 있습니다.

이 기능을 위해서 Sanctum은 어떤 종류의 토큰도 사용하지 않습니다. 대신, Sanctum은 라라벨에 내장(built-in) 된 쿠키 기반의 세션 인증 서비스를 사용합니다. 이것은 CSRF 보호, 세션 인증이라는 이점을 제공할 뿐만 아니라 XSS를 통한 인증 자격 증명(authentication credentials)의 유출을 방어합니다. Sanctum은 들어오는 요청이 자신의 SPA 프론트엔드에서 시작된 경우에만 쿠키를 사용하여 인증을 시도합니다.

<a name="installation"></a>
## 설치하기

여러분은 컴포져를 통해 라라벨 Sanctum을 설치할 수 있습니다.

    composer require laravel/sanctum

다음으로, Sanctum의 설정을 내보내고(publish), `vendor:publish` 아티즌 명령어를 사용해서 파일을 마이그레이션 합니다. `sanctum` 설정 파일은 `config` 디렉토리에 위치하게 됩니다.

    php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"

마지막으로, 데이터베이스 마이그레이션을 실행해야 합니다. Sanctum은 API 토큰을 저장하기 위한 데이터베이스 테이블 하나를 생성합니다.

    php artisan migrate

다음으로, 여러분이 SPA 인증으로 Sanctum을 활용할 계획이라면, `app/Http/Kernel.php` 파일에 있는 `api` 미들웨어 그룹에 Sanctum의 미들웨어를 넣어야 합니다.

    use Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful;

    'api' => [
        EnsureFrontendRequestsAreStateful::class,
        'throttle:60,1',
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
    ],

#### 마이그레이션 커스터마이징

Sanctum의 기본 마이그레이션을 사용하지 않으려면  `AppServiceProvider`의 `register` 메소드에서 `Sanctum::ignoreMigrations` 메소드를 호출해야합니다. `php artisan vendor:publish --tag=sanctum-migrations`를 사용하여 기본 마이그레이션을 내보낼 수 있습니다.

<a name="api-token-authentication"></a>
## API 토큰 인증

> {팁} 여러분의 자사 SPA를 인증하기 위해서 API 토큰을 사용하지 마세요. 대신, Sanctum에 내장된 [SPA 인증](#spa-authentication)을 사용하세요.

<a name="issuing-api-tokens"></a>
### API 토큰 발급하기

Sanctum을 사용하면 API 요청을 인증할 때 사용할 수 있는 API 토큰 / 개인 액세스 토큰을 발행할 수 있습니다. API 토큰을 사용한 요청이 올 때, 토큰은 `Authorization` 헤더 안에 `Bearer` 토큰 형식으로 포함되어야 합니다.

사용자에게 토큰 발행을 시작하려면 User 모델에 `HasApiTokens` 트레잇(trait)을 use에 넣어야 합니다.

    use Laravel\Sanctum\HasApiTokens;

    class User extends Authenticatable
    {
        use HasApiTokens, Notifiable;
    }

토큰을 발행하기 위해서 `createToken` 메소드를 사용할 수 있습니다. `createToken` 메소드는 `Laravel\Sanctum\NewAccessToken` 인스턴스를 반환합니다. API 토큰은 데이터베이스에 저장되기 전에 SHA-256 해싱을 이용해서 해시 되지만, `NewAccessToken` 인스턴스의 `plainTextToken` 속성을 사용해서 평문으로 액세스할 수 있습니다. 토큰이 생성된 직후 이 값을 사용자에게 표시해야 합니다.

    $token = $user->createToken('token-name');

    return $token->plainTextToken;

`HasApiTokens` 트레잇이 제공하는 `tokens` 엘로퀀트 관계(relationship)를 이용해서 모든 사용자의 토큰에 액세스할 수 있습니다.

    foreach ($user->tokens as $token) {
        //
    }

<a name="token-abilities"></a>
### 토큰 기능

Sanctum은 토큰에 OAuth "스코프(scopes)"와 유사한 "권한(abilities)"을 할당합니다. `createToken` 메소드의 두 번째 인수로 문자열로 된 권한(abilities)의 배열을 넘기게 됩니다.

    return $user->createToken('token-name', ['server:update'])->plainTextToken;

Sanctum에 의해 들어오는 요청 인증이 핸들링 될 때, 여러분은 `tokenCan` 메소드를 사용해서 그 토큰이 주어진 권한이 있는지를 판단합니다.

    if ($user->tokenCan('server:update')) {
        //
    }

> {팁} 편의상 `tokenCan` 메소드는 여러분 자사의 SPA에서 들어오는 요청이고, Sanctum 내장 [SPA 인증](#spa-authentication)을 사용한다면, 항상 `true`를 반환합니다.

<a name="protecting-routes"></a>
### 라우트 보호하기

들어오는 모든 요청이 인증되도록 라우트를 보호하려면 `routes/api.php` 파일의 API 라우트에 `sanctum` 인증 가드를 연결해야 합니다. 이 가드는 들어오는 요청이 SPA로부터 상태 저장(stateful) 인증 요청으로 인증되었는지 혹은 제3자로부터의 요청인 경우 유효한 API 토큰 헤더를 가지고 있는지를 확인합니다.

    Route::middleware('auth:sanctum')->get('/user', function (Request $request) {
        return $request->user();
    });

<a name="revoking-tokens"></a>
### 토큰 해지하기

`HasApiTokens` 트레잇이 제공하는 `tokens` 관계(relationship)을 사용하여 데이터베이스에서 삭제함으로서 토큰을 해지합니다.

    // 모든 토큰 해지...
    $user->tokens()->delete();

    // 지정된 토큰 해지...
    $user->tokens()->where('id', $id)->delete();


<a name="spa-authentication"></a>
## SPA 인증

Sanctum은 라라벨 기반 API와 통신해야하는 싱글 페이지 애플리케이션(SPAs)을 인증하는 간단한 방법을 제공하기 위해 만들어졌습니다. 이런 SPA는 라라벨 애플리케이션과 같은 저장소(repository)에 있거나, Vue CLI를 사용해 생성 된 SPA처럼 전체가 분리된 저장소에 있을 수도 있습니다.

이 기능을 위해서 Sanctum은 어떤 종류의 토큰도 사용하지 않습니다. 대신, Sanctum은 라라벨에 내장(built-in) 된 쿠키 기반의 세션 인증 서비스를 사용합니다. 이것은 CSRF 보호, 세션 인증이라는 이점을 제공할 뿐만 아니라 XSS를 통한 인증 자격 증명(authentication credentials)의 유출을 방어합니다. Sanctum은 들어오는 요청이 자신의 SPA 프론트엔드에서 시작된 경우에만 쿠키를 사용하여 인증을 시도합니다.

> {note} 인증을 받으려면 SPA와 API가 동일한 최상위 도메인을 공유해야합니다. 그러나 다른 하위 도메인에 배치 될 수 있습니다.

<a name="spa-configuration"></a>
### 설정

#### 자사(First-Party) 도메인 설정

먼저 SPA가 요청할 도메인을 설정해야 합니다. `sanctum` 설정 파일 안에 `stateful` 설정 옵션을 사용해서 이러한 도메인을 설정합니다. 이 설정 세팅은 어떤 도메인이 API에 요청할 때 라라벨 세션 쿠키를 사용하여 "상태 유지(stateful)" 인증을 유지할 도메인을 결정합니다.

#### Sanctum 미들웨어

다음으로, Sanctum 미들웨어를 `app/Http/Kernel.php` 파일 내에 있는 `api` 미들웨어 그룹에 추가해야 합니다. 이 미들웨어는 SPA에서 들어오는 요청이 라라벨의 세션 쿠키를 사용해서 인증할 수 있도록 보장하는 동시에, 여전히 제3자 혹은 모바일 애플리케이션으로부터 API 토큰을 사용하여 인증할 수 있도록 합니다.

    use Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful;

    'api' => [
        EnsureFrontendRequestsAreStateful::class,
        'throttle:60,1',
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
    ],

<a name="cors-and-cookies"></a>
#### CORS 와 쿠키

별도의 서브도메인에서 실행되는 SPA에서 애플리케이션을 인증하는데 문제가 있는 경우, CORS (Cross-Origin Resource Sharing) 또는 세션 쿠키 세팅이 잘못 설정되었을 수 있습니다.

애플리케이션의 `cors` 설정 파일 안에 `supports_credentials` 옵션을 `true`로 세팅해서 `True` 값이 포함된 `Access-Control-Allow-Credentials` 헤더를 리턴하도록 애플리케이션의 CORS 설정을 확인해야 합니다.

또한 전역 `axios` 인스턴스의 `withCredentials` 옵션을 활성화해야 합니다. 일반적으로 이것은 `resources/js/bootstrap.js` 파일에서 수행되어야 합니다.

    axios.defaults.withCredentials = true;

마지막으로 애플리케이션의 세션 쿠키 도메인 설정이 루트 도메인의 서브도메인을 지원하는지를 확인해야 합니다. `session` 설정 파일 안에 `.`을 붙이면 됩니다.

    'domain' => '.domain.com',

<a name="spa-authenticating"></a>
### 인증하기

SPA를 인증하려면 먼저 SPA 로그인 페이지에서 CSRF 보호(protection)를 초기화하기 위해서 `/sanctum/csrf-cookie` 라우트를 가장 먼저 요청해야 합니다.

    axios.get('/sanctum/csrf-cookie').then(response => {
        // Login...
    });

한번 CSRF 보호가 초기화되면, 여러분은 일반적인  라라벨 `/login` 라우트에 `POST`로 요청해야 합니다. 이 `/login` 라우트는 `laravel/ui` [인증 스케폴딩](/docs/{{version}}/authentication#introduction) 패키지에서 제공될 수 있습니다.

만약 로그인 요청이 성공한다면 라라벨 백엔드가 클라이언트에 발급 한 세션 쿠키를 통해 인증되고, API 라우트로 보내는 후속 요청이 자동으로 인증됩니다.

> {팁} 자신만의 `/login` 엔드포인트를 자유롭게 작성할 수 있습니다; 그러나, [라라벨이 제공하는 세션 기반의 인증 서비스](/docs/{{version}}/authentication#authenticating-users) 를 사용해서 사용자를 인증해야 합니다.

<a name="protecting-spa-routes"></a>
### 라우트 보호하기

들어오는 모든 요청이 인증되도록 라우트를 보호하려면 `routes/api.php` 파일의 API 라우트에 `sanctum` 인증 가드를 연결해야 합니다. 이 가드는 들어오는 요청이 SPA로부터 상태 저장(stateful) 인증 요청으로 인증되었는지 혹은 제3자로부터의 요청인 경우 유효한 API 토큰 헤더를 가지고 있는지를 확인합니다.

    Route::middleware('auth:sanctum')->get('/user', function (Request $request) {
        return $request->user();
    });

<a name="authorizing-private-broadcast-channels"></a>
### Private 브로드캐스트 채널 승인하기

만약 SPA가 [Private / Presence 브로드캐스트 채널](/docs/{{version}}/broadcasting#authorizing-channels)로 인증될 필요가 있다면, `routes/api.php` 파일 내에 `Broadcast::routes` 메소드 호출(method call)을 넣어야 합니다.

    Broadcast::routes(['middleware' => ['auth:sanctum']]);

다음으로, 푸셔(Pusher)의 승인 요청에 성공하려면, [라라벨 에코](/docs/{{version}}/broadcasting#installing-laravel-echo)를 초기화할 때, 커스텀 푸셔 `authorizer`를 제공해야 합니다. 이를 통해 애플리케이션은 [크로스 도메인 요청에 대해 올바르게 설정된](#cors-and-cookies) `axios` 인스턴스를 사용하도록 푸셔를 설정할 수 있습니다.

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

시작하려면, 사용자의 이메일 / 사용자 이름, 비밀번호 및 장치 이름을 허용하는 라우트를 만든 다음, 해당 자격 증명을 새로운 Sanctum 토큰으로 교환하십시오. 엔드포인트는 평문으로 된 Sanctum 토큰을 반환한 다음 모바일 장치에 저장하여 추가 API 요청을 수행하는 데 사용할 수 있습니다.

    use App\User;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Hash;
    use Illuminate\Validation\ValidationException;

    Route::post('/sanctum/token', function (Request $request) {
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

모바일 디바이스가 토큰을 사용하여 애플리케이션에 API 요청을 하는 경우, `Authorization` 헤더에 `Bearer` 토큰으로 전달해야 합니다.

> {팁} 모바일 애플리케이션에 대한 토큰을 발행할 때 [토큰 기능](#token-abilities)를 자유롭게 지정할 수도 있습니다

<a name="protecting-mobile-api-routes"></a>
### 라우트 보호하기

앞서 문서화 한 바와 같이, 경로에 `sanctum` 인증 가드를 연결하여 들어오는 모든 요청을 인증해야 하는 경로를 보호할 수 있습니다. 일반적으로`routes/api.php` 파일에 정의된 라우트에 이 가드를 연결합니다.

    Route::middleware('auth:sanctum')->get('/user', function (Request $request) {
        return $request->user();
    });

<a name="revoking-mobile-api-tokens"></a>
### 토큰 해지하기

사용자가 모바일 장치에 발급된 API 토큰을 해지할 수 있도록 웹 애플리케이션 UI의 "계정 설정"부분에 "해지"버튼과 함께 이름별로 토큰을 나열할 수 있습니다. 사용자가 "해지"버튼을 클릭하면 데이터베이스에서 토큰을 삭제할 수 있습니다. `HasApiTokens` 트레잇이 제공하는`tokens`관계를 통해 사용자의 API 토큰에 액세스할 수 있습니다.

    // Revoke all tokens...
    $user->tokens()->delete();

    // Revoke a specific token...
    $user->tokens()->where('id', $id)->delete();

    // 모든 토큰 해지...
    $user->tokens()->delete();

    // 지정된 토큰 해지...
    $user->tokens()->where('id', $id)->delete();


<a name="testing"></a>
## 테스트하기

테스트하는 동안 `Sanctum::actingAs` 메소드를 사용하여 사용자를 인증하고 토큰에 부여할 기능을 지정할 수 있습니다.

    use App\User;
    use Laravel\Sanctum\Sanctum;

    public function test_task_list_can_be_retrieved()
    {
        Sanctum::actingAs(
            factory(User::class)->create(),
            ['view-tasks']
        );

        $response = $this->get('/api/task');

        $response->assertOk();
    }

모든 기능(abilities)을 토큰에 부여하려면 `actingAs` 메소드에서 제공한 기능 목록에`*`를 포함시켜야 합니다.

    Sanctum::actingAs(
        factory(User::class)->create(),
        ['*']
    );
