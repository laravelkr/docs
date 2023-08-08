# Eloquent: Mutators & Casting

- [시작하기](#introduction)
- [Accessors & Mutators](#accessors-and-mutators)
    - [Accessor 정의하기](#defining-an-accessor)
    - [Mutator 정의하기](#defining-a-mutator)
- [속성(Attribute) 캐스팅](#attribute-casting)
    - [배열 & JSON 캐스팅](#array-and-json-casting)
    - [날짜 캐스팅](#date-casting)
    - [열거형 캐스팅](#enum-casting)
    - [암호화 캐스팅](#encrypted-casting)
    - [쿼리 시간 캐스팅](#query-time-casting)
- [커스텀 캐스트](#custom-casts)
    - [밸류 오브젝트 캐스팅](#value-object-casting)
    - [배열 / JSON Serialization](#array-json-serialization)
    - [인바운드 캐스팅](#inbound-casting)
    - [캐스트 파라미터](#cast-parameters)
    - [Castables](#castables)

<a name="introduction"></a>
## 시작하기

Accessors, mutators, 속성 캐스팅을 사용하면 모델 인스턴스에서 Eloquent 속성 값을 검색하거나 설정할 때 Eloquent 속성 값을 변환할 수 있습니다. 예를 들어, [라라벨 암호화기](/docs/{{version}}/encryption)를 사용하여 값이 데이터베이스에 저장되어 있는 동안 암호화한 다음 Eloquent 모델에서 액세스할 때 속성을 자동으로 복호화할 수 있습니다. 또는 Eloquent 모델을 통해 액세스할 때 데이터베이스에 저장된 JSON 문자열을 배열로 변환할 수 있습니다.

<a name="accessors-and-mutators"></a>
## Accessors & Mutators

<a name="defining-an-accessor"></a>
### Accessor 정의하기

accessor는 접근할 때 Eloquent 속성 값을 변환합니다. accessor를 정의하려면 모델의 속성에 접근하는 protected 메소드를 생성합니다. 이 메서드의 이름은 모델의 속성 / 데이터베이스 컬럼을 표현하는 "카멜 케이스" 형태의 이름이어야 합니다.

아래 예제에서는 `first_name` 속성에 대한 accessor를 정의합니다. accessor는 `first_name` 속성의 값을 검색하려고 시도할 때 Eloquent에 의해 자동으로 호출됩니다. 모든 속성 accessor / mutator 메소드는 `Illuminate\Database\Eloquent\Casts\Attribute` 타입을 반환해야합니다. 

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Casts\Attribute;
    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * Get the user's first name.
         *
         * @return \Illuminate\Database\Eloquent\Casts\Attribute
         */
        protected function firstName(): Attribute
        {
            return Attribute::make(
                get: fn ($value) => ucfirst($value),
            );
        }
    }

모든 accessor 메소드는 모델의 속성에 접근하고, 경우에 따라 변경하는 방법을 정의하는 `Attribute` 인스턴스를 반환합니다. 이 예제에서는 속성에 접근하는 방법만 정의 하였습니다. 이렇게 하기 위해서 `Attribute` 클래스의 생성자에 `get` 인자를 제공합니다.

위 예제에서 볼 수 있듯이, 컬럼의 원래 값이 accessor 로 전달되고, 값을 가공하여 반환할 수 있습니다. accessor의 값에 엑세스하려면, 모델 인스턴스의 `first_name` 속성에 엑세스하면 됩니다.

    use App\Models\User;

    $user = User::find(1);

    $firstName = $user->first_name;

> **Note**
> 계산된 값을 모델의 배열 / JSON 표현에 추가하려면 [이 값을 추가해야 합니다](/docs/{{version}}/eloquent-serialization#appending-values-to-json).

<a name="building-value-objects-from-multiple-attributes"></a>
#### 여러개의 속성을 Value Object

경우에 따라서 accessor 가 여러개의 모델 속성을 하나의 "value object"로 변환해야할 수도 있습니다. 이렇게 하기 위해서 `get` 클로저는 `$attributes`를 두번째 인자로 전달받을 수 있습니다. 이 인자는 모델의 모든 속성 배열을 포함하고 있으며 자동으로 클로저에게 전달됩니다.

```php
use App\Support\Address;
use Illuminate\Database\Eloquent\Casts\Attribute;

/**
 * Interact with the user's address.
 *
 * @return  \Illuminate\Database\Eloquent\Casts\Attribute
 */
protected function address(): Attribute
{
    return Attribute::make(
        get: fn ($value, $attributes) => new Address(
            $attributes['address_line_one'],
            $attributes['address_line_two'],
        ),
    );
}
```

<a name="accessor-caching"></a>
#### Accessor 캐싱

accessor 로 부터 value object 를 반환할 때, value object에 대한 모든 변경사항은 모델이 저장되기 전에 자동으로 모델에 다시 동기화됩니다. 이런 동작이 가능한 이유는 엘로퀀트가 accessor 가 반환한 인스턴스를 유지하고 있어서 accessor가 호출될 때마다 동일한 인스턴스를 반환할 수 있기 때문입니다.   

    use App\Models\User;

    $user = User::find(1);

    $user->address->lineOne = 'Updated Address Line 1 Value';
    $user->address->lineTwo = 'Updated Address Line 2 Value';

    $user->save();

그러나 특히 계산 집약적인 경우 문자열 및 부울과 같은 기본 값에 대해 캐싱을 활성화하려는 경우가 있습니다. 이를 수행하기 위해 접근자를 정의할 때 `shouldCache` 메서드를 호출할 수 있습니다.

```php
protected function hash(): Attribute
{
    return Attribute::make(
        get: fn ($value) => bcrypt(gzuncompress($value)),
    )->shouldCache();
}
```

이런 accessor 의 속성 오브젝트 캐싱 동작을 비활성화하려면, 속성을 정의할 때 `withoutObjectCaching` 메소드를 호출하면 됩니다.

```php
/**
 * Interact with the user's address.
 *
 * @return  \Illuminate\Database\Eloquent\Casts\Attribute
 */
protected function address(): Attribute
{
    return Attribute::make(
        get: fn ($value, $attributes) => new Address(
            $attributes['address_line_one'],
            $attributes['address_line_two'],
        ),
    )->withoutObjectCaching();
}
```

<a name="defining-a-mutator"></a>
### Mutator 정의하기

mutator 는 엘로퀀트 속성값이 설정될 때 값을 변환합니다. mutator를 정의하려면 속성을 정의할 때 `set` 인자를 정의하면 됩니다. `first_name` 속성에 대한 mutator 를 정의한다고 가정해보겠습니다. 이 mutator 모델의 `first_name` 의 값을 설정하려고 하면 자동으로 호출됩니다.  

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Casts\Attribute;
    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * Interact with the user's first name.
         *
         * @param  string  $value
         * @return \Illuminate\Database\Eloquent\Casts\Attribute
         */
        protected function firstName(): Attribute
        {
            return Attribute::make(
                get: fn ($value) => ucfirst($value),
                set: fn ($value) => strtolower($value),
            );
        }
    }

mutator 클로저는 속성에 설정하고자 하는 값을 인자로 전달받습니다. 이 값을 변경하고 반환하면 됩니다. mutator 를 사용하기 위해서는 엘로퀀트 모델에서 `first_name` 속성값을 설정하기만 하면 됩니다.

    use App\Models\User;

    $user = User::find(1);

    $user->first_name = 'Sally';

이 예제에서 `set` 콜백은 `Sally` 라는 값과 함께 호출 될것입니다. mutator 는 이름에 대해 `strtolower` 함수가 실행되도록 하여 그 결과 값을 모델의 내부 `$attributes` 배열에 지정합니다.

<a name="mutating-multiple-attributes"></a>
#### 여러개의 속성값에 Mutator 적용하기

경우에 따라서 하나의 mutator 로 여러개의 속성값을 설정하기를 원할수도 있습니다. 이렇게 하기 위해서는 `set` 클로저가 배열을 반환하면 됩니다. 배열안의 각각의 키는 모델의 속성 / 데이터베이스 컬럼과 일치해야합니다.   

```php
use App\Support\Address;
use Illuminate\Database\Eloquent\Casts\Attribute;

/**
 * Interact with the user's address.
 *
 * @return  \Illuminate\Database\Eloquent\Casts\Attribute
 */
protected function address(): Attribute
{
    return Attribute::make(
        get: fn ($value, $attributes) => new Address(
            $attributes['address_line_one'],
            $attributes['address_line_two'],
        ),
        set: fn (Address $value) => [
            'address_line_one' => $value->lineOne,
            'address_line_two' => $value->lineTwo,
        ],
    );
}
```
<a name="attribute-casting"></a>
## 속성(Attribute) 캐스팅

속성(Attribute) 캐스팅은 모델에 추가 메소드를 정의할 필요 없이 accessors 및 mutators와 유사한 기능을 제공합니다.  대신, 모델의 `$casts` 속성을 사용하면 속성을 일반적인 데이터 타입으로 편리하게 변환할 수 있습니다.

`$casts` 속성은 key가 캐스트되는 속성의 이름이고 값이 컬럼을 캐스팅하려는 데이터 타입이 배열이어야 합니다. 지원되는 캐스트의 데이터 타입은 다음과 같습니다.

- `array`
- `AsStringable::class`
- `boolean`
- `collection`
- `date`
- `datetime`
- `immutable_date`
- `immutable_datetime`
- <code>decimal:&lt;precision&gt;</code>
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

캐스트를 정의한 후에는, 기본 값이 데이터베이스에 정수로 저장되어 있더라도 액세스할 때 `is_admin` 속성이 항상 boolean 으로 캐스트됩니다.

    $user = App\Models\User::find(1);

    if ($user->is_admin) {
        //
    }

런타임에 임시 캐스트를 새로 추가해야 하는 경우, `mergeCasts` 메소드를 사용할 수 있습니다. 이러한 캐스트 정의는 모델에 이미 정의된 캐스트에 추가됩니다.

    $user->mergeCasts([
        'is_admin' => 'integer',
        'options' => 'object',
    ]);

> **Warning**
> `null`인 속성은 캐스트되지 않습니다. 또한 관계와 이름이 같은 캐스트(또는 속성)를 정의하면 안됩니다.

<a name="stringable-casting"></a>
#### 스트링 가능한(Stringable) 캐스팅

`Illuminate\Database\Eloquent\Casts\AsStringable` 캐스트 클래스를 사용하여 모델 속성을 [유창한 `Illuminate\Support\Stringable` 객체](/docs/{{version}}/helpers#fluent-strings-method-list)로 캐스팅할 수 있습니다.

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
### 배열 및 JSON 캐스팅

`array` 캐스트는 직렬화된 JSON으로 저장된 컬럼들로 작업할 때 특히 유용합니다. 예를 들어, 데이터베이스에 직렬화된 JSON이 포함된 `JSON` 또는 `TEXT` 필드 유형이 있는 경우 해당 속성에 `Array` 캐스트를 추가하면 Eloquent 모델에서 액세스할 때 PHP 배열에 속성을 자동으로 역직렬화합니다.

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

캐스트가 정의되면 `options` 속성에 액세스할 수 있으며 JSON에서 PHP 배열로 자동으로 역직렬화됩니다. `options` 속성 값을 설정하면 지정된 배열이 JSON으로 자동 직렬화되어 저장됩니다.

    use App\Models\User;

    $user = User::find(1);

    $options = $user->options;

    $options['key'] = 'value';

    $user->options = $options;

    $user->save();

더 간단한 구문으로 JSON 속성의 단일 필드를 업데이트하려면, `update` 메소드를 호출할 때 `->` 연산자를 사용할 수 있습니다.

    $user = User::find(1);

    $user->update(['options->key' => 'value']);

<a name="array-object-and-collection-casting"></a>
#### 배열 객체 및 컬렉션 캐스팅

표준 `array` 캐스트는 많은 애플리케이션에 충분하지만 몇 가지 단점이 있습니다. `array` 캐스트는 기본 타입을 반환하므로 배열의 오프셋을 직접 변경할 수 없습니다. 예를 들어 다음 코드는 PHP 오류를 트리거합니다.

    $user = User::find(1);

    $user->options['key'] = $value;

이를 해결하기 위해 라라벨은 JSON 속성을 [ArrayObject](https://www.php.net/manual/en/class.arrayobject.php) 클래스로 변환하는 `AsArrayObject` 캐스트를 제공합니다. 이 기능은 라라벨의 [커스텀 케스트](#custom-casts)를 사용하여 구현되며, 이를 통해 라라벨은 PHP 오류를 유발하지 않고 개별 오프셋을 수정할 수 있도록 변형된 객체를 영리하게 캐시하고 변환할 수 있습니다. `AsArrayObject` 캐스트를 사용하려면 속성에 할당하기만 하면 됩니다.

    use Illuminate\Database\Eloquent\Casts\AsArrayObject;

    /**
     * The attributes that should be cast.
     *
     * @var array
     */
    protected $casts = [
        'options' => AsArrayObject::class,
    ];

마찬가지로, 라라벨은 JSON 속성을 라라벨 [컬렉션](/docs/{{version}}/collections) 인스턴스로 변환하는 `AsCollection` 캐스트를 제공합니다.

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
### 날짜 캐스팅

기본적으로 Eloquent는 `created_at` 및 `updated_at` 컬럼을 [Carbon](https://github.com/briannesbitt/Carbon) 의 인스턴스로 캐스팅합니다. 이 인스턴스는 PHP `DateTime` 클래스를 확장하고 다양하고 유용한 메소드를 제공합니다. 모델의 `$casts` 속성의 배열 내에서 추가 날짜 캐스트를 정의하여 추가 날짜 속성을 캐스트할 수 있습니다. 일반적으로 날짜는 `datetime` 또는 `immutable_datetime` 캐스트 유형을 사용하여 캐스트해야 합니다.

`date` 또는 `datetime` 캐스트를 정의할 때 날짜 형식도 지정할 수 있습니다. 이 형식은 [모델이 배열 또는 JSON으로 직렬화](/docs/{{version}}/eloquent-serialization) 될 때 사용합니다.

    /**
     * The attributes that should be cast.
     *
     * @var array
     */
    protected $casts = [
        'created_at' => 'datetime:Y-m-d',
    ];

컬럼이 날짜로 캐스팅되면 해당 모델 속성 값을 UNIX 타임스탬프, 날짜 문자열(`Y-m-d`), 날짜-시간에 대한 문자열, 또는 `DateTime` / `Carbon` 인스턴스로 설정할 수 있습니다. 날짜 값이 올바르게 변환되어 데이터베이스에 저장됩니다.

모델에 `serializeDate` 메소드를 정의하여 모든 모델의 날짜에 대한 기본 직렬화 형식을 커스텀할 수 있습니다. 이 방법은 데이터베이스에 저장하기 위해 날짜 형식이 지정되는 방식에 영향을 주지 않습니다.

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

모델의 날짜를 실제로 데이터베이스에 저장할 때 사용할 형식을 지정하려면 모델에 `$dateFormat` 속성을 정의해야 합니다.

    /**
     * The storage format of the model's date columns.
     *
     * @var string
     */
    protected $dateFormat = 'U';

<a name="date-casting-and-timezones"></a>
#### 날짜 캐스팅, 직렬화 & 타임존

기본적으로 `date` 및 `datetime` 캐스트는 애플리케이션의 `timezone` 구성 옵션에 지정된 시간대에 관계없이 UTC ISO-8601 날짜 문자열(`1986-05-28T21:05:54.0000Z`) 로 날짜를 직렬화합니다. 애플리케이션의 `timezone` 구성 옵션을 기본 `UTC` 값에서 변경하지 않음으로써 애플리케이션의 날짜를 UTC 표준 시간대에 저장하는 것은 물론 직렬화 형식을 사용하는 것을 강력히 권장합니다. 애플리케이션 전체에서 UTC 시간대를 일관되게 사용하면 PHP 및 JavaScript로 작성된 다른 날짜 라이브러리와의 최대 상호 운용성을 제공할 수 있습니다. 

`date` 또는 `datetime` 캐스트에 사용자 지정 형식이 적용되는 경우(예: `datetime:Y-m-d H:i:s`), 날짜 직렬화 중에 Carbon 인스턴스의 내부 시간대가 사용됩니다. 일반적으로 애플리케이션의 `timezone` 구성 옵션에 지정된 시간대가 됩니다.

<a name="enum-casting"></a>
### 열거형 캐스팅

> **Warning**
> 열거형 캐스팅은 PHP 8.1 이상에서만 사용할 수 있습니다.

Eloquent를 사용하면 속성 값을 PHP [열거형](https://www.php.net/manual/en/language.enumerations.backed.php)으로 캐스팅할 수도 있습니다. 이를 수행하기 위해 모델의 `$casts` 속성 배열에 캐스팅하려는 속성과 열거형을 지정할 수 있습니다.

    use App\Enums\ServerStatus;

    /**
     * The attributes that should be cast.
     *
     * @var array
     */
    protected $casts = [
        'status' => ServerStatus::class,
    ];

모델에 대한 캐스트를 정의하고 나면 속성과 상호 작용할 때 지정된 속성이 열거형으로 자동으로 캐스트되거나 열거형에서 캐스트됩니다.

    if ($server->status == ServerStatus::Provisioned) {
        $server->status = ServerStatus::Ready;

        $server->save();
    }

<a name="casting-arrays-of-enums"></a>
#### Enum 의 값 배열 캐스팅

모델에서 하나의 컬럼에 Enum 의 값 배열을 저장하고자 하는 경우에는 라라벨에서 제공하는 `AsEnumArrayObject`, `AsEnumCollection` 캐스팅을 활용할 수 있습니다.

 use App\Enums\ServerStatus;
    use Illuminate\Database\Eloquent\Casts\AsEnumCollection;

    /**
     * The attributes that should be cast.
     *
     * @var array
     */
    protected $casts = [
        'statuses' => AsEnumCollection::class.':'.ServerStatus::class,
    ];

<a name="encrypted-casting"></a>
### Encrypted Casting

`encrypted` 캐스트는 라라벨에 내장 [encryption](/docs/{{version}}/encryption) 기능을 사용하여 모델의 속성 값을 암호화합니다. 또한 `encrypted:array`, `encrypted:collection`, `encrypted:object`, `AsEncryptedArrayObject` 및 `AsEncryptedCollection` 캐스트는 암호화되지 않은것 처럼 작동합니다. 그러나 예상대로 기본 값은 데이터베이스에 저장될 때 암호화됩니다.

암호화된 텍스트의 최종 길이는 예측할 수 없고 일반 텍스트보다 길기 때문에 연결된 데이터베이스의 컬럼이 `TEXT`타입 또는 그 이상인지 확인하십시오. 또한 값이 데이터베이스에서 암호화되기 때문에, 암호화된 속성 값을 쿼리하거나 검색할 수 없습니다.

<a name="key-rotation"></a>
#### 암호화 키 교체

익히 알다시피, 라라벨은 애플리케이션의 `app` 설정 파일의 `key` 설정값을 사용하여 문자열을 암호화합니다. ㅇ리반적으로 이 값은 `APP_KEY` 환경변수값입니다. 애플리케이션의 암호화 키를 교체해야되는 경우 새로운 키를 사용하여 암호화된 속성을 수동으로 다시 암호화해야합니다. 

<a name="query-time-casting"></a>
### 쿼리 시간 캐스팅

때로는 테이블에서 원시 값을 선택할 때와 같이 쿼리를 실행하는 동안 캐스트를 적용해야 할 수도 있습니다. 예를 들어 다음 쿼리를 고려하십시오.

    use App\Models\Post;
    use App\Models\User;

    $users = User::select([
        'users.*',
        'last_posted_at' => Post::selectRaw('MAX(created_at)')
                ->whereColumn('user_id', 'users.id')
    ])->get();

이 쿼리 결과의 `last_posted_at` 속성은 간단한 문자열입니다. 쿼리를 실행할 때 이 속성에 `datetime` 캐스트를 적용할 수 있다면 정말 좋을 것입니다. 고맙게도 `withCasts` 메소드를 사용하여 이 작업을 수행할 수 있습니다.

    $users = User::select([
        'users.*',
        'last_posted_at' => Post::selectRaw('MAX(created_at)')
                ->whereColumn('user_id', 'users.id')
    ])->withCasts([
        'last_posted_at' => 'datetime'
    ])->get();

<a name="custom-casts"></a>
## 커스텀 캐스트

라라벨은 다양한 캐스트 타입을 내장하고 있습니다. 하지만 캐스트 유형을 직접 정의 해야 할 때도 있습니다. 캐스트를 생성하려면 `make:cast` 아티즌 명령어를 실행하세요. 새 캐스트 클래스는 `app/Casts` 디렉터리에 생성됩니다.

```shell
php artisan make:cast Json
```

모든 커스텀 캐스트 클래스는 `CastAttributes` 인터페이스를 구현합니다. 이 인터페이스를 구현하는 클래스는 반드시 `get`과 `set` 메소드를 구현해야합니다. `get` 메소드는 데이터베이스의 원시 값을 캐스트 된 값으로 변환하는 역활을 합니다, 반면 `set`메소드는 데이터베이스에 저장할 수 있는 원시 값으로 변환해야 합니다, 예제로 내장된 `json` 캐스팅 타입을 커스텀 캐스트 타입으로 다시 구현하였습니다.

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
### 밸류 오브젝트 캐스팅

값을 기본 유형으로 캐스팅하는 것으로 제한되지 않습니다. 또한 오브젝트에 값을 캐스팅할 수도 있습니다. 오브젝트에 값을 캐스팅하는 커스텀 캐스트를 정의하는 것은 기본 유형으로 캐스팅하는 것과 매우 유사합니다. 그러나 `set` 메소드는 모델에 기본 값을 설정하는 데 사용할 키 key / value 의 배열을 반환해야 합니다.

예제로 하나의 `Address`값을 가지는 밸류 오브젝트를 여러 모델 값을 캐스트 하는 커스텀 캐스트 클래스 정의했습니다, `Address` 값에는 `lineOne`과 `lineTwo`라는 두 가지 퍼블릭 프로퍼티가 있다고 가정하겠습니다.

    <?php

    namespace App\Casts;

    use App\ValueObjects\Address as AddressValueObject;
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
         * @return \App\ValueObjects\Address
         */
        public function get($model, $key, $value, $attributes)
        {
            return new AddressValueObject(
                $attributes['address_line_one'],
                $attributes['address_line_two']
            );
        }

        /**
         * Prepare the given value for storage.
         *
         * @param  \Illuminate\Database\Eloquent\Model  $model
         * @param  string  $key
         * @param  \App\ValueObjects\Address  $value
         * @param  array  $attributes
         * @return array
         */
        public function set($model, $key, $value, $attributes)
        {
            if (! $value instanceof AddressValueObject) {
                throw new InvalidArgumentException('The given value is not an Address instance.');
            }

            return [
                'address_line_one' => $value->lineOne,
                'address_line_two' => $value->lineTwo,
            ];
        }
    }

벨류 오프젝트를 캐스팅할 때 밸류 오브젝트에 대한 변경 사항은 모델이 저장되기 전에 자동으로 모델에 자동 동기화됩니다.

    use App\Models\User;

    $user = User::find(1);

    $user->address->lineOne = 'Updated Address Value';

    $user->save();

> **Note**
> 밸류 오브젝트를 포함하는 Eloquent 모델을 JSON 또는 배열로 직렬화하려는 경우 밸류 오브젝트에 `Illuminate\Contracts\Support\Arrayable` 및 `JsonSerializable` 인터페이스를 구현해야 합니다.

<a name="array-json-serialization"></a>
### 배열 / JSON Serialization

Eloquent 모델이 `toArray` 및 `toJson` 메소드를 사용하여 배열 또는 JSON으로 변환되는 경우, 커스텀 캐스트 밸류 오브젝트는 일반적으로 `Illuminate\Contracts\Support\Arrayable` 및 `JsonSerializable` 인터페이스를 구현하는 한 직렬화가됩니다. 그러나 타사 라이브러리에서 제공하는 밸류 오브젝트를 사용하는 경우 이러한 인터페이스를 오브젝트에 추가하지 못할 수 있습니다.

따라서 커스텀 캐스트 클래스가 벨류 오브젝트를 직렬화하도록 지정할 수 있습니다. 이렇게 하려면 커스텀 캐스트 클래스가 `Illuminate\Contracts\Database\Eloquent\SerializesCastableAttributes` 인터페이스를 구현해야 합니다. 이 인터페이스는 클래스에 밸류 오브젝트의 직렬화된 형식을 반환해야 하는 `serialize` 메소드를 포함해야 한다고 명시하고 있습니다.

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
### 인바운드 캐스팅

가끔은 모델에 설정되는 값을 변환하기만 하고 모델에서 속성을 조회할 때는 아무 동작도 하지 않는 커스텀 캐스트 클래스를 작성해야할 때도 있습니다. 

인바운드 전용 커스텀 캐스트는 `set` 메소드만 정의하면 되는 `CastsInboundAttributes`인터페이스를 구현해야 합니다. `--inbound` 옵션을 주고`make:cast` 아티즌 명령을 실행하면 인바운드 전용 캐스트 클래스를 생성할 수 있습니다.

```shell
php artisan make:cast Hash --inbound
```

인바운드 전용 캐스트의 대표적인 예는 "해싱(hashing)" 캐스트입니다. 예를 들어, 주어진 알고리즘을 통해 인바운드 값을 해싱하는 캐스트를 정의할 수 있습니다.

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
### 캐스트 파라미터

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

애플리케이션의 밸류 오브젝트가 고유한 커스텀 캐스트 클래스를 정의하도록 허용할 수 있습니다. 커스텀 캐스트 클래스를 모델에 연결하는 대신 `Illuminate\Contracts\Database\Eloquent\Castable` 인터페이스를 구현하는 밸류 오브젝트 클래스를 대안으로 연결할 수 있습니다.

    use App\Models\Address;

    protected $casts = [
        'address' => Address::class,
    ];

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

`Castable` 클래스를 사용할 때 `$casts` 정의에 인수를 제공할 수 있습니다. 인수는 `castUsing` 메소드에 전달됩니다.

    use App\Models\Address;

    protected $casts = [
        'address' => Address::class.':argument',
    ];

<a name="anonymous-cast-classes"></a>
#### 캐스터블 및 익명 캐스트 클래스

"castables"을 PHP의 [익명 클래스](https://www.php.net/manual/en/language.oop5.anonymous.php) 와 결합함으로써 값 객체와 해당 캐스팅 논리를 단일 castable 객체로 정의할 수 있습니다. 이를 수행하려면 밸류 오브젝트의 `castUsing` 메소드에서 익명 클래스를 반환합니다. 익명 클래스는 `CastsAttributes` 인터페이스를 구현해야 합니다.

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
