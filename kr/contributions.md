# Contribution Guide
# 기여 가이드

- [Bug Reports](#bug-reports)
- [버그 리포트](#bug-reports)
- [Core Development Discussion](#core-development-discussion)
- [코어 개발에 대한 논의](#core-development-discussion)
- [Which Branch?](#which-branch)
- [브랜치 선택?](#which-branch)
- [Security Vulnerabilities](#security-vulnerabilities)
- [보안 취약점](#security-vulnerabilities)
- [Coding Style](#coding-style)
- [코딩 스타일](#coding-style)
    - [Code Style Fixer](#code-style-fixer)
    - [코드 스타일 Fixer](#code-style-fixer)

<a name="bug-reports"></a>
## Bug Reports
## 버그 리포트

To encourage active collaboration, Laravel strongly encourages pull requests, not just bug reports. "Bug reports" may also be sent in the form of a pull request containing a failing test.

활발한 협력을 장려하기 위해, 라라벨은 버그 리포트가 아니라 PR을 권장하고 있습니다. "버그 리포트"는 문제가 있는 테스트를 포함한 PR로 보내져야 합니다. 

However, if you file a bug report, your issue should contain a title and a clear description of the issue. You should also include as much relevant information as possible and a code sample that demonstrates the issue. The goal of a bug report is to make it easy for yourself - and others - to replicate the bug and develop a fix. 

버그 리포트를 작성하는 경우, 이슈는 제목과 이슈에 대한 명확한 설명을 포함해야합니다. 또한 해당 이슈에 대한 가능한 많은 관련 정보와 데모 코드 샘플을 포함해야 합니다. 버그 리포트의 목적은 여러분 자신과 그리고 다른 사람들도 쉽게 버그를 재현 할 수 있도록 하고 버그가 수정되도록 하는 것 입니다.

Remember, bug reports are created in the hope that others with the same problem will be able to collaborate with you on solving it. Do not expect that the bug report will automatically see any activity or that others will jump to fix it. Creating a bug report serves to help yourself and others start on the path of fixing the problem. 

명심할 것은 버그 리포트가 같은 문제에 있던 다른 사람들과 문제를 해결하기 위해 협력 할 수 있는 기회을 만든다는 것입니다. 버그 리포트를 통해 자동으로 뭔가가 일어나고, 다른 누군가가 문제를 해결하기를 기대하지 마십시오. 버그 리포트 제출은 여러분 자신과 다른 사람이 문제를 해결하는 길을 시작하는 계기가 되는 것입니다. 

The Laravel source code is managed on Github, and there are repositories for each of the Laravel projects: 

라라벨의 소스 코드는 GitHub에서 관리되며 각각의 라라벨 프로젝트의 저장소가 있습니다.

- [Laravel Framework](https://github.com/laravel/framework)
- [라라벨 프레임워크](https://github.com/laravel/framework)
- [Laravel Application](https://github.com/laravel/laravel)
- [라라벨 어플리케이션](https://github.com/laravel/laravel)
- [Laravel Documentation](https://github.com/laravel/docs)
- [라라벨 매뉴얼](https://github.com/laravel/docs)
- [Laravel Cashier](https://github.com/laravel/cashier)
- [라라벨 캐셔](https://github.com/laravel/cashier)
- [Laravel Envoy](https://github.com/laravel/envoy)
- [라라벨 Envoy](https://github.com/laravel/envoy)
- [Laravel Homestead](https://github.com/laravel/homestead)
- [라라벨 홈스테드](https://github.com/laravel/homestead)
- [Laravel Homestead Build Scripts](https://github.com/laravel/settler)
- [라라벨 홈스테드 구성 스크립트](https://github.com/laravel/settler)
- [Laravel Website](https://github.com/laravel/laravel.com)
- [라라벨 웹사이트](https://github.com/laravel/laravel.com)
- [Laravel Art](https://github.com/laravel/art)
- [라라벨 아트웤](https://github.com/laravel/art)

<a name="core-development-discussion"></a>
## Core Development Discussion
## 코어 개발에 대한 논의

Discussion regarding bugs, new features, and implementation of existing features takes place in the `#internals` channel of the [LaraChat](http://larachat.co) Slack team. Taylor Otwell, the maintainer of Laravel, is typically present in the channel on weekdays from 8am-5pm (UTC-06:00 or America/Chicago), and sporadically present in the channel at other times.

버그와 관련되거나, 새로운 기능 및 기존 기능의 구현에 대한 논의는 [LaraChat](http://larachat.com)의 `#internals` 채널에서 진행됩니다. 라라벨의 메인 관리자인 Taylor Otwell은 일반적으로 평일 오전 8시부터 5시까지 (America/Chicago 표준시 UTC-06:00 기준) 접속해 있고, 그 외에 다른 시간대에는 가끔 접속합니다.

<a name="which-branch"></a>
## Which Branch?
## 브랜치 선택

**All** bug fixes should be sent to the latest stable branch. Bug fixes should **never** be sent to the `master` branch unless they fix features that exist only in the upcoming release. 

**모든** 버그 수정은 최신 안정 브랜치에 보내져야 합니다. 다음 릴리스에만 존재하는 기능에 대한 수정사항이 아니라면 버그 수정사항을 `master` 브랜치에 **절대** 보내지 마십시오.

**Minor** features that are **fully backwards compatible** with the current Laravel release may be sent to the latest stable branch. 

현재 라라벨 릴리스와 **완전한 호환성** 을 가진 **마이너** 기능은 최신 안정 브랜치로 보내 주시기 바랍니다.

**Major** new features should always be sent to the `master` branch, which contains the upcoming Laravel release.

다음 라라벨 릴리즈에 포함될, **주요한** 새로운 기능들은 항상 `master` 브랜치로 보내 주시기 바랍니다.

If you are unsure if your feature qualifies as a major or minor, please ask Taylor Otwell in the `#internals` channel of the [LaraChat](http://larachat.co) Slack team.

만약 당신의 새로운 기능이 메이저인지 마이너인지 명확하지 않으면 [LaraChat](http://larachat.co) 의 `#internals` 채널에서 Taylor Otwell에 문의하십시오.

<a name="security-vulnerabilities"></a>
## Security Vulnerabilities
## 보안 취약점

If you discover a security vulnerability within Laravel, please send an e-mail to Taylor Otwell at <a href="mailto:taylor@laravel.com">taylor@laravel.com</a>. All security vulnerabilities will be promptly addressed. 

만약 라라벨에서 보안 취약점을 발견했다면 테일러 오트웰 <a href="mailto:taylor@laravel.com">taylor@laravel.com</a>에게 이메일을 보내주시길 바랍니다. 모든 보안 취약점은 신속하게 해결될 것입니다. 

<a name="coding-style"></a>
## Coding Style
## 코딩 스타일

Laravel follows the [PSR-2](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md) coding standard and the [PSR-4](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader.md) autoloading standard.

라라벨은 [PSR-2](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md)와 [PSR-4](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader.md) 코딩 표준을 따르고 있습니다.

### DocBlocks
### 문서 블럭 주석

`@param` tags should **not be aligned** and arguments should be separated by **2 spaces**.
`@param` 태그는 **정렬되어서는 안되고**, 인자는 **2개의 스페이스**로 구분되어 져야 합니다.

Here's an example block:
다음은 문서 블럭의 예제입니다.

    /**
     * Register a binding with the container.
     *
     * @param  string|array  $abstract
     * @param  \Closure|string|null  $concrete
     * @param  bool  $shared
     * @return void
     */
    public function bind($abstract, $concrete = null, $shared = false)
    {
        //
    }

<a name="code-style-fixer"></a>
### Code Style Fixer
### 코드 스타일 Fixer

You may use the [PHP-CS-Fixer](https://github.com/FriendsOfPHP/PHP-CS-Fixer) to fix your code style before committing.

커밋을 하기 전에 코드 스타일을 [PHP-CS-Fixer](https://github.com/FriendsOfPHP/PHP-CS-Fixer)를 사용하여 수정하십시오.

To get started, [install the tool globally](https://github.com/FriendsOfPHP/PHP-CS-Fixer#globally-manual) and check the code style by issuing the following terminal command from your project's root directory:

이 툴을 사용하기 위해서 [툴 글로벌 설치하기](https://github.com/FriendsOfPHP/PHP-CS-Fixer#globally-manual)를 실행하고 프로젝트 루트 디렉토리에서 터미너를 통해서 코드 스타일을 체크하시기 바랍니다.

```sh
php-cs-fixer fix
```