# Helpers
# 헬퍼 함수들

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Available Methods](#available-methods)
- [사용 가능한 메소드들](#available-methods)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel includes a variety of global "helper" PHP functions. Many of these functions are used by the framework itself; however, you are free to use them in your own applications if you find them convenient.

라라벨은 다양한 글로벌 "헬퍼" PHP 함수들을 포함하고 있습니다. 그중 많은 기능은 프레임워크가 스스로 사용하지만, 여러분의 어플리케이션에도 자유롭게 사용하실 수 있습니다.

<a name="available-methods"></a>
## Available Methods
## 사용 가능한 메소드

<style>
    .collection-method-list > p {
        column-count: 3; -moz-column-count: 3; -webkit-column-count: 3;
        column-gap: 2em; -moz-column-gap: 2em; -webkit-column-gap: 2em;
    }

    .collection-method-list a {
        display: block;
    }
</style>

### Arrays & Objects
### 배열 & 객체

- [Arr::accessible](#method-array-accessible)
- [Arr::add](#method-array-add)
- [Arr::collapse](#method-array-collapse)
- [Arr::crossJoin](#method-array-crossjoin)
- [Arr::divide](#method-array-divide)
- [Arr::dot](#method-array-dot)
- [Arr::except](#method-array-except)
- [Arr::exists](#method-array-exists)
- [Arr::first](#method-array-first)
- [Arr::flatten](#method-array-flatten)
- [Arr::forget](#method-array-forget)
- [Arr::get](#method-array-get)
- [Arr::has](#method-array-has)
- [Arr::hasAny](#method-array-hasany)
- [Arr::isAssoc](#method-array-isassoc)
- [Arr::last](#method-array-last)
- [Arr::only](#method-array-only)
- [Arr::pluck](#method-array-pluck)
- [Arr::prepend](#method-array-prepend)
- [Arr::pull](#method-array-pull)
- [Arr::query](#method-array-query)
- [Arr::random](#method-array-random)
- [Arr::set](#method-array-set)
- [Arr::shuffle](#method-array-shuffle)
- [Arr::sort](#method-array-sort)
- [Arr::sortRecursive](#method-array-sort-recursive)
- [Arr::toCssClasses](#method-array-to-css-classes)
- [Arr::undot](#method-array-undot)
- [Arr::where](#method-array-where)
- [Arr::whereNotNull](#method-array-where-not-null)
- [Arr::wrap](#method-array-wrap)
- [data_fill](#method-data-fill)
- [data_get](#method-data-get)
- [data_set](#method-data-set)
- [head](#method-head)
- [last](#method-last)

### Paths
### 경로

- [app_path](#method-app-path)
- [base_path](#method-base-path)
- [config_path](#method-config-path)
- [database_path](#method-database-path)
- [mix](#method-mix)
- [public_path](#method-public-path)
- [resource_path](#method-resource-path)
- [storage_path](#method-storage-path)

### Strings
### 문자열

- [\__](#method-__)
- [class_basename](#method-class-basename)
- [e](#method-e)
- [preg_replace_array](#method-preg-replace-array)
- [Str::after](#method-str-after)
- [Str::afterLast](#method-str-after-last)
- [Str::ascii](#method-str-ascii)
- [Str::before](#method-str-before)
- [Str::beforeLast](#method-str-before-last)
- [Str::between](#method-str-between)
- [Str::camel](#method-camel-case)
- [Str::contains](#method-str-contains)
- [Str::containsAll](#method-str-contains-all)
- [Str::endsWith](#method-ends-with)
- [Str::finish](#method-str-finish)
- [Str::headline](#method-str-headline)
- [Str::is](#method-str-is)
- [Str::isAscii](#method-str-is-ascii)
- [Str::isUuid](#method-str-is-uuid)
- [Str::kebab](#method-kebab-case)
- [Str::length](#method-str-length)
- [Str::limit](#method-str-limit)
- [Str::lower](#method-str-lower)
- [Str::markdown](#method-str-markdown)
  [Str::mask](#method-str-mask)
- [Str::orderedUuid](#method-str-ordered-uuid)
- [Str::padBoth](#method-str-padboth)
- [Str::padLeft](#method-str-padleft)
- [Str::padRight](#method-str-padright)
- [Str::plural](#method-str-plural)
- [Str::pluralStudly](#method-str-plural-studly)
- [Str::random](#method-str-random)
- [Str::remove](#method-str-remove)
- [Str::replace](#method-str-replace)
- [Str::replaceArray](#method-str-replace-array)
- [Str::replaceFirst](#method-str-replace-first)
- [Str::replaceLast](#method-str-replace-last)
- [Str::reverse](#method-str-reverse)
- [Str::singular](#method-str-singular)
- [Str::slug](#method-str-slug)
- [Str::snake](#method-snake-case)
- [Str::start](#method-str-start)
- [Str::startsWith](#method-starts-with)
- [Str::studly](#method-studly-case)
- [Str::substr](#method-str-substr)
- [Str::substrCount](#method-str-substrcount)
- [Str::substrReplace](#method-str-substrreplace)
- [Str::title](#method-title-case)
- [Str::toHtmlString](#method-str-to-html-string)
- [Str::ucfirst](#method-str-ucfirst)
- [Str::upper](#method-str-upper)
- [Str::uuid](#method-str-uuid)
- [Str::wordCount](#method-str-word-count)
- [Str::words](#method-str-words)
- [trans](#method-trans)
- [trans_choice](#method-trans-choice)

<a name="fluent-strings"></a>
### Fluent Strings
### Fluent Strings

- [after](#method-fluent-str-after)
- [afterLast](#method-fluent-str-after-last)
- [append](#method-fluent-str-append)
- [ascii](#method-fluent-str-ascii)
- [basename](#method-fluent-str-basename)
- [before](#method-fluent-str-before)
- [beforeLast](#method-fluent-str-before-last)
- [camel](#method-fluent-str-camel)
- [contains](#method-fluent-str-contains)
- [containsAll](#method-fluent-str-contains-all)
- [dirname](#method-fluent-str-dirname)
- [endsWith](#method-fluent-str-ends-with)
- [exactly](#method-fluent-str-exactly)
- [explode](#method-fluent-str-explode)
- [finish](#method-fluent-str-finish)
- [is](#method-fluent-str-is)
- [isAscii](#method-fluent-str-is-ascii)
- [isEmpty](#method-fluent-str-is-empty)
- [isNotEmpty](#method-fluent-str-is-not-empty)
- [kebab](#method-fluent-str-kebab)
- [length](#method-fluent-str-length)
- [limit](#method-fluent-str-limit)
- [lower](#method-fluent-str-lower)
- [ltrim](#method-fluent-str-ltrim)
- [match](#method-fluent-str-match)
- [matchAll](#method-fluent-str-match-all)
- [padBoth](#method-fluent-str-padboth)
- [padLeft](#method-fluent-str-padleft)
- [padRight](#method-fluent-str-padright)
- [plural](#method-fluent-str-plural)
- [prepend](#method-fluent-str-prepend)
- [replace](#method-fluent-str-replace)
- [replaceArray](#method-fluent-str-replace-array)
- [replaceFirst](#method-fluent-str-replace-first)
- [replaceLast](#method-fluent-str-replace-last)
- [replaceMatches](#method-fluent-str-replace-matches)
- [rtrim](#method-fluent-str-rtrim)
- [singular](#method-fluent-str-singular)
- [slug](#method-fluent-str-slug)
- [snake](#method-fluent-str-snake)
- [split](#method-fluent-str-split)
- [start](#method-fluent-str-start)
- [startsWith](#method-fluent-str-starts-with)
- [studly](#method-fluent-str-studly)
- [substr](#method-fluent-str-substr)
- [title](#method-fluent-str-title)
- [trim](#method-fluent-str-trim)
- [ucfirst](#method-fluent-str-ucfirst)
- [upper](#method-fluent-str-upper)
- [when](#method-fluent-str-when)
- [whenEmpty](#method-fluent-str-when-empty)
- [words](#method-fluent-str-words)

### URLs
### URLs

- [action](#method-action)
- [asset](#method-asset)
- [route](#method-route)
- [secure_asset](#method-secure-asset)
- [secure_url](#method-secure-url)
- [url](#method-url)

### Miscellaneous
### 기타

- [abort](#method-abort)
- [abort_if](#method-abort-if)
- [abort_unless](#method-abort-unless)
- [app](#method-app)
- [auth](#method-auth)
- [back](#method-back)
- [bcrypt](#method-bcrypt)
- [blank](#method-blank)
- [broadcast](#method-broadcast)
- [cache](#method-cache)
- [class_uses_recursive](#method-class-uses-recursive)
- [collect](#method-collect)
- [config](#method-config)
- [cookie](#method-cookie)
- [csrf_field](#method-csrf-field)
- [csrf_token](#method-csrf-token)
- [dd](#method-dd)
- [dispatch](#method-dispatch)
- [dispatch_now](#method-dispatch-now)
- [dump](#method-dump)
- [env](#method-env)
- [event](#method-event)
- [factory](#method-factory)
- [filled](#method-filled)
- [info](#method-info)
- [logger](#method-logger)
- [method_field](#method-method-field)
- [now](#method-now)
- [old](#method-old)
- [optional](#method-optional)
- [policy](#method-policy)
- [redirect](#method-redirect)
- [report](#method-report)
- [request](#method-request)
- [rescue](#method-rescue)
- [resolve](#method-resolve)
- [response](#method-response)
- [retry](#method-retry)
- [session](#method-session)
- [tap](#method-tap)
- [throw_if](#method-throw-if)
- [throw_unless](#method-throw-unless)
- [today](#method-today)
- [trait_uses_recursive](#method-trait-uses-recursive)
- [transform](#method-transform)
- [validator](#method-validator)
- [value](#method-value)
- [view](#method-view)
- [with](#method-with)

<a name="method-listing"></a>
## Method Listing
## 메소드 목록

<style>
    #collection-method code {
        font-size: 14px;
    }

    #collection-method:not(.first-collection-method) {
        margin-top: 50px;
    }
</style>

<a name="arrays"></a>
## Arrays & Objects
## 배열 & 객체

<a name="method-array-accessible"></a>
#### `Arr::accessible()` {#collection-method .first-collection-method}

The `Arr::accessible` method checks that the given value is array accessible:

`Arr::accessible` 메소드는 주어진 값이 배열에 접근할 수 있는지 확인합니다.

    use Illuminate\Support\Arr;
    use Illuminate\Support\Collection;

    $isAccessible = Arr::accessible(['a' => 1, 'b' => 2]);

    // true

    $isAccessible = Arr::accessible(new Collection);

    // true

    $isAccessible = Arr::accessible('abc');

    // false

    $isAccessible = Arr::accessible(new stdClass);

    // false

<a name="method-array-add"></a>
#### `Arr::add()` {#collection-method}

The `Arr::add` method adds a given key / value pair to an array if the given key doesn't already exist in the array or is set to `null`:

`Arr::add` 메소드는 배열 내에 키가 존재하지 않거나 `null`이면 주어진 key / value 쌍을 배열에 추가합니다.

    use Illuminate\Support\Arr;

    $array = Arr::add(['name' => 'Desk'], 'price', 100);

    // ['name' => 'Desk', 'price' => 100]

    $array = Arr::add(['name' => 'Desk', 'price' => null], 'price', 100);

    // ['name' => 'Desk', 'price' => 100]


<a name="method-array-collapse"></a>
#### `Arr::collapse()` {#collection-method}

The `Arr::collapse` method collapses an array of arrays into a single array:

`Arr::collapse` 메소드는 배열들의 배열(여러 개의 배열)을 하나의 배열로 통합합니다.

    use Illuminate\Support\Arr;

    $array = Arr::collapse([[1, 2, 3], [4, 5, 6], [7, 8, 9]]);

    // [1, 2, 3, 4, 5, 6, 7, 8, 9]

<a name="method-array-crossjoin"></a>
#### `Arr::crossJoin()` {#collection-method}

The `Arr::crossJoin` method cross joins the given arrays, returning a Cartesian product with all possible permutations:

`Arr::crossJoin` 메소드는 주어진 배열을 교차 결합하여 가능한 모든 순열이 있는 데카르트 곱을 반환합니다.

    use Illuminate\Support\Arr;

    $matrix = Arr::crossJoin([1, 2], ['a', 'b']);

    /*
        [
            [1, 'a'],
            [1, 'b'],
            [2, 'a'],
            [2, 'b'],
        ]
    */

    $matrix = Arr::crossJoin([1, 2], ['a', 'b'], ['I', 'II']);

    /*
        [
            [1, 'a', 'I'],
            [1, 'a', 'II'],
            [1, 'b', 'I'],
            [1, 'b', 'II'],
            [2, 'a', 'I'],
            [2, 'a', 'II'],
            [2, 'b', 'I'],
            [2, 'b', 'II'],
        ]
    */

<a name="method-array-divide"></a>
#### `Arr::divide()` {#collection-method}

The `Arr::divide` method returns two arrays, one containing the keys, and the other containing the values of the given array:

`Arr::divide` 메소드는 주어진 배열에서 키들을 담고 있는 배열과 값들을 담고 있는 배열, 총 2개의 배열을 반환합니다.

    use Illuminate\Support\Arr;

    [$keys, $values] = Arr::divide(['name' => 'Desk']);

    // $keys: ['name']

    // $values: ['Desk']

<a name="method-array-dot"></a>
#### `Arr::dot()` {#collection-method}

The `Arr::dot` method flattens a multi-dimensional array into a single level array that uses "dot" notation to indicate depth:

`Arr::dot` 메소드는 다차원 배열을 "점(.)"으로 배열 깊이를 표기하면서 단일 레벨의 배열로 만듭니다.

    use Illuminate\Support\Arr;

    $array = ['products' => ['desk' => ['price' => 100]]];

    $flattened = Arr::dot($array);

    // ['products.desk.price' => 100]

<a name="method-array-except"></a>
#### `Arr::except()` {#collection-method}

The `Arr::except` method removes the given key / value pairs from an array:

`Arr::except` 메소드는 주어진 키 / 값 쌍을 배열에서 제거합니다.

    use Illuminate\Support\Arr;

    $array = ['name' => 'Desk', 'price' => 100];

    $filtered = Arr::except($array, ['price']);

    // ['name' => 'Desk']

<a name="method-array-exists"></a>
#### `Arr::exists()` {#collection-method}

The `Arr::exists` method checks that the given key exists in the provided array:

`Arr::exists` 메소드는 주어진 키가 제공된 배열에 있는지 확인합니다.

    use Illuminate\Support\Arr;

    $array = ['name' => 'John Doe', 'age' => 17];

    $exists = Arr::exists($array, 'name');

    // true

    $exists = Arr::exists($array, 'salary');

    // false

<a name="method-array-first"></a>
#### `Arr::first()` {#collection-method}

The `Arr::first` method returns the first element of an array passing a given truth test:

`Arr::first` 메소드는 전달된 배열 중 주어진 조건을 만족하는 첫 번째 요소를 반환합니다.

    use Illuminate\Support\Arr;

    $array = [100, 200, 300];

    $first = Arr::first($array, function ($value, $key) {
        return $value >= 150;
    });

    // 200

A default value may also be passed as the third parameter to the method. This value will be returned if no value passes the truth test:

메소드에 세번째 파라미터로 기본값을 지정할 수 있습니다. 배열의 어떠한 값도 조건을 통과하지 못했을 때 이 값이 반환됩니다.

    use Illuminate\Support\Arr;

    $first = Arr::first($array, $callback, $default);

<a name="method-array-flatten"></a>
#### `Arr::flatten()` {#collection-method}

The `Arr::flatten` method flattens a multi-dimensional array into a single level array:

`Arr::flatten` 메소드는 다차원 배열을 단일 레벨의 1차원 배열로 만듭니다.

    use Illuminate\Support\Arr;

    $array = ['name' => 'Joe', 'languages' => ['PHP', 'Ruby']];

    $flattened = Arr::flatten($array);

    // ['Joe', 'PHP', 'Ruby']

<a name="method-array-forget"></a>
#### `Arr::forget()` {#collection-method}

The `Arr::forget` method removes a given key / value pair from a deeply nested array using "dot" notation:

`Arr::forget` 메소드는 "점(.)" 표기법을 사용하여 중첩 배열로부터 주어진 키 / 값 쌍을 제거합니다.

    use Illuminate\Support\Arr;

    $array = ['products' => ['desk' => ['price' => 100]]];

    Arr::forget($array, 'products.desk');

    // ['products' => []]

<a name="method-array-get"></a>
#### `Arr::get()` {#collection-method}

The `Arr::get` method retrieves a value from a deeply nested array using "dot" notation:

`Arr::get` 메소드는 "점(.)" 표기법으로 중첩 배열로부터 주어진 값을 찾습니다.

    use Illuminate\Support\Arr;

    $array = ['products' => ['desk' => ['price' => 100]]];

    $price = Arr::get($array, 'products.desk.price');

    // 100

The `Arr::get` method also accepts a default value, which will be returned if the specific key is not found:

`Arr::get` 메소드는 특정 키를 찾지 못한 경우 반환되는 기본값을 지정할 수도 있습니다.

    use Illuminate\Support\Arr;

    $discount = Arr::get($array, 'products.desk.discount', 0);

    // 0

<a name="method-array-has"></a>
#### `Arr::has()` {#collection-method}

The `Arr::has` method checks whether a given item or items exists in an array using "dot" notation:

`Arr::has` 메소드는 "점(.)" 표기를 이용하여 배열에 주어진 아이템 또는 아이템들이 존재하는지 확인합니다.

    use Illuminate\Support\Arr;

    $array = ['product' => ['name' => 'Desk', 'price' => 100]];

    $contains = Arr::has($array, 'product.name');

    // true

    $contains = Arr::has($array, ['product.price', 'product.discount']);

    // false

<a name="method-array-hasany"></a>
#### `Arr::hasAny()` {#collection-method}

The `Arr::hasAny` method checks whether any item in a given set exists in an array using "dot" notation:

`Arr::hasAny` 메소드는 "점(.)" 표기를 이용하여 주어진 세트의 항목이 배열에 존재하는지 확인합니다.

    use Illuminate\Support\Arr;

    $array = ['product' => ['name' => 'Desk', 'price' => 100]];

    $contains = Arr::hasAny($array, 'product.name');

    // true

    $contains = Arr::hasAny($array, ['product.name', 'product.discount']);

    // true

    $contains = Arr::hasAny($array, ['category', 'product.discount']);

    // false

<a name="method-array-isassoc"></a>
#### `Arr::isAssoc()` {#collection-method}

The `Arr::isAssoc` returns `true` if the given array is an associative array. An array is considered "associative" if it doesn't have sequential numerical keys beginning with zero:

주어진 배열이 연관 배열이면 `Arr::isAssoc`은 `true`를 반환합니다. 배열은 0으로 시작하는 순차적 숫자 키가 없는 경우 "연관-associative"로 간주됩니다.

    use Illuminate\Support\Arr;

    $isAssoc = Arr::isAssoc(['product' => ['name' => 'Desk', 'price' => 100]]);

    // true

    $isAssoc = Arr::isAssoc([1, 2, 3]);

    // false

<a name="method-array-last"></a>
#### `Arr::last()` {#collection-method}

The `Arr::last` method returns the last element of an array passing a given truth test:

`Arr::last` 메소드는 전달된 조건을 통과하는 아이템의 가장 마지막 요소를 반환합니다.

    use Illuminate\Support\Arr;

    $array = [100, 200, 300, 110];

    $last = Arr::last($array, function ($value, $key) {
        return $value >= 150;
    });

    // 300

A default value may be passed as the third argument to the method. This value will be returned if no value passes the truth test:

메소드의 세 번째 인자로 기본값을 전달할 수 있습니다. 이 값은 조건을 통과하는 값이 없을 때 반환됩니다.

    use Illuminate\Support\Arr;

    $last = Arr::last($array, $callback, $default);

<a name="method-array-only"></a>
#### `Arr::only()` {#collection-method}

The `Arr::only` method returns only the specified key / value pairs from the given array:

`Arr::only` 메소드는 특정한 키 / 값 쌍만을 배열로부터 반환합니다.

    use Illuminate\Support\Arr;

    $array = ['name' => 'Desk', 'price' => 100, 'orders' => 10];

    $slice = Arr::only($array, ['name', 'price']);

    // ['name' => 'Desk', 'price' => 100]

<a name="method-array-pluck"></a>
#### `Arr::pluck()` {#collection-method}

The `Arr::pluck` method retrieves all of the values for a given key from an array:

`Arr::pluck` 메소드는 배열로부터 주어진 키 / 값 쌍의 리스트를 조회합니다.

    use Illuminate\Support\Arr;

    $array = [
        ['developer' => ['id' => 1, 'name' => 'Taylor']],
        ['developer' => ['id' => 2, 'name' => 'Abigail']],
    ];

    $names = Arr::pluck($array, 'developer.name');

    // ['Taylor', 'Abigail']

You may also specify how you wish the resulting list to be keyed:

리스트가 만들어 지는 결과가 어떻게 키로 변환될지 지정할 수도 있습니다.

    use Illuminate\Support\Arr;

    $names = Arr::pluck($array, 'developer.name', 'developer.id');

    // [1 => 'Taylor', 2 => 'Abigail']

<a name="method-array-prepend"></a>
#### `Arr::prepend()` {#collection-method}

The `Arr::prepend` method will push an item onto the beginning of an array:

`Arr::prepend` 메소드는 배열의 시작 부분에 아이템을 추가할 것입니다.

    use Illuminate\Support\Arr;

    $array = ['one', 'two', 'three', 'four'];

    $array = Arr::prepend($array, 'zero');

    // ['zero', 'one', 'two', 'three', 'four']

If needed, you may specify the key that should be used for the value:

필요한 경우, 아이템의 키를 지정할 수도 있습니다.

    use Illuminate\Support\Arr;

    $array = ['price' => 100];

    $array = Arr::prepend($array, 'Desk', 'name');

    // ['name' => 'Desk', 'price' => 100]

<a name="method-array-pull"></a>
#### `Arr::pull()` {#collection-method}

The `Arr::pull` method returns and removes a key / value pair from an array:

`Arr::pull` 메소드는 배열에서 주어진 키 / 값 쌍을 반환함과 동시에 제거합니다.

    use Illuminate\Support\Arr;

    $array = ['name' => 'Desk', 'price' => 100];

    $name = Arr::pull($array, 'name');

    // $name: Desk

    // $array: ['price' => 100]

A default value may be passed as the third argument to the method. This value will be returned if the key doesn't exist:

메소드의 세 번째 인자로 기본값을 전달할 수 있습니다. 키가 존재하지 않는 경우 이 값이 반환됩니다.

    use Illuminate\Support\Arr;

    $value = Arr::pull($array, $key, $default);

<a name="method-array-query"></a>
#### `Arr::query()` {#collection-method}

The `Arr::query` method converts the array into a query string:

`Arr::query` 메소드는 배열을 쿼리 스트링으로 변환합니다.

    use Illuminate\Support\Arr;

    $array = ['name' => 'Taylor', 'order' => ['column' => 'created_at', 'direction' => 'desc']];

    Arr::query($array);

    // name=Taylor&order[column]=created_at&order[direction]=desc

<a name="method-array-random"></a>
#### `Arr::random()` {#collection-method}

The `Arr::random` method returns a random value from an array:

`Arr::random` 메소드는 배열에서 임의의 값을 반환합니다.

    use Illuminate\Support\Arr;

    $array = [1, 2, 3, 4, 5];

    $random = Arr::random($array);

    // 4 - (retrieved randomly)

You may also specify the number of items to return as an optional second argument. Note that providing this argument will return an array, even if only one item is desired:

두 번째 인자로 몇 개의 아이템을 반환할지 값을 지정할 수 있습니다. 이 인자를 지정하면, 하나의 아이템이 포함되더라도 배열이 반환됩니다.

    use Illuminate\Support\Arr;

    $items = Arr::random($array, 2);

    // [2, 5] - (retrieved randomly)

<a name="method-array-set"></a>
#### `Arr::set()` {#collection-method}

The `Arr::set` method sets a value within a deeply nested array using "dot" notation:

`Arr::set` 메소드는 "점(.)" 표기법을 이용하여 중첩된 배열 내에 값을 설정합니다.

    use Illuminate\Support\Arr;

    $array = ['products' => ['desk' => ['price' => 100]]];

    Arr::set($array, 'products.desk.price', 200);

    // ['products' => ['desk' => ['price' => 200]]]

<a name="method-array-shuffle"></a>
#### `Arr::shuffle()` {#collection-method}

The `Arr::shuffle` method randomly shuffles the items in the array:

`Arr::shuffle` 메소드는 배열의 항목을 무작위로 섞습니다.

    use Illuminate\Support\Arr;

    $array = Arr::shuffle([1, 2, 3, 4, 5]);

    // [3, 2, 5, 1, 4] - (generated randomly)

<a name="method-array-sort"></a>
#### `Arr::sort()` {#collection-method}

The `Arr::sort` method sorts an array by its values:

`Arr::sort` 메소드는 값을 기반으로 정렬을 수행합니다.

    use Illuminate\Support\Arr;

    $array = ['Desk', 'Table', 'Chair'];

    $sorted = Arr::sort($array);

    // ['Chair', 'Desk', 'Table']

You may also sort the array by the results of the given Closure:

또한, 주어진 클로저의 결괏값으로 배열을 정렬할 수 있습니다.

    use Illuminate\Support\Arr;

    $array = [
        ['name' => 'Desk'],
        ['name' => 'Table'],
        ['name' => 'Chair'],
    ];

    $sorted = array_values(Arr::sort($array, function ($value) {
        return $value['name'];
    }));

    /*
        [
            ['name' => 'Chair'],
            ['name' => 'Desk'],
            ['name' => 'Table'],
        ]
    */

<a name="method-array-sort-recursive"></a>
#### `Arr::sortRecursive()` {#collection-method}

The `Arr::sortRecursive` method recursively sorts an array using the `sort` function for numeric sub=arrays and `ksort` for associative subarrays:

`Arr::sortRecursive` 메소드는 순차적 하위 배열을 위한 `sort` 함수와 연관 하위 배열을 위한 `ksort` 함수를 사용하여 배열을 재귀적으로 정렬합니다.

    use Illuminate\Support\Arr;

    $array = [
        ['Roman', 'Taylor', 'Li'],
        ['PHP', 'Ruby', 'JavaScript'],
        ['one' => 1, 'two' => 2, 'three' => 3],
    ];

    $sorted = Arr::sortRecursive($array);

    /*
        [
            ['JavaScript', 'PHP', 'Ruby'],
            ['one' => 1, 'three' => 3, 'two' => 2],
            ['Li', 'Roman', 'Taylor'],
        ]
    */

<a name="method-array-where"></a>
#### `Arr::where()` {#collection-method}

The `Arr::where` method filters an array using the given Closure:

`Arr::where` 메소드는 주어진 클로져를 사용하여 배열을 필터링합니다.

    use Illuminate\Support\Arr;

    $array = [100, '200', 300, '400', 500];

    $filtered = Arr::where($array, function ($value, $key) {
        return is_string($value);
    });

    // [1 => '200', 3 => '400']

<a name="method-array-wrap"></a>
#### `Arr::wrap()` {#collection-method}

The `Arr::wrap` method wraps the given value in an array. If the given value is already an array it will not be changed:

`Arr::wrap` 메소드는 주어진 값을 배열로 만듭니다. 만약 함수에 전달된 값이 배열이라면 결과에는 변경사항이 없습니다.

    use Illuminate\Support\Arr;

    $string = 'Laravel';

    $array = Arr::wrap($string);

    // ['Laravel']

If the given value is null, an empty array will be returned:

주어진 값이 null인 경우, 빈 배열이 반환됩니다.

    use Illuminate\Support\Arr;

    $nothing = null;

    $array = Arr::wrap($nothing);

    // []

<a name="method-data-fill"></a>
#### `data_fill()` {#collection-method}

The `data_fill` function sets a missing value within a nested array or object using "dot" notation:

`data_fill` 함수는 "점(.)" 표기법을 사용하여 중첩된 배열 또는 객체 내부에 빠진 값을 채워 넣습니다.

    $data = ['products' => ['desk' => ['price' => 100]]];

    data_fill($data, 'products.desk.price', 200);

    // ['products' => ['desk' => ['price' => 100]]]

    data_fill($data, 'products.desk.discount', 10);

    // ['products' => ['desk' => ['price' => 100, 'discount' => 10]]]

This function also accepts asterisks as wildcards and will fill the target accordingly:

이 함수는 또한 별표를 와일드카드로 받아들여 해당하는 대상을 채워넣습니다.

    $data = [
        'products' => [
            ['name' => 'Desk 1', 'price' => 100],
            ['name' => 'Desk 2'],
        ],
    ];

    data_fill($data, 'products.*.price', 200);

    /*
        [
            'products' => [
                ['name' => 'Desk 1', 'price' => 100],
                ['name' => 'Desk 2', 'price' => 200],
            ],
        ]
    */

<a name="method-data-get"></a>
#### `data_get()` {#collection-method}

The `data_get` function retrieves a value from a nested array or object using "dot" notation:

`data_get` 함수는 "점(.)" 표기법을 사용하여 중첩된 배열 또는 객체 안에서 값을 조회합니다.

    $data = ['products' => ['desk' => ['price' => 100]]];

    $price = data_get($data, 'products.desk.price');

    // 100

The `data_get` function also accepts a default value, which will be returned if the specified key is not found:

`data_get` 함수는 또한 지정된 키를 찾을 수 없는 경우 반환될 기본값을 인자로 받습니다.

    $discount = data_get($data, 'products.desk.discount', 0);

    // 0

The function also accepts wildcards using asterisks, which may target any key of the array or object:

이 함수는 배열이나 객체의 키를 대상으로 별표를 이용한 와일드 카드도 허용합니다.

    $data = [
        'product-one' => ['name' => 'Desk 1', 'price' => 100],
        'product-two' => ['name' => 'Desk 2', 'price' => 150],
    ];

    data_get($data, '*.name');

    // ['Desk 1', 'Desk 2'];

<a name="method-data-set"></a>
#### `data_set()` {#collection-method}

The `data_set` function sets a value within a nested array or object using "dot" notation:

`data_set` 함수는 "점(.)" 표기법을 사용하여 중첩된 배열 또는 객체 안의 값을 설정합니다.

    $data = ['products' => ['desk' => ['price' => 100]]];

    data_set($data, 'products.desk.price', 200);

    // ['products' => ['desk' => ['price' => 200]]]

This function also accepts wildcards and will set values on the target accordingly:

이 함수는 또한 별표를 와일드 카드로 받아들여 해당하는 대상을 설정합니다.

    $data = [
        'products' => [
            ['name' => 'Desk 1', 'price' => 100],
            ['name' => 'Desk 2', 'price' => 150],
        ],
    ];

    data_set($data, 'products.*.price', 200);

    /*
        [
            'products' => [
                ['name' => 'Desk 1', 'price' => 200],
                ['name' => 'Desk 2', 'price' => 200],
            ],
        ]
    */

By default, any existing values are overwritten. If you wish to only set a value if it doesn't exist, you may pass `false` as the fourth argument:

기본적으로 기존 값은 덮어 씁니다. 새로운 값을 설정하고자 한다면, 값이 존재하지 않을 때만 네 번째 인자로 `false`를 전달하면 됩니다.

    $data = ['products' => ['desk' => ['price' => 100]]];

    data_set($data, 'products.desk.price', 200, false);

    // ['products' => ['desk' => ['price' => 100]]]

<a name="method-head"></a>
#### `head()` {#collection-method}

The `head` function returns the first element in the given array:

`head` 함수는 배열의 첫 번째 요소를 반환합니다.

    $array = [100, 200, 300];

    $first = head($array);

    // 100

<a name="method-last"></a>
#### `last()` {#collection-method}

The `last` function returns the last element in the given array:

`last` 함수는 배열의 마지막 요소를 반환합니다.

    $array = [100, 200, 300];

    $last = last($array);

    // 300

<a name="paths"></a>
## Paths
## 경로

<a name="method-app-path"></a>
#### `app_path()` {#collection-method}

The `app_path` function returns the fully qualified path to the `app` directory. You may also use the `app_path` function to generate a fully qualified path to a file relative to the application directory:

`app_path` 함수는 `app` 디렉토리에 대한 절대 경로를 반환합니다. `app_path` 함수를 사용하면 어플리케이션 디렉토리에서 특정 파일의 절대 경로를 생성할 수도 있습니다.

    $path = app_path();

    $path = app_path('Http/Controllers/Controller.php');

<a name="method-base-path"></a>
#### `base_path()` {#collection-method}

The `base_path` function returns the fully qualified path to the project root. You may also use the `base_path` function to generate a fully qualified path to a given file relative to the project root directory:

`base_path` 함수는 프로젝트의 루트 디렉토리에 대한 절대 경로를 반환합니다. `base_path` 함수를 사용하여 프로젝트 루트 디렉토리에 대한 특정 파일의 절대 경로를 생성할 수도 있습니다.

    $path = base_path();

    $path = base_path('vendor/bin');

<a name="method-config-path"></a>
#### `config_path()` {#collection-method}

The `config_path` function returns the fully qualified path to the `config` directory. You may also use the `config_path` function to generate a fully qualified path to a given file within the application's configuration directory:

`config_path` 함수는 애플리케이션의 `config` 디렉토리에 대한 절대 경로를 반환합니다. `config_path` 함수를 사용하여, 어플리케이션의 설정 디렉토리 안에 있는 파일에 대한 절대 경로를 생성할 수도 있습니다.

    $path = config_path();

    $path = config_path('app.php');

<a name="method-database-path"></a>
#### `database_path()` {#collection-method}

The `database_path` function returns the fully qualified path to the `database` directory. You may also use the `database_path` function to generate a fully qualified path to a given file within the database directory:

`database_path` 함수는 애플리케이션의 `database` 디렉토리에 대한 절대 경로를 반환합니다. `database_path` 함수를 사용하여, 애플리케이션의 데이터베이스 디렉토리 안에 있는 파일에 대한 절대 경로를 생성할 수도 있습니다.

    $path = database_path();

    $path = database_path('factories/UserFactory.php');

<a name="method-mix"></a>
#### `mix()` {#collection-method}

The `mix` function returns the path to a [versioned Mix file](/docs/{{version}}/mix):

`mix` 함수는 [버전이 지정된 Mix](/docs/{{version}}/mix) 파일에 대한 경로를 반환합니다.

    $path = mix('css/app.css');

<a name="method-public-path"></a>
#### `public_path()` {#collection-method}

The `public_path` function returns the fully qualified path to the `public` directory. You may also use the `public_path` function to generate a fully qualified path to a given file within the public directory:

`public_path` 함수는 `public` 디렉토리에 대한 절대 경로를 반환합니다. `public_path` 함수를 사용하여, 애플리케이션의 public 디렉토리 안에 있는 파일에 대한 절대 경로를 생성할 수도 있습니다.

    $path = public_path();

    $path = public_path('css/app.css');

<a name="method-resource-path"></a>
#### `resource_path()` {#collection-method}

The `resource_path` function returns the fully qualified path to the `resources` directory. You may also use the `resource_path` function to generate a fully qualified path to a given file within the resources directory:

`resource_path` 함수는 `resources` 디렉토리에 대한 절대 경로를 반환합니다. `resource_path` 함수를 사용하여 리소스 디렉토리 안에 있는 파일에 대한 절대 경로를 생성할 수도 있습니다.

    $path = resource_path();

    $path = resource_path('sass/app.scss');

<a name="method-storage-path"></a>
#### `storage_path()` {#collection-method}

The `storage_path` function returns the fully qualified path to the `storage` directory. You may also use the `storage_path` function to generate a fully qualified path to a given file within the storage directory:

`storage_path` 함수는 `storage` 디렉토리에 대한 절대 경로를 반환합니다. `storage_path` 함수를 사용하여 스토리지 디렉토리 안에 있는 파일에 대한 절대 경로를 생성할 수도 있습니다.

    $path = storage_path();

    $path = storage_path('app/file.txt');

<a name="strings"></a>
## Strings
## 문자열

<a name="method-__"></a>
#### `__()` {#collection-method}

The `__` function translates the given translation string or translation key using your [localization files](/docs/{{version}}/localization):

`__` 함수는 주어진 다국어 문자열 또는 다국어 키를 [다국어 파일](/docs/{{version}}/localization)을 사용하여 변환합니다.

    echo __('Welcome to our application');

    echo __('messages.welcome');

If the specified translation string or key does not exist, the `__` function will return the given value. So, using the example above, the `__` function would return `messages.welcome` if that translation key does not exist.

지정된 다국어 문자열이나 키가 존재하지 않는 경우, `__` 함수는 주어진 값을 그대로 반환합니다. 따라서 예제와 같이 `__` 함수는 다국어 키가 존재하지 않는다면 `messages.welcome`을 그대로 반환합니다.

<a name="method-class-basename"></a>
#### `class_basename()` {#collection-method}

The `class_basename` function returns the class name of the given class with the class's namespace removed:

`class_basename` 함수는 클래스의 네임스페이스를 제거한 클래스의 클래스 명을 반환합니다.

    $class = class_basename('Foo\Bar\Baz');

    // Baz

<a name="method-e"></a>
#### `e()` {#collection-method}

The `e` function runs PHP's `htmlspecialchars` function with the `double_encode` option set to `true` by default:

`e` 함수는 주어진 문자열에 PHP의 `htmlspecialchars` 함수를 `double_encode` 옵션이 기본적으로 `true`인 형태로 실행한 결과를 반환합니다.

    echo e('<html>foo</html>');

    // &lt;html&gt;foo&lt;/html&gt;

<a name="method-preg-replace-array"></a>
#### `preg_replace_array()` {#collection-method}

The `preg_replace_array` function replaces a given pattern in the string sequentially using an array:

`preg_replace_array` 함수는 주어진 패턴에 맞는 문자열을 차례대로 배열로 대체합니다.

    $string = 'The event will take place between :start and :end';

    $replaced = preg_replace_array('/:[a-z_]+/', ['8:30', '9:00'], $string);

    // The event will take place between 8:30 and 9:00

<a name="method-str-after"></a>
#### `Str::after()` {#collection-method}

The `Str::after` method returns everything after the given value in a string. The entire string will be returned if the value does not exist within the string:

`Str::after` 메소드는 주어진 값 이후의 모든 것을 반환합니다. 문자열 내에 값이 없으면 전체 문자열이 반환됩니다.

    use Illuminate\Support\Str;

    $slice = Str::after('This is my name', 'This is');

    // ' my name'

<a name="method-str-after-last"></a>
#### `Str::afterLast()` {#collection-method}

The `Str::afterLast` method returns everything after the last occurrence of the given value in a string. The entire string will be returned if the value does not exist within the string:

`Str::afterLast` 메소드는 문자열에서 주어진 값이 마지막으로 나타난 후 모든 것을 반환합니다. 문자열 내에 값이 없으면 전체 문자열이 반환됩니다.

    use Illuminate\Support\Str;

    $slice = Str::afterLast('App\Http\Controllers\Controller', '\\');

    // 'Controller'

<a name="method-str-ascii"></a>
#### `Str::ascii()` {#collection-method}

The `Str::ascii` method will attempt to transliterate the string into an ASCII value:

`Str::ascii` 메소드는 문자열을 ASCII 값으로 변환하려고 시도합니다.

    use Illuminate\Support\Str;

    $slice = Str::ascii('û');

    // 'u'

<a name="method-str-before"></a>
#### `Str::before()` {#collection-method}

The `Str::before` method returns everything before the given value in a string:

`Str::before` 메소드는 문자열에서 주어진 값 이전의 모든 것을 반환합니다.

    use Illuminate\Support\Str;

    $slice = Str::before('This is my name', 'my name');

    // 'This is '

<a name="method-str-before-last"></a>
#### `Str::beforeLast()` {#collection-method}

The `Str::beforeLast` method returns everything before the last occurrence of the given value in a string:

`Str::beforeLast` 메소드는 문자열에서 주어진 값 이전의 모든 것을 반환합니다.

    use Illuminate\Support\Str;

    $slice = Str::beforeLast('This is my name', 'is');

    // 'This '

<a name="method-str-between"></a>
#### `Str::between()` {#collection-method}

The `Str::between` method returns the portion of a string between two values:

`Str::between` 메소드는 두 값 사이의 문자열 부분을 반환합니다.


    use Illuminate\Support\Str;

    $slice = Str::between('This is my name', 'This', 'name');

    // ' is my '

<a name="method-camel-case"></a>
#### `Str::camel()` {#collection-method}

The `Str::camel` method converts the given string to `camelCase`:

`Str::camel` 메소드는 주어진 문자열을 `camelCase`로 변환합니다.

    use Illuminate\Support\Str;

    $converted = Str::camel('foo_bar');

    // fooBar

<a name="method-str-contains"></a>
#### `Str::contains()` {#collection-method}

The `Str::contains` method determines if the given string contains the given value (case sensitive):

`Str::contains` 메소드는 주어진 문자열이 주어진 값을 포함하는지 확인합니다. (대소문자를 구분합니다.)

    use Illuminate\Support\Str;

    $contains = Str::contains('This is my name', 'my');

    // true

You may also pass an array of values to determine if the given string contains any of the values:

또한, 주어진 문자열에 값이 포함되어 있는지 확인하기 위해 배열을 전달할 수도 있습니다.

    use Illuminate\Support\Str;

    $contains = Str::contains('This is my name', ['my', 'foo']);

    // true

<a name="method-str-contains-all"></a>
#### `Str::containsAll()` {#collection-method}

The `Str::containsAll` method determines if the given string contains all array values:

`Str::containsAll` 메소드는 주어진 문자열에 모든 배열 값이 포함되어 있는지 확인합니다.

    use Illuminate\Support\Str;

    $containsAll = Str::containsAll('This is my name', ['my', 'name']);

    // true

<a name="method-ends-with"></a>
#### `Str::endsWith()` {#collection-method}

The `Str::endsWith` method determines if the given string ends with the given value:

`Str::endsWith` 메소드는 주어진 문자열이 주어진 값으로 끝나는지 확인합니다.

    use Illuminate\Support\Str;

    $result = Str::endsWith('This is my name', 'name');

    // true


You may also pass an array of values to determine if the given string ends with any of the given values:

주어진 문자열이 주어진 값으로 끝나는지 확인하기 위해 배열을 전달할 수도 있습니다.

    use Illuminate\Support\Str;

    $result = Str::endsWith('This is my name', ['name', 'foo']);

    // true

    $result = Str::endsWith('This is my name', ['this', 'foo']);

    // false

<a name="method-str-finish"></a>
#### `Str::finish()` {#collection-method}

The `Str::finish` method adds a single instance of the given value to a string if it does not already end with the value:

`Str::finish` 메소드는 문자열이 주어진 값으로 끝나지 않으면 해당 값을 추가합니다.

    use Illuminate\Support\Str;

    $adjusted = Str::finish('this/string', '/');

    // this/string/

    $adjusted = Str::finish('this/string/', '/');

    // this/string/

<a name="method-str-is"></a>
#### `Str::is()` {#collection-method}

The `Str::is` method determines if a given string matches a given pattern. Asterisks may be used to indicate wildcards:

`Str::is` 메소드는 주어진 문자열이 주어진 패턴과 일치하는지를 확인합니다. 와일드카드를 나타내는 데 별표를 사용할 수 있습니다.

    use Illuminate\Support\Str;

    $matches = Str::is('foo*', 'foobar');

    // true

    $matches = Str::is('baz*', 'foobar');

    // false

<a name="method-str-is-ascii"></a>
#### `Str::isAscii()` {#collection-method}

The `Str::isAscii` method determines if a given string is 7 bit ASCII:

`Str::isAscii` 메소드는 주어진 문자열이 7 비트 ASCII인지 확인합니다.

    use Illuminate\Support\Str;

    $isAscii = Str::isAscii('Taylor');

    // true

    $isAscii = Str::isAscii('ü');

    // false

<a name="method-str-is-uuid"></a>
#### `Str::isUuid()` {#collection-method}

The `Str::isUuid` method determines if the given string is a valid UUID:

`Str::isUuid` 메소드는 주어진 문자열이 유효한 UUID인지 확인합니다.

    use Illuminate\Support\Str;

    $isUuid = Str::isUuid('a0a2a2d2-0b87-4a18-83f2-2529882be2de');

    // true

    $isUuid = Str::isUuid('laravel');

    // false

<a name="method-kebab-case"></a>
#### `Str::kebab()` {#collection-method}

The `Str::kebab` method converts the given string to `kebab-case`:

`Str::kebab` 메소드는 주어진 문자열을 `kebab-case`로 변환합니다.
(역자주 : 단어와 단어를 '-'로 연결한 형태)

    use Illuminate\Support\Str;

    $converted = Str::kebab('fooBar');

    // foo-bar

<a name="method-str-length"></a>
#### `Str::length()` {#collection-method}

The `Str::length` method returns the length of the given string:

 `Str::length` 메소드는 주어진 문자열을 지정된 길이를 반환합니다.

    use Illuminate\Support\Str;

    $length = Str::length('Laravel');

    // 7

<a name="method-str-limit"></a>
#### `Str::limit()` {#collection-method}

The `Str::limit` method truncates the given string at the specified length:

`Str::limit` 메소드는 주어진 문자열을 지정된 길이로 제한합니다.

    use Illuminate\Support\Str;

    $truncated = Str::limit('The quick brown fox jumps over the lazy dog', 20);

    // The quick brown fox...

You may also pass a third argument to change the string that will be appended to the end:

끝에 추가 될 문자열을 변경하기 위해 세 번째 인수를 전달할 수도 있습니다.

    use Illuminate\Support\Str;

    $truncated = Str::limit('The quick brown fox jumps over the lazy dog', 20, ' (...)');

    // The quick brown fox (...)

<a name="method-str-lower"></a>
#### `Str::lower()` {#collection-method}

The `Str::lower` method converts the given string to lowercase:

`Str::lower` 메소드는 주어진 문자열을 소문자로 변환합니다.

    use Illuminate\Support\Str;

    $converted = Str::lower('LARAVEL');

    // laravel

<a name="method-str-ordered-uuid"></a>
#### `Str::orderedUuid()` {#collection-method}

The `Str::orderedUuid` method generates a "timestamp first" UUID that may be efficiently stored in an indexed database column:

`Str::orderedUuid` 메소드는 인덱싱된 데이터베이스 컬럼에 효과적으로 저장될 수 있도록 "타임스탬프와 같은 정렬이 가능한" UUID를 생성합니다.

(역자주 : 이 헬퍼 메소드의 결과로 생성되는 UUID의 첫 번째 부분은 시간에 따라서 증감하는 형태를 보입니다.)

    use Illuminate\Support\Str;

    return (string) Str::orderedUuid();

<a name="method-str-padboth"></a>
#### `Str::padBoth()` {#collection-method}

The `Str::padBoth` method wraps PHP's `str_pad` function, padding both sides of a string with another:

`Str::padBoth` 메소드는 PHP의 `str_pad` 함수를 래핑하여 문자열의 양쪽을 다른 문자로 채웁니다.

    use Illuminate\Support\Str;

    $padded = Str::padBoth('James', 10, '_');

    // '__James___'

    $padded = Str::padBoth('James', 10);

    // '  James   '

<a name="method-str-padleft"></a>
#### `Str::padLeft()` {#collection-method}

The `Str::padLeft` method wraps PHP's `str_pad` function, padding the left side of a string with another:

`String::padLeft` 메서드는 PHP `str_pad` 함수를 래핑하여 문자열의 왼쪽을 다른 문자로 채웁니다.

    use Illuminate\Support\Str;

    $padded = Str::padLeft('James', 10, '-=');

    // '-=-=-James'

    $padded = Str::padLeft('James', 10);

    // '     James'

<a name="method-str-padright"></a>
#### `Str::padRight()` {#collection-method}

The `Str::padRight` method wraps PHP's `str_pad` function, padding the right side of a string with another:

`String::padRight` 메소드는 PHP `str_pad` 함수를 래핑하여 문자열의 오른쪽을 다른 문자로 채웁니다.

    use Illuminate\Support\Str;

    $padded = Str::padRight('James', 10, '-');

    // 'James-----'

    $padded = Str::padRight('James', 10);

    // 'James     '

<a name="method-str-plural"></a>
#### `Str::plural()` {#collection-method}

The `Str::plural` method converts a single word string to its plural form. This function currently only supports the English language:

`Str::plural` 메소드는 단수형을 복수형으로 변환합니다. 이 기능은 현재 영어만 지원합니다.

    use Illuminate\Support\Str;

    $plural = Str::plural('car');

    // cars

    $plural = Str::plural('child');

    // children

You may provide an integer as a second argument to the function to retrieve the singular or plural form of the string:

문자열의 단수 또는 복수 형식을 검색하기 위해 함수에 두 번째 인수로 정수를 제공할 수 있습니다.

    use Illuminate\Support\Str;

    $plural = Str::plural('child', 2);

    // children

    $plural = Str::plural('child', 1);

    // child

<a name="method-str-random"></a>
#### `Str::random()` {#collection-method}

The `Str::random` method generates a random string of the specified length. This function uses PHP's `random_bytes` function:

`Str::random` 메소드는 지정된 길이의 문자열을 무작위로 생성합니다. 이 함수는 PHP의 `random_bytes` 함수를 사용합니다.

    use Illuminate\Support\Str;

    $random = Str::random(40);

<a name="method-str-replace-array"></a>
#### `Str::replaceArray()` {#collection-method}

The `Str::replaceArray` method replaces a given value in the string sequentially using an array:

`Str::replaceArray` 메소드는 배열을 사용하여 문자열에서 주어진 값을 차례대로 바꿉니다.

    use Illuminate\Support\Str;

    $string = 'The event will take place between ? and ?';

    $replaced = Str::replaceArray('?', ['8:30', '9:00'], $string);

    // The event will take place between 8:30 and 9:00

<a name="method-str-replace-first"></a>
#### `Str::replaceFirst()` {#collection-method}

The `Str::replaceFirst` method replaces the first occurrence of a given value in a string:

`Str::replaceFirst` 메소드는 문자열에서 주어진 값이 발견된 첫 번째 부분을 대체합니다.

    use Illuminate\Support\Str;

    $replaced = Str::replaceFirst('the', 'a', 'the quick brown fox jumps over the lazy dog');

    // a quick brown fox jumps over the lazy dog

<a name="method-str-replace-last"></a>
#### `Str::replaceLast()` {#collection-method}

The `Str::replaceLast` method replaces the last occurrence of a given value in a string:

`Str::replaceLast` 메소드는 문자열에서 주어진 값이 발견된 마지막 부분을 대체합니다.

    use Illuminate\Support\Str;

    $replaced = Str::replaceLast('the', 'a', 'the quick brown fox jumps over the lazy dog');

    // the quick brown fox jumps over a lazy dog

<a name="method-str-singular"></a>
#### `Str::singular()` {#collection-method}

The `Str::singular` method converts a string to its singular form. This function currently only supports the English language:

`Str::singular` 메소드는 문자열을 단수 형식으로 변환합니다. 이 기능은 현재 영어만 지원합니다.

    use Illuminate\Support\Str;

    $singular = Str::singular('cars');

    // car

    $singular = Str::singular('children');

    // child

<a name="method-str-slug"></a>
#### `Str::slug()` {#collection-method}

The `Str::slug` method generates a URL friendly "slug" from the given string:

`Str::slug` 메소드는 주어진 문자열로부터 URL에 알맞은 "slug"를 생성합니다.

    use Illuminate\Support\Str;

    $slug = Str::slug('Laravel 5 Framework', '-');

    // laravel-5-framework

<a name="method-snake-case"></a>
#### `Str::snake()` {#collection-method}

The `Str::snake` method converts the given string to `snake_case`:

`Str::snake` 메소드는 주어진 문자열을 `snake_case` 형태로 변환합니다.

    use Illuminate\Support\Str;

    $converted = Str::snake('fooBar');

    // foo_bar

<a name="method-str-start"></a>
#### `Str::start()` {#collection-method}

The `Str::start` method adds a single instance of the given value to a string if it does not already start with the value:

`Str::start` 메소드는 문자열이 주어진 값으로 시작하지 않으면 이를 문자열에 추가합니다.

    use Illuminate\Support\Str;

    $adjusted = Str::start('this/string', '/');

    // /this/string

    $adjusted = Str::start('/this/string', '/');

    // /this/string

<a name="method-starts-with"></a>
#### `Str::startsWith()` {#collection-method}

The `Str::startsWith` method determines if the given string begins with the given value:

`Str::startsWith` 메소드는 문자열이 주어진 값으로 시작하는지를 확인합니다.

    use Illuminate\Support\Str;

    $result = Str::startsWith('This is my name', 'This');

    // true

<a name="method-studly-case"></a>
#### `Str::studly()` {#collection-method}

The `Str::studly` method converts the given string to `StudlyCase`:

`Str::studly` 메소드는 주어진 문자열을 `StudlyCase`로 변환합니다.

    use Illuminate\Support\Str;

    $converted = Str::studly('foo_bar');

    // FooBar

<a name="method-str-substr"></a>
#### `Str::substr()` {#collection-method}

The `Str::substr` method returns the portion of string specified by the start and length parameters:

`Str::substr` 메소드는 start 및 length 매개 변수로 지정된 문자열 부분을 리턴합니다.

    use Illuminate\Support\Str;

    $converted = Str::substr('The Laravel Framework', 4, 7);

    // Laravel

<a name="method-title-case"></a>
#### `Str::title()` {#collection-method}

The `Str::title` method converts the given string to `Title Case`:

`Str::title` 메소드는 주어진 문자열을 `Title Case`로 변환합니다.

    use Illuminate\Support\Str;

    $converted = Str::title('a nice title uses the correct case');

    // A Nice Title Uses The Correct Case

<a name="method-str-ucfirst"></a>
#### `Str::ucfirst()` {#collection-method}

The `Str::ucfirst` method returns the given string with the first character capitalized:

`Str::ucfirst` 메소드는 첫 문자를 대문자로하여 주어진 문자열을 반환합니다.

    use Illuminate\Support\Str;

    $string = Str::ucfirst('foo bar');

    // Foo bar

<a name="method-str-upper"></a>
#### `Str::upper()` {#collection-method}

The `Str::upper` method converts the given string to uppercase:

`Str::upper` 메소드는 주어진 문자열을 대문자로 변환합니다.

    use Illuminate\Support\Str;

    $string = Str::upper('laravel');

    // LARAVEL

<a name="method-str-uuid"></a>
#### `Str::uuid()` {#collection-method}

The `Str::uuid` method generates a UUID (version 4):

`Str::uuid` 메소드는 UUID (version 4)를 생성합니다.

    use Illuminate\Support\Str;

    return (string) Str::uuid();

<a name="method-str-words"></a>
#### `Str::words()` {#collection-method}

The `Str::words` method limits the number of words in a string:

`Str::words` 메소드는 문자열의 단어 수를 제한합니다.

    use Illuminate\Support\Str;

    return Str::words('Perfectly balanced, as all things should be.', 3, ' >>>');

    // Perfectly balanced, as >>>

<a name="method-trans"></a>
#### `trans()` {#collection-method}

The `trans` function translates the given translation key using your [localization files](/docs/{{version}}/localization):

`trans` 함수는 [다국어 파일](/docs/{{version}}/localization)을 이용하여 주어진 다국어 키를 변환합니다.

    echo trans('messages.welcome');

If the specified translation key does not exist, the `trans` function will return the given key. So, using the example above, the `trans` function would return `messages.welcome` if the translation key does not exist.

지정된 다국어 키가 존재하지 않는다면, `trans` 함수는 주어진 키를 반환합니다. 따라서 예제에서 다국어 키가 존재하지 않는다면 `trans` 함수는 `messages.welcome`을 그대로 반환합니다.

<a name="method-trans-choice"></a>
#### `trans_choice()` {#collection-method}

The `trans_choice` function translates the given translation key with inflection:

`trans_choice` 함수는 주어진, 수량(단수, 복수 처리)을 이용하여 주어진 다국어 키를 번역합니다.

    echo trans_choice('messages.notifications', $unreadCount);

If the specified translation key does not exist, the `trans_choice` function will return the given key. So, using the example above, the `trans_choice` function would return `messages.notifications` if the translation key does not exist.

지정된 다국어 키가 존재하지 않는다면, `trans_choice` 함수는 주어진 키를 반환합니다. 따라서 예제에서 다국어 키가 존재하지 않는다면 `trans_choice` 함수는 `messages.notifications`를 그대로 반환합니다.

<a name="fluent-strings"></a>
## Fluent Strings
## Fluent Strings

Fluent strings provide a more fluent, object-oriented interface for working with string values, allowing you to chain multiple string operations together using a more readable syntax compared to traditional string operations.

Fluent Strings는 문자열 값을 사용할 때보다 유창하고 객체 지향적인 인터페이스를 제공하므로, 기존 문자열의 작동보다 더 읽기 쉬운 구문을 사용하여 여러 문자열 연산을 함께 연결할 수 있습니다.

<a name="method-fluent-str-after"></a>
#### `after` {#collection-method}

The `after` method returns everything after the given value in a string. The entire string will be returned if the value does not exist within the string:

`after` 메소드는 주어진 값 이후의 모든 것을 반환합니다. 문자열 내에 값이 없으면 전체 문자열이 반환됩니다.

    use Illuminate\Support\Str;

    $slice = Str::of('This is my name')->after('This is');

    // ' my name'

<a name="method-fluent-str-after-last"></a>
#### `afterLast` {#collection-method}

The `afterLast` method returns everything after the last occurrence of the given value in a string. The entire string will be returned if the value does not exist within the string:

`afterLast` 메소드는 문자열에서 주어진 값이 마지막으로 나타난 후 모든 것을 반환합니다. 문자열 내에 값이 없으면 전체 문자열이 반환됩니다.

    use Illuminate\Support\Str;

    $slice = Str::of('App\Http\Controllers\Controller')->afterLast('\\');

    // 'Controller'

<a name="method-fluent-str-append"></a>
#### `append` {#collection-method}

The `append` method appends the given values to the string:

`append` 메소드는 주어진 값을 문자열에 추가합니다.

    use Illuminate\Support\Str;

    $string = Str::of('Taylor')->append(' Otwell');

    // 'Taylor Otwell'

<a name="method-fluent-str-ascii"></a>
#### `ascii` {#collection-method}

The `ascii` method will attempt to transliterate the string into an ASCII value:

`ascii` 메소드는 문자열을 ASCII 값으로 변환합니다.

    use Illuminate\Support\Str;

    $string = Str::of('ü')->ascii();

    // 'u'

<a name="method-fluent-str-basename"></a>
#### `basename` {#collection-method}

The `basename` method will return the trailing name component of the given string:

`basename` 메소드는 주어진 문자열의 마지막 구성 요소를 반환합니다.

    use Illuminate\Support\Str;

    $string = Str::of('/foo/bar/baz')->basename();

    // 'baz'

If needed, you may provide an "extension" that will be removed from the trailing component:

필요한 경우 마지막 구성 요소에서 제거할 "확장자"를 제공할 수 있습니다.

    use Illuminate\Support\Str;

    $string = Str::of('/foo/bar/baz.jpg')->basename('.jpg');

    // 'baz'

<a name="method-fluent-str-before"></a>
#### `before` {#collection-method}

The `before` method returns everything before the given value in a string:

`before` 메소드는 문자열에서 주어진 값 이전의 모든 것을 반환합니다.

    use Illuminate\Support\Str;

    $slice = Str::of('This is my name')->before('my name');

    // 'This is '

<a name="method-fluent-str-before-last"></a>
#### `beforeLast` {#collection-method}

The `beforeLast` method returns everything before the last occurrence of the given value in a string:

`beforeLast` 메소드는 문자열에서 주어진 값 이전의 모든 것을 반환합니다.

    use Illuminate\Support\Str;

    $slice = Str::of('This is my name')->beforeLast('is');

    // 'This '

<a name="method-fluent-str-camel"></a>
#### `camel` {#collection-method}

The `camel` method converts the given string to `camelCase`:

`camel` 메소드는 주어진 문자열을 `camelCase`로 변환합니다.

    use Illuminate\Support\Str;

    $converted = Str::of('foo_bar')->camel();

    // fooBar

<a name="method-fluent-str-contains"></a>
#### `contains` {#collection-method}

The `contains` method determines if the given string contains the given value (case sensitive):

`contains` 메소드는 주어진 문자열이 주어진 값을 포함하는지 확인합니다. (대소문자를 구분합니다.)

    use Illuminate\Support\Str;

    $contains = Str::of('This is my name')->contains('my');

    // true

You may also pass an array of values to determine if the given string contains any of the values:

또한, 주어진 문자열에 값이 포함되어 있는지 확인하기 위해 배열을 전달할 수도 있습니다.

    use Illuminate\Support\Str;

    $contains = Str::of('This is my name')->contains(['my', 'foo']);

    // true

<a name="method-fluent-str-contains-all"></a>
#### `containsAll` {#collection-method}

The `containsAll` method determines if the given string contains all array values:

`containsAll` 메소드는 주어진 문자열에 모든 배열 값이 포함되어 있는지 확인합니다.

    use Illuminate\Support\Str;

    $containsAll = Str::of('This is my name')->containsAll(['my', 'name']);

    // true

<a name="method-fluent-str-dirname"></a>
#### `dirname` {#collection-method}

The `dirname` method returns the parent directory portion of the given string:

`dirname` 메소드는 주어진 문자열의 부모 디렉토리를 반환합니다.

    use Illuminate\Support\Str;

    $string = Str::of('/foo/bar/baz')->dirname();

    // '/foo/bar'

Optionally, You may specify how many directory levels you wish to trim from the string:

선택적으로 문자열에서 자르려는 디렉토리 레벨 수를 지정할 수 있습니다.

    use Illuminate\Support\Str;

    $string = Str::of('/foo/bar/baz')->dirname(2);

    // '/foo'

<a name="method-fluent-str-ends-with"></a>
#### `endsWith` {#collection-method}

The `endsWith` method determines if the given string ends with the given value:

`endsWith` 메소드는 주어진 문자열이 주어진 값으로 끝나는지 확인합니다.

    use Illuminate\Support\Str;

    $result = Str::of('This is my name')->endsWith('name');

    // true

You may also pass an array of values to determine if the given string ends with any of the given values:

주어진 문자열이 주어진 값으로 끝나는지 확인하기 위해 배열을 전달할 수도 있습니다.

    use Illuminate\Support\Str;

    $result = Str::of('This is my name')->endsWith(['name', 'foo']);

    // true

    $result = Str::of('This is my name')->endsWith(['this', 'foo']);

    // false

<a name="method-fluent-str-exactly"></a>
#### `exactly` {#collection-method}

The `exactly` method determines if the given string is an exact match with another string:

`exactly` 메소드는 주어진 문자열이 다른 문자열과 정확히 일치하는지 확인합니다.

    use Illuminate\Support\Str;

    $result = Str::of('Laravel')->exactly('Laravel');

    // true

<a name="method-fluent-str-explode"></a>
#### `explode` {#collection-method}

The `explode` method splits the string by the given delimiter and returns a collection containing each section of the split string:

`explode` 메소드는 주어진 구분 기호로 문자열을 분할하고 분할 문자열의 각 섹션을 포함하는 컬렉션을 반환합니다.

    use Illuminate\Support\Str;

    $collection = Str::of('foo bar baz')->explode(' ');

    // collect(['foo', 'bar', 'baz'])

<a name="method-fluent-str-finish"></a>
#### `finish` {#collection-method}

The `finish` method adds a single instance of the given value to a string if it does not already end with the value:

`finish` 메소드는 문자열이 주어진 값으로 끝나지 않으면 해당 값을 추가합니다.

    use Illuminate\Support\Str;

    $adjusted = Str::of('this/string')->finish('/');

    // this/string/

    $adjusted = Str::of('this/string/')->finish('/');

    // this/string/

<a name="method-fluent-str-is"></a>
#### `is` {#collection-method}

The `is` method determines if a given string matches a given pattern. Asterisks may be used to indicate wildcards:

`is` 메소드는 주어진 문자열이 주어진 패턴과 일치하는지를 확인합니다. 와일드카드를 나타내는 데 별표를 사용할 수 있습니다.

    use Illuminate\Support\Str;

    $matches = Str::of('foobar')->is('foo*');

    // true

    $matches = Str::of('foobar')->is('baz*');

    // false

<a name="method-fluent-str-is-ascii"></a>
#### `isAscii` {#collection-method}

The `isAscii` method determines if a given string is an ASCII string:

`isAscii` 메소드는 주어진 문자열이 ASCII 문자열인지 확인합니다.

    use Illuminate\Support\Str;

    $result = Str::of('Taylor')->isAscii();

    // true

    $result = Str::of('ü')->isAscii();

    // false

<a name="method-fluent-str-is-empty"></a>
#### `isEmpty` {#collection-method}

The `isEmpty` method determines if the given string is empty:

`isEmpty` 메소드는 주어진 문자열이 비어 있는지 확인합니다.

    use Illuminate\Support\Str;

    $result = Str::of('  ')->trim()->isEmpty();

    // true

    $result = Str::of('Laravel')->trim()->isEmpty();

    // false

<a name="method-fluent-str-is-not-empty"></a>
#### `isNotEmpty` {#collection-method}

The `isNotEmpty` method determines if the given string is not empty:

`isNotEmpty` 메소드는 주어진 문자열이 비어 있지 않은지 확인합니다.


    use Illuminate\Support\Str;

    $result = Str::of('  ')->trim()->isNotEmpty();

    // false

    $result = Str::of('Laravel')->trim()->isNotEmpty();

    // true

<a name="method-fluent-str-kebab"></a>
#### `kebab` {#collection-method}

The `kebab` method converts the given string to `kebab-case`:

`kebab` 메소드는 주어진 문자열을 `kebab-case`로 변환합니다.

    use Illuminate\Support\Str;

    $converted = Str::of('fooBar')->kebab();

    // foo-bar

<a name="method-fluent-str-length"></a>
#### `length` {#collection-method}

The `length` method returns the length of the given string:

`length` 메소드는 주어진 문자열의 길이를 반환합니다.

    use Illuminate\Support\Str;

    $length = Str::of('Laravel')->length();

    // 7

<a name="method-fluent-str-limit"></a>
#### `limit` {#collection-method}

The `limit` method truncates the given string at the specified length:

`limit` 메소드는 주어진 문자열을 지정된 길이로 제한합니다.

    use Illuminate\Support\Str;

    $truncated = Str::of('The quick brown fox jumps over the lazy dog')->limit(20);

    // The quick brown fox...

You may also pass a second argument to change the string that will be appended to the end:

끝에 추가 될 문자열을 변경하기 위해 두 번째 인수를 전달할 수도 있습니다.

    use Illuminate\Support\Str;

    $truncated = Str::of('The quick brown fox jumps over the lazy dog')->limit(20, ' (...)');

    // The quick brown fox (...)

<a name="method-fluent-str-lower"></a>
#### `lower` {#collection-method}

The `lower` method converts the given string to lowercase:

`lower` 메소드는 주어진 문자열을 소문자로 변환합니다.

    use Illuminate\Support\Str;

    $result = Str::of('LARAVEL')->lower();

    // 'laravel'

<a name="method-fluent-str-ltrim"></a>
#### `ltrim` {#collection-method}

The `ltrim` method left trims the given string:

`ltrim` 메소드는 주어진 문자열 왼쪽의 공백문자를 제거합니다.

(역자주: 또는 하단과 같이 주어진 문자를 제거 합니다)

    use Illuminate\Support\Str;

    $string = Str::of('  Laravel  ')->ltrim();

    // 'Laravel  '

    $string = Str::of('/Laravel/')->ltrim('/');

    // 'Laravel/'

<a name="method-fluent-str-match"></a>
#### `match` {#collection-method}

The `match` method will return the portion of a string that matches a given regular expression pattern:

`match` 메소드는 주어진 정규식 패턴과 일치하는 문자열을 반환합니다.

    use Illuminate\Support\Str;

    $result = Str::of('foo bar')->match('/bar/');

    // 'bar'

    $result = Str::of('foo bar')->match('/foo (.*)/');

    // 'bar'

<a name="method-fluent-str-match-all"></a>
#### `matchAll` {#collection-method}

The `matchAll` method will return a collection containing the portions of a string that match a given regular expression pattern:

`matchAll` 메소드는 주어진 정규 표현식 패턴과 일치하는 문자열을 포함하는 컬렉션을 반환합니다.

    use Illuminate\Support\Str;

    $result = Str::of('bar fun bar fly')->matchAll('/f(\w*)/');

    // collect(['bar', 'bar'])

If you specify a matching group within the expression, Laravel will return a collection of that group's matches:

식 내에서 일치하는 그룹을 지정하면 Laravel은 해당 그룹의 일치하는 컬렉션을 반환합니다.

    use Illuminate\Support\Str;

    $result = Str::of('bar fun bar fly')->match('/f(\w*)/');

    // collect(['un', 'ly']);

If no matches are found, an empty collection will be returned.

일치하는 항목이 없으면 빈 컬렉션이 반환됩니다.

<a name="method-fluent-str-padboth"></a>
#### `padBoth` {#collection-method}

The `padBoth` method wraps PHP's `str_pad` function, padding both sides of a string with another:

`padBoth` 메소드는 PHP의 `str_pad` 함수를 래핑하여 문자열의 양쪽을 다른 문자로 채웁니다

    use Illuminate\Support\Str;

    $padded = Str::of('James')->padBoth(10, '_');

    // '__James___'

    $padded = Str::of('James')->padBoth(10);

    // '  James   '

<a name="method-fluent-str-padleft"></a>
#### `padLeft` {#collection-method}

The `padLeft` method wraps PHP's `str_pad` function, padding the left side of a string with another:

`padLeft` 메소드는 PHP의 `str_pad` 함수를 래핑하여 문자열의 왼쪽을 다른 문자로 채웁니다

    use Illuminate\Support\Str;

    $padded = Str::of('James')->padLeft(10, '-=');

    // '-=-=-James'

    $padded = Str::of('James')->padLeft(10);

    // '     James'

<a name="method-fluent-str-padright"></a>
#### `padRight` {#collection-method}

The `padRight` method wraps PHP's `str_pad` function, padding the right side of a string with another:

`padRight` 메소드는 PHP의 `str_pad` 함수를 래핑하여 문자열의 오른쪽을 다른 문자로 채웁니다

    use Illuminate\Support\Str;

    $padded = Str::of('James')->padRight(10, '-');

    // 'James-----'

    $padded = Str::of('James')->padRight(10);

    // 'James     '

<a name="method-fluent-str-plural"></a>
#### `plural` {#collection-method}

The `plural` method converts a single word string to its plural form. This function currently only supports the English language:

`plural` 메소드는 단수형을 복수형으로 변환합니다. 이 기능은 현재 영어만 지원합니다.

    use Illuminate\Support\Str;

    $plural = Str::of('car')->plural();

    // cars

    $plural = Str::of('child')->plural();

    // children

You may provide an integer as a second argument to the function to retrieve the singular or plural form of the string:

문자열의 단수 또는 복수 형식을 검색하기 위해 함수에 두 번째 인수로 정수를 제공할 수 있습니다.

    use Illuminate\Support\Str;

    $plural = Str::of('child')->plural(2);

    // children

    $plural = Str::of('child')->plural(1);

    // child

<a name="method-fluent-str-prepend"></a>
#### `prepend` {#collection-method}

The `prepend` method prepends the given values onto the string:

`prepend` 메소드는 주어진 값을 문자열 앞에 추가합니다.

    use Illuminate\Support\Str;

    $string = Str::of('Framework')->prepend('Laravel ');

    // Laravel Framework

<a name="method-fluent-str-replace"></a>
#### `replace` {#collection-method}

The `replace` method replaces a given string within the string:

`replace` 메소드는 문자열 내에서 주어진 문자열을 대체합니다.

    use Illuminate\Support\Str;

    $replaced = Str::of('Laravel 6.x')->replace('6.x', '7.x');

    // Laravel 7.x

<a name="method-fluent-str-replace-array"></a>
#### `replaceArray` {#collection-method}

The `replaceArray` method replaces a given value in the string sequentially using an array:

`replaceArray` 메소드는 배열을 사용하여 문자열에서 주어진 값을 차례대로 바꿉니다.

    use Illuminate\Support\Str;

    $string = 'The event will take place between ? and ?';

    $replaced = Str::of($string)->replaceArray('?', ['8:30', '9:00']);

    // The event will take place between 8:30 and 9:00

<a name="method-fluent-str-replace-first"></a>
#### `replaceFirst` {#collection-method}

The `replaceFirst` method replaces the first occurrence of a given value in a string:

`replaceFirst` 메소드는 문자열에서 주어진 값이 발견된 첫 번째 부분을 대체합니다.

    use Illuminate\Support\Str;

    $replaced = Str::of('the quick brown fox jumps over the lazy dog')->replaceFirst('the', 'a');

    // a quick brown fox jumps over the lazy dog

<a name="method-fluent-str-replace-last"></a>
#### `replaceLast` {#collection-method}

The `replaceLast` method replaces the last occurrence of a given value in a string:

`replaceLast` 메소드는 문자열에서 주어진 값이 발견된 마지막 부분을 대체합니다.

    use Illuminate\Support\Str;

    $replaced = Str::of('the quick brown fox jumps over the lazy dog')->replaceLast('the', 'a');

    // the quick brown fox jumps over a lazy dog

<a name="method-fluent-str-replace-matches"></a>
#### `replaceMatches` {#collection-method}

The `replaceMatches` method replaces all portions of a string matching a given pattern with the given replacement string:
 
`replaceMatches` 메소드는 주어진 패턴과 일치하는 문자열의 모든 부분을 주어진 대체 문자열로 변경합니다.

    use Illuminate\Support\Str;

    $replaced = Str::of('(+1) 501-555-1000')->replaceMatches('/[^A-Za-z0-9]++/', '')

    // '15015551000'

The `replaceMatches` method also accepts a Closure that will be invoked with each portion of the string matching the given party, allowing you to perform the replacement logic within the Closure and return the replaced value:

`replaceMatches` 메소드는 또한 주어진 파티에 일치하는 문자열의 각 부분에 대해 호출되는 클로저를 허용하므로 closure 내에서 대체 로직을 실행하고 대체된 값을 반환할 수 있습니다.

    use Illuminate\Support\Str;

    $replaced = Str::of('123')->replaceMatches('/\d/', function ($match) {
        return '['.$match[0].']';
    });

    // '[1][2][3]'

<a name="method-fluent-str-rtrim"></a>
#### `rtrim` {#collection-method}

The `rtrim` method right trims the given string:

`rtrim` 메소드는 주어진 문자열 오른쪽의 공백문자를 제거합니다.

(역자주: 또는 하단과 같이 주어진 문자를 제거 합니다)

    use Illuminate\Support\Str;

    $string = Str::of('  Laravel  ')->rtrim();

    // '  Laravel'

    $string = Str::of('/Laravel/')->rtrim('/');

    // '/Laravel'

<a name="method-fluent-str-singular"></a>
#### `singular` {#collection-method}

The `singular` method converts a string to its singular form. This function currently only supports the English language:

`singular` 메소드는 문자열을 단수 형식으로 변환합니다. 이 기능은 현재 영어만 지원합니다.

    use Illuminate\Support\Str;

    $singular = Str::of('cars')->singular();

    // car

    $singular = Str::of('children')->singular();

    // child

<a name="method-fluent-str-slug"></a>
#### `slug` {#collection-method}

The `slug` method generates a URL friendly "slug" from the given string:

`slug` 메소드는 주어진 문자열로부터 URL에 알맞는 "slug"를 생성합니다.

    use Illuminate\Support\Str;

    $slug = Str::of('Laravel Framework')->slug('-');

    // laravel-framework

<a name="method-fluent-str-snake"></a>
#### `snake` {#collection-method}

The `snake` method converts the given string to `snake_case`:

`snake` 메소드는 주어진 문자열을 `snake_case`로 변환합니다.

    use Illuminate\Support\Str;

    $converted = Str::of('fooBar')->snake();

    // foo_bar

<a name="method-fluent-str-split"></a>
#### `split` {#collection-method}

The `split` method splits a string into a collection using a regular expression:

    use Illuminate\Support\Str;

    $segments = Str::of('one, two, three')->split('/[\s,]+/');

    // collect(["one", "two", "three"])

<a name="method-fluent-str-start"></a>
#### `start` {#collection-method}

The `start` method adds a single instance of the given value to a string if it does not already start with the value:

`start` 메소드는 문자열이 주어진 값으로 시작하지 않으면 이를 문자열에 추가합니다.

    use Illuminate\Support\Str;

    $adjusted = Str::of('this/string')->start('/');

    // /this/string

    $adjusted = Str::of('/this/string')->start('/');

    // /this/string

<a name="method-fluent-str-starts-with"></a>
#### `startsWith` {#collection-method}

The `startsWith` method determines if the given string begins with the given value:

`startsWith` 메소드는 주어진 문자열이 주어진 값으로 시작하는지를 확인합니다.

    use Illuminate\Support\Str;

    $result = Str::of('This is my name')->startsWith('This');

    // true

<a name="method-fluent-str-studly"></a>
#### `studly` {#collection-method}

The `studly` method converts the given string to `StudlyCase`:

`studly` 메소드는 주어진 문자열을 `StudlyCase`로 변환합니다.

    use Illuminate\Support\Str;

    $converted = Str::of('foo_bar')->studly();

    // FooBar

<a name="method-fluent-str-substr"></a>
#### `substr` {#collection-method}

The `substr` method returns the portion of the string specified by the given start and length parameters:

`substr` 메소드는 주어진 시작 및 길이 파라미터에 의해 지정된 문자열 부분을 반환합니다.

    use Illuminate\Support\Str;

    $string = Str::of('Laravel Framework')->substr(8);

    // Framework

    $string = Str::of('Laravel Framework')->substr(8, 5);

    // Frame

<a name="method-fluent-str-title"></a>
#### `title` {#collection-method}

The `title` method converts the given string to `Title Case`:

`title` 메소드는 주어진 문자열을 `Title Case`로 변환합니다.

    use Illuminate\Support\Str;

    $converted = Str::of('a nice title uses the correct case')->title();

    // A Nice Title Uses The Correct Case

<a name="method-fluent-str-trim"></a>
#### `trim` {#collection-method}

The `trim` method trims the given string:

`trim` 메소드는 주어진 문자열을 잘라냅니다.

    use Illuminate\Support\Str;

    $string = Str::of('  Laravel  ')->trim();

    // 'Laravel'

    $string = Str::of('/Laravel/')->trim('/');

    // 'Laravel'

<a name="method-fluent-str-ucfirst"></a>
#### `ucfirst` {#collection-method}

The `ucfirst` method returns the given string with the first character capitalized:

`ucfirst` 메소드는 첫 문자를 대문자로 하여 주어진 문자열을 반환합니다.

    use Illuminate\Support\Str;

    $string = Str::of('foo bar')->ucfirst();

    // Foo bar

<a name="method-fluent-str-upper"></a>
#### `upper` {#collection-method}

The `upper` method converts the given string to uppercase:

`upper` 메소드는 주어진 문자열을 대문자로 변환합니다.

    use Illuminate\Support\Str;

    $adjusted = Str::of('laravel')->upper();

    // LARAVEL

<a name="method-fluent-str-when"></a>
#### `when` {#collection-method}

The `when` method invokes the given Closure if a given condition is true. The Closure will receive the fluent string instance:

`when` 메소드는 주어진 조건이 참이면 주어진 Closure를 호출합니다. Closure는 fluent string 인스턴스를받습니다.

    use Illuminate\Support\Str;

    $string = Str::of('Taylor')
                    ->when(true, function ($string) {
                        return $string->append(' Otwell');
                    });

    // 'Taylor Otwell'

If necessary, you may pass another Closure as the third parameter to the `when` method. This Closure will execute if the condition parameter evaluates to `false`.

필요한 경우 `when` 메소드의 세 번째 매개 변수로 다른 Closure를 전달할 수 있습니다. 조건 매개 변수가 `false`로 판별되면 이 Closure가 실행됩니다.

<a name="method-fluent-str-when-empty"></a>
#### `whenEmpty` {#collection-method}

The `whenEmpty` method invokes the given Closure if the string is empty. If the Closure returns a value, that value will also be returned by the `whenEmpty` method. If the Closure does not return a value, the fluent string instance will be returned:

`whenEmpty` 메소드는 문자열이 비어 있으면 주어진 클로저를 호출합니다. 클로저가 값을 반환하면 그 값도 `whenEmpty` 메소드에 의해 반환됩니다. 클로저가 값을 반환하지 않으면 fluent string 인스턴스가 반환됩니다.

    use Illuminate\Support\Str;

    $string = Str::of('  ')->whenEmpty(function ($string) {
        return $string->trim()->prepend('Laravel');
    });

    // 'Laravel'

<a name="method-fluent-str-words"></a>
#### `words` {#collection-method}

The `words` method limits the number of words in a string:

`words` 메소드는 문자열의 단어 수를 제한합니다.

    use Illuminate\Support\Str;

    $string = Str::of('Perfectly balanced, as all things should be.')->words(3, ' >>>');

    // Perfectly balanced, as >>>

<a name="urls"></a>
## URLs

<a name="method-action"></a>
#### `action()` {#collection-method}

The `action` function generates a URL for the given controller action:

`action` 함수는 주어진 컨트롤러 메소드로 URL을 생성합니다.

    $url = action([HomeController::class, 'index']);

If the method accepts route parameters, you may pass them as the second argument to the method:

메소드가 라우트 파라미터를 받아들인다면, 두 번째 인자로 메소드에 전달하십시오.

    $url = action([UserController::class, 'profile'], ['id' => 1]);

<a name="method-asset"></a>
#### `asset()` {#collection-method}

The `asset` function generates a URL for an asset using the current scheme of the request (HTTP or HTTPS):

`asset` 함수는 HTTP요청의 현재 scheme(HTTP나 HTTPS)을 이용하여 asset을 사용하기 위한 URL을 생성합니다.

    $url = asset('img/photo.jpg');

You can configure the asset URL host by setting the `ASSET_URL` variable in your `.env` file. This can be useful if you host your assets on an external service like Amazon S3:

`.env` 파일에 `ASSET_URL` 변수를 설정하여 asset의 URL 호스트를 설정할 수 있습니다. 이는 Amazon S3와 같은 외부 서비스에서 asset을 호스팅하는 경우 유용 할 수 있습니다.

    // ASSET_URL=http://example.com/assets

    $url = asset('img/photo.jpg'); // http://example.com/assets/img/photo.jpg

<a name="method-route"></a>
#### `route()` {#collection-method}

The `route` function generates a URL for the given named route:

`route` 함수는 주어진 라우트 이름으로 URL을 생성합니다.

    $url = route('routeName');

If the route accepts parameters, you may pass them as the second argument to the method:

라우트가 파라미터를 가진다면 파라미터를 두 번째 인자로 메소드에 전달하세요.

    $url = route('routeName', ['id' => 1]);

By default, the `route` function generates an absolute URL. If you wish to generate a relative URL, you may pass `false` as the third argument:

기본적으로 `route` 함수는 절대 경로 URL을 생성합니다. 만약 상태경로의 URL을 생성하려면 세번째 인자를 `false`로 전달하면 됩니다.

    $url = route('routeName', ['id' => 1], false);

<a name="method-secure-asset"></a>
#### `secure_asset()` {#collection-method}

The `secure_asset` function generates a URL for an asset using HTTPS:

`secure_asset` 함수는 HTTPS를 이용하여 asset을 사용하기 위한 URL을 생성합니다.

    $url = secure_asset('img/photo.jpg');

<a name="method-secure-url"></a>
#### `secure_url()` {#collection-method}

The `secure_url` function generates a fully qualified HTTPS URL to the given path:

`secure_url` 함수는 주어진 경로에 대한 전체 HTTPS URL을 생성합니다.

    $url = secure_url('user/profile');

    $url = secure_url('user/profile', [1]);

<a name="method-url"></a>
#### `url()` {#collection-method}

The `url` function generates a fully qualified URL to the given path:

`url` 함수는 주어진 경로에 대한 전체 URL을 생성합니다.

    $url = url('user/profile');

    $url = url('user/profile', [1]);

If no path is provided, a `Illuminate\Routing\UrlGenerator` instance is returned:

경로를 전달하지 않으면, `Illuminate\Routing\UrlGenerator` 인스턴스가 반환됩니다.

    $current = url()->current();

    $full = url()->full();

    $previous = url()->previous();

<a name="miscellaneous"></a>
## Miscellaneous
## 기타 함수들

<a name="method-abort"></a>
#### `abort()` {#collection-method}

The `abort` function throws [an HTTP exception](/docs/{{version}}/errors#http-exceptions) which will be rendered by the [exception handler](/docs/{{version}}/errors#the-exception-handler):

`abort` 함수는 [Exception 핸들러](/docs/{{version}}/errors#the-exception-handler)에 의해서 렌더링 될 수 있는 [HTTP exception](/docs/{{version}}/errors#http-exceptions)을 발생시킵니다.

    abort(403);

You may also provide the exception's message and custom response headers that should be sent to the browser:

브라우저에 보내야 하는 exception 의 메세지를 제공하거나, 커스텀 헤더를 지정할 수도 있습니다.

    abort(403, 'Unauthorized.', $headers);

<a name="method-abort-if"></a>
#### `abort_if()` {#collection-method}

The `abort_if` function throws an HTTP exception if a given boolean expression evaluates to `true`:

`abort_if` 함수는 주어진 조건식이 `true` 일때 HTTP exception을 발생시킵니다.

    abort_if(! Auth::user()->isAdmin(), 403);

Like the `abort` method, you may also provide the exception's response text as the third argument and an array of custom response headers as the fourth argument.

`abort` 메소드와 같이, exception 의 응답 텍스트를 세번째 인자로 전달할 수 있으며, 네번째 인자로 커스텀 응답 헤더를 전달할 수도 있습니다.

<a name="method-abort-unless"></a>
#### `abort_unless()` {#collection-method}

The `abort_unless` function throws an HTTP exception if a given boolean expression evaluates to `false`:

`abort_unless` 함수는 주어진 조건식이 `false` 일때 HTTP exception 을 발생시킵니다.

    abort_unless(Auth::user()->isAdmin(), 403);

Like the `abort` method, you may also provide the exception's response text as the third argument and an array of custom response headers as the fourth argument.

`abort` 메소드와 같이, exception 의 응답 텍스트를 세번째 인자로 전달할 수 있으며, 네번째 인자로 커스텀 응답 헤더를 전달할 수도 있습니다.

<a name="method-app"></a>
#### `app()` {#collection-method}

The `app` function returns the [service container](/docs/{{version}}/container) instance:

`app` 함수는 [서비스 컨테이너](/docs/{{version}}/container) 인스턴스를 반환합니다.

    $container = app();

You may pass a class or interface name to resolve it from the container:

컨테이너에 의존성을 해결하고자 하는 클래스나 인터페이스의 이름을 전달할 수도 있습니다.

    $api = app('HelpSpot\API');

<a name="method-auth"></a>
#### `auth()` {#collection-method}

The `auth` function returns an [authenticator](/docs/{{version}}/authentication) instance. You may use it instead of the `Auth` facade for convenience:

`auth` 함수는 [authenticator](/docs/{{version}}/authentication) 인스턴스를 반환합니다. 편의를 위하여 `Auth` 파사드 대신 이용할 수 있습니다.

    $user = auth()->user();

If needed, you may specify which guard instance you would like to access:

필요한 경우, 어떤 guard 인스턴스를 사용할지 지정할 수 있습니다.

    $user = auth('admin')->user();

<a name="method-back"></a>
#### `back()` {#collection-method}

The `back` function generates a [redirect HTTP response](/docs/{{version}}/responses#redirects) to the user's previous location:

`back()` 함수는 사용자의 이전 위치로 [리다이렉트 HTTP response-응답](/docs/{{version}}/responses#redirects)을 생성합니다.

    return back($status = 302, $headers = [], $fallback = false);

    return back();

<a name="method-bcrypt"></a>
#### `bcrypt()` {#collection-method}

The `bcrypt` function [hashes](/docs/{{version}}/hashing) the given value using Bcrypt. You may use it as an alternative to the `Hash` facade:

`bcrypt` 함수는 Bcrypt를 이용하여 주어진 값을 [해시](/docs/{{version}}/hashing) 처리합니다. `Hash` 파사드 대신 사용할 수 있습니다.

    $password = bcrypt('my-secret-password');

<a name="method-blank"></a>
#### `blank()` {#collection-method}

The `blank` function returns whether the given value is "blank":

`blank` 함수는 주어진 값이 "빈값"인지 판단하여 결과를 반환합니다.

    blank('');
    blank('   ');
    blank(null);
    blank(collect());

    // true

    blank(0);
    blank(true);
    blank(false);

    // false

For the inverse of `blank`, see the [`filled`](#method-filled) method.

`blank`의 반대는, [`filled`](#method-filled) 메소드를 참고하십시오.

<a name="method-broadcast"></a>
#### `broadcast()` {#collection-method}

The `broadcast` function [broadcasts](/docs/{{version}}/broadcasting) the given [event](/docs/{{version}}/events) to its listeners:

`broadcast` 함수는 주어진 [이벤트](/docs/{{version}}/events)를 리스너들에게 [broadcasts](/docs/{{version}}/broadcasting) 합니다.

    broadcast(new UserRegistered($user));

<a name="method-cache"></a>
#### `cache()` {#collection-method}

The `cache` function may be used to get values from the [cache](/docs/{{version}}/cache). If the given key does not exist in the cache, an optional default value will be returned:

`cache` 함수는 [캐시](/docs/{{version}}/cache)로 부터 값을 가져오는데 사용할 수 있습니다. 캐시에서 주어진 키가 존재하지 않는 경우, 옵션으로 전달된 기본값(두 번째인자)가 반환됩니다

    $value = cache('key');

    $value = cache('key', 'default');

You may add items to the cache by passing an array of key / value pairs to the function. You should also pass the number of seconds or duration the cached value should be considered valid:

함수에 키 / 값으로 된 배열을 전달하여 캐시에 아이템을 추가할 수 있습니다. 또한 캐시에 값이 얼마나 유지되어야 하는지에 대한 시간(초)을 숫자로 전달할 수도 있습니다.

    cache(['key' => 'value'], 300);

    cache(['key' => 'value'], now()->addSeconds(10));

<a name="method-class-uses-recursive"></a>
#### `class_uses_recursive()` {#collection-method}

The `class_uses_recursive` function returns all traits used by a class, including traits used by all of its parent classes:

`class_uses_recursive` 함수는 모든 부모 클래스가 사용하는 trait를 포함하여 클래스가 사용하는 모든 trait을 반환합니다.

    $traits = class_uses_recursive(App\Models\User::class);

<a name="method-collect"></a>
#### `collect()` {#collection-method}

The `collect` function creates a [collection](/docs/{{version}}/collections) instance from the given value:

`collect` 함수는 주어진 값으로부터 [collection](/docs/{{version}}/collections) 인스턴스를 생성합니다.

    $collection = collect(['taylor', 'abigail']);

<a name="method-config"></a>
#### `config()` {#collection-method}

The `config` function gets the value of a [configuration](/docs/{{version}}/configuration) variable. The configuration values may be accessed using "dot" syntax, which includes the name of the file and the option you wish to access. A default value may be specified and is returned if the configuration option does not exist:

`config` 함수는 [설정](/docs/{{version}}/configuration) 변수의 값을 가져옵니다. 설정 값은 파일명과 접근하고자 하는 옵션을 포함하는 "점(.)" 문법(syntax)를 사용하여 접근할 수 있습니다. 설정 옵션이 존재하지 않는다면 지정된 기본값이 반환됩니다.

    $value = config('app.timezone');

    $value = config('app.timezone', $default);


You may set configuration variables at runtime by passing an array of key / value pairs:

런타임에 설정 값을 키 / 값 형태로 전달하여 값을 설정할 수도 있습니다.

    config(['app.debug' => true]);

<a name="method-cookie"></a>
#### `cookie()` {#collection-method}

The `cookie` function creates a new [cookie](/docs/{{version}}/requests#cookies) instance:

`cookie` 함수는 새로운 [쿠키](/docs/{{version}}/requests#cookies) 인스턴스를 생성합니다.

    $cookie = cookie('name', 'value', $minutes);

<a name="method-csrf-field"></a>
#### `csrf_field()` {#collection-method}

The `csrf_field` function generates an HTML `hidden` input field containing the value of the CSRF token. For example, using [Blade syntax](/docs/{{version}}/blade):

`csrf_field` 함수는 CSRF 토큰 값을 포함하는 HTML `hidden` Input 필드를 생성합니다. 예를 들어 [Blade syntax](/docs/{{version}}/blade)에서 사용할 수 있습니다.

    {{ csrf_field() }}

<a name="method-csrf-token"></a>
#### `csrf_token()` {#collection-method}

The `csrf_token` function retrieves the value of the current CSRF token:

`csrf_token` 함수는 현재 CSRF 토큰의 값을 조회합니다.

    $token = csrf_token();

<a name="method-dd"></a>
#### `dd()` {#collection-method}

The `dd` function dumps the given variables and ends execution of the script:

`dd` 함수는 주어진 변수들을 Dump 처리하고 스크립트의 실행을 중단합니다.

    dd($value);

    dd($value1, $value2, $value3, ...);

If you do not want to halt the execution of your script, use the [`dump`](#method-dump) function instead.

스크립트 실행을 중단하고 싶지 않다면, [`dump`](#method-dump) 함수를 사용하십시오:

<a name="method-dispatch"></a>
#### `dispatch()` {#collection-method}

The `dispatch` function pushes the given [job](/docs/{{version}}/queues#creating-jobs) onto the Laravel [job queue](/docs/{{version}}/queues):

`dispatch` 함수는 라라벨의 [job queue](/docs/{{version}}/queues)에 주어진 [job](/docs/{{version}}/queues#creating-jobs)을 추가합니다.

    dispatch(new App\Jobs\SendEmails);

<a name="method-dispatch-now"></a>
#### `dispatch_now()` {#collection-method}

The `dispatch_now` function runs the given [job](/docs/{{version}}/queues#creating-jobs) immediately and returns the value from its `handle` method:

`dispatch_now` 함수는 주어진 [job](/docs/{{version}}/queues#creating-jobs)을 즉시 실행하고 `handle` 메소드의 값을 반환합니다.

    $result = dispatch_now(new App\Jobs\SendEmails);

<a name="method-dump"></a>
#### `dump()` {#collection-method}

The `dump` function dumps the given variables:

`dump` 함수는 주어진 변수의 값을 덤프하여 표시합니다.

    dump($value);

    dump($value1, $value2, $value3, ...);

If you want to stop executing the script after dumping the variables, use the [`dd`](#method-dd) function instead.

변수읙 값을 표시한 다음에 스크립트의 실행을 멈추고자 한다면, [`dd`](#method-dd)함수를 사용하십시오.

<a name="method-env"></a>
#### `env()` {#collection-method}

The `env` function retrieves the value of an [environment variable](/docs/{{version}}/configuration#environment-configuration) or returns a default value:

`env` 함수는 [환경변수](/docs/{{version}}/configuration#environment-configuration)의 값을 가져오거나 기본값을 조회합니다.

    $env = env('APP_ENV');

    // Returns 'production' if APP_ENV is not set...
    $env = env('APP_ENV', 'production');

> {note} If you execute the `config:cache` command during your deployment process, you should be sure that you are only calling the `env` function from within your configuration files. Once the configuration has been cached, the `.env` file will not be loaded and all calls to the `env` function will return `null`.

> {note} 배포과정에서 `config:cache` 명령어를 실행했다면, 설정 파일안에서 `env` 함수를 호출한 부분이 있는지 확인해야 합니다. 설정이 캐싱되고 나면, `.env` 파일은 로드하지 않고, 모든 `env` 함수는 `null`을 반환합니다.

<a name="method-event"></a>
#### `event()` {#collection-method}

The `event` function dispatches the given [event](/docs/{{version}}/events) to its listeners:

`event` 함수는 주어진 [event](/docs/{{version}}/events)를 리스너들에게 보냅니다.

    event(new UserRegistered($user));

<a name="method-factory"></a>
#### `factory()` {#collection-method}

The `factory` function creates a model factory builder for a given class, name, and amount. It can be used while [testing](/docs/{{version}}/database-testing#writing-factories) or [seeding](/docs/{{version}}/seeding#using-model-factories):

`factory` 함수는 주어진 클래스, 이름, 양을 위한 모델 팩토리 빌더를 생성합니다. [testing](/docs/{{version}}/database-testing#writing-factories)이나 [seeding](/docs/{{version}}/seeding#using-model-factories) 중에 이용할 수 있습니다.

    $user = factory(App\User::class)->make();

<a name="method-filled"></a>
#### `filled()` {#collection-method}

The `filled` function returns whether the given value is not "blank":

`filled` 함수는 주어진 값이 "빈값"이 아닌지 판별하여 결과를 반환합니다.

    filled(0);
    filled(true);
    filled(false);

    // true

    filled('');
    filled('   ');
    filled(null);
    filled(collect());

    // false

For the inverse of `filled`, see the [`blank`](#method-blank) method.

`filled`의 반대는, [`blank`](#method-blank) 메소드를 확인하십시오.

<a name="method-info"></a>
#### `info()` {#collection-method}

The `info` function will write information to the [log](/docs/{{version}}/logging):

`info` 함수는 [로그](/docs/{{version}}/logging)에 정보를 기록합니다.

    info('Some helpful information!');

An array of contextual data may also be passed to the function:

문맥에대한 데이터를 함수에 배열로 전달할 수도 있습니다.

    info('User login attempt failed.', ['id' => $user->id]);

<a name="method-logger"></a>
#### `logger()` {#collection-method}

The `logger` function can be used to write a `debug` level message to the [log](/docs/{{version}}/logging):

`logger` 함수는 [로그](/docs/{{version}}/logging)에 `debug` 로그 레벨을 기록하는데 사용합니다.

    logger('Debug message');

An array of contextual data may also be passed to the function:

문맥에대한 데이터를 함수에 배열로 전달할 수도 있습니다.

    logger('User has logged in.', ['id' => $user->id]);

A [logger](/docs/{{version}}/errors#logging) instance will be returned if no value is passed to the function:

함수에 아무런 값이 전달되지 않으면 [logger](/docs/{{version}}/errors#logging) 인스턴스가 반환됩니다.

    logger()->error('You are not allowed here.');

<a name="method-method-field"></a>
#### `method_field()` {#collection-method}

The `method_field` function generates an HTML `hidden` input field containing the spoofed value of the form's HTTP verb. For example, using [Blade syntax](/docs/{{version}}/blade):

`method_field` 함수는 HTTP 메소드 형식의 가짜(spoof) 값을 포함하는 HTML `hidden` Input 필드를 생성합니다. 예를 들어 [Blade syntax](/docs/{{version}}/blade)에서 사용할 수 있습니다.

    <form method="POST">
        {{ method_field('DELETE') }}
    </form>

<a name="method-now"></a>
#### `now()` {#collection-method}

The `now` function creates a new `Illuminate\Support\Carbon` instance for the current time:

`now` 함수는 현재 시간을 기반으로한 `Illuminate\Support\Carbon` 인스턴스를 생성합니다.

    $now = now();

<a name="method-old"></a>
#### `old()` {#collection-method}

The `old` function [retrieves](/docs/{{version}}/requests#retrieving-input) an [old input](/docs/{{version}}/requests#old-input) value flashed into the session:

`old` 함수는 세션에 저장된 [이전 입력값](/docs/{{version}}/requests#old-input)(flashed)을 [조회](/docs/{{version}}/requests#retrieving-input)합니다.
    $value = old('value');

    $value = old('value', 'default');

<a name="method-optional"></a>
#### `optional()` {#collection-method}

The `optional` function accepts any argument and allows you to access properties or call methods on that object. If the given object is `null`, properties and methods will return `null` instead of causing an error:

`optional` 함수는 인자를 전달받아 해당 객체의 프로퍼티에 엑세스 하거나, 메소드를 호출할 수 있도록 합니다. 지정된 객체가 `null` 이라면, 프로퍼티와 메소드는 에러를 유발하는 대신에 `null` 을 반환합니다.

    return optional($user->address)->street;

    {!! old('name', optional($user)->name) !!}

The `optional` function also accepts a Closure as its second argument. The Closure will be invoked if the value provided as the first argument is not null:

`optional` 함수는 두 번째 인자로 클로저를 받을 수 있습니다. 첫 번째 인자가 `null`이 아닌경우, 클로저가 호출됩니다.

    return optional(User::find($id), function ($user) {
        return new DummyUser;
    });

<a name="method-policy"></a>
#### `policy()` {#collection-method}

The `policy` method retrieves a [policy](/docs/{{version}}/authorization#creating-policies) instance for a given class:

`policy` 메소도는 주어진 클래스를 위한 [policy](/docs/{{version}}/authorization#creating-policies) 인스턴스를 조회합니다.

    $policy = policy(App\Models\User::class);

<a name="method-redirect"></a>
#### `redirect()` {#collection-method}

The `redirect` function returns a [redirect HTTP response](/docs/{{version}}/responses#redirects), or returns the redirector instance if called with no arguments:

`redirect` 함수는 [리다이렉트 HTTP response-응답](/docs/{{version}}/responses#redirects)을 반환하거나, 인자 없이 호출되는 경우 리디렉터 인스턴스를 반환합니다.

    return redirect($to = null, $status = 302, $headers = [], $secure = null);

    return redirect('/home');

    return redirect()->route('route.name');

<a name="method-report"></a>
#### `report()` {#collection-method}

The `report` function will report an exception using your [exception handler](/docs/{{version}}/errors#the-exception-handler):

`report` 함수는 [exception 핸들러](/docs/{{version}}/errors#the-exception-handler)를 예외(exception)를 보고합니다.

    report($e);

<a name="method-request"></a>
#### `request()` {#collection-method}

The `request` function returns the current [request](/docs/{{version}}/requests) instance or obtains an input item:

`request` 함수는 현재의 [요청](/docs/{{version}}/requests) 인스턴스를 반환하거나 입력 아이템을 가져옵니다.

    $request = request();

    $value = request('key', $default);

<a name="method-rescue"></a>
#### `rescue()` {#collection-method}

The `rescue` function executes the given Closure and catches any exceptions that occur during its execution. All exceptions that are caught will be sent to your [exception handler](/docs/{{version}}/errors#the-exception-handler); however, the request will continue processing:

`rescue` 함수는 주어진 클로저를 실행하고, 실행중 발생하는 예외(exception)을 받아냅니다. catch 된 모든 예외(exception) [exception 핸들러](/docs/{{version}}/errors#the-exception-handler)의 요청(request)에 대한 처리는 계속됩니다.

    return rescue(function () {
        return $this->method();
    });

You may also pass a second argument to the `rescue` function. This argument will be the "default" value that should be returned if an exception occurs while executing the Closure:

`rescue` 함수에는 두 번째 인자를 전달 할 수 있는데, 이 인자는 클로저를 실행하는 동안 예외-exception가 발생하면 반환된 "기본값" 입니다.

    return rescue(function () {
        return $this->method();
    }, false);

    return rescue(function () {
        return $this->method();
    }, function () {
        return $this->failure();
    });

<a name="method-resolve"></a>
#### `resolve()` {#collection-method}

The `resolve` function resolves a given class or interface name to its instance using the [service container](/docs/{{version}}/container):

`resolve` 함수는 [서비스 컨테이너](/docs/{{version}}/container)를 사용하여 주어진 클래스 또는 인터페이스를 의존성 해결하여 인스턴스를 반환합니다.

    $api = resolve('HelpSpot\API');

<a name="method-response"></a>
#### `response()` {#collection-method}

The `response` function creates a [response](/docs/{{version}}/responses) instance or obtains an instance of the response factory:

`response` 함수는 [응답](/docs/{{version}}/responses) 인스턴스를 생성하거나 응답 팩토리의 인스턴스를 가져옵니다.

    return response('Hello World', 200, $headers);

    return response()->json(['foo' => 'bar'], 200, $headers);

<a name="method-retry"></a>
#### `retry()` {#collection-method}

The `retry` function attempts to execute the given callback until the given maximum attempt threshold is met. If the callback does not throw an exception, its return value will be returned. If the callback throws an exception, it will automatically be retried. If the maximum attempt count is exceeded, the exception will be thrown:

`retry` 함수는 주어진 최대 횟수가 될 때까지 주어진 콜백을 실행하려고 시도합니다. 콜백이 예외-exception를 던지지 않는다면, 결과 값이 반환됩니다. 콜백이 예외-exception를 던지면 자동으로 다시 시도됩니다. 최대 재시도 횟수를 초과하면 예외가 던져집니다.

    return retry(5, function () {
        // Attempt 5 times while resting 100ms in between attempts...
    }, 100);

<a name="method-session"></a>
#### `session()` {#collection-method}

The `session` function may be used to get or set [session](/docs/{{version}}/session) values:

`session` 함수는 [세션](/docs/{{version}}/session) 값을 얻거나 지정하는 데에 사용할 수 있습니다.

    $value = session('key');

You may set values by passing an array of key / value pairs to the function:

키 / 값 쌍들의 배열을 함수로 전달하여 값을 설정할 수 있습니다.

    session(['chairs' => 7, 'instruments' => 3]);

The session store will be returned if no value is passed to the function:

함수에 아무런 값도 전달되지 않는다면 세션 스토어가 반환됩니다.

    $value = session()->get('key');

    session()->put('key', $value);

<a name="method-tap"></a>
#### `tap()` {#collection-method}

The `tap` function accepts two arguments: an arbitrary `$value` and a Closure. The `$value` will be passed to the Closure and then be returned by the `tap` function. The return value of the Closure is irrelevant:

`tap` 함수는 임의의 `$value` 와 클로저 두개의 인자를 받아들입니다. `$value` 는 클로저에 전달되어 `tap` 함수에 의해서 반환됩니다. 반환되는 값은 클로저와 무관합니다.

    $user = tap(User::first(), function ($user) {
        $user->name = 'taylor';

        $user->save();
    });

If no Closure is passed to the `tap` function, you may call any method on the given `$value`. The return value of the method you call will always be `$value`, regardless of what the method actually returns in its definition. For example, the Eloquent `update` method typically returns an integer. However, we can force the method to return the model itself by chaining the `update` method call through the `tap` function:

`tap` 함수에 클로저를 전달하지 않는다면, 주어진 `$value` 에 모든 메소드를 호출할 수 있습니다. 메소드에서 반환하는 값은 메소드가 실제로 정의해서 반환하는 `$value` 와는 관계없이 항상 `$value` 가 됩니다. 예를 들어 Eloquent update 메소드는 일반적으로 정수값을 반환하지만, `tap` 메소드를 통해서 `update` 메소드를 호출을 체이닝하면 메소드가 모델 그 자체를 반환하도록 할 수 있습니다.

    $user = tap($user)->update([
        'name' => $name,
        'email' => $email,
    ]);

To add a `tap` method to a class, you may add the `Illuminate\Support\Traits\Tappable` trait to the class. The `tap` method of this trait accepts a Closure as its only argument. The object instance itself will be passed to the Closure and then be returned by the `tap` method:

클래스에 `tap` 메소드를 추가하기 위해서는 `Illuminate\Support\Traits\Tappable` Trait을 클래스에 추가하면됩니다. 이 Trait의 `tap` 메소드는 Closure만 인수로 받아들입니다. 객체 인스턴스 자체는 Closure에 전달되고 `tap` 메소드에 의해 반환됩니다.

    return $user->tap(function ($user) {
        //
    });

<a name="method-throw-if"></a>
#### `throw_if()` {#collection-method}

The `throw_if` function throws the given exception if a given boolean expression evaluates to `true`:

`throw_if` 함수는 주어진 결과가 `true` 인 경우에 주어진 exception-예외를 던집니다.

    throw_if(! Auth::user()->isAdmin(), AuthorizationException::class);

    throw_if(
        ! Auth::user()->isAdmin(),
        AuthorizationException::class,
        'You are not allowed to access this page'
    );

<a name="method-throw-unless"></a>
#### `throw_unless()` {#collection-method}

The `throw_unless` function throws the given exception if a given boolean expression evaluates to `false`:

`throw_unless` 함수는 주어진 결과가 `false` 인 경우에 주어진 exception-예외를 던집니다.

    throw_unless(Auth::user()->isAdmin(), AuthorizationException::class);

    throw_unless(
        Auth::user()->isAdmin(),
        AuthorizationException::class,
        'You are not allowed to access this page'
    );

<a name="method-today"></a>
#### `today()` {#collection-method}

The `today` function creates a new `Illuminate\Support\Carbon` instance for the current date:

`today` 함수는 현재 날짜를 기준으로한 `Illuminate\Support\Carbon` 인스턴스를 생성합니다.

    $today = today();

<a name="method-trait-uses-recursive"></a>
#### `trait_uses_recursive()` {#collection-method}

The `trait_uses_recursive` function returns all traits used by a trait:

`trait_uses_recursive` 함수는 해당 trait에서 사용된 모든 trait을 반환합니다.

    $traits = trait_uses_recursive(\Illuminate\Notifications\Notifiable::class);

<a name="method-transform"></a>
#### `transform()` {#collection-method}

The `transform` function executes a `Closure` on a given value if the value is not [blank](#method-blank) and returns the result of the `Closure`:

`transform` 함수는 주어진 값이 [빈값](#method-blank)이 아닌 경우에 `Closure`를 실행하고 `Closure`의 결과를 반환합니다.

    $callback = function ($value) {
        return $value * 2;
    };

    $result = transform(5, $callback);

    // 10

A default value or `Closure` may also be passed as the third parameter to the method. This value will be returned if the given value is blank:

세번째 인자로 기본값 또는 `Closure` 가 전달될 수 있습니다. 이 값은 첫 번째 인자가 빈값인 경우 반환됩니다.

    $result = transform(null, $callback, 'The value is blank');

    // The value is blank

<a name="method-validator"></a>
#### `validator()` {#collection-method}

The `validator` function creates a new [validator](/docs/{{version}}/validation) instance with the given arguments. You may use it instead of the `Validator` facade for convenience:

`validator` 함수는 주어진 인자를 통해서 새로운 [validator](/docs/{{version}}/validation) 인스턴스를 생성합니다. 보다 편리하게 `Validator` 파사드를 사용할 수도 있습니다.

    $validator = validator($data, $rules, $messages);

<a name="method-value"></a>
#### `value()` {#collection-method}

The `value` function returns the value it is given. However, if you pass a `Closure` to the function, the `Closure` will be executed then its result will be returned:

`value` 함수는 자신에게 주어진 값을 그대로 반환합니다. 그렇지만 함수에 `Closure`를 전달하면 `Closure`가 실행되고 그 결과물이 반환됩니다.

    $result = value(true);

    // true

    $result = value(function () {
        return false;
    });

    // false

<a name="method-view"></a>
#### `view()` {#collection-method}

The `view` function retrieves a [view](/docs/{{version}}/views) instance:

`view` 함수는 [view](/docs/{{version}}/views) 인스턴스를 조회합니다.

    return view('auth.login');

<a name="method-with"></a>
#### `with()` {#collection-method}

The `with` function returns the value it is given. If a `Closure` is passed as the second argument to the function, the `Closure` will be executed and its result will be returned:

`with` 함수는 자신에게 주어진 값을 그대로 반환합니다. 만약 함수에 두 번째 인자로 `Closure` 가 전달되면, `Closure` 가 실행되어 그 결과를 반환합니다.

    $callback = function ($value) {
        return (is_numeric($value)) ? $value * 2 : 0;
    };

    $result = with(5, $callback);

    // 10

    $result = with(null, $callback);

    // 0

    $result = with(5, null);

    // 5
