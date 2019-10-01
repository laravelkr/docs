# 해시

- [시작하기](#introduction)
- [설정](#configuration)
- [기본적인 사용법](#basic-usage)

<a name="introduction"></a>
## 시작하기

라라벨의 `Hash` [파사드](/docs/{{version}}/facades)는 사용자의 암호를 저장하는데 필요한 안전한 Bcrypt와 Argon2 해싱을 제공합니다. 만약 라라벨 애플리케이션에 포함되어 있는 `LoginController` 와 `RegisterController`를 사용하고 있다면, 컨트롤러에서 자동으로 회원 가입과 인증에 자동으로 Bcrypt 방식을 사용하게 됩니다.

> {tip} Bcrypt 는 "work factor"를 조정할 수 있기 때문에 암호 해시로는 좋은 선택이 됩니다. 이것이 의미하는 것은 즉 하드웨어의 성능이 증가하면 해시의 생성 시간을 빨리 할 수 있다는 것을 의미합니다.

<a name="configuration"></a>
## Configuration

여러분의 애플리케이션을 위한 기본 해싱 드라이버는 `config/hashing.php` 구성 파일 안에 설정되어 있습니다. 현재 지원되는 세개의 드라이버: [Bcrypt](https://en.wikipedia.org/wiki/Bcrypt)와 [Argon2](https://en.wikipedia.org/wiki/Argon2) (Argon2i 와 Argon2id 변경버전).

> {note} 아르곤2i 드라이버는 PHP 7.2.0 이상의 버전을 요구하고 아르곤2id 드라이버는 PHP 7.3.0 이상을 요구합니다.

<a name="basic-usage"></a>
## Basic Usage

`Hash` 파사드에서 `make` 메소드를 호출하여 패스워드를 해싱 할 수 있습니다. 

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Hash;
    use App\Http\Controllers\Controller;

    class UpdatePasswordController extends Controller
    {
        /**
         * Update the password for the user.
         *
         * @param  Request  $request
         * @return Response
         */
        public function update(Request $request)
        {
            // Validate the new password length...

            $request->user()->fill([
                'password' => Hash::make($request->newPassword)
            ])->save();
        }
    }

#### Bcrypt Work Factor 조정하기

만약 여러분이 Bcrypt 알고리즘을 사용하고 있다면, `make` 메소드는 `rounds` 옵션을 사용하여 Bcrypt 알고리즘에서 사용하는 work factor를 관리할 수 있게 해줍니다; 하지만, 대부분의 애플리케이션에서는 기본값을 사용할 수 있습니다.

    $hashed = Hash::make('password', [
        'rounds' => 12
    ]);

#### Argon2 Work Factor 조정하기

만약 여러분이 Argon2 알고리즘을 사용하고 있다면, `make` 메소드는 `memory`,`time` 그리고 `threads` 옵션을 사용하는 Argon2 알고리즘의 work factor를 관리할 수 있게 해줍니다; 하지만, 대부분의 애플리케이션에서는 기본값을 사용할 수 있습니다.

    $hashed = Hash::make('password', [
        'memory' => 1024,
        'time' => 2,
        'threads' => 2,
    ]);

> {tip} 이 옵션들에 대한 더 많은 정보를 얻으려면, [공식 PHP 문서](https://secure.php.net/manual/en/function.password-hash.php)를 확인하세요.

#### 패스워드에 대한 해시 확인하기

`check` 메소드를 사용하면 주어진 문자열이 주어진 해시값과 일치하는지 확인할 수 있습니다. 하지만, 여러분이 [라라벨에 포함된](/docs/{{version}}/authentication) `LoginController` 를 사용중이라면 컨트롤러가 자동으로 이 메소드를 호출하기 때문에 해시 확인을 직접할 필요가 없습니다. 

    if (Hash::check('plain-text', $hashedPassword)) {
        // The passwords match...
    }

#### 패스워드의 리해싱이 필요한지 확인하기

`needsRehash` 메소드를 사용하면 패스워드가 해싱된 후 해시의 work factor 가 사용되었는지 확인할 수 있습니다.

    if (Hash::needsRehash($hashed)) {
        $hashed = Hash::make('plain-text');
    }
