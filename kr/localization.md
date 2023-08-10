# Localization
# 지역화 (다국어)

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [Publishing The Language Files](#publishing-the-language-files)
    - [언어 파일 생성하기](#publishing-the-language-files)
    - [Configuring The Locale](#configuring-the-locale)
    - [Locale 설정하기](#configuring-the-locale)
    - [Pluralization Language](#pluralization-language)
    - [복수화 언어](#pluralization-language)
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

> **Note**  
> By default, the Laravel application skeleton does not include the `lang` directory. If you would like to customize Laravel's language files, you may publish them via the `lang:publish` Artisan command.

> **Note**  
> 기본적으로, 라라벨 애플리케이션은 `lang` 디렉토리를 포함하고 있지 않습니다. 라라벨의 언어 파일을 편집하고 싶다면 `lang:publish` 아티즌 명령어를 사용하여 이를 편집 가능하게 만드십시오.

Laravel's localization features provide a convenient way to retrieve strings in various languages, allowing you to easily support multiple languages within your application.

라라벨의 현지화 기능은 다양한 언어로 된 문자열을 검색하는 편리한 방법을 제공하므로 애플리케이션 내에서 여러 언어를 쉽게 지원할 수 있습니다.

Laravel provides two ways to manage translation strings. First, language strings may be stored in files within the application's `lang` directory. Within this directory, there may be subdirectories for each language supported by the application. This is the approach Laravel uses to manage translation strings for built-in Laravel features such as validation error messages:

라라벨은 번역 문자열을 관리하는 두 가지 방법을 제공합니다. 첫째, 언어 문자열은 애플리케이션의 `lang` 디렉토리 내 파일에 저장할 수 있습니다. 이 디렉토리에는 애플리케이션에서 지원하는 각 언어에 대한 하위 디렉토리가 있을 수 있습니다. 이것은 라라벨이 유효성 검사 오류 메시지와 같은 내장 라라벨 기능에 대한 번역 문자열을 관리하는 데 사용하는 접근 방식입니다.

    /lang
        /en
            messages.php
        /es
            messages.php

Or, translation strings may be defined within JSON files that are placed within the `lang` directory. When taking this approach, each language supported by your application would have a corresponding JSON file within this directory. This approach is recommended for applications that have a large number of translatable strings:

또는 `lang` 디렉토리에 있는 JSON 파일 내에 번역 문자열을 정의할 수 있습니다. 이 접근 방식을 사용할 때 애플리케이션에서 지원하는 각 언어는 이 디렉토리 내에 해당 JSON 파일을 갖습니다. 이 접근 방식은 번역 가능한 문자열이 많은 애플리케이션에 권장됩니다.

    /lang
        en.json
        es.json

We'll discuss each approach to managing translation strings within this documentation.

이 문서에서 번역 문자열을 관리하는 각 접근 방식에 대해 설명합니다.

<a name="publishing-the-language-files"></a>
### Publishing The Language Files
### 언어 파일 생성하기

By default, the Laravel application skeleton does not include the `lang` directory. If you would like to customize Laravel's language files or create your own, you should scaffold the `lang` directory via the `lang:publish` Artisan command. The `lang:publish` command will create the `lang` directory in your application and publish the default set of language files used by Laravel:

기본적으로 Laravel 애플리케이션 스켈레톤은 `lang` 디렉토리를 포함하지 않습니다. 라라벨의 언어 파일을 커스터마이징하거나 직접 만들고 싶다면 `lang:publish` Artisan 명령을 통해 `lang` 디렉토리를 스캐폴딩해야 합니다. `lang:publish` 명령은 애플리케이션에 `lang` 디렉토리를 생성하고 Laravel이 사용하는 기본 언어 파일 세트를 생성합니다.

```shell
php artisan lang:publish
```

<a name="configuring-the-locale"></a>
### Configuring The Locale
### 로케일 설정하기

The default language for your application is stored in the `config/app.php` configuration file's `locale` configuration option. You are free to modify this value to suit the needs of your application.

애플리케이션의 기본 언어는 `config/app.php` 설정 파일의 `locale` 설정 옵션에 저장됩니다. 애플리케이션의 요구 사항에 맞게 이 값을 자유롭게 수정할 수 있습니다.

You may modify the default language for a single HTTP request at runtime using the `setLocale` method provided by the `App` facade:

`App` 파사드에서 제공하는 `setLocale` 메소드를 사용하여 런타임 시 단일 HTTP 요청에 대한 기본 언어를 수정할 수 있습니다.

    use Illuminate\Support\Facades\App;

    Route::get('/greeting/{locale}', function (string $locale) {
        if (! in_array($locale, ['en', 'es', 'fr'])) {
            abort(400);
        }

        App::setLocale($locale);

        // ...
    });

You may configure a "fallback language", which will be used when the active language does not contain a given translation string. Like the default language, the fallback language is also configured in the `config/app.php` configuration file:

지정된 언어로 설정된 문자열이 존재하지 않는 경우에 사용할 수 있는 "대체 언어"를 설정 할 수 있습니다. 기본언어와 마찬가지로 대체 언어는 `config/app.php` 설정 파일에서 지정할 수 있습니다.

    'fallback_locale' => 'en',

<a name="determining-the-current-locale"></a>
#### Determining The Current Locale
#### 현재 로케일 확인하기

You may use the `currentLocale` and `isLocale` methods on the `App` facade to determine the current locale or check if the locale is a given value:

`App` 파사드에서 `currentLocale` 및 `isLocale` 메소드를 사용하여 현재 로케일을 결정하거나 로케일이 주어진 값인지 확인할 수 있습니다.

    use Illuminate\Support\Facades\App;

    $locale = App::currentLocale();

    if (App::isLocale('en')) {
        // ...
    }

<a name="pluralization-language"></a>
### Pluralization Language
### 복수화 언어

You may instruct Laravel's "pluralizer", which is used by Eloquent and other portions of the framework to convert singular strings to plural strings, to use a language other than English. This may be accomplished by invoking the `useLanguage` method within the `boot` method of one of your application's service providers. The pluralizer's currently supported languages are: `french`, `norwegian-bokmal`, `portuguese`, `spanish`, and `turkish`:

Eloquent 및 프레임워크의 다른 부분에서 단수 문자열을 복수 문자열로 변환하는 데 사용하는 라라벨의 "pluralizer"에 영어 이외의 언어를 사용하도록 지시할 수 있습니다. 이것은 애플리케이션의 서비스 공급자 중 하나의 `boot` 메서드 내에서 `useLanguage` 메서드를 호출하여 수행할 수 있습니다 . pluralizer가 현재 지원하는 언어는 `프랑스어`, `norwegian-bokmal`, `포르투갈어`, `스페인어`, `터키어` 입니다.

    use Illuminate\Support\Pluralizer;

    /**
     * Bootstrap any application services.
     */
    public function boot(): void
    {
        Pluralizer::useLanguage('spanish');     

        // ...     
    }

> **Warning**  
> If you customize the pluralizer's language, you should explicitly define your Eloquent model's [table names](/docs/{{version}}/eloquent#table-names).

> **Warning**  
> 복수형 언어를 사용자 정의하는 경우 Eloquent 모델의 [테이블 이름](/docs/{{version}}/eloquent#table-names)을 명시적으로 정의해야 합니다 .

<a name="defining-translation-strings"></a>
## Defining Translation Strings
## 다국어 문자값 정의하기

<a name="using-short-keys"></a>
### Using Short Keys
### 짧은 키 사용하기

Typically, translation strings are stored in files within the `lang` directory. Within this directory, there should be a subdirectory for each language supported by your application. This is the approach Laravel uses to manage translation strings for built-in Laravel features such as validation error messages:

일반적으로 번역 문자열은 `lang` 디렉토리 내의 파일에 저장됩니다. 이 디렉토리에는 애플리케이션에서 지원하는 각 언어에 대한 하위 디렉토리가 있어야 합니다. 이것은 라라벨이 유효성 검사 오류 메시지와 같은 내장 라라벨 기능에 대한 번역 문자열을 관리하는 데 사용하는 접근 방식입니다.

    /lang
        /en
            messages.php
        /es
            messages.php

All language files return an array of keyed strings. For example:

모든 언어파일은 문자키 형태의 배열을 반환합니다. 예를 들면:

    <?php

    // lang/en/messages.php

    return [
        'welcome' => 'Welcome to our application!',
    ];

> **Warning**  
> For languages that differ by territory, you should name the language directories according to the ISO 15897. For example, "en_GB" should be used for British English rather than "en-gb".

> **Warning**  
> 지역별로 다른 언어의 경우 ISO 15897에 따라 언어 디렉토리의 이름을 지정해야 합니다. 예를 들어 영국식 영어의 경우 "en-gb"가 아닌 "en_GB"를 사용해야 합니다.

<a name="using-translation-strings-as-keys"></a>
### Using Translation Strings As Keys
### 다국어 문자열을 키로 사용하기

For applications with a large number of translatable strings, defining every string with a "short key" can become confusing when referencing the keys in your views and it is cumbersome to continually invent keys for every translation string supported by your application.

번역 가능한 문자열이 많은 애플리케이션의 경우, 뷰에서 키를 참조할 때 "짧은 키"로 모든 문자열을 정의하는 것이 혼란스러울 수 있으며, 애플리케이션에서 지원하는 모든 번역 문자열에 대해 키를 계속해서 생성하는 것은 번거롭습니다.

For this reason, Laravel also provides support for defining translation strings using the "default" translation of the string as the key. Language files that use translation strings as keys are stored as JSON files in the `lang` directory. For example, if your application has a Spanish translation, you should create a `lang/es.json` file:

이러한 이유로 라라벨은 문자열의 "기본" 번역을 키로 사용하여 번역 문자열 정의에 대한 지원도 제공합니다. 번역 문자열을 키로 사용하는 언어 파일은 `lang` 디렉토리에 JSON 파일로 저장됩니다. 예를 들어 애플리케이션에 스페인어 번역이 있는 경우 `lang/es.json` 파일을 만들어야 합니다.

```json
{
    "I love programming.": "Me encanta programar."
}
```

#### Key / File Conflicts
#### 키 / 파일 충돌

You should not define translation string keys that conflict with other translation filenames. For example, translating `__('Action')` for the "NL" locale while a `nl/action.php` file exists but a `nl.json` file does not exist will result in the translator returning the entire contents of `nl/action.php`.

다른 번역 파일 이름과 충돌하는 번역 문자열 키를 정의하면 안 됩니다. 예를 들어, `nl/action.php` 파일이 존재하지만 `nl.json` 파일이 존재하지 않는  경우, "NL" 로케일에 대해 `__('Action')`을 번역하면 번역기가 `nl/action.php`의 전체 내용을 반환하게 됩니다..

<a name="retrieving-translation-strings"></a>
## Retrieving Translation Strings
## 다국어 문자 조회하기

You may retrieve translation strings from your language files using the `__` helper function. If you are using "short keys" to define your translation strings, you should pass the file that contains the key and the key itself to the `__` function using "dot" syntax. For example, let's retrieve the `welcome` translation string from the `lang/en/messages.php` language file:

`__` 헬퍼 함수를 사용하여 언어 파일에서 번역 문자열을 검색할 수 있습니다. "짧은 키"를 사용하여 번역 문자열을 정의하는 경우 "." 구문을 사용하여 키와 키 자체를 포함하는 파일을 `__` 함수에 전달해야 합니다. 예를 들어 `lang/en/messages.php` 언어 파일에서 `welcome` 번역 문자열을 검색해 보겠습니다.

    echo __('messages.welcome');

If the specified translation string does not exist, the `__` function will return the translation string key. So, using the example above, the `__` function would return `messages.welcome` if the translation string does not exist.

지정된 번역 문자열이 존재하지 않으면 `__` 함수는 번역 문자열 키를 반환합니다. 따라서 위의 예를 사용하면 `__` 함수는 번역 문자열이 존재하지 않는 경우 `messages.welcome`을 반환합니다.

If you are using your [default translation strings as your translation keys](#using-translation-strings-as-keys), you should pass the default translation of your string to the `__` function;

[기본 번역 문자열을 번역 키로 사용](#using-translation-strings-as-keys)을 사용하는 경우 문자열의 기본 번역을 `__` 함수에 전달해야 합니다.

    echo __('I love programming.');

Again, if the translation string does not exist, the `__` function will return the translation string key that it was given.

다시 말하지만, 번역 문자열이 존재하지 않으면 `__` 함수는 제공된 번역 문자열 키를 반환합니다.

If you are using the [Blade templating engine](/docs/{{version}}/blade), you may use the `{{ }}` echo syntax to display the translation string:

[Blade 템플릿 엔진](/docs/{{version}}/blade)을 사용하는 경우 `{{ }}` 에코 구문을 사용하여 번역 문자열을 표시할 수 있습니다.

    {{ __('messages.welcome') }}

<a name="replacing-parameters-in-translation-strings"></a>
### Replacing Parameters In Translation Strings
### 다국어 문자열의 파라미터를 치환하기

If you wish, you may define placeholders in your translation strings. All placeholders are prefixed with a `:`. For example, you may define a welcome message with a placeholder name:

여러분은 다국어 문자에서 일부분을 특정하여 교체하기를(placeholders) 원할 수도 있습니다. 이런 모든 플레이스홀더 들은 `:` 으로 시작합니다. 예를들어 환영 메세지에 이름을 특정하게 표현하기를 원한다면 다음과 같이 하면 됩니다.

    'welcome' => 'Welcome, :name',

To replace the placeholders when retrieving a translation string, you may pass an array of replacements as the second argument to the `__` function:

번역 문자열을 검색할 때 자리 표시자를 교체하려면 대체 할 문자의 배열을 `__` 함수의 두 번째 인수로 전달할 수 있습니다.

    echo __('messages.welcome', ['name' => 'dayle']);

If your placeholder contains all capital letters, or only has its first letter capitalized, the translated value will be capitalized accordingly:

플레이스 홀더가 모두 대문자이거나, 첫번째 문자가 대문자라면, 변환된 값또한 이에 따라 대문자로 표기될것 입니다.

    'welcome' => 'Welcome, :NAME', // Welcome, DAYLE
    'goodbye' => 'Goodbye, :Name', // Goodbye, Dayle

<a name="object-replacement-formatting"></a>
#### Object Replacement Formatting
#### 객체 대체 서식

If you attempt to provide an object as a translation placeholder, the object's `__toString` method will be invoked. The [`__toString`](https://www.php.net/manual/en/language.oop5.magic.php#object.tostring) method is one of PHP's built-in "magic methods". However, sometimes you may not have control over the `__toString` method of a given class, such as when the class that you are interacting with belongs to a third-party library.

객체를 번역 placeholder로 제공하려고 하면 객체의 `__toString` 메서드가 호출됩니다. [`__toString`](https://www.php.net/manual/en/language.oop5.magic.php#object.tostring) 메소드는 PHP의 내장 "매직 메소드" 중 하나입니다. 그러나 때로는 상호 작용하는 클래스가 타사 라이브러리에 속하는 경우와 같이 지정된 클래스의 `__toString` 메서드를 제어하지 못할 수 있습니다.

In these cases, Laravel allows you to register a custom formatting handler for that particular type of object. To accomplish this, you should invoke the translator's `stringable` method. The `stringable` method accepts a closure, which should type-hint the type of object that it is responsible for formatting. Typically, the `stringable` method should be invoked within the `boot` method of your application's `AppServiceProvider` class:

이러한 경우 라라벨은 특정 유형의 객체에 대한 사용자 정의 서식 핸들러를 등록할 수 있도록 합니다. 이를 달성하려면 번역기의 `stringable` 메서드를 호출해야 합니다. `stringable` 메서드는 클로저를 허용하며, 이는 서식 지정을 담당하는 개체 유형을 암시해야 합니다. 일반적으로 `stringable` 메서드는 애플리케이션 `AppServiceProvider` 클래스의 `boot` 메소드 내에서 호출되어야 합니다.

    use Illuminate\Support\Facades\Lang;
    use Money\Money;

    /**
     * Bootstrap any application services.
     */
    public function boot(): void
    {
        Lang::stringable(function (Money $money) {
            return $money->formatTo('en_GB');
        });
    }

<a name="pluralization"></a>
### Pluralization
### 복수 표기

Pluralization is a complex problem, as different languages have a variety of complex rules for pluralization; however, Laravel can help you translate strings differently based on pluralization rules that you define. Using a `|` character, you may distinguish singular and plural forms of a string:

언어마다 복수화에 대한 다양한 복잡한 규칙이 있기 때문에 복수화는 복잡한 문제입니다. 그러나 라라벨은 정의한 복수화 규칙에 따라 문자열을 다르게 번역하는 데 도움을 줄 수 있습니다. `|` 문자를 사용하여 문자열의 단수형과 복수형을 구별할 수 있습니다.

    'apples' => 'There is one apple|There are many apples',

Of course, pluralization is also supported when using [translation strings as keys](#using-translation-strings-as-keys):

물론 [번역 문자열을 키로 사용](using-translation-strings-as-keys)을 사용할 때도 복수화가 지원됩니다.

```json
{
    "There is one apple|There are many apples": "Hay una manzana|Hay muchas manzanas"
}
```

You may even create more complex pluralization rules which specify translation strings for multiple ranges of values:

여러 값 범위에 대한 번역 문자열을 지정하는 더 복잡한 복수화 규칙을 만들 수도 있습니다.

    'apples' => '{0} There are none|[1,19] There are some|[20,*] There are many',

After defining a translation string that has pluralization options, you may use the `trans_choice` function to retrieve the line for a given "count". In this example, since the count is greater than one, the plural form of the translation string is returned:

복수 표기를 가지는 다국어 문자열을 정의한 다음에 여러분은 `trans_choice` 함수를 사용하여 주어진 "개수"에 맞는 다국어 메세지를 표시할 수 있습니다. 예를 들어 하나 이상의 수량을 표시하는 경우에는 다음처럼 하면 됩니다.

    echo trans_choice('messages.apples', 10);

You may also define placeholder attributes in pluralization strings. These placeholders may be replaced by passing an array as the third argument to the `trans_choice` function:

복수형 문자를 위한 플레이스홀더 속성을 정의할 수 있습니다. 이 플레이스홀더는 `trans_choice` 함수에 전달되는 세번째 인자인 배열의 값으로 치환됩니다.

    'minutes_ago' => '{1} :value minute ago|[2,*] :value minutes ago',

    echo trans_choice('time.minutes_ago', 5, ['value' => 5]);

If you would like to display the integer value that was passed to the `trans_choice` function, you may use the built-in `:count` placeholder:

`trans_choice` 함수에 전달된 정수 값을 표시하려면 내장 `:count` 자리 표시자를 사용할 수 있습니다.

    'apples' => '{0} There are none|{1} There is one|[2,*] There are :count',

<a name="overriding-package-language-files"></a>
## Overriding Package Language Files
## 패키지의 언어 파일 재정의하기

Some packages may ship with their own language files. Instead of changing the package's core files to tweak these lines, you may override them by placing files in the `lang/vendor/{package}/{locale}` directory.

몇몇 패키지들은 고유한 언어 파일들을 제공합니다. 출력되는 문구를 수정하기 위해 패키지의 소스를 수정하는 대신에 `lang/vendor/{locale}/{package}` 디렉토리에 파일을 추가하여 다국어를 재정의 할 수 있습니다.

So, for example, if you need to override the English translation strings in `messages.php` for a package named `skyrim/hearthfire`, you should place a language file at: `lang/vendor/hearthfire/en/messages.php`. Within this file, you should only define the translation strings you wish to override. Any translation strings you don't override will still be loaded from the package's original language files.

예를 들어, `skyrim/hearthfire` 패키지의 영어 다국어 문자열을 대체 할 필요가 있다면, `lang/vendor/en/hearthfire/en/messages.php`에 언어 파일을 추가해야 합니다. 이 파일에서 여러분이 재정의하고자 하는 다국어 문자를 지정해야 합니다. 재정의하지 않은 다국어 메세지들은 패키지의 언어 파일의 정의를 그대로 따릅니다.
