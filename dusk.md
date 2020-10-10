# 라라벨 Dusk

- [시작하기](#introduction)
- [설치하기](#installation)
    - [크롬 드라이버 설치 관리](#managing-chromedriver-installations)
    - [다른 브라우저 사용하기](#using-other-browsers)
- [시작하기](#getting-started)
    - [테스트 클래스 생성하기](#generating-tests)
    - [테스트 실행하기](#running-tests)
    - [구동환경 처리](#environment-handling)
    - [브라우저 생성하기](#creating-browsers)
    - [브라우저 매크로](#browser-macros)
    - [인증](#authentication)
    - [데이터베이스 마이그레이션](#migrations)
    - [Cookies](#cookies)
    - [스크린 샷 찍기](#taking-a-screenshot)
    - [콘솔 출력을 디스크에 저장하기](#storing-console-output-to-disk)
    - [페이지 소스를 디스크에 저장하기](#storing-page-source-to-disk)
- [Element 조작하기](#interacting-with-elements)
    - [Dusk 선택자](#dusk-selectors)
    - [링크 클릭](#clicking-links)
    - [Text, Values, & Attributes](#text-values-and-attributes)
    - [Form 사용하기](#using-forms)
    - [파일 첨부](#attaching-files)
    - [키보드 사용하기](#using-the-keyboard)
    - [마우스 사용하기](#using-the-mouse)
    - [자바스크립트 대화상자](#javascript-dialogs)
    - [Scoping Selectors](#scoping-selectors)
    - [Elements 기다리기](#waiting-for-elements)
    - [Element가 보이는 곳으로 스크롤하기](#scrolling-an-element-into-view)
    - [Vue Assertions 만들기](#making-vue-assertions)
- [사용 가능한 Assertions](#available-assertions)
- [페이지-Pages](#pages)
    - [페이지 생성하기](#generating-pages)
    - [페이지 설정하기](#configuring-pages)
    - [페이지 탐색](#navigating-to-pages)
    - [단축 셀렉터](#shorthand-selectors)
    - [페이지 메소드](#page-methods)
- [컴포넌트](#components)
    - [컴포넌트 생성하기](#generating-components)
    - [컴포넌트 사용하기](#using-components)
- [CI - 지속적 통합](#continuous-integration)
    - [CircleCI](#running-tests-on-circle-ci)
    - [Codeship](#running-tests-on-codeship)
    - [Heroku CI](#running-tests-on-heroku-ci)
    - [Travis CI](#running-tests-on-travis-ci)
    - [GitHub Actions](#running-tests-on-github-actions)

<a name="introduction"></a>
## 시작하기

라라벨 Dusk는 구성과 사용이 쉬운 브라우저 자동화 및 테스팅 API를 제공합니다. 기본적으로 Dusk는 사용자 머신에 JDK 나 Selenium을 설치하도록 요구하지 않습니다. 대신에 Dusk는 독립적인 [ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/home)를 사용합니다. 그렇긴 하지만, 원하는 경우 다른 Selenium 호환 드라이버를 사용할 수도 있습니다.

<a name="installation"></a>
## 설치하기

시작하기 위해서, 컴포저 의존성에 `laravel/dusk`을 추가해야 합니다.

    composer require --dev laravel/dusk

> {note} 여러분이 Dusk의 서비스 프로바이더를 수동으로 등록하는 경우, 실서버 환경에서는 **절대로** 이를 등록하면 안됩니다. 이렇게 하면 익명의 사용자가 애플리케이션의 인증을 통과할 수가 있습니다.

Dusk 패키지를 설치하고 나서는 `dusk:install` 아티즌 명령어를 실행하십시오:

    php artisan dusk:install

`tests` 디렉토리 안에 `Browser` 디렉토리가 생성되고 예제 테스트가 포함됩니다. 그런 다음에 `.env` 파일에서 `APP_URL` 환경 변수를 설정하십시오. 이 변수는 브라우저에서 애플리케이션에 엑세스 하는데 사용하는 URL과 일치해야 합니다.

테스트를 실행하기 위해서는 `dusk` 아티즌 명령어를 사용합니다. `dusk` 명령어는 `phpunit` 명령어에 전달할 수 있는 모든 인자를 받을 수 있습니다.

    php artisan dusk

마지막으로 `dusk` 명령을 실행했을 때 테스트가 실패했었다면, `dusk:fails` 명령을 사용하여 먼저 실패한 테스트를 다시 실행하여 시간을 아낄 수 있습니다.

    php artisan dusk:fails

<a name="managing-chromedriver-installations"></a>
### 크롬 드라이버 설치 관리

Laravel Dusk에 포함 된 것과 다른 버전의 ChromeDriver를 설치하려면 `dusk:chrome-driver` 명령을 사용할 수 있습니다.

    # Install the latest version of ChromeDriver for your OS...
    php artisan dusk:chrome-driver

    # Install a given version of ChromeDriver for your OS...
    php artisan dusk:chrome-driver 74

    # Install a given version of ChromeDriver for all supported OSs...
    php artisan dusk:chrome-driver --all

> {note} Dusk 를 실행하기 위해서는 `chromedriver` 바이너리가 필요합니다. Dusk를 구동하는데 문제가 있다면, 다음 명령어를 통해서 바이너리가 실행가능해야 합니다. `chmod -R 0755 vendor/laravel/dusk/bin`.

<a name="using-other-browsers"></a>
### 다른 브라우저 사용하기

기본적으로, Dusk는 브라우저 테스트에서 구글 크롬 및 [ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/home)을 사용합니다. 그렇지만, 여러분이 다른 Selenium 서버를 구성하고 원하는 브라우저를 테스트할 수도 있습니다.

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

Dusk 테스트를 생성하기 위해서는 `dusk:make` 아티즌 명령어를 사용합니다. 생성된 테스트 파일은 `tests/Browser` 디렉토리에 저장됩니다.

    php artisan dusk:make LoginTest

<a name="running-tests"></a>
### 테스트 실행하기

브라우저 테스트를 실행하려면, `dusk` 아티즌 명령어를 사용하십시오:

    php artisan dusk

마지막으로 `dusk` 명령을 실행했을 때 테스트가 실패했었다면, `dusk:fails` 명령을 사용하여 먼저 실패한 테스트를 다시 실행하여 시간을 아낄 수 있습니다.

    php artisan dusk:fails

`dusk` 명령어는 일반적으로 PHPUnit 테스트에 전달할 수 있는 인자들을 받을 수 있기 때문에, 지정된 [그룹](https://phpunit.de/manual/current/en/appendixes.annotations.html#appendixes.annotations.group)에 대해서 테스트를 실행할 수도 있습니다.

    php artisan dusk --group=foo

#### 수동으로 ChromeDriver 시작하기

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

또한, ChromeDriver가 9515포트가 아닌 다른 포트를 사용한다면, 이 클래스의 `driver` 메소드를 수정해야합니다.

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

시작하기 위해서, 애플리케이션에 로그인할 수 있는지 확인하는 테스트를 작성해 보겠습니다. 테스트 파일을 생성한 다음에, 이를 수정하여 로그인 페이지로 이동하고, 인증 정보를 입력한다음, "Login" 버튼을 클릭해 보겠습니다. 브라우저 인스턴스를 생성하기 위해서는 `browse` 메소드를 호출하면 됩니다.

    <?php

    namespace Tests\Browser;

    use App\User;
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
        public function testBasicExample()
        {
            $user = factory(User::class)->create([
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

위 예제에서와 같이, `browser` 메소드는 콜백을 전달 받습니다. 브라우저 인스턴스는 Dusk에 의해서 콜백에 전달되며, 애플리케이션과의 인터랙션을 통해서 테스트를 확인하는데 사용됩니다.

#### 여러개의 브라우저 생성하기

경우에 따라 테스트를 제대로 수행하기 위해서 여러개의 브라우저가 필요할 수도 있습니다. 예를 들어 웹 소켓과 상호작용하는 채팅 화면을 테스트 하는 경우 여러개의 브라우저가 필요합니다. 여러개의 브라우저 인스턴스를 생성하기 위해서는 주어진 `browser` 메소드에 하나 이상의 브라우저를 표시한 콜백을 "요청"하면 됩니다.

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

브라우저의 윈도우 사이즈를 조정하려면 `resize` 메소드를 사용하면 됩니다.

    $browser->resize(1920, 1080);

`maximize` 메소드는 브라우저의 윈도우를 최대 크기로 조정하는데 사용합니다.

    $browser->maximize();

`fitContent` 메소드는 컨텐츠의 크기와 일치하도록 브라우저 창의 크기를 조정합니다.

    $browser->fitContent();

테스트에 실패하면 Dusk는 스크린 샷을 만들기 전에 콘텐츠에 맞게 브라우저의 크기를 자동으로 조정합니다. 테스트 내에서 `disableFitOnFailure` 메소드를 호출하여 이 기능을 비활성화 할 수 있습니다.

    $browser->disableFitOnFailure();

`move` 메소드를 사용하여 브라우저 창을 화면의 다른 위치로 이동할 수 있습니다.

    $browser->move(100, 100);

<a name="browser-macros"></a>
### 브라우저 매크로

다양한 테스트에서 재사용할 수 있는 커스텀 브라우저 메소드를 정의한려고 한다면 `Browser` 클래스의 `macro` 메소드를 사용할 수 있습니다. 일반적으로 이 메소드는 [서비스 프로바이더](/docs/{{version}}/providers)의 `boot` 메소드 안에서 호출해야 합니다.

    <?php

    namespace App\Providers;

    use Illuminate\Support\ServiceProvider;
    use Laravel\Dusk\Browser;

    class DuskServiceProvider extends ServiceProvider
    {
        /**
         * Register the Dusk's browser macros.
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

`macro` 함수는 첫번째 인자로 이름을, 두번째 인자로 클로저를 받습니다. 매크로의 클로저는 `Browser` 구현체에서 메소드를 호출할때 실행됩니다.

    $this->browse(function ($browser) use ($user) {
        $browser->visit('/pay')
                ->scrollToElement('#credit-card-details')
                ->assertSee('Enter Credit Card Details');
    });

<a name="authentication"></a>
### 인증

종종, 인증이 필요한 페이지를 테스트 할 수도 있습니다. 모든 테스트에서 로그인 화면에서의 인터렉션을 하지 않아도 되게끔, Dusk의 `loginAs` 메소드를 사용할 수 있습니다. `loginAs` 메소드는 사용자 ID 또는 모델 인스턴스를 전달받습니다.

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
    use Illuminate\Foundation\Testing\DatabaseMigrations;
    use Laravel\Dusk\Chrome;
    use Tests\DuskTestCase;

    class ExampleTest extends DuskTestCase
    {
        use DatabaseMigrations;
    }

<a name="cookies"></a>
### Cookies

`cookie` 메소드를 사용하여 암호화 된 쿠키의 값을 가져 오거나 설정할 수 있습니다.

    $browser->cookie('name');

    $browser->cookie('name', 'Taylor');

암호화되지 않은 쿠키의 값을 가져 오거나 설정하기 위해서는 `plainCookie` 메소드를 사용할 수 있습니다.

    $browser->plainCookie('name');

    $browser->plainCookie('name', 'Taylor');

쿠키를 삭제하기 위해 `deleteCookie` 메소드를 사용할 수 있습니다.

    $browser->deleteCookie('name');

<a name="taking-a-screenshot"></a>
### 스크린 샷 찍기

`screenshot` 메서드를 사용하여 스크린 샷을 찍고 주어진 파일 이름으로 저장할 수 있습니다. 모든 스크린 샷은 `tests/Browser/screenshots` 디렉토리에 저장됩니다.

    $browser->screenshot('filename');

<a name="storing-console-output-to-disk"></a>
### 콘솔 출력을 디스크에 저장하기

`storeConsoleLog` 메소드를 사용하여 주어진 파일 이름으로 콘솔 출력을 디스크에 쓸 수 있습니다. 콘솔 출력은 `tests/Browser/console` 디렉토리에 저장됩니다.

    $browser->storeConsoleLog('filename');

<a name="storing-page-source-to-disk"></a>
### 페이지 소스를 디스크에 저장하기

`storeSource` 메소드를 사용하여 페이지의 현재 소스를 주어진 파일 이름으로 디스크에 쓸 수 있습니다. 페이지 소스는 `tests/Browser/source` 디렉토리에 저장됩니다.

    $browser->storeSource('filename');

<a name="interacting-with-elements"></a>
## Element 조작하기

<a name="dusk-selectors"></a>
### Dusk 선택자

엘리먼츠와 상호 작용할 수있는 좋은 CSS 선택자를 선택하는 것은 Dusk 테스트를 작성하면서 가장 어려운 부분 중 하나입니다. 시간이 지나면 프론트엔드 변경으로 인해 다음과 같은 CSS 선택자가 테스트를 중단시킬 수 있습니다.

    // HTML...

    <button>Login</button>

    // Test...

    $browser->click('.login-page .container div > button');

Dusk 선택자를 사용하면 CSS 선택자를 기억하는 것보다 효과적인 테스트를 작성하는 데 집중할 수 있습니다. 선택자를 정의하려면 HTML 요소에 `dusk` 속성-attribute을 추가하십시오. 그런 다음 선택자 앞에 `@` 접두어를 붙이면 Dusk 테스트에서 첨부 된 엘리먼츠를 조작합니다.

    // HTML...

    <button dusk="login-button">Login</button>

    // Test...

    $browser->click('@login-button');

<a name="clicking-links"></a>
### 링크 클릭하기

링크를 클릭하기 위해서는 브라우저 인스턴스에서 `clickLink` 메소드를 사용하면 됩니다. `clickLink` 메소드는 주어진 텍스트에 해당하는 링크를 클릭합니다.

    $browser->clickLink($linkText);

`seeLink` 메소드를 사용하여 지정한 텍스트로 링크가 걸린 것이 페이지에 표시되는지 확인할 수 있습니다.

    if ($browser->seeLink($linkText)) {
        // ...
    }

> {note} 이 메소드는 jQuery와 상호작용합니다. 웹 페이지에서 jQuery를 사용할 수 없다면 Dusk는 테스트가 수행되는 동안에 자동으로 이를 로딩하여 사용하능한 형태로 만듭니다.

<a name="text-values-and-attributes"></a>
### Text, Values, & Attributes

#### Retrieving & Setting Values

Dusk 는 페이지에 현재 표시된 텍스트, 입력값 그리고 속성들에 대해서 처리할 수 있는 다양한 메소드를 제공합니다. 예를 들어 주어진 selector 와 매칭되는 Element의 "value"를 얻고자 한다면 `value` 메소드를 사용하면 됩니다.

    // Retrieve the value...
    $value = $browser->value('selector');

    // Set the value...
    $browser->value('selector', 'value');

주어진 필드 이름을 가진 input element의 "값"을 얻기 위해 `inputValue` 메소드를 사용할 수 있습니다.

    // Retrieve the value of an input element...
    $inputValue = $browser->inputValue('field');

#### 텍스트 조회하기

`text` 메소드는 주어진 selector 와 매칭되는 element의 텍스트를 조회하는데 사용됩니다.

    $text = $browser->text('selector');

#### 속성 조회하기

`attribute` 메소드는 주어진 selector와 매칭되는 element의 속성을 조회하는데 사용됩니다.

    $attribute = $browser->attribute('selector', 'value');

<a name="using-forms"></a>
### Form 사용하기

#### 값 입력하기

Dusk는 form 과 element와 상호작용할 수 있는 다양한 메소드를 제공합니다. 먼저, input 필드에 값을 입력하는 예쩨를 살펴보겠습니다.

    $browser->type('email', 'taylor@laravel.com');

위에서 보시다시피, `type` 메소드는 필요한 경우 CSS selector를 인자로 받습니다. 만약 CSS selector 가 전달되지 않으면, Dusk는 주어진 `name` 속성을 통해서 input 필드를 찾습니다. 그런 다음에 Dusk는 주어진 `name` 속성에 해당하는 `textarea` 를 찾으려고 시도합니다.

필드의 내용을 유지한채로 문자를 추가하려면 `append` 메소드를 사용하면 됩니다.

    $browser->type('tags', 'foo')
            ->append('tags', ', bar, baz');

`clear` 메소드를 통해서 input 필드의 값을 비울 수 있습니다.

    $browser->clear('email');

Dusk에게 `typeSlowly` 메서드를 사용하여 천천히 입력하도록 지시 할 수 있습니다. 기본적으로 Dusk는 키를 누를 때마다 100 밀리 초 동안 일시 중지됩니다. 키 누름 사이의 시간을 커스터마이징하려면 메서드의 두 번째 인수로 적절한 밀리 초를 전달할 수 있습니다.

    $browser->typeSlowly('mobile', '+1 (202) 555-5555');

    $browser->typeSlowly('mobile', '+1 (202) 555-5555', 300);

`appendSlowly` 메소드를 사용하여 텍스트를 천천히 추가 할 수 있습니다.

    $browser->type('tags', 'foo')
            ->appendSlowly('tags', ', bar, baz');

#### Dropdowns(셀렉트박스)

dropdown 셀렉트 박스에서 값을 선택하려면, `select` 메소드를 사용하면 됩니다. `type` 메소드와 같이 `select` 메소드는 완전한 CSS selector 를 필요로 하지는 않습니다. `select` 메소드에 값이 전달될 때에는, 표시된 텍스트가 아니라, 선택할 값을 전달해야 된다는 것에 주의하십시오:

    $browser->select('size', 'Large');

두번째 인자를 생략하면 랜덤한 값을 선택하게 됩니다.

    $browser->select('size');

#### 체크박스

체크박스 필드를 "체크" 처리하려면 `check` 메소드를 사용하면 됩니다. 다른 input 관련 메소드와 같이, 완전한 CSS selector는 필요하지 않습니다. 완전히 일치하는 selector를 찾을 수 없다면 Dusk는 `name` 속성을 기준으로 매칭되는 체크박스를 찾습니다.

    $browser->check('terms');

    $browser->uncheck('terms');

#### 라디오 버튼

라디오 버튼의 옵션을 "선택" 하려면, `radio` 메소드를 사용하면 됩니다. 다른 input 관련 메소드와 같이, 완전한 CSS selector는 필요하지 않습니다. 완전히 일치하는 selector를 찾을 수 없다면, Dusk는 `name` 과 `value` 속성을 기준으로 매칭되는 라디오를 찾습니다.

    $browser->radio('version', 'php7');

<a name="attaching-files"></a>
### 파일 첨부

`file` input element 에 파일을 첨부하는데에는 `attach` 메소드가 사용됩니다. 다른 input 관련 메소드와 같이, 완전한 CSS selector는 필요하지 않습니다. 완전히 일치하는 selector를 찾을 수 없다면, Dusk는 `name` 속성을 기준으로 매칭되는 file input을 찾습니다.

    $browser->attach('photo', __DIR__.'/photos/me.png');

> {note} 파일 첨부기능을 사용하려면 서버에 `Zip` PHP 확장 기능이 설치되어 있고 활성화 되어 있어야 합니다.

<a name="using-the-keyboard"></a>
### 키보드 사용하기

`keys` 메소드는 `type` 메소드가 제공하는 것 보다 더 복잡한 작업들을 처리할 수 있습니다. 예를 들어, 특수 키를 값을 입력하는 동안 유지할 수 있습니다. 이 예제에서는 주어진 selector에 매칭되는 element에 `taylor`를 입력하는 동안 `shift` 키를 누르고 있는상태로 유지됩니다. `taylor` 가 입력된 후에, `otwell` 이 입력될 때는 특수키를 유지하지 않습니다.

    $browser->keys('selector', ['{shift}', 'taylor'], 'otwell');

애플리케이션에 포함된 기본 CSS selector에서 "단축키"를 전달 할 수도 있습니다.

    $browser->keys('.app', ['{command}', 'j']);

> {tip} 모든 특수키는 `{}` 문자로 감싸여져 있으며, 사용되는 표시자들은 `Facebook\WebDriver\WebDriverKeys` 클래스에 정의되어 있습니다. 이러한 사항은 [GitHub](https://github.com/php-webdriver/php-webdriver/blob/master/lib/WebDriverKeys.php)에서 보다 자세한 내용을 확인할 수 있습니다.

<a name="using-the-mouse"></a>
### 마우스 사용하기

#### Element 클릭하기

`click` 메소드는 주어진 selector에 매칭되는 element를 "클릭"하는데 사용합니다.

    $browser->click('.selector');

`clickAtXPath` 메소드는 주어진 XPath 표현식과 일치하는 요소를 "클릭"하는 데 사용할 수 있습니다.

    $browser->clickAtXPath('//div[@class = "selector"]');

`clickAtPoint` 메소드는 브라우저의 볼 수있는 영역을 기준으로 지정된 좌표 쌍에서 최상위 요소를 "클릭"하는 데 사용할 수 있습니다.

    $browser->clickAtPoint(0, 0);

`doubleClick` 메소드를 사용하여 마우스의 더블 클릭을 시뮬레이션 할 수 있습니다.

    $browser->doubleClick();

`rightClick`메소드는 마우스의 오른쪽 "클릭"을 시뮬레이션하는 데 사용할 수 있습니다.

    $browser->rightClick();

    $browser->rightClick('.selector');

`clickAndHold` 메서드는 마우스 버튼을 클릭하고 누르고 있는 것을 시뮬레이션하는 데 사용할 수 있습니다. 후속 호출로 `releaseMouse` 메서드를 사용하면 이 동작을 중단하고 마우스 버튼을 놓습니다.

    $browser->clickAndHold()
            ->pause(1000)
            ->releaseMouse();

#### Mouseover

`mouseover` 메소드는 주어진 selector에 매칭되는 element에 마우스를 이동하여 올려놓는 작업이 필요할 때 사용합니다.

    $browser->mouseover('.selector');

#### 드래그 & 드롭

`drag` 메소드는 주어진 selector와 매칭되는 element를 다른 element로 드래그 하는데 사용합니다.

    $browser->drag('.from-selector', '.to-selector');

또는, element를 어느 한 방향으로 드래그 할 수 있습니다.

    $browser->dragLeft('.selector', 10);
    $browser->dragRight('.selector', 10);
    $browser->dragUp('.selector', 10);
    $browser->dragDown('.selector', 10);

마지막으로 주어진 오프셋으로 요소를 드래그 할 수 있습니다.

    $browser->dragOffset('.selector', 10, 10);

<a name="javascript-dialogs"></a>
### 자바스크립트 대화상자

Dusk은 JavaScript Dialogs와 상호 작용하는 다양한 메소드를 제공합니다.

    // Wait for a dialog to appear:
    $browser->waitForDialog($seconds = null);

    // Assert that a dialog has been displayed and that its message matches the given value:
    $browser->assertDialogOpened('value');

    // Type the given value in an open JavaScript prompt dialog:
    $browser->typeInDialog('Hello World');

열린 JavaScript 대화 상자를 닫으려면 확인 버튼을 클릭하십시오.

    $browser->acceptDialog();

열린 JavaScript 대화 상자를 닫으려면 취소 버튼을 클릭하십시오 (확인 대화 상자에만 해당).

    $browser->dismissDialog();

<a name="scoping-selectors"></a>
### Selector의 특정 범위를 제한하여 동작하기

때로는 주어진 selector안에서 특정 범위를 지정하여 동작을 수행하기을 원할 수도 있습니다. 예를 들어, 테이블 안에 있는 텍스트를 확인하고, 버튼을 클릭하고자 할 수 있습니다. 이 경우 `with`메소드를 사용하면 됩니다. `with` 메소드에 주어진 콜백안에서 수행되는 모든 동작들은 원래의 selector 에서 범위가 제한됩니다.

    $browser->with('.table', function ($table) {
        $table->assertSee('Hello World')
              ->clickLink('Delete');
    });

때때로 현재 범위 밖에서 검증을 실행해야 할 수도 있습니다. 이를 위해 `elsewhere` 메소드를 사용할 수 있습니다.

     $browser->with('.table', function ($table) {
        // Current scope is `body .table`...
        $browser->elsewhere('.page-title', function ($title) {
            // Current scope is `body .page-title`...
            $title->assertSee('Hello World');
        });
     });

<a name="waiting-for-elements"></a>
### Element 기다리기

자바스크립트를 많이 사용한 애플리케이션을 테스트할 때, 테스트를 수행하기 위해서 특정 element가 표시되기까지 "기다리는" 행동이 필요할 때가 있습니다. Dusk는 이를 아주 쉽게 해결합니다. 다양한 메소드를 사용하여, element가 페이지에 표시될 때까지 기다리거나, 주어진 자바스크립트 표현식이 `true` 가 될 때까지 기다릴 수 있습니다.

#### 시간기준으로 기다리기

주어진 milliseconde 동안 테스트를 일시 정지하려면 `pause` 메소드를 사용합니다.

    $browser->pause(1000);

#### Selector 가 표시되기를 기다리기

`waitFor` 메소드는 주어진 CSS selector 에 해당하는 element가 페이지에 표시될 때까지 테스트 실행을 일시 정지하는데 사용합니다. 기본적으로 이 동작은 exception이 발생하기 전까지 최대 5초동안 테스트를 일시 정지합니다. 필요하다면, 두번재 인자로 커스텀 타임아웃 값을 전달 할 수 있습니다.

    // Wait a maximum of five seconds for the selector...
    $browser->waitFor('.selector');

    // Wait a maximum of one second for the selector...
    $browser->waitFor('.selector', 1);

또한 주어진 selector가 페이지에서 사라질 때까지 일시 정지할 수도 있습니다.

    $browser->waitUntilMissing('.selector');

    $browser->waitUntilMissing('.selector', 1);

#### 사용가능할 때 Selector의 특정 범위를 제한하여 동작하기

때로는, 주어진 selector 가 사용가능해지면, 매칭되는 selector에 작업을 수행하려고 할 수도 있습니다. 예를 들면, 모달 윈도우가 나타나기를 기다렸다가, 모달의 "OK" 버튼을 누른다고 해보겠습니다. 이런 경우 `whenAvailable` 메소를 사용하면 됩니다. 모든 element 작업은 원래의 selector 로 제한되어 주어진 콜백 안에서 수행됩니다

    $browser->whenAvailable('.modal', function ($modal) {
        $modal->assertSee('Hello World')
              ->press('OK');
    });

#### 텍스트가 나타날 때까지 기다리기

`waitForText` 메소드는 페이지에서 주어진 텍스트가 나타날때 까지 기다릴 때 사용됩니다.

    // Wait a maximum of five seconds for the text...
    $browser->waitForText('Hello World');

    // Wait a maximum of one second for the text...
    $browser->waitForText('Hello World', 1);

`waitUntilMissingText` 메소드를 사용하여 표시된 텍스트가 페이지에서 제거 될 때까지 기다릴 수 있습니다.

    // Wait a maximum of five seconds for the text to be removed...
    $browser->waitUntilMissingText('Hello World');

    // Wait a maximum of one second for the text to be removed...
    $browser->waitUntilMissingText('Hello World', 1);

#### 링크가 나타날 때까지 기다리기

`waitForLink` 메소드는 페이지에서 주어진 링크가 나타날때 까지 기다릴 때 사용됩니다.

    // Wait a maximum of five seconds for the link...
    $browser->waitForLink('Create');

    // Wait a maximum of one second for the link...
    $browser->waitForLink('Create', 1);

#### 페이지 경로를 기다리기

`$browser->assertPathIs('/home')`와 같은 경로 assertion을 만들 때 `window.location.pathname`이 비동기적으로 업데이트되면 assertion이 실패 할 수 있습니다. location이 주어진 값이 될 때까지 기다리기 위해서 `waitForLocation` 메소드를 사용할 수 있습니다.

    $browser->waitForLocation('/secret');

이름이 지정된 라우트의 경로도 기다릴 수 있습니다.

    $browser->waitForRoute($routeName, $parameters);

#### 페이지 새로고침 기다리기

페이지가 새로고침 된 후에 assertion을 해야한다면 `waitForReload` 메소드를 사용하십시오.

    $browser->click('.some-action')
            ->waitForReload()
            ->assertSee('something');

#### 자바스크립트 표현식을 통해서 기다리기

때로는 주어진 자바스크립트 표현식이 `true` 일 때까지 테스트 실행을 멈추고 있기를 원할 수도 있습니다. 이럴 때는 `waitUntil` 메소드를 사용하면 됩니다. 이 메소드에 표현식을 전달할 때에는 `return` 키워드나 종료 세미콜론을 포함할 필요가 없습니다.

    // Wait a maximum of five seconds for the expression to be true...
    $browser->waitUntil('App.dataLoaded');

    $browser->waitUntil('App.data.servers.length > 0');

    // Wait a maximum of one second for the expression to be true...
    $browser->waitUntil('App.data.servers.length > 0', 1);

#### Vue의 표현을 기다리기

다음 메소드는 주어진 Vue 컴포넌트의 속성이 주어진 값을 가질 때까지 대기하는 데 사용될 수 있습니다.

    // Wait until the component attribute contains the given value...
    $browser->waitUntilVue('user.name', 'Taylor', '@user');

    // Wait until the component attribute doesn't contain the given value...
    $browser->waitUntilVueIsNot('user.name', null, '@user');

#### 콜백 기다리기

Dusk의 "wait"메소드의 대부분은 기본 `waitUsing` 메소드에 의존합니다. 이 메소드를 사용하면 지정된 콜백이 `true`를 돌려주는 것을 기다릴 수가 있습니다. `waitUsing` 메소드는 대기 할 최대 초 수, Closure가 검증되는 간격, Closure 및 선택적 실패 메시지를 입력받습니다.

    $browser->waitUsing(10, 1, function () use ($something) {
        return $something->isReady();
    }, "Something wasn't ready in time.");

<a name="scrolling-an-element-into-view"></a>
### Element가 보이는 곳으로 스크롤하기

때때로 Element가 브라우저의 볼 수 있는 영역 밖에 있기 때문에 클릭하지 못할 수 있습니다. `scrollIntoView` 메소드는 지정된 선택기의 요소가 뷰 내에있을 때까지 브라우저 창을 스크롤합니다.

    $browser->scrollIntoView('selector')
            ->click('selector');

<a name="making-vue-assertions"></a>
### Vue Assertions 만들기

Dusk마저도 [Vue](https://vuejs.org) 컴포넌트 데이터의 상태에 대한 assertion을 할 수 있습니다. 예를 들어, 애플리케이션에 아래와 같은 Vue 컴포넌트가 있다고 가정합니다.

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

다음과 같이 Vue 컴포넌트의 상태를 assert 할 수 있습니다.

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
- [assertSelectMissingOption](#assert-select-missing-option)
- [assertSelectMissingOptions](#assert-select-missing-options)
- [assertSelectHasOption](#assert-select-has-option)
- [assertValue](#assert-value)
- [assertAttribute](#assert-attribute)
- [assertAriaAttribute](#assert-aria-attribute)
- [assertDataAttribute](#assert-data-attribute)
- [assertVisible](#assert-visible)
- [assertPresent](#assert-present)
- [assertMissing](#assert-missing)
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

페이지 타이틀이 주어진 텍스트와 일치하는지 확인:

    $browser->assertTitle($title);

<a name="assert-title-contains"></a>
#### assertTitleContains

페이지 타이틀이 주어진 텍스트를 포함하는지 확인:

    $browser->assertTitleContains($title);

<a name="assert-url-is"></a>
#### assertUrlIs

현재의 URL이 (쿼리 스트링을 제외하고) 주어진 문자열과 일치 하는지 확인:

    $browser->assertUrlIs($url);

<a name="assert-scheme-is"></a>
#### assertSchemeIs

현재의 URL이 주어진 스키마(scheme)와 일치 하는지 확인:

    $browser->assertSchemeIs($scheme);

<a name="assert-scheme-is-not"></a>
#### assertSchemeIsNot

현재의 URL이 주어진 스키마(scheme)와 일치 하지 않는지 확인:

    $browser->assertSchemeIsNot($scheme);

<a name="assert-host-is"></a>
#### assertHostIs

현재의 URL이 주어진 호스트와 일치 하는지 확인:

    $browser->assertHostIs($host);

<a name="assert-host-is-not"></a>
#### assertHostIsNot

현재의 URL이 주어진 호스트와 일치 하지 않는지 확인:

    $browser->assertHostIsNot($host);

<a name="assert-port-is"></a>
#### assertPortIs

현재의 URL이 주어진 포트와 일치 하는지 확인:

    $browser->assertPortIs($port);

<a name="assert-port-is-not"></a>
#### assertPortIsNot

현재의 URL이 주어진 포트와 일치 하지 않는지 확인:

    $browser->assertPortIsNot($port);

<a name="assert-path-begins-with"></a>
#### assertPathBeginsWith

현재 URL 경로가 주어진 경로로 시작하는지 확인:

    $browser->assertPathBeginsWith($path);

<a name="assert-path-is"></a>
#### assertPathIs

현재 경로가 주어진 경로와 일치하는지 확인:

    $browser->assertPathIs('/home');

<a name="assert-path-is-not"></a>
#### assertPathIsNot

현재 경로가 주어진 경로와 일치하지 않는 것을 확인:

    $browser->assertPathIsNot('/home');

<a name="assert-route-is"></a>
#### assertRouteIs

현재 URL이 주어진 이름의 라우트의 URL과 일치하는지 확인:

    $browser->assertRouteIs($name, $parameters);

<a name="assert-query-string-has"></a>
#### assertQueryStringHas

쿼리 스트링 파라미터가 존재하는지 확인:

    $browser->assertQueryStringHas($name);

쿼리 스트링 파라미터가 존재하고 주어진 값을 가지고 있는지 확인:

    $browser->assertQueryStringHas($name, $value);

<a name="assert-query-string-missing"></a>
#### assertQueryStringMissing

주어진 쿼리 스트링 파라미터가 없다는 것을 확인:

    $browser->assertQueryStringMissing($name);

<a name="assert-fragment-is"></a>
#### assertFragmentIs

현재의 fragment 가 주어진 fragment와 일치하는지 확인:

    $browser->assertFragmentIs('anchor');

<a name="assert-fragment-begins-with"></a>
#### assertFragmentBeginsWith

현재의 fragment 가 주어진 fragment로 시작하는지 확인:

    $browser->assertFragmentBeginsWith('anchor');

<a name="assert-fragment-is-not"></a>
#### assertFragmentIsNot

현재의 fragment 가 주어진 fragment와 일치하지 않는 것을 확인:

    $browser->assertFragmentIsNot('anchor');

<a name="assert-has-cookie"></a>
#### assertHasCookie

주어진 암호화된 쿠키가 존재하는지 확인:

    $browser->assertHasCookie($name);

<a name="assert-has-plain-cookie"></a>

주어진 암호화되지 않은 쿠키가 있는지 확인:

    $browser->assertHasPlainCookie($name);

<a name="assert-cookie-missing"></a>
#### assertCookieMissing

주어진 암호화된 쿠키가 존재하지 않는 것을 확인:

    $browser->assertCookieMissing($name);

<a name="assert-plain-cookie-missing"></a>

주어진 암호화되지 않은 쿠키가 존재하지 않는 것을 확인:

    $browser->assertPlainCookieMissing($name);

<a name="assert-cookie-value"></a>
#### assertCookieValue

암호화된 쿠키가 주어진 값을 가지고 있는지 확인:

    $browser->assertCookieValue($name, $value);

<a name="assert-plain-cookie-value"></a>
#### assertPlainCookieValue

암호화 되지 않은 쿠키가 주어진 값을 가지고 있는지 확인:

    $browser->assertPlainCookieValue($name, $value);

<a name="assert-see"></a>
#### assertSee

현재 페이지에 주어진 텍스트가 존재하는지 확인:

    $browser->assertSee($text);

<a name="assert-dont-see"></a>
#### assertDontSee

현재 페이지에 주어진 텍스트가 존재하지 않는 것을 확인:

    $browser->assertDontSee($text);

<a name="assert-see-in"></a>
#### assertSeeIn

selector 안에서 주어진 텍스트가 존재하는지 확인:

    $browser->assertSeeIn($selector, $text);

<a name="assert-dont-see-in"></a>
#### assertDontSeeIn

selector 안에서 주어진 텍스트가 존재하지 않는 것을 확인:

    $browser->assertDontSeeIn($selector, $text);

<a name="assert-source-has"></a>
#### assertSourceHas

주어진 소스 코드가 현재 페이지에 존재하는지 확인:

    $browser->assertSourceHas($code);

<a name="assert-source-missing"></a>
#### assertSourceMissing

주어진 소스 코드가 현재 페이지에 존재하지 않는 것을 확인:

    $browser->assertSourceMissing($code);

<a name="assert-see-link"></a>
#### assertSeeLink

현재 페이지에 주어진 링크가 존재하는지 확인:

    $browser->assertSeeLink($linkText);

<a name="assert-dont-see-link"></a>
#### assertDontSeeLink

현재 페이지에 주어진 링크가 존재하지 않는 것을 확인:

    $browser->assertDontSeeLink($linkText);

<a name="assert-input-value"></a>
#### assertInputValue

주어진 input 필드가 주어진 값을 가지는지 확인:

    $browser->assertInputValue($field, $value);

<a name="assert-input-value-is-not"></a>
#### assertInputValueIsNot

주어진 input 필드가 주어진 값을 가지고 있지 않을 것을 확인:

    $browser->assertInputValueIsNot($field, $value);

<a name="assert-checked"></a>
#### assertChecked

주어진 체크박스가 체크되어 있는지 확인:

    $browser->assertChecked($field);

<a name="assert-not-checked"></a>
#### assertNotChecked

주어진 체크박스가 체크되어 있지 않은 것을 확인:

    $browser->assertNotChecked($field);

<a name="assert-radio-selected"></a>
#### assertRadioSelected

주어진 라디오박스의 필드가 선택되어 있는지 확인:

    $browser->assertRadioSelected($field, $value);

<a name="assert-radio-not-selected"></a>
#### assertRadioNotSelected

주어진 라디오박스의 필드가 선택되어 있지 않은 것을 확인:

    $browser->assertRadioNotSelected($field, $value);

<a name="assert-selected"></a>
#### assertSelected

주어진 드랍박스(셀렉트박스)에서 주어진 값이 선택되어 있는 것을 확인:

    $browser->assertSelected($field, $value);

<a name="assert-not-selected"></a>
#### assertNotSelected

주어진 드랍박스(셀렉트박스)에서 주어진 값이 선택되어 있지 않은 것을 확인:

    $browser->assertNotSelected($field, $value);

<a name="assert-select-has-options"></a>
#### assertSelectHasOptions

주어진 값의 배열이 선택가능한 옵션으로 제공되는지 확인:

    $browser->assertSelectHasOptions($field, $values);

<a name="assert-select-missing-option"></a>
#### assertSelectMissingOption

주어진 값을 선택할 수 없음을 확인:

    $browser->assertSelectMissingOption($field, $value);

<a name="assert-select-missing-options"></a>
#### assertSelectMissingOptions

주어진 값의 배열이 선택가능하지 않는 옵션으로 제공되는지 확인:

    $browser->assertSelectMissingOptions($field, $values);

<a name="assert-select-has-option"></a>
#### assertSelectHasOption

주어진 값이 필드에서 선택 가능한 값인지 확인:

    $browser->assertSelectHasOption($field, $value);

<a name="assert-value"></a>
#### assertValue

주어진 selector 와 매칭되는 element가 주어진 값을 가지는지 확인:

    $browser->assertValue($selector, $value);

<a name="assert-attribute"></a>
#### assertAttribute

주어진 선택자와 일치하는 요소가 제공된 속성에 주어진 값을 가지는지 확인:

    $browser->assertAttribute($selector, $attribute, $value);

<a name="assert-aria-attribute"></a>
#### assertAriaAttribute

제공된 선택자와 일치하는 요소가 제공된 aria 속성에 지정된 값을 가지고 있는지 확인:

    $browser->assertAriaAttribute($selector, $attribute, $value);

예를 들어, `<button aria-label="Add"></button>`마크업이 주어지면 다음과 같이 `aria-label` 속성에 대해 검증:

    $browser->assertAriaAttribute('button', 'label', 'Add')

<a name="assert-data-attribute"></a>
#### assertDataAttribute

제공된 선택기와 일치하는 요소가 제공된 데이터 속성에 지정된 값을 가지고 있는지 확인:

    $browser->assertDataAttribute($selector, $attribute, $value);

예를 들어 `<tr id="row-1" data-content="attendees"></tr>`마크 업이 주어지면 다음과 같이 `data-label` 속성에 대해 검증:

    $browser->assertDataAttribute('#row-1', 'content', 'attendees')

<a name="assert-visible"></a>
#### assertVisible

주어진 selector 와 매칭되는 element가 화면에 보이는지 확인:

    $browser->assertVisible($selector);

<a name="assert-present"></a>
#### assertPresent

주어진 selector 와 매칭되는 element가 존재하는지 확인:

    $browser->assertPresent($selector);

<a name="assert-missing"></a>
#### assertMissing

주어진 selector 와 매칭되는 element가 화면에 보이지 않는 것을 확인:

    $browser->assertMissing($selector);

<a name="assert-dialog-opened"></a>
#### assertDialogOpened

주어진 메세지가 있는 자바스크립트 다이얼로그가 열려있는지 확인:

    $browser->assertDialogOpened($message);

<a name="assert-enabled"></a>
#### assertEnabled

주어진 필드가 사용가능한지 확인:

    $browser->assertEnabled($field);

<a name="assert-disabled"></a>
#### assertDisabled

주어진 필드가 사용 불가능한지 확인:

    $browser->assertDisabled($field);

<a name="assert-button-enabled"></a>
#### assertButtonEnabled

주어진 버튼이 활성화되어 있는지 확인:

    $browser->assertButtonEnabled($button);

<a name="assert-button-disabled"></a>
#### assertButtonDisabled

주어진 버튼이 비활성화되어 있는지 확인:

    $browser->assertButtonDisabled($button);

<a name="assert-focused"></a>
#### assertFocused

주어진 필드에 포커스가 되었는지 확인:

    $browser->assertFocused($field);

<a name="assert-not-focused"></a>
#### assertNotFocused

주어진 필드에 포커스가 되지 않은 것을 확인:

    $browser->assertNotFocused($field);

<a name="assert-authenticated"></a>
#### assertAuthenticated

사용자가 인증되었는지 확인:

    $browser->assertAuthenticated();

<a name="assert-guest"></a>
#### assertGuest

사용자가 인증되지 않았음을 확인:

    $browser->assertGuest();

<a name="assert-authenticated-as"></a>
#### assertAuthenticatedAs

사용자가 지정된 사용자로 인증되었는지 확인:

    $browser->assertAuthenticatedAs($user);

<a name="assert-vue"></a>
#### assertVue

주어진 Vue 컴포넌트의 속성이 주어진 값과 일치하는지 확인:

    $browser->assertVue($property, $value, $componentSelector = null);

<a name="assert-vue-is-not"></a>
#### assertVueIsNot

주어진 Vue 컴포넌트의 속성이 주어진 값과 일치하지 않는 것을 확인:

    $browser->assertVueIsNot($property, $value, $componentSelector = null);

<a name="assert-vue-contains"></a>
#### assertVueContains

주어진 Vue 컴포넌트의 data 프로퍼티가 배열이고 주어진 값을 포함하는지 확인:

    $browser->assertVueContains($property, $value, $componentSelector = null);

<a name="assert-vue-does-not-contain"></a>
#### assertVueDoesNotContain

주어진 Vue 컴포넌트의 data 프로퍼티가 배열이고 주어진 값을 포함하지 않는 것을 확인:

    $browser->assertVueDoesNotContain($property, $value, $componentSelector = null);

<a name="pages"></a>
## 페이지-Pages

때로는 여러 차례의 복잡한 작업을 순서대로 수행해야합니다. 이렇게하면 테스트를 읽고 이해하기가 어려워 질 수 있습니다. 페이지를 사용하면 단일 메소드를 사용하여 주어진 페이지에서 수행 할 수있는 표현 작업을 정의 할 수 있습니다. 또한 페이지를 사용하면 애플리케이션 또는 단일 페이지의 공통 선택자에 대한 단축키를 정의 할 수 있습니다.

<a name="generating-pages"></a>
### 페이지 생성하기

페이지 객체를 생성하려면 `dusk:page` Artisan 명령을 사용하십시오. 모든 페이지 객체는 `tests/Browser/Pages` 디렉토리에 있습니다.

    php artisan dusk:page Login

<a name="configuring-pages"></a>
### 페이지 설정하기

기본적으로, 페이지는 `url`, `assert`, `elements`의 세 가지 메소드를 가집니다. 우리는 이제 `url`과 `assert` 메소드에 대해서 논의 할 것입니다. `elements` 메소드는 [아래에서 자세히 논의 할 것입니다](#shorthand-selectors)

#### `url` 메소드

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

#### `assert` 메소드

`assert` 메소드는 브라우저가 주어진 페이지에 실제로 접속해 있는지 확인하는 데 필요한 assertion을 만들 수 있습니다. 반드시 이 메소드는 작성할 필요는 없습니다. 그러나 원할 경우 자유롭게 이러한 assertion을 할 수 있습니다. 이러한 assertion은 페이지 탐색시 자동으로 실행됩니다.

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
### 페이지 탐색

페이지가 설정되면 `visit` 메소드를 사용하여 페이지 탐색 할 수 있습니다.

    use Tests\Browser\Pages\Login;

    $browser->visit(new Login);

`visitRoute` 메소드를 사용하여 이름이 지정된 라우트로 이동할 수 있습니다.

    $browser->visitRoute('login');

`back` 및 `forward` 메소드를 사용하여 '뒤로' 및 '앞으로' 이동 할 수 있습니다.

    $browser->back();

    $browser->forward();

페이지를 새로 고침하기 위해 `refresh` 메소드를 사용할 수 있습니다.

    $browser->refresh();

때로는 이미 주어진 페이지에 접속 해 있을 수 있으며 페이지의 선택자와 메소드를 현재 테스트 컨텍스트에 "로드"해야 할 때가 있습니다. 이는 버튼을 누르고 명시적으로 탐색하지 않고 지정된 페이지로 리디렉션 될 때 일반적입니다. 이 상황에서, 당신은 페이지를 로드하기 위해 `on` 메소드를 사용할 수 있습니다.

    use Tests\Browser\Pages\CreatePlaylist;

    $browser->visit('/dashboard')
            ->clickLink('Create Playlist')
            ->on(new CreatePlaylist)
            ->assertSee('@create');

<a name="shorthand-selectors"></a>
### 단축 셀렉터

페이지의 `elements` 메소드는 페이지의 모든 CSS 선택자에 대한 단축키를 빠르고 쉽게 정의 할 수 있도록합니다. 예를 들어, 애플리케이션 로그인 페이지의 "email" 입력 필드에 대한 바로 가기를 정의해보겠습니다.

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

이제 전체 CSS 셀렉터를 사용할 수있는 곳에서 이 단축 셀렉터를 사용할 수 있습니다.

    $browser->type('@email', 'taylor@laravel.com');

#### 전역 단축 셀렉터

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
### 페이지 메소드

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

메소드가 정의되면 페이지를 사용하는 테스트 내에서 메소드를 사용할 수 있습니다. 브라우저 인스턴스가 자동으로 페이지 메소드에 전달됩니다.

    use Tests\Browser\Pages\Dashboard;

    $browser->visit(new Dashboard)
            ->createPlaylist('My Playlist')
            ->assertSee('My Playlist');

<a name="components"></a>
## 컴포넌트

컴포넌트는 Dusk의 "페이지 개체-objects"와 유사하지만 네비게이션바 또는 알림 창과 같이 애플리케이션 전체에서 다시 사용되는 UI 및 기능에 사용됩니다. 따라서 컴포넌트는 특정 URL에 바인딩되지 않습니다.

<a name="generating-components"></a>
### 컴포넌트 생성하기

컴포넌트를 생성하려면 `dusk:component` Artisan 명령을 사용하십시오. 새로운 컴포넌트는 `tests/Browser/Components` 디렉토리에 존재합니다.

    php artisan dusk:component DatePicker

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
        public function selectDate($browser, $year, $month, $day)
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
### 컴포넌트 사용하기

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
## CI - 지속적 통합

> {note} continous integration 설정 파일을 추가하기 전에 `.env.testing` 파일에 인 `APP_URL` 항목의 값이 `http://127.0.0.1:8000`으로 포함되어 있는지 확인하십시오.

<a name="running-tests-on-circle-ci"></a>
### CircleCI

CircleCI를 사용하여 Dusk 테스트를 실행하는 경우 이 설정 파일을 시작점으로 사용할 수 있습니다. TravisCI와 마찬가지로 우리는 `php artisan serve` 명령을 사용하여 PHP의 내장 웹 서버를 시작합니다.

    version: 2
    jobs:
        build:
            steps:
                - run: sudo apt-get install -y libsqlite3-dev
                - run: cp .env.testing .env
                - run: composer install -n --ignore-platform-reqs
                - run: php artisan key:generate
                - run: php artisan dusk:chrome-driver
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

                - store_artifacts:
                    path: tests/Browser/screenshots

                - store_artifacts:
                    path: tests/Browser/console

                - store_artifacts:
                    path: storage/logs

<a name="running-tests-on-codeship"></a>
### Codeship

[Codeship](https://codeship.com)에서 Dusk 테스트를 실행하려면, 다음의 명령어들을 Codeship 프로젝트에 추가하십시오. 이 명령어들은 기본적인 명령어들이며, 필요한 경우 자유롭게 추가할 수 있습니다.

    phpenv local 7.2
    cp .env.testing .env
    mkdir -p ./bootstrap/cache
    composer install --no-interaction --prefer-dist
    php artisan key:generate
    php artisan dusk:chrome-driver
    nohup bash -c "php artisan serve 2>&1 &" && sleep 5
    php artisan dusk

<a name="running-tests-on-heroku-ci"></a>
### Heroku CI

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

[Travis CI](https://travis-ci.org)에서 Dusk 테스트를 수행하기 위해서는 아래에 나오는 `travis.yml` 설정을 사용하십시오. Travis CI는 그래픽 환경이 아니기 때문에, 크롬 브라우저를 구동하기 위해서는 몇가지 별도의 작업을 필요로 합니다. 추가적으로 PHP 내장 브라우저를 구동하기 위해서 `php artisan serve`를 사용할 수 있습니다.

    language: php

    php:
      - 7.3

    addons:
      chrome: stable

    install:
      - cp .env.testing .env
      - travis_retry composer install --no-interaction --prefer-dist --no-suggest
      - php artisan key:generate
      - php artisan dusk:chrome-driver

    before_script:
      - google-chrome-stable --headless --disable-gpu --remote-debugging-port=9222 http://localhost &
      - php artisan serve &

    script:
      - php artisan dusk

<a name="running-tests-on-github-actions"></a>
### GitHub Actions

[Github Actions](https://github.com/features/actions)를 사용하여 Dusk 테스트를 실행하는 경우 이 설정 파일을 시작포인트로 사용할 수 있습니다. TravisCI와 마찬가지로, 우리는 `php artisan serve` 명령을 사용하여 PHP의 내장 웹 서버를 시작합니다.

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
            run: composer install --no-progress --no-suggest --prefer-dist --optimize-autoloader
          - name: Generate Application Key
            run: php artisan key:generate
          - name: Upgrade Chrome Driver
            run: php artisan dusk:chrome-driver `/opt/google/chrome/chrome --version | cut -d " " -f3 | cut -d "." -f1`
          - name: Start Chrome Driver
            run: ./vendor/laravel/dusk/bin/chromedriver-linux &
          - name: Run Laravel Server
            run: php artisan serve &
          - name: Run Dusk Tests
            env:
              APP_URL: "http://127.0.0.1:8000"
            run: php artisan dusk
