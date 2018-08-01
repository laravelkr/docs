# Browser Tests (Laravel Dusk)
# 브라우저 테스트 (라라벨 Dusk)

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Installation](#installation)
- [설치하기](#installation)
    - [Using Other Browsers](#using-other-browsers)
    - [다른 브라우저 사용하기](#using-other-browsers)
- [Getting Started](#getting-started)
- [시작하기](#getting-started)
    - [Generating Tests](#generating-tests)
    - [테스트 클래스 생성하기](#generating-tests)
    - [Running Tests](#running-tests)
    - [테스트 실행하기](#running-tests)
    - [Environment Handling](#environment-handling)
    - [구동환경 처리](#environment-handling)
    - [Creating Browsers](#creating-browsers)
    - [브라우저 생성하기](#creating-browsers)
    - [Authentication](#authentication)
    - [인증](#authentication)
    - [Database Migrations](#migrations)
    - [데이터베이스 마이그레이션](#migrations)
- [Interacting With Elements](#interacting-with-elements)
- [Element 조작하기](#interacting-with-elements)
    - [Dusk Selectors](#dusk-selectors)
    - [Dusk Selectors](#dusk-selectors)
    - [Clicking Links](#clicking-links)
    - [링크 클릭](#clicking-links)
    - [Text, Values, & Attributes](#text-values-and-attributes)
    - [Text, Values, & Attributes](#text-values-and-attributes)
    - [Using Forms](#using-forms)
    - [Form 사용하기](#using-forms)
    - [Attaching Files](#attaching-files)
    - [파일 첨부](#attaching-files)
    - [Using The Keyboard](#using-the-keyboard)
    - [키보드 사용하기](#using-the-keyboard)
    - [Using The Mouse](#using-the-mouse)
    - [마우스 사용하기](#using-the-mouse)
    - [Scoping Selectors](#scoping-selectors)
    - [Scoping Selectors](#scoping-selectors)
    - [Waiting For Elements](#waiting-for-elements)
    - [Waiting For Elements](#waiting-for-elements)
    - [Making Vue Assertions](#making-vue-assertions)
    - [Making Vue Assertions](#making-vue-assertions)
- [Available Assertions](#available-assertions)
- [Available Assertions](#available-assertions)
- [Pages](#pages)
- [Pages](#pages)
    - [Generating Pages](#generating-pages)
    - [Generating Pages](#generating-pages)
    - [Configuring Pages](#configuring-pages)
    - [Configuring Pages](#configuring-pages)
    - [Navigating To Pages](#navigating-to-pages)
    - [Navigating To Pages](#navigating-to-pages)
    - [Shorthand Selectors](#shorthand-selectors)
    - [Shorthand Selectors](#shorthand-selectors)
    - [Page Methods](#page-methods)
    - [Page Methods](#page-methods)
- [Components](#components)
- [Components](#components)
    - [Generating Components](#generating-components)
    - [Generating Components](#generating-components)
    - [Using Components](#using-components)
    - [Using Components](#using-components)
- [Continuous Integration](#continuous-integration)
- [CI - 지속적 통합](#continuous-integration)
    - [Travis CI](#running-tests-on-travis-ci)
    - [Travis CI](#running-tests-on-travis-ci)
    - [CircleCI](#running-tests-on-circle-ci)
    - [CircleCI](#running-tests-on-circle-ci)
    - [Codeship](#running-tests-on-codeship)
    - [Codeship](#running-tests-on-codeship)
    - [Heroku CI](#running-tests-on-heroku-ci)
    - [Heroku CI](#running-tests-on-heroku-ci)
    - [Travis CI](#running-tests-on-travis-ci)
    - [Travis CI](#running-tests-on-travis-ci)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel Dusk provides an expressive, easy-to-use browser automation and testing API. By default, Dusk does not require you to install JDK or Selenium on your machine. Instead, Dusk uses a standalone [ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/home) installation. However, you are free to utilize any other Selenium compatible driver you wish.

라라벨 Dusk는 구성과 사용이 쉬운 브라우저 자동화 및 테스팅 API를 제공합니다. 기본적으로 Dusk는 사용자 머신에 JDK 나 Selenium을 설치하도록 요구하지 않습니다. 대신에 Dusk는 독립적인 [ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/home)를 사용합니다. 그렇긴 하지만, 원하는 경우 다른 Selenium 호환 드라이버를 사용할 수도 있습니다.

<a name="installation"></a>
## Installation
## 설치하기

To get started, you should add the `laravel/dusk` Composer dependency to your project:

시작하기 위해서, 컴포저 의존성에 `laravel/dusk`을 추가해야 합니다:

    composer require --dev laravel/dusk

Once Dusk is installed, you should register the `Laravel\Dusk\DuskServiceProvider` service provider. Typically, this will be done automatically via Laravel's automatic service provider registration.

Dusk를 설치하고 나면, `Laravel\Dusk\DuskServiceProvider` 서비스 프로바이더를 등록해야 합니다. 일반적으로는 이 작업은 라라벨의 자동 서비스 프로바이더 등록 기능이 알아서 해줍니다.

> {note} If you are manually registering Dusk's service provider, you should **never** register it in your production environment, as doing so could lead to arbitrary users being able to authenticate with your application.

> {note} 여러분이 Dusk의 서비스 프로바이더를 수동으로 등록하는 경우, 실서버 환경에서는 **절대로** 이를 등록하면 안됩니다. 이렇게 하면 익명의 사용자가 애플리케이션의 인증을 통과할 수가 있습니다.

After installing the Dusk package, run the `dusk:install` Artisan command:

Dusk 패키지를 설치하고 나서는 `dusk:install` 아티즌 명령어를 실행하십시오:

    php artisan dusk:install

A `Browser` directory will be created within your `tests` directory and will contain an example test. Next, set the `APP_URL` environment variable in your `.env` file. This value should match the URL you use to access your application in a browser.

`tests` 디렉토리 안에 `Browser` 디렉토리가 생성되고 예제 테스트가 포함됩니다. 그런 다음에 `.env` 파일에서 `APP_URL` 환경 변수를 설정하십시오. 이 변수는 브라우저에서 애플리케이션에 엑세스 하는데 사용하는 URL과 일치해야 합니다.

To run your tests, use the `dusk` Artisan command. The `dusk` command accepts any argument that is also accepted by the `phpunit` command:

테스트를 실행하기 위해서는 `dusk` 아티즌 명령어를 사용합니다. `dusk` 명령어는 `phpunit` 명령어에 전달할 수 있는 모든 인자를 받을 수 있습니다:

    php artisan dusk

<a name="using-other-browsers"></a>
### Using Other Browsers
### 다른 브라우저 사용하기

By default, Dusk uses Google Chrome and a standalone [ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/home) installation to run your browser tests. However, you may start your own Selenium server and run your tests against any browser you wish.

기본적으로, Dusk는 브라우저 테스트에서 구글 크롬 및 [ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/home)을 사용합니다. 그렇지만, 여러분이 다른 Selenium 서버를 구성하고 원하는 브라우저를 테스트할 수도 있습니다.

To get started, open your `tests/DuskTestCase.php` file, which is the base Dusk test case for your application. Within this file, you can remove the call to the `startChromeDriver` method. This will stop Dusk from automatically starting the ChromeDriver:

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

Next, you may modify the `driver` method to connect to the URL and port of your choice. In addition, you may modify the "desired capabilities" that should be passed to the WebDriver:

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
## Getting Started
## 시작하기

<a name="generating-tests"></a>
### Generating Tests
### 테스트 클래스 생성하기

To generate a Dusk test, use the `dusk:make` Artisan command. The generated test will be placed in the `tests/Browser` directory:

Dusk 테스트를 생성하기 위해서는 `dusk:make` 아티즌 명령어를 사용합니다. 생성된 테스트 파일은 `tests/Browser` 디렉토리에 저장됩니다:

    php artisan dusk:make LoginTest

<a name="running-tests"></a>
### Running Tests
### 테스트 실행하기

To run your browser tests, use the `dusk` Artisan command:

브라우저 테스트를 실행하려면, `dusk` 아티즌 명령어를 사용하십시오:

    php artisan dusk

The `dusk` command accepts any argument that is normally accepted by the PHPUnit test runner, allowing you to only run the tests for a given [group](https://phpunit.de/manual/current/en/appendixes.annotations.html#appendixes.annotations.group), etc:

`dusk` 명령어는 일반적으로 PHPUnit 테스트에 전달할 수 있는 인자들을 받을 수 있기 때문에, 지정된 [그룹](https://phpunit.de/manual/current/en/appendixes.annotations.html#appendixes.annotations.group)에 대해서 테스트를 실행할 수도 있습니다.

    php artisan dusk --group=foo

#### Manually Starting ChromeDriver
#### 수동으로 ChromeDriver 시작하기

By default, Dusk will automatically attempt to start ChromeDriver. If this does not work for your particular system, you may manually start ChromeDriver before running the `dusk` command. If you choose to start ChromeDriver manually, you should comment out the following line of your `tests/DuskTestCase.php` file:

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

In addition, if you start ChromeDriver on a port other than 9515, you should modify the `driver` method of the same class:

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
### Environment Handling
### 구동환경 처리

To force Dusk to use its own environment file when running tests, create a `.env.dusk.{environment}` file in the root of your project. For example, if you will be initiating the `dusk` command from your `local` environment, you should create a `.env.dusk.local` file.

테스트를 실행할 때 Dusk가 사용할 구동환경을 강제로 지정하려면, 프로젝트 루트 디렉토리에 `.env.dusk.{environment}` 파일을 만들면 됩니다. 예를 들어 `local` 구동환경에 `dusk` 명령어를 실행한다면, `.env.dusk.local` 파일을 만들어야 합니다.

When running tests, Dusk will back-up your `.env` file and rename your Dusk environment to `.env`. Once the tests have completed, your `.env` file will be restored.

테스트가 실행될 때, Dusk는 `.env` 파일을 백업 파일로 이름을 변경해 놓고, Dusk 구동환경 파일은 `.env` 로 바꿔놓습니다. 테스트가 완료되고 나면 `.env` 파일이 다시 복구됩니다.

<a name="creating-browsers"></a>
### Creating Browsers
### 브라우저 생성하기

To get started, let's write a test that verifies we can log into our application. After generating a test, we can modify it to navigate to the login page, enter some credentials, and click the "Login" button. To create a browser instance, call the `browse` method:

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

As you can see in the example above, the `browse` method accepts a callback. A browser instance will automatically be passed to this callback by Dusk and is the main object used to interact with and make assertions against your application.

위 예제에서와 같이, `browser` 메소드는 콜백을 전달 받습니다. 브라우저 인스턴스는 Dusk에 의해서 콜백에 전달되며, 애플리케이션과의 인터랙션을 통해서 테스트를 확인하는데 사용됩니다.

> {tip} This test can be used to test the login screen generated by the `make:auth` Artisan command.

> {tip} 이 테스트 내용은 `make:auth` 아티즌 명령어를 통해서 생성된 로그인 화면을 테스트하는데 사용할 수 있습니다.

#### Creating Multiple Browsers
#### 여러개의 브라우저 생성하기

Sometimes you may need multiple browsers in order to properly carry out a test. For example, multiple browsers may be needed to test a chat screen that interacts with websockets. To create multiple browsers, "ask" for more than one browser in the signature of the callback given to the `browse` method:

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

#### Resizing Browser Windows
#### 브라우저의 윈도우 리사이징

You may use the `resize` method to adjust the size of the browser window:

브라우저의 윈도우 사이즈를 조정하려면 `resize` 메소드를 사용하면 됩니다:

    $browser->resize(1920, 1080);

The `maximize` method may be used to maximize the browser window:

`maximize` 메소드는 브라우저의 윈도우를 최대 크기로 조정하는데 사용합니다:

    $browser->maximize();

<a name="authentication"></a>
### Authentication
### 인증

Often, you will be testing pages that require authentication. You can use Dusk's `loginAs` method in order to avoid interacting with the login screen during every test. The `loginAs` method accepts a user ID or user model instance:

종종, 인증이 필요한 페이지를 테스트 할 수도 있습니다. 모든 테스트에서 로그인 화면에서의 인터렉션을 하지 않아도 되게끔, Dusk의 `loginAs` 메소드를 사용할 수 있습니다. `loginAs` 메소드는 사용자 ID 또는 모델 인스턴스를 전달받습니다:

    $this->browse(function ($first, $second) {
        $first->loginAs(User::find(1))
              ->visit('/home');
    });

> {note} After using the `loginAs` method, the user session will be maintained for all tests within the file.

> {note} `loginAs` 메소드를 사용하면, 파일 내의 모든 테스트에서 사용자 세션이 유지됩니다.

<a name="migrations"></a>
### Database Migrations
### 데이터베이스 마이그레이션

When your test requires migrations, like the authentication example above, you should never use the `RefreshDatabase` trait. The `RefreshDatabase` trait leverages database transactions which will not be applicable across HTTP requests. Instead, use the `DatabaseMigrations` trait:

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
## Interacting With Elements
## Element 조작하기

<a name="dusk-selectors"></a>
### Dusk Selectors
### Dusk Selectors

Choosing good CSS selectors for interacting with elements is one of the hardest parts of writing Dusk tests. Over time, frontend changes can cause CSS selectors like the following to break your tests:

Choosing good CSS selectors for interacting with elements is one of the hardest parts of writing Dusk tests. Over time, frontend changes can cause CSS selectors like the following to break your tests:

    // HTML...

    <button>Login</button>

    // Test...

    $browser->click('.login-page .container div > button');

Dusk selectors allow you to focus on writing effective tests rather than remembering CSS selectors. To define a selector, add a `dusk` attribute to your HTML element. Then, prefix the selector with `@` to manipulate the attached element within a Dusk test:

Dusk selectors allow you to focus on writing effective tests rather than remembering CSS selectors. To define a selector, add a `dusk` attribute to your HTML element. Then, prefix the selector with `@` to manipulate the attached element within a Dusk test:

    // HTML...

    <button dusk="login-button">Login</button>

    // Test...

    $browser->click('@login-button');

<a name="clicking-links"></a>
### Clicking Links
### 링크 클릭하기

To click a link, you may use the `clickLink` method on the browser instance. The `clickLink` method will click the link that has the given display text:

링크를 클릭하기 위해서는 브라우저 인스턴스에서 `clickLink` 메소드를 사용하면 됩니다. `clickLink` 메소드는 주어진 텍스트에 해당하는 링크를 클릭합니다:

    $browser->clickLink($linkText);

> {note} This method interacts with jQuery. If jQuery is not available on the page, Dusk will automatically inject it into the page so it is available for the test's duration.

> {note} 이 메소드는 jQuery를 통해서 상호작용을 수행합니다. 웹 페이지에서 jQuery를 사용할 수 없다면 Dusk는 테스트가 수행되는 동안에 자동으로 이를 로딩하여 사용하능한 형태로 만듭니다.

<a name="text-values-and-attributes"></a>
### Text, Values, & Attributes
### Text, Values, & Attributes

#### Retrieving & Setting Values
#### Retrieving & Setting Values

Dusk provides several methods for interacting with the current display text, value, and attributes of elements on the page. For example, to get the "value" of an element that matches a given selector, use the `value` method:

Dusk 는 페이지에 현재 표시된 텍스트, 입력값 그리고 속성들에 대해서 처리할 수 있는 다양한 메소드를 제공합니다. 예를 들어 주어진 selector 와 매칭되는 Element의 "value"를 얻고자 한다면 `value` 메소드를 사용하면 됩니다:

    // Retrieve the value...
    $value = $browser->value('selector');

    // Set the value...
    $browser->value('selector', 'value');

#### Retrieving Text
#### 텍스트 조회하기

The `text` method may be used to retrieve the display text of an element that matches the given selector:

`text` 메소드는 주어진 selector 와 매칭되는 element의 텍스트를 조회하는데 사용됩니다:

    $text = $browser->text('selector');

#### Retrieving Attributes
#### 속성 조회하기

Finally, the `attribute` method may be used to retrieve an attribute of an element matching the given selector:

`attribute` 메소드는 주어진 selector와 매칭되는 element의 속성을 조회하는데 사용됩니다:

    $attribute = $browser->attribute('selector', 'value');

<a name="using-forms"></a>
### Using Forms
### Form 사용하기

#### Typing Values
#### 값 입력하기

Dusk provides a variety of methods for interacting with forms and input elements. First, let's take a look at an example of typing text into an input field:

Dusk는 form 과 element와 상호작용할 수 있는 다양한 메소드를 제공합니다. 먼저, input 필드에 값을 입력하는 예쩨를 살펴보겠습니다:

    $browser->type('email', 'taylor@laravel.com');

Note that, although the method accepts one if necessary, we are not required to pass a CSS selector into the `type` method. If a CSS selector is not provided, Dusk will search for an input field with the given `name` attribute. Finally, Dusk will attempt to find a `textarea` with the given `name` attribute.

위에서 보시다시피, `type` 메소드는 필요한 경우 CSS selector를 인자로 받습니다. 만약 CSS selector 가 전달되지 않으면, Dusk는 주어진 `name` 속성을 통해서 input 필드를 찾습니다. 그런 다음에 Dusk는 주어진 `name` 속성에 해당하는 `textarea` 를 찾으려고 시도합니다.

To append text to a field without clearing its content, you may use the `append` method:

필드의 내용을 유지한채로 문자를 추가하려면 `append` 메소드를 사용하면 됩니다:

    $browser->type('tags', 'foo')
            ->append('tags', ', bar, baz');

You may clear the value of an input using the `clear` method:

`clear` 메소드를 통해서 input 필드의 값을 비울 수 있습니다:

    $browser->clear('email');

#### Dropdowns
#### Dropdowns(셀렉트박스)

To select a value in a dropdown selection box, you may use the `select` method. Like the `type` method, the `select` method does not require a full CSS selector. When passing a value to the `select` method, you should pass the underlying option value instead of the display text:

dropdown 셀렉트 박스에서 값을 선택하려면, `select` 메소드를 사용하면 됩니다. `type` 메소드와 같이 `select` 메소드는 완전한 CSS selector 를 필요로 하지는 않습니다. `select` 메소드에 값이 전달될 때에는, 표시된 텍스트가 아니라, 선택할 값을 전달해야 된다는 것에 주의하십시오:

    $browser->select('size', 'Large');

You may select a random option by omitting the second parameter:

두번째 인자를 생략하면 랜덤한 값을 선택하게 됩니다:

    $browser->select('size');

#### Checkboxes
#### 체크박스

To "check" a checkbox field, you may use the `check` method. Like many other input related methods, a full CSS selector is not required. If an exact selector match can't be found, Dusk will search for a checkbox with a matching `name` attribute:

체크박스 필드를 "체크" 처리하려면 `check` 메소드를 사용하면 됩니다. 다른 input 관련 메소드와 같이, 완전한 CSS selector는 필요하지 않습니다. 완전히 일치하는 selector를 찾을 수 없다면 Dusk는 `name` 속성을 기준으로 매칭되는 체크박스를 찾습니다:

    $browser->check('terms');

    $browser->uncheck('terms');

#### Radio Buttons
#### 라디오 버튼

To "select" a radio button option, you may use the `radio` method. Like many other input related methods, a full CSS selector is not required. If an exact selector match can't be found, Dusk will search for a radio with matching `name` and `value` attributes:

라디오 버튼의 옵션을 "선택" 하려면, `radio` 메소드를 사용하면 됩니다. 다른 input 관련 메소드와 같이, 완전한 CSS selector는 필요하지 않습니다. 완전히 일치하는 selector를 찾을 수 없다면, Dusk는 `name` 과 `value` 속성을 기준으로 매칭되는 라디오를 찾습니다:

    $browser->radio('version', 'php7');

<a name="attaching-files"></a>
### Attaching Files
### 파일 첨부

The `attach` method may be used to attach a file to a `file` input element. Like many other input related methods, a full CSS selector is not required. If an exact selector match can't be found, Dusk will search for a file input with matching `name` attribute:

`file` input element 에 파일을 첨부하는데에는 `attach` 메소드가 사용됩니다. 다른 input 관련 메소드와 같이, 완전한 CSS selector는 필요하지 않습니다. 완전히 일치하는 selector를 찾을 수 없다면, Dusk는 `name` 속성을 기준으로 매칭되는 file input을 찾습니다:

    $browser->attach('photo', __DIR__.'/photos/me.png');

> {note} The attach function requires the `Zip` PHP extension to be installed and enabled on your server.

> {note} 파일 첨부기능을 사용하려면 서버에 `Zip` PHP 확장 기능이 설치되어 있고 활성화 되어 있어야 합니다.

<a name="using-the-keyboard"></a>
### Using The Keyboard
### 키보드 사용하기

The `keys` method allows you to provide more complex input sequences to a given element than normally allowed by the `type` method. For example, you may hold modifier keys entering values. In this example, the `shift` key will be held while `taylor` is entered into the element matching the given selector. After `taylor` is typed, `otwell` will be typed without any modifier keys:

`keys` 메소드는 `type` 메소드가 제공하는 것 보다 더 복잡한 작업들을 처리할 수 있습니다. 예를 들어, 특수 키를 값을 입력하는 동안 유지할 수 있습니다. 이 예제에서는 주어진 selector에 매칭되는 element에 `taylor`를 입력하는 동안 `shift` 키를 누르고 있는상태로 유지됩니다. `taylor` 가 입력된 후에, `otwell` 이 입력될 때는 특수키를 유지하지 않습니다:

    $browser->keys('selector', ['{shift}', 'taylor'], 'otwell');

You may even send a "hot key" to the primary CSS selector that contains your application:

애플리케이션에 포함된 기본 CSS selector에서 "단축키"를 전달 할 수도 있습니다:

    $browser->keys('.app', ['{command}', 'j']);

> {tip} All modifier keys are wrapped in `{}` characters, and match the constants defined in the `Facebook\WebDriver\WebDriverKeys` class, which can be [found on GitHub](https://github.com/facebook/php-webdriver/blob/community/lib/WebDriverKeys.php).

> {tip} 모든 특수키는 `{}` 문자로 감싸여져 있으며, 사용되는 표시자들은 `Facebook\WebDriver\WebDriverKeys` 클래스에 정의되어 있습니다. 이러한 사항은 [GitHub](https://github.com/facebook/php-webdriver/blob/community/lib/WebDriverKeys.php)에서 보다 자세한 내용을 확인할 수 있습니다.

<a name="using-the-mouse"></a>
### Using The Mouse
### 마우스 사용하기

#### Clicking On Elements
#### Element 클릭하기

The `click` method may be used to "click" on an element matching the given selector:

`click` 메소드는 주어진 selector에 매칭되는 element를 "클릭"하는데 사용합니다:

    $browser->click('.selector');

#### Mouseover
#### Mouseover

The `mouseover` method may be used when you need to move the mouse over an element matching the given selector:

`mouseover` 메소드는 주어진 selector에 매칭되는 element에 마우스를 이동하여 올려놓는 작업이 필요할 때 사용합니다:

    $browser->mouseover('.selector');

#### Drag & Drop
#### 드래그 & 드롭

The `drag` method may be used to drag an element matching the given selector to another element:

`drag` 메소드는 주어진 selector와 매칭되는 element를 다른 element로 드래그 하는데 사용합니다:

    $browser->drag('.from-selector', '.to-selector');

Or, you may drag an element in a single direction:

또는, element를 어느 한 방향으로 드래그 할 수 있습니다:

    $browser->dragLeft('.selector', 10);
    $browser->dragRight('.selector', 10);
    $browser->dragUp('.selector', 10);
    $browser->dragDown('.selector', 10);

<a name="scoping-selectors"></a>
### Scoping Selectors
### Selector의 특정 범위를 제한하여 동작하기

Sometimes you may wish to perform several operations while scoping all of the operations within a given selector. For example, you may wish to assert that some text exists only within a table and then click a button within that table. You may use the `with` method to accomplish this. All operations performed within the callback given to the `with` method will be scoped to the original selector:

때로는 주어진 selector안에서 특정 범위를 지정하여 동작을 수행하기을 원할 수도 있습니다. 예를 들어, 테이블 안에 있는 텍스트를 확인하고, 버튼을 클릭하고자 할 수 있습니다. 이 경우 `with`메소드를 사용하면 됩니다. `with` 메소드에 주어진 콜백안에서 수행되는 모든 동작들은 원래의 selector 에서 범위가 제한됩니다:

    $browser->with('.table', function ($table) {
        $table->assertSee('Hello World')
              ->clickLink('Delete');
    });

<a name="waiting-for-elements"></a>
### Waiting For Elements
### Element 기다리기

When testing applications that use JavaScript extensively, it often becomes necessary to "wait" for certain elements or data to be available before proceeding with a test. Dusk makes this a cinch. Using a variety of methods, you may wait for elements to be visible on the page or even wait until a given JavaScript expression evaluates to `true`.

자바스크립트를 많이 사용한 애플리케이션을 테스트할 때, 테스트를 수행하기 위해서 특정 element가 표시되기까지 "기다리는" 행동이 필요할 때가 있습니다. Dusk는 이를 아주 쉽게 해결합니다. 다양한 메소드를 사용하여, element가 페이지에 표시될 때까지 기다리거나, 주어진 자바스크립트 표현식이 `true` 가 될 때까지 기다릴 수 있습니다.

#### Waiting
#### 시간기준으로 기다리기

If you need to pause the test for a given number of milliseconds, use the `pause` method:

주어진 milliseconde 동안 테스트를 일시 정지하려면 `pause` 메소드를 사용합니다:

    $browser->pause(1000);

#### Waiting For Selectors
#### Selector 가 표시되기를 기다리기

The `waitFor` method may be used to pause the execution of the test until the element matching the given CSS selector is displayed on the page. By default, this will pause the test for a maximum of five seconds before throwing an exception. If necessary, you may pass a custom timeout threshold as the second argument to the method:

`waitFor` 메소드는 주어진 CSS selector 에 해당하는 element가 페이지에 표시될 때까지 테스트 실행을 일시 정지하는데 사용합니다. 기본적으로 이 동작은 exception이 발생하기 전까지 최대 5초동안 테스트를 일시 정지합니다. 필요하다면, 두번재 인자로 커스텀 타임아웃 값을 전달 할 수 있습니다:

    // Wait a maximum of five seconds for the selector...
    $browser->waitFor('.selector');

    // Wait a maximum of one second for the selector...
    $browser->waitFor('.selector', 1);

You may also wait until the given selector is missing from the page:

또한 주어진 selector가 페이지에서 사라질 때까지 일시 정지할 수도 있습니다:

    $browser->waitUntilMissing('.selector');

    $browser->waitUntilMissing('.selector', 1);

#### Scoping Selectors When Available
#### 사용가능할 때 Selector의 특정 범위를 제한하여 동작하기

Occasionally, you may wish to wait for a given selector and then interact with the element matching the selector. For example, you may wish to wait until a modal window is available and then press the "OK" button within the modal. The `whenAvailable` method may be used in this case. All element operations performed within the given callback will be scoped to the original selector:

때로는, 주어진 selector 가 사용가능해지면, 매칭되는 selector에 작업을 수행하려고 할 수도 있습니다. 예를 들면, 모달 윈도우가 나타나기를 기다렸다가, 모달의 "OK" 버튼을 누른다고 해보겠습니다. 이런 경우 `whenAvailable` 메소를 사용하면 됩니다. 모든 element 작업은 원래의 selector 로 제한되어 주어진 콜백 안에서 수행됩니다

    $browser->whenAvailable('.modal', function ($modal) {
        $modal->assertSee('Hello World')
              ->press('OK');
    });

#### Waiting For Text
#### 텍스트가 나타날 때까지 기다리기

The `waitForText` method may be used to wait until the given text is displayed on the page:

`waitForText` 메소드는 페이지에서 주어진 텍스트가 나타날때 까지 기다릴 때 사용됩니다:

    // Wait a maximum of five seconds for the text...
    $browser->waitForText('Hello World');

    // Wait a maximum of one second for the text...
    $browser->waitForText('Hello World', 1);

#### Waiting For Links
#### 링크가 나타날 때까지 기다리기

The `waitForLink` method may be used to wait until the given link text is displayed on the page:

`waitForLink` 메소드는 페이지에서 주어진 링크가 나타날때 까지 기다릴 때 사용됩니다:

    // Wait a maximum of five seconds for the link...
    $browser->waitForLink('Create');

    // Wait a maximum of one second for the link...
    $browser->waitForLink('Create', 1);

#### Waiting On The Page Location

When making a path assertion such as `$browser->assertPathIs('/home')`, the assertion can fail if `window.location.pathname` is being updated asynchronously. You may use the `waitForLocation` method to wait for the location to be a given value:

    $browser->waitForLocation('/secret');

You may also wait for a named route's location:

    $browser->waitForRoute($routeName, $parameters);

#### Waiting for Page Reloads

If you need to make assertions after a page has been reloaded, use the `waitForReload` method:

    $browser->click('.some-action')
            ->waitForReload()
            ->assertSee('something');

#### Waiting On JavaScript Expressions
#### 자바스크립트 표현식을 통해서 대기하기

Sometimes you may wish to pause the execution of a test until a given JavaScript expression evaluates to `true`. You may easily accomplish this using the `waitUntil` method. When passing an expression to this method, you do not need to include the `return` keyword or an ending semi-colon:

때로는 주어진 자바스크립트 표현식이 `true` 일 때까지 테스트 실행을 멈추고 있기를 원할 수도 있습니다. 이럴 때는 `waitUntil` 메소드를 사용하면 됩니다. 이 메소드에 표현식을 전달할 때에는 `return` 키워드나 종료 세미콜론을 포함할 필요가 없습니다:

    // Wait a maximum of five seconds for the expression to be true...
    $browser->waitUntil('App.dataLoaded');

    $browser->waitUntil('App.data.servers.length > 0');

    // Wait a maximum of one second for the expression to be true...
    $browser->waitUntil('App.data.servers.length > 0', 1);

#### Waiting With A Callback
#### Waiting With A Callback

Many of the "wait" methods in Dusk rely on the underlying `waitUsing` method. You may use this method directly to wait for a given callback to return `true`. The `waitUsing` method accepts the maximum number of seconds to wait, the interval at which the Closure should be evaluated, the Closure, and an optional failure message:

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
## Available Assertions
## 사용 가능한 Assertions

Dusk provides a variety of assertions that you may make against your application. All of the available assertions are documented in the list below:

Dusk는 애플리케이션에서 사용가능한 다양한 assertion을 제공합니다. 가능한 모든 assertions은 아래 목록과 같습니다:

[assertTitle](#assert-title)
[assertTitleContains](#assert-title-contains)
[assertUrlIs](#assert-url-is)
[assertPathBeginsWith](#assert-path-begins-with)
[assertPathIs](#assert-path-is)
[assertPathIsNot](#assert-path-is-not)
[assertRouteIs](#assert-route-is)
[assertQueryStringHas](#assert-query-string-has)
[assertQueryStringMissing](#assert-query-string-missing)
[assertFragmentIs](#assert-fragment-is)
[assertFragmentBeginsWith](#assert-fragment-begins-with)
[assertFragmentIsNot](#assert-fragment-is-not)
[assertHasCookie](#assert-has-cookie)
[assertCookieMissing](#assert-cookie-missing)
[assertCookieValue](#assert-cookie-value)
[assertPlainCookieValue](#assert-plain-cookie-value)
[assertSee](#assert-see)
[assertDontSee](#assert-dont-see)
[assertSeeIn](#assert-see-in)
[assertDontSeeIn](#assert-dont-see-in)
[assertSourceHas](#assert-source-has)
[assertSourceMissing](#assert-source-missing)
[assertSeeLink](#assert-see-link)
[assertDontSeeLink](#assert-dont-see-link)
[assertInputValue](#assert-input-value)
[assertInputValueIsNot](#assert-input-value-is-not)
[assertChecked](#assert-checked)
[assertNotChecked](#assert-not-checked)
[assertRadioSelected](#assert-radio-selected)
[assertRadioNotSelected](#assert-radio-not-selected)
[assertSelected](#assert-selected)
[assertNotSelected](#assert-not-selected)
[assertSelectHasOptions](#assert-select-has-options)
[assertSelectMissingOptions](#assert-select-missing-options)
[assertSelectHasOption](#assert-select-has-option)
[assertValue](#assert-value)
[assertVisible](#assert-visible)
[assertPresent](#assert-present)
[assertMissing](#assert-missing)
[assertDialogOpened](#assert-dialog-opened)
[assertEnabled](#assert-enabled)
[assertDisabled](#assert-disabled)
[assertFocused](#assert-focused)
[assertNotFocused](#assert-not-focused)
[assertVue](#assert-vue)
[assertVueIsNot](#assert-vue-is-not)
[assertVueContains](#assert-vue-contains)
[assertVueDoesNotContain](#assert-vue-does-not-contain)

<a name="assert-title"></a>
#### assertTitle

Assert the page title matches the given text:

페이지 타이틀이 주어진 텍스트와 일치하는지 확인:

    $browser->assertTitle($title);

<a name="assert-title-contains"></a>
#### assertTitleContains

Assert the page title contains the given text:

페이지 타이틀이 주어진 텍스트를 포함하는지 확인:

    $browser->assertTitleContains($title);

<a name="assert-url-is"></a>
#### assertUrlIs

Assert that the current URL (without the query string) matches the given string:

현재의 URL이 (쿼리 스트링을 제외하고) 주어진 문자열과 일치 하는지 확인:

    $browser->assertUrlIs($url);

<a name="assert-path-begins-with"></a>
#### assertPathBeginsWith

Assert that the current URL path begins with given path:

현재 URL 경로가 주어진 경로로 시작하는지 확인:

    $browser->assertPathBeginsWith($path);

<a name="assert-path-is"></a>
#### assertPathIs

Assert the current path matches the given path:

현재 경로가 주어진 경로와 일치하는지 확인:

    $browser->assertPathIs('/home');

<a name="assert-path-is-not"></a>
#### assertPathIsNot

Assert the current path does not match the given path:

현재 경로가 주어진 경로와 일치하지 않는 것을 확인:

    $browser->assertPathIsNot('/home');

<a name="assert-route-is"></a>
#### assertRouteIs

Assert the current URL matches the given named route's URL:

현재 URL이 주어진 이름의 라우트의 URL과 일치하는지 확인:

    $browser->assertRouteIs($name, $parameters);

<a name="assert-query-string-has"></a>
#### assertQueryStringHas

Assert the given query string parameter is present:

쿼리 스트링 파라미터가 존재하는지 확인:

    $browser->assertQueryStringHas($name);

Assert the given query string parameter is present and has a given value:

쿼리 스트링 파라미터가 존재하고 주어진 값을 가지고 있는지 확인:

    $browser->assertQueryStringHas($name, $value);

<a name="assert-query-string-missing"></a>
#### assertQueryStringMissing

Assert the given query string parameter is missing:

주어진 쿼리 스트링 파라미터가 없다는 것을 확인:

    $browser->assertQueryStringMissing($name);


<a name="assert-fragment-is"></a>
#### assertFragmentIs

Assert the current fragment matches the given fragment:

현재의 fragment 가 주어진 fragment와 일치하는지 확인:

    $browser->assertFragmentIs('anchor');

<a name="assert-fragment-begins-with"></a>
#### assertFragmentBeginsWith

Assert that the current fragment begins with given fragment:

현재의 fragment 가 주어진 fragment로 시작하는지 확인:

    $browser->assertFragmentBeginsWith('anchor');

<a name="assert-fragment-is-not"></a>
#### assertFragmentIsNot

Assert the current fragment does not match the given fragment:

현재의 fragment 가 주어진 fragment와 일치하지 않는 것을 확인:

    $browser->assertFragmentIsNot('anchor');

<a name="assert-has-cookie"></a>
#### assertHasCookie

Assert the given cookie is present:

주어진 쿠키가 존재하는지 확인:

    $browser->assertHasCookie($name);

<a name="assert-cookie-missing"></a>
#### assertCookieMissing

Assert that the given cookie is not present:

주어진 쿠키가 존재하지 않는 것을 확인:

    $browser->assertCookieMissing($name);

<a name="assert-cookie-value"></a>
#### assertCookieValue

Assert a cookie has a given value:

쿠키가 주어진 값을 가지고 있는지 확인:

    $browser->assertCookieValue($name, $value);

<a name="assert-plain-cookie-value"></a>
#### assertPlainCookieValue

Assert an unencrypted cookie has a given value:

암호화 되지 않은 쿠키가 주어진 값을 가지고 있는지 확인:

    $browser->assertPlainCookieValue($name, $value);

<a name="assert-see"></a>
#### assertSee

Assert the given text is present on the page:

현재 페이지에 주어진 텍스트가 존재하는지 확인:

    $browser->assertSee($text);

<a name="assert-dont-see"></a>
#### assertDontSee

Assert the given text is not present on the page:

현재 페이지에 주어진 텍스트가 존재하지 않는 것을 확인:

    $browser->assertDontSee($text);

<a name="assert-see-in"></a>
#### assertSeeIn

Assert the given text is present within the selector:

selector 안에서 주어진 텍스트가 존재하는지 확인:

    $browser->assertSeeIn($selector, $text);

<a name="assert-dont-see-in"></a>
#### assertDontSeeIn

Assert the given text is not present within the selector:

selector 안에서 주어진 텍스트가 존재하지 않는 것을 확인:

    $browser->assertDontSeeIn($selector, $text);

<a name="assert-source-has"></a>
#### assertSourceHas

Assert that the given source code is present on the page:

주어진 소스 코드가 현재 페이지에 존재하는지 확인:

    $browser->assertSourceHas($code);

<a name="assert-source-missing"></a>
#### assertSourceMissing

Assert that the given source code is not present on the page:

주어진 소스 코드가 현재 페이지에 존재하지 않는 것을 확인:

    $browser->assertSourceMissing($code);

<a name="assert-see-link"></a>
#### assertSeeLink

Assert the given link is present on the page:

현재 페이지에 주어진 링크가 존재하는지 확인:

    $browser->assertSeeLink($linkText);

<a name="assert-dont-see-link"></a>
#### assertDontSeeLink

Assert the given link is not present on the page:

현재 페이지에 주어진 링크가 존재하지 않는 것을 확인:

    $browser->assertDontSeeLink($linkText);

<a name="assert-input-value"></a>
#### assertInputValue

Assert the given input field has the given value:

주어진 input 필드가 주어진 값을 가지는지 확인:

    $browser->assertInputValue($field, $value);

<a name="assert-input-value-is-not"></a>
#### assertInputValueIsNot

Assert the given input field does not have the given value:

주어진 input 필드가 주어진 값을 가지고 있지 않을 것을 확인:

    $browser->assertInputValueIsNot($field, $value);

<a name="assert-checked"></a>
#### assertChecked

Assert the given checkbox is checked:

주어진 체크박스가 체크되어 있는지 확인:

    $browser->assertChecked($field);

<a name="assert-not-checked"></a>
#### assertNotChecked

Assert the given checkbox is not checked:

주어진 체크박스가 체크되어 있지 않은 것을 확인:

    $browser->assertNotChecked($field);

<a name="assert-radio-selected"></a>
#### assertRadioSelected

Assert the given radio field is selected:

주어진 라디오박스의 필드가 선택되어 있는지 확인:

    $browser->assertRadioSelected($field, $value);

<a name="assert-radio-not-selected"></a>
#### assertRadioNotSelected

Assert the given radio field is not selected:

주어진 라디오박스의 필드가 선택되어 있지 않은 것을 확인:

    $browser->assertRadioNotSelected($field, $value);

<a name="assert-selected"></a>
#### assertSelected

Assert the given dropdown has the given value selected:

주어진 드랍박스(셀렉트박스)에서 주어진 값이 선택되어 있는 것을 확인:

    $browser->assertSelected($field, $value);

<a name="assert-not-selected"></a>
#### assertNotSelected

Assert the given dropdown does not have the given value selected:

주어진 드랍박스(셀렉트박스)에서 주어진 값이 선택되어 있지 않은 것을 확인:

    $browser->assertNotSelected($field, $value);

<a name="assert-select-has-options"></a>
#### assertSelectHasOptions

Assert that the given array of values are available to be selected:

주어진 값의 배열이 선택가능한 옵션으로 제공되는지 확인:

    $browser->assertSelectHasOptions($field, $values);

<a name="assert-select-missing-options"></a>
#### assertSelectMissingOptions

Assert that the given array of values are not available to be selected:

주어진 값의 배열이 선택가능하지 않는 옵션으로 제공되는지 확인:

    $browser->assertSelectMissingOptions($field, $values);

<a name="assert-select-has-option"></a>
#### assertSelectHasOption

Assert that the given value is available to be selected on the given field:

주어진 값이 필드에서 선택 가능한 값인지 확인:

    $browser->assertSelectHasOption($field, $value);

<a name="assert-value"></a>
#### assertValue

Assert the element matching the given selector has the given value:

주어진 selector 와 매칭되는 element가 주어진 값을 가지는지 확인:

    $browser->assertValue($selector, $value);

<a name="assert-visible"></a>
#### assertVisible

Assert the element matching the given selector is visible:

주어진 selector 와 매칭되는 element가 화면에 보이는지 확인:

    $browser->assertVisible($selector);

<a name="assert-present"></a>
#### assertPresent

Assert the element matching the given selector is present:

주어진 selector 와 매칭되는 element가 존재하는지 확인:

    $browser->assertPresent($selector);

<a name="assert-missing"></a>
#### assertMissing

Assert the element matching the given selector is not visible:

주어진 selector 와 매칭되는 element가 화면에 보이지 않는 것을 확인:

    $browser->assertMissing($selector);

<a name="assert-dialog-opened"></a>
#### assertDialogOpened

Assert that a JavaScript dialog with given message has been opened:

주어진 메세지가 있는 자바스크립트 다이얼로그가 열려있는지 확인:

    $browser->assertDialogOpened($message);

<a name="assert-enabled"></a>
#### assertEnabled

Assert that the given field is enabled:

주어진 필드가 사용가능한지 확인:

    $browser->assertEnabled($field);

<a name="assert-disabled"></a>
#### assertDisabled

Assert that the given field is disabled:

주어진 필드가 사용 불가능한지 확인:

    $browser->assertDisabled($field);

<a name="assert-focused"></a>
#### assertFocused

Assert that the given field is focused:

주어진 필드에 포커스가 되었는지 확인:

    $browser->assertFocused($field);

<a name="assert-not-focused"></a>
#### assertNotFocused

Assert that the given field is not focused:

주어진 필드에 포커스가 되지 않은 것을 확인:

    $browser->assertNotFocused($field);

<a name="assert-vue"></a>
#### assertVue

Assert that a given Vue component data property matches the given value:

주어진 Vue 컴포넌트의 속성이 주어진 값과 일치하는지 확인:

    $browser->assertVue($property, $value, $componentSelector = null);

<a name="assert-vue-is-not"></a>
#### assertVueIsNot

Assert that a given Vue component data property does not match the given value:

주어진 Vue 컴포넌트의 속성이 주어진 값과 일치하지 않는 것을 확인:

    $browser->assertVueIsNot($property, $value, $componentSelector = null);

<a name="assert-vue-contains"></a>
#### assertVueContains

Assert that a given Vue component data property is an array and contains the given value:

주어진 Vue 컴포넌트의 data 프로퍼티가 배열이고 주어진 값을 포함하는지 확인:

    $browser->assertVueContains($property, $value, $componentSelector = null);

<a name="assert-vue-does-not-contain"></a>
#### assertVueDoesNotContain

Assert that a given Vue component data property is an array and does not contain the given value:

주어진 Vue 컴포넌트의 data 프로퍼티가 배열이고 주어진 값을 포함하지 않는 것을 확인:

    $browser->assertVueDoesNotContain($property, $value, $componentSelector = null);

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
## Continuous Integration
## CI - 지속적 통합

<a name="running-tests-on-circle-ci"></a>
### CircleCI
### CircleCI

#### CircleCI 1.0
#### CircleCI 1.0

If you are using CircleCI 1.0 to run your Dusk tests, you may use this configuration file as a starting point. Like TravisCI, we will use the `php artisan serve` command to launch PHP's built-in web server:

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
#### CircleCI 2.0

If you are using CircleCI 2.0 to run your Dusk tests, you may add these steps to your build:

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
### Codeship

To run Dusk tests on [Codeship](https://codeship.com), add the following commands to your Codeship project. Of course, these commands are a starting point and you are free to add additional commands as needed:

[Codeship](https://codeship.com)에서 Dusk 테스트를 실행하려면, 다음의 명령어들을 Codeship 프로젝트에 추가하십시오. 물론, 이 명령어들은 기본적인 명령어들이며, 필요한 경우 자유롭게 추가할 수 있습니다:

    phpenv local 7.1
    cp .env.testing .env
    composer install --no-interaction
    nohup bash -c "./vendor/laravel/dusk/bin/chromedriver-linux 2>&1 &"
    nohup bash -c "php artisan serve 2>&1 &" && sleep 5
    php artisan dusk


<a name="running-tests-on-heroku-ci"></a>
### Heroku CI
### Heroku CI

To run Dusk tests on [Heroku CI](https://www.heroku.com/continuous-integration), add the following Google Chrome buildpack and scripts to your Heroku `app.json` file:

[Heroku CI](https://www.heroku.com/continuous-integration)에서 Dusk 테스트를 실행한다면, Heroku의 `app.json` 파일에 다음의 구글 크롬 빌드팩과 스크립트를 추가하십시오:

    {
      "environments": {
        "test": {
          "buildpacks": [
            { "url": "heroku/php" },
            { "url": "https://github.com/heroku/heroku-buildpack-google-chrome" }
          ],
          "scripts": {
            "test-setup": "cp .env.testing .env",
            "test": "nohup bash -c './vendor/laravel/dusk/bin/chromedriver-linux > /dev/null 2>&1 &' && nohup bash -c 'php artisan serve > /dev/null 2>&1 &' && php artisan dusk"
          }
        }
      }
    }

<a name="running-tests-on-travis-ci"></a>
### Travis CI
### Travis CI

To run your Dusk tests on Travis CI, we will need to use the "sudo-enabled" Ubuntu 14.04 (Trusty) environment. Since Travis CI is not a graphical environment, we will need to take some extra steps in order to launch a Chrome browser. In addition, we will use `php artisan serve` to launch PHP's built-in web server:

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

