# Hashing 해시

- [Introduction 소개](#introduction)
- [Basic Usage 기본 사용법](#basic-usage)

<a name="introduction"></a>
## Introduction 소개

The Laravel `Hash` facade provides secure Bcrypt hashing for storing user passwords. 라라벨의 `Hash` 파사드는 사용자의 암호를 저장하는데 필요한 안전한 Bcrypt 해싱을 제공합니다. If you are using the `AuthController` controller that is included with your Laravel application, it will be take care of verifying the Bcrypt password against the un-hashed version provided by the user. 만약 라라벨 애플리케이션에 포함되어 있는 `AuthController` 컨트롤러를 사용하고 있다면 사용자로부터 입력받은 해시되지 않은 암호를 Bcrypt 된 암호와 비교하여 확인할 수 있습니다. 

Likewise, the user `Registrar` service that ships with Laravel makes the proper `bcrypt` function call to hash stored passwords. 마찬가지로, 라라벨과 함께 제공되는 사용자 `Registrar` 서비스는 저장된 패스워드를 해시하기 위한 `bcrypt` 함수를 제공합니다. 

<a name="basic-usage"></a>
## Basic Usage 기본 사용법

#### Hashing A Password Using Bcrypt Bcrypt 를 사용해서 패스워드를 해시하기

	$password = Hash::make('secret');

You may also use the `bcrypt` helper function:
`bcrypt` 헬퍼 함수를 사용할 수도 있습니다. 

	$password = bcrypt('secret');

#### Verifying A Password Against A Hash 패스워드에 대한 해시 확인하기

	if (Hash::check('secret', $hashedPassword))
	{
		// The passwords match...
	}

#### Checking If A Password Needs To Be Rehashed 패스워드가 리해시가 필요한지 확인하기

	if (Hash::needsRehash($hashed))
	{
		$hashed = Hash::make('secret');
	}
