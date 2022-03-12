# 아티즌 콘솔

- [시작하기](#introduction)
    - [Tinker (REPL)](#tinker)
- [명령어 작성하기](#writing-commands)
    - [명령 생성](#generating-commands)
    - [명령어의 구조](#command-structure)
    - [클로저 명령](#closure-commands)
- [입력 값들 정의하기](#defining-input-expectations)
    - [인자들](#arguments)
    - [옵션들](#options)
    - [배열 입력](#input-arrays)
    - [설명 입력](#input-descriptions)
- [명령어 입출력](#command-io)
    - [입력 조회](#retrieving-input)
    - [입력 프롬프트](#prompting-for-input)
    - [출력 기록](#writing-output)
- [명령어 등록하기](#registering-commands)
- [프로그래밍 방식으로 명령 실행](#programmatically-executing-commands)
    - [다른 명령에서 명령 호출](#calling-commands-from-other-commands)
- [시그널 처리](#signal-handling)
- [Stub 커스터마이징](#stub-customization)
- [이벤트](#events)

<a name="introduction"></a>
## 시작하기

아티즌(Artisan)은 라라벨에 포함된 커맨드라인 인터페이스(CLI)의 이름입니다. Artisan은 `artisan` 스크립트 파일로 애플리케이션의 최상위 폴더에 존재하며 애플리케이션을 빌드하는 동안 도움이 될 수있는 여러가지 유용한 명령을 제공합니다. 실행가능한 아티즌 명령어 목록을 확인하려면 `list` 명령어를 다음과 같이 입력하면 됩니다.

    php artisan list

모든 명령어들은 "도움말" 기능을 포함하고 있어서 해당 명령어의 인자와, 옵션들을 확인할 수 있습니다. 도움말 화면을 확인하기 위해서는 다음과 같이 명령어 앞에 `help` 를 추가하여 실행하면 됩니다.

    php artisan help migrate

<a name="laravel-sail"></a>
#### Laravel Sail

로컬 개발 환경으로 [Laravel Sail](/docs/{{version}}/sail)을 사용하는 중이라면 `sail` 명령어을 사용하여 아티즌을 호출해야합니다. Sail은 Docker 컨테이너 내에서 아티즌을 실행해줍니다.

    ./sail artisan list

<a name="tinker"></a>
### Tinker (REPL)

라라벨 Tinker는 [PsySH](https://github.com/bobthecow/psysh) 패키지로 구동되는 Laravel 프레임워크를 위한 강력한 REPL입니다.

<a name="installation"></a>
#### Installation

모든 라라벨 애플리케이션은 기본적으로 Tinker를 포함합니다. 이전에 애플리케이션에서 Tinker를 삭제했을 경우 Composer를 사용하여 Tinker를 다시 설치할 수 있습니다.

    composer require laravel/tinker

> {tip} 라라벨 애플리케이션과 대화하기위한 그래픽 UI를 찾고 계십니까? [Tinkerwell](https://tinkerwell.app)을 확인해보세요!

<a name="usage"></a>
#### 사용법

Tinker를 사용하면 커맨드 라인에서 Eloquent 모델, Job, 이벤트 등을 포함하는 라라벨 애플리케이션 전체를 사용할 수 있습니다. Tinker에 들어가려면 `tinker` Artisan 커맨드를 실행하십시오.

    php artisan tinker

`vendor:publish` 명령어를 사용하여 Tinker의 설정 파일을 별도로 만들 수 있습니다.

    php artisan vendor:publish --provider="Laravel\Tinker\TinkerServiceProvider"

> {note} `dispatch` 헬퍼 함수와 `Dispatchable` 클래스의 `dispatch` 메소드는 job을 큐에 배치하기 위해 가비지 콜렉션에 의존합니다. 따라서, tinker를 사용할 때는 `Bus::dispatch` 또는 `Queue::push`를 사용하여 job을 전달해야 합니다.

<a name="command-allow-list"></a>
#### 명령어 허용 리스트

Tinker 는 어떤 아티즌 명령어들이 쉘(shell) 에서 구동할 수 있는지 허용 리스트를 구성합니다. 기본적으로 여러분은 `clear-compiled`, `down`, `env`, `inspire`, `migrate`, `optimize` 그리고 `up` 명령어를 실행할 수 있습니다. 더 많은 명령어들을 허용 리스트에 추가하고자 한다면, `tinker.php` 설정 파일의 `commands` 배열에 추가 하면됩니다.

    'commands' => [
        // App\Console\Commands\ExampleCommand::class,
    ],

<a name="classes-that-should-not-be-aliased"></a>
#### 별칭-Aliased 처리하지 않는 클래스

일반적으로 Tinker는 필요한 클래스와 상호 작용할 때 자동으로 별칭(alias)을 지정합니다. 하지만 일부 클래스는 별칭을 지정하지 않을 수도 있습니다. `tinker.php` 설정 파일의 `dont_alias` 배열에 클래스를 추가하면 됩니다.

    'dont_alias' => [
        App\Models\User::class,
    ],

<a name="writing-commands"></a>
## 명령어 작성하기

아티즌에서 제공하는 명령어들 외에에도 원하는 명령어를 만들 수 있습니다. 일반적으로 명령어는 `app/Console/Commands` 디렉토리에 저장하지만, 컴포저를 이용하여 커맨드를 로드할 수만 있다면 다른 원하는 위치에 저장해도 상관없습니다.

<a name="generating-commands"></a>
### 명령어 생성하기

새로운 명령어를 생성할 땐, 아티즌 명령어 `make:command` 을 사용하면 됩니다. 이 명령어는 `app/Console/Commands` 디렉토리에 새로운 명령어 클래스를 만들어줍니다. 이 디렉토리는 `make:command` 아티즌 명령을 처음 실행할 때 자동으로 생성되기 때문에, 기존에 이 디렉토리가 애플리케이션에 존재하지 않는다고 걱정할 필요는 없습니다.

    php artisan make:command SendEmails

<a name="command-structure"></a>
### 명령어 구조

명령어를 생성 한 후 클래스의 `signature`와 `description` 속성에 적절한 값을 지정해줘야합니다. 이 속성은 화면에 `list` 명령을 표시 할 때 사용됩니다. `signature` 속성을 사용하면 [명령어의 입력 기대 값](#defining-input-expectations)을 정의 할 수도 있습니다. 명령어가 실행되면 `handle` 메소드가 호출됩니다. 이 메소드에 실행할 로직들을 작성하면 됩니다.

예제 명령어을 살펴 보겠습니다. 명령어의 `handle` 메소드에 실행될 때 필요한 모든 의존성들을 작성할 수 있습니다. 그러면 Laravel [서비스 컨테이너](/docs/{{version}}/container)에서 이 메소드에 타입 힌트가 있는 모든 의존성을 자동으로 주입해줍니다.

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
         * @param  \App\Support\DripEmailer  $drip
         * @return mixed
         */
        public function handle(DripEmailer $drip)
        {
            $drip->send(User::find($this->argument('user')));
        }
    }

> {tip} 코드 재 사용률을 높이려면 콘솔 명령어을 가볍게 유지하고 작업을 수행하기 위해 애플리케이션 서비스를 불러오도록 하는 것이 좋습니다. 위의 예제에서 우리는 이메일을 보내는 "중요한 작업"을 수행하기 위해 서비스 클래스를 주입 받았습니다.

<a name="closure-commands"></a>
### 클로저 명령어

클로저 기반 명령어는 콘솔 명령어을 클래스로 정의하는 대신에 사용할 수 있습니다. 라우트에서 클로저를 컨트롤러 대신 쓸 수 있는 것처럼, 같은 방식으로 명령어 클래스 대신 명령어 클로저를 사용한다고 생각하면 됩니다. `app/Console/Kernel.php` 파일의 `commands` 함수 내에서 라라벨은 `routes/console.php` 파일을 로드합니다.

    /**
     * Register the closure based commands for the application.
     *
     * @return void
     */
    protected function commands()
    {
        require base_path('routes/console.php');
    }

이 파일은 HTTP로 접속하는 경로를 정의하지 않고, 애플리케이션에 콘솔 기반의 진입점(entry-point-라우트)을 정의합니다. 이 파일에서 `Artisan::command` 함수를 사용하여 클로저 기반의 콘솔 커맨드를 정의 할 수 있습니다. `command` 메소드는 [command signature](#defining-input-expectations), 그리고 명령어의 인자 및 옵션을 전달 받는 클로저의 총 두 개의 인자를 받습니다.

    Artisan::command('mail:send {user}', function ($user) {
        $this->info("Sending email to: {$user}!");
    });

클로저는 기본 명령어 인스턴스에 바인딩되므로 일반적으로 전체 명령어 클래스에서 액세스 할 수있는 모든 헬퍼 함수에 대한 전체 접근 권한을 가집니다.

<a name="type-hinting-dependencies"></a>
#### 타입-힌팅 의존성

명령어의 인자와 옵션을 전달 받는 것 이외에도, 명령어 클로저는 타입힌트를 추가하여 원하는 의존성을 [서비스 컨테이너](/docs/{{version}}/container)를 통해 가져올 수 있습니다.

    use App\Models\User;
    use App\Support\DripEmailer;

    Artisan::command('mail:send {user}', function (DripEmailer $drip, $user) {
        $drip->send(User::find($user));
    });

<a name="closure-command-descriptions"></a>
#### 클로저 명령 설명 추가

클로저 기반 명령어을 정의 할 때 `purpose` 메소드를 사용하여 명령어에 설명을 추가 할 수 있습니다. 이 설명은 `php artisan list` 또는 `php artisan help` 명령을 실행할 때 표시됩니다.

    Artisan::command('mail:send {user}', function ($user) {
        // ...
    })->purpose('Send a marketing email to a user');

<a name="defining-input-expectations"></a>
## 입력 값들 정의하기

콘솔 명령어를 작성할 때, 일반적으로 사용자로부터 인자 또는 옵션을 이용해 입력값을 받습니다. 라라벨에서는 명령어의 `signature` 속성을 이용해 사용자로부터 입력받을 값에 대한 설명을 쉽게 작성할 수 있습니다. `signature` 속성을 사용하면 라우트와 유사한 방법으로 명령어의 이름, 인자 및 옵션을 정의 할 수 있습니다.

<a name="arguments"></a>
### 인자들

사용자로 부터 전달받을 인자 및 옵션은 모두 중괄호로 묶여져 있어야합니다. 다음 예제의 경우 `user` 인자가 명령어에 필수적으로 필요하다고 정의하고 있습니다.

    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'mail:send {user}';

인자를 선택적으로 만들거나 인자의 기본값을 정의 할 수도 있습니다.

    // Optional argument...
    mail:send {user?}

    // Optional argument with default value...
    mail:send {user=foo}

<a name="options"></a>
### 옵션들

옵션또한 인자와 마찬가지로 사용자의 입력입니다. 옵션은 명령어 라인에서 입력할 때, 두 개의 하이픈 (`--`)으로 시작해야 됩니다. 옵션은 두가지 형태가 있습니다. 값을 받는 옵션과 그렇지 않은 옵션입니다. 값을받지 않는 옵션은 bool값의 "스위치"역할을 합니다. 이 옵션 형태의 예를 살펴 보겠습니다.


    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'mail:send {user} {--queue}';

이 예제에서, 아티즌 명령어가 실행될 때, `--queue` 스위치를 지정할 수 있습니다. `--queue` 스위치가 지정되면 옵션 값은 `true` 입니다. 그렇지 않으면, 값은 `false`가 됩니다

    php artisan mail:send 1 --queue

<a name="options-with-values"></a>
#### 값에 의한 옵션들

다음으로, 값을 예상하는 옵션을 살펴 보겠습니다. 사용자가 옵션 값을 지정해야하는 경우 옵션 이름 뒤에 `=`기호를 붙여야합니다.

    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'mail:send {user} {--queue=}';

사용자는 다음과 같이 옵션에 대한 값을 전달할 수 있습니다. 명령어을 실행 할 때 옵션에 값을 지정하지 않으면 해당 값은 `null`이 됩니다.

    php artisan mail:send 1 --queue=default

옵션 이름 뒤에 기본값을 지정하여 옵션에 기본값을 할당 할 수 있습니다. 사용자가 옵션 값을 전달하지 않으면 기본값이 사용됩니다.

    mail:send {user} {--queue=default}

<a name="option-shortcuts"></a>
#### 옵션의 짧은 표현

옵션을 정의 할 때 단축키를 지정하려면 전체 옵션 이름 앞에 `|`로 구분하여 단축키를 지정하면 됩니다.

    mail:send {user} {--Q|queue}

터미널에서 명령을 호출할 때 옵션의 짧은 표현은 하이픈 하나로 시작 해야 합니다.

    php artisan mail:send 1 -Q

<a name="input-arrays"></a>
### 배열 입력


인자나 옵션을 배열형태로 입력받기도록 정의하고자 한다면, `*` 문자를 사용하면 됩니다. 먼저 이러한 인자를 지정하는 예제를 살펴 보겠습니다.

    mail:send {user*}

이 함수를 호출 할 때, 명령 행에 `user` 인자를 순서대로 전달할 수 있습니다. 예를 들어 다음 명령어는 `user` 값을 `foo`와 `bar` 값을 가진 배열로 만들어 줍니다.

    php artisan mail:send foo bar

`*`문자는 0개 이상의 인자를 입력받기 위해 선택적인 인자 정의와 함께 사용 할 수 있습니다.

    mail:send {user?*}

<a name="option-arrays"></a>
#### 배열 옵션

여러 입력 값을 예상하는 옵션을 정의 할 때, 명령에 전달 된 각 옵션 값은 옵션 이름으로 시작해야합니다.

    mail:send {user} {--id=*}

    php artisan mail:send --id=1 --id=2

<a name="input-descriptions"></a>
### 입력 설명하기

설명에서 콜론을 사용해 인자 이름과 구분하여, 입력 인자 및 옵션에 대한 설명을 작성할 수 있습니다. 명령어을 정의하기 위해 공간이 더 필요하면 여러 줄로 나눠서 작성 할 수 있습니다.

    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'mail:send
                            {user : The ID of the user}
                            {--queue : Whether the job should be queued}';

<a name="command-io"></a>
## 명령어 입출력

<a name="retrieving-input"></a>
### 입력 조회하기

명령어가 실행되는 도중, 명령어가 전달 받은 인자 및 옵션의 값을 확인할 필요가 있을 수 있습니다. 이 경우에는 `argument` 메소드와 `option` 메소드를 사용할 수 있습니다. 인자 나 옵션이 없으면 `null`이 반환됩니다.

    /**
     * Execute the console command.
     *
     * @return int
     */
    public function handle()
    {
        $userId = $this->argument('user');

        //
    }

모든 인자들을 `array` 형태로 확인하고자 한다면 파라미터 없이 `arguments` 메소드를 호출하면 됩니다.

    $arguments = $this->arguments();

옵션을 확인하는 방법은 인자를 확인하는 것과 마찬가지로 `option` 메소드를 사용하면 됩니다. 모든 옵션을 배열 형태로 조회하려면 `options` 메소드를 호출하면 됩니다.

    // Retrieve a specific option...
    $queueName = $this->option('queue');

    // Retrieve all options as an array...
    $options = $this->options();

<a name="prompting-for-input"></a>
### 입력 프롬프트

출력을 표시하는 것 외에도 명령어을 실행하는 동안 사용자에게 입력을 요청할 수도 있습니다. `ask` 메소드는 사용자에게 주어진 질문을 보여주고 입력을 받은 다음, 사용자의 입력을 명령어에 반환합니다.

    /**
     * Execute the console command.
     *
     * @return mixed
     */
    public function handle()
    {
        $name = $this->ask('What is your name?');
    }

`secret` 메소드는 `ask` 메소드와 비슷하지만, 콘솔에서 사용자가 입력하는 동안 입력하는 값을 화면에 표시하지 않습니다. 이 메소드는 패스워드와 같은 민감한 정보를 물어볼 때 유용합니다.

    $password = $this->secret('What is the password?');

<a name="asking-for-confirmation"></a>
#### 사용자 확인받기

단순히 사용자에게 "예 또는 아니요"라고 확인을 받는 경우에는 `confirm` 메소드를 사용하면 됩니다. 이 메소드는 기본값으로 `false` 를 반환하지만 입력창에서 `y` 또는 `yes`를 입력하면 `true` 를 반환합니다.

    if ($this->confirm('Do you wish to continue?')) {
        //
    }

필요하다면 `confirm`메소드에 두 번째 인수로 `true`를 지정해, 입력창에서 기본적으로 `true`를 반환하도록 할 수 있습니다.

    if ($this->confirm('Do you wish to continue?', true)) {
        //
    }

<a name="auto-completion"></a>
#### 자동완성

`anticipate` 메소드는 입력가능한 값들에 대한 자동완성 기능을 제공합니다. 사용자는 자동완성과 관계없이, 어떤 대답도 입력 할 수 있습니다.

    $name = $this->anticipate('What is your name?', ['Taylor', 'Dayle']);

또한 `anticipate`메소드의 두 번째 인자로 클로저를 전달할 수도 있습니다. 사용자가 문자를 입력 할 때마다 클로저가 호출됩니다. 클로저는 사용자의 지금까지 입력한 값을 문자열 파라메터로 입력받고 자동 완성을 위한 옵션 배열을 반환해야합니다.

    $name = $this->anticipate('What is your address?', function ($input) {
        // Return auto-completion options...
    });

<a name="multiple-choice-questions"></a>
#### 여러개의 선택지

미리 주어진 선택지를 사용자에게 제공하려는 경우에는 `choice` 메소드를 사용하면 됩니다. 메소드에 세 번째 인수로, 옵션을 선택하지 않은 경우 반환 할 기본값의 배열 인덱스를 설정할 수 있습니다.

    $name = $this->choice(
        'What is your name?',
        ['Taylor', 'Dayle'],
        $defaultIndex
    );

또한, `choice` 메소드는 유효한 응답을 선택할 수 있도록, 네 번째 및 다섯 번째 인수를 통해 최대 시도 횟수와 다중 선택의 가능 여부를 선택적으로 입력받습니다.

    $name = $this->choice(
        'What is your name?',
        ['Taylor', 'Dayle'],
        $defaultIndex,
        $maxAttempts = null,
        $allowMultipleSelections = false
    );

<a name="writing-output"></a>
### 출력 작성하기

콘솔에 출력하기 위해서는 `line`, `info`, `comment`, `question`, `warn`, `error` 메소드를 사용하면 됩니다. 각 메소드는 각자의 목적에 맞는 ANSI 색상으로 출력합니다. 예를 들어 사용자에게 몇 가지 일반적인 정보를 출력해 보겠습니다. 사용자에게 정보에 관한 메시지를 보여주고싶은 경우에는 `info` 메소드를 사용합니다. 일반적으로 이 경우 녹색 텍스트가 콘솔에 표시됩니다.

    /**
     * Execute the console command.
     *
     * @return mixed
     */
    public function handle()
    {
        // ...

        $this->info('The command was successful!');
    }

에러 메시지 출력을 원하면 `error`메소드를 사용하면 됩니다. 에러메세지는 일반적으로 빨간색으로 표시됩니다.

    $this->error('Something went wrong!');

`line`메소드를 사용하여 색상이 지정되지 않은 일반 텍스트를 표시 할 수 있습니다.

    $this->line('Display this on the screen');

`newLine` 메소드를 사용하여 빈 줄을 표시 할 수 있습니다.

    // Write a single blank line...
    $this->newLine();

    // Write three blank lines...
    $this->newLine(3);

<a name="tables"></a>
#### 표

`table` 메소드를 사용하면 데이터의 여러 행과 열을 쉽게 도식화 할 수 있습니다. 테이블의 열 이름과 데이터를 제공하기만하면 라라벨이 자동으로 표의 적절한 너비와 높이를 계산합니다.

    use App\Models\User;

    $this->table(
        ['Name', 'Email'],
        User::all(['name', 'email'])->toArray()
    );

<a name="progress-bars"></a>
#### 프로그래스 바 - 진행률 표시줄

오래 실행되는 작업의 경우, 사용자에게 작업이 얼마나 완료되었는지 알려주는 진행률 표시줄을 표시하는 것이 좋을 수 있습니다. `withProgressBar` 메소드를 사용하여 라라벨은 진행률 표시줄을 알려주고, 주어진 반복 가능한 값에 대해 현재 진행률을 출력합니다.

    use App\Models\User;

    $users = $this->withProgressBar(User::all(), function ($user) {
        $this->performTask($user);
    });

때로는 진행률 표시줄에 얼마나 진행되었는지에 출력하기 위해 더 많은 수동 제어가 필요할 수 있습니다. 먼저 프로세스가 반복 할 총 단계 수를 정의합니다. 그런 다음 각 항목을 처리 한 후 진행률 표시 줄을 진행합니다.

    $users = App\Models\User::all();

    $bar = $this->output->createProgressBar(count($users));

    $bar->start();

    foreach ($users as $user) {
        $this->performTask($user);

        $bar->advance();
    }

    $bar->finish();

> {tip} 고급 옵션은 [Symfony Progress Bar 구성 요소 문서](https://symfony.com/doc/current/components/console/helpers/progressbar.html) 를 확인하세요.

<a name="registering-commands"></a>
## 명령어 등록하기

모든 콘솔 명령어는 애플리케이션의 '콘솔 커널'인 애플리케이션의 `App\Console\Kernel`클래스에 등록됩니다. 이 클래스의 `commands` 메소드를 보면, 커널의 `load` 메소드를 호출하는 것을 볼 수 있습니다. `load` 메소드는 `app/Console/Commands` 디렉토리를 스캔하고, 디렉토리에 포함 된 각 명령어들을 Artisan에 자동으로 등록합니다. 아티즌 명령어에 대해 다른 디렉토리를 스캔하기 위해 `load` 메소드를 추가로 호출 할 수도 있습니다.

    /**
     * Register the commands for the application.
     *
     * @return void
     */
    protected function commands()
    {
        $this->load(__DIR__.'/Commands');
        $this->load(__DIR__.'/../Domain/Orders/Commands');

        // ...
    }

필요한 경우 `App\Console\Kernel` 클래스의 `$commands` 속성에 명령어의 클래스 이름을 추가하여 수동으로 명령을 등록 할 수 있습니다. 만약 `$commands` 속성이 커널에 정의되어있지 않다면 수동으로 정의 해야 합니다. Artisan이 실행되면, 이 속성에 나열된 모든 명령어가 [service container](/docs/{{version}}/container) 에 의해 의존성을 해결하고 Artisan에 등록됩니다.

    protected $commands = [
        Commands\SendEmails::class
    ];

<a name="programmatically-executing-commands"></a>
## 프로그래밍 방식의 명령어 실행

때때로 CLI 외부에서 아티즌 명령어를 실행하고자 할 수 있습니다. 예를 들어, 라우트 또는 컨트롤러에서 아티즌 명령어를 실행할 수도 있습니다. 이것을 위해 `Artisan` 파사드의 `call` 메소드를 사용할 수 있습니다. `call` 메소드는 명령어의 지정한 이름 또는 클래스 이름을 첫 번째 인수로, 명령어를 실행하기 위한 매개 변수 배열을 두 번째 인수로받습니다. 종료 코드가 반환됩니다.

    use Illuminate\Support\Facades\Artisan;

    Route::post('/user/{user}/mail', function ($user) {
        $exitCode = Artisan::call('mail:send', [
            'user' => $user, '--queue' => 'default'
        ]);

        //
    });

또는 `call`메소드에 아티즌 명령어 전체를 문자열로 전달할 수 있습니다.

    Artisan::call('mail:send 1 --queue=default');

<a name="passing-array-values"></a>
#### 배열값 전달하기

명령어가 옵셥값으로 배열을 받도록 정의되어 있다면, 다음과 같이 배열 값을 전달 할 수 있습니다.

    use Illuminate\Support\Facades\Artisan;

    Route::post('/mail', function () {
        $exitCode = Artisan::call('mail:send', [
            '--id' => [5, 13]
        ]);
    });

<a name="passing-boolean-values"></a>
#### Boolean 값 전달하기

만약 `migrate:refresh` 명령어의 `--force` 옵션과 같이 문자열을 받지 않는 옵션의 값을 지정해야 한다면, 옵션값으로 `true` 혹은 `false` 를 입력해야 합니다.

    $exitCode = Artisan::call('migrate:refresh', [
        '--force' => true,
    ]);

<a name="queueing-artisan-commands"></a>
#### Artisan 대기열 명령어 

`Artisan` 파사드에서 `queue` 메소드를 사용하면 아티즌 명령어를 대기열에 추가하여 [queue workers](/docs/{{version}}/queues)에 의해 백그라운드에서 처리되도록 할 수도 있습니다. 이 방법을 사용하기 전에 큐를 설정하고 큐 리스너를 실행 중인지 확인하십시오.

    use Illuminate\Support\Facades\Artisan;

    Route::post('/user/{user}/mail', function ($user) {
        Artisan::queue('mail:send', [
            'user' => $user, '--queue' => 'default'
        ]);

        //
    });

`onConnection` 및 `onQueue` 메소드를 사용하여 아티즌 명령어가 전달되어야하는 연결 또는 대기열을 지정할 수 있습니다.

    Artisan::queue('mail:send', [
        'user' => 1, '--queue' => 'default'
    ])->onConnection('redis')->onQueue('commands');

<a name="calling-commands-from-other-commands"></a>
### 다른 명령어 호출하기

때때로 명령어에서 다른 아티즌 명령어를 호출하고 싶을 수도 있습니다. 이 경우에는 `call` 메소드를 사용하면 됩니다. `call` 메소드는 명령어의 이름과 명령어 인자와 옵션 배열을 입력받습니다.

    /**
     * Execute the console command.
     *
     * @return mixed
     */
    public function handle()
    {
        $this->call('mail:send', [
            'user' => 1, '--queue' => 'default'
        ]);

        //
    }

다른 콘솔 명령어를 실행하면서 출력을 모두 무시하려면 `callSilently` 메소드를 사용하십시오. `callSilently` 메소드의 사용법은 `call` 메소드와 동일합니다.

    $this->callSilently('mail:send', [
        'user' => 1, '--queue' => 'default'
    ]);

<a name="signal-handling"></a>
## 시그널 처리

아티즌 콘솔을 구동하는 Symfony 콘솔 구성 요소를 사용하면, 명령어가 실행되는 동안 프로세스 시그널을(있을 경우) 처리 할 수 있습니다. 예를 들어 명령어가 `SIGINT` and `SIGTERM` 신호를 처리할 수 있습니다.

사용하려면 아티즌 명령어 클래스에서 `Symfony\Component\Console\Command\SignalableCommandInterface` 인터페이스를 구현해야합니다. 이 인터페이스를 상속받은 경우 `getSubscribedSignals` 및 `handleSignal`의 두 가지 메소드를 구현해야합니다.

```php
<?php

use Symfony\Component\Console\Command\SignalableCommandInterface;

class StartServer extends Command implements SignalableCommandInterface
{
    // ...

    /**
     * Get the list of signals handled by the command.
     *
     * @return array
     */
    public function getSubscribedSignals(): array
    {
        return [SIGINT, SIGTERM];
    }

    /**
     * Handle an incoming signal.
     *
     * @param  int  $signal
     * @return void
     */
    public function handleSignal(int $signal): void
    {
        if ($signal === SIGINT) {
            $this->stopServer();

            return;
        }
    }
}
```

예상했겠지만 `getSubscribedSignals` 메소드는 명령어가 처리 할 수있는 시그널의 배열을 반환하며, `handleSignal` 메소드는 시그널을 수신하고 그에 따른 응답을 처리 할 수 있습니다.

<a name="stub-customization"></a>
## 스텁 사용자 정의

아티즌 콘솔의 `make` 명령어는 컨트롤러, 작업, 마이그레이션 및 테스트와 같은 다양한 클래스를 생성하는 데 사용됩니다. 이러한 클래스는 "스텁(stub)"파일에 입력값을 기반으로 내용을 채워 생성합니다. 그러나 아티즌에서 생성하는 파일을 약간 변경하는 것이 좋습니다. 이를 수행하려면 `stub:publish` 명령어를 사용하여 가장 일반적인 스텁파일을 애플리케이션으로 꺼내서 원하는 대로 수정 할 수 있습니다.

    php artisan stub:publish

스텁은 애플리케이션 최상단의 `stubs` 디렉토리에 생성됩니다. 이 스텁에 수정된 사항들은 아티즌의 `make` 명령어를 사용하여 해당 클래스를 생성 할 때 반영됩니다.

<a name="events"></a>
## 이벤트

아티즌은 명령어를 실행할 때  `Illuminate\Console\Events\ArtisanStarting`, `Illuminate\Console\Events\CommandStarting`, 그리고 `Illuminate\Console\Events\CommandFinished`의 세 가지 이벤트를 발생시킵니다. 아티즌이 실행되기 시작하면 `ArtisanStarting` 이벤트가 즉시 발생됩니다. 다음으로 명령이 실행되기 직전에 `CommandStarting` 이벤트가 발생됩니다. 마지막으로 명령 실행이 완료되면 `CommandFinished` 이벤트가 발생됩니다.

