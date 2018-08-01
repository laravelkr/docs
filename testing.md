# 테스팅 : 시작하기

- [시작하기](#introduction)
- [환경](#environment)
- [테스트 생성 & 실행](#creating-and-running-tests)

<a name="introduction"></a>
## 시작하기

라라벨은 단위 테스트를 고려하여 구성되어 있습니다. 실제로는 PHPUnit을 통한 테스팅이 별다른 설정 없이도 지원되며 애플리케이션을 위한 `phpunit.xml` 파일이 이미 설정되어 있습니다. 또한 이 프레임워크는 다양한 표현을 통해서 애플리케이션을 테스트할 수 있도록 편리한 헬퍼 메소드들을 제공합니다.

기본적으로, 애플리케이션의 `tests` 디렉토리는 두개의 디렉토리: `Feature` 와 `Unit` 디렉토리를 가지고 있습니다. 단위테스트는 코드의 매우 작고, 독립적인 부분에 초점을 둔 테스트 입니다. 실제로, 대부분의 단위 테스트는 하나의 메소드에 포커스를 맞춥니다. 기능 테스트는 여러 객체가 서로 상호작용하는 방식 또는 JSON 엔드 포인트에 대한 전체 HTTP request-요청을 포함하여 코드의 많은 부분을 확인하는 테스트 입니다.

`Feature` 와 `Unit` 디렉토리에는 `ExampleTest.php` 테스트 예제 파일이 들어 있습니다. 새로운 라라벨 애플리케이션을 설치한 뒤에, 커맨드 라인에서 `phpunit` 명령어를 실행하여 테스트를 수행할 수 있습니다.

<a name="environment"></a>
## 환경

`phpunit`을 통해서 단위 테스트를 실행할 때 라라벨은 자동으로 설정 환경을 `testing`으로 구성합니다, 왜냐하면 `phpunit.xml`에 구동환경이 정의되어 있기 때문입니다. 라라벨은 또한 테스트 환경에서의 `session` 과 `cache`을 위한 설정 파일들을 포함하고 있습니다. 이 두개의 드라이버는 테스트 환경에서 `array` 로 설정되며 이는, 세션 또는 캐시 데이터가 테스팅이 진행되는 동안에만 존재한다는 것을 의미합니다.

여러분은 필요한 경우에 자유롭게 테스트 환경 설정 값들을 정의할 수 있습니다. `testing` 환경 변수는 `phpunit.xml` 파일에 설정되어 있습니다. 테스트를 실행 하기 전에 `config:clear` 아티즌 명령어를 실행하여 설정들에 대한 캐시를 지우는 것을 잊지 마십시오!

이에 더해, 프로젝트 루트 디렉토리에 `.env.testing` 파일을 생성할 수 있습니다. 이 파일은 PHPUnit 테스트가 실행되거나, 아티즌 명령어가 `--env=testing` 옵션과 함께 실행될때 `.env` 파일을 오버라이드 하는데 사용됩니다.

<a name="creating-and-running-tests"></a>
## 테스트 생성 & 실행

새로운 테스트 케이스를 생성하려면 `make:test` 아티즌 명령어를 이용하십시오:

    // Create a test in the Feature directory...
    php artisan make:test UserTest

    // Create a test in the Unit directory...
    php artisan make:test UserTest --unit

테스트 파일이 생성되고 나면, PHPUnit에서 사용할 테스트 메소드를 정의하면 됩니다. 테스트를 실행하려면 터미널에서 `phpunit` 커맨드를 실행하면 됩니다:

    <?php

    namespace Tests\Unit;

    use Tests\TestCase;
    use Illuminate\Foundation\Testing\RefreshDatabase;

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

> {note} 테스트 클래스 내에서 여러분만의 `setUp` 메소드를 정의한다면 반드시 `parent::setUp()`를 호출하십시오.

