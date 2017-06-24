# Release Notes
# 릴리즈 노트

- [Versioning Scheme](#versioning-scheme)
- [Support Policy](#support-policy)
- [Laravel 5.4.22](#laravel-5.4.22)
- [Laravel 5.4](#laravel-5.4)

<a name="versioning-scheme"></a>
## Versioning Scheme
## 버전 관리 체계

Laravel's versioning scheme maintains the following convention: `paradigm.minor.patch`. Minor framework releases are released every six months (January and July), while patch releases may be released as often as every week. Patch releases should **never** contain breaking changes.

라라벨의 버전 관리 체계는 다음의 컨벤션을 유지합니다: `paradigm.minor.patch`. 마이너 프레임워크 릴리즈는 6 개월마다 (1월, 7월) 릴리즈되며, 패치 릴리스는 매주 여러번 릴리즈 될 수 있습니다. 패치 릴리즈에는 이전 버전의 호환성을 깨뜨리는 변경 사항이 **없어야** 합니다.

When referencing the Laravel framework or its components from your application or package, you should always use a version constraint such as `5.4.*`, since minor releases of Laravel do include breaking changes. However, we strive to always ensure you may update to a new minor release in one day or less.

어플리케이션에서 라라벨 프레임워크, 라라벨의 컴포넌트 또는 패키지를 참조할 때에, 라라벨의 마이너 릴리즈가 이전 버전과 호환성을 유지하지 못하는 변경사항을 포함하고 있기 때문에 항상 `5.4.*` 와 같이 참조하도록 해야 합니다.

Paradigm shifting releases are separated by many years and represent fundamental shifts in the framework's architecture and conventions. Currently, there is no paradigm shifting release under development.

패러다임 변경 릴리즈는 몇년간 구분되어 있으며, 프레임워크의 아키텍처와 컨벤션에 대한 근본적인 변경을 나타냅니다. 현재까지는 개발중인 버전에 대한 변경 발표는 없습니다.

#### Why Doesn't Laravel Use Semantic Versioning?
#### 왜 라라벨은 시멘틱 버저닝을 사용하지 않습니까?

On one hand, all optional components of Laravel (Cashier, Dusk, Valet, Socialite, etc.) **do** use semantic versioning. However, the Laravel framework itself does not. The reason for this is because semantic versioning is a "reductionist" way of determining if two pieces of code are compatible. Even when using semantic versioning, you still must install the upgraded package and run your automated test suite to know if anything is *actually* incompatible with your code base.

한편, 라라벨의 모든 선택가능한 추가 컴포넌트들(Cashier, Dusk, Valet, Socialite, 등)은 시멘틱 버저닝을 **사용**합니다. 그렇지만 라라벨 프레임워크 자체는 그렇지 않습니다.

그 이유는 시멘틱 버저닝이 코드의 두개의 피스가 호환되는지 결정하는 "reductionist" 방법이기 때문입니다. 시멘틱 버저닝을 사용하는 경우라도, 실제로 업그레이드된 패키지를 설치하고 자동화된 테스트를 통해서 *실제* 코드와 호환되지 않는 것이 있는지 확인해야 합니다.

So, instead, the Laravel framework uses a versioning scheme that is more communicative of the actual scope of the release.

Furthermore, since patch releases **never** contain intentional breaking changes,

you should never receive a breaking change as long as your version constraints follow the `paradigm.minor.*` convention.

따라서, 그 대신에, 라라벨 프레임워크는 릴리즈의 실제 범위와 관련된 더 많은 정보를 제공하는 버전 관리 체계를 사용합니다. 또한 패치 릴리즈에는 **절대로** 이전 버전과 호환성을 깨뜨리는 변경사항을 포함하고 있지 않습니다. 여러분이 `paradigm.minor.*` 컨벤션을 따르는 한 절대로 이전 버전과 호환되지 않는 사항을 포함하는 변경사항을 적용받지 않습니다.

<a name="support-policy"></a>
## Support Policy
## 지원 정책

For LTS releases, such as Laravel 5.1, bug fixes are provided for 2 years and security fixes are provided for 3 years. These releases provide the longest window of support and maintenance. For general releases, bug fixes are provided for 6 months and security fixes are provided for 1 year.

라라벨 5.1과 같은 LTS 릴리즈 동안에는, 2년간의 버그 픽스와 3년동안의 보안 패치가 지원됩니다. 이러한 릴리즈는 장기간에 걸친 지원과 유지보수를 제공합니다. 일반적인 릴리즈에서는 버그 픽스는 6개월, 보안 패치는 1년동안 제공됩니다.

<a name="laravel-5.4.22"></a>
## Laravel 5.4.22
## 라라벨 5.4.22

Laravel 5.4.22 patches a security vulnerability in the Laravel 5.4 release series that allows phishing attempts on users of the application. Using the password reset system, malicious users can attempt to trick your users into entering their login credentials into a separate application that they control. Since the password reset notification uses the host of the incoming request to build the password reset URL, the host of the password reset URL may be spoofed. If users do not notice that they are not on their intended application's domain, they may accidentally enter their login credentials into a malicious application.

라라벨 5.4.22는 어플리케이션의 이용자에게 피싱시도가 가능한 5.4 릴리스의 보안 취약점을 패치하였습니다. 악의적인 ​​사용자가 패스워드 재설정 시스템을 악용하여 로그인을 통해서 어플리케이션에 진입하는 사용자를 자신이 제어하는 ​​별도의 어플리케이션으로 이동하도록 할 수 있습니다. 패스워드 재설정 알림은 유입된 요청의 호스트를 사용하여 패스워드 재설정 URL을 작성하기 때문에 패스워드 재설정 URL의 호스트가 스푸핑 될 수 있습니다. 개발자가 의도한 어플리케이션의 도메인이 사용되지 있다는 것을 확인하지 않으면, 이용자가 로그인 과정에서 악의적인 어플리케이션으로 이동 할 수 있습니다.

In Laravel 5.1 applications, the password reset notification is maintained by the developer, so this vulnerability may or may not be present. You should verify that your application generates an absolute URL for password reset links:

라라벨 5.1 어플리케이션에서 패스워드 재설정 알림은 개발자가 구성 하므로 이 취약점이 존재할 수도 있고, 없을 수도 있습니다. 어플리케이션이 패스워드 재설정 링크를 위한 절대경로의 URL을 생성하는지 확인해야합니다:

    {{ url('http://example.com/password/reset/'.$token) }}

<a name="laravel-5.4"></a>
## Laravel 5.4
## 라라벨 5.4

Laravel 5.4 continues the improvements made in Laravel 5.3 by adding support for [Markdown based emails and notifications](/docs/5.4/mail#markdown-mailables), the [Laravel Dusk](/docs/5.4/dusk) browser automation and testing framework, Laravel Mix, Blade "components" and "slots", route model binding on broadcast channels, higher order messages for Collections, object-based Eloquent events, job-level "retry" and "timeout" settings, "realtime" facades, improved support for Redis Cluster, custom pivot table models, middleware for request input trimming and cleaning, and more. In addition, the entire codebase of the framework was reviewed and refactored for general cleanliness.

라라벨 5.4는 [마크다운을 기반으로한 이메일과 알림기능](/docs/5.4/mail#markdown-mailables), [라라벨 Dusk](/docs/5.4/dusk)를 통한 브라우저 테스팅 자동화, 라라벨 Mix, 블레이드의 "컴포넌트" 및 "슬롯"의 지원, 브로드캐스팅 채널에서 라우트 모델 바인딩, 컬렉션을 위한 higher order messages , 객체 기반의 Eloquent 이벤트, job 클래스에서의 "재시도" 및 "타임아웃" 설정, "realtime" 파사드, Redis의 클러스터 지원향상, 커스텀 피봇 테이블 모델, Request-요청에서 입력값의 trim 처리를 위한 미들웨어 추가등 라라벨 5.3에서 기능이 개선 계속 되었습니다. 또한 프레임워크의 전체 코드를 검토하여 클린한 코드를 위해서 리팩토링했습니다.

> {tip} This documentation summarizes the most notable improvements to the framework; however, more thorough change logs are always available [on GitHub](https://github.com/laravel/framework/blob/5.4/CHANGELOG-5.4.md).

> {tip} 이 문서는 프레임워크에서 주요한 개선사항들에 대한 요약입니다. 보다 자세한 변경사항은 [GitHub](https://github.com/laravel/framework/blob/5.4/CHANGELOG-5.4.md)에서 확인할 수 있습니다.

### Markdown Mail & Notifications
### 마크다운 메일 & 알림 기능

> {video} There is a free [video tutorial](https://laracasts.com/series/whats-new-in-laravel-5-4/episodes/7) for this feature available on Laracasts.

> {video} 이 기능을 설명한 Laracast 무료 [비디오 강좌](https://laracasts.com/series/whats-new-in-laravel-5-4/episodes/7)를 참고하십시오.

Markdown mailable messages allow you to take advantage of the pre-built templates and components of mail notifications in your mailables. Since the messages are written in Markdown, Laravel is able to render beautiful, responsive HTML templates for the messages while also automatically generating a plain-text counterpart. For example, a Markdown email might look something like the following:

마크다운 mailable 메세지 기능을 사용하면 미리 템플릿과 메일 컴포넌트를 활용할 수 있는 장점이 있습니다. 마크다운을 통해서 메세지를 작성하게 되면서, 라라벨은 메세지를 보다 원활하게 렌더링 하고, 반응형 HTML 템플릿을 사용하는 동시에 일반 텍스트를 자동으로 생성할 수 있게 되었습니다. 예를 들면 마크다운 이메일은 다음과 같이 구성할 수 있습니다:

    @component('mail::message')
    # Order Shipped

    Your order has been shipped!

    @component('mail::button', ['url' => $url])
    View Order
    @endcomponent

    Next Steps:

    - Track Your Order On Our Website
    - Pre-Sign For Delivery

    Thanks,<br>
    {{ config('app.name') }}
    @endcomponent

Using this simple Markdown template, Laravel is able to generate a responsive HTML email and plain-text counterpart:

이 마크다운 예제 템플릿을 사용하면, 라라벨은 반응형 HTML 이메일과 텍스트를 생성합니다:

<img src="https://laravel.com/assets/img/examples/markdown.png" width="551" height="596">

To read more about Markdown mail and notifications, check out the full [mail](/docs/5.4/mail) and [notification](/docs/5.4/notifications) documentation.

마크다운 메일과 일림에 대해서 보다 자세한 사항은 [메일](/docs/5.4/mail) 그리고 [알림](/docs/5.4/notifications) 문서를 확인하십시오.

> {tip} You may export all of the Markdown mail components to your own application for customization. To export the components, use the `vendor:publish` Artisan command to publish the `laravel-mail` asset tag.

> {tip} 어플리케이션에서 사용하는 모든 마크다운 메일 컴포넌트는 커스터마이징이 가능합니다. 먼저 컴포넌트를 내보내기 위해서 `vendor:publish` 아티즌 명령어를 사용하여 `laravel-mail` 애셋 태그를 지정합니다.

### Laravel Dusk
### 라라벨 Dusk

> {video} There is a free [video tutorial](https://laracasts.com/series/whats-new-in-laravel-5-4/episodes/9) for this feature available on Laracasts.

> {video} 이 기능을 설명한 Laracast 무료 [비디오 강좌](https://laracasts.com/series/whats-new-in-laravel-5-4/episodes/9)를 참고하십시오.

Laravel Dusk provides an expressive, easy-to-use browser automation and testing API. By default, Dusk does not require you to install JDK or Selenium on your machine. Instead, Dusk uses a standalone [ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/home) installation. However, you are free to utilize any other Selenium compatible driver you wish.

라라벨 Dusk는 구성과 사용이 쉬운 브라우저 자동화 및 테스팅 API를 제공합니다. 기본적으로 Dusk는 사용자 머신에 JDK 나 Selenium을 설치하도록 요구하지 않습니다. 대신에 Dusk는 독립적인 [ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/home)를 사용합니다. 그렇긴 하지만, 원하는 경우 다른 Selenium 호환 드라이버를 사용할 수도 있습니다.

Since Dusk operates using a real browser, you are able to easily test and interact with your applications that heavily use JavaScript:

Dusk는 실제 브라우저를 사용하기 때문에, 자바스크립트를 많이 사용하는 어플리케이션이더라도 어플리케이션을 손쉽게 테스트하고 조작할 수 있습니다:

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
            $browser->loginAs($user)
                    ->visit('/home')
                    ->press('Create Playlist')
                    ->whenAvailable('.playlist-modal', function ($modal) {
                        $modal->type('name', 'My Playlist')
                              ->press('Create');
                    });

            $browser->waitForText('Playlist Created');
        });
    }

For more information on Dusk, consult the full [Dusk documentation](/docs/5.4/dusk).

Dusk에 대한 보다 자세한 사항은 [Dusk 문서](/docs/5.4/dusk)를 참고하십시오.

### Laravel Mix
### 라라벨 Mix

> {video} There is a free [video tutorial](https://laracasts.com/series/whats-new-in-laravel-5-4/episodes/3) for this feature available on Laracasts.

> {video} 이 기능을 설명한 Laracast 무료 [비디오 강좌](https://laracasts.com/series/whats-new-in-laravel-5-4/episodes/3)를 참고하십시오.

Laravel Mix is the spiritual successor of Laravel Elixir, and its entirely based on Webpack instead of Gulp. Laravel Mix provides a fluent API for defining Webpack build steps for your Laravel application using several common CSS and JavaScript pre-processors. Through simple method chaining, you can fluently define your asset pipeline. For example:

라라벨 Mix는 차세대 라라벨 Elixir 입니다. 이는 전적으로 Gulp 대신에 Webpack을 기반으로 합니다. 라라벨 Mix는 공통의 CSS 및 자바스크립트 프리프로세싱을 사용하는 라라벨 어플리케이션을 위해서 Webpack 빌드를 정의하는 유연한 API를 제공합니다. 메소드 체이닝이 가능하기 때문에, asset 파이프라인을 보다 손쉽게 정의할 수 있습니다. 예를 들면:

    mix.js('resources/assets/js/app.js', 'public/js')
       .sass('resources/assets/sass/app.scss', 'public/css');

### Blade Components & Slots
### 블레이드 컴포넌트 & 슬롯

> {video} There is a free [video tutorial](https://laracasts.com/series/whats-new-in-laravel-5-4/episodes/6) for this feature available on Laracasts.

> {video} 이 기능을 설명한 Laracast 무료 [비디오 강좌](https://laracasts.com/series/whats-new-in-laravel-5-4/episodes/6)를 참고하십시오.

Blade components and slots provide similar benefits to sections and layouts; however, some may find the mental model of components and slots easier to understand. First, let's imagine a reusable "alert" component we would like to reuse throughout our application:

블레이드 컴포넌트 & 슬롯은 섹션 & 레이아웃과 비슷한 장점을 제공하지만, 컴포넌트와 슬롯의 개념보다 이해하기가 더 쉽습니다. 먼저 어플리케이션에서 재사용할 수 있는 "경고-alert" 컴포넌트를 생각해 보겠습니다:

    <!-- /resources/views/alert.blade.php -->

    <div class="alert alert-danger">
        {{ $slot }}
    </div>

The `{{ $slot }}` variable will contain the content we wish to inject into the component. Now, to construct this component, we can use the `@component` Blade directive:

`{{ $slot }}` 변수는 컴포넌트에 주입하고자 하는 컨텐츠를 가지게 됩니다. 이제, 이 컴포넌트를 생성하기 위해서 `@component` 블레이드 지시어를 사용할 수 있습니다:

    @component('alert')
        <strong>Whoops!</strong> Something went wrong!
    @endcomponent

Named slots allow you to provide multiple slots into a single component:

이름이 지정된 슬롯을 사용하면 하나의 컴포넌트에 여러개의 슬롯을 제공 할 수 있습니다:

    <!-- /resources/views/alert.blade.php -->

    <div class="alert alert-danger">
        <div class="alert-title">{{ $title }}</div>

        {{ $slot }}
    </div>

Named slots may be injected using the `@slot` directive. Any content is not within a `@slot` directive will be passed to the component in the `$slot` variable:

이름이 지정된 슬롯은 `@slot` 지시어를 사용하여 주입할 수 있습니다. `@slot` 지시어에 들어 있지 않은 컨텐츠는 컴포넌트의 `$slot` 변수에 전달됩니다:

    @component('alert')
        @slot('title')
            Forbidden
        @endslot

        You are not allowed to access this resource!
    @endcomponent

To read more about components and slots, consult the full [Blade documentation](/docs/5.4/blade).

컴포넌트와 슬롯에 대한 보다 자세한 사항은 [블레이드 문서](/docs/5.4/blade)를 참고하십시오.

### Broadcast Model Binding
### 브로드캐스팅 모델 바인딩

Just like HTTP routes, channel routes may now take advantage of implicit and explicit [route model binding](/docs/5.4/routing#route-model-binding). For example, instead of receiving the string or numeric order ID, you may request an actual `Order` model instance:

HTTP 라우트와 같이 채널 라우트는 명시적 그리고 묵시적 [라우트 모델 바인딩](/docs/5.4/routing#route-model-binding)의 장점을 사용할 수 있습니다. 예를 들어, 문자열이나 숫자형태의 주문 ID를 받는 대신에, 실제 `Order` 모델 인스턴스를 요청할 수 있습니다:

    use App\Order;

    Broadcast::channel('order.{order}', function ($user, Order $order) {
        return $user->id === $order->user_id;
    });

To read more about broadcast model binding, consult the full [event broadcasting](/docs/5.4/broadcasting) documentation.

브로드캐스팅 모델 바인딩에 대한 보다 자세한 사항은 [이벤트 브로드캐스팅](/docs/5.4/broadcasting)에 대한 문서를 참고하십시오.

### Collection Higher Order Messages
### 컬렉션 Higher Order Messages

> {video} There is a free [video tutorial](https://laracasts.com/series/whats-new-in-laravel-5-4/episodes/2) for this feature available on Laracasts.

> {video} 이 기능을 설명한 Laracast 무료 [비디오 강좌](https://laracasts.com/series/whats-new-in-laravel-5-4/episodes/2)를 참고하십시오.

Collections now provide support for "higher order messages", which are short-cuts for performing common actions on collections. The collection methods that provide higher order messages are: `contains`, `each`, `every`, `filter`, `first`, `map`, `partition`, `reject`, `sortBy`, `sortByDesc`, and `sum`.

컬렉션은 이제 공통된 작업을 수행하는데 필요한 "higher order message"를 제공합니다. 컬렉션에서 higher order message 가 가능한 메소드들은 `contains`, `each`, `every`, `filter`, `first`, `map`, `partition`, `reject`, `sortBy`, `sortByDesc`, `sum` 입니다.

Each higher order message can be accessed as a dynamic property on a collection instance. For instance, let's use the `each` higher order message to call a method on each object within a collection:

각각의 higher order message 는 컬렉션 인스턴스의 동적 속성에 접근할 수 있습니다. 예를 들자면, 컬렉션 안에 있는 각 객체의 메소드를 호출하기 위해서 `each` higher order message 를 사용해보겠습니다:

    $users = User::where('votes', '>', 500)->get();

    $users->each->markAsVip();

Likewise, we can use the `sum` higher order message to gather the total number of "votes" for a collection of users:

마찬가지로 `sum` higher order message를 사용하여 사용자 컬렉션의 "전체 투표 수를" 확인할 수 있습니다:

    $users = User::where('group', 'Development')->get();

    return $users->sum->votes;

### Object Based Eloquent Events
### 객체 기반의 Eloquent 이벤트

> {video} There is a free [video tutorial](https://laracasts.com/series/whats-new-in-laravel-5-4/episodes/10) for this feature available on Laracasts.

> {video} 이 기능을 설명한 Laracast 무료 [비디오 강좌](https://laracasts.com/series/whats-new-in-laravel-5-4/episodes/10)를 참고하십시오.

Eloquent event handlers may now be mapped to event objects. This provides a more intuitive way of handling Eloquent events and makes it easier to test the events. To get started, define an `$events` property on your Eloquent model that maps various points of the Eloquent model's lifecycle to your own [event classes](/docs/5.4/events):

Eloquent 이벤트 핸들러는 이제 이벤트 객체로 매핑되었습니다. 이를 통해서 Eloquent 이벤트 처리가 보다 직관적이고 테스트가 쉬워졌습니다. 시작하기 위해서, Eloquent 모델의 라이프사이클에 해당하는 다양한 포인트를 나타내는 [이벤트 클래스](/docs/5.4/events)를 매핑하고 있는 `$events` 속성을 정의하십시오:

    <?php

    namespace App;

    use App\Events\UserSaved;
    use App\Events\UserDeleted;
    use Illuminate\Notifications\Notifiable;
    use Illuminate\Foundation\Auth\User as Authenticatable;

    class User extends Authenticatable
    {
        use Notifiable;

        /**
         * The event map for the model.
         *
         * @var array
         */
        protected $events = [
            'saved' => UserSaved::class,
            'deleted' => UserDeleted::class,
        ];
    }

### Job Level Retry & Timeout
### Job 재시도 횟수 & 타임아웃

Previously, queue job "retry" and "timeout" settings could only be configured globally for all jobs on the command line. However, in Laravel 5.4, these settings may be configured on a per-job basis by defining them directly on the job class:

이전 버전까지는, queue job의 "재시도 횟수" 그리고 "타임아웃" 설정은 커맨드 라인에서 모든 job들을 대상으로만 설정이 가능했습니다. 라라벨 5.4에서는 이 설정들은 job 클래스에 직접 정의하여 설정이 가능하도록 변경되었습니다:

    <?php

    namespace App\Jobs;

    class ProcessPodcast implements ShouldQueue
    {
        /**
         * The number of times the job may be attempted.
         *
         * @var int
         */
        public $tries = 5;

        /**
         * The number of seconds the job can run before timing out.
         *
         * @var int
         */
        public $timeout = 120;
    }

For more information about these settings, consult the full [queue documentation](/docs/5.4/queues).

이 설정에 대한 보다 자세한 사항은 [queue 문서](/docs/5.4/queues)를 참고하십시오.

### Request Sanitization Middleware
### Request-요청 Sanitization 미들웨어

> {video} There is a free [video tutorial](https://laracasts.com/series/whats-new-in-laravel-5-4/episodes/1) for this feature available on Laracasts.

> {video} 이 기능을 설명한 Laracast 무료 [비디오 강좌](https://laracasts.com/series/whats-new-in-laravel-5-4/episodes/1)를 참고하십시오.

Laravel 5.4 includes two new middleware in the default middleware stack: `TrimStrings` and `ConvertEmptyStringsToNull`:

라라벨 5.4는 기본 미들웨어 스택에 `TrimStrings`와 `ConvertEmptyStringsToNull`이라는 새로운 미들웨어를 포함합니다:

    /**
     * The application's global HTTP middleware stack.
     *
     * These middleware are run during every request to your application.
     *
     * @var array
     */
    protected $middleware = [
        \Illuminate\Foundation\Http\Middleware\CheckForMaintenanceMode::class,
        \Illuminate\Foundation\Http\Middleware\ValidatePostSize::class,
        \App\Http\Middleware\TrimStrings::class,
        \Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull::class,
    ];

These middleware will automatically trim request input values and convert any empty strings to `null`. This helps you normalize the input for every request entering into your application and not have to worry about continually calling the `trim` function in every route and controller.

이 미들웨어는 request-요청의 모든 입력값을 자동으로 trim 처리하고, 빈 문자필드는 `null`로 변환합니다. 이를 통해서 어플리케이션에 유입되는 모든 request의 입력값의 일반화 처리에 대해서 걱정할 필요가 없으며, 매번 라우트와 컨트롤러에서 `trim` 함수를 호출하지 않아도 됩니다.

### "Realtime" Facades
### "Realtime" 파사드

> {video} There is a free [video tutorial](https://laracasts.com/series/whats-new-in-laravel-5-4/episodes/8) for this feature available on Laracasts.

> {video} 이 기능을 설명한 Laracast 무료 [비디오 강좌](https://laracasts.com/series/whats-new-in-laravel-5-4/episodes/8)를 참고하십시오.

Previously, only Laravel's own built-in services exposed [facades](/docs/5.4/facades), which provide quick, terse access to their methods via the service container. However, in Laravel 5.4, you may easily convert any of your application's classes into a facade in realtime simply by prefixing the imported class name with `Facades`. For example, imagine your application contains a class like the following:

이전까지는, 라라벨의 고유한 내장 서비스만 [파사드](/docs/5.4/facades)로 노출되어 서비스 컨테이너를 통해서 메소드에 보다 빠르게 엑세스 할 수 있었습니다. 라라벨 5.4에서는 어플리케이션의 클래스앞에 `Facades`를 붙여서 import 함으로써 여러분의 고유한 클래스를 손쉽게 파사드로 변환할 수 있습니다. 예를 들어, 어플리케이션에 다음과 같은 클래스가 있다고 가정해보겠습니다:

    <?php

    namespace App\Services;

    class PaymentGateway
    {
        protected $tax;

        /**
         * Create a new payment gateway instance.
         *
         * @param  TaxCalculator  $tax
         * @return void
         */
        public function __construct(TaxCalculator $tax)
        {
            $this->tax = $tax;
        }

        /**
         * Pay the given amount.
         *
         * @param  int  $amount
         * @return void
         */
        public function pay($amount)
        {
            // Pay an amount...
        }
    }

You may easily use this class as a facade like so:

다음처럼 이 클래스를 손쉽게 파사드로 사용할 수 있습니다:

    use Facades\ {
        App\Services\PaymentGateway
    };

    Route::get('/pay/{amount}', function ($amount) {
        PaymentGateway::pay($amount);
    });

Of course, if you leverage a realtime facade in this way, you may easily write a test for the interaction using Laravel's [facade mocking capabilities](/docs/5.4/mocking):

물론, 이렇게 realtime 파사드를 사용하도라도, 라라벨의 [파사드 mocking 호환](/docs/5.4/mocking)기능을 통해서 테스트를 손쉽게 작성할 수 있습니다:

    PaymentGateway::shouldReceive('pay')->with('100');

### Custom Pivot Table Models
### 커스텀 피벗 테이블 모델

In Laravel 5.3, all "pivot" table models for `belongsToMany` relationships used the same built-in `Pivot` model instance. In Laravel 5.4, you may define custom models for your pivot tables. If you would like to define a custom model to represent the intermediate table of your relationship, use the `using` method when defining the relationship:

라라벨 5.3에서는 `belongsToMany` 관계를 위한 모든 "피벗" 테이블 모델은 동일한 내장 `Pivot` 모델 인스턴스를 사용했습니다. 라라벨 5.4에서는 이 피벗 테이블을 위한 커스텀 모델을 정의할 수 있습니다. 관계의 중간 테이블을 표현하기 위해서 커스텀 모델을 정의하려면, 관례를 정의할 때 `using` 메소드를 호출하면 됩니다:

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Role extends Model
    {
        /**
         * The users that belong to the role.
         */
        public function users()
        {
            return $this->belongsToMany('App\User')->using('App\UserRole');
        }
    }

### Improved Redis Cluster Support
### 레디스 클러스터 지원 향상

Previously, it was not possible to define Redis connections to single hosts and to clusters in the same application. In Laravel 5.4, you may now define Redis connections to multiple single hosts and multiple clusters within the same application. For more information on Redis in Laravel, please consult the full [Redis documentation](/docs/5.4/redis).

이전버전까지, 동일한 어플리케이션에서 하나의 호스트 및 클러스터에 대한 Redis 커넥션을 정의할 수 없었습니다. 라라벨 5.4에서는 동일한 어플리케이션 안에서 여러개의 다수의 단일 호수트 및 다중 클러스터에 대한 Redis 커넥션을 정의할 수 있습니다. 라라벨의 Redis에 대한 보다 자세한 사항은 [Redis 문서](/docs/5.4/redis)를 참고하시기 바랍니다.

<a name="utf8mb4"></a>
### Migration Default String Length
### 기본 문자열 길이 마이그레이션 하기

Laravel 5.4 uses the `utf8mb4` character set by default, which includes support for storing "emojis" in the database. If you are upgrading your application from Laravel 5.3, you are not required to switch to this character set.

라라벨 5.4는 기본적으로 "emojis"를 데이터베이스에 저장할 수 있는 `utf8mb4` 캐릭터셋을 사용합니다. 어플리케이션이 라라벨 5.3에서 업그레이드 되었다면, 이 캐릭터셋을 변경할 필요는 없습니다.

If you choose to switch to this character set manually and are running a version of MySQL older than the 5.7.7 release, you may need to manually configure the default string length generated by migrations. You may configure this by calling the `Schema::defaultStringLength` method within your `AppServiceProvider`:

이 캐릭터셋을 수동으로 변경하기를 원하거나 MySQL 5.7.7 이전버전을 사용중이라면, 마이그레이션 파일을 생성하여 수동으로 기본 문자열 길이를 조정해야합니다. `AppServiceProvider` 파일 안에서 `Schema::defaultStringLength` 메소드를 호출하여 이를 설정할 수 있습니다:

    use Illuminate\Support\Facades\Schema;

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        Schema::defaultStringLength(191);
    }
