# Laravel Octane

- [시작하기](#introduction)
- [설치하기](#installation)
- [서버 전제 조건](#server-prerequisites)
    - [RoadRunner](#roadrunner)
    - [Swoole](#swoole)
- [애플리케이션에 제공하기](#serving-your-application)
    - [HTTPS을 이용해 애플리케이션 제공하기](#serving-your-application-via-https)
    - [Nginx를 이용한 애플리케이션 제공하기](#serving-your-application-via-nginx)
    - [파일 변경 사항 감지하기](#watching-for-file-changes)
    - [워커 수 지정하기](#specifying-the-worker-count)
    - [최대 요청 수 지정하기](#specifying-the-max-request-count)
    - [Worker 재호출하기](#reloading-the-workers)
    - [서버 중지하기](#stopping-the-server)
- [의존성 주입 & Octane](#dependency-injection-and-octane)
    - [Container 주입](#container-injection)
    - [Request 주입](#request-injection)
    - [설정 Repository 주입](#configuration-repository-injection)
- [Memory 누수 관리하기](#managing-memory-leaks)
- [동시성 Tasks](#concurrent-tasks)
- [Ticks & Intervals](#ticks-and-intervals)
- [Octane 캐시](#the-octane-cache)
- [테이블](#tables)

<a name="introduction"></a>
## 시작하기

[Laravel Octane](https://github.com/laravel/octane) 은 [Open Swoole](https://swoole.co.uk), [Swoole](https://github.com/swoole/swoole-src), [RoadRunner](https://roadrunner.dev) 같은 고성능 application 서버를 사용해 애플리케이션의 성능을 향상 시킵니다. Octane은 애플리케이션이 한번 부팅된 후에 메모리에서 유지되는데, 이 요청들은 초고속으로 응답합니다.

<a name="installation"></a>
## 설치하기

Octane은 Composer 패키지 매니저를 통해 설치할 수 있습니다.


```bash
composer require laravel/octane
```

Octane을 설치하면, `octane:install` 이라는 Artisan 명령어를 통해 Octane의 설정파일을 애플리케이션에 간단하게 설치 할 수 있습니다. 

```bash
php artisan octane:install
```

<a name="server-prerequisites"></a>
## 서버 전제 조건

> {note} Laravel Octane [PHP 8.0+](https://php.net/releases/) 을 필요로 합니다.

<a name="roadrunner"></a>
### RoadRunner

[RoadRunner](https://roadrunner.dev) Go로 구축된 RoadRunner binary로 구동됩니다. RoadRunner 베이스의 Octane server를 처음 시작하는 순간, Octane을 RoadRunner binary로 다운로드하고 설치할 것을 제안합니다. 

<a name="roadrunner-via-laravel-sail"></a>
#### Laravel Sail을 통한 RoadRunner

만약 애플리케이션에서 [Laravel Sail](/docs/{{version}}/sail) 을 사용해 개발할 계획이라면,  Octane과 RoadRunner를 설치하려면 다음 명령을 실행해야 합니다.

```bash
./vendor/bin/sail up

./vendor/bin/sail composer require laravel/octane spiral/roadrunner
```

그 다음, Sail shell을 시작하고, `rr` 실행 파일을 사용하여 RoadRunner binary의 최신 Linux 기반으로 빌드된 것을 가져와야 합니다.

```bash
./vendor/bin/sail shell

# Within the Sail shell...
./vendor/bin/rr get-binary
```

RoadRunner binary를 설치한 후에, Sail shell 세션을 끝낼수 있습니다. 이제 Sail에서 사용하는 `supervisor.conf` 파일을 조정하여 애플리케이션을 계속 실행해야 합니다. 시작하기 위해선는 `sail:publish` Artisan 명령을 실행합니다.

```bash
./vendor/bin/sail artisan sail:publish
```

그 다음, 애플리케이션의 `docker/supervisord.conf` 파일의 `command`를 업데이트하여 Sail이 PHP 개발 서버 대신 Octane을 사용하여 애플리케이션이 제공되도록 합니다.

```ini
command=/usr/bin/php -d variables_order=EGPCS /var/www/html/artisan octane:start --server=roadrunner --host=0.0.0.0 --rpc-port=6001 --port=8000
```

마지막으로, `rr` binary가 실행 가능한지 확인하고 Sail 이미지를 빌드합니다.

```bash
chmod +x ./rr

./vendor/bin/sail build --no-cache
```

<a name="swoole"></a>
### Swoole

만약 Swoole application 서버를 사용해 Laravel Octane 애플리케이션을 제공할 예정이라면, 반드시 Swoole PHP extension을 설치해야 합니다. 일반적으로, PECL을 이용할 수 있습니다.  

```bash
pecl install swoole
```

<a name="swoole-via-laravel-sail"></a>
#### Laravel Sail을 통한 Swoole

> {note} Octane 애플리케이션을 Sail로 제공하기 전에, Laravel Sail의 가장 최신버전을 확인하기 위해 애플리케이션의 root 디렉토리에서 `./vendor/bin/sail build --no-cache` 를 실행해야 합니다. 

다른 대안으로, Swoole 베이스의 Octane 애플리케이션을 개발할때 라라벨을 위한 공식 Docker 기반 개발 환경인 [Laravel Sail](/docs/{{version}}/sail)을 사용할수 있습니다. Laravel Sail은 Swoole 확장모듈을 기본으로 포함하고 있습니다. 그러나 애플리케이션을 계속 실행하려면 Sail에서 사용하는 `supervisor.conf` 파일을 조정해야 합니다. 시작하기 위해 `sail:publish` Artisan 명령을 실행합니다.

```bash
./vendor/bin/sail artisan sail:publish
```

그 다음, 애플리케이션의 `docker/supervisord.conf` 파일의 `command`를 업데이트하여 Sail이 PHP 개발 서버 대신 Octane을 사용하여 애플리케이션이 제공되도록 합니다.

```ini
command=/usr/bin/php -d variables_order=EGPCS /var/www/html/artisan octane:start --server=swoole --host=0.0.0.0 --port=80
```

마지막으로 Sail 이미지를 빌드합니다.

```bash
./vendor/bin/sail build --no-cache
```

<a name="swoole-configuration"></a>
#### Swoole 설정

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
## 애플리케이션에 제공하기

Octane 서버는 `octane:start`라는 Artisan 명령어로 시작할 수 있습니다. 기본적으로 이 명령어는 애플리케이션의 `octane` 설정파일에서 옵션으로 지정한 `server` 설정을 활용합니다.

```bash
php artisan octane:start
```

기본적으로, Octane 서버는 8000 포트로 시작할 수 있고, 웹 브라우저는 `http://localhost:8000`를 통해서 애플리케이션에 접속 할 수 있습니다. 

<a name="serving-your-application-via-https"></a>
### HTTPS을 이용해 애플리케이션 제공하기

기본적으로, Octane을 통해 실행되는 애플리케이션은 `http://` 접두사가 붙은 링크를 생성합니다.  `OCTANE_HTTPS` 환경변수는 `config/octane.php`라는 설정 파일을 통해 사용하고, 애플리케이션을 HTTPS으로 제공하고자 할때 `true`로 세팅할 수 있습니다. 이 설정값을 `true`로 세팅할 때, Octane은 생성된 모든 링크에 `https://`를 접두사로 붙이도록 라라벨에 지시합니다.

```php
'https' => env('OCTANE_HTTPS', false),
```

<a name="serving-your-application-via-nginx"></a>
### Nginx를 이용한 애플리케이션 제공하기

> {tip} 자체 서버 설정을 관리할 수 없거나 강력한 Laravel Octane 애플리케이션을 실행하기 위해 필요로 하는 서비스를 구성하기 어렵다면 [Laravel Forge](https://forge.laravel.com)를 체크하세요. 

Octane 애플리케이션은 production 환경에서 Nginx나 Apache 같은 기존 웹서버 뒤에서 제공되어야 합니다. 그렇게 하면 웹 서버가 이미지나 스타일시트같은 static 자산을 제공하고 SSL 인증서 종료도 관리할 수 있습니다.

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
### 파일 변경 사항을 감지하기 

Octane 서버가 시작 될 때 메모리에 적재해 둔 것을 불러오기 때문에, 웹사이트를 새로고침 할 때 변경된 애플리케이션 파일의 그 어떤 것도 반영해 주지 않습니다. 예를 들어, `routes/web.php`에 route 정의가 추가 되더라도 서버를 재시작하기 전까지는 반영되지 않습니다. 편의상 , `--watch`라는 플래그를 통해 애플리케이션의 파일이 변경될때 마다 Octane 서버가 자동으로 재시작하도록 명령 할 수 있습니다. 

```bash
php artisan octane:start --watch
```

이런 기능을 사용하기 전에, [Node](https://nodejs.org)를 local 개발 환경에 설치해 두어야 합니다. 또, 파일 감시를 위해 [Chokidar](https://github.com/paulmillr/chokidar) 를 project:library:내에 설치해야 합니다.

```bash
npm install --save-dev chokidar
```

애플리케이션의 `config/octane.php` 설정파일 내에서 `watch` 옵션을 사용해 감시해야 하는 디렉토리와 파일을 설정 할 수 있습니다.

<a name="specifying-the-worker-count"></a>
### 워커 수 지정하기

기본적으로 Octane은 애플리케이션의 요청을 처리하는 worker를 기기의 CPU 코어 수 만큼 할당 해서 시작할 수 있습니다. 이 worker들은 HTTP 요청이 애플리케이션으로 들어오는 서비스를 수행할 때 사용되어 집니다. `octane:start` 명령을 호출할 때 `--workers` 옵션으로 시작할 작업자 수를 수동으로 지정할 수 있습니다. 

```bash
php artisan octane:start --workers=4
```

만약 Swoole 애플리케이션 서버를 사용합니다면, 사용하고자 하는 ["task workers"](#concurrent-tasks)수를 얼마나 사용할 지 지정할 수 있습니다. 

```bash
php artisan octane:start --workers=4 --task-workers=6
```

<a name="specifying-the-max-request-count"></a>
### 최대 요청 수 지정하기

stray memory 누수를 방지하기 위해서는, Octane은 주어진 몇몇의 요청을 수행하면서도 우아하게 worker를 재시작 할 수도 있습니다. `--max-requests` 옵션을 사용해 Octane이 이런 동작을 처리하도록 지시 할 수 있습니다. 

```bash
php artisan octane:start --max-requests=250
```

<a name="reloading-the-workers"></a>
### Worker 재호출하기

Octane 서버의 애플리케이션 worker를 우아하게 재시작하기 위해서는 `octane:reload`이라는 명령을 사용할 수 있습니다. 일반적으로, 개발 후 새로운 코드가 배포되고 메모리 안에 적재된 후에 후속 요청들이 사용할 수 있도록, 배포 이후에 수행되야 합니다. 

```bash
php artisan octane:reload
```

<a name="stopping-the-server"></a>
### 서버 중지하기

`octane:stop` 라는 Artisan 명령어를 사용하면 Octane 서버를 중지할 수 있습니다.

```bash
php artisan octane:stop
```

<a name="checking-the-server-status"></a>
#### 서버 상태 확인하기

`octane:stop` 라는 Artisan 명령어를 사용하면 Octane 서버의 현재 상태를 체크할 수 있습니다. 

```bash
php artisan octane:status
```

<a name="dependency-injection-and-octane"></a>
## 의존성 주입 & Octane

Octane은 애플리케이션이 부팅하고 메모리에 적재하는 동안에도 request를 처리하기 때문에, 애플리케이션을 빌드 할 때도 몇가지 고려사항을 숙지해야 합니다. 예를 들면, 애플리케이션 서비스 제공자의 `register` 와 `boot` 메서드는 오직 request worker가 처음 한번 부팅될 때 수행되어 집니다. 후속 요청에서 동일한 애플리케이션 instance는 재사용 되어 집니다. 

이를 고려하여 애플리케이션 서비스 컨테이너 또는 요청은 개체의 생성자에 삽입할 때 특별한 주의를 기울여야 합니다. 이렇게 하면 해당 객체는 오래된 버전의 컨테이너나 후속 요청에 대한 요청을 할 수 있습니다. 

Octane은 자동적으로 모든 first-party framework의 상태와 요청사이에서 재설정을 할 수 있습니다. 하지만, Octane은 언제나 애플리케이션이 설정한 전역 상태를 재설정하는 방법을 항상 알지는 못합니다. 그러므로 Octane에 친화적인 방식으로 애플리케이션을 빌드하는 방법을 알고 있어야 합니다.

<a name="container-injection"></a>
### Container 주입

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

이런 예제는 애플리케이션 boot process 중에 `Service` 인스턴스가 해결되면 컨테이너가 서비스에 주입되고, 동일한 컨테이너가 후속 요청에서 `Service` 인스턴스에 의해 유지됩니다. 이것은 **아마도** 특정 프로세스에서는 큰 문제가 되지 않을 수 있습니다. 하지만, 이로 인해 나중에 부팅 주기나 후속 요청에 의해 추가된 바인딩이 컨테이너에 예상치 않게 누락될 수 있습니다.

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

전역 `app` 헬퍼와 `Container::getInstance()` 메서드는 언제나 application 컨테이너의 가장 최신 버전으로 반환됩니다. 

<a name="request-injection"></a>
### Request 주입

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

이 예제에서는, 만약 `Service`인스턴스가 애플리케이션 boot process 중에 해결된다면, HTTP 요청은 서비스에 주입되고 동일한 요청은 후속 요청에서 `Service` 인스턴스에 의해 유지됩니다. 그러므로, 모든 헤더와, input, 그리고 쿼리 스트링 data, 심지어는 다른 request data 마저도 올바르지 않습니다.

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

전역 `request` 헬퍼는 항상 애플리케이션이 현재 처리하고 있는 요청을 반환하므로 애플리케이션 내에서 안전하게 사용할 수 있습니다.

> {note} 컨트롤러의 메서드와 route의 클로저에서 `Illuminate\Http\Request` 인스턴스를 타입힌트로 사용하는 것은 허용 됩니다. 

<a name="configuration-repository-injection"></a>
### 설정 Repository 주입

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

이 예제에서, 만약 설정 값이 요청들 사이에서 변경되면, 서비스는 원래 repository 인스턴스에 의존하기 때문에 새로운 값에 접근 할수 없습니다.

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

전역 `config`는 항상 가장 최신의 설정 repository 버전을 반환하기 때문에 애플리케이션 내에서 안전하게 사용할 수 있습니다.

<a name="managing-memory-leaks"></a>
### Memory 누수 관리하기

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

애플리케이션을 빌드하는 동안에, 특별한 주의를 통해 이런 타입의 메모리 누수가 발생하지 않도록 해야 합니다. 애플리케이션에서 새로운 메모리 누수가 발생하지 않도록 로컬 개발 중에 애플리케이션의 메모리 사용량을 모니터링하는 것이 좋습니다.

<a name="concurrent-tasks"></a>
## 동시성 Tasks

> {note} 이 기능은 [Swoole](#swoole)이 필요합니다. 

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

Octane은 Swoole의 "task workers"를 통해 동시적으로 task들을 수행하고, 들어오는 요청과 전혀 다른 프로세스로 수행합니다. 동시 작업을 처리하는 데 사용할 수 있는 작업자의 수는 `octane:start` 명령어를 사용할 때 `--task-workers` 지시문에 의해 결정됩니다.

```bash
php artisan octane:start --workers=4 --task-workers=6
```

<a name="ticks-and-intervals"></a>
## Ticks & Intervals

> {note} 이 기능은 [Swoole](#swoole)이 필요합니다.

Swoole을 사용해, "tick" 지정된 시간(초)마다 실행될 작업을 등록할 수 있습니다. "tick" 콜백은 `tick`메서드를 통해서 등록할수 있습니다. 첫번째 인자는 `tick`메서드를 대표하는 ticker의 이름을 문자열을 받습니다. 두번째 인자는 지정된 간격으로 호출되는 callable 입니다. 

예제에서는, 10초 간격으로 호출되는 클로저를 등록했습니다. 일반적으로 `tick`메서드는 애플리케이션의 서비스 제공자 중 하나로 `boot`메서드와 함께 호출됩니다.

```php
Octane::tick('simple-ticker', fn () => ray('Ticking...'))
        ->seconds(10);
```

`immediate` 메서드를 사용할때, Octane 서버가 초기화 할때 이후부터는 Octane이 즉시 tick 콜백을 호출하도록 명령을 합니다.

```php
Octane::tick('simple-ticker', fn () => ray('Ticking...'))
        ->seconds(10)
        ->immediate();
```

<a name="the-octane-cache"></a>
## Octane 캐시

> {note} 이 기능은 [Swoole](#swoole)이 필요합니다.

Swoole을 사용할 때, 초당 최대 200만 작업의 읽기 및 쓰기 속도 제공하는 Octane 캐시 드라이버 활용할 수 있습니다. 그러므로, 캐시 드라이버는 caching layer를 이용해 extream한 읽기/쓰기 속도가 필요한 애플리케이션에서 위한 최고의 선택입니다. 

캐시 드라이버는 [Swoole tables](https://www.swoole.co.uk/docs/modules/swoole-table) 로 구동됩니다. 캐시에 저장된 모든 데이터는 서버의 모든 작업자가 사용할 수 있습니다. 하지만, 캐시 데이터는 서버가 재시작 될 때 날아가버립니다. 

```php
Cache::store('octane')->put('framework', 'Laravel', 30);
```

> {tip} 애플리케이션의 `octane` 설정 파일에서 Octane 캐시에 허용할 최대 항목의 수를 정의할 수 있습니다. 
 
<a name="cache-intervals"></a>
### 주기적으로 캐시하기

라라벨의 캐시 시스템으로 제공되는 일반적인 방법 말고도 Octane 캐시 드라이버는 interval 기반의 캐시를 제공합니다. 이 캐시들은 지정된 간격에 자동적으로 새로고침됩니다. 그리고 애플리케이션의 서비스 제공자 중 하나인 `boot` 메소드 내에 등록해야 합니다. 예를 들면, 다음 캐시는 5초마다 새로 고쳐집니다.

```php
use Illuminate\Support\Str;

Cache::store('octane')->interval('random', function () {
    return Str::random(10);
}, seconds: 5)
```

<a name="tables"></a>
## Tables

> {note} 이 기능은 [Swoole](#swoole)이 필요합니다.

Swoole을 사용할때, 임의의 [Swoole 테이블]을 정의하고 상호 작용할 수 있습니다. Swoole 테이블은 extream한 성능 처리량을 제공하며, 이 테이블의 데이터는 서버의 모든 작업자가 접근할 수 있습니다. 하지만, 서버가 재시작되면 데이터가 사라지게 됩니다. 

테이블은 애플리케이션의 `octane` 설정 파일에서 `tables`설정 배열을 사용해 수정 될 수 있습니다. 예제 테이블은 최대 1000개의 row로 이미 준비되어 있습니다. 문자열 row의 최대 크기는 아래와 같이 row 유형 뒤에 row 크기를 지정하여 설정할 수 있습니다.

```php
'tables' => [
    'example:1000' => [
        'name' => 'string:1000',
        'votes' => 'int',
    ],
],
```

`Octane::table` 메서드를 사용하면 테이블에 접근 할 수 있습니다. 

```php
use Laravel\Octane\Facades\Octane;

Octane::table('example')->set('uuid', [
    'name' => 'Nuno Maduro',
    'votes' => 1000,
]);

return Octane::table('example')->get('uuid');
```

> {note} 컬럼 타입은 Swoole 테이블에서 `string`, `int`, `float`을 제공합니다. 