# Laravel Homestead
# 라라벨 홈스테드

- [Introduction 소개](#introduction)
- [Included Software 포함된 소프트웨어들](#included-software)
- [Installation & Setup 설치 & 구성하기](#installation-and-setup)
- [Daily Usage 사용방법](#daily-usage)
- [Ports 포트](#ports)
- [Blackfire Profiler Blackfire 프로파일러](#blackfire-profiler)

<a name="introduction"></a>
## Introduction
## 소개

Laravel strives to make the entire PHP development experience delightful, including your local development environment. 라라벨은 여러분의 로컬 개발 환경을 포함하여 전체 PHP 개발 경험을 유쾌하게 만들기 위해서 노력하고 있습니다. [Vagrant](http://vagrantup.com) provides a simple, elegant way to manage and provision Virtual Machines. [Vagrant](http://vagrantup.com)은 간단하고 세련되게 가상 머신을 관리하고 준비할 수 있는 방법을 제공합니다. 

Laravel Homestead is an official, pre-packaged Vagrant "box" that provides you a wonderful development environment without requiring you to install PHP, HHVM, a web server, and any other server software on your local machine. 라라벨 홈스테드는 공식적으로 여러분이 PHP, HHVM, 웹서버, 기타 다른 서버 소프트웨어를 설치할 필요가 없는 사전에 준비된 Vagrant “Box”이며 멋진 개발 환경을 제공합니다. No more worrying about messing up your operating system! 운영체제가 너저분 해지는 것을 걱정할 필요가 없습니다! Vagrant boxes are completely disposable. Vagrant box는 완전히 일회용입니다. If something goes wrong, you can destroy and re-create the box in minutes! 만약 뭔가 잘 되지 않는다면, 여러분은 몇분안에 다시 box를 생성할 수 있습니다. 

Homestead runs on any Windows, Mac, or Linux system, and includes the Nginx web server, PHP 5.6, MySQL, Postgres, Redis, Memcached, and all of the other goodies you need to develop amazing Laravel applications.

홈스테드는 Windows, Mac 그리고 Linux 시스템에서 실행할 수 있으며 Nginx 웹 서버, PHP5.6, MySQL, Postgres, Redis, Memcached 및 기타 라라벨 애플리케이션 개발에 필요한 멋진 도구들을 모두 포함하고 있습니다. .

> **Note 주의:** If you are using Windows, you may need to enable hardware virtualization (VT-x). It can usually be enabled via your BIOS. 만약 윈도우를 사용하는 경우, 하드웨어 가상화 (VT-x)를 활성화해야합니다. 이 설정은 일반적으로 BIOS에서 사용할 수 있습니다.

Homestead is currently built and tested using Vagrant 1.7.
홈스테디드는 현재 Vagrant1.7을 기반으로 구성되고 테스트되어 있습니다.

<a name="included-software"></a>
## Included Software
## 포함된 소프트웨어들

- Ubuntu 14.04
- PHP 5.6
- HHVM
- Nginx
- MySQL
- Postgres
- Node (With Bower, Grunt, and Gulp)
- Redis
- Memcached
- Beanstalkd
- [Laravel Envoy](/docs/{{version}}/envoy)
- [Blackfire Profiler](#blackfire-profiler)

<a name="installation-and-setup"></a>
## Installation & Setup
## 설치 & 구성하기

### Installing VirtualBox / VMware & Vagrant
### VirtualBox / VMware와 Vagrant 설치

Before launching your Homestead environment, you must install [VirtualBox](https://www.virtualbox.org/wiki/Downloads) and [Vagrant](http://www.vagrantup.com/downloads.html). 라라벨 홈스테드를 구동하기 전에 여러분은 반드시 [VirtualBox](https://www.virtualbox.org/wiki/Downloads) 와 [Vagrant](http://www.vagrantup.com/downloads.html)를 설치해야 합니다. Both of these software packages provide easy-to-use visual installers for all popular operating systems.
두가지 소프트웨어 패키지는 모든 운영체제에서 손쉽게 설치할 수 있는 비주얼 프로그램을 제공합니다. 

#### VMware
#### VMware

In addition to VirtualBox, Homestead also supports VMware. VirtualBox 에 더하여, 홈스테드는 VMware 도 지원하고 있습니다. To use the VMware provider, you will need to purchase both VMware Fusion / Desktop and the [VMware Vagrant plug-in](http://www.vagrantup.com/vmware). VMware 프로바이더를 사용하려면 VMware Fusion / Desktop 과 [VMware Vagrant plug-in](http://www.vagrantup.com/vmware)을 구매해야 합니다. VMware provides much faster shared folder performance out of the box. VMware 는 별다른 설정없이도 더 빠른 공유폴더 퍼포먼스를 제공합니다. 

### Adding The Vagrant Box
### Vagrant Box 추가하기

Once VirtualBox / VMware and Vagrant have been installed, you should add the `laravel/homestead` box to your Vagrant installation using the following command in your terminal. VirtualBox / VMware 그리고 Vagrant 가 설치되었다면, 터미널에서 다음의 명령어를 통해서 인스톨된 Vagrant에 `laravel/homestead` box 를 추가해야 합니다.  It will take a few minutes to download the box, depending on your Internet connection speed: box를 다운로드 하는 것은 인터넨 연결 속도에 따라 몇분 정도 소요됩니다. 

	vagrant box add laravel/homestead

If this command fails, you may have an old version of Vagrant that requires the full URL: 이 명령어가 실패하면 전체 URL을 지정해야하는 이전 버전의 Vagrant를 사용할 수 있습니다. 

	vagrant box add laravel/homestead https://atlas.hashicorp.com/laravel/boxes/homestead

### Installing Homestead
### 홈스테드 설치하기

If you do not want to install PHP on your local machine, you may install Homestead manually by simply cloning the repository. Consider cloning the repository into a `Homestead` folder within your "home" directory, as the Homestead box will serve as the host to all of your Laravel (and PHP) projects:

Git 저장소를 복제하여 수동으로 홈스테드를 설치할 수 있습니다. 여러분의 “home” 디렉토리안에 `Homestead` 폴더로 저장소를 복제하면 홈스테드 box가 라라벨(그리고 PHP) 프로젝트의 모든 호스트 역할을 할 것입니다. 

	git clone https://github.com/laravel/homestead.git Homestead

Once you have cloned the Homestead CLI tool, run the `bash init.sh` command from the Homestead directory to create the `Homestead.yaml` configuration file: 홈스테드 CLI 툴을 클로닝한 뒤, 홈스테드 디렉토리에서 `bash init.sh` 명령어를 통해서 `Homestead.yaml` 설정 파일을 생성할 수 있습니다. 

	bash init.sh

The `Homestead.yaml` file will be placed in your `~/.homestead` directory. `Homestead.yaml`파일은 여러분의 `~/.homestead` 디렉토리에 저장됩니다. 

### Configure Your Provider
### 프로바이더 설정하기

The `provider` key in your `Homestead.yaml` file indicates which Vagrant provider should be used: `virtualbox`, `vmware_fusion` (Mac OS X) or `vmware_workstation` (Windows). You may set this to whichever provider you prefer. `Homestead.yaml` 파일의 `provider` 키는 `virtualbox`, `vmware_fusion`(Mac OS X)와 `vmware_workstation` (Windows)  중 어느 것을 사용할 것인지를 알려줍니다. You may set this to whichever provider you prefer. 여러분이 원하는대로 프로바이더를 설정할 수 있습니다.  

	provider: virtualbox

### Set Your SSH Key
### SSH 키 설정하기

Next, you should edit the `Homestead.yaml` file. 다음으로 `Homestead.yaml`파일을 편집해야 합니다. In this file, you can configure the path to your public SSH key, as well as the folders you wish to be shared between your main machine and the Homestead virtual machine. 이 파일에서 SSH 공개 키의 경로를 설정할 수 있으며, 또한 메인 머신과 홈스테드 가상 머신 사이에서 공유할 폴더를 지정할 수 있습니다.

Don't have an SSH key? SSH 키가 없습니까? On Mac and Linux, you can generally create an SSH key pair using the following command: 맥 과 리눅스에서는 일반적으로 다음의 명령어를 통해서 SSH 키 페어를 생성할 수 있습니다. 

	ssh-keygen -t rsa -C "you@homestead"

On Windows, you may install [Git](http://git-scm.com/) and use the `Git Bash` shell included with Git to issue the command above. Windows에서는 [Git](http://git-scm.com/) 를 설치하고 Git 명령을 실행하기위한 `Git Bash` 쉘을 사용합니다. Alternatively, you may use [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) and [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 다른 방법으로 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 와[PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 을 사용할 수 있습니다.

Once you have created a SSH key, specify the key's path in the `authorize` property of your `Homestead.yaml` file. SSH 키가 생성되었다면 `Homestead.yaml`파일의 `authorize` 속성에 키의 경로를 지정하십시오. 

### Configure Your Shared Folders
### 공유 폴더 설정하기

The `folders` property of the `Homestead.yaml` file lists all of the folders you wish to share with your Homestead environment. `Homestead.yaml` 파일의 `folders` 속성은 홈스테드 환경과 공유하고자 하는 모든 폴더가 나열되어 있습니다. As files within these folders are changed, they will be kept in sync between your local machine and the Homestead environment. 여러분의 로컬 머신과 홈스테드 환경사이에서 동기화된 이 폴더 안의 파일들이 변경되면  변경사항이 서로 유지됩니다. You may configure as many shared folders as necessary! 필요한 만큼 공유 폴더를 설정하십시오!

To enable [NFS](http://docs.vagrantup.com/v2/synced-folders/nfs.html), just add a simple flag to your synced folder: [NFS](http://docs.vagrantup.com/v2/synced-folders/nfs.html) 를 사용하려면 동기화 폴더에 플래그를 지정하면 됩니다.

	folders:
	    - map: ~/Code
	      to: /home/vagrant/Code
	      type: "nfs"

### Configure Your Nginx Sites
### Ngin 사이트 설정하기

Not familiar with Nginx? No problem. Nginx에 익숙하지 않으신가요? 문제없습니다. The `sites` property allows you to easily map a "domain" to a folder on your Homestead environment. `sites` 속성을 통해 홈스테드  환경의 폴더와 "도메인"을 쉽게 매핑 할 수 있습니다. A sample site configuration is included in the `Homestead.yaml` file. 사이트 샘플이 `Homestead.yaml` 파일에 포함되어 있습니다. Again, you may add as many sites to your Homestead environment as necessary. 여기에서도 여러분의 홈스테드 환경에 필요한만큼 사이트를 추가할 수 있습니다. Homestead can serve as a convenient, virtualized environment for every Laravel project you are working on!  홈스테드는 편리하게, 여러분이 작업을 수행하는 모든 라라벨 프로젝트를 위한 가상 환경을 제공합니다!

You can make any Homestead site use [HHVM](http://hhvm.com) by setting the `hhvm` option to `true`: `hhvm` 옵션을 `true` 로 설정하면 홈스테드 사이트가 [HHVM](http://hhvm.com)를 사용하게 할 수 있습니다.

	sites:
	    - map: homestead.app
	      to: /home/vagrant/Code/Laravel/public
	      hhvm: true

Each site will be accessible by HTTP via port 8000 and HTTPS via port 44300.
각각의 사이트는 HTTP 8000 포트와, HTTPS 44300 포트를 통해서 접속할 수 있습니다.

### Bash Aliases
### Bash 별칭

To add Bash aliases to your Homestead box, simply add to the `aliases` file in the root of the `~/.homestead` directory.
Bash 별칭을 Homestead box에 추가하려면 `~/.homestead` 디렉토리의 루트에 `aliases` 파일을 추가하면 됩니다.

### Launch The Vagrant Box
### Vagrant Box 구동하기

Once you have edited the `Homestead.yaml` to your liking, run the `vagrant up` command from your Homestead directory. `Homestead.yaml` 파일 설정이 끝났다면, 홈스테드 디렉토리에서 `vagrant up` 명령어를 실행하십시오. 

Vagrant will boot the virtual machine, and configure your shared folders and Nginx sites automatically! Vagrant가 가상머신을 구동시키고 공유폴더와 Nginx 사이트를 자동으로 설정할 것입니다. To destroy the machine, you may use the `vagrant destroy --force` command.
가상 머신을 파괴하기 위해서는 `vagrant destroy --force` 명령어를 사용하면 됩니다.  

Don't forget to add the "domains" for your Nginx sites to the `hosts` file on your machine! 여러분이 Nginx 사이트에 설정한 “도메인”을 로컬 머신의 `hosts` 파일에 추가하는 것을 잊지 마십시오. The `hosts` file will redirect your requests for the local domains into your Homestead environment. `hosts` 파일은 로컬 도메인에 대한 여러분의 요청을 홈스테드 환경으로  리다이렉트 시킬 것 입니다. On Mac and Linux, this file is located at `/etc/hosts`. On Windows, it is located at `C:\Windows\System32\drivers\etc\hosts`. 맥과 리눅스에서는 `/etc/hosts`, 윈도우에서는 `C:\Windows\System32\drivers\etc\hosts`에 파일이 있습니다. The lines you add to this file will look like the following: 다음과 같이 도메인을 추가하십시오. 

	192.168.10.10  homestead.app

Make sure the IP address listed is the one you set in your `Homestead.yaml` file. 입력한 IP 주소가 여러분의 `Homestead.yaml` 파일에 설정된 하나 인지 확인하십시오. Once you have added the domain to your `hosts` file, you can access the site via your web browser! `hosts` 파일에 도메인을 추가 하면, 웹브라우저를 통해서 사이트에 액세스 할 수 있습니다!

	http://homestead.app

To learn how to connect to your databases, read on!
다음은 데이터베이스에 연결하는 방법을 배워보겠습니다!

<a name="daily-usage"></a>
## Daily Usage
## 사용 방법

### Connecting Via SSH
### SSH를 통해서 연결하기

To connect to your Homestead environment via SSH, issue the `vagrant ssh` command from your Homestead directory. SSH를 통해서 홈스테드 환경에 접속하려면 홈스테드 디렉토리에서 `vagrant ssh` 명령어를 실행하면 됩니다. 

Since you will probably need to SSH into your Homestead machine frequently, consider creating an "alias" on your host machine to quickly SSH into the Homestead box:
 홈스테드 머신에 자주 접속할 것이기 때문에 손쉽게 홈스테드 머신에 SSH로 접속하기 위해서 호스트 머신에 "별칭"을 만드는 것을 고려해 볼 수 있습니다.

	alias vm="ssh vagrant@127.0.0.1 -p 2222"

Once you create this alias, you can simply use the "vm" command to SSH into your Homestead machine from anywhere on your system. 위와 같이 별칭을 생성하고 나면, 시스템의 어느곳에서도 “vm” 명령어를 통해서 홈스테드 머신에 SSH로 접속할 수 있습니다. 

Alternatively, you can use the `vagrant ssh` command from your Homestead directory.
또한 홈스테드 디렉토리에서 `vagrant ssh` 명령어를 사용할 수도 있습니다. 

### Connecting To Your Databases
### 데이터베이스에 접속하기 

A `homestead` database is configured for both MySQL and Postgres out of the box. 별다른 설정 없이도 `homestead` 데이터베이스는 MySQL과 Postgres 가 설정되어 있습니다. For even more convenience, Laravel's `local` database configuration is set to use this database by default. 보다 편리하게하기 위해 라라벨의 `local` 데이터베이스 설정이 기본적으로 이 데이터베이스를 사용하도록 설정되어 있습니다.

To connect to your MySQL or Postgres database from your main machine via Navicat or Sequel Pro, you should connect to `127.0.0.1` and port 33060 (MySQL) or 54320 (Postgres). Navicat 나 Sequel Pro를 통해서 MySQL 또는 Postgres 데이터베이스에 접속하려면 `127.0.0.1` 의 33060 (MySQL) 또는 54320 (Postgres) 포트에 연결하면 됩니다. The username and password for both databases is `homestead` / `secret`. 데이터베이스 사용자 이름과 암호는 모두 `homestead` / `secret` 입니다. 

> **Note 주의:** You should only use these non-standard ports when connecting to the databases from your main machine. 데이터베이스에 접속할 때는 이러한 표준이 아닌 포트를 사용해야 합니다. You will use the default 3306 and 5432 ports in your Laravel database configuration file since Laravel is running _within_ the Virtual Machine. 라라벨이 가상 머신 안에서 동작하고 있기 때문에 기본적인 3306 과 5432 포트는 라라벨 데이터베이스 설정 파일 안에서 사용할 수 있습니다.  

### Adding Additional Sites
### 추가적인 사이트 지정하기

Once your Homestead environment is provisioned and running, you may want to add additional Nginx sites for your Laravel applications. 홈스테드 환경이 준비되어 구성되고 난 뒤에 라라벨 애플리케이션에 추가적인 Nginx 사이트를 구성하기를 원할 수도 있습니다. You can run as many Laravel installations as you wish on a single Homestead environment.  하나의 홈스테드 환경 안에서 여러개의 라라벨을 설치하여 작동 시킬수도 있습니다. There are two ways to do this: First, you may simply add the sites to your `Homestead.yaml` file and then run `vagrant provision` from your Homestead directory. 여기에는 두가지 방법이 있습니다: 첫번째 방법은 간단하게 `Homestead.yaml` 파일에 사이트를 추가하고 홈스테드 디렉토리에서 `vagrant provision` 을 실행하는 것입니다. 

> **Note 주의:** This process is destructive. When running the `provision` command, your existing databases will be destroyed and recreated. 이 명령어는 내부에 변경사항이 있습니다. `provision` 명령어가 실행되면 이미 존재하는 데이터베이스가 삭제되고 다시 생성됩니다. 

Alternatively, you may use the `serve` script that is available on your Homestead environment. 다른 방법으로는 홈스테드 환경에서 `serve` 스크립트를 사용하는 방법입니다. To use the `serve` script, SSH into your Homestead environment and run the following command: `serve` 스크립트를 사용하기 위해서는 홈스테드 환경에 SSH로 접속하여 다음과 같이 명령어를 실행하면 됩니다. 

	serve domain.app /home/vagrant/Code/path/to/public/directory 80

> **Note 주의:** After running the `serve` command, do not forget to add the new site to the `hosts` file on your main machine! `serve` 명령어를 실행하고 로컬머신의 `hosts` 파일에 새로운 사이트를 추가하는 것을 잊지 마십시오!

<a name="ports"></a>
## Ports
## 포트

The following ports are forwarded to your Homestead environment:
다음 포트가 홈스테드 환경에 포트 포워딩되어 있습니다.

- **SSH:** 2222 &rarr; Forwards To 22
- **HTTP:** 8000 &rarr; Forwards To 80
- **HTTPS:** 44300 &rarr; Forwards To 443
- **MySQL:** 33060 &rarr; Forwards To 3306
- **Postgres:** 54320 &rarr; Forwards To 5432

### Adding Additional Ports
### 포트 추가로 지정하기

If you wish, you may forward additional ports to the Vagrant box, as well as specify their protocol: 원한다면 포트뿐만 아니라 프로토콜을 추가로 Vagrant Box에 포워딩하도록 지정 할 수 있습니다.

	ports:
	    - send: 93000
	      to: 9300
	    - send: 7777
	      to: 777
	      protocol: udp

<a name="blackfire-profiler"></a>
## Blackfire Profiler
## Blackfire 프로파일러

[Blackfire Profiler](https://blackfire.io) by SensioLabs automatically gathers data about your code's execution, such as RAM, CPU time, and disk I/O. SensioLabs 의 [Blackfire 프로파일러](https://blackfire.io)는 자동으로 여러분의 코드 수행시간, RAM, CPU 소요시간 그리고 disk I/O와 같은 정보들을 수집해 줍니다. Homestead makes it a breeze to use this profiler for your own applications. 홈스테드는 여러분의 애플리케이션에 이 프로파일러를 매우 쉽게 사용할 수 있습니다. 

All of the proper packages have already been installed on your Homestead box, you simply need to set a Blackfire **Server** ID and token in your `Homestead.yaml` file: 필요한 패키지는 이미 Homestead Box에 설치되어 있기 때문에, `Homestead.yaml` 파일에 Blackfire의 **서버** ID와 토큰을 설정하면 됩니다.

	blackfire:
	    - id: your-server-id
	      token: your-server-token
	      client-id: your-client-id
	      client-token: your-client-token

Once you have configured your Blackfire credentials, re-provision the box using `vagrant provision` from your Homestead directory Blackfire 인증정보를 설정하고 난 뒤에, 여러분의 홈스테드 디렉토리에서 `vagrant provision`을 사용하여 Box 를 다시 실행하십시오. Of course, be sure to review the [Blackfire documentation](https://blackfire.io/getting-started) to learn how to install the Blackfire companion extension for your web browser.  또한 Blackfire 설치와 추가 확장에 대해서는 웹사이트에서 [Blackfire 문서](https://blackfire.io/getting-started)를 참고하십시오. 
