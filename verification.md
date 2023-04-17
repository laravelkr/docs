# Email Verification

- [Email Verification](#email-verification)
  - [Introduction](#introduction)
  - [소개](#소개)
    - [Model Preparation](#model-preparation)
    - [모델 준비](#모델-준비)
    - [Database Preparation](#database-preparation)
    - [데이터베이스 준비](#데이터베이스-준비)
  - [Routing](#routing)
  - [라우팅](#라우팅)
    - [The Email Verification Notice](#the-email-verification-notice)
    - [이메일 인증 공지](#이메일-인증-공지)
    - [The Email Verification Handler](#the-email-verification-handler)
    - [이메일 인증 핸들러](#이메일-인증-핸들러)
    - [Resending The Verification Email](#resending-the-verification-email)
    - [인증 이메일 재전송](#인증-이메일-재전송)
    - [Protecting Routes](#protecting-routes)
    - [라우트 보호](#라우트-보호)
  - [Customization](#customization)
  - [커스터마이징](#커스터마이징)
      - [Verification Email Customization](#verification-email-customization)
      - [인증 이메일 커스터마이징](#인증-이메일-커스터마이징)
  - [Events](#events)
  - [이벤트](#이벤트)

<a name="introduction"></a>
## Introduction
## 소개

Many web applications require users to verify their email addresses before using the application. Rather than forcing you to re-implement this feature by hand for each application you create, Laravel provides convenient built-in services for sending and verifying email verification requests.

많은 웹 어플리케이션은 사용자가 이메일 주소를 인증한 후에만 사용할 수 있도록 요구합니다. 각 어플리케이션마다 이 기능을 수동으로 다시 구현하는 것을 강요하지 않고, Laravel은 이메일 인증 요청을 보내고 인증하는 데 필요한 편리한 내장 서비스를 제공합니다.

> **Note**  
> Want to get started fast? Install one of the [Laravel application starter kits](/docs/{{version}}/starter-kits) in a fresh Laravel application. The starter kits will take care of scaffolding your entire authentication system, including email verification support.
>
> **참고**
> 빠르게 시작하고 싶으세요? 새로운 Laravel 어플리케이션에 [Laravel application starter kits](/docs/{{version}}/starter-kits) 중 하나를 설치하세요. 스타터 키트는 이메일 인증 지원을 포함한 전체 인증 시스템을 구성해줍니다.

<a name="model-preparation"></a>
### Model Preparation
### 모델 준비

Before getting started, verify that your `App\Models\User` model implements the `Illuminate\Contracts\Auth\MustVerifyEmail` contract:

시작하기 전에 `App\Models\User` 모델이 `Illuminate\Contracts\Auth\MustVerifyEmail` 계약을 구현하는지 확인하세요.

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

이 인터페이스가 모델에 추가되면 새로 등록된 사용자는 이메일 인증 링크를 포함한 이메일을 자동으로 보냅니다. 애플리케이션의 `App\Providers\EventServiceProvider`를 검사하여 보면 Laravel에는 이미 `Illuminate\Auth\Events\Registered` 이벤트에 연결된 `SendEmailVerificationNotification` [리스너](/docs/{{version}}/events)가 있습니다. 이 이벤트 리스너는 이메일 인증 링크를 사용자에게 보냅니다.

If you are manually implementing registration within your application instead of using [a starter kit](/docs/{{version}}/starter-kits), you should ensure that you are dispatching the `Illuminate\Auth\Events\Registered` event after a user's registration is successful:

애플리케이션에서 [스타터 키트](/docs/{{version}}/starter-kits)를 사용하는 대신 수동으로 등록을 구현하는 경우, 사용자의 등록이 성공한 후 `Illuminate\Auth\Events\Registered` 이벤트를 디스패치하는지 확인하세요.

    use Illuminate\Auth\Events\Registered;

    event(new Registered($user));

<a name="database-preparation"></a>
### Database Preparation
### 데이터베이스 준비

Next, your `users` table must contain an `email_verified_at` column to store the date and time that the user's email address was verified. By default, the `users` table migration included with the Laravel framework already includes this column. So, all you need to do is run your database migrations:

다음으로, `users` 테이블에는 사용자의 이메일 주소가 인증된 날짜와 시간을 저장할 `email_verified_at` 컬럼이 있어야 합니다. 기본적으로 Laravel 프레임워크에 포함된 `users` 테이블 마이그레이션에는 이미 이 컬럼이 포함되어 있습니다. 따라서 데이터베이스 마이그레이션만 실행하면 됩니다.

```shell
php artisan migrate
```

<a name="verification-routing"></a>
## Routing
## 라우팅

To properly implement email verification, three routes will need to be defined. First, a route will be needed to display a notice to the user that they should click the email verification link in the verification email that Laravel sent them after registration.

이메일 인증을 올바르게 구현하려면 세 개의 라우트를 정의해야 합니다. 먼저, 사용자에게 등록 후 Laravel이 보낸 인증 이메일의 인증 링크를 클릭해야 한다는 공지를 표시하는 라우트가 필요합니다.

Second, a route will be needed to handle requests generated when the user clicks the email verification link in the email.

두 번째로, 사용자가 이메일에서 이메일 인증 링크를 클릭할 때 생성되는 요청을 처리하는 라우트가 필요합니다.

Third, a route will be needed to resend a verification link if the user accidentally loses the first verification link.

세 번째로, 사용자가 실수로 첫 번째 인증 링크를 잃어버리면 인증 링크를 다시 보내는 라우트가 필요합니다.

<a name="the-email-verification-notice"></a>
### The Email Verification Notice
### 이메일 인증 공지

As mentioned previously, a route should be defined that will return a view instructing the user to click the email verification link that was emailed to them by Laravel after registration. This view will be displayed to users when they try to access other parts of the application without verifying their email address first. Remember, the link is automatically emailed to the user as long as your `App\Models\User` model implements the `MustVerifyEmail` interface:

이전에 언급했듯이, 등록 후 Laravel이 이메일로 보낸 이메일 인증 링크를 클릭하라는 안내를 표시하는 뷰를 반환하는 라우트를 정의해야 합니다. 이 뷰는 사용자가 이메일 주소를 먼저 확인하지 않고 애플리케이션의 다른 부분에 액세스하려고 할 때 표시됩니다. 기억하세요, `App\Models\User` 모델이 `MustVerifyEmail` 인터페이스를 구현하면 링크가 자동으로 사용자에게 이메일로 전송됩니다.

    Route::get('/email/verify', function () {
        return view('auth.verify-email');
    })->middleware('auth')->name('verification.notice');

The route that returns the email verification notice should be named `verification.notice`. It is important that the route is assigned this exact name since the `verified` middleware [included with Laravel](#protecting-routes) will automatically redirect to this route name if a user has not verified their email address.

이메일 인증 공지를 반환하는 라우트는 `verification.notice`로 이름을 지정해야 합니다. 이 라우트에 정확한 이름이 지정되어 있어야 하는 이유는 `verified` 미들웨어가 사용자가 이메일 주소를 확인하지 않은 경우 자동으로 이 라우트 이름으로 리디렉션하기 때문입니다.

> **Note**  
> When manually implementing email verification, you are required to define the contents of the verification notice view yourself. If you would like scaffolding that includes all necessary authentication and verification views, check out the [Laravel application starter kits](/docs/{{version}}/starter-kits).
>
> **참고**
> 이메일 인증을 수동으로 구현할 때는 인증 공지 뷰의 내용을 직접 정의해야 합니다. 필요한 모든 인증 및 인증 뷰를 포함하는 스캐폴딩을 원한다면 [Laravel 애플리케이션 스타터 키트](/docs/{{version}}/starter-kits)를 확인하세요.

<a name="the-email-verification-handler"></a>
### The Email Verification Handler
### 이메일 인증 핸들러

Next, we need to define a route that will handle requests generated when the user clicks the email verification link that was emailed to them. This route should be named `verification.verify` and be assigned the `auth` and `signed` middlewares:

다음으로, 사용자가 이메일로 보낸 이메일 인증 링크를 클릭할 때 생성되는 요청을 처리하는 라우트를 정의해야 합니다. 이 라우트는 `verification.verify`로 이름을 지정하고 `auth` 및 `signed` 미들웨어를 할당해야 합니다.

    use Illuminate\Foundation\Auth\EmailVerificationRequest;

    Route::get('/email/verify/{id}/{hash}', function (EmailVerificationRequest $request) {
        $request->fulfill();

        return redirect('/home');
    })->middleware(['auth', 'signed'])->name('verification.verify');

Before moving on, let's take a closer look at this route. First, you'll notice we are using an `EmailVerificationRequest` request type instead of the typical `Illuminate\Http\Request` instance. The `EmailVerificationRequest` is a [form request](/docs/{{version}}/validation#form-request-validation) that is included with Laravel. This request will automatically take care of validating the request's `id` and `hash` parameters.

다음으로, 이 라우트에 대해 자세히 살펴보겠습니다. 먼저, 일반적인 `Illuminate\Http\Request` 인스턴스 대신 `EmailVerificationRequest` 요청 유형을 사용하고 있다는 것을 알 수 있습니다. `EmailVerificationRequest`는 Laravel에 포함된 [폼 요청](/docs/{{version}}/validation#form-request-validation)입니다. 이 요청은 요청의 `id` 및 `hash` 매개변수를 자동으로 유효성 검사합니다.

Next, we can proceed directly to calling the `fulfill` method on the request. This method will call the `markEmailAsVerified` method on the authenticated user and dispatch the `Illuminate\Auth\Events\Verified` event. The `markEmailAsVerified` method is available to the default `App\Models\User` model via the `Illuminate\Foundation\Auth\User` base class. Once the user's email address has been verified, you may redirect them wherever you wish.

다음으로, 요청에 대해 `fulfill` 메서드를 직접 호출할 수 있습니다. 이 메서드는 인증된 사용자의 `markEmailAsVerified` 메서드를 호출하고 `Illuminate\Auth\Events\Verified` 이벤트를 디스패치합니다. `markEmailAsVerified` 메서드는 `Illuminate\Foundation\Auth\User` 기본 클래스를 통해 기본 `App\Models\User` 모델에서 사용할 수 있습니다. 사용자의 이메일 주소가 확인되면 원하는 곳으로 리디렉션할 수 있습니다.

<a name="resending-the-verification-email"></a>
### Resending The Verification Email
### 인증 이메일 재전송

Sometimes a user may misplace or accidentally delete the email address verification email. To accommodate this, you may wish to define a route to allow the user to request that the verification email be resent. You may then make a request to this route by placing a simple form submission button within your [verification notice view](#the-email-verification-notice):

때때로 사용자가 이메일 주소 인증 이메일을 실수로 삭제할 수 있습니다. 이를 수용하기 위해 인증 이메일을 재전송하도록 사용자가 요청할 수 있는 라우트를 정의할 수 있습니다. 그런 다음 [인증 공지 뷰](#the-email-verification-notice)에 간단한 폼 제출 버튼을 배치하여 이 라우트에 대한 요청을 만들 수 있습니다.

    use Illuminate\Http\Request;

    Route::post('/email/verification-notification', function (Request $request) {
        $request->user()->sendEmailVerificationNotification();

        return back()->with('message', 'Verification link sent!');
    })->middleware(['auth', 'throttle:6,1'])->name('verification.send');

<a name="protecting-routes"></a>
### Protecting Routes
### 라우트 보호

[Route middleware](/docs/{{version}}/middleware) may be used to only allow verified users to access a given route. Laravel ships with a `verified` middleware alias, which is an alias for the `Illuminate\Auth\Middleware\EnsureEmailIsVerified` class. Since this middleware is already registered in your application's HTTP kernel, all you need to do is attach the middleware to a route definition. Typically, this middleware is paired with the `auth` middleware:

[라우트 미들웨어](/docs/{{version}}/middleware)를 사용하여 인증된 사용자만 특정 라우트에 액세스할 수 있도록 할 수 있습니다. Laravel에는 `verified` 미들웨어 별칭이 있으며, 이는 `Illuminate\Auth\Middleware\EnsureEmailIsVerified` 클래스의 별칭입니다. 이 미들웨어는 이미 응용 프로그램의 HTTP 커널에 등록되어 있으므로 라우트 정의에 미들웨어를 연결하는 것만 하면 됩니다. 일반적으로 이 미들웨어는 `auth` 미들웨어와 함께 사용됩니다.

    Route::get('/profile', function () {
        // Only verified users may access this route...
    })->middleware(['auth', 'verified']);

If an unverified user attempts to access a route that has been assigned this middleware, they will automatically be redirected to the `verification.notice` [named route](/docs/{{version}}/routing#named-routes).

인증되지 않은 사용자가 이 미들웨어가 할당된 라우트에 액세스하려고 시도하면 자동으로 `verification.notice` [이름있는 라우트](/docs/{{version}}/routing#named-routes)로 리디렉션됩니다.

<a name="customization"></a>
## Customization
## 커스터마이징

<a name="verification-email-customization"></a>
#### Verification Email Customization
#### 인증 이메일 커스터마이징

Although the default email verification notification should satisfy the requirements of most applications, Laravel allows you to customize how the email verification mail message is constructed.

기본 이메일 인증 알림은 대부분의 응용 프로그램의 요구 사항을 충족시키지만, Laravel은 이메일 인증 메일 메시지가 구축되는 방식을 커스터마이징할 수 있도록 허용합니다.

To get started, pass a closure to the `toMailUsing` method provided by the `Illuminate\Auth\Notifications\VerifyEmail` notification. The closure will receive the notifiable model instance that is receiving the notification as well as the signed email verification URL that the user must visit to verify their email address. The closure should return an instance of `Illuminate\Notifications\Messages\MailMessage`. Typically, you should call the `toMailUsing` method from the `boot` method of your application's `App\Providers\AuthServiceProvider` class:

시작하려면 `Illuminate\Auth\Notifications\VerifyEmail` 알림에 의해 제공되는 `toMailUsing` 메서드에 클로저를 전달합니다. 클로저는 알림을 받는 모델 인스턴스와 사용자가 이메일 주소를 확인하기 위해 방문해야 하는 서명된 이메일 인증 URL을 받습니다. 클로저는 `Illuminate\Notifications\Messages\MailMessage` 인스턴스를 반환해야 합니다. 일반적으로 `App\Providers\AuthServiceProvider` 클래스의 `boot` 메서드에서 `toMailUsing` 메서드를 호출해야 합니다.

    use Illuminate\Auth\Notifications\VerifyEmail;
    use Illuminate\Notifications\Messages\MailMessage;

    /**
     * Register any authentication / authorization services.
     */
    public function boot(): void
    {
        // ...

        VerifyEmail::toMailUsing(function (object $notifiable, string $url) {
            return (new MailMessage)
                ->subject('Verify Email Address')
                ->line('Click the button below to verify your email address.')
                ->action('Verify Email Address', $url);
        });
    }

> **Note**  
> To learn more about mail notifications, please consult the [mail notification documentation](/docs/{{version}}/notifications#mail-notifications).
>
> **참고**
> 메일 알림에 대한 자세한 내용은 [메일 알림 문서](/docs/{{version}}/notifications#mail-notifications)를 참조하십시오.

<a name="events"></a>
## Events
## 이벤트

When using the [Laravel application starter kits](/docs/{{version}}/starter-kits), Laravel dispatches [events](/docs/{{version}}/events) during the email verification process. If you are manually handling email verification for your application, you may wish to manually dispatch these events after verification is completed. You may attach listeners to these events in your application's `EventServiceProvider`:

[Laravel 응용 프로그램 스타터 키트](/docs/{{version}}/starter-kits)를 사용할 때 Laravel은 이메일 인증 프로세스 중에 [이벤트](/docs/{{version}}/events)를 디스패치합니다. 응용 프로그램에서 이메일 인증을 수동으로 처리하는 경우 인증이 완료된 후 이 이벤트를 수동으로 디스패치할 수 있습니다. 응용 프로그램의 `EventServiceProvider`에서 이 이벤트에 리스너를 연결할 수 있습니다.

    use App\Listeners\LogVerifiedUser;
    use Illuminate\Auth\Events\Verified;
    
    /**
     * The event listener mappings for the application.
     *
     * @var array
     */
    protected $listen = [
        Verified::class => [
            LogVerifiedUser::class,
        ],
    ];
