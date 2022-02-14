# Authentication
# 인증

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [Starter Kits](#starter-kits)
    - [스타터 키트](#starter-kits)
    - [Database Considerations](#introduction-database-considerations)
    - [데이터베이스 유의사항](#introduction-database-considerations)
    - [Ecosystem Overview](#ecosystem-overview)
    - [생태계 개요](#ecosystem-overview)
- [Authentication Quickstart](#authentication-quickstart)
- [빠르게 인증 살펴보기](#authentication-quickstart)
    - [Install A Starter Kit](#install-a-starter-kit)
    - [### 스타터 키트 설치](#install-a-starter-kit)
    - [Retrieving The Authenticated User](#retrieving-the-authenticated-user)
    - [인증된 사용자 조회하기](#retrieving-the-authenticated-user)
    - [Protecting Routes](#protecting-routes)
    - [라우트 보호하기](#protecting-routes)
    - [Login Throttling](#login-throttling)
    - [로그인 횟수 제한](#login-throttling)
- [Manually Authenticating Users](#authenticating-users)
- [수동으로 사용자 인증하기](#authenticating-users)
    - [Remembering Users](#remembering-users)
    - [사용자 기억하기](#remembering-users)
    - [Other Authentication Methods](#other-authentication-methods)
    - [그외 인증 메소드](#other-authentication-methods)
- [HTTP Basic Authentication](#http-basic-authentication)
- [HTTP 기본 인증](#http-basic-authentication)
    - [Stateless HTTP Basic Authentication](#stateless-http-basic-authentication)
    - [상태를 유지하지 않는 HTTP 기본 인증](#stateless-http-basic-authentication)
- [Logging Out](#logging-out)
- [로그아웃](#logging-out)
    - [Invalidating Sessions On Other Devices](#invalidating-sessions-on-other-devices)
    - [다른 디바이스의 세션 무효화](#invalidating-sessions-on-other-devices)
- [Password Confirmation](#password-confirmation)
- [비밀번호 확인](#password-confirmation)
    - [Configuration](#password-confirmation-configuration)
    - [설정하기](#password-confirmation-configuration)
    - [Routing](#password-confirmation-routing)
    - [라우팅](#password-confirmation-routing)
    - [Protecting Routes](#password-confirmation-protecting-routes)
    - [라우트 보호하기](#password-confirmation-protecting-routes)
- [Adding Custom Guards](#adding-custom-guards)
- [사용자 정의 Guards 추가하기](#adding-custom-guards)
    - [Closure Request Guards](#closure-request-guards)
    - [클로저 형태의 Request Guards](#closure-request-guards)
- [Adding Custom User Providers](#adding-custom-user-providers)
- [사용자 정의 User 프로바이더 추가하기](#adding-custom-user-providers)
    - [The User Provider Contract](#the-user-provider-contract)
    - [사용자 프로바이더 Contract](#the-user-provider-contract)
    - [The Authenticatable Contract](#the-authenticatable-contract)
    - [인증가능 Contract](#the-authenticatable-contract)
- [Social Authentication](/docs/{{version}}/socialite)
- [소셜 인증](/docs/{{version}}/socialite)
- [Events](#events)
- [이벤트](#events)

<a name="introduction"></a>
## Introduction
## 시작하기

Many web applications provide a way for their users to authenticate with the application and "login". Implementing this feature in web applications can be a complex and potentially risky endeavor. For this reason, Laravel strives to give you the tools you need to implement authentication quickly, securely, and easily.

많은 웹 애플리케이션은 사용자가 애플리케이션으로 인증하고 "로그인"할 수있는 방법을 제공합니다. 웹 애플리케이션에서 이 기능을 구현하는 것은 복잡하고 잠재적으로 위험한 작업이 될 수 있습니다. 이러한 이유로 라라벨은 인증을 빠르고 안전하며 쉽게 구현하는 데 필요한 도구를 제공하기 위해 노력하고 있습니다.

At its core, Laravel's authentication facilities are made up of "guards" and "providers". Guards define how users are authenticated for each request. For example, Laravel ships with a `session` guard which maintains state using session storage and cookies.

내부 시스템에서, 라라벨의 인증 기능은 "가드" 와 "프로바이더"로 구성되어 있습니다. 가드는 사용자가 매 요청-request마다 어떻게 인증되는지 정의합니다. 예를 들어 라라벨은 세션 스토리지와 쿠키를 사용하여 상태를 유지하는 `session` 가드와, 각 요청-request와 함께 전달되는 "API 토큰"을 사용하여 사용자를 인증하는 `token` 가드를 제공합니다.

Providers define how users are retrieved from your persistent storage. Laravel ships with support for retrieving users using [Eloquent](/docs/{{version}}/eloquent) and the database query builder. However, you are free to define additional providers as needed for your application.

프로바이더는 저장소에서 사용자를 어떻게 조회 할 수 있는지 정의합니다. 라라벨은 [Eloquent](/docs/{{version}}/eloquent)와 데이터베이스 쿼리 빌더를 사용하여 사용자를 찾을 수 있도록 지원합니다. 또한, 애플리케이션에서 필요로 하는 추가적인 프로바이더를 자유롭게 정의할 수 있습니다.

Your application's authentication configuration file is located at `config/auth.php`. This file contains several well documented options for tweaking the behavior of Laravel's authentication services.

애플리케이션의 인증 설정은 `config/auth.php` 파일에서 할 수 있습니다. 이 파일에는 라라벨 인증 서비스의 동작을 조정하기위한 몇 가지 잘 문서화 된 옵션이 포함되어 있습니다.

> {tip} Guards and providers should not be confused with "roles" and "permissions". To learn more about authorizing user actions via permissions, please refer to the [authorization](/docs/{{version}}/authorization) documentation.

> {tip} 가드와 프로바이더의 "역할"및 "권한"을 혼동해서는 안됩니다. 권한을 통해 사용자의 행동을 승인하기위한 자세한 내용은 [authorization](/docs/{{version}}/authorization) 문서를 참조하세요.

<a name="starter-kits"></a>
### Starter Kits
### 스타터 키트

Want to get started fast? Install a [Laravel application starter kit](/docs/{{version}}/starter-kits) in a fresh Laravel application. After migrating your database, navigate your browser to `/register` or any other URL that is assigned to your application. The starter kits will take care of scaffolding your entire authentication system!

빨리 시작하고 싶으세요? 새로운 라라벨 애플리케이션에 [라라벨 애플리케이션 스타터 키트](/docs/{{version}}/starter-kits)를 설치합니다. 데이터베이스를 마이그레이션 한 후 브라우저를 탐색하여 `/register` 또는 애플리케이션에 할당 된 다른 URL을 들어가보세요. 스타터 키트는 전체 인증 시스템을 자동으로 만들어줍니다!

**Even if you choose not to use a starter kit in your final Laravel application, installing the [Laravel Breeze](/docs/{{version}}/starter-kits#laravel-breeze) starter kit can be a wonderful opportunity to learn how to implement all of Laravel's authentication functionality in an actual Laravel project.** Since Laravel Breeze creates authentication controllers, routes, and views for you, you can examine the code within these files to learn how Laravel's authentication features may be implemented.

**최종적으로 라라벨 애플리케이션에서 스타터 키트를 사용하지 않기로 선택한 경우에도 [라라벨 Breeze](/docs/{{version}}/starter-kits#laravel-breeze) 스타터 키트를 설치하면, 실제 라라벨 프로젝트의 인증 기능을 구현하는 방법을 모두 배울 수 있습니다.** 라라벨 Breeze는 인증 컨트롤러, 라우트 및 뷰를 생성하므로, 이 파일들의 코드를 통해 라라벨의 인증 기능을 구현하는 방법을 배울 수 있습니다.

<a name="introduction-database-considerations"></a>
### Database Considerations
### 데이터베이스 유의사항

By default, Laravel includes an `App\Models\User` [Eloquent model](/docs/{{version}}/eloquent) in your `app/Models` directory. This model may be used with the default Eloquent authentication driver. If your application is not using Eloquent, you may use the `database` authentication provider which uses the Laravel query builder.

기본적으로 라라벨은 `app/Models` 디렉토리에 `App\Models\User` [Eloquent model](/docs/{{version}}/eloquent)를 포함합니다. 이 모델은 기본 Eloquent 인증 드라이버와 함께 사용할 수 있습니다. 애플리케이션이 Eloquent를 사용하지 않는다면, 라라벨 쿼리 빌더를 사용하는 `database` 인증 프로바이더를 사용할 수 있습니다.

When building the database schema for the `App\Models\User` model, make sure the password column is at least 60 characters in length. Of course, the `users` table migration that is included in new Laravel applications already creates a column that exceeds this length.

`App\Models\User` 모델에 대한 데이터베이스 스키마를 생성 할 때 비밀번호 열의 길이가 60자 이상인지 확인하십시오. 물론 새로운 라라벨 애플리케이션에 포함 된 `users` 테이블 마이그레이션은 이미 이 길이를 초과하는 열을 생성합니다.

Also, you should verify that your `users` (or equivalent) table contains a nullable, string `remember_token` column of 100 characters. This column will be used to store a token for users that select the "remember me" option when logging into your application. Again, the default `users` table migration that is included in new Laravel applications already contains this column.

또한 `users`(또는 이에 상응하는) 테이블에 100 자의 nullable 문자열 `remember_token`열이 포함되어 있는지 확인해야합니다. 이 열은 애플리케이션에 로그인 할 때 "remember me"옵션을 선택한 사용자의 토큰을 저장하는 데 사용됩니다. 다시 말하지만, 새로운 라라벨 애플리케이션에 포함 된 기본 `users` 테이블 마이그레이션에는 이미 이 열이 포함되어 있습니다.

<a name="ecosystem-overview"></a>
### Ecosystem Overview
### 생태계 개요

Laravel offers several packages related to authentication. Before continuing, we'll review the general authentication ecosystem in Laravel and discuss each package's intended purpose.

라라벨은 인증과 관련된 여러 패키지를 제공합니다. 계속하기 전에 라라벨의 일반적인 인증 생태계를 검토하고 각 패키지의 의도된 목적에 대해 설명할 것입니다.

First, consider how authentication works. When using a web browser, a user will provide their username and password via a login form. If these credentials are correct, the application will store information about the authenticated user in the user's [session](/docs/{{version}}/session). A cookie issued to the browser contains the session ID so that subsequent requests to the application can associate the user with the correct session. After the session cookie is received, the application will retrieve the session data based on the session ID, note that the authentication information has been stored in the session, and will consider the user as "authenticated".

먼저 인증 작동 방식을 살펴보겠습니다. 웹 브라우저를 사용할 때 사용자는 로그인 form을 통해 사용자 이름과 비밀번호를 입력합니다. 이 인증정보가 맞다면 애플리케이션은 인증된 사용자에 대한 정보를 사용자의 [세션](/docs/{{version}}/session)에 저장합니다. 브라우저에 생성된 쿠키에는 이후 요청들부터 라라벨에서 사용자를 올바른 세션과 연결할 수 있도록 세션 ID가 포함됩니다. 세션 쿠키가 수신되면 라라벨에서 세션 ID를 기반으로 세션 데이터를 검색해서, 인증 정보가 세션에 저장되어있으면 사용자를 "인증된"것으로 간주합니다.

When a remote service needs to authenticate to access an API, cookies are not typically used for authentication because there is no web browser. Instead, the remote service sends an API token to the API on each request. The application may validate the incoming token against a table of valid API tokens and "authenticate" the request as being performed by the user associated with that API token.

원격 서비스가 API에 액세스하기 위해서 인증해야하는 경우, 웹 브라우저가 없기 때문에 일반적으로 쿠키가 인증에 사용되지 않습니다. 대신 원격 서비스는 각 요청마다 API 토큰을 API로 보냅니다. 애플리케이션은 유효한 API 토큰 테이블에서 수신된 토큰의 유효성을 검사하고, 해당 API 토큰과 관련된 사용자의 요청을 "인증"하고 수행 할 수 있습니다.

<a name="laravels-built-in-browser-authentication-services"></a>
#### Laravel's Built-in Browser Authentication Services
#### 라라벨의 내장 브라우져 인증 서비스

Laravel includes built-in authentication and session services which are typically accessed via the `Auth` and `Session` facades. These features provide cookie based authentication for requests that are initiated from web browsers. They provide methods that allow you to verify a user's credentials and authenticate the user. In addition, these services will automatically store the proper authentication data in the user's session and issue the user's session cookie. A discussion of how to use these services is contained within this documentation.

라라벨에는 일반적으로 `Auth` 및 `Session` 파사드를 통해 접근할 수 있는 내장 인증 서비스 및 세션 서비스가 포함되어 있습니다. 이러한 기능은 웹 브라우저의 요청에 대해 쿠키 기반 인증을 제공합니다. 사용자 인증정보를 확인하고 사용자를 인증 할 수 있는 방법을 제공합니다. 또한 이러한 서비스는 사용자 세션에 적절한 인증 데이터를 자동으로 저장하고 사용자 세션 쿠키를 생성합니다. 이 문서에는 이러한 서비스를 사용하는 방법에 대한 설명이 포함되어 있습니다.

**Application Starter Kits**
**애플리케이션 스타터 키트**

As discussed in this documentation, you can interact with these authentication services manually to build your application's own authentication layer. However, to help you get started more quickly, we have released [free packages](/docs/{{version}}/starter-kits) that provide robust, modern scaffolding of the entire authentication layer. These packages are [Laravel Breeze](/docs/{{version}}/starter-kits#laravel-breeze), [Laravel Jetstream](/docs/{{version}}/starter-kits#laravel-jetstream), and [Laravel Fortify](/docs/{{version}}/fortify).

위에서 설명한대로, 이러한 인증 서비스와 상호 작용하는 애플리케이션의 고유한 인증 계층을 직접 만들 수 있습니다. 하지만 더 빨리 시작할 수 있도록 전체 인증 계층에 대한 강력하고 현대적인 스캐폴딩을 제공하는 [무료 패키지](/docs/{{version}}/starter-kits)를 출시했습니다. 이러한 패키지는 [Laravel Breeze](/docs/{{version}}/starter-kits#laravel-breeze), [Laravel Jetstream](/docs/{{version}}/starter-kits#laravel-jetstream) 및 [Laravel Fortify](/docs/{{version}}/fortify)입니다.

_Laravel Breeze_ is a simple, minimal implementation of all of Laravel's authentication features, including login, registration, password reset, email verification, and password confirmation. Laravel Breeze's view layer is comprised of simple [Blade templates](/docs/{{version}}/blade) styled with [Tailwind CSS](https://tailwindcss.com). To get started, check out the documentation on Laravel's [application starter kits](/docs/{{version}}/starter-kits).

_Laravel Breeze_ 는 로그인, 등록, 비밀번호 재설정, 이메일 확인, 비밀번호 확인을 포함한 라라벨의 모든 인증 기능을 최소한으로 구현 한 것입니다. Laravel Breeze의 뷰 레이어는 [Tailwind CSS](https://tailwindcss.com) 스타일과 함께 간단한 [Blade templates](/docs/{{version}}/blade)으로 구성됩니다. 시작하려면 라라벨의 [application starter kits](/docs/{{version}}/starter-kits) 문서를 확인하세요.

_Laravel Fortify_ is a headless authentication backend for Laravel that implements many of the features found in this documentation, including cookie-based authentication as well as other features such as two-factor authentication and email verification. Fortify provides the authentication backend for Laravel Jetstream or may be used independently in combination with [Laravel Sanctum](/docs/{{version}}/sanctum) to provide authentication for an SPA that needs to authenticate with Laravel.

_Laravel Fortify_ 는 쿠키 기반 인증과 2단계 인증 및 이메일 확인과 같은 기타 기능을 포함하여, 이 문서에 있는 많은 기능을 구현하는 라라벨용 헤드리스 인증 백엔드입니다. Fortify는 Laravel Jetstream에 대한 인증 백엔드를 제공하거나 [Laravel Sanctum](/docs/{{version}}/sanctum)과 함께 독립적으로 사용하여 Laravel에 인증해야하는 SPA에 인증 기능을 제공 할 수 있습니다.

_[Laravel Jetstream](https://jetstream.laravel.com)_ is a robust application starter kit that consumes and exposes Laravel Fortify's authentication services with a beautiful, modern UI powered by [Tailwind CSS](https://tailwindcss.com), [Livewire](https://laravel-livewire.com), and / or [Inertia.js](https://inertiajs.com). Laravel Jetstream includes optional support for two-factor authentication, team support, browser session management, profile management, and built-in integration with [Laravel Sanctum](/docs/{{version}}/sanctum) to offer API token authentication. Laravel's API authentication offerings are discussed below.

_[Laravel Jetstream](https://jetstream.laravel.com)_ 은 [Tailwind CSS](https://tailwindcss.com)와 [Livewire](https://laravel-livewire.com) 또는 [Inertia.js](https://inertiajs.com)로 제공하는 아름답고 현대적인 UI로, Laravel Fortify의 인증 서비스를 사용하여 제공하는 강력한 애플리케이션 스타터 키트입니다. Laravel Jetstream은 2 단계 인증, 팀 지원, 브라우저 세션 관리, 프로필 관리 및 API 토큰 인증을 제공하기 위해 [Laravel Sanctum](/docs/{{version}}/sanctum)과의 내장 통합에 대한 선택적인 지원을 포함하고 있습니다. 라라벨의 API 인증 제품은 아래에서 설명합니다.

<a name="laravels-api-authentication-services"></a>
#### Laravel's API Authentication Services
#### 라라벨 API 인증 서비스

Laravel provides two optional packages to assist you in managing API tokens and authenticating requests made with API tokens: [Passport](/docs/{{version}}/passport) and [Sanctum](/docs/{{version}}/sanctum). Please note that these libraries and Laravel's built-in cookie based authentication libraries are not mutually exclusive. These libraries primarily focus on API token authentication while the built-in authentication services focus on cookie based browser authentication. Many applications will use both Laravel's built-in cookie based authentication services and one of Laravel's API authentication packages.

라라벨은 API 토큰을 관리하고 API 토큰으로 요청을 인증하는데 도움이 되는 선택 가능한 두 가지 패키지 [Passport](/docs/{{version}}/passport)와 [Sanctum](/docs/{{version}}/sanctum)을 제공합니다. 이러한 라이브러리와 라라벨의 내장 쿠키 기반 인증 라이브러리는 상호 배타적이지 않습니다. 이러한 라이브러리는 주로 API 토큰 인증에 중점을 두고 있으며, 기본적으로 제공하는 인증 서비스는 쿠키 기반의 브라우저 인증에 중점을 둡니다. 많은 애플리케이션이 라라벨의 내장 쿠키 기반 인증 서비스와 라라벨의 API 인증 패키지를 모두 사용합니다.

**Passport**
**Passport**

Passport is an OAuth2 authentication provider, offering a variety of OAuth2 "grant types" which allow you to issue various types of tokens. In general, this is a robust and complex package for API authentication. However, most applications do not require the complex features offered by the OAuth2 spec, which can be confusing for both users and developers. In addition, developers have been historically confused about how to authenticate SPA applications or mobile applications using OAuth2 authentication providers like Passport.

Passport는 다양한 유형의 토큰을 발급 할 수 있는, 다양한 OAuth2 "grant types"을 제공하는 OAuth2 인증 공급자입니다. 일반적으로 이것은 API 인증을 위한 강력하고 복잡한 패키지입니다. 그러나 대부분의 애플리케이션에는 OAuth2 사양에서 제공하는 복잡한 기능이 필요하지 않으므로, 사용자와 개발자 모두에게 혼란을 줄 수 있습니다. 또한, 개발자는 Passport와 같은 OAuth2 인증 공급자를 사용하여 SPA 애플리케이션 또는 모바일 애플리케이션을 인증하는 방법에 대해 그동안 혼란스러웠습니다.

**Sanctum**
**Sanctum**

In response to the complexity of OAuth2 and developer confusion, we set out to build a simpler, more streamlined authentication package that could handle both first-party web requests from a web browser and API requests via tokens. This goal was realized with the release of [Laravel Sanctum](/docs/{{version}}/sanctum), which should be considered the preferred and recommended authentication package for applications that will be offering a first-party web UI in addition to an API, or will be powered by a single-page application (SPA) that exists separately from the backend Laravel application, or applications that offer a mobile client.

OAuth2의 복잡성과 개발자의 혼란을 방지하기위해, 웹 브라우저에서의 웹 요청과 토큰을 통한 API 요청을 모두 처리 할 수 있는 더 간단하고 간소화된 인증 패키지를 구축하기 시작했습니다. 이 목표는 [Laravel Sanctum](/docs/{{version}}/sanctum)의 출시와 함께 이루어졌습니다. 이 패키지는 API 외에도 자사 웹 UI 또는 라라벨 백엔드 애플리케이션과 단일 페이지 애플리케이션 (SPA)에 연동할 때 또는 모바일 클라이언트와 연동할 때 권장하는 인증 패키지입니다.

Laravel Sanctum is a hybrid web / API authentication package that can manage your application's entire authentication process. This is possible because when Sanctum based applications receive a request, Sanctum will first determine if the request includes a session cookie that references an authenticated session. Sanctum accomplishes this by calling Laravel's built-in authentication services which we discussed earlier. If the request is not being authenticated via a session cookie, Sanctum will inspect the request for an API token. If an API token is present, Sanctum will authenticate the request using that token. To learn more about this process, please consult Sanctum's ["how it works"](/docs/{{version}}/sanctum#how-it-works) documentation.

Laravel Sanctum은 애플리케이션의 전체 인증 프로세스를 관리 할 수 있는 하이브리드 웹 / API 인증 패키지입니다. 이는 Sanctum 기반 애플리케이션이 요청을 수신할 때 Sanctum이 먼저 요청에 인증된 세션을 참조하는 세션 쿠키가 포함되어 있는지 확인하기 때문에 가능합니다. Sanctum은 앞서 논의한 라라벨의 내장 인증 서비스를 호출하여 이를 수행합니다. 요청이 세션 쿠키를 통해 인증되지 않는 경우 Sanctum은 요청에서 API 토큰을 검사합니다. API 토큰이 있는 경우 Sanctum은 해당 토큰을 사용하여 요청을 인증합니다. 이 프로세스에 대한 자세한 내용은 Sanctum의 ["작동 방식"](/docs/{{version}}/sanctum#how-it-works) 문서를 참조하십시오.

Laravel Sanctum is the API package we have chosen to include with the [Laravel Jetstream](https://jetstream.laravel.com) application starter kit because we believe it is the best fit for the majority of web application's authentication needs.

Laravel Sanctum은 [Laravel Jetstream](https://jetstream.laravel.com) 애플리케이션 스타터 키트의 API 패키지로 포함하도록 선택했습니다. 이것은 대부분의 웹 애플리케이션 인증 요구 사항에 가장 적합하다고 생각하기 때문입니다.

<a name="summary-choosing-your-stack"></a>
#### Summary & Choosing Your Stack
#### 요약 및 스택 선택

In summary, if your application will be accessed using a browser and you are building a monolithic Laravel application, your application will use Laravel's built-in authentication services.

요약하자면, 브라우저를 사용하여 라라벨 일체형으로 만들어진 애플리케이션에 접근할 경우, 애플리케이션은 라라벨의 내장 인증 서비스를 사용합니다.

Next, if your application offers an API that will be consumed by third parties, you will choose between [Passport](/docs/{{version}}/passport) or [Sanctum](/docs/{{version}}/sanctum) to provide API token authentication for your application. In general, Sanctum should be preferred when possible since it is a simple, complete solution for API authentication, SPA authentication, and mobile authentication, including support for "scopes" or "abilities".

그리고, 다른 곳에서 사용할 API를 만들어야 한다면 [Passport](/docs/{{version}}/passport) 또는 [Sanctum](/docs/{{version}}/sanctum) 중에서 골라 API 토큰 인증을 사용하면 됩니다. 일반적으로 Sanctum은 "범위"또는 "기능"을 지원하며 API 인증, SPA 인증 및 모바일 인증을 위한 간단하고 완전한 솔루션이기 때문에 가능하면 Sanctum을 사용하세요.

If you are building a single-page application (SPA) that will be powered by a Laravel backend, you should use [Laravel Sanctum](/docs/{{version}}/sanctum). When using Sanctum, you will either need to [manually implement your own backend authentication routes](#authenticating-users) or utilize [Laravel Fortify](/docs/{{version}}/fortify) as a headless authentication backend service that provides routes and controllers for features such as registration, password reset, email verification, and more.

라라벨이 백엔드로 이루어진 단일 페이지 애플리케이션 (SPA)을 만든다면 [Laravel Sanctum](/docs/{{version}}/sanctum)을 사용하면 됩니다. Sanctum을 사용할 때는 [자신의 백엔드 인증 라우트를 수동으로 구현](#authenticating-users) 하거나 [Laravel Fortify](/docs/{{version}}/fortify)를 활용하여 사용자 등록, 비밀번호 재설정, 이메일 확인 등과 같은 기능의 라우트 및 컨트롤러를 제공하는 헤드리스 인증 백엔드 서비스를 제공해야합니다. 

Passport may be chosen when your application absolutely needs all of the features provided by the OAuth2 specification.

애플리케이션에 OAuth2 사양에서 제공하는 모든 기능이 무조건 필요한 경우 Passport를 선택하면 됩니다.

And, if you would like to get started quickly, we are pleased to recommend [Laravel Jetstream](https://jetstream.laravel.com) as a quick way to start a new Laravel application that already uses our preferred authentication stack of Laravel's built-in authentication services and Laravel Sanctum.

<a name="authentication-quickstart"></a>
## Authentication Quickstart
## 빠르게 인증 살펴보기

> {note} This portion of the documentation discusses authenticating users via the [Laravel application starter kits](/docs/{{version}}/starter-kits), which includes UI scaffolding to help you get started quickly. If you would like to integrate with Laravel's authentication systems directly, check out the documentation on [manually authenticating users](#authenticating-users).

> {note} 이 부분에서는 개발을 빠르게 시작하는 데 도움이 되는 UI 스캐폴딩이 포함 된 [라라벨 애플리케이션 스타터 키트](/docs/{{version}}/starter-kits)를 이용한 사용자 인증에 대해 살펴봤습니다. 라라벨의 인증 시스템을 직접 만들고 싶다면 [사용자 인증을 직접 구현 하기 위한](#authenticating-users) 문서를 확인해보세요.

<a name="install-a-starter-kit"></a>
### Install A Starter Kit
### 스타터 키트 설치

First, you should [install a Laravel application starter kit](/docs/{{version}}/starter-kits). Our current starter kits, Laravel Breeze and Laravel Jetstream, offer beautifully designed starting points for incorporating authentication into your fresh Laravel application.

먼저 [라라벨 애플리케이션 스타터 키트를 설치](/docs/{{version}}/starter-kits)해야합니다. 현재 스타터 키트 인 Laravel Breeze 및 Laravel Jetstream은 새로운 라라벨 애플리케이션을 만들 때, 깔끔하게 사용자 인증을 통합해서 만들수 있게 도와줍니다.

Laravel Breeze is a minimal, simple implementation of all of Laravel's authentication features, including login, registration, password reset, email verification, and password confirmation. Laravel Breeze's view layer is made up of simple [Blade templates](/docs/{{version}}/blade) styled with [Tailwind CSS](https://tailwindcss.com). Breeze also offers an [Inertia](https://inertiajs.com) based scaffolding option using Vue or React.

Laravel Breeze는 로그인, 사용자 등록, 비밀번호 재설정, 이메일 확인 및 비밀번호 확인을 포함하여 라라벨의 모든 인증 기능을 최소한으로 간단하게 구현해줍니다. Laravel Breeze의 뷰 레이어는 [Tailwind CSS](https://tailwindcss.com) 스타일의 간단한 [Blade templates](/docs/{{version}}/blade)로 만들어집니다. 또한 Breeze는 Vue 또는 React를 사용하는 [Inertia](https://inertiajs.com) 기반 스캐폴딩 옵션도 제공합니다.

[Laravel Jetstream](https://jetstream.laravel.com) is a more robust application starter kit that includes support for scaffolding your application with [Livewire](https://laravel-livewire.com) or [Inertia.js and Vue](https://inertiajs.com). In addition, Jetstream features optional support for two-factor authentication, teams, profile management, browser session management, API support via [Laravel Sanctum](/docs/{{version}}/sanctum), account deletion, and more.

[Laravel Jetstream](https://jetstream.laravel.com)은 [Livewire](https://laravel-livewire.com) 또는 [Inertia.js 및 Vue](https://inertiajs.com)를 사용하여 애플리케이션의 스캐폴딩을 지원하는 강력한 애플리케이션 스타터 키트입니다. 또한 Jetstream은 2 단계 인증, 팀, 프로필 관리, 브라우저 세션 관리, [Laravel Sanctum](/docs/{{version}}/sanctum)을 통한 API 지원, 계정 삭제 등에 대해 선택적으로 지원할 수 있는 기능을 제공합니다.

<a name="retrieving-the-authenticated-user"></a>
### Retrieving The Authenticated User
### 승인된 사용자 조회하기

After installing an authentication starter kit and allowing users to register and authenticate with your application, you will often need to interact with the currently authenticated user. While handling an incoming request, you may access the authenticated user via the `Auth` facade's `user` method:

인증 스타터 키트를 설치하고 사용자가 애플리케이션에 등록하고 인증 할 수 있도록 만든 후, 현재 인증 된 사용자 정보를 가져와야하는 경우가 많습니다. 사용자의 요청을 처리하는 동안 `Auth` 파사드의 `user` 메소드를 통해 인증 된 사용자정보를 가져올 수 있습니다.

    use Illuminate\Support\Facades\Auth;

    // Retrieve the currently authenticated user...
    $user = Auth::user();

    // Retrieve the currently authenticated user's ID...
    $id = Auth::id();

Alternatively, once a user is authenticated, you may access the authenticated user via an `Illuminate\Http\Request` instance. Remember, type-hinted classes will automatically be injected into your controller methods. By type-hinting the `Illuminate\Http\Request` object, you may gain convenient access to the authenticated user from any controller method in your application via the request's `user` method:

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
#### Determining If The Current User Is Authenticated
#### 현재 사용자의 승인 여부 결정하기

To determine if the user making the incoming HTTP request is authenticated, you may use the `check` method on the `Auth` facade. This method will return `true` if the user is authenticated:

들어오는 HTTP 요청에 사용자가 인증되어 있는지 확인해보려면 `Auth` 파사드에서 `check` 메소드를 사용하면 됩니다. 이 메서드는 사용자가 인증 된 경우 `true`를 반환합니다.

    use Illuminate\Support\Facades\Auth;

    if (Auth::check()) {
        // The user is logged in...
    }

> {tip} Even though it is possible to determine if a user is authenticated using the `check` method, you will typically use a middleware to verify that the user is authenticated before allowing the user access to certain routes / controllers. To learn more about this, check out the documentation on [protecting routes](/docs/{{version}}/authentication#protecting-routes).

> {tip} `check` 메서드를 사용하여 사용자가 인증되어 있는지 확인할 수 있지만, 일반적으로 사용자가 특정 라우트 컨트롤러에 접근 할 수 있도록 허용할 때, 미들웨어를 사용하여 사용자가 인증되었는지 확인합니다. 이에 대해 자세히 알아 보려면 [라우트 보호하기](/docs/{{version}}/authentication#protecting-routes)에 대한 문서를 확인하십시오.

<a name="protecting-routes"></a>
### Protecting Routes
### 라우트 보호하기

[Route middleware](/docs/{{version}}/middleware) can be used to only allow authenticated users to access a given route. Laravel ships with an `auth` middleware, which references the `Illuminate\Auth\Middleware\Authenticate` class. Since this middleware is already registered in your application's HTTP kernel, all you need to do is attach the middleware to a route definition:

[라우트 미들웨어](/docs/{{version}}/middleware)를 이용하여 지정한 라우트에 인증된 사용자에게만 접근할 수 있도록 만들 수도 있습니다. 라라벨은 `Illuminate\Auth\Middleware\Authenticate` 클래스를 참조하는 `auth` 미들웨어를 제공하고 있습니다. 이 미들웨어는 이미 애플리케이션의 HTTP 커널에 등록되어 있으므로 미들웨어를 라우트 정의에 추가하기만 만하면됩니다.

    Route::get('/flights', function () {
        // Only authenticated users may access this route...
    })->middleware('auth');

<a name="redirecting-unauthenticated-users"></a>
#### Redirecting Unauthenticated Users
#### 인증되지 않는 사용자 리다이렉팅하기

When the `auth` middleware detects an unauthenticated user, it will redirect the user to the `login` [named route](/docs/{{version}}/routing#named-routes). You may modify this behavior by updating the `redirectTo` function in your application's `app/Http/Middleware/Authenticate.php` file:

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
#### Specifying A Guard
#### Guard 지정하기

When attaching the `auth` middleware to a route, you may also specify which "guard" should be used to authenticate the user. The guard specified should correspond to one of the keys in the `guards` array of your `auth.php` configuration file:

`auth` 미들웨어를 라우트에 추가할 때, 어떤 "가드"를 인증에 사용할지 정할 수도 있습니다. 지정한 가드는 `auth.php` 설정 파일의 `guards` 배열에 등록된 키 중에 한개여야 합니다.


    Route::get('/flights', function () {
        // Only authenticated users may access this route...
    })->middleware('auth:admin');

<a name="login-throttling"></a>
### Login Throttling
### 로그인 횟수 제한

If you are using the Laravel Breeze or Laravel Jetstream [starter kits](/docs/{{version}}/starter-kits), rate limiting will automatically be applied to login attempts. By default, the user will not be able to login for one minute if they fail to provide the correct credentials after several attempts. The throttling is unique to the user's username / email address and their IP address.

Laravel Breeze나 Laravel Jetstream 같은 [스타터 키트](/docs/{{version}}/starter-kits)를 사용하는 경우, 로그인 시도에 횟수 제한이 자동으로 적용됩니다. 기본적으로 사용자는 몇 번의 시도 후에도 올바른 인증 정보를 제공하지 않으면, 1분 동안 로그인할 수 없습니다. 이 제한은 사용자의 사용자 이름 / 이메일 및 IP 주소별로 각각 동작합니다.

> {tip} If you would like to rate limit other routes in your application, check out the [rate limiting documentation](/docs/{{version}}/routing#rate-limiting).

> {tip} 애플리케이션 내의 다른 경로에도 횟수를 제한하려면 [횟수 제한 문서](/docs/{{version}}/routing#rate-limiting)를 확인하세요.

<a name="authenticating-users"></a>
## Manually Authenticating Users
## 수동으로 사용자 인증하기

You are not required to use the authentication scaffolding included with Laravel's [application starter kits](/docs/{{version}}/starter-kits). If you choose not to use this scaffolding, you will need to manage user authentication using the Laravel authentication classes directly. Don't worry, it's a cinch!

라라벨 [스타터 키트](/docs/{{version}}/starter-kits)에 포함된 인증 스캐폴딩을 꼭 사용할 필요는 없습니다. 이 스캐폴딩을 사용하지 않기로 결정했다면, 라라벨 인증 클래스를 사용하여 직접 사용자 인증을 처리해야 합니다. 걱정하지 마세요. 간단합니다!

We will access Laravel's authentication services via the `Auth` [facade](/docs/{{version}}/facades), so we'll need to make sure to import the `Auth` facade at the top of the class. Next, let's check out the `attempt` method. The `attempt` method is normally used to handle authentication attempt's from your application's "login" form. If authentication is successful, you should regenerate the user's [session](/docs/{{version}}/session) to prevent [session fixation](https://en.wikipedia.org/wiki/Session_fixation):

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

The `attempt` method accepts an array of key / value pairs as its first argument. The values in the array will be used to find the user in your database table. So, in the example above, the user will be retrieved by the value of the `email` column. If the user is found, the hashed password stored in the database will be compared with the `password` value passed to the method via the array. You should not hash the incoming request's `password` value, since the framework will automatically hash the value before comparing it to the hashed password in the database. An authenticated session will be started for the user if the two hashed passwords match.

`attempt` 메소드는 키 / 값의 쌍으로 이루어진 배열을 첫번째 인자로 전달 받습니다. 배열의 값들은 데이터베이스 테이블에서 사용자를 찾는데 사용됩니다. 따라서 위의 예제에서는, `email` 컬럼을 통해서 사용자를 찾습니다. 사용자를 찾았다면, 해시 처리되어 데이터베이스에 저장된 패스워드와 메소드로 전달받은 배열의 `password` 값을 비교합니다. 데이터베이스에서 해시 처리된 암호와 비교하기 전에 프레임워크가 값을 자동으로 해시처리 하기 때문에, 입력한 `password` 값을 해시처리하면 안됩니다. 두개의 해시처리된 패스워드가 일치한다면 해당 사용자로 인증된 세션을 새로 발급해줍니다.

Remember, Laravel's authentication services will retrieve users from your database based on your authentication guard's "provider" configuration. In the default `config/auth.php` configuration file, the Eloquent user provider is specified and it is instructed to use the `App\Models\User` model when retrieving users. You may change these values within your configuration file based on the needs of your application.

라라벨의 인증 서비스는 인증 가드의 "공급자"설정에 따라 데이터베이스에서 사용자를 검색합니다. `config/auth.php` 설정 파일에는 기본적으로 Eloquent 사용자 공급자가 지정되어 있으며, 사용자를 검색 할 때 `App\Models\User` 모델을 사용하도록 설정되어 있습니다. 필요하다면 설정 파일에서 값을 변경하면 됩니다.

The `attempt` method will return `true` if authentication was successful. Otherwise, `false` will be returned.

`attempt` 메소드는 인증에 성공하면 `true`를, 실패하면 `false`를 반환합니다.

The `intended` method provided by Laravel's redirector will redirect the user to the URL they were attempting to access before being intercepted by the authentication middleware. A fallback URI may be given to this method in case the intended destination is not available.

라라벨의 리다이렉터가 제공하는 `intended` 메소드는 사용자가 인증 미들웨어에 의해 잡히기 전, 원래 접근하려고 시도했었던 URL로 사용자를 리다이렉트 시킵니다. 이전 URL로 이동이 불가능한 경우, 대체 할 URI를 메소드에 지정할 수 있습니다.

<a name="specifying-additional-conditions"></a>
#### Specifying Additional Conditions
#### 추가적인 조건 지정하기

If you wish, you may also add extra query conditions to the authentication query in addition to the user's email and password. To accomplish this, we may simply add the query conditions to the array passed to the `attempt` method. For example, we may verify that the user is marked as "active":

원한다면, 사용자의 이메일 및 비밀번호 외에 추가 쿼리 조건을 인증 쿼리에 추가 할 수도 있습니다. 사용하려면 `attempt` 메소드에 전달 된 배열에 쿼리 조건을 추가하면 됩니다. 예를 들면, 다음과 같이 사용자가 "사용중-active"으로 등록되어 있는지 검사할 수 있습니다.

    if (Auth::attempt(['email' => $email, 'password' => $password, 'active' => 1])) {
        // Authentication was successful...
    }

> {note} In these examples, `email` is not a required option, it is merely used as an example. You should use whatever column name corresponds to a "username" in your database table.

> {note} 이 예제에서, `email` 은 필수 옵션이 아니라, 그냥 예제에서 사용한 것입니다. 여러분은 데이터베이스 테이블 안에 있는 "username"과 일치하는 어떠한 컬럼 이름을 사용해야 합니다.

<a name="accessing-specific-guard-instances"></a>
#### Accessing Specific Guard Instances
#### 지정된 Guard 인스턴스에 엑세스하기

Via the `Auth` facade's `guard` method, you may specify which guard instance you would like to utilize when authenticating the user. This allows you to manage authentication for separate parts of your application using entirely separate authenticatable models or user tables.

`Auth` 파사드의 `guard` 메소드를 통해 사용자를 인증 할 때, 사용할 가드 인스턴스를 지정할 수 있습니다. 이를 통해 완전히 분리된 모델 또는 사용자 테이블을 사용하여, 애플리케이션의 일부분에 대한 인증을 따로 관리 할 수 있습니다.

The guard name passed to the `guard` method should correspond to one of the guards configured in your `auth.php` configuration file:

`guard` 메소드에 전달되는 guard의 이름은 `auth.php` 설정 파일에 설정된 guard 중 하나와 일치 해야합니다.

    if (Auth::guard('admin')->attempt($credentials)) {
        // ...
    }

<a name="remembering-users"></a>
### Remembering Users
### 사용자 기억하기

Many web applications provide a "remember me" checkbox on their login form. If you would like to provide "remember me" functionality in your application, you may pass a boolean value as the second argument to the `attempt` method.

많은 웹 애플리케이션은 로그인 양식에 "내 정보 기억" 체크박스을 제공합니다. 애플리케이션에 "기억하기" 기능을 제공하려면, `attempt` 메서드의 두번째 인자에 boolean 값을 전달하면 됩니다.

When this value is `true`, Laravel will keep the user authenticated indefinitely or until they manually logout. Your `users` table must include the string `remember_token` column, which will be used to store the "remember me" token. The `users` table migration included with new Laravel applications already includes this column:

이 값이 `true`이면 라라벨은 사용자를 무기한으로 또는 수동으로 로그아웃 할 때까지 인증 된 상태로 유지합니다. `users`테이블에는 'remember me'토큰을 저장하는 데 사용할 `remember_token`열이 있어야 합니다. 새로운 라라벨 애플리케이션에 포함 된 `users` 테이블 마이그레이션에는 기본적으로 해당 컬럼이 포함되어 있습니다.

    use Illuminate\Support\Facades\Auth;

    if (Auth::attempt(['email' => $email, 'password' => $password], $remember)) {
        // The user is being remembered...
    }

<a name="other-authentication-methods"></a>
### Other Authentication Methods
### 그 외 인증 메소드

<a name="authenticate-a-user-instance"></a>
#### Authenticate A User Instance
#### 사용자 인스턴스를 통해서 인증하기

If you need to set an existing user instance as the currently authenticated user, you may pass the user instance to the `Auth` facade's `login` method. The given user instance must be an implementation of the `Illuminate\Contracts\Auth\Authenticatable` [contract](/docs/{{version}}/contracts). The `App\Models\User` model included with Laravel already implements this interface. This method of authentication is useful when you already have a valid user instance, such as directly after a user registers with your application:

사용자 인스턴스를 현재 로그인 한 사용자로 등록하고 싶다면, 사용자 인스턴스를 `Auth` 파사드의 `login` 메소드에 입력하면 됩니다. 입력한 인스턴스는 `Illuminate\Contracts\Auth\Authenticatable` [contract](/docs/{{version}}/contracts)의 구현체여야 합니다. 라라벨에 포함된 `App\Models\User` 모델은 이미 이 인터페이스를 구현하고 있습니다. 이 인증 메서드는 사용자를 애플리케이션에 등록한 직후와 같이 유효한 사용자 인스턴스가 이미 있는 경우에 유용합니다. (`역자주: 회원가입 직후 해당 유저로 바로 로그인 시켜줄 때를 의미합니다.`)

    use Illuminate\Support\Facades\Auth;

    Auth::login($user);

You may pass a boolean value as the second argument to the `login` method. This value indicates if "remember me" functionality is desired for the authenticated session. Remember, this means that the session will be authenticated indefinitely or until the user manually logs out of the application:

`login`메소드의 두 번째 인수로 boolean 값을 전달할 수 있습니다. 이 값은 인증 된 세션에 "remember me"기능을 사용할지를 의미합니다. `true`로 설정한다면 세션이 무기한으로 또는 사용자가 애플리케이션에서 수동으로 로그아웃 할 때까지 인증을 유지하게 됩니다.

    Auth::login($user, $remember = true);

If needed, you may specify an authentication guard before calling the `login` method:

필요한 경우 `login` 메서드를 호출하기 전에 인증 가드를 지정할 수 있습니다.

    Auth::guard('admin')->login($user);

<a name="authenticate-a-user-by-id"></a>
#### Authenticate A User By ID
#### ID를 통해서 사용자 인증하기

To authenticate a user using their database record's primary key, you may use the `loginUsingId` method. This method accepts the primary key of the user you wish to authenticate:

데이터베이스 레코드의 기본 키를 사용하여 사용자를 인증하려면 `loginUsingId` 메소드를 사용하면 됩니다. 이 메서드는 인증하려는 사용자의 기본 키를 입력받습니다.

    Auth::loginUsingId(1);

You may pass a boolean value as the second argument to the `loginUsingId` method. This value indicates if "remember me" functionality is desired for the authenticated session. Remember, this means that the session will be authenticated indefinitely or until the user manually logs out of the application:

`loginUsingId`메소드의 두 번째 인수로 boolean 값을 전달할 수 있습니다. 이 값은 인증 된 세션에 "remember me"기능을 사용할지를 의미합니다. `true`로 설정한다면 세션이 무기한으로 또는 사용자가 애플리케이션에서 수동으로 로그아웃 할 때까지 인증을 유지하게 됩니다.

    Auth::loginUsingId(1, $remember = true);

<a name="authenticate-a-user-once"></a>
#### Authenticate A User Once
#### 한번만 사용자로 인증 하기

You may use the `once` method to authenticate a user with the application for a single request. No sessions or cookies will be utilized when calling this method:

일회성 요청에 대해 사용자로 인증하기 위해 `once` 메소드를 사용할 수 있습니다. 이 메서드를 사용 할 때는 세션이나 쿠키가 사용되지 않습니다.

    if (Auth::once($credentials)) {
        //
    }

<a name="http-basic-authentication"></a>
## HTTP Basic Authentication
## HTTP 기본 인증

[HTTP Basic Authentication](https://en.wikipedia.org/wiki/Basic_access_authentication) provides a quick way to authenticate users of your application without setting up a dedicated "login" page. To get started, attach the `auth.basic` [middleware](/docs/{{version}}/middleware) to a route. The `auth.basic` middleware is included with the Laravel framework, so you do not need to define it:

[HTTP 기본 인증](https://en.wikipedia.org/wiki/Basic_access_authentication) 은 애플리케이션에 별도의 "login" 페이지 설정없이도 사용자 인증을 할 수 있는 손쉬운 방법을 제공합니다. 이를 위해서는 `auth.basic` [미들웨어](/docs/{{version}}/middleware)를 라우트에 추가하면 됩니다. `auth.basic` 미들웨어는 라라벨에 포함되어 있기 때문에 따로 정의할 필요가 없습니다.

    Route::get('/profile', function () {
        // Only authenticated users may access this route...
    })->middleware('auth.basic');

Once the middleware has been attached to the route, you will automatically be prompted for credentials when accessing the route in your browser. By default, the `auth.basic` middleware will assume the `email` column on your `users` database table is the user's "username".

미들웨어가 라우트에 추가되면 브라우저에서 라우트에 접근할 때, 인증 정보를 입력하라는 메시지가 자동으로 표시됩니다. `auth.basic` 미들웨어는 기본적으로 `users` 데이터베이스 테이블의 `email` 컬럼이 "username"이라고 가정합니다.

<a name="a-note-on-fastcgi"></a>
#### A Note On FastCGI
#### FastCGI에 대한 참고사항

If you are using PHP FastCGI and Apache to serve your Laravel application, HTTP Basic authentication may not work correctly. To correct these problems, the following lines may be added to your application's `.htaccess` file:

라라벨 애플리케이션을 사용하기 위해 PHP FastCGI 및 Apache를 사용하는 경우 HTTP 기본 인증이 제대로 작동하지 않을 수 있습니다. 이런 문제를 해결하려면, 애플리케이션의 `.htaccess` 파일에 다음과 같이 추가하면 됩니다.

    RewriteCond %{HTTP:Authorization} ^(.+)$
    RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

<a name="stateless-http-basic-authentication"></a>
### Stateless HTTP Basic Authentication
### 상태를 유지하지 않는 HTTP 기본 인증

You may also use HTTP Basic Authentication without setting a user identifier cookie in the session. This is primarily helpful if you choose to use HTTP Authentication to authenticate requests to your application's API. To accomplish this, [define a middleware](/docs/{{version}}/middleware) that calls the `onceBasic` method. If no response is returned by the `onceBasic` method, the request may be passed further into the application:

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

Next, [register the route middleware](/docs/{{version}}/middleware#registering-middleware) and attach it to a route:

다음으로, [라우트 미들웨어에 등록하고](/docs/{{version}}/middleware#registering-middleware), 이 미들웨어를 라우트에 추가하십시오.

    Route::get('/api/user', function () {
        // Only authenticated users may access this route...
    })->middleware('auth.basic.once');

<a name="logging-out"></a>
## Logging Out
## 로그아웃

To manually log users out of your application, you may use the `logout` method provided by the `Auth` facade. This will remove the authentication information from the user's session so that subsequent requests are not authenticated.

애플리케이션에서 사용자를 수동으로 로그아웃하려면 `Auth` 파사드에서 제공하는 `logout` 메소드를 사용하면 됩니다. 이렇게하면 후속 요청에서는 인증되지 않도록 사용자 세션에서 인증 정보가 제거됩니다.

In addition to calling the `logout` method, it is recommended that you invalidate the user's session and regenerate their [CSRF token](/docs/{{version}}/csrf). After logging the user out, you would typically redirect the user to the root of your application:

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
### Invalidating Sessions On Other Devices
### 다른 디바이스의 세션 무효화

Laravel also provides a mechanism for invalidating and "logging out" a user's sessions that are active on other devices without invalidating the session on their current device. This feature is typically utilized when a user is changing or updating their password and you would like to invalidate sessions on other devices while keeping the current device authenticated.

라라벨은 또한 현재 장치에서 로그인정보를 유지하면서, 다른 장치에서 인증된 된 로그인 정보를 무효화하고 "로그아웃"시키는 방법을 제공합니다. 이 기능은 일반적으로 사용자가 암호를 변경하거나 업데이트 할 때, 현재 장치의 인증을 유지하면서 다른 장치에서 세션을 무효화하려는 경우에 사용됩니다.

Before getting started, you should make sure that the `Illuminate\Session\Middleware\AuthenticateSession` middleware is present and un-commented in your `App\Http\Kernel` class' `web` middleware group:

시작하기 전에 `Illuminate\Session\Middleware\AuthenticateSession` 미들웨어가 존재하고 `App\Http\Kernel` 클래스의 `web` 미들웨어 그룹에서 주석 처리되어있지는 않은지 확인해야합니다.

    'web' => [
        // ...
        \Illuminate\Session\Middleware\AuthenticateSession::class,
        // ...
    ],

Then, you may use the `logoutOtherDevices` method provided by the `Auth` facade. This method requires the user to confirm their current password, which your application should accept through an input form:

그런 다음 `Auth` 파사드에서 제공하는 `logoutOtherDevices` 메소드를 사용하면 됩니다. 이 메서드를 사용하려면 사용자가 현재 비밀번호를 입력해줘야 합니다. 애플리케이션이 입력 폼을 통해 입력받아서 처리합니다.

    use Illuminate\Support\Facades\Auth;

    Auth::logoutOtherDevices($currentPassword);

When the `logoutOtherDevices` method is invoked, the user's other sessions will be invalidated entirely, meaning they will be "logged out" of all guards they were previously authenticated by.

`logoutOtherDevices` 메소드가 호출되면 사용자의 다른 세션은 완전히 무효화됩니다. 즉, 이전에 인증 된 모든 가드에서 "로그아웃"됩니다.

<a name="password-confirmation"></a>
## Password Confirmation
## 비밀번호 확인

While building your application, you may occasionally have actions that should require the user to confirm their password before the action is performed or before the user is redirected to a sensitive area of the application. Laravel includes built-in middleware to make this process a breeze. Implementing this feature will require you to define two routes: one route to display a view asking the user to confirm their password and another route to confirm that the password is valid and redirect the user to their intended destination.

애플리케이션을 만드는 동안, 어떠한 작업을 수행하기 전, 또는 사용자가 애플리케이션의 중요한 곳으로 이동하기 전에 사용자의 암호를 확인해야 할 필요가 있을 수도 있습니다. 라라벨은 이 과정을 쉽게 만들어주는 내장 미들웨어를 포함하고 있습니다. 이 기능을 구현하려면 두 가지 경로를 정의해야 합니다. 하나는 사용자에게 암호 확인을 요청하는 view를 표시하는 경로이고, 다른 하나는 암호가 유효한지 확인 후, 사용자를 원하는 곳으로 이동하는 경로입니다.

> {tip} The following documentation discusses how to integrate with Laravel's password confirmation features directly; however, if you would like to get started more quickly, the [Laravel application starter kits](/docs/{{version}}/starter-kits) include support for this feature!

> {tip} 아래 문서는 라라벨의 비밀번호 확인 기능을 직접 연동하는 방법에 대해 설명합니다. 그러나 더 빨리 시작하기위해 [라라벨 애플리케이션 스타터 키트](/docs/{{version}}/starter-kits)가 이 기능들을 지원하고 있습니다!

<a name="password-confirmation-configuration"></a>
### Configuration
### 환경설정

After confirming their password, a user will not be asked to confirm their password again for three hours. However, you may configure the length of time before the user is re-prompted for their password by changing the value of the `password_timeout` configuration value within your application's `config/auth.php` configuration file.

비밀번호를 확인한 후 사용자는 3시간 동안 비밀번호를 다시 확인하지 않아도 됩니다. 그러나 애플리케이션의 `config/auth.php` 설정 파일의 `password_timeout` 값을 변경하여 사용자에게 암호를 다시 묻는 메시지가 표시되기 까지의 시간을 변경할 수 있습니다.

<a name="password-confirmation-routing"></a>
### Routing
### 라우팅

<a name="the-password-confirmation-form"></a>
#### The Password Confirmation Form
#### 비밀번호 확인 양식

First, we will define a route to display a view that requests that the user to confirm their password:

먼저 사용자에게 비밀번호 확인을 요청하는 폼의 경로를 지정합니다.

    Route::get('/confirm-password', function () {
        return view('auth.confirm-password');
    })->middleware('auth')->name('password.confirm');

As you might expect, the view that is returned by this route should have a form containing a `password` field. In addition, feel free to include text within the view that explains that the user is entering a protected area of the application and must confirm their password.

예상 할 수 있듯이, 이 경로에서 반환되는 view에는 `password` 필드가 포함된 form이 있어야 합니다. 또한 사용자가 애플리케이션의 보호된 영역에 들어가기위해 비밀번호를 확인해야 한다는 내용을 view에 자유롭게 작성할 수 있습니다.

<a name="confirming-the-password"></a>
#### Confirming The Password
#### 비밀번호 확인

Next, we will define a route that will handle the form request from the "confirm password" view. This route will be responsible for validating the password and redirecting the user to their intended destination:

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

Before moving on, let's examine this route in more detail. First, the request's `password` field is determined to actually match the authenticated user's password. If the password is valid, we need to inform Laravel's session that the user has confirmed their password. The `passwordConfirmed` method will set a timestamp in the user's session that Laravel can use to determine when the user last confirmed their password. Finally, we can redirect the user to their intended destination.

계속 진행하기 전에 이 경로를 자세히 살펴보겠습니다. 먼저 요청된 `password`필드가 현재 인증된 사용자의 비밀번호와 실제로 일치하는지 확인합니다. 비밀번호가 맞다면 사용자가 비밀번호를 인증해줬다고 라라벨 세션에 등록해줍니다. `passwordConfirmed` 메소드는 사용자가 마지막으로 비밀번호를 인증한 시기를 저장해두기위해, 라라벨이 사용할 수 있는 사용자 세션에 타임스탬프를 저장합니다. 마지막으로 사용자가 원한 목적지로 이동하게 됩니다.

<a name="password-confirmation-protecting-routes"></a>
### Protecting Routes
### 라우트 보호하기

You should ensure that any route that performs an action which requires recent password confirmation is assigned the `password.confirm` middleware. This middleware is included with the default installation of Laravel and will automatically store the user's intended destination in the session so that the user may be redirected to that location after confirming their password. After storing the user's intended destination in the session, the middleware will redirect the user to the `password.confirm` [named route](/docs/{{version}}/routing#named-routes):

비밀번호 확인이 필요한 작업을 수행하는 모든 경로에 `password.confirm` 미들웨어가 할당 되었는지 확인해야 합니다. 이 미들웨어는 라라벨의 기본적으로 포함되어 있으며 사용자가 암호를 확인한 후 해당 위치로 이동 할 수 있도록, 세션에 사용자가 원했던 경로를 자동으로 저장합니다. 세션에 사용자가 원했던 경로를 저장 한 후, 미들웨어는 사용자를 `password.confirm` [라는 이름이 지정된 경로로](/docs/{{version}}/routing#named-routes) 이동합니다.

    Route::get('/settings', function () {
        // ...
    })->middleware(['password.confirm']);

    Route::post('/settings', function () {
        // ...
    })->middleware(['password.confirm']);

<a name="adding-custom-guards"></a>
## Adding Custom Guards
## 사용자정의 Guard 추가

You may define your own authentication guards using the `extend` method on the `Auth` facade. You should place your call to the `extend` method within a [service provider](/docs/{{version}}/providers). Since Laravel already ships with an `AuthServiceProvider`, we can place the code in that provider:

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

As you can see in the example above, the callback passed to the `extend` method should return an implementation of `Illuminate\Contracts\Auth\Guard`. This interface contains a few methods you will need to implement to define a custom guard. Once your custom guard has been defined, you may reference the guard in the `guards` configuration of your `auth.php` configuration file:

아래의 예제에서 볼 수 있듯이, `extend` 메소드에 전달된 콜백은 `Illuminate\Contracts\Auth\Guard` 구현체를 반환해야 합니다. 이 인터페이스는 사용자 정의 가드를 구현하는데 필요한 몇가지 메소드를 가지고 있습니다. 사용자 정의 가드를 정의하고나면, `guards` 설정에서 정의한 가드를 사용할 수 있습니다.

    'guards' => [
        'api' => [
            'driver' => 'jwt',
            'provider' => 'users',
        ],
    ],

<a name="closure-request-guards"></a>
### Closure Request Guards
### 클로저 형태의 Request Guards

The simplest way to implement a custom, HTTP request based authentication system is by using the `Auth::viaRequest` method. This method allows you to quickly define your authentication process using a single closure.

인증 시스템을 기반으로한 커스텀 HTTP request-요청을 구현하는 가장 간단한 방법은 `Auth::viaRequest` 메소드를 사용하는 것 입니다. 이 메소드는 하나의 클로저를 사용하여 빠르기 인증을 정의할 수 있게 해줍니다.

To get started, call the `Auth::viaRequest` method within the `boot` method of your `AuthServiceProvider`. The `viaRequest` method accepts an authentication driver name as its first argument. This name can be any string that describes your custom guard. The second argument passed to the method should be a closure that receives the incoming HTTP request and returns a user instance or, if authentication fails, `null`:

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

Once your custom authentication driver has been defined, you may configure it as a driver within the `guards` configuration of your `auth.php` configuration file:

사용자 정의 인증 드라이버를 만들었다면, `auth.php` 설정 파일의 `guards` 설정 안에서 드라이버로 사용할 수 있습니다.

    'guards' => [
        'api' => [
            'driver' => 'custom-token',
        ],
    ],

<a name="adding-custom-user-providers"></a>
## Adding Custom User Providers
## 사용자 정의 User 프로바이더 추가하기

If you are not using a traditional relational database to store your users, you will need to extend Laravel with your own authentication user provider. We will use the `provider` method on the `Auth` facade to define a custom user provider. The user provider resolver should return an implementation of `Illuminate\Contracts\Auth\UserProvider`:

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

After you have registered the provider using the `provider` method, you may switch to the new user provider in your `auth.php` configuration file. First, define a `provider` that uses your new driver:

`provider` 메소드로 프로바이더를 등록한 뒤, `auth.php` 설정 파일에서 새 User 프로바이더로 변경할 수 있습니다. 먼저 새로운 드라이버를 사용하는 `provider`를 정의합시다.

    'providers' => [
        'users' => [
            'driver' => 'mongo',
        ],
    ],

Finally, you may reference this provider in your `guards` configuration:

마지막으로, `guards` 설정 에서 이 프로바이더를 사용할 수 있습니다.

    'guards' => [
        'web' => [
            'driver' => 'session',
            'provider' => 'users',
        ],
    ],

<a name="the-user-provider-contract"></a>
### The User Provider Contract
### 사용자 프로바이더 Contract

`Illuminate\Contracts\Auth\UserProvider` implementations are responsible for fetching an `Illuminate\Contracts\Auth\Authenticatable` implementation out of a persistent storage system, such as MySQL, MongoDB, etc. These two interfaces allow the Laravel authentication mechanisms to continue functioning regardless of how the user data is stored or what type of class is used to represent the authenticated user:

`Illuminate\Contracts\Auth\UserProvider`의 구현체는 MySQL, MongoDB 등의 영구 스토리지 시스템에서 `Illuminate\Contracts\Auth\Authenticatable`의 구현체를 불러오는 것만 담당합니다. 이 두 인터페이스는 사용자 데이터가 어떻게 저장되었는지 또는 이를 위해 어떤 클래스가 사용되고 있는지 여부에 상관없이 라라벨 인증 메커니즘이 작동하도록 해줍니다.

Let's take a look at the `Illuminate\Contracts\Auth\UserProvider` contract:

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

The `retrieveById` function typically receives a key representing the user, such as an auto-incrementing ID from a MySQL database. The `Authenticatable` implementation matching the ID should be retrieved and returned by the method.

`retrieveById` 함수는 일반적으로 MySQL 데이터베이스의 자동 증가 ID와 같은 사용자를 대표하는 키를 전달 받습니다. ID에 상응하는 `Authenticatable` 구현체를 조회한 후 반환해야 합니다.

The `retrieveByToken` function retrieves a user by their unique `$identifier` and "remember me" `$token`, typically stored in a database column like `remember_token`. As with the previous method, the `Authenticatable` implementation with a matching token value should be returned by this method.

`retrieveByToken`함수는 일반적으로 `remember_token`과 같은 데이터베이스 열에 저장되는 고유한 `$identifier`와 "remember me" `$token`을 사용해 사용자를 조회합니다. 이전에 설명한 메소드와 마찬가지로 이 메소드도 토큰 값이 일치한 `Authenticatable` 구현체를 반환합니다.

The `updateRememberToken` method updates the `$user` instance's `remember_token` with the new `$token`. A fresh token is assigned to users on a successful "remember me" authentication attempt or when the user is logging out.

`updateRememberToken` 메소드는 `user` 인스턴스의 `remember_token`을 새로운 `token`으로 업데이트합니다. "remember me" 인증 시도가 성공하거나 사용자가 로그아웃 할 때 사용자에게 새 토큰이 할당됩니다.

The `retrieveByCredentials` method receives the array of credentials passed to the `Auth::attempt` method when attempting to authenticate with an application. The method should then "query" the underlying persistent storage for the user matching those credentials. Typically, this method will run a query with a "where" condition that searches for a user record with a "username" matching the value of `$credentials['username']`. The method should return an implementation of `Authenticatable`. **This method should not attempt to do any password validation or authentication.**

`retrieveByCredentials` 메소드는 애플리케이션에 로그인하려고 시도할 때 `Auth::attempt` 메소드로 전달되는 인증 정보 배열을 전달 받습니다. 그러면 메소드는 그 인증 정보에 맞는 사용자들을 기반에 있는 영속적인 저장소(persistent storage)에 "질의"해야 합니다. 일반적으로 이 메소드는 `$credentials['username']`와 일치하는 "username"를 가진 사용자를 조회하는 "where" 쿼리를 실행할 것입니다. 그 뒤에 메소드는 `Authenticatable`의 구현체를 반환해야 합니다. **이 메소드는 패스워드 확인이나 인증을 시도해서는 안됩니다.**

The `validateCredentials` method should compare the given `$user` with the `$credentials` to authenticate the user. For example, this method will typically use the `Hash::check` method to compare the value of `$user->getAuthPassword()` to the value of `$credentials['password']`. This method should return `true` or `false` indicating whether the password is valid.

`validateCredentials` 메소드는 주어진 `$user`를 `$credentials`와 비교하여 사용자를 인증해야합니다. 예를 들어, 이 메소드는 일반적으로 `Hash::check` 메소드를 사용하여 `$user->getAuthPassword()`값을 `$credentials['password']`값과 비교합니다. 이 메소드는 비밀번호가 유효한지 여부를 나타내는 `true` 혹은 `false`를 반환해야합니다.

<a name="the-authenticatable-contract"></a>
### The Authenticatable Contract
### Authenticatable Contract 살펴보기

Now that we have explored each of the methods on the `UserProvider`, let's take a look at the `Authenticatable` contract. Remember, user providers should return implementations of this interface from the `retrieveById`, `retrieveByToken`, and `retrieveByCredentials` methods:

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

This interface is simple. The `getAuthIdentifierName` method should return the name of the "primary key" field of the user and the `getAuthIdentifier` method should return the "primary key" of the user. When using a MySQL back-end, this would likely be the auto-incrementing primary key assigned to the user record. The `getAuthPassword` method should return the user's hashed password.

이 인터페이스는 간단합니다. `getAuthIdentifierName` 메소드는 사용자의 "primary key" 필드의 이름을 반환해야하고 `getAuthIdentifier` 메소드는 사용자의 "primary key"를 반환해야합니다. MySQL 백엔드를 사용하는 경우 이는 사용자 레코드에 할당 된 auto-incrementing primary key일 수 있습니다. `getAuthPassword` 메소드는 사용자의 해시 된 비밀번호를 반환해야합니다.

This interface allows the authentication system to work with any "user" class, regardless of what ORM or storage abstraction layer you are using. By default, Laravel includes a `App\Models\User` class in the `app/Models` directory which implements this interface.

이 인터페이스를 사용하면 사용중인 ORM 또는 스토리지 추상화 계층과 상관없이, 인증 시스템을 "사용자"클래스와 함께 동작시킬 수 있습니다. 기본적으로 라라벨은 이 인터페이스를 구현한 `App\Models\User` 클래스가 `app/Models` 디렉토리에 포함되어 있습니다.

<a name="events"></a>
## Events
## 이벤트

Laravel dispatches a variety of [events](/docs/{{version}}/events) during the authentication process. You may attach listeners to these events in your `EventServiceProvider`:

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
