# Collections
# 컬렉션

- [Introduction](#introduction)
- [소개](#introduction)
    - [Creating Collections](#creating-collections)
    - [컬렉션 생성하기](#creating-collections)
- [Available Methods](#available-methods)
- [사용 가능한 메소드](#available-methods)

<a name="introduction"></a>
## Introduction
## 소개

The `Illuminate\Support\Collection` class provides a fluent, convenient wrapper for working with arrays of data. For example, check out the following code. We'll use the `collect` helper to create a new collection instance from the array, run the `strtoupper` function on each element, and then remove all empty elements:

`Illuminate\Support\Collection` 클래스는 배열 데이터를 사용하기위한 유연하고 편리한 래퍼(wrapper)를 제공합니다. 다음의 사용예제를 확인하십시오. `collect` 헬퍼 함수를 사용하여 배열에서 새로운 collection 인스턴스를 생성하고 모든 요소들을 대문자로 변경한 다음 비어있는 요소들을 제거하는 예제 입니다:

    $collection = collect(['taylor', 'abigail', null])->map(function ($name) {
        return strtoupper($name);
    })
    ->reject(function ($name) {
        return empty($name);
    });


As you can see, the `Collection` class allows you to chain its methods to perform fluent mapping and reducing of the underlying array. In general, collections are immutable, meaning every `Collection` method returns an entirely new `Collection` instance.

보시는 바와 같이 `Collection` 클래스는 map 매소드와 reject메소드를 체이닝 방식으로 사용할 수 있게 해줍니다. 다시 말해서 컬렉션은 불변하며(immutable) 모든 `Collection`의 메소드는 `Collection`의 인스턴스를 반환합니다. 

<a name="creating-collections"></a>
### Creating Collections
### 컬렉션 생성하기

As mentioned above, the `collect` helper returns a new `Illuminate\Support\Collection` instance for the given array. So, creating a collection is as simple as:

앞서 설명한 대로 `collect` 헬퍼 함수는 주어진 배열으로 부터 `Illuminate\Support\Collection` 인스턴스를 반환합니다. 따라서 컬렉션을 생성하는 것은 보시는 바와 같이 아주 간단합니다. 

    $collection = collect([1, 2, 3]);

The results of [Eloquent](/docs/{{version}}/eloquent) queries are always returned as `Collection` instances.

[Eloquent](/docs/{{version}}/eloquent) 쿼리는 항상 `Collection` 인스턴스를 반환합니다.

<a name="available-methods"></a>
## Available Methods
## 사용가능한 메소드

For the remainder of this documentation, we'll discuss each method available on the `Collection` class. Remember, all of these methods may be chained to fluently manipulating the underlying array. Furthermore, almost every method returns a new `Collection` instance, allowing you to preserve the original copy of the collection when necessary:

이 문서의 나머지 부분에서는 `Collection` 클래스에서 사용할 수 있는 각각의 메소드를 설명합니다. 모든 메소드들은 보다 유연하게 배열을 처리할 수 있도록 체이닝 할 수 있다는 것을 기억하십시오. 또한, 대부분의 모든 메소드는 필요한 경우 컬렉션 원래의 컬렉션을 사용할 수 있도록, 새로운 `Collection` 인스턴스를 반환합니다: 

<style>
    #collection-method-list > p {
        column-count: 3; -moz-column-count: 3; -webkit-column-count: 3;
        column-gap: 2em; -moz-column-gap: 2em; -webkit-column-gap: 2em;
    }

    #collection-method-list a {
        display: block;
    }
</style>

- [all](#method-all)
- [average](#method-average)
- [avg](#method-avg)
- [chunk](#method-chunk)
- [collapse](#method-collapse)
- [contains](#method-contains)
- [count](#method-count)
- [diff](#method-diff)
- [each](#method-each)
- [every](#method-every)
- [except](#method-except)
- [filter](#method-filter)
- [first](#method-first)
- [flatMap](#method-flatmap)
- [flatten](#method-flatten)
- [flip](#method-flip)
- [forget](#method-forget)
- [forPage](#method-forpage)
- [get](#method-get)
- [groupBy](#method-groupby)
- [has](#method-has)
- [implode](#method-implode)
- [intersect](#method-intersect)
- [isEmpty](#method-isempty)
- [keyBy](#method-keyby)
- [keys](#method-keys)
- [last](#method-last)
- [map](#method-map)
- [max](#method-max)
- [merge](#method-merge)
- [min](#method-min)
- [only](#method-only)
- [pluck](#method-pluck)
- [pop](#method-pop)
- [prepend](#method-prepend)
- [pull](#method-pull)
- [push](#method-push)
- [put](#method-put)
- [random](#method-random)
- [reduce](#method-reduce)
- [reject](#method-reject)
- [reverse](#method-reverse)
- [search](#method-search)
- [shift](#method-shift)
- [shuffle](#method-shuffle)
- [slice](#method-slice)
- [sort](#method-sort)
- [sortBy](#method-sortby)
- [sortByDesc](#method-sortbydesc)
- [splice](#method-splice)
- [sum](#method-sum)
- [take](#method-take)
- [toArray](#method-toarray)
- [toJson](#method-tojson)
- [transform](#method-transform)
- [unique](#method-unique)
- [values](#method-values)
- [where](#method-where)
- [whereLoose](#method-whereloose)
- [zip](#method-zip)

<a name="method-listing"></a>
## Method Listing

<style>
    #collection-method code {
        font-size: 14px;
    }

    #collection-method:not(.first-collection-method) {
        margin-top: 50px;
    }
</style>

<a name="method-all"></a>
#### `all()` {#collection-method .first-collection-method}

The `all` method returns the underlying array represented by the collection:

`all` 메소드는 주어진 배열을 컬렉션으로 되돌려 줍니다: 

    collect([1, 2, 3])->all();

    // [1, 2, 3]

<a name="method-average"></a>
#### `average()` {#collection-method}

Alias for the [`avg`](#method-avg) method.

[`avg`](#method-avg) 메소드의 별칭입니다.

<a name="method-avg"></a>
#### `avg()` {#collection-method}

The `avg` method returns the [average value](https://en.wikipedia.org/wiki/Average) of a given key:

`avg` 메소드는 주어진 키의 [평균값](https://en.wikipedia.org/wiki/Average)을 반환합니다: 

    $average = collect([['foo' => 10], ['foo' => 10], ['foo' => 20], ['foo' => 40]])->avg('foo');

    // 20

    $average = collect([1, 1, 2, 4])->avg();

    // 2

<a name="method-chunk"></a>
#### `chunk()` {#collection-method}

The `chunk` method breaks the collection into multiple, smaller collections of a given size:

`chunk` 메소드는 컬렉션을 주어진 사이즈의 더 작은 여러개로 분할합니다.

    $collection = collect([1, 2, 3, 4, 5, 6, 7]);

    $chunks = $collection->chunk(4);

    $chunks->toArray();

    // [[1, 2, 3, 4], [5, 6, 7]]

This method is especially useful in [views](/docs/{{version}}/views) when working with a grid system such as [Bootstrap](https://getbootstrap.com/css/#grid). Imagine you have a collection of [Eloquent](/docs/{{version}}/eloquent) models you want to display in a grid:

이 메소드는 특히 [뷰-views](/docs/{{version}}/views) 안에서 [부트스트랩](https://getbootstrap.com/css/#grid)과 같은 그리드 시스템을 작업할 때 유용합니다. [Eloquent](/docs/{{version}}/eloquent) 모델 컬렉션을 그리드에 표시한다고 생각해 보십시오:

    @foreach ($products->chunk(3) as $chunk)
        <div class="row">
            @foreach ($chunk as $product)
                <div class="col-xs-4">{{ $product->name }}</div>
            @endforeach
        </div>
    @endforeach

<a name="method-collapse"></a>
#### `collapse()` {#collection-method}

The `collapse` method collapses a collection of arrays into a single, flat collection:

`collapse` 메소드는 배열의 컬렉션을 하나의 컬렉션으로 만듭니다:
 
    $collection = collect([[1, 2, 3], [4, 5, 6], [7, 8, 9]]);

    $collapsed = $collection->collapse();

    $collapsed->all();

    // [1, 2, 3, 4, 5, 6, 7, 8, 9]

<a name="method-contains"></a>
#### `contains()` {#collection-method}

The `contains` method determines whether the collection contains a given item:

`contains` 메소드는 컬렉션에 주어진 아이템을 포함하고 있는지 알려줍니다: 

    $collection = collect(['name' => 'Desk', 'price' => 100]);

    $collection->contains('Desk');

    // true

    $collection->contains('New York');

    // false

You may also pass a key / value pair to the `contains` method, which will determine if the given pair exists in the collection:

또한 주어진 키/값 쌍이 컬렉션 안에 존재하는지 확인할 수 있도록 `container` 메소드에 키/값 쌍을 인자로 전달할 수도 있습니다:

    $collection = collect([
        ['product' => 'Desk', 'price' => 200],
        ['product' => 'Chair', 'price' => 100],
    ]);

    $collection->contains('product', 'Bookcase');

    // false

Finally, you may also pass a callback to the `contains` method to perform your own truth test:

마지막으로, `contains` 메소드에 주어진 조건을 판별할 수 있는 콜백을 인자로 전달할 수도 있습니다:

    $collection = collect([1, 2, 3, 4, 5]);

    $collection->contains(function ($key, $value) {
        return $value > 5;
    });

    // false

The `contains` method uses "loose" comparisons when checking item values, meaning a string with an integer value will be considered equal to an integer of the same value.

`contains` 메소드는 아이템의 값을 비교할 때 "느슨한" 비교를 수행하기 때문에, 정수값이 문자형일 때에도 정수형 값과 동일하다고 판단합니다.

<a name="method-count"></a>
#### `count()` {#collection-method}

The `count` method returns the total number of items in the collection:

`count` 메소드는 컬렉션안에 존재하는 아이템의 전체 갯수를 반환합니다:

    $collection = collect([1, 2, 3, 4]);

    $collection->count();

    // 4

<a name="method-diff"></a>
#### `diff()` {#collection-method}

The `diff` method compares the collection against another collection or a plain PHP `array` based on its values. This method will return the values in the original collection that are not present in the given collection:

`diff` 메소드는 컬렉션을 다른 컬렉션 또는 일반적인 PHP `배열`을 값을 기준으로 비교합니다. 이 메소드는 주어진 컬렉션 안에 들어 있지 않은 원래의 컬렉션 안에 있는 값을 반환합니다:

    $collection = collect([1, 2, 3, 4, 5]);

    $diff = $collection->diff([2, 4, 6, 8]);

    $diff->all();

    // [1, 3, 5]

<a name="method-each"></a>
#### `each()` {#collection-method}

The `each` method iterates over the items in the collection and passes each item to a callback:

`each` 메소드는 컬렉션의 아이템을 반복적으로 처리하여 콜백에 각 아이템을 전달합니다:

    $collection = $collection->each(function ($item, $key) {
        //
    });

If you would like to stop iterating through the items, you may return `false` from your callback:

전체 항목에 대한 반복을 중지하려면, 콜백 안에서 `false` 를 반환하면 됩니다:

    $collection = $collection->each(function ($item, $key) {
        if (/* some condition */) {
            return false;
        }
    });

<a name="method-every"></a>
#### `every()` {#collection-method}

The `every` method creates a new collection consisting of every n-th element:

`every` 메소드는 매 n 번째 존재하는 요소로 구성된 새로운 컬렉션을 생성합니다. 

(역자주: 첫번째 인자로 주어진 숫자로 나누어 나머지가 0인 경우의 아이템들로 구성된 컬렉션이 반환됩니다. 인덱스는 0부터 시작)

    $collection = collect(['a', 'b', 'c', 'd', 'e', 'f']);

    $collection->every(4);

    // ['a', 'e']

You may optionally pass an offset as the second argument:

두번째 인자로 offset을 전달할 수도 있습니다:

    $collection->every(4, 1);

    // ['b', 'f']

<a name="method-except"></a>
#### `except()` {#collection-method}

The `except` method returns all items in the collection except for those with the specified keys:

`except` 메소드는 지정된 키에 대한 아이템을 제외한 컬렉션의 모든 아이템을 반환합니다: 

    $collection = collect(['product_id' => 1, 'price' => 100, 'discount' => false]);

    $filtered = $collection->except(['price', 'discount']);

    $filtered->all();

    // ['product_id' => 1]

For the inverse of `except`, see the [only](#method-only) method.

<a name="method-filter"></a>
#### `filter()` {#collection-method}

The `filter` method filters the collection using the given callback, keeping only those items that pass a given truth test:

`filter` 메소드는 주어진 콜백을 사용하여 컬렉션을 필터링하고, 주어진 테스트에 true를 반환하는 항목 만 남겨둡니다:

    $collection = collect([1, 2, 3, 4]);

    $filtered = $collection->filter(function ($value) {
        return $value > 2;
    });

    $filtered->all();

    // [3, 4]

If no callback is supplied, all entries of the collection that are equivalent to `false` will be removed:

콜백이 전달되지 않는다면, 모든 컬렉션의 엔트리 중에서 `false` 로 인식되는 값들은 제거됩니다:

    $collection = collect([1, 2, 3, null, false, '', 0, []]);

    $collection->filter()->all();

    // [1, 2, 3]

For the inverse of `filter`, see the [reject](#method-reject) method.

`filter` 의 반대는 [reject](#method-reject) 메소드를 확인하십시오.

<a name="method-first"></a>
#### `first()` {#collection-method}

The `first` method returns the first element in the collection that passes a given truth test:

`first` 메소드는 주어진 테스트를 통과하는 컬렉션의 첫 번째 요소를 반환합니다.

    collect([1, 2, 3, 4])->first(function ($key, $value) {
        return $value > 2;
    });

    // 3

You may also call the `first` method with no arguments to get the first element in the collection. If the collection is empty, `null` is returned:

`first` 메소드를 아무런 인자 없이 호출하여 컬렉션의 첫 번째 요소를 획득할 수 있습니다. 만약 컬렉션이 비어 있다면 `null` 이 반환됩니다.

    collect([1, 2, 3, 4])->first();

    // 1

<a name="method-flatmap"></a>
#### `flatMap()` {#collection-method}

The `flatMap` method iterates through the collection and passes each value to the given callback. The callback is free to modify the item and return it, thus forming a new collection of modified items. Then, the array is flattened by a level:

`flatMap` 메소드는 각각의 컬렉션을 반복하며 각가의 값을 주어진 콜백에 전달합니다. 콜백은 자유롭게 이 값을 변경하고 돌려주며 이를 통해서 수정된 값을 기반으로 새로운 컬렉션을 만듭니다. 이 배열은 일차원이 됩니다:

    $collection = collect([
        ['name' => 'Sally'],
        ['school' => 'Arkansas'],
        ['age' => 28]
    ]);

    $flattened = $collection->flatMap(function ($values) {
        return array_map('strtoupper', $values);
    });

    $flattened->all();

    // ['name' => 'SALLY', 'school' => 'ARKANSAS', 'age' => '28'];

<a name="method-flatten"></a>
#### `flatten()` {#collection-method}

The `flatten` method flattens a multi-dimensional collection into a single dimension:

`flatten` 메소드는 다차원으로 구성된 컬렉션을 한 차원으로 변경시킵니다.

    $collection = collect(['name' => 'taylor', 'languages' => ['php', 'javascript']]);

    $flattened = $collection->flatten();

    $flattened->all();

    // ['taylor', 'php', 'javascript'];

<a name="method-flip"></a>
#### `flip()` {#collection-method}

The `flip` method swaps the collection's keys with their corresponding values:

`flip` 메소드는 컬렉션의 키와 대응하는 값을 바꿉니다.

    $collection = collect(['name' => 'taylor', 'framework' => 'laravel']);

    $flipped = $collection->flip();

    $flipped->all();

    // ['taylor' => 'name', 'laravel' => 'framework']

<a name="method-forget"></a>
#### `forget()` {#collection-method}

The `forget` method removes an item from the collection by its key:

`forget` 메소드는 키에 해당하는 컬렉션의 아이템을 삭제합니다.

    $collection = collect(['name' => 'taylor', 'framework' => 'laravel']);

    $collection->forget('name');

    $collection->all();

    // ['framework' => 'laravel']

> **Note:** Unlike most other collection methods, `forget` does not return a new modified collection; it modifies the collection it is called on.

> **주의:** 다른 컬렉션 메소드와는 달리 `forget`는 수정된 새로운 컬렉션을 반환하지 않습니다; 호출된 컬렉션을 변경합니다.

<a name="method-forpage"></a>
#### `forPage()` {#collection-method}

The `forPage` method returns a new collection containing the items that would be present on a given page number. The method accepts the page number as its first argument and the number of items to show per page as its second argument:

`forPage` 메소드는 주어진 페이지 넘버에 해당하는 아이템을 가지고 있는 새로운 컬렉션을 반환합니다. 이 메소드는 페이지 번호를 첫번째 인자로 페이지별로 보여주고자 하는 아이템의 갯수를 두번째 인자로 받아 들입니다: 

    $collection = collect([1, 2, 3, 4, 5, 6, 7, 8, 9]);

    $chunk = $collection->forPage(2, 3);

    $chunk->all();

    // [4, 5, 6]

<a name="method-get"></a>
#### `get()` {#collection-method}

The `get` method returns the item at a given key. If the key does not exist, `null` is returned:

`get` 메소드는 주어진 키에 대한 아이템을 반환합니다. 키가 존재하지 않느다면, `null` 이 반환됩니다:

    $collection = collect(['name' => 'taylor', 'framework' => 'laravel']);

    $value = $collection->get('name');

    // taylor

You may optionally pass a default value as the second argument:

두번째 인자로 기본값을 전달할 수도 있습니다:

    $collection = collect(['name' => 'taylor', 'framework' => 'laravel']);

    $value = $collection->get('foo', 'default-value');

    // default-value

You may even pass a callback as the default value. The result of the callback will be returned if the specified key does not exist:

기본값을 결정하는 콜백을 전달할 수도 있습니다. 지정된 키가 존재하지 않는 경우에 콜백의 결과가 반환될 것입니다: 

    $collection->get('email', function () {
        return 'default-value';
    });

    // default-value

<a name="method-groupby"></a>
#### `groupBy()` {#collection-method}

The `groupBy` method groups the collection's items by a given key:

`groupBy` 메소드는 주어진 키를 기준으로한 컬렉션의 아이템을 그룹을 지정합니다:

    $collection = collect([
        ['account_id' => 'account-x10', 'product' => 'Chair'],
        ['account_id' => 'account-x10', 'product' => 'Bookcase'],
        ['account_id' => 'account-x11', 'product' => 'Desk'],
    ]);

    $grouped = $collection->groupBy('account_id');

    $grouped->toArray();

    /*
        [
            'account-x10' => [
                ['account_id' => 'account-x10', 'product' => 'Chair'],
                ['account_id' => 'account-x10', 'product' => 'Bookcase'],
            ],
            'account-x11' => [
                ['account_id' => 'account-x11', 'product' => 'Desk'],
            ],
        ]
    */

In addition to passing a string `key`, you may also pass a callback. The callback should return the value you wish to key the group by:

문자로 된 `key` 를 전달하는 것에 더해서, 콜백을 전달할 수도 있습니다. 콜백은 그룹으로 지정할 키를 반환해야 합니다.

    $grouped = $collection->groupBy(function ($item, $key) {
        return substr($item['account_id'], -3);
    });

    $grouped->toArray();

    /*
        [
            'x10' => [
                ['account_id' => 'account-x10', 'product' => 'Chair'],
                ['account_id' => 'account-x10', 'product' => 'Bookcase'],
            ],
            'x11' => [
                ['account_id' => 'account-x11', 'product' => 'Desk'],
            ],
        ]
    */

<a name="method-has"></a>
#### `has()` {#collection-method}

The `has` method determines if a given key exists in the collection:

`has` 메소드는 주어진 키가 컬렉션 안에 존재하는지 알려줍니다:

    $collection = collect(['account_id' => 1, 'product' => 'Desk']);

    $collection->has('product');

    // true

<a name="method-implode"></a>
#### `implode()` {#collection-method}

The `implode` method joins the items in a collection. Its arguments depend on the type of items in the collection. If the collection contains arrays or objects, you should pass the key of the attributes you wish to join, and the "glue" string you wish to place between the values:

`implode` 메소드는 컬렉션 안의 아이템들을 합쳐줍니다. 이 메소드의 인자는 컬렉션 안에 있는 아이템들의 타입에 의존합니다. 컬렉션이 배열 또는 객체를 가지고 있다면, 합치고자 하는 속성의 키와 값들 사이에 끼워 넣고자 하는 "glue" 문자열을 전달해야 합니다:

    $collection = collect([
        ['account_id' => 1, 'product' => 'Desk'],
        ['account_id' => 2, 'product' => 'Chair'],
    ]);

    $collection->implode('product', ', ');

    // Desk, Chair

If the collection contains simple strings or numeric values, simply pass the "glue" as the only argument to the method:

컬렉션이 간단한 문자열 또는 숫자값을 가지고 있다면, 간단하게 "glue" 를 첫번째 인자로 전달하면 됩니다:

    collect([1, 2, 3, 4, 5])->implode('-');

    // '1-2-3-4-5'

<a name="method-intersect"></a>
#### `intersect()` {#collection-method}

The `intersect` method removes any values from the original collection that are not present in the given `array` or collection. The resulting collection will preserve the original collection's keys:

`intersect` 메소드는 원래의 컬렉션에서 주어진 `배열` 또는 컬렉션에 존재하지 않는 값을 제거합니다. 메소드를 호출한 뒤에도 원래 컬렉션의 키 값은 보존됩니다:

    $collection = collect(['Desk', 'Sofa', 'Chair']);

    $intersect = $collection->intersect(['Desk', 'Chair', 'Bookcase']);

    $intersect->all();

    // [0 => 'Desk', 2 => 'Chair']

<a name="method-isempty"></a>
#### `isEmpty()` {#collection-method}

The `isEmpty` method returns `true` if the collection is empty; otherwise, `false` is returned:

`isEmpty` 메소드는 컬렉션이 비어 있는 경우 `true` 를 반환하고, 그렇지 않은 경우 `false` 를 반환합니다:

    collect([])->isEmpty();

    // true

<a name="method-keyby"></a>
#### `keyBy()` {#collection-method}

The `keyBy` method keys the collection by the given key. If multiple items have the same key, only the last one will appear in the new collection:

`keyBy` 메소드는 주어진 키를 기준으로 컬렉션을 다시 구성합니다. 여러 아이템이 같은 키를 가지고 있다면, 새로운 컬렉션에서는 마지막 항목만 나타납니다:

    $collection = collect([
        ['product_id' => 'prod-100', 'name' => 'desk'],
        ['product_id' => 'prod-200', 'name' => 'chair'],
    ]);

    $keyed = $collection->keyBy('product_id');

    $keyed->all();

    /*
        [
            'prod-100' => ['product_id' => 'prod-100', 'name' => 'Desk'],
            'prod-200' => ['product_id' => 'prod-200', 'name' => 'Chair'],
        ]
    */

You may also pass a callback to the method. The callback should return the value to key the collection by:

메소드에 콜백은 전달할 수도 있습니다. 콜백은 컬렉션의 키 값을 반환해야합니다:

    $keyed = $collection->keyBy(function ($item) {
        return strtoupper($item['product_id']);
    });

    $keyed->all();

    /*
        [
            'PROD-100' => ['product_id' => 'prod-100', 'name' => 'Desk'],
            'PROD-200' => ['product_id' => 'prod-200', 'name' => 'Chair'],
        ]
    */

<a name="method-keys"></a>
#### `keys()` {#collection-method}

The `keys` method returns all of the collection's keys:

`keys` 메소드는 컬렉션의 모든 키를 반환합니다:

    $collection = collect([
        'prod-100' => ['product_id' => 'prod-100', 'name' => 'Desk'],
        'prod-200' => ['product_id' => 'prod-200', 'name' => 'Chair'],
    ]);

    $keys = $collection->keys();

    $keys->all();

    // ['prod-100', 'prod-200']

<a name="method-last"></a>
#### `last()` {#collection-method}

The `last` method returns the last element in the collection that passes a given truth test:

`last` 메소드는 주어진 콜백에서 참이되는 값중에 가장 마지막 값을 반환합니다:

    collect([1, 2, 3, 4])->last(function ($key, $value) {
        return $value < 3;
    });

    // 2

You may also call the `last` method with no arguments to get the last element in the collection. If the collection is empty, `null` is returned:

`last` 메소드를 인자 없이 호출하여 컬렉션의 가장 마지막 값을 획득할 수도 있습니다. 컬렉션이 비어 있다면, `null` 이 반환됩니다:

    collect([1, 2, 3, 4])->last();

    // 4

<a name="method-map"></a>
#### `map()` {#collection-method}

The `map` method iterates through the collection and passes each value to the given callback. The callback is free to modify the item and return it, thus forming a new collection of modified items:

`map` 메소드는 컬렉션 전체를 반복하여 주어진 콜백에 각각의 값을 전달합니다. 콜백은 자유롭게 아이템을 변경하고 반환하여, 변경된 아이템으로 구성된 새로운 컬렉션이 만들어 집니다. 

    $collection = collect([1, 2, 3, 4, 5]);

    $multiplied = $collection->map(function ($item, $key) {
        return $item * 2;
    });

    $multiplied->all();

    // [2, 4, 6, 8, 10]

> **Note:** Like most other collection methods, `map` returns a new collection instance; it does not modify the collection it is called on. If you want to transform the original collection, use the [`transform`](#method-transform) method.

> **참고:** 대다수의 다른 컬렉션 메소드와 같이, `map` 메소드는 새로운 컬렉션 인스턴스를 반환합니다; 이 메소드는 호출된 컬렉션을 변경하지 않습니다. 원래의 컬렉션을 변경하고자 한다면 [`transform`](#method-transform)메소드를 사용하십시오.

<a name="method-max"></a>
#### `max()` {#collection-method}

The `max` method returns the maximum value of a given key:

`max` 메소드는 주어진 키에 해당하는 최대 값을 반환합니다: 

    $max = collect([['foo' => 10], ['foo' => 20]])->max('foo');

    // 20

    $max = collect([1, 2, 3, 4, 5])->max();

    // 5

<a name="method-merge"></a>
#### `merge()` {#collection-method}

The `merge` method merges the given array or collection with the original collection. If a string key in the given items matches a string key in the original collection, the given items's value will overwrite the value in the original collection:

`merge` 메소드는 주어진 배열 또는 컬렉션을 원래의 컬렉션과 합칩니다. 배열 안에 들어 있는 키가 컬렉션에 들어 있는 키와 일치한다면, 주어진 배열의 값이 원래의 컬렉션 안의 값을 덮어 씁니다:

    $collection = collect(['product_id' => 1, 'price' => 100]);

    $merged = $collection->merge(['price' => 200, 'discount' => false]);

    $merged->all();

    // ['product_id' => 1, 'price' => 200, 'discount' => false]

If the given items's keys are numeric, the values will be appended to the end of the collection:

만약 주어진 아이템의 키가 숫자라면, 이 값들은 컬렉션의 가장 마지막에 추가됩니다:

    $collection = collect(['Desk', 'Chair']);

    $merged = $collection->merge(['Bookcase', 'Door']);

    $merged->all();

    // ['Desk', 'Chair', 'Bookcase', 'Door']

<a name="method-min"></a>
#### `min()` {#collection-method}

The `min` method returns the minimum value of a given key:

`min` 메소드는 주어진 키에 대한 최소 값을 반환합니다:

    $min = collect([['foo' => 10], ['foo' => 20]])->min('foo');

    // 10

    $min = collect([1, 2, 3, 4, 5])->min();

    // 1

<a name="method-only"></a>
#### `only()` {#collection-method}

The `only` method returns the items in the collection with the specified keys:

`only` 메소드는 컬렉션 안에서 지정된 키에 대한 아이템만을 반환합니다. 

    $collection = collect(['product_id' => 1, 'name' => 'Desk', 'price' => 100, 'discount' => false]);

    $filtered = $collection->only(['product_id', 'name']);

    $filtered->all();

    // ['product_id' => 1, 'name' => 'Desk']

For the inverse of `only`, see the [except](#method-except) method.

`only` 메소드의 반대는, [except](#method-except)메소드를 확인하십시오.

<a name="method-pluck"></a>
#### `pluck()` {#collection-method}

The `pluck` method retrieves all of the values for a given key:

`pluck` 메소드는 주어진 키에 대한 모든 값을 반환합니다:

    $collection = collect([
        ['product_id' => 'prod-100', 'name' => 'Desk'],
        ['product_id' => 'prod-200', 'name' => 'Chair'],
    ]);

    $plucked = $collection->pluck('name');

    $plucked->all();

    // ['Desk', 'Chair']

You may also specify how you wish the resulting collection to be keyed:

또한 컬렉션에서 키로 지정하고자 하는 값을 지정할 수도 있습니다:

    $plucked = $collection->pluck('name', 'product_id');

    $plucked->all();

    // ['prod-100' => 'Desk', 'prod-200' => 'Chair']

<a name="method-pop"></a>
#### `pop()` {#collection-method}

The `pop` method removes and returns the last item from the collection:

`pop` 메소드는 컬렉션의 마지막 값을 반환하고 제거합니다:

    $collection = collect([1, 2, 3, 4, 5]);

    $collection->pop();

    // 5

    $collection->all();

    // [1, 2, 3, 4]

<a name="method-prepend"></a>
#### `prepend()` {#collection-method}

The `prepend` method adds an item to the beginning of the collection:

`prepend` 메소드는 컬렉션의 앞 부분에 아이템을 추가합니다:

    $collection = collect([1, 2, 3, 4, 5]);

    $collection->prepend(0);

    $collection->all();

    // [0, 1, 2, 3, 4, 5]

You may also pass a second argument to set the key of the prepended item:

또한 두번째 인자로 앞에 붙이고자 하는 아이템의 키를 설정할 수도 있습니다:

    $collection = collect(['one' => 1, 'two' => 2]);

    $collection->prepend(0, 'zero');

    $collection->all();

    // ['zero' => 0, 'one' => 1, 'two' => 2]

<a name="method-pull"></a>
#### `pull()` {#collection-method}

The `pull` method removes and returns an item from the collection by its key:

`pull` 메소드는 주어진 키에 해당하는 아이템을 반환하고 삭제합니다:

    $collection = collect(['product_id' => 'prod-100', 'name' => 'Desk']);

    $collection->pull('name');

    // 'Desk'

    $collection->all();

    // ['product_id' => 'prod-100']

<a name="method-push"></a>
#### `push()` {#collection-method}

The `push` method appends an item to the end of the collection:

`push` 메소드는 컬렉션의 마지막에 아이템을 추가합니다:

    $collection = collect([1, 2, 3, 4]);

    $collection->push(5);

    $collection->all();

    // [1, 2, 3, 4, 5]

<a name="method-put"></a>
#### `put()` {#collection-method}

The `put` method sets the given key and value in the collection:

`put` 메소드는 주어진 키와 값을 컬렉션에 추가합니다: 

    $collection = collect(['product_id' => 1, 'name' => 'Desk']);

    $collection->put('price', 100);

    $collection->all();

    // ['product_id' => 1, 'name' => 'Desk', 'price' => 100]

<a name="method-random"></a>
#### `random()` {#collection-method}

The `random` method returns a random item from the collection:

`random` 메소드는 컬렉션의 아이템중 하나를 랜덤하게 반환합니다:

    $collection = collect([1, 2, 3, 4, 5]);

    $collection->random();

    // 4 - (retrieved randomly)

You may optionally pass an integer to `random` to specify how many items you would like to randomly retrieve. If that integer is more than `1`, a collection of items is returned:

얼마나 많은 아이템을 랜덤으로 조회할지 `random` 메소드에 선택적으로 정수값을 전달할 수 있습니다. 정수 값이 `1` 보다 크다면 컬렉션의 아이템 들이 반환 됩니다:

    $random = $collection->random(3);

    $random->all();

    // [2, 4, 5] - (retrieved randomly)

<a name="method-reduce"></a>
#### `reduce()` {#collection-method}

The `reduce` method reduces the collection to a single value, passing the result of each iteration into the subsequent iteration:

`reduce` 메소드는 각각의 인자를 반복하여 다음 반복에 결과를 전달하면서 컬렉션을 하나의 값으로 줄입니다. 

    $collection = collect([1, 2, 3]);

    $total = $collection->reduce(function ($carry, $item) {
        return $carry + $item;
    });

    // 6

The value for `$carry` on the first iteration is `null`; however, you may specify its initial value by passing a second argument to `reduce`:

첫번째 반복에서 `$carry` 의 값은 null 입니다; 그러나 초기 값을 지정하고자 하는 경우에 `reduce` 의 두번째 인자로 전달할 수 있습니다. 

    $collection->reduce(function ($carry, $item) {
        return $carry + $item;
    }, 4);

    // 10

<a name="method-reject"></a>
#### `reject()` {#collection-method}

The `reject` method filters the collection using the given callback. The callback should return `true` if the item should be removed from the resulting collection:

`reject` 메소드는 컬렉션에서 지정된 콜백을 사용하여 필터링을 합니다. 콜백은 결과 컬렉션에서 제거되어야 하는 아이템의 경우 `true`를 반환해야합니다:

    $collection = collect([1, 2, 3, 4]);

    $filtered = $collection->reject(function ($value) {
        return $value > 2;
    });

    $filtered->all();

    // [1, 2]

For the inverse of the `reject` method, see the [`filter`](#method-filter) method.

<a name="method-reverse"></a>
#### `reverse()` {#collection-method}

The `reverse` method reverses the order of the collection's items:

`reverse` 메소드는 컬렉션의 아이템 순서를 반대로 합니다:

    $collection = collect([1, 2, 3, 4, 5]);

    $reversed = $collection->reverse();

    $reversed->all();

    // [5, 4, 3, 2, 1]

<a name="method-search"></a>
#### `search()` {#collection-method}

The `search` method searches the collection for the given value and returns its key if found. If the item is not found, `false` is returned.

`search` 메소드는 주어진 값이 컬렉션에 있는지 찾아서 해당 값의 키를 반환합니다. 아이템을 찾지 못한 경우, `false` 가 반환됩니다.
    $collection = collect([2, 4, 6, 8]);

    $collection->search(4);

    // 1

The search is done using a "loose" comparison, meaning a string with an integer value will be considered equal to an integer of the same value. To use "strict" comparison, pass `true` as the second argument to the method:

검색은 "느슨한" 비교로 (타입을 엄격하게 비교하지 않습니다) 다시말해 문자열과 정수값의 경우 동일한 값이라면 같다고 판단합니다. 타입에 일치하는 "엄격한" 비교를 수행하려면 `true`를 메소드의 두 번째 인자로 전달하면 됩니다:

    $collection->search('4', true);

    // false

Alternatively, you may pass in your own callback to search for the first item that passes your truth test:

다른 방법으로, 검색 콜백을 전달하여 콜백을 통과 하는 첫 번째 아이템을 획득할 수도 있습니다.

    $collection->search(function ($item, $key) {
        return $item > 5;
    });

    // 2

<a name="method-shift"></a>
#### `shift()` {#collection-method}

The `shift` method removes and returns the first item from the collection:

`shift` 메소드는 컬렉션에서 첫 번째 아이템을 제거하고 해당 값을 반환합니다.

    $collection = collect([1, 2, 3, 4, 5]);

    $collection->shift();

    // 1

    $collection->all();

    // [2, 3, 4, 5]

<a name="method-shuffle"></a>
#### `shuffle()` {#collection-method}

The `shuffle` method randomly shuffles the items in the collection:

`shuffle` 메소드는 컬렉션의 아이템을 랜덤하게 섞어버립니다.

    $collection = collect([1, 2, 3, 4, 5]);

    $shuffled = $collection->shuffle();

    $shuffled->all();

    // [3, 2, 5, 1, 4] - (generated randomly)

<a name="method-slice"></a>
#### `slice()` {#collection-method}

The `slice` method returns a slice of the collection starting at the given index:

`slice` 메소드는 주어진 인덱스에서 시작하는 컬렉션으로 잘라냅니다.

    $collection = collect([1, 2, 3, 4, 5, 6, 7, 8, 9, 10]);

    $slice = $collection->slice(4);

    $slice->all();

    // [5, 6, 7, 8, 9, 10]

If you would like to limit the size of the returned slice, pass the desired size as the second argument to the method:

만약 반환되는 컬렉션의 사이즈를 제한하고자 한다면, 메소드의 두번재 인자로 사이즈를 전달하면 됩니다:

    $slice = $collection->slice(4, 2);

    $slice->all();

    // [5, 6]

The returned slice will have new, numerically indexed keys. If you wish to preserve the original keys, pass `true` as the third argument to the method.

반환되는 컬렉션은 새로운 인덱스 키로 구성되어 있습니다. 만약 이전의 원래 키를 유지하려면, 메소드의 세번째 인자로 `true` 를 전달하십시오.

<a name="method-sort"></a>
#### `sort()` {#collection-method}

The `sort` method sorts the collection. The sorted collection keeps the original array keys, so in this example we'll use the [`values`](#method-values) method to reset the keys to consecutively numbered indexes:

`sort` 메소드는 컬렉션을 정렬합니다. 정렬된 컬렉션은 원래의 배열 키를 유지 하기 때문에, 다음 예제에서 연속된 숫자 인덱스를 리셋하기 위해서 [`values`](#method-values) 메소드를 사용할 것입니다:

    $collection = collect([5, 3, 1, 2, 4]);

    $sorted = $collection->sort();

    $sorted->values()->all();

    // [1, 2, 3, 4, 5]

If your sorting needs are more advanced, you may pass a callback to `sort` with your own algorithm. Refer to the PHP documentation on [`usort`](https://secure.php.net/manual/en/function.usort.php#refsect1-function.usort-parameters), which is what the collection's `sort` method calls under the hood.

보다 복잡한 정렬이 필요하다면, `sort` 메소드에 여러분의 고유한 알고리즘을 위한 콜백을 전달할 수 있습니다. 컬렉션의 `sort` 메소드가 호출될 때의 동작은 [`usort`](https://secure.php.net/manual/en/function.usort.php#refsect1-function.usort-parameters) PHP 문서를 참고하십시오.

If you need to sort a collection of nested arrays or objects, see the [`sortBy`](#method-sortby) and [`sortByDesc`](#method-sortbydesc) methods.

만약 중첩된 배열이나 객체의 컬렉션을 정렬할 필요가 있다면, [`sortBy`](#method-sortby) 또는 [`sortByDesc`](#method-sortbydesc) 메소드를 확인하십시오.

<a name="method-sortby"></a>
#### `sortBy()` {#collection-method}

The `sortBy` method sorts the collection by the given key. The sorted collection keeps the original array keys, so in this example we'll use the [`values`](#method-values) method to reset the keys to consecutively numbered indexes:

`sortBy` 메소드는 주어진 키에 의한 정렬을 수행합니다. 정렬된 컬렉션은 원래의 배열 키를 유지 하기 때문에, 다음 예제에서 연속된 숫자 인덱스를 리셋하기 위해서 [`values`](#method-values) 메소드를 사용할 것입니다:

    $collection = collect([
        ['name' => 'Desk', 'price' => 200],
        ['name' => 'Chair', 'price' => 100],
        ['name' => 'Bookcase', 'price' => 150],
    ]);

    $sorted = $collection->sortBy('price');

    $sorted->values()->all();

    /*
        [
            ['name' => 'Chair', 'price' => 100],
            ['name' => 'Bookcase', 'price' => 150],
            ['name' => 'Desk', 'price' => 200],
        ]
    */

You can also pass your own callback to determine how to sort the collection values:

컬렉션의 값을 어떻게 정렬하는지를 결정하기 위해 콜백을 전달할 수도 있습니다.

    $collection = collect([
        ['name' => 'Desk', 'colors' => ['Black', 'Mahogany']],
        ['name' => 'Chair', 'colors' => ['Black']],
        ['name' => 'Bookcase', 'colors' => ['Red', 'Beige', 'Brown']],
    ]);

    $sorted = $collection->sortBy(function ($product, $key) {
        return count($product['colors']);
    });

    $sorted->values()->all();

    /*
        [
            ['name' => 'Chair', 'colors' => ['Black']],
            ['name' => 'Desk', 'colors' => ['Black', 'Mahogany']],
            ['name' => 'Bookcase', 'colors' => ['Red', 'Beige', 'Brown']],
        ]
    */

<a name="method-sortbydesc"></a>
#### `sortByDesc()` {#collection-method}

This method has the same signature as the [`sortBy`](#method-sortby) method, but will sort the collection in the opposite order.

이 메소드의 사용법은 [`sortBy`](#method-sortby) 메소드와 동일하지만, 반대의 순서로 컬렉션을 정렬합니다.

<a name="method-splice"></a>
#### `splice()` {#collection-method}

The `splice` method removes and returns a slice of items starting at the specified index:

`splice` 메소드는 지정된 인덱스에서 시작하여 아이템을 잘라내고 제거하여 반환합니다.

    $collection = collect([1, 2, 3, 4, 5]);

    $chunk = $collection->splice(2);

    $chunk->all();

    // [3, 4, 5]

    $collection->all();

    // [1, 2]

You may pass a second argument to limit the size of the resulting chunk:

반환되는 결과의 사이즈를 제한하기 위해서 두번째 인자로 전달할 수 있습니다:

    $collection = collect([1, 2, 3, 4, 5]);

    $chunk = $collection->splice(2, 1);

    $chunk->all();

    // [3]

    $collection->all();

    // [1, 2, 4, 5]

In addition, you can pass a third argument containing the new items to replace the items removed from the collection:

추가적으로, 컬렉션에서 아이템을 제거하고, 대체할 새로운 아이템을 세번째 인자로 전달할 수 있습니다:

    $collection = collect([1, 2, 3, 4, 5]);

    $chunk = $collection->splice(2, 1, [10, 11]);

    $chunk->all();

    // [3]

    $collection->all();

    // [1, 2, 10, 11, 4, 5]

<a name="method-sum"></a>
#### `sum()` {#collection-method}

The `sum` method returns the sum of all items in the collection:

`sum` 메소드는 컬렉션 안에 있는 모든 아이템들의 합을 반환합니다:

    collect([1, 2, 3, 4, 5])->sum();

    // 15

If the collection contains nested arrays or objects, you should pass a key to use for determining which values to sum:

컬렉션이 중첩된 배열이나 객체를 가지고 있다면, 어떤 값을 더해야할지 결정하는 키를 전달해야합니다:

    $collection = collect([
        ['name' => 'JavaScript: The Good Parts', 'pages' => 176],
        ['name' => 'JavaScript: The Definitive Guide', 'pages' => 1096],
    ]);

    $collection->sum('pages');

    // 1272

In addition, you may pass your own callback to determine which values of the collection to sum:

추가적으로, 컬렉션의 어떤 값을 더해야 할지 결정하는 콜백을 전달할 수도 있습니다:

    $collection = collect([
        ['name' => 'Chair', 'colors' => ['Black']],
        ['name' => 'Desk', 'colors' => ['Black', 'Mahogany']],
        ['name' => 'Bookcase', 'colors' => ['Red', 'Beige', 'Brown']],
    ]);

    $collection->sum(function ($product) {
        return count($product['colors']);
    });

    // 6

<a name="method-take"></a>
#### `take()` {#collection-method}

The `take` method returns a new collection with the specified number of items:

`take` 메소드는 지정된 숫자의 아이템으로 이루어진 새로운 컬렉션을 반환합니다:

    $collection = collect([0, 1, 2, 3, 4, 5]);

    $chunk = $collection->take(3);

    $chunk->all();

    // [0, 1, 2]

You may also pass a negative integer to take the specified amount of items from the end of the collection:

컬렉션의 끝에서 부터 가져올 아이템의 갯수를 지정하기 위해서 음수를 전달할수도 있습니다. 

    $collection = collect([0, 1, 2, 3, 4, 5]);

    $chunk = $collection->take(-2);

    $chunk->all();

    // [4, 5]

<a name="method-toarray"></a>
#### `toArray()` {#collection-method}

The `toArray` method converts the collection into a plain PHP `array`. If the collection's values are [Eloquent](/docs/{{version}}/eloquent) models, the models will also be converted to arrays:

`toArray` 메소드는 컬렉션을 PHP `배열`로 변환합니다. 컬렉션의 값이 [Eloquent](/docs/{{version}}/eloquent) 이라면, 이 모델또한 배열로 변환될 것입니다:

    $collection = collect(['name' => 'Desk', 'price' => 200]);

    $collection->toArray();

    /*
        [
            ['name' => 'Desk', 'price' => 200],
        ]
    */

> **Note:** `toArray` also converts all of the collection's nested objects to an array. If you want to get the raw underlying array, use the [`all`](#method-all) method instead.

> **주의:** `toArray` 는 또한 모든 컬렉션의 중첩된 객체도 배열로 변환할 것입니다. 근본적인 raw 배열을 얻기를 원한다면 [`all`](#method-all) 메소드를 대신 사용하십시오.

<a name="method-tojson"></a>
#### `toJson()` {#collection-method}

The `toJson` method converts the collection into a JSON serialized string:

`toJson` 메소드는 모든 컬렉션을 JSON serialized 문자열으로 변환합니다:

    $collection = collect(['name' => 'Desk', 'price' => 200]);

    $collection->toJson();

    // '{"name":"Desk", "price":200}'

<a name="method-transform"></a>
#### `transform()` {#collection-method}

The `transform` method iterates over the collection and calls the given callback with each item in the collection. The items in the collection will be replaced by the values returned by the callback:

`transform` 메소드는 컬렉션을 반복하여 컬렉션의 각각의 아이템에 대해서 주어진 콜백을 호출합니다. 컬렉션 안에 있는 아이템들은 자동으로 콜백의 결과로 대체될 것입니다. 

    $collection = collect([1, 2, 3, 4, 5]);

    $collection->transform(function ($item, $key) {
        return $item * 2;
    });

    $collection->all();

    // [2, 4, 6, 8, 10]

> **Note:** Unlike most other collection methods, `transform` modifies the collection itself. If you wish to create a new collection instead, use the [`map`](#method-map) method.

> **주의:** 다른 컬렉션 메소드와 다르게, `transform` 메소드는 컬렉션 자신을 변경합니다. 대신에, 새로운 컬렉션을 생성하려면 [`map`](#method-map) 메소드를 사용하십시오.

<a name="method-unique"></a>
#### `unique()` {#collection-method}

The `unique` method returns all of the unique items in the collection. The returned collection keeps the original array keys, so in this example we'll use the [`values`](#method-values) method to reset the keys to consecutively numbered indexes:

`unique` 메소드는 컬렉션 안에서 유니크한 모든 아이템들을 반환합니다. 반환된 컬렉션은 원래의 배열 키를 유지 하기 때문에, 다음 예제에서 연속된 숫자 인덱스를 리셋하기 위해서 [`values`](#method-values) 메소드를 사용할 것입니다:

    $collection = collect([1, 1, 2, 2, 3, 4, 2]);

    $unique = $collection->unique();

    $unique->values()->all();

    // [1, 2, 3, 4]

When dealing with nested arrays or objects, you may specify the key used to determine uniqueness:

중첩된 배열이나 객체를 다룰 때에는, 유니크 여부를 결정할 키를 지정할 수 있습니다:

    $collection = collect([
        ['name' => 'iPhone 6', 'brand' => 'Apple', 'type' => 'phone'],
        ['name' => 'iPhone 5', 'brand' => 'Apple', 'type' => 'phone'],
        ['name' => 'Apple Watch', 'brand' => 'Apple', 'type' => 'watch'],
        ['name' => 'Galaxy S6', 'brand' => 'Samsung', 'type' => 'phone'],
        ['name' => 'Galaxy Gear', 'brand' => 'Samsung', 'type' => 'watch'],
    ]);

    $unique = $collection->unique('brand');

    $unique->values()->all();

    /*
        [
            ['name' => 'iPhone 6', 'brand' => 'Apple', 'type' => 'phone'],
            ['name' => 'Galaxy S6', 'brand' => 'Samsung', 'type' => 'phone'],
        ]
    */

You may also pass your own callback to determine item uniqueness:

또한 아이템이 유니크한지 결정하기 위한 콜백을 전달할 수도 있습니다:

    $unique = $collection->unique(function ($item) {
        return $item['brand'].$item['type'];
    });

    $unique->values()->all();

    /*
        [
            ['name' => 'iPhone 6', 'brand' => 'Apple', 'type' => 'phone'],
            ['name' => 'Apple Watch', 'brand' => 'Apple', 'type' => 'watch'],
            ['name' => 'Galaxy S6', 'brand' => 'Samsung', 'type' => 'phone'],
            ['name' => 'Galaxy Gear', 'brand' => 'Samsung', 'type' => 'watch'],
        ]
    */

The `unique` method uses "loose" comparisons when checking item values, meaning a string with an integer value will be considered equal to an integer of the same value.

`unique` 메소드는 아이템의 값을 비교할 때 "느슨한" 비교를 수행하기 때문에, 정수값이 문자형일 때에도 정수형 값과 동일하다고 판단합니다.

<a name="method-values"></a>
#### `values()` {#collection-method}

The `values` method returns a new collection with the keys reset to consecutive integers:

`values` 메소드는 키를 리셋하고 연속적인 정수를 키로 하는 새로운 컬렉션을 반환합니다. 

    $collection = collect([
        10 => ['product' => 'Desk', 'price' => 200],
        11 => ['product' => 'Desk', 'price' => 200]
    ]);

    $values = $collection->values();

    $values->all();

    /*
        [
            0 => ['product' => 'Desk', 'price' => 200],
            1 => ['product' => 'Desk', 'price' => 200],
        ]
    */

<a name="method-where"></a>
#### `where()` {#collection-method}

The `where` method filters the collection by a given key / value pair:

`where` 메소드는 주어진 키 / 값 쌍에 대해서 컬렉션을 필터링합니다:

    $collection = collect([
        ['product' => 'Desk', 'price' => 200],
        ['product' => 'Chair', 'price' => 100],
        ['product' => 'Bookcase', 'price' => 150],
        ['product' => 'Door', 'price' => 100],
    ]);

    $filtered = $collection->where('price', 100);

    $filtered->all();

    /*
        [
            ['product' => 'Chair', 'price' => 100],
            ['product' => 'Door', 'price' => 100],
        ]
    */

The `where` method uses "strict" comparisons when checking item values, meaning a string with an integer value will not be considered equal to an integer of the same value. Use the [`whereLoose`](#method-whereloose) method to filter using "loose" comparisons.

where 메소드는 아이템의 값을 확인할 때 타입을 "엄격하게" 비교를 수행하기 때문에, 문자형으로 된 정수값과 정수형은과 동일하다고 판단하지 않습니다. "느슨한" 비교를 사용하여 필터링을 하려면 [`whereLoose`](#method-whereloose) 메소드를 사용하십시오.

<a name="method-whereloose"></a>
#### `whereLoose()` {#collection-method}

This method has the same signature as the [`where`](#method-where) method; however, all values are compared using "loose" comparisons.

이 메소드는 [`where`](#method-where) 메소드와 동일한 사용법을 가지고 있습니다;하지만 모든 값이 "느슨하게" 비교되어 집니다.

<a name="method-zip"></a>
#### `zip()` {#collection-method}

The `zip` method merges together the values of the given array with the values of the original collection at the corresponding index:

`zip` 메소드는 해당 인덱스의 원래 컬렉션의 값으로 주어진 배열의 값을 함께 합칩니다:

    $collection = collect(['Chair', 'Desk']);

    $zipped = $collection->zip([100, 200]);

    $zipped->all();

    // [['Chair', 100], ['Desk', 200]]
