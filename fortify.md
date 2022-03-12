# 라라벨 Fortify

- [시작하기](#introduction)
    - [Fortify 란?](#what-is-fortify)
    - [언제 Fortify를 사용해야 합니까?](#when-should-i-use-fortify)
- [설치](#installation)
    - [Fortify 서비스 프로바이더](#the-fortify-service-provider)
    - [Fortify 특징](#fortify-features)
    - [View 비활성화](#disabling-views)
- [인증](#authentication)
    - [User 인증 커스터마이징](#customizing-user-authentication)
    - [인증 파이프라인 커스터마이징](#customizing-the-authentication-pipeline)
    - [리다이렉트 커스터마이징](#customizing-authentication-redirects)
- [2단계 인증](#two-factor-authentication)
    - [2단계 인증 활성화](#enabling-two-factor-authentication)
    - [2단계 인증으로 인증하기](#authenticating-with-two-factor-authentication)
    - [2단계 인증 비활성화](#disabling-two-factor-authentication)
- [등록](#registration)
    - [등록 커스터마이징](#customizing-registration)
- [비밀번호 재설정](#password-reset)
    - [비밀번호 재설정 링크 요청](#requesting-a-password-reset-link)
    - [비밀번호 재설정](#resetting-the-password)
    - [비밀번호 재설정 커스터마이징](#customizing-password-resets)
- [이메일 인증](#email-verification)
    - [라이터 보호](#protecting-routes)
- [비밀번호 재확인](#password-confirmation)

<a name="introduction"></a>
## 시작하기

[Laravel Fortify](https://github.com/laravel/fortify)는 Laravel에서 프론트엔드의 관계 없이 사용할 수 있는 백엔드 인증에 대한 구현입니다. Fortify는 로그인, 등록, 비밀번호 재설정, 이메일 확인 등을 포함하여 Laravel의 모든 인증 기능을 구현하는 데 필요한 경로와 컨트롤러를 등록합니다. Fortify를 설치한 후 `route:list` Artisan 명령을 실행하여 Fortify가 등록한 경로를 볼 수 있습니다.

Fortify는 사용자 인터페이스를 제공하지 않기 때문에 등록된 경로에 대한 요청을 처리하는 사용자 인터페이스와 쌍을 이루도록 되어 있습니다. 이 문서에선 이러한 경로에 요청하는 방법에 대해 정확히 논의할 것입니다.

> {팁}, Fortify는 라라벨의 인증 기능을 구현하기 시작하는 데 도움이 되는 패키지입니다. **반드시 이 패키지를 사용하지 않아도 됩니다.** 이 패키지와 관계없이 언젠든 [authentication](/docs/{{version}}/authentication), [password reset](/docs/{{version}}/passwords)와 [email verification](/docs/{{version}}/verification) 문서에 따라 직접 Laravel 인증 서비스와 자유롭게 사용할 수 있습니다.

<a name="what-is-fortify"></a>
### Fortify 란?

위에서 언급했듯이 Laravel Fortify는 Laravel을 위한 프론트엔드와 관게 없이 사용할 수 있는 백엔드 인증에 대한 구현입니다. Fortify는 로그인, 등록, 비밀번호 재설정, 이메일 확인 등을 포함하여 Laravel의 모든 인증 기능을 구현하는 데 필요한 경로와 컨트롤러를 등록합니다.

**Laravel의 인증 기능을 사용하기 위해 Fortify를 사용할 필요는 없습니다.** 이 패키지와 관계없이 언젠든 [인증](/docs/{{version}}/authentication), [비밀번호 재설정](/docs/{{version}}/passwords), 및 [이메일 인증](/docs/{{version}}/verification) 문서에 따라 직접 Laravel 인증 서비스와 자유롭게 사용할 수 있습니다.

Laravel을 처음 사용하는 경우 Laravel Fortify를 사용하기 전에 [Laravel Breeze](/docs/{{version}}/starter-kits) 애플리케이션 스타터 키트를 살펴보는 것이 좋습니다. Laravel Breeze는 [Tailwind CSS](https://tailwindcss.com)로 구축된 사용자 인터페이스를 포함하는 애플리케이션을 위한 인증 스캐폴딩을 제공합니다. Fortify와 달리 Breeze는 해당 경로와 컨트롤러를 애플리케이션에 직접 추가합니다. 이를 통해 Laravel Fortify가 이러한 기능을 구현하기 전에 Laravel의 인증 기능을 학습하고 익숙해질 수 있습니다.

Laravel Fortify는 본질적으로 Laravel Breeze의 경로와 컨트롤러를 가져와 사용자 인터페이스가 포함되지 않은 상태로 제공합니다. 이를 통해 특정 프론트엔드에 얽매이지 않고 애플리케이션 인증 계층의 백엔드 구현을 빠르게 스캐폴딩할 수 있습니다.

<a name="when-should-i-use-fortify"></a>
### 언제 Fortify를 사용해야 합니까?

언제 Laravel Fortify를 사용하는 것이 적절한지 궁금할 것입니다. 먼저 Laravel의 [애플리케이션 스타터 키트](/docs/{{version}}/starter-kits) 중 하나를 사용하는 경우 Laravel의 모든 애플리케이션 스타터 키트가 이미 전체 인증 구현을 제공하므로 Laravel Fortify를 설치할 필요가 없습니다.

애플리케이션 스타터 키트를 사용하지 않고 애플리케이션에 인증 기능이 필요한 경우 애플리케이션의 인증 기능을 수동으로 구현하거나 Laravel Fortify를 사용하여 이러한 기능의 백엔드 구현을 제공하는 두 가지 옵션이 있습니다.

Fortify를 설치하기로 선택한 경우 사용자 인터페이스는 사용자를 인증하고 등록하기 위해 이 설명서에 자세히 설명된 Fortify의 인증 경로를 요청합니다.

Fortify를 사용하는 대신 Laravel의 인증 서비스와 수동으로 상호 작용하기로 선택한 경우 [인증](/docs/{{version}}/authentication), [password reset](/docs/{{version}}/passwords) 및 [이메일 인증](/docs/{{version}}/verification) 문서에서 제공되는 문서를 따르면 됩니다.

<a name="laravel-fortify-and-laravel-sanctum"></a>
#### Laravel Fortify & Laravel Sanctum

일부 개발자는 [Laravel Sanctum](/docs/{{version}}/sanctum)과 Laravel Fortify의 차이점에 대해 혼란스러워합니다. 두 패키지는 서로 다르지만 관련된 두 가지 문제를 해결하기 때문에 Laravel Fortify와 Laravel Sanctum은 상호 배타적이거나 경쟁적인 패키지가 아닙니다.

Laravel Sanctum은 API 토큰을 관리하고 세션 쿠키 또는 토큰을 사용하여 기존 사용자를 인증하는 데만 관심이 있습니다. Sanctum은 사용자 등록, 비밀번호 재설정 등을 처리하는 경로를 제공하지 않습니다.

API를 제공하거나 단일 페이지 애플리케이션의 백엔드 역할을 하는 애플리케이션에 대한 인증 계층을 수동으로 구축하려는 경우 Laravel Fortify(사용자 등록, 비밀번호 재설정 등) 및 Laravel Sanctum(API 토큰 관리, 세션 인증)를 모두 활용하는 것이 가능합니다.

<a name="installation"></a>
## 설치

시작하려면 Composer 패키지 관리자를 사용하여 Fortify를 설치합니다.

```nothing
composer require laravel/fortify
```

그 다음, Fortify 리소스를 `vendor:publish` 명령어로 publish합니다.

```bash
php artisan vendor:publish --provider="Laravel\Fortify\FortifyServiceProvider"
```

이 명령은 Fortify의 작업을 `app/Actions` 디렉토리에 게시하며, 존재하지 않는 경우 생성됩니다. 또한 Fortify의 구성 파일 및 마이그레이션이 게시됩니다.

그 다음, 데이터베이스를 마이그레이션을 실행합니다.

```bash
php artisan migrate
```

<a name="the-fortify-service-provider"></a>
### Fortify 서비스 프로바이더

위에서 설명한 `vendor:publish` 명령은 `App\Providers\FortifyServiceProvider` 클래스도 게시합니다. 이 클래스가 애플리케이션의 `config/app.php` 구성 파일의 `providers` 배열 내에 등록되어 있는지 확인해야 합니다.

Fortify 서비스 제공자는 Fortify가 게시한 작업을 등록하고 Fortify가 해당 작업을 실행할 때 Fortify에 이를 사용하도록 지시합니다.

<a name="fortify-features"></a>
### Fortify 특징

`fortify` 환경설정에는 `features` 구성 배열이 포함되어 있습니다. 이 배열은 Fortify가 기본적으로 노출할 백엔드 경로/기능을 정의합니다. Fortify를 [Laravel Jetstream](https://jetstream.laravel.com)과 함께 사용하지 않는 경우 대부분의 Laravel 애플리케이션에서 제공하는 기본 인증 기능인 다음 기능만 활성화하는 것이 좋습니다.

```php
'features' => [
    Features::registration(),
    Features::resetPasswords(),
    Features::emailVerification(),
],
```

<a name="disabling-views"></a>
### View 비활성화

기본적으로 Fortify는 로그인 화면이나 등록 화면과 같은 View를 반환하기 위한 경로를 정의합니다. 그러나 SPA(Single-Page Application)을 빌드하는 경우 이러한 경로가 필요하지 않을 수 있습니다. 이러한 이유로 애플리케이션의 `config/fortify.php` 구성 파일에 있는 `views` 구성 값을 `false`로 설정하여 이러한 경로를 완전히 비활성화할 수 있습니다.

```php
'views' => false,
```

<a name="disabling-views-and-password-reset"></a>
#### View 비활성화 & 비밀번호 재설정

Fortify의 View를 비활성화하고 애플리케이션에 대한 비밀번호 재설정 기능을 구현하는 경우, 여전히 애플리케이션의 "비밀번호 재설정" View를 표시하는 `password.reset`이라는 경로를 정의해야 합니다. 이는 라라벨의 `Illuminate\Auth\Notifications\ResetPassword` 알림이 `password.reset`이라는 경로를 통해 비밀번호 재설정 URL을 생성하기 때문에 필요합니다.

<a name="authentication"></a>
## 인증

시작하려면 "로그인" View를 반환하는 방법을 Fortify에 지시해야 합니다. Fortify는 헤드리스 인증 라이브러리라는 것을 기억해야 합니다. 이미 완료된 라라벨 인증 기능의 프론트엔드 구현을 원하시면 [애플리케이션 스타터 키트](/docs/{{version}}/starter-kits)를 사용해야 합니다.

모든 인증 View의 렌더링 로직은 `Laravel\Fortify\Fortify` 클래스를 통해 적절한 방법을 사용해 정의할 수 있습니다. 일반적으로 애플리케이션의 `App\Providers\FortifyServiceProvider` 클래스의 `boot` 메소드에서 이 메소드를 호출해야 합니다. Fortify는 이 View를 반환하는 `/login` 경로 정의를 처리합니다.

    use Laravel\Fortify\Fortify;

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        Fortify::loginView(function () {
            return view('auth.login');
        });

        // ...
    }

로그인 템플릿에는 `/login`에 대한 POST 요청을 만드는 양식이 포함되어야 합니다. `/login` 엔드포인트는 `email` / `username` 문자열과 `password`를 필요로 합니다. 이메일/사용자 이름 필드의 이름은 `config/fortify.php` 구성 파일 내의 `username` 값과 일치해야 합니다. 추가로, 사용자가 라라벨이 제공하는 "기억하기" 기능을 사용하기를 원한다는 것을 나타내기 위해 Boolean `remember` 필드가 제공될 수 있습니다.

로그인 시도가 성공하면 Fortify는 애플리케이션의 `fortify` 구성 파일 내 `home` 구성 옵션을 통해 구성된 URI로 리디렉션합니다. 로그인 요청이 XHR 요청인 경우 200 HTTP 응답이 반환됩니다.

요청이 성공하지 못한 경우 사용자는 로그인 화면으로 다시 리디렉션되고 유효성 검사 오류는 `$errors` 로 확인할 수 있습니다. [Blade 템플릿 변수](/docs/{{version}}/validation#quick-displaying-the-validation-errors)를 통해 확인할 수 있습니다. 또는 XHR 요청의 경우 422 HTTP 응답과 함께 유효성 검사 오류가 반환됩니다.

<a name="customizing-user-authentication"></a>
### 유저 인증 커터마징

Fortify는 제공된 자격 증명과 애플리케이션에 대해 구성된 인증 가드를 기반으로 사용자를 자동으로 검색하고 인증합니다. 그러나 로그인 자격 증명이 인증되고 사용자가 검색되는 방식에 대해 전체 사용자 지정을 원하는 경우가 있습니다. 고맙게도 Fortify에서는 `Fortify::authenticateUsing` 메서드를 사용하여 이를 쉽게 수행할 수 있습니다.

이 메서드는 들어오는 HTTP 요청을 클로저로 받을 수 있습니다. 클로저는 요청에 연결된 로그인 자격 증명의 유효성을 검사하고 연결된 사용자 인스턴스를 반환하는 역할을 합니다. 자격 증명이 유효하지 않거나 사용자를 찾을 수 없으면 클로저에서 `null` 또는 `false`를 반환해야 합니다. 일반적으로 이 메소드는 `FortifyServiceProvider`의 `boot` 메소드에서 호출해야 합니다.

```php
use App\Models\User;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;
use Laravel\Fortify\Fortify;

/**
 * Bootstrap any application services.
 *
 * @return void
 */
public function boot()
{
    Fortify::authenticateUsing(function (Request $request) {
        $user = User::where('email', $request->email)->first();

        if ($user &&
            Hash::check($request->password, $user->password)) {
            return $user;
        }
    });

    // ...
}
```

<a name="authentication-guard"></a>
#### 인증 가드

애플리케이션의 `fortify` 구성 파일 내에서 Fortify가 사용하는 인증 가드를 커스터마이즈할 수 있습니다. 그러나 구성된 가드가 `Illuminate\Contracts\Auth\StatefulGuard`의 구현인지 확인해야 합니다. SPA를 인증하기 위해 Laravel Fortify를 사용하려는 경우 [Laravel Sanctum](https://laravel.com/docs/sanctum)과 함께 Laravel의 기본 `web` 가드를 사용해야 합니다.

<a name="customizing-the-authentication-pipeline"></a>
### 인증 파이프라인 커스터마이징

Laravel Fortify는 호출 가능한 클래스의 파이프라인을 통해 로그인 요청을 인증합니다. 원하는 경우 로그인 요청이 통과해야 하는 클래스의 파이프라인을 커스터마이즈할 수 있습니다. 각 클래스에는 들어오는 `Illuminate\Http\Request` 인스턴스를 수신하는 `__invoke` 메서드가 있어야 하며, [미들웨어](/docs/{{version}}/middleware)와 같이 다음에서 호출되는 `$next` 변수가 있어야 합니다. 파이프라인의 다음 클래스로 요청을 전달하기 위해

커스텀 파이프라인을 정의하기 위해 `Fortify::authenticateThrough` 메소드를 사용할 수 있습니다. 이 메서드는 로그인 요청을 파이프로 연결하기 위해 클래스 배열을 반환하는 클로저를 받을 수 있습니다. 일반적으로 이 메소드는 `App\Providers\FortifyServiceProvider` 클래스의 `boot` 메소드에서 호출해야 합니다.

아래 예시는 직접 수정할 때 시작점으로 사용할 수 있는 기본 파이프라인 정의가 포함되어 있습니다.

```php
use Laravel\Fortify\Actions\AttemptToAuthenticate;
use Laravel\Fortify\Actions\EnsureLoginIsNotThrottled;
use Laravel\Fortify\Actions\PrepareAuthenticatedSession;
use Laravel\Fortify\Actions\RedirectIfTwoFactorAuthenticatable;
use Laravel\Fortify\Fortify;
use Illuminate\Http\Request;

Fortify::authenticateThrough(function (Request $request) {
    return array_filter([
            config('fortify.limiters.login') ? null : EnsureLoginIsNotThrottled::class,
            Features::enabled(Features::twoFactorAuthentication()) ? RedirectIfTwoFactorAuthenticatable::class : null,
            AttemptToAuthenticate::class,
            PrepareAuthenticatedSession::class,
    ]);
});
```

<a name="customizing-authentication-redirects"></a>
### 리다이렉트 커스터마이징

로그인 시도가 성공하면 Fortify는 애플리케이션의 `fortify` 구성 파일 내 `home` 구성 옵션을 통해 구성된 URI로 리디렉션합니다. 로그인 요청이 XHR 요청인 경우 200 HTTP 응답이 반환됩니다. 사용자가 애플리케이션에서 로그아웃하면 사용자는 `/` URI로 리디렉션됩니다.

이 동작의 고급 커스터마이즈가 필요한 경우 `LoginResponse` 및 `LogoutResponse`의 구현을 Laravel [서비스 컨테이너](/docs/{{version}}/container)에 바인딩할 수 있습니다. 일반적으로 이것은 애플리케이션의 `App\Providers\FortifyServiceProvider` 클래스의 `register` 메소드 내에서 수행되어야 합니다.

```php
use Laravel\Fortify\Contracts\LogoutResponse;

/**
 * Register any application services.
 *
 * @return void
 */
public function register()
{
    $this->app->instance(LogoutResponse::class, new class implements LogoutResponse {
        public function toResponse($request)
        {
            return redirect('/');
        }
    });
}
```

<a name="two-factor-authentication"></a>
## 2단계 인증

Fortify의 2단계 인증 기능이 활성화되면 사용자는 인증 과정에서 6자리 숫자 토큰을 입력해야 합니다. 이 토큰은 Google Authenticator와 같은 모든 TOTP 호환 모바일 인증 애플리케이션에서 검색할 수 있는 TOTP(시간 기반 일회용 비밀번호)를 사용하여 생성됩니다.

시작하기 전에 먼저 애플리케이션의 `App\Models\User` 모델이 `Laravel\Fortify\TwoFactorAuthenticatable` trait을 사용하는지 확인해야 합니다.

```php
<?php

namespace App\Models;

use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Notifications\Notifiable;
use Laravel\Fortify\TwoFactorAuthenticatable;

class User extends Authenticatable
{
    use Notifiable, TwoFactorAuthenticatable;
}
```

다음으로, 사용자가 2단계 인증 설정을 관리할 수 있는 화면을 애플리케이션 내에 구축해야 합니다. 이 화면에서 사용자는 2단계 인증을 활성화 및 비활성화하고 2단계 인증 복구 코드를 다시 생성할 수 있습니다.

> 기본적으로 `fortify` 구성 파일의 `features` 배열은 Fortify의 2단계 인증 설정이 수정하기 전에 비밀번호 확인을 요구하도록 지시합니다. 따라서 계속하기 전에 애플리케이션에서 Fortify의 [비밀번호 확인](#password-confirmation) 기능을 구현해야 합니다.

<a name="enabling-two-factor-authentication"></a>
### 2단계 인증 활성화

2단계 인증을 활성화하려면 애플리케이션이 Fortify에서 정의한 `/user/two-factor-authentication` 엔드포인트에 POST 요청을 해야 합니다. 요청이 성공하면 사용자는 이전 URL로 다시 리디렉션되고 `status` 세션 변수는 `two-factor-authentication-enabled`로 설정됩니다. 템플릿 내에서 이 `status` 세션 변수를 감지하여 적절한 성공 메시지를 표시할 수 있습니다. 요청이 XHR 요청인 경우 `200` HTTP 응답이 반환됩니다.

```html
@if (session('status') == 'two-factor-authentication-enabled')
<div class="mb-4 font-medium text-sm text-green-600">
  Two factor authentication has been enabled.
</div>
@endif
```

다음으로, 사용자가 인증 애플리케이션을 스캔할 수 있도록 2단계 인증 QR 코드를 표시해야 합니다. 블레이드를 사용하여 애플리케이션의 프론트엔드를 렌더링하는 경우 사용자 인스턴스에서 사용할 수 있는 `twoFactorQrCodeSvg` 메서드를 사용하여 QR 코드 SVG를 검색할 수 있습니다.

```php
$request->user()->twoFactorQrCodeSvg();
```

JavaScript 기반 프론트엔드를 구축하는 경우 `/user/two-factor-qr-code` 엔드포인트에 XHR GET 요청을 만들어 사용자의 2단계 인증 QR 코드를 검색할 수 있습니다. 이 끝점은 `svg` 키가 포함된 JSON 객체를 반환합니다.

<a name="displaying-the-recovery-codes"></a>
#### 복구 코드 표시

또한 사용자의 2단계 복구 코드를 표시해야 합니다. 이러한 복구 코드를 통해 사용자는 모바일 장치에 액세스할 수 없는 경우 인증할 수 있습니다. Blade를 사용하여 애플리케이션의 프론트엔드를 렌더링하는 경우 인증된 사용자 인스턴스를 통해 복구 코드에 액세스할 수 있습니다.

```php
(array) $request->user()->recoveryCodes()
```

JavaScript 기반 프론트엔드를 구축하는 경우 `/user/two-factor-recovery-codes` 엔드포인트에 XHR GET 요청을 할 수 있습니다. 이 끝점은 사용자의 복구 코드가 포함된 JSON 배열을 반환합니다.

사용자의 복구 코드를 재생성하려면 애플리케이션이 `/user/two-factor-recovery-codes` 엔드포인트에 POST 요청을 해야 합니다.

<a name="authenticating-with-two-factor-authentication"></a>
### 2단계 인증으로 인증하기

인증 프로세스 동안 Fortify는 자동으로 사용자를 애플리케이션의 2단계 인증 챌린지 화면으로 리디렉션합니다. 그러나 애플리케이션이 XHR 로그인 요청을 하는 경우 성공적인 인증 시도 후 반환되는 JSON 응답에는 `two_factor` Boolean 속성이 있는 JSON 객체가 포함됩니다. 이 값을 검사하여 애플리케이션의 2단계 인증 챌린지 화면으로 리디렉션해야 하는지 여부를 알아야 합니다.

2단계 인증 기능 구현을 시작하려면 Fortify에 2단계 인증 챌린지 View를 반환하는 방법을 지시해야 합니다. Fortify의 모든 인증 뷰 렌더링 로직은 `Laravel\Fortify\Fortify` 클래스를 통해 사용할 수 있는 적절한 방법을 사용하여 커스터마이즈할 수 있습니다. 일반적으로 애플리케이션의 `App\Providers\FortifyServiceProvider` 클래스의 `boot` 메소드에서 이 메소드를 호출해야 합니다.

```php
use Laravel\Fortify\Fortify;

/**
 * Bootstrap any application services.
 *
 * @return void
 */
public function boot()
{
    Fortify::twoFactorChallengeView(function () {
        return view('auth.two-factor-challenge');
    });

    // ...
}
```

Fortify는 이 View를 반환하는 `/two-factor-challenge` 경로 정의를 처리합니다. `two-factor-challenge` 템플릿에는 `/two-factor-challenge` 엔드포인트에 POST 요청을 하는 양식이 포함되어야 합니다. `/two-factor-challenge` 작업은 유효한 TOTP 토큰이 포함된 `code` 필드 또는 사용자의 복구 코드 중 하나가 포함된 `recovery_code` 필드를 예상합니다.

로그인 시도가 성공하면 Fortify는 애플리케이션의 `fortify` 구성 파일 내 `home` 구성 옵션을 통해 구성된 URI로 사용자를 리디렉션합니다. 로그인 요청이 XHR 요청인 경우 204 HTTP 응답이 반환됩니다.

요청이 성공하지 못한 경우 사용자는 로그인 화면으로 다시 리디렉션되고 유효성 검사 오류는 `$errors` [Blade 템플릿 변수](/docs/{{version}}/validation#quick-displaying-the-validation-errors)를 통해 확인할 수 있습니다. 또는 XHR 요청의 경우 422 HTTP 응답과 함께 유효성 검사 오류가 반환됩니다.


<a name="disabling-two-factor-authentication"></a>
### 2단계 인증 비활성화

2단계 인증을 비활성화하려면 애플리케이션이 `/user/two-factor-authentication` 엔드포인트에 DELETE 요청을 해야 합니다. Fortify의 2단계 인증 엔드포인트는 호출되기 전에 [비밀번호 확인](#password-confirmation)이 필요하다는 것을 기억하십시오.

<a name="registration"></a>
## 등록

애플리케이션의 등록 기능 구현을 시작하려면 Fortify에 "등록" View를 반환하는 방법을 지시해야 합니다. Fortify는 헤드리스 인증 라이브러리라는 것을 기억해야합니다. 이미 완료된 라라벨 인증 기능의 프론트엔드 구현을 원하시면 [애플리케이션 스타터 키트](/docs/{{version}}/starter-kits)를 사용해야 합니다.

Fortify의 모든 View 렌더링 로직은 `Laravel\Fortify\Fortify` 클래스를 통해 사용할 수 있는 적절한 방법을 사용하여 커스터마이즈할 수 있습니다. 일반적으로 `App\Providers\FortifyServiceProvider` 클래스의 `boot` 메소드에서 이 메소드를 호출해야 합니다.

```php
use Laravel\Fortify\Fortify;

/**
 * Bootstrap any application services.
 *
 * @return void
 */
public function boot()
{
    Fortify::registerView(function () {
        return view('auth.register');
    });

    // ...
}
```

Fortify는 이 View를 반환하는 `/register` 경로 정의를 처리합니다. `register` 템플릿에는 Fortify에서 정의한 `/register` 엔드포인트에 POST 요청을 하는 양식이 포함되어야 합니다.

`/register` 엔드포인트에는 문자열 `name`, 문자열 이메일 주소/사용자 이름, `password` 및 `password_confirmation` 필드가 필요합니다. 이메일/사용자 이름 필드의 이름은 애플리케이션의 `fortify` 구성 파일에 정의된 `username` 구성 값과 일치해야 합니다.

등록 시도가 성공하면 Fortify는 애플리케이션의 `fortify` 구성 파일 내 `home` 구성 옵션을 통해 구성된 URI로 사용자를 리디렉션합니다. 로그인 요청이 XHR 요청인 경우 200 HTTP 응답이 반환됩니다.

요청이 성공하지 못한 경우 사용자는 등록 화면으로 다시 리디렉션되고 유효성 검사 오류는 `$errors` [Blade 템플릿 변수](/docs/{{version}}/validation#quick-displaying-the-validation-errors)를 통해 확인할 수 있습니다. 또는 XHR 요청의 경우 422 HTTP 응답과 함께 유효성 검사 오류가 반환됩니다.

<a name="customizing-registration"></a>
### 등록 커스터마이징

사용자 유효성 검사 및 생성 프로세스는 Laravel Fortify를 설치할 때 생성된 `App\Actions\Fortify\CreateNewUser` 작업을 수정하여 커스터마이즈할 수 있습니다.

<a name="password-reset"></a>
## 비밀번호 재설정

<a name="requesting-a-password-reset-link"></a>
### 비밀번호 재설정 링크 요청

애플리케이션의 비밀번호 재설정 기능 구현을 시작하려면 Fortify에 "비밀번호 찾기" View를 반환하는 방법을 지시해야 합니다. Fortify는 헤드리스 인증 라이브러리라는 것을 기억해야합니다. 이미 완료된 라라벨 인증 기능의 프론트엔드 구현을 원하시면 [애플리케이션 스타터 키트](/docs/{{version}}/starter-kits)를 사용해야 합니다.

Fortify의 모든 View 렌더링 로직은 `Laravel\Fortify\Fortify` 클래스를 통해 사용할 수 있는 적절한 방법을 사용하여 커스터마이즈할 수 있습니다. 일반적으로 애플리케이션의 `App\Providers\FortifyServiceProvider` 클래스의 `boot` 메소드에서 이 메소드를 호출해야 합니다.

```php
use Laravel\Fortify\Fortify;

/**
 * Bootstrap any application services.
 *
 * @return void
 */
public function boot()
{
    Fortify::requestPasswordResetLinkView(function () {
        return view('auth.forgot-password');
    });

    // ...
}
```

Fortify는 이 View를 반환하는 `/forgot-password` 엔드포인트 정의를 처리합니다. `forgot-password` 템플릿에는 `/forgot-password` 엔드포인트에 POST 요청을 하는 양식이 포함되어야 합니다.

`/forgot-password` 엔드포인트는 문자열 `email` 필드를 필요로 합니다. 이 필드/데이터베이스 열의 이름은 애플리케이션의 `fortify` 구성 파일에 있는 `email` 구성 값과 일치해야 합니다.

<a name="handling-the-password-reset-link-request-response"></a>
#### 비밀번호 재설정 링크 요청 응답 처리

비밀번호 재설정 링크 요청이 성공하면 Fortify는 사용자를 다시 `/forgot-password` 엔드포인트로 리디렉션하고 비밀번호 재설정에 사용할 수 있는 보안 링크가 포함된 이메일을 사용자에게 보냅니다. 요청이 XHR 요청인 경우 200 HTTP 응답이 반환됩니다.

요청이 성공한 후 `/forgot-password` 엔드포인트로 다시 리디렉션된 후 `status` 세션 변수를 사용하여 비밀번호 재설정 링크 요청 시도의 상태를 표시할 수 있습니다. 이 세션 변수의 값은 애플리케이션의 '비밀번호' [언어 파일](/docs/{{version}}/localization) 내에 정의된 번역 문자열 중 하나와 일치합니다.

```html
@if (session('status'))
<div class="mb-4 font-medium text-sm text-green-600">
  {{ session('status') }}
</div>
@endif
```

요청이 성공하지 못한 경우 사용자는 요청 비밀번호 재설정 링크 화면으로 다시 리디렉션되고 유효성 검사 오류는 공유 `$errors` [Blade 템플릿 변수](/docs/{{version}}/validation#quick-displaying-the-validation-errors). 또는 XHR 요청의 경우 422 HTTP 응답과 함께 유효성 검사 오류가 반환됩니다.

<a name="resetting-the-password"></a>
### 비밀번호 재설정

애플리케이션의 비밀번호 재설정 기능 구현을 완료하려면 Fortify에 "비밀번호 재설정" View를 반환하는 방법을 지시해야 합니다.

Fortify의 모든 View 렌더링 로직은 `Laravel\Fortify\Fortify` 클래스를 통해 사용할 수 있는 적절한 방법을 사용하여 커스터마이즈할 수 있습니다. 일반적으로 애플리케이션의 `App\Providers\FortifyServiceProvider` 클래스의 `boot` 메소드에서 이 메소드를 호출해야 합니다.

```php
use Laravel\Fortify\Fortify;

/**
 * Bootstrap any application services.
 *
 * @return void
 */
public function boot()
{
    Fortify::resetPasswordView(function ($request) {
        return view('auth.reset-password', ['request' => $request]);
    });

    // ...
}
```

Fortify는 이 View를 표시하기 위한 경로 정의를 처리합니다. `reset-password` 템플릿에는 `/reset-password`에 대한 POST 요청을 만드는 양식이 포함되어야 합니다.

`/reset-password` 엔드포인트는 문자열 `email` 필드, `password` 필드, `password_confirmation` 필드, `request()->route('token')`이라는 숨겨진 필드를 예상합니다. "email" 필드/데이터베이스 열의 이름은 애플리케이션의 `fortify` 구성 파일에 정의된 `email` 구성 값과 일치해야 합니다.

<a name="handling-the-password-reset-response"></a>
#### 비밀번호 재설정 응답 처리

비밀번호 재설정 요청이 성공하면 Fortify는 사용자가 새 비밀번호로 로그인할 수 있도록 `/login` 경로로 다시 리디렉션합니다. 또한 로그인 화면에 재설정 성공 상태를 표시할 수 있도록 `status` 세션 변수가 설정됩니다.

```html
@if (session('status'))
<div class="mb-4 font-medium text-sm text-green-600">
  {{ session('status') }}
</div>
@endif
```

요청이 XHR 요청인 경우 200 HTTP 응답이 반환됩니다.

요청이 성공하지 못한 경우 사용자는 비밀번호 재설정 화면으로 다시 리디렉션되며 `$errors` [Blade 템플릿 변수](/docs/{{version}}/validation#quick-displaying-the-validation-errors). 또는 XHR 요청의 경우 422 HTTP 응답과 함께 유효성 검사 오류가 반환됩니다.

<a name="customizing-password-resets"></a>
### 비밀번호 재설정 커스터마이징

암호 재설정 프로세스는 Laravel Fortify를 설치할 때 생성된 `App\Actions\ResetUserPassword` 작업을 수정하여 사용자 지정할 수 있습니다.

<a name="email-verification"></a>
## 이메일 인증

등록 후 사용자가 애플리케이션에 계속 액세스하기 전에 이메일 주소를 확인하도록 할 수 있습니다. 사용하려면 `fortify` 구성 파일의 `features` 배열에서 `emailVerification` 기능이 활성화되어 있는지 확인하십시오. 다음으로 `App\Models\User` 클래스가 `Illuminate\Contracts\Auth\MustVerifyEmail` 인터페이스를 구현하는지 확인해야 합니다.

이 두 가지 설정 단계가 완료되면 새로 등록된 사용자는 이메일 주소 소유권을 확인하라는 이메일을 받게 됩니다. 그러나 사용자에게 이메일의 확인 링크를 클릭해야 함을 알리는 이메일 확인 화면을 표시하는 방법을 Fortify에 알려야 합니다.

Fortify의 모든 뷰의 렌더링 로직은 `Laravel\Fortify\Fortify` 클래스를 통해 사용할 수 있는 적절한 방법을 사용하여 커스터마이즈할 수 있습니다. 일반적으로 애플리케이션의 `App\Providers\FortifyServiceProvider` 클래스의 `boot` 메소드에서 이 메소드를 호출해야 합니다.

```php
use Laravel\Fortify\Fortify;

/**
 * Bootstrap any application services.
 *
 * @return void
 */
public function boot()
{
    Fortify::verifyEmailView(function () {
        return view('auth.verify-email');
    });

    // ...
}
```

Fortify는 사용자가 Laravel의 내장된 `verified` 미들웨어에 의해 `/email/verify` 엔드포인트로 리디렉션될 때 이 View를 표시하는 경로를 정의합니다.

`verify-email` 템플릿에는 사용자에게 이메일 주소로 전송된 이메일 확인 링크를 클릭하도록 지시하는 정보 메시지가 포함되어야 합니다.

<a name="resending-email-verification-links"></a>
#### 이메일 확인 링크 재전송

원하는 경우 `/email/verification-notification` 엔드포인트에 대한 POST 요청을 트리거하는 버튼을 애플리케이션의 `verify-email` 템플릿에 추가할 수 있습니다. 이 엔드포인트가 요청을 받으면 새 확인 이메일 링크가 사용자에게 이메일로 전송되어 이전 링크가 실수로 삭제되거나 손실된 경우 사용자가 새 확인 링크를 받을 수 있습니다.

확인 링크 이메일 재전송 요청이 성공하면 Fortify는 `status` 세션 변수를 사용하여 사용자를 `/email/verify` 엔드포인트로 다시 리디렉션하여 작업이 완료됨 메시지를 사용자에게 표시할 수 있습니다. 요청이 XHR 요청인 경우 202 HTTP 응답이 반환됩니다.

```html
@if (session('status') == 'verification-link-sent')
<div class="mb-4 font-medium text-sm text-green-600">
  A new email verification link has been emailed to you!
</div>
@endif
```
<a name="protecting-routes"></a>
### 경로 보호

사용자가 이메일 주소를 확인해야 하는 경로 또는 경로 그룹을 지정하려면 Laravel의 내장 `verified` 미들웨어를 경로에 연결해야 합니다. 이 미들웨어는 애플리케이션의 `App\Http\Kernel` 클래스에 등록됩니다.

```php
Route::get('/dashboard', function () {
    // ...
})->middleware(['verified']);
```

<a name="password-confirmation"></a>
## 비밀번호 확인

애플리케이션을 빌드하는 동안 작업을 수행하기 전에 사용자가 암호를 확인해야 하는 작업이 있을 수 있습니다. 일반적으로 이러한 경로는 Laravel의 내장 `password.confirm` 미들웨어에 의해 보호됩니다.

비밀번호 확인 기능 구현을 시작하려면 애플리케이션의 "비밀번호 확인" View를 반환하는 방법을 Fortify에 지시해야 합니다. Fortify는 헤드리스 인증 라이브러리라는 것을 기억하십시오. 이미 완료된 라라벨 인증 기능의 프론트엔드 구현을 원하시면 [애플리케이션 스타터 키트](/docs/{{version}}/starter-kits)를 사용해야 합니다.

Fortify의 모든 뷰 렌더링 로직은 `Laravel\Fortify\Fortify` 클래스를 통해 사용할 수 있는 적절한 방법을 사용하여 커스터마이즈할 수 있습니다. 일반적으로 애플리케이션의 `App\Providers\FortifyServiceProvider` 클래스의 `boot` 메소드에서 이 메소드를 호출해야 합니다.

```php
use Laravel\Fortify\Fortify;

/**
 * Bootstrap any application services.
 *
 * @return void
 */
public function boot()
{
    Fortify::confirmPasswordView(function () {
        return view('auth.confirm-password');
    });

    // ...
}
```

Fortify는 이 View를 반환하는 `/user/confirm-password` 엔드포인트 정의를 처리합니다. `confirm-password` 템플릿에는 `/user/confirm-password` 엔드포인트에 POST 요청을 하는 양식이 포함되어야 합니다. `/user/confirm-password` 엔드포인트는 사용자의 현재 비밀번호가 포함된 `password` 필드를 예상합니다.

암호가 사용자의 현재 암호와 일치하면 Fortify는 사용자가 액세스를 시도한 경로로 리디렉션합니다. 요청이 XHR 요청인 경우 201 HTTP 응답이 반환됩니다.

요청이 성공하지 못한 경우 사용자는 암호 확인 화면으로 다시 리디렉션되고 유효성 검사 오류는 `$errors` 블레이드 템플릿 변수를 통해 확인할 수 있습니다. 또는 XHR 요청의 경우 422 HTTP 응답과 함께 유효성 검사 오류가 반환됩니다.
