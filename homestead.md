# 라라벨 홈스테드(Laravel Homestead)

- [소개](#introduction)
- [포함된 소프트웨어들](#included-software)
- [설치 & 구성하기](#installation-and-setup)
- [사용방법](#daily-usage)
- [포트](#ports)
- [Blackfire 프로파일러](#blackfire-profiler)

<a name="introduction"></a>
## 소개

라라벨은 여러분의 로컬 개발 환경을 포함하여 전체 PHP 개발 경험을 유쾌하게 만들기 위해서 노력하고 있습니다. [Vagrant](http://vagrantup.com)은 간단하고 세련되게 가상 머신을 관리하고 준비할 수 있는 방법을 제공합니다.

라라벨 홈스테드는 공식적으로 여러분이 PHP, HHVM, 웹서버, 기타 다른 서버 소프트웨어를 설치할 필요가 없는 사전에 준비된 Vagrant “Box”이며 멋진 개발 환경을 제공합니다. 운영체제가 너저분 해지는 것을 걱정할 필요가 없습니다! Vagrant box는 완전히 일회용입니다. 만약 뭔가 잘 되지 않는다면, 여러분은 몇분안에 다시 box를 생성할 수 있습니다.

홈스테드는 Windows, Mac 그리고 Linux 시스템에서 실행할 수 있으며 Nginx 웹 서버, PHP5.6, MySQL, Postgres, Redis, Memcached 및 기타 라라벨 어플리케이션 개발에 필요한 멋진 도구들을 모두 포함하고 있습니다. .

> **주의:** 만약 윈도우를 사용하는 경우, 하드웨어 가상화 (VT-x)를 활성화해야 합니다. 이 설정은 일반적으로 BIOS에서 사용할 수 있습니다.

홈스테디드는 현재 Vagrant1.7을 기반으로 구성되고 테스트되어 있습니다.

<!--chak-comment-라라벨-홈스테드(Laravel-Homestead)-소개-->

<a name="included-software"></a>
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
- [Laravel Envoy](/docs/5.0/envoy)
- [Blackfire Profiler](#blackfire-profiler)

<!--chak-comment-라라벨-홈스테드(Laravel-Homestead)-포함된-소프트웨어들-->

<a name="installation-and-setup"></a>
## 설치 & 구성하기

### VirtualBox / VMware와 Vagrant 설치

라라벨 홈스테드를 구동하기 전에 여러분은 반드시 [VirtualBox](https://www.virtualbox.org/wiki/Downloads)와 [Vagrant](http://www.vagrantup.com/downloads.html)를 설치해야 합니다. 두 가지 소프트웨어 패키지는 모든 운영체제에서 손쉽게 설치할 수 있는 비주얼 프로그램을 제공합니다.

#### VMware

VirtualBox에 더하여, 홈스테드는 VMware 도 지원하고 있습니다. VMware 프로바이더를 사용하려면 VMware Fusion / Desktop과 [VMware Vagrant plug-in](http://www.vagrantup.com/vmware)을 구매해야 합니다. VMware는 별다른 설정없이도 더 빠른 공유폴더 퍼포먼스를 제공합니다.

### Vagrant Box 추가하기

VirtualBox / VMware 그리고 Vagrant가 설치되었다면, 터미널에서 다음의 명령어를 통해서 인스톨된 Vagrant에 `laravel/homestead` box를 추가해야 합니다. box를 다운로드 하는 것은 인터넨 연결 속도에 따라 몇분 정도 소요됩니다:

	vagrant box add laravel/homestead

이 명령어가 실패하면 전체 URL을 지정해야하는 이전 버전의 Vagrant를 사용할 수 있습니다:

	vagrant box add laravel/homestead https://atlas.hashicorp.com/laravel/boxes/homestead

### 홈스테드 설치하기

Git 저장소를 복제하여 수동으로 홈스테드를 설치할 수 있습니다. 여러분의 “home” 디렉토리안에 `Homestead` 폴더로 저장소를 복제하면 홈스테드 box가 라라벨(그리고 PHP) 프로젝트의 모든 호스트 역할을 할 것입니다:

	git clone https://github.com/laravel/homestead.git Homestead

홈스테드 CLI 툴을 클로닝한 뒤, 홈스테드 디렉토리에서 `bash init.sh` 명령어를 통해서 `Homestead.yaml` 설정 파일을 생성할 수 있습니다.

	bash init.sh

`Homestead.yaml`파일은 여러분의 `~/.homestead` 디렉토리에 저장됩니다.

### 프로바이더 설정하기

`Homestead.yaml` 파일의 `provider` 키는 `virtualbox`, `vmware_fusion`(Max OS X)와 `vmware_workstation` (Windows) 중 어느 것을 사용할 것인지를 알려줍니다. 여러분이 원하는대로 프로바이더를 설정할 수 있습니다.

	provider: virtualbox

### SSH 키 설정하기

다음으로 `Homestead.yaml`파일을 편집해야 합니다. 이 파일에서 SSH 공개 키의 경로를 설정할 수 있으며, 또한, 메인 머신과 홈스테드 가상 머신 사이에서 공유할 폴더를 지정할 수 있습니다.

SSH 키가 없습니까? 맥과 리눅스에서는 일반적으로 다음의 명령어를 통해서 SSH 키 페어를 생성할 수 있습니다.

	ssh-keygen -t rsa -C "you@homestead"

Windows에서는 [Git](http://git-scm.com/)를 설치하고 Git 명령을 실행하기위한 `Git Bash` 쉘을 사용합니다. 다른 방법으로 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 와[PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)을 사용할 수 있습니다.

SSH 키가 생성되었다면 `Homestead.yaml`파일의 `authorize` 속성에 키의 경로를 지정하십시오.

### 공유 폴더 설정하기

`Homestead.yaml` 파일의 `folders` 속성은 홈스테드 환경과 공유하고자 하는 모든 폴더가 나열되어 있습니다. 여러분의 로컬 머신과 홈스테드 환경사이에서 동기화된 이 폴더 안의 파일들이 변경되면  변경사항이 서로 유지됩니다. 필요한 만큼 공유 폴더를 설정하십시오!

[NFS](http://docs.vagrantup.com/v2/synced-folders/nfs.html)를 사용하려면 동기화 폴더에 플래그를 지정하면 됩니다:

	folders:
	    - map: ~/Code
	      to: /home/vagrant/Code
	      type: "nfs"

### Nginx 사이트 설정하기

`sites` 속성을 통해 홈스테드  환경의 폴더와 "도메인"을 쉽게 매핑 할 수 있습니다. 사이트 샘플이 `Homestead.yaml` 파일에 포함되어 있습니다. 여기에서도 여러분의 홈스테드 환경에 필요한만큼 사이트를 추가할 수 있습니다. 홈스테드는 편리하게, 여러분이 작업을 수행하는 모든 라라벨 프로젝트를 위한 가상 환경을 제공합니다!

`hhvm` 옵션을 `true`로 설정하면 홈스테드 사이트가 [HHVM](http://hhvm.com)를 사용하게 할 수 있습니다.

	sites:
	    - map: homestead.app
	      to: /home/vagrant/Code/Laravel/public
	      hhvm: true

각각의 사이트는 HTTP 8000 포트와, HTTPS 44300 포트를 통해서 접속할 수 있습니다.

### Bash 별칭

Bash 별칭을 Homestead box에 추가하려면 `~/.homestead` 디렉토리의 루트에 `aliases` 파일을 추가하면 됩니다.

### Vagrant Box 구동하기

`Homestead.yaml` 파일 설정이 끝났다면, 홈스테드 디렉토리에서 `vagrant up` 명령어를 실행하십시오.

Vagrant가 가상머신을 구동시키고 공유폴더와 Nginx 사이트를 자동으로 설정할 것입니다. 가상 머신을 파괴하기 위해서는 `vagrant destroy --force` 명령어를 사용하면 됩니다.

여러분이 Nginx 사이트에 설정한 “도메인”을 로컬 머신의 `hosts` 파일에 추가하는 것을 잊지 마십시오. `hosts` 파일은 로컬 도메인에 대한 여러분의 요청을 홈스테드 환경으로 리다이렉트 시킬 것입니다. 맥과 리눅스에서는 `/etc/hosts`, 윈도우에서는 `C:\Windows\System32\drivers\etc\hosts`에 파일이 있습니다. 다음과 같이 도메인을 추가하십시오:

	192.168.10.10  homestead.app

입력한 IP 주소가 여러분의 `Homestead.yaml` 파일에 설정된 하나 인지 확인하십시오. `hosts` 파일에 도메인을 추가 하면, 웹브라우저를 통해서 사이트에 액세스 할 수 있습니다!

	http://homestead.app

다음은 데이터베이스에 연결하는 방법을 배워보겠습니다!

<!--chak-comment-라라벨-홈스테드(Laravel-Homestead)-설치---구성하기-->

<a name="daily-usage"></a>
## 사용 방법

### SSH를 통해서 연결하기

SSH를 통해서 홈스테드 환경에 접속하려면 홈스테드 디렉토리에서 `vagrant ssh` 명령어를 실행하면 됩니다.

홈스테드 머신에 자주 접속할 것이기 때문에 호스트 머신에 "별칭"을 만드는 것을 고려해 볼 수 있습니다:

	alias vm="ssh vagrant@127.0.0.1 -p 2222"

위와 같이 별칭을 생성하고 나면, 시스템의 어느곳에서도 “vm” 명령어를 통해서 홈스테드 머신에 SSH로 접속할 수 있습니다.

또한, 홈스테드 디렉토리에서 `vagrant ssh` 명령어를 사용할 수도 있습니다.

### 데이터베이스에 접속하기

별다른 설정 없이도 `homestead` 데이터베이스는 MySQL과 Postgres가 설정되어 있습니다. 보다 편리하게하기 위해 라라벨의 `local` 데이터베이스 설정이 기본적으로 이 데이터베이스를 사용하도록 설정되어 있습니다.

Navicat 나 Sequel Pro를 통해서 MySQL 또는 Postgres 데이터베이스에 접속하려면 `127.0.0.1`의 33060 (MySQL) 또는 54320 (Postgres) 포트에 연결하면 됩니다. 데이터베이스 사용자 이름과 암호는 모두 `homestead` / `secret`입니다.

> **주의:** 데이터베이스에 접속할 때는 이러한 표준이 아닌 포트를 사용해야 합니다. 라라벨이 가상 머신 안에서 동작하고 있으므로 기본적인 3306과 5432 포트는 라라벨 데이터베이스 설정 파일 안에서 사용할 수 있습니다.

### 추가적인 사이트 지정하기

홈스테드 환경이 준비되어 구성되고 난 뒤에 라라벨 어플리케이션에 추가적인 Nginx 사이트를 구성하기를 원할 수도 있습니다. 하나의 홈스테드 환경 안에서 여러개의 라라벨을 설치하여 작동 시킬수도 있습니다. 여기에는 두 가지 방법이 있습니다: 첫 번째 방법은 간단하게 `Homestead.yaml` 파일에 사이트를 추가하고 홈스테드 디렉토리에서 `vagrant provision`을 실행하는 것입니다.

> **주의:** 이 명령어는 내부에 변경사항이 있습니다. `provision` 명령어가 실행되면 이미 존재하는 데이터베이스가 삭제되고 다시 생성됩니다.

다른 방법으로는 홈스테드 환경에서 `serve` 스크립트를 사용하는 방법입니다. `serve` 스크립트를 사용하기 위해서는 홈스테드 환경에 SSH로 접속하여 다음과 같이 명령어를 실행하면 됩니다:

	serve domain.app /home/vagrant/Code/path/to/public/directory 80

> **주의:** `serve` 명령어를 실행하고 로컬머신의 `hosts` 파일에 새로운 사이트를 추가하는 것을 잊지 마십시오!

<!--chak-comment-라라벨-홈스테드(Laravel-Homestead)-사용-방법-->

<a name="ports"></a>
## 포트

다음 포트가 홈스테드 환경에 포트 포워딩되어 있습니다.

- **SSH:** 2222 &rarr; Forwards To 22
- **HTTP:** 8000 &rarr; Forwards To 80
- **HTTPS:** 44300 &rarr; Forwards To 443
- **MySQL:** 33060 &rarr; Forwards To 3306
- **Postgres:** 54320 &rarr; Forwards To 5432

### 포트 추가로 지정하기

원한다면 포트뿐만 아니라 프로토콜을 추가로 Vagrant Box에 포워딩하도록 지정 할 수 있습니다.

	ports:
	    - send: 93000
	      to: 9300
	    - send: 7777
	      to: 777
	      protocol: udp

<!--chak-comment-라라벨-홈스테드(Laravel-Homestead)-포트-->

<a name="blackfire-profiler"></a>
## Blackfire 프로파일러

SensioLabs의 [Blackfire 프로파일러](https://blackfire.io)는 자동으로 여러분의 코드 수행시간, RAM, CPU 소요시간 그리고 disk I/O와 같은 정보들을 수집해 줍니다. 홈스테드는 여러분의 어플리케이션에 이 프로파일러를 매우 쉽게 사용할 수 있습니다.

필요한 패키지는 이미 Homestead Box에 설치되어 있으므로, `Homestead.yaml` 파일에 Blackfire의 **서버** ID와 토큰을 설정하면 됩니다:

	blackfire:
	    - id: your-server-id
	      token: your-server-token
	      client-id: your-client-id
	      client-token: your-client-token

Blackfire 인증정보를 설정하고 난 뒤에, 여러분의 홈스테드 디렉토리에서 `vagrant provision`을 사용하여 Box를 다시 실행하십시오. 또한, Blackfire 설치와 추가 확장에 대해서는 웹사이트에서 [Blackfire 문서](https://blackfire.io/getting-started)를 참고하십시오.

<!--chak-comment-라라벨-홈스테드(Laravel-Homestead)-Blackfire-프로파일러-->
