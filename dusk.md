# 라라벨 Dusk

- [시작하기](#introduction)
- [설치하기](#installation)
    - [크롬 드라이버 설치 관리](#managing-chromedriver-installations)
    - [다른 브라우저 사용하기](#using-other-browsers)
- [시작하기](#getting-started)
    - [테스트 클래스 생성하기](#generating-tests)
    - [데이터베이스 마이그레이션](#migrations)
    - [테스트 실행하기](#running-tests)
    - [구동환경 처리](#environment-handling)
- [브라우저 기본조작](#browser-basics)
    - [브라우저 생성하기](#creating-browsers)
    - [Navigation](#navigation)
    - [브라우저 창 크기 조정](#resizing-browser-windows)
    - [브라우저 매크로](#browser-macros)
    - [인증](#authentication)
    - [Cookies](#cookies)
    - [JavaScript 실행하기](#executing-javascript)
    - [스크린샷 찍기](#taking-a-screenshot)
    - [콘솔 출력을 디스크에 저장하기](#storing-console-output-to-disk)
    - [페이지 소스를 디스크에 저장하기](#storing-page-source-to-disk)
- [Element 조작하기](#interacting-with-elements)
    - [Dusk 선택자](#dusk-selectors)
    - [Text, Values, & Attributes](#text-values-and-attributes)
    - [폼 조작하기](#interacting-with-forms)
    - [파일 첨부](#attaching-files)
    - [버튼 누르기](#pressing-buttons)
    - [링크 클릭](#clicking-links)
    - [키보드 사용하기](#using-the-keyboard)
    - [마우스 사용하기](#using-the-mouse)
    - [자바스크립트 대화상자](#javascript-dialogs)
    - [Selector의 범위 제한](#scoping-selectors)
    - [Element 기다리기](#waiting-for-elements)
    - [Element가 보이는 곳으로 스크롤하기](#scrolling-an-element-into-view)
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
    - [Heroku CI](#running-tests-on-heroku-ci)
    - [Travis CI](#running-tests-on-travis-ci)
    - [GitHub Actions](#running-tests-on-github-actions)

<a name="introduction"></a>
## 시작하기

[라라벨 Dusk](https://github.com/laravel/dusk) 는 표현력이 풍부하고 사용하기 쉬운 브라우저 자동화 및 테스트 API를 제공합니다. 기본적으로 Dusk는 로컬 컴퓨터에 JDK 또는 Selenium을 설치할 필요가 없습니다. 대신 Dusk는 독립 실행형 [ChromeDriver](https://sites.google.com/chromium.org/driver) 를 설치해 사용합니다. 그러나 원하는 다른 Selenium 호환 드라이버를 자유롭게 사용할 수 있습니다.

<a name="installation"></a>
## 설치하기

시작하려면 [Google Chrome](https://www.google.com/chrome) 을 설치하고 프로젝트에 `laravel/dusk` Composer 의존성을 추가해야 합니다.

    composer require --dev laravel/dusk

> {note} 여러분이 Dusk의 서비스 프로바이더를 수동으로 등록하는 경우, 실서버 환경에서는 **절대로** 이를 등록하면 안됩니다. 이렇게 하면 익명의 사용자가 애플리케이션의 인증을 통과할 수가 있습니다.

Dusk 패키지를 설치한 후 `dusk:install` Artisan 명령을 실행합니다. `dusk:install` 명령은 `tests/Browser` 디렉토리와 Dusk 테스트의 예제를 생성합니다.

    php artisan dusk:install

다음으로 애플리케이션의 `.env` 파일에서 `APP_URL` 환경 변수를 설정합니다. 이 값은 브라우저에서 애플리케이션에 액세스하는 데 사용하는 URL과 일치해야 합니다.

> {tip} [Laravel Sail](/docs/{{version}}/sail)을 사용하여 로컬 개발 환경을 관리하는 경우, [Dusk 테스트 설정 및 실행](/docs/{{version}}/sail#laravel-dusk)에 대한 Sail 문서도 참조하세요.

<a name="managing-chromedriver-installations"></a>
### 크롬 드라이버 설치 관리

라라벨 Dusk에 포함 된 것과 다른 버전의 ChromeDriver를 설치하려면 `dusk:chrome-driver` 명령을 사용할 수 있습니다.

    # Install the latest version of ChromeDriver for your OS...
    php artisan dusk:chrome-driver

    # Install a given version of ChromeDriver for your OS...
    php artisan dusk:chrome-driver 86

    # Install a given version of ChromeDriver for all supported OSs...
    php artisan dusk:chrome-driver --all

    # Install the version of ChromeDriver that matches the detected version of Chrome / Chromium for your OS...
    php artisan dusk:chrome-driver --detect

> {note} Dusk 를 실행하기 위해서는 `chromedriver` 바이너리가 필요합니다. Dusk를 구동하는데 문제가 있다면, 다음 명령어를 통해서 바이너리가 실행가능해야 합니다. `chmod -R 0755 vendor/laravel/dusk/bin`.

<a name="using-other-browsers"></a>
### 다른 브라우저 사용하기

기본적으로 Dusk는 Chrome 및 독립 실행형 [ChromeDriver](https://sites.google.com/chromium.org/driver) 설치를 사용하여 브라우저 테스트를 실행합니다. 그러나 자체 Selenium 서버를 실행하고, 원하는 브라우저에 대한 테스트를 실행할 수 있습니다.

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

<a name="migrations"></a>
### 데이터베이스 마이그레이션

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

> {note} Dusk 테스트를 실행할 때 SQLite 인메모리 데이터베이스를 사용할 수 없습니다. 브라우저는 자체 프로세스 내에서 실행되기 때문에 다른 프로세스의 메모리 내 데이터베이스에 액세스할 수 없습니다.

<a name="running-tests"></a>
### 테스트 실행하기

브라우저 테스트를 실행하려면 `dusk` Artisan 명령을 실행하세요:

    php artisan dusk

마지막으로 `dusk` 명령을 실행했을 때 테스트가 실패했었다면, `dusk:fails` 명령을 사용하여 이전에 실패한 테스트를 다시 실행하여 시간을 아낄 수 있습니다.

    php artisan dusk:fails

`dusk` 명령은 지정된 [그룹](https://phpunit.de/manual/current/en/appendixes.annotations.html#appendixes.annotations.group) 에 대한 테스트만 실행할 수 있도록 입력받는 것처럼, PHPUnit의 일반적으로 입력받는 모든 인수를 입력받습니다.

    php artisan dusk --group=foo

> {tip} 로컬 개발 환경을 관리하기 위해 [Laravel Sail](/docs/{{version}}/sail)을 사용하는 경우 [Dusk 테스트 설정 및 실행](/docs/{{ version}}/sail#laravel-dusk).

<a name="manually-starting-chromedriver"></a>
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
### 구동환경 처리

테스트를 실행할 때 Dusk가 사용할 구동환경을 강제로 지정하려면, 프로젝트 루트 디렉토리에 `.env.dusk.{environment}` 파일을 만들면 됩니다. 예를 들어 `local` 구동환경에 `dusk` 명령어를 실행한다면, `.env.dusk.local` 파일을 만들어야 합니다.

테스트가 실행될 때, Dusk는 `.env` 파일을 백업 파일로 이름을 변경해 놓고, Dusk 구동환경 파일은 `.env` 로 바꿔놓습니다. 테스트가 완료되고 나면 `.env` 파일이 다시 복구됩니다.

<a name="browser-basics"></a>
## 브라우저 기본조작

<a name="creating-browsers"></a>
### 브라우저 생성하기

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

위의 예에서 볼 수 있듯이 `browse` 메소드는 클로저를 입력받습니다. 브라우저 인스턴스는 Dusk에 의해 이 클로저에 자동으로 전달되며 애플리케이션과 상호 작용하고 애플리케이션에 대해 검증하는 데 사용되는 기본 개체입니다.

<a name="creating-multiple-browsers"></a>
#### 여러개의 브라우저 생성하기

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

`visit` 메소드는 애플리케이션 내에서 주어진 URI로 이동하는 데 사용할 수 있습니다.

    $browser->visit('/login');

`visitRoute` 메소드를 사용하여 [named route](/docs/{{version}}/routing#named-routes)로 이동할 수 있습니다.

    $browser->visitRoute('login');

`back` 및 `forward` 방법을 사용하여 "back" 및 "forward"를 탐색할 수 있습니다.

    $browser->back();

    $browser->forward();

`refresh` 메서드를 사용하여 페이지를 새로 고칠 수 있습니다.

    $browser->refresh();

<a name="resizing-browser-windows"></a>
### 브라우저 창 크기 조정

브라우저의 윈도우 사이즈를 조정하려면 `resize` 메소드를 사용하면 됩니다.

    $browser->resize(1920, 1080);

`maximize` 메소드는 브라우저의 윈도우를 최대 크기로 조정하는데 사용합니다.

    $browser->maximize();

`fitContent` 메서드는 콘텐츠 크기에 맞게 브라우저 창의 크기를 조정합니다.

    $browser->fitContent();

테스트에 실패하면 Dusk는 스크린 샷을 만들기 전에 콘텐츠에 맞게 브라우저의 크기를 자동으로 조정합니다. 테스트 내에서 `disableFitOnFailure` 메소드를 호출하여 이 기능을 비활성화 할 수 있습니다.

    $browser->disableFitOnFailure();

`move` 메소드를 사용하여 브라우저 창을 화면의 다른 위치로 이동할 수 있습니다.

    $browser->move($x = 100, $y = 100);

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

`macro` 함수는 이름을 첫 번째 인수로 받아들이고 클로저를 두 번째 인수로 받아들입니다. 매크로의 클로저는 `Browser` 인스턴스의 메소드로 매크로를 호출할 때 실행됩니다.

    $this->browse(function ($browser) use ($user) {
        $browser->visit('/pay')
                ->scrollToElement('#credit-card-details')
                ->assertSee('Enter Credit Card Details');
    });

<a name="authentication"></a>
### 인증

종종 인증이 필요한 페이지를 테스트하게 됩니다. 모든 테스트 중에 애플리케이션의 로그인 화면과 상호 작용하는 것을 피하기 위해 Dusk의 `loginAs` 메소드를 사용할 수 있습니다. `loginAs` 메소드는 인증 가능한 모델 또는 인증 가능한 모델 인스턴스와 연결된 기본 키를 입력받습니다.

    use App\Models\User;

    $this->browse(function ($browser) {
        $browser->loginAs(User::find(1))
              ->visit('/home');
    });

> {note} `loginAs` 메소드를 사용하면, 파일 내의 모든 테스트에서 사용자 세션이 유지됩니다.

<a name="cookies"></a>
### Cookies

`cookie` 메소드를 사용하여 암호화된 쿠키의 값을 가져오거나 설정할 수 있습니다. 기본적으로 라라벨이 생성한 모든 쿠키는 암호화됩니다.

    $browser->cookie('name');

    $browser->cookie('name', 'Taylor');

암호화되지 않은 쿠키의 값을 가져 오거나 설정하기 위해서는 `plainCookie` 메소드를 사용할 수 있습니다.

    $browser->plainCookie('name');

    $browser->plainCookie('name', 'Taylor');

쿠키를 삭제하기 위해 `deleteCookie` 메소드를 사용할 수 있습니다.

    $browser->deleteCookie('name');

<a name="executing-javascript"></a>
### JavaScript 실행하기

`script` 메서드를 사용하여 브라우저 내에서 임의의 JavaScript 문을 실행할 수 있습니다.

    $browser->script('document.documentElement.scrollTop = 0');

    $browser->script([
        'document.body.scrollTop = 0',
        'document.documentElement.scrollTop = 0',
    ]);

    $output = $browser->script('return window.location.pathname');

<a name="taking-a-screenshot"></a>
### 스크린 샷 찍기

`screenshot` 메서드를 사용하여 스크린 샷을 찍고 주어진 파일 이름으로 저장할 수 있습니다. 모든 스크린 샷은 `tests/Browser/screenshots` 디렉토리에 저장됩니다.

    $browser->screenshot('filename');

<a name="storing-console-output-to-disk"></a>
### 콘솔 출력을 디스크에 저장하기

`storeConsoleLog` 메소드를 사용하여 현재 브라우저의 콘솔 출력을 주어진 파일 이름으로 디스크에 쓸 수 있습니다. 콘솔 출력은 `tests/Browser/console` 디렉토리에 저장됩니다.

    $browser->storeConsoleLog('filename');

<a name="storing-page-source-to-disk"></a>
### 페이지 소스를 디스크에 저장하기

`storeSource` 메소드를 사용하여 현재 페이지의 소스를 주어진 파일 이름으로 디스크에 쓸 수 있습니다. 페이지 소스는 `tests/Browser/source` 디렉토리에 저장됩니다.

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

Dusk 선택자를 사용하면 CSS 선택자를 외우는 대신 효과적인 테스트 작성에 집중할 수 있습니다. 선택자를 정의하려면 HTML 요소에 `dusk` 속성을 추가하세요. 그런 다음 Dusk 브라우저와 상호 작용할 때 선택자에 `@` 접두사를 붙여 테스트 내에서 연결된 요소를 조작합니다.

    // HTML...

    <button dusk="login-button">Login</button>

    // Test...

    $browser->click('@login-button');

<a name="text-values-and-attributes"></a>
### Text, Values, & Attributes

<a name="retrieving-setting-values"></a>
#### 값 검색 및 설정

Dusk는 현재 값, 표시 텍스트 및 페이지 요소의 속성과 상호 작용하기 위한 여러 메서드를 제공합니다. 예를 들어, 주어진 CSS 또는 Dusk 선택자와 일치하는 요소의 "값"을 얻으려면 `value` 메소드를 사용하십시오.

    // Retrieve the value...
    $value = $browser->value('selector');

    // Set the value...
    $browser->value('selector', 'value');

주어진 필드 이름을 가진 input element의 "값"을 얻기 위해 `inputValue` 메소드를 사용할 수 있습니다.

    $value = $browser->inputValue('field');

<a name="retrieving-text"></a>
#### 텍스트 조회하기

`text` 메소드는 주어진 selector 와 매칭되는 element의 텍스트를 조회하는데 사용됩니다.

    $text = $browser->text('selector');

<a name="retrieving-attributes"></a>
#### 속성 조회하기

마지막으로 `attribute` 메소드를 사용하여 주어진 선택자와 일치하는 요소의 속성 값을 검색할 수 있습니다.

    $attribute = $browser->attribute('selector', 'value');

<a name="interacting-with-forms"></a>
### 폼 조작하기

<a name="typing-values"></a>
#### 값 입력하기

Dusk는 form 과 element와 상호작용할 수 있는 다양한 메소드를 제공합니다. 먼저, input 필드에 값을 입력하는 예쩨를 살펴보겠습니다.

    $browser->type('email', 'taylor@laravel.com');

메서드는 필요한 경우 이름만 입력하고, CSS 선택자를 `type` 메서드에 전달할 필요는 없습니다. CSS 선택자가 제공되지 않으면 Dusk는 주어진 `name` 속성으로 `input` 또는 `textarea` 필드를 검색합니다.

필드의 내용을 유지한채로 문자를 추가하려면 `append` 메소드를 사용하면 됩니다.

    $browser->type('tags', 'foo')
            ->append('tags', ', bar, baz');

`clear` 메소드를 통해서 input 필드의 값을 비울 수 있습니다.

    $browser->clear('email');

`typeSlowly` 메서드를 사용하여 Dusk에게 천천히 입력하도록 지시할 수 있습니다. 기본적으로 Dusk는 키 누름 사이에 100밀리초 동안 일시 중지됩니다. 키 누름 사이의 시간을 사용자 지정하려면 메서드에 대한 세 번째 인수로 적절한 밀리초 수를 전달할 수 있습니다.

    $browser->typeSlowly('mobile', '+1 (202) 555-5555');

    $browser->typeSlowly('mobile', '+1 (202) 555-5555', 300);

`appendSlowly` 메소드를 사용하여 텍스트를 천천히 추가 할 수 있습니다.

    $browser->type('tags', 'foo')
            ->appendSlowly('tags', ', bar, baz');

<a name="dropdowns"></a>
#### Dropdowns(셀렉트박스)

`select` 요소에서 사용 가능한 값을 선택하려면 `select` 메서드를 사용할 수 있습니다. `type` 메소드와 마찬가지로 `select` 메소드에는 전체 CSS 선택자가 필요하지 않습니다. `select` 메소드에 값을 전달할 때 표시 텍스트 대신 기본 옵션 값을 전달해야 합니다.

    $browser->select('size', 'Large');

두 번째 인수를 생략하여 임의의 옵션을 선택할 수 있습니다.

    $browser->select('size');

`select` 메소드에 대한 두 번째 인수로 배열을 제공하면 메소드에 여러 옵션을 선택하도록 지시할 수 있습니다.

    $browser->select('categories', ['Art', 'Music']);

<a name="checkboxes"></a>
#### 체크박스

체크박스 입력을 "확인"하려면 `check` 메서드를 사용할 수 있습니다. 다른 많은 입력 관련 메서드과 마찬가지로 전체 CSS 선택자가 필요하지 않습니다. CSS 선택자 일치 항목을 찾을 수 없으면, Dusk는 일치하는 `name` 속성이 있는 체크박스를 검색합니다.

    $browser->check('terms');

`uncheck` 메소드는 체크박스 입력을 "선택 해제"하는 데 사용할 수 있습니다.

    $browser->uncheck('terms');

<a name="radio-buttons"></a>
#### 라디오 버튼

`radio` 입력 옵션을 "선택"하려면 `radio` 메서드를 사용할 수 있습니다. 다른 많은 입력 관련 방법과 마찬가지로 전체 CSS 선택자가 필요하지 않습니다. CSS 선택자 일치 항목을 찾을 수 없으면 Dusk는 `name` 및 `value` 속성이 일치하는 `radio` 입력을 검색합니다.

    $browser->radio('size', 'large');

<a name="attaching-files"></a>
### 파일 첨부

`attach` 메소드는 파일을 `file` 입력 요소에 첨부하는 데 사용할 수 있습니다. 다른 많은 입력 관련 방법과 마찬가지로 전체 CSS 선택자가 필요하지 않습니다. CSS 선택자 일치 항목을 찾을 수 없으면 Dusk는 일치하는 `name` 속성이 있는 `file` 입력을 검색합니다.

    $browser->attach('photo', __DIR__.'/photos/mountains.png');

> {note} 파일 첨부기능을 사용하려면 서버에 `Zip` PHP 확장 기능이 설치되어 있고 활성화 되어 있어야 합니다.

<a name="pressing-buttons"></a>
### 버튼 누르기

'press' 메서드는 페이지의 버튼 요소를 클릭하는 데 사용할 수 있습니다. 'press' 메서드에 지정된 첫 번째 인수는 버튼의 표시 텍스트 또는 CSS Dusk 선택자일 수 있습니다.

    $browser->press('Login');

양식을 제출할 때 많은 애플리케이션이 양식 제출 버튼을 누른 후 비활성화했다가 양식 제출의 HTTP 요청이 완료되면 버튼을 다시 활성화합니다. 버튼을 누르고 버튼이 다시 활성화될 때까지 기다리려면 `pressAndWaitFor` 메서드를 사용할 수 있습니다.

    // Press the button and wait a maximum of 5 seconds for it to be enabled...
    $browser->pressAndWaitFor('Save');

    // Press the button and wait a maximum of 1 second for it to be enabled...
    $browser->pressAndWaitFor('Save', 1);

<a name="clicking-links"></a>
### 링크 클릭

링크를 클릭하려면 브라우저 인스턴스에서 `clickLink` 메서드를 사용할 수 있습니다. `clickLink` 메소드는 주어진 표시 텍스트가 있는 링크를 클릭합니다.

    $browser->clickLink($linkText);

'seeLink' 메서드를 사용하여 주어진 표시 텍스트가 있는 링크가 페이지에 표시되는지 확인할 수 있습니다.

    if ($browser->seeLink($linkText)) {
        // ...
    }

> {note} 이 메소드는 jQuery와 상호 작용합니다. 페이지에서 jQuery를 사용할 수 없는 경우 Dusk는 테스트 기간 동안 사용할 수 있도록 페이지에 자동으로 삽입합니다.

<a name="using-the-keyboard"></a>
### 키보드 사용하기

`keys` 메소드를 사용하면 `type` 메소드에서 일반적으로 허용하는 것보다 더 복잡한 입력 시퀀스를 주어진 요소에 제공할 수 있습니다. 예를 들어 Dusk에 값을 입력하는 동안 보조 키를 유지하도록 지시할 수 있습니다. 이 예에서 `taylor`가 주어진 선택자와 일치하는 요소에 입력되는 동안 `shift` 키가 유지됩니다. `taylor`를 입력한 후 `swift`는 수식 키 없이 입력됩니다.

    $browser->keys('selector', ['{shift}', 'taylor'], 'swift');

`keys` 메소드의 또 다른 유용한 사용 사례는 "키보드 단축키" 조합을 애플리케이션의 기본 CSS 선택자로 보내는 것입니다.

    $browser->keys('.app', ['{command}', 'j']);

> {tip} `{command}`와 같은 모든 수식 키는 `{}` 문자로 래핑되며, [GitHub에서 찾을 수 있는] `Facebook\WebDriver\WebDriverKeys` 클래스에 정의된 상수와 일치합니다(https: github.comphp-webdriverphp-webdriverblobmasterlibWebDriverKeys.php).

<a name="using-the-mouse"></a>
### 마우스 사용하기

<a name="clicking-on-elements"></a>
#### Element 클릭하기

`click` 메소드는 주어진 CSS 또는 Dusk 선택자와 일치하는 요소를 클릭하는 데 사용할 수 있습니다.

    $browser->click('.selector');

`clickAtXPath` 메소드는 주어진 XPath 표현식과 일치하는 요소를 클릭하는 데 사용할 수 있습니다.

    $browser->clickAtXPath('//div[@class = "selector"]');

`clickAtPoint` 메소드는 브라우저의 볼 수 있는 영역을 기준으로 주어진 좌표 쌍에서 최상위 요소를 클릭하는 데 사용할 수 있습니다.

    $browser->clickAtPoint($x = 0, $y = 0);

`doubleClick` 메서드를 사용하여 마우스 더블 클릭을 시뮬레이션할 수 있습니다.

    $browser->doubleClick();

`rightClick` 메서드를 사용하여 마우스 오른쪽 클릭을 시뮬레이션할 수 있습니다.

    $browser->rightClick();

    $browser->rightClick('.selector');

`clickAndHold` 메서드는 마우스 버튼을 클릭하고 누르고 있는 것을 시뮬레이션하는 데 사용할 수 있습니다. 후속 호출로 `releaseMouse` 메서드를 사용하면 이 동작을 중단하고 마우스 버튼을 놓습니다.

    $browser->clickAndHold()
            ->pause(1000)
            ->releaseMouse();

<a name="mouseover"></a>
#### Mouseover

주어진 CSS 또는 Dusk 선택자와 일치하는 요소 위로 마우스를 이동해야 할 때 `mouseover` 메소드를 사용할 수 있습니다.

    $browser->mouseover('.selector');

<a name="drag-drop"></a>
#### 드래그 & 드롭

`drag` 메소드는 주어진 selector와 매칭되는 element를 다른 element로 드래그 하는데 사용합니다.

    $browser->drag('.from-selector', '.to-selector');

또는, element를 어느 한 방향으로 드래그 할 수 있습니다.

    $browser->dragLeft('.selector', $pixels = 10);
    $browser->dragRight('.selector', $pixels = 10);
    $browser->dragUp('.selector', $pixels = 10);
    $browser->dragDown('.selector', $pixels = 10);

마지막으로 주어진 오프셋으로 요소를 드래그 할 수 있습니다.

    $browser->dragOffset('.selector', $x = 10, $y = 10);

<a name="javascript-dialogs"></a>
### 자바스크립트 대화상자

Dusk는 JavaScript 대화 상자와 상호 작용하는 다양한 메서드를 제공합니다. 예를 들어 `waitForDialog` 메서드를 사용하여 JavaScript 대화 상자가 나타날 때까지 기다릴 수 있습니다. 이 메서드는 대화 상자가 나타날 때까지 몇 초를 기다려야 하는지를 나타내는 선택적 인수를 허용합니다.

    $browser->waitForDialog($seconds = null);

`assertDialogOpened` 메소드는 대화 상자가 표시되고 주어진 메시지를 포함하는지 확인하는 데 사용할 수 있습니다.

    $browser->assertDialogOpened('Dialog message');

JavaScript 대화 상자에 프롬프트가 포함되어 있으면 `typeInDialog` 메서드를 사용하여 프롬프트에 값을 입력할 수 있습니다.

    $browser->typeInDialog('Hello World');

"확인" 버튼을 클릭하여 열려 있는 JavaScript 대화 상자를 닫으려면 `acceptDialog` 메서드를 호출할 수 있습니다.

    $browser->acceptDialog();

"취소" 버튼을 클릭하여 열려 있는 JavaScript 대화 상자를 닫으려면 `dismissDialog` 메서드를 호출할 수 있습니다.

    $browser->dismissDialog();

<a name="scoping-selectors"></a>
### Selector의 범위 제한

주어진 선택자 내에서 모든 작업의 범위를 지정하는 동안 여러 작업을 수행하려는 경우가 있습니다. 예를 들어, 일부 텍스트가 테이블 내에만 존재한다고 검증한 다음 해당 테이블 내에서 버튼을 클릭할 수 있습니다. 이를 수행하기 위해 `with` 메소드를 사용할 수 있습니다. 'with' 메소드에 제공된 클로저 내에서 수행되는 모든 작업은 원래 선택자로 범위가 지정됩니다.

    $browser->with('.table', function ($table) {
        $table->assertSee('Hello World')
              ->clickLink('Delete');
    });

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
### Element 기다리기

JavaScript를 광범위하게 사용하는 애플리케이션을 테스트할 때 테스트를 진행하기 전에 특정 요소나 데이터를 사용할 수 있을 때까지 "대기"해야 하는 경우가 종종 있습니다. 황혼은 이것을 간단하게 만듭니다. 다양한 메서드를 사용하여 요소가 페이지에 표시될 때까지 기다리거나 주어진 JavaScript 표현식이 `true`로 평가될 때까지 기다릴 수도 있습니다.

<a name="waiting"></a>
#### 시간기준으로 기다리기

주어진 시간(밀리초) 동안 테스트를 일시 중지해야 하는 경우 `pause' 메서드를 사용하세요.

    $browser->pause(1000);

<a name="waiting-for-selectors"></a>
#### Selector 가 표시되기를 기다리기

`waitFor` 메소드는 주어진 CSS 또는 Dusk 선택자와 일치하는 요소가 페이지에 표시될 때까지 테스트 실행을 일시 중지하는 데 사용할 수 있습니다. 기본적으로 예외가 발생하기 전에 최대 5초 동안 테스트를 일시 중지합니다. 필요한 경우 메서드에 대한 두 번째 인수로 사용자 지정 시간 초과 임계값을 전달할 수 있습니다.

    // Wait a maximum of five seconds for the selector...
    $browser->waitFor('.selector');

    // Wait a maximum of one second for the selector...
    $browser->waitFor('.selector', 1);

주어진 선택자와 일치하는 요소에 주어진 텍스트가 포함될 때까지 기다릴 수도 있습니다.

    // Wait a maximum of five seconds for the selector to contain the given text...
    $browser->waitForTextIn('.selector', 'Hello World');

    // Wait a maximum of one second for the selector to contain the given text...
    $browser->waitForTextIn('.selector', 'Hello World', 1);

주어진 선택자와 일치하는 요소가 페이지에서 제거 될 때까지 기다릴 수도 있습니다.

    // Wait a maximum of five seconds until the selector is missing...
    $browser->waitUntilMissing('.selector');

    // Wait a maximum of one second until the selector is missing...
    $browser->waitUntilMissing('.selector', 1);

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
#### 사용가능할 때 Selector의 특정 범위를 제한하여 동작하기

때때로 주어진 선택자와 일치하는 요소가 나타날 때까지 기다렸다가 요소와 상호 작용할 수 있습니다. 예를 들어, 모달 창을 사용할 수 있을 때까지 기다렸다가 모달 내에서 "확인" 버튼을 누를 수 있습니다. 이를 수행하기 위해 `whenAvailable` 메서드를 사용할 수 있습니다. 주어진 클로저 내에서 수행되는 모든 요소 작업은 원래 선택자로 범위가 지정됩니다.

    $browser->whenAvailable('.modal', function ($modal) {
        $modal->assertSee('Hello World')
              ->press('OK');
    });

<a name="waiting-for-text"></a>
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

<a name="waiting-for-links"></a>
#### 링크가 나타날 때까지 기다리기

`waitForLink` 메소드는 페이지에서 주어진 링크가 나타날때 까지 기다릴 때 사용됩니다.

    // Wait a maximum of five seconds for the link...
    $browser->waitForLink('Create');

    // Wait a maximum of one second for the link...
    $browser->waitForLink('Create', 1);

<a name="waiting-on-the-page-location"></a>
#### 페이지 경로를 기다리기

`$browser->assertPathIs('/home')`와 같은 경로에 대한 검증을 만들 때 `window.location.pathname`이 비동기식으로 업데이트되면 검증이 실패할 수 있습니다. `waitForLocation` 메소드를 사용하여 위치가 주어진 값이 될 때까지 기다릴 수 있습니다.

    $browser->waitForLocation('/secret');

`waitForLocation` 메소드를 사용하여 현재 창 위치가 정규화된 URL이 될 때까지 기다릴 수도 있습니다.

    $browser->waitForLocation('https://example.com/path');

[이름이 지정된 라우트](/docs/{{version}}/routing#named-routes) 위치를 기다릴 수도 있습니다.

    $browser->waitForRoute($routeName, $parameters);

<a name="waiting-for-page-reloads"></a>
#### 페이지 새로고침 기다리기

작업을 수행한 후 페이지가 다시 로드될 때까지 기다려야 하는 경우 `waitForReload` 메서드를 사용하세요.

    use Laravel\Dusk\Browser;

    $browser->waitForReload(function (Browser $browser) {
        $browser->press('Submit');
    })
    ->assertSee('Success!');

일반적으로 버튼을 클릭한 후 페이지가 다시 로드될 때까지 기다려야 하므로 편의를 위해 `clickAndWaitForReload` 메서드를 사용할 수 있습니다.

    $browser->clickAndWaitForReload('.selector')
            ->assertSee('something');

<a name="waiting-on-javascript-expressions"></a>
#### 자바스크립트 표현식을 통해서 기다리기

때로는 주어진 자바스크립트 표현식이 `true` 일 때까지 테스트 실행을 멈추고 있기를 원할 수도 있습니다. 이럴 때는 `waitUntil` 메소드를 사용하면 됩니다. 이 메소드에 표현식을 전달할 때에는 `return` 키워드나 종료 세미콜론을 포함할 필요가 없습니다.

    // Wait a maximum of five seconds for the expression to be true...
    $browser->waitUntil('App.data.servers.length > 0');

    // Wait a maximum of one second for the expression to be true...
    $browser->waitUntil('App.data.servers.length > 0', 1);

<a name="waiting-on-vue-expressions"></a>
#### Vue의 표현을 기다리기

`waitUntilVue` 및 `waitUntilVueIsNot` 메소드는 [Vue component](https://vuejs.org) 속성이 주어진 값을 가질 때까지 대기하는 데 사용할 수 있습니다.

    // Wait until the component attribute contains the given value...
    $browser->waitUntilVue('user.name', 'Taylor', '@user');

    // Wait until the component attribute doesn't contain the given value...
    $browser->waitUntilVueIsNot('user.name', null, '@user');

<a name="waiting-with-a-callback"></a>
#### 콜백 기다리기

Dusk의 많은 "wait" 메소드는 기본 `waitUsing` 메소드에 의존합니다. 주어진 클로저가 `true`를 반환할 때까지 기다리기 위해 이 메서드를 직접 사용할 수 있습니다. `waitUsing` 메소드는 대기할 최대 시간(초), 클로저가 검사되어야 하는 간격, 클로저 및 선택적 실패 메시지를 입력해야합니다.

    $browser->waitUsing(10, 1, function () use ($something) {
        return $something->isReady();
    }, "Something wasn't ready in time.");

<a name="scrolling-an-element-into-view"></a>
### Element가 보이는 곳으로 스크롤하기

때때로 Element가 브라우저의 볼 수 있는 영역 밖에 있기 때문에 클릭하지 못할 수 있습니다. `scrollIntoView` 메소드는 지정된 선택자의 요소가 뷰 내에있을 때까지 브라우저 창을 스크롤합니다.

    $browser->scrollIntoView('.selector')
            ->click('.selector');

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

    $browser->assertPathBeginsWith('/home');

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

현재 URL이 지정된 [이름이 지정된 라우트](/docs/{{version}}/routing#named-routes) URL과 일치하는지 확인:

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

URL의 현재 해시 조각이 주어진 조각과 일치하는지 확인:

    $browser->assertFragmentIs('anchor');

<a name="assert-fragment-begins-with"></a>
#### assertFragmentBeginsWith

URL의 현재 해시 값이 주어진 값으로 시작하는지 확인:

    $browser->assertFragmentBeginsWith('anchor');

<a name="assert-fragment-is-not"></a>
#### assertFragmentIsNot

URL의 현재 해시 값이 주어진 값과 일치하지 않는지 확인:

    $browser->assertFragmentIsNot('anchor');

<a name="assert-has-cookie"></a>
#### assertHasCookie

주어진 암호화된 쿠키가 존재하는지 확인:

    $browser->assertHasCookie($name);

<a name="assert-has-plain-cookie"></a>
#### assertHasPlainCookie

주어진 암호화되지 않은 쿠키가 있는지 확인:

    $browser->assertHasPlainCookie($name);

<a name="assert-cookie-missing"></a>
#### assertCookieMissing

주어진 암호화된 쿠키가 존재하지 않는 것을 확인:

    $browser->assertCookieMissing($name);

<a name="assert-plain-cookie-missing"></a>
#### assertPlainCookieMissing

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

<a name="assert-see-anything-in"></a>
#### assertSeeAnythingIn

선택자 내에 텍스트가 있는지 확인:

    $browser->assertSeeAnythingIn($selector);

<a name="assert-see-nothing-in"></a>
#### assertSeeNothingIn

선택자 내에 텍스트가 없는지 확인:

    $browser->assertSeeNothingIn($selector);

<a name="assert-script"></a>
#### assertScript

주어진 값이 JavaScript 표현식인지 확인:

    $browser->assertScript('window.isLoaded')
            ->assertScript('document.readyState', 'complete');

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

<a name="assert-select-missing-options"></a>
#### assertSelectMissingOptions

주어진 값의 배열이 선택가능하지 않는 옵션으로 제공되는지 확인:

    $browser->assertSelectMissingOptions($field, $values);

<a name="assert-select-has-option"></a>
#### assertSelectHasOption

주어진 값이 필드에서 선택 가능한 값인지 확인:

    $browser->assertSelectHasOption($field, $value);

<a name="assert-select-missing-option"></a>
#### assertSelectMissingOption

주어진 값을 선택할 수 없음을 확인:

    $browser->assertSelectMissingOption($field, $value);

<a name="assert-value"></a>
#### assertValue

주어진 selector 와 매칭되는 element가 주어진 값을 가지는지 확인:

    $browser->assertValue($selector, $value);

<a name="assert-value-is-not"></a>
#### assertValueIsNot

주어진 선택자와 일치하는 요소에 주어진 값이 없음을 확인:

    $browser->assertValueIsNot($selector, $value);

<a name="assert-attribute"></a>
#### assertAttribute

주어진 선택자와 일치하는 요소가 제공된 속성에 주어진 값을 가지는지 확인:

    $browser->assertAttribute($selector, $attribute, $value);

<a name="assert-attribute-contains"></a>
#### assertAttributeContains

주어진 선택자와 일치하는 요소가 제공된 속성에 주어진 값을 포함하는지 확인:

    $browser->assertAttributeContains($selector, $attribute, $value);

<a name="assert-aria-attribute"></a>
#### assertAriaAttribute

제공된 선택자와 일치하는 요소가 제공된 aria 속성에 지정된 값을 가지고 있는지 확인:

    $browser->assertAriaAttribute($selector, $attribute, $value);

예를 들어, `<button aria-label="Add"></button>`마크업이 주어지면 다음과 같이 `aria-label` 속성에 대해 검증:

    $browser->assertAriaAttribute('button', 'label', 'Add')

<a name="assert-data-attribute"></a>
#### assertDataAttribute

제공된 선택자와 일치하는 요소가 제공된 데이터 속성에 지정된 값을 가지고 있는지 확인:

    $browser->assertDataAttribute($selector, $attribute, $value);

예를 들어 `<tr id="row-1" data-content="attendees"></tr>`마크 업이 주어지면 다음과 같이`data-label` 속성에 대해 검증:

    $browser->assertDataAttribute('#row-1', 'content', 'attendees')

<a name="assert-visible"></a>
#### assertVisible

주어진 selector 와 매칭되는 element가 화면에 보이는지 확인:

    $browser->assertVisible($selector);

<a name="assert-present"></a>
#### assertPresent

주어진 선택자와 일치하는 요소가 소스에 있는지 확인:

    $browser->assertPresent($selector);

<a name="assert-not-present"></a>
#### assertNotPresent

주어진 선택자와 일치하는 요소가 소스에 존재하지 않는지 확인:

    $browser->assertNotPresent($selector);

<a name="assert-missing"></a>
#### assertMissing

주어진 selector 와 매칭되는 element가 화면에 보이지 않는 것을 확인:

    $browser->assertMissing($selector);

<a name="assert-input-present"></a>
#### assertInputPresent

주어진 이름의 입력이 존재하는지 확인:

    $browser->assertInputPresent($name);

<a name="assert-input-missing"></a>
#### assertInputMissing

주어진 이름을 가진 입력이 소스에 존재하지 않는지 확인:

    $browser->assertInputMissing($name);

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

때때로 테스트는 여러 복잡한 작업을 순서대로 수행해야 합니다. 이렇게 하면 테스트를 읽고 이해하기가 더 어려워질 수 있습니다. Dusk 페이지를 사용하면 단일 메서드를 통해 주어진 페이지에서 수행할 수 있는 표현 작업을 정의할 수 있습니다. 페이지를 사용하면 애플리케이션 또는 단일 페이지에 대한 공통 선택자에 대한 바로 가기를 정의할 수도 있습니다.

<a name="generating-pages"></a>
### 페이지 생성하기

페이지 개체를 생성하려면 `dusk:page` Artisan 명령을 실행하세요. 모든 페이지 객체는 애플리케이션의 `tests/Browser/Pages` 디렉토리에 생성됩니다.

    php artisan dusk:page Login

<a name="configuring-pages"></a>
### 페이지 설정하기

기본적으로, 페이지는 `url`, `assert`, `elements`의 세 가지 메소드를 가집니다. 우리는 이제 `url`과 `assert` 메소드에 대해서 논의 할 것입니다. `elements` 메소드는 [아래에서 자세히 논의 할 것입니다](#shorthand-selectors)

<a name="the-url-method"></a>
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

<a name="the-assert-method"></a>
#### `assert` 메소드

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
### 페이지 탐색

페이지가 정의되면 `visit` 메소드를 사용하여 페이지를 탐색할 수 있습니다.

    use Tests\Browser\Pages\Login;

    $browser->visit(new Login);

때로는 이미 주어진 페이지에 접속 해 있을 수 있으며 페이지의 선택자와 메소드를 현재 테스트 컨텍스트에 "로드"해야 할 때가 있습니다. 이는 버튼을 누르고 명시적으로 탐색하지 않고 지정된 페이지로 리디렉션 될 때 일반적입니다. 이 상황에서, 당신은 페이지를 로드하기 위해 `on` 메소드를 사용할 수 있습니다.

    use Tests\Browser\Pages\CreatePlaylist;

    $browser->visit('/dashboard')
            ->clickLink('Create Playlist')
            ->on(new CreatePlaylist)
            ->assertSee('@create');

<a name="shorthand-selectors"></a>
### 단축 셀렉터

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

바로 가기가 정의되면 일반적으로 전체 CSS 선택자를 사용하는 곳에서 약식 선택자를 사용할 수 있습니다.

    $browser->type('@email', 'taylor@laravel.com');

<a name="global-shorthand-selectors"></a>
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

메서드가 정의되면 페이지를 활용하는 모든 테스트 내에서 사용할 수 있습니다. 브라우저 인스턴스는 사용자 정의 페이지 메소드에 대한 첫 번째 인수로 자동으로 전달됩니다.

    use Tests\Browser\Pages\Dashboard;

    $browser->visit(new Dashboard)
            ->createPlaylist('My Playlist')
            ->assertSee('My Playlist');

<a name="components"></a>
## 컴포넌트

컴포넌트는 Dusk의 "페이지 개체-objects"와 유사하지만 네비게이션바 또는 알림 창과 같이 애플리케이션 전체에서 다시 사용되는 UI 및 기능에 사용됩니다. 따라서 컴포넌트는 특정 URL에 바인딩되지 않습니다.

<a name="generating-components"></a>
### 컴포넌트 생성하기

컴포넌트를 생성하려면 `dusk:component` Artisan 명령을 실행하세요. 새 컴포넌트는 `tests/Browser/Components` 디렉토리에 생성됩니다.

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

> {note} 대부분의 Dusk 지속적 통합 설정은 8000포트에서 내장 PHP 개발 서버를 사용하여, 라라벨 애플리케이션이 제공될 것으로 예상합니다. 따라서 계속하기 전에 지속적 통합 환경에 `APP_URL` 환경 변수 값이 `http://127.0.0.1:8000`로 존재하는지 확인해야 합니다.

<a name="running-tests-on-heroku-ci"></a>
### Heroku CI

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

[Travis CI](https://travis-ci.org) 에서 Dusk 테스트를 수행하기 위해서는 아래에 나오는 `travis.yml` 설정을 사용하십시오. Travis CI는 그래픽 환경이 아니기 때문에, 크롬 브라우저를 구동하기 위해서는 몇가지 별도의 작업을 필요로 합니다. 추가적으로 PHP 내장 브라우저를 구동하기 위해서 `php artisan serve`를 사용할 수 있습니다.

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

<a name="running-tests-on-github-actions"></a>
### GitHub Actions

[Github Actions](https://github.com/features/actions) 를 사용하여 Dusk 테스트를 실행하는 경우 다음 설정 파일을 시작점으로 사용할 수 있습니다. TravisCI와 마찬가지로 `php artisan serve` 명령을 사용하여 PHP의 내장 웹 서버를 시작합니다.

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
