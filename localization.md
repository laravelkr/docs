# 지역화 (다국어)

- [시작하기](#introduction)
- [다국어 언어값 가져오기](#retrieving-language-lines)
    - [언어 라인안에서 파라미터 교체하기](#replacing-parameters-in-language-lines)
    - [복수 단어 표기](#pluralization)
- [패키지의 언어 파일 재정의하기](#overriding-package-language-files)

<a name="introduction"></a>
## 시작하기

라라벨의 다국어 기능은 애플리케이션이 다국어에 대응 할 수 있도록 다양한 언어로 번역된 문자열을 검색하는 편리한 방법을 제공합니다. 언어 문자열은 `resources/lang` 디렉토리에 저장되어 있습니다. 이 디렉토리 안에서 애플리케이션이 지원하는 언어의 디렉토리가 지정되어야 합니다.

    /resources
        /lang
            /en
                messages.php
            /es
                messages.php

모든 언어 파일은 키를 가지고 있는 배열을 반환합니다. 예를들자면:

    <?php

    return [
        'welcome' => 'Welcome to our application'
    ];

### 로케일 설정하기

애플리케이션의 기본 언어는 `config/app.php` 설정 파일에 지정되어 있습니다. 물론 여러분은 이 값들을 애플리케이션에 맞게 수정할 수 있습니다. 또한 `App` 파사드의 `setLocale` 메소드를 사용하여 런타임에 활성화될 언어를 변경할 수도 있습니다:

    Route::get('welcome/{locale}', function ($locale) {
        App::setLocale($locale);

        //
    });

지정된 언어로 설정된 문자열이 존재하지 않는 경우에 사용할 수 있는 "대체 언어"를 설정 할 수 있습니다. 기본언어와 마찬가지로 대체 언어는 `config/app.php` 설정 파일에서 지정할 수 있습니다:

    'fallback_locale' => 'en',

#### 현재 로케일 확인하기

`App` 파사드의 `getLocale` 과 `isLocale` 메소드를 사용하여 현재 로케일이나 주어진 로케일이 사용되는지 확인할 수 있습니다:

    $locale = App::getLocale();

    if (App::isLocale('en')) {
        //
    }

<a name="retrieving-language-lines"></a>
## 다국어 언어값 가져오기

`trans` 헬퍼 함수를 사용하여 다국어파일을 찾을 수 있습니다. `trans` 메소드는 다국 메세지를 찾기 위해서 파일과 키로 구성된 인자를 전달받습니다. 예를 들어 `resources/lang/messages.php` 파일에서 `welcome` 에 해당하는 다국어 값을 찾으려면 다음과 같이 하면 됩니다:

    echo trans('messages.welcome');

당연하게도, [블레이드 템플릿 엔진](/docs/{{version}}/blade)을 사용한다면 `{{ }}` 구문 안에서 또는 `@lang` 지시어를 통해서 다국어를 사용할 수 있습니다:

    {{ trans('messages.welcome') }}

    @lang('messages.welcome')

지정된 언어에 대한 다국어 메세지를 찾을 수 없는 경우 `trans` 함수는 다국어 키를 반환할 것입니다. 따라서, 위의 예제에서 다국어 메세지가 없을 경우에 `trans` 함수는 `message.welcome` 라는 값을 반환할 것입니다.

<a name="replacing-parameters-in-language-lines"></a>
### 다국어 메세지의 일부 교체하기

여러분은 다국어 메세지에서 일부분을 특정하게 표현하기를(place-holder) 원할 수도 있습니다. 이런 모든 플레이스홀더 들은 `:` 으로 시작합니다. 예를들어 환영 메세지에 이름을 특정하게 표현하기를 원한다면 다음과 같이 하면 됩니다:

    'welcome' => 'Welcome, :name',

다국어 메세지에서 플레이스 홀더를 교체하려면 `trans` 함수의 두번째 인자로 교체할 값의 배열을 전달하면 됩니다:

    echo trans('messages.welcome', ['name' => 'dayle']);

플레이스 홀더가 모두 대문자이거나, 첫번째 문자가 대문자라면, 변환된 값또한 이에 따라 대문자로 표기될것 입니다:

    'welcome' => 'Welcome, :NAME', // Welcome, DAYLE
    'goodbye' => 'Goodbye, :Name', // Goodbye, Dayle


<a name="pluralization"></a>
### 복수 표기

각기 다른 언어에서 단수와 복수의 표기는 복잡하고 다양한 것처럼, 복수 표기는 아주 복잡한 문제입니다. "파이프" 문자를 사용하여 여러분은 단수 문자열과 복수형의 문자열을 나눌 수 있습니다:

    'apples' => 'There is one apple|There are many apples',

복수 표기를 가지는 다국어 언어값을 정의한 다음에 여러분은 `trans_choice` 함수를 사용하여 주어진 "갯수"에 맞는 다국어 메세지를 표시할 수 있습니다. 예를 들어 하나 이상인 경우에는 다음처럼 표시하면 됩니다:

    echo trans_choice('messages.apples', 10);

라라벨 Translator는 Symfony Translation 컴포넌트를 사용하고 있기 때문에, 많은 범위의 복수 표현을 가지고 있는 언어에서 보다 복잡한 복수 표기라도 편리하게 규칙을 만들 수 있습니다:

    'apples' => '{0} There are none|[1,19] There are some|[20,Inf] There are many',

<a name="overriding-package-language-files"></a>
## 패키지의 언어 파일 재정의하기

몇몇 패키지들은 고유한 언어 파일들을 제공합니다. 출력되는 문구를 수정하기 위해 패키지의 소스를 수정하는 대신에 `resources/lang/vendor/{locale}/{package}` 디렉토리에 파일을 추가하여 다국어를 재정의 할 수 있습니다.

예를 들어, `skyrim/hearthfire` 패키지의 영어 다국어 메세지들을 대체 할 필요가 있다면, `resources/lang/vendor/en/hearthfire/en/messages.php`에 언어 파일을 추가해야 합니다. 이 파일에서 여러분이 재정의하고자 하는 언어 라인을 지정해야 합니다. 재정의하지 않은 다국어 메세지들은 패키지의 언어 파일의 정의를 그대로 따릅니다.
