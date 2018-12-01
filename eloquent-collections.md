# Eloquent: Collections

- [시작하기](#introduction)
- [사용 가능한 메소드들](#available-methods)
- [커스텀-사용자 정의 Collections](#custom-collections)

<a name="introduction"></a>
## 시작하기

`get` 메소드를 통하거나 하나의 `relationship-관계`를 통해서 Eloquent로 부터 반환되는 모든 멀티 레코드 결과는 `Illuminate\Database\Eloquent\Collection` 객체의 인스턴스가 됩니다. Eloquent 컬렉션 객체는 라라벨의 [base collection](/docs/{{version}}/collections)을 상속받기 때문에, 자연스럽게 ELoquent 모델에 대한 결과에서 다양하고 편리한 메소드들을 사용할 수 있습니다. 

또한 당연하게도, 모든 컬렉션은 Iterators(반복자)이기 때문에, 간단한 PHP 배열과 같이 반복문 안에서 사용할 수도 있습니다: 

    $users = App\User::where('active', 1)->get();

    foreach ($users as $user) {
        echo $user->name;
    }

하지만, 컬렉션은 배열보다 강력하며, 직관적인 인터페이스를 사용하여 메소드 체인이 가능한 map / reduce 메소드를 사용 할 수 있습니다. 예를 들어 사용자 중에서 비활성화된 사용자를 제외하고 남은 사용자들의 이름을 확인하려면 다음과 같이 하면 됩니다:

    $users = App\User::all();

    $names = $users->reject(function ($user) {
        return $user->active === false;
    })
    ->map(function ($user) {
        return $user->name;
    });

> {note} 대부분의 Eloquent 컬렉션 메소드는 새로운 Eloquent 컬렉션 인스턴스를 반환하고, `pluck`, `keys`, `zip`, `collapse`, `flatten` 그리고 `flip` 메소드는 [기본 컬렉션](/docs/{{version}}/collections) 인스턴스를 반환합니다. 또한 만약 `map` 실행이 Eloquent 모델을 포함하지 않는 컬렉션을 반환한다면, 자동으로 기본 컬렉션으로 캐스팅 될 것입니다.

<a name="available-methods"></a>
## 사용가능한 메소드들 

### 기본 컬렉션

모든 Eloquent 컬렉션은 [Laravel collection](/docs/{{version}}/collections) 객체를 상속받습니다; 따라서 라라벨의 기본 컬렉션 클래스의 모든 강력한 메소드들을 상속받습니다. 

<style>
    #collection-method-list > p {
        column-count: 3; -moz-column-count: 3; -webkit-column-count: 3;
        column-gap: 2em; -moz-column-gap: 2em; -webkit-column-gap: 2em;
    }

    #collection-method-list a {
        display: block;
    }
</style>

- [all](/docs/{{version}}/collections#method-all)
- [average](/docs/{{version}}/collections#method-average)
- [avg](/docs/{{version}}/collections#method-avg)
- [chunk](/docs/{{version}}/collections#method-chunk)
- [collapse](/docs/{{version}}/collections#method-collapse)
- [combine](/docs/{{version}}/collections#method-combine)
- [concat](/docs/{{version}}/collections#method-concat)
- [contains](/docs/{{version}}/collections#method-contains)
- [containsStrict](/docs/{{version}}/collections#method-containsstrict)
- [count](/docs/{{version}}/collections#method-count)
- [crossJoin](/docs/{{version}}/collections#method-crossjoin)
- [dd](/docs/{{version}}/collections#method-dd)
- [diff](/docs/{{version}}/collections#method-diff)
- [diffKeys](/docs/{{version}}/collections#method-diffkeys)
- [dump](/docs/{{version}}/collections#method-dump)
- [each](/docs/{{version}}/collections#method-each)
- [eachSpread](/docs/{{version}}/collections#method-eachspread)
- [every](/docs/{{version}}/collections#method-every)
- [except](/docs/{{version}}/collections#method-except)
- [filter](/docs/{{version}}/collections#method-filter)
- [first](/docs/{{version}}/collections#method-first)
- [flatMap](/docs/{{version}}/collections#method-flatmap)
- [flatten](/docs/{{version}}/collections#method-flatten)
- [flip](/docs/{{version}}/collections#method-flip)
- [forget](/docs/{{version}}/collections#method-forget)
- [forPage](/docs/{{version}}/collections#method-forpage)
- [get](/docs/{{version}}/collections#method-get)
- [groupBy](/docs/{{version}}/collections#method-groupby)
- [has](/docs/{{version}}/collections#method-has)
- [implode](/docs/{{version}}/collections#method-implode)
- [intersect](/docs/{{version}}/collections#method-intersect)
- [isEmpty](/docs/{{version}}/collections#method-isempty)
- [isNotEmpty](/docs/{{version}}/collections#method-isnotempty)
- [keyBy](/docs/{{version}}/collections#method-keyby)
- [keys](/docs/{{version}}/collections#method-keys)
- [last](/docs/{{version}}/collections#method-last)
- [map](/docs/{{version}}/collections#method-map)
- [mapInto](/docs/{{version}}/collections#method-mapinto)
- [mapSpread](/docs/{{version}}/collections#method-mapspread)
- [mapToGroups](/docs/{{version}}/collections#method-maptogroups)
- [mapWithKeys](/docs/{{version}}/collections#method-mapwithkeys)
- [max](/docs/{{version}}/collections#method-max)
- [median](/docs/{{version}}/collections#method-median)
- [merge](/docs/{{version}}/collections#method-merge)
- [min](/docs/{{version}}/collections#method-min)
- [mode](/docs/{{version}}/collections#method-mode)
- [nth](/docs/{{version}}/collections#method-nth)
- [only](/docs/{{version}}/collections#method-only)
- [pad](/docs/{{version}}/collections#method-pad)
- [partition](/docs/{{version}}/collections#method-partition)
- [pipe](/docs/{{version}}/collections#method-pipe)
- [pluck](/docs/{{version}}/collections#method-pluck)
- [pop](/docs/{{version}}/collections#method-pop)
- [prepend](/docs/{{version}}/collections#method-prepend)
- [pull](/docs/{{version}}/collections#method-pull)
- [push](/docs/{{version}}/collections#method-push)
- [put](/docs/{{version}}/collections#method-put)
- [random](/docs/{{version}}/collections#method-random)
- [reduce](/docs/{{version}}/collections#method-reduce)
- [reject](/docs/{{version}}/collections#method-reject)
- [reverse](/docs/{{version}}/collections#method-reverse)
- [search](/docs/{{version}}/collections#method-search)
- [shift](/docs/{{version}}/collections#method-shift)
- [shuffle](/docs/{{version}}/collections#method-shuffle)
- [slice](/docs/{{version}}/collections#method-slice)
- [some](/docs/{{version}}/collections#method-some)
- [sort](/docs/{{version}}/collections#method-sort)
- [sortBy](/docs/{{version}}/collections#method-sortby)
- [sortByDesc](/docs/{{version}}/collections#method-sortbydesc)
- [splice](/docs/{{version}}/collections#method-splice)
- [split](/docs/{{version}}/collections#method-split)
- [sum](/docs/{{version}}/collections#method-sum)
- [take](/docs/{{version}}/collections#method-take)
- [tap](/docs/{{version}}/collections#method-tap)
- [toArray](/docs/{{version}}/collections#method-toarray)
- [toJson](/docs/{{version}}/collections#method-tojson)
- [transform](/docs/{{version}}/collections#method-transform)
- [union](/docs/{{version}}/collections#method-union)
- [unique](/docs/{{version}}/collections#method-unique)
- [uniqueStrict](/docs/{{version}}/collections#method-uniquestrict)
- [unless](/docs/{{version}}/collections#method-unless)
- [values](/docs/{{version}}/collections#method-values)
- [when](/docs/{{version}}/collections#method-when)
- [where](/docs/{{version}}/collections#method-where)
- [whereStrict](/docs/{{version}}/collections#method-wherestrict)
- [whereIn](/docs/{{version}}/collections#method-wherein)
- [whereInStrict](/docs/{{version}}/collections#method-whereinstrict)
- [whereNotIn](/docs/{{version}}/collections#method-wherenotin)
- [whereNotInStrict](/docs/{{version}}/collections#method-wherenotinstrict)
- [zip](/docs/{{version}}/collections#method-zip)

<a name="custom-collections"></a>
## 커스텀-사용자 정의 컬렉션

만약 여러분이 고유한 `Collection` 객체를 사용하고자 한다면, 모델 클래스에서 `newCollection` 메소드를 오버라이드(재지정) 하면 됩니다:

    <?php

    namespace App;

    use App\CustomCollection;
    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * Create a new Eloquent Collection instance.
         *
         * @param  array  $models
         * @return \Illuminate\Database\Eloquent\Collection
         */
        public function newCollection(array $models = [])
        {
            return new CustomCollection($models);
        }
    }

`newCollection` 메소드를 정의하고 나면, Eloquent가 모델에서 `Collection` 인스턴스를 반환할 때 언제나 여러분이 지정한 사용자 정의 컬렉션을 반환할 것입니다. 애플리케이션의 모든 모델에서 사용자 정의 컬렉션을 사용하기를 원한다면, 모든 모델 클래스들이 상속받는 기본 Model 클래스에서 `newCollection` 메소드를 오버라이드 해야만합니다. 
