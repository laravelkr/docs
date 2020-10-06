# Email Verification
# 이메일 검증

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Model Preparation](#model-preparation)
- [모델 준비사항](#model-preparation)
- [Database Considerations](#verification-database)
- [데이터베이스 고려사항](#verification-database)
- [Routing](#verification-routing)
- [라우팅](#verification-routing)
    - [Protecting Routes](#protecting-routes)
    - [라우트 보호하기](#protecting-routes)
- [Views](#verification-views)
- [뷰](#verification-views)
- [Events](#events)
- [이벤트](#events)

<a name="introduction"></a>
## Introduction
## 시작하기

Many web applications require users to verify their email addresses before using the application. Rather than forcing you to re-implement this on each application, Laravel provides convenient methods for sending and verifying email verification requests.

많은 웹 애플리케이션에서 사용자는 애플리케이션을 사용하기 전에 이메일 주소를 확인해야합니다. 라라벨은 각 애플리케이션에서 이를 다시 구현하지 않고 이메일 검증 요청을 보내고 검증하는 편리한 방법을 제공합니다.

#### Getting Started Fast

Want to get started fast? Install [Laravel Jetstream](https://jetstream.laravel.com) in a fresh Laravel application. After migrating your database, navigate your browser to `/register` or any other URL that is assigned to your application. Jetstream will take care of scaffolding your entire authentication system, including email verification support!

<a name="model-preparation"></a>
## Model Preparation
## 모델 준비사항

To get started, verify that your `App\Models\User` model implements the `Illuminate\Contracts\Auth\MustVerifyEmail` contract:

시작하려면 `App\Models\User` 모델이 `Illuminate\Contracts\Auth\MustVerifyEmail` contract을 구현하는지 확인하십시오.


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

<a name="verification-database"></a>
### Database Considerations
### 데이터베이스 고려사항

#### The Email Verification Column
#### 이메일 검증 컬럼

Next, your `user` table must contain an `email_verified_at` column to store the date and time that the email address was verified. By default, the `users` table migration included with the Laravel framework already includes this column. So, all you need to do is run your database migrations:

`user` 테이블은 이메일 주소가 검증 된 날짜와 시간을 저장하는 `email_verified_at` 컬럼을 포함해야 합니다. 라라벨에 기본으로 포함되어 있는 `users` 테이블 마이그레이션에는 이미 이 컬럼이 포함되어 있습니다. 따라서 데이터베이스 마이그레이션을 실행하기만 하면됩니다.

    php artisan migrate

<a name="verification-routing"></a>
## Routing
## 라우팅

All of the routes needed to perform email verification are automatically included in [Laravel Jetstream](https://jetstream.laravel.com). To learn how to install Jetstream, please consult the official [Jetstream documentation](https://jetstream.laravel.com/1.x/features/authentication.html#email-verification).

이메일 확인에 필요한 모든 라우트는 [Laravel Jetstream](https://jetstream.laravel.com)에 포함되어 있습니다. Jetstream 설치 방법을 알아 보려면 공식 [Jetstream 문서](https://jetstream.laravel.com/1.x/features/authentication.html#email-verification) 를 참조하세요.

<a name="protecting-routes"></a>
### Protecting Routes
### 라우트 보호하기

[Route middleware](/docs/{{version}}/middleware) can be used to only allow verified users to access a given route. Laravel ships with a `verified` middleware, which is defined at `Illuminate\Auth\Middleware\EnsureEmailIsVerified`. Since this middleware is already registered in your application's HTTP kernel, all you need to do is attach the middleware to a route definition:

[Route middleware](/docs/{{version}}/middleware)는 검증된 사용자만 주어진 경로에 접근할 수 있게 허용합니다. 라라벨은 `verified` 미들웨어를 가지고 있으며 `Illuminate\Auth\Middleware\EnsureEmailIsVerified` 에 정의되어 있습니다. 이 미들웨어는 이미 애플리케이션의 HTTP 커널에 등록되어 있기 때문에 미들웨어를 라우트 정의에 추가하면 됩니다.

    Route::get('profile', function () {
        // Only verified users may enter...
    })->middleware('verified');

<a name="verification-views"></a>
## Views
## 뷰

All of the views needed to perform email verification are automatically included in [Laravel Jetstream](https://jetstream.laravel.com). To learn how to install Jetstream, please consult the official [Jetstream documentation](https://jetstream.laravel.com).

이메일 확인에 필요한 모든 뷰는 [Laravel Jetstream](https://jetstream.laravel.com)에 포함되어 있습니다. Jetstream 설치 방법을 알아 보려면 공식 [Jetstream 문서](https://jetstream.laravel.com/1.x/installation.html) 를 참조하세요.

<a name="events"></a>
## Events
## 이벤트

Laravel dispatches [events](/docs/{{version}}/events) during the email verification process. You may attach listeners to these events in your `EventServiceProvider`:

라라벨은 이메일 검증 과정에서 [이벤트](/docs/{{version}}/events)를 발생시킵니다. `EventServiceProvider`에서 이 이벤트에 리스너를 지정할 수 있습니다.

    /**
     * The event listener mappings for the application.
     *
     * @var array
     */
    protected $listen = [
        'Illuminate\Auth\Events\Verified' => [
            'App\Listeners\LogVerifiedUser',
        ],
    ];
