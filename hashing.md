# 해시

- [시작하기](#introduction)
- [기본적인 사용법](#basic-usage)

<a name="introduction"></a>
## 시작하기

라라벨의 `Hash` [파사드](/docs/{{version}}/facades)는 사용자의 암호를 저장하는데 필요한 안전한 Bcrypt 해싱을 제공합니다. 만약 라라벨 애플리케이션에 포함되어 있는 `AuthController`를 사용하고 있다면, 컨트롤러에서 자동으로 회원 가입과 인증에 자동으로 Bcrypt 방식을 사용하게 됩니다.

Bcrypt 는 "work factor"를 조정할 수 있기 때문에 암호 해시로는 좋은 대안이 됩니다. 이 것이 의미하는 것은 즉 하드웨어의 성능이 증가하면 해시의 생성 시간을 빨리 할 수 있다는 것을 의미합니다.

<a name="basic-usage"></a>
## 기본적인 사용법

`Hash` 파사드에서 `make` 메소드를 호출하여 패스워드를 해싱 할 수 있습니다. 

    <?php

    namespace App\Http\Controllers;

    use Hash;
    use App\User;
    use Illuminate\Http\Request;
    use App\Http\Controllers\Controller;

    class UserController extends Controller
    {
        /**
         * Update the password for the user.
         *
         * @param  Request  $request
         * @param  int  $id
         * @return Response
         */
        public function updatePassword(Request $request, $id)
        {
            $user = User::findOrFail($id);

            // Validate the new password length...

            $user->fill([
                'password' => Hash::make($request->newPassword)
            ])->save();
        }
    }

다른 방법으로는 `bcrypt` 헬퍼 함수를 사용할 수도 있습니다. 

    bcrypt('plain-text');

#### 패스워드에 대한 해시 확인하기

`check` 메소드를 사용하면 주어진 문자열이 주어진 해시값과 일치하는지 확인할 수 있습니다. 하지만, 여러분이 [라라벨에 포함된](/docs/{{version}}/authentication) `AuthController` 를 사용중이라면 컨트롤러가 자동으로 처리하기 때문에 해시 확인을 직접할 필요가 없습니다. 

    if (Hash::check('plain-text', $hashedPassword)) {
        // The passwords match...
    }

#### 패스워드의 리해싱이 필요한지 확인하기

`needsRehash` 메소드를 사용하면 패스워드가 해싱된 후 해시의 work factor 가 사용되었는지 확인할 수 있습니다.

    if (Hash::needsRehash($hashed)) {
        $hashed = Hash::make('plain-text');
    }
