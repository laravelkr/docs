# Blade Templates
# 블레이드 템플릿

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Template Inheritance](#template-inheritance)
- [템플릿 상속](#template-inheritance)
    - [Defining A Layout](#defining-a-layout)
    - [레이아웃 정의하기](#defining-a-layout)
    - [Extending A Layout](#extending-a-layout)
    - [레이아웃 확장하기](#extending-a-layout)
- [Displaying Data](#displaying-data)
- [데이터 표시하기](#displaying-data)
    - [Blade & JavaScript Frameworks](#blade-and-javascript-frameworks)
    - [블레이드 & 자바스크립트 프레임워크](#blade-and-javascript-frameworks)
- [Control Structures](#control-structures)
- [제어 구조](#control-structures)
    - [If Statements](#if-statements)
    - [조건문](#if-statements)
    - [Switch Statements](#switch-statements)
    - [스위치 구문](#switch-statements)
    - [Loops](#loops)
    - [반복문](#loops)
    - [The Loop Variable](#the-loop-variable)
    - [루프 변수](#the-loop-variable)
    - [Comments](#comments)
    - [주석](#comments)
    - [PHP](#php)
    - [PHP](#php)
    - [The `@once` Directive](#the-once-directive)
    - [`@once` 지시어](#the-once-directive)
- [Forms](#forms)
- [폼](#forms)
    - [CSRF Field](#csrf-field)
    - [CSRF 필드](#csrf-field)
    - [Method Field](#method-field)
    - [메소드 필드](#method-field)
    - [Validation Errors](#validation-errors)
- [Components](#components)
- [컴포넌트](#components)
    - [Displaying Components](#displaying-components)
    - [컴포넌트 표시](#displaying-components)
    - [Passing Data To Components](#passing-data-to-components)
    - [컴포넌트에 데이터 전달하기](#passing-data-to-components)
    - [Managing Attributes](#managing-attributes)
    - [속성 관리](#managing-attributes)
    - [Slots](#slots)
    - [슬롯](#slots)
    - [Inline Component Views](#inline-component-views)
    - [인라인 컴포넌트 뷰](#inline-component-views)
    - [Anonymous Components](#anonymous-components)
    - [익명 컴포넌트](#anonymous-components)
    - [Dynamic Components](#dynamic-components)
    - [동적 컴포넌트](#dynamic-components)
- [Including Subviews](#including-subviews)
- [하위 뷰파일 포함시키기](#including-sub-views)
    - [Rendering Views For Collections](#rendering-views-for-collections)
    - [컬렉션을 뷰에서 렌더링하기](#rendering-views-for-collections)
- [Stacks](#stacks)
- [스택](#stacks)
- [Service Injection](#service-injection)
- [서비스 인젝션-주입](#service-injection)
- [Extending Blade](#extending-blade)
- [블레이드 기능 확장하기](#extending-blade)
    - [Custom If Statements](#custom-if-statements)
    - [커스텀 If 구문](#custom-if-statements)

<a name="introduction"></a>
## Introduction
## 시작하기

Blade is the simple, yet powerful templating engine provided with Laravel. Unlike other popular PHP templating engines, Blade does not restrict you from using plain PHP code in your views. In fact, all Blade views are compiled into plain PHP code and cached until they are modified, meaning Blade adds essentially zero overhead to your application. Blade view files use the `.blade.php` file extension and are typically stored in the `resources/views` directory.

블레이드는 라라벨에서 제공하는 간단하지만 강력한 템플릿 엔진입니다. 다른 인지도 높은 PHP 템플릿 엔진들과는 달리 블레이드는 뷰에서 순수한 PHP 코드를 작성하는 것을 허용합니다. 실제로는, 모든 블레이드 뷰는 단순한 PHP 코드로 컴파일되고 변경되기 전까지 캐시 됩니다. 이는 블레이드가 애플리케이션에 아무런 부담을 주지 않는다는 것을 의미합니다. 블레이드 뷰 파일은 `.blade.php` 형식의 파일 확장자를 사용하고 주로 `resources/views`에 저장됩니다.

<a name="template-inheritance"></a>
## Template Inheritance
## 템플릿 상속

<a name="defining-a-layout"></a>
### Defining A Layout
### 레이아웃 정의하기

Two of the primary benefits of using Blade are _template inheritance_ and _sections_. To get started, let's take a look at a simple example. First, we will examine a "master" page layout. Since most web applications maintain the same general layout across various pages, it's convenient to define this layout as a single Blade view:

블레이드의 가장 주요한 두가지 장점은 _템플릿 상속_ 과 _섹션_ 입니다. 먼저 간단한 예를 살펴보겠습니다. 우선 "마스터" 페이지 레이아웃을 구성할 것입니다. 대부분의 웹 애플리케이션이 다양한 페이지에서 동일한 레이아웃을 유지하기 때문에 이 레이아웃을 하나의 블레이드 뷰로 정의하는 것이 편리합니다.

    <!-- Stored in resources/views/layouts/app.blade.php -->

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

As you can see, this file contains typical HTML mark-up. However, take note of the `@section` and `@yield` directives. The `@section` directive, as the name implies, defines a section of content, while the `@yield` directive is used to display the contents of a given section.

여기서 볼 수 있듯이, 이 파일은 전형적인 HTML 마크업을 가지고 있습니다. 하지만 `@section` 와 `@yield` 지시어을 확인하십시오. `@section` 는 말 그대로 내용의 섹션을 정의하고 `@yield`는 어떤 섹션의 컨텐츠을 나타내는 데에 사용됩니다.

Now that we have defined a layout for our application, let's define a child page that inherits the layout.

이제 애플리케이션의 레이아웃을 정의하였으니, 이 레이아웃을 상속하는 자식 페이지를 정의하도록 하겠습니다.

<a name="extending-a-layout"></a>
### Extending A Layout
### 레이아웃 확장하기

When defining a child view, use the Blade `@extends` directive to specify which layout the child view should "inherit". Views which extend a Blade layout may inject content into the layout's sections using `@section` directives. Remember, as seen in the example above, the contents of these sections will be displayed in the layout using `@yield`:

하위 뷰를 정의할 때 블레이드 `@extends` 지시어을 사용해 하위 페이지가 어느 레이아웃을 "상속" 받을지 명시할 수 있습니다. 블레이드 레이아웃을 상속 받는 뷰는 `@section` 지시어를 이용해 레이아웃의 섹션에 컨텐츠를 삽입할 수 있습니다. 위의 예제에서 본 것처럼 이 섹션들의 컨텐츠는 `@yield`를 통해 레이아웃에 명시됩니다.

    <!-- Stored in resources/views/child.blade.php -->

    @extends('layouts.app')

    @section('title', 'Page Title')

    @section('sidebar')
        @@parent

        <p>This is appended to the master sidebar.</p>
    @endsection

    @section('content')
        <p>This is my body content.</p>
    @endsection

In this example, the `sidebar` section is utilizing the `@@parent` directive to append (rather than overwriting) content to the layout's sidebar. The `@@parent` directive will be replaced by the content of the layout when the view is rendered.

이 예제에 `sidebar` 섹션은 `@@parent` 지시어를 활용해서 레이아웃 사이드바에 컨텐츠를 겹쳐 쓰지 않고 추가합니다. `@@parent` 지시어은 뷰가 렌더링되면 레이아웃의 컨텐츠에 의해 대체됩니다.

> {tip} Contrary to the previous example, this `sidebar` section ends with `@endsection` instead of `@show`. The `@endsection` directive will only define a section while `@show` will define and **immediately yield** the section.

> {tip} 이전 예제와는 다르게, `sidebar` 섹션은 `@show` 대신에 `@endsection` 으로 끝납니다. `@endsection` 지시어는 섹션 만을 정의하고, `@show`는 정의하는 **즉시 섹션을 생성** 합니다.

The `@yield` directive also accepts a default value as its second parameter. This value will be rendered if the section being yielded is undefined:

또한 `@yield` 지시어는 두 번째 파라미터를 통해 기본값을 입력받습니다. 해당 섹션이 정의되지 않을 경우 이 값이 렌더링됩니다.

    @yield('content', View::make('view.name'))

Blade views may be returned from routes using the global `view` helper:

블레이드 뷰도 글로벌 `view` 헬퍼를 사용하여 라우트에서 반환될 수 있습니다.

    Route::get('blade', function () {
        return view('child');
    });

<a name="displaying-data"></a>
## Displaying Data
## 데이터 표시하기

You may display data passed to your Blade views by wrapping the variable in curly braces. For example, given the following route:

블레이드 뷰로 전달된 데이터를 표시하기 위해 중괄호로 쌓인 변수를 전달할 수 있습니다. 예로 들어 다음의 라우트를 볼 수 있습니다.

    Route::get('greeting', function () {
        return view('welcome', ['name' => 'Samantha']);
    });

You may display the contents of the `name` variable like so:

다음과 같이 컨텐츠의 `name` 변수를 표시할 수 있습니다.

    Hello, {{ $name }}.

> {tip} Blade `{{ }}` statements are automatically sent through PHP's `htmlspecialchars` function to prevent XSS attacks.

> {tip} 블레이드의 `{{ }}` 구문은 XSS 공격을 방지하기 위해서 자동으로 PHP의 `htmlspecialchars` 함수를 실행하게 됩니다.

You are not limited to displaying the contents of the variables passed to the view. You may also echo the results of any PHP function. In fact, you can put any PHP code you wish inside of a Blade echo statement:

뷰에는 전달된 변수들의 컨텐츠만 표시할 수 있는 것은 아닙니다. PHP 함수의 모든 결과는 출력될 수 있습니다. 블레이드에서는 출력되는 어떠한 PHP 코드도 넣을 수 있습니다.

    The current UNIX timestamp is {{ time() }}.

#### Displaying Unescaped Data
#### Escape 처리되지 않은 데이터 표시하기

By default, Blade `{{ }}` statements are automatically sent through PHP's `htmlspecialchars` function to prevent XSS attacks. If you do not want your data to be escaped, you may use the following syntax:

기본적으로 블레이드 `{{ }}` 문장은 XSS 공격을 방지하기 위해 PHP의 `htmlspecialchars` 함수를 통과합니다. 데이터를 escape 처리를 하지 않으려면 다음과 같이 작성하면 됩니다.

    Hello, {!! $name !!}.

> {note} Be very careful when echoing content that is supplied by users of your application. Always use the escaped, double curly brace syntax to prevent XSS attacks when displaying user supplied data.

> {note} 애플리케이션의 사용자들로 부터 입력하여 표시되는 컨텐츠를 출력할 때는 escape-이스케이프에 대한 주의가 필요합니다. 사용자가 제공 한 데이터를 표시 할 때 XSS 공격을 방지하려면 항상 이스케이프 처리 된 이중 중괄호 문법을 사용하십시오.

#### Rendering JSON
#### JSON 렌더링

Sometimes you may pass an array to your view with the intention of rendering it as JSON in order to initialize a JavaScript variable. For example:

때로는 다음의 예와 같이, 자바스크립트 변수를 초기화하는데 사용하기 위해서 뷰에 배열을 전달하여 json으로 렌더링하기를 원할수도 있습니다.

    <script>
        var app = <?php echo json_encode($array); ?>;
    </script>

However, instead of manually calling `json_encode`, you may use the `@json` Blade directive. The `@json` directive accepts the same arguments as PHP's `json_encode` function:

직접 `json_encode` 함수를 호출하는 대신에, `@json` 블레이드 지시어를 사용할 수 있습니다. `@json` 지시어는 PHP의 `json_encode` 함수와 같은 인수를 받아들입니다.

    <script>
        var app = @json($array);

        var app = @json($array, JSON_PRETTY_PRINT);
    </script>

> {note} You should only use the `@json` directive to render existing variables as JSON. The Blade templating is based on regular expressions and attempts to pass a complex expression to the directive may cause unexpected failures.

> {note} 기존 변수를 JSON으로 렌더링하려면 `@json` 지시어 만 사용해야합니다. Blade 템플릿은 정규 표현식을 기반으로하며 지시어에 복잡한 표현식을 전달하려고하면 예상치 못한 오류가 발생할 수 있습니다.

#### HTML Entity Encoding
#### HTML Entity 인코딩

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

<a name="blade-and-javascript-frameworks"></a>
### Blade & JavaScript Frameworks
### 블레이드 & 자바스크립트 프레임워크

Since many JavaScript frameworks also use "curly" braces to indicate a given expression should be displayed in the browser, you may use the `@` symbol to inform the Blade rendering engine an expression should remain untouched. For example:

많은 자바스크립트 프레임워크에서 또한 중괄호를 사용하여 특정 표현이 브라우저에서 표시되어야 하는다는 것을 명시하기 때문에 `@` 기호를 써서 이 중괄호 표현을 유지해야 한다는 것을 블레이드 렌더링 엔진에게 알려 줄 수 있습니다. 예를 들어:

    <h1>Laravel</h1>

    Hello, @{{ name }}.

In this example, the `@` symbol will be removed by Blade; however, `{{ name }}` expression will remain untouched by the Blade engine, allowing it to instead be rendered by your JavaScript framework.

이 예제에서 `@` 기호는 블레이드에 의해 지워질 것입니다. 하지만 `{{ name }}` 표현은 블레이드 엔진에 의해 영향을 받지 않을 것이며 자바스크립트 프레임워크에 의해 렌더링될 수 있게 해줍니다.

The `@` symbol may also be used to escape Blade directives:

`@`기호를 사용하여 Blade 지시문을 이스케이프 할 수도 있습니다.

    {{-- Blade --}}
    @@json()

    <!-- HTML output -->
    @json()

#### The `@verbatim` Directive
#### `@verbatim` 지시어

If you are displaying JavaScript variables in a large portion of your template, you may wrap the HTML in the `@verbatim` directive so that you do not have to prefix each Blade echo statement with an `@` symbol:

여러분이 템플릿의 많은 부분에서 자바스크립트 변수를 표시하는 경우라면, HTML을 `@verbatim` 지시어로 둘러쌓여 있게 할 수 있습니다. 이렇게 하면 여러분은 각각의 블레이드 출력 구문에 `@` 심볼을 매번 붙이지 않아도 됩니다.

    @verbatim
        <div class="container">
            Hello, {{ name }}.
        </div>
    @endverbatim

<a name="control-structures"></a>
## Control Structures
## 컨트롤 구조

In addition to template inheritance and displaying data, Blade also provides convenient shortcuts for common PHP control structures, such as conditional statements and loops. These shortcuts provide a very clean, terse way of working with PHP control structures, while also remaining familiar to their PHP counterparts.

템플릿 상속과 데이터 표시 외에도 블레이드는 공통적인 PHP 컨트롤 구조를 위해서 조건문과 반복문과 같은 편리한 방법들을 제공합니다. 이 방법들은 PHP 컨트롤 구조를 이용할 수 있는 깔끔하고 간단한 방법을 제공하면서 PHP에 익숙한 구조와 비슷하도록 유지합니다.

<a name="if-statements"></a>
### If Statements
### 조건문

You may construct `if` statements using the `@if`, `@elseif`, `@else`, and `@endif` directives. These directives function identically to their PHP counterparts:

`@if`, `@elseif`, `@else`, 그리고 `@endif` 지시어을 이용하여 조건문을 만들 수 있습니다. 이 지시어들은 대응하는 PHP 문장들과 동일하게 동작합니다.

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

#### Authentication Directives
#### 인증 관련 지시어

The `@auth` and `@guest` directives may be used to quickly determine if the current user is authenticated or is a guest:

`@auth` 그리고 `@guest` 지시어는 현재 접속자가 인증된 사용자인지 아니면 guest 인지 판별하는데 사용가능한 편의 기능입니다.

    @auth
        // The user is authenticated...
    @endauth

    @guest
        // The user is not authenticated...
    @endguest

If needed, you may specify the [authentication guard](/docs/{{version}}/authentication) that should be checked when using the `@auth` and `@guest` directives:

필요한 경우, `@auth` 와 `@guest` 지시어에 체크하고자 하는 [인증 guard](/docs/{{version}}/authentication)를 지정할 수 있습니다.

    @auth('admin')
        // The user is authenticated...
    @endauth

    @guest('admin')
        // The user is not authenticated...
    @endguest

#### Section Directives
#### 섹션 지시어

You may check if a section has content using the `@hasSection` directive:

`@hasSection` 지시어를 사용하여 섹션이 내용을 가지고 있는지 확인할 수 있습니다.

    @hasSection('navigation')
        <div class="pull-right">
            @yield('navigation')
        </div>

        <div class="clearfix"></div>
    @endif

You may use the `sectionMissing` directive to determine if a section does not have content:

섹션에 콘텐츠가 없는지 확인하려면 `sectionMissing` 지시문을 사용할 수 있습니다.

    @sectionMissing('navigation')
        <div class="pull-right">
            @include('default-navigation')
        </div>
    @endif

#### Environment Directives
#### 환경 지시문

You may check if the application is running in the production environment using the `@production` directive:

`@production` 지시문을 사용하여 애플리케이션이 프로덕션 환경에서 실행 중인지 확인할 수 있습니다.

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

When using loops you may also end the loop or skip the current iteration:

반복문을 사용할 때에는 반복문의 중료 또는 현재 반복의 중단을 표시할 필요가 있습니다.

    @foreach ($users as $user)
        @if ($user->type == 1)
            @continue
        @endif

        <li>{{ $user->name }}</li>

        @if ($user->number == 5)
            @break
        @endif
    @endforeach

You may also include the condition with the directive declaration in one line:

또한 하나의 라인으로 표현되는 조건식을 포함할 수도 있습니다.

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
                This is first iteration of the parent loop.
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

<a name="comments"></a>
### Comments
### 주석

Blade also allows you to define comments in your views. However, unlike HTML comments, Blade comments are not included in the HTML returned by your application:

블레이드는 또한 뷰에 주석을 정의할 수 있습니다. 하지만 HTML 주석과는 다르게 블레이드 주석은 애플리케이션이 반환하는 HTML에 포함되어 있지 않습니다.

    {{-- This comment will not be present in the rendered HTML --}}

<a name="php"></a>
### PHP
### PHP

In some situations, it's useful to embed PHP code into your views. You can use the Blade `@php` directive to execute a block of plain PHP within your template:

상황에 따라서, 뷰에서 PHP 코드 자체를 삽입하는 것이 유용할 수도 있습니다. 템플릿 안에서 블레이드의 `@php` 지시어를 사용하여 해당 블럭의 PHP를 실행 할 수 있습니다.

    @php
        //
    @endphp

> {tip} While Blade provides this feature, using it frequently may be a signal that you have too much logic embedded within your template.

> {tip} 블레이드가 이 기능을 제공하지만, 이 기능을 너무 빈번하게 사용하는 것은 너무 많은 로직이 템플릿 안에 포함되어 있다는 신호일 수 있습니다.

<a name="the-once-directive"></a>
### The `@once` Directive
### `@once` 지시문

The `@once` directive allows you to define a portion of the template that will only be evaluate once per rendering cycle. This may be useful for pushing a given piece of JavaScript into the page's header using [stacks](#stacks). For example, if you are rendering a given [component](#components) within a loop, you may wish to only push the JavaScript to the header the the first time the component is rendered:

`@once` 지시문을 사용하면 렌더링 주기당 한 번만 처리하는 템플릿을 정의 할 수 있습니다. 이것은 [stacks](#stacks)를 사용하여 페이지의 헤더에 주어진 자바 스크립트 부분을 푸시하는 데 유용 할 수 있습니다. 예를 들어 루프 내에서 지정된 [component](#components)를 렌더링하는 경우 구성 요소가 처음 렌더링 될 때만 JavaScript를 헤더에 푸시 할 수 있습니다.

    @once
        @push('scripts')
            <script>
                // Your custom JavaScript...
            </script>
        @endpush
    @endonce

<a name="forms"></a>
## Forms
## 폼

<a name="csrf-field"></a>
### CSRF Field
### CSRF 필드

Anytime you define an HTML form in your application, you should include a hidden CSRF token field in the form so that [the CSRF protection](https://laravel.com/docs/{{version}}/csrf) middleware can validate the request. You may use the `@csrf` Blade directive to generate the token field:

애플리케이션에서 HTML 폼을 만들 할 때마다 [CSRF 보호](/docs/{{version}}/csrf) 미들웨어가 요청에 대한 유효성 검사를 할 수 있도록 폼에 숨겨진 CSRF 토큰 필드를 포함해야합니다. Blade의 `@csrf` 지시어를 사용하여 토큰 필드를 생성 할 수 있습니다.

    <form method="POST" action="/profile">
        @csrf

        ...
    </form>

<a name="method-field"></a>
### Method Field
### 메소드 필드

Since HTML forms can't make `PUT`, `PATCH`, or `DELETE` requests, you will need to add a hidden `_method` field to spoof these HTTP verbs. The `@method` Blade directive can create this field for you:

HTML 폼은 `PUT`,`PATCH` 또는`DELETE` 요청을 만들 수 없기 때문에 숨겨진 `_method` 필드를 추가하여 HTTP 동작을 속여합니다. Blade의 `@method` 지시어로 이 필드를 생성 할 수 있습니다.

    <form action="/foo/bar" method="POST">
        @method('PUT')

        ...
    </form>

<a name="validation-errors"></a>
### Validation Errors
### 유효성 검증 실패

The `@error` directive may be used to quickly check if [validation error messages](/docs/{{version}}/validation#quick-displaying-the-validation-errors) exist for a given attribute. Within an `@error` directive, you may echo the `$message` variable to display the error message:

`@error` 지시어는 주어진 속성에 대해 [유효성 검증 실패 메세지](/docs/{{version}}/validation#quick-displaying-the-validation-errors) 가 있는지 빠르게 확인하는 데 사용할 수 있습니다. `@error` 지시어 내에서 `$message` 변수를 echo 하여 에러 메시지를 표시 할 수 있습니다.

    <!-- /resources/views/post/create.blade.php -->

    <label for="title">Post Title</label>

    <input id="title" type="text" class="@error('title') is-invalid @enderror">

    @error('title')
        <div class="alert alert-danger">{{ $message }}</div>
    @enderror

You may pass [the name of a specific error bag](/docs/{{version}}/validation#named-error-bags) as the second parameter to the `@error` directive to retrieve validation error messages on pages containing multiple forms:

`@error` 지시문-directive의 두 번째 파라메터로 [특정 에러 백의 이름](/docs/{{version}}/validation#named-error-bags)을 전달하여 여러개의 폼이 포함 된 페이지에서 유효성 검증 오류 메시지를 조회 할 수 있습니다.

    <!-- /resources/views/auth.blade.php -->

    <label for="email">Email address</label>

    <input id="email" type="email" class="@error('email', 'login') is-invalid @enderror">

    @error('email', 'login')
        <div class="alert alert-danger">{{ $message }}</div>
    @enderror

<a name="components"></a>
## Components
## 컴포넌트

Components and slots provide similar benefits to sections and layouts; however, some may find the mental model of components and slots easier to understand. There are two approaches to writing components: class based components and anonymous components.

컴포넌트와 슬롯은 섹션 및 레이아웃과 유사한 장점을 제공합니다. 그러나 컴포넌트와 슬롯은 결과 모델을 보다 쉽게 이해할 수 있게 해줍니다. 컴포넌트 작성에는 클래스 기반 컴포넌트와 익명 컴포넌트의 두 가지 접근 방식이 있습니다.


To create a class based component, you may use the `make:component` Artisan command. To illustrate how to use components, we will create a simple `Alert` component. The `make:component` command will place the component in the `App\View\Components` directory:

클래스 기반 컴포넌트를 만들려면 `make:component` 아티즌 커맨드를 사용할 수 있습니다. 컴포넌트 사용 방법을 설명하기 위해 간단한 `Alert` 컴포넌트를 만들어보겠습니다. `make:component` 커맨드로 `App\View\Components` 디렉토리에 컴포넌트를 만듭니다.

    php artisan make:component Alert

The `make:component` command will also create a view template for the component. The view will be placed in the `resources/views/components` directory.

`make:component` 커맨드는 컴포넌트에 대한 뷰템플릿도 만듭니다. 뷰는 `resources/views/components` 디렉토리에 만들어집니다.

#### Manually Registering Package Components
#### 패키지 컴포넌트 수동 등록

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
        Blade::component('package-alert', AlertComponent::class);
    }

Once your component has been registered, it may be rendered using its tag alias:

컴포넌트가 등록되면 별칭 태그을 사용하여 렌더링 될 수 있습니다.

    <x-package-alert/>

Alternatively, you may use the `componentNamespace` method to autoload component classes by convention. For example, a `Nightshade` package might have `Calendar` and `ColorPicker` components that reside within the `Package\Views\Components` namespace:

대신, 관례상 autoload 컴포넌트 클래스에 `componentNamespace` 메소드를 사용할 수 있습니다. 예를 들어, `Nightshade` 패키지가 `Package\Views\Components` 네임스페이스 안에 있는 `Calendar` 와 `ColorPicker` 컴포넌트를 가지는 경우:

    use Illuminate\Support\Facades\Blade;

    /**
     * Bootstrap your package's services.
     */
    public function boot()
    {
        Blade::componentNamespace('Nightshade\\Views\\Components', 'nightshade');
    }

This will allow the usage of package components by their vendor namespace using the `package-name::` syntax:

`package-name::` 문법을 사용하여 벤더 네임스페이스에 의한 패키지 컴포넌트의 사용을 가능하게 할 것입니다:

    <x-nightshade::calendar />
    <x-nightshade::color-picker />

Blade will automatically detect the class that's linked to this component by pascal-casing the component name. Subdirectories are also supported using "dot" notation.

블레이드는 파스칼 케이스를 적용한 이름으로 이 컴포넌트를 알아서 감지합니다. 또한 하위 디렉토리를 위한 "." 표기법도 지원합니다.

<a name="displaying-components"></a>
### Displaying Components
### 컴포넌트 표시

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

You may pass data to Blade components using HTML attributes. Hard-coded, primitive values may be passed to the component using simple HTML attributes. PHP expressions and variables should be passed to the component via attributes that use the `:` character as a prefix:

HTML 속성을 사용하여 블레이드 컴포넌트에 데이터를 전달할 수 있습니다. 하드코딩 된 기본 값은 간단한 HTML 속성을 사용하여 컴포넌트에 전달 할 수 있습니다. PHP 표현식과 변수는 `:` 을 접두사로 한 속성을 통해 컴포넌트에 전달되어야 합니다.

    <x-alert type="error" :message="$message"/>

You should define the component's required data in its class constructor. All public properties on a component will automatically be made available to the component's view. It is not necessary to pass the data to the view from the component's `render` method:

클래스 생성자에서 컴포넌트의 필수 데이터를 정의해야합니다. 컴포넌트의 모든 공용 속성은 컴포넌트보기에 자동으로 제공됩니다. 컴포넌트의 `render` 메소드에서 뷰로 데이터를 전달 할 필요는 없습니다 :

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

    <div class="alert alert-{{ $type }}">
        {{ $message }}
    </div>

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

The `$alertType` argument may be provided like so:

`$alertType` 인수는 다음과 같이 제공 될 수 있습니다.

    <x-alert alert-type="danger" />

#### Component Methods
#### 컴포넌트 메소드

In addition to public variables being available to your component template, any public methods on the component may also be executed. For example, imagine a component that has a `isSelected` method:

컴포넌트 템플릿에 사용 가능한 공용 변수 외에도 컴포넌트의 모든 공용 메서드가 실행될 수도 있습니다. 예를 들어, `isSelected` 메소드가 있는 컴포넌트를 생각해보십시오.

    /**
     * Determine if the given option is the current selected option.
     *
     * @param  string  $option
     * @return bool
     */
    public function isSelected($option)
    {
        return $option === $this->selected;
    }

You may execute this method from your component template by invoking the variable matching the name of the method:

메소드 이름과 일치하는 변수를 호출하여 컴포넌트 템플릿에서 이 메소드를 실행할 수 있습니다.

    <option {{ $isSelected($value) ? 'selected="selected"' : '' }} value="{{ $value }}">
        {{ $label }}
    </option>

#### Using Attributes & Slots Inside The Class
#### 클래스 내 속성 및 슬롯 사용

Blade components also allow you to access the component name, attributes, and slot inside the class's render method. However, in order to access this data, you should return a Closure from your component's `render` method. The Closure will receive a `$data` array as its only argument:

블레이드 컴포넌트를 사용하면 클래스의 렌더링 메서드 내에서 컴포넌트 이름, 속성 및 슬롯에 액세스 할 수도 있습니다. 그러나 이 데이터에 액세스하려면 컴포넌트의 `render` 메서드에서 Closure를 반환해야합니다. Closure는 유일한 인수로 `$data` 배열을 받습니다

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

            return '<div>Component content</div>';
        };
    }

The `componentName` is equal to the name used in the HTML tag after the `x-` prefix. So `<x-alert />`'s `componentName` will be `alert`. The `attributes` element will contain all of the attributes that were present on the HTML tag. The `slot` element is a `Illuminate\Support\HtmlString` instance with the contents of the slot from the component.

`componentName`은 `x-` 접두사 뒤의 HTML 태그에 사용 된 이름과 동일합니다. 따라서 `<x-alert />`의 `componentName`은 `alert`이 됩니다. `attributes` 요소는 HTML 태그에 있던 모든 속성을 포함합니다. `slot` 요소는 컴포넌트의 슬롯 내용이 포함 된 `Illuminate\Support\HtmlString` 인스턴스입니다.

#### Additional Dependencies
#### 추가 의존성

If your component requires dependencies from Laravel's [service container](/docs/{{version}}/container), you may list them before any of the component's data attributes and they will automatically be injected by the container:

컴포넌트에 라라벨의 [service container](/docs/{{version}}/container)의 종속성이 필요한 경우 컴포넌트의 데이터 속성 앞에 나열하면 컨테이너에 의해 자동으로 주입됩니다.

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

<a name="managing-attributes"></a>
### Managing Attributes
### 속성 관리

We've already examined how to pass data attributes to a component; however, sometimes you may need to specify additional HTML attributes, such as `class`, that are not part of the data required for a component to function. Typically, you want to pass these additional attributes down to the root element of the component template. For example, imagine we want to render an `alert` component like so:

우리는 이미 데이터 속성을 컴포넌트에 전달하는 방법을 살펴 보았습니다. 그러나 때때로 컴포넌트가 작동하는 데 필요한 데이터의 일부가 아닌 `class`와 같은 추가 HTML 속성을 지정해야 할 수도 있습니다. 일반적으로 이러한 추가 속성을 컴포넌트 템플릿의 루트 요소로 전달하려고합니다. 예를 들어, 다음과 같이 `alert` 컴포넌트를 렌더링한다고 가정하십시오.

    <x-alert type="error" :message="$message" class="mt-4"/>

All of the attributes that are not part of the component's constructor will automatically be added to the component's "attribute bag". This attribute bag is automatically made available to the component via the `$attributes` variable. All of the attributes may be rendered within the component by echoing this variable:

컴포넌트 생성자의 일부가 아닌 모든 속성은 컴포넌트의 "attribute bag"에 자동으로 추가됩니다. 이 속성 백은 `$attributes` 변수를 통해 컴포넌트에 자동으로 제공됩니다. 이 변수를 출력하여 컴포넌트 내에서 모든 속성을 렌더링 할 수 있습니다.

    <div {{ $attributes }}>
        <!-- Component Content -->
    </div>

> {note} Using directives such as `@env` directly on a component is not supported at this time.

> {note} 현재는 `@env`와 같은 지시문을 컴포넌트에 직접 사용하는 것은 지원되지 않습니다.

#### Default / Merged Attributes
#### 기본 / 병합 속성

Sometimes you may need to specify default values for attributes or merge additional values into some of the component's attributes. To accomplish this, you may use the attribute bag's `merge` method:

때로는 속성의 기본값을 지정하거나 추가 값을 컴포넌트의 속성 중 일부에 병합해야 할 수도 있습니다. 이를 달성하기 위해 속성 백의 `merge`방법을 사용할 수 있습니다.

    <div {{ $attributes->merge(['class' => 'alert alert-'.$type]) }}>
        {{ $message }}
    </div>

If we assume this component is utilized like so:

이 컴포넌트가 다음과 같이 사용된다고 가정하면

    <x-alert type="error" :message="$message" class="mb-4"/>

The final, rendered HTML of the component will appear like the following:

컴포넌트의 최종 렌더링 HTML은 다음과 같이 나타납니다.

    <div class="alert alert-error mb-4">
        <!-- Contents of the $message variable -->
    </div>

#### Filtering Attributes
#### 필터링 속성

You may filter attributes using the `filter` method. This method accepts a Closure which should return `true` if you wish to retain the attribute in the attribute bag:

`filter` 메소드를 사용하여 속성을 필터링 할 수 있습니다. 이 메서드는 속성-attribute 모음에 속성을 유지하려는 경우 `true`를 반환하는 Closure를 입력받습니다.

    {{ $attributes->filter(fn ($value, $key) => $key == 'foo') }}

For convenience, you may use the `whereStartsWith` method to retrieve all attributes whose keys begin with a given string:

편의를 위해 `whereStartsWith` 메소드를 사용하여 키가 주어진 문자열로 시작하는 모든 속성을 검색 할 수 있습니다.

    {{ $attributes->whereStartsWith('wire:model') }}

Using the `first` method, you may render the first attribute in a given attribute bag:

`first` 메소드를 사용하면 주어진 속성 모음에서 첫 번째 속성을 렌더링 할 수 있습니다.

    {{ $attributes->whereStartsWith('wire:model')->first() }}

<a name="slots"></a>
### Slots
### 슬롯

Often, you will need to pass additional content to your component via "slots". Let's imagine that an `alert` component we created has the following markup:

때로는 "슬롯"을 통해 추가 컨텐츠를 컴포넌트에 전달해야합니다. 우리가 만든 `alert`컴포넌트에 다음과 같은 마크 업이 있다고 가정 해 봅시다.

    <!-- /resources/views/components/alert.blade.php -->

    <div class="alert alert-danger">
        {{ $slot }}
    </div>

We may pass content to the `slot` by injecting content into the component:

컴포넌트에 컨텐츠를 주입하여 `slot`에 컨텐츠를 전달할 수 있습니다.

    <x-alert>
        <strong>Whoops!</strong> Something went wrong!
    </x-alert>

Sometimes a component may need to render multiple different slots in different locations within the component. Let's modify our alert component to allow for the injection of a "title":

때로는 컴포넌트가 컴포넌트 내의 다른 위치에 여러 개의 다른 슬롯을 렌더링해야 할 수도 있습니다. "제목"을 삽입 할 수 있도록 경고 컴포넌트를 수정하겠습니다.

    <!-- /resources/views/components/alert.blade.php -->

    <span class="alert-title">{{ $title }}</span>

    <div class="alert alert-danger">
        {{ $slot }}
    </div>

You may define the content of the named slot using the `x-slot` tag. Any content not within a `x-slot` tag will be passed to the component in the `$slot` variable:

`x-slot` 태그를 사용하여 명명 된 슬롯의 내용을 정의 할 수 있습니다. `x-slot` 태그에없는 모든 내용은 `$slot` 변수의 컴포넌트로 전달됩니다.

    <x-alert>
        <x-slot name="title">
            Server Error
        </x-slot>

        <strong>Whoops!</strong> Something went wrong!
    </x-alert>

#### Scoped Slots
#### 범위가 지정된 슬롯

If you have used a JavaScript framework such as Vue, you may be familiar with "scoped slots", which allow you to access data or methods from the component within your slot. You may achieve similar behavior in Laravel by defining public methods or properties on your component and accessing the component within your slot via the `$component` variable:

Vue와 같은 자바스크립트 프레임워크를 사용했다면 슬롯 내 컴포넌트에서 데이터 또는 메소드에 액세스 할 수있는 "범위가 지정된 슬롯"에 익숙 할 것입니다. 컴포넌트에 퍼블릭 메소드 또는 속성을 정의하고 `$component` 변수를 통해 슬롯 내 컴포넌트에 액세스하여 라라벨에서 유사한 동작을 수행 할 수 있습니다.

    <x-alert>
        <x-slot name="title">
            {{ $component->formatAlert('Server Error') }}
        </x-slot>

        <strong>Whoops!</strong> Something went wrong!
    </x-alert>

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

#### Generating Inline View Components
#### 인라인 뷰 컴포넌트 생성

To create a component that renders an inline view, you may use the `inline` option when executing the `make:component` command:

인라인 뷰를 렌더링하는 컴포넌트를 만들려면 `make:component` 명령을 실행할 때 `inline` 옵션을 사용할 수 있습니다.

    php artisan make:component Alert --inline

<a name="anonymous-components"></a>
### Anonymous Components
### 익명 컴포넌트

Similar to inline components, anonymous components provide a mechanism for managing a component via a single file. However, anonymous components utilize a single view file and have no associated class. To define an anonymous component, you only need to place a Blade template within your `resources/views/components` directory. For example, assuming you have defined a component at `resources/views/components/alert.blade.php`:

인라인 컴포넌트와 마찬가지로 익명 컴포넌트는 단일 파일을 통해 컴포넌트를 관리하는 메커니즘을 제공합니다. 그러나 익명 컴포넌트는 단일 뷰 파일을 사용하며 관련 클래스가 없습니다. 익명의 컴포넌트를 정의하려면 블레이드/템플릿을 `resources/views/components` 디렉토리에만 배치하면됩니다. 예를 들어, `resources/views/components/alert.blade.php`에 컴포넌트를 정의했다고 가정합니다.

    <x-alert/>

You may use the `.` character to indicate if a component is nested deeper inside the `components` directory. For example, assuming the component is defined at `resources/views/components/inputs/button.blade.php`, you may render it like so:

`.` 문자를 사용하여 컴포넌트가 `components` 디렉토리 안에 더 깊게 중첩되어 있는지 여부를 나타낼 수 있습니다. 예를 들어 컴포넌트가 `resources/views/components/inputs/button.blade.php`에 정의되어 있다고 가정하면 다음과 같이 렌더링 할 수 있습니다.

    <x-inputs.button/>

#### Data Properties / Attributes
#### 데이터 속성 / 속성

Since anonymous components do not have any associated class, you may wonder how you may differentiate which data should be passed to the component as variables and which attributes should be placed in the component's [attribute bag](#managing-attributes).

익명 컴포넌트에는 연결된 클래스가 없으므로 컴포넌트에 변수로 전달해야하는 데이터와 컴포넌트의 [속성 백](#managing-attributes)에 어떤 특성을 배치해야하는지 구별 할 수 있습니다.

You may specify which attributes should be considered data variables using the `@props` directive at the top of your component's Blade template. All other attributes on the component will be available via the component's attribute bag. If you wish to give a data variable a default value, you may specify the variable's name as the array key and the default value as the array value:

컴포넌트의 블레이드 템플릿 맨 위에있는 `@props` 지시문을 사용하여 데이터 변수로 간주 할 속성을 지정할 수 있습니다. 컴포넌트의 다른 모든 속성은 컴포넌트의 속성 백을 통해 사용할 수 있습니다. 데이터 변수에 기본값을 지정하려면 변수 이름을 배열 키로 지정하고 기본값을 배열 값으로 지정할 수 있습니다.

    <!-- /resources/views/components/alert.blade.php -->

    @props(['type' => 'info', 'message'])

    <div {{ $attributes->merge(['class' => 'alert alert-'.$type]) }}>
        {{ $message }}
    </div>

<a name="dynamic-components"></a>
### Dynamic Components
### 동적 컴포넌트

Sometimes you may need to render a component but not know which component should be rendered until runtime. In this situation, you may use Laravel's built-in `dynamic-component` component to render the component based on a runtime value or variable:

때때로 컴포넌트 렌더링이 필요하지만 어떤 컴포넌트가 런타임까지 렌더되는지 알지 못합니다. 이러한 상황에서, 런타임 값이나 변수를 기반으로 컴포넌트를 렌더하기 위해 라라벨 내장의 `dynamic-component` 컴포넌트를 사용할 수 있습니다. 

    <x-dynamic-component :component="$componentName" class="mt-4" />

<a name="including-subviews"></a>
## Including Subviews
## 하위 뷰 포함하기

Blade's `@include` directive allows you to include a Blade view from within another view. All variables that are available to the parent view will be made available to the included view:

블레이드의 `@include` 지시어는 뷰 파일 안에 다른 블레이드 뷰를 포함할 수 있도록 해줍니다. 부모 뷰에서 사용가능한 모든 변수들은 하위 뷰에도 사용가능합니다.

    <div>
        @include('shared.errors')

        <form>
            <!-- Form Contents -->
        </form>
    </div>

Even though the included view will inherit all data available in the parent view, you may also pass an array of extra data to the included view:

하위에 포함하게 될 뷰는 부모 뷰의 모든 데이터를 상속하게 되지만, 하위 뷰에 데이터 배열을 직접 전달할 수도 있습니다.

    @include('view.name', ['some' => 'data'])

If you attempt to `@include` a view which does not exist, Laravel will throw an error. If you would like to include a view that may or may not be present, you should use the `@includeIf` directive:

`@include` 의 뷰가 존재하지 않으면 라라벨은 에러를 발생합니다. 존재하지 않을 수도 있는 하위 뷰를 포함하려면 `@includeIf` 지시어를 사용하면 됩니다.

    @includeIf('view.name', ['some' => 'data'])

If you would like to `@include` a view if a given boolean expression evaluates to `true`, you may use the `@includeWhen` directive:

주어진 Boolean 표현식이 `true`일때 뷰를 `@include`하고 싶다면 `@includeWhen` 지시어를 사용할 수 있습니다.

    @includeWhen($boolean, 'view.name', ['some' => 'data'])

If you would like to `@include` a view if a given boolean expression evaluates to `false`, you may use the `@includeUnless` directive:

주어진 Boolean 표현식이 `false`일때 뷰를 `@include`하고 싶다면 `@includeUnless` 지시어를 사용할 수 있습니다.

    @includeUnless($boolean, 'view.name', ['some' => 'data'])

To include the first view that exists from a given array of views, you may use the `includeFirst` directive:

주어진 배열에서 존재하는 것이 확인된 첫번째 뷰를 포함하도록 하려면, `includeFirst` 지시어를 사용하면 됩니다.

    @includeFirst(['custom.admin', 'admin'], ['some' => 'data'])

> {note} You should avoid using the `__DIR__` and `__FILE__` constants in your Blade views, since they will refer to the location of the cached, compiled view.

> {note} 블레이드 뷰에서 `__DIR__`와 `__FILE__` 를 사용하지 마십시오. 이를 사용하면 컴파일된 캐시 뷰의 경로가 반환됩니다.

#### Aliasing Includes
#### Include의 별칭 지정

If your Blade includes are stored in a subdirectory, you may wish to alias them for easier access. For example, imagine a Blade include that is stored at `resources/views/includes/input.blade.php` with the following content:

Blade Include가 하위 디렉토리에 저장되어있는 경우 쉽게 액세스 할 수 있도록 별칭을 지정할 수 있습니다. 예를 들어, 다음 내용으로 `resources/views/includes/input.blade.php`에 저장된 블레이드 포함 시키는 것을 생각해보십시오 :

    <input type="{{ $type ?? 'text' }}">

You may use the `include` method to alias the include from `includes.input` to `input`. Typically, this should be done in the `boot` method of your `AppServiceProvider`:

`include` 메소드를 사용하여 `input`을 `includes.input` 에 대한 include 별칭으로 지정할 수 있습니다. 일반적으로 이것은 `AppServiceProvider`의 `boot` 메소드에서 수행되어야합니다.

    use Illuminate\Support\Facades\Blade;

    Blade::include('includes.input', 'input');

Once the include has been aliased, you may render it using the alias name as the Blade directive:

include에 별칭이 지정되면 별칭 이름을 블레이드 지시문으로 사용하여 렌더링 할 수 있습니다.

    @input(['type' => 'email'])

<a name="rendering-views-for-collections"></a>
### Rendering Views For Collections
### 컬렉션을 뷰에서 렌더링하기

You may combine loops and includes into one line with Blade's `@each` directive:

블레이드의 `@each` 지시어을 사용하면 반복문을 하나의 줄로 구성할 수 있습니다.

    @each('view.name', $jobs, 'job')

The first argument is the view partial to render for each element in the array or collection. The second argument is the array or collection you wish to iterate over, while the third argument is the variable name that will be assigned to the current iteration within the view. So, for example, if you are iterating over an array of `jobs`, typically you will want to access each job as a `job` variable within your view partial. The key for the current iteration will be available as the `key` variable within your view partial.

첫번째 인자는 배열이나 컬렉션의 각 요소를 렌더링하기 위한 부분적 뷰의 이름입니다. 두번째 인자는 반복 처리하는 배열이나 컬렉션이며 세번째 인수는 뷰에서의 반복값이 대입되는 변수의 이름입니다. 예를 들어 `jobs` 배열을 반복 처리하려면 보통 부분적 뷰에서 각 과제를 `job` 변수로 접근해야 할 것입니다. 현재 반복에서의 키값은 부분적 뷰에서 `key` 변수로 접근할 수 있습니다.

You may also pass a fourth argument to the `@each` directive. This argument determines the view that will be rendered if the given array is empty.

또한 `@each` 지시어에 네번째 인수를 전달할 수도 있습니다. 이 인자는 특정 배열이 비었을 경우 렌더링될 뷰를 결정합니다.

    @each('view.name', $jobs, 'job', 'view.empty')

> {note} Views rendered via `@each` do not inherit the variables from the parent view. If the child view requires these variables, you should use `@foreach` and `@include` instead.

> {note} `@each`를 통해서 렌더링 되는 뷰는 부모 뷰에서 변수를 상속받지 않습니다. 만약 자식뷰에서 이 변수들을 사용해야 한다면, 대신 `@foreach` 그리고 `@include` 를 사용해야합니다.

<a name="stacks"></a>
## Stacks
## 스택

Blade allows you to push to named stacks which can be rendered somewhere else in another view or layout. This can be particularly useful for specifying any JavaScript libraries required by your child views:

블레이드는 또한 다른 뷰 또는 레이아웃에서 렌더링 할 수 있도록 이름이 지정된 스택에 푸시 할 수 있습니다. 이는 특히 하위 뷰에 필요한 JavaScript 라이브러리를 지정하는 데 유용합니다.

    @push('scripts')
        <script src="/example.js"></script>
    @endpush

You may push to a stack as many times as needed. To render the complete stack contents, pass the name of the stack to the `@stack` directive:

필요한 경우 여러번 스택에 푸쉬할 수 있습니다. 전체 스택 컨텐츠를 렌더링 하려면, 스택 이름을 `@stack` 지시어에 전달하면 됩니다.

    <head>
        <!-- Head Contents -->

        @stack('scripts')
    </head>

If you would like to prepend content onto the beginning of a stack, you should use the `@prepend` directive:

스택이 시작하는 앞부분에 내용을 추가하고자 한다면, `@prepend` 지시어를 사용하면 됩니다.

    @push('scripts')
        This will be second...
    @endpush

    // Later...

    @prepend('scripts')
        This will be first...
    @endprepend

<a name="service-injection"></a>
## Service Injection
## 서비스 인젝션-주입

The `@inject` directive may be used to retrieve a service from the Laravel [service container](/docs/{{version}}/container). The first argument passed to `@inject` is the name of the variable the service will be placed into, while the second argument is the class or interface name of the service you wish to resolve:

`@inject` 지시어는 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)에서 서비스를 반환하는 데 사용할 수 있습니다. `@inject` 지시어에 전달하는 첫번째 인자는 서비스를 할당할 변수의 이름이고, 두번째는 의존성을 해결하려는 서비스 클래스 또는 인터페이스의 이름입니다.

    @inject('metrics', 'App\Services\MetricsService')

    <div>
        Monthly Revenue: {{ $metrics->monthlyRevenue() }}.
    </div>

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

<a name="custom-if-statements"></a>
### Custom If Statements
### 커스텀 If 구문

Programming a custom directive is sometimes more complex than necessary when defining simple, custom conditional statements. For that reason, Blade provides a `Blade::if` method which allows you to quickly define custom conditional directives using Closures. For example, let's define a custom conditional that checks the current application cloud provider. We may do this in the `boot` method of our `AppServiceProvider`:

커스텀한 지시어를 프로그래밍하면 간단한 조건문을 정의할 때 필요 이상으로 복잡한 경우가 많습니다. 이때문에 블레이드는 클로저를 사용하여 커스텀 If 시지어를 보다 빠르게 정의할 수 있는 `Blade::if` 메소드를 제공합니다. 예를 들어 현재 애플리케이션의 클라우드 공급자를 확인하는 커스텀 지시어를 정의하면 다음처럼 `AppServiceProvider` 의 `boot` 메소드에서 사용할 수 있습니다.

    use Illuminate\Support\Facades\Blade;

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        Blade::if('cloud', function ($provider) {
            return config('filesystems.default') === $provider;
        });
    }

Once the custom conditional has been defined, we can easily use it on our templates:

커스텀 조건을 정의한 뒤에는, 템플릿에서 손쉽게 사용할 수 있습니다.

    @cloud('digitalocean')
        // The application is using the digitalocean cloud provider...
    @elsecloud('aws')
        // The application is using the aws provider...
    @else
        // The application is not using the digitalocean or aws environment...
    @endcloud

    @unlesscloud('aws')
        // The application is not using the aws environment...
    @endcloud
