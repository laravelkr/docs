# Release Notes

- [Versioning Scheme](#versioning-scheme)
- [버전 관리 체계](#versioning-scheme)
- [Support Policy](#support-policy)
- [지원 정책](#support-policy)
- [Laravel 7](#laravel-7)
- [라라벨 7](#laravel-7)

<a name="versioning-scheme"></a>
## Versioning Scheme
## 버전 관리 체계

Laravel and its other first-party packages follow [Semantic Versioning](https://semver.org). Major framework releases are released every six months (February and August), while minor and patch releases may be released as often as every week. Minor and patch releases should **never** contain breaking changes.

라라벨과 그외 자체 제공하는 패키지들은 [유의적 버젼](https://semver.org/lang/ko/)을 따릅니다. 메이저 프레임워크 릴리즈는 6개월마다 (2월, 8월) 릴리즈되며, 마이너 릴리스는 매주 여러번 릴리즈 될 수 있습니다. 마이너 릴리즈에는 이전 버전의 호환성을 깨뜨리는 변경 사항이 **없어야** 합니다.

When referencing the Laravel framework or its components from your application or package, you should always use a version constraint such as `^7.0`, since major releases of Laravel do include breaking changes. However, we strive to always ensure you may update to a new major release in one day or less.

애플리케이션에서 라라벨 프레임워크, 라라벨의 컴포넌트 또는 패키지를 참조할 때에, 라라벨의 메이저 릴리즈가 이전 버전과 호환성을 유지하지 못하는 변경사항을 포함하고 있기 때문에 항상 `^7.0` 와 같이 참조하도록 해야 합니다. 변경사항에 대해서는 하루 안에 새로운 릴리즈를 업데이트 할 수 있도록 노력하고 있습니다.

<a name="support-policy"></a>
## Support Policy
## 지원 정책

For LTS releases, such as Laravel 6, bug fixes are provided for 2 years and security fixes are provided for 3 years. These releases provide the longest window of support and maintenance. For general releases, bug fixes are provided for 6 months and security fixes are provided for 1 year. For all additional libraries, including Lumen, only the latest release receives bug fixes. In addition, please review the database versions [supported by Laravel](/docs/{{version}}/database#introduction).

라라벨 6과 같은 LTS 릴리즈 동안에는, 2년간의 버그 픽스와 3년동안의 보안 패치가 지원됩니다. 이러한 릴리즈는 장기간에 걸친 지원과 유지보수를 제공합니다. 일반적인 릴리즈에서는 버그 픽스는 6개월, 보안 패치는 1년동안 제공됩니다. Lumen 을 포함한 모든 추가 라이브러리의 경우 최신 릴리스에서만 버그 수정을 받습니다. 
또한 [라라벨에서 지원되는 데이터베이스](/docs/{{version}}/database#introduction) 버전들도 참고하세요.

| Version | Release | Bug Fixes Until | Security Fixes Until |
| --- | --- | --- | --- |
| 5.5 (LTS) | August 30th, 2017 | August 30th, 2019 | August 30th, 2020 |
| 5.6 | February 7th, 2018 | August 7th, 2018 | February 7th, 2019 |
| 5.7 | September 4th, 2018 | March 4th, 2019 | September 4th, 2019 |
| 5.8 | February 26th, 2019 | August 26th, 2019 | February 26th, 2020 |
| 6 (LTS) | September 3rd, 2019 | September 3rd, 2021 | September 3rd, 2022 |
| 7 | March 3rd, 2020 | September 3rd, 2020 | March 3rd, 2021 |

| 버전 | 릴리즈 | 버그픽스 지원기간| 보안 패치 지원기간 |
| --- | --- | --- | --- |
| 5.5 (LTS) | 2017년 8월 30일 | 2019년 8월 30일  | 2020년 8월 30일 |
| 5.6 | 2018년 2월 7일 | 2018년 8월 7일 | 2019년 2월 7일 |
| 5.7 | 2018년 9월 4일 | 2019년 3월 4일 | 2019년 9월 4일 |
| 5.8 | 2019년 2월 26일 | 2019년 8월 26일 | 2020년 2월 26일 |
| 6 (LTS) | 2019년 9월 3일 | 2021년 9월 3일 | 2022년 9월 3일 |
| 7 | 2020년 3월 3일 | 2020년 9월 30일 | 2021년 3월 3일 |

<a name="laravel-7"></a>
## Laravel 7
## 라라벨 7

Laravel 7 continues the improvements made in Laravel 6.x by introducing Laravel Airlock, routing speed improvements, custom Eloquent casts, Blade component tags, fluent string operations, a developer focused HTTP client, first-party CORS support, improved scoping for route model binding, stub customization, database queue improvements, multiple mail drivers, query-time casts, a new `artisan test` command, and a variety of other bug fixes and usability improvements.

라라벨 7은 라라벨 Airlock, 라우트 속도 향상, 커스텀 엘로퀀트 캐스트, 블레이드 컴포넌트 태그, 막힘없는 문자열 처리, 개발자를 위한 HTTP 클라이언트, CORS 자체 지원, 개선된 라우트 모델 바인드 스코프, 스텁 커스터마이징, 데이베이스 큐 개선, 다중 메일 드라이버, 쿼리-타임 캐스트, 새로운 `artisan test` 커맨드 및 다양한 버그 수정 및 유용성 개선 등을 통해 라라벨 6에서 만들어진 것들을 지속적으로 개선해 나갈 것 입니다.

### Laravel Airlock
### 라라벨 Airlock

_Laravel Airlock was built by [Taylor Otwell](https://github.com/taylorotwell)_.

_라라벨 Airlock 은 [Taylor Otwell](https://github.com/taylorotwell)에 의해 만들어졌습니다_.

Laravel Airlock provides a featherweight authentication system for SPAs (single page applications), mobile applications, and simple, token based APIs. Airlock allows each user of your application to generate multiple API tokens for their account. These tokens may be granted abilities / scopes which specify which actions the tokens are allowed to perform.

라라벨 Airlock 은 SPAs(싱글 페이지 어플리케이션), 모바일 어플리케이션, 토큰 기반의 API를 위한 가벼운 인증 시스템을 제공합니다. Airlock은 어플리케이션 사용자에게 여러개의 API 토큰을 생성할 수 있게 해 줍니다. 이 토큰들에는 어떤 액션들이 실행될 수 있는지 허용 범위나 권한을 부여할수 있습니다.

For more information on Laravel Airlock, consult the [Airlock documentation](/docs/{{version}}/airlock).

라라벨 Airlock 에 대해 더 알고싶다면 [Airlock](/docs/{{version}}/airlock) 문서를 참고하세요.

### Custom Eloquent Casts
### 커스텀 엘로퀜트 캐스트

_Custom Eloquent casts was contributed by [Taylor Otwell](https://github.com/taylorotwell)_.

_커스텀 엘로퀜트 캐스트 개발에는 [Taylor Otwell](https://github.com/taylorotwell)이 기여했습니다_.

Laravel has a variety of built-in, helpful cast types; however, you may occasionally need to define your own cast types. You may now accomplish this by defining a class that implements the `CastsAttributes` interface.

라라벨은 여러가지로 유용한 빌트인 타입 캐스트를 가지고 있지만, 때때로 이를 직접 정의해야할 경우가 있습니다. 이제 `CastsAttributes` 인터페이스를 구현한 클래스를 정의할 수 있습니다.

Classes that implement this interface must define a `get` and `set` methods. The `get` method is responsible for transforming a raw value from the database into a cast value, while the `set` method should transform a cast value into a raw value that can be stored in the database. As an example, we will re-implement the built-in `json` cast type as a custom cast type:

이 인터페이스를 구현할때는 반드시 `get` 메소드와 `set` 메소드를 정의해야 합니다. `get` 메소드는 데이터베이스에서 가져온 원시 값을 캐스팅된 값으로 변환하며, `set` 메소드는 반대로 캐스팅된 값이 데이터베이스의 원시 값으로 변환될수 있도록 합니다. 예를 들어서, 라라벨에서 빌트인 된 `json` 캐스트 타입을 커스텀 캐스트 타입으로 다시 구현해보면 아래와 같습니다.

    <?php

    namespace App\Casts;

    use Illuminate\Contracts\Database\Eloquent\CastsAttributes;

    class Json implements CastsAttributes
    {
        /**
         * Cast the given value.
         *
         * @param  \Illuminate\Database\Eloquent\Model  $model
         * @param  string  $key
         * @param  mixed  $value
         * @param  array  $attributes
         * @return array
         */
        public function get($model, $key, $value, $attributes)
        {
            return json_decode($value, true);
        }

        /**
         * Prepare the given value for storage.
         *
         * @param  \Illuminate\Database\Eloquent\Model  $model
         * @param  string  $key
         * @param  array  $value
         * @param  array  $attributes
         * @return string
         */
        public function set($model, $key, $value, $attributes)
        {
            return json_encode($value);
        }
    }

Once you have defined a custom cast type, you may attach it to a model attribute using its class name:

커스텀 캐스팅 타입을 정의하였다면, 해당 클래스명으로 모델 속성에 사용할 수 있습니다.

    <?php

    namespace App;

    use App\Casts\Json;
    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * The attributes that should be cast to native types.
         *
         * @var array
         */
        protected $casts = [
            'options' => Json::class,
        ];
    }

To learn how to write custom Eloquent casts, including custom casts that cast to value objects, please consult the [Eloquent documentation](/docs/{{version}}/eloquent-mutators#custom-casts).

위와 같은 방식 뿐만 아니라, 밸류 객체를 사용자가 정의한대로 캐스팅하는 것을 포함한 커스텀 엘로퀜트 캐스트에 대해서 좀 더 알고 싶다면 [엘로퀜트 모델](/docs/{{version}}/eloquent-mutators#custom-casts) 문서를 참고하세요.

### Blade Component Tags & Improvements
### 블레이드 컴포넌트 태그 & 개선사항

_Blade component tags were contributed by [Spatie](https://spatie.be/), [Marcel Pociot](https://twitter.com/marcelpociot), [Caleb Porzio](https://twitter.com/calebporzio), [Dries Vints](https://twitter.com/driesvints), and [Taylor Otwell](https://github.com/taylorotwell)_.

_블레이드 컴포넌트 태그 개발에는 Spatie](https://spatie.be/), [Marcel Pociot](https://twitter.com/marcelpociot), [Caleb Porzio](https://twitter.com/calebporzio), [Dries Vints](https://twitter.com/driesvints), and [Taylor Otwell](https://github.com/taylorotwell)이 기여했습니다_.

> {tip} Blade components have been overhauled to allow tag based rendering, attribute management, component classes, inline view components, and more. Since the overhaul of Blade components is so extensive, please consult the [full Blade component documentation](/docs/{{version}}/blade#components) to learn about this feature.

> {팁} 블레이드 컴포넌트가 태그 기반 렌더링, 속성 관리, 컴포넌트 클래스, 인라인 뷰 컴포넌트 등을 할 수 있도록 완전히 정비되었습니다. 이 변경사항이 워낙 방대하기 때문에, [블레이드 컴포넌트](/docs/{{version}}/blade#components) 문서를 참고해서 해당 기능을 학습하세요.

In summary, a component may now have an associated class which specifies the data it accepts. All public properties and methods defined on the component class will automatically be made available to the component view. Any additional HTML attributes specified on the component may be managed using the automatically included `$attributes` variable, which is an attribute bag instance.

블레이드 컴포넌트는 이제 연결된 클래스를 가지며, 여기에 어떤 데이터를 받을지 정의합니다. 컴포넌트 클래스의 퍼블릭 프로퍼티들과 메소드들이 컴포넌트 뷰를 사용 가능하게 해줍니다. 또한 컴포넌트에 정의할 추가적인 HTML 속성들은 `$attributes` 백 인스턴스 변수에 자동으로 포함되어서 관리됩니다.

In this example, we will assume that an `App\View\Components\Alert` component has been defined like so:

아래와 같이 정의된 `App\View\Components\Alert` 컴포넌트를 예로 들어서 가정해보겠습니다.

    <?php

    namespace App\View\Components;

    use Illuminate\View\Component;

    class Alert extends Component
    {
        /**
         * The alert type.
         *
         * @var string
         */
        public $type;

        /**
         * Create the component instance.
         *
         * @param  string  $type
         * @return void
         */
        public function __construct($type)
        {
            $this->type = $type;
        }

        /**
         * Get the class for the given alert type.
         *
         * @return string
         */
        public function classForType()
        {
            return $this->type == 'danger' ? 'alert-danger' : 'alert-warning';
        }

        /**
         * Get the view / contents that represent the component.
         *
         * @return \Illuminate\View\View|string
         */
        public function render()
        {
            return view('components.alert');
        }
    }

And, assuming the component's Blade template has been defined like so:

그리고 이 컴포넌트의 블레이드 템플릿은 아래와 같이 정의되어 있다고 가정합니다.

    <!-- /resources/views/components/alert.blade.php -->

    <div class="alert {{ $classForType }}" {{ $attributes }}>
        {{ $heading }}

        {{ $slot }}
    </div>

The component may be rendered in another Blade view using the component's tag:

이 컴포넌트는 다른 블레이드 뷰에서 컴포넌트 태그를 통해 사용됩니다.

    <x-alert type="error" class="mb-4">
        <x-slot name="heading">
            Alert content...
        </x-slot>

        Default slot content...
    </x-alert>

As mentioned, this is just a very small sample of the functionality of the Blade component overhaul in Laravel 7 and does not demonstrate anonymous components, inline view components, and a variety of other features. Please consult the [full Blade component documentation](/docs/{{version}}/blade#components) to learn about this feature.

위에 언급한 내용은 라라벨 7의 블레이드 컴포넌트 개선사항 중 익명 컴포넌트, 인라인 뷰 컴포넌트 등의 다른 여러가지 기능까지는 모두 보여주지 않은 작은 예시일 뿐입니다. 이를 학습하기 위해선 [블레이드 컴포넌트](/docs/{{version}}/blade#components) 문서를 참고하세요.

> {note} The previous `@component` syntax for Blade components has not and will not be removed.

> {note} 기존에 블레이드 컴포넌트를 위한 `@component` 문법은 사용되지 않으며 삭제될 예정입니다.

### HTTP Client
### HTTP 클라이언트

_The HTTP client is a wrapper of Guzzle and was contributed by [Adam Wathan](https://twitter.com/adamwathan), [Jason McCreary](https://twitter.com/gonedark), and [Taylor Otwell](https://github.com/taylorotwell)_.

_HTTP 클라이언트는 Guzzle 의 래퍼이며, [Adam Wathan](https://twitter.com/adamwathan), [Jason McCreary](https://twitter.com/gonedark), and [Taylor Otwell](https://github.com/taylorotwell)이 개발에 기여했습니다_.

Laravel now provides an expressive, minimal API around the [Guzzle HTTP client](http://docs.guzzlephp.org/en/stable/), allowing you to quickly make outgoing HTTP requests to communicate with other web applications. Laravel's wrapper around Guzzle is focused on its most common use cases and a wonderful developer experience. For example, the client makes it a breeze to `POST` and interface with JSON data:

라라벨은 이제 [Guzzle HTTP 클라이언트](http://docs.guzzlephp.org/en/stable/) 기반의 풍부하면서도 최소한의 API를 제공해, 다른 웹 어플리케이션과 통신하기 위한 HTTP 요청을 쉽게 만들 수 있습니다. 라라벨의 이 Guzzle 래퍼는 대부분의 유스 케이스와 개발자의 멋진 경험에 중점을 두고 있습니다. 아래는 이 클라이언트가 JSON 데이터를 가지고 `POST` 요청을 보내는 예시입니다.

    use Illuminate\Support\Facades\Http;

    $response = Http::withHeaders([
        'X-First' => 'foo',
        'X-Second' => 'bar'
    ])->post('http://test.com/users', [
        'name' => 'Taylor',
    ]);

    return $response['id'];

In addition, the HTTP client provides fantastic, ergonomic testing functionality:

거기다 이 HTTP 클라이언트는 멋지고 인간공학적인 테스팅 기능도 제공합니다.

    Http::fake([
        // Stub a JSON response for GitHub endpoints...
        'github.com/*' => Http::response(['foo' => 'bar'], 200, ['Headers']),

        // Stub a string response for Google endpoints...
        'google.com/*' => Http::response('Hello World', 200, ['Headers']),

        // Stub a series of responses for Facebook endpoints...
        'facebook.com/*' => Http::sequence()
                                ->push('Hello World', 200)
                                ->push(['foo' => 'bar'], 200)
                                ->pushStatus(404),
    ]);

To learn more about all of the features of the HTTP client, please consult the [HTTP client documentation](/docs/{{version}}/http-client).

이 HTTP 클라이언트에 대해서 좀 더 알고싶다면 [HTTP 클라이언트](/docs/{{version}}/http-client) 문서를 참고하세요.

### Fluent String Operations
### 막힘없는 문자열 연산

_Fluent string operations were contributed by [Taylor Otwell](https://twitter.com/taylorotwell)_.

_문자열 연산에 관련 개발은 [Taylor Otwell](https://twitter.com/taylorotwell)이 기여했습니다_.

You are likely familiar with Laravel's existing `Illuminate\Support\Str` class, which provides a variety of helpful string manipulation functions. Laravel 7 now offers a more object-oriented, fluent string manipulation library built on top of these functions. You may create a fluent `Illuminate\Support\Stringable` object using the `Str::of` method. A variety of methods may then be chained onto the object to manipulate the string:

여러분은 이미 라라벨에서 여러가지 문자열 처리 기능을 제공하는 `Illuminate\Support\Str` 클래스에 익숙할 겁니다. 라라벨 7은 이 기능에 더해 더 객체 지향적이고 막힘없는 문자열 조작 기능을 제공합니다. 이제 `Str::of` 메소드를 사용해, `Illuminate\Support\Stringable` 이라는 객체를 만들 수 있습니다. 문자열 조작을 위한 여러가지 메소드들이 이 객체를 통해 체이닝 됩니다.

    return (string) Str::of('  Laravel Framework 6.x ')
                        ->trim()
                        ->replace('6.x', '7.x')
                        ->slug();

For more information on the methods available via fluent string manipulation, please consult its [full documentation](/docs/{{version}}/helpers#fluent-strings).

이러한 문자열 조작을 통해 가능한 메소드들에 대해서는 [막힘없는 문자열 연산](/docs/{{version}}/helpers#fluent-strings) 문서를 참고하세요.

### Route Model Binding Improvements
### 라우트 모델 바인딩 개선

_Route model binding improvements were contributed by [Taylor Otwell](https://twitter.com/taylorotwell)_.

_라우트 모델 바인딩 개선에 대한 개발에는 [Taylor Otwell](https://twitter.com/taylorotwell)이 기여했습니다_.

#### Key Customization
#### 키 커스터마이징

Sometimes you may wish to resolve Eloquent models using a column other than `id`. To do so, Laravel 7 allows you to specify the column in the route parameter definition:

때때로 엘로퀜트 모델에 대한 의존성을 `id` 컬럼 대신에 다른 컬럼을 통해 결정하고 싶을 경우가 있을 것입니다. 라라벨 7에서는 라우팅 파라미터 정의에 모델의 컬럼을 명시할 수 있습니다.

    Route::get('api/posts/{post:slug}', function (App\Post $post) {
        return $post;
    });

#### Automatic Scoping
#### 자동 스코핑

Sometimes, when implicitly binding multiple Eloquent models in a single route definition, you may wish to scope the second Eloquent model such that it must be a child of the first Eloquent model. For example, consider this situation that retrieves a blog post by slug for a specific user:

하나의 라우트 정의에 여러 개의 엘로퀜트 모델을 바인딩할 때, 두 번째 엘로퀜트 모델이 첫 번째 엘로퀜트 모델의 자식 관계이어야 할 경우가 있습니다. 아래와 같이 특정 유저의 블로그 포스팅을 가져와야하는 상황을 가정해봅시다.

    use App\Post;
    use App\User;

    Route::get('api/users/{user}/posts/{post:slug}', function (User $user, Post $post) {
        return $post;
    });

When using a custom keyed implicit binding as a nested route parameter, Laravel 7 will automatically scope the query to retrieve the nested model by its parent using conventions to guess the relationship name on the parent. In this case, it will be assumed that the `User` model has a relationship named `posts` (the plural of the route parameter name) which can be used to retrieve the `Post` model.

커스텀 키를 이용한 암시적인 바인딩을 이러한 중첩된 라우트 파라미터로 사용할 경우, 라라벨 7은 부모 모델이 사용하는 컨벤션에 의해 관계명을 추측하고, 자동으로 쿼리 범위를 잡습니다. 이 경우, `User` 모델은 `Post` 모델을 조회하기 위해 `posts` (라우트 파라미터의 복수형) 관계를 봅니다.

For more information on route model binding, please consult the [routing documentation](/docs/{{version}}/routing#route-model-binding).

이 라우트 모델 바인딩에 대해서 좀 더 알고 싶다면 [라우팅](/docs/{{version}}/routing#route-model-binding) 문서를 참고하세요.

### Multiple Mail Drivers
### 다중 메일 드라이버

_Multiple mail driver support was contributed by [Taylor Otwell](https://twitter.com/taylorotwell)_.

_다중 메일 드라이버 지원은 [Taylor Otwell](https://twitter.com/taylorotwell)이 기여했습니다_.

Laravel 7 allows the configuration of multiple "mailers" for a single application. Each mailer configured within the `mail` configuration file may have its own options and even its own unique "transport", allowing your application to use different email services to send certain email messages. For example, your application might use Postmark to send transactional mail while using Amazon SES to send bulk mail.

라라벨 7은 하나의 어플리케이션에서 다양한 "메일러" 설정을 지원합니다. `mail` 설정 파일 안에 있는 각각의 메일러는 서로 다른 옵션 뿐만 아니라, 어플리케이션에서 다른 메일 발송 서비스를 사용하게 하는 고유한 "트랜스포트" 를 가지고 있습니다. 예를 들어, 어플리케이션이 트랜잭셔널 메일(역자 주: 회원가입, 상품 구매등 이벤트 발생시 발송하는 메일)에는 Postmark를 사용하고, 벌크 메일(역자 주: 마케팅 등을 위해 동시에 대량으로 발송하는 메일)에는 Amazon SES를 사용할 수 있습니다.

By default, Laravel will use the mailer configured as the `default` mailer in your `mail` configuration file. However, you may use the `mailer` method to send a message using a specific mailer configuration

기본적으로 라라벨은 `mail` 설정 파일 내에서 `default` 메일러를 사용합니다. 하지만 `mailer` 메소드를 사용해 특정한 메일러 설정을 사용할 수도 있습니다.

    Mail::mailer('postmark')
            ->to($request->user())
            ->send(new OrderShipped($order));

### Route Caching Speed Improvements
### 라우트 캐싱 속도 향상

_The route caching speed improvements were contributed by upstream [Symfony](https://symfony.com) contributors and [Dries Vints](https://twitter.com/driesvints)_.

_라우트 캐싱 속도 향상은 업스트림 [Symfony](https://symfony.com) 개발 기여자들과 [Dries Vints](https://twitter.com/driesvints)가 개발에 기여했습니다_.

Laravel 7 includes a new method of matching compiled, cached routes that have been cached using the `route:cache` Artisan command. On large applications (for example, applications with 800 or more routes), these improvements can result in a **2x speed improvement** in requests per second on a simple "Hello World" benchmark. No changes to your application are required.

라라벨 7은 `route:cache` 아티즌 커맨드를 사용해 캐싱되던 매칭 컴파일 및 라우트 캐싱에 대해 새로운 방식을 포함하고 있습니다. 대형 어플리케이션(예를 들어, 800개 이상의 라우트를 가지는 어플리케이션)에서 이 개선사항은 간단한 "Hello World" 벤치마크에서 초당 리퀘스트 수의 **2배의 성능 향상** 을 가져옵니다. 사용자 어플리케이션에 대한 변경사항은 필요하지 않습니다.

### CORS Support
### CORS 지원

_CORS support was contributed by [Barry vd. Heuvel](https://twitter.com/barryvdh)_.

_CORS 지원은 [Barry vd. Heuvel](https://twitter.com/barryvdh)이 기여했습니다._

Laravel 7 includes first-party support for configuring Cross-Origin Resource Sharing (CORS) `OPTIONS` request responses by integrating the popular Laravel CORS package written by Barry vd. Heuvel. A new `cors` configuration is included in the [default Laravel application skeleton](https://github.com/laravel/laravel/blob/develop/config/cors.php).

라라벨 7은 Barry vd. Heuvel이 작성한 라라벨 패키지를 통합하면서 크로스-원본 자원 공유(CORS) `OPTIONS` 요청 및 응답을 설정하는 것을 자체적으로 지원합니다. 새로운 `cors` 설정이 [라라벨 기본 어플리케이션 스켈레톤](https://github.com/laravel/laravel/blob/develop/config/cors.php)에 포함되어 있습니다.

For more information on CORS support in Laravel 7.x, please consult the [CORS documentation](/docs/{{version}}/routing#cors).

라라벨 7.x의 CORS 설정에 대해서 좀 더 알고싶다면 [CORS](/docs/{{version}}/routing#cors) 문서를 참고하세요.

### Query Time Casts
### 쿼리 타임 캐스트

_Query time casting was contributed by [Matt Barlow](https://github.com/mpbarlow)_.
_쿼리 타임 캐스팅 개발은 Matt Barlow](https://github.com/mpbarlow)가 기여했습니다_.

Sometimes you may need to apply casts while executing a query, such as when selecting a raw value from a table. For example, consider the following query:

쿼리를 실행할 때 데이터베이스의 원시 값을 셀렉트하면서 바로 캐스팅을 적용해야 할 필요가 있을겁니다. 예를 들어, 아래와 같은 쿼리를 살펴봅시다.

    use App\Post;
    use App\User;

    $users = User::select([
        'users.*',
        'last_posted_at' => Post::selectRaw('MAX(created_at)')
                ->whereColumn('user_id', 'users.id')
    ])->get();

The `last_posted_at` attribute on the results of this query will be a raw string. It would be convenient if we could apply a `date` cast to this attribute when executing the query. To accomplish this, we may use the `withCasts` method provided by Laravel 7:

쿼리 결과에서 `last_posted_at` 속성은 문자열입니다. 만약 쿼리할때 이걸 `date` 캐스팅이 자동으로 적용된다면 좀 더 편리할 것입니다. 이를 라라벨 7에서 제공되는 `withCasts` 메소드를 통해서 사용 가능합니다.

    $users = User::select([
        'users.*',
        'last_posted_at' => Post::selectRaw('MAX(created_at)')
                ->whereColumn('user_id', 'users.id')
    ])->withCasts([
        'last_posted_at' => 'date'
    ])->get();

### MySQL 8+ Database Queue Improvements
### MySQL 8 이상 데이터베이스 큐 개선

_MySQL database queue improvements were contributed by [Mohamed Said](https://github.com/themsaid)_.

_MySQL 데이터베이스 큐 개선은 [Mohamed Said](https://github.com/themsaid)가 기여했습니다_.

In previous releases of Laravel, the `database` queue was not considered robust enough for production usage, due to deadlocks. However, Laravel 7 provides improvements to applications using MySQL 8+ as their database backed queue. By using the `FOR UPDATE SKIP LOCKED` clause and other SQL enhancements, the `database` driver may now safely be used in higher volume production applications.

라라벨의 이전 버전에서는 `database` 큐는 데드락 때문에 프로덕션 환경에서 사용하기에는 어려웠지만, 라라벨 7은 MySQL 8 이상 버전에서 데이터베이스에서 지원하는 큐를 통해 이에 대한 개선을 제공합니다. `database` 큐 드라이버는 `FOR UPDATE SKIP LOCKED` 쿼리 절 (역자 주: 락이 걸려 있는 row를 제외하고 쿼리를 바로 실행하는 옵션)과 다른 SQL 향상을 사용해, 좀 더 안전하게 대용량 프로덕션 환경의 어플리케이션에서 사용될 수 있습니다.

### Artisan `test` Command
### 아티즌 `test` 커맨드

_The `test` command was contributed by [Nuno Maduro](https://twitter.com/enunomaduro)_.
_`test` 커맨드 추가는 [Nuno Maduro](https://twitter.com/enunomaduro)가 기여했습니다_.

In addition to the `phpunit` command, you may now use the `test` Artisan command to run your tests. The Artisan test runner provides beautiful console UX and more information regarding the test that is currently running. In addition, the runner will automatically stop on the first test failure:

테스트를 실행할 때 `phpunit` 커맨드 뿐만 아니라 이제 아티즌의 `test` 커맨드를 사용할 수 있습니다. 아티즌의 테스트 러너는 아름다운 콘솔 유저 경험과 현재 어떤 테스트가 실행 중인가에 대한 더 많은 정보를 제공합니다. 또한 테스트 러너는 첫 번째 테스트 실패가 발생할 경우에 자동으로 중단됩니다.

    php artisan test

<p align="center">
<img src="https://res.cloudinary.com/dtfbvvkyp/image/upload/v1582142435/Screen_Shot_2020-02-19_at_2.00.01_PM.png">
</p>

Any arguments that can be passed to the `phpunit` command may also be passed to the Artisan `test` command:

`phpunit` 커맨드 실행시 전달될 수 있는 어떠한 인수 역시 아티즌 `test` 커맨드를 통해 전달될 수 있습니다.

    php artisan test --group=feature

### Markdown Mail Template Improvements
### 마크다운 메일 템플릿 개선

_Markdown mail template improvements were contributed by [Taylor Otwell](https://twitter.com/taylorotwell)_.

_마크다운 메일 템플릿 개선은 [Taylor Otwell](https://twitter.com/taylorotwell)이 기여했습니다_.

The default Markdown mail template has received a fresh, more modern design based on the Tailwind CSS color palette. Of course, this template can be published and customized according to your application's needs:

기본 마크다운 메일 템플릿이 테일윈드 CSS 컬러 팔레트에 기반해 좀 더 새롭고 모던한 디자인을 받아들였습니다. 물론 템플릿은 어플리케이션의 용도에 따라 발행되고 커스터마이징 될 수 있습니다.

<p align="center">
<img src="https://res.cloudinary.com/dtfbvvkyp/image/upload/v1582142674/Screen_Shot_2020-02-19_at_2.04.11_PM.png">
</p>

For more information on Markdown mail, please consult the [mail documentation](/docs/{{version}}/mail#markdown-mailables).

마크다운 메일에 대해서 좀 더 알고 싶다면 [메일](/docs/{{version}}/mail#markdown-mailables) 문서를 참고하세요.

### Stub Customization
### 스텁 커스터마이즈

_Stub customization was contributed by [Taylor Otwell](https://twitter.com/taylorotwell)_.

_스텁 커스터마이즈는 [Taylor Otwell](https://twitter.com/taylorotwell)이 기여했습니다_.

The Artisan console's `make` commands are used to create a variety of classes, such as controllers, jobs, migrations, and tests. These classes are generated using "stub" files that are populated with values based on your input. However, you may sometimes wish to make small changes to files generated by Artisan. To accomplish this, Laravel 7 provides the `stub:publish` command to publish the most common stubs for customization:

아티즌 콘솔의 `make` 커맨드는 컨트롤러, job, 마이그레이션, 테스트 등의 여러가지 클래스를 생성하기 위해 사용됩니다. 이런 클래스들은 커맨드 입력값에 따라 "스텁" 이라고 하는 파일을 사용해 생성됩니다. 하지만 때때로 아티즌에 대해 생성되는 파일에 대해 약간의 변화를 주고싶을 때가 있습니다. 라라벨 7에서는 대부분의 공통 스텁 파일을 커스터마이징해 만들 수 있는 `stub:publish` 커맨드를 제공합니다.

    php artisan stub:publish

The published stubs will be located within a `stubs` directory in the root of your application. Any changes you make to these stubs will be reflected when you generate their corresponding classes using Artisan `make` commands.

생성된 스텁은 어플리케이션 루트 디렉토리 내 `stubs` 디렉토리에 위치하게 됩니다. 이렇게 생성된 스텁 파일에 대해 추가한 변경사항은 이후 아티즌 `make` 커맨드를 사용해 해당하는 클래스를 생성할 때 반영됩니다.

### Queue `maxExceptions` Configuration
### 큐 `maxExceptions` 설정

_The `maxExceptions` property was contributed by [Mohamed Said](https://twitter.com/themsaid)_.

_`maxExceptions` 프로퍼티 추가는 [Mohamed Said](https://twitter.com/themsaid)가 기여했습니다_.

Sometimes you may wish to specify that a job may be attempted many times, but should fail if the retries are triggered by a given number of exceptions. In Laravel 7, you may define a `maxExceptions` property on your job class:

큐에서 job이 여러 번 재시도 될 수 있게 명시하더라도, 정해놓은 횟수만큼 예외가 발생할경우 job을 실패 처리하길 원할 때가 있습니다. 라라벨 7에서는 job 클래스의 프로퍼티 중 `maxExceptions` 를 정의할 수 있습니다.

    <?php

    namespace App\Jobs;

    class ProcessPodcast implements ShouldQueue
    {
        /**
         * The number of times the job may be attempted.
         *
         * @var int
         */
        public $tries = 25;

        /**
         * The maximum number of exceptions to allow before failing.
         *
         * @var int
         */
        public $maxExceptions = 3;

        /**
         * Execute the job.
         *
         * @return void
         */
        public function handle()
        {
            Redis::throttle('key')->allow(10)->every(60)->then(function () {
                // Lock obtained, process the podcast...
            }, function () {
                // Unable to obtain lock...
                return $this->release(10);
            });
        }
    }

In this example, the job is released for ten seconds if the application is unable to obtain a Redis lock and will continue to be retried up to 25 times. However, the job will fail if three unhandled exceptions are thrown by the job.

위 예제에서 job은 어플리케이션이 레디스에서 락을 획득하지 못하면 10초간 기다리는 것을 25번까지 재시도할수 있습니다. 하지만, 처리되지 않은 예외가 3번 발생시 job을 실패처리하게 됩니다.