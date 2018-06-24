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
    - [홈스테드 글로벌 접근하기](#accessing-homestead-globally)
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
- [Network Interfaces](#network-interfaces)
- [네트워크 인터페이스](#network-interfaces)

<a name="introduction"></a>
## Introduction
## 소개

Laravel strives to make the entire PHP development experience delightful, including your local development environment. [Vagrant](http://vagrantup.com) provides a simple, elegant way to manage and provision Virtual Machines.

라라벨은 여러분의 로컬 개발 환경을 포함하여 전체 PHP 개발 경험을 유쾌하게 만들기 위해서 노력하고 있습니다. [Vagrant](http://vagrantup.com)은 간단하고 세련되게 가상 머신을 관리하고 준비할 수 있는 방법을 제공합니다. 

Laravel Homestead is an official, pre-packaged Vagrant box that provides you a wonderful development environment without requiring you to install PHP, HHVM, a web server, and any other server software on your local machine. No more worrying about messing up your operating system! Vagrant boxes are completely disposable. If something goes wrong, you can destroy and re-create the box in minutes!

라라벨 홈스테드는 공식적으로 여러분이 PHP, HHVM, 웹서버, 기타 다른 서버 소프트웨어를 설치할 필요가 없는 사전에 준비된 Vagrant Box이며 멋진 개발 환경을 제공합니다. 운영체제가 너저분 해지는 것을 걱정할 필요가 없습니다! Vagrant box는 완전히 일회용입니다. 만약 뭔가 잘 되지 않는다면, 여러분은 몇분안에 다시 box를 생성할 수 있습니다. 

Homestead runs on any Windows, Mac, or Linux system, and includes the Nginx web server, PHP 7.0, MySQL, Postgres, Redis, Memcached, Node, and all of the other goodies you need to develop amazing Laravel applications.

홈스테드는 Windows, Mac 그리고 Linux 시스템에서 실행할 수 있으며 Nginx 웹 서버, PHP7.0, MySQL, Postgres, Redis, Memcached, Node 및 기타 라라벨 애플리케이션 개발에 필요한 멋진 도구들을 모두 포함하고 있습니다. .

> **Note:** If you are using Windows, you may need to enable hardware virtualization (VT-x). It can usually be enabled via your BIOS. If you are using Hyper-V on a UEFI system you may additionally need to disable Hyper-V in order to access VT-x.

> **주의:** 만약 윈도우를 사용하는 경우, 하드웨어 가상화 (VT-x)를 활성화해야합니다. 이 설정은 일반적으로 BIOS에서 사용할 수 있습니다. 만약 UEFI 시스템에서 Hyper-V 를 사용중이라면, 추가적으로 VT-x 에 접근할 수 있도록  Hyper-V 를 비활성화 해야할 수도 있습니다. 

<a name="included-software"></a>
### Included Software
### 포함된 소프트웨어

- Ubuntu 14.04
- Git
- PHP 7.0
- HHVM
- Nginx
- MySQL
- MariaDB
- Sqlite3
- Postgres
- Composer
- Node (With PM2, Bower, Grunt, and Gulp)
- Redis
- Memcached
- Beanstalkd

<a name="installation-and-setup"></a>
## Installation & Setup
## 설치 & 구성하기

<a name="first-steps"></a>
### First Steps
### 첫번째 단계

Before launching your Homestead environment, you must install [VirtualBox 5.x](https://www.virtualbox.org/wiki/Downloads) or [VMWare](http://www.vmware.com) as well as [Vagrant](http://www.vagrantup.com/downloads.html). All of these software packages provide easy-to-use visual installers for all popular operating systems.

라라벨 홈스테드를 구동하기 전에 여러분은 반드시 [VirtualBox 5.x](https://www.virtualbox.org/wiki/Downloads) 또는 [VMWare](http://www.vmware.com) 그리고 [Vagrant](http://www.vagrantup.com/downloads.html)를 설치해야 합니다. 이 소프트웨어 패키지들은 모든 운영체제에서 손쉽게 설치할 수 있는 인스톨러 프로그램을 제공합니다. 

To use the VMware provider, you will need to purchase both VMware Fusion / Workstation and the [VMware Vagrant plug-in](http://www.vagrantup.com/vmware). Though it is not free, VMware can provide faster shared folder performance out of the box.

VMware 프로바이더를 사용하려면 VMware Fusion / Workstation 과 [VMware Vagrant plug-in](http://www.vagrantup.com/vmware)을 구매해야 합니다. 무료는 아니지만, VMware 의 경우 추가적인 설정 없이도 더 빠른 공유폴더의 퍼포먼스를 제공할 수 있습니다.

#### Installing The Homestead Vagrant Box
#### 홈스테드 Vagrant Box 설치하기

Once VirtualBox / VMware and Vagrant have been installed, you should add the `laravel/homestead` box to your Vagrant installation using the following command in your terminal. It will take a few minutes to download the box, depending on your Internet connection speed:

VirtualBox / VMware 그리고 Vagrant 가 설치되었다면, 터미널에서 다음의 명령어를 통해서 인스톨된 Vagrant에 `laravel/homestead` box 를 추가해야 합니다. box를 다운로드 하는 것은 인터넨 연결 속도에 따라 몇분 정도 소요됩니다. 

    vagrant box add laravel/homestead

If this command fails, make sure your Vagrant installation is up to date.

이 명령어가 실패한다면, Vagrant 가 최신버전인지 확인하십시오.

#### Installing Homestead
#### 홈스테드 설치하기

You may install Homestead by simply cloning the repository. Consider cloning the repository into a `Homestead` folder within your "home" directory, as the Homestead box will serve as the host to all of your Laravel projects:

간단하게 Git 저장소를 복제하여 홈스테드를 설치할 수 있습니다. 여러분의 "home" 디렉토리안에 `Homestead` 폴더로 저장소를 복제하면 홈스테드 box가 라라벨 프로젝트의 모든 호스트 역할을 할 것입니다. 

    cd ~ 
    
    git clone https://github.com/laravel/homestead.git Homestead

Once you have cloned the Homestead repository, run the `bash init.sh` command from the Homestead directory to create the `Homestead.yaml` configuration file. The `Homestead.yaml` file will be placed in the `~/.homestead` hidden directory:

홈스테드 저장소를 복제한 뒤에, 홈스테드 디렉토리에서 `bash init.sh` 명령어를 통해서 `Homestead.yaml` 설정 파일을 생성할 수 있습니다. `~/.homestead` 라는 숨겨진 디렉토리에 `Homestead.yaml` 파일이 생성될 것입니다. 

    bash init.sh

<a name="configuring-homestead"></a>
### Configuring Homestead
### 홈스테드 설정하기

#### Setting Your Provider
#### 프로바이더 설정하기

The `provider` key in your `~/.homestead/Homestead.yaml` file indicates which Vagrant provider should be used: `virtualbox`, `vmware_fusion`, or `vmware_workstation`. You may set this to the provider you prefer:

`~/.homestead/Homestead.yaml` 파일의 `provider` 키는 `virtualbox`, `vmware_fusion`와 `vmware_workstation` 중 어느 것을 사용할 것인지를 알려줍니다. 여러분이 원하는대로 프로바이더를 설정할 수 있습니다.  

    provider: virtualbox

#### Configuring Shared Folders
#### 공유폴더 설정하기

The `folders` property of the `Homestead.yaml` file lists all of the folders you wish to share with your Homestead environment. As files within these folders are changed, they will be kept in sync between your local machine and the Homestead environment. You may configure as many shared folders as necessary:

`Homestead.yaml` 파일의 `folders` 속성은 홈스테드 환경과 공유하고자 하는 모든 폴더가 나열되어 있습니다. 여러분의 로컬 머신과 홈스테드 환경사이에서 동기화된 이 폴더 안의 파일들이 변경되면  변경사항이 서로 유지됩니다. 필요한 만큼 공유 폴더를 설정하십시오:

    folders:
        - map: ~/Code
          to: /home/vagrant/Code

To enable [NFS](http://docs.vagrantup.com/v2/synced-folders/nfs.html), just add a simple flag to your synced folder configuration:

[NFS](http://docs.vagrantup.com/v2/synced-folders/nfs.html) 를 사용하려면 동기화 폴더 설정에 플래그를 지정하면 됩니다.

    folders:
        - map: ~/Code
          to: /home/vagrant/Code
          type: "nfs"

#### Configuring Nginx Sites
#### Nginx 사이트 설정하기

Not familiar with Nginx? No problem. The `sites` property allows you to easily map a "domain" to a folder on your Homestead environment. A sample site configuration is included in the `Homestead.yaml` file. Again, you may add as many sites to your Homestead environment as necessary. Homestead can serve as a convenient, virtualized environment for every Laravel project you are working on:

Nginx에 익숙하지 않으신가요? 문제없습니다. `sites` 속성을 통해 홈스테드 환경의 폴더와 "도메인"을 쉽게 매핑 할 수 있습니다. 사이트 샘플이 `Homestead.yaml` 파일에 포함되어 있습니다. 여기에서도 여러분의 홈스테드 환경에 필요한만큼 사이트를 추가할 수 있습니다. 홈스테드는 편리하게, 여러분이 작업을 수행하는 모든 라라벨 프로젝트를 위한 가상 환경을 제공합니다:

    sites:
        - map: homestead.app
          to: /home/vagrant/Code/Laravel/public

You can make any Homestead site use [HHVM](http://hhvm.com) by setting the `hhvm` option to `true`:

`hhvm` 옵션을 `true` 로 설정하면 홈스테드 사이트가 [HHVM](http://hhvm.com)를 사용하게 할 수 있습니다:

    sites:
        - map: homestead.app
          to: /home/vagrant/Code/Laravel/public
          hhvm: true

If you change the `sites` property after provisioning the Homestead box, you should re-run `vagrant reload --provision`  to update the Nginx configuration on the virtual machine.

만약 홈스테드 box 를 프로비저닝 한 이후에 `sites` 속성을 변경하였다면, 다시 `vagrant reload --provision` 을 실행하여 가상 머신에 Nginx 설정을 갱신시켜 주어야 합니다.

#### The Hosts File
#### Hosts 파일

You must add the "domains" for your Nginx sites to the `hosts` file on your machine. The `hosts` file will redirect requests for your Homestead sites into your Homestead machine. On Mac and Linux, this file is located at `/etc/hosts`. On Windows, it is located at `C:\Windows\System32\drivers\etc\hosts`. The lines you add to this file will look like the following:

여러분이 Nginx 사이트에 설정한 "도메인"을 로컬 머신의 `hosts` 파일에 추가하여야 합니다. `hosts` 파일은 홈스테드 사이트에 대한 요청들을 홈스테드 환경으로 리다이렉트 시킬 것 입니다. 맥과 리눅스에서는 `/etc/hosts`, 윈도우에서는 `C:\Windows\System32\drivers\etc\hosts`에 파일이 있습니다. 다음과 같이 도메인을 추가하십시오. 

    192.168.10.10  homestead.app

Make sure the IP address listed is the one set in your `~/.homestead/Homestead.yaml` file. Once you have added the domain to your `hosts` file, you can access the site via your web browser:

입력한 IP 주소가 여러분의 `~/.homestead/Homestead.yaml` 파일에 설정된 목록중 하나인지 확인하십시오. `hosts` 파일에 도메인을 추가 하면, 웹브라우저를 통해서 사이트에 액세스 할 수 있습니다!

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

홈스테드를 전역에 설치하고 전체 프로젝트에서 동일한 홈스테드 박스를 공유하는 대신, 홈스테드 인스턴스를 여러분이 관리하는 각각의 프로젝트별로 설정할 수 있습니다. 프로젝트마다 홈스테드를 설치하는 것은 `Vagrantfile`을 프로젝트와 함께 구성하고 `vagrant up`을 할 때 다른 작업을 할 수 있도록 하는 장점이 있습니다. 

To install Homestead directly into your project, require it using Composer:

홈스테드를 프로젝트에 직접 설치하려면 Composer를 사용합니다.

    composer require laravel/homestead --dev

Once Homestead has been installed, use the `make` command to generate the `Vagrantfile` and `Homestead.yaml` file in your project root. The `make` command will automatically configure the `sites` and `folders` directives in the `Homestead.yaml` file.

홈스테드가 설치되고 나면, `make` 명령어를 사용하여 `Vagrantfile` 과 `Homestead.yaml` 파일을 프로젝트 루트 디렉토리에 생성합니다. `make` 명령어는 자동으로 `Homestead.yaml` 파일에서 `sites` 와 `folders` 옵션을 설정할 것입니다. 

Mac / Linux:

    php vendor/bin/homestead make

Windows:

  vendor\bin\homestead make

Next, run the `vagrant up` command in your terminal and access your project at `http://homestead.app` in your browser. Remember, you will still need to add an `/etc/hosts` file entry for `homestead.app` or the domain of your choice.

다음으로 터미널에서 `vagrant up` 명령어를 실행하고 브라우저에서 `http://homestead.app` 프로젝트에 엑세스 하십시오. 유의할 것은 `/etc/hosts` 파일에 `homestead.spp` 또는 선택한 도메인을 추가할 필요가 있다는 것입니다. 

<a name="installing-mariadb"></a>
### Installing MariaDB
### MariaDB 설치하기 

If you prefer to use MariaDB instead of MySQL, you may add the `mariadb` option to your `Homestead.yaml` file. This option will remove MySQL and install MariaDB. MariaDB serves as a drop-in replacement for MySQL so you should still use the `mysql` database driver in your application's database configuration:

MySQL 대신에 MariaDB 를 사용하고자 한다면, `Homestead.yaml` 파일에 `mariadb` 옵션을 추가하면 됩니다. 이 옵션은 MySQL을 제거하고, MariaDB 를 설치할 것입니다. MariaDB는 MySQL을 대체할 수 있으므로, 애플리케이션의 데이터베이스 설정에서 `mysql` 데이터베이스 드라이버를 계속 사용할 수 있습니다.

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
### 홈스테드 글로벌 접근하기

Sometimes you may want to `vagrant up` your Homestead machine from anywhere on your filesystem. You can do this by adding a simple Bash alias to your Bash profile. This alias will allow you to run any Vagrant command from anywhere on your system and will automatically point that command to your Homestead installation:

때로는, 파일시스템 어디에서든 홈스테드 머신을 위해서 `vagrant up`을 수행하고 싶을 수도 있습니다. Bash 프로필에 Bash 별칭을 추가하면 가능합니다. 다음의 별칭은 시스템의 어디에서나 어떠한 Vagrant 명령어라도 실행할 수 있도록 하고 해당 명령어가 홈스테드 설치 위치를 가리키도록 할 것입니다. 

    alias homestead='function __homestead() { (cd ~/Homestead && vagrant $*); unset -f __homestead; }; __homestead'

Make sure to tweak the `~/Homestead` path in the alias to the location of your actual Homestead installation. Once the alias is installed, you may run commands like `homestead up` or `homestead ssh` from anywhere on your system.

위의 `~/Homestead` 경로를 실제로 홈스테드를 설치한 위치로 수정하십시오. 해당 별칭이 설치되고나면 시스템의 어디에서나 `homestead up` 또는 `homestead ssh` 처럼 실행할 수 있습니다.

<a name="connecting-via-ssh"></a>
### Connecting Via SSH
### SSH를 통해서 연결하기

You can SSH into your virtual machine by issuing the `vagrant ssh` terminal command from your Homestead directory.

홈스테드 디렉토리에서 `vagrant ssh` 명령어를 실행하여 SSH를 통해서 홈스테드 환경에 접속할 수 있습니다. 

But, since you will probably need to SSH into your Homestead machine frequently, consider adding the "alias" described above to your host machine to quickly SSH into the Homestead box.

하지만, 홈스테드 머신에 자주 접속할 것이기 때문에 호스트 머신에서 손쉽게 홈스테드 박스에 SSH로 접속하기 위해서 "별칭"을 추가하는 것을 고려해 볼 수 있습니다. 

<a name="connecting-to-databases"></a>
### Connecting To Databases
### 데이터베이스 접속하기

A `homestead` database is configured for both MySQL and Postgres out of the box. For even more convenience, Laravel's `.env` file configures the framework to use this database out of the box.

별다른 설정 없이도 `homestead` 데이터베이스는 MySQL과 Postgres 가 설정되어 있습니다. 보다 편리하게하기 위해 라라벨의 `.env` 파일 설정을 통해서 프레임워크가 별다른 설정 없이도 이 데이터베이스를 사용하도록 설정되어 있습니다.

To connect to your MySQL or Postgres database from your host machine via Navicat or Sequel Pro, you should connect to `127.0.0.1` and port `33060` (MySQL) or `54320` (Postgres). The username and password for both databases is `homestead` / `secret`.

Navicat 나 Sequel Pro를 통해서 MySQL 또는 Postgres 데이터베이스에 접속하려면 `127.0.0.1` 의 `33060` (MySQL) 또는 `54320` (Postgres) 포트에 연결하면 됩니다. 데이터베이스 사용자 이름과 암호는 모두 `homestead` / `secret` 입니다. 

> **Note:** You should only use these non-standard ports when connecting to the databases from your host machine. You will use the default 3306 and 5432 ports in your Laravel database configuration file since Laravel is running _within_ the virtual machine.

> **주의:** 데이터베이스에 접속할 때는 이러한 표준이 아닌 포트를 사용해야 합니다. 라라벨이 가상 머신 안에서 동작하고 있기 때문에 기본적인 3306 과 5432 포트는 라라벨 데이터베이스 설정 파일 안에서 사용할 수 있습니다.  

<a name="adding-additional-sites"></a>
### Adding Additional Sites
### 추가적인 사이트 지정하기

Once your Homestead environment is provisioned and running, you may want to add additional Nginx sites for your Laravel applications. You can run as many Laravel installations as you wish on a single Homestead environment. To add an additional site, simply add the site to your `~/.homestead/Homestead.yaml` file and then run the `vagrant provision` terminal command from your Homestead directory.

홈스테드 환경이 준비되어 구성되고 난 뒤에 라라벨 애플리케이션에 추가적인 Nginx 사이트를 구성하기를 원할 수도 있습니다. 하나의 홈스테드 환경 안에서 여러개의 라라벨을 설치하여 작동 시킬수도 있습니다. 추가적인 사이트를 지정하기 위해서, 간단하게 `~/.homestead/Homestead.yaml` 파일에 사이트를 추가하고 터미널에서 홈스테드 디렉토리로 이동한 뒤 `vagrant provision` 명령어를 실행하면 됩니다. 

<a name="configuring-cron-schedules"></a>
### Configuring Cron Schedules
### Cron 스케줄링 설정하기 =

Laravel provides a convenient way to [schedule Cron jobs](/docs/{{version}}/scheduling) by scheduling a single `schedule:run` Artisan command to be run every minute. The `schedule:run` command will examine the job scheduled defined in your `App\Console\Kernel` class to determine which jobs should be run.

라라벨에서는 단 한번 `schedule:run` 아티즌 명령어를 실행하여 매분마다 [Cron 작업 스케줄링](/docs/{{version}}/scheduling)을 할 수 있도록 하는 편리한 방법을 제공합니다. `schedule:run` 명령어는 `App\Console\Kernel` 클래스에 정의되어 있는 작업들이 실행되어야 하는지 검사할 것입니다. 

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

#### Forwarding Additional Ports
#### 추가적인 포트 포워딩하기

If you wish, you may forward additional ports to the Vagrant box, as well as specify their protocol:

원한다면 포트뿐만 아니라 프로토콜을 추가로 Vagrant Box에 포워딩하도록 지정 할 수 있습니다.

    ports:
        - send: 93000
          to: 9300
        - send: 7777
          to: 777
          protocol: udp

<a name="network-interfaces"></a>
## Network Interfaces
## 네트워크 인터페이스

The `networks` property of the `Homestead.yaml` configures network interfaces for your Homestead environment. You may configure as many interfaces as necessary:

`Homestead.yaml` 파일의 `networks` 속성은 홈스테드 환경의 네트워크 인터페이스를 설정합니다. 여러분은 필요한 다수의 인터페이스들을 설정할 수 있습니다.

    networks:
        - type: "private_network"
          ip: "192.168.10.20"

To enable a [bridged](https://www.vagrantup.com/docs/networking/public_network.html) interface, configure a `bridge` setting and change the network type to `public_network`:

[bridged](https://www.vagrantup.com/docs/networking/public_network.html) 인터페이스를 활성화 하려면, `bridge` 설정으로 변경하고, 네트워크 타입을 `public_network` 로 변경하면 됩니다.

    networks:
        - type: "public_network"
          ip: "192.168.10.20"
          bridge: "en1: Wi-Fi (AirPort)"

To enable [DHCP](https://www.vagrantup.com/docs/networking/public_network.html), just remove the `ip` option from your configuration:

[DHCP](https://www.vagrantup.com/docs/networking/public_network.html) 를 활성화 하려면, 간단하게 설정에서 `ip` 옵션을 제거하면 됩니다: 

    networks:
        - type: "public_network"
          bridge: "en1: Wi-Fi (AirPort)"
