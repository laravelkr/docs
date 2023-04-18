# Artisan Console

- [Artisan Console](#artisan-console)
  - [Introduction](#introduction)
  - [시작하기](#시작하기)
      - [Laravel Sail](#laravel-sail)
      - [Laravel Sail](#laravel-sail-1)
    - [Tinker (REPL)](#tinker-repl)
    - [Tinker (REPL)](#tinker-repl-1)
      - [Installation](#installation)
      - [설치](#설치)
      - [Usage](#usage)
      - [사용법](#사용법)
      - [Command Allow List](#command-allow-list)
      - [명령어 허용 목록](#명령어-허용-목록)
      - [Classes That Should Not Be Aliased](#classes-that-should-not-be-aliased)
      - [별칭을 지정해서는 안되는 클래스](#별칭을-지정해서는-안되는-클래스)
  - [Writing Commands](#writing-commands)
  - [명령어 작성](#명령어-작성)
    - [Generating Commands](#generating-commands)
    - [명령어 생성](#명령어-생성)
    - [Command Structure](#command-structure)
    - [명령어 구조](#명령어-구조)
    - [Closure Commands](#closure-commands)
    - [클로저 명령어](#클로저-명령어)
      - [Type-Hinting Dependencies](#type-hinting-dependencies)
      - [타입힌팅 의존성](#타입힌팅-의존성)
      - [Closure Command Descriptions](#closure-command-descriptions)
      - [클로저 명령어 설명](#클로저-명령어-설명)
    - [Isolatable Commands](#isolatable-commands)
    - [의존성 명령어](#의존성-명령어)
      - [Lock Expiration Time](#lock-expiration-time)
      - [잠금 만료 시간](#잠금-만료-시간)
  - [Defining Input Expectations](#defining-input-expectations)
  - [입력 예상 정의](#입력-예상-정의)
    - [Arguments](#arguments)
    - [인자](#인자)
    - [Options](#options)
    - [옵션](#옵션)
      - [Options With Values](#options-with-values)
      - [값이 있는 옵션](#값이-있는-옵션)
      - [Option Shortcuts](#option-shortcuts)
      - [옵션 단축키](#옵션-단축키)
    - [Input Arrays](#input-arrays)
    - [입력 배열](#입력-배열)
      - [Option Arrays](#option-arrays)
      - [옵션 배열](#옵션-배열)
    - [Input Descriptions](#input-descriptions)
    - [입력 설명](#입력-설명)
  - [Command I/O](#command-io)
  - [명령 I/O](#명령-io)
    - [Retrieving Input](#retrieving-input)
    - [입력 검색](#입력-검색)
    - [Prompting For Input](#prompting-for-input)
    - [입력 프롬프트](#입력-프롬프트)
      - [Asking For Confirmation](#asking-for-confirmation)
      - [확인 요청](#확인-요청)
      - [Auto-Completion](#auto-completion)
      - [자동 완성](#자동-완성)
      - [Multiple Choice Questions](#multiple-choice-questions)
      - [다중 선택 질문](#다중-선택-질문)
    - [Writing Output](#writing-output)
    - [출력 작성](#출력-작성)
      - [Tables](#tables)
      - [테이블](#테이블)
      - [Progress Bars](#progress-bars)
      - [진행률 표시줄](#진행률-표시줄)
  - [Registering Commands](#registering-commands)
  - [명령어 등록](#명령어-등록)
  - [Programmatically Executing Commands](#programmatically-executing-commands)
  - [명령어 프로그래밍적 실행](#명령어-프로그래밍적-실행)
      - [Passing Array Values](#passing-array-values)
      - [배열 값 전달](#배열-값-전달)
      - [Passing Boolean Values](#passing-boolean-values)
      - [불리언 값 전달](#불리언-값-전달)
      - [Queueing Artisan Commands](#queueing-artisan-commands)
      - [Artisan 명령어 큐에 넣기](#artisan-명령어-큐에-넣기)
    - [Calling Commands From Other Commands](#calling-commands-from-other-commands)
    - [다른 명령어에서 명령어 호출하기](#다른-명령어에서-명령어-호출하기)
  - [Signal Handling](#signal-handling)
  - [시그널 처리](#시그널-처리)
  - [Stub Customization](#stub-customization)
  - [스텁 커스터마이징](#스텁-커스터마이징)
  - [Events](#events)
  - [이벤트](#이벤트)

<a name="introduction"></a>
## Introduction
## 시작하기

Artisan is the command line interface included with Laravel. Artisan exists at the root of your application as the `artisan` script and provides a number of helpful commands that can assist you while you build your application. To view a list of all available Artisan commands, you may use the `list` command:

Artisan은 Laravel에 포함된 명령어 인터페이스입니다. Artisan은 `artisan` 스크립트로 어플리케이션의 루트에 있으며 어플리케이션을 구축하는 동안 도움이 되는 다양한 명령어를 제공합니다. 사용 가능한 모든 Artisan 명령어 목록을 보려면 `list` 명령을 사용할 수 있습니다.

```shell
php artisan list
```

Every command also includes a "help" screen which displays and describes the command's available arguments and options. To view a help screen, precede the name of the command with `help`:

모든 명령어에는 "도움말" 화면이 포함되어 있으며 이 화면에는 명령어의 사용 가능한 인자와 옵션을 표시하고 설명합니다. 도움말 화면을 보려면 명령어 이름 앞에 `help`를 입력합니다.

```shell

```shell
php artisan help migrate
```

<a name="laravel-sail"></a>
#### Laravel Sail
#### Laravel Sail

If you are using [Laravel Sail](/docs/{{version}}/sail) as your local development environment, remember to use the `sail` command line to invoke Artisan commands. Sail will execute your Artisan commands within your application's Docker containers:

만약 로컬 개발 환경으로 [Laravel Sail](/docs/{{version}}/sail)을 사용하고 있다면 Artisan 명령어를 실행하기 위해 `sail` 명령어 라인을 사용해야 합니다. Sail은 어플리케이션의 Docker 컨테이너에서 Artisan 명령어를 실행합니다.

```shell
./vendor/bin/sail artisan list
```

<a name="tinker"></a>
### Tinker (REPL)
### Tinker (REPL)

Laravel Tinker is a powerful REPL for the Laravel framework, powered by the [PsySH](https://github.com/bobthecow/psysh) package.

Laravel Tinker는 Laravel 프레임워크의 강력한 REPL로 [PsySH](https://github.com/bobthecow/psysh) 패키지에 의해 제공됩니다.

<a name="installation"></a>
#### Installation
#### 설치

All Laravel applications include Tinker by default. However, you may install Tinker using Composer if you have previously removed it from your application:

모든 Laravel 어플리케이션에는 기본적으로 Tinker가 포함되어 있습니다. 하지만, 이전에 어플리케이션에서 Tinker를 제거한 경우 Composer를 사용하여 Tinker를 설치할 수 있습니다.

```shell
composer require laravel/tinker
```

> **Note**  
> Looking for a graphical UI for interacting with your Laravel application? Check out [Tinkerwell](https://tinkerwell.app)!
>
> **참고**
> Laravel 어플리케이션과 상호 작용하기 위한 그래픽 UI를 찾고 있나요? [Tinkerwell](https://tinkerwell.app)을 확인해 보세요!

<a name="usage"></a>
#### Usage
#### 사용법

Tinker allows you to interact with your entire Laravel application on the command line, including your Eloquent models, jobs, events, and more. To enter the Tinker environment, run the `tinker` Artisan command:

Tinker를 사용하면 Eloquent 모델, 작업, 이벤트 등을 포함하여 전체 Laravel 어플리케이션과 명령어 인터페이스를 상호 작용할 수 있습니다. Tinker 환경으로 진입하려면 `tinker` Artisan 명령을 실행합니다:

```shell
php artisan tinker
```

You can publish Tinker's configuration file using the `vendor:publish` command:

`vendor:publish` 명령을 사용하여 Tinker의 구성 파일을 게시할 수 있습니다:

```shell
php artisan vendor:publish --provider="Laravel\Tinker\TinkerServiceProvider"
```

> **Warning**  
> The `dispatch` helper function and `dispatch` method on the `Dispatchable` class depends on garbage collection to place the job on the queue. Therefore, when using tinker, you should use `Bus::dispatch` or `Queue::push` to dispatch jobs.
>
> **경고**
> Dispatchable 클래스의 `dispatch` 메소드와 `dispatch` 헬퍼 함수는 가비지 컬렉션에 의존하여 작업을 큐에 배치합니다. 따라서 tinker를 사용할 때는 작업을 배치하기 위해 `Bus::dispatch` 또는 `Queue::push`를 사용해야 합니다.

<a name="command-allow-list"></a>
#### Command Allow List
#### 명령어 허용 목록

Tinker utilizes an "allow" list to determine which Artisan commands are allowed to be run within its shell. By default, you may run the `clear-compiled`, `down`, `env`, `inspire`, `migrate`, `optimize`, and `up` commands. If you would like to allow more commands you may add them to the `commands` array in your `tinker.php` configuration file:

Tinker는 "허용" 목록을 사용하여 셸 내에서 실행할 수 있는 Artisan 명령어를 결정합니다. 기본적으로 `clear-compiled`, `down`, `env`, `inspire`, `migrate`, `optimize`, `up` 명령어를 실행할 수 있습니다. 더 많은 명령어를 허용하려면 `tinker.php` 구성 파일의 `commands` 배열에 추가할 수 있습니다:

    'commands' => [
        // App\Console\Commands\ExampleCommand::class,
    ],

<a name="classes-that-should-not-be-aliased"></a>
#### Classes That Should Not Be Aliased
#### 별칭을 지정해서는 안되는 클래스

Typically, Tinker automatically aliases classes as you interact with them in Tinker. However, you may wish to never alias some classes. You may accomplish this by listing the classes in the `dont_alias` array of your `tinker.php` configuration file:

일반적으로 Tinker는 Tinker에서 클래스와 상호 작용할 때 클래스를 자동으로 별칭으로 지정합니다. 그러나, 일부 클래스에는 별칭을 지정하지 않는 것이 좋습니다. `tinker.php` 구성 파일의 `dont_alias` 배열에 클래스를 나열하여 이를 수행할 수 있습니다:

    'dont_alias' => [
        App\Models\User::class,
    ],

<a name="writing-commands"></a>
## Writing Commands
## 명령어 작성

In addition to the commands provided with Artisan, you may build your own custom commands. Commands are typically stored in the `app/Console/Commands` directory; however, you are free to choose your own storage location as long as your commands can be loaded by Composer.

Artisan에 제공되는 명령어 외에도 커스텀 명령어를 작성할 수 있습니다. 명령어는 일반적으로 `app/Console/Commands` 디렉터리에 저장되지만, Composer로 로드할 수 있는 명령어의 저장 위치는 자유롭게 선택할 수 있습니다.

<a name="generating-commands"></a>
### Generating Commands
### 명령어 생성

To create a new command, you may use the `make:command` Artisan command. This command will create a new command class in the `app/Console/Commands` directory. Don't worry if this directory does not exist in your application - it will be created the first time you run the `make:command` Artisan command:

새 명령어를 생성하려면 `make:command` Artisan 명령을 사용할 수 있습니다. 이 명령은 `app/Console/Commands` 디렉터리에 새 명령어 클래스를 생성합니다. 어플리케이션에 이 디렉터리가 없는 경우 걱정하지 마십시오. `make:command` Artisan 명령을 실행하면 이 디렉터리가 생성됩니다.

```shell
php artisan make:command SendEmails
```

<a name="command-structure"></a>
### Command Structure
### 명령어 구조

After generating your command, you should define appropriate values for the `signature` and `description` properties of the class. These properties will be used when displaying your command on the `list` screen. The `signature` property also allows you to define [your command's input expectations](#defining-input-expectations). The `handle` method will be called when your command is executed. You may place your command logic in this method.

명령어를 생성한 후 클래스의 `signature` 및 `description` 속성에 적절한 값을 정의해야 합니다. 이러한 속성은 명령어를 `list` 화면에 표시할 때 사용됩니다. `signature` 프로퍼티는 또한 [명령어의 입력 예상값을 정의](#defining-input-expectations)할 수 있도록 합니다. `handle` 메소드는 명령어가 실행될 때 호출됩니다. 이 메소드에 명령어 로직을 배치할 수 있습니다.

Let's take a look at an example command. Note that we are able to request any dependencies we need via the command's `handle` method. The Laravel [service container](/docs/{{version}}/container) will automatically inject all dependencies that are type-hinted in this method's signature:

예제 명령어를 살펴보겠습니다. 명령어의 `handle` 메소드를 통해 필요한 모든 종속성을 요청할 수 있다는 점에 유의하십시오. Laravel [서비스 컨테이너](/docs/{{version}}/container)는 자동으로 이 메소드의 시그니처에 지정된 모든 종속성을 주입합니다:

    <?php

    namespace App\Console\Commands;

    use App\Models\User;
    use App\Support\DripEmailer;
    use Illuminate\Console\Command;

    class SendEmails extends Command
    {
        /**
         * The name and signature of the console command.
         *
         * @var string
         */
        protected $signature = 'mail:send {user}';

        /**
         * The console command description.
         *
         * @var string
         */
        protected $description = 'Send a marketing email to a user';

        /**
         * Execute the console command.
         */
        public function handle(DripEmailer $drip): void
        {
            $drip->send(User::find($this->argument('user')));
        }
    }

> **Note**  
> For greater code reuse, it is good practice to keep your console commands light and let them defer to application services to accomplish their tasks. In the example above, note that we inject a service class to do the "heavy lifting" of sending the e-mails.
>
> **참고**
> 코드 재사용을 위해 콘솔 명령어를 가볍게 유지하고, 명령어의 작업을 수행하기 위해 어플리케이션 서비스를 지연시키는 것이 좋습니다. 위의 예에서는 이메일을 보내는 "무거운 작업"을 수행하기 위해 서비스 클래스를 주입했음을 유의하십시오.

<a name="closure-commands"></a>
### Closure Commands
### 클로저 명령어

Closure based commands provide an alternative to defining console commands as classes. In the same way that route closures are an alternative to controllers, think of command closures as an alternative to command classes. Within the `commands` method of your `app/Console/Kernel.php` file, Laravel loads the `routes/console.php` file:

클로저 기반 명령어는 콘솔 명령을 클래스로 정의하는 대안을 제공합니다. 라우트 클로저가 컨트롤러 대안이라는 것처럼, 명령어 클로저는 명령어 클래스 대안입니다. `app/Console/Kernel.php` 파일의 `commands` 메소드에서 Laravel은 `routes/console.php` 파일을 로드합니다.

    /**
     * Register the closure based commands for the application.
     */
    protected function commands(): void
    {
        require base_path('routes/console.php');
    }

Even though this file does not define HTTP routes, it defines console based entry points (routes) into your application. Within this file, you may define all of your closure based console commands using the `Artisan::command` method. The `command` method accepts two arguments: the [command signature](#defining-input-expectations) and a closure which receives the command's arguments and options:

이 파일은 HTTP 라우트를 정의하지 않지만, 어플리케이션에 대한 콘솔 기반 엔트리 포인트(라우트)를 정의합니다. 이 파일 내에서 `Artisan::command` 메소드를 사용하여 모든 클로저 기반 콘솔 명령을 정의할 수 있습니다. `command` 메소드는 두 개의 인수를 받습니다: [명령어 시그니처](#defining-input-expectations) 및 명령어의 인수와 옵션을 받는 클로저입니다:

    Artisan::command('mail:send {user}', function (string $user) {
        $this->info("Sending email to: {$user}!");
    });

The closure is bound to the underlying command instance, so you have full access to all of the helper methods you would typically be able to access on a full command class.

클로저는 기본 명령어 인스턴스에 바인딩되므로, 전체 명령어 클래스에서 접근할 수 있는 모든 헬퍼 메소드에 대한 전체 액세스 권한을 가집니다.

<a name="type-hinting-dependencies"></a>
#### Type-Hinting Dependencies
#### 타입힌팅 의존성

In addition to receiving your command's arguments and options, command closures may also type-hint additional dependencies that you would like resolved out of the [service container](/docs/{{version}}/container):

명령어의 인수와 옵션을 받는 것 외에도, 명령어 클로저는 또한 [서비스 컨테이너](/docs/{{version}}/container)에서 해결되는 추가 의존성을 타입힌팅할 수 있습니다:

    use App\Models\User;
    use App\Support\DripEmailer;

    Artisan::command('mail:send {user}', function (DripEmailer $drip, string $user) {
        $drip->send(User::find($user));
    });

<a name="closure-command-descriptions"></a>
#### Closure Command Descriptions
#### 클로저 명령어 설명

When defining a closure based command, you may use the `purpose` method to add a description to the command. This description will be displayed when you run the `php artisan list` or `php artisan help` commands:

클로저 기반 명령어를 정의할 때 `purpose` 메소드를 사용하여 명령어에 설명을 추가할 수 있습니다. 이 설명은 `php artisan list` 또는 `php artisan help` 명령을 실행할 때 표시됩니다:

    Artisan::command('mail:send {user}', function (string $user) {
        // ...
    })->purpose('Send a marketing email to a user');

<a name="isolatable-commands"></a>
### Isolatable Commands
### 의존성 명령어

> **Warning**
> To utilize this feature, your application must be using the `memcached`, `redis`, `dynamodb`, `database`, `file`, or `array` cache driver as your application's default cache driver. In addition, all servers must be communicating with the same central cache server.
>
> **경고**
> 이 기능을 사용하려면 어플리케이션이 `memcached`, `redis`, `dynamodb`, `database`, `file`, 또는 `array` 캐시 드라이버를 어플리케이션의 기본 캐시 드라이버로 사용해야 합니다. 또한 모든 서버는 동일한 중앙 캐시 서버와 통신해야 합니다.

Sometimes you may wish to ensure that only one instance of a command can run at a time. To accomplish this, you may implement the `Illuminate\Contracts\Console\Isolatable` interface on your command class:

가끔 명령어의 인스턴스가 한 번에 하나만 실행되도록 하려고 할 수 있습니다. 이를 위해 명령어 클래스에서 `Illuminate\Contracts\Console\Isolatable` 인터페이스를 구현할 수 있습니다:

    <?php

    namespace App\Console\Commands;

    use Illuminate\Console\Command;
    use Illuminate\Contracts\Console\Isolatable;

    class SendEmails extends Command implements Isolatable
    {
        // ...
    }

When a command is marked as `Isolatable`, Laravel will automatically add an `--isolated` option to the command. When the command is invoked with that option, Laravel will ensure that no other instances of that command are already running. Laravel accomplishes this by attempting to acquire an atomic lock using your application's default cache driver. If other instances of the command are running, the command will not execute; however, the command will still exit with a successful exit status code:

명령어가 `Isolatable`로 표시되면 Laravel은 자동으로 명령어에 `--isolated` 옵션을 추가합니다. 해당 옵션으로 명령어가 호출되면 Laravel은 해당 명령어의 다른 인스턴스가 이미 실행 중인지 확인합니다. Laravel는 어플리케이션의 기본 캐시 드라이버를 사용하여 원자적 잠금을 획득하려고 시도하여 이를 수행합니다. 명령어의 다른 인스턴스가 실행 중인 경우 명령어는 실행되지 않지만, 명령어는 여전히 성공적인 종료 상태 코드로 종료됩니다.

```shell
php artisan mail:send 1 --isolated
```

If you would like to specify the exit status code that the command should return if it is not able to execute, you may provide the desired status code via the `isolated` option:

명령어가 실행할 수 없는 경우 명령어가 반환해야 하는 종료 상태 코드를 지정하려면 `isolated` 옵션을 통해 원하는 상태 코드를 제공할 수 있습니다:

```shell
php artisan mail:send 1 --isolated=12
```

<a name="lock-expiration-time"></a>
#### Lock Expiration Time
#### 잠금 만료 시간

By default, isolation locks expire after the command is finished. Or, if the command is interrupted and unable to finish, the lock will expire after one hour. However, you may adjust the lock expiration time by defining a `isolationLockExpiresAt` method on your command:

기본적으로, 격리 잠금은 명령어가 완료된 후 만료됩니다. 또는 명령어가 중단되어 완료할 수 없는 경우 잠금은 1시간 후에 만료됩니다. 그러나 명령어에서 `isolationLockExpiresAt` 메소드를 정의하여 잠금 만료 시간을 조정할 수 있습니다:

```php
use DateTimeInterface;
use DateInterval;

/**
 * Determine when an isolation lock expires for the command.
 */
public function isolationLockExpiresAt(): DateTimeInterface|DateInterval
{
    return now()->addMinutes(5);
}
```

<a name="defining-input-expectations"></a>
## Defining Input Expectations
## 입력 예상 정의

When writing console commands, it is common to gather input from the user through arguments or options. Laravel makes it very convenient to define the input you expect from the user using the `signature` property on your commands. The `signature` property allows you to define the name, arguments, and options for the command in a single, expressive, route-like syntax.

콘솔 명령어를 작성할 때, 사용자로부터 인자 또는 옵션을 통해 입력을 수집하는 것이 일반적입니다. Laravel은 명령의 `signature` 속성을 사용하여 사용자로부터 예상하는 입력을 정의하는 것이 매우 편리합니다. `signature` 프로퍼티를 사용하면 단일, 표현적인 라우트와 유사한 구문을 사용하여 명령의 이름, 인자 및 옵션을 정의할 수 있습니다.

<a name="arguments"></a>
### Arguments
### 인자

All user supplied arguments and options are wrapped in curly braces. In the following example, the command defines one required argument: `user`:

모든 사용자가 제공한 인자와 옵션은 중괄호로 감싸져 있습니다. 다음 예에서 명령은 하나의 필수 인자 `user`를 정의합니다:

    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'mail:send {user}';

You may also make arguments optional or define default values for arguments:

인자를 선택적으로 만들거나 인자의 기본값을 정의할 수도 있습니다:

    // Optional argument...
    'mail:send {user?}'

    // Optional argument with default value...
    'mail:send {user=foo}'

<a name="options"></a>
### Options
### 옵션

Options, like arguments, are another form of user input. Options are prefixed by two hyphens (`--`) when they are provided via the command line. There are two types of options: those that receive a value and those that don't. Options that don't receive a value serve as a boolean "switch". Let's take a look at an example of this type of option:

인자와 마찬가지로, 옵션은 사용자 입력의 다른 형식입니다. 옵션은 명령 줄을 통해 제공될 때 두 개의 하이픈 (`--`)으로 접두사가 붙습니다. 옵션에는 값을 받는 옵션과 그렇지 않은 옵션이 두 가지 유형이 있습니다. 값이 없는 옵션은 부울 "스위치"로 작동합니다. 이러한 유형의 옵션의 예를 살펴보겠습니다:

    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'mail:send {user} {--queue}';

In this example, the `--queue` switch may be specified when calling the Artisan command. If the `--queue` switch is passed, the value of the option will be `true`. Otherwise, the value will be `false`:

이 예제에서, `--queue` 스위치는 아티젠 명령을 호출할 때 지정할 수 있습니다. `--queue` 스위치가 전달되면, 옵션의 값은 `true`가 됩니다. 그렇지 않으면, 값은 `false`가 됩니다.

```shell
php artisan mail:send 1 --queue
```

<a name="options-with-values"></a>
#### Options With Values
#### 값이 있는 옵션

Next, let's take a look at an option that expects a value. If the user must specify a value for an option, you should suffix the option name with a `=` sign:

다음으로, 값이 필요한 옵션을 살펴보겠습니다. 사용자가 옵션에 대한 값을 지정해야 하는 경우, 옵션 이름에 `=` 기호를 붙여야 합니다:

    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'mail:send {user} {--queue=}';

In this example, the user may pass a value for the option like so. If the option is not specified when invoking the command, its value will be `null`:

이 예제에서, 사용자는 다음과 같이 옵션에 대한 값을 전달할 수 있습니다. 명령어를 호출할 때 옵션이 지정되지 않으면, 값은 `null`이 됩니다.

```shell
php artisan mail:send 1 --queue=default
```

You may assign default values to options by specifying the default value after the option name. If no option value is passed by the user, the default value will be used:

옵션 이름 뒤에 기본값을 지정하여 옵션에 기본값을 할당할 수 있습니다. 사용자가 옵션 값을 전달하지 않으면, 기본값이 사용됩니다.

    'mail:send {user} {--queue=default}'

<a name="option-shortcuts"></a>
#### Option Shortcuts
#### 옵션 단축키

To assign a shortcut when defining an option, you may specify it before the option name and use the `|` character as a delimiter to separate the shortcut from the full option name:

옵션을 정의할 때 단축키를 할당하려면, 옵션 이름 앞에 지정하고 단축키를 전체 옵션 이름으로부터 분리하기 위해 `|` 문자를 구분자로 사용할 수 있습니다:

    'mail:send {user} {--Q|queue}'

When invoking the command on your terminal, option shortcuts should be prefixed with a single hyphen:

터미널에서 명령을 호출할 때, 옵션 단축키는 하이픈 하나로 접두사가 붙어야 합니다.

```shell
php artisan mail:send 1 -Q
```

<a name="input-arrays"></a>
### Input Arrays
### 입력 배열

If you would like to define arguments or options to expect multiple input values, you may use the `*` character. First, let's take a look at an example that specifies such an argument:

여러 입력 값을 기대하는 인자나 옵션을 정의하려면, `*` 문자를 사용할 수 있습니다. 먼저, 다음과 같이 인자를 지정하는 예제를 살펴보겠습니다:

    'mail:send {user*}'

When calling this method, the `user` arguments may be passed in order to the command line. For example, the following command will set the value of `user` to an array with `1` and `2` as its values:

이 메소드를 호출할 때, `user` 인자는 명령 줄에 순서대로 전달됩니다. 예를 들어, 다음 명령은 `user`의 값을 `1`과 `2`의 값으로 가지는 배열로 설정합니다.

```shell
php artisan mail:send 1 2
```

This `*` character can be combined with an optional argument definition to allow zero or more instances of an argument:

이 `*` 문자는 선택적 인자 정의와 결합하여 인자의 인스턴스를 0개 이상 허용할 수 있습니다.

    'mail:send {user?*}'

<a name="option-arrays"></a>
#### Option Arrays
#### 옵션 배열

When defining an option that expects multiple input values, each option value passed to the command should be prefixed with the option name:

여러 입력 값을 기대하는 옵션을 정의할 때, 명령에 전달된 각 옵션 값은 옵션 이름으로 접두사가 붙어야 합니다.

    'mail:send {--id=*}'

Such a command may be invoked by passing multiple `--id` arguments:

이러한 명령은 여러 `--id` 인자를 전달하여 호출할 수 있습니다.

```shell
php artisan mail:send --id=1 --id=2
```

<a name="input-descriptions"></a>
### Input Descriptions
### 입력 설명

You may assign descriptions to input arguments and options by separating the argument name from the description using a colon. If you need a little extra room to define your command, feel free to spread the definition across multiple lines:

인자와 옵션에 대한 설명을 입력하려면, 콜론을 사용하여 인자 이름과 설명을 분리합니다. 명령을 정의하는 데 약간의 여유 공간이 필요하다면, 정의를 여러 줄에 걸쳐서 할 수 있습니다:

    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'mail:send
                            {user : The ID of the user}
                            {--queue : Whether the job should be queued}';

<a name="command-io"></a>
## Command I/O
## 명령 I/O

<a name="retrieving-input"></a>
### Retrieving Input
### 입력 검색

While your command is executing, you will likely need to access the values for the arguments and options accepted by your command. To do so, you may use the `argument` and `option` methods. If an argument or option does not exist, `null` will be returned:

명령이 실행되는 동안, 명령이 받는 인자와 옵션의 값을 액세스해야 할 수도 있습니다. 이를 위해, `argument`와 `option` 메소드를 사용할 수 있습니다. 인자나 옵션이 존재하지 않으면, `null`이 반환됩니다:

    /**
     * Execute the console command.
     */
    public function handle(): void
    {
        $userId = $this->argument('user');
    }

If you need to retrieve all of the arguments as an `array`, call the `arguments` method:

인자를 `array`로 검색해야 하는 경우, `arguments` 메소드를 호출합니다:

    $arguments = $this->arguments();

Options may be retrieved just as easily as arguments using the `option` method. To retrieve all of the options as an array, call the `options` method:

옵션은 인자와 마찬가지로 `option` 메소드를 사용하여 검색할 수 있습니다. 모든 옵션을 배열로 검색하려면, `options` 메소드를 호출합니다:

    // Retrieve a specific option...
    $queueName = $this->option('queue');

    // Retrieve all options as an array...
    $options = $this->options();

<a name="prompting-for-input"></a>
### Prompting For Input
### 입력 프롬프트

In addition to displaying output, you may also ask the user to provide input during the execution of your command. The `ask` method will prompt the user with the given question, accept their input, and then return the user's input back to your command:

출력을 표시하는 것 외에도, 명령 실행 중에 사용자가 입력을 제공하도록 요청할 수 있습니다. `ask` 메소드는 주어진 질문으로 사용자에게 프롬프트를 표시하고, 입력을 받은 다음, 사용자의 입력을 명령에 반환합니다:

    /**
     * Execute the console command.
     */
    public function handle(): void
    {
        $name = $this->ask('What is your name?');

        // ...
    }

The `secret` method is similar to `ask`, but the user's input will not be visible to them as they type in the console. This method is useful when asking for sensitive information such as passwords:

`secret` 메소드는 `ask`와 유사하지만, 사용자의 입력은 콘솔에서 입력할 때 표시되지 않습니다. 이 메소드는 비밀번호와 같은 민감한 정보를 요청할 때 유용합니다:

    $password = $this->secret('What is the password?');

<a name="asking-for-confirmation"></a>
#### Asking For Confirmation
#### 확인 요청

If you need to ask the user for a simple "yes or no" confirmation, you may use the `confirm` method. By default, this method will return `false`. However, if the user enters `y` or `yes` in response to the prompt, the method will return `true`.

사용자에게 간단한 "예 또는 아니오" 확인을 요청해야 하는 경우, `confirm` 메소드를 사용할 수 있습니다. 기본적으로, 이 메소드는 `false`를 반환합니다. 그러나, 사용자가 프롬프트에 대한 응답으로 `y` 또는 `yes`를 입력하면, 메소드는 `true`를 반환합니다.

    if ($this->confirm('Do you wish to continue?')) {
        // ...
    }

If necessary, you may specify that the confirmation prompt should return `true` by default by passing `true` as the second argument to the `confirm` method:

필요한 경우, `confirm` 메소드의 두 번째 인자로 `true`를 전달하여 확인 프롬프트가 기본적으로 `true`를 반환하도록 지정할 수 있습니다:

    if ($this->confirm('Do you wish to continue?', true)) {
        // ...
    }

<a name="auto-completion"></a>
#### Auto-Completion
#### 자동 완성

The `anticipate` method can be used to provide auto-completion for possible choices. The user can still provide any answer, regardless of the auto-completion hints:

`anticipate` 메소드는 가능한 선택에 대한 자동 완성을 제공하는 데 사용할 수 있습니다. 사용자는 여전히 자동 완성 힌트에 관계없이 어떤 답변이든 제공할 수 있습니다:

    $name = $this->anticipate('What is your name?', ['Taylor', 'Dayle']);

Alternatively, you may pass a closure as the second argument to the `anticipate` method. The closure will be called each time the user types an input character. The closure should accept a string parameter containing the user's input so far, and return an array of options for auto-completion:

또는, `anticipate` 메소드의 두 번째 인자로 클로저를 전달할 수 있습니다. 클로저는 사용자가 입력 문자를 입력할 때마다 호출됩니다. 클로저는 사용자가 지금까지 입력한 문자열을 포함하는 문자열 매개변수를 받아야 하며, 자동 완성을 위한 옵션 배열을 반환해야 합니다:

    $name = $this->anticipate('What is your address?', function (string $input) {
        // Return auto-completion options...
    });

<a name="multiple-choice-questions"></a>
#### Multiple Choice Questions
#### 다중 선택 질문

If you need to give the user a predefined set of choices when asking a question, you may use the `choice` method. You may set the array index of the default value to be returned if no option is chosen by passing the index as the third argument to the method:

질문을 할 때 사용자에게 미리 정의된 선택지 집합을 제공해야 하는 경우, `choice` 메소드를 사용할 수 있습니다. 인덱스를 메소드의 세 번째 인자로 전달하여 선택하지 않은 경우 반환할 기본값의 배열 인덱스를 설정할 수 있습니다:

    $name = $this->choice(
        'What is your name?',
        ['Taylor', 'Dayle'],
        $defaultIndex
    );

In addition, the `choice` method accepts optional fourth and fifth arguments for determining the maximum number of attempts to select a valid response and whether multiple selections are permitted:

또한, `choice` 메소드는 유효한 응답을 선택할 수 있는 최대 시도 횟수와 여러 선택이 허용되는지 여부를 결정하는 선택적 네 번째 및 다섯 번째 인자를 받습니다:

    $name = $this->choice(
        'What is your name?',
        ['Taylor', 'Dayle'],
        $defaultIndex,
        $maxAttempts = null,
        $allowMultipleSelections = false
    );

<a name="writing-output"></a>
### Writing Output
### 출력 작성

To send output to the console, you may use the `line`, `info`, `comment`, `question`, `warn`, and `error` methods. Each of these methods will use appropriate ANSI colors for their purpose. For example, let's display some general information to the user. Typically, the `info` method will display in the console as green colored text:

콘솔에 출력을 보내려면, `line`, `info`, `comment`, `question`, `warn`, `error` 메소드를 사용할 수 있습니다. 각각의 메소드는 목적에 맞는 적절한 ANSI 색상을 사용합니다. 예를 들어, 사용자에게 일반 정보를 표시해 보겠습니다. 일반적으로, `info` 메소드는 콘솔에 녹색으로 표시됩니다.

    /**
     * Execute the console command.
     */
    public function handle(): void
    {
        // ...

        $this->info('The command was successful!');
    }

To display an error message, use the `error` method. Error message text is typically displayed in red:

`error` 메소드를 사용하여 오류 메시지를 표시할 수 있습니다. 오류 메시지 텍스트는 일반적으로 빨간색으로 표시됩니다:

    $this->error('Something went wrong!');

You may use the `line` method to display plain, uncolored text:

`line` 메소드를 사용하여 일반 텍스트를 표시할 수 있습니다: 

    $this->line('Display this on the screen');

You may use the `newLine` method to display a blank line:

`newLine` 메소드를 사용하여 빈 줄을 표시할 수 있습니다:

    // Write a single blank line...
    $this->newLine();

    // Write three blank lines...
    $this->newLine(3);

<a name="tables"></a>
#### Tables
#### 테이블

The `table` method makes it easy to correctly format multiple rows / columns of data. All you need to do is provide the column names and the data for the table and Laravel will
automatically calculate the appropriate width and height of the table for you:

`table` 메소드를 사용하면 여러 행/컬럼의 데이터를 올바르게 포맷하는 것이 쉽습니다. 할 일은 테이블의 컬럼 이름과 데이터를 제공하는 것뿐이며, Laravel은 테이블의 적절한 너비와 높이를 자동으로 계산합니다:

    use App\Models\User;

    $this->table(
        ['Name', 'Email'],
        User::all(['name', 'email'])->toArray()
    );

<a name="progress-bars"></a>
#### Progress Bars
#### 진행률 표시줄

For long running tasks, it can be helpful to show a progress bar that informs users how complete the task is. Using the `withProgressBar` method, Laravel will display a progress bar and advance its progress for each iteration over a given iterable value:

긴 실행 시간을 가지는 작업의 경우, 작업이 얼마나 완료되었는지 사용자에게 알려주는 진행률 표시줄을 표시하는 것이 도움이 될 수 있습니다. `withProgressBar` 메소드를 사용하면, Laravel은 진행률 표시줄을 표시하고 주어진 반복 가능한 값에 대해 각 반복마다 진행률을 전진시킵니다:

    use App\Models\User;

    $users = $this->withProgressBar(User::all(), function (User $user) {
        $this->performTask($user);
    });

Sometimes, you may need more manual control over how a progress bar is advanced. First, define the total number of steps the process will iterate through. Then, advance the progress bar after processing each item:

가끔, 진행률 표시줄이 전진하는 방식을 더 많이 제어해야 할 수도 있습니다. 먼저, 프로세스가 반복할 총 단계 수를 정의합니다. 그런 다음 각 항목을 처리한 후 진행률 표시줄을 전진시킵니다:

    $users = App\Models\User::all();

    $bar = $this->output->createProgressBar(count($users));

    $bar->start();

    foreach ($users as $user) {
        $this->performTask($user);

        $bar->advance();
    }

    $bar->finish();

> **Note**  
> For more advanced options, check out the [Symfony Progress Bar component documentation](https://symfony.com/doc/current/components/console/helpers/progressbar.html).
>
> **참고**
> 고급 옵션에 대해서는 [Symfony Progress Bar component documentation](https://symfony.com/doc/current/components/console/helpers/progressbar.html)을 확인하세요.

<a name="registering-commands"></a>
## Registering Commands
## 명령어 등록

All of your console commands are registered within your application's `App\Console\Kernel` class, which is your application's "console kernel". Within the `commands` method of this class, you will see a call to the kernel's `load` method. The `load` method will scan the `app/Console/Commands` directory and automatically register each command it contains with Artisan. You are even free to make additional calls to the `load` method to scan other directories for Artisan commands:

모든 콘솔 명령어는 어플리케이션의 `App\Console\Kernel` 클래스에 등록되어 있으며, 이것이 어플리케이션의 "콘솔 커널"입니다. 이 클래스의 `commands` 메소드에서는 커널의 `load` 메소드를 호출하는 것을 볼 수 있습니다. `load` 메소드는 `app/Console/Commands` 디렉토리를 스캔하고 Artisan에 포함된 각 명령어를 자동으로 등록합니다. Artisan 명령어를 스캔하기 위해 다른 디렉토리에 대해 `load` 메소드를 추가로 호출할 수도 있습니다:

    /**
     * Register the commands for the application.
     */
    protected function commands(): void
    {
        $this->load(__DIR__.'/Commands');
        $this->load(__DIR__.'/../Domain/Orders/Commands');

        // ...
    }

If necessary, you may manually register commands by adding the command's class name to a `$commands` property within your `App\Console\Kernel` class. If this property is not already defined on your kernel, you should define it manually. When Artisan boots, all the commands listed in this property will be resolved by the [service container](/docs/{{version}}/container) and registered with Artisan:

필요한 경우, `App\Console\Kernel` 클래스의 `$commands` 프로퍼티에 명령어 클래스 이름을 추가하여 명령어를 수동으로 등록할 수 있습니다. 커널에 이미 정의되어 있지 않은 경우 수동으로 정의해야 합니다. Artisan이 부팅되면, 이 프로퍼티에 나열된 모든 명령어는 [서비스 컨테이너](/docs/{{version}}/container)에 의해 해결되고 Artisan에 등록됩니다:

    protected $commands = [
        Commands\SendEmails::class
    ];

<a name="programmatically-executing-commands"></a>
## Programmatically Executing Commands
## 명령어 프로그래밍적 실행

Sometimes you may wish to execute an Artisan command outside of the CLI. For example, you may wish to execute an Artisan command from a route or controller. You may use the `call` method on the `Artisan` facade to accomplish this. The `call` method accepts either the command's signature name or class name as its first argument, and an array of command parameters as the second argument. The exit code will be returned:

가끔 CLI 외부에서 Artisan 명령어를 실행하고 싶을 수 있습니다. 예를 들어, 라우트나 컨트롤러에서 Artisan 명령어를 실행하고 싶을 수 있습니다. `Artisan` facade의 `call` 메소드를 사용하여 이를 수행할 수 있습니다. `call` 메소드는 첫 번째 인수로 명령어 서명 이름 또는 클래스 이름을, 두 번째 인수로 명령어 매개변수 배열을 받습니다. 종료 코드가 반환됩니다:

    use Illuminate\Support\Facades\Artisan;

    Route::post('/user/{user}/mail', function (string $user) {
        $exitCode = Artisan::call('mail:send', [
            'user' => $user, '--queue' => 'default'
        ]);

        // ...
    });

Alternatively, you may pass the entire Artisan command to the `call` method as a string:

또는, `call` 메소드에 전체 Artisan 명령어를 문자열로 전달할 수 있습니다:

    Artisan::call('mail:send 1 --queue=default');

<a name="passing-array-values"></a>
#### Passing Array Values
#### 배열 값 전달

If your command defines an option that accepts an array, you may pass an array of values to that option:

명령어가 배열을 허용하는 옵션을 정의하고 있다면, 해당 옵션에 배열 값을 전달할 수 있습니다:

    use Illuminate\Support\Facades\Artisan;

    Route::post('/mail', function () {
        $exitCode = Artisan::call('mail:send', [
            '--id' => [5, 13]
        ]);
    });

<a name="passing-boolean-values"></a>
#### Passing Boolean Values
#### 불리언 값 전달

If you need to specify the value of an option that does not accept string values, such as the `--force` flag on the `migrate:refresh` command, you should pass `true` or `false` as the value of the option:

문자열 값을 허용하지 않는 옵션의 값을 지정해야 하는 경우, `migrate:refresh` 명령어의 `--force` 플래그와 같은 경우, `true` 또는 `false`를 옵션의 값으로 전달해야 합니다:

    $exitCode = Artisan::call('migrate:refresh', [
        '--force' => true,
    ]);

<a name="queueing-artisan-commands"></a>
#### Queueing Artisan Commands
#### Artisan 명령어 큐에 넣기

Using the `queue` method on the `Artisan` facade, you may even queue Artisan commands so they are processed in the background by your [queue workers](/docs/{{version}}/queues). Before using this method, make sure you have configured your queue and are running a queue listener:

`Artisan` facade의 `queue` 메소드를 사용하면, [큐 워커](/docs/{{version}}/queues)에 의해 백그라운드에서 처리되도록 Artisan 명령어를 큐에 넣을 수 있습니다. 이 메소드를 사용하기 전에 큐를 구성하고 큐 리스너를 실행하는지 확인하세요:

    use Illuminate\Support\Facades\Artisan;

    Route::post('/user/{user}/mail', function (string $user) {
        Artisan::queue('mail:send', [
            'user' => $user, '--queue' => 'default'
        ]);

        // ...
    });

Using the `onConnection` and `onQueue` methods, you may specify the connection or queue the Artisan command should be dispatched to:

`onConnection` 및 `onQueue` 메소드를 사용하여 Artisan 명령어가 전달될 연결 또는 큐를 지정할 수 있습니다:

    Artisan::queue('mail:send', [
        'user' => 1, '--queue' => 'default'
    ])->onConnection('redis')->onQueue('commands');

<a name="calling-commands-from-other-commands"></a>
### Calling Commands From Other Commands
### 다른 명령어에서 명령어 호출하기

Sometimes you may wish to call other commands from an existing Artisan command. You may do so using the `call` method. This `call` method accepts the command name and an array of command arguments / options:

가끔 기존 Artisan 명령어에서 다른 명령어를 호출하고 싶을 수 있습니다. `call` 메소드를 사용하여 이를 수행할 수 있습니다. 이 `call` 메소드는 명령어 이름과 명령어 인수/옵션 배열을 받습니다:

    /**
     * Execute the console command.
     */
    public function handle(): void
    {
        $this->call('mail:send', [
            'user' => 1, '--queue' => 'default'
        ]);

        // ...
    }

If you would like to call another console command and suppress all of its output, you may use the `callSilently` method. The `callSilently` method has the same signature as the `call` method:

다른 콘솔 명령어를 호출하고 모든 출력을 숨기고 싶다면, `callSilently` 메소드를 사용할 수 있습니다. `callSilently` 메소드는 `call` 메소드와 동일한 시그니처를 가집니다:

    $this->callSilently('mail:send', [
        'user' => 1, '--queue' => 'default'
    ]);

<a name="signal-handling"></a>
## Signal Handling
## 시그널 처리

As you may know, operating systems allow signals to be sent to running processes. For example, the `SIGTERM` signal is how operating systems ask a program to terminate. If you wish to listen for signals in your Artisan console commands and execute code when they occur, you may use the `trap` method:

알고 계시다시피, 운영 체제는 실행 중인 프로세스에 시그널을 보낼 수 있습니다. 예를 들어, `SIGTERM` 시그널은 운영 체제가 프로그램을 종료하도록 요청하는 방법입니다. Artisan 콘솔 명령어에서 시그널을 수신하고 발생할 때 코드를 실행하려면, `trap` 메소드를 사용할 수 있습니다:

    /**
     * Execute the console command.
     */
    public function handle(): void
    {
        $this->trap(SIGTERM, fn () => $this->shouldKeepRunning = false);

        while ($this->shouldKeepRunning) {
            // ...
        }
    }

To listen for multiple signals at once, you may provide an array of signals to the `trap` method:

한 번에 여러 시그널을 수신하려면, `trap` 메소드에 시그널 배열을 전달할 수 있습니다:

    $this->trap([SIGTERM, SIGQUIT], function (int $signal) {
        $this->shouldKeepRunning = false;

        dump($signal); // SIGTERM / SIGQUIT
    });

<a name="stub-customization"></a>
## Stub Customization
## 스텁 커스터마이징

The Artisan console's `make` commands are used to create a variety of classes, such as controllers, jobs, migrations, and tests. These classes are generated using "stub" files that are populated with values based on your input. However, you may want to make small changes to files generated by Artisan. To accomplish this, you may use the `stub:publish` command to publish the most common stubs to your application so that you can customize them:

Artisan 콘솔의 `make` 명령어는 컨트롤러, 작업, 마이그레이션 및 테스트와 같은 다양한 클래스를 생성하는 데 사용됩니다. 이러한 클래스는 사용자 입력에 따라 값이 채워진 "스텁" 파일을 사용하여 생성됩니다. 그러나 Artisan에 의해 생성된 파일을 약간 변경하고 싶을 수 있습니다. 이를 수행하려면, `stub:publish` 명령을 사용하여 어플리케이션에 가장 일반적인 스텁을 게시하여 커스터마이징할 수 있습니다:

```shell
php artisan stub:publish
```

The published stubs will be located within a `stubs` directory in the root of your application. Any changes you make to these stubs will be reflected when you generate their corresponding classes using Artisan's `make` commands.

게시된 스텁은 어플리케이션의 루트에 있는 `stubs` 디렉토리에 위치합니다. 이러한 스텁에 대한 변경 사항은 Artisan의 `make` 명령을 사용하여 해당 클래스를 생성할 때 반영됩니다.

<a name="events"></a>
## Events
## 이벤트

Artisan dispatches three events when running commands: `Illuminate\Console\Events\ArtisanStarting`, `Illuminate\Console\Events\CommandStarting`, and `Illuminate\Console\Events\CommandFinished`. The `ArtisanStarting` event is dispatched immediately when Artisan starts running. Next, the `CommandStarting` event is dispatched immediately before a command runs. Finally, the `CommandFinished` event is dispatched once a command finishes executing.

Artisan은 명령을 실행할 때 세 가지 이벤트를 전달합니다: `Illuminate\Console\Events\ArtisanStarting`, `Illuminate\Console\Events\CommandStarting`, `Illuminate\Console\Events\CommandFinished`. `ArtisanStarting` 이벤트는 Artisan이 실행을 시작할 때 즉시 전달됩니다. 다음으로, `CommandStarting` 이벤트는 명령이 실행되기 전에 즉시 전달됩니다. 마지막으로, `CommandFinished` 이벤트는 명령이 실행을 완료한 후 한 번 전달됩니다.