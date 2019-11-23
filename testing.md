# Testing: Getting Started
# 테스팅 : 시작하기

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Environment](#environment)
- [환경](#environment)
- [Creating & Running Tests](#creating-and-running-tests)
- [테스트 생성 & 실행](#creating-and-running-tests)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel is built with testing in mind. In fact, support for testing with PHPUnit is included out of the box and a `phpunit.xml` file is already set up for your application. The framework also ships with convenient helper methods that allow you to expressively test your applications.

라라벨은 테스트를 고려하여 제작되었습니다. 실제로 PHPUnit 테스팅 지원은 기본 포함되어 있고 `phpunit.xml` 파일은 이미 애플리케이션이 맞게 설정되어 있습니다. 또한, 프레임워크는 애플리케이션을 다양하게 테스트할 수 있도록 편리한 헬퍼 메소드들을 제공합니다.

By default, your application's `tests` directory contains two directories: `Feature` and `Unit`. Unit tests are tests that focus on a very small, isolated portion of your code. In fact, most unit tests probably focus on a single method. Feature tests may test a larger portion of your code, including how several objects interact with each other or even a full HTTP request to a JSON endpoint.

기본적으로, 애플리케이션의 `tests` 디렉토리는 두개의 디렉토리: `Feature` 와 `Unit` 를 가지고 있습니다. 단위테스트는 코드의 매우 작고, 독립적인 부분에 초점을 둔 테스트 입니다. 실제로, 대부분의 단위 테스트는 하나의 메소드에 포커스를 맞춥니다. 기능 테스트는 여러 객체가 서로 상호작용하는 방식 또는 JSON 엔드 포인트에 대한 전체 HTTP 요청을 포함하여 코드의 많은 부분을 테스트할 수 있습니다.

An `ExampleTest.php` file is provided in both the `Feature` and `Unit` test directories. After installing a new Laravel application, run `phpunit` on the command line to run your tests.

`ExampleTest.php` 파일은 `Feature` 와 `Unit` 테스트 디렉토리에 모두 제공됩니다. 새로운 라라벨 애플리케이션을 설치한 후, 테스트를 실행하려면 커맨드 라인에서 `phpunit`을 실행하십시오.

<a name="environment"></a>
## Environment
## 환경

When running tests via `phpunit`, Laravel will automatically set the configuration environment to `testing` because of the environment variables defined in the `phpunit.xml` file. Laravel also automatically configures the session and cache to the `array` driver while testing, meaning no session or cache data will be persisted while testing.

`phpunit`을 통해서 테스트를 실행할 때, 라라벨은 `phpunit.xml`에 정의된 환경변수 때문에, 설정 환경을 `testing`으로 자동 설정합니다. 라라벨은 테스팅 하는 동안은 `array` 드라이버에 세션과 캐시를 자동 설정합니다. 이것은 테스트 하는 동안 세션과 캐시 데이터는 유지되지 않음을 의미합니다. 

You are free to define other testing environment configuration values as necessary. The `testing` environment variables may be configured in the `phpunit.xml` file, but make sure to clear your configuration cache using the `config:clear` Artisan command before running your tests!

필요한 다른 테스트 환경 설정 값들을 자유롭게 정의할 수 있습니다. `testing` 환경 변수는 `phpunit.xml` 파일에 설정되어 있지만, 테스트를 실행 하기 전에 `config:clear` 아티즌 명령을 이용하여 설정된 캐시를 지우십시오!

In addition, you may create a `.env.testing` file in the root of your project. This file will override the `.env` file when running PHPUnit tests or executing Artisan commands with the `--env=testing` option.

추가로, 프로젝트의 루트에 `.env.testing` 파일을 생성할 수도 있습니다. 이 파일은 PHPUnit 테스트가 실행될 때나 `--env=testing` 옵션으로 아티즌 명령을 실행할때 `.env` 파일을 오버라이드합니다.

<a name="creating-and-running-tests"></a>
## Creating & Running Tests
## 테스트 생성 & 실행

To create a new test case, use the `make:test` Artisan command:

새로운 테스트 케이스를 생성하려면 `make:test` 아티즌 명령어를 이용하십시오.

    // Create a test in the Feature directory...
    php artisan make:test UserTest

    // Create a test in the Unit directory...
    php artisan make:test UserTest --unit

Once the test has been generated, you may define test methods as you normally would using PHPUnit. To run your tests, execute the `phpunit` command from your terminal:

테스트 파일이 생성되면, 보통 PHPUnit을 사용하는 것처럼 테스트 메소드를 정의할 수 있습니다. 테스트를 실행하려면 터미널에서 `phpunit` 커맨드를 실행하십시오.

    <?php

    namespace Tests\Unit;

    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        /**
         * A basic test example.
         *
         * @return void
         */
        public function testBasicTest()
        {
            $this->assertTrue(true);
        }
    }

> {note} If you define your own `setUp` / `tearDown` methods within a test class, be sure to call the respective `parent::setUp()` / `parent::tearDown()` methods on the parent class.

> {note} 테스트 클래스 내에서 자신만의 `setUp` / `tearDown` 메소드를 정의한다면, 부모 클래스에서 `parent::setUp()` / `parent::tearDown()` 메소드를 호출해야 합니다.
