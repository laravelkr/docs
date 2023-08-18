# Prompts

- [Introduction](#introduction)
- [Installation](#installation)
- [Available Prompts](#available-prompts)
    - [Text](#text)
    - [Password](#password)
    - [Confirm](#confirm)
    - [Select](#select)
    - [Multi-select](#multiselect)
    - [Suggest](#suggest)
    - [Search](#search)
- [Terminal Considerations](#terminal-considerations)
- [Unsupported Environments & Fallbacks](#fallbacks)

<a name="introduction"></a>
## Introduction

Laravel Prompts is a PHP package for adding beautiful and user-friendly forms to your command-line applications, with browser-like features including placeholder text and validation.

Laravel Prompts is perfect for accepting user input in your [Artisan console commands](/docs/{{version}}/artisan#writing-commands), but it may also be used in any command-line PHP project.

> **Note**  
> Laravel Prompts supports macOS, Linux, and Windows with WSL. For more information, please see our documentation on [unsupported environments & fallbacks](#fallbacks).

<a name="installation"></a>
## Installation

Laravel Prompts is already included with the latest release of Laravel.

Laravel Prompts may also be installed in your other PHP projects by using the Composer package manager:

```shell
composer require laravel/prompts
```

<a name="available-prompts"></a>
## Available Prompts

<a name="text"></a>
### Text

The `text` function will prompt the user with the given question, accept their input, and then return it:

```php
use function Laravel\Prompts\text;

$name = text('What is your name?');
```

You may also include placeholder text and a default value:

```php
$name = text(
    label: 'What is your name?',
    placeholder: 'E.g. Taylor Otwell',
    default: $user?->name
);
```

<a name="text-required"></a>
#### Required Values

If you require a value to be entered, you may pass the `required` argument:

```php
$name = text(
    label: 'What is your name?',
    required: true
);
```

If you would like to customize the validation message, you may also pass a string:

```php
$name = text(
    label: 'What is your name?',
    required: 'Your name is required.'
);
```

<a name="text-validation"></a>
#### Additional Validation

Finally, if you would like to perform additional validation logic, you may pass a closure to the `validate` argument:

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

<a name="password"></a>
### Password

The `password` function is similar to the `text` function, but the user's input will be masked as they type in the console. This is useful when asking for sensitive information such as passwords:

```php
use function Laravel\Prompts\password;

$password = password('What is your password?');
```

You may also include placeholder text:

```php
$password = password(
    label: 'What is your password?',
    placeholder: 'Minimum 8 characters...'
);
```

<a name="password-required"></a>
#### Required Values

If you require a value to be entered, you may pass the `required` argument:

```php
$name = password(
    label: 'What is your password?',
    required: true
);
```

If you would like to customize the validation message, you may also pass a string:

```php
$name = password(
    label: 'What is your password?',
    required: 'The password is required.'
);
```

<a name="password-validation"></a>
#### Additional Validation

Finally, if you would like to perform additional validation logic, you may pass a closure to the `validate` argument:

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

<a name="confirm"></a>
### Confirm

If you need to ask the user for a "yes or no" confirmation, you may use the `confirm` function. Users may use the arrow keys or press `y` or `n` to select their response. This function will return either `true` or `false`.

```php
use function Laravel\Prompts\confirm;

$confirmed = confirm('Do you accept the terms?');
```

By default, the "Yes" answer will be pre-selected. However, you may configure the default to be "No" by passing `false` to the `default` argument:

```php
$confirmed = confirm(
    label: 'Do you accept the terms?',
    default: false,
);
```

You may also customize the wording used for the "Yes" and "No" labels by passing the `yes` and `no` arguments:

```php
$confirmed = confirm(
    label: 'Do you accept the terms?',
    yes: 'I accept',
    no: 'I decline'
);
```

<a name="confirm-required"></a>
#### Requiring "Yes"

If necessary, you may require your users to select "Yes" by passing the `required` argument:

```php
$confirmed = confirm(
    label: 'Do you accept the terms?',
    required: true
);
```

If you would like to customize the validation message, you may also pass a string:

```php
$confirmed = confirm(
    label: 'Do you accept the terms?',
    required: 'You must accept the terms to continue.'
);
```

<a name="select"></a>
### Select

If you need the user to select from a predefined set of choices, you may use the `select` function:

```php
use function Laravel\Prompts\select;

$role = select(
    'What role should the user have?',
    ['Member', 'Contributor', 'Owner'],
);
```

You may also specify the default choice by passing the `default` argument:

```php
$role = select(
    label: 'What role should the user have?',
    options: ['Member', 'Contributor', 'Owner'],
    default: 'Owner'
);
```

You may also pass an associative array to the `options` argument to have the selected key returned instead of its value:

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

```php
$role = select(
    label: 'Which category would you like to assign?',
    options: Category::pluck('name', 'id'),
    scroll: 10
);
```

<a name="select-validation"></a>
#### Validation

Unlike other prompt functions, the `select` function doesn't accept the `required` argument because it is not possible to select nothing. However, you may pass a closure to the `validate` argument if you need to present an option but prevent it from being selected:

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

<a name="multiselect"></a>
### Multi-select

If you need to the user to be able to select multiple options, you may use the `multiselect` function:

```php
use function Laravel\Prompts\multiselect;

$permissions = multiselect(
    'What permissions should be assigned?',
    ['Read', 'Create', 'Update', 'Delete']
);
```

You may also specify options that should be pre-selected by passing an array to the `default` argument:

```php
use function Laravel\Prompts\multiselect;

$permissions = multiselect(
    label: 'What permissions should be assigned?',
    options: ['Read', 'Create', 'Update', 'Delete'],
    default: ['Read', 'Create']
);
```

You may also pass an associative array to the `options` argument to return the selected options' keys instead of their values:

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

```php
$role = multiselect(
    label: 'What categories should be assigned?',
    options: Category::pluck('name', 'id'),
    scroll: 10
);
```

<a name="multiselect-required"></a>
#### Requiring a Value

By default, the user may select zero or more options. You may pass the `required` argument to enforce one or more options instead:

```php
$role = multiselect(
    label: 'What categories should be assigned?',
    options: Category::pluck('name', 'id'),
    required: true,
);
```

<a name="multiselect-validation"></a>
#### Validation

You may pass a closure to the `validate` argument if you need to present an option but prevent it from being selected:

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

<a name="suggest"></a>
### Suggest

The `suggest` function can be used to provide auto-completion for possible choices. The user can still provide any answer, regardless of the auto-completion hints:

```php
use function Laravel\Prompts\suggest;

$name = suggest('What is your name?', ['Taylor', 'Dayle']);
```

Alternatively, you may pass a closure as the second argument to the `suggest` function. The closure will be called each time the user types an input character. The closure should accept a string parameter containing the user's input so far and return an array of options for auto-completion:

```php
$name = suggest(
    'What is your name?',
    fn ($value) => collect(['Taylor', 'Dayle'])
        ->filter(fn ($name) => Str::contains($name, $value, ignoreCase: true))
)
```

You may also include placeholder text and a default value:

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

If you require a value to be entered, you may pass the `required` argument:

```php
$name = suggest(
    label: 'What is your name?',
    options: ['Taylor', 'Dayle'],
    required: true
);
```

If you would like to customize the validation message, you may also pass a string:

```php
$name = suggest(
    label: 'What is your name?',
    options: ['Taylor', 'Dayle'],
    required: 'Your name is required.'
);
```

<a name="suggest-validation"></a>
#### Additional Validation

Finally, if you would like to perform additional validation logic, you may pass a closure to the `validate` argument:

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

<a name="search"></a>
### Search

If you have a lot of options for the user to select from, the `search` function allows the user to type a search query to filter the results before using the arrow keys to select an option:

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

You may also include placeholder text:

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

If you would like to perform additional validation logic, you may pass a closure to the `validate` argument:

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

<a name="terminal-considerations"></a>
### Terminal Considerations

<a name="terminal-width"></a>
#### Terminal Width

If the length of any label, option, or validation message exceeds the number of "columns" in the user's terminal, it will be automatically truncated to fit. Consider minimizing the length of these strings if your users may be using narrower terminals. A typically safe maximum length is 74 characters to support an 80-character terminal.

<a name="terminal-height"></a>
#### Terminal Height

For any prompts that accept the `scroll` argument, the configured value will automatically be reduced to fit the height of the user's terminal, including space for a validation message.

<a name="fallbacks"></a>
### Unsupported Environments & Fallbacks

Laravel Prompts supports macOS, Linux, and Windows with WSL. Due to limitations in the Windows version of PHP, it is not currently possible to use Laravel Prompts on Windows outside of WSL.

For this reason, Laravel Prompts supports falling back to an alternative implementation such as the [Symfony Console Question Helper](https://symfony.com/doc/current/components/console/helpers/questionhelper.html).

> **Note**  
> When using Laravel Prompts with the Laravel framework, fallbacks for each prompt have been configured for you and will be automatically enabled in unsupported environments.

<a name="fallback-conditions"></a>
#### Fallback Conditions

If you are not using Laravel or need to customize when the fallback behavior is used, you may pass a boolean to the `fallbackWhen` static method on the `Prompt` class:

```php
use Laravel\Prompts\Prompt;

Prompt::fallbackWhen(
    ! $input->isInteractive() || windows_os() || app()->runningUnitTests()
);
```

<a name="fallback-behavior"></a>
#### Fallback Behavior

If you are not using Laravel or need to customize the fallback behavior, you may pass a closure to the `fallbackUsing` static method on each prompt class:

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
