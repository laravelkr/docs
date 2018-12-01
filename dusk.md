# 브라우저 테스트 (라라벨 Dusk)

- [시작하기](#introduction)
- [설치하기](#installation)
    - [다른 브라우저 사용하기](#using-other-browsers)
- [시작하기](#getting-started)
    - [테스트 클래스 생성하기](#generating-tests)
    - [테스트 실행하기](#running-tests)
    - [구동환경 처리](#environment-handling)
    - [브라우저 생성하기](#creating-browsers)
    - [인증](#authentication)
    - [데이터베이스 마이그레이션](#migrations)
- [Element 조작하기](#interacting-with-elements)
    - [Dusk Selectors](#dusk-selectors)
    - [링크 클릭](#clicking-links)
    - [Text, Values, & Attributes](#text-values-and-attributes)
    - [Form 사용하기](#using-forms)
    - [파일 첨부](#attaching-files)
    - [키보드 사용하기](#using-the-keyboard)
    - [마우스 사용하기](#using-the-mouse)
    - [Scoping Selectors](#scoping-selectors)
    - [Waiting For Elements](#waiting-for-elements)
    - [Making Vue Assertions](#making-vue-assertions)
- [Available Assertions](#available-assertions)
- [Pages](#pages)
    - [Generating Pages](#generating-pages)
    - [Configuring Pages](#configuring-pages)
    - [Navigating To Pages](#navigating-to-pages)
    - [Shorthand Selectors](#shorthand-selectors)
    - [Page Methods](#page-methods)
- [Components](#components)
    - [Generating Components](#generating-components)
    - [Using Components](#using-components)
- [CI - 지속적 통합](#continuous-integration)
    - [Travis CI](#running-tests-on-travis-ci)
    - [CircleCI](#running-tests-on-circle-ci)
    - [Codeship](#running-tests-on-codeship)

<a name="introduction"></a>
## 시작하기

라라벨 Dusk는 구성과 사용이 쉬운 브라우저 자동화 및 테스팅 API를 제공합니다. 기본적으로 Dusk는 사용자 머신에 JDK 나 Selenium을 설치하도록 요구하지 않습니다. 대신에 Dusk는 독립적인 [ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/home)를 사용합니다. 그렇긴 하지만, 원하는 경우 다른 Selenium 호환 드라이버를 사용할 수도 있습니다.

<a name="installation"></a>
## 설치하기

시작하기 위해서, 컴포저 의존성에 `laravel/dusk`을 추가해야 합니다:

    composer require --dev laravel/dusk:"^2.0"

Dusk를 설치하고 나면, `Laravel\Dusk\DuskServiceProvider` 서비스 프로바이더를 등록해야 합니다. 일반적으로는 이 작업은 라라벨의 자동 서비스 프로바이더 등록 기능이 알아서 해줍니다.

> {note} 여러분이 Dusk의 서비스 프로바이더를 수동으로 등록하는 경우, 실서버 환경에서는 **절대로** 이를 등록하면 안됩니다. 이렇게 하면 익명의 사용자가 애플리케이션의 인증을 통과할 수가 있습니다.

Dusk 패키지를 설치하고 나서는 `dusk:install` 아티즌 명령어를 실행하십시오:

    php artisan dusk:install

`tests` 디렉토리 안에 `Browser` 디렉토리가 생성되고 예제 테스트가 포함됩니다. 그런 다음에 `.env` 파일에서 `APP_URL` 환경 변수를 설정하십시오. 이 변수는 브라우저에서 애플리케이션에 엑세스 하는데 사용하는 URL과 일치해야 합니다.

테스트를 실행하기 위해서는 `dusk` 아티즌 명령어를 사용합니다. `dusk` 명령어는 `phpunit` 명령어에 전달할 수 있는 모든 인자를 받을 수 있습니다:

    php artisan dusk

<a name="using-other-browsers"></a>
### 다른 브라우저 사용하기

기본적으로, Dusk는 브라우저 테스트에서 구글 크롬 및 [ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/home)을 사용합니다. 그렇지만, 여러분이 다른 Selenium 서버를 구성하고 원하는 브라우저를 테스트할 수도 있습니다.

다른 브라우저를 사용하기 위해서는, 애플리케이션의 베이스 Dusk 테스트 케이스가 되는 `tests/DuskTestCase.php` 파일을 엽니다. 이 파일안에 있는 `startChromeDriver`을 제거하면 됩니다. 이렇게 하면 Dusk가 자동으로 ChromeDriver를 시작하지 않게 됩니다:

    /**
     * Prepare for Dusk test execution.
     *
     * @beforeClass
     * @return void
     */
    public static function prepare()
    {
        // static::startChromeDriver();
    }

다음으로 `driver` 메소드에 접속하고자 하는 URL을 수정하면 됩니다. 또한 WebDriver에 전달되어야하는 "desired capabilities"을 수정할 수도 있습니다.

(역자주 : "desired capabilities"는 Facebook\WebDriver\Remote\RemoteWebDriver 의 create 메소드에서 필요한 DesiredCapabilities 클래스 호출을 의미합니다)

    /**
     * Create the RemoteWebDriver instance.
     *
     * @return \Facebook\WebDriver\Remote\RemoteWebDriver
     */
    protected function driver()
    {
        return RemoteWebDriver::create(
            'http://localhost:4444/wd/hub', DesiredCapabilities::phantomjs()
        );
    }

<a name="getting-started"></a>
## 시작하기

<a name="generating-tests"></a>
### 테스트 클래스 생성하기

Dusk 테스트를 생성하기 위해서는 `dusk:make` 아티즌 명령어를 사용합니다. 생성된 테스트 파일은 `tests/Browser` 디렉토리에 저장됩니다:

    php artisan dusk:make LoginTest

<a name="running-tests"></a>
### 테스트 실행하기

브라우저 테스트를 실행하려면, `dusk` 아티즌 명령어를 사용하십시오:

    php artisan dusk

`dusk` 명령어는 일반적으로 PHPUnit 테스트에 전달할 수 있는 인자들을 받을 수 있기 때문에, 지정된 [그룹](https://phpunit.de/manual/current/en/appendixes.annotations.html#appendixes.annotations.group)에 대해서 테스트를 실행할 수도 있습니다.

    php artisan dusk --group=foo

#### 수동으로 ChromeDriver 시작하기

기본적으로는 Dusk가 자동으로 ChromeDriver를 시작합니다. 만약 특정 시스템에서 ChromeDriver가 동작하지 않으면 `dusk` 명령어를 실행하기 전에 수동으로 ChromeDriver를 시작해야 합니다. 수동으로 ChromeDriver를 시작하려면, `tests/DuskTestCase.php` 파일의 다음 라인을 주석으로 표시해야합니다:

    /**
     * Prepare for Dusk test execution.
     *
     * @beforeClass
     * @return void
     */
    public static function prepare()
    {
        // static::startChromeDriver();
    }

또한, ChromeDriver가 9515포트가 아닌 다른 포트를 사용한다면, 이 클래스의 `driver` 메소드를 수정해야합니다:

    /**
     * Create the RemoteWebDriver instance.
     *
     * @return \Facebook\WebDriver\Remote\RemoteWebDriver
     */
    protected function driver()
    {
        return RemoteWebDriver::create(
            'http://localhost:9515', DesiredCapabilities::chrome()
        );
    }

<a name="environment-handling"></a>
### 구동환경 처리

테스트를 실행할 때 Dusk가 사용할 구동환경을 강제로 지정하려면, 프로젝트 루트 디렉토리에 `.env.dusk.{environment}` 파일을 만들면 됩니다. 예를 들어 `local` 구동환경에 `dusk` 명령어를 실행한다면, `.env.dusk.local` 파일을 만들어야 합니다.

테스트가 실행될 때, Dusk는 `.env` 파일을 백업 파일로 이름을 변경해 놓고, Dusk 구동환경 파일은 `.env` 로 바꿔놓습니다. 테스트가 완료되고 나면 `.env` 파일이 다시 복구됩니다.

<a name="creating-browsers"></a>
### 브라우저 생성하기

시작하기 위해서, 애플리케이션에 로그인할 수 있는지 확인하는 테스트를 작성해 보겠습니다. 테스트 파일을 생성한 다음에, 이를 수정하여 로그인 페이지로 이동하고, 인증 정보를 입력한다음, "Login" 버튼을 클릭해 보겠습니다. 브라우저 인스턴스를 생성하기 위해서는 `browse` 메소드를 호출하면 됩니다:

    <?php

    namespace Tests\Browser;

    use App\User;
    use Tests\DuskTestCase;
    use Laravel\Dusk\Chrome;
    use Illuminate\Foundation\Testing\DatabaseMigrations;

    class ExampleTest extends DuskTestCase
    {
        use DatabaseMigrations;

        /**
         * A basic browser test example.
         *
         * @return void
         */
        public function testBasicExample()
        {
            $user = factory(User::class)->create([
                'email' => 'taylor@laravel.com',
            ]);

            $this->browse(function ($browser) use ($user) {
                $browser->visit('/login')
                        ->type('email', $user->email)
                        ->type('password', 'secret')
                        ->press('Login')
                        ->assertPathIs('/home');
            });
        }
    }

위 예제에서와 같이, `browser` 메소드는 콜백을 전달 받습니다. 브라우저 인스턴스는 Dusk에 의해서 콜백에 전달되며, 애플리케이션과의 인터랙션을 통해서 테스트를 확인하는데 사용됩니다.

> {tip} 이 테스트 내용은 `make:auth` 아티즌 명령어를 통해서 생성된 로그인 화면을 테스트하는데 사용할 수 있습니다.

#### 여러개의 브라우저 생성하기

경우에 따라 테스트를 제대로 수행하기 위해서 여러개의 브라우저가 필요할 수도 있습니다. 예를 들어 웹 소켓과 상호작용하는 채팅 화면을 테스트 하는 경우 여러개의 브라우저가 필요합니다. 여러개의 브라우저 인스턴스를 생성하기 위해서는 주어진 `browser` 메소드에 하나 이상의 브라우저를 표시한 콜백을 "요청"하면 됩니다:

    $this->browse(function ($first, $second) {
        $first->loginAs(User::find(1))
              ->visit('/home')
              ->waitForText('Message');

        $second->loginAs(User::find(2))
               ->visit('/home')
               ->waitForText('Message')
               ->type('message', 'Hey Taylor')
               ->press('Send');

        $first->waitForText('Hey Taylor')
              ->assertSee('Jeffrey Way');
    });

#### 브라우저의 윈도우 리사이징

브라우저의 윈도우 사이즈를 조정하려면 `resize` 메소드를 사용하면 됩니다:

    $browser->resize(1920, 1080);

`maximize` 메소드는 브라우저의 윈도우를 최대 크기로 조정하는데 사용합니다:

    $browser->maximize();

<a name="authentication"></a>
### 인증

종종, 인증이 필요한 페이지를 테스트 할 수도 있습니다. 모든 테스트에서 로그인 화면에서의 인터렉션을 하지 않아도 되게끔, Dusk의 `loginAs` 메소드를 사용할 수 있습니다. `loginAs` 메소드는 사용자 ID 또는 모델 인스턴스를 전달받습니다:

    $this->browse(function ($first, $second) {
        $first->loginAs(User::find(1))
              ->visit('/home');
    });

> {note} `loginAs` 메소드를 사용하면, 파일 내의 모든 테스트에서 사용자 세션이 유지됩니다.

<a name="migrations"></a>
### 데이터베이스 마이그레이션

위의 인증 예제와 같이, 테스트가 마이그레이션을 필요로 할 때에는 절대로 `RefreshDatabase` 트레이트-trait을 사용하면 안됩니다. `RefreshDatabase` 트레이트-trait은 HTTP 요청에서 적용 할 수 없는 데이터베이스 트랜잭션을 사용합니다. 이 대신에 `DatabaseMigrations` 트레이트-trait을 사용하십시오:

    <?php

    namespace Tests\Browser;

    use App\User;
    use Tests\DuskTestCase;
    use Laravel\Dusk\Chrome;
    use Illuminate\Foundation\Testing\DatabaseMigrations;

    class ExampleTest extends DuskTestCase
    {
        use DatabaseMigrations;
    }

<a name="interacting-with-elements"></a>
## Element 조작하기

<a name="dusk-selectors"></a>
### Dusk Selectors

Choosing good CSS selectors for interacting with elements is one of the hardest parts of writing Dusk tests. Over time, frontend changes can cause CSS selectors like the following to break your tests:

    // HTML...

    <button>Login</button>

    // Test...

    $browser->click('.login-page .container div > button');

Dusk selectors allow you to focus on writing effective tests rather than remembering CSS selectors. To define a selector, add a `dusk` attribute to your HTML element. Then, prefix the selector with `@` to manipulate the attached element within a Dusk test:

    // HTML...

    <button dusk="login-button">Login</button>

    // Test...

    $browser->click('@login-button');

<a name="clicking-links"></a>
### 링크 클릭하기

링크를 클릭하기 위해서는 브라우저 인스턴스에서 `clickLink` 메소드를 사용하면 됩니다. `clickLink` 메소드는 주어진 텍스트에 해당하는 링크를 클릭합니다:

    $browser->clickLink($linkText);

> {note} 이 메소드는 jQuery를 통해서 상호작용을 수행합니다. 웹 페이지에서 jQuery를 사용할 수 없다면 Dusk는 테스트가 수행되는 동안에 자동으로 이를 로딩하여 사용하능한 형태로 만듭니다.

<a name="text-values-and-attributes"></a>
### Text, Values, & Attributes

#### Retrieving & Setting Values

Dusk 는 페이지에 현재 표시된 텍스트, 입력값 그리고 속성들에 대해서 처리할 수 있는 다양한 메소드를 제공합니다. 예를 들어 주어진 selector 와 매칭되는 Element의 "value"를 얻고자 한다면 `value` 메소드를 사용하면 됩니다:

    // Retrieve the value...
    $value = $browser->value('selector');

    // Set the value...
    $browser->value('selector', 'value');

#### 텍스트 조회하기

`text` 메소드는 주어진 selector 와 매칭되는 element의 텍스트를 조회하는데 사용됩니다:

    $text = $browser->text('selector');

#### 속성 조회하기

`attribute` 메소드는 주어진 selector와 매칭되는 element의 속성을 조회하는데 사용됩니다:

    $attribute = $browser->attribute('selector', 'value');

<a name="using-forms"></a>
### Form 사용하기

#### 값 입력하기

Dusk는 form 과 element와 상호작용할 수 있는 다양한 메소드를 제공합니다. 먼저, input 필드에 값을 입력하는 예쩨를 살펴보겠습니다:

    $browser->type('email', 'taylor@laravel.com');

위에서 보시다시피, `type` 메소드는 필요한 경우 CSS selector를 인자로 받습니다. 만약 CSS selector 가 전달되지 않으면, Dusk는 주어진 `name` 속성을 통해서 input 필드를 찾습니다. 그런 다음에 Dusk는 주어진 `name` 속성에 해당하는 `textarea` 를 찾으려고 시도합니다.

필드의 내용을 유지한채로 문자를 추가하려면 `append` 메소드를 사용하면 됩니다:

    $browser->type('tags', 'foo')
            ->append('tags', ', bar, baz');

`clear` 메소드를 통해서 input 필드의 값을 비울 수 있습니다:

    $browser->clear('email');

#### Dropdowns(셀렉트박스)

dropdown 셀렉트 박스에서 값을 선택하려면, `select` 메소드를 사용하면 됩니다. `type` 메소드와 같이 `select` 메소드는 완전한 CSS selector 를 필요로 하지는 않습니다. `select` 메소드에 값이 전달될 때에는, 표시된 텍스트가 아니라, 선택할 값을 전달해야 된다는 것에 주의하십시오:

    $browser->select('size', 'Large');

두번째 인자를 생략하면 랜덤한 값을 선택하게 됩니다:

    $browser->select('size');

#### 체크박스

체크박스 필드를 "체크" 처리하려면 `check` 메소드를 사용하면 됩니다. 다른 input 관련 메소드와 같이, 완전한 CSS selector는 필요하지 않습니다. 완전히 일치하는 selector를 찾을 수 없다면 Dusk는 `name` 속성을 기준으로 매칭되는 체크박스를 찾습니다:

    $browser->check('terms');

    $browser->uncheck('terms');

#### 라디오 버튼

라디오 버튼의 옵션을 "선택" 하려면, `radio` 메소드를 사용하면 됩니다. 다른 input 관련 메소드와 같이, 완전한 CSS selector는 필요하지 않습니다. 완전히 일치하는 selector를 찾을 수 없다면, Dusk는 `name` 과 `value` 속성을 기준으로 매칭되는 라디오를 찾습니다:

    $browser->radio('version', 'php7');

<a name="attaching-files"></a>
### 파일 첨부

`file` input element 에 파일을 첨부하는데에는 `attach` 메소드가 사용됩니다. 다른 input 관련 메소드와 같이, 완전한 CSS selector는 필요하지 않습니다. 완전히 일치하는 selector를 찾을 수 없다면, Dusk는 `name` 속성을 기준으로 매칭되는 file input을 찾습니다:

    $browser->attach('photo', __DIR__.'/photos/me.png');

<a name="using-the-keyboard"></a>
### 키보드 사용하기

`keys` 메소드는 `type` 메소드가 제공하는 것 보다 더 복잡한 작업들을 처리할 수 있습니다. 예를 들어, 특수 키를 값을 입력하는 동안 유지할 수 있습니다. 이 예제에서는 주어진 selector에 매칭되는 element에 `taylor`를 입력하는 동안 `shift` 키를 누르고 있는상태로 유지됩니다. `taylor` 가 입력된 후에, `otwell` 이 입력될 때는 특수키를 유지하지 않습니다:

    $browser->keys('selector', ['{shift}', 'taylor'], 'otwell');

애플리케이션에 포함된 기본 CSS selector에서 "단축키"를 전달 할 수도 있습니다:

    $browser->keys('.app', ['{command}', 'j']);

> {tip} 모든 특수키는 `{}` 문자로 감싸여져 있으며, 사용되는 표시자들은 `Facebook\WebDriver\WebDriverKeys` 클래스에 정의되어 있습니다. 이러한 사항은 [GitHub](https://github.com/facebook/php-webdriver/blob/community/lib/WebDriverKeys.php)에서 보다 자세한 내용을 확인할 수 있습니다.

<a name="using-the-mouse"></a>
### 마우스 사용하기

#### Element 클릭하기

`click` 메소드는 주어진 selector에 매칭되는 element를 "클릭"하는데 사용합니다:

    $browser->click('.selector');

#### Mouseover

`mouseover` 메소드는 주어진 selector에 매칭되는 element에 마우스를 이동하여 올려놓는 작업이 필요할 때 사용합니다:

    $browser->mouseover('.selector');

#### 드래그 & 드롭

`drag` 메소드는 주어진 selector와 매칭되는 element를 다른 element로 드래그 하는데 사용합니다:

    $browser->drag('.from-selector', '.to-selector');

또는, element를 어느 한 방향으로 드래그 할 수 있습니다:

    $browser->dragLeft('.selector', 10);
    $browser->dragRight('.selector', 10);
    $browser->dragUp('.selector', 10);
    $browser->dragDown('.selector', 10);

<a name="scoping-selectors"></a>
### Selector의 특정 범위를 제한하여 동작하기

때로는 주어진 selector안에서 특정 범위를 지정하여 동작을 수행하기을 원할 수도 있습니다. 예를 들어, 테이블 안에 있는 텍스트를 확인하고, 버튼을 클릭하고자 할 수 있습니다. 이 경우 `with`메소드를 사용하면 됩니다. `with` 메소드에 주어진 콜백안에서 수행되는 모든 동작들은 원래의 selector 에서 범위가 제한됩니다:

    $browser->with('.table', function ($table) {
        $table->assertSee('Hello World')
              ->clickLink('Delete');
    });

<a name="waiting-for-elements"></a>
### Element 기다리기

자바스크립트를 많이 사용한 애플리케이션을 테스트할 때, 테스트를 수행하기 위해서 특정 element가 표시되기까지 "기다리는" 행동이 필요할 때가 있습니다. Dusk는 이를 아주 쉽게 해결합니다. 다양한 메소드를 사용하여, element가 페이지에 표시될 때까지 기다리거나, 주어진 자바스크립트 표현식이 `true` 가 될 때까지 기다릴 수 있습니다.

#### 시간기준으로 기다리기

주어진 milliseconde 동안 테스트를 일시 정지하려면 `pause` 메소드를 사용합니다:

    $browser->pause(1000);

#### Selector 가 표시되기를 기다리기

`waitFor` 메소드는 주어진 CSS selector 에 해당하는 element가 페이지에 표시될 때까지 테스트 실행을 일시 정지하는데 사용합니다. 기본적으로 이 동작은 exception이 발생하기 전까지 최대 5초동안 테스트를 일시 정지합니다. 필요하다면, 두번재 인자로 커스텀 타임아웃 값을 전달 할 수 있습니다:

    // Wait a maximum of five seconds for the selector...
    $browser->waitFor('.selector');

    // Wait a maximum of one second for the selector...
    $browser->waitFor('.selector', 1);

또한 주어진 selector가 페이지에서 사라질 때까지 일시 정지할 수도 있습니다:

    $browser->waitUntilMissing('.selector');

    $browser->waitUntilMissing('.selector', 1);

#### 사용가능할 때 Selector의 특정 범위를 제한하여 동작하기

때로는, 주어진 selector 가 사용가능해지면, 매칭되는 selector에 작업을 수행하려고 할 수도 있습니다. 예를 들면, 모달 윈도우가 나타나기를 기다렸다가, 모달의 "OK" 버튼을 누른다고 해보겠습니다. 이런 경우 `whenAvailable` 메소를 사용하면 됩니다. 모든 element 작업은 원래의 selector 로 제한되어 주어진 콜백 안에서 수행됩니다

    $browser->whenAvailable('.modal', function ($modal) {
        $modal->assertSee('Hello World')
              ->press('OK');
    });

#### 텍스트가 나타날 때까지 기다리기

`waitForText` 메소드는 페이지에서 주어진 텍스트가 나타날때 까지 기다릴 때 사용됩니다:

    // Wait a maximum of five seconds for the text...
    $browser->waitForText('Hello World');

    // Wait a maximum of one second for the text...
    $browser->waitForText('Hello World', 1);

#### 링크가 나타날 때까지 기다리기

`waitForLink` 메소드는 페이지에서 주어진 링크가 나타날때 까지 기다릴 때 사용됩니다:

    // Wait a maximum of five seconds for the link...
    $browser->waitForLink('Create');

    // Wait a maximum of one second for the link...
    $browser->waitForLink('Create', 1);

#### 자바스크립트 표현식을 통해서 대기하기

때로는 주어진 자바스크립트 표현식이 `true` 일 때까지 테스트 실행을 멈추고 있기를 원할 수도 있습니다. 이럴 때는 `waitUntil` 메소드를 사용하면 됩니다. 이 메소드에 표현식을 전달할 때에는 `return` 키워드나 종료 세미콜론을 포함할 필요가 없습니다:

    // Wait a maximum of five seconds for the expression to be true...
    $browser->waitUntil('App.dataLoaded');

    $browser->waitUntil('App.data.servers.length > 0');

    // Wait a maximum of one second for the expression to be true...
    $browser->waitUntil('App.data.servers.length > 0', 1);

#### Waiting With A Callback

Many of the "wait" methods in Dusk rely on the underlying `waitUsing` method. You may use this method directly to wait for a given callback to return `true`. The `waitUsing` method accepts the maximum number of seconds to wait, the interval at which the Closure should be evaluated, the Closure, and an optional failure message:

    $browser->waitUsing(10, 1, function () use ($something) {
        return $something->isReady();
    }, "Something wasn't ready in time.");

<a name="making-vue-assertions"></a>
### Making Vue Assertions

Dusk even allows you to make assertions on the state of [Vue](https://vuejs.org) component data. For example, imagine your application contains the following Vue component:

    // HTML...

    <profile dusk="profile-component"></profile>

    // Component Definition...

    Vue.component('profile', {
        template: '<div>{{ user.name }}</div>',

        data: function () {
            return {
                user: {
                  name: 'Taylor'
                }
            };
        }
    });

You may assert on the state of the Vue component like so:

    /**
     * A basic Vue test example.
     *
     * @return void
     */
    public function testVue()
    {
        $this->browse(function (Browser $browser) {
            $browser->visit('/')
                    ->assertVue('user.name', 'Taylor', '@profile-component');
        });
    }

<a name="available-assertions"></a>
## 사용 가능한 Assertions

Dusk는 애플리케이션에서 사용가능한 다양한 assertion을 제공합니다. 가능한 모든 assertions은 아래 표와 같습니다:

Assertion  | 설명
------------- | -------------
`$browser->assertTitle($title)`  |  페이지 타이틀이 주어진 텍스트와 일치하는지 확인.
`$browser->assertTitleContains($title)`  |  페이지 타이틀이 주어진 텍스트를 포함하는지 확인.
`$browser->assertUrlIs($url)`  |  현재의 URL이 (쿼리 스트링을 제외하고) 주어진 문자열과 일치 하는지 확인.
`$browser->assertPathBeginsWith($path)`  |  현재 URL 경로가 주어진 경로로 시작하는지 확인.
`$browser->assertPathIs('/home')`  |  현재 경로가 주어진 경로와 일치하는지 확인.
`$browser->assertPathIsNot('/home')`  |  현재 경로가 주어진 경로와 일치하지 않는 것을 확인.
`$browser->assertRouteIs($name, $parameters)`  |  현재 URL이 주어진 이름의 라우트의 URL과 일치하는지 확인.
`$browser->assertQueryStringHas($name, $value)`  |  쿼리 스트링 파라미터가 존재하고 주어진 값을 가지고 있는지 확인.
`$browser->assertQueryStringMissing($name)`  |  주어진 쿼리 스트링 파라미터가 없다는 것을 확인.
`$browser->assertHasQueryStringParameter($name)`  |  주어진 쿼리 스트링 파라미터가 존재하는지 확인.
`$browser->assertHasCookie($name)`  |  주어진 쿠키가 존재하는지 확인.
`$browser->assertCookieMissing($name)`  |  주어진 쿠키가 존재하지 않는 것을 확인.
`$browser->assertCookieValue($name, $value)`  |  쿠키가 주어진 값을 가지고 있는지 확인.
`$browser->assertPlainCookieValue($name, $value)`  |  암호화 되지 않은 쿠키가 주어진 값을 가지고 있는지 확인.
`$browser->assertSee($text)`  |  현재 페이지에 주어진 텍스트가 존재하는지 확인.
`$browser->assertDontSee($text)`  |  현재 페이지에 주어진 텍스트가 존재하지 않는 것을 확인.
`$browser->assertSeeIn($selector, $text)`  |  selector 안에서 주어진 텍스트가 존재하는지 확인.
`$browser->assertDontSeeIn($selector, $text)`  |  selector 안에서 주어진 텍스트가 존재하지 않는 것을 확인.
`$browser->assertSourceHas($code)`  |  주어진 소스 코드가 현재 페이지에 존재하는지 확인.
`$browser->assertSourceMissing($code)`  |  주어진 소스 코드가 현재 페이지에 존재하지 않는 것을 확인.
`$browser->assertSeeLink($linkText)`  |  현재 페이지에 주어진 링크가 존재하는지 확인.
`$browser->assertDontSeeLink($linkText)`  |  현재 페이지에 주어진 링크가 존재하지 않는 것을 확인.
`$browser->assertInputValue($field, $value)`  |  주어진 input 필드가 주어진 값을 가지는지 확인.
`$browser->assertInputValueIsNot($field, $value)`  |  주어진 input 필드가 주어진 값을 가지고 있지 않을 것을 확인.
`$browser->assertChecked($field)`  |  주어진 체크박스가 체크되어 있는지 확인.
`$browser->assertNotChecked($field)`  |  주어진 체크박스가 체크되어 있지 않은 것을 확인.
`$browser->assertRadioSelected($field, $value)`  | 주어진 라디오박스가 선택되어 있는지 확인.
`$browser->assertRadioNotSelected($field, $value)` |  주어진 라디오박스가 선택되어 있지 않은 것을 확인.
`$browser->assertSelected($field, $value)`  |  주어진 드랍박스(셀렉트박스)에서 주어진 값이 선택되어 있는 것을 확인.
`$browser->assertNotSelected($field, $value)`  |  주어진 드랍박스(셀렉트박스)에서 주어진 값이 선택되어 있지 않은 것을 확인.
`$browser->assertSelectHasOptions($field, $values)`  |  주어진 값의 배열이 선택가능한 옵션으로 제공되는지 확인.
`$browser->assertSelectMissingOptions($field, $values)`  |  주어진 값의 배열이 선택가능하지 않는 옵션으로 제공되는지 확인.
`$browser->assertSelectHasOption($field, $value)`  |  주어진 값이 필드에서 선택 가능한 값인지 확인.
`$browser->assertValue($selector, $value)`  |  주어진 selector 와 매칭되는 element가 주어진 값을 가지는지 확인.
`$browser->assertVisible($selector)`  |  주어진 selector 와 매칭되는 element가 화면에 보이는지 확인.
`$browser->assertMissing($selector)`  |  주어진 selector 와 매칭되는 element가 화면에 보이지 않는 것을 확인.
`$browser->assertDialogOpened($message)`  |  주어진 메세지가 있는 자바스크립트 다이얼로그가 열려있는지 확인.
`$browser->assertVue($property, $value, $component)`  |  주어진 Vue 컴포넌트의 속성이 주어진 값과 일치하는지 확인.
`$browser->assertVueIsNot($property, $value, $component)`  |  주어진 Vue 컴포넌트의 속성이 주어진 값과 일치하지 않는 것을 확인.

<a name="pages"></a>
## Pages

Sometimes, tests require several complicated actions to be performed in sequence. This can make your tests harder to read and understand. Pages allow you to define expressive actions that may then be performed on a given page using a single method. Pages also allow you to define short-cuts to common selectors for your application or a single page.

<a name="generating-pages"></a>
### Generating Pages

To generate a page object, use the `dusk:page` Artisan command. All page objects will be placed in the `tests/Browser/Pages` directory:

    php artisan dusk:page Login

<a name="configuring-pages"></a>
### Configuring Pages

By default, pages have three methods: `url`, `assert`, and `elements`. We will discuss the `url` and `assert` methods now. The `elements` method will be [discussed in more detail below](#shorthand-selectors).

#### The `url` Method

The `url` method should return the path of the URL that represents the page. Dusk will use this URL when navigating to the page in the browser:

    /**
     * Get the URL for the page.
     *
     * @return string
     */
    public function url()
    {
        return '/login';
    }

#### The `assert` Method

The `assert` method may make any assertions necessary to verify that the browser is actually on the given page. Completing this method is not necessary; however, you are free to make these assertions if you wish. These assertions will be run automatically when navigating to the page:

    /**
     * Assert that the browser is on the page.
     *
     * @return void
     */
    public function assert(Browser $browser)
    {
        $browser->assertPathIs($this->url());
    }

<a name="navigating-to-pages"></a>
### Navigating To Pages

Once a page has been configured, you may navigate to it using the `visit` method:

    use Tests\Browser\Pages\Login;

    $browser->visit(new Login);

Sometimes you may already be on a given page and need to "load" the page's selectors and methods into the current test context. This is common when pressing a button and being redirected to a given page without explicitly navigating to it. In this situation, you may use the `on` method to load the page:

    use Tests\Browser\Pages\CreatePlaylist;

    $browser->visit('/dashboard')
            ->clickLink('Create Playlist')
            ->on(new CreatePlaylist)
            ->assertSee('@create');

<a name="shorthand-selectors"></a>
### Shorthand Selectors

The `elements` method of pages allows you to define quick, easy-to-remember shortcuts for any CSS selector on your page. For example, let's define a shortcut for the "email" input field of the application's login page:

    /**
     * Get the element shortcuts for the page.
     *
     * @return array
     */
    public function elements()
    {
        return [
            '@email' => 'input[name=email]',
        ];
    }

Now, you may use this shorthand selector anywhere you would use a full CSS selector:

    $browser->type('@email', 'taylor@laravel.com');

#### Global Shorthand Selectors

After installing Dusk, a base `Page` class will be placed in your `tests/Browser/Pages` directory. This class contains a `siteElements` method which may be used to define global shorthand selectors that should be available on every page throughout your application:

    /**
     * Get the global element shortcuts for the site.
     *
     * @return array
     */
    public static function siteElements()
    {
        return [
            '@element' => '#selector',
        ];
    }

<a name="page-methods"></a>
### Page Methods

In addition to the default methods defined on pages, you may define additional methods which may be used throughout your tests. For example, let's imagine we are building a music management application. A common action for one page of the application might be to create a playlist. Instead of re-writing the logic to create a playlist in each test, you may define a `createPlaylist` method on a page class:

    <?php

    namespace Tests\Browser\Pages;

    use Laravel\Dusk\Browser;

    class Dashboard extends Page
    {
        // Other page methods...

        /**
         * Create a new playlist.
         *
         * @param  \Laravel\Dusk\Browser  $browser
         * @param  string  $name
         * @return void
         */
        public function createPlaylist(Browser $browser, $name)
        {
            $browser->type('name', $name)
                    ->check('share')
                    ->press('Create Playlist');
        }
    }

Once the method has been defined, you may use it within any test that utilizes the page. The browser instance will automatically be passed to the page method:

    use Tests\Browser\Pages\Dashboard;

    $browser->visit(new Dashboard)
            ->createPlaylist('My Playlist')
            ->assertSee('My Playlist');

<a name="components"></a>
## Components

Components are similar to Dusk’s “page objects”, but are intended for pieces of UI and functionality that are re-used throughout your application, such as a navigation bar or notification window. As such, components are not bound to specific URLs.

<a name="generating-components"></a>
### Generating Components

To generate a component, use the `dusk:component` Artisan command. New components are placed in the `test/Browser/Components` directory:

    php artisan dusk:component DatePicker

As shown above, a "date picker" is an example of a component that might exist throughout your application on a variety of pages. It can become cumbersome to manually write the browser automation logic to select a date in dozens of tests throughout your test suite. Instead, we can define a Dusk component to represent the date picker, allowing us to encapsulate that logic within the component:

    <?php

    namespace Tests\Browser\Components;

    use Laravel\Dusk\Browser;
    use Laravel\Dusk\Component as BaseComponent;

    class DatePicker extends BaseComponent
    {
        /**
         * Get the root selector for the component.
         *
         * @return string
         */
        public function selector()
        {
            return '.date-picker';
        }

        /**
         * Assert that the browser page contains the component.
         *
         * @param  Browser  $browser
         * @return void
         */
        public function assert(Browser $browser)
        {
            $browser->assertVisible($this->selector());
        }

        /**
         * Get the element shortcuts for the component.
         *
         * @return array
         */
        public function elements()
        {
            return [
                '@date-field' => 'input.datepicker-input',
                '@month-list' => 'div > div.datepicker-months',
                '@day-list' => 'div > div.datepicker-days',
            ];
        }

        /**
         * Select the given date.
         *
         * @param  \Laravel\Dusk\Browser  $browser
         * @param  int  $month
         * @param  int  $year
         * @return void
         */
        public function selectDate($browser, $month, $year)
        {
            $browser->click('@date-field')
                    ->within('@month-list', function ($browser) use ($month) {
                        $browser->click($month);
                    })
                    ->within('@day-list', function ($browser) use ($day) {
                        $browser->click($day);
                    });
        }
    }

<a name="using-components"></a>
### Using Components

Once the component has been defined, we can easily select a date within the date picker from any test. And, if the logic necessary to select a date changes, we only need to update the component:

    <?php

    namespace Tests\Browser;

    use Tests\DuskTestCase;
    use Laravel\Dusk\Browser;
    use Tests\Browser\Components\DatePicker;
    use Illuminate\Foundation\Testing\DatabaseMigrations;

    class ExampleTest extends DuskTestCase
    {
        /**
         * A basic component test example.
         *
         * @return void
         */
        public function testBasicExample()
        {
            $this->browse(function (Browser $browser) {
                $browser->visit('/')
                        ->within(new DatePicker, function ($browser) {
                            $browser->selectDate(1, 2018);
                        })
                        ->assertSee('January');
            });
        }
    }

<a name="continuous-integration"></a>
## CI - 지속적 통합

<a name="running-tests-on-travis-ci"></a>
### Travis CI

Travis CI에서 Dusk 테스트를 수행하기 위해서는 "sudo-enabled"가 가능한 우분투 14.04 (Trusty) 환경을 사용해야 합니다. Travis CI는 그래픽 환경이 아니기 때문에, 크롬 브라우저를 구동하기 위해서는 몇가지 별도의 작업을 필요로 합니다. 추가적으로 PHP 내장 브라우저를 구동하기 위해서 `php artisan serve`를 사용할 수 있습니다:

    sudo: required
    dist: trusty

    addons:
       chrome: stable

    install:
       - cp .env.testing .env
       - travis_retry composer install --no-interaction --prefer-dist --no-suggest

    before_script:
       - google-chrome-stable --headless --disable-gpu --remote-debugging-port=9222 http://localhost &
       - php artisan serve &

    script:
       - php artisan dusk

<a name="running-tests-on-circle-ci"></a>
### CircleCI

#### CircleCI 1.0

Dusk 테스트를 수행하기 위해서 CircleCI 1.0를 사용한다면, 다음 설정 파일을 사용할 수 있습니다. TravisCI와 같이, PHP 내장 웹서버를 실행하기 위해서 `php artisan serve` 를 사용할 수 있습니다:

	dependencies:
	  pre:
	      - curl -L -o google-chrome.deb https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
	      - sudo dpkg -i google-chrome.deb
	      - sudo sed -i 's|HERE/chrome\"|HERE/chrome\" --disable-setuid-sandbox|g' /opt/google/chrome/google-chrome
	      - rm google-chrome.deb

    test:
        pre:
            - "./vendor/laravel/dusk/bin/chromedriver-linux":
                background: true
            - cp .env.testing .env
            - "php artisan serve":
                background: true

        override:
            - php artisan dusk


#### CircleCI 2.0

Dusk 테스트를 수행하기 위해서 CircleCI 2.0를 사용한다면, 빌드를 위해서 다음을 추가하십시오:

     version: 2
     jobs:
         build:
             steps:
                - run: sudo apt-get install -y libsqlite3-dev
                - run: cp .env.testing .env
                - run: composer install -n --ignore-platform-reqs
                - run: npm install
                - run: npm run production
                - run: vendor/bin/phpunit

                - run:
                   name: Start Chrome Driver
                   command: ./vendor/laravel/dusk/bin/chromedriver-linux
                   background: true

                - run:
                   name: Run Laravel Server
                   command: php artisan serve
                   background: true

                - run:
                   name: Run Laravel Dusk Tests
                   command: php artisan dusk

<a name="running-tests-on-codeship"></a>
### Codeship

[Codeship](https://codeship.com)에서 Dusk 테스트를 실행하려면, 다음의 명령어들을 Codeship 프로젝트에 추가하십시오. 물론, 이 명령어들은 기본적인 명령어들이며, 필요한 경우 자유롭게 추가할 수 있습니다:

    phpenv local 7.1
    cp .env.testing .env
    composer install --no-interaction
    nohup bash -c "./vendor/laravel/dusk/bin/chromedriver-linux 2>&1 &"
    nohup bash -c "php artisan serve 2>&1 &" && sleep 5
    php artisan dusk
