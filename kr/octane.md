# Laravel Octane
# Laravel Octane

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Installation](#installation)
- [설치하기](#installation)
- [Server Prerequisites](#server-prerequisites)
- [서버 전제 조건](#server-prerequisites)
    - [RoadRunner](#roadrunner)
    - [RoadRunner](#roadrunner)
    - [Swoole](#swoole)
    - [Swoole](#swoole)
- [Serving Your Application](#serving-your-application)
- [애플리케이션에 제공하기](#serving-your-application)
    - [Serving Your Application Via HTTPS](#serving-your-application-via-https)
    - [HTTPS을 이용해 애플리케이션 제공하기](#serving-your-application-via-https)
    - [Serving Your Application Via Nginx](#serving-your-application-via-nginx)
    - [Nginx를 이용한 애플리케이션 제공하기](#serving-your-application-via-nginx)
    - [Watching For File Changes](#watching-for-file-changes)
    - [파일 변경 사항 감지하기](#watching-for-file-changes)
    - [Specifying The Worker Count](#specifying-the-worker-count)
    - [워커 수 지정하기](#specifying-the-worker-count)
    - [Specifying The Max Request Count](#specifying-the-max-request-count)
    - [최대 요청 수 지정하기](#specifying-the-max-request-count)
    - [Reloading The Workers](#reloading-the-workers)
    - [Worker 재호출하기](#reloading-the-workers)
    - [Stopping The Server](#stopping-the-server)
    - [서버 중지하기](#stopping-the-server)
- [Dependency Injection & Octane](#dependency-injection-and-octane)
- [의존성 주입 & Octane](#dependency-injection-and-octane)
    - [Container Injection](#container-injection)
    - [Container 주입](#container-injection)
    - [Request Injection](#request-injection)
    - [Request 주입](#request-injection)
    - [Configuration Repository Injection](#configuration-repository-injection)
    - [설정 Repository 주입](#configuration-repository-injection)
- [Managing Memory Leaks](#managing-memory-leaks)
- [Memory 누수 관리하기](#managing-memory-leaks)
- [Concurrent Tasks](#concurrent-tasks)
- [동시성 Tasks](#concurrent-tasks)
- [Ticks & Intervals](#ticks-and-intervals)
- [Ticks & Intervals](#ticks-and-intervals)
- [The Octane Cache](#the-octane-cache)
- [Octane 캐시](#the-octane-cache)
- [Tables](#tables)
- [테이블](#tables)

<a name="introduction"></a>
## Introduction
## 시작하기

[Laravel Octane](https://github.com/laravel/octane) supercharges your application's performance by serving your application using high-powered application servers, including [Open Swoole](https://swoole.co.uk), [Swoole](https://github.com/swoole/swoole-src), and [RoadRunner](https://roadrunner.dev). Octane boots your application once, keeps it in memory, and then feeds it requests at supersonic speeds.

[Laravel Octane](https://github.com/laravel/octane) 은 [Open Swoole](https://swoole.co.uk), [Swoole](https://github.com/swoole/swoole-src), [RoadRunner](https://roadrunner.dev) 같은 고성능 application 서버를 사용해 애플리케이션의 성능을 향상 시킵니다. Octane은 애플리케이션이 한번 부팅된 후에 메모리에서 유지되는데, 이 요청들은 초고속으로 응답합니다.

<a name="installation"></a>
## Installation
## 설치하기

Octane may be installed via the Composer package manager:

Octane은 Composer 패키지 매니저를 통해 설치할 수 있습니다.


```bash
composer require laravel/octane
```

After installing Octane, you may execute the `octane:install` Artisan command, which will install Octane's configuration file into your application:

Octane을 설치하면, `octane:install` 이라는 Artisan 명령어를 통해 Octane의 설정파일을 애플리케이션에 간단하게 설치 할 수 있습니다. 

```bash
php artisan octane:install
```

<a name="server-prerequisites"></a>
## Server Prerequisites
## 서버 전제 조건

> {note} Laravel Octane requires [PHP 8.0+](https://php.net/releases/).

> {note} Laravel Octane [PHP 8.0+](https://php.net/releases/) 을 필요로 합니다.

<a name="roadrunner"></a>
### RoadRunner
### RoadRunner

[RoadRunner](https://roadrunner.dev) is powered by the RoadRunner binary, which is built using Go. The first time you start a RoadRunner based Octane server, Octane will offer to download and install the RoadRunner binary for you.

[RoadRunner](https://roadrunner.dev) Go로 구축된 RoadRunner binary로 구동됩니다. RoadRunner 베이스의 Octane server를 처음 시작하는 순간, Octane을 RoadRunner binary로 다운로드하고 설치할 것을 제안합니다. 

<a name="roadrunner-via-laravel-sail"></a>
#### RoadRunner Via Laravel Sail
#### Laravel Sail을 통한 RoadRunner

If you plan to develop your application using [Laravel Sail](/docs/{{version}}/sail), you should run the following commands to install Octane and RoadRunner:

만약 애플리케이션에서 [Laravel Sail](/docs/{{version}}/sail) 을 사용해 개발할 계획이라면,  Octane과 RoadRunner를 설치하려면 다음 명령을 실행해야 합니다.

```bash
./vendor/bin/sail up

./vendor/bin/sail composer require laravel/octane spiral/roadrunner
```

Next, you should start a Sail shell and use the `rr` executable to retrieve the latest Linux based build of the RoadRunner binary:

그 다음, Sail shell을 시작하고, `rr` 실행 파일을 사용하여 RoadRunner binary의 최신 Linux 기반으로 빌드된 것을 가져와야 합니다.

```bash
./vendor/bin/sail shell

# Within the Sail shell...
./vendor/bin/rr get-binary
```

After installing the RoadRunner binary, you may exit your Sail shell session. You will now need to adjust the `supervisor.conf` file used by Sail to keep your application running. To get started, execute the `sail:publish` Artisan command:

RoadRunner binary를 설치한 후에, Sail shell 세션을 끝낼수 있습니다. 이제 Sail에서 사용하는 `supervisor.conf` 파일을 조정하여 애플리케이션을 계속 실행해야 합니다. 시작하기 위해선는 `sail:publish` Artisan 명령을 실행합니다.

```bash
./vendor/bin/sail artisan sail:publish
```

Next, update the `command` directive of your application's `docker/supervisord.conf` file so that Sail serves your application using Octane instead of the PHP development server:

그 다음, 애플리케이션의 `docker/supervisord.conf` 파일의 `command`를 업데이트하여 Sail이 PHP 개발 서버 대신 Octane을 사용하여 애플리케이션이 제공되도록 합니다.

```ini
command=/usr/bin/php -d variables_order=EGPCS /var/www/html/artisan octane:start --server=roadrunner --host=0.0.0.0 --rpc-port=6001 --port=8000
```

Finally, ensure the `rr` binary is executable and build your Sail images:

마지막으로, `rr` binary가 실행 가능한지 확인하고 Sail 이미지를 빌드합니다.

```bash
chmod +x ./rr

./vendor/bin/sail build --no-cache
```

<a name="swoole"></a>
### Swoole
### Swoole

If you plan to use the Swoole application server to serve your Laravel Octane application, you must install the Swoole PHP extension. Typically, this can be done via PECL:

만약 Swoole application 서버를 사용해 Laravel Octane 애플리케이션을 제공할 예정이라면, 반드시 Swoole PHP extension을 설치해야 합니다. 일반적으로, PECL을 이용할 수 있습니다.  

```bash
pecl install swoole
```

<a name="swoole-via-laravel-sail"></a>
#### Swoole Via Laravel Sail
#### Laravel Sail을 통한 Swoole

> {note} Before serving an Octane application via Sail, ensure you have the latest version of Laravel Sail and execute `./vendor/bin/sail build --no-cache` within your application's root directory.

> {note} Octane 애플리케이션을 Sail로 제공하기 전에, Laravel Sail의 가장 최신버전을 확인하기 위해 애플리케이션의 root 디렉토리에서 `./vendor/bin/sail build --no-cache` 를 실행해야 합니다. 

Alternatively, you may develop your Swoole based Octane application using [Laravel Sail](/docs/{{version}}/sail), the official Docker based development environment for Laravel. Laravel Sail includes the Swoole extension by default. However, you will still need to adjust the `supervisor.conf` file used by Sail to keep your application running. To get started, execute the `sail:publish` Artisan command:

다른 대안으로, Swoole 베이스의 Octane 애플리케이션을 개발할때 라라벨을 위한 공식 Docker 기반 개발 환경인 [Laravel Sail](/docs/{{version}}/sail)을 사용할수 있습니다. Laravel Sail은 Swoole 확장모듈을 기본으로 포함하고 있습니다. 그러나 애플리케이션을 계속 실행하려면 Sail에서 사용하는 `supervisor.conf` 파일을 조정해야 합니다. 시작하기 위해 `sail:publish` Artisan 명령을 실행합니다.

```bash
./vendor/bin/sail artisan sail:publish
```

Next, update the `command` directive of your application's `docker/supervisord.conf` file so that Sail serves your application using Octane instead of the PHP development server:

그 다음, 애플리케이션의 `docker/supervisord.conf` 파일의 `command`를 업데이트하여 Sail이 PHP 개발 서버 대신 Octane을 사용하여 애플리케이션이 제공되도록 합니다.

```ini
command=/usr/bin/php -d variables_order=EGPCS /var/www/html/artisan octane:start --server=swoole --host=0.0.0.0 --port=80
```

Finally, build your Sail images:

마지막으로 Sail 이미지를 빌드합니다.

```bash
./vendor/bin/sail build --no-cache
```

<a name="swoole-configuration"></a>
#### Swoole Configuration
#### Swoole 설정

Swoole supports a few additional configuration options that you may add to your `octane` configuration file if necessary. Because they rarely need to be modified, these options are not included in the default configuration file:

Swoole은 `octane` 설정 파일에 필요한 것을 추가할 수 있도록 몇 가지 추가 옵션을 지원합니다. 수정 할 필요가 거의 없기 때문에 다음 옵션은 기본 설정 파일에 포함되지 않습니다.

```php
'swoole' => [
    'options' => [
        'log_file' => storage_path('logs/swoole_http.log'),
        'package_max_length' => 10 * 1024 * 1024,
    ],
];
```

<a name="serving-your-application"></a>
## Serving Your Application
## 애플리케이션에 제공하기

The Octane server can be started via the `octane:start` Artisan command. By default, this command will utilize the server specified by the `server` configuration option of your application's `octane` configuration file:

Octane 서버는 `octane:start`라는 Artisan 명령어로 시작할 수 있습니다. 기본적으로 이 명령어는 애플리케이션의 `octane` 설정파일에서 옵션으로 지정한 `server` 설정을 활용합니다.


```bash
php artisan octane:start
```

By default, Octane will start the server on port 8000, so you may access your application in a web browser via `http://localhost:8000`.

기본적으로, Octane 서버는 8000 포트로 시작할 수 있고, 웹 브라우저는 `http://localhost:8000`를 통해서 애플리케이션에 접속 할 수 있습니다. 

<a name="serving-your-application-via-https"></a>
### Serving Your Application Via HTTPS
### HTTPS을 이용해 애플리케이션 제공하기

By default, applications running via Octane generate links prefixed with `http://`. The `OCTANE_HTTPS` environment variable, used within your application's `config/octane.php` configuration file, can be set to `true` when serving your application via HTTPS. When this configuration value is set to `true`, Octane will instruct Laravel to prefix all generated links with `https://`:

기본적으로, Octane을 통해 실행되는 애플리케이션은 `http://` 접두사가 붙은 링크를 생성합니다.  `OCTANE_HTTPS` 환경변수는 `config/octane.php`라는 설정 파일을 통해 사용하고, 애플리케이션을 HTTPS으로 제공하고자 할때 `true`로 세팅할 수 있습니다. 이 설정값을 `true`로 세팅할 때, Octane은 생성된 모든 링크에 `https://`를 접두사로 붙이도록 라라벨에 지시합니다.

```php
'https' => env('OCTANE_HTTPS', false),
```

<a name="serving-your-application-via-nginx"></a>
### Serving Your Application Via Nginx
### Nginx를 이용한 애플리케이션 제공하기

> {tip} If you aren't quite ready to manage your own server configuration or aren't comfortable configuring all of the various services needed to run a robust Laravel Octane application, check out [Laravel Forge](https://forge.laravel.com).

> {tip} 자체 서버 설정을 관리할 수 없거나 강력한 Laravel Octane 애플리케이션을 실행하기 위해 필요로 하는 서비스를 구성하기 어렵다면 [Laravel Forge](https://forge.laravel.com)를 체크하세요. 

In production environments, you should serve your Octane application behind a traditional web server such as a Nginx or Apache. Doing so will allow the web server to serve your static assets such as images and stylesheets, as well as manage your SSL certificate termination.

Octane 애플리케이션은 production 환경에서 Nginx나 Apache 같은 기존 웹서버 뒤에서 제공되어야 합니다. 그렇게 하면 웹 서버가 이미지나 스타일시트같은 static 자산을 제공하고 SSL 인증서 종료도 관리할 수 있습니다.

In the Nginx configuration example below, Nginx will serve the site's static assets and proxy requests to the Octane server that is running on port 8000:

아래는 Nginx 설정의 예로, Nginx가 포트 8000에서 실행되는 Octane 서버에 대한 사이트의 정적파일 및 프록시 요청을 제공합니다. 

```conf
map $http_upgrade $connection_upgrade {
    default upgrade;
    ''      close;
}

server {
    listen 80;
    listen [::]:80;
    server_name domain.com;
    server_tokens off;
    root /home/forge/domain.com/public;

    index index.php;

    charset utf-8;

    location /index.php {
        try_files /not_exists @octane;
    }

    location / {
        try_files $uri $uri/ @octane;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    access_log off;
    error_log  /var/log/nginx/domain.com-error.log error;

    error_page 404 /index.php;

    location @octane {
        set $suffix "";

        if ($uri = /index.php) {
            set $suffix ?$query_string;
        }

        proxy_http_version 1.1;
        proxy_set_header Host $http_host;
        proxy_set_header Scheme $scheme;
        proxy_set_header SERVER_PORT $server_port;
        proxy_set_header REMOTE_ADDR $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;

        proxy_pass http://127.0.0.1:8000$suffix;
    }
}
```

<a name="watching-for-file-changes"></a>
### Watching For File Changes
### 파일 변경 사항을 감지하기 

Since your application is loaded in memory once when the Octane server starts, any changes to your application's files will not be reflected when you refresh your browser. For example, route definitions added to your `routes/web.php` file will not be reflected until the server is restarted. For convenience, you may use the `--watch` flag to instruct Octane to automatically restart the server on any file changes within your application:

Octane 서버가 시작 될 때 메모리에 적재해 둔 것을 불러오기 때문에, 웹사이트를 새로고침 할 때 변경된 애플리케이션 파일의 그 어떤 것도 반영해 주지 않습니다. 예를 들어, `routes/web.php`에 route 정의가 추가 되더라도 서버를 재시작하기 전까지는 반영되지 않습니다. 편의상 , `--watch`라는 플래그를 통해 애플리케이션의 파일이 변경될때 마다 Octane 서버가 자동으로 재시작하도록 명령 할 수 있습니다. 

```bash
php artisan octane:start --watch
```

Before using this feature, you should ensure that [Node](https://nodejs.org) is installed within your local development environment. In addition, you should install the [Chokidar](https://github.com/paulmillr/chokidar) file-watching library within your project:library:

이런 기능을 사용하기 전에, [Node](https://nodejs.org)를 local 개발 환경에 설치해 두어야 합니다. 또, 파일 감시를 위해 [Chokidar](https://github.com/paulmillr/chokidar) 를 project:library:내에 설치해야 합니다.

```bash
npm install --save-dev chokidar
```

You may configure the directories and files that should be watched using the `watch` configuration option within your application's `config/octane.php` configuration file.

애플리케이션의 `config/octane.php` 설정파일 내에서 `watch` 옵션을 사용해 감시해야 하는 디렉토리와 파일을 설정 할 수 있습니다.

<a name="specifying-the-worker-count"></a>
### Specifying The Worker Count
### 워커 수 지정하기

By default, Octane will start an application request worker for each CPU core provided by your machine. These workers will then be used to serve incoming HTTP requests as they enter your application. You may manually specify how many workers you would like to start using the `--workers` option when invoking the `octane:start` command:

기본적으로 Octane은 애플리케이션의 요청을 처리하는 worker를 기기의 CPU 코어 수 만큼 할당 해서 시작할 수 있습니다. 이 worker들은 HTTP 요청이 애플리케이션으로 들어오는 서비스를 수행할 때 사용되어 집니다. `octane:start` 명령을 호출할 때 `--workers` 옵션으로 시작할 작업자 수를 수동으로 지정할 수 있습니다. 

```bash
php artisan octane:start --workers=4
```

If you are using the Swoole application server, you may also specify how many ["task workers"](#concurrent-tasks) you wish to start:

만약 Swoole 애플리케이션 서버를 사용합니다면, 사용하고자 하는 ["task workers"](#concurrent-tasks)수를 얼마나 사용할 지 지정할 수 있습니다. 

```bash
php artisan octane:start --workers=4 --task-workers=6
```

<a name="specifying-the-max-request-count"></a>
### Specifying The Max Request Count
### 최대 요청 수 지정하기

To help prevent stray memory leaks, Octane can gracefully restart a worker once it has handled a given number of requests. To instruct Octane to do this, you may use the `--max-requests` option:

stray memory 누수를 방지하기 위해서는, Octane은 주어진 몇몇의 요청을 수행하면서도 우아하게 worker를 재시작 할 수도 있습니다. `--max-requests` 옵션을 사용해 Octane이 이런 동작을 처리하도록 지시 할 수 있습니다. 

```bash
php artisan octane:start --max-requests=250
```

<a name="reloading-the-workers"></a>
### Reloading The Workers
### Worker 재호출하기

You may gracefully restart the Octane server's application workers using the `octane:reload` command. Typically, this should be done after deployment so that your newly deployed code is loaded into memory and is used to serve to subsequent requests:

Octane 서버의 애플리케이션 worker를 우아하게 재시작하기 위해서는 `octane:reload`이라는 명령을 사용할 수 있습니다. 일반적으로, 개발 후 새로운 코드가 배포되고 메모리 안에 적재된 후에 후속 요청들이 사용할 수 있도록, 배포 이후에 수행되야 합니다. 

```bash
php artisan octane:reload
```

<a name="stopping-the-server"></a>
### Stopping The Server
### 서버 중지하기

You may stop the Octane server using the `octane:stop` Artisan command:

`octane:stop` 라는 Artisan 명령어를 사용하면 Octane 서버를 중지할 수 있습니다.

```bash
php artisan octane:stop
```

<a name="checking-the-server-status"></a>
#### Checking The Server Status
#### 서버 상태 확인하기

You may check the current status of the Octane server using the `octane:status` Artisan command:

`octane:stop` 라는 Artisan 명령어를 사용하면 Octane 서버의 현재 상태를 체크할 수 있습니다. 

```bash
php artisan octane:status
```

<a name="dependency-injection-and-octane"></a>
## Dependency Injection & Octane
## 의존성 주입 & Octane

Since Octane boots your application once and keeps it in memory while serving requests, there are a few caveats you should consider while building your application. For example, the `register` and `boot` methods of your application's service providers will only be executed once when the request worker initially boots. On subsequent requests, the same application instance will be reused.

Octane은 애플리케이션이 부팅하고 메모리에 적재하는 동안에도 request를 처리하기 때문에, 애플리케이션을 빌드 할 때도 몇가지 고려사항을 숙지해야 합니다. 예를 들면, 애플리케이션 서비스 제공자의 `register` 와 `boot` 메서드는 오직 request worker가 처음 한번 부팅될 때 수행되어 집니다. 후속 요청에서 동일한 애플리케이션 instance는 재사용 되어 집니다. 

In light of this, you should take special care when injecting the application service container or request into any object's constructor. By doing so, that object may have a  stale version of the container or request on subsequent requests.

이를 고려하여 애플리케이션 서비스 컨테이너 또는 요청은 개체의 생성자에 삽입할 때 특별한 주의를 기울여야 합니다. 이렇게 하면 해당 객체는 오래된 버전의 컨테이너나 후속 요청에 대한 요청을 할 수 있습니다. 

Octane will automatically handle resetting any first-party framework state between requests. However, Octane does not always know how to reset the global state created by your application. Therefore, you should be aware of how to build your application in a way that is Octane friendly. Below, we will discuss the most common situations that may cause problems while using Octane.

Octane은 자동적으로 모든 first-party framework의 상태와 요청사이에서 재설정을 할 수 있습니다. 하지만, Octane은 언제나 애플리케이션이 설정한 전역 상태를 재설정하는 방법을 항상 알지는 못합니다. 그러므로 Octane에 친화적인 방식으로 애플리케이션을 빌드하는 방법을 알고 있어야 합니다.

<a name="container-injection"></a>
### Container Injection
### Container 주입

In general, you should avoid injecting the application service container or HTTP request instance into the constructors of other objects. For example, the following binding injects the entire application service container into an object that is bound as a singleton:

일반적으로, 애플리케이션 서비스 컨테이너나 HTTP request 인스턴스를 다른 객체의 생성자 안에 주입하는 것은 피해야 합니다. 예를 들면, 다음 바인딩은 전체 애플리케이션 서비스 컨테이너가 싱글톤에 묶인 객체안에 주입됩니다. 

```php
use App\Service;

/**
 * Register any application services.
 *
 * @return void
 */
public function register()
{
    $this->app->singleton(Service::class, function ($app) {
        return new Service($app);
    });
}
```

In this example, if the `Service` instance is resolved during the application boot process, the container will be injected into the service and that same container will be held by the `Service` instance on subsequent requests. This **may** not be a problem for your particular application; however, it can lead to the container unexpectedly missing bindings that were added later in the boot cycle or by a subsequent request.

이런 예제는 애플리케이션 boot process 중에 `Service` 인스턴스가 해결되면 컨테이너가 서비스에 주입되고, 동일한 컨테이너가 후속 요청에서 `Service` 인스턴스에 의해 유지됩니다. 이것은 **아마도** 특정 프로세스에서는 큰 문제가 되지 않을 수 있습니다. 하지만, 이로 인해 나중에 부팅 주기나 후속 요청에 의해 추가된 바인딩이 컨테이너에 예상치 않게 누락될 수 있습니다.

As a work-around, you could either stop registering the binding as a singleton, or you could inject a container resolver closure into the service that always resolves the current container instance:

이 문제를 해결하려면 바인딩을 싱글톤으로 등록하는 것을 중지하거나 현재 컨테이너 인스턴스를 항상 확인하는 서비스에 resolver closure로 주입할 수 있습니다.

```php
use App\Service;
use Illuminate\Container\Container;

$this->app->bind(Service::class, function ($app) {
    return new Service($app);
});

$this->app->singleton(Service::class, function () {
    return new Service(fn () => Container::getInstance());
});
```

The global `app` helper and the `Container::getInstance()` method will always return the latest version of the application container.

전역 `app` 헬퍼와 `Container::getInstance()` 메서드는 언제나 application 컨테이너의 가장 최신 버전으로 반환됩니다. 

<a name="request-injection"></a>
### Request Injection
### Request 주입

In general, you should avoid injecting the application service container or HTTP request instance into the constructors of other objects. For example, the following binding injects the entire request instance into an object that is bound as a singleton:

일반적으로, 애플리케이션 서비스 컨테이너나 HTTP request 인스턴스를 다른 객체의 생성자 안에 주입하는 것은 피해야 합니다. 예를 들면, 다음 바인딩은 전체 요청 인스턴스를 싱글톤으로 바인딩된 객체에 주입합니다.

```php
use App\Service;

/**
 * Register any application services.
 *
 * @return void
 */
public function register()
{
    $this->app->singleton(Service::class, function ($app) {
        return new Service($app['request']);
    });
}
```

In this example, if the `Service` instance is resolved during the application boot process, the HTTP request will be injected into the service and that same request will be held by the `Service` instance on subsequent requests. Therefore, all headers, input, and query string data will be incorrect, as well as all other request data.

이 예제에서는, 만약 `Service`인스턴스가 애플리케이션 boot process 중에 해결된다면, HTTP 요청은 서비스에 주입되고 동일한 요청은 후속 요청에서 `Service` 인스턴스에 의해 유지됩니다. 그러므로, 모든 헤더와, input, 그리고 쿼리 스트링 data, 심지어는 다른 request data 마저도 올바르지 않습니다.

As a work-around, you could either stop registering the binding as a singleton, or you could inject a request resolver closure into the service that always resolves the current request instance. Or, the most recommended approach is simply to pass the specific request information your object needs to one of the object's methods at runtime:

이 문제를 해결하려면 바인딩을 싱글톤으로 등록하는 것을 중지하거나 현재 요청 인스턴스를 항상 확인하는 서비스에 요청 resolver closure로 삽입할 수 있습니다. 또는 가장 권유하는 접근 방식은 단순히 런타임에 개체에 필요한 특정 요청 정보를 개체의 메서드 중 하나로 전달하는 것입니다.


```php
use App\Service;

$this->app->bind(Service::class, function ($app) {
    return new Service($app['request']);
});

$this->app->singleton(Service::class, function ($app) {
    return new Service(fn () => $app['request']);
});

// Or...

$service->method($request->input('name'));
```

The global `request` helper will always return the request the application is currently handling and is therefore safe to use within your application.

전역 `request` 헬퍼는 항상 애플리케이션이 현재 처리하고 있는 요청을 반환하므로 애플리케이션 내에서 안전하게 사용할 수 있습니다.

> {note} It is acceptable to type-hint the `Illuminate\Http\Request` instance on your controller methods and route closures.

> {note} 컨트롤러의 메서드와 route의 클로저에서 `Illuminate\Http\Request` 인스턴스를 타입힌트로 사용하는 것은 허용 됩니다. 

<a name="configuration-repository-injection"></a>
### Configuration Repository Injection
### 설정 Repository 주입

In general, you should avoid injecting the configuration repository instance into the constructors of other objects. For example, the following binding injects the configuration repository into an object that is bound as a singleton:

일반적으로,설정 repository instance를 다른 객체의 생성자로 주입하는 것은 피해야 합니다. 예를 들면, 다음 바인딩은 전체 설정 repository를 싱글톤으로 바인딩된 객체에 주입합니다.

```php
use App\Service;

/**
 * Register any application services.
 *
 * @return void
 */
public function register()
{
    $this->app->singleton(Service::class, function ($app) {
        return new Service($app->make('config'));
    });
}
```

In this example, if the configuration values change between requests, that service will not have access to the new values because it's depending on the original repository instance.

이 예제에서, 만약 설정 값이 요청들 사이에서 변경되면, 서비스는 원래 repository 인스턴스에 의존하기 때문에 새로운 값에 접근 할수 없습니다.

As a work-around, you could either stop registering the binding as a singleton, or you could inject a configuration repository resolver closure to the class:

이 문제를 해결하는 방법으로는, 싱글톤에 바인딩을 등록하는 것을 중지하거나, 설정 repository를 클래스에 resolver closure를 할 수 있습니다. 

```php
use App\Service;
use Illuminate\Container\Container;

$this->app->bind(Service::class, function ($app) {
    return new Service($app->make('config'));
});

$this->app->singleton(Service::class, function () {
    return new Service(fn () => Container::getInstance()->make('config'));
});
```

The global `config` will always return the latest version of the configuration repository and is therefore safe to use within your application.

전역 `config`는 항상 가장 최신의 설정 repository 버전을 반환하기 때문에 애플리케이션 내에서 안전하게 사용할 수 있습니다.

<a name="managing-memory-leaks"></a>
### Managing Memory Leaks
### Memory 누수 관리하기

Remember, Octane keeps your application in memory between requests; therefore, adding data to a statically maintained array will result in a memory leak. For example, the following controller has a memory leak since each request to the application will continue to add data to the static `$data` array:

기억해야 하는 것은, Octane은 요청들 중간에도 애플리케이션이 메모리에 항상 유지됩니다. 그러므로, 정적으로 유지되는 배열에 데이터를 추가하는 것은 메모리 누수를 초래할 수 있습니다. 예를 들면, 다음 컨트롤러에는 애플리케이션에 각 요청이 정적 `$data` 배열에 데이터를 계속 추가하기 때문에 메모리 누수가 발생합니다.

```php
use App\Service;
use Illuminate\Http\Request;
use Illuminate\Support\Str;

/**
 * Handle an incoming request.
 *
 * @param  \Illuminate\Http\Request  $request
 * @return void
 */
public function index(Request $request)
{
    Service::$data[] = Str::random(10);

    // ...
}
```

While building your application, you should take special care to avoid creating these types of memory leaks. It is recommended that you monitor your application's memory usage during local development to ensure you are not introducing new memory leaks into your application.

애플리케이션을 빌드하는 동안에, 특별한 주의를 통해 이런 타입의 메모리 누수가 발생하지 않도록 해야 합니다. 애플리케이션에서 새로운 메모리 누수가 발생하지 않도록 로컬 개발 중에 애플리케이션의 메모리 사용량을 모니터링하는 것이 좋습니다.

<a name="concurrent-tasks"></a>
## Concurrent Tasks
## 동시성 Tasks

> {note} This feature requires [Swoole](#swoole).

> {note} 이 기능은 [Swoole](#swoole)이 필요합니다. 

When using Swoole, you may execute operations concurrently via light-weight background tasks. You may accomplish this using Octane's `concurrently` method. You may combine this method with PHP array destructuring to retrieve the results of each operation:

Swoole 사용할 때, 가벼운 백그라운드 작업을 통해 동시에 작업을 실행할 수 있습니다. Octane의 `concurrently` 메서드를 사용해 수행할 수도 있습니다. 이 방법은 PHP 배열 구조 분해하고 결합하여 각 작업의 결과를 검색할 수 있습니다.

```php
use App\User;
use App\Server;
use Laravel\Octane\Facades\Octane;

[$users, $servers] = Octane::concurrently([
    fn () => User::all(),
    fn () => Server::all(),
]);
```

Concurrent tasks processed by Octane utilize Swoole's "task workers", and execute within an entirely different process than the incoming request. The amount of workers available to process concurrent tasks is determined by the `--task-workers` directive on the `octane:start` command:

Octane은 Swoole의 "task workers"를 통해 동시적으로 task들을 수행하고, 들어오는 요청과 전혀 다른 프로세스로 수행합니다. 동시 작업을 처리하는 데 사용할 수 있는 작업자의 수는 `octane:start` 명령어를 사용할 때 `--task-workers` 지시문에 의해 결정됩니다.

```bash
php artisan octane:start --workers=4 --task-workers=6
```

<a name="ticks-and-intervals"></a>
## Ticks & Intervals
## Ticks & Intervals

> {note} This feature requires [Swoole](#swoole).

> {note} 이 기능은 [Swoole](#swoole)이 필요합니다.

When using Swoole, you may register "tick" operations that will be executed every specified number of seconds. You may register "tick" callbacks via the `tick` method. The first argument provided to the `tick` method should be a string that represents the name of the ticker. The second argument should be a callable that will be invoked at the specified interval.

Swoole을 사용해, "tick" 지정된 시간(초)마다 실행될 작업을 등록할 수 있습니다. "tick" 콜백은 `tick`메서드를 통해서 등록할수 있습니다. 첫번째 인자는 `tick`메서드를 대표하는 ticker의 이름을 문자열을 받습니다. 두번째 인자는 지정된 간격으로 호출되는 callable 입니다. 

In this example, we will register a closure to be invoked every 10 seconds. Typically, the `tick` method should be called within the `boot` method of one of your application's service providers:

예제에서는, 10초 간격으로 호출되는 클로저를 등록했습니다. 일반적으로 `tick`메서드는 애플리케이션의 서비스 제공자 중 하나로 `boot`메서드와 함께 호출됩니다.

```php
Octane::tick('simple-ticker', fn () => ray('Ticking...'))
        ->seconds(10);
```

Using the `immediate` method, you may instruct Octane to immediately invoke the tick callback when the Octane server initially boots, and every N seconds thereafter:

`immediate` 메서드를 사용할때, Octane 서버가 초기화 할때 이후부터는 Octane이 즉시 tick 콜백을 호출하도록 명령을 합니다.

```php
Octane::tick('simple-ticker', fn () => ray('Ticking...'))
        ->seconds(10)
        ->immediate();
```

<a name="the-octane-cache"></a>
## The Octane Cache
## Octane 캐시

> {note} This feature requires [Swoole](#swoole).

> {note} 이 기능은 [Swoole](#swoole)이 필요합니다.

When using Swoole, you may leverage the Octane cache driver, which provides read and write speeds of up to 2 million operations per second. Therefore, this cache driver is an excellent choice for applications that need extreme read / write speeds from their caching layer.

Swoole을 사용할 때, 초당 최대 200만 작업의 읽기 및 쓰기 속도 제공하는 Octane 캐시 드라이버 활용할 수 있습니다. 그러므로, 캐시 드라이버는 caching layer를 이용해 extream한 읽기/쓰기 속도가 필요한 애플리케이션에서 위한 최고의 선택입니다. 

This cache driver is powered by [Swoole tables](https://www.swoole.co.uk/docs/modules/swoole-table). All data stored in the cache is available to all workers on the server. However, the cached data will be flushed when the server is restarted:

캐시 드라이버는 [Swoole tables](https://www.swoole.co.uk/docs/modules/swoole-table) 로 구동됩니다. 캐시에 저장된 모든 데이터는 서버의 모든 작업자가 사용할 수 있습니다. 하지만, 캐시 데이터는 서버가 재시작 될 때 날아가버립니다. 

```php
Cache::store('octane')->put('framework', 'Laravel', 30);
```

> {tip} The maximum number of entries allowed in the Octane cache may be defined in your application's `octane` configuration file.

> {tip} 애플리케이션의 `octane` 설정 파일에서 Octane 캐시에 허용할 최대 항목의 수를 정의할 수 있습니다. 
 
<a name="cache-intervals"></a>
### Cache Intervals
### 주기적으로 캐시하기

In addition to the typical methods provided by Laravel's cache system, the Octane cache driver features interval based caches. These caches are automatically refreshed at the specified interval and should be registered within the `boot` method of one of your application's service providers. For example, the following cache will be refreshed every five seconds:

라라벨의 캐시 시스템으로 제공되는 일반적인 방법 말고도 Octane 캐시 드라이버는 interval 기반의 캐시를 제공합니다. 이 캐시들은 지정된 간격에 자동적으로 새로고침됩니다. 그리고 애플리케이션의 서비스 제공자 중 하나인 `boot` 메소드 내에 등록해야 합니다. 예를 들면, 다음 캐시는 5초마다 새로 고쳐집니다.

```php
use Illuminate\Support\Str;

Cache::store('octane')->interval('random', function () {
    return Str::random(10);
}, seconds: 5)
```

<a name="tables"></a>
## Tables
## Tables

> {note} This feature requires [Swoole](#swoole).

> {note} 이 기능은 [Swoole](#swoole)이 필요합니다.

When using Swoole, you may define and interact with your own arbitrary [Swoole tables](https://www.swoole.co.uk/docs/modules/swoole-table). Swoole tables provide extreme performance throughput and the data in these tables can be accessed by all workers on the server. However, the data within them will be lost when the server is restarted.

Swoole을 사용할때, 임의의 [Swoole 테이블]을 정의하고 상호 작용할 수 있습니다. Swoole 테이블은 extream한 성능 처리량을 제공하며, 이 테이블의 데이터는 서버의 모든 작업자가 접근할 수 있습니다. 하지만, 서버가 재시작되면 데이터가 사라지게 됩니다. 

Tables should be defined within the `tables` configuration array of your application's `octane` configuration file. An example table that allows a maximum of 1000 rows is already configured for you. The maximum size of string columns may be configured by specifying the column size after the column type as seen below:

테이블은 애플리케이션의 `octane` 설정 파일에서 `tables`설정 배열을 사용해 수정 될 수 있습니다. 예제 테이블은 최대 1000개의 row로 이미 준비되어 있습니다. 문자열 row의 최대 크기는 아래와 같이 row 유형 뒤에 row 크기를 지정하여 설정할 수 있습니다.

```php
'tables' => [
    'example:1000' => [
        'name' => 'string:1000',
        'votes' => 'int',
    ],
],
```

To access a table, you may use the `Octane::table` method:

`Octane::table` 메서드를 사용하면 테이블에 접근 할 수 있습니다. 

```php
use Laravel\Octane\Facades\Octane;

Octane::table('example')->set('uuid', [
    'name' => 'Nuno Maduro',
    'votes' => 1000,
]);

return Octane::table('example')->get('uuid');
```

> {note} The column types supported by Swoole tables are: `string`, `int`, and `float`.

> {note} 컬럼 타입은 Swoole 테이블에서 `string`, `int`, `float`을 제공합니다. 