# Eloquent: API Resources

- [시작하기](#introduction)
- [리소스 클래스 생성하기](#generating-resources)
- [컨셉 살펴보기](#concept-overview)
    - [리소스 컬렉션](#resource-collections)
- [리소스 클래스 작성하기](#writing-resources)
    - [데이터 Wrapping(랩핑)](#data-wrapping)
    - [페이지네이션](#pagination)
    - [조건에 따른 속성값 표현](#conditional-attributes)
    - [조건에 따른 관계 표현](#conditional-relationships)
    - [메타 데이터 추가하기](#adding-meta-data)
- [리소스 응답](#resource-responses)

<a name="introduction"></a>
## 시작하기

API를 작성할 때, 애플리케이션의 사용자에게 Eloquent 모델을 JSON response로 전달해주기 위한 변환 레이어(transformation layer)가 필요할 수 있습니다. 예를 들어, 다른 사용자가 아닌 일부 사용자에 대한 특정 속성을 표시하거나 모델의 JSON 표현에 특정 관계를 항상 포함하려고 할 수 있습니다. Eloquent의 리소스 클래스는 손쉽게 이 모델과 모델 컬렉션을 JSON으로 표현하도록 지원해줍니다.

물론 `toJson` 메소드를 사용하여 Eloquent 모델이나 컬렉션을 항상 JSON으로 변환할 수 있습니다. 그러나 Eloquent 리소스는 모델과 그 관계의 JSON 직렬화에 대해 보다 세분화되고 강력한 제어를 제공합니다.

<a name="generating-resources"></a>
## 리소스 클래스 생성하기

리소스 클래스를 생성하기 위해서는 `make:resource` 아티즌 명령어를 사용하면 됩니다. 기본적으로 리소스 클래스는 애플리케이션의 `app/Http/Resources` 디렉토리에 생성됩니다. 모든 리소스 클래스는 `Illuminate\Http\Resources\Json\JsonResource` 클래스를 상속받습니다.

```shell
php artisan make:resource UserResource
```

<a name="generating-resource-collections"></a>
#### 리소스 컬렉션

개별적인 모델을 변환하는 것에 더해서, 모델의 컬렉션을 표현하기 위한 리소스 클래스를 생성할 수 있습니다. 이렇게 하면 지정된 JSON 리소스에 대한 응답-response에 주어진 모델 컬렉션과 연관된 링크 또는 기타 메타 정보를 포함시킬 수 있습니다.

리소스 컬렉션 클래스를 생성하기 위해서는 리소스 클래스를 생성할 때 `--collection` 플래그를 지정하면 됩니다. 또는 리소스 클래스를 생성할 때, 이름에 `Collection` 라는 단어가 포함되어 있으면, 라라벨은 이를 자동으로 컬렉션을 위한 리소스 클래스로 생성합니다. 모든 컬렉션 리소스 클래스는 `Illuminate\Http\Resources\Json\ResourceCollection` 클래스를 상속받습니다.

```shell
php artisan make:resource User --collection

php artisan make:resource UserCollection
```

<a name="concept-overview"></a>
## 컨셉 살펴보기

> **Note**
> 아래 내용은 리소스 클래스와 리소스 컬렉션 클래스에 대한 간단한 내용입니다. 리소스에 대한 커스터마이징과 기능에 대한 자세한 내용은 이 문서의 다른 영역을 참고하십시오.

리소스 클래스를 작성할 때 사용가능한 모든 옵션들을 살펴보기 전에, 먼저 라라벨에서 리소스를 사용하는 방법을 간략하게 알아보겠습니다. 하나의 리소스 클래스는 JSON으로 변환하고자 하는 하나의 모델을 나타냅니다. 예를 들어 다음은 `UserResource` 리소스 클래스 입니다.

    <?php

    namespace App\Http\Resources;

    use Illuminate\Http\Resources\Json\JsonResource;

    class UserResource extends JsonResource
    {
        /**
         * Transform the resource into an array.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return array
         */
        public function toArray($request)
        {
            return [
                'id' => $this->id,
                'name' => $this->name,
                'email' => $this->email,
                'created_at' => $this->created_at,
                'updated_at' => $this->updated_at,
            ];
        }
    }

모든 리소스 클래스는 리소스가 경로 또는 컨트롤러 메소드에서 응답-response으로 반환될 때 JSON으로 변환되어야 하는 속성의 배열을 반환하는 `toArray` 메소드를 정의합니다.

`$this` 변수를 통해서 모델에 직접 엑세스 할 수 있습니다. 이는 리소스 클래스가 자동으로 기본 모델에 대한 속성과 메소드에 엑세스 할 수 있게 프록시를 제공하기 때문입니다. 리소스 클래스를 정의하면, 이는 라우터 또는 컨트롤러에서 반환 할 수 있게 됩니다. 리소스는 생성자를 통해 기본 모델 인스턴스를 승합니다.

    use App\Http\Resources\UserResource;
    use App\Models\User;

    Route::get('/user/{id}', function ($id) {
        return new UserResource(User::findOrFail($id));
    });

<a name="resource-collections"></a>
### 리소스 컬렉션

리소스 컬렉션이나, 페이지네이션 처리된 응답-response을 반환하는 경우, 라우터나 컨트롤러에서 리소스 인스턴스를 생성하기 위해 리소스 클래스에서 제공하는 `collection` 메소드를 사용할 수 있습니다.

    use App\Http\Resources\UserResource;
    use App\Models\User;

    Route::get('/users', function () {
        return UserResource::collection(User::all());
    });

이 경우에는 컬렉션과 함께 리턴하고자 하는 커스텀 메타 데이터는 추가할 수 없습니다. 리소스 컬렉션 응답-response를 커스터마이징 하고자 하는 경우에는, 컬렉션을 나타내는 특정한 리소스 클래스를 생성할 수 있습니다.

```shell
php artisan make:resource UserCollection
```

리소스 컬렉션 클래스를 생성하면, 응답-response에 포함 되어야 할 메타 데이터를 손쉽게 정의할 수 있습니다.

    <?php

    namespace App\Http\Resources;

    use Illuminate\Http\Resources\Json\ResourceCollection;

    class UserCollection extends ResourceCollection
    {
        /**
         * Transform the resource collection into an array.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return array
         */
        public function toArray($request)
        {
            return [
                'data' => $this->collection,
                'links' => [
                    'self' => 'link-value',
                ],
            ];
        }
    }

리소스 컬렉션 클래스를 정의하고 나면, 라우터나 컨트롤러에서 이를 반환할 수 있습니다.

    use App\Http\Resources\UserCollection;
    use App\Models\User;

    Route::get('/users', function () {
        return new UserCollection(User::all());
    });

<a name="preserving-collection-keys"></a>
#### 컬렉션 키 유지하기

라우터에서 리소스 컬렉션을 리턴할 때, 라라벨은 컬렉션의 키를 번호순으로 재지정합니다. 그러나 컬렉션 키를 유지하고자 할 경우, `preserveKeys` 속성을 리소스 클래스에 추가해서 컬렉션의 키를 유지할 수 있습니다.

    <?php

    namespace App\Http\Resources;

    use Illuminate\Http\Resources\Json\JsonResource;

    class UserResource extends JsonResource
    {
        /**
         * Indicates if the resource's collection keys should be preserved.
         *
         * @var bool
         */
        public $preserveKeys = true;
    }

`preserveKeys` 속성이 `true` 로 지정되면, 컬렉션 키는 라우터 또는 컨트롤러에서 컬렉션이 반환될 때 유지됩니다.

    use App\Http\Resources\UserResource;
    use App\Models\User;

    Route::get('/users', function () {
        return UserResource::collection(User::all()->keyBy->id);
    });

<a name="customizing-the-underlying-resource-class"></a>
#### 리소스 클래스의 커스터마이징

일반적으로, 리소스 컬렉션의 `$this->collection` 속성은 자동으로 컬렉션에 해당하는 단일 리소스 클래스를 매핑한 결과물들로 채워집니다. 단일 리소스 클래스는 클래스 이름의 뒤에 `Collection` 부분이 없는 컬렉션의 클래스 이름으로 간주됩니다. 또한 개인 취향에 따라 단수 리소스 클래스에 `Resource` 접미사가 붙을 수도 있고 아닐 수도 있습니다.

예를 들어 `UserCollection` 는 주어진 사용자 인스턴스를 `UserResource` 리소스로 매핑하려고 시도 할 것입니다. 이 행동을 커스터마이징하려면 리소스 컬렉션의 `$collects` 속성을 오버라이드를 하면 됩니다.

    <?php

    namespace App\Http\Resources;

    use Illuminate\Http\Resources\Json\ResourceCollection;

    class UserCollection extends ResourceCollection
    {
        /**
         * The resource that this resource collects.
         *
         * @var string
         */
        public $collects = Member::class;
    }

<a name="writing-resources"></a>
## 리소스 클래스 작성하기

> **Note**
> [컨셉 살펴보기](#concept-overview)를 읽지 않았다면, 아래 문서를 확인하기 전에 해당 부분을 먼저 읽어보시기 바랍니다.

본질적으로, 리소스라는 것은 간단합니다. 리소스의 역할은 모델을 배열로 반환하는 하는 것입니다. 따라서 각 리소스에는 모델의 속성을 애플리케이션의 라우터 또는 컨트롤러에서 반환할 수 있고 API에 친화적 배열로 변환하는 `toArray` 메소드가 포함되어 있습니다.

    <?php

    namespace App\Http\Resources;

    use Illuminate\Http\Resources\Json\JsonResource;

    class UserResource extends JsonResource
    {
        /**
         * Transform the resource into an array.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return array
         */
        public function toArray($request)
        {
            return [
                'id' => $this->id,
                'name' => $this->name,
                'email' => $this->email,
                'created_at' => $this->created_at,
                'updated_at' => $this->updated_at,
            ];
        }
    }

리소스를 정의하고 나면, 라우터 또는 컨트롤러에서 바로 반환될 수 있습니다.

    use App\Http\Resources\UserResource;
    use App\Models\User;

    Route::get('/user/{id}', function ($id) {
        return new UserResource(User::findOrFail($id));
    });

<a name="relationships"></a>
#### 관계-relationships

응답-response에 관련된 리소스를 포함하고자 한다면, `toArray` 메소드에서 반환하는 배열에 이를 추가하면 됩니다. 다음 예제에서는 `PostResource` 리소스의 `collection` 메소드를 사용하여 사용자의 블로그 게시글을 응답-response에 추가합니다.

    use App\Http\Resources\PostResource;

    /**
     * Transform the resource into an array.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return array
     */
    public function toArray($request)
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'email' => $this->email,
            'posts' => PostResource::collection($this->posts),
            'created_at' => $this->created_at,
            'updated_at' => $this->updated_at,
        ];
    }

> **Note**
> 이미 로딩된 경우에만, 관계-relationships을 포함하고자 한다면, [조건에 따른 관계 표현](#conditional-relationships) 문서를 확인하십시오.

<a name="writing-resource-collections"></a>
#### 리소스 컬렉션

리소스가 하나의 모델을 배열로 변환한다면, 리소스 컬렉션은 모델 컬렉션을 배열로 변환합니다. 모든 리소스가 "ad-hoc" 리소스 컬렉션을 즉석에서 생성하는 `collection` 메소드를 제공하고 있기 때문에, 모든 모델에 대해 리소스 컬렉션 클래스를 정의할 필요는 없습니다.

    use App\Http\Resources\UserResource;
    use App\Models\User;

    Route::get('/users', function () {
        return UserResource::collection(User::all());
    });

그러나, 컬렉션과 함께 반환된 메타 데이터를 커스터마이징 해야 하는 경우 고유한 리소스 컬렉션을 정의해야 합니다.

    <?php

    namespace App\Http\Resources;

    use Illuminate\Http\Resources\Json\ResourceCollection;

    class UserCollection extends ResourceCollection
    {
        /**
         * Transform the resource collection into an array.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return array
         */
        public function toArray($request)
        {
            return [
                'data' => $this->collection,
                'links' => [
                    'self' => 'link-value',
                ],
            ];
        }
    }

단일 리소스와 같이, 리소스 컬렉션은 라우터나 컨트롤러에서 바로 반환할 수 있습니다.

    use App\Http\Resources\UserCollection;
    use App\Models\User;

    Route::get('/users', function () {
        return new UserCollection(User::all());
    });

<a name="data-wrapping"></a>
### 데이터 Wrapping(랩핑)

기본적으로, 리소스를 통한 응답-response는 JSON으로 변환될 때 `data`라는 키로 랩핑됩니다. 예들 들면, 일반적인 리소스 응답-response는 다음과 같습니다.

```json
{
    "data": [
        {
            "id": 1,
            "name": "Eladio Schroeder Sr.",
            "email": "therese28@example.com"
        },
        {
            "id": 2,
            "name": "Liliana Mayert",
            "email": "evandervort@example.com"
        }
    ]
}
```

`data`대신 맞춤 키를 사용하려면 리소스 클래스에 `$wrap`속성을 정의하면 됩니다.

    <?php

    namespace App\Http\Resources;

    use Illuminate\Http\Resources\Json\JsonResource;

    class UserResource extends JsonResource
    {
        /**
         * The "data" wrapper that should be applied.
         *
         * @var string
         */
        public static $wrap = 'user';
    }

위와 같은 데이터 랩핑을 원하지 않는다면, 기본 `Illuminate\Http\Resources\Json\JsonResource` 클래스에서 `withoutWrapping` 메소드를 호출해야 합니다. 일반적으로 이 메소드는 `AppServiceProvider` 또는 애플리케이션의 다른 [서비스 제공자](/docs/{{version}}/providers)에서 호출해야 합니다.

    <?php

    namespace App\Providers;

    use Illuminate\Http\Resources\Json\JsonResource;
    use Illuminate\Support\ServiceProvider;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Register any application services.
         *
         * @return void
         */
        public function register()
        {
            //
        }

        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            JsonResource::withoutWrapping();
        }
    }

> **Warning**
> `withoutWrapping` 메소드는 가장 바깥쪽의 데이터 구조에만 영향을 주며, `data` 키를 제거하지는 않습니다.

<a name="wrapping-nested-resources"></a>
#### 중첩된 리소스 랩핑

리소스에서 관계-relationships이 어떻게 랩핑될지 자유롭게 결정할 수 있습니다. 중첩(nested)이 되었는지에 상관없이 모든 리소스 컬렉션을 `data` 키에 랩핑하고자 한다면, 각 리소스에 대한 리소스 컬렉션 클래스를 정의하고 `data` 키 안에서 컬렉션을 반환해야 합니다.

이렇게 되었을 때, 외부 리소스가 두개의 `data` 키로 랩핑되어 있는지 의문을 가질 수 있습니다. 걱정하지 마십시오. 라라벨은 리소스를 두번 랩핑하지 않으므로 변환중인 리소스 컬렉션이 중첩되었는지에 대해서 걱정할 필요가 없습니다.

    <?php

    namespace App\Http\Resources;

    use Illuminate\Http\Resources\Json\ResourceCollection;

    class CommentsCollection extends ResourceCollection
    {
        /**
         * Transform the resource collection into an array.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return array
         */
        public function toArray($request)
        {
            return ['data' => $this->collection];
        }
    }

<a name="data-wrapping-and-pagination"></a>
#### 데이터 랩핑과 페이지네이션

리소스 응답-response을 통해 페이징된 컬렉션을 반환할 때, 라라벨은 `withoutWrapping` 메소드가 호출된 경우에도 리소스 데이터를 `data` 키로 랩핑합니다. 이는 페이징된 응답-response는 항상 페이지네이터의 상태를 나타내는 `meta`, `links` 키가 포함되기 때문입니다.

```json
{
    "data": [
        {
            "id": 1,
            "name": "Eladio Schroeder Sr.",
            "email": "therese28@example.com"
        },
        {
            "id": 2,
            "name": "Liliana Mayert",
            "email": "evandervort@example.com"
        }
    ],
    "links":{
        "first": "http://example.com/pagination?page=1",
        "last": "http://example.com/pagination?page=1",
        "prev": null,
        "next": null
    },
    "meta":{
        "current_page": 1,
        "from": 1,
        "last_page": 1,
        "path": "http://example.com/pagination",
        "per_page": 15,
        "to": 10,
        "total": 10
    }
}
```

<a name="pagination"></a>
### 페이지네이션

라라벨 paginate 인스턴스를 리소스의 `collection` 메소드나 커스텀 리소스 컬렉션에 전달할 수 있습니다.

    use App\Http\Resources\UserCollection;
    use App\Models\User;

    Route::get('/users', function () {
        return new UserCollection(User::paginate());
    });

페이징된 응답-response는 항상 페이지가 매겨진 상태를 나타내는 `meta`, `links` 키가 포함됩니다.

```json
{
    "data": [
        {
            "id": 1,
            "name": "Eladio Schroeder Sr.",
            "email": "therese28@example.com"
        },
        {
            "id": 2,
            "name": "Liliana Mayert",
            "email": "evandervort@example.com"
        }
    ],
    "links":{
        "first": "http://example.com/pagination?page=1",
        "last": "http://example.com/pagination?page=1",
        "prev": null,
        "next": null
    },
    "meta":{
        "current_page": 1,
        "from": 1,
        "last_page": 1,
        "path": "http://example.com/pagination",
        "per_page": 15,
        "to": 10,
        "total": 10
    }
}
```

<a name="conditional-attributes"></a>
### 조건에 따른 속성값 표현

때로는 주어진 조건이 충족 될 때, 리소스 응답에 지정된 속성을 포함시키고자 할 수도 있습니다. 예를 들어 현재 사용자가 "관리자"인 경우에만 값을 포함하고자 할 수 있습니다. 라라벨은 이러한 경우를 지원하기 위해서 다양한 헬퍼 메소드를 제공합니다. `when` 메소드는 리소스 응답-response에 조건에 따라 속성을 추가하는데 사용됩니다.

    /**
     * Transform the resource into an array.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return array
     */
    public function toArray($request)
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'email' => $this->email,
            'secret' => $this->when($request->user()->isAdmin(), 'secret-value'),
            'created_at' => $this->created_at,
            'updated_at' => $this->updated_at,
        ];
    }

이 예제에서 인증된 사용자의 `isAdmin` 메소드가 `true`를 반환 하면 최종적인 리소스 응답-response에 `secret` 키가 반환됩니다. 이 메소드가 `false`를 반환하면, `secret` 키는 리소스 응답-response이 클라이언트에게 보내기 전에 제거됩니다. `when` 메소드를 사용하면, 배열을 만들 때 조건문을 의존하지 않고 명시적으로 리소스를 정의할 수 있습니다.

`when` 메소드는 두번째 인자로 클로저를 받을 수 있는데, 주어진 조건이 `true` 인 경우에 결과 값을 계산합니다.

    'secret' => $this->when($request->user()->isAdmin(), function () {
        return 'secret-value';
    }),

`whenHas` 메소드는 기본 모델에 실제로 존재하는 경우에 속성을 포함시키는데 사용됩니다.

    'name' => $this->whenHas('name'),


추가적으로, `whenNotNull` 메서드는 속성이 null이 아닐 때 리소스 응답에 속성을 포함할 때 사용합니다.

    'name' => $this->whenNotNull($this->name),

<a name="merging-conditional-attributes"></a>
#### 조건에 따라 속성값 포함시키기

때로는 특정 조건에 기반헤서 리소스 응답-response에 포함되어야 할 속성을 구성할 수도 있습니다. 이 경우 `mergeWhen` 메소드를 사용하여 주어진 조건이 `true` 일 때만 응답-response에 속성값을 포함시킬 수 있습니다.

    /**
     * Transform the resource into an array.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return array
     */
    public function toArray($request)
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'email' => $this->email,
            $this->mergeWhen($request->user()->isAdmin(), [
                'first-secret' => 'value',
                'second-secret' => 'value',
            ]),
            'created_at' => $this->created_at,
            'updated_at' => $this->updated_at,
        ];
    }

다시 말하지만, 주어진 조건이 `false` 인 경우에는 리소스 응답-response이 클라이언트에게 보내기 전에 제거됩니다.

> **Warning**
> `mergeWhen` 메소드는 문자열과 숫자 키가 섞여 있는 배열 안에서 사용하면 안됩니다. 그리고 순서대로 정렬되지 않은 숫자 키가 있는 배열에서도 마찬가지로 사용하면 안됩니다.

<a name="conditional-relationships"></a>
### 조건에 따라 관계-relationship 표시하기

조건에 따라서 속성값을 표시하는 것에 더해서, 모델에 관계-relationship 이 로딩되어 있는 경우에 조건에 따라서, 응답-response에 관계-relationship을 포함 시킬 수 있습니다. 컨트롤러는 이를 통해서 어떤 관계-relationship을 로딩해야 하는지 결정할 수 있으며 실제로 로딩된 경우에만 리소스에 쉽게 포함 시킬 수 있습니다. 궁극적으로 이는 리소스 안에서 "N+1" 쿼리 문제를 회피할 수 있도록 해줍니다.

`whenLoaded` 메소드는 조건에 따라 관계-relationship 를 로딩하는데 사용할 수 있습니다. 필요하지 않은 관계-relationship를 로딩하는 것을 회피하기 위해서 이 메소드는 관계-relationship 자체 대신 관계-relationship 이름을 인지로 받습니다.

    use App\Http\Resources\PostResource;
    /**
     * Transform the resource into an array.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return array
     */
    public function toArray($request)
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'email' => $this->email,
            'posts' => PostResource::collection($this->whenLoaded('posts')),
            'created_at' => $this->created_at,
            'updated_at' => $this->updated_at,
        ];
    }

이 예제에서 관계-relationship가 로딩되지 않은 경우에 `posts` 키가 클라이언트에 전송되기 전에 리소스 응답-response에서 제거됩니다.

<a name="conditional-relationship-counts"></a>
#### 조건적 관계 수

조건부로 관계를 포함시키는 것에 더해 관계 수가 모델에 로드되었는지 여부에 따라 조건부로 관계 "수"를 리소스 응답에 포함시킬 수 있습니다.

    new UserResource($user->loadCount('posts'));

`whenCounted` 메서드는 리소스 응답에 관계 수를 조건부로 포함시킬 때 사용됩니다. 이 메서드는 관계 수가 존재하지 않을 때 불필요한 속성을 추가하는 걸 막아줍니다.

    /**
     * Transform the resource into an array.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return array
     */
    public function toArray($request)
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'email' => $this->email,
            'posts_count' => $this->whenCounted('posts'),
            'created_at' => $this->created_at,
            'updated_at' => $this->updated_at,
        ];
    }

이 예제에서 `posts` 관계 수가 로드되지 않았다면, `posts_count` 키는 클라이언트에게 보내지기 전에 리소스 응답에서 제거됩니다.

<a name="conditional-pivot-information"></a>
#### 조건에 따른 피벗 정보 포함하기

리소스 응답-response에서 조건에 따라 관계-relationship를 포함하는 것에 더해서, `whenPivotLoaded` 메소드를 사용해서 다대다 관계-relationship 에서 조건에 따라서 중간 테이블을 포함시킬 수 있습니다. `whenPivotLoaded` 메소드는 첫번째 인자로 피벗 테이블의 이름을 전달 받고, 두번째 인자로 모델에서 피벗 정보를 사용할 수 있는 경우 반환하는 클로저를 전달받습니다.

    /**
     * Transform the resource into an array.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return array
     */
    public function toArray($request)
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'expires_at' => $this->whenPivotLoaded('role_user', function () {
                return $this->pivot->expires_at;
            }),
        ];
    }

