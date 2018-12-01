# Testing
# 테스팅

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Application Testing](#application-testing)
- [애플리케이션 테스팅](#application-testing)
    - [Interacting With Your Application](#interacting-with-your-application)
    - [애플리케이션과 상호작용하기](#interacting-with-your-application)
    - [Testing JSON APIs](#testing-json-apis)
    - [Testing JSON APIs](#testing-json-apis)
    - [Sessions / Authentication](#sessions-and-authentication)
    - [Sessions / Authentication](#sessions-and-authentication)
    - [Disabling Middleware](#disabling-middleware)
    - [Disabling Middleware](#disabling-middleware)
    - [Custom HTTP Requests](#custom-http-requests)
    - [Custom HTTP Requests](#custom-http-requests)
    - [PHPUnit Assertions](#phpunit-assertions)
    - [PHPUnit Assertions](#phpunit-assertions)
- [Working With Databases](#working-with-databases)
- [Working With Databases](#working-with-databases)
    - [Resetting The Database After Each Test](#resetting-the-database-after-each-test)
    - [Resetting The Database After Each Test](#resetting-the-database-after-each-test)
    - [Model Factories](#model-factories)
    - [Model Factories](#model-factories)
- [Mocking](#mocking)
- [Mocking](#mocking)
    - [Mocking Events](#mocking-events)
    - [Mocking Events](#mocking-events)
    - [Mocking Jobs](#mocking-jobs)
    - [Mocking Jobs](#mocking-jobs)
    - [Mocking Facades](#mocking-facades)
    - [Mocking Facades](#mocking-facades)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel is built with testing in mind. In fact, support for testing with PHPUnit is included out of the box, and a `phpunit.xml` file is already setup for your application. The framework also ships with convenient helper methods allowing you to expressively test your applications.

라라벨은 단위 테스트를 고려하여 구성되어 있습니다. 실제로는 PHPUnit을 통한 테스팅이 별다른 설정 없이도 지원되며 애플리케이션을 위한 `phpunit.xml` 파일이 이미 설정되어 있습니다. 또한 이 프레임워크는 다양한 표현을 통해서 애플리케이션을 테스트할 수 있도록 편리한 헬퍼 메소드들을 제공합니다. 

An `ExampleTest.php` file is provided in the `tests` directory. After installing a new Laravel application, simply run `phpunit` on the command line to run your tests.

`tests` 디렉토리에는 테스트 예제 파일이 제공되어 있습니다. 새롭게 라라벨 애플리케이션을 설치한 후 커맨드 라인에서 그대로 `phpunit` 명령어를 실행하면 테스트를 수행할 수 있습니다. 

### Test Environment 
### 테스트 환경

When running tests, Laravel will automatically set the configuration environment to `testing`. Laravel automatically configures the session and cache to the `array` driver while testing, meaning no session or cache data will be persisted while testing.

단위 테스트를 실행할 때 라라벨은 자동으로 설정 환경을 `testing`에 구성해 놓습니다. 또한 라라벨은 테스트 환경에서의 `session` 과 `cache`을 위한 설정 파일들을 포함하고 있습니다. 이 두개의 드라이버는 테스트 환경에서 `array` 로 설정되며 이는, 세션 또는 캐시 데이터가 테스팅이 진행되는 동안에만 존재한다는 것을 의미합니다. 

You are free to create other testing environment configurations as necessary. The `testing` environment variables may be configured in the `phpunit.xml` file.

여러분은 필요한 경우에 자유롭게 테스트 환경 설정을 생성할 수 있습니다. `testing` 환경 변수는 `phpunit.xml` 파일에 설정되어 있습니다.

### Defining & Running Tests 
### 테스트 정의 & 실행하기

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

> **Note:** If you define your own `setUp` method within a test class, be sure to call `parent::setUp`.

> **주의:** 테스트 클래스 내에서 여러분만의 `setUp` 메소드를 정의한다면 반드시 `parent::setUp`를 호출하십시오. 

<a name="application-testing"></a>
## Application Testing 
## 애플리케이션 테스트하기

Laravel provides a very fluent API for making HTTP requests to your application, examining the output, and even filling out forms. For example, take a look at the `ExampleTest.php` file included in your `tests` directory:

라라벨은 애플리케이션에 HTTP request-요청을 하고, 결과을 검사하고, 또한 form을 채우는 매우 다양한 사용이 가능한 API를 제공합니다. 다음과 같이, `tests` 디렉토리의 `ExampleTest.php` 파일을 살펴보겠습니다: 

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

The `visit` method makes a `GET` request into the application. The `see` method asserts that we should see the given text in the response returned by the application. The `dontSee` method asserts that the given text is not returned in the application response. This is the most basic application test available in Laravel.

`visit` 메소드는 애플리케이션에 `GET` request-요청을 만듭니다. `see` 메소드는 주어진 텍스트가 애플리케이션에서 반환된 reponse-응답에 있는지 확인 합니다. `dontSee` 메소드는 주어진 텍스트가 애플리케이션 response-응답으로 반환되지 않았다는 것을 확인합니다. 이것은 라라벨이 제공하는 가장 기본적인 애플리케이션 테스트입니다. 

<a name="interacting-with-your-application"></a>
### Interacting With Your Application 
### 애플리케이션과 상호작용하기 

Of course, you can do much more than simply assert that text appears in a given response. Let's take a look at some examples of clicking links and filling out forms:

물론 단순하게 주어진 응답에 텍스트가 나타날지 확인하는 것 이상의 테스트가 가능합니다. 링크 클릭하는 것과 form을 채워넣는 예제들을 살펴보겠습니다:

#### Clicking Links 
#### 링크 클릭하기

In this test, we will make a request to the application, "click" a link in the returned response, and then assert that we landed on a given URI. For example, let's assume there is a link in our response that has a text value of "About Us":

이 테스트에서는 애플리케이션에 요청을 보내고, 응답으로 돌아온 링크를 "클릭"하여, 특정 URI에 이동된것을 확인할 것입니다. 예를 들어 응답으로 "About Us"이라는 텍스트 값을 가진 링크가 왔다고 가정해봅시다: 

    <a href="/about-us">About Us</a>

Now, let's write a test that clicks the link and asserts the user lands on the correct page:

이제 링크를 클릭하고 사용자가 알맞은 페이지로 이동한다고 확인하는 테스트를 작성해 봅시다: 

    public function testBasicExample()
    {
        $this->visit('/')
             ->click('About Us')
             ->seePageIs('/about-us');
    }

#### Working With Forms 
#### Form과 관련된 작업하기 

Laravel also provides several methods for testing forms. The `type`, `select`, `check`, `attach`, and `press` methods allow you to interact with all of your form's inputs. For example, let's imagine this form exists on the application's registration page:

라라벨은 또한 form을 테스트하는 여러 메소드들을 제공합니다. `type`, `select`, `check`, `attach`, 그리고 `press` 메소드들은 form의 모든 input들과 상호작용할 수 있도록 해줍니다. 예를 들어 이 form이 애플리케이션의 등록 페이지에 존재한다고 가정해보겠습니다: 

    <form action="/register" method="POST">
        {!! csrf_field() !!}

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

We can write a test to complete this form and inspect the result:

이 form을 완성하고 결과를 검사할 수 있는 테스트를 작성할 수 있습니다: 

    public function testNewUserRegistration()
    {
        $this->visit('/register')
             ->type('Taylor', 'name')
             ->check('terms')
             ->press('Register')
             ->seePageIs('/dashboard');
    }

Of course, if your form contains other inputs such as radio buttons or drop-down boxes, you may easily fill out those types of fields as well. Here is a list of each form manipulation method:

물론 form이 라디오 버튼이나 드롭다운 박스와 같은 다른 input을 가지고 있다면 이런 종류의 필드들도 손쉽게 채울 수 있습니다. 다음은 각각의 form을 조작하는 메소드 목록입니다: 

Method  | Description
------------- | -------------
`$this->type($text, $elementName)`  |  "Type" text into a given field.
`$this->select($value, $elementName)`  |  "Select" a radio button or drop-down field.
`$this->check($elementName)`  |  "Check" a checkbox field.
`$this->attach($pathToFile, $elementName)`  |  "Attach" a file to the form.
`$this->press($buttonTextOrElementName)`  |  "Press" a button with the given text or name.

메소드  | 설명
------------- | -------------
`$this->type($text, $elementName)`  |  주어진 필드에 텍스트를 "채워넣음"
`$this->select($value, $elementName)`  |  라디오 버튼 또는 드랍다운 필드를 "선택"
`$this->check($elementName)`  |  체크박스 필드를 "체크"
`$this->attach($pathToFile, $elementName)`  |  파일을 form에 "추가"
`$this->press($buttonTextOrElementName)`  |  주어진 텍스 또는 이름의 버튼을 "누르기"

#### Working With Attachments 
#### 첨부파일 관련 작업하기 

If your form contains `file` input types, you may attach files to the form using the `attach` method:

form이 `file` input 타입을 포함하고 있다면, `attach` 메소드를 이용하여 form에 파일을 첨부할 수 있습니다: 

    public function testPhotoCanBeUploaded()
    {
        $this->visit('/upload')
             ->name('File Name', 'name')
             ->attach($absolutePathToFile, 'photo')
             ->press('Upload')
             ->see('Upload Successful!');
    }

<a name="testing-json-apis"></a>
### Testing JSON APIs 
### JSON API 테스트하기 

Laravel also provides several helpers for testing JSON APIs and their responses. For example, the `get`, `post`, `put`, `patch`, and `delete` methods may be used to issue requests with various HTTP verbs. You may also easily pass data and headers to these methods. To get started, let's write a test to make a `POST` request to `/user` and assert that a given array was returned in JSON format:

라라벨은 또한 JSON API와 그 결과를 테스트하기 위해 여러 헬퍼들을 제공합니다. 예를 들어, `get`, `post`, `put`, `patch`, 그리고 `delete` 메소드들을 이용하여 다양한 HTTP verb를 가진 request-요청을 할 수 있습니다. 이 메소드들에 손쉽게 데이터와 헤더를 전달할 수도 있습니다. 이를 위해 `/user`에 `POST` request-요청을 하고 주어진 배열이 JSON 형식으로 반환되었는지 확인하는 테스트를 작성해보겠습니다: 

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
            $this->post('/user', ['name' => 'Sally'])
                 ->seeJson([
                     'created' => true,
                 ]);
        }
    }

The `seeJson` method converts the given array into JSON, and then verifies that the JSON fragment occurs **anywhere** within the entire JSON response returned by the application. So, if there are other properties in the JSON response, this test will still pass as long as the given fragment is present.

`seeJson` 메소드는 주어진 배열을 JSON으로 변환하고 변환된 JSON이 애플리케이션이 반환하는 JSON 응답 중 **어느 한곳에** 존재하는 것을 확인합니다. 따라서 JSON response-응답이 다른 속성을 가지고 있어도 특정 부분이 있기만 하면 테스트는 통과할 것입니다. 

#### Verify Exact JSON Match
#### JSON과 완전히 일치하는지 확인하기

If you would like to verify that the given array is an **exact** match for the JSON returned by the application, you should use the `seeJsonEquals` method:

주어진 배열이 반환된 JSON과 **정확히** 일치하는지 확인하고자 한다면, `seeJsonEquals` 메소드를 사용하면됩니다: 

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
            $this->post('/user', ['name' => 'Sally'])
                 ->seeJsonEquals([
                     'created' => true,
                 ]);
        }
    }

<a name="sessions-and-authentication"></a>
### Sessions / Authentication 
### 세션 / 인증

Laravel provides several helpers for working with the session during testing. First, you may set the session data to a given array using the `withSession` method. This is useful for loading the session with data before testing a request to your application:

라라벨은 테스팅 중 세션 작업을 하는 데 필요한 여러 헬퍼들을 제공합니다. 먼저, `withSession` 메소드를 이용하여 주어진 배열을 세션 데이터로 설정 할 수 있습니다. 이것은 애플리케이션에 response-응답을 테스트해보기 전에 데이터를 세션에 로드하는 경우에 유용합니다:

    <?php

    class ExampleTest extends TestCase
    {
        public function testApplication()
        {
            $this->withSession(['foo' => 'bar'])
                 ->visit('/');
        }
    }

Of course, one common use of the session is for maintaining user state, such as the authenticated user. The `actingAs` helper method provides a simple way to authenticate a given user as the current user. For example, we may use a [model factory](#model-factories) to generate and authenticate a user:

물론 일반적인 세션의 이용법 중 하나는 인증된 사용자와 같이 사용자 상태를 유지하는 것입니다. `actingAs` 헬퍼 메소드는 특정 사용자를 현재 사용자로 인증하는 단순한 방법을 제공합니다. 예를 들어, 사용자를 생성하고 인증하기 위해 [model factory](#model-factories)를 사용할 수 있습니다: 

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

<a name="disabling-middleware"></a>
### Disabling Middleware 
### 미들웨어 비활성화 시키기

When testing your application, you may find it convenient to disable [middleware](/docs/{{version}}/middleware) for some of your tests. This will allow you to test your routes and controller in isolation from any middleware concerns. Laravel includes a simple `WithoutMiddleware` trait that you can use to automatically disable all middleware for the test class:

애플리케이션을 테스트할 때 몇몇 테스트에서 [미들웨어](/docs/{{version}}/middleware)를 비활성화 하는 편리한 방법을 원할 수도 있습니다. 이는 어떤 미들웨어에도 관계 없이 라우트와 컨트롤러를 테스트할 수 있게 해줍니다. 라라벨은 자동으로 테스트 클래스의 모든 미들웨어를 비활성화할 수 있는 간단한 `WithoutMiddleware` 트레이트-trait을 포함하고 있습니다: 

    <?php

    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Foundation\Testing\DatabaseTransactions;

    class ExampleTest extends TestCase
    {
        use WithoutMiddleware;

        //
    }

If you would like to only disable middleware for a few test methods, you may call the `withoutMiddleware` method from within the test methods:

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
### Custom HTTP Requests 
### 사용자 정의-커스텀 HTTP 요청

If you would like to make a custom HTTP request into your application and get the full `Illuminate\Http\Response` object, you may use the `call` method:

애플리케이션에 사용자 정의 HTTP request-요청을 하고 `Illuminate\Http\Response` 객체 전체를 가지고자 한다면 `call` 메소드를 이용하면 됩니다: 

    public function testApplication()
    {
        $response = $this->call('GET', '/');

        $this->assertEquals(200, $response->status());
    }

If you are making `POST`, `PUT`, or `PATCH` requests you may pass an array of input data with the request. Of course, this data will be available in your routes and controller via the [Request instance](/docs/{{version}}/requests):

`POST`, `PUT`, 혹은 `PATCH` 요청의 경우, 해당 요청과 함께 입력 데이터의 배열을 전달할 수 있습니다. 물론 이 데이터는 [request 인스턴스](/docs/{{version}}/requests)를 통해 라우트와 컨트롤러에서 사용 가능할 수 있습니다: 

       $response = $this->call('POST', '/user', ['name' => 'Taylor']);

<a name="phpunit-assertions"></a>
### PHPUnit Assertions
### PHPUnit Assertions

Laravel provides several additional assertion methods for [PHPUnit](https://phpunit.de/) tests:

라라벨은 [PHPUnit](https://phpunit.de/) 테스트를 위해 몇가지 assertion 메소드를 추가로 제공합니다. 

Method  | Description
------------- | -------------
`->assertResponseOk();`  |  Assert that the client response has an OK status code.
`->assertResponseStatus($code);`  |  Assert that the client response has a given code.
`->assertViewHas($key, $value = null);`  |  Assert that the response view has a given piece of bound data.
`->assertViewHasAll(array $bindings);`  |  Assert that the view has a given list of bound data.
`->assertViewMissing($key);`  |  Assert that the response view is missing a piece of bound data.
`->assertRedirectedTo($uri, $with = []);`  |  Assert whether the client was redirected to a given URI.
`->assertRedirectedToRoute($name, $parameters = [], $with = []);`  |  Assert whether the client was redirected to a given route.
`->assertRedirectedToAction($name, $parameters = [], $with = []);`  |  Assert whether the client was redirected to a given action.
`->assertSessionHas($key, $value = null);`  |  Assert that the session has a given value.
`->assertSessionHasAll(array $bindings);`  |  Assert that the session has a given list of values.
`->assertSessionHasErrors($bindings = [], $format = null);`  |  Assert that the session has errors bound.
`->assertHasOldInput();`  |  Assert that the session has old input.


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

<a name="working-with-databases"></a>
## Working With Databases 
## 데이터베이스 작업하기

Laravel also provides a variety of helpful tools to make it easier to test your database driven applications. First, you may use the `seeInDatabase` helper to assert that data exists in the database matching a given set of criteria. For example, if we would like to verify that there is a record in the `users` table with the `email` value of `sally@example.com`, we can do the following:

라라벨은 또한 데이터베이스를 기반으로 하는 애플리케이션을 손쉽게 테스트 할수 있도록 도와주는 다양한 툴을 제공합니다. 우선 `seeInDatabase` 헬퍼를 이용하여 데이터베이스 안에 특정 조건을 만족하는 데이터가 존재하는지 확인할 수 있습니다. 예를 들어, `users` 테이블에 `sally@example.com`의 `email` 값을 가진 레코드가 존재하는지 확인하기 위해 다음과 같이 할 수 있습니다: 

    public function testDatabase()
    {
        // Make call to application...

        $this->seeInDatabase('users', ['email' => 'sally@example.com']);
    }

Of course, the `seeInDatabase` method and other helpers like it are for convenience. You are free to use any of PHPUnit's built-in assertion methods to supplement your tests.

물론, 편의성을 위해서 `seeInDatabase` 메소드와 그와 비슷한 다른 헬퍼도 존재합니다. 여러분의 테스트를 보완하기 위해서 PHPUnit에 내장되어 있는 어떤 assertion 메소드라도 사용할 수 있습니다. 

<a name="resetting-the-database-after-each-test"></a>
### Resetting The Database After Each Test 
### 각각의 테스트 수행 후에 데이터베이스 재설정하기 

It is often useful to reset your database after each test so that data from a previous test does not interfere with subsequent tests.

종종 이전의 테스트를 위한 데이터가 이어지는 테스트들을 방해하는 것을 방지하기 위해 각 테스트 후에 데이터베이스를 재설정하는 것이 유용합니다. 

#### Using Migrations 
#### 마이그레이션 이용하기 

One option is to rollback the database after each test and migrate it before the next test. Laravel provides a simple `DatabaseMigrations` trait that will automatically handle this for you. Simply use the trait on your test class:

첫번째 대안은 각각의 테스트 수행 후에 데이터베이스를 롤백하고 다음 테스트를 수행하기 전에 다시 마이그레이션을 실행하는 것입니다. 라라벨은 `DatabaseMigrations` 트레이트-trait을 제공하여 이를 자동으로 처리 해줍니다. 간단하게 테스트 클래스에서 이 트레이트-trait을 사용하면 됩니다: 

    <?php

    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Foundation\Testing\DatabaseMigrations;
    use Illuminate\Foundation\Testing\DatabaseTransactions;

    class ExampleTest extends TestCase
    {
        use DatabaseMigrations;

        /**
         * A basic functional test example.
         *
         * @return void
         */
        public function testBasicExample()
        {
            $this->visit('/')
                 ->see('Laravel 5');
        }
    }

#### Using Transactions 
#### 트랜잭션 이용하기 

Another option is to wrap every test case in a database transaction. Again, Laravel provides a convenient `DatabaseTransactions` trait that will automatically handle this:

또다른 대안은 데이터베이스 트랜잭션으로 모든 테스트 케이스를 감싸는 것입니다. 이 때에도 라라벨은 편리하게 `DatabaseTransactions` 트레이트-trait을 제공하여 이것이 자동으로 처리되도록 합니다: 

    <?php

    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Foundation\Testing\DatabaseMigrations;
    use Illuminate\Foundation\Testing\DatabaseTransactions;

    class ExampleTest extends TestCase
    {
        use DatabaseTransactions;

        /**
         * A basic functional test example.
         *
         * @return void
         */
        public function testBasicExample()
        {
            $this->visit('/')
                 ->see('Laravel 5');
        }
    }

> **Note:** This trait will only wrap the default database connection in a transaction.

> **주의: ** 이 트레이트-trait은 트랜잭션의 기본 데이터베이스 연결-connection만을 둘러싸게 됩니다. 

<a name="model-factories"></a>
### Model Factories 
### 모델 팩토리

When testing, it is common to need to insert a few records into your database before executing your test. Instead of manually specifying the value of each column when you create this test data, Laravel allows you to define a default set of attributes for each of your [Eloquent models](/docs/{{version}}/eloquent) using "factories". To get started, take a look at the `database/factories/ModelFactory.php` file in your application. Out of the box, this file contains one factory definition:

테스트를 실행하기 전에 흔히 데이터베이스에 몇몇 레코드를 입력하는 것이 필요할 때가 있습니다. 이 테스트 데이터를 생성할 때 수동으로 각각의 컬럼의 값을 지정하는 대신에 라라벨은 "팩토리"를 사용하여 각각의 [Eloquent 모델](/docs/{{version}}/eloquent)을 위한 기본 속성의 세트를 정의하도록 해줍니다. 먼저 애플리케이션의 `database/factories/ModelFactory.php` 파일을 살펴보겠습니다. 이 파일은 바로 사용이 가능한 하나의 팩토리 정의를 가지고 있습니다. 

    $factory->define(App\User::class, function (Faker\Generator $faker) {
        return [
            'name' => $faker->name,
            'email' => $faker->email,
            'password' => bcrypt(str_random(10)),
            'remember_token' => str_random(10),
        ];
    });

Within the Closure, which serves as the factory definition, you may return the default test values of all attributes on the model. The Closure will receive an instance of the [Faker](https://github.com/fzaninotto/Faker) PHP library, which allows you to conveniently generate various kinds of random data for testing.

팩토리의 정의를 제공하는 클로저 안에서 모든 모델의 속성의 기본 테스트 값을 반환할 수 있습니다. 클로저는 [Faker](https://github.com/fzaninotto/Faker) PHP 라이브러리 인스턴스를 전달 받을 것이고, 이는 테스트할 수 있는 다양한 랜덤 데이터를 편리하게 생성할 수 있게 해줍니다. 

Of course, you are free to add your own additional factories to the `ModelFactory.php` file.

물론 `ModelFactory.php` 파일에 여러분의 고유한 팩토리들을 자유롭게 추가할 수 있습니다. 

#### Multiple Factory Types 
#### 여러가지 팩토리 타입들

Sometimes you may wish to have multiple factories for the same Eloquent model class. For example, perhaps you would like to have a factory for "Administrator" users in addition to normal users. You may define these factories using the `defineAs` method:

여러분은 종종 동일한 Eloquent 모델 클래스를 위해 여러 개의 팩토리들을 가지고자 할수도 있습니다. 예를 들어, 보통 사용자들 외에도 "관리자" 사용자들을 위한 팩토리를 원할 수 있습니다. `defineAs` 메소드로 이 팩토리들을 정의 할 수 있습니다.

    $factory->defineAs(App\User::class, 'admin', function ($faker) {
        return [
            'name' => $faker->name,
            'email' => $faker->email,
            'password' => str_random(10),
            'remember_token' => str_random(10),
            'admin' => true,
        ];
    });

Instead of duplicating all of the attributes from your base user factory, you may use the `raw` method to retrieve the base attributes. Once you have the attributes, simply supplement them with any additional values you require:

베이스 사용자 팩토리에서 모든 속성들을 복제하는 대신에, 기본 속성들을 조회하기 위해서 `raw` 메소드를 사용할 수 있습니다. 속성들을 조회한 뒤에는 필요한 어떤 값들로도 보완 할 수 있습니다. 

    $factory->defineAs(App\User::class, 'admin', function ($faker) use ($factory) {
        $user = $factory->raw(App\User::class);

        return array_merge($user, ['admin' => true]);
    });

#### Using Factories In Tests
#### 테스트 안에서 팩토리 사용하기

Once you have defined your factories, you may use them in your tests or database seed files to generate model instances using the global `factory` function. So, let's take a look at a few examples of creating models. First, we'll use the `make` method, which creates models but does not save them to the database:

팩토리를 정의한 뒤에는, 테스트 안에서나 데이터 시드 파일에서 글로벌 `factory` 함수를 이용하여 모델 인스턴스를 생성할 수 있습니다. 그럼 모델을 생성하는 몇몇의 예제들을 살펴보겠습니다. 우선 모델을 생성하지만 데이터베이스에 저장하지는 않는, `make` 메소드를 사용할 것입니다: 

    public function testDatabase()
    {
        $user = factory(App\User::class)->make();

        // Use model in tests...
    }

If you would like to override some of the default values of your models, you may pass an array of values to the `make` method. Only the specified values will be replaced while the rest of the values remain set to their default values as specified by the factory:

모델의 몇몇 기본값을 대체하고 싶다면 `make` 메소드로 값들의 배열을 전달하면 됩니다. 지정된 값들만 대체되고 나머지 값들은 팩토리에 의해 지정된 기본값들을 유지할 것입니다. 

    $user = factory(App\User::class)->make([
        'name' => 'Abigail',
       ]);

You may also create a Collection of many models or create models of a given type:

또한 여러 모델의 컬렉션을 생성하거나 주어진 타입의 모델을 생성할 수도 있습니다: 

    // Create three App\User instances...
    $users = factory(App\User::class, 3)->make();

    // Create an App\User "admin" instance...
    $user = factory(App\User::class, 'admin')->make();

    // Create three App\User "admin" instances...
    $users = factory(App\User::class, 'admin', 3)->make();

#### Persisting Factory Models 
#### 팩토리 모델의 데이터 저장하기

The `create` method not only creates the model instances, but also saves them to the database using Eloquent's `save` method:

`create` 메소드는 모델 인스턴스를 단지 생성하기만 하기 때문에, 데이터베이스에 저장하려면, Eloquent의 `save` 메소드를 사용하면 됩니다: 

    public function testDatabase()
    {
        $user = factory(App\User::class)->create();

        // Use model in tests...
    }

Again, you may override attributes on the model by passing an array to the `create` method:

이 경우에도 `create` 메소드에 배열을 전달하여 모델의 속성들을 재지정 할 수 있습니다: 

    $user = factory(App\User::class)->create([
        'name' => 'Abigail',
       ]);

#### Adding Relations To Models 
#### 모델에 관계 추가하기 

You may even persist multiple models to the database. In this example, we'll even attach a relation to the created models. When using the `create` method to create multiple models, an Eloquent [collection instance](/docs/{{version}}/eloquent-collections) is returned, allowing you to use any of the convenient functions provided by the collection, such as `each`:
[KO]
여러분은 데이터베이스에 여러 모델을 유지하고 싶을 수도 있습니다. 이 예제에서는, 생성된 모델에 관계를 추가할 것입니다. 여러 모델을 생성하기 위해서 `create` 메소드를 사용할 때 Eloquent [컬렉션 인스턴스](/docs/{{version}}/eloquent-collections)가 반환되어 `each`와 같이 컬렉션이 제공하는 편리한 함수들을 이용할 수 있도록 해줍니다: 

    $users = factory(App\User::class, 3)
               ->create()
               ->each(function($u) {
                    $u->posts()->save(factory(App\Post::class)->make());
                });

<a name="mocking"></a>
## Mocking 
## Mocking

<a name="mocking-events"></a>
### Mocking Events 
### 이벤트 Mocking

If you are making heavy use of Laravel's event system, you may wish to silence or mock certain events while testing. For example, if you are testing user registration, you probably do not want all of a `UserRegistered` event's handlers firing, since these may send "welcome" e-mails, etc.

라라벨의 이벤트 시스템을 많이 사용 중이라면, 테스트 도중에 어떤 이벤트들을 조용하게 시키거나 mock하고자 할 수도 있을 것입니다. 예를 들어, 사용자 등록을 테스트하고 있다면 `UserRegistered` 이벤트의 핸들러가 처리되어 "welcome" 이메일이 보내지는 것을 원하지 않을수도 있을 것입니다.

Laravel provides a convenient `expectsEvents` method that verifies the expected events are fired, but prevents any handlers for those events from running:

라라벨은 예상되는 이벤트들이 발행했다는 것을 확인하고, 그 이벤트들의 핸들러들이 실행되는 것을 방지해주는 `expectsEvents` 메소드를 제공합니다: 

    <?php

    class ExampleTest extends TestCase
    {
        public function testUserRegistration()
        {
            $this->expectsEvents(App\Events\UserRegistered::class);

            // Test user registration code...
        }
    }

If you would like to prevent all event handlers from running, you may use the `withoutEvents` method:

모든 이벤트 핸들러들이 실행되는 것을 방지하고 싶다면 `withoutEvents` 메소드를 사용하면 됩니다: 

    <?php

    class ExampleTest extends TestCase
    {
        public function testUserRegistration()
        {
            $this->withoutEvents();

            // Test user registration code...
        }
    }

<a name="mocking-jobs"></a>
### Mocking Jobs 
### Job Mocking

Sometimes, you may wish to simply test that specific jobs are dispatched by your controllers when making requests to your application. This allows you to test your routes / controllers in isolation - set apart from your job's logic. Of course, you can then test the job itself in a separate test class.

가끔은 애플리케이션에 요청했을 때 특정 작업이 컨트롤러에서 의해서 처리되는지 확인하는 테스트를 하고 싶을 수 있습니다. 이는 작업의 로직으로부터 라우트와 컨트롤러를 분리하여  테스트할 수 있게 해줍니다. 물론 그러고 나서 개별 테스트 클래스 에서 Job 자체에 대해서 테스트할 수 있습니다. 

Laravel provides a convenient `expectsJobs` method that will verify that the expected jobs are dispatched, but the job itself will not be executed:

라라벨은 예정된 job이 보내졌다는 것을 확인하지만 실행은 하지 않는 것을 확인하는 편리한 `expectsJobs` 메소드를 제공합니다: 

    <?php

    class ExampleTest extends TestCase
    {
        public function testPurchasePodcast()
        {
            $this->expectsJobs(App\Jobs\PurchasePodcast::class);

            // Test purchase podcast code...
        }
    }

> **Note:** This method only detects jobs that are dispatched via the `DispatchesJobs` trait's dispatch methods. It does not detect jobs that are sent directly to `Queue::push`.

> **주의:** 이 메소드는 `DispatchesJobs` 트레이트-trait의 dispatch 메소드를 통해 job 이 보내졌는지만을 감지합니다. `Queue::push`로 바로 보내진 job은 감지하지 못합니다. 

<a name="mocking-facades"></a>
### Mocking Facades 
### 파사드 Mocking

When testing, you may often want to mock a call to a Laravel [facade](/docs/{{version}}/facades). For example, consider the following controller action:

테스트할 때, 종종 라라벨의 [파사드](/docs/{{version}}/facades)의 mock에 호출하고 싶을 수도 있습니다. 예를 들어, 다음의 컨트롤러 액션을 생각해 보겠습니다.:

    <?php

    namespace App\Http\Controllers;

    use Cache;
    use Illuminate\Routing\Controller;

    class UserController extends Controller
    {
        /**
         * Show a list of all users of the application.
         *
         * @return Response
         */
        public function index()
        {
            $value = Cache::get('key');

            //
        }
    }

We can mock the call to the `Cache` facade by using the `shouldReceive` method, which will return an instance of a [Mockery](https://github.com/padraic/mockery) mock. Since facades are actually resolved and managed by the Laravel [service container](/docs/{{version}}/container), they have much more testability than a typical static class. For example, let's mock our call to the `Cache` facade:

`shouldReceive` 메소드를 사용하여 `Cache` 파사드 호출을 mock할 수 있고 [Mockery](https://github.com/padraic/mockery)의 mock 인스턴스를 반환할 것입니다. 파사드는 실제로는 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)에서 조회하고 관리하기 때문에 일반적인 정적 클래스보다 훨씬 테스트되기 쉽습니다. 예를 들어 `Cache` 에 mock 호출을 할 수 있습니다: 

    <?php

    class FooTest extends TestCase
    {
        public function testGetIndex()
        {
            Cache::shouldReceive('get')
                        ->once()
                        ->with('key')
                        ->andReturn('value');

            $this->visit('/users')->see('value');
        }
    }

> **Note:** You should not mock the `Request` facade. Instead, pass the input you desire into the HTTP helper methods such as `call` and `post` when running your test.

>**주의:** `Request` 파사드를 mock 해서는 안됩니다. 대신에 테스트를 수행할 때 `call` 과 `post`와 같은 HTTP 헬퍼 메소드에 원하는 request-입력을 전달하십시오. 
