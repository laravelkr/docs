# 암호화

- [설정하기](#configuration)
- [기본 사용법](#basic-usage)

<a name="configuration"></a>
## 설정하기

라라벨의 encrypter 를 사용하기 전에, `config/app.php` 설정 파일에 32자리 랜덤한 문자열을 `key` 옵션으로 지정해야 합니다. 이 값을 설정하기 전이라면 라라벨에서 암호화된 값은 안전하지 않을 것입니다. 

<a name="basic-usage"></a>
## 기본적인 사용법

#### 값 암호화하기

특정 값을 암호화하려면 `Crypt` [파사드](/docs/{{version}}/facades)를 사용할 수 있습니다. 모든 암호화에는 OpenSSL이 사용되며 `AES-256-CBC` 알고리즘이 적용된니다. 또한 모든 암호화 값은 변경을 감지하기 위해서 (MAC)을 기본으로한 서명이 적용됩니다.

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

> **주의** 암호화된 값은 암호화가 진행되면서 `직렬화-serialize`되어 있으며, 이를 통해서 객체와 배열들을 암호화 할 수 있습니다. 따라서 PHP 이외의 클라이언트에서 암호화 된 값을 받는 경우, 해당 데이터를 `비 직렬화-unserialize` 할 필요가 있습니다.

#### 값 복호화하기

당연하게도, `Crypt` 파사드의 `decrypt` 메소드를 사용하여 특정 값을 복호화 할 수 있습니다. MAC 이 일치하지 않는 경우에는, 지정한 값이 정상적으로 복호화 되지 않으며 `Illuminate\Contracts\Encryption\DecryptException`이 발생할 것입니다. 

    use Illuminate\Contracts\Encryption\DecryptException;

    try {
        $decrypted = Crypt::decrypt($encryptedValue);
    } catch (DecryptException $e) {
        //
    }
