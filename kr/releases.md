# Release Notes
# 릴리즈 노트

- [Versioning Scheme](#versioning-scheme)
- [Support Policy](#support-policy)
- [Laravel 5.4.22](#laravel-5.4.22)
- [Laravel 5.4](#laravel-5.4)
- [Laravel 5.3](#laravel-5.3)
- [Laravel 5.2](#laravel-5.2)
- [Laravel 5.1.11](#laravel-5.1.11)
- [Laravel 5.1.4](#laravel-5.1.4)
- [Laravel 5.1](#laravel-5.1)
- [Laravel 5.0](#laravel-5.0)
- [Laravel 4.2](#laravel-4.2)
- [Laravel 4.1](#laravel-4.1)

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

<a name="laravel-5.3"></a>
## Laravel 5.3
## 라라벨 5.3

Laravel 5.3 continues the improvements made in Laravel 5.2 by adding a driver based [notification system](/docs/5.3/notifications), robust realtime support via [Laravel Echo](/docs/5.3/broadcasting), painless OAuth2 servers via [Laravel Passport](/docs/5.3/passport), full-text model searching via [Laravel Scout](/docs/5.3/scout), Webpack support in Laravel Elixir, "mailable" objects, explicit separation of `web` and `api` routes, Closure based console commands, convenient helpers for storing uploaded files, support for POPO and single-action controllers, improved default frontend scaffolding, and more.

라라벨 5.3은 드라이버 베이스의 [Notifications 시스템](/docs/5.3/notifications), [라라벨 에코](/docs/5.3/broadcasting)를 통한 실시간 지원, [라라벨 패스포트](/docs/5.3/passport)를 통한 보다 편리한 OAuth2 서버, [라라벨 스카웃](/docs/5.3/scout)을 통한 full-text 모델 검색, 라라벨 Elixir 안에서의 Webpack 지원, "mailable" 객체, `web` 과 `api` 라우트의 명시적인 분리, 클로저 기반의 콘솔 명령어, 업로드 된 파일을 저장하기 위한 편리한 헬퍼들, POPO의 지완과 단일 액션 컨트롤러, 기본적인 프론트엔드 스캐폴딩의 향상등 라라벨 5.2에서 기능이 개선 계속 되었습니다.

### Notifications
### Notifications-알림

> {video} There is a free [video tutorial](https://laracasts.com/series/whats-new-in-laravel-5-3/episodes/9) for this feature available on Laracasts.

> {video} 라라캐스트에서 이 기능을 위한 무료 [동영상 튜토리얼](https://laracasts.com/series/whats-new-in-laravel-5-3/episodes/9) 이 있습니다.

Laravel Notifications provide a simple, expressive API for sending notifications across a variety of delivery channels such as email, Slack, SMS, and more. For example, you may define a notification that an invoice has been paid and deliver that notification via email and SMS. Then, you may send the notification using a single, simple method:

라라벨 Notifications는 이메일, 슬랙, SMS등과 같이 다양한 전송 채널에 알림을 보내기위한 간단하고 풍부한 표현이 가능한 API를 제공합니다. 예를 들어 청구서가 지불되었고 이에 대한 알림을 이메일 및 SMS를 통해 보내지도록 정의할 수 있습니다. 그런 다음 한번의 간단한 메소드를 통해서 알림을 보낼 수 있습니다:

    $user->notify(new InvoicePaid($invoice));

There is already a wide variety of [community written drivers](http://laravel-notification-channels.com) for notifications, including support for iOS and Android notifications. To learn more about notifications, be sure to check out the [full notification documentation](/docs/5.3/notifications).

이미 [커뮤니티에 의해서 작성된 알림 드라이버](http://laravel-notification-channels.com)가 폭넓게 준비되어 있습니다. 이중에는 iOS 와 안드로이드 알림을 지원합니다. 알림에 대해서 보다 자세한 내용을 확인하려면 [notification 문서](/docs/5.3/notifications)를 확인하십시오.

### WebSockets / Event Broadcasting
### 웹소켓 / 이벤트 브로드캐스팅

While event broadcasting existed in previous versions of Laravel, the Laravel 5.3 release greatly improves this feature of the framework by adding channel-level authentication for private and presence WebSocket channels:

이전 버전의 라라벨에도 이벤트 브로드캐스트가 있었지만, 라라벨 5.3 릴리즈에서는 비공개 와 참석한 웹소켓 채널만을 위한 채널 레벨의 인증을 추가하여 프레임워크의 기능이 크게 향상되었습니다.

    /*
     * Authenticate the channel subscription...
     */
    Broadcast::channel('orders.*', function ($user, $orderId) {
        return $user->placedOrder($orderId);
    });

Laravel Echo, a new JavaScript package installable via NPM, has also been released to provide a simple, beautiful API for subscribing to channels and listening for your server-side events in your client-side JavaScript application. Echo includes support for [Pusher](https://pusher.com) and [Socket.io](http://socket.io):

라라벨 Echo는 NPM을 통해서 설치할 수 있는 새로운 JavaSript 패키지이며, 클라이언트 측 JavaScript 어플리케이션에서 채널을 구독하고 서버 측 이벤트를 수신하리 위한 간단하고 아름다은 API를 제공합니다. Echo는 [Pusher](https://pusher.com) 와 [Socket.io](http://socket.io)도 지원하고 있습니다:

    Echo.channel('orders.' + orderId)
        .listen('ShippingStatusUpdated', (e) => {
            console.log(e.description);
        });

In addition to subscribing to traditional channels, Laravel Echo also makes it a breeze to subscribe to presence channels which provide information about who is listening on a given channel:

기존 채널을 구독하는 것에 더하여, 라라벨 Echo는 주어진 채널에서 수신자를 알 수 있는 정보를 제공하는 참석자 전용 채널에 가입할 수 있는 간편한 방법을 제공합니다:

    Echo.join('chat.' + roomId)
        .here((users) => {
            //
        })
        .joining((user) => {
            console.log(user.name);
        })
        .leaving((user) => {
            console.log(user.name);
        });

To learn more about Echo and event broadcasting, check out the [full documentation](/docs/5.3/broadcasting).

Echo 와 이벤트 브로드캐스트에 대한 보다 자세한 정보는 [전체 매뉴얼](/docs/5.3/broadcasting)을 확인하십시오.

### Laravel Passport (OAuth2 Server)
### 라라벨 Passport (OAuth2 서버)

> {video} There is a free [video tutorial](https://laracasts.com/series/whats-new-in-laravel-5-3/episodes/13) for this feature available on Laracasts.

> {video} 라라캐스트에서 이 기능을 위한 무료 [동영상 튜토리얼](https://laracasts.com/series/whats-new-in-laravel-5-3/episodes/13) 이 있습니다.

Laravel 5.3 makes API authentication a breeze using [Laravel Passport](/docs/{{version}}/passport), which provides a full OAuth2 server implementation for your Laravel application in a matter of minutes. Passport is built on top of the [League OAuth2 server](https://github.com/thephpleague/oauth2-server) that is maintained by Alex Bilbie.

라라벨 5.3은 [라라벨 Passport](/docs/{{version}}/passport)를 사용하여 빠르게 여러분의 어플리케이션에 완전한 OAuth2 서버 구현을 제공하여 API 인증을 매우 용이하게 만들어 줍니다.Passport는 Alex Bilbie에 의해 관리되고있는 [League OAuth2 server](https://github.com/thephpleague/oauth2-server)위에 구축되어 있습니다.

Passport makes it painless to issue access tokens via OAuth2 authorization codes. You may also allow your users to create "personal access tokens" via your web UI. To get you started quickly, Passport includes [Vue components](https://vuejs.org) that can serve as a starting point for your OAuth2 dashboard, allowing users to create clients, revoke access tokens, and more:

Passport는 OAuth2 승인 코드를 통해서 엑세스 토큰을 발급을 어렵지 않게 만들어 줍니다. 사용자가 웹 UI를 통해서 "개인용 엑세스 토큰"을 생성하도록 허용 할 수도 있습니다. 빠르게 시작할 수 있도록 Passport는 OAuth2 대쉬보드를 제공하고, 사용자가 클라이언트를 생성하거나, 엑세스 토큰을 취소하는 등의 작업을 할 수 있는  [Vue 컴포넌트](https://vue.js.org)를 포함하고 있습니다:

    <passport-clients></passport-clients>
    <passport-authorized-clients></passport-authorized-clients>
    <passport-personal-access-tokens></passport-personal-access-tokens>

If you do not want to use the Vue components, you are welcome to provide your own frontend dashboard for managing clients and access tokens. Passport exposes a simple JSON API that you may use with any JavaScript framework you choose.

Vue 컴포넌트를 사용하지 않으려면 클라이언트 및 엑세스 토큰을 관리하기 위한 프론트 엔드 대쉬보드를 직접 제공해야 합니다. Passport는 여러분이 선택한 어떠한 자바스크립트 프레임워크에서도 사용할 수 있는 간단한 JSON API를 제공합니다.

Of course, Passport also makes it simple to define access token scopes that may be requested by application's consuming your API:

물론 Passport는 어플리케이션이 API를 사용하여 요청하는 엑세스 토큰의 범위를 정의하는 것도 손쉽게 만들어 줍니다:

    Passport::tokensCan([
        'place-orders' => 'Place new orders',
        'check-status' => 'Check order status',
    ]);

In addition, Passport includes helpful middleware for verifying that an access token authenticated request contains the necessary token scopes:

이에 더해, Passport는 엑세스 토큰의 인증 요청에서, 필요한 토큰 범위를 포함하고 있는지 확인하기 위한 편리한 미들웨어도 포함하고 있습니다:

    Route::get('/orders/{order}/status', function (Order $order) {
        // Access token has "check-status" scope...
    })->middleware('scope:check-status');

Lastly, Passport includes support for consuming your own API from your JavaScript application without worrying about passing access tokens. Passport achieves this through encrypted JWT cookies and synchronized CSRF tokens, allowing you to focus on what matters: your application. For more information on Passport, be sure to check out its [full documentation](/docs/5.3/passport).

마지막으로, Passport는 여러분의 자바스크립트 어플리케이션에서 엑세스 토큰을 전달하는 것에 대한 걱정 없이도 여러분의 고유한 API를 사용할 수 있도록 지원하고 있습니다. Passport는 암호화된 JWT 쿠키와 동기화된 CSRF 토큰을 통해서 이를 달성하며 여러분이 보다 중요한 사항(여러분의 어플리케이션)에 초점을 맞출 수 있도록 해줍니다. Passport에 대한 보다 자세한 사항은 [전체 매뉴얼](/docs/5.3/passport)을 참고하시기 바랍니다.

### Search (Laravel Scout)
### 검색 (라라벨 Scout)

Laravel Scout provides a simple, driver based solution for adding full-text search to your [Eloquent models](/docs/5.3/eloquent). Using model observers, Scout will automatically keep your search indexes in sync with your Eloquent records. Currently, Scout ships with an [Algolia](https://www.algolia.com/) driver; however, writing custom drivers is simple and you are free to extend Scout with your own search implementations.

라라벨 Scout-스카우트는 [Eloquent 모델](/docs/{{version}}/eloquent)에 full-text 검색 기능을 장착할 수 있는 드라이버 기반의 단순한 솔루션을 제공합니다. 스카우트는 모델 옵저버를 사용하여 엘로퀀트 레코드와 동기화된 검색 인덱스를 자동으로 유지합니다. 현재 스카우트는 [Algolia](https://www.algolia.com/) 드라이버를 제공합니다만; 사용자 정의 드라이버 작성은 간단하기 때문에 자유롭게 Scout을 여러분의 고유한 검색 구현으로 확장할 수 있습니다.

Making models searchable is as simple as adding a `Searchable` trait to the model:

모델이 검색이 가능하게 만드려면 간단하게 `Searchable` 트레이트를 모델에 추가하면 됩니다:

    <?php

    namespace App;

    use Laravel\Scout\Searchable;
    use Illuminate\Database\Eloquent\Model;

    class Post extends Model
    {
        use Searchable;
    }

Once the trait has been added to your model, its information will be kept in sync with your search indexes by simply saving the model:

모델에 트레이트가 추가하고 나면, 이 정보는 모델을 저장할 때마다 검색 인덱스와 동기화 됩니다.

    $order = new Order;

    // ...

    $order->save();

Once your models have been indexed, its a breeze to perform full-text searches across all of your models. You may even paginate your search results:

모델이 인덱싱 되고 나면, 모든 모델에 대해서 full-text 검색을 실행하는 것이 매우 쉽습니다. 검색 결과에 페이지네이션을 적용할 수도 있습니다:

    return Order::search('Star Trek')->get();

    return Order::search('Star Trek')->where('user_id', 1)->paginate();

Of course, Scout has many more features which are covered in the [full documentation](/docs/5.3/scout).

물론 스카우트는 더 기능을 가지고 있으며, [전체 문서](/docs/5.3/scout) 에서 설명하고 있습니다.

### Mailable Objects
### Mailable 객체

> {video} There is a free [video tutorial](https://laracasts.com/series/whats-new-in-laravel-5-3/episodes/6) for this feature available on Laracasts.

> {video} 라라캐스트에서 이 기능을 위한 무료 [동영상 튜토리얼](https://laracasts.com/series/whats-new-in-laravel-5-3/episodes/6) 이 있습니다.

Laravel 5.3 ships with support for mailable objects. These objects allow you to represent your email messages as a simple objects instead of customizing mail messages within Closures. For example, you may define a simple mailable object for a "welcome" email:

라라벨 5.3은 mailable 객체를 지원합니다. 이 객체들은 여러분에게 클로저 안에서 메일 메세지를 커스터마이징 하는 대신에 여러분의 이메일 메세지를 간단한 객체로 나타내 줍니다. 예를 들어 "환영" 이메일을 위한 간단한 mailable 객체를 정의 할 수 있습니다:

    class WelcomeMessage extends Mailable
    {
        use Queueable, SerializesModels;

        /**
         * Build the message.
         *
         * @return $this
         */
        public function build()
        {
            return $this->view('emails.welcome');
        }
    }

Once the mailable object has been defined, you can send it to a user using a simple, expressive API. Mailable objects are great for discovering the intent of your messages while scanning your code:

mailable 객체가 정의되고 나면, 간단하고 풍부한 표현이 가능한 API를 통해 사용자에게 발송할 수 있습니다. Mailable 객체는 코드를 읽어들이면서, 메시지의 의도를 알아차리는 데 유용합니다.

    Mail::to($user)->send(new WelcomeMessage);

Of course, you may also mark mailable objects as "queueable" so that they will be sent in the background by your queue workers:

또한, mailable 객체를 "queueable"로 표시하여 queue 워커에 의해서 백그라운드에서 메일이 발송되게 할 수도 있습니다:

    class WelcomeMessage extends Mailable implements ShouldQueue
    {
        //
    }

For more information on mailable objects, be sure to check out the [mail documentation](/docs/5.3/mail).

mailable 객체에 대한 보다 자세한 정보는 [메일 문서](/docs/5.3/mail)를 참고하시기 바랍니다.

### Storing Uploaded Files
### 업로드 된 파일의 저장

> {video} There is a free [video tutorial](https://laracasts.com/series/whats-new-in-laravel-5-3/episodes/12) for this feature available on Laracasts.

> {video} 라라캐스트에서 이 기능을 위한 무료 [동영상 튜토리얼](https://laracasts.com/series/whats-new-in-laravel-5-3/episodes/12) 이 있습니다.

In web applications, one of the most common use-cases for storing files is storing user uploaded files such as profile pictures, photos, and documents. Laravel 5.3 makes it very easy to store uploaded files using the new `store` method on an uploaded file instance. Simply call the `store` method with the path at which you wish to store the uploaded file:

웹 어플리케이션에서 파일을 저장하는 가장 공통적인 사용예 중 하나는 프로필 이미지, 사진, 그리고 문서와 같은 사용자가 업로드한 파일을 저장하는 것입니다. 라라벨 5.3은 업로드된 파일 인스턴스에서 새로운 `store` 메소드를 사용하여 업로드한 파일이 저장되는 것을 아주 쉽게 처리합니다. 간단하게 저장된 파일이 저장되고자 하는 경로를 넘겨주며 `store` 메소드를 호출하면 됩니다:

    /**
     * Update the avatar for the user.
     *
     * @param  Request  $request
     * @return Response
     */
    public function update(Request $request)
    {
        $path = $request->file('avatar')->store('avatars', 's3');

        return $path;
    }

For more information on storing uploaded files, check out the [full documentation](/docs/{{version}}/filesystem#file-uploads).

업로드 된 파일을 저장하는 방법에 대한 보다 자세한 내용은 [전체 문서](/docs/{{version}}/filesystem#file-uploads)를 참고하시기 바랍니다.

### Webpack & Laravel Elixir
### Webpack & 라라벨 Elixir

Along with Laravel 5.3, Laravel Elixir 6.0 has been released with baked-in support for the Webpack and Rollup JavaScript module bundlers. By default, the Laravel 5.3 `gulpfile.js` file now uses Webpack to compile your JavaScript. The [full Laravel Elixir documentation](/docs/5.3/elixir) contains more information on both of these bundlers:

라라벨 5.3과 함께 라라벨 Elixir 6.0이 Webpack 과 Rollup 자바스크립트 모듈 번들러에 대한 지원이 내장되어 새롭게 릴리즈 되었습니다. 기본적으로 라라벨 5.3의 `gulpfile.js` 파일은 이제 자바스크립트를 컴파일 하는데 Webpack 을 사용합니다. [전체 Elixir 문서](/docs/5.3/elixir)에는 이러한 bundler-번들러에 대한 자세한 정보가 포함되어 있습니다:

    elixir(mix => {
        mix.sass('app.scss')
           .webpack('app.js');
    });

### Frontend Structure
### 프론트엔드 구조

> {video} There is a free [video tutorial](https://laracasts.com/series/whats-new-in-laravel-5-3/episodes/4) for this feature available on Laracasts.

> {video} 라라캐스트에서 이 기능을 위한 무료 [동영상 튜토리얼](https://laracasts.com/series/whats-new-in-laravel-5-3/episodes/4) 이 있습니다.

Laravel 5.3 ships with a more modern frontend structure. This primarily affects the `make:auth` authentication scaffolding. Instead of loading frontend assets from a CDN, dependencies are specified in the default `package.json` file.

라라벨 5.3 은 보다 현대적인 프론트엔드 구조를 제공합니다. 이는 주로 `make:auth` 인증 스캐폴딩에 영향을 줍니다. 프론트 엔드 asset을 DNS에서 로딩하는 대신, 의존패키지들은 기본적인 `package.json` 파일안에 정의되어 있습니다. 

In addition, support for single file [Vue components](https://vuejs.org) is now included out of the box. A sample `Example.vue` component is included in the `resources/assets/js/components` directory. In addition, the new `resources/assets/js/app.js` file bootstraps and configures your JavaScript libraries and, if applicable, Vue components.

이에 더해, 별다른 설정 없이도 단일 파일의 [Vue 컴포넌트](https://vuejs.org)지원이 포함되어 있습니다. `resources/assets/js/components` 디렉토리안에 샘플 `Example.vue` 컴포넌트가 포함되어 있습니다. 또한 새로운 `resources/assets/js/app.js` 파일이 여러분의 자바스크립트 라이브러리와 만약 가능하다면 Vue 컴포넌트를 부트스트래핑 하고 설정할 것입니다. 

This structure provides more guidance on how to begin developing modern, robust JavaScript applications, without requiring your application to use any given JavaScript or CSS framework. For more information on getting started with modern Laravel frontend development, check out the new [introductory frontend documentation](/docs/5.3/frontend).

이 구조는 어플리케이션에 특정 자바스크립트 또는 CSS 프레임워크를 사용하지 않고도, 현대적이고 견고한 자바스크립트 개발을 시작할 것인가에 대한 가이드를 제공합니다. 모던한 라라벨 프론트 엔드 개발을 시작하는 방법에 대한 보다 자세한 정보는 [프론트 엔드 소개 문서](/docs/5.3/frontend)를 참고하십시오

### Routes Files
### 라우트 파일들

By default, fresh Laravel 5.3 applications contain two HTTP route files in a new top-level `routes` directory. The `web` and `api` route files provide more explicit guidance in how to split the routes for your web interface and your API. The routes in the `api` route file are automatically assigned the `api` prefix by the `RouteServiceProvider`.

기본적으로 새롭게 설치한 라라벨 5.3 어플리케이션에서는 새로운 탑레벨의 `routes` 디렉토리 안에 있는 두개의 HTTP 라우트 파일을 가지고 있습니다. `web` 과 `api` 라우트 파일은 웹 인터페이스와 API를 어떤 라우트로 나눌 것인가에 대해서 보다 명시적인 가이드를 제공합니다. `api` 라우트 파일안에 있는 라우트들은 `RouteServiceProvider` 에 의해서 자동으로 `api` prefix 가 지정됩니다. 

### Closure Console Commands
### 클로저 콘솔 명령어

In addition to being defined as command classes, Artisan commands may now be defined as simple Closures in the `commands` method of your `app/Console/Kernel.php` file. In fresh Laravel 5.3 applications, the `commands` method loads a `routes/console.php` file which allows you to define your Console commands as route-like, Closure based entry points into your application:

명령어 클래스를 정의하는 것에 더하여, 아티즌 명령어는 이제 `app/Console/Kernel.php` 파일의 `commands` 메소드 안에서 간단한 클로저로 정의할 수 있습니다. 새롭게 설치한 라라벨 5.3 어플리케이션에서는 여러분의 콜솔 명령어를 라우트처럼 어플리케이션을 가리키는 클로저를 기반으로 한 `routes/console.php`파일을 `commands` 메소드에서 로딩합니다.  

    Artisan::command('build {project}', function ($project) {
        $this->info('Building project...');
    });

For more information on Closure commands, check out the [full Artisan documentation](/docs/5.3/artisan#closure-commands).

클로저 명령어에 대한 보다 자세한 정보는 [전체 아티즌 명령어](/docs/5.3/artisan#closure-commands)를 참고하십시오.

### The `$loop` Variable
### `$loop` 변수

> {video} There is a free [video tutorial](https://laracasts.com/series/whats-new-in-laravel-5-3/episodes/7) for this feature available on Laracasts.

> {video} 라라캐스트에서 이 기능을 위한 무료 [동영상 튜토리얼](https://laracasts.com/series/whats-new-in-laravel-5-3/episodes/7) 이 있습니다.

When looping within a Blade template, a `$loop` variable will be available inside of your loop. This variable provides access to some useful bits of information such as the current loop index and whether this is the first or last iteration through the loop:

블레이드 템플릿 안에서 반복문을 구성할 때, 해당 반복문 안에서 `$loop` 변수를 사용할 수 있습니다. 이 변수는 현재 반복문의 인덱스나 반복문에서 지금이 첫번째 반복 인지, 마지막 반복인지와 같은 몇가지 유용한 정보를 제공합니다: 

    @foreach ($users as $user)
        @if ($loop->first)
            This is the first iteration.
        @endif

        @if ($loop->last)
            This is the last iteration.
        @endif

        <p>This is user {{ $user->id }}</p>
    @endforeach

For more information, consult the [full Blade documentation](/docs/5.3/blade#the-loop-variable).

보다 자세한 정보는 [전체 블레이드 문서](/docs/5.3/blade#the-loop-variable)를 참고하십시오.

<a name="laravel-5.2"></a>
## Laravel 5.2
## 라라벨 5.2

Laravel 5.2 continues the improvements made in Laravel 5.1 by adding multiple authentication driver support, implicit model binding, simplified Eloquent global scopes, opt-in authentication scaffolding, middleware groups, rate limiting middleware, array validation improvements, and more.

라라벨 5.2는 다중 인증 드라이버 지원, 묵시적 모델 바인딩, 간략해진 Eloquent 글로벌 스코프, 내장된 인증 스캐폴딩, 미들웨어 그룹, 접근 속도 제한 미들웨어, 배열 유효성 검증의 개선등 라라벨 5.1에서 기능이 개선되었습니다. 

### Authentication Drivers / "Multi-Auth"
### 인증 드라이버 / "다중-인증"

In previous versions of Laravel, only the default, session-based authentication driver was supported out of the box, and you could not have more than one authenticatable model instance per application.

이번 버전의 라라벨에서는, 별다른 설정을 하지 않은경우, 오로지 기본으로 설정된 세션을 기반으로한 인증 드라이버만 지원되었고, 어플리케이션의 하나 이상의 인증 모델 인스턴스를 가질 수 없었습니다. 

However, in Laravel 5.2, you may define additional authentication drivers as well define multiple authenticatable models or user tables, and control their authentication process separately from each other. For example, if your application has one database table for "admin" users and one database table for "student" users, you may now use the `Auth` methods to authenticate against each of these tables separately.

하지만 라라벨 5.2에서는 사용자 모델 또는 사용자 테이블을 다중으로 정의하는 것처럼 추가적인 인증 드라이버를 정의하여 각각의 경우마다 다른 프로세스로 인증을 수행할 수 있습니다. 예를 들어 어플리케이션이 하나의 "admin" 사용자 테이블을 가지고 하나의 "student" 사용자 테이블을 가지는 경우, 각각의 테이블마다 별도로 `Auth` 메소드를 사용할 수 있습니다. 

### Authentication Scaffolding
### 인증 스캐폴딩

Laravel already makes it easy to handle authentication on the back-end; however, Laravel 5.2 provides a convenient, lightning-fast way to scaffold the authentication views for your front-end. Simply execute the `make:auth` command on your terminal:

라라벨은 이미 인증을 백엔드에서 손쉽게 할 수 있도록 하였지만; 라라벨 5.2에서는 보다 편리하면서 쉽고 빠르게 스캐폴딩을 사용하여 인증에 필요한 프론트엔드 뷰를 생성할 수 있게 제공합니다. 간단하게 `make:auth` 명령어를 터미널에서 실행하면 됩니다: 

    php artisan make:auth

This command will generate plain, Bootstrap compatible views for user login, registration, and password reset. The command will also update your routes file with the appropriate routes.

이 명령어는 사용자의 로그인, 가입, 패스워드 재설정을 위한 순수한 부트스트랩 호환 뷰 파일들을 생성할 것입니다. 이 명령어는 또한 여러분의 라우트 파일에 필요한 라우트를 추가할 것입니다. 

> {note} This feature is only meant to be used on new applications, not during application upgrades.

> {note} 이 기능은 어플리케이션의 업그레이드가 아니라 새로운 어플리케이션에서 사용되는 것을 의미합니다. 

### Implicit Model Binding
### 묵시적 모델 바인딩

Implicit model binding makes it painless to inject relevant models directly into your routes and controllers. For example, assume you have a route defined like the following:

묵시적 모델 바인딩은, 라우트나 컨트롤러에 연관된 모델을 손쉽게 바로 주입할 수 있도록 합니다. 예를 들어 다음과 같은 라우트가 정의 되어 있다고 생각해 보겠습니다: 

    use App\User;

    Route::get('/user/{user}', function (User $user) {
        return $user;
    });

In Laravel 5.1, you would typically need to use the `Route::model` method to instruct Laravel to inject the `App\User` instance that matches the `{user}` parameter in your route definition. However, in Laravel 5.2, the framework will **automatically** inject this model based on the URI segment, allowing you to quickly gain access to the model instances you need.

라라벨 5.1에서는 일반적으로 라우트에 선언된 `{user}` 파라미터와 일치하는 `App\User` 인스턴스를 라라벨에 주입하도록 지시하기 위해서는 `Route::model` 메소드를 사용할 필요가 있었습니다. 그러나, 라라벨 5.2에서는 프레임워크가 **자동으로** URI 세그먼트를 기반으로 모델을 주입해줄 것이기 때문에, 필요한 모델 인스턴스를 빠르게 획득할 수 있습니다. 

Laravel will automatically inject the model when the route parameter segment (`{user}`) matches the route Closure or controller method's corresponding variable name (`$user`) and the variable is type-hinting an Eloquent model class.

라라벨은 라우트 파라미터 세그먼트가 (`{user}`) 라우트 클로저와 매칭되거나 컨트롤러 메소드의 일치하는 변수 이름(`$user`)과 Eloquent 모델 클래스가 타입힌트되어 있다면 자동으로 모델을 주입할 것입니다. 

### Middleware Groups
### 미들웨어 그룹

Middleware groups allow you to group several route middleware under a single, convenient key, allowing you to assign several middleware to a route at once. For example, this can be useful when building a web UI and an API within the same application. You may group the session and CSRF routes into a `web` group, and perhaps the rate limiter in the `api` group.

미들웨어 그룹은 여러개의 라우트 미들웨어를 간단한 키를 사용하여 하나로 묶고, 이 미들웨어들을 한번에 라우트에 할당할 수 있게 해줍니다. 예를들어, 어플리케이션에서 웹 UI와 API를 구성할 때 유용 할 수 있습니다. 여러분은 세션과 CSRF 라우트 미들웨어를 `web` 그룹으로 묶을 수도 있고, `api` 그룹 안에서는 아마도 접근 속도 제한 미들웨어를 사용할 수도 있습니다. 

In fact, the default Laravel 5.2 application structure takes exactly this approach. For example, in the default `App\Http\Kernel.php` file you will find the following:

사실, 라라벨 5.2의 어플리케이션 구조에서는 기본적으로 이러한 기능을 이미 사용합니다. 예를 들어, `App\Http\Kernel.php`파일에는 기본적으로 다음과 같이 구성된 다음 내용을 확인할 수 있습니다:

    /**
     * The application's route middleware groups.
     *
     * @var array
     */
    protected $middlewareGroups = [
        'web' => [
            \App\Http\Middleware\EncryptCookies::class,
            \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
            \Illuminate\Session\Middleware\StartSession::class,
            \Illuminate\View\Middleware\ShareErrorsFromSession::class,
            \App\Http\Middleware\VerifyCsrfToken::class,
        ],

        'api' => [
            'throttle:60,1',
        ],
    ];

Then, the `web` group may be assigned to routes like so:

그리고, `web` 그룹은 다음과 같이 라우트에 할당할 수 있습니다:

    Route::group(['middleware' => ['web']], function () {
        //
    });

However, keep in mind the `web` middleware group is *already* applied to your routes by default since the `RouteServiceProvider` includes it in the default middleware group.

다만, `web` 미들웨어 그룹은 `RouteServiceProvider`가 포함하고 있는 기본 미들웨어 그룹에 의해서 *이미* 적용되었다는 것을 주의하십시오. 

### Rate Limiting
### 접근 속도 제한

A new rate limiter middleware is now included with the framework, allowing you to easily limit the number of requests that a given IP address can make to a route over a specified number of minutes. For example, to limit a route to 60 requests every minute from a single IP address, you may do the following:

새로운 접근 속도 제한 미들웨어가 프레임워크에 포함되어 주어진 IP 에 대해서 주어진 시간내에 많은 접속을 요청하는 것을 제한할 수 있게 되었습니다. 예를들어 하나의 IP에 대해서 매분마다 60번의 요청을 할 수 있도록 제한하려면, 다음과 같이 하면 됩니다: 

    Route::get('/api/users', ['middleware' => 'throttle:60,1', function () {
        //
    }]);

### Array Validation
### 배열 유효성 검사

Validating array form input fields is much easier in Laravel 5.2. For example, to validate that each e-mail in a given array input field is unique, you may do the following:

라라벨 5.2에서 배열 form 필드 입력에 대해서 유효성을 검사 하는 것이 보다 손쉬워졌습니다. 예를 들어 주어진 배열 입력필드의 각각의 이메일이 고유한지 검증하려면 다음처럼 하면 됩니다: 

    $validator = Validator::make($request->all(), [
        'person.*.email' => 'email|unique:users'
    ]);

Likewise, you may use the `*` character when specifying your validation messages in your language files, making it a breeze to use a single validation message for array based fields:

마찬가지로, 언어 파일에서 유효성 검사 메세지를 지정하는데, `*` 문자를 사용할 수 있으며, 이는 배열기반의 필드에 대한 유효성 검사 메세지를 손쉽게 구성할 수 있게 만듭니다:

    'custom' => [
        'person.*.email' => [
            'unique' => 'Each person must have a unique e-mail address',
        ]
    ],

### Bail Validation Rule
### Bail 유효성 규칙

A new `bail` validation rule has been added, which instructs the validator to stop validating after the first validation failure for a given rule. For example, you may now prevent the validator from running a `unique` check if an attribute fails an `integer` check:

새로운 `bail` 유효성 규칙이 추가되어 유효성 검사 도중에 주어진 첫번째 규칙이 실패하면 이후의 검사를 중단할 수 있습니다. 예를 들어 `integer` 검사 규칙이 실패하면 `unique` 체크를 하지 않도록 방지할 수 있습니다.

    $this->validate($request, [
        'user_id' => 'bail|integer|unique:users'
    ]);

### Eloquent Global Scope Improvements
### Eloquent 글로벌 스코프 개선

In previous versions of Laravel, global Eloquent scopes were complicated and error-prone to implement; however, in Laravel 5.2, global query scopes only require you to implement a single, simple method: `apply`.

이전 버전의 라라벨에서는 Eloquent 글로벌 스코프는 복잡하고 구현하는데 에러가 발생하기 쉬웠습니다; 하지만 라라벨 5.2에서는 글로벌 쿼리 스코프는 단 하나의 `apply` 메소드만 구현하면 됩니다.

For more information on writing global scopes, check out the full [Eloquent documentation](/docs/{{version}}/eloquent#global-scopes).

글로벌 스코프를 작성하는 보다 자세한 내용은 [Eloquent 문서](/docs/{{version}}/eloquent#global-scopes)를 확인하십시오. 

<a name="laravel-5.1.11"></a>
## Laravel 5.1.11
## 라라벨 5.1.11

Laravel 5.1.11 introduces [authorization](/docs/{{version}}/authorization) support out of the box! Conveniently organize your application's authorization logic using simple callbacks or policy classes, and authorize actions using simple, expressive methods.

라라벨 5.1.11 에서는 [authorization-승인](/docs/{{version}}/authorization)기능이 도입되었습니다.

For more information, please refer to the [authorization documentation](/docs/{{version}}/authorization).

보다 자세한 사항은 [authorization-승인 문서](/docs/{{version}}/authorization)를 참고하십시오.

<a name="laravel-5.1.4"></a>
## Laravel 5.1.4
## 라라벨 5.1.4

Laravel 5.1.4 introduces simple login throttling to the framework. Consult the [authentication documentation](/docs/{{version}}/authentication#authentication-throttling) for more information.

라라벨 5.1.4 에서는 로그인 시도 제한 기능이 프레임워크에 도입되었습니다. 자세한 내용은 [인증 문서](/docs/{{version}}/authentication#authentication-throttling)를 참고하십시오.

<a name="laravel-5.1"></a>
## Laravel 5.1
## 라라벨 5.1

Laravel 5.1 continues the improvements made in Laravel 5.0 by adopting PSR-2 and adding event broadcasting, middleware parameters, Artisan improvements, and more.

라라벨 5.1은 PSR-2를 적용하였고, 라라벨 5.0 버전에서 이벤트 브로드캐스팅, 미들웨어 파라미터, 아티즌 개선등 기능이 개선되었습니다. 

### PHP 5.5.9+
### PHP 5.5.9이상

Since PHP 5.4 will enter "end of life" in September and will no longer receive security updates from the PHP development team, Laravel 5.1 requires PHP 5.5.9 or greater. PHP 5.5.9 allows compatibility with the latest versions of popular PHP libraries such as Guzzle and the AWS SDK.

PHP5.4가 2015년 9월에 "지원종료"되고, PHP 개발팀에서 보안 업데이트를 받을 수 없게 되므로, 라라벨 5.1은 PHP 5.5.9 버전 이상의 환경을 필요로 합니다. PHP 5.5.9는 Guzzle와 AWS SDK 처럼 인기있는 PHP 라이브러리의 최신 버전과 호환됩니다.

### LTS
### LTS

Laravel 5.1 is the first release of Laravel to receive **long term support**. Laravel 5.1 will receive bug fixes for 2 years and security fixes for 3 years. This support window is the largest ever provided for Laravel and provides stability and peace of mind for larger, enterprise clients and customers.

라라벨 5.1 은 첫번째 **장기 지원** 릴리즈 버전입니다. 라라벨 5.1은 2년간 버그 픽스와 3년의 보안 패치를 지원합니다. 이 지원 기간은 지금까지 제공된 릴리즈 중에서 가장 긴 지원 기간이며, 기업 고객과 그 사용자들에게 안정성과 편안함을 제공할 것입니다. 

### PSR-2
### PSR-2

The [PSR-2 coding style guide](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md) has been adopted as the default style guide for the Laravel framework. Additionally, all generators have been updated to generate PSR-2 compatible syntax.

[PSR-2 코딩 스타일 가이드](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md)가 라라벨 프레임워크의 표준 스타일 가이드로 적용되었습니다. 또한 모든 문법이 PSR-2와 호환되도록 재작성 되어 수정되었습니다.

### Documentation
### 문서

Every page of the Laravel documentation has been meticulously reviewed and dramatically improved. All code examples have also been reviewed and expanded to provide more relevance and context.

모든 라라벨 문서의 페이지들이 다시 검토되어, 많은 부분이 개선되었습니다. 모든 코드 예제들 또한 다시 확인되고, 보다 적절한 문맥에 맞게 수정되었습니다. 

### Event Broadcasting
### 이벤트 브로드캐스팅

In many modern web applications, web sockets are used to implement realtime, live-updating user interfaces. When some data is updated on the server, a message is typically sent over a websocket connection to be handled by the client.

많은 모던 Web 어플리케이션들에서, 실시간 처리와, 라이브 업데이트를 지원하는 사용자 인터페이스를 구현하기 위해서 웹 소켓을 사용하고 있습니다. 서버에서 데이터가 업데이트 되면, 메세지가 웹 소켓 커넥션을 통해서 클라이언트에서 처리되도록 전달됩니다. 

To assist you in building these types of applications, Laravel makes it easy to "broadcast" your events over a websocket connection. Broadcasting your Laravel events allows you to share the same event names between your server-side code and your client-side JavaScript framework.

이러한 유형의 어플리케이션을 구축하는 데 도움이 되도록 라라벨은 이벤트를 웹 소켓 연결 상에 쉽게 "브로드 캐스트"할 수 있도록 했습니다. 라라벨 이벤트를 브로드 캐스트하는 것은  같은 이벤트 이름을 서버 사이드의 코드와 클라이언트 사이드 JavaScript 프레임 워크와 공유 할 수 있게 해줍니다.

To learn more about event broadcasting, check out the [event documentation](/docs/{{version}}/events#broadcasting-events).

이벤트 브로드캐스팅에 대한 더 자세한 사항은, [이벤트 문서](/docs/{{version}}/events#broadcasting-events)를 참고하십시오.

### Middleware Parameters
### 미들웨어 파라미터

Middleware can now receive additional custom parameters. For example, if your application needs to verify that the authenticated user has a given "role" before performing a given action, you could create a `RoleMiddleware` that receives a role name as an additional argument:

미들웨어는 이제, 추가적인 사용자 정의 파라미터를 받을 수 있습니다. 예를 들어 어플리케이션에서 인증된 사용자가 주어진 액션을 처리할 수 있는 "역할"을 가지고 있는지 확인할 필요가 있다면, 추가적인 인자로 롤의 이름을 전달받는 `RoleMiddleware`를 생성할 수 있습니다. 

    <?php

    namespace App\Http\Middleware;

    use Closure;

    class RoleMiddleware
    {
        /**
         * Run the request filter.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  \Closure  $next
         * @param  string  $role
         * @return mixed
         */
        public function handle($request, Closure $next, $role)
        {
            if (! $request->user()->hasRole($role)) {
                // Redirect...
            }

            return $next($request);
        }

    }

Middleware parameters may be specified when defining the route by separating the middleware name and parameters with a `:`. Multiple parameters should be delimited by commas:

미들웨어 파라미터는 라우트를 정의 할 때 지정된 미들웨어 이름과 파라미터를 `:` 로 구분하여 지정할 수 있습니다. 여러개의 파라미터는 쉼표로 구분합니다.

    Route::put('post/{id}', ['middleware' => 'role:editor', function ($id) {
        //
    }]);

For more information on middleware, check out the [middleware documentation](/docs/{{version}}/middleware).

미들웨어에 대한 보다 자세한 정보는 [미들웨어 문서](/docs/{{version}}/middleware)를 확인하십시오. 

### Testing Overhaul
### 테스팅의 개편

The built-in testing capabilities of Laravel have been dramatically improved. A variety of new methods provide a fluent, expressive interface for interacting with your application and examining its responses. For example, check out the following test:

라라벨에 내장된 테스팅 능력이 크게 향상되었습니다. 어플리케이션 그리고 관련된 응답을들 테스트 하기 위해서 유연하고 보다 풍부한 표현이 가능한 인터페이스를 제공하는 다양한 메소드들이 제공됩니다. 다음의 테스트를 확인해 보겠습니다.

    public function testNewUserRegistration()
    {
        $this->visit('/register')
             ->type('Taylor', 'name')
             ->check('terms')
             ->press('Register')
             ->seePageIs('/dashboard');
    }

For more information on testing, check out the [testing documentation](/docs/{{version}}/testing).

테스팅과 관련된 보다 자세한 사항은 [테스팅 문서](/docs/{{version}}/testing)를 확인하십시오.

### Model Factories
### 모델 팩토리

Laravel now ships with an easy way to create stub Eloquent models using [model factories](/docs/{{version}}/database-testing#writing-factories). Model factories allow you to easily define a set of "default" attributes for your Eloquent model, and then generate test model instances for your tests or database seeds. Model factories also take advantage of the powerful [Faker](https://github.com/fzaninotto/Faker) PHP library for generating random attribute data:

라라벨은 이제, [모델 팩토리](/docs/{{version}}/database-testing#writing-factories)를 사용하여 Eloquent 모델의 스텁을 쉽게 만드는 방법이 제공됩니다. 모델 팩토리는 여러분이 Eloquent 모델을 위한 "기본" 속성들의 세트를 손쉽게 정의하도록 하고, 테스트나 데이터베이스 시드를 위한 테스트용 모델 인스턴스 생성하도록 합니다. 모델 팩토리는 또한 랜덤한 속성 데이터를 생성하는 [Faker](https://github.com/fzaninotto/Faker) PHP 라이브러리를 사용하는 강력한 장점을 가집니다. 

    $factory->define(App\User::class, function ($faker) {
        return [
            'name' => $faker->name,
            'email' => $faker->email,
            'password' => str_random(10),
            'remember_token' => str_random(10),
        ];
    });

For more information on model factories, check out [the documentation](/docs/{{version}}/database-testing#writing-factories).

모델 팩토리에 대한 보다 자세한 사항은 [문서](/docs/{{version}}/database-testing#writing-factories)를 참조하십시오.

### Artisan Improvements
### 아티즌의 향상

Artisan commands may now be defined using a simple, route-like "signature", which provides an extremely simple interface for defining command line arguments and options. For example, you may define a simple command and its options like so:

아티즌 명령어는 이제, 커맨드 라인 인자와 옵션을 정의하기 위한 매우 간단한 인터페이스를 제공하는, 라우트와 비슷한 "시그니처"를 사용하여 손쉽게 정의될 수 있습니다. 예를 들어, 다음처럼 간단한 명령과 옵션을 정의할 수 있습니다.

    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'email:send {user} {--force}';

For more information on defining Artisan commands, consult the [Artisan documentation](/docs/{{version}}/artisan).

아티즌 명령어에 대한 보다 자세한 사항은 [아티즌 문서](/docs/{{version}}/artisan)를 참고하십시오.

### Folder Structure
### 폴더 구조

To better express intent, the `app/Commands` directory has been renamed to `app/Jobs`. Additionally, the `app/Handlers` directory has been consolidated into a single `app/Listeners` directory which simply contains event listeners. However, this is not a breaking change and you are not required to update to the new folder structure to use Laravel 5.1.

보다 의도를 알기 쉽게 하기 위해서, `app/Commands` 디렉토리가 `app/Jobs`로 이름이 ​​변경되었습니다. 또한 `app/Handlers` 디렉토리는 이벤트 리스너 전용 `app/Listeners` 에 통합되었습니다. 그러나 이것은 호환성을 무너 뜨리는 변경이 아니기 때문에, Laravel 5.1의 새로운 폴더 구조로 변경 할 필요는 없습니다.

### Encryption
### 암호화

In previous versions of Laravel, encryption was handled by the `mcrypt` PHP extension. However, beginning in Laravel 5.1, encryption is handled by the `openssl` extension, which is more actively maintained.

이전 버전의 Laravel은 PHP의 mcrypt extension을 통해 암호화가 이루어지고 있었습니다. 그러나, Laravel5.1 에서는 더 활발하게 관리가 이루어지고 있는 openssl extension을 사용하여 암호화됩니다.

<a name="laravel-5.0"></a>
## Laravel 5.0
## 라라벨 5.0

Laravel 5.0 introduces a fresh application structure to the default Laravel project. This new structure serves as a better foundation for building a robust application in Laravel, as well as embraces new auto-loading standards (PSR-4) throughout the application. First, let's examine some of the major changes:

라라벨 5.0 에서는 완전히 새로운 어플리케이션 구조가 구성되었습니다. 이 새로운 구조는 라라벨이 보다 나은 어플리케이션을 구축하기 위한 좋은 기초가 되며, 어플리케이션에는 새로운 auto-loading 기준인 (PSR-4)가 적용되었습니다. 먼저 주요한 변경 사항들을 확인해 보겠습니다:

### New Folder Structure
### 새로운 폴더 구조

The old `app/models` directory has been entirely removed. Instead, all of your code lives directly within the `app` folder, and, by default, is organized to the `App` namespace. This default namespace can be quickly changed using the new `app:name` Artisan command.

이전의 `app/modules` 디렉토리는 완전히 제거되었습니다. 대신에, 여러분의 모든 코드는 `app` 폴더안으로 옮겨졌고, 기본적으로 `App` 네임스페이스 이름으로 구성되었습니다. 이 기본 네임스페이스 는 `app:name` 아티즌 명령어를 통해서 손쉽게 변경가능합니다. 

Controllers, middleware, and requests (a new type of class in Laravel 5.0) are now grouped under the `app/Http` directory, as they are all classes related to the HTTP transport layer of your application. Instead of a single, flat file of route filters, all middleware are now broken into their own class files.

컨트롤러, 미들웨어, 그리고 request-요청 ( 라라벨 5.0 의 새로운 클래스 타입)은 어플리케이션의 HTTP 전송 계층과 관련된 클래스로, `app/Http` 디렉토리 아래에 위치합니다. 구조 없이 하나로 되어 있던 라우트 필터, 전체 미들웨어는 각각의 클래스 파일로 분할되었습니다. 

A new `app/Providers` directory replaces the `app/start` files from previous versions of Laravel 4.x. These service providers provide various bootstrapping functions to your application, such as error handling, logging, route loading, and more. Of course, you are free to create additional service providers for your application.

이전의 라라벨 4.X 버전의 `app/start` 파일들이 새로운 `app/Providers` 디렉토리로 대체되었습니다. 이 서비스 프로바이더들은 에러 핸들링, 로깅, 라우트 로딩등의 어플리케이션의 부트스트래핑을 위한 다양한 기능을 제공합니다. 물론 어플리케이션에서 필요한 추가적인 서비스 프로바이더들을 자유롭게 추가할 수 있습니다. 

Application language files and views have been moved to the `resources` directory.

어플리케이션의 언어 파일과 뷰 파일들은 `resources` 디렉토리로 이동되었습니다. 

### Contracts
### Contracts

All major Laravel components implement interfaces which are located in the `illuminate/contracts` repository. This repository has no external dependencies. Having a convenient, centrally located set of interfaces you may use for decoupling and dependency injection will serve as an easy alternative option to Laravel Facades.

모든 라라벨의 주요 컴포넌트들은 `illuminate/contracts` 저장소에 있는 인터페이스들을 구현하고 있습니다. 이 저장소는 어떠한 외부 의존성도 가지고 있지 않습니다. 보다 편의를 위해서, 인터페이스들이 한곳에 모여서 위치하고, 이 인터페이스들을 사용한 디커플링과 의존성 주입은 라라벨 파사드를 통해서 다른 대안을 손쉽게 사용할 수 있게 합니다 

For more information on contracts, consult the [full documentation](/docs/{{version}}/contracts).

contracts 에 관한 보다 자세한 내용은 [문서](/docs/{{version}}/contracts)를 참고하십시오. 

### Route Cache
### 라우트 캐시

If your application is made up entirely of controller routes, you may utilize the new `route:cache` Artisan command to drastically speed up the registration of your routes. This is primarily useful on applications with 100+ routes and will **drastically** speed up this portion of your application.

어플리케이션이 컨트롤러의 라우트만으로 구성된 경우에, `route:cache` 아티즌 명령어를 사용하여, 라우트 등록 속도를 대대적으로 개선할 수 있습니다. 이 기능은 주로 어플리케이션에 100개 이상의 라우트가 있는 경우 유용하며, 어플리케이션의 라우트 부분을 **극적으로** 단축 할 수 있습니다. 

### Route Middleware
### 라우트 미들웨어

In addition to Laravel 4 style route "filters", Laravel 5 now supports HTTP middleware, and the included authentication and CSRF "filters" have been converted to middleware. Middleware provides a single, consistent interface to replace all types of filters, allowing you to easily inspect, and even reject, requests before they enter your application.

라라벨 4 스타일의 라우트 "필터"에 더하여, 라라벨 5 에서는 HTTP 미들웨어가 지원되고, 사용자 인증과 CSRF "필터"가 미들웨어로 변경되었습니다. 미들웨어는 request 가 어플리케이션에 진입하기 전에 손쉽게 확인하고, 거부할 수 있도록 모든 타입의 필터를 대체하기 위한 하나의 일관된 인터페이스를 제공합니다. 

For more information on middleware, check out [the documentation](/docs/{{version}}/middleware).

미들웨어 대한 보다 자세한 내용은 [문서](/docs/{{version}}/middleware)에서 확인하십시오.

### Controller Method Injection
### 컨트롤러 메소드 주입

In addition to the existing constructor injection, you may now type-hint dependencies on controller methods. The [service container](/docs/{{version}}/container) will automatically inject the dependencies, even if the route contains other parameters:

이미 존재하는 생성자에서의 주입에 더해, 컨트롤러의 메소드에서도 타입-힌트 의존성 주입을 사용할 수 있습니다. [서비스 컨테이너](/docs/{{version}}/container)는 라우트에 다른 파라미터를 포함하고 있더라도, 자동으로 의존성을 주입할 것입니다. 

    public function createPost(Request $request, PostRepository $posts)
    {
        //
    }

### Authentication Scaffolding
### 인증 스캐폴딩

User registration, authentication, and password reset controllers are now included out of the box, as well as simple corresponding views, which are located at `resources/views/auth`. In addition, a "users" table migration has been included with the framework. Including these simple resources allows rapid development of application ideas without bogging down on authentication boilerplate. The authentication views may be accessed on the `auth/login` and `auth/register` routes. The `App\Services\Auth\Registrar` service is responsible for user validation and creation.

사용자의 등록, 인증, 암호 재설정 컨트롤러는 별다른 설정 없이도 포함되어 있습니다. 동시에, 이에 대응하는 간단한 뷰가 `resources/views/auth` 에 위치하고 있습니다. 이에 더해, "users" 테이블 마이그레이션 파일이 프레임워크에 포함되어 있습니다. 이러한 간단한 리소스가 포함되어 있어서, 인증과 관련된 번거로운 작업 없이도 어플리케이션을 빠르게 구성할 수 있습니다. 인증 뷰는 `auth/login` 와 `auth/register` 라우트를 통해서 엑세스 할 수 있습니다. `App\Services\Auth\Registrar` 서비스가 사용자 검증과 생성과 관련된 역할을 합니다. 

### Event Objects
### 이벤트 객체

You may now define events as objects instead of simply using strings. For example, check out the following event:

이벤트는 이제 단순한 문자열 대신 객체로 정의됩니다. 다음의 이벤트 예제를 확인하십시오: 

    <?php

    class PodcastWasPurchased
    {
        public $podcast;

        public function __construct(Podcast $podcast)
        {
            $this->podcast = $podcast;
        }
    }

The event may be dispatched like normal:

다음과 같이 이벤트를 발생 시킬 수 있습니다:

    Event::fire(new PodcastWasPurchased($podcast));

Of course, your event handler will receive the event object instead of a list of data:

물론, 이벤트 핸들러는 데이터 목록 대신 이벤트 객체를 받을 것입니다.

    <?php

    class ReportPodcastPurchase
    {
        public function handle(PodcastWasPurchased $event)
        {
            //
        }
    }

For more information on working with events, check out the [full documentation](/docs/{{version}}/events).

이벤트의 동작과 관련된 보다 자세한 사항은 [문서](/docs/{{version}}/events)를 참고하십시오. 

### Commands / Queueing
### 명령어 / 큐

In addition to the queue job format supported in Laravel 4, Laravel 5 allows you to represent your queued jobs as simple command objects. These commands live in the `app/Commands` directory. Here's a sample command:

라라벨 4 에서 큐 작업 포맷을 지원하는데 더해, 라라벨 5 는 큐 작업을 간단한 명령어 객체로 나타낼 수 있게되었습니다. 이 명령어는 `app/Commands` 디렉토리에 위치합니다. 다음은 간단한 명령어 예입니다. 

    <?php

    class PurchasePodcast extends Command implements SelfHandling, ShouldBeQueued
    {
        use SerializesModels;

        protected $user, $podcast;

        /**
         * Create a new command instance.
         *
         * @return void
         */
        public function __construct(User $user, Podcast $podcast)
        {
            $this->user = $user;
            $this->podcast = $podcast;
        }

        /**
         * Execute the command.
         *
         * @return void
         */
        public function handle()
        {
            // Handle the logic to purchase the podcast...

            event(new PodcastWasPurchased($this->user, $this->podcast));
        }
    }

The base Laravel controller utilizes the new `DispatchesCommands` trait, allowing you to easily dispatch your commands for execution:

여러분의 명령어들을 손쉽게 수행할 수 있도록 라라벨의 기본 컨트롤러에서는 `DispatchesCommands` 트레이트-trait을 사용하고 있습니다. 

    $this->dispatch(new PurchasePodcastCommand($user, $podcast));

Of course, you may also use commands for tasks that are executed synchronously (are not queued). In fact, using commands is a great way to encapsulate complex tasks your application needs to perform. For more information, check out the [command bus](/docs/5.0/bus) documentation.

물론 작업을 동기적으로(큐를 통하지 않고) 실행하는 명령어를 사용할 수도 있습니다. 실제로, 명령어를 사용하는 것 자체는, 어플리케이션에서 수행해야 하는 복잡한 작업을 캡슐화 하기 위한 좋은 방법입니다. 보다 자세한 사항은 [command bus](/docs/5.0/bus) 문서를 확이하십시오. 

### Database Queue
### 데이터베이스 큐

A `database` queue driver is now included in Laravel, providing a simple, local queue driver that requires no extra package installation beyond your database software.

라라벨에 데이터베이스를 통한 간단한, 로컬 큐 드라이버를 제공하는 `database` 큐 드라이버가 포함되었습니다. 

### Laravel Scheduler
### 라라벨 스케줄러

In the past, developers have generated a Cron entry for each console command they wished to schedule. However, this is a headache. Your console schedule is no longer in source control, and you must SSH into your server to add the Cron entries. Let's make our lives easier. The Laravel command scheduler allows you to fluently and expressively define your command schedule within Laravel itself, and only a single Cron entry is needed on your server.

이전에는 개발자는 스케줄링을 원하는 각각의 콘솔 명령어를 Cron 항목으로 생성했었습니다. 그러나 이건 골치 아픈일입니다. 콘솔 스케줄링은 소스 관리되 되지 않고, Cron 항목을 추가하기 위해서는 SSH 를 통해서 서버에 접속해야만 합니다. 보다 손쉽게 하기 위해서 라라벨 명령어 스케줄러는 라라벨 자체에서 명령어 스케줄링을 보다 유연하고, 풍부한 표현이 가능하게 정의할 수 있도록 허용합니다. 서버에 필요한 Cron 항목은 단 한줄이면 충분합니다. 

It looks like this:

다음과 같이 정의할 수 있습니다:

    $schedule->command('artisan:command')->dailyAt('15:00');

Of course, check out the [full documentation](/docs/{{version}}/scheduling) to learn all about the scheduler!

스케줄러에 대해서 자세한 내용은 [문서](/docs/{{version}}/scheduling)를 참고하십시오!

### Tinker / Psysh
### Tinker / Psysh

The `php artisan tinker` command now utilizes [Psysh](https://github.com/bobthecow/psysh) by Justin Hileman, a more robust REPL for PHP. If you liked Boris in Laravel 4, you're going to love Psysh. Even better, it works on Windows! To get started, just try:

이제, `php artisan tinker` 명령어는 PHP REPL 인 Justin Hileman 의 강령한 [Psysh](https://github.com/bobthecow/psysh)를 사용합니다. 
라라벨 4의 Boris를 좋아했다면, Psysh는 더 좋아 하실 것입니다. 심지어, Windows에서도 동작합니다! 바로 사용해 보십시오.

    php artisan tinker

### DotEnv
### DotEnv

Instead of a variety of confusing, nested environment configuration directories, Laravel 5 now utilizes [DotEnv](https://github.com/vlucas/phpdotenv) by Vance Lucas. This library provides a super simple way to manage your environment configuration, and makes environment detection in Laravel 5 a breeze. For more details, check out the full [configuration documentation](/docs/{{version}}/installation#environment-configuration).

다양한 혼란을 가져왔던 중첩된 구동 환경 설정을 위한 디렉토리 구조 대신에, 라라벨 5는 이제 Vance Lucas의 [DotEnv](https://github.com/vlucas/phpdotenv)를 사용합니다. 이 라이브러리는 매우 간단한 방법으로 여러분의 구동 환경 설정을 구성할 수 있도록 하고, 라라벨 5에서 손쉽게 구동 환경을 확인할 수 있도록 만들어 줍니다. 보다 자세한 사항은 [설정 문서](/docs/{{version}}/installation#environment-configuration)를 확인하십시오. 

### Laravel Elixir
### 라라벨 엘릭서

Laravel Elixir, by Jeffrey Way, provides a fluent, expressive interface to compiling and concatenating your assets. If you've ever been intimidated by learning Grunt or Gulp, fear no more. Elixir makes it a cinch to get started using Gulp to compile your Less, Sass, and CoffeeScript. It can even run your tests for you!

Jeffrey Way의 라라벨 엘릭서는 assets 을 컴파일하고 합치기 위해서, 유연하고 풍부한 표현이 가능한 인터페이스를 제공합니다. Grunt 나 Gulp 를 배우는데 애를 먹었었다면, 더 이상 두려워할 필요가 없습니다. 엘릭서는 Gulp를 사용하여 Less, Sass 그리고 CoffeeScript를 컴파일하는것을 매우 쉽게 만들어 줍니다. 테스팅을 위해서 사용할 수도 있습니다! 

For more information on Elixir, check out the [full documentation](/docs/5.3/elixir).

엘릭서에 대한 보다 자세한 내용은 [문서](/docs/5.3/elixir)를 확인하십시오.

### Laravel Socialite
### 라라벨 Socialite

Laravel Socialite is an optional, Laravel 5.0+ compatible package that provides totally painless authentication with OAuth providers. Currently, Socialite supports Facebook, Twitter, Google, and GitHub. Here's what it looks like:

라라벨 Socialite는 라라벨 5.0+ 에 호환되는 옵션 패키지로, OAuth 프로바이더를 통한 손쉬운 인증을 제공합니다. 현재 Socialite가 지원하고 있는 것은 Facebook, Twitter, Google, GitHub입니다. 다음 예를 참고하십시오.

    public function redirectForAuth()
    {
        return Socialize::with('twitter')->redirect();
    }

    public function getUserFromProvider()
    {
        $user = Socialize::with('twitter')->user();
    }

No more spending hours writing OAuth authentication flows. Get started in minutes! The [full documentation](/docs/{{version}}/authentication#social-authentication) has all the details.

OAuth 인증을 구성하기 위해서 더 이상 시간 낭비할 필요가 없습니다. 몇 문안에 구성할 수 있을 정도입니다! 자세한 사항은 [문서](/docs/{{version}}/authentication#social-authentication)를 참고하십시오. 

### Flysystem Integration
### 파일시스템 통합

Laravel now includes the powerful [Flysystem](https://github.com/thephpleague/flysystem) filesystem abstraction library, providing pain free integration with local, Amazon S3, and Rackspace cloud storage - all with one, unified and elegant API! Storing a file in Amazon S3 is now as simple as:

라라벨은 이제, 로컬, 아마존 S3, 그리고 Rackspace 와 같은 클라우트 스토리지 서비스를 위해서, 일관되고 우아한 API를 제공하는 강력한 파일시스템 추상 라이브러리를 도입하였습니다! 아마존 S3 에 파일을 저장하는 것은 다음처럼 쉬워졌습니다. 

    Storage::put('file.txt', 'contents');

For more information on the Laravel Flysystem integration, consult the [full documentation](/docs/{{version}}/filesystem).

라라벨 파일시스템 통합에 대한 보다 자세한 내용은 [문서](/docs/{{version}}/filesystem)를 확인하십시오. 

### Form Requests
### Form Requests-요청

Laravel 5.0 introduces **form requests**, which extend the `Illuminate\Foundation\Http\FormRequest` class. These request objects can be combined with controller method injection to provide a boiler-plate free method of validating user input. Let's dig in and look at a sample `FormRequest`:

라라벨 5.0 에서는 `Illuminate\Foundation\Http\FormRequest` 클래스를 확장한 **form requests**를 도입하였습니다. 이 request 객체는 컨트롤러의 메소드 주입과 결합되어, 사용자 입력의 유효성을 검증하거는데 별다른 코드를 작성하지 않아도 되도록 합니다. `FormRequest` 샘플을 확인해 보겠습니다. 

    <?php

    namespace App\Http\Requests;

    class RegisterRequest extends FormRequest
    {
        public function rules()
        {
            return [
                'email' => 'required|email|unique:users',
                'password' => 'required|confirmed|min:8',
            ];
        }

        public function authorize()
        {
            return true;
        }
    }

Once the class has been defined, we can type-hint it on our controller action:

클래스를 정의하였다면, 컨트롤러 액션에 타입-힌트를 지정할 수 있습니다.

    public function register(RegisterRequest $request)
    {
        var_dump($request->input());
    }

When the Laravel service container identifies that the class it is injecting is a `FormRequest` instance, the request will **automatically be validated**. This means that if your controller action is called, you can safely assume the HTTP request input has been validated according to the rules you specified in your form request class. Even more, if the request is invalid, an HTTP redirect, which you may customize, will automatically be issued, and the error messages will be either flashed to the session or converted to JSON. **Form validation has never been more simple.** For more information on `FormRequest` validation, check out the [documentation](/docs/{{version}}/validation#form-request-validation).

라라벨의 서비스 컨테이너가 `FromRequest` 인스턴스의 주입을 식별하게 되면, 그 요청은 **자동으로 유효성 검증**이 될 것입니다. 이것은 여러분의 컨트롤러 액션에 호출되면 HTTP request 입력이 안전하게 지정한 form reqeust 클래스에 의해서 유효성이 검증되었다는 것을 의미합니다. 이에 더해서, request 가 유효하지 않다면 사용자가 정의한 HTTP 리다이렉션이 자동으로 발생하여 에러 메세지가 세션에 자동으로 임시 저장되거나, JSON으로 변환된다는 것을 의미합니다. **form 유효성 검사가 이보다 더 간단할 수는 없을 것입니다.** `FormRequest` 유효성 검사에 대한 보다 자세한 사항은 [문서](/docs/{{version}}/validation#form-request-validation)를 확인하시기 바랍니다. 

### Simple Controller Request Validation
### 간단한 컨트롤러 Request 유효성 검증

The Laravel 5 base controller now includes a `ValidatesRequests` trait. This trait provides a simple `validate` method to validate incoming requests. If `FormRequests` are a little too much for your application, check this out:

이제 라라벨 5의 기본 컨트롤러는 `ValidatesRequests` 트레이트-trait을 포함하고 있습니다. 이 트레이트-trait은 유입되는 reqeust-요청을 검사할 수 있는 간단한 `validate` 메소드를 제공합니다. 어플리케이션의 `FormRequest` 가 너무 크다면 다음을 확인하십시오: 

    public function createPost(Request $request)
    {
        $this->validate($request, [
            'title' => 'required|max:255',
            'body' => 'required',
        ]);
    }

If the validation fails, an exception will be thrown and the proper HTTP response will automatically be sent back to the browser. The validation errors will even be flashed to the session! If the request was an AJAX request, Laravel even takes care of sending a JSON representation of the validation errors back to you.

유효성 검사가 실패한다면, exception-예외 가 던져지고 적당한 HTTP response 가 자동으로 브라우저로 보내질 것입니다. 유효성 검사 에러는 세션에 저장될 것입니다! request-요청 이 AJAX request-요청 이라면, 라라벨은 유효성 검사 에러를 JSON으로 구성하여 돌려보낼 것입니다. 

For more information on this new method, check out [the documentation](/docs/{{version}}/validation#validation-quickstart).

이 새로운 메소드에 대한 보다 자세한 내용은 [문서](/docs/{{version}}/validation#validation-quickstart)를 참고하십시오.

### New Generators
### 새로운 Generators

To complement the new default application structure, new Artisan generator commands have been added to the framework. See `php artisan list` for more details.

새로운 기본 어플리케이션의 구조를 구성하기 위한, 새로운 아티즌 generator 명령어가 프레임워크에 추가되었습니다. `php artisan list` 를 통해서 자세한 내용을 확인하십시오. 

### Configuration Cache
### 설정 캐시

You may now cache all of your configuration in a single file using the `config:cache` command.

`config:cache` 명령어를 사용하여 설정을 하나의 파일로 구서하여 캐시할 수 있습니다. 

### Symfony VarDumper
### Symfony VarDumper

The popular `dd` helper function, which dumps variable debug information, has been upgraded to use the amazing Symfony VarDumper. This provides color-coded output and even collapsing of arrays. Just try the following in your project:

변수 디버그 정보를 덤프하는 인기있는 `dd` 헬퍼 함수가 Symfony VarDumper 를 사용하도록 업그레이드 되었습니다. 이를 통해서 색상이 지정된 결과와 열고 닫을 수 있는 배열형태로 출력이 가능합니다. 프로젝트에서 다음과 같이 사용 해보십시오. 

    dd([1, 2, 3]);

<a name="laravel-4.2"></a>
## Laravel 4.2
## 라라벨 4.2

The full change list for this release by running the `php artisan changes` command from a 4.2 installation, or by [viewing the change file on Github](https://github.com/laravel/framework/blob/4.2/src/Illuminate/Foundation/changes.json). These notes only cover the major enhancements and changes for the release.

이번 릴리즈의 전체 변경 목록은 설치한 4.2에서 `php artisan changes` 명령어를 실행하여 표시됩니다. 혹은 [Github에서 변경 내역 파일을 확인하십시오.](https://github.com/laravel/framework/blob/4.2/src/Illuminate/Foundation/changes.json). 이 노트에서는 릴리즈의 주요한 개선사항과 변경사항들만 기록되어 있습니다. 

> {note} During the 4.2 release cycle, many small bug fixes and enhancements were incorporated into the various Laravel 4.1 point releases. So, be sure to check the change list for Laravel 4.1 as well!

> {note} 4.2 릴리즈 사이클 동안, 다수의 소소한 버그 수정과 개선이 라라벨 4.1에 포함되어 왔습니다. 따라서, 라라벨 4.1의 변경 사항도 주의 깊게 확인해야 합니다

### PHP 5.4 Requirement
### PHP 5.4 이상 필요

Laravel 4.2 requires PHP 5.4 or greater. This upgraded PHP requirement allows us to use new PHP features such as traits to provide more expressive interfaces for tools like [Laravel Cashier](/docs/billing). PHP 5.4 also brings significant speed and performance improvements over PHP 5.3.

라라벨 4.2는 PHP 5.4 또는 그 이상을 필요로 합니다. PHP 필요사항이 업그레이드 된 것은 [라라벨 캐셔](/docs/billing)와 같은 툴에서 보다 유연한 인터페이스를 제공하게 해주는 트레이트-trait와 같은 새로운 PHP 기능들 사용할 수 있도록 해줍니다. PHP 5.4 는 또한 PHP 5.3 보다 유효한 속도와 성능 향상을 가져옵니다. 

### Laravel Forge
### 라라벨 Forge

Laravel Forge, a new web based application, provides a simple way to create and manage PHP servers on the cloud of your choice, including Linode, DigitalOcean, Rackspace, and Amazon EC2. Supporting automated Nginx configuration, SSH key access, Cron job automation, server monitoring via NewRelic & Papertrail, "Push To Deploy", Laravel queue worker configuration, and more, Forge provides the simplest and most affordable way to launch all of your Laravel applications.

라라벨 Forge 는 새로운 웹 기반의 어플리케이션으로 Linode, DigitalOcean, Rackspace, 그리고 아마존 EC2 와 같은 클라우드 에서 PHP 서버를 생성하고 관리하는 간단한 방법을 제공합니다. 자동화된 Nginx 설정, SSH key 엑세스, Cron 작업의 자동화, NewRelic & Papertrail 을 통한 서버 모니터링, "Push To Deploy", 라라벨 큐 worker 설정등 Forge 는 가장 간단하고, 가장 손쉬운 방법으로 라라벨 어플리케이션을 구동할 수 있도록 해줍니다. 

The default Laravel 4.2 installation's `app/config/database.php` configuration file is now configured for Forge usage by default, allowing for more convenient deployment of fresh applications onto the platform.

설치된 라라벨 4.2의 `app/config/database.php` 설정 파일은 보다 편리한 방법으로 어플리케이션을 플랫폼에 배포할 수 있도록 기본적으로 Forge 를 사용하도록 설정되어 있습니다. 

More information about Laravel Forge can be found on the [official Forge website](https://forge.laravel.com).

라라벨 Forge 에 대한 보다 자세한 내용은 [공식 Forge 웹사이트](https://forge.laravel.com) 에서 찾으실 수 있습니다. 

### Laravel Homestead
### 라라벨 홈스테드

Laravel Homestead is an official Vagrant environment for developing robust Laravel and PHP applications. The vast majority of the boxes' provisioning needs are handled before the box is packaged for distribution, allowing the box to boot extremely quickly. Homestead includes Nginx 1.6, PHP 5.6, MySQL, Postgres, Redis, Memcached, Beanstalk, Node, Gulp, Grunt, & Bower. Homestead includes a simple `Homestead.yaml` configuration file for managing multiple Laravel applications on a single box.

라라벨 홈스테드는 강력한 라라벨 및 PHP 어플리케이션을 배포하기 위한 공식 Vagrant 환경입니다. 박스의 프로비저닝의 거대한 다수는 필요는 처리하는 것 박스가 배포를 위해서 패킹되기 전에 필요로 하는 것 박스가 부트 되기 위해서 외부적으로 빠르게 

배포를 위해서 box가 pack 되기 전에 필요한 box의 준비 작업은 처리된 상태이기 때문에, 매우 빠르게 box를 시작할 수 있습니다. 홈스테드는 Nginx 1.6, PHP 5.6 MySQL, Postgres, Redis, Memcached, Beanstalk, Node, Gulp, Grunt & Bower 를 포함하고 있습니다. 홈스테드는 하나의 box 에서 여러개의 라라벨 어플리케이션을 관리하기 위해서 하나의 `Homestead.yaml` 설정 파일을 가지고 있습니다. 

The default Laravel 4.2 installation now includes an `app/config/local/database.php` configuration file that is configured to use the Homestead database out of the box, making Laravel initial installation and configuration more convenient.

설치된 라라벨 4.2는 이제 기본적으로 별다른 설정없이도 홈스테드 데이터베이스를 사용할 수 있도록 하여 라라벨의 초기 설정과 설정이 편리해 질 수 있또록 설정된 `app/config/local/database.php` 설정 파일을 포함하고 있습니다. 

The official documentation has also been updated to include [Homestead documentation](/docs/homestead).

공식 문서는 또한 포함된 [홈스테드 문서](/docs/homestead)를 통해서 업데이트 되었습니다. 

### Laravel Cashier
### 라라벨 캐셔

Laravel Cashier is a simple, expressive library for managing subscription billing with Stripe. With the introduction of Laravel 4.2, we are including Cashier documentation along with the main Laravel documentation, though installation of the component itself is still optional. This release of Cashier brings numerous bug fixes, multi-currency support, and compatibility with the latest Stripe API.

라라벨 캐셔는 Stripe 를 사용하여 구독 결제를 관리하기 위한 간단하고, 풍부한 표현이 가능한 라이브러리 입니다. 라라벨 4.2 에서 도입되고, 캐셔 문서또한 주요 라라벨 메뉴얼에 포함되었지만, 컴포넌트의 설치는 아직 선택적으로 결정해야 합니다. 이번 릴리즈의 캐셔는 많은 버그가 수정되어, 다수의 통화를 지원하고, 최신 Stripe API와 호환됩니다. 

### Daemon Queue Workers
### 데몬 큐 Worker

The Artisan `queue:work` command now supports a `--daemon` option to start a worker in "daemon mode", meaning the worker will continue to process jobs without ever re-booting the framework. This results in a significant reduction in CPU usage at the cost of a slightly more complex application deployment process.

아티즌 `queue:work` 명령어는 이제 worker를 "데몬 모드"로 시작할 수 있는 `--daemon` 옵션을 지원합니다. 이는 worker 가 프레임워크를 재시작 하지 않고도 작업을 계속 처리 할 수 있다는 것을 의미합니다. 그 결과 CPU 사용율이 효과적으로 줄어들지만, 어플리케이션의 배포 프로세스가 다소 복잡해집니다. 

More information about daemon queue workers can be found in the [queue documentation](/docs/queues#daemon-queue-worker).

데몬 큐 worker 에 대한 보다 자세한 정보는 [큐 문서](/docs/queues#daemon-queue-worker)에서 확인할 수 있습니다. 

### Mail API Drivers
### 메일 API 드라이버

Laravel 4.2 introduces new Mailgun and Mandrill API drivers for the `Mail` functions. For many applications, this provides a faster and more reliable method of sending e-mails than the SMTP options. The new drivers utilize the Guzzle 4 HTTP library.

라라벨 4.2는 `Mail` 기능을 위해서 새로운 Mailgun 와 Mandrill API 드라이버를 도입했습니다. 이는 많은 어플리케이션에서 SMTP를 통한것 보다 빠르고, 신뢰할 수 있게 이메일을 보내는 기능을 제공합니다. 새로운 드라이버는 Guzzle 4 HTTP 라이브러리를 사용하고 있습니다.

### Soft Deleting Traits
### Soft 삭제 트레이트-Trait

A much cleaner architecture for "soft deletes" and other "global scopes" has been introduced via PHP 5.4 traits. This new architecture allows for the easier construction of similar global traits, and a cleaner separation of concerns within the framework itself.

PHP 5.4 트레이트-trait을 통해서 "soft 삭제" 와 "글로벌 스코프"를 위한 보다 깔끔한 아키텍처가 도입되었습니다. 이 새로운 아키텍처는 비슷한 글로벌 트레이트의 손쉬운 생성과 프레임워크 안에서 깔끔한 관심의 분리를 가능하게 합니다. 

More information on the new `SoftDeletingTrait` may be found in the [Eloquent documentation](/docs/eloquent#soft-deleting).

새로운 `SoftDeletingTrait`에 대한 보다 자세한 정보는 [Eloquent 문서](/docs/eloquent#soft-deleting)에서 확인할 수 있습니다. 

### Convenient Auth & Remindable Traits
### 편리한 인증과 암호 알림 트레이트-trait

The default Laravel 4.2 installation now uses simple traits for including the needed properties for the authentication and password reminder user interfaces. This provides a much cleaner default `User` model file out of the box.

기본적인 라라벨 4.2 설치는 이제 인증과 패스워드 리마인더 유저 인터페이스를 위해서 필요한 속성을 포함하기 위해서 간단한 트레이트를 사용합니다. 이것은 처음부터 포함되어 있는 기본 `User` 모델 파일을 보다 깔끔하게 해줍니다. 

### "Simple Paginate"
### "간단한 페이징"

A new `simplePaginate` method was added to the query and Eloquent builder which allows for more efficient queries when using simple "Next" and "Previous" links in your pagination view.

여러분의 페이지 뷰에서 단순한 "다음"과 "이전" 링크를 사용할 때, 보다 효율적인 쿼리를 위해서, 새로운 `simplePaginate` 메소드가 쿼리와 Eloquent 빌더에 추가되었습니다. 

### Migration Confirmation
### 마이그레이션 확인

In production, destructive migration operations will now ask for confirmation. Commands may be forced to run without any prompts using the `--force` command.

실제 운영 서버에서, 위험한 마이그레이션 작업은 확인 과정을 묻게 되었습니다. `--force` 명령어를 사용하여, 프롬프트 없이 강제로 실행하게 할 수 있습니다. 

<a name="laravel-4.1"></a>
## Laravel 4.1
## 라라벨 4.1

### Full Change List
### 전체 변경 목록

The full change list for this release by running the `php artisan changes` command from a 4.1 installation, or by [viewing the change file on Github](https://github.com/laravel/framework/blob/4.1/src/Illuminate/Foundation/changes.json). These notes only cover the major enhancements and changes for the release.

이번 릴리즈의 전체 변경 목록은 설치한 4.1에서 `php artisan changes` 명령어를 실행하여 표시됩니다. 혹은 [Github에서 변경 내역 파일을 확인하십시오.](https://github.com/laravel/framework/blob/4.1/src/Illuminate/Foundation/changes.json). 이 노트에서는 릴리즈의 주요한 개선사항과 변경사항들만 기록되어 있습니다. 

### New SSH Component
### 새로운 SSH 컴포넌트

An entirely new `SSH` component has been introduced with this release. This feature allows you to easily SSH into remote servers and run commands. To learn more, consult the [SSH component documentation](/docs/4.1/ssh).

이 릴리즈에서 완전히 새로운 `SSH` 컴포넌트가 도입되었습니다. 이 기능은 여러분이 손쉽게 원격의 서버에 SSH 로 접속하고 명령어를 실행할 수 있도록 합니다. 이에 대해서 알아보려면 [SSH 컴포넌트 문서](/docs/4.1/ssh)를 참고하십시오. 

The new `php artisan tail` command utilizes the new SSH component. For more information, consult the `tail` [command documentation](http://laravel.com/docs/ssh#tailing-remote-logs).

새로운 `php artisan tail` 명령어는 새로운 SSH 컴포넌트를 사용합니다. 보다 자세한 정보는 [명령어 문서](http://laravel.com/docs/ssh#tailing-remote-logs)를 참고하십시오. 

### Boris In Tinker
### Tinker 에서의 Boris

The `php artisan tinker` command now utilizes the [Boris REPL](https://github.com/d11wtq/boris) if your system supports it. The `readline` and `pcntl` PHP extensions must be installed to use this feature. If you do not have these extensions, the shell from 4.0 will be used.

`php artisan tinker` 명령어는 시스템이 지원하는 경우 [Boris REPL](https://github.com/d11wtq/boris)을 활용합니다. 이 기능을 사용하기 위해서는 `readline` 와 `pcntl` PHP extension을 설치해야만 합니다. 이러한 extension들을 가지고 있지 않다면, 4.0 의 shell 이 사용될 것입니다. 

### Eloquent Improvements
### Eloquent 향상

A new `hasManyThrough` relationship has been added to Eloquent. To learn how to use it, consult the [Eloquent documentation](/docs/eloquent#has-many-through).

새로운 `hasManyThrough` 관계가 Eloquent 에 추가되었습니다. 어떻게 사용하는지 알아보려면 [Eloquent 문서](/docs/eloquent#has-many-through)를 참고하십시오.

A new `whereHas` method has also been introduced to allow [retrieving models based on relationship constraints](/docs/eloquent#querying-relations).

[모델을 관계-relationship의 제약에 따라서 획득하기 위해서](/docs/eloquent#querying-relations) 새로운 `whereHas` 메소드가 또한 도입되었습니다. 

### Database Read / Write Connections
### 데이터베이스 읽기 / 쓰기 커넥션

Automatic handling of separate read / write connections is now available throughout the database layer, including the query builder and Eloquent. For more information, consult [the documentation](/docs/database#read-write-connections).

쿼리빌더와 Eloquent를 포함하여 데이터베이스 레이어에서 자동으로 읽기 / 쓰기의 분리된 커넥션을 처리하는 것이 이제 가능합니다. 보다 자세한 정보는 [문서](/docs/database#read-write-connections)를 참조하십시오.

### Queue Priority
### 큐 우선순위

Queue priorities are now supported by passing a comma-delimited list to the `queue:listen` command.

큐의 우선순위는 `queue:listen` 명령어에 콤마로 구분되는 리스트를 전달하여 지원될수 있습니다. 

### Failed Queue Job Handling
### 실패한 큐 작업 처리하기

The queue facilities now include automatic handling of failed jobs when using the new `--tries` switch on `queue:listen`. More information on handling failed jobs can be found in the [queue documentation](/docs/queues#failed-jobs).

큐 기능은 이제 `queue:listen`의 `--tries` 스위치를 붙이는 것으로, 자동으로 실패된 작업들을 처리하는 기능을 포함하고 있습니다. 실패한 작업들을 처리하는 보다 자세한 정보는 [큐 문서](/docs/queues#failed-jobs)를 참조하십시오. 

### Cache Tags
### 캐시 태그

Cache "sections" have been superseded by "tags". Cache tags allow you to assign multiple "tags" to a cache item, and flush all items assigned to a single tag. More information on using cache tags may be found in the [cache documentation](/docs/cache#cache-tags).

캐시 "section"은 "tags"로 대체되었습니다. 캐시 태그는 여러가지 태그를 캐시 아이템에 붙일 수 있도록 하고, 동일한 태그를 할당한 아이템들을 한번에 삭제할 수 있도록 해줍니다. 캐시 태그를 사용하는 보다 자세한 정보는 [캐시 문서](/docs/cache#cache-tags)에서 찾을 수 있습니다. 

### Flexible Password Reminders
### 보다 유연해진 패스워드 리마인더

The password reminder engine has been changed to provide greater developer flexibility when validating passwords, flashing status messages to the session, etc. For more information on using the enhanced password reminder engine, [consult the documentation](/docs/4.1/security#password-reminders-and-reset).

패스워드를 검증하고, 상태 메세지를 세션에서 지우는등, 패스워드 리마인더 엔진이 개발자들이 보다 유연하게 사용할 수 있도록 변경되었습니다. 강화된 패스워드 리마인더 엔진에 대한 자세한 정보는 [문서](/docs/4.1/security#password-reminders-and-reset)를 참고하십시오

### Improved Routing Engine
### 라우팅 엔진 개선

Laravel 4.1 features a totally re-written routing layer. The API is the same; however, registering routes is a full 100% faster compared to 4.0. The entire engine has been greatly simplified, and the dependency on Symfony Routing has been minimized to the compiling of route expressions.

라라벨 4.1에서는 라우트 레이어를 완전히 새로 작성하였습니다. API는 동일합니다; 하지만 라우터를 등록하는 것이 4.0에 비해서 100% 빨라졌습니다. 엔진 전체는 보다 단순해지고, 라우트의 해석시 Symfony Routing에 대한 의존도가 줄어 들었습니다.

### Improved Session Engine
### 세션 엔진 개선

With this release, we're also introducing an entirely new session engine. Similar to the routing improvements, the new session layer is leaner and faster. We are no longer using Symfony's (and therefore PHP's) session handling facilities, and are using a custom solution that is simpler and easier to maintain.

이 릴리즈에서 완전히 새로운 세션 엔진을 도입되었습니다. 라우팅의 개선과 비슷하게 새로운 세션 레이어는 더 작고 빨라졌습니다. 더 이상 Symfony의 (그리고 PHP의) 세션 핸들링 기능을 사용하지 않고, 더 간단하고 유지 보수하기 쉬운 사용자 정의 처리를 사용하고 있습니다. 

### Doctrine DBAL
### 독트린 DBAL

If you are using the `renameColumn` function in your migrations, you will need to add the `doctrine/dbal` dependency to your `composer.json` file. This package is no longer included in Laravel by default.

마이그레이션 작업에서 `renameColumn` 함수를 사용하고 있다면 `composer.json` 파일에 `doctrine/dbal` 의존성을 추가해야할 필요가 있습니다. 이 패키지는 라라벨에서 더이상 기본으로 포함하고 있지 않습니다. 
