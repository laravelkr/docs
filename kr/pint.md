# Laravel Pint

- [소개](#introduction)
- [설치](#installation)
- [실행](#running-pint)
- [설정](#configuring-pint)
  - [프리셋](#presets)
  - [규칙](#rules)
  - [파일/폴더 제외](#excluding-files-or-folders)

<a name="introduction"></a>

## 소개

[라라벨 파인트](https://github.com/laravel/pint) 는 미니멀리스트를 위한 PHP만의 고유한 코드 수정 도구입니다. 파인트는 PHP-CS-FIXER 기반으로 구축되었으며 코드 스타일을 간결하고 일관되게 유지하는 것을 더욱 간편하게 해줍니다.

파인트는 새로운 라라벨 애플리케이션을 설치하면 자동으로 설치되며, 곧바로 사용할 수 있습니다. 기본적으로 파인트는 어떤 설정도 요구하지 않으며, 라라벨의 고유한 코드 스타일을 따라 당신의 코드를 수정해줄 것입니다.

<a name="installation"></a>

## 설치

파인트는 최신 라라벨 프레임워크에 포함되어 있으며, 일반적으로 설치는 불필요합니다. 그러나 이전 버전의 애플리케이션이라면 Composer를 이용하여 설치하여야 합니다.

```shell
composer require laravel/pint --dev
```

<a name="running-pint"></a>

## 실행

당신은 당신의 프로젝트 내 `vendor/bin` 디렉토리에 있는 `pint` 바이너리를 실행하여 파인트로 하여금 코드를 수정할 수 있도록 명령할 수 있습니다.

```shell
./vendor/bin/pint
```

또한 당신은 특정 파일이나 디렉토리에 파인트를 실행할 수 있습니다.

```shell
./vendor/bin/pint app/Models

./vendor/bin/pint app/Models/User.php
```

파인트는 업데이트하고자하는 모든 파일들의 리스트를 보여줄 것입니다. 파인트가 수정하고자 하는 더 자세한 내용을 보고자 한다면 `-v` 옵션을 추가하여 실행해주세요.

```shell
./vendor/bin/pint -v
```

파인트가 파일 수정 없이 간단하게 코드 스타일 오류를 검사하기를 원한다면, `--test` 옵션을 사용할 수 있습니다.

```shell
./vendor/bin/pint --test
```

Git 상에서 커밋되지 않은 내용만들 수정하고자 한다면, `--dirty` 옵션을 사용하세요

```shell
./vendor/bin/pint --dirty
```

<a name="configuring-pint"></a>

## 설정

앞서 이야기한 것처럼 파인트는 어떤 설정도 필요로 하지 않습니다. 그러나 프리셋, 규칙, 검사 폴더 등을 수정하고자 한다면, `pint.json` 파일을 루트 디렉토리에 생성하여야 합니다.

```json
{
	"preset": "laravel"
}
```

추가로, `pint.json` 파일을 특정 디렉토리에서 이용하고자 한다면, 파인트를 실행할 때 `--config` 옵션을 제공할 수 있습니다.

```shell
pint --config vendor/my-company/coding-style/pint.json
```

<a name="presets"></a>

### 프리셋

프리셋은 당신 코드에 있는 스타일 이슈를 수정하는 몇 가지 규칙을 정의합니다. 기본적으로 파인트는 `laravel` 프리셋을 사용하며, 이는 라라벨 특유의 코드 스타일을 준수합니다. 그러나, 당신이 다른 프리셋을 사용하고자 한다면 파인트 사용 시 `--preset` 옵션을 추가해야 합니다.

```shell
pint --preset psr12
```

원한다면 `pint.json` 파일에 프리셋을 추가할 수도 있습니다.

```json
{
	"preset": "psr12"
}
```

파인트는 현재 `laravel`, `psr12`, `symfony` 프리셋을 지원합니다.

<a name="rules"></a>

### 규칙

규칙이란 파인트가 코드를 수정할 때 사용할 스타일 가이드라인입니다. 앞에서 이야기한 것처럼, 프리셋은 많은 PHP 프로젝트에서 사용되기에 최적인 몇 가지 사전 정의된 규칙들이며, 당신은 일반적으로 개별적인 규칙에 대해 걱정할 필요가 없습니다.

그러나 원한다면, `pint.json` 파일내에서 특정한 규칙을 적용하거나 적용하지 않도록 조정할 수 있습니다..

```json
{
	"preset": "laravel",
	"rules": {
		"simplified_null_return": true,
		"braces": false,
		"new_with_braces": {
			"anonymous_class": false,
			"named_class": false
		}
	}
}
```

파인트는 [PHP-CS-Fixer](https://github.com/FriendsOfPHP/PHP-CS-Fixer)를 기반으로 합니다. 그러므로 PHP-CS-Fixer의 규칙을 준수해야 합니다[PHP-CS-Fixer Configurator](https://mlocati.github.io/php-cs-fixer-configurator).

<a name="excluding-files-or-folders"></a>

### 파일/폴더 제외

기본적으로 파인트는 프로젝트 내에 `vendor` 디렉토리를 제외한 모든 `.php` 파일을 검사합니다. 만약 폴더를 제외하고 싶다면, `exclude` 옵션을 이용하세요.

```json
{
	"exclude": ["my-specific/folder"]
}
```

특정 네이밍 패턴을 가진 모든 파일을 제외하고자 한다면, `notName` 옵션을 이용하세요.

```json
{
	"notName": ["*-my-file.php"]
}
```

특정 경로의 파일을 제외하고자 한다면, `notPath` 옵션을 이용하세요

```json
{
	"notPath": ["path/to/excluded-file.php"]
}
```
