# 컬렉션

- [소개](#introduction)
    - [컬렉션 생성하기](#creating-collections)
- [사용 가능한 메소드](#available-methods)

<a name="introduction"></a>
## 소개

`Illuminate\Support\Collection` 클래스는 배열 데이터를 사용하기위한 유연하고 편리한 래퍼(wrapper)를 제공합니다. 다음의 사용예제를 확인하십시오. `collect` 헬퍼 함수를 사용하여 배열에서 새로운 collection 인스턴스를 생성하고 모든 요소들을 대문자로 변경한 다음 비어있는 요소들을 제거하는 예제 입니다:

    $collection = collect(['taylor', 'abigail', null])->map(function ($name) {
        return strtoupper($name);
    })
    ->reject(function ($name) {
        return empty($name);
    });

보시는 바와 같이 `Collection` 클래스는 map 매소드와 reject메소드를 체이닝 방식으로 사용할 수 있게 해줍니다. 다시 말해서 컬렉션은 불변하며(immutable) 모든 `Collection`의 메소드는 `Collection`의 인스턴스를 반환합니다. 

<a name="creating-collections"></a>
### 컬렉션 생성하기

앞서 설명한 대로 `collect` 헬퍼 함수는 주어진 배열으로 부터 `Illuminate\Support\Collection` 인스턴스를 반환합니다. 따라서 컬렉션을 생성하는 것은 보시는 바와 같이 아주 간단합니다. 

    $collection = collect([1, 2, 3]);

기본적으로 [Eloquent](/docs/5.1/eloquent) 모델의 컬렉션은 항상 `Collection` 인스턴스를 반환합니다. 꼭 여기가 아니더라도 애플리케이션의 어느곳이라도 편리하게 `Collection` 클래스를 이용할 수 있습니다.

<a name="available-methods"></a>
## 사용가능한 메소드

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

`all` 메소드는 주어진 배열을 컬렉션으로 되돌려 줍니다: 

    collect([1, 2, 3])->all();

    // [1, 2, 3]

<a name="method-average"></a>
#### `average()` {#collection-method}

[`avg`](#method-avg) 메소드의 별칭입니다.

<a name="method-avg"></a>
#### `avg()` {#collection-method}

`avg` 메소드는 주어진 키의 [평균값](https://en.wikipedia.org/wiki/Average)을 반환합니다: 

    $average = collect([['foo' => 10], ['foo' => 10], ['foo' => 20], ['foo' => 40]])->avg('foo');

    // 20

    $average = collect([1, 1, 2, 4])->avg();

    // 2

<a name="method-chunk"></a>
#### `chunk()` {#collection-method}

`chunk` 메소드는 컬렉션을 주어진 사이즈의 더 작은 여러개로 분할합니다.

    $collection = collect([1, 2, 3, 4, 5, 6, 7]);

    $chunks = $collection->chunk(4);

    $chunks->toArray();

    // [[1, 2, 3, 4], [5, 6, 7]]

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

`collapse` 메소드는 배열의 컬렉션을 하나의 컬렉션으로 만듭니다:

    $collection = collect([[1, 2, 3], [4, 5, 6], [7, 8, 9]]);

    $collapsed = $collection->collapse();

    $collapsed->all();

    // [1, 2, 3, 4, 5, 6, 7, 8, 9]

<a name="method-contains"></a>
#### `contains()` {#collection-method}

`contains` 메소드는 컬렉션에 주어진 아이템을 포함하고 있는지 알려줍니다: 

    $collection = collect(['name' => 'Desk', 'price' => 100]);

    $collection->contains('Desk');

    // true

    $collection->contains('New York');

    // false

또한 주어진 키/값 쌍이 컬렉션 안에 존재하는지 확인할 수 있도록 `container` 메소드에 키/값 쌍을 인자로 전달할 수도 있습니다:

    $collection = collect([
        ['product' => 'Desk', 'price' => 200],
        ['product' => 'Chair', 'price' => 100],
    ]);

    $collection->contains('product', 'Bookcase');

    // false

마지막으로, `contains` 메소드에 주어진 조건을 판별할 수 있는 콜백을 인자로 전달할 수도 있습니다:

    $collection = collect([1, 2, 3, 4, 5]);

    $collection->contains(function ($key, $value) {
        return $value > 5;
    });

    // false

`contains` 메소드는 아이템의 값을 비교할 때 "느슨한" 비교를 수행하기 때문에, 정수값이 문자형일 때에도 정수형 값과 동일하다고 판단합니다.

<a name="method-count"></a>
#### `count()` {#collection-method}

`count` 메소드는 컬렉션안에 존재하는 아이템의 전체 갯수를 반환합니다:

    $collection = collect([1, 2, 3, 4]);

    $collection->count();

    // 4

<a name="method-diff"></a>
#### `diff()` {#collection-method}

`diff` 메소드는 컬렉션을 다른 컬렉션 또는 일반적인 PHP `배열`을 값을 기준으로 비교합니다. 이 메소드는 주어진 컬렉션 안에 들어 있지 않은 원래의 컬렉션 안에 있는 값을 반환합니다:

    $collection = collect([1, 2, 3, 4, 5]);

    $diff = $collection->diff([2, 4, 6, 8]);

    $diff->all();

    // [1, 3, 5]

<a name="method-each"></a>
#### `each()` {#collection-method}

`each` 메소드는 컬렉션의 아이템을 반복적으로 처리하여 콜백에 각 아이템을 전달합니다:

    $collection = $collection->each(function ($item, $key) {
        //
    });

전체 항목에 대한 반복을 중지하려면, 콜백 안에서 `false` 를 반환하면 됩니다:

    $collection = $collection->each(function ($item, $key) {
        if (/* some condition */) {
            return false;
        }
    });

<a name="method-every"></a>
#### `every()` {#collection-method}

`every` 메소드는 매 n 번째 존재하는 요소로 구성된 새로운 컬렉션을 생성합니다. 

(역자주: 첫번째 인자로 주어진 숫자로 나누어 나머지가 0인 경우의 아이템들로 구성된 컬렉션이 반환됩니다. 인덱스는 0부터 시작)

    $collection = collect(['a', 'b', 'c', 'd', 'e', 'f']);

    $collection->every(4);

    // ['a', 'e']

두번째 인자로 offset을 전달할 수도 있습니다:

    $collection->every(4, 1);

    // ['b', 'f']

<a name="method-except"></a>
#### `except()` {#collection-method}

`except` 메소드는 지정된 키에 대한 아이템을 제외한 컬렉션의 모든 아이템을 반환합니다: 

    $collection = collect(['product_id' => 1, 'price' => 100, 'discount' => false]);

    $filtered = $collection->except(['price', 'discount']);

    $filtered->all();

    // ['product_id' => 1]

For the inverse of `except`, see the [only](#method-only) method.

`except` 의 반대는, [only](#method-only) 메소드를 확인하십시오.

<a name="method-filter"></a>
#### `filter()` {#collection-method}

`filter` 메소드는 주어진 콜백을 사용하여 컬렉션을 필터링하고, 주어진 테스트에 true를 반환하는 항목 만 남겨둡니다:

    $collection = collect([1, 2, 3, 4]);

    $filtered = $collection->filter(function ($value) {
        return $value > 2;
    });

    $filtered->all();

    // [3, 4]

콜백이 전달되지 않는다면, 모든 컬렉션의 엔트리 중에서 `false` 로 인식되는 값들은 제거됩니다:

    $collection = collect([1, 2, 3, null, false, '', 0, []]);

    $collection->filter()->all();

    // [1, 2, 3]

`filter` 의 반대는 [reject](#method-reject) 메소드를 확인하십시오.

<a name="method-first"></a>
#### `first()` {#collection-method}

`first` 메소드는 주어진 테스트를 통과하는 컬렉션의 첫 번째 요소를 반환합니다.

    collect([1, 2, 3, 4])->first(function ($key, $value) {
        return $value > 2;
    });

    // 3

`first` 메소드를 아무런 인자 없이 호출하여 컬렉션의 첫 번째 요소를 획득할 수 있습니다. 만약 컬렉션이 비어 있다면 `null` 이 반환됩니다.

    collect([1, 2, 3, 4])->first();

    // 1

<a name="method-flatmap"></a>
#### `flatMap()` {#collection-method}

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

`flatten` 메소드는 다차원으로 구성된 컬렉션을 한 차원으로 변경시킵니다.

    $collection = collect(['name' => 'taylor', 'languages' => ['php', 'javascript']]);

    $flattened = $collection->flatten();

    $flattened->all();

    // ['taylor', 'php', 'javascript'];

<a name="method-flip"></a>
#### `flip()` {#collection-method}

`flip` 메소드는 컬렉션의 키와 대응하는 값을 바꿉니다.

    $collection = collect(['name' => 'taylor', 'framework' => 'laravel']);

    $flipped = $collection->flip();

    $flipped->all();

    // ['taylor' => 'name', 'laravel' => 'framework']

<a name="method-forget"></a>
#### `forget()` {#collection-method}

`forget` 메소드는 키에 해당하는 컬렉션의 아이템을 삭제합니다.

    $collection = collect(['name' => 'taylor', 'framework' => 'laravel']);

    $collection->forget('name');

    $collection->all();

    // ['framework' => 'laravel']

> **주의:** 다른 컬렉션 메소드와는 달리 `forget`는 수정된 새로운 컬렉션을 반환하지 않습니다; 호출된 컬렉션을 변경합니다.

<a name="method-forpage"></a>
#### `forPage()` {#collection-method}

`forPage` 메소드는 주어진 페이지 넘버에 해당하는 아이템을 가지고 있는 새로운 컬렉션을 반환합니다. 이 메소드는 페이지 번호를 첫번째 인자로 페이지별로 보여주고자 하는 아이템의 갯수를 두번째 인자로 받아 들입니다: 

    $collection = collect([1, 2, 3, 4, 5, 6, 7, 8, 9]);

    $chunk = $collection->forPage(2, 3);

    $chunk->all();

    // [4, 5, 6]

<a name="method-get"></a>
#### `get()` {#collection-method}

`get` 메소드는 주어진 키에 대한 아이템을 반환합니다. 키가 존재하지 않느다면, `null` 이 반환됩니다:

    $collection = collect(['name' => 'taylor', 'framework' => 'laravel']);

    $value = $collection->get('name');

    // taylor

두번째 인자로 기본값을 전달할 수도 있습니다:

    $collection = collect(['name' => 'taylor', 'framework' => 'laravel']);

    $value = $collection->get('foo', 'default-value');

    // default-value

기본값을 결정하는 콜백을 전달할 수도 있습니다. 지정된 키가 존재하지 않는 경우에 콜백의 결과가 반환될 것입니다: 

    $collection->get('email', function () {
        return 'default-value';
    });

    // default-value

<a name="method-groupby"></a>
#### `groupBy()` {#collection-method}

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

`has` 메소드는 주어진 키가 컬렉션 안에 존재하는지 알려줍니다:

    $collection = collect(['account_id' => 1, 'product' => 'Desk']);

    $collection->has('product');

    // true

<a name="method-implode"></a>
#### `implode()` {#collection-method}

`implode` 메소드는 컬렉션 안의 아이템들을 합쳐줍니다. 이 메소드의 인자는 컬렉션 안에 있는 아이템들의 타입에 의존합니다. 컬렉션이 배열 또는 객체를 가지고 있다면, 합치고자 하는 속성의 키와 값들 사이에 끼워 넣고자 하는 "glue" 문자열을 전달해야 합니다:

    $collection = collect([
        ['account_id' => 1, 'product' => 'Desk'],
        ['account_id' => 2, 'product' => 'Chair'],
    ]);

    $collection->implode('product', ', ');

    // Desk, Chair

컬렉션이 간단한 문자열 또는 숫자값을 가지고 있다면, 간단하게 "glue" 를 첫번째 인자로 전달하면 됩니다:

    collect([1, 2, 3, 4, 5])->implode('-');

    // '1-2-3-4-5'

<a name="method-intersect"></a>
#### `intersect()` {#collection-method}

`intersect` 메소드는 원래의 컬렉션에서 주어진 `배열` 또는 컬렉션에 존재하지 않는 값을 제거합니다. 메소드를 호출한 뒤에도 원래 컬렉션의 키 값은 보존됩니다:

    $collection = collect(['Desk', 'Sofa', 'Chair']);

    $intersect = $collection->intersect(['Desk', 'Chair', 'Bookcase']);

    $intersect->all();

    // [0 => 'Desk', 2 => 'Chair']

<a name="method-isempty"></a>
#### `isEmpty()` {#collection-method}

`isEmpty` 메소드는 컬렉션이 비어 있는 경우 `true` 를 반환하고, 그렇지 않은 경우 `false` 를 반환합니다:

    collect([])->isEmpty();

    // true

<a name="method-keyby"></a>
#### `keyBy()` {#collection-method}

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

`last` 메소드는 주어진 콜백에서 참이되는 값중에 가장 마지막 값을 반환합니다:

    collect([1, 2, 3, 4])->last(function ($key, $value) {
        return $value < 3;
    });

    // 2

`last` 메소드를 인자 없이 호출하여 컬렉션의 가장 마지막 값을 획득할 수도 있습니다. 컬렉션이 비어 있다면, `null` 이 반환됩니다:

    collect([1, 2, 3, 4])->last();

    // 4

<a name="method-map"></a>
#### `map()` {#collection-method}

`map` 메소드는 컬렉션 전체를 반복하여 주어진 콜백에 각각의 값을 전달합니다. 콜백은 자유롭게 아이템을 변경하고 반환하여, 변경된 아이템으로 구성된 새로운 컬렉션이 만들어 집니다. 

    $collection = collect([1, 2, 3, 4, 5]);

    $multiplied = $collection->map(function ($item, $key) {
        return $item * 2;
    });

    $multiplied->all();

    // [2, 4, 6, 8, 10]

> **참고:** 대다수의 다른 컬렉션 메소드와 같이, `map` 메소드는 새로운 컬렉션 인스턴스를 반환합니다; 이 메소드는 호출된 컬렉션을 변경하지 않습니다. 원래의 컬렉션을 변경하고자 한다면 [`transform`](#method-transform)메소드를 사용하십시오.

<a name="method-max"></a>
#### `max()` {#collection-method}

`max` 메소드는 주어진 키에 해당하는 최대 값을 반환합니다: 

    $max = collect([['foo' => 10], ['foo' => 20]])->max('foo');

    // 20

    $max = collect([1, 2, 3, 4, 5])->max();

    // 5

<a name="method-merge"></a>
#### `merge()` {#collection-method}

`merge` 메소드는 주어진 배열 또는 컬렉션을 원래의 컬렉션과 합칩니다. 배열 안에 들어 있는 키가 컬렉션에 들어 있는 키와 일치한다면, 주어진 배열의 값이 원래의 컬렉션 안의 값을 덮어 씁니다:

    $collection = collect(['product_id' => 1, 'price' => 100]);

    $merged = $collection->merge(['price' => 200, 'discount' => false]);

    $merged->all();

    // ['product_id' => 1, 'price' => 200, 'discount' => false]

만약 주어진 아이템의 키가 숫자라면, 이 값들은 컬렉션의 가장 마지막에 추가됩니다:

    $collection = collect(['Desk', 'Chair']);

    $merged = $collection->merge(['Bookcase', 'Door']);

    $merged->all();

    // ['Desk', 'Chair', 'Bookcase', 'Door']

<a name="method-min"></a>
#### `min()` {#collection-method}

`min` 메소드는 주어진 키에 대한 최소 값을 반환합니다:

    $min = collect([['foo' => 10], ['foo' => 20]])->min('foo');

    // 10

    $min = collect([1, 2, 3, 4, 5])->min();

    // 1

<a name="method-only"></a>
#### `only()` {#collection-method}

`only` 메소드는 컬렉션 안에서 지정된 키에 대한 아이템만을 반환합니다. 

    $collection = collect(['product_id' => 1, 'name' => 'Desk', 'price' => 100, 'discount' => false]);

    $filtered = $collection->only(['product_id', 'name']);

    $filtered->all();

    // ['product_id' => 1, 'name' => 'Desk']

`only` 메소드의 반대는, [except](#method-except)메소드를 확인하십시오.

<a name="method-pluck"></a>
#### `pluck()` {#collection-method}

`pluck` 메소드는 주어진 키에 대한 모든 값을 반환합니다:

    $collection = collect([
        ['product_id' => 'prod-100', 'name' => 'Desk'],
        ['product_id' => 'prod-200', 'name' => 'Chair'],
    ]);

    $plucked = $collection->pluck('name');

    $plucked->all();

    // ['Desk', 'Chair']

또한 컬렉션에서 키로 지정하고자 하는 값을 지정할 수도 있습니다:

    $plucked = $collection->pluck('name', 'product_id');

    $plucked->all();

    // ['prod-100' => 'Desk', 'prod-200' => 'Chair']

<a name="method-pop"></a>
#### `pop()` {#collection-method}

`pop` 메소드는 컬렉션의 마지막 값을 반환하고 제거합니다:

    $collection = collect([1, 2, 3, 4, 5]);

    $collection->pop();

    // 5

    $collection->all();

    // [1, 2, 3, 4]

<a name="method-prepend"></a>
#### `prepend()` {#collection-method}

`prepend` 메소드는 컬렉션의 앞 부분에 아이템을 추가합니다:

    $collection = collect([1, 2, 3, 4, 5]);

    $collection->prepend(0);

    $collection->all();

    // [0, 1, 2, 3, 4, 5]

또한 두번째 인자로 앞에 붙이고자 하는 아이템의 키를 설정할 수도 있습니다:

    $collection = collect(['one' => 1, 'two' => 2]);

    $collection->prepend(0, 'zero');

    $collection->all();

    // ['zero' => 0, 'one' => 1, 'two' => 2]

<a name="method-pull"></a>
#### `pull()` {#collection-method}

`pull` 메소드는 주어진 키에 해당하는 아이템을 반환하고 삭제합니다:

    $collection = collect(['product_id' => 'prod-100', 'name' => 'Desk']);

    $collection->pull('name');

    // 'Desk'

    $collection->all();

    // ['product_id' => 'prod-100']

<a name="method-push"></a>
#### `push()` {#collection-method}

`push` 메소드는 컬렉션의 마지막에 아이템을 추가합니다:

    $collection = collect([1, 2, 3, 4]);

    $collection->push(5);

    $collection->all();

    // [1, 2, 3, 4, 5]

<a name="method-put"></a>
#### `put()` {#collection-method}

`put` 메소드는 주어진 키와 값을 컬렉션에 추가합니다: 

    $collection = collect(['product_id' => 1, 'name' => 'Desk']);

    $collection->put('price', 100);

    $collection->all();

    // ['product_id' => 1, 'name' => 'Desk', 'price' => 100]

<a name="method-random"></a>
#### `random()` {#collection-method}

`random` 메소드는 컬렉션의 아이템중 하나를 랜덤하게 반환합니다:

    $collection = collect([1, 2, 3, 4, 5]);

    $collection->random();

    // 4 - (retrieved randomly)

얼마나 많은 아이템을 랜덤으로 조회할지 `random` 메소드에 선택적으로 정수값을 전달할 수 있습니다. 정수 값이 `1` 보다 크다면 컬렉션의 아이템 들이 반환 됩니다:

    $random = $collection->random(3);

    $random->all();

    // [2, 4, 5] - (retrieved randomly)

<a name="method-reduce"></a>
#### `reduce()` {#collection-method}

`reduce` 메소드는 각각의 인자를 반복하여 다음 반복에 결과를 전달하면서 컬렉션을 하나의 값으로 줄입니다. 

    $collection = collect([1, 2, 3]);

    $total = $collection->reduce(function ($carry, $item) {
        return $carry + $item;
    });

    // 6

첫번째 반복에서 `$carry` 의 값은 null 입니다; 그러나 초기 값을 지정하고자 하는 경우에 `reduce` 의 두번째 인자로 전달할 수 있습니다. 

    $collection->reduce(function ($carry, $item) {
        return $carry + $item;
    }, 4);

    // 10

<a name="method-reject"></a>
#### `reject()` {#collection-method}

`reject` 메소드는 컬렉션에서 지정된 콜백을 사용하여 필터링을 합니다. 콜백은 결과 컬렉션에서 제거되어야 하는 아이템의 경우 `true`를 반환해야합니다:

    $collection = collect([1, 2, 3, 4]);

    $filtered = $collection->reject(function ($value) {
        return $value > 2;
    });

    $filtered->all();

    // [1, 2]

`reject` 메소드의 반대는, [`filter`](#method-filter) 메소드를 확인하십시오. 

<a name="method-reverse"></a>
#### `reverse()` {#collection-method}

`reverse` 메소드는 컬렉션의 아이템 순서를 반대로 합니다:

    $collection = collect([1, 2, 3, 4, 5]);

    $reversed = $collection->reverse();

    $reversed->all();

    // [5, 4, 3, 2, 1]

<a name="method-search"></a>
#### `search()` {#collection-method}

`search` 메소드는 주어진 값이 컬렉션에 있는지 찾아서 해당 값의 키를 반환합니다. 아이템을 찾지 못한 경우, `false` 가 반환됩니다.
    $collection = collect([2, 4, 6, 8]);

    $collection->search(4);

    // 1

검색은 "느슨한" 비교로 (타입을 엄격하게 비교하지 않습니다) 다시말해 문자열과 정수값의 경우 동일한 값이라면 같다고 판단합니다. 타입에 일치하는 "엄격한" 비교를 수행하려면 `true`를 메소드의 두 번째 인자로 전달하면 됩니다:

    $collection->search('4', true);

    // false

다른 방법으로, 검색 콜백을 전달하여 콜백을 통과 하는 첫 번째 아이템을 획득할 수도 있습니다.

    $collection->search(function ($item, $key) {
        return $item > 5;
    });

    // 2

<a name="method-shift"></a>
#### `shift()` {#collection-method}

`shift` 메소드는 컬렉션에서 첫 번째 아이템을 제거하고 해당 값을 반환합니다.

    $collection = collect([1, 2, 3, 4, 5]);

    $collection->shift();

    // 1

    $collection->all();

    // [2, 3, 4, 5]

<a name="method-shuffle"></a>
#### `shuffle()` {#collection-method}

`shuffle` 메소드는 컬렉션의 아이템을 랜덤하게 섞어버립니다.

    $collection = collect([1, 2, 3, 4, 5]);

    $shuffled = $collection->shuffle();

    $shuffled->all();

    // [3, 2, 5, 1, 4] - (generated randomly)

<a name="method-slice"></a>
#### `slice()` {#collection-method}

`slice` 메소드는 주어진 인덱스에서 시작하는 컬렉션으로 잘라냅니다.

    $collection = collect([1, 2, 3, 4, 5, 6, 7, 8, 9, 10]);

    $slice = $collection->slice(4);

    $slice->all();

    // [5, 6, 7, 8, 9, 10]

만약 반환되는 컬렉션의 사이즈를 제한하고자 한다면, 메소드의 두번재 인자로 사이즈를 전달하면 됩니다:

    $slice = $collection->slice(4, 2);

    $slice->all();

    // [5, 6]

반환되는 컬렉션은 새로운 인덱스 키로 구성되어 있습니다. 만약 이전의 원래 키를 유지하려면, 메소드의 세번째 인자로 `true` 를 전달하십시오.

<a name="method-sort"></a>
#### `sort()` {#collection-method}

`sort` 메소드는 컬렉션을 정렬합니다. 정렬된 컬렉션은 원래의 배열 키를 유지 하기 때문에, 다음 예제에서 연속된 숫자 인덱스를 리셋하기 위해서 [`values`](#method-values) 메소드를 사용할 것입니다:

    $collection = collect([5, 3, 1, 2, 4]);

    $sorted = $collection->sort();

    $sorted->values()->all();

    // [1, 2, 3, 4, 5]

보다 복잡한 정렬이 필요하다면, `sort` 메소드에 여러분의 고유한 알고리즘을 위한 콜백을 전달할 수 있습니다. 컬렉션의 `sort` 메소드가 호출될 때의 동작은 [`usort`](https://secure.php.net/manual/en/function.usort.php#refsect1-function.usort-parameters) PHP 문서를 참고하십시오.

만약 중첩된 배열이나 객체의 컬렉션을 정렬할 필요가 있다면, [`sortBy`](#method-sortby) 또는 [`sortByDesc`](#method-sortbydesc) 메소드를 확인하십시오.

<a name="method-sortby"></a>
#### `sortBy()` {#collection-method}

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

이 메소드의 사용법은 [`sortBy`](#method-sortby) 메소드와 동일하지만, 반대의 순서로 컬렉션을 정렬합니다.

<a name="method-splice"></a>
#### `splice()` {#collection-method}

`splice` 메소드는 지정된 인덱스에서 시작하여 아이템을 잘라내고 제거하여 반환합니다.

    $collection = collect([1, 2, 3, 4, 5]);

    $chunk = $collection->splice(2);

    $chunk->all();

    // [3, 4, 5]

    $collection->all();

    // [1, 2]

반환되는 결과의 사이즈를 제한하기 위해서 두번째 인자로 전달할 수 있습니다:

    $collection = collect([1, 2, 3, 4, 5]);

    $chunk = $collection->splice(2, 1);

    $chunk->all();

    // [3]

    $collection->all();

    // [1, 2, 4, 5]

추가적으로, 컬렉션에서 아이템을 제거하고, 대체할 새로운 아이템을 세번째 인자로 전달할 수 있습니다:

    $collection = collect([1, 2, 3, 4, 5]);

    $chunk = $collection->splice(2, 1, [10, 11]);

    $chunk->all();

    // [3]

    $collection->all();

    // [1, 2, 10, 11, 4, 5]

<a name="method-sum"></a>
#### `sum()` {#collection-method}

`sum` 메소드는 컬렉션 안에 있는 모든 아이템들의 합을 반환합니다:

    collect([1, 2, 3, 4, 5])->sum();

    // 15

컬렉션이 중첩된 배열이나 객체를 가지고 있다면, 어떤 값을 더해야할지 결정하는 키를 전달해야합니다:

    $collection = collect([
        ['name' => 'JavaScript: The Good Parts', 'pages' => 176],
        ['name' => 'JavaScript: The Definitive Guide', 'pages' => 1096],
    ]);

    $collection->sum('pages');

    // 1272

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

`take` 메소드는 지정된 숫자의 아이템으로 이루어진 새로운 컬렉션을 반환합니다:

    $collection = collect([0, 1, 2, 3, 4, 5]);

    $chunk = $collection->take(3);

    $chunk->all();

    // [0, 1, 2]

컬렉션의 끝에서 부터 가져올 아이템의 갯수를 지정하기 위해서 음수를 전달할수도 있습니다. 

    $collection = collect([0, 1, 2, 3, 4, 5]);

    $chunk = $collection->take(-2);

    $chunk->all();

    // [4, 5]

<a name="method-toarray"></a>
#### `toArray()` {#collection-method}

`toArray` 메소드는 컬렉션을 PHP `배열`로 변환합니다. 컬렉션의 값이 [Eloquent](/docs/{{version}}/eloquent) 이라면, 이 모델또한 배열로 변환될 것입니다:

    $collection = collect(['name' => 'Desk', 'price' => 200]);

    $collection->toArray();

    /*
        [
            ['name' => 'Desk', 'price' => 200],
        ]
    */

> **주의:** `toArray` 는 또한 모든 컬렉션의 중첩된 객체도 배열로 변환할 것입니다. 근본적인 raw 배열을 얻기를 원한다면 [`all`](#method-all) 메소드를 대신 사용하십시오.

<a name="method-tojson"></a>
#### `toJson()` {#collection-method}

`toJson` 메소드는 모든 컬렉션을 JSON serialized 문자열으로 변환합니다:

    $collection = collect(['name' => 'Desk', 'price' => 200]);

    $collection->toJson();

    // '{"name":"Desk", "price":200}'

<a name="method-transform"></a>
#### `transform()` {#collection-method}

`transform` 메소드는 컬렉션을 반복하여 컬렉션의 각각의 아이템에 대해서 주어진 콜백을 호출합니다. 컬렉션 안에 있는 아이템들은 자동으로 콜백의 결과로 대체될 것입니다.:

    $collection = collect([1, 2, 3, 4, 5]);

    $collection->transform(function ($item, $key) {
        return $item * 2;
    });

    $collection->all();

    // [2, 4, 6, 8, 10]

> **주의:** 다른 컬렉션 메소드와 다르게, `transform` 메소드는 컬렉션 자신을 변경합니다. 대신에, 새로운 컬렉션을 생성하려면 [`map`](#method-map) 메소드를 사용하십시오.

<a name="method-unique"></a>
#### `unique()` {#collection-method}

`unique` 메소드는 컬렉션 안에서 유니크한 모든 아이템들을 반환합니다. 반환된 컬렉션은 원래의 배열 키를 유지 하기 때문에, 다음 예제에서 연속된 숫자 인덱스를 리셋하기 위해서 [`values`](#method-values) 메소드를 사용할 것입니다:

    $collection = collect([1, 1, 2, 2, 3, 4, 2]);

    $unique = $collection->unique();

    $unique->values()->all();

    // [1, 2, 3, 4]

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

`unique` 메소드는 아이템의 값을 비교할 때 "느슨한" 비교를 수행하기 때문에, 정수값이 문자형일 때에도 정수형 값과 동일하다고 판단합니다.

<a name="method-values"></a>
#### `values()` {#collection-method}

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

`where` 메소드는 아이템의 값을 확인할 때 타입을 "엄격하게" 비교를 수행하기 때문에, 문자형으로 된 정수값과 정수형은과 동일하다고 판단하지 않습니다. "느슨한" 비교를 사용하여 필터링을 하려면 [`whereLoose`](#method-whereloose) 메소드를 사용하십시오.

<a name="method-whereloose"></a>
#### `whereLoose()` {#collection-method}

이 메소드는 [`where`](#method-where) 메소드와 동일한 사용법을 가지고 있습니다;하지만 모든 값이 "느슨하게" 비교되어 집니다.

<a name="method-zip"></a>
#### `zip()` {#collection-method}

`zip` 메소드는 해당 인덱스의 원래 컬렉션의 값으로 주어진 배열의 값을 함께 합칩니다:

    $collection = collect(['Chair', 'Desk']);

    $zipped = $collection->zip([100, 200]);

    $zipped->all();

    // [['Chair', 100], ['Desk', 200]]
