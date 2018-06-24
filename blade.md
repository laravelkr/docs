# 블레이드 템플릿

- [소개](#introduction)
- [템플릿 상속](#template-inheritance)
    - [레이아웃 정의하기](#defining-a-layout)
    - [레이아웃 확장하기](#extending-a-layout)
- [데이터 표시하기](#displaying-data)
- [컨트롤 구조](#control-structures)
- [스택](#stacks)
- [서비스 주입하기](#service-injection)
- [블레이드 기능 확장하기](#extending-blade)

<a name="introduction"></a>
## 소개

블레이드는 라라벨에서 제공하는 간단하지만 강력한 템플릿 엔진입니다. 다른 인지도 높은 PHP 템플릿 엔진들과는 달리 블레이드는 뷰에서 순수한 PHP 코드를 작성하는 것을 허용합니다. 모든 블레이드 뷰는 단순한 PHP 코드로 컴파일되고 변경되기 전까지 캐시 됩니다. 이는 블레이드가 애플리케이션에 아무런 부담을 주지 않는다는 것을 의미합니다. 블레이드 뷰 파일은 `.blade.php` 형식의 파일 확장자를 사용하고 주로 `resources/views`에 저장됩니다.

<a name="template-inheritance"></a>
## 템플릿 상속

<a name="defining-a-layout"></a>
### 레이아웃 정의하기

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

여기서 볼 수 있듯이, 이 파일은 전형적인 HTML 마크업을 가지고 있습니다. 하지만 `@section` 와 `@yield` 지시어을 확인하십시오. `@section` 는 말 그대로 내용의 섹션을 정의하고 `@yield`는 어떤 섹션의 컨텐츠을 나타내는 데에 사용됩니다.

이제 애플리케이션의 레이아웃을 정의하였으니, 이 레이아웃을 상속하는 자식 페이지를 정의하도록 하겠습니다.

<a name="extending-a-layout"></a>
### 레이아웃 확장하기

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

이 예제에서 `sidebar` 섹션은 `@@parent` 지시문을 활용하여 레이아웃 사이드바에 컨텐츠를 겹쳐 쓰지 않고 추가합니다. `@@parent` 지시문은 뷰가 렌더링되면 레이아웃의 컨텐츠에 의해 대체됩니다.

물론 순수 PHP 뷰와 같이 블레이드 뷰도 공통 `view` 헬퍼 기능을 이용하여 라우트에서 반환될 수 있습니다:

    Route::get('blade', function () {
        return view('child');
    });

<a name="displaying-data"></a>
## 데이터 표시하기

블레이드 뷰로 전달된 데이터를 표시하기 위해 중괄호로 쌓인 변수를 전달할 수 있습니다. 예로 들어 다음의 라우트를 볼 수 있습니다.:

    Route::get('greeting', function () {
        return view('welcome', ['name' => 'Samantha']);
    });

다음과 같이 컨텐츠의 `name` 변수를 표시할 수 있습니다:

    Hello, {{ $name }}.

물론 뷰에는 전달된 변수들의 컨텐츠만 표시할 수 있는 것은 아닙니다. PHP 함수의 모든 결과는 출력될 수 있습니다. 블레이드에서는 출력되는 어떠한 PHP 코드도 넣을 수 있습니다:

    The current UNIX timestamp is {{ time() }}.

> **주의:** 블레이드 `{{ }}` 문장들은 XSS 공격을 방지하기 위해 자동으로 PHP의 'htmlentities' 함수를 통과합니다.

#### 블레이드 & 자바스크립트 프레임워크

많은 자바스크립트 프레임워크에서 또한 중괄호를 사용하여 특정 표현이 브라우저에서 표시되어야 하는다는 것을 명시하기 때문에 `@` 기호를 써서 이 중괄호 표현을 유지해야 한다는 것을 블레이드 렌더링 엔진에게 알려 줄 수 있습니다. 예를 들어:

    <h1>Laravel</h1>

    Hello, @{{ name }}.

이 예제에서 `@` 기호는 블레이드에 의해 지워질 것입니다. 하지만 `{{ name }}` 표현은 블레이드 엔진에 의해 영향을 받지 않을 것이며 자바스크립트 프레임워크에 의해 렌더링될 수 있게 해줍니다.

#### 데이터가 존재할 때 출력하기

때로는 변수를 출력하고자 할 때 해당 변수가 존재하는지 정확하게 알지 못할 수도 있습니다. 이경우 PHP 코드로 다음과 같이 길게 표현될 수 있습니다:

    {{ isset($name) ? $name : 'Default' }}

하지만 이 경우 삼항연산자를 작성하는 대신 블레이드에서는 짧게 축약해서 표현할 수 있습니다:

    {{ $name or 'Default' }}

이 예제의 경우 `$name` 변수가 존재한다면 해당 값이 표시될 것입니다. 하지만 값이 존재하지 않는다면 `Default` 가 표시될 것입니다.

#### Escape 처리되지 않은 데이터 표시하기

기본적으로 블레이드 `{{ }}` 문장은 XSS 공격을 방지하기 위해 PHP의 `htmlentities` 함수를 통과합니다. 데이터를 escape 처리를 하지 않으려면 다음과 같이 작성하면 됩니다:

    Hello, {!! $name !!}.

> **주의:** 애플리케이션의 사용자들로 부터 입력되어 표현하는 컨텐츠를 출력할 때는 escape에 대한 주의가 필요합니다. 컨텐츠의 HTML 엔티티를 escape하기 위해서 항상 이중 중괄호 표기법을 사용하면 됩니다.

<a name="control-structures"></a>
## 컨트롤 구조

템플릿 상속과 데이터 표시 외에도 블레이드는 공통적인 PHP 컨트롤 구조를 위해서 조건문과 반복문과 같은 편리한 방법들을 제공합니다. 이 방법들은 PHP 컨트롤 구조를 이용할 수 있는 깔끔하고 간단한 방법을 제공하면서 PHP에 익숙한 구조와 비슷하도록 유지합니다.

#### 조건문

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

또한 주어진 섹션이 컨텐츠 내용을 가지고 있는지 확인하기 위해 `@hasSection` 지시어를 사용할 수도 있습니다.

    <title>
        @hasSection ('title')
            @yield('title') - App Name
        @else
            App Name
        @endif
    </title>

#### 반복문

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

#### 하위 뷰 포함하기

블레이드의 `@include` 지시어은 이미 존재하는 뷰 내부에 블레이드 뷰를 쉽게 포함할 수 있도록 해줍니다. 부모 뷰에서 볼 수 있는 모든 변수들은 포함되는 뷰에도 제공될 것입니다.

    <div>
        @include('shared.errors')

        <form>
            <!-- Form Contents -->
        </form>
    </div>

하위에 포함하게 될 뷰는 부모 뷰의 모든 데이터를 상속하게 되지만, 하위 뷰에 데이터 배열을 직접 전달할 수도 있습니다.

    @include('view.name', ['some' => 'data'])

> **주의:** 블레이드 뷰에서 `__DIR__`와 `__FILE__` 를 사용하지 마십시오. 이를 사용하면 캐시된 뷰의 경로가 반환됩니다.

#### 컬렉션을 뷰에서 렌더링하기

블레이드의 `@each` 지시오을 사용하면 반복문을 하나의 줄로 구성할 수 있습니다:

    @each('view.name', $jobs, 'job')

첫번째 인자는 배열이나 컬렉션의 각 요소를 렌더링하기 위한 부분적 뷰의 이름입니다. 두번째 인자는 반복 처리하는 배열이나 컬렉션이며 세번째 인수는 뷰에서의 반복값이 대입되는 변수의 이름입니다. 예를 들어 `jobs` 배열을 반복 처리하려면 보통 부분적 뷰에서 각 과제를 `job` 변수로 접근해야 할 것입니다. 현재 반복에서의 키값은 부분적 뷰에서 `key` 변수로 접근할 수 있습니다.

또한 `@each` 지시어로 네번째 인수를 전달할 수도 있습니다. 이 인자는 특정 배열이 비었을 경우 렌더링될 뷰를 결정합니다.

    @each('view.name', $jobs, 'job', 'view.empty')

#### 주석

블레이드는 또한 뷰에 주석을 정의할 수 있습니다. 하지만 HTML 주석과는 다르게 블레이드 주석은 애플리케이션이 반환하는 HTML에 포함되어 있지 않습니다:

    {{-- This comment will not be present in the rendered HTML --}}

<a name="stacks"></a>
## 스택

블레이드는 또한 다른 뷰 또는 레이아웃에서 렌더링 할 수 있도록 이름이 지정된 스택에 푸시 할 수 있습니다:

    @push('scripts')
        <script src="/example.js"></script>
    @endpush

필요한 경우 동일한 스택에 여러번 푸시할 수 있습니다. 스택을 렌더링 하려면, `@stack` 문법을 사용하면 됩니다:

    <head>
        <!-- Head Contents -->

        @stack('scripts')
    </head>

<a name="service-injection"></a>
## 서비스 주입하기

`@inject` 지시어는 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)에서 서비스를 반환하는 데에 사용될 수 있습니다. `@inject`에 전달된 첫번째 인자는 서비스가 위치할 변수의 이름이며 두번째 인수는 의존성을 해결하려는 클래스/인터페이스의 이름입니다:

    @inject('metrics', 'App\Services\MetricsService')

    <div>
        Monthly Revenue: {{ $metrics->monthlyRevenue() }}.
    </div>

<a name="extending-blade"></a>
## 블레이드 기능 확장하기

블레이드는 사용자가 고유의 지시어을 정의할 수 있습니다. `directive` 메소드를 이용해 지시어을 등록할 수 있습니다. 블레이드 컴파일러가 지시어을 발견하면 인자에 지정한 콜백 함수를 호출합니다.

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

예제에서 볼 수 있듯이 이 지시어에서 라라벨의 `with` 헬퍼 함수가 사용되었습니다. `with` 헬퍼는 단순히 지정된 객체/값을 반환하며 편리한 메소드 체인을 제공합니다. 이 지시어가 생성하는 최종 PHP는 다음과 같습니다:

    <?php echo with($var)->format('m/d/Y H:i'); ?>

블레이드 지시어 로직을 수정한 뒤에는, 블레이드 뷰 캐시를 삭제할 필요가 있습니다. 블레이드 뷰의 캐시는 `view:clear` 아티즌 명령어를 사용하여 제거할 수 있습니다.
