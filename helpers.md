# 헬퍼 함수들

- [시작하기](#introduction)
- [사용 가능한 메소드들](#available-methods)

<a name="introduction"></a>
## 시작하기

라라벨은 다양한 글로벌 "헬퍼" PHP 함수들을 포함하고 있습니다. 그중 많은 기능은 프레임워크가 스스로 사용하지만, 여러분의 어플리케이션에도 자유롭게 사용하실 수 있습니다.

<a name="available-methods"></a>
## 사용 가능한 메소드

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
- [Arr::where](#method-array-where)
- [Arr::wrap](#method-array-wrap)
- [data_fill](#method-data-fill)
- [data_get](#method-data-get)
- [data_set](#method-data-set)
- [head](#method-head)
- [last](#method-last)


### 경로

- [app_path](#method-app-path)
- [base_path](#method-base-path)
- [config_path](#method-config-path)
- [database_path](#method-database-path)
- [mix](#method-mix)
- [public_path](#method-public-path)
- [resource_path](#method-resource-path)
- [storage_path](#method-storage-path)

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
- [Str::is](#method-str-is)
- [Str::isAscii](#method-str-is-ascii)
- [Str::isUuid](#method-str-is-uuid)
- [Str::kebab](#method-kebab-case)
- [Str::length](#method-str-length)
- [Str::limit](#method-str-limit)
- [Str::lower](#method-str-lower)
- [Str::orderedUuid](#method-str-ordered-uuid)
- [Str::padBoth](#method-str-padboth)
- [Str::padLeft](#method-str-padleft)
- [Str::padRight](#method-str-padright)
- [Str::plural](#method-str-plural)
- [Str::random](#method-str-random)
- [Str::replaceArray](#method-str-replace-array)
- [Str::replaceFirst](#method-str-replace-first)
- [Str::replaceLast](#method-str-replace-last)
- [Str::singular](#method-str-singular)
- [Str::slug](#method-str-slug)
- [Str::snake](#method-snake-case)
- [Str::start](#method-str-start)
- [Str::startsWith](#method-starts-with)
- [Str::studly](#method-studly-case)
- [Str::substr](#method-str-substr)
- [Str::title](#method-title-case)
- [Str::ucfirst](#method-str-ucfirst)
- [Str::upper](#method-str-upper)
- [Str::uuid](#method-str-uuid)
- [Str::words](#method-str-words)
- [trans](#method-trans)
- [trans_choice](#method-trans-choice)

<a name="fluent-strings"></a>
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

- [action](#method-action)
- [asset](#method-asset)
- [route](#method-route)
- [secure_asset](#method-secure-asset)
- [secure_url](#method-secure-url)
- [url](#method-url)

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
## 메소드 목록

<a name="arrays"></a>
## 배열 & 객체

<a name="method-array-accessible"></a>
#### `Arr::accessible()` {#collection-method .first-collection-method}

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

`Arr::add` 메소드는 배열 내에 키가 존재하지 않거나 `null`이면 주어진 key / value 쌍을 배열에 추가합니다.

    use Illuminate\Support\Arr;

    $array = Arr::add(['name' => 'Desk'], 'price', 100);

    // ['name' => 'Desk', 'price' => 100]

    $array = Arr::add(['name' => 'Desk', 'price' => null], 'price', 100);

    // ['name' => 'Desk', 'price' => 100]


<a name="method-array-collapse"></a>
#### `Arr::collapse()` {#collection-method}

`Arr::collapse` 메소드는 배열들의 배열(여러 개의 배열)을 하나의 배열로 통합합니다.

    use Illuminate\Support\Arr;

    $array = Arr::collapse([[1, 2, 3], [4, 5, 6], [7, 8, 9]]);

    // [1, 2, 3, 4, 5, 6, 7, 8, 9]

<a name="method-array-crossjoin"></a>
#### `Arr::crossJoin()` {#collection-method}

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

`Arr::divide` 메소드는 주어진 배열에서 키들을 담고 있는 배열과 값들을 담고 있는 배열, 총 2개의 배열을 반환합니다.

    use Illuminate\Support\Arr;

    [$keys, $values] = Arr::divide(['name' => 'Desk']);

    // $keys: ['name']

    // $values: ['Desk']

<a name="method-array-dot"></a>
#### `Arr::dot()` {#collection-method}

`Arr::dot` 메소드는 다차원 배열을 "점(.)"으로 배열 깊이를 표기하면서 단일 레벨의 배열로 만듭니다.

    use Illuminate\Support\Arr;

    $array = ['products' => ['desk' => ['price' => 100]]];

    $flattened = Arr::dot($array);

    // ['products.desk.price' => 100]

<a name="method-array-except"></a>
#### `Arr::except()` {#collection-method}

`Arr::except` 메소드는 주어진 키 / 값 쌍을 배열에서 제거합니다.

    use Illuminate\Support\Arr;

    $array = ['name' => 'Desk', 'price' => 100];

    $filtered = Arr::except($array, ['price']);

    // ['name' => 'Desk']

<a name="method-array-exists"></a>
#### `Arr::exists()` {#collection-method}

`Arr::exists` 메소드는 주어진 키가 제공된 배열에 있는지 확인합니다.

    use Illuminate\Support\Arr;

    $array = ['name' => 'John Doe', 'age' => 17];

    $exists = Arr::exists($array, 'name');

    // true

    $exists = Arr::exists($array, 'salary');

    // false

<a name="method-array-first"></a>
#### `Arr::first()` {#collection-method}

`Arr::first` 메소드는 전달된 배열 중 주어진 조건을 만족하는 첫 번째 요소를 반환합니다.

    use Illuminate\Support\Arr;

    $array = [100, 200, 300];

    $first = Arr::first($array, function ($value, $key) {
        return $value >= 150;
    });

    // 200

메소드에 세번째 파라미터로 기본값을 지정할 수 있습니다. 배열의 어떠한 값도 조건을 통과하지 못했을 때 이 값이 반환됩니다.

    use Illuminate\Support\Arr;

    $first = Arr::first($array, $callback, $default);

<a name="method-array-flatten"></a>
#### `Arr::flatten()` {#collection-method}

`Arr::flatten` 메소드는 다차원 배열을 단일 레벨의 1차원 배열로 만듭니다.

    use Illuminate\Support\Arr;

    $array = ['name' => 'Joe', 'languages' => ['PHP', 'Ruby']];

    $flattened = Arr::flatten($array);

    // ['Joe', 'PHP', 'Ruby']

<a name="method-array-forget"></a>
#### `Arr::forget()` {#collection-method}

`Arr::forget` 메소드는 "점(.)" 표기법을 사용하여 중첩 배열로부터 주어진 키 / 값 쌍을 제거합니다.

    use Illuminate\Support\Arr;

    $array = ['products' => ['desk' => ['price' => 100]]];

    Arr::forget($array, 'products.desk');

    // ['products' => []]

<a name="method-array-get"></a>
#### `Arr::get()` {#collection-method}

`Arr::get` 메소드는 "점(.)" 표기법으로 중첩 배열로부터 주어진 값을 찾습니다.

    use Illuminate\Support\Arr;

    $array = ['products' => ['desk' => ['price' => 100]]];

    $price = Arr::get($array, 'products.desk.price');

    // 100

`Arr::get` 메소드는 특정 키를 찾지 못한 경우 반환되는 기본값을 지정할 수도 있습니다.

    use Illuminate\Support\Arr;

    $discount = Arr::get($array, 'products.desk.discount', 0);

    // 0

<a name="method-array-has"></a>
#### `Arr::has()` {#collection-method}

`Arr::has` 메소드는 "점(.)" 표기를 이용하여 배열에 주어진 아이템 또는 아이템들이 존재하는지 확인합니다.

    use Illuminate\Support\Arr;

    $array = ['product' => ['name' => 'Desk', 'price' => 100]];

    $contains = Arr::has($array, 'product.name');

    // true

    $contains = Arr::has($array, ['product.price', 'product.discount']);

    // false

<a name="method-array-hasany"></a>
#### `Arr::hasAny()` {#collection-method}

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

주어진 배열이 연관 배열이면 `Arr::isAssoc`은 `true`를 반환합니다. 배열은 0으로 시작하는 순차적 숫자 키가 없는 경우 "연관-associative"로 간주됩니다.

    use Illuminate\Support\Arr;

    $isAssoc = Arr::isAssoc(['product' => ['name' => 'Desk', 'price' => 100]]);

    // true

    $isAssoc = Arr::isAssoc([1, 2, 3]);

    // false

<a name="method-array-last"></a>
#### `Arr::last()` {#collection-method}

`Arr::last` 메소드는 전달된 조건을 통과하는 아이템의 가장 마지막 요소를 반환합니다.

    use Illuminate\Support\Arr;

    $array = [100, 200, 300, 110];

    $last = Arr::last($array, function ($value, $key) {
        return $value >= 150;
    });

    // 300

메소드의 세 번째 인자로 기본값을 전달할 수 있습니다. 이 값은 조건을 통과하는 값이 없을 때 반환됩니다.

    use Illuminate\Support\Arr;

    $last = Arr::last($array, $callback, $default);

<a name="method-array-only"></a>
#### `Arr::only()` {#collection-method}

`Arr::only` 메소드는 특정한 키 / 값 쌍만을 배열로부터 반환합니다.

    use Illuminate\Support\Arr;

    $array = ['name' => 'Desk', 'price' => 100, 'orders' => 10];

    $slice = Arr::only($array, ['name', 'price']);

    // ['name' => 'Desk', 'price' => 100]

<a name="method-array-pluck"></a>
#### `Arr::pluck()` {#collection-method}

`Arr::pluck` 메소드는 배열로부터 주어진 키 / 값 쌍의 리스트를 조회합니다.

    use Illuminate\Support\Arr;

    $array = [
        ['developer' => ['id' => 1, 'name' => 'Taylor']],
        ['developer' => ['id' => 2, 'name' => 'Abigail']],
    ];

    $names = Arr::pluck($array, 'developer.name');

    // ['Taylor', 'Abigail']

리스트가 만들어 지는 결과가 어떻게 키로 변환될지 지정할 수도 있습니다.

    use Illuminate\Support\Arr;

    $names = Arr::pluck($array, 'developer.name', 'developer.id');

    // [1 => 'Taylor', 2 => 'Abigail']

<a name="method-array-prepend"></a>
#### `Arr::prepend()` {#collection-method}

`Arr::prepend` 메소드는 배열의 시작 부분에 아이템을 추가할 것입니다.

    use Illuminate\Support\Arr;

    $array = ['one', 'two', 'three', 'four'];

    $array = Arr::prepend($array, 'zero');

    // ['zero', 'one', 'two', 'three', 'four']

필요한 경우, 아이템의 키를 지정할 수도 있습니다.

    use Illuminate\Support\Arr;

    $array = ['price' => 100];

    $array = Arr::prepend($array, 'Desk', 'name');

    // ['name' => 'Desk', 'price' => 100]

<a name="method-array-pull"></a>
#### `Arr::pull()` {#collection-method}

`Arr::pull` 메소드는 배열에서 주어진 키 / 값 쌍을 반환함과 동시에 제거합니다.

    use Illuminate\Support\Arr;

    $array = ['name' => 'Desk', 'price' => 100];

    $name = Arr::pull($array, 'name');

    // $name: Desk

    // $array: ['price' => 100]

메소드의 세 번째 인자로 기본값을 전달할 수 있습니다. 키가 존재하지 않는 경우 이 값이 반환됩니다.

    use Illuminate\Support\Arr;

    $value = Arr::pull($array, $key, $default);

<a name="method-array-query"></a>
#### `Arr::query()` {#collection-method}

`Arr::query` 메소드는 배열을 쿼리 스트링으로 변환합니다.

    use Illuminate\Support\Arr;

    $array = ['name' => 'Taylor', 'order' => ['column' => 'created_at', 'direction' => 'desc']];

    Arr::query($array);

    // name=Taylor&order[column]=created_at&order[direction]=desc

<a name="method-array-random"></a>
#### `Arr::random()` {#collection-method}

`Arr::random` 메소드는 배열에서 임의의 값을 반환합니다.

    use Illuminate\Support\Arr;

    $array = [1, 2, 3, 4, 5];

    $random = Arr::random($array);

    // 4 - (retrieved randomly)

두 번째 인자로 몇 개의 아이템을 반환할지 값을 지정할 수 있습니다. 이 인자를 지정하면, 하나의 아이템이 포함되더라도 배열이 반환됩니다.

    use Illuminate\Support\Arr;

    $items = Arr::random($array, 2);

    // [2, 5] - (retrieved randomly)

<a name="method-array-set"></a>
#### `Arr::set()` {#collection-method}

`Arr::set` 메소드는 "점(.)" 표기법을 이용하여 중첩된 배열 내에 값을 설정합니다.

    use Illuminate\Support\Arr;

    $array = ['products' => ['desk' => ['price' => 100]]];

    Arr::set($array, 'products.desk.price', 200);

    // ['products' => ['desk' => ['price' => 200]]]

<a name="method-array-shuffle"></a>
#### `Arr::shuffle()` {#collection-method}

`Arr::shuffle` 메소드는 배열의 항목을 무작위로 섞습니다.

    use Illuminate\Support\Arr;

    $array = Arr::shuffle([1, 2, 3, 4, 5]);

    // [3, 2, 5, 1, 4] - (generated randomly)

<a name="method-array-sort"></a>
#### `Arr::sort()` {#collection-method}

`Arr::sort` 메소드는 값을 기반으로 정렬을 수행합니다.

    use Illuminate\Support\Arr;

    $array = ['Desk', 'Table', 'Chair'];

    $sorted = Arr::sort($array);

    // ['Chair', 'Desk', 'Table']

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

`Arr::where` 메소드는 주어진 클로져를 사용하여 배열을 필터링합니다.

    use Illuminate\Support\Arr;

    $array = [100, '200', 300, '400', 500];

    $filtered = Arr::where($array, function ($value, $key) {
        return is_string($value);
    });

    // [1 => '200', 3 => '400']

<a name="method-array-wrap"></a>
#### `Arr::wrap()` {#collection-method}

`Arr::wrap` 메소드는 주어진 값을 배열로 만듭니다. 만약 함수에 전달된 값이 배열이라면 결과에는 변경사항이 없습니다.

    use Illuminate\Support\Arr;

    $string = 'Laravel';

    $array = Arr::wrap($string);

    // ['Laravel']

주어진 값이 null인 경우, 빈 배열이 반환됩니다.

    use Illuminate\Support\Arr;

    $nothing = null;

    $array = Arr::wrap($nothing);

    // []

<a name="method-data-fill"></a>
#### `data_fill()` {#collection-method}

`data_fill` 함수는 "점(.)" 표기법을 사용하여 중첩된 배열 또는 객체 내부에 빠진 값을 채워 넣습니다.

    $data = ['products' => ['desk' => ['price' => 100]]];

    data_fill($data, 'products.desk.price', 200);

    // ['products' => ['desk' => ['price' => 100]]]

    data_fill($data, 'products.desk.discount', 10);

    // ['products' => ['desk' => ['price' => 100, 'discount' => 10]]]

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

`data_get` 함수는 "점(.)" 표기법을 사용하여 중첩된 배열 또는 객체 안에서 값을 조회합니다.

    $data = ['products' => ['desk' => ['price' => 100]]];

    $price = data_get($data, 'products.desk.price');

    // 100

`data_get` 함수는 또한 지정된 키를 찾을 수 없는 경우 반환될 기본값을 인자로 받습니다.

    $discount = data_get($data, 'products.desk.discount', 0);

    // 0

이 함수는 배열이나 객체의 키를 대상으로 별표를 이용한 와일드 카드도 허용합니다.

    $data = [
        'product-one' => ['name' => 'Desk 1', 'price' => 100],
        'product-two' => ['name' => 'Desk 2', 'price' => 150],
    ];

    data_get($data, '*.name');

    // ['Desk 1', 'Desk 2'];

<a name="method-data-set"></a>
#### `data_set()` {#collection-method}

`data_set` 함수는 "점(.)" 표기법을 사용하여 중첩된 배열 또는 객체 안의 값을 설정합니다.

    $data = ['products' => ['desk' => ['price' => 100]]];

    data_set($data, 'products.desk.price', 200);

    // ['products' => ['desk' => ['price' => 200]]]

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

기본적으로 기존 값은 덮어 씁니다. 새로운 값을 설정하고자 한다면, 값이 존재하지 않을 때만 네 번째 인자로 `false`를 전달하면 됩니다.

    $data = ['products' => ['desk' => ['price' => 100]]];

    data_set($data, 'products.desk.price', 200, false);

    // ['products' => ['desk' => ['price' => 100]]]

<a name="method-head"></a>
#### `head()` {#collection-method}

`head` 함수는 배열의 첫 번째 요소를 반환합니다.

    $array = [100, 200, 300];

    $first = head($array);

    // 100

<a name="method-last"></a>
#### `last()` {#collection-method}

`last` 함수는 배열의 마지막 요소를 반환합니다.

    $array = [100, 200, 300];

    $last = last($array);

    // 300

<a name="paths"></a>
## 경로

<a name="method-app-path"></a>
#### `app_path()` {#collection-method}

`app_path` 함수는 `app` 디렉토리에 대한 절대 경로를 반환합니다. `app_path` 함수를 사용하면 어플리케이션 디렉토리에서 특정 파일의 절대 경로를 생성할 수도 있습니다.

    $path = app_path();

    $path = app_path('Http/Controllers/Controller.php');

<a name="method-base-path"></a>
#### `base_path()` {#collection-method}

`base_path` 함수는 프로젝트의 루트 디렉토리에 대한 절대 경로를 반환합니다. `base_path` 함수를 사용하여 프로젝트 루트 디렉토리에 대한 특정 파일의 절대 경로를 생성할 수도 있습니다.

    $path = base_path();

    $path = base_path('vendor/bin');

<a name="method-config-path"></a>
#### `config_path()` {#collection-method}

`config_path` 함수는 애플리케이션의 `config` 디렉토리에 대한 절대 경로를 반환합니다. `config_path` 함수를 사용하여, 어플리케이션의 설정 디렉토리 안에 있는 파일에 대한 절대 경로를 생성할 수도 있습니다.

    $path = config_path();

    $path = config_path('app.php');

<a name="method-database-path"></a>
#### `database_path()` {#collection-method}

`database_path` 함수는 애플리케이션의 `database` 디렉토리에 대한 절대 경로를 반환합니다. `database_path` 함수를 사용하여, 애플리케이션의 데이터베이스 디렉토리 안에 있는 파일에 대한 절대 경로를 생성할 수도 있습니다.

    $path = database_path();

    $path = database_path('factories/UserFactory.php');

<a name="method-mix"></a>
#### `mix()` {#collection-method}

`mix` 함수는 [버전이 지정된 Mix](/docs/{{version}}/mix) 파일에 대한 경로를 반환합니다.

    $path = mix('css/app.css');

<a name="method-public-path"></a>
#### `public_path()` {#collection-method}

`public_path` 함수는 `public` 디렉토리에 대한 절대 경로를 반환합니다. `public_path` 함수를 사용하여, 애플리케이션의 public 디렉토리 안에 있는 파일에 대한 절대 경로를 생성할 수도 있습니다.

    $path = public_path();

    $path = public_path('css/app.css');

<a name="method-resource-path"></a>
#### `resource_path()` {#collection-method}

`resource_path` 함수는 `resources` 디렉토리에 대한 절대 경로를 반환합니다. `resource_path` 함수를 사용하여 리소스 디렉토리 안에 있는 파일에 대한 절대 경로를 생성할 수도 있습니다.

    $path = resource_path();

    $path = resource_path('sass/app.scss');

<a name="method-storage-path"></a>
#### `storage_path()` {#collection-method}

`storage_path` 함수는 `storage` 디렉토리에 대한 절대 경로를 반환합니다. `storage_path` 함수를 사용하여 스토리지 디렉토리 안에 있는 파일에 대한 절대 경로를 생성할 수도 있습니다.

    $path = storage_path();

    $path = storage_path('app/file.txt');

<a name="strings"></a>
## 문자열

<a name="method-__"></a>
#### `__()` {#collection-method}

`__` 함수는 주어진 다국어 문자열 또는 다국어 키를 [다국어 파일](/docs/{{version}}/localization)을 사용하여 변환합니다.

    echo __('Welcome to our application');

    echo __('messages.welcome');

지정된 다국어 문자열이나 키가 존재하지 않는 경우, `__` 함수는 주어진 값을 그대로 반환합니다. 따라서 예제와 같이 `__` 함수는 다국어 키가 존재하지 않는다면 `messages.welcome`을 그대로 반환합니다.

<a name="method-class-basename"></a>
#### `class_basename()` {#collection-method}

`class_basename` 함수는 클래스의 네임스페이스를 제거한 클래스의 클래스 명을 반환합니다.

    $class = class_basename('Foo\Bar\Baz');

    // Baz

<a name="method-e"></a>
#### `e()` {#collection-method}

`e` 함수는 주어진 문자열에 PHP의 `htmlspecialchars` 함수를 `double_encode` 옵션이 기본적으로 `true`인 형태로 실행한 결과를 반환합니다.

    echo e('<html>foo</html>');

    // &lt;html&gt;foo&lt;/html&gt;

<a name="method-preg-replace-array"></a>
#### `preg_replace_array()` {#collection-method}

`preg_replace_array` 함수는 주어진 패턴에 맞는 문자열을 차례대로 배열로 대체합니다.

    $string = 'The event will take place between :start and :end';

    $replaced = preg_replace_array('/:[a-z_]+/', ['8:30', '9:00'], $string);

    // The event will take place between 8:30 and 9:00

<a name="method-str-after"></a>
#### `Str::after()` {#collection-method}

`Str::after` 메소드는 주어진 값 이후의 모든 것을 반환합니다. 문자열 내에 값이 없으면 전체 문자열이 반환됩니다.

    use Illuminate\Support\Str;

    $slice = Str::after('This is my name', 'This is');

    // ' my name'

<a name="method-str-after-last"></a>
#### `Str::afterLast()` {#collection-method}

`Str::afterLast` 메소드는 문자열에서 주어진 값이 마지막으로 나타난 후 모든 것을 반환합니다. 문자열 내에 값이 없으면 전체 문자열이 반환됩니다.

    use Illuminate\Support\Str;

    $slice = Str::afterLast('App\Http\Controllers\Controller', '\\');

    // 'Controller'

<a name="method-str-ascii"></a>
#### `Str::ascii()` {#collection-method}

`Str::ascii` 메소드는 문자열을 ASCII 값으로 변환하려고 시도합니다.

    use Illuminate\Support\Str;

    $slice = Str::ascii('û');

    // 'u'

<a name="method-str-before"></a>
#### `Str::before()` {#collection-method}

`Str::before` 메소드는 문자열에서 주어진 값 이전의 모든 것을 반환합니다.

    use Illuminate\Support\Str;

    $slice = Str::before('This is my name', 'my name');

    // 'This is '

<a name="method-str-before-last"></a>
#### `Str::beforeLast()` {#collection-method}

`Str::beforeLast` 메소드는 문자열에서 주어진 값 이전의 모든 것을 반환합니다.

    use Illuminate\Support\Str;

    $slice = Str::beforeLast('This is my name', 'is');

    // 'This '

<a name="method-str-between"></a>
#### `Str::between()` {#collection-method}

`Str::between` 메소드는 두 값 사이의 문자열 부분을 반환합니다.


    use Illuminate\Support\Str;

    $slice = Str::between('This is my name', 'This', 'name');

    // ' is my '

<a name="method-camel-case"></a>
#### `Str::camel()` {#collection-method}

`Str::camel` 메소드는 주어진 문자열을 `camelCase`로 변환합니다.

    use Illuminate\Support\Str;

    $converted = Str::camel('foo_bar');

    // fooBar

<a name="method-str-contains"></a>
#### `Str::contains()` {#collection-method}

`Str::contains` 메소드는 주어진 문자열이 주어진 값을 포함하는지 확인합니다. (대소문자를 구분합니다.)

    use Illuminate\Support\Str;

    $contains = Str::contains('This is my name', 'my');

    // true

또한, 주어진 문자열에 값이 포함되어 있는지 확인하기 위해 배열을 전달할 수도 있습니다.

    use Illuminate\Support\Str;

    $contains = Str::contains('This is my name', ['my', 'foo']);

    // true

<a name="method-str-contains-all"></a>
#### `Str::containsAll()` {#collection-method}

`Str::containsAll` 메소드는 주어진 문자열에 모든 배열 값이 포함되어 있는지 확인합니다.

    use Illuminate\Support\Str;

    $containsAll = Str::containsAll('This is my name', ['my', 'name']);

    // true

<a name="method-ends-with"></a>
#### `Str::endsWith()` {#collection-method}

`Str::endsWith` 메소드는 주어진 문자열이 주어진 값으로 끝나는지 확인합니다.

    use Illuminate\Support\Str;

    $result = Str::endsWith('This is my name', 'name');

    // true

주어진 문자열이 주어진 값으로 끝나는지 확인하기 위해 배열을 전달할 수도 있습니다.

    use Illuminate\Support\Str;

    $result = Str::endsWith('This is my name', ['name', 'foo']);

    // true

    $result = Str::endsWith('This is my name', ['this', 'foo']);

    // false

<a name="method-str-finish"></a>
#### `Str::finish()` {#collection-method}

`Str::finish` 메소드는 문자열이 주어진 값으로 끝나지 않으면 해당 값을 추가합니다.

    use Illuminate\Support\Str;

    $adjusted = Str::finish('this/string', '/');

    // this/string/

    $adjusted = Str::finish('this/string/', '/');

    // this/string/

<a name="method-str-is"></a>
#### `Str::is()` {#collection-method}

`Str::is` 메소드는 주어진 문자열이 주어진 패턴과 일치하는지를 확인합니다. 와일드카드를 나타내는 데 별표를 사용할 수 있습니다.

    use Illuminate\Support\Str;

    $matches = Str::is('foo*', 'foobar');

    // true

    $matches = Str::is('baz*', 'foobar');

    // false

<a name="method-str-is-ascii"></a>
#### `Str::isAscii()` {#collection-method}

`Str::isAscii` 메소드는 주어진 문자열이 7 비트 ASCII인지 확인합니다.

    use Illuminate\Support\Str;

    $isAscii = Str::isAscii('Taylor');

    // true

    $isAscii = Str::isAscii('ü');

    // false

<a name="method-str-is-uuid"></a>
#### `Str::isUuid()` {#collection-method}

`Str::isUuid` 메소드는 주어진 문자열이 유효한 UUID인지 확인합니다.

    use Illuminate\Support\Str;

    $isUuid = Str::isUuid('a0a2a2d2-0b87-4a18-83f2-2529882be2de');

    // true

    $isUuid = Str::isUuid('laravel');

    // false

<a name="method-kebab-case"></a>
#### `Str::kebab()` {#collection-method}

`Str::kebab` 메소드는 주어진 문자열을 `kebab-case`로 변환합니다.
(역자주 : 단어와 단어를 '-'로 연결한 형태)

    use Illuminate\Support\Str;

    $converted = Str::kebab('fooBar');

    // foo-bar

<a name="method-str-length"></a>
#### `Str::length()` {#collection-method}

 `Str::length` 메소드는 주어진 문자열을 지정된 길이를 반환합니다.

    use Illuminate\Support\Str;

    $length = Str::length('Laravel');

    // 7

<a name="method-str-limit"></a>
#### `Str::limit()` {#collection-method}

`Str::limit` 메소드는 주어진 문자열을 지정된 길이로 제한합니다.

    use Illuminate\Support\Str;

    $truncated = Str::limit('The quick brown fox jumps over the lazy dog', 20);

    // The quick brown fox...

끝에 추가 될 문자열을 변경하기 위해 세 번째 인수를 전달할 수도 있습니다.

    use Illuminate\Support\Str;

    $truncated = Str::limit('The quick brown fox jumps over the lazy dog', 20, ' (...)');

    // The quick brown fox (...)

<a name="method-str-lower"></a>
#### `Str::lower()` {#collection-method}

`Str::lower` 메소드는 주어진 문자열을 소문자로 변환합니다.

    use Illuminate\Support\Str;

    $converted = Str::lower('LARAVEL');

    // laravel

<a name="method-str-ordered-uuid"></a>
#### `Str::orderedUuid()` {#collection-method}

`Str::orderedUuid` 메소드는 인덱싱된 데이터베이스 컬럼에 효과적으로 저장될 수 있도록 "타임스탬프와 같은 정렬이 가능한" UUID를 생성합니다.

(역자주 : 이 헬퍼 메소드의 결과로 생성되는 UUID의 첫 번째 부분은 시간에 따라서 증감하는 형태를 보입니다.)

    use Illuminate\Support\Str;

    return (string) Str::orderedUuid();

<a name="method-str-padboth"></a>
#### `Str::padBoth()` {#collection-method}

`Str::padBoth` 메소드는 PHP의 `str_pad` 함수를 래핑하여 문자열의 양쪽을 다른 문자로 채웁니다.

    use Illuminate\Support\Str;

    $padded = Str::padBoth('James', 10, '_');

    // '__James___'

    $padded = Str::padBoth('James', 10);

    // '  James   '

<a name="method-str-padleft"></a>
#### `Str::padLeft()` {#collection-method}

`String::padLeft` 메서드는 PHP `str_pad` 함수를 래핑하여 문자열의 왼쪽을 다른 문자로 채웁니다.

    use Illuminate\Support\Str;

    $padded = Str::padLeft('James', 10, '-=');

    // '-=-=-James'

    $padded = Str::padLeft('James', 10);

    // '     James'

<a name="method-str-padright"></a>
#### `Str::padRight()` {#collection-method}

`String::padRight` 메소드는 PHP `str_pad` 함수를 래핑하여 문자열의 오른쪽을 다른 문자로 채웁니다.

    use Illuminate\Support\Str;

    $padded = Str::padRight('James', 10, '-');

    // 'James-----'

    $padded = Str::padRight('James', 10);

    // 'James     '

<a name="method-str-plural"></a>
#### `Str::plural()` {#collection-method}

`Str::plural` 메소드는 단수형을 복수형으로 변환합니다. 이 기능은 현재 영어만 지원합니다.

    use Illuminate\Support\Str;

    $plural = Str::plural('car');

    // cars

    $plural = Str::plural('child');

    // children

문자열의 단수 또는 복수 형식을 검색하기 위해 함수에 두 번째 인수로 정수를 제공할 수 있습니다.

    use Illuminate\Support\Str;

    $plural = Str::plural('child', 2);

    // children

    $plural = Str::plural('child', 1);

    // child

<a name="method-str-random"></a>
#### `Str::random()` {#collection-method}

`Str::random` 메소드는 지정된 길이의 문자열을 무작위로 생성합니다. 이 함수는 PHP의 `random_bytes` 함수를 사용합니다.

    use Illuminate\Support\Str;

    $random = Str::random(40);

<a name="method-str-replace-array"></a>
#### `Str::replaceArray()` {#collection-method}

`Str::replaceArray` 메소드는 배열을 사용하여 문자열에서 주어진 값을 차례대로 바꿉니다.

    use Illuminate\Support\Str;

    $string = 'The event will take place between ? and ?';

    $replaced = Str::replaceArray('?', ['8:30', '9:00'], $string);

    // The event will take place between 8:30 and 9:00

<a name="method-str-replace-first"></a>
#### `Str::replaceFirst()` {#collection-method}

`Str::replaceFirst` 메소드는 문자열에서 주어진 값이 발견된 첫 번째 부분을 대체합니다.

    use Illuminate\Support\Str;

    $replaced = Str::replaceFirst('the', 'a', 'the quick brown fox jumps over the lazy dog');

    // a quick brown fox jumps over the lazy dog

<a name="method-str-replace-last"></a>
#### `Str::replaceLast()` {#collection-method}

`Str::replaceLast` 메소드는 문자열에서 주어진 값이 발견된 마지막 부분을 대체합니다.

    use Illuminate\Support\Str;

    $replaced = Str::replaceLast('the', 'a', 'the quick brown fox jumps over the lazy dog');

    // the quick brown fox jumps over a lazy dog

<a name="method-str-singular"></a>
#### `Str::singular()` {#collection-method}

`Str::singular` 메소드는 문자열을 단수 형식으로 변환합니다. 이 기능은 현재 영어만 지원합니다.

    use Illuminate\Support\Str;

    $singular = Str::singular('cars');

    // car

    $singular = Str::singular('children');

    // child

<a name="method-str-slug"></a>
#### `Str::slug()` {#collection-method}

`Str::slug` 메소드는 주어진 문자열로부터 URL에 알맞은 "slug"를 생성합니다.

    use Illuminate\Support\Str;

    $slug = Str::slug('Laravel 5 Framework', '-');

    // laravel-5-framework

<a name="method-snake-case"></a>
#### `Str::snake()` {#collection-method}

`Str::snake` 메소드는 주어진 문자열을 `snake_case` 형태로 변환합니다.

    use Illuminate\Support\Str;

    $converted = Str::snake('fooBar');

    // foo_bar

<a name="method-str-start"></a>
#### `Str::start()` {#collection-method}

`Str::start` 메소드는 문자열이 주어진 값으로 시작하지 않으면 이를 문자열에 추가합니다.

    use Illuminate\Support\Str;

    $adjusted = Str::start('this/string', '/');

    // /this/string

    $adjusted = Str::start('/this/string', '/');

    // /this/string

<a name="method-starts-with"></a>
#### `Str::startsWith()` {#collection-method}

`Str::startsWith` 메소드는 문자열이 주어진 값으로 시작하는지를 확인합니다.

    use Illuminate\Support\Str;

    $result = Str::startsWith('This is my name', 'This');

    // true

<a name="method-studly-case"></a>
#### `Str::studly()` {#collection-method}

`Str::studly` 메소드는 주어진 문자열을 `StudlyCase`로 변환합니다.

    use Illuminate\Support\Str;

    $converted = Str::studly('foo_bar');

    // FooBar

<a name="method-str-substr"></a>
#### `Str::substr()` {#collection-method}

`Str::substr` 메소드는 start 및 length 매개 변수로 지정된 문자열 부분을 리턴합니다.

    use Illuminate\Support\Str;

    $converted = Str::substr('The Laravel Framework', 4, 7);

    // Laravel

<a name="method-title-case"></a>
#### `Str::title()` {#collection-method}

`Str::title` 메소드는 주어진 문자열을 `Title Case`로 변환합니다.

    use Illuminate\Support\Str;

    $converted = Str::title('a nice title uses the correct case');

    // A Nice Title Uses The Correct Case

<a name="method-str-ucfirst"></a>
#### `Str::ucfirst()` {#collection-method}

`Str::ucfirst` 메소드는 첫 문자를 대문자로하여 주어진 문자열을 반환합니다.

    use Illuminate\Support\Str;

    $string = Str::ucfirst('foo bar');

    // Foo bar

<a name="method-str-upper"></a>
#### `Str::upper()` {#collection-method}

`Str::upper` 메소드는 주어진 문자열을 대문자로 변환합니다.

    use Illuminate\Support\Str;

    $string = Str::upper('laravel');

    // LARAVEL

<a name="method-str-uuid"></a>
#### `Str::uuid()` {#collection-method}

`Str::uuid` 메소드는 UUID (version 4)를 생성합니다.

    use Illuminate\Support\Str;

    return (string) Str::uuid();

<a name="method-str-words"></a>
#### `Str::words()` {#collection-method}

`Str::words` 메소드는 문자열의 단어 수를 제한합니다.

    use Illuminate\Support\Str;

    return Str::words('Perfectly balanced, as all things should be.', 3, ' >>>');

    // Perfectly balanced, as >>>

<a name="method-trans"></a>
#### `trans()` {#collection-method}

`trans` 함수는 [다국어 파일](/docs/{{version}}/localization)을 이용하여 주어진 다국어 키를 변환합니다.

    echo trans('messages.welcome');

지정된 다국어 키가 존재하지 않는다면, `trans` 함수는 주어진 키를 반환합니다. 따라서 예제에서 다국어 키가 존재하지 않는다면 `trans` 함수는 `messages.welcome`을 그대로 반환합니다.

<a name="method-trans-choice"></a>
#### `trans_choice()` {#collection-method}

`trans_choice` 함수는 주어진, 수량(단수, 복수 처리)을 이용하여 주어진 다국어 키를 번역합니다.

    echo trans_choice('messages.notifications', $unreadCount);

지정된 다국어 키가 존재하지 않는다면, `trans_choice` 함수는 주어진 키를 반환합니다. 따라서 예제에서 다국어 키가 존재하지 않는다면 `trans_choice` 함수는 `messages.notifications`를 그대로 반환합니다.

<a name="fluent-strings"></a>
## Fluent Strings

Fluent Strings는 문자열 값을 사용할 때보다 유창하고 객체 지향적인 인터페이스를 제공하므로, 기존 문자열의 작동보다 더 읽기 쉬운 구문을 사용하여 여러 문자열 연산을 함께 연결할 수 있습니다.

<a name="method-fluent-str-after"></a>
#### `after` {#collection-method}

`after` 메소드는 주어진 값 이후의 모든 것을 반환합니다. 문자열 내에 값이 없으면 전체 문자열이 반환됩니다.

    use Illuminate\Support\Str;

    $slice = Str::of('This is my name')->after('This is');

    // ' my name'

<a name="method-fluent-str-after-last"></a>
#### `afterLast` {#collection-method}

`afterLast` 메소드는 문자열에서 주어진 값이 마지막으로 나타난 후 모든 것을 반환합니다. 문자열 내에 값이 없으면 전체 문자열이 반환됩니다.

    use Illuminate\Support\Str;

    $slice = Str::of('App\Http\Controllers\Controller')->afterLast('\\');

    // 'Controller'

<a name="method-fluent-str-append"></a>
#### `append` {#collection-method}

`append` 메소드는 주어진 값을 문자열에 추가합니다.

    use Illuminate\Support\Str;

    $string = Str::of('Taylor')->append(' Otwell');

    // 'Taylor Otwell'

<a name="method-fluent-str-ascii"></a>
#### `ascii` {#collection-method}

`ascii` 메소드는 문자열을 ASCII 값으로 변환합니다.

    use Illuminate\Support\Str;

    $string = Str::of('ü')->ascii();

    // 'u'

<a name="method-fluent-str-basename"></a>
#### `basename` {#collection-method}

`basename` 메소드는 주어진 문자열의 마지막 구성 요소를 반환합니다.

    use Illuminate\Support\Str;

    $string = Str::of('/foo/bar/baz')->basename();

    // 'baz'

필요한 경우 마지막 구성 요소에서 제거할 "확장자"를 제공할 수 있습니다.

    use Illuminate\Support\Str;

    $string = Str::of('/foo/bar/baz.jpg')->basename('.jpg');

    // 'baz'

<a name="method-fluent-str-before"></a>
#### `before` {#collection-method}

`before` 메소드는 문자열에서 주어진 값 이전의 모든 것을 반환합니다.

    use Illuminate\Support\Str;

    $slice = Str::of('This is my name')->before('my name');

    // 'This is '

<a name="method-fluent-str-before-last"></a>
#### `beforeLast` {#collection-method}

`beforeLast` 메소드는 문자열에서 주어진 값 이전의 모든 것을 반환합니다.

    use Illuminate\Support\Str;

    $slice = Str::of('This is my name')->beforeLast('is');

    // 'This '

<a name="method-fluent-str-camel"></a>
#### `camel` {#collection-method}

`camel` 메소드는 주어진 문자열을 `camelCase`로 변환합니다.

    use Illuminate\Support\Str;

    $converted = Str::of('foo_bar')->camel();

    // fooBar

<a name="method-fluent-str-contains"></a>
#### `contains` {#collection-method}

`contains` 메소드는 주어진 문자열이 주어진 값을 포함하는지 확인합니다. (대소문자를 구분합니다.)

    use Illuminate\Support\Str;

    $contains = Str::of('This is my name')->contains('my');

    // true

또한, 주어진 문자열에 값이 포함되어 있는지 확인하기 위해 배열을 전달할 수도 있습니다.

    use Illuminate\Support\Str;

    $contains = Str::of('This is my name')->contains(['my', 'foo']);

    // true

<a name="method-fluent-str-contains-all"></a>
#### `containsAll` {#collection-method}

`containsAll` 메소드는 주어진 문자열에 모든 배열 값이 포함되어 있는지 확인합니다.

    use Illuminate\Support\Str;

    $containsAll = Str::of('This is my name')->containsAll(['my', 'name']);

    // true

<a name="method-fluent-str-dirname"></a>
#### `dirname` {#collection-method}

`dirname` 메소드는 주어진 문자열의 부모 디렉토리를 반환합니다.

    use Illuminate\Support\Str;

    $string = Str::of('/foo/bar/baz')->dirname();

    // '/foo/bar'

선택적으로 문자열에서 자르려는 디렉토리 레벨 수를 지정할 수 있습니다.

    use Illuminate\Support\Str;

    $string = Str::of('/foo/bar/baz')->dirname(2);

    // '/foo'

<a name="method-fluent-str-ends-with"></a>
#### `endsWith` {#collection-method}

`endsWith` 메소드는 주어진 문자열이 주어진 값으로 끝나는지 확인합니다.

    use Illuminate\Support\Str;

    $result = Str::of('This is my name')->endsWith('name');

    // true

주어진 문자열이 주어진 값으로 끝나는지 확인하기 위해 배열을 전달할 수도 있습니다.

    use Illuminate\Support\Str;

    $result = Str::of('This is my name')->endsWith(['name', 'foo']);

    // true

    $result = Str::of('This is my name')->endsWith(['this', 'foo']);

    // false

<a name="method-fluent-str-exactly"></a>
#### `exactly` {#collection-method}

`exactly` 메소드는 주어진 문자열이 다른 문자열과 정확히 일치하는지 확인합니다.

    use Illuminate\Support\Str;

    $result = Str::of('Laravel')->exactly('Laravel');

    // true

<a name="method-fluent-str-explode"></a>
#### `explode` {#collection-method}

`explode` 메소드는 주어진 구분 기호로 문자열을 분할하고 분할 문자열의 각 섹션을 포함하는 컬렉션을 반환합니다.

    use Illuminate\Support\Str;

    $collection = Str::of('foo bar baz')->explode(' ');

    // collect(['foo', 'bar', 'baz'])

<a name="method-fluent-str-finish"></a>
#### `finish` {#collection-method}

`finish` 메소드는 문자열이 주어진 값으로 끝나지 않으면 해당 값을 추가합니다.

    use Illuminate\Support\Str;

    $adjusted = Str::of('this/string')->finish('/');

    // this/string/

    $adjusted = Str::of('this/string/')->finish('/');

    // this/string/

<a name="method-fluent-str-is"></a>
#### `is` {#collection-method}

`is` 메소드는 주어진 문자열이 주어진 패턴과 일치하는지를 확인합니다. 와일드카드를 나타내는 데 별표를 사용할 수 있습니다.

    use Illuminate\Support\Str;

    $matches = Str::of('foobar')->is('foo*');

    // true

    $matches = Str::of('foobar')->is('baz*');

    // false

<a name="method-fluent-str-is-ascii"></a>
#### `isAscii` {#collection-method}

`isAscii` 메소드는 주어진 문자열이 ASCII 문자열인지 확인합니다.

    use Illuminate\Support\Str;

    $result = Str::of('Taylor')->isAscii();

    // true

    $result = Str::of('ü')->isAscii();

    // false

<a name="method-fluent-str-is-empty"></a>
#### `isEmpty` {#collection-method}

`isEmpty` 메소드는 주어진 문자열이 비어 있는지 확인합니다.

    use Illuminate\Support\Str;

    $result = Str::of('  ')->trim()->isEmpty();

    // true

    $result = Str::of('Laravel')->trim()->isEmpty();

    // false

<a name="method-fluent-str-is-not-empty"></a>
#### `isNotEmpty` {#collection-method}

`isNotEmpty` 메소드는 주어진 문자열이 비어 있지 않은지 확인합니다.


    use Illuminate\Support\Str;

    $result = Str::of('  ')->trim()->isNotEmpty();

    // false

    $result = Str::of('Laravel')->trim()->isNotEmpty();

    // true

<a name="method-fluent-str-kebab"></a>
#### `kebab` {#collection-method}

`kebab` 메소드는 주어진 문자열을 `kebab-case`로 변환합니다.

    use Illuminate\Support\Str;

    $converted = Str::of('fooBar')->kebab();

    // foo-bar

<a name="method-fluent-str-length"></a>
#### `length` {#collection-method}

`length` 메소드는 주어진 문자열의 길이를 반환합니다.

    use Illuminate\Support\Str;

    $length = Str::of('Laravel')->length();

    // 7

<a name="method-fluent-str-limit"></a>
#### `limit` {#collection-method}

`limit` 메소드는 주어진 문자열을 지정된 길이로 제한합니다.

    use Illuminate\Support\Str;

    $truncated = Str::of('The quick brown fox jumps over the lazy dog')->limit(20);

    // The quick brown fox...

끝에 추가 될 문자열을 변경하기 위해 두 번째 인수를 전달할 수도 있습니다.

    use Illuminate\Support\Str;

    $truncated = Str::of('The quick brown fox jumps over the lazy dog')->limit(20, ' (...)');

    // The quick brown fox (...)

<a name="method-fluent-str-lower"></a>
#### `lower` {#collection-method}

`lower` 메소드는 주어진 문자열을 소문자로 변환합니다.

    use Illuminate\Support\Str;

    $result = Str::of('LARAVEL')->lower();

    // 'laravel'

<a name="method-fluent-str-ltrim"></a>
#### `ltrim` {#collection-method}

`ltrim` 메소드는 주어진 문자열 왼쪽의 공백문자를 제거합니다.

(역자주: 또는 하단과 같이 주어진 문자를 제거 합니다)

    use Illuminate\Support\Str;

    $string = Str::of('  Laravel  ')->ltrim();

    // 'Laravel  '

    $string = Str::of('/Laravel/')->ltrim('/');

    // 'Laravel/'

<a name="method-fluent-str-match"></a>
#### `match` {#collection-method}

`match` 메소드는 주어진 정규식 패턴과 일치하는 문자열을 반환합니다.

    use Illuminate\Support\Str;

    $result = Str::of('foo bar')->match('/bar/');

    // 'bar'

    $result = Str::of('foo bar')->match('/foo (.*)/');

    // 'bar'

<a name="method-fluent-str-match-all"></a>
#### `matchAll` {#collection-method}

`matchAll` 메소드는 주어진 정규 표현식 패턴과 일치하는 문자열을 포함하는 컬렉션을 반환합니다.

    use Illuminate\Support\Str;

    $result = Str::of('bar fun bar fly')->matchAll('/f(\w*)/');

    // collect(['bar', 'bar'])

식 내에서 일치하는 그룹을 지정하면 Laravel은 해당 그룹의 일치하는 컬렉션을 반환합니다.

    use Illuminate\Support\Str;

    $result = Str::of('bar fun bar fly')->match('/f(\w*)/');

    // collect(['un', 'ly']);

일치하는 항목이 없으면 빈 컬렉션이 반환됩니다.

<a name="method-fluent-str-padboth"></a>
#### `padBoth` {#collection-method}

`padBoth` 메소드는 PHP의 `str_pad` 함수를 래핑하여 문자열의 양쪽을 다른 문자로 채웁니다

    use Illuminate\Support\Str;

    $padded = Str::of('James')->padBoth(10, '_');

    // '__James___'

    $padded = Str::of('James')->padBoth(10);

    // '  James   '

<a name="method-fluent-str-padleft"></a>
#### `padLeft` {#collection-method}

`padLeft` 메소드는 PHP의 `str_pad` 함수를 래핑하여 문자열의 왼쪽을 다른 문자로 채웁니다

    use Illuminate\Support\Str;

    $padded = Str::of('James')->padLeft(10, '-=');

    // '-=-=-James'

    $padded = Str::of('James')->padLeft(10);

    // '     James'

<a name="method-fluent-str-padright"></a>
#### `padRight` {#collection-method}

`padRight` 메소드는 PHP의 `str_pad` 함수를 래핑하여 문자열의 오른쪽을 다른 문자로 채웁니다

    use Illuminate\Support\Str;

    $padded = Str::of('James')->padRight(10, '-');

    // 'James-----'

    $padded = Str::of('James')->padRight(10);

    // 'James     '

<a name="method-fluent-str-plural"></a>
#### `plural` {#collection-method}

`plural` 메소드는 단수형을 복수형으로 변환합니다. 이 기능은 현재 영어만 지원합니다.

    use Illuminate\Support\Str;

    $plural = Str::of('car')->plural();

    // cars

    $plural = Str::of('child')->plural();

    // children

문자열의 단수 또는 복수 형식을 검색하기 위해 함수에 두 번째 인수로 정수를 제공할 수 있습니다.

    use Illuminate\Support\Str;

    $plural = Str::of('child')->plural(2);

    // children

    $plural = Str::of('child')->plural(1);

    // child

<a name="method-fluent-str-prepend"></a>
#### `prepend` {#collection-method}

`prepend` 메소드는 주어진 값을 문자열 앞에 추가합니다.

    use Illuminate\Support\Str;

    $string = Str::of('Framework')->prepend('Laravel ');

    // Laravel Framework

<a name="method-fluent-str-replace"></a>
#### `replace` {#collection-method}

`replace` 메소드는 문자열 내에서 주어진 문자열을 대체합니다.

    use Illuminate\Support\Str;

    $replaced = Str::of('Laravel 6.x')->replace('6.x', '7.x');

    // Laravel 7.x

<a name="method-fluent-str-replace-array"></a>
#### `replaceArray` {#collection-method}

`replaceArray` 메소드는 배열을 사용하여 문자열에서 주어진 값을 차례대로 바꿉니다.

    use Illuminate\Support\Str;

    $string = 'The event will take place between ? and ?';

    $replaced = Str::of($string)->replaceArray('?', ['8:30', '9:00']);

    // The event will take place between 8:30 and 9:00

<a name="method-fluent-str-replace-first"></a>
#### `replaceFirst` {#collection-method}

`replaceFirst` 메소드는 문자열에서 주어진 값이 발견된 첫 번째 부분을 대체합니다.

    use Illuminate\Support\Str;

    $replaced = Str::of('the quick brown fox jumps over the lazy dog')->replaceFirst('the', 'a');

    // a quick brown fox jumps over the lazy dog

<a name="method-fluent-str-replace-last"></a>
#### `replaceLast` {#collection-method}

`replaceLast` 메소드는 문자열에서 주어진 값이 발견된 마지막 부분을 대체합니다.

    use Illuminate\Support\Str;

    $replaced = Str::of('the quick brown fox jumps over the lazy dog')->replaceLast('the', 'a');

    // the quick brown fox jumps over a lazy dog

<a name="method-fluent-str-replace-matches"></a>
#### `replaceMatches` {#collection-method}

`replaceMatches` 메소드는 주어진 패턴과 일치하는 문자열의 모든 부분을 주어진 대체 문자열로 변경합니다.

    use Illuminate\Support\Str;

    $replaced = Str::of('(+1) 501-555-1000')->replaceMatches('/[^A-Za-z0-9]++/', '')

    // '15015551000'

`replaceMatches` 메소드는 또한 주어진 파티에 일치하는 문자열의 각 부분에 대해 호출되는 클로저를 허용하므로 closure 내에서 대체 로직을 실행하고 대체된 값을 반환할 수 있습니다.

    use Illuminate\Support\Str;

    $replaced = Str::of('123')->replaceMatches('/\d/', function ($match) {
        return '['.$match[0].']';
    });

    // '[1][2][3]'

<a name="method-fluent-str-rtrim"></a>
#### `rtrim` {#collection-method}

`rtrim` 메소드는 주어진 문자열 오른쪽의 공백문자를 제거합니다.

(역자주: 또는 하단과 같이 주어진 문자를 제거 합니다)

    use Illuminate\Support\Str;

    $string = Str::of('  Laravel  ')->rtrim();

    // '  Laravel'

    $string = Str::of('/Laravel/')->rtrim('/');

    // '/Laravel'

<a name="method-fluent-str-singular"></a>
#### `singular` {#collection-method}

`singular` 메소드는 문자열을 단수 형식으로 변환합니다. 이 기능은 현재 영어만 지원합니다.

    use Illuminate\Support\Str;

    $singular = Str::of('cars')->singular();

    // car

    $singular = Str::of('children')->singular();

    // child

<a name="method-fluent-str-slug"></a>
#### `slug` {#collection-method}

`slug` 메소드는 주어진 문자열로부터 URL에 알맞는 "slug"를 생성합니다.

    use Illuminate\Support\Str;

    $slug = Str::of('Laravel Framework')->slug('-');

    // laravel-framework

<a name="method-fluent-str-snake"></a>
#### `snake` {#collection-method}

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

`start` 메소드는 문자열이 주어진 값으로 시작하지 않으면 이를 문자열에 추가합니다.

    use Illuminate\Support\Str;

    $adjusted = Str::of('this/string')->start('/');

    // /this/string

    $adjusted = Str::of('/this/string')->start('/');

    // /this/string

<a name="method-fluent-str-starts-with"></a>
#### `startsWith` {#collection-method}

`startsWith` 메소드는 주어진 문자열이 주어진 값으로 시작하는지를 확인합니다.

    use Illuminate\Support\Str;

    $result = Str::of('This is my name')->startsWith('This');

    // true

<a name="method-fluent-str-studly"></a>
#### `studly` {#collection-method}

`studly` 메소드는 주어진 문자열을 `StudlyCase`로 변환합니다.

    use Illuminate\Support\Str;

    $converted = Str::of('foo_bar')->studly();

    // FooBar

<a name="method-fluent-str-substr"></a>
#### `substr` {#collection-method}

`substr` 메소드는 주어진 시작 및 길이 파라미터에 의해 지정된 문자열 부분을 반환합니다.

    use Illuminate\Support\Str;

    $string = Str::of('Laravel Framework')->substr(8);

    // Framework

    $string = Str::of('Laravel Framework')->substr(8, 5);

    // Frame

<a name="method-fluent-str-title"></a>
#### `title` {#collection-method}

`title` 메소드는 주어진 문자열을 `Title Case`로 변환합니다.

    use Illuminate\Support\Str;

    $converted = Str::of('a nice title uses the correct case')->title();

    // A Nice Title Uses The Correct Case

<a name="method-fluent-str-trim"></a>
#### `trim` {#collection-method}

`trim` 메소드는 주어진 문자열을 잘라냅니다.

    use Illuminate\Support\Str;

    $string = Str::of('  Laravel  ')->trim();

    // 'Laravel'

    $string = Str::of('/Laravel/')->trim('/');

    // 'Laravel'

<a name="method-fluent-str-ucfirst"></a>
#### `ucfirst` {#collection-method}

`ucfirst` 메소드는 첫 문자를 대문자로 하여 주어진 문자열을 반환합니다.

    use Illuminate\Support\Str;

    $string = Str::of('foo bar')->ucfirst();

    // Foo bar

<a name="method-fluent-str-upper"></a>
#### `upper` {#collection-method}

`upper` 메소드는 주어진 문자열을 대문자로 변환합니다.

    use Illuminate\Support\Str;

    $adjusted = Str::of('laravel')->upper();

    // LARAVEL

<a name="method-fluent-str-when"></a>
#### `when` {#collection-method}

`when` 메소드는 주어진 조건이 참이면 주어진 Closure를 호출합니다. Closure는 fluent string 인스턴스를받습니다.

    use Illuminate\Support\Str;

    $string = Str::of('Taylor')
                    ->when(true, function ($string) {
                        return $string->append(' Otwell');
                    });

    // 'Taylor Otwell'

필요한 경우 `when` 메소드의 세 번째 매개 변수로 다른 Closure를 전달할 수 있습니다. 조건 매개 변수가 `false`로 판별되면 이 Closure가 실행됩니다.

<a name="method-fluent-str-when-empty"></a>
#### `whenEmpty` {#collection-method}

`whenEmpty` 메소드는 문자열이 비어 있으면 주어진 클로저를 호출합니다. 클로저가 값을 반환하면 그 값도 `whenEmpty` 메소드에 의해 반환됩니다. 클로저가 값을 반환하지 않으면 fluent string 인스턴스가 반환됩니다.

    use Illuminate\Support\Str;

    $string = Str::of('  ')->whenEmpty(function ($string) {
        return $string->trim()->prepend('Laravel');
    });

    // 'Laravel'

<a name="method-fluent-str-words"></a>
#### `words` {#collection-method}

`words` 메소드는 문자열의 단어 수를 제한합니다.

    use Illuminate\Support\Str;

    $string = Str::of('Perfectly balanced, as all things should be.')->words(3, ' >>>');

    // Perfectly balanced, as >>>

<a name="urls"></a>
## URLs

<a name="method-action"></a>
#### `action()` {#collection-method}

`action` 함수는 주어진 컨트롤러 메소드로 URL을 생성합니다.

    $url = action([HomeController::class, 'index']);

메소드가 라우트 파라미터를 받아들인다면, 두 번째 인자로 메소드에 전달하십시오.

    $url = action([UserController::class, 'profile'], ['id' => 1]);

<a name="method-asset"></a>
#### `asset()` {#collection-method}

`asset` 함수는 HTTP요청의 현재 scheme(HTTP나 HTTPS)을 이용하여 asset을 사용하기 위한 URL을 생성합니다.

    $url = asset('img/photo.jpg');

`.env` 파일에 `ASSET_URL` 변수를 설정하여 asset의 URL 호스트를 설정할 수 있습니다. 이는 Amazon S3와 같은 외부 서비스에서 asset을 호스팅하는 경우 유용 할 수 있습니다.

    // ASSET_URL=http://example.com/assets

    $url = asset('img/photo.jpg'); // http://example.com/assets/img/photo.jpg

<a name="method-route"></a>
#### `route()` {#collection-method}

`route` 함수는 주어진 라우트 이름으로 URL을 생성합니다.

    $url = route('routeName');

라우트가 파라미터를 가진다면 파라미터를 두 번째 인자로 메소드에 전달하세요.

    $url = route('routeName', ['id' => 1]);

기본적으로 `route` 함수는 절대 경로 URL을 생성합니다. 만약 상태경로의 URL을 생성하려면 세번째 인자를 `false`로 전달하면 됩니다.

    $url = route('routeName', ['id' => 1], false);

<a name="method-secure-asset"></a>
#### `secure_asset()` {#collection-method}

`secure_asset` 함수는 HTTPS를 이용하여 asset을 사용하기 위한 URL을 생성합니다.

    $url = secure_asset('img/photo.jpg');

<a name="method-secure-url"></a>
#### `secure_url()` {#collection-method}

`secure_url` 함수는 주어진 경로에 대한 전체 HTTPS URL을 생성합니다.

    $url = secure_url('user/profile');

    $url = secure_url('user/profile', [1]);

<a name="method-url"></a>
#### `url()` {#collection-method}

`url` 함수는 주어진 경로에 대한 전체 URL을 생성합니다.

    $url = url('user/profile');

    $url = url('user/profile', [1]);

경로를 전달하지 않으면, `Illuminate\Routing\UrlGenerator` 인스턴스가 반환됩니다.

    $current = url()->current();

    $full = url()->full();

    $previous = url()->previous();

<a name="miscellaneous"></a>
## 기타 함수들

<a name="method-abort"></a>
#### `abort()` {#collection-method}

`abort` 함수는 [Exception 핸들러](/docs/{{version}}/errors#the-exception-handler)에 의해서 렌더링 될 수 있는 [HTTP exception](/docs/{{version}}/errors#http-exceptions)을 발생시킵니다.

    abort(403);

브라우저에 보내야 하는 exception 의 메세지를 제공하거나, 커스텀 헤더를 지정할 수도 있습니다.

    abort(403, 'Unauthorized.', $headers);

<a name="method-abort-if"></a>
#### `abort_if()` {#collection-method}

`abort_if` 함수는 주어진 조건식이 `true` 일때 HTTP exception을 발생시킵니다.

    abort_if(! Auth::user()->isAdmin(), 403);

`abort` 메소드와 같이, exception 의 응답 텍스트를 세번째 인자로 전달할 수 있으며, 네번째 인자로 커스텀 응답 헤더를 전달할 수도 있습니다.

<a name="method-abort-unless"></a>
#### `abort_unless()` {#collection-method}

`abort_unless` 함수는 주어진 조건식이 `false` 일때 HTTP exception 을 발생시킵니다.

    abort_unless(Auth::user()->isAdmin(), 403);

`abort` 메소드와 같이, exception 의 응답 텍스트를 세번째 인자로 전달할 수 있으며, 네번째 인자로 커스텀 응답 헤더를 전달할 수도 있습니다.

<a name="method-app"></a>
#### `app()` {#collection-method}

`app` 함수는 [서비스 컨테이너](/docs/{{version}}/container) 인스턴스를 반환합니다.

    $container = app();

컨테이너에 의존성을 해결하고자 하는 클래스나 인터페이스의 이름을 전달할 수도 있습니다.

    $api = app('HelpSpot\API');

<a name="method-auth"></a>
#### `auth()` {#collection-method}

`auth` 함수는 [authenticator](/docs/{{version}}/authentication) 인스턴스를 반환합니다. 편의를 위하여 `Auth` 파사드 대신 이용할 수 있습니다.

    $user = auth()->user();

필요한 경우, 어떤 guard 인스턴스를 사용할지 지정할 수 있습니다.

    $user = auth('admin')->user();

<a name="method-back"></a>
#### `back()` {#collection-method}

`back()` 함수는 사용자의 이전 위치로 [리다이렉트 HTTP response-응답](/docs/{{version}}/responses#redirects)을 생성합니다.

    return back($status = 302, $headers = [], $fallback = false);

    return back();

<a name="method-bcrypt"></a>
#### `bcrypt()` {#collection-method}

`bcrypt` 함수는 Bcrypt를 이용하여 주어진 값을 [해시](/docs/{{version}}/hashing) 처리합니다. `Hash` 파사드 대신 사용할 수 있습니다.

    $password = bcrypt('my-secret-password');

<a name="method-blank"></a>
#### `blank()` {#collection-method}

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

`blank`의 반대는, [`filled`](#method-filled) 메소드를 참고하십시오.

<a name="method-broadcast"></a>
#### `broadcast()` {#collection-method}

`broadcast` 함수는 주어진 [이벤트](/docs/{{version}}/events)를 리스너들에게 [broadcasts](/docs/{{version}}/broadcasting) 합니다.

    broadcast(new UserRegistered($user));

<a name="method-cache"></a>
#### `cache()` {#collection-method}

`cache` 함수는 [캐시](/docs/{{version}}/cache)로 부터 값을 가져오는데 사용할 수 있습니다. 캐시에서 주어진 키가 존재하지 않는 경우, 옵션으로 전달된 기본값(두 번째인자)가 반환됩니다

    $value = cache('key');

    $value = cache('key', 'default');

함수에 키 / 값으로 된 배열을 전달하여 캐시에 아이템을 추가할 수 있습니다. 또한 캐시에 값이 얼마나 유지되어야 하는지에 대한 시간(초)을 숫자로 전달할 수도 있습니다.

    cache(['key' => 'value'], 300);

    cache(['key' => 'value'], now()->addSeconds(10));

<a name="method-class-uses-recursive"></a>
#### `class_uses_recursive()` {#collection-method}

`class_uses_recursive` 함수는 모든 부모 클래스가 사용하는 trait를 포함하여 클래스가 사용하는 모든 trait을 반환합니다.

    $traits = class_uses_recursive(App\Models\User::class);

<a name="method-collect"></a>
#### `collect()` {#collection-method}

`collect` 함수는 주어진 값으로부터 [collection](/docs/{{version}}/collections) 인스턴스를 생성합니다.

    $collection = collect(['taylor', 'abigail']);

<a name="method-config"></a>
#### `config()` {#collection-method}

`config` 함수는 [설정](/docs/{{version}}/configuration) 변수의 값을 가져옵니다. 설정 값은 파일명과 접근하고자 하는 옵션을 포함하는 "점(.)" 문법(syntax)를 사용하여 접근할 수 있습니다. 설정 옵션이 존재하지 않는다면 지정된 기본값이 반환됩니다.

    $value = config('app.timezone');

    $value = config('app.timezone', $default);

런타임에 설정 값을 키 / 값 형태로 전달하여 값을 설정할 수도 있습니다.

    config(['app.debug' => true]);

<a name="method-cookie"></a>
#### `cookie()` {#collection-method}

`cookie` 함수는 새로운 [쿠키](/docs/{{version}}/requests#cookies) 인스턴스를 생성합니다.

    $cookie = cookie('name', 'value', $minutes);

<a name="method-csrf-field"></a>
#### `csrf_field()` {#collection-method}

`csrf_field` 함수는 CSRF 토큰 값을 포함하는 HTML `hidden` Input 필드를 생성합니다. 예를 들어 [Blade syntax](/docs/{{version}}/blade)에서 사용할 수 있습니다.

    {{ csrf_field() }}

<a name="method-csrf-token"></a>
#### `csrf_token()` {#collection-method}

`csrf_token` 함수는 현재 CSRF 토큰의 값을 조회합니다.

    $token = csrf_token();

<a name="method-dd"></a>
#### `dd()` {#collection-method}

`dd` 함수는 주어진 변수들을 Dump 처리하고 스크립트의 실행을 중단합니다.

    dd($value);

    dd($value1, $value2, $value3, ...);

스크립트 실행을 중단하고 싶지 않다면, [`dump`](#method-dump) 함수를 사용하십시오:

<a name="method-dispatch"></a>
#### `dispatch()` {#collection-method}

`dispatch` 함수는 라라벨의 [job queue](/docs/{{version}}/queues)에 주어진 [job](/docs/{{version}}/queues#creating-jobs)을 추가합니다.

    dispatch(new App\Jobs\SendEmails);

<a name="method-dispatch-now"></a>
#### `dispatch_now()` {#collection-method}

`dispatch_now` 함수는 주어진 [job](/docs/{{version}}/queues#creating-jobs)을 즉시 실행하고 `handle` 메소드의 값을 반환합니다.

    $result = dispatch_now(new App\Jobs\SendEmails);

<a name="method-dump"></a>
#### `dump()` {#collection-method}

`dump` 함수는 주어진 변수의 값을 덤프하여 표시합니다.

    dump($value);

    dump($value1, $value2, $value3, ...);

변수읙 값을 표시한 다음에 스크립트의 실행을 멈추고자 한다면, [`dd`](#method-dd)함수를 사용하십시오.

<a name="method-env"></a>
#### `env()` {#collection-method}

`env` 함수는 [환경변수](/docs/{{version}}/configuration#environment-configuration)의 값을 가져오거나 기본값을 조회합니다.

    $env = env('APP_ENV');

    // Returns 'production' if APP_ENV is not set...
    $env = env('APP_ENV', 'production');

> {note} 배포과정에서 `config:cache` 명령어를 실행했다면, 설정 파일안에서 `env` 함수를 호출한 부분이 있는지 확인해야 합니다. 설정이 캐싱되고 나면, `.env` 파일은 로드하지 않고, 모든 `env` 함수는 `null`을 반환합니다.

<a name="method-event"></a>
#### `event()` {#collection-method}

`event` 함수는 주어진 [event](/docs/{{version}}/events)를 리스너들에게 보냅니다.

    event(new UserRegistered($user));

<a name="method-factory"></a>
#### `factory()` {#collection-method}

`factory` 함수는 주어진 클래스, 이름, 양을 위한 모델 팩토리 빌더를 생성합니다. [testing](/docs/{{version}}/database-testing#writing-factories)이나 [seeding](/docs/{{version}}/seeding#using-model-factories) 중에 이용할 수 있습니다.

    $user = factory(App\User::class)->make();

<a name="method-filled"></a>
#### `filled()` {#collection-method}

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

`filled`의 반대는, [`blank`](#method-blank) 메소드를 확인하십시오.

<a name="method-info"></a>
#### `info()` {#collection-method}

`info` 함수는 [로그](/docs/{{version}}/logging)에 정보를 기록합니다.

    info('Some helpful information!');

문맥에대한 데이터를 함수에 배열로 전달할 수도 있습니다.

    info('User login attempt failed.', ['id' => $user->id]);

<a name="method-logger"></a>
#### `logger()` {#collection-method}

`logger` 함수는 [로그](/docs/{{version}}/logging)에 `debug` 로그 레벨을 기록하는데 사용합니다.

    logger('Debug message');

문맥에대한 데이터를 함수에 배열로 전달할 수도 있습니다.

    logger('User has logged in.', ['id' => $user->id]);

함수에 아무런 값이 전달되지 않으면 [logger](/docs/{{version}}/errors#logging) 인스턴스가 반환됩니다.

    logger()->error('You are not allowed here.');

<a name="method-method-field"></a>
#### `method_field()` {#collection-method}

`method_field` 함수는 HTTP 메소드 형식의 가짜(spoof) 값을 포함하는 HTML `hidden` Input 필드를 생성합니다. 예를 들어 [Blade syntax](/docs/{{version}}/blade)에서 사용할 수 있습니다.

    <form method="POST">
        {{ method_field('DELETE') }}
    </form>

<a name="method-now"></a>
#### `now()` {#collection-method}

`now` 함수는 현재 시간을 기반으로한 `Illuminate\Support\Carbon` 인스턴스를 생성합니다.

    $now = now();

<a name="method-old"></a>
#### `old()` {#collection-method}

`old` 함수는 세션에 저장된 [이전 입력값](/docs/{{version}}/requests#old-input)(flashed)을 [조회](/docs/{{version}}/requests#retrieving-input)합니다.
    $value = old('value');

    $value = old('value', 'default');

<a name="method-optional"></a>
#### `optional()` {#collection-method}

`optional` 함수는 인자를 전달받아 해당 객체의 프로퍼티에 엑세스 하거나, 메소드를 호출할 수 있도록 합니다. 지정된 객체가 `null` 이라면, 프로퍼티와 메소드는 에러를 유발하는 대신에 `null` 을 반환합니다.

    return optional($user->address)->street;

    {!! old('name', optional($user)->name) !!}

`optional` 함수는 두 번째 인자로 클로저를 받을 수 있습니다. 첫 번째 인자가 `null`이 아닌경우, 클로저가 호출됩니다.

    return optional(User::find($id), function ($user) {
        return new DummyUser;
    });

<a name="method-policy"></a>
#### `policy()` {#collection-method}

`policy` 메소도는 주어진 클래스를 위한 [policy](/docs/{{version}}/authorization#creating-policies) 인스턴스를 조회합니다.

    $policy = policy(App\Models\User::class);

<a name="method-redirect"></a>
#### `redirect()` {#collection-method}

`redirect` 함수는 [리다이렉트 HTTP response-응답](/docs/{{version}}/responses#redirects)을 반환하거나, 인자 없이 호출되는 경우 리디렉터 인스턴스를 반환합니다.

    return redirect($to = null, $status = 302, $headers = [], $secure = null);

    return redirect('/home');

    return redirect()->route('route.name');

<a name="method-report"></a>
#### `report()` {#collection-method}

`report` 함수는 [exception 핸들러](/docs/{{version}}/errors#the-exception-handler)를 예외(exception)를 보고합니다.

    report($e);

<a name="method-request"></a>
#### `request()` {#collection-method}

`request` 함수는 현재의 [요청](/docs/{{version}}/requests) 인스턴스를 반환하거나 입력 아이템을 가져옵니다.

    $request = request();

    $value = request('key', $default);

<a name="method-rescue"></a>
#### `rescue()` {#collection-method}

`rescue` 함수는 주어진 클로저를 실행하고, 실행중 발생하는 예외(exception)을 받아냅니다. catch 된 모든 예외(exception) [exception 핸들러](/docs/{{version}}/errors#the-exception-handler)의 요청(request)에 대한 처리는 계속됩니다.

    return rescue(function () {
        return $this->method();
    });

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

`resolve` 함수는 [서비스 컨테이너](/docs/{{version}}/container)를 사용하여 주어진 클래스 또는 인터페이스를 의존성 해결하여 인스턴스를 반환합니다.

    $api = resolve('HelpSpot\API');

<a name="method-response"></a>
#### `response()` {#collection-method}

`response` 함수는 [응답](/docs/{{version}}/responses) 인스턴스를 생성하거나 응답 팩토리의 인스턴스를 가져옵니다.

    return response('Hello World', 200, $headers);

    return response()->json(['foo' => 'bar'], 200, $headers);

<a name="method-retry"></a>
#### `retry()` {#collection-method}

`retry` 함수는 주어진 최대 횟수가 될 때까지 주어진 콜백을 실행하려고 시도합니다. 콜백이 예외-exception를 던지지 않는다면, 결과 값이 반환됩니다. 콜백이 예외-exception를 던지면 자동으로 다시 시도됩니다. 최대 재시도 횟수를 초과하면 예외가 던져집니다.

    return retry(5, function () {
        // Attempt 5 times while resting 100ms in between attempts...
    }, 100);

<a name="method-session"></a>
#### `session()` {#collection-method}

`session` 함수는 [세션](/docs/{{version}}/session) 값을 얻거나 지정하는 데에 사용할 수 있습니다.

    $value = session('key');

키 / 값 쌍들의 배열을 함수로 전달하여 값을 설정할 수 있습니다.

    session(['chairs' => 7, 'instruments' => 3]);

함수에 아무런 값도 전달되지 않는다면 세션 스토어가 반환됩니다.

    $value = session()->get('key');

    session()->put('key', $value);

<a name="method-tap"></a>
#### `tap()` {#collection-method}

`tap` 함수는 임의의 `$value` 와 클로저 두개의 인자를 받아들입니다. `$value` 는 클로저에 전달되어 `tap` 함수에 의해서 반환됩니다. 반환되는 값은 클로저와 무관합니다.

    $user = tap(User::first(), function ($user) {
        $user->name = 'taylor';

        $user->save();
    });

`tap` 함수에 클로저를 전달하지 않는다면, 주어진 `$value` 에 모든 메소드를 호출할 수 있습니다. 메소드에서 반환하는 값은 메소드가 실제로 정의해서 반환하는 `$value` 와는 관계없이 항상 `$value` 가 됩니다. 예를 들어 Eloquent update 메소드는 일반적으로 정수값을 반환하지만, `tap` 메소드를 통해서 `update` 메소드를 호출을 체이닝하면 메소드가 모델 그 자체를 반환하도록 할 수 있습니다.

    $user = tap($user)->update([
        'name' => $name,
        'email' => $email,
    ]);

클래스에 `tap` 메소드를 추가하기 위해서는 `Illuminate\Support\Traits\Tappable` Trait을 클래스에 추가하면됩니다. 이 Trait의 `tap` 메소드는 Closure만 인수로 받아들입니다. 객체 인스턴스 자체는 Closure에 전달되고 `tap` 메소드에 의해 반환됩니다.

    return $user->tap(function ($user) {
        //
    });

<a name="method-throw-if"></a>
#### `throw_if()` {#collection-method}

`throw_if` 함수는 주어진 결과가 `true` 인 경우에 주어진 exception-예외를 던집니다.

    throw_if(! Auth::user()->isAdmin(), AuthorizationException::class);

    throw_if(
        ! Auth::user()->isAdmin(),
        AuthorizationException::class,
        'You are not allowed to access this page'
    );

<a name="method-throw-unless"></a>
#### `throw_unless()` {#collection-method}

`throw_unless` 함수는 주어진 결과가 `false` 인 경우에 주어진 exception-예외를 던집니다.

    throw_unless(Auth::user()->isAdmin(), AuthorizationException::class);

    throw_unless(
        Auth::user()->isAdmin(),
        AuthorizationException::class,
        'You are not allowed to access this page'
    );

<a name="method-today"></a>
#### `today()` {#collection-method}

`today` 함수는 현재 날짜를 기준으로한 `Illuminate\Support\Carbon` 인스턴스를 생성합니다.

    $today = today();

<a name="method-trait-uses-recursive"></a>
#### `trait_uses_recursive()` {#collection-method}

`trait_uses_recursive` 함수는 해당 trait에서 사용된 모든 trait을 반환합니다.

    $traits = trait_uses_recursive(\Illuminate\Notifications\Notifiable::class);

<a name="method-transform"></a>
#### `transform()` {#collection-method}

`transform` 함수는 주어진 값이 [빈값](#method-blank)이 아닌 경우에 `Closure`를 실행하고 `Closure`의 결과를 반환합니다.

    $callback = function ($value) {
        return $value * 2;
    };

    $result = transform(5, $callback);

    // 10

세번째 인자로 기본값 또는 `Closure` 가 전달될 수 있습니다. 이 값은 첫 번째 인자가 빈값인 경우 반환됩니다.

    $result = transform(null, $callback, 'The value is blank');

    // The value is blank

<a name="method-validator"></a>
#### `validator()` {#collection-method}

`validator` 함수는 주어진 인자를 통해서 새로운 [validator](/docs/{{version}}/validation) 인스턴스를 생성합니다. 보다 편리하게 `Validator` 파사드를 사용할 수도 있습니다.

    $validator = validator($data, $rules, $messages);

<a name="method-value"></a>
#### `value()` {#collection-method}

`value` 함수는 자신에게 주어진 값을 그대로 반환합니다. 그렇지만 함수에 `Closure`를 전달하면 `Closure`가 실행되고 그 결과물이 반환됩니다.

    $result = value(true);

    // true

    $result = value(function () {
        return false;
    });

    // false

<a name="method-view"></a>
#### `view()` {#collection-method}

`view` 함수는 [view](/docs/{{version}}/views) 인스턴스를 조회합니다.

    return view('auth.login');

<a name="method-with"></a>
#### `with()` {#collection-method}

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
