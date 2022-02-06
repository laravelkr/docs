# Blade Templates
# 블레이드 템플릿

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Displaying Data](#displaying-data)
- [데이터 표시](#displaying-data)
    - [HTML Entity Encoding](#html-entity-encoding)
    - [HTML 엔티티 인코딩](#html-entity-encoding)
    - [Blade & JavaScript Frameworks](#blade-and-javascript-frameworks)
    - [블레이드 및 자바스크립트 프레임워크](#blade-and-javascript-frameworks)
- [Blade Directives](#blade-directives)
- [블레이드 지시자](#blade-directives)
    - [If Statements](#if-statements)
    - [조건문](#if-statements)
    - [Switch Statements](#switch-statements)
    - [스위치 구문](#switch-statements)
    - [Loops](#loops)
    - [반복문](#loops)
    - [The Loop Variable](#the-loop-variable)
    - [루프 변수](#the-loop-variable)
    - [Conditional Classes](#conditional-classes)
    - [조건부 클래스](#conditional-classes)
    - [Including Subviews](#including-subviews)
    - [하위 뷰 포함하기](#including-subviews)
    - [The `@once` Directive](#the-once-directive)
    - [`@once` 지시자](#the-once-directive)
    - [Raw PHP](#raw-php)
    - [Raw PHP](#raw-php)
    - [Comments](#comments)
    - [주석](#comments)
- [Components](#components)
- [컴포넌트](#components)
    - [Rendering Components](#rendering-components)
    - [컴포넌트 렌더링](#rendering-components)
    - [Passing Data To Components](#passing-data-to-components)
    - [컴포넌트에 데이터 전달하기](#passing-data-to-components)
    - [Component Attributes](#component-attributes)
    - [컴포넌트 속성](#component-attributes)
    - [Reserved Keywords](#reserved-keywords)
    - [예약어](#reserved-keywords)
    - [Slots](#slots)
    - [슬롯](#slots)
    - [Inline Component Views](#inline-component-views)
    - [인라인 컴포넌트 뷰](#inline-component-views)
    - [Anonymous Components](#anonymous-components)
    - [익명 컴포넌트](#anonymous-components)
    - [Dynamic Components](#dynamic-components)
    - [동적 컴포넌트](#dynamic-components)
    - [Manually Registering Components](#manually-registering-components)
    - [수동으로 컴포넌트 등록](#manually-registering-components)
- [Building Layouts](#building-layouts)
- [Building Layouts](#building-layouts)
    - [Layouts Using Components](#layouts-using-components)
    - [Layouts Using Components](#layouts-using-components)
    - [Layouts Using Template Inheritance](#layouts-using-template-inheritance)
    - [Layouts Using Template Inheritance](#layouts-using-template-inheritance)
- [Forms](#forms)
- [Forms](#forms)
    - [CSRF Field](#csrf-field)
    - [CSRF Field](#csrf-field)
    - [Method Field](#method-field)
    - [Method Field](#method-field)
    - [Validation Errors](#validation-errors)
    - [Validation Errors](#validation-errors)
- [Stacks](#stacks)
- [스택](#stacks)
- [Service Injection](#service-injection)
- [서비스 인젝션-주입](#service-injection)
- [Extending Blade](#extending-blade)
- [블레이드 기능 확장하기](#extending-blade)
    - [Custom Echo Handlers](#custom-echo-handlers)
    - [사용자 정의 출력-echo 핸들러](#custom-echo-handlers)
    - [Custom If Statements](#custom-if-statements)
    - [사용자 정의 조건문](#custom-if-statements)

<a name="introduction"></a>
## Introduction
## 시작하기

Blade is the simple, yet powerful templating engine that is included with Laravel. Unlike some PHP templating engines, Blade does not restrict you from using plain PHP code in your templates. In fact, all Blade templates are compiled into plain PHP code and cached until they are modified, meaning Blade adds essentially zero overhead to your application. Blade template files use the `.blade.php` file extension and are typically stored in the `resources/views` directory.

블레이드는 라라벨에 포함된 단순하지만 강력한 템플릿 엔진입니다. 일부 PHP 템플릿 엔진과 달리 블레이드는 템플릿에서 일반 PHP 코드를 사용하는 것을 제한하지 않습니다. 사실, 모든 Blade 템플릿은 일반 PHP 코드로 컴파일되고 수정될 때까지 캐시됩니다. 이는 블레이드가 기본적으로 애플리케이션에 추가적인 부하를 추가하지 않는다는 것을 의미합니다. 블레이드 템플릿 파일은 `.blade.php` 파일 확장자를 사용하며 일반적으로 `resources/views` 디렉토리에 저장됩니다.

Blade views may be returned from routes or controller using the global `view` helper. Of course, as mentioned in the documentation on [views](/docs/{{version}}/views), data may be passed to the Blade view using the `view` helper's second argument:

블레이드는 전역 `view` 헬퍼를 사용하여 라우트 또는 컨트롤러에서 반환 할 수 있습니다. 물론 [views](docs{{version}}views) 문서에서 언급했듯이 `view` 헬퍼의 두 번째 인수를 사용하여 데이터를 블레이드에 전달할 수 있습니다.

    Route::get('/', function () {
        return view('greeting', ['name' => 'Finn']);
    });

> {tip} Want to take your Blade templates to the next level and build dynamic interfaces with ease? Check out [Laravel Livewire](https://laravel-livewire.com).

> {tip} 블레이드 템플릿을 한 단계 업그레이드하고 쉽게 동적 인터페이스를 구축하고 싶으십니까? [라라벨 라이브와이어](https:laravel-livewire.com)를 확인하세요.

<a name="displaying-data"></a>
## Displaying Data
## 데이터 표시하기

You may display data that is passed to your Blade views by wrapping the variable in curly braces. For example, given the following route:

블레이드 뷰로 전달된 데이터를 표시하기 위해 중괄호로 쌓인 변수를 전달할 수 있습니다. 예로 들어 다음의 라우트를 볼 수 있습니다.

    Route::get('/', function () {
        return view('welcome', ['name' => 'Samantha']);
    });

You may display the contents of the `name` variable like so:

다음과 같이 컨텐츠의 `name` 변수를 표시할 수 있습니다.

    Hello, {{ $name }}.

> {tip} Blade's `{{ }}` echo statements are automatically sent through PHP's `htmlspecialchars` function to prevent XSS attacks.

> {tip} 블레이드의 `{{ }}` 구문은 XSS 공격을 방지하기 위해서 자동으로 PHP의 `htmlspecialchars` 함수를 실행하게 됩니다.

You are not limited to displaying the contents of the variables passed to the view. You may also echo the results of any PHP function. In fact, you can put any PHP code you wish inside of a Blade echo statement:

뷰에는 전달된 변수들의 컨텐츠만 표시할 수 있는 것은 아닙니다. PHP 함수의 모든 결과는 출력될 수 있습니다. 블레이드에서는 출력되는 어떠한 PHP 코드도 넣을 수 있습니다.

    The current UNIX timestamp is {{ time() }}.

<a name="html-entity-encoding"></a>
### HTML Entity Encoding
### HTML 엔티티 인코딩

By default, Blade (and the Laravel `e` helper) will double encode HTML entities. If you would like to disable double encoding, call the `Blade::withoutDoubleEncoding` method from the `boot` method of your `AppServiceProvider`:

기본적으로, 블레이드 (그리고 라라벨의 `e` 헬퍼)는 HTML 요소를 두번 인코딩을 합니다. 이중 인코딩을 비활성화 하고 싶을 때에는, `AppServiceProvider` 의 `boot` 메소드 안에 `Blade::withoutDoubleEncoding` 메소드를 호출하면 됩니다.

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\Blade;
    use Illuminate\Support\ServiceProvider;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            Blade::withoutDoubleEncoding();
        }
    }

<a name="displaying-unescaped-data"></a>
#### Displaying Unescaped Data
#### 이스케이프 처리되지 않은 데이터 표시

By default, Blade `{{ }}` statements are automatically sent through PHP's `htmlspecialchars` function to prevent XSS attacks. If you do not want your data to be escaped, you may use the following syntax:

기본적으로 블레이드의 `{{ }}` 문은 XSS 공격을 방지하기 위해 PHP의 `htmlspecialchars` 함수를 통해 자동으로 처리됩니다. 데이터를 이스케이프하지 않으려면 다음 문법을 사용할 수 있습니다.

    Hello, {!! $name !!}.

> {note} Be very careful when echoing content that is supplied by users of your application. You should typically use the escaped, double curly brace syntax to prevent XSS attacks when displaying user supplied data.

> {note} 애플리케이션의 사용자가 입력한 콘텐츠를 출력할 때는 매우 주의하십시오. 일반적으로는 사용자가 입력한 데이터를 표시할 때 XSS 공격을 방지하려면 이스케이프된 이중 중괄호 구문을 사용해야 합니다.

<a name="blade-and-javascript-frameworks"></a>
### Blade & JavaScript Frameworks
### 블레이드 & 자바스크립트 프레임워크

Since many JavaScript frameworks also use "curly" braces to indicate a given expression should be displayed in the browser, you may use the `@` symbol to inform the Blade rendering engine an expression should remain untouched. For example:

많은 자바스크립트 프레임워크에서 또한 중괄호를 사용하여 특정 표현이 브라우저에서 표시되어야 하는다는 것을 명시하기 때문에 `@` 기호를 써서 이 중괄호 표현을 유지해야 한다는 것을 블레이드 렌더링 엔진에게 알려 줄 수 있습니다. 예를 들어

    <h1>Laravel</h1>

    Hello, @{{ name }}.

In this example, the `@` symbol will be removed by Blade; however, `{{ name }}` expression will remain untouched by the Blade engine, allowing it to be rendered by your JavaScript framework.

이 예제에서 `@` 기호는 블레이드에 의해 지워질 것입니다. 하지만 `{{ name }}` 표현은 블레이드 엔진에 의해 영향을 받지 않을 것이며 자바스크립트 프레임워크에 의해 렌더링될 수 있게 해줍니다.

The `@` symbol may also be used to escape Blade directives:

`@`기호를 사용하여 Blade 지시문을 이스케이프 할 수도 있습니다.

    {{-- Blade template --}}
    @@if()

    <!-- HTML output -->
    @if()

<a name="rendering-json"></a>
#### Rendering JSON
#### JSON 렌더링

Sometimes you may pass an array to your view with the intention of rendering it as JSON in order to initialize a JavaScript variable. For example:

때로는 JavaScript 변수를 초기화하기 위해 배열을 JSON으로 렌더링해서 뷰에 전달할 수 있습니다. 예를 들어

    <script>
        var app = <?php echo json_encode($array); ?>;
    </script>

However, instead of manually calling `json_encode`, you may use the `Illuminate\Support\Js::from` method directive. The `from` method accepts the same arguments as PHP's `json_encode` function; however, it will ensure that the resulting JSON is properly escaped for inclusion within HTML quotes. The `from` method will return a string `JSON.parse` JavaScript statement that will convert the given object or array into a valid JavaScript object:

그러나 `json_encode`를 수동으로 호출하는 대신 `Illuminate\Support\Js::from` 메소드 지시문을 사용할 수 있습니다. `from` 메소드는 PHP의 `json_encode` 함수와 동일한 인수를 입력받습니다. 그러나 결과 JSON이 HTML 따옴표 안에 포함되도록 적절하게 이스케이프 처리되도록 합니다. `from` 메소드는 주어진 객체 또는 배열을 유효한 JavaScript 객체로 변환할 수있는 `JSON.parse` JavaScript 문을 반환합니다.

    <script>
        var app = {{ Illuminate\Support\Js::from($array) }};
    </script>

The latest versions of the Laravel application skeleton include a `Js` facade, which provides convenient access to this functionality within your Blade templates:

최신 버전의 라라벨 애플리케이션 스켈레톤에는 블레이드 템플릿 내에서 이 기능에 대한 편리한 액세스를 제공하는 `Js` 파사드가 포함되어 있습니다.

    <script>
        var app = {{ Js::from($array) }};
    </script>

> {note} You should only use the `Js::from` method to render existing variables as JSON. The Blade templating is based on regular expressions and attempts to pass a complex expression to the directive may cause unexpected failures.

> {note} 기존 변수를 JSON으로 렌더링하려면 `Js::from` 메서드만 사용해야 합니다. 블레이드 템플릿은 정규 표현식을 기반으로 하며 복잡한 표현식을 지시문에 전달하려고 하면 예기치 않은 오류가 발생할 수 있습니다.

<a name="the-at-verbatim-directive"></a>
#### The `@verbatim` Directive
#### `@verbatim` 지시어

If you are displaying JavaScript variables in a large portion of your template, you may wrap the HTML in the `@verbatim` directive so that you do not have to prefix each Blade echo statement with an `@` symbol:

여러분이 템플릿의 많은 부분에서 자바스크립트 변수를 표시하는 경우라면, HTML을 `@verbatim` 지시어로 둘러쌓여 있게 할 수 있습니다. 이렇게 하면 여러분은 각각의 블레이드 출력 구문에 `@` 심볼을 매번 붙이지 않아도 됩니다.

    @verbatim
        <div class="container">
            Hello, {{ name }}.
        </div>
    @endverbatim

<a name="blade-directives"></a>
## Blade Directives
## 블레이드 지시어

In addition to template inheritance and displaying data, Blade also provides convenient shortcuts for common PHP control structures, such as conditional statements and loops. These shortcuts provide a very clean, terse way of working with PHP control structures while also remaining familiar to their PHP counterparts.

템플릿의 상속 및 데이터 표시 외에도 블레이드는 조건문 및 루프와 같은 일반적인 PHP 제어 구조에 대한 편리한 단축키를 제공합니다. 이러한 단축키는 PHP 제어 구조로 작업하는 매우 깔끔하고 간결한 방법을 제공하는 동시에 PHP 구조와도 유사합니다.

<a name="if-statements"></a>
### If Statements
### 조건문

You may construct `if` statements using the `@if`, `@elseif`, `@else`, and `@endif` directives. These directives function identically to their PHP counterparts:

`@if`, `@elseif`, `@else`, 그리고 `@endif` 지시어을 이용하여 `if`문을 만들 수 있습니다. 이 지시어들은 대응하는 PHP 문법들과 동일하게 동작합니다.

    @if (count($records) === 1)
        I have one record!
    @elseif (count($records) > 1)
        I have multiple records!
    @else
        I don't have any records!
    @endif

For convenience, Blade also provides an `@unless` directive:

보다 편리하게 블레이드는 `@unless` 지시어을 제공합니다.

    @unless (Auth::check())
        You are not signed in.
    @endunless

In addition to the conditional directives already discussed, the `@isset` and `@empty` directives may be used as convenient shortcuts for their respective PHP functions:

추가적으로 `@isset` 와 `@empty` 지시어는 각각 대응되는 PHP 함수를 편리하게 사용할 수 있는 방법입니다.

    @isset($records)
        // $records is defined and is not null...
    @endisset

    @empty($records)
        // $records is "empty"...
    @endempty

<a name="authentication-directives"></a>
#### Authentication Directives
#### 인증 관련 지시어

The `@auth` and `@guest` directives may be used to quickly determine if the current user is [authenticated](/docs/{{version}}/authentication) or is a guest:

`@auth` 그리고 `@guest` 지시어는 현재 접속자가 [인증된](/docs/{{version}}/authentication) 사용자인지 아니면 guest 인지 판별하는데 사용가능한 편의 기능입니다.

    @auth
        // The user is authenticated...
    @endauth

    @guest
        // The user is not authenticated...
    @endguest

If needed, you may specify the authentication guard that should be checked when using the `@auth` and `@guest` directives:

필요한 경우 `@auth` 및 `@guest` 지시문을 사용할 때 확인해야 하는 인증 가드를 지정할 수 있습니다.

    @auth('admin')
        // The user is authenticated...
    @endauth

    @guest('admin')
        // The user is not authenticated...
    @endguest

<a name="environment-directives"></a>
#### Environment Directives
#### 환경 지시어

You may check if the application is running in the production environment using the `@production` directive:

`@production` 지시어을 사용하여 애플리케이션이 프로덕션 환경에서 실행 중인지 확인할 수 있습니다.

    @production
        // Production specific content...
    @endproduction

Or, you may determine if the application is running in a specific environment using the `@env` directive:

또는 `@env` 지시문을 사용하여 애플리케이션이 특정 환경에서 실행 중인지 확인할 수 있습니다.

    @env('staging')
        // The application is running in "staging"...
    @endenv

    @env(['staging', 'production'])
        // The application is running in "staging" or "production"...
    @endenv

<a name="section-directives"></a>
#### Section Directives
#### 섹션 지시어

You may determine if a template inheritance section has content using the `@hasSection` directive:

`@hasSection` 지시어를 사용하여 템플릿 상속 섹션이 내용이 있는지 확인할 수 있습니다.

```html
@hasSection('navigation')
    <div class="pull-right">
        @yield('navigation')
    </div>

    <div class="clearfix"></div>
@endif
```

You may use the `sectionMissing` directive to determine if a section does not have content:

섹션에 콘텐츠가 없는지 확인하려면 `sectionMissing` 지시문을 사용할 수 있습니다.

```html
@sectionMissing('navigation')
    <div class="pull-right">
        @include('default-navigation')
    </div>
@endif
```

<a name="switch-statements"></a>
### Switch Statements
### 스위치 구문

Switch statements can be constructed using the `@switch`, `@case`, `@break`, `@default` and `@endswitch` directives:

스위치 구문은 `@switch`, `@case`, `@break`, `@default` 그리고 `@endswitch` 지시어로 구성됩니다.

    @switch($i)
        @case(1)
            First case...
            @break

        @case(2)
            Second case...
            @break

        @default
            Default case...
    @endswitch

<a name="loops"></a>
### Loops
### 반복문

In addition to conditional statements, Blade provides simple directives for working with PHP's loop structures. Again, each of these directives functions identically to their PHP counterparts:

조건문 외에도 블레이드는 PHP의 반복 구조를 위한 단순한 지시어들을 제공합니다. 이 지시어들 또한 대응하는 PHP 문장들과 동일하게 동작합니다.

    @for ($i = 0; $i < 10; $i++)
        The current value is {{ $i }}
    @endfor

    @foreach ($users as $user)
        <p>This is user {{ $user->id }}</p>
    @endforeach

    @forelse ($users as $user)
        <li>{{ $user->name }}</li>
    @empty
        <p>No users</p>
    @endforelse

    @while (true)
        <p>I'm looping forever.</p>
    @endwhile

> {tip} When looping, you may use the [loop variable](#the-loop-variable) to gain valuable information about the loop, such as whether you are in the first or last iteration through the loop.

> {tip} 반복문에서 [루프 변수](#the-loop-variable)를 사용하여 반복의 처음과 마지막에 대한 정보를 얻을 수 있습니다.

When using loops you may also end the loop or skip the current iteration using the `@continue` and `@break` directives:

루프를 사용할 때 `@continue` 및 `@break` 지시문을 사용하여 루프를 종료하거나 현재 반복을 건너뛸 수도 있습니다.

    @foreach ($users as $user)
        @if ($user->type == 1)
            @continue
        @endif

        <li>{{ $user->name }}</li>

        @if ($user->number == 5)
            @break
        @endif
    @endforeach

You may also include the continuation or break condition within the directive declaration:

지시문 내에 continue 또는 break 조건을 포함할 수도 있습니다.

    @foreach ($users as $user)
        @continue($user->type == 1)

        <li>{{ $user->name }}</li>

        @break($user->number == 5)
    @endforeach

<a name="the-loop-variable"></a>
### The Loop Variable
### 루프 변수

When looping, a `$loop` variable will be available inside of your loop. This variable provides access to some useful bits of information such as the current loop index and whether this is the first or last iteration through the loop:

반복문을 사용할 때, 반복문 안에서 `$loop` 변수를 사용할 수 있습니다. 이 변수는 현재의 반복문의 인덱스와 반복문이 첫 번째 또는 마지막 인지 알 수 있는 것과 같은 유용한 정보에 엑세스할 수 있습니다.

    @foreach ($users as $user)
        @if ($loop->first)
            This is the first iteration.
        @endif

        @if ($loop->last)
            This is the last iteration.
        @endif

        <p>This is user {{ $user->id }}</p>
    @endforeach

If you are in a nested loop, you may access the parent loop's `$loop` variable via the `parent` property:

반복문이 중첩된 경우라면, 상위 반복문의 `$loop` 변수에 `parent` 속성을 통해서 액세스 할 수 있습니다.

    @foreach ($users as $user)
        @foreach ($user->posts as $post)
            @if ($loop->parent->first)
                This is the first iteration of the parent loop.
            @endif
        @endforeach
    @endforeach

The `$loop` variable also contains a variety of other useful properties:

`$loop` 변수는 그 밖에도 여러가지 유용한 속성을 가지고 있습니다.

Property  | Description
------------- | -------------
`$loop->index`  |  The index of the current loop iteration (starts at 0).
`$loop->iteration`  |  The current loop iteration (starts at 1).
`$loop->remaining`  |  The iterations remaining in the loop.
`$loop->count`  |  The total number of items in the array being iterated.
`$loop->first`  |  Whether this is the first iteration through the loop.
`$loop->last`  |  Whether this is the last iteration through the loop.
`$loop->even`  |  Whether this is an even iteration through the loop.
`$loop->odd`  |  Whether this is an odd iteration through the loop.
`$loop->depth`  |  The nesting level of the current loop.
`$loop->parent`  |  When in a nested loop, the parent's loop variable.

속성 | 설명
------------- | -------------
`$loop->index`  |  현재 반복문의 인덱스(0 부터 시작).
`$loop->iteration`  |  현재 반복문의 횟수(1 부터 시작).
`$loop->remaining`  |  반복문의 남은 횟수.
`$loop->count`  |  반복되는 배열의 총 아이템 수.
`$loop->first`  |  현재 반복문의 첫번째 인지 확인.
`$loop->last`  |  현재 반복문의 마지막 인지 확인.
`$loop->even`  |  현재 반복문이 짝수번째 인지 확인.
`$loop->odd`  |  현재 반복문이 홀수번째 인지 확인.
`$loop->depth`  |  중첩된 반복문의 깊이.
`$loop->parent`  |  반복문이 중첩된 경우 부모의 루프 변수.

<a name="conditional-classes"></a>
### Conditional Classes
### 조건부 클래스

The `@class` directive conditionally compiles a CSS class string. The directive accepts an array of classes where the array key contains the class or classes you wish to add, while the value is a boolean expression. If the array element has a numeric key, it will always be included in the rendered class list:

`@class` 지시문은 CSS 클래스 문자열을 조건부로 컴파일합니다. 지시문은 추가하려는 클래스의 이름을 키로,  boolean 표현식을 값으로 이루어진 배열을 입력입니다. 만약 배열 요소에 숫자로 된 키가 있으면 항상 렌더링 된 클래스의 목록에 포함됩니다.

    @php
        $isActive = false;
        $hasError = true;
    @endphp

    <span @class([
        'p-4',
        'font-bold' => $isActive,
        'text-gray-500' => ! $isActive,
        'bg-red' => $hasError,
    ])></span>

    <span class="p-4 text-gray-500 bg-red"></span>

<a name="including-subviews"></a>
### Including Subviews
### 하위 뷰 포함하기

> {tip} While you're free to use the `@include` directive, Blade [components](#components) provide similar functionality and offer several benefits over the `@include` directive such as data and attribute binding.

> {tip} `@include` 지시문을 자유롭게 사용할 수도 있지만, `@include` 지시문에 비해 Blade [components](#components)는 유사한 기능을 제공하고 데이터 및 속성 바인딩과 같은 여러 이점을 제공합니다.

Blade's `@include` directive allows you to include a Blade view from within another view. All variables that are available to the parent view will be made available to the included view:

블레이드의 `@include` 지시문을 사용하면 다른 뷰 내에서 블레이드 뷰를 포함할 수 있습니다. 상위 뷰에서 사용할 수 있는 모든 변수는 포함된 뷰에서 사용할 수 있습니다.

```html
<div>
    @include('shared.errors')

    <form>
        <!-- Form Contents -->
    </form>
</div>
```

Even though the included view will inherit all data available in the parent view, you may also pass an array of additional data that should be made available to the included view:

포함된 뷰가 상위 뷰에서 사용 가능한 모든 데이터를 상속하더라도 포함된 뷰에서 사용할 데이터 배열을 추가로 전달할 수도 있습니다.

    @include('view.name', ['status' => 'complete'])

If you attempt to `@include` a view which does not exist, Laravel will throw an error. If you would like to include a view that may or may not be present, you should use the `@includeIf` directive:

존재하지 않는 뷰를 `@include`하려고 하면 라라벨에서 오류가 발생합니다. 존재하거나 존재하지 않을 수 있는 뷰를 포함하려면 `@includeIf` 지시문을 사용해야 합니다.

    @includeIf('view.name', ['status' => 'complete'])

If you would like to `@include` a view if a given boolean expression evaluates to `true` or `false`, you may use the `@includeWhen` and `@includeUnless` directives:

주어진 boolean 표현식이 `true` 또는 `false`로 평가되는 경우 뷰를 `@include`하려면 `@includeWhen` 및 `@includeUnless` 지시문을 사용할 수 있습니다.

    @includeWhen($boolean, 'view.name', ['status' => 'complete'])

    @includeUnless($boolean, 'view.name', ['status' => 'complete'])

To include the first view that exists from a given array of views, you may use the `includeFirst` directive:

주어진 뷰 배열에서 존재하는 첫 번째 뷰를 포함하려면 `includeFirst` 지시문을 사용할 수 있습니다.

    @includeFirst(['custom.admin', 'admin'], ['status' => 'complete'])

> {note} You should avoid using the `__DIR__` and `__FILE__` constants in your Blade views, since they will refer to the location of the cached, compiled view.

> {note} 블레이드 뷰에서 `__DIR__` 및 `__FILE__` 상수는 캐시되고 컴파일된 뷰의 위치를 참조하므로 사용을 피해야 합니다.

<a name="rendering-views-for-collections"></a>
#### Rendering Views For Collections
#### 컬렉션에 대한 뷰 렌더링

You may combine loops and includes into one line with Blade's `@each` directive:

블레이드의 `@each` 지시문을 사용하여 루프와 포함을 한 줄로 결합할 수 있습니다.

    @each('view.name', $jobs, 'job')

The `@each` directive's first argument is the view to render for each element in the array or collection. The second argument is the array or collection you wish to iterate over, while the third argument is the variable name that will be assigned to the current iteration within the view. So, for example, if you are iterating over an array of `jobs`, typically you will want to access each job as a `job` variable within the view. The array key for the current iteration will be available as the `key` variable within the view.

`@each` 지시문의 첫 번째 인수는 배열 또는 컬렉션의 각 요소에 대해 렌더링할 뷰입니다. 두 번째 인수는 반복하려는 배열 또는 컬렉션이고, 세 번째 인수는 뷰 내에서 현재 반복에 할당될 변수 이름입니다. 따라서 예를 들어 `jobs` 배열을 반복하는 경우 일반적으로 뷰 내에서 각 작업에 `job` 변수로 액세스하려고 할 것입니다. 현재 반복의 배열 키는 뷰 내에서 `key` 변수로 사용할 수 있습니다.

You may also pass a fourth argument to the `@each` directive. This argument determines the view that will be rendered if the given array is empty.

네 번째 인수를 `@each` 지시문에 전달할 수도 있습니다. 이 인수는 지정된 배열이 비어 있는 경우 렌더링될 뷰를 결정합니다.

    @each('view.name', $jobs, 'job', 'view.empty')

> {note} Views rendered via `@each` do not inherit the variables from the parent view. If the child view requires these variables, you should use the `@foreach` and `@include` directives instead.

> {note} `@each`를 통해 렌더링된 뷰는 상위 뷰에서 변수를 상속하지 않습니다. 자식 뷰에 이러한 변수가 필요한 경우 `@foreach` 및 `@include` 지시문을 사용해야 합니다.

<a name="the-once-directive"></a>
### The `@once` Directive
### `@once` 지시문

The `@once` directive allows you to define a portion of the template that will only be evaluated once per rendering cycle. This may be useful for pushing a given piece of JavaScript into the page's header using [stacks](#stacks). For example, if you are rendering a given [component](#components) within a loop, you may wish to only push the JavaScript to the header the first time the component is rendered:

`@once` 지시문을 사용하면 렌더링 주기당 한 번만 처리하는 템플릿을 정의 할 수 있습니다. 이것은 [stacks](#stacks)를 사용하여 페이지의 헤더에 주어진 자바 스크립트 부분을 푸시하는 데 유용 할 수 있습니다. 예를 들어 루프 내에서 지정된 [component](#components)를 렌더링하는 경우 컴포넌트가 처음 렌더링 될 때만 JavaScript를 헤더에 푸시 할 수 있습니다.

    @once
        @push('scripts')
            <script>
                // Your custom JavaScript...
            </script>
        @endpush
    @endonce

<a name="raw-php"></a>
### Raw PHP
### Raw PHP

In some situations, it's useful to embed PHP code into your views. You can use the Blade `@php` directive to execute a block of plain PHP within your template:

어떤 경우에는 PHP 코드를 뷰에 포함하는 것이 유용합니다. 블레이드의 `@php` 지시문을 사용하여 템플릿 내에서 일반 PHP를 실행할 수 있습니다.

    @php
        $counter = 1;
    @endphp

<a name="comments"></a>
### Comments
### 주석

Blade also allows you to define comments in your views. However, unlike HTML comments, Blade comments are not included in the HTML returned by your application:

블레이드를 사용하면 뷰에서 주석을 정의할 수도 있습니다. 그러나 HTML 주석과 달리 Blade 주석은 애플리케이션에서 반환된 HTML에 포함되지 않습니다.

    {{-- This comment will not be present in the rendered HTML --}}

<a name="components"></a>
## Components
## 컴포넌트

Components and slots provide similar benefits to sections, layouts, and includes; however, some may find the mental model of components and slots easier to understand. There are two approaches to writing components: class based components and anonymous components.

컴포넌트와 슬롯은 섹션 및 레이아웃 및 include 과 유사한 장점을 제공합니다. 그러나 컴포넌트와 슬롯은 결과 모델을 보다 쉽게 이해할 수 있게 해줍니다. 컴포넌트 작성에는 클래스 기반 컴포넌트와 익명 컴포넌트의 두 가지 접근 방식이 있습니다.

To create a class based component, you may use the `make:component` Artisan command. To illustrate how to use components, we will create a simple `Alert` component. The `make:component` command will place the component in the `App\View\Components` directory:

클래스 기반 컴포넌트를 만들려면 `make:component` 아티즌 커맨드를 사용할 수 있습니다. 컴포넌트 사용 방법을 설명하기 위해 간단한 `Alert` 컴포넌트를 만들어보겠습니다. `make:component` 커맨드로 `App\View\Components` 디렉토리에 컴포넌트를 만듭니다.

    php artisan make:component Alert

The `make:component` command will also create a view template for the component. The view will be placed in the `resources/views/components` directory. When writing components for your own application, components are automatically discovered within the `app/View/Components` directory and `resources/views/components` directory, so no further component registration is typically required.

`make:component` 명령은 컴포넌트에 대한 뷰 템플릿도 생성합니다. 뷰는 `resources/views/components` 디렉토리에 생성됩니다. 자신의 애플리케이션을 위한 컴포넌트를 작성할 때 컴포넌트는 `app/View/Components` 디렉토리와 `resources/views/components` 디렉토리 내에서 자동으로 검색되므로, 일반적으로 추가적인 컴포넌트 등록 작업이 필요하지 않습니다.

You may also create components within subdirectories:

하위 디렉토리 내에 컴포넌트를 생성할 수도 있습니다.

    php artisan make:component Forms/Input

The command above will create an `Input` component in the `App\View\Components\Forms` directory and the view will be placed in the `resources/views/components/forms` directory.

위의 명령은 `App\View\Components\Forms` 디렉토리에 `Input` 컴포넌트를 생성하고 뷰는 `resources/views/components/forms` 디렉토리에 생성합니다.

<a name="manually-registering-package-components"></a>
#### Manually Registering Package Components
#### 수동으로 패키지 컴포넌트 등록

When writing components for your own application, components are automatically discovered within the `app/View/Components` directory and `resources/views/components` directory.

자신의 애플리케이션을 위한 컴포넌트를 만들때는 컴포넌트는 `app/View/Components` 디렉토리 및 `resources/views/components` 디렉토리에서 자동으로 감지됩니다.

However, if you are building a package that utilizes Blade components, you will need to manually register your component class and its HTML tag alias. You should typically register your components in the `boot` method of your package's service provider:

그러나 블레이드 컴포넌트를 사용하는 패키지를 만드는 경우 컴포넌트 클래스와 HTML 별칭 태그를 수동으로 등록해야합니다. 일반적으로 패키지 서비스 제공자의 `boot` 메소드에 컴포넌트를 등록해야합니다.

    use Illuminate\Support\Facades\Blade;

    /**
     * Bootstrap your package's services.
     */
    public function boot()
    {
        Blade::component('package-alert', Alert::class);
    }

Once your component has been registered, it may be rendered using its tag alias:

컴포넌트가 등록되면 별칭 태그을 사용하여 렌더링 할 수 있습니다.

    <x-package-alert/>

Alternatively, you may use the `componentNamespace` method to autoload component classes by convention. For example, a `Nightshade` package might have `Calendar` and `ColorPicker` components that reside within the `Package\Views\Components` namespace:

또는 `componentNamespace` 메서드를 사용하여 규칙에 따라 컴포넌트 클래스를 자동으로 로드할 수 있습니다. 예를 들어, `Nightshade` 패키지에는 `Package\Views\Components` 네임스페이스 내에 있는 `Calendar` 및 `ColorPicker` 컴포넌트가 있을 수 있습니다.

    use Illuminate\Support\Facades\Blade;

    /**
     * Bootstrap your package's services.
     *
     * @return void
     */
    public function boot()
    {
        Blade::componentNamespace('Nightshade\\Views\\Components', 'nightshade');
    }

This will allow the usage of package components by their vendor namespace using the `package-name::` syntax:

이렇게하면 `package-name::` 문법을 사용하여 벤더 네임스페이스에 존재하는 패키지 컴포넌트를 사용 할 수 있습니다.

    <x-nightshade::calendar />
    <x-nightshade::color-picker />

Blade will automatically detect the class that's linked to this component by pascal-casing the component name. Subdirectories are also supported using "dot" notation.

블레이드는 컴포넌트 이름을 파스칼 케이스로 변환하여 이 컴포넌트에 연결된 클래스를 자동으로 감지합니다. "." 표기법을 사용하여 하위 디렉터리도 지원됩니다.

<a name="rendering-components"></a>
### Rendering Components
### 컴포넌트 렌더링

To display a component, you may use a Blade component tag within one of your Blade templates. Blade component tags start with the string `x-` followed by the kebab case name of the component class:

컴포넌트를 표시하려면 블레이드 템플릿 중 하나에 블레이드 컴포넌트 태그를 사용할 수 있습니다. 블레이드 컴포넌트 태그는 문자열 `x-`로 시작하고 그 뒤에 컴포넌트 클래스의 케밥 케이스형태의 이름이 옵니다.

    <x-alert/>

    <x-user-profile/>

If the component class is nested deeper within the `App\View\Components` directory, you may use the `.` character to indicate directory nesting. For example, if we assume a component is located at `App\View\Components\Inputs\Button.php`, we may render it like so:

컴포넌트 클래스가 `App\View\Components` 디렉토리에 더 깊게 중첩 된 경우 `.` 문자를 사용하여 디렉토리 중첩을 표시 할 수 있습니다. 예를 들어 컴포넌트가 `App\View\Components\Inputs\Button.php`에 있다고 가정하면 다음과 같이 렌더링 할 수 있습니다.

    <x-inputs.button/>

<a name="passing-data-to-components"></a>
### Passing Data To Components
### 컴포넌트에 데이터 전달하기

You may pass data to Blade components using HTML attributes. Hard-coded, primitive values may be passed to the component using simple HTML attribute strings. PHP expressions and variables should be passed to the component via attributes that use the `:` character as a prefix:

HTML 속성을 사용하여 블레이드 컴포넌트에 데이터를 전달할 수 있습니다. 하드코딩 된 기본 값은 간단한 HTML 속성을 사용하여 컴포넌트에 전달 할 수 있습니다. PHP 표현식과 변수는 `:` 을 접두사로 한 속성을 통해 컴포넌트에 전달되어야 합니다.

    <x-alert type="error" :message="$message"/>

You should define the component's required data in its class constructor. All public properties on a component will automatically be made available to the component's view. It is not necessary to pass the data to the view from the component's `render` method:

클래스 생성자에서 컴포넌트의 필수 데이터를 정의해야합니다. 컴포넌트의 모든 공용 속성은 컴포넌트보기에 자동으로 제공됩니다. 컴포넌트의 `render` 메소드에서 뷰로 데이터를 전달 할 필요는 없습니다.

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
         * The alert message.
         *
         * @var string
         */
        public $message;

        /**
         * Create the component instance.
         *
         * @param  string  $type
         * @param  string  $message
         * @return void
         */
        public function __construct($type, $message)
        {
            $this->type = $type;
            $this->message = $message;
        }

        /**
         * Get the view / contents that represent the component.
         *
         * @return \Illuminate\View\View|\Closure|string
         */
        public function render()
        {
            return view('components.alert');
        }
    }

When your component is rendered, you may display the contents of your component's public variables by echoing the variables by name:

컴포넌트가 렌더링 될 때 이름별로 변수를 출력하여 컴포넌트의 공용 변수 컨텐츠를 표시 할 수 있습니다.

```html
<div class="alert alert-{{ $type }}">
    {{ $message }}
</div>
```

<a name="casing"></a>
#### Casing
#### Casing

Component constructor arguments should be specified using `camelCase`, while `kebab-case` should be used when referencing the argument names in your HTML attributes. For example, given the following component constructor:

컴포넌트 생성자 인수는 `camelCase`를 사용하여 지정해야하며, `camelCase`는 HTML 속성에서 인수 이름을 참조 할 때 사용해야합니다. 예를 들어 다음과 같은 컴포넌트 생성자가 있습니다.

    /**
     * Create the component instance.
     *
     * @param  string  $alertType
     * @return void
     */
    public function __construct($alertType)
    {
        $this->alertType = $alertType;
    }

The `$alertType` argument may be provided to the component like so:

`$alertType` 인수는 다음과 같이 컴포넌트에 전달할 수 있습니다.

    <x-alert alert-type="danger" />

<a name="escaping-attribute-rendering"></a>
#### Escaping Attribute Rendering
#### 속성 렌더링 이스케이프

Since some JavaScript frameworks such as Alpine.js also use colon-prefixed attributes, you may use a double colon (`::`) prefix to inform Blade that the attribute is not a PHP expression. For example, given the following component:

Alpine.js와 같은 일부 JavaScript 프레임워크도 콜론 접두사 속성을 사용하기 때문에 이중 콜론(`::`) 접두사를 사용하여 속성이 PHP 표현식이 아님을 블레이드에 알릴 수 있습니다. 예를 들어 다음 컴포넌트가 있다고 가정합니다.

    <x-button ::class="{ danger: isDeleting }">
        Submit
    </x-button>

The following HTML will be rendered by Blade:

다음 HTML은 블레이드에 의해 렌더링됩니다.

    <button :class="{ danger: isDeleting }">
        Submit
    </button>

<a name="component-methods"></a>
#### Component Methods
#### 컴포넌트 메소드

In addition to public variables being available to your component template, any public methods on the component may be invoked. For example, imagine a component that has an `isSelected` method:

컴포넌트 템플릿에서 사용할 수 있는 공용 변수 외에도 컴포넌트의 모든 공용 메서드를 호출할 수 있습니다. 예를 들어 `isSelected` 메서드를 가진 컴포넌트가 있다고 생각해보겠습니다.

    /**
     * Determine if the given option is the currently selected option.
     *
     * @param  string  $option
     * @return bool
     */
    public function isSelected($option)
    {
        return $option === $this->selected;
    }

You may execute this method from your component template by invoking the variable matching the name of the method:

메소드 이름과 동일한 변수를 호출하면 컴포넌트 템플릿에서 이 메소드를 실행할 수 있습니다.

    <option {{ $isSelected($value) ? 'selected="selected"' : '' }} value="{{ $value }}">
        {{ $label }}
    </option>

<a name="using-attributes-slots-within-component-class"></a>
#### Accessing Attributes & Slots Within Component Classes
#### 컴포넌트 클래스 내의 속성 및 슬롯 액세스

Blade components also allow you to access the component name, attributes, and slot inside the class's render method. However, in order to access this data, you should return a closure from your component's `render` method. The closure will receive a `$data` array as its only argument. This array will contain several elements that provide information about the component:

블레이드 컴포넌트를 사용하면 클래스의 렌더링 메서드 내부에 컴포넌트 이름, 속성 및 슬롯에 액세스할 수도 있습니다. 그러나 이 데이터에 액세스하려면 컴포넌트의 `render` 메서드에서 클로저를 반환해야 합니다. 클로저는 `$data` 배열을 유일한 인수로 받습니다. 이 배열에는 컴포넌트에 대한 정보를 제공하는 여러 요소가 포함됩니다.

    /**
     * Get the view / contents that represent the component.
     *
     * @return \Illuminate\View\View|\Closure|string
     */
    public function render()
    {
        return function (array $data) {
            // $data['componentName'];
            // $data['attributes'];
            // $data['slot'];

            return '<div>Components content</div>';
        };
    }

The `componentName` is equal to the name used in the HTML tag after the `x-` prefix. So `<x-alert />`'s `componentName` will be `alert`. The `attributes` element will contain all of the attributes that were present on the HTML tag. The `slot` element is an `Illuminate\Support\HtmlString` instance with the contents of the component's slot.

`componentName`은 `x-` 접두사 뒤의 HTML 태그에서 사용된 이름과 같습니다. 따라서 `<x-alert >`의 `componentName`은 `alert`가 됩니다. `attributes` 요소에는 HTML 태그에 있던 모든 속성이 포함됩니다. `slot` 요소는 컴포넌트의 슬롯 내용이 있는 `Illuminate\Support\HtmlString` 인스턴스입니다.

The closure should return a string. If the returned string corresponds to an existing view, that view will be rendered; otherwise, the returned string will be evaluated as an inline Blade view.

클로저는 문자열을 반환해야 합니다. 반환된 문자열과 일치하는 뷰가 존재한다면 해당 뷰가 렌더링됩니다. 그렇지 않으면 반환된 문자열이 인라인 블레이드 뷰로 처리됩니다.

<a name="additional-dependencies"></a>
#### Additional Dependencies
#### 추가 의존성

If your component requires dependencies from Laravel's [service container](/docs/{{version}}/container), you may list them before any of the component's data attributes and they will automatically be injected by the container:

컴포넌트에 라라벨의 [service container](/docs/{{version}}/container)의 의존성이 필요한 경우 컴포넌트의 데이터 속성 앞에 나열하면 컨테이너에 의해 자동으로 주입됩니다.

    use App\Services\AlertCreator

    /**
     * Create the component instance.
     *
     * @param  \App\Services\AlertCreator  $creator
     * @param  string  $type
     * @param  string  $message
     * @return void
     */
    public function __construct(AlertCreator $creator, $type, $message)
    {
        $this->creator = $creator;
        $this->type = $type;
        $this->message = $message;
    }

<a name="hiding-attributes-and-methods"></a>
#### Hiding Attributes / Methods
#### 속성 / 메소드 숨기기

If you would like to prevent some public methods or properties from being exposed as variables to your component template, you may add them to an `$except` array property on your component:

일부 공개-public 메서드나 속성이 컴포넌트 템플릿에 변수로 노출되는 것을 방지하려면 컴포넌트의 `$except` 배열 속성에 추가할 수 있습니다.

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
         * The properties / methods that should not be exposed to the component template.
         *
         * @var array
         */
        protected $except = ['type'];
    }

<a name="component-attributes"></a>
### Component Attributes
### 컴포넌트 속성

We've already examined how to pass data attributes to a component; however, sometimes you may need to specify additional HTML attributes, such as `class`, that are not part of the data required for a component to function. Typically, you want to pass these additional attributes down to the root element of the component template. For example, imagine we want to render an `alert` component like so:

데이터 속성을 컴포넌트에 전달하는 방법을 앞에서 확인해보았습니다. 그러나 때로는 컴포넌트가 작동하는 데 필요한 데이터의 일부가 아닌 `class`와 같은 추가적인 HTML 속성을 지정해야 할 수도 있습니다. 일반적으로는 이러한 추가 속성을 컴포넌트 템플릿의 루트 요소로 전달하려고 합니다. 예를 들어 다음과 같이 `alert` 컴포넌트를 렌더링한다고 가정해 보겠습니다.

    <x-alert type="error" :message="$message" class="mt-4"/>

All of the attributes that are not part of the component's constructor will automatically be added to the component's "attribute bag". This attribute bag is automatically made available to the component via the `$attributes` variable. All of the attributes may be rendered within the component by echoing this variable:

컴포넌트 생성자의 일부가 아닌 모든 속성은 컴포넌트의 "attribute bag"에 자동으로 추가됩니다. 이 속성 백은 `$attributes` 변수를 통해 컴포넌트에 자동으로 제공됩니다. 이 변수를 출력하여 컴포넌트 내에서 모든 속성을 렌더링 할 수 있습니다.

    <div {{ $attributes }}>
        <!-- Component content -->
    </div>

> {note} Using directives such as `@env` within component tags is not supported at this time. For example, `<x-alert :live="@env('production')"/>` will not be compiled.

> {note} 현재 컴포넌트 태그 내에서 `@env`와 같은 지시어을 사용하는 것은 지원하지 않습니다. 예를 들어 `<x-alert :live="@env('production')">`는 컴파일되지 않습니다.

<a name="default-merged-attributes"></a>
#### Default / Merged Attributes
#### 기본 / 병합 속성

Sometimes you may need to specify default values for attributes or merge additional values into some of the component's attributes. To accomplish this, you may use the attribute bag's `merge` method. This method is particularly useful for defining a set of default CSS classes that should always be applied to a component:

경우에 따라 속성에 대한 기본값을 지정하거나 컴포넌트의 일부 속성에 추가 값을 병합해야 할 수도 있습니다. 이를 수행하기 위해 속성 모음의 `merge` 메서드를 사용하면 됩니다. 이 메서드는 항상 컴포넌트에 적용해야 하는 CSS 클래스의 기본값 목록을 정의할 때 특히 유용합니다.

    <div {{ $attributes->merge(['class' => 'alert alert-'.$type]) }}>
        {{ $message }}
    </div>

If we assume this component is utilized like so:

이 컴포넌트를 다음과 같이 사용한다고 가정하면

    <x-alert type="error" :message="$message" class="mb-4"/>

The final, rendered HTML of the component will appear like the following:

컴포넌트의 최종 렌더링 HTML은 다음과 같이 나타납니다.

```html
<div class="alert alert-error mb-4">
    <!-- Contents of the $message variable -->
</div>
```

<a name="conditionally-merge-classes"></a>
#### Conditionally Merge Classes
#### 조건부로 클래스 병합

Sometimes you may wish to merge classes if a given condition is `true`. You can accomplish this via the `class` method, which accepts an array of classes where the array key contains the class or classes you wish to add, while the value is a boolean expression. If the array element has a numeric key, it will always be included in the rendered class list:

때때로 주어진 조건이 `true`인 경우 클래스를 병합하고자 할 수 있습니다. `class` 메서드에 추가하려는 클래스의 이름을 key로, 값은 boolean인 배열을 입력하여 이를 처리할 수 있습니다. 배열에 키가 숫자인 요소가 있으면 항상 렌더링된 클래스 목록에 포함됩니다.

    <div {{ $attributes->class(['p-4', 'bg-red' => $hasError]) }}>
        {{ $message }}
    </div>

If you need to merge other attributes onto your component, you can chain the `merge` method onto the `class` method:

다른 속성을 컴포넌트에 병합해야 하는 경우 `merge` 메소드를 `class` 메소드에 연결해서 사용 할 수 있습니다.

    <button {{ $attributes->class(['p-4'])->merge(['type' => 'button']) }}>
        {{ $slot }}
    </button>

> {tip} If you need to conditionally compile classes on other HTML elements that shouldn't receive merged attributes, you can use the [`@class` directive](#conditional-classes).

> {tip} 병합된 속성을 받지 않아야 하는 다른 HTML 요소의 클래스를, 조건에 따라 컴파일해야 하는 경우 [`@class` 지시어](#conditional-classes)을 사용할 수 있습니다.

<a name="non-class-attribute-merging"></a>
#### Non-Class Attribute Merging
#### 클래스가 아닌 속성의 병합

When merging attributes that are not `class` attributes, the values provided to the `merge` method will be considered the "default" values of the attribute. However, unlike the `class` attribute, these attributes will not be merged with injected attribute values. Instead, they will be overwritten. For example, a `button` component's implementation may look like the following:

`class` 속성이 아닌 속성을 병합할 때 `merge` 메소드에 제공된 값은 속성의 "default" 값으로 간주됩니다. 그러나 `class` 속성과 달리 이러한 속성은 삽입된 속성 값과 병합되지 않습니다. 대신 덮어쓰게 됩니다. 예를 들어 `button` 컴포넌트의 구현은 다음과 같을 수 있습니다.

    <button {{ $attributes->merge(['type' => 'button']) }}>
        {{ $slot }}
    </button>

To render the button component with a custom `type`, it may be specified when consuming the component. If no type is specified, the `button` type will be used:

버튼 컴포넌트를 사용자 정의 `type`으로 렌더링하려면, 컴포넌트를 사용할 때 지정할 수 있습니다. 유형을 지정하지 않으면 `button` 유형이 사용됩니다.

    <x-button type="submit">
        Submit
    </x-button>

The rendered HTML of the `button` component in this example would be:

이 예에서 `button` 컴포넌트의 렌더링된 HTML은 다음과 같습니다.

    <button type="submit">
        Submit
    </button>

If you would like an attribute other than `class` to have its default value and injected values joined together, you may use the `prepends` method. In this example, the `data-controller` attribute will always begin with `profile-controller` and any additional injected `data-controller` values will be placed after this default value:

`class` 이외의 속성이 기본값과 주입된 값을 함께 결합하도록 하려면 `prepends` 메소드를 사용할 수 있습니다. 이 예에서 `data-controller` 속성은 항상 `profile-controller`로 시작하고 추가로 주입된 `data-controller` 값은 이 기본값 뒤에 배치됩니다.

    <div {{ $attributes->merge(['data-controller' => $attributes->prepends('profile-controller')]) }}>
        {{ $slot }}
    </div>

<a name="filtering-attributes"></a>
#### Retrieving & Filtering Attributes
#### 속성 검색 및 필터링

You may filter attributes using the `filter` method. This method accepts a closure which should return `true` if you wish to retain the attribute in the attribute bag:

`filter` 메서드를 사용하여 속성을 필터링할 수 있습니다. 이 메서드는 속성 모음에 속성을 유지하려는 경우 `true`를 반환해야 하는 클로저를 입력받습니다.

    {{ $attributes->filter(fn ($value, $key) => $key == 'foo') }}

For convenience, you may use the `whereStartsWith` method to retrieve all attributes whose keys begin with a given string:

편의를 위해 `whereStartsWith` 메소드를 사용하여 키가 주어진 문자열로 시작하는 모든 속성을 검색 할 수 있습니다.

    {{ $attributes->whereStartsWith('wire:model') }}

Conversely, the `whereDoesntStartWith` method may be used to exclude all attributes whose keys begin with a given string:

반대로 `whereDoesntStartWith` 메서드는 키가 주어진 문자열로 시작하는 모든 속성을 제외하는 데 사용할 수 있습니다.

    {{ $attributes->whereDoesntStartWith('wire:model') }}

Using the `first` method, you may render the first attribute in a given attribute bag:

`first` 메소드를 사용하면 주어진 속성 모음에서 첫 번째 속성을 렌더링 할 수 있습니다.

    {{ $attributes->whereStartsWith('wire:model')->first() }}

If you would like to check if an attribute is present on the component, you may use the `has` method. This method accepts the attribute name as its only argument and returns a boolean indicating whether or not the attribute is present:

컴포넌트에 속성이 있는지 확인하려면 `has` 메서드를 사용할 수 있습니다. 이 메서드는 속성 이름을 유일한 인수로 받아들이고 속성이 있는지 여부를 나타내는 부울 값을 반환합니다.

    @if ($attributes->has('class'))
        <div>Class attribute is present</div>
    @endif

You may retrieve a specific attribute's value using the `get` method:

`get` 메소드를 사용하여 특정 속성의 값을 검색할 수 있습니다.

    {{ $attributes->get('class') }}

<a name="reserved-keywords"></a>
### Reserved Keywords
### 예약어

By default, some keywords are reserved for Blade's internal use in order to render components. The following keywords cannot be defined as public properties or method names within your components:

기본적으로 일부 키워드는 블레이드의 내부에서 컴포넌트를 렌더링하기 위해 예약되어 있습니다. 다음 키워드는 컴포넌트 내에서 public 속성 또는 메서드 이름으로 정의할 수 없습니다.

- `data`
- `render`
- `resolveView`
- `shouldRender`
- `view`
- `withAttributes`
- `withName`

<a name="slots"></a>
### Slots
### 슬롯

You will often need to pass additional content to your component via "slots". Component slots are rendered by echoing the `$slot` variable. To explore this concept, let's imagine that an `alert` component has the following markup:

종종 "슬롯"을 통해 컴포넌트에 추가 콘텐츠를 전달해야 합니다. 컴포넌트 슬롯은 `slot` 변수를 반영하여 렌더링됩니다. 이 개념을 살펴보기 위해 `alert` 컴포넌트에 다음 마크업이 있다고 가정해 보겠습니다.

```html
<!-- /resources/views/components/alert.blade.php -->

<div class="alert alert-danger">
    {{ $slot }}
</div>
```

We may pass content to the `slot` by injecting content into the component:

컴포넌트에 컨텐츠를 주입하여 `slot`에 컨텐츠를 전달할 수 있습니다.

```html
<x-alert>
    <strong>Whoops!</strong> Something went wrong!
</x-alert>
```

Sometimes a component may need to render multiple different slots in different locations within the component. Let's modify our alert component to allow for the injection of a "title" slot:

때때로 컴포넌트는 컴포넌트 내의 서로 다른 위치에 있는 여러 개의 서로 다른 슬롯을 렌더링해야 할 수 있습니다. "제목" 슬롯을 삽입할 수 있도록 경고 컴포넌트를 수정해 보겠습니다.

```html
<!-- /resources/views/components/alert.blade.php -->

<span class="alert-title">{{ $title }}</span>

<div class="alert alert-danger">
    {{ $slot }}
</div>
```

You may define the content of the named slot using the `x-slot` tag. Any content not within an explicit `x-slot` tag will be passed to the component in the `$slot` variable:

`x-slot` 태그를 사용하여 명명된 슬롯의 내용을 정의할 수 있습니다. 명시적으로 `x-slot` 태그에 포함되지 않은 콘텐츠는 `$slot` 변수의 컴포넌트로 전달됩니다.

```html
<x-alert>
    <x-slot name="title">
        Server Error
    </x-slot>

    <strong>Whoops!</strong> Something went wrong!
</x-alert>
```

<a name="scoped-slots"></a>
#### Scoped Slots
#### 범위가 지정된 슬롯

If you have used a JavaScript framework such as Vue, you may be familiar with "scoped slots", which allow you to access data or methods from the component within your slot. You may achieve similar behavior in Laravel by defining public methods or properties on your component and accessing the component within your slot via the `$component` variable. In this example, we will assume that the `x-alert` component has a public `formatAlert` method defined on its component class:

Vue와 같은 자바스크립트 프레임워크를 사용해본적이 있다면, 슬롯 내 컴포넌트에서 데이터 또는 메소드에 액세스 할 수있는 "범위가 지정된 슬롯"에 익숙 할 것입니다. 컴포넌트에 public 메소드 또는 속성을 정의하고 `$component` 변수를 통해 슬롯 내 컴포넌트에 액세스하여 라라벨에서 유사한 동작을 수행 할 수 있습니다. 이 예에서는 `x-alert` 컴포넌트에 해당 컴포넌트 클래스에 정의된 public `formatAlert` 메서드가 있다고 가정합니다.

```html
<x-alert>
    <x-slot name="title">
        {{ $component->formatAlert('Server Error') }}
    </x-slot>

    <strong>Whoops!</strong> Something went wrong!
</x-alert>
```

<a name="slot-attributes"></a>
#### Slot Attributes
#### 슬롯 속성

Like Blade components, you may assign additional [attributes](#component-attributes) to slots such as CSS class names:

블레이드 컴포넌트와 마찬가지로 CSS 클래스 이름과 같은 슬롯에 추가 [속성](#component-attributes)을 할당할 수 있습니다.

```html
<x-card class="shadow-sm">
    <x-slot name="heading" class="font-bold">
        Heading
    </x-slot>

    Content

    <x-slot name="footer" class="text-sm">
        Footer
    </x-slot>
</x-card>
```

To interact with slot attributes, you may access the `attributes` property of the slot's variable. For more information on how to interact with attributes, please consult the documentation on [component attributes](#component-attributes):

슬롯 속성과 상호 작용하기 위해 슬롯 변수의 `attributes` 속성에 액세스할 수 있습니다. 속성과 상호 작용하는 방법에 대한 자세한 내용은 [component attributes](#component-attributes)에 대한 문서를 참조하세요.

```php
@props([
    'heading',
    'footer',
])

<div {{ $attributes->class(['border']) }}>
    <h1 {{ $heading->attributes->class(['text-lg']) }}>
        {{ $heading }}
    </h1>

    {{ $slot }}

    <footer {{ $footer->attributes->class(['text-gray-700']) }}>
        {{ $footer }}
    </footer>
</div>
```

<a name="inline-component-views"></a>
### Inline Component Views
### 인라인 컴포넌트 뷰

For very small components, it may feel cumbersome to manage both the component class and the component's view template. For this reason, you may return the component's markup directly from the `render` method:

매우 작은 컴포넌트의 경우 컴포넌트 클래스와 컴포넌트의 뷰 템플릿을 모두 관리하는 것이 번거로울 수 있습니다. 이러한 이유로 컴포넌트의 마크 업을`render` 메소드에서 직접 반환 할 수 있습니다.

    /**
     * Get the view / contents that represent the component.
     *
     * @return \Illuminate\View\View|\Closure|string
     */
    public function render()
    {
        return <<<'blade'
            <div class="alert alert-danger">
                {{ $slot }}
            </div>
        blade;
    }

<a name="generating-inline-view-components"></a>
#### Generating Inline View Components
#### 인라인 뷰 컴포넌트 생성

To create a component that renders an inline view, you may use the `inline` option when executing the `make:component` command:

인라인 뷰를 렌더링하는 컴포넌트를 만들려면 `make:component` 명령을 실행할 때 `inline` 옵션을 사용할 수 있습니다.

    php artisan make:component Alert --inline

<a name="anonymous-components"></a>
### Anonymous Components
### 익명 컴포넌트

Similar to inline components, anonymous components provide a mechanism for managing a component via a single file. However, anonymous components utilize a single view file and have no associated class. To define an anonymous component, you only need to place a Blade template within your `resources/views/components` directory. For example, assuming you have defined a component at `resources/views/components/alert.blade.php`, you may simply render it like so:

인라인 컴포넌트와 마찬가지로 익명 컴포넌트는 단일 파일을 통해 컴포넌트를 관리하는 메커니즘을 제공합니다. 그러나 익명 컴포넌트는 단일 뷰 파일을 사용하며 관련된 클래스가 없습니다. 익명의 컴포넌트를 정의하려면 블레이드 템플릿을 `resources/views/components` 디렉토리에만 배치하면됩니다. 예를 들어, `resources/views/components/alert.blade.php`에 컴포넌트를 정의했다고 가정합니다.

    <x-alert/>

You may use the `.` character to indicate if a component is nested deeper inside the `components` directory. For example, assuming the component is defined at `resources/views/components/inputs/button.blade.php`, you may render it like so:

`.` 문자를 사용하여 컴포넌트가 `components` 디렉토리 안에 더 깊게 중첩되어 있는지 여부를 나타낼 수 있습니다. 예를 들어 컴포넌트가 `resources/views/components/inputs/button.blade.php`에 정의되어 있다고 가정하면 다음과 같이 렌더링 할 수 있습니다.

    <x-inputs.button/>

<a name="anonymous-index-components"></a>
#### Anonymous Index Components
#### 익명 인덱스 컴포넌트

Sometimes, when a component is made up of many Blade templates, you may wish to group the given component's templates within a single directory. For example, imagine an "accordion" component with the following directory structure:

때때로 컴포넌트가 많은 블레이드 템플릿으로 구성되어 있을 때 단일 디렉토리 내에서 주어진 컴포너트의 템플릿을 그룹화하기를 원할 수 있습니다. 예를 들어, 다음 디렉토리 구조를 가진 "아코디언" 컴포넌트를 상상해 보십시오.

```none
/resources/views/components/accordion.blade.php
/resources/views/components/accordion/item.blade.php
```

This directory structure allows you to render the accordion component and its item like so:

이 디렉토리 구조를 사용할 경우, 다음과 같이 아코디언 컴포넌트와 해당 항목을 렌더링할 수 있습니다.

```html
<x-accordion>
    <x-accordion.item>
        ...
    </x-accordion.item>
</x-accordion>
```

However, in order to render the accordion component via `x-accordion`, we were forced to place the "index" accordion component template in the `resources/views/components` directory instead of nesting it within the `accordion` directory with the other accordion related templates.

그러나 `x-accordion`을 통해 아코디언 컴포넌트를 렌더링하기 위해 "index" 아코디언 컴포넌트 템플릿을 다른 아코디언 관련 템플릿과 함께 `accordion` 디렉토리에 작성하지 못하고, 대신 `resources/views/components` 디렉토리에 작성해야 했습니다.

Thankfully, Blade allows you to place an `index.blade.php` file within a component's template directory. When an `index.blade.php` template exists for the component, it will be rendered as the "root" node of the component. So, we can continue to use the same Blade syntax given in the example above; however, we will adjust our directory structure like so:

고맙게도 블레이드를 사용하면 컴포넌트의 템플릿 디렉토리에 `index.blade.php` 파일을 만들어서 처리 할 수 있습니다. 컴포넌트에 대한 `index.blade.php` 템플릿이 있으면 컴포넌트의 "루트" 노드로 렌더링됩니다. 따라서 위의 예제에 제공된 것과 동일한 블레이드 구문을 계속 사용할 수 있습니다. 그러나 다음과 같이 디렉토리 구조를 정리 할 수 있을 것입니다.

```none
/resources/views/components/accordion/index.blade.php
/resources/views/components/accordion/item.blade.php
```

<a name="data-properties-attributes"></a>
#### Data Properties / Attributes
#### 데이터 속성 / 속성

Since anonymous components do not have any associated class, you may wonder how you may differentiate which data should be passed to the component as variables and which attributes should be placed in the component's [attribute bag](#component-attributes).

익명 컴포넌트에는 연결된 클래스가 없으므로 컴포넌트에 변수로 전달해야하는 데이터와 컴포넌트의 [속성 백-bag](#component-attributes)에 어떤 특성을 배치해야하는지 구별 할 수 있습니다.

You may specify which attributes should be considered data variables using the `@props` directive at the top of your component's Blade template. All other attributes on the component will be available via the component's attribute bag. If you wish to give a data variable a default value, you may specify the variable's name as the array key and the default value as the array value:

컴포넌트의 블레이드 템플릿 맨 위에있는 `@props` 지시문을 사용하여 데이터 변수로 간주 할 속성을 지정할 수 있습니다. 컴포넌트의 다른 모든 속성은 컴포넌트의 속성 백을 통해 사용할 수 있습니다. 데이터 변수에 기본값을 지정하려면 변수 이름을 배열 키로 지정하고 기본값을 배열 값으로 지정할 수 있습니다.

    <!-- /resources/views/components/alert.blade.php -->

    @props(['type' => 'info', 'message'])

    <div {{ $attributes->merge(['class' => 'alert alert-'.$type]) }}>
        {{ $message }}
    </div>

Given the component definition above, we may render the component like so:

위의 컴포넌트 정의가 주어지면 다음과 같이 컴포넌트를 렌더링 할 수 있습니다.

    <x-alert type="error" :message="$message" class="mb-4"/>

<a name="accessing-parent-data"></a>
#### Accessing Parent Data
#### 상위 데이터 액세스

Sometimes you may want to access data from a parent component inside a child component. In these cases, you may use the `@aware` directive. For example, imagine we are building a complex menu component consisting of a parent `<x-menu>` and child `<x-menu.item>`:

때로는 자식 컴포넌트 내부에서 부모 컴포넌트의 데이터에 액세스하려고 할 수 있습니다. 이 경우 `@aware` 지시어를 사용할 수 있습니다. 예를 들어 상위 `<x-menu>`와 하위 `<x-menu.item>`으로 구성된 복잡한 메뉴 컴포넌트를 구축한다고 가정해 보겠습니다.

    <x-menu color="purple">
        <x-menu.item>...</x-menu.item>
        <x-menu.item>...</x-menu.item>
    </x-menu>

The `<x-menu>` component may have an implementation like the following:

`<x-menu>` 컴포넌트는 다음과 같이 구현할 수 있습니다.

    <!-- /resources/views/components/menu/index.blade.php -->

    @props(['color' => 'gray'])

    <ul {{ $attributes->merge(['class' => 'bg-'.$color.'-200']) }}>
        {{ $slot }}
    </ul>

Because the `color` prop was only passed into the parent (`<x-menu>`), it won't be available inside `<x-menu.item>`. However, if we use the `@aware` directive, we can make it available inside `<x-menu.item>` as well:

`color` prop은 부모(`<x-menu>`)에만 전달되었기 때문에 `<x-menu.item>` 내에서는 사용할 수 없습니다. 그러나 `@aware` 지시어를 사용하면 `<x-menu.item>` 내부에서도 사용할 수 있습니다.

    <!-- /resources/views/components/menu/item.blade.php -->

    @aware(['color' => 'gray'])

    <li {{ $attributes->merge(['class' => 'text-'.$color.'-800']) }}>
        {{ $slot }}
    </li>

<a name="dynamic-components"></a>
### Dynamic Components
### 동적 컴포넌트

Sometimes you may need to render a component but not know which component should be rendered until runtime. In this situation, you may use Laravel's built-in `dynamic-component` component to render the component based on a runtime value or variable:

때때로 컴포넌트 렌더링이 필요하지만 어떤 컴포넌트가 런타임까지 렌더되는지 알지 못합니다. 이러한 상황에서, 런타임 값이나 변수를 기반으로 컴포넌트를 렌더하기 위해 라라벨 내장의 `dynamic-component` 컴포넌트를 사용할 수 있습니다. 

    <x-dynamic-component :component="$componentName" class="mt-4" />

<a name="manually-registering-components"></a>
### Manually Registering Components
### 수동으로 컴포넌트 등록

> {note} The following documentation on manually registering components is primarily applicable to those who are writing Laravel packages that include view components. If you are not writing a package, this portion of the component documentation may not be relevant to you.

> {note} 아래의 문서는 수동으로 컴포넌트를 등록하는, 뷰 컴포넌트를 포함한 라라벨 패키지를 작성하는 사람들에게 주로 해당됩니다. 패키지를 작성하지 않는 경우 컴포넌트 설명서의 이 부분은 당신과 관련이 없을 수 있습니다.

When writing components for your own application, components are automatically discovered within the `app/View/Components` directory and `resources/views/components` directory.

자신의 애플리케이션을 위한 컴포넌트를 작성할 때 컴포넌트는 `app/View/Components` 디렉토리와 `resources/views/components` 디렉토리에서 자동으로 검색됩니다.

However, if you are building a package that utilizes Blade components or placing components in non-conventional directories, you will need to manually register your component class and its HTML tag alias so that Laravel knows where to find the component. You should typically register your components in the `boot` method of your package's service provider:

그러나 블레이드 컴포넌트를 활용하는 패키지를 빌드하거나, 컴포넌트를 일반적이지 않은 디렉터리에 배치하는 경우, 컴포넌트 클래스와 HTML 태그 별칭을 수동으로 등록해야 라라벨이 컴포넌트를 찾을 수 있는 위치를 알 수 있습니다. 일반적으로 패키지 서비스 제공자의 `boot` 메소드에 컴포넌트를 등록해야 합니다.

    use Illuminate\Support\Facades\Blade;
    use VendorPackage\View\Components\AlertComponent;

    /**
     * Bootstrap your package's services.
     *
     * @return void
     */
    public function boot()
    {
        Blade::component('package-alert', AlertComponent::class);
    }

Once your component has been registered, it may be rendered using its tag alias:

컴포넌트가 등록되면 태그 별칭을 사용하여 렌더링할 수 있습니다.

    <x-package-alert/>

#### Autoloading Package Components
#### 패키지 컴포넌트 자동 로드

Alternatively, you may use the `componentNamespace` method to autoload component classes by convention. For example, a `Nightshade` package might have `Calendar` and `ColorPicker` components that reside within the `Package\Views\Components` namespace:

또는 `componentNamespace` 메서드를 사용하여 규칙에 따라 컴포넌트 클래스를 자동으로 로드할 수 있습니다. 예를 들어, `Nightshade` 패키지에는 `Package\Views\Components` 네임스페이스 내에 있는 `Calendar` 및 `ColorPicker` 컴포넌트가 있을 수 있습니다.

    use Illuminate\Support\Facades\Blade;

    /**
     * Bootstrap your package's services.
     *
     * @return void
     */
    public function boot()
    {
        Blade::componentNamespace('Nightshade\\Views\\Components', 'nightshade');
    }

This will allow the usage of package components by their vendor namespace using the `package-name::` syntax:

이렇게하면 `package-name::` 문법을 사용하여 벤더 네임스페이스에 존재하는 패키지 컴포넌트를 사용 할 수 있습니다.

    <x-nightshade::calendar />
    <x-nightshade::color-picker />

Blade will automatically detect the class that's linked to this component by pascal-casing the component name. Subdirectories are also supported using "dot" notation.

블레이드는 컴포넌트 이름을 파스칼 케이스로 변환하여 이 컴포넌트에 연결된 클래스를 자동으로 감지합니다. "." 표기법을 사용하여 하위 디렉터리도 지원됩니다.

<a name="building-layouts"></a>
## Building Layouts
## 레이아웃 만들기

<a name="layouts-using-components"></a>
### Layouts Using Components
### 컴포넌트를 사용한 레이아웃

Most web applications maintain the same general layout across various pages. It would be incredibly cumbersome and hard to maintain our application if we had to repeat the entire layout HTML in every view we create. Thankfully, it's convenient to define this layout as a single [Blade component](#components) and then use it throughout our application.

대부분의 웹 애플리케이션은 다양한 페이지에서 동일한 일반 레이아웃을 유지합니다. 우리가 만드는 모든 뷰에서 전체 레이아웃 HTML을 반복해야 한다면 애플리케이션을 유지 관리하는 것은 엄청나게 번거롭고 어려울 것입니다. 고맙게도 이 레이아웃을 단일 [Blade component](#components)로 정의한 다음 애플리케이션 전체에서 사용하는 것이 편리합니다.

<a name="defining-the-layout-component"></a>
#### Defining The Layout Component
#### 레이아웃 컴포넌트 정의

For example, imagine we are building a "todo" list application. We might define a `layout` component that looks like the following:

예를 들어, "할 일" 목록 애플리케이션을 구축한다고 상상해 보십시오. 다음과 같은 `layout` 컴포넌트를 정의할 수 있습니다.

```html
<!-- resources/views/components/layout.blade.php -->

<html>
    <head>
        <title>{{ $title ?? 'Todo Manager' }}</title>
    </head>
    <body>
        <h1>Todos</h1>
        <hr/>
        {{ $slot }}
    </body>
</html>
```

<a name="applying-the-layout-component"></a>
#### Applying The Layout Component
#### 레이아웃 컴포넌트 적용

Once the `layout` component has been defined, we may create a Blade view that utilizes the component. In this example, we will define a simple view that displays our task list:

`layout` 컴포넌트가 정의되면 컴포넌트를 활용하는 블레이드 뷰를 만들 수 있습니다. 이 예에서는 작업 목록을 표시하는 간단한 뷰를 정의합니다.

```html
<!-- resources/views/tasks.blade.php -->

<x-layout>
    @foreach ($tasks as $task)
        {{ $task }}
    @endforeach
</x-layout>
```

Remember, content that is injected into a component will be supplied to the default `$slot` variable within our `layout` component. As you may have noticed, our `layout` also respects a `$title` slot if one is provided; otherwise, a default title is shown. We may inject a custom title from our task list view using the standard slot syntax discussed in the [component documentation](#components):

컴포넌트에 주입된 콘텐츠는 `layout` 컴포넌트 내의 기본 `$slot` 변수에 제공됩니다. 눈치채셨겠지만, 우리의 `layout`은 `$title` 슬롯이 있는 경우 이를 우선합니다. 그렇지 않으면 기본 제목이 표시됩니다. [컴포넌트 문서](#components)에 설명된 표준 슬롯 구문을 사용하여 작업 목록 뷰에서 사용자 지정 제목을 삽입할 수 있습니다.

```html
<!-- resources/views/tasks.blade.php -->

<x-layout>
    <x-slot name="title">
        Custom Title
    </x-slot>

    @foreach ($tasks as $task)
        {{ $task }}
    @endforeach
</x-layout>
```

Now that we have defined our layout and task list views, we just need to return the `task` view from a route:

이제 레이아웃 및 작업 목록 뷰를 정의했으므로 경로에서 `task` 뷰를 반환하기만 하면 됩니다.

    use App\Models\Task;

    Route::get('/tasks', function () {
        return view('tasks', ['tasks' => Task::all()]);
    });

<a name="layouts-using-template-inheritance"></a>
### Layouts Using Template Inheritance
### 템플릿 상속을 사용한 레이아웃

<a name="defining-a-layout"></a>
#### Defining A Layout
#### 레이아웃 정의

Layouts may also be created via "template inheritance". This was the primary way of building applications prior to the introduction of [components](#components).

레이아웃은 "템플릿 상속"을 통해 생성할 수도 있습니다. 이것은 [components](#components)가 도입되기 전에 애플리케이션을 구축하기위해 많이 사용하는 방법이었습니다.

To get started, let's take a look at a simple example. First, we will examine a page layout. Since most web applications maintain the same general layout across various pages, it's convenient to define this layout as a single Blade view:

시작하기 위해 간단한 예를 살펴보겠습니다. 먼저 페이지 레이아웃을 살펴보겠습니다. 대부분의 웹 애플리케이션은 다양한 페이지에서 동일한 일반 레이아웃을 유지하므로 이 레이아웃을 단일 블레이드 뷰로 정의하는 것이 편리합니다.

```html
<!-- resources/views/layouts/app.blade.php -->

<html>
    <head>
        <title>App Name - @yield('title')</title>
    </head>
    <body>
        @section('sidebar')
            This is the master sidebar.
        @show

        <div class="container">
            @yield('content')
        </div>
    </body>
</html>
```

As you can see, this file contains typical HTML mark-up. However, take note of the `@section` and `@yield` directives. The `@section` directive, as the name implies, defines a section of content, while the `@yield` directive is used to display the contents of a given section.

보시다시피 이 파일에는 일반적인 HTML 마크업이 포함되어 있습니다. 그러나 `@section` 및 `@yield` 지시문에 유의하세요. `@section` 지시문은 이름에서 알 수 있듯이 콘텐츠의 섹션을 정의하는 반면 `@yield` 지시어는 주어진 섹션의 콘텐츠를 표시하는 데 사용됩니다.

Now that we have defined a layout for our application, let's define a child page that inherits the layout.

이제 애플리케이션에 대한 레이아웃을 정의했으므로 레이아웃을 상속하는 자식 페이지를 정의해 보겠습니다.

<a name="extending-a-layout"></a>
#### Extending A Layout
#### 레이아웃 확장

When defining a child view, use the `@extends` Blade directive to specify which layout the child view should "inherit". Views which extend a Blade layout may inject content into the layout's sections using `@section` directives. Remember, as seen in the example above, the contents of these sections will be displayed in the layout using `@yield`:

자식 뷰를 정의할 때 `@extens` 블레이드 지시문을 사용하여 자식 뷰가 "상속"해야 하는 레이아웃을 지정합니다. 블레이드 레이아웃을 확장하는 뷰는 `@section` 지시문을 사용하여 레이아웃의 섹션에 콘텐츠를 주입할 수 있습니다. 위의 예에서 볼 수 있듯이 이 섹션의 내용은 `@yield`를 사용하여 레이아웃에 표시됩니다.

```html
<!-- resources/views/child.blade.php -->

@extends('layouts.app')

@section('title', 'Page Title')

@section('sidebar')
    @@parent

    <p>This is appended to the master sidebar.</p>
@endsection

@section('content')
    <p>This is my body content.</p>
@endsection
```

In this example, the `sidebar` section is utilizing the `@@parent` directive to append (rather than overwriting) content to the layout's sidebar. The `@@parent` directive will be replaced by the content of the layout when the view is rendered.

이 예제에서 `sidebar` 섹션은 `@@parent` 지시문을 활용하여 레이아웃의 사이드바에 콘텐츠를 (덮어쓰기가 아니라) 추가합니다. `@@parent` 지시문은 뷰가 렌더링될 때 레이아웃의 내용으로 대체됩니다.

> {tip} Contrary to the previous example, this `sidebar` section ends with `@endsection` instead of `@show`. The `@endsection` directive will only define a section while `@show` will define and **immediately yield** the section.

> {tip} 이전 예제와 달리 이 `sidebar` 섹션은 `@show` 대신 `@endsection`으로 끝납니다. `@endsection` 지시문은 섹션만 정의하는 반면 `@show`는 섹션을 정의하고 즉시 생성합니다.

The `@yield` directive also accepts a default value as its second parameter. This value will be rendered if the section being yielded is undefined:

`@yield` 지시문은 두 번째 매개변수로 기본값을 입력받습니다. 생성되는 섹션이 정의되지 않은 경우 이 값이 렌더링됩니다.

    @yield('content', 'Default content')

<a name="forms"></a>
## Forms
## Form

<a name="csrf-field"></a>
### CSRF Field
### CSRF Field

Anytime you define an HTML form in your application, you should include a hidden CSRF token field in the form so that [the CSRF protection](/docs/{{version}}/csrf) middleware can validate the request. You may use the `@csrf` Blade directive to generate the token field:

애플리케이션에서 HTML 양식을 정의할 때마다 [CSRF 보호](/docs/{{version}}/csrf) 미들웨어가 유효한 요청인지 검사할 수 있도록 양식에 숨겨진 CSRF 토큰 필드를 포함해야 합니다. 토큰 필드를 생성하기 위해 `@csrf` 블레이드 지시문을 사용할 수 있습니다:

```html
<form method="POST" action="/profile">
    @csrf

    ...
</form>
```

<a name="method-field"></a>
### Method Field
### 메소드 필드

Since HTML forms can't make `PUT`, `PATCH`, or `DELETE` requests, you will need to add a hidden `_method` field to spoof these HTTP verbs. The `@method` Blade directive can create this field for you:

HTML 양식은 `PUT`, `PATCH` 또는 `DELETE` 요청을 할 수 없으므로 이러한 HTTP 동사를 스푸핑하려면 숨겨진 `_method` 필드를 추가해야 합니다. `@method` 블레이드 지시문은 다음 필드를 생성할 수 있습니다.

```html
<form action="/foo/bar" method="POST">
    @method('PUT')

    ...
</form>
```

<a name="validation-errors"></a>
### Validation Errors
### 검증 오류

The `@error` directive may be used to quickly check if [validation error messages](/docs/{{version}}/validation#quick-displaying-the-validation-errors) exist for a given attribute. Within an `@error` directive, you may echo the `$message` variable to display the error message:

`@error` 지시문은 주어진 속성에 대해 [validation error messages](/docs/{{version}}/validation#quick-displaying-the-validation-errors)가 있는지 빠르게 확인하는 데 사용할 수 있습니다. `@error` 지시문 내에서 `message` 변수를 출력하여 오류 메시지를 표시할 수 있습니다.

```html
<!-- /resources/views/post/create.blade.php -->

<label for="title">Post Title</label>

<input id="title" type="text" class="@error('title') is-invalid @enderror">

@error('title')
    <div class="alert alert-danger">{{ $message }}</div>
@enderror
```

Since the `@error` directive compiles to an "if" statement, you may use the `@else` directive to render content when there is not an error for an attribute:

`@error` 지시문이 "if" 문으로 컴파일되기 때문에 속성에 대한 오류가 없을 때 `@else` 지시문을 사용하여 콘텐츠를 렌더링할 수 있습니다.

```html
<!-- /resources/views/auth.blade.php -->

<label for="email">Email address</label>

<input id="email" type="email" class="@error('email') is-invalid @else is-valid @enderror">
```

You may pass [the name of a specific error bag](/docs/{{version}}/validation#named-error-bags) as the second parameter to the `@error` directive to retrieve validation error messages on pages containing multiple forms:

여러 form이 포함된 페이지에서 유효성 검사 오류 메시지를 검색하려면 [특정 오류 백-bag의 이름](/docs/{{version}}/validation#named-error-bags)을 `@error` 지시문에 두 번째 매개변수로 전달할 수 있습니다.

```html
<!-- /resources/views/auth.blade.php -->

<label for="email">Email address</label>

<input id="email" type="email" class="@error('email', 'login') is-invalid @enderror">

@error('email', 'login')
    <div class="alert alert-danger">{{ $message }}</div>
@enderror
```

<a name="stacks"></a>
## Stacks
## 스택

Blade allows you to push to named stacks which can be rendered somewhere else in another view or layout. This can be particularly useful for specifying any JavaScript libraries required by your child views:

블레이드는 또한 다른 뷰 또는 레이아웃에서 렌더링 할 수 있도록 이름이 지정된 스택에 푸시 할 수 있습니다. 이는 특히 하위 뷰에 필요한 JavaScript 라이브러리를 지정하는 데 유용합니다.

```html
@push('scripts')
    <script src="/example.js"></script>
@endpush
```

You may push to a stack as many times as needed. To render the complete stack contents, pass the name of the stack to the `@stack` directive:

필요한 경우 여러번 스택에 푸쉬할 수 있습니다. 전체 스택 컨텐츠를 렌더링 하려면, 스택 이름을 `@stack` 지시어에 전달하면 됩니다.

```html
<head>
    <!-- Head Contents -->

    @stack('scripts')
</head>
```

If you would like to prepend content onto the beginning of a stack, you should use the `@prepend` directive:

스택이 시작하는 앞부분에 내용을 추가하고자 한다면, `@prepend` 지시어를 사용하면 됩니다.

```html
@push('scripts')
    This will be second...
@endpush

// Later...

@prepend('scripts')
    This will be first...
@endprepend
```

<a name="service-injection"></a>
## Service Injection
## 서비스 인젝션-주입

The `@inject` directive may be used to retrieve a service from the Laravel [service container](/docs/{{version}}/container). The first argument passed to `@inject` is the name of the variable the service will be placed into, while the second argument is the class or interface name of the service you wish to resolve:

`@inject` 지시어는 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)에서 서비스를 반환하는 데 사용할 수 있습니다. `@inject` 지시어에 전달하는 첫번째 인자는 서비스를 할당할 변수의 이름이고, 두번째는 의존성을 해결하려는 서비스 클래스 또는 인터페이스의 이름입니다.

```html
@inject('metrics', 'App\Services\MetricsService')

<div>
    Monthly Revenue: {{ $metrics->monthlyRevenue() }}.
</div>
```

<a name="extending-blade"></a>
## Extending Blade
## 블레이드 기능 확장하기

Blade allows you to define your own custom directives using the `directive` method. When the Blade compiler encounters the custom directive, it will call the provided callback with the expression that the directive contains.

블레이드에서는 `directive` 메소드를 사용하여 사용자가 고유한 지시어을 정의할 수 있습니다. 블레이드 컴파일러가 사용자가 정의한 지시어을 발견하면 지시어에 정의된 콜백 함수를 호출합니다.

The following example creates a `@datetime($var)` directive which formats a given `$var`, which should be an instance of `DateTime`:

다음의 예제는 전달된 `DateTime` 인스턴스인 `$var`의 포맷을 변경하는 `@datetime($var)` 지시어을 생성합니다.

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\Blade;
    use Illuminate\Support\ServiceProvider;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Register any application services.
         *
         * @return void
         */
        public function register()
        {
            //
        }

        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            Blade::directive('datetime', function ($expression) {
                return "<?php echo ($expression)->format('m/d/Y H:i'); ?>";
            });
        }
    }

As you can see, we will chain the `format` method onto whatever expression is passed into the directive. So, in this example, the final PHP generated by this directive will be:

예제에서 볼 수 있듯이 이 지시어에 어떤 것이든 전달된 표현식에서 `format` 메소드를 체이닝합니다. 따라서 이 예제의 지시어의 경우에는 최종적으로 생성되는 PHP 코드는 다음과 같습니다.

    <?php echo ($var)->format('m/d/Y H:i'); ?>

> {note} After updating the logic of a Blade directive, you will need to delete all of the cached Blade views. The cached Blade views may be removed using the `view:clear` Artisan command.

> {note} 블레이드 지시어 로직을 수정한 뒤에는, 블레이드 뷰 캐시를 삭제할 필요가 있습니다. 블레이드 뷰의 캐시는 `view:clear` 아티즌 명령어를 사용하여 제거할 수 있습니다.

<a name="custom-echo-handlers"></a>
### Custom Echo Handlers
### 사용자 정의 출력-echo 핸들러

If you attempt to "echo" an object using Blade, the object's `__toString` method will be invoked. The [`__toString`](https://www.php.net/manual/en/language.oop5.magic.php#object.tostring) method is one of PHP's built-in "magic methods". However, sometimes you may not have control over the `__toString` method of a given class, such as when the class that you are interacting with belongs to a third-party library.

블레이드를 사용하여 객체를 "출력-echo"하려고 하면 객체의 `__toString` 메서드가 호출됩니다. [`__toString`](https:www.php.netmanualenlanguage.oop5.magic.phpobject.tostring) 메서드는 PHP에 내장된 "매직 메서드" 중 하나입니다. 그러나 해당하는 클래스가 타사에서 제공하는 라이브러리같이 원하는 클래스의 `__toString` 메서드를 사용 할 수 없는 경우가 있습니다.

In these cases, Blade allows you to register a custom echo handler for that particular type of object. To accomplish this, you should invoke Blade's `stringable` method. The `stringable` method accepts a closure. This closure should type-hint the type of object that it is responsible for rendering. Typically, the `stringable` method should be invoked within the `boot` method of your application's `AppServiceProvider` class:

이러한 경우 블레이드를 사용하여 특정 유형의 개체에 대한 사용자 정의 출력-echo 핸들러를 등록할 수 있습니다. 이를 수행하려면 블레이드의 `stringable` 메소드를 호출해야 합니다. `stringable` 메소드는 클로저를 입력받습니다. 이 클로저는 렌더링을 담당하는 객체 유형을 유형-type 힌트로 지정해야 합니다. 일반적으로 `stringable` 메소드는 애플리케이션의 `AppServiceProvider` 클래스의 `boot` 메소드 내에서 호출되어야 합니다.

    use Illuminate\Support\Facades\Blade;
    use Money\Money;

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        Blade::stringable(function (Money $money) {
            return $money->formatTo('en_GB');
        });
    }

Once your custom echo handler has been defined, you may simply echo the object in your Blade template:

사용자 정의 출력-echo 핸들러가 정의되면 단순히 블레이드 템플릿에서 객체를 출력할 수 있습니다.

```html
Cost: {{ $money }}
```

<a name="custom-if-statements"></a>
### Custom If Statements
### 사용자 정의 조건문

Programming a custom directive is sometimes more complex than necessary when defining simple, custom conditional statements. For that reason, Blade provides a `Blade::if` method which allows you to quickly define custom conditional directives using closures. For example, let's define a custom conditional that checks the configured default "disk" for the application. We may do this in the `boot` method of our `AppServiceProvider`:

사용자 지정 지시문을 프로그래밍하는 것은 간단한 사용자 지정 조건문을 정의할 때 필요 이상으로 복잡합니다. 이러한 이유로 블레이드는 클로저를 사용하여 사용자 정의 조건부 지시문을 빠르게 정의할 수 있는 `Blade::if` 메소드를 제공합니다. 예를 들어 애플리케이션에 대해 구성된 기본 "디스크"를 확인하는 사용자 지정 조건을 정의해 보겠습니다. 우리는 `AppServiceProvider`의 `boot` 메소드에서 이 작업을 수행할 수 있습니다.

    use Illuminate\Support\Facades\Blade;

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        Blade::if('disk', function ($value) {
            return config('filesystems.default') === $value;
        });
    }

Once the custom conditional has been defined, you can use it within your templates:

사용자 정의 조건문이 정의되면 템플릿 내에서 사용할 수 있습니다.

```html
@disk('local')
    <!-- The application is using the local disk... -->
@elsedisk('s3')
    <!-- The application is using the s3 disk... -->
@else
    <!-- The application is using some other disk... -->
@enddisk

@unlessdisk('local')
    <!-- The application is not using the local disk... -->
@enddisk
```
