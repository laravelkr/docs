# Artisan Console
# 아티즌 콘솔 명령

- [Introduction](#introduction)
- [소개](#introduction)
- [Writing Commands](#writing-commands)
- [명령어 작성하기](#writing-commands)
    - [Command Structure](#command-structure)
    - [명령어의 구조](#command-structure)
- [Command I/O](#command-io)
- [명령어 I/O](#command-io)
    - [Defining Input Expectations](#defining-input-expectations)
    - [입력받을 값들 지정하기](#defining-input-expectations)
    - [Retrieving Input](#retrieving-input)
    - [입력 확인하기](#retrieving-input)
    - [Prompting For Input](#prompting-for-input)
    - [입력 프롬프트](#prompting-for-input)
    - [Writing Output](#writing-output)
    - [출력 작성하기](#writing-output)
- [Registering Commands](#registering-commands)
- [명령어 등록하기](#registering-commands)
- [Calling Commands Via Code](#calling-commands-via-code)
- [코드 상에서 명령어 호출하기](#calling-commands-via-code)

<a name="introduction"></a>
## Introduction
## 소개

Artisan is the name of the command-line interface included with Laravel. It provides a number of helpful commands for your use while developing your application. It is driven by the powerful Symfony Console component. To view a list of all available Artisan commands, you may use the `list` command:

아티즌(Artisan)은 라라벨에 포함된 커맨드라인 인터페이스(CLI)의 이름입니다. 아티즌은 어플리케이션 개발에 도움을 주는 많은 명령어들을 제공합니다. 아티즌은 강력한 Symfony 콘솔 콤포넌트에서 동작합니다. 실행가능한 아티즌 명령어 목록을 확인하려면 `list` 명령어를 다음과 같이 입력하면 됩니다.

    php artisan list

Every command also includes a "help" screen which displays and describes the command's available arguments and options. To view a help screen, simply precede the name of the command with `help`:

모든 명령어들은 "도움말" 기능을 포함하고 있어서 해당 명령어의 인자와, 옵션들을 확인할 수 있습니다. 도움말 화면을 확인하기 위해서는 다음과 같이 명령어 앞에 `help` 를 추가하여 실행하면 됩니다.

    php artisan help migrate

<a name="writing-commands"></a>
## Writing Commands
## 명령어 작성하기

In addition to the commands provided with Artisan, you may also build your own custom commands for working with your application.You may store your custom commands in the `app/Console/Commands` directory; however, you are free to choose your own storage location as long as your commands can be autoloaded based on your `composer.json` settings.

아티즌에서 제공하는 명령어들에 더하여 추가적으로 어플리케이션에서 동작하는 여러분 자신의 고유한 명령어를 만들 수 있습니다. 여러분의 고유한 명령어는 `app/Console/Commands` 디렉토리에 저장하면 됩니다만, 실제로는 명령어가 `composer.json` 세팅을 기반하여 오토도딩 될 수 있다면 어느 위치에 넣어도 상관없습니다.

To create a new command, you may use the `make:console` Artisan command, which will generate a command stub to help you get started:

새로운 명령어를 생성하기 위해, 아티즌 명령어 `make:console`을 사용합니다.

    php artisan make:console SendEmails

The command above would generate a class at `app/Console/Commands/SendEmails.php`. When creating the command, the `--command` option may be used to assign the terminal command name:

이 명령어를 실행하면 `app/Console/Commands/SendEmails.php` 파일이 생성될 것입니다. 명령어를 생성할 때 `--command` 옵션을 사용하면 터미널에서 사용할 명령어의 이름을 지정할 수 있습니다.

    php artisan make:console SendEmails --command=emails:send

<a name="command-structure"></a>
### Command Structure
### 명령어 구조

Once your command is generated, you should fill out the `signature` and `description` properties of the class, which will be used when displaying your command on the `list` screen.

명령어가 생성되었으면, 클래스의 `signature` 과 `description` 를 입력합니다. 이것들은 `list` 화면에 커맨드를 표시 할 때 사용됩니다.

The `handle` method will be called when your command is executed. You may place any command logic in this method. Let's take a look at an example command.

명령어가 실행될 때에는 `handle` 메소드가 호출됩니다. 여러분이 구성하고자 하는 로직을 이 메소드에 넣으면 됩니다. 간단한 예제를 살펴보겠습니다.

Note that we are able to inject any dependencies we need into the command's constructor. The Laravel [service container](/docs/{{version}}/container) will automatically inject all dependencies type-hinted in the constructor. For greater code reusability, it is good practice to keep your console commands light and let them defer to application services to accomplish their tasks.

명령어 클래스의 생성자에서는 필요한 의존성 주입이 가능합니다. 라라벨의 [서비스 컨테이너](/docs/5.1/container) 는 자동으로 생성자에서 타입이 지정된 의존객체들을 주입해 줄 것입니다. 보다 나은 코드 재사용성을 위해, 콘솔 명령어를 가볍게 유지하고 어플리케이션 서비스들이 해당 작업을 수행할 수 있도록 하는 것이 좋습니다.

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
         * The drip e-mail service.
         *
         * @var DripEmailer
         */
        protected $drip;

        /**
         * Create a new command instance.
         *
         * @param  DripEmailer  $drip
         * @return void
         */
        public function __construct(DripEmailer $drip)
        {
            parent::__construct();

            $this->drip = $drip;
        }

        /**
         * Execute the console command.
         *
         * @return mixed
         */
        public function handle()
        {
            $this->drip->send(User::find($this->argument('user')));
        }
    }

<a name="command-io"></a>
## Command I/O
## 명령어 I/O

<a name="defining-input-expectations"></a>
### Defining Input Expectations
### 입력받을 값들 지정하기

When writing console commands, it is common to gather input from the user through arguments or options. Laravel makes it very convenient to define the input you expect from the user using the `signature` property on your commands. The `signature` property allows you to define the name, arguments, and options for the command in a single, expressive, route-like syntax.

콘솔 명령어를 작성할 때 인자 또는 옵션을 통해 사용자로부터 입력을 받는것은 일반적인 일입니다. 라라벨에서는 명령어의 `signature` 속성을 통해서 사용자로 부터 입력받을 값을 설명하는 것을 편리하게 적상할 수 있습니다. `signature` 속성은 여러분이 명령어를 위한 특정한 이름, 인자 그리고 옵션값들 표현하기 위한 간단하고, 라우트와 비슷한 문법을 지원합니다.

All user supplied arguments and options are wrapped in curly braces. In the following example, the command defines one **required** argument: `user`:

사용자로 부터 전달받을 인자 및 옵션은 모두 대괄호로 묶여져 있어야합니다. 다음 예제의 경우 **필수적으로** `user` 인자가 명령어에 필요하다고 정의하고 있습니다.

    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'email:send {user}';

You may also make arguments optional and define default values for optional arguments:

또한 다음처럼 인자를 선택적으로 입력 받을 수 있도록 정의할 수도 있습니다.

    // Optional argument...
    email:send {user?}

    // Optional argument with default value...
    email:send {user=foo}

Options, like arguments, are also a form of user input. However, they are prefixed by two hyphens (`--`) when they are specified on the command line. We can define options in the signature like so:

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

You may also specify that the option should be assigned a value by the user by suffixing the option name with a `=` sign, indicating that a value should be provided:

사용자가 옵션 값을 지정해야만 하도록 할 수 있으며, 이 경우 옵션 이름의 마지막에 `=` 기호를 붙이면 됩니다.

    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'email:send {user} {--queue=}';

In this example, the user may pass a value for the option like so:

이 예제에서, 다음과 같이 옵션 값을 지정합니다.

    php artisan email:send 1 --queue=default

You may also assign default values to options:

또는 옵션의 기본값을 설정할 수도 있습니다.

    email:send {user} {--queue=default}

To assign a shortcut when defining an option, you may specify it before the option name and use a | delimiter to separate the shortcut from the full option name:

옵션을 정의 할 때 단축키를 지정하려면 전체 옵션 이름 앞에 |로 구분하여 단축키를 지정하면 됩니다.

    email:send {user} {--Q|queue}

#### Input Descriptions
#### 입력 설명하기

You may assign descriptions to input arguments and options by separating the parameter from the description using a colon:

여러분은 입력 인자와 옵션에 대한 설명을 콜론으로 구분하여 지정할 수 있습니다.

    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'email:send
                            {user : The ID of the user}
                            {--queue= : Whether the job should be queued}';

<a name="retrieving-input"></a>
### Retrieving Input
### 입력값 조회하기

While your command is executing, you will obviously need to access the values for the arguments and options accepted by your command. To do so, you may use the `argument` and `option` methods:

명령어가 실행되는 중에, 명령어가 전달 받은 인자 및 옵션의 값을 확인할 필요가 있을 수 있습니다. 이 경우에는 `argument` 메소드와 `option` 메소드를 사용할 수 있습니다.

To retrieve the value of an argument, use the `argument` method:

인자들의 값을 확인하기 위해서는 `argument` 메소드를 사용하면 됩니다.

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

If you need to retrieve all of the arguments as an `array`, call `argument` with no parameters:

모든 인자들을 `배열` 형태로 확인하고자 한다면 파라미터 없이 `argument` 메소드를 호출하면 됩니다.

    $arguments = $this->argument();

Options may be retrieved just as easily as arguments using the `option` method. Like the `argument` method, you may call `option` without any parameters in order to retrieve all of the options as an `array`:

옵션을 확인하는 방법은 인자를 확인하는 것과 마찬가지로 `option` 메소드를 호출하면 됩니다. `argument` 메소드처럼 파라미터 없이 `option` 메소드를 호출하는 경우에 모든 옵션값들을 `배열` 형태로 확인이 가능합니다.

    // Retrieve a specific option...
    $queueName = $this->option('queue');

    // Retrieve all options...
    $options = $this->option();

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

If you need to ask the user for a simple confirmation, you may use the `confirm` method. By default, this method will return `false`. However, if the user enters `y` in response to the prompt, the method will return `true`.

단순히 사용자에게 확인 받고자 하는 경우에는 `confirm` 메소드를 사용하면 됩니다. 이 메소드는 기본값으로 `false` 를 반환합니다. 프롬프트에서 `y` 를 입력하면 `true` 를 반환합니다.

    if ($this->confirm('Do you wish to continue? [y|N]')) {
        //
    }

#### Giving The User A Choice
#### 사용자 선택

The `anticipate` method can be used to provided autocompletion for possible choices. The user can still choose any answer, regardless of the choices.


`anticipate` 메소드는 입력가능한 값들에 대한 자동완성 기능을 제공합니다. 사용자는 선택지와 더불어 다른 값도 입력 할 수 있습니다.

    $name = $this->anticipate('What is your name?', ['Taylor', 'Dayle']);

If you need to give the user a predefined set of choices, you may use the `choice` method. The user chooses the index of the answer, but the value of the answer will be returned to you. You may set the default value to be returned if nothing is chosen:

미리 주어진 선택지를 사용자에게 제공하려는 경우에는 `choice` 메소드를 사용하면 됩니다. 사용자는 특정한 값을 선택하고, 결과 값이 돌아옵니다. 아무것도 선택되지 않은 경우에는 기본값을 지정할 수도 있습니다.

    $name = $this->choice('What is your name?', ['Taylor', 'Dayle'], false);

<a name="writing-output"></a>
### Writing Output
### 출력 작성하기

To send output to the console, use the `line`, `info`, `comment`, `question` and `error` methods. Each of these methods will use the appropriate ANSI colors for their purpose.

콘솔에 출력하기 위해서는 `line`, `info`, `comment`, `question`, `error` 메소드를 사용합니다. 각각의 이름이 나타내는 목적에 맞게 사용되고 각각 적당한 ANSI 컬러로 표시됩니다.

To display an information message to the user, use the `info` method. Typically, this will display in the console as green text:

사용자에게 정보 메시지를 알리는 경우에는 `info` 메소드를 사용합니다. 일반적으로 이 경우 녹색 텍스트가 콘솔에 표시됩니다.

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

오류 메시지를 표시하는 경우, `error` 메소드를 사용하면 됩니다. 오류 메시지는 일반적으로 빨간색으로 표시됩니다.

    $this->error('Something went wrong!');

If you want to display plain console output, use the `line` method. The `line` method does not receive any unique coloration:

간단한 콘솔 출력을 하려면 `line` 메소드를 사용하면 됩니다. `line` 메소드는 특정한 색상을 사용하지는 않습니다.

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

For long running tasks, it could be helpful to show a progress indicator. Using the output object, we can start, advance and stop the Progress Bar. You have to define the number of steps when you start the progress, then advance the Progress Bar after each step:

시간이 오래 걸리는 작업의 경우, 진행 상황을 표시하는 것이 도움이 됩니다. 출력 객체를 사용하여 시작, 진행중, 정지를 표시 할 수 있습니다. 진행률 표시줄을 시작할 때 여러 단계를 정의해야만 각 단계마다 진행 표시줄이 이동합니다.

    $users = App\User::all();

    $bar = $this->output->createProgressBar(count($users));

    foreach ($users as $user) {
        $this->performTask($user);

        $bar->advance();
    }

    $bar->finish();

For more advanced options, check out the [Symfony Progress Bar component documentation](http://symfony.com/doc/2.7/components/console/helpers/progressbar.html).

보다 자세한 옵션에 대해서는 [Symfony 진행률 표시줄 구성 컴포넌트 문서](http://symfony.com/doc/2.7/components/console/helpers/progressbar.html)  확인하십시오.

<a name="registering-commands"></a>
## Registering Commands
## 명령어 등록하기

Once your command is finished, you need to register it with Artisan so it will be available for use. This is done within the `app/Console/Kernel.php` file.

명령어 작성이 완료되면 아티즌에서 사용할 수 있도록 등록할 필요가 있습니다. 이 작업은 일반적으로 `app/Console/Kernel.php` 파일에서 이루어 집니다.

Within this file, you will find a list of commands in the `commands` property. To register your command, simply add the class name to the list. When Artisan boots, all the commands listed in this property will be resolved by the [service container](/docs/{{version}}/container) and registered with Artisan:

이 파일 안에서, 명령어들의 목록을 담고 있는 `command` 변수를 찾을 수 있을 것입니다. 명령어를 등록하기 위해서 간단하게 목록에 작성한 클래스 이름을 추가하십시오. 아티즌이 구동할 때, 목록 안의 모든 명령어들은 [서비스 컨테이너](/docs/5.1/container)에 의해서 의존성이 해결된 상태로 생성되어 아티즌에 등록되어 집니다.

    protected $commands = [
        Commands\SendEmails::class
    ];

<a name="calling-commands-via-code"></a>
## Calling Commands Via Code
## 코드 상에서 명령어 호출하기

Sometimes you may wish to execute an Artisan command outside of the CLI. For example, you may wish to fire an Artisan command from a route or controller. You may use the `call` method on the `Artisan` facade to accomplish this. The `call` method accepts the name of the command as the first argument, and an array of command parameters as the second argument. The exit code will be returned:

CLI 가 아니더라도 아티즌 명령어를 실행하고자 하는 경우가 있을 수 있습니다. 예를 들어 라우트 또는 컨트롤러에서 아티즌 명령어를 수행하기를 원할 수도 있습니다. 이 경우 `Artisan` 파사드의 `call` 메소드를 호출하면 됩니다. `call` 메소드는 명령어의 이름을 첫번째 인자로 전달받고 명령어의 파라미터들로 구성된 배열을 두번째 인자로 전달받습니다. 결과 코드가 반환될 것입니다:

    Route::get('/foo', function () {
        $exitCode = Artisan::call('email:send', [
            'user' => 1, '--queue' => 'default'
        ]);

        //
    });

Using the `queue` method on the `Artisan` facade, you may even queue Artisan commands so they are processed in the background by your [queue workers](/docs/{{version}}/queues):

`Artisan` 파사드에서 `queue` 메소드를 호출하는 경우 [queue workers](/docs/5.1/queues)를 통해서 아티즌 명령어가 백그라운드로 실행됩니다.

    Route::get('/foo', function () {
        Artisan::queue('email:send', [
            'user' => 1, '--queue' => 'default'
        ]);

        //
    });

If you need to specify the value of an option that does not accept string values, such as the `--force` flag on the `migrate:refresh` command, you may pass a boolean `true` or `false`:

`migrate:refresh` 명령어와 같이 문자열을 받지 못하는 지정된 옵션 값을들 필요로 하는 경우에는 `--force` 플래그를 통해서 `ture` 또는 `false` 값을 전달할 수 있습니다.

    $exitCode = Artisan::call('migrate:refresh', [
        '--force' => true,
    ]);

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