관계가 [사용자 지정 중간 테이블 모델](/docs/{{version}}/eloquent-relationships#defining-custom-intermediate-table-models)을 사용하는 경우, 중간 테이블 모델의 인스턴스를 `whenPivotLoaded` 메소드에 첫 번째 인수로 전달할 수 있습니다.

    'expires_at' => $this->whenPivotLoaded(new Membership, function () {
        return $this->pivot->expires_at;
    }),

중간 테이블이 `pivot` 이외의 접근자를 사용한다면 `whenPivotLoadedAs` 메소드를 사용할 수 있습니다.

    /**
     * Transform the resource into an array.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return array
     */
    public function toArray($request)
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'expires_at' => $this->whenPivotLoadedAs('subscription', 'role_user', function () {
                return $this->subscription->expires_at;
            }),
        ];
    }

<a name="adding-meta-data"></a>
### 메타 데이터 추가하기

일부 JSON API 표준에서는 리소스 및 리소스 컬렉션 응답-response에 메타 데이터를 추가해야합니다. 여기에는 리소스 또는 연관된 리소스에 `links` 같은 데이터를 추가하거나, 리소스 그 자체에 대한 메타 데이터를 추가하는 것들이 포함됩니다. 만약 여러분이 리소스에 대한 추가적인 메타 데이터를 반환할 필요가 있다면, 간단하게 `toArray` 메소드를 포함시키면 됩니다. 예를 들어, 다음처럼 리소스 컬렉션이 변환될 때 `link` 정보를 포함시킬 수 있습니다.

    /**
     * Transform the resource into an array.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return array
     */
    public function toArray($request)
    {
        return [
            'data' => $this->collection,
            'links' => [
                'self' => 'link-value',
            ],
        ];
    }

