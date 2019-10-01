# Collections
# 컬렉션

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [Creating Collections](#creating-collections)
    - [컬렉션 생성하기](#creating-collections)
    - [Extending Collections](#extending-collections)
    - [컬렉션 확장하기](#extending-collections)
- [Available Methods](#available-methods)
- [사용 가능한 메소드](#available-methods)
- [Higher Order Messages](#higher-order-messages)
- [Higher Order Messages](#higher-order-messages)
- [Lazy Collections](#lazy-collections)
- [지연 컬렉션-Lazy Collections](#lazy-collections)
    - [Introduction](#lazy-collection-introduction)
    - [시작하기](#lazy-collection-introduction)
    - [The Enumerable Contract](#the-enumerable-contract)
    - [열거형 Contract](#the-enumerable-contract)
    - [Lazy Collection Methods](#lazy-collection-methods)
    - [지연 컬렉션-Lazy Collection 메소드](#lazy-collection-methods)

<a name="introduction"></a>
## Introduction
## 시작하기

The `Illuminate\Support\Collection` class provides a fluent, convenient wrapper for working with arrays of data. For example, check out the following code. We'll use the `collect` helper to create a new collection instance from the array, run the `strtoupper` function on each element, and then remove all empty elements:

`Illuminate\Support\Collection` 클래스는 배열 데이터를 사용하기위한 유연하고 편리한 래퍼(wrapper)를 제공합니다. 다음의 사용예제를 확인하십시오. `collect` 헬퍼 함수를 사용하여 배열에서 새로운 collection 인스턴스를 생성하고 모든 요소들을 대문자로 변경한 다음 비어있는 요소들을 제거하는 예제 입니다.

    $collection = collect(['taylor', 'abigail', null])->map(function ($name) {
        return strtoupper($name);
    })
    ->reject(function ($name) {
        return empty($name);
    });

As you can see, the `Collection` class allows you to chain its methods to perform fluent mapping and reducing of the underlying array. In general, collections are immutable, meaning every `Collection` method returns an entirely new `Collection` instance.

보시는 바와 같이 `Collection` 클래스는 편리한 맵핑과 배열의 감소를 수행하기 위한 체이닝 방식을 제공합니다. 일반적으로, 컬렉션은 immutable-불편하고 모든 `Collection`의 메소드는 `Collection`의 인스턴스를 반환합니다.

<a name="creating-collections"></a>
### Creating Collections
### 컬렉션 생성하기

As mentioned above, the `collect` helper returns a new `Illuminate\Support\Collection` instance for the given array. So, creating a collection is as simple as:

앞서 설명한 대로 `collect` 헬퍼 함수는 주어진 배열으로 부터 `Illuminate\Support\Collection` 인스턴스를 반환합니다. 따라서 컬렉션을 생성하는 것은 보시는 바와 같이 아주 간단합니다.

    $collection = collect([1, 2, 3]);

> {tip} The results of [Eloquent](/docs/{{version}}/eloquent) queries are always returned as `Collection` instances.

> {tip} [Eloquent](/docs/{{version}}/eloquent) 쿼리의 결과는 항상 `Collection` 인스턴스를 반환합니다.

<a name="extending-collections"></a>
### Extending Collections
### 컬렉션 확장하기

Collections are "macroable", which allows you to add additional methods to the `Collection` class at run time. For example, the following code adds a `toUpper` method to the `Collection` class:

컬렉션은 "macroable" 하기 때문에, 런타임에 `Collection` 클래스에 메소드를 추가하라 수 있습니다. 예를 들어 다음의 코드는 `Collection` 클래스에 `toUpper` 메소드를 추가합니다.

    use Illuminate\Support\Str;

    Collection::macro('toUpper', function () {
        return $this->map(function ($value) {
            return Str::upper($value);
        });
    });

    $collection = collect(['first', 'second']);

    $upper = $collection->toUpper();

    // ['FIRST', 'SECOND']

Typically, you should declare collection macros in a [service provider](/docs/{{version}}/providers).

일반적으로, [서비스 프로바이더](/docs/{{version}}/providers)에서 컬렉션에 메소드를 추가하게 됩니다.

<a name="available-methods"></a>
## Available Methods
## 사용가능한 메소드

For the remainder of this documentation, we'll discuss each method available on the `Collection` class. Remember, all of these methods may be chained to fluently manipulate the underlying array. Furthermore, almost every method returns a new `Collection` instance, allowing you to preserve the original copy of the collection when necessary:

이 문서의 나머지 부분에서는 `Collection` 클래스에서 사용할 수 있는 각각의 메소드를 설명합니다. 모든 메소드들은 보다 유연하게 배열을 처리할 수 있도록 체이닝 할 수 있다는 것을 기억하십시오. 또한, 대부분의 모든 메소드는 필요한 경우 원래의 컬렉션을 사용할 수 있도록, 새로운 `Collection` 인스턴스를 반환합니다.

<style>
    #collection-method-list > p {
        column-count: 3; -moz-column-count: 3; -webkit-column-count: 3;
        column-gap: 2em; -moz-column-gap: 2em; -webkit-column-gap: 2em;
    }

    #collection-method-list a {
        display: block;
    }
</style>

<div id="collection-method-list" markdown="1">

- [all](#method-all)
- [average](#method-average)
- [avg](#method-avg)
- [chunk](#method-chunk)
- [collapse](#method-collapse)
- [collect](#method-collect)
- [combine](#method-combine)
- [concat](#method-concat)
- [contains](#method-contains)
- [containsStrict](#method-containsstrict)
- [count](#method-count)
- [countBy](#method-countBy)
- [crossJoin](#method-crossjoin)
- [dd](#method-dd)
- [diff](#method-diff)
- [diffAssoc](#method-diffassoc)
- [diffKeys](#method-diffkeys)
- [dump](#method-dump)
- [duplicates](#method-duplicates)
- [duplicatesStrict](#method-duplicatesstrict)
- [each](#method-each)
- [eachSpread](#method-eachspread)
- [every](#method-every)
- [except](#method-except)
- [filter](#method-filter)
- [first](#method-first)
- [firstWhere](#method-first-where)
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
- [intersectByKeys](#method-intersectbykeys)
- [isEmpty](#method-isempty)
- [isNotEmpty](#method-isnotempty)
- [keyBy](#method-keyby)
- [keys](#method-keys)
- [last](#method-last)
- [macro](#method-macro)
- [make](#method-make)
- [map](#method-map)
- [mapInto](#method-mapinto)
- [mapSpread](#method-mapspread)
- [mapToGroups](#method-maptogroups)
- [mapWithKeys](#method-mapwithkeys)
- [max](#method-max)
- [median](#method-median)
- [merge](#method-merge)
- [mergeRecursive](#method-mergerecursive)
- [min](#method-min)
- [mode](#method-mode)
- [nth](#method-nth)
- [only](#method-only)
- [pad](#method-pad)
- [partition](#method-partition)
- [pipe](#method-pipe)
- [pluck](#method-pluck)
- [pop](#method-pop)
- [prepend](#method-prepend)
- [pull](#method-pull)
- [push](#method-push)
- [put](#method-put)
- [random](#method-random)
- [reduce](#method-reduce)
- [reject](#method-reject)
- [replace](#method-replace)
- [replaceRecursive](#method-replacerecursive)
- [reverse](#method-reverse)
- [search](#method-search)
- [shift](#method-shift)
- [shuffle](#method-shuffle)
- [skip](#method-skip)
- [slice](#method-slice)
- [some](#method-some)
- [sort](#method-sort)
- [sortBy](#method-sortby)
- [sortByDesc](#method-sortbydesc)
- [sortKeys](#method-sortkeys)
- [sortKeysDesc](#method-sortkeysdesc)
- [splice](#method-splice)
- [split](#method-split)
- [sum](#method-sum)
- [take](#method-take)
- [tap](#method-tap)
- [times](#method-times)
- [toArray](#method-toarray)
- [toJson](#method-tojson)
- [transform](#method-transform)
- [union](#method-union)
- [unique](#method-unique)
- [uniqueStrict](#method-uniquestrict)
- [unless](#method-unless)
- [unlessEmpty](#method-unlessempty)
- [unlessNotEmpty](#method-unlessnotempty)
- [unwrap](#method-unwrap)
- [values](#method-values)
- [when](#method-when)
- [whenEmpty](#method-whenempty)
- [whenNotEmpty](#method-whennotempty)
- [where](#method-where)
- [whereStrict](#method-wherestrict)
- [whereBetween](#method-wherebetween)
- [whereIn](#method-wherein)
- [whereInStrict](#method-whereinstrict)
- [whereInstanceOf](#method-whereinstanceof)
- [whereNotBetween](#method-wherenotbetween)
- [whereNotIn](#method-wherenotin)
- [whereNotInStrict](#method-wherenotinstrict)
- [wrap](#method-wrap)
- [zip](#method-zip)

</div>

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

<a name="method-all"></a>
#### `all()` {#collection-method .first-collection-method}
#### `all()` {#collection-method .first-collection-method}

The `all` method returns the underlying array represented by the collection:

`all` 메소드는 주어진 배열을 컬렉션으로 되돌려 줍니다.

    collect([1, 2, 3])->all();

    // [1, 2, 3]

<a name="method-average"></a>
#### `average()` {#collection-method}
#### `average()` {#collection-method}

Alias for the [`avg`](#method-avg) method.

[`avg`](#method-avg) 메소드의 별칭입니다.

<a name="method-avg"></a>
#### `avg()` {#collection-method}
#### `avg()` {#collection-method}

The `avg` method returns the [average value](https://en.wikipedia.org/wiki/Average) of a given key:

`avg` 메소드는 주어진 키의 [평균값](https://en.wikipedia.org/wiki/Average)을 반환합니다.

    $average = collect([['foo' => 10], ['foo' => 10], ['foo' => 20], ['foo' => 40]])->avg('foo');

    // 20

    $average = collect([1, 1, 2, 4])->avg();

    // 2

<a name="method-chunk"></a>
#### `chunk()` {#collection-method}
#### `chunk()` {#collection-method}

The `chunk` method breaks the collection into multiple, smaller collections of a given size:

`chunk` 메소드는 컬렉션을 주어진 사이즈의 더 작은 여러 개로 분할합니다.

    $collection = collect([1, 2, 3, 4, 5, 6, 7]);

    $chunks = $collection->chunk(4);

    $chunks->toArray();

    // [[1, 2, 3, 4], [5, 6, 7]]

This method is especially useful in [views](/docs/{{version}}/views) when working with a grid system such as [Bootstrap](https://getbootstrap.com/docs/4.1/layout/grid/). Imagine you have a collection of [Eloquent](/docs/{{version}}/eloquent) models you want to display in a grid:

이 메소드는 특히 [뷰](/docs/{{version}}/views) 안에서 [부트스트랩 프레임워크](https://getbootstrap.com/docs/4.1/layout/grid/)와 같은 그리드(grid) 시스템을 작업할 때 유용합니다.
[Eloquent](/docs/{{version}}/eloquent) 모델 컬렉션을 그리드에 표시한다고 생각해 보십시오:

    @foreach ($products->chunk(3) as $chunk)
        <div class="row">
            @foreach ($chunk as $product)
                <div class="col-xs-4">{{ $product->name }}</div>
            @endforeach
        </div>
    @endforeach

<a name="method-collapse"></a>
#### `collapse()` {#collection-method}
#### `collapse()` {#collection-method}

The `collapse` method collapses a collection of arrays into a single, flat collection:

`collapse` 메소드는 배열의 컬렉션을 하나의 평면적인 컬렉션으로 만듭니다.

    $collection = collect([[1, 2, 3], [4, 5, 6], [7, 8, 9]]);

    $collapsed = $collection->collapse();

    $collapsed->all();

    // [1, 2, 3, 4, 5, 6, 7, 8, 9]

<a name="method-combine"></a>
#### `combine()` {#collection-method}
#### `combine()` {#collection-method}

The `combine` method combines the values of the collection, as keys, with the values of another array or collection:

`combine` 메소드는 컬렉션의 값들을 키로 다른 배열 또는 컬렉션의 값을 결합합니다.

    $collection = collect(['name', 'age']);

    $combined = $collection->combine(['George', 29]);

    $combined->all();

    // ['name' => 'George', 'age' => 29]

<a name="method-collect"></a>
#### `collect()` {#collection-method}
#### `collect()` {#collection-method}

The `collect` method returns a new `Collection` instance with the items currently in the collection:

`collect` 메소드는 컬렉션 안에 현재 존재하는 항목들로 이루어진 새 `Collection` 인스턴스를 반환합니다.

    $collectionA = collect([1, 2, 3]);

    $collectionB = $collectionA->collect();

    $collectionB->all();

    // [1, 2, 3]

The `collect` method is primarily useful for converting [lazy collections](#lazy-collections) into standard `Collection` instances:

`collect` 메소드는 [지연 컬렉션-lazy collections](#lazy-collections)을 표준 `Collection` 인스턴스로 변환하는데 유용합니다.

    $lazyCollection = LazyCollection::make(function () {
        yield 1;
        yield 2;
        yield 3;
    });

    $collection = $lazyCollection->collect();

    get_class($collection);

    // 'Illuminate\Support\Collection'

    $collection->all();

    // [1, 2, 3]

> {tip} The `collect` method is especially useful when you have an instance of `Enumerable` and need a non-lazy collection instance. Since `collect()` is part of the `Enumerable` contract, you can safely use it to get a `Collection` instance.

> {tip} `collect` 메소드는 `Enumerable` 인스턴스를 가지고 있고, 비지연(non-lazy) 컬렉션 인스턴스가 필요할 때 특히 유용합니다. `collect()`는 `Enumerable` contract에 속해있기 때문에, `Collection` 인스턴스를 얻는데 안전하게 사용할 수 있습니다. 

<a name="method-concat"></a>
#### `concat()` {#collection-method}
#### `concat()` {#collection-method}

The `concat` method appends the given `array` or collection values onto the end of the collection:

`concat` 메소드는 주어진 `배열` 또는 컬렉션 값을 컬렉션의 마지막에 추가합니다.

    $collection = collect(['John Doe']);

    $concatenated = $collection->concat(['Jane Doe'])->concat(['name' => 'Johnny Doe']);

    $concatenated->all();

    // ['John Doe', 'Jane Doe', 'Johnny Doe']

<a name="method-contains"></a>
#### `contains()` {#collection-method}
#### `contains()` {#collection-method}

The `contains` method determines whether the collection contains a given item:

`contains` 메소드는 컬렉션에 주어진 아이템을 포함하고 있는지 알려줍니다.

    $collection = collect(['name' => 'Desk', 'price' => 100]);

    $collection->contains('Desk');

    // true

    $collection->contains('New York');

    // false

You may also pass a key / value pair to the `contains` method, which will determine if the given pair exists in the collection:

또한 주어진 키/값 쌍이 컬렉션 안에 존재하는지 확인할 수 있도록 `contains` 메소드에 키/값 쌍을 인자로 전달할 수도 있습니다.

    $collection = collect([
        ['product' => 'Desk', 'price' => 200],
        ['product' => 'Chair', 'price' => 100],
    ]);

    $collection->contains('product', 'Bookcase');

    // false

Finally, you may also pass a callback to the `contains` method to perform your own truth test:

마지막으로, `contains` 메소드에 주어진 조건을 판별할 수 있는 콜백을 인자로 전달할 수도 있습니다.

    $collection = collect([1, 2, 3, 4, 5]);

    $collection->contains(function ($value, $key) {
        return $value > 5;
    });

    // false

The `contains` method uses "loose" comparisons when checking item values, meaning a string with an integer value will be considered equal to an integer of the same value. Use the [`containsStrict`](#method-containsstrict) method to filter using "strict" comparisons.

`contains` 메소드는 아이템의 값을 비교할 때 "느슨한" 비교를 수행하기 때문에, 정수값이 문자형일 때에도 정수형 값과 동일하다고 판단합니다. 타입에 대한 "엄격한" 비교를 원한다면 [`containsStrict`](#method-containsstrict) 메소드를 사용하십시오.

<a name="method-containsstrict"></a>
#### `containsStrict()` {#collection-method}
#### `containsStrict()` {#collection-method}

This method has the same signature as the [`contains`](#method-contains) method; however, all values are compared using "strict" comparisons.

이 메소드는 [`contains`](#method-contains) 메소드와 동일하게 사용되지만, "엄격한" 비교를 수행하는 것이 차이점입니다.

<a name="method-count"></a>
#### `count()` {#collection-method}
#### `count()` {#collection-method}

The `count` method returns the total number of items in the collection:

`count` 메소드는 컬렉션안에 존재하는 아이템의 전체 갯수를 반환합니다.

    $collection = collect([1, 2, 3, 4]);

    $collection->count();

    // 4

<a name="method-countBy"></a>
#### `countBy()` {#collection-method}
#### `countBy()` {#collection-method}

The `countBy` method counts the occurrences of values in the collection. By default, the method counts the occurrences of every element:

`countBy` 메소드는 컬렉션 안의 값의 발생 횟수를 셉니다. 기본적으로 모든 요소가 해당됩니다.

    $collection = collect([1, 2, 2, 2, 3]);
    
    $collection->countBy();
    
    // collect([1 => 1, 2 => 3, 3 => 1])

However, you pass a callback to the `countBy` method to count all items by a custom value:

`countBy` 메소드에 특정 값으로 모든 아이템을 계산하는 콜백을 인자로 전달할 수도 있습니다.

    $collection = collect(['alice@gmail.com', 'bob@yahoo.com', 'carlos@gmail.com']);

    $collection->countBy(function ($email) {
        return substr(strrchr($email, "@"), 1);
    });

    // collect(['gmail.com' => 2, 'yahoo.com' => 1])

<a name="method-crossjoin"></a>
#### `crossJoin()` {#collection-method}
#### `crossJoin()` {#collection-method}

The `crossJoin` method cross joins the collection's values among the given arrays or collections, returning a Cartesian product with all possible permutations:

`crossJoin` 메소드는 컬렉션의 값을 주어진 배열 또는 컬렉션을 사용하여 크로스 조인(cross join)하여 합성 가능한 모든 순열을 가지는 데카르트 곱(직교 값)을 반환합니다.

    $collection = collect([1, 2]);

    $matrix = $collection->crossJoin(['a', 'b']);

    $matrix->all();

    /*
        [
            [1, 'a'],
            [1, 'b'],
            [2, 'a'],
            [2, 'b'],
        ]
    */

    $collection = collect([1, 2]);

    $matrix = $collection->crossJoin(['a', 'b'], ['I', 'II']);

    $matrix->all();

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

<a name="method-dd"></a>
#### `dd()` {#collection-method}
#### `dd()` {#collection-method}

The `dd` method dumps the collection's items and ends execution of the script:

`dd` 메소드는 컬렉션의 아이템을 덤프하여 표시하고 스크립트를 종료합니다.

    $collection = collect(['John Doe', 'Jane Doe']);

    $collection->dd();

    /*
        Collection {
            #items: array:2 [
                0 => "John Doe"
                1 => "Jane Doe"
            ]
        }
    */

If you do not want to stop executing the script, use the [`dump`](#method-dump) method instead.

스크립트가 종료되지 않길 원한다면, [`dump`](#method-dump) 메소드를 사용하십시오.

<a name="method-diff"></a>
#### `diff()` {#collection-method}
#### `diff()` {#collection-method}

The `diff` method compares the collection against another collection or a plain PHP `array` based on its values. This method will return the values in the original collection that are not present in the given collection:

`diff` 메소드는 컬렉션을 다른 컬렉션 또는 일반적인 PHP `배열`의 값을 기준으로 비교합니다. 이 메소드는 주어진 컬렉션 안에는 없고, 원래의 컬렉션 안에는 있는 값을 반환합니다.

    $collection = collect([1, 2, 3, 4, 5]);

    $diff = $collection->diff([2, 4, 6, 8]);

    $diff->all();

    // [1, 3, 5]

<a name="method-diffassoc"></a>
#### `diffAssoc()` {#collection-method}
#### `diffAssoc()` {#collection-method}

The `diffAssoc` method compares the collection against another collection or a plain PHP `array` based on its keys and values. This method will return the key / value pairs in the original collection that are not present in the given collection:

`diffAssoc` 메소드는 컬렉션을 키와 값을 기준으로 하여 다른 컬렉션 또는 PHP `배열`과 비교합니다. 이 메소드는 주어진 컬렉션에는 있지만 원래의 컬렉션에서는 존재하지 않는 키/값 쌍을 반환합니다.

    $collection = collect([
        'color' => 'orange',
        'type' => 'fruit',
        'remain' => 6
    ]);

    $diff = $collection->diffAssoc([
        'color' => 'yellow',
        'type' => 'fruit',
        'remain' => 3,
        'used' => 6,
    ]);

    $diff->all();

    // ['color' => 'orange', 'remain' => 6]

<a name="method-diffkeys"></a>
#### `diffKeys()` {#collection-method}
#### `diffKeys()` {#collection-method}

The `diffKeys` method compares the collection against another collection or a plain PHP `array` based on its keys. This method will return the key / value pairs in the original collection that are not present in the given collection:

`diffKeys` 메소드는 컬렉션과 다른 컬렉션의 키를 기준으로 `배열`을 비교합니다. 이 메소드는 주어진 컬렉션 안에 들어 있지 않은 원래의 컬렉션 안에 있는 키/값 쌍을 반환합니다.

    $collection = collect([
        'one' => 10,
        'two' => 20,
        'three' => 30,
        'four' => 40,
        'five' => 50,
    ]);

    $diff = $collection->diffKeys([
        'two' => 2,
        'four' => 4,
        'six' => 6,
        'eight' => 8,
    ]);

    $diff->all();

    // ['one' => 10, 'three' => 30, 'five' => 50]

<a name="method-dump"></a>
#### `dump()` {#collection-method}
#### `dump()` {#collection-method}

The `dump` method dumps the collection's items:

`dump` 메소드는 컬렉션의 아이템을 덤프하여 표시합니다.

    $collection = collect(['John Doe', 'Jane Doe']);

    $collection->dump();

    /*
        Collection {
            #items: array:2 [
                0 => "John Doe"
                1 => "Jane Doe"
            ]
        }
    */

If you want to stop executing the script after dumping the collection, use the [`dd`](#method-dd) method instead.

컬렉션을 덤프한 후 스크립트의 실행을 멈추고 싶다면, [`dd`](#method-dd) 메소드를 사용하십시오.

<a name="method-duplicates"></a>
#### `duplicates()` {#collection-method}
#### `duplicates()` {#collection-method}

The `duplicates` method retrieves and returns duplicate values from the collection:

`duplicates` 메소드는 컬렉션으로부터 중복 된 값을 받아서 반환합니다.

    $collection = collect(['a', 'b', 'a', 'c', 'b']);

    $collection->duplicates();

    // [2 => 'a', 4 => 'b']

If the collection contains arrays or objects, you can pass the key of the attributes that you wish to check for duplicate values:

컬렉션에 배열이나 객체가 포함되어 있으면 중복 값을 확인하려는 속성의 키를 전달할 수 있습니다.

    $employees = collect([
        ['email' => 'abigail@example.com', 'position' => 'Developer'],
        ['email' => 'james@example.com', 'position' => 'Designer'],
        ['email' => 'victoria@example.com', 'position' => 'Developer'],
    ])

    $employees->duplicates('position');

    // [2 => 'Developer']

<a name="method-duplicatesstrict"></a>
#### `duplicatesStrict()` {#collection-method}
#### `duplicatesStrict()` {#collection-method}

This method has the same signature as the [`duplicates`](#method-duplicates) method; however, all values are compared using "strict" comparisons.

이 메소드는 [`duplicates`](#method-duplicates) 메소드와 사용법이 같습니다. 그러나 모든 값은 "엄격한-strict"비교를 사용하여 비교됩니다.

<a name="method-each"></a>
#### `each()` {#collection-method}
#### `each()` {#collection-method}

The `each` method iterates over the items in the collection and passes each item to a callback:

`each` 메소드는 컬렉션의 아이템을 반복적으로 처리하여 콜백에 각 아이템을 전달합니다.

    $collection->each(function ($item, $key) {
        //
    });

If you would like to stop iterating through the items, you may return `false` from your callback:

전체 항목에 대한 반복을 중지하려면, 콜백 안에서 `false` 를 반환하면 됩니다.

    $collection->each(function ($item, $key) {
        if (/* some condition */) {
            return false;
        }
    });

<a name="method-eachspread"></a>
#### `eachSpread()` {#collection-method}
#### `eachSpread()` {#collection-method}

The `eachSpread` method iterates over the collection's items, passing each nested item value into the given callback:

`eachSpread` 메소드는 컬렉션의 아이템을 돌면서 각각의 중첩된 아이템 값을 주어진 콜백에 전달합니다.

    $collection = collect([['John Doe', 35], ['Jane Doe', 33]]);

    $collection->eachSpread(function ($name, $age) {
        //
    });

You may stop iterating through the items by returning `false` from the callback:

반복을 멈추고자 한다면, 콜백안에서 `false` 를 반환하면 됩니다.

    $collection->eachSpread(function ($name, $age) {
        return false;
    });

<a name="method-every"></a>
#### `every()` {#collection-method}
#### `every()` {#collection-method}

The `every` method may be used to verify that all elements of a collection pass a given truth test:

`every` 메소드는 컬렉션의 모든 요소들이 전달된 조건을 충족하는지 확인하는데 사용할 수 있습니다.

    collect([1, 2, 3, 4])->every(function ($value, $key) {
        return $value > 2;
    });

    // false

If the collection is empty, `every` will return true:

컬렉션이 비어 있다면, `every`는 true를 반환할 것입니다.

    $collection = collect([]);

    $collection->every(function($value, $key) {
        return $value > 2;
    });

    // true

<a name="method-except"></a>
#### `except()` {#collection-method}
#### `except()` {#collection-method}

The `except` method returns all items in the collection except for those with the specified keys:

`except` 메소드는 지정된 키에 대한 아이템을 제외한 컬렉션의 모든 아이템을 반환합니다.

    $collection = collect(['product_id' => 1, 'price' => 100, 'discount' => false]);

    $filtered = $collection->except(['price', 'discount']);

    $filtered->all();

    // ['product_id' => 1]

For the inverse of `except`, see the [only](#method-only) method.

`except`의 반대는, [only](#method-only)메소드를 확인하십시오.

<a name="method-filter"></a>
#### `filter()` {#collection-method}
#### `filter()` {#collection-method}

The `filter` method filters the collection using the given callback, keeping only those items that pass a given truth test:

`filter` 메소드는 주어진 콜백 컬렉션을 사용하여 필터링을 하고, 주어진 테스트에 true를 반환하는 항목 만 남게 됩니다.

    $collection = collect([1, 2, 3, 4]);

    $filtered = $collection->filter(function ($value, $key) {
        return $value > 2;
    });

    $filtered->all();

    // [3, 4]

If no callback is supplied, all entries of the collection that are equivalent to `false` will be removed:

콜백이 전달되지 않는다면, 모든 컬렉션의 엔트리 중에서 `false` 로 인식되는 값들은 제거됩니다.

    $collection = collect([1, 2, 3, null, false, '', 0, []]);

    $collection->filter()->all();

    // [1, 2, 3]

For the inverse of `filter`, see the [reject](#method-reject) method.

`filter`의 반대는 [reject](#method-reject) 메소드를 확인하십시오.

<a name="method-first"></a>
#### `first()` {#collection-method}
#### `first()` {#collection-method}

The `first` method returns the first element in the collection that passes a given truth test:

`first` 메소드는 주어진 테스트를 통과하는 컬렉션의 첫 번째 요소를 반환합니다.

    collect([1, 2, 3, 4])->first(function ($value, $key) {
        return $value > 2;
    });

    // 3

You may also call the `first` method with no arguments to get the first element in the collection. If the collection is empty, `null` is returned:

`first` 메소드를 아무런 인자 없이 호출하여 컬렉션의 첫 번째 요소를 획득할 수 있습니다. 만약 컬렉션이 비어 있다면 `null`이 반환됩니다.

    collect([1, 2, 3, 4])->first();

    // 1

<a name="method-first-where"></a>
#### `firstWhere()` {#collection-method}
#### `firstWhere()` {#collection-method}

The `firstWhere` method returns the first element in the collection with the given key / value pair:

`firstWhere` 메소드는 주어진 키 /값에 해당하는 첫 번째 요소를 반환합니다.

    $collection = collect([
        ['name' => 'Regena', 'age' => null],
        ['name' => 'Linda', 'age' => 14],
        ['name' => 'Diego', 'age' => 23],
        ['name' => 'Linda', 'age' => 84],
    ]);

    $collection->firstWhere('name', 'Linda');

    // ['name' => 'Linda', 'age' => 14]

You may also call the `firstWhere` method with an operator:

또한 `firstWhere` 메소드에 연산자를 지정할 수도 있습니다.

    $collection->firstWhere('age', '>=', 18);

    // ['name' => 'Diego', 'age' => 23]

Like the [where](#method-where) method, you may pass one argument to the `firstWhere` method. In this scenario, the `firstWhere` method will return the first item where the given item key's value is "truthy":

[where](#method-where) 메소드와 같이, `firstWhere` 메소드에 인자 한 개를 전달할 수 있습니다. 이러한 경우에는 `firstWhere` 메소드는 주어진 아이템 키의 값이 참이 되는 첫 번째 아이템을 반환합니다.

    $collection->firstWhere('age');

    // ['name' => 'Linda', 'age' => 14]

<a name="method-flatmap"></a>
#### `flatMap()` {#collection-method}
#### `flatMap()` {#collection-method}

The `flatMap` method iterates through the collection and passes each value to the given callback. The callback is free to modify the item and return it, thus forming a new collection of modified items. Then, the array is flattened by a level:

`flatMap` 메소드는 각각의 컬렉션을 반복하며 각자의 값을 주어진 콜백에 전달합니다. 콜백은 자유롭게 이 값을 변경하고 돌려주며 이를 통해서 수정된 값을 기반으로 새로운 컬렉션을 만듭니다. 이 배열은 일차원이 됩니다.

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
#### `flatten()` {#collection-method}

The `flatten` method flattens a multi-dimensional collection into a single dimension:

`flatten` 메소드는 다차원으로 구성된 컬렉션을 한 차원으로 변경시킵니다.

    $collection = collect(['name' => 'taylor', 'languages' => ['php', 'javascript']]);

    $flattened = $collection->flatten();

    $flattened->all();

    // ['taylor', 'php', 'javascript'];

You may optionally pass the function a "depth" argument:

여러분은 선택적으로 함수에 "깊이" 인자를 전달할 수 있습니다.

    $collection = collect([
        'Apple' => [
            ['name' => 'iPhone 6S', 'brand' => 'Apple'],
        ],
        'Samsung' => [
            ['name' => 'Galaxy S7', 'brand' => 'Samsung']
        ],
    ]);

    $products = $collection->flatten(1);

    $products->values()->all();

    /*
        [
            ['name' => 'iPhone 6S', 'brand' => 'Apple'],
            ['name' => 'Galaxy S7', 'brand' => 'Samsung'],
        ]
    */

In this example, calling `flatten` without providing the depth would have also flattened the nested arrays, resulting in `['iPhone 6S', 'Apple', 'Galaxy S7', 'Samsung']`. Providing a depth allows you to restrict the levels of nested arrays that will be flattened.

이 예제에서, `flatten` 을 깊이에 대한 인자 없이 호출하면, 중첩 배열을 평평하게 하기 때문에 그 결과 `['iPhone 6S', 'Apple', 'Galaxy S7', 'Samsung']`이 됩니다. 깊이를 전달하게 되면 중첩된 배열의 깊이를 제한하여 정리될 것입니다.

<a name="method-flip"></a>
#### `flip()` {#collection-method}
#### `flip()` {#collection-method}

The `flip` method swaps the collection's keys with their corresponding values:

`flip` 메소드는 컬렉션의 키와 대응하는 값을 바꿉니다.

    $collection = collect(['name' => 'taylor', 'framework' => 'laravel']);

    $flipped = $collection->flip();

    $flipped->all();

    // ['taylor' => 'name', 'laravel' => 'framework']

<a name="method-forget"></a>
#### `forget()` {#collection-method}
#### `forget()` {#collection-method}

The `forget` method removes an item from the collection by its key:

`forget` 메소드는 키에 해당하는 컬렉션의 아이템을 삭제합니다.

    $collection = collect(['name' => 'taylor', 'framework' => 'laravel']);

    $collection->forget('name');

    $collection->all();

    // ['framework' => 'laravel']

> {note} Unlike most other collection methods, `forget` does not return a new modified collection; it modifies the collection it is called on.

> {note} 다른 컬렉션 메소드와는 달리 `forget`는 수정된 새로운 컬렉션을 반환하지 않습니다; 호출된 컬렉션을 변경합니다.

<a name="method-forpage"></a>
#### `forPage()` {#collection-method}
#### `forPage()` {#collection-method}

The `forPage` method returns a new collection containing the items that would be present on a given page number. The method accepts the page number as its first argument and the number of items to show per page as its second argument:

`forPage` 메소드는 주어진 페이지 넘버에 해당하는 아이템을 가지고 있는 새로운 컬렉션을 반환합니다. 이 메소드는 페이지 번호를 첫 번째 인자로 페이지 별로 보여주고자 하는 아이템의 개수를 두 번째 인자로 받아들입니다.

    $collection = collect([1, 2, 3, 4, 5, 6, 7, 8, 9]);

    $chunk = $collection->forPage(2, 3);

    $chunk->all();

    // [4, 5, 6]

<a name="method-get"></a>
#### `get()` {#collection-method}
#### `get()` {#collection-method}

The `get` method returns the item at a given key. If the key does not exist, `null` is returned:

`get` 메소드는 주어진 키에 대한 아이템을 반환합니다. 키가 존재하지 않다면, `null`이 반환됩니다.

    $collection = collect(['name' => 'taylor', 'framework' => 'laravel']);

    $value = $collection->get('name');

    // taylor

You may optionally pass a default value as the second argument:

두 번째 인자로 기본값을 전달할 수도 있습니다.

    $collection = collect(['name' => 'taylor', 'framework' => 'laravel']);

    $value = $collection->get('foo', 'default-value');

    // default-value

You may even pass a callback as the default value. The result of the callback will be returned if the specified key does not exist:

기본값을 결정하는 콜백을 전달할 수도 있습니다. 지정된 키가 존재하지 않는 경우에 콜백의 결과가 반환될 것입니다.

    $collection->get('email', function () {
        return 'default-value';
    });

    // default-value

<a name="method-groupby"></a>
#### `groupBy()` {#collection-method}
#### `groupBy()` {#collection-method}

The `groupBy` method groups the collection's items by a given key:

`groupBy` 메소드는 주어진 키를 기준으로한 컬렉션의 아이템을 그룹을 지정합니다.

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

Instead of passing a string `key`, you may pass a callback. The callback should return the value you wish to key the group by:

문자로 된 `key` 를 전달하는 대신 콜백을 전달할 수 있습니다. 콜백은 그룹으로 지정할 키를 반환해야 합니다.

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

Multiple grouping criteria may be passed as an array. Each array element will be applied to the corresponding level within a multi-dimensional array:

다중 그룹 처리를 위해 배열을 넘길 수 있습니다. 각 배열 요소들은 다차원 배열에 맞는 단계로 그룹 처리 되어 적용됩니다.

    $data = new Collection([
        10 => ['user' => 1, 'skill' => 1, 'roles' => ['Role_1', 'Role_3']],
        20 => ['user' => 2, 'skill' => 1, 'roles' => ['Role_1', 'Role_2']],
        30 => ['user' => 3, 'skill' => 2, 'roles' => ['Role_1']],
        40 => ['user' => 4, 'skill' => 2, 'roles' => ['Role_2']],
    ]);

    $result = $data->groupBy([
        'skill',
        function ($item) {
            return $item['roles'];
        },
    ], $preserveKeys = true);

    /*
    [
        1 => [
            'Role_1' => [
                10 => ['user' => 1, 'skill' => 1, 'roles' => ['Role_1', 'Role_3']],
                20 => ['user' => 2, 'skill' => 1, 'roles' => ['Role_1', 'Role_2']],
            ],
            'Role_2' => [
                20 => ['user' => 2, 'skill' => 1, 'roles' => ['Role_1', 'Role_2']],
            ],
            'Role_3' => [
                10 => ['user' => 1, 'skill' => 1, 'roles' => ['Role_1', 'Role_3']],
            ],
        ],
        2 => [
            'Role_1' => [
                30 => ['user' => 3, 'skill' => 2, 'roles' => ['Role_1']],
            ],
            'Role_2' => [
                40 => ['user' => 4, 'skill' => 2, 'roles' => ['Role_2']],
            ],
        ],
    ];
    */

<a name="method-has"></a>
#### `has()` {#collection-method}
#### `has()` {#collection-method}

The `has` method determines if a given key exists in the collection:

`has` 메소드는 주어진 키가 컬렉션 안에 존재하는지 알려줍니다.

    $collection = collect(['account_id' => 1, 'product' => 'Desk', 'amount' => 5]);

    $collection->has('product');

    // true

    $collection->has(['product', 'amount']);

    // true

    $collection->has(['amount', 'price']);

    // false

<a name="method-implode"></a>
#### `implode()` {#collection-method}
#### `implode()` {#collection-method}

The `implode` method joins the items in a collection. Its arguments depend on the type of items in the collection. If the collection contains arrays or objects, you should pass the key of the attributes you wish to join, and the "glue" string you wish to place between the values:

`implode` 메소드는 컬렉션 안의 아이템들을 합쳐줍니다. 이 메소드의 인자는 컬렉션 안에 있는 아이템들의 타입에 의존합니다. 컬렉션이 배열 또는 객체를 가지고 있다면, 합치고자 하는 속성의 키와 값들 사이에 끼워 넣고자 하는 "glue" 문자열을 전달해야 합니다.

    $collection = collect([
        ['account_id' => 1, 'product' => 'Desk'],
        ['account_id' => 2, 'product' => 'Chair'],
    ]);

    $collection->implode('product', ', ');

    // Desk, Chair

If the collection contains simple strings or numeric values, pass the "glue" as the only argument to the method:

컬렉션이 간단한 문자열 또는 숫자값을 가지고 있다면, 간단하게 "glue" 를 첫 번째 인자로 전달하면 됩니다.

    collect([1, 2, 3, 4, 5])->implode('-');

    // '1-2-3-4-5'

<a name="method-intersect"></a>
#### `intersect()` {#collection-method}
#### `intersect()` {#collection-method}

The `intersect` method removes any values from the original collection that are not present in the given `array` or collection. The resulting collection will preserve the original collection's keys:

`intersect` 메소드는 원래의 컬렉션에서 주어진 배열 또는 컬렉션에 존재하지 않는 값을 제거합니다. 메소드를 호출한 뒤에도 원래 컬렉션의 키 값은 보존됩니다.

    $collection = collect(['Desk', 'Sofa', 'Chair']);

    $intersect = $collection->intersect(['Desk', 'Chair', 'Bookcase']);

    $intersect->all();

    // [0 => 'Desk', 2 => 'Chair']

<a name="method-intersectbykeys"></a>
#### `intersectByKeys()` {#collection-method}
#### `intersectByKeys()` {#collection-method}

The `intersectByKeys` method removes any keys from the original collection that are not present in the given `array` or collection:

`intersectByKeys` 메소드는 원래의 컬렉션에서 주어진 `배열` 또는 컬렉션에 존재하지 않는 키를 제거합니다.

    $collection = collect([
        'serial' => 'UX301', 'type' => 'screen', 'year' => 2009
    ]);

    $intersect = $collection->intersectByKeys([
        'reference' => 'UX404', 'type' => 'tab', 'year' => 2011
    ]);

    $intersect->all();

    // ['type' => 'screen', 'year' => 2009]

<a name="method-isempty"></a>
#### `isEmpty()` {#collection-method}
#### `isEmpty()` {#collection-method}

The `isEmpty` method returns `true` if the collection is empty; otherwise, `false` is returned:

`isEmpty` 메소드는 컬렉션이 비어 있는 경우 `true`를 반환하고, 그렇지 않은 경우 `false`를 반환합니다.

    collect([])->isEmpty();

    // true

<a name="method-isnotempty"></a>
#### `isNotEmpty()` {#collection-method}
#### `isNotEmpty()` {#collection-method}

The `isNotEmpty` method returns `true` if the collection is not empty; otherwise, `false` is returned:

`isNotEmpty` 메소드는 컬렉션이 비어 있지 않은 경우 `true`를 반환하고, 그렇지 않은 경우 `false`를 반환합니다.

    collect([])->isNotEmpty();

    // false

<a name="method-keyby"></a>
#### `keyBy()` {#collection-method}
#### `keyBy()` {#collection-method}

The `keyBy` method keys the collection by the given key. If multiple items have the same key, only the last one will appear in the new collection:

`keyBy` 메소드는 주어진 키를 기준으로 컬렉션을 다시 구성합니다. 여러 아이템이 같은 키를 가지고 있다면, 새로운 컬렉션에서는 마지막 항목만 나타납니다.

    $collection = collect([
        ['product_id' => 'prod-100', 'name' => 'Desk'],
        ['product_id' => 'prod-200', 'name' => 'Chair'],
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

메소드에 콜백은 전달할 수도 있습니다. 콜백은 컬렉션의 키 값을 반환해야합니다.

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
#### `keys()` {#collection-method}

The `keys` method returns all of the collection's keys:

`keys` 메소드는 컬렉션의 모든 키를 반환합니다.

    $collection = collect([
        'prod-100' => ['product_id' => 'prod-100', 'name' => 'Desk'],
        'prod-200' => ['product_id' => 'prod-200', 'name' => 'Chair'],
    ]);

    $keys = $collection->keys();

    $keys->all();

    // ['prod-100', 'prod-200']

<a name="method-last"></a>
#### `last()` {#collection-method}
#### `last()` {#collection-method}

The `last` method returns the last element in the collection that passes a given truth test:

`last` 메소드는 주어진 콜백에서 참이 되는 값 중에 가장 마지막 값을 반환합니다.

    collect([1, 2, 3, 4])->last(function ($value, $key) {
        return $value < 3;
    });

    // 2

You may also call the `last` method with no arguments to get the last element in the collection. If the collection is empty, `null` is returned:

`last` 메소드를 인자 없이 호출하여 컬렉션의 가장 마지막 값을 획득할 수도 있습니다. 컬렉션이 비어 있다면, `null`이 반환됩니다.

    collect([1, 2, 3, 4])->last();

    // 4

<a name="method-macro"></a>
#### `macro()` {#collection-method}
#### `macro()` {#collection-method}

The static `macro` method allows you to add methods to the `Collection` class at run time. Refer to the documentation on [extending collections](#extending-collections) for more information.

`macro` 메소드를 사용하면 런타임에 `Collection` 클래스에 메소드를 추가할 수 있습니다. 자세한 정보는 [컬렉션 확장하기](#extending-collections) 문서를 참조하십시오.

<a name="method-make"></a>
#### `make()` {#collection-method}
#### `make()` {#collection-method}

The static `make` method creates a new collection instance. See the [Creating Collections](#creating-collections) section.

`make` 메소드는 새로운 컬렉션 인스턴스를 생성합니다. [컬렉션 생성하기](#creating-collections) 부분을 참고하십시오.

<a name="method-map"></a>
#### `map()` {#collection-method}
#### `map()` {#collection-method}

The `map` method iterates through the collection and passes each value to the given callback. The callback is free to modify the item and return it, thus forming a new collection of modified items:

`map` 메소드는 컬렉션 전체를 반복하여 주어진 콜백에 각각의 값을 전달합니다. 콜백은 자유롭게 아이템을 변경하고 반환하여, 변경된 아이템으로 구성된 새로운 컬렉션이 만들어 집니다.

    $collection = collect([1, 2, 3, 4, 5]);

    $multiplied = $collection->map(function ($item, $key) {
        return $item * 2;
    });

    $multiplied->all();

    // [2, 4, 6, 8, 10]

> {note} Like most other collection methods, `map` returns a new collection instance; it does not modify the collection it is called on. If you want to transform the original collection, use the [`transform`](#method-transform) method.

> {note} 대다수의 다른 컬렉션 메소드와 같이, `map` 메소드는 새로운 컬렉션 인스턴스를 반환합니다; 이 메소드는 호출된 컬렉션을 변경하지 않습니다. 원래의 컬렉션을 변경하고자 한다면 [`transform`](#method-transform)메소드를 사용하십시오.

<a name="method-mapinto"></a>
#### `mapInto()` {#collection-method}
#### `mapInto()` {#collection-method}

The `mapInto()` method iterates over the collection, creating a new instance of the given class by passing the value into the constructor:

`mapInto()` 메소드는 컬렉션의 아이템을 반복하면서 전달된 클래스의 생성자에 주어진 값을 전달하여 새로운 인스턴스를 생성합니다.

    class Currency
    {
        /**
         * Create a new currency instance.
         *
         * @param  string  $code
         * @return void
         */
        function __construct(string $code)
        {
            $this->code = $code;
        }
    }

    $collection = collect(['USD', 'EUR', 'GBP']);

    $currencies = $collection->mapInto(Currency::class);

    $currencies->all();

    // [Currency('USD'), Currency('EUR'), Currency('GBP')]

<a name="method-mapspread"></a>
#### `mapSpread()` {#collection-method}
#### `mapSpread()` {#collection-method}

The `mapSpread` method iterates over the collection's items, passing each nested item value into the given callback. The callback is free to modify the item and return it, thus forming a new collection of modified items:

`mapSpread` 메소드는 컬렉션의 아이템을 돌면서 각각의 중첩된 아이템 값을 주어진 콜백에 전달합니다. 콜백에서는 자유롭게 아이템을 수정하고 반환할 수 있으며, 그 결과 수정된 아이템으로 구성된 새로운 컬렉션을 반환합니다.

    $collection = collect([0, 1, 2, 3, 4, 5, 6, 7, 8, 9]);

    $chunks = $collection->chunk(2);

    $sequence = $chunks->mapSpread(function ($even, $odd) {
        return $even + $odd;
    });

    $sequence->all();

    // [1, 5, 9, 13, 17]

<a name="method-maptogroups"></a>
#### `mapToGroups()` {#collection-method}
#### `mapToGroups()` {#collection-method}

The `mapToGroups` method groups the collection's items by the given callback. The callback should return an associative array containing a single key / value pair, thus forming a new collection of grouped values:

`mapToGroups` 메소드는 컬렉션의 항목을 지정된 콜백 별로 그룹화합니다. 콜백은 하나의 키/값 쌍을 포함하는 그룹화 된 값으로 구성된 새로운 컬렉션을 반환합니다.

    $collection = collect([
        [
            'name' => 'John Doe',
            'department' => 'Sales',
        ],
        [
            'name' => 'Jane Doe',
            'department' => 'Sales',
        ],
        [
            'name' => 'Johnny Doe',
            'department' => 'Marketing',
        ]
    ]);

    $grouped = $collection->mapToGroups(function ($item, $key) {
        return [$item['department'] => $item['name']];
    });

    $grouped->toArray();

    /*
        [
            'Sales' => ['John Doe', 'Jane Doe'],
            'Marketing' => ['Johnny Doe'],
        ]
    */

    $grouped->get('Sales')->all();

    // ['John Doe', 'Jane Doe']

<a name="method-mapwithkeys"></a>
#### `mapWithKeys()` {#collection-method}
#### `mapWithKeys()` {#collection-method}

The `mapWithKeys` method iterates through the collection and passes each value to the given callback. The callback should return an associative array containing a single key / value pair:

`mapWithKeys` 메소드는 컬렉션 전체를 반복하며 각각의 값을 주어진 콜백에 전달합니다 콜백은 하나의 키/값 쌍을 포함하는 연관 배열을 반환합니다.

    $collection = collect([
        [
            'name' => 'John',
            'department' => 'Sales',
            'email' => 'john@example.com'
        ],
        [
            'name' => 'Jane',
            'department' => 'Marketing',
            'email' => 'jane@example.com'
        ]
    ]);

    $keyed = $collection->mapWithKeys(function ($item) {
        return [$item['email'] => $item['name']];
    });

    $keyed->all();

    /*
        [
            'john@example.com' => 'John',
            'jane@example.com' => 'Jane',
        ]
    */

<a name="method-max"></a>
#### `max()` {#collection-method}
#### `max()` {#collection-method}

The `max` method returns the maximum value of a given key:

`max` 메소드는 주어진 키에 해당하는 최대값을 반환합니다.

    $max = collect([['foo' => 10], ['foo' => 20]])->max('foo');

    // 20

    $max = collect([1, 2, 3, 4, 5])->max();

    // 5

<a name="method-median"></a>
#### `median()` {#collection-method}
#### `median()` {#collection-method}

The `median` method returns the [median value](https://en.wikipedia.org/wiki/Median) of a given key:

`median` 메소드는 주어진 키에 대한 [중간값](https://en.wikipedia.org/wiki/Median)을 반환합니다

    $median = collect([['foo' => 10], ['foo' => 10], ['foo' => 20], ['foo' => 40]])->median('foo');

    // 15

    $median = collect([1, 1, 2, 4])->median();

    // 1.5

<a name="method-merge"></a>
#### `merge()` {#collection-method}
#### `merge()` {#collection-method}

The `merge` method merges the given array or collection with the original collection. If a string key in the given items matches a string key in the original collection, the given items's value will overwrite the value in the original collection:

`merge` 메소드는 주어진 배열 또는 컬렉션을 원래의 컬렉션과 합칩니다. 배열 안에 들어 있는 키가 컬렉션에 들어 있는 키와 일치한다면, 주어진 배열의 값이 원래의 컬렉션 안의 값을 덮어 쓸 것입니다.

    $collection = collect(['product_id' => 1, 'price' => 100]);

    $merged = $collection->merge(['price' => 200, 'discount' => false]);

    $merged->all();

    // ['product_id' => 1, 'price' => 200, 'discount' => false]

If the given items's keys are numeric, the values will be appended to the end of the collection:

만약 주어진 아이템의 키가 숫자라면, 이 값들은 컬렉션의 가장 마지막에 추가됩니다.

    $collection = collect(['Desk', 'Chair']);

    $merged = $collection->merge(['Bookcase', 'Door']);

    $merged->all();

    // ['Desk', 'Chair', 'Bookcase', 'Door']

<a name="method-mergerecursive"></a>
#### `mergeRecursive()` {#collection-method}
#### `mergeRecursive()` {#collection-method}

The `mergeRecursive` method merges the given array or collection recursively with the original collection. If a string key in the given items matches a string key in the original collection, then the values for these keys are merged together into an array, and this is done recursively:

`mergeRecursive` 메소드는 주어진 배열이나 콜렉션을 재귀적으로 원래 콜렉션과 병합합니다. 지정된 항목의 문자열 키가 원래 모음의 문자열 키와 일치하면이 키의 값이 배열로 함께 병합되며 이는 재귀적으로 수행됩니다.

    $collection = collect(['product_id' => 1, 'price' => 100]);

    $merged = $collection->mergeRecursive(['product_id' => 2, 'price' => 200, 'discount' => false]);

    $merged->all();

    // ['product_id' => [1, 2], 'price' => [100, 200], 'discount' => false]

<a name="method-min"></a>
#### `min()` {#collection-method}
#### `min()` {#collection-method}

The `min` method returns the minimum value of a given key:

`min` 메소드는 주어진 키에 대한 최소 값을 반환합니다.

    $min = collect([['foo' => 10], ['foo' => 20]])->min('foo');

    // 10

    $min = collect([1, 2, 3, 4, 5])->min();

    // 1

<a name="method-mode"></a>
#### `mode()` {#collection-method}
#### `mode()` {#collection-method}

The `mode` method returns the [mode value](https://en.wikipedia.org/wiki/Mode_(statistics)) of a given key:

`mode` 메소드는 [확률분포에서의 중앙값](https://en.wikipedia.org/wiki/Mode_(statistics))을 반환합니다.

    $mode = collect([['foo' => 10], ['foo' => 10], ['foo' => 20], ['foo' => 40]])->mode('foo');

    // [10]

    $mode = collect([1, 1, 2, 4])->mode();

    // [1]

<a name="method-nth"></a>
#### `nth()` {#collection-method}
#### `nth()` {#collection-method}

The `nth` method creates a new collection consisting of every n-th element:

`nth` 메소드는 매 n 번째 존재하는 요소로 구성된 새로운 컬렉션을 생성합니다.

(역자주: 첫 번째 인자로 주어진 숫자로 나누어 나머지가 0인 경우의 아이템들로 구성된 컬렉션이 반환됩니다. 인덱스는 0부터 시작)

    $collection = collect(['a', 'b', 'c', 'd', 'e', 'f']);

    $collection->nth(4);

    // ['a', 'e']

You may optionally pass an offset as the second argument:

두 번째 인자로 offset을 전달할 수도 있습니다.

    $collection->nth(4, 1);

    // ['b', 'f']

<a name="method-only"></a>
#### `only()` {#collection-method}
#### `only()` {#collection-method}

The `only` method returns the items in the collection with the specified keys:

`only` 메소드는 컬렉션 안에서 지정된 키에 대한 아이템 만을 반환합니다.

    $collection = collect(['product_id' => 1, 'name' => 'Desk', 'price' => 100, 'discount' => false]);

    $filtered = $collection->only(['product_id', 'name']);

    $filtered->all();

    // ['product_id' => 1, 'name' => 'Desk']

For the inverse of `only`, see the [except](#method-except) method.

`only` 메소드의 반대는, [except](#method-except)메소드를 확인하십시오.

<a name="method-pad"></a>
#### `pad()` {#collection-method}
#### `pad()` {#collection-method}

The `pad` method will fill the array with the given value until the array reaches the specified size. This method behaves like the [array_pad](https://secure.php.net/manual/en/function.array-pad.php) PHP function.

`pad` 메소드는 배열이 지정된 사이즈에 도달할 때까지, 배열을 주어진 값으로 채웁니다. 이 메소드는 [array_pad](https://secure.php.net/manual/en/function.array-pad.php) PHP 함수와 같은 형태로 동작합니다.

To pad to the left, you should specify a negative size. No padding will take place if the absolute value of the given size is less than or equal to the length of the array:

배열의 앞쪽을 채우기 위해서는 사이즈를 음수로 지정하면 됩니다. 주어진 사이즈의 절대 값이 배열의 길이보다 작거나 같으면 메소드는 실행되지 않습니다.

    $collection = collect(['A', 'B', 'C']);

    $filtered = $collection->pad(5, 0);

    $filtered->all();

    // ['A', 'B', 'C', 0, 0]

    $filtered = $collection->pad(-5, 0);

    $filtered->all();

    // [0, 0, 'A', 'B', 'C']

<a name="method-partition"></a>
#### `partition()` {#collection-method}
#### `partition()` {#collection-method}

The `partition` method may be combined with the `list` PHP function to separate elements that pass a given truth test from those that do not:

`partition` 메소드는 PHP의 `list` 함수와 함께 사용되어 주어진 조건식을 만족하는, 그리고 만족하지 못하는 두 개의 요소들로 나누는데 사용할 수 있습니다.

    $collection = collect([1, 2, 3, 4, 5, 6]);

    list($underThree, $equalOrAboveThree) = $collection->partition(function ($i) {
        return $i < 3;
    });

    $underThree->all();

    // [1, 2]

    $equalOrAboveThree->all();

    // [3, 4, 5, 6]

<a name="method-pipe"></a>
#### `pipe()` {#collection-method}
#### `pipe()` {#collection-method}

The `pipe` method passes the collection to the given callback and returns the result:

`pipe` 메소드는 컬렉션을 주어진 콜백에 전달하고 그 결과를 반환합니다.

    $collection = collect([1, 2, 3]);

    $piped = $collection->pipe(function ($collection) {
        return $collection->sum();
    });

    // 6

<a name="method-pluck"></a>
#### `pluck()` {#collection-method}
#### `pluck()` {#collection-method}

The `pluck` method retrieves all of the values for a given key:

`pluck` 메소드는 주어진 키에 대한 모든 값을 반환합니다.

    $collection = collect([
        ['product_id' => 'prod-100', 'name' => 'Desk'],
        ['product_id' => 'prod-200', 'name' => 'Chair'],
    ]);

    $plucked = $collection->pluck('name');

    $plucked->all();

    // ['Desk', 'Chair']

You may also specify how you wish the resulting collection to be keyed:

또한 컬렉션에서 반환되는 결과의 키로 지정하고자 하는 값을 지정할 수도 있습니다.

    $plucked = $collection->pluck('name', 'product_id');

    $plucked->all();

    // ['prod-100' => 'Desk', 'prod-200' => 'Chair']

If duplicate keys exist, the last matching element will be inserted into the plucked collection:

중복되는 키가 존재한다면, 마지막에 매칭되는 요소가 pluck 결과 컬렉션에 추가됩니다.

    $collection = collect([
        ['brand' => 'Tesla',  'color' => 'red'],
        ['brand' => 'Pagani', 'color' => 'white'],
        ['brand' => 'Tesla',  'color' => 'black'],
        ['brand' => 'Pagani', 'color' => 'orange'],
    ]);

    $plucked = $collection->pluck('color', 'brand');

    $plucked->all();

    // ['Tesla' => 'black', 'Pagani' => 'orange']

<a name="method-pop"></a>
#### `pop()` {#collection-method}
#### `pop()` {#collection-method}

The `pop` method removes and returns the last item from the collection:

`pop` 메소드는 컬렉션의 마지막 값을 반환하고 제거합니다.

    $collection = collect([1, 2, 3, 4, 5]);

    $collection->pop();

    // 5

    $collection->all();

    // [1, 2, 3, 4]

<a name="method-prepend"></a>
#### `prepend()` {#collection-method}
#### `prepend()` {#collection-method}

The `prepend` method adds an item to the beginning of the collection:

`prepend` 메소드는 컬렉션의 앞 부분에 아이템을 추가합니다.

    $collection = collect([1, 2, 3, 4, 5]);

    $collection->prepend(0);

    $collection->all();

    // [0, 1, 2, 3, 4, 5]

You may also pass a second argument to set the key of the prepended item:

또한 두 번째 인자로 앞에 붙이고자 하는 아이템의 키를 설정할 수도 있습니다.

    $collection = collect(['one' => 1, 'two' => 2]);

    $collection->prepend(0, 'zero');

    $collection->all();

    // ['zero' => 0, 'one' => 1, 'two' => 2]

<a name="method-pull"></a>
#### `pull()` {#collection-method}
#### `pull()` {#collection-method}

The `pull` method removes and returns an item from the collection by its key:

`pull` 메소드는 주어진 키에 해당하는 아이템을 반환하고 삭제합니다.

    $collection = collect(['product_id' => 'prod-100', 'name' => 'Desk']);

    $collection->pull('name');

    // 'Desk'

    $collection->all();

    // ['product_id' => 'prod-100']

<a name="method-push"></a>
#### `push()` {#collection-method}
#### `push()` {#collection-method}

The `push` method appends an item to the end of the collection:

`push` 메소드는 컬렉션의 마지막에 아이템을 추가합니다.

    $collection = collect([1, 2, 3, 4]);

    $collection->push(5);

    $collection->all();

    // [1, 2, 3, 4, 5]

<a name="method-put"></a>
#### `put()` {#collection-method}
#### `put()` {#collection-method}

The `put` method sets the given key and value in the collection:

`put` 메소드는 주어진 키와 값을 컬렉션에 추가합니다.

    $collection = collect(['product_id' => 1, 'name' => 'Desk']);

    $collection->put('price', 100);

    $collection->all();

    // ['product_id' => 1, 'name' => 'Desk', 'price' => 100]

<a name="method-random"></a>
#### `random()` {#collection-method}
#### `random()` {#collection-method}

The `random` method returns a random item from the collection:

`random` 메소드는 컬렉션의 아이템 중 하나를 랜덤하게 반환합니다.

    $collection = collect([1, 2, 3, 4, 5]);

    $collection->random();

    // 4 - (retrieved randomly)

You may optionally pass an integer to `random` to specify how many items you would like to randomly retrieve. A collection of items is always returned when explicitly passing the number of items you wish to receive:

얼마나 많은 아이템을 랜덤으로 조회할지 `random` 메소드에 선택적으로 정수값을 전달할 수 있습니다. 수신할 아이템의 수를 명시적으로 전달하면 아이템 컬렉션이 반환됩니다.

    $random = $collection->random(3);

    $random->all();

    // [2, 4, 5] - (retrieved randomly)

If the Collection has fewer items than requested, the method will throw an `InvalidArgumentException`.

컬렉션이 요청된 것보다 더 적은 개수의 아이템을 가지고 있다면, 메소드는 `InvalidArgumentException`을 던집니다.

<a name="method-reduce"></a>
#### `reduce()` {#collection-method}
#### `reduce()` {#collection-method}

The `reduce` method reduces the collection to a single value, passing the result of each iteration into the subsequent iteration:

`reduce` 메소드는 각각의 인자를 반복하여 다음 반복에 결과를 전달하면서 컬렉션을 하나의 값으로 줄입니다.

    $collection = collect([1, 2, 3]);

    $total = $collection->reduce(function ($carry, $item) {
        return $carry + $item;
    });

    // 6

The value for `$carry` on the first iteration is `null`; however, you may specify its initial value by passing a second argument to `reduce`:

첫 번째 반복에서 `$carry`의 값은 `null`입니다; 그러나 초기값을 지정하고자 하는 경우에 `reduce`의 두 번째 인자로 전달할 수 있습니다.

    $collection->reduce(function ($carry, $item) {
        return $carry + $item;
    }, 4);

    // 10

<a name="method-reject"></a>
#### `reject()` {#collection-method}
#### `reject()` {#collection-method}

The `reject` method filters the collection using the given callback. The callback should return `true` if the item should be removed from the resulting collection:

`reject` 메소드는 컬렉션에서 지정된 콜백을 사용하여 필터링을 합니다. 콜백은 결과 컬렉션에서 제거되어야 하는 아이템의 경우 `true`를 반환해야 합니다.

    $collection = collect([1, 2, 3, 4]);

    $filtered = $collection->reject(function ($value, $key) {
        return $value > 2;
    });

    $filtered->all();

    // [1, 2]

For the inverse of the `reject` method, see the [`filter`](#method-filter) method.

`reject` 메소드의 반대는, [`filter`](#method-filter)메소드를 확인하십시오.

<a name="method-replace"></a>
#### `replace()` {#collection-method}
#### `replace()` {#collection-method}

The `replace` method behaves similarly to `merge`; however, in addition to overwriting matching items with string keys, the `replace` method will also overwrite items in the collection that have matching numeric keys:

`replace` 메소드는 `merge`와 비슷하게 동작합니다. 그러나 일치하는 항목을 문자열 키로 덮어 쓰는 것 외에도 `replace` 메소드는 콜렉션에서 일치하는 숫자 키를 가진 항목을 덮어 씁니다.

    $collection = collect(['Taylor', 'Abigail', 'James']);

    $replaced = $collection->replace([1 => 'Victoria', 3 => 'Finn']);

    $replaced->all();

    // ['Taylor', 'Victoria', 'James', 'Finn']

<a name="method-replacerecursive"></a>
#### `replaceRecursive()` {#collection-method}
#### `replaceRecursive()` {#collection-method}

This method works like `replace`, but it will recurse into arrays and apply the same replacement process to the inner values:

이 메소드는 `replace`처럼 동작하지만, 재귀적 배열로 반복되어 내부 값에 동일한 대체 프로세스를 적용합니다.

    $collection = collect(['Taylor', 'Abigail', ['James', 'Victoria', 'Finn']]);

    $replaced = $collection->replaceRecursive(['Charlie', 2 => [1 => 'King']]);

    $replaced->all();

    // ['Charlie', 'Abigail', ['James', 'King', 'Finn']]

<a name="method-reverse"></a>
#### `reverse()` {#collection-method}
#### `reverse()` {#collection-method}

The `reverse` method reverses the order of the collection's items, preserving the original keys:

`reverse` 메소드는 컬렉션의 원래의 키는 유지하면서 아이템의 순서를 반대가 되게 합니다.

    $collection = collect(['a', 'b', 'c', 'd', 'e']);

    $reversed = $collection->reverse();

    $reversed->all();

    /*
        [
            4 => 'e',
            3 => 'd',
            2 => 'c',
            1 => 'b',
            0 => 'a',
        ]
    */

<a name="method-search"></a>
#### `search()` {#collection-method}
#### `search()` {#collection-method}

The `search` method searches the collection for the given value and returns its key if found. If the item is not found, `false` is returned.

`search` 메소드는 주어진 값이 컬렉션에 있는지 찾아서 해당 값의 키를 반환합니다. 아이템을 찾지 못한 경우, `false`가 반환됩니다.

    $collection = collect([2, 4, 6, 8]);

    $collection->search(4);

    // 1

The search is done using a "loose" comparison, meaning a string with an integer value will be considered equal to an integer of the same value. To use "strict" comparison, pass `true` as the second argument to the method:

검색은 "느슨한" 비교로 (타입을 엄격하게 비교하지 않습니다) 다시말해 문자열과 정수값의 경우 동일한 값이라면 같다고 판단합니다. 타입에 일치하는 "엄격한" 비교를 수행하려면 `true`를 메소드의 두 번째 인자로 전달하면 됩니다.

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
#### `shuffle()` {#collection-method}

The `shuffle` method randomly shuffles the items in the collection:

`shuffle` 메소드는 컬렉션의 아이템을 랜덤하게 섞어버립니다.

    $collection = collect([1, 2, 3, 4, 5]);

    $shuffled = $collection->shuffle();

    $shuffled->all();

    // [3, 2, 5, 1, 4] - (generated randomly)

<a name="method-skip"></a>
#### `skip()` {#collection-method}
#### `skip()` {#collection-method}

The `skip` method returns a new collection, without the first given amount of items:

`skip` 메소드는 첫번째로 주어진 항목의 수 만큼을 제외한 새 컬렉션을 반환합니다.

    $collection = collect([1, 2, 3, 4, 5, 6, 7, 8, 9, 10]);

    $collection = $collection->skip(4);

    $collection->all();

    // [5, 6, 7, 8, 9, 10]

<a name="method-slice"></a>
#### `slice()` {#collection-method}
#### `slice()` {#collection-method}

The `slice` method returns a slice of the collection starting at the given index:

`slice` 메소드는 주어진 인덱스에서 시작하는 컬렉션으로 잘라냅니다.

    $collection = collect([1, 2, 3, 4, 5, 6, 7, 8, 9, 10]);

    $slice = $collection->slice(4);

    $slice->all();

    // [5, 6, 7, 8, 9, 10]

If you would like to limit the size of the returned slice, pass the desired size as the second argument to the method:

만약 반환되는 컬렉션의 사이즈를 제한하고자 한다면, 메소드의 두 번째 인자로 사이즈를 전달하면 됩니다.

    $slice = $collection->slice(4, 2);

    $slice->all();

    // [5, 6]

The returned slice will preserve keys by default. If you do not wish to preserve the original keys, you can use the [`values`](#method-values) method to reindex them.

반환되는 슬라이스는 기본적으로 키 값을 유지 한 채 반환합니다. 만약 이전의 원래 키를 유지하지 않길 원한다면, 새로운 인덱스를 구성하기 위해서 [`values`](#method-values) 메소드를 사용할 수 있습니다.

<a name="method-some"></a>
#### `some()` {#collection-method}
#### `some()` {#collection-method}

Alias for the [`contains`](#method-contains) method.

[`contains`](#method-contains) 메소드의 별칭입니다.


<a name="method-sort"></a>
#### `sort()` {#collection-method}
#### `sort()` {#collection-method}

The `sort` method sorts the collection. The sorted collection keeps the original array keys, so in this example we'll use the [`values`](#method-values) method to reset the keys to consecutively numbered indexes:

`sort` 메소드는 컬렉션을 정렬합니다. 정렬된 컬렉션은 원래의 배열 키를 유지 하기 때문에, 다음 예제에서 연속된 숫자 인덱스를 리셋하기 위해서 [`values`](#method-values)메소드를 사용할 것입니다.

    $collection = collect([5, 3, 1, 2, 4]);

    $sorted = $collection->sort();

    $sorted->values()->all();

    // [1, 2, 3, 4, 5]

If your sorting needs are more advanced, you may pass a callback to `sort` with your own algorithm. Refer to the PHP documentation on [`uasort`](https://secure.php.net/manual/en/function.uasort.php#refsect1-function.uasort-parameters), which is what the collection's `sort` method calls under the hood.

보다 복잡한 정렬이 필요하다면, `sort` 메소드에 여러분의 고유한 알고리즘을 위한 콜백을 전달할 수 있습니다. 컬렉션의 `sort` 메소드가 호출될 때의 동작은 [`uasort`](https://secure.php.net/manual/en/function.uasort.php#refsect1-function.uasort-parameters)PHP 문서를 참고하십시오.

> {tip} If you need to sort a collection of nested arrays or objects, see the [`sortBy`](#method-sortby) and [`sortByDesc`](#method-sortbydesc) methods.

> {tip} 만약 중첩된 배열이나 객체의 컬렉션을 정렬할 필요가 있다면, [`sortBy`](#method-sortby) 또는 [`sortByDesc`](#method-sortbydesc) 메소드를 확인하십시오.

<a name="method-sortby"></a>
#### `sortBy()` {#collection-method}
#### `sortBy()` {#collection-method}

The `sortBy` method sorts the collection by the given key. The sorted collection keeps the original array keys, so in this example we'll use the [`values`](#method-values) method to reset the keys to consecutively numbered indexes:

`sortBy` 메소드는 주어진 키에 의한 정렬을 수행합니다. 정렬된 컬렉션은 원래의 배열 키를 유지하기 때문에, 다음 예제에서 연속된 숫자 인덱스를 리셋하기 위해서 [`values`](#method-values)메소드를 사용할 것입니다.

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
#### `sortByDesc()` {#collection-method}

This method has the same signature as the [`sortBy`](#method-sortby) method, but will sort the collection in the opposite order.

이 메소드의 사용법은 [`sortBy`](#method-sortby) 메소드와 동일하지만, 반대의 순서로 컬렉션을 정렬합니다.

<a name="method-sortkeys"></a>
#### `sortKeys()` {#collection-method}
#### `sortKeys()` {#collection-method}

The `sortKeys` method sorts the collection by the keys of the underlying associative array:

`sortKeys` 메소드는 컬렉션을 지정된 배열의 키를 기준으로 정렬합니다.

    $collection = collect([
        'id' => 22345,
        'first' => 'John',
        'last' => 'Doe',
    ]);

    $sorted = $collection->sortKeys();

    $sorted->all();

    /*
        [
            'first' => 'John',
            'id' => 22345,
            'last' => 'Doe',
        ]
    */

<a name="method-sortkeysdesc"></a>
#### `sortKeysDesc()` {#collection-method}
#### `sortKeysDesc()` {#collection-method}

This method has the same signature as the [`sortKeys`](#method-sortkeys) method, but will sort the collection in the opposite order.

이 메소드의 사용법은 [`sortKeys`](#method-sortkeys) 메소드와 동일하지만, 반대의 순서로 컬렉션을 정렬합니다.

<a name="method-splice"></a>
#### `splice()` {#collection-method}
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

반환되는 결과의 사이즈를 제한하기 위해서 두 번째 인자로 전달할 수 있습니다.

    $collection = collect([1, 2, 3, 4, 5]);

    $chunk = $collection->splice(2, 1);

    $chunk->all();

    // [3]

    $collection->all();

    // [1, 2, 4, 5]

In addition, you can pass a third argument containing the new items to replace the items removed from the collection:

추가적으로, 컬렉션에서 아이템을 제거하고, 대체할 새로운 아이템을 세 번째 인자로 전달할 수 있습니다.

    $collection = collect([1, 2, 3, 4, 5]);

    $chunk = $collection->splice(2, 1, [10, 11]);

    $chunk->all();

    // [3]

    $collection->all();

    // [1, 2, 10, 11, 4, 5]

<a name="method-split"></a>
#### `split()` {#collection-method}
#### `split()` {#collection-method}

The `split` method breaks a collection into the given number of groups:

`split` 메소드는 컬렉션을 주어진 개수의 그룹으로 나눕니다.

    $collection = collect([1, 2, 3, 4, 5]);

    $groups = $collection->split(3);

    $groups->toArray();

    // [[1, 2], [3, 4], [5]]

<a name="method-sum"></a>
#### `sum()` {#collection-method}
#### `sum()` {#collection-method}

The `sum` method returns the sum of all items in the collection:

`sum` 메소드는 컬렉션 안에 있는 모든 아이템들의 합을 반환합니다.

    collect([1, 2, 3, 4, 5])->sum();

    // 15

If the collection contains nested arrays or objects, you should pass a key to use for determining which values to sum:

컬렉션이 중첩된 배열이나 객체를 가지고 있다면, 어떤 값을 더해야할지 결정하는 키를 전달해야 합니다.

    $collection = collect([
        ['name' => 'JavaScript: The Good Parts', 'pages' => 176],
        ['name' => 'JavaScript: The Definitive Guide', 'pages' => 1096],
    ]);

    $collection->sum('pages');

    // 1272

In addition, you may pass your own callback to determine which values of the collection to sum:

추가적으로, 컬렉션의 어떤 값을 더해야 할지 결정하는 콜백을 전달할 수도 있습니다.

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
#### `take()` {#collection-method}

The `take` method returns a new collection with the specified number of items:

`take` 메소드는 지정된 숫자의 아이템으로 이루어진 새로운 컬렉션을 반환합니다.

    $collection = collect([0, 1, 2, 3, 4, 5]);

    $chunk = $collection->take(3);

    $chunk->all();

    // [0, 1, 2]

You may also pass a negative integer to take the specified amount of items from the end of the collection:

컬렉션의 끝에서부터 가져올 아이템의 개수를 지정하기 위해서 음수를 전달할 수도 있습니다.

    $collection = collect([0, 1, 2, 3, 4, 5]);

    $chunk = $collection->take(-2);

    $chunk->all();

    // [4, 5]

<a name="method-tap"></a>
#### `tap()` {#collection-method}
#### `tap()` {#collection-method}

The `tap` method passes the collection to the given callback, allowing you to "tap" into the collection at a specific point and do something with the items while not affecting the collection itself:

`tap` 메소드는 컬렉션에 콜백과 함께 전달되며, 원래의 컬렉션에 영향을 주지 않고 특정 지점에 대해서 어떤 작업을 수행하고자 할 때 사용됩니다.

    collect([2, 4, 3, 1, 5])
        ->sort()
        ->tap(function ($collection) {
            Log::debug('Values after sorting', $collection->values()->toArray());
        })
        ->shift();

    // 1

<a name="method-times"></a>
#### `times()` {#collection-method}
#### `times()` {#collection-method}

The static `times` method creates a new collection by invoking the callback a given amount of times:

`times` 메소드는 주어진 양의 횟수만큼 콜백을 수행한 결과로 새로운 컬렉션을 생성합니다.

    $collection = Collection::times(10, function ($number) {
        return $number * 9;
    });

    $collection->all();

    // [9, 18, 27, 36, 45, 54, 63, 72, 81, 90]

This method can be useful when combined with factories to create [Eloquent](/docs/{{version}}/eloquent) models:

이 메소드는 [Eloquent](/docs/{{version}}/eloquent) 모델을 생성하는 팩토리와 조합할 때 유용합니다.

    $categories = Collection::times(3, function ($number) {
        return factory(Category::class)->create(['name' => "Category No. $number"]);
    });

    $categories->all();

    /*
        [
            ['id' => 1, 'name' => 'Category #1'],
            ['id' => 2, 'name' => 'Category #2'],
            ['id' => 3, 'name' => 'Category #3'],
        ]
    */

<a name="method-toarray"></a>
#### `toArray()` {#collection-method}
#### `toArray()` {#collection-method}

The `toArray` method converts the collection into a plain PHP `array`. If the collection's values are [Eloquent](/docs/{{version}}/eloquent) models, the models will also be converted to arrays:

`toArray` 메소드는 컬렉션을 PHP `배열`로 변환합니다. 컬렉션의 값이 [Eloquent](/docs/{{version}}/eloquent) 라면, 이 모델 또한 배열로 변환될 것입니다.

    $collection = collect(['name' => 'Desk', 'price' => 200]);

    $collection->toArray();

    /*
        [
            ['name' => 'Desk', 'price' => 200],
        ]
    */

> {note} `toArray` also converts all of the collection's nested objects that are an instance of `Arrayable` to an array. If you want to get the raw underlying array, use the [`all`](#method-all) method instead.

> {note} 또한 `toArray`는 컬렉션 내의 `Arrayable` 인스턴스인 모든 중첩된 객체를 배열로 변환할 것입니다. 근본적인 raw 배열을 얻기를 원한다면 [`all`](#method-all) 메소드를 대신 사용하십시오.

<a name="method-tojson"></a>
#### `toJson()` {#collection-method}
#### `toJson()` {#collection-method}

The `toJson` method converts the collection into a JSON serialized string:

`toJson` 메소드는 모든 컬렉션을 JSON serialized 문자열로 변환합니다.

    $collection = collect(['name' => 'Desk', 'price' => 200]);

    $collection->toJson();

    // '{"name":"Desk", "price":200}'

<a name="method-transform"></a>
#### `transform()` {#collection-method}
#### `transform()` {#collection-method}

The `transform` method iterates over the collection and calls the given callback with each item in the collection. The items in the collection will be replaced by the values returned by the callback:

`transform` 메소드는 컬렉션을 반복하여 컬렉션의 각각의 아이템에 대해서 주어진 콜백을 호출합니다. 컬렉션 안에 있는 아이템들은 자동으로 콜백의 결과로 대체될 것입니다.

    $collection = collect([1, 2, 3, 4, 5]);

    $collection->transform(function ($item, $key) {
        return $item * 2;
    });

    $collection->all();

    // [2, 4, 6, 8, 10]

> {note} Unlike most other collection methods, `transform` modifies the collection itself. If you wish to create a new collection instead, use the [`map`](#method-map) method.

> {note} 다른 컬렉션 메소드와 다르게, `transform` 메소드는 컬렉션 자신을 변경합니다. 대신에, 새로운 컬렉션을 생성하려면 [`map`](#method-map) 메소드를 사용하십시오.

<a name="method-union"></a>
#### `union()` {#collection-method}
#### `union()` {#collection-method}

The `union` method adds the given array to the collection. If the given array contains keys that are already in the original collection, the original collection's values will be preferred:

`union` 메소드는 주어진 배열을 컬렉션에 추가합니다. 만약 원래의 컬렉션에서 이미 가지고 있는 키를 주어진 배열에서 가지고 있다면, 원래의 컬렉션 값이 우선합니다.

    $collection = collect([1 => ['a'], 2 => ['b']]);

    $union = $collection->union([3 => ['c'], 1 => ['b']]);

    $union->all();

    // [1 => ['a'], 2 => ['b'], 3 => ['c']]

<a name="method-unique"></a>
#### `unique()` {#collection-method}
#### `unique()` {#collection-method}

The `unique` method returns all of the unique items in the collection. The returned collection keeps the original array keys, so in this example we'll use the [`values`](#method-values) method to reset the keys to consecutively numbered indexes:

`unique` 메소드는 컬렉션 안에서 유니크한 모든 아이템들을 반환합니다. 반환된 컬렉션은 원래의 배열 키를 유지하기 때문에, 다음 예제에서 연속된 숫자 인덱스를 리셋하기 위해서 [`values`](#method-values)메소드를 사용할 것입니다.

    $collection = collect([1, 1, 2, 2, 3, 4, 2]);

    $unique = $collection->unique();

    $unique->values()->all();

    // [1, 2, 3, 4]

When dealing with nested arrays or objects, you may specify the key used to determine uniqueness:

중첩된 배열이나 객체를 다룰 때에는, 유니크 여부를 결정할 키를 지정할 수 있습니다.

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

또한 아이템이 유니크한지 결정하기 위한 콜백을 전달할 수도 있습니다.


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

The `unique` method uses "loose" comparisons when checking item values, meaning a string with an integer value will be considered equal to an integer of the same value. Use the [`uniqueStrict`](#method-uniquestrict) method to filter using "strict" comparisons.

`unique` 메소드는 아이템의 값을 비교할 때 "느슨한" 비교를 수행하기 때문에, 정수값이 문자형일 때에도 정수형 값과 동일하다고 판단합니다. 타입에 대한 "엄격한" 비교를 원한다면 [`uniqueStrict`](#method-uniquestrict) 메소드를 사용하십시오.

<a name="method-uniquestrict"></a>
#### `uniqueStrict()` {#collection-method}
#### `uniqueStrict()` {#collection-method}

This method has the same signature as the [`unique`](#method-unique) method; however, all values are compared using "strict" comparisons.

이 메소드는 [`unique`](#method-unique)와 사용방법이 동일합니다. 차이점은 "엄격한" 비교를 수행한다는 점입니다.

<a name="method-unless"></a>
#### `unless()` {#collection-method}
#### `unless()` {#collection-method}

The `unless` method will execute the given callback unless the first argument given to the method evaluates to `true`:

`unless` 메소드는 메소드에 주어진 첫 번째 인자값이 `true`가 아닐 때 두 번째 인자로 전달되는 콜백을 실행합니다.

    $collection = collect([1, 2, 3]);

    $collection->unless(true, function ($collection) {
        return $collection->push(4);
    });

    $collection->unless(false, function ($collection) {
        return $collection->push(5);
    });

    $collection->all();

    // [1, 2, 3, 5]

For the inverse of `unless`, see the [`when`](#method-when) method.

`unless` 메소드의 반대는 [`when`](#method-when) 메소드를 참고하십시오.

<a name="method-unlessempty"></a>
#### `unlessEmpty()` {#collection-method}
#### `unlessEmpty()` {#collection-method}

Alias for the [`whenNotEmpty`](#method-whennotempty) method.

[`whenNotEmpty`](#method-whennotempty) 메소드의 별칭입니다.

<a name="method-unlessnotempty"></a>
#### `unlessNotEmpty()` {#collection-method}
#### `unlessNotEmpty()` {#collection-method}

[`whenEmpty`](#method-whenempty) 메소드의 별칭입니다.

<a name="method-unwrap"></a>
#### `unwrap()` {#collection-method}
#### `unwrap()` {#collection-method}

The static `unwrap` method returns the collection's underlying items from the given value when applicable:

정적 메소드인 `unwrap` 메소드는 해당되는 경우, 컬렉션의 아이템을 컬렉션의 형태에서 해제하여 기본 타입 형태로 반환합니다.

    Collection::unwrap(collect('John Doe'));

    // ['John Doe']

    Collection::unwrap(['John Doe']);

    // ['John Doe']

    Collection::unwrap('John Doe');

    // 'John Doe'

<a name="method-values"></a>
#### `values()` {#collection-method}
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

<a name="method-when"></a>
#### `when()` {#collection-method}
#### `when()` {#collection-method}

The `when` method will execute the given callback when the first argument given to the method evaluates to `true`:

`when` 메소드는 when 메소드에 주어진 첫 번째 인자가 `true`로 판정 될 때 두 번째 인자로 전달되는 콜백을 실행합니다.

    $collection = collect([1, 2, 3]);

    $collection->when(true, function ($collection) {
        return $collection->push(4);
    });

    $collection->when(false, function ($collection) {
        return $collection->push(5);
    });

    $collection->all();

    // [1, 2, 3, 4]

For the inverse of `when`, see the [`unless`](#method-unless) method.

`when` 메소드의 반대는, [`unless`](#method-unless) 메소드를 참고하십시오.

<a name="method-whenempty"></a>
#### `whenEmpty()` {#collection-method}
#### `whenEmpty()` {#collection-method}

The `whenEmpty` method will execute the given callback when the collection is empty:

`whenEmpty` 메소드는 컬렉션이 비어 있을때 전달되는 콜백을 실행합니다.

    $collection = collect(['michael', 'tom']);

    $collection->whenEmpty(function ($collection) {
        return $collection->push('adam');
    });

    $collection->all();

    // ['michael', 'tom']


    $collection = collect();

    $collection->whenEmpty(function ($collection) {
        return $collection->push('adam');
    });

    $collection->all();

    // ['adam']


    $collection = collect(['michael', 'tom']);

    $collection->whenEmpty(function($collection) {
        return $collection->push('adam');
    }, function($collection) {
        return $collection->push('taylor');
    });

    $collection->all();

    // ['michael', 'tom', 'taylor']

For the inverse of `whenEmpty`, see the [`whenNotEmpty`](#method-whennotempty) method.

`whenEmpty`의 반대는, [`whenNotEmpty`](#method-whennotempty) 메소드를 확인하십시오.

<a name="method-whennotempty"></a>
#### `whenNotEmpty()` {#collection-method}
#### `whenNotEmpty()` {#collection-method}

The `whenNotEmpty` method will execute the given callback when the collection is not empty:

`whenNotEmpty` 메소드는 컬렉션이 비어 있지 않을 때, 주어진 콜백을 실행합니다.

    $collection = collect(['michael', 'tom']);

    $collection->whenNotEmpty(function ($collection) {
        return $collection->push('adam');
    });

    $collection->all();

    // ['michael', 'tom', 'adam']


    $collection = collect();

    $collection->whenNotEmpty(function ($collection) {
        return $collection->push('adam');
    });

    $collection->all();

    // []


    $collection = collect();

    $collection->whenNotEmpty(function($collection) {
        return $collection->push('adam');
    }, function($collection) {
        return $collection->push('taylor');
    });

    $collection->all();

    // ['taylor']

For the inverse of `whenNotEmpty`, see the [`whenEmpty`](#method-whenempty) method.

`whenNotEmpty`의 반대는, [`whenEmpty`](#method-whenempty) 메소드를 확인하십시오.

<a name="method-where"></a>
#### `where()` {#collection-method}
#### `where()` {#collection-method}

The `where` method filters the collection by a given key / value pair:

`where` 메소드는 주어진 키/값 쌍에 대해서 컬렉션을 필터링합니다.

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

The `where` method uses "loose" comparisons when checking item values, meaning a string with an integer value will be considered equal to an integer of the same value. Use the [`whereStrict`](#method-wherestrict) method to filter using "strict" comparisons.

`where` 메소드는 아이템의 값을 확인할 때 타입을 "느슨하게" 비교하기 때문에, 문자형으로 된 정수값이라도 정수형과 동일하다고 판단합니다. "엄격한" 비교를 사용하여 필터링을 하려면 [`whereLoose`](#method-whereloose) 메소드를 사용하십시오.

Optionally, you may pass a comparison operator as the second parameter.

선택적으로, 비교 연산자로 쓰인 두 번째 파라미터를 생략할 수 있습니다.

    $collection = collect([
        ['name' => 'Jim', 'deleted_at' => '2019-01-01 00:00:00'],
        ['name' => 'Sally', 'deleted_at' => '2019-01-02 00:00:00'],
        ['name' => 'Sue', 'deleted_at' => null],
    ]);

    $filtered = $collection->where('deleted_at', '!=', null);

    $filtered->all();

    /*
        [
            ['name' => 'Jim', 'deleted_at' => '2019-01-01 00:00:00'],
            ['name' => 'Sally', 'deleted_at' => '2019-01-02 00:00:00'],
        ]
    */

<a name="method-wherestrict"></a>
#### `whereStrict()` {#collection-method}
#### `whereStrict()` {#collection-method}

This method has the same signature as the [`where`](#method-where) method; however, all values are compared using "strict" comparisons.

이 메소드는 [`where`](#method-where) 메소드와 동일한 사용법을 가지고 있습니다; 하지만 모든 값이 "엄격하게" 비교되어 집니다.(타입까지 일치하는지 체크합니다)

<a name="method-wherebetween"></a>
#### `whereBetween()` {#collection-method}
#### `whereBetween()` {#collection-method}

The `whereBetween` method filters the collection within a given range:

`whereBetween` 메소드는 컬렉션을 주어진 범위안에 해당되는 값으로만 필터링합니다.

    $collection = collect([
        ['product' => 'Desk', 'price' => 200],
        ['product' => 'Chair', 'price' => 80],
        ['product' => 'Bookcase', 'price' => 150],
        ['product' => 'Pencil', 'price' => 30],
        ['product' => 'Door', 'price' => 100],
    ]);

    $filtered = $collection->whereBetween('price', [100, 200]);

    $filtered->all();

    /*
        [
            ['product' => 'Desk', 'price' => 200],
            ['product' => 'Bookcase', 'price' => 150],
            ['product' => 'Door', 'price' => 100],
        ]
    */

<a name="method-wherein"></a>
#### `whereIn()` {#collection-method}
#### `whereIn()` {#collection-method}

The `whereIn` method filters the collection by a given key / value contained within the given array:

`whereIn` 메소드는 주어진 배열 안에 포함된 주어진 키/값을 사용하여 컬렉션을 필터링합니다.

    $collection = collect([
        ['product' => 'Desk', 'price' => 200],
        ['product' => 'Chair', 'price' => 100],
        ['product' => 'Bookcase', 'price' => 150],
        ['product' => 'Door', 'price' => 100],
    ]);

    $filtered = $collection->whereIn('price', [150, 200]);

    $filtered->all();

    /*
        [
            ['product' => 'Bookcase', 'price' => 150],
            ['product' => 'Desk', 'price' => 200],
        ]
    */

The `whereIn` method uses "loose" comparisons when checking item values, meaning a string with an integer value will be considered equal to an integer of the same value. Use the [`whereInStrict`](#method-whereinstrict) method to filter using "strict" comparisons.

`whereIn` 메소드는 아이템 값을 "느슨하게" 비교하기 때문에, 문자형 정수값이더라도 정수형과 동일하다고 판단합니다. "엄격한" 비교를 통해서 필터링 하려면 [`whereInStrict`](#method-whereinstrict) 메소드를 사용하십시오.

<a name="method-whereinstrict"></a>
#### `whereInStrict()` {#collection-method}
#### `whereInStrict()` {#collection-method}

This method has the same signature as the [`whereIn`](#method-wherein) method; however, all values are compared using "strict" comparisons.

이 메소드는 [`whereIn`](#method-wherein) 와 동일합니다만, 모든 값들은 "엄격한" 비교를 진행합니다.

(역자주 : 느슨한 비교와 엄격한 비교는 `==`와 `===`의 차이처럼 타입과 값이 모두 일치하는지 비교하는 정도를 나타냅니다)

<a name="method-whereinstanceof"></a>
#### `whereInstanceOf()` {#collection-method}
#### `whereInstanceOf()` {#collection-method}

The `whereInstanceOf` method filters the collection by a given class type:

`whereInstanceOf` 메소드는 컬렉션을 주어진 클래스 타입으로 필터링합니다.

    $collection = collect([
        new User,
        new User,
        new Post,
    ]);

    return $collection->whereInstanceOf(User::class);

<a name="method-wherenotbetween"></a>
#### `whereNotBetween()` {#collection-method}
#### `whereNotBetween()` {#collection-method}

The `whereNotBetween` method filters the collection within a given range:

`whereNotBetween` 메소드는 주어진 범위 내에서 컬렉션을 필터링합니다.

    $collection = collect([
        ['product' => 'Desk', 'price' => 200],
        ['product' => 'Chair', 'price' => 80],
        ['product' => 'Bookcase', 'price' => 150],
        ['product' => 'Pencil', 'price' => 30],
        ['product' => 'Door', 'price' => 100],
    ]);

    $filtered = $collection->whereNotBetween('price', [100, 200]);

    $filtered->all();

    /*
        [
            ['product' => 'Chair', 'price' => 80],
            ['product' => 'Pencil', 'price' => 30],
        ]
    */

<a name="method-wherenotin"></a>
#### `whereNotIn()` {#collection-method}
#### `whereNotIn()` {#collection-method}

The `whereNotIn` method filters the collection by a given key / value not contained within the given array:

`whereNotIn` 메소드는 컬렉션 안에 주어진 키/값을 포함하지 않은 값들을 필터링합니다.

    $collection = collect([
        ['product' => 'Desk', 'price' => 200],
        ['product' => 'Chair', 'price' => 100],
        ['product' => 'Bookcase', 'price' => 150],
        ['product' => 'Door', 'price' => 100],
    ]);

    $filtered = $collection->whereNotIn('price', [150, 200]);

    $filtered->all();

    /*
        [
            ['product' => 'Chair', 'price' => 100],
            ['product' => 'Door', 'price' => 100],
        ]
    */

The `whereNotIn` method uses "loose" comparisons when checking item values, meaning a string with an integer value will be considered equal to an integer of the same value. Use the [`whereNotInStrict`](#method-wherenotinstrict) method to filter using "strict" comparisons.

`whereNotIn` 메소드는 아이템의 값을 확인할 때 "느슨하게" 비교하기 때문에, 문자형 정수값이더라도 정수형과 동일하다고 판단합니다. (타입을 엄격하게 비교하지 않습니다) "엄격한" 비교를 원한다면, [`whereNotInStrict`](#method-wherenotinstrict) 메소드를 사용하십시오.

<a name="method-wherenotinstrict"></a>
#### `whereNotInStrict()` {#collection-method}
#### `whereNotInStrict()` {#collection-method}

This method has the same signature as the [`whereNotIn`](#method-wherenotin) method; however, all values are compared using "strict" comparisons.

이 메소드의 사용법은 [`whereNotIn`](#method-wherenotin) 메소드와 동일하지만, 모든 값들은 "엄격한" 비교를 수행합니다.

<a name="method-wrap"></a>
#### `wrap()` {#collection-method}
#### `wrap()` {#collection-method}

The static `wrap` method wraps the given value in a collection when applicable:

정적 메소드인 `wrap` 메소드는 가능한 경우, 주어진 값을 컬렉션으로 감쌉니다.

    $collection = Collection::wrap('John Doe');

    $collection->all();

    // ['John Doe']

    $collection = Collection::wrap(['John Doe']);

    $collection->all();

    // ['John Doe']

    $collection = Collection::wrap(collect('John Doe'));

    $collection->all();

    // ['John Doe']

<a name="method-zip"></a>
#### `zip()` {#collection-method}
#### `zip()` {#collection-method}

The `zip` method merges together the values of the given array with the values of the original collection at the corresponding index:

`zip` 메소드는 해당 인덱스의 원래 컬렉션의 값으로 주어진 배열의 값을 함께 합칩니다.

    $collection = collect(['Chair', 'Desk']);

    $zipped = $collection->zip([100, 200]);

    $zipped->all();

    // [['Chair', 100], ['Desk', 200]]

<a name="higher-order-messages"></a>
## Higher Order Messages
## Higher Order Messages

Collections also provide support for "higher order messages", which are short-cuts for performing common actions on collections. The collection methods that provide higher order messages are: [`average`](#method-average), [`avg`](#method-avg), [`contains`](#method-contains), [`each`](#method-each), [`every`](#method-every), [`filter`](#method-filter), [`first`](#method-first), [`flatMap`](#method-flatmap), [`groupBy`](#method-groupby), [`keyBy`](#method-keyby), [`map`](#method-map), [`max`](#method-max), [`min`](#method-min), [`partition`](#method-partition), [`reject`](#method-reject), [`some`](#method-some), [`sortBy`](#method-sortby), [`sortByDesc`](#method-sortbydesc), [`sum`](#method-sum), and [`unique`](#method-unique).

컬렉션은 공통된 작업을 수행하는데 필요한 "higher order message"를 제공합니다. 컬렉션에서 higher order message 가 가능한 메소드들은 [`average`](#method-average), [`avg`](#method-avg), [`contains`](#method-contains), [`each`](#method-each), [`every`](#method-every), [`filter`](#method-filter), [`first`](#method-first), [`flatMap`](#method-flatmap), [`groupBy`](#method-groupby), [`keyBy`](#method-keyby), [`map`](#method-map), [`max`](#method-max), [`min`](#method-min), [`partition`](#method-partition), [`reject`](#method-reject), [`some`](#method-some), [`sortBy`](#method-sortby), [`sortByDesc`](#method-sortbydesc), [`sum`](#method-sum), 그리고 [`unique`](#method-unique) 입니다.

Each higher order message can be accessed as a dynamic property on a collection instance. For instance, let's use the `each` higher order message to call a method on each object within a collection:

각각의 higher order message 는 컬렉션 인스턴스의 동적 속성에 접근할 수 있습니다. 예를 들자면, 컬렉션 안에 있는 각 객체의 메소드를 호출하기 위해서 `each` higher order message 를 사용해보겠습니다.

    $users = User::where('votes', '>', 500)->get();

    $users->each->markAsVip();

Likewise, we can use the `sum` higher order message to gather the total number of "votes" for a collection of users:

마찬가지로 `sum` higher order message를 사용하여 사용자 컬렉션의 전체 "투표" 수를 확인할 수 있습니다.

    $users = User::where('group', 'Development')->get();

    return $users->sum->votes;

<a name="lazy-collections"></a>
## Lazy Collections
## 지연 컬렉션-Lazy Collections

<a name="lazy-collection-introduction"></a>
### Introduction
### 시작하기

> {note} Before learning more about Laravel's lazy collections, take some time to familiarize yourself with [PHP generators](https://www.php.net/manual/en/language.generators.overview.php).

라라벨 지연 컬렉션-lazy collection에 대해 배우기 전에, [PHP generators](https://www.php.net/manual/en/language.generators.overview.php)에 익숙해지는데 시간을 투자하세요. 

To supplement the already powerful `Collection` class, the `LazyCollection` class leverages PHP's [generators](https://www.php.net/manual/en/language.generators.overview.php) to allow you to work with very large datasets while keeping memory usage low.

이미 강력한 `컬렉션` 클래스를 보완하기 위해 `LazyCollection`은 PHP의 [generators](https://www.php.net/manual/en/language.generators.overview.php)를 이용해 메모리 사용량을 적게 유지하면서도 매우 큰 데이터 셋을 처리할 수 있게 해줍니다.

For example, imagine your application needs to process a multi-gigabyte log file while taking advantage of Laravel's collection methods to parse the logs. Instead of reading the entire file into memory at once, lazy collections may be used to keep only a small part of the file in memory at a given time:

예를 들어, 어플리케이션이 로그를 파싱하는 컬렉션 메소드를 활용해서 수 기가바이트의 로그 파일을 처리한다고 생각해 봅시다. 한 번에 전체 파일을 메모리에 읽는 대신, 지연 컬렉션-lazy collection을 이용해, 파일의 극히 일부분만 메모리에 담아놓을 수 있습니다.

    use App\LogEntry;
    use Illuminate\Support\LazyCollection;

    LazyCollection::make(function () {
        $handle = fopen('log.txt', 'r');

        while (($line = fgets($handle)) !== false) {
            yield $line;
        }
    })->chunk(4)->map(function ($lines) {
        return LogEntry::fromLines($lines);
    })->each(function (LogEntry $logEntry) {
        // Process the log entry...
    });

Or, imagine you need to iterate through 10,000 Eloquent models. When using traditional Laravel collections, all 10,000 Eloquent models must be loaded into memory at the same time:

또는 10,000개의 Eloquent 모델을 반복해야 한다고 가정해 봅시다. 전통적인 라라벨 컬렉션을 사용할때는, 10,000개의 Eloquent 모델이 모두 동시에 메모리에 로드되어야 합니다. 

    $users = App\User::all()->filter(function ($user) {
        return $user->id > 500;
    });

However, the query builder's `cursor` method returns a `LazyCollection` instance. This allows you to still only run a single query against the database but also only keep one Eloquent model loaded in memory at a time. In this example, the `filter` callback is not executed until we actually iterate over each user individually, allowing for a drastic reduction in memory usage:

그러나, 쿼리 빌더의 `cursor` 메소드는 `LazyCollection` 인스턴스를 반환합니다. 이렇게 하면 데이터베이스에 단 하나의 쿼리만 실행할 수 있을 뿐만 아니라 한 번에 하나의 Eloquent 모델만 메모리에 로드됩니다. 이 예제에서는 `filter` 콜백이 각각 사용자에 대해 개별적으로 반복할 때까지 실행되지 않으므로, 메모리 사용량이 크게 줄어듭니다.

    $users = App\User::cursor()->filter(function ($user) {
        return $user->id > 500;
    });

    foreach ($users as $user) {
        echo $user->id;
    }

<a name="creating-lazy-collections"></a>
### Creating Lazy Collections
### 지연 컬렉션-Lazy Collections 생성하기

To create a lazy collection instance, you should pass a PHP generator function to the collection's `make` method:

지연 컬렉션-lazy collection 인스턴스를 생성하기 위해서는 PHP 제네레이터 함수를 컬렉션의 `make` 메소드에 전달해야 합니다.

    use Illuminate\Support\LazyCollection;

    LazyCollection::make(function () {
        $handle = fopen('log.txt', 'r');

        while (($line = fgets($handle)) !== false) {
            yield $line;
        }
    });

<a name="the-enumerable-contract"></a>
### The Enumerable Contract
### 열거형 Contract

Almost all methods available on the `Collection` class are also available on the `LazyCollection` class. Both of these classes implement the `Illuminate\Support\Enumerable` contract, which defines the following methods:

`Collection` 클래스에서 사용가능한 거의 모든 메소드들은 `LazyCollection` 클래스에서도 사용할 수 있습니다. 두 클래스 모두 다음에 나오는 메소드들을 정의하는 `Illuminate\Support\Enumerable` contract에 구현되어 있습니다.

<div id="collection-method-list" markdown="1">

[all](#method-all)
[average](#method-average)
[avg](#method-avg)
[chunk](#method-chunk)
[collapse](#method-collapse)
[collect](#method-collect)
[combine](#method-combine)
[concat](#method-concat)
[contains](#method-contains)
[containsStrict](#method-containsstrict)
[count](#method-count)
[countBy](#method-countBy)
[crossJoin](#method-crossjoin)
[dd](#method-dd)
[diff](#method-diff)
[diffAssoc](#method-diffassoc)
[diffKeys](#method-diffkeys)
[dump](#method-dump)
[duplicates](#method-duplicates)
[duplicatesStrict](#method-duplicatesstrict)
[each](#method-each)
[eachSpread](#method-eachspread)
[every](#method-every)
[except](#method-except)
[filter](#method-filter)
[first](#method-first)
[firstWhere](#method-first-where)
[flatMap](#method-flatmap)
[flatten](#method-flatten)
[flip](#method-flip)
[forPage](#method-forpage)
[get](#method-get)
[groupBy](#method-groupby)
[has](#method-has)
[implode](#method-implode)
[intersect](#method-intersect)
[intersectByKeys](#method-intersectbykeys)
[isEmpty](#method-isempty)
[isNotEmpty](#method-isnotempty)
[join](#method-join)
[keyBy](#method-keyby)
[keys](#method-keys)
[last](#method-last)
[macro](#method-macro)
[make](#method-make)
[map](#method-map)
[mapInto](#method-mapinto)
[mapSpread](#method-mapspread)
[mapToGroups](#method-maptogroups)
[mapWithKeys](#method-mapwithkeys)
[max](#method-max)
[median](#method-median)
[merge](#method-merge)
[mergeRecursive](#method-mergerecursive)
[min](#method-min)
[mode](#method-mode)
[nth](#method-nth)
[only](#method-only)
[pad](#method-pad)
[partition](#method-partition)
[pipe](#method-pipe)
[pluck](#method-pluck)
[random](#method-random)
[reduce](#method-reduce)
[reject](#method-reject)
[replace](#method-replace)
[replaceRecursive](#method-replacerecursive)
[reverse](#method-reverse)
[search](#method-search)
[shuffle](#method-shuffle)
[skip](#method-skip)
[slice](#method-slice)
[some](#method-some)
[sort](#method-sort)
[sortBy](#method-sortby)
[sortByDesc](#method-sortbydesc)
[sortKeys](#method-sortkeys)
[sortKeysDesc](#method-sortkeysdesc)
[split](#method-split)
[sum](#method-sum)
[take](#method-take)
[tap](#method-tap)
[times](#method-times)
[toArray](#method-toarray)
[toJson](#method-tojson)
[union](#method-union)
[unique](#method-unique)
[uniqueStrict](#method-uniquestrict)
[unless](#method-unless)
[unlessEmpty](#method-unlessempty)
[unlessNotEmpty](#method-unlessnotempty)
[unwrap](#method-unwrap)
[values](#method-values)
[when](#method-when)
[whenEmpty](#method-whenempty)
[whenNotEmpty](#method-whennotempty)
[where](#method-where)
[whereStrict](#method-wherestrict)
[whereBetween](#method-wherebetween)
[whereIn](#method-wherein)
[whereInStrict](#method-whereinstrict)
[whereInstanceOf](#method-whereinstanceof)
[whereNotBetween](#method-wherenotbetween)
[whereNotIn](#method-wherenotin)
[whereNotInStrict](#method-wherenotinstrict)
[wrap](#method-wrap)
[zip](#method-zip)

</div>

> {note} Methods that mutate the collection (such as `shift`, `pop`, `prepend` etc.) are are _not_ available on the `LazyCollection` class.

> {note} `shift`, `pop`, `prepend` 등과 같이 컬렉션을 변형시키는 메소드들은 `LazyCollection` 클래스 에서 사용할 수 없습니다.

<a name="lazy-collection-methods"></a>
### Lazy Collection Methods
### Lazy Collection 메소드

In addition to the methods defined in the `Enumerable` contract, the `LazyCollection` class contains the following methods:

`Enumerable` contract에 정의된 메소드 외에, `LazyCollection` 클래스는 추가적으로 다음 메소드를 포함합니다.

<a name="method-tapEach"></a>
#### `tapEach()` {#collection-method}
#### `tapEach()` {#collection-method}

While the `each` method calls the given callback for each item in the collection right away, the `tapEach` method only calls the given callback as the items are being pulled out of the list one by one:

`each` 메소드는 각 항목에 대한 콜백을 즉시 호출하는 반면, `tabEach` 메소드는 목록에서 하나씩 빼내어지는 항목에 대해서만 주어진 콜백을 호출합니다.

    $lazyCollection = LazyCollection::times(INF)->tapEach(function ($value) {
        dump($value);
    });

    // Nothing has been dumped so far...

    $array = $lazyCollection->take(3)->all();

    // 1
    // 2
    // 3