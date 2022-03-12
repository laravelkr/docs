# Eloquent: Collections

- [시작하기](#introduction)
- [사용 가능한 메소드들](#available-methods)
- [커스텀-사용자 정의 Collections](#custom-collections)

<a name="introduction"></a>
## 시작하기

둘 이상의 모델 결과를 반환하는 모든 Eloquent 메소드는 `get` 메소드를 통해 검색되거나 관계를 통해 액세스되는 결과를 포함하여 `Illuminate\Database\Eloquent\Collection` 클래스의 인스턴스를 반환합니다. Eloquent Collection 객체는 Laravel의 [기본 컬렉션](/docs/{{version}}/collections)을 확장하므로 자연스럽게 Eloquent 모델의 기본 배열과 원활하게 작동하는 데 사용되는 수십 가지 메서드를 상속합니다. 이러한 유용한 방법들을 알아보려면 Laravel 컬렉션 문서를 확인하세요!

모든 컬렉션은 Iterators(반복자)이기 때문에, 간단한 PHP 배열과 같이 반복문 안에서 사용할 수도 있습니다.

    use App\Models\User;

    $users = User::where('active', 1)->get();

    foreach ($users as $user) {
        echo $user->name;
    }

그러나 이전에 언급했듯이, 컬렉션은 배열보다 강력하며, 직관적인 인터페이스를 사용하여 메소드 체인이 가능한 map / reduce 메소드를 사용 할 수 있습니다. 예를 들어 사용자 중에서 비활성화된 사용자를 제외하고 남은 사용자들의 이름을 확인하려면 다음과 같이 하면 됩니다.

    $names = User::all()->reject(function ($user) {
        return $user->active === false;
    })->map(function ($user) {
        return $user->name;
    });

<a name="eloquent-collection-conversion"></a>
#### Eloquent 컬렉션 변환

대부분의 Eloquent 컬렉션 메소드가 Eloquent 컬렉션의 새로운 인스턴스를 반환하는 반면, `collapse`, `flatten`, `flip`, `keys`, `pluck` 및 `zip` 메소드는 [base collection](/docs/{{version}}/collections) 인스턴스. 마찬가지로, `map` 작업이 Eloquent 모델을 포함하지 않는 컬렉션을 반환하면 기본 컬렉션 인스턴스로 변환됩니다.

<a name="available-methods"></a>
## 사용가능한 메소드들 

- [contains](#method-contains)
- [diff](#method-diff)
- [except](#method-except)
- [find](#method-find)
- [fresh](#method-fresh)
- [intersect](#method-intersect)
- [load](#method-load)
- [loadMissing](#method-loadMissing)
- [modelKeys](#method-modelKeys)
- [makeVisible](#method-makeVisible)
- [makeHidden](#method-makeHidden)
- [only](#method-only)
- [toQuery](#method-toquery)
- [unique](#method-unique)

모든 Eloquent 컬렉션은 기본 [Laravel collection](/docs/{{version}}/collections#available-methods) 객체를 확장합니다. 따라서 기본 컬렉션 클래스에서 제공하는 모든 강력한 메소드를 상속받습니다.

또한, `Illuminate\Database\Eloquent\Collection` 클래스는 모델 컬렉션을 관리하는 데 도움이 되는 메서드의 상위 집합을 제공합니다. 대부분의 메소드는 `Illuminate\Database\Eloquent\Collection` 인스턴스를 반환합니다. 그러나 `modelKeys`와 같은 일부 메소드는 `Illuminate\Support\Collection` 인스턴스를 반환합니다.

<a name="method-contains"></a>
#### `contains($key, $operator = null, $value = null)`

`contains` 메소드는 주어진 모델 인스턴스가 컬렉션에 포함되어 있는지를 결정하는데 사용될 수 있습니다. 이 메소드는 기본-primary 키 또는 모델 인스턴스를 허용합니다.

    $users->contains(1);

    $users->contains(User::find(1));

<a name="method-diff"></a>
#### `diff($items)`

`diff` 메소드는 주어진 컬렉션에 존재하지 않는 모든 모델을 리턴합니다.

    use App\Models\User;

    $users = $users->diff(User::whereIn('id', [1, 2, 3])->get());

<a name="method-except"></a>
#### `except($keys)`

`except` 메소드는 주어진 기본-primary 키를 가지고 있지 않은 모든 모델을 반환합니다.

    $users = $users->except([1, 2, 3]);

<a name="method-find"></a>
#### `find($key)`

`find` 메서드는 주어진 키와 일치하는 기본-primary 키가 있는 모델을 반환합니다. `$key`가 모델 인스턴스라면, `find`는 기본-primary 키와 일치하는 모델을 반환하려고 시도 할 것입니다. `$key`가 키 배열이면 `find`는 지정된 배열에 기본 키가 있는 모든 모델을 반환합니다.

    $users = User::all();

    $user = $users->find(1);

<a name="method-fresh"></a>
#### `fresh($with = [])`

`fresh` 메소드는 데이터베이스에서 컬렉션의 각 모델의 새로운 인스턴스를 가져옵니다. 또한 지정된 모든 관계가 eager 로드됩니다.

    $users = $users->fresh();

    $users = $users->fresh('comments');

<a name="method-intersect"></a>
#### `intersect($items)`

`intersect` 메소드는 주어진 컬렉션에 존재하는 모든 모델을 반환합니다.

    use App\Models\User;

    $users = $users->intersect(User::whereIn('id', [1, 2, 3])->get());

<a name="method-load"></a>
#### `load($relations)`

`load` 메소드는 컬렉션의 모든 모델에 대해 주어진 관계를 로드합니다.

    $users->load(['comments', 'posts']);

    $users->load('comments.author');

<a name="method-loadMissing"></a>
#### `loadMissing($relations)`

`loadMissing` 메소드는 관계가 아직 로드되지 않은 경우 컬렉션의 모든 모델에 대해 주어진 관계를 로드합니다.

    $users->loadMissing(['comments', 'posts']);

    $users->loadMissing('comments.author');

<a name="method-modelKeys"></a>
#### `modelKeys()`

`modelKeys` 메소드는 컬렉션 내의 모든 모델에 대한 기본-primary 키를 반환합니다.

    $users->modelKeys();

    // [1, 2, 3, 4, 5]

<a name="method-makeVisible"></a>
#### `makeVisible($attributes)`

`makeVisible` 메소드[makes attributes visible](/docs/{{version}}/eloquent-serialization#hiding-attributes-from-json)는 컬렉션의 각 모델에서 전형적으로 "숨겨진-hidden" 속성을 가시적으로 만듭니다.

    $users = $users->makeVisible(['address', 'phone_number']);

<a name="method-makeHidden"></a>
#### `makeHidden($attributes)`

`makeHidden` 메소드[hides attributes](/docs/{{version}}/eloquent-serialization#hiding-attributes-from-json)는 컬렉션의 각 모델에 일반적으로 "가시적-visible"인 속성을 숨깁니다.

    $users = $users->makeHidden(['address', 'phone_number']);

<a name="method-only"></a>
#### `only($keys)`

`only` 메소드는 주어진 기본-primary 키를 가진 모든 모델을 반환합니다.

    $users = $users->only([1, 2, 3]);

<a name="method-toquery"></a>
#### `toQuery()`

`toQuery` 메소드는 컬렉션 모델의 기본 키에 대한 `whereIn` 제약 조건을 포함하는 Eloquent 쿼리 빌더 인스턴스를 반환합니다.

    use App\Models\User;

    $users = User::where('status', 'VIP')->get();

    $users->toQuery()->update([
        'status' => 'Administrator',
    ]);

<a name="method-unique"></a>
#### `unique($key = null, $strict = false)`

`unique` 메소드는 컬렉션 내의 모든 유일한 모델들을 반환합니다. 컬렉션의 다른 모델과 동일한 기본-primary 키가 있는 동일한 유형의 모델은 제거됩니다.

    $users = $users->unique();

<a name="custom-collections"></a>
## 커스텀-사용자 정의 Collections

주어진 모델과 상호작용할 때 사용자 정의 `Collection` 객체를 사용하려면 모델에 `newCollection` 메소드를 정의할 수 있습니다.

    <?php

    namespace App\Models;

    use App\Support\UserCollection;
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
            return new UserCollection($models);
        }
    }

일단 `newCollection` 메소드를 정의하면 Eloquent가 일반적으로 `Illuminate\Database\Eloquent\Collection` 인스턴스를 반환할 때마다 사용자 정의 컬렉션의 인스턴스를 받게 됩니다. 애플리케이션의 모든 모델에 대해 사용자 지정 컬렉션을 사용하려면 애플리케이션의 모든 모델에 의해 확장되는 기본 모델 클래스에 `newCollection` 메서드를 정의해야 합니다. 
