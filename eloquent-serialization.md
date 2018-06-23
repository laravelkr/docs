# Eloquent: Serialization

- [소개](#introduction)
- [기본적인 사용법](#basic-usage)
- [JSON 변환시 속성값 숨기기](#hiding-attributes-from-json)
- [JSON 변환시 특정 값 추가하기](#appending-values-to-json)

<a name="introduction"></a>
## 소개

JSON API를 구성할 때, 여러분은 자주 특정 모델과, 연관된 모델들을 배열 또는 JSON 으로 변환해야될 필요가 있을 것입니다. Eloquent 는 serialization 에서 이러한 변환들과, 어떠한 속성들이 JSON에 포함되어야 하는지 제어할 수 있도록 편리한 메소드들을 가지고 있습니다.

<a name="basic-usage"></a>
## 기본적인 사용법

#### Converting A Model To An Array

하나의 모델과 연관된 [관계 모델](/docs/{{version}}/eloquent-relationships)들을 변환 하고자 할 때에는, `toArray` 메소드를 사용하면 됩니다. 이 메소드는 재귀적이기 때문에, 모든 속성들과 모든 관계 모델들(관계 모델들 안에 포함된 관계 모델들까지)이 배열로 변환될 것입니다.

    $user = App\User::with('roles')->first();

    return $user->toArray();

또한 [collections](/docs/{{version}}/eloquent-collections)을 배열로 변환할 수도 있습니다.

    $users = App\User::all();

    return $users->toArray();

#### 모델을 JSON으로 변환하기

모델을 JSON으로 변환하기 위해서는, `toJson` 메소드를 사용하면 됩니다. `toArray` 와 같이 `toJson` 메소드는 재귀적이므로, 모든 속성과 관계 모델들은 JSON으로 변환될 것입니다.

    $user = App\User::find(1);

    return $user->toJson();

이렇게 하는 대신에, 모델 또는 컬렉션을 문자열(string)으로 캐스팅하면, 자동으로 `toJson` 메소드가 호출 될것입니다.

    $user = App\User::find(1);

    return (string) $user;

모델과 컬렉션을 문자열로 캐스팅할 때에는, JSON으로 변환되므로, 어플리케이션의 라우트 또는 컨트롤러에서 Eloquent 객체를 바로 반환할 수도 있습니다.

    Route::get('users', function () {
        return App\User::all();
    });

<a name="hiding-attributes-from-json"></a>
## JSON 변환시 속성값 숨기기

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

> **주의:** 관계를 숨기고자 할 때에는, 동적인 프로퍼티 이름이 아니라, 관계에 대한 **메소드** 이름을 사용하십시오.

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

<a name="appending-values-to-json"></a>
## JSON 변환시 특정 값 추가하기

때때로, 사용자는 데이터베이스에서 해당 컬럼이 없는 배열 속성을 추가가 필요할 수도 있습니다. 이렇게 하기 위해서는, 단순히 값에 대한 accessor를 정의하면 됩니다:

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

속성이 `appends` 리스트에 추가되고나면 모델이 배열이나 JSON 형태로 변환될 때 자동으로 포함될 것입니다 .`appends` 배열 안에 있는 속성들은 또한 모델에 정의된 `visible`와 `hidden`값에 영향을 받을 것입니다.
