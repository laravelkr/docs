# Email Verification
# 이메일 검증

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [Model Preparation](#model-preparation)
    - [모델 준비사항](#model-preparation)
    - [Database Preparation](#database-preparation)
    - [데이터베이스 준비사항](#database-preparation)
- [Routing](#verification-routing)
- [라우팅](#verification-routing)
    - [The Email Verification Notice](#the-email-verification-notice)
    - [이메일 검증 발송](#the-email-verification-notice)
    - [The Email Verification Handler](#the-email-verification-handler)
    - [이메일 검증 핸들러](#the-email-verification-handler)
    - [Resending The Verification Email](#resending-the-verification-email)
    - [이메일 검증 재발송](#resending-the-verification-email)
    - [Protecting Routes](#protecting-routes)
    - [라우트 보호하기](#protecting-routes)
- [Customization](#customization)
- [사용자 정의](#customization)
- [Events](#events)
- [이벤트](#events)

<a name="introduction"></a>
## Introduction
## 시작하기

Many web applications require users to verify their email addresses before using the application. Rather than forcing you to re-implement this feature by hand for each application you create, Laravel provides convenient built-in services for sending and verifying email verification requests.

많은 웹 애플리케이션들이 사용자에게 사용 전 이메일 검증을 요구합니다. 라라벨은 여러분이 생성하는 애플리케이션에서 이 기능을 쉽게 구현할 수 있도록 편리한 방법을 제공합니다.

> {tip} Want to get started fast? Install one of the [Laravel application starter kits](/docs/{{version}}/starter-kits) in a fresh Laravel application. The starter kits will take care of scaffolding your entire authentication system, including email verification support.

> {tip} 빠르게 적용하고 싶으신가요? 새로운 라라벨 애플리케이션에 [Laravel application starter kits](/docs/{{version}}/starter-kits)을 설치합니다. Starter kits은 이메일 검증과 여러 지원을 포함하여 인증 시스템 전체를 스캐 폴딩합니다!

<a name="model-preparation"></a>
## Model Preparation
## 모델 준비사항

Before getting started, verify that your `App\Models\User` model implements the `Illuminate\Contracts\Auth\MustVerifyEmail` contract:

시작하기 앞서 `App\Models\User` 모델이 `Illuminate\Contracts\Auth\MustVerifyEmail` contract을 구현하는지 확인하십시오.

    <?php

    namespace App\Models;

    use Illuminate\Contracts\Auth\MustVerifyEmail;
    use Illuminate\Foundation\Auth\User as Authenticatable;
    use Illuminate\Notifications\Notifiable;

    class User extends Authenticatable implements MustVerifyEmail
    {
        use Notifiable;

        // ...
    }

Once this interface has been added to your model, newly registered users will automatically be sent an email containing an email verification link. As you can see by examining your application's `App\Providers\EventServiceProvider`, Laravel already contains a `SendEmailVerificationNotification` [listener](/docs/{{version}}/events) that is attached to the `Illuminate\Auth\Events\Registered` event. This event listener will send the email verification link to the user.

이 인터페이스가 모델에 추가되면 새로 등록된 사용자에게 이메일 검증 링크가 포함된 이메일이 자동으로 전송됩니다. 여러분의 애플리케이션의 `App\Providers\EventServiceProvider` 서비스 프로바이더를 확인하면 라라벨이 `Illuminate\Auth\Events\Registered` 이벤트에 등록된 `SendEmailVerificationNotification` [리스너](/docs/{{version}}/events)를 가져오는 것을 확인 할 수 있습니다. 이 이벤트 리스너는 사용자에게 이메일 검증 링크를 보냅니다.

If you are manually implementing registration within your application instead of using [a starter kit](/docs/{{version}}/starter-kits), you should ensure that you are dispatching the `Illuminate\Auth\Events\Registered` event after a user's registration is successful:

만약 여러분이 [starter kit](/docs/{{version}}/starter-kits)를 사용하는 대신 애플리케이션에 직접 코드를 입력하는 경우, 새로운 사용자가 생성될 때 `Illuminate\Auth\Events\Registered` 이벤트를 보내도록 처리하여야 합니다.

    use Illuminate\Auth\Events\Registered;

    event(new Registered($user));

<a name="database-preparation"></a>
### Database Preparation
### 데이터베이스 준비사항

Next, your `users` table must contain an `email_verified_at` column to store the date and time that the user's email address was verified. By default, the `users` table migration included with the Laravel framework already includes this column. So, all you need to do is run your database migrations:

다음으로 여러분의 `users` 테이블은 사용자의 이메일 주소가 검증 된 날짜와 시간을 저장하는 `email_verified_at` 컬럼을 포함해야 합니다. 라라벨에 기본으로 포함되어 있는 `users` 테이블 마이그레이션에는 이미 이 컬럼이 포함되어 있습니다. 따라서 데이터베이스 마이그레이션을 실행하기만 하면 됩니다.

    php artisan migrate

<a name="verification-routing"></a>
## Routing
## 라우팅

To properly implement email verification, three routes will need to be defined. First, a route will be needed to display a notice to the user that they should click the email verification link in the verification email that Laravel sent them after registration.

이메일 검증을 올바르게 구현하려면 세 가지 라우트를 정의해야합니다. 첫째, 새로운 사용자가 생성된 후 사용자에게 라라벨이 보낸 검증 이메일을 확인 하도록 알려주는 라우터가 필요합니다.

Second, a route will be needed to handle requests generated when the user clicks the email verification link in the email.

둘째, 사용자가 이메일 검증 링크를 클릭 할 때 생성 된 요청을 처리하는 라우터가 필요합니다.

Third, a route will be needed to resend a verification link if the user accidentally loses the first verification link.

셋째, 이메일 검증 링크가 만료되거나 접근에 문제가 있는 경우 사용자에게 인증 링크를 재생성 후 발송하는 라우터가 필요합니다.

<a name="the-email-verification-notice"></a>
### The Email Verification Notice
### 이메일 검증 링크 발송 

As mentioned previously, a route should be defined that will return a view instructing the user to click the email verification link that was emailed to them by Laravel after registration. This view will be displayed to users when they try to access other parts of the application without verifying their email address first. Remember, the link is automatically emailed to the user as long as your `App\Models\User` model implements the `MustVerifyEmail` interface:

앞서 언급한 것처럼 새로운 사용자가 생성된 후 라벨이 보낸 이메일 검증링크를 사용자가 확인할 수 있도록 알려주는 뷰를 반환하는 라우터를 정의해야 합니다. 이 뷰는 사용자의 이메일이 검증되지 않은 상태에서 웹 애플리케이션 특정 라우터에 접근하려고 할 때 사용자에게 이메일 확인 요청을 합니다. 기억하세요 `App\Models\User` 모델에 `MusterVerifyEmail` 인터페이스를 구현하여야 새로운 사용자 생성될 때 이메일 검증 링크가 자동으로 발송됩니다.

    Route::get('/email/verify', function () {
        return view('auth.verify-email');
    })->middleware('auth')->name('verification.notice');

> {tip} When manually implementing email verification, you are required to define the contents of the verification notice view yourself. If you would like scaffolding that includes all necessary authentication and verification views, check out the [Laravel application starter kits](/docs/{{version}}/starter-kits).

> {tip} 이메일 검증을 수동으로 구현할 때 이메일 검증 링크 확인을 요청하는 뷰는 여러분이 직접 정의 해야 합니다. 필요한 모든 검증 및 검증 요청 뷰가 포함 된 스캐폴딩을 원하신다면 [Laravel application starter kits](/docs/{{version}}/starter-kits)를 확인하십시오.

<a name="the-email-verification-handler"></a>
### The Email Verification Handler
### 이메일 검증 핸들러

Next, we need to define a route that will handle requests generated when the user clicks the email verification link that was emailed to them. This route should be named `verification.verify` and be assigned the `auth` and `signed` middlewares:

다음으로 사용자가 이메일로 받은 검증 링크를 클릭 할 때 생성 된 요청을 처리 할 라우터를 정의 해야 합니다. 이 라우터의 name은 `verification.verify`로 정의하고 `auth` 및 `signed` 미들웨어를 할당해야 합니다.

    use Illuminate\Foundation\Auth\EmailVerificationRequest;

    Route::get('/email/verify/{id}/{hash}', function (EmailVerificationRequest $request) {
        $request->fulfill();

        return redirect('/home');
    })->middleware(['auth', 'signed'])->name('verification.verify');

Before moving on, let's take a closer look at this route. First, you'll notice we are using an `EmailVerificationRequest` request type instead of the typical `Illuminate\Http\Request` instance. The `EmailVerificationRequest` is a [form request](/docs/{{version}}/validation#form-request-validation) that is included with Laravel. This request will automatically take care of validating the request's `id` and `hash` parameters

계속하기 전에, 이 라우터를 자세히 살펴 보겠습니다. 첫번째로 여러분은 request type이 `Illuminate\Http\Request` 대신 `EmailVerificationRequest` 인스턴트를 사용하는 것을 알 수 있습니다. 이 `EmailVerificationRequest` 인스턴스는 [form request](/docs/{{version}}/validation#form-request-validation)이고 라라벨에 포함되어 있습니다. 이 request-요청은 `id`, `hash` 파라메터 값의 유효성 검사를 자동으로 처리합니다.

Next, we can proceed directly to calling the `fulfill` method on the request. This method will call the `markEmailAsVerified` method on the authenticated user and dispatch the `Illuminate\Auth\Events\Verified` event. The `markEmailAsVerified` method is available to the default `App\Models\User` model via the `Illuminate\Foundation\Auth\User` base class. Once the user's email address has been verified, you may redirect them wherever you wish.

다음으로 request-요청에서 `fulfill` 메소드를 직접 호출하여 진행할 수 있습니다. 이 메소드는 `markEmailAsVerified` 메소드를 호출하고 검증된 사용자를 `Illuminate\Auth\Events\Verified` 이벤트에 전달 합니다. 이 `markEmailAsVerified` 메소드는 기본 클래스 `Illuminate\Foundation\Auth\User`를 통해 `App\Models\User` 모델에서 기본적으로 사용할 수 있습니다. 사용자의 이메일 검증이 완료되면 여러분이 원하시는 라우터로 이메일이 검증된 사용자들을 리다이렉트 시킬 수 있습니다.

<a name="resending-the-verification-email"></a>
### Resending The Verification Email
### 이메일 검증 재발송

Sometimes a user may misplace or accidentally delete the email address verification email. To accommodate this, you may wish to define a route to allow the user to request that the verification email be resent. You may then make a request to this route by placing a simple form submission button within your [verification notice view](#the-email-verification-notice):

경우에 따라 사용자가 이메일 주소 확인 이메일을 잘못 보관하거나 실수로 삭제할 수 있습니다. 이 문제를 해결하기 위해 사용자가 검증 이메일 재전송을 요청할 수 있는 라우터를 정의할 수 있습니다. 그런 다음 [verification notice view](#the-email-verification-notice) 내에 간단한 form submit 버튼을 추가하여 이 라우터에 요청할 수 있습니다.

    use Illuminate\Http\Request;

    Route::post('/email/verification-notification', function (Request $request) {
        $request->user()->sendEmailVerificationNotification();

        return back()->with('message', 'Verification link sent!');
    })->middleware(['auth', 'throttle:6,1'])->name('verification.send');

<a name="protecting-routes"></a>
### Protecting Routes
### 라우트 보호하기

[Route middleware](/docs/{{version}}/middleware) may be used to only allow verified users to access a given route. Laravel ships with a `verified` middleware, which references the `Illuminate\Auth\Middleware\EnsureEmailIsVerified` class. Since this middleware is already registered in your application's HTTP kernel, all you need to do is attach the middleware to a route definition:

[Route middleware](/docs/{{version}}/middleware)는 이메일이 검증된 사용자만 주어진 라우터에 접근할 수 있게 허용합니다. 라라벨은 `verified` 미들웨어를 가지고 있으며 `Illuminate\Auth\Middleware\EnsureEmailIsVerified`에 정의되어 있습니다. 이 미들웨어는 이미 애플리케이션의 HTTP 커널에 등록되어 있기 때문에 미들웨어를 라우트 정의에 추가하면 됩니다.

    Route::get('/profile', function () {
        // Only verified users may access this route...
    })->middleware('verified');

<a name="customization"></a>
## Customization
## 사용자 정의

<a name="verification-email-customization"></a>
#### Verification Email Customization
#### 이메일 검증 사용자 지정

Although the default email verification notification should satisfy the requirements of most applications, Laravel allows you to customize how the email verification mail message is constructed.

라라벨은 이메일 검증 메일이 구성되는 방식을 여러분이 지정할 수 있도록 허용합니다.

To get started, pass a closure to the `toMailUsing` method provided by the `Illuminate\Auth\Notifications\VerifyEmail` notification. The closure will receive the notifiable model instance that is receiving the notification as well as the signed email verification URL that the user must visit to verify their email address. The closure should return an instance of `Illuminate\Notifications\Messages\MailMessage`. Typically, you should call the `toMailUsing` method from the `boot` method of your application's `App\Providers\AuthServiceProvider` class:

이 기능을 사용하려면 먼저, `Illuminate\Auth\Notifications\VerifyEmail` 알림에서 제공하는 `toMailUsing` 메소드에 클로저를 전달하세요. 클로저는 알림을 수신하는 알림 가능한 모델 인스턴스 및 사용자가 이메일 주소를 검증하기 위해 방문해야 하는 서명된 이메일 검증 URL을 수신합니다. 클로저는 `Illuminate\Notifications\Messages\MailMessage`의 인스턴스를 return 합니다. 여러분의 애플리케이션 클래스 중 `App\Providers\AuthServiceProvider` 클래스의 `boot` 메소드에서 `toMailUsing` 메소드를 호출해야 합니다.


    use Illuminate\Auth\Notifications\VerifyEmail;
    use Illuminate\Notifications\Messages\MailMessage;

    /**
     * Register any authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        // ...

        VerifyEmail::toMailUsing(function ($notifiable, $url) {
            return (new MailMessage)
                ->subject('Verify Email Address')
                ->line('Click the button below to verify your email address.')
                ->action('Verify Email Address', $url);
        });
    }

> {tip} To learn more about mail notifications, please consult the [mail notification documentation](/docs/{{version}}/notifications#mail-notifications).

> {tip} 메일 알림에 대한 자세한 내용은 [mail notification documentation](/docs/{{version}}/notifications#mail-notifications)에서 참조하세요.

<a name="events"></a>
## Events
## 이벤트

When using the [Laravel application starter kits](/docs/{{version}}/starter-kits), Laravel dispatches [events](/docs/{{version}}/events) during the email verification process. If you are manually handling email verification for your application, you may wish to manually dispatch these events after verification is completed. You may attach listeners to these events in your application's `EventServiceProvider`:

[Laravel application starter kits](/docs/{{version}}/starter-kits)을 사용할 때 라라벨은 이메일 검증 과정에서 [이벤트](/docs/{{version}}/events)를 전달합니다. 여러분의 애플리케이션에 대한 이메일 검증을 수동으로 처리하는 경우, 이메일 검증 완료 이벤트를 수동으로 전달할 수 있습니다. 여러분 애플리케이션의 `EventServiceProvider`에서 이벤트에 리스너를 연결할 수 있습니다.

    /*
     * The event listener mappings for the application.
     *
     * @var array
     */
    protected $listen = [
        'Illuminate\Auth\Events\Verified' => [
            'App\Listeners\LogVerifiedUser',
        ],
    ];