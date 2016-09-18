# 지역화 (다국어-Localization)

- [소개](#introduction)
- [언어 파일들](#language-files)
- [기본적인 사용법](#basic-usage)
- [복수 단어 표기](#pluralization)
- [검증 메세지 지역화하기](#validation)
- [패키지 언어 파일 재정의하기](#overriding-package-language-files)

<a name="introduction"></a>
## 소개

라라벨의 `Lang` 파사드는 어플리케이션이 다국어에 대응하게 할 수 있도록 다양한 언어로 번역 된 문자열을 검색하는 방법을 제공합니다.

<!--chak-comment-지역화-(다국어-Localization)-소개-->

<a name="language-files"></a>
## 언어 파일들

언어 문자열은 `resources/lang` 디렉토리에 저장되어 있습니다. 이 디렉토리 안에서 어플리케이션이 지원하는 언어의 디렉토리가 지정되어야 합니다.

	/resources
		/lang
			/en
				messages.php
			/es
				messages.php

#### 언어 파일 예제

언어 파일은 문자열 키에 대한 간단한 배열을 반환합니다. 예를 들어:

	<?php

	return [
		'welcome' => 'Welcome to our application'
	];

#### 실행중에 기본 언어 파일 변경하기

어플리케이션의 기본 언어는 `config/app.php` 설정 파일에 지정되어 있습니다. 언제라도 `App::setLocale` 메소드를 사용하여 원하는 언어로 설정을 변경할 수 있습니다:

	App::setLocale('es');

#### 대체 언어 설정하기

지정된 언어로 설정된 문자열이 존재하지 않는 경우에 사용할 수 있는 "대체 언어"를 설정 할 수 있습니다. 기본언어와 같이 대체 언어는 `config/app.php` 설정 파일에서 지정할 수 있습니다:

	'fallback_locale' => 'en',

<!--chak-comment-지역화-(다국어-Localization)-언어-파일들-->

<a name="basic-usage"></a>
## 기본적인 사용법

#### 언어 파일에서 문자열 검색하기

	echo Lang::get('messages.welcome');

`get` 메소드에 전달 되는 문자열의 첫 번째 부분은 언어 파일의 이름이고, 두 번째 부분은 검색하고자 하는 문자열 행의 이름입니다.

> **주의:** 찾고자 하는 언어파일이 존재하지 않는 경우, `get` 메소드는 키 값을 반환합니다.

`trans` 헬퍼함수를 `Lang::get` 메소드의 별칭으로 사용할 수 있습니다.

	echo trans('messages.welcome');

#### 라인의 일부 교체하기

언어 라인에 위치 표시기(place-holders)를 정의 할 수 있습니다 :

	'welcome' => 'Welcome, :name',

그런 뒤에 `Lang:get` 메소드의 두 번째 인자로 교체할 문자열을 전달 할 수 있습니다:

	echo Lang::get('messages.welcome', ['name' => 'Dayle']);

#### 언어파일이 키에 해당하는 문자열 라인을 가지고 있는지 확인하기

	if (Lang::has('messages.welcome'))
	{
		//
	}

<!--chak-comment-지역화-(다국어-Localization)-기본적인-사용법-->

<a name="pluralization"></a>
## 복수 표기

각기 다른 언어에서 단수와 복수의 표기는 복잡하고 또 다양한 것처럼 이런 복수 표기는 복잡한 문제입니다. 언어 파일에서 여러분은 손쉽게 이를 관리할 수 있습니다. 라라벨에서는 “파이프” 문자를 사용하여 단수 문자열과 복수형의 문자열을 나눌 수 있습니다.

	'apples' => 'There is one apple|There are many apples',

이 형식의 라인을 검색하려면 `Lang::choice` 메소드를 사용하면 됩니다.

	echo Lang::choice('messages.apples', 10);

언어를 지정하기 위해 로케일 인수를 전달할 수 있습니다. 예를 들어, 러시아어 (ru)를 이용하고 싶은 경우 :

	echo Lang::choice('товар|товара|товаров', $count, [], 'ru');

라라벨 Translator는 Symfony Translation 컴포넌트를 사용하고 있으므로, 편리하게 복수 표기 규칙을 만들 수 있습니다.

	'apples' => '{0} There are none|[1,19] There are some|[20,Inf] There are many',

<!--chak-comment-지역화-(다국어-Localization)-복수-표기-->


<a name="validation"></a>
## 검증 메세지 지역화하기

검증 오류와 메세지에 대한 다국어 지역화는 <a href="/docs/5.0/validation#localization">검증 문서</a>를 참고하십시오.

<!--chak-comment-지역화-(다국어-Localization)-검증-메세지-지역화하기-->

<a name="overriding-package-language-files"></a>
## 패키지 언어 파일 재정의하기

많은 패키지들이 고유한 언어 라인들을 제공하고 있습니다. 출력되는 문구를 수정하기 위해 패키지의 소스를 수정하는 대신 `resources/lang/packages/{locale}/{package}` 디렉토리에 파일을 추가하여 재정의 할 수 있습니다. 예를 들어, `skyrim/hearthfire` 패키지의 영어 라인을 대체 할 필요가 있다면, `resources/lang/packages/en/hearthfire/messages.php`에 언어 파일을 추가 하면 됩니다. 이 파일에서 여러분이 재정의하고자 하는 언어 라인을 지정하면 됩니다. 재정의하지 않은 언어 라인들은 패키지의 언어 파일의 정의를 그대로 따릅니다.

<!--chak-comment-지역화-(다국어-Localization)-패키지-언어-파일-재정의하기-->
