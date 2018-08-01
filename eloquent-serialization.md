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

JSON API를 구성할 때, 여러분은 자주 특정 모델과, 연관된 모델들을 배열 또는 JSON 으로 변환해야될 필요가 있을 것입니다. Eloquent 는 serialization 에서 이러한 변환들과, 어떠한 속성들이 JSON에 포함되어야 하는지 제어할 수 있도록 편리한 메소드들을 가지고 있습니다.

<a name="serializing-models-and-collections"></a>
## 모델 & 컬렉션 Serializing

<a name="serializing-to-arrays"></a>
### 배열로 Serializaing

하나의 모델과 연관된 [관계 모델](/docs/{{version}}/eloquent-relationships)들을 배열로 변환 하고자 할 때에는, `toArray` 메소드를 사용해야만 합니다. 이 메소드는 재귀적이기 때문에, 모든 속성들과 모든 관계 모델들(관계 모델들 안에 포함된 관계 모델들까지)이 배열로 변환될 것입니다.

    $user = App\User::with('roles')->first();

    return $user->toArray();

또한 전체 모델 [collections](/docs/{{version}}/eloquent-collections)을 배열로 변환할 수도 있습니다.

    $users = App\User::all();

    return $users->toArray();

<a name="serializing-to-json"></a>
### JSON으로 Serializaing

모델을 JSON으로 변환하기 위해서는, `toJson` 메소드를 사용해야 합니다. `toArray` 메소드와 같이 `toJson` 메소드는 재귀적이기 때문에, 모든 속성들과 relations-관계들은 JSON으로 변환됩니다. 또한 [PHP에서 제공되는](http://php.net/manual/en/function.json-encode.php) JSON 인코딩 옵션을 지정할 수 있습니다:

    $user = App\User::find(1);

    return $user->toJson();

    return $user->toJson(JSON_PRETTY_PRINT);

이렇게 하는 대신에, 모델 또는 컬렉션을 문자열(string)으로 캐스팅하면, 자동으로 `toJson` 메소드가 호출 될것입니다.

    $user = App\User::find(1);

    return (string) $user;

모델과 컬렉션을 문자열로 캐스팅할 때에는, JSON으로 변환되므로, 애플리케이션의 라우트 또는 컨트롤러에서 Eloquent 객체를 바로 반환할 수도 있습니다.

    Route::get('users', function () {
        return App\User::all();
    });

<a name="hiding-attributes-from-json"></a>
## JSON 변환시 속성값 숨기기

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

> {note} 관계를 숨기고자 할 때에는, 관계에 대한 메소드 이름을 사용하십시오

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

#### 일시적으로 속성들의 노출 정도를 수정하기

주어진 모델 인스턴스에서 몇가지 일반적인 숨겨진 속성을 보이도록 만들고자 한다면, `makeVisible` 메소드를 사용하면 됩니다. `makeVisible` 메소드는 편리한 메소드 체이닝을 위해서 모델 인스턴스를 반환합니다:

    return $user->makeVisible('attribute')->toArray();

마찬가지로, 주어진 모델 인스턴스에서 몇가지 일반적으로 보여지던 속성을 보이지 않도록 만들고자 한다면, `makeHidden`메소드를 사용하면 됩니다.

    return $user->makeHidden('attribute')->toArray();

<a name="appending-values-to-json"></a>
## JSON 변환시 특정 값 추가하기

때때로, 모델이 배열이나 JSON으로 캐스팅 될 때, 데이터베이스의 컬럼에 일치하지 않는 속성들을 추가하기를 원할 수도 있습니다. 이렇게 하기 위해서는, 단순히 값에 대한 [accessor](/docs/{{version}}/eloquent-mutators)를 정의하면 됩니다:

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

accessor 를 생성한 다음에, 모델의 `appends`값에 속성의 이름을 추가합니다. accessor 가 "카멜 케이스"로 정의되어 있더라도, 속성 이름은 정상적으로 "스네이크 케이스"로 엑세스 됩니다:

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

#### 실행 될때(run time에서) 추가하기

`append` 메소드를 사용해서 하나의 모델 인스턴스에 속성을 추가할 수도 있습니다. 또는 `setAppends` 메소드를 사용하여 주어진 모델 인스턴스에 추가된 속성들의 전체 배열을 오버라이드 할 수 있습니다:

    return $user->append('is_admin')->toArray();

    return $user->setAppends(['is_admin'])->toArray();

<a name="date-serialization"></a>
## 날짜 Serialization

#### 날짜 포맷의 속성을 커스터마이징 하기

만약 각각의 Eloquent 시리얼라이즈 포맷을 커스터마이즈 하고싶다면, 날짜 형식은 [cast declaration](/docs/{{version}}/eloquent-mutators#attribute-casting) 에 자세하게 명시되어 있습니다:

    protected $casts = [
        'birthday' => 'date:Y-m-d',
        'joined_at' => 'datetime:Y-m-d H:00',
    ];

#### Carbon을 통한 전역 커스터마이징

라라벨은 Carbon의 JSON 시리얼라이즈 포맷을 편리하게 정의할 수 있도록 [Carbon](https://github.com/briannesbitt/Carbon) 날짜 라이브러리를 확장하고 있습니다. 애플리케이션에서 시리얼라이즈 되는 모든 Carbon 날짜를 커스터마이징 하려면 `Carbon::serializeUsing` 메소드를 사용하면 됩니다. `Carbon::serializeUsing` 메소드는 JSON 시리얼라이즈되는 날짜의 문자형식을 반환하는 클로저를 인자로 받습니다:

    <?php

    namespace App\Providers;

    use Illuminate\Support\Carbon;
    use Illuminate\Support\ServiceProvider;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Perform post-registration booting of services.
         *
         * @return void
         */
        public function boot()
        {
            Carbon::serializeUsing(function ($carbon) {
                return $carbon->format('U');
            });
        }

        /**
         * Register bindings in the container.
         *
         * @return void
         */
        public function register()
        {
            //
        }
    }
