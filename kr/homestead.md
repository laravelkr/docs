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
    - [Launching The Vagrant Box](#launching-the-vagrant-box)
    - [Vagrant Box 구동하기](#launching-the-vagrant-box)
    - [Per Project Installation](#per-project-installation)
    - [프로젝트별 설치하기](#per-project-installation)
    - [Aliases](#aliases)
    - [별칭 설정](#aliases)
- [Daily Usage](#daily-usage)
- [사용 방법](#daily-usage)
    - [Accessing Homestead Globally](#accessing-homestead-globally)
    - [어디에서나 홈스테드 엑세스하기](#accessing-homestead-globally)
    - [Connecting Via SSH](#connecting-via-ssh)
    - [SSH로 접속하기 ](#connecting-via-ssh)
    - [Connecting To Databases](#connecting-to-databases)
    - [데이터베이스에 접속하기](#connecting-to-databases)
    - [Database Backups](#database-backups)
    - [데이터베이스 백업하기Backups](#database-backups)
    - [Database Snapshots](#database-snapshots)
    - [데이터베이스 스냅샷](#database-snapshots)
    - [Adding Additional Sites](#adding-additional-sites)
    - [사이트 추가하기](#adding-additional-sites)
    - [Environment Variables](#environment-variables)
    - [시스템 ENV 환경 변수](#environment-variables)
    - [Configuring Cron Schedules](#configuring-cron-schedules)
    - [Cron 설정하기](#configuring-cron-schedules)
    - [Configuring Mailhog](#configuring-mailhog)
    - [Mailhog 설정하기](#configuring-mailhog)
    - [Configuring Minio](#configuring-minio)
    - [Minio 설정하기](#configuring-minio)
    - [Ports](#ports)
    - [포트 지정](#ports)
    - [Sharing Your Environment](#sharing-your-environment)
    - [환경 공유하기](#sharing-your-environment)
    - [Multiple PHP Versions](#multiple-php-versions)
    - [여러 버전의 PHP 사용하기](#multiple-php-versions)
    - [Web Servers](#web-servers)
    - [웹서버](#web-servers)
    - [Mail](#mail)
    - [이메일](#mail)
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
- [홈스테드 확장하기](#extending-homestead)
- [Updating Homestead](#updating-homestead)
- [홈스테드 업데이트하기](#updating-homestead)
- [Provider Specific Settings](#provider-specific-settings)
- [프로바이더 한정 셋팅](#provider-specific-settings)
    - [VirtualBox](#provider-specific-virtualbox)
    - [VirtualBox](#provider-specific-virtualbox)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel strives to make the entire PHP development experience delightful, including your local development environment. [Vagrant](https://www.vagrantup.com) provides a simple, elegant way to manage and provision Virtual Machines.

라라벨은 여러분의 로컬 개발 환경을 포함하여 전체 PHP 개발 경험을 유쾌하게 만들기 위해서 노력하고 있습니다. [Vagrant](https://www.vagrantup.com)은 간단하고 세련되게 가상 머신을 관리하고 준비할 수 있는 방법을 제공합니다.

Laravel Homestead is an official, pre-packaged Vagrant box that provides you a wonderful development environment without requiring you to install PHP, a web server, and any other server software on your local machine. No more worrying about messing up your operating system! Vagrant boxes are completely disposable. If something goes wrong, you can destroy and re-create the box in minutes!

라라벨 홈스테드는 공식적으로 여러분이 PHP, 웹서버, 기타 다른 서버 소프트웨어를 설치할 필요가 없는 사전에 준비된 Vagrant box이며 멋진 개발 환경을 제공합니다. 운영체제가 너저분 해지는 것을 걱정할 필요가 없습니다! Vagrant box는 완전히 일회용입니다. 만약 뭔가 잘 되지 않는다면, 여러분은 몇분안에 다시 box를 생성할 수 있습니다.

Homestead runs on any Windows, Mac, or Linux system, and includes Nginx, PHP, MySQL, PostgreSQL, Redis, Memcached, Node, and all of the other goodies you need to develop amazing Laravel applications.

홈스테드는 Windows, Mac 그리고 Linux 시스템에서 실행할 수 있으며 Nginx, PHP, MySQL, PostgreSQL, Redis, Memcached, Node 및 기타 라라벨 애플리케이션 개발에 필요한 멋진 도구들을 모두 포함하고 있습니다. .

> {note} If you are using Windows, you may need to enable hardware virtualization (VT-x). It can usually be enabled via your BIOS. If you are using Hyper-V on a UEFI system you may additionally need to disable Hyper-V in order to access VT-x.

> {note} 만약 윈도우를 사용하는 경우, 하드웨어 가상화 (VT-x)를 활성화해야합니다. 이 설정은 일반적으로 BIOS에서 사용할 수 있습니다. 만약 UEFI 시스템에서 Hyper-V 를 사용중이라면 추가적으로 VT-x에 액세스하기 위해 Hyper-V를 비활성화 해야합니다.

<a name="included-software"></a>
### Included Software
### 포함된 소프트웨어


- Ubuntu 18.04
- Git
- PHP 7.3
- PHP 7.2
- PHP 7.1
- PHP 7.0
- PHP 5.6
- Nginx
- MySQL
- lmm for MySQL or MariaDB database snapshots
- Sqlite3
- PostgreSQL
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

- Apache
- Blackfire
- Cassandra
- Chronograf
- CouchDB
- Crystal & Lucky Framework
- Docker
- Elasticsearch
- Gearman
- Go
- Grafana
- InfluxDB
- MariaDB
- MinIO
- MongoDB
- MySQL 8
- Neo4j
- Oh My Zsh
- Open Resty
- PM2
- Python
- RabbitMQ
- Solr
- Webdriver & Laravel Dusk Utilities

<a name="installation-and-setup"></a>
## Installation & Setup
## 설치 & 구성하기

<a name="first-steps"></a>
### First Steps
### 첫번째 단계

Before launching your Homestead environment, you must install [VirtualBox 6.x](https://www.virtualbox.org/wiki/Downloads), [VMWare](https://www.vmware.com), [Parallels](https://www.parallels.com/products/desktop/) or [Hyper-V](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) as well as [Vagrant](https://www.vagrantup.com/downloads.html). All of these software packages provide easy-to-use visual installers for all popular operating systems.

라라벨 홈스테드를 구동하기 전에 여러분은 반드시 [VirtualBox 6.x](https://www.virtualbox.org/wiki/Downloads), [VMWare](https://www.vmware.com) 또는 [Parallels](https://www.parallels.com/products/desktop/), [Hyper-V](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) 그리고 [Vagrant](https://www.vagrantup.com/downloads.html)를 설치해야 합니다. 이 소프트웨어 패키지들은 모든 운영체제에서 손쉽게 설치할 수 있는 인스톨러 프로그램을 제공합니다.

To use the VMware provider, you will need to purchase both VMware Fusion / Workstation and the [VMware Vagrant plug-in](https://www.vagrantup.com/vmware). Though it is not free, VMware can provide faster shared folder performance out of the box.

VMware 프로바이더를 사용하려면 VMware Fusion / Workstation 과 [VMware Vagrant plug-in](https://www.vagrantup.com/vmware)을 구매해야 합니다. 무료는 아니지만, VMware 의 경우 추가적인 설정 없이도 더 빠른 공유폴더의 퍼포먼스를 제공합니다.

To use the Parallels provider, you will need to install [Parallels Vagrant plug-in](https://github.com/Parallels/vagrant-parallels). It is free of charge.

Parallels 프로바이더를 사용하려면, [Parallels Vagrant 플러그인](https://github.com/Parallels/vagrant-parallels)을 설치해야 합니다. 이는 무료입니다.

Because of [Vagrant limitations](https://www.vagrantup.com/docs/hyperv/limitations.html), The Hyper-V provider ignores all networking settings.

[Vagrant 제한사항](https://www.vagrantup.com/docs/hyperv/limitations.html)으로 인해서, Hyper-V 프로바이더에서는 모든 네트워크 설정이 무시됩니다.

#### Installing The Homestead Vagrant Box
#### 홈스테드 Vagrant Box 설치하기

Once VirtualBox / VMware and Vagrant have been installed, you should add the `laravel/homestead` box to your Vagrant installation using the following command in your terminal. It will take a few minutes to download the box, depending on your Internet connection speed:

VirtualBox / VMware 그리고 Vagrant 가 설치되었다면, 터미널에서 다음의 명령어를 통해서 인스톨된 Vagrant에 `laravel/homestead` box 를 추가해야 합니다. box를 다운로드 하는 것은 인터넨 연결 속도에 따라 몇분 정도 소요됩니다.

    vagrant box add laravel/homestead

If this command fails, make sure your Vagrant installation is up to date.

이 명령어가 실패하면, Vagrant 설치가 최신버전인지 확인하십시오.

> {note} Periodically Homestead will issue Alpha or Beta boxes for testing. These versions *will* interfere with the `vagrant box add` command. If you are having issues running this command you can safely skip this for now and the correct base box will be downloaded the first time you run `vagrant up`.

> {note} Homestead는 정기적으로 테스트를 위해 알파 또는 베타 버전의 상자를 발행합니다. 이 버전들은 `vagrant box add` 명령을 *방해합니다*. 이 명령을 실행하는 데 문제가 있는 경우 안전하게 지금 이 단계를 건너뛰고 `vagrant up`을 처음 실행할 때 올바른 기본 상자가 다운로드됩니다.

#### Installing Homestead
#### 홈스테드 설치하기

You may install Homestead by cloning the repository onto your host machine. Consider cloning the repository into a `Homestead` folder within your "home" directory, as the Homestead box will serve as the host to all of your Laravel projects:

Git 저장소를 작업하는 머신에 복제하여 홈스테드를 설치할 수 있습니다. 여러분의 "home" 디렉토리안에 `Homestead` 폴더로 저장소를 복제하면 홈스테드 box가 라라벨 프로젝트의 모든 호스트 역할을 할 것입니다.

    git clone https://github.com/laravel/homestead.git ~/Homestead

You should check out a tagged version of Homestead since the `master` branch may not always be stable. You can find the latest stable version on the [GitHub Release Page](https://github.com/laravel/homestead/releases). Alternatively, you may checkout the `release` branch which always contains the latest stable release:

`master` 브랜치는 개발중이라 안정적이지 않을 수도 있기 때문에, 태그를 지정한 버전을 체크아웃 해야합니다. 안정적인 버전은 [GitHub 릴리즈 페이지](https://github.com/laravel/homestead/releases)에서 찾으실 수 있습니다. 또는 항상 최신 안정 릴리즈가 포함 된 `release` 브랜치를 체크 아웃 할 수 있습니다.

    cd ~/Homestead

    git checkout release

Once you have cloned the Homestead repository, run the `bash init.sh` command from the Homestead directory to create the `Homestead.yaml` configuration file. The `Homestead.yaml` file will be placed in the Homestead directory:

홈스테드 저장소를 복제한 뒤에, 홈스테드 디렉토리에서 `bash init.sh` 명령어를 통해서 `Homestead.yaml` 설정 파일을 생성할 수 있습니다. 홈스테드 디렉토리에 `Homestead.yaml` 파일이 생성될 것입니다.

    // Mac / Linux...
    bash init.sh

    // Windows...
    init.bat

<a name="configuring-homestead"></a>
### Configuring Homestead
### 홈스테드 설정하기

#### Setting Your Provider
#### 프로바이더 설정하기

The `provider` key in your `Homestead.yaml` file indicates which Vagrant provider should be used: `virtualbox`, `vmware_fusion`, `vmware_workstation`, `parallels` or `hyperv`. You may set this to the provider you prefer:

`Homestead.yaml` 파일의 `provider` 키는 `virtualbox`, `vmware_fusion`, `vmware_workstation`, `parallels`와 `hyperv` 중 어느 것을 사용할 것인지를 알려줍니다. 여러분이 원하는대로 이 프로바이더를 설정할 수 있습니다.

    provider: virtualbox

#### Configuring Shared Folders
#### 공유폴더 설정하기

The `folders` property of the `Homestead.yaml` file lists all of the folders you wish to share with your Homestead environment. As files within these folders are changed, they will be kept in sync between your local machine and the Homestead environment. You may configure as many shared folders as necessary:

`Homestead.yaml` 파일의 `folders` 속성은 홈스테드 환경과 공유하고자 하는 모든 폴더가 나열되어 있습니다. 여러분의 로컬 머신과 홈스테드 환경사이에서 동기화된 이 폴더 안의 파일들이 변경되면  변경사항이 서로 유지됩니다. 필요한 만큼 공유 폴더를 설정하십시오:

    folders:
        - map: ~/code/project1
          to: /home/vagrant/project1

> {note} Windows users should not use the `~/` path syntax and instead should use the full path to their project, such as `C:\Users\user\Code\project1`.

> {note} Windows 사용자는 `~/`경로 구문을 사용하지 말고 대신 `C:\Users\user\Code\project1`와 같은 프로젝트의 전체 경로를 사용해야 합니다.


You should always map individual projects to their own folder mapping instead of mapping your entire `~/code` folder. When you map a folder the virtual machine must keep track of all disk IO for *every* file in the folder. This leads to performance issues if you have a large number of files in a folder.

`~/code` 폴더 전체를 매핑하는 대신 개별 프로젝트를 항상 자신의 폴더 매핑에 매핑해야합니다. 폴더를 매핑 할 때 가상머신은 폴더의 **모든** 파일에 대한 모든 디스크 IO를 추적해야합니다. 폴더에 많은 수의 파일이있는 경우 성능 문제가 발생합니다.

    folders:
        - map: ~/code/project1
          to: /home/vagrant/project1

        - map: ~/code/project2
          to: /home/vagrant/project2

> {note} You should never mount `.` (the current directory) when using Homestead. This causes Vagrant to not map the current folder to `/vagrant` and will break optional features and cause unexpected results while provisioning.

> {note} Homestead를 사용할 때는 `.` (현재 디렉토리)를 마운트하지 마십시오. 이로 인해 Vagrant는 현재 폴더를 `/vagrant`로 매핑하지 않으며 옵션 기능을 중단하고 프로비저닝하는 동안 예상치 못한 결과를 초래합니다.

To enable [NFS](https://www.vagrantup.com/docs/synced-folders/nfs.html), you only need to add a simple flag to your synced folder configuration:

[NFS](https://www.vagrantup.com/docs/synced-folders/nfs.html)를 사용하기 위해서는 동기화 폴더 설정에 플래그를 더해주면 됩니다.

    folders:
        - map: ~/code/project1
          to: /home/vagrant/project1
          type: "nfs"

> {note} When using NFS on Windows, you should consider installing the [vagrant-winnfsd](https://github.com/winnfsd/vagrant-winnfsd) plug-in. This plug-in will maintain the correct user / group permissions for files and directories within the Homestead box.

> {note} 윈도우에서 NFS를 사용할 때에는, [vagrant-winnfsd](https://github.com/winnfsd/vagrant-winnfsd) 플러그인을 설치해야만 합니다. 이 플러그인은 홈스테드 box안에서 파일과 디렉토리를 위한 올바른 사용자와 권한을 관리해줍니다.

You may also pass any options supported by Vagrant's [Synced Folders](https://www.vagrantup.com/docs/synced-folders/basic_usage.html) by listing them under the `options` key:

`options` 키 아래 Vagrant의 [동기화 폴더](https://www.vagrantup.com/docs/synced-folders/basic_usage.html)옵션을 나열식으로 전달할 수 있습니다.

    folders:
        - map: ~/code/project1
          to: /home/vagrant/project1
          type: "rsync"
          options:
              rsync__args: ["--verbose", "--archive", "--delete", "-zz"]
              rsync__exclude: ["node_modules"]

#### Configuring Nginx Sites
#### Nginx 사이트 설정하기

Not familiar with Nginx? No problem. The `sites` property allows you to easily map a "domain" to a folder on your Homestead environment. A sample site configuration is included in the `Homestead.yaml` file. Again, you may add as many sites to your Homestead environment as necessary. Homestead can serve as a convenient, virtualized environment for every Laravel project you are working on:

Nginx에 익숙하지 않으신가요? 문제없습니다. `sites` 속성을 통해 홈스테드 환경의 폴더와 "도메인"을 쉽게 매핑 할 수 있습니다. 사이트 샘플이 `Homestead.yaml` 파일에 포함되어 있습니다. 여기에서도 여러분의 홈스테드 환경에 필요한만큼 사이트를 추가할 수 있습니다. 홈스테드는 편리하게, 여러분이 작업을 수행하는 모든 라라벨 프로젝트를 위한 가상 환경을 제공합니다.

    sites:
        - map: homestead.test
          to: /home/vagrant/project1/public

If you change the `sites` property after provisioning the Homestead box, you should re-run `vagrant reload --provision`  to update the Nginx configuration on the virtual machine.

홈스테드 박스가 프로비저닝 된 이후에 `sites` 속성을 변경한다면, 가상머신의 Nginx 설정을 갱신하기 위해서 `vagrant reload --provision` 을 다시 실행시켜야만 합니다.

> {note} Homestead scripts are built to be as idempotent as possible. However, if you are experiencing issues while provisioning you should destroy and rebuild the machine via `vagrant destroy && vagrant up`.

> {note} Homestead 스크립트는 가능한 한 멱등성을 유지하도록 만들어졌습니다. 그러나 프로비저닝 중에 문제가 발생하는 경우, `vagrant destroy && vagrant up`를 통해 가상머신을 제거하고 재구성해야합니다.

<a name="hostname-resolution"></a>
#### Hostname Resolution
#### Hostname 해결

Homestead publishes hostnames over `mDNS` for automatic host resolution. If you set `hostname: homestead` in your `Homestead.yaml` file, the host will be available at `homestead.local`. MacOS, iOS, and Linux desktop distributions include `mDNS` support by default. Windows requires installing [Bonjour Print Services for Windows](https://support.apple.com/kb/DL999?viewlocale=en_US&locale=en_US).

Homestead는 자동 호스트 해결을 위해 `mDNS`를 통해 hostname을 발행합니다. `Homestead.yaml` 파일에 `hostname:homestead`를 설정하면 `homestead.local`로 호스트를 사용할 수 있습니다. MacOS, iOS 및 Linux 데스크탑 배포판에는 기본적으로 `mDNS`지원이 포함되어 있습니다. Windows에서 [Windows 용 Bonjour Print Services](https://support.apple.com/kb/DL999?viewlocale=en_US&locale=ko_KR)를 설치해야합니다.

Using automatic hostnames works best for "per project" installations of Homestead. If you host multiple sites on a single Homestead instance, you may add the "domains" for your web sites to the `hosts` file on your machine. The `hosts` file will redirect requests for your Homestead sites into your Homestead machine. On Mac and Linux, this file is located at `/etc/hosts`. On Windows, it is located at `C:\Windows\System32\drivers\etc\hosts`. The lines you add to this file will look like the following:

자동 hostname 사용은 Homestead의 "프로젝트 별"설치에 가장 적합합니다. Homestead 인스턴스 하나에 여러 사이트를 호스팅하는 경우 웹 사이트의 "도메인"을 컴퓨터의 `hosts` 파일에 추가 할 수 있습니다. `hosts` 파일은 여러분의 홈스테드 사이트에 대한 요청을 홈스테드 머신으로 리다이렉트 시킬 것 입니다. 맥과 리눅스에서는 `/etc/hosts`, 윈도우에서는 `C:\Windows\System32\drivers\etc\hosts`에 파일이 있습니다. 이 파일에 추가하는 행은 다음과 같습니다.

    192.168.10.10  homestead.test

Make sure the IP address listed is the one set in your `Homestead.yaml` file. Once you have added the domain to your `hosts` file and launched the Vagrant box you will be able to access the site via your web browser:

입력한 IP 주소가 여러분의 `Homestead.yaml` 파일에 설정된 목록중 하나인지 확인하십시오. `hosts` 파일에 도메인을 추가 하고 Vagrant box를 구동하면 웹브라우저를 통해서 사이트에 액세스 할 수 있습니다.

    http://homestead.test

<a name="launching-the-vagrant-box"></a>
### Launching The Vagrant Box
### Vagrant Box 구동하기

Once you have edited the `Homestead.yaml` to your liking, run the `vagrant up` command from your Homestead directory. Vagrant will boot the virtual machine and automatically configure your shared folders and Nginx sites.

`Homestead.yaml` 파일 설정이 끝났다면, 홈스테드 디렉토리에서 `vagrant up` 명령어를 실행하십시오. Vagrant가 가상머신을 구동시키고 자동으로 공유폴더와 Nginx 사이트를 설정할 것입니다.

To destroy the machine, you may use the `vagrant destroy --force` command.

가상 머신을 제거하기 위해서는 `vagrant destroy --force` 명령어를 사용하면 됩니다.

<a name="per-project-installation"></a>
### Per Project Installation
### 프로젝트별 설치하기

Instead of installing Homestead globally and sharing the same Homestead box across all of your projects, you may instead configure a Homestead instance for each project you manage. Installing Homestead per project may be beneficial if you wish to ship a `Vagrantfile` with your project, allowing others working on the project to `vagrant up`.

홈스테드를 전역에 설치하고 전체 프로젝트에서 동일한 홈스테드 박스를 공유하는 대신, 홈스테드 인스턴스를 각각의 관리하는 프로젝트별로 설정할 수 있습니다. 프로젝트마다 홈스테드를 설치하는 것은 `Vagrantfile`을 프로젝트에서 구성하여, `vagrant up`을 할 때 다른 작업을 할 수 있도록 하는 장점이 있습니다.

To install Homestead directly into your project, require it using Composer:

홈스테드를 프로젝트에 직접 설치하려면 Composer-컴포저를 사용합니다.

    composer require laravel/homestead --dev

Once Homestead has been installed, use the `make` command to generate the `Vagrantfile` and `Homestead.yaml` file in your project root. The `make` command will automatically configure the `sites` and `folders` directives in the `Homestead.yaml` file.

홈스테드가 설치되고 나면, `make` 명령어를 사용하여 `Vagrantfile` 과 `Homestead.yaml` 파일을 프로젝트 루트 디렉토리에 생성합니다. `make` 명령어는 자동으로 `Homestead.yaml` 파일에서 `sites` 와 `folders` 옵션을 설정할 것입니다.

Mac / Linux:

    php vendor/bin/homestead make

Windows:

    vendor\\bin\\homestead make

Next, run the `vagrant up` command in your terminal and access your project at `http://homestead.test` in your browser. Remember, you will still need to add an `/etc/hosts` file entry for `homestead.test` or the domain of your choice if you are not using automatic [hostname resolution](#hostname-resolution).

다음으로 터미널에서 `vagrant up` 명령어를 실행하고 브라우저에서 `http://homestead.test` 프로젝트에 엑세스 하십시오. 자동화된 [hostname 해결](#hostname-resolution)을 사용하지 않는다면, `homestead.test` 또는 선택한 도메인을 `/etc/hosts` 파일에 추가 할 필요가 있다는 것을 기억하십시오.

<a name="installing-optional-features"></a>
### Installing Optional Features
### 옵션 기능 설치

Optional software is installed using the "features" setting in your Homestead configuration file. Most features can be enabled or disabled with a boolean value, while some features allow multiple configuration options:

선택적인 소프트웨어는 Homestead 설정 파일의 "features"설정을 사용하여 설치합니다. boolean 값으로 대부분의 기능을 활성화 또는 비활성화 할 수 있으며 일부 기능은 여러 옵션 설정이 가능합니다.

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
            version: 7
        - gearman: true
        - golang: true
        - grafana: true
        - influxdb: true
        - mariadb: true
        - minio: true
        - mongodb: true
        - mysql8: true
        - neo4j: true
        - ohmyzsh: true
        - openresty: true
        - pm2: true
        - python: true
        - rabbitmq: true
        - solr: true
        - webdriver: true

#### MariaDB
#### MariaDB

Enabling MariaDB will remove MySQL and install MariaDB. MariaDB serves as a drop-in replacement for MySQL, so you should still use the `mysql` database driver in your application's database configuration.

MariaDB를 활성화하면 MySQL이 제거되고 MariaDB가 설치됩니다. MariaDB는 MySQL의 상위호환 대체품이기 때문에 애플리케이션의 데이터베이스 구성에서 mysql 데이터베이스 드라이버를 사용해야합니다.

#### MongoDB
#### MongoDB

The default MongoDB installation will set the database username to `homestead` and the corresponding password to `secret`.

기본 MonogoDB 설치에는 `homestead` 를 계정 이름으로, `secret`을 패스워드로 지정됩니다.

#### Elasticsearch
#### Elasticsearch

You may specify a supported version of Elasticsearch, which may be a major version or an exact version number (major.minor.patch). The default installation will create a cluster named 'homestead'. You should never give Elasticsearch more than half of the operating system's memory, so make sure your Homestead machine has at least twice the Elasticsearch allocation.

Elasticsearch의 지원 버전을 지정할 수 있습니다. 이 버전은 메이저 버전 또는 (major.minor.patch) 형태의 정확한 버전을 지정하면 됩니다. 기본 설치는 'homestead'라는 이름으로 클러스터가 생성됩니다. Elasticsearch에 운영체제 메모리의 절반 이상을 할당하면 안됩니다. 따라서 홈스테드 머신에 Elasticsearch에 할당한 것의 두배 이상의 메모리가 있는지 확인하십시오.

> {tip} Check out the [Elasticsearch documentation](https://www.elastic.co/guide/en/elasticsearch/reference/current) to learn how to customize your configuration.

> {tip} 관련 설정을 변경하려면 [Elasticsearch 매뉴얼](https://www.elastic.co/guide/en/elasticsearch/reference/current)을 참고하십시오.

#### Neo4j
#### Neo4j

The default Neo4j installation will set the database username to `homestead` and corresponding password to `secret`. To access the Neo4j browser, visit `http://homestead.test:7474` via your web browser. The ports `7687` (Bolt), `7474` (HTTP), and `7473` (HTTPS) are ready to serve requests from the Neo4j client.

기본적인 Neo4j 설치에는 `homestead` 를 계정 이름으로 `secret`을 패스워드로 지정됩니다. Neo4j에 엑세스 하기 위해서는, 브라우저에서 `http://homestead.test:7474` 를 통해서 접근하면 됩니다. 포트 `7687` (Bolt), `7474` (HTTP), `7473` (HTTPS) 를 통해서 Neo4j 에 접근할 수 있습니다.

<a name="aliases"></a>
### Aliases
### 별칭 설정

You may add Bash aliases to your Homestead machine by modifying the `aliases` file within your Homestead directory:

홈스테드 디렉토리에 있는 `aliases` 파일을 수정하여 홈스테드 머신에 Bash 별칭을 추가할 수 있습니다.

    alias c='clear'
    alias ..='cd ..'

After you have updated the `aliases` file, you should re-provision the Homestead machine using the `vagrant reload --provision` command. This will ensure that your new aliases are available on the machine.

`aliases` 파일을 수정한 뒤에는 `vagrant reload --provision` 명령어를 사용하여 홈스테드 머신을 다시 프로비저닝 하십시오. 이렇게 하면 머신에서 새로운 별칭이 사용가능해집니다.

<a name="daily-usage"></a>
## Daily Usage
## 사용 방법

<a name="accessing-homestead-globally"></a>
### Accessing Homestead Globally
### 어디에서나 홈스테드 엑세스하기

Sometimes you may want to `vagrant up` your Homestead machine from anywhere on your filesystem. You can do this on Mac / Linux systems by adding a Bash function to your Bash profile. On Windows, you may accomplish this by adding a "batch" file to your `PATH`. These scripts will allow you to run any Vagrant command from anywhere on your system and will automatically point that command to your Homestead installation:

때로는 파일시스템의 어디에서나 여러분의 홈스테드 머신에 `vagrant up`을 실행하고자 할 수 있습니다. 이렇게 하기 위해서는 맥 / 리눅스 시스템에서 Bash 함수를 Bash profile 에 추가하여 구현할 수 있습니다. 윈도우에서는 `PATH`에 "배치 파일을 추가하여 수행할 수 있습니다. 이 스크립트는 여러분이 시스템 어디에서나 Vagrant 명령어를 실행할 수 있게 하고 자동으로 홈스테드 설치를 대상으로 하게 할 수 있습니다.

#### Mac / Linux
#### 맥 / 리눅스

    function homestead() {
        ( cd ~/Homestead && vagrant $* )
    }

Make sure to tweak the `~/Homestead` path in the function to the location of your actual Homestead installation. Once the function is installed, you may run commands like `homestead up` or `homestead ssh` from anywhere on your system.

함수에서 설정한 `~/Homestead` 경로가 여러분의 실제 홈스테드 설치 위치인지 확인하십시오. 함수를 설정하면 시스템의 어느 곳에서나 `homestead up` 또는 `homestead ssh`와 같은 명령어를 실행할 수 있습니다.

#### Windows
#### 윈도우

Create a `homestead.bat` batch file anywhere on your machine with the following contents:

다음 내용을 가지는 `homestead.bat` 배치 파일을 원하는 곳에다가 생성합니다.

    @echo off

    set cwd=%cd%
    set homesteadVagrant=C:\Homestead

    cd /d %homesteadVagrant% && vagrant %*
    cd /d %cwd%

    set cwd=
    set homesteadVagrant=

Make sure to tweak the example `C:\Homestead` path in the script to the actual location of your Homestead installation. After creating the file, add the file location to your `PATH`. You may then run commands like `homestead up` or `homestead ssh` from anywhere on your system.

스크립트에서 설정한 `C:\Homestead` 경로가 여러분의 실제 홈스테드 설치 위치인지 확인하십시오 파일을 생성한뒤에, 파일의 경로를 `PATH` 에 추가합니다. 시스템의 어떤 경로에서라도 `homestead up` 또는 `homestead ssh` 명령어를 실행할 수 있습니다.

<a name="connecting-via-ssh"></a>
### Connecting Via SSH
### SSH를 통해서 연결하기

You can SSH into your virtual machine by issuing the `vagrant ssh` terminal command from your Homestead directory.

홈스테드 디렉토리에서 `vagrant ssh` 명령어를 실행하여 SSH를 통해서 홈스테드 환경에 접속할 수 있습니다.

But, since you will probably need to SSH into your Homestead machine frequently, consider adding the "function" described above to your host machine to quickly SSH into the Homestead box.

하지만, 홈스테드 머신에 자주 접속할 것이기 때문에 손쉽게 홈스테드 머신에 SSH로 접속하기 위해서 앞서 이야기한 "함수"을 추가하는 것을 고려해 보십시오.

<a name="connecting-to-databases"></a>
### Connecting To Databases
### 데이터베이스 접속하기

A `homestead` database is configured for both MySQL and PostgreSQL out of the box. For even more convenience, Laravel's `.env` file configures the framework to use this database out of the box.

별다른 설정 없이도 `homestead` 데이터베이스는 MySQL과 PostgreSQL 가 설정되어 있습니다. 보다 편리하게 하기 위해 라라벨의 `.env` 파일의 설정이 프로임워크에서 별다른 작업 없이도 이 데이터베이스를 사용하도록 설정되어 있습니다.

To connect to your MySQL or PostgreSQL database from your host machine's database client, you should connect to `127.0.0.1` and port `33060` (MySQL) or `54320` (PostgreSQL). The username and password for both databases is `homestead` / `secret`.

데이터베이스 클라이언트를 통해서 MySQL 또는 PostgreSQL 데이터베이스에 접속하려면 `127.0.0.1` 의 `33060` (MySQL) 또는 `54320` (PostgreSQL) 포트에 연결하면 됩니다. 데이터베이스 사용자 이름과 암호는 모두 `homestead` / `secret` 입니다.

> {note} You should only use these non-standard ports when connecting to the databases from your host machine. You will use the default 3306 and 5432 ports in your Laravel database configuration file since Laravel is running _within_ the virtual machine.

> {note} 데이터베이스에 접속할 때는 이러한 표준이 아닌 포트를 사용해야 합니다. 라라벨이 가상 머신 _안에서_ 동작하고 있기 때문에 기본적인 3306 과 5432 포트는 라라벨 데이터베이스 설정 파일 안에서 사용할 수 있습니다.

<a name="database-backups"></a>
### Database Backups
### 데이터베이스 백업하기

Homestead can automatically backup your database when your Vagrant box is destroyed. To utilize this feature, you must be using Vagrant 2.1.0 or greater. Or, if you are using an older version of Vagrant, you must install the `vagrant-triggers` plug-in. To enable automatic database backups, add the following line to your `Homestead.yaml` file:

홈스테드는 Vagrant box 가 종료될 때 자동으로 데이터베이스를 백업합니다. 이 기능을 사용하기 위해서는 Vagrant 2.1.0 이상 버전을 사용해야 합니다. 그 이전버전이라면, `vagrant-triggers` 플러그인을 설치해야 합니다. 자동으로 데이터베이스 백업을 활성화 하려면, 다음의 설정을 `Homestead.yaml` 파일에 추가하면 됩니다.

    backup: true

Once configured, Homestead will export your databases to `mysql_backup` and `postgres_backup` directories when the `vagrant destroy` command is executed. These directories can be found in the folder where you cloned Homestead or in the root of your project if you are using the [per project installation](#per-project-installation) method.

설정을 하고나면, 홈스테드는 `vagrant destroy` 명령이 실행될 때 데이터베이스를 `mysql_backup` 과 `postgres_backup` 디렉토리로 백업을 내보냅니다. 이 디렉토리는 [프로젝트 별 설치](#per-project-installation)방법을 사용하는 경우에는 루트 디렉토리에, 홈스테드를 클론(복제) 한 경우에는 해당 디렉토리에서 찾을 수 있습니다.

<a name="database-snapshots"></a>
### Database Snapshots
### 데이터베이스 스냅샷

Homestead supports freezing the state of MySQL and MariaDB databases and branching between them using [Logical MySQL Manager](https://github.com/Lullabot/lmm). For example, imagine working on a site with a multi-gigabyte database. You can import the database and take a snapshot. After doing some work and creating some test content locally, you may quickly restore back to the original state.

Homestead는 MySQL 및 MariaDB 데이터베이스의 상태를 고정시키고 [Logical MySQL Manager](https://github.com/Lullabot/lmm)를 사용하여 이들 사이를 분기하는 것을 지원합니다. 예를 들어, 수 기가 바이트 데이터베이스가 있는 사이트에서 작업하는 것을 상상해보십시오. 데이터베이스를 가져 와서 스냅 샷을 만들 수 있습니다. 일부 작업을 수행하고 일부 테스트 컨텐츠를 로컬로 작성한 후 신속하게 원래 상태로 복원 할 수 있습니다.

Under the hood, LMM uses LVM's thin snapshot functionality with copy-on-write support. In practice, this means that changing a single row in a table will only cause the changes you made to be written to disk, saving significant time and disk space during restores.

내부적으로 LMM은 LVM의 씬-thin 스냅 샷 기능을 사용하여 쓰기 중 복사를 지원합니다. 실제로 이는 테이블의 단일 행을 변경하면 디스크에 변경 사항 만 기록하여 복원 중 상당한 시간과 디스크 공간을 절약 할 수 있음을 의미합니다.

Since `lmm` interacts with LVM, it must be run as `root`. To see all available commands, run `sudo lmm` inside your Vagrant box. A common workflow looks like the following:

`lmm`은 LVM과 상호 작용하기 때문에 `root`로 실행되어야 합니다. 사용 가능한 모든 명령을 보려면, 당신의 Vagrant 박스 안에 `sudo lmm`을 실행하십시오. 일반적인 작업순서는 다음과 같습니다.

1. Import a database into the default `master` lmm branch.
1. Save a snapshot of the unchanged database using `sudo lmm branch prod-YYYY-MM-DD`.
1. Modify the database.
1. Run `sudo lmm merge prod-YYYY-MM-DD` to undo all changes.
1. Run `sudo lmm delete <branch>` to delete unneeded branches.

1. 디폴트 `master` lmm 브랜치로 데이터베이스를 임포트하십시오.
1. `sudo lmm branch prod-YYYY-MM-DD`를 사용하여 변경되지 않은 데이터베이스의 스냅 샷을 저장하십시오.
1. 데이터베이스를 수정하십시오.
1. `sudo lmm merge prod -YYYY-MM-DD`를 실행하여 모든 변경 사항을 취소하십시오.
1. `sudo lmm delete <branch>`를 실행하여 필요없는 브랜치를 삭제하십시오. 

<a name="adding-additional-sites"></a>
### Adding Additional Sites
### 추가적인 사이트 지정하기

Once your Homestead environment is provisioned and running, you may want to add additional Nginx sites for your Laravel applications. You can run as many Laravel installations as you wish on a single Homestead environment. To add an additional site, add the site to your `Homestead.yaml` file:

홈스테드 환경이 준비되어 구성되고 난 뒤에 라라벨 애플리케이션에 추가적인 Nginx 사이트를 구성하기를 원할 수도 있습니다. 하나의 홈스테드 환경 안에서 여러개의 라라벨을 설치하여 작동 시킬수도 있습니다. 추가적인 사이트를 지정하기 위해서, `Homestead.yaml` 파일에 사이트를 추가하십시오:

    sites:
        - map: homestead.test
          to: /home/vagrant/project1/public
        - map: another.test
          to: /home/vagrant/project2/public

If Vagrant is not automatically managing your "hosts" file, you may need to add the new site to that file as well:

Vagrant 가 자동으로 "hosts" 파일을 관리하지 않는다면, 직접 다음의 사이트를 호스트 파일에 추가해야합니다.

    192.168.10.10  homestead.test
    192.168.10.10  another.test

Once the site has been added, run the `vagrant reload --provision` command from your Homestead directory.

사이트가 추가되면, 홈스테드 디렉토리에서 `vagrant reload --provision` 명령어를 실행하십시오.

<a name="site-types"></a>
#### Site Types
#### 사이트 타입

Homestead supports several types of sites which allow you to easily run projects that are not based on Laravel. For example, we may easily add a Symfony application to Homestead using the `symfony2` site type:

홈스테드는 라라벨이 아닌 프로젝트를 손쉽게 구동할 수 있는 몇가지 사이트 타입을 지원합니다. 예를 들자면, `symfony2` 사이트 타입은 Synfony 애플리케이션을 홈스테드에서 추가할 수 있도록 해줍니다.

    sites:
        - map: symfony2.test
          to: /home/vagrant/my-symfony-project/web
          type: "symfony2"

The available site types are: `apache`, `apigility`, `expressive`, `laravel` (the default), `proxy`, `silverstripe`, `statamic`, `symfony2`, `symfony4`, and `zf`.

사용가능한 사이트 타입에는 `apache`, `apigility`, `expressive`, `laravel` (기본값), `proxy`, `silverstripe`, `statamic`, `symfony2`, `symfony4`, 그리고 `zf`가 있습니다.

<a name="site-parameters"></a>
#### Site Parameters
#### 사이트 파라미터

You may add additional Nginx `fastcgi_param` values to your site via the `params` site directive. For example, we'll add a `FOO` parameter with a value of `BAR`:

추가적인 Nginx `fastcgi_param` 값을 사이트에 설정하려면 `params` 사이트 지시어를 사용하면 됩니다. 예를 들어 `BAR` 라는 값을 가지는 `FOO` 파라미터를 추가해보겠습니다.

    sites:
        - map: homestead.test
          to: /home/vagrant/project1/public
          params:
              - key: FOO
                value: BAR

<a name="environment-variables"></a>
### Environment Variables
### 시스템 ENV 환경 변수

You can set global environment variables by adding them to your `Homestead.yaml` file:

`Homestead.yaml` 파일에 시스템 ENV 변수를 지정할 수 있습니다.

    variables:
        - key: APP_ENV
          value: local
        - key: FOO
          value: bar

After updating the `Homestead.yaml`, be sure to re-provision the machine by running `vagrant reload --provision`. This will update the PHP-FPM configuration for all of the installed PHP versions and also update the environment for the `vagrant` user.

`Homestead.yaml` 파일을 수정한 뒤에는 `vagrant reload --provision` 를 실행하여 머신을 새롭게 프로비저닝 하십시오. 이 명령어는 설치된 PHP버전에 맞는 PHP-FPM 설정을 업데이트하고 `vagrant` 사용자를 위한 구동환경을 업데이트 합니다.

<a name="configuring-cron-schedules"></a>
### Configuring Cron Schedules
### Cron 스케줄링 설정하기

Laravel provides a convenient way to [schedule Cron jobs](/docs/{{version}}/scheduling) by scheduling a single `schedule:run` Artisan command to be run every minute. The `schedule:run` command will examine the job schedule defined in your `App\Console\Kernel` class to determine which jobs should be run.

라라벨에서는 단 한번 `schedule:run` 아티즌 명령어를 실행하여 매분마다 [Cron 작업 스케줄링](/docs/{{version}}/scheduling)을 할 수 있도록 하는 편리한 방법을 제공합니다. `schedule:run` 명령어는 `App\Console\Kernel` 클래스에 정의되어 있는 작업 스케줄이 실행되어야 하는지 검사할 것입니다.

If you would like the `schedule:run` command to be run for a Homestead site, you may set the `schedule` option to `true` when defining the site:

홈스테드의 사이트에서 `schedule:run` 명령을 실행하려면 사이트를 정의 할 때 `schedule` 옵션을 `true` 로 설정하십시오.

    sites:
        - map: homestead.test
          to: /home/vagrant/project1/public
          schedule: true

The Cron job for the site will be defined in the `/etc/cron.d` folder of the virtual machine.

사이트를 위한 Cron 작업은 가상 머신의 `/etc/cron.d` 폴더에 정의됩니다.

<a name="configuring-mailhog"></a>
### Configuring Mailhog
### Mailhog 설정하기

Mailhog allows you to easily catch your outgoing email and examine it without actually sending the mail to its recipients. To get started, update your `.env` file to use the following mail settings:

Mailhog를 사용하면 실제로 메일을 받는 사람에게 메일을 보내지 않고도 송신하는 메일을 손쉽게 찾아보고 확인 할 수 있습니다. 이를 시작하려면 `.env` 파일을 수정하여 다음의 메일 설정을 사용하십시오:

    MAIL_DRIVER=smtp
    MAIL_HOST=localhost
    MAIL_PORT=1025
    MAIL_USERNAME=null
    MAIL_PASSWORD=null
    MAIL_ENCRYPTION=null

Once Mailhog has been configured, you may access the Mailhog dashboard at `http://localhost:8025`.

Mailhog이 구성되면 `http://localhost:8025` 로 Mailhog 대시 보드에 액세스 할 수 있습니다.

<a name="configuring-minio"></a>
### Configuring Minio
### Minio 설정하기

Minio is an open source object storage server with an Amazon S3 compatible API. To install Minio, update your `Homestead.yaml` file with the following configuration option:

Minio 는 Amazon S3 API와 호환되는 오픈소스 객체 스토리지 서버입니다. Minio를 설치하려면 `Homestead.yaml` 파일에 다음의 설정 내용을 추가하십시오:

    minio: true

By default, Minio is available on port 9600. You may access the Minio control panel by visiting `http://homestead:9600/`. The default access key is `homestead`, while the default secret key is `secretkey`. When accessing Minio, you should always use region `us-east-1`.

기본적으로 Minio는 9600포트를 사용합니다. `http://homestead:9600/` 을 방문하여 Minio 제어판에 엑세스 할 수 있습니다. 기본 엑세스 키는 `homestead` 이고, 기본 비밀 키는 `secretkey` 입니다. Minio에 엑세스 할 때는 항상 `us-east-1` 리전을 사용해야합니다.

In order to use Minio you will need to adjust the S3 disk configuration in your `config/filesystems.php` configuration file. You will need to add the `use_path_style_endpoint` option to the disk configuration, as well as change the `url` key to `endpoint`:

Minio를 사용하기 위해서는 설정 파일 `config/filesystems.php` 에서 S3 디스크 설정을 수정해야합니다. 디스크 구성에 `use_path_style_endpoint` 옵션을 추가하고 `url` 키를 `endpoint` 로 변경해야합니다.

    's3' => [
        'driver' => 's3',
        'key' => env('AWS_ACCESS_KEY_ID'),
        'secret' => env('AWS_SECRET_ACCESS_KEY'),
        'region' => env('AWS_DEFAULT_REGION'),
        'bucket' => env('AWS_BUCKET'),
        'endpoint' => env('AWS_URL'),
        'use_path_style_endpoint' => true
    ]

Finally, ensure your `.env` file has the following options:

마지막으로 `.env` 파일에 다음 옵션이 있는지 확인하십시오 :

    AWS_ACCESS_KEY_ID=homestead
    AWS_SECRET_ACCESS_KEY=secretkey
    AWS_DEFAULT_REGION=us-east-1
    AWS_URL=http://homestead:9600

To provision buckets, add a `buckets` directive to your Homestead configuration file:

버킷을 프로비저닝하려면 Homestead 환경설정 파일에 `buckets` 지시문을 추가하십시오.

    buckets:
        - name: your-bucket
          policy: public
        - name: your-private-bucket
          policy: none

Supported `policy` values include: `none`, `download`, `upload`, and `public`.

지원되는`policy` 값은`none`,`download`,`upload`,`public`입니다.

<a name="ports"></a>
### Ports
### 포트지정하기

By default, the following ports are forwarded to your Homestead environment:

기본적으로 다음의 포트들이 홈스테드 환경에서 포워딩 설정 되어 있습니다.


- **SSH:** 2222 &rarr; Forwards To 22
- **ngrok UI:** 4040 &rarr; Forwards To 4040
- **HTTP:** 8000 &rarr; Forwards To 80
- **HTTPS:** 44300 &rarr; Forwards To 443
- **MySQL:** 33060 &rarr; Forwards To 3306
- **PostgreSQL:** 54320 &rarr; Forwards To 5432
- **MongoDB:** 27017 &rarr; Forwards To 27017
- **Mailhog:** 8025 &rarr; Forwards To 8025
- **Minio:** 9600 &rarr; Forwards To 9600


#### Forwarding Additional Ports
#### 추가적인 포트 포워딩하기

If you wish, you may forward additional ports to the Vagrant box, as well as specify their protocol:

원한다면 포트뿐만 아니라 프로토콜을 추가로 Vagrant Box에 포워딩하도록 지정 할 수 있습니다.

    ports:
        - send: 50000
          to: 5000
        - send: 7777
          to: 777
          protocol: udp

<a name="sharing-your-environment"></a>
### Sharing Your Environment
### 환경 공유하기

Sometimes you may wish to share what you're currently working on with coworkers or a client. Vagrant has a built-in way to support this via `vagrant share`; however, this will not work if you have multiple sites configured in your `Homestead.yaml` file.

가끔씩, 현재 작업하고 있는 환경을 동료들이나, 다른 사람들과 공유하고 싶을 수도 있습니다. Vagrant는 이를 위해서 `vagrant share`를 통해서 환경을 공유할 수 있는 내장 기능이 지원됩니다; 그렇지만, `Homestead.yaml` 파일에 여러개의 사이트가 설정된 경우에는 동작이 원하는대로 작동하지 않습니다.

To solve this problem, Homestead includes its own `share` command. To get started, SSH into your Homestead machine via `vagrant ssh` and run `share homestead.test`. This will share the `homestead.test` site from your `Homestead.yaml` configuration file. You may substitute any of your other configured sites for `homestead.test`:

이 문제를 극복하기 위해서, 홈스테드에 `share` 명령어가 포함되었습니다. 이렇게 하기 위해서 `vagrant ssh` 명령어를 통해서 홈스테드 머신에 SSH 접속을 한 다음 `share homestead.test` 을 실행하십시오. 이렇게 하면 여러분의 `Homestead.yaml` 설정에 있는 `homestead.test` 사이트와 다른 사이트도 공유할 수 있습니다.

    share homestead.test

After running the command, you will see an Ngrok screen appear which contains the activity log and the publicly accessible URLs for the shared site. If you would like to specify a custom region, subdomain, or other Ngrok runtime option, you may add them to your `share` command:

이 명령어를 실행하면, 여러분은 Ngrok 스크린에서 activity log와 함께 공유한 사이트에 접속할 수 있는 public URL을 확인할 수 있습니다. 커스텀 리전, 서브도메인, Ngrok 실행 옵션을 지정하고자 한다면 `share` 명령어에 이를 추가하면됩니다.

    share homestead.test -region=eu -subdomain=laravel

> {note} Remember, Vagrant is inherently insecure and you are exposing your virtual machine to the Internet when running the `share` command.

> {note} 유념할점은, `share` 명령어를 실행중일 때는 가상머신이 인터넷에 노출되고 보안에 노출될 수 있다는 점입니다.

<a name="multiple-php-versions"></a>
### Multiple PHP Versions
### 여러 버전의 PHP 사용하기

Homestead 6 introduced support for multiple versions of PHP on the same virtual machine. You may specify which version of PHP to use for a given site within your `Homestead.yaml` file. The available PHP versions are: "5.6", "7.0", "7.1", "7.2" and "7.3" (the default):

홈스테드 6부터 동일한 가상 머신에서 여러버전의 PHP를 사용할 수 있습니다. 해당 사이트에서 어떤 버전의 PHP를 사용할지 `Homestead.yaml`에서 지정할 수 있습니다. 사용가능한 PHP 버전은 "5.6", "7.0", "7.1", "7.2", "7.3"(기본) 입니다.

    sites:
        - map: homestead.test
          to: /home/vagrant/project1/public
          php: "7.1"

In addition, you may use any of the supported PHP versions via the CLI:

추가적으로 CLI를 통해서 지원되는 PHP 버전들은 다음과 같이 사용할 수 있습니다.

    php5.6 artisan list
    php7.0 artisan list
    php7.1 artisan list
    php7.2 artisan list
    php7.3 artisan list

<a name="web-servers"></a>
### Web Servers
### 웹서버

Homestead uses the Nginx web server by default. However, it can install Apache if `apache` is specified as a site type. While both web servers can be installed at the same time, they cannot both be *running* at the same time. The `flip` shell command is available to ease the process of switching between web servers. The `flip` command automatically determines which web server is running, shuts it off, and then starts the other server. To use this command, SSH into your Homestead machine and run the command in your terminal:

홈스테드는 기본적으로 Nginx를 웹서버로 사용합니다. 그렇지만, site 타입에 `apache`를 지정해서 아파치를 설치할 수 있습니다. 두 웹서버를 모두 설치할 수 있지만, 동시에 실행할 수는 없습니다. `flip` 쉘 명령어를 사용하여 웹서버를 쉽게 전환할 수 있습니다. `flip` 쉘 명령어는 자동으로 실행되는 웹서버를 멈추고 다른 서버를 구동합니다. 이 명령어을 사용하려면 홈스테드에 SSH 접속을 하고 터미널에 명령어를 실행하면 됩니다.

    flip

<a name="mail"></a>
### Mail
### 이메일

Homestead includes the Postfix mail transfer agent, which is listening on port `1025` by default. So, you may instruct your application to use the `smtp` mail driver on `localhost` port `1025`. Then, all sent mail will be handled by Postfix and caught by Mailhog. To view your sent emails, open [http://localhost:8025](http://localhost:8025) in your web browser.

홈스테드는 기본적으로 `1025` 포트를 사용하는 Postfix 메일 전송 에이전트를 포함하고 있습니다. 따라서 애플리케이션에서 `smtp` 메일드라이버에 `localhost` 와 포트번호 `1025` 를 설정할 수 있습니다. 이렇게 하면 모든 메일은 Postfix로 처리되고 Mailhog 에 기록됩니다. 전달된 이메일을 확인하려면 브라우저에서 [http://localhost:8025](http://localhost:8025)를 열어보십시오.

<a name="debugging-and-profiling"></a>
## Debugging & Profiling
## 디버깅과 프로파일링

<a name="debugging-web-requests"></a>
### Debugging Web Requests With Xdebug
### Xdebug로 웹 요청 디버깅

Homestead includes support for step debugging using [Xdebug](https://xdebug.org). For example, you can load a web page from a browser, and PHP will connect to your IDE to allow inspection and modification of the running code.

Homestead는 [Xdebug](https://xdebug.org)를 사용하여 단계별 디버깅을 지원합니다. 예를 들어 브라우저에서 웹 페이지를 로드 할 수 있으며, PHP는 IDE에 연결하여 실행중인 코드를 검사하고 수정할 수 있습니다.

By default Xdebug is already running and ready to accept connections. If you need to enable Xdebug on the CLI run the `sudo phpenmod xdebug` command within your Vagrant box. Next, follow your IDE's instructions to enable debugging. Finally, configure your browser to trigger Xdebug with an extension or [bookmarklet](https://www.jetbrains.com/phpstorm/marklets/).

기본적으로 Xdebug는 이미 실행 중이며 연결을 수락 할 준비가 되어 있습니다. CLI에서 Xdebug를 활성화하려면 Vagrant 박스 안에서 `sudo phpenmod xdebug` 커맨드를 실행하십시오. 그런 다음 IDE의 지침에 따라 디버깅을 활성화합니다. 마지막으로 Xdebug와 함께 확장 프로그램이나 [bookmarklet](https://www.jetbrains.com/phpstorm/marklets/)이 실행되도록 브라우저를 구성합니다.

> {note} Xdebug causes PHP to run significantly slower. To disable Xdebug, run `sudo phpdismod xdebug` within your Vagrant box and restart the FPM service.

> {note} Xdebug를 사용하면 PHP가 상당히 느리게 실행됩니다. Xdebug를 비활성화하려면, Vagrant 상자에서 `sudo phpdismod xdebug`를 실행하고 FPM 서비스를 다시 시작하십시오.

<a name="debugging-cli-applications"></a>
### Debugging CLI Applications
### CLI 애플리케이션 디버깅

To debug a PHP CLI application, use the `xphp` shell alias inside your Vagrant box:

PHP CLI 애플리케이션을 디버깅하려면, Vagrant 박스 안에 `xphp` 쉘 별칭을 사용하십시오.

    xphp path/to/script

#### Autostarting Xdebug
#### Xdebug 자동시작

When debugging functional tests that make requests to the web server, it is easier to autostart debugging rather than modifying tests to pass through a custom header or cookie to trigger debugging. To force Xdebug to start automatically, modify `/etc/php/7.#/fpm/conf.d/20-xdebug.ini` inside your Vagrant box and add the following configuration:

웹 서버에 요청하는 기능 테스트를 디버깅 할 때 커스텀 헤더 또는 쿠키를 통해서 디버깅을 실행하도록 테스트를 수정하는 대신 디버깅을 자동 시작하는 것이 더 쉽습니다. Xdebug를 자동으로 시작하려면 Vagrant 박스에서 `/etc/php/7./fpm/conf.d/20-xdebug.ini`을 수정하고 다음 설정을 추가하십시오.

    ; If Homestead.yml contains a different subnet for the IP address, this address may be different...
    xdebug.remote_host = 192.168.10.1
    xdebug.remote_autostart = 1

<a name="profiling-applications-with-blackfire"></a>
### Profiling Applications with Blackfire
### Blackfire로 애플리케이션 프로파일링

[Blackfire](https://blackfire.io/docs/introduction) is a SaaS service for profiling web requests and CLI applications and writing performance assertions. It offers an interactive user interface which displays profile data in call-graphs and timelines. It is built for use in development, staging, and production, with no overhead for end users. It provides performance, quality, and security checks on code and `php.ini` configuration settings.

[Blackfire](https://blackfire.io/docs/introduction)는 웹 요청 및 CLI 애플리케이션 및 쓰기 성능 assertion을 프로파일링하는 SaaS 서비스입니다. 콜 그래프 및 타임 라인에 프로필 데이터를 표시하는 대화 형 사용자 인터페이스를 제공합니다. 이는 개발, 스테이징 및 프로덕션에서 사용하기 위해 제작되었으며 최종 사용자에게는 오버 헤드가 없습니다. 코드와 `php.ini` 설정 값에 대한 성능, 품질, 보안 검사를 제공합니다.

The [Blackfire Player](https://blackfire.io/docs/player/index) is an open-source Web Crawling, Web Testing and Web Scraping application which can work jointly with Blackfire in order to script profiling scenarios.

[Blackfire Player](https://blackfire.io/docs/player/index)는 프로파일링 시나리오를 스크립팅하기 위해 Blackfire와 공동으로 작업 할 수있는 오픈 소스 웹 크롤링, 웹 테스트 및 웹 스크래핑 애플리케이션입니다.

To enable Blackfire, use the "features" setting in your Homestead configuration file:

Blackfire를 사용하려면 Homestead 설정 파일에서 "features"설정을 사용하십시오.

    features:
        - blackfire:
            server_id: "server_id"
            server_token: "server_value"
            client_id: "client_id"
            client_token: "client_value"

Blackfire server credentials and client credentials [require a user account](https://blackfire.io/signup). Blackfire offers various options to profile an application, including a CLI tool and browser extension. Please [review the Blackfire documentation for more details](https://blackfire.io/docs/cookbooks/index).

Blackfire 서버 인증 및 클라이언트 인증은 [사용자 계정이 필요합니다](https://blackfire.io/signup). Blackfire는 CLI 도구 및 브라우저 확장을 포함하여 애플리케이션을 프로파일링하기위한 다양한 옵션을 제공합니다. [자세한 내용은 Blackfire 설명서를 검토하십시오](https://blackfire.io/docs/cookbooks/index).

### Profiling PHP Performance Using XHGui
### XHGui를 사용하여 PHP 성능 프로파일링

[XHGui](https://www.github.com/perftools/xhgui) is a user interface for exploring the performance of your PHP applications. To enable XHGui, add `xhgui: 'true'` to your site configuration:

[XHGui](https://www.github.com/perftools/xhgui)는 PHP 애플리케이션의 성능을 탐색 할 수있는 사용자 인터페이스입니다. XHGui를 활성화하려면 사이트 설정에 `xhgui:'true'`를 추가하십시오.

    sites:
        -
            map: your-site.test
            to: /home/vagrant/your-site/public
            type: "apache"
            xhgui: 'true'

If the site already exists, make sure to run `vagrant provision` after updating your configuration.

사이트가 이미 존재하는 경우 설정을 업데이트 한 후 `vagrant provision`을 실행하십시오.

To profile a web request, add `xhgui=on` as a query parameter to a request. XHGui will automatically attach a cookie to the response so that subsequent requests do not need the query string value. You may view your application profile results by browsing to `http://your-site.test/xhgui`.

웹 요청을 프로파일링하려면 쿼리 파라미터로 `xhgui=on`을 요청에 추가하십시오. XHGui는 응답에 쿠키를 자동으로 첨부하여 이후 요청에는 쿼리 문자열 값이 필요하지 않습니다. `http://your-site.test/xhgui`로 이동하여 애플리케이션 프로필 결과를 볼 수 있습니다.

To profile a CLI request using XHGui, prefix the command with `XHGUI=on`:

XHGui를 사용하여 CLI 요청을 프로파일링하려면 명령 앞에 `XHGUI=on`을 추가하십시오 :

    XHGUI=on path/to/script

CLI profile results may be viewed in the same way as web profile results.

CLI 프로파일 결과는 웹 프로파일 결과와 동일한 방법으로 볼 수 있습니다.

Note that the act of profiling slows down script execution, and absolute times may be as much as twice as real-world requests. Therefore, always compare percentage improvements and not absolute numbers. Also, be aware the execution time includes any time spent paused in a debugger.

프로파일링을하면 스크립트 실행 속도가 느려지고 절대 시간은 실제 요청보다 두 배나 늘어 날 수 있습니다. 따라서 절대 값이 아닌 항상 개선율을 비교하십시오. 또한 실행 시간에는 디버거에서 일시 중지 한 시간이 포함됩니다.

Since performance profiles take up significant disk space, they are deleted automatically after a few days.

성능 프로파일은 상당한 디스크 공간을 차지하므로 며칠 후에 자동으로 삭제됩니다.

<a name="network-interfaces"></a>
## Network Interfaces
## 네트워크 인터페이스

The `networks` property of the `Homestead.yaml` configures network interfaces for your Homestead environment. You may configure as many interfaces as necessary:

`Homestead.yaml` 설정의 `networks` 속성은 홈스테드 환경의 네트워크 인터페이스 설정에 관련된 것입니다. 필요한 경우 여러개의 인터페이스를 설정할 수 있습니다.

    networks:
        - type: "private_network"
          ip: "192.168.10.20"

To enable a [bridged](https://www.vagrantup.com/docs/networking/public_network.html) interface, configure a `bridge` setting and change the network type to `public_network`:

[bridged](https://www.vagrantup.com/docs/networking/public_network.html) 인터페이스를 활성화 하기 위해서는, `bridge` 옵션을 설정하고 네트워크 타입을 `public_network`로 변경해야 합니다.

    networks:
        - type: "public_network"
          ip: "192.168.10.20"
          bridge: "en1: Wi-Fi (AirPort)"

To enable [DHCP](https://www.vagrantup.com/docs/networking/public_network.html), just remove the `ip` option from your configuration:

[DHCP](https://www.vagrantup.com/docs/networking/public_network.html)를 활성화하기 위해서는, 설정에서 간단하게 `ip` 옵션을 제거하면 됩니다.

    networks:
        - type: "public_network"
          bridge: "en1: Wi-Fi (AirPort)"

<a name="extending-homestead"></a>
## Extending Homestead
## 홈스테드 확장하기

You may extend Homestead using the `after.sh` script in the root of your Homestead directory. Within this file, you may add any shell commands that are necessary to properly configure and customize your virtual machine.

Homestead의 디렉토리 루트에있는 `after.sh` 스크립트를 사용하여 Homestead를 확장 할 수 있습니다. 이 파일에서 가상 머신을 올바르게 구성하고 커스터마이징 하는 데 필요한 쉘 명령을 추가 할 수 있습니다.

When customizing Homestead, Ubuntu may ask you if you would like to keep a package's original configuration or overwrite it with a new configuration file. To avoid this, you should use the following command when installing packages to avoid overwriting any configuration previously written by Homestead:

Homestead를 커스터마이징 할 때, 우분투는 패키지의 원래 환경설정을 유지하거나 새로운 환경설정 파일로 덮어 쓸 것인지 묻습니다. 이를 방지하려면 패키지를 설치할 때 이전에 Homestead에서 작성한 환경설정을 덮어 쓰지 않도록 다음 명령을 사용해야합니다.

    sudo apt-get -y \
        -o Dpkg::Options::="--force-confdef" \
        -o Dpkg::Options::="--force-confold" \
        install your-package

### User Customizations
### 개인 커스터마이징

When using Homestead in a team setting, you may want to tweak Homestead to better fit your personal development style. You may create a `user-customizations.sh` file in the root of your Homestead directory (The same directory containing your `Homestead.yaml`). Within this file, you may make any customization you would like; however, the `user-customizations.sh` should not be version controlled.

Homestead를 팀 환경에서 사용할 때 개인 개발 스타일에 더 잘 적응할 수 있도록 Homestead를 조정할 수 있습니다. Homestead 디렉토리의 루트 (`Homestead.yaml`이 들어있는 동일한 디렉토리)에 `user-customizations.sh` 파일을 만들 수 있습니다. 이 파일 내에서 원하는대로 커스터마이징 할 수 있습니다. 그러나 `user-customizations.sh`는 버전 관리가 되어서는 안됩니다.

<a name="updating-homestead"></a>
## Updating Homestead
## 홈스테드 업데이트하기

Before you begin updating Homestead ensure you run `vagrant destroy` to remove your current virtual machine. 

Homestead를 업데이트하기 전에 `vagrant destroy`를 실행하여 현재 가상 머신을 제거하십시오. 

    vagrant destroy

Next, you need to update the Homestead source code. If you cloned the repository you can run the following commands at the location you originally cloned the repository:

다음으로 Homestead 소스 코드를 업데이트해야합니다. 저장소를 복제 한 경우 원래 저장소를 복제 한 위치에서 다음 명령을 실행할 수 있습니다.

    git fetch

    git pull origin release
    
These commands pull the latest Homestead code from the GitHub repository, fetches the latest tags, and then checks out the latest tagged release. You can find the latest stable release version on the [GitHub releases page](https://github.com/laravel/homestead/releases).

이 명령은 Homestead 코드를 GitHub 저장소에서 가져 와서 최신 태그를 가져온 다음 최신 태그 릴리스를 체크 아웃합니다. 최신 안정 버전은 [GitHub 릴리즈 페이지](https://github.com/laravel/homestead/releases)에서 찾을 수 있습니다.

If you have installed Homestead via your project's `composer.json` file, you should ensure your `composer.json` file contains `"laravel/homestead": "^9"` and update your dependencies:

프로젝트의 `composer.json` 파일을 통해서 홈스테드를 설치했었다면, `composer.json` 파일이 `"laravel/homestead": "^9"`를 포함하여 의존성을 업데이트 할 수 있게 해야합니다.

    composer update

Then, you should update the Vagrant box using the `vagrant box update` command:

그런 다음 `vagrant box update` 명령어를 사용하여 Vagrant 박스를 업데이트 해야합니다.

    vagrant box update

Finally, you will need to destroy and regenerate your Homestead box to utilize the latest Vagrant installation. To accomplish this, run the following commands in your Homestead directory:

마지막으로 Homestead 박스를 제거하고 재생성하여 최신 Vagrant 설치를 활용해야합니다. 이를 수행하려면 Homestead 디렉토리에서 다음 명령을 실행하십시오.

    vagrant destroy

    vagrant up

<a name="provider-specific-settings"></a>
## Provider Specific Settings
## 프로바이더 지정 셋팅

<a name="provider-specific-virtualbox"></a>
### VirtualBox

#### `natdnshostresolver`
#### `natdnshostresolver`

By default, Homestead configures the `natdnshostresolver` setting to `on`. This allows Homestead to use your host operating system's DNS settings. If you would like to override this behavior, add the following lines to your `Homestead.yaml` file:

기본적으로, 홈스테드는 `natdnshostresolver` 설정을 `on` 으로 구성합니다. 이렇게 하면 홈스테드가 여러분의 OS의 DNS 셋팅을 사용할 수 있게 해줍니다. 이 동작을 오버라이딩 하고자 한다면, `Homestead.yaml` 파일에 다음 라인을 추가하십시오:

    provider: virtualbox
    natdnshostresolver: 'off'

#### Symbolic Links On Windows
#### 윈도우에서 심볼릭 링크

If symbolic links are not working properly on your Windows machine, you may need to add the following block to your `Vagrantfile`:

윈도우 환경에서 심볼릭 링크가 원하는 대로 동작하지 않는다면, `Vagrantfile` 파일에 다음 블럭을 추가하십시오:

    config.vm.provider "virtualbox" do |v|
        v.customize ["setextradata", :id, "VBoxInternal2/SharedFoldersEnableSymlinksCreate/v-root", "1"]
    end
