# Artisan Development 아티즌 개발하기

- [Introduction 소개](#introduction)
- [Building A Command 명령어 만들기](#building-a-command)
- [Registering Commands 명령어 등록하기](#registering-commands)

<a name="introduction"></a>
## Introduction 소개

In addition to the commands provided with Artisan, you may also build your own custom commands for working with your application. 아티즌에서 제공하는 명령어에 추가적으로 애플리케이션에서 동작하는 자신의 고유한 명령어를 만들 수 있습니다. You may store your custom commands in the `app/Console/Commands` directory; however, you are free to choose your own storage location as long as your commands can be autoloaded based on your `composer.json` settings.  여러분의 고유한 명령어는 `app/Console/Commands` 디렉토리에 저장하면 됩니다만, 실제로는 명령어가 `composer.json` 세팅을 기반하여 오토도딩 될 수 있다면 어느 위치에 넣어도 상관없습니다.

<a name="building-a-command"></a>
## Building A Command 명령어 만들기

### Generating The Class 클래스 만들기

To create a new command, you may use the `make:console` Artisan command, which will generate a command stub to help you get started: 새로운 명령어를 생성하기 위해, 아티즌 명령어 `make:console`을 사용합니다. 

#### Generate A New Command Class 새로운 명령어 클래스 생성하기

	php artisan make:console FooCommand

The command above would generate a class at `app/Console/Commands/FooCommand.php`.

이 명령어는 `app/Console/Commands/FooCommand.php`파일을 생성할 것입니다. 

When creating the command, the `--command` option may be used to assign the terminal command name: 명령어를 생성할 때 `--command` 옵션을 사용하면 터미널에서 사용할 명령어 이름을 지정할 수 있습니다. 

	php artisan make:console AssignUsers --command=users:assign

### Writing The Command 명령어 작성하기

Once your command is generated, you should fill out the `name` and `description` properties of the class, which will be used when displaying your command on the `list` screen. 명령어가 생성되었으면, 클래스의 `name` 과 `description` 를 입력합니다. 이것들은 `list` 화면에 커맨드를 표시 할 때 사용됩니다.

The `fire` method will be called when your command is executed. `file` 메소드는 명령이 실행될 때 호출됩니다. You may place any command logic in this method. 수행할 로직들을 이 메소드 안에 작성하면 됩니다. 

### Arguments & Options 인자 & 옵션

The `getArguments` and `getOptions` methods are where you may define any arguments or options your command receives. 명령어가 입력 받는 인자와 옵션을 `getArguments` 와 `getOptions` 메소드에 정의합니다. Both of these methods return an array of commands, which are described by a list of array options. 이 두 메소드는 배열 옵션 목록에서 표시되는 커맨드의 배열을 반환합니다. 

When defining `arguments`, the array definition values represent the following:
`arguments` 를 정의하는 정의 배열은 다음과 같은 형식입니다. 

	[$name, $mode, $description, $defaultValue]

The argument `mode` may be any of the following: `InputArgument::REQUIRED` or `InputArgument::OPTIONAL`. 인자의 `mode`는 `InputArgument::REQUIRED` 또는 `InputArgument::OPTIONAL` 중 하나의 값을 가집니다. 

When defining `options`, the array definition values represent the following:
`options` 를 정의하는 배열은 다음과 같은 형식입니다.

	[$name, $shortcut, $mode, $description, $defaultValue]

For options, the argument `mode` may be: `InputOption::VALUE_REQUIRED`, `InputOption::VALUE_OPTIONAL`, `InputOption::VALUE_IS_ARRAY`, `InputOption::VALUE_NONE`. 옵션에서 `mode` 는 `InputOption:REQUIRED` , `InputOption::OPTIONAL` , `InputOption::VALUE_IS_ARRAY` , `InputOption::VALUE_NONE` 중 하나의 값을 가집니다. 

The `VALUE_IS_ARRAY` mode indicates that the switch may be used multiple times when calling the command: `VALUE_IS_ARRAY` 모드는 명령 실행시 여러 번 사용되는 스위치를 나타냅니다.

	InputOption::VALUE_REQUIRED | InputOption::VALUE_IS_ARRAY
	
Would then allow for this command:
위의 모드는 다음의 커맨드를 허용합니다:

	php artisan foo --option=bar --option=baz

The `VALUE_NONE` option indicates that the option is simply used as a "switch":
`VALUE_NONE` 옵션은 이 옵션이 단순히 스위치로 사용된다는 것을 나타냅니다.

	php artisan foo --option

### Retrieving Input 입력값 조회하기

While your command is executing, you will obviously need to access the values for the arguments and options accepted by your application. 명령이 실행중일 때, 응용 프로그램이 받은 인자 및 옵션의 값에 엑세스할 필요가 있습니다. To do so, you may use the `argument` and `option` methods: 이 경우 `argument` 와 `option`메소드를 사용하면 됩니다. 

#### Retrieving The Value Of A Command Argument 명령어 인자 값 조회

	$value = $this->argument('name');

#### Retrieving All Arguments 모든 인자 값들을 조회하기

	$arguments = $this->argument();

#### Retrieving The Value Of A Command Option 명령어 옵션 값 조회하기

	$value = $this->option('name');

#### Retrieving All Options 모든 옵션 값 조회하기

	$options = $this->option();

### Writing Output 출력 작성하기

To send output to the console, you may use the `info`, `comment`, `question` and `error` methods. Each of these methods will use the appropriate ANSI colors for their purpose. 콘솔에 출력하려면 `info`, `comment`, `question` 과 `error` 메소드를 사용하면 됩니다. 각 메소드는 용도에 따라 적절한 ANSI 컬러로 표시됩니다. 

#### Sending Information To The Console 콘솔에 정보 표시하기

	$this->info('Display this on the screen');

#### Sending An Error Message To The Console 콘솔에 에러 메세지 표시하기

	$this->error('Something went wrong!');

### Asking Questions 질문하기 

You may also use the `ask` and `confirm` methods to prompt the user for input:
사용자 입력을 요청하는 `ask`와 `confirm` 메소드도 사용할 수 있습니다. 

#### Asking The User For Input 사용자에게 입력을 요구하기

	$name = $this->ask('What is your name?');

#### Asking The User For Secret Input 사용자에게 비밀 문자열 입력을 요구하기

	$password = $this->secret('What is the password?');

#### Asking The User For Confirmation 사용자에게 확인을 요구하기

	if ($this->confirm('Do you wish to continue? [yes|no]'))
	{
		//
	}

You may also specify a default value to the `confirm` method, which should be `true` or `false`: `confirm` 메소드에 대한 기본값을 `true`와 `false` 로 지정할 수 있습니다. 

	$this->confirm($question, true);

### Calling Other Commands 다른 명령어 호출하기 

Sometimes you may wish to call other commands from your command. 때로는 해당 명령어가 다른 명령어를 호출해야할 때가 있습니다. You may do so using the `call` method: 이 경우에 `call` 메소드를 사용하면 됩니다. 

	$this->call('command:name', ['argument' => 'foo', '--option' => 'bar']);

<a name="registering-commands"></a>
## Registering Commands 명령어 등록하기

#### Registering An Artisan Command
#### 아티즌 명령어 등록하기

Once your command is finished, you need to register it with Artisan so it will be available for use. 명령어 작성이 완료되면 명령어를 사용가능하도록 아티즌에 등록할 필요가 있습니다. This is typically done in the `app/Console/Kernel.php` file. 이 작업은 일반적으로 `app/Console/Kernel.php` 파일에서 이루어 집니다. Within this file, you will find a list of commands in the `commands` property. 이 파일에서, 명령어들의 목록을 담고 있는 `command` 변수를 찾을 수 있을 것입니다. To register your command, simply add it to this list. 여러분이 작성한 명령어를 등록하려면 해당 배열에 생성한 명령어 클래스를 추가하십시오. 

	protected $commands = [
		'App\Console\Commands\FooCommand'
	];

When Artisan boots, all the commands listed in this property will be resolved by the [service container](/docs/{{version}}/container) and registered with Artisan. 아티즌이 구동되면서 변수내의 모든 명령어들의 목록을 참고하여 [service container](/docs/{{version}}/container)들이 자동으로 실행가능한 상태로 아티즌에 등록됩니다. 
