# 지역화 (다국어)

- [시작하기](#introduction)
    - [Locale 설정하기](#configuring-the-locale)
- [다국어 문자값 정의하기](#defining-translation-strings)
    - [짧은 키 사용하기](#using-short-keys)
    - [다국어 문자열을 키로 사용하기](#using-translation-strings-as-keys)
- [다국어 문자 조회하기](#retrieving-translation-strings)
    - [다국어 문자열의 파라미터를 치환하기](#replacing-parameters-in-translation-strings)
    - [복수 표기](#pluralization)
- [패키지의 언어 파일 재정의하기](#overriding-package-language-files)

<a name="introduction"></a>
## 시작하기

라라벨의 현지화 기능은 다양한 언어로 된 문자열을 검색하는 편리한 방법을 제공하므로 애플리케이션 내에서 여러 언어를 쉽게 지원할 수 있습니다.

라라벨은 번역 문자열을 관리하는 두 가지 방법을 제공합니다. 첫째, 언어 문자열은 `resources/lang` 디렉토리 내의 파일에 저장할 수 있습니다. 이 디렉토리에는 애플리케이션에서 지원하는 각 언어에 대한 하위 디렉토리가 있을 수 있습니다. 이것은 라라벨이 유효성 검사 오류 메시지와 같은 내장 라라벨 기능에 대한 번역 문자열을 관리하는 데 사용하는 접근 방식입니다.

    /resources
        /lang
            /en
                messages.php
            /es
                messages.php

또는 `resources/lang` 디렉토리에 있는 JSON 파일 내에 번역 문자열을 정의할 수 있습니다. 이 접근 방식을 사용할 때 애플리케이션에서 지원하는 각 언어는 이 디렉토리 내에 해당 JSON 파일을 갖습니다. 이 접근 방식은 번역 가능한 문자열이 많은 애플리케이션에 권장됩니다.

    /resources
        /lang
            en.json
            es.json

이 문서에서 번역 문자열을 관리하는 각 접근 방식에 대해 설명합니다.

<a name="configuring-the-locale"></a>
### 로케일 설정하기

애플리케이션의 기본 언어는 `config/app.php` 설정 파일의 `locale` 설정 옵션에 저장됩니다. 애플리케이션의 요구 사항에 맞게 이 값을 자유롭게 수정할 수 있습니다.

`App` 파사드에서 제공하는 `setLocale` 메소드를 사용하여 런타임 시 단일 HTTP 요청에 대한 기본 언어를 수정할 수 있습니다.

    use Illuminate\Support\Facades\App;

    Route::get('/greeting/{locale}', function ($locale) {
        if (! in_array($locale, ['en', 'es', 'fr'])) {
            abort(400);
        }

        App::setLocale($locale);

        //
    });

지정된 언어로 설정된 문자열이 존재하지 않는 경우에 사용할 수 있는 "대체 언어"를 설정 할 수 있습니다. 기본언어와 마찬가지로 대체 언어는 `config/app.php` 설정 파일에서 지정할 수 있습니다.

    'fallback_locale' => 'en',

<a name="determining-the-current-locale"></a>
#### 현재 로케일 확인하기

`App` 파사드에서 `currentLocale` 및 `isLocale` 메소드를 사용하여 현재 로케일을 결정하거나 로케일이 주어진 값인지 확인할 수 있습니다.

    use Illuminate\Support\Facades\App;

    $locale = App::currentLocale();

    if (App::isLocale('en')) {
        //
    }

<a name="defining-translation-strings"></a>
## 다국어 문자값 정의하기

<a name="using-short-keys"></a>
### 짧은 키 사용하기

일반적으로 번역 문자열은 `resources/lang` 디렉토리 내의 파일에 저장됩니다. 이 디렉토리에는 애플리케이션에서 지원하는 각 언어에 대한 하위 디렉토리가 있어야 합니다. 이것은 라라벨이 유효성 검사 오류 메시지와 같은 내장 라라벨 기능에 대한 번역 문자열을 관리하는 데 사용하는 접근 방식입니다.

    /resources
        /lang
            /en
                messages.php
            /es
                messages.php

모든 언어파일은 문자키 형태의 배열을 반환합니다. 예를 들면:

    <?php

    // resources/lang/en/messages.php

    return [
        'welcome' => 'Welcome to our application!',
    ];

> {note} 지역별로 다른 언어의 경우 ISO 15897에 따라 언어 디렉토리의 이름을 지정해야 합니다. 예를 들어 영국식 영어의 경우 "en-gb"가 아닌 "en_GB"를 사용해야 합니다.

<a name="using-translation-strings-as-keys"></a>
### 다국어 문자열을 키로 사용하기

번역 가능한 문자열이 많은 애플리케이션의 경우, 뷰에서 키를 참조할 때 "짧은 키"로 모든 문자열을 정의하는 것이 혼란스러울 수 있으며, 애플리케이션에서 지원하는 모든 번역 문자열에 대해 키를 계속해서 생성하는 것은 번거롭습니다.

이러한 이유로 라라벨은 문자열의 "기본" 번역을 키로 사용하여 번역 문자열 정의에 대한 지원도 제공합니다. 번역 문자열을 키로 사용하는 번역 파일은 `resources/lang` 디렉토리에 JSON 파일로 저장됩니다. 예를 들어 애플리케이션에 스페인어 번역이 있는 경우 `resources/lang/es.json` 파일을 만들어야 합니다.

```js
{
    "I love programming.": "Me encanta programar."
}
```

#### 키 / 파일 충돌

다른 번역 파일 이름과 충돌하는 번역 문자열 키를 정의하면 안 됩니다. 예를 들어, `nl/action.php` 파일이 존재하지만 `nl.json` 파일이 존재하지 않는  경우, "NL" 로케일에 대해 `__('Action')`을 번역하면 번역기가 `nl/action.php`의 내용을 반환하게 됩니다..

<a name="retrieving-translation-strings"></a>
## 다국어 문자 조회하기

`__` 헬퍼 함수를 사용하여 언어 파일에서 번역 문자열을 검색할 수 있습니다. "짧은 키"를 사용하여 번역 문자열을 정의하는 경우 "." 구문을 사용하여 키와 키 자체를 포함하는 파일을 `__` 함수에 전달해야 합니다. 예를 들어 `resources/lang/en/messages.php` 언어 파일에서 `welcome` 번역 문자열을 검색해 보겠습니다.

    echo __('messages.welcome');

지정된 번역 문자열이 존재하지 않으면 `__` 함수는 번역 문자열 키를 반환합니다. 따라서 위의 예를 사용하면 `__` 함수는 번역 문자열이 존재하지 않는 경우 `messages.welcome`을 반환합니다.

[기본 번역 문자열을 번역 키로 사용](#using-translation-strings-as-keys)을 사용하는 경우 문자열의 기본 번역을 `__` 함수에 전달해야 합니다.

    echo __('I love programming.');

다시 말하지만, 번역 문자열이 존재하지 않으면 `__` 함수는 제공된 번역 문자열 키를 반환합니다.

[Blade 템플릿 엔진](/docs/{{version}}/blade)을 사용하는 경우 `{{ }}` 에코 구문을 사용하여 번역 문자열을 표시할 수 있습니다.

    {{ __('messages.welcome') }}

<a name="replacing-parameters-in-translation-strings"></a>
### 다국어 문자열의 파라미터를 치환하기

여러분은 다국어 문자에서 일부분을 특정하여 교체하기를(placeholders) 원할 수도 있습니다. 이런 모든 플레이스홀더 들은 `:` 으로 시작합니다. 예를들어 환영 메세지에 이름을 특정하게 표현하기를 원한다면 다음과 같이 하면 됩니다.

    'welcome' => 'Welcome, :name',

번역 문자열을 검색할 때 자리 표시자를 교체하려면 대체 할 문자의 배열을 `__` 함수의 두 번째 인수로 전달할 수 있습니다.

    echo __('messages.welcome', ['name' => 'dayle']);

플레이스 홀더가 모두 대문자이거나, 첫번째 문자가 대문자라면, 변환된 값또한 이에 따라 대문자로 표기될것 입니다.

    'welcome' => 'Welcome, :NAME', // Welcome, DAYLE
    'goodbye' => 'Goodbye, :Name', // Goodbye, Dayle

<a name="pluralization"></a>
### 복수 표기

언어마다 복수화에 대한 다양한 복잡한 규칙이 있기 때문에 복수화는 복잡한 문제입니다. 그러나 라라벨은 정의한 복수화 규칙에 따라 문자열을 다르게 번역하는 데 도움을 줄 수 있습니다. `|` 문자를 사용하여 문자열의 단수형과 복수형을 구별할 수 있습니다.

    'apples' => 'There is one apple|There are many apples',

물론 [번역 문자열을 키로 사용](using-translation-strings-as-keys)을 사용할 때도 복수화가 지원됩니다.

```js
{
    "There is one apple|There are many apples": "Hay una manzana|Hay muchas manzanas"
}
```

여러 값 범위에 대한 번역 문자열을 지정하는 더 복잡한 복수화 규칙을 만들 수도 있습니다.

    'apples' => '{0} There are none|[1,19] There are some|[20,*] There are many',

복수 표기를 가지는 다국어 문자열을 정의한 다음에 여러분은 `trans_choice` 함수를 사용하여 주어진 "개수"에 맞는 다국어 메세지를 표시할 수 있습니다. 예를 들어 하나 이상의 수량을 표시하는 경우에는 다음처럼 하면 됩니다.

    echo trans_choice('messages.apples', 10);

복수형 문자를 위한 플레이스홀더 속성을 정의할 수 있습니다. 이 플레이스홀더는 `trans_choice` 함수에 전달되는 세번째 인자인 배열의 값으로 치환됩니다.

    'minutes_ago' => '{1} :value minute ago|[2,*] :value minutes ago',

    echo trans_choice('time.minutes_ago', 5, ['value' => 5]);

`trans_choice` 함수에 전달된 정수 값을 표시하려면 내장 `:count` 자리 표시자를 사용할 수 있습니다.

    'apples' => '{0} There are none|{1} There is one|[2,*] There are :count',

<a name="overriding-package-language-files"></a>
## 패키지의 언어 파일 재정의하기

몇몇 패키지들은 고유한 언어 파일들을 제공합니다. 출력되는 문구를 수정하기 위해 패키지의 소스를 수정하는 대신에 `resources/lang/vendor/{locale}/{package}` 디렉토리에 파일을 추가하여 다국어를 재정의 할 수 있습니다.

예를 들어, `skyrim/hearthfire` 패키지의 영어 다국어 문자열을 대체 할 필요가 있다면, `resources/lang/vendor/en/hearthfire/en/messages.php`에 언어 파일을 추가해야 합니다. 이 파일에서 여러분이 재정의하고자 하는 다국어 문자를 지정해야 합니다. 재정의하지 않은 다국어 메세지들은 패키지의 언어 파일의 정의를 그대로 따릅니다.
