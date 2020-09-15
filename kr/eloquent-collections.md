# Eloquent: Collections
# Eloquent: Collections

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Available Methods](#available-methods)
- [사용 가능한 메소드들](#available-methods)
- [Custom Collections](#custom-collections)
- [커스텀-사용자 정의 Collections](#custom-collections)

<a name="introduction"></a>
## Introduction
## 시작하기

All multi-result sets returned by Eloquent are instances of the `Illuminate\Database\Eloquent\Collection` object, including results retrieved via the `get` method or accessed via a relationship. The Eloquent collection object extends the Laravel [base collection](/docs/{{version}}/collections), so it naturally inherits dozens of methods used to fluently work with the underlying array of Eloquent models.

`get` 메소드를 통하거나 하나의 `relationship-관계`를 통해서 Eloquent로 부터 반환되는 모든 멀티 레코드 결과는 `Illuminate\Database\Eloquent\Collection` 객체의 인스턴스가 됩니다. Eloquent 컬렉션 객체는 라라벨의 [base collection](/docs/{{version}}/collections)을 상속받기 때문에, 자연스럽게 ELoquent 모델에 대한 결과에서 다양하고 편리한 메소드들을 사용할 수 있습니다. 

All collections also serve as iterators, allowing you to loop over them as if they were simple PHP arrays:

모든 컬렉션은 Iterators(반복자)이기 때문에, 간단한 PHP 배열과 같이 반복문 안에서 사용할 수도 있습니다.

    $users = App\User::where('active', 1)->get();

    foreach ($users as $user) {
        echo $user->name;
    }

However, collections are much more powerful than arrays and expose a variety of map / reduce operations that may be chained using an intuitive interface. For example, let's remove all inactive models and gather the first name for each remaining user:

하지만, 컬렉션은 배열보다 강력하며, 직관적인 인터페이스를 사용하여 메소드 체인이 가능한 map / reduce 메소드를 사용 할 수 있습니다. 예를 들어 사용자 중에서 비활성화된 사용자를 제외하고 남은 사용자들의 이름을 확인하려면 다음과 같이 하면 됩니다.

    $users = App\User::all();

    $names = $users->reject(function ($user) {
        return $user->active === false;
    })
    ->map(function ($user) {
        return $user->name;
    });

> {note} While most Eloquent collection methods return a new instance of an Eloquent collection, the `pluck`, `keys`, `zip`, `collapse`, `flatten` and `flip` methods return a [base collection](/docs/{{version}}/collections) instance. Likewise, if a `map` operation returns a collection that does not contain any Eloquent models, it will be automatically cast to a base collection.

> {note} 대부분의 Eloquent 컬렉션 메소드는 새로운 Eloquent 컬렉션 인스턴스를 반환하고, `pluck`, `keys`, `zip`, `collapse`, `flatten` 그리고 `flip` 메소드는 [기본 컬렉션](/docs/{{version}}/collections) 인스턴스를 반환합니다. 또한 만약 `map` 실행이 Eloquent 모델을 포함하지 않는 컬렉션을 반환한다면, 자동으로 기본 컬렉션으로 캐스팅 될 것입니다.

<a name="available-methods"></a>
## Available Methods
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

All Eloquent collections extend the base [Laravel collection](/docs/{{version}}/collections#available-methods) object; therefore, they inherit all of the powerful methods provided by the base collection class.

모든 Eloquent 컬렉션은 기본 [Laravel collection](/docs/{{version}}/collections#available-methods) 객체를 확장합니다. 따라서 기본 컬렉션 클래스에서 제공하는 모든 강력한 메소드를 상속받습니다.

In addition, the `Illuminate\Database\Eloquent\Collection` class provides a superset of methods to aid with managing your model collections. Most methods return `Illuminate\Database\Eloquent\Collection` instances; however, some methods return a base `Illuminate\Support\Collection` instance.

또한 `Illuminate\Database\Eloquent\Collection` 클래스는 모델 컬렉션을 관리하는 데 도움이 되는 슈퍼셋을 제공합니다. 대부분의 메소드는 `Illuminate\Database\Eloquent\Collection` 인스턴스를 반환하지만 일부 메소드는 기본 `Illuminate\Support\Collection` 인스턴스를 반환합니다.

#### `contains($key, $operator = null, $value = null)`
#### `contains($key, $operator = null, $value = null)`

The `contains` method may be used to determine if a given model instance is contained by the collection. This method accepts a primary key or a model instance:

`contains` 메소드는 주어진 모델 인스턴스가 컬렉션에 포함되어 있는지를 결정하는데 사용될 수 있습니다. 이 메소드는 기본-primary 키 또는 모델 인스턴스를 허용합니다.

    $users->contains(1);

    $users->contains(User::find(1));

<a name="method-diff"></a>
#### `diff($items)`
#### `diff($items)`

The `diff` method returns all of the models that are not present in the given collection:

`diff` 메소드는 주어진 컬렉션에 존재하지 않는 모든 모델을 리턴합니다.

    use App\User;

    $users = $users->diff(User::whereIn('id', [1, 2, 3])->get());

<a name="method-except"></a>
#### `except($keys)`
#### `except($keys)`

The `except` method returns all of the models that do not have the given primary keys:

`except` 메소드는 주어진 기본-primary 키를 가지고 있지 않은 모든 모델을 반환합니다.

    $users = $users->except([1, 2, 3]);

<a name="method-find"></a>
#### `find($key)` {#collection-method .first-collection-method}
#### `find($key)` {#collection-method .first-collection-method}

The `find` method finds a model that has a given primary key. If `$key` is a model instance, `find` will attempt to return a model matching the primary key. If `$key` is an array of keys, `find` will return all models which match the `$keys` using `whereIn()`:

`find` 메소드는 주어진 기본-primary 키를 가진 모델을 찾습니다. `$key`가 모델 인스턴스라면, `find`는 기본-primary 키와 일치하는 모델을 반환하려고 시도 할 것입니다. `$key`가 키 배열이면,`find`는`$ keys`와 일치하는 모든 모델을`whereIn ()`을 사용하여 돌려줍니다.

    $users = User::all();

    $user = $users->find(1);

<a name="method-fresh"></a>
#### `fresh($with = [])`
#### `fresh($with = [])`

The `fresh` method retrieves a fresh instance of each model in the collection from the database. In addition, any specified relationships will be eager loaded:

`fresh` 메소드는 데이터베이스에서 컬렉션의 각 모델의 새로운 인스턴스를 가져옵니다. 또한 지정된 모든 관계가 eager 로드됩니다.

    $users = $users->fresh();

    $users = $users->fresh('comments');

<a name="method-intersect"></a>
#### `intersect($items)`
#### `intersect($items)`

The `intersect` method returns all of the models that are also present in the given collection:

`intersect` 메소드는 주어진 컬렉션에 존재하는 모든 모델을 반환합니다.

    use App\User;

    $users = $users->intersect(User::whereIn('id', [1, 2, 3])->get());

<a name="method-load"></a>
#### `load($relations)`
#### `load($relations)`

The `load` method eager loads the given relationships for all models in the collection:

`load` 메소드는 컬렉션의 모든 모델에 대해 주어진 관계를 로드합니다.

    $users->load('comments', 'posts');

    $users->load('comments.author');

<a name="method-loadMissing"></a>
#### `loadMissing($relations)`
#### `loadMissing($relations)`

The `loadMissing` method eager loads the given relationships for all models in the collection if the relationships are not already loaded:

`loadMissing` 메소드는 관계가 아직 로드되지 않은 경우 컬렉션의 모든 모델에 대해 주어진 관계를 로드합니다.

    $users->loadMissing('comments', 'posts');

    $users->loadMissing('comments.author');

<a name="method-modelKeys"></a>
#### `modelKeys()`
#### `modelKeys()`

The `modelKeys` method returns the primary keys for all models in the collection:

`modelKeys` 메소드는 컬렉션 내의 모든 모델에 대한 기본-primary 키를 반환합니다.

    $users->modelKeys();

    // [1, 2, 3, 4, 5]

<a name="method-makeVisible"></a>
#### `makeVisible($attributes)`
#### `makeVisible($attributes)`

The `makeVisible` method makes attributes visible that are typically "hidden" on each model in the collection:

`makeVisible` 메소드는 컬렉션의 각 모델에서 전형적으로 "숨겨진-hidden" 속성을 가시적으로 만듭니다.

    $users = $users->makeVisible(['address', 'phone_number']);

<a name="method-makeHidden"></a>
#### `makeHidden($attributes)`
#### `makeHidden($attributes)`

The `makeHidden` method hides attributes that are typically "visible" on each model in the collection:

`makeHidden` 메소드는 컬렉션의 각 모델에 일반적으로 "가시적-visible"인 속성을 숨깁니다.

    $users = $users->makeHidden(['address', 'phone_number']);

<a name="method-only"></a>
#### `only($keys)`
#### `only($keys)`

The `only` method returns all of the models that have the given primary keys:

`only` 메소드는 주어진 기본-primary 키를 가진 모든 모델을 반환합니다.

    $users = $users->only([1, 2, 3]);

<a name="method-toquery"></a>
#### `toQuery()`
#### `toQuery()`

The `toQuery` method returns an Eloquent query builder instance containing a `whereIn` constraint on the collection model's primary keys:

`toQuery` 메소드는 컬렉션 모델의 기본 키에 대한 `whereIn` 제약 조건을 포함하는 Eloquent 쿼리 빌더 인스턴스를 반환합니다.

    $users = App\User::where('status', 'VIP')->get();

    $users->toQuery()->update([
        'status' => 'Administrator',
    ]);

<a name="method-unique"></a>
#### `unique($key = null, $strict = false)`
#### `unique($key = null, $strict = false)`

The `unique` method returns all of the unique models in the collection. Any models of the same type with the same primary key as another model in the collection are removed.

`unique` 메소드는 컬렉션 내의 모든 유일한 모델들을 반환합니다. 컬렉션의 다른 모델과 동일한 기본-primary 키가 있는 동일한 유형의 모델은 제거됩니다.

    $users = $users->unique();

<a name="custom-collections"></a>
## Custom Collections
## 커스텀-사용자 정의 컬렉션

If you need to use a custom `Collection` object with your own extension methods, you may override the `newCollection` method on your model:

만약 여러분이 고유한 `Collection` 객체를 사용하고자 한다면, 모델 클래스에서 `newCollection` 메소드를 오버라이드(재지정) 하면 됩니다.

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

Once you have defined a `newCollection` method, you will receive an instance of your custom collection anytime Eloquent returns a `Collection` instance of that model. If you would like to use a custom collection for every model in your application, you should override the `newCollection` method on a base model class that is extended by all of your models.

`newCollection` 메소드를 정의하고 나면, Eloquent가 모델에서 `Collection` 인스턴스를 반환할 때 언제나 여러분이 지정한 사용자 정의 컬렉션을 반환할 것입니다. 애플리케이션의 모든 모델에서 사용자 정의 컬렉션을 사용하기를 원한다면, 모든 모델 클래스들이 상속받는 기본 Model 클래스에서 `newCollection` 메소드를 오버라이드 해야합니다. 
