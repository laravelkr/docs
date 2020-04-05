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
- [폼](#forms)
    - [CSRF 필드](#csrf-field)
    - [메소드 필드](#method-field)
    - [Validation Errors](#validation-errors)
- [컴포넌트](#components)
    - [컴포넌트 표시](#displaying-components)
    - [컴포넌트에 데이터 전달하기](#passing-data-to-components)
    - [속성 관리](#managing-attributes)
    - [슬롯](#slots)
    - [인라인 컴포넌트 뷰](#inline-component-views)
    - [익명 컴포넌트](#anonymous-components)
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

여기서 볼 수 있듯이, 이 파일은 전형적인 HTML 마크업을 가지고 있습니다. 하지만 `@section` 와 `@yield` 지시어을 확인하십시오. `@section` 는 말 그대로 내용의 섹션을 정의하고 `@yield`는 어떤 섹션의 컨텐츠을 나타내는 데에 사용됩니다.

이제 애플리케이션의 레이아웃을 정의하였으니, 이 레이아웃을 상속하는 자식 페이지를 정의하도록 하겠습니다.

<a name="extending-a-layout"></a>
### 레이아웃 확장하기

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

이 예제에 `sidebar` 섹션은 `@@parent` 지시어를 활용해서 레이아웃 사이드바에 컨텐츠를 겹쳐 쓰지 않고 추가합니다. `@@parent` 지시어은 뷰가 렌더링되면 레이아웃의 컨텐츠에 의해 대체됩니다.

> {tip} 이전 예제와는 다르게, `sidebar` 섹션은 `@show` 대신에 `@endsection` 으로 끝납니다. `@endsection` 지시어는 섹션 만을 정의하고, `@show`는 정의하는 **즉시 섹션을 생성** 합니다.

또한 `@yield` 지시어는 두 번째 파라미터를 통해 기본값을 입력받습니다. 해당 섹션이 정의되지 않을 경우 이 값이 렌더링됩니다.

    @yield('content', View::make('view.name'))

블레이드 뷰도 글로벌 `view` 헬퍼를 사용하여 라우트에서 반환될 수 있습니다.

    Route::get('blade', function () {
        return view('child');
    });

<a name="displaying-data"></a>
## 데이터 표시하기

블레이드 뷰로 전달된 데이터를 표시하기 위해 중괄호로 쌓인 변수를 전달할 수 있습니다. 예로 들어 다음의 라우트를 볼 수 있습니다.

    Route::get('greeting', function () {
        return view('welcome', ['name' => 'Samantha']);
    });

다음과 같이 컨텐츠의 `name` 변수를 표시할 수 있습니다.

    Hello, {{ $name }}.

> {tip} 블레이드의 `{{ }}` 구문은 XSS 공격을 방지하기 위해서 자동으로 PHP의 `htmlspecialchars` 함수를 실행하게 됩니다.

뷰에는 전달된 변수들의 컨텐츠만 표시할 수 있는 것은 아닙니다. PHP 함수의 모든 결과는 출력될 수 있습니다. 블레이드에서는 출력되는 어떠한 PHP 코드도 넣을 수 있습니다.

    The current UNIX timestamp is {{ time() }}.

#### Escape 처리되지 않은 데이터 표시하기

기본적으로 블레이드 `{{ }}` 문장은 XSS 공격을 방지하기 위해 PHP의 `htmlspecialchars` 함수를 통과합니다. 데이터를 escape 처리를 하지 않으려면 다음과 같이 작성하면 됩니다.

    Hello, {!! $name !!}.

> {note} 애플리케이션의 사용자들로 부터 입력하여 표시되는 컨텐츠를 출력할 때는 escape-이스케이프에 대한 주의가 필요합니다. 사용자가 제공 한 데이터를 표시 할 때 XSS 공격을 방지하려면 항상 이스케이프 처리 된 이중 중괄호 문법을 사용하십시오.

#### JSON 렌더링

때로는 다음의 예와 같이, 자바스크립트 변수를 초기화하는데 사용하기 위해서 뷰에 배열을 전달하여 json으로 렌더링하기를 원할수도 있습니다.

    <script>
        var app = <?php echo json_encode($array); ?>;
    </script>

직접 `json_encode` 함수를 호출하는 대신에, `@json` 블레이드 지시어를 사용할 수 있습니다. `@json` 지시어는 PHP의 `json_encode` 함수와 같은 인수를 받아들입니다.

    <script>
        var app = @json($array);

        var app = @json($array, JSON_PRETTY_PRINT);
    </script>

> {note} 기존 변수를 JSON으로 렌더링하려면 `@json` 지시어 만 사용해야합니다. Blade 템플릿은 정규 표현식을 기반으로하며 지시어에 복잡한 표현식을 전달하려고하면 예상치 못한 오류가 발생할 수 있습니다.

또한 `@json` 지시어는 Vue 컴포넌트 나 `data-*` 속성을 시딩하는데 유용합니다.

    <example-component :some-prop='@json($array)'></example-component>

> {note} 엘리먼츠의 속성에서 `@json`을 사용하려면 홑따옴표로 묶어야합니다.

#### HTML Entity 인코딩

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
### 블레이드 & 자바스크립트 프레임워크

많은 자바스크립트 프레임워크에서 또한 중괄호를 사용하여 특정 표현이 브라우저에서 표시되어야 하는다는 것을 명시하기 때문에 `@` 기호를 써서 이 중괄호 표현을 유지해야 한다는 것을 블레이드 렌더링 엔진에게 알려 줄 수 있습니다. 예를 들어:

    <h1>Laravel</h1>

    Hello, @{{ name }}.

이 예제에서 `@` 기호는 블레이드에 의해 지워질 것입니다. 하지만 `{{ name }}` 표현은 블레이드 엔진에 의해 영향을 받지 않을 것이며 자바스크립트 프레임워크에 의해 렌더링될 수 있게 해줍니다.

#### `@verbatim` 지시어

여러분이 템플릿의 많은 부분에서 자바스크립트 변수를 표시하는 경우라면, HTML을 `@verbatim` 지시어로 둘러쌓여 있게 할 수 있습니다. 이렇게 하면 여러분은 각각의 블레이드 출력 구문에 `@` 심볼을 매번 붙이지 않아도 됩니다.

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

보다 편리하게 블레이드는 `@unless` 지시어을 제공합니다.

    @unless (Auth::check())
        You are not signed in.
    @endunless

추가적으로 `@isset` 와 `@empty` 지시어는 각각 대응되는 PHP 함수를 편리하게 사용할 수 있는 방법입니다.

    @isset($records)
        // $records is defined and is not null...
    @endisset

    @empty($records)
        // $records is "empty"...
    @endempty

#### 인증 관련 지시어

`@auth` 그리고 `@guest` 지시어는 현재 접속자가 인증된 사용자인지 아니면 guest 인지 판별하는데 사용가능한 편의 기능입니다.

    @auth
        // The user is authenticated...
    @endauth

    @guest
        // The user is not authenticated...
    @endguest

필요한 경우, `@auth` 와 `@guest` 지시어에 체크하고자 하는 [인증 guard](/docs/{{version}}/authentication)를 지정할 수 있습니다.

    @auth('admin')
        // The user is authenticated...
    @endauth

    @guest('admin')
        // The user is not authenticated...
    @endguest

#### 섹션 지시어

`@hasSection` 지시어를 사용하여 섹션이 내용을 가지고 있는지 확인할 수 있습니다.

    @hasSection('navigation')
        <div class="pull-right">
            @yield('navigation')
        </div>

        <div class="clearfix"></div>
    @endif

<a name="switch-statements"></a>
### 스위치 구문

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
### 반복문

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

> {tip} 반복문에서 [루프 변수](#the-loop-variable)를 사용하여 반복의 처음과 마지막에 대한 정보를 얻을 수 있습니다.

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

또한 하나의 라인으로 표현되는 조건식을 포함할 수도 있습니다.

    @foreach ($users as $user)
        @continue($user->type == 1)

        <li>{{ $user->name }}</li>

        @break($user->number == 5)
    @endforeach

<a name="the-loop-variable"></a>
### 루프 변수

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

반복문이 중첩된 경우라면, 상위 반복문의 `$loop` 변수에 `parent` 속성을 통해서 액세스 할 수 있습니다.

    @foreach ($users as $user)
        @foreach ($user->posts as $post)
            @if ($loop->parent->first)
                This is first iteration of the parent loop.
            @endif
        @endforeach
    @endforeach

`$loop` 변수는 그 밖에도 여러가지 유용한 속성을 가지고 있습니다.

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
### 주석

블레이드는 또한 뷰에 주석을 정의할 수 있습니다. 하지만 HTML 주석과는 다르게 블레이드 주석은 애플리케이션이 반환하는 HTML에 포함되어 있지 않습니다.

    {{-- This comment will not be present in the rendered HTML --}}

<a name="php"></a>
### PHP

상황에 따라서, 뷰에서 PHP 코드 자체를 삽입하는 것이 유용할 수도 있습니다. 템플릿 안에서 블레이드의 `@php` 지시어를 사용하여 해당 블럭의 PHP를 실행 할 수 있습니다.

    @php
        //
    @endphp

> {tip} 블레이드가 이 기능을 제공하지만, 이 기능을 너무 빈번하게 사용하는 것은 너무 많은 로직이 템플릿 안에 포함되어 있다는 신호일 수 있습니다.

<a name="forms"></a>
## 폼

<a name="csrf-field"></a>
### CSRF 필드

애플리케이션에서 HTML 폼을 만들 할 때마다 [CSRF 보호](/docs/{{version}}/csrf) 미들웨어가 요청에 대한 유효성 검사를 할 수 있도록 폼에 숨겨진 CSRF 토큰 필드를 포함해야합니다. Blade의 `@csrf` 지시어를 사용하여 토큰 필드를 생성 할 수 있습니다.

    <form method="POST" action="/profile">
        @csrf

        ...
    </form>

<a name="method-field"></a>
### 메소드 필드

HTML 폼은 `PUT`,`PATCH` 또는`DELETE` 요청을 만들 수 없기 때문에 숨겨진 `_method` 필드를 추가하여 HTTP 동작을 속여합니다. Blade의 `@method` 지시어로 이 필드를 생성 할 수 있습니다.

    <form action="/foo/bar" method="POST">
        @method('PUT')

        ...
    </form>

<a name="validation-errors"></a>
### 유효성 검증 실패

`@error` 지시어는 주어진 속성에 대해 [유효성 검증 실패 메세지](/docs/{{version}}/validation#quick-displaying-the-validation-errors) 가 있는지 빠르게 확인하는 데 사용할 수 있습니다. `@error` 지시어 내에서 `$message` 변수를 echo 하여 에러 메시지를 표시 할 수 있습니다.

    <!-- /resources/views/post/create.blade.php -->

    <label for="title">Post Title</label>

    <input id="title" type="text" class="@error('title') is-invalid @enderror">

    @error('title')
        <div class="alert alert-danger">{{ $message }}</div>
    @enderror

`@error` 지시문-directive의 두 번째 파라메터로 [특정 에러 백의 이름](/docs/{{version}}/validation#named-error-bags)을 전달하여 여러개의 폼이 포함 된 페이지에서 유효성 검증 오류 메시지를 조회 할 수 있습니다.

    <!-- /resources/views/auth.blade.php -->

    <label for="email">Email address</label>

    <input id="email" type="email" class="@error('email', 'login') is-invalid @enderror">

    @error('email', 'login')
        <div class="alert alert-danger">{{ $message }}</div>
    @enderror

<a name="components"></a>
## 컴포넌트

컴포넌트와 슬롯은 섹션 및 레이아웃과 유사한 장점을 제공합니다. 그러나 컴포넌트와 슬롯은 결과 모델을 보다 쉽게 이해할 수 있게 해줍니다. 컴포넌트 작성에는 클래스 기반 컴포넌트와 익명 컴포넌트의 두 가지 접근 방식이 있습니다.

클래스 기반 컴포넌트를 만들려면 `make:component` 아티즌 커맨드를 사용할 수 있습니다. 컴포넌트 사용 방법을 설명하기 위해 간단한 `Alert` 컴포넌트를 만들어보겠습니다. `make:component` 커맨드로 `App\View\Components` 디렉토리에 컴포넌트를 만듭니다.

    php artisan make:component Alert

`make:component` 커맨드는 컴포넌트에 대한 뷰템플릿도 만듭니다. 뷰는 `resources/views/components` 디렉토리에 만들어집니다.

#### 패키지 컴포넌트 수동 등록

자신의 애플리케이션을 위한 컴포넌트를 만들때는 컴포넌트는 `app/View/Components` 디렉토리 및 `resources/views/components` 디렉토리에서 자동으로 감지됩니다.

그러나 블레이드 컴포넌트를 사용하는 패키지를 만드는 경우 컴포넌트 클래스와 HTML 별칭 태그를 수동으로 등록해야합니다. 일반적으로 패키지 서비스 제공자의 `boot` 메소드에 컴포넌트를 등록해야합니다.

    use Illuminate\Support\Facades\Blade;

    /**
     * Bootstrap your package's services.
     */
    public function boot()
    {
        Blade::component('package-alert', AlertComponent::class);
    }

컴포넌트가 등록되면 별칭 태그을 사용하여 렌더링 될 수 있습니다.

    <x-package-alert/>

<a name="displaying-components"></a>
### 컴포넌트 표시

컴포넌트를 표시하려면 블레이드 템플릿 중 하나에 블레이드 컴포넌트 태그를 사용할 수 있습니다. 블레이드 컴포넌트 태그는 문자열 `x-`로 시작하고 그 뒤에 컴포넌트 클래스의 케밥 케이스형태의 이름이 옵니다.

    <x-alert/>

    <x-user-profile/>

컴포넌트 클래스가 `App\View\Components` 디렉토리에 더 깊게 중첩 된 경우 `.` 문자를 사용하여 디렉토리 중첩을 표시 할 수 있습니다. 예를 들어 컴포넌트가 `App\View\Components\Inputs\Button.php`에 있다고 가정하면 다음과 같이 렌더링 할 수 있습니다.

    <x-inputs.button/>

<a name="passing-data-to-components"></a>
### 컴포넌트에 데이터 전달하기

HTML 속성을 사용하여 블레이드 컴포넌트에 데이터를 전달할 수 있습니다. 하드코딩 된 기본 값은 간단한 HTML 속성을 사용하여 컴포넌트에 전달 할 수 있습니다. PHP 표현식과 변수는 접두사가 `:`인 속성을 통해 컴포넌트에 전달되어야합니다.

    <x-alert type="error" :message="$message"/>

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
         * @return \Illuminate\View\View|string
         */
        public function render()
        {
            return view('components.alert');
        }
    }

컴포넌트가 렌더링 될 때 이름별로 변수를 출력하여 컴포넌트의 공용 변수 컨텐츠를 표시 할 수 있습니다.

    <div class="alert alert-{{ $type }}">
        {{ $message }}
    </div>

#### Casing

컴포넌트 생성자 인수는 `camelCase`를 사용하여 지정해야하며, `camelCase`는 HTML 속성에서 인수 이름을 참조 할 때 사용해야합니다. 예를 들어 다음과 같은 컴포넌트 생성자가 있습니다.

    /**
     * Create the component instance.
     *
     * @param  string  $alertType
     * @param  string  $message
     * @return void
     */
    public function __construct($alertType)
    {
        $this->alertType = $alertType;
    }

`$alertType` 인수는 다음과 같이 제공 될 수 있습니다.

    <x-alert alert-type="danger" />

#### 컴포넌트 메소드

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

메소드 이름과 일치하는 변수를 호출하여 컴포넌트 템플릿에서 이 메소드를 실행할 수 있습니다.

    <option {{ $isSelected($value) ? 'selected="selected"' : '' }} value="{{ $value }}">
        {{ $label }}
    </option>

컴포넌트 메소드가 인수를 허용하지 않으면 메소드 이름을 함수로 호출하는 대신 변수로 간단히 렌더링 할 수 있습니다. 예를 들어 단순히 문자열을 반환하는 컴포넌트 메서드를 생각해보십시오.

    /**
     * Get the size.
     *
     * @return string
     */
    public function size()
    {
        return 'Large';
    }

컴포넌트 내에서 메소드의 값을 변수로 검색 할 수 있습니다.

    {{ $size }}

#### 추가 의존성

컴포넌트에 라라벨의 [service container](/docs/{{version}}/container)의 종속성이 필요한 경우 컴포넌트의 데이터 속성 앞에 나열하면 컨테이너에 의해 자동으로 주입됩니다.

    use App\AlertCreator

    /**
     * Create the component instance.
     *
     * @param  \App\AlertCreator  $creator
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
### 속성 관리

우리는 이미 데이터 속성을 컴포넌트에 전달하는 방법을 살펴 보았습니다. 그러나 때때로 컴포넌트가 작동하는 데 필요한 데이터의 일부가 아닌 `class`와 같은 추가 HTML 속성을 지정해야 할 수도 있습니다. 일반적으로 이러한 추가 속성을 컴포넌트 템플릿의 루트 요소로 전달하려고합니다. 예를 들어, 다음과 같이 `alert` 컴포넌트를 렌더링한다고 가정하십시오.

    <x-alert type="error" :message="$message" class="mt-4"/>

컴포넌트 생성자의 일부가 아닌 모든 속성은 컴포넌트의 "attribute bag"에 자동으로 추가됩니다. 이 속성 백은 `$attributes` 변수를 통해 컴포넌트에 자동으로 제공됩니다. 이 변수를 출력하여 컴포넌트 내에서 모든 속성을 렌더링 할 수 있습니다.

    <div {{ $attributes }}>
        <!-- Component Content -->
    </div>

#### 기본 / 병합 속성

때로는 속성의 기본값을 지정하거나 추가 값을 컴포넌트의 속성 중 일부에 병합해야 할 수도 있습니다. 이를 달성하기 위해 속성 백의 `merge`방법을 사용할 수 있습니다.

    <div {{ $attributes->merge(['class' => 'alert alert-'.$type]) }}>
        {{ $message }}
    </div>

이 컴포넌트가 다음과 같이 사용된다고 가정하면

    <x-alert type="error" :message="$message" class="mb-4"/>

컴포넌트의 최종 렌더링 HTML은 다음과 같이 나타납니다.

    <div class="alert alert-error mb-4">
        <!-- Contents of the $message variable -->
    </div>

<a name="slots"></a>
### 슬롯

때로는 "슬롯"을 통해 추가 컨텐츠를 컴포넌트에 전달해야합니다. 우리가 만든 `alert`컴포넌트에 다음과 같은 마크 업이 있다고 가정 해 봅시다.

    <!-- /resources/views/components/alert.blade.php -->

    <div class="alert alert-danger">
        {{ $slot }}
    </div>

컴포넌트에 컨텐츠를 주입하여 `slot`에 컨텐츠를 전달할 수 있습니다.

    <x-alert>
        <strong>Whoops!</strong> Something went wrong!
    </x-alert>

때로는 컴포넌트가 컴포넌트 내의 다른 위치에 여러 개의 다른 슬롯을 렌더링해야 할 수도 있습니다. "제목"을 삽입 할 수 있도록 경고 컴포넌트를 수정하겠습니다.

    <!-- /resources/views/components/alert.blade.php -->

    <span class="alert-title">{{ $title }}</span>

    <div class="alert alert-danger">
        {{ $slot }}
    </div>

`x-slot` 태그를 사용하여 명명 된 슬롯의 내용을 정의 할 수 있습니다. `x-slot` 태그에없는 모든 내용은 `$slot` 변수의 컴포넌트로 전달됩니다.

    <x-alert>
        <x-slot name="title">
            Server Error
        </x-slot>

        <strong>Whoops!</strong> Something went wrong!
    </x-alert>

<a name="inline-component-views"></a>
### 인라인 컴포넌트 뷰

매우 작은 컴포넌트의 경우 컴포넌트 클래스와 컴포넌트의 뷰 템플릿을 모두 관리하는 것이 번거로울 수 있습니다. 이러한 이유로 컴포넌트의 마크 업을`render` 메소드에서 직접 반환 할 수 있습니다.

    /**
     * Get the view / contents that represent the component.
     *
     * @return \Illuminate\View\View|string
     */
    public function render()
    {
        return <<<'blade'
            <div class="alert alert-danger">
                {{ $slot }}
            </div>
        blade;
    }

#### 인라인 뷰 컴포넌트 생성

인라인 뷰를 렌더링하는 컴포넌트를 만들려면 `make:component` 명령을 실행할 때 `inline` 옵션을 사용할 수 있습니다.

    php artisan make:component Alert --inline

<a name="anonymous-components"></a>
### 익명 컴포넌트

인라인 컴포넌트와 마찬가지로 익명 컴포넌트는 단일 파일을 통해 컴포넌트를 관리하는 메커니즘을 제공합니다. 그러나 익명 컴포넌트는 단일 뷰 파일을 사용하며 관련 클래스가 없습니다. 익명의 컴포넌트를 정의하려면 블레이드/템플릿을 `resources/views/components` 디렉토리에만 배치하면됩니다. 예를 들어, `resources/views/components/alert.blade.php`에 컴포넌트를 정의했다고 가정합니다.

    <x-alert/>

`.` 문자를 사용하여 컴포넌트가 `components` 디렉토리 안에 더 깊게 중첩되어 있는지 여부를 나타낼 수 있습니다. 예를 들어 컴포넌트가 `resources/views/components/inputs/button.blade.php`에 정의되어 있다고 가정하면 다음과 같이 렌더링 할 수 있습니다.

    <x-inputs.button/>

#### 데이터 속성 / 속성

익명 컴포넌트에는 연결된 클래스가 없으므로 컴포넌트에 변수로 전달해야하는 데이터와 컴포넌트의 [속성 백](#managing-attributes)에 어떤 특성을 배치해야하는지 구별 할 수 있습니다.

컴포넌트의 블레이드 템플릿 맨 위에있는 `@props` 지시문을 사용하여 데이터 변수로 간주 할 속성을 지정할 수 있습니다. 컴포넌트의 다른 모든 속성은 컴포넌트의 속성 백을 통해 사용할 수 있습니다. 데이터 변수에 기본값을 지정하려면 변수 이름을 배열 키로 지정하고 기본값을 배열 값으로 지정할 수 있습니다.


    <!-- /resources/views/components/alert.blade.php -->

    @props(['type' => 'info', 'message'])

    <div {{ $attributes->merge(['class' => 'alert alert-'.$type]) }}>
        {{ $message }}
    </div>

<a name="including-subviews"></a>
## 하위 뷰 포함하기

블레이드의 `@include` 지시어는 뷰 파일 안에 다른 블레이드 뷰를 포함할 수 있도록 해줍니다. 부모 뷰에서 사용가능한 모든 변수들은 하위 뷰에도 사용가능합니다.

    <div>
        @include('shared.errors')

        <form>
            <!-- Form Contents -->
        </form>
    </div>

하위에 포함하게 될 뷰는 부모 뷰의 모든 데이터를 상속하게 되지만, 하위 뷰에 데이터 배열을 직접 전달할 수도 있습니다.

    @include('view.name', ['some' => 'data'])

`@include` 의 뷰가 존재하지 않으면 라라벨은 에러를 발생합니다. 존재하지 않을 수도 있는 하위 뷰를 포함하려면 `@includeIf` 지시어를 사용하면 됩니다.

    @includeIf('view.name', ['some' => 'data'])

주어진 Boolean 표현식이 `true`일때 뷰를 `@include`하고 싶다면 `@includeWhen` 지시어를 사용할 수 있습니다.

    @includeWhen($boolean, 'view.name', ['some' => 'data'])

주어진 Boolean 표현식이 `false`일때 뷰를 `@include`하고 싶다면 `@includeUnless` 지시어를 사용할 수 있습니다.

    @includeUnless($boolean, 'view.name', ['some' => 'data'])

주어진 배열에서 존재하는 것이 확인된 첫번째 뷰를 포함하도록 하려면, `includeFirst` 지시어를 사용하면 됩니다.

    @includeFirst(['custom.admin', 'admin'], ['some' => 'data'])

> {note} 블레이드 뷰에서 `__DIR__`와 `__FILE__` 를 사용하지 마십시오. 이를 사용하면 컴파일된 캐시 뷰의 경로가 반환됩니다.

#### Include의 별칭 지정

Blade Include가 하위 디렉토리에 저장되어있는 경우 쉽게 액세스 할 수 있도록 별칭을 지정할 수 있습니다. 예를 들어, 다음 내용으로 `resources/views/includes/input.blade.php`에 저장된 블레이드 포함 시키는 것을 생각해보십시오.

    <input type="{{ $type ?? 'text' }}">

`include` 메소드를 사용하여 `input`을 `includes.input` 에 대한 include 별칭으로 지정할 수 있습니다. 일반적으로 이것은 `AppServiceProvider`의 `boot` 메소드에서 수행되어야합니다.

    use Illuminate\Support\Facades\Blade;

    Blade::include('includes.input', 'input');

include에 별칭이 지정되면 별칭 이름을 블레이드 지시문으로 사용하여 렌더링 할 수 있습니다.

    @input(['type' => 'email'])

<a name="rendering-views-for-collections"></a>
### 컬렉션을 뷰에서 렌더링하기

블레이드의 `@each` 지시어을 사용하면 반복문을 하나의 줄로 구성할 수 있습니다.

    @each('view.name', $jobs, 'job')

첫번째 인자는 배열이나 컬렉션의 각 요소를 렌더링하기 위한 부분적 뷰의 이름입니다. 두번째 인자는 반복 처리하는 배열이나 컬렉션이며 세번째 인수는 뷰에서의 반복값이 대입되는 변수의 이름입니다. 예를 들어 `jobs` 배열을 반복 처리하려면 보통 부분적 뷰에서 각 과제를 `job` 변수로 접근해야 할 것입니다. 현재 반복에서의 키값은 부분적 뷰에서 `key` 변수로 접근할 수 있습니다.

또한 `@each` 지시어에 네번째 인수를 전달할 수도 있습니다. 이 인자는 특정 배열이 비었을 경우 렌더링될 뷰를 결정합니다.

    @each('view.name', $jobs, 'job', 'view.empty')

> {note} `@each`를 통해서 렌더링 되는 뷰는 부모 뷰에서 변수를 상속받지 않습니다. 만약 자식뷰에서 이 변수들을 사용해야 한다면, 대신 `@foreach` 그리고 `@include` 를 사용해야합니다.

<a name="stacks"></a>
## 스택

블레이드는 또한 다른 뷰 또는 레이아웃에서 렌더링 할 수 있도록 이름이 지정된 스택에 푸시 할 수 있습니다. 이는 특히 하위 뷰에 필요한 JavaScript 라이브러리를 지정하는 데 유용합니다.

    @push('scripts')
        <script src="/example.js"></script>
    @endpush

필요한 경우 여러번 스택에 푸쉬할 수 있습니다. 전체 스택 컨텐츠를 렌더링 하려면, 스택 이름을 `@stack` 지시어에 전달하면 됩니다.

    <head>
        <!-- Head Contents -->

        @stack('scripts')
    </head>

스택이 시작하는 앞부분에 내용을 추가하고자 한다면, `@prepend` 지시어를 사용하면 됩니다.

    @push('scripts')
        This will be second...
    @endpush

    // Later...

    @prepend('scripts')
        This will be first...
    @endprepend

<a name="service-injection"></a>
## 서비스 인젝션-주입

`@inject` 지시어는 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)에서 서비스를 반환하는 데 사용할 수 있습니다. `@inject` 지시어에 전달하는 첫번째 인자는 서비스를 할당할 변수의 이름이고, 두번째는 의존성을 해결하려는 서비스 클래스 또는 인터페이스의 이름입니다.

    @inject('metrics', 'App\Services\MetricsService')

    <div>
        Monthly Revenue: {{ $metrics->monthlyRevenue() }}.
    </div>

<a name="extending-blade"></a>
## 블레이드 기능 확장하기

블레이드에서는 `directive` 메소드를 사용하여 사용자가 고유한 지시어을 정의할 수 있습니다. 블레이드 컴파일러가 사용자가 정의한 지시어을 발견하면 지시어에 정의된 콜백 함수를 호출합니다.

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

예제에서 볼 수 있듯이 이 지시어에 어떤 것이든 전달된 표현식에서 `format` 메소드를 체이닝합니다. 따라서 이 예제의 지시어의 경우에는 최종적으로 생성되는 PHP 코드는 다음과 같습니다.

    <?php echo ($var)->format('m/d/Y H:i'); ?>

> {note} 블레이드 지시어 로직을 수정한 뒤에는, 블레이드 뷰 캐시를 삭제할 필요가 있습니다. 블레이드 뷰의 캐시는 `view:clear` 아티즌 명령어를 사용하여 제거할 수 있습니다.

<a name="custom-if-statements"></a>
### 커스텀 If 구문

커스텀한 지시어를 프로그래밍하면 간단한 조건문을 정의할 때 필요 이상으로 복잡한 경우가 많습니다. 이때문에 블레이드는 클로저를 사용하여 커스텀 If 시지어를 보다 빠르게 정의할 수 있는 `Blade::if` 메소드를 제공합니다. 예를 들어 현재 애플리케이션의 구동 환경을 확인하는 커스텀 지시어를 정의하면 다음처럼 `AppServiceProvider` 의 `boot` 메소드에서 사용할 수 있습니다.

    use Illuminate\Support\Facades\Blade;

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        Blade::if('env', function ($environment) {
            return app()->environment($environment);
        });
    }

커스텀 조건을 정의한 뒤에는, 템플릿에서 손쉽게 사용할 수 있습니다.

    @env('local')
        // The application is in the local environment...
    @elseenv('testing')
        // The application is in the testing environment...
    @else
        // The application is not in the local or testing environment...
    @endenv

    @unlessenv('production')
        // The application is not in the production environment...
    @endenv
