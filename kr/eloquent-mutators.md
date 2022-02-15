# Eloquent: Mutators & Casting
# Eloquent: Mutators & Casting

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Accessors & Mutators](#accessors-and-mutators)
- [Accessors & Mutators](#accessors-and-mutators)
    - [Defining An Accessor](#defining-an-accessor)
    - [Accessor 정의하기](#defining-an-accessor)
    - [Defining A Mutator](#defining-a-mutator)
    - [Mutator 정의하기](#defining-a-mutator)
- [Attribute Casting](#attribute-casting)
- [속성(Attribute) 캐스팅](#attribute-casting) 
  - [Array & JSON Casting](#array-and-json-casting)
  - [배열 & JSON 캐스팅](#array-and-json-casting)
  - [Date Casting](#date-casting)
  - [날짜 캐스팅](#date-casting)
  - [Enum Casting](#enum-casting)
  - [Enum Casting](#enum-casting)
  - [Encrypted Casting](#encrypted-casting)
  - [암호화 캐스팅](#encrypted-casting)
  - [Query Time Casting](#query-time-casting)
  - [쿼리 시간 캐스팅](#query-time-casting)
- [Custom Casts](#custom-casts)
- [커스텀 캐스트](#custom-casts)
  - [Value Object Casting](#value-object-casting)
  - [밸류 오브젝트 캐스팅](#value-object-casting)
  - [Array / JSON Serialization](#array-json-serialization)
  - [배열 / JSON Serialization](#array-json-serialization)
  - [Inbound Casting](#inbound-casting)
  - [인바운드 캐스팅](#inbound-casting)
  - [Cast Parameters](#cast-parameters)
  - [캐스트 파라미터](#cast-parameters)
  - [Castables](#castables)
  - [Castables](#castables)


<a name="introduction"></a>
## Introduction
## 시작하기

Accessors and mutators allow you to format Eloquent attribute values when you retrieve or set them on model instances. For example, you may want to use the [Laravel encrypter](/docs/{{version}}/encryption) to encrypt a value while it is stored in the database, and then automatically decrypt the attribute when you access it on an Eloquent model.

Accessors와 mutators를 사용하면 모델 인스턴스에서 Eloquent 속성 값을 검색하거나 설정할 때 형식을 지정할 수 있습니다. 예를 들어, [라라벨 encrypter](docs/{{version}}/encryption)를 사용하여 값이 데이터베이스에 저장되어 있는 동안 암호화한 다음 Eloquent 모델에서 액세스할 때 속성을 자동으로 복호화할 수 있습니다.

In addition to custom accessors and mutators, Eloquent can also automatically cast date fields to [Carbon](https://github.com/briannesbitt/Carbon) instances or even [cast text fields to JSON](#attribute-casting).

사용자 정의 accessors 및 mutators 외에도, 자동으로 날짜 필드를 [Carbon](https://github.com/briannesbitt/Carbon) 인스턴스로 변환하거나 [텍스트 필드를 JSON으로 캐스팅](#attribute-casting)할 수도 있습니다.

In addition to user-specified accessors and mutators, Eloquent can also automatically cast date fields to [Carbon](https:github.combriannesbittCarbon) instances, or [attribute-casting text fields to JSON].

사용자가 지정한 accessors 와 mutators 에 더하여, Eloquent 는 또한 자동으로 날짜 필드를 [Carbon](https://github.com/briannesbitt/Carbon) 인스턴스로 캐스팅 하거나, [텍스트 필드를 JSON으로 캐스팅](#attribute-casting) 할 수 있습니다.

<a name="accessors-and-mutators"></a>
## Accessors & Mutators
## Accessors & Mutators

<a name="defining-an-accessor"></a>
### Defining An Accessor
### Accessor 정의하기

An accessor transforms an Eloquent attribute value when it is accessed. To define an accessor, create a `get{Attribute}Attribute` method on your model where `{Attribute}` is the "studly" cased name of the column you wish to access.

accessor는 접근할 때 Eloquent 속성 값을 변환합니다. accessor를 정의하려면 모델에 `get{Attribute}Attribute` 메서드를 생성합니다. 여기서 `{Attribute}`는 접근하려는 column의 "studly" 케이스 이름입니다.

In this example, we'll define an accessor for the `first_name` attribute. The accessor will automatically be called by Eloquent when attempting to retrieve the value of the `first_name` attribute:

아래 예제에서는 `first_name` 속성에 대한 accessor를 정의합니다. accessor는 `first_name` 속성의 값을 검색하려고 시도할 때 Eloquent에 의해 자동으로 호출됩니다:

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * Get the user's first name.
         *
         * @param  string  $value
         * @return string
         */
        public function getFirstNameAttribute($value)
        {
            return ucfirst($value);
        }
    }

As you can see, the original value of the column is passed to the accessor, allowing you to manipulate and return the value. To access the value of the accessor, you may simply access the `first_name` attribute on a model instance:

위 예제에서 볼 수 있듯이, 컬럼의 원래 값이 accessor 로 전달되고, 값을 가공하여 반환할 수 있습니다. accessor의 값에 엑세스하려면, 모델 인스턴스의 `first_name` 속성에 엑세스하면 됩니다:

    use App\Models\User;

    $user = User::find(1);

    $firstName = $user->first_name;

You are not limited to interacting with a single attribute within your accessor. You may also use accessors to return new, computed values from existing attributes:

accessor 내에서 단일 속성과 상호 작용하는 것으로 제한하지 않습니다. accessor를 사용하여 기존 속성에서 계산된 새 값을 반환할 수도 있습니다.

    /**
     * Get the user's full name.
     *
     * @return string
     */
    public function getFullNameAttribute()
    {
        return "{$this->first_name} {$this->last_name}";
    }

> {tip} If you would like these computed values to be added to the array / JSON representations of your model, [you will need to append them](/docs/{{version}}/eloquent-serialization#appending-values-to-json).
> {tip} 계산된 값을 모델의 배열 / JSON 표현에 추가하려면 [이 값을 추가해야 합니다](docs{{version}}eloquent-serializationappending-values-to-json).

<a name="defining-a-mutator"></a>
### Defining A Mutator
### Mutator 정의하기

A mutator transforms an Eloquent attribute value when it is set. To define a mutator, define a `set{Attribute}Attribute` method on your model where `{Attribute}` is the "studly" cased name of the column you wish to access.

mutator는 Eloquent 속성 값이 설정될 때 변환됩니다. mutator를 정의하려면 모델에 `set{Attribute}Attribute` 메소드를 정의하십시오. 여기서 `{Attribute}`는 액세스하려는 Column의 "studly" Case 이름입니다.

Let's define a mutator for the `first_name` attribute. This mutator will be automatically called when we attempt to set the value of the `first_name` attribute on the model:

`first_name` 속성에 대한 mutator를 정의해 보겠습니다. 이 mutator는 모델의 `first_name` 속성 값을 설정하려고 할 때 자동으로 호출됩니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * Set the user's first name.
         *
         * @param  string  $value
         * @return void
         */
        public function setFirstNameAttribute($value)
        {
            $this->attributes['first_name'] = strtolower($value);
        }
    }

The mutator will receive the value that is being set on the attribute, allowing you to manipulate the value and set the manipulated value on the Eloquent model's internal `$attributes` property. To use our mutator, we only need to set the `first_name` attribute on an Eloquent model:

mutator 는 속성에 설정하고자 하는 값을 전달 받아, 값을 변형하고, 변형된 값을 Eloquent 모델의 `$attributes` 속성에 지정할 것입니다. mutator를 사용하려면, Eloquent 모델에 `first_name` 속성만 설정하면 됩니다.

    use App\Models\User;

    $user = User::find(1);

    $user->first_name = 'Sally';

In this example, the `setFirstNameAttribute` function will be called with the value `Sally`. The mutator will then apply the `strtolower` function to the name and set its resulting value in the internal `$attributes` array.

이 예제에서 `setFirstNameAttribute` 함수는 `Sally` 라는 값과 함께 호출 될것입니다. mutator 는 이름에 대해 `strtolower` 함수가 실행되도록 하여 그 결과 값을 내부 `$attributes` 배열에 지정할 것입니다.

<a name="attribute-casting"></a>
## Attribute Casting
## 속성(Attribute) 캐스팅

Attribute casting provides functionality similar to accessors and mutators without requiring you to define any additional methods on your model. Instead, your model's `$casts` property provides a convenient method of converting attributes to common data types.

속성(Attribute) 캐스팅은 모델에 추가 메서드를 정의할 필요 없이 accessors 및 mutators와 유사한 기능을 제공합니다.  대신, 모델의 'casts' 속성을 사용하면 속성을 일반적인 데이터 유형으로 편리하게 변환할 수 있습니다.

The `$casts` property should be an array where the key is the name of the attribute being cast and the value is the type you wish to cast the column to. The supported cast types are:

`$casts` 속성은 key가 캐스트되는 속성의 이름이고 value가 column을 캐스팅하려는 Data Type이 배열이어야 합니다. 지원되는 캐스트 Data Type은 다음과 같습니다.

<div class="content-list" markdown="1">
- `array`
- `AsStringable::class`
- `boolean`
- `collection`
- `date`
- `datetime`
- `immutable_date`
- `immutable_datetime`
- `decimal:`<code>&lt;digits&gt;</code>
- `double`
- `encrypted`
- `encrypted:array`
- `encrypted:collection`
- `encrypted:object`
- `float`
- `integer`
- `object`
- `real`
- `string`
- `timestamp`
</div>

To demonstrate attribute casting, let's cast the `is_admin` attribute, which is stored in our database as an integer (`0` or `1`) to a boolean value:

속성 캐스팅을 시연하기 위해, 데이터베이스에 정수(`0` 또는 `1`)로 저장된 `is_admin` 속성을 Boolean 값으로 캐스팅해 보겠습니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * The attributes that should be cast.
         *
         * @var array
         */
        protected $casts = [
            'is_admin' => 'boolean',
        ];
    }

After defining the cast, the `is_admin` attribute will always be cast to a boolean when you access it, even if the underlying value is stored in the database as an integer:

캐스트를 정의한 후에는, 기본 값이 데이터베이스에 정수로 저장되어 있더라도 액세스할 때 `is_admin` 속성이 항상 boolean 으로 캐스트됩니다.

    $user = App\Models\User::find(1);

    if ($user->is_admin) {
        //
    }

If you need to add a new, temporary cast at runtime, you may use the `mergeCasts` method. These cast definitions will be added to any of the casts already defined on the model:

런타임에 임시 캐스트를 새로 추가해야 하는 경우, `mergeCasts` 메서드를 사용할 수 있습니다. 이러한 캐스트 정의는 모델에 이미 정의된 캐스트에 추가됩니다.

    $user->mergeCasts([
        'is_admin' => 'integer',
        'options' => 'object',
    ]);

> {note} Attributes that are `null` will not be cast. In addition, you should never define a cast (or an attribute) that has the same name as a relationship.

> {note} `null`인 속성은 캐스트되지 않습니다. 또한 관계와 이름이 같은 캐스트(또는 속성)를 정의하면 안 됩니다.

<a name="stringable-casting"></a>
### Stringable Casting
### 스트링 가능한(Stringable) 캐스팅

You may use the `Illuminate\Database\Eloquent\Casts\AsStringable` cast class to cast a model attribute to a [fluent `Illuminate\Support\Stringable` object](/docs/{{version}}/helpers#fluent-strings-method-list):

`Illuminate\Database\Eloquent\Casts\AsStringable` 캐스트 클래스를 사용하여 모델 속성을 [유창한 `Illuminate\Support\Stringable` 객체](/docs/{{version}}/helpers#fluent-strings-method-list)로 캐스팅할 수 있습니다. :

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Casts\AsStringable;
    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * The attributes that should be cast.
         *
         * @var array
         */
        protected $casts = [
            'directory' => AsStringable::class,
        ];
    }

<a name="array-and-json-casting"></a>
### Array & JSON Casting
### 배열 및 JSON 캐스팅

The `array` cast is particularly useful when working with columns that are stored as serialized JSON. For example, if your database has a `JSON` or `TEXT` field type that contains serialized JSON, adding the `array` cast to that attribute will automatically deserialize the attribute to a PHP array when you access it on your Eloquent model:

`array` 캐스트는 직렬화된 JSON으로 저장된 columns 으로 작업할 때 특히 유용합니다. 예를 들어, 데이터베이스에 직렬화된 JSON이 포함된 `JSON` 또는 `TEXT` 필드 유형이 있는 경우 해당 속성에 `Array` 캐스트를 추가하면 Eloquent 모델에서 액세스할 때 PHP 배열에 속성을 자동으로 역직렬화합니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * The attributes that should be cast.
         *
         * @var array
         */
        protected $casts = [
            'options' => 'array',
        ];
    }

Once the cast is defined, you may access the `options` attribute and it will automatically be deserialized from JSON into a PHP array. When you set the value of the `options` attribute, the given array will automatically be serialized back into JSON for storage:

캐스트가 정의되면 `options` 속성에 액세스할 수 있으며 JSON에서 PHP 배열로 자동으로 역직렬화됩니다. `options` 속성 값을 설정하면 지정된 배열이 JSON으로 자동 직렬화되어 저장됩니다.

    use App\Models\User;

    $user = User::find(1);

    $options = $user->options;

    $options['key'] = 'value';

    $user->options = $options;

    $user->save();

To update a single field of a JSON attribute with a more terse syntax, you may use the `->` operator when calling the `update` method:

더 간단한 구문으로 JSON 속성의 단일 필드를 업데이트하려면, `update` 메소드를 호출할 때 `->` 연산자를 사용할 수 있습니다.

    $user = User::find(1);

    $user->update(['options->key' => 'value']);

<a name="array-object-and-collection-casting"></a>
#### Array Object & Collection Casting
#### 배열 객체 및 컬렉션 캐스팅

Although the standard `array` cast is sufficient for many applications, it does have some disadvantages. Since the `array` cast returns a primitive type, it is not possible to mutate an offset of the array directly. For example, the following code will trigger a PHP error:

표준 `array` 캐스트는 많은 애플리케이션에 충분하지만 몇 가지 단점이 있습니다. 'array' 캐스트는 기본 타입을 반환하므로 배열의 오프셋을 직접 변경할 수 없습니다. 예를 들어 다음 코드는 PHP 오류를 트리거합니다.

    $user = User::find(1);

    $user->options['key'] = $value;

To solve this, Laravel offers an `AsArrayObject` cast that casts your JSON attribute to an [ArrayObject](https://www.php.net/manual/en/class.arrayobject.php) class. This feature is implemented using Laravel's [custom cast](#custom-casts) implementation, which allows Laravel to intelligently cache and transform the mutated object such that individual offsets may be modified without triggering a PHP error. To use the `AsArrayObject` cast, simply assign it to an attribute:

이를 해결하기 위해 라라벨은 JSON 속성을 [ArrayObject](https://www.php.net/manual/en/class.arrayobject.php) 클래스로 변환하는 `AsArrayObject` 캐스트를 제공합니다. 이 기능은 라라벨의 [custom cast](#custom-casts) 를 사용하여 구현되며, 이를 통해 라라벨은 PHP 오류를 유발하지 않고 개별 오프셋을 수정할 수 있도록 변형된 객체를 영리하게 캐시하고 변환할 수 있습니다. `AsArrayObject` 캐스트를 사용하려면 속성에 할당하기만 하면 됩니다.

    use Illuminate\Database\Eloquent\Casts\AsArrayObject;

    /**
     * The attributes that should be cast.
     *
     * @var array
     */
    protected $casts = [
        'options' => AsArrayObject::class,
    ];

Similarly, Laravel offers an `AsCollection` cast that casts your JSON attribute to a Laravel [Collection](/docs/{{version}}/collections) instance:

마찬가지로, 라라벨은 JSON 속성을 라라벨 [Collection](docs{{version}}collections) 인스턴스로 변환하는 `AsCollection` 캐스트를 제공합니다.

    use Illuminate\Database\Eloquent\Casts\AsCollection;

    /**
     * The attributes that should be cast.
     *
     * @var array
     */
    protected $casts = [
        'options' => AsCollection::class,
    ];

<a name="date-casting"></a>
### Date Casting
### 날짜 캐스팅

By default, Eloquent will cast the `created_at` and `updated_at` columns to instances of [Carbon](https://github.com/briannesbitt/Carbon), which extends the PHP `DateTime` class and provides an assortment of helpful methods. You may cast additional date attributes by defining additional date casts within your model's `$casts` property array. Typically, dates should be cast using the `datetime` or `immutable_datetime` cast types.

기본적으로 Eloquent는 `created_at` 및 `updated_at` Columns을 [Carbon](https://github.com/briannesbitt/Carbon) 의 인스턴스로 캐스팅합니다. 이 인스턴스는 PHP `DateTime` 클래스를 확장하고 다양하고 유용한 메소드를 제공합니다. 모델의 `$casts` 속성의 배열 내에서 추가 날짜 캐스트를 정의하여 추가 날짜 속성을 캐스트할 수 있습니다. 일반적으로 날짜는 `datetime` 또는 `immutable_datetime` 캐스트 유형을 사용하여 캐스트해야 합니다.

When defining a `date` or `datetime` cast, you may also specify the date's format. This format will be used when the [model is serialized to an array or JSON](/docs/{{version}}/eloquent-serialization):

`date` 또는 `datetime` 캐스트를 정의할 때 날짜 형식도 지정할 수 있습니다. 이 형식은 [모델이 배열 또는 JSON으로 직렬화](/docs/{{version}}/eloquent-serialization) 될 때 사용합니다.

    /**
     * The attributes that should be cast.
     *
     * @var array
     */
    protected $casts = [
        'created_at' => 'datetime:Y-m-d',
    ];

When a column is cast as a date, you may set the corresponding model attribute value to a UNIX timestamp, date string (`Y-m-d`), date-time string, or a `DateTime` / `Carbon` instance. The date's value will be correctly converted and stored in your database.

Column이 날짜로 캐스팅되면 해당 모델 속성 값을 UNIX 타임스탬프, 날짜 문자열(`Y-m-d`), 날짜-시간에 대한 문자열, 또는 `DateTime` / `Carbon` 인스턴스로 설정할 수 있습니다. 날짜 값이 올바르게 변환되어 데이터베이스에 저장됩니다.

You may customize the default serialization format for all of your model's dates by defining a `serializeDate` method on your model. This method does not affect how your dates are formatted for storage in the database:

모델에 `serializeDate` 메소드를 정의하여 모든 모델의 날짜에 대한 기본 직렬화 형식을 사용자 정의할 수 있습니다. 이 방법은 데이터베이스에 저장하기 위해 날짜 형식이 지정되는 방식에 영향을 주지 않습니다.

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

To specify the format that should be used when actually storing a model's dates within your database, you should define a `$dateFormat` property on your model:

모델의 날짜를 실제로 데이터베이스에 저장할 때 사용할 형식을 지정하려면 모델에 `$dateFormat` 속성을 정의해야 합니다.

    /**
     * The storage format of the model's date columns.
     *
     * @var string
     */
    protected $dateFormat = 'U';

<a name="date-casting-and-timezones"></a>
#### Date Casting, Serialization, & Timezones
#### 날짜 캐스팅, 직렬화 & 타임존

By default, the `date` and `datetime` casts will serialize dates to a UTC ISO-8601 date string (`1986-05-28T21:05:54.000000Z`), regardless of the timezone specified in your application's `timezone` configuration option. You are strongly encouraged to always use this serialization format, as well as to store your application's dates in the UTC timezone by not changing your application's `timezone` configuration option from its default `UTC` value. Consistently using the UTC timezone throughout your application will provide the maximum level of interoperability with other date manipulation libraries written in PHP and JavaScript.

기본적으로 `date` 및 `datetime` 캐스트는 애플리케이션의 `timezone` 구성 옵션에 지정된 시간대에 관계없이 UTC ISO-8601 날짜 문자열(`1986-05-28T21:05:54.0000Z`) 로 날짜를 직렬화합니다. 응용 프로그램의 `timezone` 구성 옵션을 기본 `UTC` 값에서 변경하지 않음으로써 애플리케이션의 날짜를 UTC 표준 시간대에 저장하는 것은 물론 직렬화 형식을 사용하는 것을 강력히 권장합니다. 애플리케이션 전체에서 UTC 시간대를 일관되게 사용하면 PHP 및 JavaScript로 작성된 다른 날짜 라이브러리와의 최대 상호 운용성을 제공할 수 있습니다. 

If a custom format is applied to the `date` or `datetime` cast, such as `datetime:Y-m-d H:i:s`, the inner timezone of the Carbon instance will be used during date serialization. Typically, this will be the timezone specified in your application's `timezone` configuration option.

`date` 또는 `datetime` 캐스트에 사용자 지정 형식이 적용되는 경우(예: `datetime:Y-m-d H:i:s`), 날짜 직렬화 중에 Carbon 인스턴스의 내부 시간대가 사용됩니다. 일반적으로 애플리케이션의 `timezone` 구성 옵션에 지정된 시간대가 됩니다.

<a name="enum-casting"></a>
### Enum Casting
### 열거형 캐스팅

> {note} Enum casting is only available for PHP 8.1+.
> {note} 열거형 캐스팅은 PHP 8.1 이상에서만 사용할 수 있습니다.

Eloquent also allows you to cast your attribute values to PHP enums. To accomplish this, you may specify the attribute and enum you wish to cast in your model's `$casts` property array:

Eloquent를 사용하면 속성 값을 PHP 열거형으로 캐스팅할 수도 있습니다. 이를 수행하기 위해 모델의 'casts' 속성 배열에 캐스팅하려는 속성과 열거형을 지정할 수 있습니다.

    use App\Enums\ServerStatus;

    /**
     * The attributes that should be cast.
     *
     * @var array
     */
    protected $casts = [
        'status' => ServerStatus::class,
    ];

Once you have defined the cast on your model, the specified attribute will be automatically cast to and from an enum when you interact with the attribute:

모델에 대한 캐스트를 정의하고 나면 속성과 상호 작용할 때 지정된 속성이 열거형으로 자동으로 캐스트되거나 열거형에서 캐스트됩니다.

    if ($server->status == ServerStatus::provisioned) {
        $server->status = ServerStatus::ready;

        $server->save();
    }

<a name="encrypted-casting"></a>
### Encrypted Casting
### 암호화된 캐스팅

The `encrypted` cast will encrypt a model's attribute value using Laravel's built-in [encryption](/docs/{{version}}/encryption) features. In addition, the `encrypted:array`, `encrypted:collection`, `encrypted:object`, `AsEncryptedArrayObject`, and `AsEncryptedCollection` casts work like their unencrypted counterparts; however, as you might expect, the underlying value is encrypted when stored in your database.

`encrypted` 캐스트는 라라벨에 내장 [encryption](/docs/{{version}}/encryption) 기능을 사용하여 모델의 속성 값을 암호화합니다. 또한 `encrypted:array`, `encrypted:collection`, `encrypted:object`, `AsEncryptedArrayObject` 및 `AsEncryptedCollection` 캐스트는 암호화되지 않은것 처럼 작동합니다. 그러나 예상대로 기본 값은 데이터베이스에 저장될 때 암호화됩니다.

As the final length of the encrypted text is not predictable and is longer than its plain text counterpart, make sure the associated database column is of `TEXT` type or larger. In addition, since the values are encrypted in the database, you will not be able to query or search encrypted attribute values.

암호화된 텍스트의 최종 길이는 예측할 수 없고 일반 텍스트보다 길기 때문에 연결된 데이터베이스의 Column 이 'TEXT' 타입 또는 그 이상인지 확인하십시오. 또한 값이 데이터베이스에서 암호화되기 때문에, 암호화된 속성 값을 쿼리하거나 검색할 수 없습니다.

<a name="query-time-casting"></a>
### Query Time Casting
### 쿼리 시간 캐스팅

Sometimes you may need to apply casts while executing a query, such as when selecting a raw value from a table. For example, consider the following query:
때로는 테이블에서 원시 값을 선택할 때와 같이 쿼리를 실행하는 동안 캐스트를 적용해야 할 수도 있습니다. 예를 들어 다음 쿼리를 고려하십시오.

    use App\Models\Post;
    use App\Models\User;

    $users = User::select([
        'users.*',
        'last_posted_at' => Post::selectRaw('MAX(created_at)')
                ->whereColumn('user_id', 'users.id')
    ])->get();

The `last_posted_at` attribute on the results of this query will be a simple string. It would be wonderful if we could apply a `datetime` cast to this attribute when executing the query. Thankfully, we may accomplish this using the `withCasts` method:
이 쿼리 결과의 'last_posted_at' 속성은 간단한 문자열입니다. 쿼리를 실행할 때 이 속성에 'datetime' 캐스트를 적용할 수 있다면 정말 좋을 것입니다. 고맙게도 'withCasts' 메서드를 사용하여 이 작업을 수행할 수 있습니다.

    $users = User::select([
        'users.*',
        'last_posted_at' => Post::selectRaw('MAX(created_at)')
                ->whereColumn('user_id', 'users.id')
    ])->withCasts([
        'last_posted_at' => 'datetime'
    ])->get();

<a name="custom-casts"></a>
## Custom Casts
## 커스텀 캐스트

Laravel has a variety of built-in, helpful cast types; however, you may occasionally need to define your own cast types. You may accomplish this by defining a class that implements the `CastsAttributes` interface.

라라벨은 다양한 캐스트 타입 내장하고 있습니다 하지만 캐스트 유형을 직접 정의 해야 할 때도 있습니다. `CastsAttributes` 인터페이스를 구현하는 클래스를 정의하여 사용 할 수 있습니다.

Classes that implement this interface must define a `get` and `set` method. The `get` method is responsible for transforming a raw value from the database into a cast value, while the `set` method should transform a cast value into a raw value that can be stored in the database. As an example, we will re-implement the built-in `json` cast type as a custom cast type:

이 인터페이스를 구현하는 클래스는 반드시 `get`과 `set` 메소드를 구현해야합니다. `get` 메소드는 데이터베이스의 원시 값을 캐스트 된 값으로 변환하는 역활을 합니다, 반면 `set`메소드는 데이터베이스에 저장할 수 있는 원시 값으로 변환해야 합니다, 예제로 내장된 `json` 캐스팅 타입을 커스텀 캐스트 타입으로 다시 구현하였습니다.

    <?php

    namespace App\Casts;

    use Illuminate\Contracts\Database\Eloquent\CastsAttributes;

    class Json implements CastsAttributes
    {
        /**
         * Cast the given value.
         *
         * @param  \Illuminate\Database\Eloquent\Model  $model
         * @param  string  $key
         * @param  mixed  $value
         * @param  array  $attributes
         * @return array
         */
        public function get($model, $key, $value, $attributes)
        {
            return json_decode($value, true);
        }

        /**
         * Prepare the given value for storage.
         *
         * @param  \Illuminate\Database\Eloquent\Model  $model
         * @param  string  $key
         * @param  array  $value
         * @param  array  $attributes
         * @return string
         */
        public function set($model, $key, $value, $attributes)
        {
            return json_encode($value);
        }
    }

Once you have defined a custom cast type, you may attach it to a model attribute using its class name:

한번 커스텀 캐스트 타입을 정의하면, 해당 클래스 이름으로 모델 속성에 추가 할 수 있습니다.

    <?php

    namespace App\Models;

    use App\Casts\Json;
    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * The attributes that should be cast.
         *
         * @var array
         */
        protected $casts = [
            'options' => Json::class,
        ];
    }

<a name="value-object-casting"></a>
### Value Object Casting
### 밸류 오브젝트 캐스팅

You are not limited to casting values to primitive types. You may also cast values to objects. Defining custom casts that cast values to objects is very similar to casting to primitive types; however, the `set` method should return an array of key / value pairs that will be used to set raw, storable values on the model.

원시 타입의 캐스팅 이외에 오브젝트의 값도 캐스트 할 수 있습니다. 오브젝트의 값의 커스텀 캐스트 정의는 원시 타입의 커스텀 캐스팅과 매우 비슷합니다, 하지만 `set` 메소드는 모델에서 저장 가능한 원시 값을 설정할 때 사용되는 key / value 쌍의 배열을 리턴 해야 합니다.

As an example, we will define a custom cast class that casts multiple model values into a single `Address` value object. We will assume the `Address` value has two public properties: `lineOne` and `lineTwo`:

예제로 하나의 `Address`값을 가지는 밸류 오브젝트를 여러 모델 값을 캐스트 하는 커스텀 캐스트 클래스 정의했습니다, `Address` 값에는 `lineOne`과 `lineTwo`라는 두 가지 퍼블릭 프로퍼티가 있다고 가정하겠습니다.

    <?php

    namespace App\Casts;

    use App\Models\Address as AddressModel;
    use Illuminate\Contracts\Database\Eloquent\CastsAttributes;
    use InvalidArgumentException;

    class Address implements CastsAttributes
    {
        /**
         * Cast the given value.
         *
         * @param  \Illuminate\Database\Eloquent\Model  $model
         * @param  string  $key
         * @param  mixed  $value
         * @param  array  $attributes
         * @return \App\Models\Address
         */
        public function get($model, $key, $value, $attributes)
        {
            return new AddressModel(
                $attributes['address_line_one'],
                $attributes['address_line_two']
            );
        }

        /**
         * Prepare the given value for storage.
         *
         * @param  \Illuminate\Database\Eloquent\Model  $model
         * @param  string  $key
         * @param  \App\Models\Address  $value
         * @param  array  $attributes
         * @return array
         */
        public function set($model, $key, $value, $attributes)
        {
            if (! $value instanceof AddressModel) {
                throw new InvalidArgumentException('The given value is not an Address instance.');
            }

            return [
                'address_line_one' => $value->lineOne,
                'address_line_two' => $value->lineTwo,
            ];
        }
    }

When casting to value objects, any changes made to the value object will automatically be synced back to the model before the model is saved:
값 개체로 캐스팅할 때 값 개체에 대한 변경 사항은 모델이 저장되기 전에 자동으로 모델에 자동 동기화됩니다.

    use App\Models\User;

    $user = User::find(1);

    $user->address->lineOne = 'Updated Address Value';

    $user->save();

> {tip} If you plan to serialize your Eloquent models containing value objects to JSON or arrays, you should implement the `Illuminate\Contracts\Support\Arrayable` and `JsonSerializable` interfaces on the value object.
> {tip} 값 개체를 포함하는 Eloquent 모델을 JSON 또는 배열로 직렬화하려는 경우 값 개체에 `Illuminate\Contracts\Support\Arrayable` 및 `JsonSerializable` 인터페이스를 구현해야 합니다.

<a name="array-json-serialization"></a>
### Array / JSON Serialization
### Array / JSON Serialization

When an Eloquent model is converted to an array or JSON using the `toArray` and `toJson` methods, your custom cast value objects will typically be serialized as well as long as they implement the `Illuminate\Contracts\Support\Arrayable` and `JsonSerializable` interfaces. However, when using value objects provided by third-party libraries, you may not have the ability to add these interfaces to the object.

Eloquent 모델이 `toArray` 및 `toJson` 메소드를 사용하여 배열 또는 JSON으로 변환되는 경우, 사용자 지정 캐스트 값 객체는 일반적으로 `Illuminate\Contracts\Support\Arrayable` 및 `JsonSerializable` 인터페이스를 구현하는 한 직렬화가됩니다. 그러나 타사 라이브러리에서 제공하는 값 개체를 사용하는 경우 이러한 인터페이스를 개체에 추가하지 못할 수 있습니다.

Therefore, you may specify that your custom cast class will be responsible for serializing the value object. To do so, your custom cast class should implement the `Illuminate\Contracts\Database\Eloquent\SerializesCastableAttributes` interface. This interface states that your class should contain a `serialize` method which should return the serialized form of your value object:

따라서 사용자 지정 캐스트 클래스가 값 객체를 직렬화하도록 지정할 수 있습니다. 이렇게 하려면 사용자 지정 캐스트 클래스가 `Illuminate\Contracts\Database\Eloquent\SerializesCastableAttributes` 인터페이스를 구현해야 합니다. 이 인터페이스는 클래스에 값 개체의 직렬화된 형식을 반환해야 하는 `serialize` 메서드를 포함해야 한다고 명시하고 있습니다:


    /**
     * Get the serialized representation of the value.
     *
     * @param  \Illuminate\Database\Eloquent\Model  $model
     * @param  string  $key
     * @param  mixed  $value
     * @param  array  $attributes
     * @return mixed
     */
    public function serialize($model, string $key, $value, array $attributes)
    {
        return (string) $value;
    }

<a name="inbound-casting"></a>
### Inbound Casting
### 인바운드 캐스팅

Occasionally, you may need to write a custom cast that only transforms values that are being set on the model and does not perform any operations when attributes are being retrieved from the model. A classic example of an inbound only cast is a "hashing" cast. Inbound only custom casts should implement the `CastsInboundAttributes` interface, which only requires a `set` method to be defined.

가끔은 모델에서 설정 중인 값만 변환하고 속성을 검색하지 않는 캐스트를 작성해야 할 필요가 있습니다. 인바운드 캐스팅의 전형적인 예제는 "해싱"입니다, 인바운드 커스텀 캐스트는 `set` 메소드만 정의하면 되는 `CastsInboundAttributes`인터페이스를 구현 해야 합니다.

    <?php

    namespace App\Casts;

    use Illuminate\Contracts\Database\Eloquent\CastsInboundAttributes;

    class Hash implements CastsInboundAttributes
    {
        /**
         * The hashing algorithm.
         *
         * @var string
         */
        protected $algorithm;

        /**
         * Create a new cast class instance.
         *
         * @param  string|null  $algorithm
         * @return void
         */
        public function __construct($algorithm = null)
        {
            $this->algorithm = $algorithm;
        }

        /**
         * Prepare the given value for storage.
         *
         * @param  \Illuminate\Database\Eloquent\Model  $model
         * @param  string  $key
         * @param  array  $value
         * @param  array  $attributes
         * @return string
         */
        public function set($model, $key, $value, $attributes)
        {
            return is_null($this->algorithm)
                        ? bcrypt($value)
                        : hash($this->algorithm, $value);
        }
    }

<a name="cast-parameters"></a>
### Cast Parameters
### 캐스트 파라메터

When attaching a custom cast to a model, cast parameters may be specified by separating them from the class name using a `:` character and comma-delimiting multiple parameters. The parameters will be passed to the constructor of the cast class:

모델에 커스텀 캐스트를 추가할 때 캐스트 파라메터는 `:`문자를 사용하여 클래스 이름 쉼표로 구분된 여러 파라메터로 구분하여 지정할 수 있습니다. 파라메터는 캐스트 클래스의 생성자로 전달됩니다.

    /**
     * The attributes that should be cast.
     *
     * @var array
     */
    protected $casts = [
        'secret' => Hash::class.':sha256',
    ];

<a name="castables"></a>
### Castables
### Castables

You may want to allow your application's value objects to define their own custom cast classes. Instead of attaching the custom cast class to your model, you may alternatively attach a value object class that implements the `Illuminate\Contracts\Database\Eloquent\Castable` interface:

애플리케이션의 값 개체가 고유한 사용자 지정 캐스트 클래스를 정의하도록 허용할 수 있습니다. 사용자 지정 캐스트 클래스를 모델에 연결하는 대신 `Illuminate\Contracts\Database\Eloquent\Castable` 인터페이스를 구현하는 값 개체 클래스를 대안으로 연결할 수 있습니다.

    use App\Models\Address;

    protected $casts = [
        'address' => Address::class,
    ];

Objects that implement the `Castable` interface must define a `castUsing` method that returns the class name of the custom caster class that is responsible for casting to and from the `Castable` class:

`Castable` 인터페이스를 구현하는 객체는 `Castable` 클래스로의 캐스트를 담당하는 커스텀 캐스터 클래스의 클래스 이름을 리턴하는 `castUsing` 메소드를 정의해야합니다.


    <?php

    namespace App\Models;

    use Illuminate\Contracts\Database\Eloquent\Castable;
    use App\Casts\Address as AddressCast;

    class Address implements Castable
    {
        /**
         * Get the name of the caster class to use when casting from / to this cast target.
         *
         * @param  array  $arguments
         * @return string
         */
        public static function castUsing(array $arguments)
        {
            return AddressCast::class;
        }
    }

When using `Castable` classes, you may still provide arguments in the `$casts` definition. The arguments will be passed to the `castUsing` method:

'Castable' 클래스를 사용할 때 `$casts` 정의에 인수를 제공할 수 있습니다. 인수는 `castUsing` 메서드에 전달됩니다.

    use App\Models\Address;

    protected $casts = [
        'address' => Address::class.':argument',
    ];

<a name="anonymous-cast-classes"></a>
#### Castables & Anonymous Cast Classes
#### 캐스터블 및 익명 캐스트 클래스

By combining "castables" with PHP's [anonymous classes](https://www.php.net/manual/en/language.oop5.anonymous.php), you may define a value object and its casting logic as a single castable object. To accomplish this, return an anonymous class from your value object's `castUsing` method. The anonymous class should implement the `CastsAttributes` interface:

"castables"을 PHP의 [익명 클래스](https://www.php.net/manual/en/language.oop5.anonymous.php)와 결합함으로써 값 객체와 해당 캐스팅 논리를 단일 castable 객체로 정의할 수 있습니다. 이를 수행하려면 값 개체의 'castUsing' 메서드에서 익명 클래스를 반환합니다. 익명 클래스는 `CastsAttributes` 인터페이스를 구현해야 합니다.

    <?php

    namespace App\Models;

    use Illuminate\Contracts\Database\Eloquent\Castable;
    use Illuminate\Contracts\Database\Eloquent\CastsAttributes;

    class Address implements Castable
    {
        // ...

        /**
         * Get the caster class to use when casting from / to this cast target.
         *
         * @param  array  $arguments
         * @return object|string
         */
        public static function castUsing(array $arguments)
        {
            return new class implements CastsAttributes
            {
                public function get($model, $key, $value, $attributes)
                {
                    return new Address(
                        $attributes['address_line_one'],
                        $attributes['address_line_two']
                    );
                }

                public function set($model, $key, $value, $attributes)
                {
                    return [
                        'address_line_one' => $value->lineOne,
                        'address_line_two' => $value->lineTwo,
                    ];
                }
            };
        }
    }
