# Laravel Valet
# 라라벨 발렛

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [Valet Or Homestead](#valet-or-homestead)
    - [발렛 또는 홈스테드](#valet-or-homestead)
- [Installation](#installation)
- [설치하기](#installation)
    - [Upgrading](#upgrading)
    - [업그레이드하기](#upgrading)
- [Serving Sites](#serving-sites)
- [사이트 동작시키기](#serving-sites)
    - [The "Park" Command](#the-park-command)
    - ["Park" 명령어](#the-park-command)
    - [The "Link" Command](#the-link-command)
    - ["Link" 명령어](#the-link-command)
    - [Securing Sites With TLS](#securing-sites)
    - [TLS를 사용한 안전한 사이트](#securing-sites)
- [Sharing Sites](#sharing-sites)
- [사이트 공유하기](#sharing-sites)
- [Site Specific Environment Variables](#site-specific-environment-variables)
- [사이트 별 환경 변수](#site-specific-environment-variables)
- [Proxying Services](#proxying-services)
- [프록시 서비스](#proxying-services)
- [Custom Valet Drivers](#custom-valet-drivers)
- [사용자 정의-커스텀 발렛 드라이버](#custom-valet-drivers)
    - [Local Drivers](#local-drivers)
    - [로컬 드라이버](#local-drivers)
- [Other Valet Commands](#other-valet-commands)
- [기타 발렛 명령어들](#other-valet-commands)
- [Valet Directories & Files](#valet-directories-and-files)
- [Valet 디렉토리와 파일](#valet-directories-and-files)

<a name="introduction"></a>
## Introduction
## 시작하기

Valet is a Laravel development environment for Mac minimalists. No Vagrant, no `/etc/hosts` file. You can even share your sites publicly using local tunnels. _Yeah, we like it too._

발렛은 Mac에서 가벼움을 선호하는 사람들을 위한 라라벨 개발 환경입니다. Vagrant도, `/etc/hosts` 파일도 필요하지 않습니다. 심지어 로컬 터널을 사용하여 사이트를 공유할 수도 있습니다. _우린 이런걸 좋아합니다._

Laravel Valet configures your Mac to always run [Nginx](https://www.nginx.com/) in the background when your machine starts. Then, using [DnsMasq](https://en.wikipedia.org/wiki/Dnsmasq), Valet proxies all requests on the `*.test` domain to point to sites installed on your local machine.

라라벨 발렛 설정은 여러분의 Mac 머신이 시작할 때 백그라운드에서 항상 [Nginx](https://www.nginx.com/)가 실행되도록 설정합니다. 그러면 [DnsMasq](https://en.wikipedia.org/wiki/Dnsmasq)를 사용하여 발렛은 `*.test` 도메인에 대한 모든 요청을 여러분의 로컬 머신에 설치된 사이트로 지정되도록 프록시로 동작합니다.

In other words, a blazing fast Laravel development environment that uses roughly 7 MB of RAM. Valet isn't a complete replacement for Vagrant or Homestead, but provides a great alternative if you want flexible basics, prefer extreme speed, or are working on a machine with a limited amount of RAM.

다시말해, 대략 7MB 의 RAM을 사용하는 매우 빠른 라라벨 개발 환경입니다. 발렛은 Vagrant 나 홈스테드를 완전히 대체하지는 않지만, 여러분이 유연하고, 속도를 중시하며, 또는 RAM 이 제한되어 있는 머신에서 동작시키는 데에는 훌륭한 대안입니다.

Out of the box, Valet support includes, but is not limited to:

별다른 설정 없이도, 발렛은 다음을 지원하지만, 이게 전부는 아닙니다.

<style>
    #valet-support > ul {
        column-count: 3; -moz-column-count: 3; -webkit-column-count: 3;
        line-height: 1.9;
    }
</style>

<div id="valet-support" markdown="1">
- [Laravel](https://laravel.com)
- [Lumen](https://lumen.laravel.com)
- [Bedrock](https://roots.io/bedrock/)
- [CakePHP 3](https://cakephp.org)
- [Concrete5](https://www.concrete5.org/)
- [Contao](https://contao.org/en/)
- [Craft](https://craftcms.com)
- [Drupal](https://www.drupal.org/)
- [ExpressionEngine](https://www.expressionengine.com/)
- [Jigsaw](https://jigsaw.tighten.co)
- [Joomla](https://www.joomla.org/)
- [Katana](https://github.com/themsaid/katana)
- [Kirby](https://getkirby.com/)
- [Magento](https://magento.com/)
- [OctoberCMS](https://octobercms.com/)
- [Sculpin](https://sculpin.io/)
- [Slim](https://www.slimframework.com)
- [Statamic](https://statamic.com)
- Static HTML
- [Symfony](https://symfony.com)
- [WordPress](https://wordpress.org)
- [Zend](https://framework.zend.com)
</div>

However, you may extend Valet with your own [custom drivers](#custom-valet-drivers).

여러분은 발렛을 고유한 [사용자 정의 드라이버](#custom-valet-drivers)를 통해서 확장할 수 있습니다.

<a name="valet-or-homestead"></a>
### Valet Or Homestead
### 발렛 또는 홈스테드

As you may know, Laravel offers [Homestead](/docs/{{version}}/homestead), another local Laravel development environment. Homestead and Valet differ in regards to their intended audience and their approach to local development. Homestead offers an entire Ubuntu virtual machine with automated Nginx configuration. Homestead is a wonderful choice if you want a fully virtualized Linux development environment or are on Windows / Linux.

이미 알고 있다시피, 라라벨은 또다른 라라벨 개발 환경인 [홈스테드](/docs/{{version}}/homestead)를 제공하고 있습니다. 홈스테드와 발렛은 이용자의 로컬 개발에 대한 목적과 방식이 다릅니다. 홈스테드는 자동화된 Nginx 설정과 함께 전체 우분투 가상 머신 환경을 제공합니다. 홈스테드는 리눅스 개발 환경에 대한 전체 가상화를 원하거나, 윈도우 / 리눅스 환경에서라면 아주 멋진 선택이 됩니다.

Valet only supports Mac, and requires you to install PHP and a database server directly onto your local machine. This is easily achieved by using [Homebrew](https://brew.sh/) with commands like `brew install php` and `brew install mysql`. Valet provides a blazing fast local development environment with minimal resource consumption, so it's great for developers who only require PHP / MySQL and do not need a fully virtualized development environment.

발렛은 Mac 만을 지원하고, 그리고 여러분의 로컬 머신에 직접 PHP 와 데이터베이스 서버를 설치할 필요가 있습니다. [Homebrew](https://brew.sh/)를 사용해서 `brew install php` 그리고 `brew install mysql`와 같은 명령어를 실행하면 손쉽게 설치가 가능합니다. 발렛은 최소한의 리소스를 사용하여 매우 빠른 로컬 개발 환경을 제공합니다, 따라서 단지 PHP / MySQL 그리고 전체 가상 개발 환경이 필요하지 않은 개발자에게 적합합니다.

Both Valet and Homestead are great choices for configuring your Laravel development environment. Which one you choose will depend on your personal taste and your team's needs.

Valet과 Homestead는 모두 Laravel 개발 환경을 구성하기위한 훌륭한 선택입니다. 선택은 개인적인 취향과 팀의 필요에 따라 달라질 것입니다.

<a name="installation"></a>
## Installation
## 설치하기

**Valet requires macOS and [Homebrew](https://brew.sh/). Before installation, you should make sure that no other programs such as Apache or Nginx are binding to your local machine's port 80.**

**발렛은 macOS와 [Homebrew](https://brew.sh/)를 필요로 합니다. 설치하기 전에, 여러분은 Apache 또는 Nginx 가 로컬 머신의 80번 포트를 바인딩 하지 않고 있다는 것을 확인해야 합니다.**

<div class="content-list" markdown="1">
- Install or update [Homebrew](https://brew.sh/) to the latest version using `brew update`.
- Install PHP 7.4 using Homebrew via `brew install php`.
- Install [Composer](https://getcomposer.org).
- Install Valet with Composer via `composer global require laravel/valet`. Make sure the `~/.composer/vendor/bin` directory is in your system's "PATH".
- Run the `valet install` command. This will configure and install Valet and DnsMasq, and register Valet's daemon to launch when your system starts.
</div>

<div class="content-list" markdown="1">
- [Homebrew](https://brew.sh/) 설치하거나 `brew update`를 사용하여 최신 버전으로 업데이트 하십시오.
- `brew install php` Homebrew 명령어를 사용하여 PHP7.3을 설치하십시오.
- [Composer](https://getcomposer.org) 설치.
- `composer global require laravel/valet` 명령어를 사용하여 컴포저로 발렛을 설치하십시오. 여러분 시스템의 "PATH" 에 `~/.composer/vendor/bin` 디렉토리가 들어 있는지 확인하십시오.
- `valet install` 명령어를 실행하십시오. 이 명령어는 발렛과 DnsMasq 를 설치하고 설정하여 발렛 데몬을 여러분의 시스템이 시작할 때 구동되도록 등록할 것입니다.
</div>

Once Valet is installed, try pinging any `*.test` domain on your terminal using a command such as `ping foobar.test`. If Valet is installed correctly you should see this domain responding on `127.0.0.1`.

발렛이 설치되고나면, 터미널에서 `ping foobar.test` 와 같은 명령어를 사용하여 아무 `*.test` 도메인으로 핑을 시도해보십시오. 발렛이 올바르게 설치되었다면 `127.0.0.1` 로 부터 응답을 확인할 수 있어야만 합니다.

Valet will automatically start its daemon each time your machine boots. There is no need to run `valet start` or `valet install` ever again once the initial Valet installation is complete.

발렛은 여러분의 머신이 매번 부팅될 때 자동적으로 데몬이 시작될 것입니다. 처음 발렛이 설치되어 완료되고 나면 별도의 `valet start` 나 `valet install` 을 실행할 필요가 없습니다.

#### Using Another Domain
#### 다른 도메인 사용하기

By default, Valet serves your projects using the `.test` TLD. If you'd like to use another domain, you can do so using the `valet tld tld-name` command.

기본적으로 발렛은 프로젝트를 `.test` TLD(Top Level Domain)를 프로젝트 에 제공합니다. 다른 도메인을 사용하고자 한다면, `valet tld tld-name` 명령어를 사용하면 됩니다.

For example, if you'd like to use `.app` instead of `.test`, run `valet tld app` and Valet will start serving your projects at `*.app` automatically.

예를 들어, `.test` 대신에 `.app` 을 사용하고자 한다면 `valet tld app` 을 실행하면 발렛이 `*.app` 을 프로젝트에 자동적으로 제공하게 될것입니다.

#### Database
#### 데이터베이스

If you need a database, try MySQL by running `brew install mysql@5.7` on your command line. Once MySQL has been installed, you may start it using the `brew services start mysql@5.7` command. You can then connect to the database at `127.0.0.1` using the `root` username and an empty string for the password.

데이터베이스가 필요하다면, 커맨드 라인에서 `brew install mysql@5.7`를 실행하여 MySQL를 설치해 보십시오. MySQL가 설치되고 나면, `brew services start mysql@5.7` 명령어를 통해서 서비스를 시작할 수 있습니다. 그렇게 하면 `127.0.0.1` 서버에 `root` 계정으로 패스워드 없이 접속 할 수 있습니다.

#### PHP Versions
#### PHP 버전

Valet allows you to switch PHP versions using the `valet use php@version` command. Valet will install the specified PHP version via Brew if it is not already installed:

Valet은 `valet use php@version` 명령을 사용하여 PHP 버전을 전환 할 수있게합니다. Valet은 Brew를 통해 지정된 PHP 버전을 아직 설치하지 않은 경우 설치합니다.

    valet use php@7.2
    
    valet use php

> {note} Valet only serves one PHP version at a time, even if you have multiple PHP versions installed.

> {note} Valet는 여러 개의 PHP 버전이 설치되어 있어도 한 번에 하나의 PHP 버전 만 제공합니다.

#### Resetting Your Installation
#### 설치 재설정

If you are having trouble getting your Valet installation to run properly, executing the `composer global update` command followed by `valet install` will reset your installation and can solve a variety of problems. In rare cases it may be necessary to "hard reset" Valet by executing `valet uninstall --force` followed by `valet install`.

Valet 설치 후 제대로 실행되지 않는 경우 `composer global update` 명령과 `valet install`을 실행하면 설치가 재설정되고 다양한 문제를 해결할 수 있습니다. 드문 경우지만 `valet uninstall --force`와 `valet install`을 실행하여 Valet를 "하드 리셋"해야 할 수도 있습니다.

<a name="upgrading"></a>
### Upgrading
### 업그레이드 하기

You may update your Valet installation using the `composer global update` command in your terminal. After upgrading, it is good practice to run the `valet install` command so Valet can make additional upgrades to your configuration files if necessary.

터미널에서 `composer global update` 명령어를 사용하여 Valet 설치파일을 업데이트 할 수 있습니다. 업그레이드가 진행되면, `valet install` 명령어를 실행시켜 필요한 경우 설정파일에 추가적인 업그레이드가 진행되도록 하십시오.

<a name="serving-sites"></a>
## Serving Sites
## 사이트 동작시키기

Once Valet is installed, you're ready to start serving sites. Valet provides two commands to help you serve your Laravel sites: `park` and `link`.

발렛이 설치되고 나면, 사이트를 동작시킬 준비가된 것입니다. 발렛은 라라벨 사이트를 구동하는데 도움을 줄 수 있는 `park` 와 `link` 두가지 명령어를 제공합니다.

<a name="the-park-command"></a>
#### The `park` Command
#### `park` 명령어

<div class="content-list" markdown="1">
- Create a new directory on your Mac by running something like `mkdir ~/Sites`. Next, `cd ~/Sites` and run `valet park`. This command will register your current working directory as a path that Valet should search for sites.
- Next, create a new Laravel site within this directory: `laravel new blog`.
- Open `http://blog.test` in your browser.
</div>

<div class="content-list" markdown="1">
- Mac 에 `mkdir ~/Sites`와 같은 명령어를 실행하여 새로운 디렉토리를 생성합니다. 다음으로, `cd ~/Sites` 와 `valet park`을 실행합니다. 이 명령어는 현재 작업 디렉토리를 사이트로 접속했을 때 발렛이 찾게 되는 디렉토리로 등록합니다.
- 다음으로 이 디렉토리에서 새로운 라라벨 사이트를 생성합니다. `laravel new blog`.
- 브라우저에서 `http://blog.test` 사이트를 열어서 확인합니다.
</div>

**That's all there is to it.** Now, any Laravel project you create within your "parked" directory will automatically be served using the `http://folder-name.test` convention.

**여기까지가 전부 입니다.** 이제 여러분이 "parked(지정한)" 디렉토리 안에 생성된 라라벨 프로젝트는 자동으로 `http://folder-name.test` 형태로 접속할 수 있습니다.

<a name="the-link-command"></a>
#### The `link` Command
#### `link` 명령어

The `link` command may also be used to serve your Laravel sites. This command is useful if you want to serve a single site in a directory and not the entire directory.

`link` 명령어는 사이트를 동작시킬 때 사용됩니다. 이 명령어는 전체 디렉토리가 아니라 하나의 디렉토리 안에서 한개의 사이트를 제공하기를 원할 때 유용합니다.

<div class="content-list" markdown="1">
- To use the command, navigate to one of your projects and run `valet link app-name` in your terminal. Valet will create a symbolic link in `~/.config/valet/Sites` which points to your current working directory.
- After running the `link` command, you can access the site in your browser at `http://app-name.test`.
</div>

<div class="content-list" markdown="1">
- 명령어를 사용하기 위해서, 프로젝트 중 하나의 디렉토리에 대해서 터미널에서 `valet link app-name` 을 실행합니다. 발렛은 현재 작업 디렉토리를 `~/.config/valet/Sites`가 지정하도록 심볼릭 링크를 생성할 것입니다.
- `link` 명령어를 실행한 다음에, 브라우저에서 `http://app-name.test` 로 접속할 수 있습니다.
</div>

To see a listing of all of your linked directories, run the `valet links` command. You may use `valet unlink app-name` to destroy the symbolic link.

링크로 연결된 디렉토리들의 모든 목록을 확인하고자 한다면, `valet links` 명령어를 실행하십시오. `valet unlink app-name`는 디렉토리에 대한 심볼릭 링크를 제거하는데 사용할 수 있습니다.

> {tip} You can use `valet link` to serve the same project from multiple (sub)domains. To add a subdomain or another domain to your project run `valet link subdomain.app-name` from the project folder.

> {tip} `valet link` 를 동일한 프로젝트에 대해서 다수의 (서브) 도메인을 통해서 사이트가 동작하도록 할 수 있습니다. 프로젝트에 서브도메인이나, 다른 도메인을 추가하려면 프로젝트 폴더에서 `valet link subdomain.app-name` 을 실행하십시오.

<a name="securing-sites"></a>
#### Securing Sites With TLS
#### TLS를 사용한 안전한 사이트

By default, Valet serves sites over plain HTTP. However, if you would like to serve a site over encrypted TLS using HTTP/2, use the `secure` command. For example, if your site is being served by Valet on the `laravel.test` domain, you should run the following command to secure it:

기본적으로 발렛은 사이트를 일반적인 HTTP 를 통해서 제공합니다. 하지만 사이트를 HTTP/2 를 사용하여 TLS 암호화되어 제공하려면, `secure` 명령어를 사용하십시오. 예를 들어 `laravel.test` 도메인을 발렛을 통해서 제공하고자 한다면, 다음 명령어를 통해서 안전하게 구동해야만 합니다.

    valet secure laravel

To "unsecure" a site and revert back to serving its traffic over plain HTTP, use the `unsecure` command. Like the `secure` command, this command accepts the host name that you wish to unsecure:

사이트를 "안전하지 않게" 되돌리고 일반적인 HTTP 로 통신하게 하려면 `unsecure` 명령어를 사용하십시오. `secure` 명령어 같이, 이 명령어는 안전한 통신을 그만두고자 하는 호스트의 이름을 전달 받습니다.

    valet unsecure laravel

<a name="sharing-sites"></a>
## Sharing Sites
## 사이트 공유

Valet even includes a command to share your local sites with the world, providing an easy way to test your site on mobile devices or share it with team members and clients. No additional software installation is required once Valet is installed.

발렛에는 여러분의 로컬 사이트를 세상과 공유하는 명령어도 포함되어 있어서 모바일 기기에서 사이트를 쉽게 테스트하거나 팀원 및 고객과 사이트를 공유 할 수 있습니다. 발렛만 설치되어 있으면 추가적인 소프트웨어의 설치도 필요하지 않습니다.

### Sharing Sites Via Ngrok
### Ngrok를 통해 사이트 공유

To share a site, navigate to the site's directory in your terminal and run the `valet share` command. A publicly accessible URL will be inserted into your clipboard and is ready to paste directly into your browser or share with your team.

사이트를 공유하기 위해서는, 터미널 상에서 사이트의 디렉토리로 이동한 뒤에 `valet share` 명령어를 입력하십시오. 공개된 접근가능한 URL 이 여러분의 클립보드에 복사되고 브라우저에서 직접 붙여넣거나 팀원들과 공유 할 수 있습니다.

To stop sharing your site, hit `Control + C` to cancel the process.

사이트의 공유를 중단하려면 `컨트롤 + C`를 눌러 프로세스를 취소하십시오.

> {tip} You may pass additional parameters to the share command, such as `valet share --region=eu`. For more information, consult the [ngrok documentation](https://ngrok.com/docs).

> {tip} `valet share --region=eu`와 같이 추가 파라메터를 share 명령에 전달할 수 있습니다. 자세한 내용은 [ngrok documentation](https://ngrok.com/docs)을 참조하십시오.

### Sharing Sites Via Expose
### Expose을 통해 사이트 공유

If you have [Expose](https://beyondco.de/docs/expose) installed, you can share your site by navigating to the site's directory in your terminal and running the `expose` command. Consult the expose documentation for additional command-line parameters it supports. After sharing the site, Expose will display the sharable URL that you may use on your other devices or amongst team members.

[Expose](https://beyondco.de/docs/expose)가 설치되어있는 경우 터미널에서 사이트 디렉토리로 이동하고 `expose` 명령을 실행하여 사이트를 공유 할 수 있습니다. 지원하는 추가 명령 줄 매개 변수는 expose 문서를 참조하세요. 사이트를 공유 한 후 Expose는 다른 장치 또는 팀 구성원간에 사용할 수있는 공유 가능한 URL을 표시합니다.

To stop sharing your site, hit `Control + C` to cancel the process.

사이트 공유를 중지하려면 `Control + C`를 눌러 프로세스를 취소하십시오.

### Sharing Sites On Your Local Network
### 로컬 네트워크에서 사이트 공유

Valet restricts incoming traffic to the internal `127.0.0.1` interface by default. This way your development machine isn't exposed to security risks from the Internet.

Valet는 들어오는 트래픽을 기본적으로 내부 `127.0.0.1` 인터페이스로 제한합니다. 이렇게하면 개발 시스템이 인터넷의 보안 위험에 노출되지 않습니다.

If you wish to allow other devices on your local network to access the Valet sites on your machine via your machine's IP address (eg: `192.168.1.10/app-name.test`), you will need to manually edit the appropriate Nginx configuration file for that site to remove the restriction on the `listen` directive by removing the the `127.0.0.1:` prefix on the directive for ports 80 and 443.

로컬 네트워크의 다른 장치가 컴퓨터의 IP 주소를 통해 컴퓨터의 Valet 사이트에 접속하도록하려면, (예 : `192.168.1.10/app-name.test`) 해당 사이트에 대한 적절한 Nginx 설정파일을 수동으로 편집하여 포트 80 및 443에 대한 지시문에서 `127.0.0.1:` 접두어를 제거하여 `listen` 지시문에 대한 제한 사항을 제거해야합니다.

If you have not run `valet secure` on the project, you can open up network access for all non-HTTPS sites by editing the `/usr/local/etc/nginx/valet/valet.conf` file. However, if you're serving the project site over HTTPS (you have run `valet secure` for the site) then you should edit the `~/.config/valet/Nginx/app-name.test` file.

프로젝트에서 `valet secure`를 실행하지 않은 경우 `/usr/local/etc/nginx/valet/valet.conf` 파일을 편집하여 모든 비 HTTPS 사이트에 대한 네트워크 액세스를 열 수 있습니다. 그러나 HTTPS를 통해 프로젝트 사이트를 제공하는 경우 (사이트에 대해 `valet secure`를 실행 한 경우) `~/.config/valet/Nginx/app-name.test` 파일을 편집해야합니다.

Once you have updated your Nginx configuration, run the `valet restart` command to apply the configuration changes.

Nginx 설정을 변경하고 나면 `valet restart` 명령을 실행하여 설정을 변경하십시오.

<a name="site-specific-environment-variables"></a>
## Site Specific Environment Variables
## 사이트 별 환경변수

Some applications using other frameworks may depend on server environment variables but do not provide a way for those variables to be configured within your project. Valet allows you to configure site specific environment variables by adding a `.valet-env.php` file within the root of your project. These variables will be added to the `$_SERVER` global array:

다른 프레임워크를 사용하는 일부 애플리케이션은 서버 환경 변수에 의존 할 수 있지만 이 변수를 프로젝트 내에 추가하는 방법을 제공하지는 않습니다. Valet을 사용하면 프로젝트의 루트 내에 `.valet-env.php` 파일을 추가하여 사이트 별 환경 변수를 구성 할 수 있습니다. 이 변수들은 `$_SERVER` 전역 변수의 배열에 추가 될 것입니다.

    <?php

    // Set $_SERVER['key'] to "value" for the foo.test site...
    return [
        'foo' => [
            'key' => 'value',
        ],
    ];

    // Set $_SERVER['key'] to "value" for all sites...
    return [
        '*' => [
            'key' => 'value',
        ],
    ];

<a name="proxying-services"></a>
## Proxying Services
## 프록시 서비스

Sometimes you may wish to proxy a Valet domain to another service on your local machine. For example, you may occasionally need to run Valet while also running a separate site in Docker; however, Valet and Docker can't both bind to port 80 at the same time.

때로는 Valet 도메인을 로컬 컴퓨터의 다른 서비스로 프록시 할 수 있습니다. 예를 들어 Docker에서 별도의 사이트를 실행하면서 Valet를 실행해야하는 경우가 있습니다. 그러나 Valet와 Docker는 동시에 포트 80에 바인딩 할 수 없습니다.

To solve this, you may use the `proxy` command to generate a proxy. For example, you may proxy all traffic from `http://elasticsearch.test` to `http://127.0.0.1:9200`:

이를 해결하기 위해 `proxy` 명령을 사용하여 프록시를 생성 할 수 있습니다. 예를 들어 `http://elasticsearch.test`에서 `http://127.0.0.1:9200`으로 모든 트래픽을 프록시 할 수 있습니다.

    valet proxy elasticsearch http://127.0.0.1:9200

You may remove a proxy using the `unproxy` command:

`unproxy` 명령을 사용하여 프록시를 제거 할 수 있습니다.

    valet unproxy elasticsearch

You may use the `proxies` command to list all site configuration that are proxied:

프록시 된 모든 사이트 설정을 확인하려면 `proxies` 명령을 사용할 수 있습니다.

    valet proxies

<a name="custom-valet-drivers"></a>
## Custom Valet Drivers
## 사용자 정의-커스텀 발렛 드라이버

You can write your own Valet "driver" to serve PHP applications running on another framework or CMS that is not natively supported by Valet. When you install Valet, a `~/.config/valet/Drivers` directory is created which contains a `SampleValetDriver.php` file. This file contains a sample driver implementation to demonstrate how to write a custom driver. Writing a driver only requires you to implement three methods: `serves`, `isStaticFile`, and `frontControllerPath`.

여러분은 발렛에서 기본적으로 지원하고 있지 않은 다른 프레임워크나 CMS와 같은 PHP 애플리케이션을 제공하기 위해서 여러분의 고유한 발렛 "드라이버"를 작성할 수 있습니다. 발렛을 설치할 때 생성되는 `~/.config/valet/Drivers`에는 `SampleValetDriver.php` 파일이 들어 있습니다. 이 파일은 어떻게 사용자 정의 드라이버를 작성할 것인가에 대한 샘플 드라이버의 구현 코드입니다. 드라이버를 구현하는데 필요한 메소드는 `serves`, `isStaticFile`, 그리고 `frontControllerPath`의 3가지 입니다.

All three methods receive the `$sitePath`, `$siteName`, and `$uri` values as their arguments. The `$sitePath` is the fully qualified path to the site being served on your machine, such as `/Users/Lisa/Sites/my-project`. The `$siteName` is the "host" / "site name" portion of the domain (`my-project`). The `$uri` is the incoming request URI (`/foo/bar`).

세가지 메소드들 모두 `$sitePath`, `$siteName`, 그리고 `$uri` 값을 인자로 받습니다. `$sitePath` 는 `/Users/Lisa/Sites/my-project`와 같은 여러분의 머신에서 사이트가 제공되기 할 전체 경로 입니다. `$siteName` 는 도메인의 "호스트" / "사이트 이름" 부분 입니다(`my-project`). `$uri` 는 유입되는 요청 URI 입니다(`/foo/bar`).

Once you have completed your custom Valet driver, place it in the `~/.config/valet/Drivers` directory using the `FrameworkValetDriver.php` naming convention. For example, if you are writing a custom valet driver for WordPress, your file name should be `WordPressValetDriver.php`.

여러분의 발렛 드라이버를 작성하고 나면, 이 파일을 `FrameworkValetDriver.php` 네이밍 컨벤션에 맞게 `~/.config/valet/Drivers` 디렉토리에 저장하십시오. 예를 들자면, 여러분이 워드프레스를 위한 발렛 드라이버를 작성하였다면 여러분의 파일 이름음 `WordPressValetDriver.php`가 되어야 합니다.

Let's take a look at a sample implementation of each method your custom Valet driver should implement.

사용자 정의 발렛 드라이버에서 구현하는 메소드의 샘플 코드를 살펴 보겠습니다.

#### The `serves` Method
#### `serves` 메소드

The `serves` method should return `true` if your driver should handle the incoming request. Otherwise, the method should return `false`. So, within this method you should attempt to determine if the given `$sitePath` contains a project of the type you are trying to serve.

`serves` 메소드는 드라이버가 유입된 요청을 처리해야 할 경우 `true`를 반환합니다. 그렇지 않은 경우는 `false`를 반환합니다. 따라서 이 메소드 안에서 여러분은 주어진 `$sitePath` 가 동작시키고자 하는 유형의 프로젝트인지 확인도록 해야합니다.

For example, let's pretend we are writing a `WordPressValetDriver`. Our `serves` method might look something like this:

예를 들어, `WordPressValetDriver`를 작성한다고 가정해 봅시다. `serves` 메소드는 다음과 같을 것입니다.

    /**
     * Determine if the driver serves the request.
     *
     * @param  string  $sitePath
     * @param  string  $siteName
     * @param  string  $uri
     * @return bool
     */
    public function serves($sitePath, $siteName, $uri)
    {
        return is_dir($sitePath.'/wp-admin');
    }

#### The `isStaticFile` Method
#### `isStaticFile` 메소드

The `isStaticFile` should determine if the incoming request is for a file that is "static", such as an image or a stylesheet. If the file is static, the method should return the fully qualified path to the static file on disk. If the incoming request is not for a static file, the method should return `false`:

`isStaticFile` 는 유입된 요청이 이미지나 스타일 시트와 같은 "정적" 파일인지 판단합니다. 파일이 정적파일이라면 해당 파일이 존재하는 디스크의 전체 경로를 반환합니다. 유입된 요청이 정적 파일이 아니라면 `false` 를 반환해야 합니다.

    /**
     * Determine if the incoming request is for a static file.
     *
     * @param  string  $sitePath
     * @param  string  $siteName
     * @param  string  $uri
     * @return string|false
     */
    public function isStaticFile($sitePath, $siteName, $uri)
    {
        if (file_exists($staticFilePath = $sitePath.'/public/'.$uri)) {
            return $staticFilePath;
        }

        return false;
    }

> {note} The `isStaticFile` method will only be called if the `serves` method returns `true` for the incoming request and the request URI is not `/`.

> {note} `isStaticFile` 메소드는 유입된 요청이 `/` 가 아니고 `serves` 메소드가 `true`를 반환하는 경우에만 호출될 것입니다.

#### The `frontControllerPath` Method
#### `frontControllerPath` 메소드

The `frontControllerPath` method should return the fully qualified path to your application's "front controller", which is typically your "index.php" file or equivalent:

`frontControllerPath` 메소드는 일반적으로 "index.php" 파일 또는 유사한 파일이 되는 애플리케이션의 "프론트 컨트롤러"에 대한 전체 경로를 반환합니다.

    /**
     * Get the fully resolved path to the application's front controller.
     *
     * @param  string  $sitePath
     * @param  string  $siteName
     * @param  string  $uri
     * @return string
     */
    public function frontControllerPath($sitePath, $siteName, $uri)
    {
        return $sitePath.'/public/index.php';
    }

<a name="local-drivers"></a>
### Local Drivers
### 로컬 드라이버

If you would like to define a custom Valet driver for a single application, create a `LocalValetDriver.php` in the application's root directory. Your custom driver may extend the base `ValetDriver` class or extend an existing application specific driver such as the `LaravelValetDriver`:

하나의 애플리케이션을 위한 커스텀 Valet 드라이버를 정의하고자 한다면, 애플리케이션의 루트 디렉토리에 `LocalValetDriver.php` 파일을 생성하십시오. 커스텀 드라이버는 베이스 `ValetDriver` 클래스를 상속받거나 `LaravelValetDriver`과 같은 기존 애플리케이션에서 지정한 드라이버를 상속해야합니다.

    class LocalValetDriver extends LaravelValetDriver
    {
        /**
         * Determine if the driver serves the request.
         *
         * @param  string  $sitePath
         * @param  string  $siteName
         * @param  string  $uri
         * @return bool
         */
        public function serves($sitePath, $siteName, $uri)
        {
            return true;
        }

        /**
         * Get the fully resolved path to the application's front controller.
         *
         * @param  string  $sitePath
         * @param  string  $siteName
         * @param  string  $uri
         * @return string
         */
        public function frontControllerPath($sitePath, $siteName, $uri)
        {
            return $sitePath.'/public_html/index.php';
        }
    }

<a name="other-valet-commands"></a>
## Other Valet Commands
## 기타 발렛 명령어들

Command  | Description
------------- | -------------
`valet forget` | Run this command from a "parked" directory to remove it from the parked directory list.
`valet log` | View a list of logs which are written by Valet's services.
`valet paths` | View all of your "parked" paths.
`valet restart` | Restart the Valet daemon.
`valet start` | Start the Valet daemon.
`valet stop` | Stop the Valet daemon.
`valet trust` | Add sudoers files for Brew and Valet to allow Valet commands to be run without prompting for passwords.
`valet uninstall` | Uninstall Valet: Shows instructions for manual uninstall; or pass the `--force` parameter to aggressively delete all of Valet.

명령어 | 설명
------------- | -------------
`valet forget` | parked 디렉토리 목록에서 디렉토리를 제거하기 위해서는 "parked" 된 디렉토리에서 이 명령어를 실행하십시오.
`valet log` | Valet의 서비스가 작성한 로그 목록보기.
`valet paths` | "parked" 된 모든 경로를 확인합니다.
`valet restart` | 발렛 데몬을 재시작 합니다.
`valet start` | 발렛 데몬을 시작합니다.
`valet stop` | 발렛 데몬을 중지합니다.
`valet trust` | 암호를 묻지 않고 Valet 명령을 실행할 수 있도록 Brew와 Valet을 sudoers에 추가합니다.
`valet uninstall` | Valet 제거 : 수동 제거에 대한 명렁을 보여줍니다. 또는 `--force` 파라메터를 전달하여 모든 Valet을 적극적으로 삭제합니다.

<a name="valet-directories-and-files"></a>
## Valet Directories & Files
## Valet 디렉토리 및 파일

You may find the following directory and file information helpful while troubleshooting issues with your Valet environment:

Valet 환경설정 관련 문제를 해결하기 위해 다음 디렉토리 및 파일 정보가 도움이 될 수 있습니다.

File / Path | Description
--------- | -----------
`~/.config/valet/` | Contains all of Valet's configuration. You may wish to maintain a backup of this folder.
`~/.config/valet/dnsmasq.d/` | Contains DNSMasq's configuration.
`~/.config/valet/Drivers/` | Contains custom Valet drivers.
`~/.config/valet/Extensions/` | Contains custom Valet extensions / commands.
`~/.config/valet/Nginx/` | Contains all Valet generated Nginx site configurations. These files are rebuilt when running the `install`, `secure`, and `tld` commands.
`~/.config/valet/Sites/` | Contains all symbolic links for linked projects.
`~/.config/valet/config.json` | Valet's master configuration file
`~/.config/valet/valet.sock` | The PHP-FPM socket used by Valet's Nginx configuration. This will only exist if PHP is running properly.
`~/.config/valet/Log/fpm-php.www.log` | User log for PHP errors.
`~/.config/valet/Log/nginx-error.log` | User log for Nginx errors.
`/usr/local/var/log/php-fpm.log` | System log for PHP-FPM errors.
`/usr/local/var/log/nginx` | Contains Nginx access and error logs.
`/usr/local/etc/php/X.X/conf.d` | Contains `*.ini` files for various PHP configuration settings.
`/usr/local/etc/php/X.X/php-fpm.d/valet-fpm.conf` | PHP-FPM pool configuration file.
`~/.composer/vendor/laravel/valet/cli/stubs/secure.valet.conf` | The default Nginx configuration used for building site certificates.

파일 / 경로 | 설명
--------- | -----------
`~/.config/valet/` | Valet의 모든 설정이 들어 있습니다. 이 폴더의 백업하여 유지 관리 할 수 ​​있습니다.
`~/.config/valet/dnsmasq.d/` | DNSMasq의 설정이 들어 있습니다.
`~/.config/valet/Drivers/` | 커스텀 Valet 드라이버가 들어 있습니다.
`~/.config/valet/Extensions/` | 커스텀 Valet 확장모듈 / 커맨드가 들어 있습니다.
`~/.config/valet/Nginx/` | Valet이 생성한 모든 Nginx 사이트 설정이 들어 있습니다. 이 파일들은 `install`, `secure` 및 `tld` 명령을 실행할 때 다시 빌드됩니다.
`~/.config/valet/Sites/` | 연결된 프로젝트에 대한 모든 심볼릭 링크가 들어 있습니다.
`~/.config/valet/config.json` | Valet의 마스터 설정 파일
`~/.config/valet/valet.sock` | Valet의 Nginx 설정에서 사용되는 PHP-FPM 소켓. PHP가 실행되고 있는 경우에만 존재합니다.
`~/.config/valet/Log/fpm-php.www.log` | PHP 오류에 대한 사용자 로그.
`~/.config/valet/Log/nginx-error.log` | Nginx 오류에 대한 사용자 로그.
`/usr/local/var/log/php-fpm.log` | PHP-FPM 오류에 대한 시스템 로그.
`/usr/local/var/log/nginx` | Nginx 액세스 및 오류 로그.
`/usr/local/etc/php/X.X/conf.d` | 다양한 PHP 환경 설정을 위한 `* .ini` 파일이 들어 있습니다.
`/usr/local/etc/php/X.X/php-fpm.d/valet-fpm.conf` | PHP-FPM 풀 설정 파일.
`~/.composer/vendor/laravel/valet/cli/stubs/secure.valet.conf` | 사이트 인증서를 작성하는 데 사용되는 기본 Nginx 설정.