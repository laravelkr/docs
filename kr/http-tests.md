# HTTP Tests
# HTTP 테스트

- [Introduction](#introduction)
- [소개](#introduction)
- [Session / Authentication](#session-and-authentication)
- [세션 / 인증](#session-and-authentication)
- [Testing JSON APIs](#testing-json-apis)
- [JSON API 테스팅](#testing-json-apis)
- [Testing File Uploads](#testing-file-uploads)
- [파일 업로드 테스트하기](#testing-file-uploads)
- [Available Assertions](#available-assertions)
- [사용가능한 Assertions](#available-assertions)

<a name="introduction"></a>
## Introduction
## 소개

Laravel provides a very fluent API for making HTTP requests to your application and examining the output. For example, take a look at the test defined below:

라라벨은 어플리케이션에 HTTP request-요청을 하고, 결과를 검사하는데 사용할 수 있는, 유연한 API를 제공합니다. 다음에 정의된 테스트 예제를 살펴보겠습니다: 

    <?php

    namespace Tests\Feature;

    use Tests\TestCase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Foundation\Testing\DatabaseMigrations;
    use Illuminate\Foundation\Testing\DatabaseTransactions;

    class ExampleTest extends TestCase
    {
        /**
         * A basic test example.
         *
         * @return void
         */
        public function testBasicTest()
        {
            $response = $this->get('/');

            $response->assertStatus(200);
        }
    }

The `get` method makes a `GET` request into the application, while the `assertStatus` method asserts that the returned response should have the given HTTP status code. In addition to this simple assertion, Laravel also contains a variety of assertions for inspecting the response headers, content, JSON structure, and more.

`get` 메소드는 어플리케이션에 `GET` request-요청을 만들고, `assertStatus` 메소드는 반환된 response-응답이 주어진 HTTP 상태 코드와 일치하는지 확인합니다. 간단한 테스트에 더해, 라라벨은 response의 헤더값, 컨텐츠, JSON 구조 및 기타 확인을 할 수 있는 기능을 제공합니다.  

<a name="session-and-authentication"></a>
## Session / Authentication
## 세션 / 인증

Laravel provides several helpers for working with the session during HTTP testing. First, you may set the session data to a given array using the `withSession` method. This is useful for loading the session with data before issuing a request to your application:

라라벨은 HTTP 테스팅 중 세션 작업을 하는 데 필요한 여러 헬퍼 메소드를 제공합니다. 먼저, `withSession` 메소드를 이용하여 주어진 배열을 세션 데이터로 설정할 수 있습니다. 이것은 어플리케이션에 response-응답을 전달하기 전에 데이터를 세션에 로드하는 경우에 유용합니다:

    <?php

    class ExampleTest extends TestCase
    {
        public function testApplication()
        {
            $response = $this->withSession(['foo' => 'bar'])
                             ->get('/');
        }
    }

Of course, one common use of the session is for maintaining state for the authenticated user. The `actingAs` helper method provides a simple way to authenticate a given user as the current user. For example, we may use a [model factory](/docs/{{version}}/database-testing#writing-factories) to generate and authenticate a user:

물론 일반적인 세션의 이용법 중 하나는 인증된 사용자를 위해서 상태를 유지하는 것입니다. `actingAs` 헬퍼 메소드는 특정 사용자를 현재 사용자로 인증하는 단순한 방법을 제공합니다. 예를 들어, 사용자를 생성하고 인증하기 위해 [model factory](/docs/{{version}}/database-testing#writing-factories)를 사용할 수 있습니다: 

    <?php

    use App\User;

    class ExampleTest extends TestCase
    {
        public function testApplication()
        {
            $user = factory(User::class)->create();

            $response = $this->actingAs($user)
                             ->withSession(['foo' => 'bar'])
                             ->get('/');
        }
    }

You may also specify which guard should be used to authenticate the given user by passing the guard name as the second argument to the `actingAs` method:

또한 `actingAs` 메소드의 두 번째 인자로 주어진 사용자에 대한 인증에 어떤 guard를 사용해야 하는지 지정하도록 guard 이름을 전달 할 수도 있습니다:

    $this->actingAs($user, 'api')

<a name="testing-json-apis"></a>
## Testing JSON APIs
## JSON API 테스팅하기

Laravel also provides several helpers for testing JSON APIs and their responses. For example, the `json`, `get`, `post`, `put`, `patch`, and `delete` methods may be used to issue requests with various HTTP verbs. You may also easily pass data and headers to these methods. To get started, let's write a test to make a `POST` request to `/user` and assert that the expected data was returned:

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
            $response = $this->json('POST', '/user', ['name' => 'Sally']);

            $response
                ->assertStatus(200)
                ->assertJson([
                    'created' => true,
                ]);
        }
    }

> {tip} The `assertJson` method converts the response to an array and utilizes `PHPUnit::assertArraySubset` to verify that the given array exists within the JSON response returned by the application. So, if there are other properties in the JSON response, this test will still pass as long as the given fragment is present.

> {tip} `assertJson` 메소드는 response-응답을 배열로 변환하고 `PHPUnit::assertArraySubset`을 사용하여 어플리케이션에서 반환된 JSON 배열 안에 주어진 내용이 존재하는지 확인합니다. 따라서 JSON response-응답에 다른 속성이 있더라도, 주어진 내용이 존재하면 테스트는 통과합니다.

<a name="verifying-exact-match"></a>
### Verifying Exact Match
### 정확하게 일치하는지 확인하기

If you would like to verify that the given array is an **exact** match for the JSON returned by the application, you should use the `assertExactJson` method:

주어진 배열이 반환된 JSON과 **정확히** 일치하는지 확인하고자 한다면, `assertExactJson` 메소드를 사용하면 됩니다: 

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
            $response = $this->json('POST', '/user', ['name' => 'Sally']);

            $response
                ->assertStatus(200)
                ->assertExactJson([
                    'created' => true,
                ]);
        }
    }

<a name="testing-file-uploads"></a>
## Testing File Uploads
## 파일 업로드 테스트하기

The `Illuminate\Http\UploadedFile` class provides a `fake` method which may be used to generate dummy files or images for testing. This, combined with the `Storage` facade's `fake` method greatly simplifies the testing of file uploads. For example, you may combine these two features to easily test an avatar upload form:

`Illuminate\Http\UploadedFile` 클래스는 테스트를 위해서 더미 파일 또는 이미지를 생성하는 `fake` 메소드를 제공합니다. 이는 `Storage` 파사드의 `fake` 메소드와 함께 파일 업로드 테스팅을 간단하게 해줍니다. 예를 들어, 이 두기능을 통해서 프로필 이미지 업로드 폼을 손쉽게 테스트 할 수 있습니다. 

    <?php

    namespace Tests\Feature;

    use Tests\TestCase;
    use Illuminate\Http\UploadedFile;
    use Illuminate\Support\Facades\Storage;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Foundation\Testing\DatabaseMigrations;
    use Illuminate\Foundation\Testing\DatabaseTransactions;

    class ExampleTest extends TestCase
    {
        public function testAvatarUpload()
        {
            Storage::fake('avatars');

            $response = $this->json('POST', '/avatar', [
                'avatar' => UploadedFile::fake()->image('avatar.jpg')
            ]);

            // Assert the file was stored...
            Storage::disk('avatars')->assertExists('avatar.jpg');

            // Assert a file does not exist...
            Storage::disk('avatars')->assertMissing('missing.jpg');
        }
    }

#### Fake File Customization
#### Fake 파일 커스터마이징

When creating files using the `fake` method, you may specify the width, height, and size of the image in order to better test your validation rules:

`fake` 메소드를 사용하여 파일을 생성할 때, 유효성 검사 테스트를 위해서 파일의 가로, 세로 및 파일 사이즈를 지정할 수 있습니다:

    UploadedFile::fake()->image('avatar.jpg', $width, $height)->size(100);

In addition to creating images, you may create files of any other type using the `create` method:

이미지 타입 뿐만 아니라 `create` 메소드를 사용하여 다른 타입들의 파일도 생성할 수 있습니다: 

    UploadedFile::fake()->create('document.pdf', $sizeInKilobytes);

<a name="available-assertions"></a>
## Available Assertions
## 사용 가능한 Assertions

Laravel provides a variety of custom assertion methods for your [PHPUnit](https://phpunit.de/) tests. These assertions may be accessed on the response that is returned from the `json`, `get`, `post`, `put`, and `delete` test methods:

라라벨은 [PHPUnit](https://phpunit.de/) 테스트를 위해 다양한 커스텀 assertion 메소드를 제공합니다. 이러한 assertions 은 `json`, `get`, `post`, `put`, 그리고 `delete` 테스트 메소드에서 반환된 response-응답에 엑세스 할 수 있습니다: 

Method  | Description
------------- | -------------
`$response->assertStatus($code);`  |  Assert that the response has a given code.
`$response->assertRedirect($uri);`  |  Assert that the response is a redirect to a given URI.
`$response->assertHeader($headerName, $value = null);`  |  Assert that the given header is present on the response.
`$response->assertCookie($cookieName, $value = null);`  |  Assert that the response contains the given cookie.
`$response->assertPlainCookie($cookieName, $value = null);`  |  Assert that the response contains the given cookie (unencrypted).
`$response->assertSessionHas($key, $value = null);`  |  Assert that the session contains the given piece of data.
`$response->assertSessionHasErrors(array $keys);`  |  Assert that the session contains an error for the given field.
`$response->assertSessionMissing($key);`  |  Assert that the session does not contain the given key.
`$response->assertJson(array $data);`  |  Assert that the response contains the given JSON data.
`$response->assertJsonFragment(array $data);`  |  Assert that the response contains the given JSON fragment.
`$response->assertJsonMissing(array $data);`  |  Assert that the response does not contain the given JSON fragment.
`$response->assertExactJson(array $data);`  |  Assert that the response contains an exact match of the given JSON data.
`$response->assertJsonStructure(array $structure);`  |  Assert that the response has a given JSON structure.
`$response->assertViewHas($key, $value = null);`  |  Assert that the response view was given a piece of data.

메소드  | 설명
------------- | -------------
`$response->assertStatus($code);`  |  응답이 주어진 코드를 가지고 있는지 확인.
`$response->assertRedirect($uri);`  |  응답이 주어진 URI로 리다이렉트되었는지 여부를 확인.
`$response->assertHeader($headerName, $value = null);`  |  응답에서 주어진 헤더가 존재하는지 확인.
`$response->assertCookie($cookieName, $value = null);`  |  응답에서 주어진 쿠키가 포함되어 있는지 확인.
`$response->assertPlainCookie($cookieName, $value = null);`  |  응답에서 주어진 (암호화 되지 않은)쿠키가 포함되어 있는지 확인.
`$response->assertSessionHas($key, $value = null);`  |  세션에 주어진 데이터가 포함되어 있는지 확인.
`$response->assertSessionHasErrors(array $keys);`  |  세션에 주어진 필드에 대한 에러가 포함되어 있는지 확인.
`$response->assertSessionMissing($key);`  |  세션에 주어진 키가 포함되어 있지 않은 것을 확인.
`$response->assertJson(array $data);`  |  응답에 주어진 JSON 데이터가 포함되어 있는지 확인.
`$response->assertJsonFragment(array $data);`  |  응답에 주어진 JSON 내용이 포함되어 있는지 확인
`$response->assertJsonMissing(array $data);`  |  응답에 주어진 JSON 내용이 포함되어 있지 않은 것을 확인.
`$response->assertExactJson(array $data);`  |  응답에 주어진 JSON 데이터와 정확하게 일치하게 포함되어 있는지 확인.
`$response->assertJsonStructure(array $structure);`  |  응답이 주어진 JOSN 구조를 가지고 있는지 확인.
`$response->assertViewHas($key, $value = null);`  |  응답 뷰가 주어진 데이터인지 확인.