리소스에서 추가적인 메타 데이터를 반환 할 때는, 페이징 처리된 응답-reponse가 반환 하면서 실수로 라라벨에 의해서 자동으로 추가되는 `links` 또는 `mata` 키를 오버라이딩 하는 것을 걱정하지 않아도 됩니다. 여러분이 정의한 추가적인 `links`는 페이지네이터에 의해서 제공되는 링크와 자동으로 병합됩니다.

<a name="top-level-meta-data"></a>
#### 최상위 레벨의 메타 데이터

반환되는 리소스가 가장 외부의 리소스인 경우에, 리소스 응답-response에 특정 메타 데이터만을 포함시키기를 원할 수 있습니다. 일반적으로 이는 전체적인 응답-response에 대한 메타데이터를 포함합니다. 이러한 메타 데이터를 정의하려면, 리소스 클래스에 `with` 메소드를 추가하면 됩니다. 이 메소드는 리소스가 변형 되는 가장 외부의 리소스인 경우에 리소스 응답-response에 포함되어야 하는 메타 데이터의 배열을 반환해야 합니다.

    <?php

    namespace App\Http\Resources;

    use Illuminate\Http\Resources\Json\ResourceCollection;

    class UserCollection extends ResourceCollection
    {
        /**
         * Transform the resource collection into an array.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return array
         */
        public function toArray($request)
        {
            return parent::toArray($request);
        }

        /**
         * Get additional data that should be returned with the resource array.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return array
         */
        public function with($request)
        {
            return [
                'meta' => [
                    'key' => 'value',
                ],
            ];
        }
    }

