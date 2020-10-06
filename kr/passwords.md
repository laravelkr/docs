# Resetting Passwords
# 패스워드 재설정하기

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Database Considerations](#resetting-database)
- [데이터베이스 고려사항](#resetting-database)
- [Routing](#resetting-routing)
- [라우팅](#resetting-routing)
- [Views](#resetting-views)
- [뷰](#resetting-views)
- [Customization](#password-customization)
- [커스터마이징](#password-customization)

<a name="introduction"></a>
## Introduction
## 시작하기

Most web applications provide a way for users to reset their forgotten passwords. Rather than forcing you to re-implement this on each application, Laravel provides convenient methods for sending password reminders and performing password resets.

대부분의 웹 애플리케이션은 사용자가 그들이 잊어버린 패스워드를 재설정 할 수 있는 방법을 제공합니다. 각각의 애플리케이션에서 매번 이를 다시 구현하는 것을 대신해서 라라벨은 패스워드 분실 이메일을 보내고 패스워드 재설정을 수행할 수 있는 편리한 방법를 제공합니다.

> {note} Before using the password reset features of Laravel, your user must use the `Illuminate\Notifications\Notifiable` trait.

> {note} 라라벨의 패스워드 재설정 기능을 사용하기 전에, 여러분의 사용자 모델은 `Illuminate\Notifications\Notifiable` 트레이트를 사용하고 있어야 합니다.

#### Getting Started Fast
#### 빠르게 시작하기

Want to get started fast? Install [Laravel Jetstream](https://jetstream.laravel.com) in a fresh Laravel application. After migrating your database, navigate your browser to `/register` or any other URL that is assigned to your application. Jetstream will take care of scaffolding your entire authentication system, including resetting passwords!

빠르게 시작하고 싶으세요? 새로운 라라벨 애플리케이션에 [Laravel Jetstream](https://jetstream.laravel.com)을 설치합니다. 데이터베이스를 마이그레이션 한 후 브라우저에서 `/register` 또는 애플리케이션에 할당 된 다른 URL로 이동합니다. Jetstream은 비밀번호 재설정을 포함하여 전체 인증 시스템을 스캐 폴딩합니다!

<a name="resetting-database"></a>
## Database Considerations
## 데이터베이스 고려사항

To get started, verify that your `App\Models\User` model implements the `Illuminate\Contracts\Auth\CanResetPassword` contract. The `App\Models\User` model included with the framework already implements this interface, and uses the `Illuminate\Auth\Passwords\CanResetPassword` trait to include the methods needed to implement the interface.

시작하기에 앞서, 여러분의 `App\Models\User` 모델이 `Illuminate\Contracts\Auth\CanResetPassword` contract를 구현하고 있는지 확인하십시오. 라라벨에 포함되어 있는 `App\Models\User` 모델은 이미 이 인터페이스를 구현하고 있으며 인터페이스 구현에 필요한 메소드를 포함하고 있는 `Illuminate\Auth\Passwords\CanResetPassword` 트레이트를 사용하고 있습니다.

#### Generating The Reset Token Table Migration
#### 재설정 토큰을 저장하는 테이블 마이그레이션 파일 생성하기

Next, a table must be created to store the password reset tokens. The migration for this table is included in the default Laravel installation, so you only need to migrate your database to create this table:

다음으로 암호 재설정 토큰을 저장하기위한 테이블을 만들어야합니다. 이 테이블에 대한 마이그레이션은 기본 라라벨 설치에 포함되어 있으므로 이 테이블을 생성하려면 데이터베이스를 마이그레이션만 하면됩니다.

    php artisan migrate

<a name="resetting-routing"></a>
## Routing
## 라우팅

All of the routes needed to perform password resets are automatically included in [Laravel Jetstream](https://jetstream.laravel.com). To learn how to install Jetstream, please consult the official [Jetstream documentation](https://jetstream.laravel.com).

비밀번호 재설정을 수행하는 데 필요한 모든 라우트는 [Laravel Jetstream](https://jetstream.laravel.com)에 포함되어 있습니다. Jetstream 설치 방법을 알아 보려면 공식 [Jetstream 문서](https://jetstream.laravel.com)를 참조하세요.

<a name="resetting-views"></a>
## Views
## 뷰

All of the views needed to perform password resets are automatically included in [Laravel Jetstream](https://jetstream.laravel.com). To learn how to install Jetstream, please consult the official [Jetstream documentation](https://jetstream.laravel.com).

비밀번호 재설정을 수행하는 데 필요한 모든 뷰는 [Laravel Jetstream](https://jetstream.laravel.com)에 포함되어 있습니다. Jetstream 설치 방법을 알아 보려면 공식 [Jetstream 문서](https://jetstream.laravel.com)를 참조하세요.

<a name="password-customization"></a>
## Customization
## 커스터마이징

#### Reset Email Customization
#### 패스워드 재설정 이메일 커스터마이징하기

You may easily modify the notification class used to send the password reset link to the user. To get started, override the `sendPasswordResetNotification` method on your `User` model. Within this method, you may send the notification using any notification class you choose. The password reset `$token` is the first argument received by the method:

패스워드를 재설정하는 링크를 사용자에게 보내기 위해서 사용되는 알림 클래스를 손쉽게 수정할 수 있습니다. 먼저, `User` 모델의 `sendPasswordResetNotification` 메소드를 오버라이딩 해야합니다. 이 메소드 안에서 여러분이 선택한 알림 클래스를 사용하여 알림을 보낼 수 있습니다. 패스워드 재설정 `$token` 은 메소드가 받아 들이는 첫번째 인자 입니다.

    /**
     * Send the password reset notification.
     *
     * @param  string  $token
     * @return void
     */
    public function sendPasswordResetNotification($token)
    {
        $this->notify(new ResetPasswordNotification($token));
    }
