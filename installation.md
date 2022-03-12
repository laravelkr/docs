# 설치하기

- [라라벨을 만나보세요](#meet-laravel)
    - [왜 라라벨인가?](#why-laravel)
- [첫 번째 라라벨 프로젝트](#your-first-laravel-project)
    - [macOS에서 시작하기](#getting-started-on-macos)
    - [Windows에서 시작하기](#getting-started-on-windows)
    - [Linux에서 시작하기](#getting-started-on-linux)
    - [Sail 서비스 선택](#choosing-your-sail-services)
    - [Composer를 통한 설치](#installation-via-composer)
- [초기 설정](#initial-configuration)
    - [환경 기반 설정](#environment-based-configuration)
    - [디렉토리 설정](#directory-configuration)
- [다음 단계](#next-steps)
    - [라라벨 풀 스택 프레임워크](#laravel-the-fullstack-framework)
    - [Laravel API 백엔드](#laravel-the-api-backend)

<a name="meet-laravel"></a>
## 라라벨을 만나보세요

라라벨은 표현적이고 우아한 문법을 가진 웹 애플리케이션 프레임워크입니다. 웹 프레임워크는 애플리케이션을 만드는 구조와 시작점을 제공하므로, 세부 사항에 신경을 쓰는 동안 놀라운 것을 만드는 데 집중할 수 있습니다.

라라벨은 철저한 의존성 주입, 표현식 데이터베이스 추상화 계층, queues-큐 및 예약된 작업, 단위 및 통합 테스트 등과 같은 강력한 기능을 제공하면서 놀라운 개발자 경험을 제공하기 위해 노력하고 있습니다.

PHP나 웹 프레임워크를 처음 접하거나 다년간의 경험이 있더라도 라라벨은 함께 성장할 수 있는 프레임워크입니다. 웹 개발자로서의 첫 걸음을 떼도록 돕거나 전문 지식을 한 단계 높일 수 있도록 도와드립니다. 우리는 당신이 무엇을 만들지 기대하고 있습니다.

<a name="why-laravel"></a>
### 왜 라라벨인가?

웹 애플리케이션을 구축할 때 사용할 수 있는 다양한 도구와 프레임워크가 있습니다. 그러나 우리는 라라벨이 현대적인 풀 스택 웹 애플리케이션을 구축하는 데 가장 적합한 선택이라고 생각합니다.

#### 점진적인 프레임워크

우리는 라라벨을 "점진적인" 프레임워크라고 부르고 싶습니다. 즉, 라라벨이 여러분과 함께 성장한다는 의미입니다. 웹 개발의 첫 단계를 막 시작하는 경우 라라벨의 방대한 문서, 가이드 및 [동영상 튜토리얼](https://laracasts.com)을 통해 부담 없이 배우는 데 도움이 될 것입니다.

당신이 시니어 개발자라면 라라벨은 [의존성 주입](/docs/{{version}}/container), [단위 테스트](/docs/{{version}}/testing), [queues-큐](/docs/{{version}}/queues), [실시간 이벤트](/docs/{{version}}/broadcasting) 등을 위한 강력한 도구를 제공합니다. 라라벨은 전문적인 웹 애플리케이션을 구축할 수 있도록 미세 조정되었으며 엔터프라이즈 작업 부하를 처리할 준비가 되어 있습니다.

#### 확장 가능한 프레임워크

라라벨은 확장성이 매우 뛰어납니다. PHP의 확장 친화적인 특성과 Redis와 같은 빠른 분산 캐시 시스템에 대한 라라벨의 내장 지원 덕분에 라라벨을 통한 수평 확장은 매우 쉽습니다. 실제로 라라벨 애플리케이션은 매달 수억 건의 요청을 처리할 수 있도록 쉽게 확장되었습니다.

극한의 확장이 필요하십니까? [라라벨 Vapor](https://vapor.laravel.com)와 같은 플랫폼을 사용하면 AWS의 최신 서버리스 기술에서 거의 무제한 규모로 라라벨 애플리케이션을 실행할 수 있습니다. 

#### 커뮤니티 프레임워크

라라벨은 PHP 생태계에서 최고의 패키지를 결합하여 사용 가능한 가장 강력하고 개발자 친화적인 프레임워크를 제공합니다. 또한 전 세계의 수천 명의 재능 있는 개발자들이 [프레임워크에 기여](https://github.com/laravel/framework) 하고 있습니다. 당신이 라라벨의 기여자가 될 수도 있습니다.

<a name="your-first-laravel-project"></a>
## 첫 번째 라라벨 프로젝트

우리는 라라벨을 가능한 한 쉽게 시작할 수 있기를 바랍니다. 자신의 컴퓨터에서 라라벨 프로젝트를 개발하고 실행하기 위한 다양한 옵션이 있습니다. 라라벨은 [Docker](https://www.docker.com)을 사용하여 라라벨 프로젝트를 실행하기 위한 기본 제공 솔루션인 [Sail](/docs/{version}/sail)을 제공합니다.

Docker는 로컬 컴퓨터에 설치된 소프트웨어 또는 구성을 방해하지 않는 작고 가벼운 "컨테이너"에서 애플리케이션 및 서비스를 실행하기 위한 도구입니다. 이는 개인 컴퓨터에서 웹 서버 및 데이터베이스와 같은 복잡한 개발 도구를 구성하거나 설정하는 것에 대해 걱정할 필요가 없음을 의미합니다. 시작하려면 [Docker Desktop](https://www.docker.com/products/docker-desktop)만 설치하면 됩니다.

라라벨 Sail은 라라벨의 기본 Docker 구성과 상호 작용하기 위한 경량 명령줄 인터페이스입니다. Sail은 사전 Docker 경험 없이도 PHP, MySQL 및 Redis를 사용하여 라라벨 애플리케이션을 구축할 수 있는 좋은 출발점을 제공합니다.

> {tip} 이미 Docker 전문가이신가요? 걱정하지 마세요! Sail에 관한 모든 것은 라라벨에 포함된 `docker-compose.yml` 파일을 사용하여 커스터마이징할 수 있습니다.

<a name="getting-started-on-macos"></a>
### macOS에서 시작하기

Mac에서 개발 중이며 [Docker Desktop](https://www.docker.com/products/docker-desktop)이 이미 설치되어 있다면 간단한 터미널 명령을 사용하여 새 라라벨 프로젝트를 만들 수 있습니다. 예를 들어, "example-app"라는 디렉터리에 새 라라벨 애플리케이션을 생성하려면 터미널에서 다음 명령을 실행합니다.

```nothing
curl -s "https://laravel.build/example-app" | bash
```

물론 이 URL의 "example-app"을 원하는 대로 변경할 수 있습니다. 라라벨 애플리케이션의 디렉토리는 명령을 실행한 디렉토리 내에 생성됩니다.

프로젝트가 생성되면 애플리케이션 디렉토리로 이동하여 라라벨 Sail을 시작할 수 있습니다. 라라벨 Sail은 라라벨의 기본 Docker 구성과 상호 작용하기 위한 간단한 명령줄 인터페이스를 제공합니다.

```nothing
cd example-app

./vendor/bin/sail up
```

Sail `up` 명령을 처음 실행하면 Sail의 애플리케이션 컨테이너가 시스템에 구축됩니다. 이 작업은 몇 분 정도 걸릴 수 있습니다. **걱정하지 마세요. 이후 Sail을 시작하는 것이 훨씬 빨라집니다.**

애플리케이션의 Docker 컨테이너가 시작되면 웹 브라우저(http://localhost)에서 애플리케이션에 접근할 수 있습니다.

> {tip} 라라벨 Sail에 대해 자세히 알아보려면 [전체 문서](/docs/{{version}}/sail)를 검토하세요.

<a name="getting-started-on-windows"></a>
### Windows에서 시작하기

Windows 컴퓨터에서 새 라라벨 애플리케이션을 생성하기 전에 [Docker Desktop](https://www.docker.com/products/docker-desktop)을 설치해야 합니다. 다음으로 WSL2(Windows Subsystem for Linux 2)이 설치되어 활성화되어 있는지 확인해야 합니다. WSL을 사용하면 Windows 10에서 기본적으로 Linux 바이너리 실행 파일을 실행할 수 있습니다. WSL2를 설치하고 활성화하는 방법에 대한 정보는 Microsoft의 [개발자 환경 문서](https://docs.microsoft.com/en-us/windows/wsl/install-win10)에서 찾을 수 있습니다.

> {tip} WSL2를 설치하고 활성화한 후에는 Docker Desktop이 [WSL2 백엔드를 사용하도록 구성](https://docs.docker.com/docker-for-windows/wsl/)해야 합니다.

다음으로, 여러분은 첫 번째 라라벨 프로젝트를 만들 준비가 되었습니다. [Windows 터미널](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701?rtc=1&activetab=pivot:overviewtab)을 시작하고 WSL2 Linux 운영 체제의 새 터미널 세션을 시작합니다. 다음으로 간단한 터미널 명령을 사용하여 새 라라벨 프로젝트를 생성할 수 있습니다. 예를 들어, "example-app"이라는 디렉토리에 새로운 라라벨 애플리케이션을 생성하려면 터미널에서 다음 명령을 실행합니다.

```nothing
curl -s https://laravel.build/example-app | bash
```

물론 이 URL의 "example-app"을 원하는 대로 변경할 수 있습니다. 라라벨 애플리케이션의 디렉토리는 명령을 실행한 디렉토리 내에 생성됩니다.

프로젝트가 생성되면 애플리케이션 디렉토리로 이동하여 라라벨 Sail을 시작할 수 있습니다. 라라벨 Sail은 라라벨의 기본 Docker 구성과 상호 작용하기 위한 간단한 명령줄 인터페이스를 제공합니다.

```nothing
cd example-app

./vendor/bin/sail up
```

Sail `up` 명령을 처음 실행하면 Sail의 애플리케이션 컨테이너가 시스템에 구축됩니다. 이 작업은 몇 분 정도 걸릴 수 있습니다. **걱정하지 마세요. 이후 Sail을 시작하는 것이 훨씬 빨라집니다.**

애플리케이션의 Docker 컨테이너가 시작되면 웹 브라우저(http://localhost)에서 애플리케이션에 접근할 수 있습니다.

> {tip} 라라벨 Sail에 대해 자세히 알아보려면 [전체 문서](/docs/{{version}}/sail)를 검토하세요.

#### WSL2 내에서 개발

물론 WSL2 설치 환경에 생성된 라라벨 애플리케이션 파일을 수정할 수 있어야 합니다. 이를 수행하려면 Microsoft의 [Visual Studio Code](https://code.visualstudio.com) 에디터와 자사의 [Remote Development](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack) extension을 사용하는 것이 좋습니다.

이러한 도구가 설치되면 Windows 터미널을 사용하여 애플리케이션의 루트 디렉토리에서 `code .` 명령을 실행하여 모든 라라벨 프로젝트를 열 수 있습니다. 

<a name="getting-started-on-linux"></a>
### Linux에서 시작하기

Linux에서 개발 중이며 [Docker](https://www.docker.com)가 이미 설치되어 있다면 간단한 터미널 명령을 사용하여 새 라라벨 프로젝트를 만들 수 있습니다. 예를 들어, "example-app"라는 디렉터리에 새 라라벨 애플리케이션을 생성하려면 터미널에서 다음 명령을 실행합니다.

```nothing
curl -s https://laravel.build/example-app | bash
```

물론 이 URL의 "example-app"을 원하는 대로 변경할 수 있습니다. 라라벨 애플리케이션의 디렉토리는 명령을 실행한 디렉토리 내에 생성됩니다.

프로젝트가 생성되면 애플리케이션 디렉토리로 이동하여 라라벨 Sail을 시작할 수 있습니다. 라라벨 Sail은 라라벨의 기본 Docker 구성과 상호 작용하기 위한 간단한 명령줄 인터페이스를 제공합니다.

```nothing
cd example-app

./vendor/bin/sail up
```

Sail `up` 명령을 처음 실행하면 Sail의 애플리케이션 컨테이너가 시스템에 구축됩니다. 이 작업은 몇 분 정도 걸릴 수 있습니다. **걱정하지 마세요. 이후 Sail을 시작하는 것이 훨씬 빨라집니다.**

애플리케이션의 Docker 컨테이너가 시작되면 웹 브라우저(http://localhost)에서 애플리케이션에 접근할 수 있습니다.

> {tip} 라라벨 Sail에 대해 자세히 알아보려면 [전체 문서](/docs/{{version}}/sail)를 검토하세요.

<a name="choosing-your-sail-services"></a>
### Sail 서비스 선택

Sail을 통해 새 라라벨 애플리케이션을 생성할 때 `with` 쿼리 문자열 변수를 사용하여 새 애플리케이션의 `docker-compose.yml` 파일에서 구성해야 하는 서비스를 선택할 수 있습니다. 사용 가능한 서비스에는 `mysql`, `pgsql`, `mariadb`, `redis`, `memcached`, `meilisearch`, `minio`, `selenium`, 그리고 `mailhog`가 있습니다.

```nothing
curl -s "https://laravel.build/example-app?with=mysql,redis" | bash
```

구성할 서비스를 지정하지 않으면 `mysql`, `redis`, `meilisearch`, `mailhog`, 그리고 `selenium`의 기본 스택이 구성됩니다.

<a name="installation-via-composer"></a>
### Composer를 통한 설치

컴퓨터에 PHP 및 Composer가 이미 설치되어 있는 경우 Composer를 직접 사용하여 새 라라벨 프로젝트를 생성할 수 있습니다. 애플리케이션이 생성된 후 Artisan CLI의 `serve` 명령을 사용하여 라라벨의 로컬 개발 서버를 시작할 수 있습니다.

    composer create-project laravel/laravel example-app

    cd example-app

    php artisan serve

<a name="the-laravel-installer"></a>
#### 라라벨 인스톨러

또는 라라벨 인스톨러를 전역 Composer를 이용해서 설치 할 수 있습니다.

```nothing
composer global require laravel/installer

laravel new example-app

cd example-app

php artisan serve
```

시스템에서 `laravel` 실행 파일을 찾을 수 있도록 Composer의 시스템 전체 vendor bin 디렉토리를 `$PATH`에 배치해야 합니다. 이 디렉토리는 운영 체제에 따라 다른 위치에 있습니다. 그러나 몇 가지 운영 체제에서의 일반적인 위치는 다음과 같습니다.

- macOS: `$HOME/.composer/vendor/bin`
- Windows: `%USERPROFILE%\AppData\Roaming\Composer\vendor\bin`
- GNU / Linux Distributions: `$HOME/.config/composer/vendor/bin` or `$HOME/.composer/vendor/bin`

편의를 위해 라라벨 인스톨러는 새 프로젝트에 사용할 Git 저장소를 생성할 수도 있습니다. Git 저장소를 생성하려는 것을 나타내려면 새 프로젝트를 생성할 때 `--git` 플래그를 전달합니다.

```bash
laravel new example-app --git
```

이 명령은 프로젝트의 새 Git 저장소를 초기화하고 기본 라라벨 스켈레톤을 자동으로 커밋합니다. `git` 플래그는 Git을 올바르게 설치하고 구성했다고 가정합니다. `--branch` 플래그를 사용하여 초기 브랜치 이름을 설정할 수도 있습니다.

```bash
laravel new example-app --git --branch="main"
```

`--git` 플래그를 사용하는 대신 `--github` 플래그를 사용하여 Git 저장소를 생성하고 GitHub에 해당 private 저장소를 만들 수도 있습니다.

```bash
laravel new example-app --github
```

생성된 저장소는 `https://github.com/<your-account>/example-app`에서 사용할 수 있습니다. `github` 플래그는 [GitHub CLI](https://cli.github.com)를 제대로 설치하고 GitHub에서 인증했다고 가정합니다. 또한 `git`이 설치되고 제대로 구성되어 있어야 합니다. 필요한 경우 GitHub CLI에서 지원하는 추가 플래그를 전달할 수 있습니다.

```bash
laravel new example-app --github="--public"
```

`--organization` 플래그를 사용하여 특정 GitHub 조직 아래에 저장소를 만들 수 있습니다.

```bash
laravel new example-app --github="--public" --organization="laravel"
```

<a name="initial-configuration"></a>
## 초기 설정

라라벨 프레임워크의 모든 설정 파일은 `config` 디렉토리에 저장됩니다. 각 옵션은 문서화되어 있으므로 자유롭게 파일을 살펴보고 사용 가능한 옵션을 숙지하세요.

라라벨은 기본적으로 추가 설정이 거의 필요하지 않습니다. 자유롭게 개발을 시작할 수 있습니다! 그러나 `config/app.php` 파일과 문서를 검토해야 할 수도 있습니다. 애플리케이션에 따라 변경할 수 있는 `timezone` 및 `locale`과 같은 여러 옵션이 포함되어 있습니다.

<a name="environment-based-configuration"></a>
### 환경 기반 설정

라라벨의 설정 옵션 값은 애플리케이션이 로컬 컴퓨터에서 실행되는지 아니면 프로덕션 웹 서버에서 실행되는지에 따라 다를 수 있으므로 애플리케이션의 루트에 있는 `.env` 파일을 사용하여 많은 중요한 설정 값을 정의합니다.

애플리케이션을 사용하는 개발자/서버마다 다른 환경 설정이 필요할 수 있으므로 `.env` 파일은 애플리케이션의 버전 관리에 커밋되지 않아야 합니다. 또한 민감한 자격 증명이 노출될 수 있으므로 침입자가 버전 관리 저장소에 접근할 수 있는 경우 보안 위험이 됩니다.

> {tip} `.env` 파일 및 환경 기반 설정에 대한 자세한 내용은 전체 [설정하기 문서](/docs/{{version}}/configuration#environment-configuration)를 확인하세요.

<a name="directory-configuration"></a>
### 디렉토리 설정

라라벨은 항상 웹 서버에 대해 설정된 "웹 디렉토리"의 루트에서 제공되어야 합니다. "웹 디렉토리"의 하위 디렉토리에서 라라벨 애플리케이션을 제공하려고 시도해서는 안 됩니다. 그렇게 하면 애플리케이션 내에 있는 민감한 파일이 노출될 수 있습니다.

<a name="next-steps"></a>
## 다음 단계

이제 여러분은 라라벨 프로젝트를 만들었기 때문에 다음에 무엇을 배워야 할지 궁금할 것입니다. 먼저 다음 문서를 읽고 라라벨이 작동하는 방식에 익숙해지는 것이 좋습니다. 

- [Request 라이프사이클](/docs/{{version}}/lifecycle)
- [설정하기](/docs/{{version}}/configuration)
- [디렉토리 구조](/docs/{{version}}/structure)
- [서비스 컨테이너](/docs/{{version}}/container)
- [파사드](/docs/{{version}}/facades)

라라벨을 어떻게 사용하고 싶은지에 따라 여정의 다음 단계도 결정됩니다. 라라벨을 사용하는 방법은 다양하며 아래 프레임워크에 대한 두 가지 주요 사용 사례를 살펴보겠습니다.

<a name="laravel-the-fullstack-framework"></a>
### 라라벨 풀 스택 프레임워크

라라벨은 풀 스택 프레임워크 역할을 할 수 있습니다. "풀 스택" 프레임워크는 라라벨을 사용하여 요청을 애플리케이션으로 라우팅하고 [블레이드 템플릿](/docs/{{version}}/blade) 또는 [Inertia.js](https://inertiajs.com)와 같은 단일 페이지 애플리케이션 하이브리드 기술을 통해 프런트엔드를 렌더링한다는 것을 의미합니다. 이것은 라라벨 프레임워크를 사용하는 가장 일반적인 방법입니다.

라라벨을 이렇게 사용할 계획이라면 [라우팅](/docs/{{version}}/routing), [뷰](/docs/{{version}}/views) 또는 [Eloquent ORM](/docs/{{version}}/eloquent)에 대한 문서를 확인하는 것이 좋습니다. 또한 [Livewire](https://laravel-livewire.com) 및 [Inertia.js](https://inertiajs.com)와 같은 커뮤니티 패키지에 대해 학습하는 데 관심이 있을 수 있습니다. 이 패키지를 사용하면 단일 페이지 자바스크립트 애플리케이션이 제공하는 많은 UI의 이점을 즐기면서 라라벨을 풀 스택 프레임워크로 사용할 수 있습니다.

라라벨을 풀 스택 프레임워크로 사용하는 경우 [Laravel Mix](/docs/{{version}}/mix)를 사용하여 애플리케이션의 CSS 및 자바스크립트를 컴파일하는 방법을 배우는 것을 강력히 권장합니다.

> {tip} 애플리케이션 구축을 시작하려면 공식 [애플리케이션 스타터 킷](/docs/{{version}}/starter-kits) 중 하나를 확인하세요.

<a name="laravel-the-api-backend"></a>
### Laravel API 백엔드

라라벨은 자바스크립트 단일 페이지 애플리케이션 또는 모바일 애플리케이션에 대한 API 백엔드 역할도 할 수 있습니다. 예를 들어, 라라벨을 [Next.js](https://nextjs.org) 애플리케이션의 API 백엔드로 사용할 수 있습니다. 이러한 맥락에서 라라벨을 사용하여 애플리케이션에 [인증](/docs/{{version}}/sanctum)과 데이터 저장/검색 기능을 제공하는 동시에 queues-큐, 이메일, 알림 등과 같은 Laravel의 강력한 서비스를 활용할 수 있습니다.

라라벨을 이렇게 사용할 계획이라면 [라우팅](/docs/{{version}}/routing), [Laravel Sanctum](/docs/{{version}}/sanctum) 및 [Eloquent ORM](/docs/{{version}}/eloquent)에 대한 문서를 확인해 보는 것이 좋습니다.

> {tip} 라라벨 백엔드와 Next.js 프론트엔드를 시작하기 위한 스캐폴딩이 필요하십니까? 라라벨 Breeze는 [API 스택](/docs/{{version}}/starter-kits#breeze-and-next)과 [Next.js 프론트엔드 구현](https://github.com/laravel/breeze-next) 을 제공하므로 몇 분 안에 시작할 수 있습니다.