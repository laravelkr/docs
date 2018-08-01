# 블레이드 템플릿

- [시작하기](#introduction)
- [템플릿 상속](#template-inheritance)
    - [레이아웃 정의하기](#defining-a-layout)
    - [레이아웃 확장하기](#extending-a-layout)
- [컴포넌트 & 슬롯](#components-and-slots)
- [데이터 표시하기](#displaying-data)
    - [블레이드 & 자바스크립트 프레임워크](#blade-and-javascript-frameworks)
- [제어 구조](#control-structures)
    - [조건문](#if-statements)
    - [스위치 구문](#switch-statements)
    - [반복문](#loops)
    - [루프 변수](#the-loop-variable)
    - [주석](#comments)
    - [PHP](#php)
- [하위 뷰파일 포함시키기](#including-sub-views)
    - [컬렉션을 뷰에서 렌더링하기](#rendering-views-for-collections)
- [스택](#stacks)
- [서비스 인젝션-주입](#service-injection)
- [블레이드 기능 확장하기](#extending-blade)
    - [커스텀 If 구문](#custom-if-statements)

<a name="introduction"></a>
## 시작하기

블레이드는 라라벨에서 제공하는 간단하지만 강력한 템플릿 엔진입니다. 다른 인지도 높은 PHP 템플릿 엔진들과는 달리 블레이드는 뷰에서 순수한 PHP 코드를 작성하는 것을 허용합니다. 실제로는, 모든 블레이드 뷰는 단순한 PHP 코드로 컴파일되고 변경되기 전까지 캐시 됩니다. 이는 블레이드가 애플리케이션에 아무런 부담을 주지 않는다는 것을 의미합니다. 블레이드 뷰 파일은 `.blade.php` 형식의 파일 확장자를 사용하고 주로 `resources/views`에 저장됩니다.

<a name="template-inheritance"></a>
## 템플릿 상속

<a name="defining-a-layout"></a>
### 레이아웃 정의하기

블레이드의 가장 주요한 두가지 장점은 _템플릿 상속_ 과 _섹션_ 입니다. 먼저 간단한 예를 살펴보겠습니다. 우선 "마스터" 페이지 레이아웃을 구성할 것입니다. 대부분의 웹 애플리케이션이 다양한 페이지에서 동일한 레이아웃을 유지하기 때문에 이 레이아웃을 하나의 블레이드 뷰로 정의하는 것이 편리합니다:

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

여기서 볼 수 있듯이, 이 파일은 전형적인 HTML 마크업을 가지고 있습니다. 하지만 `@section` 와 `@yield` 지시어을 확인하십시오. `@section` 는 말 그대로 내용의 섹션을 정의하고 `@yield`는 어떤 섹션의 컨텐츠을 나타내는 데에 사용됩니다.

이제 애플리케이션의 레이아웃을 정의하였으니, 이 레이아웃을 상속하는 자식 페이지를 정의하도록 하겠습니다.

<a name="extending-a-layout"></a>
### 레이아웃 확장하기

하위 뷰를 정의할 때 블레이드 `@extends` 지시어을 사용해 하위 페이지가 어느 레이아웃을 "상속" 받을지 명시할 수 있습니다. 블레이드 레이아웃을 상속 받는 뷰는 `@section` 지시어를 이용해 레이아웃의 섹션에 컨텐츠를 삽입할 수 있습니다. 위의 예제에서 본 것처럼 이 섹션들의 컨텐츠는 `@yield`를 통해 레이아웃에 명시됩니다:

    <!-- Stored in resources/views/child.blade.php -->

    @extends('layouts.app')

    @section('title', 'Page Title')

    @section('sidebar')
        @parent

        <p>This is appended to the master sidebar.</p>
    @endsection

    @section('content')
        <p>This is my body content.</p>
    @endsection

이 예제에 `sidebar` 섹션은 `@parent` 지시어를 활용해서 레이아웃 사이드바에 컨텐츠를 겹쳐 쓰지 않고 추가합니다. `@parent` 지시어은 뷰가 렌더링되면 레이아웃의 컨텐츠에 의해 대체됩니다.

> {tip} 이전 예제와는 다르게, `sidebar` 섹션은 `@show` 대신에 `@endsection` 으로 끝납니다. `@endsection` 지시어는 섹션 만을 정의하고, `@show`는 정의하는 **즉시 섹션을 생성** 합니다.

블레이드 뷰도 글로벌 `view` 헬퍼를 사용하여 라우트에서 반환될 수 있습니다:

    Route::get('blade', function () {
        return view('child');
    });

<a name="components-and-slots"></a>
## 컴포넌트 & 슬롯

컴포넌트와 슬롯은 섹션 및 레이아웃과 유사한 장점을 제공합니다. 컴포넌트와 슬롯은 결과 모델을 보다 쉽게 이해할 수 있게 해줍니다. 먼저 애플리케이션에서 재사용이 가능한 "경고(alert)" 컴포넌트를 생각해 보겠습니다.

    <!-- /resources/views/alert.blade.php -->

    <div class="alert alert-danger">
        {{ $slot }}
    </div>

이 `{{ $slot }}` 변수는 컴포넌트에 주입될 내용을 가지고 있습니다. 이 컴포넌트를 구성하기 위해서 `@component` 블레이드 지시어를 사용할 수 있습니다:

    @component('alert')
        <strong>Whoops!</strong> Something went wrong!
    @endcomponent

때로는 컴포넌트에 여러개의 슬롯을 정의하는 것이 유용합니다. "제목(title)" 주입이 가능하도록 경고(alert) 컴포넌트를 수정해보겠습니다. 이름이 지정된 슬롯은 일치하는 이름의 변수가 "출력" 되도록 표시할 수 있습니다:

    <!-- /resources/views/alert.blade.php -->

    <div class="alert alert-danger">
        <div class="alert-title">{{ $title }}</div>

        {{ $slot }}
    </div>

그러면 이제, `@slot` 지시어를 사용하여 이름이 지정된 슬롯에 내용을 주입할 수 있습니다. `@slot` 지시어에 포함되어 있지 않는 컨텐츠는 `$slot` 변수의 컴포넌트로 전달됩니다:

    @component('alert')
        @slot('title')
            Forbidden
        @endslot

        You are not allowed to access this resource!
    @endcomponent

#### 추가적인 데이터를 컴포넌트로 전달하기

컴포넌트로 추가적인 데이터를 전달할 필요가 있는 경우, `@component` 지시어의 두번째 인자로 데이터 배열을 전달하면 됩니다. 전달된 데이터는 컴포넌트 템플릿 에서 변수로 사용 가능합니다:

    @component('alert', ['foo' => 'bar'])
        ...
    @endcomponent

#### 별칭 컴포넌트

블레이드 컴포넌트가 서브 디렉토리에 저장되어있다면, 그 컴포넌트에 쉽게 접근하기 위한 별칭을 사용할 수 있습니다. 예를 들어, `resources/views/components/alert.blade.php` 에 저장된 블레이드 컴포넌트가 있다고 생각해봅시다. `component` 메소드를 사용하여 `components.alert`를 `alert`라고 별칭을 지정할 수 있습니다. 일반적으로 이 방법은 `AppServiceProvider` 의 `boot` 메소드 안에서 완료되어야 합니다:

    use Illuminate\Support\Facades\Blade;

    Blade::component('components.alert', 'alert');

해당 컴포넌트에 별칭이 지정되면, 지시어를 사용해서 이를 렌더링할 수 있습니다:

    @alert(['type' => 'danger'])
        You are not allowed to access this resource!
    @endalert

컴포넌트에 추가 슬롯이 없는 경우, 컴포넌트 파라미터를 생략할 수 있습니다:

    @alert
        You are not allowed to access this resource!
    @endalert

<a name="displaying-data"></a>
## 데이터 표시하기

블레이드 뷰로 전달된 데이터를 표시하기 위해 중괄호로 쌓인 변수를 전달할 수 있습니다. 예로 들어 다음의 라우트를 볼 수 있습니다:

    Route::get('greeting', function () {
        return view('welcome', ['name' => 'Samantha']);
    });

다음과 같이 컨텐츠의 `name` 변수를 표시할 수 있습니다:

    Hello, {{ $name }}.

물론 뷰에는 전달된 변수들의 컨텐츠만 표시할 수 있는 것은 아닙니다. PHP 함수의 모든 결과는 출력될 수 있습니다. 블레이드에서는 출력되는 어떠한 PHP 코드도 넣을 수 있습니다:

    The current UNIX timestamp is {{ time() }}.

> {tip} 블레이드 `{{ }}` 문장들은 XSS 공격을 방지하기 위해 자동으로 PHP의 'htmlspecialchars' 함수를 통과합니다.

#### Escape 처리되지 않은 데이터 표시하기

기본적으로 블레이드 `{{ }}` 문장은 XSS 공격을 방지하기 위해 PHP의 `htmlspecialchars` 함수를 통과합니다. 데이터를 escape 처리를 하지 않으려면 다음과 같이 작성하면 됩니다:

    Hello, {!! $name !!}.

> {note} 애플리케이션의 사용자들로 부터 입력하여 표시되는 컨텐츠를 출력할 때는 escape-이스케이프에 대한 주의가 필요합니다. 사용자가 제공 한 데이터를 표시 할 때 XSS 공격을 방지하려면 항상 이스케이프 처리 된 이중 중괄호 문법을 사용하십시오.

#### JSON 렌더링

때로는 다음의 예와 같이, 자바스크립트 변수를 초기화하는데 사용하기 위해서 뷰에 배열을 전달하여 json으로 렌더링하기를 원할수도 있습니다:

    <script>
        var app = <?php echo json_encode($array); ?>;
    </script>

직접 `json_encode` 함수를 호출하는 대신에, `@json` 블레이드 지시어를 사용할 수 있습니다:

    <script>
        var app = @json($array);
    </script>

#### HTML Entity 인코딩

기본적으로, 블레이드 (그리고 라라벨의 `e` 헬퍼)는 HTML 요소를 두번 인코딩을 합니다. 이중 인코딩을 비활성화 하고 싶을 때에는, `AppServiceProvider` 의 `boot` 메소드 안에 `Blade::withoutDoubleEncoding` 메소드를 호출하면 됩니다:

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
### 블레이드 & 자바스크립트 프레임워크

많은 자바스크립트 프레임워크에서 또한 중괄호를 사용하여 특정 표현이 브라우저에서 표시되어야 하는다는 것을 명시하기 때문에 `@` 기호를 써서 이 중괄호 표현을 유지해야 한다는 것을 블레이드 렌더링 엔진에게 알려 줄 수 있습니다. 예를 들어:

    <h1>Laravel</h1>

    Hello, @{{ name }}.

이 예제에서 `@` 기호는 블레이드에 의해 지워질 것입니다. 하지만 `{{ name }}` 표현은 블레이드 엔진에 의해 영향을 받지 않을 것이며 자바스크립트 프레임워크에 의해 렌더링될 수 있게 해줍니다.

#### `@verbatim` 지시어

여러분이 템플릿의 많은 부분에서 자바스크립트 변수를 표시하는 경우라면, HTML을 `@verbatim` 지시어로 둘러쌓여 있게 할 수 있습니다. 이렇게 하면 여러분은 각각의 블레이드 출력 구문에 `@` 심볼을 매번 붙이지 않아도 됩니다:

    @verbatim
        <div class="container">
            Hello, {{ name }}.
        </div>
    @endverbatim

<a name="control-structures"></a>
## 컨트롤 구조

템플릿 상속과 데이터 표시 외에도 블레이드는 공통적인 PHP 컨트롤 구조를 위해서 조건문과 반복문과 같은 편리한 방법들을 제공합니다. 이 방법들은 PHP 컨트롤 구조를 이용할 수 있는 깔끔하고 간단한 방법을 제공하면서 PHP에 익숙한 구조와 비슷하도록 유지합니다.

<a name="if-statements"></a>
### 조건문

`@if`, `@elseif`, `@else`, 그리고 `@endif` 지시어을 이용하여 조건문을 만들 수 있습니다. 이 지시어들은 대응하는 PHP 문장들과 동일하게 동작합니다.

    @if (count($records) === 1)
        I have one record!
    @elseif (count($records) > 1)
        I have multiple records!
    @else
        I don't have any records!
    @endif

보다 편리하게 블레이드는 `@unless` 지시어을 제공합니다:

    @unless (Auth::check())
        You are not signed in.
    @endunless

추가적으로 `@isset` 와 `@empty` 지시어는 각각 대응되는 PHP 함수를 편리하게 사용할 수 있는 방법입니다:

    @isset($records)
        // $records is defined and is not null...
    @endisset

    @empty($records)
        // $records is "empty"...
    @endempty

#### 인증 관련 지시어

`@auth` 그리고 `@guest` 지시어는 현재 접속자가 인증된 사용자인지 아니면 guest 인지 판별하는데 사용가능한 편의 기능입니다:

    @auth
        // The user is authenticated...
    @endauth

    @guest
        // The user is not authenticated...
    @endguest

필요한 경우, `@auth` 와 `@guest` 지시어에 체크하고자 하는 [인증 guard](/docs/{{version}}/authentication)를 지정할 수 있습니다:

    @auth('admin')
        // The user is authenticated...
    @endauth

    @guest('admin')
        // The user is not authenticated...
    @endguest

#### 섹션 지시어

`@hasSection` 지시어를 사용하여 섹션이 내용을 가지고 있는지 확인할 수 있습니다:

    @hasSection('navigation')
        <div class="pull-right">
            @yield('navigation')
        </div>

        <div class="clearfix"></div>
    @endif

<a name="switch-statements"></a>
### 스위치 구문

스위치 구문은 `@switch`, `@case`, `@break`, `@default` 그리고 `@endswitch` 지시어로 구성됩니다:

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
### 반복문

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

> {tip} 반복문에서 [루프 변수](#the-loop-variable)를 사용하여 반복의 처음과 마지막에 대한 정보를 얻을 수 있습니다.

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

또한 하나의 라인으로 표현되는 조건식을 포함할 수도 있습니다:

    @foreach ($users as $user)
        @continue($user->type == 1)

        <li>{{ $user->name }}</li>

        @break($user->number == 5)
    @endforeach

<a name="the-loop-variable"></a>
### 루프 변수

반복문을 사용할 때, 반복문 안에서 `$loop` 변수를 사용할 수 있습니다. 이 변수는 현재의 반복문의 인덱스와 반복문이 첫 번째 또는 마지막 인지 알 수 있는 것과 같은 유용한 정보에 엑세스할 수 있습니다:

    @foreach ($users as $user)
        @if ($loop->first)
            This is the first iteration.
        @endif

        @if ($loop->last)
            This is the last iteration.
        @endif

        <p>This is user {{ $user->id }}</p>
    @endforeach

반복문이 중첩된 경우라면, 상위 반복문의 `$loop` 변수에 `parent` 속성을 통해서 액세스 할 수 있습니다:

    @foreach ($users as $user)
        @foreach ($user->posts as $post)
            @if ($loop->parent->first)
                This is first iteration of the parent loop.
            @endif
        @endforeach
    @endforeach

`$loop` 변수는 그 밖에도 여러가지 유용한 속성을 가지고 있습니다:

속성 | 설명
------------- | -------------
`$loop->index`  |  현재 반복문의 인덱스(0 부터 시작).
`$loop->iteration`  |  현재 반복문의 횟수(1 부터 시작).
`$loop->remaining`  |  반복문의 남은 횟수.
`$loop->count`  |  반복되는 배열의 총 아이템 수.
`$loop->first`  |  현재 반복문의 첫번째 인지 확인.
`$loop->last`  |  현재 반복문의 마지막 인지 확인.
`$loop->depth`  |  중첩된 반복문의 깊이.
`$loop->parent`  |  반복문이 중첩된 경우 부모의 루프 변수.

<a name="comments"></a>
### 주석

블레이드는 또한 뷰에 주석을 정의할 수 있습니다. 하지만 HTML 주석과는 다르게 블레이드 주석은 애플리케이션이 반환하는 HTML에 포함되어 있지 않습니다:

    {{-- This comment will not be present in the rendered HTML --}}

<a name="php"></a>
### PHP

상황에 따라서, 뷰에서 PHP 코드 자체를 삽입하는 것이 유용할 수도 있습니다. 템플릿 안에서 블레이드의 `@php` 지시어를 사용하여 해당 블럭의 PHP를 실행 할 수 있습니다:

    @php
        //
    @endphp

> {tip} 블레이드가 이 기능을 제공하지만, 이 기능을 너무 빈번하게 사용하는 것은 너무 많은 로직이 템플린 안에 포함되어 있다는 신호일 수 있습니다.

<a name="including-sub-views"></a>
## 하위 뷰 포함하기

블레이드의 `@include` 지시어는 뷰 파일 안에 다른 블레이드 뷰를 포함할 수 있도록 해줍니다. 부모 뷰에서 사용가능한 모든 변수들은 하위 뷰에도 사용가능합니다:

    <div>
        @include('shared.errors')

        <form>
            <!-- Form Contents -->
        </form>
    </div>

하위에 포함하게 될 뷰는 부모 뷰의 모든 데이터를 상속하게 되지만, 하위 뷰에 데이터 배열을 직접 전달할 수도 있습니다:

    @include('view.name', ['some' => 'data'])

당연하게도, `@include` 의 뷰가 존재하지 않으면 라라벨은 에러를 발생합니다. 존재하지 않을 수도 있는 하위 뷰를 포함하려면 `@includeIf` 지시어를 사용하면 됩니다:

    @includeIf('view.name', ['some' => 'data'])

조건값에 따라서 뷰 파일을 `@include` 하기를 원한다면 `@includeWhen` 지시어를 사용하면 됩니다:

    @includeWhen($boolean, 'view.name', ['some' => 'data'])

주어진 배열에서 존재하는 것이 확인된 첫번째 뷰를 포함하도록 하려면, `includeFirst` 지시어를 사용하면 됩니다:

    @includeFirst(['custom.admin', 'admin'], ['some' => 'data'])

> {note} 블레이드 뷰에서 `__DIR__`와 `__FILE__` 를 사용하지 마십시오. 이를 사용하면 컴파일된 캐시 뷰의 경로가 반환됩니다.

<a name="rendering-views-for-collections"></a>
### 컬렉션을 뷰에서 렌더링하기

블레이드의 `@each` 지시어을 사용하면 반복문을 하나의 줄로 구성할 수 있습니다:

    @each('view.name', $jobs, 'job')

첫번째 인자는 배열이나 컬렉션의 각 요소를 렌더링하기 위한 부분적 뷰의 이름입니다. 두번째 인자는 반복 처리하는 배열이나 컬렉션이며 세번째 인수는 뷰에서의 반복값이 대입되는 변수의 이름입니다. 예를 들어 `jobs` 배열을 반복 처리하려면 보통 부분적 뷰에서 각 과제를 `job` 변수로 접근해야 할 것입니다. 현재 반복에서의 키값은 부분적 뷰에서 `key` 변수로 접근할 수 있습니다.

또한 `@each` 지시어에 네번째 인수를 전달할 수도 있습니다. 이 인자는 특정 배열이 비었을 경우 렌더링될 뷰를 결정합니다.

    @each('view.name', $jobs, 'job', 'view.empty')

> {note} `@each`를 통해서 렌더링 되는 뷰는 부모 뷰에서 변수를 상속받지 않습니다. 만약 자식뷰에서 이 변수들을 사용해야 한다면, 대신 `@foreach` 그리고 `@include` 를 사용해야합니다.

<a name="stacks"></a>
## 스택

블레이드는 또한 다른 뷰 또는 레이아웃에서 렌더링 할 수 있도록 이름이 지정된 스택에 푸시 할 수 있습니다. 이는 특히 하위 뷰에 필요한 JavaScript 라이브러리를 지정하는 데 유용합니다:

    @push('scripts')
        <script src="/example.js"></script>
    @endpush

필요한 경우 여러번 스택에 푸쉬할 수 있습니다. 전체 스택 컨텐츠를 렌더링 하려면, 스택 이름을 `@stack` 지시어에 전달하면 됩니다:

    <head>
        <!-- Head Contents -->

        @stack('scripts')
    </head>

스택이 시작하는 앞부분에 내용을 추가하고자 한다면, `@prepend` 지시어를 사용하면 됩니다:

    @push('scripts')
        This will be second...
    @endpush

    // Later...

    @prepend('scripts')
        This will be first...
    @endprepend

<a name="service-injection"></a>
## 서비스 인젝션-주입

`@inject` 지시어는 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)에서 서비스를 반환하는 데 사용할 수 있습니다. `@inject` 지시어에 전달하는 첫번째 인자는 서비스를 할당할 변수의 이름이고, 두번째는 의존성을 해결하려는 서비스 클래스 또는 인터페이스의 이름입니다:

    @inject('metrics', 'App\Services\MetricsService')

    <div>
        Monthly Revenue: {{ $metrics->monthlyRevenue() }}.
    </div>

<a name="extending-blade"></a>
## 블레이드 기능 확장하기

블레이드에서는 `directive` 메소드를 사용하여 사용자가 고유한 지시어을 정의할 수 있습니다. 블레이드 컴파일러가 사용자가 정의한 지시어을 발견하면 지시어에 정의된 콜백 함수를 호출합니다.

다음의 예제는 전달된 `DateTime` 인스턴스인 `$var`의 포맷을 변경하는 `@datetime($var)` 지시어을 생성합니다:

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\Blade;
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
            Blade::directive('datetime', function ($expression) {
                return "<?php echo ($expression)->format('m/d/Y H:i'); ?>";
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

예제에서 볼 수 있듯이 이 지시어에 어떤 것이든 전달된 표현식에서 `format` 메소드를 체이닝합니다. 따라서 이 예제의 지시어의 경우에는 최종적으로 생성되는 PHP 코드는 다음과 같습니다:

    <?php echo ($var)->format('m/d/Y H:i'); ?>

> {note} 블레이드 지시어 로직을 수정한 뒤에는, 블레이드 뷰 캐시를 삭제할 필요가 있습니다. 블레이드 뷰의 캐시는 `view:clear` 아티즌 명령어를 사용하여 제거할 수 있습니다.

<a name="custom-if-statements"></a>
### 커스텀 If 구문

커스텀한 지시어를 프로그래밍하면 간단한 조건문을 정의할 때 필요 이상으로 복잡한 경우가 많습니다. 이때문에 블레이드는 클로저를 사용하여 커스텀 If 시지어를 보다 빠르게 정의할 수 있는 `Blade::if` 메소드를 제공합니다. 예를 들어 현재 애플리케이션의 구동 환경을 확인하는 커스텀 지시어를 정의하면 다음처럼 `AppServiceProvider` 의 `boot` 메소드에서 사용할 수 있습니다:

    use Illuminate\Support\Facades\Blade;

    /**
     * Perform post-registration booting of services.
     *
     * @return void
     */
    public function boot()
    {
        Blade::if('env', function ($environment) {
            return app()->environment($environment);
        });
    }

커스텀 조건을 정의한 뒤에는, 템플릿에서 손쉽게 사용할 수 있습니다:

    @env('local')
        // The application is in the local environment...
    @elseenv('testing')
        // The application is in the testing environment...
    @else
        // The application is not in the local or testing environment...
    @endenv
