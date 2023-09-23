# Processes
# Processes

- [Processes](#processes)
- [Processes](#processes)
  - [Introduction](#introduction)
  - [시작하기](#introduction)
  - [Invoking Processes](#invoking-processes)
  - [프로세스 호출하기](#invoking-processes)
      - [Throwing Exceptions](#throwing-exceptions)
      - [예외 발생시키기](#throwing-exceptions)
    - [Process Options](#process-options)
    - [프로세스 옵션](#process-options)
      - [Working Directory Path](#working-directory-path)
      - [작업 디렉토리 경로 지정하기](#working-directory-path)
      - [Input](#input)
      - [입력](#input)
      - [Timeouts](#timeouts)
      - [타임아웃](#timeouts)
      - [Environment Variables](#environment-variables)
      - [환경 변수](#environment-variables)
      - [TTY Mode](#tty-mode)
      - [TTY 모드](#tty-mode)
    - [Process Output](#process-output)
    - [프로세스 출력](#process-output)
      - [Disabling Process Output](#disabling-process-output)
      - [프로세스 출력 비활성화](#disabling-process-output)
    - [Pipelines](#pipelines)
    - [파이프라인](#pipelines)
  - [Asynchronous Processes](#asynchronous-processes)
  - [비동기 프로세스](#asynchronous-processes)
    - [Process IDs \& Signals](#process-ids--signals)
    - [프로세스 ID \& 시그널](#process-ids--signals)
    - [Asynchronous Process Output](#asynchronous-process-output)
    - [비동기 프로세스 출력](#asynchronous-process-output)
  - [Concurrent Processes](#concurrent-processes)
  - [동시 프로세스](#concurrent-processes)
    - [Naming Pool Processes](#naming-pool-processes)
    - [풀안에 프로세스에 이름 지정하기](#naming-pool-processes)
    - [Pool Process IDs \& Signals](#pool-process-ids--signals)
    - [풀 프로세스 ID \& 시그널](#pool-process-ids--signals)
  - [Testing](#testing)
  - [테스트](#testing)
    - [Faking Processes](#faking-processes)
    - [가짜 프로세스](#faking-processes)
    - [Faking Specific Processes](#faking-specific-processes)
    - [특정 프로세스 결과를 가짜(Fake)로 지정하기](#faking-specific-processes)
    - [Faking Process Sequences](#faking-process-sequences)
    - [프로세스 시퀀스 가짜(Fake)로 지정하기](#faking-process-sequences)
    - [Faking Asynchronous Process Lifecycles](#faking-asynchronous-process-lifecycles)
    - [비동기 프로세스 라이프사이클 위조](#faking-asynchronous-process-lifecycles)
    - [Available Assertions](#available-assertions)
    - [사용 가능한 어설션](#available-assertions)
      - [assertRan](#assertran)
      - [assertRan](#assertran)
      - [assertDidntRun](#assertdidntrun)
      - [assertDidntRun](#assertdidntrun)
      - [assertRanTimes](#assertrantimes)
      - [assertRanTimes](#assertrantimes)
    - [Preventing Stray Processes](#preventing-stray-processes)
    - [잘못된 프로세스 방지](#preventing-stray-processes)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel provides an expressive, minimal API around the [Symfony Process component](https://symfony.com/doc/current/components/process.html), allowing you to conveniently invoke external processes from your Laravel application. Laravel's process features are focused on the most common use cases and a wonderful developer experience.

라라벨은 [Symfony Process 컴포넌트](https://symfony.com/doc/current/components/process.html)를 기반으로 프로세스를 다루는 다양한 API를 제공합니다. 이를 통해 라라벨 애플리케이션 외부의 프로세스를 편리하게 호출할 수 있습니다. 라라벨의 프로세스 기능은 가장 일반적인 사용 사례에 초점을 맞추고 있으며 개발자 경험도 훌륭합니다.

<a name="invoking-processes"></a>
## Invoking Processes
## 프로세스 호출하기

To invoke a process, you may use the `run` and `start` methods offered by the `Process` facade. The `run` method will invoke a process and wait for the process to finish executing, while the `start` method is used for asynchronous process execution. We'll examine both approaches within this documentation. First, let's examine how to invoke a basic, synchronous process and inspect its result:

프로세스를 호출하려면 `Process` 파사드가 제공하는 `run` 및 `start` 메서드를 사용할 수 있습니다. `run` 메서드는 프로세스를 호출하고 프로세스가 실행을 완료할 때까지 기다립니다. `start` 메서드는 비동기 프로세스를 실행하는데 사용됩니다. 이 문서에서 두 가지 접근 방식을 모두 살펴보겠습니다. 먼저 기본적인 동기적 프로세스를 호출하고 결과를 확인하는 방법을 살펴보겠습니다.

```php
use Illuminate\Support\Facades\Process;

$result = Process::run('ls -la');

return $result->output();
```

Of course, the `Illuminate\Contracts\Process\ProcessResult` instance returned by the `run` method offers a variety of helpful methods that may be used to inspect the process result:

`run` 메서드가 반환하는 `Illuminate\Contracts\Process\ProcessResult` 인스턴스는 프로세스 결과를 확인하는 데 사용할 수 있는 다양한 메서드를 제공합니다.

```php
$result = Process::run('ls -la');

$result->successful();
$result->failed();
$result->exitCode();
$result->output();
$result->errorOutput();
```

<a name="throwing-exceptions"></a>
#### Throwing Exceptions
#### 예외 발생시키기

If you have a process result and would like to throw an instance of `Illuminate\Process\Exceptions\ProcessFailedException` if the exit code is greater than zero (thus indicating failure), you may use the `throw` and `throwIf` methods. If the process did not fail, the process result instance will be returned:

프로세스 결과를 확인하여 종료 코드가 0보다 큰 경우(즉, 실행 실패를 나타내는 경우) `Illuminate\Process\Exceptions\ProcessFailedException` 인스턴스를 발생시키려면 `throw` 및 `throwIf` 메서드를 사용하면 됩니다. 프로세스가 실패하지 않았다면 프로세스 결과 인스턴스(ProcessResult)가 반환됩니다.

```php
$result = Process::run('ls -la')->throw();

$result = Process::run('ls -la')->throwIf($condition);
```

<a name="process-options"></a>
### Process Options
### 프로세스 옵션

Of course, you may need to customize the behavior of a process before invoking it. Thankfully, Laravel allows you to tweak a variety of process features, such as the working directory, timeout, and environment variables.

프로세스를 호출하기 전에 다양한 옵션을 커스터마이징해야 할 수도 있습니다. 다행히도 라라벨은 작업 디렉토리, 타임아웃, 환경 변수와 같은 다양한 프로세스 기능을 조정할 수 있는 기능을 제공합니다.

<a name="working-directory-path"></a>
#### Working Directory Path
#### 작업 디렉토리 경로 지정하기

You may use the `path` method to specify the working directory of the process. If this method is not invoked, the process will inherit the working directory of the currently executing PHP script:

`path` 메서드를 사용하면 프로세스의 작업 디렉토리를 지정할 수 있습니다. 이 메서드가 호출되지 않으면 프로세스는 현재 실행 중인 PHP 스크립트를 기준으로 작업 디렉토리를 결정합니다.

```php
$result = Process::path(__DIR__)->run('ls -la');
```

<a name="input"></a>
#### Input
#### 입력 

You may provide input via the "standard input" of the process using the `input` method:

`input` 메서드를 사용하여 프로세스의 "표준 입력"을 통해 입력을 제공할 수 있습니다.

```php
$result = Process::input('Hello World')->run('cat');
```

<a name="timeouts"></a>
#### Timeouts
#### 타임아웃

By default, processes will throw an instance of `Illuminate\Process\Exceptions\ProcessTimedOutException` after executing for more than 60 seconds. However, you can customize this behavior via the `timeout` method:

기본적으로 프로세스는 실행한지 60초가 넘어가면 `Illuminate\Process\Exceptions\ProcessTimedOutException` 인스턴스를 발생시킵니다. 하지만 `timeout` 메서드를 통해 이 동작을 변경할 수 있습니다.

```php
$result = Process::timeout(120)->run('bash import.sh');
```

Or, if you would like to disable the process timeout entirely, you may invoke the `forever` method:

프로세스 타임아웃을 완전히 비활성화하려면 `forever` 메서드를 호출하면 됩니다.

```php
$result = Process::forever()->run('bash import.sh');
```

The `idleTimeout` method may be used to specify the maximum number of seconds the process may run without returning any output:

`idleTimeout` 메서드를 사용하여 프로세스가 출력을 반환하지 않고 실행할 수 있는 최대 시간(초)을 지정할 수 있습니다.

```php
$result = Process::timeout(60)->idleTimeout(30)->run('bash import.sh');
```

<a name="environment-variables"></a>
#### Environment Variables
#### 환경 변수

Environment variables may be provided to the process via the `env` method. The invoked process will also inherit all of the environment variables defined by your system:

환경 변수는 `env` 메서드를 통해 프로세스에 제공할 수 있습니다. 호출된 프로세스는 시스템에서 정의한 모든 환경 변수를 상속합니다.

```php
$result = Process::forever()
            ->env(['IMPORT_PATH' => __DIR__])
            ->run('bash import.sh');
```

If you wish to remove an inherited environment variable from the invoked process, you may provide that environment variable with a value of `false`:

호출된 프로세스에서 상속된 환경 변수를 제거하려면 해당 환경 변수에 `false` 값을 전달하면 됩니다.

```php
$result = Process::forever()
            ->env(['LOAD_PATH' => false])
            ->run('bash import.sh');
```

<a name="tty-mode"></a>
#### TTY Mode
#### TTY 모드

The `tty` method may be used to enable TTY mode for your process. TTY mode connects the input and output of the process to the input and output of your program, allowing your process to open an editor like Vim or Nano as a process:

`tty` 메서드를 사용하여 프로세스에 TTY 모드를 활성화할 수 있습니다. TTY 모드는 프로세스의 입력 및 출력을 프로그램의 입력 및 출력에 연결하여 프로세스에서 Vim 또는 Nano와 같은 편집기를 프로세스로 열 수 있도록 합니다.
```php
Process::forever()->tty()->run('vim');
```

<a name="process-output"></a>
### Process Output
### 프로세스 출력

As previously discussed, process output may be accessed using the `output` (stdout) and `errorOutput` (stderr) methods on a process result:

앞선 예시에서 볼 수 있듯이, 프로세스 출력은 프로세스 결과의 `output`(stdout) 및 `errorOutput`(stderr) 메서드를 사용하여 액세스할 수 있습니다.

```php
use Illuminate\Support\Facades\Process;

$result = Process::run('ls -la');

echo $result->output();
echo $result->errorOutput();
```

However, output may also be gathered in real-time by passing a closure as the second argument to the `run` method. The closure will receive two arguments: the "type" of output (`stdout` or `stderr`) and the output string itself:

프로세스의 출력은 `run` 메서드의 두 번째 인자로 클로저를 전달하여 실시간으로 확인할 수도 있습니다. 클로저는 두 개의 인자를 전달받습니다. 출력의 "타입"(`stdout` 또는 `stderr`)과 출력 문자열 그 자체입니다.

```php
$result = Process::run('ls -la', function (string $type, string $output) {
    echo $output;
});
```

Laravel also offers the `seeInOutput` and `seeInErrorOutput` methods, which provide a convenient way to determine if a given string was contained in the process' output:

라라벨은 `seeInOutput` 및 `seeInErrorOutput` 메서드도 제공하는데, 이 메서드는 주어진 문자열이 프로세스의 출력에 포함되어 있는지 여부를 확인하는 데 유용합니다.

```php
if (Process::run('ls -la')->seeInOutput('laravel')) {
    // ...
}
```

<a name="disabling-process-output"></a>
#### Disabling Process Output
#### 프로세스 출력 비활성화

If your process is writing a significant amount of output that you are not interested in, you can conserve memory by disabling output retrieval entirely. To accomplish this, invoke the `quietly` method while building the process:

프로세스에서 불필요한 많은 양의 출력을 생성하는 경우, 출력 조회를 완전히 비활성화하여 메모리를 절약할 수 있습니다. 이렇게 하려면 프로세스를 빌드하는 동안 `quietly` 메서드를 호출하면 됩니다.

```php
use Illuminate\Support\Facades\Process;

$result = Process::quietly()->run('bash import.sh');
```

<a name="process-pipelines"></a>
### Pipelines
### 파이프라인

Sometimes you may want to make the output of one process the input of another process. This is often referred to as "piping" the output of a process into another. The `pipe` method provided by the `Process` facades makes this easy to accomplish. The `pipe` method will execute the piped processes synchronously and return the process result for the last process in the pipeline:

때로는 한 프로세스의 출력을 다른 프로세스의 입력으로 만들고 싶을 수 있습니다. 이를 종종 프로세스의 출력을 다른 프로세스로 "파이프링(piping)"이라고 합니다. `Process` 파사드가 제공하는 `pipe` 메서드를 사용하면 이를 쉽게 수행할 수 있습니다. `pipe` 메서드는 파이프된 프로세스를 동기적으로 실행하고 파이프라인의 마지막 프로세스에 대한 프로세스 결과를 반환합니다.

```php
use Illuminate\Process\Pipe;
use Illuminate\Support\Facades\Process;

$result = Process::pipe(function (Pipe $pipe) {
    $pipe->command('cat example.txt');
    $pipe->command('grep -i "laravel"');
});

if ($result->successful()) {
    // ...
}
```

If you do not need to customize the individual processes that make up the pipeline, you may simply pass an array of command strings to the `pipe` method:

파이프라인을 구성하는 개별 프로세스를 커스터마이즈할 필요가 없으면 `pipe` 메서드에 명령어 문자열 배열을 전달하기만 하면 됩니다.

```php
$result = Process::pipe([
    'cat example.txt',
    'grep -i "laravel"',
]);
```

The process output may be gathered in real-time by passing a closure as the second argument to the `pipe` method. The closure will receive two arguments: the "type" of output (`stdout` or `stderr`) and the output string itself:

`pipe` 메서드의 두 번째 인자로 클로저를 전달하여 프로세스 출력을 실시간으로 확인할 수도 있습니다. 클로저는 두 개의 인자를 전달받습니다. 출력의 "타입"(`stdout` 또는 `stderr`)과 출력 문자열 그 자체입니다.

```php
$result = Process::pipe(function (Pipe $pipe) {
    $pipe->command('cat example.txt');
    $pipe->command('grep -i "laravel"');
}, function (string $type, string $output) {
    echo $output;
});
```

Laravel also allows you to assign string keys to each process within a pipeline via the `as` method. This key will also be passed to the output closure provided to the `pipe` method, allowing you to determine which process the output belongs to:

`as` 메서드를 통해 파이프라인 내의 각 프로세스에 문자열 키를 할당할 수도 있습니다. 이 키는 `pipe` 메서드에 제공된 출력 클로저에도 전달되므로 출력이 어느 프로세스에 속하는지 확인할 수 있습니다.

```php
$result = Process::pipe(function (Pipe $pipe) {
    $pipe->as('first')->command('cat example.txt');
    $pipe->as('second')->command('grep -i "laravel"');
})->start(function (string $type, string $output, string $key) {
    // ...
});
```

<a name="asynchronous-processes"></a>
## Asynchronous Processes
## 비동기 프로세스

While the `run` method invokes processes synchronously, the `start` method may be used to invoke a process asynchronously. This allows your application to continue performing other tasks while the process runs in the background. Once the process has been invoked, you may utilize the `running` method to determine if the process is still running:

`run` 메서드는 프로세스를 동기적으로 호출하지만 `start` 메서드는 프로세스를 비동기적으로 호출합니다. 이를 통해 프로세스가 백그라운드에서 실행되는 동안 애플리케이션이 다른 작업을 계속 수행할 수 있습니다. 프로세스를 호출한 후 `running` 메서드를 사용하여 프로세스가 여전히 실행 중인지 확인할 수 있습니다.

```php
$process = Process::timeout(120)->start('bash import.sh');

while ($process->running()) {
    // ...
}

$result = $process->wait();
```

As you may have noticed, you may invoke the `wait` method to wait until the process is finished executing and retrieve the process result instance:

`wait` 메서드를 호출하여 프로세스가 실행을 완료할 때까지 기다릴 수 있고 프로세스 결과 인스턴스를 조회할 수 있습니다.

```php
$process = Process::timeout(120)->start('bash import.sh');

// ...

$result = $process->wait();
```

<a name="process-ids-and-signals"></a>
### Process IDs & Signals
### 프로세스 ID & 시그널

The `id` method may be used to retrieve the operating system assigned process ID of the running process:

`id` 메서드를 사용하여 실행 중인 프로세스가 운영 체제로 부터 부여받은 프로세스 ID를 조회할 수 있습니다.

```php
$process = Process::start('bash import.sh');

return $process->id();
```

You may use the `signal` method to send a "signal" to the running process. A list of predefined signal constants can be found within the [PHP documentation](https://www.php.net/manual/en/pcntl.constants.php):

`signal` 메서드를 사용하여 실행 중인 프로세스에 "시그널"을 보낼 수 있습니다. 미리 정의된 시그널 상수 목록은 [PHP 문서](https://www.php.net/manual/en/pcntl.constants.php)에서 찾을 수 있습니다.

```php
$process->signal(SIGUSR2);
```

<a name="asynchronous-process-output"></a>
### Asynchronous Process Output
### 비동기 프로세스의 출력

While an asynchronous process is running, you may access its entire current output using the `output` and `errorOutput` methods; however, you may utilize the `latestOutput` and `latestErrorOutput` to access the output from the process that has occurred since the output was last retrieved:

비동기 프로세스가 실행되는 동안에는 `output` 및 `errorOutput` 메서드를 사용하여 전체 현재 출력에 액세스할 수 있지만, `latestOutput` 및 `latestErrorOutput`을 사용하여 출력이 마지막으로 조회된 이후 발생한 프로세스의 출력에 액세스할 수도 있습니다.

```php
$process = Process::timeout(120)->start('bash import.sh');

while ($process->running()) {
    echo $process->latestOutput();
    echo $process->latestErrorOutput();

    sleep(1);
}
```

Like the `run` method, output may also be gathered in real-time from asynchronous processes by passing a closure as the second argument to the `start` method. The closure will receive two arguments: the "type" of output (`stdout` or `stderr`) and the output string itself:

`run` 메서드와 마찬가지로, `start` 메서드의 두 번째 인자로 클로저를 전달하여 비동기 프로세스에서 실시간으로 출력을 확인할 수도 있습니다. 클로저는 두 개의 인자를 전달 받습니다. 출력의 "타입"(`stdout` 또는 `stderr`)과 출력 문자열 그 자체입니다.

```php
$process = Process::start('bash import.sh', function (string $type, string $output) {
    echo $output;
});

$result = $process->wait();
```

<a name="concurrent-processes"></a>
## Concurrent Processes
## 동시 프로세스

Laravel also makes it a breeze to manage a pool of concurrent, asynchronous processes, allowing you to easily execute many tasks simultaneously. To get started, invoke the `pool` method, which accepts a closure that receives an instance of `Illuminate\Process\Pool`.

라라벨을 사용하면 동시, 비동기 프로세스 풀을 쉽게 관리할 수 있으므로 많은 작업을 동시에 쉽게 실행할 수 있습니다. 동시 프로세스를 시작하려면 `Illuminate\Process\Pool`의 인스턴스를 인자로 전달 받는 클로저를 전달하여 `pool` 메서드를 호출하면 됩니다.

Within this closure, you may define the processes that belong to the pool. Once a process pool is started via the `start` method, you may access the [collection](/docs/{{version}}/collections) of running processes via the `running` method:

이 클로저 내에서 풀에 속하는 프로세스를 정의할 수 있습니다. `start` 메서드를 통해 프로세스 풀을 시작하면 `running` 메서드를 통해 실행 중인 프로세스의 [컬렉션](/docs/{{version}}/collections)에 액세스할 수 있습니다.

```php
use Illuminate\Process\Pool;
use Illuminate\Support\Facades\Process;

$pool = Process::pool(function (Pool $pool) {
    $pool->path(__DIR__)->command('bash import-1.sh');
    $pool->path(__DIR__)->command('bash import-2.sh');
    $pool->path(__DIR__)->command('bash import-3.sh');
})->start(function (string $type, string $output, int $key) {
    // ...
});

while ($pool->running()->isNotEmpty()) {
    // ...
}

$results = $pool->wait();
```

As you can see, you may wait for all of the pool processes to finish executing and resolve their results via the `wait` method. The `wait` method returns an array accessible object that allows you to access the process result instance of each process in the pool by its key:

보시다시피, 모든 풀 프로세스의 실행이 완료될 때까지 기다렸다가 `wait` 메서드를 통해 그 결과를 확인할 수 있습니다. `wait` 메서드는 풀에 있는 각 프로세스의 프로세스 결과 인스턴스에 해당 키로 접근할 수 있는 배열 접근 가능 객체를 반환합니다.

```php
$results = $pool->wait();

echo $results[0]->output();
```

Or, for convenience, the `concurrently` method may be used to start an asynchronous process pool and immediately wait on its results. This can provide particularly expressive syntax when combined with PHP's array destructuring capabilities:

편의를 위해서 `concurrently` 메서드를 사용하여 비동기 프로세스 풀을 시작하고 그 결과를 즉시 기다릴 수 있습니다. 이 메서드는 PHP의 배열 구조 분해 기능(array destructuring)과 함께 사용하면 특히 표현력이 풍부한 구문을 제공할 수 있습니다.

```php
[$first, $second, $third] = Process::concurrently(function (Pool $pool) {
    $pool->path(__DIR__)->command('ls -la');
    $pool->path(app_path())->command('ls -la');
    $pool->path(storage_path())->command('ls -la');
});

echo $first->output();
```

<a name="naming-pool-processes"></a>
### Naming Pool Processes
### 풀안에 프로세스에 이름 지정하기 

Accessing process pool results via a numeric key is not very expressive; therefore, Laravel allows you to assign string keys to each process within a pool via the `as` method. This key will also be passed to the closure provided to the `start` method, allowing you to determine which process the output belongs to:

숫자 키를 통해 프로세스 풀 결과에 액세스하는 것은 코드를 이해할 때 불편합니다. `as` 메서드를 통해 풀 내의 각 프로세스에 문자열 키를 할당할 수 있습니다. 이 키는 `start` 메서드에 제공된 클로저에도 전달되어 출력이 어떤 프로세스에 속하는지 확인할 수 있습니다.

```php
$pool = Process::pool(function (Pool $pool) {
    $pool->as('first')->command('bash import-1.sh');
    $pool->as('second')->command('bash import-2.sh');
    $pool->as('third')->command('bash import-3.sh');
})->start(function (string $type, string $output, string $key) {
    // ...
});

$results = $pool->wait();

return $results['first']->output();
```

<a name="pool-process-ids-and-signals"></a>
### Pool Process IDs & Signals
### 풀의 프로세스 ID & 시그널

Since the process pool's `running` method provides a collection of all invoked processes within the pool, you may easily access the underlying pool process IDs:

프로세스 풀의 `running` 메서드는 풀 안에서 호출된 모든 프로세스의 컬렉션을 제공하므로 백그라운드 풀 안에 있는 프로세스의 ID에 쉽게 액세스할 수 있습니다.

```php
$processIds = $pool->running()->each->id();
```

And, for convenience, you may invoke the `signal` method on a process pool to send a signal to every process within the pool:

프로세스 풀에서 `signal` 메서드를 호출하여 풀 내의 모든 프로세스에 시그널을 보낼 수 있습니다.

```php
$pool->signal(SIGUSR2);
```

<a name="testing"></a>
## Testing
## 테스트

Many Laravel services provide functionality to help you easily and expressively write tests, and Laravel's process service is no exception. The `Process` facade's `fake` method allows you to instruct Laravel to return stubbed / dummy results when processes are invoked.

많은 라라벨 서비스는 테스트를 쉽고 이해하기 쉽게 작성할 수 있도록 도와주는 기능을 제공합니다. 라라벨의 Process-프로세스 기능도 마찬가지입니다. `Process` 파사드의 `fake` 메서드를 사용하면 프로세스가 호출될 때 라라벨이 스텁 / 더미 결과를 반환하도록 명령할 수 있습니다.

<a name="faking-processes"></a>
### Faking Processes
### 가짜 프로세스

To explore Laravel's ability to fake processes, let's imagine a route that invokes a process:

라라벨의 프로세스의 가짜(fake) 기능을 살펴보기 위해 프로세스를 호출하는 라우트를 상상해 보겠습니다.

```php
use Illuminate\Support\Facades\Process;
use Illuminate\Support\Facades\Route;

Route::get('/import', function () {
    Process::run('bash import.sh');

    return 'Import complete!';
});
```

When testing this route, we can instruct Laravel to return a fake, successful process result for every invoked process by calling the `fake` method on the `Process` facade with no arguments. In addition, we can even [assert](#available-assertions) that a given process was "ran":

이 라우트를 테스트할 때 `Process` 파사드의 `fake` 메서드를 인자 없이 호출하여 호출된 모든 프로세스에 대해 가짜(fake) 프로세스가 무사히 완료된 결과를 반환하도록 라라벨에 지시할 수 있습니다. 또한, 주어진 프로세스가 "실행"되었는지 [assert](#available-assertions)할 수도 있습니다.

```php
<?php

namespace Tests\Feature;

use Illuminate\Process\PendingProcess;
use Illuminate\Contracts\Process\ProcessResult;
use Illuminate\Support\Facades\Process;
use Tests\TestCase;

class ExampleTest extends TestCase
{
    public function test_process_is_invoked(): void
    {
        Process::fake();

        $response = $this->get('/');

        // Simple process assertion...
        Process::assertRan('bash import.sh');

        // Or, inspecting the process configuration...
        Process::assertRan(function (PendingProcess $process, ProcessResult $result) {
            return $process->command === 'bash import.sh' &&
                   $process->timeout === 60;
        });
    }
}
```

As discussed, invoking the `fake` method on the `Process` facade will instruct Laravel to always return a successful process result with no output. However, you may easily specify the output and exit code for faked processes using the `Process` facade's `result` method:

이전에 설명한 바와 같이 `Process` 파사드의 `fake` 메서드를 호출하면 라라벨이 항상 출력이 없는 성공적인 프로세스 결과를 반환하도록 지시합니다. 그러나 `Process` 파사드의 `result` 메서드를 사용하여 가짜(fake) 프로세스의 출력 및 종료 코드를 지정할 수도 있습니다.

```php
Process::fake([
    '*' => Process::result(
        output: 'Test output',
        errorOutput: 'Test error output',
        exitCode: 1,
    ),
]);
```

<a name="faking-specific-processes"></a>
### Faking Specific Processes
### 특정 프로세스 결과를 가짜(Fake)로 지정하기

As you may have noticed in a previous example, the `Process` facade allows you to specify different fake results per process by passing an array to the `fake` method.

앞선 예제에서 알 수 있듯이 `Process` 파사드는 `fake` 메서드에 배열을 전달하여 프로세스별로 다른 가짜(fake) 결과를 지정할 수 있습니다.

The array's keys should represent command patterns that you wish to fake and their associated results. The `*` character may be used as a wildcard character. Any process commands that have not been faked will actually be invoked. You may use the `Process` facade's `result` method to construct stub / fake results for these commands:

배열의 키는 가짜(fake)를 지정하려는 명령 패턴과 연결된 결과를 나타내야 합니다. `*` 문자는 와일드카드 문자로 사용할 수 있습니다. 가짜(fake)가 아닌 모든 프로세스 명령은 실제로 호출됩니다. `Process` 파사드의 `result` 메서드를 사용하여 이러한 명령에 대한 스텁 / 가짜(fake) 결과를 설정할 수 있습니다.

```php
Process::fake([
    'cat *' => Process::result(
        output: 'Test "cat" output',
    ),
    'ls *' => Process::result(
        output: 'Test "ls" output',
    ),
]);
```

If you do not need to customize the exit code or error output of a faked process, you may find it more convenient to specify the fake process results as simple strings:

가짜(fake) 프로세스의 종료 코드 또는 오류 출력을 커스터마이징 할 필요가 없으면 가짜(fake) 프로세스 결과를 간단한 문자열로 지정하는 것이 더 편리할 수 있습니다.

```php
Process::fake([
    'cat *' => 'Test "cat" output',
    'ls *' => 'Test "ls" output',
]);
```

<a name="faking-process-sequences"></a>
### Faking Process Sequences
### 프로세스 시퀀스 가짜(Fake)로 지정하기

If the code you are testing invokes multiple processes with the same command, you may wish to assign a different fake process result to each process invocation. You may accomplish this via the `Process` facade's `sequence` method:

테스트하는 코드가 동일한 명령으로 여러 개의 프로세스를 호출하는 경우 각각의 프로세스 호출의 결과를 각기 다른 가짜(fake) 프로세스 결과를 할당할 수 있습니다. `Process` 파사드의 `sequence` 메서드를 사용하여 다음과 같이 작성할 수 있습니다.

```php
Process::fake([
    'ls *' => Process::sequence()
                ->push(Process::result('First invocation'))
                ->push(Process::result('Second invocation')),
]);
```

<a name="faking-asynchronous-process-lifecycles"></a>
### Faking Asynchronous Process Lifecycles
### 비동기 프로세스 라이프사이클 가짜(Fake)로 지정하기

Thus far, we have primarily discussed faking processes which are invoked synchronously using the `run` method. However, if you are attempting to test code that interacts with asynchronous processes invoked via `start`, you may need a more sophisticated approach to describing your fake processes.

지금까지 `run` 메서드를 사용하여 동기적으로 호출되는 프로세스를 가짜(fake)로 지정하는 방법에 대해 주로 논의했습니다. 그렇지만 `start`를 통해 호출된 비동기 프로세스와 상호 작용하는 코드를 테스트하려는 경우에는 가짜(fake) 프로세스를 다루는 좀 더 정교한 접근 방식이 필요합니다.

For example, let's imagine the following route which interacts with an asynchronous process:

예를 들어, 다음과 같은 비동기 프로세스와 상호 작용하는 라우트를 가정해 보겠습니다.

```php
use Illuminate\Support\Facades\Log;
use Illuminate\Support\Facades\Route;

Route::get('/import', function () {
    $process = Process::start('bash import.sh');

    while ($process->running()) {
        Log::info($process->latestOutput());
        Log::info($process->latestErrorOutput());
    }

    return 'Done';
});
```

To properly fake this process, we need to be able to describe how many times the `running` method should return `true`. In addition, we may want to specify multiple lines of output that should be returned in sequence. To accomplish this, we can use the `Process` facade's `describe` method:

이 프로세스를 제대로 가짜(fake)로 지정하려면 `running` 메서드가 `true`를 반환하는 횟수를 설명할 수 있어야 합니다. 또한, 순차적으로 반환되어야 하는 여러 줄의 출력을 지정할 수도 있습니다. 이렇게 하기 위해서는 `Process` 파사드의 `describe` 메서드를 사용하면 됩니다.

```php
Process::fake([
    'bash import.sh' => Process::describe()
            ->output('First line of standard output')
            ->errorOutput('First line of error output')
            ->output('Second line of standard output')
            ->exitCode(0)
            ->iterations(3),
]);
```

Let's dig into the example above. Using the `output` and `errorOutput` methods, we may specify multiple lines of output that will be returned in sequence. The `exitCode` method may be used to specify the final exit code of the fake process. Finally, the `iterations` method may be used to specify how many times the `running` method should return `true`.

위의 예제를 좀 더 살펴보겠습니다. `output` 및 `errorOutput` 메서드를 사용하여 순차적으로 반환되는 여러 줄의 출력을 지정할 수 있습니다. `exitCode` 메서드를 사용하여 가짜(fake) 프로세스의 최종 종료 코드를 지정할 수 있습니다. 마지막으로 `iterations` 메서드를 사용하여 `running` 메서드가 `true`를 반환하는 횟수를 지정할 수 있습니다.

<a name="available-assertions"></a>
### Available Assertions
### 사용 가능한 어설션

As [previously discussed](#faking-processes), Laravel provides several process assertions for your feature tests. We'll discuss each of these assertions below.

앞서 [설명한 바](#faking-processes)와 같이 라라벨은 기능 테스트에 대한 여러 프로세스 어설션을 제공합니다. 아래에서 각 어설션에 대해 설명하겠습니다.

<a name="assert-process-ran"></a>
#### assertRan
#### assertRan

Assert that a given process was invoked:

주어진 프로세스가 호출되었는지 확인합니다.

```php
use Illuminate\Support\Facades\Process;

Process::assertRan('ls -la');
```

The `assertRan` method also accepts a closure, which will receive an instance of a process and a process result, allowing you to inspect the process' configured options. If this closure returns `true`, the assertion will "pass":

`assertRan` 메서드는 프로세스와 프로세스 결과의 인스턴스를 받아 프로세스의 구성 옵션을 검사할 수 있는 클로저도 받습니다. 이 클로저가 `true`를 반환하면 어설션이 "통과"됩니다.

```php
Process::assertRan(fn ($process, $result) =>
    $process->command === 'ls -la' &&
    $process->path === __DIR__ &&
    $process->timeout === 60
);
```

The `$process` passed to the `assertRan` closure is an instance of `Illuminate\Process\PendingProcess`, while the `$result` is an instance of `Illuminate\Contracts\Process\ProcessResult`.

`assertRan` 클로저에 전달된 `$process`는 `Illuminate\Process\PendingProcess`의 인스턴스이고 `$result`는 `Illuminate\Contracts\Process\ProcessResult`의 인스턴스입니다.

<a name="assert-process-didnt-run"></a>
#### assertDidntRun
#### assertDidntRun

Assert that a given process was not invoked:

주어진 프로세스가 호출되지 않았 다는 것을 확인합니다.

```php
use Illuminate\Support\Facades\Process;

Process::assertDidntRun('ls -la');
```

Like the `assertRan` method, the `assertDidntRun` method also accepts a closure, which will receive an instance of a process and a process result, allowing you to inspect the process' configured options. If this closure returns `true`, the assertion will "fail":

`assertRan` 메서드와 마찬가지로 `assertDidntRun` 메서드도 프로세스와 프로세스 결과의 인스턴스를 받아 프로세스의 구성 옵션을 검사할 수 있는 클로저도 받습니다. 이 클로저가 `true`를 반환하면 어설션이 "실패"합니다.

```php
Process::assertDidntRun(fn (PendingProcess $process, ProcessResult $result) =>
    $process->command === 'ls -la'
);
```

<a name="assert-process-ran-times"></a>
#### assertRanTimes
#### assertRanTimes

Assert that a given process was invoked a given number of times:

주어진 프로세스가 주어진 횟수만큼 호출되었는지 확인합니다.

```php
use Illuminate\Support\Facades\Process;

Process::assertRanTimes('ls -la', times: 3);
```

The `assertRanTimes` method also accepts a closure, which will receive an instance of a process and a process result, allowing you to inspect the process' configured options. If this closure returns `true` and the process was invoked the specified number of times, the assertion will "pass":

`assertRanTimes` 메서드는 프로세스와 프로세스 결과의 인스턴스를 받아 프로세스의 구성 옵션을 검사할 수 있는 클로저도 받습니다. 이 클로저가 `true`를 반환하고 프로세스가 지정된 횟수만큼 호출되었으면 어설션이 "통과"합니다.

```php
Process::assertRanTimes(function (PendingProcess $process, ProcessResult $result) {
    return $process->command === 'ls -la';
}, times: 3);
```

<a name="preventing-stray-processes"></a>
### Preventing Stray Processes
### 잘못된 프로세스 방지

If you would like to ensure that all invoked processes have been faked throughout your individual test or complete test suite, you can call the `preventStrayProcesses` method. After calling this method, any processes that do not have a corresponding fake result will throw an exception rather than starting an actual process:

개별 테스트 또는 전체 테스트 스위트에서 호출된 모든 프로세스가 가짜(fake)로 지정되었는지 확인하려면 `preventStrayProcesses` 메서드를 호출하면 됩니다. 이 메서드를 호출한 후 해당 가짜(fake) 결과가 없는 프로세스는 실제 프로세스를 시작하는 대신 예외를 발생시킵니다.

    use Illuminate\Support\Facades\Process;

    Process::preventStrayProcesses();

    Process::fake([
        'ls *' => 'Test output...',
    ]);

    // Fake response is returned...
    Process::run('ls -la');

    // An exception is thrown...
    Process::run('bash import.sh');
