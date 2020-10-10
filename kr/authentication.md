# Authentication
# 인증

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [Database Considerations](#introduction-database-considerations)
    - [데이터베이스 유의사항](#introduction-database-considerations)
    - [Ecosystem Overview](#ecosystem-overview)
    - [생태계 개요](#ecosystem-overview)
- [Authentication Quickstart](#authentication-quickstart)
- [빠르게 인증 살펴보기](#authentication-quickstart)
    - [Routing](#included-routing)
    - [Routing-라우팅](#included-routing)
    - [Views](#included-views)
    - [Views-뷰](#included-views)
    - [Authenticating](#included-authenticating)
    - [인증하기](#included-authenticating)
    - [Retrieving The Authenticated User](#retrieving-the-authenticated-user)
    - [승인된 사용자 조회하기](#retrieving-the-authenticated-user)
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
- [Social Authentication](/docs/{{version}}/socialite)
- [소셜 인증](/docs/{{version}}/socialite)
- [Adding Custom Guards](#adding-custom-guards)
- [사용자 정의 Guards 추가하기](#adding-custom-guards)
    - [Closure Request Guards](#closure-request-guards)
    - [클로저 형태의 Request Guards](#closure-request-guards)
- [Adding Custom User Providers](#adding-custom-user-providers)
- [사용자 정의 User 프로바이더 추가하기](#adding-custom-user-providers)
    - [The User Provider Contract](#the-user-provider-contract)
    - [사용자 프로바이더 Contract](#the-user-provider-contract)
    - [The Authenticatable Contract](#the-authenticatable-contract)
    - [인증가능 계약](#the-authenticatable-contract)
- [Events](#events)
- [이벤트](#events)


<a name="introduction"></a>
## Introduction
## 시작하기

Laravel makes implementing authentication very simple. In fact, almost everything is configured for you out of the box. The authentication configuration file is located at `config/auth.php`, which contains several well documented options for tweaking the behavior of the authentication services.

라라벨은 인증기능 구현을 매우 쉽게 해줍니다. 기본적으로 별도의 설정 없이도 대부분 이미 준비되어 있습니다. 인증에 대한 설정 파일은 `config/auth.php`으로 인증 서비스의 동작을 제어할 수 있는 옵션들이 자세한 설명과 함께 제공됩니다.

At its core, Laravel's authentication facilities are made up of "guards" and "providers". Guards define how users are authenticated for each request. For example, Laravel ships with a `session` guard which maintains state using session storage and cookies.

내부 시스템에서, 라라벨의 인증 기능은 "guards" 와 "프로바이더"로 구성되어 있습니다. Guard는 사용자가 각각의 요청-request마다 어떻게 인증되는지 정의합니다. 예를 들어 라라벨은 세션 스토리지와 쿠키를 사용하여 상태를 유지하는 `session` guard와, 각 요청-request와 함께 전달되는 "API 토큰"을 사용하여 사용자를 인증하는 `token` guard를 제공합니다.

Providers define how users are retrieved from your persistent storage. Laravel ships with support for retrieving users using Eloquent and the database query builder. However, you are free to define additional providers as needed for your application.

프로바이더는 저장소에서 사용자를 어떻게 찾을 수 있는지 정의합니다. 라라벨은 Eloquent와 데이터베이스 쿼리 빌더를 사용하여 사용자를 찾을 수 있도록 지원합니다. 또한, 애플리케이션에서 필요로 하는 추가적인 프로바이더를 자유롭게 정의할 수 있습니다.

Don't worry if this all sounds confusing now! Many applications will never need to modify the default authentication configuration.

지금 이러한 이야기가 혼란스럽더라도 걱정하지 마십시오. 많은 애플리케이션은 기본 인증 설정을 변경할 필요가 전혀 없습니다.

#### Getting Started Fast
#### 빠르게 시작하기

Want to get started fast? Install [Laravel Jetstream](https://jetstream.laravel.com) in a fresh Laravel application. After migrating your database, navigate your browser to `/register` or any other URL that is assigned to your application. Jetstream will take care of scaffolding your entire authentication system!

빠르게 시작하길 원하십니까? [Laravel Jetstream](https://jetstream.laravel.com) 을 새로운 라라벨 애플리케이션에 설치하십시오. 데이터베이스를 마이그레이션 한 후 브라우저에서 `/register` 또는 애플리케이션에 할당된 다른 URL로 이동하십시오. Jetstream이 전체적인 인증 시스템을 스캐폴딩 합니다.

<a name="introduction-database-considerations"></a>
### Database Considerations
### 데이터베이스 유의사항

By default, Laravel includes an `App\Models\User` [Eloquent model](/docs/{{version}}/eloquent) in your `app/Models` directory. This model may be used with the default Eloquent authentication driver. If your application is not using Eloquent, you may use the `database` authentication driver which uses the Laravel query builder.

기본적으로 라라벨은 `app/Models` 디렉토리에 `App\Models\User` [Eloquent model](/docs/{{version}}/eloquent) 모델을 포함하고 있습니다. 이 모델은 기본적인 Eloquent 인증 드라이버와 함께 사용하게 됩니다. 애플리케이션이 Eloquent를 사용하고 있지 않다면 라라벨 쿼리 빌더를 사용하는 `database` 인증 드라이버를 이용하면 됩니다.

When building the database schema for the `App\Models\User` model, make sure the password column is at least 60 characters in length. Maintaining the default string column length of 255 characters would be a good choice.

`App\Models\User` 모델을 위한 데이터베이스 스키마를 구성할 때, 패스워드 컬럼은 최소 60자가 되어야 하는 것을 명심하십시오. 기본값인 255을 유지하는 것도 좋은 선택입니다.

Also, you should verify that your `users` (or equivalent) table contains a nullable, string `remember_token` column of 100 characters. This column will be used to store a token for users that select the "remember me" option when logging into your application.

또한 `users` (또는 동일한) 테이블이 NULL을 허용하는 100자리 문자열의 `remember_token` 컬럼을 포함하고 있는지 확인하십시오. 이 컬럼은 애플리케이션에 로그인할 때 "remember me" 옵션을 선택한 사용자의 토큰을 저장하는 데 사용됩니다.

<a name="ecosystem-overview"></a>
### Ecosystem Overview
### 생태계 개요

Laravel offers several packages related to authentication. Before continuing, we'll review the general authentication ecosystem in Laravel and discuss each package's intended purpose.

라라벨은 인증과 관련된 여러 패키지를 제공합니다. 계속하기 전에 Laravel의 일반 인증 생태계를 검토하고 각 패키지의 의도된 목적에 대해 논의할 것입니다.

First, consider how authentication works. When using a web browser, a user will provide their username and password via a login form. If these credentials are correct, the application will store information about the authenticated user in the user's [session](/docs/{{version}}/session). A cookie issued to the browser contains the session ID so that subsequent requests to the application can associate the user with the correct session. After the session cookie is received, the application will retrieve the session data based on the session ID, note that the authentication information has been stored in the session, and will consider the user as "authenticated".

먼저 인증 작동 방식을 고려하십시오. 웹 브라우저를 사용할 때 사용자는 로그인 form을 통해 사용자 이름과 비밀번호를 제공합니다. 이러한 자격 증명이 올바르면 애플리케이션은 인증된 사용자에 대한 정보를 사용자의 [세션](/docs/{{version}}/session)에 저장합니다. 브라우저에 발행된 쿠키에는 애플리케이션에 대한 후속 요청이 사용자를 올바른 세션과 연결할 수 있도록 세션 ID가 포함됩니다. 세션 쿠키가 수신된 후 애플리케이션은 세션 ID를 기반으로 세션 데이터를 검색하고 인증 정보가 세션에 저장되었음을 확인하고 사용자를 "인증된"것으로 간주합니다.

When a remote service needs to authenticate to access an API, cookies are not typically used because there is no web browser. Instead, the remote service sends an API token to the API on each request. The application may validate the incoming token against a table of valid API tokens and "authenticate" the request as being performed by the user associated with that API token.

원격 서비스가 API에 액세스하기 위해 인증해야 하는 경우 웹 브라우저가 없기 때문에 일반적으로 쿠키가 사용되지 않습니다. 대신 원격 서비스는 각 요청에서 API 토큰을 API에 보냅니다. 애플리케이션은 유효한 API 토큰 테이블에 대해 들어오는 토큰의 유효성을 검사하고 해당 API 토큰과 관련된 사용자가 수행하는 요청을 "인증"할 수 있습니다.

#### Laravel's Built-in Browser Authentication Services
#### 라라벨의 내장 브라우져 인증 서비스

Laravel includes built-in authentication and session services which are typically accessed via the `Auth` and `Session` facades. These features provide cookie based authentication for requests that are initiated from web browsers. They provide methods that allow you to verify a user's credentials and authenticate the user. In addition, these services will automatically store the proper data in the user's session and issue the proper session cookie. A discussion of how to use these services is contained within this documentation.

라라벨에는 일반적으로`Auth` 및`Session` 파사드를 통해 액세스되는 내장 인증 및 세션 서비스가 포함되어 있습니다. 이러한 기능은 웹 브라우저에서 시작된 요청에 대해 쿠키 기반 인증을 제공합니다. 사용자의 자격 증명을 확인하고 사용자를 인증 할 수 있는 방법을 제공합니다. 또한 이러한 서비스는 사용자 세션에 적절한 데이터를 자동으로 저장하고 적절한 세션 쿠키를 발행합니다. 이러한 서비스를 사용하는 방법에 대한 설명이 이 문서에 포함되어 있습니다.

**Jetstream / Fortify**
**Jetstream / Fortify**

As discussed in this documentation, you can interact with these authentication services manually to build your application's own authentication layer. However, to help you get started more quickly, we have released free packages that provide robust, modern scaffolding of the entire authentication layer. These packages are [Laravel Jetstream](https://jetstream.laravel.com) and [Laravel Fortify](https://github.com/laravel/fortify).

이 문서에 설명된 대로 이러한 인증 서비스와 수동으로 상호 작용하여 애플리케이션의 고유한 인증 계층을 구축 할 수 있습니다. 그러나 보다 빠르게 시작할 수 있도록 전체 인증 계층에 대한 강력하고 현대적인 스캐폴딩을 제공하는 무료 패키지를 출시했습니다. 이러한 패키지는 [Laravel Jetstream](https://jetstream.laravel.com) 및 [Laravel Fortify](https://github.com/laravel/fortify) 입니다.

Laravel Fortify is a headless authentication backend for Laravel that implements many of the features found in this documentation, including cookie-based authentication as well as other features such as two-factor authentication and email verification. Laravel Jetstream is a UI that consumes and exposes Fortify's authentication services with a beautiful, modern UI powered by [Tailwind CSS](https://tailwindcss.com), [Laravel Livewire](https://laravel-livewire.com), and / or [Inertia.js](https://inertiajs.com). Laravel Jetstream, in addition to offering browser-based cookie authentication, includes built-in integration with Laravel Sanctum to offer API token authentication. Laravel's API authentication offerings are discussed below.

라라벨 Fortify는 쿠키 기반 인증은 물론 2단계 인증 및 이메일 확인과 같은 기타 기능을 포함하여 이 문서에 있는 많은 기능을 구현하는 라라벨용 헤드리스 인증 백엔드입니다. 라라벨 Jetstream은 [Tailwind CSS](https://tailwindcss.com), [Laravel Livewire](https://laravel-livewire.com), [Inertia.js](https://inertiajs.com) 에서 제공하는 아름답고 현대적인 UI로 Fortify의 인증 서비스를 이용하고 노출하는 UI입니다. 라라벨 Jetstream은 브라우저 기반 쿠키 인증을 제공하는 것 외에도 라라벨 Sanctum과의 통합하여 API 토큰 인증을 제공합니다. 라라벨에서 제공하는 API 인증은 아래에서 설명합니다.

#### Laravel's API Authentication Services
#### 라라벨 API 인증 서비스

Laravel provides two optional packages to assist you in managing API tokens and authenticating requests made with API tokens: [Passport](/docs/{{version}}/passport) and [Sanctum](/docs/{{version}}/sanctum). Please note that these libraries and Laravel's built-in cookie based authentication libraries are not mutually exclusive. These libraries primarily focus on API token authentication while the built-in authentication services focus on cookie based browser authentication. Many applications will use both Laravel's built-in cookie based authentication services and one of Laravel's API authentication packages.

라라벨은 API 토큰을 관리하고 API 토큰으로 요청을 인증하는데 도움이 되는 두 가지 선택 가능한 패키지 [Passport](/docs/{{version}}/passport)와 [Sanctum](/docs/{{version}}/sanctum)을 제공합니다. 이러한 라이브러리와 Laravel의 내장 쿠키 기반 인증 라이브러리는 상호 배타적이지 않습니다. 이러한 라이브러리는 주로 API 토큰 인증에 중점을 두고 있으며 기본 제공 인증 서비스는 쿠키 기반 브라우저 인증에 중점을 둡니다. 많은 애플리케이션이 라라벨의 내장 쿠키 기반 인증 서비스와 라라벨의 API 인증 패키지를 모두 사용합니다.

**Passport**
**Passport**

Passport is an OAuth2 authentication provider, offering a variety of OAuth2 "grant types" which allow you to issue various types of tokens. In general, this is a robust and complex package for API authentication. However, most applications do not require the complex features offered by the OAuth2 spec, which can be confusing for both users and developers. In addition, developers have been historically confused about how to authenticate SPA applications or mobile applications using OAuth2 authentication providers like Passport.

Passport는 다양한 유형의 토큰을 발급 할 수 있는 다양한 OAuth2 "grant types"을 제공하는 OAuth2 인증 공급자입니다. 일반적으로 이것은 API 인증을 위한 강력하고 복잡한 패키지입니다. 그러나 대부분의 애플리케이션에는 OAuth2 사양에서 제공하는 복잡한 기능이 필요하지 않으므로 사용자와 개발자 모두에게 혼란을 줄 수 있습니다. 또한, 개발자는 Passport와 같은 OAuth2 인증 공급자를 사용하여 SPA 애플리케이션 또는 모바일 애플리케이션을 인증하는 방법에 대해 그동안 혼란스러웠습니다.

**Sanctum**
**Sanctum**

In response to the complexity of OAuth2 and developer confusion, we set out to build a simpler, more streamlined authentication package that could handle both first-party web requests from a web browser and API requests via tokens. This goal was realized with the release of [Laravel Sanctum](/docs/{{version}}/sanctum), which should be considered the preferred and recommended authentication package for applications that will be offering a first-party web UI in addition to an API, or will be powered by a single-page application that exists separately from the backend Laravel application, or applications that offer a mobile client.

OAuth2의 복잡성과 개발자 혼란에 대응하여, 웹 브라우저의 자사 웹 요청과 토큰을 통한 API 요청을 모두 처리 할 수 있는 더 간단하고 간소화된 인증 패키지를 구축하기 시작했습니다. 이 목표는 [Laravel Sanctum](/docs/{{version}}/sanctum)의 출시와 함께 실현되었습니다. 이 패키지는 다음과 함께 자사 웹 UI를 제공 할 애플리케이션에 대해 선호되고 권장되는 인증 패키지로 간주되어야 합니다. API 또는 백엔드 라라벨 애플리케이션과 별도로 존재하는 SPA (단일 페이지 애플리케이션) 또는 모바일 클라이언트를 제공하는 애플리케이션에 의해 구동됩니다.

Laravel Sanctum is a hybrid web / API authentication package that can manage your application's entire authentication process. This is possible because when Sanctum based applications receive a request, Sanctum will first determine if the request includes a session cookie that references an authenticated session. Sanctum accomplishes this by calling Laravel's built-in authentication services which we discussed earlier. If the request is not being authenticated via a session cookie, Sanctum will inspect the request for an API token. If an API token is present, Sanctum will authenticate the request using that token. To learn more about this process, please consult Sanctum's ["how it works"](/docs/{{version}}/sanctum#how-it-works) documentation.

Laravel Sanctum은 애플리케이션의 전체 인증 프로세스를 관리 할 수 있는 하이브리드 웹 / API 인증 패키지입니다. 이는 Sanctum 기반 애플리케이션이 요청을 수신할 때 Sanctum이 먼저 요청에 인증된 세션을 참조하는 세션 쿠키가 포함되어 있는지 확인하기 때문에 가능합니다. Sanctum은 앞서 논의한 라라벨의 내장 인증 서비스를 호출하여 이를 수행합니다. 요청이 세션 쿠키를 통해 인증되지 않는 경우 Sanctum은 요청에서 API 토큰을 검사합니다. API 토큰이 있는 경우 Sanctum은 해당 토큰을 사용하여 요청을 인증합니다. 이 프로세스에 대한 자세한 내용은 Sanctum의 ["작동 방식"](/docs/{{version}}/sanctum#how-it-works) 문서를 참조하십시오.

Laravel Sanctum is the API package we have chosen to include with the [Laravel Jetstream](https://jetstream.laravel.com) authentication scaffolding because we believe it is the best fit for the majority of web application's authentication needs.

Laravel Santum은 웹 애플리케이션 인증에 가장 적합하다고 판단되어 [Laravel Jetstream](https://jetstream.laravel.com)의 인증 스캐폴딩에 포함하기로 선택한 API 패키지 입니다.
 
#### Summary & Choosing Your Stack
#### 요약 & 스택 선택

In summary, if your application will be accessed using a browser, your application will use Laravel's built-in authentication services.

요약하면, 브라우저를 사용하여 애플리케이션에 접근할 경우 애플리케이션은 라라벨의 기본 제공 인증 서비스를 사용할 수 있습니다.

Next, if your application offers an API, you will choose between [Passport](/docs/{{version}}/passport) or [Sanctum](/docs/{{version}}/sanctum) to provide API token authentication for your application. In general, Sanctum should be preferred when possible since it is a simple, complete solution for API authentication, SPA authentication, and mobile authentication, including support for "scopes" or "abilities".

다음으로, 애플리케이션이 API를 제공하는 경우 [Passport](/docs/{{version}}/passport) 또는 [Sanctum](/docs/{{version}}/Sanctum) 중에서 선택하여 API 토큰 인증을 제공합니다. 일반적으로 Sanctum은 API 인증, SPA 인증, 모바일 인증을 위한 것으로, 해당 인증의 "범위" 나 "기능성"에 대한 지원을 포함하는 간편하고 완벽한 솔루션이기 때문에, 가능한 한 채택되어야 합니다.

Passport may be chosen when your application absolutely needs all of the features provided by the OAuth2 specification.

애플리케이션에 OAuth2 사양에서 제공하는 모든 기능이 절대적으로 필요한 경우 Passport를 선택할 수 있습니다.

And, if you would like to get started quickly, we are pleased to recommend [Laravel Jetstream](https://jetstream.laravel.com) as a quick way to start a new Laravel application that already uses our preferred authentication stack of Laravel's built-in authentication services and Laravel Sanctum.

만약 당신이 빠르게 시작하고자 한다면, 우리는 새로운 라라벨 애플리케이션을 시작하는 빠른 방법으로 라라벨의 내장(built-in) 인증 서비스와 Laravel Sanctum으로 이루어진 선호도 높은 인증 스택을 이미 사용하는 [Laravel Jetstream](https://jetstream.laravel.com)을 흔쾌히 추천합니다.

<a name="authentication-quickstart"></a>
## Authentication Quickstart
## 빠르게 인증 살펴보기

> {note} This portion of the documentation discusses authenticating users via the [Laravel Jetstream](https://jetstream.laravel.com) package, which includes UI scaffolding to help you get started quickly. If you would like to integrate with Laravel's authentication systems directly, check out the documentation on [manually authenticating users](#authenticating-users).

> {참고} 문서의이 부분에서는 빠르게 시작할 수 있도록 UI 스캐폴딩이 포함된 [Laravel Jetstream](https://jetstream.laravel.com) 패키지를 통한 사용자 인증에 대해 설명합니다. 라라벨 인증 시스템과 직접 통합하려면 [수동 인증 사용자](#authenticating-users) 문서를 확인하세요.

<a name="included-routing"></a>
### Routing
### Routing-라우팅

Laravel's `laravel/jetstream` package provides a quick way to scaffold all of the routes, views, and other backend logic needed for authentication using a few simple commands:

Laravel의 `laravel/jetstream` 패키지는 몇 가지 간단한 명령을 사용하여 인증에 필요한 모든 경로, view 및 기타 백엔드 로직을 스캐폴딩하는 빠른 방법을 제공합니다.

    composer require laravel/jetstream

    // Install Jetstream with the Livewire stack...
    php artisan jetstream:install livewire

    // Install Jetstream with the Inertia stack...
    php artisan jetstream:install inertia

This command should be used on fresh applications and will install a layout view, registration and login views, as well as routes for all authentication end-points. A `/dashboard` route will also be generated to handle post-login requests to your application's dashboard.

이 명령은 새로운 애플리케이션에서 사용해야 하며 레이아웃 view, 등록 및 로그인 view는 물론 모든 인증 end-point 에 대한 경로를 설치합니다. 애플리케이션 대시 보드에 대한 로그인 후 요청을 처리하기 위해`/dashboard` 경로도 생성됩니다.

#### Creating Applications Including Authentication
#### 인증을 포함한 애플리케이션 만들기

If you are starting a brand new application and would like to include the authentication scaffolding, you may use the `--jet` directive when creating your application via the Laravel Installer. This command will create a new application with all of the authentication scaffolding compiled and installed:

새로운 애플리케이션을 시작하고 인증 스캐폴딩을 포함하려면 라라벨 설치 프로그램을 통해 애플리케이션을 만들 때 `--jet` 지시문을 사용할 수 있습니다. 이 명령은 모든 인증 스캐폴딩이 컴파일되고 설치된 새 애플리케이션을 만듭니다.

    laravel new kitetail --jet

>{tip} To learn more about Jetstream, please visit the official [Jetstream documentation](https://jetstream.laravel.com).

>{tip} Jetstream에 대해 자세히 알아 보려면 공식사이트에 방문하세요. [Jetstream documentation](https://jetstream.laravel.com).

<a name="included-views"></a>
### Views
### Views-뷰

As mentioned in the previous section, the `laravel/jetstream` package's `php artisan jetstream:install` command will create all of the views you need for authentication and place them in the `resources/views/auth` directory.

이전 섹션에서 언급했듯이 `laravel/jetstream` 패키지의 `php artisan jetstream:install` 명령은 인증에 필요한 모든 view 를 생성하고 `Resources/views/auth` 디렉토리에 배치합니다.

Jetstream will also create a `resources/views/layouts` directory containing a base layout for your application. All of these views use the [Tailwind CSS](https://tailwindcss.com) framework, but you are free to customize them however you wish.

Jetstream은 애플리케이션의 기본 레이아웃을 포함하는 `resources/views/layouts` 디렉토리도 만듭니다. 이러한 모든 view는 [Tailwind CSS](https://tailwindcss.com) 프레임 워크를 사용하지만 원하는 대로 자유롭게 맞춤 설정할 수 있습니다.

<a name="included-authenticating"></a>
### Authenticating
### 인증하기

Now that your application has been scaffolded for authentication, you are ready to register and authenticate! You may simply access your application in a browser since Jetstream's authentication controllers already contain the logic to authenticate existing users and store new users in the database.

이제 애플리케이션이 인증을 위해 스캐폴딩 되었으므로 등록 및 인증 할 준비가 되었습니다! Jetstream의 인증 컨트롤러에는 이미 기존 사용자를 인증하고 데이터베이스에 새 사용자를 저장하는 로직이 포함되어 있음으로 브라우저에서 애플리케이션에 액세스하면 됩니다.

#### Path Customization
#### 리다이렉트 경로 수정하기

When a user is successfully authenticated, they will typically be redirected to the `/home` URI. You can customize the post-authentication redirect path using the `HOME` constant defined in your `RouteServiceProvider`:

사용자가 성공적으로 인증되면 일반적으로 `/home` URI로 이동됩니다. `RouteServiceProvider`에 정의 된 `HOME` 상수를 사용하여 인증 후 이동되는 경로를 수정할 수 있습니다.

    public const HOME = '/home';

When using Laravel Jetstream, the Jetstream installation process will change the value of the `HOME` constant to `/dashboard`.

Laravel Jetstream을 사용할 때 Jetstream 설치 프로세스는 `HOME` 상수를 `/dashboard` 로 변경합니다.

<a name="retrieving-the-authenticated-user"></a>
### Retrieving The Authenticated User
### 승인된 사용자 조회하기

While handling an incoming request, you may access the authenticated user via the `Auth` facade:

들어오는 요청을 처리하는 동안 `Auth` 파사드를 통해 인증된 사용자에 액세스 할 수 있습니다.

    use Illuminate\Support\Facades\Auth;

    // Get the currently authenticated user...
    $user = Auth::user();

    // Get the currently authenticated user's ID...
    $id = Auth::id();

Alternatively, once a user is authenticated, you may access the authenticated user via an `Illuminate\Http\Request` instance. Remember, type-hinted classes will automatically be injected into your controller methods. By type-hinting the `Illuminate\Http\Request` object, you may gain convenient access to the authenticated user from any controller method in your application:

또는, 사용자가 인증되면, `Illuminate\http\Request` 인스턴스를 통해 인증된 사용자에게 접근할 수 있습니다. 타입힌트된 클래스는 컨트롤러 메서드에 자동으로 주입된다는 것을 기억하십시오. `Illuminate\http\Request` 개체를 타입힌트하면 애플리케이션의 모든 컨트롤러 메서드에서 인증된 사용자에게 편리하게 접근할 수 있습니다.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;

    class FlightController extends Controller
    {
        /**
         * Get a list of all available flights.
         *
         * @param  Request  $request
         * @return Response
         */
        public function update(Request $request)
        {
            // $request->user() returns an instance of the authenticated user...
        }
    }

#### Determining If The Current User Is Authenticated
#### 현재 사용자의 승인 여부 결정하기

To determine if the user is already logged into your application, you may use the `check` method on the `Auth` facade, which will return `true` if the user is authenticated:

사용자가 이미 애플리케이션에 로그인했는지 판별하려면, `Auth` 파사드의 `check` 메소드를 사용할 수 있습니다. 사용자가 인증되었다면 `true`를 반환합니다.

    use Illuminate\Support\Facades\Auth;

    if (Auth::check()) {
        // The user is logged in...
    }

> {tip} Even though it is possible to determine if a user is authenticated using the `check` method, you will typically use a middleware to verify that the user is authenticated before allowing the user access to certain routes / controllers. To learn more about this, check out the documentation on [protecting routes](/docs/{{version}}/authentication#protecting-routes).

> {tip} 사용자가 인증되었는지 `check` 함수로 판단할 수 있긴 해도, 여러분은 사용자의 특정 라우트 / 컨트롤러 접근을 허용하기 전에 그 사용자가 인증 되었는지 확인할 때 보통 미들웨어를 사용하게 될 것입니다. 이에 대한 자세한 내용은 [라우트 보호하기](/docs/{{version}}/authentication#protecting-routes) 문서를 확인하십시오.

<a name="protecting-routes"></a>
### Protecting Routes
### 라우트 보호하기

[Route middleware](/docs/{{version}}/middleware) can be used to only allow authenticated users to access a given route. Laravel ships with an `auth` middleware, which references the `Illuminate\Auth\Middleware\Authenticate` class. Since this middleware is already registered in your HTTP kernel, all you need to do is attach the middleware to a route definition:

[라우트 미들웨어](/docs/{{version}}/middleware)는 인증된 사용자에게만 주어진 라우트에 접근하도록 허용하는데 사용될 수 있습니다. 라라벨은 `Illuminate\Auth\Middleware\Authenticate` 클래스를 참조하는 `auth` 미들웨어를 제공하고 있습니다. 이제 여러분이 할 일은 라우트가 정의된 부분에 미들웨어를 추가하는 것 뿐입니다.

    Route::get('flights', function () {
        // Only authenticated users may enter...
    })->middleware('auth');

#### Redirecting Unauthenticated Users
#### 인증되지 않는 사용자 리다이렉팅하기

When the `auth` middleware detects an unauthorized user, it will redirect the user to the `login` [named route](/docs/{{version}}/routing#named-routes). You may modify this behavior by updating the `redirectTo` function in your `app/Http/Middleware/Authenticate.php` file:

사용자가 인증되지 않은 경우, `auth` 미들웨어는 `login` [이라는 이름이 지정된 라우트](/docs/{{version}}/routing#named-routes) 으로 사용자를 리다이렉트 됩니다. `app/Http/Middleware/Authenticate.php` 파일의 `redirectTo` 함수를 수정해서 이 동작을 변경 할 수 있습니다.

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

#### Specifying A Guard
#### Guard 지정하기

When attaching the `auth` middleware to a route, you may also specify which guard should be used to authenticate the user. The guard specified should correspond to one of the keys in the `guards` array of your `auth.php` configuration file:

`auth` 미들웨어를 라우트에 추가할 때, 여러분은 또한 어떤 guard가 인증에 사용되어야 하는지 지정할 수 있습니다. 지정된 guard는 `auth.php` 설정 파일의 `guards` 배열에 있는 키 중 하나와 일치해야 합니다.

    Route::get('flights', function () {
        // Only authenticated users may enter...
    })->middleware('auth:api');

<a name="login-throttling"></a>
### Login Throttling
### 로그인 횟수 제한

If you are using Laravel Jetstream, rate limiting will automatically be applied to login attempts. By default, the user will not be able to login for one minute if they fail to provide the correct credentials after several attempts. The throttling is unique to the user's username / e-mail address and their IP address.

Laravel Jetstream을 사용하는 경우 로그인 시도에 속도 제한이 자동으로 적용됩니다. 기본적으로 사용자는 몇 번의 시도 후에도 올바른 자격 증명을 제공하지 않으면 1분 동안 로그인할 수 없습니다. 제한은 사용자의 사용자 이름 / 이메일 및 IP 주소에 대해 고유하게 동작합니다.

> {tip} If you would like to rate limit your own routes, check out the [rate limiting documentation](/docs/{{version}}/routing#rate-limiting).

> {tip} 자신의 경로를 속도 제한하려면 [속도 제한 문서](/docs/{{version}}/routing#rate-limiting)를 확인하세요.

<a name="authenticating-users"></a>
## Manually Authenticating Users
## 수동으로 사용자 인증하기

You are not required to use the authentication scaffolding included with Laravel Jetstream. If you choose to not use this scaffolding, you will need to manage user authentication using the Laravel authentication classes directly. Don't worry, it's a cinch!

Laravel Jetstream에 포함된 인증 스캐폴딩을 꼭 사용할 필요는 없습니다. 이 스캐폴딩을 사용하지 않기로 선택한 경우 인증 라라벨 클래스를 직접 사용하여 사용자 인증을 관리해야 합니다. 걱정하지 마세요.

We will access Laravel's authentication services via the `Auth` [facade](/docs/{{version}}/facades), so we'll need to make sure to import the `Auth` facade at the top of the class. Next, let's check out the `attempt` method:

라라벨의 인증 서비스를 `Auth` [파사드](/docs/{{version}}/facades)를 통해 접근할 것이기 때문에 클래스의 가장 상단 부분에 `Auth` 파사드를 사용하도록 선언하는 것이 필수입니다. 다음은 `attempt` 메소드를 확인해 보겠습니다.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Auth;

    class LoginController extends Controller
    {
        /**
         * Handle an authentication attempt.
         *
         * @param  \Illuminate\Http\Request $request
         *
         * @return Response
         */
        public function authenticate(Request $request)
        {
            $credentials = $request->only('email', 'password');

            if (Auth::attempt($credentials)) {
                // Authentication passed...
                return redirect()->intended('dashboard');
            }
        }
    }

The `attempt` method accepts an array of key / value pairs as its first argument. The values in the array will be used to find the user in your database table. So, in the example above, the user will be retrieved by the value of the `email` column. If the user is found, the hashed password stored in the database will be compared with the `password` value passed to the method via the array. You should not hash the password specified as the `password` value, since the framework will automatically hash the value before comparing it to the hashed password in the database. If the two hashed passwords match an authenticated session will be started for the user.

`attempt` 메소드는 키 / 값의 쌍으로 이루어진 배열을 첫번째 인자로 전달 받습니다. 배열의 값들은 데이터베이스 테이블에서 사용자를 찾는데 사용될 것입니다. 따라서 위의 예제에서는, `email` 컬럼을 통해서 사용자를 찾게됩니다. 사용자를 찾았다면, 해시 처리되어 데이터베이스에 저장된 패스워드와 메소드에 전달받은 배열의 `password` 값을 비교할 것입니다. 프레임워크가 데이터베이스에서 해시 처리된 암호와 비교하기 전에 값을 자동으로 해시처리 하기 때문에, `password` 로 지정된 값을 해시처리해서는 안됩니다. 두개의 해시처리된 패스워드가 일치한다면 해당 사용자의 새로운 인증 세션이 시작됩니다.

The `attempt` method will return `true` if authentication was successful. Otherwise, `false` will be returned.

`attempt` 메소드는 인증이 성공하면 `true` 를 반환합니다. 실패 시 `false` 를 반환합니다.

The `intended` method on the redirector will redirect the user to the URL they were attempting to access before being intercepted by the authentication middleware. A fallback URI may be given to this method in case the intended destination is not available.

리다이렉터의 `intended` 메소드는 사용자가 인증 필터에 잡히기 전에 원래 엑세스 하려고 시도했었던 URL로 사용자를 리다이렉트 시킵니다. 해당 리다이렉트가 불가능한 경우 주어진 대체 URI를 메소드에 지정할 수 있습니다.

#### Specifying Additional Conditions
#### 추가적인 조건 지정하기

If you wish, you may also add extra conditions to the authentication query in addition to the user's e-mail and password. For example, we may verify that user is marked as "active":

원한다면, 사용자의 이메일과 패스워드 외에도 부가적인 조건들을 인증 쿼리에 추가할 수 있습니다. 예를 들어 사용자가 "활성화"로 표시되어있는지 확인할 수 있습니다.

    if (Auth::attempt(['email' => $email, 'password' => $password, 'active' => 1])) {
        // The user is active, not suspended, and exists.
    }

> {note} In these examples, `email` is not a required option, it is merely used as an example. You should use whatever column name corresponds to a "username" in your database.

> {note} 이 예제에서, `email` 은 필수 옵션이 아니라, 그냥 예제에서 사용된것입니다. 여러분은 데이터베이스 안에 있는 "username"과 일치하는 어떠한 컬럼 이름을 사용해야 합니다.

#### Accessing Specific Guard Instances
#### 지정된 Guard 인스턴스에 엑세스하기

You may specify which guard instance you would like to utilize using the `guard` method on the `Auth` facade. This allows you to manage authentication for separate parts of your application using entirely separate authenticatable models or user tables.

여러분은 `Auth` 파사드에 `guard` 메소드를 사용하여 어떤 guard 인스턴스를 사용하고자 하는지 지정할 수 있습니다. 이것은 여러분이 완전히 분리된 인증 모델 또는 사용자 테이블을 사용하여 애플리케이션의 개별 파트에 대한 인증을 관리할 수 있도록 합니다.

The guard name passed to the `guard` method should correspond to one of the guards configured in your `auth.php` configuration file:

`guard` 메소드에 전달되는 guard의 이름은 `auth.php` 설정 파일에 설정된 guard 중 하나와 일치 해야합니다.

    if (Auth::guard('admin')->attempt($credentials)) {
        //
    }

#### Logging Out
#### 로그아웃

To log users out of your application, you may use the `logout` method on the `Auth` facade. This will clear the authentication information in the user's session:

애플리케이션에서 사용자를 로그아웃 시키려면 `Auth` 파사드의 `logout` 메소드를 사용하면 됩니다. 그러면 사용자 세션에서 인증 정보가 제거될 것입니다.

    Auth::logout();

<a name="remembering-users"></a>
### Remembering Users
### 사용자 기억하기

If you would like to provide "remember me" functionality in your application, you may pass a boolean value as the second argument to the `attempt` method, which will keep the user authenticated indefinitely, or until they manually logout. Your `users` table must include the string `remember_token` column, which will be used to store the "remember me" token.

여러분의 애플리케이션에 "기억하기" 기능을 제공하고자 한다면, `attempt` 메소드의 두번째 인자로 사용자의 인증을 무기한 계속 유지할지, 아니면 수동으로 로그아웃 할때까지 유지할지 결정하는 boolean 값을 전달하면 됩니다. 이를 위해서는 `users` 테이블은 "기억하기" 토근을 저장하는데 사용되는 `remember_token` 컬럼을 가지고 있어야만 합니다.

    if (Auth::attempt(['email' => $email, 'password' => $password], $remember)) {
        // The user is being remembered...
    }

If you are "remembering" users, you may use the `viaRemember` method to determine if the user was authenticated using the "remember me" cookie:

여러분이 "remember"하는 사용자라면, `viaRemember` 메소드를 통해 해당 사용자가 "remember me" 쿠키로 인증이 되었는 지 확인 할 수 있습니다.

    if (Auth::viaRemember()) {
        //
    }

<a name="other-authentication-methods"></a>
### Other Authentication Methods
### 그 외 인증 메소드

#### Authenticate A User Instance
#### 사용자 인스턴스를 통해서 인증하기

If you need to log an existing user instance into your application, you may call the `login` method with the user instance. The given object must be an implementation of the `Illuminate\Contracts\Auth\Authenticatable` [contract](/docs/{{version}}/contracts). The `App\Models\User` model included with Laravel already implements this interface. This method of authentication is useful when you already have a valid user instance, such as directly after a user registers with your application:

기존 사용자 인스턴스를 애플리케이션에 로그인해야 하는 경우 사용자 인스턴스와 함께 `login` 메서드를 호출 할 수 있습니다. 주어진 객체는 `Illuminate\Contracts\Auth\Authenticatable` [contract](/docs/{{version}}/contract)의 구현이어야 합니다. 라라벨에 포함된 `App\Models\User` 모델은 이미 이 인터페이스를 구현하고 있습니다. 이 인증 방법은 사용자가 애플리케이션에 등록한 직후와 같이 유효한 사용자 인스턴스가 이미 있는 경우에 유용합니다.

    Auth::login($user);

    // Login and "remember" the given user...
    Auth::login($user, true);

You may specify the guard instance you would like to use:

사용하고자 하는 가드 인스턴스를 지정할 수도 있습니다.

    Auth::guard('admin')->login($user);

#### Authenticate A User By ID
#### ID를 통해서 사용자 인증하기

To log a user into the application by their ID, you may use the `loginUsingId` method. This method accepts the primary key of the user you wish to authenticate:

사용자를 ID를 통해 애플리케이션에 로그인 시키려면, `loginUsingId` 메소드를 사용하면 됩니다. 이 메소드는 인증하고자 하는 사용자의 프라이머리 키를 전달 받습니다.

    Auth::loginUsingId(1);

    // Login and "remember" the given user...
    Auth::loginUsingId(1, true);

#### Authenticate A User Once
#### 사용자 인증 한 번 하기

You may use the `once` method to log a user into the application for a single request. No sessions or cookies will be utilized, which means this method may be helpful when building a stateless API:

하나의 request에 대해서 `once` 메소드로 사용자를 로그인시킬 수 있습니다. 세션과 쿠키는 활용되지 않을 것이며 이는 상태를 유지하지 않는 API를 만드는데 도움이 됩니다.

    if (Auth::once($credentials)) {
        //
    }

<a name="http-basic-authentication"></a>
## HTTP Basic Authentication
## HTTP 기본 인증

[HTTP Basic Authentication](https://en.wikipedia.org/wiki/Basic_access_authentication) provides a quick way to authenticate users of your application without setting up a dedicated "login" page. To get started, attach the `auth.basic` [middleware](/docs/{{version}}/middleware) to your route. The `auth.basic` middleware is included with the Laravel framework, so you do not need to define it:

[HTTP 기본 인증](https://en.wikipedia.org/wiki/Basic_access_authentication)은 애플리케이션에 별도의 "login" 페이지 설정없이도 사용자 인증을 할 수 있는 손쉬운 방법을 제공합니다. 이를 위해서는 `auth.basic` [미들웨어](/docs/{{version}}/middleware)를 라우트에 추가하면 됩니다. `auth.basic` 미들웨어는 라라벨에 포함되어 있기 때문에 따로 정의할 필요가 없습니다.

    Route::get('profile', function () {
        // Only authenticated users may enter...
    })->middleware('auth.basic');

Once the middleware has been attached to the route, you will automatically be prompted for credentials when accessing the route in your browser. By default, the `auth.basic` middleware will use the `email` column on the user record as the "username".

미들웨어가 라우트에 추가되면 브라우저에서 라우트에 접근할 때 자동으로 인증 자격을 증명할 것을 요구받을 것입니다. `auth.basic` 미들웨어는 기본적으로 사용자 레코드의 `email` 컬럼을 "username"으로 사용합니다.

#### A Note On FastCGI
#### FastCGI에 대한 참고사항

If you are using PHP FastCGI, HTTP Basic authentication may not work correctly out of the box. The following lines should be added to your `.htaccess` file:

PHP FastCGI를 사용하는 경우, HTTP 기본 인증이 제대로 작동하지 않을 것입니다. 다음을 `.htaccess` 파일에 추가하십시오.

    RewriteCond %{HTTP:Authorization} ^(.+)$
    RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

<a name="stateless-http-basic-authentication"></a>
### Stateless HTTP Basic Authentication
### 상태를 유지하지 않는 HTTP 기본 인증

You may also use HTTP Basic Authentication without setting a user identifier cookie in the session, which is particularly useful for API authentication. To do so, [define a middleware](/docs/{{version}}/middleware) that calls the `onceBasic` method. If no response is returned by the `onceBasic` method, the request may be passed further into the application:

세션에서 사용자 식별자 쿠키를 사용하지 않고 HTTP 기본 인증을 사용할 수도 있습니다. 이것은 API 인증에 특히 유용합니다. 그럴려면 `onceBasic` 메소드를 호출하는 [미들웨어](/docs/{{version}}/middleware)를 정의합니다. `onceBasic`메소드가 응답을 리턴하지 않으면 요청-request가 애플리케이션으로 안쪽으로 전달 될 수 있습니다.

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

다음으로, [라우트 미들웨어를 등록하고](/docs/{{version}}/middleware#registering-middleware) 이를 라우트에 추가하십시오.

    Route::get('api/user', function () {
        // Only authenticated users may enter...
    })->middleware('auth.basic.once');

<a name="logging-out"></a>
## Logging Out
## 로그아웃

To manually log users out of your application, you may use the `logout` method on the `Auth` facade. This will clear the authentication information in the user's session:

애플리케이션에서 사용자를 로그아웃 시키려면, `Auth` 파사드의 `logout` 메소드를 사용하면 됩니다. 이 메소드는 사용자의 세션에서 인증 정보를 삭제할 것입니다.

    use Illuminate\Support\Facades\Auth;

    Auth::logout();

<a name="invalidating-sessions-on-other-devices"></a>
### Invalidating Sessions On Other Devices
### 다른 디바이스의 세션 무효화

Laravel also provides a mechanism for invalidating and "logging out" a user's sessions that are active on other devices without invalidating the session on their current device. Before getting started, you should make sure that the `Illuminate\Session\Middleware\AuthenticateSession` middleware is present and un-commented in your `app/Http/Kernel.php` class' `web` middleware group:

라라벨은 현재 접속한 디바이스의 세션은 유지하면서 다른 디바이스의 사용자 세션을 무효화하고 "로그아웃" 시키는 기능을 제공합니다. 이를 위해서 `app/Http/Kernel.php` 클래스의 `web` 미들웨어 그룹의 코멘트가 해제되었는지 확인하십시오.

    'web' => [
        // ...
        \Illuminate\Session\Middleware\AuthenticateSession::class,
        // ...
    ],

Then, you may use the `logoutOtherDevices` method on the `Auth` facade. This method requires the user to provide their current password, which your application should accept through an input form:

이렇게 하면, `Auth` 파사드의 `logoutOtherDevices` 메소드를 사용할 수 있습니다. 이 메소드를 사용하려면 사용자가 입력폼을 통해서 패스워드를 입력하도록 해야합니다.

    use Illuminate\Support\Facades\Auth;

    Auth::logoutOtherDevices($password);

When the `logoutOtherDevices` method is invoked, the user's other sessions will be invalidated entirely, meaning they will be "logged out" of all guards they were previously authenticated by.

`logoutOtherDevices` 메소드가 호출되면, 사용자의 다른 세션은 완전히 무효화됩니다. 즉, 이전에 인증 된 모든 가드에서 "로그 아웃"됩니다.

> {note} When using the `AuthenticateSession` middleware in combination with a custom route name for the `login` route, you must override the `unauthenticated` method on your application's exception handler to properly redirect users to your login page.

> {note} `login`라우트에 대한 커스텀 라우트 이름과 함께 `AuthenticateSession`미들웨어를 사용하는 경우 사용자를 로그인 페이지로 올바르게 리디렉션하려면 애플리케이션의 예외 처리기에서 `unauthenticated` 메서드를 재정의해야합니다.


<a name="password-confirmation"></a>
## Password Confirmation
## 비밀번호 확인

While building your application, you may occasionally have actions that should require the user to confirm their password before the action is performed. Laravel includes built-in middleware to make this process a breeze. Implementing this feature will require you to define two routes: one route to display a view asking the user to confirm their password, and one route to confirm that the password is valid and redirect the user to their intended destination.

애플리케이션을 구축하는 동안 작업을 수행하기 전에 사용자가 암호를 확인 해야하는 작업이 있을 수 있습니다. 라라벨은 이 프로세스를 쉽게 만들어주는 내장 미들웨어를 포함하고 있습니다. 이 기능을 구현하려면 두 가지 경로를 정의해야 합니다. 하나는 사용자에게 암호 확인을 요청하는 view를 표시하는 경로이고 다른 하나는 암호가 유효한지 확인하고 사용자를 원하는 대상으로 이동하는 경로입니다.

> {tip} The following documentation discusses how to integrate with Laravel's password confirmation features directly; however, if you would like to get started more quickly, the [Laravel Jetstream](https://jetstream.laravel.com) authentication scaffolding package includes support for this feature!

> {Tip} 다음 문서는 라라벨의 비밀번호 확인 기능과 직접 통합하는 방법을 설명합니다. 하지만 더 빨리 시작하려면 [Laravel Jetstream](https://jetstream.laravel.com) 인증 스캐폴딩 패키지에 이 기능에 대한 지원이 포함되어 있습니다!

<a name="password-confirmation-configuration"></a>
### Configuration
### 환경설정

After confirming their password, a user will not be asked to confirm their password again for three hours. However, you may configure the length of time before the user is re-prompted for their password by changing the value of the `password_timeout` configuration value within your `auth` configuration file.

비밀번호를 확인한 후 사용자는 3시간 동안 비밀번호를 다시 확인하지 않아도 됩니다. 그러나 `auth` 설정 파일의 `password_timeout` 값을 변경하여 사용자에게 암호를 다시 묻는 메시지가 표시되기 까지의 시간을 변경할 수 있습니다.

<a name="password-confirmation-routing"></a>
### Routing
### 라우팅

#### The Password Confirmation Form
#### 비밀번호 확인 양식

First, we will define the route that is needed to display a view requesting that the user confirm their password:

먼저 사용자가 자신의 암호를 확인하도록 요청하는 view를 표시하는 데 필요한 경로를 정의합니다.

    Route::get('/confirm-password', function () {
        return view('auth.confirm-password');
    })->middleware(['auth'])->name('password.confirm');

As you might expect, the view that is returned by this route should have a form containing a `password` field. In addition, feel free to include text within the view that explains that the user is entering a protected area of the application and must confirm their password.

예상 할 수 있듯이 이 경로에서 반환되는 view에는 `password` 필드가 포함된 양식이 있어야 합니다. 또한 사용자가 애플리케이션의 보호 영역에 들어가고 있으며 비밀번호를 확인해야 한다는 내용을 view에 자유롭게 포함 할 수 있습니다.

#### Confirming The Password
#### 비밀번호 확인

Next, we will define a route will handle the form request from the "confirm password" view. This route will be responsible for validating the password and redirecting the user to their intended destination:

다음으로 "비밀번호 확인" view에서 요청 양식을 처리 할 경로를 정의합니다. 이 경로는 암호의 유효성을 검사하고 사용자를 원하는 대상으로 이동하는 역할을 합니다.

    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Hash;

    Route::post('/confirm-password', function (Request $request) {
        if (! Hash::check($request->password, $request->user()->password)) {
            return back()->withErrors([
                'password' => ['The provided password does not match our records.']
            ]);
        }

        $request->session()->passwordConfirmed();

        return redirect()->intended();
    })->middleware(['auth', 'throttle:6,1'])->name('password.confirm');

Before moving on, let's examine this route in more detail. First, the request's `password` attribute is determined to actually match the authenticated user's password. If the password is valid, we need to inform Laravel's session that the user has confirmed their password. The `passwordConfirmed` method will set a timestamp in the user's session that Laravel can use to determine when the user last confirmed their password. Finally, we can redirect the user to their intended destination.

계속 진행하기 전에 이 경로를 자세히 살펴보겠습니다. 먼저 요청의 `password` 속성이 인증된 사용자의 비밀번호와 실제로 일치하는지 확인합니다. 암호가 유효하면 사용자가 암호를 확인했음을 라라벨 세션에 알려야 합니다. `passwordConfirmed` 메소드는 사용자가 마지막으로 비밀번호를 확인한 시기를 결정하기 위해 라라벨이 사용할 수 있는 사용자 세션의 타임 스탬프를 설정합니다. 마지막으로 사용자를 의도한 목적지로 이동 할 수 있습니다.

<a name="password-confirmation-protecting-routes"></a>
### Protecting Routes
### 라우트 보호하기

You should ensure that any route that performs an action that should require recent password confirmation is assigned the `password.confirm` middleware. This middleware is included with the default installation of Laravel and will automatically store the user's intended destination in the session so that the user may be redirected to that location after confirming their password. After storing the user's intended destination in the session, the middleware will redirect the user to the `password.confirm` [named route](/docs/{{version}}/routing#named-routes):

최근 암호 확인이 필요한 작업을 수행하는 모든 경로에 `password.confirm` 미들웨어가 할당 되었는지 확인해야 합니다. 이 미들웨어는 라라벨의 기본 설치에 포함되어 있으며 사용자가 암호를 확인한 후 해당 위치로 이동 될 수 있도록 세션에 사용자가 의도한 대상을 자동으로 저장합니다. 세션에 대한 사용자의 의도된 대상을 저장 한 후 미들웨어는 사용자를 `password.confirm` [named route](/docs/{{version}}/routing#named-routes) 으로 이동합니다.

    Route::get('/settings', function () {
        // ...
    })->middleware(['password.confirm']);

    Route::post('/settings', function () {
        // ...
    })->middleware(['password.confirm']);

<a name="adding-custom-guards"></a>
## Adding Custom Guards
## 사용자정의 Guard 추가

You may define your own authentication guards using the `extend` method on the `Auth` facade. You should place this call to `extend` within a [service provider](/docs/{{version}}/providers). Since Laravel already ships with an `AuthServiceProvider`, we can place the code in that provider:

여러분은 `Auth` 파사드의 `extend` 메소드를 사용하여 고유한 인증 guard를 정의할 수 있습니다. 이 메소드는 [서비스 프로바이더](/docs/{{version}}/providers) 중 하나의 `extend`에서 호출해야 합니다. 라라벨은 이미 `AuthServiceProvider`를 통해 제공하고 있으므로, 우린 그 프로바이더 안에 코드만 넣으면 됩니다.

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

As you can see in the example above, the callback passed to the `extend` method should return an implementation of `Illuminate\Contracts\Auth\Guard`. This interface contains a few methods you will need to implement to define a custom guard. Once your custom guard has been defined, you may use this guard in the `guards` configuration of your `auth.php` configuration file:

이 예제에서 볼 수 있듯이, `extend` 메소드에 전달된 콜백은 `Illuminate\Contracts\Auth\Guard` 구현체를 반환해야 합니다. 이 인터페이스는 사용자 정의 guard를 구현하는데 필요한 몇가지 메소드를 가지고 있습니다. 사용자 정의 guard를 정의하고나면, `guards` 설정에서 정의한 guard 를 사용할 수 있습니다.

    'guards' => [
        'api' => [
            'driver' => 'jwt',
            'provider' => 'users',
        ],
    ],

<a name="closure-request-guards"></a>
### Closure Request Guards
### 클로저 형태의 Request Guards

The simplest way to implement a custom, HTTP request based authentication system is by using the `Auth::viaRequest` method. This method allows you to quickly define your authentication process using a single Closure.

인증 시스템을 기반으로한 커스텀 HTTP request-요청을 구현하는 가장간단한 방법은 `Auth::viaRequest` 메소드를 사용하는 것 입니다. 이 메소드는 하나의 클로저를 사용하여 빠르기 인증을 정의할 수 있게 해줍니다.

To get started, call the `Auth::viaRequest` method within the `boot` method of your `AuthServiceProvider`. The `viaRequest` method accepts an authentication driver name as its first argument. This name can be any string that describes your custom guard. The second argument passed to the method should be a Closure that receives the incoming HTTP request and returns a user instance or, if authentication fails, `null`:

시작하려면, `AuthServiceProvider` 의 `boot` 메소드에서 `Auth::viaRequest` 메소드를 호출하면 됩니다. `viaRequest` 메소드는 인증 드라이버 이름을 첫번째 인자로 전달 받습니다. 이 이름은 커스텀 guard를 표현하는 문자열이 될 수도 있습니다. 메소드에 전달되는 두번째 인자는 유입되는 HTTP request-요청을 전달받아 사용자 인스턴스를 반환하는 클로저여야하며, 인증에 실패한다면, `null` 을 반환해야 합니다.

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

        Auth::viaRequest('custom-token', function ($request) {
            return User::where('token', $request->token)->first();
        });
    }

Once your custom authentication driver has been defined, you use it as a driver within `guards` configuration of your `auth.php` configuration file:

커스텀 인증 드라이버 를 정의했다면, `auth.php` 설정 파일의 `guards` 설정 안에서 사용할 수 있습니다.

    'guards' => [
        'api' => [
            'driver' => 'custom-token',
        ],
    ],

<a name="adding-custom-user-providers"></a>
## Adding Custom User Providers
## 사용자 정의 User 프로바이더 추가하기

If you are not using a traditional relational database to store your users, you will need to extend Laravel with your own authentication user provider. We will use the `provider` method on the `Auth` facade to define a custom user provider:

사용자들을 저장하는 데 전통적인 관계형 데이터베이스를 이용하지 않는다면 사용자 정의 User 프로바이더를 통해 라라벨을 확장해야 합니다. 우린 사용자 정의 User 프로바이더를 정의하기 위해 `Auth` 파사드의 `provider` 메소드를 사용할 것입니다.

    <?php

    namespace App\Providers;

    use App\Extensions\RiakUserProvider;
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

            Auth::provider('riak', function ($app, array $config) {
                // Return an instance of Illuminate\Contracts\Auth\UserProvider...

                return new RiakUserProvider($app->make('riak.connection'));
            });
        }
    }

After you have registered the provider using the `provider` method, you may switch to the new user provider in your `auth.php` configuration file. First, define a `provider` that uses your new driver:

`provider` 메소드로 프로바이더를 등록한 뒤, `auth.php` 설정 파일에서 새 User 프로바이더로 변경할 수 있습니다. 먼저 새로운 드라이버를 사용하는 `provider`를 정의합시다.

    'providers' => [
        'users' => [
            'driver' => 'riak',
        ],
    ],

Finally, you may use this provider in your `guards` configuration:

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

The `Illuminate\Contracts\Auth\UserProvider` implementations are only responsible for fetching a `Illuminate\Contracts\Auth\Authenticatable` implementation out of a persistent storage system, such as MySQL, Riak, etc. These two interfaces allow the Laravel authentication mechanisms to continue functioning regardless of how the user data is stored or what type of class is used to represent it.

`Illuminate\Contracts\Auth\UserProvider`의 구현체는 MySQL, Riak 등의 지속적인 스토리지 시스템에서 `Illuminate\Contracts\Auth\Authenticatable`을 불러오는 것만 담당합니다. 이 두 인터페이스는 사용자 데이터가 어떻게 저장되었는지 또는 이를 위해 어떤 클래스가 사용되고 있는지 여부에 상관없이 라라벨 인증 메커니즘이 작동하도록 해줍니다.

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

`retrieveById` 함수는 일반적으로 MySQL 데이터베이스의 자동 증가 ID와 같은 사용자를 대표하는 키를 전달 받습니다. ID에 상응하는 `Authenticatable` 구현체가 조회되고 그 메소드를 통해 반환됩니다.

The `retrieveByToken` function retrieves a user by their unique `$identifier` and "remember me" `$token`, stored in a field `remember_token`. As with the previous method, the `Authenticatable` implementation should be returned.

`retrieveByToken`와 `remember_token`에 저장된 "remember me" `$token`에 따라 사용자를 조회합니다. 이전의 메소드와 같이 `Authenticatable` 구현체가 반환됩니다.

The `updateRememberToken` method updates the `$user` field `remember_token` with the new `$token`. A fresh token is assigned on a successful "remember me" login attempt or when the user is logging out.

`updateRememberToken` 메소드는 새로운 `$token`으로 `$user`의 `remember_token`필드를 업데이트합니다. 새로운 토큰값은 정상적으로 "remember me"을 통해서 로그인을 시도하기 위해서 할당된 값이거나, 사용자가 로그아웃되었을 때를 위한 값일 수 있습니다.

The `retrieveByCredentials` method receives the array of credentials passed to the `Auth::attempt` method when attempting to sign into an application. The method should then "query" the underlying persistent storage for the user matching those credentials. Typically, this method will run a query with a "where" condition on `$credentials['username']`. The method should then return an implementation of `Authenticatable`. **This method should not attempt to do any password validation or authentication.**

`retrieveByCredentials` 메소드는 애플리케이션에 로그인하려고 시도할 때 `Auth::attempt` 메소드로 전달되는 인증 정보 배열을 전달 받습니다. 그러면 메소드는 그 인증 정보에 맞는 사용자들을 기반에 있는 지속적 저장소(persistent storage)에서 "질의"해야 합니다. 일반적으로 이 메소드는 `$credentials['username']`에 맞는 "where" 쿼리를 실행할 것입니다. 그 뒤에 메소드는 `Authenticatable`의 구현체를 반환해야 합니다. **이 메소드는 패스워드 확인이나 인증을 시도해서는 안됩니다.**

The `validateCredentials` method should compare the given `$user` with the `$credentials` to authenticate the user. For example, this method should probably use `Hash::check` to compare the value of `$user->getAuthPassword()` to the value of `$credentials['password']`. This method should return `true` or `false` indicating on whether the password is valid.

`validateCredentials` 메소드는 사용자를 인증하기 위하여 주어진 `$user`를 `$credentials`에 비교해야 합니다. 예를 들자면, 이 메소드는 `$user->getAuthPassword()`의 값을 `$credentials['password']`와 비교하기 위해 `Hash::check`를 사용하게 될 것 입니다. 이 메소드는 패스워드가 유효한 지를 나타내는 `true` 혹은 `false` 값을 반환합니다.

<a name="the-authenticatable-contract"></a>
### The Authenticatable Contract
### Authenticatable Contract 살펴보기

Now that we have explored each of the methods on the `UserProvider`, let's take a look at the `Authenticatable` contract. Remember, the provider should return implementations of this interface from the `retrieveById`, `retrieveByToken`, and `retrieveByCredentials` methods:

`UserProvider`의 각 메소드에 대해 알아보았으니 이제 `Authenticatable` contract를 살펴 보도록 하겠습니다. 프로바이더는 `retrieveById`, `retrieveByToken`, 그리고 `retrieveByCredentials` 메소드에서 이 인터페이스의 구현을 반환해야 한다는 것을 기억하십시오..

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

This interface is simple. The `getAuthIdentifierName` method should return the name of the "primary key" field of the user and the `getAuthIdentifier` method should return the "primary key" of the user. In a MySQL back-end, again, this would be the auto-incrementing primary key. The `getAuthPassword` should return the user's hashed password. This interface allows the authentication system to work with any User class, regardless of what ORM or storage abstraction layer you are using. By default, Laravel includes a `User` class in the `app` directory which implements this interface, so you may consult this class for an implementation example.

이 인터페이스는 간단합니다. `getAuthIdentifierName` 메소드는 사용자의 "프라이머리 키" 필드의 이름을 반환해야하며 `getAuthIdentifier` 메소드는 사용자의 "프라이머리 키"를 반환합니다. MySQL의 경우 auto-incrementing primary key에 해당합니다. `getAuthPassword`는 사용자의 해시된 패스워드를 반환합니다. 이 인터페이스는 어떤 ORM 혹은 저장소 추상화 계층을 사용하든지에 상관없이 인증 시스템이 어떤 사용자 클래스에도 적용 될수 있도록 해줍니다. 라라벨은 기본적으로 이 인터페이스를 구현하는 `app` 디렉토리에 `User` 클래스를 포함하기 때문에 구현된 예를 보기 위해 이 클래스를 이용면 됩니다.

<a name="events"></a>
## Events
## 이벤트

Laravel raises a variety of [events](/docs/{{version}}/events) during the authentication process. You may attach listeners to these events in your `EventServiceProvider`:

라라벨은 인증 과정 중에 다양한 [이벤트](/docs/{{version}}/events)들을 발생시킵니다. `EventServiceProvider`에서 이 이벤트들을 위한 listener를 추가할 수 있습니다.

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
