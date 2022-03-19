# Laravel Valet
# 라라벨 발렛

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Installation](#installation)
- [설치하기](#installation)
  - [Upgrading Valet](#upgrading-valet)
  - [발렛 업그레이드](#upgrading-valet)
- [Serving Sites](#serving-sites)
- [사이트 동작시키기](#serving-sites)
  - [The "Park" Command](#the-park-command)
  - ["Park" 명령어](#the-park-command)
  - [The "Link" Command](#the-link-command)
  - ["Link" 명령어](#the-link-command)
  - [Securing Sites With TLS](#securing-sites)
  - [TLS를 사용한 안전한 사이트](#securing-sites)
  - [Serving a Default Site](#serving-a-default-site)
  - [기본 사이트 제공](#serving-a-default-site)
- [Sharing Sites](#sharing-sites)
- [사이트 공유하기](#sharing-sites)
  - [Sharing Sites Via Ngrok](#sharing-sites-via-ngrok)
  - [Ngrok을 통한 사이트 공유](#sharing-sites-via-ngrok)
  - [Sharing Sites Via Expose](#sharing-sites-via-expose)
  - [Expose를 통한 사이트 공유](#sharing-sites-via-expose)
  - [Sharing Sites On Your Local Network](#sharing-sites-on-your-local-network)
  - [로컬 네트워크에서 사이트 공유](#sharing-sites-on-your-local-network)
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

[Laravel Valet](https://github.com/laravel/valet) is a development environment for macOS minimalists. Laravel Valet configures your Mac to always run [Nginx](https://www.nginx.com/) in the background when your machine starts. Then, using [DnsMasq](https://en.wikipedia.org/wiki/Dnsmasq), Valet proxies all requests on the `*.test` domain to point to sites installed on your local machine.

[라라벨 발렛](https://github.com/laravel/valet) 은 macOS 미니멀리스트를 위한 개발 환경입니다. 라라벨 발렛은 시스템이 시작될 때 항상 백그라운드에서 [Nginx](https://www.nginx.com/) 를 실행하도록 Mac을 구성합니다. 그런 다음 발렛은 [DnsMasq](https://en.wikipedia.org/wiki/Dnsmasq) 를 사용하여 `.test` 도메인의 모든 요청을 프록시하여 로컬 컴퓨터에 설치된 사이트를 바라보게 만듭니다.

In other words, Valet is a blazing fast Laravel development environment that uses roughly 7 MB of RAM. Valet isn't a complete replacement for [Sail](/docs/{{version}}/sail) or [Homestead](/docs/{{version}}/homestead), but provides a great alternative if you want flexible basics, prefer extreme speed, or are working on a machine with a limited amount of RAM.

즉, 발렛은 약 7MB의 RAM을 사용하는 매우 빠른 라라벨 개발 환경입니다. 발렛은 [Sail](/docs/{{version}}/sail) 또는 [Homestead](/docs/{{version}}/homestead)를 완전히 대체하지는 않지만, 유연한 기본기를 원하거나, 극한의 속도를 선호하거나, RAM이 제한된 컴퓨터에서 사용합니다.

Out of the box, Valet support includes, but is not limited to:

별다른 설정 없이도, 발렛은 다음을 지원하지만, 이게 전부는 아닙니다.

- [Laravel](https://laravel.com)
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

However, you may extend Valet with your own [custom drivers](#custom-valet-drivers).

여러분은 발렛을 고유한 [사용자 정의 드라이버](#custom-valet-drivers)를 통해서 확장할 수 있습니다.

<a name="installation"></a>
## Installation
## 설치하기

> {note} Valet requires macOS and [Homebrew](https://brew.sh/). Before installation, you should make sure that no other programs such as Apache or Nginx are binding to your local machine's port 80.

> {note} 발렛은 macOS 및 [Homebrew](https://brew.s/h) 가 필요합니다. 설치하기 전에 Apache 또는 Nginx와 같은 다른 프로그램이 로컬 시스템의 포트 80에 바인딩되어 있지 않은지 확인해야 합니다.

To get started, you first need to ensure that Homebrew is up to date using the `update` command:

시작하려면 먼저 `update` 명령을 사용하여 Homebrew가 최신 상태인지 확인해야 합니다.

```shell
brew update
```

Next, you should use Homebrew to install PHP:

다음으로 Homebrew를 사용하여 PHP를 설치해야 합니다.

```shell
brew install php
```

After installing PHP, you are ready to install the [Composer package manager](https://getcomposer.org). In addition, you should make sure the `~/.composer/vendor/bin` directory is in your system's "PATH". After Composer has been installed, you may install Laravel Valet as a global Composer package:

PHP를 설치하면 [Composer 패키지 관리자](https://getcomposer.org)를 설치할 준비가 된 것입니다. 또한 `~.composer/vendor/bin` 디렉토리가 시스템의 "PATH"에 있는지 확인해야 합니다. Composer가 설치된 후 Laravel 발렛을 글로벌 Composer 패키지로 설치할 수 있습니다.

```shell
composer global require laravel/valet
```

Finally, you may execute Valet's `install` command. This will configure and install Valet and DnsMasq. In addition, the daemons Valet depends on will be configured to launch when your system starts:

마지막으로 발렛의 `install` 명령을 실행할 수 있습니다. 발렛 및 DnsMasq를 설정하고 설치합니다. 또한 발렛이 의존하는 데몬은 시스템이 시작될 때 실행되도록 설정됩니다.

```shell
valet install
```

Once Valet is installed, try pinging any `*.test` domain on your terminal using a command such as `ping foobar.test`. If Valet is installed correctly you should see this domain responding on `127.0.0.1`.

발렛이 설치되면 `ping foobar.test`와 같은 명령을 사용하여, 터미널에서 `.test` 도메인에 대해 ping을 시도하십시오. 발렛이 올바르게 설치된 경우, 이 도메인이 `127.0.0.1`로 응답하는 것을 볼 수 있습니다.

Valet will automatically start its required services each time your machine boots.

발렛은 장비가 부팅될 때마다 필요한 서비스를 자동으로 시작합니다.

<a name="php-versions"></a>
#### PHP Versions
#### PHP 버전

Valet allows you to switch PHP versions using the `valet use php@version` command. Valet will install the specified PHP version via Homebrew if it is not already installed:

발렛을 사용하면 `valet use php@version` 명령을 사용하여 PHP 버전을 전환할 수 있습니다. 발렛은 Homebrew가 아직 설치되지 않은 경우, 지정한 PHP 버전을 설치합니다.

```shell
valet use php@7.2

valet use php
```

You may also create a `.valetphprc` file in the root of your project. The `.valetphprc` file should contain the PHP version the site should use:

프로젝트 루트에 `.valetphprc` 파일을 생성할 수도 있습니다. `.valetphprc` 파일에는 사이트에서 사용해야 하는 PHP 버전이 포함되어야 합니다.

```shell
php@7.2
```

Once this file has been created, you may simply execute the `valet use` command and the command will determine the site's preferred PHP version by reading the file.

이 파일이 생성되면 `valet use` 명령을 실행하기만 하면 이 명령이 파일을 읽어 사이트의 기본 PHP 버전을 결정합니다.

> {note} Valet only serves one PHP version at a time, even if you have multiple PHP versions installed.

> {note} 발렛은 여러 PHP 버전이 설치되어 있어도 한 번에 하나의 PHP 버전만 제공합니다.

<a name="database"></a>
#### Database
#### 데이터베이스

If your application needs a database, check out [DBngin](https://dbngin.com). DBngin provides a free, all-in-one database management tool that includes MySQL, PostgreSQL, and Redis. After DBngin has been installed, you can connect to your database at `127.0.0.1` using the `root` username and an empty string for the password.

애플리케이션에 데이터베이스가 필요한 경우 [DBngin](https://dbngin.com)을 확인하세요. DBngin은 MySQL, PostgreSQL 및 Redis를 포함하는 무료 올인원 데이터베이스 관리 도구를 제공합니다. DBngin을 설치한 후 `root` 사용자 이름과 비밀번호에 빈 문자열을 사용하여 `127.0.0.1`의 데이터베이스에 연결할 수 있습니다.

<a name="resetting-your-installation"></a>
#### Resetting Your Installation
#### 설치 재설정

If you are having trouble getting your Valet installation to run properly, executing the `composer global update` command followed by `valet install` will reset your installation and can solve a variety of problems. In rare cases, it may be necessary to "hard reset" Valet by executing `valet uninstall --force` followed by `valet install`.

발렛 설치를 제대로 실행하는 데 문제가 있는 경우 `composer global update` 명령 다음에 `valet install`을 실행하면 설치가 재설정되고 다양한 문제를 해결할 수 있습니다. 드문 경우지만 `valet uninstall --force` 다음에 `valet install`을 실행하여 발렛을 "강제 초기화"해야 할 수도 있습니다.

<a name="upgrading-valet"></a>
### Upgrading Valet
### 발렛 업그레이드

You may update your Valet installation by executing the `composer global update` command in your terminal. After upgrading, it is good practice to run the `valet install` command so Valet can make additional upgrades to your configuration files if necessary.

터미널에서 `composer global update` 명령을 실행하여 발렛 설치를 업데이트할 수 있습니다. 업그레이드 후에는 발렛이 필요할 경우, 설정 파일을 추가로 업그레이드할 수 있도록 `valet install` 명령을 실행하는 것이 좋습니다.

<a name="serving-sites"></a>
## Serving Sites
## 사이트 동작시키기

Once Valet is installed, you're ready to start serving your Laravel applications. Valet provides two commands to help you serve your applications: `park` and `link`.

발렛이 설치되면 라라벨 애플리케이션 서비스를 시작할 준비가 된 것입니다. 발렛은 애플리케이션을 제공하는 데 도움이 되는 두 가지 명령인 `park`와 `link`를 제공합니다.

<a name="the-park-command"></a>
### The `park` Command
### `park` 명령

The `park` command registers a directory on your machine that contains your applications. Once the directory has been "parked" with Valet, all of the directories within that directory will be accessible in your web browser at `http://<directory-name>.test`:

`park` 명령은 애플리케이션이 포함된 디렉토리를 머신에 등록합니다. 디렉토리가 발렛으로 "파킹"되면 해당 디렉토리 내의 모든 디렉토리는 웹 브라우저의 `http://<directory-name>.test`에서 액세스할 수 있습니다.

```shell
cd ~/Sites

valet park
```

That's all there is to it. Now, any application you create within your "parked" directory will automatically be served using the `http://<directory-name>.test` convention. So, if your parked directory contains a directory named "laravel", the application within that directory will be accessible at `http://laravel.test`. In addition, Valet automatically allows you to access the site using wildcard subdomains (`http://foo.laravel.test`).

그게 전부입니다. 이제 "parked" 디렉토리 내에 생성한 모든 애플리케이션은 `http://<directory-name>.test` 규칙을 사용하여 자동으로 제공됩니다. 따라서 파킹된 디렉토리에 "laravel"이라는 디렉토리가 포함되어 있으면 해당 디렉토리 내의 애플리케이션은 `http://laravel.test`에서 액세스할 수 있습니다. 또한 발렛은 와일드카드 하위 도메인(`http:foo.laravel.test`)을 사용하여 사이트에 자동으로 액세스할 수 있도록 합니다.

<a name="the-link-command"></a>
### The `link` Command
### `link` 명령

The `link` command can also be used to serve your Laravel applications. This command is useful if you want to serve a single site in a directory and not the entire directory:

`link` 명령은 라라벨 애플리케이션을 제공하는 데에도 사용할 수 있습니다. 이 명령은 전체 디렉토리가 아닌 디렉토리의 단일 사이트를 제공하려는 경우에 유용합니다.

```shell
cd ~/Sites/laravel

valet link
```

Once an application has been linked to Valet using the `link` command, you may access the application using its directory name. So, the site that was linked in the example above may be accessed at `http://laravel.test`. In addition, Valet automatically allows you to access the site using wildcard sub-domains (`http://foo.laravel.test`).

애플리케이션이 `link` 명령을 사용하여 발렛에 연결되면, 해당 디렉터리 이름을 사용하여 애플리케이션에 액세스할 수 있습니다. 따라서 위의 예시에서 링크된 사이트는 `http:laravel.test`로 접속할 수 있습니다. 또한 발렛은 와일드카드 하위 도메인(`http:foo.laravel.test`)을 사용하여 사이트에 자동으로 액세스할 수 있도록 합니다.

If you would like to serve the application at a different hostname, you may pass the hostname to the `link` command. For example, you may run the following command to make an application available at `http://application.test`:

다른 호스트 이름에서 애플리케이션을 제공하려면, `link` 명령에 호스트 이름을 전달할 수 있습니다. 예를 들어, 다음 명령을 실행하여 `http://application.test`에서 애플리케이션을 접속할 수 있도록 할 수 있습니다.

```shell
cd ~/Sites/laravel

valet link application
```

You may execute the `links` command to display a list of all of your linked directories:

`links` 명령을 실행하여 연결된 모든 디렉토리 목록을 표시할 수 있습니다.

```shell
valet links
```

The `unlink` command may be used to destroy the symbolic link for a site:

`unlink` 명령은 사이트의 심볼릭 링크를 제거하는 데 사용할 수 있습니다.

```shell
cd ~/Sites/laravel

valet unlink
```

<a name="securing-sites"></a>
### Securing Sites With TLS
### TLS를 사용한 안전한 사이트

By default, Valet serves sites over HTTP. However, if you would like to serve a site over encrypted TLS using HTTP/2, you may use the `secure` command. For example, if your site is being served by Valet on the `laravel.test` domain, you should run the following command to secure it:

기본적으로 발렛은 HTTP를 통해 사이트를 제공합니다. 그러나 HTTP2를 사용하여 암호화된 TLS를 통해 사이트를 제공하려는 경우 `secure` 명령을 사용할 수 있습니다. 예를 들어, `laravel.test` 도메인에서 발렛이 사이트를 제공하는 경우 다음 명령을 실행하여 사이트를 보호해야 합니다.

```shell
valet secure laravel
```

To "unsecure" a site and revert back to serving its traffic over plain HTTP, use the `unsecure` command. Like the `secure` command, this command accepts the hostname that you wish to unsecure:

사이트를 "안전하지 않게" 되돌리고 일반적인 HTTP 로 통신하게 하려면 `unsecure` 명령어를 사용하십시오. `secure` 명령어 같이, 이 명령어는 안전한 통신을 그만두고자 하는 호스트의 이름을 전달 받습니다.

```shell
valet unsecure laravel
```

<a name="serving-a-default-site"></a>
### Serving A Default Site
### 기본 사이트 제공

Sometimes, you may wish to configure Valet to serve a "default" site instead of a `404` when visiting an unknown `test` domain. To accomplish this, you may add a `default` option to your `~/.config/valet/config.json` configuration file containing the path to the site that should serve as your default site:

알 수 없는 `test` 도메인을 방문할 때 `404` 대신 `default` 사이트로 접속되도록 발렛을 설정하고 싶을수도 있습니다. 이때는 기본 사이트 역할을 해야 하는 사이트 주소가 포함된 `~/.config/valet/config.json` 설정 파일에 `default` 옵션을 추가할 수 있습니다.

    "default": "/Users/Sally/Sites/foo",

<a name="sharing-sites"></a>
## Sharing Sites
## 사이트 공유

Valet even includes a command to share your local sites with the world, providing an easy way to test your site on mobile devices or share it with team members and clients.

발렛에는 로컬 사이트를 외부와 공유하는 명령도 포함되어 있어, 모바일 장치에서 사이트를 쉽게 테스트하거나 팀 구성원 및 클라이언트와 공유할 수 있습니다.

<a name="sharing-sites-via-ngrok"></a>
### Sharing Sites Via Ngrok
### Ngrok을 통한 사이트 공유

To share a site, navigate to the site's directory in your terminal and run Valet's `share` command. A publicly accessible URL will be inserted into your clipboard and is ready to paste directly into your browser or share with your team:

사이트를 공유하려면 터미널에서 사이트 디렉토리로 이동하여 발렛의 `share` 명령을 실행하세요. 공개적으로 액세스할 수 있는 URL이 클립보드에 삽입되고, 브라우저에 직접 붙여넣거나 팀과 공유할 수 있습니다.

```shell
cd ~/Sites/laravel

valet share
```

To stop sharing your site, you may press `Control + C`.

사이트 공유를 중지하려면 `Control + C`를 누르면 됩니다.

> {tip} You may pass additional Ngrok parameters to the share command, such as `valet share --region=eu`. For more information, consult the [ngrok documentation](https://ngrok.com/docs).

> {tip} `valet share --region=eu`와 같은 추가 Ngrok 매개변수를 share 명령에 전달할 수 있습니다. 자세한 내용은 [ngrok 문서](https://ngrok.com/docs) 를 참조하세요.

<a name="sharing-sites-via-expose"></a>
### Sharing Sites Via Expose
### Expose를 통한 사이트 공유

If you have [Expose](https://expose.dev) installed, you can share your site by navigating to the site's directory in your terminal and running the `expose` command. Consult the [Expose documentation](https://expose.dev/docs) for information regarding the additional command-line parameters it supports. After sharing the site, Expose will display the sharable URL that you may use on your other devices or amongst team members:

[Expose](https://expose.dev)가 설치되어 있으면, 터미널에서 사이트 디렉토리로 이동하고 `expose` 명령을 실행하여 사이트를 공유할 수 있습니다. 지원하는 추가 명령줄 매개변수에 대한 정보는 [Expose 문서](https://expose.dev/docs) 를 참조하십시오. 사이트를 공유한 후 Expose는 다른 장치에서 또는 팀 구성원 간에 사용할 수 있는 공유 가능한 URL을 표시합니다.

    cd ~/Sites/laravel

    expose

To stop sharing your site, you may press `Control + C`.

사이트 공유를 중지하려면 `Control + C`를 누르면 됩니다.

<a name="sharing-sites-on-your-local-network"></a>
### Sharing Sites On Your Local Network
### 로컬 네트워크에서 사이트 공유

Valet restricts incoming traffic to the internal `127.0.0.1` interface by default so that your development machine isn't exposed to security risks from the Internet.

발렛은 기본적으로 내부 `127.0.0.1` 인터페이스로 들어오는 트래픽으로 제한하므로, 개발 시스템이 인터넷의 보안 위험에 노출되지 않습니다.

If you wish to allow other devices on your local network to access the Valet sites on your machine via your machine's IP address (eg: `192.168.1.10/application.test`), you will need to manually edit the appropriate Nginx configuration file for that site to remove the restriction on the `listen` directive. You should remove the `127.0.0.1:` prefix on the `listen` directive for ports 80 and 443.

로컬 네트워크의 다른 장치가 컴퓨터의 IP 주소(예: `192.168.1.10/application.test`)를 통해, 컴퓨터의 발렛 사이트에 접근하도록 허용하려면, 해당 Nginx 설정 파일을 수동으로 편집해야 합니다. `listen` 지시문에 대한 제한을 제거하려면 사이트를 방문하세요. 포트 80 및 443에 대한 `listen` 지시문에서 `127.0.0.1:` 접두사를 제거해야 합니다.

If you have not run `valet secure` on the project, you can open up network access for all non-HTTPS sites by editing the `/usr/local/etc/nginx/valet/valet.conf` file. However, if you're serving the project site over HTTPS (you have run `valet secure` for the site) then you should edit the `~/.config/valet/Nginx/app-name.test` file.

프로젝트에서 `valet secure`를 실행하지 않은 경우 `/usr/local/etc/nginx/valet/valet.conf` 파일을 편집하여 모든 비 HTTPS 사이트에 대한 네트워크 액세스를 열어 줄 수 있습니다. 그러나 HTTPS를 통해 프로젝트 사이트를 제공하는 경우 (사이트에 대해 `valet secure`를 실행 한 경우) `~/.config/valet/Nginx/app-name.test` 파일을 편집해야합니다.

Once you have updated your Nginx configuration, run the `valet restart` command to apply the configuration changes.

Nginx 설정을 변경하고 나면 `valet restart` 명령을 실행하여, 변경된 설정을 적용하십시오.

<a name="site-specific-environment-variables"></a>
## Site Specific Environment Variables
## 사이트 별 환경변수

Some applications using other frameworks may depend on server environment variables but do not provide a way for those variables to be configured within your project. Valet allows you to configure site specific environment variables by adding a `.valet-env.php` file within the root of your project. This file should return an array of site / environment variable pairs which will be added to the global `$_SERVER` array for each site specified in the array:

다른 프레임워크를 사용하는 일부 애플리케이션은 서버 환경 변수에 의존할 수 있지만, 프로젝트 내에서 이러한 변수를 설정하는 방법을 제공하지 않습니다. 발렛을 사용하면 프로젝트 루트 내에 `.valet-env.php` 파일을 추가하여 사이트별 환경 변수를 변경할 수 있습니다. 이 파일은 배열에 지정된 각 사이트의 전역 `_SERVER` 배열에 추가될 사이트 환경 변수 쌍의 배열을 반환해야 합니다.

    <?php

    return [
        // Set $_SERVER['key'] to "value" for the laravel.test site...
        'laravel' => [
            'key' => 'value',
        ],

        // Set $_SERVER['key'] to "value" for all sites...
        '*' => [
            'key' => 'value',
        ],
    ];

<a name="proxying-services"></a>
## Proxying Services
## 프록시 서비스

Sometimes you may wish to proxy a Valet domain to another service on your local machine. For example, you may occasionally need to run Valet while also running a separate site in Docker; however, Valet and Docker can't both bind to port 80 at the same time.

때로는 발렛 도메인을 로컬 컴퓨터의 다른 서비스로 프록시 할 수 있습니다. 예를 들어 Docker에서 별도의 사이트를 실행하면서 발렛를 실행해야하는 경우가 있습니다. 그러나 발렛과 Docker는 동시에 포트 80에 바인딩 할 수 없습니다.

To solve this, you may use the `proxy` command to generate a proxy. For example, you may proxy all traffic from `http://elasticsearch.test` to `http://127.0.0.1:9200`:

이를 해결하기 위해 `proxy` 명령을 사용하여 프록시를 생성 할 수 있습니다. 예를 들어 `http://elasticsearch.test`로 들어오는 모든 트래픽을 `http://127.0.0.1:9200`로 보내도록 프록시 처리 할 수 있습니다.

```shell
# Proxy over HTTP...
valet proxy elasticsearch http://127.0.0.1:9200

# Proxy over TLS + HTTP/2...
valet proxy elasticsearch http://127.0.0.1:9200 --secure
```

You may remove a proxy using the `unproxy` command:

`unproxy` 명령을 사용하여 프록시를 제거 할 수 있습니다.

```shell
valet unproxy elasticsearch
```

You may use the `proxies` command to list all site configurations that are proxied:

`proxys` 명령을 사용하여 프록시된 모든 사이트 설정을 나열할 수 있습니다.

```shell
valet proxies
```

<a name="custom-valet-drivers"></a>
## Custom Valet Drivers
## 사용자 정의-커스텀 발렛 드라이버

You can write your own Valet "driver" to serve PHP applications running on a framework or CMS that is not natively supported by Valet. When you install Valet, a `~/.config/valet/Drivers` directory is created which contains a `SampleValetDriver.php` file. This file contains a sample driver implementation to demonstrate how to write a custom driver. Writing a driver only requires you to implement three methods: `serves`, `isStaticFile`, and `frontControllerPath`.

발렛에서 기본적으로 지원하지 않는 프레임워크 또는 CMS에서 실행되는 PHP 애플리케이션을 제공하기 위해 자체 발렛 "드라이버"를 작성할 수 있습니다. 발렛을 설치하면 `SampleValetDriver.php` 파일이 포함된 `~/.config/valet/Drivers` 디렉토리가 생성됩니다. 이 파일에는 사용자 지정 드라이버를 작성하는 방법을 보여 주는 샘플 드라이버 구현이 포함되어 있습니다. 드라이버를 작성하려면 `serves`, `isStaticFile`, `frontControllerPath`의 세 가지 방법만 구현하면 됩니다.

All three methods receive the `$sitePath`, `$siteName`, and `$uri` values as their arguments. The `$sitePath` is the fully qualified path to the site being served on your machine, such as `/Users/Lisa/Sites/my-project`. The `$siteName` is the "host" / "site name" portion of the domain (`my-project`). The `$uri` is the incoming request URI (`/foo/bar`).

세가지 메소드들 모두 `$sitePath`, `$siteName`, 그리고 `$uri` 값을 인자로 받습니다. `$sitePath` 는 `/Users/Lisa/Sites/my-project`와 같은 여러분의 머신에서 사이트가 제공되기 할 전체 경로 입니다. `$siteName` 는 도메인의 "호스트" / "사이트 이름" 부분 입니다(`my-project`). `$uri` 는 유입되는 요청 URI 입니다(`/foo/bar`).

Once you have completed your custom Valet driver, place it in the `~/.config/valet/Drivers` directory using the `FrameworkValetDriver.php` naming convention. For example, if you are writing a custom valet driver for WordPress, your filename should be `WordPressValetDriver.php`.

여러분의 발렛 드라이버를 작성하고 나면, 이 파일을 `FrameworkValetDriver.php` 네이밍 컨벤션에 맞게 `~/.config/valet/Drivers` 디렉토리에 저장하십시오. 예를 들자면, 여러분이 워드프레스를 위한 발렛 드라이버를 작성하였다면 여러분의 파일 이름음 `WordPressValetDriver.php`가 되어야 합니다.

Let's take a look at a sample implementation of each method your custom Valet driver should implement.

사용자 정의 발렛 드라이버에서 구현하는 메소드의 샘플 코드를 살펴 보겠습니다.

<a name="the-serves-method"></a>
#### The `serves` Method
#### `serves` 메소드

The `serves` method should return `true` if your driver should handle the incoming request. Otherwise, the method should return `false`. So, within this method, you should attempt to determine if the given `$sitePath` contains a project of the type you are trying to serve.

드라이버가 들어오는 요청을 처리해야 하는 경우 `serves` 메서드는 `true`를 반환해야 합니다. 그렇지 않으면 메서드는 `false`을 반환해야 합니다. 따라서 이 메소드 내에서 제공된 `sitePath`에 제공하려는 유형의 프로젝트가 포함되어 있는지 확인해야 합니다.

For example, let's imagine we are writing a `WordPressValetDriver`. Our `serves` method might look something like this:

예를 들어 `WordPressValetDriver`를 작성한다고 가정해 보겠습니다. 우리의 `serves` 메소드는 다음과 같을 것입니다.

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

<a name="the-isstaticfile-method"></a>
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

<a name="the-frontcontrollerpath-method"></a>
#### The `frontControllerPath` Method
#### `frontControllerPath` 메소드

The `frontControllerPath` method should return the fully qualified path to your application's "front controller", which is typically an "index.php" file or equivalent:

`frontControllerPath` 메소드는 일반적으로 "index.php" 파일 또는 이와 동등한 파일인 애플리케이션의 "전면 컨트롤러"에 대한 완전한 경로를 반환해야 합니다.

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

If you would like to define a custom Valet driver for a single application, create a `LocalValetDriver.php` file in the application's root directory. Your custom driver may extend the base `ValetDriver` class or extend an existing application specific driver such as the `LaravelValetDriver`:

단일 애플리케이션에 대한 맞춤형 Valet 드라이버를 정의하려면 애플리케이션의 루트 디렉토리에 `LocalValetDriver.php` 파일을 생성하십시오. 사용자 정의 드라이버는 기본 `ValetDriver` 클래스를 확장하거나 `LaravelValetDriver`와 같은 기존 애플리케이션 특정 드라이버를 확장할 수 있습니다.

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
`valet restart` | Restart the Valet daemons.
`valet start` | Start the Valet daemons.
`valet stop` | Stop the Valet daemons.
`valet trust` | Add sudoers files for Brew and Valet to allow Valet commands to be run without prompting for your password.
`valet uninstall` | Uninstall Valet: shows instructions for manual uninstall. Pass the `--force` option to aggressively delete all of Valet's resources.


명령 | 설명 
---------- | ---------- 
`valet forget` | 파킹된 디렉토리 목록에서 제거하려면 "파킹된-parked" 디렉토리에서 이 명령을 실행하십시오.
`valet log` | 발렛 서비스가 작성한 로그 목록을 봅니다. 
`valet paths` | 모든 "파킹된-parked" 경로를 봅니다. 
`valet restart` | 발렛 데몬을 다시 시작합니다. 
`valet start` | 발렛 데몬을 시작합니다.
`valet stop` | 발렛 데몬을 중지합니다. 
`valet trust` | Brew 및 Valet용 sudoers 파일을 추가하여 암호를 묻지 않고 발렛 명령을 실행할 수 있습니다. 
`valet uninstall` | 발렛을 제거합니다. 수동 제거를 위한 메뉴얼을 보여줍니다. 발렛의 모든 리소스를 강제로 삭제하려면 `--force` 옵션을 추가하세요.

<a name="valet-directories-and-files"></a>
## Valet Directories & Files
## Valet 디렉토리 및 파일

You may find the following directory and file information helpful while troubleshooting issues with your Valet environment:

발렛 환경설정 관련 문제를 해결하기 위해 다음 디렉토리 및 파일 정보가 도움이 될 수 있습니다.

#### `~/.config/valet`
#### `~/.config/valet`

Contains all of Valet's configuration. You may wish to maintain a backup of this directory.

발렛의 모든 설정이 들어있습니다. 이 디렉토리의 백업해 유지 관리할 수 있습니다.

#### `~/.config/valet/dnsmasq.d/`
#### `~/.config/valet/dnsmasq.d/`

This directory contains DNSMasq's configuration.

이 디렉토리에는 DNSMasq의 설정이 포함되어 있습니다.

#### `~/.config/valet/Drivers/`
#### `~/.config/valet/Drivers/`

This directory contains Valet's drivers. Drivers determine how a particular framework / CMS is served.

이 디렉토리에는 발렛의 드라이버가 포함되어 있습니다. 드라이버는 특정 프레임워크 CMS가 제공되는 방식을 결정합니다.

#### `~/.config/valet/Extensions/`
#### `~/.config/valet/Extensions/`

This directory contains custom Valet extensions / commands.

이 디렉토리에는 사용자 정의 발렛 확장 명령이 포함되어 있습니다.

#### `~/.config/valet/Nginx/`
#### `~/.config/valet/Nginx/`

This directory contains all of Valet's Nginx site configurations. These files are rebuilt when running the `install`, `secure`, and `tld` commands.

이 디렉토리에는 발렛의 모든 Nginx 사이트 설정이 포함되어 있습니다. 이 파일은 `install`, `secure` 및 `tld` 명령을 실행할 때 다시 빌드됩니다.

#### `~/.config/valet/Sites/`
#### `~/.config/valet/Sites/`

This directory contains all of the symbolic links for your [linked projects](#the-link-command).

이 디렉토리에는 [링크된 프로젝트](#the-link-command)에 대한 모든 심볼릭 링크가 포함되어 있습니다.

#### `~/.config/valet/config.json`
#### `~/.config/valet/config.json`

This file is Valet's master configuration file.

이 파일은 발렛의 마스터 설정 파일입니다.

#### `~/.config/valet/valet.sock`
#### `~/.config/valet/valet.sock`

This file is the PHP-FPM socket used by Valet's Nginx installation. This will only exist if PHP is running properly.

이 파일은 발렛의 Nginx 설치에 사용되는 PHP-FPM 소켓입니다. 이것은 PHP가 제대로 실행되는 경우에만 존재합니다.

#### `~/.config/valet/Log/fpm-php.www.log`
#### `~/.config/valet/Log/fpm-php.www.log`

This file is the user log for PHP errors.

이 파일은 PHP 오류에 대한 사용자 로그입니다.

#### `~/.config/valet/Log/nginx-error.log`
#### `~/.config/valet/Log/nginx-error.log`

This file is the user log for Nginx errors.

이 파일은 Nginx 오류에 대한 사용자 로그입니다.

#### `/usr/local/var/log/php-fpm.log`
#### `/usr/local/var/log/php-fpm.log`

This file is the system log for PHP-FPM errors.

이 파일은 PHP-FPM 오류에 대한 시스템 로그입니다.

#### `/usr/local/var/log/nginx`
#### `/usr/local/var/log/nginx`

This directory contains the Nginx access and error logs.

이 디렉토리에는 Nginx 액세스 및 오류 로그가 포함되어 있습니다.

#### `/usr/local/etc/php/X.X/conf.d`
#### `/usr/local/etc/php/X.X/conf.d`

This directory contains the `*.ini` files for various PHP configuration settings.

이 디렉토리에는 다양한 PHP 설정을 위한 `.ini` 파일이 존재합니다.

#### `/usr/local/etc/php/X.X/php-fpm.d/valet-fpm.conf`
#### `/usr/local/etc/php/X.X/php-fpm.d/valet-fpm.conf`

This file is the PHP-FPM pool configuration file.

이 파일은 PHP-FPM pool의 설정 파일입니다.

#### `~/.composer/vendor/laravel/valet/cli/stubs/secure.valet.conf`
#### `~/.composer/vendor/laravel/valet/cli/stubs/secure.valet.conf`

This file is the default Nginx configuration used for building SSL certificates for your sites.

이 파일은 사이트에 대한 SSL 인증서를 빌드하는 데 사용되는 기본 Nginx 구성입니다.
