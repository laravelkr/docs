# Contribution Guide
# 기여 가이드

- [Bug Reports](#bug-reports)
- [버그 리포트](#bug-reports)
- [Core Development Discussion](#core-development-discussion)
- [코어 개발에 대한 논의](#core-development-discussion)
- [Which Branch?](#which-branch)
- [브랜치 선택?](#which-branch)
- [Compiled Assets](#compiled-assets)
- [컴파일된 Assets](#compiled-assets)
- [Security Vulnerabilities](#security-vulnerabilities)
- [보안 취약점](#security-vulnerabilities)
- [Coding Style](#coding-style)
- [코딩 스타일](#coding-style)
    - [PHPDoc](#phpdoc)
    - [StyleCI](#styleci)

<a name="bug-reports"></a>
## Bug Reports
## 버그 리포트

To encourage active collaboration, Laravel strongly encourages pull requests, not just bug reports. "Bug reports" may also be sent in the form of a pull request containing a failing test.

활발한 협력을 장려하기 위해, 라라벨은 버그 리포트가 아니라 PR을 권장하고 있습니다. "버그 리포트"는 문제가 있는 테스트를 포함한 PR로 보내져야 합니다.

However, if you file a bug report, your issue should contain a title and a clear description of the issue. You should also include as much relevant information as possible and a code sample that demonstrates the issue. The goal of a bug report is to make it easy for yourself - and others - to replicate the bug and develop a fix.

버그 리포트를 작성하는 경우, 이슈는 제목과 이슈에 대한 명확한 설명을 포함해야 합니다. 또한 해당 이슈에 대한 가능한 많은 관련 정보와 데모 코드 샘플을 포함해야 합니다. 버그 리포트의 목적은 여러분 자신과 다른 사람들이 쉽게 버그를 재현하고, 수정할 수 있게 하는 것입니다.

Remember, bug reports are created in the hope that others with the same problem will be able to collaborate with you on solving it. Do not expect that the bug report will automatically see any activity or that others will jump to fix it. Creating a bug report serves to help yourself and others start on the path of fixing the problem.

버그 리포트를 통해 같은 문제를 겪고 있는 다른 사람들과 협력해서 그 문제를 해결할 수 있다는 것을 명심하세요. 버그 리포트가 자동으로 어떤 활동을 만들어 내거나, 다른 이들이 버그을 고칠 것이라는 기대를 하지 마십시오. 버그 리포트 제출은 여러분 자신과 다른 사람이 문제를 해결할 수 있는 길에 들어설 수 있도록 하는 것입니다.

The Laravel source code is managed on GitHub, and there are repositories for each of the Laravel projects:

라라벨의 소스 코드는 GitHub에서 관리되며 각각의 라라벨 프로젝트의 저장소가 있습니다.


- [Laravel Application](https://github.com/laravel/laravel)
- [라라벨 애플리케이션](https://github.com/laravel/laravel)
- [Laravel Art](https://github.com/laravel/art)
- [라라벨 아트웤](https://github.com/laravel/art)
- [Laravel Documentation](https://github.com/laravel/docs)
- [라라벨 매뉴얼](https://github.com/laravel/docs)
- [Laravel Cashier](https://github.com/laravel/cashier)
- [라라벨 캐셔](https://github.com/laravel/cashier)
- [Laravel Cashier for Braintree](https://github.com/laravel/cashier-braintree)
- [Braintree를 위한 라라벨 캐셔](https://github.com/laravel/cashier-braintree)
- [Laravel Envoy](https://github.com/laravel/envoy)
- [라라벨 Envoy](https://github.com/laravel/envoy)
- [Laravel Framework](https://github.com/laravel/framework)
- [라라벨 프레임워크](https://github.com/laravel/framework)
- [Laravel Homestead](https://github.com/laravel/homestead)
- [라라벨 홈스테드](https://github.com/laravel/homestead)
- [Laravel Homestead Build Scripts](https://github.com/laravel/settler)
- [라라벨 홈스테드 빌드스크립트](https://github.com/laravel/settler)
- [Laravel Horizon](https://github.com/laravel/horizon)
- [라라벨 Horizon](https://github.com/laravel/horizon)
- [Laravel Passport](https://github.com/laravel/passport)
- [라라벨 패스포트(Passport)](https://github.com/laravel/passport)
- [Laravel Scout](https://github.com/laravel/scout)
- [라라벨 스카웃(Scout)](https://github.com/laravel/scout)
- [Laravel Socialite](https://github.com/laravel/socialite)
- [라라벨 Socialite](https://github.com/laravel/socialite)
- [Laravel Telescope](https://github.com/laravel/telescope)
- [라라벨 Telescope](https://github.com/laravel/telescope)
- [Laravel Website](https://github.com/laravel/laravel.com)
- [라라벨 웹사이트](https://github.com/laravel/laravel.com)


<a name="core-development-discussion"></a>
## Core Development Discussion
## 코어 개발에 대한 논의

You may propose new features or improvements of existing Laravel behavior in the Laravel Ideas [issue board](https://github.com/laravel/ideas/issues). If you propose a new feature, please be willing to implement at least some of the code that would be needed to complete the feature.

새로운 기능이나 라라벨의 동작에 대한 개선을 제안하고자 하는 경우는 라라벨 아이디어 [이슈 보드](https://github.com/ideas/internals/issues)에 제안하십시오. 새로운 기능을 제안하는 경우 자발적으로 기능을 구동시키는 데 필요한 코드를 최소한으로 구현하시기 바랍니다.

Informal discussion regarding bugs, new features, and implementation of existing features takes place in the `#internals` channel of the [Laravel Discord server](https://discordapp.com/invite/mPZNm7A). Taylor Otwell, the maintainer of Laravel, is typically present in the channel on weekdays from 8am-5pm (UTC-06:00 or America/Chicago), and sporadically present in the channel at other times.

버그와 관련 및 새로운 기능과 기존 기능의 구현에 대한 격식에 얽매이지 않은 일상적인 논의는 [Laravel Discord server](https://discordapp.com/invite/mPZNm7A)의 `#internals` 채널에서 진행됩니다. 라라벨의 메인 관리자인 Taylor Otwell은 일반적으로 평일 오전 8시부터 5시까지 (America/Chicago 표준시 UTC-06:00 기준) 접속해 있고, 그 외에 다른 시간대에는 가끔 접속합니다.

<a name="which-branch"></a>
## Which Branch?
## 브랜치 선택

**All** bug fixes should be sent to the latest stable branch or to the [current LTS branch](/docs/{{version}}/releases#support-policy). Bug fixes should **never** be sent to the `master` branch unless they fix features that exist only in the upcoming release.

**모든** 버그 수정은 최신 안정 브랜치 또는  [현재의 LTS 브랜치](/docs/{{version}}/releases#support-policy)에 보내져야 합니다. 다음 릴리스에만 존재하는 기능에 대한 수정사항이 아니라면 버그 수정사항을 `master` 브랜치에 **절대** 보내지 마십시오.

**Minor** features that are **fully backwards compatible** with the current Laravel release may be sent to the latest stable branch.

현재 라라벨 릴리스와 **완전한 호환성** 을 가진 **마이너** 기능은 최신 안정 브랜치로 보내 주시기 바랍니다.

**Major** new features should always be sent to the `master` branch, which contains the upcoming Laravel release.

다음 라라벨 릴리즈에 포함될, **주요한** 새로운 기능들은 항상 `master` 브랜치로 보내 주시기 바랍니다.

If you are unsure if your feature qualifies as a major or minor, please ask Taylor Otwell in the `#internals` channel of the [Laravel Discord server](https://discordapp.com/invite/mPZNm7A).

만약 당신의 새로운 기능이 메이저인지 마이너인지 명확하지 않으면 [Laravel Discord server](https://discordapp.com/invite/mPZNm7A) 의 `#internals` 채널에서 Taylor Otwell에 문의하십시오.

<a name="compiled-assets"></a>
## Compiled Assets
## 컴파일된 Assets

If you are submitting a change that will affect a compiled file, such as most of the files in `resources/sass` or `resources/js` of the `laravel/laravel` repository, do not commit the compiled files. Due to their large size, they cannot realistically be reviewed by a maintainer. This could be exploited as a way to inject malicious code into Laravel. In order to defensively prevent this, all compiled files will be generated and committed by Laravel maintainers.

`laravel/laravel` 저장소의 `resources/sass` 또는 `resources/js` 에있는 대부분의 파일과 같이 컴파일 된 Asset 파일에 영향을 주는 변경 사항을 제출하는 경우 컴파일 된 파일을 커밋하지 마십시오. 이 파일들은 크기가 크기 때문에 메인테이너가 실제로 검토 할 수 없습니다. 이러한 방법은 악성 코드를 라라벨에 삽입하는 방법으로 악용 될 소지가 있습니다. 이를 방지하기 위해 컴파일 된 모든 파일은 라라벨 관리자에 의해 생성되고 커밋됩니다.

<a name="security-vulnerabilities"></a>
## Security Vulnerabilities
## 보안 취약점

If you discover a security vulnerability within Laravel, please send an email to Taylor Otwell at <a href="mailto:taylor@laravel.com">taylor@laravel.com</a>. All security vulnerabilities will be promptly addressed.

만약 라라벨에서 보안 취약점을 발견했다면 테일러 오트웰 <a href="mailto:taylor@laravel.com">taylor@laravel.com</a>에게 이메일을 보내주시길 바랍니다. 모든 보안 취약점은 신속하게 해결될 것입니다.

<a name="coding-style"></a>
## Coding Style
## 코딩 스타일

Laravel follows the [PSR-2](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md) coding standard and the [PSR-4](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader.md) autoloading standard.

라라벨은 [PSR-2](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md)와 [PSR-4](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader.md) 코딩 표준을 따르고 있습니다.

<a name="phpdoc"></a>
### PHPDoc
### PHPDoc

Below is an example of a valid Laravel documentation block. Note that the `@param` attribute is followed by two spaces, the argument type, two more spaces, and finally the variable name:

다음은 유효한 라라벨 문서 블록의 예시입니다. `@param` 속성 뒤에는 2개의 스페이스, 인수의 타입, 다시 2개의 스페이스, 마지막으로 변수의 이름이 위치합니다:

    /**
     * Register a binding with the container.
     *
     * @param  string|array  $abstract
     * @param  \Closure|string|null  $concrete
     * @param  bool  $shared
     * @return void
     * @throws \Exception
     */
    public function bind($abstract, $concrete = null, $shared = false)
    {
        //
    }

<a name="styleci"></a>
### StyleCI
### StyleCI

Don't worry if your code styling isn't perfect! [StyleCI](https://styleci.io/) will automatically merge any style fixes into the Laravel repository after pull requests are merged. This allows us to focus on the content of the contribution and not the code style.

코드의 스타일이 완벽하지 않더라도 걱정하지 마십시오. PR이 Merge 될 때 [StyleCI](https://styleci.io/)가 자동으로 스타일을 수정하고 라라벨 저장소에 머지할 것입니다. 이렇게 하면 코드 스타일이 아니라 기여의 내용에 집중할 수 있습니다.
