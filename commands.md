# 아티즌 명령어 개발하기(Artisan Development)

- [소개](#introduction)
- [명령어 만들기](#building-a-command)
- [명령어 등록하기](#registering-commands)


<a name="introduction"></a>
## 소개

아티즌에서 제공하는 명령어에 추가로 어플리케이션에서 동작하는 자신의 고유한 명령어를 만들 수 있습니다. 여러분의 고유한 명령어는 `app/Console/Commands` 디렉토리에 저장하면 됩니다만, 실제로는 명령어가 `composer.json` 세팅을 기반하여 오토로딩 될 수 있다면 어느 위치에 넣어도 상관없습니다.

<!--chak-comment-아티즌-명령어-개발하기(Artisan-Development)-소개-->

<a name="building-a-command"></a>
## 명령어 만들기

### 클래스 생성하기

새로운 명령어를 생성하기 위해 아티즌 명령어 `make:console`을 사용합니다:

#### 새로운 명령어 클래스 생성하기

	php artisan make:console FooCommand

이 명령어는 `app/Console/Commands/FooCommand.php`파일을 생성할 것입니다.

명령어를 생성할 때 `--command` 옵션을 사용하면 터미널에서 사용할 명령어 이름을 지정할 수 있습니다:

	php artisan make:console AssignUsers --command=users:assign

### 명령어 작성하기

명령어가 생성되면 클래스의 `name`과 `description`을 입력합니다. 이것들은 `list` 화면에 커맨드를 표시 할 때 사용됩니다.

`file` 메소드는 명령이 실행될 때 호출됩니다. 수행할 로직을 이 메소드 안에 작성하면 됩니다.

### 인자 & 옵션

명령어가 입력받는 인자와 옵션을 `getArguments`와 `getOptions` 메소드에 정의합니다. 이 두 메소드는 배열 옵션 목록에서 표시되는 커맨드의 배열을 반환합니다.

`arguments`를 정의하는 정의 배열은 다음과 같은 형식입니다:

	[$name, $mode, $description, $defaultValue]

인자의 `mode`는 `InputArgument::REQUIRED` 또는 `InputArgument::OPTIONAL` 중 하나의 값을 가집니다.

`options`를 정의하는 배열은 다음과 같은 형식입니다:

	[$name, $shortcut, $mode, $description, $defaultValue]

옵션에서 `mode`는 `InputOption:REQUIRED`, `InputOption::OPTIONAL`, `InputOption::VALUE_IS_ARRAY`, `InputOption::VALUE_NONE` 중 하나의 값을 가집니다.

`VALUE_IS_ARRAY` 모드는 명령 실행 시 여러 번 사용되는 스위치를 나타냅니다:

	InputOption::VALUE_REQUIRED | InputOption::VALUE_IS_ARRAY

위의 모드는 다음의 커맨드를 허용합니다:

	php artisan foo --option=bar --option=baz

`VALUE_NONE` 옵션은 이 옵션이 단순히 스위치로 사용된다는 것을 나타냅니다:

	php artisan foo --option

### Retrieving Input 입력값 조회하기

While your command is executing, you will obviously need to access the values for the arguments and options accepted by your application. 명령이 실행중일 때, 어플리케이션이 받은 인자 및 옵션의 값에 엑세스할 필요가 있습니다. To do so, you may use the `argument` and `option` methods: 이 경우 `argument`와 `option`메소드를 사용하면 됩니다.

#### 명령어 인자 값 조회

	$value = $this->argument('name');

#### 모든 인자 값을 조회하기

	$arguments = $this->argument();

#### 명령어 옵션 값 조회하기

	$value = $this->option('name');

#### 모든 옵션 값 조회하기

	$options = $this->option();

### 출력 작성하기

콘솔에 출력하려면 `info`, `comment`, `question`과 `error` 메소드를 사용하면 됩니다. 각 메소드는 용도에 따라 적절한 ANSI 컬러로 표시됩니다.

#### 콘솔에 정보 표시하기

	$this->info('Display this on the screen');

#### 콘솔에 에러 메세지 표시하기

	$this->error('Something went wrong!');

### 질문하기

사용자 입력을 요청하는 `ask`와 `confirm` 메소드도 사용할 수 있습니다:

#### 사용자에게 입력을 요구하기

	$name = $this->ask('What is your name?');

#### 사용자에게 비밀 문자열 입력을 요구하기

	$password = $this->secret('What is the password?');

#### 사용자에게 확인을 요구하기

	if ($this->confirm('Do you wish to continue? [yes|no]'))
	{
		//
	}

`confirm` 메소드에 대한 기본값을 `true`와 `false`로 지정할 수 있습니다:

	$this->confirm($question, true);

### 다른 명령어 호출하기

때로는 해당 명령어가 다른 명령어를 호출해야 할 때가 있습니다. 이 경우에 `call` 메소드를 사용하면 됩니다:

	$this->call('command:name', ['argument' => 'foo', '--option' => 'bar']);

<!--chak-comment-아티즌-명령어-개발하기(Artisan-Development)-명령어-만들기-->

<a name="registering-commands"></a>
## 명령어 등록하기

#### 아티즌 명령어 등록하기

명령어 작성이 완료되면 명령어를 사용가능하도록 아티즌에 등록해야 합니다. 이 작업은 일반적으로 `app/Console/Kernel.php` 파일에서 이루어 집니다. 이 파일에서 명령어들의 목록을 담고 있는 `command` 변수를 찾을 수 있을 것입니다. 여러분이 작성한 명령어를 등록하려면 해당 배열에 생성한 명령어 클래스를 추가하십시오.

	protected $commands = [
		'App\Console\Commands\FooCommand'
	];

아티즌이 구동되면서 변수 내의 모든 명령어의 목록을 참고하여 [service container](/docs/5.0/container)들이 자동으로 실행 가능한 상태로 아티즌에 등록됩니다.

<!--chak-comment-아티즌-명령어-개발하기(Artisan-Development)-명령어-등록하기-->
