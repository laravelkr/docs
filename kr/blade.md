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
- [Control Structures](#control-structures)
- [컨트롤 구조](#control-structures)
- [Stacks](#stacks)
- [스택](#stacks)
- [Service Injection](#service-injection)
- [서비스 주입하기](#service-injection)
- [Extending Blade](#extending-blade)
- [블레이드 기능 확장하기](#extending-blade)

<a name="introduction"></a>
## Introduction
## 시작하기

Blade is the simple, yet powerful templating engine provided with Laravel. Unlike other popular PHP templating engines, Blade does not restrict you from using plain PHP code in your views. All Blade views are compiled into plain PHP code and cached until they are modified, meaning Blade adds essentially zero overhead to your application. Blade view files use the `.blade.php` file extension and are typically stored in the `resources/views` directory.

블레이드는 라라벨에서 제공하는 간단하지만 강력한 템플릿 엔진입니다. 다른 인지도 높은 PHP 템플릿 엔진들과는 달리 블레이드는 뷰에서 순수한 PHP 코드를 작성하는 것을 허용합니다. 모든 블레이드 뷰는 단순한 PHP 코드로 컴파일되고 변경되기 전까지 캐시 됩니다. 이는 블레이드가 애플리케이션에 아무런 부담을 주지 않는다는 것을 의미합니다. 블레이드 뷰 파일은 `.blade.php` 형식의 파일 확장자를 사용하고 주로 `resources/views`에 저장됩니다.

<a name="template-inheritance"></a>
## Template Inheritance
## 템플릿 상속

<a name="defining-a-layout"></a>
### Defining A Layout
### 레이아웃 정의하기

Two of the primary benefits of using Blade are _template inheritance_ and _sections_. To get started, let's take a look at a simple example. First, we will examine a "master" page layout. Since most web applications maintain the same general layout across various pages, it's convenient to define this layout as a single Blade view:

블레이드의 가장 주요한 두가지 장점은 _템플릿 상속_과 _섹션_입니다. 먼저 간단한 예를 살펴보겠습니다. 우선 "마스터" 페이지 레이아웃을 구성할 것입니다. 대부분의 웹 애플리케이션이 다양한 페이지에서 동일한 레이아웃을 유지하기 때문에 이 레이아웃을 하나의 블레이드 뷰로 정의하는 것이 편리합니다:

    <!-- Stored in resources/views/layouts/master.blade.php -->

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

When defining a child page, you may use the Blade `@extends` directive to specify which layout the child page should "inherit". Views which `@extends` a Blade layout may inject content into the layout's sections using `@section` directives. Remember, as seen in the example above, the contents of these sections will be displayed in the layout using `@yield`:

자식 페이지를 정의할 때 블레이드 `@extends` 지시어을 사용해 자식 페이지가 어느 레이아웃을 "상속" 받을지 명시할 수 있습니다. 블레이드 레이아웃을 `@extends`하는 뷰는 `@section` 를 이용해 레이아웃의 섹션에 텐츠를 삽입할 수 있습니다. 위의 예제에서 본 것처럼 이 섹션들의 컨텐츠는 `@yield`를 통해 레이아웃에 명시될 것입니다:

    <!-- Stored in resources/views/child.blade.php -->

    @extends('layouts.master')

    @section('title', 'Page Title')

    @section('sidebar')
        @@parent

        <p>This is appended to the master sidebar.</p>
    @endsection

    @section('content')
        <p>This is my body content.</p>
    @endsection

In this example, the `sidebar` section is utilizing the `@@parent` directive to append (rather than overwriting) content to the layout's sidebar. The `@@parent` directive will be replaced by the content of the layout when the view is rendered.

이 예제에서 `sidebar` 섹션은 `@@parent` 지시문을 활용하여 레이아웃 사이드바에 컨텐츠를 겹쳐 쓰지 않고 추가합니다. `@@parent` 지시문은 뷰가 렌더링되면 레이아웃의 컨텐츠에 의해 대체됩니다.

Of course, just like plain PHP views, Blade views may be returned from routes using the global `view` helper function:

물론 순수 PHP 뷰와 같이 블레이드 뷰도 공통 `view` 헬퍼 기능을 이용하여 라우트에서 반환될 수 있습니다:

    Route::get('blade', function () {
        return view('child');
    });

<a name="displaying-data"></a>
## Displaying Data
## 데이터 표시하기

You may display data passed to your Blade views by wrapping the variable in "curly" braces. For example, given the following route:

블레이드 뷰로 전달된 데이터를 표시하기 위해 중괄호로 쌓인 변수를 전달할 수 있습니다. 예로 들어 다음의 라우트를 볼 수 있습니다.:

    Route::get('greeting', function () {
        return view('welcome', ['name' => 'Samantha']);
    });

You may display the contents of the `name` variable like so:

다음과 같이 컨텐츠의 `name` 변수를 표시할 수 있습니다:

    Hello, {{ $name }}.

Of course, you are not limited to displaying the contents of the variables passed to the view. You may also echo the results of any PHP function. In fact, you can put any PHP code you wish inside of a Blade echo statement:

물론 뷰에는 전달된 변수들의 컨텐츠만 표시할 수 있는 것은 아닙니다. PHP 함수의 모든 결과는 출력될 수 있습니다. 블레이드에서는 출력되는 어떠한 PHP 코드도 넣을 수 있습니다:

    The current UNIX timestamp is {{ time() }}.

> **Note:** Blade `{{ }}` statements are automatically sent through PHP's `htmlentities` function to prevent XSS attacks.

> **주의:** 블레이드 `{{ }}` 문장들은 XSS 공격을 방지하기 위해 자동으로 PHP의 'htmlentities' 함수를 통과합니다.

#### Blade & JavaScript Frameworks
#### 블레이드 & 자바스크립트 프레임워크

Since many JavaScript frameworks also use "curly" braces to indicate a given expression should be displayed in the browser, you may use the `@` symbol to inform the Blade rendering engine an expression should remain untouched. For example:

많은 자바스크립트 프레임워크에서 또한 중괄호를 사용하여 특정 표현이 브라우저에서 표시되어야 하는다는 것을 명시하기 때문에 `@` 기호를 써서 이 중괄호 표현을 유지해야 한다는 것을 블레이드 렌더링 엔진에게 알려 줄 수 있습니다. 예를 들어:

    <h1>Laravel</h1>

    Hello, @{{ name }}.

In this example, the `@` symbol will be removed by Blade; however, `{{ name }}` expression will remain untouched by the Blade engine, allowing it to instead be rendered by your JavaScript framework.

이 예제에서 `@` 기호는 블레이드에 의해 지워질 것입니다. 하지만 `{{ name }}` 표현은 블레이드 엔진에 의해 영향을 받지 않을 것이며 자바스크립트 프레임워크에 의해 렌더링될 수 있게 해줍니다.

#### Echoing Data If It Exists
#### 데이터가 존재할 때 출력하기

Sometimes you may wish to echo a variable, but you aren't sure if the variable has been set. We can express this in verbose PHP code like so:

때로는 변수를 출력하고자 할 때 해당 변수가 존재하는지 정확하게 알지 못할 수도 있습니다. 이경우 PHP 코드로 다음과 같이 길게 표현될 수 있습니다:

    {{ isset($name) ? $name : 'Default' }}

However, instead of writing a ternary statement, Blade provides you with the following convenient short-cut:

하지만 이 경우 삼항연산자를 작성하는 대신 블레이드에서는 짧게 축약해서 표현할 수 있습니다:

    {{ $name or 'Default' }}

In this example, if the `$name` variable exists, its value will be displayed. However, if it does not exist, the word `Default` will be displayed.

이 예제의 경우 `$name` 변수가 존재한다면 해당 값이 표시될 것입니다. 하지만 값이 존재하지 않는다면 `Default` 가 표시될 것입니다.

#### Displaying Unescaped Data
#### Escape 처리되지 않은 데이터 표시하기

By default, Blade `{{ }}` statements are automatically sent through PHP's `htmlentities` function to prevent XSS attacks. If you do not want your data to be escaped, you may use the following syntax:

기본적으로 블레이드 `{{ }}` 문장은 XSS 공격을 방지하기 위해 PHP의 `htmlentities` 함수를 통과합니다. 데이터를 escape 처리를 하지 않으려면 다음과 같이 작성하면 됩니다:

    Hello, {!! $name !!}.

> **Note:** Be very careful when echoing content that is supplied by users of your application. Always use the double curly brace syntax to escape any HTML entities in the content.

> **주의:** 애플리케이션의 사용자들로 부터 입력되어 표현하는 컨텐츠를 출력할 때는 escape에 대한 주의가 필요합니다. 컨텐츠의 HTML 엔티티를 escape하기 위해서 항상 이중 중괄호 표기법을 사용하면 됩니다.

<a name="control-structures"></a>
## Control Structures
## 컨트롤 구조

In addition to template inheritance and displaying data, Blade also provides convenient short-cuts for common PHP control structures, such as conditional statements and loops. These short-cuts provide a very clean, terse way of working with PHP control structures, while also remaining familiar to their PHP counterparts.

템플릿 상속과 데이터 표시 외에도 블레이드는 공통적인 PHP 컨트롤 구조를 위해서 조건문과 반복문과 같은 편리한 방법들을 제공합니다. 이 방법들은 PHP 컨트롤 구조를 이용할 수 있는 깔끔하고 간단한 방법을 제공하면서 PHP에 익숙한 구조와 비슷하도록 유지합니다.

#### If Statements
#### 조건문

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

보다 편리하게 블레이드는 `@unless` 지시어을 제공합니다:

    @unless (Auth::check())
        You are not signed in.
    @endunless

You may also determine if a given layout section has any content using the `@hasSection` directive:

또한 주어진 섹션이 컨텐츠 내용을 가지고 있는지 확인하기 위해 `@hasSection` 지시어를 사용할 수도 있습니다.

    <title>
        @hasSection ('title')
            @yield('title') - App Name
        @else
            App Name
        @endif
    </title>

#### Loops
#### 반복문

In addition to conditional statements, Blade provides simple directives for working with PHP's supported loop structures. Again, each of these directives functions identically to their PHP counterparts:

조건문 외에도 블레이드는 PHP의 반복 구조를 위한 단순한 지시어들을 제공합니다. 이 지시어들 또한 대응하는 PHP 문장들과 동일하게 동작합니다:

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

When using loops you might need to end the loop or skip the current iteration:

반복문을 사용할 때에는 반복문의 중료 또는 현재 반복의 중단을 표시할 필요가 있습니다:

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

또한 하나의 라인으로 표현되는 조건식을 포함할 수도 있습니다:

    @foreach ($users as $user)
        @continue($user->type == 1)

        <li>{{ $user->name }}</li>

        @break($user->number == 5)
    @endforeach

#### Including Sub-Views
#### 하위 뷰 포함하기

Blade's `@include` directive, allows you to easily include a Blade view from within an existing view. All variables that are available to the parent view will be made available to the included view:

블레이드의 `@include` 지시어은 이미 존재하는 뷰 내부에 블레이드 뷰를 쉽게 포함할 수 있도록 해줍니다. 부모 뷰에서 볼 수 있는 모든 변수들은 포함되는 뷰에도 제공될 것입니다.

    <div>
        @include('shared.errors')

        <form>
            <!-- Form Contents -->
        </form>
    </div>

Even though the included view will inherit all data available in the parent view, you may also pass an array of extra data to the included view:

하위에 포함하게 될 뷰는 부모 뷰의 모든 데이터를 상속하게 되지만, 하위 뷰에 데이터 배열을 직접 전달할 수도 있습니다.

    @include('view.name', ['some' => 'data'])

> **Note:** You should avoid using the `__DIR__` and `__FILE__` constants in your Blade views, since they will refer to the location of the cached view.

> **주의:** 블레이드 뷰에서 `__DIR__`와 `__FILE__` 를 사용하지 마십시오. 이를 사용하면 캐시된 뷰의 경로가 반환됩니다.

#### Rendering Views For Collections
#### 컬렉션을 뷰에서 렌더링하기

You may combine loops and includes into one line with Blade's `@each` directive:

블레이드의 `@each` 지시오을 사용하면 반복문을 하나의 줄로 구성할 수 있습니다:

    @each('view.name', $jobs, 'job')

The first argument is the view partial to render for each element in the array or collection. The second argument is the array or collection you wish to iterate over, while the third argument is the variable name that will be assigned to the current iteration within the view. So, for example, if you are iterating over an array of `jobs`, typically you will want to access each job as a `job` variable within your view partial. The key for the current iteration will be available as the `key` variable within your view partial.

첫번째 인자는 배열이나 컬렉션의 각 요소를 렌더링하기 위한 부분적 뷰의 이름입니다. 두번째 인자는 반복 처리하는 배열이나 컬렉션이며 세번째 인수는 뷰에서의 반복값이 대입되는 변수의 이름입니다. 예를 들어 `jobs` 배열을 반복 처리하려면 보통 부분적 뷰에서 각 과제를 `job` 변수로 접근해야 할 것입니다. 현재 반복에서의 키값은 부분적 뷰에서 `key` 변수로 접근할 수 있습니다.

You may also pass a fourth argument to the `@each` directive. This argument determines the view that will be rendered if the given array is empty.

또한 `@each` 지시어로 네번째 인수를 전달할 수도 있습니다. 이 인자는 특정 배열이 비었을 경우 렌더링될 뷰를 결정합니다.

    @each('view.name', $jobs, 'job', 'view.empty')

#### Comments
#### 주석

Blade also allows you to define comments in your views. However, unlike HTML comments, Blade comments are not included in the HTML returned by your application:

블레이드는 또한 뷰에 주석을 정의할 수 있습니다. 하지만 HTML 주석과는 다르게 블레이드 주석은 애플리케이션이 반환하는 HTML에 포함되어 있지 않습니다:

    {{-- This comment will not be present in the rendered HTML --}}

<a name="stacks"></a>
## Stacks
## 스택

Blade also allows you to push to named stacks which can be rendered somewhere else in another view or layout:

블레이드는 또한 다른 뷰 또는 레이아웃에서 렌더링 할 수 있도록 이름이 지정된 스택에 푸시 할 수 있습니다:

    @push('scripts')
        <script src="/example.js"></script>
    @endpush

You may push to the same stack as many times as needed. To render a stack, use the `@stack` syntax:

필요한 경우 동일한 스택에 여러번 푸시할 수 있습니다. 스택을 렌더링 하려면, `@stack` 문법을 사용하면 됩니다:

    <head>
        <!-- Head Contents -->

        @stack('scripts')
    </head>

<a name="service-injection"></a>
## Service Injection
## 서비스 주입하기

The `@inject` directive may be used to retrieve a service from the Laravel [service container](/docs/{{version}}/container). The first argument passed to `@inject` is the name of the variable the service will be placed into, while the second argument is the class / interface name of the service you wish to resolve:

`@inject` 지시어는 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)에서 서비스를 반환하는 데에 사용될 수 있습니다. `@inject`에 전달된 첫번째 인자는 서비스가 위치할 변수의 이름이며 두번째 인수는 의존성을 해결하려는 클래스/인터페이스의 이름입니다:

    @inject('metrics', 'App\Services\MetricsService')

    <div>
        Monthly Revenue: {{ $metrics->monthlyRevenue() }}.
    </div>

<a name="extending-blade"></a>
## Extending Blade
## 블레이드 기능 확장하기

Blade even allows you to define your own custom directives. You can use the `directive` method to register a directive. When the Blade compiler encounters the directive, it calls the provided callback with its parameter.

블레이드는 사용자가 고유의 지시어을 정의할 수 있습니다. `directive` 메소드를 이용해 지시어을 등록할 수 있습니다. 블레이드 컴파일러가 지시어을 발견하면 인자에 지정한 콜백 함수를 호출합니다.

The following example creates a `@datetime($var)` directive which formats a given `$var`:

다음의 예제는 전달된 `$var`의 포맷을 변경하는 `@datetime($var)` 지시문을 생성합니다:

    <?php

    namespace App\Providers;

    use Blade;
    use Illuminate\Support\ServiceProvider;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Perform post-registration booting of services.
         *
         * @return void
         */
        public function boot()
        {
            Blade::directive('datetime', function($expression) {
                return "<?php echo with{$expression}->format('m/d/Y H:i'); ?>";
            });
        }

        /**
         * Register bindings in the container.
         *
         * @return void
         */
        public function register()
        {
            //
        }
    }

As you can see, Laravel's `with` helper function was used in this directive. The `with` helper simply returns the object / value it is given, allowing for convenient method chaining. The final PHP generated by this directive will be:

예제에서 볼 수 있듯이 이 지시어에서 라라벨의 `with` 헬퍼 함수가 사용되었습니다. `with` 헬퍼는 단순히 지정된 객체/값을 반환하며 편리한 메소드 체인을 제공합니다. 이 지시어가 생성하는 최종 PHP는 다음과 같습니다:

    <?php echo with($var)->format('m/d/Y H:i'); ?>

After updating the logic of a Blade directive, you will need to delete all of the cached Blade views. The cached Blade views may be removed using the `view:clear` Artisan command.

블레이드 지시어 로직을 수정한 뒤에는, 블레이드 뷰 캐시를 삭제할 필요가 있습니다. 블레이드 뷰의 캐시는 `view:clear` 아티즌 명령어를 사용하여 제거할 수 있습니다.
