# Resetting Passwords
# 패스워드 재설정하기

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [Model Preparation](#model-preparation)
    - [모델 준비하기](#model-preparation)
    - [Database Preparation](#database-preparation)
    - [데이터베이스 준비하기](#database-preparation)
    - [Configuring Trusted Hosts](#configuring-trusted-hosts)
    - [신뢰할 수 있는 호스트 설정](#configuring-trusted-hosts)
- [Routing](#resetting-routing)
- [라우팅](#resetting-routing)
    - [Requesting The Password Reset Link](#requesting-the-password-reset-link)
    - [비밀번호 재설정 링크 요청하기](#requesting-the-password-reset-link)
    - [Resetting The Password](#resetting-the-password)
    - [비밀번호 재설정](#resetting-the-password)
- [Deleting Expired Tokens](#deleting-expired-tokens)
- [만료된 토큰 삭제](#deleting-expired-tokens)
- [Customization](#password-customization)
- [커스터마이징](#password-customization)

<a name="introduction"></a>
## Introduction
## 시작하기

Most web applications provide a way for users to reset their forgotten passwords. Rather than forcing you to re-implement this by hand for every application you create, Laravel provides convenient services for sending password reset links and secure resetting passwords.

대부분의 웹 애플리케이션은 유저가 잊어버린 비밀번호를 재설정할 수 있는 방법을 제공합니다. 애플리케이션을 만들 때 마다 매번 일일이 비밀번호 재설정을 다시 만드는 수고가 없도록 라라벨은 보안성이 높은 비밀번호 재설정 과정을 제공하며 비밀번호 재설정 링크를 보낼 수 있는 편리한 서비스를 제공합니다.

> {tip} Want to get started fast? Install a Laravel [application starter kit](/docs/{{version}}/starter-kits) in a fresh Laravel application. Laravel's starter kits will take care of scaffolding your entire authentication system, including resetting forgotten passwords.

> {tip} 애플리케이션을 빠르게 만들고 싶으신가요? 라라벨 애플리케이션을 새로 만들었다면 [애플리케이션 스타터 킷](/docs/{{version}}/starter-kits)을 설치하세요. 라라벨 스타터 킷은 전체 인증 시스템을 스캐폴딩을 통해 설치합니다. 스타터 킷의 인증 스캐폴딩은 비밀번호를 잊어 버렸을 때 재설정하는 기능을 포함합니다.

<a name="model-preparation"></a>
### Model Preparation
### 모델 준비하기

Before using the password reset features of Laravel, your application's `App\Models\User` model must use the `Illuminate\Notifications\Notifiable` trait. Typically, this trait is already included on the default `App\Models\User` model that is created with new Laravel applications.

라라벨의 비밀번호 재설정 기능을 사용하기 전에 애플리케이션의 `App\Models\User` 모델에는 반드시 `Illuminate\Notifications\Notifiable` 트레이트를 사용해 줘야 합니다. 일반적으로 이 트레이트는 라라벨 애플리케이션을 새로 생성할 때 디폴트로 `App\Models\User` 모델에 포함되어 있습니다.

Next, verify that your `App\Models\User` model implements the `Illuminate\Contracts\Auth\CanResetPassword` contract. The `App\Models\User` model included with the framework already implements this interface, and uses the `Illuminate\Auth\Passwords\CanResetPassword` trait to include the methods needed to implement the interface.

다음으로 `App\Models\User` 모델이 `Illuminate\Contracts\Auth\CanResetPassword` 컨트렉트(contract)를 구현하는지 확인합니다. 라라벨 프레임워크의 `App\Models\User` 모델은 `Illuminate\Auth\Passwords\CanResetPassword` 트레이트를 사용하여 인터페이스인 컨트렉트를 구현하고 있습니다.

<a name="database-preparation"></a>
### Database Preparation
### 데이터베이스 준비하기

A table must be created to store your application's password reset tokens. The migration for this table is included in the default Laravel application, so you only need to migrate your database to create this table:

라라벨의 기본 비밀번호 재설정 기능을 사용하기 위해서는 애플리케이션에 비밀번호 재설정 토큰을 저장하는 테이블을 만들어 주어야 합니다. 비밀번호 재설정 토큰을 저장하는 테이블은 라라벨의 디폴트 마이그레이션을 통해 생성할 수 있습니다.

    php artisan migrate

<a name="configuring-trusted-hosts"></a>
### Configuring Trusted Hosts
### 신뢰할 수 있는 호스트 설정

By default, Laravel will respond to all requests it receives regardless of the content of the HTTP request's `Host` header. In addition, the `Host` header's value will be used when generating absolute URLs to your application during a web request.

기본적으로 라라벨은 HTTP 요청(request)의 헤더의 `Host` 값에 관계없이 수신하는 모든 요청에 응답합니다. 또한 웹 요청 중에 애플리케이션에 대한 절대 경로의 URL(absolute URLs)을 생성할 때 헤더의 `Host` 값이 사용됩니다.

Typically, you should configure your web server, such as Nginx or Apache, to only send requests to your application that match a given host name. However, if you do not have the ability to customize your web server directly and need to instruct Laravel to only respond to certain host names, you may do so by enabling the `App\Http\Middleware\TrustHosts` middleware for your application. This is particular important when your application offers password reset functionality.

일반적으로 지정된 호스트 이름과 일치하는 요청만 애플리케이션에 보내기 위해서는 Nginx 또는 Apache 를 사용하여 웹 서버를 구성해야 합니다. 그러나 웹 서버를 직접 커스터마이징 할 수 없는 상황이고 특정 호스트 이름에만 응답하도록 해야 한다면 라라벨에 직접 일치하는 호스트 이름에만 응답하도록 지시할 수 있습니다. 라라벨을 통해 지정된 호스트와 일치하는 요청에만 응답하도록 구성하려면 `App\Http\Middleware\TrustHosts` 미들웨어를 활성화합니다. 이 미들웨어는 애플리케이션이 비밀번호 재설정 기능을 제공할 때 특히 요긴하게 쓰입니다.

To learn more about this middleware, please consult the [`TrustHosts` middleware documentation](/docs/{{version}}/requests#configuring-trusted-hosts).

이 미들웨어에 대한 자세한 내용은 [`TrustHosts` 미들웨어 문서](/docs/{{version}}/requests#configuring-trusted-hosts)를 보세요.

<a name="resetting-routing"></a>
## Routing
## 라우팅

To properly implement support for allowing users to reset their passwords, we will need to define several routes. First, we will need a pair of routes to handle allowing the user to request a password reset link via their email address. Second, we will need a pair of routes to handle actually resetting the password once the user visits the password reset link that is emailed to them and completes the password reset form.

사용자의 비밀번호를 재설정하는 기능을 만들기 위해서는 몇 가지 라우트를 구성하는 것이 좋습니다. 먼저 사용자가 이메일 주소를 통해서 비밀번호 재설정 링크로 이동하기 위한 라우트가 필요합니다. 다음으로 사용자가 이메일로 받은 비밀번호 재설정 링크를 방문하여 비밀번호를 재설정하는 양식을 작성해 제출할 때 비밀번호 재설정을 처리하는 라우터가 필요합니다. 비밀번호 재설정 기능을 제공하기 위해서는 폼 화면으로 이동하기 위한 라우터와 제출하기 위한 라우터, 한 쌍의 라우터가 정의 되어 있어야 합니다.

<a name="requesting-the-password-reset-link"></a>
### Requesting The Password Reset Link
### 비밀번호 재설정 링크 요청하기

<a name="the-password-reset-link-request-form"></a>
#### The Password Reset Link Request Form
#### 비밀번호 재설정 링크 리퀘스트 폼

First, we will define the routes that are needed to request password reset links. To get started, we will define a route that returns a view with the password reset link request form:

먼저 비밀번호 재설정 링크를 요청하는 데 필요한 경로를 정의합니다. 이를 구현하기 위해 비밀번호 재설정 링크 요청(request) 양식을 사용하여 뷰(view)를 반환하는 경로를 정의합니다.

    Route::get('/forgot-password', function () {
        return view('auth.forgot-password');
    })->middleware('guest')->name('password.request');

The view that is returned by this route should have a form containing an `email` field, which will allow the user to request a password reset link for a given email address.

이 라우트에 의해 반환되는 뷰(view)에는 `email` 필드가 포함된 양식이 있어야 하며, 유저가 제출한 이메일 주소에 대한 패스워드 재설정 링크를 요청하는 것을 허가합니다.

<a name="password-reset-handling-the-form-submission"></a>
#### Handling The Form Submission
#### 양식 제출 처리

Next, we will define a route that handles the form submission request from the "forgot password" view. This route will be responsible for validating the email address and sending the password reset request to the corresponding user:

다음으로 "비밀번호 찾기" 뷰(view)에서 폼(form) 제출 요청을 처리하는 경로를 정의합니다. 이 라우트는 이메일 주소의 유효성을 확인하고 메일과 일치하는 유저에게 비밀번호 재설정 요청을 보내는 역할을 합니다.

    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Password;

    Route::post('/forgot-password', function (Request $request) {
        $request->validate(['email' => 'required|email']);

        $status = Password::sendResetLink(
            $request->only('email')
        );

        return $status === Password::RESET_LINK_SENT
                    ? back()->with(['status' => __($status)])
                    : back()->withErrors(['email' => __($status)]);
    })->middleware('guest')->name('password.email');

Before moving on, let's examine this route in more detail. First, the request's `email` attribute is validated. Next, we will use Laravel's built-in "password broker" (via the `Password` facade) to send a password reset link to the user. The password broker will take care of retrieving the user by the given field (in this case, the email address) and sending the user a password reset link via Laravel's built-in [notification system](/docs/{{version}}/notifications).

더 설명하기에 앞서 이 라우트에 대해 더 자세히 살펴보겠습니다. 먼저 요청의 `email` 속성의 유효성을 검사합니다. 다음으로, 라라벨의 내장 "비밀번호 브로커(password broker)"(`Password` 파사드를 통해)를 사용하여 비밀번호 재설정 링크를 유저에게 보냅니다. 비밀번호 브로커는 유저가 폼을 통해 전달한 필드 값(이 설명의 경우 이메일 주소)을 회수합니다. 그리고 라라벨에 내장된 [노티피케이션 시스템](/docs/{{version}}/notifications)을 통해 비밀번호 재설정 링크를 유저에게 보냅니다.

The `sendResetLink` method returns a "status" slug. This status may be translated using Laravel's [localization](/docs/{{version}}/localization) helpers in order to display a user-friendly message to the user regarding the status of their request. The translation of the password reset status is determined by your application's `resources/lang/{lang}/passwords.php` language file. An entry for each possible value of the status slug is located within the `passwords` language file.

`sendResetLink` 메소드는 "스테이터스(status)" [메시지(slug)](https://en.wikipedia.org/wiki/Clean_URL#Slug)를 반환합니다. 스테이터스 메시지(slug)는 유저 상태와 관련된 메시지를 표시합니다. 또한 스테이터스 메시지(slug)는 라라벨의 [로컬라이제이션](/docs/{{version}}/localization) 헬퍼를 통해 번역을 제공하므로 유저 친화적인 메시지를 표시하게 됩니다. 비밀번호 재설정 스테이터스 메시지는 애플리케이션의 `resources/lang/{lang}/passwords.php` 파일에 정의된 내용을 바탕으로 번역됩니다. 스테이터스 메시지(slug)로 가능한 각각의 항목값은 `passwords` 언어 파일 내에 정의되어 있습니다.

You may be wondering how Laravel knows how to retrieve the user record from your application's database when calling the `Password` facade's `sendResetLink` method. The Laravel password broker utilizes your authentication system's "user providers" to retrieve database records. The user provider used by the password broker is configured within the `passwords` configuration array of your `config/auth.php` configuration file. To learn more about writing custom user providers, consult the [authentication documentation](/docs/{{version}}/authentication#adding-custom-user-providers).

라라벨이 `Password` 파사드의 `sendResetLink` 메소드를 호출할 때 애플리케이션 데이터베이스에서 사용자 레코드를 가져오는 방법을 어떻게 알고 있는지 궁금할 것입니다. 라라벨 비밀번호 브로커는 인증 시스템의 "user providers"를 활용하여 데이터베이스 레코드를 검색합니다. 비밀번호 브로커가 사용하는 user 프로바이더는 `config/auth.php` 설정 파일의 `passwords` 배열 내에서 설정됩니다. user 프로바이더를 커스텀하려면 [인증 문서](/docs/{{version}}/authentication#adding-custom-user-providers)를 참고 하십시오.

> {tip} When manually implementing password resets, you are required to define the contents of the views and routes yourself. If you would like scaffolding that includes all necessary authentication and verification logic, check out the [Laravel application starter kits](/docs/{{version}}/starter-kits).

> {tip} 메뉴얼대로 비밀번호 재설정을 구현하려고 한다면, 뷰(views) 안의 내용을 정의할 필요가 있습니다. 모든 필수적인 인증과 유효성 검사 로직을 포함하고 있는 스캐폴딩을 사용하고자 한다면 [라라벨 애플리케이션 스타터 킷](/docs/{{version}}/starter-kits)을 확인하십시오.

<a name="resetting-the-password"></a>
### Resetting The Password
### 비밀번호 재설정하기

<a name="the-password-reset-form"></a>
#### The Password Reset Form
#### 비밀번호 재설정 폼(Form)

Next, we will define the routes necessary to actually reset the password once the user clicks on the password reset link that has been emailed to them and provides a new password. First, let's define the route that will display the reset password form that is displayed when the user clicks the reset password link. This route will receive a `token` parameter that we will use later to verify the password reset request:

다음으로, 실제로 비밀번호를 재설정하고 새로운 비밀번호를 제출하기 위해서 필요한 라우트를 정의할 것입니다. 이 라우트는 유저의 메일로 발송된 비밀번호 재설정 링크를 유저가 클릭했을 때 사용됩니다. 먼저 유저가 비밀번호 재설정 링크를 클릭했을 때 비밀번호 재설정 폼을 표시하기 위한 라우트를 정의합니다. 이 라우트는 비밀번호 재설정 요청을 인증하기 위해 추후 사용될 `token` 파라메터를 받습니다. 

    Route::get('/reset-password/{token}', function ($token) {
        return view('auth.reset-password', ['token' => $token]);
    })->middleware('guest')->name('password.reset');

The view that is returned by this route should display a form containing an `email` field, a `password` field, a `password_confirmation` field, and a hidden `token` field, which should contain the value of the secret `$token` received by our route.

이 라우트에 의해 반환 된 뷰(view)의 폼(form)에는 `email`필드 `password` 필드, `password_confirmation` 필드, `token` 필드를 포함하고 있어야 합나다. `email`필드 `password` 필드, `password_confirmation` 필드는 페이지에 표시되는 폼이며 `token` 필드는 hidden 속성으로 숨겨진 필드입니다. `token` 필드에는 라우트에서 받은 `$token` 비밀(secret) 토큰의 값이 포함되어 있어야 합니다.

<a name="password-reset-handling-the-form-submission"></a>
#### Handling The Form Submission
#### 양식 제출 처리

Of course, we need to define a route to actually handle the password reset form submission. This route will be responsible for validating the incoming request and updating the user's password in the database:

당연하게도 비밀번호 재설정 폼에서 제출된 데이터를 실제로 처리하기 위해서는 라우트를 정의해야 합니다. 라우트는 들어오는 요청의 유효성을 검사하고 데이터베이스에서 유저의 비밀번호를 업데이트 하는 역할을 담당하고 있습니다.

    use Illuminate\Auth\Events\PasswordReset;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Hash;
    use Illuminate\Support\Facades\Password;
    use Illuminate\Support\Str;

    Route::post('/reset-password', function (Request $request) {
        $request->validate([
            'token' => 'required',
            'email' => 'required|email',
            'password' => 'required|min:8|confirmed',
        ]);

        $status = Password::reset(
            $request->only('email', 'password', 'password_confirmation', 'token'),
            function ($user, $password) {
                $user->forceFill([
                    'password' => Hash::make($password)
                ])->setRememberToken(Str::random(60));

                $user->save();

                event(new PasswordReset($user));
            }
        );

        return $status === Password::PASSWORD_RESET
                    ? redirect()->route('login')->with('status', __($status))
                    : back()->withErrors(['email' => [__($status)]]);
    })->middleware('guest')->name('password.update');

Before moving on, let's examine this route in more detail. First, the request's `token`, `email`, and `password` attributes are validated. Next, we will use Laravel's built-in "password broker" (via the `Password` facade) to validate the password reset request credentials.

계속 진행하기 전에 이 라우트를 더 자세히 살펴보겠습니다. 먼저 요청의 `token`, `email`, `password` 속성이 검증됩니다. 다음으로, 비밀번호 재설정 요청의 자격(credentials)을 확인하고 유효성 검사를 하기 위해 라라벨의 내장 "비밀번호 브로커"(`Password` 파사드를 통해)를 사용할 것입니다.

If the token, email address, and password given to the password broker are valid, the closure passed to the `reset` method will be invoked. Within this closure, which receives the user instance and the plain-text password provided to the password reset form, we may update the user's password in the database.

암호 브로커에 제공된 토큰, 이메일 주소, 암호가 유효하면 `reset` 메소드로 전달된 클로저가 호출(invoked)됩니다. 이 클로저는 인자로 유저 인스턴스와 비밀번호 재설정 폼(form)에서 보내진 평문 비밀번호를 전달 받아 데이터베이스의 사용자 암호를 업데이트 합니다.

The `reset` method returns a "status" slug. This status may be translated using Laravel's [localization](/docs/{{version}}/localization) helpers in order to display a user-friendly message to the user regarding the status of their request. The translation of the password reset status is determined by your application's `resources/lang/{lang}/passwords.php` language file. An entry for each possible value of the status slug is located within the `passwords` language file.

`reset` 메소드는 "스테이터스(status)" [메시지(slug)](https://en.wikipedia.org/wiki/Clean_URL#Slug)를 반환합니다. 스테이터스 메시지(slug)는 유저 상태와 관련된 메시지를 표시합니다. 또한 스테이터스 메시지(slug)는 라라벨의 [로컬라이제이션](/docs/{{version}}/localization) 헬퍼를 통해 번역 되어 유저 친화적인 메시지를 표시합니다. 비밀번호 재설정 스테이터스 메시지는 애플리케이션의 `resources/lang/{lang}/passwords.php` 파일에 정의된 내용을 바탕으로 번역됩니다. 스테이터스 메시지(slug)로 가능한 각각의 항목값은 `passwords` 언어 파일 내에 정의되어 있습니다.

Before moving on, you may be wondering how Laravel knows how to retrieve the user record from your application's database when calling the `Password` facade's `reset` method. The Laravel password broker utilizes your authentication system's "user providers" to retrieve database records. The user provider used by the password broker is configured within the `passwords` configuration array of your `config/auth.php` configuration file. To learn more about writing custom user providers, consult the [authentication documentation](/docs/{{version}}/authentication#adding-custom-user-providers).

계속하기에 앞서 라라벨이 `Password` 파사드의 `reset` 메소드를 호출할 때 애플리케이션 데이터베이스에서 사용자 레코드를 가져오는 방법을 어떻게 알고 있는지 궁금할 것입니다. 라라벨 비밀번호 브로커는 인증 시스템의 "user providers"를 활용하여 데이터베이스 레코드를 검색합니다. 비밀번호 브로커가 사용하는 user 프로바이더는 `config/auth.php` 설정 파일의 `passwords` 배열 내에서 설정됩니다. user 프로바이더를 커스텀하려면 [인증 문서](/docs/{{version}}/authentication#adding-custom-user-providers)를 참고 하십시오.

<a name="deleting-expired-tokens"></a>
## Deleting Expired Tokens
## 만료된 토큰 삭제하기

Password reset tokens that have expired will still be present within your database. However, you may easily delete these records using the `auth:clear-resets` Artisan command:

만료된 비밀번호 재설정 토큰은 데이터베이스에 계속 존재합니다. 그러나 `auth:clear-resets` Artisan 명령을 사용하여 데이터베이스에 존재하는 만료된 토큰 기록을 손쉽게 삭제할 수 있습니다.

    php artisan auth:clear-resets

If you would like to automate this process, consider adding the command to your application's [scheduler](/docs/{{version}}/scheduling):

데이터베이스에 존재하는 만료된 토큰을 자동으로 삭제하려면 애플리케이션의 [스케줄러](/docs/{{version}}/scheduling)에 다음 코드을 추가하는 것이 좋습니다.

    $schedule->command('auth:clear-resets')->everyFifteenMinutes();

<a name="password-customization"></a>
## Customization
## 커스터마이징

<a name="reset-link-customization"></a>
#### Reset Link Customization
#### 재설정 링크를 사용자 정의하기

You may customize the password reset link URL using the `createUrlUsing` method provided by the `ResetPassword` notification class. This method accepts a closure which receives the user instance that is receiving the notification as well as the password reset link token. Typically, you should call this method from your `App\Providers\AuthServiceProvider` service provider's `boot` method:

`ResetPassword` notification 클래스에서 제공하는 `createUrlUsing` 메소드를 사용하여 비밀번호 재설정 링크의 URL을 커스터마이징할 수 있습니다. `createUrlUsing` 메소드는 클로저를 인자로 받습니다. 이 클로저는 user 인스턴스와 비밀번호 재설정 링크의 토큰을 인자로 받습니다. 유저 인스턴스는 알람(notification)을 받을 사용자를 선택하는 역할을 합니다. 일반적으로 `ResetPassword` 클래스의 `createUrlUsing` 메소드는 `App\Providers\AuthServiceProvider` 서비스 프로바이더의 `boot` 메서드에서 호출해야 합니다.

    use Illuminate\Auth\Notifications\ResetPassword;

    /**
     * Register any authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();

        ResetPassword::createUrlUsing(function ($user, string $token) {
            return 'https://example.com/reset-password?token='.$token;
        });
    }

#### Reset Email Customization
#### 패스워드 재설정 이메일 커스터마이징하기

You may easily modify the notification class used to send the password reset link to the user. To get started, override the `sendPasswordResetNotification` method on your `App\Models\User` model. Within this method, you may send the notification using any [notification class](/docs/{{version}}/notifications) of your own creation. The password reset `$token` is the first argument received by the method. You may use this `$token` to build the password reset URL of your choice and send your notification to the user:

비밀번호 재설정 링크를 보내기 위해서는 notification 클래스를 사용해야 합니다. notification 클래스는  원하는대로 쉽게 수정할 수 있습니다. notification 클래스를 수정하려면 `App\Models\User` 모델에서 `sendPasswordResetNotification` 메소드를 오버라이드 해야 합니다. [notification class](/docs/{{version}}/notifications)를 사용하여 이 메소드를 오버라이딩 하면 어떠한 알람 (notification)이라도 보낼 수 있습니다. 비밀번호 재설정 메소드의 첫번째 인자는 `$token`을 전달 받습니다. 전달된 `$token`을 사용하여 원하는 비밀번호 재설정 URL을 만들고 사용자에게 알림(notification)을 보낼 수 있습니다.

    use App\Notifications\ResetPasswordNotification;

    /**
     * Send a password reset notification to the user.
     *
     * @param  string  $token
     * @return void
     */
    public function sendPasswordResetNotification($token)
    {
        $url = 'https://example.com/reset-password?token='.$token;

        $this->notify(new ResetPasswordNotification($url));
    }
