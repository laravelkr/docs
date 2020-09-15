# HTTP Tests
# HTTP 테스트

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [Customizing Request Headers](#customizing-request-headers)
    - [요청-Request 헤더 커스터마이징하기](#customizing-request-headers)
    - [Cookies](#cookies)
    - [쿠키](#cookies)
    - [Debugging Responses](#debugging-responses)
    - [응답 디버깅](#debugging-responses)
- [Session / Authentication](#session-and-authentication)
- [세션 / 인증](#session-and-authentication)
- [Testing JSON APIs](#testing-json-apis)
- [JSON API 테스팅](#testing-json-apis)
- [Testing File Uploads](#testing-file-uploads)
- [파일 업로드 테스트하기](#testing-file-uploads)
- [Available Assertions](#available-assertions)
- [사용가능한 Assertions](#available-assertions)
    - [Response Assertions](#response-assertions)
    - [응답-Response Assertions](#response-assertions)
    - [Authentication Assertions](#authentication-assertions)
    - [인증 Assertions](#authentication-assertions)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel provides a very fluent API for making HTTP requests to your application and examining the output. For example, take a look at the feature test defined below:

라라벨은 애플리케이션에 HTTP request-요청을 하고, 결과를 검사하는데 사용할 수 있는, 유연한 API를 제공합니다. 다음에 정의된 피쳐 테스트 예제를 살펴보겠습니다.

    <?php

    namespace Tests\Feature;

    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
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
            $response = $this->get('/');

            $response->assertStatus(200);
        }
    }

The `get` method makes a `GET` request into the application, while the `assertStatus` method asserts that the returned response should have the given HTTP status code. In addition to this simple assertion, Laravel also contains a variety of assertions for inspecting the response headers, content, JSON structure, and more.

`get` 메소드는 애플리케이션에 `GET` request-요청을 만들고, `assertStatus` 메소드는 반환된 response-응답이 주어진 HTTP 상태 코드와 일치하는지 확인합니다. 간단한 테스트에 더해, 라라벨은 response의 헤더값, 컨텐츠, JSON 구조 및 기타 확인을 할 수 있는 기능을 제공합니다.

<a name="customizing-request-headers"></a>
### Customizing Request Headers
### 요청-Request 헤더 커스터마이징하기

You may use the `withHeaders` method to customize the request's headers before it is sent to the application. This allows you to add any custom headers you would like to the request:

`withHeaders` 메소드를 사용하여 요청-requestr가 애플리케이션에 전달되기 전에 헤더를 커스터마이징 할 수 있습니다. 이를 통해 요청-request에 커스텀 헤더를 추가 할 수 있습니다.

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
            $response = $this->withHeaders([
                'X-Header' => 'Value',
            ])->json('POST', '/user', ['name' => 'Sally']);

            $response
                ->assertStatus(201)
                ->assertJson([
                    'created' => true,
                ]);
        }
    }

> {tip} The CSRF middleware is automatically disabled when running tests.

> {tip} 테스트가 실행되는 동안 CSRF 미들웨어는 자동으로 비활성화 됩니다.

<a name="cookies"></a>
### Cookies
### 쿠키

You may use the `withCookie` or `withCookies` methods to set cookie values before making a request. The `withCookie` method accepts a cookie name and value as its two arguments, while the `withCookies` method accepts an array of name / value pairs:

`withCookie` 또는 `withCookies` 메소드를 사용하여 요청하기 전에 쿠키 값을 설정할 수 있습니다. `withCookie` 메소드는 쿠키 이름과 값을 두 개의 인수로 받아들이고`withCookies` 메소드는 이름 / 값 쌍의 배열을받습니다.

    <?php

    class ExampleTest extends TestCase
    {
        public function testCookies()
        {
            $response = $this->withCookie('color', 'blue')->get('/');

            $response = $this->withCookies([
                'color' => 'blue',
                'name' => 'Taylor',
            ])->get('/');
        }
    }

<a name="debugging-responses"></a>
### Debugging Responses
### 응답 디버깅

After making a test request to your application, the `dump`, `dumpHeaders`, and `dumpSession` methods may be used to examine and debug the response contents:

애플리케이션에 테스트 요청을 한 후에는 `dump`, `dumpHeaders` 및 `dumpSession` 메소드를 사용하여 응답 내용을 검사하고 디버그 할 수 있습니다.


    <?php

    namespace Tests\Feature;

    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
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
            $response = $this->get('/');

            $response->dumpHeaders();

            $response->dumpSession();

            $response->dump();
        }
    }

<a name="session-and-authentication"></a>
## Session / Authentication
## 세션 / 인증

Laravel provides several helpers for working with the session during HTTP testing. First, you may set the session data to a given array using the `withSession` method. This is useful for loading the session with data before issuing a request to your application:

라라벨은 HTTP 테스팅 중 세션 작업을 하는 데 필요한 여러 헬퍼 메소드를 제공합니다. 먼저, `withSession` 메소드를 이용하여 주어진 배열을 세션 데이터로 설정할 수 있습니다. 이것은 애플리케이션에 response-응답을 전달하기 전에 데이터를 세션에 로드하는 경우에 유용합니다.

    <?php

    class ExampleTest extends TestCase
    {
        public function testApplication()
        {
            $response = $this->withSession(['foo' => 'bar'])
                             ->get('/');
        }
    }

One common use of the session is for maintaining state for the authenticated user. The `actingAs` helper method provides a simple way to authenticate a given user as the current user. For example, we may use a [model factory](/docs/{{version}}/database-testing#writing-factories) to generate and authenticate a user:

일반적인 세션의 이용법 중 하나는 인증된 사용자를 위해서 상태를 유지하는 것입니다. `actingAs` 헬퍼 메소드는 특정 사용자를 현재 사용자로 인증하는 단순한 방법을 제공합니다. 예를 들어, 사용자를 생성하고 인증하기 위해 [model factory](/docs/{{version}}/database-testing#writing-factories)를 사용할 수 있습니다.

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

또한 `actingAs` 메소드의 두 번째 인자로 주어진 사용자에 대한 인증에 어떤 guard를 사용해야 하는지 지정하도록 guard 이름을 전달 할 수도 있습니다.

    $this->actingAs($user, 'api')

<a name="testing-json-apis"></a>
## Testing JSON APIs
## JSON API 테스팅하기

Laravel also provides several helpers for testing JSON APIs and their responses. For example, the `json`, `getJson`, `postJson`, `putJson`, `patchJson`, `deleteJson`, and `optionsJson` methods may be used to issue JSON requests with various HTTP verbs. You may also easily pass data and headers to these methods. To get started, let's write a test to make a `POST` request to `/user` and assert that the expected data was returned:

라라벨은 또한 JSON API와 그 결과를 테스트하기 위해 여러 헬퍼들을 제공합니다. 예를 들어, `json`, `getJson`, `postJson`, `putJson`, `patchJson`, `deleteJson`, 그리고 `optionsJson` 메소드들을 이용하여 다양한 HTTP verb를 가진 request-요청을 할 수 있습니다. 이 메소드들에 손쉽게 데이터와 헤더를 전달할 수도 있습니다. 이를 위해 `/user`에 `POST` request-요청을 하고 원하는 데이터가 반환되는지 확인하는 테스트를 작성해보겠습니다.

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
            $response = $this->postJson('/user', ['name' => 'Sally']);

            $response
                ->assertStatus(201)
                ->assertJson([
                    'created' => true,
                ]);
        }
    }

> {tip} The `assertJson` method converts the response to an array and utilizes `PHPUnit::assertArraySubset` to verify that the given array exists within the JSON response returned by the application. So, if there are other properties in the JSON response, this test will still pass as long as the given fragment is present.

> {tip} `assertJson` 메소드는 response-응답을 배열로 변환하고 `PHPUnit::assertArraySubset`을 사용하여 애플리케이션에서 반환된 JSON 배열 안에 주어진 내용이 존재하는지 확인합니다. 따라서 JSON response-응답에 다른 속성이 있더라도, 주어진 내용이 존재하면 테스트는 통과합니다.

In addition, JSON response data may be accessed as array variables on the response:

덧붙여 말하자면, JSON 응답 데이터는 응답(response)에서 배열의 변수들을 통해 접근이 가능합니다.

    $this->assertTrue($response['created']);

<a name="verifying-exact-match"></a>
### Verifying An Exact JSON Match
### JSON이 정확하게 일치하는지 확인하기

If you would like to verify that the given array is an **exact** match for the JSON returned by the application, you should use the `assertExactJson` method:

주어진 배열이 반환된 JSON과 **정확히** 일치하는지 확인하고자 한다면, `assertExactJson` 메소드를 사용하면 됩니다.

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
                ->assertStatus(201)
                ->assertExactJson([
                    'created' => true,
                ]);
        }
    }

<a name="verifying-json-paths"></a>
### Verifying JSON Paths
###  JSON Path 검증하기

If you would like to verify that the JSON response contains some given data at a specified path, you should use the `assertJsonPath` method:

JSON 응답에 지정된 경로에 지정된 데이터가 포함되어 있는지 확인하려면 `assertJsonPath` 메소드를 사용해야합니다.

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
                ->assertStatus(201)
                ->assertJsonPath('team.owner.name', 'foo')
        }
    }

<a name="testing-file-uploads"></a>
## Testing File Uploads
## 파일 업로드 테스트하기

The `Illuminate\Http\UploadedFile` class provides a `fake` method which may be used to generate dummy files or images for testing. This, combined with the `Storage` facade's `fake` method greatly simplifies the testing of file uploads. For example, you may combine these two features to easily test an avatar upload form:

`Illuminate\Http\UploadedFile` 클래스는 테스트를 위해서 더미 파일 또는 이미지를 생성하는 `fake` 메소드를 제공합니다. 이는 `Storage` 파사드의 `fake` 메소드와 함께 파일 업로드 테스팅을 간단하게 해줍니다. 예를 들어, 이 두기능을 통해서 프로필 이미지 업로드 폼을 손쉽게 테스트 할 수 있습니다.

    <?php

    namespace Tests\Feature;

    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Http\UploadedFile;
    use Illuminate\Support\Facades\Storage;
    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        public function testAvatarUpload()
        {
            Storage::fake('avatars');

            $file = UploadedFile::fake()->image('avatar.jpg');

            $response = $this->json('POST', '/avatar', [
                'avatar' => $file,
            ]);

            // Assert the file was stored...
            Storage::disk('avatars')->assertExists($file->hashName());

            // Assert a file does not exist...
            Storage::disk('avatars')->assertMissing('missing.jpg');
        }
    }

#### Fake File Customization
#### Fake 파일 커스터마이징

When creating files using the `fake` method, you may specify the width, height, and size of the image in order to better test your validation rules:

`fake` 메소드를 사용하여 파일을 생성할 때, 유효성 검사 테스트를 위해서 파일의 가로, 세로 및 파일 사이즈를 지정할 수 있습니다.

    UploadedFile::fake()->image('avatar.jpg', $width, $height)->size(100);

In addition to creating images, you may create files of any other type using the `create` method:

이미지 타입 뿐만 아니라 `create` 메소드를 사용하여 다른 타입들의 파일도 생성할 수 있습니다.

    UploadedFile::fake()->create('document.pdf', $sizeInKilobytes);

If needed, you may pass a `$mimeType` argument to the method to explicitly define the MIME type that should be returned by the file:

필요한 경우, 파일에 의해 반환되어야하는 MIME 유형을 명시적으로 정의하기 위해 메소드에 `$mimeType` 인수를 전달할 수 있습니다.

    UploadedFile::fake()->create('document.pdf', $sizeInKilobytes, 'application/pdf');

<a name="available-assertions"></a>
## Available Assertions
## 사용 가능한 Assertions

<a name="response-assertions"></a>
### Response Assertions
### 응답-response Assertions

Laravel provides a variety of custom assertion methods for your [PHPUnit](https://phpunit.de/) feature tests. These assertions may be accessed on the response that is returned from the `json`, `get`, `post`, `put`, and `delete` test methods:

라라벨은 [PHPUnit](https://phpunit.de/) 피쳐 테스트를 위해 다양한 커스텀 assertion 메소드를 제공합니다. 이러한 assertions 은 `json`, `get`, `post`, `put`, 그리고 `delete` 테스트 메소드에서 반환된 response-응답에 엑세스 할 수 있습니다.



- [assertCookie](#assert-cookie)
- [assertCookieExpired](#assert-cookie-expired)
- [assertCookieNotExpired](#assert-cookie-not-expired)
- [assertCookieMissing](#assert-cookie-missing)
- [assertCreated](#assert-created)
- [assertDontSee](#assert-dont-see)
- [assertDontSeeText](#assert-dont-see-text)
- [assertExactJson](#assert-exact-json)
- [assertForbidden](#assert-forbidden)
- [assertHeader](#assert-header)
- [assertHeaderMissing](#assert-header-missing)
- [assertJson](#assert-json)
- [assertJsonCount](#assert-json-count)
- [assertJsonFragment](#assert-json-fragment)
- [assertJsonMissing](#assert-json-missing)
- [assertJsonMissingExact](#assert-json-missing-exact)
- [assertJsonMissingValidationErrors](#assert-json-missing-validation-errors)
- [assertJsonPath](#assert-json-path)
- [assertJsonStructure](#assert-json-structure)
- [assertJsonValidationErrors](#assert-json-validation-errors)
- [assertLocation](#assert-location)
- [assertNotFound](#assert-not-found)
- [assertOk](#assert-ok)
- [assertPlainCookie](#assert-plain-cookie)
- [assertRedirect](#assert-redirect)
- [assertSee](#assert-see)
- [assertSeeInOrder](#assert-see-in-order)
- [assertSeeText](#assert-see-text)
- [assertSeeTextInOrder](#assert-see-text-in-order)
- [assertSessionHas](#assert-session-has)
- [assertSessionHasInput](#assert-session-has-input)
- [assertSessionHasAll](#assert-session-has-all)
- [assertSessionHasErrors](#assert-session-has-errors)
- [assertSessionHasErrorsIn](#assert-session-has-errors-in)
- [assertSessionHasNoErrors](#assert-session-has-no-errors)
- [assertSessionDoesntHaveErrors](#assert-session-doesnt-have-errors)
- [assertSessionMissing](#assert-session-missing)
- [assertStatus](#assert-status)
- [assertSuccessful](#assert-successful)
- [assertUnauthorized](#assert-unauthorized)
- [assertViewHas](#assert-view-has)
- [assertViewHasAll](#assert-view-has-all)
- [assertViewIs](#assert-view-is)
- [assertViewMissing](#assert-view-missing)



<a name="assert-cookie"></a>
#### assertCookie

Assert that the response contains the given cookie:

response-응답에서 주어진 쿠키가 포함되어 있는지 확인:

    $response->assertCookie($cookieName, $value = null);

<a name="assert-cookie-expired"></a>
#### assertCookieExpired

Assert that the response contains the given cookie and it is expired:

response-응답에서 주어진 쿠키가 기간이 만료되었는지 확인:

    $response->assertCookieExpired($cookieName);

<a name="assert-cookie-not-expired"></a>
#### assertCookieNotExpired

Assert that the response contains the given cookie and it is not expired:

response-응답에서 주어진 쿠키가 기간이 만료되지 않은 것을 확인:

    $response->assertCookieNotExpired($cookieName);

<a name="assert-cookie-missing"></a>
#### assertCookieMissing

Assert that the response does not contains the given cookie:

response-응답에서 주어진 쿠키를 포함하고 있지 않은 것을 확인:

    $response->assertCookieMissing($cookieName);

<a name="assert-created"></a>
#### assertCreated

Assert that the response has a 201 status code:

response-응답에  201 상태 코드가 있는 지 확인:

    $response->assertCreated();

<a name="assert-dont-see"></a>
#### assertDontSee

Assert that the given string is not contained within the response. This assertion will automatically escape the given string unless you pass a second argument of `false`:

response-응답에 주어진 문자열이 포함되어 있지 않은 것을 확인. 이 테스트는 두번째 인자값으로 `false`를 지정하지 않는 한, 자동으로 주어진 문자열을 노출시킵니다.

    $response->assertDontSee($value, $escaped = true);

<a name="assert-dont-see-text"></a>
#### assertDontSeeText

Assert that the given string is not contained within the response text. This assertion will automatically escape the given string unless you pass a second argument of `false`:

response-응답 텍스트에 주어진 문자열이 포함되어 있지 않은 것을 확인. 이 테스트는 두번째 인자값으로 `false`를 지정하지 않는 한, 자동으로 주어진 문자열을 노출시킵니다.

    $response->assertDontSeeText($value, $escaped = true);

<a name="assert-exact-json"></a>
#### assertExactJson

Assert that the response contains an exact match of the given JSON data:

response-응답에 주어진 JSON 데이터가 정확하게 일치하게 포함되어 있는지 확인:

    $response->assertExactJson(array $data);

<a name="assert-forbidden"></a>
#### assertForbidden

Assert that the response has a forbidden (403) status code:

response-응답이 forbidden(403) 상태코드를 가지고 있는지 확인:

    $response->assertForbidden();

<a name="assert-header"></a>
#### assertHeader

Assert that the given header is present on the response:

response-응답에서 주어진 헤더가 존재하는지 확인:

    $response->assertHeader($headerName, $value = null);

<a name="assert-header-missing"></a>
#### assertHeaderMissing

Assert that the given header is not present on the response:

response-응답에서 주어진 헤더가 존재하는 않는 것을 확인:

    $response->assertHeaderMissing($headerName);

<a name="assert-json"></a>
#### assertJson

Assert that the response contains the given JSON data:

response-응답에 주어진 JSON 데이터가 포함되어 있는지 확인:

    $response->assertJson(array $data, $strict = false);

<a name="assert-json-count"></a>
#### assertJsonCount

Assert that the response JSON has an array with the expected number of items at the given key:

response-응답 JSON 에 주어진 키에 해당되는 아이템 숫자의 배열을 가지고 있는지 확인:

    $response->assertJsonCount($count, $key = null);

<a name="assert-json-fragment"></a>
#### assertJsonFragment

Assert that the response contains the given JSON fragment:

response-응답에 주어진 JSON 내용이 포함되어 있는지 확인:

    $response->assertJsonFragment(array $data);

<a name="assert-json-missing"></a>
#### assertJsonMissing

Assert that the response does not contain the given JSON fragment:

response-응답에 주어진 JSON 내용이 포함되어 있지 않은 것을 확인:

    $response->assertJsonMissing(array $data);

<a name="assert-json-missing-exact"></a>
#### assertJsonMissingExact

Assert that the response does not contain the exact JSON fragment:

response-응답에 주어진 JSON 내용이 정확하게 포함되어 있지 않은 것을 확인:

    $response->assertJsonMissingExact(array $data);

<a name="assert-json-missing-validation-errors"></a>
#### assertJsonMissingValidationErrors

Assert that the response has no JSON validation errors for the given keys:

response-응답에 주어진키에 대한 JSON 유효성 검사 에러가 포함되어 있지 않은 것을 확인:

    $response->assertJsonMissingValidationErrors($keys);

<a name="assert-json-structure"></a>
#### assertJsonStructure

Assert that the response has a given JSON structure:

response-응답이 주어진 JOSN 구조를 가지고 있는지 확인:

    $response->assertJsonStructure(array $structure);

<a name="assert-json-path"></a>
#### assertJsonPath

Assert that the response contains the given data at the specified path:

response-응답에 지정된 경로와 지정된 데이터가 포함되어 있는지 확인:

    $response->assertJsonPath($path, array $data, $strict = false);

<a name="assert-json-structure"></a>
#### assertJsonStructure

Assert that the response has a given JSON structure:

response-응답에 지정된 JSON 구조가 있는지 확인:

    $response->assertJsonStructure(array $structure);

<a name="assert-json-validation-errors"></a>
#### assertJsonValidationErrors

Assert that the response has the given JSON validation errors:

response-응답이 JSON 유효성 에러를 가지고 있는지 확인:

    $response->assertJsonValidationErrors(array $data);

<a name="assert-location"></a>
#### assertLocation

Assert that the response has the given URI value in the `Location` header:

response-응답의 `Location` 헤더에 주어진 URI를 가지고 있는지 확인:

    $response->assertLocation($uri);

<a name="assert-no-content"></a>
#### assertNoContent

Assert that the response has the given status code and no content.

response-응답에 주어진 상태 코드가 있고 내용이 없는지 확인:

    $response->assertNoContent($status = 204);

<a name="assert-not-found"></a>
#### assertNotFound

Assert that the response has a not found status code:

response-응답이 not found (404) 상태코드를 가지고 있는지 확인:

    $response->assertNotFound();

<a name="assert-ok"></a>
#### assertOk

Assert that the response has a 200 status code:

response-응답이 200 상태 코드를 가지고 있는지 확인:

    $response->assertOk();

<a name="assert-plain-cookie"></a>
#### assertPlainCookie

Assert that the response contains the given cookie (unencrypted):

response-응답에서 (암호화 되지 않은) 주어진 쿠키가 포함되어 있는지 확인:

    $response->assertPlainCookie($cookieName, $value = null);

<a name="assert-redirect"></a>
#### assertRedirect

Assert that the response is a redirect to a given URI:

response-응답이 주어진 URI로 리다이렉트되는지 여부를 확인:

    $response->assertRedirect($uri);

<a name="assert-see"></a>
#### assertSee

Assert that the given string is contained within the response. This assertion will automatically escape the given string unless you pass a second argument of `false`:

response-응답이 주어진 문자열을 포함하고 있는지 확인. 이 테스트는 두번째 인자값으로 `false`를 지정하지 않는 한, 자동으로 주어진 문자열을 노출시킵니다.

    $response->assertSee($value, $escaped = true);

<a name="assert-see-in-order"></a>
#### assertSeeInOrder

Assert that the given strings are contained in order within the response. This assertion will automatically escape the given strings unless you pass a second argument of `false`:

response-응답이 주어진 문자열 배열을 순서대로 포함하고 있는지 확인. 이 테스트는 두번째 인자값으로 `false`를 지정하지 않는 한, 자동으로 주어진 문자열들을 노출시킵니다.

    $response->assertSeeInOrder(array $values, $escaped = true);

<a name="assert-see-text"></a>
#### assertSeeText

Assert that the given string is contained within the response text. This assertion will automatically escape the given string unless you pass a second argument of `false`:

response-응답 텍스트가 주어진 문자열을 포함하고 있는지 확인. 이 테스트는 두번째 인자값으로 `false`를 지정하지 않는 한, 자동으로 주어진 문자열을 노출시킵니다.

    $response->assertSeeText($value, $escaped = true);

<a name="assert-see-text-in-order"></a>
#### assertSeeTextInOrder

Assert that the given strings are contained in order within the response text. This assertion will automatically escape the given strings unless you pass a second argument of `false`:

response-응답 텍스트가 주어진 문자열 배열을 순서대로 포함하고 있는지 확인. 이 테스트는 두번째 인자값으로 `false`를 지정하지 않는 한, 자동으로 주어진 문자열들을 노출시킵니다.

    $response->assertSeeTextInOrder(array $values, $escaped = true);

<a name="assert-session-has"></a>
#### assertSessionHas

Assert that the session contains the given piece of data:

세션에 주어진 데이터가 포함되어 있는지 확인:

    $response->assertSessionHas($key, $value = null);

<a name="assert-session-has-input"></a>
#### assertSessionHasInput

Assert that the session has a given value in the flashed input array:

세션에 임시저장(flashed) 된 입력 배열에 주어진 값이 있는지 확인:

    $response->assertSessionHasInput($key, $value = null);

<a name="assert-session-has-all"></a>
#### assertSessionHasAll

Assert that the session has a given list of values:

세션에 주어진 목록의 값들이 포함되어 있는지 확인:

    $response->assertSessionHasAll(array $data);

<a name="assert-session-has-errors"></a>
#### assertSessionHasErrors

Assert that the session contains an error for the given `$keys`. If `$keys` is an associative array, assert that the session contains a specific error message (value) for each field (key):

세션에 주어진 `$keys`에 대한 에러가 포함되어 있는지 확인. `$keys`가 연관 배열 인 경우 세션에 각 필드 (키)에 대한 특정 오류 메시지 (값)가 포함되어 있는지 확인:

    $response->assertSessionHasErrors(array $keys, $format = null, $errorBag = 'default');

<a name="assert-session-has-errors-in"></a>
#### assertSessionHasErrorsIn

Assert that the session contains an error for the given `$keys`, within a specific error bag. If `$keys` is an associative array, assert that the session contains a specific error message (value) for each field (key), within the error bag:

세션에 특정 error bag 내에서 주어진 `$keys`에 대한 오류가 포함되어 있는지 확인. `$keys`가 연관 배열 인 경우, 세션에 error bag 내의 각 필드 (키)에 대한 특정 오류 메시지 (값)가 포함되어 있는지 확인:

    $response->assertSessionHasErrorsIn($errorBag, $keys = [], $format = null);

<a name="assert-session-has-no-errors"></a>
#### assertSessionHasNoErrors

Assert that the session has no errors:

세션에 에러가 없는지 확인:

    $response->assertSessionHasNoErrors();

<a name="assert-session-doesnt-have-errors"></a>
#### assertSessionDoesntHaveErrors

Assert that the session has no errors for the given keys:

세션에 주어진 키에 대해 오류가 없는 지 확인.

    $response->assertSessionDoesntHaveErrors($keys = [], $format = null, $errorBag = 'default');

<a name="assert-session-missing"></a>
#### assertSessionMissing

Assert that the session does not contain the given key:

세션에 주어진 키가 포함되어 있지 않은 것을 확인:

    $response->assertSessionMissing($key);

<a name="assert-status"></a>
#### assertStatus

Assert that the response has a given code:

response-응답이 주어진 코드를 가지고 있는지 확인:

    $response->assertStatus($code);

<a name="assert-successful"></a>
#### assertSuccessful

Assert that the response has a successful (>= 200 and < 300) status code:

response-응답이 성공적인 상태코드(>= 200 and < 300)를 가지고 있는지 확인:

    $response->assertSuccessful();

<a name="assert-unauthorized"></a>
#### assertUnauthorized

Assert that the response has an unauthorized (401) status code:

response-응답이 unauthorized 상태코드(401)를 가지고 있는지 확인:

    $response->assertUnauthorized();

<a name="assert-view-has"></a>
#### assertViewHas

Assert that the response view was given a piece of data:

response-응답 뷰가 주어진 데이터인지 확인:

    $response->assertViewHas($key, $value = null);

In addition, view data may be accessed as array variables on the response:

덧붙여 말하자면, 뷰 데이터는 응답(response)에서 배열의 변수들을 통해 접근이 가능합니다.

    $this->assertEquals('Taylor', $response['name']);

<a name="assert-view-has-all"></a>
#### assertViewHasAll

Assert that the response view has a given list of data:

response-응답 뷰에서 주어진 데이터 리스트를 가지고 있는지 확인:

    $response->assertViewHasAll(array $data);

<a name="assert-view-is"></a>
#### assertViewIs

Assert that the given view was returned by the route:

라우터에 의해서 주어진 뷰가 반환되었는지 확인:

    $response->assertViewIs($value);

<a name="assert-view-missing"></a>
#### assertViewMissing

Assert that the response view is missing a piece of bound data:

response-응답 뷰가 주어진 데이터가 아닌것을 확인:

    $response->assertViewMissing($key);

<a name="authentication-assertions"></a>
### Authentication Assertions
### 인증 Assertions

Laravel also provides a variety of authentication related assertions for your [PHPUnit](https://phpunit.de/) feature tests:

라라벨은 또한 [PHPUnit](https://phpunit.de/) 피쳐 테스트를 위해서 인증과 관련된 다양한 assertion 메소드를 제공합니다.

Method  | Description
------------- | -------------
`$this->assertAuthenticated($guard = null);`  |  Assert that the user is authenticated.
`$this->assertGuest($guard = null);`  |  Assert that the user is not authenticated.
`$this->assertAuthenticatedAs($user, $guard = null);`  |  Assert that the given user is authenticated.
`$this->assertCredentials(array $credentials, $guard = null);`  |  Assert that the given credentials are valid.
`$this->assertInvalidCredentials(array $credentials, $guard = null);`  |  Assert that the given credentials are invalid.

메소드  | 설명
------------- | -------------
`$this->assertAuthenticated($guard = null);`  |  사용자가 인증되었는지 확인.
`$this->assertGuest($guard = null);`  |  사용자가 인증되지 않은 것을 확인.
`$this->assertAuthenticatedAs($user, $guard = null);`  |  주어진 사용자가 인증되었는지 확인.
`$this->assertCredentials(array $credentials, $guard = null);`  |  주어진 인증정보가 유효한지 확인.
`$this->assertInvalidCredentials(array $credentials, $guard = null);`  |  주어진 인증정보가 유효하지 않은 것을 확인.
