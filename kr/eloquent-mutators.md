# Eloquent: Mutators
# Eloquent: Mutators

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Accessors & Mutators](#accessors-and-mutators)
- [Accessors & Mutators](#accessors-and-mutators)
- [Date Mutators](#date-mutators)
- [날짜 Mutators](#date-mutators)
- [Attribute Casting](#attribute-casting)
- [속성(Attribute) 캐스팅](#attribute-casting)

<a name="introduction"></a>
## Introduction
## 시작하기

Accessors and mutators allow you to format Eloquent attributes when retrieving them from a model or setting their value. For example, you may want to use the [Laravel encrypter](/docs/{{version}}/encryption) to encrypt a value while it is stored in the database, and then automatically decrypt the attribute when you access it on an Eloquent model.

Accessors 와 mutators 는 Eloquent 속성을 찾을 때나 값을 설정할 때 포맷을 가능하게 합니다. 예를 들어 데이터베이스에서 값을 저장할 때, [라라벨 encrypter](/docs/{{version}}/encryption)을 사용하여 값을 암호화 하고, Eloquent 모델에서 값에 엑세스 할 때 자동으로 복호화 하기를 원할 수도 있습니다. 

In addition to custom accessors and mutators, Eloquent can also automatically cast date fields to [Carbon](https://github.com/briannesbitt/Carbon) instances or even [cast text fields to JSON](#attribute-casting).

사용자가 지정한 accessors 와 mutators 에 더하여, Eloquent 는 또한 자동으로 날짜 필드를 [Carbon](https://github.com/briannesbitt/Carbon) 인스턴스로 캐스팅 하거나, [텍스트 필드를 JSON으로 캐스팅](#attribute-casting) 할 수 있습니다. 

<a name="accessors-and-mutators"></a>
## Accessors & Mutators
## Accessors & Mutators

#### Defining An Accessor
#### Accessor 정의하기

To define an accessor, create a `getFooAttribute` method on your model where `Foo` is the "camel" cased name of the column you wish to access. In this example, we'll define an accessor for the `first_name` attribute. The accessor will automatically be called by Eloquent when attempting to retrieve the value of `first_name`:

accessor를 정의하기 위해서, `Foo` 모델에 접근하고자 하는 컬럼을 위한 `getFooAttribute` 메소드를 "카멜" 케이스 형태의 이름으로 생성합니다. 다음 예제에서는 `first_name` 컬럽에 대한 accessor를 정의할 것입니다. accessor는 `first_name` 값이 찾아졌을 때 Eloquent 에 의해서 자동으로 호출될 것입니다. 

    <?php

    namespace App;

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

As you can see, the original value of the column is passed to the accessor, allowing you to manipulate and return the value. To access the value of the mutator, you may simply access the `first_name` attribute:

위에서 볼 수 있듯이, 컬럼의 원래 값이 accessor 로 전달되고, 값을 가공하여 반환됩니다. 변형된 값에 액세스하려면, 간단하게 `first_name` 속성에 액세스하면 됩니다. 

    $user = App\User::find(1);

    $firstName = $user->first_name;

#### Defining A Mutator
#### Mutator 정의하기

To define a mutator, define a `setFooAttribute` method on your model where `Foo` is the "camel" cased name of the column you wish to access. So, again, let's define a mutator for the `first_name` attribute. This mutator will be automatically called when we attempt to set the value of the `first_name` attribute on the model:

mutator 를 정의하기 위해서, `Foo` 모델 클래스에 엑세스 하고자 하는 컬럼을 위한 `setFooAttribute` 메소드를 "카멜" 케이스 형태의 이름으로 정의합니다. 이번에도 `first_name` 속성에 대해서 mutator를 정의하겠습니다. 이 mutator는 모델에서 `first_name` 속성을 변경하려고 할 때, 자동으로 호출 될 것입니다. 

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * Set the user's first name.
         *
         * @param  string  $value
         * @return string
         */
        public function setFirstNameAttribute($value)
        {
            $this->attributes['first_name'] = strtolower($value);
        }
    }

The mutator will receive the value that is being set on the attribute, allowing you to manipulate the value and set the manipulated value on the Eloquent model's internal `$attributes` property. So, for example, if we attempt to set the `first_name` attribute to `Sally`:

mutator 는 속성에 설정하고자 하는 값을 전달 받아, 값을 변형하고, 변형된 값을 Eloquent 모델의 `$attributes` 속성에 지정할 것입니다. 다음 처럼`first_name` 속성을 `Sally` 로 지정해 보겠습니다. 

    $user = App\User::find(1);

    $user->first_name = 'Sally';

In this example, the `setFirstNameAttribute` function will be called with the value `Sally`. The mutator will then apply the `strtolower` function to the name and set its value in the internal `$attributes` array.

이 예제에서 `setFirstNameAttribute` 함수는 `Sally` 라는 값과 함께 호출 될것입니다. mutator 는 이름에 대해 `strtolower` 함수가 실행되도록 하여 그 결과를 내부 `$attributes` 배열에 지정할 것입니다. 

<a name="date-mutators"></a>
## Date Mutators
## 날짜 Mutators

By default, Eloquent will convert the `created_at` and `updated_at` columns to instances of [Carbon](https://github.com/briannesbitt/Carbon), which provides an assortment of helpful methods, and extends the native PHP `DateTime` class.

기본적으로 Eloquent는 `created_at` 컬럼과 `updated_at` 컬럼을 [Carbon](https://github.com/briannesbitt/Carbon) 클래스의 인스턴스로(네이티브 PHP의 `DateTime` 클래스의 확장형이며, 여러 메소드를 제공하는) 변환해줍니다.  

You may customize which fields are automatically mutated, and even completely disable this mutation, by overriding the `$dates` property of your model:

사용자 정의 모델의 `$dates` 메소드를 재정의 하여 필드를 자동으로 바뀌도록 활성화 또는 비활성화 할 수 있습니다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * The attributes that should be mutated to dates.
         *
         * @var array
         */
        protected $dates = ['created_at', 'updated_at', 'deleted_at'];
    }

When a column is considered a date, you may set its value to a UNIX timestamp, date string (`Y-m-d`), date-time string, and of course a `DateTime` / `Carbon` instance, and the date's value will automatically be correctly stored in your database:

컬럼이 날짜라고 추정되는 경우, 여러분은 이 값을 UNIX 타임스탬프 값, date(`Y-m-d`) 문자열 값, 날짜-시간에 대한 문자열 값, 그리고 `DateTime` / `Carbon` 클래스의 인스턴스 값으로 설정할 수 있고, 날짜는 자동으로 데이터베이스에 정확하게 저장될 것입니다. 

    $user = App\User::find(1);

    $user->deleted_at = Carbon::now();

    $user->save();

As noted above, when retrieving attributes that are listed in your `$dates` property, they will automatically be cast to [Carbon](https://github.com/briannesbitt/Carbon) instances, allowing you to use any of Carbon's methods on your attributes:

위에서 보다시피, `$dates` 속성에 나열된 값을 가져오려고 하는 경우, 이 값은 자동으로 [Carbon](https://github.com/briannesbitt/Carbon)인스턴스로 캐스팅 될것이기 때문에, 속성에 대해서 Carbon의 메소드를 아무거나 사용할 수 있습니다.

    $user = App\User::find(1);

    return $user->deleted_at->getTimestamp();

By default, timestamps are formatted as `'Y-m-d H:i:s'`. If you need to customize the timestamp format, set the `$dateFormat` property on your model. This property determines how date attributes are stored in the database, as well as their format when the model is serialized to an array or JSON:

기본적으로, 타임스탬프 형식은 `'Y-m-d H:i:s'` 입니다. 타임 스탬프 형식을 별도로 지정할 필요가 있다면, 모델에서 `$dateFormat` 속성을 설정하면 됩니다. 이 속성은 데이터베이스에서 날짜 속성이 어떻게 저장되어야 하는지, 그리고 모델이 배열 또는 JSON 형태로 serialize 될 때의 형식을 결정합니다. 

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Flight extends Model
    {
        /**
         * The storage format of the model's date columns.
         *
         * @var string
         */
        protected $dateFormat = 'U';
    }

<a name="attribute-casting"></a>
## Attribute Casting
## 속성(Attribute) 캐스팅

The `$casts` property on your model provides a convenient method of converting attributes to common data types. The `$casts` property should be an array where the key is the name of the attribute being cast, while the value is the type you wish to cast to the column to. The supported cast types are: `integer`, `real`, `float`, `double`, `string`, `boolean`, `object`, `array`, `collection`, `date` and `datetime`.

모델의 `$casts` 값은 속성에 대해서 공통의 데이타 타입으로 변환하는 편리함 메소드를 제공합니다. `$casts` 값은 캐스팅 하고자 하는 속성들의 이름들을 key로 가지고, 그 타입을 값으로 가지는 배열 형태여야 합니다. 지원하는 캐스트 타입 유형은 integer , real , float , double , string , boolean , object , array , collection , date , datetime 입니다.

For example, let's cast the `is_admin` attribute, which is stored in our database as an integer (`0` or `1`) to a boolean value:

예를 들어, 데이터베이스에 정수형으로 저장된 `is_admin` 속성을 boolean 값으로 캐스팅 해 봅시다: 

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * The attributes that should be casted to native types.
         *
         * @var array
         */
        protected $casts = [
            'is_admin' => 'boolean',
        ];
    }

Now the `is_admin` attribute will always be cast to a boolean when you access it, even if the underlying value is stored in the database as an integer:

이제 사용자가 액세스 할 때 데이터베이스에 정수형 값으로 저장되어있는 경우에도 `is_admin` 속성은 항상 boolean으로 캐스팅됩니다. 

    $user = App\User::find(1);

    if ($user->is_admin) {
        //
    }

#### Array Casting
#### 배열 캐스팅

The `array` cast type is particularly useful when working with columns that are stored as serialized JSON. For example, if your database has a `TEXT` field type that contains serialized JSON, adding the `array` cast to that attribute will automatically deserialize the attribute to a PHP array when you access it on your Eloquent model:

`array` 타입 캐스팅은 직렬화 된 JSON으로 컬럼에 저장하는 작업을 할 때 특히 유용합니다. 예를 들어, 데이터베이스에 직렬화 된 JSON을 포함하는 `텍스트 형식` 필드가있는 경우, `array` 캐스팅을 해당 속성에 추가하면 Eloquent 사용자 정의 모델에 접근할 때 자동으로 역 직렬화 된 PHP 배열 값이 해당 속성 값으로 들어갑니다:

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * The attributes that should be casted to native types.
         *
         * @var array
         */
        protected $casts = [
            'options' => 'array',
        ];
    }

Once the cast is defined, you may access the `options` attribute and it will automatically be deserialized from JSON into a PHP array. When you set the value of the `options` attribute, the given array will automatically be serialized back into JSON for storage:

캐스팅이 정의하고 나서, `options` 속성에 액세스하면 자동으로 JSON 이 PHP 배열로 deserialize 될 것입니다. `options` 속성에 값을 설정하면, 주어진 배열은 자동으로 JSON으로 serialize 될 것입니다.

    $user = App\User::find(1);

    $options = $user->options;

    $options['key'] = 'value';

    $user->options = $options;

    $user->save();
