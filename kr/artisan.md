# Artisan Console
# 아티즌 콘솔

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [Tinker (REPL)](#tinker)
    - [Tinker (REPL)](#tinker)
- [Writing Commands](#writing-commands)
- [명령어 작성하기](#writing-commands)
    - [Generating Commands](#generating-commands)
    - [명령 생성](#generating-commands)
    - [Command Structure](#command-structure)
    - [명령어의 구조](#command-structure)
    - [Closure Commands](#closure-commands)
    - [클로저 명령](#closure-commands)
- [Defining Input Expectations](#defining-input-expectations)
- [입력 값들 정의하기](#defining-input-expectations)
    - [Arguments](#arguments)
    - [인자들](#arguments)
    - [Options](#options)
    - [옵션들](#options)
    - [Input Arrays](#input-arrays)
    - [배열 입력](#input-arrays)
    - [Input Descriptions](#input-descriptions)
    - [설명 입력](#input-descriptions)
- [Command I/O](#command-io)
- [명령 입출력](#command-io)
    - [Retrieving Input](#retrieving-input)
    - [입력 조회](#retrieving-input)
    - [Prompting For Input](#prompting-for-input)
    - [입력 프롬프트](#prompting-for-input)
    - [Writing Output](#writing-output)
    - [출력 기록](#writing-output)
- [Registering Commands](#registering-commands)
- [명령어 등록하기](#registering-commands)
- [Programmatically Executing Commands](#programmatically-executing-commands)
- [프로그래밍 방식으로 명령 실행](#programmatically-executing-commands)
    - [Calling Commands From Other Commands](#calling-commands-from-other-commands)
    - [다른 명령에서 명령 호출](#calling-commands-from-other-commands)

<a name="introduction"></a>
## Introduction
## 시작하기

Artisan is the command-line interface included with Laravel. It provides a number of helpful commands that can assist you while you build your application. To view a list of all available Artisan commands, you may use the `list` command:

아티즌(Artisan)은 라라벨에 포함된 커맨드라인 인터페이스(CLI)의 이름입니다. 아티즌은 애플리케이션 개발에 도움을 주는 많은 명령어들을 제공합니다. 실행가능한 아티즌 명령어 목록을 확인하려면 `list` 명령어를 다음과 같이 입력하면 됩니다.

    php artisan list

Every command also includes a "help" screen which displays and describes the command's available arguments and options. To view a help screen, precede the name of the command with `help`:

모든 명령어들은 "도움말" 기능을 포함하고 있어서 해당 명령어의 인자와, 옵션들을 확인할 수 있습니다. 도움말 화면을 확인하기 위해서는 다음과 같이 명령어 앞에 `help` 를 추가하여 실행하면 됩니다.

    php artisan help migrate

<a name="tinker"></a>
### Tinker (REPL)
### Tinker (REPL)

All Laravel applications include Tinker, a REPL powered by the [PsySH](https://github.com/bobthecow/psysh) package. Tinker allows you to interact with your entire Laravel application on the command line, including the Eloquent ORM, jobs, events, and more. To enter the Tinker environment, run the `tinker` Artisan command:

모든 라라벨 애플리케이션에는 [PsySH](https://github.com/bobthecow/psysh) 패키지로 구동되는 REPL 인 Tinker가 포함됩니다. Tinker를 사용하면 Eloquent ORM, Job, 이벤트 등 커맨드라인을 통해서 라라벨 애플리케이션을 조작할 수 있습니다. Tinker 환경에 진입하려면 `tinker` 아티즌 명령어를 실행하십시오:

    php artisan tinker

You can publish Tinker's configuration file using the `vendor:publish` command:

`vendor:publish` 명령어를 사용하여 Tinker의 설정 파일을 구성할 수 있습니다:

    php artisan vendor:publish --provider="Laravel\Tinker\TinkerServiceProvider"

#### Command Whitelist
#### 명령어 화이트리스트

Tinker utilizes a white-list to determine which Artisan commands are allowed to be run within its shell. By default, you may run the `clear-compiled`, `down`, `env`, `inspire`, `migrate`, `optimize`, and `up` commands. If you would like to white-list more commands you may add them to the `commands` array in your `tinker.php` configuration file:

Tinker 는 어떤 아티즌 명령어들이 쉘(shell) 에서 구동할 수 있는지 화이트리스트를 구성합니다. 기본적으로 여러분은 `clear-compiled`, `down`, `env`, `inspire`, `migrate`, `optimize` 그리고 `up` 명령어를 실행할 수 있습니다. 더 많은 명령어들을 화이트리스트에 추가하고자 한다면, `tinker.php` 설정 파일의 `commands` 배열에 추가 하면됩니다:

    'commands' => [
        // App\Console\Commands\ExampleCommand::class,
    ],

#### Alias Blacklist
#### 별칭 블랙리스트

Typically, Tinker automatically aliases classes as you require them in Tinker. However, you may wish to never alias some classes. You may accomplish this by listing the classes in the `dont_alias` array of your `tinker.php` configuration file:

일반적으로 Tinker는 필요한 클래스에 자동으로 별칭(alias)을 지정합니다. 하지만 일부 클래스틑 별칭을 지정하지 않을 수도 있습니다. `tinker.php` 설정 파일의 `dont_alias` 배열에 클래스를 추가하면 됩니다:

    'dont_alias' => [
        App\User::class,
    ],

<a name="writing-commands"></a>
## Writing Commands
## 명령어 작성하기

In addition to the commands provided with Artisan, you may also build your own custom commands. Commands are typically stored in the `app/Console/Commands` directory; however, you are free to choose your own storage location as long as your commands can be loaded by Composer.

아티즌에서 제공하는 명령어들에 더하여 추가적으로 애플리케이션에서 동작하는 여러분 자신의 고유한 명령어를 만들 수 있습니다. 여러분의 고유한 명령어는 `app/Console/Commands` 디렉토리에 저장하면 됩니다만, 여러분들이 컴포저를 이용하여 소유한 저장소를 자유롭게 선택하여 사용할 수 있습니다.

<a name="generating-commands"></a>
### Generating Commands
### 명령어 생성하기

To create a new command, use the `make:command` Artisan command. This command will create a new command class in the `app/Console/Commands` directory. Don't worry if this directory does not exist in your application, since it will be created the first time you run the `make:command` Artisan command. The generated command will include the default set of properties and methods that are present on all commands:

새로운 명령어를 생성하기 위해, 아티즌 명령어 `make:command` 을 사용합니다. 이 명령어는 `app/Console/Commands` 경로에 새로운 명령클래스를 생성할 것입니다. `make:command` 아티즌 명령을 처음 실행할 때 생성되기 때문에 이 경로가 애플리케이션에 존재하지 않는다고 걱정하지 마십시오. 생성 된 명령에는 모든 명령에있는 기본 등록 정보 및 함수 세트가 포함됩니다:

    php artisan make:command SendEmails

<a name="command-structure"></a>
### Command Structure
### 명령어 구조

After generating your command, you should fill in the `signature` and `description` properties of the class, which will be used when displaying your command on the `list` screen. The `handle` method will be called when your command is executed. You may place your command logic in this method.

명령을 생성 후, 클래스의 `signature` 과 `description` 를 입력합니다. 이것들은 `list` 화면에 커맨드를 표시 할 때 사용됩니다. `handle` 함수는 여러분의 명령이 실행될 때 호출됩니다. 이 방법으로 명령 구문을 배치 할 수 있습니다.

> {tip} For greater code reuse, it is good practice to keep your console commands light and let them defer to application services to accomplish their tasks. In the example below, note that we inject a service class to do the "heavy lifting" of sending the e-mails.

> {팁} 보다 나은 코드 재사용성을 위해, 콘솔 명령어를 가볍게 유지하고 애플리케이션 서비스들이 해당 작업을 수행할 수 있도록 하는 것이 좋습니다. 아래 예제에서 우리는 "대용량 전송" 메일 전송을 위해 서비스 클래스를 주입합니다.

Let's take a look at an example command. Note that we are able to inject any dependencies we need into the command's `handle` method. The Laravel [service container](/docs/{{version}}/container) will automatically inject all dependencies that are type-hinted in this method's signature:

예제 명령을 살펴 보겠습니다. 명령어 클래스는 `handle` 메소드를 통해 필요한 의존성 주입을 할 수 있습니다. 라라벨의 [서비스 컨테이너](/docs/{{version}}/container) 는 `handle` 에 타입이 지정된 의존객체들을 자동으로 주입해 줄 것입니다.

    <?php

    namespace App\Console\Commands;

    use App\User;
    use App\DripEmailer;
    use Illuminate\Console\Command;

    class SendEmails extends Command
    {
        /**
         * The name and signature of the console command.
         *
         * @var string
         */
        protected $signature = 'email:send {user}';

        /**
         * The console command description.
         *
         * @var string
         */
        protected $description = 'Send drip e-mails to a user';

        /**
         * Create a new command instance.
         *
         * @return void
         */
        public function __construct()
        {
            parent::__construct();
        }

        /**
         * Execute the console command.
         *
         * @param  \App\DripEmailer  $drip
         * @return mixed
         */
        public function handle(DripEmailer $drip)
        {
            $drip->send(User::find($this->argument('user')));
        }
    }

<a name="closure-commands"></a>
### Closure Commands
### 클로저 명령

Closure based commands provide an alternative to defining console commands as classes. In the same way that route Closures are an alternative to controllers, think of command Closures as an alternative to command classes. Within the `commands` method of your `app/Console/Kernel.php` file, Laravel loads the `routes/console.php` file:

클로저 기반 명령은 콘솔 명령을 클래스로 정의하는 대신에 사용할 수 있습니다. 라우트 클로저가 컨트롤러의 대안 인 것과 같은 방식으로 명령 클래스에 대한 명령 클로저를 생각해보십시오. `app/Console/Kernel.php` 파일의`commands` 함수 내에서 Laravel은`routes/console.php` 파일을 로드합니다. :

    /**
     * Register the Closure based commands for the application.
     *
     * @return void
     */
    protected function commands()
    {
        require base_path('routes/console.php');
    }

Even though this file does not define HTTP routes, it defines console based entry points (routes) into your application. Within this file, you may define all of your Closure based routes using the `Artisan::command` method. The `command` method accepts two arguments: the [command signature](#defining-input-expectations) and a Closure which receives the commands arguments and options:

이 파일은 HTTP 라우트를 정의하지 않지만 애플리케이션에 콘솔 기반의 진입점(entry-point-라우트)을 정의합니다. 이 파일에서 `Artisan::command` 함수를 사용하여 클로저 기반 라우트를 정의 할 수 있습니다. `command` 메소드는 [command signature](#defining-input-expectations), 그리고 명령어의 인자 및 옵션을 전달 받는 클로저의 총 두 개의 인자를 받습니다:

    Artisan::command('build {project}', function ($project) {
        $this->info("Building {$project}!");
    });

The Closure is bound to the underlying command instance, so you have full access to all of the helper methods you would typically be able to access on a full command class.

클로저는 기본 명령 인스턴스에 바인딩되므로 일반적으로 전체 명령 클래스에서 액세스 할 수있는 모든 헬퍼 함수에 대한 전체 액세스 권한을 갖습니다.

#### Type-Hinting Dependencies
#### 타입-힌팅 의존성

In addition to receiving your command's arguments and options, command Closures may also type-hint additional dependencies that you would like resolved out of the [service container](/docs/{{version}}/container):

명령어의 인자와 옵션을 전달 받는 것 이외에도 명령어 클로저는 [서비스 컨테이너](/docs/{{version}}/container) 에서 해결하고자 하는 추가적인 의존성을 입력 할 수 있습니다:

    use App\User;
    use App\DripEmailer;

    Artisan::command('email:send {user}', function (DripEmailer $drip, $user) {
        $drip->send(User::find($user));
    });

#### Closure Command Descriptions
#### 클로저 명령 설명 추가

When defining a Closure based command, you may use the `describe` method to add a description to the command. This description will be displayed when you run the `php artisan list` or `php artisan help` commands:

클로저 기반 명령을 정의 할 때`describe` 메소드를 사용하여 명령에 설명을 추가 할 수 있습니다. 이 설명은`php artisan list` 또는`php artisan help` 명령을 실행할 때 표시됩니다 :

    Artisan::command('build {project}', function ($project) {
        $this->info("Building {$project}!");
    })->describe('Build the project');

<a name="defining-input-expectations"></a>
## Defining Input Expectations
## 입력 값들 정의하기

When writing console commands, it is common to gather input from the user through arguments or options. Laravel makes it very convenient to define the input you expect from the user using the `signature` property on your commands. The `signature` property allows you to define the name, arguments, and options for the command in a single, expressive, route-like syntax.

콘솔 명령어를 작성할 때 인자 또는 옵션을 통해 사용자로부터 입력을 받는것은 일반적인 일입니다. 라라벨에서는 명령어의 `signature` 속성을 통해서 사용자로 부터 입력받을 값을 설명하는 것을 편리하게 적상할 수 있습니다. `signature` 속성은 여러분이 명령어를 위한 특정한 이름, 인자 그리고 옵션값들 표현하기 위한 간단하고, 라우트와 비슷한 문법을 지원합니다.

<a name="arguments"></a>
### Arguments
### 인자들

All user supplied arguments and options are wrapped in curly braces. In the following example, the command defines one **required** argument: `user`:

사용자로 부터 전달받을 인자 및 옵션은 모두 대괄호로 묶여져 있어야합니다. 다음 예제의 경우 **필수적으로** `user` 인자가 명령어에 필요하다고 정의하고 있습니다:

    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'email:send {user}';

You may also make arguments optional and define default values for arguments:

또한 다음처럼 인자를 선택적으로 입력 받을 수 있도록 정의할 수도 있습니다.

    // Optional argument...
    email:send {user?}

    // Optional argument with default value...
    email:send {user=foo}

<a name="options"></a>
### Options
### 옵션들

Options, like arguments, are another form of user input. Options are prefixed by two hyphens (`--`) when they are specified on the command line. There are two types of options: those that receive a value and those that don't. Options that don't receive a value serve as a boolean "switch". Let's take a look at an example of this type of option:

옵션또한 인자와 마찬가지로 사용자의 입력입니다. 그러나 명령어 라인에서 입력하는 경우, 두 개의 하이픈 (`--`)을 사용해야 됩니다. 그래서 signature에서 옵션 정의도 동일한 방식으로 합니다.

    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'email:send {user} {--queue}';

In this example, the `--queue` switch may be specified when calling the Artisan command. If the `--queue` switch is passed, the value of the option will be `true`. Otherwise, the value will be `false`:

이 예제에서, 아티즌 명령어가 실행될 때, `--queue` 스위치를 지정할 수 있습니다. `--queue` 스위치가 지정되면 옵션 값은 `true` 입니다. 그렇지 않으면, 값은 `false` 가됩니다

    php artisan email:send 1 --queue

<a name="options-with-values"></a>
#### Options With Values
#### 값에 의한 옵션들

Next, let's take a look at an option that expects a value. If the user must specify a value for an option, suffix the option name with a `=` sign:

다음으로, 값을 예상하는 옵션을 살펴 보겠습니다. 사용자가 옵션 값을 지정해야하는 경우 옵션 이름에 `=` 기호를 붙이십시오.

    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'email:send {user} {--queue=}';

In this example, the user may pass a value for the option like so:

이 예제에서, 다음과 같이 옵션 값을 지정합니다.

    php artisan email:send 1 --queue=default

You may assign default values to options by specifying the default value after the option name. If no option value is passed by the user, the default value will be used:

옵션 이름 뒤에 기본값을 지정하여 옵션에 기본값을 할당 할 수 있습니다. 사용자가 옵션 값을 전달하지 않으면 기본값이 사용됩니다.

    email:send {user} {--queue=default}

<a name="option-shortcuts"></a>
#### Option Shortcuts
#### 옵션의 짧은 표현

To assign a shortcut when defining an option, you may specify it before the option name and use a | delimiter to separate the shortcut from the full option name:

옵션을 정의 할 때 단축키를 지정하려면 전체 옵션 이름 앞에 |로 구분하여 단축키를 지정하면 됩니다.

    email:send {user} {--Q|queue}

<a name="input-arrays"></a>
### Input Arrays
### 입력 배열

If you would like to define arguments or options to expect array inputs, you may use the `*` character. First, let's take a look at an example that specifies an array argument:

인자나 옵션을 배열형태로 입력받기도록 정의하고자 한다면, `*` 문자를 사용하면 됩니다:

    email:send {user*}

When calling this method, the `user` arguments may be passed in order to the command line. For example, the following command will set the value of `user` to `['foo', 'bar']`:

이 함수를 호출 할 때`user` 인자는 명령 행에 순서대로 전달 될 수 있습니다. 예를 들어, 다음 명령은 `user`의 값을`[ 'foo', 'bar']`로 설정합니다. :

    php artisan email:send foo bar

When defining an option that expects an array input, each option value passed to the command should be prefixed with the option name:

배열 입력을 예상하는 옵션을 정의 할 때 명령에 전달 된 각 옵션 값 앞에 옵션 이름 접두사가 있어야합니다.

    email:send {user} {--id=*}

    php artisan email:send --id=1 --id=2

<a name="input-descriptions"></a>
### Input Descriptions
### 입력 설명하기

You may assign descriptions to input arguments and options by separating the parameter from the description using a colon. If you need a little extra room to define your command, feel free to spread the definition across multiple lines:

여러분은 입력 인자와 옵션에 대한 설명을 콜론으로 구분하여 지정할 수 있습니다.

    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'email:send
                            {user : The ID of the user}
                            {--queue= : Whether the job should be queued}';

<a name="command-io"></a>
## Command I/O
## 명령 입출력

<a name="retrieving-input"></a>
### Retrieving Input
### 입력 조회하기

While your command is executing, you will obviously need to access the values for the arguments and options accepted by your command. To do so, you may use the `argument` and `option` methods:

명령어가 실행되는 중에, 명령어가 전달 받은 인자 및 옵션의 값을 확인할 필요가 있을 수 있습니다. 이 경우에는 `argument` 메소드와 `option` 메소드를 사용할 수 있습니다.

    /**
     * Execute the console command.
     *
     * @return mixed
     */
    public function handle()
    {
        $userId = $this->argument('user');

        //
    }

If you need to retrieve all of the arguments as an `array`, call the `arguments` method:

모든 인자들을 `배열` 형태로 확인하고자 한다면 파라미터 없이 `argument` 메소드를 호출하면 됩니다.

    $arguments = $this->arguments();

Options may be retrieved just as easily as arguments using the `option` method. To retrieve all of the options as an array, call the `options` method:

옵션을 확인하는 방법은 인자를 확인하는 것과 마찬가지로 `option` 메소드를 호출하면 됩니다. `argument` 메소드처럼 파라미터 없이 `option` 메소드를 호출하는 경우에 모든 옵션값들을 `배열` 형태로 확인이 가능합니다.

    // Retrieve a specific option...
    $queueName = $this->option('queue');

    // Retrieve all options...
    $options = $this->options();

If the argument or option does not exist, `null` will be returned.

만약 인자나 옵션이 존재하지 않으면 `null`값이 반환됩니다.

<a name="prompting-for-input"></a>
### Prompting For Input
### 입력 프롬프트

In addition to displaying output, you may also ask the user to provide input during the execution of your command. The `ask` method will prompt the user with the given question, accept their input, and then return the user's input back to your command:

명령어가 실행되는 동안 화면에 문자를 출력하는데 더해, 사용자에게 입력받을 값을 물어볼 수 있습니다. `ask` 메소드는 사용자에게 질문을 표시하고 입력을 받아 명령어로 되돌려 줍니다.

    /**
     * Execute the console command.
     *
     * @return mixed
     */
    public function handle()
    {
        $name = $this->ask('What is your name?');
    }

The `secret` method is similar to `ask`, but the user's input will not be visible to them as they type in the console. This method is useful when asking for sensitive information such as a password:

`secret` 메소드는 `ask` 메소드와 비슷하지만 콘솔에서 사용자가 입력하는 동안 입력하는 값을 화면에 표시하지 않습니다. 이 메소드는 패스워드와 같은 민감한 정보를 물어볼 때 유용합니다.

    $password = $this->secret('What is the password?');

#### Asking For Confirmation
#### 사용자 확인받기

If you need to ask the user for a simple confirmation, you may use the `confirm` method. By default, this method will return `false`. However, if the user enters `y` or `yes` in response to the prompt, the method will return `true`.

단순히 사용자에게 확인 받고자 하는 경우에는 `confirm` 메소드를 사용하면 됩니다. 이 메소드는 기본값으로 `false` 를 반환합니다. 프롬프트에서 `y` 또는 `yes`를 입력하면 `true` 를 반환합니다.

    if ($this->confirm('Do you wish to continue?')) {
        //
    }

#### Auto-Completion
#### 자동완성

The `anticipate` method can be used to provide auto-completion for possible choices. The user can still choose any answer, regardless of the auto-completion hints:

`anticipate` 메소드는 입력가능한 값들에 대한 자동완성 기능을 제공합니다. 사용자는 자동완성되는 결과에 개의치 않고, 어떤 대답도 입력 할 수 있습니다.

    $name = $this->anticipate('What is your name?', ['Taylor', 'Dayle']);

#### Multiple Choice Questions
#### 여러개의 선택지

If you need to give the user a predefined set of choices, you may use the `choice` method. You may set the array index of the default value to be returned if no option is chosen:

미리 주어진 선택지를 사용자에게 제공하려는 경우에는 `choice` 메소드를 사용하면 됩니다. 사용자는 특정한 배열의 인덱스를 선택하고, 결과 값을 선택합니다. 아무것도 선택되지 않은 경우에는 기본값을 지정할 수도 있습니다.

    $name = $this->choice('What is your name?', ['Taylor', 'Dayle'], $defaultIndex);

<a name="writing-output"></a>
### Writing Output
### 출력 작성하기

To send output to the console, use the `line`, `info`, `comment`, `question` and `error` methods. Each of these methods will use appropriate ANSI colors for their purpose. For example, let's display some general information to the user. Typically, the `info` method will display in the console as green text:

콘솔에 출력하기 위해서는 `line`, `info`, `comment`, `question`, `error` 메소드를 사용합니다. 각각의 이름이 나타내는 목적에 맞게 사용되고 각각 적당한 ANSI 컬러로 표시됩니다. 사용자에게 정보 메시지를 알리는 경우에는 `info` 메소드를 사용합니다. 일반적으로 이 경우 녹색 텍스트가 콘솔에 표시됩니다. 

    /**
     * Execute the console command.
     *
     * @return mixed
     */
    public function handle()
    {
        $this->info('Display this on the screen');
    }

To display an error message, use the `error` method. Error message text is typically displayed in red:

에러 메시지 출력을 원하면, 일반적으로 빨간색으로 출력이 되는 `error` 메소드를 사용하면 됩니다.

    $this->error('Something went wrong!');

If you would like to display plain, uncolored console output, use the `line` method:

무색 콘솔 출력을 원하면 `line` 메소드를 사용하면 됩니다. :

    $this->line('Display this on the screen');

#### Table Layouts
#### 테이블 레이아웃

The `table` method makes it easy to correctly format multiple rows / columns of data. Just pass in the headers and rows to the method. The width and height will be dynamically calculated based on the given data:

`table` 메소드는 데이터의 여러 행 / 열에 대한 포맷을 구성할 수 있게 해줍니다. 간단하게 해더와 행을 메소드에 전달하면 됩니다. 폭과 높이는 주어진 데이터를 기반으로 동적으로 계산됩니다.

    $headers = ['Name', 'Email'];

    $users = App\User::all(['name', 'email'])->toArray();

    $this->table($headers, $users);

#### Progress Bars
#### 프로그래스 바 - 진행률 표시줄

For long running tasks, it could be helpful to show a progress indicator. Using the output object, we can start, advance and stop the Progress Bar. First, define the total number of steps the process will iterate through. Then, advance the Progress Bar after processing each item:

시간이 오래 걸리는 작업의 경우, 진행 상황을 표시하는 것이 도움이 됩니다. 출력 객체를 사용하여 시작, 진행중, 정지를 표시 할 수 있습니다. 진행률 표시줄을 시작할 때 여러 단계를 정의해야만 각 단계마다 진행 표시줄이 이동합니다.


    $users = App\User::all();

    $bar = $this->output->createProgressBar(count($users));

    $bar->start();

    foreach ($users as $user) {
        $this->performTask($user);

        $bar->advance();
    }

    $bar->finish();

For more advanced options, check out the [Symfony Progress Bar component documentation](https://symfony.com/doc/current/components/console/helpers/progressbar.html).

보다 자세한 옵션에 대해서는 [Symfony 진행률 표시줄 구성 컴포넌트 문서](https://symfony.com/doc/current/components/console/helpers/progressbar.html)  확인하십시오.

<a name="registering-commands"></a>
## Registering Commands
## 명령어 등록하기

Because of the `load` method call in your console kernel's `commands` method, all commands within the `app/Console/Commands` directory will automatically be registered with Artisan. In fact, you are free to make additional calls to the `load` method to scan other directories for Artisan commands:

콘솔 커널의 `commands` 메소드가 `load` 메소드를 호출하기 때문에, `app/Console/Commands` 디렉토리안에 들어 있는 모든 명령어들은 아티즌에 자동으로 등록됩니다. 또한, 아티즌 명령어들을 스캔하기 위한 다른 디렉토리를 `load` 메소드에서 자유롭게 추가할 수도 있습니다:

    /**
     * Register the commands for the application.
     *
     * @return void
     */
    protected function commands()
    {
        $this->load(__DIR__.'/Commands');
        $this->load(__DIR__.'/MoreCommands');

        // ...
    }

You may also manually register commands by adding its class name to the `$commands` property of your `app/Console/Kernel.php` file. When Artisan boots, all the commands listed in this property will be resolved by the [service container](/docs/{{version}}/container) and registered with Artisan:

또한, `app/Console/Kernel.php` 파일의 `$commands` 속성에 클래스 이름을 추가하여 수동으로 명령어를 등록할 수도 있습니다. 아티즌이 부팅될 때, 이 속성에서 나열된 모든 명령어어는 [서비스 컨테이너](/docs/{{version}}/container)에 의해서 자동으로 의존성이 해결되어 아티즌에 등록됩니다.

    protected $commands = [
        Commands\SendEmails::class
    ];

<a name="programmatically-executing-commands"></a>
## Programmatically Executing Commands
## 프로그래밍 방식의 명령 실행

Sometimes you may wish to execute an Artisan command outside of the CLI. For example, you may wish to fire an Artisan command from a route or controller. You may use the `call` method on the `Artisan` facade to accomplish this. The `call` method accepts either the command's name or class as the first argument, and an array of command parameters as the second argument. The exit code will be returned:

CLI 가 아니더라도 아티즌 명령어를 실행하고자 하는 경우가 있을 수 있습니다. 예를 들어 라우트 또는 컨트롤러에서 아티즌 명령어를 수행하기를 원할 수도 있습니다. 이 경우 `Artisan` 파사드의 `call` 메소드를 호출하면 됩니다. `call` 메소드는 첫번째 인자로 명령어 이름 또는 클래스를 인자로 받고, 명령어의 파라미터들로 구성된 배열을 두번째 인자로 전달받습니다. 결과 코드가 반환될 것입니다:

    Route::get('/foo', function () {
        $exitCode = Artisan::call('email:send', [
            'user' => 1, '--queue' => 'default'
        ]);

        //
    });

Alternatively, you may pass the entire Artisan command to the `call` method as a string:

또한, 아티즌 명령어를 전달하고자 할 경우  `call` 메소드를 이용합니다.

    Artisan::call('email:send 1 --queue=default');

Using the `queue` method on the `Artisan` facade, you may even queue Artisan commands so they are processed in the background by your [queue workers](/docs/{{version}}/queues). Before using this method, make sure you have configured your queue and are running a queue listener:

`Artisan` 파사드에서 `queue` 메소드를 호출하는 경우 [queue workers](/docs/{{version}}/queues)를 통해서 아티즌 명령어가 백그라운드로 실행됩니다.  이 메소드를 사용하기 전에 큐를 설정하고, 큐 리스너가 실행상태여야 합니다.

    Route::get('/foo', function () {
        Artisan::queue('email:send', [
            'user' => 1, '--queue' => 'default'
        ]);

        //
    });

You may also specify the connection or queue the Artisan command should be dispatched to:

또한 Artisan 명령어가 어떻게 처리되어야 하는지 커넥션 또는 큐를 지정할 수도 있습니다:

    Artisan::queue('email:send', [
        'user' => 1, '--queue' => 'default'
    ])->onConnection('redis')->onQueue('commands');

#### Passing Array Values
#### 배열값 전달하기

If your command defines an option that accepts an array, you may pass an array of values to that option:

명령어가 옵셥값으로 배열을 받도록 정의되어 있다면, 다음과 같이 배열 값을 전달 할 수 있습니다:

    Route::get('/foo', function () {
        $exitCode = Artisan::call('email:send', [
            'user' => 1, '--id' => [5, 13]
        ]);
    });

#### Passing Boolean Values
#### Boolean 값 전달하기

If you need to specify the value of an option that does not accept string values, such as the `--force` flag on the `migrate:refresh` command, you should pass `true` or `false`:

`migrate:refresh` 명령어와 같이 문자열이 아닌 옵션 값을들 필요로 하는 경우에는 `--force` 플래그를 통해서 `true` 또는 `false` 값을 전달할 수 있습니다.

    $exitCode = Artisan::call('migrate:refresh', [
        '--force' => true,
    ]);

<a name="calling-commands-from-other-commands"></a>
### Calling Commands From Other Commands
### 다른 명령어 호출하기

Sometimes you may wish to call other commands from an existing Artisan command. You may do so using the `call` method. This `call` method accepts the command name and an array of command parameters:

때때로 아티즌 명령어에서 다른 명령어를 호출해야할 수도 있습니다. 이 경우에는 `call` 메소드를 사용하면 됩니다. `call` 메소드는 명령어의 이름과 명령어 인자에 대한 배열을 전달받습니다.

    /**
     * Execute the console command.
     *
     * @return mixed
     */
    public function handle()
    {
        $this->call('email:send', [
            'user' => 1, '--queue' => 'default'
        ]);

        //
    }

If you would like to call another console command and suppress all of its output, you may use the `callSilent` method. The `callSilent` method has the same signature as the `call` method:

다른 콘솔 명령을 실행하면서 출력을 모두 무시하려면 `callSilent` 메소드를 사용하십시오. `callSilent` 메소드의 사용법은 `call` 메소드와 동일합니다.

    $this->callSilent('email:send', [
        'user' => 1, '--queue' => 'default'
    ]);
