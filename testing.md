# 테스팅(Testing)

- [소개](#introduction)
- [테스팅 정의와 실행하기](#defining-and-running-tests)
- [테스트 환경](#test-environment)
- [테스트에서 라우트 호출하기](#calling-routes-from-tests)
- [파사드 Mocking](#mocking-facades)
- [프레임워크 Assertions](#framework-assertions)
- [헬퍼 메소드](#helper-methods)
- [어플리케이션 새로 고침](#refreshing-the-application)

<a name="introduction"></a>
## 소개

라라벨은 단위 테스트를 고려하여 구성되어 있습니다. 실제로는 PHPUnit을 통한 테스팅이 별다른 설정 없이도 지원되며 어플리케이션을 위한 `phpunit.xml` 파일이  이미 설정되어 있습니다.

`tests` 디렉토리에는 테스트 예제 파일이 제공되어 있습니다. 새롭게 라라벨 어플리케이션을 설치한 후 커맨드 라인에서 그대로 `phpunit` 명령어를 실행하면 테스트를 수행할 수 있습니다.

<!--chak-comment-테스팅(Testing)-소개-->

<a name="defining-and-running-tests"></a>
## 테스트 정의 & 실행하기

테스트 케이스를 만들기 위해서는 `tests` 디렉토리에 새로운 테스트 파일을 작성하면 됩니다. 테스트 클래스는 `TestCase` 클래스를 상속받아야 합니다. 여러분은 PHPUnit을 사용할 때와 마찬가지로 테스트 메소드를 정의 하면 됩니다.

#### 테스트 클래스의 예제

	class FooTest extends TestCase {

		public function testSomethingIsTrue()
		{
			$this->assertTrue(true);
		}

	}

터미널에서 `phpunit` 명령어를 실행함으로써 어플리케이션의 모든 테스트를 실행할 수 있습니다.

> **참고** 여러분이 `setUp` 메소드를 정의했다면 `parent::setUp`을 호출하는 것을 확인하십시오.

<!--chak-comment-테스팅(Testing)-테스트-정의---실행하기-->

<a name="test-environment"></a>
## 테스트 환경

단위 테스트를 실행할 때 라라벨은 자동으로 설정 환경을 `testing`에 구성해 놓습니다. 또한, 라라벨은 테스트 환경에서의 `session`과 `cache`을 위한 설정 파일들을 포함하고 있습니다. 이 두 개의 드라이버는 테스트 환경에서 `array`로 설정되며 세션 또는 캐시 데이터는 테스팅이 진행되는 동안에만 존재한다는 것을 의미합니다. 여러분은 필요한 경우에 자유롭게 테스트 환경 설정을 만들 수 있습니다.

`testing` 환경 변수는 `phpunit.xml` 파일에 설정되어 있습니다.

<!--chak-comment-테스팅(Testing)-테스트-환경-->

<a name="calling-routes-from-tests"></a>
## 테스트에서 라우트 호출하기

#### 테스트에서 라우트 호출하기

여러분은 테스트에서 `call` 메소르들 사용하여 손쉽게 라우트를 호출할 수 있습니다:

	$response = $this->call('GET', 'user/profile');

	$response = $this->call($method, $uri, $parameters, $cookies, $files, $server, $content);

그리고 `Illuminate\Http\Response` 객체를 확인하면 됩니다:

	$this->assertEquals('Hello World', $response->getContent());

#### 테스트에서 컨트롤러 호출하기

또한, 테스트에서 컨트롤러를 호출할 수도 있습니다:

	$response = $this->action('GET', 'HomeController@index');

	$response = $this->action('GET', 'UserController@profile', ['user' => 1]);

> **주의** `action` 메소드를 사용할 경우에 여러분은 컨트롤러의 전체 네임스페이스를 지정할 필요가 없습니다. `App\Http\Controllers` 네임 스페이스에 뒤에 붙게 되는 클래스 이름만 지정하면 됩니다.

`getContent` 메소드는 응답을 통해 얻어지는 문자열을 반환합니다. 여러분의 라우트가 `View`를 반환하는 경우 `original` 속성을 사용하여 뷰에 액세스 할 수 있습니다.

	$view = $response->original;

	$this->assertEquals('John', $view['name']);

HTTPS 라우트를 호출하기 위해서는 `callSecure` 메소드를 사용하면 됩니다:

	$response = $this->callSecure('GET', 'foo/bar');

<!--chak-comment-테스팅(Testing)-테스트에서-라우트-호출하기-->

<a name="mocking-facades"></a>
## 파사드 Mocking

테스트를 진행할 때 여러분은 종종 라라벨의 static 파사드를 Mock 하기를 원할 수도 있습니다. 예를 들어, 다음의 컨트롤러 액션의 경우를 생각해 보겠습니다.

	public function getIndex()
	{
		Event::fire('foo', ['name' => 'Dayle']);

		return 'All done!';
	}

파사드에 `shouldReceive` 메소드를 사용하여 `Event` 클래스의 호출을 Mock 할 수 있으며 이것은 [Mockery](https://github.com/padraic/mockery) mock 인스턴스를 반환할 것입니다.

#### 파사드 Mocking

	public function testGetIndex()
	{
		Event::shouldReceive('fire')->once()->with('foo', ['name' => 'Dayle']);

		$this->call('GET', '/');
	}

> **주의** `Request` 파사드를 mock 해서는 안됩니다. 대신에 테스트를 수행할 때 `call` 메소드에 원하는 입력을 전달하십시오.

<!--chak-comment-테스팅(Testing)-파사드-Mocking-->

<a name="framework-assertions"></a>
## 프레임워크 Assertions

라라벨은 테스트를 돕기 위해서 몇 가지 `assert` 메소드가 준비되어 있습니다:

#### 응답이 제대로 이루어졌는지 assert

	public function testMethod()
	{
		$this->call('GET', '/');

		$this->assertResponseOk();
	}

#### 응답 코드 assert

	$this->assertResponseStatus(403);

#### 응답이 리다이렉션인지 assert

	$this->assertRedirectedTo('foo');

	$this->assertRedirectedToRoute('route.name');

	$this->assertRedirectedToAction('Controller@method');

#### 뷰가 가지는 데이터에 대한 assert

	public function testMethod()
	{
		$this->call('GET', '/');

		$this->assertViewHas('name');
		$this->assertViewHas('age', $value);
	}

#### 세션이 가지는 데이터에 대한 assert

	public function testMethod()
	{
		$this->call('GET', '/');

		$this->assertSessionHas('name');
		$this->assertSessionHas('age', $value);
	}

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

#### 지난 입력값에 대한 assert

	public function testMethod()
	{
		$this->call('GET', '/');

		$this->assertHasOldInput();
	}

<!--chak-comment-테스팅(Testing)-프레임워크-Assertions-->

<a name="helper-methods"></a>
## 헬퍼 메소드

`TestCase` 클래스는 어플리케이션의 테스팅을 돕기 위한 몇 가지의 헬퍼 메소드를 포함하고 있습니다.

#### 테스트에서 세션을 설정하고 비우기

	$this->session(['foo' => 'bar']);

	$this->flushSession();

#### 현재의 인증된 사용자를 설정하기

`be`  메소드를 사용하여 현재 인증된 사용자를 설정할 수 있습니다:

	$user = new User(['name' => 'John']);

	$this->be($user);

테스트에서 데이터베이스의 내용을 재구성하려면 `seed` 메소드를 사용하면 됩니다:

#### 테스트에서 데이터베이스 초기 값 설정 다시 실행

	$this->seed();

	$this->seed($connection);

시드를 생성하는 데에 대한 보다 자세한 정보는 공식 문서의 [마이그레이션과 시딩](/docs/migrations#database-seeding) 섹션을 참고하십시오.

<!--chak-comment-테스팅(Testing)-헬퍼-메소드-->

<a name="refreshing-the-application"></a>
## 어플리케이션 새로 고침

이미 알고 있다 시피 여러분은 테스트 메소드에서 `$this->app`을 통해서 어플리케이션([서비스 컨테이너](/docs/5.0/container))에 엑세스 할 수 있습니다. 이 서비스 컨테이너 인스턴스는 각각의 테스트 클래스 마다 새로 고침됩니다. 만약 여러분이 수동으로 어플리케이션을 새로고침 하고 싶은 경우에는 테스트 메소드에서 `refreshApplication` 메소드를 사용하면 됩니다. 이렇게 하면 테스트 케이스가 실행을 시작한 이후 IoC 컨테이너에 등록된 mock 객체와 추가적인 바인딩을 재설정 할것입니다.

<!--chak-comment-테스팅(Testing)-어플리케이션-새로-고침-->
