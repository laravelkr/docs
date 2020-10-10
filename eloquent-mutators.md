# Eloquent: Mutators

- [시작하기](#introduction)
- [Accessors & Mutators](#accessors-and-mutators)
    - [Accessor 정의하기](#defining-an-accessor)
    - [Mutator 정의하기](#defining-a-mutator)
- [날짜 Mutators](#date-mutators)
- [속성(Attribute) 캐스팅](#attribute-casting)
    - [커스텀 캐스트](#custom-casts)
    - [배열 & JSON 캐스팅](#array-and-json-casting)
    - [날짜 캐스팅](#date-casting)
    - [쿼리 시간 캐스팅](#query-time-casting)

<a name="introduction"></a>
## 시작하기

Accessors 와 mutators 는 Eloquent 모델에서 속성값을 찾을 때나 값을 설정할 때 포맷을 가능하게 합니다. 예를 들어 데이터베이스에서 값을 저장할 때, [라라벨 encrypter](/docs/{{version}}/encryption)을 사용하여 값을 암호화 하고, Eloquent 모델에서 값에 엑세스 할 때 자동으로 복호화 하기를 원할 수도 있습니다.

사용자가 지정한 accessors 와 mutators 에 더하여, Eloquent 는 또한 자동으로 날짜 필드를 [Carbon](https://github.com/briannesbitt/Carbon) 인스턴스로 캐스팅 하거나, [텍스트 필드를 JSON으로 캐스팅](#attribute-casting) 할 수 있습니다.

<a name="accessors-and-mutators"></a>
## Accessors & Mutators

<a name="defining-an-accessor"></a>
### Accessor 정의하기

accessor를 정의하기 위해서, `Foo` 모델에 접근하고자 하는 컬럼을 위한 `getFooAttribute` 메소드를 "studly" 케이스 형태의 이름으로 생성합니다. 다음 예제에서는 `first_name` 컬럽에 대한 accessor를 정의할 것입니다. accessor는 `first_name` 값이 찾아졌을 때 Eloquent 에 의해서 자동으로 호출될 것입니다.

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

위에서 볼 수 있듯이, 컬럼의 원래 값이 accessor 로 전달되고, 값을 가공하여 반환됩니다. accessor의 값에 액세스하려면, 모델 인스턴스의 `first_name` 속성에 액세스하면 됩니다.

    $user = App\Models\User::find(1);

    $firstName = $user->first_name;

이미 존재하는 속성값의 새롭게 변경하는데에도 accessor 를 사용할 수 있습니다.

    /**
     * Get the user's full name.
     *
     * @return string
     */
    public function getFullNameAttribute()
    {
        return "{$this->first_name} {$this->last_name}";
    }

> {tip} 이 계산 된 값을 모델의 배열 / JSON 출력에 추가하려면 [여기에 추가해야합니다](/docs/{{version}}/eloquent-serialization#appending-values-to-json).

<a name="defining-a-mutator"></a>
### Mutator 정의하기

mutator 를 정의하기 위해서, `Foo` 모델 클래스에 엑세스 하고자 하는 컬럼을 위한 `setFooAttribute` 메소드를 "카멜" 케이스 형태의 이름으로 정의합니다. 이번에도 `first_name` 속성에 대해서 mutator를 정의하겠습니다. 이 mutator는 모델에서 `first_name` 속성을 변경하려고 할 때, 자동으로 호출 될 것입니다.

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

mutator 는 속성에 설정하고자 하는 값을 전달 받아, 값을 변형하고, 변형된 값을 Eloquent 모델의 `$attributes` 속성에 지정할 것입니다. 다음 처럼 `first_name` 속성을 `Sally` 로 지정해 보겠습니다.

    $user = App\Models\User::find(1);

    $user->first_name = 'Sally';

이 예제에서 `setFirstNameAttribute` 함수는 `Sally` 라는 값과 함께 호출 될것입니다. mutator 는 이름에 대해 `strtolower` 함수가 실행되도록 하여 그 결과 값을 내부 `$attributes` 배열에 지정할 것입니다.

<a name="date-mutators"></a>
## 날짜 Mutators

기본적으로 Eloquent는 `created_at` 컬럼과 `updated_at` 컬럼을 가지고 있습니다. 이 컬럼은 유용한 메소드를 가지고 있으며,  PHP `DateTime` 클래스의 확장이기도한 [Carbon](https://github.com/briannesbitt/Carbon) 클래스의 인스턴스로 변환해줍니다. 모델의 `$dates` 속성을 설정하여 추가적인 날짜 속성을 추가할 수 있습니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * The attributes that should be mutated to dates.
         *
         * @var array
         */
        protected $dates = [
            'seen_at',
        ];
    }

> {tip} 모델의 `$timestamp` 속성을 `false` 로 지정하여 기본적으로 활성화 되는 `created_at` 와 `updated_at` 타임스탬프 값을 사용하지 않을 수(비활성화) 있습니다.

컬럼이 날짜라고 추정되는 경우, 이 값을 UNIX 타임스탬프, 날짜 문자열(`Y-m-d`), 날짜-시간에 대한 문자열, 또는 `DateTime` / `Carbon` 클래스의 인스턴스 값으로 설정할 수 있고, 날짜는 올바르게 변환되어 데이터베이스에 저장됩니다.

    $user = App\Models\User::find(1);

    $user->deleted_at = now();

    $user->save();

위에서 보다시피, `$dates` 속성에 나열된 값을 가져오려고 하는 경우, 이 값은 자동으로 [Carbon](https://github.com/briannesbitt/Carbon)인스턴스로 캐스팅 될것이기 때문에, 속성에 대해서 Carbon의 메소드를 아무거나 사용할 수 있습니다.

    $user = App\Models\User::find(1);

    return $user->deleted_at->getTimestamp();

#### Date Formats

기본적으로, 타임스탬프 형식은 `'Y-m-d H:i:s'` 입니다. 타임 스탬프 형식을 별도로 지정할 필요가 있다면, 모델에서 `$dateFormat` 속성을 설정하면 됩니다. 이 속성은 데이터베이스에서 날짜 속성이 어떻게 저장되어야 하는지 결정합니다.

    <?php

    namespace App\Models;

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
## 속성(Attribute) 캐스팅

모델의 `$casts` 값은 속성에 대해서 공통의 데이타 타입으로 변환하는 편리함 메소드를 제공합니다. `$casts` 값은 캐스팅 하고자 하는 속성들의 이름들을 key로 가지고, 그 타입을 값으로 가지는 배열 형태여야 합니다. 지원하는 캐스트 타입 유형은 `integer`, `real`, `float`, `double`, `decimal:<digits>`, `string`, `boolean`, `object`, `array`, `collection`, `date`, `datetime`, `timestamp`입니다. `decimal`로 변환 할 경우 `decimal:2`와 같이 자릿수를 정의해야합니다 

속성 캐스팅을 알아보기 위해, 데이터베이스에 정수형으로 저장된 `is_admin` 속성을 boolean 값으로 캐스팅 해 봅시다.

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

이제 사용자가 액세스 할 때 데이터베이스에 정수형 값으로 저장되어있는 경우에도 `is_admin` 속성은 항상 boolean으로 캐스팅됩니다.

    $user = App\Models\User::find(1);

    if ($user->is_admin) {
        //
    }

> {note} `null`인 속성은 캐스트되지 않습니다. 또한 관계와 이름이 같은 캐스트 (또는 속성)를 정의해서는 안됩니다.

<a name="custom-casts"></a>
### 커스텀 캐스트

라라벨은 다양한 캐스트 타입 내장하고 있습니다 하지만 캐스트 유형을 직접 정의 해야 할 때도 있습니다. `CastsAttributes` 인터페이스를 구현하는 클래스를 정의하여 사용 할 수 있습니다.

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

#### 밸류 오브젝트 캐스팅 

원시 타입의 캐스팅 이외에 오브젝트의 값도 캐스트 할 수 있습니다. 오브젝트의 값의 커스텀 캐스트 정의는 원시 타입의 커스텀 캐스팅과 매우 비슷합니다, 하지만 `set` 메소드는 모델에서 저장 가능한 원시 값을 설정할 때 사용되는 key / value 쌍의 배열을 리턴 해야 합니다.

예제로 하나의 `Address`값을 가지는 밸류 오브젝트를 여러 모델 값을 캐스트 하는 커스텀 캐스트 클래스 정의했습니다, `Address` 값에는 `lineOne`과 `lineTwo`라는 두 가지 퍼블릭 프로퍼티가 있다고 가정하겠습니다.
    
    <?php

    namespace App\Casts;

    use App\Models\Address;
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
         * @return \App\Address
         */
        public function get($model, $key, $value, $attributes)
        {
            return new Address(
                $attributes['address_line_one'],
                $attributes['address_line_two']
            );
        }

        /**
         * Prepare the given value for storage.
         *
         * @param  \Illuminate\Database\Eloquent\Model  $model
         * @param  string  $key
         * @param  \App\Address  $value
         * @param  array  $attributes
         * @return array
         */
        public function set($model, $key, $value, $attributes)
        {
            if (! $value instanceof Address) {
                throw new InvalidArgumentException('The given value is not an Address instance.');
            }

            return [
                'address_line_one' => $value->lineOne,
                'address_line_two' => $value->lineTwo,
            ];
        }
    }

밸류 오브젝트를 캐스팅 할 때 밸류 오브젝트의 변경 사항은 모델이 저장되기 전으로 자동으로 동기화됩니다.

    $user = App\Models\User::find(1);

    $user->address->lineOne = 'Updated Address Value';

    $user->save();

> {tip} 값 객체를 포함하는 Eloquent 모델을 JSON 또는 배열로 직렬화하려면 값 객체에 `Illuminate\Contracts\Support\Arrayable` 및 `JsonSerializable` 인터페이스를 구현해야합니다.

#### 인바운드 캐스팅

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

#### Cast Parameters
캐스트 파라메터

모델에 커스텀 캐스트를 추가할 때 캐스트 파라메터는 `:`문자를 사용하여 클래스 이름 쉼표로 구분된 여러 파라메터로 구분하여 지정할 수 있습니다. 파라메터는 캐스트 클래스의 생성자로 전달됩니다.
    
    /**
     * The attributes that should be cast.
     *
     * @var array
     */
    protected $casts = [
        'secret' => Hash::class.':sha256',
    ];

#### Castables

모델에 커스텀 캐스트를 부착하는 대신 `Illuminate\Contracts\Database\Eloquent\Castable` 인터페이스를 구현하는 클래스를 부착 할 수도 있습니다.

    protected $casts = [
        'address' => \App\Models\Address::class,
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
         * @return string
         */
        public static function castUsing()
        {
            return AddressCast::class;
        }
    }

`Castable` 클래스를 사용할 때 `$casts` 정의에서 인수를 제공 할 수 있습니다. 인수는 캐스터 클래스로 직접 전달됩니다.

    protected $casts = [
        'address' => \App\Models\Address::class.':argument',
    ];

<a name="array-and-json-casting"></a>
### 배열 && JSON 캐스팅

`array` 타입 캐스팅은 직렬화 된 JSON으로 컬럼에 저장하는 작업을 할 때 특히 유용합니다. 예를 들어, 데이터베이스에 직렬화 된 JSON을 포함하는 `JSON` 또는 `TEXT` 필드가있는 경우, `array` 캐스팅을 해당 속성에 추가하면 Eloquent 사용자 정의 모델에 접근할 때 자동으로 역 직렬화 된 PHP 배열 값이 해당 속성 값으로 들어갑니다.

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

캐스팅이 정의하고 나서, `options` 속성에 액세스하면 자동으로 JSON 이 PHP 배열로 deserialize 될 것입니다. `options` 속성에 값을 설정하면, 주어진 배열은 자동으로 JSON으로 serialize 될 것입니다.

    $user = App\Models\User::find(1);

    $options = $user->options;

    $options['key'] = 'value';

    $user->options = $options;

    $user->save();

<a name="date-casting"></a>
### 날짜 캐스팅

`date` 또는 `datetime` 캐스트 타입을 사용할 때 날짜 포맷을 지정할 수 있습니다. 이 포맷은 [모델이 배열이나, JSON으로 serialize](/docs/{{version}}/eloquent-serialization) 될 때 사용됩니다.

    /**
     * The attributes that should be cast.
     *
     * @var array
     */
    protected $casts = [
        'created_at' => 'datetime:Y-m-d',
    ];

<a name="query-time-casting"></a>
### 쿼리 시간 캐스팅

테이블에서 원시 값을 선택 할때 실행하는 동안 캐스트를 적용 할 수 있습니다. 예시로 다음의 쿼리 참고 하세요.

    use App\Models\Post;
    use App\Models\User;

    $users = User::select([
        'users.*',
        'last_posted_at' => Post::selectRaw('MAX(created_at)')
                ->whereColumn('user_id', 'users.id')
    ])->get();

쿼리 결과의 `last_posted_at` 속성은 원시 문자열입니다. `date` 캐스트를 이 속성에 적용하면 편리할 것입니다. 이를 위해 `withCasts` 메소드를 사용 할 수 있습니다.

    $users = User::select([
        'users.*',
        'last_posted_at' => Post::selectRaw('MAX(created_at)')
                ->whereColumn('user_id', 'users.id')
    ])->withCasts([
        'last_posted_at' => 'datetime'
    ])->get();
