# Hashing
# 해시

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Configuration](#configuration)
- [설정](#configuration)
- [Basic Usage](#basic-usage)
- [기본적인 사용법](#basic-usage)
    - [Hashing Passwords](#hashing-passwords)
    - [비밀번호 해싱하기](#hashing-passwords)
    - [Verifying That A Password Matches A Hash](#verifying-that-a-password-matches-a-hash)
    - [A 비밀번호가 A 해시인지 확인하는 방법](#verifying-that-a-password-matches-a-hash)
      - [Determining If A Password Needs To Be Rehashed](#determining-if-a-password-needs-to-be-rehashed)
      - [A 비밀번호의 해시화를 다시 해야 할 필요가 있는지 결정하기](#determining-if-a-password-needs-to-be-rehashed)

<a name="introduction"></a>
## Introduction
## 시작하기

The Laravel `Hash` [facade](/docs/{{version}}/facades) provides secure Bcrypt and Argon2 hashing for storing user passwords. If you are using one of the [Laravel application starter kits](/docs/{{version}}/starter-kits), Bcrypt will be used for registration and authentication by default.

라라벨의 `Hash` [파사드](/docs/{{version}}/facades)는 사용자의 암호를 저장하는데 필요한 안전한 Bcrypt와 Argon2 해싱을 제공합니다. 만약 라라벨 애플리케이션에 포함되어 있는 [Laravel application starter kits](/docs/{{version}}/starter-kits)을 사용하고 있다면, 회원가입(registration)과 인증(authentication)과정에서 Bcrypt 방식을 사용하게 됩니다.

Bcrypt is a great choice for hashing passwords because its "work factor" is adjustable, which means that the time it takes to generate a hash can be increased as hardware power increases. When hashing passwords, slow is good. The longer an algorithm takes to hash a password, the longer it takes malicious users to generate "rainbow tables" of all possible string hash values that may be used in brute force attacks against applications.

비밀번호를 해싱(Hashing)하는데 Bcrypt 방식의 해시 알고리즘은 "work factor"를 조절할 수 있기 때문에 아주 좋은 선택입니다. "work factor"를 조절할 수 있다는 말은 하드웨어의 사양이 좋아짐에 따라 해시를 만들어내는 시간도 증가 시킬 수 있다는 의미입니다. 비밀번호를 해시화 할 때는 느릴 수록 좋습니다. 해시 알고리즘이 사용하는 시간이 오래 걸리면 오래 걸릴수록 악의 적인 사용자가 있어 무차별 대입 공격(brute force attacks)에 필요한 모든 가능한 문자열 해시 값을 가진 레인보우 테이블(rainbow tables)을 생성하는데 더 오랜 시간이 걸립니다.

(역자주 : 'work factor'는 해싱 알고리즘을 선택하는데 고려되는 요소를 의미합니다. 대표적으로 무차별 대입 공격이 성공하지 못하도록 막으려는 요소와 로그인에 필요한 해싱 속도를 향상 시키는 요소가 있습니다. 일반적으로 해싱 알고리즘이 복잡할수록 무차별 대입 공격에 소모되는 비용이 커지는 반면, 로그인 시도의 속도 또한 느려집니다. 해싱을 할 때는 어느 정도 빠른 인증 속도를 보장하면서도 해킹이 어렵도록 풀기 어려운 암호를 만드는 해싱 알고리즘의 선택과 'work factor'를 조정하는 것이 중요합니다.)


<a name="configuration"></a>
## Configuration

The default hashing driver for your application is configured in your application's `config/hashing.php` configuration file. There are currently three supported drivers: [Bcrypt](https://en.wikipedia.org/wiki/Bcrypt) and [Argon2](https://en.wikipedia.org/wiki/Argon2) (Argon2i and Argon2id variants).

라라벨 애플리케이션에 기본(default)으로 설정되어 있는 해시 드라이버를 바꾸려면 `config/hashing.php` 구성 파일을 확인하십시오. 라라벨에서는 현재 세 개의 해싱 드라이버를 지원하고 있습니다. [Bcrypt](https://en.wikipedia.org/wiki/Bcrypt)와 [Argon2](https://en.wikipedia.org/wiki/Argon2) (Argon2i 와 Argon2id 변경버전).

> {note} The Argon2i driver requires PHP 7.2.0 or greater and the Argon2id driver requires PHP 7.3.0 or greater.

> {note} 아르곤2i 드라이버는 PHP 7.2.0 이상의 버전을 요구하고 아르곤2id 드라이버는 PHP 7.3.0 이상을 요구합니다.

<a name="basic-usage"></a>
## Basic Usage
## 기본 사용법

<a name="hashing-passwords"></a>
### Hashing Passwords
### 비밀번호 해싱

You may hash a password by calling the `make` method on the `Hash` facade:

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
#### Adjusting The Bcrypt Work Factor
#### Bcrypt Work Factor 조정하기

If you are using the Bcrypt algorithm, the `make` method allows you to manage the work factor of the algorithm using the `rounds` option; however, the default work factor managed by Laravel is acceptable for most applications:

여러분이 Bcrypt 알고리즘을 사용하고 있다면, `make` 메소드는 해싱 알고리즘의 `rounds` 옵션을 사용하여 work factor를 조절할 수 있습니다. 라라벨에서는 대부분의 애플리케이션에서 기본적으로 받아 들여지고 있는 기본 값을 사용하며 work factor의 값을 조정할 수 있도록 옵션을 제공하고 있습니다.

    $hashed = Hash::make('password', [
        'rounds' => 12,
    ]);


#### Adjusting The Argon2 Work Factor
#### Argon2의 Work Factor 조정하기

If you are using the Argon2 algorithm, the `make` method allows you to manage the work factor of the algorithm using the `memory`, `time`, and `threads` options; however, the defaults are acceptable for most applications:

만약 여러분이 Argon2 알고리즘을 사용하고 있다면, `make` 메소드는 `memory`,`time` 그리고 `threads` 옵션을 사용하는 Argon2 알고리즘의 work factor를 관리할 수 있게 해줍니다; 하지만, 대부분의 애플리케이션에서는 기본값을 사용할 수 있습니다.

    $hashed = Hash::make('password', [
        'memory' => 1024,
        'time' => 2,
        'threads' => 2,
    ]);

> {tip} For more information on these options, please refer to the [official PHP documentation regarding Argon hashing](https://secure.php.net/manual/en/function.password-hash.php).

> {팁} work factor을 조정하기 위한 옵션에 대한 더 많은 정보를 보시려면 다음을 참고하세요.  [official PHP documentation Argon 해싱에 관하여](https://secure.php.net/manual/en/function.password-hash.php).

<a name="verifying-that-a-password-matches-a-hash"></a>
### Verifying That A Password Matches A Hash
### A 비밀번호가 A 해시인지 확인하는 방법

The `check` method provided by the `Hash` facade allows you to verify that a given plain-text string corresponds to a given hash:

`Hash` 파사드가 제공하는 `check` 메소드는 평문(암호화 하지 않은 데이터)가 주어진 해시와 일치하는지 확인합니다.

    if (Hash::check('plain-text', $hashedPassword)) {
        // The passwords match...
    }

<a name="determining-if-a-password-needs-to-be-rehashed"></a>
### Determining If A Password Needs To Be Rehashed
### A 비밀번호의 해시화를 다시 해야 할 필요가 있는지 결정하기

The `needsRehash` method provided by the `Hash` facade allows you to determine if the work factor used by the hasher has changed since the password was hashed. Some applications choose to perform this check during the application's authentication process:

work factor가 바뀌기 전의 해시된 값과 work factor가 바뀐 이후 해시된 값이 달라지면 인증이 되지 않는 문제가 발생할 수 있습니다. `Hash` 파사드가 제공하는 `needsRehash` 메소드를 사용하면 비밀번호가 해시된 이후에, 사용된 해시가 사용한 work factor가 바뀌었는지 확인할 수 있습니다. 일부 애플리케이션에서는 인증 프로세스 내부에 work factor의 변경 여부를 검사하는 방식을 채택하고 있습니다.

    if (Hash::needsRehash($hashed)) {
        $hashed = Hash::make('plain-text');
    }