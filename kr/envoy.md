# Envoy Task Runner

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Writing Tasks](#writing-tasks)
- [작업 작성하기](#writing-tasks)
    - [Task Variables](#task-variables)
    - [작업 변수들](#task-variables)
    - [Multiple Servers](#envoy-multiple-servers)
    - [여러개의 서버](#envoy-multiple-servers)
    - [Task Macros](#envoy-task-macros)
    - [작업 매크로](#envoy-task-macros)
- [Running Tasks](#envoy-running-tasks)
- [작업 실행하기](#envoy-running-tasks)
- [Notifications](#envoy-notifications)
- [알림](#envoy-notifications)
    - [HipChat](#hipchat)
    - [HipChat](#hipchat)
    - [Slack](#slack)
    - [Slack](#slack)

<a name="introduction"></a>
## Introduction
## 시작하기

[Laravel Envoy](https://github.com/laravel/envoy) provides a clean, minimal syntax for defining common tasks you run on your remote servers. Using a Blade style syntax, you can easily setup tasks for deployment, Artisan commands, and more. Currently, Envoy only supports the Mac and Linux operating systems.

라라벨 Envoy는 원격 서버에서 정의된 공통의 작업을 수행하기 위한 깔끔하고 간결한 문법을 제공합니다. 블레이드 스타일의 문법을 사용하여 손쉽게 배포, 아티즌 명령어 등의 작업을 설정할 수 있습니다. 현재 Envoy 는 맥과 리눅스 OS를 지원하고 있습니다.

<a name="envoy-installation"></a>
### Installation
### 설치

First, install Envoy using the Composer `global` command:

먼저 Composer의 `global` 명령어를 통해서 Envoy를 설치합니다. 

    composer global require "laravel/envoy=~1.0"

Make sure to place the `~/.composer/vendor/bin` directory in your PATH so the `envoy` executable is found when you run the `envoy` command in your terminal.

`~/.composer/vendor/bin` 디렉토리를 여러분의 PATH에 추가하여 터미널에서 `envoy` 명령어를 실행할 때 `envoy`를 바로 찾을 수 있도록 설정하십시오. 

#### Updating Envoy
#### Envoy 업데이트 하기

You may also use Composer to keep your Envoy installation up to date:

설치 한 Envoy를 업데이트 할 때도 Composer를 사용합니다.

    composer global update

<a name="writing-tasks"></a>
## Writing Tasks
## 작업 작성하기

All of your Envoy tasks should be defined in an `Envoy.blade.php` file in the root of your project. Here's an example to get you started:

모든 Envoy 작업들은 프로젝트 루트 디렉토리에 있는 `Envoy.blade.php` 파일에 정의되어 있어야 합니다. 다음은 간단한 예제 입니다. 

    @servers(['web' => 'user@192.168.1.1'])

    @task('foo', ['on' => 'web'])
        ls -la
    @endtask

As you can see, an array of `@servers` is defined at the top of the file, allowing you to reference these servers in the `on` option of your task declarations. Within your `@task` declarations, you should place the Bash code that will be run on your server when the task is executed.

위에서 보시다 시피 `@servers` 배열이 파일의 최상단에 정의되어 작업을 지정할 때 `on` 옵션을 사용할 때 해당 서버를 참조할 수 있습니다. `@task` 선언 안에서 여러분은 작업이 실행될 때 구동시킬 Bash 코드를 작성해야 합니다. 

#### Bootstrapping
#### 부트스트래핑

Sometimes, you may need to execute some PHP code before evaluating your Envoy tasks. You may use the ```@setup``` directive to declare variables and do general PHP work inside the Envoy file:

때로는 Envoy 작업이 시작되기 전에 어떤 PHP 코드를 실행시켜야 될 필요가 있을 수도 있습니다. 이 경우 ```@setup``` 지시어를 사용하여 Envoy 파일에서 변수 선언을하거나 일반적인 PHP 코드를 실행시킬 수 있습니다.

    @setup
        $now = new DateTime();

        $environment = isset($env) ? $env : "testing";
    @endsetup

You may also use ```@include``` to include any outside PHP files:

외부의 PHP 파일을 인클루드 하기 위해 ```@include```를 이용할 수도 있습니다. 

    @include('vendor/autoload.php')

#### Confirming Tasks
#### 작업을 실행 확인하기

If you would like to be prompted for confirmation before running a given task on your servers, you may add the `confirm` directive to your task declaration:

서버에 특정 작업을 실행하기 전에 확인 메시지를 추가하고 싶은 경우에는, 작업 실행 부분에 `confirm` 지시어를 사용할 수 있습니다 :

    @task('deploy', ['on' => 'web', 'confirm' => true])
        cd site
        git pull origin {{ $branch }}
        php artisan migrate
    @endtask

<a name="task-variables"></a>
### Task Variables
### 작업 변수

If needed, you may pass variables into the Envoy file using command line switches, allowing you to customize your tasks:

    envoy run deploy --branch=master

You may use the options in your tasks via Blade's "echo" syntax:

    @servers(['web' => '192.168.1.1'])

    @task('deploy', ['on' => 'web'])
        cd site
        git pull origin {{ $branch }}
        php artisan migrate
    @endtask

<a name="envoy-multiple-servers"></a>
### Multiple Servers
### 여러대의 서버

You may easily run a task across multiple servers. First, add additional servers to your `@servers` declaration. Each server should be assigned a unique name. Once you have defined your additional servers, simply list the servers in the task declaration's `on` array:

손쉽게 여러서버에서 작업을 실행시킬 수 있습니다. 먼저 `@servers` 선언부분에 추가 서버를 더합니다. 각각의 서버는 고유한 이름을 가져야 합니다. 추가 서버를 선언한 뒤에는 작업 선언부분의 `on` 배열에 서버 목록을 기입하면 됩니다. 

    @servers(['web-1' => '192.168.1.1', 'web-2' => '192.168.1.2'])

    @task('deploy', ['on' => ['web-1', 'web-2']])
        cd site
        git pull origin {{ $branch }}
        php artisan migrate
    @endtask

By default, the task will be executed on each server serially. Meaning, the task will finish running on the first server before proceeding to execute on the next server.

기본적으로 작업들은 각 서버에서 순차적으로 실행됩니다. 즉, 첫번째 서버에서 실행이 완료되면 다음 서버에서 작업이 실행됩니다. 

#### Parallel Execution
#### 병렬 실행

If you would like to run a task across multiple servers in parallel, add the `parallel` option to your task declaration:

여러 서버에서 동시에 작업을 수행하려면, 작업 선언 부분에 `parallel` 옵션을 지정하십시오.

    @servers(['web-1' => '192.168.1.1', 'web-2' => '192.168.1.2'])

    @task('deploy', ['on' => ['web-1', 'web-2'], 'parallel' => true])
        cd site
        git pull origin {{ $branch }}
        php artisan migrate
    @endtask

<a name="envoy-task-macros"></a>
### Task Macros
### 작업 매크로

Macros allow you to define a set of tasks to be run in sequence using a single command. For instance, a `deploy` macro may run the `git` and `composer` tasks:

매크로는 하나의 명령으로 순차적으로 실행하는 일련의 작업을 정의합니다. 예를 들어, `deploy` 매크로가 `git` 과 `composer` 작업을 수행한다고 하겠습니다.

    @servers(['web' => '192.168.1.1'])

    @macro('deploy')
        git
        composer
    @endmacro

    @task('git')
        git pull origin master
    @endtask

    @task('composer')
        composer install
    @endtask

Once the macro has been defined, you may run it via single, simple command:

매크로가 정의되면, 단순하게 하나의 명령어로 실행이 됩니다. 

    envoy run deploy

<a name="envoy-running-tasks"></a>
## Running Tasks
## 작업 실행하기

To run a task from your `Envoy.blade.php` file, execute Envoy's `run` command, passing the command the name of the task or macro you would like to execute. Envoy will run the task and display the output from the servers as the task is running:

`Envoy.blade.php` 파일에 대한 작업을 실해하기 위해서는 Envoy의 `run` 명령어에 실행하고자 하는 작업 또는 매크로의 이름을 전달하며 실행하면 됩니다. Envoy 는 서버에서 작업이 실행되면서 표시되는 결과를 화면에 출력할 것입니다. 

    envoy run task

<a name="envoy-notifications"></a>
<a name="envoy-hipchat-notifications"></a>
## Notifications
## 알림

<a name="hipchat"></a>
### HipChat
### HipChat

After running a task, you may send a notification to your team's HipChat room using Envoy's `@hipchat` directive. The directive accepts an API token, the name of the room, and the username to be displayed as the sender of the message:

`@hipchat` 지시어를 사용하여 작업을 수행 후, 여러분의 팀의 HipChat 룸에 알림을 보낼 수 있습니다. 이 지시어는 API 토큰, 채팅룸의 이름, 그리고 메세지를 보내는 사용자로 표시되는 이름을 인자로 받습니다. 

    @servers(['web' => '192.168.1.1'])

    @task('foo', ['on' => 'web'])
        ls -la
    @endtask

    @after
        @hipchat('token', 'room', 'Envoy')
    @endafter

If you wish, you may also pass a custom message to send to the HipChat room. Any variables available to your Envoy tasks will also be available when constructing the message:

원하는 경우, HipChat 에 원하는 사용자 메세지를 전달할 수도 있습니다. 사용자 메세지를 구성하기 위해서 Envoy 작업의 어떤 변수라도 사용할 수 있습니다. 

    @after
        @hipchat('token', 'room', 'Envoy', "{{ $task }} ran in the {{ $env }} environment.")
    @endafter

<a name="slack"></a>
### Slack
### Slack

In addition to HipChat, Envoy also supports sending notifications to [Slack](https://slack.com). The `@slack` directive accepts a Slack hook URL, a channel name, and the message you wish to send to the channel:

HipChat 에 더하여 Envoy 는 [Slack](https://slack.com)에 알림을 보낼 수도 있습니다. `@slack` 지시어는 Slack 후킹 URL, 채널의 이름, 그리고 테널에 보내고자 하는 메세지를 인자로 받습니다. 

    @after
        @slack('hook', 'channel', 'message')
    @endafter

You may retrieve your webhook URL by creating an `Incoming WebHooks` integration on Slack's website. The `hook` argument should be the entire webhook URL provided by the Incoming Webhooks Slack Integration. For example:

Slack 의 웹사이트에 `Incoming WebHooks` 페이지에서 후킹 URL을 찾을 수 있습니다. `hook` 인자는 Slack 의 Incoming Webhooks 에서 제공되는 후킹 전체 URL 이어야 합니다. 다음은 그 예제 입니다:

    https://hooks.slack.com/services/ZZZZZZZZZ/YYYYYYYYY/XXXXXXXXXXXXXXX

You may provide one of the following as the channel argument:

채널 인자는 다음 중 하나를 지정할 수 있습니다.

- To send the notification to a channel: `#channel`
- To send the notification to a user: `@user`
