# Laravel Envoy

- [시작하기](#introduction)
- [설치하기](#installation)
- [작업 작성하기](#writing-tasks)
  - [Defining Tasks](#defining-tasks)
  - [작업 정의](#defining-tasks)
  - [Multiple Servers](#multiple-servers)
  - [다중 서버](#multiple-servers)
  - [Setup](#setup)
  - [설정하기](#setup)
  - [Variables](#variables)
  - [변수들](#variables)
  - [Stories](#stories)
  - [스토리](#stories)
  - [Completion Hooks](#completion-hooks)
  - [완료 훅-Hook](#completion-hooks)
- [작업 실행하기](#running-tasks)
  - [Confirming Task Execution](#confirming-task-execution)
  - [실행여부 확인하기](#confirming-task-execution)
- [알림](#notifications)
  - [Slack](#slack)
  - [슬랙](#slack)
  - [Discord](#discord)
  - [디스코드](#discord)
  - [Telegram](#telegram)
  - [텔레그램](#telegram)
  - [Microsoft Teams](#microsoft-teams)
  - [마이크로소프트 팀즈](#microsoft-teams)

<a name="introduction"></a>
## 시작하기

[Laravel Envoy](https://github.com/laravel/envoy) 는 원격 서버에서 실행하는 일반적인 작업을 실행하기 위한 도구입니다. [Blade](/docs/{{version}}/blade) 스타일 구문을 사용하여 배포 작업, Artisan 명령 등을 쉽게 설정할 수 있습니다. 현재 Envoy는 Mac 및 Linux 운영 체제만 지원합니다. Windows는 [WSL2](https://docs.microsoft.com/en-us/windows/wsl/install-win10) 를 사용해서 처리할 수 있습니다.

<a name="installation"></a>
## 설치하기

먼저 Composer 패키지 관리자를 사용하여 프로젝트에 Envoy를 설치합니다.

    composer require laravel/envoy --dev

Envoy가 설치되면 애플리케이션의 `vendor/bin` 디렉토리에서 Envoy 바이너리를 사용할 수 있습니다.

    php vendor/bin/envoy

<a name="writing-tasks"></a>
## 작업 작성하기

<a name="defining-tasks"></a>
### 작업 정의

작업은 Envoy의 기본으로 만들어지는 단위입니다. 작업은 작업이 호출될 때 원격 서버에서 실행해야 하는 셸 명령을 정의합니다. 예를 들어, 애플리케이션의 모든 대기열 작업자 서버에서 `php artisan queue:restart` 명령을 실행하는 작업을 정의할 수 있습니다.

모든 Envoy 작업은 애플리케이션 루트의 `Envoy.blade.php` 파일에 작성해야 합니다. 다음은 시작하기 위한 예제입니다.

```bash
@servers(['web' => ['user@192.168.1.1'], 'workers' => ['user@192.168.1.2']])

@task('restart-queues', ['on' => 'workers'])
    cd /home/user/example.com
    php artisan queue:restart
@endtask
```

보시다시피 `@servers` 배열이 파일 상단에 작성한 후, 작업의 `on` 옵션을 통해 이 서버를 참조할 수 있습니다. `@servers` 정의는 항상 한 줄에 있어야 합니다. `@task` 선언 내에서 작업을 호출할 때 서버에서 실행되어야 하는 셸 명령을 작성해야 합니다.

<a name="local-tasks"></a>
#### 로컬 작업

서버의 IP 주소를 `127.0.0.1`로 지정하여 로컬 컴퓨터에서 스크립트를 강제 실행할 수 있습니다.

```bash
@servers(['localhost' => '127.0.0.1'])
```

<a name="importing-envoy-tasks"></a>
#### Envoy 작업 가져오기

`@import` 지시어를 사용하여 다른 Envoy 파일을 가져와서 스토리와 작업을 추가할 수 있습니다. 파일을 가져온 후 Envoy 파일에 정의된 것처럼 파일에 포함된 작업을 실행할 수 있습니다.

```bash
@import('vendor/package/Envoy.blade.php')
```

<a name="multiple-servers"></a>
### 다중 서버

Envoy를 사용하면 여러 서버에서 작업을 쉽게 실행할 수 있습니다. 먼저 `@servers` 선언에 추가 서버를 추가합니다. 각 서버에는 고유한 이름을 지정해야 합니다. 추가 서버를 정의했으면 작업의 `on` 배열에 각 서버를 추가할 수 있습니다.

```bash
@servers(['web-1' => '192.168.1.1', 'web-2' => '192.168.1.2'])

@task('deploy', ['on' => ['web-1', 'web-2']])
    cd /home/user/example.com
    git pull origin {{ $branch }}
    php artisan migrate --force
@endtask
```

<a name="parallel-execution"></a>
#### 병렬 실행

기본적으로 작업은 각 서버에서 순차적으로 실행됩니다. 즉, 작업은 두 번째 서버에서 실행을 계속하기 전에 첫 번째 서버에서 실행을 완료합니다. 여러 서버에서 작업을 병렬로 실행하려면 작업 선언에 `parallel` 옵션을 추가하세요.

```bash
@servers(['web-1' => '192.168.1.1', 'web-2' => '192.168.1.2'])

@task('deploy', ['on' => ['web-1', 'web-2'], 'parallel' => true])
    cd /home/user/example.com
    git pull origin {{ $branch }}
    php artisan migrate --force
@endtask
```

<a name="setup"></a>
### 설정하기

때로는 Envoy 작업을 실행하기 전에 임의의 PHP 코드를 실행해야 할 수도 있습니다. `@setup` 지시문을 사용하여 작업 전에 실행해야 하는 PHP 코드 블록을 정의할 수 있습니다.

```php
@setup
    $now = new DateTime;
@endsetup
```

작업을 실행하기 전에 다른 PHP 파일이 필요한 경우 `Envoy.blade.php` 파일 상단에 `@include` 지시문을 사용할 수 있습니다.

```bash
@include('vendor/autoload.php')

@task('restart-queues')
    # ...
@endtask
```

<a name="variables"></a>
### 변수들

필요한 경우 Envoy를 호출할 때 명령줄을 통해 Envoy 작업에 인수를 전달할 수 있습니다.

    php vendor/bin/envoy run deploy --branch=master

Blade의 "echo" 구문을 사용하여 작업 내의 옵션을 사용할 수 있습니다. 작업 내에서 Blade `if` 문과 루프를 작성할 수도 있습니다. 예를 들어 `git pull` 명령을 실행하기 전에 `$branch` 변수가 있는지 확인 후 실행 할 수 있습니다.

```bash
@servers(['web' => ['user@192.168.1.1']])

@task('deploy', ['on' => 'web'])
    cd /home/user/example.com

    @if ($branch)
        git pull origin {{ $branch }}
    @endif

    php artisan migrate --force
@endtask
```

<a name="stories"></a>
### 스토리

스토리는 손쉽게 작업들을 한가지 이름으로 그룹화해줍니다. 예를 들어 `deploy` 스토리는 정의 내에 작업 이름을 나열하여 `update-code` 및 `install-dependencies` 작업을 실행할 수 있습니다.

```bash
@servers(['web' => ['user@192.168.1.1']])

@story('deploy')
    update-code
    install-dependencies
@endstory

@task('update-code')
    cd /home/user/example.com
    git pull origin master
@endtask

@task('install-dependencies')
    cd /home/user/example.com
    composer install
@endtask
```

스토리가 작성되면 작업을 호출하는 것과 같은 방식으로 스토리를 호출할 수 있습니다.

    php vendor/bin/envoy run deploy

<a name="completion-hooks"></a>
### 완료 훅-hook

작업과 스토리가 완료되면 여러 훅-hook이 실행됩니다. Envoy에서 지원하는 훅 유형은 `@after`, `@error`, `@success` 및 `@finished`입니다. 이 훅의 모든 코드는 PHP로 해석되고 작업이 실행되는 원격 서버가 아닌 로컬에서 실행됩니다.

이러한 각 훅을 원하는 만큼 정의할 수 있습니다. Envoy 스크립트에 작성한 순서대로 실행됩니다.

<a name="completion-after"></a>
#### `@after`

각 작업 실행 후 Envoy 스크립트에 등록된 모든 `@after` 훅이 실행됩니다. `@after` 훅은 실행된 작업의 이름을 전달받습니다.

```php
@after
    if ($task === 'deploy') {
        // ...
    }
@endafter
```

<a name="completion-error"></a>
#### `@error`

모든 작업 실패(`0`보다 큰 상태 코드로 종료) 후 Envoy 스크립트에 등록된 모든 `@error` 훅이 실행됩니다. `@error` 훅은 실행된 작업의 이름을 전달받습니다.

```php
@error
    if ($task === 'deploy') {
        // ...
    }
@enderror
```

<a name="completion-success"></a>
#### `@success`

모든 작업이 오류 없이 실행되면 Envoy 스크립트에 등록된 모든 `@success` 훅이 실행됩니다.

```bash
@success
    // ...
@endsuccess
```

<a name="completion-finished"></a>
#### `@finished`

모든 작업이 실행된 후(종료 상태에 관계없이) 모든 `@finished` 훅이 실행됩니다. `@finished` 훅은 완료된 작업의 상태 코드를 수신합니다. 이 코드는 `null`이거나 `0`보다 크거나 같은 `integer`일 수 있습니다.

```bash
@finished
    if ($exitCode > 0) {
        // There were errors in one of the tasks...
    }
@endfinished
```

<a name="running-tasks"></a>
## 작업 실행하기

애플리케이션의 `Envoy.blade.php` 파일에 정의된 작업이나 스토리를 실행하려면 Envoy의 `run` 명령에 실행하려는 작업이나 스토리의 이름을 같이 입력합니다. Envoy는 작업을 실행하고 작업이 실행될 때 원격 서버의 출력을 표시합니다.

    php vendor/bin/envoy run deploy

<a name="confirming-task-execution"></a>
### 작업 실행 확인하기

서버에 특정 작업을 실행하기 전에 확인 메시지를 추가하고 싶은 경우에는, 작업 실행 부분에 `confirm` 지시어를 사용할 수 있습니다. 이 옵션은 위험한 작업을 할 경우에 특히 유용합니다.

```bash
@task('deploy', ['on' => 'web', 'confirm' => true])
    cd /home/user/example.com
    git pull origin {{ $branch }}
    php artisan migrate
@endtask
```

<a name="notifications"></a>
## 알림

<a name="slack"></a>
### 슬랙

Envoy는 각 작업이 실행된 후 [Slack](https://slack.com) 에 알림 보내기를 지원합니다. `@slack` 지시문은 Slack 훅 URL과 채널 사용자 이름을 입력받습니다. Slack 제어판에서 "Incoming WebHooks"을 생성하여 웹훅 URL을 만들 수 있습니다.

전체 웹훅 URL을 `@slack` 지시문에 지정된 첫 번째 인수로 전달해야 합니다. `@slack` 지시문에 지정된 두 번째 인수는 채널 이름(`channel`) 또는 사용자 이름(`@user`)이어야 합니다.

    @finished
        @slack('webhook-url', '#bots')
    @endfinished

기본적으로 Envoy 알림은 실행된 작업을 설명하는 메시지를 알림 채널로 보냅니다. 그러나 `@slack` 지시문에 세 번째 인수를 전달하여 이 메시지를 사용자 정의 메시지로 덮어쓸 수 있습니다.

    @finished
        @slack('webhook-url', '#bots', 'Hello, Slack.')
    @endfinished

<a name="discord"></a>
### 디스코드

Envoy는 또한 각각의 작업이 실행된 후에 [디스코드](https://discord.com)를 통해서 알림을 보내는 작업을 지원합니다. `@discord` 지시어는 디스코드 Hook URL 과 메시지를 입력받습니다. 서버 설정에서 "Webhook" 을 만들고 웹훅을 게시할 채널을 선택할 수 있습니다. 여러분은 전체 웹훅 URL 을 `@discord` 지시어에 전달하면 됩니다.

    @finished
        @discord('discord-webhook-url')
    @endfinished

<a name="telegram"></a>
### 텔레그램

Envoy는 각 작업이 실행 된 후 [Telegram](https://telegram.org)에 알림을 보내는 것도 지원합니다. `@telegram` 지시문은 Telegram Bot ID와 Chat ID를 입력받습니다. [BotFather](https://t.me/botfather) 를 사용하여 새 봇을 생성하여 Bot ID를 검색 할 수 있습니다. [@username_to_id_bot](https://t.me/username_to_id_bot) 을 사용하여 유효한 채팅 ID를 검색 할 수 있습니다. 전체 봇 ID와 채팅 ID를 `@telegram` 지시문에 전달해야합니다.

    @finished
        @telegram('bot-id','chat-id')
    @endfinished

<a name="microsoft-teams"></a>
### 마이크로소프트 팀즈

Envoy는 또한 각 작업이 실행된 후 [Microsoft Teams](https://www.microsoft.com/en-us/microsoft-teams) 로 알림 보내기를 지원합니다. `@microsoftTeams` 지시문은 Teams Webhook(필수), 메시지, 테마 색상(성공, 정보, 경고, 오류) 및 옵션 배열을 입력받습니다. 새 [수신 웹훅](https://docs.microsoft.com/en-us/microsoftteams/platform/webhooks-and-connectors/how-to/add-incoming-webhook) 을 만들어 Teams 웹북을 검색할 수 있습니다. Teams API에는 제목, 요약 및 섹션과 같은 메시지 상자를 사용자 지정할 수 있는 다른 많은 속성이 있습니다. [Microsoft Teams 문서](https://docs.microsoft.com/en-us/microsoftteams/platform/webhooks-and-connectors/how-to/connectors-using?tabs=cURL#example-of-connector-message) 에서 자세한 정보를 찾을 수 있습니다. 전체 Webhook URL을 `@microsoftTeams` 지시문에 전달해야 합니다.

    @finished
        @microsoftTeams('webhook-url')
    @endfinished
