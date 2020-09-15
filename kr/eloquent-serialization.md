# Eloquent: Serialization
# Eloquent: Serialization

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Serializing Models & Collections](#serializing-models-and-collections)
- [모델 & 컬렉션 Serializing](#serializing-models-and-collections)
    - [Serializing To Arrays](#serializing-to-arrays)
    - [배열로 Serializing](#serializing-to-arrays)
    - [Serializing To JSON](#serializing-to-json)
    - [JSON 으로 Serializing](#serializing-to-json)
- [Hiding Attributes From JSON](#hiding-attributes-from-json)
- [JSON 변환시 속성값 숨기기](#hiding-attributes-from-json)
- [Appending Values To JSON](#appending-values-to-json)
- [JSON 변환시 특정 값 추가하기](#appending-values-to-json)
- [Date Serialization](#date-serialization)
- [날짜 Serialization](#date-serialization)

<a name="introduction"></a>
## Introduction
## 시작하기

When building JSON APIs, you will often need to convert your models and relationships to arrays or JSON. Eloquent includes convenient methods for making these conversions, as well as controlling which attributes are included in your serializations.

JSON API를 구성할 때, 여러분은 자주 특정 모델과, 연관된 모델들을 배열 또는 JSON 으로 변환해야될 필요가 있을 것입니다. Eloquent 는 serialization 에서 이러한 변환들과, 어떠한 속성들이 JSON에 포함되어야 하는지 제어할 수 있도록 편리한 메소드들을 가지고 있습니다.

<a name="serializing-models-and-collections"></a>
## Serializing Models & Collections
## 모델 & 컬렉션 Serializing

<a name="serializing-to-arrays"></a>
### Serializing To Arrays
### 배열로 Serializaing

To convert a model and its loaded [relationships](/docs/{{version}}/eloquent-relationships) to an array, you should use the `toArray` method. This method is recursive, so all attributes and all relations (including the relations of relations) will be converted to arrays:

하나의 모델과 연관된 [관계 모델](/docs/{{version}}/eloquent-relationships)들을 배열로 변환 하고자 할 때에는, `toArray` 메소드를 사용해야만 합니다. 이 메소드는 재귀적이기 때문에, 모든 속성들과 모든 관계 모델들(관계 모델들 안에 포함된 관계 모델들까지)이 배열로 변환될 것입니다.

    $user = App\User::with('roles')->first();

    return $user->toArray();

To convert only a model's attributes to an array, use the `attributesToArray` method:

모델의 속성만을 배열로 변환하려면 `attributesToArray` 메소드를 사용하십시오.

    $user = App\User::first();

    return $user->attributesToArray();

You may also convert entire [collections](/docs/{{version}}/eloquent-collections) of models to arrays:

또한 전체 모델 [collections](/docs/{{version}}/eloquent-collections)을 배열로 변환할 수도 있습니다.

    $users = App\User::all();

    return $users->toArray();

<a name="serializing-to-json"></a>
### Serializing To JSON
### JSON으로 Serializaing

To convert a model to JSON, you should use the `toJson` method. Like `toArray`, the `toJson` method is recursive, so all attributes and relations will be converted to JSON. You may also specify JSON encoding options [supported by PHP](https://secure.php.net/manual/en/function.json-encode.php):

모델을 JSON으로 변환하기 위해서는, `toJson` 메소드를 사용해야 합니다. `toArray` 메소드와 같이 `toJson` 메소드는 재귀적이기 때문에, 모든 속성들과 relations-관계들은 JSON으로 변환됩니다. 또한 [PHP에서 제공되는](https://secure.php.net/manual/en/function.json-encode.php) JSON 인코딩 옵션을 지정할 수 있습니다.

    $user = App\User::find(1);

    return $user->toJson();

    return $user->toJson(JSON_PRETTY_PRINT);

Alternatively, you may cast a model or collection to a string, which will automatically call the `toJson` method on the model or collection:

이렇게 하는 대신에, 모델 또는 컬렉션을 문자열(string)으로 캐스팅하면, 자동으로 `toJson` 메소드가 호출 될것입니다.

    $user = App\User::find(1);

    return (string) $user;

Since models and collections are converted to JSON when cast to a string, you can return Eloquent objects directly from your application's routes or controllers:

모델과 컬렉션을 문자열로 캐스팅할 때에는, JSON으로 변환되므로, 애플리케이션의 라우트 또는 컨트롤러에서 Eloquent 객체를 바로 반환할 수도 있습니다.

    Route::get('users', function () {
        return App\User::all();
    });

#### Relationships
#### 관계-Relationships

When an Eloquent model is converted to JSON, its loaded relationships will automatically be included as attributes on the JSON object. Also, though Eloquent relationship methods are defined using "camel case", a relationship's JSON attribute will be "snake case".

Eloquent 모델을 JSON으로 변환하면 로드 된 관계가 자동으로 JSON 객체의 속성에 포함됩니다. 또한 Eloquent 관계 메소드는 "camel case"를 사용하여 정의되지만 관계의 JSON 속성은 "snake case"가됩니다.

<a name="hiding-attributes-from-json"></a>
## Hiding Attributes From JSON
## JSON 변환시 속성값 숨기기

Sometimes you may wish to limit the attributes, such as passwords, that are included in your model's array or JSON representation. To do so, add a `$hidden` property to your model:

때로는 패스워드와 같이, 모델이 배열 또는 JSON으로 재구성될 때 속성을 제한하고자 할 수도 있습니다. 이렇게 하기 위해서는 모델의 `$hidden` 속성에 제한하고자 하는 필드를 추가하면 됩니다.

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

> {note} When hiding relationships, use the relationship's method name.

> {note} 관계를 숨기고자 할 때에는, 관계에 대한 메소드 이름을 사용하십시오

Alternatively, you may use the `visible` property to define a white-list of attributes that should be included in your model's array and JSON representation. All other attributes will be hidden when the model is converted to an array or JSON:

이렇게 하는 대신, 모델이 배열 또는 JSON으로 재구성될 때 포함되어야 하는 속성들에 대한 화이트 리스트를 정의하는 `visible` 값을 사용할 수도 있습니다. 모든 다른 속성들은 모델이 배열이나 JSON 으로 변환될 때 숨겨질 것입니다.

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

#### Temporarily Modifying Attribute Visibility
#### 일시적으로 속성들의 노출 정도를 수정하기

If you would like to make some typically hidden attributes visible on a given model instance, you may use the `makeVisible` method. The `makeVisible` method returns the model instance for convenient method chaining:

주어진 모델 인스턴스에서 몇가지 일반적인 숨겨진 속성을 보이도록 만들고자 한다면, `makeVisible` 메소드를 사용하면 됩니다. `makeVisible` 메소드는 편리한 메소드 체이닝을 위해서 모델 인스턴스를 반환합니다.

    return $user->makeVisible('attribute')->toArray();

Likewise, if you would like to make some typically visible attributes hidden on a given model instance, you may use the `makeHidden` method.

마찬가지로, 주어진 모델 인스턴스에서 몇가지 일반적으로 보여지던 속성을 보이지 않도록 만들고자 한다면, `makeHidden`메소드를 사용하면 됩니다.

    return $user->makeHidden('attribute')->toArray();

<a name="appending-values-to-json"></a>
## Appending Values To JSON
## JSON 변환시 특정 값 추가하기

Occasionally, when casting models to an array or JSON, you may wish to add attributes that do not have a corresponding column in your database. To do so, first define an [accessor](/docs/{{version}}/eloquent-mutators) for the value:

때때로, 모델이 배열이나 JSON으로 캐스팅 될 때, 데이터베이스의 컬럼에 일치하지 않는 속성들을 추가하기를 원할 수도 있습니다. 이렇게 하기 위해서는, 단순히 값에 대한 [accessor](/docs/{{version}}/eloquent-mutators)를 정의하면 됩니다.

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
            return $this->attributes['admin'] === 'yes';
        }
    }

After creating the accessor, add the attribute name to the `appends` property on the model. Note that attribute names are typically referenced in "snake case", even though the accessor is defined using "camel case":

accessor 를 생성한 다음에, 모델의 `appends`값에 속성의 이름을 추가합니다. accessor 가 "카멜 케이스"로 정의되어 있더라도, 속성 이름은 정상적으로 "스네이크 케이스"로 엑세스 됩니다.

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

Once the attribute has been added to the `appends` list, it will be included in both the model's array and JSON representations. Attributes in the `appends` array will also respect the `visible` and `hidden` settings configured on the model.

속성이 `appends` 리스트에 추가되고나면 모델이 배열이나 JSON 형태로 변환될 때 자동으로 포함될 것입니다 .`appends` 배열 안에 있는 속성들은 또한 모델에 정의된 `visible`와 `hidden`값에 영향을 받을 것입니다.

#### Appending At Run Time
#### 실행 될때(run time에서) 추가하기

You may instruct a single model instance to append attributes using the `append` method. Or, you may use the `setAppends` method to override the entire array of appended properties for a given model instance:

`append` 메소드를 사용해서 하나의 모델 인스턴스에 속성을 추가할 수도 있습니다. 또는 `setAppends` 메소드를 사용하여 주어진 모델 인스턴스에 추가된 속성들의 전체 배열을 오버라이드 할 수 있습니다.

    return $user->append('is_admin')->toArray();

    return $user->setAppends(['is_admin'])->toArray();

<a name="date-serialization"></a>
## Date Serialization
## 날짜 Serialization

#### Customizing The Default Date Format
#### 기본 날짜 형식 사용자 정의

You may customize the default serialization format by overriding the `serializeDate` method:

`serializeDate` 메소드를 재정의하여 기본 시리얼라이즈 포맷을 원하는데로 설정 할 수 있습니다.

    /**
     * Prepare a date for array / JSON serialization.
     *
     * @param  \DateTimeInterface  $date
     * @return string
     */
    protected function serializeDate(DateTimeInterface $date)
    {
        return $date->format('Y-m-d');
    }

#### Customizing The Date Format Per Attribute
#### 날짜 포맷의 속성을 커스터마이징 하기

You may customize the serialization format of individual Eloquent date attributes by specifying the date format in the [cast declaration](/docs/{{version}}/eloquent-mutators#attribute-casting):

만약 각각의 Eloquent 시리얼라이즈 포맷을 커스터마이즈 하고싶다면, 날짜 형식은 [cast declaration](/docs/{{version}}/eloquent-mutators#attribute-casting) 에 자세하게 명시되어 있습니다.

    protected $casts = [
        'birthday' => 'date:Y-m-d',
        'joined_at' => 'datetime:Y-m-d H:00',
    ];
