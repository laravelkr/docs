# 아티즌 콘솔

- [시작하기](#introduction)
- [명령어 작성하기](#writing-commands)
    - [명령어의 구조](#command-structure)
- [명령어 입력/출력](#command-io)
    - [입력받을 값들 지정하기](#defining-input-expectations)
    - [입력 확인하기](#retrieving-input)
    - [입력 프롬프트](#prompting-for-input)
    - [출력 작성하기](#writing-output)
- [명령어 등록하기](#registering-commands)
- [코드 상에서 명령어 호출하기](#calling-commands-via-code)

<a name="introduction"></a>
## 시작하기

아티즌(Artisan)은 라라벨에 포함된 커맨드라인 인터페이스(CLI)의 이름입니다. 아티즌은 애플리케이션 개발에 도움을 주는 많은 명령어들을 제공합니다. 아티즌은 강력한 Symfony 콘솔 콤포넌트에서 동작합니다. 실행가능한 아티즌 명령어 목록을 확인하려면 `list` 명령어를 다음과 같이 입력하면 됩니다.

    php artisan list

모든 명령어들은 "도움말" 기능을 포함하고 있어서 해당 명령어의 인자와, 옵션들을 확인할 수 있습니다. 도움말 화면을 확인하기 위해서는 다음과 같이 명령어 앞에 `help` 를 추가하여 실행하면 됩니다.

    php artisan help migrate

<a name="writing-commands"></a>
## 명령어 작성하기

아티즌에서 제공하는 명령어들에 더하여 추가적으로 애플리케이션에서 동작하는 여러분 자신의 고유한 명령어를 만들 수 있습니다. 여러분의 고유한 명령어는 `app/Console/Commands` 디렉토리에 저장하면 됩니다만, 실제로는 명령어가 `composer.json` 세팅을 기반하여 오토도딩 될 수 있다면 어느 위치에 넣어도 상관없습니다.

새로운 명령어를 생성하기 위해, 아티즌 명령어 `make:console`을 사용합니다.

    php artisan make:console SendEmails

이 명령어를 실행하면 `app/Console/Commands/SendEmails.php` 파일이 생성될 것입니다. 명령어를 생성할 때 `--command` 옵션을 사용하면 터미널에서 사용할 명령어의 이름을 지정할 수 있습니다.

    php artisan make:console SendEmails --command=emails:send

<a name="command-structure"></a>
### 명령어 구조

명령어가 생성되었으면, 클래스의 `signature` 과 `description` 를 입력합니다. 이것들은 `list` 화면에 커맨드를 표시 할 때 사용됩니다.

명령어가 실행될 때에는 `handle` 메소드가 호출됩니다. 여러분이 구성하고자 하는 로직을 이 메소드에 넣으면 됩니다. 간단한 예제를 살펴보겠습니다.

명령어 클래스의 생성자에서는 필요한 의존성 주입이 가능합니다. 라라벨의 [서비스 컨테이너](/docs/{{version}}/container) 는 자동으로 생성자에서 타입이 지정된 의존객체들을 주입해 줄 것입니다. 보다 나은 코드 재사용성을 위해, 콘솔 명령어를 가볍게 유지하고 애플리케이션 서비스들이 해당 작업을 수행할 수 있도록 하는 것이 좋습니다.

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
## 명령어 입력/출력

<a name="defining-input-expectations"></a>
### 입력받을 값들 지정하기

콘솔 명령어를 작성할 때 인자 또는 옵션을 통해 사용자로부터 입력을 받는것은 일반적인 일입니다. 라라벨에서는 명령어의 `signature` 속성을 통해서 사용자로 부터 입력받을 값을 설명하는 것을 편리하게 적상할 수 있습니다. `signature` 속성은 여러분이 명령어를 위한 특정한 이름, 인자 그리고 옵션값들 표현하기 위한 간단하고, 라우트와 비슷한 문법을 지원합니다.

사용자로 부터 전달받을 인자 및 옵션은 모두 대괄호로 묶여져 있어야합니다. 다음 예제의 경우 **필수적으로** `user` 인자가 명령어에 필요하다고 정의하고 있습니다.

    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'email:send {user}';

또한 다음처럼 인자를 선택적으로 입력 받을 수 있도록 정의할 수도 있습니다.

    // Optional argument...
    email:send {user?}

    // Optional argument with default value...
    email:send {user=foo}

옵션또한 인자와 마찬가지로 사용자의 입력입니다. 그러나 명령어 라인에서 입력하는 경우, 두 개의 하이픈 (`--`)을 사용해야 됩니다. 그래서 signature에서 옵션 정의도 동일한 방식으로 합니다.

    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'email:send {user} {--queue}';

이 예제에서, 아티즌 명령어가 실행될 때, `--queue` 스위치를 지정할 수 있습니다. `--queue` 스위치가 지정되면 옵션 값은 `true` 입니다. 그렇지 않으면, 값은 `false` 가됩니다

    php artisan email:send 1 --queue

사용자가 옵션 값을 지정해야만 하도록 할 수 있으며, 이 경우 옵션 이름의 마지막에 `=` 기호를 붙이면 됩니다.

    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'email:send {user} {--queue=}';

이 예제에서, 다음과 같이 옵션 값을 지정합니다.

    php artisan email:send 1 --queue=default

또는 옵션의 기본값을 설정할 수도 있습니다.

    email:send {user} {--queue=default}

옵션을 정의 할 때 단축키를 지정하려면 전체 옵션 이름 앞에 |로 구분하여 단축키를 지정하면 됩니다.

    email:send {user} {--Q|queue}

인자나 옵션을 배열형태로 입력받기도록 정의하고자 한다면, `*` 문자를 사용하면 됩니다:

    email:send {user*}

    email:send {user} {--id=*}

#### 입력 설명하기

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
### 입력값 조회하기

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

모든 인자들을 `배열` 형태로 확인하고자 한다면 파라미터 없이 `argument` 메소드를 호출하면 됩니다.

    $arguments = $this->argument();

옵션을 확인하는 방법은 인자를 확인하는 것과 마찬가지로 `option` 메소드를 호출하면 됩니다. `argument` 메소드처럼 파라미터 없이 `option` 메소드를 호출하는 경우에 모든 옵션값들을 `배열` 형태로 확인이 가능합니다.

    // Retrieve a specific option...
    $queueName = $this->option('queue');

    // Retrieve all options...
    $options = $this->option();

만약 인자나 옵션이 존재하지 않으면 `null`값이 반환됩니다.

<a name="prompting-for-input"></a>
### 입력 프롬프트

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

`secret` 메소드는 `ask` 메소드와 비슷하지만 콘솔에서 사용자가 입력하는 동안 입력하는 값을 화면에 표시하지 않습니다. 이 메소드는 패스워드와 같은 민감한 정보를 물어볼 때 유용합니다.

    $password = $this->secret('What is the password?');

#### 사용자 확인받기

단순히 사용자에게 확인 받고자 하는 경우에는 `confirm` 메소드를 사용하면 됩니다. 이 메소드는 기본값으로 `false` 를 반환합니다. 프롬프트에서 `y` 를 입력하면 `true` 를 반환합니다.

    if ($this->confirm('Do you wish to continue? [y|N]')) {
        //
    }

#### 사용자 선택

`anticipate` 메소드는 입력가능한 값들에 대한 자동완성 기능을 제공합니다. 사용자는 자동완성되는 결과에 개의치 않고, 어떤 대답도 입력 할 수 있습니다.

    $name = $this->anticipate('What is your name?', ['Taylor', 'Dayle']);

미리 주어진 선택지를 사용자에게 제공하려는 경우에는 `choice` 메소드를 사용하면 됩니다. 사용자는 특정한 값을 선택하고, 결과 값이 돌아옵니다. 아무것도 선택되지 않은 경우에는 기본값을 지정할 수도 있습니다.

    $name = $this->choice('What is your name?', ['Taylor', 'Dayle'], $default);

<a name="writing-output"></a>
### 출력 작성하기

콘솔에 출력하기 위해서는 `line`, `info`, `comment`, `question`, `error` 메소드를 사용합니다. 각각의 이름이 나타내는 목적에 맞게 사용되고 각각 적당한 ANSI 컬러로 표시됩니다.

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

오류 메시지를 표시하는 경우, `error` 메소드를 사용하면 됩니다. 오류 메시지는 일반적으로 빨간색으로 표시됩니다.

    $this->error('Something went wrong!');

간단한 콘솔 출력을 하려면 `line` 메소드를 사용하면 됩니다. `line` 메소드는 특정한 색상을 사용하지는 않습니다.

    $this->line('Display this on the screen');

#### 테이블 레이아웃

`table` 메소드는 데이터의 여러 행 / 열에 대한 포맷을 구성할 수 있게 해줍니다. 간단하게 해더와 행을 메소드에 전달하면 됩니다. 폭과 높이는 주어진 데이터를 기반으로 동적으로 계산됩니다.

    $headers = ['Name', 'Email'];

    $users = App\User::all(['name', 'email'])->toArray();

    $this->table($headers, $users);

#### 프로그래스 바 - 진행률 표시줄

시간이 오래 걸리는 작업의 경우, 진행 상황을 표시하는 것이 도움이 됩니다. 출력 객체를 사용하여 시작, 진행중, 정지를 표시 할 수 있습니다. 진행률 표시줄을 시작할 때 여러 단계를 정의해야만 각 단계마다 진행 표시줄이 이동합니다.

    $users = App\User::all();

    $bar = $this->output->createProgressBar(count($users));

    foreach ($users as $user) {
        $this->performTask($user);

        $bar->advance();
    }

    $bar->finish();

보다 자세한 옵션에 대해서는 [Symfony 진행률 표시줄 구성 컴포넌트 문서](http://symfony.com/doc/2.7/components/console/helpers/progressbar.html)  확인하십시오.

<a name="registering-commands"></a>
## 명령어 등록하기

명령어 작성이 완료되면 아티즌에서 사용할 수 있도록 등록할 필요가 있습니다. 이 작업은 일반적으로 `app/Console/Kernel.php` 파일에서 이루어 집니다.

이 파일 안에서, 명령어들의 목록을 담고 있는 `command` 변수를 찾을 수 있을 것입니다. 명령어를 등록하기 위해서 간단하게 목록에 작성한 클래스 이름을 추가하십시오. 아티즌이 구동할 때, 목록 안의 모든 명령어들은 [서비스 컨테이너](/docs/{{version}}/container)에 의해서 의존성이 해결된 상태로 생성되어 아티즌에 등록되어 집니다.

    protected $commands = [
        Commands\SendEmails::class
    ];

<a name="calling-commands-via-code"></a>
## 코드 상에서 명령어 호출하기

CLI 가 아니더라도 아티즌 명령어를 실행하고자 하는 경우가 있을 수 있습니다. 예를 들어 라우트 또는 컨트롤러에서 아티즌 명령어를 수행하기를 원할 수도 있습니다. 이 경우 `Artisan` 파사드의 `call` 메소드를 호출하면 됩니다. `call` 메소드는 명령어의 이름을 첫번째 인자로 전달받고 명령어의 파라미터들로 구성된 배열을 두번째 인자로 전달받습니다. 결과 코드가 반환될 것입니다:

    Route::get('/foo', function () {
        $exitCode = Artisan::call('email:send', [
            'user' => 1, '--queue' => 'default'
        ]);

        //
    });

`Artisan` 파사드에서 `queue` 메소드를 호출하는 경우 [queue workers](/docs/{{version}}/queues)를 통해서 아티즌 명령어가 백그라운드로 실행됩니다.

    Route::get('/foo', function () {
        Artisan::queue('email:send', [
            'user' => 1, '--queue' => 'default'
        ]);

        //
    });

`migrate:refresh` 명령어와 같이 문자열을 받지 못하는 지정된 옵션 값을들 필요로 하는 경우에는 `--force` 플래그를 통해서 `ture` 또는 `false` 값을 전달할 수 있습니다.

    $exitCode = Artisan::call('migrate:refresh', [
        '--force' => true,
    ]);

### 다른 명령어 호출하기

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

다른 콘솔 명령을 실행하면서 출력을 모두 무시하려면 `callSilent` 메소드를 사용하십시오. `callSilent` 메소드의 사용법은 `call` 메소드와 동일합니다.

    $this->callSilent('email:send', [
        'user' => 1, '--queue' => 'default'
    ]);
