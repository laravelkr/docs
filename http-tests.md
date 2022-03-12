# HTTP 테스트

- [시작하기](#introduction)
- [Request 만들기](#making-requests)
    - [요청-Request 헤더 커스터마이징하기](#customizing-request-headers)
    - [쿠키](#cookies)
    - [세션 / 인증](#session-and-authentication)
    - [응답 디버깅](#debugging-responses)
    - [에러 핸들링](#exception-handling)
- [JSON API 테스팅](#testing-json-apis)
    - [유연한 JSON 테스트](#fluent-json-testing)
- [파일 업로드 테스트하기](#testing-file-uploads)
- [뷰 테스트하기](#testing-views)
    - [Rendering Blade & Components](#rendering-blade-and-components)
- [사용가능한 Assertions](#available-assertions)
    - [응답-Response Assertions](#response-assertions)
    - [인증 Assertions](#authentication-assertions)

<a name="introduction"></a>
## 시작하기

라라벨은 애플리케이션에 HTTP 요청-request을 하고, 응답-response을 검사하는데 사용할 수 있는, 유연한 API를 제공합니다. 다음에 정의된 기능-feature 테스트 예제를 살펴보겠습니다.

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
        public function test_a_basic_request()
        {
            $response = $this->get('/');

            $response->assertStatus(200);
        }
    }

`get` 메소드는 애플리케이션에 `GET` request-요청을 만들고, `assertStatus` 메소드는 반환된 response-응답이 주어진 HTTP 상태 코드와 일치하는지 확인합니다. 간단한 테스트에 더해, 라라벨은 response의 헤더값, 컨텐츠, JSON 구조 및 기타 확인을 할 수 있는 기능을 제공합니다.

<a name="making-requests"></a>
## Request 만들기

애플리케이션에 요청하려면 테스트 내에서 `'get',` `post`, `put`, `patch` 또는 `delete` 메서드를 호출할 수 있습니다. 이러한 메소드는 실제로 애플리케이션에 "실제" HTTP 요청을 발행시키지는 않습니다. 대신 전체 네트워크 요청이 내부적으로 시뮬레이션됩니다.

테스트 요청 메소드는 `Illuminate\Http\Response` 인스턴스를 반환하는 대신, 애플리케이션 응답-response에 [다양하고 유용한 검증](#available-assertions)을 제공하는 `Illuminate\Testing\TestResponse` 인스턴스를 반환합니다.

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
        public function test_a_basic_request()
        {
            $response = $this->get('/');

            $response->assertStatus(200);
        }
    }

일반적으로 각 테스트는 애플리케이션에 한 번만 요청해야 합니다. 단일 테스트 메서드 내에서 여러 요청이 실행되는 경우 예기치 않은 동작이 발생할 수 있습니다.

> {tip} 편의를 위해 CSRF 미들웨어는 테스트를 실행할 때 자동으로 비활성화됩니다.

<a name="customizing-request-headers"></a>
### 요청-Request 헤더 커스터마이징하기

`withHeaders` 메소드를 사용하여 요청-requestr가 애플리케이션에 전달되기 전에 헤더를 커스터마이징 할 수 있습니다. 이 메서드를 통해 요청-request에 커스텀 헤더를 추가 할 수 있습니다.

    <?php

    namespace Tests\Feature;

    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        /**
         * A basic functional test example.
         *
         * @return void
         */
        public function test_interacting_with_headers()
        {
            $response = $this->withHeaders([
                'X-Header' => 'Value',
            ])->post('/user', ['name' => 'Sally']);

            $response->assertStatus(201);
        }
    }

<a name="cookies"></a>
### 쿠키

`withCookie` 또는 `withCookies` 메소드를 사용하여 요청하기 전에 쿠키 값을 설정할 수 있습니다. `withCookie` 메소드는 쿠키 이름과 값을 두 개의 인수로 받아들이고`withCookies` 메소드는 이름 / 값 쌍의 배열을받습니다.

    <?php

    namespace Tests\Feature;

    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        public function test_interacting_with_cookies()
        {
            $response = $this->withCookie('color', 'blue')->get('/');

            $response = $this->withCookies([
                'color' => 'blue',
                'name' => 'Taylor',
            ])->get('/');
        }
    }

<a name="session-and-authentication"></a>
### 세션 / 인증

라라벨은 HTTP 테스트 중에 세션과 상호 작용하기 위한 여러 헬퍼를 제공합니다. 먼저 `withSession` 메소드를 사용하여 세션 데이터를 주어진 배열로 설정할 수 있습니다. 이것은 애플리케이션에 요청을 보내기 전에 데이터가 있는 세션을 로드하는 데 유용합니다.

    <?php

    namespace Tests\Feature;

    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        public function test_interacting_with_the_session()
        {
            $response = $this->withSession(['banned' => false])->get('/');
        }
    }

라라벨의 세션은 일반적으로 현재 인증된 사용자의 상태를 유지하는 데 사용됩니다. 따라서 `actingAs` 헬퍼 메서드는 주어진 사용자를 현재 사용자로 인증하는 간단한 방법을 제공합니다. 예를 들어 [모델 팩토리](/docs/{{version}}/database-testing#writing-factories)를 사용하여 사용자를 생성하고 인증할 수 있습니다.

    <?php

    namespace Tests\Feature;

    use App\Models\User;
    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        public function test_an_action_that_requires_authentication()
        {
            $user = User::factory()->create();

            $response = $this->actingAs($user)
                             ->withSession(['banned' => false])
                             ->get('/');
        }
    }

가드 이름을 `actingAs` 메소드의 두 번째 인수로 전달하여, 주어진 사용자를 인증하는 데 사용할 가드를 지정할 수도 있습니다.

    $this->actingAs($user, 'web')

<a name="debugging-responses"></a>
### 응답 디버깅

애플리케이션에 테스트 요청을 한 후에는 `dump`, `dumpHeaders` 및 `dumpSession` 메소드를 사용하여 응답 내용을 검사하고 디버그 할 수 있습니다.

    <?php

    namespace Tests\Feature;

    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        /**
         * A basic test example.
         *
         * @return void
         */
        public function test_basic_test()
        {
            $response = $this->get('/');

            $response->dumpHeaders();

            $response->dumpSession();

            $response->dump();
        }
    }

또는 `dd`, `ddHeaders` 및 `ddSession` 메서드를 사용하여 응답에 대한 정보를 덤프한 다음 실행을 중지할 수 있습니다.

    <?php

    namespace Tests\Feature;

    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        /**
         * A basic test example.
         *
         * @return void
         */
        public function test_basic_test()
        {
            $response = $this->get('/');

            $response->ddHeaders();

            $response->ddSession();

            $response->dd();
        }
    }

<a name="exception-handling"></a>
### 에러 핸들링

때로는 애플리케이션에서 특정 예외가 발생하는지 테스트하고 싶을 수 있습니다. 예외가 라라벨의 예외 핸들러에 의해 포착되어 HTTP 응답으로 반환되지 않도록 하려면 요청하기 전에 `withoutExceptionHandling` 메소드를 호출할 수 있습니다.

    $response = $this->withoutExceptionHandling()->get('/');

또한 애플리케이션이 PHP 언어 또는 애플리케이션에서 사용 중인 라이브러리에서 더 이상 사용되지 않는 기능을 사용하지 않도록 하려면 요청하기 전에 `withoutDeprecationHandling` 메서드를 호출할 수 있습니다. 지원 중단 처리가 비활성화되면 지원 중단 경고가 예외로 변환되어 테스트가 실패하게 됩니다.

    $response = $this->withoutDeprecationHandling()->get('/');

<a name="testing-json-apis"></a>
## JSON API 테스팅하기

라라벨은 또한 JSON API 및 해당 응답을 테스트하기 위한 여러 헬퍼를 제공합니다. 예를 들어 `json`, `getJson`, `postJson`, `putJson`, `patchJson`, `deleteJson` 및 `optionsJson` 메소드는 다양한 HTTP 동사로 JSON 요청을 발행하는 데 사용할 수 있습니다. 또한 이러한 메서드에 데이터와 헤더를 쉽게 전달할 수도 있습니다. 시작하기 위해 `/api/user`에 대한 `POST` 요청을 작성하고 예상 JSON 데이터가 반환되었는지 확인하는 테스트를 작성해 보겠습니다.

    <?php

    namespace Tests\Feature;

    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        /**
         * A basic functional test example.
         *
         * @return void
         */
        public function test_making_an_api_request()
        {
            $response = $this->postJson('/api/user', ['name' => 'Sally']);

            $response
                ->assertStatus(201)
                ->assertJson([
                    'created' => true,
                ]);
        }
    }

또한 JSON 응답 데이터는 응답의 배열 변수로 액세스할 수 있으므로 JSON 응답 내에서 반환된 개별 값을 편리하게 검사할 수 있습니다.

    $this->assertTrue($response['created']);

> {tip} `assertJson` 메소드는 응답을 배열로 변환하고 `PHPUnit::assertArraySubset`을 사용하여 애플리케이션에서 반환된 JSON 응답 내에 주어진 배열이 존재하는지 확인합니다. 따라서 JSON 응답에 다른 속성이 있는 경우, 이 테스트는 주어진 조각이 있는 한 계속 통과합니다.

<a name="verifying-exact-match"></a>
#### 정확하게 JSON 일치 검증

앞서 언급했듯이 `assertJson` 메서드는 JSON 응답 내에 JSON 조각이 존재한다고 검증하는 데 사용할 수 있습니다. 주어진 배열이 애플리케이션에서 반환된 JSON과 **정확히 일치하는지** 확인하려면 `assertExactJson` 메서드를 사용해야 합니다.

    <?php

    namespace Tests\Feature;

    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        /**
         * A basic functional test example.
         *
         * @return void
         */
        public function test_asserting_an_exact_json_match()
        {
            $response = $this->postJson('/user', ['name' => 'Sally']);

            $response
                ->assertStatus(201)
                ->assertExactJson([
                    'created' => true,
                ]);
        }
    }

<a name="verifying-json-paths"></a>
#### JSON Path 검증

JSON 응답에 지정된 경로에 지정된 데이터가 포함되어 있는지 확인하려면 `assertJsonPath` 메소드를 사용해야합니다.

    <?php

    namespace Tests\Feature;

    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        /**
         * A basic functional test example.
         *
         * @return void
         */
        public function test_asserting_a_json_paths_value()
        {
            $response = $this->postJson('/user', ['name' => 'Sally']);

            $response
                ->assertStatus(201)
                ->assertJsonPath('team.owner.name', 'Darian');
        }
    }

<a name="fluent-json-testing"></a>
### 유연한 JSON 테스트

라라벨은 또한 애플리케이션의 JSON 응답을 유연하게 테스트하는 아름다운 방법을 제공합니다. 시작하려면 `assertJson` 메서드에 클로저를 전달하세요. 이 클로저는 `Illuminate\Testing\Fluent\AssertableJson` 인스턴스와 함께 호출되며 애플리케이션에서 반환된 JSON에 대해 검증을 만드는 데 사용할 수 있습니다. `where` 메소드는 JSON의 특정 속성에 대한 검증을 만드는 데 사용할 수 있는 반면, `missing` 메소드는 JSON에서 특정 속성이 누락되었음을 검증하는 데 사용할 수 있습니다.

    use Illuminate\Testing\Fluent\AssertableJson;

    /**
     * A basic functional test example.
     *
     * @return void
     */
    public function test_fluent_json()
    {
        $response = $this->getJson('/users/1');

        $response
            ->assertJson(fn (AssertableJson $json) =>
                $json->where('id', 1)
                     ->where('name', 'Victoria Faith')
                     ->missing('password')
                     ->etc()
            );
    }

#### `etc` 메소드 이해하기

위의 예에서 검증 체인의 끝에서 `etc` 메서드를 호출한 것을 보셨을 것입니다. 이 메소드는 JSON 객체에 다른 속성이 있을 수 있음을 라라벨에 알려줍니다. `etc` 메서드를 사용하지 않는 경우 검증을 수행하지 않은 다른 속성이 JSON 개체에 있으면 테스트가 실패합니다.

이 동작 뒤에 숨겨진 의도는 속성에 대해 명시적으로 검증을 만들거나 `etc` 메서드를 통해 추가 속성을 명시적으로 입력하도록 하여 JSON 응답에서 민감한 정보가 의도치 않게 노출되는 것을 방지하는 것입니다.

<a name="asserting-json-attribute-presence-and-absence"></a>
#### 속성 존재 / 부재 검증

속성이 있는지 여부를 확인하려면 `has` 및 `missing` 메서드를 사용할 수 있습니다.

    $response->assertJson(fn (AssertableJson $json) =>
        $json->has('data')
             ->missing('message')
    );

또한 `hasAll` 및 `missingAll` 메서드를 사용하면 여러 속성의 존재 여부를 동시에 확인할 수 있습니다.

    $response->assertJson(fn (AssertableJson $json) =>
        $json->hasAll('status', 'data')
             ->missingAll('message', 'code')
    );

`hasAny` 메소드를 사용하여 주어진 속성 목록 중 적어도 하나가 존재하는지 확인할 수 있습니다.

    $response->assertJson(fn (AssertableJson $json) =>
        $json->has('status')
             ->hasAny('data', 'message', 'code')
    );

<a name="asserting-against-json-collections"></a>
#### JSON 컬렉션에 대한 검증

때로는 여러명의 사용자와 같이, 여러개의 항목을 가진 JSON 응답을 반환합니다.

    Route::get('/users', function () {
        return User::all();
    });

이러한 상황에서 우리는 유창한 JSON 객체의 `has` 메소드를 사용하여 응답에 포함된 사용자에 대해 검증을 할 수 있습니다. 예를 들어 JSON 응답에 세 명의 사용자가 포함되어 있다고 가정해 보겠습니다. 다음으로 `first` 메서드를 사용하여 컬렉션의 첫 번째 사용자에 대한 몇 가지 검증을 할 것입니다. `first` 메소드는 JSON 컬렉션의 첫 번째 객체에 대한 검증을 만드는 데 사용할 수 있는 또 다른 검증 가능한 JSON 문자열을 수신하는 클로저를 허용합니다.

    $response
        ->assertJson(fn (AssertableJson $json) =>
            $json->has(3)
                 ->first(fn ($json) =>
                    $json->where('id', 1)
                         ->where('name', 'Victoria Faith')
                         ->missing('password')
                         ->etc()
                 )
        );

<a name="scoping-json-collection-assertions"></a>
#### JSON 컬렉션 어설션 범위 지정

때때로 애플리케이션의 경로는 명명된 키가 할당된 JSON 컬렉션을 반환합니다.

    Route::get('/users', function () {
        return [
            'meta' => [...],
            'users' => User::all(),
        ];
    })

이러한 경로를 테스트할 때 컬렉션의 항목 수에 대해 검증하기 위해 `has` 메서드를 사용할 수 있습니다. 또한 `has` 메서드를 사용하여 일련의 검증 범위를 지정할 수 있습니다.

    $response
        ->assertJson(fn (AssertableJson $json) =>
            $json->has('meta')
                 ->has('users', 3)
                 ->has('users.0', fn ($json) =>
                    $json->where('id', 1)
                         ->where('name', 'Victoria Faith')
                         ->missing('password')
                         ->etc()
                 )
        );

그러나 `users` 컬렉션에 대해 검증하기 위해 `has` 메소드에 대한 두 개의 개별 호출을 수행하는 대신, 세 번째 매개변수로 클로저를 제공하는 단일 호출을 수행할 수 있습니다. 그렇게 하면 클로저가 자동으로 호출되고 컬렉션의 첫 번째 항목으로 범위가 지정됩니다.

    $response
        ->assertJson(fn (AssertableJson $json) =>
            $json->has('meta')
                 ->has('users', 3, fn ($json) =>
                    $json->where('id', 1)
                         ->where('name', 'Victoria Faith')
                         ->missing('password')
                         ->etc()
                 )
        );

<a name="asserting-json-types"></a>
#### JSON Type 검증

JSON 응답의 속성이 특정 유형인지만 검증 할 수도 있습니다. `Illuminate\Testing\Fluent\AssertableJson` 클래스는 이를 수행하기 위한 `whereType` 및 `whereAllType` 메서드를 제공합니다.

    $response->assertJson(fn (AssertableJson $json) =>
        $json->whereType('id', 'integer')
             ->whereAllType([
                'users.0.name' => 'string',
                'meta' => 'array'
            ])
    );

`|` 문자를 사용하여 여러 유형을 지정하거나 `whereType` 메소드의 두 번째 매개변수로 유형 배열을 전달할 수 있습니다. 응답 값이 나열된 유형 중 하나이면 검증이 성공합니다.

    $response->assertJson(fn (AssertableJson $json) =>
        $json->whereType('name', 'string|null')
             ->whereType('id', ['string', 'integer'])
    );

`whereType` 및 `whereAllType` 메소드는 `string`, `integer`, `double`, `boolean`, `array` 및 `null` 유형을 인식합니다.

<a name="testing-file-uploads"></a>
## 파일 업로드 테스트하기

`Illuminate\Http\UploadedFile` 클래스는 테스트를 위해 더미 파일이나 이미지를 생성하는 데 사용할 수 있는 `fake` 메서드를 제공합니다. 이것은 `Storage` 파사드의 `fake` 방법과 결합되어 파일 업로드 테스트를 크게 단순화합니다. 예를 들어 다음 두 기능을 결합하여 아바타 업로드 양식을 쉽게 테스트할 수 있습니다.

    <?php

    namespace Tests\Feature;

    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Http\UploadedFile;
    use Illuminate\Support\Facades\Storage;
    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        public function test_avatars_can_be_uploaded()
        {
            Storage::fake('avatars');

            $file = UploadedFile::fake()->image('avatar.jpg');

            $response = $this->post('/avatar', [
                'avatar' => $file,
            ]);

            Storage::disk('avatars')->assertExists($file->hashName());
        }
    }

주어진 파일이 존재하지 않는다고 검증하고 싶다면 `Storage` 파사드에서 제공하는 `assertMissing` 메소드를 사용할 수 있습니다.

    Storage::fake('avatars');

    // ...

    Storage::disk('avatars')->assertMissing('missing.jpg');

<a name="fake-file-customization"></a>
#### Fake 파일 커스터마이징

`UploadedFile` 클래스에서 제공하는 `fake` 메소드를 사용하여 파일을 생성할 때, 애플리케이션의 유효성 검사 규칙을 더 잘 테스트하기 위해 이미지의 너비, 높이 및 크기(KB)를 지정할 수 있습니다.

    UploadedFile::fake()->image('avatar.jpg', $width, $height)->size(100);

이미지 타입 뿐만 아니라 `create` 메소드를 사용하여 다른 타입들의 파일도 생성할 수 있습니다.

    UploadedFile::fake()->create('document.pdf', $sizeInKilobytes);

필요한 경우, 파일에 의해 반환되어야하는 MIME 유형을 명시적으로 정의하기 위해 메소드에 `$mimeType` 인수를 전달할 수 있습니다.

    UploadedFile::fake()->create(
        'document.pdf', $sizeInKilobytes, 'application/pdf'
    );

<a name="testing-views"></a>
## 뷰 테스트하기

라라벨을 사용하면 애플리케이션에 시뮬레이션된 HTTP 요청을 하지 않고도 뷰를 렌더링할 수 있습니다. 이를 수행하기 위해 테스트 내에서 `view` 메소드를 호출할 수 있습니다. `view` 메소드는 뷰 이름과 데이터의 선택적인 배열을 입력받습니다. 이 메서드는 뷰의 내용에 대해 편리하게 검증할 수 있는 여러 메서드를 제공하는 `Illuminate\Testing\TestView`의 인스턴스를 반환합니다.

    <?php

    namespace Tests\Feature;

    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        public function test_a_welcome_view_can_be_rendered()
        {
            $view = $this->view('welcome', ['name' => 'Taylor']);

            $view->assertSee('Taylor');
        }
    }

`TestView` 클래스는 `assertSee`, `assertSeeInOrder`, `assertSeeText`, `assertSeeTextInOrder`, `assertDontSee` 및 `assertDontSeeText`와 같은 검증 메소드를 제공합니다.

필요하다면 `TestView` 인스턴스를 문자열로 캐스팅하여 렌더링 된 뷰의 원문을 가져올 수 있습니다.

    $contents = (string) $this->view('welcome');

<a name="sharing-errors"></a>
#### 에러 공유하기

일부 보기는 [라라벨이 제공하는 전역 오류 bag](/docs/{{version}}/validation#quick-displaying-the-validation-errors)에서 공유되는 오류에 따라 달라질 수 있습니다. 오류 메시지가 있는 오류 백을 가져오려면 `withViewErrors` 메서드를 사용할 수 있습니다.

    $view = $this->withViewErrors([
        'name' => ['Please provide a valid name.']
    ])->view('form');

    $view->assertSee('Please provide a valid name.');

<a name="rendering-blade-and-components"></a>
### 블레이드 및 컴포넌트 렌더링

필요한 경우 `blade` 메서드를 사용하여 원시 [Blade](/docs/{{version}}/blade) 문자열을 평가하고 렌더링할 수 있습니다. `view` 메소드와 마찬가지로 `blade` 메소드는 `Illuminate\Testing\TestView`의 인스턴스를 반환합니다.

    $view = $this->blade(
        '<x-component :name="$name" />',
        ['name' => 'Taylor']
    );

    $view->assertSee('Taylor');

`component` 메소드를 사용하여 [Blade component](/docs/{{version}}/blade#components)를 평가하고 렌더링할 수 있습니다. `view` 메소드와 마찬가지로 `component` 메소드는 `Illuminate\Testing\TestView`의 인스턴스를 반환합니다.

    $view = $this->component(Profile::class, ['name' => 'Taylor']);

    $view->assertSee('Taylor');

<a name="available-assertions"></a>
## 사용 가능한 Assertions

<a name="response-assertions"></a>
### 응답-response Assertions

라라벨의 `Illuminate\Testing\TestResponse` 클래스는 애플리케이션을 테스트할 때 활용할 수 있는 다양한 커스텀 검증 메소드를 제공합니다. 이러한 검증은 `json`, `get`, `post`, `put` 및 `delete` 테스트 메서드에서 반환된 응답에서 액세스할 수 있습니다.

- [assertCookie](#assert-cookie)
- [assertCookieExpired](#assert-cookie-expired)
- [assertCookieNotExpired](#assert-cookie-not-expired)
- [assertCookieMissing](#assert-cookie-missing)
- [assertCreated](#assert-created)
- [assertDontSee](#assert-dont-see)
- [assertDontSeeText](#assert-dont-see-text)
- [assertDownload](#assert-download)
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
- [assertJsonValidationErrorFor](#assert-json-validation-error-for)
- [assertLocation](#assert-location)
- [assertNoContent](#assert-no-content)
- [assertNotFound](#assert-not-found)
- [assertOk](#assert-ok)
- [assertPlainCookie](#assert-plain-cookie)
- [assertRedirect](#assert-redirect)
- [assertRedirectContains](#assert-redirect-contains)
- [assertRedirectToSignedRoute](#assert-redirect-to-signed-route)
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
- [assertSimilarJson](#assert-similar-json)
- [assertStatus](#assert-status)
- [assertSuccessful](#assert-successful)
- [assertUnauthorized](#assert-unauthorized)
- [assertUnprocessable](#assert-unprocessable)
- [assertValid](#assert-valid)
- [assertInvalid](#assert-invalid)
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

<a name="assert-created"></a>
#### assertCreated

response-응답에 201 HTTP 상태 코드가 있는 지 확인:

    $response->assertCreated();

<a name="assert-dont-see"></a>
#### assertDontSee

애플리케이션에서 돌아온 response-응답에 주어진 문자열이 포함되어 있지 않은 것을 확인. 이 테스트는 두번째 인자값으로 `false`를 지정하지 않는 한, 자동으로 주어진 문자열을 노출시킵니다.

    $response->assertDontSee($value, $escaped = true);

<a name="assert-dont-see-text"></a>
#### assertDontSeeText

response-응답 텍스트에 주어진 문자열이 포함되어 있지 않은 것을 확인. 이 테스트는 두번째 인자값으로 `false`를 지정하지 않는 한, 자동으로 주어진 문자열을 노출시킵니다. 이 메소드는 검증을 하기 전에 `strip_tags` PHP 함수에 응답 내용을 전달할 것입니다.

    $response->assertDontSeeText($value, $escaped = true);

<a name="assert-download"></a>
#### assertDownload

응답이 "다운로드"임을 확인합니다. 일반적으로 이는 응답을 반환한 호출된 경로가 `Response::download` 응답, `BinaryFileResponse` 또는 `Storage::download` 응답을 반환했음을 의미합니다.

    $response->assertDownload();

원하는 경우 다운로드 가능한 파일에 지정한 파일 이름이 할당되었는지 검증할 수 있습니다.

    $response->assertDownload('image.jpg');

<a name="assert-exact-json"></a>
#### assertExactJson

response-응답에 주어진 JSON 데이터가 정확하게 일치하게 포함되어 있는지 확인:

    $response->assertExactJson(array $data);

<a name="assert-forbidden"></a>
#### assertForbidden

response-응답이 forbidden(403) HTTP 상태코드를 가지고 있는지 확인:

    $response->assertForbidden();

<a name="assert-header"></a>
#### assertHeader

response-응답에서 주어진 헤더와 값이 존재하는지 확인:

    $response->assertHeader($headerName, $value = null);

<a name="assert-header-missing"></a>
#### assertHeaderMissing

response-응답에서 주어진 헤더가 존재하는 않는 것을 확인:

    $response->assertHeaderMissing($headerName);

<a name="assert-json"></a>
#### assertJson

response-응답에 주어진 JSON 데이터가 포함되어 있는지 확인:

    $response->assertJson(array $data, $strict = false);

`assertJson` 메소드는 응답을 배열로 변환하고 `PHPUnit::assertArraySubset`을 사용하여 애플리케이션에서 반환된 JSON 응답 내에 주어진 배열이 존재하는지 확인합니다. 따라서 JSON 응답에 다른 속성이 있는 경우 이 테스트는 주어진 조각이 있는 한 계속 통과합니다.

<a name="assert-json-count"></a>
#### assertJsonCount

response-응답 JSON 에 주어진 키에 해당되는 아이템 숫자의 배열을 가지고 있는지 확인:

    $response->assertJsonCount($count, $key = null);

<a name="assert-json-fragment"></a>
#### assertJsonFragment

응답에 지정된 JSON 데이터가 응답의 어딘가에 포함되어 있는지 확인:

    Route::get('/users', function () {
        return [
            'users' => [
                [
                    'name' => 'Taylor Otwell',
                ],
            ],
        ];
    });

    $response->assertJsonFragment(['name' => 'Taylor Otwell']);

<a name="assert-json-missing"></a>
#### assertJsonMissing

response-응답에 주어진 JSON 데이터가 포함되어 있지 않은 것을 확인:

    $response->assertJsonMissing(array $data);

<a name="assert-json-missing-exact"></a>
#### assertJsonMissingExact

response-응답에 주어진 JSON 데이터가 정확하게 포함되어 있지 않은 것을 확인:

    $response->assertJsonMissingExact(array $data);

<a name="assert-json-missing-validation-errors"></a>
#### assertJsonMissingValidationErrors

response-응답에 주어진키에 대한 JSON 유효성 검사 에러가 포함되어 있지 않은 것을 확인:

    $response->assertJsonMissingValidationErrors($keys);

> {tip} 보다 일반적인 [assertValid](#assert-valid) 메서드를 사용하여 응답에 JSON **으로 반환된 유효성 검사 오류가 없고** 세션 저장소에 오류가 표시되지 않았다고 검증할 수 있습니다.

<a name="assert-json-path"></a>
#### assertJsonPath

response-응답에 지정된 경로와 지정된 데이터가 포함되어 있는지 확인:

    $response->assertJsonPath($path, $expectedValue);

예를 들어 애플리케이션에서 반환된 JSON 응답에 다음 데이터가 포함된 경우

```js
{
    "user": {
        "name": "Steve Schoger"
    }
}
```

`user` 객체의 `name` 속성이 다음과 같이 주어진 값과 일치한다고 검증할 수 있습니다.

    $response->assertJsonPath('user.name', 'Steve Schoger');

<a name="assert-json-structure"></a>
#### assertJsonStructure

response-응답에 지정된 JSON 구조가 있는지 확인:

    $response->assertJsonStructure(array $structure);

예를 들어 애플리케이션에서 반환된 JSON 응답에 다음 데이터가 포함된 경우:

```js
{
    "user": {
        "name": "Steve Schoger"
    }
}
```

다음과 같이 JSON 구조가 예상과 일치한다고 검증할 수 있습니다.

    $response->assertJsonStructure([
        'user' => [
            'name',
        ]
    ]);

경우에 따라 애플리케이션에서 반환된 JSON 응답에 객체 배열이 포함될 수 있습니다.

```js
{
    "user": [
        {
            "name": "Steve Schoger",
            "age": 55,
            "location": "Earth"
        },
        {
            "name": "Mary Schoger",
            "age": 60,
            "location": "Earth"
        }
    ]
}
```

이 상황에서 `*` 문자를 사용하여 배열에 있는 모든 객체의 구조에 대해 검증할 수 있습니다.

    $response->assertJsonStructure([
        'user' => [
            '*' => [
                 'name',
                 'age',
                 'location'
            ]
        ]
    ]);

<a name="assert-json-validation-errors"></a>
#### assertJsonValidationErrors

응답에 지정된 키에 대해 지정된 JSON 유효성 검사 오류가 있는지 확인합니다. 이 메서드는 유효성 검사 오류가 세션에 플래시되는 대신 JSON 구조로 반환되는 응답에 대해 검증할 때 사용해야 합니다.

    $response->assertJsonValidationErrors(array $data, $responseKey = 'errors');

> {tip} 보다 일반적인 [assertInvalid](#assert-invalid) 메서드를 사용하여 응답에 유효성 검사 오류가 JSON으로 반환된 **또는** 오류가 세션 저장소로 플래시되었음을 확인할 수 있습니다.

<a name="assert-json-validation-error-for"></a>
#### assertJsonValidationErrorFor

응답에 지정된 키에 대한 JSON 유효성 검사 오류가 있는지 검증:

    $response->assertJsonValidationErrorFor(string $key, $responseKey = 'errors');

<a name="assert-location"></a>
#### assertLocation

response-응답의 `Location` 헤더에 주어진 URI를 가지고 있는지 확인:

    $response->assertLocation($uri);

<a name="assert-no-content"></a>
#### assertNoContent

response-응답에 주어진 HTTP 상태 코드가 있고 내용이 없는지 확인:

    $response->assertNoContent($status = 204);

<a name="assert-not-found"></a>
#### assertNotFound

response-응답이 not found (404) HTTP 상태코드를 가지고 있는지 확인:

    $response->assertNotFound();

<a name="assert-ok"></a>
#### assertOk

response-응답이 200 HTTP 상태 코드를 가지고 있는지 확인:

    $response->assertOk();

<a name="assert-plain-cookie"></a>
#### assertPlainCookie

response-응답에서 암호화 되지 않은 주어진 쿠키가 포함되어 있는지 확인:

    $response->assertPlainCookie($cookieName, $value = null);

<a name="assert-redirect"></a>
#### assertRedirect

response-응답이 주어진 URI로 리다이렉트되는지 여부를 확인:

    $response->assertRedirect($uri);

<a name="assert-redirect-contains"></a>
#### assertRedirectContains

응답이 주어진 문자열을 포함하는 URI로 리디렉션되는지 여부를 확인:

    $response->assertRedirectContains($string);

<a name="assert-redirect-to-signed-route"></a>
#### assertRedirectToSignedRoute

응답이 지정된 서명된 경로로의 리디렉션임을 확인:

    $response->assertRedirectToSignedRoute($name = null, $parameters = []);

<a name="assert-see"></a>
#### assertSee

response-응답이 주어진 문자열을 포함하고 있는지 확인. 이 테스트는 두번째 인자값으로 `false`를 지정하지 않는 한, 자동으로 주어진 문자열을 노출시킵니다.

    $response->assertSee($value, $escaped = true);

<a name="assert-see-in-order"></a>
#### assertSeeInOrder

response-응답이 주어진 문자열 배열을 순서대로 포함하고 있는지 확인. 이 테스트는 두번째 인자값으로 `false`를 지정하지 않는 한, 자동으로 주어진 문자열들을 노출시킵니다.

    $response->assertSeeInOrder(array $values, $escaped = true);

<a name="assert-see-text"></a>
#### assertSeeText

response-응답 텍스트가 주어진 문자열을 포함하고 있는지 확인. 이 테스트는 두번째 인자값으로 `false`를 지정하지 않는 한, 자동으로 주어진 문자열을 노출시킵니다. 응답 내용은 검증이 이루어지기 전에 `strip_tags` PHP 함수로 전달됩니다.

    $response->assertSeeText($value, $escaped = true);

<a name="assert-see-text-in-order"></a>
#### assertSeeTextInOrder

response-응답 텍스트가 주어진 문자열 배열을 순서대로 포함하고 있는지 확인. 이 테스트는 두번째 인자값으로 `false`를 지정하지 않는 한, 자동으로 주어진 문자열들을 노출시킵니다. 응답 내용은 검증이 이루어지기 전에 `strip_tags` PHP 함수로 전달됩니다.

    $response->assertSeeTextInOrder(array $values, $escaped = true);

<a name="assert-session-has"></a>
#### assertSessionHas

세션에 주어진 데이터가 포함되어 있는지 확인:

    $response->assertSessionHas($key, $value = null);

필요한 경우 클로저를 `assertSessionHas` 메소드에 대한 두 번째 인수로 전달할 수 있습니다. 클로저가 `true`를 반환하면 검증이 전달됩니다.

    $response->assertSessionHas($key, function ($value) {
        return $value->name === 'Taylor Otwell';
    });

<a name="assert-session-has-input"></a>
#### assertSessionHasInput

세션에 [임시저장(flashed) 된 입력 배열](/docs/{{version}}/responses#redirecting-with-flashed-session-data)에 주어진 값이 있는지 확인:

    $response->assertSessionHasInput($key, $value = null);

필요한 경우 클로저를 `assertSessionHasInput` 메소드에 대한 두 번째 인수로 제공할 수 있습니다. 클로저가 `true`를 반환하면 검증이 전달됩니다.

    $response->assertSessionHasInput($key, function ($value) {
        return Crypt::decryptString($value) === 'secret';
    });

<a name="assert-session-has-all"></a>
#### assertSessionHasAll

세션에 키/값 쌍의 주어진 배열이 포함되어 있는지 확인:

    $response->assertSessionHasAll(array $data);

예를 들어, 애플리케이션의 세션에 `name` 및 `status` 키가 포함되어 있으면 둘 다 존재하고 다음과 같이 지정된 값이 있는지 검증할 수 있습니다.

    $response->assertSessionHasAll([
        'name' => 'Taylor Otwell',
        'status' => 'active',
    ]);

<a name="assert-session-has-errors"></a>
#### assertSessionHasErrors

세션에 주어진 `$keys`에 대한 오류가 포함되어 있는지 확인합니다. `$keys`가 연관 배열인 경우 세션에 각 필드(키)에 대한 특정 오류 메시지(값)가 포함되어 있는지 검증합니다. 이 메서드는 유효성 검사 오류를 JSON 구조로 반환하는 대신 세션에 플래시하는 경로를 테스트할 때 사용해야 합니다.

    $response->assertSessionHasErrors(
        array $keys, $format = null, $errorBag = 'default'
    );

예를 들어 `name` 및 `email` 필드에 세션에 플래시된 유효성 검사 오류 메시지가 있는지 검증하려면 다음과 같이 `assertSessionHasErrors` 메서드를 호출할 수 있습니다.

    $response->assertSessionHasErrors(['name', 'email']);

또는 주어진 필드에 특정 유효성 검사 오류 메시지가 있는지 검증할 수 있습니다.

    $response->assertSessionHasErrors([
        'name' => 'The given name was invalid.'
    ]);

<a name="assert-session-has-errors-in"></a>
#### assertSessionHasErrorsIn

세션에 특정 [error bag](/docs/{{version}}/validation#named-error-bags) 내에서 주어진 `$keys`에 대한 오류가 포함되어 있는지 확인합니다. `$keys`가 연관 배열인 경우 세션에 오류 백 내에서 각 필드(키)에 대한 특정 오류 메시지(값)가 포함되어 있는지 검증합니다.

    $response->assertSessionHasErrorsIn($errorBag, $keys = [], $format = null);

<a name="assert-session-has-no-errors"></a>
#### assertSessionHasNoErrors

세션에 유효성 검사 오류가 없는지 확인:

    $response->assertSessionHasNoErrors();

<a name="assert-session-doesnt-have-errors"></a>
#### assertSessionDoesntHaveErrors

세션에 주어진 키에 대한 유효성 검사 오류가 없음을 확인:

    $response->assertSessionDoesntHaveErrors($keys = [], $format = null, $errorBag = 'default');

<a name="assert-session-missing"></a>
#### assertSessionMissing

세션에 주어진 키가 포함되어 있지 않은 것을 확인:

    $response->assertSessionMissing($key);

<a name="assert-status"></a>
#### assertStatus

response-응답이 주어진 HTTP 상태코드를 가지고 있는지 확인:

    $response->assertStatus($code);

<a name="assert-successful"></a>
#### assertSuccessful

response-응답이 성공적인(>= 200 and < 300) HTTP 상태코드를 가지고 있는지 확인:

    $response->assertSuccessful();

<a name="assert-unauthorized"></a>
#### assertUnauthorized

response-응답이 unauthorized(401) HTTP 상태코드를 가지고 있는지 확인:

    $response->assertUnauthorized();

<a name="assert-unprocessable"></a>
#### assertUnprocessable

response-응답이 unprocessable(422) HTTP 상태코드를 가지고 있는지 확인:

    $response->assertUnprocessable();

<a name="assert-valid"></a>
#### assertValid

응답에 주어진 키에 대한 유효성 검사 오류가 없는지 확인합니다. 이 메서드는 유효성 검사 오류가 JSON 구조로 반환되거나 유효성 검사 오류가 세션에 플래시된 응답에 대해 검증하는 데 사용할 수 있습니다.

    // Assert that no validation errors are present...
    $response->assertValid();

    // Assert that the given keys do not have validation errors...
    $response->assertValid(['name', 'email']);

<a name="assert-invalid"></a>
#### assertInvalid

응답에 주어진 키에 대한 유효성 검사 오류가 있는지 확인합니다. 이 메서드는 유효성 검사 오류가 JSON 구조로 반환되거나 유효성 검사 오류가 세션에 플래시된 응답에 대해 검증하는 데 사용할 수 있습니다.

    $response->assertInvalid(['name', 'email']);

주어진 키에 특정 유효성 검사 오류 메시지가 있는지 검증할 수도 있습니다. 그렇게 할 때 전체 메시지를 제공하거나 메시지의 일부만 제공할 수 있습니다.

    $response->assertInvalid([
        'name' => 'The name field is required.',
        'email' => 'valid email address',
    ]);

<a name="assert-view-has"></a>
#### assertViewHas

응답-response 뷰에 주어진 데이터 조각이 포함되어 있는지 확인:

    $response->assertViewHas($key, $value = null);

클로저를 `assertViewHas` 메소드에 대한 두 번째 인수로 전달하면, 특정 뷰 데이터 조각을 검사하고 검증할 수 있습니다.

    $response->assertViewHas('user', function (User $user) {
        return $user->name === 'Taylor';
    });

또한 뷰 데이터는 응답의 배열 변수로 액세스할 수 있으므로 편리하게 검사할 수 있습니다.

    $this->assertEquals('Taylor', $response['name']);

<a name="assert-view-has-all"></a>
#### assertViewHasAll

response-응답 뷰에서 주어진 데이터 리스트를 가지고 있는지 확인:

    $response->assertViewHasAll(array $data);

이 메서드는 뷰가 단순히 주어진 키와 일치하는 데이터를 포함하는지 검증하는 데 사용할 수 있습니다.

    $response->assertViewHasAll([
        'name',
        'email',
    ]);

또는 뷰 데이터가 있고 특정 값이 있는지 검증할 수 있습니다.

    $response->assertViewHasAll([
        'name' => 'Taylor Otwell',
        'email' => 'taylor@example.com,',
    ]);

<a name="assert-view-is"></a>
#### assertViewIs

라우터에 의해서 주어진 뷰가 반환되었는지 확인:

    $response->assertViewIs($value);

<a name="assert-view-missing"></a>
#### assertViewMissing

애플리케이션의 응답에서 반환된 뷰에서 주어진 데이터 키를 사용할 수 없음을 확인:

    $response->assertViewMissing($key);

<a name="authentication-assertions"></a>
### Authentication Assertions

라라벨은 또한 애플리케이션의 기능 테스트 내에서 활용할 수 있는 다양한 인증 관련 검증을 제공합니다. 이러한 메소드는 `get` 및 `post`와 같은 메소드에서 반환된 `Illuminate\Testing\TestResponse` 인스턴스가 아니라 테스트 클래스 자체에서 호출됩니다.

<a name="assert-authenticated"></a>
#### assertAuthenticated

사용자가 인증되었는지 확인:

    $this->assertAuthenticated($guard = null);

<a name="assert-guest"></a>
#### assertGuest

사용자가 인증되지 않았는지 확인:

    $this->assertGuest($guard = null);

<a name="assert-authenticated-as"></a>
#### assertAuthenticatedAs

특정 사용자가 인증되었는지 확인:

    $this->assertAuthenticatedAs($user, $guard = null);
