# Encryption 암호화

- [Introduction 소개](#introduction)
- [Basic Usage 기본 사용법](#basic-usage)

<a name="introduction"></a>
## Introduction 소개

Laravel provides facilities for strong AES encryption via the Mcrypt PHP extension.
라라벨은 Mcrypt PHP 확장을 이용한 강력한 AES 암호화 기능을 제공합니다.

<a name="basic-usage"></a>
## Basic Usage 기본 사용법

#### Encrypting A Value 값 암호화하기

	$encrypted = Crypt::encrypt('secret');

> **Note참고:** Be sure to set a 16, 24, or 32 character random string in the `key` option of the `config/app.php` file. Otherwise, encrypted values will not be secure. `config/app.php` 파일의 `key` 옵션에 16,24 또는 32 자리의 임의의 문자열을 정확히 지정하십시오. 이를 설정하지 않으면 암호화된 결과값은 안전하지 않습니다.

#### Decrypting A Value 값 복호화하기

	$decrypted = Crypt::decrypt($encryptedValue);

#### Setting The Cipher & Mode 암호 & 모드 설정하기

You may also set the cipher and mode used by the encrypter:
암호화에 사용하는 방법과 모드를 지정할 수 있습니다.

	Crypt::setMode('ctr');

	Crypt::setCipher($cipher);
