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
- [After Resetting Passwords](#after-resetting-passwords)
- [패스워드 재설정 이후 처리사항](#after-resetting-passwords)
- [Customization](#password-customization)
- [커스터마이징](#password-customization)

<a name="introduction"></a>
## Introduction
## 시작하기

> {tip} **Want to get started fast?** Install the `laravel/ui` Composer package and run `php artisan ui vue --auth` in a fresh Laravel application. After migrating your database, navigate your browser to `http://your-app.test/register` or any other URL that is assigned to your application. This single command will take care of scaffolding your entire authentication system, including resetting passwords!

> {tip} **빠르게 시작해보시겠습니까?** 그렇다면 새로 설치한 라라벨 애플리케이션에서 `laravel/ui` 컴포저 패키지를 설치하고 `php artisan ui vue --auth`를 실행하십시오. 그리고 데이터베이스를 마이그레이션 한 후, 브라우저에서 `http://your-app.test/register` 또는 자신의 어플리케이션 URL로 접속해보세요. 이 한줄의 명령어는 패스워드 재설정을 포함한 전체 인증 시스템을 위한 스캐폴딩을 지원합니다!

Most web applications provide a way for users to reset their forgotten passwords. Rather than forcing you to re-implement this on each application, Laravel provides convenient methods for sending password reminders and performing password resets.

대부분의 웹 애플리케이션은 사용자가 그들이 잊어버린 패스워드를 재설정 할 수 있는 방법을 제공합니다. 각각의 애플리케이션에서 매번 이를 다시 구현하는 것을 대신해서 라라벨은 패스워드 분실 이메일을 보내고 패스워드 재설정을 수행할 수 있는 편리한 방법를 제공합니다.

> {note} Before using the password reset features of Laravel, your user must use the `Illuminate\Notifications\Notifiable` trait.

> {note} 라라벨의 패스워드 재설정 기능을 사용하기 전에, 여러분의 사용자 모델은 `Illuminate\Notifications\Notifiable` 트레이트를 사용하고 있어야 합니다.

<a name="resetting-database"></a>
## Database Considerations
## 데이터베이스 고려사항

To get started, verify that your `App\User` model implements the `Illuminate\Contracts\Auth\CanResetPassword` contract. The `App\User` model included with the framework already implements this interface, and uses the `Illuminate\Auth\Passwords\CanResetPassword` trait to include the methods needed to implement the interface.

시작하기에 앞서, 여러분의 `App\User` 모델이 `Illuminate\Contracts\Auth\CanResetPassword` contract를 구현하고 있는지 확인하십시오. 라라벨에 포함되어 있는 `App\User` 모델은 이미 이 인터페이스를 구현하고 있으며 인터페이스 구현에 필요한 메소드를 포함하고 있는 `Illuminate\Auth\Passwords\CanResetPassword` 트레이트를 사용하고 있습니다.

#### Generating The Reset Token Table Migration
#### 재설정 토큰을 저장하는 테이블 마이그레이션 파일 생성하기

Next, a table must be created to store the password reset tokens. The migration for this table is included with Laravel out of the box, and resides in the `database/migrations` directory. So, all you need to do is run your database migrations:

다음으로, 패스워드 재설정 토큰을 저장하는 테이블을 생성해야 합니다. 라라벨에서 별다른 설정 없이도 이 테이블을 위한 마이그레이션 파일이 `database/migrations` 디렉토리에 포함되어 있습니다. 따라서 여러분은 데이터베이스 마이그레이션을 실행하기만 하면 됩니다.

    php artisan migrate

<a name="resetting-routing"></a>
## Routing
## 라우팅

Laravel includes `Auth\ForgotPasswordController` and `Auth\ResetPasswordController` classes that contains the logic necessary to e-mail password reset links and reset user passwords. All of the routes needed to perform password resets may be generated using the `laravel/ui` Composer package:

라라벨에서는 패스워드 재설정 링크 이메일을 보내고 사용자의 패스워드를 재설정하는데 필요한 로직을 가지고 있는 `Auth\ForgotPasswordController` 그리고 `Auth\ResetPasswordController` 클래스를 포함하고 있습니다. 패스워드 재설정에 필요한 모든 라우트는 `laravel/ui` 컴포저 패키지를 사용하여 생성할 수 있습니다.

    composer require laravel/ui

    php artisan ui vue --auth

<a name="resetting-views"></a>
## Views
## 뷰

To generate all of the necessary view for resetting passwords, you may use the `laravel/ui` Composer package:

패스워드 재설정에 필요한 모든 뷰 파일을 생성하기 위해 `laravel/ui` 컴포저 패키지를 사용할 수 있습니다.

    composer require laravel/ui

    php artisan ui vue --auth

These views are placed in `resources/views/auth/passwords`. You are free to customize them as needed for your application.

이 뷰 파일들은 `resources/views/auth/passwords`에 생성되며 애플리케이션에 필요한대로 자유롭게 커스터마이징 할 수 있습니다.

<a name="after-resetting-passwords"></a>
## After Resetting Passwords
## 패스워드 재설정 이후 처리사항

Once you have defined the routes and views to reset your user's passwords, you may access the route in your browser at `/password/reset`. The `ForgotPasswordController` included with the framework already includes the logic to send the password reset link e-mails, while the `ResetPasswordController` includes the logic to reset user passwords.

사용자의 패스워드를 재설정하기 위한 라우트와 뷰를 정의하고 나면, 여러분은 브라우저에서 `/password/reset` 주소로 엑세스 할 수 있습니다. 프레임워크에서 포함되어 있는 `ForgotPasswordController`는 이미 패스워드를 재설정하는 링크가지고 있는 이메일을 발송하는 로직을 포함하고 있고, `ResetPasswordController` 는 사용자의 패스워드를 재설정하는 로직을 포함하고 있습니다.

After a password is reset, the user will automatically be logged into the application and redirected to `/home`. You can customize the post password reset redirect location by defining a `redirectTo` property on the `ResetPasswordController`:

패스워드가 재설정 되고나면, 사용자는 자동으로 애플리케이션에 로드인되고, `/home` 으로 리다이렉션됩니다. 패스워드가 재설정되고 난 뒤에 이동되는 리다이렉션 경로를 수정하고자 한다면, `ResetPasswordController` 의 `redirectTo` 속성을 정의하면 됩니다.

    protected $redirectTo = '/dashboard';

> {note} By default, password reset tokens expire after one hour. You may change this via the password reset `expire` option in your `config/auth.php` file.

> {note} 기본적으로, 패스워드 재설정 토큰은 한시간 뒤에 만료됩니다. `config/auth.php` 파일의 패스워드 재설정 `expire` 옵션을 통해서 이를 변경할 수 있습니다.

<a name="password-customization"></a>
## Customization
## 커스터마이징

#### Authentication Guard Customization
#### 인증 Guard 커스터마이징

In your `auth.php` configuration file, you may configure multiple "guards", which may be used to define authentication behavior for multiple user tables. You can customize the included `ResetPasswordController` to use the guard of your choice by overriding the `guard` method on the controller. This method should return a guard instance:

`auth.php` 설정 파일에서 여러개의 사용자 테이블을 기반으로 동작하는 여러개의(멀티플) 인증 동작에 사용되는 "guards" 여러개를 정의할 수 있습니다. `ResetPasswordController` 컨트롤러의 `guard` 메소드를 오버라이딩하여 이 중 어떤 guard를 사용할지 결정할 수 있습니다. 이 메소드는 guard 인스턴스를 리턴해야 합니다.

    use Illuminate\Support\Facades\Auth;

    /**
     * Get the guard to be used during password reset.
     *
     * @return \Illuminate\Contracts\Auth\StatefulGuard
     */
    protected function guard()
    {
        return Auth::guard('guard-name');
    }

#### Password Broker Customization
#### 패스워드 브로커 커스터마이징

In your `auth.php` configuration file, you may configure multiple password "brokers", which may be used to reset passwords on multiple user tables. You can customize the included `ForgotPasswordController` and `ResetPasswordController` to use the broker of your choice by overriding the `broker` method:

`auth.php` 설정 파일안에, 여러 사용자 테이블에 대한 암호를 재설정하는데 사용하는 여러개의 "브로커"를 설정할 수 있습니다. 포함되어 있는 `ForgotPasswordController` 와 `ResetPasswordController`의 `broker` 메소드를 오버라이딩 하여 어떤 브로커를 사용할지 결정할 수 있습니다.

    use Illuminate\Support\Facades\Password;

    /**
     * Get the broker to be used during password reset.
     *
     * @return PasswordBroker
     */
    public function broker()
    {
        return Password::broker('name');
    }

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
