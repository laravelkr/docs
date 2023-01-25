# Laravel Pint
# 라라벨 파인트

- [Introduction](#introduction)
- [소개](#introduction)
- [Installation](#installation)
- [설치](#installation)
- [Running Pint](#running-pint)
- [파인트 실행하기](#running-pint)
- [Configuring Pint](#configuring-pint)
- [파인트 설정하기](#configuring-pint)
    - [Presets](#presets)
    - [사전 설정](#presets)
    - [Rules](#rules)
    - [규칙](#rules)
    - [Excluding Files / Folders](#excluding-files-or-folders)
    - [파일 / 폴더 제외하기](#excluding-files-or-folders)

<a name="introduction"></a>
## Introduction
## 소개

[Laravel Pint](https://github.com/laravel/pint) is an opinionated PHP code style fixer for minimalists. Pint is built on top of PHP-CS-Fixer and makes it simple to ensure that your code style stays clean and consistent.

[라라벨 파인트](https://github.com/laravel/pint)는 미니멀리스트를 위한 독창적인 PHP 코드 스타일 해결사입니다. 파인트는 PHP-CS-Fixer를 기반으로 구축되었으며 코드 스타일이 깨끗하고 일관성을 유지하도록 간단하게 해줍니다.

Pint is automatically installed with all new Laravel applications so you may start using it immediately. By default, Pint does not require any configuration and will fix code style issues in your code by following the opinionated coding style of Laravel.

파인트는 모든 새로운 라라벨 애플리케이션과 함께 자동으로 설치되므로 즉시 사용할 수 있습니다. 기본적으로 파인트는 구성이 필요하지 않으며 라라벨의 독창적인 코딩 스타일을 따라 코드의 코드 스타일 문제를 수정합니다.

<a name="installation"></a>
## Installation
## 설치

Pint is included in recent releases of the Laravel framework, so installation is typically unnecessary. However, for older applications, you may install Laravel Pint via Composer:

파인트는 라라벨 프레임워크의 최신 릴리스에 포함되어 있으므로 일반적으로 설치가 필요하지 않습니다. 그러나 이전 애플리케이션의 경우 컴포저를 통해 라라벨 파인트를 설치할 수 있습니다.

```shell
composer require laravel/pint --dev
```

<a name="running-pint"></a>
## Running Pint
## 파인트 설치하기

You can instruct Pint to fix code style issues by invoking the `pint` binary that is available in your project's `vendor/bin` directory:

`.vendor/bin` 프로젝트 디렉토리에서 사용할 수 있는 `pint` 바이너리를 호출하여 파인트가 코드 스타일 문제를 수정하도록 지시할 수 있습니다 

```shell
./vendor/bin/pint
```

You may also run Pint on specific files or directories:

특정 파일이나 디렉토리에서도 파인트를 실행할 수 있습니다.

```shell
./vendor/bin/pint app/Models

./vendor/bin/pint app/Models/User.php
```

Pint will display a thorough list of all of the files that it updates. You can view even more detail about Pint's changes by providing the `-V` option when invoking Pint:

파인트는 업데이트하는 모든 파일의 전체 목록을 표시합니다. 파인트를 호출할 때 `-v` 옵션을 제공하여 파인트의 변경 사항에 대한 자세한 내용을 볼 수 있습니다 .

```shell
./vendor/bin/pint -v
```

If you would like Pint to simply inspect your code for style errors without actually changing the files, you may use the `--test` option:

파인트가 실제로 파일을 변경하지 않고 코드에서 스타일 오류를 검사하도록 하려면 `--test` 옵션을 사용하면 됩니다.

```shell
./vendor/bin/pint --test
```

<a name="configuring-pint"></a>
## Configuring Pint
## 파인트 설정하기

As previously mentioned, Pint does not require any configuration. However, if you wish to customize the presets, rules, or inspected folders, you may do so by creating a `pint.json` file in your project's root directory:

앞서 언급했듯이 파인트는 설정이 필요하지 않습니다. 그러나 사전 설정, 규칙 또는 검사된 폴더를 사용자 지정하려면 프로젝트의 루트 디렉터리에 `pint.json` 파일을 생성하면 됩니다.

```json
{
    "preset": "laravel"
}
```

In addition, if you wish to use a `pint.json` from a specific directory, you may provide the `--config` option when invoking Pint:

또한 `pint.json`를 특정 디렉토리에서 사용하려는 경우 파인트를 호출할 때 `--config` 옵션을 제공할 수 있습니다.

```shell
pint --config vendor/my-company/coding-style/pint.json
```

<a name="presets"></a>
### Presets
### 사전 설정

Presets defines a set of rules that can be used to fix code style issues in your code. By default, Pint uses the `laravel` preset, which fixes issues by following the opinionated coding style of Laravel. However, you may specify a different preset by providing the `--preset` option to Pint:

사전 설정은 코드 스타일 문제를 수정하는 데 사용할 수 있는 일련의 규칙을 정의합니다. 기본적으로 파인트는 라라벨의 독창적인 코딩 스타일을 따라 문제를 수정하는 `laravel` 사전 설정을 사용합니다. 그러나 파인트에 `--preset` 옵션을 제공하여 다른 사전 설정을 지정할 수 있습니다 .

```shell
pint --preset psr12
```

If you wish, you may also set the preset in your project's `pint.json` file:

원하는 경우 프로젝트 `pint.json` 파일에서 사전 설정을 설정할 수도 있습니다.

```json
{
    "preset": "psr12"
}
```

Pint's currently supported presets are: `laravel`, `psr12`, and `symfony`.

파인트의 현재 지원되는 사전 설정은 `laravel`, `psr12`, `symfony` 입니다.

<a name="rules"></a>
### Rules
### 규칙

Rules are style guidelines that Pint will use to fix code style issues in your code. As mentioned above, presets are predefined groups of rules that should be perfect for most PHP projects, so you typically will not need to worry about the individual rules they contain.

규칙은 파인트가 코드 스타일 문제를 수정하는 데 사용할 스타일 지침입니다. 위에서 언급했듯이 사전 설정은 대부분의 PHP 프로젝트에 완벽해야 하는 사전 정의된 규칙 그룹이므로 일반적으로 사전 설정에 포함된 개별 규칙에 대해 걱정할 필요가 없습니다.

However, if you wish, you may enable or disable specific rules in your `pint.json` file:

그러나 원하는 경우 `pint.json` 파일에서 특정 규칙을 활성화하거나 비활성화할 수 있습니다.

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

Pint is built on top of [PHP-CS-Fixer](https://github.com/FriendsOfPHP/PHP-CS-Fixer). Therefore, you may use any of its rules to fix code style issues in your project: [PHP-CS-Fixer Configurator](https://mlocati.github.io/php-cs-fixer-configurator).

파인트는 [PHP-CS-Fixer](https://github.com/FriendsOfPHP/PHP-CS-Fixer) 위에 구축되었습니다 . 따라서 모든 PHP-CS-Fixer의 규칙을 사용하여 프로젝트의 코드 스타일 문제를 수정할 수 있습니다. [PHP-CS-Fixer 설정기](https://mlocati.github.io/php-cs-fixer-configurator)를 참고하세요.

<a name="excluding-files-or-folders"></a>
### Excluding Files / Folders
### 파일 / 폴더 제외

By default, Pint will inspect all `.php` files in your project except those in the `vendor` directory. If you wish to exclude more folders, you may do so using the `exclude` configuration option:

기본적으로 파인트는 `vendor` 디렉토리에 있는 파일을 제외한 프로젝트의 모든 `.php` 파일을 검사합니다. 더 많은 폴더를 제외하려면 `exclude` 구성 옵션을 사용하여 제외할 수 있습니다.

```json
{
    "exclude": [
        "my-specific/folder"
    ]
}
```

If you wish to exclude all files that contain a given name pattern, you may do so using the `notName` configuration option:

지정된 이름 패턴을 포함하는 모든 파일을 제외하려면 `notName` 구성 옵션을 사용하여 제외할 수 있습니다.

```json
{
    "notName": [
        "*-my-file.php"
    ]
}
```

If you would like to exclude a file by providing an exact path to the file, you may do so using the `notPath` configuration option:

파일에 대한 정확한 경로를 제공하여 파일을 제외하려면 `notPath` 구성 옵션을 사용하면 됩니다.

```json
{
    "notPath": [
        "path/to/excluded-file.php"
    ]
}
```
