# Laravel Homestead
# 라라벨 홈스테드

- [Introduction](#introduction)
- [소개](#introduction)
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
    - [Installing MariaDB](#installing-mariadb)
    - [MariaDB 설치하기](#installing-mariadb)
- [Daily Usage](#daily-usage)
- [사용 방법](#daily-usage)
    - [Accessing Homestead Globally](#accessing-homestead-globally)
    - [어디에서나 홈스테드 엑세스하기](#accessing-homestead-globally)
    - [Connecting Via SSH](#connecting-via-ssh)
    - [SSH로 접속하기 ](#connecting-via-ssh)
    - [Connecting To Databases](#connecting-to-databases)
    - [데이터베이스에 접속하기](#connecting-to-databases)
    - [Adding Additional Sites](#adding-additional-sites)
    - [사이트 추가하기](#adding-additional-sites)
    - [Configuring Cron Schedules](#configuring-cron-schedules)
    - [Cron 설정하기](#configuring-cron-schedules)
    - [Ports](#ports)
    - [포트 지정](#ports)
    - [Sharing Your Environment](#sharing-your-environment)
    - [환경 공유하기](#sharing-your-environment)
    - [Multiple PHP Versions](#multiple-php-versions)
    - [여러 버전의 PHP 사용하기](#multiple-php-versions)
- [Network Interfaces](#network-interfaces)
- [네트워크 인터페이스](#network-interfaces)
- [Updating Homestead](#updating-homestead)
- [홈스테드 업데이트하기](#updating-homestead)
- [Old Versions](#old-versions)
- [이전 버전](#old-versions)
- [Provider Specific Settings](#provider-specific-settings)
- [프로바이더 한정 셋팅](#provider-specific-settings)
    - [VirtualBox](#provider-specific-virtualbox)
    - [VirtualBox](#provider-specific-virtualbox)

<a name="introduction"></a>
## Introduction
## 소개

Laravel strives to make the entire PHP development experience delightful, including your local development environment. [Vagrant](https://www.vagrantup.com) provides a simple, elegant way to manage and provision Virtual Machines.

라라벨은 여러분의 로컬 개발 환경을 포함하여 전체 PHP 개발 경험을 유쾌하게 만들기 위해서 노력하고 있습니다. [Vagrant](https://www.vagrantup.com)은 간단하고 세련되게 가상 머신을 관리하고 준비할 수 있는 방법을 제공합니다. 

Laravel Homestead is an official, pre-packaged Vagrant box that provides you a wonderful development environment without requiring you to install PHP, a web server, and any other server software on your local machine. No more worrying about messing up your operating system! Vagrant boxes are completely disposable. If something goes wrong, you can destroy and re-create the box in minutes!

라라벨 홈스테드는 공식적으로 여러분이 PHP, 웹서버, 기타 다른 서버 소프트웨어를 설치할 필요가 없는 사전에 준비된 Vagrant box이며 멋진 개발 환경을 제공합니다. 운영체제가 너저분 해지는 것을 걱정할 필요가 없습니다! Vagrant box는 완전히 일회용입니다. 만약 뭔가 잘 되지 않는다면, 여러분은 몇분안에 다시 box를 생성할 수 있습니다. 

Homestead runs on any Windows, Mac, or Linux system, and includes the Nginx web server, PHP 7.1, MySQL, Postgres, Redis, Memcached, Node, and all of the other goodies you need to develop amazing Laravel applications.

홈스테드는 Windows, Mac 그리고 Linux 시스템에서 실행할 수 있으며 Nginx 웹 서버, PHP 7.1, MySQL, Postgres, Redis, Memcached, Node 및 기타 라라벨 어플리케이션 개발에 필요한 멋진 도구들을 모두 포함하고 있습니다. .

> {note} If you are using Windows, you may need to enable hardware virtualization (VT-x). It can usually be enabled via your BIOS. If you are using Hyper-V on a UEFI system you may additionally need to disable Hyper-V in order to access VT-x.

> {note} 만약 윈도우를 사용하는 경우, 하드웨어 가상화 (VT-x)를 활성화해야합니다. 이 설정은 일반적으로 BIOS에서 사용할 수 있습니다. 만약 UEFI 시스템에서 Hyper-V 를 사용중이라면 추가적으로 VT-x에 액세스하기 위해 Hyper-V를 비활성화 해야합니다.

<a name="included-software"></a>
### Included Software
### 포함된 소프트웨어

- Ubuntu 16.04
- Git
- PHP 7.1
- Nginx
- MySQL
- MariaDB
- Sqlite3
- Postgres
- Composer
- Node (With Yarn, Bower, Grunt, and Gulp)
- Redis
- Memcached
- Beanstalkd
- Mailhog
- ngrok

<a name="installation-and-setup"></a>
## Installation & Setup
## 설치 & 구성하기

<a name="first-steps"></a>
### First Steps
### 첫번째 단계

Before launching your Homestead environment, you must install [VirtualBox 5.1](https://www.virtualbox.org/wiki/Downloads), [VMWare](https://www.vmware.com), or [Parallels](http://www.parallels.com/products/desktop/) as well as [Vagrant](https://www.vagrantup.com/downloads.html). All of these software packages provide easy-to-use visual installers for all popular operating systems.

라라벨 홈스테드를 구동하기 전에 여러분은 반드시 [VirtualBox 5.1](https://www.virtualbox.org/wiki/Downloads), [VMWare](https://www.vmware.com) 또는 [Parallels](http://www.parallels.com/products/desktop/) 그리고 [Vagrant](https://www.vagrantup.com/downloads.html)를 설치해야 합니다. 이 소프트웨어 패키지들은 모든 운영체제에서 손쉽게 설치할 수 있는 인스톨러 프로그램을 제공합니다. 

To use the VMware provider, you will need to purchase both VMware Fusion / Workstation and the [VMware Vagrant plug-in](https://www.vagrantup.com/vmware). Though it is not free, VMware can provide faster shared folder performance out of the box.

VMware 프로바이더를 사용하려면 VMware Fusion / Workstation 과 [VMware Vagrant plug-in](https://www.vagrantup.com/vmware)을 구매해야 합니다. 무료는 아니지만, VMware 의 경우 추가적인 설정 없이도 더 빠른 공유폴더의 퍼포먼스를 제공합니다. 

To use the Parallels provider, you will need to install [Parallels Vagrant plug-in](https://github.com/Parallels/vagrant-parallels). It is free of charge.

Parallels 프로바이더를 사용하려면, [Parallels Vagrant 플러그인](https://github.com/Parallels/vagrant-parallels)을 설치해야 합니다. 이는 무료입니다.
 
#### Installing The Homestead Vagrant Box
#### 홈스테드 Vagrant Box 설치하기

Once VirtualBox / VMware and Vagrant have been installed, you should add the `laravel/homestead` box to your Vagrant installation using the following command in your terminal. It will take a few minutes to download the box, depending on your Internet connection speed:

VirtualBox / VMware 그리고 Vagrant 가 설치되었다면, 터미널에서 다음의 명령어를 통해서 인스톨된 Vagrant에 `laravel/homestead` box 를 추가해야 합니다. box를 다운로드 하는 것은 인터넨 연결 속도에 따라 몇분 정도 소요됩니다. 

    vagrant box add laravel/homestead

If this command fails, make sure your Vagrant installation is up to date.

이 명령어가 실패하면, Vagrant 설치가 최신버전인지 확인하십시오.

#### Installing Homestead
#### 홈스테드 설치하기

You may install Homestead by simply cloning the repository. Consider cloning the repository into a `Homestead` folder within your "home" directory, as the Homestead box will serve as the host to all of your Laravel projects:

간단하게 Git 저장소를 복제하여 홈스테드를 설치할 수 있습니다. 여러분의 "home" 디렉토리안에 `Homestead` 폴더로 저장소를 복제하면 홈스테드 box가 라라벨 프로젝트의 모든 호스트 역할을 할 것입니다. 

    cd ~

    git clone https://github.com/laravel/homestead.git Homestead

You should check out a tagged version of Homestead since the `master` branch may not always be stable. You can find the latest stable version on the [Github Release Page](https://github.com/laravel/homestead/releases):

`master` 브랜치는 개발중이라 안정적이지 않을 수도 있기 때문에, 태그를 지정한 버전을 체크아웃 해야합니다. 안정적인 버전은 [Github 릴리즈 페이지](https://github.com/laravel/homestead/releases)에서 찾으실 수 있습니다:

    cd Homestead

    // Clone the desired release...
    git checkout v5.4.0

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

The `provider` key in your `Homestead.yaml` file indicates which Vagrant provider should be used: `virtualbox`, `vmware_fusion`, `vmware_workstation`, or `parallels`. You may set this to the provider you prefer:

`Homestead.yaml` 파일의 `provider` 키는 `virtualbox`, `vmware_fusion`, `vmware_workstation` 와 `parallels`중 어느 것을 사용할 것인지를 알려줍니다. 여러분이 원하는대로 이 프로바이더를 설정할 수 있습니다.

    provider: virtualbox

#### Configuring Shared Folders
#### 공유폴더 설정하기

The `folders` property of the `Homestead.yaml` file lists all of the folders you wish to share with your Homestead environment. As files within these folders are changed, they will be kept in sync between your local machine and the Homestead environment. You may configure as many shared folders as necessary:

`Homestead.yaml` 파일의 `folders` 속성은 홈스테드 환경과 공유하고자 하는 모든 폴더가 나열되어 있습니다. 여러분의 로컬 머신과 홈스테드 환경사이에서 동기화된 이 폴더 안의 파일들이 변경되면  변경사항이 서로 유지됩니다. 필요한 만큼 공유 폴더를 설정하십시오:

    folders:
        - map: ~/Code
          to: /home/vagrant/Code

To enable [NFS](https://www.vagrantup.com/docs/synced-folders/nfs.html), you only need to add a simple flag to your synced folder configuration:

[NFS](https://www.vagrantup.com/docs/synced-folders/nfs.html)를 사용하기 위해서는 동기화 폴더 설정에 플래그를 더해주면 됩니다:

    folders:
        - map: ~/Code
          to: /home/vagrant/Code
          type: "nfs"

> {note} When using NFS, you should consider installing the [vagrant-bindfs](https://github.com/gael-ian/vagrant-bindfs) plug-in. This plug-in will maintain the correct user / group permissions for files and directories within the Homestead box.

> {note} NFS를 사용할 때에는, [vagrant-bindfs](https://github.com/gael-ian/vagrant-bindfs) 플러그인을 설치해야만 합니다. 이 플러그인은 홈스테드 box안에서 파일과 디렉토리를 위한 올바른 사용자와 권한을 관리해줍니다.

You may also pass any options supported by Vagrant's [Synced Folders](https://www.vagrantup.com/docs/synced-folders/basic_usage.html) by listing them under the `options` key:

`options` 키 아래 Vagrant의 [동기화 폴더](https://www.vagrantup.com/docs/synced-folders/basic_usage.html)옵션을 나열식으로 전달할 수 있습니다:

    folders:
        - map: ~/Code
          to: /home/vagrant/Code
          type: "rsync"
          options:
              rsync__args: ["--verbose", "--archive", "--delete", "-zz"]
              rsync__exclude: ["node_modules"]

#### Configuring Nginx Sites
#### Nginx 사이트 설정하기

Not familiar with Nginx? No problem. The `sites` property allows you to easily map a "domain" to a folder on your Homestead environment. A sample site configuration is included in the `Homestead.yaml` file. Again, you may add as many sites to your Homestead environment as necessary. Homestead can serve as a convenient, virtualized environment for every Laravel project you are working on:

Nginx에 익숙하지 않으신가요? 문제없습니다. `sites` 속성을 통해 홈스테드 환경의 폴더와 "도메인"을 쉽게 매핑 할 수 있습니다. 사이트 샘플이 `Homestead.yaml` 파일에 포함되어 있습니다. 여기에서도 여러분의 홈스테드 환경에 필요한만큼 사이트를 추가할 수 있습니다. 홈스테드는 편리하게, 여러분이 작업을 수행하는 모든 라라벨 프로젝트를 위한 가상 환경을 제공합니다:

    sites:
        - map: homestead.app
          to: /home/vagrant/Code/Laravel/public

If you change the `sites` property after provisioning the Homestead box, you should re-run `vagrant reload --provision`  to update the Nginx configuration on the virtual machine.

홈스테드 박스가 프로비저닝 된 이후에 `sites` 속성을 변경한다면, 가상머신의 Nginx 설정을 갱신하기 위해서 `vagrant reload --provision` 을 다시 실행시켜야만 합니다.

#### The Hosts File
#### Hosts 파일

You must add the "domains" for your Nginx sites to the `hosts` file on your machine. The `hosts` file will redirect requests for your Homestead sites into your Homestead machine. On Mac and Linux, this file is located at `/etc/hosts`. On Windows, it is located at `C:\Windows\System32\drivers\etc\hosts`. The lines you add to this file will look like the following:

여러분은 Nginx 사이트에 설정한 "도메인"을 로컬 머신의 `hosts` 파일에 추가해야 합니다. `hosts` 파일은 여러분의 홈스테드 사이트에 대한 요청을 홈스테드 머신으로 리다이렉트 시킬 것 입니다. 맥과 리눅스에서는 `/etc/hosts`, 윈도우에서는 `C:\Windows\System32\drivers\etc\hosts`에 파일이 있습니다. 다음과 같이 도메인을 추가하십시오. 

    192.168.10.10  homestead.app

Make sure the IP address listed is the one set in your `Homestead.yaml` file. Once you have added the domain to your `hosts` file and launched the Vagrant box you will be able to access the site via your web browser:

입력한 IP 주소가 여러분의 `Homestead.yaml` 파일에 설정된 목록중 하나인지 확인하십시오. `hosts` 파일에 도메인을 추가 하고 Vagrant box를 구동하면 웹브라우저를 통해서 사이트에 액세스 할 수 있습니다:

    http://homestead.app

<a name="launching-the-vagrant-box"></a>
### Launching The Vagrant Box
### Vagrant Box 구동하기

Once you have edited the `Homestead.yaml` to your liking, run the `vagrant up` command from your Homestead directory. Vagrant will boot the virtual machine and automatically configure your shared folders and Nginx sites.

`Homestead.yaml` 파일 설정이 끝났다면, 홈스테드 디렉토리에서 `vagrant up` 명령어를 실행하십시오. Vagrant가 가상머신을 구동시키고 자동으로 공유폴더와 Nginx 사이트를 설정할 것입니다. 

To destroy the machine, you may use the `vagrant destroy --force` command.

가상 머신을 파괴하기 위해서는 `vagrant destroy --force` 명령어를 사용하면 됩니다.  

<a name="per-project-installation"></a>
### Per Project Installation
### 프로젝트별 설치하기

Instead of installing Homestead globally and sharing the same Homestead box across all of your projects, you may instead configure a Homestead instance for each project you manage. Installing Homestead per project may be beneficial if you wish to ship a `Vagrantfile` with your project, allowing others working on the project to simply `vagrant up`.

홈스테드를 전역에 설치하고 전체 프로젝트에서 동일한 홈스테드 박스를 공유하는 대신, 홈스테드 인스턴스를 각각의 관리하는 프로젝트별로 설정할 수 있습니다. 프로젝트마다 홈스테드를 설치하는 것은 `Vagrantfile`을 프로젝트에서 구성하여, `vagrant up`을 할 때 다른 작업을 할 수 있도록 하는 장점이 있습니다. 

To install Homestead directly into your project, require it using Composer:

홈스테드를 프로젝트에 직접 설치하려면 Composer를 사용합니다.

    composer require laravel/homestead --dev

Once Homestead has been installed, use the `make` command to generate the `Vagrantfile` and `Homestead.yaml` file in your project root. The `make` command will automatically configure the `sites` and `folders` directives in the `Homestead.yaml` file.

홈스테드가 설치되고 나면, `make` 명령어를 사용하여 `Vagrantfile` 과 `Homestead.yaml` 파일을 프로젝트 루트 디렉토리에 생성합니다. `make` 명령어는 자동으로 `Homestead.yaml` 파일에서 `sites` 와 `folders` 옵션을 설정할 것입니다. 

Mac / Linux:

    php vendor/bin/homestead make

Windows:

    vendor\\bin\\homestead make

Next, run the `vagrant up` command in your terminal and access your project at `http://homestead.app` in your browser. Remember, you will still need to add an `/etc/hosts` file entry for `homestead.app` or the domain of your choice.

다음으로 터미널에서 `vagrant up` 명령어를 실행하고 브라우저에서 `http://homestead.app` 프로젝트에 엑세스 하십시오. 유의할 것은 `/etc/hosts` 파일에 `homestead.spp` 또는 선택한 도메인을 추가할 필요가 있다는 것입니다. 

<a name="installing-mariadb"></a>
### Installing MariaDB
### MariaDB 설치하기

If you prefer to use MariaDB instead of MySQL, you may add the `mariadb` option to your `Homestead.yaml` file. This option will remove MySQL and install MariaDB. MariaDB serves as a drop-in replacement for MySQL so you should still use the `mysql` database driver in your application's database configuration:

MySQL 대신에 MariaDB를 사용하고자 한다면, `Homestead.yaml` 파일에 `mariadb` 옵션을 추가하면 됩니다. 이 옵션은 MySQL을 제거하고 MariaDB를 설치할 것입니다. MariaDB는 MySQL을 바로 대체가능하기 때문에, 어플리케이션에서 `mysql` 데이터베이스 드라이버를 그대로 사용할 수 있습니다.

    box: laravel/homestead
    ip: "192.168.20.20"
    memory: 2048
    cpus: 4
    provider: virtualbox
    mariadb: true

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

다음 내용을 가지는 `homestead.bat` 배치 파일을 원하는 곳에다가 생성합니다:

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

A `homestead` database is configured for both MySQL and Postgres out of the box. For even more convenience, Laravel's `.env` file configures the framework to use this database out of the box.

별다른 설정 없이도 `homestead` 데이터베이스는 MySQL과 Postgres 가 설정되어 있습니다. 보다 편리하게 하기 위해 라라벨의 `.env` 파일의 설정이 프로임워크에서 별다른 작업 없이도 이 데이터베이스를 사용하도록 설정되어 있습니다.

To connect to your MySQL or Postgres database from your host machine's database client, you should connect to `127.0.0.1` and port `33060` (MySQL) or `54320` (Postgres). The username and password for both databases is `homestead` / `secret`.

데이터베이스 클라이언트를 통해서 MySQL 또는 Postgres 데이터베이스에 접속하려면 `127.0.0.1` 의 `33060` (MySQL) 또는 `54320` (Postgres) 포트에 연결하면 됩니다. 데이터베이스 사용자 이름과 암호는 모두 `homestead` / `secret` 입니다. 

> {note} You should only use these non-standard ports when connecting to the databases from your host machine. You will use the default 3306 and 5432 ports in your Laravel database configuration file since Laravel is running _within_ the virtual machine.

> {note} 데이터베이스에 접속할 때는 이러한 표준이 아닌 포트를 사용해야 합니다. 라라벨이 가상 머신 _안에서_ 동작하고 있기 때문에 기본적인 3306 과 5432 포트는 라라벨 데이터베이스 설정 파일 안에서 사용할 수 있습니다.  

<a name="adding-additional-sites"></a>
### Adding Additional Sites
### 추가적인 사이트 지정하기

Once your Homestead environment is provisioned and running, you may want to add additional Nginx sites for your Laravel applications. You can run as many Laravel installations as you wish on a single Homestead environment. To add an additional site, simply add the site to your `Homestead.yaml` file:

홈스테드 환경이 준비되어 구성되고 난 뒤에 라라벨 어플리케이션에 추가적인 Nginx 사이트를 구성하기를 원할 수도 있습니다. 하나의 홈스테드 환경 안에서 여러개의 라라벨을 설치하여 작동 시킬수도 있습니다. 추가적인 사이트를 지정하기 위해서, 간단하게 `Homestead.yaml` 파일에 사이트를 추가하십시오:

    sites:
        - map: homestead.app
          to: /home/vagrant/Code/Laravel/public
        - map: another.app
          to: /home/vagrant/Code/another/public

If Vagrant is not automatically managing your "hosts" file, you may need to add the new site to that file as well:

Vagrant 가 자동으로 "hosts" 파일을 관리하지 않는다면, 직접 다음의 사이트를 호스트 파일에 추가해야합니다: 

    192.168.10.10  homestead.app
    192.168.10.10  another.app

Once the site has been added, run the `vagrant reload --provision` command from your Homestead directory.

사이트가 추가되면, 홈스테드 디렉토리에서 `vagrant reload --provision` 명령어를 실행하십시오.

<a name="site-types"></a>
#### Site Types
#### 사이트 타입

Homestead supports several types of sites which allow you to easily run projects that are not based on Laravel. For example, we may easily add a Symfony application to Homestead using the `symfony2` site type:

홈스테드는 라라벨이 아닌 프로젝트를 손쉽게 구동할 수 있는 몇가지 사이트 타입을 지원합니다. 예를 들자면, `symfony2` 사이트 타입은 Synfony 어플리케이션을 홈스테드에서 추가할 수 있도록 해줍니다:

    sites:
        - map: symfony2.app
          to: /home/vagrant/Code/Symfony/public
          type: symfony2

The available site types are: `apache`, `laravel` (the default), `proxy`, `silverstripe`, `statamic`, `symfony2`, and `symfony4`.

사용가능한 사이트 타입에는 `apache`, `laravel` (기본값), `proxy`, `silverstripe`, `statamic`, `symfony2`, 그리고 `symfony4`가 있습니다.

<a name="site-parameters"></a>
#### Site Parameters
#### 사이트 파라미터

You may add additional Nginx `fastcgi_param` values to your site via the `params` site directive. For example, we'll add a `FOO` parameter with a value of `BAR`:

추가적인 Nginx `fastcgi_param` 값을 사이트에 설정하려면 `params` 사이트 지시어를 사용하면 됩니다. 예를 들어 `BAR` 라는 값을 가지는 `FOO` 파라미터를 추가해보겠습니다:  

    sites:
        - map: homestead.app
          to: /home/vagrant/Code/Laravel/public
          params:
              - key: FOO
                value: BAR

<a name="configuring-cron-schedules"></a>
### Configuring Cron Schedules
### Cron 스케줄링 설정하기 =

Laravel provides a convenient way to [schedule Cron jobs](/docs/{{version}}/scheduling) by scheduling a single `schedule:run` Artisan command to be run every minute. The `schedule:run` command will examine the job schedule defined in your `App\Console\Kernel` class to determine which jobs should be run.

라라벨에서는 단 한번 `schedule:run` 아티즌 명령어를 실행하여 매분마다 [Cron 작업 스케줄링](/docs/{{version}}/scheduling)을 할 수 있도록 하는 편리한 방법을 제공합니다. `schedule:run` 명령어는 `App\Console\Kernel` 클래스에 정의되어 있는 작업 스케줄이 실행되어야 하는지 검사할 것입니다. 

If you would like the `schedule:run` command to be run for a Homestead site, you may set the `schedule` option to `true` when defining the site:

홈스테드의 사이트에서 `schedule:run` 명령을 실행하려면 사이트를 정의 할 때 `schedule` 옵션을 `true` 로 설정하십시오.

    sites:
        - map: homestead.app
          to: /home/vagrant/Code/Laravel/public
          schedule: true

The Cron job for the site will be defined in the `/etc/cron.d` folder of the virtual machine.

사이트를 위한 Cron 작업은 가상 머신의 `/etc/cron.d` 폴더에 정의됩니다.

<a name="ports"></a>
### Ports
### 포트지정하기

By default, the following ports are forwarded to your Homestead environment:
            
기본적으로 다음의 포트들이 홈스테드 환경에서 포워딩 설정 되어 있습니다.

- **SSH:** 2222 &rarr; Forwards To 22
- **HTTP:** 8000 &rarr; Forwards To 80
- **HTTPS:** 44300 &rarr; Forwards To 443
- **MySQL:** 33060 &rarr; Forwards To 3306
- **Postgres:** 54320 &rarr; Forwards To 5432
- **Mailhog:** 8025 &rarr; Forwards To 8025

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

To solve this problem, Homestead includes its own `share` command. To get started, SSH into your Homestead machine via `vagrant ssh` and run `share homestead.app`. This will share the `homestead.app` site from your `Homestead.yaml` configuration file. Of course, you may substitute any of your other configured sites for `homestead.app`:

이 문제를 극복하기 위해서, 홈스테드에 `share` 명령어가 포함되었습니다. 이렇게 하기 위해서 `vagrant ssh` 명령어를 통해서 홈스테드 머신에 SSH 접속을 한 다음 `share homestead.app` 을 실행하십시오. 이렇게 하면 여러분의 `Homestead.yaml` 설정에 있는 ``homestead.app` 사이트는 물론 다른 사이트도 공유할 수 있습니다:

    share homestead.app

After running the command, you will see an Ngrok screen appear which contains the activity log and the publicly accessible URLs for the shared site. If you would like to specify a custom region, subdomain, or other Ngrok runtime option, you may add them to your `share` command:

이 명령어를 실행하면, 여러분은 Ngrok 스크린에서 activity log와 함께 공유한 사이트에 접속할 수 있는 public URL을 확인할 수 있습니다. 커스텀 리전, 서브도메인, Ngrok 실행 옵션을 지정하고자 한다면 `share` 명령어에 이를 추가하면됩니다:

    share homestead.app -region=eu -subdomain=laravel

> {note} Remember, Vagrant is inherently insecure and you are exposing your virtual machine to the Internet when running the `share` command.

> {note} 유념할점은, `share` 명령어를 실행중일 때는 가상머신이 인터넷에 노출되고 보안에 노출될 수 있다는 점입니다.

<a name="multiple-php-versions"></a>
### Multiple PHP Versions
### 여러 버전의 PHP 사용하기

Homestead 6 introduced support for multiple versions of PHP on the same virtual machine. You may specify which version of PHP to use for a given site within your `Homestead.yaml` file. The available PHP versions are: "5.6", "7.0", and "7.1":

홈스테드 6부터 동일한 가상 머신에서 여러버전의 PHP를 사용할 수 있습니다. 해당 사이트에서 어떤 버전의 PHP를 사용할지 `Homestead.yaml`에서 지정할 수 있습니다. 사용가능한 PHP 버전은 "5.6", "7.0", "7.1" 입니다:

    sites:
        - map: homestead.app
          to: /home/vagrant/Code/Laravel/public
          php: "5.6"

<a name="network-interfaces"></a>
## Network Interfaces
## 네트워크 인터페이스

The `networks` property of the `Homestead.yaml` configures network interfaces for your Homestead environment. You may configure as many interfaces as necessary:

`Homestead.yaml` 설정의 `networks` 속성은 홈스테드 환경의 네트워크 인터페이스 설정에 관련된 것입니다. 필요한 경우 여러개의 인터페이스를 설정할 수 있습니다:

    networks:
        - type: "private_network"
          ip: "192.168.10.20"

To enable a [bridged](https://www.vagrantup.com/docs/networking/public_network.html) interface, configure a `bridge` setting and change the network type to `public_network`:

[bridged](https://www.vagrantup.com/docs/networking/public_network.html) 인터페이스를 활성화 하기 위해서는, `bridge` 옵션을 설정하고 네트워크 타입을 `public_network`로 변경해야 합니다:

    networks:
        - type: "public_network"
          ip: "192.168.10.20"
          bridge: "en1: Wi-Fi (AirPort)"

To enable [DHCP](https://www.vagrantup.com/docs/networking/public_network.html), just remove the `ip` option from your configuration:

[DHCP](https://www.vagrantup.com/docs/networking/public_network.html)를 활성화하기 위해서는, 설정에서 간단하게 `ip` 옵션을 제거하면 됩니다: 

    networks:
        - type: "public_network"
          bridge: "en1: Wi-Fi (AirPort)"

<a name="updating-homestead"></a>
## Updating Homestead
## 홈스테드 업데이트하기

You can update Homestead in two simple steps. First, you should update the Vagrant box using the `vagrant box update` command:

두가지 간단한 동작으로 홈스테드를 업데이트 할 수 있습니다. 먼저 `vagrant box update` 명령어를 사용하여 Vagrant 박스를 업데이트 해야합니다:

    vagrant box update

Next, you need to update the Homestead source code. If you cloned the repository you can simply `git pull origin master` at the location you originally cloned the repository.

그 다음으로 홈스테드 소스 코드를 업데이트 해야 합니다. 저장소를 복제했었다면 복제한 디렉토리에서 간단하게 `git pull origin master` 를 사용할 수 있습니다.

If you have installed Homestead via your project's `composer.json` file, you should ensure your `composer.json` file contains `"laravel/homestead": "^4"` and update your dependencies:

프로젝트의 `composer.json` 파일을 통해서 홈스테드를 설치했었다면, `composer.json` 파일이 `"laravel/homestead": "^4"`를 포함하여 의존성을 업데이트 할 수 있게 해야합니다: 

    composer update

<a name="old-versions"></a>
## Old Versions
## 이전 버전

You can easily override the version of the box that Homestead uses by adding the following line to your `Homestead.yaml` file:

여러분은 `Homestead.yaml` 파일에 다음 라인을 추가하여 손쉽게 사용하는 홈스테드 box 버전을 지정할 수 있습니다: 

    version: 0.6.0

An example:

사용 예:

    box: laravel/homestead
    version: 0.6.0
    ip: "192.168.20.20"
    memory: 2048
    cpus: 4
    provider: virtualbox

When you use an older version of the Homestead box you need to match that with a compatible version of the Homestead source code. Below is a chart which shows the supported box versions, which version of Homestead source code to use, and the version of PHP provided:

이전버전의 홈스테드 박스를 사용하는 경우 홈스테드 소스 코드와 호환되는 버전이어야 합니다 다음은 지원되는 박스의 버전과 사용할 홈스테드 소스 코드 버전 및 제공되는 PHP 버전을 보여주는 표입니다:

|   | Homestead Version | Box Version |
|---|---|---|
| PHP 7.0 | 3.1.0 | 0.6.0 |
| PHP 7.1 | 4.0.0 | 1.0.0 |

<a name="provider-specific-settings"></a>
## Provider Specific Settings
## 프로바이더 지정 셋팅

<a name="provider-specific-virtualbox"></a>
### VirtualBox
### VirtualBox

By default, Homestead configures the `natdnshostresolver` setting to `on`. This allows Homestead to use your host operating system's DNS settings. If you would like to override this behavior, add the following lines to your `Homestead.yaml` file:

기본적으로, 홈스테드는 `natdnshostresolver` 설정을 `on` 으로 구성합니다. 이렇게 하면 홈스테드가 여러분의 OS의 DNS 셋팅을 사용할 수 있게 해줍니다. 이 동작을 오버라이딩 하고자 한다면, `Homestead.yaml` 파일에 다음 라인을 추가하십시오:

    provider: virtualbox
    natdnshostresolver: off
