# Testing
# 테스팅

- [Introduction 소개](#introduction)
- [Defining & Running Tests 테스팅 정의와 실행하기](#defining-and-running-tests)
- [Test Environment 테스트 환경](#test-environment)
- [Calling Routes From Tests 테스트에서 라우트 호출하기](#calling-routes-from-tests)
- [Mocking Facades 파사드 Mocking](#mocking-facades)
- [Framework Assertions 프레임워크 Assertions](#framework-assertions)
- [Helper Methods 헬퍼 메소드](#helper-methods)
- [Refreshing The Application 애플리케이션 새로 고침](#refreshing-the-application)

<a name="introduction"></a>
## Introduction
## 소개

Laravel is built with unit testing in mind. In fact, support for testing with PHPUnit is included out of the box, and a `phpunit.xml` file is already setup for your application.

라라벨은 단위 테스트를 고려하여 구성되어 있습니다. 실제로는 PHPUnit을 통한 테스팅이 별다른 설정 없이도 지원되며 애플리케이션을 위한 `phpunit.xml` 파일이  이미 설정되어 있습니다. 

An example test file is provided in the `tests` directory. After installing a new Laravel application, simply run `phpunit` on the command line to run your tests.

`tests` 디렉토리에는 테스트 예제 파일이 제공되어 있습니다. 새롭게 라라벨 애플리케이션을 설치한 후 커맨드 라인에서 그대로 `phpunit` 명령어를 실행하면 테스트를 수행할 수 있습니다. 

<a name="defining-and-running-tests"></a>
## Defining & Running Tests
## 테스트 정의 & 실행하기

To create a test case, simply create a new test file in the `tests` directory. The test class should extend `TestCase`. You may then define test methods as you normally would when using PHPUnit.

테스트 케이스를 만들기 위해서는 `tests` 디렉토리에 새로운 테스트 파일을 작성하면 됩니다. 테스트 클래스는 `TestCase` 클래스를 상속받아야 합니다. 여러분은 PHPUnit을 사용할 때와 마찬가지로 테스트 메소드를 정의 하면 됩니다. 

#### An Example Test Class
#### 테스트 클래스의 예제

	class FooTest extends TestCase {

		public function testSomethingIsTrue()
		{
			$this->assertTrue(true);
		}

	}

You may run all of the tests for your application by executing the `phpunit` command from your terminal.

터미널에서 `phpunit` 명령어를 실행함으로써 애플리케이션의 모든 테스트를 실행할 수 있습니다. 

> **Note:** If you define your own `setUp` method, be sure to call `parent::setUp`.

> **참고** 여러분이 `setUp` 메소드를 정의했다면 `parent::setUp` 을 호출하는 것을 확인하십시오. 

<a name="test-environment"></a>
## Test Environment
## 테스트 환경

When running unit tests, Laravel will automatically set the configuration environment to `testing`. Also, Laravel includes configuration files for `session` and `cache` in the test environment. Both of these drivers are set to `array` while in the test environment, meaning no session or cache data will be persisted while testing. You are free to create other testing environment configurations as necessary.

단위 테스트를 실행할 때 라라벨은 자동으로 설정 환경을 `testing`에 구성해 놓습니다. 또한 라라벨은 테스트 환경에서의 `session` 과 `cache`을 위한 설정 파일들을 포함하고 있습니다. 이 두개의 드라이버는 테스트 환경에서 `array` 로 설정되며 세션 또는 캐시 데이터는 테스팅이 진행되는 동안에만 존재한다는 것을 의미합니다. 여러분은 필요한 경우에 자유롭게 테스트 환경 설정을 만들 수 있습니다. 

The `testing` environment variables may be configured in the `phpunit.xml` file.

`testing` 환경 변수는 `phpunit.xml` 파일에 설정되어 있습니다.

<a name="calling-routes-from-tests"></a>
## Calling Routes From Tests
## 테스트에서 라우트 호출하기

#### Calling A Route From A Test
#### 테스트에서 라우트 호출하기

You may easily call one of your routes for a test using the `call` method:
여러분은 테스트에서 `call` 메소르들 사용하여 손쉽게 라우트를 호출할 수 있습니다. 

	$response = $this->call('GET', 'user/profile');

	$response = $this->call($method, $uri, $parameters, $cookies, $files, $server, $content);

You may then inspect the `Illuminate\Http\Response` object:
그리고 `Illuminate\Http\Response` 객체를 확인하면 됩니다. 

	$this->assertEquals('Hello World', $response->getContent());

#### Calling A Controller From A Test
#### 테스트에서 컨트롤러 호출하기

You may also call a controller from a test:
또한 테스트에서 컨트롤러를 호출할 수도 있습니다. 

	$response = $this->action('GET', 'HomeController@index');

	$response = $this->action('GET', 'UserController@profile', ['user' => 1]);

> **Note:** You do not need to specify the full controller namespace when using the `action` method. Only specify the portion of the class name that follows the `App\Http\Controllers` namespace.

> **주의** `action` 메소드를 사용할 경우에 여러분은 컨트롤러의 전체 네임스페이스를 지정할 필요가 없습니다. `App\Http\Controllers` 네임 스페이스에 뒤에 붙게 되는 클래스 이름만 지정하면 됩니다. 

The `getContent` method will return the evaluated string contents of the response. If your route returns a `View`, you may access it using the `original` property:

`getContent` 메소드는 응답을 통해 얻어지는 문자열을 반환합니다. 여러분의 라우트가 `View`를 반환하는 경우 `original` 속성을 사용하여 뷰에 액세스 할 수 있습니다.

	$view = $response->original;

	$this->assertEquals('John', $view['name']);

To call a HTTPS route, you may use the `callSecure` method:

HTTPS 라우트를 호출하기 위해서는 `callSecure` 메소드를 사용하면 됩니다. 

	$response = $this->callSecure('GET', 'foo/bar');

<a name="mocking-facades"></a>
## Mocking Facades
## 파사드 Mocking 

When testing, you may often want to mock a call to a Laravel static facade. For example, consider the following controller action:

테스트를 진행할 때 여러분은 종종 라라벨의 static 파사드를 Mock 하기를 원할 수도 있습니다. 예를 들어 다음의 컨트롤러 액션의 경우를 생각해 보겠습니다. 

	public function getIndex()
	{
		Event::fire('foo', ['name' => 'Dayle']);

		return 'All done!';
	}

We can mock the call to the `Event` class by using the `shouldReceive` method on the facade, which will return an instance of a [Mockery](https://github.com/padraic/mockery) mock.

파사드에 `shouldReceive` 메소드를 사용하여 `Event` 클래스의 호출을 Mock 할 수 있으며 이것은 [Mockery](https://github.com/padraic/mockery) mock 인스턴스를 반환할 것입니다. 

#### Mocking A Facade
#### 파사드 Mocking

	public function testGetIndex()
	{
		Event::shouldReceive('fire')->once()->with('foo', ['name' => 'Dayle']);

		$this->call('GET', '/');
	}

> **Note:** You should not mock the `Request` facade. Instead, pass the input you desire into the `call` method when running your test.

> **주의** `Request` 파사드를 mock 해서는 안됩니다. 대신에 테스트를 수행할 때 `call` 메소드에 원하는 입력을 전달하십시오. 

<a name="framework-assertions"></a>
## Framework Assertions
## 프레임워크 Assertions

Laravel ships with several `assert` methods to make testing a little easier:
라라벨은 테스트를 돕기 위해서 몇 가지 `assert` 메소드가 준비되어 있습니다.

#### Asserting Responses Are OK
#### 응답이 제대로 이루어졌는지 assert 

	public function testMethod()
	{
		$this->call('GET', '/');

		$this->assertResponseOk();
	}

#### Asserting Response Statuses
#### 응답 코드 assert

	$this->assertResponseStatus(403);

#### Asserting Responses Are Redirects
#### 응답이 리다이렉션인지 assert

	$this->assertRedirectedTo('foo');

	$this->assertRedirectedToRoute('route.name');

	$this->assertRedirectedToAction('Controller@method');

#### Asserting A View Has Some Data
#### 뷰가 가지는 데이터에 대한 assert

	public function testMethod()
	{
		$this->call('GET', '/');

		$this->assertViewHas('name');
		$this->assertViewHas('age', $value);
	}

#### Asserting The Session Has Some Data
#### 세션이 가지는 데이터에 대한 assert

	public function testMethod()
	{
		$this->call('GET', '/');

		$this->assertSessionHas('name');
		$this->assertSessionHas('age', $value);
	}

#### Asserting The Session Has Errors
#### 세션이 에러를 가지는지 assert

    public function testMethod()
    {
        $this->call('GET', '/');

        $this->assertSessionHasErrors();

        // Asserting the session has errors for a given key...
        $this->assertSessionHasErrors('name');

        // Asserting the session has errors for several keys...
        $this->assertSessionHasErrors(['name', 'age']);
    }

#### Asserting Old Input Has Some Data
#### 지난 입력값에 대한 assert

	public function testMethod()
	{
		$this->call('GET', '/');

		$this->assertHasOldInput();
	}

<a name="helper-methods"></a>
## Helper Methods
## 헬퍼 메소드

The `TestCase` class contains several helper methods to make testing your application easier.
`TestCase` 클래스는 애플리케이션의 테스팅을 돕기 위한 몇가지의 헬퍼 메소드를 포함하고 있습니다. 

#### Setting And Flushing Sessions From Tests
#### 테스트에서 세션을 설정하고 비우기

	$this->session(['foo' => 'bar']);

	$this->flushSession();

#### Setting The Currently Authenticated User
#### 현재의 인증된 사용자를 설정하기

You may set the currently authenticated user using the `be` method:
`be` 메소드를 사용하여 현재 인증된 사용자를 설정할 수 있습니다. 

	$user = new User(['name' => 'John']);

	$this->be($user);

#### Re-Seeding Database From Tests
#### 테스트에서 데이터베이스 초기 값 설정 다시 실행

You may re-seed your database from a test using the `seed` method:

테스트에서 데이터베이스의 내용을 재구성하려면 `seed` 메소드를 사용하면 됩니다. 

	$this->seed();

	$this->seed($connection);

More information on creating seeds may be found in the [migrations and seeding](/docs/migrations#database-seeding) section of the documentation.

시드를 생성하는 데에 대한 보다 자세한 정보는 공식 문서의 [마이그레이션 과 시딩](/docs/migrations#database-seeding) 섹션을 참고하십시오. 

<a name="refreshing-the-application"></a>
## Refreshing The Application
## 애플리케이션 새로 고침

As you may already know, you can access your Application ([service container](/docs/{{version}}/container)) via `$this->app` from any test method. This service container instance is refreshed for each test class. If you wish to manually force the Application to be refreshed for a given method, you may use the `refreshApplication` method from your test method. This will reset any extra bindings, such as mocks, that have been placed in the IoC container since the test case started running.

이미 알고 있다 시피 여러분은 테스트 메소드에서 `$this->app` 을 통해서 애플리케이션([서비스 컨테이너](/docs/{{version}}/container))에 엑세스 할 수 있습니다. 이 서비스 컨테이너 인스턴스는 각각의 테스트 클래스 마다 새로 고침됩니다. 만약 여러분이 수동으로 애플리케이션을 새로고침 하고 싶은 경우에는 테스트 메소드에서 `refreshApplication` 메소드를 사용하면 됩니다. 이렇게 하면 테스트 케이스가 실행을 시작한 이후 IoC 컨테이너에 등록된 mock 객체와 추가적인 바인딩을 재설정 할것입니다. 
