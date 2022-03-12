# 해시

- [시작하기](#introduction)
- [설정](#configuration)
- [기본적인 사용법](#basic-usage)
    - [비밀번호 해싱하기](#hashing-passwords)
    - [A 비밀번호가 A 해시인지 확인하는 방법](#verifying-that-a-password-matches-a-hash)
        - [A 비밀번호의 해시화를 다시 해야 할 필요가 있는지 결정하기](#determining-if-a-password-needs-to-be-rehashed)

<a name="introduction"></a>
## 시작하기

라라벨의 `Hash` [파사드](/docs/{{version}}/facades)는 사용자의 암호를 저장하는데 필요한 보안성 높은 Bcrypt와 Argon2 해싱을 제공합니다. 만약 라라벨 애플리케이션에 포함되어 있는 [Laravel application starter kits](/docs/{{version}}/starter-kits)을 사용하고 있다면, 회원가입(registration)과 인증(authentication)과정에서 Bcrypt 방식을 사용하게 됩니다.

비밀번호를 해싱(Hashing)하는데 Bcrypt 방식의 해시 알고리즘은 "work factor"를 조절할 수 있기 때문에 아주 좋은 선택입니다. "work factor"를 조절할 수 있다는 말은 해시를 만드는 데 드는 시간이 증가함에 따라서 하드웨어의 파워(power)도 증가한다는 의미입니다. 비밀번호를 해시화 할 때는 느릴 수록 좋습니다. 해시 알고리즘이 사용하는 시간이 오래 걸리면 오래 걸릴수록 악의적인 사용자가 무차별 대입 공격(brute force attacks)에 필요한 모든 가능한 문자열 해시 값을 가진 레인보우 테이블(rainbow tables)을 생성하는데 더 오랜 시간이 걸립니다.

(역자주 : 'work factor'는 해싱 알고리즘을 선택하는데 고려되는 요소를 의미합니다. 대표적으로 무차별 대입 공격이 성공하지 못하도록 막으려는 요소와 로그인에 필요한 해싱 속도를 향상 시키는 요소가 있습니다. 일반적으로 해싱 알고리즘이 복잡할수록 무차별 대입 공격에 소모되는 비용이 커지는 반면, 로그인 시도의 속도 또한 느려집니다. 해싱을 할 때는 어느 정도 빠른 인증 속도를 보장하면서도 해킹이 어렵도록 풀기 어려운 암호를 만드는 해싱 알고리즘의 선택과 'work factor'를 조정하는 것이 중요합니다.)


<a name="configuration"></a>
## 설정

라라벨 애플리케이션에 기본(default)으로 설정되어 있는 해시 드라이버를 바꾸려면 `config/hashing.php` 설정 파일을 확인하십시오. 라라벨에서는 현재 세 개의 해싱 드라이버를 지원하고 있습니다. [Bcrypt](https://en.wikipedia.org/wiki/Bcrypt)와 [Argon2](https://en.wikipedia.org/wiki/Argon2) (Argon2i 와 Argon2id 변경버전).

> {note} 아르곤2i 드라이버는 PHP 7.2.0 이상의 버전을 요구하고 아르곤2id 드라이버는 PHP 7.3.0 이상을 요구합니다.

<a name="basic-usage"></a>
## 기본 사용법

<a name="hashing-passwords"></a>
### 비밀번호 해싱

`Hash` 파사드에서 `make` 메소드를 호출하여 패스워드를 해싱 할 수 있습니다. 

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Hash;

    class PasswordController extends Controller
    {
        /**
         * Update the password for the user.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return \Illuminate\Http\Response
         */
        public function update(Request $request)
        {
            // Validate the new password length...

            $request->user()->fill([
                'password' => Hash::make($request->newPassword)
            ])->save();
        }
    }

<a name="adjusting-the-bcrypt-work-factor"></a>
#### Bcrypt Work Factor 조정하기

여러분이 Bcrypt 알고리즘을 사용하고 있다면, `make` 메소드는 해싱 알고리즘의 `rounds` 옵션을 사용하여 work factor를 조절할 수 있습니다. 라라벨에서는 대부분의 애플리케이션에서 기본적으로 받아 들여지고 있는 기본 값을 사용하며, work factor의 값을 조정할 수 있도록 옵션을 제공하고 있습니다.

    $hashed = Hash::make('password', [
        'rounds' => 12,
    ]);


#### Argon2의 Work Factor 조정하기

만약 여러분이 Argon2 알고리즘을 사용하고 있다면, `make` 메소드는 `memory`,`time` 그리고 `threads` 옵션을 사용하는 Argon2 알고리즘의 work factor를 관리할 수 있게 해줍니다; 하지만, 대부분의 애플리케이션에서는 기본값을 사용할 수 있습니다.

    $hashed = Hash::make('password', [
        'memory' => 1024,
        'time' => 2,
        'threads' => 2,
    ]);

> {tip} work factor을 조정하기 위한 옵션에 대한 더 많은 정보를 보시려면 다음을 참고하세요.  [official PHP documentation Argon 해싱에 관하여](https://secure.php.net/manual/en/function.password-hash.php).

<a name="verifying-that-a-password-matches-a-hash"></a>
### A 비밀번호가 A 해시인지 확인하는 방법

`Hash` 파사드가 제공하는 `check` 메소드는 평문(암호화 하지 않은 데이터)이 주어진 해시와 일치하는지 확인합니다.

    if (Hash::check('plain-text', $hashedPassword)) {
        // The passwords match...
    }

<a name="determining-if-a-password-needs-to-be-rehashed"></a>
### A 비밀번호의 해시화를 다시 해야 할 필요가 있는지 결정하기

work factor가 바뀌기 전의 해시된 값과 work factor가 바뀐 이후 해시된 값이 달라지면 인증이 되지 않는 문제가 발생할 수 있습니다. `Hash` 파사드가 제공하는 `needsRehash` 메소드를 사용하면 비밀번호가 해시된 이후에, 사용된 해시가 사용한 work factor가 바뀌었는지 확인할 수 있습니다. 일부 애플리케이션에서는 인증 프로세스 내부에 work factor의 변경 여부를 검사하는 방식을 채택하고 있습니다.

    if (Hash::needsRehash($hashed)) {
        $hashed = Hash::make('plain-text');
    }