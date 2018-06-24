# Templates 템플릿

- [Blade Templating 블레이드 템플릿](#blade-templating)
- [Other Blade Control Structures 기타 블레이드 컨트롤 구조](#other-blade-control-structures)

<a name="blade-templating"></a>
## Blade Templating 블레이드 템플릿

Blade is a simple, yet powerful templating engine provided with Laravel. 블레이드는 라라벨에서 제공하는 간단하지만 강력한 템플릿 엔진입니다. Unlike controller layouts, Blade is driven by _template inheritance_ and _sections_. 컨트롤러 레이아웃과는 다르게 블레이드는 _템플릿 상속_과 _섹션_을 통해서 처리됩니다. All Blade templates should use the `.blade.php` extension. 모든 블레이드 템플릿 파일은 `.blade.php` 확장자를 가져야 합니다. 

#### Defining A Blade Layout
#### 블레이드 레이아웃 정의하기 

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

#### Using A Blade Layout
#### 블레이드 레이아웃 사용하기 

	@extends('layouts.master')
	
	@section('title', 'Page Title')

	@section('sidebar')
		@@parent

		<p>This is appended to the master sidebar.</p>
	@stop

	@section('content')
		<p>This is my body content.</p>
	@stop

Note that views which `extend` a Blade layout simply override sections from the layout. 블레이드 레이아웃을 `extend`하면 레이아웃의 섹션영역을 재지정하게 됩니다. Content of the layout can be included in a child view using the `@@parent` directive in a section, allowing you to append to the contents of a layout section such as a sidebar or footer. 자식 뷰에서 부모의 콘텐츠를 포함하려면 `@@ parent` 지시문을 섹션에서 사용하면 됩니다. 주로 사이드 바 또는 하단 글 레이아웃에 내용을 추가 할 때 유용합니다.

Sometimes, such as when you are not sure if a section has been defined, you may wish to pass a default value to the `@yield` directive. 때로는 섹션을 정의해야할지 정확하게 판단하지 못할 수도 있는데, 이 경우에는 기본값을 `@yield`에 직접 지정할 수도 있습니다. You may pass the default value as the second argument: 다음과 같이 두 번째 인자에 기본값을 지정하면 됩니다.

	@yield('section', 'Default Content')

<a name="other-blade-control-structures"></a>
## Other Blade Control Structures
## 기타 블레이드 컨트롤 구조

#### Echoing Data
#### 데이타 출력하기

	Hello, {{ $name }}.

	The current UNIX timestamp is {{ time() }}.

#### Echoing Data After Checking For Existence
#### 데이터가 존재하는지 확인후에 출력하기

Sometimes you may wish to echo a variable, but you aren't sure if the variable has been set. 때로는 변수를 출력하고자 할 때 해당 변수가 존재하는지 정확하게 알지 못할 때가 있습니다. Basically, you want to do this: 이런경우 여러분은 아마 다음처럼 하기를 원할 것입니다. 

	{{ isset($name) ? $name : 'Default' }}

However, instead of writing a ternary statement, Blade allows you to use the following convenient short-cut: 하지만 삼항연산자를 작성하는 대신 블레이드에서는 짧게 축약해서 표현할 수 있습니다. 

	{{ $name or 'Default' }}

#### Displaying Raw Text With Curly Braces
#### 중괄호를 그대로 표시하기

If you need to display a string that is wrapped in curly braces, you may escape the Blade behavior by prefixing your text with an `@` symbol: 
중괄호로 둘러싸인 문자열을 그대로 출력 할 필요가 있는 경우에는 `@` 를 앞에 붙이는 것으로, Blade의 처리를 무시 할 수 있습니다.

	@{{ This will not be processed by Blade }}

If you don't want the data to be escaped, you may use the following syntax: 데이터 escape 처리를 하지 않으려면 다음과 같이 작성하면됩니다. 

	Hello, {!! $name !!}.

> **Note 주의:** Be very careful when echoing content that is supplied by users of your application. 애플리케이션의 사용자로부터 입력 된 내용을 표시 할 때에는 escape에 대한 주의가 필요합니다. Always use the double curly brace syntax to escape any HTML entities in the content. 컨텐츠의 HTML 엔티티를 escape 하기위해 항상 이중 중괄호 표기법을 사용하십시오.

#### If Statements
#### 조건문

	@if (count($records) === 1)
		I have one record!
	@elseif (count($records) > 1)
		I have multiple records!
	@else
		I don't have any records!
	@endif

	@unless (Auth::check())
		You are not signed in.
	@endunless

#### Loops
#### 반복

	@for ($i = 0; $i < 10; $i++)
		The current value is {{ $i }}
	@endfor

	@foreach ($users as $user)
		<p>This is user {{ $user->id }}</p>
	@endforeach

	@forelse($users as $user)
	  	<li>{{ $user->name }}</li>
	@empty
	  	<p>No users</p>
	@endforelse

	@while (true)
		<p>I'm looping forever.</p>
	@endwhile

#### Including Sub-Views
#### 하위 뷰 포함하기

	@include('view.name')

You may also pass an array of data to the included view: 포함하게될 하위 뷰에 데이터 배열을 전달할 수 있습니다.

	@include('view.name', ['some' => 'data'])

#### Overwriting Sections
#### 섹션 재정의하기 

To overwrite a section entirely, you may use the `overwrite` statement: 섹션 전체를 다시 재정의하려면 `overwrite` 문을 사용하십시오

	@extends('list.item.container')

	@section('list.item.content')
		<p>This is an item of type {{ $item->type }}</p>
	@overwrite

#### Displaying Language Lines
#### 다국어에 대응 된 행 표시

	@lang('language.line')

	@choice('language.line', 1)

#### Comments
#### 주석

	{{-- This comment will not be in the rendered HTML --}}
