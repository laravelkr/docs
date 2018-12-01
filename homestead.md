# 라라벨 홈스테드

- [시작하기](#introduction)
- [인스톨 & 설정](#installation-and-setup)
    - [첫번째 단계](#first-steps)
    - [홈스테드 설정](#configuring-homestead)
    - [Vagrant Box 구동하기](#launching-the-vagrant-box)
    - [프로젝트별 설치하기](#per-project-installation)
    - [MariaDB 설치하기](#installing-mariadb)
- [사용 방법](#daily-usage)
    - [어디에서나 홈스테드 엑세스하기](#accessing-homestead-globally)
    - [SSH로 접속하기 ](#connecting-via-ssh)
    - [데이터베이스에 접속하기](#connecting-to-databases)
    - [사이트 추가하기](#adding-additional-sites)
    - [Cron 설정하기](#configuring-cron-schedules)
    - [포트 지정](#ports)
- [네트워크 인터페이스](#network-interfaces)
- [홈스테드 업데이트하기](#updating-homestead)
- [이전 버전](#old-versions)

<a name="introduction"></a>
## 시작하기

라라벨은 여러분의 로컬 개발 환경을 포함하여 전체 PHP 개발 경험을 유쾌하게 만들기 위해서 노력하고 있습니다. [Vagrant](https://www.vagrantup.com)은 간단하고 세련되게 가상 머신을 관리하고 준비할 수 있는 방법을 제공합니다. 

라라벨 홈스테드는 공식적으로 여러분이 PHP, 웹서버, 기타 다른 서버 소프트웨어를 설치할 필요가 없는 사전에 준비된 Vagrant box이며 멋진 개발 환경을 제공합니다. 운영체제가 너저분 해지는 것을 걱정할 필요가 없습니다! Vagrant box는 완전히 일회용입니다. 만약 뭔가 잘 되지 않는다면, 여러분은 몇분안에 다시 box를 생성할 수 있습니다. 

홈스테드는 Windows, Mac 그리고 Linux 시스템에서 실행할 수 있으며 Nginx 웹 서버, PHP 7.1, MySQL, Postgres, Redis, Memcached, Node 및 기타 라라벨 애플리케이션 개발에 필요한 멋진 도구들을 모두 포함하고 있습니다. .

> {note} 만약 윈도우를 사용하는 경우, 하드웨어 가상화 (VT-x)를 활성화해야합니다. 이 설정은 일반적으로 BIOS에서 사용할 수 있습니다. 만약 UEFI 시스템에서 Hyper-V 를 사용중이라면 추가적으로 VT-x에 액세스하기 위해 Hyper-V를 비활성화 해야합니다.

<a name="included-software"></a>
### 포함된 소프트웨어

- Ubuntu 16.04
- Git
- PHP 7.0
- Nginx
- MySQL
- MariaDB
- Sqlite3
- Postgres
- Composer
- Node (With Yarn, PM2, Bower, Grunt, and Gulp)
- Redis
- Memcached
- Beanstalkd

<a name="installation-and-setup"></a>
## 설치 & 구성하기

<a name="first-steps"></a>
### 첫번째 단계

라라벨 홈스테드를 구동하기 전에 여러분은 반드시 [VirtualBox 5.1](https://www.virtualbox.org/wiki/Downloads), [VMWare](https://www.vmware.com) 또는 [Parallels](http://www.parallels.com/products/desktop/) 그리고 [Vagrant](https://www.vagrantup.com/downloads.html)를 설치해야 합니다. 이 소프트웨어 패키지들은 모든 운영체제에서 손쉽게 설치할 수 있는 인스톨러 프로그램을 제공합니다. 

VMware 프로바이더를 사용하려면 VMware Fusion / Workstation 과 [VMware Vagrant plug-in](https://www.vagrantup.com/vmware)을 구매해야 합니다. 무료는 아니지만, VMware 의 경우 추가적인 설정 없이도 더 빠른 공유폴더의 퍼포먼스를 제공합니다. 

#### 홈스테드 Vagrant Box 설치하기

VirtualBox / VMware 그리고 Vagrant 가 설치되었다면, 터미널에서 다음의 명령어를 통해서 인스톨된 Vagrant에 `laravel/homestead` box 를 추가해야 합니다. box를 다운로드 하는 것은 인터넨 연결 속도에 따라 몇분 정도 소요됩니다. 

    vagrant box add laravel/homestead

이 명령어가 실패하면, Vagrant 설치가 최신버전인지 확인하십시오.

#### 홈스테드 설치하기

간단하게 Git 저장소를 복제하여 홈스테드를 설치할 수 있습니다. 여러분의 "home" 디렉토리안에 `Homestead` 폴더로 저장소를 복제하면 홈스테드 box가 라라벨 프로젝트의 모든 호스트 역할을 할 것입니다. 

    cd ~

    git clone https://github.com/laravel/homestead.git Homestead

홈스테드 저장소를 복제한 뒤에, 홈스테드 디렉토리에서 `bash init.sh` 명령어를 통해서 `Homestead.yaml` 설정 파일을 생성할 수 있습니다. `~/.homestead`의 숨겨진 디렉토리에 `Homestead.yaml` 파일이 생성될 것입니다. 

    bash init.sh

<a name="configuring-homestead"></a>
### 홈스테드 설정하기

#### 프로바이더 설정하기

`~/.homestead/Homestead.yaml` 파일의 `provider` 키는 `virtualbox`, `vmware_fusion`, `vmware_workstation` 와 `parallels`중 어느 것을 사용할 것인지를 알려줍니다. 여러분이 원하는대로 이 프로바이더를 설정할 수 있습니다.  

    provider: virtualbox

#### 공유폴더 설정하기

`Homestead.yaml` 파일의 `folders` 속성은 홈스테드 환경과 공유하고자 하는 모든 폴더가 나열되어 있습니다. 여러분의 로컬 머신과 홈스테드 환경사이에서 동기화된 이 폴더 안의 파일들이 변경되면  변경사항이 서로 유지됩니다. 필요한 만큼 공유 폴더를 설정하십시오:

    folders:
        - map: ~/Code
          to: /home/vagrant/Code

[NFS](https://www.vagrantup.com/docs/synced-folders/nfs.html) 를 사용하려면 동기화 폴더 설정에 플래그를 지정하면 됩니다.

    folders:
        - map: ~/Code
          to: /home/vagrant/Code
          type: "nfs"

`options` 키 아래 Vagrant의 [동기화 폴더](https://www.vagrantup.com/docs/synced-folders/basic_usage.html)옵션을 나열식으로 전달할 수 있습니다:

    folders:
        - map: ~/Code
          to: /home/vagrant/Code
          type: "rsync"
          options:
              rsync__args: ["--verbose", "--archive", "--delete", "-zz"]
              rsync__exclude: ["node_modules"]

#### Nginx 사이트 설정하기

Nginx에 익숙하지 않으신가요? 문제없습니다. `sites` 속성을 통해 홈스테드 환경의 폴더와 "도메인"을 쉽게 매핑 할 수 있습니다. 사이트 샘플이 `Homestead.yaml` 파일에 포함되어 있습니다. 여기에서도 여러분의 홈스테드 환경에 필요한만큼 사이트를 추가할 수 있습니다. 홈스테드는 편리하게, 여러분이 작업을 수행하는 모든 라라벨 프로젝트를 위한 가상 환경을 제공합니다:

    sites:
        - map: homestead.app
          to: /home/vagrant/Code/Laravel/public

홈스테드 박스가 프로비저닝 된 이후에 `sites` 속성을 변경한다면, 가상머신의 Nginx 설정을 갱신하기 위해서 `vagrant reload --provision` 을 다시 실행시켜야만 합니다.

#### Hosts 파일

여러분은 Nginx 사이트에 설정한 "도메인"을 로컬 머신의 `hosts` 파일에 추가해야 합니다. `hosts` 파일은 여러분의 홈스테드 사이트에 대한 요청을 홈스테드 머신으로 리다이렉트 시킬 것 입니다. 맥과 리눅스에서는 `/etc/hosts`, 윈도우에서는 `C:\Windows\System32\drivers\etc\hosts`에 파일이 있습니다. 다음과 같이 도메인을 추가하십시오. 

    192.168.10.10  homestead.app

입력한 IP 주소가 여러분의 `~/.homestead/Homestead.yaml` 파일에 설정된 목록중 하나인지 확인하십시오. `hosts` 파일에 도메인을 추가 하고 Vagrant box를 구동하면 웹브라우저를 통해서 사이트에 액세스 할 수 있습니다:

    http://homestead.app

<a name="launching-the-vagrant-box"></a>
### Vagrant Box 구동하기

`Homestead.yaml` 파일 설정이 끝났다면, 홈스테드 디렉토리에서 `vagrant up` 명령어를 실행하십시오. Vagrant가 가상머신을 구동시키고 자동으로 공유폴더와 Nginx 사이트를 설정할 것입니다. 

가상 머신을 파괴하기 위해서는 `vagrant destroy --force` 명령어를 사용하면 됩니다.  

<a name="per-project-installation"></a>
### 프로젝트별 설치하기

홈스테드를 전역에 설치하고 전체 프로젝트에서 동일한 홈스테드 박스를 공유하는 대신, 홈스테드 인스턴스를 각각의 관리하는 프로젝트별로 설정할 수 있습니다. 프로젝트마다 홈스테드를 설치하는 것은 `Vagrantfile`을 프로젝트에서 구성하여, `vagrant up`을 할 때 다른 작업을 할 수 있도록 하는 장점이 있습니다. 

홈스테드를 프로젝트에 직접 설치하려면 Composer를 사용합니다.

    composer require laravel/homestead --dev

홈스테드가 설치되고 나면, `make` 명령어를 사용하여 `Vagrantfile` 과 `Homestead.yaml` 파일을 프로젝트 루트 디렉토리에 생성합니다. `make` 명령어는 자동으로 `Homestead.yaml` 파일에서 `sites` 와 `folders` 옵션을 설정할 것입니다. 

Mac / Linux:

    php vendor/bin/homestead make

Windows:

    vendor\\bin\\homestead make

다음으로 터미널에서 `vagrant up` 명령어를 실행하고 브라우저에서 `http://homestead.app` 프로젝트에 엑세스 하십시오. 유의할 것은 `/etc/hosts` 파일에 `homestead.spp` 또는 선택한 도메인을 추가할 필요가 있다는 것입니다. 

<a name="installing-mariadb"></a>
### MariaDB 설치하기

MySQL 대신에 MariaDB를 사용하려면, `Homestead.yaml` 파일에 `mariadb` 옵션을 추가하면 됩니다. 이 옵션은 MySQL을 제거하고 MariaDB를 설치할 것입니다. MariaDB는 MySQL을 바로 대체가능하기 때문에, 애플리케이션에서 `mysql` 데이터베이스 드라이버를 그대로 사용할 수 있습니다.

    box: laravel/homestead
    ip: "192.168.20.20"
    memory: 2048
    cpus: 4
    provider: virtualbox
    mariadb: true

<a name="daily-usage"></a>
## 사용 방법

<a name="accessing-homestead-globally"></a>
### 어디에서나 홈스테드 엑세스하기

때로는 파일시스템의 어디에서나 여러분의 홈스테드 머신에 `vagrant up`을 실행하고자 할 수 있습니다. 이렇게 하기 위해서는 간단한 Bash 함수를 Bash profile 에 추가하여 구현할 수 있습니다. 이 함수는 여러분이 시스템 어디에서나 Vagrant 명령어를 실행할 수 있게 하고 자동으로 홈스테드 설치를 대상으로 하게 할 수 있습니다. 

    function homestead() {
        ( cd ~/Homestead && vagrant $* )
    }

함수에서 설정한 `~/Homestead` 경로가 여러분의 실제 홈스테드 설치 위치인지 확인하십시오. 함수를 설정하면 시스템의 어느 곳에서나 `homestead up` 또는 `homestead ssh`와 같은 명령어를 실행할 수 있습니다. 

<a name="connecting-via-ssh"></a>
### SSH를 통해서 연결하기

홈스테드 디렉토리에서 `vagrant ssh` 명령어를 실행하여 SSH를 통해서 홈스테드 환경에 접속할 수 있습니다.

하지만, 홈스테드 머신에 자주 접속할 것이기 때문에 손쉽게 홈스테드 머신에 SSH로 접속하기 위해서 앞서 이야기한 "함수"을 추가하는 것을 고려해 보십시오.

<a name="connecting-to-databases"></a>
### 데이터베이스 접속하기

별다른 설정 없이도 `homestead` 데이터베이스는 MySQL과 Postgres 가 설정되어 있습니다. 보다 편리하게 하기 위해 라라벨의 `.env` 파일의 설정이 프로임워크에서 별다른 작업 없이도 이 데이터베이스를 사용하도록 설정되어 있습니다.

데이터베이스 클라이언트를 통해서 MySQL 또는 Postgres 데이터베이스에 접속하려면 `127.0.0.1` 의 `33060` (MySQL) 또는 `54320` (Postgres) 포트에 연결하면 됩니다. 데이터베이스 사용자 이름과 암호는 모두 `homestead` / `secret` 입니다. 

> {note} 데이터베이스에 접속할 때는 이러한 표준이 아닌 포트를 사용해야 합니다. 라라벨이 가상 머신 _안에서_ 동작하고 있기 때문에 기본적인 3306 과 5432 포트는 라라벨 데이터베이스 설정 파일 안에서 사용할 수 있습니다.  

<a name="adding-additional-sites"></a>
### 추가적인 사이트 지정하기

홈스테드 환경이 준비되어 구성되고 난 뒤에 라라벨 애플리케이션에 추가적인 Nginx 사이트를 구성하기를 원할 수도 있습니다. 하나의 홈스테드 환경 안에서 여러개의 라라벨을 설치하여 작동 시킬수도 있습니다. 추가적인 사이트를 지정하기 위해서, 간단하게 `~/.homestead/Homestead.yaml` 파일에 사이트를 추가하고 터미널에서 홈스테드 디렉토리로 이동한 뒤 `vagrant reload --provision` 명령어를 실행하면 됩니다. 

<a name="configuring-cron-schedules"></a>
### Cron 스케줄링 설정하기 =

라라벨에서는 단 한번 `schedule:run` 아티즌 명령어를 실행하여 매분마다 [Cron 작업 스케줄링](/docs/{{version}}/scheduling)을 할 수 있도록 하는 편리한 방법을 제공합니다. `schedule:run` 명령어는 `App\Console\Kernel` 클래스에 정의되어 있는 작업 스케줄이 실행되어야 하는지 검사할 것입니다. 

홈스테드의 사이트에서 `schedule:run` 명령을 실행하려면 사이트를 정의 할 때 `schedule` 옵션을 `true` 로 설정하십시오.

    sites:
        - map: homestead.app
          to: /home/vagrant/Code/Laravel/public
          schedule: true

사이트를 위한 Cron 작업은 가상 머신의 `/etc/cron.d` 폴더에 정의됩니다.

<a name="ports"></a>
### 포트지정하기

기본적으로 다음의 포트들이 홈스테드 환경에서 포워딩 설정 되어 있습니다.

- **SSH:** 2222 &rarr; Forwards To 22
- **HTTP:** 8000 &rarr; Forwards To 80
- **HTTPS:** 44300 &rarr; Forwards To 443
- **MySQL:** 33060 &rarr; Forwards To 3306
- **Postgres:** 54320 &rarr; Forwards To 5432

#### 추가적인 포트 포워딩하기

원한다면 포트뿐만 아니라 프로토콜을 추가로 Vagrant Box에 포워딩하도록 지정 할 수 있습니다.

    ports:
        - send: 93000
          to: 9300
        - send: 7777
          to: 777
          protocol: udp

<a name="network-interfaces"></a>
## 네트워크 인터페이스

`Homestead.yaml` 설정의 `networks` 속성은 홈스테드 환경의 네트워크 인터페이스 설정에 관련된 것입니다. 필요한 경우 여러개의 인터페이스를 설정할 수 있습니다:

    networks:
        - type: "private_network"
          ip: "192.168.10.20"

[bridged](https://www.vagrantup.com/docs/networking/public_network.html) 인터페이스를 활성화 하기 위해서는, `bridge` 옵션을 설정하고 네트워크 타입을 `public_network`로 변경해야 합니다:

    networks:
        - type: "public_network"
          ip: "192.168.10.20"
          bridge: "en1: Wi-Fi (AirPort)"

[DHCP](https://www.vagrantup.com/docs/networking/public_network.html)를 활성화하기 위해서는, 설정에서 간단하게 `ip` 옵션을 제거하면 됩니다: 

    networks:
        - type: "public_network"
          bridge: "en1: Wi-Fi (AirPort)"

<a name="updating-homestead"></a>
## 홈스테드 업데이트하기

두가지 간단한 동작으로 홈스테드를 업데이트 할 수 있습니다. 먼저 `vagrant box update` 명령어를 사용하여 Vagrant 박스를 업데이트 해야합니다:

    vagrant box update

그 다음으로 홈스테드 소스 코드를 업데이트 해야 합니다. 저장소를 복제했었다면 복제한 디렉토리에서 간단하게 `git pull origin master` 를 사용할 수 있습니다.

프로젝트의 `composer.json` 파일을 통해서 홈스테드를 설치했었다면, `composer.json` 파일이 `"laravel/homestead": "^4"`를 포함하여 의존성을 업데이트 할 수 있게 해야합니다: 

    composer update

<a name="old-versions"></a>
## 이전 버전

여러분은 `Homestead.yaml` 파일에 다음 라인을 추가하여 손쉽게 사용하는 홈스테드 box 버전을 지정할 수 있습니다: 

    version: 0.6.0

사용 예:

    box: laravel/homestead
    version: 0.6.0
    ip: "192.168.20.20"
    memory: 2048
    cpus: 4
    provider: virtualbox

이전버전의 홈스테드 박스를 사용하는 경우 홈스테드 소스 코드와 호환되는 버전이어야 합니다 다음은 지원되는 박스의 버전과 사용할 홈스테드 소스 코드 버전 및 제공되는 PHP 버전을 보여주는 표입니다:

|   | Homestead Version | Box Version |
|---|---|---|
| PHP 7.0 | 3.1.0 | 0.6.0 |
| PHP 7.1 | 4.0.0 | 1.0.0 |
