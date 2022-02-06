# Eloquent: Serialization
# Eloquent: Serialization

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Serializing Models & Collections](#serializing-models-and-collections)
- [모델 및 컬렉션 Serializing](#serializing-models-and-collections)
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

When building APIs using Laravel, you will often need to convert your models and relationships to arrays or JSON. Eloquent includes convenient methods for making these conversions, as well as controlling which attributes are included in the serialized representation of your models.

라라벨을 사용하여 API를 구성할 때, 여러분은 자주 특정 모델과, 연관된 모델들을 배열 또는 JSON 으로 변환해야 하는 경우가 있을겁니다. Eloquent에는 이러한 변환을 수행하는 편리한 메소드와 모델의 직렬화된(serialized) 표현에 포함되는 속성을 제어하는 메소드가 포함되어 있습니다.

> {tip} For an even more robust way of handling Eloquent model and collection JSON serialization, check out the documentation on [Eloquent API resources](/docs/{{version}}/eloquent-resources).

> {tip} Eloquent 모델 및 컬렉션 JSON 직렬화를 처리하는 훨씬 더 강력한 방법은 [Eloquent API 리소스](/docs/{{version}}/eloquent-resources)에 대한 문서를 확인하세요.

<a name="serializing-models-and-collections"></a>
## Serializing Models & Collections
## 모델 & 컬렉션 Serializing

<a name="serializing-to-arrays"></a>
### Serializing To Arrays
### 배열로 Serializaing

To convert a model and its loaded [relationships](/docs/{{version}}/eloquent-relationships) to an array, you should use the `toArray` method. This method is recursive, so all attributes and all relations (including the relations of relations) will be converted to arrays:

하나의 모델과 연관된 [관계 모델](/docs/{{version}}/eloquent-relationships)들을 배열로 변환 하고자 할 때에는, `toArray` 메소드를 사용해야만 합니다. 이 메소드는 재귀적이기 때문에, 모든 속성들과 모든 관계 모델들(관계 모델들 안에 포함된 관계 모델들까지)이 배열로 변환될 것입니다.

    use App\Models\User;

    $user = User::with('roles')->first();

    return $user->toArray();

The `attributesToArray` method may be used to convert a model's attributes to an array but not its relationships:

`attributesToArray` 메소드는 모델의 속성을 배열로 변환하는 데 사용할 수 있지만 관계는 변환하지 않습니다.

    $user = User::first();

    return $user->attributesToArray();

You may also convert entire [collections](/docs/{{version}}/eloquent-collections) of models to arrays by calling the `toArray` method on the collection instance:

컬렉션 인스턴스에서 `toArray` 메서드를 호출하여 모델의 전체 [collections](/docs/{{version}}/eloquent-collections)을 배열로 변환할 수도 있습니다.

    $users = User::all();

    return $users->toArray();

<a name="serializing-to-json"></a>
### Serializing To JSON
### JSON으로 Serializaing

To convert a model to JSON, you should use the `toJson` method. Like `toArray`, the `toJson` method is recursive, so all attributes and relations will be converted to JSON. You may also specify any JSON encoding options that are [supported by PHP](https://secure.php.net/manual/en/function.json-encode.php):

모델을 JSON으로 변환하기 위해서는, `toJson` 메소드를 사용해야 합니다. `toArray` 메소드와 마찬가지로 `toJson` 메서드는 재귀적이기 때문에, 모든 속성들과 relations-관계들은 JSON으로 변환됩니다. 또한 [PHP에서 지원하는](https://secure.php.net/manual/en/function.json-encode.php) JSON 인코딩 옵션을 지정할 수도 있습니다.

    use App\Models\User;

    $user = User::find(1);

    return $user->toJson();

    return $user->toJson(JSON_PRETTY_PRINT);

Alternatively, you may cast a model or collection to a string, which will automatically call the `toJson` method on the model or collection:

또는, 모델 또는 컬렉션을 문자열(string)으로 캐스팅하면, 자동으로 `toJson` 메소드가 호출 될것입니다.

    return (string) User::find(1);

Since models and collections are converted to JSON when cast to a string, you can return Eloquent objects directly from your application's routes or controllers. Laravel will automatically serialize your Eloquent models and collections to JSON when they are returned from routes or controllers:

모델과 컬렉션을 문자열로 캐스팅할 때에는, JSON으로 변환되므로, 애플리케이션의 라우트 또는 컨트롤러에서 Eloquent 객체를 바로 반환할 수도 있습니다. Laravel은 라우트나 컨트롤러에서 반환될 때 Eloquent 모델과 컬렉션을 자동으로 JSON으로 직렬화(serialize)합니다.

    Route::get('users', function () {
        return User::all();
    });

<a name="relationships"></a>
#### Relationships
#### 관계-Relationships

When an Eloquent model is converted to JSON, its loaded relationships will automatically be included as attributes on the JSON object. Also, though Eloquent relationship methods are defined using "camel case" method names, a relationship's JSON attribute will be "snake case".

Eloquent 모델을 JSON으로 변환하면 로드 된 관계가 자동으로 JSON 객체의 속성에 포함됩니다. 또한 Eloquent 관계 메서드는 "카멜 케이스"를 메서드 이름을 사용하여 정의되지만 관계의 JSON 속성은 "스네이크 케이스"가 됩니다.

<a name="hiding-attributes-from-json"></a>
## Hiding Attributes From JSON
## JSON 변환시 속성값 숨기기

Sometimes you may wish to limit the attributes, such as passwords, that are included in your model's array or JSON representation. To do so, add a `$hidden` property to your model. In attributes that are listed in the `$hidden` property's array will not be included in the serialized representation of your model:

때로는 패스워드와 같이, 모델이 배열 또는 JSON으로 재구성될 때 속성을 제한하고자 할 수도 있습니다. 이렇게 하기 위해서는 모델의 `$hidden` 속성에 제한하고자 하는 필드를 추가하면 됩니다. `$hidden` 속성의 배열에 나열된 속성에서 모델의 직렬화(serialize)된 표현에는 포함되지 않습니다.

    <?php

    namespace App\Models;

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

> {tip} To hide relationships, add the relationship's method name to your Eloquent model's `$hidden` property.

> {tip} 관계를 숨기려면 관계의 메소드 이름을 Eloquent 모델의 `$hidden` 속성에 추가하십시오.

Alternatively, you may use the `visible` property to define an "allow list" of attributes that should be included in your model's array and JSON representation. All attributes that are not present in the `$visible` array will be hidden when the model is converted to an array or JSON:

또한, `visible` 속성을 사용하여 모델의 배열 및 JSON 표현에 포함되어야 하는 속성의 "허용 목록"을 정의할 수 있습니다. `$visible` 배열에 없는 모든 속성은 모델이 배열이나 JSON으로 변환될 때 숨겨집니다.

    <?php

    namespace App\Models;

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

<a name="temporarily-modifying-attribute-visibility"></a>
#### Temporarily Modifying Attribute Visibility
#### 일시적으로 속성들의 노출 정도를 수정하기

If you would like to make some typically hidden attributes visible on a given model instance, you may use the `makeVisible` method. The `makeVisible` method returns the model instance:

주어진 모델 인스턴스에서 몇가지 일반적으로 숨겨진 속성을 표시하려면 `makeVisible` 메소드를 사용할 수 있습니다. `makeVisible` 메서드는 모델 인스턴스를 반환합니다.

    return $user->makeVisible('attribute')->toArray();

Likewise, if you would like to hide some attributes that are typically visible, you may use the `makeHidden` method.

마찬가지로, 일반적으로 표시되는 일부 속성을 숨기려면 `makeHidden` 메서드를 사용할 수 있습니다.

    return $user->makeHidden('attribute')->toArray();

<a name="appending-values-to-json"></a>
## Appending Values To JSON
## JSON 변환시 특정 값 추가하기

Occasionally, when converting models to arrays or JSON, you may wish to add attributes that do not have a corresponding column in your database. To do so, first define an [accessor](/docs/{{version}}/eloquent-mutators) for the value:

때때로, 모델이 배열이나 JSON으로 캐스팅 될 때, 데이터베이스의 컬럼에 일치하지 않는 속성들을 추가하기를 원할 수도 있습니다. 이렇게 하기 위해서는, 단순히 값에 대한 [accessor](/docs/{{version}}/eloquent-mutators)를 정의하면 됩니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * Determine if the user is an administrator.
         *
         * @return bool
         */
        public function getIsAdminAttribute()
        {
            return $this->attributes['admin'] === 'yes';
        }
    }

After creating the accessor, add the attribute name to the `appends` property of your model. Note that attribute names are typically referenced using their "snake case" serialized representation, even though the accessor's PHP method is defined using "camel case":

accessor 를 생성한 다음에, 모델의 `appends`값에 속성의 이름을 추가합니다. accessor 가 PHP 메서드가 "카멜 케이스" 를 사용하여 정의되더라도 속성 이름은 일반적으로 "스네이크 케이스"로 엑세스 됩니다.

    <?php

    namespace App\Models;

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

<a name="appending-at-run-time"></a>
#### Appending At Run Time
#### 실행 될때(run time에서) 추가하기

At runtime, you may instruct a model instance to append additional attributes using the `append` method. Or, you may use the `setAppends` method to override the entire array of appended properties for a given model instance:

런타임에서, `append` 메서드를 사용하여 속성을 추가하도록 모델 인스턴스에 지시할 수 있습니다. 또는 `setAppends` 메소드를 사용하여 주어진 모델 인스턴스에 대해 추가된 속성의 전체 배열을 오버라이드 할 수 있습니다.

    return $user->append('is_admin')->toArray();

    return $user->setAppends(['is_admin'])->toArray();

<a name="date-serialization"></a>
## Date Serialization
## 날짜 Serialization

<a name="customizing-the-default-date-format"></a>
#### Customizing The Default Date Format
#### 기본 날짜 형식 사용자 정의

You may customize the default serialization format by overriding the `serializeDate` method. This method does not affect how your dates are formatted for storage in the database:

`serializeDate` 메소드를 재정의하여 기본 시리얼라이즈 포맷을 원하는데로 설정 할 수 있습니다. 이 방법은 데이터베이스에 저장하기 위해 날짜 형식이 포맷되는 방식에 영향을 주지 않습니다.

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

<a name="customizing-the-date-format-per-attribute"></a>
#### Customizing The Date Format Per Attribute
#### 날짜 포맷의 속성을 커스터마이징 하기

You may customize the serialization format of individual Eloquent date attributes by specifying the date format in the model's [cast declarations](/docs/{{version}}/eloquent-mutators#attribute-casting):

만약 각각의 Eloquent 시리얼라이즈 포맷을 커스터마이즈 하고싶다면, 날짜 형식은 모델의 [캐스트 선언](/docs/{{version}}/eloquent-mutators#attribute-casting)에 자세하게 명시되어 있습니다.

    protected $casts = [
        'birthday' => 'date:Y-m-d',
        'joined_at' => 'datetime:Y-m-d H:00',
    ];
