# Eloquent: Collections

- [Introduction](#introduction)
- [Available Methods](#available-methods)
- [Custom Collections](#custom-collections)

<a name="introduction"></a>
## Introduction

All Eloquent methods that return more than one model result will return instances of the `Illuminate\Database\Eloquent\Collection` class, including results retrieved via the `get` method or accessed via a relationship. The Eloquent collection object extends Laravel's [base collection](/docs/{{version}}/collections), so it naturally inherits dozens of methods used to fluently work with the underlying array of Eloquent models. Be sure to review the Laravel collection documentation to learn all about these helpful methods!

All collections also serve as iterators, allowing you to loop over them as if they were simple PHP arrays:

    use App\Models\User;

    $users = User::where('active', 1)->get();

    foreach ($users as $user) {
        echo $user->name;
    }

However, as previously mentioned, collections are much more powerful than arrays and expose a variety of map / reduce operations that may be chained using an intuitive interface. For example, we may remove all inactive models and then gather the first name for each remaining user:

    $names = User::all()->reject(function ($user) {
        return $user->active === false;
    })->map(function ($user) {
        return $user->name;
    });

<a name="eloquent-collection-conversion"></a>
#### Eloquent Collection Conversion

While most Eloquent collection methods return a new instance of an Eloquent collection, the `collapse`, `flatten`, `flip`, `keys`, `pluck`, and `zip` methods return a [base collection](/docs/{{version}}/collections) instance. Likewise, if a `map` operation returns a collection that does not contain any Eloquent models, it will be converted to a base collection instance.

<a name="available-methods"></a>
## Available Methods

All Eloquent collections extend the base [Laravel collection](/docs/{{version}}/collections#available-methods) object; therefore, they inherit all of the powerful methods provided by the base collection class.

In addition, the `Illuminate\Database\Eloquent\Collection` class provides a superset of methods to aid with managing your model collections. Most methods return `Illuminate\Database\Eloquent\Collection` instances; however, some methods, like `modelKeys`, return an `Illuminate\Support\Collection` instance.

<style>
    #collection-method-list > p {
        column-count: 1; -moz-column-count: 1; -webkit-column-count: 1;
        column-gap: 2em; -moz-column-gap: 2em; -webkit-column-gap: 2em;
    }

    #collection-method-list a {
        display: block;
    }

    .collection-method code {
        font-size: 14px;
    }

    .collection-method:not(.first-collection-method) {
        margin-top: 50px;
    }
</style>

<div id="collection-method-list" markdown="1">

[contains](#method-contains)
[diff](#method-diff)
[except](#method-except)
[find](#method-find)
[fresh](#method-fresh)
[intersect](#method-intersect)
[load](#method-load)
[loadMissing](#method-loadMissing)
[modelKeys](#method-modelKeys)
[makeVisible](#method-makeVisible)
[makeHidden](#method-makeHidden)
[only](#method-only)
[toQuery](#method-toquery)
[unique](#method-unique)

</div>

<a name="method-contains"></a>
#### `contains($key, $operator = null, $value = null)` {.collection-method .first-collection-method}

The `contains` method may be used to determine if a given model instance is contained by the collection. This method accepts a primary key or a model instance:

    $users->contains(1);

    $users->contains(User::find(1));

<a name="method-diff"></a>
#### `diff($items)` {.collection-method}

The `diff` method returns all of the models that are not present in the given collection:

    use App\Models\User;

    $users = $users->diff(User::whereIn('id', [1, 2, 3])->get());

<a name="method-except"></a>
#### `except($keys)` {.collection-method}

The `except` method returns all of the models that do not have the given primary keys:

    $users = $users->except([1, 2, 3]);

<a name="method-find"></a>
#### `find($key)` {.collection-method}

The `find` method returns the model that has a primary key matching the given key. If `$key` is a model instance, `find` will attempt to return a model matching the primary key. If `$key` is an array of keys, `find` will return all models which have a primary key in the given array:

    $users = User::all();

    $user = $users->find(1);

<a name="method-fresh"></a>
#### `fresh($with = [])` {.collection-method}

The `fresh` method retrieves a fresh instance of each model in the collection from the database. In addition, any specified relationships will be eager loaded:

    $users = $users->fresh();

    $users = $users->fresh('comments');

<a name="method-intersect"></a>
#### `intersect($items)` {.collection-method}

The `intersect` method returns all of the models that are also present in the given collection:

    use App\Models\User;

    $users = $users->intersect(User::whereIn('id', [1, 2, 3])->get());

<a name="method-load"></a>
#### `load($relations)` {.collection-method}

The `load` method eager loads the given relationships for all models in the collection:

    $users->load(['comments', 'posts']);

    $users->load('comments.author');

<a name="method-loadMissing"></a>
#### `loadMissing($relations)` {.collection-method}

The `loadMissing` method eager loads the given relationships for all models in the collection if the relationships are not already loaded:

    $users->loadMissing(['comments', 'posts']);

    $users->loadMissing('comments.author');

<a name="method-modelKeys"></a>
#### `modelKeys()` {.collection-method}

The `modelKeys` method returns the primary keys for all models in the collection:

    $users->modelKeys();

    // [1, 2, 3, 4, 5]

<a name="method-makeVisible"></a>
#### `makeVisible($attributes)` {.collection-method}

The `makeVisible` method [makes attributes visible](/docs/{{version}}/eloquent-serialization#hiding-attributes-from-json) that are typically "hidden" on each model in the collection:

    $users = $users->makeVisible(['address', 'phone_number']);

<a name="method-makeHidden"></a>
#### `makeHidden($attributes)` {.collection-method}

The `makeHidden` method [hides attributes](/docs/{{version}}/eloquent-serialization#hiding-attributes-from-json) that are typically "visible" on each model in the collection:

    $users = $users->makeHidden(['address', 'phone_number']);

<a name="method-only"></a>
#### `only($keys)` {.collection-method}

The `only` method returns all of the models that have the given primary keys:

    $users = $users->only([1, 2, 3]);

<a name="method-toquery"></a>
#### `toQuery()` {.collection-method}

The `toQuery` method returns an Eloquent query builder instance containing a `whereIn` constraint on the collection model's primary keys:

    use App\Models\User;

    $users = User::where('status', 'VIP')->get();

    $users->toQuery()->update([
        'status' => 'Administrator',
    ]);

<a name="method-unique"></a>
#### `unique($key = null, $strict = false)` {.collection-method}

The `unique` method returns all of the unique models in the collection. Any models of the same type with the same primary key as another model in the collection are removed:

    $users = $users->unique();

<a name="custom-collections"></a>
## Custom Collections

If you would like to use a custom `Collection` object when interacting with a given model, you may define a `newCollection` method on your model:

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

Once you have defined a `newCollection` method, you will receive an instance of your custom collection anytime Eloquent would normally return an `Illuminate\Database\Eloquent\Collection` instance. If you would like to use a custom collection for every model in your application, you should define the `newCollection` method on a base model class that is extended by all of your application's models.
