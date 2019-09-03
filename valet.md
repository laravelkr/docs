# 라라벨 발렛

- [시작하기](#introduction)
    - [발렛 또는 홈스테드](#valet-or-homestead)
- [설치하기](#installation)
    - [업그레이드하기](#upgrading)
- [사이트 동작시키기](#serving-sites)
    - ["Park" 명령어](#the-park-command)
    - ["Link" 명령어](#the-link-command)
    - [TLS를 사용한 안전한 사이트](#securing-sites)
- [사이트 공유하기](#sharing-sites)
- [사이트 별 환경 변수](#site-specific-environment-variables)
- [사용자 정의-커스텀 발렛 드라이버](#custom-valet-drivers)
    - [로컬 드라이버](#local-drivers)
- [기타 발렛 명령어들](#other-valet-commands)

<a name="introduction"></a>
## 시작하기

발렛은 Mac에서 가벼움을 선호하는 사람들을 위한 라라벨 개발 환경입니다. Vagrant도, `/etc/hosts` 파일도 필요하지 않습니다. 심지어 로컬 터널을 사용하여 사이트를 공유할 수도 있습니다. _우린 이런걸 좋아합니다._

라라벨 발렛 설정은 여러분의 Mac 머신이 시작할 때 백그라운드에서 항상 [Nginx](https://www.nginx.com/)가 실행되도록 설정합니다. 그러면 [DnsMasq](https://en.wikipedia.org/wiki/Dnsmasq)를 사용하여 발렛은 `*.test` 도메인에 대한 모든 요청을 여러분의 로컬 머신에 설치된 사이트로 지정되도록 프록시로 동작합니다.

다시말해, 대략 7MB 의 RAM을 사용하는 매우 빠른 라라벨 개발 환경입니다. 발렛은 Vagrant 나 홈스테드를 완전히 대체하지는 않지만, 여러분이 유연하고, 속도를 중시하며, 또는 RAM 이 제한되어 있는 머신에서 동작시키는 데에는 훌륭한 대안입니다.

별다른 설정 없이도, 발렛은 다음을 지원하지만, 이게 전부는 아닙니다:


- [Lumen](https://lumen.laravel.com)
- [CakePHP 3](https://cakephp.org)
- [Contao](https://contao.org/en/)
- [Drupal](https://www.drupal.org/)
- [Joomla](https://www.joomla.org/)
- [Kirby](https://getkirby.com/)
- [OctoberCMS](https://octobercms.com/)
- [Slim](https://www.slimframework.com)
- [Statamic](https://statamic.com)
- Static HTML
- [WordPress](https://wordpress.org)
- [Zend](https://framework.zend.com)



여러분은 발렛을 고유한 [사용자 정의 드라이버](#custom-valet-drivers)를 통해서 확장할 수 있습니다.

<a name="valet-or-homestead"></a>
### 발렛 또는 홈스테드

이미 알고 있다시피, 라라벨은 또다른 라라벨 개발 환경인 [홈스테드](/docs/{{version}}/homestead)를 제공하고 있습니다. 홈스테드와 발렛은 이용자의 로컬 개발에 대한 목적과 방식이 다릅니다. 홈스테드는 자동화된 Nginx 설정과 함께 전체 우분투 가상 머신 환경을 제공합니다. 홈스테드는 리눅스 개발 환경에 대한 전체 가상화를 원하거나, 윈도우 / 리눅스 환경에서라면 아주 멋진 선택이 됩니다.

발렛은 Mac 만을 지원하고, 그리고 여러분의 로컬 머신에 직접 PHP 와 데이터베이스 서버를 설치할 필요가 있습니다. [Homebrew](https://brew.sh/)를 사용해서 `brew install php` 그리고 `brew install mysql`와 같은 명령어를 실행하면 손쉽게 설치가 가능합니다. 발렛은 최소한의 리소스를 사용하여 매우 빠른 로컬 개발 환경을 제공합니다, 따라서 단지 PHP / MySQL 그리고 전체 가상 개발 환경이 필요하지 않은 개발자에게 적합합니다.

Valet과 Homestead는 모두 Laravel 개발 환경을 구성하기위한 훌륭한 선택입니다. 선택은 개인적인 취향과 팀의 필요에 따라 달라질 것입니다.

<a name="installation"></a>
## 설치하기

**발렛은 macOS와 [Homebrew](https://brew.sh/)를 필요로 합니다. 설치하기 전에, 여러분은 Apache 또는 Nginx 가 로컬 머신의 80번 포트를 바인딩 하지 않고 있다는 것을 확인해야 합니다.**


- [Homebrew](https://brew.sh/) 설치하거나 `brew update`를 사용하여 최신 버전으로 업데이트 하십시오.
- `brew install php` Homebrew 명령어를 사용하여 PHP7.3을 설치하십시오.
- [Composer](https://getcomposer.org) 설치.
- `composer global require laravel/valet` 명령어를 사용하여 컴포저로 발렛을 설치하십시오. 여러분 시스템의 "PATH" 에 `~/.composer/vendor/bin` 디렉토리가 들어 있는지 확인하십시오.
- `valet install` 명령어를 실행하십시오. 이 명령어는 발렛과 DnsMasq 를 설치하고 설정하여 발렛 데몬을 여러분의 시스템이 시작할 때 구동되도록 등록할 것입니다.


발렛이 설치되고나면, 터미널에서 `ping foobar.test` 와 같은 명령어를 사용하여 아무 `*.test` 도메인으로 핑을 시도해보십시오. 발렛이 올바르게 설치되었다면 `127.0.0.1` 로 부터 응답을 확인할 수 있어야만 합니다.

발렛은 여러분의 머신이 매번 부팅될 때 자동적으로 데몬이 시작될 것입니다. 처음 발렛이 설치되어 완료되고 나면 별도의 `valet start` 나 `valet install` 을 실행할 필요가 없습니다.

#### 다른 도메인 사용하기

기본적으로 발렛은 프로젝트를 `.test` TLD(Top Level Domain)를 프로젝트 에 제공합니다. 다른 도메인을 사용하고자 한다면, `valet tld tld-name` 명령어를 사용하면 됩니다.

예를 들어, `.test` 대신에 `.app` 을 사용하고자 한다면 `valet tld app` 을 실행하면 발렛이 `*.app` 을 프로젝트에 자동적으로 제공하게 될것입니다.

#### 데이터베이스

데이터베이스가 필요하다면, 커맨드 라인에서 `brew install mysql@5.7`를 실행하여 MySQL를 설치해 보십시오. MySQL가 설치되고 나면, `brew services start mysql@5.7` 명령어를 통해서 서비스를 시작할 수 있습니다. 그렇게 하면 `127.0.0.1` 서버에 `root` 계정으로 패스워드 없이 접속 할 수 있습니다.

#### PHP 버전

Valet은 `valet use php@version` 명령을 사용하여 PHP 버전을 전환 할 수있게합니다. Valet은 Brew를 통해 지정된 PHP 버전을 아직 설치하지 않은 경우 설치합니다.

    valet use php@7.2
    
    valet use php
    
<a name="upgrading"></a>
### 업그레이드 하기

터미널에서 `composer global update` 명령어를 사용하여 Valet 설치파일을 업데이트 할 수 있습니다. 업그레이드가 진행되면, `valet install` 명령어를 실행시켜 필요한 경우 설정파일에 추가적인 업그레이드가 진행되도록 하십시오.

#### 발렛 2.0 으로 업그레이드 하기

발렛 2.0에서는 발렛이 Caddy 에서 Nginx 를 사용하도록 변경되었습니다. 새로운 버전으로 업그레이드 하기 전에, 다음의 명령어를 사용하여 Caddy 데몬을 중지하고 언인스톨 해야합니다:

    valet stop
    valet uninstall

그 다음, 최신 발렛 버전을 설치합니다. 발렛을 어떻게 설치했느냐에 따라서, Git 또는 Composer 를 통해서 진행됩니다. 컴포저를 통해서 발렛을 설치했다면, 최신버전을 설치하기 위해서 다음 명령어를 사용해야합니다:

    composer global require laravel/valet

일단 새로운 발렛 소스 코드가 다운로드 되고나서,`install` 명령을 실행해야합니다:

    valet install
    valet restart

업그레이드를 완료하면, 사이트를 다시 park 하거나 다시 link 설정해야 합니다.

<a name="serving-sites"></a>
## 사이트 동작시키기

발렛이 설치되고 나면, 사이트를 동작시킬 준비가된 것입니다. 발렛은 라라벨 사이트를 구동하는데 도움을 줄 수 있는 `park` 와 `link` 두가지 명령어를 제공합니다:

<a name="the-park-command"></a>
**`park` 명령어**

- Mac 에 `mkdir ~/Sites`와 같은 명령어를 실행하여 새로운 디렉토리를 생성합니다. 다음으로, `cd ~/Sites` 와 `valet park`을 실행합니다. 이 명령어는 현재 작업 디렉토리를 사이트로 접속했을 때 발렛이 찾게 되는 디렉토리로 등록합니다.
- 다음으로 이 디렉토리에서 새로운 라라벨 사이트를 생성합니다: `laravel new blog`.
- 브라우저에서 `http://blog.test` 사이트를 열어서 확인합니다.


**That's all there is to it.** Now, any Laravel project you create within your "parked" directory will automatically be served using the `http://folder-name.test` convention.
**여기까지가 전부 입니다.** 이제 여러분이 "parked(지정한)" 디렉토리 안에 생성된 라라벨 프로젝트는 자동으로 `http://folder-name.test` 형태로 접속할 수 있습니다.

<a name="the-link-command"></a>
**`link` 명령어**

`link` 명령어는 사이트를 동작시킬 때 사용됩니다. 이 명령어는 전체 디렉토리가 아니라 하나의 디렉토리 안에서 한개의 사이트를 제공하기를 원할 때 유용합니다.

- 명령어를 사용하기 위해서, 프로젝트 중 하나의 디렉토리에 대해서 터미널에서 `valet link app-name` 을 실행합니다. 발렛은 현재 작업 디렉토리를 `~/.config/valet/Sites`가 지정하도록 심볼릭 링크를 생성할 것입니다.
- `link` 명령어를 실행한 다음에, 브라우저에서 `http://app-name.test` 로 접속할 수 있습니다.


링크로 연결된 디렉토리들의 모든 목록을 확인하고자 한다면, `valet links` 명령어를 실행하십시오. `valet unlink app-name`는 디렉토리에 대한 심볼릭 링크를 제거하는데 사용할 수 있습니다.

> {tip} `valet link` 를 동일한 프로젝트에 대해서 다수의 (서브) 도메인을 통해서 사이트가 동작하도록 할 수 있습니다. 프로젝트에 서브도메인이나, 다른 도메인을 추가하려면 프로젝트 폴더에서 `valet link subdomain.app-name` 을 실행하십시오.

<a name="securing-sites"></a>
**TLS를 사용한 안전한 사이트**

기본적으로 발렛은 사이트를 일반적인 HTTP 를 통해서 제공합니다. 하지만 사이트를 HTTP/2 를 사용하여 TLS 암호화되어 제공하려면, `secure` 명령어를 사용하십시오. 예를 들어 `laravel.test` 도메인을 발렛을 통해서 제공하고자 한다면, 다음 명령어를 통해서 안전하게 구동해야만 합니다:

    valet secure laravel

사이트를 "안전하지 않게" 되돌리고 일반적인 HTTP 로 통신하게 하려면 `unsecure` 명령어를 사용하십시오. `secure` 명령어 같이, 이 명령어는 안전한 통신을 그만두고자 하는 호스트의 이름을 전달 받습니다:

    valet unsecure laravel

<a name="sharing-sites"></a>
## 사이트 공유

발렛에는 여러분의 로컬 사이트를 세상과 공유하는 명령어도 포함되어 있습니다. 발렛만 설치되어 있으면 추가적인 소프트웨어의 설치도 필요하지 않습니다.

사이트를 공유하기 위해서는, 터미널 상에서 사이트의 디렉토리로 이동한 뒤에 `valet share` 명령어를 입력하십시오. 공개된 접근가능한 URL 이 여러분의 클립보드에 복사되고 브라우저에서 직접 붙여넣을 수 있습니다. 이게 끝입니다.

사이트의 공유를 중단하려면 `컨트롤 + C`를 눌러 프로세스를 취소하십시오.

<a name="site-specific-environment-variables"></a>
## 사이트 별 환경변수

다른 프레임워크를 사용하는 일부 애플리케이션은 서버 환경 변수에 의존 할 수 있지만 이 변수를 프로젝트 내에 추가하는 방법을 제공하지는 않습니다. Valet을 사용하면 프로젝트의 루트 내에 `.valet-env.php` 파일을 추가하여 사이트 별 환경 변수를 구성 할 수 있습니다. 이 변수들은 `$_SERVER` 전역 변수의 배열에 추가 될 것입니다 :

    <?php

    return [
        'WEBSITE_NAME' => 'My Blog',
    ];

<a name="custom-valet-drivers"></a>
## 사용자 정의-커스텀 발렛 드라이버

여러분은 발렛에서 기본적으로 지원하고 있지 않은 다른 프레임워크나 CMS와 같은 PHP 애플리케이션을 제공하기 위해서 여러분의 고유한 발렛 "드라이버"를 작성할 수 있습니다. 발렛을 설치할 때 생성되는 `~/.config/valet/Drivers`에는 `SampleValetDriver.php` 파일이 들어 있습니다. 이 파일은 어떻게 사용자 정의 드라이버를 작성할 것인가에 대한 샘플 드라이버의 구현 코드입니다. 드라이버를 구현하는데 필요한 메소드는 `serves`, `isStaticFile`, 그리고 `frontControllerPath`의 3가지 입니다.

세가지 메소드들 모두 `$sitePath`, `$siteName`, 그리고 `$uri` 값을 인자로 받습니다. `$sitePath` 는 `/Users/Lisa/Sites/my-project`와 같은 여러분의 머신에서 사이트가 제공되기 할 전체 경로 입니다. `$siteName` 는 도메인의 "호스트" / "사이트 이름" 부분 입니다(`my-project`). `$uri` 는 유입되는 요청 URI 입니다(`/foo/bar`).

여러분의 발렛 드라이버를 작성하고 나면, 이 파일을 `FrameworkValetDriver.php` 네이밍 컨벤션에 맞게 `~/.config/valet/Drivers` 디렉토리에 저장하십시오. 예를 들자면, 여러분이 워드프레스를 위한 발렛 드라이버를 작성하였다면 여러분의 파일 이름음 `WordPressValetDriver.php`가 되어야 합니다.

사용자 정의 발렛 드라이버에서 구현하는 메소드의 샘플 코드를 살펴 보겠습니다.

#### `serves` 메소드

`serves` 메소드는 드라이버가 유입된 요청을 처리해야 할 경우 `true`를 반환합니다. 그렇지 않은 경우는 `false`를 반환합니다. 따라서 이 메소드 안에서 여러분은 주어진 `$sitePath` 가 동작시키고자 하는 유형의 프로젝트인지 확인도록 해야합니다.

예를 들어, `WordPressValetDriver`를 작성한다고 가정해 봅시다. `serves` 메소드는 다음과 같을 것입니다:

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

#### `isStaticFile` 메소드

`isStaticFile` 는 유입된 요청이 이미지나 스타일 시트와 같은 "정적" 파일인지 판단합니다. 파일이 정적파일이라면 해당 파일이 존재하는 디스크의 전체 경로를 반환합니다. 유입된 요청이 정적 파일이 아니라면 `false` 를 반환해야 합니다:

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

> {note} `isStaticFile` 메소드는 유입된 요청이 `/` 가 아니고 `serves` 메소드가 `true`를 반환하는 경우에만 호출될 것입니다.

#### `frontControllerPath` 메소드

`frontControllerPath` 메소드는 일반적으로 "index.php" 파일 또는 유사한 파일이 되는 애플리케이션의 "프론트 컨트롤러"에 대한 전체 경로를 반환합니다:

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
### 로컬 드라이버

하나의 애플리케이션을 위한 커스텀 Valet 드라이버를 정의하고자 한다면, 애플리케이션의 루트 디렉토리에 `LocalValetDriver.php` 파일을 생성하십시오. 커스텀 드라이버는 베이스 `ValetDriver` 클래스를 상속받거나 `LaravelValetDriver`과 같은 기존 애플리케이션에서 지정한 드라이버를 상속해야합니다:

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
## 기타 발렛 명령어들

명령어 | 설명
------------- | -------------
`valet forget` | parked 디렉토리 목록에서 디렉토리를 제거하기 위해서는 "parked" 된 디렉토리에서 이 명령어를 실행하십시오.
`valet log` | Valet의 서비스가 작성한 로그 목록보기.
`valet paths` | "parked" 된 모든 경로를 확인합니다.
`valet restart` | 발렛 데몬을 재시작 합니다.
`valet start` | 발렛 데몬을 시작합니다.
`valet stop` | 발렛 데몬을 중지합니다.
`valet uninstall` | Valet 데몬 제거.
