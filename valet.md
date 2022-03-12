# 라라벨 발렛

- [시작하기](#introduction)
- [설치하기](#installation)
    - [발렛 업그레이드](#upgrading-valet)
- [사이트 동작시키기](#serving-sites)
    - ["Park" 명령어](#the-park-command)
    - ["Link" 명령어](#the-link-command)
    - [TLS를 사용한 안전한 사이트](#securing-sites)
    - [기본 사이트 제공](#serving-a-default-site)
- [사이트 공유하기](#sharing-sites)
    - [Ngrok을 통한 사이트 공유](#sharing-sites-via-ngrok)
    - [Expose를 통한 사이트 공유](#sharing-sites-via-expose)
    - [로컬 네트워크에서 사이트 공유](#sharing-sites-on-your-local-network)
- [사이트 별 환경 변수](#site-specific-environment-variables)
- [프록시 서비스](#proxying-services)
- [사용자 정의-커스텀 발렛 드라이버](#custom-valet-drivers)
    - [로컬 드라이버](#local-drivers)
- [기타 발렛 명령어들](#other-valet-commands)
- [Valet 디렉토리와 파일](#valet-directories-and-files)

<a name="introduction"></a>
## 시작하기

[라라벨 발렛](https://github.com/laravel/valet) 은 macOS 미니멀리스트를 위한 개발 환경입니다. 라라벨 발렛은 시스템이 시작될 때 항상 백그라운드에서 [Nginx](https://www.nginx.com/) 를 실행하도록 Mac을 구성합니다. 그런 다음 발렛은 [DnsMasq](https://en.wikipedia.org/wiki/Dnsmasq) 를 사용하여 `.test` 도메인의 모든 요청을 프록시하여 로컬 컴퓨터에 설치된 사이트를 바라보게 만듭니다.

즉, 발렛은 약 7MB의 RAM을 사용하는 매우 빠른 라라벨 개발 환경입니다. 발렛은 [Sail](/docs/{{version}}/sail) 또는 [Homestead](/docs/{{version}}/homestead)를 완전히 대체하지는 않지만, 유연한 기본기를 원하거나, 극한의 속도를 선호하거나, RAM이 제한된 컴퓨터에서 사용합니다.

별다른 설정 없이도, 발렛은 다음을 지원하지만, 이게 전부는 아닙니다.

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

여러분은 발렛을 고유한 [사용자 정의 드라이버](#custom-valet-drivers)를 통해서 확장할 수 있습니다.

<a name="installation"></a>
## 설치하기

> {note} 발렛은 macOS 및 [Homebrew](https://brew.s/h) 가 필요합니다. 설치하기 전에 Apache 또는 Nginx와 같은 다른 프로그램이 로컬 시스템의 포트 80에 바인딩되어 있지 않은지 확인해야 합니다.

시작하려면 먼저 `update` 명령을 사용하여 Homebrew가 최신 상태인지 확인해야 합니다.

    brew update

다음으로 Homebrew를 사용하여 PHP를 설치해야 합니다.

    brew install php

PHP를 설치하면 [Composer 패키지 관리자](https://getcomposer.org)를 설치할 준비가 된 것입니다. 또한 `~.composer/vendor/bin` 디렉토리가 시스템의 "PATH"에 있는지 확인해야 합니다. Composer가 설치된 후 Laravel 발렛을 글로벌 Composer 패키지로 설치할 수 있습니다.

    composer global require laravel/valet

마지막으로 발렛의 `install` 명령을 실행할 수 있습니다. 발렛 및 DnsMasq를 설정하고 설치합니다. 또한 발렛이 의존하는 데몬은 시스템이 시작될 때 실행되도록 설정됩니다.

    valet install

발렛이 설치되면 `ping foobar.test`와 같은 명령을 사용하여, 터미널에서 `.test` 도메인에 대해 ping을 시도하십시오. 발렛이 올바르게 설치된 경우, 이 도메인이 `127.0.0.1`로 응답하는 것을 볼 수 있습니다.

발렛은 장비가 부팅될 때마다 필요한 서비스를 자동으로 시작합니다.

<a name="php-versions"></a>
#### PHP 버전

발렛을 사용하면 `valet use php@version` 명령을 사용하여 PHP 버전을 전환할 수 있습니다. 발렛은 Homebrew가 아직 설치되지 않은 경우, 지정한 PHP 버전을 설치합니다.

    valet use php@7.2

    valet use php

프로젝트 루트에 `.valetphprc` 파일을 생성할 수도 있습니다. `.valetphprc` 파일에는 사이트에서 사용해야 하는 PHP 버전이 포함되어야 합니다.

    php@7.2

이 파일이 생성되면 `valet use` 명령을 실행하기만 하면 이 명령이 파일을 읽어 사이트의 기본 PHP 버전을 결정합니다.

> {note} 발렛은 여러 PHP 버전이 설치되어 있어도 한 번에 하나의 PHP 버전만 제공합니다.

<a name="database"></a>
#### 데이터베이스

애플리케이션에 데이터베이스가 필요한 경우 [DBngin](https://dbngin.com)을 확인하세요. DBngin은 MySQL, PostgreSQL 및 Redis를 포함하는 무료 올인원 데이터베이스 관리 도구를 제공합니다. DBngin을 설치한 후 `root` 사용자 이름과 비밀번호에 빈 문자열을 사용하여 `127.0.0.1`의 데이터베이스에 연결할 수 있습니다.

<a name="resetting-your-installation"></a>
#### 설치 재설정

발렛 설치를 제대로 실행하는 데 문제가 있는 경우 `composer global update` 명령 다음에 `valet install`을 실행하면 설치가 재설정되고 다양한 문제를 해결할 수 있습니다. 드문 경우지만 `valet uninstall --force` 다음에 `valet install`을 실행하여 발렛을 "강제 초기화"해야 할 수도 있습니다.

<a name="upgrading-valet"></a>
### 발렛 업그레이드

터미널에서 `composer global update` 명령을 실행하여 발렛 설치를 업데이트할 수 있습니다. 업그레이드 후에는 발렛이 필요할 경우, 설정 파일을 추가로 업그레이드할 수 있도록 `valet install` 명령을 실행하는 것이 좋습니다.

<a name="serving-sites"></a>
## 사이트 동작시키기

발렛이 설치되면 라라벨 애플리케이션 서비스를 시작할 준비가 된 것입니다. 발렛은 애플리케이션을 제공하는 데 도움이 되는 두 가지 명령인 `park`와 `link`를 제공합니다.

<a name="the-park-command"></a>
### `park` 명령

`park` 명령은 애플리케이션이 포함된 디렉토리를 머신에 등록합니다. 디렉토리가 발렛으로 "파킹"되면 해당 디렉토리 내의 모든 디렉토리는 웹 브라우저의 `http://<directory-name>.test`에서 액세스할 수 있습니다.

    cd ~/Sites

    valet park

그게 전부입니다. 이제 "parked" 디렉토리 내에 생성한 모든 애플리케이션은 `http://<directory-name>.test` 규칙을 사용하여 자동으로 제공됩니다. 따라서 파킹된 디렉토리에 "laravel"이라는 디렉토리가 포함되어 있으면 해당 디렉토리 내의 애플리케이션은 `http://laravel.test`에서 액세스할 수 있습니다. 또한 발렛은 와일드카드 하위 도메인(`http:foo.laravel.test`)을 사용하여 사이트에 자동으로 액세스할 수 있도록 합니다.

<a name="the-link-command"></a>
### `link` 명령

`link` 명령은 라라벨 애플리케이션을 제공하는 데에도 사용할 수 있습니다. 이 명령은 전체 디렉토리가 아닌 디렉토리의 단일 사이트를 제공하려는 경우에 유용합니다.

    cd ~/Sites/laravel

    valet link

애플리케이션이 `link` 명령을 사용하여 발렛에 연결되면, 해당 디렉터리 이름을 사용하여 애플리케이션에 액세스할 수 있습니다. 따라서 위의 예시에서 링크된 사이트는 `http:laravel.test`로 접속할 수 있습니다. 또한 발렛은 와일드카드 하위 도메인(`http:foo.laravel.test`)을 사용하여 사이트에 자동으로 액세스할 수 있도록 합니다.

다른 호스트 이름에서 애플리케이션을 제공하려면, `link` 명령에 호스트 이름을 전달할 수 있습니다. 예를 들어, 다음 명령을 실행하여 `http://application.test`에서 애플리케이션을 접속할 수 있도록 할 수 있습니다.

    cd ~/Sites/laravel

    valet link application

`links` 명령을 실행하여 연결된 모든 디렉토리 목록을 표시할 수 있습니다.

    valet links

`unlink` 명령은 사이트의 심볼릭 링크를 제거하는 데 사용할 수 있습니다.

    cd ~/Sites/laravel

    valet unlink

<a name="securing-sites"></a>
### TLS를 사용한 안전한 사이트

기본적으로 발렛은 HTTP를 통해 사이트를 제공합니다. 그러나 HTTP2를 사용하여 암호화된 TLS를 통해 사이트를 제공하려는 경우 `secure` 명령을 사용할 수 있습니다. 예를 들어, `laravel.test` 도메인에서 발렛이 사이트를 제공하는 경우 다음 명령을 실행하여 사이트를 보호해야 합니다.

    valet secure laravel

사이트를 "안전하지 않게" 되돌리고 일반적인 HTTP 로 통신하게 하려면 `unsecure` 명령어를 사용하십시오. `secure` 명령어 같이, 이 명령어는 안전한 통신을 그만두고자 하는 호스트의 이름을 전달 받습니다.

    valet unsecure laravel

<a name="serving-a-default-site"></a>
### 기본 사이트 제공

알 수 없는 `test` 도메인을 방문할 때 `404` 대신 `default` 사이트로 접속되도록 발렛을 설정하고 싶을수도 있습니다. 이때는 기본 사이트 역할을 해야 하는 사이트 주소가 포함된 `~/.config/valet/config.json` 설정 파일에 `default` 옵션을 추가할 수 있습니다.

    "default": "/Users/Sally/Sites/foo",

<a name="sharing-sites"></a>
## 사이트 공유

발렛에는 로컬 사이트를 외부와 공유하는 명령도 포함되어 있어, 모바일 장치에서 사이트를 쉽게 테스트하거나 팀 구성원 및 클라이언트와 공유할 수 있습니다.

<a name="sharing-sites-via-ngrok"></a>
### Ngrok을 통한 사이트 공유

사이트를 공유하려면 터미널에서 사이트 디렉토리로 이동하여 발렛의 `share` 명령을 실행하세요. 공개적으로 액세스할 수 있는 URL이 클립보드에 삽입되고, 브라우저에 직접 붙여넣거나 팀과 공유할 수 있습니다.

    cd ~/Sites/laravel

    valet share

사이트 공유를 중지하려면 `Control + C`를 누르면 됩니다.

> {tip} `valet share --region=eu`와 같은 추가 Ngrok 매개변수를 share 명령에 전달할 수 있습니다. 자세한 내용은 [ngrok 문서](https://ngrok.com/docs) 를 참조하세요.

<a name="sharing-sites-via-expose"></a>
### Expose를 통한 사이트 공유

[Expose](https://expose.dev)가 설치되어 있으면, 터미널에서 사이트 디렉토리로 이동하고 `expose` 명령을 실행하여 사이트를 공유할 수 있습니다. 지원하는 추가 명령줄 매개변수에 대한 정보는 [Expose 문서](https://expose.dev/docs) 를 참조하십시오. 사이트를 공유한 후 Expose는 다른 장치에서 또는 팀 구성원 간에 사용할 수 있는 공유 가능한 URL을 표시합니다.

    cd ~/Sites/laravel

    expose

사이트 공유를 중지하려면 `Control + C`를 누르면 됩니다.

<a name="sharing-sites-on-your-local-network"></a>
### 로컬 네트워크에서 사이트 공유

발렛은 기본적으로 내부 `127.0.0.1` 인터페이스로 들어오는 트래픽으로 제한하므로, 개발 시스템이 인터넷의 보안 위험에 노출되지 않습니다.

로컬 네트워크의 다른 장치가 컴퓨터의 IP 주소(예: `192.168.1.10/application.test`)를 통해, 컴퓨터의 발렛 사이트에 접근하도록 허용하려면, 해당 Nginx 설정 파일을 수동으로 편집해야 합니다. `listen` 지시문에 대한 제한을 제거하려면 사이트를 방문하세요. 포트 80 및 443에 대한 `listen` 지시문에서 `127.0.0.1:` 접두사를 제거해야 합니다.

프로젝트에서 `valet secure`를 실행하지 않은 경우 `/usr/local/etc/nginx/valet/valet.conf` 파일을 편집하여 모든 비 HTTPS 사이트에 대한 네트워크 액세스를 열어 줄 수 있습니다. 그러나 HTTPS를 통해 프로젝트 사이트를 제공하는 경우 (사이트에 대해 `valet secure`를 실행 한 경우) `~/.config/valet/Nginx/app-name.test` 파일을 편집해야합니다.

Nginx 설정을 변경하고 나면 `valet restart` 명령을 실행하여, 변경된 설정을 적용하십시오.

<a name="site-specific-environment-variables"></a>
## 사이트 별 환경변수

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
## 프록시 서비스

때로는 발렛 도메인을 로컬 컴퓨터의 다른 서비스로 프록시 할 수 있습니다. 예를 들어 Docker에서 별도의 사이트를 실행하면서 발렛를 실행해야하는 경우가 있습니다. 그러나 발렛과 Docker는 동시에 포트 80에 바인딩 할 수 없습니다.

이를 해결하기 위해 `proxy` 명령을 사용하여 프록시를 생성 할 수 있습니다. 예를 들어 `http://elasticsearch.test`로 들어오는 모든 트래픽을 `http://127.0.0.1:9200`로 보내도록 프록시 처리 할 수 있습니다.

```bash
// Proxy over HTTP...
valet proxy elasticsearch http://127.0.0.1:9200

// Proxy over TLS + HTTP/2...
valet proxy elasticsearch http://127.0.0.1:9200 --secure
```

`unproxy` 명령을 사용하여 프록시를 제거 할 수 있습니다.

    valet unproxy elasticsearch

`proxys` 명령을 사용하여 프록시된 모든 사이트 설정을 나열할 수 있습니다.

    valet proxies

<a name="custom-valet-drivers"></a>
## 사용자 정의-커스텀 발렛 드라이버

발렛에서 기본적으로 지원하지 않는 프레임워크 또는 CMS에서 실행되는 PHP 애플리케이션을 제공하기 위해 자체 발렛 "드라이버"를 작성할 수 있습니다. 발렛을 설치하면 `SampleValetDriver.php` 파일이 포함된 `~/.config/valet/Drivers` 디렉토리가 생성됩니다. 이 파일에는 사용자 지정 드라이버를 작성하는 방법을 보여 주는 샘플 드라이버 구현이 포함되어 있습니다. 드라이버를 작성하려면 `serves`, `isStaticFile`, `frontControllerPath`의 세 가지 방법만 구현하면 됩니다.

세가지 메소드들 모두 `$sitePath`, `$siteName`, 그리고 `$uri` 값을 인자로 받습니다. `$sitePath` 는 `/Users/Lisa/Sites/my-project`와 같은 여러분의 머신에서 사이트가 제공되기 할 전체 경로 입니다. `$siteName` 는 도메인의 "호스트" / "사이트 이름" 부분 입니다(`my-project`). `$uri` 는 유입되는 요청 URI 입니다(`/foo/bar`).

여러분의 발렛 드라이버를 작성하고 나면, 이 파일을 `FrameworkValetDriver.php` 네이밍 컨벤션에 맞게 `~/.config/valet/Drivers` 디렉토리에 저장하십시오. 예를 들자면, 여러분이 워드프레스를 위한 발렛 드라이버를 작성하였다면 여러분의 파일 이름음 `WordPressValetDriver.php`가 되어야 합니다.

사용자 정의 발렛 드라이버에서 구현하는 메소드의 샘플 코드를 살펴 보겠습니다.

<a name="the-serves-method"></a>
#### `serves` 메소드

드라이버가 들어오는 요청을 처리해야 하는 경우 `serves` 메서드는 `true`를 반환해야 합니다. 그렇지 않으면 메서드는 `false`을 반환해야 합니다. 따라서 이 메소드 내에서 제공된 `sitePath`에 제공하려는 유형의 프로젝트가 포함되어 있는지 확인해야 합니다.

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
#### `isStaticFile` 메소드

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

> {note} `isStaticFile` 메소드는 유입된 요청이 `/` 가 아니고 `serves` 메소드가 `true`를 반환하는 경우에만 호출될 것입니다.

<a name="the-frontcontrollerpath-method"></a>
#### `frontControllerPath` 메소드

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
### 로컬 드라이버

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
## 기타 발렛 명령어들

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
## Valet 디렉토리 및 파일

발렛 환경설정 관련 문제를 해결하기 위해 다음 디렉토리 및 파일 정보가 도움이 될 수 있습니다.

#### `~/.config/valet`

발렛의 모든 설정이 들어있습니다. 이 디렉토리의 백업해 유지 관리할 수 있습니다.

#### `~/.config/valet/dnsmasq.d/`

이 디렉토리에는 DNSMasq의 설정이 포함되어 있습니다.

#### `~/.config/valet/Drivers/`

이 디렉토리에는 발렛의 드라이버가 포함되어 있습니다. 드라이버는 특정 프레임워크 CMS가 제공되는 방식을 결정합니다.

#### `~/.config/valet/Extensions/`

이 디렉토리에는 사용자 정의 발렛 확장 명령이 포함되어 있습니다.

#### `~/.config/valet/Nginx/`

이 디렉토리에는 발렛의 모든 Nginx 사이트 설정이 포함되어 있습니다. 이 파일은 `install`, `secure` 및 `tld` 명령을 실행할 때 다시 빌드됩니다.

#### `~/.config/valet/Sites/`

이 디렉토리에는 [링크된 프로젝트](#the-link-command)에 대한 모든 심볼릭 링크가 포함되어 있습니다.

#### `~/.config/valet/config.json`

이 파일은 발렛의 마스터 설정 파일입니다.

#### `~/.config/valet/valet.sock`

이 파일은 발렛의 Nginx 설치에 사용되는 PHP-FPM 소켓입니다. 이것은 PHP가 제대로 실행되는 경우에만 존재합니다.

#### `~/.config/valet/Log/fpm-php.www.log`

이 파일은 PHP 오류에 대한 사용자 로그입니다.

#### `~/.config/valet/Log/nginx-error.log`

이 파일은 Nginx 오류에 대한 사용자 로그입니다.

#### `/usr/local/var/log/php-fpm.log`

이 파일은 PHP-FPM 오류에 대한 시스템 로그입니다.

#### `/usr/local/var/log/nginx`

이 디렉토리에는 Nginx 액세스 및 오류 로그가 포함되어 있습니다.

#### `/usr/local/etc/php/X.X/conf.d`

이 디렉토리에는 다양한 PHP 설정을 위한 `.ini` 파일이 존재합니다.

#### `/usr/local/etc/php/X.X/php-fpm.d/valet-fpm.conf`

이 파일은 PHP-FPM pool의 설정 파일입니다.

#### `~/.composer/vendor/laravel/valet/cli/stubs/secure.valet.conf`

이 파일은 사이트에 대한 SSL 인증서를 빌드하는 데 사용되는 기본 Nginx 구성입니다.
