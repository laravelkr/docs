# Eloquent: Serialization
# Eloquent: Serialization

- [Introduction](#introduction)
- [소개](#introduction)
- [Basic Usage](#basic-usage)
- [기본적인 사용법](#basic-usage)
- [Hiding Attributes From JSON](#hiding-attributes-from-json)
- [JSON 변환시 속성값 숨기기](#hiding-attributes-from-json)
- [Appending Values To JSON](#appending-values-to-json)
- [JSON 변환시 특정 값 추가하기](#appending-values-to-json)

<a name="introduction"></a>
## Introduction
## 소개

When building JSON APIs, you will often need to convert your models and relationships to arrays or JSON. Eloquent includes convenient methods for making these conversions, as well as controlling which attributes are included in your serializations.

JSON API를 구성할 때, 여러분은 자주 특정 모델과, 연관된 모델들을 배열 또는 JSON 으로 변환해야될 필요가 있을 것입니다. Eloquent 는 serialization 에서 이러한 변환들과, 어떠한 속성들이 JSON에 포함되어야 하는지 제어할 수 있도록 편리한 메소드들을 가지고 있습니다.

<a name="basic-usage"></a>
## Basic Usage
## 기본적인 사용법

#### Converting A Model To An Array
#### Converting A Model To An Array

To convert a model and its loaded [relationships](/docs/{{version}}/eloquent-relationships) to an array, you may use the `toArray` method. This method is recursive, so all attributes and all relations (including the relations of relations) will be converted to arrays:

하나의 모델과 연관된 [관계 모델](/docs/{{version}}/eloquent-relationships)들을 배열로 변환 하고자 할 때에는, `toArray` 메소드를 사용하면 됩니다. 이 메소드는 재귀적이기 때문에, 모든 속성들과 모든 관계 모델들(관계 모델들 안에 포함된 관계 모델들까지)이 배열로 변환될 것입니다.

    $user = App\User::with('roles')->first();

    return $user->toArray();

You may also convert [collections](/docs/{{version}}/eloquent-collections) to arrays:

또한 [collections](/docs/{{version}}/eloquent-collections)을 배열로 변환할 수도 있습니다.

    $users = App\User::all();

    return $users->toArray();

#### Converting A Model To JSON
#### 모델을 JSON으로 변환하기

To convert a model to JSON, you may use the `toJson` method. Like `toArray`, the `toJson` method is recursive, so all attributes and relations will be converted to JSON:

모델을 JSON으로 변환하기 위해서는, `toJson` 메소드를 사용하면 됩니다. `toArray` 와 같이 `toJson` 메소드는 재귀적이므로, 모든 속성과 관계 모델들은 JSON으로 변환될 것입니다.

    $user = App\User::find(1);

    return $user->toJson();

Alternatively, you may cast a model or collection to a string, which will automatically call the `toJson` method:

이렇게 하는 대신에, 모델 또는 컬렉션을 문자열(string)으로 캐스팅하면, 자동으로 `toJson` 메소드가 호출 될것입니다.

    $user = App\User::find(1);

    return (string) $user;

Since models and collections are converted to JSON when cast to a string, you can return Eloquent objects directly from your application's routes or controllers:

모델과 컬렉션을 문자열로 캐스팅할 때에는, JSON으로 변환되므로, 어플리케이션의 라우트 또는 컨트롤러에서 Eloquent 객체를 바로 반환할 수도 있습니다.

    Route::get('users', function () {
        return App\User::all();
    });

<a name="hiding-attributes-from-json"></a>
## Hiding Attributes From JSON
## JSON 변환시 속성값 숨기기

Sometimes you may wish to limit the attributes, such as passwords, that are included in your model's array or JSON representation. To do so, add a `$hidden` property definition to your model:

때로는 패스워드와 같이, 모델이 배열 또는 JSON으로 재구성될 때 속성을 제한하고자 할 수도 있습니다. 이렇게 하기 위해서는 모델의 `$hidden` 값을 정의에 제한하고자 하는 속성을 추가하면 됩니다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * The attributes that should be hidden for arrays.
         *
         * @var array
         */
        protected $hidden = ['password'];
    }

> **Note:** When hiding relationships, use the relationship's **method** name, not its dynamic property name.

> **주의:** 관계를 숨기고자 할 때에는, 동적인 프로퍼티 이름이 아니라, 관계에 대한 **메소드** 이름을 사용하십시오.

Alternatively, you may use the `visible` property to define a white-list of attributes that should be included in your model's array and JSON representation:

이렇게 하는 대신, 모델이 배열 또는 JSON으로 재구성될 때 포함되어야 하는 속성들에 대한 화이트 리스트를 정의하는 `visible` 값을 사용할 수도 있습니다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * The attributes that should be visible in arrays.
         *
         * @var array
         */
        protected $visible = ['first_name', 'last_name'];
    }

#### Temporarily Modifying Property Visibility
#### 일시적으로 프로퍼티의 노출 정도를 수정하기

If you would like to make some typically hidden attributes visible on a given model instance, you may use the `makeVisible` method. The `makeVisible` method returns the model instance for convenient method chaining:

주어진 모델 인스턴스에서 몇가지 일반적인 숨겨진 속성을 보이도록 만들고자 한다면, `makeVisible` 메소드를 사용하면 됩니다. `makeVisible` 메소드는 편리한 메소드 체이닝을 위해서 모델 인스턴스를 반환합니다:

    return $user->makeVisible('attribute')->toArray();

Likewise, if you would like to make some typically visible attributes hidden on a given model instance, you may use the `makeHidden` method.

마찬가지로, 주어진 모델 인스턴스에서 몇가지 일반적으로 보여지던 속성을 보이지 않도록 만들고자 한다면, `makeHidden`메소드를 사용하면 됩니다.

    return $user->makeHidden('attribute')->toArray();

<a name="appending-values-to-json"></a>
## Appending Values To JSON
## JSON 변환시 특정 값 추가하기

Occasionally, you may need to add array attributes that do not have a corresponding column in your database. To do so, first define an [accessor](/docs/{{version}}/eloquent-mutators) for the value:

때때로, 사용자는 데이터베이스에서 해당 컬럼이 없는 배열 속성을 추가가 필요할 수도 있습니다. 이렇게 하기 위해서는, 단순히 값에 대한 [accessor](/docs/{{version}}/eloquent-mutators)를 정의하면 됩니다:

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * Get the administrator flag for the user.
         *
         * @return bool
         */
        public function getIsAdminAttribute()
        {
            return $this->attributes['admin'] == 'yes';
        }
    }

Once you have created the accessor, add the attribute name to the `appends` property on the model:

사용자가 accessor를 생성한 뒤, 모델의 `appends`값에 속성의 이름을 추가합니다:

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * The accessors to append to the model's array form.
         *
         * @var array
         */
        protected $appends = ['is_admin'];
    }

Once the attribute has been added to the `appends` list, it will be included in both the model's array and JSON forms. Attributes in the `appends` array will also respect the `visible` and `hidden` settings configured on the model.

속성이 `appends` 리스트에 추가되고나면 모델이 배열이나 JSON 형태로 변환될 때 자동으로 포함될 것입니다 .`appends` 배열 안에 있는 속성들은 또한 모델에 정의된 `visible`와 `hidden`값에 영향을 받을 것입니다.
