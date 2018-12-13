# 라라벨 홈스테드

- [시작하기](#introduction)
- [인스톨 & 설정](#installation-and-setup)
    - [첫번째 단계](#first-steps)
    - [홈스테드 설정](#configuring-homestead)
    - [Vagrant Box 구동하기](#launching-the-vagrant-box)
    - [프로젝트별 설치하기](#per-project-installation)
    - [MariaDB 설치하기](#installing-mariadb)
    - [MongoDB 설치하기](#installing-mongodb)
    - [Elasticsearch 설치하기](#installing-elasticsearch)
    - [Neo4j 설치하기](#installing-neo4j)
    - [별칭 설정](#aliases)
- [사용 방법](#daily-usage)
    - [어디에서나 홈스테드 엑세스하기](#accessing-homestead-globally)
    - [SSH로 접속하기 ](#connecting-via-ssh)
    - [데이터베이스에 접속하기](#connecting-to-databases)
    - [데이터베이스 백업하기](#database-backups)
    - [사이트 추가하기](#adding-additional-sites)
    - [시스템 ENV 환경 변수](#environment-variables)
    - [Cron 설정하기](#configuring-cron-schedules)
    - [Mailhog 설정하기](#configuring-mailhog)
    - [Minio 설정하기](#configuring-minio)
    - [포트 지정](#ports)
    - [환경 공유하기](#sharing-your-environment)
    - [여러 버전의 PHP 사용하기](#multiple-php-versions)
    - [웹서버](#web-servers)
    - [이메일](#mail)
- [네트워크 인터페이스](#network-interfaces)
- [홈스테드 업데이트하기](#updating-homestead)
- [프로바이더 한정 셋팅](#provider-specific-settings)
    - [VirtualBox](#provider-specific-virtualbox)

<a name="introduction"></a>
## 시작하기

라라벨은 여러분의 로컬 개발 환경을 포함하여 전체 PHP 개발 경험을 유쾌하게 만들기 위해서 노력하고 있습니다. [Vagrant](https://www.vagrantup.com)은 간단하고 세련되게 가상 머신을 관리하고 준비할 수 있는 방법을 제공합니다.

라라벨 홈스테드는 공식적으로 여러분이 PHP, 웹서버, 기타 다른 서버 소프트웨어를 설치할 필요가 없는 사전에 준비된 Vagrant box이며 멋진 개발 환경을 제공합니다. 운영체제가 너저분 해지는 것을 걱정할 필요가 없습니다! Vagrant box는 완전히 일회용입니다. 만약 뭔가 잘 되지 않는다면, 여러분은 몇분안에 다시 box를 생성할 수 있습니다.

홈스테드는 Windows, Mac 그리고 Linux 시스템에서 실행할 수 있으며 Nginx 웹 서버, PHP 7.2, PHP 7.1, PHP 7.0, PHP 5.6, MySQL, PostgreSQL, Redis, Memcached, Node 및 기타 라라벨 애플리케이션 개발에 필요한 멋진 도구들을 모두 포함하고 있습니다. .

> {note} 만약 윈도우를 사용하는 경우, 하드웨어 가상화 (VT-x)를 활성화해야합니다. 이 설정은 일반적으로 BIOS에서 사용할 수 있습니다. 만약 UEFI 시스템에서 Hyper-V 를 사용중이라면 추가적으로 VT-x에 액세스하기 위해 Hyper-V를 비활성화 해야합니다.

<a name="included-software"></a>
### 포함된 소프트웨어

- Ubuntu 18.04
- Git
- PHP 7.2
- PHP 7.1
- PHP 7.0
- PHP 5.6
- Nginx
- Apache (Optional)
- MySQL
- MariaDB (Optional)
- Sqlite3
- PostgreSQL
- Composer
- Node (With Yarn, B    git checkout v7.8.0
ower, Grunt, and Gulp)
- Redis
- Memcached
- Beanstalkd
- Mailhog
- Neo4j (Optional)
- MongoDB (Optional)
- Elasticsearch (Optional)
- ngrok
- wp-cli
- Zend Z-Ray
- Go
- Minio

<a name="installation-and-setup"></a>
## 설치 & 구성하기

<a name="first-steps"></a>
### 첫번째 단계

라라벨 홈스테드를 구동하기 전에 여러분은 반드시 [VirtualBox 5.2](https://www.virtualbox.org/wiki/Downloads), [VMWare](https://www.vmware.com) 또는 [Parallels](https://www.parallels.com/products/desktop/), [Hyper-V](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) 그리고 [Vagrant](https://www.vagrantup.com/downloads.html)를 설치해야 합니다. 이 소프트웨어 패키지들은 모든 운영체제에서 손쉽게 설치할 수 있는 인스톨러 프로그램을 제공합니다.

VMware 프로바이더를 사용하려면 VMware Fusion / Workstation 과 [VMware Vagrant plug-in](https://www.vagrantup.com/vmware)을 구매해야 합니다. 무료는 아니지만, VMware 의 경우 추가적인 설정 없이도 더 빠른 공유폴더의 퍼포먼스를 제공합니다.

Parallels 프로바이더를 사용하려면, [Parallels Vagrant 플러그인](https://github.com/Parallels/vagrant-parallels)을 설치해야 합니다. 이는 무료입니다.

[Vagrant 제한사항](https://www.vagrantup.com/docs/hyperv/limitations.html)으로 인해서, Hyper-V 프로바이더에서는 모든 네트워크 설정이 무시됩니다.

#### 홈스테드 Vagrant Box 설치하기

VirtualBox / VMware 그리고 Vagrant 가 설치되었다면, 터미널에서 다음의 명령어를 통해서 인스톨된 Vagrant에 `laravel/homestead` box 를 추가해야 합니다. box를 다운로드 하는 것은 인터넨 연결 속도에 따라 몇분 정도 소요됩니다.

    vagrant box add laravel/homestead

이 명령어가 실패하면, Vagrant 설치가 최신버전인지 확인하십시오.

#### 홈스테드 설치하기

Git 저장소를 복제하여 홈스테드를 설치할 수 있습니다. 여러분의 "home" 디렉토리안에 `Homestead` 폴더로 저장소를 복제하면 홈스테드 box가 라라벨 프로젝트의 모든 호스트 역할을 할 것입니다.

    git clone https://github.com/laravel/homestead.git ~/Homestead

`master` 브랜치는 개발중이라 안정적이지 않을 수도 있기 때문에, 태그를 지정한 버전을 체크아웃 해야합니다. 안정적인 버전은 [GitHub 릴리즈 페이지](https://github.com/laravel/homestead/releases)에서 찾으실 수 있습니다:

    cd ~/Homestead

    // Clone the desired release...
    git checkout v7.16.1

홈스테드 저장소를 복제한 뒤에, 홈스테드 디렉토리에서 `bash init.sh` 명령어를 통해서 `Homestead.yaml` 설정 파일을 생성할 수 있습니다. 홈스테드 디렉토리에 `Homestead.yaml` 파일이 생성될 것입니다.

    // Mac / Linux...
    bash init.sh

    // Windows...
    init.bat

<a name="configuring-homestead"></a>
### 홈스테드 설정하기

#### 프로바이더 설정하기

`Homestead.yaml` 파일의 `provider` 키는 `virtualbox`, `vmware_fusion`, `vmware_workstation`, `parallels`와 `hyperv` 중 어느 것을 사용할 것인지를 알려줍니다. 여러분이 원하는대로 이 프로바이더를 설정할 수 있습니다.

    provider: virtualbox

#### 공유폴더 설정하기

`Homestead.yaml` 파일의 `folders` 속성은 홈스테드 환경과 공유하고자 하는 모든 폴더가 나열되어 있습니다. 여러분의 로컬 머신과 홈스테드 환경사이에서 동기화된 이 폴더 안의 파일들이 변경되면  변경사항이 서로 유지됩니다. 필요한 만큼 공유 폴더를 설정하십시오:

    folders:
        - map: ~/code
          to: /home/vagrant/code

몇개의 사이트만을 생성할 때에는 일반적인 맵핑으로도 충분합니다. 그렇지만, 사이트 수가 계속 증가하는 경우, 성능 문제를 겪을 수도 있습니다. 이 문제는 머신의 성능이 좋지 않거나, 프로젝트의 파일이 많은 경우에 아주 고통스럽습니다. 이러한 문제를 겪는다면, 프로젝트를 개별 Vagrant 폴더에 맵핑하십시오:

    folders:
        - map: ~/code/project1
          to: /home/vagrant/code/project1

        - map: ~/code/project2
          to: /home/vagrant/code/project2

[NFS](https://www.vagrantup.com/docs/synced-folders/nfs.html)를 사용하기 위해서는 동기화 폴더 설정에 플래그를 더해주면 됩니다:

    folders:
        - map: ~/code
          to: /home/vagrant/code
          type: "nfs"

> {note} NFS를 사용할 때에는, [vagrant-bindfs](https://github.com/gael-ian/vagrant-bindfs) 플러그인을 설치해야만 합니다. 이 플러그인은 홈스테드 box안에서 파일과 디렉토리를 위한 올바른 사용자와 권한을 관리해줍니다.

`options` 키 아래 Vagrant의 [동기화 폴더](https://www.vagrantup.com/docs/synced-folders/basic_usage.html)옵션을 나열식으로 전달할 수 있습니다:

    folders:
        - map: ~/code
          to: /home/vagrant/code
          type: "rsync"
          options:
              rsync__args: ["--verbose", "--archive", "--delete", "-zz"]
              rsync__exclude: ["node_modules"]

#### Nginx 사이트 설정하기

Nginx에 익숙하지 않으신가요? 문제없습니다. `sites` 속성을 통해 홈스테드 환경의 폴더와 "도메인"을 쉽게 매핑 할 수 있습니다. 사이트 샘플이 `Homestead.yaml` 파일에 포함되어 있습니다. 여기에서도 여러분의 홈스테드 환경에 필요한만큼 사이트를 추가할 수 있습니다. 홈스테드는 편리하게, 여러분이 작업을 수행하는 모든 라라벨 프로젝트를 위한 가상 환경을 제공합니다:

    sites:
        - map: homestead.test
          to: /home/vagrant/code/Laravel/public

홈스테드 박스가 프로비저닝 된 이후에 `sites` 속성을 변경한다면, 가상머신의 Nginx 설정을 갱신하기 위해서 `vagrant reload --provision` 을 다시 실행시켜야만 합니다.

#### Hosts 파일


여러분은 Nginx 사이트에 설정한 "도메인"을 로컬 머신의 `hosts` 파일에 추가해야 합니다. `hosts` 파일은 여러분의 홈스테드 사이트에 대한 요청을 홈스테드 머신으로 리다이렉트 시킬 것 입니다. 맥과 리눅스에서는 `/etc/hosts`, 윈도우에서는 `C:\Windows\System32\drivers\etc\hosts`에 파일이 있습니다. 다음과 같이 도메인을 추가하십시오.

    192.168.10.10  homestead.test

입력한 IP 주소가 여러분의 `Homestead.yaml` 파일에 설정된 목록중 하나인지 확인하십시오. `hosts` 파일에 도메인을 추가 하고 Vagrant box를 구동하면 웹브라우저를 통해서 사이트에 액세스 할 수 있습니다:

    http://homestead.test

<a name="launching-the-vagrant-box"></a>
### Vagrant Box 구동하기

`Homestead.yaml` 파일 설정이 끝났다면, 홈스테드 디렉토리에서 `vagrant up` 명령어를 실행하십시오. Vagrant가 가상머신을 구동시키고 자동으로 공유폴더와 Nginx 사이트를 설정할 것입니다.

가상 머신을 제거하기 위해서는 `vagrant destroy --force` 명령어를 사용하면 됩니다.

<a name="per-project-installation"></a>
### 프로젝트별 설치하기

홈스테드를 전역에 설치하고 전체 프로젝트에서 동일한 홈스테드 박스를 공유하는 대신, 홈스테드 인스턴스를 각각의 관리하는 프로젝트별로 설정할 수 있습니다. 프로젝트마다 홈스테드를 설치하는 것은 `Vagrantfile`을 프로젝트에서 구성하여, `vagrant up`을 할 때 다른 작업을 할 수 있도록 하는 장점이 있습니다.

홈스테드를 프로젝트에 직접 설치하려면 Composer-컴포저를 사용합니다.

    composer require laravel/homestead --dev

홈스테드가 설치되고 나면, `make` 명령어를 사용하여 `Vagrantfile` 과 `Homestead.yaml` 파일을 프로젝트 루트 디렉토리에 생성합니다. `make` 명령어는 자동으로 `Homestead.yaml` 파일에서 `sites` 와 `folders` 옵션을 설정할 것입니다.

Mac / Linux:

    php vendor/bin/homestead make

Windows:

    vendor\\bin\\homestead make

다음으로 터미널에서 `vagrant up` 명령어를 실행하고 브라우저에서 `http://homestead.test` 프로젝트에 엑세스 하십시오. 유의할 것은 `/etc/hosts` 파일에 `homestead.test` 또는 선택한 도메인을 추가할 필요가 있다는 것입니다.

<a name="installing-mariadb"></a>
### MariaDB 설치하기

MySQL 대신에 MariaDB를 사용하고자 한다면, `Homestead.yaml` 파일에 `mariadb` 옵션을 추가하면 됩니다. 이 옵션은 MySQL을 제거하고 MariaDB를 설치할 것입니다. MariaDB는 MySQL을 바로 대체가능하기 때문에, 애플리케이션에서 `mysql` 데이터베이스 드라이버를 그대로 사용할 수 있습니다.

    box: laravel/homestead
    ip: "192.168.10.10"
    memory: 2048
    cpus: 4
    provider: virtualbox
    mariadb: true

<a name="installing-mongodb"></a>
### MongoDB 설치하기

MongoDB 커뮤니티 에디션을 설치하기 위해서는, `Homestead.yaml` 파일에 다음의 설정옵션을 추가해야합니다:

    mongodb: true

기본 MonogoDB 설치에는 `homestead` 를 계정 이름으로, `secret`을 패스워드로 지정됩니다.

<a name="installing-elasticsearch"></a>
### Elasticsearch 설치하기

Elasticsearch를 설치하려면 `Homestead.yaml` 파일에 `elasticsearch` 옵션을 추가하고 지원버전을 메이저 버전 또는 (major.minor.patch) 형태의 정확한 버전을 지정하면 됩니다. 기본설치는 `homestead` 이름으로 클러스터가 생성됩니다. Elasticsearch 에 운영체제 메모리의 절반 이상을 할당하면 안됩니다. 따라서 홈스테드 머신에 Elasticsearch 헬당한 것의 두배 이상의 메모리가 있는지 확인하십시오:

    box: laravel/homestead
    ip: "192.168.10.10"
    memory: 4096
    cpus: 4
    provider: virtualbox
    elasticsearch: 6

> {tip} 관련 설정을 변경하려면 [Elasticsearch 매뉴얼](https://www.elastic.co/guide/en/elasticsearch/reference/current)을 참고하십시오.

<a name="installing-neo4j"></a>
### Neo4j 설치하기

[Neo4j](https://neo4j.com/)는 그래프 데이터베이스 시스템입니다. Neo4j 커뮤니티 에디션을 설치하기 위해서는, `Homestead.yaml` 파일에 다음의 설정 옵션을 추가하면 됩니다:

    neo4j: true

기본적인 Neo4j 설치에는 `homestead` 를 계정 이름으로 `secret`을 패스워드로 지정됩니다. Neo4j에 엑세스 하기 위해서는, 브라우저에서 `http://homestead.test:7474` 를 통해서 접근하면 됩니다. 포트 `7687` (Bolt), `7474` (HTTP), `7473` (HTTPS) 를 통해서 Neo4j 에 접근할 수 있습니다.

<a name="aliases"></a>
### 별칭 설정

홈스테드 디렉토리에 있는 `aliases` 파일을 수정하여 홈스테드 머신에 Bash 별칭을 추가할 수 있습니다:

    alias c='clear'
    alias ..='cd ..'

`aliases` 파일을 수정한 뒤에는 `vagrant reload --provision` 명령어를 사용하여 홈스테드 머신을 다시 프로비저닝 하십시오. 이렇게 하면 머신에서 새로운 별칭이 사용가능해집니다.

<a name="daily-usage"></a>
## 사용 방법

<a name="accessing-homestead-globally"></a>
### 어디에서나 홈스테드 엑세스하기

때로는 파일시스템의 어디에서나 여러분의 홈스테드 머신에 `vagrant up`을 실행하고자 할 수 있습니다. 이렇게 하기 위해서는 맥 / 리눅스 시스템에서 Bash 함수를 Bash profile 에 추가하여 구현할 수 있습니다. 윈도우에서는 `PATH`에 "배치 파일을 추가하여 수행할 수 있습니다. 이 스크립트는 여러분이 시스템 어디에서나 Vagrant 명령어를 실행할 수 있게 하고 자동으로 홈스테드 설치를 대상으로 하게 할 수 있습니다.

#### 맥 / 리눅스

    function homestead() {
        ( cd ~/Homestead && vagrant $* )
    }

Make sure to tweak the `~/Homestead` path in the function to the location of your actual Homestead installation. Once the function is installed, you may run commands like `homestead up` or `homestead ssh` from anywhere on your system.

함수에서 설정한 `~/Homestead` 경로가 여러분의 실제 홈스테드 설치 위치인지 확인하십시오. 함수를 설정하면 시스템의 어느 곳에서나 `homestead up` 또는 `homestead ssh`와 같은 명령어를 실행할 수 있습니다.

#### 윈도우

다음 내용을 가지는 `homestead.bat` 배치 파일을 원하는 곳에다가 생성합니다:

    @echo off

    set cwd=%cd%
    set homesteadVagrant=C:\Homestead

    cd /d %homesteadVagrant% && vagrant %*
    cd /d %cwd%

    set cwd=
    set homesteadVagrant=

스크립트에서 설정한 `C:\Homestead` 경로가 여러분의 실제 홈스테드 설치 위치인지 확인하십시오 파일을 생성한뒤에, 파일의 경로를 `PATH` 에 추가합니다. 시스템의 어떤 경로에서라도 `homestead up` 또는 `homestead ssh` 명령어를 실행할 수 있습니다.

<a name="connecting-via-ssh"></a>
### SSH를 통해서 연결하기

홈스테드 디렉토리에서 `vagrant ssh` 명령어를 실행하여 SSH를 통해서 홈스테드 환경에 접속할 수 있습니다.

하지만, 홈스테드 머신에 자주 접속할 것이기 때문에 손쉽게 홈스테드 머신에 SSH로 접속하기 위해서 앞서 이야기한 "함수"을 추가하는 것을 고려해 보십시오.

<a name="connecting-to-databases"></a>
### 데이터베이스 접속하기

별다른 설정 없이도 `homestead` 데이터베이스는 MySQL과 PostgreSQL 가 설정되어 있습니다. 보다 편리하게 하기 위해 라라벨의 `.env` 파일의 설정이 프로임워크에서 별다른 작업 없이도 이 데이터베이스를 사용하도록 설정되어 있습니다.

데이터베이스 클라이언트를 통해서 MySQL 또는 PostgreSQL 데이터베이스에 접속하려면 `127.0.0.1` 의 `33060` (MySQL) 또는 `54320` (PostgreSQL) 포트에 연결하면 됩니다. 데이터베이스 사용자 이름과 암호는 모두 `homestead` / `secret` 입니다.

> {note} 데이터베이스에 접속할 때는 이러한 표준이 아닌 포트를 사용해야 합니다. 라라벨이 가상 머신 _안에서_ 동작하고 있기 때문에 기본적인 3306 과 5432 포트는 라라벨 데이터베이스 설정 파일 안에서 사용할 수 있습니다.

<a name="database-backups"></a>
### 데이터베이스 백업하기

홈스테드는 Vagrant box 가 종료될 때 자동으로 데이터베이스를 백업합니다. 이 기능을 사용하기 위해서는 Vagrant 2.1.0 이상 버전을 사용해야 합니다. 그 이전버전이라면, `vagrant-triggers` 플러그인을 설치해야 합니다. 자동으로 데이터베이스 백업을 활성화 하려면, 다음의 설정을 `Homestead.yaml` 파일에 추가하면 됩니다:

    backup: true

설정을 하고나면, 홈스테드는 `vagrant destroy` 명령이 실행될 때 데이터베이스를 `mysql_backup` 과 `postgres_backup` 디렉토리로 백업을 내보냅니다. 이 디렉토리는 [프로젝트 별 설치](#per-project-installation)방법을 사용하는 경우에는 루트 디렉토리에, 홈스테드를 클론(복제) 한 경우에는 해당 디렉토리에서 찾을 수 있습니다.

<a name="adding-additional-sites"></a>
### 추가적인 사이트 지정하기

홈스테드 환경이 준비되어 구성되고 난 뒤에 라라벨 애플리케이션에 추가적인 Nginx 사이트를 구성하기를 원할 수도 있습니다. 하나의 홈스테드 환경 안에서 여러개의 라라벨을 설치하여 작동 시킬수도 있습니다. 추가적인 사이트를 지정하기 위해서, `Homestead.yaml` 파일에 사이트를 추가하십시오:

    sites:
        - map: homestead.test
          to: /home/vagrant/code/Laravel/public
        - map: another.test
          to: /home/vagrant/code/another/public

Vagrant 가 자동으로 "hosts" 파일을 관리하지 않는다면, 직접 다음의 사이트를 호스트 파일에 추가해야합니다:

    192.168.10.10  homestead.test
    192.168.10.10  another.test

사이트가 추가되면, 홈스테드 디렉토리에서 `vagrant reload --provision` 명령어를 실행하십시오.

<a name="site-types"></a>
#### 사이트 타입

홈스테드는 라라벨이 아닌 프로젝트를 손쉽게 구동할 수 있는 몇가지 사이트 타입을 지원합니다. 예를 들자면, `symfony2` 사이트 타입은 Synfony 애플리케이션을 홈스테드에서 추가할 수 있도록 해줍니다:

    sites:
        - map: symfony2.test
          to: /home/vagrant/code/Symfony/web
          type: "symfony2"

사용가능한 사이트 타입에는 `apache`, `apigility`, `expressive`, `laravel` (기본값), `proxy`, `silverstripe`, `statamic`, `symfony2`, `symfony4`, 그리고 `zf`가 있습니다.

<a name="site-parameters"></a>
#### 사이트 파라미터

추가적인 Nginx `fastcgi_param` 값을 사이트에 설정하려면 `params` 사이트 지시어를 사용하면 됩니다. 예를 들어 `BAR` 라는 값을 가지는 `FOO` 파라미터를 추가해보겠습니다:

    sites:
        - map: homestead.test
          to: /home/vagrant/code/Laravel/public
          params:
              - key: FOO
                value: BAR

<a name="environment-variables"></a>
### 시스템 ENV 환경 변수

`Homestead.yaml` 파일에 시스템 ENV 변수를 지정할 수 있습니다:

    variables:
        - key: APP_ENV
          value: local
        - key: FOO
          value: bar

`Homestead.yaml` 파일을 수정한 뒤에는 `vagrant reload --provision` 를 실행하여 머신을 새롭게 프로비저닝 하십시오. 이 명령어는 설치된 PHP버전에 맞는 PHP-FPM 설정을 업데이트하고 `vagrant` 사용자를 위한 구동환경을 업데이트 합니다.

<a name="configuring-cron-schedules"></a>
### Cron 스케줄링 설정하기

라라벨에서는 단 한번 `schedule:run` 아티즌 명령어를 실행하여 매분마다 [Cron 작업 스케줄링](/docs/{{version}}/scheduling)을 할 수 있도록 하는 편리한 방법을 제공합니다. `schedule:run` 명령어는 `App\Console\Kernel` 클래스에 정의되어 있는 작업 스케줄이 실행되어야 하는지 검사할 것입니다.

홈스테드의 사이트에서 `schedule:run` 명령을 실행하려면 사이트를 정의 할 때 `schedule` 옵션을 `true` 로 설정하십시오.

    sites:
        - map: homestead.test
          to: /home/vagrant/code/Laravel/public
          schedule: true

사이트를 위한 Cron 작업은 가상 머신의 `/etc/cron.d` 폴더에 정의됩니다.

<a name="configuring-mailhog"></a>
### Mailhog 설정하기

Mailhog를 사용하면 실제로 메일을 받는 사람에게 메일을 보내지 않고도 송신하는 메일을 손쉽게 찾아보고 확인 할 수 있습니다. 이를 시작하려면 `.env` 파일을 수정하여 다음의 메일 설정을 사용하십시오:

    MAIL_DRIVER=smtp
    MAIL_HOST=localhost
    MAIL_PORT=1025
    MAIL_USERNAME=null
    MAIL_PASSWORD=null
    MAIL_ENCRYPTION=null

<a name="configuring-minio"></a>
### Minio 설정하기

Minio 는 Amazon S3 API와 호환되는 오픈소스 객체 스토리지 서버입니다. Minio를 설치하려면 `Homestead.yaml` 파일에 다음의 설정 내용을 추가하십시오:

    minio: true

기본적으로 Minio는 9600포트를 사용합니다. `http://homestead:9600/` 을 방문하여 Minio 제어판에 엑세스 할 수 있습니다. 기본 엑세스 키는 `homestead` 이고, 기본 비밀 키는 `secretkey` 입니다. Minio에 엑세스 할 때는 항상 `us-east-1` 리전을 사용해야합니다.

Minio를 사용하기 위해서는 설정 파일 `config/filesystems.php` 에서 S3 디스크 설정을 수정해야합니다. 디스크 구성에 `use_path_style_endpoint` 옵션을 추가하고 `url` 키를 `endpoint` 로 변경해야합니다 :

    's3' => [
        'driver' => 's3',
        'key' => env('AWS_ACCESS_KEY_ID'),
        'secret' => env('AWS_SECRET_ACCESS_KEY'),
        'region' => env('AWS_DEFAULT_REGION'),
        'bucket' => env('AWS_BUCKET'),
        'endpoint' => env('AWS_URL'),
        'use_path_style_endpoint' => true
    ]

마지막으로 `.env` 파일에 다음 옵션이 있는지 확인하십시오 :

    AWS_ACCESS_KEY_ID=homestead
    AWS_SECRET_ACCESS_KEY=secretkey
    AWS_DEFAULT_REGION=us-east-1
    AWS_URL=http://homestead:9600

<a name="ports"></a>
### 포트지정하기

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

#### 추가적인 포트 포워딩하기

원한다면 포트뿐만 아니라 프로토콜을 추가로 Vagrant Box에 포워딩하도록 지정 할 수 있습니다:

    ports:
        - send: 50000
          to: 5000
        - send: 7777
          to: 777
          protocol: udp

<a name="sharing-your-environment"></a>
### 환경 공유하기

가끔씩, 현재 작업하고 있는 환경을 동료들이나, 다른 사람들과 공유하고 싶을 수도 있습니다. Vagrant는 이를 위해서 `vagrant share`를 통해서 환경을 공유할 수 있는 내장 기능이 지원됩니다; 그렇지만, `Homestead.yaml` 파일에 여러개의 사이트가 설정된 경우에는 동작이 원하는대로 작동하지 않습니다.

이 문제를 극복하기 위해서, 홈스테드에 `share` 명령어가 포함되었습니다. 이렇게 하기 위해서 `vagrant ssh` 명령어를 통해서 홈스테드 머신에 SSH 접속을 한 다음 `share homestead.test` 을 실행하십시오. 이렇게 하면 여러분의 `Homestead.yaml` 설정에 있는 `homestead.test` 사이트는 물론 다른 사이트도 공유할 수 있습니다:

    share homestead.test

이 명령어를 실행하면, 여러분은 Ngrok 스크린에서 activity log와 함께 공유한 사이트에 접속할 수 있는 public URL을 확인할 수 있습니다. 커스텀 리전, 서브도메인, Ngrok 실행 옵션을 지정하고자 한다면 `share` 명령어에 이를 추가하면됩니다:

    share homestead.test -region=eu -subdomain=laravel

> {note} 유념할점은, `share` 명령어를 실행중일 때는 가상머신이 인터넷에 노출되고 보안에 노출될 수 있다는 점입니다.

<a name="multiple-php-versions"></a>
### 여러 버전의 PHP 사용하기

> {note} 이 기능은 Nginx에서만 유효합니다.

홈스테드 6부터 동일한 가상 머신에서 여러버전의 PHP를 사용할 수 있습니다. 해당 사이트에서 어떤 버전의 PHP를 사용할지 `Homestead.yaml`에서 지정할 수 있습니다. 사용가능한 PHP 버전은 "5.6", "7.0", "7.1", "7.2"(기본) 입니다:

    sites:
        - map: homestead.test
          to: /home/vagrant/code/Laravel/public
          php: "5.6"

추가적으로 CLI를 통해서 지원되는 PHP 버전들은 다음과 같이 사용할 수 있습니다:

    php5.6 artisan list
    php7.0 artisan list
    php7.1 artisan list
    php7.2 artisan list

<a name="web-servers"></a>
### 웹서버

홈스테드는 기본적으로 Nginx를 웹서버로 사용합니다. 그렇지만, site 타입에 `apache`를 지정해서 아파치를 설치할 수 있습니다. 두 웹서버를 모두 설치할 수 있지만, 동시에 실행할 수는 없습니다. `flip` 쉘 명령어를 사용하여 웹서버를 쉽게 전환할 수 있습니다. `flip` 쉘 명령어는 자동으로 실행되는 웹서버를 멈추고 다른 서버를 구동합니다. 이 명령어을 사용하려면 홈스테드에 SSH 접속을 하고 터미널에 명령어를 실행하면 됩니다:

    flip

<a name="mail"></a>
### 이메일

홈스테드는 기본적으로 `1025` 포트를 사용하는 Postfix 메일 전송 에이전트를 포함하고 있습니다. 따라서 애플리케이션에서 `smtp` 메일드라이버에 `localhost` 와 포트번호 `1025` 를 설정할 수 있습니다. 이렇게 하면 모든 메일은 Postfix로 처리되고 Mailhog 에 기록됩니다. 전달된 이메일을 확인하려면 브라우저에서 [http://localhost:8025](http://localhost:8025)를 열어보십시오.

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

그 다음으로 홈스테드 소스 코드를 업데이트 해야 합니다. 저장소를 복제했었다면 복제한 디렉토리에서 `git pull origin master` 를 사용할 수 있습니다.

프로젝트의 `composer.json` 파일을 통해서 홈스테드를 설치했었다면, `composer.json` 파일이 `"laravel/homestead": "^7"`를 포함하여 의존성을 업데이트 할 수 있게 해야합니다:

    composer update

<a name="provider-specific-settings"></a>
## 프로바이더 지정 셋팅

<a name="provider-specific-virtualbox"></a>
### VirtualBox

#### `natdnshostresolver`

기본적으로, 홈스테드는 `natdnshostresolver` 설정을 `on` 으로 구성합니다. 이렇게 하면 홈스테드가 여러분의 OS의 DNS 셋팅을 사용할 수 있게 해줍니다. 이 동작을 오버라이딩 하고자 한다면, `Homestead.yaml` 파일에 다음 라인을 추가하십시오:

    provider: virtualbox
    natdnshostresolver: off

#### 윈도우에서 심볼릭 링크

윈도우 환경에서 심볼릭 링크가 원하는 대로 동작하지 않는다면, `Vagrantfile` 파일에 다음 블럭을 추가하십시오:

    config.vm.provider "virtualbox" do |v|
        v.customize ["setextradata", :id, "VBoxInternal2/SharedFoldersEnableSymlinksCreate/v-root", "1"]
    end
