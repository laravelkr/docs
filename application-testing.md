# 애플리케이션 테스팅

- [시작하기](#introduction)
- [애플리케이션과 상호작용하기](#interacting-with-your-application)
    - [링크를 통한 상호작용](#interacting-with-links)
    - [Form을 사용한 상호작용](#interacting-with-forms)
- [JSON API 테스팅하기](#testing-json-apis)
    - [정확하게 일치하는지 확인하기](#verifying-exact-match)
    - [구조적으로 일치하는지 확인하기](#verifying-structural-match)
- [세션 / 인증](#sessions-and-authentication)
- [미들웨어 비활성화 시키기](#disabling-middleware)
- [사용자 정의 HTTP Request](#custom-http-requests)
- [PHPUnit Assertions](#phpunit-assertions)

<a name="introduction"></a>
## 시작하기

라라벨은 애플리케이션에 HTTP request-요청을 하고, 결과를 검사하고, 또한 form을 채우는 매우 다양한 사용이 가능한 API를 제공합니다. 다음에 정의된 테스트 예제를 살펴보겠습니다: 

    <?php

    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Foundation\Testing\DatabaseTransactions;

    class ExampleTest extends TestCase
    {
        /**
         * A basic functional test example.
         *
         * @return void
         */
        public function testBasicExample()
        {
            $this->visit('/')
                 ->see('Laravel 5')
                 ->dontSee('Rails');
        }
    }

`visit` 메소드는 애플리케이션에 `GET` request-요청을 만듭니다. `see` 메소드는 애플리케이션에서 반환된 reponse-응답에 주어진 텍스트가 있는지 확인합니다. `dontSee` 메소드는 주어진 텍스트가 애플리케이션 response-응답으로 반환되지 않았다는 것을 확인합니다. 이것은 라라벨이 제공하는 가장 기본적인 애플리케이션 테스트입니다. 

또한 이름이 지정된 라우트에 대한 `GET` request를 만들기 위해서 `visitRoute` 메소드를 사용할 수 있습니다: 

    $this->visitRoute('profile');

    $this->visitRoute('profile', ['user' => 1]);

<a name="interacting-with-your-application"></a>
## 애플리케이션과 상호작용하기 

물론 단순하게 주어진 응답에 텍스트가 나타날지 확인하는 것 이상의 테스트가 가능합니다. 링크 클릭하는 것과 form을 채워넣는 예제들을 살펴보겠습니다:

<a name="interacting-with-links"></a>
### 링크를 통한 상호작용

이 테스트에서는 애플리케이션에 요청을 보내고, 응답으로 돌아온 링크를 "클릭"하여, 주어진 URI로 이동하도록 할 것입니다. 예를 들어 응답으로 "About Us"이라는 텍스트 값을 가진 링크가 왔다고 가정해봅시다: 

    <a href="/about-us">About Us</a>

이제 링크를 클릭해 사용자가 알맞은 페이지로 이동했는지 확인하는 테스트를 작성해 봅시다: 

    public function testBasicExample()
    {
        $this->visit('/')
             ->click('About Us')
             ->seePageIs('/about-us');
    }

또한 `seeRouteIs` 메소드를 사용하여 사용자가 이름이 지정된 라우트에 도착했는지 확인할 수 있습니다. 

    ->seeRouteIs('profile', ['user' => 1]);

<a name="interacting-with-forms"></a>
### Form을 사용한 상호작용

라라벨은 또한 form을 테스트하는 여러 메소드들을 제공합니다. `type`, `select`, `check`, `attach`, 그리고 `press` 메소드는 form의 모든 input들과 상호작용할 수 있도록 해줍니다. 예를 들어 이 form이 애플리케이션의 등록 페이지에 존재한다고 가정해보겠습니다:

    <form action="/register" method="POST">
        {{ csrf_field() }}

        <div>
            Name: <input type="text" name="name">
        </div>

        <div>
            <input type="checkbox" value="yes" name="terms"> Accept Terms
        </div>

        <div>
            <input type="submit" value="Register">
        </div>
    </form>

이 form을 완성하고 결과를 검사할 수 있는 테스트를 작성할 수 있습니다: 

    public function testNewUserRegistration()
    {
        $this->visit('/register')
             ->type('Taylor', 'name')
             ->check('terms')
             ->press('Register')
             ->seePageIs('/dashboard');
    }

물론 form이 라디오 버튼이나 드롭다운 박스와 같은 다른 input을 가지고 있다면 이런 종류의 필드도 손쉽게 채울 수 있습니다. 다음은 각각의 form을 조작하는 메소드 목록입니다: 

메소드  | 설명
------------- | -------------
`$this->type($text, $elementName)`  |  주어진 필드에 텍스트를 "채워넣음"
`$this->select($value, $elementName)`  |  라디오 버튼 또는 드롭다운 필드를 "선택"
`$this->check($elementName)`  |  체크박스 필드를 "체크"
`$this->uncheck($elementName)`  |  체크박스 필드를 "언체크" 
`$this->attach($pathToFile, $elementName)`  |  파일을 form에 "추가"
`$this->press($buttonTextOrElementName)`  |  주어진 텍스 또는 이름의 버튼을 "누르기"

<a name="file-inputs"></a>
#### 파일 Input

form이 `file` input을 가지고 있다면, `attach` 메소드를 이용하여 form에 파일을 첨부할 수 있습니다:

    public function testPhotoCanBeUploaded()
    {
        $this->visit('/upload')
             ->attach($pathToFile, 'photo')
             ->press('Upload')
             ->see('Upload Successful!');
    }

<a name="testing-json-apis"></a>
### JSON API 테스팅하기

라라벨은 또한 JSON API와 그 결과를 테스트하기 위해 여러 헬퍼들을 제공합니다. 예를 들어, `json`, `get`, `post`, `put`, `patch`, 그리고 `delete` 메소드들을 이용하여 다양한 HTTP verb를 가진 request-요청을 할 수 있습니다. 이 메소드들에 손쉽게 데이터와 헤더를 전달할 수도 있습니다. 이를 위해 `/user`에 `POST` request-요청을 하고 원하는 데이터가 반환되는지 확인하는 테스트를 작성해보겠습니다: 

    <?php

    class ExampleTest extends TestCase
    {
        /**
         * A basic functional test example.
         *
         * @return void
         */
        public function testBasicExample()
        {
            $this->json('POST', '/user', ['name' => 'Sally'])
                 ->seeJson([
                     'created' => true,
                 ]);
        }
    }

> {팁} `seeJson` 메소드는 주어진 배열을 JSON으로 변환하고 변환된 JSON이 애플리케이션이 반환하는 JSON 응답 중 **어느 한 곳에** 존재하는 것을 확인합니다. 따라서 JSON response-응답이 다른 속성을 가지고 있어도 특정 부분이 있기만 하면 테스트는 통과할 것입니다. 

<a name="verifying-exact-match"></a>
### 정확하게 일치하는지 확인하기

주어진 배열이 반환된 JSON과 **정확히** 일치하는지 확인하고자 한다면, `seeJsonEquals` 메소드를 사용하면 됩니다: 

    <?php

    class ExampleTest extends TestCase
    {
        /**
         * A basic functional test example.
         *
         * @return void
         */
        public function testBasicExample()
        {
            $this->json('POST', '/user', ['name' => 'Sally'])
                 ->seeJsonEquals([
                     'created' => true,
                 ]);
        }
    }

<a name="verifying-structural-match"></a>
### 구조적으로 일치하는지 확인하기

JSON 응답이 지정한 구조에 부합하는지 확인하는 것도 가능합니다. 이 시나리오에서는 `seeJsonStructure` 메소드를 사용할 것이며, 기대되는 JSON 구조를 전달해야 합니다:

    <?php

    class ExampleTest extends TestCase
    {
        /**
         * A basic functional test example.
         *
         * @return void
         */
        public function testBasicExample()
        {
            $this->get('/user/1')
                 ->seeJsonStructure([
                     'name',
                     'pet' => [
                         'name', 'age'
                     ]
                 ]);
        }
    }

위의 예제는 "`name`" 과 "중첩된 `name` 과 `age`를 가지는 `pet` 객체"를 반환할 것이라고 예상하고 있습니다. 응답에 추가적인 키가 존재한다 해도 `seeJsonStructure` 에서는 실패로 간주되지 않습니다. 예를 들어 `pet`에 `weight` 속성을 가지고 있다고 해도 테스트는 여전히 통과할 것입니다.

반환된 JSON 구조가 각 목록 아이템 값의 세트에서 주어진 속성을 반드시 포함하는 리스트를 가지고 있는지 확인하기 위해 `*`를 사용할 수 있습니다:

    <?php

    class ExampleTest extends TestCase
    {
        /**
         * A basic functional test example.
         *
         * @return void
         */
        public function testBasicExample()
        {
            // Assert that each user in the list has at least an id, name and email attribute.
            $this->get('/users')
                 ->seeJsonStructure([
                     '*' => [
                         'id', 'name', 'email'
                     ]
                 ]);
        }
    }

`*` 표기는 중첩할 수도 있습니다. 다음 예제의 경우 JSON response는 각각의 사용자가 주어진 속성들을 가지고 사용자의 pet들은 중첩되어 주어진 속성들을 가지고 있는지 확인할 수 있습니다:

    $this->get('/users')
         ->seeJsonStructure([
             '*' => [
                 'id', 'name', 'email', 'pets' => [
                     '*' => [
                         'name', 'age'
                     ]
                 ]
             ]
         ]);

<a name="sessions-and-authentication"></a>
### 세션 / 인증

라라벨은 테스팅 중 세션 작업을 하는 데 필요한 여러 헬퍼들을 제공합니다. 먼저, `withSession` 메소드를 이용하여 주어진 배열을 세션 데이터로 설정할 수 있습니다. 이것은 애플리케이션에 response-응답을 전달하기 전에 데이터를 세션에 로드하는 경우에 유용합니다:

    <?php

    class ExampleTest extends TestCase
    {
        public function testApplication()
        {
            $this->withSession(['foo' => 'bar'])
                 ->visit('/');
        }
    }

물론 일반적인 세션의 이용법 중 하나는 인증된 사용자를 위해서 상태를 유지하는 것입니다. `actingAs` 헬퍼 메소드는 특정 사용자를 현재 사용자로 인증하는 단순한 방법을 제공합니다. 예를 들어, 사용자를 생성하고 인증하기 위해 [model factory](/docs/{{version}}/database-testing#writing-factories)를 사용할 수 있습니다: 

    <?php

    class ExampleTest extends TestCase
    {
        public function testApplication()
        {
            $user = factory(App\User::class)->create();

            $this->actingAs($user)
                 ->withSession(['foo' => 'bar'])
                 ->visit('/')
                 ->see('Hello, '.$user->name);
        }
    }

또한 `actingAs` 메소드의 두 번째 인자로 주어진 사용자에 대한 인증에 어떤 guard를 사용해야 하는지 지정하도록 guard 이름을 전달 할 수도 있습니다:

    $this->actingAs($user, 'api')

<a name="disabling-middleware"></a>
### 미들웨어 비활성화 시키기 

애플리케이션을 테스트할 때 일부 테스트에서 [미들웨어](/docs/{{version}}/middleware)를 비활성화 하는 편리한 방법을 원할 수도 있습니다. 이는 어떤 미들웨어에도 관계 없이 라우트와 컨트롤러를 테스트할 수 있게 해줍니다. 라라벨은 자동으로 테스트 클래스의 모든 미들웨어를 비활성화할 수 있는 간단한 `WithoutMiddleware` 트레이트-trait을 포함하고 있습니다: 

    <?php

    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Foundation\Testing\DatabaseMigrations;
    use Illuminate\Foundation\Testing\DatabaseTransactions;

    class ExampleTest extends TestCase
    {
        use WithoutMiddleware;

        //
    }

몇 개의 테스트 메소드에서만 미들웨어를 비활성화하기를 원한다면 테스트 메소드에서 `withoutMiddleware` 메소드를 호출하면 됩니다: 

    <?php

    class ExampleTest extends TestCase
    {
        /**
         * A basic functional test example.
         *
         * @return void
         */
        public function testBasicExample()
        {
            $this->withoutMiddleware();

            $this->visit('/')
                 ->see('Laravel 5');
        }
    }

<a name="custom-http-requests"></a>
### 사용자 정의 HTTP 요청

애플리케이션에 사용자 정의 HTTP request-요청을 하고 `Illuminate\Http\Response` 객체 전체를 가져오려면 `call` 메소드를 이용하면 됩니다:

    public function testApplication()
    {
        $response = $this->call('GET', '/');

        $this->assertEquals(200, $response->status());
    }

`POST`, `PUT`, 혹은 `PATCH` 요청의 경우, 해당 요청과 함께 입력 데이터의 배열을 전달할 수 있습니다. 물론 이 데이터는 [request 인스턴스](/docs/{{version}}/requests)를 통해 라우트와 컨트롤러에서 사용 가능할 수 있습니다: 

       $response = $this->call('POST', '/user', ['name' => 'Taylor']);

<a name="phpunit-assertions"></a>
### PHPUnit Assertions

라라벨은 [PHPUnit](https://phpunit.de/) 테스트를 위해 다양한 커스텀 assertion 메소드를 제공합니다. 

메소드  | 설명
------------- | -------------
`->assertResponseOk();`  |  클라이언트의 응답이 괜찮은(OK) 상태 코드를 갖고 있는지 확인. 
`->assertResponseStatus($code);`  |  클라이언트 응답이 주어진 코드를 가지고 있는지 확인.
`->assertViewHas($key, $value = null);`  |  응답 뷰가 주어진 데이터의 특정 부분을 가지고 있는지 확인. 
`->assertViewHasAll(array $bindings);`  |  뷰가 주어진 데이터의 특정 목록을 가지고 있는지 확인. 
`->assertViewMissing($key);`  |  응답 뷰가 주어진 데이터의 특정 부분을 가지고 있지 않는지 확인. 
`->assertRedirectedTo($uri, $with = []);`  |  클라이언트가 특정 URI로 리다이렉트되었는지 여부를 확인. 
`->assertRedirectedToRoute($name, $parameters = [], $with = []);`  |  클라이언트가 특정 라우트로 리다이렉트되었는지 확인. 
`->assertRedirectedToAction($name, $parameters = [], $with = []);`  |  클라이언트가 특정 동작으로 리다이렉트되었는지 확인. 
`->assertSessionHas($key, $value = null);`  |  세션이 주어진 값을 가지는지 확인.
`->assertSessionHasAll(array $bindings);`  |  세션이 주어진 값의 목록을 가지는지 확인.
`->assertSessionHasErrors($bindings = [], $format = null);`  |  세션이 주어진 오류들을 가지고 있는지 확인. 
`->assertHasOldInput();`  |  세션이 이전의 입력값을 가지고 있는지 확인.
`->assertSessionMissing($key);`  |  세션이 주어진 키를 가지고 있지 않는 것을 확인.
