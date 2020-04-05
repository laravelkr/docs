# Localization
# 지역화 (다국어)

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [Configuring The Locale](#configuring-the-locale)
    - [Locale-로케일 설정하기](#configuring-the-locale)
- [Defining Translation Strings](#defining-translation-strings)
- [다국어 문자값 정의하기](#defining-translation-strings)
    - [Using Short Keys](#using-short-keys)
    - [짧은 키 사용하기](#using-short-keys)
    - [Using Translation Strings As Keys](#using-translation-strings-as-keys)
    - [다국어 문자열을 키로 사용하기](#using-translation-strings-as-keys)
- [Retrieving Translation Strings](#retrieving-translation-strings)
- [다국어 문자 조회하기](#retrieving-translation-strings)
    - [Replacing Parameters In Translation Strings](#replacing-parameters-in-translation-strings)
    - [다국어 문자열의 파라미터를 치환하기](#replacing-parameters-in-translation-strings)
    - [Pluralization](#pluralization)
    - [복수 표기](#pluralization)
- [Overriding Package Language Files](#overriding-package-language-files)
- [패키지의 언어 파일 재정의하기](#overriding-package-language-files)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel's localization features provide a convenient way to retrieve strings in various languages, allowing you to easily support multiple languages within your application. Language strings are stored in files within the `resources/lang` directory. Within this directory there should be a subdirectory for each language supported by the application:

라라벨의 다국어 기능은 애플리케이션이 다국어에 대응할 수 있도록 다양한 언어로 번역된 문자열을 검색하는 편리한 방법을 제공합니다. 언어 문자열은 `resources/lang` 디렉토리에 저장되어 있습니다. 이 디렉토리 안에서 애플리케이션이 지원하는 언어의 디렉토리가 지정되어야 합니다.

    /resources
        /lang
            /en
                messages.php
            /es
                messages.php

All language files return an array of keyed strings. For example:

모든 언어 파일은 키를 가지고 있는 배열을 반환합니다. 예를들자면:

    <?php

    return [
        'welcome' => 'Welcome to our application'
    ];

> {note} For languages that differ by territory, you should name the language directories according to the ISO 15897. For example, "en_GB" should be used for British English rather than "en-gb".

> {note} 지역별로 다른 언어를 사용하는 경우 ISO 15897에 따라 구분되는 언어의 명칭을 지정해야합니다. 예를 들어 "en-GB" 대신 영국 영어에는 "en_GB"를 사용해야합니다.

<a name="configuring-the-locale"></a>
### Configuring The Locale
### 로케일 설정하기

The default language for your application is stored in the `config/app.php` configuration file. You may modify this value to suit the needs of your application. You may also change the active language at runtime using the `setLocale` method on the `App` facade:

애플리케이션의 기본 언어는 `config/app.php` 설정 파일에 지정되어 있습니다. 여러분은 이 값들을 애플리케이션에 맞게 수정할 수 있습니다. 또한 `App` 파사드의 `setLocale` 메소드를 사용하여 런타임에 활성화될 언어를 변경할 수도 있습니다.

    Route::get('welcome/{locale}', function ($locale) {
        if (! in_array($locale, ['en', 'es', 'fr'])) {
            abort(400);
        }

        App::setLocale($locale);

        //
    });

You may configure a "fallback language", which will be used when the active language does not contain a given translation string. Like the default language, the fallback language is also configured in the `config/app.php` configuration file:

지정된 언어로 설정된 문자열이 존재하지 않는 경우에 사용할 수 있는 "대체 언어"를 설정 할 수 있습니다. 기본언어와 마찬가지로 대체 언어는 `config/app.php` 설정 파일에서 지정할 수 있습니다.

    'fallback_locale' => 'en',

#### Determining The Current Locale
#### 현재 로케일 확인하기

You may use the `getLocale` and `isLocale` methods on the `App` facade to determine the current locale or check if the locale is a given value:

`App` 파사드의 `getLocale` 과 `isLocale` 메소드를 사용하여 현재 로케일이나 주어진 로케일이 사용되는지 확인할 수 있습니다.

    $locale = App::getLocale();

    if (App::isLocale('en')) {
        //
    }

<a name="defining-translation-strings"></a>
## Defining Translation Strings
## 다국어 문자값 정의하기

<a name="using-short-keys"></a>
### Using Short Keys
### 짧은 키 사용하기

Typically, translation strings are stored in files within the `resources/lang` directory. Within this directory there should be a subdirectory for each language supported by the application:

일반적으로 다국어 문자값은 `resources/lang` 디렉토리에 있는 파일로 저장됩니다. 이 디렉토리안에서는 애플리케이션에서 지원할 각 언어가 하위 디렉토리로 존재해야 합니다.

    /resources
        /lang
            /en
                messages.php
            /es
                messages.php

All language files return an array of keyed strings. For example:

모든 언어파일은 문자키 형태의 배열을 반환합니다 예를 들면:

    <?php

    // resources/lang/en/messages.php

    return [
        'welcome' => 'Welcome to our application'
    ];

<a name="using-translation-strings-as-keys"></a>
### Using Translation Strings As Keys
### 다국어 문자열을 키로 사용하기

For applications with heavy translation requirements, defining every string with a "short key" can become quickly confusing when referencing them in your views. For this reason, Laravel also provides support for defining translation strings using the "default" translation of the string as the key.

다국어 변환이 많은 애플리케이션의 경우에는, 모든 문자를 "짧은 키"로 정의하면 뷰에서 참조할때 금방 복잡해 집니다. 따라서 라라벨은 변환 문자를 "기본" 다국어 문자를 키로 사용하여 다국어 변환 문자열을 정의할 수 있도록 지원하고 있습니다.

Translation files that use translation strings as keys are stored as JSON files in the `resources/lang` directory. For example, if your application has a Spanish translation, you should create a `resources/lang/es.json` file:

다국어 문자열을 키로 사용하는 다국어 파일은 `resources/lang` 디렉토리에 JSON파일로 저장됩니다. 예를 들어, 애플리케이션에서 스페인 언어가 필요한경우 `resources/lang/es.json` 파일을 생성해야합니다.

    {
        "I love programming.": "Me encanta programar."
    }

<a name="retrieving-translation-strings"></a>
## Retrieving Translation Strings
## 다국어 문자 조회하기

You may retrieve lines from language files using the `__` helper function. The `__` method accepts the file and key of the translation string as its first argument. For example, let's retrieve the `welcome` translation string from the `resources/lang/messages.php` language file:

`__` 헬퍼 함수를 사용하여 다국어파일을 찾을 수 있습니다. `__` 메소드는 다국어 메세지를 찾기 위해서 파일과 키로 구성된 인자를 전달받습니다. 예를 들어 `resources/lang/messages.php` 파일에서 `welcome` 에 해당하는 다국어 문자를 찾으려면 다음과 같이 하면 됩니다.

    echo __('messages.welcome');

    echo __('I love programming.');

If you are using the [Blade templating engine](/docs/{{version}}/blade), you may use the `{{ }}` syntax to echo the translation string or use the `@lang` directive:

[블레이드 템플릿 엔진](/docs/{{version}}/blade)을 사용한다면, `{{ }}` 구문 안에서 또는 `@lang` 지시어를 통해서 다국어 문자를 출력할 수 있습니다.

    {{ __('messages.welcome') }}

    @lang('messages.welcome')

If the specified translation string does not exist, the `__` function will return the translation string key. So, using the example above, the `__` function would return `messages.welcome` if the translation string does not exist.

지정된 언어에 대한 다국어 문자를 찾을 수 없는 경우 `__` 함수는 다국어 문자 키를 반환할 것입니다. 따라서, 위의 예제에서 다국어 메세지가 없을 경우에 `__` 함수는 `message.welcome` 라는 값을 반환할 것입니다.

> {note} The `@lang` directive does not escape any output. You are **fully responsible** for escaping your own output when using this directive.

> {note} `@lang` 지시어는 아웃풋에 어떠한 escape 처리도 해주지 않습니다. 이 지시어을 사용할 때 사용자는 자신의 출력을 escape 처리 할 **책임**이 있습니다.

<a name="replacing-parameters-in-translation-strings"></a>
### Replacing Parameters In Translation Strings
### 다국어 문자열의 파라미터를 치환하기

If you wish, you may define placeholders in your translation strings. All placeholders are prefixed with a `:`. For example, you may define a welcome message with a placeholder name:

여러분은 다국어 문자에서 일부분을 특정하여 교체하기를(placeholders) 원할 수도 있습니다. 이런 모든 플레이스홀더 들은 `:` 으로 시작합니다. 예를들어 환영 메세지에 이름을 특정하게 표현하기를 원한다면 다음과 같이 하면 됩니다.

    'welcome' => 'Welcome, :name',

To replace the placeholders when retrieving a translation string, pass an array of replacements as the second argument to the `__` function:

다국어 문자열에서 플레이스 홀더를 교체하려면 `__` 함수의 두번째 인자로 교체할 값의 배열을 전달하면 됩니다.

    echo __('messages.welcome', ['name' => 'dayle']);

If your placeholder contains all capital letters, or only has its first letter capitalized, the translated value will be capitalized accordingly:

플레이스 홀더가 모두 대문자이거나, 첫번째 문자가 대문자라면, 변환된 값또한 이에 따라 대문자로 표기될것 입니다.

    'welcome' => 'Welcome, :NAME', // Welcome, DAYLE
    'goodbye' => 'Goodbye, :Name', // Goodbye, Dayle


<a name="pluralization"></a>
### Pluralization
### 복수 표기

Pluralization is a complex problem, as different languages have a variety of complex rules for pluralization. By using a "pipe" character, you may distinguish singular and plural forms of a string:

각기 다른 언어에서 단수와 복수의 표기는 복잡하고 다양한 것처럼, 복수 표기는 아주 복잡한 문제입니다. "파이프" 문자를 사용하여 여러분은 단수 문자열과 복수형의 문자열을 나눌 수 있습니다.

    'apples' => 'There is one apple|There are many apples',

You may even create more complex pluralization rules which specify translation strings for multiple number ranges:

많은 범위의 복수 표현을 가지고 있는 언어에서 보다 복잡한 복수 표현이 필요하더라도 편리하게 규칙을 만들 수 있습니다.

    'apples' => '{0} There are none|[1,19] There are some|[20,*] There are many',

After defining a translation string that has pluralization options, you may use the `trans_choice` function to retrieve the line for a given "count". In this example, since the count is greater than one, the plural form of the translation string is returned:

복수 표기를 가지는 다국어 문자열을 정의한 다음에 여러분은 `trans_choice` 함수를 사용하여 주어진 "개수"에 맞는 다국어 메세지를 표시할 수 있습니다. 예를 들어 하나 이상의 수량을 표시하는 경우에는 다음처럼 하면 됩니다.

    echo trans_choice('messages.apples', 10);

You may also define placeholder attributes in pluralization strings. These placeholders may be replaced by passing an array as the third argument to the `trans_choice` function:

복수형 문자를 위한 플레이스홀더 속성을 정의할 수 있습니다. 이 플레이스홀더는 `trans_choice` 함수에 전달되는 세번째 인자인 배열의 값으로 치환됩니다.

    'minutes_ago' => '{1} :value minute ago|[2,*] :value minutes ago',

    echo trans_choice('time.minutes_ago', 5, ['value' => 5]);

If you would like to display the integer value that was passed to the `trans_choice` function, you may use the `:count` placeholder:

`trans_choice` 함수에 전달 된 정수 값을 표시하려면 placeholder `:count` 를 사용할 수 있습니다.

    'apples' => '{0} There are none|{1} There is one|[2,*] There are :count',

<a name="overriding-package-language-files"></a>
## Overriding Package Language Files
## 패키지의 언어 파일 재정의하기

Some packages may ship with their own language files. Instead of changing the package's core files to tweak these lines, you may override them by placing files in the `resources/lang/vendor/{package}/{locale}` directory.

몇몇 패키지들은 고유한 언어 파일들을 제공합니다. 출력되는 문구를 수정하기 위해 패키지의 소스를 수정하는 대신에 `resources/lang/vendor/{locale}/{package}` 디렉토리에 파일을 추가하여 다국어를 재정의 할 수 있습니다.

So, for example, if you need to override the English translation strings in `messages.php` for a package named `skyrim/hearthfire`, you should place a language file at: `resources/lang/vendor/hearthfire/en/messages.php`. Within this file, you should only define the translation strings you wish to override. Any translation strings you don't override will still be loaded from the package's original language files.

예를 들어, `skyrim/hearthfire` 패키지의 영어 다국어 문자열을 대체 할 필요가 있다면, `resources/lang/vendor/en/hearthfire/en/messages.php`에 언어 파일을 추가해야 합니다. 이 파일에서 여러분이 재정의하고자 하는 다국어 문자를 지정해야 합니다. 재정의하지 않은 다국어 메세지들은 패키지의 언어 파일의 정의를 그대로 따릅니다.
