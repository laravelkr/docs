# 이메일 검증

- [시작하기](#introduction)
- [모델 준비사항](#model-preparation)
- [데이터베이스 고려사항](#verification-database)
- [라우팅](#verification-routing)
    - [라우트 보호하기](#protecting-routes)
- [뷰](#verification-views)
- [이벤트](#events)

<a name="introduction"></a>
## 시작하기

많은 웹 애플리케이션에서 사용자는 애플리케이션을 사용하기 전에 이메일 주소를 확인해야합니다. 라라벨은 각 애플리케이션에서 이를 다시 구현하지 않고 이메일 검증 요청을 보내고 검증하는 편리한 방법을 제공합니다.

#### 빠르게 시작하기

빨리 시작하고 싶으세요? 새로운 Laravel 애플리케이션에 [Laravel Jetstream](https://jetstream.laravel.com)을 설치합니다. 데이터베이스를 마이그레이션 한 후 브라우저에서 `/register` 또는 애플리케이션에 할당 된 다른 URL로 이동합니다. Jetstream은 이메일 확인 지원을 포함하여 전체 인증 시스템을 스캐 폴딩합니다!

<a name="model-preparation"></a>
## 모델 준비사항

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
### 데이터베이스 고려사항

#### 이메일 검증 컬럼

`user` 테이블은 이메일 주소가 검증 된 날짜와 시간을 저장하는 `email_verified_at` 컬럼을 포함해야 합니다. 라라벨에 기본으로 포함되어 있는 `users` 테이블 마이그레이션에는 이미 이 컬럼이 포함되어 있습니다. 따라서 데이터베이스 마이그레이션을 실행하기만 하면됩니다.

    php artisan migrate

<a name="verification-routing"></a>
## 라우팅

이메일 확인에 필요한 모든 라우트는 [Laravel Jetstream](https://jetstream.laravel.com)에 포함되어 있습니다. Jetstream 설치 방법을 알아 보려면 공식 [Jetstream 문서](https://jetstream.laravel.com/1.x/features/authentication.html#email-verification) 를 참조하세요.

<a name="protecting-routes"></a>
### 라우트 보호하기

[Route middleware](/docs/{{version}}/middleware)는 검증된 사용자만 주어진 경로에 접근할 수 있게 허용합니다. 라라벨은 `verified` 미들웨어를 가지고 있으며 `Illuminate\Auth\Middleware\EnsureEmailIsVerified` 에 정의되어 있습니다. 이 미들웨어는 이미 애플리케이션의 HTTP 커널에 등록되어 있기 때문에 미들웨어를 라우트 정의에 추가하면 됩니다.

    Route::get('profile', function () {
        // Only verified users may enter...
    })->middleware('verified');

<a name="verification-views"></a>
## 뷰

이메일 확인에 필요한 모든 뷰는 [Laravel Jetstream](https://jetstream.laravel.com)에 포함되어 있습니다. Jetstream 설치 방법을 알아 보려면 공식 [Jetstream 문서](https://jetstream.laravel.com/1.x/installation.html) 를 참조하세요.

<a name="events"></a>
## 이벤트

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
