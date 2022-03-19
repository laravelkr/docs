# Contribution Guide
# 기여 가이드

- [Bug Reports](#bug-reports)
- [버그 리포트](#bug-reports)
- [Support Questions](#support-questions)
- [지원 질문](#support-questions)
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
- [Code of Conduct](#code-of-conduct)
- [행동 지침](#code-of-conduct)

<a name="bug-reports"></a>
## Bug Reports
## 버그 리포트

To encourage active collaboration, Laravel strongly encourages pull requests, not just bug reports. "Bug reports" may also be sent in the form of a pull request containing a failing test.

활발한 협력을 장려하기 위해, 라라벨은 버그 리포트가 아니라 PR을 권장하고 있습니다. "버그 리포트"는 문제가 있는 테스트를 포함한 PR로 보내져야 합니다.

However, if you file a bug report, your issue should contain a title and a clear description of the issue. You should also include as much relevant information as possible and a code sample that demonstrates the issue. The goal of a bug report is to make it easy for yourself - and others - to replicate the bug and develop a fix.

버그 리포트를 작성하는 경우, 이슈는 제목과 이슈에 대한 명확한 설명을 포함해야 합니다. 또한 해당 이슈에 대한 가능한 많은 관련 정보와 데모 코드 샘플을 포함해야 합니다. 버그 리포트의 목적은 여러분 자신과 다른 사람들이 쉽게 버그를 재현하고, 수정할 수 있게 하는 것입니다.

Remember, bug reports are created in the hope that others with the same problem will be able to collaborate with you on solving it. Do not expect that the bug report will automatically see any activity or that others will jump to fix it. Creating a bug report serves to help yourself and others start on the path of fixing the problem. If you want to chip in, you can help out by fixing [any bugs listed in our issue trackers](https://github.com/issues?q=is%3Aopen+is%3Aissue+label%3Abug+user%3Alaravel+-repo%3Alaravel%2Fnova-issues).

버그 리포트를 통해 같은 문제를 겪고 있는 다른 사람들과 협력해서 그 문제를 해결할 수 있다는 것을 명심하세요. 버그 리포트가 자동으로 어떤 활동을 만들어 내거나, 다른 이들이 버그을 고칠 것이라는 기대를 하지 마십시오. 버그 리포트 제출은 여러분 자신과 다른 사람이 문제를 해결할 수 있는 길에 들어설 수 있도록 하는 것입니다. 문제를 해결하려면 [이슈 트래커에 나열된 모든 버그](https://github.com/issues?q=is%3Aopen+is%3Aissue+label%3Abug+user%3Alaravel+-repo%3Alaravel%2Fnova-issues) 를 수정하여 도움을 줄 수 있습니다.

The Laravel source code is managed on GitHub, and there are repositories for each of the Laravel projects:

라라벨의 소스 코드는 GitHub에서 관리되며 각각의 라라벨 프로젝트의 저장소가 있습니다.

- [Laravel Application](https://github.com/laravel/laravel)
- [라라벨 애플리케이션](https://github.com/laravel/laravel)
- [Laravel Art](https://github.com/laravel/art)
- [라라벨 아트웤](https://github.com/laravel/art)
- [Laravel Documentation](https://github.com/laravel/docs)
- [라라벨 매뉴얼](https://github.com/laravel/docs)
- [Laravel Dusk](https://github.com/laravel/dusk)
- [라라벨 더스크(Dusk)](https://github.com/laravel/dusk)
- [Laravel Cashier Stripe](https://github.com/laravel/cashier)
- [라라벨 캐셔 Stripe](https://github.com/laravel/cashier)
- [Laravel Cashier Paddle](https://github.com/laravel/cashier-paddle)
- [라라벨 캐셔 Paddle](https://github.com/laravel/cashier-paddle)
- [Laravel Echo](https://github.com/laravel/echo)
- [라라벨 에코(Echo)](https://github.com/laravel/echo)
- [Laravel Envoy](https://github.com/laravel/envoy)
- [라라벨 Envoy](https://github.com/laravel/envoy)
- [Laravel Framework](https://github.com/laravel/framework)
- [라라벨 프레임워크](https://github.com/laravel/framework)
- [Laravel Homestead](https://github.com/laravel/homestead)
- [라라벨 홈스테드](https://github.com/laravel/homestead)
- [Laravel Homestead Build Scripts](https://github.com/laravel/settler)
- [라라벨 홈스테드 빌드스크립트](https://github.com/laravel/settler)
- [Laravel Horizon](https://github.com/laravel/horizon)
- [Laravel Jetstream](https://github.com/laravel/jetstream)
- [Laravel 제트스트림(Jetstream)](https://github.com/laravel/jetstream)
- [Laravel Passport](https://github.com/laravel/passport)
- [Laravel Sanctum](https://github.com/laravel/sanctum)
- [라라벨 생텀(Sanctum)](https://github.com/laravel/sanctum)
- [Laravel Scout](https://github.com/laravel/scout)
- [라라벨 스카웃(Scout)](https://github.com/laravel/scout)
- [Laravel Socialite](https://github.com/laravel/socialite)
- [라라벨 Socialite](https://github.com/laravel/socialite)
- [Laravel Telescope](https://github.com/laravel/telescope)
- [라라벨 Telescope](https://github.com/laravel/telescope)
- [Laravel Website](https://github.com/laravel/laravel.com-next)
- [라라벨 웹사이트](https://github.com/laravel/laravel.com-next)

<a name="support-questions"></a>
## Support Questions
## 지원 질문

Laravel's GitHub issue trackers are not intended to provide Laravel help or support. Instead, use one of the following channels:

Laravel의 GitHub 이슈 트래커는 Laravel 도움말이나 지원을 제공하기위한 것이 아닙니다. 대신 다음 채널 중 하나를 사용하십시오.

- [GitHub Discussions](https://github.com/laravel/framework/discussions)
- [GitHub Discussions](https://github.com/laravel/framework/discussions)
- [Laracasts Forums](https://laracasts.com/discuss)
- [Laracasts Forums](https://laracasts.com/discuss)
- [Laravel.io Forums](https://laravel.io/forum)
- [Laravel.io Forums](https://laravel.io/forum)
- [StackOverflow](https://stackoverflow.com/questions/tagged/laravel)
- [StackOverflow](https://stackoverflow.com/questions/tagged/laravel)
- [Discord](https://discord.gg/laravel)
- [Discord](https://discord.gg/laravel)
- [Larachat](https://larachat.co)
- [Larachat](https://larachat.co)
- [IRC](https://webchat.freenode.net/?nick=artisan&channels=%23laravel&prompt=1)
- [IRC](https://webchat.freenode.net/?nick=artisan&channels=%23laravel&prompt=1)

<a name="core-development-discussion"></a>
## Core Development Discussion
## 코어 개발에 대한 논의

You may propose new features or improvements of existing Laravel behavior in the Laravel Ideas [issue board](https://github.com/laravel/ideas/issues). If you propose a new feature, please be willing to implement at least some of the code that would be needed to complete the feature.

새로운 기능이나 라라벨의 동작에 대한 개선을 제안하고자 하는 경우는 라라벨 아이디어 [이슈 보드](https://github.com/ideas/internals/issues)에 제안하십시오. 새로운 기능을 제안하는 경우 자발적으로 기능을 구동시키는 데 필요한 코드를 최소한으로 구현하시기 바랍니다.

Informal discussion regarding bugs, new features, and implementation of existing features takes place in the `#internals` channel of the [Laravel Discord server](https://discord.gg/laravel). Taylor Otwell, the maintainer of Laravel, is typically present in the channel on weekdays from 8am-5pm (UTC-06:00 or America/Chicago), and sporadically present in the channel at other times.

버그와 관련 및 새로운 기능과 기존 기능의 구현에 대한 격식에 얽매이지 않은 일상적인 논의는 [Laravel Discord server](https://discord.gg/laravel)의 `#internals` 채널에서 진행됩니다. 라라벨의 메인 관리자인 Taylor Otwell은 일반적으로 평일 오전 8시부터 5시까지 (America/Chicago 표준시 UTC-06:00 기준) 접속해 있고, 그 외에 다른 시간대에는 가끔 접속합니다.

<a name="which-branch"></a>
## Which Branch?
## 브랜치 선택

**All** bug fixes should be sent to the latest version that supports bug fixes (currently `8.x`). Bug fixes should **never** be sent to the `master` branch unless they fix features that exist only in the upcoming release.

**모든** 버그 수정은 버그 수정을 지원하는 최신버전(현재는 8.x)에 보내져야 합니다. 다음 릴리스에만 존재하는 기능에 대한 수정사항이 아니라면 버그 수정사항을 `master` 브랜치에 **절대** 보내지 마십시오.

**Minor** features that are **fully backward compatible** with the current release may be sent to the latest stable branch (currently `9.x`).

현재 라라벨 릴리스와 **완전한 호환성** 을 가진 **마이너** 기능은 최신 안정 브랜치로(현재 `9.x` ) 보내 주시기 바랍니다.

**Major** new features or features with breaking changes should always be sent to the `master` branch, which contains the upcoming release.

**메이저** 새로운 또는 변경사항이 있는 기능은 다음 라라벨 릴리즈에 포함될 `master` 브랜치로 보내 주시기 바랍니다.

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

다음은 유효한 라라벨 문서 블록의 예시입니다. `@param` 속성 뒤에는 2개의 스페이스, 인수의 타입, 다시 2개의 스페이스, 마지막으로 변수의 이름이 위치합니다.

    /**
     * Register a binding with the container.
     *
     * @param  string|array  $abstract
     * @param  \Closure|string|null  $concrete
     * @param  bool  $shared
     * @return void
     *
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

코드 스타일링이 완벽하지 않아도 걱정하지 마세요! [StyleCI](https://styleci.io/)는 pull 요청이 병합된 후 모든 스타일 수정 사항을 라라벨 저장소에 자동으로 병합합니다. 이를 통해 우리는 코드 스타일이 아닌 기여 내용에 집중할 수 있습니다.

<a name="code-of-conduct"></a>
## Code of Conduct
## 행동 지침

The Laravel code of conduct is derived from the Ruby code of conduct. Any violations of the code of conduct may be reported to Taylor Otwell (taylor@laravel.com):

Laravel 행동 지침은 Ruby 행동 지침에서 파생되었습니다. 행동 지침 위반은 Taylor Otwell (taylor@laravel.com)에게 보고 될 수 있습니다.

- Participants will be tolerant of opposing views.
- 참여자는 반대 의견에 관대해야 합니다.
- Participants must ensure that their language and actions are free of personal attacks and disparaging personal remarks.
- 참여자는 말과 행동을 통해 인신공격을 하거나 헐뜯어서는 안 됩니다.
- When interpreting the words and actions of others, participants should always assume good intentions.
- 참여자는 다른 사람의 말이나 행동을 항상 좋은 의도로 해석해야 합니다.
- Behavior that can be reasonably considered harassment will not be tolerated.
- 타당하게 괴롭힘이라고 판단될 수 있는 행동은 용인되지 않을 것입니다.
