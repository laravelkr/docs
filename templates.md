# 템플릿(Templates)

- [블레이드 템플릿](#blade-templating)
- [기타 블레이드 컨트롤 구조](#other-blade-control-structures)

<a name="blade-templating"></a>
## 블레이드 템플릿

블레이드는 라라벨에서 제공하는 간단하지만 강력한 템플릿 엔진입니다. 컨트롤러 레이아웃과는 다르게 블레이드는 _템플릿 상속_과 _섹션_을 통해서 처리됩니다. 모든 블레이드 템플릿 파일은 `.blade.php` 확장자를 가져야 합니다.

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

#### 블레이드 레이아웃 사용하기

	@extends('layouts.master')

	@section('title', 'Page Title')

	@section('sidebar')
		@parent

		<p>This is appended to the master sidebar.</p>
	@stop

	@section('content')
		<p>This is my body content.</p>
	@stop

블레이드 레이아웃을 `extend`하면 레이아웃의 섹션영역을 재지정하게 됩니다. 자식 뷰에서 부모의 콘텐츠를 포함하려면 `@@ parent` 지시문을 섹션에서 사용하면 됩니다. 주로 사이드 바 또는 하단 글 레이아웃에 내용을 추가할 때 유용합니다.

때로는 섹션을 정의해야 할지 정확하게 판단하지 못할 수도 있는데, 이 경우에는 기본값을 `@yield`에 직접 지정할 수도 있습니다. 다음과 같이 두 번째 인자에 기본값을 지정하면 됩니다.

	@yield('section', 'Default Content')

<!--chak-comment-템플릿(Templates)-블레이드-템플릿-->

<a name="other-blade-control-structures"></a>
## 기타 블레이드 컨트롤 구조

#### 데이터 출력하기

	Hello, {{ $name }}.

	The current UNIX timestamp is {{ time() }}.

#### 데이터가 존재하는지 확인후에 출력하기

때로는 변수를 출력하고자 할 때 해당 변수가 존재하는지 정확하게 알지 못할 때가 있습니다. 이런경우 여러분은 아마 다음처럼 하기를 원할 것입니다:

	{{ isset($name) ? $name : 'Default' }}

하지만 삼항연산자를 작성하는 대신 블레이드에서는 짧게 축약해서 표현할 수 있습니다:

	{{ $name or 'Default' }}

#### 중괄호를 그대로 표시하기

중괄호로 둘러싸인 문자열을 그대로 출력 할 필요가 있는 경우에는 `@`를 앞에 붙이는 것으로, Blade의 처리를 무시 할 수 있습니다:

	@{{ This will not be processed by Blade }}

데이터 escape 처리를 하지 않으려면 다음과 같이 작성하면됩니다:

	Hello, {!! $name !!}.

> ** 주의:** 어플리케이션의 사용자로부터 입력 된 내용을 표시 할 때에는 escape에 대한 주의가 필요합니다. 컨텐츠의 HTML 엔티티를 escape 하기위해 항상 이중 중괄호 표기법을 사용하십시오.

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

#### 하위 뷰 포함하기

	@include('view.name')

포함하게될 하위 뷰에 데이터 배열을 전달할 수 있습니다:

	@include('view.name', ['some' => 'data'])

#### 섹션 재정의하기

섹션 전체를 다시 재정의하려면 `overwrite` 문을 사용하십시오:

	@extends('list.item.container')

	@section('list.item.content')
		<p>This is an item of type {{ $item->type }}</p>
	@overwrite

#### 다국어에 대응 된 행 표시

	@lang('language.line')

	@choice('language.line', 1)

#### 주석

	{{-- This comment will not be in the rendered HTML --}}

<!--chak-comment-템플릿(Templates)-기타-블레이드-컨트롤-구조-->
