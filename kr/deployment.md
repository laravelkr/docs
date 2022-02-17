# Deployment
# 배포

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Server Requirements](#server-requirements)
- [서버 요구 사항](#server-requirements)
- [Server Configuration](#server-configuration)
- [서버 설정하기](#server-configuration)
    - [Nginx](#nginx)
    - [Nginx](#nginx)
- [Optimization](#optimization)
- [최적화](#optimization)
    - [Autoloader Optimization](#autoloader-optimization)
    - [Autoloader 최적화](#autoloader-optimization)
    - [Optimizing Configuration Loading](#optimizing-configuration-loading)
    - [설정내역 로딩 최적화](#optimizing-configuration-loading)
    - [Optimizing Route Loading](#optimizing-route-loading)
    - [라우트 로딩 최적화](#optimizing-route-loading)
    - [Optimizing View Loading](#optimizing-view-loading)
    - [뷰 로딩 최적화](#optimizing-view-loading)
- [Debug Mode](#debug-mode)
- [디버그 모드](#debug-mode)
- [Deploying With Forge / Vapor](#deploying-with-forge-or-vapor)
- [Forge / Vapor로 배포하기](#deploying-with-forge-or-vapor)

<a name="introduction"></a>
## Introduction
## 시작하기

When you're ready to deploy your Laravel application to production, there are some important things you can do to make sure your application is running as efficiently as possible. In this document, we'll cover some great starting points for making sure your Laravel application is deployed properly.

여러분의 라라벨 애플리케이션이 실제 서비스에 배포할 준비가 되었다면, 가능한 한 효율적으로 애플리케이션이 구동되도록 하기 위해서 할 수 있는 몇가지 중요한 작업이 있습니다. 이 문서에서는 라라벨 애플리케이션이 제대로 배포되었는지 확인하기 위한 시발점에 대해서 설명합니다.

<a name="server-requirements"></a>
## Server Requirements
## 서버 요구 사항

The Laravel framework has a few system requirements. You should ensure that your web server has the following minimum PHP version and extensions:

라라벨 프레임워크는 몇 가지 시스템 요구 사항이 있습니다. 웹 서버에 최소 PHP 버전 및 extension이 다음과 같은지 확인해야 합니다.

- PHP >= 7.3
- BCMath PHP Extension
- Ctype PHP Extension
- Fileinfo PHP Extension
- JSON PHP Extension
- Mbstring PHP Extension
- OpenSSL PHP Extension
- PDO PHP Extension
- Tokenizer PHP Extension
- XML PHP Extension

<a name="server-configuration"></a>
## Server Configuration
## 서버 설정

<a name="nginx"></a>
### Nginx
### Nginx

If you are deploying your application to a server that is running Nginx, you may use the following configuration file as a starting point for configuring your web server. Most likely, this file will need to be customized depending on your server's configuration. **If you would like assistance in managing your server, consider using a first-party Laravel server management and deployment service such as [Laravel Forge](https://forge.laravel.com).**

애플리케이션을 Nginx가 동작하는 서버에 배포하는 경우 다음과 같은 설정 파일을 웹서버 설정으로 사용할 수 있습니다. 대부분의 경우 이 파일은 서버 설정에 따라서 사용자가 수정할 필요가 있습니다. **서버 관리에 도움이 필요하다면, [라라벨 Forge](https://forge.laravel.com)와 같은 자사의 라라벨 서버 관리 및 배포 서비스를 사용하는 것이 고려해보세요.**

Please ensure, like the configuration below, your web server directs all requests to your application's `public/index.php` file. You should never attempt to move the `index.php` file to your project's root, as serving the application from the project root will expose many sensitive configuration files to the public Internet:

아래 구성과 같이 웹 서버가 모든 요청을 애플리케이션의 `public/index.php` 파일로 보내는지 확인해보세요. 프로젝트 루트에서 애플리케이션을 제공하면 많은 민감한 설정 파일이 공개 인터넷에 노출되므로 `index.php` 파일을 프로젝트 루트로 이동하려고 시도해서는 안 됩니다.

    server {
        listen 80;
        listen [::]:80;
        server_name example.com;
        root /srv/example.com/public;

        add_header X-Frame-Options "SAMEORIGIN";
        add_header X-Content-Type-Options "nosniff";

        index index.php;

        charset utf-8;

        location / {
            try_files $uri $uri/ /index.php?$query_string;
        }

        location = /favicon.ico { access_log off; log_not_found off; }
        location = /robots.txt  { access_log off; log_not_found off; }

        error_page 404 /index.php;

        location ~ \.php$ {
            fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
            fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
            include fastcgi_params;
        }

        location ~ /\.(?!well-known).* {
            deny all;
        }
    }

<a name="optimization"></a>
## Optimization
## 최적화

<a name="autoloader-optimization"></a>
### Autoloader Optimization
### Autoloader 최적화

When deploying to production, make sure that you are optimizing Composer's class autoloader map so Composer can quickly find the proper file to load for a given class:

실 서버에 배포할 때 컴포저의 클래스 오토로딩 맵을 최적화 하여 컴포저가 주어진 클래스 파일을 빠르게 찾을 수 있도록 하십시오.

    composer install --optimize-autoloader --no-dev

> {tip} In addition to optimizing the autoloader, you should always be sure to include a `composer.lock` file in your project's source control repository. Your project's dependencies can be installed much faster when a `composer.lock` file is present.

> {tip} 오토로더를 최적화 하는데 더하여, 프로젝트의 소스 컨트롤 저장소에 `composer.lock` 파일을 포함하고 있는지 확인하십시오. `composer.lock` 파일이 존재한다면 프로젝트의 의존성을 보다 빠르게 설치할 수 있습니다.

<a name="optimizing-configuration-loading"></a>
### Optimizing Configuration Loading
### 설정내역 로딩 최적화

When deploying your application to production, you should make sure that you run the `config:cache` Artisan command during your deployment process:

실 서버에 배포할 때에 배포 프로세스에 `config:cache` 아티즌 명령어를 실행하도록 하십시오.

    php artisan config:cache

This command will combine all of Laravel's configuration files into a single, cached file, which greatly reduces the number of trips the framework must make to the filesystem when loading your configuration values.

이 명령어는 라라벨의 전체 설정 파일을 하나로 합쳐서, 캐시 파일을 만들고, 프레임워크가 설정파일을 로딩하기 위해서 여러개의 파일을 읽는 것을 줄여줍니다.

> {note} If you execute the `config:cache` command during your deployment process, you should be sure that you are only calling the `env` function from within your configuration files. Once the configuration has been cached, the `.env` file will not be loaded and all calls to the `env` function for `.env` variables will return `null`.

> {note} 배포 과정에서 `config:cache` 명령을 실행하면 설정 파일 내에서 `env` 함수 만 호출해야합니다. 설정이 캐시되면 `.env` 파일이 로드되지 않고 `.env`의 변수에 대한 `env` 함수의 모든 호출은 `null`을 반환합니다.

<a name="optimizing-route-loading"></a>
### Optimizing Route Loading
### 라우트 로딩 최적화

If you are building a large application with many routes, you should make sure that you are running the `route:cache` Artisan command during your deployment process:

큰 애플리케이션과 같이 많은 라우트를 등록하는 경우에, 배포 프로세스에 `route:cache` 아티즌 명령어를 실행하도록 하십시오.

    php artisan route:cache

This command reduces all of your route registrations into a single method call within a cached file, improving the performance of route registration when registering hundreds of routes.

이 명령어는 라라벨의 전체 라우트 등록을 하나의 캐시 파일로 만들어 라우트를 등록하는 속도를 줄여줍니다.

<a name="optimizing-view-loading"></a>
### Optimizing View Loading
### 뷰 로딩 최적화

When deploying your application to production, you should make sure that you run the `view:cache` Artisan command during your deployment process:

실 서버에 배포할 때에, 배포 프로세스에 `view:cache` 아티즌 명령어를 실행하도록 하십시오.

    php artisan view:cache

This command precompiles all your Blade views so they are not compiled on demand, improving the performance of each request that returns a view.

이 명령은 모든 블레이드 뷰를 사전 컴파일하여 요청시 컴파일하지 않으며, 뷰를 리턴하는 요청들의 성능을 향상시킵니다.

<a name="debug-mode"></a>
## Debug Mode
## 디버그 모드

The debug option in your config/app.php configuration file determines how much information about an error is actually displayed to the user. By default, this option is set to respect the value of the APP_DEBUG environment variable, which is stored in your .env file.

config/app.php 설정 파일의 디버그 옵션에 따라 사용자에게 실제로 표시되는 오류 정보의 양이 결정됩니다. 기본적으로 이 옵션은 .env 파일에 저장된 APP_DEBUG 환경 변수의 값을 고려하도록 설정됩니다.

**In your production environment, this value should always be `false`. If the `APP_DEBUG` variable is set to `true` in production, you risk exposing sensitive configuration values to your application's end users.**

**프로덕션 환경에서 이 값은 항상 `false`여야 합니다. 프로덕션에서 `APP_DEBUG` 변수가 `true`로 설정되면 민감한 설정 값이 애플리케이션의 최종 사용자에게 노출될 위험이 있습니다.**

<a name="deploying-with-forge-or-vapor"></a>
## Deploying With Forge / Vapor
## Forge / Vapor로 배포하기

<a name="laravel-forge"></a>
#### Laravel Forge
#### Laravel Forge

If you aren't quite ready to manage your own server configuration or aren't comfortable configuring all of the various services needed to run a robust Laravel application, [Laravel Forge](https://forge.laravel.com) is a wonderful alternative.

여러분이 서버 구성을 잘 관리할 준비가 되어 있지 않거나, 라라벨 애플리케이션을 실행하는데 필요한 여러 서비스들을 모두 구성하는게 어렵다면, [라라벨 Forge](https://forge.laravel.com) 가 대안이 될 수 있습니다.

Laravel Forge can create servers on various infrastructure providers such as DigitalOcean, Linode, AWS, and more. In addition, Forge installs and manages all of the tools needed to build robust Laravel applications, such as Nginx, MySQL, Redis, Memcached, Beanstalk, and more.

라라벨 Forge 는 DigitalOcean, Linode, AWS 와 같은 다양한 인프라를 제공하는 서비스 위에서 서버를 구성할 수 있습니다. 또한 Forge는 Nginx, MySQL, Redis, Memcached, Beanstalk 와 같은 라라벨 애플리케이션을 구축하는데 필요한 모든 툴들을 설치하고 관리해줍니다.

<a name="laravel-vapor"></a>
#### Laravel Vapor
#### Laravel Vapor

If you would like a totally serverless, auto-scaling deployment platform tuned for Laravel, check out [Laravel Vapor](https://vapor.laravel.com). Laravel Vapor is a serverless deployment platform for Laravel, powered by AWS. Launch your Laravel infrastructure on Vapor and fall in love with the scalable simplicity of serverless. Laravel Vapor is fine-tuned by Laravel's creators to work seamlessly with the framework so you can keep writing your Laravel applications exactly like you're used to.

라라벨에 맞게 조정 된 완전한 서버리스 자동 확장 배포 플랫폼을 원한다면 [라라벨 Vapor](https://vapor.laravel.com)을 확인하세요. 라라벨 Vapor은 AWS 기반 라라벨 용 서버리스 배포 플랫폼입니다. Vapor에서 라라벨 인프라를 시작하고 서버리스의 확장 가능한 단순성에 빠져보세요. 라라벨 Vapor은 라라벨의 제작자가 프레임워크와 원활하게 작동하도록 미세 조정하므로 익숙한 방식으로 라라벨 애플리케이션을 계속 작성할 수 있습니다.