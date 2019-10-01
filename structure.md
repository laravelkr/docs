# 디렉토리 구조

- [시작하기](#introduction)
- [Root 디렉토리](#the-root-directory)
    - [`app` 디렉토리](#the-root-app-directory)
    - [`bootstrap` 디렉토리](#the-bootstrap-directory)
    - [`config` 디렉토리](#the-config-directory)
    - [`database` 디렉토리](#the-database-directory)
    - [`public` 디렉토리](#the-public-directory)
    - [`resources` 디렉토리](#the-resources-directory)
    - [`routes` 디렉토리](#the-routes-directory)
    - [`storage` 디렉토리](#the-storage-directory)
    - [`tests` 디렉토리](#the-tests-directory)
    - [`vendor` 디렉토리](#the-vendor-directory)
- [App 디렉토리](#the-app-directory)
    - [`Broadcasting` 디렉토리](#the-broadcasting-directory)
    - [`Console` 디렉토리](#the-console-directory)
    - [`Events` 디렉토리](#the-events-directory)
    - [`Exceptions` 디렉토리](#the-exceptions-directory)
    - [`Http` 디렉토리](#the-http-directory)
    - [`Jobs` 디렉토리](#the-jobs-directory)
    - [`Listeners` 디렉토리](#the-listeners-directory)
    - [`Mail` 디렉토리](#the-mail-directory)
    - [`Notifications` 디렉토리](#the-notifications-directory)
    - [`Policies` 디렉토리](#the-policies-directory)
    - [`Providers` 디렉토리](#the-providers-directory)
    - [`Rules` 디렉토리](#the-rules-directory)

<a name="introduction"></a>
## 시작하기

기본적인 라라벨 애플리케이션의 구조는 애플리케이션이 크건, 작건 좋은 시작점이 되는것을 의도하고 있습니다. 애플리케이션을 원하는대로 구성해도 됩니다. 컴포저가 클래스를 오토로딩할 수 있는 한 클래스를 어디에 위치시키는가에 대한 제약사항은 없습니다.

#### 모델 디렉토리는 어디에 있나요?

라라벨을 시작할 때, 많은 개발자들이 `models` 디렉토리가 없는 것에 혼란을 느낍니다. 하지만 이 디렉토리가 없는 것은 의도된 것입니다. 우리는 "모델"이라는 많은 사람들에게 각기 다른 의미로 받아들려지기 때문에 모호하다는 것을 알게되었습니다. 일부 개발자들은 애플리케이션의 "모델"을 비지니스 로직의 전체로서 참조하는 반면, 다른 개발자들은 "모델"을 관계형 데이터베이스와 상호작용하는데 사용합니다.

이러한 이유로, 기본적으로 `app` 디렉토리를 Eloquent 모델이 저장되는 곳으로 선택했으며, 개발자가 원하는 그 어떤 곳이라도 이 파일들을 배치할 수 있게 허용 하고 있습니다.

<a name="the-root-directory"></a>
## Root 디렉토리

<a name="the-root-app-directory"></a>
#### App 디렉토리

`app` 디렉토리에는 애플리케이션의 핵심 코드가 들어 있습니다. 우리는 이 디렉토리를 더 자세히 살펴볼 것입니다. 애플리케이션의 거의 모든 클래스가 이 디렉토리에 있습니다.

<a name="the-bootstrap-directory"></a>
#### Bootstrap 디렉토리

`bootstrap` 디렉토리는 프레임워크의 부트스트래핑을 담당하는 `app.php` 파일을 가지고 있습니다. 또한 프레임워크가 라우트나 서비스 캐시 파일과 같은 퍼포먼스 향상을 위해서 생성하는 `cache` 디렉토리를 가지고 있습니다.

<a name="the-config-directory"></a>
#### Config 디렉토리

`config` 디렉토리는 이름이 의미하는 것과 같이, 애플리케이션의 설정 파일을 포함하고 있습니다. 여기 있는 모든 파일들을 읽어보고 사용할 수 있는 옵션들을 익혀두는 것이 좋습니다.

<a name="the-database-directory"></a>
#### Database 디렉토리

`database` 디렉토리는 데이터베이스 마이그레이션 파일, 모델 팩토리, 시딩 파일들을 포함하고 있습니다. 원한다면, 이 디렉토리를 SQLite 데이터베이스가 저장되는 곳으로 사용할수도 있습니다.

<a name="the-public-directory"></a>
#### Public 디렉토리

`public` 디렉토리는 애플리케이션에 진입하는 모든 request-요청들에 대한 진입점 역할과 오토로딩을 설정하는 `index.php` 파일을 가지고 있습니다. 이 디렉토리는 이미지나 자바스크립트, CSS와 같은 asset파일들을도 포함되어 있습니다.

<a name="the-resources-directory"></a>
#### Resources 디렉토리

`resources` 디렉토리는 뷰 파일과 LESS, SASS, 자바스크립트와 같이 컴파일 되기 전의 asset파일들을 가지고 있습니다. 이 디렉토리는 또한 다국어 파일도 들어 있습니다.

<a name="the-routes-directory"></a>
#### Routes 디렉토리

`routes` 디렉토리는 애플리케이션에서 정의된 모든 라우트들이 들어 있습니다. 기본적으로 여기에는 라라벨에서 제공하는 `web.php`, `api.php`, `console.php` 그리고 `channels.php` 파일이 포함되어 있습니다.

`web.php` 파일은 `RouteServiceProvider` 의 `web` 미들웨어 그룹안에 포함되는 라우트들을 포함하고 있으며, 세션 상태, CSRF 보호, 쿠키 암호화 기능을 제공합니다. 만약 여러분의 애플리케이션이 상태를 저장하지 않는 RESTful API를 제공하지 않는다면, 대부분의 라우트는 `web.php` 파일안에 정의될것입니다.

`api.php` 파일은 `RouteServiceProvider` 의 `api` 미들웨어 그룹안에 포함되는 라우트들을 포함하고 있으며, 접속 속도 제한 기능을 제공합니다. 이 라우트들은 상태를 저장하지 않는것을 대상으로 하며 따라서 requests-요청들이 애플리케이션에 진입하면 이 라우트들을 통해서 토큰을 통해서 인증되고, 세션 상태에 엑세스 할 수 없습니다.

`console.php` 파일은 클로저 기반의 명령어들을 정의해 놓을 수 있는 파일입니다. 각각의 클로저는 각각의 명령어 IO 메소드와 상호작용하는 간단한 명령어를 구현하는 명령어 인스턴스에 바인드 되어 있습니다. 이 파일에는 HTTP 라우트가 정의되어 있지는 않지만, 애플리케이션에 콘솔 기반의 진입점(라우트와 비슷하게)을 정의합니다.

`channels.php` 파일은 애플리케이션에서 지원하는 모든 이벤트 브로드캐스팅 채널을 등록하는 파일입니다.

<a name="the-storage-directory"></a>
#### Storage 디렉토리

`storage` 디렉토리는 여러분의 컴파일된 블레이트 템플릿, 파일 기반의 세션, 파일 캐시 그리고 기타 프레임워크에서 생성된 파일들을 가지고 있습니다. 이 디렉토리는 그 안에 `app`, `framework` 그리고 `logs` 디렉토리로 구성되어 있습니다. `app` 디렉토리는 애플리케이션에 의해서 생성된 파일을 저장하는데 사용되어집니다. `framework` 디렉토리는 프레임워크가 생성한 파일들과 캐시를 저장하는데 사용됩니다. 마지막으로 `logs` 디렉토리는 애플리케이션의 로그 파일들을 가지고 있습니다.

`storage/app/public` 디렉토리는 공개적으로 엑세스가 가능해야 하는 프로필 아바타와 같은 사용자가 생성한 파일들에 사용할 수 있습니다. 여러분은 이 디렉토리를 가리키는 `public/storage` 심볼릭 링크를 생성해야 합니다. `php artisan storage:link` 명령어를 통해서 이 링크를 생성할 수 있습니다.

<a name="the-tests-directory"></a>
#### Tests 디렉토리

`tests` 디렉토리는 자동화된 테스트가 포함되어 있습니다. 별다른 설정 없이도 [PHPUnit](https://phpunit.de/) 테스트의 예제가 제공됩니다. 각각의 테스트 클래스는 `Test` 라는 단어가 붙어 있어야 합니다. `phpunit` 또는 `php vendor/bin/phpunit` 명령어를 통해서 테스트를 실행할 수 있습니다.

<a name="the-vendor-directory"></a>
#### Vendor 디렉토리

`vendor` 디렉토리는 [Composer](https://getcomposer.org)의 의존성 폴더 입니다.

<a name="the-app-directory"></a>
## App 디렉토리

애플리케이션의 대부분의 파일들은 `app` 디렉토리 안에 보관됩니다. 기본적으로 이 디렉토리는 [PSR-4 오토로딩 표준](https://www.php-fig.org/psr/psr-4/)를 사용하는 컴포저에 의해서 `App` 이라는 네임스페이스를 통해서 오토로딩됩니다.

`app` 디렉토리는 `Console`, `Http` 그리고 `Providers` 와 같은 다양한 추가 디렉토리를 가지고 있습니다. `Console` 과 `Http` 디렉토리는 애플리케이션의 코어에 API를 제공하고 있다고 생각할 수 있습니다. HTTP 프로토콜과 CLI 모두 애플리케이션과 상호작용하는 매커니즘이지만, 실제 애플리케이션 로직을 포함하지는 않습니다. 다시말해, 이것은 애플리케이션에 명령을 보내는 두가지 방법일 뿐입니다. `Console` 디렉토리는 모든 아티즌 명령어들을 포함하고, `Http` 디렉토리는 컨트롤러와 미들웨어, requests 들을 포함합니다.

다른 다양한 디렉토리들은 클래스들을 생성하기 위해서 `make` 아티즌 명령어를 실행할 때 `app` 디렉토리 내부에 생성됩니다. 따라서 예를들어, `app/Jobs` 디렉토리는 job 클래스를 생성하기 위해서 `make:job` 아티즌 명령어를 실행하기 전까지 존재하지 않습니다.

> {tip} `app` 디렉토리 안에 있는 많은 클래스들은 아티즌 명령어를 통해서 생성됩니다. 사용가능한 명령어들을 살펴보기 위해서는 터미널에서 `php artisan list make` 명령어를 실행하면 됩니다.

<a name="the-broadcasting-directory"></a>
#### Broadcasting 디렉토리

`Broadcasting` 디렉토리는 애플리케이션의 모든 브로드캐스트 채널 클래스를 포함하고 있습니다. 이 클래스는 `make:channel` 명령어를 통해서 생성될 수 있습니다. 이 디렉토리는 기본적으로 존재하지 않지만, 첫번째 채널 클래스가 생성될 때 만들어 집니다. 채널에 대한 보다 자세한 사항은 [이벤트 브로드캐스팅](/docs/{{version}}/broadcasting) 문서를 참고하십시오.

<a name="the-console-directory"></a>
#### Console 디렉토리

`Console` 디렉토리는 애플리케이션의 모든 사용자 아티즌 명령어들을 포함하고 있습니다. 이 명령어들은 `make:command` 명령어를 통해서 생성될 수 있습니다. 이 디렉토리는 또한, 사용자가 정의한 아티즌 명령어들의 [스케줄링 작업](/docs/{{version}}/scheduling)이 등록되어 있는 콘솔 커널이 저장되어 있는 디렉토리 입니다.

<a name="the-events-directory"></a>
#### Events 디렉토리

이 디렉토리는 기본적으로 존재하지 않지만 `event:generate`와 `make:event` 아티즌 명령으로 생성됩니다. `Events` 디렉토리에는 [events classes](/docs/{{version}}/events)가 있습니다. 이벤트는 애플리케이션의 다른 부분에 주어진 동작이 발생했음을 알리는 데 사용되며 많은 유연성과 디커플링을 제공합니다.

<a name="the-exceptions-directory"></a>
#### Exceptions 디렉토리

`Exceptions` 디렉토리는 애플리케이션의 예외-exception 핸들러를 포함하고 있으며, 또한 애플리케이션에서 발생하는 exception들을 배치하기 좋은 장소입니다. exception 이 어떻게 로깅되고, 렌더링 될지 커스터마이징 하고자 한다면, 이 디렉토리에 있는 `Handler` 클래스를 수정해야 합니다.

<a name="the-http-directory"></a>
#### Http 디렉토리

`Http` 디렉토리는 컨트롤러, 미들웨어 그리고 form request 를 가지고 있습니다. 애플리케이션으로 들어오는 request를 처리하는 대부분의 로직은 이 디렉토리에 위치하고 있습니다.

<a name="the-jobs-directory"></a>
#### Jobs 디렉토리

이 디렉토리는 기본적으로 존재하지 않지만, `make:job` 아티즌 명령어를 실행하는 경우 생성됩니다. `Jobs` 디렉토리는 애플리케이션의 [큐로 지정이 가능한 작업](/docs/{{version}}/queue)모아 놓은 곳입니다. jobs 는 애플리케이션에 의해서 큐를 통해 실생되거나 현재 request 라이프사이클 안에서 실행됩니다. 현재 request 안에서 동기적으로 실행되는 job은 [커맨드 패턴](https://en.wikipedia.org/wiki/Command_pattern)을 구현하고 있기 때문에 "command"라고 지칭하기도 합니다.

<a name="the-listeners-directory"></a>
#### Listeners 디렉토리

이 디렉토리는 기본적으로 존재하지 않지만, `event:generate` 나 `make:listener` 아티즌 명령어를 실행하는 경우 생성됩니다. `Listeners` 디렉토리는 [이벤트](/docs/{{version}}/events)를 처리하는 클래스들을 가지고 있습니다. 이벤트 리스너는 이벤트 인스턴스를 수신하여 발생한 이벤트에 대응하는 로직을 수행합니다. 예를 들어, `UserRegistered` 이벤트는 `SendWelcomeEmail` 리스너에 의해서 처리될 것입니다.

<a name="the-mail-directory"></a>
#### Mail 디렉토리

이 디렉토리는 기본적으로 존재하지 않지만, `make:mail` 아티즌 명령어를 실행하는 경우 생성됩니다. `Mail` 디렉토리는 애플리케이션에 의해서 발송되는 이메일을 나타내는 클래스들을 가지고 있습니다. 메일 객체는 `Mail::send` 메소드를 사용하여 보낼 수 있는 간단한 하나의 클래스를 통해서 이메일을 구성하는 로직을 캡슐화 합니다.

<a name="the-notifications-directory"></a>
#### Notifications 디렉토리

이 디렉토리는 기본적으로 존재하지 않지만, `make:notification` 아티즌 명령어를 실행하는 경우 생성됩니다. `Notifications` 디렉토리는 애플리케이션 안에서 발생하는 이벤트에 대한 간단한 알림으로, 애플리케이션에서 전송되는 "트랜잭션" 알림을 제공합니다. 라라벨의 알림 기능은 이메일, 슬랙, SMS와 같은 다양한 드라이버를 통해서 알림을 보내거나, 데이터베이스에 저장하는 추상화 기능입니다.

<a name="the-policies-directory"></a>
#### Policies 디렉토리

이 디렉토리는 기본적으로 존재하지 않지만, `make:policy` 아티즌 명령어를 실행하는 경우 생성됩니다. `Policies` 디렉토리는 애플리케이션의 authorization policy 클래스를 가지고 있습니다. Policies는 사용자가 특정 리소스에 대한 주어진 액션을 수행할 수 있는지를 결정하는데 사용됩니다. 보다 자세한 정보는 [authorization 문서](/docs/{{version}}/authorization)를 참고하십시오.

<a name="the-providers-directory"></a>
#### 프로바이더 디렉토리

`Providers` 디렉토리는 애플리케이션의 모든 [서비스 프로바이더](/docs/{{version}}/providers) 파일들을 가지고 있습니다. 서비스 프로바이더는 애플리케이션에서 필요한 서비스들을 컨테이너에 바인딩하고, 이벤트를 등록하고, 기타 애플리케이션이 유입된 request들을 처리하기 위해서 준비되어야 하는 작업들을 수행합니다.

라라벨을 새롭게 설치하면 이 디렉토리안에는 이미 몇몇 프로바이더들이 포함되어 있습니다. 필요에 따라 이 디렉토리에 여러분의 프로바이더를 자유롭게 추가할 수 있습니다.

<a name="the-rules-directory"></a>
#### Rules 디렉토리

이 디렉토리는 기본적으로 존재하지 않습니다만, `make:rule` 아티즌 명령어를 실행하게 된다면 생성됩니다. `Rules` 디렉토리는 애플리케이션에서 사용할 수 있는 커스텀 validation-유효성 검사를 위한 객체들을 포함하게 됩니다. Rules 은 복잡한 유효성 검사 로직을 간단한 객체로 캡슐화 하는데 사용됩니다. 보다 자세한 내용은 [validation 문서](/docs/{{version}}/validation).를 참고하십시오.
