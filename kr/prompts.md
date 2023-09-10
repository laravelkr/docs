# Prompts
# Prompts

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Installation](#installation)
- [설치하기](#installation)
- [Available Prompts](#available-prompts)
- [사용가능한 프롬프트](#available-prompts)
    - [Text](#text)
    - [Text](#text)
    - [Password](#password)
    - [Password](#password)
    - [Confirm](#confirm)
    - [Confirm](#confirm)
    - [Select](#select)
    - [Select](#select)
    - [Multi-select](#multiselect)
    - [Multi-select](#multiselect)
    - [Suggest](#suggest)
    - [Suggest](#suggest)
    - [Search](#search)
    - [Search](#search)
- [Terminal Considerations](#terminal-considerations)
- [터미널 환경에서의 고려사항](#terminal-considerations)
- [Unsupported Environments & Fallbacks](#fallbacks)

<a name="introduction"></a>
## Introduction

Laravel Prompts is a PHP package for adding beautiful and user-friendly forms to your command-line applications, with browser-like features including placeholder text and validation.

라라벨 Prompts는 CLI 애플리케이션에 사용자 친화적이고 예쁜 입력폼을 추가할 수 있는 PHP 패키지입니다. 이 패키지에는 브라우저와 비슷하게 플레이스홀더와 입력값의 유효성 검증을 수행하는 기능들을 가지고 있습니다.

Laravel Prompts is perfect for accepting user input in your [Artisan console commands](/docs/{{version}}/artisan#writing-commands), but it may also be used in any command-line PHP project.

라라벨 Prompts는 Artisan 콘솔 명령어에서 사용자 입력을 받을 때 사용하기 적합하지만, 다른 모든 커맨드라인 PHP 프로젝트에서도 사용할 수 있습니다.

> **Note**  
> Laravel Prompts supports macOS, Linux, and Windows with WSL. For more information, please see our documentation on [unsupported environments & fallbacks](#fallbacks).

> **Note**  
> 라라벨 Prompt는 macOS, Linux, Window (with WSL)을 지원합니다. 보다 자세한 정보는 [지원하지 않는 환경 & 대안](#fallbacks) 문서를 참고하십시오.

<a name="installation"></a>
## Installation
## 설치하기

Laravel Prompts is already included with the latest release of Laravel.

최신 버전의 라라벨에는 라라벨 Prompt가 이미 포함되어 있습니다.

Laravel Prompts may also be installed in your other PHP projects by using the Composer package manager:

컴포저 패키지 매니저를 사용하여 여러분의 고유한 PHP 프로젝트에 라라벨 Prompt를 설치할 수도 있습니다.

```shell
composer require laravel/prompts
```

<a name="available-prompts"></a>
## Available Prompts
## 사용가능한 프롬프트

<a name="text"></a>
### Text

The `text` function will prompt the user with the given question, accept their input, and then return it:

`text` 함수는 주어진 질문을 프롬프트로 보여주고, 사용자에게 입력을 받아서 반환합니다.

```php
use function Laravel\Prompts\text;

$name = text('What is your name?');
```

You may also include placeholder text and a default value:

플레이스홀더와 기본값을 지정할 수도 있습니다.

```php
$name = text(
    label: 'What is your name?',
    placeholder: 'E.g. Taylor Otwell',
    default: $user?->name
);
```

<a name="text-required"></a>
#### Required Values
#### 필수값

If you require a value to be entered, you may pass the `required` argument:

꼭 입력해야할 필수값이 있다면, `required` 인자를 전달하십시오. 

```php
$name = text(
    label: 'What is your name?',
    required: true
);
```

If you would like to customize the validation message, you may also pass a string:

필수값을 표시하는 메시지를 커스터마이즈하고 싶다면, 문자열을 전달하면 됩니다.

```php
$name = text(
    label: 'What is your name?',
    required: 'Your name is required.'
);
```

<a name="text-validation"></a>
#### Additional Validation
#### 추가적인 입력값 유효성 검증

Finally, if you would like to perform additional validation logic, you may pass a closure to the `validate` argument:

마지막으로, 입력값에 추가적인 유효성 검사 로직을 수행하고 싶다면, `validate` 인자에 클로저를 전달하면 됩니다.

```php
$name = text(
    label: 'What is your name?',
    validate: fn (string $value) => match (true) {
        strlen($value) < 3 => 'The name must be at least 3 characters.',
        strlen($value) > 255 => 'The name must not exceed 255 characters.',
        default => null
    }
);
```

The closure will receive the value that has been entered and may return an error message, or `null` if the validation passes.

클로저는 입력된 값을 전달받아, 유효성 검사가 통과하면 `null`을 반환하고 그렇지 않으면, 오류 메시지를 반환합니다.

<a name="password"></a>
### Password
### 패스워드

The `password` function is similar to the `text` function, but the user's input will be masked as they type in the console. This is useful when asking for sensitive information such as passwords:

`password` 함수는 `text` 함수와 유사하지만, 사용자가 콘솔에서 입력한 값이 마스킹되어 표시됩니다. 이는 비밀번호와 같은 민감한 정보를 입력할 때 유용합니다.

```php
use function Laravel\Prompts\password;

$password = password('What is your password?');
```

You may also include placeholder text:

마찬가지로 플레이스홀더를 지정할 수 있습니다.

```php
$password = password(
    label: 'What is your password?',
    placeholder: 'Minimum 8 characters...'
);
```

<a name="password-required"></a>
#### Required Values
#### 필수값

If you require a value to be entered, you may pass the `required` argument:

꼭 입력해야할 필수값이 있다면, `required` 인자를 전달하십시오.

```php
$name = password(
    label: 'What is your password?',
    required: true
);
```

If you would like to customize the validation message, you may also pass a string:

필수값을 표시하는 메시지를 커스터마이즈하고 싶다면, 문자열을 전달하면 됩니다.

```php
$name = password(
    label: 'What is your password?',
    required: 'The password is required.'
);
```

<a name="password-validation"></a>
#### Additional Validation
#### 추가적인 입력값 유효성 검증

Finally, if you would like to perform additional validation logic, you may pass a closure to the `validate` argument:

마지막으로, 추가적인 유효성 검사 로직을 수행하고 싶다면, `validate` 인자에 클로저를 전달하면 됩니다.

```php
$name = password(
    label: 'What is your password?',
    validate: fn (string $value) => match (true) {
        strlen($value) < 8 => 'The password must be at least 8 characters.',
        default => null
    }
);
```

The closure will receive the value that has been entered and may return an error message, or `null` if the validation passes.

클로저는 입력된 값을 전달받아, 유효성 검사가 통과하면 `null`을 반환하고 그렇지 않으면, 오류 메시지를 반환합니다.

<a name="confirm"></a>
### Confirm
### 확인하기-confirm

If you need to ask the user for a "yes or no" confirmation, you may use the `confirm` function. Users may use the arrow keys or press `y` or `n` to select their response. This function will return either `true` or `false`.

사용자에게 "예 또는 아니오" 확인을 요청해야 하는 경우, `confirm` 함수를 사용할 수 있습니다. 사용자는 화살표 키를 사용하거나 `y` 또는 `n`을 눌러 응답을 선택할 수 있습니다. 이 함수는 `true` 또는 `false`를 반환합니다.

```php
use function Laravel\Prompts\confirm;

$confirmed = confirm('Do you accept the terms?');
```

By default, the "Yes" answer will be pre-selected. However, you may configure the default to be "No" by passing `false` to the `default` argument:

기본적으로 "예" 응답이 미리 선택되어 있습니다. 하지만 `default` 인자에 `false`를 전달하면 기본값을 "아니오"로 설정할 수 있습니다.

```php
$confirmed = confirm(
    label: 'Do you accept the terms?',
    default: false,
);
```

You may also customize the wording used for the "Yes" and "No" labels by passing the `yes` and `no` arguments:

`yes` 및 `no` 인자를 전달하면 "예"와 "아니오" 라벨에 사용되는 문구를 커스터마이즈 할수도 있습니다.

```php
$confirmed = confirm(
    label: 'Do you accept the terms?',
    yes: 'I accept',
    no: 'I decline'
);
```

<a name="confirm-required"></a>
#### Requiring "Yes"
#### "Yes" 를 선택하게 하기

If necessary, you may require your users to select "Yes" by passing the `required` argument:

필요한 경우 confirm 메서드에 `required` 인자를 전달하여 사용자가 "Yes"를 선택하도록 요구할 수 있습니다.

```php
$confirmed = confirm(
    label: 'Do you accept the terms?',
    required: true
);
```

If you would like to customize the validation message, you may also pass a string:

메세지를 커스터마이징 하려면 문자열을 전달하면 됩니다.

```php
$confirmed = confirm(
    label: 'Do you accept the terms?',
    required: 'You must accept the terms to continue.'
);
```

<a name="select"></a>
### Select
### 선택하기

If you need the user to select from a predefined set of choices, you may use the `select` function:

사용자가 미리 정의된 선택항목들 중에 하나를 선택하게 하려면 `select` 함수를 사용하면 됩니다.

```php
use function Laravel\Prompts\select;

$role = select(
    'What role should the user have?',
    ['Member', 'Contributor', 'Owner'],
);
```

You may also specify the default choice by passing the `default` argument:

특정 값을 기본적으로 선택되게 지정하려면 `default` 인자를 전달하면 됩니다.

```php
$role = select(
    label: 'What role should the user have?',
    options: ['Member', 'Contributor', 'Owner'],
    default: 'Owner'
);
```

You may also pass an associative array to the `options` argument to have the selected key returned instead of its value:

`options` 배열에 값 대신에 선택한 옵션에 대한 키가 반환되게 하려면 배열을 키/값 형태로 전달하십시오.

```php
$role = select(
    label: 'What role should the user have?',
    options: [
        'member' => 'Member',
        'contributor' => 'Contributor',
        'owner' => 'Owner'
    ],
    default: 'owner'
);
```

Up to five options will be displayed before the list begins to scroll. You may customize this by passing the `scroll` argument:

선택 옵션의 목록은 스크롤 되기 전에 최대 5개가 표시됩니다. `scroll` 인자를 전달하여 이를 변경할 수 있습니다. 

```php
$role = select(
    label: 'Which category would you like to assign?',
    options: Category::pluck('name', 'id'),
    scroll: 10
);
```

<a name="select-validation"></a>
#### Validation
#### 입력값 유효성 검증

Unlike other prompt functions, the `select` function doesn't accept the `required` argument because it is not possible to select nothing. However, you may pass a closure to the `validate` argument if you need to present an option but prevent it from being selected:

다른 프롬프트 함수와 다르게 `select` 함수는 아무것도 선택하지 않는 것은 불가능하기 때문에 `required` 인자는 지원하지 않습니다. 하지만 선택 옵션을 표시하되 조건에 따라 선택이 불가능하게 만들려면 로직을 클로저로 만들어 `validate` 인자에 전달하면 됩니다. 

```php
$role = select(
    label: 'What role should the user have?'
    options: [
        'member' => 'Member',
        'contributor' => 'Contributor',
        'owner' => 'Owner'
    ],
    validate: fn (string $value) =>
        $value === 'owner' && User::where('role', 'owner')->exists()
            ? 'An owner already exists.'
            : null
    }
)
```

If the `options` argument is an associative array, then the closure will receive the selected key, otherwise it will receive the selected value. The closure may return an error message, or `null` if the validation passes.

`options` 인자가 키/값 형태로 된 배열이라면 클로저에 전달되는 것은 선택된 배열의 키가 됩니다. 배열이 값으로만 구성되어 있다면 선택된 값이 클로저에 전달됩니다. 클로저는 입력을 전달받아, 유효성 검사가 통과하면 `null`을 반환하고 그렇지 않으면, 오류 메시지를 반환합니다.

<a name="multiselect"></a>
### Multi-select
### 다중 선택하기

If you need to the user to be able to select multiple options, you may use the `multiselect` function:

여러개를 선택할 수 있게 하려면 `multiselect` 함수를 사용하면 됩니다.

```php
use function Laravel\Prompts\multiselect;

$permissions = multiselect(
    'What permissions should be assigned?',
    ['Read', 'Create', 'Update', 'Delete']
);
```

You may also specify options that should be pre-selected by passing an array to the `default` argument:

`default` 인자는 미리 선택되어 있어야 하는 값을 지정하는데 사용합니다.

```php
use function Laravel\Prompts\multiselect;

$permissions = multiselect(
    label: 'What permissions should be assigned?',
    options: ['Read', 'Create', 'Update', 'Delete'],
    default: ['Read', 'Create']
);
```

You may also pass an associative array to the `options` argument to return the selected options' keys instead of their values:

`options` 인자가 키/값 형태로 구성되어 있다면 선택된 값이 아닌 선택된 키가 반환됩니다.

```
$permissions = multiselect(
    label: 'What permissions should be assigned?',
    options: [
        'read' => 'Read',
        'create' => 'Create',
        'update' => 'Update',
        'delete' => 'Delete'
    ],
    default: ['read', 'create']
);
```

Up to five options will be displayed before the list begins to scroll. You may customize this by passing the `scroll` argument:

선택 옵션의 목록은 스크롤 되기 전에 최대 5개가 표시됩니다. `scroll` 인자를 전달하여 이를 변경할 수 있습니다. 

```php
$role = multiselect(
    label: 'What categories should be assigned?',
    options: Category::pluck('name', 'id'),
    scroll: 10
);
```

<a name="multiselect-required"></a>
#### Requiring a Value
#### 하나 이상 선택하게 하기

By default, the user may select zero or more options. You may pass the `required` argument to enforce one or more options instead:

기본적으로 multiselect 는 하나도 선택하지 않거나 하나 이상 여러개의 옵션을 선택할 수 있습니다. `required` 인자를 전달하면 무조건 하나 이상의 옵션을 선택하게 할 수 있습니다.

```php
$role = multiselect(
    label: 'What categories should be assigned?',
    options: Category::pluck('name', 'id'),
    required: true,
);
```

<a name="multiselect-validation"></a>
#### Validation
#### 입력값 유효성 검증

You may pass a closure to the `validate` argument if you need to present an option but prevent it from being selected:

선택 옵션을 표시하되 조건에 따라 선택이 불가능하게 만들려면 로직을 클로저로 만들어 `validate` 인자에 전달하면 됩니다.

```
$permissions = select(
    label: 'What permissions should the user have?'
    options: [
        'read' => 'Read',
        'create' => 'Create',
        'update' => 'Update',
        'delete' => 'Delete'
    ],
    validate: fn (array $values) => ! in_array('read', $values)
        ? 'All users require the read permission.'
        : null
);
```

If the `options` argument is an associative array then the closure will receive the selected keys, otherwise it will receive the selected values. The closure may return an error message, or `null` if the validation passes.

`options` 인자가 키/값 형태로 된 배열이라면 클로저에 전달되는 것은 선택된 배열의 키가 됩니다. 배열이 값으로만 구성되어 있다면 선택된 값이 클로저에 전달됩니다. 클로저는 입력을 전달받아, 유효성 검사가 통과하면 `null`을 반환하고 그렇지 않으면, 오류 메시지를 반환합니다.

<a name="suggest"></a>
### Suggest
### 입력 제안

The `suggest` function can be used to provide auto-completion for possible choices. The user can still provide any answer, regardless of the auto-completion hints:

`suggest` 기능을 사용하면 선택 옵션에 대한 자동 완성 기능을 사용할 수 있습니다. 자동 완성으로 입력값을 제안하더라도 사용자는 자동 완성으로 표시되는 내용과 상관없이 원하는 답변을 입력할 수 있습니다.

```php
use function Laravel\Prompts\suggest;

$name = suggest('What is your name?', ['Taylor', 'Dayle']);
```

Alternatively, you may pass a closure as the second argument to the `suggest` function. The closure will be called each time the user types an input character. The closure should accept a string parameter containing the user's input so far and return an array of options for auto-completion:

`suggest` 함수에 두 번째 인수로 클로저를 전달할 수도 있습니다. 이 클로저는 사용자가 문자를 입력할 때마다 호출됩니다. 클로저는 사용자가 입력한 전체 문자열을 인자로 받아 자동 완성을 위한 옵션 배열을 반환해야 합니다.

```php
$name = suggest(
    'What is your name?',
    fn ($value) => collect(['Taylor', 'Dayle'])
        ->filter(fn ($name) => Str::contains($name, $value, ignoreCase: true))
)
```

You may also include placeholder text and a default value:

플레이스홀더 텍스트와 기본값을 지정할 수도 있습니다.

```php
$name = suggest(
    label: 'What is your name?',
    options: ['Taylor', 'Dayle'],
    placeholder: 'E.g. Taylor',
    default: $user?->name
);
```

<a name="suggest-required"></a>
#### Required Values
#### 필수값 

If you require a value to be entered, you may pass the `required` argument:

꼭 입력해야할 필수값이 있다면, `required` 인자를 전달하십시오.

```php
$name = suggest(
    label: 'What is your name?',
    options: ['Taylor', 'Dayle'],
    required: true
);
```

If you would like to customize the validation message, you may also pass a string:

필수값을 표시하는 메시지를 커스터마이즈하고 싶다면, 문자열을 전달하면 됩니다.

```php
$name = suggest(
    label: 'What is your name?',
    options: ['Taylor', 'Dayle'],
    required: 'Your name is required.'
);
```

<a name="suggest-validation"></a>
#### Additional Validation
#### 추가적인 입력값 유효성 검증

Finally, if you would like to perform additional validation logic, you may pass a closure to the `validate` argument:

마지막으로, 추가적인 유효성 검사 로직을 수행하고 싶다면, `validate` 인자에 클로저를 전달하면 됩니다.


```php
$name = suggest(
    label: 'What is your name?',
    options: ['Taylor', 'Dayle'],
    validate: fn (string $value) => match (true) {
        strlen($value) < 3 => 'The name must be at least 3 characters.',
        strlen($value) > 255 => 'The name must not exceed 255 characters.',
        default => null
    }
);
```

The closure will receive the value that has been entered and may return an error message, or `null` if the validation passes.

클로저는 입력된 값을 전달받아, 유효성 검사가 통과하면 `null`을 반환하고 그렇지 않으면, 오류 메시지를 반환합니다.

<a name="search"></a>
### Search
### 검색

If you have a lot of options for the user to select from, the `search` function allows the user to type a search query to filter the results before using the arrow keys to select an option:

사용자가 선택할 수 있는 옵션이 너무 많은 경우라면 `search` 기능을 사용할 수 있습니다. 사용자는 검색 쿼리를 입력하여 필터링된 결과를 확인할 수 있고 화살표 키를 사용하여 옵션을 선택할 수 있습니다.

```php
use function Laravel\Prompts\search;

$id = search(
    'Search for the user that should receive the mail',
    fn (string $value) => strlen($value) > 0
        ? User::where('name', 'like', "%{$value}%")->pluck('name', 'id')->all()
        : []
);
```

The closure will receive the text that has been typed by the user so far and must return an array of options. If you return an associative array then the selected option's key will be returned, otherwise its value will be returned instead.

클로저는 지금까지 사용자가 입력한 텍스트를 전달받아 옵션 배열을 반환해야 합니다. 키/값의 형태의 배열을 반환하면 선택한 옵션에 해당하는 배열 키가 반환되고, 그렇지 않으면 배열의 값이 반환됩니다.

You may also include placeholder text:

플레이스 홀더 텍스트도 지정할 수 있습니다.

```php
$id = search(
    label: 'Search for the user that should receive the mail',
    placeholder: 'E.g. Taylor Otwell',
    options: fn (string $value) => strlen($value) > 0
        ? User::where('name', 'like', "%{$value}%")->pluck('name', 'id')->all()
        : []
);
```

Up to five options will be displayed before the list begins to scroll. You may customize this by passing the `scroll` argument:

선택 옵션의 목록은 스크롤 되기 전에 최대 5개가 표시됩니다. `scroll` 인자를 전달하여 이를 변경할 수 있습니다. 

```php
$id = search(
    label: 'Search for the user that should receive the mail',
    options: fn (string $value) => strlen($value) > 0
        ? User::where('name', 'like', "%{$value}%")->pluck('name', 'id')->all()
        : [],
    scroll: 10
);
```

<a name="search-validation"></a>
#### Validation
#### 입력값 유효성 검증

If you would like to perform additional validation logic, you may pass a closure to the `validate` argument:

입력값에 추가적인 유효성 검사 로직을 수행하고 싶다면, `validate` 인자에 클로저를 전달하면 됩니다.

```php
$id = search(
    label: 'Search for the user that should receive the mail',
    options: fn (string $value) => strlen($value) > 0
        ? User::where('name', 'like', "%{$value}%")->pluck('name', 'id')->all()
        : []
    validate: function (int|string $value) {
        $user = User::findOrFail($value);

        if ($user->opted_out) {
            return 'This user has opted-out of receiving mail.';
        }
    }
);
```

If the `options` closure returns an associative array, then the closure will receive the selected key, otherwise, it will receive the selected value. The closure may return an error message, or `null` if the validation passes.

`options` 클로저가 키/값 형태로 된 배열을 반환하면 입력값 유효성을 검증하는 클로저에 전달되는 것은 선택된 배열의 키가 됩니다. `options` 클로저가 반환하는 배열이 값으로만 구성되어 있다면 선택된 값이 입력값 유효성 검증하는 클로저에 전달됩니다. 유효성 검증 클로저는 입력을 전달받아, 유효성 검사가 통과하면 `null`을 반환하고 그렇지 않으면, 오류 메시지를 반환합니다.

<a name="terminal-considerations"></a>
### Terminal Considerations
### 터미널 환경에서의 고려사항

<a name="terminal-width"></a>
#### Terminal Width
#### 터미널의 너비

If the length of any label, option, or validation message exceeds the number of "columns" in the user's terminal, it will be automatically truncated to fit. Consider minimizing the length of these strings if your users may be using narrower terminals. A typically safe maximum length is 74 characters to support an 80-character terminal.

라벨, 옵션, 입력값 유효성 검사을 위한 메세지를 출력할 때 길이가 사용자 터미널의 "컬럼" 수를 초과하면 자동으로 잘려서 표현됩니다. 사용자가 더 좁은 너비의 터미널을 사용하는 경우를 고려하여 이러한 문자열의 길이를 최소화하는 것이 좋습니다. 일반적으로 안전한 최대 길이는 80자 터미널에서도 잘 보이는 74자입니다.

<a name="terminal-height"></a>
#### Terminal Height
#### 터미널의 높이

For any prompts that accept the `scroll` argument, the configured value will automatically be reduced to fit the height of the user's terminal, including space for a validation message.

`scroll` 인자를 전달받을 수 있는 프롬프트 함수의 경우, 입력값 유효성 검증을 위해서 출력되는 메세지 공간을 포함하여 사용자 터미널 높이에 맞게 자동으로 높이가 조절됩니다.

<a name="fallbacks"></a>
### Unsupported Environments & Fallbacks
### 지원하지 않는 환경 & 대체 환경

Laravel Prompts supports macOS, Linux, and Windows with WSL. Due to limitations in the Windows version of PHP, it is not currently possible to use Laravel Prompts on Windows outside of WSL.

라라벨 Prompt는 macOS, Linux, Window (with WSL)을 지원합니다. PHP의 Windows 버전의 제한 때문에 현재 라라벨 Prompts를 WSL 이 아닌 Windows에서 사용하는 것은 불가능합니다. 

For this reason, Laravel Prompts supports falling back to an alternative implementation such as the [Symfony Console Question Helper](https://symfony.com/doc/current/components/console/helpers/questionhelper.html).

이러한 이유로, 라라벨 Prompt는 [Symfony Console Question Helper](https://symfony.com/doc/current/components/console/helpers/questionhelper.html)와 같은 대체 구현을 지원합니다.

> **Note**  
> When using Laravel Prompts with the Laravel framework, fallbacks for each prompt have been configured for you and will be automatically enabled in unsupported environments.

> **Note**  
> 라라벨 프레임워크에서 라라벨 Prompts를 사용할 때에는 지원되지 않는 환경에서 자동으로 대체 구현이 설정되어 사용할 수 있습니다. 

<a name="fallback-conditions"></a>
#### Fallback Conditions
#### 대체구현 사용 조건

If you are not using Laravel or need to customize when the fallback behavior is used, you may pass a boolean to the `fallbackWhen` static method on the `Prompt` class:

라라벨을 사용하고 있지 않거나 대체구현을 사용하는 조건을 커스터마이징해야 하는 경우, `Prompt` 클래스의 `fallbackWhen` 정적 메서드에 불리언 값을 전달하면 됩니다.

```php
use Laravel\Prompts\Prompt;

Prompt::fallbackWhen(
    ! $input->isInteractive() || windows_os() || app()->runningUnitTests()
);
```

<a name="fallback-behavior"></a>
#### Fallback Behavior
#### 대체구현 동작

If you are not using Laravel or need to customize the fallback behavior, you may pass a closure to the `fallbackUsing` static method on each prompt class:

라라벨을 사용하지 않는 경우, 또는 대체구현 동작을 커스터마이징 하고 싶은 경우에는 `fallbackUsing` 메서드를 사용하면 됩니다. 각 프롬프트 클래스의 `fallbackUsing` 스태틱 메서드에 클로저를 전달하면 됩니다.  

```php
use Laravel\Prompts\TextPrompt;
use Symfony\Component\Console\Question\Question;
use Symfony\Component\Console\Style\SymfonyStyle;

TextPrompt::fallbackUsing(function (TextPrompt $prompt) use ($input, $output) {
    $question = (new Question($prompt->label, $prompt->default ?: null))
        ->setValidator(function ($answer) use ($prompt) {
            if ($prompt->required && $answer === null) {
                throw new \RuntimeException(is_string($prompt->required) ? $prompt->required : 'Required.');
            }

            if ($prompt->validate) {
                $error = ($prompt->validate)($answer ?? '');

                if ($error) {
                    throw new \RuntimeException($error);
                }
            }

            return $answer;
        });

    return (new SymfonyStyle($input, $output))
        ->askQuestion($question);
});
```

Fallbacks must be configured individually for each prompt class. The closure will receive an instance of the prompt class and must return an appropriate type for the prompt.

각 프롬프트 클래스마다 대체 설정을 개별적으로 해야 합니다. 클로저는 프롬프트 클래스의 인스턴스를 받게 되며, 프롬프트에 적절한 타입을 반환해야 합니다.
