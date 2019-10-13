# 라라벨 Passport

- [시작하기](#introduction)
- [설치하기](#installation)
    - [프론트 엔드 빠른시작](#frontend-quickstart)
    - [Passport 배포하기](#deploying-passport)
- [설정하기](#configuration)
    - [토큰 지속시간](#token-lifetimes)
    - [기본모델 덮어쓰기](#overriding-default-models)
- [엑세스 토큰 발급하기](#issuing-access-tokens)
    - [클라이언트 관리](#managing-clients)
    - [토큰 요청](#requesting-tokens)
    - [토큰 갱신하기](#refreshing-tokens)
- [패스워드 Grant 토큰](#password-grant-tokens)
    - [패스워드 Grant 클라이언트 생성하기](#creating-a-password-grant-client)
    - [토큰 요청](#requesting-password-grant-tokens)
    - [모든 범위에 대하여 요청하기](#requesting-all-scopes)
    - [사용자 이름 필드 사용자 정의하기](#customizing-the-username-field)
    - [패스워드 유효성 검사 커스터마이징](#customizing-the-password-validation)
- [묵시적 grant 토큰](#implicit-grant-tokens)
- [클라이언트의 자격증명을 위한 Grant 토큰](#client-credentials-grant-tokens)
- [개인용 엑세스 토큰](#personal-access-tokens)
    - [개인용 엑세스 클라이언트 생성하기](#creating-a-personal-access-client)
    - [개인용 엑세스 토큰 관리하기](#managing-personal-access-tokens)
- [라우트 보호하기](#protecting-routes)
    - [미들웨어를 통해서](#via-middleware)
    - [엑세스 토큰 전달하기](#passing-the-access-token)
- [토큰 스코프(범위)](#token-scopes)
    - [스코프 정의하기](#defining-scopes)
    - [토큰에 스코프 할당하기](#assigning-scopes-to-tokens)
    - [Scope 확인하기](#checking-scopes)
- [자바스크립트로 API 사용하기](#consuming-your-api-with-javascript)
- [이벤트](#events)
- [테스팅](#testing)

<a name="introduction"></a>
## 시작하기

라라벨은 이미 전통적인 로그인 폼을 통한 사용자 인증을 손쉽게 사용할 수 있는 방법을 가지고 있습니다. 하지만 API의 경우에는 어떨까요? API는 일반적으로 사용자 인증을 위해서 토큰을 사용하고 request-요청에서는 세션을 사용하지 않습니다. 라라벨은 애플리케이션에 Full OAuth2 서버 구현을 제공하는 Passport를 사용하여 API 인증을 용이하게 합니다. Passport 는 Andy Millington 와 Simon Hamp에 의해서 관리되고 있는 [League OAuth2 server](https://github.com/thephpleague/oauth2-server) 를 기반으로 구성되어 있습니다.

> {note} 이 문서는 귀하가 이미 OAuth2에 익숙하다고 가정합니다. OAuth2에 대해 잘 모르는 경우 계속하기 전에 일반 [용어](https://oauth2.thephpleague.com/terminology/) 및 OAuth2의 기능을 숙지하십시오.

<a name="installation"></a>
## 설치하기

컴포저를 통해서 Passport를 설치하는 것부터 시작해보겠습니다.

    composer require laravel/passport

Passport 서비스 프로바이더는 고유한 데이터베이스 마이그레이션 디렉토리를 등록하기 때문에, 패키지를 설치 한 뒤에 데이터베이스 마이그레이션을 실행해야 합니다. Passport 마이그레이션을 실행하면 애플리케이션에서 필요한 클라이언트와 엑세스 토큰을 저장하는 테이블이 생성됩니다.

    php artisan migrate

> {note} passport 기본 마이그레이션을 사용하지 않는다면, `AppServiceProvider`파일의 `register` 메소드 안에서 `Passport::ignoreMigrations` 를 호출해야합니다. `php artisan vendor:publish --tag=passport-migrations` 명령어를 사용하여 기본 마에그레이션 파일을 확인할 수 있습니다.

다음으로, `passport:install` 명령어를 실행해야합니다. 이 명령어는 안전한 엑세스 토큰을 생성하는데 필요한 암호화 키를 생성합니다. 추가적으로, 명령어는 엑세스 토큰을 생성하는데 사용되는 "personal access" 그리고 "password grant" 클라이언트를 생성합니다.

    php artisan passport:install

이 명령어를 실행한 후에, `App\User` 모델에 `Laravel\Passport\HasApiTokens` 트레이트-trait 를 추가하십시오. 이 트레이트-trait는 모델에 인증된 사용자의 토큰과 범위를 확인하기 위한 몇가지 헬퍼 메소드를 제공합니다.

    <?php

    namespace App;

    use Laravel\Passport\HasApiTokens;
    use Illuminate\Notifications\Notifiable;
    use Illuminate\Foundation\Auth\User as Authenticatable;

    class User extends Authenticatable
    {
        use HasApiTokens, Notifiable;
    }

다음으로, `AuthServiceProvider` 의 `boot` 메소드에서 `Passport::routes` 메소드를 호출해야 합니다. 이 메소드는 엑세스 토큰을 발급하는 라우트와 엑세스 토큰, 클라이언트 그리고 개인용 엑세스 토큰을 해제하는 라우트를 등록합니다.

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

마지막으로, `config/auth.php` 설정 파일에서 guard `api` 인증 `driver` 옵션을 `passport` 로 변경해야 합니다. 이렇게 하면, 인증 API request이 유입될 때 애플리케이션이 Passport의 `TokenGuard` 를 사용합니다.

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

### 마이그레이션 커스터마이징

Passport의 기본 마이그레이션을 사용하지 않으려면, `AppServiceProvider` 의 `register` 메소드에서 `Passport::ignoreMigrations` 메소드를 호출해야합니다. `php artisan vendor:publish --tag=passport-migrations` 를 사용하여 기본 마이그레이션을 가져올 수 있습니다.

기본적으로 Passport는 정수 컬럼을 사용하여 `user_id` 를 저장합니다. 애플리케이션이 사용자를 구별하기 위해 다른 유형의 컬럼 (예 : UUID)을 사용하는 경우 기본 Passport 마이그레이션을 가져온 후 수정해야합니다.

<a name="frontend-quickstart"></a>
### 빠른 프론트엔드 시작하기

> {note} Passport 의 Vue 컴포넌트를 사용하려면, 여러분은 [Vue](https://vuejs.org) 자바스크립트 프레임워크를 사용해야만 합니다. 또한 이 컴포넌트는 부트스트랩 CSS 프레임워크도 사용합니다. 이러한 툴들을 사용하지 않더라도, 여러분의 고유한 프론트엔드 구현을 위한 주요한 참고 자료로 사용될 수 있습니다.

Passport 는 여러분의 사용자가 클라이언트와 개인용 엑세스 토큰을 생성하는 것을 가능하게 하는 JSON API를 제공합니다. 하지만 이런 API에 대한 프론트엔드를 구성하는데는 시간이 필요합니다. 그래서 Pssport는 여러분이 사용할 수 있는 구현 예제 또는 고유한 구현을 위한 참고가 될 수 있도록 사전에 작성해놓은 [Vue](https://vuejs.org) 컴포넌트를 포함하고 있습니다.

Passport Vue 컴포넌트를 퍼블리싱 하려면, `vendor:publish` 아티즌 명령어를 사용하면 됩니다.

    php artisan vendor:publish --tag=passport-components

퍼블리싱이 끝난 컴포넌트는 `resources/js/components` 디렉토리에 설치됩니다. 컴포넌트가 퍼블리싱되고 나면, `resources/js/app.js` 파일에 등록해야합니다.

    Vue.component(
        'passport-clients',
        require('./components/passport/Clients.vue').default
    );

    Vue.component(
        'passport-authorized-clients',
        require('./components/passport/AuthorizedClients.vue').default
    );

    Vue.component(
        'passport-personal-access-tokens',
        require('./components/passport/PersonalAccessTokens.vue').default
    );

> {note} 라라벨 v5.7.19에 따라, 컴포넌트를 등록할때 `.default` 를 덧붙이는 것은 콘솔상에서 에러를 유발합니다. 이러한 변경사항에 대한 자세한 설명은 [라라벨 Mix v4.0.0 릴리즈 노트](https://github.com/JeffreyWay/laravel-mix/releases/tag/v4.0.0)를 참고해주십시오.

컴포넌트를 등록하고 나서 asset을 컴파일 하기 위해서 `npm run dev`를 실행하십시오. asset을 컴파일 하고나면, 클라이언트와 개인용 엑세스 토큰을 생성하기 위해서 애플리케이션의 템플릿에 다음 코드를 복사하십시오:

    <passport-clients></passport-clients>
    <passport-authorized-clients></passport-authorized-clients>
    <passport-personal-access-tokens></passport-personal-access-tokens>

<a name="deploying-passport"></a>
### Passport 배포하기

Passport 를 실서버에 맨 처음 배포할 때, `passport:keys` 명령어가 필요할 수 있습니다. 이 명령어는 액세스 토큰을 생성하기 위해 Passport에서 필요한 암호화된 키를 생성합니다. 생성 된 키는 일반적으로 소스 컨트롤에 유지되지 않습니다.

    php artisan passport:keys

필요한 경우 Passport의 키를 로드해야 하는 경로를 정의 할 수 있습니다. 이를 위해 `Passport::loadKeysFrom` 메소드를 사용할 수 있습니다 :

    /**
     * Register any authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();

        Passport::routes();

        Passport::loadKeysFrom('/secret-keys/oauth');
    }

또한 `php artisan vendor:publish --tag=passport-config`를 사용하여 Passport의 설정 파일을 내보낼 수 있습니다. 그러면 환경 변수에서 암호화 키를 로드하는 옵션이 제공됩니다.

    PASSPORT_PRIVATE_KEY="-----BEGIN RSA PRIVATE KEY-----
    <private key here>
    -----END RSA PRIVATE KEY-----"

    PASSPORT_PUBLIC_KEY="-----BEGIN PUBLIC KEY-----
    <public key here>
    -----END PUBLIC KEY-----"

<a name="configuration"></a>
## 설정하기

<a name="token-lifetimes"></a>
### 토큰 지속시간

기본적으로 Passport는 엑세스 토큰을 오랫동안 유지합니다.(일년 후에 만료됩니다) 토큰의 지속시간을 늘리거나 줄이려면  `tokensExpireIn`, `refreshTokensExpireIn` 그리고 `personalAccessTokensExpireIn` 메소드를 사용하면 됩니다. 이 메소드는 `AuthServiceProvider` 의 `boot` 메소드에서 호출되어야 합니다.

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
    }

<a name="overriding-default-models"></a>
### 기본모델 오버라이딩

Passport가 내부적으로 사용하는 모델을 자유롭게 확장 할 수 있습니다. 그런 다음 Passport에 `Passport` 클래스를 통해 커스텀 모델을 사용하도록 지시 할 수 있습니다 :

    use App\Models\Passport\Client;
    use App\Models\Passport\Token;
    use App\Models\Passport\AuthCode;
    use App\Models\Passport\PersonalAccessClient;

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
## 엑세스 토큰 발급하기

승인 코드와 함께 OAuth2를 사용하는 것은 대부분의 개발자가 OAuth2를 사용하는 가장 익숙한 방법입니다. 승인 코드를 사용할 때, 클라이언트 애플리케이션은 사용자를 서버로 리다이렉션 시켜서, 클라이언트에 대한 엑세스 토큰을 발급하는 요청-request를 승인하거나, 거부하게 됩니다.

<a name="managing-clients"></a>
### 클라이언트 관리

먼저, 애플리케이션의 API와 인터렉션을 해야하는 애플리케이션을 작성하는 개발자는 하나의 "클라이언트"를 만들어 애플리케이션에 등록해야 합니다. 일반적으로 이것은 애플리케이션의 이름과 사용자가 reqeust을 승인한 뒤에 애플리케이션이 리다렉션 할 수 있는 URL을 제공하여 구성합니다.

#### `passport:client` 명령어

클라이언트를 만드는 가장 간단한 방법은 `passport:client` 아티즌 명령어를 사용하는 것입니다. 이 명령어는 OAuth2 기능을 테스트하는 여러분의 고유한 클라이언트를 생성하는데 사용될 수 있습니다. 여러분이 `client` 명령어를 실행하면, Passport는 클리이언트에 대한 보다 자세한 정보를 물어보는 메세지를 표시하고 클라이언트의 ID 와 암호를 제공합니다.

    php artisan passport:client

**URL 리다이렉션**

클라이언트에 여러 개의 URL 리디렉션을 허용하려면 `passport:client` 명령으로 URL을 입력하라는 메시지가 표시됐을 때 쉼표로 구분 된 목록을 입력하여 지정할 수 있습니다.

    http://example.com/callback,http://examplefoo.com/callback

> {note} 쉼표가 포함 된 URL은 모두 인코딩해야합니다.

#### JSON API

여러분의 사용자는 `client` 명령어를 사용할 수 없기 때문에, Passport 는 클라이언트를 생성하는데 사용할 수 있는 JSON API를 제공합니다. 이렇게 하면, 일일이 클라이언트를 생성하고, 수정하고, 삭제하는 컨트롤러 코드를 구성해야 하는 문제에서 벗어날 수 있습니다.

그렇지만, Passport JSON API에 대응하는 대시보드를 통해서 사용자가 클라이언트를 관리할 수 있도록 프론트 엔드를 구성해야 합니다. 아래에서, 클라이언트를 관리하는 모든 API 엔드포인트를 확인해보겠습니다. 편의를 위해서, 엔드 포인트에 대한 HTTP request-요청을 만드는 데모에는 [Axios](https://github.com/axios/axios)를 사용하겠습니다.

JSON API는 `web` 및 `auth` 미들웨어에 의해 보호됩니다. 따라서 자신의 애플리케이션에서만 호출 할 수 있습니다. 외부 소스에서는 호출 할 수 없습니다.

> {tip} 만약 클라이언트 관리용 전체 프론트 엔드를 자체적으로 구현하는 것을 원하지 않는다면, [빠르게 프론트 엔드 시작하기](#frontend-quickstart)를 사용하여, 프론트엔드 전체 기능을 구성할 수 있습니다.

#### `GET /oauth/clients`

이 라우트는 인증된 사용자의 모든 클라이언트들을 반환합니다. 이는 주로 사용자의 모든 클라이언트 목록을 표시하고 수정이나 삭제하고자 할 때 유용합니다.

    axios.get('/oauth/clients')
        .then(response => {
            console.log(response.data);
        });

#### `POST /oauth/clients`

이 라우트는 새로운 클라이언트를 생성하는데 사용됩니다. 여기에는 두개의 데이터가 필요합니다. 클라이언트의 `name`과 한개의 `redirect` URL입니다. `redirect` URL은 request-요청에 대한 접근이 승인 또는 거부된 뒤에 사용자가 리다이렉션 되는 곳입니다.

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

#### `PUT /oauth/clients/{client-id}`

이 라우트는 새로운 클라이언트를 생성하는데 사용됩니다. 여기에는 두개의 데이터가 필요합니다. 클라이언트의 `name`과 한개의 `redirect` URL입니다. `redirect` URL은 request-요청에 대한 접근이 승인 또는 거부된 뒤에 사용자가 리다이렉션 되는 곳입니다. 이 라우트는 수정된 클라이언트의 인스턴스를 반환합니다.

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

#### `DELETE /oauth/clients/{client-id}`

이 라우트는 클라이언트를 삭제할 때 사용됩니다.

    axios.delete('/oauth/clients/' + clientId)
        .then(response => {
            //
        });

<a name="requesting-tokens"></a>
### 토큰 요청

#### 권한승인을 위한 리다이렉팅

클라이언트가 생성되고 나서, 개발자는 클라이언트 ID와 암호키를를 사용하여 권한 승인을 요청하고 애플리케이션의 토큰에 엑세스 할 수 있습니다. 먼저 API를 사용하는 애플리케이션이 다음의 `/oauth/authorize` 라우트로 리다이렉트 요청을 하도록 만들어야 합니다.

    Route::get('/redirect', function (Request $request) {
        $request->session()->put('state', $state = Str::random(40));

        $query = http_build_query([
            'client_id' => 'client-id',
            'redirect_uri' => 'http://example.com/callback',
            'response_type' => 'code',
            'scope' => '',
            'state' => $state,
        ]);

        return redirect('http://your-app.com/oauth/authorize?'.$query);
    });

> {tip} `/oauth/authorize` 라우트는 `Passport::routes` 메소드에서 이미 정의되어 있습니다. 이 라우트를 수동으로 등록할 필요가 없습니다.

#### Request-요청 승인

권한 승인 요청을 받으면, Passport는 자동으로 사용자가 템플릿을 표시하여 승인 요청을 수락하거나 거부할 수 있게 합니다. 요청이 승인되면, 애플리케이션에 의해서 지정된 `redirect_uri` 로 리다이렉션 됩니다. `redirect_uri` 는 클라이언트가 생성될 때 지정되었던 `redirect` URL과 일치해야 합니다.

권한 승인 화면을 커스터마이징 하고싶다면, `vendor:publish` 아티즌 명령어를 사용하여 Passport 뷰 파일을 퍼블리싱할 수 있습니다. 퍼블리싱된 뷰파일은 `resources/views/vendor/passport` 디렉토리에 위치합니다. 이제 이를 수정하면 됩니다.

    php artisan vendor:publish --tag=passport-views

때로는 자체 클라이언트에 권한 부여 할 때와 같이 권한 부여 입력창를 건너 뛰고 싶을 수 있습니다. 클라이언트 모델에서 `skipsAuthorization` 메소드를 정의하여 이를 수행 할 수 있습니다. `skipsAuthorization`가 `true`를 반환하면 클라이언트는 승인되고 즉시 `redirect_uri`로 리디렉션됩니다.

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

#### 승인 코드를 엑세스 토큰으로 변환하기

사용자가 인증 요청을 승인하면, 해당 인증을 사용 할 애플리케이션으로 리다이렉션됩니다. 인증을 사용할 어플리케이션은 리다이렉션으로 자신에게 돌아오면, 다른 경로로 이동하기 전에 `state` 파라미터에 저장된 값을 확인해야합니다. state 파라미터가 사용자와 일치하면 애플리케이션에 `POST`로 액세스 토큰을 요청해야합니다. 이 요청에는 사용자가 인증 요청을 승인 할 때 애플리케이션에서 발행 한 승인 코드를 포함해야합니다. 이 예제에서는 Guzzle HTTP 라이브러리를 사용하여 `POST`로 요청합니다.

(역자주: 원문을 직역하면 너무 이상한 듯 하여 이해하기 쉽도록 의역을 많이 하였습니다. 문장이 이상하다고 느껴지신다면 원문을 참고 바랍니다)

    Route::get('/callback', function (Request $request) {
        $state = $request->session()->pull('state');

        throw_unless(
            strlen($state) > 0 && $state === $request->state,
            InvalidArgumentException::class
        );

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

`/oauth/token` 라우트는 `access_token`, `refresh_token`, 그리고 `expires_in`을 포함하는 JSON 응답-response를 반환합니다. `expires_in` 속성은 엑세스 토큰이 만료되기까지의 (초)를 가지고 있습니다.

> {tip} `/oauth/authorize` 라우트와 같이 `/oauth/token` 라우트는 `Passport::routes` 메소드에 의해서 정의됩니다. 이 라우트를 수동으로 등록할 필요가 없습니다. 기본적으로 이 경로는 `ThrottleRequests` 미들웨어의 설정을 사용해여 제한합니다.

<a name="refreshing-tokens"></a>
### 토큰 갱신하기

애플리케이션에서 사용기간이 짧은 엑세스 토큰을 발급한다면, 사용자는 엑세스 토큰을 발급할 때 제공된 리프레쉬 토큰을 사용하여 엑세스 토큰을 새롭게 갱신해야 합니다. 이 예제에서 Guzzle HTTP 라이브러리르 사용하여 토큰을 갱신해보겠습니다.

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

`/oauth/token` 라우트는 `access_token`, `refresh_token`, 그리고 `expires_in`을 포함하는 JSON 응답-response를 반환합니다. `expires_in` 속성은 엑세스 토큰이 만료되기까지의 (초)를 가지고 있습니다.

<a name="password-grant-tokens"></a>
## 패스워드 Grant 토큰

OAuth2 패스워드 그랜트는 모바일 애플리케이션과 같은 여러분의 다른 클라이언트가 이메일 주소와 / 사용자 이름 및 암호를 사용하여 엑세스 토큰을 획득할 수 있도록 해줍니다. 이를 통해 OAuth2의 승인 코드 리다이렉션 request를 필요로 하지 않고도 엑세스 토큰을 안전하게 발급할 수 있도록 해줍니다.

<a name="creating-a-password-grant-client"></a>
### 패스워드 Grant 클라이언트 생성하기

패스워드 grant를 통해서 애플리케이션에서 토큰을 발급하기 전에, 패스워드 grant 클라이언트를 생성해야 합니다. `passport:client` 명령어에 `--password` 옵션을 지정하여 이를 생성할 수 있습니다. `passport:install` 명령어를 이미 실행했다면, 이 명령어를 실행할 필요는 없습니다.

    php artisan passport:client --password

<a name="requesting-password-grant-tokens"></a>
### 토큰 요청하기

패스워드 grant 클라이언트가 생성되면, 사용자의 이메일과 패스워드와 함께 `/oauth/token` 라우트에 엑세스 토큰 발급 `POST` request-요청을 보낼 수 있습니다. 기억할점은, 이 라우트는 `Passport::routes`메소드에 의해서 이미 등록되어 있기 때문에, 직접 라우트를 등록할 필요가 없다는 것입니다. 요청-request가 성공적이라면, 서버로 부터 `access_token` 과 `refresh_token` 가 담긴 JSON 응답-response를 받습니다.

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

> {tip} 엑세스 토큰은 기본적으로 오랜시간 지속됩니다. 그렇지만 필요하다면, 자유롭게 [엑세스 토큰 지속시간을 설정](#configuration) 할 수 있습니다.

<a name="requesting-all-scopes"></a>
### 모든 범위에 대하여 요청하기

패스워드 grant 또는 client 자격증명 grant를 사용할 때, 여러분은 애플리케이션에서 지원하는 모든 범위의 토큰을 승인 하고자 할수 있습니다. 이렇게 하려면 `*` 범위로 요청하면 됩니다. `*` 범위를 요청하면 토큰 인스턴스의 `can` 메소드가 항상 `true` 를 반환할 것입니다. 이 스코프는 `password` grant를 통해 발행 된 토큰에만 할당하는 것이 좋습니다.

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

<a name="customizing-the-username-field"></a>
### 사용자 이름 필드 사용자 정의하기

암호를 사용하여 인증 할 때 Passport는 모델의 `email` 속성을 "username"으로 사용합니다. 그러나 모델에서 `findForPassport` 메소드를 정의함으로써 이 동작을 커스터마이징 할 수 있습니다.

    <?php

    namespace App;

    use Laravel\Passport\HasApiTokens;
    use Illuminate\Notifications\Notifiable;
    use Illuminate\Foundation\Auth\User as Authenticatable;

    class User extends Authenticatable
    {
        use HasApiTokens, Notifiable;

        /**
         * Find the user instance for the given username.
         *
         * @param  string  $username
         * @return \App\User
         */
        public function findForPassport($username)
        {
            return $this->where('username', $username)->first();
        }
    }

<a name="customizing-the-password-validation"></a>
### 패스워드 유효성 검사 커스터마이징

패스워드를 사용하여 인증 할 때 Passport는 모델의 `password` 속성을 사용하여 주어진 패스워드의 유효성을 검사합니다. 모델에 `password` 속성이 없거나 패스워드 유효성 검사 로직을 커스터마이징 하고자 하는 경우 모델에 `validateForPassportPasswordGrant` 메소드를 정의 할 수 있습니다.

    <?php

    namespace App;

    use Laravel\Passport\HasApiTokens;
    use Illuminate\Support\Facades\Hash;
    use Illuminate\Notifications\Notifiable;
    use Illuminate\Foundation\Auth\User as Authenticatable;

    class User extends Authenticatable
    {
        use HasApiTokens, Notifiable;

        /**
        * Validate the password of the user for the Passport password grant.
        *
        * @param  string $password
        * @return bool
        */
        public function validateForPassportPasswordGrant($password)
        {
            return Hash::check($password, $this->password);
        }
    }

<a name="implicit-grant-tokens"></a>
## 묵시적 grant 토큰

묵시적 grant는 승인 코드 grant와 비슷합니다. 그렇지만, 클라이언트에서 반환되는 토큰은 승인 코드를 교환하지 않습니다. 이 grant는 클라이언트 자격증명을 안전하게 저장할 수 없는 자바스크립트 또는 모바일 애플리케이션에서 가장 일반적으로 사용됩니다. grant를 활성화하려면, `AuthServiceProvider` 에서 `enableImplicitGrant` 메소드를 호출하면 됩니다.

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

grant가 활성화 되면, 개발자는 애플리케이션에서 엑세스 토큰을 요청하는데 클라이언트 ID를 사용할 수 있습니다. 이를 처리하는 애플리케이션은 다음처럼 리다이렉트 요청-request를 여러분의 애플리케이션의 `/oauth/authorize` 라우트로 보내야 합니다.

    Route::get('/redirect', function (Request $request) {
        $request->session()->put('state', $state = Str::random(40));

        $query = http_build_query([
            'client_id' => 'client-id',
            'redirect_uri' => 'http://example.com/callback',
            'response_type' => 'token',
            'scope' => '',
            'state' => $state,
        ]);

        return redirect('http://your-app.com/oauth/authorize?'.$query);
    });

> {tip} `/oauth/authorize` 라우트는 `Passport::routes` 메소드에 의해서 정의된다는 것을 기억하십시오. 이 라우트를 수동으로 등록할 필요가 없습니다.

<a name="client-credentials-grant-tokens"></a>
## 클라이언트의 자격증명을 위한 Grant 토큰

클라이언트의 자격증명을 위한 Grant 는 시스템간의 인증에 적합합니다. 예를 들어, API를 통해서 관리 작업을 수행하도록 예약된 스케줄링 job에서 이 grant를 사용할 수 있습니다.

애플리케이션이 클라이언트의 자격증명 권한 인증를 통해 토큰을 발행하기 전에, 사용자는 클라이언트의 자격증명 인증 클라이언트를 생성해야합니다. `passport:client` 명령의 `--client` 옵션을 사용하면 됩니다 :

    php artisan passport:client --client

다음으로, 이 권한 유형을 사용하려면 `CheckClientCredentials` 미들웨어를 `app/Http/Kernel.php` 파일의 `$routeMiddleware` 프로퍼티에 추가해야합니다 :


    use Laravel\Passport\Http\Middleware\CheckClientCredentials;

    protected $routeMiddleware = [
        'client' => CheckClientCredentials::class,
    ];

그 다음 이 미들웨어를 라우트에 추가하십시오:

    Route::get('/orders', function (Request $request) {
        ...
    })->middleware('client');

특정 범위의 라우트에 대한 액세스를 제한하기 위해 `client` 미들웨어를 라우트에 연결할 때, 쉼표로 분리 된 필수 범위의 목록을 지정 할 수 있습니다.

    Route::get('/orders', function (Request $request) {
        ...
    })->middleware('client:check-status,your-scope');

### Retrieving Tokens

이 권한 유형을 사용하여 토큰을 검색하려면 `oauth/token`의 엔드 포인트에 요청하십시오 :

    $guzzle = new GuzzleHttp\Client;

    $response = $guzzle->post('http://your-app.com/oauth/token', [
        'form_params' => [
            'grant_type' => 'client_credentials',
            'client_id' => 'client-id',
            'client_secret' => 'client-secret',
            'scope' => 'your-scope',
        ],
    ]);

    return json_decode((string) $response->getBody(), true)['access_token'];

<a name="personal-access-tokens"></a>
## 개인용 엑세스 토큰

때로는, 사용자가 일반적인 승인 코드 리다이렉션 플로우를 거치지 않고 엑세스 토큰을 발급하기를 원할 수도 있습니다. 사용자가 애플리케이션의 UI를 통해 자신에게 토큰을 발행 할 수 있게 하면, 사용자가 API를 테스트해 볼 수도 있고, 일반적으로 액세스 토큰을 발행하기 위한 더 간단한 방법으로도 사용할 수 있습니다.

<a name="creating-a-personal-access-client"></a>
### 개인용 엑세스 클라이언트 생성하기

애플리케이션이 개인용 엑세스 토큰을 발급 할 수 있도록 하기 전에, 개인용 엑세스 클라이언트를 생성해야 합니다. 이렇게 하려면 `passport:client` 명령어에 `--personal` 옵션을 사용하면 됩니다. 만약 이미 `passport:install`명령어를 실행했다면, 이 명령어를 실행할 필요는 없습니다.

    php artisan passport:client --personal

이미 개인 액세스 클라이언트를 정의한 경우 Passport가 `personalAccessClientId` 메소드를 사용하여 이것를 사용 할 수 있습니다. 일반적으로 이 메소드는 `AuthServiceProvider` 의 `boot` 메소드에서 호출되어야합니다 :

    /**
     * Register any authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();

        Passport::routes();

        Passport::personalAccessClientId('client-id');
    }

<a name="managing-personal-access-tokens"></a>
### 개인용 엑세스 토큰 관리하기

개인용 엑세스 클라이언트를 생성하고 나면, `User` 모델 인스턴스의 `createToken` 메소드를 사용하여 주어진 사용자를 위한 토큰을 발급할 수 있습니다. `createToken` 메소드는 토큰의 이름을 첫번째 인자로, [범위](#token-scopes)의 배열을 선택적으로 두번째 인자로 받습니다.

    $user = App\User::find(1);

    // Creating a token without scopes...
    $token = $user->createToken('Token Name')->accessToken;

    // Creating a token with scopes...
    $token = $user->createToken('My Token', ['place-orders'])->accessToken;

#### JSON API

passport는 이미 개인용 엑세스 토큰을 관리하는 JSON APIf를 포함하고 있습니다. 이 API와 함께 여러분의 고유한 프론트 엔드를 구성하여 사용자에게 개인용 엑세스 토큰을 관리할 수 있는 대시보드를 제공할 수 있습니다. 아래에서 개인용 엑세스 토큰을 관리하는 모든 API 엔드포인트를 확인해보겠습니다. 편의를 위해서, 엔드포인트에 대한 HTTP request-요청을 만드는 데모에는 [Axios](https://github.com/mzabriskie/axios)를 사용하겠습니다.

JSON API는 `web` 및 `auth` 미들웨어에 의해 보호됩니다. 따라서 자신의 애플리케이션에서만 호출 할 수 있습니다. 외부 소스에서는 호출 할 수 없습니다.

> {tip} 만약 개인용 엑세스 토큰 관리용 전체 프론트 엔드를 자체적으로 구현하는 것을 원하지 않는다면, [빠르게 프론트 엔드 시작하기](#frontend-quickstart)를 사용하여, 프론트엔드 전체 기능을 구성할 수 있습니다.

#### `GET /oauth/scopes`

이 라우트는 애플리케이션에서 정의된 모든 [스코프-범위](#token-scopes)를 반환합니다. 이 라우트를 사용자가 개인용 엑세스 토큰에 할당된 범위를 나열하는데 사용할 수 있습니다.

    axios.get('/oauth/scopes')
        .then(response => {
            console.log(response.data);
        });

#### `GET /oauth/personal-access-tokens`

이 라우트는 인증된 사용자가 생성한 모든 개인용 엑세스 토큰을 반환합니다. 모든 사용자 토큰을 목록을 확인하는데 유용하여, 주로 이를 수정하거나, 삭제할 수 있습니다.

    axios.get('/oauth/personal-access-tokens')
        .then(response => {
            console.log(response.data);
        });

#### `POST /oauth/personal-access-tokens`

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

#### `DELETE /oauth/personal-access-tokens/{token-id}`

이 라우트는 개인용 엑세스 토큰을 삭제하는데 사용됩니다.

    axios.delete('/oauth/personal-access-tokens/' + tokenId);

<a name="protecting-routes"></a>
## 라우트 보호하기

<a name="via-middleware"></a>
### 미들웨어를 통해서

Passport는 유입되는 request-요청에 대해서 엑세스 토큰을 검증하는 [사용자 승인 guard](/docs/{{version}}/authentication#adding-custom-guards)를 포함하고 있습니다. `api` guard가 `passport` 드라이버를 사용하도록 설정하였다면, 엑세스 토큰 검사가 필요한 라우트에 `auth:api` 미들웨어를 지정하기만 하면 됩니다.

    Route::get('/user', function () {
        //
    })->middleware('auth:api');

<a name="passing-the-access-token"></a>
### 엑세스 토큰 전달하기

Passport에 의해서 보호되는 라우트를 호출할 때, 애플리케이션의 API 사용자는 그들의 요청-request의 `Authorization` 헤더에 `Bearer` 토큰으로 엑세스 토큰을 지정해야 합니다. 예를 들어 Guzzle HTTP 라이브러리를 사용해보겠습니다.

    $response = $client->request('GET', '/api/user', [
        'headers' => [
            'Accept' => 'application/json',
            'Authorization' => 'Bearer '.$accessToken,
        ],
    ]);

<a name="token-scopes"></a>
## 토큰 스코프(범위)

스코프는 계정에 대한 엑세스 승인을 요청할 때, 여러분의 API 클라이언트가 제한된 권한을 지정하여 요청하도록 합니다. 예를 들어, e-커머스 애플리케이션을 구성한다면, 전체 API 사용자에게 주문을 할 수 있는 권한을 줄 필요는 없을것입니다. 대신에, 사용자에게 주문 배송상황에 엑세스 할 수 있는 권한을 주면 됩니다. 다시 말해 스코프는 여러분의 애플리케이션 사용자가 써드파티 애플리케이션을 통해서 실행할 수 있는 액션을 제한할 수 있습니다.

<a name="defining-scopes"></a>
### 스코프 정의하기

API의 범위(scope)는 `AuthServiceProvider` 의 `boot` 메소드에서 `Passport:tokensCan` 메소드를 사용하여 정의할 수 있습니다. `tokensCan` 메소드는 스코프의 이름과, 설명에 대한 배열을 인자로 받습니다. 스코프 설명은 권한 승인 페이지에서 사용자에게 보여주려는 어떠한 내용도 가능합니다.

    use Laravel\Passport\Passport;

    Passport::tokensCan([
        'place-orders' => 'Place orders',
        'check-status' => 'Check order status',
    ]);

<a name="default-scope"></a>
### 기본 스코프

클라이언트가 특정한 스코프를 요청하지 않으면 `setDefaultScope` 메소드를 사용하여 토큰에 기본적인 스코프를 지저하도록 Passport 서버를 구성할 수 있습니다. 일반적으로 이 메소드는 `AuthServiceProvider` 의 `boot` 메소드 안에서 호출해야 합니다.

    use Laravel\Passport\Passport;

    Passport::setDefaultScope([
        'check-status',
        'place-orders',
    ]);

<a name="assigning-scopes-to-tokens"></a>
### 토큰에 스코프 할당하기

#### 승인 코드를 요청할 때

승인 코드 grant를 사용하여 엑세스 토큰을 요청할 때, 사용자는 `scope` 쿼리 스트링 인자를 통해서 원하는 scope-범위를 지정해야 합니다. `scope` 파라미터는 scope-범위의 목록들을 공백으로 구분한 내용이어야 합니다.

    Route::get('/redirect', function () {
        $query = http_build_query([
            'client_id' => 'client-id',
            'redirect_uri' => 'http://example.com/callback',
            'response_type' => 'code',
            'scope' => 'place-orders check-status',
        ]);

        return redirect('http://your-app.com/oauth/authorize?'.$query);
    });

#### 개인용 엑세스 토큰 발급할 때

`User` 모델의 `createToken` 메소드를 사용하여 개인용 엑세스 토큰을 발급하고자 한다면, 메소드의 두번째 인자로 원하는 scope를 배열로 전달할 수 있습니다.

    $token = $user->createToken('My Token', ['place-orders'])->accessToken;

<a name="checking-scopes"></a>
### Scope 확인하기

Passport 는 유입되는 request-요청이 주어진 코드에 의해서 권한이 확인된 토큰에 의해서 승인되었는지 확인할 수 있는 두개의 미들웨어를 포함하고 있습니다. 이를 사용하려면 `app/Http/Kernel.php` 파일의 `$routeMiddleware` 속성에 다음과 같이 정의해야 합니다.

    'scopes' => \Laravel\Passport\Http\Middleware\CheckScopes::class,
    'scope' => \Laravel\Passport\Http\Middleware\CheckForAnyScope::class,

#### 전체 Scope-범위 확인하기

`scopes` 미들웨어는 라우트에 할당하여 유입되는 request-요청의 엑세스 토큰이 *전체* scope인지 확인하는데 사용할 수 있습니다.

    Route::get('/orders', function () {
        // Access token has both "check-status" and "place-orders" scopes...
    })->middleware('scopes:check-status,place-orders');

#### 특정 범위 확인하기

`scope` 미들웨어는 라우트에 할당하여 유입되는 request-요청의 엑세스 토큰이 *최소한 하나*의 scope에 해당하는지 확인하는데 사용할 수 있습니다.

    Route::get('/orders', function () {
        // Access token has either "check-status" or "place-orders" scope...
    })->middleware('scope:check-status,place-orders');

#### 토큰 인스턴스에서 scope-범위 확인하기

엑세스 토큰이 인증된 request-요청이 애플리케이션에 전달되면, 인증된 `User` 인스턴스의 `tokenCan` 메소드를 사용하여 토큰이 주어진 scope에 해당하는지 확인할 수 있습니다.

    use Illuminate\Http\Request;

    Route::get('/orders', function (Request $request) {
        if ($request->user()->tokenCan('place-orders')) {
            //
        }
    });

#### 추가 스코프 메소드

`scopeIds` 메소드는 정의 된 모든 ID / 이름의 배열을 반환합니다 :

    Laravel\Passport\Passport::scopeIds();

`scopes` 메소드는 정의 된 모든 범위의 배열을 `Laravel\Passport\Scope` 의 인스턴스로 반환합니다 :

    Laravel\Passport\Passport::scopes();

`scopesFor` 메소드는 주어진 ID / 이름과 일치하는 `Laravel\Passport\Scope` 인스턴스의 배열을 반환합니다 :

    Laravel\Passport\Passport::scopesFor(['place-orders', 'check-status']);

`hasScope` 메소드를 사용하여 주어진 스코프가 정의되었는지를 확인 할 수 있습니다 :

    Laravel\Passport\Passport::hasScope('place-orders');

<a name="consuming-your-api-with-javascript"></a>
## 자바스크립트로 API 사용하기

API를 구성할 때 자바스크립트 애플리케이션에서 여러분의 API를 사용할 수 있으면, 매우 편리합니다. 이런 API 개발 방식을 사용하면 여러분의 애플리케이션이 전세계로 공유되는 것과 동일한 API를 사용할 수 있게 됩니다. 웹 애플리케이션, 모바일 애플리케이션, 써드파티 애플리케이션 및 다양한 패키지 관리자를 통해 퍼블리싱 할 수 있는 SDK에서 동일한 API를 사용할 수 있습니다.

일반적으로, 여러분의 API를 자바스크립트 애플리케이션에서 사용하고자 한다면, 애플리케이션에 엑세스 토큰을 수동으로 보내고, 매번 애플리케이션에 요청-request 할때 마다 이 토큰을 전달해야 합니다. 그렇지만 Passport는 이미 이를 처리하는 미들웨어를 포함하고 있습니다. 여러분에게 필요한 것은 `app/Http/Kernel.php` 파일의 `web` 미들웨어 그룹에 `CreateFreshApiToken` 미들웨어를 추가하는 것 뿐입니다.

    'web' => [
        // Other middleware...
        \Laravel\Passport\Http\Middleware\CreateFreshApiToken::class,
    ],

> {note} 미들웨어 스택에서 `CreateFreshApiToken` 미들웨어가 나오기 전에 `EncryptCookies` 미들웨어가 있는지 확인해야합니다.

이 Passport 미들웨어는 `laravel_token` 쿠키를 반환되는 응답-response에 덧붙입니다. 이 쿠키는 Passport 가 여러분의 자바스크립트 애플리케이션에서 인증 API 요청-request에서 사용할 암호화된 JWT를 가지고 있습니다. 이제 액세스 토큰을 명시적으로 전달하지 않고도 여러분의 애플리케이션에 API에 요청-request를 만들 수 있습니다.

    axios.get('/api/user')
        .then(response => {
            console.log(response.data);
        });

#### 쿠키 이름의 커스터마이징

필요하다면 `Passport::Cookie` 메소드를 사용하여 `laravel_token` 쿠키 이름을 커스터마이징 할 수 있습니다. 일반적으로 이 메소드는 `AuthServiceProvider` 의 `boot` 메소드에서 호출되어야합니다 :

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

#### CSRF Protection

이 인증 메소드를 사용할 때는 유효한 CSRF 토큰 헤더가 요청-request에 포함되어 있는지 확인해야합니다. 라라벨의 기본 자바스크립트 스캐폴딩은 Axios 인스턴스를 포함하는데, Axios 인스턴스는 자동으로 암호화 된 `XSRF-TOKEN` 쿠키의 값을 사용하여 same-origin 요청에 대해 `X-XSRF-TOKEN` 헤더를 포함하여 보냅니다.

> {tip} `X-XSRF-TOKEN` 대신 `X-CSRF-TOKEN` 헤더를 보내려면 `csrf_token()`에서 제공한 암호화되지 않은 토큰을 사용해야합니다.

<a name="events"></a>
## 이벤트

Passport 는 엑세스 토큰과 리프레쉬 토큰을 발급할 때 이벤트를 발생시킵니다. 이 이벤트를 사용하여 데이터베이스의 다른 액세스 토큰을 제거하거나 취소 할 수 있습니다. 여러분의 애플리케이션의 `EventServiceProvider` 안에서 이벤트 리스너를 추가할 수 있습니다.

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
## 테스팅

Passport의 `actionAs` 메소드는 현재 인증된 사용자를 지정하는데 사용할 수 있습니다. `actionAs` 메소드에 전달되는 첫번째 인자는 사용자 인스턴스이고, 두번째 인자는 사용자의 토큰에 허용된 스코프 배열입니다.

    use App\User;
    use Laravel\Passport\Passport;

    public function testServerCreation()
    {
        Passport::actingAs(
            factory(User::class)->create(),
            ['create-servers']
        );

        $response = $this->post('/api/create-server');

        $response->assertStatus(201);
    }
