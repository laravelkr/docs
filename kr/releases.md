# Release Notes
# 릴리즈 노트

- [Versioning Scheme](#versioning-scheme)
- [버전 관리 체계](#versioning-scheme)
- [Support Policy](#support-policy)
- [지원 정책](#support-policy)
- [Laravel 10](#laravel-10)
- [라라벨 10](#laravel-10)

<a name="versioning-scheme"></a>
## Versioning Scheme
## 버전 관리 체계

Laravel and its other first-party packages follow [Semantic Versioning](https://semver.org). Major framework releases are released every year (~Q1), while minor and patch releases may be released as often as every week. Minor and patch releases should **never** contain breaking changes.

라라벨과 그외 자체적으로 제공하는 패키지들은 [시멘틱버저닝-유의적 버젼체계](https://semver.org/lang/ko/)을 따릅니다. 메이저 프레임워크 릴리즈는 1년마다 (~1/4분기) 릴리즈되며, 마이너 릴리스는 매주 여러번 릴리즈 될 수 있습니다. 마이너 릴리즈에는 이전 버전의 호환성을 깨뜨리는 변경 사항이 **없어야** 합니다.

When referencing the Laravel framework or its components from your application or package, you should always use a version constraint such as `^10.0`, since major releases of Laravel do include breaking changes. However, we strive to always ensure you may update to a new major release in one day or less.

애플리케이션에서 라라벨 프레임워크, 라라벨의 컴포넌트 또는 패키지를 참조할 때, 메이저 릴리즈에서는 (ex 9.x -> 10.x) 이전 버전과 호환성이 깨지는 변경사항을 포함하고 있기 때문에 항상 `^10.0` 와 같이 참조하도록 해야 합니다. 변경사항에 대해서는 하루 안에 새로운 릴리즈를 업데이트 할 수 있도록 노력하고 있습니다.

<a name="named-arguments"></a>
#### Named Arguments
#### 이름이 지정된 인자 전달-Named Arguments

[Named arguments](https://www.php.net/manual/en/functions.arguments.php#functions.named-arguments) are not covered by Laravel's backwards compatibility guidelines. We may choose to rename function arguments when necessary in order to improve the Laravel codebase. Therefore, using named arguments when calling Laravel methods should be done cautiously and with the understanding that the parameter names may change in the future.

[이름이 지정된 인자 전달](https://www.php.net/manual/en/functions.arguments.php#functions.named-arguments) 기능은 라라벨의 하위 호환성 지침에 포함되지 않습니다. 라라벨 코드베이스를 개선하기 위해 필요한 경우 함수 매개 변수의 이름을 바꿀 수 있습니다. 따라서 라라벨 메서드를 호출 할 때 명명 된 인수를 사용하는 경우에는 매개 변수 이름이 향후 변경 될 수 있음을 이해하고 신중하게 수행해야합니다.

<a name="support-policy"></a>
## Support Policy
## 지원 정책

For all Laravel releases, bug fixes are provided for 18 months and security fixes are provided for 2 years. For all additional libraries, including Lumen, only the latest major release receives bug fixes. In addition, please review the database versions [supported by Laravel](/docs/{{version}}/database#introduction).

모든 라라벨 릴리즈는 18개월의 버그 픽스와 2년동안의 보안 패치가 지원됩니다. Lumen 을 포함한 모든 추가 라이브러리의 경우 최신 메이저 릴리스에서만 버그 수정을 받습니다. 추가로 [라라벨이 지원하는](/docs/{{version}}/database#introduction) 데이터베이스 버전을 확인하세요.

| Version | PHP (*) | Release | Bug Fixes Until | Security Fixes Until |
| --- | --- | --- | --- | --- |
| 8 | 7.3 - 8.1 | September 8th, 2020 | July 26th, 2022 | January 24th, 2023 |
| 9 | 8.0 - 8.2 | February 8th, 2022 | August 8th, 2023 | February 6th, 2024 |
| 10 | 8.1 - 8.2 | February 14th, 2023 | August 6th, 2024 | February 4th, 2025 |
| 11 | 8.2 | Q1 2024 | August 5th, 2025 | February 3rd, 2026 |


| 버전 | PHP (*)   | 릴리즈               | 버그픽스 지원기간    | 보안 패치 지원기간   |
|----|-----------|-------------------|--------------|--------------|
| 8  | 7.3 - 8.1 | 2020년 9월 8일       | 2022년 7월 26일 | 2023년 1월 24일 |
| 9  | 8.0 - 8.2 | 2022년 2월 8일       | 2023년 8월 8일  | 2024년 2월 6일  |
| 10 | 8.1 - 8.2 | 2023년 2월 14일      | 2024년 8월 6일  | 2025년 2월 4일  |
| 11 | 8.2       | 2024년 1/4분기 | 2025년 8월 5일  | 2026년 2월 3일  |

<div class="version-colors">
    <div class="end-of-life">
        <div class="color-box"></div>
        <div>End of life</div>
    </div>
    <div class="security-fixes">
        <div class="color-box"></div>
        <div>Security fixes only</div>
    </div>
</div>

(*) Supported PHP versions

<a name="laravel-10"></a>
## Laravel 10
## 라라벨 10

As you may know, Laravel transitioned to yearly releases with the release of Laravel 8. Previously, major versions were released every 6 months. This transition is intended to ease the maintenance burden on the community and challenge our development team to ship amazing, powerful new features without introducing breaking changes. Therefore, we have shipped a variety of robust features to Laravel 9 without breaking backwards compatibility.

라라벨은 이전 버전까지는 6개월 마다 메이저 버전이 릴리즈 되었지만 라라벨 8 릴리즈와 함께 연간 릴리즈 정책으로 전환했습니다. 이러한 정책의 변경은 커뮤니티의 관리부담을 줄임과 동시에 개발팀에게 하위호환을 깨트리지 않고 새롭고 강력한 기능을 제공하는 도전과제를 가져다 주었습니다. 라라벨 10에서는 다양하고 강력한 기능이 추가되었습니다. 

Therefore, this commitment to ship great new features during the current release will likely lead to future "major" releases being primarily used for "maintenance" tasks such as upgrading upstream dependencies, which can be seen in these release notes.

그리고, 현재의 메이저 버전이 유지되는 동안 새로운 기능을 계속 추가해나가겠다는 약속으로 인해 의존성의 변경과 같은 작업은 미래의 "메이저" 릴리즈로 이어집니다.

Laravel 10 continues the improvements made in Laravel 9.x by introducing argument and return types to all application skeleton methods, as well as all stub files used to generate classes throughout the framework. In addition, a new, developer-friendly abstraction layer has been introduced for starting and interacting with external processes. Further, Laravel Pennant has been introduced to provide a wonderful approach to managing your application's "feature flags".

라라벨 10는 프레임워크 전체에서 클래스를 생성하는 데 사용되는 모든 stub 파일을 개선, 애플리케이션 스켈레톤 메서드에 반환타입을 도입하여 라라벨 9.x 를 개선합니다. 그리고 외부 프로세스를 시작하고 인터렉션하기 위한 개발자 친화적인 새로운 추상화 레이어를 도입하였습니다. 그리고 애플리케이션에 '기능 플래그'를 관리할 수 있는 기능을 제공하는 라라벨 Pennant를 도입하였습니다. 

<a name="php-8"></a>
### PHP 8.1
### PHP 8.1

Laravel 10.x requires a minimum PHP version of 8.1.

라라벨 10.x 은 PHP 8.1 이상버전을 필요로 합니다.

<a name="types"></a>
### Types
### 타입

_Application skeleton and stub type-hints were contributed by [Nuno Maduro](https://github.com/nunomaduro)_.

_애플리케이션 스켈레톤 및 stub 타입힌트는 [Nuno Maduro](https://github.com/nunomaduro)가 기여하였습니다_.  

On its initial release, Laravel utilized all of the type-hinting features available in PHP at the time. However, many new features have been added to PHP in the subsequent years, including additional primitive type-hints, return types, and union types.

처음 출시되었을 때 라라벨은 당시의 PHP에서 사용할 수 있는 모든 타입힌트 기능을 활용했습니다. 이후 몇 년 동안 기본 타입에 대한 타입힌트, 반환 타입 및 유니온 타입을 포함한 새로운 기능들이 PHP에 추가되었습니다. 

Laravel 10.x thoroughly updates the application skeleton and all stubs utilized by the framework to introduce argument and return types to all method signatures. In addition, extraneous "doc block" type-hint information has been deleted.

라라벨 10.x는 애플리케이션 스켈레톤과 프레임워크에서 사용하는 모든 stub을 업데이트하여 모든 메서드 시그니처에 인자 및 반환 타입을 추가했습니다. 또한 필요가 없어진 "doc block" 타입 힌트 정보를 삭제하였습니다.

This change is entirely backwards compatible with existing applications. Therefore, existing applications that do not have these type-hints will continue to function normally.

이 변경 사항은 기존 애플리케이션과도 호환됩니다. 따라서 이러한 타입 힌트가 없는 기존 애플리케이션도 정상적으로 작동합니다.

<a name="laravel-pennant"></a>
### Laravel Pennant
### 라라벨 Pennant

_Laravel Pennant was developed by [Tim MacDonald](https://github.com/timacdonald)_.

_라라벨 Pennant 은 [Tim MacDonald](https://github.com/timacdonald)에 의해 개발되었습니다_.

A new first-party package, Laravel Pennant, has been released. Laravel Pennant offers a light-weight, streamlined approach to managing your application's feature flags. Out of the box, Pennant includes an in-memory `array` driver and a `database` driver for persistent feature storage.

새로운 라라벨 고유 패키지인 라라벨 Pennant가 출시되었습니다. 라라벨 Pennant는 애플리케이션의 기능 플래그를 관리하기 위한 가볍고 간소화된 접근 방식을 제공합니다. 기본적으로 Pennant에는 메모리용 'array' 드라이버와 저장을 위한 'database' 드라이버가 포함되어 있습니다.

Features can be easily defined via the `Feature::define` method:

기능에 대한 정의는 `Feature::define` 메서드를 사용하여 손쉽게 정의할 수 있습니다.

```php
use Laravel\Pennant\Feature;
use Illuminate\Support\Lottery;

Feature::define('new-onboarding-flow', function () {
    return Lottery::odds(1, 10);
});
```

Once a feature has been defined, you may easily determine if the current user has access to the given feature:

기능을 정의하고나면 현재 사용자가 주어진 기능에 접근할 수 있는지 손쉽게 확인할 수 있습니다. 

```php
if (Feature::active('new-onboarding-flow')) {
    // ...
}
```

Of course, for convenience, Blade directives are also available:

편의를 위해서 블레이드 지시어도 사용가능합니다.

```blade
@feature('new-onboarding-flow')
    <div>
        <!-- ... -->
    </div>
@endfeature
```

Pennant offers a variety of more advanced features and APIs. For more information, please consult the [comprehensive Pennant documentation](/docs/{{version}}/pennant).

Pennant는 다양한 고급 기능과 API를 제공합니다. 자세한 내용은 [Pennant 매뉴얼](/docs/{{version}}/pennant)을 참고하십시오.

<a name="process"></a>
### Process Interaction
### 프로세스와 상호작용하기

_The process abstraction layer was contributed by [Nuno Maduro](https://github.com/nunomaduro) and [Taylor Otwell](https://github.com/taylorotwell)_.

_프로세스 추상화 레이어는 [Nuno Maduro](https://github.com/nunomaduro)와 [Taylor Otwell](https://github.com/taylorotwell)이 기여하였습니다_.

Laravel 10.x introduces a beautiful abstraction layer for starting and interacting with external processes via a new `Process` facade:

라라벨 10.x 은 새로운 `Process` 파사드를 사용하여 외부 프로세스를 다루는 추상화 레이어를 소개하였습니다. 

```php
use Illuminate\Support\Facades\Process;

$result = Process::run('ls -la');

return $result->output();
```

Processes may even be started in pools, allowing for the convenient execution and management of concurrent processes:

프로세스는 풀 안에서 시작할 수도 있고, 동시에 여러 프로세스를 편리하게 실행하고 관리할 수도 있습니다. 

```php
use Illuminate\Process\Pool;
use Illuminate\Support\Facades\Process;

[$first, $second, $third] = Process::concurrently(function (Pool $pool) {
    $pool->command('cat first.txt');
    $pool->command('cat second.txt');
    $pool->command('cat third.txt');
});

return $first->output();
```

In addition, processes may be faked for convenient testing:

테스트를 편리하게 하기 위해 프로세스를 fake 할 수도 있습니다.

```php
Process::fake();

// ...

Process::assertRan('ls -la');
```

For more information on interacting with processes, please consult the [comprehensive process documentation](/docs/{{version}}/processes).

프로세스를 다루는 보다 자세한 내용은 [프로세스 매뉴얼](/docs/{{version}}/processes)을 참고하십시오.

<a name="test-profiling"></a>
### Test Profiling
### 테스트 프로파일링

_Test profiling was contributed by [Nuno Maduro](https://github.com/nunomaduro)_.

_테스트 프로파일링은 [Nuno Maduro](https://github.com/nunomaduro)가 기여하였습니다_.

The Artisan `test` command has received a new `--profile` option that allows you to easily identify the slowest tests in your application:

Artisan `test` 명령어에는 새롭게 `--profile` 옵션을 사용할 수 있습니다. 이 옵션은 애플리케이션에서 가장 느린 테스트를 쉽게 식별할 수 있게 해줍니다. 

```shell
php artisan test --profile
```

For convenience, the slowest tests will be displayed directly within the CLI output:

편의를 위해 가장 느린 테스트는 CLI 출력에 곧바로 표시됩니다.

<p align="center">
    <img width="100%" src="https://user-images.githubusercontent.com/5457236/217328439-d8d983ec-d0fc-4cde-93d9-ae5bccf5df14.png"/>
</p>

<a name="pest-scaffolding"></a>
### Pest Scaffolding
### Pest 스캐폴딩

New Laravel projects may now be created with Pest test scaffolding by default. To opt-in to this feature, provide the `--pest` flag when creating a new application via the Laravel installer:

새로운 라라벨 프로젝트는 기본적으로 Pest 테스트 스캐폴딩을 생성할 수 있습니다. 이 기능을 사용하려면 라라벨 인스톨러를 사용하여 새로운 애플리케이션을 생성할 때 `--pest` 플래그를 사용하면 됩니다.

```shell
laravel new example-application --pest
```

<a name="generator-cli-prompts"></a>
### Generator CLI Prompts
### CLI 생성명령에서의 프롬프트

_Generator CLI prompts were contributed by [Jess Archer](https://github.com/jessarcher)_.

_CLI 생성명령에서의 프롬프트 개선사항은 [Jess Archer](https://github.com/jessarcher)가 기여하였습니다_.

To improve the framework's developer experience, all of Laravel's built-in `make` commands no longer require any input. If the commands are invoked without input, you will be prompted for the required arguments:

프레임워크의 개발자 경험을 향상시키기 위해 라라벨에 내장되어 있는 모든 `make` 명령어는 더 이상 추가적인 입력사항이 필요하지 않습니다. 대신 명령어가 실행될 때 필요한 인자를 묻는 프롬프트가 표시됩니다.

```shell
php artisan make:controller
```

<a name="horizon-telescope-facelift"></a>
### Horizon / Telescope Facelift
### Horizon / Telescope 디자인 개선

[Horizon](/docs/{{version}}/horizon) and [Telescope](/docs/{{version}}/telescope) have been updated with a fresh, modern look including improved typography, spacing, and design:

[Horizon](/docs/{{version}}/horizon) 및 [Telescope](/docs/{{version}}/telescope)가 타이포그래피의 향상, 간격 및 디자인을 포함하여 새롭고 현대적인 디자인으로 업데이트되었습니다.

<img src="https://laravel.com/img/docs/horizon-example.png">
