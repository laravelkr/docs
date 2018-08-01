# Eloquent: Relationships
# Eloquent: Relationships - 관계

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Defining Relationships](#defining-relationships)
- [관계 정의하기](#defining-relationships)
    - [One To One](#one-to-one)
    - [1:1(일대일) 관계](#one-to-one)
    - [One To Many](#one-to-many)
    - [1:*(일대다) 관계](#one-to-many)
    - [One To Many (Inverse)](#one-to-many-inverse)
    - [1:*(일대다) 역관계](#one-to-many)
    - [Many To Many](#many-to-many)
    - [\*:*(다대다) 관계](#many-to-many)
    - [Has Many Through](#has-many-through)
    - [연결을 통한 다수를 가지는 관계](#has-many-through)
    - [Polymorphic Relations](#polymorphic-relations)
    - [다형성 관계](#polymorphic-relations)
    - [Many To Many Polymorphic Relations](#many-to-many-polymorphic-relations)
    - [다대다 다형성 관계](#many-to-many-polymorphic-relations)
- [Querying Relations](#querying-relations)
- [관계 쿼리 질의하기](#querying-relations)
    - [Relationship Methods Vs. Dynamic Properties](#relationship-methods-vs-dynamic-properties)
    - [관계 메소드 Vs. 동적 속성](#relationship-methods-vs-dynamic-properties)
    - [Querying Relationship Existence](#querying-relationship-existence)
    - [존재하는 관계에 대한 쿼리 질의하기](#querying-relationship-existence)
    - [Querying Relationship Absence](#querying-relationship-absence)
    - [관계된 모델이 존재하지 않는 것을 확인하며 질의하기](#querying-relationship-absence)
    - [Counting Related Models](#counting-related-models)
    - [연관된 모델 수량 확인하기-카운트](#counting-related-models)
- [Eager Loading](#eager-loading)
- [Eager 로딩](#eager-loading)
    - [Constraining Eager Loads](#constraining-eager-loads)
    - [Eager 로딩 조건 제한하기](#constraining-eager-loads)
    - [Lazy Eager Loading](#lazy-eager-loading)
    - [지연 Eager 로딩](#lazy-eager-loading)
- [Inserting & Updating Related Models](#inserting-and-updating-related-models)
- [연관된 모델 삽입하기](#inserting-and-updating-related-models)
    - [The `save` Method](#the-save-method)
    - [`save` 메소드](#the-save-method)
    - [The `create` Method](#the-create-method)
    - [`create` 메소드](#the-create-method)
    - [Belongs To Relationships](#updating-belongs-to-relationships)
    - [소속된 관계](#updating-belongs-to-relationships)
    - [Many To Many Relationships](#updating-many-to-many-relationships)
    - [다대다 관계](#updating-many-to-many-relationships)
- [Touching Parent Timestamps](#touching-parent-timestamps)
- [부모의 타임스탬프 값 갱신하기](#touching-parent-timestamps)

<a name="introduction"></a>
## Introduction
## 시작하기

Database tables are often related to one another. For example, a blog post may have many comments, or an order could be related to the user who placed it. Eloquent makes managing and working with these relationships easy, and supports several different types of relationships:

데이터베이스 테이블은 주로 서로 관련되어 있습니다. 예를 들어, 한 블로그 포스트가 많은 댓글을 가지고 있거나 어떤 명령이 그 명령을 내린 사용자와 관련되어 있을 수 있습니다. Eloquent는 이 relationship들과 관련한 작업을 하거나 관리하는 것을 쉽게 해주며 여러 타입의 relationship을 지원합니다:

- [One To One](#one-to-one)
- [1:1(일대일) 관계](#one-to-one)
- [One To Many](#one-to-many)
- [1:*(일대다) 관계](#one-to-many)
- [Many To Many](#many-to-many)
- [*:*(대다다) 관계](#many-to-many)
- [Has Many Through](#has-many-through)
- [연결을 통한 다수를 가지는 관계](#has-many-through)
- [Polymorphic Relations](#polymorphic-relations)
- [다형성 관계](#polymorphic-relations)
- [Many To Many Polymorphic Relations](#many-to-many-polymorphic-relations)
- [다대다 다형성 관계](#many-to-many-polymorphic-relations)

<a name="defining-relationships"></a>
## Defining Relationships
## 관계 정의하기

Eloquent relationships are defined as methods on your Eloquent model classes. Since, like Eloquent models themselves, relationships also serve as powerful [query builders](/docs/{{version}}/queries), defining relationships as methods provides powerful method chaining and querying capabilities. For example, we may chain additional constraints on this `posts` relationship:

Eloquent relationship들은 Eloquent 모델 클래스에 메소드로 정의되어 있습니다. Eloquent 모델들과 같이 relationships-관계를 정의한 것은 강력한 [쿼리 빌더](/docs/{{version}}/queries)로써의 기능으로도 작동하기 때문에 relationships-관계를 메소드로 정의하는 것은 강력한 메소드 체이닝과 쿼리 능력을 제공하게됩니다. 예를 들어 다음의 `post` 관계에 대해서 추가적인 제약조건을 체이닝할 수도 있습니다.:

    $user->posts()->where('active', 1)->get();

But, before diving too deep into using relationships, let's learn how to define each type.

relationship을 사용하는 법에 더 깊이 알아보기 전에, 각 타입을 어떻게 정의햐는지 알아보도록 합시다.

<a name="one-to-one"></a>
### One To One
### 1:1(일대일) 관계 정의하기

A one-to-one relationship is a very basic relation. For example, a `User` model might be associated with one `Phone`. To define this relationship, we place a `phone` method on the `User` model. The `phone` method should call the `hasOne` method and return its result:

일대일 relationship은 아주 기본적인 관계입니다. 예를 들어, 하나의 `User` 모델이 하나의 `Phone`과 관계되어 있을 수 있습니다. 이 relationship을 정의하기 위해 `User` 모델에 `phone` 메소드를 구성합니다. `phone` 메소드는 `hasOne` 메소드를 호출하게 되고 그 결과를 반환할 것입니다:

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

The first argument passed to the `hasOne` method is the name of the related model. Once the relationship is defined, we may retrieve the related record using Eloquent's dynamic properties. Dynamic properties allow you to access relationship methods as if they were properties defined on the model:

`hasOne` 메소드에 전달되는 첫번째 인자는 관련된 모델의 이름입니다. Relationship이 정의되었다면 Eloquent의 동적 속성을 이용하여 관련된 기록을 찾을 수 있습니다. 동적 속성은 모델에 정의된 속성에 접근하는 방식과 같은 방식으로 relationship 메소드에 접근하는 것을 허용합니다:

    $phone = User::find(1)->phone;

Eloquent determines the foreign key of the relationship based on the model name. In this case, the `Phone` model is automatically assumed to have a `user_id` foreign key. If you wish to override this convention, you may pass a second argument to the `hasOne` method:

Eloquent는 모델 명에 근거하여 relationship의 외래 키(foreign key)를 결정합니다. 이 경우, `Phone` 모델은 `user_id` 외래 키를 가질 것이라고 자동으로 추정합니다. 이 추정사항을 재정의하고 싶다면 `hasOne` 메소드로 두번째 인자를 전달하면 됩니다:

    return $this->hasOne('App\Phone', 'foreign_key');

Additionally, Eloquent assumes that the foreign key should have a value matching the `id` (or the custom `$primaryKey`) column of the parent. In other words, Eloquent will look for the value of the user's `id` column in the `user_id` column of the `Phone` record. If you would like the relationship to use a value other than `id`, you may pass a third argument to the `hasOne` method specifying your custom key:

Eloquent는 또한 외래 키가 부모의 `id` 컬럼(또는 사용자가 정의한 `$primaryKey`)에 상응하는 값을 가지고 있다고 추정합니다. 즉, Eloquent는 `Phone`레코드의 `user_id` 컬럼에서 사용자 `id` 컬럼의 값을 찾을 것입니다. Relationship이 `id`가 아닌 다른 값을 사용하고자 한다면 커스텀 키를 지정하는 세번째 인자를 `hasOne` 메소드로 전달하면 됩니다:

    return $this->hasOne('App\Phone', 'foreign_key', 'local_key');

#### Defining The Inverse Of The Relationship
#### 관계의 역(반대) 정의하기

So, we can access the `Phone` model from our `User`. Now, let's define a relationship on the `Phone` model that will let us access the `User` that owns the phone. We can define the inverse of a `hasOne` relationship using the `belongsTo` method:

이제 `User`에서 `Phone` 모델에 접근할 수 있습니다. 반대로, `Phone` 모델에 relationship을 정의하여 phone을 소유하는 `User`에 접근할 수 있도록 해봅시다. `belongsTo` 메소드를 이용하면 `hasOne` relationship의 반대를 정의할 수 있습니다:

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

In the example above, Eloquent will try to match the `user_id` from the `Phone` model to an `id` on the `User` model. Eloquent determines the default foreign key name by examining the name of the relationship method and suffixing the method name with `_id`. However, if the foreign key on the `Phone` model is not `user_id`, you may pass a custom key name as the second argument to the `belongsTo` method:

위 예에서 Eloquent는 `Phone` 모델의 `user_id`와 `User` 모델의 `id`를 비교해볼 것입니다. Eloquent는 relationship 메소드의 이름을 검사하고 메소드 이름에 `_id`를 붙여서 외래 키의 기본 이름으로 결정합니다. 하지만 `Phone` 모델의 외래 키가 `user_id`가 아니라면 커스텀 키 이름을 두번째 인자로 `belongsTo`에 전달하면 됩니다:

    /**
     * Get the user that owns the phone.
     */
    public function user()
    {
        return $this->belongsTo('App\User', 'foreign_key');
    }

If your parent model does not use `id` as its primary key, or you wish to join the child model to a different column, you may pass a third argument to the `belongsTo` method specifying your parent table's custom key:

부모 모델이 `id`를 프라이머리 키로 사용하지 않거나 자식 모델을 다른 컬럼에 조인시키고 싶다면 부모 테이블의 커스텀 키를 지정하는 세번째 인자를 `belongsTo` 메소드로 전달하면 됩니다:

    /**
     * Get the user that owns the phone.
     */
    public function user()
    {
        return $this->belongsTo('App\User', 'foreign_key', 'other_key');
    }

<a name="default-models"></a>
#### Default Models
#### 기본 모델

The `belongsTo` relationship allows you to define a default model that will be returned if the given relationship is `null`. This pattern is often referred to as the [Null Object pattern](https://en.wikipedia.org/wiki/Null_Object_pattern) and can help remove conditional checks in your code. In the following example, the `user` relation will return an empty `App\User` model if no `user` is attached to the post:

`belongsTo` 관계에서 주어진 관계가 만약 `null` 인 경우에 반환할 기본모델을 정의할 수 있습니다. 이 패턴은 [Null 오브젝트 패턴](https://en.wikipedia.org/wiki/Null_Object_pattern) 이라고 하며 코드에서 조건식을 제거하는데 도움이 됩니다. 다음의 예제에서 `user` 관계는 포스트에 추가된 `user` 가 없는 경우 비어 있는 `App/User` 모델을 반환합니다:

    /**
     * Get the author of the post.
     */
    public function user()
    {
        return $this->belongsTo('App\User')->withDefault();
    }

To populate the default model with attributes, you may pass an array or Closure to the `withDefault` method:

기본 모델에 속성을 구성하려면, `withDefault` 메소드에 배열 또는 클로저를 전달하면 됩니다:

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
        return $this->belongsTo('App\User')->withDefault(function ($user) {
            $user->name = 'Guest Author';
        });
    }

<a name="one-to-many"></a>
### One To Many
### 1:*(일대다) 관계 정의하기

A "one-to-many" relationship is used to define relationships where a single model owns any amount of other models. For example, a blog post may have an infinite number of comments. Like all other Eloquent relationships, one-to-many relationships are defined by placing a function on your Eloquent model:

"일대다" relationship은 하나의 모델이 다른 모델의 어떤 부분이라도 소유할 경우의 relationship을 정의하는데 사용됩니다. 예를 들어, 한 블로그 게시물이 댓글을 무제한으로 가질 수 있습니다. 다른 모든 Eloquent relationship들과 같이, 일대다 relationship들은 Eloquent 모델에 함수를 통해서 정의됩니다:

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

Remember, Eloquent will automatically determine the proper foreign key column on the `Comment` model. By convention, Eloquent will take the "snake case" name of the owning model and suffix it with `_id`. So, for this example, Eloquent will assume the foreign key on the `Comment` model is `post_id`.

앞서 언급했듯이 Eloquent는 `Comment` 모델에 적절한 외래 키를 자동으로 결정합니다. Eloquent는 관례적으로 소유하는 모델의 "snake case" 이름에 `_id`를 붙일 것입니다. 따라서 이 예에서 Eloqent는 `Comment` 모델의 외래 키가 `post_id`일 것이라고 추정합니다.

Once the relationship has been defined, we can access the collection of comments by accessing the `comments` property. Remember, since Eloquent provides "dynamic properties", we can access relationship methods as if they were defined as properties on the model:

관계가 정의되었다면 `comments` 속성에 접근하여 댓글 모음에 엑세스할 수 있습니다. 앞서 말했듯이 Eloquent는 "동적 속성"을 제공하기 때문에 모델의 속성에 접근하듯이 relationship-관계 메소드에 접근할 수 있습니다:

    $comments = App\Post::find(1)->comments;

    foreach ($comments as $comment) {
        //
    }

Of course, since all relationships also serve as query builders, you can add further constraints to which comments are retrieved by calling the `comments` method and continuing to chain conditions onto the query:

물론 모든 관계들은 쿼리 빌더로도 역할하기 때문에 `comments` 메소드를 호출하고 쿼리에 조건들을 계속해서 체인하는 것을 통해 어떤 댓글들이 조회되는지에 대한 제한들을 추가할 수 있습니다:

    $comment = App\Post::find(1)->comments()->where('title', 'foo')->first();

Like the `hasOne` method, you may also override the foreign and local keys by passing additional arguments to the `hasMany` method:

`hasOne` 메소드와 같이 `hasMany` 메소드에 추가적인 인자들을 전달하여 외래 및 로컬 키들을 재지정 할 수 있습니다:

    return $this->hasMany('App\Comment', 'foreign_key');

    return $this->hasMany('App\Comment', 'foreign_key', 'local_key');

<a name="one-to-many-inverse"></a>
### One To Many (Inverse)
### 1:*(일대다) 역관계

Now that we can access all of a post's comments, let's define a relationship to allow a comment to access its parent post. To define the inverse of a `hasMany` relationship, define a relationship function on the child model which calls the `belongsTo` method:

이제 게시물의 모든 댓글에 접근할 수 있으니 댓글이 부모 게시물에 접근할 수 있도록 해주는 관계를 정의해 봅시다. `hasMany` 관계의 반대를 정의하려면 `belongsTo` 메소드를 호출하는 자식 모델에 관계 함수를 정의하면 됩니다:

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

Once the relationship has been defined, we can retrieve the `Post` model for a `Comment` by accessing the `post` "dynamic property":

관계가 정의되었다면 `post` "동적 속성"에 접근하여서 `Comment`에 대한 `Post` 모델을 찾을 수 있습니다:

    $comment = App\Comment::find(1);

    echo $comment->post->title;

In the example above, Eloquent will try to match the `post_id` from the `Comment` model to an `id` on the `Post` model. Eloquent determines the default foreign key name by examining the name of the relationship method and suffixing the method name with `_id`. However, if the foreign key on the `Comment` model is not `post_id`, you may pass a custom key name as the second argument to the `belongsTo` method:

위의 예에서 Eloqent는 `Comment` 모델의 `post_id`와 `Post` 모델의 `id`를 비교해볼 것입니다. Eloquent는 relationship 메소드의 이름을 검사하고 메소드 이름에 `_id`를 붙여서 외래 키의 기본 이름을 결정합니다. 하지만 `Comment` 모델의 외래 키가 `post_id`가 아니라면 커스텀 키 이름을 두번째 인자로 `belongsTo`에 전달하면 됩니다:

    /**
     * Get the post that owns the comment.
     */
    public function post()
    {
        return $this->belongsTo('App\Post', 'foreign_key');
    }

If your parent model does not use `id` as its primary key, or you wish to join the child model to a different column, you may pass a third argument to the `belongsTo` method specifying your parent table's custom key:

부모 모델이 `id`를 프라이머리 키로 사용하지 않거나 자식 모델을 다른 컬럼에 조인시키고 싶으면 부모 테이블의 커스텀 키를 지정하는 세번째 인자를 `belongsTo` 메소드로 전달하면 됩니다:

    /**
     * Get the post that owns the comment.
     */
    public function post()
    {
        return $this->belongsTo('App\Post', 'foreign_key', 'other_key');
    }

<a name="many-to-many"></a>
### Many To Many
### \*:* (다대다) 관계 정의하기

Many-to-many relations are slightly more complicated than `hasOne` and `hasMany` relationships. An example of such a relationship is a user with many roles, where the roles are also shared by other users. For example, many users may have the role of "Admin". To define this relationship, three database tables are needed: `users`, `roles`, and `role_user`. The `role_user` table is derived from the alphabetical order of the related model names, and contains the `user_id` and `role_id` columns.

다대다 관계는 `hasOne`과 `hasMany` 관계들에 비해서 조금 더 복잡합니다. 이런 관계의 예로 사용자가 여러 역할을 가지면서 그 역할들이 다른 사용자와 공유되는 경우가 있습니다. 예를 들어 여러 사용자들이 "Admin" 역할을 할 수 있습니다. 이 관계를 정의하기 위해는 `users`, `roles`, 그리고 `role_user`의 3개의 데이터베이스 테이블이 필요합니다. `role_user` 테이블은 관련된 모델 이름의 알파펫 순으로부터 정렬되며 `user_id`와 `role_id` 컬럼을 가지고 있습니다.

Many-to-many relationships are defined by writing a method that returns the result of the `belongsToMany` method. For example, let's define the `roles` method on our `User` model:

다대다 관계는 `belongsToMany` 메소드의 결과를 반환하는 메소드를 작성하여 정의합니다. 예를 들어 `User` 모델에 `roles` 메소드를 정의해보도록 하겠습니다:

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

Once the relationship is defined, you may access the user's roles using the `roles` dynamic property:

관계가 정의되었다면 `roles` 동적 속성을 사용하여 사용자들의 역할에 접근할 수 있습니다:

    $user = App\User::find(1);

    foreach ($user->roles as $role) {
        //
    }

Of course, like all other relationship types, you may call the `roles` method to continue chaining query constraints onto the relationship:

물론 다른 모든 관계 타입과 같이 `roles` 메소드를 호출하여 관계에 대해서 쿼리 제한 조건들을 계속하여 체이닝 할 수 있습니다:

    $roles = App\User::find(1)->roles()->orderBy('name')->get();

As mentioned previously, to determine the table name of the relationship's joining table, Eloquent will join the two related model names in alphabetical order. However, you are free to override this convention. You may do so by passing a second argument to the `belongsToMany` method:

앞서 살펴본 바와같이, Eloquent는 relationship-관계의 조인(join) 테이블의 테이블 명을 결정하기 위해 관련된 두 모델 이름을 알파벳 순으로 합칠 것입니다. (user 와 role 일 경우에 role_user 로 이름을 추정합니다) 하지만 이러한 관례는 재지정 할 수 있습니다. `belongsToMany` 메소드로 두번째 인자를 전달하면 됩니다:

    return $this->belongsToMany('App\Role', 'role_user');

In addition to customizing the name of the joining table, you may also customize the column names of the keys on the table by passing additional arguments to the `belongsToMany` method. The third argument is the foreign key name of the model on which you are defining the relationship, while the fourth argument is the foreign key name of the model that you are joining to:

join 테이블의 이름을 커스터마이징하는 것 외에도 `belongsToMany` 메소드에 추가 인자들을 전달하면 키들의 이름들 또한 커스터마이징 할 수 있습니다. 세번째 인자는 관계를 정의하는 모델의 외래 키 이름이고 네번째 인자는 join 하는 모델의 외래 키 이름입니다:

    return $this->belongsToMany('App\Role', 'role_user', 'user_id', 'role_id');

#### Defining The Inverse Of The Relationship
#### 관계의 역(반대) 정의하기

To define the inverse of a many-to-many relationship, you place another call to `belongsToMany` on your related model. To continue our user roles example, let's define the `users` method on the `Role` model:

다대다 관계의 반대를 정의하려면 관련된 모델에 `belongsToMany`를 호출합니다. 사용자와 역할에 대한 예를 계속 들어 생각해서, `Role` 모델에 `users` 메소드를 정의해봅시다:

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

As you can see, the relationship is defined exactly the same as its `User` counterpart, with the exception of referencing the `App\User` model. Since we're reusing the `belongsToMany` method, all of the usual table and key customization options are available when defining the inverse of many-to-many relationships.

위에서 볼 수 있듯이 `App\User` 모델을 참고하는 것만 제외하고 relationship-관계는 대응하는 `User`와 정확히 동일하게 정의되어 있습니다. `belongsToMany` 메소드를 재사용하고 있기 때문에 다대다 관계의 역-반대를 정의할 때에는 테이블과 키의 모든 통상적인 커스터마이즈 옵션이 사용 가능합니다.

#### Retrieving Intermediate Table Columns
#### 중간 테이블 컬럼 조회하기

As you have already learned, working with many-to-many relations requires the presence of an intermediate table. Eloquent provides some very helpful ways of interacting with this table. For example, let's assume our `User` object has many `Role` objects that it is related to. After accessing this relationship, we may access the intermediate table using the `pivot` attribute on the models:

앞서 알아 보았듯이, 다대다 관계는 중간 테이블의 존재를 필요로 합니다. Eloquent는 이 테이블과 상호 작용할 수 있게 도움을 주는 몇몇 방법들을 제공합니다. 예를 들어, `User` 객체가 여러 `Role` 객체에 관련되어 있다고 생각해 봅시다. 이 관계에 접근한 후 모델들에 `pivot` 속성을 사용하여 중간 테이블에 접근할 수 있습니다:

    $user = App\User::find(1);

    foreach ($user->roles as $role) {
        echo $role->pivot->created_at;
    }

Notice that each `Role` model we retrieve is automatically assigned a `pivot` attribute. This attribute contains a model representing the intermediate table, and may be used like any other Eloquent model.

조회되는 각 `Role` 모델은 자동으로 `pivot` 속성을 부여받습니다. 이 속성은 중간 테이블을 나타내는 모델을 포함하고 있으며 여느 Eloquent 모델과 다르지 않게 사용될 수 있습니다.

By default, only the model keys will be present on the `pivot` object. If your pivot table contains extra attributes, you must specify them when defining the relationship:

기본적으로 `pivot` 객체에는 모델 키만 존재할 것입니다. Pivot 테이블이 추가 속성들을 포함한다면 관계를 정의할 때 명시해야 합니다:

    return $this->belongsToMany('App\Role')->withPivot('column1', 'column2');

If you want your pivot table to have automatically maintained `created_at` and `updated_at` timestamps, use the `withTimestamps` method on the relationship definition:

Pivot 테이블이 자동으로 유지되는 `created_at`와 `updated_at` 타임스탬프를 갖기 원한다면 관계 정의에 `withTimestamps` 메소드를 사용하면 됩니다:

    return $this->belongsToMany('App\Role')->withTimestamps();

#### Customizing The `pivot` Attribute Name
#### `pivot` 속성의 이름 커스터마이징 하기

As noted earlier, attributes from the intermediate table may be accessed on models using the `pivot` attribute. However, you are free to customize the name of this attribute to better reflect its purpose within your application.

앞서 이야기 한것처럼, 모델에서 `pivot` 속성을 사용하여 중간 테이블의 속성에 엑세스 할 수 있습니다. 그렇지만 애플리케이션에서 용도를 보다 명확하게 표현할 수 있도록 속성의 이름을 자유롭게 커스터마이징 할 수 있습니다.

For example, if your application contains users that may subscribe to podcasts, you probably have a many-to-many relationship between users and podcasts. If this is the case, you may wish to rename your intermediate table accessor to `subscription` instead of `pivot`. This can be done using the `as` method when defining the relationship:

예를 들어, 애플리케이션에서 팟캐스트를 등록할 수 있는 사용자를 가지는 경우, 사용자와 팟캐스트는 다대다 관계를 형성할 수 있습니다. 이 경우 중간 테이블에 엑세스 하는 `pivot` 대신에 `subscription` 으로 이름을 변경할 수 있습니다. 이는 관계를 정의 할 때 `as` 메소드를 사용하여 지정하면 됩니다:

    return $this->belongsToMany('App\Podcast')
                    ->as('subscription')
                    ->withTimestamps();

Once this is done, you may access the intermediate table data using the customized name:

이 후에는, 다음과 같이 커스터마이징한 이름을 사용하여 중간 테이블에 엑세스 할 수 있습니다:

    $users = User::with('podcasts')->get();

    foreach ($users->flatMap->podcasts as $podcast) {
        echo $podcast->subscription->created_at;
    }

#### Filtering Relationships Via Intermediate Table Columns
#### 중간 테이블의 컬럼을 사용한 관계의 필터링

You can also filter the results returned by `belongsToMany` using the `wherePivot` and `wherePivotIn` methods when defining the relationship:

관계를 정의할 때, `wherePivot` 과 `wherePivotIn` 메소드를 사용하여 `belongsToMany`이 반환하는 결과를 필터링 할 수도 있습니다.

    return $this->belongsToMany('App\Role')->wherePivot('approved', 1);

    return $this->belongsToMany('App\Role')->wherePivotIn('priority', [1, 2]);

#### Defining Custom Intermediate Table Models
#### 커스텀 중간 테이블 모델 정의하기

If you would like to define a custom model to represent the intermediate table of your relationship, you may call the `using` method when defining the relationship. All custom models used to represent intermediate tables of relationships must extend the `Illuminate\Database\Eloquent\Relations\Pivot` class. For example, we may define a `Role` which uses a custom `UserRole` pivot model:

관계의 중간 테이블을 표현하기 위해서 커스텀 모델을 정의하려면, 관계를 정의할 때 `using` 메소드를 호출하면 됩니다. 관계의 중간 테이블을 나타내는 데 사용되는 모든 커스텀 모델은 `Illuminate\Database\Eloquent\Relations\Pivot` 클래스를 상속해야합니다. 예를 들어, 커스텀 `UserRole` 피벗 모델을 사용하는 `Role` 을 정의할 수 있습니다.

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
            return $this->belongsToMany('App\User')->using('App\UserRole');
        }
    }

When defining the `UserRole` model, we will extend the `Pivot` class:

`UserRole` 모델을 정의할 때에는 `Pivot` 클래스를 상속합니다:

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Relations\Pivot;

    class UserRole extends Pivot
    {
        //
    }

<a name="has-many-through"></a>
### Has Many Through
### 연결을 통한 다수를 가지는 관계 정의하기

The "has-many-through" relationship provides a convenient shortcut for accessing distant relations via an intermediate relation. For example, a `Country` model might have many `Post` models through an intermediate `User` model. In this example, you could easily gather all blog posts for a given country. Let's look at the tables required to define this relationship:

"연결을 통한 다수를 가지는" 관계는 중간 테이블을 통해서, 서로 떨어진 관계들에 접근하는 편리한 방법을 제공합니다. 예를 들어, `Country` 모델은 중간 `User` 모델을 통해 다수의 `Post` 모델을 가지고 있을 수 있습니다. 이 예제에서는 특정 국가에 대한 모든 블로그 게시물을 쉽게 확인할 수 있습니다. 이 관계를 정의하기 위해 요구되는 테이블들을 살펴보겠습니다:

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

Though `posts` does not contain a `country_id` column, the `hasManyThrough` relation provides access to a country's posts via `$country->posts`. To perform this query, Eloquent inspects the `country_id` on the intermediate `users` table. After finding the matching user IDs, they are used to query the `posts` table.

`posts`는 `country_id` 컬럼을 포함하고 있지 않지만 `hasManyThrough` 관계는 `$country->posts`를 통해 컨트리에 대한 접근을 제공합니다. 이 쿼리를 수행하기 위해 Eloquent는 중간 `users` 테이블의 `country_id`를 검사합니다. 일치하는 사용자 ID를 찾은 후에는 `posts` 테이블을 쿼리하는데 사용합니다.

Now that we have examined the table structure for the relationship, let's define it on the `Country` model:

relationship-관계를 위한 테이블 구조를 살펴보았으니 이제 `Country` 모델에 정의해보도록 합시다:

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

The first argument passed to the `hasManyThrough` method is the name of the final model we wish to access, while the second argument is the name of the intermediate model.

`hasManyThrough` 메소드로 전달되는 첫번째 인자는 접근하고자 하는 최종 모델의 이름이며 두번째 인자는 중간 모델의 이름입니다.

Typical Eloquent foreign key conventions will be used when performing the relationship's queries. If you would like to customize the keys of the relationship, you may pass them as the third and fourth arguments to the `hasManyThrough` method. The third argument is the name of the foreign key on the intermediate model. The fourth argument is the name of the foreign key on the final model. The fifth argument is the local key, while the sixth argument is the local key of the intermediate model:

관계의 쿼리를 수행할 때 전형적인 Eloquent 외래 키 관례들이 사용될 것입니다. 관계의 키를 사용자가 정의하고 싶다면 세번째와 네번재 인자로 `hasManyThrough` 메소드에 전달할 수 있습니다. 세번째 인자는 중간 모델의 외래 키 이름입니다. 네번째 인자는 최종 모델의 외래 키 이름입니다. 다섯번째 키는 로컬 키이고, 여섯번째 키는 중간 모델 로컬 키입니다:

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

<a name="polymorphic-relations"></a>
### Polymorphic Relations
### 다형성 관계

#### Table Structure
#### 테이블 구조

Polymorphic relations allow a model to belong to more than one other model on a single association. For example, imagine users of your application can "comment" on both posts and videos. Using polymorphic relationships, you can use a single `comments` table for both of these scenarios. First, let's examine the table structure required to build this relationship:

다형성 관계는 모델이 하나의 연관관계에 대해서 하나 이상의 모델에 소속될 수 있도록 해줍니다. 예를 들어 애플리케이션의 사용자가 게시글과 비디오 둘다 "댓글"를 달 수 있다고 생각해 보겠습니다. 다형성 관계를 이용하면 이 두 시나리오 모두 지원하는 하나의 `comments` 테이블을 사용할 수 있습니다. 먼저 이 관계를 구성하기 위해 필요한 테이블 구조를 살펴보겠습니다:

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

Two important columns to note are the `commentable_id` and `commentable_type` columns on the `comments` table. The `commentable_id` column will contain the ID value of the post or video, while the `commentable_type` column will contain the class name of the owning model. The `commentable_type` column is how the ORM determines which "type" of owning model to return when accessing the `commentable` relation.

유심히 살펴봐야할 중요한 두개의 컬럼은 `comments` 테이블의 `commentable_id`와 `commentable_type` 컬럼입니다. `commentable_id` 컬럼은 게시글과 비디오의 ID 값을 가지고, `commentable_type` 컬럼은 소유 모델의 클래스 이름을 가집니다. `commentable_type` 컬럼은 `likeable` 관계에 접근할 때 어떤 "유형"의 소유 모델을 반환할지 ORM이 결정하는 방법입니다.

#### Model Structure
#### 모델 구조

Next, let's examine the model definitions needed to build this relationship:

다음으로 이 관계를 형성하기 위해 필요한 모델 정의들을 살펴보도록 하겠습니다:

    <?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Comment extends Model
    {
        /**
         * Get all of the owning commentable models.
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

#### Retrieving Polymorphic Relations
#### 다형성 관계 조회하기

Once your database table and models are defined, you may access the relationships via your models. For example, to access all of the comments for a post, we can use the `comments` dynamic property:

데이터베이스 테이블과 모델이 정의되었다면 모델들을 통해 관계들에 접근할 수 있습니다. 예를 들어, 게시글의 모든 댓글에 접근하기 위해서, `comments` 동적 속성을 사용할 수 있습니다:

    $post = App\Post::find(1);

    foreach ($post->comments as $comment) {
        //
    }

You may also retrieve the owner of a polymorphic relation from the polymorphic model by accessing the name of the method that performs the call to `morphTo`. In our case, that is the `commentable` method on the `Comment` model. So, we will access that method as a dynamic property:

또한 `morphTo`의 호출을 수행하는 메소드의 이름에 접근하여 다형성 모델에서 다형성 관계의 소유자를 조회할 수 있습니다. 이 경우, `Comment` 모델에 `commentable` 메소드를 사용합니다. 따라서 이 메소드를 동적 속성으로 접근할 것입니다:

    $comment = App\Comment::find(1);

    $commentable = $comment->commentable;

The `commentable` relation on the `Comment` model will return either a `Post` or `Video` instance, depending on which type of model owns the comment.

`Comment` 모델의 `commentabl` 관계는 댓글을 어느 모델이 소유하느냐에 따라 `Post`나 `Video` 인스턴스를 반환합니다.

#### Custom Polymorphic Types
#### 사용자 정의 다형성 타입

By default, Laravel will use the fully qualified class name to store the type of the related model. For instance, given the example above where a `Comment` may belong to a `Post` or a `Video`, the default `commentable_type` would be either `App\Post` or `App\Video`, respectively. However, you may wish to decouple your database from your application's internal structure. In that case, you may define a relationship "morph map" to instruct Eloquent to use a custom name for each model instead of the class name:

기본적으로, 라라벨은 관련된 모델의 유형을 저장하기 위해서 전체 클래스 이름을 사용합니다. 예를 들어 위의 예제에서 `Comment` 는 하나의 `Post` 또는 하나의 `Video` 에 지정되고, 기본적으로 `commentable_type` 의 각각 `App\Post` 또는 `App\Video` 이 될 수 있습니다. 그렇지만, 여러분은 데이터베이스와 애플리케이션의 내부 구조를 분리하고자 할 수 있습니다. 이 경우 여러분은 관계 설정을 위한 "morph map"을 정의하고 클래스 이름 대신 사용할 각각의 모델과 관련 있는 고유한 이름을 Eloquent 에 지시할 수 있습니다:

    use Illuminate\Database\Eloquent\Relations\Relation;

    Relation::morphMap([
        'posts' => 'App\Post',
        'videos' => 'App\Video',
    ]);

You may register the `morphMap` in the `boot` function of your `AppServiceProvider` or create a separate service provider if you wish.

여러분은 `AppServiceProvider` 의 `boot` 안에서 `morphMap` 를 등록 하거나, 원한다면 분리된 서비스 프로바이더를 만들 수도 있을 것입니다.

<a name="many-to-many-polymorphic-relations"></a>
### Many To Many Polymorphic Relations
### 다대다 다형성 관계 정의하기

#### Table Structure
#### 테이블 구조

In addition to traditional polymorphic relations, you may also define "many-to-many" polymorphic relations. For example, a blog `Post` and `Video` model could share a polymorphic relation to a `Tag` model. Using a many-to-many polymorphic relation allows you to have a single list of unique tags that are shared across blog posts and videos. First, let's examine the table structure:

일반적인 다형성 관계 말고도 "다대다" 다형성 관계 또한 정의할 수 있습니다. 예를 들어, 블로그 `Post`와 `Video` 모델은 `Tag` 모델과 다형성 관계를 공유할 수 있습니다. 다대다 다형성 관계를 사용하면 블로그 게시물과 비디오를 아울러 공유되는 고유의 태그를 하나의 목록으로 만들어줍니다. 우선 테이블 구조를 살펴보겠습니다:

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

#### Model Structure
#### 모델 구조

Next, we're ready to define the relationships on the model. The `Post` and `Video` models will both have a `tags` method that calls the `morphToMany` method on the base Eloquent class:

이제 모델에 관계를 정의할 준비가 되었습니다. `Post`와 `Video` 모델은 둘 다 Eloquent 클래스에 `morphToMany` 메소드를 호출하는 `tags` 메소드를 가집니다:

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

#### Defining The Inverse Of The Relationship
#### 역관계 정의하기

Next, on the `Tag` model, you should define a method for each of its related models. So, for this example, we will define a `posts` method and a `videos` method:

다음, `Tag` 모델에서 관련된 각 모델들을 위해 메소드를 정의해야 합니다. 이 예제에서는 `posts` 메소드와 `videos` 메소드를 정의하겠습니다:

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

#### Retrieving The Relationship
#### 관계 조회하기

Once your database table and models are defined, you may access the relationships via your models. For example, to access all of the tags for a post, you can use the `tags` dynamic property:

데이터베이스 테이블과 모델들이 정의되었다면 모델을 통해 관계에 접근할 수 있습니다. 예를 들어, 어떤 게시물의 모든 태그에 접근하려면, `tags` 동적 속성을 사용하면 됩니다:

    $post = App\Post::find(1);

    foreach ($post->tags as $tag) {
        //
    }

You may also retrieve the owner of a polymorphic relation from the polymorphic model by accessing the name of the method that performs the call to `morphedByMany`. In our case, that is the `posts` or `videos` methods on the `Tag` model. So, you will access those methods as dynamic properties:

또한 `morphedByMany`의 호출을 수행하는 메소드의 이름에 접근하여 다형성 모델에서 다형성 관계의 소유자를 조회할 수 있습니다. 이 예에서는 `Tag` 모델에 사용되는`posts`나 `videos` 메소드를 말합니다. 따라서 이 메소드들은 동적 속성으로 접근할 것입니다:

    $tag = App\Tag::find(1);

    foreach ($tag->videos as $video) {
        //
    }

<a name="querying-relations"></a>
## Querying Relations
## 관계 쿼리 질의하기

Since all types of Eloquent relationships are defined via methods, you may call those methods to obtain an instance of the relationship without actually executing the relationship queries. In addition, all types of Eloquent relationships also serve as [query builders](/docs/{{version}}/queries), allowing you to continue to chain constraints onto the relationship query before finally executing the SQL against your database.

모든 Eloquent 관계는 메소드를 통해 정의되기 때문에, 관계 쿼리를 실제로 실행하는 대신에 이 메소드을 호출하여 관계 인스턴스를 가져올 수 있습니다. 모든 종류의 Eloquent 모델들은 또한 [쿼리 빌더](/docs/{{version}}/queries)의 역할을 하기 때문에 데이터베이스에 최종적으로 SQL을 실행하기 전에 관계 쿼리에 제한(where 구문)을 체이닝(호출->호출->호출 형태의 질의) 할 수 있도록 해줍니다.

For example, imagine a blog system in which a `User` model has many associated `Post` models:

예를 들어, `User` 모델이 다수의 `Post` 모델에 연관되어 있는 블로그 시스템을 상상해 봅시다:

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

You may query the `posts` relationship and add additional constraints to the relationship like so:

다음과 같이 `posts` 관계들을 쿼리하고 관계에 제한들을 추가할 수 있습니다:

    $user = App\User::find(1);

    $user->posts()->where('active', 1)->get();

You are able to use any of the [query builder](/docs/{{version}}/queries) methods on the relationship, so be sure to explore the query builder documentation to learn about all of the methods that are available to you.

관계에 대해서 어떠한 [쿼리 빌더](/docs/{{version}}/queries) 메소드도 사용 가능하기 때문에, 가능한 메소드들에 대해서 확인하려면 쿼리 빌더에 대한 문서를 확인하시기 바랍니다.

<a name="relationship-methods-vs-dynamic-properties"></a>
### Relationship Methods Vs. Dynamic Properties
### 관계 메소드 Vs. 동적 속성

If you do not need to add additional constraints to an Eloquent relationship query, you may access the relationship as if it were a property. For example, continuing to use our `User` and `Post` example models, we may access all of a user's posts like so:

Eloquent 관계 쿼리에 제한을 추가할 필요가 없다면 속성처럼 관계에 접근할 수 있습니다. 예를 들어, `User`와 `Post` 예시 모델들을 계속해서 사용하면 다음과 같이 사용자의 모든 게시물에 접근할 수 있습니다:

    $user = App\User::find(1);

    foreach ($user->posts as $post) {
        //
    }

Dynamic properties are "lazy loading", meaning they will only load their relationship data when you actually access them. Because of this, developers often use [eager loading](#eager-loading) to pre-load relationships they know will be accessed after loading the model. Eager loading provides a significant reduction in SQL queries that must be executed to load a model's relations.

동적 속성은 "지연 로딩"으로, 실제로 엑세스를 해야만 관계 데이터를 로드합니다. 그렇게 때문에 개발자들은 종종 모델을 로드한 뒤 접근해야할 관계들을 미리 로드해주는 [eager 로딩](#eager-loading)을 사용합니다. Eager 로딩은 모델의 관계를 로드하기 위해 실행되어야 할 SQL 쿼리들을 유의미하게 줄여줍니다.

<a name="querying-relationship-existence"></a>
### Querying Relationship Existence
### 관계의 존재 여부 쿼리 질의하기

When accessing the records for a model, you may wish to limit your results based on the existence of a relationship. For example, imagine you want to retrieve all blog posts that have at least one comment. To do so, you may pass the name of the relationship to the `has` and `orHas` methods:

모델의 기록에 접근할 때, 관계의 존재 여부에 따라 결과를 제한하기 원할 수 있습니다. 예를 들어 하나 이상의 댓글을 가진 모든 블로그 게시물을 조회하려고 한다고 생각해 봅시다. 이를 위해서 `has` 또는 `orHas` 메소드로 관계의 이름을 전달할 수 있습니다:

    // Retrieve all posts that have at least one comment...
    $posts = App\Post::has('comments')->get();

You may also specify an operator and count to further customize the query:

또한 메소드에서 사용할 수 있는 연산자와 카운트 갯수를 지정하여 쿼리를 계속하여 커스터마이즈할 수 있습니다:

    // Retrieve all posts that have three or more comments...
    $posts = App\Post::has('comments', '>=', 3)->get();

Nested `has` statements may also be constructed using "dot" notation. For example, you may retrieve all posts that have at least one comment and vote:

중첩된 `has` 구문(statement)은 "점(.)" 표기를 사용하여 구성될 수 있습니다. 예를 들어, 최소한 하나의 댓글과 좋아요(vote)를 가진 모든 게시물을 조회할 수 있습니다:

    // Retrieve all posts that have at least one comment with votes...
    $posts = App\Post::has('comments.votes')->get();

If you need even more power, you may use the `whereHas` and `orWhereHas` methods to put "where" conditions on your `has` queries. These methods allow you to add customized constraints to a relationship constraint, such as checking the content of a comment:

더 많은 권한이 필요하다면 `whereHas`와 `orWhereHas` 메소드를 사용하여 `has` 쿼리에 "where" 조건을 추가할 수 있습니다. 이 메소드들은 관계 제한에 댓글 컨텐츠 확인과 같은 사용자 정의된 제한들을 추가할 수 있게 해줍니다:

    // Retrieve all posts with at least one comment containing words like foo%
    $posts = App\Post::whereHas('comments', function ($query) {
        $query->where('content', 'like', 'foo%');
    })->get();

<a name="querying-relationship-absence"></a>
### Querying Relationship Absence
### 관계된 모델이 존재하지 않는 것을 확인하며 질의하기

When accessing the records for a model, you may wish to limit your results based on the absence of a relationship. For example, imagine you want to retrieve all blog posts that **don't** have any comments. To do so, you may pass the name of the relationship to the `doesntHave` and `orDoesntHave` methods:

모델의 레코드에 엑세스 할 때, 관계된 모델이 존재하지 않는 것에 따라서 결과를 제한하고자 할 수 있습니다. 예를 들어 코멘트를 가지고 있지 **않은** 모든 블로그 포스트를 조회하는 경우를 생각해 보겠습니다. 이렇게 하기 위해서는 `doesntHave` 또는 `orDoesntHave` 메소드에 정의한 관계의 이름을 전달하면됩니다:

    $posts = App\Post::doesntHave('comments')->get();

If you need even more power, you may use the `whereDoesntHave` and `orWhereDoesntHave` methods to put "where" conditions on your `doesntHave` queries. These methods allows you to add customized constraints to a relationship constraint, such as checking the content of a comment:

더 강력한 기능을 원한다면, `doesntHave` 쿼리에 "where" 조건을 붙여서, `whereDoesntHave` 와 `orWhereDoesntHave` 메소드를 사용할 수 있습니다. 이 메소드는 코멘트의 내용을 확인하는 것과 같이 관계 제약에 커스터마이징된 제약을 추가해준다.

    $posts = App\Post::whereDoesntHave('comments', function ($query) {
        $query->where('content', 'like', 'foo%');
    })->get();

You may use "dot" notation to execute a query against a nested relationship. For example, the following query will retrieve all posts with comments from authors that are not banned:

중첩된 관계에 대해서는 "점(.)" 표기법을 사용하여 쿼리를 질의할 수 있습니다. 예를들어, 다음의 쿼리는 작성자가 규제 받지 않은 모든 포스트와 코멘트를 조회합니다:

    $posts = App\Post::whereDoesntHave('comments.author', function ($query) {
        $query->where('banned', 1);
    })->get();

<a name="counting-related-models"></a>
### Counting Related Models
### 연관된 모델의 갯수 확인하기-카운팅

If you want to count the number of results from a relationship without actually loading them you may use the `withCount` method, which will place a `{relation}_count` column on your resulting models. For example:

관계 결과의 갯수를 실제 레코드를 로딩하지 않고 알고자 한다면, `withCount` 메소드를 사용하여 건수는 결과 모델의 `{관계}_count` 컬럼에 위치하도록 할 수 있습니다. 예를 들어:

    $posts = App\Post::withCount('comments')->get();

    foreach ($posts as $post) {
        echo $post->comments_count;
    }

You may add the "counts" for multiple relations as well as add constraints to the queries:

다수의 관계에 대해서도 쿼리에 제약을 추가하여 "갯수"를 조회할 수 있습니다.

    $posts = App\Post::withCount(['votes', 'comments' => function ($query) {
        $query->where('content', 'like', 'foo%');
    }])->get();

    echo $posts[0]->votes_count;
    echo $posts[0]->comments_count;

You may also alias the relationship count result, allowing multiple counts on the same relationship:

동일한 관계에 대하여 여러번 카운트를 수행하기 위해서 카운트 결과에 별칭(alias)를 부여할 수도 있습니다:

    $posts = App\Post::withCount([
        'comments',
        'comments as pending_comments_count' => function ($query) {
            $query->where('approved', false);
        }
    ])->get();

    echo $posts[0]->comments_count;

    echo $posts[0]->pending_comments_count;

<a name="eager-loading"></a>
## Eager Loading
## Eager 로딩

When accessing Eloquent relationships as properties, the relationship data is "lazy loaded". This means the relationship data is not actually loaded until you first access the property. However, Eloquent can "eager load" relationships at the time you query the parent model. Eager loading alleviates the N + 1 query problem. To illustrate the N + 1 query problem, consider a `Book` model that is related to `Author`:

Eloquent 관계들을 속성으로 접근할 때 관계 데이터는 "지연 로드" 되어있습니다. 이는 속성에 엑세스 하기 전까지 관계 데이터가 실제로 로드되지 않는다는 것을 의미합니다. 하지만 Eloquent는 부모 모델을 쿼리할 때 관계를 "eager 로드"할 수도 있습니다. Eager 로딩은 N + 1 쿼리 문제를 해결 합니다. N + 1 쿼리 문제에 대한 예제를 들어보자면 `Author`에 연관된 `Book` 모델을 생각해볼 수 있습니다:

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

Now, let's retrieve all books and their authors:

이제 모든 책과 그 저자들을 조회해봅시다:

    $books = App\Book::all();

    foreach ($books as $book) {
        echo $book->author->name;
    }

This loop will execute 1 query to retrieve all of the books on the table, then another query for each book to retrieve the author. So, if we have 25 books, this loop would run 26 queries: 1 for the original book, and 25 additional queries to retrieve the author of each book.

이 반복문은 테이블에서 모든 책들을 조회하는 1 개의 쿼리를 실행하고, 각각의 책마다 저자를 조회하는 별개의 쿼리를 실행할 것입니다. 따라서, 만약 25개의 책이 있다면, 이 반복문은 원래 책을 위한 하나의 쿼리와 각 책의 저자를 조회하는 25개의 추가적인 쿼리, 총 26개의 쿼리를 실행하게 됩니다.

Thankfully, we can use eager loading to reduce this operation to just 2 queries. When querying, you may specify which relationships should be eager loaded using the `with` method:

다행히도, eager 로딩을 사용하면 이 작업을 2개의 쿼리로 줄일 수 있습니다. 쿼리를 실행할 때 `with` 메소드를 사용하여 어떤 관계가 eager 로드되어야 하는지 지정할 수 있습니다:

    $books = App\Book::with('author')->get();

    foreach ($books as $book) {
        echo $book->author->name;
    }

For this operation, only two queries will be executed:

이 작업에서는 두 개의 쿼리만이 실행될 것입니다:

    select * from books

    select * from authors where id in (1, 2, 3, 4, 5, ...)

#### Eager Loading Multiple Relationships
#### 여러 관계에 대해서 Eager 로딩하기

Sometimes you may need to eager load several different relationships in a single operation. To do so, just pass additional arguments to the `with` method:

종종 하나의 작업에서 여러 개의 다른 관계들을 eager 로드해야 될 때가 있습니다. 이 경우 `with` 메소드에 추가 인자들을 전달하면 됩니다:

    $books = App\Book::with(['author', 'publisher'])->get();

#### Nested Eager Loading
#### 중첩된 Eager 로딩하기

To eager load nested relationships, you may use "dot" syntax. For example, let's eager load all of the book's authors and all of the author's personal contacts in one Eloquent statement:

"점" 구문을 이용하면 중첩된 관계들을 eager 로드할 수 있습니다. 예를 들어, 하나의 Eloquent 구문(statement)에서 책의 모든 저자들과 저자들의 모든 연락처를 eager 로드해보겠습니다:

    $books = App\Book::with('author.contacts')->get();

#### Eager Loading Specific Columns
#### Eager 로딩에서 컬럼 지정하기

You may not always need every column from the relationships you are retrieving. For this reason, Eloquent allows you to specify which columns of the relationship you would like to retrieve:

조회하고자 하는 관계에서 항상 모든 컬럼이 필요한 것은 아닙니다. 이 경우, Eloquent 는 조회하고자 하는 관계에 컬럼을 지정할 수 있습니다:

    $users = App\Book::with('author:id,name')->get();

> {note} When using this feature, you should always include the `id` column in the list of columns you wish to retrieve.

> {note} 이 기능을 사용할 때에는, 조회하고자 하는 컬럼에 항상 `id` 컬럼이 포함되어 있어야 합니다.

<a name="constraining-eager-loads"></a>
### Constraining Eager Loads
### Eager 로딩에서 조건을 통해 질의 제한하기

Sometimes you may wish to eager load a relationship, but also specify additional query constraints for the eager loading query. Here's an example:

때로는 관계를 eager 로드하면서 eager 로딩 쿼리에 추가적인 쿼리 제한들을 지정하고 싶을 수 있습니다. 다음은 그 예제입니다:

    $users = App\User::with(['posts' => function ($query) {
        $query->where('title', 'like', '%first%');
    }])->get();

In this example, Eloquent will only eager load posts where the post's `title` column contains the word `first`. Of course, you may call other [query builder](/docs/{{version}}/queries) methods to further customize the eager loading operation:

이 예제에서 Eloquent는 게시물의 `title` 컬럼이 `first`라는 단어를 포함할 때만 게시물을 eager 로드할 것입니다. 물론 다른 [쿼리 빌더](/docs/{{version}}/queries)메소드를 호출하여 계속하여서 eager 로딩 작업을 커스터마이즈할 수 있습니다:

    $users = App\User::with(['posts' => function ($query) {
        $query->orderBy('created_at', 'desc');
    }])->get();

<a name="lazy-eager-loading"></a>
### Lazy Eager Loading
### 지연 Eager 로딩

Sometimes you may need to eager load a relationship after the parent model has already been retrieved. For example, this may be useful if you need to dynamically decide whether to load related models:

종종 부모 모델이 조회된 후에 관계를 eager 로드해야할 필요가 있을 수 있습니다. 예를 들자면, 연관된 모델들을 동적으로 결정해야할 때 유용하게 사용됩니다:

    $books = App\Book::all();

    if ($someCondition) {
        $books->load('author', 'publisher');
    }

If you need to set additional query constraints on the eager loading query, you may pass an array keyed by the relationships you wish to load. The array values should be `Closure` instances which receive the query instance:

Eager 로딩 쿼리에 추가적인 쿼리 제한을 지정해야 할 경우, `load` 메소드에 로그하고자 하는 관계에 대한 키로 구성된 배열을 전달할 수 있습니다. 이 배열의 값은 쿼리 인스턴스를 인자로 받아들이는 `Closure`이어야만 합니다:

    $books->load(['author' => function ($query) {
        $query->orderBy('published_date', 'asc');
    }]);

To load a relationship only when it has not already been loaded, use the `loadMissing` method:

로딩되어 있지 않았을 때에만 관계 모델을 로딩하려면 `loadMissing` 메소드를 사용하면됩니다:

    public function format(Book $book)
    {
        $book->loadMissing('author');

        return [
            'name' => $book->name,
            'author' => $book->author->name
        ];
    }

<a name="inserting-and-updating-related-models"></a>
## Inserting & Updating Related Models
## 연관된 모델 삽입하기 & 수정하기

<a name="the-save-method"></a>
### The Save Method
### Save 메소드

Eloquent provides convenient methods for adding new models to relationships. For example, perhaps you need to insert a new `Comment` for a `Post` model. Instead of manually setting the `post_id` attribute on the `Comment`, you may insert the `Comment` directly from the relationship's `save` method:

Eloquentsms 관계에 새로운 모델을 추가하는 편리한 메소드들을 제공합니다. 예를 들어, `Post` 모델에 새로운 `Comment`를 추가해야 할 수 있습니다. `Comment`에 수동으로 `post_id` 속성을 지정하는 대신 관계에 `save` 메소드에서 바로 `Comment`를 추가할 수 있습니다:

    $comment = new App\Comment(['message' => 'A new comment.']);

    $post = App\Post::find(1);

    $post->comments()->save($comment);

Notice that we did not access the `comments` relationship as a dynamic property. Instead, we called the `comments` method to obtain an instance of the relationship. The `save` method will automatically add the appropriate `post_id` value to the new `Comment` model.

`comments` 관계를 동적 속성으로 접근하지 않았다는 점에 주목 하십시오. 대신에 `comments` 메소드를 호출하여 관계의 인스턴를 얻었습니다. `save` 메소드는 자동으로 새로운 `Comment` 모델에 적절한 `post_id` 값을 추가할 것입니다.

If you need to save multiple related models, you may use the `saveMany` method:

여러 개의 관련된 모델을 저장해야 한다면 `saveMany` 메소드를 사용하세요:

    $post = App\Post::find(1);

    $post->comments()->saveMany([
        new App\Comment(['message' => 'A new comment.']),
        new App\Comment(['message' => 'Another comment.']),
    ]);

<a name="the-create-method"></a>
### The Create Method
### Create 메소드

In addition to the `save` and `saveMany` methods, you may also use the `create` method, which accepts an array of attributes, creates a model, and inserts it into the database. Again, the difference between `save` and `create` is that `save` accepts a full Eloquent model instance while `create` accepts a plain PHP `array`:

`save`와 `saveMany` 메소드 외에도 속성을 배열을 받아들이고 모델을 생성하여 데이터베이스에 삽입하는 `create` 메소드를 사용하실 수 있습니다. `save`는 완전한 Eloquent 모델 인스턴스를 받아들이는 데 반해 `create`는 순수 PHP `배열`를 받는 다는 점에서 차이가 있습니다:

    $post = App\Post::find(1);

    $comment = $post->comments()->create([
        'message' => 'A new comment.',
    ]);

> {tip} Before using the `create` method, be sure to review the documentation on attribute [mass assignment](/docs/{{version}}/eloquent#mass-assignment).

{팁} `create` 메소드를 사용하기 전에 [대량 할당-mass assignment](/docs/{{version}}/eloquent#mass-assignment) 문서를 반드시 확인하시기 바랍니다.

You may use the `createMany` method to create multiple related models:

`createMany` 메소드를 사용해서 여러 개의 관련된 모델을 만들 수 있습니다:

    $post = App\Post::find(1);

    $post->comments()->createMany([
        [
            'message' => 'A new comment.',
        ],
        [
            'message' => 'Another new comment.',
        ],
    ]);


<a name="updating-belongs-to-relationships"></a>
### Belongs To Relationships
### Belongs To 관계

When updating a `belongsTo` relationship, you may use the `associate` method. This method will set the foreign key on the child model:

`belongsTo` 관계를 변경 할 때 `associate` 메소드를 사용할 수 있습니다. 이 메소드는 자식 모델에 외래 키를 지정합니다:

    $account = App\Account::find(10);

    $user->account()->associate($account);

    $user->save();

When removing a `belongsTo` relationship, you may use the `dissociate` method. This method will set the relationship's foreign key to `null`:

`belongsTo` 관계를 제거할 때는 `dissociate` 메소드를 사용하시면 됩니다. 이 메소드는 외래 키를 `null` 로 설정할 것입니다:

    $user->account()->dissociate();

    $user->save();

<a name="updating-many-to-many-relationships"></a>
### Many To Many Relationships
### 다대다 관계

#### Attaching / Detaching
#### 추가하기 / 분리하기

Eloquent also provides a few additional helper methods to make working with related models more convenient. For example, let's imagine a user can have many roles and a role can have many users. To attach a role to a user by inserting a record in the intermediate table that joins the models, use the `attach` method:

Eloquent는 또한 연관된 모델들을 다루는 데 편리한 몇 개의 헬퍼 메소드를 추가로 제공합니다. 예를 들어 한 사용자가 여러 역할을 가질 수 있고 한 역할이 여러 사용자를 갖는다고 상상해봅시다. 모델들을 합치는 중간 테이블에 기록을 추가하여 사용자에게 역할을 추가하려면 `attach` 메소드를 사용하세요:

    $user = App\User::find(1);

    $user->roles()->attach($roleId);

When attaching a relationship to a model, you may also pass an array of additional data to be inserted into the intermediate table:

모델에 관계를 추가할 때 중간 테이블에 삽입될 추가 데이터의 배열을 전달할 수도 있습니다:

    $user->roles()->attach($roleId, ['expires' => $expires]);

Of course, sometimes it may be necessary to remove a role from a user. To remove a many-to-many relationship record, use the `detach` method. The `detach` method will remove the appropriate record out of the intermediate table; however, both models will remain in the database:

물론 경우에 따라 사용자에게서 역할을 분리하는 것이 필요할 수 있습니다. 다대다 관계 기록을 제거하려면 `detach` 메소드를 이용하면 됩니다. `detach` 메소드는 중간 테이블에서 적절한 기록을 삭제할 것입니다. 하지만 두 모델은 모두 데이터베이스에 남을 것입니다:

    // Detach a single role from the user...
    $user->roles()->detach($roleId);

    // Detach all roles from the user...
    $user->roles()->detach();

For convenience, `attach` and `detach` also accept arrays of IDs as input:

보다 편리하게, `attach`와 `detach` ID의 배열 또한 입력으로 전달 받습니다:

    $user = App\User::find(1);

    $user->roles()->detach([1, 2, 3]);

    $user->roles()->attach([
        1 => ['expires' => $expires],
        2 => ['expires' => $expires]
    ]);

#### Syncing Associations
#### 연결 동기화

You may also use the `sync` method to construct many-to-many associations. The `sync` method accepts an array of IDs to place on the intermediate table. Any IDs that are not in the given array will be removed from the intermediate table. So, after this operation is complete, only the IDs in the given array will exist in the intermediate table:

다대다 관계를 생성하기 위해 `sync` 메소드를 사용할 수도 있습니다. `sync` 메소드는 중간 테이블에 놓을 ID의 배열을 전달 받습니다. 주어진 배열에 포함되어 있지 않는 ID는 중간 테이블에서 제거됩니다. 따라서 이 작업이 끝나면 주어진 배열에 포함된 ID들만이 중간 테이블에 존재할 것입니다:

    $user->roles()->sync([1, 2, 3]);

You may also pass additional intermediate table values with the IDs:

ID들과 함께 중간 테이블 값을 추가로 전달할 수도 있습니다:

    $user->roles()->sync([1 => ['expires' => true], 2, 3]);

If you do not want to detach existing IDs, you may use the `syncWithoutDetaching` method:

존재하는 ID들을 삭제하고 싶지 않으면, `syncWithoutDetaching` 메소드를 사용하면 됩니다:

    $user->roles()->syncWithoutDetaching([1, 2, 3]);

#### Toggling Associations
#### 연결 켜고 끄기(토클)

The many-to-many relationship also provides a `toggle` method which "toggles" the attachment status of the given IDs. If the given ID is currently attached, it will be detached. Likewise, if it is currently detached, it will be attached:

다대다 관계는 또한 주어진 ID들의 추가된 상태를 "전환"할 수 있는 `toggle` 메소드를 제공합니다. 만약 주어진 ID가 현재 추가되었다면, 이는 해제될것이고 마찬가지로 현재 추가되지 않은 상태라면 추가됩니다:

    $user->roles()->toggle([1, 2, 3]);

#### Saving Additional Data On A Pivot Table
#### 피벗 테이블에서 추가직인 데이터 저장하기

When working with a many-to-many relationship, the `save` method accepts an array of additional intermediate table attributes as its second argument:

다대다 관계를 작업할 때, `save` 메소드는 두번째 인자로 추가적인 중간 테이블의 속성에 대한 배열을 받아 들입니다.

    App\User::find(1)->roles()->save($role, ['expires' => $expires]);

#### Updating A Record On A Pivot Table
#### 피벗 테이블 레코드 수정하기

If you need to update an existing row in your pivot table, you may use `updateExistingPivot` method. This method accepts the pivot record foreign key and an array of attributes to update:

피벗 테이블에 존재하는 레코드를 수정할 필요가 있다면, `updateExistingPivot` 메소드를 사용하면 됩니다. 이 메소드는 피벗 테이블의 외래 키와 수정하려는 속성에 대한 배열을 인자로 받습니다:

    $user = App\User::find(1);

    $user->roles()->updateExistingPivot($roleId, $attributes);

<a name="touching-parent-timestamps"></a>
## Touching Parent Timestamps
## 부모의 타임스탬프 값 갱신

When a model `belongsTo` or `belongsToMany` another model, such as a `Comment` which belongs to a `Post`, it is sometimes helpful to update the parent's timestamp when the child model is updated. For example, when a `Comment` model is updated, you may want to automatically "touch" the `updated_at` timestamp of the owning `Post`. Eloquent makes it easy. Just add a `touches` property containing the names of the relationships to the child model:

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

Now, when you update a `Comment`, the owning `Post` will have its `updated_at` column updated as well, making it more convenient to know when to invalidate a cache of the `Post` model:

이제 `Comment`를 업데이트할 때 소유하는 `Post`의 `updated_at` 컬럼 또한 업데이트될 것이고, 이렇게 하는 것은 `Post` 모델의 캐시가 갱신되는 편리한 방법이기도 합니다:

    $comment = App\Comment::find(1);

    $comment->text = 'Edit to this comment!';

    $comment->save();
