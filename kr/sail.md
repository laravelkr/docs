# Laravel Sail
# 라라벨 Sail

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Installation & Setup](#installation)
- [설치 & 설정](#installation)
    - [Installing Sail Into Existing Applications](#installing-sail-into-existing-applications)
    - [기존 어플리케이션에 Sail 설치하기](#installing-sail-into-existing-applications)
    - [Configuring A Bash Alias](#configuring-a-bash-alias)
    - [Bash Alias 설정](#configuring-a-bash-alias)
- [Starting & Stopping Sail](#starting-and-stopping-sail)
- [Sail 시작 & 정지](#starting-and-stopping-sail)
- [Executing Commands](#executing-sail-commands)
- [명령어 실행](#executing-sail-commands)
    - [Executing PHP Commands](#executing-php-commands)
    - [PHP 명령어 실행](#executing-php-commands)
    - [Executing Composer Commands](#executing-composer-commands)
    - [Composer 명령어 실행](#executing-composer-commands)
    - [Executing Artisan Commands](#executing-artisan-commands)
    - [Artisan 명령어 실행](#executing-artisan-commands)
    - [Executing Node / NPM Commands](#executing-node-npm-commands)
    - [Node / NPM 명령어 실행](#executing-node-npm-commands)
- [Interacting With Databases](#interacting-with-sail-databases)
- [데이터베이스와 연결](#interacting-with-sail-databases)
    - [MySQL](#mysql)
    - [MySQL](#mysql)
    - [Redis](#redis)
    - [Redis](#redis)
    - [MeiliSearch](#meilisearch)
    - [MeiliSearch](#meilisearch)
- [File Storage](#file-storage)
- [파일 저장소](#file-storage)
- [Running Tests](#running-tests)
- [테스팅 하기](#running-tests)
    - [Laravel Dusk](#laravel-dusk)
    - [라라벨 Dusk](#laravel-dusk)
- [Previewing Emails](#previewing-emails)
- [이메일 미리보기](#previewing-emails)
- [Container CLI](#sail-container-cli)
- [컨테이너 CLI](#sail-container-cli)
- [PHP Versions](#sail-php-versions)
- [PHP 버전](#sail-php-versions)
- [Node Versions](#sail-node-versions)
- [Node 버전](#sail-node-versions)
- [Sharing Your Site](#sharing-your-site)
- [사이트 공유하기](#sharing-your-site)
- [Debugging With Xdebug](#debugging-with-xdebug)
- [Xdebug 로 디버깅](#debugging-with-xdebug)
  - [Xdebug CLI Usage](#xdebug-cli-usage)
  - [Xdebug CLI 사용방법](#xdebug-cli-usage)
  - [Xdebug Browser Usage](#xdebug-browser-usage)
  - [Xdebug 브라우저 사용방법](#xdebug-browser-usage)
- [Customization](#sail-customization)
- [사용자정의](#sail-customization)

<a name="introduction"></a>
## Introduction
## 시작하기

[Laravel Sail](https://github.com/laravel/sail) is a light-weight command-line interface for interacting with Laravel's default Docker development environment. Sail provides a great starting point for building a Laravel application using PHP, MySQL, and Redis without requiring prior Docker experience.

[Laravel Sail](https://github.com/laravel/sail)은 Laravel의 기본 Docker 개발 환경과 상호 작용하기 위한 간단한 명령줄 인터페이스입니다. Sail은 도커에 대한 경험이 없더라도 PHP, MySQL 및 Redis를 사용하여 Laravel 애플리케이션을 구축할 수 있는 좋은 출발점을 제공합니다.

역자주: 만약 도커가 익숙 하시지 않다면 [Docker Docs](https://docs.docker.com/get-started/overview/) 에서 기본적인 내용을 숙지하시고 시작하시는걸 추천드립니다.

At its heart, Sail is the `docker-compose.yml` file and the `sail` script that is stored at the root of your project. The `sail` script provides a CLI with convenient methods for interacting with the Docker containers defined by the `docker-compose.yml` file.

기본적으로 Sail은 `docker-compose.yml` 파일이며 프로젝트의 루트에 저장된 `sail` 스크립트입니다. `sail` 스크립트는 `docker-compose.yml` 파일로 정의된 도커 컨테이너와 상호 작용할 수 있는 편리한 방법을 CLI로 제공합니다.

Laravel Sail is supported on macOS, Linux, and Windows (via [WSL2](https://docs.microsoft.com/en-us/windows/wsl/about)).

라라벨 Sail 은 macOS, Linux 그리고 Windows(via [WSL2](https://docs.microsoft.com/en-us/windows/wsl/about)) 을 지원합니다

<a name="installation"></a>
## Installation & Setup
## 설치 & 설정

Laravel Sail is automatically installed with all new Laravel applications so you may start using it immediately. To learn how to create a new Laravel application, please consult Laravel's [installation documentation](/docs/{{version}}/installation) for your operating system. During installation, you will be asked to choose which Sail supported services your application will be interacting with.

Laravel Sail은 새로은 Laravel 애플리케이션과 함께 자동으로 설치되므로 즉시 사용할 수 있습니다. 새로운 Laravel 애플리케이션을 만드는 방법은 운영 체제에 대한 Laravel의 [설치 설명서](/docs/{{version}}/installation)를 참조하십시오. 설치하는 동안 어플리케이션과 상호 작용할 Sail 지원 서비스를 선택하라는 메시지가 표시됩니다.

역자주: Sail 기본 도커컨테이너에는 많은것들이 기본으로 포함되어 있습니다. 그 중 필요하거나 필요없는 컨테이너를 구분해서 도커가 빌드되게 하여 어플리케이션 도커를 가볍게 유지하는것을 추천드립니다.

<a name="installing-sail-into-existing-applications"></a>
### Installing Sail Into Existing Applications
### 기존 어플리케이션에 Sail 설치하기

If you are interested in using Sail with an existing Laravel application, you may simply install Sail using the Composer package manager. Of course, these steps assume that your existing local development environment allows you to install Composer dependencies:

기존 Laravel 어플리케이션에서 Sail을 사용하려면 Composer를 사용하여 Sail을 설치하면 됩니다. 물론 이러한 단계에서는 기존 로컬 개발 환경에서 Composer 종속성을 설치할 수 있다고 가정합니다.


    composer require laravel/sail --dev

After Sail has been installed, you may run the `sail:install` Artisan command. This command will publish Sail's `docker-compose.yml` file to the root of your application:

Sail 이 설치 된 후에 `sail:install` Artisan 명령어를 실행 할 수 있습니다. 해당 명령어는 Sail의 `docker-compose.yml` 파일을 프로젝트 루트에 생성합니다.

    php artisan sail:install

Finally, you may start Sail. To continue learning how to use Sail, please continue reading the remainder of this documentation:

이제 Sail을 시작 할 수 있습니다. Sail을 사용하는 방법을 더 알고 싶다면, 이 문서를 계속 읽고 숙지하여 주십시오.

    ./vendor/bin/sail up

<a name="using-devcontainers"></a>
#### Using Devcontainers
#### Devcontainers 를 사용하는 방법

If you would like to develop within a [Devcontainer](https://code.visualstudio.com/docs/remote/containers), you may provide the `--devcontainer` option to the `sail:install` command. The `--devcontainer` option will instruct the `sail:install` command to publish a default `.devcontainer/devcontainer.json ` file to the root of your application:

[Devcontainer](https://code.visualstudio.com/docs/remote/containers) 내에서 개발하려는 경우 `sail:install` 명령에 `--devcontainer` 옵션을 제공할 수 있습니다. `--devcontainer` 옵션은 `sail:install` 명령을 지시하여 기본 `.devcontainer/devcontainer.json` 파일을 프로젝트 루트에 생성합니다.

    php artisan sail:install --devcontainer

<a name="configuring-a-bash-alias"></a>
### Configuring A Bash Alias
### Bash Alias 설정

By default, Sail commands are invoked using the `vendor/bin/sail` script that is included with all new Laravel applications:

기본적으로 Sail 명령어는 라라벨 어플리케이션에 포함된 `vendor/bin/sail` 스크립트로 호출됩니다.

```bash
./vendor/bin/sail up
```

However, instead of repeatedly typing `vendor/bin/sail` to execute Sail commands, you may wish to configure a Bash alias that allows you to execute Sail's commands more easily:

`vendor/bin/sail` 을 직접 타이핑해서 Sail 명령어를 실행하기 보다, Bash alias 를 설정하여 Sail 의 명령어를 더 쉽게 사용할 수 있습니다.

```bash
alias sail='[ -f sail ] && bash sail || bash vendor/bin/sail'
```

Once the Bash alias has been configured, you may execute Sail commands by simply typing `sail`. The remainder of this documentation's examples will assume that you have configured this alias:

Bash alias가 구성되면 `sail`을 입력하여 Sail 명령을 실행할 수 있습니다. 이 문서의 나머지 예에서는 이 alias를 구성한 것으로 가정합니다.

```bash
sail up
```

<a name="starting-and-stopping-sail"></a>
## Starting & Stopping Sail
## Sail 시작 & 정지

Laravel Sail's `docker-compose.yml` file defines a variety of Docker containers that work together to help you build Laravel applications. Each of these containers is an entry within the `services` configuration of your `docker-compose.yml` file. The `laravel.test` container is the primary application container that will be serving your application.

Laravel Sail의 `docker-compose.yml` 파일에는 Laravel 애플리케이션을 구축하는 데 도움이 되는 다양한 Docker 컨테이너가 정의되어 있습니다. 이러한 각 컨테이너는 `docker-compose.yml` 파일의 `services` 구성 내에 있는 항목입니다. `laravel.test` 컨테이너는 기본 어플리케이션 컨테이너입니다.

Before starting Sail, you should ensure that no other web servers or databases are running on your local computer. To start all of the Docker containers defined in your application's `docker-compose.yml` file, you should execute the `up` command:

Sail을 시작하기 전에 작업중인 로컬 환경에서 실행 중인 다른 웹 서버 또는 데이터베이스가 없는지 확인해야 합니다. 프로젝트 내의 `docker-compose.yml` 파일에 정의된 모든 도커 컨테이너를 시작하려면 `up` 명령을 실행해야 합니다

```bash
sail up
```

To start all of the Docker containers in the background, you may start Sail in "detached" mode:

모든 도커 컨테이너를 백그라운드에서 실행시키고 싶다면 "detached" 모드로 Sail 을 시작해야 합니다.

```bash
sail up -d
```

Once the application's containers have been started, you may access the project in your web browser at: http://localhost.

프로젝트의 컨테이너들이 시작되었다면 http://localhost 로 프로젝트의 어플리케이션을 접근할 수 있습니다.

To stop all of the containers, you may simply press Control + C to stop the container's execution. Or, if the containers are running in the background, you may use the `stop` command:

Control + C 를 눌러 간단하게 모든 컨테이너를 중지할 수 있습니다. 만약 컨테이너를 백그라운드에서 실행되고 있다면 `stop` 명령어를 사용하여 컨테이너를 중지 할 수 있습니다.

```bash
sail stop
```

<a name="executing-sail-commands"></a>
## Executing Commands
## 명령어 실행

When using Laravel Sail, your application is executing within a Docker container and is isolated from your local computer. However, Sail provides a convenient way to run various commands against your application such as arbitrary PHP commands, Artisan commands, Composer commands, and Node / NPM commands.

Laravel Sail을 사용할 때 프로그램은 도커 컨테이너 내에서 실행되며 로컬 컴퓨터내에서 격리되어 돌아갑니다. 그러나 Sail은 임의 PHP 명령, Artisan 명령, Composer 명령 및 Node / NPM 명령과 같은 다양한 명령을 어플리케이션에서 실행할 수 있는 편리한 방법을 제공합니다.

**When reading the Laravel documentation, you will often see references to Composer, Artisan, and Node / NPM commands that do not reference Sail.** Those examples assume that these tools are installed on your local computer. If you are using Sail for your local Laravel development environment, you should execute those commands using Sail:

**Laravel 설명서에는 Sail을 참조하지 않는 Composer, Artisan 및 Node / NPM 명령에 대한 참조가 자주 나타납니다.** 아래의 예에서는 현재 로컬 컴퓨터에 필요한것들이 설치되어 있다고 가정합니다. Sail을 사용하는 로컬 라라벨 개발환경에서는 명령어를 아래와 같이 실행해야 합니다. 

```bash
# Running Artisan commands locally...
php artisan queue:work

# Running Artisan commands within Laravel Sail...
sail artisan queue:work
```

<a name="executing-php-commands"></a>
### Executing PHP Commands
### PHP 명령어 실행

PHP commands may be executed using the `php` command. Of course, these commands will execute using the PHP version that is configured for your application. To learn more about the PHP versions available to Laravel Sail, consult the [PHP version documentation](#sail-php-versions):

PHP 명령은 `php` 명령어를 사용하여 실행할 수 있다. 물론, 어플리케이션을 구성 할 때 설정한 PHP 버전을 기준하여 실행됩니다. Laravel Sail에서 사용할 수 있는 PHP 버전에 대한 자세한 내용은 [PHP 버전 설명서](#sail-php-versions)를 참조하십시오.

```bash
sail php --version

sail php script.php
```

<a name="executing-composer-commands"></a>
### Executing Composer Commands
### Composer 명령어 실행

Composer commands may be executed using the `composer` command. Laravel Sail's application container includes a Composer 2.x installation:

Composer 명령은 `composer` 명령어를 사용하여 실행 할 수 있습니다. 라라벨 Sail의 어플리케이션 컨테이너에는 Composer 2.x 가 설치되어 포함됩니다.

```nothing
sail composer require laravel/sanctum
```

<a name="installing-composer-dependencies-for-existing-projects"></a>
#### Installing Composer Dependencies For Existing Applications
#### 기존 어플리케이션에 Composer 의존성 설치하기

If you are developing an application with a team, you may not be the one that initially creates the Laravel application. Therefore, none of the application's Composer dependencies, including Sail, will be installed after you clone the application's repository to your local computer.

팀과 함께 어플리케이션을 개발하는 경우 Laravel 애플리케이션을 처음 만든 사람이 아닐 수 있습니다. 따라서 어플리케이션의 레포지토리를 로컬 컴퓨터에 복제한 후에는 Sail을 포함하여 어플리케이션의 Composer 의존성이 설치되지 않습니다.

You may install the application's dependencies by navigating to the application's directory and executing the following command. This command uses a small Docker container containing PHP and Composer to install the application's dependencies:

어플리케이션(복사 또는 복제한)의 폴더로 이동하여 다음 명령어를 통해 종속성을 설치 할 수 있습니다. 이 명령은 PHP 와 Composer 가 포함된 작은 도커 컨테이너로 어플리케이션의 종속성을 설치합니다.

```nothing
docker run --rm \
    -u "$(id -u):$(id -g)" \
    -v $(pwd):/var/www/html \
    -w /var/www/html \
    laravelsail/php81-composer:latest \
    composer install --ignore-platform-reqs
```

When using the `laravelsail/phpXX-composer` image, you should use the same version of PHP that you plan to use for your application (`74`, `80`, or `81`).

`laravelsail/phpXX-composer` (XX 부분에는 `74`, `80` 또는 `81` 가 들어갑니다) 이미지를 사용중이라면, 어플리케이션이 개발된 PHP 버전과 동일한 버전을 사용해야 합니다.
<a name="executing-artisan-commands"></a>
### Executing Artisan Commands
### Artisan 명령어 실행

Laravel Artisan commands may be executed using the `artisan` command:

라라벨 Artisan 명령을 `artisan` 명령어로 실행 할 수 있습니다.

```bash
sail artisan queue:work
```

<a name="executing-node-npm-commands"></a>
### Executing Node / NPM Commands
### Node / NPM 명령어 실행

Node commands may be executed using the `node` command while NPM commands may be executed using the `npm` command:

Node 명령은 `node`, NPM 명령은 `npm` 명령어로 실행 할 수 있습니다.

```nothing
sail node --version

sail npm run prod
```

If you wish, you may use Yarn instead of NPM:

NPM 대신 Yarn 을 사용할 수 있습니다.

```nothing
sail yarn
```

<a name="interacting-with-sail-databases"></a>
## Interacting With Databases
## 데이터베이스와 연결

<a name="mysql"></a>
### MySQL
### MySQL

As you may have noticed, your application's `docker-compose.yml` file contains an entry for a MySQL container. This container uses a [Docker volume](https://docs.docker.com/storage/volumes/) so that the data stored in your database is persisted even when stopping and restarting your containers. In addition, when the MySQL container is starting, it will ensure a database exists whose name matches the value of your `DB_DATABASE` environment variable.

어플리케이션의 `docker-compose.yml` 파일에는 MySQL 컨테이너에 대한 항목이 포함되어 있습니다. 이 컨테이너는 [Docker volume](https://docs.docker.com/storage/volumes/)을 사용하여 컨테이너를 중지하고 재시작할 때에도 데이터베이스에 저장된 데이터가 유지됩니다. 또한 MySQL 컨테이너가 시작될 때 이름이 `DB_DATABASE` 환경 변수의 값과 일치하는 데이터베이스가 존재하는지 확인합니다.

Once you have started your containers, you may connect to the MySQL instance within your application by setting your `DB_HOST` environment variable within your application's `.env` file to `mysql`.

컨테이너를 시작한 후에는 어플리케이션의 `.env` 파일 내에 있는 `DB_HOST` 환경 변수를 `mysql`로 설정하여 프로그램 내 MySQL 인스턴스에 연결할 수 있습니다.

To connect to your application's MySQL database from your local machine, you may use a graphical database management application such as [TablePlus](https://tableplus.com). By default, the MySQL database is accessible at `localhost` port 3306.

로컬 컴퓨터에서 [TablePlus](https://tableplus.com)와 같은 GUI 데이터베이스 관리 어플리케이션을 활용하여 데이터베이스에 연결 할 수 있습니다. 기본적으로 MySQL 데이터베이스는 `localhost` 포트 3306에서 접근할 수 있습니다.

<a name="redis"></a>
### Redis
### Redis

Your application's `docker-compose.yml` file also contains an entry for a [Redis](https://redis.io) container. This container uses a [Docker volume](https://docs.docker.com/storage/volumes/) so that the data stored in your Redis data is persisted even when stopping and restarting your containers. Once you have started your containers, you may connect to the Redis instance within your application by setting your `REDIS_HOST` environment variable within your application's `.env` file to `redis`.

어플리케이션의 `docker-docker.yml` 파일에는 [Redis](https://redis.io) 컨테이너에 대한 항목도 포함되어 있습니다. 이 컨테이너는 Redis 데이터에 저장된 데이터가 컨테이너를 중지하고 재시작할 때도 유지되도록 [Docker volume](https://docs.docker.com/storage/volumes/)을 사용합니다. 컨테이너를 시작한 후에는 응용 프로그램의 `.env` 파일에 있는 `REDIS_HOST` 환경 변수를 `REDIS`로 설정하여 응용 프로그램 내의 Redis 인스턴스에 연결할 수 있습니다.

To connect to your application's Redis database from your local machine, you may use a graphical database management application such as [TablePlus](https://tableplus.com). By default, the Redis database is accessible at `localhost` port 6379.

로컬 컴퓨터에서 [TablePlus](https://tableplus.com)와 같은 GUI 데이터베이스 관리 어플리케이션을 활용하여 데이터베이스에 연결 할 수 있습니다. 기본적으로 Redis 는 `localhost` 포트 6379에서 접근할 수 있습니다.

<a name="meilisearch"></a>
### MeiliSearch
### MeiliSearch

If you chose to install the [MeiliSearch](https://www.meilisearch.com) service when installing Sail, your application's `docker-compose.yml` file will contain an entry for this powerful search-engine that is [compatible](https://github.com/meilisearch/meilisearch-laravel-scout) with [Laravel Scout](/docs/{{version}}/scout). Once you have started your containers, you may connect to the MeiliSearch instance within your application by setting your `MEILISEARCH_HOST` environment variable to `http://meilisearch:7700`.

Sail을 설치할 때 [MeiliSearch](https://www.meilisearch.com) 서비스를 설치하도록 선택한 경우 어플리케이션의 `docker-such.yml` 파일에 [Laravel Scout](/docs/{version}/scout)이 있는 이 강력한 검색 엔진에 대한 항목이 포함됩니다. 컨테이너를 시작한 후에는 `MEILISEARCH_HOST` 환경 변수를 `http://meilisearch:7700`으로 설정하여 어플리케이션 내의 MeiliSearch 인스턴스에 연결할 수 있습니다.

From your local machine, you may access MeiliSearch's web based administration panel by navigating to `http://localhost:7700` in your web browser.

로컬 컴퓨터에서 `http://localhost:7700` 으로 MeiliSearch 의 웹 기반 관리패널을 접속 할 수 있습니다.

<a name="file-storage"></a>
## File Storage
## 파일 저장소

If you plan to use Amazon S3 to store files while running your application in its production environment, you may wish to install the [MinIO](https://min.io) service when installing Sail. MinIO provides an S3 compatible API that you may use to develop locally using Laravel's `s3` file storage driver without creating "test" storage buckets in your production S3 environment. If you choose to install MinIO while installing Sail, a MinIO configuration section will be added to your application's `docker-compose.yml` file.

Amazon S3를 사용하여 애플리케이션을 운영 환경에서 실행하면서 파일을 저장하려는 경우 Sail 설치 시 [MinIO](https://min.io) 서비스를 설치할 수 있습니다. MinIO는 프로덕션 S3 환경에서 "test" 저장소 버킷을 만들지 않고 Laravel의 `s3` 파일 저장소 드라이버를 사용하여 로컬로 개발하는 데 사용할 수 있는 S3 호환 API를 제공합니다. Sail을 설치하는 동안 MinIO를 설치하도록 선택하면 응용 프로그램의 `docker-compose.yml` 파일에 MinIO 구성 섹션이 추가됩니다.

By default, your application's `filesystems` configuration file already contains a disk configuration for the `s3` disk. In addition to using this disk to interact with Amazon S3, you may use it to interact with any S3 compatible file storage service such as MinIO by simply modifying the associated environment variables that control its configuration. For example, when using MinIO, your filesystem environment variable configuration should be defined as follows:

기본적으로 어플리케이션의 `filesystems` 구성 파일에는 `s3` 디스크에 대한 디스크 구성이 이미 포함되어 있습니다. 이 디스크를 사용하여 Amazon S3와 상호 작용할 뿐만 아니라 구성을 제어하는 관련 환경 변수를 수정하여 MinIO와 같은 S3 호환 파일 스토리지 서비스와 상호 작용할 수도 있습니다. 예를 들어 MinIO를 사용할 때 파일 시스템 환경 변수 구성은 다음과 같이 정의되어야 합니다.

```ini
FILESYSTEM_DRIVER=s3
AWS_ACCESS_KEY_ID=sail
AWS_SECRET_ACCESS_KEY=password
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=local
AWS_ENDPOINT=http://minio:9000
AWS_USE_PATH_STYLE_ENDPOINT=true
```

<a name="running-tests"></a>
## Running Tests
## 테스팅 하기

Laravel provides amazing testing support out of the box, and you may use Sail's `test` command to run your applications [feature and unit tests](/docs/{{version}}/testing). Any CLI options that are accepted by PHPUnit may also be passed to the `test` command:

Laravel은 준수한 테스트를 제공하며, Sail의 `test` 명령을 사용하여 애플리케이션[기능 및 단위 테스트](/docs/{version}/테스트)을 실행할 수 있습니다. PHPUnit 에서 허용하는 모든 CLI 옵션은 `test` 명령에도 전달될 수 있습니다.


    sail test

    sail test --group orders

The Sail `test` command is equivalent to running the `test` Artisan command:

Sail 의 `test` 명령은 Artisan 의 `test` 명령을 실행하는것과 같습니다.

    sail artisan test

<a name="laravel-dusk"></a>
### Laravel Dusk
### 라라벨 Dusk

[Laravel Dusk](/docs/{{version}}/dusk) provides an expressive, easy-to-use browser automation and testing API. Thanks to Sail, you may run these tests without ever installing Selenium or other tools on your local computer. To get started, uncomment the Selenium service in your application's `docker-compose.yml` file:

[Laravel Dusk](/docs/{version}/dusk)는 기능이 많고, 사용하기 쉬운 브라우저 자동화 및 테스팅 API를 제공합니다. Sail 덕분에 로컬 컴퓨터에 Selenium과 같은 다른 도구를 설치하지 않고도 테스트를 실행할 수 있습니다. 시작하려면 어플리케이션의 `docker-compose.yml` 파일에서 Selenium 서비스의 주석 처리를 해제하십시오.

```yaml
selenium:
    image: 'selenium/standalone-chrome'
    volumes:
        - '/dev/shm:/dev/shm'
    networks:
        - sail
```

Next, ensure that the `laravel.test` service in your application's `docker-compose.yml` file has a `depends_on` entry for `selenium`:

다음으로 어플리케이션의 `docker-compose.yml` 파일에 있는 `laravel.test` 서비스에서 `selenium`에 대한 `depends_on` 항목이 있는지 확인합니다.

```yaml
depends_on:
    - mysql
    - redis
    - selenium
```

Finally, you may run your Dusk test suite by starting Sail and running the `dusk` command:

마지막으로 `dusk` 명령을 실행하면 sail을 시작하면서 Dusk 테스트를 실행할 수 있습니다.

    sail dusk

<a name="selenium-on-apple-silicon"></a>
#### Selenium On Apple Silicon
#### Apple Silicon 에서 Seleniumn 설정

If your local machine contains an Apple Silicon chip, your `selenium` service must use the `seleniarm/standalone-chromium` image:

로컬 컴퓨터가 Apple Silicon 칩 이라면, `selenium` 서비스는 `seleniarm/standalone-chromium` 이미지를 사용해야됩니다.

```yaml
selenium:
    image: 'seleniarm/standalone-chromium'
    volumes:
        - '/dev/shm:/dev/shm'
    networks:
        - sail
```

<a name="previewing-emails"></a>
## Previewing Emails
## 이메일 미리보기

Laravel Sail's default `docker-compose.yml` file contains a service entry for [MailHog](https://github.com/mailhog/MailHog). MailHog intercepts emails sent by your application during local development and provides a convenient web interface so that you can preview your email messages in your browser. When using Sail, MailHog's default host is `mailhog` and is available via port 1025:

Laravel Sail의 기본 `docker-docker.yml` 파일에는 [MailHog](https://github.com/mailhog/MailHog)의 서비스 항목이 포함되어 있습니다. MailHog는 로컬 개발 중에 보낸 전자 메일을 가로채서 브라우저에서 전자 메일 메시지를 미리 볼 수 있도록 편리한 웹 인터페이스를 제공합니다. Sail을 사용할 때 MailHog의 기본 호스트는 `mailhog`이며 포트 1025를 통해 사용할 수 있습니다.

```bash
MAIL_HOST=mailhog
MAIL_PORT=1025
MAIL_ENCRYPTION=null
```

When Sail is running, you may access the MailHog web interface at: http://localhost:8025

Sail 이 실행중 일때 http://localhost:8025 을 통해 MailHog 웹 인터페이스에 접근 할 수 있습니다.

<a name="sail-container-cli"></a>
## Container CLI
## 컨테이너 CLI

Sometimes you may wish to start a Bash session within your application's container. You may use the `shell` command to connect to your application's container, allowing you to inspect its files and installed services as well execute arbitrary shell commands within the container:

어플리케이션의 컨테이너 내에서 Bash 세션을 시작할 수 있습니다. `shell` 명령을 사용하여 어플리케이션의 컨테이너에 연결할 수 있으므로 해당 파일 및 설치된 서비스를 검사하고 컨테이너 내에서 임의 셸 명령을 실행할 수 있습니다.

```nothing
sail shell

sail root-shell
```

To start a new [Laravel Tinker](https://github.com/laravel/tinker) session, you may execute the `tinker` command:

`tinker` 명령어를 통해 [Laravel Tinker](https://github.com/laravel/tinker) 세션을 시작 할 수 있습니다.

```bash
sail tinker
```

<a name="sail-php-versions"></a>
## PHP Versions
## PHP 버전

Sail currently supports serving your application via PHP 8.1, PHP 8.0, or PHP 7.4. The default PHP version used by Sail is currently PHP 8.1. To change the PHP version that is used to serve your application, you should update the `build` definition of the `laravel.test` container in your application's `docker-compose.yml` file:

Sail은 현재 PHP 8.1, PHP 8.0 또는 PHP 7.4을 지원합니다. Sail에서 사용하는 기본 PHP 버전은 현재 PHP 8.1 입니다. 응용 프로그램 서비스에 사용되는 PHP 버전을 변경하려면 어플리케이션의 `docker-compose.yml` 파일에서 `laravel.test` 컨테이너의 `build` 정의를 업데이트해야 합니다.

```yaml
# PHP 8.1
context: ./vendor/laravel/sail/runtimes/8.1

# PHP 8.0
context: ./vendor/laravel/sail/runtimes/8.0

# PHP 7.4
context: ./vendor/laravel/sail/runtimes/7.4
```

In addition, you may wish to update your `image` name to reflect the version of PHP being used by your application. This option is also defined in your application's `docker-compose.yml` file:

또한 어플리케이션에서 사용 중인 PHP 버전을 반영하도록 `image` 이름을 업데이트할 수 있습니다. 이 옵션은 어플리케이션의 `docker-compose.yml` 파일에도 정의되어 있습니다.

```yaml
image: sail-8.1/app
```

After updating your application's `docker-compose.yml` file, you should rebuild your container images:

어플리케이션의 `docker-compose.yml` 파일을 업데이트한 후 컨테이너 이미지를 다시 빌드해야 합니다.

    sail build --no-cache

    sail up

<a name="sail-node-versions"></a>
## Node Versions
## Node 버전

Sail installs Node 16 by default. To change the Node version that is installed when building your images, you may update the `build.args` definition of the `laravel.test` service in your application's `docker-compose.yml` file:

Sail은 기본적으로 Node 16을 설치합니다. 이미지가 빌드될 때 설치된 Node 버전을 변경하려면 어플리케이션의 `docker-compose.yml` 파일에서 `laravel.test` 서비스의 `build.args` 정의를 업데이트하십시오.

```yaml
build:
    args:
        WWWGROUP: '${WWWGROUP}'
        NODE_VERSION: '14'
```

After updating your application's `docker-compose.yml` file, you should rebuild your container images:

어플리케이션의 `docker-compose.yml` 파일을 업데이트한 후 컨테이너 이미지를 다시 빌드해야 합니다.

    sail build --no-cache

    sail up

<a name="sharing-your-site"></a>
## Sharing Your Site
## 사이트 공유하기

Sometimes you may need to share your site publicly in order to preview your site for a colleague or to test webhook integrations with your application. To share your site, you may use the `share` command. After executing this command, you will be issued a random `laravel-sail.site` URL that you may use to access your application:

동료의 사이트를 미리 보거나 어플리케이션과의 웹훅 통합을 테스트하기 위해 사이트를 공개적으로 공유해야 할 수도 있습니다. 사이트를 공유하려면 `share` 명령을 사용하십시오. 이 명령을 실행한 후 사이트에 접속 할 수있는 임의의 `laravel-sail.site` URL 을 받게 됩니다.

    sail share

When sharing your site via the `share` command, you should configure your application's trusted proxies within the `TrustProxies` middleware. Otherwise, URL generation helpers such as `url` and `route` will be unable to determine the correct HTTP host that should be used during URL generation:

`share` 명령을 통해 사이트를 공유할 때는 `trustProxies` 미들웨어 내에서 어플리케이션의 신뢰할 수 있는 프록시를 구성해야 합니다. 그렇지 않으면 `url` 및 `route`와 같은 URL 생성 도우미가 URL 생성 중에 사용해야 하는 올바른 HTTP 호스트를 확인할 수 없습니다.

    /**
     * The trusted proxies for this application.
     *
     * @var array|string|null
     */
    protected $proxies = '*';

If you would like to choose the subdomain for your shared site, you may provide the `subdomain` option when executing the `share` command:

공유 될 사이트의 서브 도메인을 선택하려면 `share` 명령을 실행할 때 `subdomain` 옵션을 사용할 수 있습니다.

    sail share --subdomain=my-sail-site

> {tip} The `share` command is powered by [Expose](https://github.com/beyondcode/expose), an open source tunneling service by [BeyondCode](https://beyondco.de).

> {tip} `share` 명령어는 오픈소스 터널링 서비스인 [BeyondCode](https://beyondco.de)의 [Expose](https://github.com/beyondcode/expose) 에서 제공됩니다.

<a name="debugging-with-xdebug"></a>
## Debugging With Xdebug
## Xdebug 로 디버깅

Laravel Sail's Docker configuration includes support for [Xdebug](https://xdebug.org/), a popular and powerful debugger for PHP. In order to enable Xdebug, you will need to add a few variables to your application's `.env` file to [configure Xdebug](https://xdebug.org/docs/step_debug#mode). To enable Xdebug you must set the appropriate mode(s) before starting Sail:

Laravel Sail의 도커에는 인기 있고 강력한 PHP 디버거인 [Xdebug](https://xdebug.org/)을 지원합니다. Xdebug를 활성화하려면 응용 프로그램의 `.env` 파일에 [Xdebug 구성](https://xdebug.org/docs/step_debug#mode)에 몇 가지 변수를 추가해야 합니다. Xdebug를 활성화하려면 Sail을 시작하기 전에 적절한 모드를 설정해야 합니다

```ini
SAIL_XDEBUG_MODE=develop,debug
```

#### Linux Host IP Configuration
#### Linux 호스트 IP 설정

Internally, the `XDEBUG_CONFIG` environment variable is defined as `client_host=host.docker.internal` so that Xdebug will be properly configured for Mac and Windows (WSL2). If your local machine is running Linux, you will need to manually define this environment variable.

내부적으로 `XDEBUG_CONFIG` 환경 변수는 `client_host=host.docker.internal`로 정의되므로 XDEBUG가 Mac 및 Windows(WSL2) 용으로만 제대로 동작합니다. 로컬 개발환경이 Linux라면 이 환경 변수를 수동으로 정의해야 합니다.

First, you should determine the correct host IP address to add to the environment variable by running the following command. Typically, the `<container-name>` should be the name of the container that serves your application and often ends with `_laravel.test_1`:

먼저 다음 명령을 실행하여 환경 변수에 추가할 올바른 호스트 IP 주소를 결정해야 합니다. 일반적으로 `<container-name>`은 어플리케이션을 지원하는 컨테이너 이름이어야 하며 보통 `_laravel.test_1`로 끝납니다.

```bash
docker inspect -f {{range.NetworkSettings.Networks}}{{.Gateway}}{{end}} <container-name>
```

Once you have obtained the correct host IP address, you should define the `SAIL_XDEBUG_CONFIG` variable within your application's `.env` file:

올바른 호스트 IP 주소를 얻었으면 응용 프로그램의 `.env` 파일 내에 `SAIL_XDEBUG_CONFIG` 변수를 정의해야 합니다.

```ini
SAIL_XDEBUG_CONFIG="client_host=<host-ip-address>"
```

<a name="xdebug-cli-usage"></a>
### Xdebug CLI Usage
### Xdebug CLI 사용방법

A `sail debug` command may be used to start a debugging session when running an Artisan command:

`sail debug` 명령어로 Artisan 명령어가 실행될 때 디버깅 세션을 시작 할 수 있습니다.

```bash
# Run an Artisan command without Xdebug...
sail artisan migrate

# Run an Artisan command with Xdebug...
sail debug migrate
```

<a name="xdebug-browser-usage"></a>
### Xdebug Browser Usage
### Xdebug 브라우저 사용방법

To debug your application while interacting with the application via a web browser, follow the [instructions provided by Xdebug](https://xdebug.org/docs/step_debug#web-application) for initiating an Xdebug session from the web browser.

웹 브라우저를 통해 어플리케이션을 디버깅하려면 웹 브라우저에서 Xdebug 세션을 시작하기 위해 [Xdebug에 의해 제공되는 변경사항](https://xdebug.org/docs/step_debug#web-application)을 따르십시오.

If you're using PhpStorm, please review JetBrain's documentation regarding [zero-configuration debugging](https://www.jetbrains.com/help/phpstorm/zero-configuration-debugging.html).

PhpStorm 을 사용하고 있다면, JetBrain의 [zero-configuration debugging](https://www.jetbrains.com/help/phpstorm/zero-configuration-debugging.html) 문서를 확인하십시오.

> {note} Laravel Sail relies on `artisan serve` to serve your application. The `artisan serve` command only accepts the `XDEBUG_CONFIG` and `XDEBUG_MODE` variables as of Laravel version 8.53.0. Older versions of Laravel (8.52.0 and below) do not support these variables and will not accept debug connections.

> {note} Laravel Sail에서 Xdebug 실행은 `artisan serve`에 의존합니다. `artisan serve` 명령에서 `XDEBUG_CONFIG` 와 `XDEBUG_MODE` 변수만 라라벨 8.53.0 이상에서 사용할 수 있습니다. 이전 버전의 Laravel(8.52.0 이하)에서는 이러한 변수를 지원하지 않으며 디버그 연결을 허용하지 않습니다.

<a name="sail-customization"></a>
## Customization
## 사용자정의

Since Sail is just Docker, you are free to customize nearly everything about it. To publish Sail's own Dockerfiles, you may execute the `sail:publish` command:

Sail은 도커일 뿐이므로 대부분의 모든 것을 자유롭게 사용자가 변경할 수 있습니다. Sail의 도커 파일을 프로젝트에 만드려면 `sail:publish` 명령을 실행하십시오

역자주: 운영 서버 배포를 위해서 팀에서 배포용 이미지를 docker 폴더를 따로 두었거나, 로컬 환경 공유를 위해서 존재 할 수도 있습니다. `publish` 명령어를 치기 전에 현재 프로젝트를 한번 확인해주시는걸 추천드립니다.

```bash
sail artisan sail:publish
```

After running this command, the Dockerfiles and other configuration files used by Laravel Sail will be placed within a `docker` directory in your application's root directory. After customizing your Sail installation, you may wish to change the image name for the application container in your application's `docker-compose.yml` file. After doing so, rebuild your application's containers using the `build` command. Assigning a unique name to the application image is particularly important if you are using Sail to develop multiple Laravel applications on a single machine:

이 명령을 실행하면 Laravel Sail에서 사용하는 Docker 파일과 기타 구성 파일이 어플리케이션의 루트 디렉터리에 있는 `docker` 디렉터리에 배치됩니다. Sail 설치를 사용자 정의한 후 어플리케이션의 `docker-compose.yml` 파일에서 어플리케이션 컨테이너의 이미지 이름을 변경할 수 있습니다. 그런 다음 `build` 명령을 사용하여 어플리케이션의 컨테이너를 다시 만드십시오. Sail을 사용하여 단일 컴퓨터에서 여러 Laravel 어플리케이션을 개발하는 경우 어플리케이션 이미지에 고유 이름을 지정하는 것이 특히 중요합니다

```bash
sail build --no-cache
```
