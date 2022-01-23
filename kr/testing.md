# Testing: Getting Started
# 테스팅 : 시작하기

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Environment](#environment)
- [환경](#environment)
- [Creating Tests](#creating-tests)
- [테스트 생성](#creating-tests)
- [Running Tests](#running-tests)
- [테스트 실행](#running-tests)
    - [Running Tests In Parallel](#running-tests-in-parallel)
    - [병렬로 테스트 실행](#running-tests-in-parallel)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel is built with testing in mind. In fact, support for testing with PHPUnit is included out of the box and a `phpunit.xml` file is already set up for your application. The framework also ships with convenient helper methods that allow you to expressively test your applications.

라라벨은 테스트를 고려하여 제작되었습니다. 실제로 PHPUnit 테스팅 지원은 기본 포함되어 있고 `phpunit.xml` 파일은 이미 애플리케이션이 맞게 설정되어 있습니다. 또한, 프레임워크는 애플리케이션을 다양하게 테스트할 수 있도록 편리한 헬퍼 메소드들을 제공합니다.

By default, your application's `tests` directory contains two directories: `Feature` and `Unit`. Unit tests are tests that focus on a very small, isolated portion of your code. In fact, most unit tests probably focus on a single method. Tests within your "Unit" test directory do not boot your Laravel application and therefore are unable to access your application's database or other framework services.

기본적으로, 애플리케이션의 `tests` 디렉토리는 두개의 디렉토리: `Feature` 와 `Unit` 를 가지고 있습니다. 단위테스트는 코드의 매우 작고, 독립적인 부분에 초점을 둔 테스트 입니다. 실제로, 대부분의 단위 테스트는 하나의 메소드에 포커스를 맞춥니다. "Unit" 테스트 디렉토리 내의 테스트는 라라벨 응용 프로그램을 실행하지 않으므로 응용 프로그램의 데이터베이스나 다른 프레임워크 서비스에 액세스할 수 없습니다.

Feature tests may test a larger portion of your code, including how several objects interact with each other or even a full HTTP request to a JSON endpoint. **Generally, most of your tests should be feature tests. These types of tests provide the most confidence that your system as a whole is functioning as intended.**

기능 테스트는 여러 객체가 서로 상호작용하는 방식 또는 JSON 엔드 포인트에 대한 전체 HTTP 요청을 포함하여 코드의 많은 부분을 테스트할 수 있습니다. **일반적으로 대부분의 테스트는 기능 테스트여야 합니다. 이러한 유형의 테스트는 전체적으로 시스템이 올바르게 작동한다는 가장 큰 신뢰를 제공합니다.**

An `ExampleTest.php` file is provided in both the `Feature` and `Unit` test directories. After installing a new Laravel application, execute the `vendor/bin/phpunit` or `php artisan test` commands to run your tests.

`ExampleTest.php` 파일은 `Feature` 와 `Unit` 테스트 디렉토리에 모두 제공됩니다. 새로운 라라벨 애플리케이션을 설치한 후, `vendor/bin/phpunit` 또는 `php artisan test` 명령을 실행하여 테스트를 실행합니다.

<a name="environment"></a>
## Environment
## 환경

When running tests, Laravel will automatically set the [configuration environment](/docs/{{version}}/configuration#environment-configuration) to `testing` because of the environment variables defined in the `phpunit.xml` file. Laravel also automatically configures the session and cache to the `array` driver while testing, meaning no session or cache data will be persisted while testing.

테스트를 실행할 때, 라라벨은 `phpunit.xml`에 정의된 환경변수 때문에, 설정 환경을 `testing`으로 자동 설정합니다. 라라벨은 테스팅 하는 동안은 `array` 드라이버에 세션과 캐시를 자동 설정합니다. 이것은 테스트 하는 동안 세션과 캐시 데이터는 유지되지 않음을 의미합니다.

You are free to define other testing environment configuration values as necessary. The `testing` environment variables may be configured in your application's `phpunit.xml` file, but make sure to clear your configuration cache using the `config:clear` Artisan command before running your tests!

필요한 다른 테스트 환경 설정 값들을 자유롭게 정의할 수 있습니다. `testing` 환경 변수는 당신의 애플리케이션 안의 `phpunit.xml` 파일에 설정되어 있지만, 테스트를 실행 하기 전에 `config:clear` 아티즌 명령을 이용하여 설정된 캐시를 지우십시오!

In addition, you may create a `.env.testing` file in the root of your project. This file will be used instead of the `.env` file when running PHPUnit tests or executing Artisan commands with the `--env=testing` option.

추가로, 프로젝트의 루트에 `.env.testing` 파일을 생성할 수도 있습니다. 이 파일은 PHPUnit 테스트가 실행될 때나 `--env=testing` 옵션으로 아티즌 명령을 실행할때 `.env` 파일을 대신 사용됩니다.

<a name="the-creates-application-trait"></a>
#### The `CreatesApplication` Trait
#### '생성 애플리케이션'의 특성

Laravel includes a `CreatesApplication` trait that is applied to your application's base `TestCase` class. This trait contains a `createApplication` method that bootstraps the Laravel application before running your tests. It's important that you leave this trait at its original location as some features, such as Laravel's parallel testing feature, depend on it.

라라벨에는 애플리케이션의 기본 `TestCase` 클래스에 적용되는 `CreatesApplication` 특성이 포함되어 있습니다. 이 특성은 테스트를 실행하기 전에 라라벨 애플리케이션을 부트스트랩하는 `createApplication` 메서드를 포함합니다. 라라벨의 병렬 테스트 기능과 같은 일부 기능이 이 특성에 의존하므로 이 특성은 원래 위치에 두는 것이 중요합니다.

<a name="creating-tests"></a>
## Creating Tests
## 테스트 생성

To create a new test case, use the `make:test` Artisan command. By default, tests will be placed in the `tests/Feature` directory:

새로운 테스트 케이스를 생성하려면 `make:test` 아티즌 명령어를 이용하십시오. 기본적으로, 테스트에 사용되는 파일은 `tests/Feature`에 위치해 있습니다.

    php artisan make:test UserTest

If you would like to create a test within the `tests/Unit` directory, you may use the `--unit` option when executing the `make:test` command:

`tests/Unit` 디렉터리 내에 테스트를 만들려면 `make:test` 명령을 실행할 때 `--unit` 옵션을 사용할 수 있습니다.

    php artisan make:test UserTest --unit

If you would like to create a [Pest PHP](https://pestphp.com) test, you may provide the `--pest` option to the `make:test` command:

[Pest PHP](https://pestphp.com) 테스트를 생성하려면 `make:test` 명령에 `--filename` 옵션을 사용할 수 있습니다.

    php artisan make:test UserTest --pest
    php artisan make:test UserTest --unit --pest

> {tip} Test stubs may be customized using [stub publishing](/docs/{{version}}/artisan#stub-customization).

> {tip} [stub publishing](/docs/{{version}}/artisan#stub-customization)을 사용하여 Test Stub을 커스터마이징 할 수 있습니다.

Once the test has been generated, you may define test methods as you normally would using [PHPUnit](https://phpunit.de). To run your tests, execute the `vendor/bin/phpunit` or `php artisan test` command from your terminal:

테스트 파일이 생성되면, 보통 [PHPUnit](https://phpunit.de)을 사용하는 것처럼 테스트 메소드를 정의할 수 있습니다. 테스트를 실행하려면 터미널에서 `vendor/bin/phpunit` 또는 `php artisan test` 커맨드를 실행하십시오.

    <?php

    namespace Tests\Unit;

    use PHPUnit\Framework\TestCase;

    class ExampleTest extends TestCase
    {
        /**
         * A basic test example.
         *
         * @return void
         */
        public function test_basic_test()
        {
            $this->assertTrue(true);
        }
    }

> {note} If you define your own `setUp` / `tearDown` methods within a test class, be sure to call the respective `parent::setUp()` / `parent::tearDown()` methods on the parent class.

> {참고} 테스트 클래스 내에서 자신만의 `setUp` / `tearDown` 메소드를 정의한다면, 부모 클래스에서 `parent::setUp()` / `parent::tearDown()` 메소드를 호출해야 합니다.

<a name="running-tests"></a>
## Running Tests
## 테스틑 실행

As mentioned previously, once you've written tests, you may run them using `phpunit`:

이전에 언급한 바와 같이 필기 시험은 'phpunit'을 사용하여 실행할 수 있습니다.

    ./vendor/bin/phpunit

In addition to the `phpunit` command, you may use the `test` Artisan command to run your tests. The Artisan test runner provides verbose test reports in order to ease development and debugging:

`phpunit` 명령 외에도 `test` 아티즌 명령을 사용하여 테스트를 실행할 수 있습니다. 아티즌 테스트 러너는 개발 및 디버깅을 쉽게 하기 위해 자세한 테스트 보고서를 제공합니다.

    php artisan test

Any arguments that can be passed to the `phpunit` command may also be passed to the Artisan `test` command:

`phpunit` 명령어에 전달할 수 있는 모든 인수는 아티즌의 `test` 명령에도 전달될 수 있습니다.

    php artisan test --testsuite=Feature --stop-on-failure

<a name="running-tests-in-parallel"></a>
### Running Tests In Parallel
### Parallel에서 테스트 실행

By default, Laravel and PHPUnit execute your tests sequentially within a single process. However, you may greatly reduce the amount of time it takes to run your tests by running tests simultaneously across multiple processes. To get started, ensure your application depends on version `^5.3` or greater of the `nunomaduro/collision` package. Then, include the `--parallel` option when executing the `test` Artisan command:

기본적으로 라라벨과 PHPUnit은 단일 프로세스 내에서 테스트를 순차적으로 실행합니다. 그러나 여러 프로세스에서 동시에 테스트를 실행하여 테스트를 실행하는 데 걸리는 시간을 크게 줄일 수 있습니다. 시작하려면 애플리케이션이 `nunomaduro/collision` 패키지 버전 `^5.3` 이상에 의존하는지 확인하십시오. 그런 다음 `test` 아티즌 명령을 실행할 때 `--parallel` 옵션을 포함합니다.

    php artisan test --parallel

By default, Laravel will create as many processes as there are available CPU cores on your machine. However, you may adjust the number of processes using the `--processes` option:

기본적으로 라라벨은 당신의 머신에 사용 가능한 CPU 코어 수만큼 프로세스를 생성합니다. 그러나, `--processes` 옵션을 사용하여 프로세스 수를 조정할 수 있습니다.

    php artisan test --parallel --processes=4

> {note} When running tests in parallel, some PHPUnit options (such as `--do-not-cache-result`) may not be available.

> {참고} 테스트를 병렬로 실행할 때 일부 PHPUnit 옵션(예: `--do-not-cache-result`)을 사용하지 못할 수 있습니다.

<a name="parallel-testing-and-databases"></a>
#### Parallel Testing & Databases
#### 병렬 테스트 및 데이터베이스

Laravel automatically handles creating and migrating a test database for each parallel process that is running your tests. The test databases will be suffixed with a process token which is unique per process. For example, if you have two parallel test processes, Laravel will create and use `your_db_test_1` and `your_db_test_2` test databases.

라라벨은 테스트를 실행하는 각 병렬 프로세스에 대한 테스트 데이터베이스 생성 및 마이그레이션을 자동으로 처리합니다. 테스트 데이터베이스에는 프로세스마다 고유한 프로세스 토큰이 접미사로 붙습니다. 예를 들어 두 개의 병렬 테스트 프로세스가 있는 경우 라라벨은 `your_db_test_1` 및 `your_db_test_2` 테스트 데이터베이스를 만들고 사용합니다.

By default, test databases persist between calls to the `test` Artisan command so that they can be used again by subsequent `test` invocations. However, you may re-create them using the `--recreate-databases` option:

기본적으로 테스트 데이터베이스는 `test` 아티즌 명령 호출 사이에 유지되므로 후속 `test` 호출에서 다시 사용할 수 있습니다. 그러나 `--recreate-databases` 옵션을 사용하여 다시 생성할 수 있습니다.

    php artisan test --parallel --recreate-databases

<a name="parallel-testing-hooks"></a>
#### Parallel Testing Hooks
#### 병렬 테스트 후크

Occasionally, you may need to prepare certain resources used by your application's tests so they may be safely used by multiple test processes.

여러 테스트 프로세스에서 안전하게 사용할 수 있도록 애플리케이션 테스트에서 사용하는 특정 리소스를 준비해야 하는 경우가 있습니다.

Using the `ParallelTesting` facade, you may specify code to be executed on the `setUp` and `tearDown` of a process or test case. The given closures receive the `$token` and `$testCase` variables that contain the process token and the current test case, respectively:

`ParallelTesting` 파사드를 사용하여 프로세스 또는 테스트 케이스의 `setUp` 및 `tearDown`에서 실행할 코드를 지정할 수 있습니다. 주어진 클로저는 각각 프로세스 토큰과 현재 테스트 케이스를 포함하는 `$token` 및 `$testCase` 변수를 받습니다.

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\Artisan;
    use Illuminate\Support\Facades\ParallelTesting;
    use Illuminate\Support\ServiceProvider;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            ParallelTesting::setUpProcess(function ($token) {
                // ...
            });

            ParallelTesting::setUpTestCase(function ($token, $testCase) {
                // ...
            });

            // Executed when a test database is created...
            ParallelTesting::setUpTestDatabase(function ($database, $token) {
                Artisan::call('db:seed');
            });

            ParallelTesting::tearDownTestCase(function ($token, $testCase) {
                // ...
            });

            ParallelTesting::tearDownProcess(function ($token) {
                // ...
            });
        }
    }

<a name="accessing-the-parallel-testing-token"></a>
#### Accessing The Parallel Testing Token
#### 병렬 테스트 토큰 액세스

If you would like to access to current parallel process "token" from any other location in your application's test code, you may use the `token` method. This token is a unique, string identifier for an individual test process and may be used to segment resources across parallel test processes. For example, Laravel automatically appends this token to the end of the test databases created by each parallel testing process:

애플리케이션 테스트 코드의 다른 위치에서 현재 병렬 프로세스 "토큰"에 액세스하려면 `token` 메서드를 사용할 수 있습니다. 이 토큰은 개별 테스트 프로세스에 대한 고유한 문자열 식별자이며 병렬 테스트 프로세스에서 리소스를 분할하는 데 사용할 수 있습니다. 예를 들어, Laravel은 각 병렬 테스트 프로세스에서 생성된 테스트 데이터베이스의 끝에 이 토큰을 자동으로 추가합니다.

    $token = ParallelTesting::token();