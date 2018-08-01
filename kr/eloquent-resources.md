# Eloquent: API Resources
# Eloquent: API Resources

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Generating Resources](#generating-resources)
- [리소스 클래스 생성하기](#generating-resources)
- [Concept Overview](#concept-overview)
- [컨셉 살펴보기](#concept-overview)
- [Writing Resources](#writing-resources)
- [리소스 클래스 작성하기](#writing-resources)
    - [Data Wrapping](#data-wrapping)
    - [데이터 Wrapping(랩핑)](#data-wrapping)
    - [Pagination](#pagination)
    - [페이지네이션](#pagination)
    - [Conditional Attributes](#conditional-attributes)
    - [조건에 따른 속성값 표현](#conditional-attributes)
    - [Conditional Relationships](#conditional-relationships)
    - [조건에 따른 관계 표현](#conditional-relationships)
    - [Adding Meta Data](#adding-meta-data)
    - [메타 데이터 추가하기](#adding-meta-data)
- [Resource Responses](#resource-responses)
- [리소스 응답](#resource-responses)

<a name="introduction"></a>
## Introduction
## 시작하기

When building an API, you may need a transformation layer that sits between your Eloquent models and the JSON responses that are actually returned to your application's users. Laravel's resource classes allow you to expressively and easily transform your models and model collections into JSON.

API 를 작성할 때, 애플리케이션의 사용자에게 Eloquent 모델을 JSON response로 전달해주기 위한 변환 레이어(transformation layer)가 필요할 수 있습니다. 라라벨의 리소스 클래스는 손쉽게 이 모델과 모델 컬렉션을 JSON으로 표현하도록 지원해줍니다.

<a name="generating-resources"></a>
## Generating Resources
## 리소스 클래스 생성하기

To generate a resource class, you may use the `make:resource` Artisan command. By default, resources will be placed in the `app/Http/Resources` directory of your application. Resources extend the `Illuminate\Http\Resources\Json\JsonResource` class:

리소스 클래스를 생성하기 위해서는 `make:resource` 아티즌 명령어를 사용하면 됩니다. 기본적으로 리소스 클래스는 애플리케이션의 `app/Http/Resources` 디렉토리에 생성됩니다. 모든 리소스 클래스는 `Illuminate\Http\Resources\Json\JsonResource` 클래스를 상속받습니다:

    php artisan make:resource User

#### Resource Collections
#### 리소스 컬렉션

In addition to generating resources that transform individual models, you may generate resources that are responsible for transforming collections of models. This allows your response to include links and other meta information that is relevant to an entire collection of a given resource.

개별적인 모델을 변환하는 것에 더해서, 모델의 컬렉션을 표현하기 위한 리소스 클래스를 생성할 수 있습니다. 이렇게 하면 지정된 리소스에 대한 응답-response에 주어진 모델 컬렉션과 연관된 링크 또는 기타 메타 정보를 포함시킬 수 있습니다.

To create a resource collection, you should use the `--collection` flag when creating the resource. Or, including the word `Collection` in the resource name will indicate to Laravel that it should create a collection resource. Collection resources extend the `Illuminate\Http\Resources\Json\ResourceCollection` class:

리소스 컬렉션 클래스를 생성하기 위해서는 리소스 클래스를 생성할 때 `--collection` 플래그를 지정하면 됩니다. 또는 리소스 클래스를 생성할 때, 이름에 `Collection` 라는 단어가 포함되어 있으면, 라라벨은 이를 자동으로 컬렉션을 위한 리소스 클래스로 생성합니다. 모든 컬렉션 리소스 클래스는 `Illuminate\Http\Resources\Json\ResourceCollection` 클래스를 상속받습니다:

    php artisan make:resource Users --collection

    php artisan make:resource UserCollection

<a name="concept-overview"></a>
## Concept Overview
## 컨셉 살펴보기

> {tip} This is a high-level overview of resources and resource collections. You are highly encouraged to read the other sections of this documentation to gain a deeper understanding of the customization and power offered to you by resources.

> {tip} 아래 내용은 리소스 클래스와 리소스 컬렉션 클래스에 대한 개괄적인 내용입니다. 리소스에 대한 커스터마이징과 기능에 대한 자세한 내용은 이 문서의 다른 영역을 참고하십시오.

Before diving into all of the options available to you when writing resources, let's first take a high-level look at how resources are used within Laravel. A resource class represents a single model that needs to be transformed into a JSON structure. For example, here is a simple `User` resource class:

리소스 클래스를 작성할 때 사용가능한 모든 옵션들을 살펴보기 전에, 먼저 라라벨에서 리소를 사용하는 방법을 개괄적으로 알아보겠습니다. 하나의 리소스 클래스는 JSON으로 변환하고자 하는 하나의 모델을 나타냅니다. 예를 들어 다음은 `User` 리소스 클래스 입니다:

    <?php

    namespace App\Http\Resources;

    use Illuminate\Http\Resources\Json\JsonResource;

    class User extends JsonResource
    {
        /**
         * Transform the resource into an array.
         *
         * @param  \Illuminate\Http\Request
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

Every resource class defines a `toArray` method which returns the array of attributes that should be converted to JSON when sending the response. Notice that we can access model properties directly from the `$this` variable. This is because a resource class will automatically proxy property and method access down to the underlying model for convenient access. Once the resource is defined, it may be returned from a route or controller:

모든 리소스 클래스는 응답-response를 내보낼 때 JSON으로 변환되어야 하는 속성 배열을 반환하는 `toArray` 메소드를 정의하고 있습니다. `$this` 변수를 통해서 모델에 직접 엑세스 할 수 있습니다. 이는 리소스 클래스가 자동으로 기본 모델에 대한 속성과 메소드에 엑세스 할 수 있게 프록시를 제공하기 때문입니다. 리소스 클래스를 정의하면, 이는 라우트 또는 컨트롤러에서 반환 할 수 있게됩니다:

    use App\User;
    use App\Http\Resources\User as UserResource;

    Route::get('/user', function () {
        return new UserResource(User::find(1));
    });

### Resource Collections
### 리소스 컬렉션

If you are returning a collection of resources or a paginated response, you may use the `collection` method when creating the resource instance in your route or controller:

리소스 컬렉션이나, 페이지네이션 처리된 응답을 반환하는 경우, 라우트나 컨트롤러에서 리소스 인스턴스를 생성하기 위해서 `collection` 메소드를 사용할 수 있습니다:

    use App\User;
    use App\Http\Resources\User as UserResource;

    Route::get('/user', function () {
        return UserResource::collection(User::all());
    });

Of course, this does not allow any addition of meta data that may need to be returned with the collection. If you would like to customize the resource collection response, you may create a dedicated resource to represent the collection:

이 경우에는 컬렉션과 함께 리턴하고자 하는 메타 데이터는 추가할 수 없습니다. 리소스 컬렉션 응답-response를 커스터마이징 하고자 하는 경우에는, 컬렉션을 나타내는 특정한 리소스 클래스를 생성할 수 있습니다:

    php artisan make:resource UserCollection

Once the resource collection class has been generated, you may easily define any meta data that should be included with the response:

리소스 컬렉션 클래스를 생성하면, 응답-response에 포함되어야할 메타 데이터를 손쉽게 정의할 수 있습니다:

    <?php

    namespace App\Http\Resources;

    use Illuminate\Http\Resources\Json\ResourceCollection;

    class UserCollection extends ResourceCollection
    {
        /**
         * Transform the resource collection into an array.
         *
         * @param  \Illuminate\Http\Request
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

After defining your resource collection, it may be returned from a route or controller:

리소스 컬렉션 클래스를 정의하고 나면, 라우트나 컨트롤러에서 이를 반환할 수 있습니다:

    use App\User;
    use App\Http\Resources\UserCollection;

    Route::get('/users', function () {
        return new UserCollection(User::all());
    });

<a name="writing-resources"></a>
## Writing Resources
## 리소스 클래스 작성하기

> {tip} If you have not read the [concept overview](#concept-overview), you are highly encouraged to do so before proceeding with this documentation.

> {tip} [컨셉 살펴보기](#concept-overview)를 읽지 않았다면, 아래 문서를 확인하기 전에 해당 부분을 먼저 읽어보시기 바랍니다.

In essence, resources are simple. They only need to transform a given model into an array. So, each resource contains a `toArray` method which translates your model's attributes into an API friendly array that can be returned to your users:

본질적으로, 리소스라는 것은 간단합니다. 리소스의 역할은 모델을 배열로 반환하는 하는 것입니다. 따라서 각 리소스 클래스에는 모델의 속성을 사용자에게 반환할 수 있도록 API 친화적인 배열로 변환하는 `toArray` 메소드를 포함하게 됩니다:

    <?php

    namespace App\Http\Resources;

    use Illuminate\Http\Resources\Json\JsonResource;

    class User extends JsonResource
    {
        /**
         * Transform the resource into an array.
         *
         * @param  \Illuminate\Http\Request
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

Once a resource has been defined, it may be returned directly from a route or controller:

리소스 클래스를 정의하고 나면, 라우트나 컨트롤러에서 바로 반환할 수 있습니다:

    use App\User;
    use App\Http\Resources\User as UserResource;

    Route::get('/user', function () {
        return new UserResource(User::find(1));
    });

#### Relationships
#### 관계-relationships

If you would like to include related resources in your response, you may add them to the array returned by your `toArray` method. In this example, we will use the `Post` resource's `collection` method to add the user's blog posts to the resource response:

응답-response에서 연관된 리소스(relationships)를 포함하고자 한다면, `toArray` 메소드에서 반환하는 배열에 이를 추가하면 됩니다. 다음 예제에서는 `Post` 리소스의 `collection` 메소드를 사용하여 사용자의 블로그 포스트를 응답-response에 추가합니다:

    /**
     * Transform the resource into an array.
     *
     * @param  \Illuminate\Http\Request
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

> {tip} If you would like to include relationships only when they have already been loaded, check out the documentation on [conditional relationships](#conditional-relationships).

> {tip} 이미 로딩된 경우에만, 관계-relationships을 포함하고자 한다면, [조건에 따른 관계 표현](#conditional-relationships) 문서를 확인하십시오.

#### Resource Collections
#### 리소스 컬렉션

While resources translate a single model into an array, resource collections translate a collection of models into an array. It is not absolutely necessary to define a resource collection class for each one of your model types since all resources provide a `collection` method to generate an "ad-hoc" resource collection on the fly:

리소스가 하나의 모델을 배열로 변환한다면, 리소스 컬렉션은 모델 컬렉션을 배열로 변환합니다. 모든 리소스 클래스는 "ad-hoc"을 생성하는 `collection` 메소드를 제공하고 있기 때문에, 모든 모델에 대해서 리소스 컬렉션 클래스를 정의할 필요는 없습니다:

    use App\User;
    use App\Http\Resources\User as UserResource;

    Route::get('/user', function () {
        return UserResource::collection(User::all());
    });

However, if you need to customize the meta data returned with the collection, it will be necessary to define a resource collection:

그렇지만, 컬렉션에서 메타데이터를 포함하는 것과 같이 커스터마이징이 필요하다면, 리소스 컬렉션 클래스를 정의해야 합니다:

    <?php

    namespace App\Http\Resources;

    use Illuminate\Http\Resources\Json\ResourceCollection;

    class UserCollection extends ResourceCollection
    {
        /**
         * Transform the resource collection into an array.
         *
         * @param  \Illuminate\Http\Request
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

Like singular resources, resource collections may be returned directly from routes or controllers:

단일 리소스와 같이, 리소스 컬렉션은 라우트나 컨트롤러에서 바로 반환할 수 있습니다:

    use App\User;
    use App\Http\Resources\UserCollection;

    Route::get('/users', function () {
        return new UserCollection(User::all());
    });

<a name="data-wrapping"></a>
### Data Wrapping
### 데이터 Wrapping(랩핑)

By default, your outer-most resource is wrapped in a `data` key when the resource response is converted to JSON. So, for example, a typical resource collection response looks like the following:

기본적으로, 리소스를 통한 응답-response는 JSON으로 변환될 때 "data"라는 키로 랩핑됩니다. 예들 들자면, 일반적인 리소스 응답-response는 다음과 같습니다:

    {
        "data": [
            {
                "id": 1,
                "name": "Eladio Schroeder Sr.",
                "email": "therese28@example.com",
            },
            {
                "id": 2,
                "name": "Liliana Mayert",
                "email": "evandervort@example.com",
            }
        ]
    }

If you would like to disable the wrapping of the outer-most resource, you may use the `withoutWrapping` method on the base resource class. Typically, you should call this method from your `AppServiceProvider` or another [service provider](/docs/{{version}}/providers) that is loaded on every request to your application:

위와 같은 데이터 랩핑을 원하지 않는다면, base 리소스 클래스에 `withoutWrapping` 메소드를 사용하면 됩니다. 일반적으로 이 메소드는 `AppServiceProvider` 또는 애플리케이션의 다른 서비스 프로바이더에서 호출해야 합니다:

    <?php

    namespace App\Providers;

    use Illuminate\Support\ServiceProvider;
    use Illuminate\Http\Resources\Json\Resource;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Perform post-registration booting of services.
         *
         * @return void
         */
        public function boot()
        {
            Resource::withoutWrapping();
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

> {note} The `withoutWrapping` method only affects the outer-most response and will not remove `data` keys that you manually add to your own resource collections.

> {note} `withoutWrapping` 메소드는 가장 바깥쪽의 데이터 구조에만 영향을 주며, `data` 키를 제거하지는 않습니다.

### Wrapping Nested Resources
### 중첩된 리소스 랩핑

You have total freedom to determine how your resource's relationships are wrapped. If you would like all resource collections to be wrapped in a `data` key, regardless of their nesting, you should define a resource collection class for each resource and return the collection within a `data` key.

리소스에서 관계-relationships이 어떻게 랩핑될지 자유롭게 결정할 수 있습니다. 중첩(nested)되었는지에 상관없이 모든 리소스 컬렉션을 `data` 키에 랩핑하고자 한다면, 각 리소스에 대한 리소스 컬렉션 클래스를 정의하고 `data` 키 안에서 컬렉션을 반환해야 합니다.

Of course, you may be wondering if this will cause your outer-most resource to be wrapped in two `data` keys. Don't worry, Laravel will never let your resources be accidentally double-wrapped, so you don't have to be concerned about the nesting level of the resource collection you are transforming:

이렇게 되었을 때, 외부 리소스가 두개의 `data` 키로 랩핑되어 있는지 의문을 가질 수 있습니다. 걱정하지 마십시오. 라라벨은 리소스를 두번 랩핑하지 않으므로 변환중인 리소스 컬렉션이 중첩되었는지에 대해서 걱정할 필요가 없습니다:

    <?php

    namespace App\Http\Resources;

    use Illuminate\Http\Resources\Json\ResourceCollection;

    class CommentsCollection extends ResourceCollection
    {
        /**
         * Transform the resource collection into an array.
         *
         * @param  \Illuminate\Http\Request
         * @return array
         */
        public function toArray($request)
        {
            return ['data' => $this->collection];
        }
    }

### Data Wrapping And Pagination
### 데이터 랩핑과 페이지네이션

When returning paginated collections in a resource response, Laravel will wrap your resource data in a `data` key even if the `withoutWrapping` method has been called. This is because paginated responses always contain `meta` and `links` keys with information about the paginator's state:

리소스 응답-response에서 페이징된 컬렉션을 반환할 때, 라라벨은 `withoutWrapping` 메소드가 호출 된 경우에도 리소스 데이터를 `data` 키로 랩핑합니다. 이는 페이징된 응답-response는 항상 페이지네이터의 상태를 나타내는 `meta`, `links` 키가 포함되기 때문입니다:

    {
        "data": [
            {
                "id": 1,
                "name": "Eladio Schroeder Sr.",
                "email": "therese28@example.com",
            },
            {
                "id": 2,
                "name": "Liliana Mayert",
                "email": "evandervort@example.com",
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

<a name="pagination"></a>
### Pagination
### 페이지네이션

You may always pass a paginator instance to the `collection` method of a resource or to a custom resource collection:

커스텀 리소스 컬렉션이나 리소스의 `collection` 메소드에 페이지네이터의 인스턴스를 전달할 수 있습니다:

    use App\User;
    use App\Http\Resources\UserCollection;

    Route::get('/users', function () {
        return new UserCollection(User::paginate());
    });

Paginated responses always contain `meta` and `links` keys with information about the paginator's state:

페이징된 응답-response는 항상 페이지네이터의 상태를 나타내는 `meta`, `links` 키가 포함됩니다:

    {
        "data": [
            {
                "id": 1,
                "name": "Eladio Schroeder Sr.",
                "email": "therese28@example.com",
            },
            {
                "id": 2,
                "name": "Liliana Mayert",
                "email": "evandervort@example.com",
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

<a name="conditional-attributes"></a>
### Conditional Attributes
### 조건에 따른 속성값 표현

Sometimes you may wish to only include an attribute in a resource response if a given condition is met. For example, you may wish to only include a value if the current user is an "administrator". Laravel provides a variety of helper methods to assist you in this situation. The `when` method may be used to conditionally add an attribute to a resource response:

때로는 주어진 조건이 충족 될 때, 리소스 응답에 지정된 속성을 포함시키고자 할 수도 있습니다. 예를 들어 현재 사용자가 "관리자-administrator"인 경우에만 값을 포함하고자 할 수 있습니다. 라라벨은 이러한 경우를 지원하기 위해서 다양한 헬퍼 메소드를 제공합니다. `when` 메소드는 리소스 응답-response에 조건에 따라 속성을 추가하는데 사용됩니다:

    /**
     * Transform the resource into an array.
     *
     * @param  \Illuminate\Http\Request
     * @return array
     */
    public function toArray($request)
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'email' => $this->email,
            'secret' => $this->when(Auth::user()->isAdmin(), 'secret-value'),
            'created_at' => $this->created_at,
            'updated_at' => $this->updated_at,
        ];
    }

In this example, the `secret` key will only be returned in the final resource response if the authenticated user's `isAdmin` method returns `true`. If the method returns `false`, the `secret` key will be removed from the resource response entirely before it is sent back to the client. The `when` method allows you to expressively define your resources without resorting to conditional statements when building the array.

이 예제에서 인증된 사용자의 `isAdmin` 메소드가 `true`를 반환 하면 최종적인 리소스 응답-response에 `scret` 키가 반환됩니다. 이 메소드가 `false`를 반환하면, `secret` 키는 리소스 응답-response이 클라이언트에게 보내기 전에 제거됩니다. `when` 메소드를 사용하면, 배열을 만들 때 조건문을 사용하지 않고 명시적으로 리소스를 정의할 수 있습니다.

The `when` method also accepts a Closure as its second argument, allowing you to calculate the resulting value only if the given condition is `true`:

`when` 메소드는 두번째 인자로 클로저를 받을 수 있는데, 주어진 조건이 `true` 인 경우에 결과 값을 계산합니다:

    'secret' => $this->when(Auth::user()->isAdmin(), function () {
        return 'secret-value';
    }),

#### Merging Conditional Attributes
#### 조건에 따라 속성값 포함시키기

Sometimes you may have several attributes that should only be included in the resource response based on the same condition. In this case, you may use the `mergeWhen` method to include the attributes in the response only when the given condition is `true`:

때로는 특정 조건에 기반헤서 리소스 응답-response에 포함되어야 할 속성을 구성할 수도 있습니다. 이 경우 `mergeWhen` 메소드를 사용하여 주어진 조건이 `true` 일 때만 응답-response에 속성값을 포함시킬 수 있습니다:

    /**
     * Transform the resource into an array.
     *
     * @param  \Illuminate\Http\Request
     * @return array
     */
    public function toArray($request)
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'email' => $this->email,
            $this->mergeWhen(Auth::user()->isAdmin(), [
                'first-secret' => 'value',
                'second-secret' => 'value',
            ]),
            'created_at' => $this->created_at,
            'updated_at' => $this->updated_at,
        ];
    }

Again, if the given condition is `false`, these attributes will be removed from the resource response entirely before it is sent to the client.

주어진 조건이 `false` 인 경우에는 리소스 응답-response이 클라이언트에게 보내기 전에 제거됩니다.

> {note} The `mergeWhen` method should not be used within arrays that mix string and numeric keys. Furthermore, it should not be used within arrays with numeric keys that are not ordered sequentially.

> {note} `mergeWhen` 메소드는 문자열과 숫자 키가 섞여 있는 배열 안에서 사용하면 안됩니다. 그리고 순서대로 정렬되지 않은 숫자 키가 있는 배열에서도 마찬가지로 사용하면 안됩니다.

<a name="conditional-relationships"></a>
### Conditional Relationships
### 조건에 따라 관계-relationship 표시하기

In addition to conditionally loading attributes, you may conditionally include relationships on your resource responses based on if the relationship has already been loaded on the model. This allows your controller to decide which relationships should be loaded on the model and your resource can easily include them only when they have actually been loaded.

조건에 따라서 속성값을 표시하는 것에 더해서, 모델에 관계-relationship 이 로딩되어 있는 경우에 조건에 따라서, 응답-response에 관계-relationship을 포함 시킬 수 있습니다. 컨트롤러는 이를 통해서 어떤 관계-relationship을 로딩해야 하는지 결정할 수 있으며 실제로 로딩된 경우에만 리소스에 쉽게 포함 시킬 수 있습니다.

Ultimately, this makes it easier to avoid "N+1" query problems within your resources. The `whenLoaded` method may be used to conditionally load a relationship. In order to avoid unnecessarily loading relationships, this method accepts the name of the relationship instead of the relationship itself:

궁극적으로 이는 리소스 안에서 "N+1" 쿼리 문제를 회피할 수 있도록 해줍니다. `whenLoaded` 메소드는 조건에 따라 관계-relationship 를 로딩하는데 사용할 수 있습니다. 필요하지 않은 관계-relationship를 로딩하는 것을 회피하기 위해서 이 메소드는 관계-relationship 그 자체 대신 관계-relationship 이름을 인지로 받습니다:

    /**
     * Transform the resource into an array.
     *
     * @param  \Illuminate\Http\Request
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

In this example, if the relationship has not been loaded, the `posts` key will be removed from the resource response entirely before it is sent to the client.

이 예제에서 관계-relationship가 로딩되지 않은 경우에 리소스 응답-response에서 `posts` 키는 제거됩니다.

#### Conditional Pivot Information
#### 조건에 따른 피벗 정보 포함하기

In addition to conditionally including relationship information in your resource responses, you may conditionally include data from the intermediate tables of many-to-many relationships using the `whenPivotLoaded` method. The `whenPivotLoaded` method accepts the name of the pivot table as its first argument. The second argument should be a Closure that defines the value to be returned if the pivot information is available on the model:

리소스 응답-response에서 조건에 따라 관계-relationship를 포함하는 것에 더해서, `whenPivotLoaded` 메소드를 사용해서 다대다 관계-relationship 에서 조건에 따라서 중간 테이블을 포함시킬 수 있습니다. `whenPivotLoaded` 메소드는 첫번째 인자로 피벗 테이블의 이름을 전달 받고, 두번째 인자로 모델에서 피벗 정보를 사용할 수 있는 경우 반환 할 값을 정의하는 클로저를 전달받습니다:

    /**
     * Transform the resource into an array.
     *
     * @param  \Illuminate\Http\Request
     * @return array
     */
    public function toArray($request)
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'expires_at' => $this->whenPivotLoaded('role_users', function () {
                return $this->pivot->expires_at;
            }),
        ];
    }

<a name="adding-meta-data"></a>
### Adding Meta Data
### 메타 데이터 추가하기

Some JSON API standards require the addition of meta data to your resource and resource collections responses. This often includes things like `links` to the resource or related resources, or meta data about the resource itself. If you need to return additional meta data about a resource, include it in your `toArray` method. For example, you might include `link` information when transforming a resource collection:

일부 JSON API 표준에서는 리소스 및 리소스 컬렉션 응답-response에 메타 데이터를 추가해야합니다. 여기에는 리소스 또는 연관된 리소스에 `links` 같은 데이터를 추가하거나, 리소스 그 자체에 대한 메타 데이터를 추가하는 것들이 포함됩니다. 만약 여러분이 리소스에 대한 추가적인 메타 데이터를 반환할 필요가 있다면, 간단하게 `toArray` 메소드를 포함시키면 됩니다. 예를 들어, 다음처럼 리소스 컬렉션이 변환될 때 `link` 정보를 포함시킬 수 있습니다:

    /**
     * Transform the resource into an array.
     *
     * @param  \Illuminate\Http\Request
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

When returning additional meta data from your resources, you never have to worry about accidentally overriding the `links` or `meta` keys that are automatically added by Laravel when returning paginated responses. Any additional `links` you define will be merged with the links provided by the paginator.

리소스에서 추가적인 메타 데이터를 반환 할 때는, 페이징 처리된 응답-reponse가 반환 하면서 실수로 라라벨에 의해서 자동으로 추가되는 `links` 또는 `mata` 키를 오버라이딩 하는 것을 걱정하지 않아도 됩니다. 여러분이 정의한 추가적인 `links`는 페이지네이터에 의해서 제공되는 링크와 자동으로 병합됩니다.

#### Top Level Meta Data
#### 최상위 레벨의 메타 데이터

Sometimes you may wish to only include certain meta data with a resource response if the resource is the outer-most resource being returned. Typically, this includes meta information about the response as a whole. To define this meta data, add a `with` method to your resource class. This method should return an array of meta data to be included with the resource response only when the resource is the outer-most resource being rendered:

반환되는 리소스가 가장 외부의 리소스인 경우에, 리소스 응답-response에 특정 메타 데이터만을 포함시키기를 원할 수 있습니다. 일반적으로 이는 전체적인 응답-response에 대한 메타데이터를 포함합니다. 이러한 메타 데이터를 정의하려면, 리소스 클래스에 `with` 메소드를 추가하면 됩니다. 이 메소드는 리소스가 렌더링 되는 가장 외부의 리소스인 경우에 리소스 응답에 포함되어야 하는 메타 데이터의 배열을 반환해야 합니다:

    <?php

    namespace App\Http\Resources;

    use Illuminate\Http\Resources\Json\ResourceCollection;

    class UserCollection extends ResourceCollection
    {
        /**
         * Transform the resource collection into an array.
         *
         * @param  \Illuminate\Http\Request
         * @return array
         */
        public function toArray($request)
        {
            return parent::toArray($request);
        }

        /**
         * Get additional data that should be returned with the resource array.
         *
         * @param \Illuminate\Http\Request  $request
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

#### Adding Meta Data When Constructing Resources
#### 리소스가 생성될 때 메타 데이터 추가하기

You may also add top-level data when constructing resource instances in your route or controller. The `additional` method, which is available on all resources, accepts an array of data that should be added to the resource response:

또한 라우트 또는 컨트롤러에서 리소스 인스턴스가 생성될 때, 최상위 레벨의 데이터를 추가할 수도 있습니다. 모든 리소스에서 사용될 수 있는 `additional` 메소드는 리소스 응답-response에서 추가되야 하는 데이터의 베열을 인자로 받습니다:

    return (new UserCollection(User::all()->load('roles')))
                    ->additional(['meta' => [
                        'key' => 'value',
                    ]]);

<a name="resource-responses"></a>
## Resource Responses
## 리소스 응답-Responses

As you have already read, resources may be returned directly from routes and controllers:

앞서 확인한 것 처럼, 리소스는 라우트나 컨트롤러에서 바로 반환할 수 있습니다:

    use App\User;
    use App\Http\Resources\User as UserResource;

    Route::get('/user', function () {
        return new UserResource(User::find(1));
    });

However, sometimes you may need to customize the outgoing HTTP response before it is sent to the client. There are two ways to accomplish this. First, you may chain the `response` method onto the resource. This method will return an `Illuminate\Http\Response` instance, allowing you full control of the response's headers:

그렇지만, 때때로 클라이언트에 HTTP 응답-response를 내보내기 전에 이를 커스터마이징 해야 할 때도 있습니다. 이 경우, 두가지 방법이 있는데, 먼저 리소스에 `response` 메소드를 체이닝 형태로 사용하면 됩니다. 이 메소드는 `Illuminate\Http\Response` 인스턴스를 반환하는데, 이를 사용하여 응답-response의 헤더를 조작할 수 있습니다:

    use App\User;
    use App\Http\Resources\User as UserResource;

    Route::get('/user', function () {
        return (new UserResource(User::find(1)))
                    ->response()
                    ->header('X-Value', 'True');
    });

Alternatively, you may define a `withResponse` method within the resource itself. This method will be called when the resource is returned as the outer-most resource in a response:

다른 방법으로는, 리소스 클래스에 `withResponse` 메소드를 정의하면 됩니다. 이 메소드는 리소스가 반환될 때, 호출됩니다:

    <?php

    namespace App\Http\Resources;

    use Illuminate\Http\Resources\Json\JsonResource;

    class User extends JsonResource
    {
        /**
         * Transform the resource into an array.
         *
         * @param  \Illuminate\Http\Request
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
         * @param  \Illuminate\Http\Request
         * @param  \Illuminate\Http\Response
         * @return void
         */
        public function withResponse($request, $response)
        {
            $response->header('X-Value', 'True');
        }
    }
