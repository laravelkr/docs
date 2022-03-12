# Laravel Homestead
# 라라벨 홈스테드

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Installation & Setup](#installation-and-setup)
- [인스톨 & 설정](#installation-and-setup)
  - [First Steps](#first-steps)
  - [첫번째 단계](#first-steps)
  - [Configuring Homestead](#configuring-homestead)
  - [홈스테드 설정](#configuring-homestead)
  - [Configuring Nginx Sites](#configuring-nginx-sites)
  - [Nginx 사이트 구성](#configuring-nginx-sites)
  - [Configuring Services](#configuring-services)
  - [서비스 구성](#configuring-services)
  - [Launching The Vagrant Box](#launching-the-vagrant-box)
  - [Vagrant Box 구동하기](#launching-the-vagrant-box)
  - [Per Project Installation](#per-project-installation)
  - [프로젝트별 설치하기](#per-project-installation)
  - [Installing Optional Features](#installing-optional-features)
  - [선택적 기능 설치](#installing-optional-features)
  - [Aliases](#aliases)
  - [별칭 설정](#aliases)
- [Updating Homestead](#updating-homestead)
- [홈스테드 업데이트](#updating-homestead)
- [Daily Usage](#daily-usage)
- [사용 방법](#daily-usage)
  - [Connecting Via SSH](#connecting-via-ssh)
  - [SSH를 통해 연결](#connecting-via-ssh)
  - [Adding Additional Sites](#adding-additional-sites)
  - [사이트 추가](#adding-additional-sites)
  - [Environment Variables](#environment-variables)
  - [환경 변수](#environment-variables)
  - [Ports](#ports)
  - [Ports](#ports)
  - [PHP Versions](#php-versions)
  - [PHP 버전](#php-versions)
  - [Connecting To Databases](#connecting-to-databases)
  - [데이터베이스에 연결](#connecting-to-databases)
  - [Database Backups](#database-backups)
  - [데이터베이스 백업](#database-backups)
  - [Configuring Cron Schedules](#configuring-cron-schedules)
  - [주기적인 Cron 설정](#configuring-cron-schedules)
  - [Configuring MailHog](#configuring-mailhog)
  - [MailHog 설정](#configuring-mailhog)
  - [Configuring Minio](#configuring-minio)
  - [Minio 설정](#configuring-minio)
  - [Laravel Dusk](#laravel-dusk)
  - [Laravel Dusk](#laravel-dusk)
  - [Sharing Your Environment](#sharing-your-environment)
  - [환경 공유하기](#sharing-your-environment)
- [Debugging & Profiling](#debugging-and-profiling)
- [디버깅과 프로파일링](#debugging-and-profiling)
  - [Debugging Web Requests With Xdebug](#debugging-web-requests)
  - [Xdebug로 웹 요청 디버깅](#debugging-web-requests)
  - [Debugging CLI Applications](#debugging-cli-applications)
  - [CLI 애플리케이션 디버깅](#debugging-cli-applications)
  - [Profiling Applications with Blackfire](#profiling-applications-with-blackfire)
  - [Blackfire로 애플리케이션 프로파일링](#profiling-applications-with-blackfire)
- [Network Interfaces](#network-interfaces)
- [네트워크 인터페이스](#network-interfaces)
- [Extending Homestead](#extending-homestead)
- [홈스테드 확장](#extending-homestead)
- [Provider Specific Settings](#provider-specific-settings)
- [공급자별 설정](#provider-specific-settings)
  - [VirtualBox](#provider-specific-virtualbox)
  - [VirtualBox](#provider-specific-virtualbox)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel strives to make the entire PHP development experience delightful, including your local development environment. [Laravel Homestead](https://github.com/laravel/homestead) is an official, pre-packaged Vagrant box that provides you a wonderful development environment without requiring you to install PHP, a web server, and any other server software on your local machine.

라라벨은 로컬 개발 환경을 포함하여 전체 PHP 개발 경험을 즐겁게 만들기 위해 노력합니다. [Laravel Homestead](https:github.comlaravelhomestead)는 PHP, 웹 서버 및 기타 서버 소프트웨어를 로컬 컴퓨터에 설치할 필요 없이 멋진 개발 환경을 제공하는 사전 패키지된 공식 Vagrant 상자입니다.

[Vagrant](https://www.vagrantup.com) provides a simple, elegant way to manage and provision Virtual Machines. Vagrant boxes are completely disposable. If something goes wrong, you can destroy and re-create the box in minutes!

[Vagrant](https:www.vagrantup.com)는 가상 머신을 관리하고 프로비저닝하는 간단하고 우아한 방법을 제공합니다. Vagrant 상자는 완전히 일회용입니다. 문제가 발생하면 몇 분 안에 상자를 파괴하고 다시 만들 수 있습니다!

Homestead runs on any Windows, macOS, or Linux system and includes Nginx, PHP, MySQL, PostgreSQL, Redis, Memcached, Node, and all of the other software you need to develop amazing Laravel applications.

홈스테드는 Windows, macOS 또는 Linux 시스템에서 실행되며 Nginx, PHP, MySQL, PostgreSQL, Redis, Memcached, Node 및 놀라운 라라벨 애플리케이션을 개발하는 데 필요한 기타 모든 소프트웨어를 포함합니다.

> {note} If you are using Windows, you may need to enable hardware virtualization (VT-x). It can usually be enabled via your BIOS. If you are using Hyper-V on a UEFI system you may additionally need to disable Hyper-V in order to access VT-x.

> {참고} Windows를 사용하는 경우 하드웨어 가상화(VT-x)를 활성화해야 할 수 있습니다. 일반적으로 BIOS를 통해 활성화할 수 있습니다. UEFI 시스템에서 Hyper-V를 사용하는 경우 VT-x에 액세스하기 위해 Hyper-V를 비활성화해야 할 수도 있습니다.

<a name="included-software"></a>
### Included Software
### 포함된 소프트웨어

- Ubuntu 20.04
- Git
- PHP 8.1
- PHP 8.0
- PHP 7.4
- PHP 7.3
- PHP 7.2
- PHP 7.1
- PHP 7.0
- PHP 5.6
- Nginx
- MySQL 8.0
- lmm
- Sqlite3
- PostgreSQL 13
- Composer
- Node (With Yarn, Bower, Grunt, and Gulp)
- Redis
- Memcached
- Beanstalkd
- Mailhog
- avahi
- ngrok
- Xdebug
- XHProf / Tideways / XHGui
- wp-cli

<a name="optional-software"></a>
### Optional Software
### 옵션 소프트웨어

- Apache
- Blackfire
- Cassandra
- Chronograf
- CouchDB
- Crystal & Lucky Framework
- Docker
- Elasticsearch
- EventStoreDB
- Gearman
- Go
- Grafana
- InfluxDB
- MariaDB
- Meilisearch
- MinIO
- MongoDB
- Neo4j
- Oh My Zsh
- Open Resty
- PM2
- Python
- R
- RabbitMQ
- RVM (Ruby Version Manager)
- Solr
- TimescaleDB
- Trader <small>(PHP extension)</small>
- Webdriver & Laravel Dusk Utilities

<a name="installation-and-setup"></a>
## Installation & Setup
## 설치 & 구성하기

<a name="first-steps"></a>
### First Steps
### 첫번째 단계

Before launching your Homestead environment, you must install [Vagrant](https://www.vagrantup.com/downloads.html) as well as one of the following supported providers:

홈스테드 환경을 시작하기 전에 [Vagrant](https://www.vagrantup.com/downloads.html) 와 다음의 지원하는 제공자 중 하나를 설치해야 합니다.

- [VirtualBox 6.1.x](https://www.virtualbox.org/wiki/Downloads)
- [Parallels](https://www.parallels.com/products/desktop/)

All of these software packages provide easy-to-use visual installers for all popular operating systems.

이런 모든 소프트웨어 패키지는 널리 사용되는 모든 운영 체제에 사용하기 쉬운 시각적인 설치 프로그램을 제공합니다.

To use the Parallels provider, you will need to install [Parallels Vagrant plug-in](https://github.com/Parallels/vagrant-parallels). It is free of charge.

Parallels 프로바이더를 사용하려면, [Parallels Vagrant 플러그인](https://github.com/Parallels/vagrant-parallels) 을 설치해야 합니다. 이 플러그인은 무료입니다.

<a name="installing-homestead"></a>
#### Installing Homestead
#### 홈스테드 설치하기

You may install Homestead by cloning the Homestead repository onto your host machine. Consider cloning the repository into a `Homestead` folder within your "home" directory, as the Homestead virtual machine will serve as the host to all of your Laravel applications. Throughout this documentation, we will refer to this directory as your "Homestead directory":

홈스테드 저장소를 호스트 머신에 복제하여 홈스테드를 설치할 수 있습니다. 홈스테드 가상 머신이 모든 라라벨 애플리케이션의 호스트 역할을 할 것이기 때문에 저장소를 "home" 디렉토리 내의 `Homestead` 폴더에 복사하는 것을 고려하십시오. 이 문서 전체에서 우리는 이 디렉토리를 "홈스테드 디렉토리"라고 부를 것입니다.

```bash
git clone https://github.com/laravel/homestead.git ~/Homestead
```

After cloning the Laravel Homestead repository, you should checkout the `release` branch. This branch always contains the latest stable release of Homestead:

라라벨 홈스테드 저장소를 복제한 후 `release` 브랜치를 체크아웃해야 합니다. 이 브랜치는 항상 Homestead의 최신 안정 릴리스를 포함합니다.

    cd ~/Homestead

    git checkout release

Next, execute the `bash init.sh` command from the Homestead directory to create the `Homestead.yaml` configuration file. The `Homestead.yaml` file is where you will configure all of the settings for your Homestead installation. This file will be placed in the Homestead directory:

다음으로 Homestead 디렉토리에서 `bash init.sh` 명령을 실행하여 `Homestead.yaml` 설정 파일을 생성합니다. `Homestead.yaml` 파일은 홈스테드 설치를 위한 모든 설정을 관리하는 곳입니다. 이 파일은 Homestead 디렉토리에 저장됩니다.

    // macOS / Linux...
    bash init.sh

    // Windows...
    init.bat

<a name="configuring-homestead"></a>
### Configuring Homestead
### 홈스테드 설정하기

<a name="setting-your-provider"></a>
#### Setting Your Provider
#### 프로바이더 설정하기

The `provider` key in your `Homestead.yaml` file indicates which Vagrant provider should be used: `virtualbox` or `parallels`:

`Homestead.yaml` 파일의 `provider` 키는 `virtualbox` 또는 `parallels` 중 어떤 Vagrant 제공자를 사용해야 하는지 나타냅니다.

    provider: virtualbox

> {note} If you are using Apple Silicon, you should add `box: laravel/homestead-arm` to your `Homestead.yaml` file. Apple Silicon requires the Parallels provider.

> {note} Apple Silicon을 사용하는 경우 `Homestead.yaml` 파일에 `box: laravel/homestead-arm`을 추가해야 합니다. Apple Silicon에는 Parallels 공급자가 필요합니다.

<a name="configuring-shared-folders"></a>
#### Configuring Shared Folders
#### 공유폴더 설정하기

The `folders` property of the `Homestead.yaml` file lists all of the folders you wish to share with your Homestead environment. As files within these folders are changed, they will be kept in sync between your local machine and the Homestead virtual environment. You may configure as many shared folders as necessary:

`Homestead.yaml` 파일의 `folders` 속성은 홈스테드 환경과 공유하고자 하는 모든 폴더가 나열되어 있습니다. 이 폴더 내의 파일이 변경되면 로컬 머신과 홈스테드 가상 환경 간에 동기화된 상태로 유지됩니다. 필요한 만큼 공유 폴더를 구성할 수 있습니다.

```yaml
folders:
    - map: ~/code/project1
      to: /home/vagrant/project1
```

> {note} Windows users should not use the `~/` path syntax and instead should use the full path to their project, such as `C:\Users\user\Code\project1`.

> {note} Windows 사용자는 `~/`경로 구문을 사용하지 말고 대신 `C:\Users\user\Code\project1`와 같은 프로젝트의 전체 경로를 사용해야 합니다.

You should always map individual applications to their own folder mapping instead of mapping a single large directory that contains all of your applications. When you map a folder, the virtual machine must keep track of all disk IO for *every* file in the folder. You may experience reduced performance if you have a large number of files in a folder:

모든 애플리케이션이 포함된 하나의 큰 디렉터리를 매핑하는 대신 항상 개별 애플리케이션을 자체 폴더 매핑에 매핑해야 합니다. 폴더를 매핑할 때 가상 머신은 폴더의 모든 파일에 대한 모든 디스크 IO를 추적합니다. 폴더에 많은 수의 파일이 있는 경우 성능이 저하될 수 있습니다.

```yaml
folders:
    - map: ~/code/project1
      to: /home/vagrant/project1
    - map: ~/code/project2
      to: /home/vagrant/project2
```

> {note} You should never mount `.` (the current directory) when using Homestead. This causes Vagrant to not map the current folder to `/vagrant` and will break optional features and cause unexpected results while provisioning.

> {note} 홈스테드를 사용할 때는 `.` (현재 디렉토리)를 마운트하지 마십시오. 이로 인해 Vagrant는 현재 폴더를 `/vagrant`로 매핑하지 않으며 옵션 기능을 중단하고 프로비저닝하는 동안 예상치 못한 결과를 초래합니다.

To enable [NFS](https://www.vagrantup.com/docs/synced-folders/nfs.html), you may add a `type` option to your folder mapping:

[NFS](https://www.vagrantup.com/docs/synced-folders/nfs.html) 를 활성화하려면 폴더 매핑에 `type` 옵션을 추가할 수 있습니다.

    folders:
        - map: ~/code/project1
          to: /home/vagrant/project1
          type: "nfs"

> {note} When using NFS on Windows, you should consider installing the [vagrant-winnfsd](https://github.com/winnfsd/vagrant-winnfsd) plug-in. This plug-in will maintain the correct user / group permissions for files and directories within the Homestead virtual machine.

> {note} 윈도우에서 NFS를 사용할 때에는, [vagrant-winnfsd](https://github.com/winnfsd/vagrant-winnfsd) 플러그인을 설치해야만 합니다. 이 플러그인은 홈스테드 가상머신안에서 파일과 디렉토리를 위한 올바른 사용자와 권한을 관리해줍니다.

You may also pass any options supported by Vagrant's [Synced Folders](https://www.vagrantup.com/docs/synced-folders/basic_usage.html) by listing them under the `options` key:

Vagrant의 [Synced Folders](https:www.vagrantup.comdocssynced-foldersbasic_usage.html)에서 지원하는 모든 옵션을 `options` 키 아래에 나열하여 전달할 수도 있습니다.

    folders:
        - map: ~/code/project1
          to: /home/vagrant/project1
          type: "rsync"
          options:
              rsync__args: ["--verbose", "--archive", "--delete", "-zz"]
              rsync__exclude: ["node_modules"]

<a name="configuring-nginx-sites"></a>
### Configuring Nginx Sites
### Nginx 사이트 구성

Not familiar with Nginx? No problem. Your `Homestead.yaml` file's `sites` property allows you to easily map a "domain" to a folder on your Homestead environment. A sample site configuration is included in the `Homestead.yaml` file. Again, you may add as many sites to your Homestead environment as necessary. Homestead can serve as a convenient, virtualized environment for every Laravel application you are working on:

Nginx에 대해 잘 모르십니까? 걱정하지마세요. `Homestead.yaml` 파일의 `sites` 속성을 사용하면 "도메인"을 홈스테드 환경의 폴더에 쉽게 매핑할 수 있습니다. 샘플 사이트 구성은 `Homestead.yaml` 파일에 포함되어 있습니다. 다시 말하지만, 필요한 만큼 홈스테드 환경에 사이트를 추가할 수 있습니다. 홈스테드는 작업 중인 모든 라라벨 애플리케이션에 대한 편리한 가상화 환경의 역할을 할 수 있습니다.

    sites:
        - map: homestead.test
          to: /home/vagrant/project1/public

If you change the `sites` property after provisioning the Homestead virtual machine, you should execute the `vagrant reload --provision` command in your terminal to update the Nginx configuration on the virtual machine.

홈스테드 가상 머신을 이미 생성한 후에 `sites` 속성을 변경하는 경우, 터미널에서 `vagrant reload --provision` 명령을 실행하여 가상 머신의 Nginx 구성을 업데이트해야 합니다.

> {note} Homestead scripts are built to be as idempotent as possible. However, if you are experiencing issues while provisioning you should destroy and rebuild the machine by executing the `vagrant destroy && vagrant up` command.

> {note} 홈스테드 스크립트는 가능한 한 멱등성을 유지하도록 제작되었습니다. 그러나 프로비저닝하는 동안 문제가 발생하면 `vagrant destroy && vagrant up` 명령을 실행하여 시스템을 제거하고 다시 빌드해야 합니다.

<a name="hostname-resolution"></a>
#### Hostname Resolution
#### 호스트 이름 확인

Homestead publishes hostnames using `mDNS` for automatic host resolution. If you set `hostname: homestead` in your `Homestead.yaml` file, the host will be available at `homestead.local`. macOS, iOS, and Linux desktop distributions include `mDNS` support by default. If you are using Windows, you must install [Bonjour Print Services for Windows](https://support.apple.com/kb/DL999?viewlocale=en_US&locale=en_US).

홈스테드는 자동 호스트 확인을 위해 `mDNS`를 사용하여 호스트 이름을 게시합니다. `Homestead.yaml` 파일에서 `hostname: homestead`를 설정하면 호스트는 `homestead.local`에서 사용할 수 있습니다. macOS, iOS 및 Linux 데스크톱 배포에는 기본적으로 `mDNS` 지원이 포함됩니다. Windows를 사용하는 경우 [Bonjour Print Services for Windows](https://support.apple.com/kb/DL999?viewlocale=en_US&locale=en_US) 를 설치해야 합니다.

Using automatic hostnames works best for [per project installations](#per-project-installation) of Homestead. If you host multiple sites on a single Homestead instance, you may add the "domains" for your web sites to the `hosts` file on your machine. The `hosts` file will redirect requests for your Homestead sites into your Homestead virtual machine. On macOS and Linux, this file is located at `/etc/hosts`. On Windows, it is located at `C:\Windows\System32\drivers\etc\hosts`. The lines you add to this file will look like the following:

자동 호스트 이름을 사용하는 것은 Homestead의 [프로젝트별 설치](#per-project-installation) 에 가장 적합합니다. 단일 홈스테드 인스턴스에서 여러 사이트를 호스팅하는 경우 웹 사이트의 "도메인"을 컴퓨터의 `hosts` 파일에 추가할 수 있습니다. `hosts` 파일은 홈스테드 사이트에 대한 요청을 홈스테드 가상 머신으로 리디렉션합니다. macOS 및 Linux에서 이 파일은 `/etc/hosts`에 있습니다. Windows에서는 `C:\Windows\System32\drivers\etc\hosts`에 있습니다. 이 파일에 추가하는 행은 다음과 같습니다.

    192.168.56.56  homestead.test

Make sure the IP address listed is the one set in your `Homestead.yaml` file. Once you have added the domain to your `hosts` file and launched the Vagrant box you will be able to access the site via your web browser:

나열된 IP 주소가 `Homestead.yaml` 파일에 설정된 주소인지 확인하십시오. `hosts` 파일에 도메인을 추가하고 Vagrant box를 실행하면 웹 브라우저를 통해 사이트에 접속할 수 있습니다.

```bash
http://homestead.test
```

<a name="configuring-services"></a>
### Configuring Services
### 서비스 구성

Homestead starts several services by default; however, you may customize which services are enabled or disabled during provisioning. For example, you may enable PostgreSQL and disable MySQL by modifying the `services` option within your `Homestead.yaml` file:

홈스테드는 기본적으로 여러 서비스를 시작합니다. 그러나 프로비저닝하는 동안 활성화 또는 비활성화된 서비스를 사용자 지정할 수 있습니다. 예를 들어 `Homestead.yaml` 파일에서 `services` 옵션을 수정하여 PostgreSQL을 활성화하고 MySQL을 비활성화할 수 있습니다.

```yaml
services:
    - enabled:
        - "postgresql"
    - disabled:
        - "mysql"
```

The specified services will be started or stopped based on their order in the `enabled` and `disabled` directives.

지정된 서비스는 `enabled` 및 `disabled` 지시문의 순서에 따라 시작되거나 중지됩니다.

<a name="launching-the-vagrant-box"></a>
### Launching The Vagrant Box
### Vagrant Box 구동하기

Once you have edited the `Homestead.yaml` to your liking, run the `vagrant up` command from your Homestead directory. Vagrant will boot the virtual machine and automatically configure your shared folders and Nginx sites.

`Homestead.yaml` 파일 설정이 끝났다면, 홈스테드 디렉토리에서 `vagrant up` 명령어를 실행하십시오. Vagrant가 가상머신을 구동시키고 자동으로 공유폴더와 Nginx 사이트를 설정할 것입니다.

To destroy the machine, you may use the `vagrant destroy` command.

가상 머신을 제거하기 위해서는 `vagrant destroy` 명령어를 사용하면 됩니다.

<a name="per-project-installation"></a>
### Per Project Installation
### 프로젝트별 설치하기

Instead of installing Homestead globally and sharing the same Homestead virtual machine across all of your projects, you may instead configure a Homestead instance for each project you manage. Installing Homestead per project may be beneficial if you wish to ship a `Vagrantfile` with your project, allowing others working on the project to `vagrant up` immediately after cloning the project's repository.

홈스테드를 전역적으로 설치하고 모든 프로젝트에서 동일한 홈스테드 가상 머신을 공유하는 대신 관리하는 각 프로젝트에 대해 홈스테드 인스턴스를 구성할 수 있습니다. 프로젝트별로 홈스테드를 설치하면 프로젝트와 함께 `Vagrantfile`을 제공하여 프로젝트에서 작업하는 다른 사람들이 프로젝트의 저장소를 복제한 직후 `vagrant up`할 수 있도록 하는 데 도움이 될 수 있습니다.

You may install Homestead into your project using the Composer package manager:

Composer 패키지 관리자를 사용하여 홈스테드를 프로젝트에 설치할 수 있습니다.

```bash
composer require laravel/homestead --dev
```

Once Homestead has been installed, invoke Homestead's `make` command to generate the `Vagrantfile` and `Homestead.yaml` file for your project. These files will be placed in the root of your project. The `make` command will automatically configure the `sites` and `folders` directives in the `Homestead.yaml` file:

홈스테드가 설치되면 홈스테드의 `make` 명령을 호출하여 프로젝트에 대한 `Vagrantfile` 및 `Homestead.yaml` 파일을 생성합니다. 이 파일은 프로젝트의 루트에 배치됩니다. `make` 명령은 `Homestead.yaml` 파일에서 `sites` 및 `folders` 지시문을 자동으로 구성합니다.

    // macOS / Linux...
    php vendor/bin/homestead make

    // Windows...
    vendor\\bin\\homestead make

Next, run the `vagrant up` command in your terminal and access your project at `http://homestead.test` in your browser. Remember, you will still need to add an `/etc/hosts` file entry for `homestead.test` or the domain of your choice if you are not using automatic [hostname resolution](#hostname-resolution).

다음으로 터미널에서 `vagrant up` 명령을 실행하고, 브라우저의 `http://homestead.test`로 프로젝트에 접속합니다. 자동 [호스트 이름 확인](#hostname-resolution)을 사용하지 않는 경우 `homestead.test` 또는 선택한 도메인에 대한 `/etc/hosts` 파일 항목을 추가해야 한다는 것을 기억하십시오.

<a name="installing-optional-features"></a>
### Installing Optional Features
### 옵션 기능 설치

Optional software is installed using the `features` option within your `Homestead.yaml` file. Most features can be enabled or disabled with a boolean value, while some features allow multiple configuration options:

옵션 소프트웨어는 `Homestead.yaml` 파일 내의 `features` 옵션을 사용하여 설치됩니다. 대부분의 기능은 boolean 값으로 활성화 또는 비활성화할 수 있지만, 일부 기능은 여러가지 설정 옵션을 입력해야 합니다.

    features:
        - blackfire:
            server_id: "server_id"
            server_token: "server_value"
            client_id: "client_id"
            client_token: "client_value"
        - cassandra: true
        - chronograf: true
        - couchdb: true
        - crystal: true
        - docker: true
        - elasticsearch:
            version: 7.9.0
        - eventstore: true
            version: 21.2.0
        - gearman: true
        - golang: true
        - grafana: true
        - influxdb: true
        - mariadb: true
        - meilisearch: true
        - minio: true
        - mongodb: true
        - neo4j: true
        - ohmyzsh: true
        - openresty: true
        - pm2: true
        - python: true
        - r-base: true
        - rabbitmq: true
        - rvm: true
        - solr: true
        - timescaledb: true
        - trader: true
        - webdriver: true

<a name="elasticsearch"></a>
#### Elasticsearch
#### Elasticsearch

You may specify a supported version of Elasticsearch, which must be an exact version number (major.minor.patch). The default installation will create a cluster named 'homestead'. You should never give Elasticsearch more than half of the operating system's memory, so make sure your Homestead virtual machine has at least twice the Elasticsearch allocation.

지원되는 Elasticsearch 버전을 정확한 버전 번호(major.minor.patch)로 지정할 수 있습니다. 기본 설치는 `homestead` 라는 클러스터를 생성합니다. Elasticsearch에 운영 체제 메모리의 절반 이상을 제공해서는 안 되므로 Homestead 가상 머신에 Elasticsearch에 할당된 메모리의 최소 2배가 존재하는지 확인하십시오.


> {tip} Check out the [Elasticsearch documentation](https://www.elastic.co/guide/en/elasticsearch/reference/current) to learn how to customize your configuration.

> {tip} [Elasticsearch 문서](https://www.elastic.co/guide/en/elasticsearch/reference/current) 를 확인하여 설정을 커스터마이징하는 방법을 알아보세요.

<a name="mariadb"></a>
#### MariaDB
#### MariaDB

Enabling MariaDB will remove MySQL and install MariaDB. MariaDB typically serves as a drop-in replacement for MySQL, so you should still use the `mysql` database driver in your application's database configuration.

MariaDB를 활성화하면 MySQL이 제거되고 MariaDB가 설치됩니다. MariaDB는 일반적으로 MySQL을 대체하는 역할을 하므로 애플리케이션의 데이터베이스 설정에서 `mysql` 데이터베이스 드라이버를 계속 사용해야 합니다.

<a name="mongodb"></a>
#### MongoDB
#### MongoDB

The default MongoDB installation will set the database username to `homestead` and the corresponding password to `secret`.

기본 MongoDB 설치는 데이터베이스 사용자 이름을 `homestead`로 설정하고 해당 비밀번호를 `secret`으로 설정합니다.

<a name="neo4j"></a>
#### Neo4j
#### Neo4j

The default Neo4j installation will set the database username to `homestead` and the corresponding password to `secret`. To access the Neo4j browser, visit `http://homestead.test:7474` via your web browser. The ports `7687` (Bolt), `7474` (HTTP), and `7473` (HTTPS) are ready to serve requests from the Neo4j client.

기본 Neo4j 설치는 데이터베이스 사용자 이름을 `homestead`로 설정하고 해당 비밀번호를 `secret`으로 설정합니다. Neo4j 브라우저에 접속하려면 웹 브라우저를 통해 `http://homestead.test:7474`로 방문하십시오. `7687`(Bolt), `7474`(HTTP) 및 `7473`(HTTPS) 포트는 Neo4j 클라이언트의 요청을 처리할 준비가 되어있습니다.

<a name="aliases"></a>
### Aliases
### 별칭 설정

You may add Bash aliases to your Homestead virtual machine by modifying the `aliases` file within your Homestead directory:

홈스테드 디렉토리에서 `aliases` 파일을 수정하여 홈스테드 가상 머신내 Bash의 별칭을 추가할 수 있습니다.

    alias c='clear'
    alias ..='cd ..'

After you have updated the `aliases` file, you should re-provision the Homestead virtual machine using the `vagrant reload --provision` command. This will ensure that your new aliases are available on the machine.

`aliases` 파일을 수정한 후 `vagrant reload --provision` 명령을 사용하여 홈스테드 가상 머신을 다시 프로비저닝해야 합니다. 이렇게 하면 시스템에서 수정된 별칭을 사용할 수 있습니다.

<a name="updating-homestead"></a>
## Updating Homestead
## 홈스테드 업데이트

Before you begin updating Homestead you should ensure you have removed your current virtual machine by running the following command in your Homestead directory:

홈스테드 업데이트를 시작하기 전에 홈스테드 디렉터리에서 다음 명령을 실행하여 현재 가상 머신을 제거했는지 확인해야 합니다.

    vagrant destroy

Next, you need to update the Homestead source code. If you cloned the repository, you can execute the following commands at the location you originally cloned the repository:

다음으로 홈스테드 소스 코드를 업데이트해야 합니다. 리포지토리를 복제한 경우 원래 리포지토리를 복제한 위치에서 다음 명령을 실행할 수 있습니다.

    git fetch

    git pull origin release

These commands pull the latest Homestead code from the GitHub repository, fetch the latest tags, and then check out the latest tagged release. You can find the latest stable release version on Homestead's [GitHub releases page](https://github.com/laravel/homestead/releases).

이 명령은 GitHub 리포지토리에서 최신 홈스테드 코드를 가져오고 최신 태그를 가져온 다음 태그가 지정된 최신 릴리스를 확인합니다. 홈스테드의 [GitHub 릴리스 페이지](https://github.com/laravel/homestead/releases) 에서 안정적인 최신 릴리스 버전을 찾을 수 있습니다.

If you have installed Homestead via your project's `composer.json` file, you should ensure your `composer.json` file contains `"laravel/homestead": "^12"` and update your dependencies:

프로젝트의 `composer.json` 파일을 통해 홈스테드를 설치했다면, `composer.json` 파일에 `"laravel/homestead": "^12"`가 포함되어 있는지 확인하고 의존성을 업데이트해야 합니다.

    composer update

Next, you should update the Vagrant box using the `vagrant box update` command:

다음으로 `vagrant box update` 명령을 사용하여 Vagrant 상자를 업데이트해야 합니다.

    vagrant box update

After updating the Vagrant box, you should run the `bash init.sh` command from the Homestead directory in order to update Homestead's additional configuration files. You will be asked whether you wish to overwrite your existing `Homestead.yaml`, `after.sh`, and `aliases` files:

Vagrant 상자를 업데이트한 후 홈스테드의 추가 설정 파일을 업데이트하려면, 홈스테드 디렉토리에서 `bash init.sh` 명령을 실행해야 합니다. 기존 `Homestead.yaml`, `after.sh` 및 `aliases` 파일을 덮어쓸 것인지 묻는 메시지가 표시됩니다.

    // macOS / Linux...
    bash init.sh

    // Windows...
    init.bat

Finally, you will need to regenerate your Homestead virtual machine to utilize the latest Vagrant installation:

마지막으로 최신 Vagrant 설치를 활용하려면 홈스테드 가상 머신을 다시 생성해야 합니다.

    vagrant up

<a name="daily-usage"></a>
## Daily Usage
## 사용 방법

<a name="connecting-via-ssh"></a>
### Connecting Via SSH
### SSH를 통해 연결

You can SSH into your virtual machine by executing the `vagrant ssh` terminal command from your Homestead directory.

홈스테드 디렉토리에서 `vagrant ssh` 터미널 명령을 실행하여 가상 머신에 SSH로 접속 할 수 있습니다.

<a name="adding-additional-sites"></a>
### Adding Additional Sites
### 사이트 추가

Once your Homestead environment is provisioned and running, you may want to add additional Nginx sites for your other Laravel projects. You can run as many Laravel projects as you wish on a single Homestead environment. To add an additional site, add the site to your `Homestead.yaml` file.

홈스테드 환경이 프로비저닝되고 실행되면, 다른 라라벨 프로젝트에 Nginx 사이트를 추가할 수 있습니다. 단일 홈스테드 환경에서 원하는 만큼 라라벨 프로젝트를 실행할 수 있습니다. 사이트를 추가하려면 `Homestead.yaml` 파일에 사이트를 추가하세요.

    sites:
        - map: homestead.test
          to: /home/vagrant/project1/public
        - map: another.test
          to: /home/vagrant/project2/public

> {note} You should ensure that you have configured a [folder mapping](#configuring-shared-folders) for the project's directory before adding the site.

> {참고} 사이트를 추가하기 전에 프로젝트 디렉토리에 대한 [폴더 매핑](#configuring-shared-folders)을 설정했는지 확인해야 합니다.

If Vagrant is not automatically managing your "hosts" file, you may need to add the new site to that file as well. On macOS and Linux, this file is located at `/etc/hosts`. On Windows, it is located at `C:\Windows\System32\drivers\etc\hosts`:

Vagrant가 "hosts" 파일을 자동으로 관리하지 않는 경우, 해당 파일에 새 사이트를 추가해야 할 수도 있습니다. macOS 및 Linux에서 이 파일은 `/etc/hosts`에 존재합니다. Windows에서는 `C:\Windows\System32\drivers\etc\hosts`에 존재합니다.

    192.168.56.56  homestead.test
    192.168.56.56  another.test

Once the site has been added, execute the `vagrant reload --provision` terminal command from your Homestead directory.

사이트가 추가되면 홈스테드 디렉토리에서 `vagrant reload --provision` 터미널 명령을 실행하십시오.

<a name="site-types"></a>
#### Site Types
#### 사이트 유형

Homestead supports several "types" of sites which allow you to easily run projects that are not based on Laravel. For example, we may easily add a Statamic application to Homestead using the `statamic` site type:

홈스테드는 라라벨을 기반으로 하지 않는 프로젝트를 쉽게 실행할 수 있는 여러 "유형"의 사이트를 지원합니다. 예를 들어 `statamic` 사이트 유형을 사용하여 Homestead에 Statamic 애플리케이션을 쉽게 추가할 수 있습니다.

```yaml
sites:
    - map: statamic.test
      to: /home/vagrant/my-symfony-project/web
      type: "statamic"
```

The available site types are: `apache`, `apigility`, `expressive`, `laravel` (the default), `proxy`, `silverstripe`, `statamic`, `symfony2`, `symfony4`, and `zf`.

사용 가능한 사이트 유형은 `apache`, `apigility`, `expressive`, `laravel`(기본값), `proxy`, `silverstripe`, `statamic`, `symfony2`, `symfony4` 및 `zf`입니다.

<a name="site-parameters"></a>
#### Site Parameters
#### 사이트 매개변수

You may add additional Nginx `fastcgi_param` values to your site via the `params` site directive:

`params` 사이트 지시문을 통해 사이트에 Nginx `fastcgi_param` 값을 추가할 수 있습니다.

    sites:
        - map: homestead.test
          to: /home/vagrant/project1/public
          params:
              - key: FOO
                value: BAR

<a name="environment-variables"></a>
### Environment Variables
### 환경 변수

You can define global environment variables by adding them to your `Homestead.yaml` file:

`Homestead.yaml` 파일에 추가하여 전역 환경 변수를 정의할 수 있습니다.

    variables:
        - key: APP_ENV
          value: local
        - key: FOO
          value: bar

After updating the `Homestead.yaml` file, be sure to re-provision the machine by executing the `vagrant reload --provision` command. This will update the PHP-FPM configuration for all of the installed PHP versions and also update the environment for the `vagrant` user.

`Homestead.yaml` 파일을 업데이트한 후 `vagrant reload --provision` 명령을 실행하여 머신을 다시 프로비저닝해야 합니다. 이것은 설치된 모든 PHP 버전에 대한 PHP-FPM 구성을 업데이트하고 `vagrant` 사용자의 환경도 업데이트합니다.

<a name="ports"></a>
### Ports
### Ports

By default, the following ports are forwarded to your Homestead environment:

기본적으로 다음 포트는 홈스테드 환경으로 전달됩니다.

- **HTTP:** 8000 &rarr; Forwards To 80
- **HTTPS:** 44300 &rarr; Forwards To 443

<a name="forwarding-additional-ports"></a>
#### Forwarding Additional Ports
#### 추가로 포트 전달하기

If you wish, you may forward additional ports to the Vagrant box by defining a `ports` configuration entry within your `Homestead.yaml` file. After updating the `Homestead.yaml` file, be sure to re-provision the machine by executing the `vagrant reload --provision` command:

원하는 경우 `Homestead.yaml` 파일 내에서 `ports` 설정 항목을 정의하여 Vagrant 상자에 추가적으로 포트를 전달할 수 있습니다. `Homestead.yaml` 파일을 업데이트한 후 `vagrant reload --provision` 명령을 실행하여 머신을 다시 프로비저닝해야 합니다.

    ports:
        - send: 50000
          to: 5000
        - send: 7777
          to: 777
          protocol: udp

Below is a list of additional Homestead service ports that you may wish to map from your host machine to your Vagrant box:

다음은 호스트 시스템에서 Vagrant 상자로 매핑할 수 있는 추가 홈스테드 서비스 포트 목록입니다.

- **SSH:** 2222 &rarr; To 22
- **ngrok UI:** 4040 &rarr; To 4040
- **MySQL:** 33060 &rarr; To 3306
- **PostgreSQL:** 54320 &rarr; To 5432
- **MongoDB:** 27017 &rarr; To 27017
- **Mailhog:** 8025 &rarr; To 8025
- **Minio:** 9600 &rarr; To 9600

<a name="php-versions"></a>
### PHP Versions
### PHP 버전

Homestead 6 introduced support for running multiple versions of PHP on the same virtual machine. You may specify which version of PHP to use for a given site within your `Homestead.yaml` file. The available PHP versions are: "5.6", "7.0", "7.1", "7.2", "7.3", "7.4", "8.0" (the default), and "8.1":

홈스테드 6은 동일한 가상 머신에서 여러 버전의 PHP를 실행하기 위한 지원을 시작했습니다. `Homestead.yaml` 파일 내에서 주어진 사이트에 사용할 PHP 버전을 지정할 수 있습니다. 사용 가능한 PHP 버전은 "5.6", "7.0", "7.1", "7.2", "7.3", "7.4", "8.0"(기본값) 및 "8.1"입니다.

    sites:
        - map: homestead.test
          to: /home/vagrant/project1/public
          php: "7.1"

[Within your Homestead virtual machine](#connecting-via-ssh), you may use any of the supported PHP versions via the CLI:

[홈스테드 가상 머신 내에서](#connecting-via-ssh) CLI를 통해 지원되는 모든 PHP 버전을 사용할 수 있습니다.

    php5.6 artisan list
    php7.0 artisan list
    php7.1 artisan list
    php7.2 artisan list
    php7.3 artisan list
    php7.4 artisan list
    php8.0 artisan list
    php8.1 artisan list

You may change the default version of PHP used by the CLI by issuing the following commands from within your Homestead virtual machine:

홈스테드 가상 머신 내에서 다음 명령을 실행하여 CLI에서 사용하는 PHP의 기본 버전을 변경할 수 있습니다.

    php56
    php70
    php71
    php72
    php73
    php74
    php80
    php81

<a name="connecting-to-databases"></a>
### Connecting To Databases
### 데이터베이스에 연결

A `homestead` database is configured for both MySQL and PostgreSQL out of the box. To connect to your MySQL or PostgreSQL database from your host machine's database client, you should connect to `127.0.0.1` on port `33060` (MySQL) or `54320` (PostgreSQL). The username and password for both databases is `homestead` / `secret`.

`homestead` 데이터베이스는 기본적으로 MySQL과 PostgreSQL 모두 생성됩니다. 호스트 머신의 데이터베이스 클라이언트에서 MySQL 또는 PostgreSQL 데이터베이스에 연결하려면 `127.0.0.1`에서 포트 `33060`(MySQL) 또는 `54320`(PostgreSQL)에 연결해야 합니다. 두 데이터베이스의 사용자 이름과 비밀번호는 `homestead` / `secret`입니다.

> {note} You should only use these non-standard ports when connecting to the databases from your host machine. You will use the default 3306 and 5432 ports in your Laravel application's `database` configuration file since Laravel is running _within_ the virtual machine.

> {note} 호스트 머신에서 데이터베이스에 연결할 때 이러한 비표준 포트만 사용해야 합니다. 라라벨은 가상 머신 _내에서_ 실행 중이므로 라라벨 애플리케이션의 `database` 설정 파일에서는 기본 3306 및 5432 포트를 사용합니다.

<a name="database-backups"></a>
### Database Backups
### 데이터베이스 백업

Homestead can automatically backup your database when your Homestead virtual machine is destroyed. To utilize this feature, you must be using Vagrant 2.1.0 or greater. Or, if you are using an older version of Vagrant, you must install the `vagrant-triggers` plug-in. To enable automatic database backups, add the following line to your `Homestead.yaml` file:

홈스테드는 홈스테드 가상 머신이 제거될 때 자동으로 데이터베이스를 백업할 수 있습니다. 이 기능을 사용하려면 Vagrant 2.1.0 이상을 사용해야 합니다. 또는 이전 버전의 Vagrant를 사용하는 경우 `vagrant-triggers` 플러그인을 설치해야 합니다. 자동 데이터베이스 백업을 활성화하려면 `Homestead.yaml` 파일에 다음 라인을 추가하세요.

    backup: true

Once configured, Homestead will export your databases to `mysql_backup` and `postgres_backup` directories when the `vagrant destroy` command is executed. These directories can be found in the folder where you installed Homestead or in the root of your project if you are using the [per project installation](#per-project-installation) method.

일단 구성되면 홈스테드는 `vagrant destroy` 명령이 실행될 때 데이터베이스를 `mysql_backup` 및 `postgres_backup` 디렉토리로 내보냅니다. 이 디렉토리는 홈스테드를 설치한 폴더 또는 [프로젝트별 설치](#per-project-installation)를 사용하는 경우 프로젝트의 루트에서 찾을 수 있습니다.

<a name="configuring-cron-schedules"></a>
### Configuring Cron Schedules
### 주기적인 Cron 설정

Laravel provides a convenient way to [schedule cron jobs](/docs/{{version}}/scheduling) by scheduling a single `schedule:run` Artisan command to run every minute. The `schedule:run` command will examine the job schedule defined in your `App\Console\Kernel` class to determine which scheduled tasks to run.

라라벨은 매분 실행되도록 `schedule:run` Artisan 명령을 예약하여 [주기적인 cron 작업](/docs/{{version}}/scheduling)을 실행하기 위한 편리한 방법을 제공합니다. `schedule:run` 명령은 `App\Console\Kernel` 클래스에 정의된 작업 일정을 검사하여 실행할 예약된 작업을 결정합니다.

If you would like the `schedule:run` command to be run for a Homestead site, you may set the `schedule` option to `true` when defining the site:

홈스테드 사이트에 대해 `schedule:run` 명령을 실행하려면 사이트를 정의할 때 `schedule` 옵션을 `true`로 설정하면 됩니다.

```yaml
sites:
    - map: homestead.test
      to: /home/vagrant/project1/public
      schedule: true
```

The cron job for the site will be defined in the `/etc/cron.d` directory of the Homestead virtual machine.

사이트의 크론 작업은 홈스테드 가상 머신의 `/etc/cron.d` 디렉토리에 정의됩니다.

<a name="configuring-mailhog"></a>
### Configuring MailHog
### 메일호그 설정

[MailHog](https://github.com/mailhog/MailHog) allows you to intercept your outgoing email and examine it without actually sending the mail to its recipients. To get started, update your application's `.env` file to use the following mail settings:

[MailHog](https://github.com/mailhog/MailHog) 를 사용하면 실제로 받는 사람에게 메일을 보내지 않고도 보내는 이메일을 가로채서 검토할 수 있습니다. 시작하려면 다음 메일 설정을 사용하도록 애플리케이션의 `.env` 파일을 수정하세요.

    MAIL_MAILER=smtp
    MAIL_HOST=localhost
    MAIL_PORT=1025
    MAIL_USERNAME=null
    MAIL_PASSWORD=null
    MAIL_ENCRYPTION=null

Once MailHog has been configured, you may access the MailHog dashboard at `http://localhost:8025`.

MailHog가 구성되면 `http://localhost:8025`에서 MailHog 대시보드에 액세스할 수 있습니다.

<a name="configuring-minio"></a>
### Configuring Minio
### Minio 설정

[Minio](https://github.com/minio/minio) is an open source object storage server with an Amazon S3 compatible API. To install Minio, update your `Homestead.yaml` file with the following configuration option in the [features](#installing-optional-features) section:

[Minio](https://github.com/minio/minio) 는 Amazon S3와 호환이 가능한 API를 제공하는 오픈 소스 객체 스토리지 서버입니다. Minio를 설치하려면 [features](#installing-optional-features) 섹션에서 다음 설정 옵션으로 `Homestead.yaml` 파일을 업데이트하십시오.

    minio: true

By default, Minio is available on port 9600. You may access the Minio control panel by visiting `http://localhost:9600`. The default access key is `homestead`, while the default secret key is `secretkey`. When accessing Minio, you should always use region `us-east-1`.

기본적으로 Minio는 9600 포트에서 사용할 수 있습니다. Minio 제어판은 `http://localhost:9600`을 방문하여 접속할 수 있습니다. 기본 액세스 키는 `homestead`이고 기본 비밀 키는 `secretkey`입니다. Minio에 액세스할 때 항상 `us-east-1` 지역을 사용해야 합니다.

In order to use Minio, you will need to adjust the S3 disk configuration in your application's `config/filesystems.php` configuration file. You will need to add the `use_path_style_endpoint` option to the disk configuration as well as change the `url` key to `endpoint`:

Minio를 사용하려면 애플리케이션의 `config/filesystems.php` 설정 파일에서 S3 디스크 설정을 조정해야 합니다. 디스크 설정에 `use_path_style_endpoint` 옵션을 추가하고 `url` 키를 `endpoint`로 변경해야 합니다.

    's3' => [
        'driver' => 's3',
        'key' => env('AWS_ACCESS_KEY_ID'),
        'secret' => env('AWS_SECRET_ACCESS_KEY'),
        'region' => env('AWS_DEFAULT_REGION'),
        'bucket' => env('AWS_BUCKET'),
        'endpoint' => env('AWS_URL'),
        'use_path_style_endpoint' => true,
    ]

Finally, ensure your `.env` file has the following options:

마지막으로 `.env` 파일에 다음 옵션이 있는지 확인하세요.

```bash
AWS_ACCESS_KEY_ID=homestead
AWS_SECRET_ACCESS_KEY=secretkey
AWS_DEFAULT_REGION=us-east-1
AWS_URL=http://localhost:9600
```

To provision Minio powered "S3" buckets, add a `buckets` directive to your `Homestead.yaml` file. After defining your buckets, you should execute the `vagrant reload --provision` command in your terminal:

Minio 기반 "S3" 버킷을 프로비저닝하려면 `Homestead.yaml` 파일에 `buckets` 지시문을 추가하십시오. 버킷을 정의한 후 터미널에서 `vagrant reload --provision` 명령을 실행해야 합니다.

```yaml
buckets:
    - name: your-bucket
      policy: public
    - name: your-private-bucket
      policy: none
```

Supported `policy` values include: `none`, `download`, `upload`, and `public`.

지원되는 `policy` 값은 `none`, `download`, `upload` 그리고 `public`입니다.

<a name="laravel-dusk"></a>
### Laravel Dusk
### Laravel Dusk

In order to run [Laravel Dusk](/docs/{{version}}/dusk) tests within Homestead, you should enable the [`webdriver` feature](#installing-optional-features) in your Homestead configuration:

홈스테드 내에서 [Laravel Dusk](/docs/{{version}}/dusk) 테스트를 실행하려면 홈스테드 설정에서 [`webdriver` 기능](#installing-optional-features)을 활성화해야 합니다.

```yaml
features:
    - webdriver: true
```

After enabling the `webdriver` feature, you should execute the `vagrant reload --provision` command in your terminal.

`webdriver` 기능을 활성화한 후 터미널에서 `vagrant reload --provision` 명령을 실행해야 합니다.

<a name="sharing-your-environment"></a>
### Sharing Your Environment
### 환경 공유하기

Sometimes you may wish to share what you're currently working on with coworkers or a client. Vagrant has built-in support for this via the `vagrant share` command; however, this will not work if you have multiple sites configured in your `Homestead.yaml` file.

때로는 현재 작업 중인 작업을 동료나 고객과 공유하고 싶을 수 있습니다. Vagrant는 `vagrant share` 명령을 통해 이를 기본적으로 지원합니다. 그러나 `Homestead.yaml` 파일에 여러 사이트를 설정한 경우에는 작동하지 않습니다.

To solve this problem, Homestead includes its own `share` command. To get started, [SSH into your Homestead virtual machine](#connecting-via-ssh) via `vagrant ssh` and execute the `share homestead.test` command. This command will share the `homestead.test` site from your `Homestead.yaml` configuration file. You may substitute any of your other configured sites for `homestead.test`:

이 문제를 해결하기 위해 홈스테드는 자체 `share` 명령을 제공합니다. 시작하려면 `vagrant ssh`를 통해 [홈스테드 가상 머신에 SSH로 연결](#connecting-via-ssh)하고 `share homestead.test` 명령을 실행하세요. 이 명령은 `Homestead.yaml` 설정 파일의 `homestead.test` 사이트를 공유합니다. `homestead.test`를 설정한 다른 사이트로 대체할 수 있습니다.

    share homestead.test

After running the command, you will see an Ngrok screen appear which contains the activity log and the publicly accessible URLs for the shared site. If you would like to specify a custom region, subdomain, or other Ngrok runtime option, you may add them to your `share` command:

명령을 실행하면 활동 로그와 공유된 사이트에 대해 공개적으로 액세스할 수 있는 URL이 포함된 Ngrok 화면이 표시됩니다. 사용자 영역 지정, 하위 도메인 또는 기타 Ngrok 런타임 옵션을 지정하려면 `share` 명령에 다음과 같이 추가할 수 있습니다.

    share homestead.test -region=eu -subdomain=laravel

> {note} Remember, Vagrant is inherently insecure and you are exposing your virtual machine to the Internet when running the `share` command.

> {note} Vagrant는 본질적으로 안전하지 않으며 `share` 명령을 실행할 때 가상 머신을 인터넷에 노출시킵니다.

<a name="debugging-and-profiling"></a>
## Debugging & Profiling
## 디버깅과 프로파일링

<a name="debugging-web-requests"></a>
### Debugging Web Requests With Xdebug
### Xdebug로 웹 요청 디버깅

Homestead includes support for step debugging using [Xdebug](https://xdebug.org). For example, you can access a page in your browser and PHP will connect to your IDE to allow inspection and modification of the running code.

홈스테드는 [Xdebug](https://xdebug.org)를 사용한 단계별 디버깅 지원을 포함합니다. 예를 들어 브라우저에서 페이지에 접속하면 PHP를 IDE에 연결하여 실행 중인 코드를 검사하고 수정할 수 있습니다.

By default, Xdebug is already running and ready to accept connections. If you need to enable Xdebug on the CLI, execute the `sudo phpenmod xdebug` command within your Homestead virtual machine. Next, follow your IDE's instructions to enable debugging. Finally, configure your browser to trigger Xdebug with an extension or [bookmarklet](https://www.jetbrains.com/phpstorm/marklets/).

기본적으로 Xdebug는 이미 실행 중이며 연결을 수락할 준비가 되어 있습니다. CLI에서 Xdebug를 활성화해야 하는 경우 홈스테드 가상 머신 내에서 `sudo phpenmod xdebug` 명령을 실행합니다. 그런 다음 IDE의 지침에 따라 디버깅을 활성화합니다. 마지막으로 확장 프로그램 또는 [bookmarklet](https://www.jetbrains.com/phpstorm/marklets/) 으로 Xdebug를 실행하도록 브라우저를 구성합니다.

> {note} Xdebug causes PHP to run significantly slower. To disable Xdebug, run `sudo phpdismod xdebug` within your Homestead virtual machine and restart the FPM service.

> {note} Xdebug로 인해 PHP가 상당히 느리게 실행됩니다. Xdebug를 비활성화하려면 홈스테드 가상 머신 내에서 `sudo phpdismod xdebug`를 실행하고 FPM 서비스를 다시 시작하십시오.

<a name="autostarting-xdebug"></a>
#### Autostarting Xdebug
#### Xdebug 자동시작

When debugging functional tests that make requests to the web server, it is easier to autostart debugging rather than modifying tests to pass through a custom header or cookie to trigger debugging. To force Xdebug to start automatically, modify the `/etc/php/7.x/fpm/conf.d/20-xdebug.ini` file inside your Homestead virtual machine and add the following configuration:

웹 서버에 요청을 보내는 기능 테스트를 디버깅할 때, 디버깅을 트리거하기 위해 사용자 지정 헤더 또는 쿠키를 통과하도록 테스트를 수정하는 것보다 자동으로 디버깅을 시작하는 것이 더 쉽습니다. Xdebug가 자동으로 시작되도록 하려면 홈스테드 가상 머신 내부의 `/etc/php/7.x/fpm/conf.d/20-xdebug.ini` 파일을 수정하고 다음 설정을 추가하십시오.

```ini
; If Homestead.yaml contains a different subnet for the IP address, this address may be different...
xdebug.remote_host = 192.168.10.1
xdebug.remote_autostart = 1
```

<a name="debugging-cli-applications"></a>
### Debugging CLI Applications
### CLI 애플리케이션 디버깅

To debug a PHP CLI application, use the `xphp` shell alias inside your Homestead virtual machine:

PHP CLI 애플리케이션을 디버그하려면 홈스테드 가상 머신 내에서 `xphp` 셸 별칭을 사용하세요.

    xphp /path/to/script

<a name="profiling-applications-with-blackfire"></a>
### Profiling Applications with Blackfire
### Blackfire로 애플리케이션 프로파일링

[Blackfire](https://blackfire.io/docs/introduction) is a service for profiling web requests and CLI applications. It offers an interactive user interface which displays profile data in call-graphs and timelines. It is built for use in development, staging, and production, with no overhead for end users. In addition, Blackfire provides performance, quality, and security checks on code and `php.ini` configuration settings.

[Blackfire](https://blackfire.io/docs/introduction) 는 웹 요청 및 CLI 애플리케이션을 프로파일링하는 서비스입니다. 호출 그래프 및 타임라인에 프로필 데이터를 표시하는 대화형 사용자 인터페이스를 제공합니다. 최종 사용자를 위한 오버헤드 없이 개발, 스테이징 및 프로덕션에 사용하도록 구축되었습니다. 또한 Blackfire는 코드 및 `php.ini` 설정에 대한 성능, 품질 및 보안 검사를 제공합니다.

The [Blackfire Player](https://blackfire.io/docs/player/index) is an open-source Web Crawling, Web Testing, and Web Scraping application which can work jointly with Blackfire in order to script profiling scenarios.

[Blackfire Player](https://blackfire.io/docs/player/index) 는 프로파일링 시나리오를 스크립팅하기 위해 Blackfire와 함께 작동할 수 있는 오픈 소스 웹 크롤링, 웹 테스팅 및 웹 스크래핑 애플리케이션입니다.

To enable Blackfire, use the "features" setting in your Homestead configuration file:

Blackfire를 활성화하려면 Homestead 구성 파일에서 "features" 설정을 사용하십시오.

```yaml
features:
    - blackfire:
        server_id: "server_id"
        server_token: "server_value"
        client_id: "client_id"
        client_token: "client_value"
```

Blackfire server credentials and client credentials [require a Blackfire account](https://blackfire.io/signup). Blackfire offers various options to profile an application, including a CLI tool and browser extension. Please [review the Blackfire documentation for more details](https://blackfire.io/docs/cookbooks/index).

Blackfire 서버 자격 증명 및 클라이언트 자격 증명은 [Blackfire 계정이 필요합니다](https://blackfire.io/signup). Blackfire는 CLI 도구 및 브라우저 확장을 포함하여 애플리케이션을 프로파일링하는 다양한 옵션을 제공합니다. [자세한 내용은 Blackfire 문서](https://blackfire.io/docs/cookbooks/index) 를 참조하세요.

<a name="network-interfaces"></a>
## Network Interfaces
## 네트워크 인터페이스

The `networks` property of the `Homestead.yaml` file configures network interfaces for your Homestead virtual machine. You may configure as many interfaces as necessary:

`Homestead.yaml` 파일의 `networks` 속성은 홈스테드 가상 머신에 대한 네트워크 인터페이스를 설정합니다. 필요한 만큼 인터페이스를 설정할 수 있습니다.

```yaml
networks:
    - type: "private_network"
      ip: "192.168.10.20"
```

To enable a [bridged](https://www.vagrantup.com/docs/networking/public_network.html) interface, configure a `bridge` setting for the network and change the network type to `public_network`:

[bridged](https://www.vagrantup.com/docs/networking/public_network.html) 인터페이스를 활성화하려면 네트워크에 대한 `bridge` 설정을 구성하고 네트워크 유형을 `public_network`로 변경합니다.

```yaml
networks:
    - type: "public_network"
      ip: "192.168.10.20"
      bridge: "en1: Wi-Fi (AirPort)"
```

To enable [DHCP](https://www.vagrantup.com/docs/networking/public_network.html), just remove the `ip` option from your configuration:

[DHCP](https://www.vagrantup.com/docs/networking/public_network.html) 를 활성화하려면 구성에서 `ip` 옵션을 제거하기만 하면 됩니다.

```yaml
networks:
    - type: "public_network"
      bridge: "en1: Wi-Fi (AirPort)"
```

<a name="extending-homestead"></a>
## Extending Homestead
## 홈스테드 확장하기

You may extend Homestead using the `after.sh` script in the root of your Homestead directory. Within this file, you may add any shell commands that are necessary to properly configure and customize your virtual machine.

홈스테드 디렉토리의 루트에 있는 `after.sh` 스크립트를 사용하여 홈스테드를 확장할 수 있습니다. 이 파일 내에서 가상 머신을 적절하게 설정하고 사용자 지정하는 데 필요한 셸 명령을 추가할 수 있습니다.

When customizing Homestead, Ubuntu may ask you if you would like to keep a package's original configuration or overwrite it with a new configuration file. To avoid this, you should use the following command when installing packages in order to avoid overwriting any configuration previously written by Homestead:

홈스테드를 커스터마이징할 때 Ubuntu는 패키지의 원래 설정을 유지할 것인지 아니면 새 설정 파일로 덮어쓸 것인지 묻습니다. 이를 방지하려면 홈스테드에서 이전에 작성한 설정을 덮어쓰지 않도록 패키지를 설치할 때 다음 명령을 사용해야 합니다.

    sudo apt-get -y \
        -o Dpkg::Options::="--force-confdef" \
        -o Dpkg::Options::="--force-confold" \
        install package-name

<a name="user-customizations"></a>
### User Customizations
### 사용자별 커스터마이징

When using Homestead with your team, you may want to tweak Homestead to better fit your personal development style. To accomplish this, you may create a `user-customizations.sh` file in the root of your Homestead directory (the same directory containing your `Homestead.yaml` file). Within this file, you may make any customization you would like; however, the `user-customizations.sh` should not be version controlled.

팀원들과 함께 홈스테드를 사용할 때, 개인 개발 스타일에 더 잘 맞도록 홈스테드를 조정할 수 있습니다. 이를 수행하기 위해 홈스테드 디렉토리(`Homestead.yaml` 파일이 포함된 동일한 디렉토리)의 루트에 `user-customizations.sh` 파일을 생성할 수 있습니다. 이 파일 내에서 원하는 대로 사용자 정의할 수 있습니다. 그러나 `user-customizations.sh`는 버전 관리되어서는 안 됩니다.

<a name="provider-specific-settings"></a>
## Provider Specific Settings
## 공급자별 설정

<a name="provider-specific-virtualbox"></a>
### VirtualBox
### VirtualBox

<a name="natdnshostresolver"></a>
#### `natdnshostresolver`
#### `natdnshostresolver`

By default, Homestead configures the `natdnshostresolver` setting to `on`. This allows Homestead to use your host operating system's DNS settings. If you would like to override this behavior, add the following configuration options to your `Homestead.yaml` file:

기본적으로 홈스테드는 `natdnshostresolver` 설정을 `on`으로 설정합니다. 이를 통해 홈스테드는 호스트 운영 체제의 DNS 설정을 사용할 수 있습니다. 이 동작을 재정의하려면 `Homestead.yaml` 파일에 다음 옵션을 추가하세요.

```yaml
provider: virtualbox
natdnshostresolver: 'off'
```

<a name="symbolic-links-on-windows"></a>
#### Symbolic Links On Windows
#### Windows의 심볼릭 링크

If symbolic links are not working properly on your Windows machine, you may need to add the following block to your `Vagrantfile`:

기호 링크가 Windows 시스템에서 제대로 작동하지 않으면 `Vagrantfile`에 다음 블록을 추가해야 할 수 있습니다.

```ruby
config.vm.provider "virtualbox" do |v|
    v.customize ["setextradata", :id, "VBoxInternal2/SharedFoldersEnableSymlinksCreate/v-root", "1"]
end
```
