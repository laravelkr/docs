# Eloquent: Serialization

- [시작하기](#introduction)
- [모델 & 컬렉션 Serializing](#serializing-models-and-collections)
    - [배열로 Serializing](#serializing-to-arrays)
    - [JSON 으로 Serializing](#serializing-to-json)
- [JSON 변환시 속성값 숨기기](#hiding-attributes-from-json)
- [JSON 변환시 특정 값 추가하기](#appending-values-to-json)
- [날짜 Serialization](#date-serialization)

<a name="introduction"></a>
## 시작하기

라라벨을 사용하여 API를 구성할 때, 여러분은 자주 특정 모델과, 연관된 모델들을 배열 또는 JSON 으로 변환해야 하는 경우가 있을겁니다. Eloquent에는 이러한 변환을 수행하는 편리한 메소드와 모델의 직렬화된(serialized) 표현에 포함되는 속성을 제어하는 메소드가 포함되어 있습니다.

> {tip} Eloquent 모델 및 컬렉션 JSON 직렬화를 처리하는 훨씬 더 강력한 방법은 [Eloquent API 리소스](/docs/{{version}}/eloquent-resources)에 대한 문서를 확인하세요.

<a name="serializing-models-and-collections"></a>
## 모델 & 컬렉션 Serializing

<a name="serializing-to-arrays"></a>
### 배열로 Serializaing

하나의 모델과 연관된 [관계 모델](/docs/{{version}}/eloquent-relationships)들을 배열로 변환 하고자 할 때에는, `toArray` 메소드를 사용해야만 합니다. 이 메소드는 재귀적이기 때문에, 모든 속성들과 모든 관계 모델들(관계 모델들 안에 포함된 관계 모델들까지)이 배열로 변환될 것입니다.

    use App\Models\User;

    $user = User::with('roles')->first();

    return $user->toArray();

`attributesToArray` 메소드는 모델의 속성을 배열로 변환하는 데 사용할 수 있지만 관계-relationships는 변환하지 않습니다.

    $user = User::first();

    return $user->attributesToArray();

컬렉션 인스턴스에서 `toArray` 메소드를 호출하여 모델의 전체 [컬렉션](/docs/{{version}}/eloquent-collections)을 배열로 변환할 수도 있습니다.

    $users = User::all();

    return $users->toArray();

<a name="serializing-to-json"></a>
### JSON으로 Serializaing

모델을 JSON으로 변환하기 위해서는, `toJson` 메소드를 사용해야 합니다. `toArray` 메소드와 같이 `toJson` 메소드는 재귀적이기 때문에, 모든 속성들과 관계-relations들은 JSON으로 변환됩니다. 또한 [PHP에서 제공되는](https://secure.php.net/manual/en/function.json-encode.php) JSON 인코딩 옵션을 지정할 수 있습니다.

    use App\Models\User;

    $user = User::find(1);

    return $user->toJson();

    return $user->toJson(JSON_PRETTY_PRINT);

또는, 모델 또는 컬렉션을 문자열로 캐스팅하면, 자동으로 `toJson` 메소드가 호출 될 것입니다.

    return (string) User::find(1);

모델과 컬렉션을 문자열로 캐스팅할 때에는, JSON으로 변환되므로, 애플리케이션의 라우터 또는 컨트롤러에서 Eloquent 객체를 바로 반환할 수도 있습니다. 라라벨은 라우터나 컨트롤러에서 반환될 때 Eloquent 모델과 컬렉션을 자동으로 JSON으로 직렬화(serialize)합니다.

    Route::get('users', function () {
        return User::all();
    });

<a name="relationships"></a>
#### 관계-Relationships

Eloquent 모델을 JSON으로 변환하면 로드 된 관계가 자동으로 JSON 객체의 속성에 포함됩니다. 또한 Eloquent 관계-relationship 메소드는 "camel case"를 메소드 이름을 사용하여 정의되지만 관계-relationship의 JSON 속성은 "snake case"가 됩니다.

<a name="hiding-attributes-from-json"></a>
## JSON 변환시 속성값 숨기기

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

> {tip} 관계를 숨기려면 관계의 메소드 이름을 Eloquent 모델의 `$hidden` 속성에 추가하십시오.

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
#### 일시적으로 속성들의 노출 정도를 수정하기

주어진 모델 인스턴스에서 몇가지 일반적인 숨겨진 속성을 보이도록 만들고자 한다면, `makeVisible` 메소드를 사용하면 됩니다. `makeVisible` 메소드는 모델 인스턴스를 반환합니다.

    return $user->makeVisible('attribute')->toArray();

마찬가지로, 일반적으로 표시되는 일부 속성을 숨기려면 `makeHidden` 메소드를 사용할 수 있습니다.

    return $user->makeHidden('attribute')->toArray();

<a name="appending-values-to-json"></a>
## JSON 변환시 특정 값 추가하기

때때로, 모델이 배열이나 JSON으로 변환 될 때, 데이터베이스의 컬럼에 일치하지 않는 속성들을 추가하기를 원할 수도 있습니다. 이렇게 하기 위해서는, 단순히 값에 대한 [accessor](/docs/{{version}}/eloquent-mutators)를 정의하면 됩니다.

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

accessor를 생성한 다음에, 모델의 `appends`값에 속성의 이름을 추가합니다. accessor는 PHP 메소드가 "camel case" 를 사용하여 정의되더라도 속성 이름은 일반적으로 "snake case"로 엑세스 됩니다.

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

속성이 `appends` 리스트에 추가되고나면 모델이 배열이나 JSON 형태로 변환될 때 자동으로 포함될 것입니다 .`appends` 배열 안에 있는 속성들은 또한 모델에 정의된 `visible`와 `hidden`값에 영향을 받을 것입니다.

<a name="appending-at-run-time"></a>
#### 실행 될때(run time에서) 추가하기

런타임에서, `append` 메소드를 사용하여 속성을 추가하도록 모델 인스턴스에 지시할 수 있습니다. 또한 `setAppends` 메소드를 사용하여 주어진 모델 인스턴스에 대해 추가된 속성의 전체 배열을 오버라이드 할 수 있습니다.

    return $user->append('is_admin')->toArray();

    return $user->setAppends(['is_admin'])->toArray();

<a name="date-serialization"></a>
## 날짜 Serialization

<a name="customizing-the-default-date-format"></a>
#### 기본 날짜 형식 사용자 정의

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
#### 날짜 포맷의 속성을 커스터마이징 하기

만약 각각의 Eloquent 시리얼라이즈 포맷을 커스터마이즈 하고싶다면, 날짜 형식은 모델의 [cast declarations](/docs/{{version}}/eloquent-mutators#attribute-casting) 에 자세하게 명시되어 있습니다.

    protected $casts = [
        'birthday' => 'date:Y-m-d',
        'joined_at' => 'datetime:Y-m-d H:00',
    ];
