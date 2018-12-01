# Testing
# 테스팅

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Environment](#environment)
- [환경](#environment)
- [Creating & Running Tests](#creating-and-running-tests)
- [테스트 생성 & 실행](#creating-and-running-tests)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel is built with testing in mind. In fact, support for testing with PHPUnit is included out of the box and a `phpunit.xml` file is already setup for your application. The framework also ships with convenient helper methods that allow you to expressively test your applications.

라라벨은 단위 테스트를 고려하여 구성되어 있습니다. 실제로는 PHPUnit을 통한 테스팅이 별다른 설정 없이도 지원되며 애플리케이션을 위한 `phpunit.xml` 파일이 이미 설정되어 있습니다. 또한 이 프레임워크는 다양한 표현을 통해서 애플리케이션을 테스트할 수 있도록 편리한 헬퍼 메소드들을 제공합니다. 

An `ExampleTest.php` file is provided in the `tests` directory. After installing a new Laravel application, simply run `phpunit` on the command line to run your tests.

`tests` 디렉토리에는 테스트 예제 파일이 제공되어 있습니다. 새롭게 라라벨 애플리케이션을 설치한 후 커맨드 라인에서 그대로 `phpunit` 명령어를 실행하면 테스트를 수행할 수 있습니다. 

<a name="environment"></a>
## Environment
## 환경

When running tests, Laravel will automatically set the configuration environment to `testing`. Laravel automatically configures the session and cache to the `array` driver while testing, meaning no session or cache data will be persisted while testing.

단위 테스트를 실행할 때 라라벨은 자동으로 설정 환경을 `testing`에 구성해 놓습니다. 또한 라라벨은 테스트 환경에서의 `session` 과 `cache`을 위한 설정 파일들을 포함하고 있습니다. 이 두개의 드라이버는 테스트 환경에서 `array` 로 설정되며 이는, 세션 또는 캐시 데이터가 테스팅이 진행되는 동안에만 존재한다는 것을 의미합니다. 

You are free to define other testing environment configuration values as necessary. The `testing` environment variables may be configured in the `phpunit.xml` file, but make sure to clear your configuration cache using the `config:clear` Artisan command before running your tests!

여러분은 필요한 경우에 자유롭게 테스트 환경 설정 값들을 정의할 수 있습니다. `testing` 환경 변수는 `phpunit.xml` 파일에 설정되어 있습니다. 테스트를 실행 하기 전에 `config:clear` 아티즌 명령어를 실행하여 설정들에 대한 캐시를 지우는 것을 잊지 마십시오!

<a name="creating-and-running-tests"></a>
## Creating & Running Tests
## 테스트 생성 & 실행

To create a new test case, use the `make:test` Artisan command:

새로운 테스트 케이스를 생성하려면 `make:test` 아티즌 명령어를 이용하십시오: 

    php artisan make:test UserTest

This command will place a new `UserTest` class within your `tests` directory. You may then define test methods as you normally would using PHPUnit. To run your tests, simply execute the `phpunit` command from your terminal:

이 명령어는 `tests` 디렉토리에 새로운 `UserTest` 클래스를 만들 것입니다. 그리고 나서 일반적인 상황과 같이 PHPUnit을 이용하여 테스트 메소드를 정의하면 됩니다. 테스트를 실행하려면 간단하게 터미널에서 `phpunit` 커맨드를 실행하면 됩니다: 

    <?php

    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Foundation\Testing\DatabaseMigrations;
    use Illuminate\Foundation\Testing\DatabaseTransactions;

    class UserTest extends TestCase
    {
        /**
         * A basic test example.
         *
         * @return void
         */
        public function testExample()
        {
            $this->assertTrue(true);
        }
    }

> {note} If you define your own `setUp` method within a test class, be sure to call `parent::setUp`.

> {note} 테스트 클래스 내에서 여러분만의 `setUp` 메소드를 정의한다면 반드시 `parent::setUp`를 호출하십시오. 

