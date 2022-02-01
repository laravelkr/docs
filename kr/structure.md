# Directory Structure
# 디렉토리 구조

- [Introduction](#introduction)
- [시작하기](#introduction)
- [The Root Directory](#the-root-directory)
- [Root 디렉토리](#the-root-directory)
    - [The `app` Directory](#the-root-app-directory)
    - [`app` 디렉토리](#the-root-app-directory)
    - [The `bootstrap` Directory](#the-bootstrap-directory)
    - [`bootstrap` 디렉토리](#the-bootstrap-directory)
    - [The `config` Directory](#the-config-directory)
    - [`config` 디렉토리](#the-config-directory)
    - [The `database` Directory](#the-database-directory)
    - [`database` 디렉토리](#the-database-directory)
    - [The `public` Directory](#the-public-directory)
    - [`public` 디렉토리](#the-public-directory)
    - [The `resources` Directory](#the-resources-directory)
    - [`resources` 디렉토리](#the-resources-directory)
    - [The `routes` Directory](#the-routes-directory)
    - [`routes` 디렉토리](#the-routes-directory)
    - [The `storage` Directory](#the-storage-directory)
    - [`storage` 디렉토리](#the-storage-directory)
    - [The `tests` Directory](#the-tests-directory)
    - [`tests` 디렉토리](#the-tests-directory)
    - [The `vendor` Directory](#the-vendor-directory)
    - [`vendor` 디렉토리](#the-vendor-directory)
- [The App Directory](#the-app-directory)
- [App 디렉토리](#the-app-directory)
    - [The `Broadcasting` Directory](#the-broadcasting-directory)
    - [`Broadcasting` 디렉토리](#the-broadcasting-directory)
    - [The `Console` Directory](#the-console-directory)
    - [`Console` 디렉토리](#the-console-directory)
    - [The `Events` Directory](#the-events-directory)
    - [`Events` 디렉토리](#the-events-directory)
    - [The `Exceptions` Directory](#the-exceptions-directory)
    - [`Exceptions` 디렉토리](#the-exceptions-directory)
    - [The `Http` Directory](#the-http-directory)
    - [`Http` 디렉토리](#the-http-directory)
    - [The `Jobs` Directory](#the-jobs-directory)
    - [`Jobs` 디렉토리](#the-jobs-directory)
    - [The `Listeners` Directory](#the-listeners-directory)
    - [`Listeners` 디렉토리](#the-listeners-directory)
    - [The `Mail` Directory](#the-mail-directory)
    - [`Mail` 디렉토리](#the-mail-directory)
    - [The `Models` Directory](#the-models-directory)
    - [`Models` 디렉토리](#the-models-directory)
    - [The `Notifications` Directory](#the-notifications-directory)
    - [`Notifications` 디렉토리](#the-notifications-directory)
    - [The `Policies` Directory](#the-policies-directory)
    - [`Policies` 디렉토리](#the-policies-directory)
    - [The `Providers` Directory](#the-providers-directory)
    - [`Providers` 디렉토리](#the-providers-directory)
    - [The `Rules` Directory](#the-rules-directory)
    - [`Rules` 디렉토리](#the-rules-directory)

<a name="introduction"></a>
## Introduction
## 시작하기

The default Laravel application structure is intended to provide a great starting point for both large and small applications. But you are free to organize your application however you like. Laravel imposes almost no restrictions on where any given class is located - as long as Composer can autoload the class.

기본적인 라라벨 애플리케이션의 구조는 애플리케이션이 크건, 작건 좋은 시작점이 되는것을 의도하고 있습니다. 애플리케이션을 원하는대로 구성해도 됩니다. 컴포저가 클래스를 오토로딩할 수 있는 한 클래스를 어디에 위치시키는가에 대한 제약사항은 없습니다.

<a name="the-root-directory"></a>
## The Root Directory
## Root 디렉토리

<a name="the-root-app-directory"></a>
#### The App Directory
#### App 디렉토리

The `app` directory contains the core code of your application. We'll explore this directory in more detail soon; however, almost all of the classes in your application will be in this directory.

`app` 디렉토리에는 애플리케이션의 핵심 코드가 들어 있습니다. 우리는 이 디렉토리를 더 자세히 살펴볼 것입니다. 애플리케이션의 거의 모든 클래스가 이 디렉토리에 있습니다.

<a name="the-bootstrap-directory"></a>
#### The Bootstrap Directory
#### Bootstrap 디렉토리

The `bootstrap` directory contains the `app.php` file which bootstraps the framework. This directory also houses a `cache` directory which contains framework generated files for performance optimization such as the route and services cache files.

`bootstrap` 디렉토리는 프레임워크의 부트스트래핑을 담당하는 `app.php` 파일을 가지고 있습니다. 또한 프레임워크가 라우트나 서비스 캐시 파일과 같은 퍼포먼스 향상을 위해서 생성하는 `cache` 디렉토리를 가지고 있습니다.

<a name="the-config-directory"></a>
#### The Config Directory
#### Config 디렉토리

The `config` directory, as the name implies, contains all of your application's configuration files. It's a great idea to read through all of these files and familiarize yourself with all of the options available to you.

`config` 디렉토리는 이름이 의미하는 것과 같이, 애플리케이션의 설정 파일을 포함하고 있습니다. 여기 있는 모든 파일들을 읽어보고 사용할 수 있는 옵션들을 익혀두는 것이 좋습니다.

<a name="the-database-directory"></a>
#### The Database Directory
#### Database 디렉토리

The `database` directory contains your database migrations, model factories, and seeds. If you wish, you may also use this directory to hold an SQLite database.

`database` 디렉토리는 데이터베이스 마이그레이션 파일, 모델 팩토리, 시딩 파일들을 포함하고 있습니다. 원한다면, 이 디렉토리를 SQLite 데이터베이스가 저장되는 곳으로 사용할수도 있습니다.

<a name="the-public-directory"></a>
#### The Public Directory
#### Public 디렉토리

The `public` directory contains the `index.php` file, which is the entry point for all requests entering your application and configures autoloading. This directory also houses your assets such as images, JavaScript, and CSS.

`public` 디렉토리는 애플리케이션에 진입하는 모든 request-요청들에 대한 진입점 역할과 오토로딩을 설정하는 `index.php` 파일을 가지고 있습니다. 이 디렉토리는 이미지나 자바스크립트, CSS와 같은 asset파일들을도 포함되어 있습니다.

<a name="the-resources-directory"></a>
#### The Resources Directory
#### Resources 디렉토리

The `resources` directory contains your views as well as your raw, un-compiled assets such as LESS, SASS, or JavaScript. This directory also houses all of your language files.

`resources` 디렉토리는 뷰 파일과 LESS, SASS, 자바스크립트와 같이 컴파일 되기 전의 asset파일들을 가지고 있습니다. 이 디렉토리는 또한 다국어 파일도 들어 있습니다.

<a name="the-routes-directory"></a>
#### The Routes Directory
#### Routes 디렉토리

The `routes` directory contains all of the route definitions for your application. By default, several route files are included with Laravel: `web.php`, `api.php`, `console.php` and `channels.php`.

`routes` 디렉토리는 애플리케이션에서 정의된 모든 라우트들이 들어 있습니다. 기본적으로 여기에는 라라벨에서 제공하는 `web.php`, `api.php`, `console.php` 그리고 `channels.php` 파일이 포함되어 있습니다.

The `web.php` file contains routes that the `RouteServiceProvider` places in the `web` middleware group, which provides session state, CSRF protection, and cookie encryption. If your application does not offer a stateless, RESTful API, all of your routes will most likely be defined in the `web.php` file.

`web.php` 파일은 `RouteServiceProvider` 의 `web` 미들웨어 그룹안에 포함되는 라우트들을 포함하고 있으며, 세션 상태, CSRF 보호, 쿠키 암호화 기능을 제공합니다. 만약 여러분의 애플리케이션이 상태를 저장하지 않는 RESTful API를 제공하지 않는다면, 대부분의 라우트는 `web.php` 파일안에 정의될것입니다.

The `api.php` file contains routes that the `RouteServiceProvider` places in the `api` middleware group, which provides rate limiting. These routes are intended to be stateless, so requests entering the application through these routes are intended to be authenticated via tokens and will not have access to session state.

`api.php` 파일은 `RouteServiceProvider` 의 `api` 미들웨어 그룹안에 포함되는 라우트들을 포함하고 있으며, 접속 속도 제한 기능을 제공합니다. 이 라우트들은 상태를 저장하지 않는것을 대상으로 하며 따라서 requests-요청들이 애플리케이션에 진입하면 이 라우트들을 통해서 토큰을 통해서 인증되고, 세션 상태에 엑세스 할 수 없습니다.

The `console.php` file is where you may define all of your Closure based console commands. Each Closure is bound to a command instance allowing a simple approach to interacting with each command's IO methods. Even though this file does not define HTTP routes, it defines console based entry points (routes) into your application.

`console.php` 파일은 클로저 기반의 명령어들을 정의해 놓을 수 있는 파일입니다. 각각의 클로저는 각각의 명령어 IO 메소드와 상호작용하는 간단한 명령어를 구현하는 명령어 인스턴스에 바인드 되어 있습니다. 이 파일에는 HTTP 라우트가 정의되어 있지는 않지만, 애플리케이션에 콘솔 기반의 진입점(라우트와 비슷하게)을 정의합니다.

The `channels.php` file is where you may register all of the event broadcasting channels that your application supports.

`channels.php` 파일은 애플리케이션에서 지원하는 모든 이벤트 브로드캐스팅 채널을 등록하는 파일입니다.

<a name="the-storage-directory"></a>
#### The Storage Directory
#### Storage 디렉토리

The `storage` directory contains your compiled Blade templates, file based sessions, file caches, and other files generated by the framework. This directory is segregated into `app`, `framework`, and `logs` directories. The `app` directory may be used to store any files generated by your application. The `framework` directory is used to store framework generated files and caches. Finally, the `logs` directory contains your application's log files.

`storage` 디렉토리는 여러분의 컴파일된 블레이트 템플릿, 파일 기반의 세션, 파일 캐시 그리고 기타 프레임워크에서 생성된 파일들을 가지고 있습니다. 이 디렉토리는 그 안에 `app`, `framework` 그리고 `logs` 디렉토리로 구성되어 있습니다. `app` 디렉토리는 애플리케이션에 의해서 생성된 파일을 저장하는데 사용되어집니다. `framework` 디렉토리는 프레임워크가 생성한 파일들과 캐시를 저장하는데 사용됩니다. 마지막으로 `logs` 디렉토리는 애플리케이션의 로그 파일들을 가지고 있습니다.

The `storage/app/public` directory may be used to store user-generated files, such as profile avatars, that should be publicly accessible. You should create a symbolic link at `public/storage` which points to this directory. You may create the link using the `php artisan storage:link` command.

`storage/app/public` 디렉토리는 공개적으로 엑세스가 가능해야 하는 프로필 아바타와 같은 사용자가 생성한 파일들에 사용할 수 있습니다. 여러분은 이 디렉토리를 가리키는 `public/storage` 심볼릭 링크를 생성해야 합니다. `php artisan storage:link` 명령어를 통해서 이 링크를 생성할 수 있습니다.

<a name="the-tests-directory"></a>
#### The Tests Directory
#### Tests 디렉토리

The `tests` directory contains your automated tests. An example [PHPUnit](https://phpunit.de/) test is provided out of the box. Each test class should be suffixed with the word `Test`. You may run your tests using the `phpunit` or `php vendor/bin/phpunit` commands.

`tests` 디렉토리는 자동화된 테스트가 포함되어 있습니다. 별다른 설정 없이도 [PHPUnit](https://phpunit.de/) 테스트의 예제가 제공됩니다. 각각의 테스트 클래스는 `Test` 라는 단어가 붙어 있어야 합니다. `phpunit` 또는 `php vendor/bin/phpunit` 명령어를 통해서 테스트를 실행할 수 있습니다.

<a name="the-vendor-directory"></a>
#### The Vendor Directory
#### Vendor 디렉토리

The `vendor` directory contains your [Composer](https://getcomposer.org) dependencies.

`vendor` 디렉토리는 [Composer](https://getcomposer.org)의 의존성 폴더 입니다.

<a name="the-app-directory"></a>
## The App Directory
## App 디렉토리

The majority of your application is housed in the `app` directory. By default, this directory is namespaced under `App` and is autoloaded by Composer using the [PSR-4 autoloading standard](https://www.php-fig.org/psr/psr-4/).

애플리케이션의 대부분의 파일들은 `app` 디렉토리 안에 보관됩니다. 기본적으로 이 디렉토리는 [PSR-4 오토로딩 표준](https://www.php-fig.org/psr/psr-4/)를 사용하는 컴포저에 의해서 `App` 이라는 네임스페이스를 통해서 오토로딩됩니다.

The `app` directory contains a variety of additional directories such as `Console`, `Http`, and `Providers`. Think of the `Console` and `Http` directories as providing an API into the core of your application. The HTTP protocol and CLI are both mechanisms to interact with your application, but do not actually contain application logic. In other words, they are two ways of issuing commands to your application. The `Console` directory contains all of your Artisan commands, while the `Http` directory contains your controllers, middleware, and requests.

`app` 디렉토리는 `Console`, `Http` 그리고 `Providers` 와 같은 다양한 추가 디렉토리를 가지고 있습니다. `Console` 과 `Http` 디렉토리는 애플리케이션의 코어에 API를 제공하고 있다고 생각할 수 있습니다. HTTP 프로토콜과 CLI 모두 애플리케이션과 상호작용하는 매커니즘이지만, 실제 애플리케이션 로직을 포함하지는 않습니다. 다시말해, 이것은 애플리케이션에 명령을 보내는 두가지 방법일 뿐입니다. `Console` 디렉토리는 모든 아티즌 명령어들을 포함하고, `Http` 디렉토리는 컨트롤러와 미들웨어, requests 들을 포함합니다.

A variety of other directories will be generated inside the `app` directory as you use the `make` Artisan commands to generate classes. So, for example, the `app/Jobs` directory will not exist until you execute the `make:job` Artisan command to generate a job class.

다른 다양한 디렉토리들은 클래스들을 생성하기 위해서 `make` 아티즌 명령어를 실행할 때 `app` 디렉토리 내부에 생성됩니다. 따라서 예를들어, `app/Jobs` 디렉토리는 job 클래스를 생성하기 위해서 `make:job` 아티즌 명령어를 실행하기 전까지 존재하지 않습니다.

> {tip} Many of the classes in the `app` directory can be generated by Artisan via commands. To review the available commands, run the `php artisan list make` command in your terminal.

> {tip} `app` 디렉토리 안에 있는 많은 클래스들은 아티즌 명령어를 통해서 생성됩니다. 사용가능한 명령어들을 살펴보기 위해서는 터미널에서 `php artisan list make` 명령어를 실행하면 됩니다.

<a name="the-broadcasting-directory"></a>
#### The Broadcasting Directory
#### Broadcasting 디렉토리

The `Broadcasting` directory contains all of the broadcast channel classes for your application. These classes are generated using the `make:channel` command. This directory does not exist by default, but will be created for you when you create your first channel. To learn more about channels, check out the documentation on [event broadcasting](/docs/{{version}}/broadcasting).

`Broadcasting` 디렉토리는 애플리케이션의 모든 브로드캐스트 채널 클래스를 포함하고 있습니다. 이 클래스는 `make:channel` 명령어를 통해서 생성될 수 있습니다. 이 디렉토리는 기본적으로 존재하지 않지만, 첫번째 채널 클래스가 생성될 때 만들어 집니다. 채널에 대한 보다 자세한 사항은 [이벤트 브로드캐스팅](/docs/{{version}}/broadcasting) 문서를 참고하십시오.

<a name="the-console-directory"></a>
#### The Console Directory
#### Console 디렉토리

The `Console` directory contains all of the custom Artisan commands for your application. These commands may be generated using the `make:command` command. This directory also houses your console kernel, which is where your custom Artisan commands are registered and your [scheduled tasks](/docs/{{version}}/scheduling) are defined.

`Console` 디렉토리는 애플리케이션의 모든 사용자 아티즌 명령어들을 포함하고 있습니다. 이 명령어들은 `make:command` 명령어를 통해서 생성될 수 있습니다. 이 디렉토리는 또한, 사용자가 정의한 아티즌 명령어들의 [스케줄링 작업](/docs/{{version}}/scheduling)이 등록되어 있는 콘솔 커널이 저장되어 있는 디렉토리 입니다.

<a name="the-events-directory"></a>
#### The Events Directory
#### Events 디렉토리

This directory does not exist by default, but will be created for you by the `event:generate` and `make:event` Artisan commands. The `Events` directory houses [event classes](/docs/{{version}}/events). Events may be used to alert other parts of your application that a given action has occurred, providing a great deal of flexibility and decoupling.

이 디렉토리는 기본적으로 존재하지 않지만 `event:generate`와 `make:event` 아티즌 명령으로 생성됩니다. `Events` 디렉토리에는 [events classes](/docs/{{version}}/events)가 있습니다. 이벤트는 애플리케이션의 다른 부분에 주어진 동작이 발생했음을 알리는 데 사용되며 많은 유연성과 디커플링을 제공합니다.

<a name="the-exceptions-directory"></a>
#### The Exceptions Directory
#### Exceptions 디렉토리

The `Exceptions` directory contains your application's exception handler and is also a good place to place any exceptions thrown by your application. If you would like to customize how your exceptions are logged or rendered, you should modify the `Handler` class in this directory.

`Exceptions` 디렉토리는 애플리케이션의 예외-exception 핸들러를 포함하고 있으며, 또한 애플리케이션에서 발생하는 exception들을 배치하기 좋은 장소입니다. exception 이 어떻게 로깅되고, 렌더링 될지 커스터마이징 하고자 한다면, 이 디렉토리에 있는 `Handler` 클래스를 수정해야 합니다.

<a name="the-http-directory"></a>
#### The Http Directory
#### Http 디렉토리

The `Http` directory contains your controllers, middleware, and form requests. Almost all of the logic to handle requests entering your application will be placed in this directory.

`Http` 디렉토리는 컨트롤러, 미들웨어 그리고 form request 를 가지고 있습니다. 애플리케이션으로 들어오는 request를 처리하는 대부분의 로직은 이 디렉토리에 위치하고 있습니다.

<a name="the-jobs-directory"></a>
#### The Jobs Directory
#### Jobs 디렉토리

This directory does not exist by default, but will be created for you if you execute the `make:job` Artisan command. The `Jobs` directory houses the [queueable jobs](/docs/{{version}}/queues) for your application. Jobs may be queued by your application or run synchronously within the current request lifecycle. Jobs that run synchronously during the current request are sometimes referred to as "commands" since they are an implementation of the [command pattern](https://en.wikipedia.org/wiki/Command_pattern).

이 디렉토리는 기본적으로 존재하지 않지만, `make:job` 아티즌 명령어를 실행하는 경우 생성됩니다. `Jobs` 디렉토리는 애플리케이션의 [큐로 지정이 가능한 작업](/docs/{{version}}/queue)모아 놓은 곳입니다. jobs 는 애플리케이션에 의해서 큐를 통해 실생되거나 현재 request 라이프사이클 안에서 실행됩니다. 현재 request 안에서 동기적으로 실행되는 job은 [커맨드 패턴](https://en.wikipedia.org/wiki/Command_pattern)을 구현하고 있기 때문에 "command"라고 지칭하기도 합니다.

<a name="the-listeners-directory"></a>
#### The Listeners Directory
#### Listeners 디렉토리

This directory does not exist by default, but will be created for you if you execute the `event:generate` or `make:listener` Artisan commands. The `Listeners` directory contains the classes that handle your [events](/docs/{{version}}/events). Event listeners receive an event instance and perform logic in response to the event being fired. For example, a `UserRegistered` event might be handled by a `SendWelcomeEmail` listener.

이 디렉토리는 기본적으로 존재하지 않지만, `event:generate` 나 `make:listener` 아티즌 명령어를 실행하는 경우 생성됩니다. `Listeners` 디렉토리는 [이벤트](/docs/{{version}}/events)를 처리하는 클래스들을 가지고 있습니다. 이벤트 리스너는 이벤트 인스턴스를 수신하여 발생한 이벤트에 대응하는 로직을 수행합니다. 예를 들어, `UserRegistered` 이벤트는 `SendWelcomeEmail` 리스너에 의해서 처리될 것입니다.

<a name="the-mail-directory"></a>
#### The Mail Directory
#### Mail 디렉토리

This directory does not exist by default, but will be created for you if you execute the `make:mail` Artisan command. The `Mail` directory contains all of your classes that represent emails sent by your application. Mail objects allow you to encapsulate all of the logic of building an email in a single, simple class that may be sent using the `Mail::send` method.

이 디렉토리는 기본적으로 존재하지 않지만, `make:mail` 아티즌 명령어를 실행하는 경우 생성됩니다. `Mail` 디렉토리는 애플리케이션에 의해서 발송되는 이메일을 나타내는 클래스들을 가지고 있습니다. 메일 객체는 `Mail::send` 메소드를 사용하여 보낼 수 있는 간단한 하나의 클래스를 통해서 이메일을 구성하는 로직을 캡슐화 합니다.

<a name="the-models-directory"></a>
#### The Models Directory
#### Models 디렉토리

The `Models` directory contains all of your [Eloquent model classes](/docs/{{version}}/eloquent). The Eloquent ORM included with Laravel provides a beautiful, simple ActiveRecord implementation for working with your database. Each database table has a corresponding "Model" which is used to interact with that table. Models allow you to query for data in your tables, as well as insert new records into the table.

`Models` 디렉토리는 모든 [Eloquent 모델 클래스](/docs/{{version}}/eloquent)를 포함하고 있습니다. 라라벨에 포함된 Eloquent ORM은 데이터베이스를 다루는 아름답고 간편한 ActiveRecord-액티브레코드 구현을 제공합니다. 각각의 데이터베이스 테이블은 해당 테이블과 상호작용하기 위해 대응되는 "모델"을 가집니다. 모델을 이용해 테이블의 데이터를 쿼리하고 테이블에 새로운 레코드를 삽입할 수 있습니다.

<a name="the-notifications-directory"></a>
#### The Notifications Directory
#### Notifications 디렉토리

This directory does not exist by default, but will be created for you if you execute the `make:notification` Artisan command. The `Notifications` directory contains all of the "transactional" notifications that are sent by your application, such as simple notifications about events that happen within your application. Laravel's notification features abstracts sending notifications over a variety of drivers such as email, Slack, SMS, or stored in a database.

이 디렉토리는 기본적으로 존재하지 않지만, `make:notification` 아티즌 명령어를 실행하는 경우 생성됩니다. `Notifications` 디렉토리는 애플리케이션 안에서 발생하는 이벤트에 대한 간단한 알림으로, 애플리케이션에서 전송되는 "트랜잭션" 알림을 제공합니다. 라라벨의 알림 기능은 이메일, 슬랙, SMS와 같은 다양한 드라이버를 통해서 알림을 보내거나, 데이터베이스에 저장하는 추상화 기능입니다.

<a name="the-policies-directory"></a>
#### The Policies Directory
#### Policies 디렉토리

This directory does not exist by default, but will be created for you if you execute the `make:policy` Artisan command. The `Policies` directory contains the authorization policy classes for your application. Policies are used to determine if a user can perform a given action against a resource. For more information, check out the [authorization documentation](/docs/{{version}}/authorization).

이 디렉토리는 기본적으로 존재하지 않지만, `make:policy` 아티즌 명령어를 실행하는 경우 생성됩니다. `Policies` 디렉토리는 애플리케이션의 authorization policy 클래스를 가지고 있습니다. Policies는 사용자가 특정 리소스에 대한 주어진 액션을 수행할 수 있는지를 결정하는데 사용됩니다. 보다 자세한 정보는 [authorization 문서](/docs/{{version}}/authorization)를 참고하십시오.

<a name="the-providers-directory"></a>
#### The Providers Directory
#### 프로바이더 디렉토리

The `Providers` directory contains all of the [service providers](/docs/{{version}}/providers) for your application. Service providers bootstrap your application by binding services in the service container, registering events, or performing any other tasks to prepare your application for incoming requests.

`Providers` 디렉토리는 애플리케이션의 모든 [서비스 프로바이더](/docs/{{version}}/providers) 파일들을 가지고 있습니다. 서비스 프로바이더는 애플리케이션에서 필요한 서비스들을 컨테이너에 바인딩하고, 이벤트를 등록하고, 기타 애플리케이션이 유입된 request들을 처리하기 위해서 준비되어야 하는 작업들을 수행합니다.

In a fresh Laravel application, this directory will already contain several providers. You are free to add your own providers to this directory as needed.

라라벨을 새롭게 설치하면 이 디렉토리안에는 이미 몇몇 프로바이더들이 포함되어 있습니다. 필요에 따라 이 디렉토리에 여러분의 프로바이더를 자유롭게 추가할 수 있습니다.

<a name="the-rules-directory"></a>
#### The Rules Directory
#### Rules 디렉토리

This directory does not exist by default, but will be created for you if you execute the `make:rule` Artisan command. The `Rules` directory contains the custom validation rule objects for your application. Rules are used to encapsulate complicated validation logic in a simple object. For more information, check out the [validation documentation](/docs/{{version}}/validation).

이 디렉토리는 기본적으로 존재하지 않습니다만, `make:rule` 아티즌 명령어를 실행하게 된다면 생성됩니다. `Rules` 디렉토리는 애플리케이션에서 사용할 수 있는 커스텀 validation-유효성 검사를 위한 객체들을 포함하게 됩니다. Rules 은 복잡한 유효성 검사 로직을 간단한 객체로 캡슐화 하는데 사용됩니다. 보다 자세한 내용은 [validation 문서](/docs/{{version}}/validation).를 참고하십시오.
