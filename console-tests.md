# 콘솔 테스트

- [시작하기](#introduction)
- [Input / Output 예상하기](#expecting-input-and-output)

## 시작하기

Laravel은 HTTP 테스트를 단순하게 할 뿐 아니라 사용자 입력을 요구하는 콘솔 애플리케이션을 테스트하기위한 간단한 API를 제공합니다.

<a name="expecting-input-and-output"></a>
## Input / Output 예상하기

Laravel을 사용하면 `expectsQuestion` 메소드를 사용하여 콘솔 명령에 대한 사용자 입력을 쉽게 "mock" 할 수 있습니다. 또 `assertExitCode` 와 `expectsOutput` 메소드를 사용하여 콘솔 명령으로 출력 할 것으로 예상되는 종료 코드와 텍스트를 지정할 수 있습니다. 예를 들어 다음과 같은 console 명령을 생각해보십시오.

    Artisan::command('question', function () {
        $name = $this->ask('What is your name?');

        $language = $this->choice('Which language do you program in?', [
            'PHP',
            'Ruby',
            'Python',
        ]);

        $this->line('Your name is '.$name.' and you program in '.$language.'.');
    });

`expectsQuestion` , `expectsOutput` 및 `assertExitCode` 메소드를 사용하는 다음 테스트로 이 명령을 테스트 할 수 있습니다.

    /**
     * Test a console command.
     *
     * @return void
     */
    public function testConsoleCommand()
    {
        $this->artisan('question')
             ->expectsQuestion('What is your name?', 'Taylor Otwell')
             ->expectsQuestion('Which language do you program in?', 'PHP')
             ->expectsOutput('Your name is Taylor Otwell and you program in PHP.')
             ->assertExitCode(0);
    }


