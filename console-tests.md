# 콘솔 테스트

- [시작하기](#introduction)
- [성공 / 실패 기대](#success-failure-expectations)
- [Input / Output 기대](#input-output-expectations)

<a name="introduction"></a>
## 시작하기

HTTP 테스트를 단순화하는 것 외에도 라라벨은 애플리케이션의 [사용자 정의 콘솔 명령](/docs/{{version}}/artisan)을 테스트하기 위한 간단한 API를 제공합니다.

<a name="success-failure-expectations"></a>
## 성공 / 실패 기대

시작하기 위해, Artisan 명령의 종료 코드와 관련된 검증을 만드는 방법을 살펴보겠습니다. 이를 수행하기 위해 `artisan` 메서드를 사용하여 테스트에서 Artisan 명령을 호출합니다. 그런 다음 `assertExitCode` 메서드를 사용하여 명령이 주어진 종료 코드로 완료되었는지 확인합니다.

    /**
     * Test a console command.
     *
     * @return void
     */
    public function test_console_command()
    {
        $this->artisan('inspire')->assertExitCode(0);
    }

`assertNotExitCode` 메소드를 사용하여 명령이 주어진 종료 코드로 종료되지 않았다는 것을 검증할 수 있습니다.

    $this->artisan('inspire')->assertNotExitCode(1);

물론 모든 터미널 명령은 일반적으로 성공하면 `0` 상태 코드로 종료되고, 성공하지 못하면 0이 아닌 종료 코드로 종료됩니다. 따라서 편의를 위해 `assertSuccessful` 및 `assertFailed` 검증을 사용하여 주어진 명령이 성공적인 종료 코드로 종료되었는지 여부를 확인할 수 있습니다.

    $this->artisan('inspire')->assertSuccessful();

    $this->artisan('inspire')->assertFailed();

<a name="input-output-expectations"></a>
## Input / Output 기대

라라벨을 사용하면 `expectsQuestion` 메소드를 사용하여 콘솔 명령에 대한 사용자 입력을 쉽게 "mock" 할 수 있습니다. 또 `assertExitCode` 와 `expectsOutput` 메소드를 사용하여 콘솔 명령으로 출력 할 것으로 예상되는 종료 코드와 텍스트를 지정할 수 있습니다. 예를 들어 다음과 같은 console 명령을 생각해보십시오.

    Artisan::command('question', function () {
        $name = $this->ask('What is your name?');

        $language = $this->choice('Which language do you prefer?', [
            'PHP',
            'Ruby',
            'Python',
        ]);

        $this->line('Your name is '.$name.' and you prefer '.$language.'.');
    });

`expectsQuestion`, `expectsOutput`, `doesntExpectOutput` 및 `assertExitCode` 메소드를 사용하는 다음 테스트로 이 명령을 테스트할 수 있습니다.

    /**
     * Test a console command.
     *
     * @return void
     */
    public function test_console_command()
    {
        $this->artisan('question')
             ->expectsQuestion('What is your name?', 'Taylor Otwell')
             ->expectsQuestion('Which language do you prefer?', 'PHP')
             ->expectsOutput('Your name is Taylor Otwell and you prefer PHP.')
             ->doesntExpectOutput('Your name is Taylor Otwell and you prefer Ruby.')
             ->assertExitCode(0);
    }

<a name="confirmation-expectations"></a>
#### 확인창 기대

"예" 또는 "아니오" 응답의 형태로 확인창을 기대하는 명령을 작성할 때 `expectsConfirmation` 메소드를 사용할 수 있습니다.

    $this->artisan('module:import')
        ->expectsConfirmation('Do you really wish to run this command?', 'no')
        ->assertExitCode(1);

<a name="table-expectations"></a>
#### 테이블 기대

명령이 Artisan의 `table` 메서드를 사용하여 테이블형태로 정보를 표시하는 경우, 전체 테이블에 대한 출력 기대를 작성하는 것이 번거로울 수 있습니다. 대신 `expectTable` 메소드를 사용할 수 있습니다. 이 메서드는 테이블의 헤더를 첫 번째 인수로 받아들이고 테이블의 데이터를 두 번째 인수로 받아들입니다.

    $this->artisan('users:all')
        ->expectsTable([
            'ID',
            'Email',
        ], [
            [1, 'taylor@example.com'],
            [2, 'abigail@example.com'],
        ]);
