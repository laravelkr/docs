# 이메일 검증

- [시작하기](#introduction)
- [데이터베이스 고려사항](#verification-database)
- [라우팅](#verification-routing)
    - [라우트 보호하기](#protecting-routes)
- [뷰](#verification-views)
- [이메일 검증 이후](#after-verifying-emails)
- [이벤트](#events)

<a name="introduction"></a>
## 시작하기

많은 웹 애플리케이션에서 사용자는 애플리케이션을 사용하기 전에 이메일 주소를 확인해야합니다. Laravel은 각 애플리케이션에서 이를 다시 구현하지 않고 이메일 검증 요청을 보내고 검증하는 편리한 방법을 제공합니다.

### 모델 준비사항

시작하려면 `App\User` 모델이 `Illuminate\Contracts\Auth\MustVerifyEmail` contract을 구현하는지 확인하십시오 :


    <?php

    namespace App;

    use Illuminate\Notifications\Notifiable;
    use Illuminate\Contracts\Auth\MustVerifyEmail;
    use Illuminate\Foundation\Auth\User as Authenticatable;

    class User extends Authenticatable implements MustVerifyEmail
    {
        use Notifiable;

        // ...
    }

<a name="verification-database"></a>
## 데이터베이스 고려사항

#### 이메일 검증 컬럼

다음으로, `user` 테이블은 이메일 주소가 검증 된 날짜와 시간을 저장하는 `email_verified_at` 컬럼을 포함해야합니다. 기본적으로, Laravel 프레임워크에 포함 된 `users` 테이블 마이그레이션은 이미 이 컬럼을 포함합니다. 따라서 데이터베이스 마이그레이션을 실행하기만 하면됩니다.

    php artisan migrate

<a name="verification-routing"></a>
## 라우팅

Laravel은 확인 링크를 보내고 이메일을 확인하는 데 필요한 로직을 포함하는 `Auth\VerificationController` 클래스를 포함하고 있습니다. 이 컨트롤러에 사용하기 위해 필요한 라우트를 등록하려면 `Auth::routes` 메소드에 `verify` 옵션을 넘깁니다 :

    Auth::routes(['verify' => true]);

<a name="protecting-routes"></a>
### 라우트 보호하기

[Route middleware](/docs/{{version}}/middleware)는 검증 된 사용자만 주어진 경로에 액세스 할 수있게 허용합니다. Laravel은 `verified` 미들웨어를 가지고 있으며 `Illuminate\Auth\Middleware\EnsureEmailIsVerified` 에 정의되어 있습니다. 이 미들웨어는 이미 애플리케이션의 HTTP 커널에 등록되어 있기 때문에 미들웨어를 라우트 정의에 추가하면 됩니다.

    Route::get('profile', function () {
        // Only verified users may enter...
    })->middleware('verified');

<a name="verification-views"></a>
## 뷰

Laravel은 `make:auth` 명령이 실행될 때 필요한 모든 이메일 검증 뷰를 생성합니다. 이 뷰는 `resources/views/auth/verify.blade.php` 에 있습니다. 필요에 따라 이 뷰를 자유롭게 커스터마이징 할 수 있습니다.

<a name="after-verifying-emails"></a>
## 이메일 검증 이후

이메일 주소가 확인되면 사용자는 자동으로 `/home`으로 리디렉션됩니다. `VerificationController` 에 `redirectTo` 메소드 나 프로퍼티를 정의하여 post verification redirect 위치를 커스터마이징 할 수 있습니다 :

    protected $redirectTo = '/dashboard';

<a name="events"></a>
## 이벤트

라라벨은 이메일 검증 과정에서 [이벤트](/docs/{{version}}/events)를 발생시킵니다. `EventServiceProvider` 에서 이 이벤트에 리스너를 붙일 수 있습니다.

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
