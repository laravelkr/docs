# API 인증

- [시작하기](#introduction)
- [설정하기](#configuration)
    - [데이터베이스 사전준비](#database-preparation)
- [Generating Tokens-토큰 생성하기](#generating-tokens)
    - [Hashing Tokens-토큰 해싱하기](#hashing-tokens)
- [라우트 보호하기](#protecting-routes)
- [요청시 토큰 전달](#passing-tokens-in-requests)

<a name="introduction"></a>
## 시작하기

기본적으로, 라라벨은 애플리케이션의 각 유저에 할당된 랜덤(무작위)토큰을 통하여 API 인증을 간단한 솔루션으로 제공합니다. `config/auth.php` 설정 파일에서, `api` 가드는 이미 정의되어있고 `token` 드라이버를 사용합니다. 이 드라이버는 request 요청이 들어오지마자 API 토큰을 확인하고, 데이터베이스에 유저의 할당된 토큰이 맞는지 API 토큰을 확인하는 작업을 합니다.

> **Note:** 라라벨은 간단한 토큰 기반 인증 가드를 제공하지만, 우리는 API 인증을 제공하는 강력하고 생산적인 애플리케이션에는 [Laravel Passport](/docs/{{version}}/passport)을 사용하는걸 적극 추천합니다.

<a name="configuration"></a>
## 설정하기

<a name="database-preparation"></a>
### 데이터베이스 사전준비

`token` 드라이버 사용하기 전에, `users` 테이블에 `api_token` 컬럼을 추가하기 위해서는 [마이그레이션 생성하기](/docs/{{version}}/migrations) 를 이용하면 됩니다.

    Schema::table('users', function ($table) {
        $table->string('api_token', 80)->after('password')
                            ->unique()
                            ->nullable()
                            ->default(null);
    });

마이그레이션을 만들게 되면, `migrate` 아티즌 명령어를 실행하세요.

> {tip} 다른 컬럼명을 사용하려면 `config/auth.php` 설정 파일에서 API의 `storage_key`를 변경해야합니다.

<a name="generating-tokens"></a>
## Generating Tokens-토큰 생성하기

`api_token` 컬럼이 `users` 테이블에 추가되었다면, 여러분의 애플리케이션에 등록하는 유저들에게 랜덤(무작위) API 토큰을 할당할 준비가 되었습니다. 유저등록을 위해서 `User` 모델이 생성 될 때 여러분은 토큰을 할당해야합니다. `laravel/ui` 컴포저 패키지를 통해서 [authentication scaffolding-인증 스케폴딩](/docs/{{version}}/authentication#authentication-quickstart) 을 이용했다면, `RegisterController` 의 `create` 메소드에서 api_token을 사용 할 수 있습니다.

    use Illuminate\Support\Facades\Hash;
    use Illuminate\Support\Str;

    /**
     * Create a new user instance after a valid registration.
     *
     * @param  array  $data
     * @return \App\User
     */
    protected function create(array $data)
    {
        return User::forceCreate([
            'name' => $data['name'],
            'email' => $data['email'],
            'password' => Hash::make($data['password']),
            'api_token' => Str::random(80),
        ]);
    }

<a name="hashing-tokens"></a>
### Hashing Tokens-토큰 해싱하기

위 에시에서는, API 토큰은 여러분의 데이터베이스에 평문으로 저장됩니다. 만약 여러분께서 SHA-256 해싱을 사용하여 당신의 API 토큰을 해싱하고싶다면, `api` 가드 설정에서 `hash` 옵션을 `true` 로 설정하면 됩니다. `api` 가드는 `config/auth.php` 설정 파일에 정의되어 있습니다.

    'api' => [
        'driver' => 'token',
        'provider' => 'users',
        'hash' => true,
    ],

#### 해싱된 토큰 생성하기

해싱된 API 토큰을 이용할 때, 유저 등록이 진행되는 동안에 당신의 API 토큰 생성하면 안됩니다. 대신에, 애플리케이션 내에 여러분의 API 토큰 관리 페이지를 구현할 필요가 있습니다. 이 페이지는 유저들이 그들의 API 토큰을 초기화하거나 갱신을 해주어야합니다. 유저가 그들의 토큰을 초기화하거나 갱신을 요청할 때, 여러분은 데이터베이스에 해싱된 토큰 복제본을 저장해야하며, 평문의 토큰 복제본을 화면단(뷰-view / frontend client for on-time display)에 반환합니다.

예를들어, 유저의 토큰을 초기화/갱신하고 JSON 응답으로 평문으로 된 토큰을 반환하는 컨트롤러 메소드는 다음과 같습니다.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;
    use Illuminate\Support\Str;

    class ApiTokenController extends Controller
    {
        /**
         * Update the authenticated user's API token.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return array
         */
        public function update(Request $request)
        {
            $token = Str::random(80);

            $request->user()->forceFill([
                'api_token' => hash('sha256', $token),
            ])->save();

            return ['token' => $token];
        }
    }

> {tip} 위 예에서 API 토큰은 entropy-엔트로피가 충분함으로, 해시 토큰의 원래 값을 조회하기 위해 "rainbow tables-레인보우 테이블"을 만드는 것은 비실용적입니다. 그러므로 `bcrypt` 와 같은 느린 해싱 메소드는 필수가 아닙니다.

<a name="protecting-routes"></a>
## 라우트 보호하기

라라벨은 request 요청시 자동적으로 API 토큰을 검증하는 [인증 가드](/docs/{{version}}/authentication#adding-custom-guards)을 포함합니다. 여러분은 단지 검증된 접근토큰을 요구하는 라우트에 `auth:api` 미들웨어를 명시하기만 하면됩니다.

    use Illuminate\Http\Request;

    Route::middleware('auth:api')->get('/user', function (Request $request) {
        return $request->user();
    });

<a name="passing-tokens-in-requests"></a>
## 요청 시 토큰 전달

애플리케이션에 API 토큰을 전달하는 몇가지 방법이 있습니다. 우리는 Guzzle HTTP 라이브러리를 사용하여 이들의 사용법에 대해서 논의해봅시다. 여러분은 당신의 애플리케이션의 필요에 따라 사용법들 중 필요한 것을 선택해서 사용할 수 있습니다.

#### 쿼리 스트링

여러분의 애플리케이션의 API 사용자들은 `api_token` 을 쿼리스트링 값으로써 토큰을 명시할 수 있습니다.

    $response = $client->request('GET', '/api/user?api_token='.$token);

#### 요청 패이로드

여러분의 애플리케이션의 API 사용자들은 `api_token`을 폼 파라미터의 요청안에 API 토큰을 포함할 수 있습니다.

    $response = $client->request('POST', '/api/user', [
        'headers' => [
            'Accept' => 'application/json',
        ],
        'form_params' => [
            'api_token' => $token,
        ],
    ]);

#### Bearer Token

애플리케이션의 API 사용자들은 `Authorization` 요청헤더에 `Bearer` 토큰으로 API 토큰을 제공할 수 있습니다.

    $response = $client->request('POST', '/api/user', [
        'headers' => [
            'Authorization' => 'Bearer '.$token,
            'Accept' => 'application/json',
        ],
    ]);
