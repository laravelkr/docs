# Laravel Dusk
# 라라벨 Dusk

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Installation](#installation)
- [설치하기](#installation)
  - [Managing ChromeDriver Installations](#managing-chromedriver-installations)
  - [크롬 드라이버 설치 관리](#managing-chromedriver-installations)
  - [Using Other Browsers](#using-other-browsers)
  - [다른 브라우저 사용하기](#using-other-browsers)
- [Getting Started](#getting-started)
- [시작하기](#getting-started)
  - [Generating Tests](#generating-tests)
  - [테스트 클래스 생성하기](#generating-tests)
  - [Database Migrations](#migrations)
  - [데이터베이스 마이그레이션](#migrations)
  - [Running Tests](#running-tests)
  - [테스트 실행하기](#running-tests)
  - [Environment Handling](#environment-handling)
  - [구동환경 처리](#environment-handling)
- [Browser Basics](#browser-basics)
- [브라우저 기본조작](#browser-basics)
  - [Creating Browsers](#creating-browsers)
  - [브라우저 생성하기](#creating-browsers)
  - [Navigation](#navigation)
  - [Navigation](#navigation)
  - [Resizing Browser Windows](#resizing-browser-windows)
  - [브라우저 창 크기 조정](#resizing-browser-windows)
  - [Browser Macros](#browser-macros)
  - [브라우저 매크로](#browser-macros)
  - [Authentication](#authentication)
  - [인증](#authentication)
  - [Cookies](#cookies)
  - [Cookies](#cookies)
  - [Executing JavaScript](#executing-javascript)
  - [JavaScript 실행하기](#executing-javascript)
  - [Taking A Screenshot](#taking-a-screenshot)
  - [스크린샷 찍기](#taking-a-screenshot)
  - [Storing Console Output To Disk](#storing-console-output-to-disk)
  - [콘솔 출력을 디스크에 저장하기](#storing-console-output-to-disk)
  - [Storing Page Source To Disk](#storing-page-source-to-disk)
  - [페이지 소스를 디스크에 저장하기](#storing-page-source-to-disk)
- [Interacting With Elements](#interacting-with-elements)
- [Element 조작하기](#interacting-with-elements)
  - [Dusk Selectors](#dusk-selectors)
  - [Dusk 선택자](#dusk-selectors)
  - [Text, Values, & Attributes](#text-values-and-attributes)
  - [Text, Values, & Attributes](#text-values-and-attributes)
  - [Interacting With Forms](#interacting-with-forms)
  - [폼 조작하기](#interacting-with-forms)
  - [Attaching Files](#attaching-files)
  - [파일 첨부](#attaching-files)
  - [Pressing Buttons](#pressing-buttons)
  - [버튼 누르기](#pressing-buttons)
  - [Clicking Links](#clicking-links)
  - [링크 클릭](#clicking-links)
  - [Using The Keyboard](#using-the-keyboard)
  - [키보드 사용하기](#using-the-keyboard)
  - [Using The Mouse](#using-the-mouse)
  - [마우스 사용하기](#using-the-mouse)
  - [JavaScript Dialogs](#javascript-dialogs)
  - [자바스크립트 대화상자](#javascript-dialogs)
  - [Scoping Selectors](#scoping-selectors)
  - [Selector의 범위 제한](#scoping-selectors)
  - [Waiting For Elements](#waiting-for-elements)
  - [Element 기다리기](#waiting-for-elements)
  - [Scrolling An Element Into View](#scrolling-an-element-into-view)
  - [Element가 보이는 곳으로 스크롤하기](#scrolling-an-element-into-view)
- [Available Assertions](#available-assertions)
- [사용 가능한 Assertions](#available-assertions)
- [Pages](#pages)
- [페이지-Pages](#pages)
  - [Generating Pages](#generating-pages)
  - [페이지 생성하기](#generating-pages)
  - [Configuring Pages](#configuring-pages)
  - [페이지 설정하기](#configuring-pages)
  - [Navigating To Pages](#navigating-to-pages)
  - [페이지 탐색](#navigating-to-pages)
  - [Shorthand Selectors](#shorthand-selectors)
  - [단축 셀렉터](#shorthand-selectors)
  - [Page Methods](#page-methods)
  - [페이지 메소드](#page-methods)
- [Components](#components)
- [컴포넌트](#components)
  - [Generating Components](#generating-components)
  - [컴포넌트 생성하기](#generating-components)
  - [Using Components](#using-components)
  - [컴포넌트 사용하기](#using-components)
- [Continuous Integration](#continuous-integration)
- [CI - 지속적 통합](#continuous-integration)
  - [Heroku CI](#running-tests-on-heroku-ci)
  - [Heroku CI](#running-tests-on-heroku-ci)
  - [Travis CI](#running-tests-on-travis-ci)
  - [Travis CI](#running-tests-on-travis-ci)
  - [GitHub Actions](#running-tests-on-github-actions)
  - [GitHub Actions](#running-tests-on-github-actions)

<a name="introduction"></a>
## Introduction
## 시작하기

[Laravel Dusk](https://github.com/laravel/dusk) provides an expressive, easy-to-use browser automation and testing API. By default, Dusk does not require you to install JDK or Selenium on your local computer. Instead, Dusk uses a standalone [ChromeDriver](https://sites.google.com/chromium.org/driver) installation. However, you are free to utilize any other Selenium compatible driver you wish.

[라라벨 Dusk](https://github.com/laravel/dusk) 는 표현력이 풍부하고 사용하기 쉬운 브라우저 자동화 및 테스트 API를 제공합니다. 기본적으로 Dusk는 로컬 컴퓨터에 JDK 또는 Selenium을 설치할 필요가 없습니다. 대신 Dusk는 독립 실행형 [ChromeDriver](https://sites.google.com/chromium.org/driver) 를 설치해 사용합니다. 그러나 원하는 다른 Selenium 호환 드라이버를 자유롭게 사용할 수 있습니다.

<a name="installation"></a>
## Installation
## 설치하기

To get started, you should install [Google Chrome](https://www.google.com/chrome) and add the `laravel/dusk` Composer dependency to your project:

시작하려면 [Google Chrome](https://www.google.com/chrome) 을 설치하고 프로젝트에 `laravel/dusk` Composer 의존성을 추가해야 합니다.

```shell
composer require --dev laravel/dusk
```

> {note} If you are manually registering Dusk's service provider, you should **never** register it in your production environment, as doing so could lead to arbitrary users being able to authenticate with your application.

> {note} 여러분이 Dusk의 서비스 프로바이더를 수동으로 등록하는 경우, 실서버 환경에서는 **절대로** 이를 등록하면 안됩니다. 이렇게 하면 익명의 사용자가 애플리케이션의 인증을 통과할 수가 있습니다.

After installing the Dusk package, execute the `dusk:install` Artisan command. The `dusk:install` command will create a `tests/Browser` directory and an example Dusk test:

Dusk 패키지를 설치한 후 `dusk:install` Artisan 명령을 실행합니다. `dusk:install` 명령은 `tests/Browser` 디렉토리와 Dusk 테스트의 예제를 생성합니다.

```shell
php artisan dusk:install
```

Next, set the `APP_URL` environment variable in your application's `.env` file. This value should match the URL you use to access your application in a browser.

다음으로 애플리케이션의 `.env` 파일에서 `APP_URL` 환경 변수를 설정합니다. 이 값은 브라우저에서 애플리케이션에 액세스하는 데 사용하는 URL과 일치해야 합니다.

> {tip} If you are using [Laravel Sail](/docs/{{version}}/sail) to manage your local development environment, please also consult the Sail documentation on [configuring and running Dusk tests](/docs/{{version}}/sail#laravel-dusk).

> {tip} [Laravel Sail](/docs/{{version}}/sail)을 사용하여 로컬 개발 환경을 관리하는 경우, [Dusk 테스트 설정 및 실행](/docs/{{version}}/sail#laravel-dusk)에 대한 Sail 문서도 참조하세요.

<a name="managing-chromedriver-installations"></a>
### Managing ChromeDriver Installations
### 크롬 드라이버 설치 관리

If you would like to install a different version of ChromeDriver than what is included with Laravel Dusk, you may use the `dusk:chrome-driver` command:

라라벨 Dusk에 포함 된 것과 다른 버전의 ChromeDriver를 설치하려면 `dusk:chrome-driver` 명령을 사용할 수 있습니다.

```shell
# Install the latest version of ChromeDriver for your OS...
php artisan dusk:chrome-driver

# Install a given version of ChromeDriver for your OS...
php artisan dusk:chrome-driver 86

# Install a given version of ChromeDriver for all supported OSs...
php artisan dusk:chrome-driver --all

# Install the version of ChromeDriver that matches the detected version of Chrome / Chromium for your OS...
php artisan dusk:chrome-driver --detect
```

> {note} Dusk requires the `chromedriver` binaries to be executable. If you're having problems running Dusk, you should ensure the binaries are executable using the following command: `chmod -R 0755 vendor/laravel/dusk/bin/`.

> {note} Dusk 를 실행하기 위해서는 `chromedriver` 바이너리가 필요합니다. Dusk를 구동하는데 문제가 있다면, 다음 명령어를 통해서 바이너리가 실행가능해야 합니다. `chmod -R 0755 vendor/laravel/dusk/bin`.

<a name="using-other-browsers"></a>
### Using Other Browsers
### 다른 브라우저 사용하기

By default, Dusk uses Google Chrome and a standalone [ChromeDriver](https://sites.google.com/chromium.org/driver) installation to run your browser tests. However, you may start your own Selenium server and run your tests against any browser you wish.

기본적으로 Dusk는 Chrome 및 독립 실행형 [ChromeDriver](https://sites.google.com/chromium.org/driver) 설치를 사용하여 브라우저 테스트를 실행합니다. 그러나 자체 Selenium 서버를 실행하고, 원하는 브라우저에 대한 테스트를 실행할 수 있습니다.

To get started, open your `tests/DuskTestCase.php` file, which is the base Dusk test case for your application. Within this file, you can remove the call to the `startChromeDriver` method. This will stop Dusk from automatically starting the ChromeDriver:

다른 브라우저를 사용하기 위해서는, 애플리케이션의 베이스 Dusk 테스트 케이스가 되는 `tests/DuskTestCase.php` 파일을 엽니다. 이 파일안에 있는 `startChromeDriver`을 제거하면 됩니다. 이렇게 하면 Dusk가 자동으로 ChromeDriver를 시작하지 않게 됩니다.

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

Dusk 테스트를 생성하기 위해서는 `dusk:make` 아티즌 명령어를 사용합니다. 생성된 테스트 파일은 `tests/Browser` 디렉토리에 저장됩니다.

```shell
php artisan dusk:make LoginTest
```

<a name="migrations"></a>
### Database Migrations
### 데이터베이스 마이그레이션

Most of the tests you write will interact with pages that retrieve data from your application's database; however, your Dusk tests should never use the `RefreshDatabase `trait. The `RefreshDatabase` trait leverages database transactions which will not be applicable or available across HTTP requests. Instead, use the `DatabaseMigrations` trait, which re-migrates the database for each test:

작성하는 대부분의 테스트는 애플리케이션 데이터베이스에서 데이터를 검색하는 페이지와 상호 작용합니다. 그러나 Dusk 테스트는 `RefreshDatabase` 특성-trait을 사용해서는 안 됩니다. `RefreshDatabase` 특성-trait은 HTTP 요청에서 적용할 수 없거나 사용할 수 없는 데이터베이스 트랜잭션을 활용합니다. 대신, 각 테스트에 대해 데이터베이스를 다시 마이그레이션하는 `DatabaseMigrations` 특성-trait을 사용하십시오.

    <?php

    namespace Tests\Browser;

    use App\Models\User;
    use Illuminate\Foundation\Testing\DatabaseMigrations;
    use Laravel\Dusk\Chrome;
    use Tests\DuskTestCase;

    class ExampleTest extends DuskTestCase
    {
        use DatabaseMigrations;
    }

> {note} SQLite in-memory databases may not be used when executing Dusk tests. Since the browser executes within its own process, it will not be able to access the in-memory databases of other processes.

> {note} Dusk 테스트를 실행할 때 SQLite 인메모리 데이터베이스를 사용할 수 없습니다. 브라우저는 자체 프로세스 내에서 실행되기 때문에 다른 프로세스의 메모리 내 데이터베이스에 액세스할 수 없습니다.

<a name="running-tests"></a>
### Running Tests
### 테스트 실행하기

To run your browser tests, execute the `dusk` Artisan command:

브라우저 테스트를 실행하려면 `dusk` Artisan 명령을 실행하세요:

```shell
php artisan dusk
```

If you had test failures the last time you ran the `dusk` command, you may save time by re-running the failing tests first using the `dusk:fails` command:

마지막으로 `dusk` 명령을 실행했을 때 테스트가 실패했었다면, `dusk:fails` 명령을 사용하여 이전에 실패한 테스트를 다시 실행하여 시간을 아낄 수 있습니다.

```shell
php artisan dusk:fails
```

The `dusk` command accepts any argument that is normally accepted by the PHPUnit test runner, such as allowing you to only run the tests for a given [group](https://phpunit.de/manual/current/en/appendixes.annotations.html#appendixes.annotations.group):

`dusk` 명령은 지정된 [그룹](https://phpunit.de/manual/current/en/appendixes.annotations.html#appendixes.annotations.group) 에 대한 테스트만 실행할 수 있도록 입력받는 것처럼, PHPUnit의 일반적으로 입력받는 모든 인수를 입력받습니다.

```shell
php artisan dusk --group=foo
```

> {tip} If you are using [Laravel Sail](/docs/{{version}}/sail) to manage your local development environment, please consult the Sail documentation on [configuring and running Dusk tests](/docs/{{version}}/sail#laravel-dusk).

> {tip} 로컬 개발 환경을 관리하기 위해 [Laravel Sail](/docs/{{version}}/sail)을 사용하는 경우 [Dusk 테스트 설정 및 실행](/docs/{{ version}}/sail#laravel-dusk).

<a name="manually-starting-chromedriver"></a>
#### Manually Starting ChromeDriver
#### 수동으로 ChromeDriver 시작하기

By default, Dusk will automatically attempt to start ChromeDriver. If this does not work for your particular system, you may manually start ChromeDriver before running the `dusk` command. If you choose to start ChromeDriver manually, you should comment out the following line of your `tests/DuskTestCase.php` file:

기본적으로는 Dusk가 자동으로 ChromeDriver를 시작합니다. 만약 특정 시스템에서 ChromeDriver가 동작하지 않으면 `dusk` 명령어를 실행하기 전에 수동으로 ChromeDriver를 시작해야 합니다. 수동으로 ChromeDriver를 시작하려면, `tests/DuskTestCase.php` 파일의 다음 라인을 주석으로 표시해야합니다.

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

In addition, if you start ChromeDriver on a port other than 9515, you should modify the `driver` method of the same class to reflect the correct port:

또한 9515 이외의 포트에서 ChromeDriver를 시작하는 경우, 올바른 포트를 반영하도록 동일한 클래스의 `driver` 메서드를 수정해야 합니다.

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

<a name="browser-basics"></a>
## Browser Basics
## 브라우저 기본조작

<a name="creating-browsers"></a>
### Creating Browsers
### 브라우저 생성하기

To get started, let's write a test that verifies we can log into our application. After generating a test, we can modify it to navigate to the login page, enter some credentials, and click the "Login" button. To create a browser instance, you may call the `browse` method from within your Dusk test:

애플리케이션에 로그인할 수 있는지 확인하는 테스트를 작성해 보는 것으로 시작해보겠습니다. 테스트를 생성한 후 이를 수정하여 로그인 페이지로 이동하고 자격 증명을 입력하고 "로그인" 버튼을 클릭할 수 있습니다. 브라우저 인스턴스를 생성하려면 Dusk 테스트 내에서 `browse` 메소드를 호출할 수 있습니다.

    <?php

    namespace Tests\Browser;

    use App\Models\User;
    use Illuminate\Foundation\Testing\DatabaseMigrations;
    use Laravel\Dusk\Chrome;
    use Tests\DuskTestCase;

    class ExampleTest extends DuskTestCase
    {
        use DatabaseMigrations;

        /**
         * A basic browser test example.
         *
         * @return void
         */
        public function test_basic_example()
        {
            $user = User::factory()->create([
                'email' => 'taylor@laravel.com',
            ]);

            $this->browse(function ($browser) use ($user) {
                $browser->visit('/login')
                        ->type('email', $user->email)
                        ->type('password', 'password')
                        ->press('Login')
                        ->assertPathIs('/home');
            });
        }
    }

As you can see in the example above, the `browse` method accepts a closure. A browser instance will automatically be passed to this closure by Dusk and is the main object used to interact with and make assertions against your application.

위의 예에서 볼 수 있듯이 `browse` 메소드는 클로저를 입력받습니다. 브라우저 인스턴스는 Dusk에 의해 이 클로저에 자동으로 전달되며 애플리케이션과 상호 작용하고 애플리케이션에 대해 검증하는 데 사용되는 기본 개체입니다.

<a name="creating-multiple-browsers"></a>
#### Creating Multiple Browsers
#### 여러개의 브라우저 생성하기

Sometimes you may need multiple browsers in order to properly carry out a test. For example, multiple browsers may be needed to test a chat screen that interacts with websockets. To create multiple browsers, simply add more browser arguments to the signature of the closure given to the `browse` method:

때때로 테스트를 제대로 수행하기 위해 여러 브라우저가 필요할 수 있습니다. 예를 들어 웹 소켓과 상호 작용하는 채팅 화면을 테스트하려면 여러 브라우저가 필요할 수 있습니다. 여러 브라우저를 만들려면 `browse` 메서드에 제공된 클로저 서명에 브라우저 인수를 더 추가하기만 하면 됩니다.

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

<a name="navigation"></a>
### Navigation
### Navigation

The `visit` method may be used to navigate to a given URI within your application:

`visit` 메소드는 애플리케이션 내에서 주어진 URI로 이동하는 데 사용할 수 있습니다.

    $browser->visit('/login');

You may use the `visitRoute` method to navigate to a [named route](/docs/{{version}}/routing#named-routes):

`visitRoute` 메소드를 사용하여 [named route](/docs/{{version}}/routing#named-routes)로 이동할 수 있습니다.

    $browser->visitRoute('login');

You may navigate "back" and "forward" using the `back` and `forward` methods:

`back` 및 `forward` 방법을 사용하여 "back" 및 "forward"를 탐색할 수 있습니다.

    $browser->back();

    $browser->forward();

You may use the `refresh` method to refresh the page:

`refresh` 메서드를 사용하여 페이지를 새로 고칠 수 있습니다.

    $browser->refresh();

<a name="resizing-browser-windows"></a>
### Resizing Browser Windows
### 브라우저 창 크기 조정

You may use the `resize` method to adjust the size of the browser window:

브라우저의 윈도우 사이즈를 조정하려면 `resize` 메소드를 사용하면 됩니다.

    $browser->resize(1920, 1080);

The `maximize` method may be used to maximize the browser window:

`maximize` 메소드는 브라우저의 윈도우를 최대 크기로 조정하는데 사용합니다.

    $browser->maximize();

The `fitContent` method will resize the browser window to match the size of its content:

`fitContent` 메서드는 콘텐츠 크기에 맞게 브라우저 창의 크기를 조정합니다.

    $browser->fitContent();

When a test fails, Dusk will automatically resize the browser to fit the content prior to taking a screenshot. You may disable this feature by calling the `disableFitOnFailure` method within your test:

테스트에 실패하면 Dusk는 스크린 샷을 만들기 전에 콘텐츠에 맞게 브라우저의 크기를 자동으로 조정합니다. 테스트 내에서 `disableFitOnFailure` 메소드를 호출하여 이 기능을 비활성화 할 수 있습니다.

    $browser->disableFitOnFailure();

You may use the `move` method to move the browser window to a different position on your screen:

`move` 메소드를 사용하여 브라우저 창을 화면의 다른 위치로 이동할 수 있습니다.

    $browser->move($x = 100, $y = 100);

<a name="browser-macros"></a>
### Browser Macros
### 브라우저 매크로

If you would like to define a custom browser method that you can re-use in a variety of your tests, you may use the `macro` method on the `Browser` class. Typically, you should call this method from a [service provider's](/docs/{{version}}/providers) `boot` method:

다양한 테스트에서 재사용할 수 있는 커스텀 브라우저 메소드를 정의한려고 한다면 `Browser` 클래스의 `macro` 메소드를 사용할 수 있습니다. 일반적으로 이 메소드는 [서비스 프로바이더](/docs/{{version}}/providers)의 `boot` 메소드 안에서 호출해야 합니다.

    <?php

    namespace App\Providers;

    use Illuminate\Support\ServiceProvider;
    use Laravel\Dusk\Browser;

    class DuskServiceProvider extends ServiceProvider
    {
        /**
         * Register Dusk's browser macros.
         *
         * @return void
         */
        public function boot()
        {
            Browser::macro('scrollToElement', function ($element = null) {
                $this->script("$('html, body').animate({ scrollTop: $('$element').offset().top }, 0);");

                return $this;
            });
        }
    }

The `macro` function accepts a name as its first argument, and a closure as its second. The macro's closure will be executed when calling the macro as a method on a `Browser` instance:

`macro` 함수는 이름을 첫 번째 인수로 받아들이고 클로저를 두 번째 인수로 받아들입니다. 매크로의 클로저는 `Browser` 인스턴스의 메소드로 매크로를 호출할 때 실행됩니다.

    $this->browse(function ($browser) use ($user) {
        $browser->visit('/pay')
                ->scrollToElement('#credit-card-details')
                ->assertSee('Enter Credit Card Details');
    });

<a name="authentication"></a>
### Authentication
### 인증

Often, you will be testing pages that require authentication. You can use Dusk's `loginAs` method in order to avoid interacting with your application's login screen during every test. The `loginAs` method accepts a primary key associated with your authenticatable model or an authenticatable model instance:

종종 인증이 필요한 페이지를 테스트하게 됩니다. 모든 테스트 중에 애플리케이션의 로그인 화면과 상호 작용하는 것을 피하기 위해 Dusk의 `loginAs` 메소드를 사용할 수 있습니다. `loginAs` 메소드는 인증 가능한 모델 또는 인증 가능한 모델 인스턴스와 연결된 기본 키를 입력받습니다.

    use App\Models\User;

    $this->browse(function ($browser) {
        $browser->loginAs(User::find(1))
              ->visit('/home');
    });

> {note} After using the `loginAs` method, the user session will be maintained for all tests within the file.

> {note} `loginAs` 메소드를 사용하면, 파일 내의 모든 테스트에서 사용자 세션이 유지됩니다.

<a name="cookies"></a>
### Cookies
### Cookies

You may use the `cookie` method to get or set an encrypted cookie's value. By default, all of the cookies created by Laravel are encrypted:

`cookie` 메소드를 사용하여 암호화된 쿠키의 값을 가져오거나 설정할 수 있습니다. 기본적으로 라라벨이 생성한 모든 쿠키는 암호화됩니다.

    $browser->cookie('name');

    $browser->cookie('name', 'Taylor');

You may use the `plainCookie` method to get or set an unencrypted cookie's value:

암호화되지 않은 쿠키의 값을 가져 오거나 설정하기 위해서는 `plainCookie` 메소드를 사용할 수 있습니다.

    $browser->plainCookie('name');

    $browser->plainCookie('name', 'Taylor');

You may use the `deleteCookie` method to delete the given cookie:

쿠키를 삭제하기 위해 `deleteCookie` 메소드를 사용할 수 있습니다.

    $browser->deleteCookie('name');

<a name="executing-javascript"></a>
### Executing JavaScript
### JavaScript 실행하기

You may use the `script` method to execute arbitrary JavaScript statements within the browser:

`script` 메서드를 사용하여 브라우저 내에서 임의의 JavaScript 문을 실행할 수 있습니다.

    $browser->script('document.documentElement.scrollTop = 0');

    $browser->script([
        'document.body.scrollTop = 0',
        'document.documentElement.scrollTop = 0',
    ]);

    $output = $browser->script('return window.location.pathname');

<a name="taking-a-screenshot"></a>
### Taking A Screenshot
### 스크린 샷 찍기

You may use the `screenshot` method to take a screenshot and store it with the given filename. All screenshots will be stored within the `tests/Browser/screenshots` directory:

`screenshot` 메서드를 사용하여 스크린 샷을 찍고 주어진 파일 이름으로 저장할 수 있습니다. 모든 스크린 샷은 `tests/Browser/screenshots` 디렉토리에 저장됩니다.

    $browser->screenshot('filename');

<a name="storing-console-output-to-disk"></a>
### Storing Console Output To Disk
### 콘솔 출력을 디스크에 저장하기

You may use the `storeConsoleLog` method to write the current browser's console output to disk with the given filename. Console output will be stored within the `tests/Browser/console` directory:

`storeConsoleLog` 메소드를 사용하여 현재 브라우저의 콘솔 출력을 주어진 파일 이름으로 디스크에 쓸 수 있습니다. 콘솔 출력은 `tests/Browser/console` 디렉토리에 저장됩니다.

    $browser->storeConsoleLog('filename');

<a name="storing-page-source-to-disk"></a>
### Storing Page Source To Disk
### 페이지 소스를 디스크에 저장하기

You may use the `storeSource` method to write the current page's source to disk with the given filename. The page source will be stored within the `tests/Browser/source` directory:

`storeSource` 메소드를 사용하여 현재 페이지의 소스를 주어진 파일 이름으로 디스크에 쓸 수 있습니다. 페이지 소스는 `tests/Browser/source` 디렉토리에 저장됩니다.

    $browser->storeSource('filename');

<a name="interacting-with-elements"></a>
## Interacting With Elements
## Element 조작하기

<a name="dusk-selectors"></a>
### Dusk Selectors
### Dusk 선택자

Choosing good CSS selectors for interacting with elements is one of the hardest parts of writing Dusk tests. Over time, frontend changes can cause CSS selectors like the following to break your tests:

엘리먼츠와 상호 작용할 수있는 좋은 CSS 선택자를 선택하는 것은 Dusk 테스트를 작성하면서 가장 어려운 부분 중 하나입니다. 시간이 지나면 프론트엔드 변경으로 인해 다음과 같은 CSS 선택자가 테스트를 중단시킬 수 있습니다.

    // HTML...

    <button>Login</button>

    // Test...

    $browser->click('.login-page .container div > button');

Dusk selectors allow you to focus on writing effective tests rather than remembering CSS selectors. To define a selector, add a `dusk` attribute to your HTML element. Then, when interacting with a Dusk browser, prefix the selector with `@` to manipulate the attached element within your test:

Dusk 선택자를 사용하면 CSS 선택자를 외우는 대신 효과적인 테스트 작성에 집중할 수 있습니다. 선택자를 정의하려면 HTML 요소에 `dusk` 속성을 추가하세요. 그런 다음 Dusk 브라우저와 상호 작용할 때 선택자에 `@` 접두사를 붙여 테스트 내에서 연결된 요소를 조작합니다.

    // HTML...

    <button dusk="login-button">Login</button>

    // Test...

    $browser->click('@login-button');

<a name="text-values-and-attributes"></a>
### Text, Values, & Attributes
### Text, Values, & Attributes

<a name="retrieving-setting-values"></a>
#### Retrieving & Setting Values
#### 값 검색 및 설정

Dusk provides several methods for interacting with the current value, display text, and attributes of elements on the page. For example, to get the "value" of an element that matches a given CSS or Dusk selector, use the `value` method:

Dusk는 현재 값, 표시 텍스트 및 페이지 요소의 속성과 상호 작용하기 위한 여러 메서드를 제공합니다. 예를 들어, 주어진 CSS 또는 Dusk 선택자와 일치하는 요소의 "값"을 얻으려면 `value` 메소드를 사용하십시오.

    // Retrieve the value...
    $value = $browser->value('selector');

    // Set the value...
    $browser->value('selector', 'value');

You may use the `inputValue` method to get the "value" of an input element that has a given field name:

주어진 필드 이름을 가진 input element의 "값"을 얻기 위해 `inputValue` 메소드를 사용할 수 있습니다.

    $value = $browser->inputValue('field');

<a name="retrieving-text"></a>
#### Retrieving Text
#### 텍스트 조회하기

The `text` method may be used to retrieve the display text of an element that matches the given selector:

`text` 메소드는 주어진 selector 와 매칭되는 element의 텍스트를 조회하는데 사용됩니다.

    $text = $browser->text('selector');

<a name="retrieving-attributes"></a>
#### Retrieving Attributes
#### 속성 조회하기

Finally, the `attribute` method may be used to retrieve the value of an attribute of an element matching the given selector:

마지막으로 `attribute` 메소드를 사용하여 주어진 선택자와 일치하는 요소의 속성 값을 검색할 수 있습니다.

    $attribute = $browser->attribute('selector', 'value');

<a name="interacting-with-forms"></a>
### Interacting With Forms
### 폼 조작하기

<a name="typing-values"></a>
#### Typing Values
#### 값 입력하기

Dusk provides a variety of methods for interacting with forms and input elements. First, let's take a look at an example of typing text into an input field:

Dusk는 form 과 element와 상호작용할 수 있는 다양한 메소드를 제공합니다. 먼저, input 필드에 값을 입력하는 예쩨를 살펴보겠습니다.

    $browser->type('email', 'taylor@laravel.com');

Note that, although the method accepts one if necessary, we are not required to pass a CSS selector into the `type` method. If a CSS selector is not provided, Dusk will search for an `input` or `textarea` field with the given `name` attribute.

메서드는 필요한 경우 이름만 입력하고, CSS 선택자를 `type` 메서드에 전달할 필요는 없습니다. CSS 선택자가 제공되지 않으면 Dusk는 주어진 `name` 속성으로 `input` 또는 `textarea` 필드를 검색합니다.

To append text to a field without clearing its content, you may use the `append` method:

필드의 내용을 유지한채로 문자를 추가하려면 `append` 메소드를 사용하면 됩니다.

    $browser->type('tags', 'foo')
            ->append('tags', ', bar, baz');

You may clear the value of an input using the `clear` method:

`clear` 메소드를 통해서 input 필드의 값을 비울 수 있습니다.

    $browser->clear('email');

You can instruct Dusk to type slowly using the `typeSlowly` method. By default, Dusk will pause for 100 milliseconds between key presses. To customize the amount of time between key presses, you may pass the appropriate number of milliseconds as the third argument to the method:

`typeSlowly` 메서드를 사용하여 Dusk에게 천천히 입력하도록 지시할 수 있습니다. 기본적으로 Dusk는 키 누름 사이에 100밀리초 동안 일시 중지됩니다. 키 누름 사이의 시간을 사용자 지정하려면 메서드에 대한 세 번째 인수로 적절한 밀리초 수를 전달할 수 있습니다.

    $browser->typeSlowly('mobile', '+1 (202) 555-5555');

    $browser->typeSlowly('mobile', '+1 (202) 555-5555', 300);

You may use the `appendSlowly` method to append text slowly:

`appendSlowly` 메소드를 사용하여 텍스트를 천천히 추가 할 수 있습니다.

    $browser->type('tags', 'foo')
            ->appendSlowly('tags', ', bar, baz');

<a name="dropdowns"></a>
#### Dropdowns
#### Dropdowns(셀렉트박스)

To select a value available on a `select` element, you may use the `select` method. Like the `type` method, the `select` method does not require a full CSS selector. When passing a value to the `select` method, you should pass the underlying option value instead of the display text:

`select` 요소에서 사용 가능한 값을 선택하려면 `select` 메서드를 사용할 수 있습니다. `type` 메소드와 마찬가지로 `select` 메소드에는 전체 CSS 선택자가 필요하지 않습니다. `select` 메소드에 값을 전달할 때 표시 텍스트 대신 기본 옵션 값을 전달해야 합니다.

    $browser->select('size', 'Large');

You may select a random option by omitting the second argument:

두 번째 인수를 생략하여 임의의 옵션을 선택할 수 있습니다.

    $browser->select('size');

By providing an array as the second argument to the `select` method, you can instruct the method to select multiple options:

`select` 메소드에 대한 두 번째 인수로 배열을 제공하면 메소드에 여러 옵션을 선택하도록 지시할 수 있습니다.

    $browser->select('categories', ['Art', 'Music']);

<a name="checkboxes"></a>
#### Checkboxes
#### 체크박스

To "check" a checkbox input, you may use the `check` method. Like many other input related methods, a full CSS selector is not required. If a CSS selector match can't be found, Dusk will search for a checkbox with a matching `name` attribute:

체크박스 입력을 "확인"하려면 `check` 메서드를 사용할 수 있습니다. 다른 많은 입력 관련 메서드과 마찬가지로 전체 CSS 선택자가 필요하지 않습니다. CSS 선택자 일치 항목을 찾을 수 없으면, Dusk는 일치하는 `name` 속성이 있는 체크박스를 검색합니다.

    $browser->check('terms');

The `uncheck` method may be used to "uncheck" a checkbox input:

`uncheck` 메소드는 체크박스 입력을 "선택 해제"하는 데 사용할 수 있습니다.

    $browser->uncheck('terms');

<a name="radio-buttons"></a>
#### Radio Buttons
#### 라디오 버튼

To "select" a `radio` input option, you may use the `radio` method. Like many other input related methods, a full CSS selector is not required. If a CSS selector match can't be found, Dusk will search for a `radio` input with matching `name` and `value` attributes:

`radio` 입력 옵션을 "선택"하려면 `radio` 메서드를 사용할 수 있습니다. 다른 많은 입력 관련 방법과 마찬가지로 전체 CSS 선택자가 필요하지 않습니다. CSS 선택자 일치 항목을 찾을 수 없으면 Dusk는 `name` 및 `value` 속성이 일치하는 `radio` 입력을 검색합니다.

    $browser->radio('size', 'large');

<a name="attaching-files"></a>
### Attaching Files
### 파일 첨부

The `attach` method may be used to attach a file to a `file` input element. Like many other input related methods, a full CSS selector is not required. If a CSS selector match can't be found, Dusk will search for a `file` input with a matching `name` attribute:

`attach` 메소드는 파일을 `file` 입력 요소에 첨부하는 데 사용할 수 있습니다. 다른 많은 입력 관련 방법과 마찬가지로 전체 CSS 선택자가 필요하지 않습니다. CSS 선택자 일치 항목을 찾을 수 없으면 Dusk는 일치하는 `name` 속성이 있는 `file` 입력을 검색합니다.

    $browser->attach('photo', __DIR__.'/photos/mountains.png');

> {note} The attach function requires the `Zip` PHP extension to be installed and enabled on your server.

> {note} 파일 첨부기능을 사용하려면 서버에 `Zip` PHP 확장 기능이 설치되어 있고 활성화 되어 있어야 합니다.

<a name="pressing-buttons"></a>
### Pressing Buttons
### 버튼 누르기

The `press` method may be used to click a button element on the page. The first argument given to the `press` method may be either the display text of the button or a CSS / Dusk selector:

`press` 메서드는 페이지의 버튼 요소를 클릭하는 데 사용할 수 있습니다. `press` 메서드에 지정된 첫 번째 인수는 버튼의 표시 텍스트 또는 CSS Dusk 선택자일 수 있습니다.

    $browser->press('Login');

When submitting forms, many application's disable the form's submission button after it is pressed and then re-enable the button when the form submission's HTTP request is complete. To press a button and wait for the button to be re-enabled, you may use the `pressAndWaitFor` method:

양식을 제출할 때 많은 애플리케이션이 양식 제출 버튼을 누른 후 비활성화했다가 양식 제출의 HTTP 요청이 완료되면 버튼을 다시 활성화합니다. 버튼을 누르고 버튼이 다시 활성화될 때까지 기다리려면 `pressAndWaitFor` 메서드를 사용할 수 있습니다.

    // Press the button and wait a maximum of 5 seconds for it to be enabled...
    $browser->pressAndWaitFor('Save');

    // Press the button and wait a maximum of 1 second for it to be enabled...
    $browser->pressAndWaitFor('Save', 1);

<a name="clicking-links"></a>
### Clicking Links
### 링크 클릭

To click a link, you may use the `clickLink` method on the browser instance. The `clickLink` method will click the link that has the given display text:

링크를 클릭하려면 브라우저 인스턴스에서 `clickLink` 메서드를 사용할 수 있습니다. `clickLink` 메소드는 주어진 표시 텍스트가 있는 링크를 클릭합니다.

    $browser->clickLink($linkText);

You may use the `seeLink` method to determine if a link with the given display text is visible on the page:

`seeLink` 메서드를 사용하여 주어진 표시 텍스트가 있는 링크가 페이지에 표시되는지 확인할 수 있습니다.

    if ($browser->seeLink($linkText)) {
        // ...
    }

> {note} These methods interact with jQuery. If jQuery is not available on the page, Dusk will automatically inject it into the page so it is available for the test's duration.

> {note} 이 메소드는 jQuery와 상호 작용합니다. 페이지에서 jQuery를 사용할 수 없는 경우 Dusk는 테스트 기간 동안 사용할 수 있도록 페이지에 자동으로 삽입합니다.

<a name="using-the-keyboard"></a>
### Using The Keyboard
### 키보드 사용하기

The `keys` method allows you to provide more complex input sequences to a given element than normally allowed by the `type` method. For example, you may instruct Dusk to hold modifier keys while entering values. In this example, the `shift` key will be held while `taylor` is entered into the element matching the given selector. After `taylor` is typed, `swift` will be typed without any modifier keys:

`keys` 메소드를 사용하면 `type` 메소드에서 일반적으로 허용하는 것보다 더 복잡한 입력 시퀀스를 주어진 요소에 제공할 수 있습니다. 예를 들어 Dusk에 값을 입력하는 동안 보조 키를 유지하도록 지시할 수 있습니다. 이 예에서 `taylor`가 주어진 선택자와 일치하는 요소에 입력되는 동안 `shift` 키가 유지됩니다. `taylor`를 입력한 후 `swift`는 수식 키 없이 입력됩니다.

    $browser->keys('selector', ['{shift}', 'taylor'], 'swift');

Another valuable use case for the `keys` method is sending a "keyboard shortcut" combination to the primary CSS selector for your application:

`keys` 메소드의 또 다른 유용한 사용 사례는 "키보드 단축키" 조합을 애플리케이션의 기본 CSS 선택자로 보내는 것입니다.

    $browser->keys('.app', ['{command}', 'j']);

> {tip} All modifier keys such as `{command}` are wrapped in `{}` characters, and match the constants defined in the `Facebook\WebDriver\WebDriverKeys` class, which can be [found on GitHub](https://github.com/php-webdriver/php-webdriver/blob/master/lib/WebDriverKeys.php).

> {tip} `{command}`와 같은 모든 수식 키는 `{}` 문자로 래핑되며, [GitHub에서 찾을 수 있는] `Facebook\WebDriver\WebDriverKeys` 클래스에 정의된 상수와 일치합니다(https: github.comphp-webdriverphp-webdriverblobmasterlibWebDriverKeys.php).

<a name="using-the-mouse"></a>
### Using The Mouse
### 마우스 사용하기

<a name="clicking-on-elements"></a>
#### Clicking On Elements
#### Element 클릭하기

The `click` method may be used to click on an element matching the given CSS or Dusk selector:

`click` 메소드는 주어진 CSS 또는 Dusk 선택자와 일치하는 요소를 클릭하는 데 사용할 수 있습니다.

    $browser->click('.selector');

The `clickAtXPath` method may be used to click on an element matching the given XPath expression:

`clickAtXPath` 메소드는 주어진 XPath 표현식과 일치하는 요소를 클릭하는 데 사용할 수 있습니다.

    $browser->clickAtXPath('//div[@class = "selector"]');

The `clickAtPoint` method may be used to click on the topmost element at a given pair of coordinates relative to the viewable area of the browser:

`clickAtPoint` 메소드는 브라우저의 볼 수 있는 영역을 기준으로 주어진 좌표 쌍에서 최상위 요소를 클릭하는 데 사용할 수 있습니다.

    $browser->clickAtPoint($x = 0, $y = 0);

The `doubleClick` method may be used to simulate the double click of a mouse:

`doubleClick` 메서드를 사용하여 마우스 더블 클릭을 시뮬레이션할 수 있습니다.

    $browser->doubleClick();

The `rightClick` method may be used to simulate the right click of a mouse:

`rightClick` 메서드를 사용하여 마우스 오른쪽 클릭을 시뮬레이션할 수 있습니다.

    $browser->rightClick();

    $browser->rightClick('.selector');

The `clickAndHold` method may be used to simulate a mouse button being clicked and held down. A subsequent call to the `releaseMouse` method will undo this behavior and release the mouse button:

`clickAndHold` 메서드는 마우스 버튼을 클릭하고 누르고 있는 것을 시뮬레이션하는 데 사용할 수 있습니다. 후속 호출로 `releaseMouse` 메서드를 사용하면 이 동작을 중단하고 마우스 버튼을 놓습니다.

    $browser->clickAndHold()
            ->pause(1000)
            ->releaseMouse();

<a name="mouseover"></a>
#### Mouseover
#### Mouseover

The `mouseover` method may be used when you need to move the mouse over an element matching the given CSS or Dusk selector:

주어진 CSS 또는 Dusk 선택자와 일치하는 요소 위로 마우스를 이동해야 할 때 `mouseover` 메소드를 사용할 수 있습니다.

    $browser->mouseover('.selector');

<a name="drag-drop"></a>
#### Drag & Drop
#### 드래그 & 드롭

The `drag` method may be used to drag an element matching the given selector to another element:

`drag` 메소드는 주어진 selector와 매칭되는 element를 다른 element로 드래그 하는데 사용합니다.

    $browser->drag('.from-selector', '.to-selector');

Or, you may drag an element in a single direction:

또는, element를 어느 한 방향으로 드래그 할 수 있습니다.

    $browser->dragLeft('.selector', $pixels = 10);
    $browser->dragRight('.selector', $pixels = 10);
    $browser->dragUp('.selector', $pixels = 10);
    $browser->dragDown('.selector', $pixels = 10);

Finally, you may drag an element by a given offset:

마지막으로 주어진 오프셋으로 요소를 드래그 할 수 있습니다.

    $browser->dragOffset('.selector', $x = 10, $y = 10);

<a name="javascript-dialogs"></a>
### JavaScript Dialogs
### 자바스크립트 대화상자

Dusk provides various methods to interact with JavaScript Dialogs. For example, you may use the `waitForDialog` method to wait for a JavaScript dialog to appear. This method accepts an optional argument indicating how many seconds to wait for the dialog to appear:

Dusk는 JavaScript 대화 상자와 상호 작용하는 다양한 메서드를 제공합니다. 예를 들어 `waitForDialog` 메서드를 사용하여 JavaScript 대화 상자가 나타날 때까지 기다릴 수 있습니다. 이 메서드는 대화 상자가 나타날 때까지 몇 초를 기다려야 하는지를 나타내는 선택적 인수를 허용합니다.

    $browser->waitForDialog($seconds = null);

The `assertDialogOpened` method may be used to assert that a dialog has been displayed and contains the given message:

`assertDialogOpened` 메소드는 대화 상자가 표시되고 주어진 메시지를 포함하는지 확인하는 데 사용할 수 있습니다.

    $browser->assertDialogOpened('Dialog message');

If the JavaScript dialog contains a prompt, you may use the `typeInDialog` method to type a value into the prompt:

JavaScript 대화 상자에 프롬프트가 포함되어 있으면 `typeInDialog` 메서드를 사용하여 프롬프트에 값을 입력할 수 있습니다.

    $browser->typeInDialog('Hello World');

To close an open JavaScript dialog by clicking the "OK" button, you may invoke the `acceptDialog` method:

"확인" 버튼을 클릭하여 열려 있는 JavaScript 대화 상자를 닫으려면 `acceptDialog` 메서드를 호출할 수 있습니다.

    $browser->acceptDialog();

To close an open JavaScript dialog by clicking the "Cancel" button, you may invoke the `dismissDialog` method:

"취소" 버튼을 클릭하여 열려 있는 JavaScript 대화 상자를 닫으려면 `dismissDialog` 메서드를 호출할 수 있습니다.

    $browser->dismissDialog();

<a name="scoping-selectors"></a>
### Scoping Selectors
### Selector의 범위 제한

Sometimes you may wish to perform several operations while scoping all of the operations within a given selector. For example, you may wish to assert that some text exists only within a table and then click a button within that table. You may use the `with` method to accomplish this. All operations performed within the closure given to the `with` method will be scoped to the original selector:

주어진 선택자 내에서 모든 작업의 범위를 지정하는 동안 여러 작업을 수행하려는 경우가 있습니다. 예를 들어, 일부 텍스트가 테이블 내에만 존재한다고 검증한 다음 해당 테이블 내에서 버튼을 클릭할 수 있습니다. 이를 수행하기 위해 `with` 메소드를 사용할 수 있습니다. `with` 메소드에 제공된 클로저 내에서 수행되는 모든 작업은 원래 선택자로 범위가 지정됩니다.

    $browser->with('.table', function ($table) {
        $table->assertSee('Hello World')
              ->clickLink('Delete');
    });

You may occasionally need to execute assertions outside of the current scope. You may use the `elsewhere` and `elsewhereWhenAvailable` methods to accomplish this:

때때로 현재 범위 외부에서 어설션을 실행해야 할 수도 있습니다. 이를 위해 `elsewhere` 및 `elsewhereWhenAvailable` 메소드를 사용할 수 있습니다.

     $browser->with('.table', function ($table) {
        // Current scope is `body .table`...

        $browser->elsewhere('.page-title', function ($title) {
            // Current scope is `body .page-title`...
            $title->assertSee('Hello World');
        });

        $browser->elsewhereWhenAvailable('.page-title', function ($title) {
            // Current scope is `body .page-title`...
            $title->assertSee('Hello World');
        });
     });

<a name="waiting-for-elements"></a>
### Waiting For Elements
### Element 기다리기

When testing applications that use JavaScript extensively, it often becomes necessary to "wait" for certain elements or data to be available before proceeding with a test. Dusk makes this a cinch. Using a variety of methods, you may wait for elements to become visible on the page or even wait until a given JavaScript expression evaluates to `true`.

JavaScript를 광범위하게 사용하는 애플리케이션을 테스트할 때 테스트를 진행하기 전에 특정 요소나 데이터를 사용할 수 있을 때까지 "대기"해야 하는 경우가 종종 있습니다. 황혼은 이것을 간단하게 만듭니다. 다양한 메서드를 사용하여 요소가 페이지에 표시될 때까지 기다리거나 주어진 JavaScript 표현식이 `true`로 평가될 때까지 기다릴 수도 있습니다.

<a name="waiting"></a>
#### Waiting
#### 시간기준으로 기다리기

If you just need to pause the test for a given number of milliseconds, use the `pause` method:

주어진 시간(밀리초) 동안 테스트를 일시 중지해야 하는 경우 `pause' 메서드를 사용하세요.

    $browser->pause(1000);

<a name="waiting-for-selectors"></a>
#### Waiting For Selectors
#### Selector 가 표시되기를 기다리기

The `waitFor` method may be used to pause the execution of the test until the element matching the given CSS or Dusk selector is displayed on the page. By default, this will pause the test for a maximum of five seconds before throwing an exception. If necessary, you may pass a custom timeout threshold as the second argument to the method:

`waitFor` 메소드는 주어진 CSS 또는 Dusk 선택자와 일치하는 요소가 페이지에 표시될 때까지 테스트 실행을 일시 중지하는 데 사용할 수 있습니다. 기본적으로 예외가 발생하기 전에 최대 5초 동안 테스트를 일시 중지합니다. 필요한 경우 메서드에 대한 두 번째 인수로 사용자 지정 시간 초과 임계값을 전달할 수 있습니다.

    // Wait a maximum of five seconds for the selector...
    $browser->waitFor('.selector');

    // Wait a maximum of one second for the selector...
    $browser->waitFor('.selector', 1);

You may also wait until the element matching the given selector contains the given text:

주어진 선택자와 일치하는 요소에 주어진 텍스트가 포함될 때까지 기다릴 수도 있습니다.

    // Wait a maximum of five seconds for the selector to contain the given text...
    $browser->waitForTextIn('.selector', 'Hello World');

    // Wait a maximum of one second for the selector to contain the given text...
    $browser->waitForTextIn('.selector', 'Hello World', 1);

You may also wait until the element matching the given selector is missing from the page:

주어진 선택자와 일치하는 요소가 페이지에서 제거 될 때까지 기다릴 수도 있습니다.

    // Wait a maximum of five seconds until the selector is missing...
    $browser->waitUntilMissing('.selector');

    // Wait a maximum of one second until the selector is missing...
    $browser->waitUntilMissing('.selector', 1);

Or, you may wait until the element matching the given selector is enabled or disabled:

또는 주어진 선택자와 일치하는 요소가 활성화 또는 비활성화될 때까지 기다릴 수 있습니다.

    // Wait a maximum of five seconds until the selector is enabled...
    $browser->waitUntilEnabled('.selector');

    // Wait a maximum of one second until the selector is enabled...
    $browser->waitUntilEnabled('.selector', 1);

    // Wait a maximum of five seconds until the selector is disabled...
    $browser->waitUntilDisabled('.selector');

    // Wait a maximum of one second until the selector is disabled...
    $browser->waitUntilDisabled('.selector', 1);

<a name="scoping-selectors-when-available"></a>
#### Scoping Selectors When Available
#### 사용가능할 때 Selector의 특정 범위를 제한하여 동작하기

Occasionally, you may wish to wait for an element to appear that matches a given selector and then interact with the element. For example, you may wish to wait until a modal window is available and then press the "OK" button within the modal. The `whenAvailable` method may be used to accomplish this. All element operations performed within the given closure will be scoped to the original selector:

때때로 주어진 선택자와 일치하는 요소가 나타날 때까지 기다렸다가 요소와 상호 작용할 수 있습니다. 예를 들어, 모달 창을 사용할 수 있을 때까지 기다렸다가 모달 내에서 "확인" 버튼을 누를 수 있습니다. 이를 수행하기 위해 `whenAvailable` 메서드를 사용할 수 있습니다. 주어진 클로저 내에서 수행되는 모든 요소 작업은 원래 선택자로 범위가 지정됩니다.

    $browser->whenAvailable('.modal', function ($modal) {
        $modal->assertSee('Hello World')
              ->press('OK');
    });

<a name="waiting-for-text"></a>
#### Waiting For Text
#### 텍스트가 나타날 때까지 기다리기

The `waitForText` method may be used to wait until the given text is displayed on the page:

`waitForText` 메소드는 페이지에서 주어진 텍스트가 나타날때 까지 기다릴 때 사용됩니다.

    // Wait a maximum of five seconds for the text...
    $browser->waitForText('Hello World');

    // Wait a maximum of one second for the text...
    $browser->waitForText('Hello World', 1);

You may use the `waitUntilMissingText` method to wait until the displayed text has been removed from the page:

`waitUntilMissingText` 메소드를 사용하여 표시된 텍스트가 페이지에서 제거 될 때까지 기다릴 수 있습니다.

    // Wait a maximum of five seconds for the text to be removed...
    $browser->waitUntilMissingText('Hello World');

    // Wait a maximum of one second for the text to be removed...
    $browser->waitUntilMissingText('Hello World', 1);

<a name="waiting-for-links"></a>
#### Waiting For Links
#### 링크가 나타날 때까지 기다리기

The `waitForLink` method may be used to wait until the given link text is displayed on the page:

`waitForLink` 메소드는 페이지에서 주어진 링크가 나타날때 까지 기다릴 때 사용됩니다.

    // Wait a maximum of five seconds for the link...
    $browser->waitForLink('Create');

    // Wait a maximum of one second for the link...
    $browser->waitForLink('Create', 1);

<a name="waiting-on-the-page-location"></a>
#### Waiting On The Page Location
#### 페이지 경로를 기다리기

When making a path assertion such as `$browser->assertPathIs('/home')`, the assertion can fail if `window.location.pathname` is being updated asynchronously. You may use the `waitForLocation` method to wait for the location to be a given value:

`$browser->assertPathIs('/home')`와 같은 경로에 대한 검증을 만들 때 `window.location.pathname`이 비동기식으로 업데이트되면 검증이 실패할 수 있습니다. `waitForLocation` 메소드를 사용하여 위치가 주어진 값이 될 때까지 기다릴 수 있습니다.

    $browser->waitForLocation('/secret');

The `waitForLocation` method can also be used to wait for the current window location to be a fully qualified URL:

`waitForLocation` 메소드를 사용하여 현재 창 위치가 정규화된 URL이 될 때까지 기다릴 수도 있습니다.

    $browser->waitForLocation('https://example.com/path');

You may also wait for a [named route's](/docs/{{version}}/routing#named-routes) location:

[이름이 지정된 라우트](/docs/{{version}}/routing#named-routes) 위치를 기다릴 수도 있습니다.

    $browser->waitForRoute($routeName, $parameters);

<a name="waiting-for-page-reloads"></a>
#### Waiting for Page Reloads
#### 페이지 새로고침 기다리기

If you need to wait for a page to reload after performing an action, use the `waitForReload` method:

작업을 수행한 후 페이지가 다시 로드될 때까지 기다려야 하는 경우 `waitForReload` 메서드를 사용하세요.

    use Laravel\Dusk\Browser;

    $browser->waitForReload(function (Browser $browser) {
        $browser->press('Submit');
    })
    ->assertSee('Success!');

Since the need to wait for the page to reload typically occurs after clicking a button, you may use the `clickAndWaitForReload` method for convenience:

일반적으로 버튼을 클릭한 후 페이지가 다시 로드될 때까지 기다려야 하므로 편의를 위해 `clickAndWaitForReload` 메서드를 사용할 수 있습니다.

    $browser->clickAndWaitForReload('.selector')
            ->assertSee('something');

<a name="waiting-on-javascript-expressions"></a>
#### Waiting On JavaScript Expressions
#### 자바스크립트 표현식을 통해서 기다리기

Sometimes you may wish to pause the execution of a test until a given JavaScript expression evaluates to `true`. You may easily accomplish this using the `waitUntil` method. When passing an expression to this method, you do not need to include the `return` keyword or an ending semi-colon:

때로는 주어진 자바스크립트 표현식이 `true` 일 때까지 테스트 실행을 멈추고 있기를 원할 수도 있습니다. 이럴 때는 `waitUntil` 메소드를 사용하면 됩니다. 이 메소드에 표현식을 전달할 때에는 `return` 키워드나 종료 세미콜론을 포함할 필요가 없습니다.

    // Wait a maximum of five seconds for the expression to be true...
    $browser->waitUntil('App.data.servers.length > 0');

    // Wait a maximum of one second for the expression to be true...
    $browser->waitUntil('App.data.servers.length > 0', 1);

<a name="waiting-on-vue-expressions"></a>
#### Waiting On Vue Expressions
#### Vue의 표현을 기다리기

The `waitUntilVue` and `waitUntilVueIsNot` methods may be used to wait until a [Vue component](https://vuejs.org) attribute has a given value:

`waitUntilVue` 및 `waitUntilVueIsNot` 메소드는 [Vue component](https://vuejs.org) 속성이 주어진 값을 가질 때까지 대기하는 데 사용할 수 있습니다.

    // Wait until the component attribute contains the given value...
    $browser->waitUntilVue('user.name', 'Taylor', '@user');

    // Wait until the component attribute doesn't contain the given value...
    $browser->waitUntilVueIsNot('user.name', null, '@user');

<a name="waiting-with-a-callback"></a>
#### Waiting With A Callback
#### 콜백 기다리기

Many of the "wait" methods in Dusk rely on the underlying `waitUsing` method. You may use this method directly to wait for a given closure to return `true`. The `waitUsing` method accepts the maximum number of seconds to wait, the interval at which the closure should be evaluated, the closure, and an optional failure message:

Dusk의 많은 "wait" 메소드는 기본 `waitUsing` 메소드에 의존합니다. 주어진 클로저가 `true`를 반환할 때까지 기다리기 위해 이 메서드를 직접 사용할 수 있습니다. `waitUsing` 메소드는 대기할 최대 시간(초), 클로저가 검사되어야 하는 간격, 클로저 및 선택적 실패 메시지를 입력해야합니다.

    $browser->waitUsing(10, 1, function () use ($something) {
        return $something->isReady();
    }, "Something wasn't ready in time.");

<a name="scrolling-an-element-into-view"></a>
### Scrolling An Element Into View
### Element가 보이는 곳으로 스크롤하기

Sometimes you may not be able to click on an element because it is outside of the viewable area of the browser. The `scrollIntoView` method will scroll the browser window until the element at the given selector is within the view:

때때로 Element가 브라우저의 볼 수 있는 영역 밖에 있기 때문에 클릭하지 못할 수 있습니다. `scrollIntoView` 메소드는 지정된 선택자의 요소가 뷰 내에있을 때까지 브라우저 창을 스크롤합니다.

    $browser->scrollIntoView('.selector')
            ->click('.selector');

<a name="available-assertions"></a>
## Available Assertions
## 사용 가능한 Assertions

Dusk provides a variety of assertions that you may make against your application. All of the available assertions are documented in the list below:

Dusk는 애플리케이션에서 사용가능한 다양한 assertion을 제공합니다. 가능한 모든 assertions은 아래 목록과 같습니다.

- [assertTitle](#assert-title)
- [assertTitleContains](#assert-title-contains)
- [assertUrlIs](#assert-url-is)
- [assertSchemeIs](#assert-scheme-is)
- [assertSchemeIsNot](#assert-scheme-is-not)
- [assertHostIs](#assert-host-is)
- [assertHostIsNot](#assert-host-is-not)
- [assertPortIs](#assert-port-is)
- [assertPortIsNot](#assert-port-is-not)
- [assertPathBeginsWith](#assert-path-begins-with)
- [assertPathIs](#assert-path-is)
- [assertPathIsNot](#assert-path-is-not)
- [assertRouteIs](#assert-route-is)
- [assertQueryStringHas](#assert-query-string-has)
- [assertQueryStringMissing](#assert-query-string-missing)
- [assertFragmentIs](#assert-fragment-is)
- [assertFragmentBeginsWith](#assert-fragment-begins-with)
- [assertFragmentIsNot](#assert-fragment-is-not)
- [assertHasCookie](#assert-has-cookie)
- [assertHasPlainCookie](#assert-has-plain-cookie)
- [assertCookieMissing](#assert-cookie-missing)
- [assertPlainCookieMissing](#assert-plain-cookie-missing)
- [assertCookieValue](#assert-cookie-value)
- [assertPlainCookieValue](#assert-plain-cookie-value)
- [assertSee](#assert-see)
- [assertDontSee](#assert-dont-see)
- [assertSeeIn](#assert-see-in)
- [assertDontSeeIn](#assert-dont-see-in)
- [assertSeeAnythingIn](#assert-see-anything-in)
- [assertSeeNothingIn](#assert-see-nothing-in)
- [assertScript](#assert-script)
- [assertSourceHas](#assert-source-has)
- [assertSourceMissing](#assert-source-missing)
- [assertSeeLink](#assert-see-link)
- [assertDontSeeLink](#assert-dont-see-link)
- [assertInputValue](#assert-input-value)
- [assertInputValueIsNot](#assert-input-value-is-not)
- [assertChecked](#assert-checked)
- [assertNotChecked](#assert-not-checked)
- [assertRadioSelected](#assert-radio-selected)
- [assertRadioNotSelected](#assert-radio-not-selected)
- [assertSelected](#assert-selected)
- [assertNotSelected](#assert-not-selected)
- [assertSelectHasOptions](#assert-select-has-options)
- [assertSelectMissingOptions](#assert-select-missing-options)
- [assertSelectHasOption](#assert-select-has-option)
- [assertSelectMissingOption](#assert-select-missing-option)
- [assertValue](#assert-value)
- [assertValueIsNot](#assert-value-is-not)
- [assertAttribute](#assert-attribute)
- [assertAttributeContains](#assert-attribute-contains)
- [assertAriaAttribute](#assert-aria-attribute)
- [assertDataAttribute](#assert-data-attribute)
- [assertVisible](#assert-visible)
- [assertPresent](#assert-present)
- [assertNotPresent](#assert-not-present)
- [assertMissing](#assert-missing)
- [assertInputPresent](#assert-input-present)
- [assertInputMissing](#assert-input-missing)
- [assertDialogOpened](#assert-dialog-opened)
- [assertEnabled](#assert-enabled)
- [assertDisabled](#assert-disabled)
- [assertButtonEnabled](#assert-button-enabled)
- [assertButtonDisabled](#assert-button-disabled)
- [assertFocused](#assert-focused)
- [assertNotFocused](#assert-not-focused)
- [assertAuthenticated](#assert-authenticated)
- [assertGuest](#assert-guest)
- [assertAuthenticatedAs](#assert-authenticated-as)
- [assertVue](#assert-vue)
- [assertVueIsNot](#assert-vue-is-not)
- [assertVueContains](#assert-vue-contains)
- [assertVueDoesNotContain](#assert-vue-does-not-contain)

<a name="assert-title"></a>
#### assertTitle
#### assertTitle

Assert that the page title matches the given text:

페이지 타이틀이 주어진 텍스트와 일치하는지 확인:

    $browser->assertTitle($title);

<a name="assert-title-contains"></a>
#### assertTitleContains
#### assertTitleContains

Assert that the page title contains the given text:

페이지 타이틀이 주어진 텍스트를 포함하는지 확인:

    $browser->assertTitleContains($title);

<a name="assert-url-is"></a>
#### assertUrlIs
#### assertUrlIs

Assert that the current URL (without the query string) matches the given string:

현재의 URL이 (쿼리 스트링을 제외하고) 주어진 문자열과 일치 하는지 확인:

    $browser->assertUrlIs($url);

<a name="assert-scheme-is"></a>
#### assertSchemeIs
#### assertSchemeIs

Assert that the current URL scheme matches the given scheme:

현재의 URL이 주어진 스키마(scheme)와 일치 하는지 확인:

    $browser->assertSchemeIs($scheme);

<a name="assert-scheme-is-not"></a>
#### assertSchemeIsNot
#### assertSchemeIsNot

Assert that the current URL scheme does not match the given scheme:

현재의 URL이 주어진 스키마(scheme)와 일치 하지 않는지 확인:

    $browser->assertSchemeIsNot($scheme);

<a name="assert-host-is"></a>
#### assertHostIs
#### assertHostIs

Assert that the current URL host matches the given host:

현재의 URL이 주어진 호스트와 일치 하는지 확인:

    $browser->assertHostIs($host);

<a name="assert-host-is-not"></a>
#### assertHostIsNot
#### assertHostIsNot

Assert that the current URL host does not match the given host:

현재의 URL이 주어진 호스트와 일치 하지 않는지 확인:

    $browser->assertHostIsNot($host);

<a name="assert-port-is"></a>
#### assertPortIs
#### assertPortIs

Assert that the current URL port matches the given port:

현재의 URL이 주어진 포트와 일치 하는지 확인:

    $browser->assertPortIs($port);

<a name="assert-port-is-not"></a>
#### assertPortIsNot
#### assertPortIsNot

Assert that the current URL port does not match the given port:

현재의 URL이 주어진 포트와 일치 하지 않는지 확인:

    $browser->assertPortIsNot($port);

<a name="assert-path-begins-with"></a>
#### assertPathBeginsWith
#### assertPathBeginsWith

Assert that the current URL path begins with the given path:

현재 URL 경로가 주어진 경로로 시작하는지 확인:

    $browser->assertPathBeginsWith('/home');

<a name="assert-path-is"></a>
#### assertPathIs
#### assertPathIs

Assert that the current path matches the given path:

현재 경로가 주어진 경로와 일치하는지 확인:

    $browser->assertPathIs('/home');

<a name="assert-path-is-not"></a>
#### assertPathIsNot
#### assertPathIsNot

Assert that the current path does not match the given path:

현재 경로가 주어진 경로와 일치하지 않는 것을 확인:

    $browser->assertPathIsNot('/home');

<a name="assert-route-is"></a>
#### assertRouteIs
#### assertRouteIs

Assert that the current URL matches the given [named route's](/docs/{{version}}/routing#named-routes) URL:

현재 URL이 지정된 [이름이 지정된 라우트](/docs/{{version}}/routing#named-routes) URL과 일치하는지 확인:

    $browser->assertRouteIs($name, $parameters);

<a name="assert-query-string-has"></a>
#### assertQueryStringHas
#### assertQueryStringHas

Assert that the given query string parameter is present:

쿼리 스트링 파라미터가 존재하는지 확인:

    $browser->assertQueryStringHas($name);

Assert that the given query string parameter is present and has a given value:

쿼리 스트링 파라미터가 존재하고 주어진 값을 가지고 있는지 확인:

    $browser->assertQueryStringHas($name, $value);

<a name="assert-query-string-missing"></a>
#### assertQueryStringMissing
#### assertQueryStringMissing

Assert that the given query string parameter is missing:

주어진 쿼리 스트링 파라미터가 없다는 것을 확인:

    $browser->assertQueryStringMissing($name);

<a name="assert-fragment-is"></a>
#### assertFragmentIs
#### assertFragmentIs

Assert that the URL's current hash fragment matches the given fragment:

URL의 현재 해시 조각이 주어진 조각과 일치하는지 확인:

    $browser->assertFragmentIs('anchor');

<a name="assert-fragment-begins-with"></a>
#### assertFragmentBeginsWith
#### assertFragmentBeginsWith

Assert that the URL's current hash fragment begins with the given fragment:

URL의 현재 해시 값이 주어진 값으로 시작하는지 확인:

    $browser->assertFragmentBeginsWith('anchor');

<a name="assert-fragment-is-not"></a>
#### assertFragmentIsNot
#### assertFragmentIsNot

Assert that the URL's current hash fragment does not match the given fragment:

URL의 현재 해시 값이 주어진 값과 일치하지 않는지 확인:

    $browser->assertFragmentIsNot('anchor');

<a name="assert-has-cookie"></a>
#### assertHasCookie
#### assertHasCookie

Assert that the given encrypted cookie is present:

주어진 암호화된 쿠키가 존재하는지 확인:

    $browser->assertHasCookie($name);

<a name="assert-has-plain-cookie"></a>
#### assertHasPlainCookie
#### assertHasPlainCookie

Assert that the given unencrypted cookie is present:

주어진 암호화되지 않은 쿠키가 있는지 확인:

    $browser->assertHasPlainCookie($name);

<a name="assert-cookie-missing"></a>
#### assertCookieMissing
#### assertCookieMissing

Assert that the given encrypted cookie is not present:

주어진 암호화된 쿠키가 존재하지 않는 것을 확인:

    $browser->assertCookieMissing($name);

<a name="assert-plain-cookie-missing"></a>
#### assertPlainCookieMissing
#### assertPlainCookieMissing

Assert that the given unencrypted cookie is not present:

주어진 암호화되지 않은 쿠키가 존재하지 않는 것을 확인:

    $browser->assertPlainCookieMissing($name);

<a name="assert-cookie-value"></a>
#### assertCookieValue
#### assertCookieValue

Assert that an encrypted cookie has a given value:

암호화된 쿠키가 주어진 값을 가지고 있는지 확인:

    $browser->assertCookieValue($name, $value);

<a name="assert-plain-cookie-value"></a>
#### assertPlainCookieValue
#### assertPlainCookieValue

Assert that an unencrypted cookie has a given value:

암호화 되지 않은 쿠키가 주어진 값을 가지고 있는지 확인:

    $browser->assertPlainCookieValue($name, $value);

<a name="assert-see"></a>
#### assertSee
#### assertSee

Assert that the given text is present on the page:

현재 페이지에 주어진 텍스트가 존재하는지 확인:

    $browser->assertSee($text);

<a name="assert-dont-see"></a>
#### assertDontSee
#### assertDontSee

Assert that the given text is not present on the page:

현재 페이지에 주어진 텍스트가 존재하지 않는 것을 확인:

    $browser->assertDontSee($text);

<a name="assert-see-in"></a>
#### assertSeeIn
#### assertSeeIn

Assert that the given text is present within the selector:

selector 안에서 주어진 텍스트가 존재하는지 확인:

    $browser->assertSeeIn($selector, $text);

<a name="assert-dont-see-in"></a>
#### assertDontSeeIn
#### assertDontSeeIn

Assert that the given text is not present within the selector:

selector 안에서 주어진 텍스트가 존재하지 않는 것을 확인:

    $browser->assertDontSeeIn($selector, $text);

<a name="assert-see-anything-in"></a>
#### assertSeeAnythingIn
#### assertSeeAnythingIn

Assert that any text is present within the selector:

선택자 내에 텍스트가 있는지 확인:

    $browser->assertSeeAnythingIn($selector);

<a name="assert-see-nothing-in"></a>
#### assertSeeNothingIn
#### assertSeeNothingIn

Assert that no text is present within the selector:

선택자 내에 텍스트가 없는지 확인:

    $browser->assertSeeNothingIn($selector);

<a name="assert-script"></a>
#### assertScript
#### assertScript

Assert that the given JavaScript expression evaluates to the given value:

주어진 값이 JavaScript 표현식인지 확인:

    $browser->assertScript('window.isLoaded')
            ->assertScript('document.readyState', 'complete');

<a name="assert-source-has"></a>
#### assertSourceHas
#### assertSourceHas

Assert that the given source code is present on the page:

주어진 소스 코드가 현재 페이지에 존재하는지 확인:

    $browser->assertSourceHas($code);

<a name="assert-source-missing"></a>
#### assertSourceMissing
#### assertSourceMissing

Assert that the given source code is not present on the page:

주어진 소스 코드가 현재 페이지에 존재하지 않는 것을 확인:

    $browser->assertSourceMissing($code);

<a name="assert-see-link"></a>
#### assertSeeLink
#### assertSeeLink

Assert that the given link is present on the page:

현재 페이지에 주어진 링크가 존재하는지 확인:

    $browser->assertSeeLink($linkText);

<a name="assert-dont-see-link"></a>
#### assertDontSeeLink
#### assertDontSeeLink

Assert that the given link is not present on the page:

현재 페이지에 주어진 링크가 존재하지 않는 것을 확인:

    $browser->assertDontSeeLink($linkText);

<a name="assert-input-value"></a>
#### assertInputValue
#### assertInputValue

Assert that the given input field has the given value:

주어진 input 필드가 주어진 값을 가지는지 확인:

    $browser->assertInputValue($field, $value);

<a name="assert-input-value-is-not"></a>
#### assertInputValueIsNot
#### assertInputValueIsNot

Assert that the given input field does not have the given value:

주어진 input 필드가 주어진 값을 가지고 있지 않을 것을 확인:

    $browser->assertInputValueIsNot($field, $value);

<a name="assert-checked"></a>
#### assertChecked
#### assertChecked

Assert that the given checkbox is checked:

주어진 체크박스가 체크되어 있는지 확인:

    $browser->assertChecked($field);

<a name="assert-not-checked"></a>
#### assertNotChecked
#### assertNotChecked

Assert that the given checkbox is not checked:

주어진 체크박스가 체크되어 있지 않은 것을 확인:

    $browser->assertNotChecked($field);

<a name="assert-radio-selected"></a>
#### assertRadioSelected
#### assertRadioSelected

Assert that the given radio field is selected:

주어진 라디오박스의 필드가 선택되어 있는지 확인:

    $browser->assertRadioSelected($field, $value);

<a name="assert-radio-not-selected"></a>
#### assertRadioNotSelected
#### assertRadioNotSelected

Assert that the given radio field is not selected:

주어진 라디오박스의 필드가 선택되어 있지 않은 것을 확인:

    $browser->assertRadioNotSelected($field, $value);

<a name="assert-selected"></a>
#### assertSelected
#### assertSelected

Assert that the given dropdown has the given value selected:

주어진 드랍박스(셀렉트박스)에서 주어진 값이 선택되어 있는 것을 확인:

    $browser->assertSelected($field, $value);

<a name="assert-not-selected"></a>
#### assertNotSelected
#### assertNotSelected

Assert that the given dropdown does not have the given value selected:

주어진 드랍박스(셀렉트박스)에서 주어진 값이 선택되어 있지 않은 것을 확인:

    $browser->assertNotSelected($field, $value);

<a name="assert-select-has-options"></a>
#### assertSelectHasOptions
#### assertSelectHasOptions

Assert that the given array of values are available to be selected:

주어진 값의 배열이 선택가능한 옵션으로 제공되는지 확인:

    $browser->assertSelectHasOptions($field, $values);

<a name="assert-select-missing-options"></a>
#### assertSelectMissingOptions
#### assertSelectMissingOptions

Assert that the given array of values are not available to be selected:

주어진 값의 배열이 선택가능하지 않는 옵션으로 제공되는지 확인:

    $browser->assertSelectMissingOptions($field, $values);

<a name="assert-select-has-option"></a>
#### assertSelectHasOption
#### assertSelectHasOption

Assert that the given value is available to be selected on the given field:

주어진 값이 필드에서 선택 가능한 값인지 확인:

    $browser->assertSelectHasOption($field, $value);

<a name="assert-select-missing-option"></a>
#### assertSelectMissingOption
#### assertSelectMissingOption

Assert that the given value is not available to be selected:

주어진 값을 선택할 수 없음을 확인:

    $browser->assertSelectMissingOption($field, $value);

<a name="assert-value"></a>
#### assertValue
#### assertValue

Assert that the element matching the given selector has the given value:

주어진 selector 와 매칭되는 element가 주어진 값을 가지는지 확인:

    $browser->assertValue($selector, $value);

<a name="assert-value-is-not"></a>
#### assertValueIsNot
#### assertValueIsNot

Assert that the element matching the given selector does not have the given value:

주어진 선택자와 일치하는 요소에 주어진 값이 없음을 확인:

    $browser->assertValueIsNot($selector, $value);

<a name="assert-attribute"></a>
#### assertAttribute
#### assertAttribute

Assert that the element matching the given selector has the given value in the provided attribute:

주어진 선택자와 일치하는 요소가 제공된 속성에 주어진 값을 가지는지 확인:

    $browser->assertAttribute($selector, $attribute, $value);

<a name="assert-attribute-contains"></a>
#### assertAttributeContains
#### assertAttributeContains

Assert that the element matching the given selector contains the given value in the provided attribute:

주어진 선택자와 일치하는 요소가 제공된 속성에 주어진 값을 포함하는지 확인:

    $browser->assertAttributeContains($selector, $attribute, $value);

<a name="assert-aria-attribute"></a>
#### assertAriaAttribute
#### assertAriaAttribute

Assert that the element matching the given selector has the given value in the provided aria attribute:

제공된 선택자와 일치하는 요소가 제공된 aria 속성에 지정된 값을 가지고 있는지 확인:

    $browser->assertAriaAttribute($selector, $attribute, $value);

For example, given the markup `<button aria-label="Add"></button>`, you may assert against the `aria-label` attribute like so:

예를 들어, `<button aria-label="Add"></button>`마크업이 주어지면 다음과 같이 `aria-label` 속성에 대해 검증:

    $browser->assertAriaAttribute('button', 'label', 'Add')

<a name="assert-data-attribute"></a>
#### assertDataAttribute
#### assertDataAttribute

Assert that the element matching the given selector has the given value in the provided data attribute:

제공된 선택자와 일치하는 요소가 제공된 데이터 속성에 지정된 값을 가지고 있는지 확인:

    $browser->assertDataAttribute($selector, $attribute, $value);

For example, given the markup `<tr id="row-1" data-content="attendees"></tr>`, you may assert against the `data-label` attribute like so:

예를 들어 `<tr id="row-1" data-content="attendees"></tr>`마크 업이 주어지면 다음과 같이`data-label` 속성에 대해 검증:

    $browser->assertDataAttribute('#row-1', 'content', 'attendees')

<a name="assert-visible"></a>
#### assertVisible
#### assertVisible

Assert that the element matching the given selector is visible:

주어진 selector 와 매칭되는 element가 화면에 보이는지 확인:

    $browser->assertVisible($selector);

<a name="assert-present"></a>
#### assertPresent
#### assertPresent

Assert that the element matching the given selector is present in the source:

주어진 선택자와 일치하는 요소가 소스에 있는지 확인:

    $browser->assertPresent($selector);

<a name="assert-not-present"></a>
#### assertNotPresent
#### assertNotPresent

Assert that the element matching the given selector is not present in the source:

주어진 선택자와 일치하는 요소가 소스에 존재하지 않는지 확인:

    $browser->assertNotPresent($selector);

<a name="assert-missing"></a>
#### assertMissing
#### assertMissing

Assert that the element matching the given selector is not visible:

주어진 selector 와 매칭되는 element가 화면에 보이지 않는 것을 확인:

    $browser->assertMissing($selector);

<a name="assert-input-present"></a>
#### assertInputPresent
#### assertInputPresent

Assert that an input with the given name is present:

주어진 이름의 입력이 존재하는지 확인:

    $browser->assertInputPresent($name);

<a name="assert-input-missing"></a>
#### assertInputMissing
#### assertInputMissing

Assert that an input with the given name is not present in the source:

주어진 이름을 가진 입력이 소스에 존재하지 않는지 확인:

    $browser->assertInputMissing($name);

<a name="assert-dialog-opened"></a>
#### assertDialogOpened
#### assertDialogOpened

Assert that a JavaScript dialog with the given message has been opened:

주어진 메세지가 있는 자바스크립트 다이얼로그가 열려있는지 확인:

    $browser->assertDialogOpened($message);

<a name="assert-enabled"></a>
#### assertEnabled
#### assertEnabled

Assert that the given field is enabled:

주어진 필드가 사용가능한지 확인:

    $browser->assertEnabled($field);

<a name="assert-disabled"></a>
#### assertDisabled
#### assertDisabled

Assert that the given field is disabled:

주어진 필드가 사용 불가능한지 확인:

    $browser->assertDisabled($field);

<a name="assert-button-enabled"></a>
#### assertButtonEnabled
#### assertButtonEnabled

Assert that the given button is enabled:

주어진 버튼이 활성화되어 있는지 확인:

    $browser->assertButtonEnabled($button);

<a name="assert-button-disabled"></a>
#### assertButtonDisabled
#### assertButtonDisabled

Assert that the given button is disabled:

주어진 버튼이 비활성화되어 있는지 확인:

    $browser->assertButtonDisabled($button);

<a name="assert-focused"></a>
#### assertFocused
#### assertFocused

Assert that the given field is focused:

주어진 필드에 포커스가 되었는지 확인:

    $browser->assertFocused($field);

<a name="assert-not-focused"></a>
#### assertNotFocused
#### assertNotFocused

Assert that the given field is not focused:

주어진 필드에 포커스가 되지 않은 것을 확인:

    $browser->assertNotFocused($field);

<a name="assert-authenticated"></a>
#### assertAuthenticated
#### assertAuthenticated

Assert that the user is authenticated:

사용자가 인증되었는지 확인:

    $browser->assertAuthenticated();

<a name="assert-guest"></a>
#### assertGuest
#### assertGuest

Assert that the user is not authenticated:

사용자가 인증되지 않았음을 확인:

    $browser->assertGuest();

<a name="assert-authenticated-as"></a>
#### assertAuthenticatedAs
#### assertAuthenticatedAs

Assert that the user is authenticated as the given user:

사용자가 지정된 사용자로 인증되었는지 확인:

    $browser->assertAuthenticatedAs($user);

<a name="assert-vue"></a>
#### assertVue
#### assertVue

Dusk even allows you to make assertions on the state of [Vue component](https://vuejs.org) data. For example, imagine your application contains the following Vue component:

Dusk를 사용하면 [Vue component](https://vuejs.org) 데이터의 상태에 대한 검증도 할 수 있습니다. 예를 들어 애플리케이션에 다음 Vue 컴포넌트가 포함되어 있다고 가정합니다.

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

다음과 같이 Vue 컴포넌트의 상태를 검증 할 수 있습니다.

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

<a name="assert-vue-is-not"></a>
#### assertVueIsNot
#### assertVueIsNot

Assert that a given Vue component data property does not match the given value:

주어진 Vue 컴포넌트의 속성이 주어진 값과 일치하지 않는 것을 확인:

    $browser->assertVueIsNot($property, $value, $componentSelector = null);

<a name="assert-vue-contains"></a>
#### assertVueContains
#### assertVueContains

Assert that a given Vue component data property is an array and contains the given value:

주어진 Vue 컴포넌트의 data 프로퍼티가 배열이고 주어진 값을 포함하는지 확인:

    $browser->assertVueContains($property, $value, $componentSelector = null);

<a name="assert-vue-does-not-contain"></a>
#### assertVueDoesNotContain
#### assertVueDoesNotContain

Assert that a given Vue component data property is an array and does not contain the given value:

주어진 Vue 컴포넌트의 data 프로퍼티가 배열이고 주어진 값을 포함하지 않는 것을 확인:

    $browser->assertVueDoesNotContain($property, $value, $componentSelector = null);

<a name="pages"></a>
## Pages
## 페이지-Pages

Sometimes, tests require several complicated actions to be performed in sequence. This can make your tests harder to read and understand. Dusk Pages allow you to define expressive actions that may then be performed on a given page via a single method. Pages also allow you to define short-cuts to common selectors for your application or for a single page.

때때로 테스트는 여러 복잡한 작업을 순서대로 수행해야 합니다. 이렇게 하면 테스트를 읽고 이해하기가 더 어려워질 수 있습니다. Dusk 페이지를 사용하면 단일 메서드를 통해 주어진 페이지에서 수행할 수 있는 표현 작업을 정의할 수 있습니다. 페이지를 사용하면 애플리케이션 또는 단일 페이지에 대한 공통 선택자에 대한 바로 가기를 정의할 수도 있습니다.

<a name="generating-pages"></a>
### Generating Pages
### 페이지 생성하기

To generate a page object, execute the `dusk:page` Artisan command. All page objects will be placed in your application's `tests/Browser/Pages` directory:

페이지 개체를 생성하려면 `dusk:page` Artisan 명령을 실행하세요. 모든 페이지 객체는 애플리케이션의 `tests/Browser/Pages` 디렉토리에 생성됩니다.

    php artisan dusk:page Login

<a name="configuring-pages"></a>
### Configuring Pages
### 페이지 설정하기

By default, pages have three methods: `url`, `assert`, and `elements`. We will discuss the `url` and `assert` methods now. The `elements` method will be [discussed in more detail below](#shorthand-selectors).

기본적으로, 페이지는 `url`, `assert`, `elements`의 세 가지 메소드를 가집니다. 우리는 이제 `url`과 `assert` 메소드에 대해서 논의 할 것입니다. `elements` 메소드는 [아래에서 자세히 논의 할 것입니다](#shorthand-selectors)

<a name="the-url-method"></a>
#### The `url` Method
#### `url` 메소드

The `url` method should return the path of the URL that represents the page. Dusk will use this URL when navigating to the page in the browser:

`url` 메소드는 페이지를 나타내는 URL의 경로를 반환해야합니다. Dusk는 브라우저의 페이지로 이동할 때 이 URL을 사용합니다.

    /**
     * Get the URL for the page.
     *
     * @return string
     */
    public function url()
    {
        return '/login';
    }

<a name="the-assert-method"></a>
#### The `assert` Method
#### `assert` 메소드

The `assert` method may make any assertions necessary to verify that the browser is actually on the given page. It is not actually necessary to place anything within this method; however, you are free to make these assertions if you wish. These assertions will be run automatically when navigating to the page:

`assert` 메서드는 브라우저가 실제로 주어진 페이지에 있는지 확인하는 데 필요한 모든 검증을 할 수 있습니다. 실제로 이 메서드 내에 아무 것도 배치할 필요가 없습니다. 그러나 원하는 경우 이러한 검증을 자유롭게 할 수 있습니다. 다음 어설션은 페이지를 탐색할 때 자동으로 실행됩니다.

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
### 페이지 탐색

Once a page has been defined, you may navigate to it using the `visit` method:

페이지가 정의되면 `visit` 메소드를 사용하여 페이지를 탐색할 수 있습니다.

    use Tests\Browser\Pages\Login;

    $browser->visit(new Login);

Sometimes you may already be on a given page and need to "load" the page's selectors and methods into the current test context. This is common when pressing a button and being redirected to a given page without explicitly navigating to it. In this situation, you may use the `on` method to load the page:

때로는 이미 주어진 페이지에 접속 해 있을 수 있으며 페이지의 선택자와 메소드를 현재 테스트 컨텍스트에 "로드"해야 할 때가 있습니다. 이는 버튼을 누르고 명시적으로 탐색하지 않고 지정된 페이지로 리디렉션 될 때 일반적입니다. 이 상황에서, 당신은 페이지를 로드하기 위해 `on` 메소드를 사용할 수 있습니다.

    use Tests\Browser\Pages\CreatePlaylist;

    $browser->visit('/dashboard')
            ->clickLink('Create Playlist')
            ->on(new CreatePlaylist)
            ->assertSee('@create');

<a name="shorthand-selectors"></a>
### Shorthand Selectors
### 단축 셀렉터

The `elements` method within page classes allows you to define quick, easy-to-remember shortcuts for any CSS selector on your page. For example, let's define a shortcut for the "email" input field of the application's login page:

페이지 클래스 내의 `elements` 메소드를 사용하면 페이지의 모든 CSS 선택자에 대해 빠르고 기억하기 쉬운 단축키를 정의할 수 있습니다. 예를 들어, 애플리케이션 로그인 페이지의 "이메일" 입력 필드에 대한 바로 가기를 정의해 보겠습니다.

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

Once the shortcut has been defined, you may use the shorthand selector anywhere you would typically use a full CSS selector:

바로 가기가 정의되면 일반적으로 전체 CSS 선택자를 사용하는 곳에서 약식 선택자를 사용할 수 있습니다.

    $browser->type('@email', 'taylor@laravel.com');

<a name="global-shorthand-selectors"></a>
#### Global Shorthand Selectors
#### 전역 단축 셀렉터

After installing Dusk, a base `Page` class will be placed in your `tests/Browser/Pages` directory. This class contains a `siteElements` method which may be used to define global shorthand selectors that should be available on every page throughout your application:

Dusk을 설치 한 후, 기본 `Page` 클래스는 `tests/Browser/Pages` 디렉토리에 있을 것 입니다. 이 클래스는 애플리케이션 전체의 모든 페이지에서 사용할 수있는 전역 단축 셀렉터를 정의하는 데 사용할 수있는 `siteElements` 메소드를 포함하고 있습니다.

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
### 페이지 메소드

In addition to the default methods defined on pages, you may define additional methods which may be used throughout your tests. For example, let's imagine we are building a music management application. A common action for one page of the application might be to create a playlist. Instead of re-writing the logic to create a playlist in each test, you may define a `createPlaylist` method on a page class:

페이지에 정의 된 기본 메소드 외에도 테스트를 통해 사용할 수있는 추가 메소드를 정의 할 수 있습니다. 예를 들어 음악 관리 애플리케이션을 제작하고 있다고 가정 해 봅시다. 애플리케이션의 한 페이지에 대한 공통적인 동작은 재생 목록을 만드는 것입니다. 각 테스트에서 재생 목록을 생성하는 로직을 다시 작성하는 대신 페이지 클래스에 `createPlaylist` 메소드를 정의 할 수 있습니다.

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

Once the method has been defined, you may use it within any test that utilizes the page. The browser instance will automatically be passed as the first argument to custom page methods:

메서드가 정의되면 페이지를 활용하는 모든 테스트 내에서 사용할 수 있습니다. 브라우저 인스턴스는 사용자 정의 페이지 메소드에 대한 첫 번째 인수로 자동으로 전달됩니다.

    use Tests\Browser\Pages\Dashboard;

    $browser->visit(new Dashboard)
            ->createPlaylist('My Playlist')
            ->assertSee('My Playlist');

<a name="components"></a>
## Components
## 컴포넌트

Components are similar to Dusk’s “page objects”, but are intended for pieces of UI and functionality that are re-used throughout your application, such as a navigation bar or notification window. As such, components are not bound to specific URLs.

컴포넌트는 Dusk의 "페이지 개체-objects"와 유사하지만 네비게이션바 또는 알림 창과 같이 애플리케이션 전체에서 다시 사용되는 UI 및 기능에 사용됩니다. 따라서 컴포넌트는 특정 URL에 바인딩되지 않습니다.

<a name="generating-components"></a>
### Generating Components
### 컴포넌트 생성하기

To generate a component, execute the `dusk:component` Artisan command. New components are placed in the `tests/Browser/Components` directory:

컴포넌트를 생성하려면 `dusk:component` Artisan 명령을 실행하세요. 새 컴포넌트는 `tests/Browser/Components` 디렉토리에 생성됩니다.

    php artisan dusk:component DatePicker

As shown above, a "date picker" is an example of a component that might exist throughout your application on a variety of pages. It can become cumbersome to manually write the browser automation logic to select a date in dozens of tests throughout your test suite. Instead, we can define a Dusk component to represent the date picker, allowing us to encapsulate that logic within the component:

위에 표시된 것처럼 "날짜 선택 도구"는 다양한 페이지에서 애플리케이션 전체에 존재할 수있는 컴포넌트의 예입니다. 테스트 스위트-suite 전체에서 수십 가지 테스트 날짜를 선택하기 위해 수동으로 브라우저 자동화 로직을 작성하는 것은 번거로울 수 있습니다. 대신 Dusk 컴포넌트를 정의하여 날짜 선택 도구를 나타낼 수 있으므로 컴포넌트 내에 해당 논리를 캡슐화 할 수 있습니다.

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
                '@year-list' => 'div > div.datepicker-years',
                '@month-list' => 'div > div.datepicker-months',
                '@day-list' => 'div > div.datepicker-days',
            ];
        }

        /**
         * Select the given date.
         *
         * @param  \Laravel\Dusk\Browser  $browser
         * @param  int  $year
         * @param  int  $month
         * @param  int  $day
         * @return void
         */
        public function selectDate(Browser $browser, $year, $month, $day)
        {
            $browser->click('@date-field')
                    ->within('@year-list', function ($browser) use ($year) {
                        $browser->click($year);
                    })
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
### 컴포넌트 사용하기

Once the component has been defined, we can easily select a date within the date picker from any test. And, if the logic necessary to select a date changes, we only need to update the component:

컴포넌트가 정의되면 모든 테스트에서 날짜 선택 도구 내의 날짜를 쉽게 선택할 수 있습니다. 그리고 날짜를 선택하는 데 필요한 로직이 변경되면 컴포넌트만 업데이트하면 됩니다.

    <?php

    namespace Tests\Browser;

    use Illuminate\Foundation\Testing\DatabaseMigrations;
    use Laravel\Dusk\Browser;
    use Tests\Browser\Components\DatePicker;
    use Tests\DuskTestCase;

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
                            $browser->selectDate(2019, 1, 30);
                        })
                        ->assertSee('January');
            });
        }
    }

<a name="continuous-integration"></a>
## Continuous Integration
## CI - 지속적 통합

> {note} Most Dusk continuous integration configurations expect your Laravel application to be served using the built-in PHP development server on port 8000. Therefore, before continuing, you should ensure that your continuous integration environment has an `APP_URL` environment variable value of `http://127.0.0.1:8000`.

> {note} 대부분의 Dusk 지속적 통합 설정은 8000포트에서 내장 PHP 개발 서버를 사용하여, 라라벨 애플리케이션이 제공될 것으로 예상합니다. 따라서 계속하기 전에 지속적 통합 환경에 `APP_URL` 환경 변수 값이 `http://127.0.0.1:8000`로 존재하는지 확인해야 합니다.

<a name="running-tests-on-heroku-ci"></a>
### Heroku CI
### Heroku CI

To run Dusk tests on [Heroku CI](https://www.heroku.com/continuous-integration), add the following Google Chrome buildpack and scripts to your Heroku `app.json` file:

[Heroku CI](https://www.heroku.com/continuous-integration) 에서 Dusk 테스트를 실행한다면, Heroku의 `app.json` 파일에 다음의 구글 크롬 빌드팩과 스크립트를 추가하십시오.

    {
      "environments": {
        "test": {
          "buildpacks": [
            { "url": "heroku/php" },
            { "url": "https://github.com/heroku/heroku-buildpack-google-chrome" }
          ],
          "scripts": {
            "test-setup": "cp .env.testing .env",
            "test": "nohup bash -c './vendor/laravel/dusk/bin/chromedriver-linux > /dev/null 2>&1 &' && nohup bash -c 'php artisan serve --no-reload > /dev/null 2>&1 &' && php artisan dusk"
          }
        }
      }
    }

<a name="running-tests-on-travis-ci"></a>
### Travis CI
### Travis CI

To run your Dusk tests on [Travis CI](https://travis-ci.org), use the following `.travis.yml` configuration. Since Travis CI is not a graphical environment, we will need to take some extra steps in order to launch a Chrome browser. In addition, we will use `php artisan serve` to launch PHP's built-in web server:

[Travis CI](https://travis-ci.org) 에서 Dusk 테스트를 수행하기 위해서는 아래에 나오는 `travis.yml` 설정을 사용하십시오. Travis CI는 그래픽 환경이 아니기 때문에, 크롬 브라우저를 구동하기 위해서는 몇가지 별도의 작업을 필요로 합니다. 추가적으로 PHP 내장 브라우저를 구동하기 위해서 `php artisan serve`를 사용할 수 있습니다.

```yaml
language: php

php:
  - 7.3

addons:
  chrome: stable

install:
  - cp .env.testing .env
  - travis_retry composer install --no-interaction --prefer-dist
  - php artisan key:generate
  - php artisan dusk:chrome-driver

before_script:
  - google-chrome-stable --headless --disable-gpu --remote-debugging-port=9222 http://localhost &
  - php artisan serve --no-reload &

script:
  - php artisan dusk
```

<a name="running-tests-on-github-actions"></a>
### GitHub Actions
### GitHub Actions

If you are using [Github Actions](https://github.com/features/actions) to run your Dusk tests, you may use the following configuration file as a starting point. Like TravisCI, we will use the `php artisan serve` command to launch PHP's built-in web server:

[Github Actions](https://github.com/features/actions) 를 사용하여 Dusk 테스트를 실행하는 경우 다음 설정 파일을 시작점으로 사용할 수 있습니다. TravisCI와 마찬가지로 `php artisan serve` 명령을 사용하여 PHP의 내장 웹 서버를 시작합니다.

```yaml
name: CI
on: [push]
jobs:

  dusk-php:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Prepare The Environment
        run: cp .env.example .env
      - name: Create Database
        run: |
          sudo systemctl start mysql
          mysql --user="root" --password="root" -e "CREATE DATABASE 'my-database' character set UTF8mb4 collate utf8mb4_bin;"
      - name: Install Composer Dependencies
        run: composer install --no-progress --prefer-dist --optimize-autoloader
      - name: Generate Application Key
        run: php artisan key:generate
      - name: Upgrade Chrome Driver
        run: php artisan dusk:chrome-driver `/opt/google/chrome/chrome --version | cut -d " " -f3 | cut -d "." -f1`
      - name: Start Chrome Driver
        run: ./vendor/laravel/dusk/bin/chromedriver-linux &
      - name: Run Laravel Server
        run: php artisan serve --no-reload &
      - name: Run Dusk Tests
        env:
          APP_URL: "http://127.0.0.1:8000"
        run: php artisan dusk
      - name: Upload Screenshots
        if: failure()
        uses: actions/upload-artifact@v2
        with:
          name: screenshots
          path: tests/Browser/screenshots
      - name: Upload Console Logs
        if: failure()
        uses: actions/upload-artifact@v2
        with:
          name: console
          path: tests/Browser/console
```
