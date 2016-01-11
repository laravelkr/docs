# Encryption
# 암호화

- [Configuration](#configuration)
- [설정하기](#configuration)
- [Basic Usage](#basic-usage)
- [기본 사용법](#basic-usage)

<a name="configuration"></a>
## Configuration
## 설정하기

Before using Laravel's encrypter, you should set the `key` option of your `config/app.php` configuration file to a 32 character, random string. If this value is not properly set, all values encrypted by Laravel will be insecure.

라라벨의 encrypter 를 사용하기 전에, `config/app.php` 설정 파일에 32자리 랜덤한 문자열을 `key` 옵션으로 지정해야 합니다. 이 값을 설정하기 전이라면 라라벨에서 암호화된 값은 안전하지 않을 것입니다. 

<a name="basic-usage"></a>
## Basic Usage
## 기본적인 사용법

#### Encrypting A Value
#### 값 암호화하기

You may encrypt a value using the `Crypt` [facade](/docs/{{version}}/facades). All encrypted values are encrypted using OpenSSL and the `AES-256-CBC` cipher. Furthermore, all encrypted values are signed with a message authentication code (MAC) to detect any modifications to the encrypted string.

특정 값을 암호화하려면 `Crypt` [파사드](/docs/{{version}}/facades)를 사용할 수 있습니다. 모든 암호화에는 OpenSSL이 사용되며 `AES-256-CBC` 알고리즘이 적용된니다. 또한 모든 암호화 값은 변경을 감지하기 위해서 (MAC)을 기본으로한 서명이 적용됩니다.

For example, we may use the `encrypt` method to encrypt a secret and store it on an [Eloquent model](/docs/{{version}}/eloquent):

다음 예와 같이, `encrypt` 메소드를 사용하여 값을 암호화하고 [Eloquent model](/docs/{{version}}/eloquent)에 저장할 수 있습니다. 

    <?php

    namespace App\Http\Controllers;

    use Crypt;
    use App\User;
    use Illuminate\Http\Request;
    use App\Http\Controllers\Controller;

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
                'secret' => Crypt::encrypt($request->secret)
            ])->save();
        }
    }

#### Decrypting A Value
#### 값 복호화하기

Of course, you may decrypt values using the `decrypt` method on the `Crypt` facade. If the value can not be properly decrypted, such as when the MAC is invalid, an `Illuminate\Contracts\Encryption\DecryptException` will be thrown:

당연하게도, `Crypt` 파사드의 `decrypt` 메소드를 사용하여 특정 값을 복호화 할 수 있습니다. MAC 이 일치하지 않는 경우에는, 지정한 값이 정상적으로 복호화 되지 않으며 `Illuminate\Contracts\Encryption\DecryptException`이 발생할 것입니다. 

    use Illuminate\Contracts\Encryption\DecryptException;

    try {
        $decrypted = Crypt::decrypt($encryptedValue);
    } catch (DecryptException $e) {
        //
    }