<a name="adding-meta-data-when-constructing-resources"></a>
#### 리소스가 생성될 때 메타 데이터 추가하기

또한 라우터 또는 컨트롤러에서 리소스 인스턴스가 생성될 때, 최상위 레벨의 데이터를 추가할 수도 있습니다. 모든 리소스에서 사용될 수 있는 `additional` 메소드는 리소스 응답-response에서 추가되야 하는 데이터의 베열을 인자로 받습니다.

    return (new UserCollection(User::all()->load('roles')))
                    ->additional(['meta' => [
                        'key' => 'value',
                    ]]);

<a name="resource-responses"></a>
## 리소스 응답-Responses

앞서 확인한 것 처럼, 리소스는 라우터나 컨트롤러에서 바로 반환할 수 있습니다.

    use App\Http\Resources\UserResource;
    use App\Models\User;

    Route::get('/user/{id}', function ($id) {
        return new UserResource(User::findOrFail($id));
    });

그렇지만, 때때로 클라이언트에 HTTP 응답-response를 내보내기 전에 이를 커스터마이징 해야 할 때도 있습니다. 이 경우, 두가지 방법이 있는데, 먼저 리소스에 `response` 메소드를 체이닝 형태로 사용하면 됩니다. 이 메소드는 `Illuminate\Http\JsonResponse` 인스턴스를 반환하는데, 이를 사용하여 응답-response의 헤더를 조작할 수 있습니다.

    use App\Http\Resources\UserResource;
    use App\Models\User;

    Route::get('/user', function () {
        return (new UserResource(User::find(1)))
                    ->response()
                    ->header('X-Value', 'True');
    });

다른 방법으로는, 리소스 클래스에 `withResponse` 메소드를 정의하면 됩니다. 이 메소드는 리소스가 반환될 때, 호출됩니다.

    <?php

    namespace App\Http\Resources;

    use Illuminate\Http\Resources\Json\JsonResource;

    class UserResource extends JsonResource
    {
        /**
         * Transform the resource into an array.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return array
         */
        public function toArray($request)
        {
            return [
                'id' => $this->id,
            ];
        }

        /**
         * Customize the outgoing response for the resource.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  \Illuminate\Http\Response  $response
         * @return void
         */
        public function withResponse($request, $response)
        {
            $response->header('X-Value', 'True');
        }
    }
