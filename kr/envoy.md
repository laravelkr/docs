# Laravel Envoy
# Laravel Envoy

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [Installation](#installation)
    - [설치하기](#installation)
- [Writing Tasks](#writing-tasks)
- [작업 작성하기](#writing-tasks)
    - [Setup](#setup)
    - [설정하기](#setup)
    - [Variables](#variables)
    - [변수들](#variables)
    - [Stories](#stories)
    - [스토리](#stories)
    - [Multiple Servers](#multiple-servers)
    - [여러개의 서버](#multiple-servers)
- [Running Tasks](#running-tasks)
- [작업 실행하기](#running-tasks)
    - [Confirming Task Execution](#confirming-task-execution)
    - [실행여부 확인하기](#confirming-task-execution)
- [Notifications](#notifications)
- [알림](#notifications)
    - [Slack](#slack)
    - [슬랙](#slack)
    - [Discord](#discord)
    - [디스코드](#discord)

<a name="introduction"></a>
## Introduction
## 시작하기

[Laravel Envoy](https://github.com/laravel/envoy) provides a clean, minimal syntax for defining common tasks you run on your remote servers. Using Blade style syntax, you can easily setup tasks for deployment, Artisan commands, and more. Currently, Envoy only supports the Mac and Linux operating systems.

[라라벨 Envoy](https://github.com/laravel/envoy)는 원격 서버에서 정의된 공통의 작업을 수행하기 위한 깔끔하고 간결한 문법을 제공합니다. 블레이드 스타일의 문법을 사용하여 손쉽게 배포, 아티즌 명령어 등의 작업을 설정할 수 있습니다. 현재 Envoy 는 맥과 리눅스 OS를 지원하고 있습니다.

<a name="installation"></a>
### Installation
### 설치하기

First, install Envoy using the Composer `global require` command:

먼저 Composer의 `global require` 명령어를 통해서 Envoy를 설치합니다.

    composer global require laravel/envoy

Since global Composer libraries can sometimes cause package version conflicts, you may wish to consider using `cgr`, which is a drop-in replacement for the `composer global require` command. The `cgr` library's installation instructions can be [found on GitHub](https://github.com/consolidation-org/cgr).

글로벌 Composer 라이브러리는 때로는 패키지 버전의 충돌을 일으키기 때문에 `composer global require` 명령의 대안으로 `cgr`사용을 고려할 수도 있습니다. `cgr` 라이브러리의 설치 방법은 [GitHub](https://github.com/consolidation-org/cgr)에서 찾을 수 있습니다.

> {note} Make sure to place the `~/.composer/vendor/bin` directory in your PATH so the `envoy` executable is found when running the `envoy` command in your terminal.

> {note} `~/.composer/vendor/bin` 디렉토리를 여러분의 PATH에 추가하여 터미널에서 `envoy` 명령어를 실행할 때 `envoy`를 바로 찾을 수 있도록 설정하십시오.

#### Updating Envoy
#### Envoy 업데이트 하기

You may also use Composer to keep your Envoy installation up to date. Issuing the `composer global update` command will update all of your globally installed Composer packages:

설치 한 Envoy를 업데이트 할 때도 Composer를 사용합니다. `composer global update` 명령어는 설치된 글로벌 Composer 패키지를 업데이트 할 것입니다.

    composer global update

<a name="writing-tasks"></a>
## Writing Tasks
## 작업 작성하기

All of your Envoy tasks should be defined in an `Envoy.blade.php` file in the root of your project. Here's an example to get you started:

모든 Envoy 작업들은 프로젝트 루트 디렉토리에 있는 `Envoy.blade.php` 파일에 정의되어 있어야 합니다. 다음은 간단한 예제 입니다.

    @servers(['web' => ['user@192.168.1.1']])

    @task('foo', ['on' => 'web'])
        ls -la
    @endtask

As you can see, an array of `@servers` is defined at the top of the file, allowing you to reference these servers in the `on` option of your task declarations. Within your `@task` declarations, you should place the Bash code that should run on your server when the task is executed.

위에서 보시다 시피 `@servers` 배열이 파일의 최상단에 정의되어 작업을 지정할 때 `on` 옵션을 사용할 때 해당 서버를 참조할 수 있습니다. `@task` 선언 안에서 여러분은 작업이 실행될 때 서버에서 구동시킬 Bash 코드를 작성해야 합니다.

You can force a script to run locally by specifying the server's IP address as `127.0.0.1`:

로컬에서 실행될 스크립트는 서버의 IP 주소를 `127.0.0.1` 로 지정할 수 있습니다.

    @servers(['localhost' => '127.0.0.1'])

<a name="setup"></a>
### Setup
### 설정하기

Sometimes, you may need to execute some PHP code before executing your Envoy tasks. You may use the `@setup` directive to declare variables and do other general PHP work before any of your other tasks are executed:

때로는 Envoy 작업이 실행되기 전에 어떤 PHP 코드를 실행시켜야 될 필요가 있을 수 있습니다. 이 경우 다른 작업들이 실해되기 전에 `@setup` 지시어를 통해서 변수를 선언하고 다른 일반적인 PHP 작업을 실행할 수 있습니다.

    @setup
        $now = new DateTime();

        $environment = isset($env) ? $env : "testing";
    @endsetup

If you need to require other PHP files before your task is executed, you may use the `@include` directive at the top of your `Envoy.blade.php` file:

만약 작업이 실행되기 전에 다른 PHP 파일을 포함시켜야할 필요가 있다면, `Envoy.blade.php` 파일의 상단에 `@include` 지시어를 사용할 수 있습니다.

    @include('vendor/autoload.php')

    @task('foo')
        # ...
    @endtask

You may also import other Envoy files so their stories and tasks are added to yours. After they have been imported, you may execute the tasks in those files as if they were defined in your own. You should use the `@import` directive at the top of your `Envoy.blade.php` file:

다른 Envoy 파일을 가져 와서 스토리와 작업을 추가 할 수도 있습니다. 파일을 가져온 후에는 마치 자신이 정의한 것처럼 해당 파일에서 작업을 실행할 수 있습니다. Envoy.blade.php 파일 상단에 `@import` 지시문을 사용해야 합니다.

    @import('package/Envoy.blade.php')

<a name="variables"></a>
### Variables
### 변수들

If needed, you may pass option values into Envoy tasks using the command line:

필요한 경우 커맨드라인을 사용하여 Envoy 작업에 옵션 값을 전달할 수 있습니다.

    envoy run deploy --branch=master

You may access the options in your tasks via Blade's "echo" syntax. You may also use `if` statements and loops within your tasks. For example, let's verify the presence of the `$branch` variable before executing the `git pull` command:

블래이드의 "echo" 문법을 통해서 작업의 옵션에 엑세스가 가능합니다. 작업의 반복문 안에서 `if` 구문을 사용할 수도 있습니다. 예를 들어 `git pull` 명령을 실행하기 전에 `$branch` 변수가 존재하는지 확인해 보겠습니다.

    @servers(['web' => '192.168.1.1'])

    @task('deploy', ['on' => 'web'])
        cd site

        @if ($branch)
            git pull origin {{ $branch }}
        @endif

        php artisan migrate
    @endtask

<a name="stories"></a>
### Stories
### 스토리

Stories group a set of tasks under a single, convenient name, allowing you to group small, focused tasks into large tasks. For instance, a `deploy` story may run the `git` and `composer` tasks by listing the task names within its definition:

스토리는 하나의 편리한 이름으로 그룹을 묶어 작고 개별적인 작업들을 큰 작업을 구성합니다. 하나의 예제로, `deploy` 스토리는 그 정의 안에서 작업의 이름들을 나열함으로써 `git` 과 `composer` 작업들을 실행할 수 있습니다.

    @servers(['web' => '192.168.1.1'])

    @story('deploy')
        git
        composer
    @endstory

    @task('git')
        git pull origin master
    @endtask

    @task('composer')
        composer install
    @endtask

Once the story has been written, you may run it just like a typical task:

스토리가 작성되고 나면, 일반적인 작업처럼 실행할 수 있습니다.

    envoy run deploy

<a name="multiple-servers"></a>
### Multiple Servers
### 여러대의 서버

Envoy allows you to easily run a task across multiple servers. First, add additional servers to your `@servers` declaration. Each server should be assigned a unique name. Once you have defined your additional servers, list each of the servers in the task's `on` array:

Envoy 는 여러분이 여러대의 서버에 서 손쉽게 작업이 실행할 수 있게 해줍니다. 먼저 `@servers` 선언부분에 추가 서버를 더합니다. 각각의 서버는 고유한 이름을 가져야 합니다. 추가적인 서버들을 정의하고 나면, 작업의 `on` 배열안에서 각각의 서버들을 나열하면 됩니다.

    @servers(['web-1' => '192.168.1.1', 'web-2' => '192.168.1.2'])

    @task('deploy', ['on' => ['web-1', 'web-2']])
        cd site
        git pull origin {{ $branch }}
        php artisan migrate
    @endtask

#### Parallel Execution
#### 병렬 실행

By default, tasks will be executed on each server serially. In other words, a task will finish running on the first server before proceeding to execute on the second server. If you would like to run a task across multiple servers in parallel, add the `parallel` option to your task declaration:

기본적으로, 작업들은 각각의 서버에서 순차적으로 실행될 것입니다. 다시말해 첫번째 서버에서 하나의 작업이 실행을 마치면, 두번째 서버에서 실행이 됩니다. 만약 여러대의 서버에서 작업들을 병렬로 실행되게 하려면 작업 선언 부분에 `parallel` 옵션을 추가하면 됩니다.

    @servers(['web-1' => '192.168.1.1', 'web-2' => '192.168.1.2'])

    @task('deploy', ['on' => ['web-1', 'web-2'], 'parallel' => true])
        cd site
        git pull origin {{ $branch }}
        php artisan migrate
    @endtask

<a name="running-tasks"></a>
## Running Tasks
## 작업 실행하기

To run a task or story that is defined in your `Envoy.blade.php` file, execute Envoy's `run` command, passing the name of the task or story you would like to execute. Envoy will run the task and display the output from the servers as the task is running:

`Envoy.blade.php` 파일에 정의되어 있는 작업이나 스토리를 실행하려면 실행하고자 하는 작업 또는 스토리의 이름을 Envoy의 `run` 명령어를 전달하여 실행하면 됩니다. Envoy 는 작업을 실행하고 작업이 실행된 서버에서 그 결과를 표시할 것입니다.

    envoy run deploy

<a name="confirming-task-execution"></a>
### Confirming Task Execution
### 작업 실행 확인하기

If you would like to be prompted for confirmation before running a given task on your servers, you should add the `confirm` directive to your task declaration. This option is particularly useful for destructive operations:

서버에 특정 작업을 실행하기 전에 확인 메시지를 추가하고 싶은 경우에는, 작업 실행 부분에 `confirm` 지시어를 사용할 수 있습니다. 이 옵션은 위험한 작업을 할 경우에 특히 유용합니다.

    @task('deploy', ['on' => 'web', 'confirm' => true])
        cd site
        git pull origin {{ $branch }}
        php artisan migrate
    @endtask

<a name="notifications"></a>
<a name="hipchat-notifications"></a>
## Notifications
## 알림

<a name="slack"></a>
### Slack
### 슬랙

Envoy also supports sending notifications to [Slack](https://slack.com) after each task is executed. The `@slack` directive accepts a Slack hook URL and a channel name. You may retrieve your webhook URL by creating an "Incoming WebHooks" integration in your Slack control panel. You should pass the entire webhook URL into the `@slack` directive:

Envoy 는 또한 각각의 작업이 실행된 후에 [슬랙](https://slack.com)을 통해서 알림을 보내는 작업을 지원합니다. `@slack` 지시어는 슬랙 Hook URL 과 채널 이름을 인자로 받습니다. Slack 의 설정 패널의 `Incoming WebHooks` 통합 페이지에서 후킹 URL을 생성하여 찾을 수 있습니다. 여러분은 전체 웹훅 URL 을 `@slack` 지시어에 전달하면 됩니다.

    @finished
        @slack('webhook-url', '#bots')
    @endfinished

You may provide one of the following as the channel argument:

채널 인자는 다음 중 하나를 지정할 수 있습니다.


- To send the notification to a channel: `#channel`
- To send the notification to a user: `@user`



- 채널로 알림을 보낼 때: `#채널`
- 유저에게 알림을 보낼 때: `@유저`


In addition you can also send Slack updates for specific tasks so you get the context of which task was run. You can do this by adding the `@slack` directive inside the `@task` directive:

또한 특정 작업에 대해 Slack에 업데이트를 전송하여 실행 된 작업의 컨텍스트를 얻을 수 있습니다. `@task` 지시문 안에 `@slack` 지시문을 추가하면 됩니다.

    @task('deploy', ['on' => 'web', 'confirm' => true])
        cd site
        git pull origin {{ $branch }}
        php artisan migrate

        @slack('webhook-url', '#deployments')
    @endtask

<a name="discord"></a>
### Discord
### 디스코드

Envoy also supports sending notifications to [Discord](https://discord.com) after each task is executed. The `@discord` directive accepts a Discord hook URL and a message. You may retrieve your webhook URL by creating a "Webhook" in your Server Settings and choosing which channel the webhook should post to. You should pass the entire Webhook URL into the `@discord` directive:

Envoy 는 또한 각각의 작업이 실행된 후에 [디스코드](https://discord.com)를 통해서 알림을 보내는 작업을 지원합니다.`@discord` 지시어는 디스코드 Hook URL 과 메시지를 수신합니다. 서버 설정에서 "Webhook" 을 만들고 웹훅을 게시할 채널을 선택할 수 있습니다. 여러분은 전체 웹훅 URL 을 `@discord` 지시어에 전달하면 됩니다.

    @finished
        @discord('discord-webhook-url')
    @endfinished

In addition you can also send Discord updates for specific tasks so you get the context of which task was run. You can do this by adding the `@discord` directive inside the `@task` directive:

또한 특정 작업에 대해 Discord에 업데이트를 전송하여 실행 된 작업의 컨텍스트를 얻을 수 있습니다. `@task` 지시문 안에 `@discord` 지시문을 추가하면됩니다 :

    @task('deploy', ['on' => 'web', 'confirm' => true])
        cd site
        git pull origin {{ $branch }}
        php artisan migrate

        @discord('discord-webhook-url')
    @endtask
