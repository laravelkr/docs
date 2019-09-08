# HTTP 테스트

- [시작하기](#introduction)
    - [요청-Request 헤더 커스터마이징하기](#customizing-request-headers)
- [세션 / 인증](#session-and-authentication)
- [JSON API 테스팅](#testing-json-apis)
- [파일 업로드 테스트하기](#testing-file-uploads)
- [사용가능한 Assertions](#available-assertions)
    - [응답-Response Assertions](#response-assertions)
    - [인증 Assertions](#authentication-assertions)

<a name="introduction"></a>
## 시작하기

라라벨은 애플리케이션에 HTTP request-요청을 하고, 결과를 검사하는데 사용할 수 있는, 유연한 API를 제공합니다. 다음에 정의된 테스트 예제를 살펴보겠습니다:

    <?php

    namespace Tests\Feature;

    use Tests\TestCase;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;

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

`get` 메소드는 애플리케이션에 `GET` request-요청을 만들고, `assertStatus` 메소드는 반환된 response-응답이 주어진 HTTP 상태 코드와 일치하는지 확인합니다. 간단한 테스트에 더해, 라라벨은 response의 헤더값, 컨텐츠, JSON 구조 및 기타 확인을 할 수 있는 기능을 제공합니다.

<a name="customizing-request-headers"></a>
### 요청-Request 헤더 커스터마이징하기

`withHeaders` 메소드를 사용하여 요청-requestr가 애플리케이션에 전달되기 전에 헤더를 커스터마이징 할 수 있습니다. 이를 통해 요청-request에 커스텀 헤더를 추가 할 수 있습니다:

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

> {tip} 테스트가 실행되는 동안 CSRF 미들웨어는 자동으로 비활성화 됩니다.

<a name="session-and-authentication"></a>
## 세션 / 인증

라라벨은 HTTP 테스팅 중 세션 작업을 하는 데 필요한 여러 헬퍼 메소드를 제공합니다. 먼저, `withSession` 메소드를 이용하여 주어진 배열을 세션 데이터로 설정할 수 있습니다. 이것은 애플리케이션에 response-응답을 전달하기 전에 데이터를 세션에 로드하는 경우에 유용합니다:

    <?php

    class ExampleTest extends TestCase
    {
        public function testApplication()
        {
            $response = $this->withSession(['foo' => 'bar'])
                             ->get('/');
        }
    }

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

또한 `actingAs` 메소드의 두 번째 인자로 주어진 사용자에 대한 인증에 어떤 guard를 사용해야 하는지 지정하도록 guard 이름을 전달 할 수도 있습니다:

    $this->actingAs($user, 'api')

<a name="testing-json-apis"></a>
## JSON API 테스팅하기

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
                ->assertStatus(201)
                ->assertJson([
                    'created' => true,
                ]);
        }
    }

> {tip} `assertJson` 메소드는 response-응답을 배열로 변환하고 `PHPUnit::assertArraySubset`을 사용하여 애플리케이션에서 반환된 JSON 배열 안에 주어진 내용이 존재하는지 확인합니다. 따라서 JSON response-응답에 다른 속성이 있더라도, 주어진 내용이 존재하면 테스트는 통과합니다.

<a name="verifying-exact-match"></a>
### JSON이 정확하게 일치하는지 확인하기

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
                ->assertStatus(201)
                ->assertExactJson([
                    'created' => true,
                ]);
        }
    }

<a name="testing-file-uploads"></a>
## 파일 업로드 테스트하기

`Illuminate\Http\UploadedFile` 클래스는 테스트를 위해서 더미 파일 또는 이미지를 생성하는 `fake` 메소드를 제공합니다. 이는 `Storage` 파사드의 `fake` 메소드와 함께 파일 업로드 테스팅을 간단하게 해줍니다. 예를 들어, 이 두기능을 통해서 프로필 이미지 업로드 폼을 손쉽게 테스트 할 수 있습니다.

    <?php

    namespace Tests\Feature;

    use Tests\TestCase;
    use Illuminate\Http\UploadedFile;
    use Illuminate\Support\Facades\Storage;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;

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

#### Fake 파일 커스터마이징

`fake` 메소드를 사용하여 파일을 생성할 때, 유효성 검사 테스트를 위해서 파일의 가로, 세로 및 파일 사이즈를 지정할 수 있습니다:

    UploadedFile::fake()->image('avatar.jpg', $width, $height)->size(100);

이미지 타입 뿐만 아니라 `create` 메소드를 사용하여 다른 타입들의 파일도 생성할 수 있습니다:

    UploadedFile::fake()->create('document.pdf', $sizeInKilobytes);

<a name="available-assertions"></a>
## 사용 가능한 Assertions

<a name="response-assertions"></a>
### 응답-response Assertions

라라벨은 [PHPUnit](https://phpunit.de/) 테스트를 위해 다양한 커스텀 assertion 메소드를 제공합니다. 이러한 assertions 은 `json`, `get`, `post`, `put`, 그리고 `delete` 테스트 메소드에서 반환된 response-응답에 엑세스 할 수 있습니다:

- [assertCookie](#assert-cookie)
- [assertCookieExpired](#assert-cookie-expired)
- [assertCookieNotExpired](#assert-cookie-not-expired)
- [assertCookieMissing](#assert-cookie-missing)
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
- [assertSessionHasAll](#assert-session-has-all)
- [assertSessionHasErrors](#assert-session-has-errors)
- [assertSessionHasErrorsIn](#assert-session-has-errors-in)
- [assertSessionHasNoErrors](#assert-session-has-no-errors)
- [assertSessionMissing](#assert-session-missing)
- [assertStatus](#assert-status)
- [assertSuccessful](#assert-successful)
- [assertViewHas](#assert-view-has)
- [assertViewHasAll](#assert-view-has-all)
- [assertViewIs](#assert-view-is)
- [assertViewMissing](#assert-view-missing)

<a name="assert-cookie"></a>
#### assertCookie

response-응답에서 주어진 쿠키가 포함되어 있는지 확인:

    $response->assertCookie($cookieName, $value = null);

<a name="assert-cookie-expired"></a>
#### assertCookieExpired

response-응답에서 주어진 쿠키가 기간이 만료되었는지 확인:

    $response->assertCookieExpired($cookieName);

<a name="assert-cookie-not-expired"></a>
#### assertCookieNotExpired

response-응답에서 주어진 쿠키가 기간이 만료되지 않은 것을 확인:

    $response->assertCookieNotExpired($cookieName);

<a name="assert-cookie-missing"></a>
#### assertCookieMissing

response-응답에서 주어진 쿠키를 포함하고 있지 않은 것을 확인:

    $response->assertCookieMissing($cookieName);

<a name="assert-dont-see"></a>
#### assertDontSee

response-응답에 주어진 문자열이 포함되어 있지 않은 것을 확인:

    $response->assertDontSee($value);

<a name="assert-dont-see-text"></a>
#### assertDontSeeText

response-응답 텍스트에 주어진 문자열이 포함되어 있지 않은 것을 확인:

    $response->assertDontSeeText($value);

<a name="assert-exact-json"></a>
#### assertExactJson

response-응답에 주어진 JSON 데이터가 정확하게 일치하게 포함되어 있는지 확인:

    $response->assertExactJson(array $data);

<a name="assert-forbidden"></a>
#### assertForbidden

response-응답이 forbidden(403) 상태코드를 가지고 있는지 확인:

    $response->assertForbidden();

<a name="assert-header"></a>
#### assertHeader

response-응답에서 주어진 헤더가 존재하는지 확인:

    $response->assertHeader($headerName, $value = null);

<a name="assert-header-missing"></a>
#### assertHeaderMissing

response-응답에서 주어진 헤더가 존재하는 않는 것을 확인:

    $response->assertHeaderMissing($headerName);

<a name="assert-json"></a>
#### assertJson

response-응답에 주어진 JSON 데이터가 포함되어 있는지 확인:

    $response->assertJson(array $data);

<a name="assert-json-count"></a>
#### assertJsonCount

response-응답 JSON 에 주어진 키에 해당되는 아이템 숫자의 배열을 가지고 있는지 확인:

    $response->assertJsonCount($count, $key = null);

<a name="assert-json-fragment"></a>
#### assertJsonFragment

response-응답에 주어진 JSON 내용이 포함되어 있는지 확인:

    $response->assertJsonFragment(array $data);

<a name="assert-json-missing"></a>
#### assertJsonMissing

response-응답에 주어진 JSON 내용이 포함되어 있지 않은 것을 확인:

    $response->assertJsonMissing(array $data);

<a name="assert-json-missing-exact"></a>
#### assertJsonMissingExact

response-응답에 주어진 JSON 내용이 정확하게 포함되어 있지 않은 것을 확인:

    $response->assertJsonMissingExact(array $data);

<a name="assert-json-structure"></a>
#### assertJsonStructure

response-응답이 주어진 JOSN 구조를 가지고 있는지 확인:

    $response->assertJsonStructure(array $structure);

<a name="assert-json-validation-errors"></a>
#### assertJsonValidationErrors

response-응답에 주어진 키에 해당하는 JSON 유효성 에러를 가지고 있는지 확인:

    $response->assertJsonValidationErrors($keys);

<a name="assert-location"></a>
#### assertLocation

response-응답의 `Location` 헤더에 주어진 URI를 가지고 있는지 확인:

    $response->assertLocation($uri);

<a name="assert-not-found"></a>
#### assertNotFound

response-응답이 not found (404) 상태코드를 가지고 있는지 확인:

    $response->assertNotFound();

<a name="assert-ok"></a>
#### assertOk

response-응답이 200 상태 코드를 가지고 있는지 확인:

    $response->assertOk();

<a name="assert-plain-cookie"></a>
#### assertPlainCookie

response-응답에서 (암호화 되지 않은) 주어진 쿠키가 포함되어 있는지 확인:

    $response->assertPlainCookie($cookieName, $value = null);

<a name="assert-redirect"></a>
#### assertRedirect

response-응답이 주어진 URI로 리다이렉트되는지 여부를 확인:

    $response->assertRedirect($uri);

<a name="assert-see"></a>
#### assertSee

response-응답이 주어진 문자열을 포함하고 있는지 확인:

    $response->assertSee($value);

<a name="assert-see-in-order"></a>
#### assertSeeInOrder

response-응답이 주어진 문자열 배열을 순서대로 포함하고 있는지 확인:

    $response->assertSeeInOrder(array $values);

<a name="assert-see-text"></a>
#### assertSeeText

response-응답 텍스트가 주어진 문자열을 포함하고 있는지 확인:

    $response->assertSeeText($value);

<a name="assert-see-text-in-order"></a>
#### assertSeeTextInOrder

response-응답 텍스트가 주어진 문자열 배열을 순서대로 포함하고 있는지 확인:

    $response->assertSeeTextInOrder(array $values);

<a name="assert-session-has"></a>
#### assertSessionHas

세션에 주어진 데이터가 포함되어 있는지 확인:

    $response->assertSessionHas($key, $value = null);

<a name="assert-session-has-all"></a>
#### assertSessionHasAll

세션에 주어진 목록의 값들이 포함되어 있는지 확인:

    $response->assertSessionHasAll(array $data);

<a name="assert-session-has-errors"></a>
#### assertSessionHasErrors

세션에 주어진 필드에 대한 에러가 포함되어 있는지 확인:

    $response->assertSessionHasErrors(array $keys, $format = null, $errorBag = 'default');

<a name="assert-session-has-errors-in"></a>
#### assertSessionHasErrorsIn

세션이 주어진 에러를 가지고 있는지 확인:

    $response->assertSessionHasErrorsIn($errorBag, $keys = [], $format = null);

<a name="assert-session-has-no-errors"></a>
#### assertSessionHasNoErrors

세션에 에러가 없는지 확인:

    $response->assertSessionHasNoErrors();

<a name="assert-session-missing"></a>
#### assertSessionMissing

세션에 주어진 키가 포함되어 있지 않은 것을 확인:

    $response->assertSessionMissing($key);

<a name="assert-status"></a>
#### assertStatus

response-응답이 주어진 코드를 가지고 있는지 확인:

    $response->assertStatus($code);

<a name="assert-successful"></a>
#### assertSuccessful

response-응답이 성공적인 상태코드를 가지고 있는지 확인:

    $response->assertSuccessful();

<a name="assert-view-has"></a>
#### assertViewHas

response-응답 뷰가 주어진 데이터인지 확인:

    $response->assertViewHas($key, $value = null);

<a name="assert-view-has-all"></a>
#### assertViewHasAll

response-응답 뷰에서 주어진 데이터 리스트를 가지고 있는지 확인:

    $response->assertViewHasAll(array $data);

<a name="assert-view-is"></a>
#### assertViewIs

라우터에 의해서 주어진 뷰가 반환되었는지 확인:

    $response->assertViewIs($value);

<a name="assert-view-missing"></a>
#### assertViewMissing

response-응답 뷰가 주어진 데이터가 아닌것을 확인:

    $response->assertViewMissing($key);

<a name="authentication-assertions"></a>
### Authentication Assertions
### 인증 Assertions

라라벨은 또한 [PHPUnit](https://phpunit.de/) 테스트를 위해서 인증과 관련된 다양한 assertion 메소드를 제공합니다:

메소드  | 설명
------------- | -------------
`$this->assertAuthenticated($guard = null);`  |  사용자가 인증되었는지 확인.
`$this->assertGuest($guard = null);`  |  사용자가 인증되지 않은 것을 확인.
`$this->assertAuthenticatedAs($user, $guard = null);`  |  주어진 사용자가 인증되었는지 확인.
`$this->assertCredentials(array $credentials, $guard = null);`  |  주어진 인증정보가 유효한지 확인.
`$this->assertInvalidCredentials(array $credentials, $guard = null);`  |  주어진 인증정보가 유효하지 않은 것을 확인.
