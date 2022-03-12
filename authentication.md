# 인증

- [시작하기](#introduction)
    - [스타터 키트](#starter-kits)
    - [데이터베이스 유의사항](#introduction-database-considerations)
    - [생태계 개요](#ecosystem-overview)
- [빠르게 인증 살펴보기](#authentication-quickstart)
    - [### 스타터 키트 설치](#install-a-starter-kit)
    - [인증된 사용자 조회하기](#retrieving-the-authenticated-user)
    - [라우트 보호하기](#protecting-routes)
    - [로그인 횟수 제한](#login-throttling)
- [수동으로 사용자 인증하기](#authenticating-users)
    - [사용자 기억하기](#remembering-users)
    - [그외 인증 메소드](#other-authentication-methods)
- [HTTP 기본 인증](#http-basic-authentication)
    - [상태를 유지하지 않는 HTTP 기본 인증](#stateless-http-basic-authentication)
- [로그아웃](#logging-out)
    - [다른 디바이스의 세션 무효화](#invalidating-sessions-on-other-devices)
- [비밀번호 확인](#password-confirmation)
    - [설정하기](#password-confirmation-configuration)
    - [라우팅](#password-confirmation-routing)
    - [라우트 보호하기](#password-confirmation-protecting-routes)
- [사용자 정의 Guards 추가하기](#adding-custom-guards)
    - [클로저 형태의 Request Guards](#closure-request-guards)
- [사용자 정의 User 프로바이더 추가하기](#adding-custom-user-providers)
    - [사용자 프로바이더 Contract](#the-user-provider-contract)
    - [인증가능 Contract](#the-authenticatable-contract)
- [소셜 인증](/docs/{{version}}/socialite)
- [이벤트](#events)

<a name="introduction"></a>
## 시작하기

많은 웹 애플리케이션은 사용자가 애플리케이션으로 인증하고 "로그인"할 수있는 방법을 제공합니다. 웹 애플리케이션에서 이 기능을 구현하는 것은 복잡하고 잠재적으로 위험한 작업이 될 수 있습니다. 이러한 이유로 라라벨은 인증을 빠르고 안전하며 쉽게 구현하는 데 필요한 도구를 제공하기 위해 노력하고 있습니다.

내부 시스템에서, 라라벨의 인증 기능은 "가드" 와 "프로바이더"로 구성되어 있습니다. 가드는 사용자가 매 요청-request마다 어떻게 인증되는지 정의합니다. 예를 들어 라라벨은 세션 스토리지와 쿠키를 사용하여 상태를 유지하는 `session` 가드와, 각 요청-request와 함께 전달되는 "API 토큰"을 사용하여 사용자를 인증하는 `token` 가드를 제공합니다.

프로바이더는 저장소에서 사용자를 어떻게 조회 할 수 있는지 정의합니다. 라라벨은 [Eloquent](/docs/{{version}}/eloquent)와 데이터베이스 쿼리 빌더를 사용하여 사용자를 찾을 수 있도록 지원합니다. 또한, 애플리케이션에서 필요로 하는 추가적인 프로바이더를 자유롭게 정의할 수 있습니다.

애플리케이션의 인증 설정은 `config/auth.php` 파일에서 할 수 있습니다. 이 파일에는 라라벨 인증 서비스의 동작을 조정하기위한 몇 가지 잘 문서화 된 옵션이 포함되어 있습니다.

> {tip} 가드와 프로바이더의 "역할"및 "권한"을 혼동해서는 안됩니다. 권한을 통해 사용자의 행동을 승인하기위한 자세한 내용은 [authorization](/docs/{{version}}/authorization) 문서를 참조하세요.

<a name="starter-kits"></a>
### 스타터 키트

빨리 시작하고 싶으세요? 새로운 라라벨 애플리케이션에 [라라벨 애플리케이션 스타터 키트](/docs/{{version}}/starter-kits)를 설치합니다. 데이터베이스를 마이그레이션 한 후 브라우저를 탐색하여 `/register` 또는 애플리케이션에 할당 된 다른 URL을 들어가보세요. 스타터 키트는 전체 인증 시스템을 자동으로 만들어줍니다!

**최종적으로 라라벨 애플리케이션에서 스타터 키트를 사용하지 않기로 선택한 경우에도 [라라벨 Breeze](/docs/{{version}}/starter-kits#laravel-breeze) 스타터 키트를 설치하면, 실제 라라벨 프로젝트의 인증 기능을 구현하는 방법을 모두 배울 수 있습니다.** 라라벨 Breeze는 인증 컨트롤러, 라우트 및 뷰를 생성하므로, 이 파일들의 코드를 통해 라라벨의 인증 기능을 구현하는 방법을 배울 수 있습니다.

<a name="introduction-database-considerations"></a>
### 데이터베이스 유의사항

기본적으로 라라벨은 `app/Models` 디렉토리에 `App\Models\User` [Eloquent model](/docs/{{version}}/eloquent)를 포함합니다. 이 모델은 기본 Eloquent 인증 드라이버와 함께 사용할 수 있습니다. 애플리케이션이 Eloquent를 사용하지 않는다면, 라라벨 쿼리 빌더를 사용하는 `database` 인증 프로바이더를 사용할 수 있습니다.

`App\Models\User` 모델에 대한 데이터베이스 스키마를 생성 할 때 비밀번호 열의 길이가 60자 이상인지 확인하십시오. 물론 새로운 라라벨 애플리케이션에 포함 된 `users` 테이블 마이그레이션은 이미 이 길이를 초과하는 열을 생성합니다.

또한 `users`(또는 이에 상응하는) 테이블에 100 자의 nullable 문자열 `remember_token`열이 포함되어 있는지 확인해야합니다. 이 열은 애플리케이션에 로그인 할 때 "remember me"옵션을 선택한 사용자의 토큰을 저장하는 데 사용됩니다. 다시 말하지만, 새로운 라라벨 애플리케이션에 포함 된 기본 `users` 테이블 마이그레이션에는 이미 이 열이 포함되어 있습니다.

<a name="ecosystem-overview"></a>
### 생태계 개요

라라벨은 인증과 관련된 여러 패키지를 제공합니다. 계속하기 전에 라라벨의 일반적인 인증 생태계를 검토하고 각 패키지의 의도된 목적에 대해 설명할 것입니다.

먼저 인증 작동 방식을 살펴보겠습니다. 웹 브라우저를 사용할 때 사용자는 로그인 form을 통해 사용자 이름과 비밀번호를 입력합니다. 이 인증정보가 맞다면 애플리케이션은 인증된 사용자에 대한 정보를 사용자의 [세션](/docs/{{version}}/session)에 저장합니다. 브라우저에 생성된 쿠키에는 이후 요청들부터 라라벨에서 사용자를 올바른 세션과 연결할 수 있도록 세션 ID가 포함됩니다. 세션 쿠키가 수신되면 라라벨에서 세션 ID를 기반으로 세션 데이터를 검색해서, 인증 정보가 세션에 저장되어있으면 사용자를 "인증된"것으로 간주합니다.

원격 서비스가 API에 액세스하기 위해서 인증해야하는 경우, 웹 브라우저가 없기 때문에 일반적으로 쿠키가 인증에 사용되지 않습니다. 대신 원격 서비스는 각 요청마다 API 토큰을 API로 보냅니다. 애플리케이션은 유효한 API 토큰 테이블에서 수신된 토큰의 유효성을 검사하고, 해당 API 토큰과 관련된 사용자의 요청을 "인증"하고 수행 할 수 있습니다.

<a name="laravels-built-in-browser-authentication-services"></a>
#### 라라벨의 내장 브라우져 인증 서비스

라라벨에는 일반적으로 `Auth` 및 `Session` 파사드를 통해 접근할 수 있는 내장 인증 서비스 및 세션 서비스가 포함되어 있습니다. 이러한 기능은 웹 브라우저의 요청에 대해 쿠키 기반 인증을 제공합니다. 사용자 인증정보를 확인하고 사용자를 인증 할 수 있는 방법을 제공합니다. 또한 이러한 서비스는 사용자 세션에 적절한 인증 데이터를 자동으로 저장하고 사용자 세션 쿠키를 생성합니다. 이 문서에는 이러한 서비스를 사용하는 방법에 대한 설명이 포함되어 있습니다.

**애플리케이션 스타터 키트**

위에서 설명한대로, 이러한 인증 서비스와 상호 작용하는 애플리케이션의 고유한 인증 계층을 직접 만들 수 있습니다. 하지만 더 빨리 시작할 수 있도록 전체 인증 계층에 대한 강력하고 현대적인 스캐폴딩을 제공하는 [무료 패키지](/docs/{{version}}/starter-kits)를 출시했습니다. 이러한 패키지는 [Laravel Breeze](/docs/{{version}}/starter-kits#laravel-breeze), [Laravel Jetstream](/docs/{{version}}/starter-kits#laravel-jetstream) 및 [Laravel Fortify](/docs/{{version}}/fortify)입니다.

_Laravel Breeze_ 는 로그인, 등록, 비밀번호 재설정, 이메일 확인, 비밀번호 확인을 포함한 라라벨의 모든 인증 기능을 최소한으로 구현 한 것입니다. Laravel Breeze의 뷰 레이어는 [Tailwind CSS](https://tailwindcss.com) 스타일과 함께 간단한 [Blade templates](/docs/{{version}}/blade)으로 구성됩니다. 시작하려면 라라벨의 [application starter kits](/docs/{{version}}/starter-kits) 문서를 확인하세요.

_Laravel Fortify_ 는 쿠키 기반 인증과 2단계 인증 및 이메일 확인과 같은 기타 기능을 포함하여, 이 문서에 있는 많은 기능을 구현하는 라라벨용 헤드리스 인증 백엔드입니다. Fortify는 Laravel Jetstream에 대한 인증 백엔드를 제공하거나 [Laravel Sanctum](/docs/{{version}}/sanctum)과 함께 독립적으로 사용하여 Laravel에 인증해야하는 SPA에 인증 기능을 제공 할 수 있습니다.

_[Laravel Jetstream](https://jetstream.laravel.com)_ 은 [Tailwind CSS](https://tailwindcss.com)와 [Livewire](https://laravel-livewire.com) 또는 [Inertia.js](https://inertiajs.com)로 제공하는 아름답고 현대적인 UI로, Laravel Fortify의 인증 서비스를 사용하여 제공하는 강력한 애플리케이션 스타터 키트입니다. Laravel Jetstream은 2 단계 인증, 팀 지원, 브라우저 세션 관리, 프로필 관리 및 API 토큰 인증을 제공하기 위해 [Laravel Sanctum](/docs/{{version}}/sanctum)과의 내장 통합에 대한 선택적인 지원을 포함하고 있습니다. 라라벨의 API 인증 제품은 아래에서 설명합니다.

<a name="laravels-api-authentication-services"></a>
#### 라라벨 API 인증 서비스

라라벨은 API 토큰을 관리하고 API 토큰으로 요청을 인증하는데 도움이 되는 선택 가능한 두 가지 패키지 [Passport](/docs/{{version}}/passport)와 [Sanctum](/docs/{{version}}/sanctum)을 제공합니다. 이러한 라이브러리와 라라벨의 내장 쿠키 기반 인증 라이브러리는 상호 배타적이지 않습니다. 이러한 라이브러리는 주로 API 토큰 인증에 중점을 두고 있으며, 기본적으로 제공하는 인증 서비스는 쿠키 기반의 브라우저 인증에 중점을 둡니다. 많은 애플리케이션이 라라벨의 내장 쿠키 기반 인증 서비스와 라라벨의 API 인증 패키지를 모두 사용합니다.

**Passport**

Passport는 다양한 유형의 토큰을 발급 할 수 있는, 다양한 OAuth2 "grant types"을 제공하는 OAuth2 인증 공급자입니다. 일반적으로 이것은 API 인증을 위한 강력하고 복잡한 패키지입니다. 그러나 대부분의 애플리케이션에는 OAuth2 사양에서 제공하는 복잡한 기능이 필요하지 않으므로, 사용자와 개발자 모두에게 혼란을 줄 수 있습니다. 또한, 개발자는 Passport와 같은 OAuth2 인증 공급자를 사용하여 SPA 애플리케이션 또는 모바일 애플리케이션을 인증하는 방법에 대해 그동안 혼란스러웠습니다.

**Sanctum**

OAuth2의 복잡성과 개발자의 혼란을 방지하기위해, 웹 브라우저에서의 웹 요청과 토큰을 통한 API 요청을 모두 처리 할 수 있는 더 간단하고 간소화된 인증 패키지를 구축하기 시작했습니다. 이 목표는 [Laravel Sanctum](/docs/{{version}}/sanctum)의 출시와 함께 이루어졌습니다. 이 패키지는 API 외에도 자사 웹 UI 또는 라라벨 백엔드 애플리케이션과 단일 페이지 애플리케이션 (SPA)에 연동할 때 또는 모바일 클라이언트와 연동할 때 권장하는 인증 패키지입니다.

Laravel Sanctum은 애플리케이션의 전체 인증 프로세스를 관리 할 수 있는 하이브리드 웹 / API 인증 패키지입니다. 이는 Sanctum 기반 애플리케이션이 요청을 수신할 때 Sanctum이 먼저 요청에 인증된 세션을 참조하는 세션 쿠키가 포함되어 있는지 확인하기 때문에 가능합니다. Sanctum은 앞서 논의한 라라벨의 내장 인증 서비스를 호출하여 이를 수행합니다. 요청이 세션 쿠키를 통해 인증되지 않는 경우 Sanctum은 요청에서 API 토큰을 검사합니다. API 토큰이 있는 경우 Sanctum은 해당 토큰을 사용하여 요청을 인증합니다. 이 프로세스에 대한 자세한 내용은 Sanctum의 ["작동 방식"](/docs/{{version}}/sanctum#how-it-works) 문서를 참조하십시오.

Laravel Sanctum은 [Laravel Jetstream](https://jetstream.laravel.com) 애플리케이션 스타터 키트의 API 패키지로 포함하도록 선택했습니다. 이것은 대부분의 웹 애플리케이션 인증 요구 사항에 가장 적합하다고 생각하기 때문입니다.

<a name="summary-choosing-your-stack"></a>
#### 요약 및 스택 선택

요약하자면, 브라우저를 사용하여 라라벨 일체형으로 만들어진 애플리케이션에 접근할 경우, 애플리케이션은 라라벨의 내장 인증 서비스를 사용합니다.

그리고, 다른 곳에서 사용할 API를 만들어야 한다면 [Passport](/docs/{{version}}/passport) 또는 [Sanctum](/docs/{{version}}/sanctum) 중에서 골라 API 토큰 인증을 사용하면 됩니다. 일반적으로 Sanctum은 "범위"또는 "기능"을 지원하며 API 인증, SPA 인증 및 모바일 인증을 위한 간단하고 완전한 솔루션이기 때문에 가능하면 Sanctum을 사용하세요.

라라벨이 백엔드로 이루어진 단일 페이지 애플리케이션 (SPA)을 만든다면 [Laravel Sanctum](/docs/{{version}}/sanctum)을 사용하면 됩니다. Sanctum을 사용할 때는 [자신의 백엔드 인증 라우트를 수동으로 구현](#authenticating-users) 하거나 [Laravel Fortify](/docs/{{version}}/fortify)를 활용하여 사용자 등록, 비밀번호 재설정, 이메일 확인 등과 같은 기능의 라우트 및 컨트롤러를 제공하는 헤드리스 인증 백엔드 서비스를 제공해야합니다. 

애플리케이션에 OAuth2 사양에서 제공하는 모든 기능이 무조건 필요한 경우 Passport를 선택하면 됩니다.

그리고 빠르게 시작하고 싶으시다면 [Laravel Jetstream](https://jetstream.laravel.com)을 추천합니다. 라라벨의 기본 인증 서비스 및 라라벨 Sanctum 스택을 이미 사용하고 있는 새로운 라라벨 애플리케이션을 빠르게 시작할 수 있습니다.

<a name="authentication-quickstart"></a>
## 빠르게 인증 살펴보기

> {note} 이 부분에서는 개발을 빠르게 시작하는 데 도움이 되는 UI 스캐폴딩이 포함 된 [라라벨 애플리케이션 스타터 키트](/docs/{{version}}/starter-kits)를 이용한 사용자 인증에 대해 살펴봤습니다. 라라벨의 인증 시스템을 직접 만들고 싶다면 [사용자 인증을 직접 구현 하기 위한](#authenticating-users) 문서를 확인해보세요.

<a name="install-a-starter-kit"></a>
### 스타터 키트 설치

먼저 [라라벨 애플리케이션 스타터 키트를 설치](/docs/{{version}}/starter-kits)해야합니다. 현재 스타터 키트 인 Laravel Breeze 및 Laravel Jetstream은 새로운 라라벨 애플리케이션을 만들 때, 깔끔하게 사용자 인증을 통합해서 만들수 있게 도와줍니다.

Laravel Breeze는 로그인, 사용자 등록, 비밀번호 재설정, 이메일 확인 및 비밀번호 확인을 포함하여 라라벨의 모든 인증 기능을 최소한으로 간단하게 구현해줍니다. Laravel Breeze의 뷰 레이어는 [Tailwind CSS](https://tailwindcss.com) 스타일의 간단한 [Blade templates](/docs/{{version}}/blade)로 만들어집니다. 또한 Breeze는 Vue 또는 React를 사용하는 [Inertia](https://inertiajs.com) 기반 스캐폴딩 옵션도 제공합니다.

[Laravel Jetstream](https://jetstream.laravel.com)은 [Livewire](https://laravel-livewire.com) 또는 [Inertia.js 및 Vue](https://inertiajs.com)를 사용하여 애플리케이션의 스캐폴딩을 지원하는 강력한 애플리케이션 스타터 키트입니다. 또한 Jetstream은 2 단계 인증, 팀, 프로필 관리, 브라우저 세션 관리, [Laravel Sanctum](/docs/{{version}}/sanctum)을 통한 API 지원, 계정 삭제 등에 대해 선택적으로 지원할 수 있는 기능을 제공합니다.

<a name="retrieving-the-authenticated-user"></a>
### 승인된 사용자 조회하기

인증 스타터 키트를 설치하고 사용자가 애플리케이션에 등록하고 인증 할 수 있도록 만든 후, 현재 인증 된 사용자 정보를 가져와야하는 경우가 많습니다. 사용자의 요청을 처리하는 동안 `Auth` 파사드의 `user` 메소드를 통해 인증 된 사용자정보를 가져올 수 있습니다.

    use Illuminate\Support\Facades\Auth;

    // Retrieve the currently authenticated user...
    $user = Auth::user();

    // Retrieve the currently authenticated user's ID...
    $id = Auth::id();

또는 사용자가 인증되면 `Illuminate\Http\Request` 인스턴스를 통해 인증 된 사용자 정보를 가져올 수 있습니다. 컨트롤러 메서드에 타입힌트를 등록한 클래스는 자동으로 주입됩니다. `Illuminate\Http\Request` 객체의 타입 힌트를 지정하면 요청의 `user` 메서드를 통해 애플리케이션의 모든 컨트롤러 메서드에서 인증 된 사용자정보를 쉽게 가져올 수 있습니다.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;

    class FlightController extends Controller
    {
        /**
         * Update the flight information for an existing flight.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return \Illuminate\Http\Response
         */
        public function update(Request $request)
        {
            // $request->user()
        }
    }

<a name="determining-if-the-current-user-is-authenticated"></a>
#### 현재 사용자의 승인 여부 결정하기

들어오는 HTTP 요청에 사용자가 인증되어 있는지 확인해보려면 `Auth` 파사드에서 `check` 메소드를 사용하면 됩니다. 이 메서드는 사용자가 인증 된 경우 `true`를 반환합니다.

    use Illuminate\Support\Facades\Auth;

    if (Auth::check()) {
        // The user is logged in...
    }

> {tip} `check` 메서드를 사용하여 사용자가 인증되어 있는지 확인할 수 있지만, 일반적으로 사용자가 특정 라우트 컨트롤러에 접근 할 수 있도록 허용할 때, 미들웨어를 사용하여 사용자가 인증되었는지 확인합니다. 이에 대해 자세히 알아 보려면 [라우트 보호하기](/docs/{{version}}/authentication#protecting-routes)에 대한 문서를 확인하십시오.

<a name="protecting-routes"></a>
### 라우트 보호하기

[라우트 미들웨어](/docs/{{version}}/middleware)를 이용하여 지정한 라우트에 인증된 사용자에게만 접근할 수 있도록 만들 수도 있습니다. 라라벨은 `Illuminate\Auth\Middleware\Authenticate` 클래스를 참조하는 `auth` 미들웨어를 제공하고 있습니다. 이 미들웨어는 이미 애플리케이션의 HTTP 커널에 등록되어 있으므로 미들웨어를 라우트 정의에 추가하기만 만하면됩니다.

    Route::get('/flights', function () {
        // Only authenticated users may access this route...
    })->middleware('auth');

<a name="redirecting-unauthenticated-users"></a>
#### 인증되지 않는 사용자 리다이렉팅하기

`auth` 미들웨어는 사용자가 인증되지 않았다면, `login` [이라는 이름이 지정된 라우트](/docs/{{version}}/routing#named-routes)로 사용자를 돌려보냅니다. `app/Http/Middleware/Authenticate.php` 파일의 `redirectTo` 함수를 수정해서 이 동작을 바꿀 수 있습니다.

    /**
     * Get the path the user should be redirected to.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return string
     */
    protected function redirectTo($request)
    {
        return route('login');
    }

<a name="specifying-a-guard"></a>
#### Guard 지정하기

`auth` 미들웨어를 라우트에 추가할 때, 어떤 "가드"를 인증에 사용할지 정할 수도 있습니다. 지정한 가드는 `auth.php` 설정 파일의 `guards` 배열에 등록된 키 중에 한개여야 합니다.


    Route::get('/flights', function () {
        // Only authenticated users may access this route...
    })->middleware('auth:admin');

<a name="login-throttling"></a>
### 로그인 횟수 제한

Laravel Breeze나 Laravel Jetstream 같은 [스타터 키트](/docs/{{version}}/starter-kits)를 사용하는 경우, 로그인 시도에 횟수 제한이 자동으로 적용됩니다. 기본적으로 사용자는 몇 번의 시도 후에도 올바른 인증 정보를 제공하지 않으면, 1분 동안 로그인할 수 없습니다. 이 제한은 사용자의 사용자 이름 / 이메일 및 IP 주소별로 각각 동작합니다.

> {tip} 애플리케이션 내의 다른 경로에도 횟수를 제한하려면 [횟수 제한 문서](/docs/{{version}}/routing#rate-limiting)를 확인하세요.

<a name="authenticating-users"></a>
## 수동으로 사용자 인증하기

라라벨 [스타터 키트](/docs/{{version}}/starter-kits)에 포함된 인증 스캐폴딩을 꼭 사용할 필요는 없습니다. 이 스캐폴딩을 사용하지 않기로 결정했다면, 라라벨 인증 클래스를 사용하여 직접 사용자 인증을 처리해야 합니다. 걱정하지 마세요. 간단합니다!

우리는 라라벨의 인증 서비스를 `Auth` [파사드](/docs/{{version}}/facades)를 통해 접근할 것이기 때문에, 클래스의 가장 상단 부분에 `Auth` 파사드를 사용하도록 선언합니다. 다음은 `attempt` 메소드를 확인해 보겠습니다. `attempt` 메소드는 일반적으로 애플리케이션의 "로그인" 폼에서 인증 시도를 처리하는 데 사용됩니다. 인증에 성공하면 [세션 고정 공격](https://en.wikipedia.org/wiki/Session_fixation) 을 방지하기 위해, 사용자의 [세션](/docs/{{version}}/session)을 다시 생성해야합니다.


    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Auth;

    class LoginController extends Controller
    {
        /**
         * Handle an authentication attempt.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return \Illuminate\Http\Response
         */
        public function authenticate(Request $request)
        {
            $credentials = $request->validate([
                'email' => ['required', 'email'],
                'password' => ['required'],
            ]);

            if (Auth::attempt($credentials)) {
                $request->session()->regenerate();

                return redirect()->intended('dashboard');
            }

            return back()->withErrors([
                'email' => 'The provided credentials do not match our records.',
            ]);
        }
    }

`attempt` 메소드는 키 / 값의 쌍으로 이루어진 배열을 첫번째 인자로 전달 받습니다. 배열의 값들은 데이터베이스 테이블에서 사용자를 찾는데 사용됩니다. 따라서 위의 예제에서는, `email` 컬럼을 통해서 사용자를 찾습니다. 사용자를 찾았다면, 해시 처리되어 데이터베이스에 저장된 패스워드와 메소드로 전달받은 배열의 `password` 값을 비교합니다. 데이터베이스에서 해시 처리된 암호와 비교하기 전에 프레임워크가 값을 자동으로 해시처리 하기 때문에, 입력한 `password` 값을 해시처리하면 안됩니다. 두개의 해시처리된 패스워드가 일치한다면 해당 사용자로 인증된 세션을 새로 발급해줍니다.

라라벨의 인증 서비스는 인증 가드의 "공급자"설정에 따라 데이터베이스에서 사용자를 검색합니다. `config/auth.php` 설정 파일에는 기본적으로 Eloquent 사용자 공급자가 지정되어 있으며, 사용자를 검색 할 때 `App\Models\User` 모델을 사용하도록 설정되어 있습니다. 필요하다면 설정 파일에서 값을 변경하면 됩니다.

`attempt` 메소드는 인증에 성공하면 `true`를, 실패하면 `false`를 반환합니다.

라라벨의 리다이렉터가 제공하는 `intended` 메소드는 사용자가 인증 미들웨어에 의해 잡히기 전, 원래 접근하려고 시도했었던 URL로 사용자를 리다이렉트 시킵니다. 이전 URL로 이동이 불가능한 경우, 대체 할 URI를 메소드에 지정할 수 있습니다.

<a name="specifying-additional-conditions"></a>
#### 추가적인 조건 지정하기

원한다면, 사용자의 이메일 및 비밀번호 외에 추가 쿼리 조건을 인증 쿼리에 추가 할 수도 있습니다. 사용하려면 `attempt` 메소드에 전달 된 배열에 쿼리 조건을 추가하면 됩니다. 예를 들면, 다음과 같이 사용자가 "사용중-active"으로 등록되어 있는지 검사할 수 있습니다.

    if (Auth::attempt(['email' => $email, 'password' => $password, 'active' => 1])) {
        // Authentication was successful...
    }

> {note} 이 예제에서, `email` 은 필수 옵션이 아니라, 그냥 예제에서 사용한 것입니다. 여러분은 데이터베이스 테이블 안에 있는 "username"과 일치하는 어떠한 컬럼 이름을 사용해야 합니다.

<a name="accessing-specific-guard-instances"></a>
#### 지정된 Guard 인스턴스에 엑세스하기

`Auth` 파사드의 `guard` 메소드를 통해 사용자를 인증 할 때, 사용할 가드 인스턴스를 지정할 수 있습니다. 이를 통해 완전히 분리된 모델 또는 사용자 테이블을 사용하여, 애플리케이션의 일부분에 대한 인증을 따로 관리 할 수 있습니다.

`guard` 메소드에 전달되는 guard의 이름은 `auth.php` 설정 파일에 설정된 guard 중 하나와 일치 해야합니다.

    if (Auth::guard('admin')->attempt($credentials)) {
        // ...
    }

<a name="remembering-users"></a>
### 사용자 기억하기

많은 웹 애플리케이션은 로그인 양식에 "내 정보 기억" 체크박스을 제공합니다. 애플리케이션에 "기억하기" 기능을 제공하려면, `attempt` 메서드의 두번째 인자에 boolean 값을 전달하면 됩니다.

이 값이 `true`이면 라라벨은 사용자를 무기한으로 또는 수동으로 로그아웃 할 때까지 인증 된 상태로 유지합니다. `users`테이블에는 'remember me'토큰을 저장하는 데 사용할 `remember_token`열이 있어야 합니다. 새로운 라라벨 애플리케이션에 포함 된 `users` 테이블 마이그레이션에는 기본적으로 해당 컬럼이 포함되어 있습니다.

    use Illuminate\Support\Facades\Auth;

    if (Auth::attempt(['email' => $email, 'password' => $password], $remember)) {
        // The user is being remembered...
    }

<a name="other-authentication-methods"></a>
### 그 외 인증 메소드

<a name="authenticate-a-user-instance"></a>
#### 사용자 인스턴스를 통해서 인증하기

사용자 인스턴스를 현재 로그인 한 사용자로 등록하고 싶다면, 사용자 인스턴스를 `Auth` 파사드의 `login` 메소드에 입력하면 됩니다. 입력한 인스턴스는 `Illuminate\Contracts\Auth\Authenticatable` [contract](/docs/{{version}}/contracts)의 구현체여야 합니다. 라라벨에 포함된 `App\Models\User` 모델은 이미 이 인터페이스를 구현하고 있습니다. 이 인증 메서드는 사용자를 애플리케이션에 등록한 직후와 같이 유효한 사용자 인스턴스가 이미 있는 경우에 유용합니다. (`역자주: 회원가입 직후 해당 유저로 바로 로그인 시켜줄 때를 의미합니다.`)

    use Illuminate\Support\Facades\Auth;

    Auth::login($user);

`login`메소드의 두 번째 인수로 boolean 값을 전달할 수 있습니다. 이 값은 인증 된 세션에 "remember me"기능을 사용할지를 의미합니다. `true`로 설정한다면 세션이 무기한으로 또는 사용자가 애플리케이션에서 수동으로 로그아웃 할 때까지 인증을 유지하게 됩니다.

    Auth::login($user, $remember = true);

필요한 경우 `login` 메서드를 호출하기 전에 인증 가드를 지정할 수 있습니다.

    Auth::guard('admin')->login($user);

<a name="authenticate-a-user-by-id"></a>
#### ID를 통해서 사용자 인증하기

데이터베이스 레코드의 기본 키를 사용하여 사용자를 인증하려면 `loginUsingId` 메소드를 사용하면 됩니다. 이 메서드는 인증하려는 사용자의 기본 키를 입력받습니다.

    Auth::loginUsingId(1);

`loginUsingId`메소드의 두 번째 인수로 boolean 값을 전달할 수 있습니다. 이 값은 인증 된 세션에 "remember me"기능을 사용할지를 의미합니다. `true`로 설정한다면 세션이 무기한으로 또는 사용자가 애플리케이션에서 수동으로 로그아웃 할 때까지 인증을 유지하게 됩니다.

    Auth::loginUsingId(1, $remember = true);

<a name="authenticate-a-user-once"></a>
#### 한번만 사용자로 인증 하기

일회성 요청에 대해 사용자로 인증하기 위해 `once` 메소드를 사용할 수 있습니다. 이 메서드를 사용 할 때는 세션이나 쿠키가 사용되지 않습니다.

    if (Auth::once($credentials)) {
        //
    }

<a name="http-basic-authentication"></a>
## HTTP 기본 인증

[HTTP 기본 인증](https://en.wikipedia.org/wiki/Basic_access_authentication) 은 애플리케이션에 별도의 "login" 페이지 설정없이도 사용자 인증을 할 수 있는 손쉬운 방법을 제공합니다. 이를 위해서는 `auth.basic` [미들웨어](/docs/{{version}}/middleware)를 라우트에 추가하면 됩니다. `auth.basic` 미들웨어는 라라벨에 포함되어 있기 때문에 따로 정의할 필요가 없습니다.

    Route::get('/profile', function () {
        // Only authenticated users may access this route...
    })->middleware('auth.basic');

미들웨어가 라우트에 추가되면 브라우저에서 라우트에 접근할 때, 인증 정보를 입력하라는 메시지가 자동으로 표시됩니다. `auth.basic` 미들웨어는 기본적으로 `users` 데이터베이스 테이블의 `email` 컬럼이 "username"이라고 가정합니다.

<a name="a-note-on-fastcgi"></a>
#### FastCGI에 대한 참고사항

라라벨 애플리케이션을 사용하기 위해 PHP FastCGI 및 Apache를 사용하는 경우 HTTP 기본 인증이 제대로 작동하지 않을 수 있습니다. 이런 문제를 해결하려면, 애플리케이션의 `.htaccess` 파일에 다음과 같이 추가하면 됩니다.

    RewriteCond %{HTTP:Authorization} ^(.+)$
    RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

<a name="stateless-http-basic-authentication"></a>
### 상태를 유지하지 않는 HTTP 기본 인증

세션에서 사용자 식별 쿠키를 설정하지 않고, HTTP 기본 인증을 사용할 수도 있습니다. 이는 주로 HTTP 인증을 사용하여 애플리케이션의 API 요청에 대한 인증을 처리하는 경우에 유용합니다. 이를 위해 `onceBasic` 메소드를 사용하는 [미들웨어를 정의하세요](/docs/{{version}}/middleware). `onceBasic` 메소드에서 응답이 반환되지 않으면 요청이 애플리케이션으로 계속 전달됩니다.

    <?php

    namespace App\Http\Middleware;

    use Illuminate\Support\Facades\Auth;

    class AuthenticateOnceWithBasicAuth
    {
        /**
         * Handle an incoming request.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  \Closure  $next
         * @return mixed
         */
        public function handle($request, $next)
        {
            return Auth::onceBasic() ?: $next($request);
        }

    }

다음으로, [라우트 미들웨어에 등록하고](/docs/{{version}}/middleware#registering-middleware), 이 미들웨어를 라우트에 추가하십시오.

    Route::get('/api/user', function () {
        // Only authenticated users may access this route...
    })->middleware('auth.basic.once');

<a name="logging-out"></a>
## 로그아웃

애플리케이션에서 사용자를 수동으로 로그아웃하려면 `Auth` 파사드에서 제공하는 `logout` 메소드를 사용하면 됩니다. 이렇게하면 후속 요청에서는 인증되지 않도록 사용자 세션에서 인증 정보가 제거됩니다.

`logout` 메소드를 호출하는 것 외에도 사용자의 세션을 무효화하고 [CSRF 토큰](/docs/{{version}}/csrf)을 다시 생성하는 것이 좋습니다. 사용자를 로그아웃 한 후에는 일반적으로 사용자를 애플리케이션의 루트(/)로 이동시킵니다.

    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Auth;

    /**
     * Log the user out of the application.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return \Illuminate\Http\Response
     */
    public function logout(Request $request)
    {
        Auth::logout();

        $request->session()->invalidate();

        $request->session()->regenerateToken();

        return redirect('/');
    }

<a name="invalidating-sessions-on-other-devices"></a>
### 다른 디바이스의 세션 무효화

라라벨은 또한 현재 장치에서 로그인정보를 유지하면서, 다른 장치에서 인증된 된 로그인 정보를 무효화하고 "로그아웃"시키는 방법을 제공합니다. 이 기능은 일반적으로 사용자가 암호를 변경하거나 업데이트 할 때, 현재 장치의 인증을 유지하면서 다른 장치에서 세션을 무효화하려는 경우에 사용됩니다.

시작하기 전에 `Illuminate\Session\Middleware\AuthenticateSession` 미들웨어가 존재하고 `App\Http\Kernel` 클래스의 `web` 미들웨어 그룹에서 주석 처리되어있지는 않은지 확인해야합니다.

    'web' => [
        // ...
        \Illuminate\Session\Middleware\AuthenticateSession::class,
        // ...
    ],

그런 다음 `Auth` 파사드에서 제공하는 `logoutOtherDevices` 메소드를 사용하면 됩니다. 이 메서드를 사용하려면 사용자가 현재 비밀번호를 입력해줘야 합니다. 애플리케이션이 입력 폼을 통해 입력받아서 처리합니다.

    use Illuminate\Support\Facades\Auth;

    Auth::logoutOtherDevices($currentPassword);

`logoutOtherDevices` 메소드가 호출되면 사용자의 다른 세션은 완전히 무효화됩니다. 즉, 이전에 인증 된 모든 가드에서 "로그아웃"됩니다.

<a name="password-confirmation"></a>
## 비밀번호 확인

애플리케이션을 만드는 동안, 어떠한 작업을 수행하기 전, 또는 사용자가 애플리케이션의 중요한 곳으로 이동하기 전에 사용자의 암호를 확인해야 할 필요가 있을 수도 있습니다. 라라벨은 이 과정을 쉽게 만들어주는 내장 미들웨어를 포함하고 있습니다. 이 기능을 구현하려면 두 가지 경로를 정의해야 합니다. 하나는 사용자에게 암호 확인을 요청하는 view를 표시하는 경로이고, 다른 하나는 암호가 유효한지 확인 후, 사용자를 원하는 곳으로 이동하는 경로입니다.

> {tip} 아래 문서는 라라벨의 비밀번호 확인 기능을 직접 연동하는 방법에 대해 설명합니다. 그러나 더 빨리 시작하기위해 [라라벨 애플리케이션 스타터 키트](/docs/{{version}}/starter-kits)가 이 기능들을 지원하고 있습니다!

<a name="password-confirmation-configuration"></a>
### 환경설정

비밀번호를 확인한 후 사용자는 3시간 동안 비밀번호를 다시 확인하지 않아도 됩니다. 그러나 애플리케이션의 `config/auth.php` 설정 파일의 `password_timeout` 값을 변경하여 사용자에게 암호를 다시 묻는 메시지가 표시되기 까지의 시간을 변경할 수 있습니다.

<a name="password-confirmation-routing"></a>
### 라우팅

<a name="the-password-confirmation-form"></a>
#### 비밀번호 확인 양식

먼저 사용자에게 비밀번호 확인을 요청하는 폼의 경로를 지정합니다.

    Route::get('/confirm-password', function () {
        return view('auth.confirm-password');
    })->middleware('auth')->name('password.confirm');

예상 할 수 있듯이, 이 경로에서 반환되는 view에는 `password` 필드가 포함된 form이 있어야 합니다. 또한 사용자가 애플리케이션의 보호된 영역에 들어가기위해 비밀번호를 확인해야 한다는 내용을 view에 자유롭게 작성할 수 있습니다.

<a name="confirming-the-password"></a>
#### 비밀번호 확인

다음으로 "비밀번호 확인" view의 form에서 요청 할 경로를 정의합니다. 이 경로는 비밀번호의 유효성을 검사하고 사용자를 원하는 곳으로 이동시켜주게 됩니다.

    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Hash;
    use Illuminate\Support\Facades\Redirect;

    Route::post('/confirm-password', function (Request $request) {
        if (! Hash::check($request->password, $request->user()->password)) {
            return back()->withErrors([
                'password' => ['The provided password does not match our records.']
            ]);
        }

        $request->session()->passwordConfirmed();

        return redirect()->intended();
    })->middleware(['auth', 'throttle:6,1']);

계속 진행하기 전에 이 경로를 자세히 살펴보겠습니다. 먼저 요청된 `password`필드가 현재 인증된 사용자의 비밀번호와 실제로 일치하는지 확인합니다. 비밀번호가 맞다면 사용자가 비밀번호를 인증해줬다고 라라벨 세션에 등록해줍니다. `passwordConfirmed` 메소드는 사용자가 마지막으로 비밀번호를 인증한 시기를 저장해두기위해, 라라벨이 사용할 수 있는 사용자 세션에 타임스탬프를 저장합니다. 마지막으로 사용자가 원한 목적지로 이동하게 됩니다.

<a name="password-confirmation-protecting-routes"></a>
### 라우트 보호하기

비밀번호 확인이 필요한 작업을 수행하는 모든 경로에 `password.confirm` 미들웨어가 할당 되었는지 확인해야 합니다. 이 미들웨어는 라라벨의 기본적으로 포함되어 있으며 사용자가 암호를 확인한 후 해당 위치로 이동 할 수 있도록, 세션에 사용자가 원했던 경로를 자동으로 저장합니다. 세션에 사용자가 원했던 경로를 저장 한 후, 미들웨어는 사용자를 `password.confirm` [라는 이름이 지정된 경로로](/docs/{{version}}/routing#named-routes) 이동합니다.

    Route::get('/settings', function () {
        // ...
    })->middleware(['password.confirm']);

    Route::post('/settings', function () {
        // ...
    })->middleware(['password.confirm']);

<a name="adding-custom-guards"></a>
## 사용자정의 Guard 추가

여러분은 `Auth` 파사드의 `extend` 메소드를 사용하여 고유한 인증 가드를 정의할 수 있습니다. 이 메소드는 [서비스 프로바이더](/docs/{{version}}/providers) 중 하나의 `extend`에서 호출해야 합니다. 라라벨은 이미 `AuthServiceProvider`를 통해 제공하고 있으므로, 우린 그 프로바이더 안에 코드만 넣으면 됩니다.

    <?php

    namespace App\Providers;

    use App\Services\Auth\JwtGuard;
    use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;
    use Illuminate\Support\Facades\Auth;

    class AuthServiceProvider extends ServiceProvider
    {
        /**
         * Register any application authentication / authorization services.
         *
         * @return void
         */
        public function boot()
        {
            $this->registerPolicies();

            Auth::extend('jwt', function ($app, $name, array $config) {
                // Return an instance of Illuminate\Contracts\Auth\Guard...

                return new JwtGuard(Auth::createUserProvider($config['provider']));
            });
        }
    }

아래의 예제에서 볼 수 있듯이, `extend` 메소드에 전달된 콜백은 `Illuminate\Contracts\Auth\Guard` 구현체를 반환해야 합니다. 이 인터페이스는 사용자 정의 가드를 구현하는데 필요한 몇가지 메소드를 가지고 있습니다. 사용자 정의 가드를 정의하고나면, `guards` 설정에서 정의한 가드를 사용할 수 있습니다.

    'guards' => [
        'api' => [
            'driver' => 'jwt',
            'provider' => 'users',
        ],
    ],

<a name="closure-request-guards"></a>
### 클로저 형태의 Request Guards

인증 시스템을 기반으로한 커스텀 HTTP request-요청을 구현하는 가장 간단한 방법은 `Auth::viaRequest` 메소드를 사용하는 것 입니다. 이 메소드는 하나의 클로저를 사용하여 빠르기 인증을 정의할 수 있게 해줍니다.

시작하려면, `AuthServiceProvider`의 `boot` 메소드에서 `Auth::viaRequest` 메소드를 호출하면 됩니다. `viaRequest` 메소드는 인증 드라이버 이름을 첫번째 인자로 전달 받습니다. 이 이름은 사용자 지정 가드를 표현하는 문자열이 될 수도 있습니다. 메소드에 전달되는 두번째 인자는 유입되는 HTTP request-요청을 전달받아 사용자 인스턴스를 반환하는 클로저여야하며, 인증에 실패한다면, `null` 을 반환해야 합니다.

    use App\Models\User;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Auth;

    /**
     * Register any application authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();

        Auth::viaRequest('custom-token', function (Request $request) {
            return User::where('token', $request->token)->first();
        });
    }

사용자 정의 인증 드라이버를 만들었다면, `auth.php` 설정 파일의 `guards` 설정 안에서 드라이버로 사용할 수 있습니다.

    'guards' => [
        'api' => [
            'driver' => 'custom-token',
        ],
    ],

<a name="adding-custom-user-providers"></a>
## 사용자 정의 User 프로바이더 추가하기

사용자들을 저장하는 데 전통적인 관계형 데이터베이스를 사용하지 않는다면, 라라벨을 확장해 자체적인 User 프로바이더를 만들어야 합니다. 사용자 정의 User 프로바이더를 정의하기 위해 `Auth` 파사드의 `provider` 메소드를 사용할 것입니다. User 프로바이더 resolver는 `Illuminate\Contracts\Auth\UserProvider` 구현체를 반환해야합니다.

    <?php

    namespace App\Providers;

    use App\Extensions\MongoUserProvider;
    use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;
    use Illuminate\Support\Facades\Auth;

    class AuthServiceProvider extends ServiceProvider
    {
        /**
         * Register any application authentication / authorization services.
         *
         * @return void
         */
        public function boot()
        {
            $this->registerPolicies();

            Auth::provider('mongo', function ($app, array $config) {
                // Return an instance of Illuminate\Contracts\Auth\UserProvider...

                return new MongoUserProvider($app->make('mongo.connection'));
            });
        }
    }

`provider` 메소드로 프로바이더를 등록한 뒤, `auth.php` 설정 파일에서 새 User 프로바이더로 변경할 수 있습니다. 먼저 새로운 드라이버를 사용하는 `provider`를 정의합시다.

    'providers' => [
        'users' => [
            'driver' => 'mongo',
        ],
    ],

마지막으로, `guards` 설정 에서 이 프로바이더를 사용할 수 있습니다.

    'guards' => [
        'web' => [
            'driver' => 'session',
            'provider' => 'users',
        ],
    ],

<a name="the-user-provider-contract"></a>
### 사용자 프로바이더 Contract

`Illuminate\Contracts\Auth\UserProvider`의 구현체는 MySQL, MongoDB 등의 영구 스토리지 시스템에서 `Illuminate\Contracts\Auth\Authenticatable`의 구현체를 불러오는 것만 담당합니다. 이 두 인터페이스는 사용자 데이터가 어떻게 저장되었는지 또는 이를 위해 어떤 클래스가 사용되고 있는지 여부에 상관없이 라라벨 인증 메커니즘이 작동하도록 해줍니다.

`Illuminate\Contracts\Auth\UserProvider` contract를 살펴보겠습니다.

    <?php

    namespace Illuminate\Contracts\Auth;

    interface UserProvider
    {
        public function retrieveById($identifier);
        public function retrieveByToken($identifier, $token);
        public function updateRememberToken(Authenticatable $user, $token);
        public function retrieveByCredentials(array $credentials);
        public function validateCredentials(Authenticatable $user, array $credentials);
    }

`retrieveById` 함수는 일반적으로 MySQL 데이터베이스의 자동 증가 ID와 같은 사용자를 대표하는 키를 전달 받습니다. ID에 상응하는 `Authenticatable` 구현체를 조회한 후 반환해야 합니다.

`retrieveByToken`함수는 일반적으로 `remember_token`과 같은 데이터베이스 열에 저장되는 고유한 `$identifier`와 "remember me" `$token`을 사용해 사용자를 조회합니다. 이전에 설명한 메소드와 마찬가지로 이 메소드도 토큰 값이 일치한 `Authenticatable` 구현체를 반환합니다.

`updateRememberToken` 메소드는 `user` 인스턴스의 `remember_token`을 새로운 `token`으로 업데이트합니다. "remember me" 인증 시도가 성공하거나 사용자가 로그아웃 할 때 사용자에게 새 토큰이 할당됩니다.

`retrieveByCredentials` 메소드는 애플리케이션에 로그인하려고 시도할 때 `Auth::attempt` 메소드로 전달되는 인증 정보 배열을 전달 받습니다. 그러면 메소드는 그 인증 정보에 맞는 사용자들을 기반에 있는 영속적인 저장소(persistent storage)에 "질의"해야 합니다. 일반적으로 이 메소드는 `$credentials['username']`와 일치하는 "username"를 가진 사용자를 조회하는 "where" 쿼리를 실행할 것입니다. 그 뒤에 메소드는 `Authenticatable`의 구현체를 반환해야 합니다. **이 메소드는 패스워드 확인이나 인증을 시도해서는 안됩니다.**

`validateCredentials` 메소드는 주어진 `$user`를 `$credentials`와 비교하여 사용자를 인증해야합니다. 예를 들어, 이 메소드는 일반적으로 `Hash::check` 메소드를 사용하여 `$user->getAuthPassword()`값을 `$credentials['password']`값과 비교합니다. 이 메소드는 비밀번호가 유효한지 여부를 나타내는 `true` 혹은 `false`를 반환해야합니다.

<a name="the-authenticatable-contract"></a>
### Authenticatable Contract 살펴보기

`UserProvider`의 각 메소드에 대해 알아보았으니 이제 `Authenticatable` contract를 살펴 보도록 하겠습니다. User 프로바이더는 `retrieveById`, `retrieveByToken`, 그리고 `retrieveByCredentials` 메소드에서 이 인터페이스의 구현체를 반환해야합니다.

    <?php

    namespace Illuminate\Contracts\Auth;

    interface Authenticatable
    {
        public function getAuthIdentifierName();
        public function getAuthIdentifier();
        public function getAuthPassword();
        public function getRememberToken();
        public function setRememberToken($value);
        public function getRememberTokenName();
    }

이 인터페이스는 간단합니다. `getAuthIdentifierName` 메소드는 사용자의 "primary key" 필드의 이름을 반환해야하고 `getAuthIdentifier` 메소드는 사용자의 "primary key"를 반환해야합니다. MySQL 백엔드를 사용하는 경우 이는 사용자 레코드에 할당 된 auto-incrementing primary key일 수 있습니다. `getAuthPassword` 메소드는 사용자의 해시 된 비밀번호를 반환해야합니다.

이 인터페이스를 사용하면 사용중인 ORM 또는 스토리지 추상화 계층과 상관없이, 인증 시스템을 "사용자"클래스와 함께 동작시킬 수 있습니다. 기본적으로 라라벨은 이 인터페이스를 구현한 `App\Models\User` 클래스가 `app/Models` 디렉토리에 포함되어 있습니다.

<a name="events"></a>
## 이벤트

라라벨은 인증 과정 중에 다양한 [이벤트](/docs/{{version}}/events)들을 전달합니다. 다음과 같이 `EventServiceProvider`에서 이 이벤트들을 위한 리스너를 추가할 수 있습니다.

    /**
     * The event listener mappings for the application.
     *
     * @var array
     */
    protected $listen = [
        'Illuminate\Auth\Events\Registered' => [
            'App\Listeners\LogRegisteredUser',
        ],

        'Illuminate\Auth\Events\Attempting' => [
            'App\Listeners\LogAuthenticationAttempt',
        ],

        'Illuminate\Auth\Events\Authenticated' => [
            'App\Listeners\LogAuthenticated',
        ],

        'Illuminate\Auth\Events\Login' => [
            'App\Listeners\LogSuccessfulLogin',
        ],

        'Illuminate\Auth\Events\Failed' => [
            'App\Listeners\LogFailedLogin',
        ],

        'Illuminate\Auth\Events\Validated' => [
            'App\Listeners\LogValidated',
        ],

        'Illuminate\Auth\Events\Verified' => [
            'App\Listeners\LogVerified',
        ],

        'Illuminate\Auth\Events\Logout' => [
            'App\Listeners\LogSuccessfulLogout',
        ],

        'Illuminate\Auth\Events\CurrentDeviceLogout' => [
            'App\Listeners\LogCurrentDeviceLogout',
        ],

        'Illuminate\Auth\Events\OtherDeviceLogout' => [
            'App\Listeners\LogOtherDeviceLogout',
        ],

        'Illuminate\Auth\Events\Lockout' => [
            'App\Listeners\LogLockout',
        ],

        'Illuminate\Auth\Events\PasswordReset' => [
            'App\Listeners\LogPasswordReset',
        ],
    ];
