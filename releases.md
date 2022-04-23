# 릴리즈 노트

- [버전 관리 체계](#versioning-scheme)
- [지원 정책](#support-policy)
- [라라벨 9](#laravel-9)

<a name="versioning-scheme"></a>
## 버전 관리 체계

라라벨과 그외 자체적으로 제공하는 패키지들은 [시멘틱버저닝-유의적 버젼체계](https://semver.org/lang/ko/)을 따릅니다. 메이저 프레임워크 릴리즈는 1년마다 (~2월) 릴리즈되며, 마이너 릴리스는 매주 여러번 릴리즈 될 수 있습니다. 마이너 릴리즈에는 이전 버전의 호환성을 깨뜨리는 변경 사항이 **없어야** 합니다.

애플리케이션에서 라라벨 프레임워크, 라라벨의 컴포넌트 또는 패키지를 참조할 때, 메이저 릴리즈에서는 (ex 8.x -> 9.x) 이전 버전과 호환성이 깨지는 변경사항을 포함하고 있기 때문에 항상 `^9.0` 와 같이 참조하도록 해야 합니다. 변경사항에 대해서는 하루 안에 새로운 릴리즈를 업데이트 할 수 있도록 노력하고 있습니다.

<a name="named-arguments"></a>
#### 이름이 지정된 인자 전달-Named Arguments

[이름이 지정된 인자 전달](https://www.php.net/manual/en/functions.arguments.php#functions.named-arguments) 기능은 라라벨의 하위 호환성 지침에 포함되지 않습니다. 라라벨 코드베이스를 개선하기 위해 필요한 경우 함수 매개 변수의 이름을 바꿀 수 있습니다. 따라서 라라벨 메서드를 호출 할 때 명명 된 인수를 사용하는 경우에는 매개 변수 이름이 향후 변경 될 수 있음을 이해하고 신중하게 수행해야합니다.

<a name="support-policy"></a>
## 지원 정책

모든 라라벨 릴리즈는 18개월의 버그 픽스와 2년동안의 보안 패치가 지원됩니다. Lumen 을 포함한 모든 추가 라이브러리의 경우 최신 릴리스에서만 버그 수정을 받습니다.

| 버전 | PHP (*)            | 릴리즈          | 버그픽스 지원기간   | 보안 패치 지원기간  |
| --- |--------------------|--------------|-------------|-------------|
| 6 (LTS) | 7.2 - 8.0          | 2019년 9월 3일  | 2022년 1월 25일 | 2022년 9월 6일 |
| 7 | 7.2 - 8.0          | 2020년 3월 3일  | 2020년 10월 6일 | 2021년 3월 3일 |
| 8 | 7.3 - 8.1          | 2020년 9월 8일  | 2022년 7월 26일 | 2023년 1월 24일 |
| 9 | 8.0 - 8.1          | 2022년 2월 8일  | 2023년 8월 8일 | 2024년 2월 8일 |
| 10 | 8.1 | 2023년 2월 7일 | 2024년 8월 7일 | 2025년 2월 7일 |

<div class="version-colors">
    <div class="end-of-life">
        <div class="color-box"></div>
        <div>End of life</div>
    </div>
    <div class="security-fixes">
        <div class="color-box"></div>
        <div>Security fixes only</div>
    </div>
</div>

(*) Supported PHP versions

<a name="laravel-9"></a>
## 라라벨 9

라라벨은 이전 버전까지는 6개월 마다 메이저 버전이 릴리즈 되었지만 라라벨 8 릴리즈와 함께 연간 릴리즈 정책으로 전환했습니다. 이러한 정책의 변경은 커뮤니티의 관리부담을 줄임과 동시에 개발팀에게 하위호환을 깨트리지 않고 새롭고 강력한 기능을 제공하는 도전과제를 가져다 주었습니다. 이에 따라 라라벨 8에서는 병렬테스트 지원, 개선된 Breeze start kit, HTTP 클라이언트의 개선, "has one of many"와 같은 새로운 엘로퀀트 연관관계 타입 도입과 같은 다양하고 강력한 기능이 추가되었습니다. 

그리고, 현재의 메이저 버전이 유지되는 동안 새로운 기능을 계속 추가해나가겠다는 약속으로 인해 의존성의 변경과 같은 작업은 미래의 "메이저" 릴리즈로 이어집니다.

라라벨 9는 Symfony 6.0 컴포넌트 지원, Symfony Mailer 도입, Flysystem 3.0 업데이트, `route:list` 출력 결과 개선, 라라벨 Scout 데이터베이스 엔진, 새로운 엘로퀀트 accessor / mutator 문법, Enum을 활용한 묵시적 라우트 바인딩등 다양한 버그 수정과 사용성 개선을 통해서 라라벨 8.x 를 개선합니다.

<a name="php-8"></a>
### PHP 8.0

라라벨 9.x 은 PHP 8.0 이상버전을 필요로 합니다.

<a name="symfony-mailer"></a>
### Symfony Mailer

_Symfony Mailer는 [Dries Vints](https://github.com/driesvints)_, [James Brooks](https://github.com/jbrooksuk), [Julius Kiekbusch](https://github.com/Jubeki) 가 기여하였습니다.

이전버전까지는 외부로 이메일을 발송하는데 [Swift Mailer](https://swiftmailer.symfony.com/docs/introduction.html)가 사용되었습니다. 그러나 이라이브러리가 더이상 관리되고 있지 않고 있어서 Symfony Mailer 로 대체되었습니다.

애플리케이션이 Symfony Mailer와 호환되는지 확인하려면 [업그레이드 가이드](/docs/{{version}}/upgrade#symfony-mailer)를 참고하시기 바랍니다.

<a name="flysystem-3"></a>
### Flysystem 3.x

_Flysystem 3.x 지원은 [Dries Vints](https://github.com/driesvints)가 기여하였습니다._

라라벨 9.x 는 flysystem 의존성을 flysystem 3.x 으로 업그레이드 하였습니다. Flysystem 은 `Storage` 파사드게 제공하는 모든 기능을 지원합니다. 

애플리케이션이 Flysystem 3.x 와 호환되는지 확인하려면 [업그레이드 가이드](/docs/{{version}}/upgrade#flysystem-3)를 참고하십시오.

<a name="eloquent-accessors-and-mutators"></a>
### 엘로퀀트 Accessors / Mutators 기능 향상

_엘로퀀트 accessors / mutators 기능 향상은 [Taylor Otwell](https://github.com/taylorotwell)가 기여하였습니다_.

라라벨 9.x 는 엘로퀀트 [accessor 및 mutator](/docs/{{version}}/eloquent-mutators#accessors-and-mutators)를 정의하는 새로운 방법을 제공합니다. 라라벨의 이전 버전에서 accessor 와 mutator를 정의하는 유일한 방법은 다음과 같이 모델에 prefix 메서드를 정의하는 것이었습니다.

```php
public function getNameAttribute($value)
{
    return strtoupper($value);
}

public function setNameAttribute($value)
{
    $this->attributes['name'] = $value;
}
```

그렇지만 라라벨 9.x 에서는 `Illuminate\Database\Eloquent\Casts\Attribute` 타입을 반환하는 메소드를 정의할 수 있습니다.

```php
use Illuminate\Database\Eloquent\Casts\Attribute;

public function name(): Attribute
{
    return new Attribute(
        get: fn ($value) => strtoupper($value),
        set: fn ($value) => $value,
    );
}
```

이에 더하여, accessor를 정의하는 이 새로운 방식은 [커스텀 캐스트 클래스](/docs/{{version}}/eloquent-mutators#custom-casts)와 마찬가지로 속성에서 반환되는 객체 값을 캐시합니다.

```php
use App\Support\Address;
use Illuminate\Database\Eloquent\Casts\Attribute;

public function address(): Attribute
{
    return new Attribute(
        get: fn ($value, $attributes) => new Address(
            $attributes['address_line_one'],
            $attributes['address_line_two'],
        ),
        set: fn (Address $value) => [
            'address_line_one' => $value->lineOne,
            'address_line_two' => $value->lineTwo,
        ],
    );
}
```

<a name="enum-casting"></a>
### Enum 엘로퀀트 속성 캐스팅

> {note} Enum 캐스팅은 PHP 8.1이상 버전에서만 사용가능합니다. 

_Enum 캐스팅은 [Mohamed Said](https://github.com/themsaid)가 기여하였습니다_.

엘로퀀트는 이제 속성값을 PHP의 [Enums](https://www.php.net/manual/en/language.enumerations.backed.php) 타입으로 캐스팅 할 수 있습니다. 이렇게 하기 위해서는 모델의 `$cast` 속성 배열에 캐스팅하려는 속성의 키와 Enum 타입을 지정하면 됩니다.

    use App\Enums\ServerStatus;

    /**
     * The attributes that should be cast.
     *
     * @var array
     */
    protected $casts = [
        'status' => ServerStatus::class,
    ];

모델에 캐스트 되는 속성을 정의한 다음에는 지정된 속성이 자동으로 enum 으로 캐스팅 되어 값을 확인할 수 있습니다. 

    if ($server->status == ServerStatus::provisioned) {
        $server->status = ServerStatus::ready;

        $server->save();
    }

<a name="implicit-route-bindings-with-enums"></a>
### 묵시적 Enum 라우트 바인딩

_묵시적 Enum 바인딩은 [Nuno Maduro](https://github.com/nunomaduro)가 기여하였습니다_.

PHP 8.1 부터는 [Enums](https://www.php.net/manual/en/language.enumerations.backed.php)을 지원합니다. 라라벨 9.x는 라우트를 정의할 때 Enum 타입을 힌트하는 기능을 도입하였습니다. 라라벨의 URI에서 해당 라우트의 세그먼트가 유효한 Enum 값인 경우에만 라우팅 로직을 처리합니다. 그렇지 않은 경우에는 자동으로 HTTP 404 응답이 반환됩니다. 예를 들어 다음과 같은 Enum 이 정의되어 있다면.  

```php
enum Category: string
{
    case Fruits = 'fruits';
    case People = 'people';
}
```

`{category}` 라우트의 세그먼트는 `fruits` 또는 `people`인 경우에만 정의한 라우트가 동작합니다. 세그먼트 값이 일치하지 않으면 404 HTTP 응답을 반환됩니다.

```php
Route::get('/categories/{category}', function (Category $category) {
    return $category->value;
});
```

<a name="forced-scoping-of-route-bindings"></a>
### 라우트 바인딩의 스코프 강제하기

_라우트 바인딩 스코프 강제하기 기능은 [Claudio Dekker](https://github.com/claudiodekker)가 기여하였습니다._.

라라벨의 이전 릴리즈에서는 라우트 정의에서 여러개의 모델을 바인딩할 때 두 번째 모델은 이전 모델의 하위 모델이어야 했습니다. 예를 들어 특정 사용자의 블로그 포스트를 슬러그를 통해서 조회하는 라우트 정의를 생각해보겠습니다. 

    use App\Models\Post;
    use App\Models\User;

    Route::get('/users/{user}/posts/{post:slug}', function (User $user, Post $post) {
        return $post;
    });

중첩된 라우트 파라미터로 커스텀 키가 지정된 묵시적 바인딩을 사용할 때, 라라벨은 자동으로 중첩된 모델을 조회하기 위해서 상위 모델에 대한 연관관계이름을 추정하는 컨벤션을 사용하여 쿼리의 범위를 지정합니다. 하지만 이 동작은 이전에 라우트 바인딩에 커스텀키가 사용되었을 때만 가능했습니다.   

라라벨 9.x 에서는 커스텀 키가 제공되지 않은 경우에도 "하위" 바인딩의 범위를 지정할 수 있습니다. 이를 위해서는 라우트를 정의할 때 `scopeBindings` 메소드를 호출하면 됩니다.

    use App\Models\Post;
    use App\Models\User;

    Route::get('/users/{user}/posts/{post}', function (User $user, Post $post) {
        return $post;
    })->scopeBindings();

또는 전체 라우트 그룹정의에서 스코프 바인딩을 사용할수도 있습니다.

    Route::scopeBindings()->group(function () {
        Route::get('/users/{user}/posts/{post}', function (User $user, Post $post) {
            return $post;
        });
    });

<a name="controller-route-groups"></a>
### 컨트롤러 라우트 그룹

_라우트 그룹의 기능향상은 [Luke Downing](https://github.com/lukeraymonddowning)가 기여하였습니다._.

`controller` 메소드를 사용하여 라우트 그룹 안에 정의하는 모든 라우트에 대해서 공통 컨트롤러를 연결할 수 있습니다. 그리고 그룹 안에서 정의하는 라우트는 컨트롤러의 메소드 이름만 지정하면 됩니다.

    use App\Http\Controllers\OrderController;

    Route::controller(OrderController::class)->group(function () {
        Route::get('/orders/{id}', 'show');
        Route::post('/orders', 'store');
    });

<a name="full-text"></a>
### Full Text 인덱싱 / Where 절

_Full text 인덱스와 "where" 절에 대한 기능은 [Taylor Otwell](https://github.com/taylorotwell) 및 [Dries Vints](https://github.com/driesvints)가 기여하였습니다_.

MySQL 및 PostgreSQL을 사용할 때 컬럼을 정의하는 `fullText` 메소드를 사용하면 full text 인덱스를 생성할 수 있습니다.

    $table->text('bio')->fullText();

또한 `whereFullText` 와 `orWhereFullText` 메서드를 사용하여 [full text 인덱스](/docs/{{version}}/migrations#available-index)를 가지고 있는 컬럼에 대한 쿼리에 full text "where" 절을 추가할 수 있습니다. 이 메소드는 라라벨이 자동으로 기본 데이터베이스 시스템에 적합한 SQL로 변환합니다. 예를 들어 애플리케이션에서 MySQL을 사용한다면 `MATCH AGAINST` 쿼리가 생성됩니다.

    $users = DB::table('users')
               ->whereFullText('bio', 'web developer')
               ->get();

<a name="laravel-scout-database-engine"></a>
### 라라벨 Scout 데이터베이스 엔진

_라라벨의 Scout 데이터베이스 엔진은 [Taylor Otwell](https://github.com/taylorotwell) 와 [Dries Vints](https://github.com/driesvints)가 기여하였습니다_.

애플리케이션이 크지 않은 데이터베이스에서 동작하고, 부하가 가벼운 경우에는 검색을 위해서 Algolia 또는 MeiliSearch와 같은 검색 서비스를 사용하는 대신에 Scout 의 "데이터베이스" 엔진을 사용하는 것이 편리한 방법입니다. 데이터베이스 엔진은 검색 결과를 얻기 위해서 "where like"을 통해서 Full Text 인덱싱을 사용합니다.

Scout 데이터베이스 엔진에 대한 보다 자세한 내용은 [Scout 매뉴얼](/docs/{{version}}/scout)을 참고하십시오

<a name="rendering-inline-blade-templates"></a>
### 인라인 블레이드 템플릿 렌더링

_인라인 블레이드 템플릿은 [Jason Beggs](https://github.com/jasonlbeggs)가 기여하였습니다. 인라인 블레이드 컴포넌트의 렌더링은 [Toby Zerner](https://github.com/tobyzerner)가 기여하였습니다_.

때로는 블레이드 템플릿 문자열 그 자체를 HTML로 변환해야할 때도 있습니다. 이런 경우 `Blade` 파사드의 `render` 메소드를 사용하면 됩니다. `render` 메소드는 블레이드 템플릿 문법을 포함한 문자열을 인자로 전달 받습니다. 렌더링에 필요한 데이터는 배열로 전달할 수 있습니다.

```php
use Illuminate\Support\Facades\Blade;

return Blade::render('Hello, {{ $name }}', ['name' => 'Julian Bashir']);
```

유사하게, `renderComponent` 메소드는 컴포넌트 인스턴스를 메소드에 전달해서 전달된 클래스 컴포넌트를 렌더링하는데 사용할 수 있습니다.

```php
use App\View\Components\HelloComponent;

return Blade::renderComponent(new HelloComponent('Julian Bashir'));
```

<a name="slot-name-shortcut"></a>
### 슬롯 이름 숏컷-Shortcut

_슬롯 이름 숏컷-Shortcut은 [Caleb Porzio](https://github.com/calebporzio)가 기여하였습니다._

라라벨 이전 릴리즈에서는 슬롯 이름은 `x-slot` 태그의 `name` 속성을 사용해서 지정해야했습니다.

```blade
<x-alert>
    <x-slot name="title">
        Server Error
    </x-slot>

    <strong>Whoops!</strong> Something went wrong!
</x-alert>
```

라라벨 9.x 에서는 다음과 같이 더 짧은 문법으로 표현이 가능합니다.

```xml
<x-slot:title>
  Server Error
</x-slot>
```

<a name="checked-selected-blade-directives"></a>
### 체크박스 / 셀렉트박스(드랍다운 박스) 블레이드 지시어

_체크박스 및 셀렉트박스(드랍다운 박스) 블레이드 지시어는 [Ash Allen](https://github.com/ash-jc-allen) 와 [Taylor Otwell](https://github.com/taylorotwell)가 기여하였습니다_.

편의를 위해서 `@checked` 지시어를 사용해서 HTML 체크박스의 입력이 "체크" 되었는지 보다 쉽게 표시할 수 있습니다. 이 지시어는 전달된 조건식이 `true` 인 경우에 `checked` 를 표시합니다.

```blade
<input type="checkbox"
        name="active"
        value="active"
        @checked(old('active', $user->active)) />
```

마찬가지로 `@selected` 지시어는 전달된 조건식에 따라 "selected"를 표시합니다.

```blade
<select name="version">
    @foreach ($product->versions as $version)
        <option value="{{ $version }}" @selected(old('version') == $version)>
            {{ $version }}
        </option>
    @endforeach
</select>
```

<a name="bootstrap-5-pagination-views"></a>
### 부트스트랩 5 페이지네이션 뷰

_부트스트렙 5 페이지네이션 뷰 지원은 [Jared Lewis](https://github.com/jrd-lewis)가 기여하였습니다_.

라라벨은 이제 [부트스트랩 5](https://getbootstrap.com/)를 사용하는 페이지네이션 뷰를 지원합니다. 기본으로 제공되는 Tailwind 뷰대신에 부트스트랩 페이지네이션 뷰를 사용하려면 `App\Providers\AppServiceProvider` 클래스의 `boot` 메소드 안에서 `useBootstrapFive` 메소드를 호출하면 됩니다.  

    use Illuminate\Pagination\Paginator;

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        Paginator::useBootstrapFive();
    }

<a name="improved-validation-of-nested-array-data"></a>
### 중첩된 배열 데이터에 대한 유효성 검사 기능 향상

_향상된 중첩된 배열 입력 유효성 검사 기능은 [Steve Bauman](https://github.com/stevebauman)가 기여하였습니다_.

때로는 중첩된 배열의 값에 대한 유효성 검사가 필요한 경우가 있습니다. 이제 `Rule:forEach` 메소드를 사용하면 기존 보다 편리하게 이를 처리할 수 있습니다. `forEach` 메소드는 클로저를 인자로 전달 받습니다. 이 클로저는 유효성 검사 과정에서 배열의 요소에 접근하는 반복문 안에서 호출됩니다. 클로저에는 배열의 요소 키와 값을 인자로 전달받습니다. 클로저는 각각의 요소에 해당하는 규칙으로 이루어진 배열을 반환해야합니다. 

    use App\Rules\HasPermission;
    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rule;

    $validator = Validator::make($request->all(), [
        'companies.*.id' => Rule::forEach(function ($value, $attribute) {
            return [
                Rule::exists(Company::class, 'id'),
                new HasPermission('manage-company', $value),
            ];
        }),
    ]);

<a name="laravel-breeze-api"></a>
### 라라벨 Breeze API & Next.js

_라라벨 Breeze API 스캐폴딩과 Next.js 스타터 키트는 [Taylor Otwell](https://github.com/taylorotwell)와 [Miguel Piedrafita](https://twitter.com/m1guelpf)가 기여하였습니다._.

[라라벨 Breeze](/docs/{{version}}/starter-kits#breeze-and-next) 스타터 키트는 "API" 스캐폴딩 모드와 [Next.js](https://nextjs.org) [frontend implementation](https://github.com/laravel/breeze-next)를 포함하고 있습니다. 이 툴의 스캐폴딩은 벡엔드 역할을 하는 라라벨 애플리케이션과, JavaScript 프론트엔드용 Sanctum 인증 API를 손쉽게 구성하는데 사용가능합니다.

<a name="exception-page"></a>
### Ignition 예외 페이지 기능 향상

_Ignition은 [Spatie](https://spatie.be/)에 의해서 개발되었습니다.._

Spatie에서 만든 오픈 소스 예외 디버깅 툴인 Ignition이 새롭게 디자인되었습니다. 새로워진 Ignition은 라라벨 9.x와 함께 제공되며 다크 테마 지원, 커스터마이징이 가능한 "편집기에서 열기" 기능 등을 포함합니다.

<p align="center">
<img width="100%" src="https://user-images.githubusercontent.com/483853/149235404-f7caba56-ebdf-499e-9883-cac5d5610369.png"/>
</p>

<a name="improved-route-list"></a>
### `route:list` 커맨드라인 명령어 출력 결과 향상

_`route:list` CLI 출력은 [Nuno Maduro](https://github.com/nunomaduro)가 기여하였습니다_.

`route:list` CLI 출력은 라라벨 9.x 에서 기능이 크게 향상되어, 정의된 라우트를 확인하는 화면이 보기 좋게 변경되었습니다. 

<p align="center">
<img src="https://user-images.githubusercontent.com/5457236/148321982-38c8b869-f188-4f42-a3cc-a03451d5216c.png"/>
</p>

<a name="test-coverage-support-on-artisan-test-Command"></a>
### 아티즌 `test` 명령어를 사용한 테스트 커버리지

_아티즌 `test` 명령어를 사용한 테스트 커버리지 기능은 [Nuno Maduro](https://github.com/nunomaduro)가 기여하였습니다_.

아티즌 `test` 명령은 테스트가 애플리케이션에 제공하는 코드 커버리지를 확인하는데 사용할 수 있도록 새로운 `--coverage` 옵션을 지원합니다.

```shell
php artisan test --coverage
```

테스트 커버리지의 결과는 CLI 출력에서 바로 확인할 수 있습니다.

<p align="center">
<img width="100%" src="https://user-images.githubusercontent.com/5457236/150133237-440290c2-3538-4d8e-8eac-4fdd5ec7bd9e.png"/>
</p>

또한, `--min` 옵션을 사용하여 테스트 커버리지가 만족해야하는 최소한의 퍼센트를 지정할 수 있습니다. 테스트가 주어진 값을 넘지 않으면 테스트는 실패한것으로 간주됩니다.  

```shell
php artisan test --coverage --min=80.3
```

<p align="center">
<img width="100%" src="https://user-images.githubusercontent.com/5457236/149989853-a29a7629-2bfa-4bf3-bbf7-cdba339ec157.png"/>
</p>

<a name="soketi-echo-server"></a>
### 오픈소스 Echo 웹 소켓 서버 - Soketi 

_Soketi Echo 서버는 [Alex Renoki](https://github.com/rennokki)에 의해서 개발되었습니다_.

라라벨 9.x 만 해당되는 것은 아니지만, 최근 Node.js 로 작성된 [라라벨 Echo](/docs/{{version}}/broadcasting)와 호환되는 웹소켓 서버인 Soketi의 문서화를 지원했습니다. Soketi는 Pusher 및 Ably 를 대신할 수 있고 자체적으로 웹 소켓 서버 관리할 수 있는 훌륭한 오픈 소스 프로젝트 입니다.

Soketi에 대한 보다 자세한 내용은 [broadcasting 매뉴얼](/docs/{{version}}/broadcasting) 및 [Soketi 문서](https://docs.soketi.app/)를 참조하십시오.

<a name="improved-collections-ide-support"></a>
### 컬렉션 IDE 지원 향상

_컬렉션의 IDE 지원 향상은 [Nuno Maduro](https://github.com/nunomaduro)가 지원하였습니다_.

라라벨 9.x 에는 "제네릭" 스타일의 컬렉션 컴포넌트 타입 정의에 대한 IDE와 정적분석을 위한 지원이 향상되었습니다. [PHPStorm](https://blog.jetbrains.com/phpstorm/2021/12/phpstorm-2021-3-release/#support_for_future_laravel_collections)과 같은 IDE와 [PHPStan](https://phpstan.org) 은 이제 라라벨 컬렉션에 대해서 보다 잘 인식할 수 있게 되었습니다.

<p align="center">
<img width="100%" src="https://user-images.githubusercontent.com/5457236/151783350-ed301660-1e09-44c1-b549-85c6db3f078d.gif"/>
</p>

<a name="new-helpers"></a>
### 새로운 헬퍼함수

Laravel 9.x introduces two new, convenient helper functions that you may use in your own application.

라라벨 8.x 은 두 가지 새로운 헬퍼 함수를 추가하였습니다. 

<a name="new-helpers-str"></a>
#### `str`

`str` 함수는 주어진 문자열을 `Illuminate\Support\Stringable` 인스턴스로 만들어서 반환합니다. 이 함수는 `Str::of` 메소드와 동일한 기능을 수행합니다.

    $string = str('Taylor')->append(' Otwell');

    // 'Taylor Otwell'

`str` 함수에 아무런 인자가 제공되지 않으면 `Illuminate\Support\Str` 인스턴스가 반환됩니다.

    $snake = str()->snake('LaravelFramework');

    // 'laravel_framework'

<a name="new-helpers-to-route"></a>
#### `to_route`

`to_route` 함수는 주어진 이름에 해당하는 라우트로 이동하는 HTTP 리다이렉트 응답을 생성하며, 라우트 및 컨트롤러에서 이름이 지정된 라우트로 리다이렉션 하는 편리한 방법을 제공합니다. 

    return to_route('users.show', ['user' => 1]);

필요한경우, 리다이렉션 응답에 포함될 HTTP 상태코드를 세번째 인자로, 응답 해더에 포함될 값을 네번째 인자로 전달할 수도 있습니다.

    return to_route('users.show', ['user' => 1], 302, ['X-Framework' => 'Laravel']);
