# 배포

- [시작하기](#introduction)
- [서버 설정하기](#server-configuration)
    - [Nginx](#nginx)
- [최적화](#optimization)
    - [Autoloader 최적화](#autoloader-optimization)
    - [설정내역 로딩 최적화](#optimizing-configuration-loading)
    - [라우트 로딩 최적화](#optimizing-route-loading)
- [Forge에서 배포하기](#deploying-with-forge)

<a name="introduction"></a>
## 시작하기

여러분의 라라벨 애플리케이션이 실제 서비스에 배포할 준비가 되었다면, 가능한 한 효율적으로 애플리케이션이 구동되도록 하기 위해서 할 수 있는 몇가지 중요한 작업이 있습니다. 이 문서에서는 라라벨 애플리케이션이 제대로 배포되었는지 확인하기 위한 시발점에 대해서 설명합니다.

<a name="server-configuration"></a>
## 서버 설정

<a name="nginx"></a>
### Nginx

애플리케이션을 Nginx 가 동작하는 서버에 배포하는 경우 다음과 같은 설정 파일을 웹서버 설정으로 사용할 수 있습니다. 대부분의 경우 이 파일은 서버 설정에 따라서 사용자가 수정할 필요가 있습니다. 서버 관리에 도움이 필요하다면, [라라벨 Forge](https://forge.laravel.com)와 같은 서비스 사용을 고려해보십시오:

    server {
        listen 80;
        server_name example.com;
        root /example.com/public;

        add_header X-Frame-Options "SAMEORIGIN";
        add_header X-XSS-Protection "1; mode=block";
        add_header X-Content-Type-Options "nosniff";

        index index.html index.htm index.php;

        charset utf-8;

        location / {
            try_files $uri $uri/ /index.php?$query_string;
        }

        location = /favicon.ico { access_log off; log_not_found off; }
        location = /robots.txt  { access_log off; log_not_found off; }

        error_page 404 /index.php;

        location ~ \.php$ {
            fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
            include fastcgi_params;
        }

        location ~ /\.(?!well-known).* {
            deny all;
        }
    }

<a name="optimization"></a>
## 최적화

<a name="autoloader-optimization"></a>
### Autoloader 최적화

실 서버에 배포할 때 컴포저의 클래스 오토로딩 맵을 최적화 하여 컴포저가 주어진 클래스 파일을 빠르게 찾을 수 있도록 하십시오:

    composer install --optimize-autoloader --no-dev

> {tip} 오토로더를 최적화 하는데 더하여, 프로젝트의 소스 컨트롤 저장소에 `composer.lock` 파일을 포함하고 있는지 확인하십시오. `composer.lock` 파일이 존재한다면 프로젝트의 의존성을 보다 빠르게 설치할 수 있습니다.

<a name="optimizing-configuration-loading"></a>
### 설정내역 로딩 최적화

실 서버에 배포할 때에 배포 프로세스에 `config:cache` 아티즌 명령어를 실행하도록 하십시오:

    php artisan config:cache

이 명령어는 라라벨의 전체 설정 파일을 하나로 합쳐서, 캐시 파일을 만들고, 프레임워크가 설정파일을 로딩하기 위해서 여러개의 파일을 읽는 것을 줄여줍니다.

> {note} 배포 과정에서 `config:cache` 명령을 실행하면 설정 파일 내에서 `env` 함수 만 호출해야합니다. 설정이 캐시되면 `.env` 파일은 로드되지 않고, `env` 함수의 모든 호출은 `null` 를 돌려줍니다.

<a name="optimizing-route-loading"></a>
### 라우트 로딩 최적화

큰 애플리케이션과 같이 많은 라우트를 등록하는 경우에, 배포 프로세스에 `route:cache` 아티즌 명령어를 실행하도록 하십시오:

    php artisan route:cache

이 명령어는 라라벨의 전체 라우트 등록을 하나의 캐시 파일로 만들어 라우트를 등록하는 속도를 줄여줍니다.

> {note} 이 기능은 PHP의 시리얼라이제이션 기능을 사용하기 때문에, 컨트롤러 기반의 라우트로 구성된 라우트만 캐싱할 수 있습니다. PHP에서 클로저는 시리얼라이즈 할 수 없기 때문입니다.

<a name="deploying-with-forge"></a>
## Forge에서 배포하기

여러분이 서버 구성을 잘 관리할 준비가 되어 있지 않거나, 라라벨 애플리케이션을 실행하는데 필요한 여러 서비스들을 모두 구성하는게 어렵다면, 라라벨 Forge가 대안이 될 수 있습니다.

라라벨 Forge 는 DigitalOcean, Linode, AWS 와 같은 다양한 인프라를 제공하는 서비스 위에서 서버를 구성할 수 있습니다. 또한 Forge는 Nginx, MySQL, Redis, Memcached, Beanstalk 와 같은 라라벨 애플리케이션을 구축하는데 필요한 모든 툴들을 설치하고 관리해줍니다.
