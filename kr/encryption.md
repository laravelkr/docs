# Encryption
# 암호화

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Configuration](#configuration)
- [설정하기](#configuration)
- [Using The Encrypter](#using-the-encrypter)
- [Encrypter 사용하기](#using-the-encrypter)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel's encryption services provide a simple, convenient interface for encrypting and decrypting text via OpenSSL using AES-256 and AES-128 encryption. All of Laravel's encrypted values are signed using a message authentication code (MAC) so that their underlying value can not be modified or tampered with once encrypted.

라라벨의 암호화 서비스는 AES-256 및 AES-128 암호화를 사용하여 OpenSSL을 통해 텍스트를 암호화하고 해독하기 위한 간단하고 편리한 인터페이스를 제공합니다. 라라벨의 모든 암호화된 값은 메시지 인증 코드(MAC)를 사용하여 서명되므로 일단 암호화되면 기본 값을 수정하거나 변조할 수 없습니다.

<a name="configuration"></a>
## Configuration
## 설정하기

Before using Laravel's encrypter, you must set the `key` configuration option in your `config/app.php` configuration file. This configuration value is driven by the `APP_KEY` environment variable. You should use the `php artisan key:generate` command to generate this variable's value since the `key:generate` command will use PHP's secure random bytes generator to build a cryptographically secure key for your application. Typically, the value of the `APP_KEY` environment variable will be generated for you during [Laravel's installation](/docs/{{version}}/installation).

라라벨의 암호화를 사용하기 전에 `config/app.php` 설정 파일에서 `key` 설정 옵션을 설정해야 합니다. 이 설정 값은 `APP_KEY` 환경 변수에 의해 동작합니다. `key:generate` 명령은 PHP의 보안 랜덤 바이트 생성기를 사용하여 애플리케이션에 대한 암호화 보안 키를 구축하므로, `php artisan key:generate` 명령을 사용하여 이 변수의 값을 생성해야 합니다. 일반적으로 `APP_KEY` 환경 변수의 값은 [Laravel 설치](/docs/{{version}}/installation) 중에 생성됩니다.

<a name="using-the-encrypter"></a>
## Using The Encrypter
## Encrypter 사용하기

<a name="encrypting-a-value"></a>
#### Encrypting A Value
#### 하나의 값 암호화하기

You may encrypt a value using the `encryptString` method provided by the `Crypt` facade. All encrypted values are encrypted using OpenSSL and the AES-256-CBC cipher. Furthermore, all encrypted values are signed with a message authentication code (MAC). The integrated message authentication code will prevent the decryption of any values that have been tampered with by malicious users:

`Crypt` 파사드에서 제공하는 `encryptString` 메소드를 사용하여 값을 암호화할 수 있습니다. 모든 암호화된 값은 OpenSSL 및 AES-256-CBC 암호를 사용하여 암호화됩니다. 또한 모든 암호화된 값은 메시지 인증 코드(MAC)로 서명됩니다. 통합 메시지 인증 코드는 악의적인 사용자에 의해 변조된 값의 암호 해독을 방지합니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Models\User;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Crypt;

    class DigitalOceanTokenController extends Controller
    {
        /**
         * Store a DigitalOcean API token for the user.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return \Illuminate\Http\Response
         */
        public function storeSecret(Request $request)
        {
            $request->user()->fill([
                'token' => Crypt::encryptString($request->token),
            ])->save();
        }
    }

<a name="decrypting-a-value"></a>
#### Decrypting A Value
#### 값 복호화 하기

You may decrypt values using the `decryptString` method provided by the `Crypt` facade. If the value can not be properly decrypted, such as when the message authentication code is invalid, an `Illuminate\Contracts\Encryption\DecryptException` will be thrown:

`Crypt` 파사드에서 제공하는 `decryptString` 메소드를 사용하여 값을 해독할 수 있습니다. 메시지 인증 코드가 유효하지 않은 경우와 같이 값을 제대로 해독할 수 없으면 `Illuminate\Contracts\Encryption\DecryptException`이 발생합니다.

    use Illuminate\Contracts\Encryption\DecryptException;
    use Illuminate\Support\Facades\Crypt;

    try {
        $decrypted = Crypt::decryptString($encryptedValue);
    } catch (DecryptException $e) {
        //
    }
