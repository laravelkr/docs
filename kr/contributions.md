# Contribution Guide 기여 가이드

- [Bug Reports 버그 리포트](#bug-reports)
- [Core Development Discussion 핵심 개발 논의](#core-development-discussion)
- [Which Branch? 브랜치 선택](#which-branch)
- [Security Vulnerabilities 보안 취약점](#security-vulnerabilities)
- [Coding Style 코딩 스타일](#coding-style)

<a name="bug-reports"></a>
## Bug Reports

To encourage active collaboration, Laravel strongly encourages pull requests, not just bug reports. 활발한 협력을 장려하기 위해, 라라벨은 버그 리포트가 아니라 PR을 권장하고 있습니다. "Bug reports" may also be sent in the form of a pull request containing a failing unit test. "버그 리포트"는 실패한 단위 테스트를 포함한 PR로 보내져야 합니다. 

However, if you file a bug report, your issue should contain a title and a clear description of the issue. 버그 리포트를 작성하는 경우, 이슈는 제목과 이슈에 대한 명확한 설명을 포함해야합니다. You should also include as much relevant information as possible and a code sample that demonstrates the issue. 또한 해당 이슈에 대한 가능한 많은 관련 정보와 데모 코드 샘플을 포함해야 합니다. The goal of a bug report is to make it easy for yourself - and others - to replicate the bug and develop a fix. 버그 리포트의 목적은 여러분 자신과 그리고 다른 사람들도 쉽게 버그를 재현 할 수 있도록 하고 버그가 수정되도록 하는 것 입니다.

Remember, bug reports are created in the hope that others with the same problem will be able to collaborate with you on solving it. 명심할 것은 버그 리포트가 같은 문제에 있던 다른 사람들과 문제를 해결하기 위해 협력 할 수 있는 기회을 만든다는 것입니다. Do not expect that the bug report will automatically see any activity or that others will jump to fix it. 버그 리포트를 통해 자동으로 뭔가가 일어나고, 다른 누군가가 문제를 해결하기를 기대하지 마십시오. Creating a bug report serves to help yourself and others start on the path of fixing the problem. 버그 리포트 제출은 여러분 자신과 다른 사람이 문제를 해결하는 길을 시작하는 계기가 되는 것입니다. 

The Laravel source code is managed on Github, and there are repositories for each of the Laravel projects: 라라벨의 소스 코드는 GitHub에서 관리되며 각각의 라라벨 프로젝트의 저장소가 있습니다.

- [Laravel Framework 라라벨 프레임워크](https://github.com/laravel/framework) 
- [Laravel Application 라라벨 애플리케이션](https://github.com/laravel/laravel)
- [Laravel Documentation 라라벨 메뉴얼](https://github.com/laravel/docs)
- [Laravel Cashier 라라벨 캐셔](https://github.com/laravel/cashier)
- [Laravel Envoy 라라벨 Envoy](https://github.com/laravel/envoy)
- [Laravel Homestead 라라벨 Homestead](https://github.com/laravel/homestead)
- [Laravel Homestead Build Scripts 라라벨 Homestead 빌드 스크립트](https://github.com/laravel/settler)
- [Laravel Website 라라벨 웹사이트](https://github.com/laravel/website)
- [Laravel Art 라라벨 아트웤](https://github.com/laravel/art)

<a name="core-development-discussion"></a>
## Core Development Discussion
## 핵심 개발 논의

Discussion regarding bugs, new features, and implementation of existing features takes place in the `#laravel-dev` IRC channel (Freenode). Taylor Otwell, the maintainer of Laravel, is typically present in the channel on weekdays from 8am-5pm (UTC-06:00 or America/Chicago), and sporadically present in the channel at other times. 버그에 대한, 새로운 기능 및 기존 기능의 구현에 대한 논의는 Freenode의 `#laravel-dev` IRC 채널에서 진행하고 있습니다. 라라벨의 메인 관리자 인 Taylor Otwell는 일반적으로 평일 오전 8시부 터5 시까 지 (America / Chicago 표준시 UTC-06 : 00 기준) 접속해 있고, 그외에 다른 시간대에는 가끔 접속합니다. 

The `#laravel-dev` IRC channel is open to all. All are welcome to join the channel either to participate or simply observe the discussions! `#laravel-dev` IRC 채널은 모든 사람에게 개방되어 있습니다. 토론에 참여하거나, 혹은 그냥보고 있는 것만으로도 괜찮습니다, 채널에 많이 참가하세요!

<a name="which-branch"></a>
## Which Branch?
## 브랜치 선택

**All** bug fixes should be sent to the latest stable branch. **모든** 버그 수정은 최신 안정 브랜치에 보내져야 합니다. Bug fixes should **never** be sent to the `master` branch unless they fix features that exist only in the upcoming release. 다음 릴리스에만 존재하는 기능에 대한 수정사항이 아니라면 버그 수정사항을 `master` 브랜치에 **절대** 보내지 마십시오.

**Minor** features that are **fully backwards compatible** with the current Laravel release may be sent to the latest stable branch. 현재 라라벨 릴리스와 **완전히 거꾸로 겸용성** 을 가진 **작은** 기능은 최신 안정 브랜치로 보내 주시기 바랍니다.

**Major** new features should always be sent to the `master` branch, which contains the upcoming Laravel release. 다음 라라벨 릴리즈에 포함될, 새로운 기능들은 항상 `master` 브랜치로 보내 주시기 바랍니다.

If you are unsure if your feature qualifies as a major or minor, please ask Taylor Otwell in the `#laravel-dev` IRC channel (Freenode). 만약 여러분의 새로운 기능이 메이저인지 마이너인지 명확하지 않다면 Freenode의 `#laravel-dev` IRC 채널에서 Taylor Otwell에 문의 하길바랍니다.

<a name="security-vulnerabilities"></a>
## Security Vulnerabilities
## 보안 취약점

If you discover a security vulnerability within Laravel, please send an e-mail to Taylor Otwell at <a href="mailto:taylor@laravel.com">taylor@laravel.com</a>. 만약 라라벨에서 보안 취약점을 발견했다면 테일러 오트웰 <a href="mailto:taylor@laravel.com">taylor@laravel.com</a>에게 이메일을 보내주시길 바랍니다. All security vulnerabilities will be promptly addressed. 모든 보안 취약점은 신속하게 해결될 것입니다. 

<a name="coding-style"></a>
## Coding Style
## 코딩 스타일

Laravel follows the [PSR-4](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader.md) and [PSR-1](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-1-basic-coding-standard.md) coding standards. 라라벨은 [PSR-4](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader.md) 와 [PSR-1](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-1-basic-coding-standard.md) 코딩 표준을 따르고 있습니다. In addition to these standards, the following coding standards should be followed: 이러한 기준에 더하여, 다음과 같은 코딩 표준을 따라야 합니다. 

- The class namespace declaration must be on the same line as `<?php`.
- 클래스 네임스페이스는 반드시 `<?php`와 같은 라인에 있어야 합니다. 
- A class' opening `{` must be on the same line as the class name.
- 클래스를 여는 `{`는 반드시 클래스 이름과 같은 라인에 있어야 합니다. 
- Functions and control structures must use Allman style braces.
- 함수와 컨트롤 구조에서는 반드시 Allman 방식의 괄호를 구성합니다. 
- Indent with tabs, align with spaces.
- 인덴트는 탭으로, 정렬은 스페이스로 구성합니다. 
