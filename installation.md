# 설치하기

- [라라벨을 만나보세요](#meet-laravel)
    - [왜 라라벨인가?](#why-laravel)
- [첫 번째 라라벨 프로젝트](#your-first-laravel-project)
- [라라벨 & 도커](#laravel-and-docker)
    - [macOS에서 시작하기](#getting-started-on-macos)
    - [Windows에서 시작하기](#getting-started-on-windows)
    - [Linux에서 시작하기](#getting-started-on-linux)
    - [Sail 서비스 선택](#choosing-your-sail-services)
- [초기 설정](#initial-configuration)
    - [환경 기반 설정](#environment-based-configuration)
    - [데이터베이스 & 마이그레이션](#databases-and-migrations)
- [다음 단계](#next-steps)
    - [라라벨 풀 스택 프레임워크](#laravel-the-fullstack-framework)
    - [Laravel API 백엔드](#laravel-the-api-backend)

<a name="meet-laravel"></a>
## 라라벨을 만나보세요

라라벨은 표현적이고 우아한 문법을 가진 웹 애플리케이션 프레임워크입니다. 웹 프레임워크는 애플리케이션을 만드는 구조와 시작점을 제공하므로, 세부 사항에 신경을 쓰는 동안 놀라운 것을 만드는 데 집중할 수 있습니다.

라라벨은 철저한 의존성 주입, 표현식 데이터베이스 추상화 계층, queues-큐 및 예약된 작업, 단위 및 통합 테스트 등과 같은 강력한 기능을 제공하면서 놀라운 개발자 경험을 제공하기 위해 노력하고 있습니다.

PHP 웹 프레임워크를 처음 접하거나 다년간의 경험이 있더라도 라라벨은 함께 성장할 수 있는 프레임워크입니다. 웹 개발자로서의 첫 걸음을 떼도록 돕거나 전문 지식을 한 단계 높일 수 있도록 도와드립니다. 우리는 당신이 무엇을 만들지 기대하고 있습니다.

> **Note**
> 라라벨이 처음인가요? 여러분의 첫 라라벨 애플리케이션을 만드는 과정을 하나하나 보여주는 실습 투어인 [영어-라라벨 부트캠프](https://bootcamp.laravel.com), [한국어-이현석의 라라벨 입문](https://www.youtube.com/playlist?list=PLZU-Fw0hnhQb8B6hCQek2xXd5zNkyu_3U)를 살펴보세요. 

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

여러분의 첫 번째 라라벨 프로젝트를 만들기 전에 로컬 머신에 PHP와 [컴포저](https://getcomposer.org)가 설치되어 있는지 확인해주세요. 만일 맥OS에서 개발하신다면 [홈브루](https://brew.sh/)를 통해 PHP와 컴포저를 설치할 수 있습니다. 추가적으로 [노드와 NPM도 설치하시길](https://nodejs.org) 권합니다.

PHP와 컴포저를 설치한 후 컴포저의 `create-project` 명령어를 이용해 새 라라벨 프로젝트를 만들 수 있습니다.

```nothing
composer create-project laravel/laravel:^9.0 example-app
```

또는 컴포저를 통해 라라벨 인스톨러를 전역적으로 설치해서 라라벨 프로젝트를 만들 수도 있습니다.

```nothing
composer global require laravel/installer

laravel new example-app
```

프로젝트가 생성되고 나면 라라벨의 아티즌 CLI `serve` 명령어를 이용해서 로컬 개발 서버를 시작하세요. 

```nothing
cd example-app

php artisan serve
```

아티즌 개발 서버가 시작되면 웹브라우저에서 `http://localhost:8000` 으로 애플리케이션에 접근할 수 있습니다. 이제 여러분은 [라라벨 에코시스템으로 가는 다음 스텝](#next-steps)을 시작할 준비가 되었습니다. 물론 [데이터베이스 설정](#databases-and-migrations)으로 넘어갈 수도 있습니다.

> **Note**
> 라라벨 애플리케이션을 개발할 때 한발 앞서 시작하려면 당사의 [스타터 키트](/docs/{{version}}/starter-kits) 중 하나를 사용하는 것을 고려하십시오 . 라라벨의 스타터 키트는 새로운 라라벨 애플리케이션을 위한 백엔드 및 프론트엔드 인증 스캐폴딩을 제공합니다.

<a name="laravel-and-docker"></a>
## 라라벨 & 도커

선호하는 운영 체제에 관계없이 최대한 쉽게 라라벨을 시작할 수 있기를 바랍니다. 그래서 로컬 머신에서 라라벨 프로젝트를 개발하고 실행하기 위한 다양한 옵션을 준비해두었습니다. 이러한 옵션들을 탐색하고 싶을 수도 있지만, 여기서는 우선 [도커](https://www.docker.com)를 이용해서 라라벨 프로젝트를 실행할 수 있는 빌트인 솔루션인 [Sail](/docs/{{version}}/sail)을 안내하겠습니다. 

도커는 로컬 시스템에 설치된 소프트웨어 또는 구성을 방해하지 않는 작고 가벼운 "컨테이너"에서 애플리케이션 및 서비스를 실행하기 위한 도구입니다. 즉, 로컬 시스템에서 웹 서버 및 데이터베이스와 같은 복잡한 개발 도구를 구성하거나 설정하는 것에 대해 걱정할 필요가 없습니다. 시작하려면 [도커 데스크탑](https://www.docker.com/products/docker-desktop)만 설치하면 됩니다 .

라라벨 Sail은 라라벨의 기본 도커 구성과 상호 작용하기 위한 경량 명령줄 인터페이스입니다. Sail은 도커를 다뤄본 경험 없이도 PHP, MySQL 및 Redis를 사용하는 라라벨 애플리케이션을 구축하기 위한 좋은 시작점을 제공합니다.

> **Note**
> 이미 도커 전문가이신가요? 괜찮아요! Sail에 대한 모든 것은 Laravel에 포함된 docker-compose.yml 파일을 사용하여 사용자 정의할 수 있습니다.

<a name="getting-started-on-macos"></a>
### macOS에서 시작하기

Mac에서 개발 중이며 [Docker Desktop](https://www.docker.com/products/docker-desktop)이 이미 설치되어 있다면 간단한 터미널 명령을 사용하여 새 라라벨 프로젝트를 만들 수 있습니다. 예를 들어, "example-app"라는 디렉터리에 새 라라벨 애플리케이션을 생성하려면 터미널에서 다음 명령을 실행합니다.

```shell
curl -s "https://laravel.build/example-app" | bash
```

물론 이 URL의 "example-app"을 원하는 대로 변경할 수 있습니다. 애플리케이션 이름은 영어, 숫자, 대시와 언더스코어로 이루어여있는지 확인하십시오. 라라벨 애플리케이션의 디렉토리는 명령을 실행한 디렉토리 내에 생성됩니다.

Sail 설치에는 시간이 좀 걸릴 수 있습니다.

프로젝트가 생성되면 애플리케이션 디렉토리로 이동하여 라라벨 Sail을 시작할 수 있습니다. 라라벨 Sail은 라라벨의 기본 Docker 구성과 상호 작용하기 위한 간단한 명령줄 인터페이스를 제공합니다.

```shell
cd example-app

./vendor/bin/sail up
```

애플리케이션의 Docker 컨테이너가 시작되면 웹 브라우저(http://localhost)에서 애플리케이션에 접근할 수 있습니다.

> **Note**
> 라라벨 Sail에 대해 자세히 알아보려면 [전체 문서](/docs/{{version}}/sail)를 검토하세요.

<a name="getting-started-on-windows"></a>
### Windows에서 시작하기

Windows 컴퓨터에서 새 라라벨 애플리케이션을 생성하기 전에 [Docker Desktop](https://www.docker.com/products/docker-desktop)을 설치해야 합니다. 다음으로 WSL2(Windows Subsystem for Linux 2)이 설치되어 활성화되어 있는지 확인해야 합니다. WSL을 사용하면 Windows 10에서 기본적으로 Linux 바이너리 실행 파일을 실행할 수 있습니다. WSL2를 설치하고 활성화하는 방법에 대한 정보는 Microsoft의 [개발자 환경 문서](https://docs.microsoft.com/en-us/windows/wsl/install-win10)에서 찾을 수 있습니다.

> **Note**
> WSL2를 설치하고 활성화한 후에는 Docker Desktop이 [WSL2 백엔드를 사용하도록 구성](https://docs.docker.com/docker-for-windows/wsl/)해야 합니다.

다음으로, 여러분은 첫 번째 라라벨 프로젝트를 만들 준비가 되었습니다. [Windows 터미널](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701?rtc=1&activetab=pivot:overviewtab)을 시작하고 WSL2 Linux 운영 체제의 새 터미널 세션을 시작합니다. 다음으로 간단한 터미널 명령을 사용하여 새 라라벨 프로젝트를 생성할 수 있습니다. 예를 들어, "example-app"이라는 디렉토리에 새로운 라라벨 애플리케이션을 생성하려면 터미널에서 다음 명령을 실행합니다.

```shell
curl -s https://laravel.build/example-app | bash
```

물론 이 URL의 "example-app"을 원하는 대로 변경할 수 있습니다. 라라벨 애플리케이션의 디렉토리는 명령을 실행한 디렉토리 내에 생성됩니다.

프로젝트가 생성되면 애플리케이션 디렉토리로 이동하여 라라벨 Sail을 시작할 수 있습니다. 라라벨 Sail은 라라벨의 기본 Docker 구성과 상호 작용하기 위한 간단한 명령줄 인터페이스를 제공합니다.

```shell
cd example-app

./vendor/bin/sail up
```

애플리케이션의 Docker 컨테이너가 시작되면 웹 브라우저(http://localhost)에서 애플리케이션에 접근할 수 있습니다.

> **Note**
> 라라벨 Sail에 대해 자세히 알아보려면 [전체 문서](/docs/{{version}}/sail)를 검토하세요.

#### Developing Within WSL2
#### WSL2 내에서 개발

물론 WSL2 설치 환경에 생성된 라라벨 애플리케이션 파일을 수정할 수 있어야 합니다. 이를 수행하려면 Microsoft의 [Visual Studio Code](https://code.visualstudio.com) 에디터와 자사의 [Remote Development](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack) extension을 사용하는 것이 좋습니다.

이러한 도구가 설치되면 Windows 터미널을 사용하여 애플리케이션의 루트 디렉토리에서 `code .` 명령을 실행하여 모든 라라벨 프로젝트를 열 수 있습니다. 

<a name="getting-started-on-linux"></a>
### Linux에서 시작하기

Linux에서 개발 중이며 [Docker Compose](https://docs.docker.com/compose/install/)가 이미 설치되어 있다면 간단한 터미널 명령을 사용하여 새 라라벨 프로젝트를 만들 수 있습니다. 예를 들어, "example-app"라는 디렉터리에 새 라라벨 애플리케이션을 생성하려면 터미널에서 다음 명령을 실행합니다.

```shell
curl -s https://laravel.build/example-app | bash
```

물론 이 URL의 "example-app"을 원하는 대로 변경할 수 있습니다. 라라벨 애플리케이션의 디렉토리는 명령을 실행한 디렉토리 내에 생성됩니다.

Sail 설치에는 시간이 좀 걸릴 수 있습니다.

프로젝트가 생성되면 애플리케이션 디렉토리로 이동하여 라라벨 Sail을 시작할 수 있습니다. 라라벨 Sail은 라라벨의 기본 Docker 구성과 상호 작용하기 위한 간단한 명령줄 인터페이스를 제공합니다.

```shell
cd example-app

./vendor/bin/sail up
```

애플리케이션의 Docker 컨테이너가 시작되면 웹 브라우저(http://localhost)에서 애플리케이션에 접근할 수 있습니다.

> **Note**
> 라라벨 Sail에 대해 자세히 알아보려면 [전체 문서](/docs/{{version}}/sail)를 검토하세요.

<a name="choosing-your-sail-services"></a>
### Sail 서비스 선택

Sail을 통해 새 라라벨 애플리케이션을 생성할 때 `with` 쿼리 문자열 변수를 사용하여 새 애플리케이션의 `docker-compose.yml` 파일에서 구성해야 하는 서비스를 선택할 수 있습니다. 사용 가능한 서비스에는 `mysql`, `pgsql`, `mariadb`, `redis`, `memcached`, `meilisearch`, `minio`, `selenium`, `mailpit`가 있습니다.

```shell
curl -s "https://laravel.build/example-app?with=mysql,redis" | bash
```

구성할 서비스를 지정하지 않으면 `mysql`, `redis`, `meilisearch`, `mailpit`, `selenium`의 기본 스택이 구성됩니다.

<a name="initial-configuration"></a>
## 초기 설정

라라벨 프레임워크의 모든 설정 파일은 `config` 디렉토리에 저장됩니다. 각 옵션은 문서화되어 있으므로 자유롭게 파일을 살펴보고 사용 가능한 옵션을 숙지하세요.

라라벨은 기본적으로 추가 설정이 거의 필요하지 않습니다. 자유롭게 개발을 시작할 수 있습니다! 그러나 `config/app.php` 파일과 문서를 검토해야 할 수도 있습니다. 애플리케이션에 따라 변경할 수 있는 `timezone` 및 `locale`과 같은 여러 옵션이 포함되어 있습니다.

<a name="environment-based-configuration"></a>
### 환경 기반 설정

라라벨의 설정 옵션 값은 애플리케이션이 로컬 머신에서 실행되는지 아니면 프로덕션 웹 서버에서 실행되는지에 따라 다를 수 있으므로 애플리케이션의 루트에 있는 `.env` 파일을 사용하여 많은 중요한 설정 값을 정의합니다.

애플리케이션을 사용하는 개발자/서버마다 다른 환경 설정이 필요할 수 있으므로 `.env` 파일은 애플리케이션의 버전 관리에 커밋되지 않아야 합니다. 또한 민감한 자격 증명이 노출될 수 있으므로 침입자가 버전 관리 저장소에 접근할 수 있는 경우 보안 위험이 됩니다.

> **Note**  
> `.env` 파일 및 환경 기반 설정에 대한 자세한 내용은 전체 [설정하기 문서](/docs/{{version}}/configuration#environment-configuration)를 확인하세요.

<a name="databases-and-migrations"></a>
### 데이터베이스 & 마이그레이션

이제 Laravel 애플리케이션을 만들었으므로 데이터베이스에 데이터를 저장하고 싶을 것입니다. 기본적으로 애플리케이션의 .env 구성 파일은 Laravel이 MySQL 데이터베이스와 상호 작용하고 MySQL 데이터베이스는 127.0.0.1로 접근하도록 설정되어 있습니다. macOS에서 개발 중이고 MySQL, Postgres 또는 Redis를 로컬로 설치해야 하는 경우 [DBngin](https://dbngin.com/) 을 활용하는 것이 편리할 수 있습니다 .

MySQL이나 Postgres를 여러분의 로컬 머신에 설치하고 싶지 않은 경우 [SQLite](https://www.sqlite.org/index.html) 데이터베이스를 사용할 수 있습니다. SQLite는 작고 빠른 독립형 데이터베이스 엔진입니다. SQLite를 사용하려면 빈 SQLite 파일을 만들어 SQLite 데이터베이스를 만듭니다. 일반적으로 이 파일은 Laravel 애플리케이션의 `database` 디렉토리에 둡니다.

```shell
touch database/database.sqlite
```

다음으로 `sqlite`를 쓰도록 `.env` 구성 파일을 수정합니다. 다른 데이터베이스 설정 옵션은 제거해도 됩니다.

```ini
DB_CONNECTION=sqlite # [tl! add]
DB_CONNECTION=mysql # [tl! remove]
DB_HOST=127.0.0.1 # [tl! remove]
DB_PORT=3306 # [tl! remove]
DB_DATABASE=laravel # [tl! remove]
DB_USERNAME=root # [tl! remove]
DB_PASSWORD= # [tl! remove]
```

SQLite 데이터베이스를 설정하고나면 애플리케이션의 데이터베이스 테이블을 생성해주는 [마이그레이션](/docs/{{version}}/migrations)을 실행할 수 있습니다.

```shell
php artisan migrate
```

<a name="next-steps"></a>
## 다음 단계

이제 여러분은 라라벨 프로젝트를 만들었기 때문에 다음에 무엇을 배워야 할지 궁금할 것입니다. 먼저 다음 문서를 읽고 라라벨이 작동하는 방식에 익숙해지는 것이 좋습니다. 

- [Request 라이프사이클](/docs/{{version}}/lifecycle)
- [설정하기](/docs/{{version}}/configuration)
- [디렉토리 구조](/docs/{{version}}/structure)
- [프론트엔드](/docs/{{version}}/frontend)
- [서비스 컨테이너](/docs/{{version}}/container)
- [파사드](/docs/{{version}}/facades)

라라벨을 어떻게 사용하고 싶은지에 따라 여정의 다음 단계도 결정됩니다. 라라벨을 사용하는 방법은 다양하며 아래 프레임워크에 대한 두 가지 주요 사용 사례를 살펴보겠습니다.

> **Note**
> 라라벨이 처음인가요? 여러분의 첫 라라벨 애플리케이션을 만드는 과정을 하나하나 보여주는 실습 투어인 [라라벨 부트캠프](https://bootcamp.laravel.com)를 살펴보세요. 

<a name="laravel-the-fullstack-framework"></a>
### 라라벨 풀 스택 프레임워크

라라벨은 풀 스택 프레임워크 역할을 할 수 있습니다. "풀 스택" 프레임워크는 라라벨을 사용하여 요청을 애플리케이션으로 라우팅하고 [블레이드 템플릿](/docs/{{version}}/blade) 또는 [Inertia.js](https://inertiajs.com)와 같은 단일 페이지 애플리케이션 하이브리드 기술을 통해 프런트엔드를 렌더링한다는 것을 의미합니다. 이것은 라라벨 프레임워크를 사용하는 가장 일반적인 방법이고 저희가 생각하기에 라라벨을 가장 생산적으로 사용하는 방법입니다.

라라벨을 이렇게 사용할 계획이라면 [프론트엔드 개발](/docs/{{version}}/frontend), [라우팅](/docs/{{version}}/routing), [뷰](/docs/{{version}}/views) 또는 [Eloquent ORM](/docs/{{version}}/eloquent)에 대한 문서를 확인하는 것이 좋습니다. 또한 [Livewire](https://laravel-livewire.com) 및 [Inertia](https://inertiajs.com)와 같은 커뮤니티 패키지에 대해 학습하는 데 관심이 있을 수 있습니다. 이 패키지를 사용하면 단일 페이지 자바스크립트 애플리케이션이 제공하는 많은 UI의 이점을 즐기면서 라라벨을 풀 스택 프레임워크로 사용할 수 있습니다.

라라벨을 풀 스택 프레임워크로 사용하는 경우 [Vite](/docs/{{version}}/vite)를 사용하여 애플리케이션의 CSS 및 자바스크립트를 컴파일하는 방법을 배우는 것을 강력히 권장합니다.

> **Note**
> 라라벨 애플리케이션을 개발할 때 한발 앞서 시작하려면 당사의 [스타터 키트](/docs/{{version}}/starter-kits) 중 하나를 사용하는 것을 고려하십시오 . 라라벨의 스타터 키트는 새로운 라라벨 애플리케이션을 위한 백엔드 및 프론트엔드 인증 스캐폴딩을 제공합니다.

<a name="laravel-the-api-backend"></a>
### Laravel API 백엔드

라라벨은 자바스크립트 단일 페이지 애플리케이션 또는 모바일 애플리케이션에 대한 API 백엔드 역할도 할 수 있습니다. 예를 들어, 라라벨을 [Next.js](https://nextjs.org) 애플리케이션의 API 백엔드로 사용할 수 있습니다. 이러한 맥락에서 라라벨을 사용하여 애플리케이션에 [인증](/docs/{{version}}/sanctum)과 데이터 저장/검색 기능을 제공하는 동시에 queues-큐, 이메일, 알림 등과 같은 Laravel의 강력한 서비스를 활용할 수 있습니다.

라라벨을 이렇게 사용할 계획이라면 [라우팅](/docs/{{version}}/routing), [라라벨 Sanctum](/docs/{{version}}/sanctum) 및 [Eloquent ORM](/docs/{{version}}/eloquent)에 대한 문서를 확인해 보는 것이 좋습니다.

> **Note**
> 라라벨 애플리케이션을 개발할 때 한발 앞서 시작하려면 당사의 [스타터 키트](/docs/{{version}}/starter-kits) 중 하나를 사용하는 것을 고려하십시오 . 라라벨의 스타터 키트는 새로운 라라벨 애플리케이션을 위한 백엔드 및 프론트엔드 인증 스캐폴딩을 제공합니다.
