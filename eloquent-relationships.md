# Eloquent: Relationships - 관계

- [시작하기](#introduction)
- [관계 정의하기](#defining-relationships)
    - [1:1(일대일) 관계](#one-to-one)
    - [1:*(일대다) 관계](#one-to-many)
    - [1:*(일대다) 역관계](#one-to-many)
    - [\*:*(다대다) 관계](#many-to-many)
    - [커스텀 중간 테이블 모델 정의하기](#defining-custom-intermediate-table-models)
    - [연결을 통한 단일 관계](#has-one-through)
    - [연결을 통한 다수를 가지는 관계](#has-many-through)
- [다형성 관계](#polymorphic-relationships)
    - [1:1(일대일) 관계](#one-to-one-polymorphic-relations)
    - [1:*(일대다) 관계](#one-to-many-polymorphic-relations)
    - [\*:*(다대다) 관계](#many-to-many-polymorphic-relations)
    - [사용자 정의 다형성 타입](#custom-polymorphic-types)
- [관계 쿼리 질의하기](#querying-relations)
    - [관계 메소드 Vs. 동적 속성](#relationship-methods-vs-dynamic-properties)
    - [존재하는 관계에 대한 쿼리 질의하기](#querying-relationship-existence)
    - [관계된 모델이 존재하지 않는 것을 확인하며 질의하기](#querying-relationship-absence)
    - [다형성 관계 쿼리](#querying-polymorphic-relationships)
    - [연관된 모델 수량 확인하기-카운트](#counting-related-models)
- [Eager 로딩](#eager-loading)
    - [Eager 로딩 조건 제한하기](#constraining-eager-loads)
    - [지연 Eager 로딩](#lazy-eager-loading)
- [연관된 모델 삽입하기](#inserting-and-updating-related-models)
    - [`save` 메소드](#the-save-method)
    - [`create` 메소드](#the-create-method)
    - [소속된 관계](#updating-belongs-to-relationships)
    - [다대다 관계](#updating-many-to-many-relationships)
- [부모의 타임스탬프 값 갱신하기](#touching-parent-timestamps)

<a name="introduction"></a>
## 시작하기

데이터베이스 테이블은 주로 서로 관련되어 있습니다. 예를 들어, 한 블로그 포스트가 많은 댓글을 가지고 있거나 어떤 명령이 그 명령을 내린 사용자와 관련되어 있을 수 있습니다. Eloquent는 이 relationship들과 관련한 작업을 하거나 관리하는 것을 쉽게 해주며 여러 타입의 relationship을 지원합니다.

- [1:1(일대일) 관계](#one-to-one)
- [1:*(일대다) 관계](#one-to-many)
- [\*:*(다대다) 관계](#many-to-many)
- [연결을 통한 단일 관계](#has-one-through)
- [연결을 통한 다수를 가지는 관계](#has-many-through)
- [1:1(일대일) (다형성)](#one-to-one-polymorphic-relations)
- [1:*(일대다) (다형성)](#one-to-many-polymorphic-relations)
- [\*:*(다대다) (다형성)](#many-to-many-polymorphic-relations)

<a name="defining-relationships"></a>
## 관계 정의하기

Eloquent relationship들은 Eloquent 모델 클래스에 메소드로 정의되어 있습니다. Eloquent 모델들과 같이 relationships-관계를 정의한 것은 강력한 [쿼리 빌더](/docs/{{version}}/queries)로써의 기능으로도 작동하기 때문에 relationships-관계를 메소드로 정의하는 것은 강력한 메소드 체이닝과 쿼리 능력을 제공하게됩니다. 예를 들어 다음의 `post` 관계에 대해서 추가적인 제약조건을 체이닝할 수도 있습니다.

    $user->posts()->where('active', 1)->get();

relationship을 사용하는 법에 더 깊이 알아보기 전에, 각 타입을 어떻게 정의햐는지 알아보도록 합시다.

<a name="one-to-one"></a>
### 1:1(일대일) 관계 정의하기

일대일 relationship은 아주 기본적인 관계입니다. 예를 들어, 하나의 `User` 모델이 하나의 `Phone`과 관계되어 있을 수 있습니다. 이 relationship을 정의하기 위해 `User` 모델에 `phone` 메소드를 구성합니다. `phone` 메소드는 `hasOne` 메소드를 호출하게 되고 그 결과를 반환할 것입니다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * Get the phone record associated with the user.
         */
        public function phone()
        {
            return $this->hasOne('App\Phone');
        }
    }

`hasOne` 메소드에 전달되는 첫번째 인자는 관련된 모델의 이름입니다. Relationship이 정의되었다면 Eloquent의 동적 속성을 이용하여 관련된 기록을 찾을 수 있습니다. 동적 속성은 모델에 정의된 속성에 접근하는 방식과 같은 방식으로 relationship 메소드에 접근하는 것을 허용합니다.

    $phone = User::find(1)->phone;

Eloquent는 모델 명에 근거하여 relationship의 외래 키(foreign key)를 결정합니다. 이 경우, `Phone` 모델은 `user_id` 외래 키를 가질 것이라고 자동으로 추정합니다. 이 추정사항을 재정의하고 싶다면 `hasOne` 메소드로 두번째 인자를 전달하면 됩니다.

    return $this->hasOne('App\Phone', 'foreign_key');

Eloquent는 또한 외래 키가 부모의 `id` 컬럼(또는 사용자가 정의한 `$primaryKey`)에 상응하는 값을 가지고 있다고 추정합니다. 즉, Eloquent는 `Phone`레코드의 `user_id` 컬럼에서 사용자 `id` 컬럼의 값을 찾을 것입니다. Relationship이 `id`가 아닌 다른 값을 사용하고자 한다면 커스텀 키를 지정하는 세번째 인자를 `hasOne` 메소드로 전달하면 됩니다.

    return $this->hasOne('App\Phone', 'foreign_key', 'local_key');

#### 관계의 역(반대) 정의하기

이제 `User`에서 `Phone` 모델에 접근할 수 있습니다. 반대로, `Phone` 모델에 relationship을 정의하여 phone을 소유하는 `User`에 접근할 수 있도록 해봅시다. `belongsTo` 메소드를 이용하면 `hasOne` relationship의 반대를 정의할 수 있습니다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Phone extends Model
    {
        /**
         * Get the user that owns the phone.
         */
        public function user()
        {
            return $this->belongsTo('App\User');
        }
    }

위 예에서 Eloquent는 `Phone` 모델의 `user_id`와 `User` 모델의 `id`를 비교해볼 것입니다. Eloquent는 relationship 메소드의 이름을 검사하고 메소드 이름에 `_id`를 붙여서 외래 키의 기본 이름으로 결정합니다. 하지만 `Phone` 모델의 외래 키가 `user_id`가 아니라면 커스텀 키 이름을 두번째 인자로 `belongsTo`에 전달하면 됩니다.

    /**
     * Get the user that owns the phone.
     */
    public function user()
    {
        return $this->belongsTo('App\User', 'foreign_key');
    }

부모 모델이 `id`를 프라이머리 키로 사용하지 않거나 자식 모델을 다른 컬럼에 조인시키고 싶다면 부모 테이블의 커스텀 키를 지정하는 세번째 인자를 `belongsTo` 메소드로 전달하면 됩니다.

    /**
     * Get the user that owns the phone.
     */
    public function user()
    {
        return $this->belongsTo('App\User', 'foreign_key', 'other_key');
    }

<a name="one-to-many"></a>
### 1:*(일대다) 관계 정의하기

일대다 relationship은 하나의 모델이 다른 모델의 어떤 부분이라도 소유할 경우의 relationship을 정의하는데 사용됩니다. 예를 들어, 한 블로그 게시물이 댓글을 무제한으로 가질 수 있습니다. 다른 모든 Eloquent relationship들과 같이, 일대다 relationship들은 Eloquent 모델에 함수를 통해서 정의됩니다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Post extends Model
    {
        /**
         * Get the comments for the blog post.
         */
        public function comments()
        {
            return $this->hasMany('App\Comment');
        }
    }

앞서 언급했듯이 Eloquent는 `Comment` 모델에 적절한 외래 키를 자동으로 결정합니다. Eloquent는 관례적으로 소유하는 모델의 "snake case" 이름에 `_id`를 붙일 것입니다. 따라서 이 예에서 Eloqent는 `Comment` 모델의 외래 키가 `post_id`일 것이라고 추정합니다.

관계가 정의되었다면 `comments` 속성에 접근하여 댓글 모음에 엑세스할 수 있습니다. 앞서 말했듯이 Eloquent는 "동적 속성"을 제공하기 때문에 모델의 속성에 접근하듯이 relationship-관계 메소드에 접근할 수 있습니다.

    $comments = App\Post::find(1)->comments;

    foreach ($comments as $comment) {
        //
    }

모든 관계들은 쿼리 빌더로도 역할하기 때문에 `comments` 메소드를 호출하고 쿼리에 조건들을 계속해서 체인하는 것을 통해 어떤 댓글들이 조회되는지에 대한 제한들을 추가할 수 있습니다.

    $comment = App\Post::find(1)->comments()->where('title', 'foo')->first();

`hasOne` 메소드와 같이 `hasMany` 메소드에 추가적인 인자들을 전달하여 외래 및 로컬 키들을 재지정 할 수 있습니다.

    return $this->hasMany('App\Comment', 'foreign_key');

    return $this->hasMany('App\Comment', 'foreign_key', 'local_key');

<a name="one-to-many-inverse"></a>
### 1:*(일대다) 역관계

이제 게시물의 모든 댓글에 접근할 수 있으니 댓글이 부모 게시물에 접근할 수 있도록 해주는 관계를 정의해 봅시다. `hasMany` 관계의 반대를 정의하려면 `belongsTo` 메소드를 호출하는 자식 모델에 관계 함수를 정의하면 됩니다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Comment extends Model
    {
        /**
         * Get the post that owns the comment.
         */
        public function post()
        {
            return $this->belongsTo('App\Post');
        }
    }

관계가 정의되었다면 `post` "동적 속성"에 접근하여서 `Comment`에 대한 `Post` 모델을 찾을 수 있습니다.

    $comment = App\Comment::find(1);

    echo $comment->post->title;

위의 예에서 Eloqent는 `Comment` 모델의 `post_id`와 `Post` 모델의 `id`를 비교해볼 것입니다. Eloquent는 relationship 메소드의 이름을 검사하고 메소드 이름 뒤에 `_`와 기본 키 컬럼을 붙여서 외래 키의 기본 이름을 결정합니다. 하지만 `Comment` 모델의 외래 키가 `post_id`가 아니라면 커스텀 키 이름을 두번째 인자로 `belongsTo`에 전달하면 됩니다.

    /**
     * Get the post that owns the comment.
     */
    public function post()
    {
        return $this->belongsTo('App\Post', 'foreign_key');
    }

부모 모델이 `id`를 프라이머리 키로 사용하지 않거나 자식 모델을 다른 컬럼에 조인시키고 싶으면 부모 테이블의 커스텀 키를 지정하는 세번째 인자를 `belongsTo` 메소드로 전달하면 됩니다.

    /**
     * Get the post that owns the comment.
     */
    public function post()
    {
        return $this->belongsTo('App\Post', 'foreign_key', 'other_key');
    }

<a name="many-to-many"></a>
### \*:* (다대다) 관계 정의하기

다대다 관계는 `hasOne`과 `hasMany` 관계들에 비해서 조금 더 복잡합니다. 이런 관계의 예로 사용자가 여러 역할을 가지면서 그 역할들이 다른 사용자와 공유되는 경우가 있습니다. 예를 들어 여러 사용자들이 "Admin" 역할을 할 수 있습니다. 이 관계를 정의하기 위해는 `users`, `roles`, 그리고 `role_user`의 3개의 데이터베이스 테이블이 필요합니다. `role_user` 테이블은 관련된 모델 이름의 알파펫 순으로부터 정렬되며 `user_id`와 `role_id` 컬럼을 가지고 있습니다.

다대다 관계는 `belongsToMany` 메소드의 결과를 반환하는 메소드를 작성하여 정의합니다. 예를 들어 `User` 모델에 `roles` 메소드를 정의해보도록 하겠습니다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * The roles that belong to the user.
         */
        public function roles()
        {
            return $this->belongsToMany('App\Role');
        }
    }

관계가 정의되었다면 `roles` 동적 속성을 사용하여 사용자들의 역할에 접근할 수 있습니다.

    $user = App\User::find(1);

    foreach ($user->roles as $role) {
        //
    }

다른 모든 관계 타입과 같이 `roles` 메소드를 호출하여 관계에 대해서 쿼리 제한 조건들을 계속하여 체이닝 할 수 있습니다.

    $roles = App\User::find(1)->roles()->orderBy('name')->get();

앞서 살펴본 바와같이, Eloquent는 relationship-관계의 조인(join) 테이블의 테이블 명을 결정하기 위해 관련된 두 모델 이름을 알파벳 순으로 합칠 것입니다. (user 와 role 일 경우에 role_user 로 이름을 추정합니다) 하지만 이러한 관례는 재지정 할 수 있습니다. `belongsToMany` 메소드로 두번째 인자를 전달하면 됩니다.

    return $this->belongsToMany('App\Role', 'role_user');

join 테이블의 이름을 커스터마이징하는 것 외에도 `belongsToMany` 메소드에 추가 인자들을 전달하면 키들의 이름들 또한 커스터마이징 할 수 있습니다. 세번째 인자는 관계를 정의하는 모델의 외래 키 이름이고 네번째 인자는 join 하는 모델의 외래 키 이름입니다.

    return $this->belongsToMany('App\Role', 'role_user', 'user_id', 'role_id');

#### 관계의 역(반대) 정의하기

다대다 관계의 반대를 정의하려면 관련된 모델에 `belongsToMany`를 호출합니다. 사용자와 역할에 대한 예를 계속 들어 생각해서, `Role` 모델에 `users` 메소드를 정의해봅시다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Role extends Model
    {
        /**
         * The users that belong to the role.
         */
        public function users()
        {
            return $this->belongsToMany('App\User');
        }
    }

위에서 볼 수 있듯이 `App\User` 모델을 참고하는 것만 제외하고 relationship-관계는 대응하는 `User`와 정확히 동일하게 정의되어 있습니다. `belongsToMany` 메소드를 재사용하고 있기 때문에 다대다 관계의 역-반대를 정의할 때에는 테이블과 키의 모든 통상적인 커스터마이즈 옵션이 사용 가능합니다.

#### 중간 테이블 컬럼 조회하기

앞서 알아 보았듯이, 다대다 관계는 중간 테이블의 존재를 필요로 합니다. Eloquent는 이 테이블과 상호 작용할 수 있게 도움을 주는 몇몇 방법들을 제공합니다. 예를 들어, `User` 객체가 여러 `Role` 객체에 관련되어 있다고 생각해 봅시다. 이 관계에 접근한 후 모델들에 `pivot` 속성을 사용하여 중간 테이블에 접근할 수 있습니다.

    $user = App\User::find(1);

    foreach ($user->roles as $role) {
        echo $role->pivot->created_at;
    }

조회되는 각 `Role` 모델은 자동으로 `pivot` 속성을 부여받습니다. 이 속성은 중간 테이블을 나타내는 모델을 포함하고 있으며 여느 Eloquent 모델과 다르지 않게 사용될 수 있습니다.

기본적으로 `pivot` 객체에는 모델 키만 존재할 것입니다. Pivot 테이블이 추가 속성들을 포함한다면 관계를 정의할 때 명시해야 합니다.

    return $this->belongsToMany('App\Role')->withPivot('column1', 'column2');

Pivot 테이블이 자동으로 유지되는 `created_at`와 `updated_at` 타임스탬프를 갖기 원한다면 관계 정의에 `withTimestamps` 메소드를 사용하면 됩니다.

    return $this->belongsToMany('App\Role')->withTimestamps();

#### `pivot` 속성의 이름 커스터마이징 하기

앞서 이야기 한것처럼, 모델에서 `pivot` 속성을 사용하여 중간 테이블의 속성에 엑세스 할 수 있습니다. 그렇지만 애플리케이션에서 용도를 보다 명확하게 표현할 수 있도록 속성의 이름을 자유롭게 커스터마이징 할 수 있습니다.

예를 들어, 애플리케이션에서 팟캐스트를 등록할 수 있는 사용자를 가지는 경우, 사용자와 팟캐스트는 다대다 관계를 형성할 수 있습니다. 이 경우 중간 테이블에 엑세스 하는 `pivot` 대신에 `subscription` 으로 이름을 변경할 수 있습니다. 이는 관계를 정의 할 때 `as` 메소드를 사용하여 지정하면 됩니다.

    return $this->belongsToMany('App\Podcast')
                    ->as('subscription')
                    ->withTimestamps();

이 후에는, 다음과 같이 커스터마이징한 이름을 사용하여 중간 테이블에 엑세스 할 수 있습니다.

    $users = User::with('podcasts')->get();

    foreach ($users->flatMap->podcasts as $podcast) {
        echo $podcast->subscription->created_at;
    }

#### 중간 테이블의 컬럼을 사용한 관계의 필터링

관계를 정의할 때, `wherePivot` 과 `wherePivotIn` 메소드를 사용하여 `belongsToMany`이 반환하는 결과를 필터링 할 수도 있습니다.

    return $this->belongsToMany('App\Role')->wherePivot('approved', 1);

    return $this->belongsToMany('App\Role')->wherePivotIn('priority', [1, 2]);

<a name="defining-custom-intermediate-table-models"></a>
### 커스텀 중간 테이블 모델 정의하기

관계의 중간 테이블을 표현하기 위해서 커스텀 모델을 정의하려면, 관계를 정의할 때 `using` 메소드를 호출하면 됩니다. 커스텀 다대다 피벗 모델은 `Illuminate\Database\Eloquent\Relations\Pivot` 클래스를 확장해야하며 커스텀 다형성 다대다 피벗 모델은 `Illuminate\Database\Eloquent\Relations\MorphPivot` 클래스를 확장해야합니다. 예를 들어, 커스텀 `RoleUser` 피벗 모델을 사용하는 `Role` 을 정의할 수 있습니다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Role extends Model
    {
        /**
         * The users that belong to the role.
         */
        public function users()
        {
            return $this->belongsToMany('App\User')->using('App\RoleUser');
        }
    }

`RoleUser` 모델을 정의할 때에는 `Pivot` 클래스를 상속합니다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Relations\Pivot;

    class RoleUser extends Pivot
    {
        //
    }

중간 테이블에서 열을 검색하기 위해 `using` 과 `withPivot` 을 결합 할 수 있습니다. 예를 들어, 컬럼 이름을 `withPivot` 메소드에 전달함으로써 `created_by` 와 `updated_by` 컬럼을 `RoleUser` 피벗 테이블에서 검색 할 수 있습니다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Role extends Model
    {
        /**
         * The users that belong to the role.
         */
        public function users()
        {
            return $this->belongsToMany('App\User')
                            ->using('App\RoleUser')
                            ->withPivot([
                                'created_by',
                                'updated_by',
                            ]);
        }
    }

> **Note:** 피벗 모델은 `SoftDeletes` 특성을 사용할 수 없습니다. 피벗 레코드를 소프트 삭제해야하는 경우 피벗 모델을 실제 Eloquent 모델로 변환하는 것이 좋습니다.

#### 커스텀 피벗 모델 및 Incrementing IDs

커스텀 피벗 모델을 사용하는 다대다 관계를 정의하고 피벗 모델에 auto-incrementing 기본 키가 있을 경우 커스텀 피벗 모델 클래스의 ```incrementing` 속성이 `true`로 되어 있는지 확인해야 합니다.

    /**
     * Indicates if the IDs are auto-incrementing.
     *
     * @var bool
     */
    public $incrementing = true;

<a name="has-one-through"></a>
### 연결을 통한 단일 관계

"연결을 통한" 관계는 하나의 중간 테이블을 통해 연결합니다.

예를 들어 각 공급자(suppliers)가 한 명의 사용자(users)를 가지고 있고 각 사용자가 한 개의 사용자 이력(history) 레코드와 연결된 경우 공급자 모델은 사용자의 기록을 통해 접근할 수 있습니다. 이 관계를 정의하는 데 필요한 데이터베이스 테이블을 살펴보겠습니다.

    users
        id - integer
        supplier_id - integer

    suppliers
        id - integer

    history
        id - integer
        user_id - integer

`history` 테이블에는 `supplier_id` 컬럼이 포함되지 않았지만 `hasOneThrough` 관계는 공급자(suppliers) 모델에 대한 사용자 이력(history)에 대한 접근을 제공할 수 있습니다. 이제 관계에 대한 테이블 구조를 살펴보았으므로 `Supplier` 모델에 대해 정의해보겠습니다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Supplier extends Model
    {
        /**
         * Get the user's history.
         */
        public function userHistory()
        {
            return $this->hasOneThrough('App\History', 'App\User');
        }
    }

`hasOneThrough` 메소드로 전달되는 첫번째 인자는 접근하고자 하는 최종 모델의 이름이며 두번째 인자는 중간 모델의 이름입니다.

관계 쿼리를 수행할 때 일반적인 Eloquent 외래 키 규칙이 사용됩니다. 관계 키를 커스텀하러면 세번째 및 네번째 인자로 `hasOneThrough` 메소드에 전달 할 수 있습니다. 세번째 인자는 중간 모델의 외래 키입니다. 네번째 인자는 최종 모델의 외래 키입니다. 다섯번째 인자는 로컬 키며, 여섯번째 인자는 중간 모델의 로컬 키입니다.

    class Supplier extends Model
    {
        /**
         * Get the user's history.
         */
        public function userHistory()
        {
            return $this->hasOneThrough(
                'App\History',
                'App\User',
                'supplier_id', // Foreign key on users table...
                'user_id', // Foreign key on history table...
                'id', // Local key on suppliers table...
                'id' // Local key on users table...
            );
        }
    }

<a name="has-many-through"></a>
### 연결을 통한 다수를 가지는 관계 정의하기

"연결을 통한 다수를 가지는" 관계는 중간 테이블을 통해서, 서로 떨어진 관계들에 접근하는 편리한 방법을 제공합니다. 예를 들어, `Country` 모델은 중간 `User` 모델을 통해 다수의 `Post` 모델을 가지고 있을 수 있습니다. 이 예제에서는 특정 국가에 대한 모든 블로그 게시물을 쉽게 확인할 수 있습니다. 이 관계를 정의하기 위해 요구되는 테이블들을 살펴보겠습니다.

    countries
        id - integer
        name - string

    users
        id - integer
        country_id - integer
        name - string

    posts
        id - integer
        user_id - integer
        title - string

`posts`는 `country_id` 컬럼을 포함하고 있지 않지만 `hasManyThrough` 관계는 `$country->posts`를 통해 컨트리에 대한 접근을 제공합니다. 이 쿼리를 수행하기 위해 Eloquent는 중간 `users` 테이블의 `country_id`를 검사합니다. 일치하는 사용자 ID를 찾은 후에는 `posts` 테이블을 쿼리하는데 사용합니다.

relationship-관계를 위한 테이블 구조를 살펴보았으니 이제 `Country` 모델에 정의해보도록 합시다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Country extends Model
    {
        /**
         * Get all of the posts for the country.
         */
        public function posts()
        {
            return $this->hasManyThrough('App\Post', 'App\User');
        }
    }

`hasManyThrough` 메소드로 전달되는 첫번째 인자는 접근하고자 하는 최종 모델의 이름이며 두번째 인자는 중간 모델의 이름입니다.

관계의 쿼리를 수행할 때 전형적인 Eloquent 외래 키 관례들이 사용될 것입니다. 관계의 키를 사용자가 정의하고 싶다면 세번째와 네번재 인자로 `hasManyThrough` 메소드에 전달할 수 있습니다. 세번째 인자는 중간 모델의 외래 키 이름입니다. 네번째 인자는 최종 모델의 외래 키 이름입니다. 다섯번째 키는 로컬 키이고, 여섯번째 키는 중간 모델 로컬 키입니다.

    class Country extends Model
    {
        public function posts()
        {
            return $this->hasManyThrough(
                'App\Post',
                'App\User',
                'country_id', // Foreign key on users table...
                'user_id', // Foreign key on posts table...
                'id', // Local key on countries table...
                'id' // Local key on users table...
            );
        }
    }

<a name="polymorphic-relationships"></a>
## 다형성 관계

다형성 관계는 대상 모델이 하나 이상의 타입의 모델에 소속될 수 있도록 해줍니다.

<a name="one-to-one-polymorphic-relations"></a>
### 1:1(일대일) (다형성)

#### 테이블 구조

일대일 다형성 관계는 기본적인 일대일 관계와 비슷합니다; 다만, 대상 모델은 하나의 연결에서 하나 이상의 모델에 소속될 수 있습니다. 예를 들자면, 블로그 `Post` 와 `User` 는 `Image` 모델에 다형성 관계를 서로 공유 할 수 있습니다. 일대일 다형성 관계를 사용한다면 블로그 포스트와 사용자에 소속되는 하나의 이미지를 구성할 수 있습니다. 먼저 테이블 구조를 살펴보겠습니다.

    posts
        id - integer
        name - string

    users
        id - integer
        name - string

    images
        id - integer
        url - string
        imageable_id - integer
        imageable_type - string

`images` 테이블에서 `imageable_id` 와 `imageable_type` 컬럼을 확인하십시오. `imageable_id` 컬럼은 포스트 또는 사용자의 ID 값이 저장되고, `imageable_type` 컬럼에는 상위 모델 클래스의 이름이 저장됩니다. `imageable_type` 컬럼은 Eloquent 가 `imageable` 관계에서 값을 반환할때 어떠한 "타입"과 연결해야 하는지 결정하는데 사용됩니다.

#### 모델 구조

다음으로, 이러한 관계를 형성하기 위해서 모델에서 필요한 정의사항을 살펴보겠습니다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Image extends Model
    {
        /**
         * Get the owning imageable model.
         */
        public function imageable()
        {
            return $this->morphTo();
        }
    }

    class Post extends Model
    {
        /**
         * Get the post's image.
         */
        public function image()
        {
            return $this->morphOne('App\Image', 'imageable');
        }
    }

    class User extends Model
    {
        /**
         * Get the user's image.
         */
        public function image()
        {
            return $this->morphOne('App\Image', 'imageable');
        }
    }

#### 관계 조회하기

데이터베이스 테이블과 모델을 정의하고나면, 모델에서 관계 모델에 엑세스 할 수 있습니다. 예를들어 포스트의 이미지를 조회한다면, `image` 라는 동적 속성을 사용하면 됩니다.

    $post = App\Post::find(1);

    $image = $post->image;

`morphTo` 에 대한 호출을 실행하는 메소드의 이름에 엑세스해서 다형성 모델의 상위 모델을 조회 할 수도 있습니다. 이 경우에서는, `Image` 모델의 `imageable` 메소드가 됩니다. 따라서 다음과 같이 동적 속성을 사용해서 메소드에 접근합니다.

    $image = App\Image::find(1);

    $imageable = $image->imageable;

`Image` 모델의 `imageable` 관계는 이미지를 소유한 모델의 타입에 따라서 `Post` 또는 `User` 인스턴스를 반환합니다.

<a name="one-to-many-polymorphic-relations"></a>
### 1:*(일대다) (다형성)

#### 테이블 구조

일대다 다형성 관계는 기본적인 일대다 관계와 비슷합니다; 다만, 대상 모델은 하나의 연결에서 하나 이상의 모델에 소속될 수 있습니다. 예를 들어 애플리케이션의 사용자가 게시글과 비디오 둘다 "댓글"를 달 수 있다고 생각해 보겠습니다. 다형성 관계를 이용하면 이 두 시나리오 모두 지원하는 하나의 `comments` 테이블을 사용할 수 있습니다. 먼저 이 관계를 구성하기 위해 필요한 테이블 구조를 살펴보겠습니다.

    posts
        id - integer
        title - string
        body - text

    videos
        id - integer
        title - string
        url - string

    comments
        id - integer
        body - text
        commentable_id - integer
        commentable_type - string

#### 모델 구조

다음으로 이 관계를 형성하기 위해 필요한 모델 정의들을 살펴보도록 하겠습니다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Comment extends Model
    {
        /**
         * Get the owning commentable model.
         */
        public function commentable()
        {
            return $this->morphTo();
        }
    }

    class Post extends Model
    {
        /**
         * Get all of the post's comments.
         */
        public function comments()
        {
            return $this->morphMany('App\Comment', 'commentable');
        }
    }

    class Video extends Model
    {
        /**
         * Get all of the video's comments.
         */
        public function comments()
        {
            return $this->morphMany('App\Comment', 'commentable');
        }
    }

#### 관계-Relationship 조회하기

데이터베이스 테이블과 모델이 정의되면 관계-Relationship를 통해 모델에 접근 할 수 있습니다. 예를 들어, 게시물의 모든 댓글에 접근하기 위해 `comments` 동적 속성을 사용할 수 있습니다.

    $post = App\Post::find(1);

    foreach ($post->comments as $comment) {
        //
    }

`morphTo`에 대한 호출을 수행하는 메소드의 이름에 액세스하여 다형성 모델에서 다형성 관계의 소유자를 검색 할 수도 있습니다. 지금의 상황이라면 `Comment` 모델의 `commentable` 메소드입니다. 따라서 해당 메소드를 동적 속성으로 액세스합니다.

    $comment = App\Comment::find(1);

    $commentable = $comment->commentable;

`Comment` 모델의 `commentable` 관계는 댓글을 소유한 모델 타입에 따라 `Post` 또는 `Video` 인스턴스를 반환합니다.

(역자주: 말이 어렵지만 위의 섹션에서 모델을 정의한 코드만 보면 `$comment->commentable()`만 정의해뒀지만 `$comment->commentable`로 사용할 경우 라라벨이 자동으로 해당 댓글의 부모가 뭔지 찾아서 그에 맞는 객체를 가져와 준다는 의미입니다)

<a name="many-to-many-polymorphic-relations"></a>
### \*:*(다대다) (다형성)

#### 테이블 구조

다대다 다형성 관계는 `morphOne`과 `morphMany` 관계보다 약간 더 복잡합니다. 예를 들어 블로그에서 `Post`및 `Video`모델은 `Tag`모델과 다형성 관계를 공유 할 수 있습니다. 다대다 다형성 관계를 사용하면 블로그 게시물과 비디오에서 공유되는 고유한-unique 태그로 구성된 하나의 목록을 가질 수 있습니다. 먼저 테이블 구조를 살펴 보겠습니다.

    posts
        id - integer
        name - string

    videos
        id - integer
        name - string

    tags
        id - integer
        name - string

    taggables
        tag_id - integer
        taggable_id - integer
        taggable_type - string

#### 모델 구조

다음으로, 모델의 관계를 정의해보겠습니다. `Post` 및 `Video` 모델에는 기본 Eloquent 클래스에서 `morphToMany` 메소드를 호출하는 `tags` 메소드를 추가합니다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Post extends Model
    {
        /**
         * Get all of the tags for the post.
         */
        public function tags()
        {
            return $this->morphToMany('App\Tag', 'taggable');
        }
    }

#### 역관계 정의하기

다음으로, `Tag` 모델에서 관련된 각 모델들을 위해 메소드를 정의해야 합니다. 이 예제에서는 `posts` 메소드와 `videos` 메소드를 정의하겠습니다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Tag extends Model
    {
        /**
         * Get all of the posts that are assigned this tag.
         */
        public function posts()
        {
            return $this->morphedByMany('App\Post', 'taggable');
        }

        /**
         * Get all of the videos that are assigned this tag.
         */
        public function videos()
        {
            return $this->morphedByMany('App\Video', 'taggable');
        }
    }

#### 관계 조회하기

데이터베이스 테이블과 모델들이 정의되었다면 모델을 통해 관계에 접근할 수 있습니다. 예를 들어, 어떤 게시물의 모든 태그에 접근하려면, `tags` 동적 속성을 사용하면 됩니다.

    $post = App\Post::find(1);

    foreach ($post->tags as $tag) {
        //
    }

또한 `morphedByMany`의 호출을 수행하는 메소드의 이름에 접근하여 다형성 모델에서 다형성 관계의 소유자를 조회할 수 있습니다. 이 예에서는 `Tag` 모델에 사용되는`posts`나 `videos` 메소드를 말합니다. 따라서 이 메소드들은 동적 속성으로 접근할 것입니다.

    $tag = App\Tag::find(1);

    foreach ($tag->videos as $video) {
        //
    }

<a name="custom-polymorphic-types"></a>
### 커스텀-사용자 정의 다형성 타입

기본적으로, 라라벨은 관련된 모델의 유형을 저장하기 위해서 전체 클래스 이름을 사용합니다. 예를 들어 위의 일대일 관계 예제에서 `Comment` 는 하나의 `Post` 또는 하나의 `Video` 에 지정되고, 기본적으로 `commentable_type` 의 각각 `App\Post` 또는 `App\Video` 이 될 수 있습니다. 그렇지만, 여러분은 데이터베이스와 애플리케이션의 내부 구조를 분리하고자 할 수 있습니다. 이 경우 여러분은 관계 설정을 위한 "morph map"을 정의하고 클래스 이름 대신 사용할 각각의 모델과 관련 있는 고유한 이름을 Eloquent 에 지시할 수 있습니다.

    use Illuminate\Database\Eloquent\Relations\Relation;

    Relation::morphMap([
        'posts' => 'App\Post',
        'videos' => 'App\Video',
    ]);

여러분은 `AppServiceProvider` 의 `boot` 안에서 `morphMap` 를 등록 하거나, 원한다면 분리된 서비스 프로바이더를 만들 수도 있을 것입니다.

> {note} 기존 애플리케이션에 "morph map"을 추가 할 때 정규화 된 클래스를 포함하고 있는 데이터베이스의 모든 변형 가능한 `*_type` 컬럼 값을 "map"이름으로 변환해야합니다.

<a name="querying-relations"></a>
## 관계 쿼리 질의하기

모든 Eloquent 관계는 메소드를 통해 정의되기 때문에, 관계 쿼리를 실제로 실행하는 대신에 이 메소드을 호출하여 관계 인스턴스를 가져올 수 있습니다. 모든 종류의 Eloquent 모델들은 또한 [쿼리 빌더](/docs/{{version}}/queries)의 역할을 하기 때문에 데이터베이스에 최종적으로 SQL을 실행하기 전에 관계 쿼리에 제한(where 구문)을 체이닝(호출->호출->호출 형태의 질의) 할 수 있도록 해줍니다.

예를 들어, `User` 모델이 다수의 `Post` 모델에 연관되어 있는 블로그 시스템을 상상해 봅시다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * Get all of the posts for the user.
         */
        public function posts()
        {
            return $this->hasMany('App\Post');
        }
    }

다음과 같이 `posts` 관계들을 쿼리하고 관계에 제한들을 추가할 수 있습니다.

    $user = App\User::find(1);

    $user->posts()->where('active', 1)->get();

관계에 대해서 어떠한 [쿼리 빌더](/docs/{{version}}/queries) 메소드도 사용 가능하기 때문에, 가능한 메소드들에 대해서 확인하려면 쿼리 빌더에 대한 문서를 확인하시기 바랍니다.

#### 관계 정립 후에 `orWhere` 절 연결하기

위 예에서 설명한 것처럼 쿼리 할 때 관계에 제약 조건을 추가 할 수 있습니다. 그러나 `orWhere` 절이 관계 제약 조건과 같은 수준으로 논리적으로 그룹화되므로 `orWhere` 절을 관계에 연결하는 경우 주의해야합니다.

    $user->posts()
            ->where('active', 1)
            ->orWhere('votes', '>=', 100)
            ->get();

    // select * from posts
    // where user_id = ? and active = 1 or votes >= 100

대부분의 상황에서 [제약조건 그룹](/docs/{{version}}/queries#parameter-grouping)을 사용하여 괄호 사이에 조건부 검사를 논리적으로 그룹화합니다.

    use Illuminate\Database\Eloquent\Builder;

    $user->posts()
            ->where(function (Builder $query) {
                return $query->where('active', 1)
                             ->orWhere('votes', '>=', 100);
            })
            ->get();

    // select * from posts
    // where user_id = ? and (active = 1 or votes >= 100)

<a name="relationship-methods-vs-dynamic-properties"></a>
### 관계 메소드 Vs. 동적 속성

Eloquent 관계 쿼리에 제한을 추가할 필요가 없다면 속성처럼 관계에 접근할 수 있습니다. 예를 들어, `User`와 `Post` 예시 모델들을 계속해서 사용하면 다음과 같이 사용자의 모든 게시물에 접근할 수 있습니다.

    $user = App\User::find(1);

    foreach ($user->posts as $post) {
        //
    }

동적 속성은 "지연 로딩"으로, 실제로 엑세스를 해야만 관계 데이터를 로드합니다. 그렇게 때문에 개발자들은 종종 모델을 로드한 뒤 접근해야할 관계들을 미리 로드해주는 [eager 로딩](#eager-loading)을 사용합니다. Eager 로딩은 모델의 관계를 로드하기 위해 실행되어야 할 SQL 쿼리들을 유의미하게 줄여줍니다.

<a name="querying-relationship-existence"></a>
### 관계의 존재 여부 쿼리 질의하기

모델의 기록에 접근할 때, 관계의 존재 여부에 따라 결과를 제한하기 원할 수 있습니다. 예를 들어 하나 이상의 댓글을 가진 모든 블로그 게시물을 조회하려고 한다고 생각해 봅시다. 이를 위해서 `has` 또는 `orHas` 메소드로 관계의 이름을 전달할 수 있습니다.

    // Retrieve all posts that have at least one comment...
    $posts = App\Post::has('comments')->get();

또한 메소드에서 사용할 수 있는 연산자와 카운트 개수를 지정하여 쿼리를 계속하여 커스터마이즈할 수 있습니다.

    // Retrieve all posts that have three or more comments...
    $posts = App\Post::has('comments', '>=', 3)->get();

중첩된 `has` 구문(statement)은 "점(.)" 표기를 사용하여 구성될 수 있습니다. 예를 들어, 최소한 하나의 댓글과 좋아요(vote)를 가진 모든 게시물을 조회할 수 있습니다.

    // Retrieve posts that have at least one comment with votes...
    $posts = App\Post::has('comments.votes')->get();

더 많은 권한이 필요하다면 `whereHas`와 `orWhereHas` 메소드를 사용하여 `has` 쿼리에 "where" 조건을 추가할 수 있습니다. 이 메소드들은 관계 제한에 댓글 컨텐츠 확인과 같은 사용자 정의된 제한들을 추가할 수 있게 해줍니다.

    use Illuminate\Database\Eloquent\Builder;

    // Retrieve posts with at least one comment containing words like foo%...
    $posts = App\Post::whereHas('comments', function (Builder $query) {
        $query->where('content', 'like', 'foo%');
    })->get();

    // Retrieve posts with at least ten comments containing words like foo%...
    $posts = App\Post::whereHas('comments', function (Builder $query) {
        $query->where('content', 'like', 'foo%');
    }, '>=', 10)->get();

<a name="querying-relationship-absence"></a>
### 관계된 모델이 존재하지 않는 것을 확인하며 질의하기

모델의 레코드에 엑세스 할 때, 관계된 모델이 존재하지 않는 것에 따라서 결과를 제한하고자 할 수 있습니다. 예를 들어 코멘트를 가지고 있지 **않은** 모든 블로그 포스트를 조회하는 경우를 생각해 보겠습니다. 이렇게 하기 위해서는 `doesntHave` 또는 `orDoesntHave` 메소드에 정의한 관계의 이름을 전달하면됩니다.

    $posts = App\Post::doesntHave('comments')->get();

더 강력한 기능을 원한다면, `doesntHave` 쿼리에 "where" 조건을 붙여서, `whereDoesntHave` 와 `orWhereDoesntHave` 메소드를 사용할 수 있습니다. 이 메소드는 코멘트의 내용을 확인하는 것과 같이 관계 제약에 커스터마이징된 제약을 추가해준다.

    use Illuminate\Database\Eloquent\Builder;

    $posts = App\Post::whereDoesntHave('comments', function (Builder $query) {
        $query->where('content', 'like', 'foo%');
    })->get();

중첩된 관계에 대해서는 "점(.)" 표기법을 사용하여 쿼리를 질의할 수 있습니다. 예를들어, 다음의 쿼리는 작성자가 규제 받지 않은 모든 포스트와 코멘트를 조회합니다.

    use Illuminate\Database\Eloquent\Builder;

    $posts = App\Post::whereDoesntHave('comments.author', function (Builder $query) {
        $query->where('banned', 1);
    })->get();

<a name="querying-polymorphic-relationships"></a>
### 다형성 관계 쿼리

`MorphTo` 관계의 존재를 쿼리하기 위해서 `whereHasMorph` 메소드와 그것에 상응하는 메소드를 사용할 수 있습니다.

    use Illuminate\Database\Eloquent\Builder;

    // Retrieve comments associated to posts or videos with a title like foo%...
    $comments = App\Comment::whereHasMorph(
        'commentable',
        ['App\Post', 'App\Video'],
        function (Builder $query) {
            $query->where('title', 'like', 'foo%');
        }
    )->get();

    // Retrieve comments associated to posts with a title not like foo%...
    $comments = App\Comment::whereDoesntHaveMorph(
        'commentable',
        'App\Post',
        function (Builder $query) {
            $query->where('title', 'like', 'foo%');
        }
    )->get();

`$type` 파라미터를 사용하여 관련 모델에 따라 다른 제약 조건을 추가 할 수 있습니다.

    use Illuminate\Database\Eloquent\Builder;

    $comments = App\Comment::whereHasMorph(
        'commentable',
        ['App\Post', 'App\Video'],
        function (Builder $query, $type) {
            $query->where('title', 'like', 'foo%');

            if ($type === 'App\Post') {
                $query->orWhere('content', 'like', 'foo%');
            }
        }
    )->get();

가능한 다형성 모델 배열을 전달하는 대신에 `*`를 와일드 카드로 제공하고 Laravel로 하여금 가능한 모든 다형성 타입을 데이터베이스에서 검색하도록 할 수 있습니다. Laravel은 이 작업을 수행하기 위해 추가 쿼리를 실행합니다.

    use Illuminate\Database\Eloquent\Builder;

    $comments = App\Comment::whereHasMorph('commentable', '*', function (Builder $query) {
        $query->where('title', 'like', 'foo%');
    })->get();

<a name="counting-related-models"></a>
### 연관된 모델의 개수 확인하기-카운팅

관계 결과의 개수를 실제 레코드를 로딩하지 않고 알고자 한다면, `withCount` 메소드를 사용하여 건수는 결과 모델의 `{관계}_count` 컬럼에 위치하도록 할 수 있습니다. 예를 들어:

    $posts = App\Post::withCount('comments')->get();

    foreach ($posts as $post) {
        echo $post->comments_count;
    }

다수의 관계에 대해서도 쿼리에 제약을 추가하여 "개수"를 조회할 수 있습니다.

    use Illuminate\Database\Eloquent\Builder;

    $posts = App\Post::withCount(['votes', 'comments' => function (Builder $query) {
        $query->where('content', 'like', 'foo%');
    }])->get();

    echo $posts[0]->votes_count;
    echo $posts[0]->comments_count;

동일한 관계에 대하여 여러번 카운트를 수행하기 위해서 카운트 결과에 별칭(alias)를 부여할 수도 있습니다.

    use Illuminate\Database\Eloquent\Builder;

    use Illuminate\Database\Eloquent\Builder;

    $posts = App\Post::withCount([
        'comments',
        'comments as pending_comments_count' => function (Builder $query) {
            $query->where('approved', false);
        },
    ])->get();

    echo $posts[0]->comments_count;

    echo $posts[0]->pending_comments_count;

`withCount` 와 `select` 구문을 조합한다면, `select` 메소드 뒤에 `withCount` 호출하도록 구성하십시오.

    $posts = App\Post::select(['title', 'body'])->withCount('comments')->get();

    echo $posts[0]->title;
    echo $posts[0]->body;
    echo $posts[0]->comments_count;

또한 `loadCount` 메소드를 사용하여 상위 모델이 이미 처리된 후에도 관계의 수를 로드 할 수 있습니다.

    $book = App\Book::first();

    $book->loadCount('genres');

eager loading 쿼리에 쿼리 제약 조건을 추가 설정해야하는 경우 로드하려는 관계에 키에 배열을 전달할 수 있습니다. 배열의 값은 쿼리 빌더 인스턴스를 받는 `Closure` 인스턴스 여야합니다.

    $book->loadCount(['reviews' => function ($query) {
        $query->where('rating', 5);
    }])

<a name="eager-loading"></a>
## Eager 로딩

Eloquent 관계들을 속성으로 접근할 때 관계 데이터는 "지연 로드" 되어있습니다. 이는 속성에 엑세스 하기 전까지 관계 데이터가 실제로 로드되지 않는다는 것을 의미합니다. 하지만 Eloquent는 부모 모델을 쿼리할 때 관계를 "eager 로드"할 수도 있습니다. Eager 로딩은 N + 1 쿼리 문제를 해결 합니다. N + 1 쿼리 문제에 대한 예제를 들어보자면 `Author`에 연관된 `Book` 모델을 생각해볼 수 있습니다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Book extends Model
    {
        /**
         * Get the author that wrote the book.
         */
        public function author()
        {
            return $this->belongsTo('App\Author');
        }
    }

이제 모든 책과 그 저자들을 조회해봅시다.

    $books = App\Book::all();

    foreach ($books as $book) {
        echo $book->author->name;
    }

이 반복문은 테이블에서 모든 책들을 조회하는 1 개의 쿼리를 실행하고, 각각의 책마다 저자를 조회하는 별개의 쿼리를 실행할 것입니다. 따라서, 만약 25개의 책이 있다면, 이 반복문은 원래 책을 위한 하나의 쿼리와 각 책의 저자를 조회하는 25개의 추가적인 쿼리, 총 26개의 쿼리를 실행하게 됩니다.

다행히도, eager 로딩을 사용하면 이 작업을 2개의 쿼리로 줄일 수 있습니다. 쿼리를 실행할 때 `with` 메소드를 사용하여 어떤 관계가 eager 로드되어야 하는지 지정할 수 있습니다.

    $books = App\Book::with('author')->get();

    foreach ($books as $book) {
        echo $book->author->name;
    }

이 작업에서는 두 개의 쿼리만이 실행될 것입니다.

    select * from books

    select * from authors where id in (1, 2, 3, 4, 5, ...)

#### 여러 관계에 대해서 Eager 로딩하기

종종 하나의 작업에서 여러 개의 다른 관계들을 eager 로드해야 될 때가 있습니다. 이 경우 `with` 메소드에 추가 인자들을 전달하면 됩니다.

    $books = App\Book::with(['author', 'publisher'])->get();

#### 중첩된 Eager 로딩하기

"점" 구문을 이용하면 중첩된 관계들을 eager 로드할 수 있습니다. 예를 들어, 하나의 Eloquent 구문(statement)에서 책의 모든 저자들과 저자들의 모든 연락처를 eager 로드해보겠습니다.

    $books = App\Book::with('author.contacts')->get();

#### 중첩 된 `morphTo` 관계의 Eager Loading

`morphTo` 관계뿐만 아니라 그 관계에 의해 리턴 될 수있는 다양한 엔티티들에 중첩 된 관계를 eager 로드 하고 싶다면, `morph` 관계의 `morphWith` 메소드와 함께 `with` 메소드를 사용할 수 있습니다. 이 방법을 설명하기 위해 다음 모델을 고려해 보겠습니다.

    <?php

    use Illuminate\Database\Eloquent\Model;

    class ActivityFeed extends Model
    {
        /**
         * Get the parent of the activity feed record.
         */
        public function parentable()
        {
            return $this->morphTo();
        }
    }

이 예제에서 `Event`,`Photo`,`Post` 모델이 `ActivityFeed` 모델을 생성한다고 가정합시다. 또한 `Event`모델이 `Calendar`모델에 속하고, `Photo`모델이 `Tag`모델과 관련이 있고 `Post`모델이 `Author`모델에 속한다고 가정 해 보겠습니다.

이 모델 정의와 관계를 사용하여 우리는 `ActivityFeed` 모델 인스턴스를 검색 할 수 있으며 모든 `parentable`모델과 각각의 중첩 관계를 eager 로드 할 수 있습니다.

    use Illuminate\Database\Eloquent\Relations\MorphTo;

    $activities = ActivityFeed::query()
        ->with(['parentable' => function (MorphTo $morphTo) {
            $morphTo->morphWith([
                Event::class => ['calendar'],
                Photo::class => ['tags'],
                Post::class => ['author'],
            ]);
        }])->get();

#### Eager 로딩에서 컬럼 지정하기

조회하고자 하는 관계에서 항상 모든 컬럼이 필요한 것은 아닙니다. 이 경우, Eloquent 는 조회하고자 하는 관계에 컬럼을 지정할 수 있습니다.

    $books = App\Book::with('author:id,name')->get();

> {note} 이 기능을 사용할 때에는, 조회하고자 하는 컬럼에 항상 `id` 컬럼과 관련 외래 키 컬럼이 포함되어 있어야 합니다.

#### 기본적으로 Eager Loading 하기

때로는 모델을 검색 할 때 항상 일부 관계를 로드하려고 할 수 있습니다. 이를 위해 모델에 `$with` 속성을 정의 할 수 있습니다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Book extends Model
    {
        /**
         * The relationships that should always be loaded.
         *
         * @var array
         */
        protected $with = ['author'];

        /**
         * Get the author that wrote the book.
         */
        public function author()
        {
            return $this->belongsTo('App\Author');
        }
    }

단일 쿼리에 대한 `$with` 속성에서 항목을 제거하려면 `without` 메소드를 사용할 수 있습니다.

    $books = App\Book::without('author')->get();

<a name="constraining-eager-loads"></a>
### Eager 로딩에서 조건을 통해 질의 제한하기

때로는 관계를 eager 로드하면서 eager 로딩 쿼리에 추가적인 쿼리 제한들을 지정하고 싶을 수 있습니다. 다음은 그 예제입니다.

    use Illuminate\Database\Eloquent\Builder;

    $users = App\User::with(['posts' => function ($query) {
        $query->where('title', 'like', '%first%');
    }])->get();

이 예제에서 Eloquent는 게시물의 `title` 컬럼이 `first`라는 단어를 포함할 때만 게시물을 eager 로드할 것입니다. 다른 [쿼리 빌더](/docs/{{version}}/queries)메소드를 호출하여 계속하여서 eager 로딩 작업을 커스터마이즈할 수 있습니다.

    $users = App\User::with(['posts' => function ($query) {
        $query->orderBy('created_at', 'desc');
    }])->get();

> {note} eager 로드를 제한 할 때 `limit`과 `take` 쿼리 빌더 메소드는 사용할 수 없습니다.

<a name="lazy-eager-loading"></a>
### 지연 Eager 로딩

종종 부모 모델이 조회된 후에 관계를 eager 로드해야할 필요가 있을 수 있습니다. 예를 들자면, 연관된 모델들을 동적으로 결정해야할 때 유용하게 사용됩니다.

    $books = App\Book::all();

    if ($someCondition) {
        $books->load('author', 'publisher');
    }

Eager 로딩 쿼리에 추가적인 쿼리 제한을 지정해야 할 경우, `load` 메소드에 로그하고자 하는 관계에 대한 키로 구성된 배열을 전달할 수 있습니다. 이 배열의 값은 쿼리 인스턴스를 인자로 받아들이는 `Closure`이어야만 합니다.

    $author->load(['books' => function ($query) {
        $query->orderBy('published_date', 'asc');
    }]);

로딩되어 있지 않았을 때에만 관계 모델을 로딩하려면 `loadMissing` 메소드를 사용하면됩니다.

    public function format(Book $book)
    {
        $book->loadMissing('author');

        return [
            'name' => $book->name,
            'author' => $book->author->name,
        ];
    }

#### 내포된 지연 Eager 로딩 & `morphTo`

`morphTo` 관계 뿐만 아니라 해당 관계에 의해 반환될 수 있는 다양한 엔티티에서의 내포된 관계를 원하는 경우 `loadMorph` 메소드를 사용할 수 있습니다.

이 메소드는 첫번째 인자로 `morphTo` 관계를 사용하고 두번째 인자로 일련의 모델 / 짝지은 관계(relationship pairs)를 사용합니다. 이 메소드를 설명하기 위해 다음 모델을 살펴보겠습니다.

    <?php

    use Illuminate\Database\Eloquent\Model;

    class ActivityFeed extends Model
    {
        /**
         * Get the parent of the activity feed record.
         */
        public function parentable()
        {
            return $this->morphTo();
        }
    }

이 예제에서는 `Event`, `Photo` 그리고 `Post` 모델이 `ActivityFeed` 모델을 만들 수 있다고 가정해보겠습니다. 또한 `Event` 모델은 `Calendar` 모델에 속하고 `Photo` 모델은 `Tag` 모델, `Post` 모델은 `Author` 모델에 속한다고 가정해보겠습니다.

이러한 모델 정의와 관계를 사용하여 `ActivityFeed` 모델 인스턴스를 검색하고 모든 `parentable` 모델과 각각 내포된 관계를 eager 로드할 수 있습니다.

    $activities = ActivityFeed::with('parentable')
        ->get()
        ->loadMorph('parentable', [
            Event::class => ['calendar'],
            Photo::class => ['tags'],
            Post::class => ['author'],
        ]);

<a name="inserting-and-updating-related-models"></a>
## 연관된 모델 삽입하기 & 수정하기

<a name="the-save-method"></a>
### Save 메소드

Eloquent는 관계에 새로운 모델을 추가하는 편리한 메소드들을 제공합니다. 예를 들어, `Post` 모델에 새로운 `Comment`를 추가해야 할 수 있습니다. `Comment`에 수동으로 `post_id` 속성을 지정하는 대신 관계에 `save` 메소드에서 바로 `Comment`를 추가할 수 있습니다.

    $comment = new App\Comment(['message' => 'A new comment.']);

    $post = App\Post::find(1);

    $post->comments()->save($comment);

`comments` 관계를 동적 속성으로 접근하지 않았다는 점에 주목 하십시오. 대신에 `comments` 메소드를 호출하여 관계의 인스턴를 얻었습니다. `save` 메소드는 자동으로 새로운 `Comment` 모델에 적절한 `post_id` 값을 추가할 것입니다.

여러 개의 관련된 모델을 저장해야 한다면 `saveMany` 메소드를 사용하세요:

    $post = App\Post::find(1);

    $post->comments()->saveMany([
        new App\Comment(['message' => 'A new comment.']),
        new App\Comment(['message' => 'Another comment.']),
    ]);

<a name="the-push-method"></a>
#### 재귀적으로 모델 및 관계 저장

모델과 관련된 모든 관계를 `save` 하고 싶다면 `push` 메소드를 사용할 수 있습니다.

    $post = App\Post::find(1);

    $post->comments[0]->message = 'Message';
    $post->comments[0]->author->name = 'Author Name';

    $post->push();

<a name="the-create-method"></a>
### Create 메소드

`save`와 `saveMany` 메소드 외에도 속성을 배열을 받아들이고 모델을 생성하여 데이터베이스에 삽입하는 `create` 메소드를 사용하실 수 있습니다. `save`는 완전한 Eloquent 모델 인스턴스를 받아들이는 데 반해 `create`는 순수 PHP `배열`를 받는 다는 점에서 차이가 있습니다.

    $post = App\Post::find(1);

    $comment = $post->comments()->create([
        'message' => 'A new comment.',
    ]);

> {팁} `create` 메소드를 사용하기 전에 [대량 할당-mass assignment](/docs/{{version}}/eloquent#mass-assignment) 문서를 반드시 확인하시기 바랍니다.

`createMany` 메소드를 사용해서 여러 개의 관련된 모델을 만들 수 있습니다.

    $post = App\Post::find(1);

    $post->comments()->createMany([
        [
            'message' => 'A new comment.',
        ],
        [
            'message' => 'Another new comment.',
        ],
    ]);

`findOrNew`,`firstOrNew`,`firstOrCreate` 및 `updateOrCreate` 메소드를 [관계에 대한 모델 생성 및 업데이트](/docs/{{version}}/eloquent#other-creation-methods)에 사용할 수도 있습니다 .

<a name="updating-belongs-to-relationships"></a>
### Belongs To 관계

`belongsTo` 관계를 변경 할 때 `associate` 메소드를 사용할 수 있습니다. 이 메소드는 자식 모델에 외래 키를 지정합니다.

    $account = App\Account::find(10);

    $user->account()->associate($account);

    $user->save();

`belongsTo` 관계를 제거할 때는 `dissociate` 메소드를 사용하시면 됩니다. 이 메소드는 외래 키를 `null` 로 설정할 것입니다.

    $user->account()->dissociate();

    $user->save();

<a name="default-models"></a>
#### 기본 모델

`belongsTo`, `hasOne`, `hasOneThrough`, 와 `morphOne` 관계는 주어진 관계가 `null` 일 때 리턴 될 기본 모델을 정의하게합니다. 이 패턴은 [Null Object pattern](https://en.wikipedia.org/wiki/Null_Object_pattern)이라고도하며 코드에서 조건부 검사를 제거하는 데 도움이 될 수 있습니다. 다음 예제에서 `user` 관계가 작성된 글에 포함되어 있지 않으면 `user` 관계는 빈 `App\User` 모델을 반환합니다.

    /**
     * Get the author of the post.
     */
    public function user()
    {
        return $this->belongsTo('App\User')->withDefault();
    }

속성을 가진 기본 모델을 채우려면 배열이나 Closure 를 `withDefault` 메소드에 넘길 수 있습니다.

    /**
     * Get the author of the post.
     */
    public function user()
    {
        return $this->belongsTo('App\User')->withDefault([
            'name' => 'Guest Author',
        ]);
    }

    /**
     * Get the author of the post.
     */
    public function user()
    {
        return $this->belongsTo('App\User')->withDefault(function ($user, $post) {
            $user->name = 'Guest Author';
        });
    }

<a name="updating-many-to-many-relationships"></a>
### 다대다 관계

#### 추가하기 / 분리하기

Eloquent는 또한 연관된 모델들을 다루는 데 편리한 몇 개의 헬퍼 메소드를 추가로 제공합니다. 예를 들어 한 사용자가 여러 역할을 가질 수 있고 한 역할이 여러 사용자를 갖는다고 상상해봅시다. 모델들을 합치는 중간 테이블에 기록을 추가하여 사용자에게 역할을 추가하려면 `attach` 메소드를 사용하세요:

    $user = App\User::find(1);

    $user->roles()->attach($roleId);

모델에 관계를 추가할 때 중간 테이블에 삽입될 추가 데이터의 배열을 전달할 수도 있습니다.

    $user->roles()->attach($roleId, ['expires' => $expires]);

경우에 따라 사용자에게서 역할을 분리하는 것이 필요할 수 있습니다. 다대다 관계 기록을 제거하려면 `detach` 메소드를 이용하면 됩니다. `detach` 메소드는 중간 테이블에서 적절한 기록을 삭제할 것입니다. 하지만 두 모델은 모두 데이터베이스에 남을 것입니다.

    // Detach a single role from the user...
    $user->roles()->detach($roleId);

    // Detach all roles from the user...
    $user->roles()->detach();

보다 편리하게, `attach`와 `detach` ID의 배열 또한 입력으로 전달 받습니다.

    $user = App\User::find(1);

    $user->roles()->detach([1, 2, 3]);

    $user->roles()->attach([
        1 => ['expires' => $expires],
        2 => ['expires' => $expires],
    ]);

#### 연결 동기화

다대다 관계를 생성하기 위해 `sync` 메소드를 사용할 수도 있습니다. `sync` 메소드는 중간 테이블에 놓을 ID의 배열을 전달 받습니다. 주어진 배열에 포함되어 있지 않는 ID는 중간 테이블에서 제거됩니다. 따라서 이 작업이 끝나면 주어진 배열에 포함된 ID들만이 중간 테이블에 존재할 것입니다.

    $user->roles()->sync([1, 2, 3]);

ID들과 함께 중간 테이블 값을 추가로 전달할 수도 있습니다.

    $user->roles()->sync([1 => ['expires' => true], 2, 3]);

존재하는 ID들을 삭제하고 싶지 않으면, `syncWithoutDetaching` 메소드를 사용하면 됩니다.

    $user->roles()->syncWithoutDetaching([1, 2, 3]);

#### 연결 켜고 끄기(토클)

다대다 관계는 또한 주어진 ID들의 추가된 상태를 "전환"할 수 있는 `toggle` 메소드를 제공합니다. 만약 주어진 ID가 현재 추가되었다면, 이는 해제될것이고 마찬가지로 현재 추가되지 않은 상태라면 추가됩니다.

    $user->roles()->toggle([1, 2, 3]);

#### 피벗 테이블에서 추가직인 데이터 저장하기

다대다 관계를 작업할 때, `save` 메소드는 두번째 인자로 추가적인 중간 테이블의 속성에 대한 배열을 받아 들입니다.

    App\User::find(1)->roles()->save($role, ['expires' => $expires]);

#### 피벗 테이블 레코드 수정하기

피벗 테이블에 존재하는 레코드를 수정할 필요가 있다면, `updateExistingPivot` 메소드를 사용하면 됩니다. 이 메소드는 피벗 테이블의 외래 키와 수정하려는 속성에 대한 배열을 인자로 받습니다.

    $user = App\User::find(1);

    $user->roles()->updateExistingPivot($roleId, $attributes);

<a name="touching-parent-timestamps"></a>
## 부모의 타임스탬프 값 갱신

`Post`에 속하는 `Comment`와 같이 한 모델이 다른 모델에 `belongsTo`하거나 `belongsToMany`할 때, 자식 모델이 업데이트되었을 때 부모의 타임스탬프를 갱신하는 것이 유용할 수 있습니다. 예를 들어 `Comment` 모델이 업데이트되었을 때, 소유하는 `Post`의 `updated_at` 타임스탬프의 값을 자동으로 갱신하고자 할 수 있습니다. Eloquent는 이를 손쉽게 할 수 있게 해줍니다. 단순히 자식 모델에 관계의 이름들을 포함하는 `touches` 속성을 추가하면 됩니다.

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Comment extends Model
    {
        /**
         * All of the relationships to be touched.
         *
         * @var array
         */
        protected $touches = ['post'];

        /**
         * Get the post that the comment belongs to.
         */
        public function post()
        {
            return $this->belongsTo('App\Post');
        }
    }

이제 `Comment`를 업데이트할 때 소유하는 `Post`의 `updated_at` 컬럼 또한 업데이트될 것이고, 이렇게 하는 것은 `Post` 모델의 캐시가 갱신되는 편리한 방법이기도 합니다.

    $comment = App\Comment::find(1);

    $comment->text = 'Edit to this comment!';

    $comment->save();
