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

Laravel's encrypter uses OpenSSL to provide AES-256 and AES-128 encryption. You are strongly encouraged to use Laravel's built-in encryption facilities and not attempt to roll your own "home grown" encryption algorithms. All of Laravel's encrypted values are signed using a message authentication code (MAC) so that their underlying value can not be modified once encrypted.

라라벨의 encrypter AES-256 과 AES-128 암호화를 제공하기 위해서 OpelSSL을 사용합니다. 라라벨에 내장된 암호화 기능은 매우 강력합니다. 따라서 여러분의 "고유한" 암호화 알고리즘을 구성하지 않는 것이 좋습니다. 라라벨의 모든 암호화된 값은 (MAC) 메세지 인증 코드를 사용하여 서명되고, 따라서 한번 암호화 되면 값을 변경할 수 없습니다.

<a name="configuration"></a>
## Configuration
## 설정하기

Before using Laravel's encrypter, you must set a `key` option in your `config/app.php` configuration file. You should use the `php artisan key:generate` command to generate this key since this Artisan command will use PHP's secure random bytes generator to build your key. If this value is not properly set, all values encrypted by Laravel will be insecure.

라라벨의 encrypter 를 사용하기 전에, `config/app.php` 설정 파일의 `key` 옵션을 지정해야만 합니다. 여러분은 `php artisan key:generate` 명령어를 사용하여 이 키를 생성해야 합니다.  이 아티즌 명령어는 키를 생성하는데 PHP의 안전한 랜덤 바이트 제너레이터를 사용합니다. 이 값이 확실하게 설정되지 않으면, 라라벨에 의해서 암호화된 값은 안전하지 않습니다.

<a name="using-the-encrypter"></a>
## Using The Encrypter
## Encrypter 사용하기

#### Encrypting A Value
#### 하나의 값 암호화하기

You may encrypt a value using the `encryptString` method of the `Crypt` facade. All encrypted values are encrypted using OpenSSL and the `AES-256-CBC` cipher. Furthermore, all encrypted values are signed with a message authentication code (MAC) to detect any modifications to the encrypted string:

`Crypt`파사드의 `encryptString` 메서드를 사용하여 하나의 값을 암호화 할 수 있습니다. 모든 암호화된 값들은 OpenSSL 과 `AES-256-CBC` 알고리즘이 사용됩니다. 또한 모든 암호화된 값은 변경을 감지하기 위해서 (MAC) 을 기본으로한 서명이 적용됩니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\User;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Crypt;

    class UserController extends Controller
    {
        /**
         * Store a secret message for the user.
         *
         * @param  Request  $request
         * @param  int  $id
         * @return Response
         */
        public function storeSecret(Request $request, $id)
        {
            $user = User::findOrFail($id);

            $user->fill([
                'secret' => Crypt::encryptString($request->secret),
            ])->save();
        }
    }

#### Decrypting A Value
#### 값 복호화 하기

You may decrypt values using the `decryptString` method of the `Crypt` facade. If the value can not be properly decrypted, such as when the MAC is invalid, an `Illuminate\Contracts\Encryption\DecryptException` will be thrown:

`Crypt`파사드의 `encryptString` 메서드를 사용하여 특정 값을 복호화 할 수 있습니다. MAC 이 일치하지 않는 경우에는, 지정한 값이 정상적으로 복호화 되지 않으며 `Illuminate\Contracts\Encryption\DecryptException`이 발생할 것입니다.

    use Illuminate\Contracts\Encryption\DecryptException;
    use Illuminate\Support\Facades\Crypt;

    try {
        $decrypted = Crypt::decryptString($encryptedValue);
    } catch (DecryptException $e) {
        //
    }
