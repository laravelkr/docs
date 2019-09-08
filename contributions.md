# 기여 가이드

- [버그 리포트](#bug-reports)
- [코어 개발에 대한 논의](#core-development-discussion)
- [브랜치 선택?](#which-branch)
- [보안 취약점](#security-vulnerabilities)
- [코딩 스타일](#coding-style)
    - [PHPDoc](#phpdoc)
    - [StyleCI](#styleci)

<a name="bug-reports"></a>
## 버그 리포트

활발한 협력을 장려하기 위해, 라라벨은 버그 리포트가 아니라 PR을 권장하고 있습니다. "버그 리포트"는 문제가 있는 테스트를 포함한 PR로 보내져야 합니다.

버그 리포트를 작성하는 경우, 이슈는 제목과 이슈에 대한 명확한 설명을 포함해야 합니다. 또한 해당 이슈에 대한 가능한 많은 관련 정보와 데모 코드 샘플을 포함해야 합니다. 버그 리포트의 목적은 여러분 자신과 다른 사람들이 쉽게 버그를 재현하고, 수정할 수 있게 하는 것입니다.

버그 리포트를 통해 같은 문제를 겪고 있는 다른 사람들과 협력해서 그 문제를 해결할 수 있다는 것을 명심하세요. 버그 리포트가 자동으로 어떤 활동을 만들어 내거나, 다른 이들이 버그을 고칠 것이라는 기대를 하지 마십시오. 버그 리포트 제출은 여러분 자신과 다른 사람이 문제를 해결할 수 있는 길에 들어설 수 있도록 하는 것입니다.

라라벨의 소스 코드는 GitHub에서 관리되며 각각의 라라벨 프로젝트의 저장소가 있습니다.

- [라라벨 애플리케이션](https://github.com/laravel/laravel)
- [라라벨 아트웤](https://github.com/laravel/art)
- [라라벨 매뉴얼](https://github.com/laravel/docs)
- [라라벨 캐셔](https://github.com/laravel/cashier)
- [Braintree를 위한 라라벨 캐셔](https://github.com/laravel/cashier-braintree)
- [라라벨 Envoy](https://github.com/laravel/envoy)
- [라라벨 프레임워크](https://github.com/laravel/framework)
- [라라벨 홈스테드](https://github.com/laravel/homestead)
- [라라벨 홈스테드 빌드스크립트](https://github.com/laravel/settler)
- [라라벨 Horizon](https://github.com/laravel/horizon)
- [라라벨 Nova](https://github.com/laravel/nova-issues)
- [라라벨 패스포트(Passport)](https://github.com/laravel/passport)
- [라라벨 스카웃(Scout)](https://github.com/laravel/scout)
- [라라벨 Socialite](https://github.com/laravel/socialite)
- [라라벨 웹사이트](https://github.com/laravel/laravel.com)

<a name="core-development-discussion"></a>
## 코어 개발에 대한 논의

새로운 기능이나 라라벨의 동작에 대한 개선을 제안하고자 하는 경우는 라라벨 아이디어 [이슈 보드](https://github.com/ideas/internals/issues)에 제안하십시오. 새로운 기능을 제안하는 경우 자발적으로 기능을 구동시키는 데 필요한 코드를 최소한으로 구현하시기 바랍니다.

버그와 관련 및 새로운 기능과 기존 기능의 구현에 대한 격식에 얽매이지 않은 일상적인 논의는 [LaraChat](https://larachat.co)의 `#internals` 채널에서 진행됩니다. 라라벨의 메인 관리자인 Taylor Otwell은 일반적으로 평일 오전 8시부터 5시까지 (America/Chicago 표준시 UTC-06:00 기준) 접속해 있고, 그 외에 다른 시간대에는 가끔 접속합니다.

<a name="which-branch"></a>
## 브랜치 선택

**모든** 버그 수정은 최신 안정 브랜치 또는 현재의 브랜치 (5.6)에 보내져야 합니다. 다음 릴리스에만 존재하는 기능에 대한 수정사항이 아니라면 버그 수정사항을 `master` 브랜치에 **절대** 보내지 마십시오.

현재 라라벨 릴리스와 **완전한 호환성** 을 가진 **마이너** 기능은 최신 안정 브랜치로 보내 주시기 바랍니다.

다음 라라벨 릴리즈에 포함될, **주요한** 새로운 기능들은 항상 `master` 브랜치로 보내 주시기 바랍니다.

만약 당신의 새로운 기능이 메이저인지 마이너인지 명확하지 않으면 [LaraChat](https://larachat.co) 의 `#internals` 채널에서 Taylor Otwell에 문의하십시오.

<a name="security-vulnerabilities"></a>
## 보안 취약점

만약 라라벨에서 보안 취약점을 발견했다면 테일러 오트웰 <a href="mailto:taylor@laravel.com">taylor@laravel.com</a>에게 이메일을 보내주시길 바랍니다. 모든 보안 취약점은 신속하게 해결될 것입니다.

<a name="coding-style"></a>
## 코딩 스타일

라라벨은 [PSR-2](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md)와 [PSR-4](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader.md) 코딩 표준을 따르고 있습니다.

<a name="phpdoc"></a>
### PHPDoc

다음은 유효한 라라벨 문서 블록의 예시입니다. `@param` 속성 뒤에는 2개의 스페이스, 인수의 타입, 다시 2개의 스페이스, 마지막으로 변수의 이름이 위치합니다:

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

<a name="styleci"></a>
### StyleCI

코드의 스타일이 완벽하지 않더라도 걱정하지 마십시오. PR이 Merge 될 때 [StyleCI](https://styleci.io/)가 자동으로 스타일을 수정하고 라라벨 저장소에 머지할 것입니다. 이렇게 하면 코드 스타일이 아니라 기여의 내용에 집중할 수 있습니다.
