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
    - [Defining Custom Intermediate Table Models](#defining-custom-intermediate-table-models)
    - [커스텀 중간 테이블 모델 정의하기](#defining-custom-intermediate-table-models)
    - [Has One Through](#has-one-through)
    - [연결을 통한 단일 관계](#has-one-through)
    - [Has Many Through](#has-many-through)
    - [연결을 통한 다수를 가지는 관계](#has-many-through)
- [Polymorphic Relationships](#polymorphic-relationships)
- [다형성 관계](#polymorphic-relationships)
    - [One To One](#one-to-one-polymorphic-relations)
    - [1:1(일대일) 관계](#one-to-one-polymorphic-relations)
    - [One To Many](#one-to-many-polymorphic-relations)
    - [1:*(일대다) 관계](#one-to-many-polymorphic-relations)
    - [Many To Many](#many-to-many-polymorphic-relations)
    - [\*:*(다대다) 관계](#many-to-many-polymorphic-relations)
    - [Custom Polymorphic Types](#custom-polymorphic-types)
    - [사용자 정의 다형성 타입](#custom-polymorphic-types)
- [Dynamic Relationships](#dynamic-relationships)
- [동적 관계](#dynamic-relationships)
- [Querying Relations](#querying-relations)
- [관계 쿼리 질의하기](#querying-relations)
    - [Relationship Methods Vs. Dynamic Properties](#relationship-methods-vs-dynamic-properties)
    - [관계 메소드 Vs. 동적 속성](#relationship-methods-vs-dynamic-properties)
    - [Querying Relationship Existence](#querying-relationship-existence)
    - [존재하는 관계에 대한 쿼리 질의하기](#querying-relationship-existence)
    - [Querying Relationship Absence](#querying-relationship-absence)
    - [관계된 모델이 존재하지 않는 것을 확인하며 질의하기](#querying-relationship-absence)
    - [Querying Polymorphic Relationships](#querying-polymorphic-relationships)
    - [다형성 관계 쿼리](#querying-polymorphic-relationships)
    - [Counting Related Models](#counting-related-models)
    - [연관된 모델 수량 확인하기-카운트](#counting-related-models)
    - [Counting Related Models On Polymorphic Relationships](#counting-related-models-on-polymorphic-relationships)
    - [다형성 관계에서 관련된 모델 수량 확인하기](#counting-related-models-on-polymorphic-relationships)
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

데이터베이스 테이블은 주로 서로 관련되어 있습니다. 예를 들어, 한 블로그 포스트가 많은 댓글을 가지고 있거나 어떤 명령이 그 명령을 내린 사용자와 관련되어 있을 수 있습니다. Eloquent는 이 relationship들과 관련한 작업을 하거나 관리하는 것을 쉽게 해주며 여러 타입의 relationship을 지원합니다.

- [One To One](#one-to-one)
- [1:1(일대일) 관계](#one-to-one)
- [One To Many](#one-to-many)
- [1:*(일대다) 관계](#one-to-many)
- [Many To Many](#many-to-many)
- [\*:*(다대다) 관계](#many-to-many)
- [Has One Through](#has-one-through)
- [연결을 통한 단일 관계](#has-one-through)
- [Has Many Through](#has-many-through)
- [연결을 통한 다수를 가지는 관계](#has-many-through)
- [One To One (Polymorphic)](#one-to-one-polymorphic-relations)
- [1:1(일대일) (다형성)](#one-to-one-polymorphic-relations)
- [One To Many (Polymorphic)](#one-to-many-polymorphic-relations)
- [1:*(일대다) (다형성)](#one-to-many-polymorphic-relations)
- [Many To Many (Polymorphic)](#many-to-many-polymorphic-relations)
- [\*:*(다대다) (다형성)](#many-to-many-polymorphic-relations)

<a name="defining-relationships"></a>
## Defining Relationships
## 관계 정의하기

Eloquent relationships are defined as methods on your Eloquent model classes. Since, like Eloquent models themselves, relationships also serve as powerful [query builders](/docs/{{version}}/queries), defining relationships as methods provides powerful method chaining and querying capabilities. For example, we may chain additional constraints on this `posts` relationship:

Eloquent relationship들은 Eloquent 모델 클래스에 메소드로 정의되어 있습니다. Eloquent 모델들과 같이 relationships-관계를 정의한 것은 강력한 [쿼리 빌더](/docs/{{version}}/queries)로써의 기능으로도 작동하기 때문에 relationships-관계를 메소드로 정의하는 것은 강력한 메소드 체이닝과 쿼리 능력을 제공하게됩니다. 예를 들어 다음의 `post` 관계에 대해서 추가적인 제약조건을 체이닝할 수도 있습니다.

    $user->posts()->where('active', 1)->get();

But, before diving too deep into using relationships, let's learn how to define each type.

relationship을 사용하는 법에 더 깊이 알아보기 전에, 각 타입을 어떻게 정의햐는지 알아보도록 합시다.

> {note} Relationship names cannot collide with attribute names as that could lead to your model not being able to know which one to resolve.

> {note} relationship 이름은 attribute 이름과 충돌 할 수 없으므로 모델이 처리해야 할 모델을 알지 못할 수 있습니다.

<a name="one-to-one"></a>
### One To One
### 1:1(일대일) 관계 정의하기

A one-to-one relationship is a very basic relation. For example, a `User` model might be associated with one `Phone`. To define this relationship, we place a `phone` method on the `User` model. The `phone` method should call the `hasOne` method and return its result:

일대일 relationship은 아주 기본적인 관계입니다. 예를 들어, 하나의 `User` 모델이 하나의 `Phone`과 관계되어 있을 수 있습니다. 이 relationship을 정의하기 위해 `User` 모델에 `phone` 메소드를 구성합니다. `phone` 메소드는 `hasOne` 메소드를 호출하게 되고 그 결과를 반환할 것입니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * Get the phone record associated with the user.
         */
        public function phone()
        {
            return $this->hasOne('App\Models\Phone');
        }
    }

The first argument passed to the `hasOne` method is the name of the related model. Once the relationship is defined, we may retrieve the related record using Eloquent's dynamic properties. Dynamic properties allow you to access relationship methods as if they were properties defined on the model:

`hasOne` 메소드에 전달되는 첫번째 인자는 관련된 모델의 이름입니다. Relationship이 정의되었다면, Eloquent의 동적 속성을 이용하여 관련된 레코드를 찾을 수 있습니다. 동적 속성은 모델에 정의된 속성에 접근하는 방식과 같은 방식으로 relationship 메소드에 접근하는 것을 허용합니다.

    $phone = User::find(1)->phone;

Eloquent determines the foreign key of the relationship based on the model name. In this case, the `Phone` model is automatically assumed to have a `user_id` foreign key. If you wish to override this convention, you may pass a second argument to the `hasOne` method:

Eloquent는 모델 이름을 기반으로 relationship의 외래 키(foreign key)를 결정합니다. 이 경우, `Phone` 모델은 `user_id` 외래 키를 가질 것이라고 자동으로 추정됩니다. 이 규칙을 재정의하고 싶다면, `hasOne` 메소드에 두번째 인자를 전달하면 됩니다.

    return $this->hasOne('App\Models\Phone', 'foreign_key');

Additionally, Eloquent assumes that the foreign key should have a value matching the `id` (or the custom `$primaryKey`) column of the parent. In other words, Eloquent will look for the value of the user's `id` column in the `user_id` column of the `Phone` record. If you would like the relationship to use a value other than `id`, you may pass a third argument to the `hasOne` method specifying your custom key:

Eloquent는 또한 외래 키가 부모의 `id` 컬럼(또는 사용자가 정의한 `$primaryKey`)에 상응하는 값을 가지고 있다고 추정합니다. 즉, Eloquent는 `Phone`레코드의 `user_id` 컬럼에서 사용자 `id` 컬럼의 값을 찾을 것입니다. Relationship이 `id`가 아닌 다른 값을 사용하고자 한다면 커스텀 키를 지정하는 세번째 인자를 `hasOne` 메소드로 전달하면 됩니다.

    return $this->hasOne('App\Models\Phone', 'foreign_key', 'local_key');

#### Defining The Inverse Of The Relationship
#### 관계의 역(반대) 정의하기

So, we can access the `Phone` model from our `User`. Now, let's define a relationship on the `Phone` model that will let us access the `User` that owns the phone. We can define the inverse of a `hasOne` relationship using the `belongsTo` method:

이제 `User`에서 `Phone` 모델에 접근할 수 있습니다. 반대로, `Phone` 모델에 relationship을 정의하여 phone을 소유하는 `User`에 접근할 수 있도록 해봅시다. `belongsTo` 메소드를 이용하면 `hasOne` relationship의 반대를 정의할 수 있습니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class Phone extends Model
    {
        /**
         * Get the user that owns the phone.
         */
        public function user()
        {
            return $this->belongsTo('App\Models\User');
        }
    }

In the example above, Eloquent will try to match the `user_id` from the `Phone` model to an `id` on the `User` model. Eloquent determines the default foreign key name by examining the name of the relationship method and suffixing the method name with `_id`. However, if the foreign key on the `Phone` model is not `user_id`, you may pass a custom key name as the second argument to the `belongsTo` method:

위 예에서 Eloquent는 `Phone` 모델의 `user_id`와 `User` 모델의 `id`를 비교해볼 것입니다. Eloquent는 relationship 메소드의 이름을 검사하고 메소드 이름에 `_id`를 붙여서 외래 키의 기본 이름으로 결정합니다. 하지만 `Phone` 모델의 외래 키가 `user_id`가 아니라면 커스텀 키 이름을 두번째 인자로 `belongsTo`에 전달하면 됩니다.

    /**
     * Get the user that owns the phone.
     */
    public function user()
    {
        return $this->belongsTo('App\Models\User', 'foreign_key');
    }

If your parent model does not use `id` as its primary key, or you wish to join the child model to a different column, you may pass a third argument to the `belongsTo` method specifying your parent table's custom key:

부모 모델이 `id`를 프라이머리 키로 사용하지 않거나 자식 모델을 다른 컬럼에 조인시키고 싶다면 부모 테이블의 커스텀 키를 지정하는 세번째 인자를 `belongsTo` 메소드로 전달하면 됩니다.

    /**
     * Get the user that owns the phone.
     */
    public function user()
    {
        return $this->belongsTo('App\Models\User', 'foreign_key', 'other_key');
    }

<a name="one-to-many"></a>
### One To Many
### 1:*(일대다) 관계 정의하기

A one-to-many relationship is used to define relationships where a single model owns any amount of other models. For example, a blog post may have an infinite number of comments. Like all other Eloquent relationships, one-to-many relationships are defined by placing a function on your Eloquent model:

일대다 relationship은 하나의 모델이 다른 모델의 어떤 부분이라도 소유할 경우의 relationship을 정의하는데 사용됩니다. 예를 들어, 한 블로그 게시물이 댓글을 무제한으로 가질 수 있습니다. 다른 모든 Eloquent relationship들과 같이, 일대다 relationship들은 Eloquent 모델에 함수를 통해서 정의됩니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class Post extends Model
    {
        /**
         * Get the comments for the blog post.
         */
        public function comments()
        {
            return $this->hasMany('App\Models\Comment');
        }
    }

Remember, Eloquent will automatically determine the proper foreign key column on the `Comment` model. By convention, Eloquent will take the "snake case" name of the owning model and suffix it with `_id`. So, for this example, Eloquent will assume the foreign key on the `Comment` model is `post_id`.

앞서 언급했듯이 Eloquent는 `Comment` 모델에 적절한 외래 키를 자동으로 결정합니다. Eloquent는 관례적으로 소유하는 모델의 "snake case" 이름에 `_id`를 붙일 것입니다. 따라서 이 예에서 Eloqent는 `Comment` 모델의 외래 키가 `post_id`일 것이라고 추정합니다.

Once the relationship has been defined, we can access the collection of comments by accessing the `comments` property. Remember, since Eloquent provides "dynamic properties", we can access relationship methods as if they were defined as properties on the model:

관계가 정의되었다면 `comments` 속성에 접근하여 댓글 모음에 엑세스할 수 있습니다. 앞서 말했듯이 Eloquent는 "동적 속성"을 제공하기 때문에 모델의 속성에 접근하듯이 relationship-관계 메소드에 접근할 수 있습니다.

    $comments = App\Models\Post::find(1)->comments;

    foreach ($comments as $comment) {
        //
    }

Since all relationships also serve as query builders, you can add further constraints to which comments are retrieved by calling the `comments` method and continuing to chain conditions onto the query:

모든 관계들은 쿼리 빌더로도 역할하기 때문에 `comments` 메소드를 호출하고 쿼리에 조건들을 계속해서 체인하는 것을 통해 어떤 댓글들이 조회되는지에 대한 제한들을 추가할 수 있습니다.

    $comment = App\Models\Post::find(1)->comments()->where('title', 'foo')->first();

Like the `hasOne` method, you may also override the foreign and local keys by passing additional arguments to the `hasMany` method:

`hasOne` 메소드와 같이 `hasMany` 메소드에 추가적인 인자들을 전달하여 외래 및 로컬 키들을 재지정 할 수 있습니다.

    return $this->hasMany('App\Models\Comment', 'foreign_key');

    return $this->hasMany('App\Models\Comment', 'foreign_key', 'local_key');

<a name="one-to-many-inverse"></a>
### One To Many (Inverse)
### 1:*(일대다) 역관계

Now that we can access all of a post's comments, let's define a relationship to allow a comment to access its parent post. To define the inverse of a `hasMany` relationship, define a relationship function on the child model which calls the `belongsTo` method:

이제 게시물의 모든 댓글에 접근할 수 있으니 댓글이 부모 게시물에 접근할 수 있도록 해주는 관계를 정의해 봅시다. `hasMany` 관계의 반대를 정의하려면 `belongsTo` 메소드를 호출하는 자식 모델에 관계 함수를 정의하면 됩니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class Comment extends Model
    {
        /**
         * Get the post that owns the comment.
         */
        public function post()
        {
            return $this->belongsTo('App\Models\Post');
        }
    }

Once the relationship has been defined, we can retrieve the `Post` model for a `Comment` by accessing the `post` "dynamic property":

관계가 정의되었다면 `post` "동적 속성"에 접근하여 `Comment`에 대한 `Post` 모델을 찾을 수 있습니다.

    $comment = App\Models\Comment::find(1);

    echo $comment->post->title;

In the example above, Eloquent will try to match the `post_id` from the `Comment` model to an `id` on the `Post` model. Eloquent determines the default foreign key name by examining the name of the relationship method and suffixing the method name with a `_` followed by the name of the primary key column. However, if the foreign key on the `Comment` model is not `post_id`, you may pass a custom key name as the second argument to the `belongsTo` method:

위의 예에서 Eloqent는 `Comment` 모델의 `post_id`와 `Post` 모델의 `id`를 비교해볼 것입니다. Eloquent는 relationship 메소드의 이름을 검사하고 메소드 이름 뒤에 `_`와 기본 키 컬럼을 붙여서 외래 키의 기본 이름을 결정합니다. 하지만 `Comment` 모델의 외래 키가 `post_id`가 아니라면 커스텀 키 이름을 두번째 인자로 `belongsTo`에 전달하면 됩니다.

    /**
     * Get the post that owns the comment.
     */
    public function post()
    {
        return $this->belongsTo('App\Models\Post', 'foreign_key');
    }

If your parent model does not use `id` as its primary key, or you wish to join the child model to a different column, you may pass a third argument to the `belongsTo` method specifying your parent table's custom key:

부모 모델이 `id`를 프라이머리 키로 사용하지 않거나 자식 모델을 다른 컬럼에 조인시키고 싶으면 부모 테이블의 커스텀 키를 지정하는 세번째 인자를 `belongsTo` 메소드로 전달하면 됩니다.

    /**
     * Get the post that owns the comment.
     */
    public function post()
    {
        return $this->belongsTo('App\Models\Post', 'foreign_key', 'other_key');
    }

<a name="many-to-many"></a>
### Many To Many
### \*:* (다대다) 관계 정의하기

Many-to-many relations are slightly more complicated than `hasOne` and `hasMany` relationships. An example of such a relationship is a user with many roles, where the roles are also shared by other users. For example, many users may have the role of "Admin".

다 대다 관계는 `hasOne` 및 `hasMany` 관계보다 약간 더 복잡합니다. 이러한 관계의 예는 많은 역할을 가진 유저이며, 다른 유저도 해당 역할을 공유합니다. 예를 들어 많은 유저가 "관리자" 역할을 할 수 있습니다.

#### Table Structure
#### Table 구조

To define this relationship, three database tables are needed: `users`, `roles`, and `role_user`. The `role_user` table is derived from the alphabetical order of the related model names, and contains the `user_id` and `role_id` columns:

이 관계를 정의하려면 `users`, `roles` 및 `role_user`의 세 가지 데이터베이스 테이블이 필요합니다. `role_user` 테이블은 관련 모델 이름의 알파벳 순서에서 파생되며`user_id` 및 `role_id` 열을 포함합니다.

    users
        id - integer
        name - string

    roles
        id - integer
        name - string

    role_user
        user_id - integer
        role_id - integer

#### Model Structure
#### Model 구조

Many-to-many relationships are defined by writing a method that returns the result of the `belongsToMany` method. For example, let's define the `roles` method on our `User` model:

다대다 관계는 `belongsToMany` 메소드의 결과를 반환하는 메소드를 작성하여 정의합니다. 예를 들어 `User` 모델에 `roles` 메소드를 정의해보도록 하겠습니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * The roles that belong to the user.
         */
        public function roles()
        {
            return $this->belongsToMany('App\Models\Role');
        }
    }

Once the relationship is defined, you may access the user's roles using the `roles` dynamic property:

관계가 정의되었다면 `roles` 동적 속성을 사용하여 사용자들의 역할에 접근할 수 있습니다.

    $user = App\Models\User::find(1);

    foreach ($user->roles as $role) {
        //
    }

Like all other relationship types, you may call the `roles` method to continue chaining query constraints onto the relationship:

다른 모든 관계 타입과 같이 `roles` 메소드를 호출하여 관계에 대해서 쿼리 제한 조건들을 계속하여 체이닝 할 수 있습니다.

    $roles = App\Models\User::find(1)->roles()->orderBy('name')->get();

As mentioned previously, to determine the table name of the relationship's joining table, Eloquent will join the two related model names in alphabetical order. However, you are free to override this convention. You may do so by passing a second argument to the `belongsToMany` method:

앞서 살펴본 바와같이, Eloquent는 relationship-관계의 조인(join) 테이블의 테이블 명을 결정하기 위해 관련된 두 모델 이름을 알파벳 순으로 합칠 것입니다. (user 와 role 일 경우에 role_user 로 이름을 추정합니다) 하지만 이러한 관례는 재지정 할 수 있습니다. `belongsToMany` 메소드로 두번째 인자를 전달하면 됩니다.

    return $this->belongsToMany('App\Models\Role', 'role_user');

In addition to customizing the name of the joining table, you may also customize the column names of the keys on the table by passing additional arguments to the `belongsToMany` method. The third argument is the foreign key name of the model on which you are defining the relationship, while the fourth argument is the foreign key name of the model that you are joining to:

join 테이블의 이름을 커스터마이징하는 것 외에도 `belongsToMany` 메소드에 추가 인자들을 전달하면 키들의 이름들 또한 커스터마이징 할 수 있습니다. 세번째 인자는 관계를 정의하는 모델의 외래 키 이름이고 네번째 인자는 join 하는 모델의 외래 키 이름입니다.

    return $this->belongsToMany('App\Models\Role', 'role_user', 'user_id', 'role_id');

#### Defining The Inverse Of The Relationship
#### 관계의 역(반대) 정의하기

To define the inverse of a many-to-many relationship, you place another call to `belongsToMany` on your related model. To continue our user roles example, let's define the `users` method on the `Role` model:

다대다 관계의 반대를 정의하려면 관련된 모델에 `belongsToMany`를 호출합니다. 사용자와 역할에 대한 예를 계속 들어 생각해서, `Role` 모델에 `users` 메소드를 정의해봅시다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class Role extends Model
    {
        /**
         * The users that belong to the role.
         */
        public function users()
        {
            return $this->belongsToMany('App\Models\User');
        }
    }

As you can see, the relationship is defined exactly the same as its `User` counterpart, with the exception of referencing the `App\Models\User` model. Since we're reusing the `belongsToMany` method, all of the usual table and key customization options are available when defining the inverse of many-to-many relationships.

위에서 볼 수 있듯이 `App\Models\User` 모델을 참고하는 것만 제외하고 relationship-관계는 대응하는 `User`와 정확히 동일하게 정의되어 있습니다. `belongsToMany` 메소드를 재사용하고 있기 때문에 다대다 관계의 역-반대를 정의할 때에는 테이블과 키의 모든 통상적인 커스터마이즈 옵션이 사용 가능합니다.

#### Retrieving Intermediate Table Columns
#### 중간 테이블 컬럼 조회하기

As you have already learned, working with many-to-many relations requires the presence of an intermediate table. Eloquent provides some very helpful ways of interacting with this table. For example, let's assume our `User` object has many `Role` objects that it is related to. After accessing this relationship, we may access the intermediate table using the `pivot` attribute on the models:

앞서 알아 보았듯이, 다대다 관계는 중간 테이블의 존재를 필요로 합니다. Eloquent는 이 테이블과 상호 작용할 수 있게 도움을 주는 몇몇 방법들을 제공합니다. 예를 들어, `User` 객체가 여러 `Role` 객체에 관련되어 있다고 생각해 봅시다. 이 관계에 접근한 후 모델들에 `pivot` 속성을 사용하여 중간 테이블에 접근할 수 있습니다.

    $user = App\Models\User::find(1);

    foreach ($user->roles as $role) {
        echo $role->pivot->created_at;
    }

Notice that each `Role` model we retrieve is automatically assigned a `pivot` attribute. This attribute contains a model representing the intermediate table, and may be used like any other Eloquent model.

조회되는 각 `Role` 모델은 자동으로 `pivot` 속성을 부여받습니다. 이 속성은 중간 테이블을 나타내는 모델을 포함하고 있으며 여느 Eloquent 모델과 다르지 않게 사용될 수 있습니다.

By default, only the model keys will be present on the `pivot` object. If your pivot table contains extra attributes, you must specify them when defining the relationship:

기본적으로 `pivot` 객체에는 모델 키만 존재할 것입니다. Pivot 테이블이 추가 속성들을 포함한다면 관계를 정의할 때 명시해야 합니다.

    return $this->belongsToMany('App\Models\Role')->withPivot('column1', 'column2');

If you want your pivot table to have automatically maintained `created_at` and `updated_at` timestamps, use the `withTimestamps` method on the relationship definition:

Pivot 테이블이 자동으로 유지되는 `created_at`와 `updated_at` 타임스탬프를 갖기 원한다면 관계 정의에 `withTimestamps` 메소드를 사용하면 됩니다.

    return $this->belongsToMany('App\Models\Role')->withTimestamps();

#### Customizing The `pivot` Attribute Name
#### `pivot` 속성의 이름 커스터마이징 하기

As noted earlier, attributes from the intermediate table may be accessed on models using the `pivot` attribute. However, you are free to customize the name of this attribute to better reflect its purpose within your application.

앞서 이야기 한것처럼, 모델에서 `pivot` 속성을 사용하여 중간 테이블의 속성에 엑세스 할 수 있습니다. 그렇지만 애플리케이션에서 용도를 보다 명확하게 표현할 수 있도록 속성의 이름을 자유롭게 커스터마이징 할 수 있습니다.

For example, if your application contains users that may subscribe to podcasts, you probably have a many-to-many relationship between users and podcasts. If this is the case, you may wish to rename your intermediate table accessor to `subscription` instead of `pivot`. This can be done using the `as` method when defining the relationship:

예를 들어, 애플리케이션에서 팟캐스트를 등록할 수 있는 사용자를 가지는 경우, 사용자와 팟캐스트는 다대다 관계를 형성할 수 있습니다. 이 경우 중간 테이블에 엑세스 하는 `pivot` 대신에 `subscription` 으로 이름을 변경할 수 있습니다. 이는 관계를 정의 할 때 `as` 메소드를 사용하여 지정하면 됩니다.

    return $this->belongsToMany('App\Models\Podcast')
                    ->as('subscription')
                    ->withTimestamps();

Once this is done, you may access the intermediate table data using the customized name:

이 후에는, 다음과 같이 커스터마이징한 이름을 사용하여 중간 테이블에 엑세스 할 수 있습니다.

    $users = User::with('podcasts')->get();

    foreach ($users->flatMap->podcasts as $podcast) {
        echo $podcast->subscription->created_at;
    }

#### Filtering Relationships Via Intermediate Table Columns
#### 중간 테이블의 컬럼을 사용한 관계의 필터링

You can also filter the results returned by `belongsToMany` using the `wherePivot`, `wherePivotIn`, and `wherePivotNotIn` methods when defining the relationship:

관계를 정의할 때, `wherePivot`, `wherePivotIn`과 `wherePivotNotIn` 메소드를 사용하여 `belongsToMany`이 반환하는 결과를 필터링 할 수도 있습니다.

    return $this->belongsToMany('App\Models\Role')->wherePivot('approved', 1);

    return $this->belongsToMany('App\Models\Role')->wherePivotIn('priority', [1, 2]);

    return $this->belongsToMany('App\Models\Role')->wherePivotNotIn('priority', [1, 2]);

<a name="defining-custom-intermediate-table-models"></a>
### Defining Custom Intermediate Table Models
### 커스텀 중간 테이블 모델 정의하기

If you would like to define a custom model to represent the intermediate table of your relationship, you may call the `using` method when defining the relationship. Custom many-to-many pivot models should extend the `Illuminate\Database\Eloquent\Relations\Pivot` class while custom polymorphic many-to-many pivot models should extend the `Illuminate\Database\Eloquent\Relations\MorphPivot` class. For example, we may define a `Role` which uses a custom `RoleUser` pivot model:

관계의 중간 테이블을 표현하기 위해서 커스텀 모델을 정의하려면, 관계를 정의할 때 `using` 메소드를 호출하면 됩니다. 커스텀 다대다 피벗 모델은 `Illuminate\Database\Eloquent\Relations\Pivot` 클래스를 확장해야하며 커스텀 다형성 다대다 피벗 모델은 `Illuminate\Database\Eloquent\Relations\MorphPivot` 클래스를 확장해야합니다. 예를 들어, 커스텀 `RoleUser` 피벗 모델을 사용하는 `Role` 을 정의할 수 있습니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class Role extends Model
    {
        /**
         * The users that belong to the role.
         */
        public function users()
        {
            return $this->belongsToMany('App\Models\User')->using('App\Models\RoleUser');
        }
    }

When defining the `RoleUser` model, we will extend the `Pivot` class:

`RoleUser` 모델을 정의할 때에는 `Pivot` 클래스를 상속합니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Relations\Pivot;

    class RoleUser extends Pivot
    {
        //
    }

You can combine `using` and `withPivot` in order to retrieve columns from the intermediate table. For example, you may retrieve the `created_by` and `updated_by` columns from the `RoleUser` pivot table by passing the column names to the `withPivot` method:

중간 테이블에서 열을 검색하기 위해 `using` 과 `withPivot` 을 결합 할 수 있습니다. 예를 들어, 컬럼 이름을 `withPivot` 메소드에 전달함으로써 `created_by` 와 `updated_by` 컬럼을 `RoleUser` 피벗 테이블에서 검색 할 수 있습니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class Role extends Model
    {
        /**
         * The users that belong to the role.
         */
        public function users()
        {
            return $this->belongsToMany('App\Models\User')
                            ->using('App\Models\RoleUser')
                            ->withPivot([
                                'created_by',
                                'updated_by',
                            ]);
        }
    }

> **Note:** Pivot models may not use the `SoftDeletes` trait. If you need to soft delete pivot records consider converting your pivot model to an actual Eloquent model.

> **Note:** 피벗 모델은 `SoftDeletes` 특성을 사용할 수 없습니다. 피벗 레코드를 소프트 삭제해야하는 경우 피벗 모델을 실제 Eloquent 모델로 변환하는 것이 좋습니다.

#### Custom Pivot Models And Incrementing IDs
#### 커스텀 피벗 모델 및 Incrementing IDs

If you have defined a many-to-many relationship that uses a custom pivot model, and that pivot model has an auto-incrementing primary key, you should ensure your custom pivot model class defines an `incrementing` property that is set to `true`.

커스텀 피벗 모델을 사용하는 다대다 관계를 정의하고 피벗 모델에 auto-incrementing 기본 키가 있을 경우 커스텀 피벗 모델 클래스의 `incrementing` 속성이 `true`로 되어 있는지 확인해야 합니다.

    /**
     * Indicates if the IDs are auto-incrementing.
     *
     * @var bool
     */
    public $incrementing = true;

<a name="has-one-through"></a>
### Has One Through
### 연결을 통한 단일 관계

The "has-one-through" relationship links models through a single intermediate relation.

"연결을 통한" 관계는 하나의 중간 테이블을 통해 연결합니다.

For example, in a vehicle repair shop application, each `Mechanic` may have one `Car`, and each `Car` may have one `Owner`. While the `Mechanic` and the `Owner` have no direct connection, the `Mechanic` can access the `Owner` _through_ the `Car` itself. Let's look at the tables necessary to define this relationship:

예를 들어, 자동차 수리점 애플리케이션에서 각 `Mechanic`은 하나의 `Car`를 가질 수 있고, 각 `Car`는 하나의 `Owner`를 가질 수 있습니다. `Mechanic`과 `Owner`는 직접적인 연결이 없지만 `Mechanic`은 `Car` _를 통해_ `Owner`에 접근 할 수 있습니다. 이 관계를 정의하는 데 필요한 테이블을 살펴 보겠습니다.

    mechanics
        id - integer
        name - string

    cars
        id - integer
        model - string
        mechanic_id - integer

    owners
        id - integer
        name - string
        car_id - integer

Now that we have examined the table structure for the relationship, let's define the relationship on the `Mechanic` model:

관계에 대한 테이블 구조를 확인해봤으니 이제 `Mechanic` 모델에서 관계를 정의 해 보겠습니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class Mechanic extends Model
    {
        /**
         * Get the car's owner.
         */
        public function carOwner()
        {
            return $this->hasOneThrough('App\Models\Owner', 'App\Models\Car');
        }
    }

The first argument passed to the `hasOneThrough` method is the name of the final model we wish to access, while the second argument is the name of the intermediate model.

`hasOneThrough` 메소드로 전달되는 첫번째 인자는 접근하고자 하는 최종 모델의 이름이며 두번째 인자는 중간 모델의 이름입니다.

Typical Eloquent foreign key conventions will be used when performing the relationship's queries. If you would like to customize the keys of the relationship, you may pass them as the third and fourth arguments to the `hasOneThrough` method. The third argument is the name of the foreign key on the intermediate model. The fourth argument is the name of the foreign key on the final model. The fifth argument is the local key, while the sixth argument is the local key of the intermediate model:

관계 쿼리를 수행할 때 일반적인 Eloquent 외래 키 규칙이 사용됩니다. 관계 키를 커스텀하러면 세번째 및 네번째 인자로 `hasOneThrough` 메소드에 전달 할 수 있습니다. 세번째 인자는 중간 모델의 외래 키입니다. 네번째 인자는 최종 모델의 외래 키입니다. 다섯번째 인자는 로컬 키며, 여섯번째 인자는 중간 모델의 로컬 키입니다.

    class Mechanic extends Model
    {
        /**
         * Get the car's owner.
         */
        public function carOwner()
        {
            return $this->hasOneThrough(
                'App\Models\Owner',
                'App\Models\Car',
                'mechanic_id', // Foreign key on cars table...
                'car_id', // Foreign key on owners table...
                'id', // Local key on mechanics table...
                'id' // Local key on cars table...
            );
        }
    }

<a name="has-many-through"></a>
### Has Many Through
### 연결을 통한 다수를 가지는 관계 정의하기

The "has-many-through" relationship provides a convenient shortcut for accessing distant relations via an intermediate relation. For example, a `Country` model might have many `Post` models through an intermediate `User` model. In this example, you could easily gather all blog posts for a given country. Let's look at the tables required to define this relationship:

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

Though `posts` does not contain a `country_id` column, the `hasManyThrough` relation provides access to a country's posts via `$country->posts`. To perform this query, Eloquent inspects the `country_id` on the intermediate `users` table. After finding the matching user IDs, they are used to query the `posts` table.

`posts`는 `country_id` 컬럼을 포함하고 있지 않지만 `hasManyThrough` 관계는 `$country->posts`를 통해 컨트리에 대한 접근을 제공합니다. 이 쿼리를 수행하기 위해 Eloquent는 중간 `users` 테이블의 `country_id`를 검사합니다. 일치하는 사용자 ID를 찾은 후에는 `posts` 테이블을 쿼리하는데 사용합니다.

Now that we have examined the table structure for the relationship, let's define it on the `Country` model:

relationship-관계를 위한 테이블 구조를 살펴보았으니 이제 `Country` 모델에 정의해보도록 합시다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class Country extends Model
    {
        /**
         * Get all of the posts for the country.
         */
        public function posts()
        {
            return $this->hasManyThrough('App\Models\Post', 'App\Models\User');
        }
    }

The first argument passed to the `hasManyThrough` method is the name of the final model we wish to access, while the second argument is the name of the intermediate model.

`hasManyThrough` 메소드로 전달되는 첫번째 인자는 접근하고자 하는 최종 모델의 이름이며 두번째 인자는 중간 모델의 이름입니다.

Typical Eloquent foreign key conventions will be used when performing the relationship's queries. If you would like to customize the keys of the relationship, you may pass them as the third and fourth arguments to the `hasManyThrough` method. The third argument is the name of the foreign key on the intermediate model. The fourth argument is the name of the foreign key on the final model. The fifth argument is the local key, while the sixth argument is the local key of the intermediate model:

관계의 쿼리를 수행할 때 전형적인 Eloquent 외래 키 관례들이 사용될 것입니다. 관계의 키를 사용자가 정의하고 싶다면 세번째와 네번재 인자로 `hasManyThrough` 메소드에 전달할 수 있습니다. 세번째 인자는 중간 모델의 외래 키 이름입니다. 네번째 인자는 최종 모델의 외래 키 이름입니다. 다섯번째 키는 로컬 키이고, 여섯번째 키는 중간 모델 로컬 키입니다.

    class Country extends Model
    {
        public function posts()
        {
            return $this->hasManyThrough(
                'App\Models\Post',
                'App\Models\User',
                'country_id', // Foreign key on users table...
                'user_id', // Foreign key on posts table...
                'id', // Local key on countries table...
                'id' // Local key on users table...
            );
        }
    }

<a name="polymorphic-relationships"></a>
## Polymorphic Relationships
## 다형성 관계

A polymorphic relationship allows the target model to belong to more than one type of model using a single association.

다형성 관계는 대상 모델이 하나 이상의 타입의 모델에 소속될 수 있도록 해줍니다.

<a name="one-to-one-polymorphic-relations"></a>
### One To One (Polymorphic)
### 1:1(일대일) (다형성)

#### Table Structure
#### 테이블 구조

A one-to-one polymorphic relation is similar to a simple one-to-one relation; however, the target model can belong to more than one type of model on a single association. For example, a blog `Post` and a `User` may share a polymorphic relation to an `Image` model. Using a one-to-one polymorphic relation allows you to have a single list of unique images that are used for both blog posts and user accounts. First, let's examine the table structure:

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

Take note of the `imageable_id` and `imageable_type` columns on the `images` table. The `imageable_id` column will contain the ID value of the post or user, while the `imageable_type` column will contain the class name of the parent model. The `imageable_type` column is used by Eloquent to determine which "type" of parent model to return when accessing the `imageable` relation.

`images` 테이블에서 `imageable_id` 와 `imageable_type` 컬럼을 확인하십시오. `imageable_id` 컬럼은 포스트 또는 사용자의 ID 값이 저장되고, `imageable_type` 컬럼에는 상위 모델 클래스의 이름이 저장됩니다. `imageable_type` 컬럼은 Eloquent 가 `imageable` 관계에서 값을 반환할때 어떠한 "타입"과 연결해야 하는지 결정하는데 사용됩니다.

#### Model Structure
#### 모델 구조

Next, let's examine the model definitions needed to build this relationship:

다음으로, 이러한 관계를 형성하기 위해서 모델에서 필요한 정의사항을 살펴보겠습니다.

    <?php

    namespace App\Models;

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
            return $this->morphOne('App\Models\Image', 'imageable');
        }
    }

    class User extends Model
    {
        /**
         * Get the user's image.
         */
        public function image()
        {
            return $this->morphOne('App\Models\Image', 'imageable');
        }
    }

#### Retrieving The Relationship
#### 관계 조회하기

Once your database table and models are defined, you may access the relationships via your models. For example, to retrieve the image for a post, we can use the `image` dynamic property:

데이터베이스 테이블과 모델을 정의하고나면, 모델에서 관계 모델에 엑세스 할 수 있습니다. 예를들어 포스트의 이미지를 조회한다면, `image` 라는 동적 속성을 사용하면 됩니다.

    $post = App\Models\Post::find(1);

    $image = $post->image;

You may also retrieve the parent from the polymorphic model by accessing the name of the method that performs the call to `morphTo`. In our case, that is the `imageable` method on the `Image` model. So, we will access that method as a dynamic property:

`morphTo` 에 대한 호출을 실행하는 메소드의 이름에 엑세스해서 다형성 모델의 상위 모델을 조회 할 수도 있습니다. 이 경우에서는, `Image` 모델의 `imageable` 메소드가 됩니다. 따라서 다음과 같이 동적 속성을 사용해서 메소드에 접근합니다.

    $image = App\Models\Image::find(1);

    $imageable = $image->imageable;

The `imageable` relation on the `Image` model will return either a `Post` or `User` instance, depending on which type of model owns the image. If you need to specify custom `type` and `id` columns for the `morphTo` relation, always ensure you pass the relationship name (which should exactly match the method name) as the first parameter:

`Image` 모델의 `imageable` 관계는 이미지를 소유한 모델의 타입에 따라서 `Post` 또는 `User` 인스턴스를 반환합니다. `morphTo` 관계에 대해 `type` 및 `id` 열을 커스터마이징 해야하는 경우 항상 첫 번째 매개 변수로 관계 이름 (메서드 이름과 정확히 일치해야 함)을 전달해야합니다.

    /**
     * Get the model that the image belongs to.
     */
    public function imageable()
    {
        return $this->morphTo(__FUNCTION__, 'imageable_type', 'imageable_id');
    }

<a name="one-to-many-polymorphic-relations"></a>
### One To Many (Polymorphic)
### 1:*(일대다) (다형성)

#### Table Structure
#### 테이블 구조

A one-to-many polymorphic relation is similar to a simple one-to-many relation; however, the target model can belong to more than one type of model on a single association. For example, imagine users of your application can "comment" on both posts and videos. Using polymorphic relationships, you may use a single `comments` table for both of these scenarios. First, let's examine the table structure required to build this relationship:

일대다 다형성 관계는 기본적인 일대다 관계와 비슷합니다. 다만, 대상 모델은 하나의 연결에서 하나 이상의 모델에 소속될 수 있습니다. 예를 들어 애플리케이션의 사용자가 게시글과 비디오 둘다 "댓글"를 달 수 있다고 생각해 보겠습니다. 다형성 관계를 이용하면 이 두 시나리오 모두 지원하는 하나의 `comments` 테이블을 사용할 수 있습니다. 먼저 이 관계를 구성하기 위해 필요한 테이블 구조를 살펴보겠습니다.

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

#### Model Structure
#### 모델 구조

Next, let's examine the model definitions needed to build this relationship:

다음으로 이 관계를 형성하기 위해 필요한 모델 정의들을 살펴보도록 하겠습니다.

    <?php

    namespace App\Models;

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
            return $this->morphMany('App\Models\Comment', 'commentable');
        }
    }

    class Video extends Model
    {
        /**
         * Get all of the video's comments.
         */
        public function comments()
        {
            return $this->morphMany('App\Models\Comment', 'commentable');
        }
    }

#### Retrieving The Relationship
#### 관계-Relationship 조회하기

Once your database table and models are defined, you may access the relationships via your models. For example, to access all of the comments for a post, we can use the `comments` dynamic property:

데이터베이스 테이블과 모델이 정의되면 관계-Relationship를 통해 모델에 접근 할 수 있습니다. 예를 들어, 게시물의 모든 댓글에 접근하기 위해 `comments` 동적 속성을 사용할 수 있습니다.

    $post = App\Models\Post::find(1);

    foreach ($post->comments as $comment) {
        //
    }

You may also retrieve the owner of a polymorphic relation from the polymorphic model by accessing the name of the method that performs the call to `morphTo`. In our case, that is the `commentable` method on the `Comment` model. So, we will access that method as a dynamic property:

`morphTo`에 대한 호출을 수행하는 메소드의 이름에 액세스하여 다형성 모델에서 다형성 관계의 소유자를 검색 할 수도 있습니다. 지금의 상황이라면 `Comment` 모델의 `commentable` 메소드입니다. 따라서 해당 메소드를 동적 속성으로 액세스합니다.

    $comment = App\Models\Comment::find(1);

    $commentable = $comment->commentable;

The `commentable` relation on the `Comment` model will return either a `Post` or `Video` instance, depending on which type of model owns the comment.

`Comment` 모델의 `commentable` 관계는 댓글을 소유한 모델 타입에 따라 `Post` 또는 `Video` 인스턴스를 반환합니다.

(역자주: 말이 어렵지만 위의 섹션에서 모델을 정의한 코드만 보면 `$comment->commentable()`만 정의해뒀지만 `$comment->commentable`로 사용할 경우 라라벨이 자동으로 해당 댓글의 부모가 뭔지 찾아서 그에 맞는 객체를 가져와 준다는 의미입니다)

<a name="many-to-many-polymorphic-relations"></a>
### Many To Many (Polymorphic)
### \*:*(다대다) (다형성)

#### Table Structure
#### 테이블 구조

Many-to-many polymorphic relations are slightly more complicated than `morphOne` and `morphMany` relationships. For example, a blog `Post` and `Video` model could share a polymorphic relation to a `Tag` model. Using a many-to-many polymorphic relation allows you to have a single list of unique tags that are shared across blog posts and videos. First, let's examine the table structure:

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

#### Model Structure
#### 모델 구조

Next, we're ready to define the relationships on the model. The `Post` and `Video` models will both have a `tags` method that calls the `morphToMany` method on the base Eloquent class:

다음으로, 모델의 관계를 정의해보겠습니다. `Post` 및 `Video` 모델에는 기본 Eloquent 클래스에서 `morphToMany` 메소드를 호출하는 `tags` 메소드를 추가합니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class Post extends Model
    {
        /**
         * Get all of the tags for the post.
         */
        public function tags()
        {
            return $this->morphToMany('App\Models\Tag', 'taggable');
        }
    }

#### Defining The Inverse Of The Relationship
#### 역관계 정의하기

Next, on the `Tag` model, you should define a method for each of its related models. So, for this example, we will define a `posts` method and a `videos` method:

다음으로, `Tag` 모델에서 관련된 각 모델들을 위해 메소드를 정의해야 합니다. 이 예제에서는 `posts` 메소드와 `videos` 메소드를 정의하겠습니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class Tag extends Model
    {
        /**
         * Get all of the posts that are assigned this tag.
         */
        public function posts()
        {
            return $this->morphedByMany('App\Models\Post', 'taggable');
        }

        /**
         * Get all of the videos that are assigned this tag.
         */
        public function videos()
        {
            return $this->morphedByMany('App\Models\Video', 'taggable');
        }
    }

#### Retrieving The Relationship
#### 관계 조회하기

Once your database table and models are defined, you may access the relationships via your models. For example, to access all of the tags for a post, you can use the `tags` dynamic property:

데이터베이스 테이블과 모델들이 정의되었다면 모델을 통해 관계에 접근할 수 있습니다. 예를 들어, 어떤 게시물의 모든 태그에 접근하려면, `tags` 동적 속성을 사용하면 됩니다.

    $post = App\Models\Post::find(1);

    foreach ($post->tags as $tag) {
        //
    }

You may also retrieve the owner of a polymorphic relation from the polymorphic model by accessing the name of the method that performs the call to `morphedByMany`. In our case, that is the `posts` or `videos` methods on the `Tag` model. So, you will access those methods as dynamic properties:

또한 `morphedByMany`의 호출을 수행하는 메소드의 이름에 접근하여 다형성 모델에서 다형성 관계의 소유자를 조회할 수 있습니다. 이 예에서는 `Tag` 모델에 사용되는`posts`나 `videos` 메소드를 말합니다. 따라서 이 메소드들은 동적 속성으로 접근할 것입니다.

    $tag = App\Models\Tag::find(1);

    foreach ($tag->videos as $video) {
        //
    }

<a name="custom-polymorphic-types"></a>
### Custom Polymorphic Types
### 커스텀-사용자 정의 다형성 타입

By default, Laravel will use the fully qualified class name to store the type of the related model. For instance, given the one-to-many example above where a `Comment` may belong to a `Post` or a `Video`, the default `commentable_type` would be either `App\Models\Post` or `App\Models\Video`, respectively. However, you may wish to decouple your database from your application's internal structure. In that case, you may define a "morph map" to instruct Eloquent to use a custom name for each model instead of the class name:

기본적으로, 라라벨은 관련된 모델의 유형을 저장하기 위해서 전체 클래스 이름을 사용합니다. 예를 들어 위의 일대일 관계 예제에서 `Comment` 는 하나의 `Post` 또는 하나의 `Video` 에 지정되고, 기본적으로 `commentable_type` 의 각각 `App\Models\Post` 또는 `App\Models\Video` 이 될 수 있습니다. 그렇지만, 여러분은 데이터베이스와 애플리케이션의 내부 구조를 분리하고자 할 수 있습니다. 이 경우 여러분은 관계 설정을 위한 "morph map"을 정의하고 클래스 이름 대신 사용할 각각의 모델과 관련 있는 고유한 이름을 Eloquent 에 지시할 수 있습니다.

    use Illuminate\Database\Eloquent\Relations\Relation;

    Relation::morphMap([
        'posts' => 'App\Models\Post',
        'videos' => 'App\Models\Video',
    ]);

You may register the `morphMap` in the `boot` function of your `AppServiceProvider` or create a separate service provider if you wish.

여러분은 `AppServiceProvider` 의 `boot` 안에서 `morphMap` 를 등록 하거나, 원한다면 분리된 서비스 프로바이더를 만들 수도 있을 것입니다.

> {note} When adding a "morph map" to your existing application, every morphable `*_type` column value in your database that still contains a fully-qualified class will need to be converted to its "map" name.

> {note} 기존 애플리케이션에 "morph map"을 추가 할 때 정규화 된 클래스를 포함하고 있는 데이터베이스의 모든 변형 가능한 `*_type` 컬럼 값을 "map"이름으로 변환해야합니다.

You may determine the morph alias of a given model at runtime using the `getMorphClass` method. Conversely, you may determine the fully-qualified class name associated with a morph alias using the `Relation::getMorphedModel` method:

`getMorphClass` 메소드를 사용하여 런타임에 주어진 모델의 morph 별칭을 결정할 수 있습니다. 반대로 `Relation::getMorphedModel` 메소드를 사용하여 morph 별칭과 관련된 정규화 된 클래스 이름을 결정할 수 있습니다.

    use Illuminate\Database\Eloquent\Relations\Relation;

    $alias = $post->getMorphClass();

    $class = Relation::getMorphedModel($alias);

<a name="dynamic-relationships"></a>
### Dynamic Relationships
### 동적 관계

You may use the `resolveRelationUsing` method to define relations between Eloquent models at runtime. While not typically recommended for normal application development, this may occasionally be useful when developing Laravel packages:

런타임에 Eloquent 모델 간의 관계를 정의하기 위해 `resolveRelationUsing` 메소드를 사용할 수 있습니다. 일반적인 애플리케이션 개발에는 권장되지 않지만 라라벨 패키지를 개발할 때 유용 할 수 있습니다.

    use App\Models\Order;
    use App\Models\Customer;

    Order::resolveRelationUsing('customer', function ($orderModel) {
        return $orderModel->belongsTo(Customer::class, 'customer_id');
    });

> {note} When defining dynamic relationships, always provide explicit key name arguments to the Eloquent relationship methods.

> {note} 동적 관계를 정의 할 때 항상 Eloquent 관계 메서드에 명시적인 키 이름 인수를 제공하십시오.

<a name="querying-relations"></a>
## Querying Relations
## 관계 쿼리 질의하기

Since all types of Eloquent relationships are defined via methods, you may call those methods to obtain an instance of the relationship without actually executing the relationship queries. In addition, all types of Eloquent relationships also serve as [query builders](/docs/{{version}}/queries), allowing you to continue to chain constraints onto the relationship query before finally executing the SQL against your database.

모든 Eloquent 관계는 메소드를 통해 정의되기 때문에, 관계 쿼리를 실제로 실행하는 대신에 이 메소드을 호출하여 관계 인스턴스를 가져올 수 있습니다. 모든 종류의 Eloquent 모델들은 또한 [쿼리 빌더](/docs/{{version}}/queries)의 역할을 하기 때문에 데이터베이스에 최종적으로 SQL을 실행하기 전에 관계 쿼리에 제한(where 구문)을 체이닝(호출->호출->호출 형태의 질의) 할 수 있도록 해줍니다.

For example, imagine a blog system in which a `User` model has many associated `Post` models:

예를 들어, `User` 모델이 다수의 `Post` 모델에 연관되어 있는 블로그 시스템을 상상해 봅시다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * Get all of the posts for the user.
         */
        public function posts()
        {
            return $this->hasMany('App\Models\Post');
        }
    }

You may query the `posts` relationship and add additional constraints to the relationship like so:

다음과 같이 `posts` 관계들을 쿼리하고 관계에 제한들을 추가할 수 있습니다.

    $user = App\Models\User::find(1);

    $user->posts()->where('active', 1)->get();

You are able to use any of the [query builder](/docs/{{version}}/queries) methods on the relationship, so be sure to explore the query builder documentation to learn about all of the methods that are available to you.

관계에 대해서 어떠한 [쿼리 빌더](/docs/{{version}}/queries) 메소드도 사용 가능하기 때문에, 가능한 메소드들에 대해서 확인하려면 쿼리 빌더에 대한 문서를 확인하시기 바랍니다.

#### Chaining `orWhere` Clauses After Relationships
#### 관계 정립 후에 `orWhere` 절 연결하기

As demonstrated in the example above, you are free to add additional constraints to relationships when querying them. However, use caution when chaining `orWhere` clauses onto a relationship, as the `orWhere` clauses will be logically grouped at the same level as the relationship constraint:

위 예에서 설명한 것처럼 쿼리 할 때 관계에 제약 조건을 추가 할 수 있습니다. 그러나 `orWhere` 절이 관계 제약 조건과 같은 수준으로 논리적으로 그룹화되므로 `orWhere` 절을 관계에 연결하는 경우 주의해야합니다.

    $user->posts()
            ->where('active', 1)
            ->orWhere('votes', '>=', 100)
            ->get();

    // select * from posts
    // where user_id = ? and active = 1 or votes >= 100

In most situations, you likely intend to use [constraint groups](/docs/{{version}}/queries#parameter-grouping) to logically group the conditional checks between parentheses:

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
### Relationship Methods Vs. Dynamic Properties
### 관계 메소드 Vs. 동적 속성

If you do not need to add additional constraints to an Eloquent relationship query, you may access the relationship as if it were a property. For example, continuing to use our `User` and `Post` example models, we may access all of a user's posts like so:

Eloquent 관계 쿼리에 제한을 추가할 필요가 없다면 속성처럼 관계에 접근할 수 있습니다. 예를 들어, `User`와 `Post` 예시 모델들을 계속해서 사용하면 다음과 같이 사용자의 모든 게시물에 접근할 수 있습니다.

    $user = App\Models\User::find(1);

    foreach ($user->posts as $post) {
        //
    }

Dynamic properties are "lazy loading", meaning they will only load their relationship data when you actually access them. Because of this, developers often use [eager loading](#eager-loading) to pre-load relationships they know will be accessed after loading the model. Eager loading provides a significant reduction in SQL queries that must be executed to load a model's relations.

동적 속성은 "지연 로딩"으로, 실제로 엑세스를 해야만 관계 데이터를 로드합니다. 그렇게 때문에 개발자들은 종종 모델을 로드한 뒤 접근해야할 관계들을 미리 로드해주는 [eager 로딩](#eager-loading)을 사용합니다. Eager 로딩은 모델의 관계를 로드하기 위해 실행되어야 할 SQL 쿼리들을 유의미하게 줄여줍니다.

<a name="querying-relationship-existence"></a>
### Querying Relationship Existence
### 관계의 존재 여부 쿼리 질의하기

When accessing the records for a model, you may wish to limit your results based on the existence of a relationship. For example, imagine you want to retrieve all blog posts that have at least one comment. To do so, you may pass the name of the relationship to the `has` and `orHas` methods:

모델의 기록에 접근할 때, 관계의 존재 여부에 따라 결과를 제한하기 원할 수 있습니다. 예를 들어 하나 이상의 댓글을 가진 모든 블로그 게시물을 조회하려고 한다고 생각해 봅시다. 이를 위해서 `has` 또는 `orHas` 메소드로 관계의 이름을 전달할 수 있습니다.

    // Retrieve all posts that have at least one comment...
    $posts = App\Models\Post::has('comments')->get();

You may also specify an operator and count to further customize the query:

또한 메소드에서 사용할 수 있는 연산자와 카운트 개수를 지정하여 쿼리를 계속하여 커스터마이즈할 수 있습니다.

    // Retrieve all posts that have three or more comments...
    $posts = App\Models\Post::has('comments', '>=', 3)->get();

Nested `has` statements may also be constructed using "dot" notation. For example, you may retrieve all posts that have at least one comment and vote:

중첩된 `has` 구문(statement)은 "점(.)" 표기를 사용하여 구성될 수 있습니다. 예를 들어, 최소한 하나의 댓글과 좋아요(vote)를 가진 모든 게시물을 조회할 수 있습니다.

    // Retrieve posts that have at least one comment with votes...
    $posts = App\Models\Post::has('comments.votes')->get();

If you need even more power, you may use the `whereHas` and `orWhereHas` methods to put "where" conditions on your `has` queries. These methods allow you to add customized constraints to a relationship constraint, such as checking the content of a comment:

더 많은 권한이 필요하다면 `whereHas`와 `orWhereHas` 메소드를 사용하여 `has` 쿼리에 "where" 조건을 추가할 수 있습니다. 이 메소드들은 관계 제한에 댓글 컨텐츠 확인과 같은 사용자 정의된 제한들을 추가할 수 있게 해줍니다.

    use Illuminate\Database\Eloquent\Builder;

    // Retrieve posts with at least one comment containing words like foo%...
    $posts = App\Models\Post::whereHas('comments', function (Builder $query) {
        $query->where('content', 'like', 'foo%');
    })->get();

    // Retrieve posts with at least ten comments containing words like foo%...
    $posts = App\Models\Post::whereHas('comments', function (Builder $query) {
        $query->where('content', 'like', 'foo%');
    }, '>=', 10)->get();

<a name="querying-relationship-absence"></a>
### Querying Relationship Absence
### 관계된 모델이 존재하지 않는 것을 확인하며 질의하기

When accessing the records for a model, you may wish to limit your results based on the absence of a relationship. For example, imagine you want to retrieve all blog posts that **don't** have any comments. To do so, you may pass the name of the relationship to the `doesntHave` and `orDoesntHave` methods:

모델의 레코드에 엑세스 할 때, 관계된 모델이 존재하지 않는 것에 따라서 결과를 제한하고자 할 수 있습니다. 예를 들어 코멘트를 가지고 있지 **않은** 모든 블로그 포스트를 조회하는 경우를 생각해 보겠습니다. 이렇게 하기 위해서는 `doesntHave` 또는 `orDoesntHave` 메소드에 정의한 관계의 이름을 전달하면됩니다.

    $posts = App\Models\Post::doesntHave('comments')->get();

If you need even more power, you may use the `whereDoesntHave` and `orWhereDoesntHave` methods to put "where" conditions on your `doesntHave` queries. These methods allows you to add customized constraints to a relationship constraint, such as checking the content of a comment:

더 강력한 기능을 원한다면, `doesntHave` 쿼리에 "where" 조건을 붙여서, `whereDoesntHave` 와 `orWhereDoesntHave` 메소드를 사용할 수 있습니다. 이 메소드는 코멘트의 내용을 확인하는 것과 같이 관계 제약에 커스터마이징된 제약을 추가해준다.

    use Illuminate\Database\Eloquent\Builder;

    $posts = App\Models\Post::whereDoesntHave('comments', function (Builder $query) {
        $query->where('content', 'like', 'foo%');
    })->get();

You may use "dot" notation to execute a query against a nested relationship. For example, the following query will retrieve all posts that do not have comments and posts that have comments from authors that are not banned:

중첩된 관계에 대해서는 "점(.)" 표기법을 사용하여 쿼리를 질의할 수 있습니다. 예를 들어, 다음 쿼리는 댓글이 없는 모든 게시물과 차단되지 않은 작성자의 댓글이 있는 게시물을 검색합니다.

    use Illuminate\Database\Eloquent\Builder;

    $posts = App\Models\Post::whereDoesntHave('comments.author', function (Builder $query) {
        $query->where('banned', 0);
    })->get();

<a name="querying-polymorphic-relationships"></a>
### Querying Polymorphic Relationships
### 다형성 관계 쿼리

To query the existence of `MorphTo` relationships, you may use the `whereHasMorph` method and its corresponding methods:

`MorphTo` 관계의 존재를 쿼리하기 위해서 `whereHasMorph` 메소드와 그것에 상응하는 메소드를 사용할 수 있습니다.

    use Illuminate\Database\Eloquent\Builder;

    // Retrieve comments associated to posts or videos with a title like foo%...
    $comments = App\Models\Comment::whereHasMorph(
        'commentable',
        ['App\Models\Post', 'App\Models\Video'],
        function (Builder $query) {
            $query->where('title', 'like', 'foo%');
        }
    )->get();

    // Retrieve comments associated to posts with a title not like foo%...
    $comments = App\Models\Comment::whereDoesntHaveMorph(
        'commentable',
        'App\Models\Post',
        function (Builder $query) {
            $query->where('title', 'like', 'foo%');
        }
    )->get();

You may use the `$type` parameter to add different constraints depending on the related model:

`$type` 파라미터를 사용하여 관련 모델에 따라 다른 제약 조건을 추가 할 수 있습니다.

    use Illuminate\Database\Eloquent\Builder;

    $comments = App\Models\Comment::whereHasMorph(
        'commentable',
        ['App\Models\Post', 'App\Models\Video'],
        function (Builder $query, $type) {
            $query->where('title', 'like', 'foo%');

            if ($type === 'App\Models\Post') {
                $query->orWhere('content', 'like', 'foo%');
            }
        }
    )->get();

Instead of passing an array of possible polymorphic models, you may provide `*` as a wildcard and let Laravel retrieve all the possible polymorphic types from the database. Laravel will execute an additional query in order to perform this operation:

가능한 다형성 모델 배열을 전달하는 대신에 `*`를 와일드 카드로 제공하고 Laravel로 하여금 가능한 모든 다형성 타입을 데이터베이스에서 검색하도록 할 수 있습니다. Laravel은 이 작업을 수행하기 위해 추가 쿼리를 실행합니다.

    use Illuminate\Database\Eloquent\Builder;

    $comments = App\Models\Comment::whereHasMorph('commentable', '*', function (Builder $query) {
        $query->where('title', 'like', 'foo%');
    })->get();

<a name="counting-related-models"></a>
### Counting Related Models
### 연관된 모델의 개수 확인하기-카운팅

If you want to count the number of results from a relationship without actually loading them you may use the `withCount` method, which will place a `{relation}_count` column on your resulting models. For example:

관계 결과의 개수를 실제 레코드를 로딩하지 않고 알고자 한다면, `withCount` 메소드를 사용하여 건수는 결과 모델의 `{관계}_count` 컬럼에 위치하도록 할 수 있습니다. 예를 들어:

    $posts = App\Models\Post::withCount('comments')->get();

    foreach ($posts as $post) {
        echo $post->comments_count;
    }

You may add the "counts" for multiple relations as well as add constraints to the queries:

다수의 관계에 대해서도 쿼리에 제약을 추가하여 "개수"를 조회할 수 있습니다.

    use Illuminate\Database\Eloquent\Builder;

    $posts = App\Models\Post::withCount(['votes', 'comments' => function (Builder $query) {
        $query->where('content', 'like', 'foo%');
    }])->get();

    echo $posts[0]->votes_count;
    echo $posts[0]->comments_count;

You may also alias the relationship count result, allowing multiple counts on the same relationship:

동일한 관계에 대하여 여러번 카운트를 수행하기 위해서 카운트 결과에 별칭(alias)를 부여할 수도 있습니다.

    use Illuminate\Database\Eloquent\Builder;

    $posts = App\Models\Post::withCount([
        'comments',
        'comments as pending_comments_count' => function (Builder $query) {
            $query->where('approved', false);
        },
    ])->get();

    echo $posts[0]->comments_count;

    echo $posts[0]->pending_comments_count;

If you're combining `withCount` with a `select` statement, ensure that you call `withCount` after the `select` method:

`withCount` 와 `select` 구문을 조합한다면, `select` 메소드 뒤에 `withCount` 호출하도록 구성하십시오.

    $posts = App\Models\Post::select(['title', 'body'])->withCount('comments')->get();

    echo $posts[0]->title;
    echo $posts[0]->body;
    echo $posts[0]->comments_count;

In addition, using the `loadCount` method, you may load a relationship count after the parent model has already been retrieved:

또한 `loadCount` 메소드를 사용하여 상위 모델이 이미 처리된 후에도 관계의 수를 로드 할 수 있습니다.

    $book = App\Models\Book::first();

    $book->loadCount('genres');

If you need to set additional query constraints on the eager loading query, you may pass an array keyed by the relationships you wish to load. The array values should be `Closure` instances which receive the query builder instance:

eager loading 쿼리에 쿼리 제약 조건을 추가 설정해야하는 경우 로드하려는 관계에 키에 배열을 전달할 수 있습니다. 배열의 값은 쿼리 빌더 인스턴스를 받는 `Closure` 인스턴스 여야합니다.

    $book->loadCount(['reviews' => function ($query) {
        $query->where('rating', 5);
    }])

<a name="counting-related-models-on-polymorphic-relationships"></a>
### Counting Related Models On Polymorphic Relationships
### 다형성 관계에서 관련된 모델 수량 확인하기

If you would like to eager load a `morphTo` relationship, as well as nested relationship counts on the various entities that may be returned by that relationship, you may use the `with` method in combination with the `morphTo` relationship's `morphWithCount` method.

`morphTo` 관계뿐만 아니라 해당 관계에 의해 반환 될 수 있는 다양한 엔터티의 중첩 된 관계의 숫자를 직접 로드하려면 `with`메서드내에서 `morphTo`메서드와 `morphWithCount` 메서드를 함께 사용하면 됩니다.

In this example, let's assume `Photo` and `Post` models may create `ActivityFeed` models. Additionally, let's assume that `Photo` models are associated with `Tag` models, and `Post` models are associated with `Comment` models.

이 예제에서는 `Photo` 및 `Post` 모델이 `ActivityFeed` 모델을 생성 할 수 있다고 가정합니다. 또한 `Photo`모델은 `Tag`모델과 연결되고 `Post`모델은 `Comment`모델과 연결되어 있다고 가정 해 보겠습니다.

Using these model definitions and relationships, we may retrieve `ActivityFeed` model instances and eager load all `parentable` models and their respective nested relationship counts:

이러한 모델 정의와 관계를 사용하여 `ActivityFeed` 모델 인스턴스를 검색하고 모든 `parentable` 모델과 각각의 중첩 된 관계 수를 즉시로드 할 수 있습니다.

    use Illuminate\Database\Eloquent\Relations\MorphTo;

    $activities = ActivityFeed::query()
        ->with(['parentable' => function (MorphTo $morphTo) {
            $morphTo->morphWithCount([
                Photo::class => ['tags'],
                Post::class => ['comments'],
            ]);
        }])->get();

In addition, you may use the `loadMorphCount` method to eager load all nested relationship counts on the various entities of the polymorphic relation if the `ActivityFeed` models have already been retrieved:

또한, `ActivityFeed` 모델이 이미 검색된 경우 `loadMorphCount` 메서드를 사용하여 다형성 관계의 다양한 엔티티에 대한 모든 중첩 관계 수를 즉시로드 할 수 있습니다.

    $activities = ActivityFeed::with('parentable')
        ->get()
        ->loadMorphCount('parentable', [
            Photo::class => ['tags'],
            Post::class => ['comments'],
        ]);

<a name="eager-loading"></a>
## Eager Loading
## Eager 로딩

When accessing Eloquent relationships as properties, the relationship data is "lazy loaded". This means the relationship data is not actually loaded until you first access the property. However, Eloquent can "eager load" relationships at the time you query the parent model. Eager loading alleviates the N + 1 query problem. To illustrate the N + 1 query problem, consider a `Book` model that is related to `Author`:

Eloquent 관계들을 속성으로 접근할 때 관계 데이터는 "지연 로드" 되어있습니다. 이는 속성에 엑세스 하기 전까지 관계 데이터가 실제로 로드되지 않는다는 것을 의미합니다. 하지만 Eloquent는 부모 모델을 쿼리할 때 관계를 "eager 로드"할 수도 있습니다. Eager 로딩은 N + 1 쿼리 문제를 해결 합니다. N + 1 쿼리 문제에 대한 예제를 들어보자면 `Author`에 연관된 `Book` 모델을 생각해볼 수 있습니다.

    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class Book extends Model
    {
        /**
         * Get the author that wrote the book.
         */
        public function author()
        {
            return $this->belongsTo('App\Models\Author');
        }
    }

Now, let's retrieve all books and their authors:

이제 모든 책과 그 저자들을 조회해봅시다.

    $books = App\Models\Book::all();

    foreach ($books as $book) {
        echo $book->author->name;
    }

This loop will execute 1 query to retrieve all of the books on the table, then another query for each book to retrieve the author. So, if we have 25 books, the code above would run 26 queries: 1 for the original book, and 25 additional queries to retrieve the author of each book.

이 반복문은 테이블에서 모든 책들을 조회하는 1 개의 쿼리를 실행하고, 각각의 책마다 저자를 조회하는 별개의 쿼리를 실행할 것입니다. 따라서, 만약 25개의 책이 있다면, 위의 코드는 원래 책을 위한 하나의 쿼리와 각 책의 저자를 조회하는 25개의 추가적인 쿼리, 총 26개의 쿼리를 실행하게 됩니다.

Thankfully, we can use eager loading to reduce this operation to just 2 queries. When querying, you may specify which relationships should be eager loaded using the `with` method:

다행히도, eager 로딩을 사용하면 이 작업을 2개의 쿼리로 줄일 수 있습니다. 쿼리를 실행할 때 `with` 메소드를 사용하여 어떤 관계가 eager 로드되어야 하는지 지정할 수 있습니다.

    $books = App\Models\Book::with('author')->get();

    foreach ($books as $book) {
        echo $book->author->name;
    }

For this operation, only two queries will be executed:

이 작업에서는 두 개의 쿼리만이 실행될 것입니다.

    select * from books

    select * from authors where id in (1, 2, 3, 4, 5, ...)

#### Eager Loading Multiple Relationships
#### 여러 관계에 대해서 Eager 로딩하기

Sometimes you may need to eager load several different relationships in a single operation. To do so, just pass additional arguments to the `with` method:

종종 하나의 작업에서 여러 개의 다른 관계들을 eager 로드해야 될 때가 있습니다. 이 경우 `with` 메소드에 추가 인자들을 전달하면 됩니다.

    $books = App\Models\Book::with(['author', 'publisher'])->get();

#### Nested Eager Loading
#### 중첩된 Eager 로딩하기

To eager load nested relationships, you may use "dot" syntax. For example, let's eager load all of the book's authors and all of the author's personal contacts in one Eloquent statement:

"점" 구문을 이용하면 중첩된 관계들을 eager 로드할 수 있습니다. 예를 들어, 하나의 Eloquent 구문(statement)에서 책의 모든 저자들과 저자들의 모든 연락처를 eager 로드해보겠습니다.

    $books = App\Models\Book::with('author.contacts')->get();

#### Nested Eager Loading `morphTo` Relationships
#### 중첩 된 `morphTo` 관계의 Eager Loading

If you would like to eager load a `morphTo` relationship, as well as nested relationships on the various entities that may be returned by that relationship, you may use the `with` method in combination with the `morphTo` relationship's `morphWith` method. To help illustrate this method, let's consider the following model:

`morphTo` 관계뿐만 아니라 그 관계에 의해 리턴 될 수 있는 다양한 엔티티들에 중첩된 관계를 eager 로드 하고 싶다면, `morph` 관계의 `morphWith` 메소드와 함께 `with` 메소드를 사용할 수 있습니다. 이 방법을 설명하기 위해 다음 모델을 고려해보겠습니다.

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

In this example, let's assume `Event`, `Photo`, and `Post` models may create `ActivityFeed` models. Additionally, let's assume that `Event` models belong to a `Calendar` model, `Photo` models are associated with `Tag` models, and `Post` models belong to an `Author` model.

이 예제에서 `Event`,`Photo`,`Post` 모델이 `ActivityFeed` 모델을 생성한다고 가정합시다. 또한 `Event`모델이 `Calendar`모델에 속하고, `Photo`모델이 `Tag`모델과 관련이 있고 `Post`모델이 `Author`모델에 속한다고 가정 해 보겠습니다.

Using these model definitions and relationships, we may retrieve `ActivityFeed` model instances and eager load all `parentable` models and their respective nested relationships:

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

#### Eager Loading Specific Columns
#### Eager 로딩에서 컬럼 지정하기

You may not always need every column from the relationships you are retrieving. For this reason, Eloquent allows you to specify which columns of the relationship you would like to retrieve:

조회하고자 하는 관계에서 항상 모든 컬럼이 필요한 것은 아닙니다. 이 경우, Eloquent 는 조회하고자 하는 관계에 컬럼을 지정할 수 있습니다.

    $books = App\Models\Book::with('author:id,name')->get();

> {note} When using this feature, you should always include the `id` column and any relevant foreign key columns in the list of columns you wish to retrieve.

> {note} 이 기능을 사용할 때에는, 조회하고자 하는 컬럼에 항상 `id` 컬럼과 관련 외래 키 컬럼이 포함되어 있어야 합니다.

#### Eager Loading By Default
#### 기본적으로 Eager Loading 하기

Sometimes you might want to always load some relationships when retrieving a model. To accomplish this, you may define a `$with` property on the model:

때로는 모델을 검색 할 때 항상 일부 관계를 로드하려고 할 수 있습니다. 이를 위해 모델에 `$with` 속성을 정의 할 수 있습니다.

    <?php

    namespace App\Models;

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
            return $this->belongsTo('App\Models\Author');
        }
    }

If you would like to remove an item from the `$with` property for a single query, you may use the `without` method:

단일 쿼리에 대한 `$with` 속성에서 항목을 제거하려면 `without` 메소드를 사용할 수 있습니다.

    $books = App\Models\Book::without('author')->get();

<a name="constraining-eager-loads"></a>
### Constraining Eager Loads
### Eager 로딩에서 조건을 통해 질의 제한하기

Sometimes you may wish to eager load a relationship, but also specify additional query conditions for the eager loading query. Here's an example:

때로는 관계를 eager 로드하면서 eager 로딩 쿼리에 추가적인 쿼리 제한들을 지정하고 싶을 수 있습니다. 다음은 그 예제입니다.

    $users = App\Models\User::with(['posts' => function ($query) {
        $query->where('title', 'like', '%first%');
    }])->get();

In this example, Eloquent will only eager load posts where the post's `title` column contains the word `first`. You may call other [query builder](/docs/{{version}}/queries) methods to further customize the eager loading operation:

이 예제에서 Eloquent는 게시물의 `title` 컬럼이 `first`라는 단어를 포함할 때만 게시물을 eager 로드할 것입니다. 다른 [쿼리 빌더](/docs/{{version}}/queries)메소드를 호출하여 계속하여서 eager 로딩 작업을 커스터마이즈할 수 있습니다.

    $users = App\Models\User::with(['posts' => function ($query) {
        $query->orderBy('created_at', 'desc');
    }])->get();

> {note} The `limit` and `take` query builder methods may not be used when constraining eager loads.

> {note} eager 로드를 제한 할 때 `limit`과 `take` 쿼리 빌더 메소드는 사용할 수 없습니다.

<a name="lazy-eager-loading"></a>
### Lazy Eager Loading
### 지연 Eager 로딩

Sometimes you may need to eager load a relationship after the parent model has already been retrieved. For example, this may be useful if you need to dynamically decide whether to load related models:

종종 부모 모델이 조회된 후에 관계를 eager 로드해야할 필요가 있을 수 있습니다. 예를 들자면, 연관된 모델들을 동적으로 결정해야할 때 유용하게 사용됩니다.

    $books = App\Models\Book::all();

    if ($someCondition) {
        $books->load('author', 'publisher');
    }

If you need to set additional query constraints on the eager loading query, you may pass an array keyed by the relationships you wish to load. The array values should be `Closure` instances which receive the query instance:

Eager 로딩 쿼리에 추가적인 쿼리 제한을 지정해야 할 경우, `load` 메소드에 로그하고자 하는 관계에 대한 키로 구성된 배열을 전달할 수 있습니다. 이 배열의 값은 쿼리 인스턴스를 인자로 받아들이는 `Closure`이어야만 합니다.

    $author->load(['books' => function ($query) {
        $query->orderBy('published_date', 'asc');
    }]);

To load a relationship only when it has not already been loaded, use the `loadMissing` method:

로딩되어 있지 않았을 때에만 관계 모델을 로딩하려면 `loadMissing` 메소드를 사용하면됩니다.

    public function format(Book $book)
    {
        $book->loadMissing('author');

        return [
            'name' => $book->name,
            'author' => $book->author->name,
        ];
    }

#### Nested Lazy Eager Loading & `morphTo`
#### 내포된 지연 Eager 로딩 & `morphTo`

If you would like to eager load a `morphTo` relationship, as well as nested relationships on the various entities that may be returned by that relationship, you may use the `loadMorph` method.

`morphTo` 관계 뿐만 아니라 해당 관계에 의해 반환될 수 있는 다양한 엔티티에서의 내포된 관계를 원하는 경우 `loadMorph` 메소드를 사용할 수 있습니다.

This method accepts the name of the `morphTo` relationship as its first argument, and an array of model / relationship pairs as its second argument. To help illustrate this method, let's consider the following model:

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

In this example, let's assume `Event`, `Photo`, and `Post` models may create `ActivityFeed` models. Additionally, let's assume that `Event` models belong to a `Calendar` model, `Photo` models are associated with `Tag` models, and `Post` models belong to an `Author` model.

이 예제에서는 `Event`, `Photo` 그리고 `Post` 모델이 `ActivityFeed` 모델을 만들 수 있다고 가정해보겠습니다. 또한 `Event` 모델은 `Calendar` 모델에 속하고 `Photo` 모델은 `Tag` 모델, `Post` 모델은 `Author` 모델에 속한다고 가정해보겠습니다.

Using these model definitions and relationships, we may retrieve `ActivityFeed` model instances and eager load all `parentable` models and their respective nested relationships:

이러한 모델 정의와 관계를 사용하여 `ActivityFeed` 모델 인스턴스를 검색하고 모든 `parentable` 모델과 각각 내포된 관계를 eager 로드할 수 있습니다.

    $activities = ActivityFeed::with('parentable')
        ->get()
        ->loadMorph('parentable', [
            Event::class => ['calendar'],
            Photo::class => ['tags'],
            Post::class => ['author'],
        ]);

<a name="inserting-and-updating-related-models"></a>
## Inserting & Updating Related Models
## 연관된 모델 삽입하기 & 수정하기

<a name="the-save-method"></a>
### The Save Method
### Save 메소드

Eloquent provides convenient methods for adding new models to relationships. For example, perhaps you need to insert a new `Comment` for a `Post` model. Instead of manually setting the `post_id` attribute on the `Comment`, you may insert the `Comment` directly from the relationship's `save` method:

Eloquent는 관계에 새로운 모델을 추가하는 편리한 메소드들을 제공합니다. 예를 들어, `Post` 모델에 새로운 `Comment`를 추가해야 할 수 있습니다. `Comment`에 수동으로 `post_id` 속성을 지정하는 대신 관계에 `save` 메소드에서 바로 `Comment`를 추가할 수 있습니다.

    $comment = new App\Models\Comment(['message' => 'A new comment.']);

    $post = App\Models\Post::find(1);

    $post->comments()->save($comment);

Notice that we did not access the `comments` relationship as a dynamic property. Instead, we called the `comments` method to obtain an instance of the relationship. The `save` method will automatically add the appropriate `post_id` value to the new `Comment` model.

`comments` 관계를 동적 속성으로 접근하지 않았다는 점을 주목하십시오. 대신에 `comments` 메소드를 호출하여 관계의 인스턴스를 얻었습니다. `save` 메소드는 자동으로 새로운 `Comment` 모델에 적절한 `post_id` 값을 추가할 것입니다.

If you need to save multiple related models, you may use the `saveMany` method:

여러 개의 관련된 모델을 저장해야 한다면 `saveMany` 메소드를 사용하세요:

    $post = App\Models\Post::find(1);

    $post->comments()->saveMany([
        new App\Models\Comment(['message' => 'A new comment.']),
        new App\Models\Comment(['message' => 'Another comment.']),
    ]);

The `save` and `saveMany` methods will not add the new models to any in-memory relationships that are already loaded onto the parent model. If you plan on accessing the relationship after using the `save` or `saveMany` methods, you may wish to use the `refresh` method to reload the model and its relationships:

`save` 및 `saveMany` 메소드는 이미 상위 모델에 로드 된 인메모리 관계에 새 모델을 추가하지 않습니다. `save` 또는 `saveMany` 메서드를 사용한 후 관계에 액세스하려는 경우 `refresh` 메서드를 사용하여 모델과 해당 관계를 다시 로드 할 수 있습니다.

    $post->comments()->save($comment);

    $post->refresh();

    // All comments, including the newly saved comment...
    $post->comments;

<a name="the-push-method"></a>
#### Recursively Saving Models & Relationships
#### 재귀적으로 모델 및 관계 저장

If you would like to `save` your model and all of its associated relationships, you may use the `push` method:

모델과 관련된 모든 관계를 `save` 하고 싶다면 `push` 메소드를 사용할 수 있습니다.

    $post = App\Models\Post::find(1);

    $post->comments[0]->message = 'Message';
    $post->comments[0]->author->name = 'Author Name';

    $post->push();

<a name="the-create-method"></a>
### The Create Method
### Create 메소드

In addition to the `save` and `saveMany` methods, you may also use the `create` method, which accepts an array of attributes, creates a model, and inserts it into the database. Again, the difference between `save` and `create` is that `save` accepts a full Eloquent model instance while `create` accepts a plain PHP `array`:

`save`와 `saveMany` 메소드 외에도 속성을 배열을 받아들이고 모델을 생성하여 데이터베이스에 삽입하는 `create` 메소드를 사용하실 수 있습니다. `save`는 완전한 Eloquent 모델 인스턴스를 받아들이는 데 반해 `create`는 순수 PHP `배열`를 받는다는 점에서 차이가 있습니다.

    $post = App\Models\Post::find(1);

    $comment = $post->comments()->create([
        'message' => 'A new comment.',
    ]);

> {tip} Before using the `create` method, be sure to review the documentation on attribute [mass assignment](/docs/{{version}}/eloquent#mass-assignment).

> {팁} `create` 메소드를 사용하기 전에 [대량 할당-mass assignment](/docs/{{version}}/eloquent#mass-assignment) 문서를 반드시 확인하시기 바랍니다.

You may use the `createMany` method to create multiple related models:

`createMany` 메소드를 사용해서 여러 개의 관련된 모델을 만들 수 있습니다.

    $post = App\Models\Post::find(1);

    $post->comments()->createMany([
        [
            'message' => 'A new comment.',
        ],
        [
            'message' => 'Another new comment.',
        ],
    ]);

You may also use the `findOrNew`, `firstOrNew`, `firstOrCreate` and `updateOrCreate` methods to [create and update models on relationships](https://laravel.com/docs/{{version}}/eloquent#other-creation-methods).

`findOrNew`,`firstOrNew`,`firstOrCreate` 및 `updateOrCreate` 메소드를 [관계에 대한 모델 생성 및 업데이트](/docs/{{version}}/eloquent#other-creation-methods)에 사용할 수도 있습니다 .

<a name="updating-belongs-to-relationships"></a>
### Belongs To Relationships
### Belongs To 관계

When updating a `belongsTo` relationship, you may use the `associate` method. This method will set the foreign key on the child model:

`belongsTo` 관계를 변경 할 때 `associate` 메소드를 사용할 수 있습니다. 이 메소드는 자식 모델에 외래 키를 지정합니다.

    $account = App\Models\Account::find(10);

    $user->account()->associate($account);

    $user->save();

When removing a `belongsTo` relationship, you may use the `dissociate` method. This method will set the relationship's foreign key to `null`:

`belongsTo` 관계를 제거할 때는 `dissociate` 메소드를 사용하시면 됩니다. 이 메소드는 외래 키를 `null` 로 설정할 것입니다.

    $user->account()->dissociate();

    $user->save();

<a name="default-models"></a>
#### Default Models
#### 기본 모델

The `belongsTo`, `hasOne`, `hasOneThrough`, and `morphOne` relationships allow you to define a default model that will be returned if the given relationship is `null`. This pattern is often referred to as the [Null Object pattern](https://en.wikipedia.org/wiki/Null_Object_pattern) and can help remove conditional checks in your code. In the following example, the `user` relation will return an empty `App\Models\User` model if no `user` is attached to the post:

`belongsTo`, `hasOne`, `hasOneThrough`, 와 `morphOne` 관계는 주어진 관계가 `null` 일 때 리턴될 기본 모델을 정의하게합니다. 이 패턴은 [Null Object pattern](https://en.wikipedia.org/wiki/Null_Object_pattern)이라고도하며 코드에서 조건부 검사를 제거하는 데 도움이 될 수 있습니다. 다음 예제에서 `user` 관계가 작성된 글에 포함되어 있지 않으면 `user` 관계는 빈 `App\Models\User` 모델을 반환합니다.

    /**
     * Get the author of the post.
     */
    public function user()
    {
        return $this->belongsTo('App\Models\User')->withDefault();
    }

To populate the default model with attributes, you may pass an array or Closure to the `withDefault` method:

속성을 가진 기본 모델을 채우려면 배열이나 Closure 를 `withDefault` 메소드에 넘길 수 있습니다.

    /**
     * Get the author of the post.
     */
    public function user()
    {
        return $this->belongsTo('App\Models\User')->withDefault([
            'name' => 'Guest Author',
        ]);
    }

    /**
     * Get the author of the post.
     */
    public function user()
    {
        return $this->belongsTo('App\Models\User')->withDefault(function ($user, $post) {
            $user->name = 'Guest Author';
        });
    }

<a name="updating-many-to-many-relationships"></a>
### Many To Many Relationships
### 다대다 관계

#### Attaching / Detaching
#### 추가하기 / 분리하기

Eloquent also provides a few additional helper methods to make working with related models more convenient. For example, let's imagine a user can have many roles and a role can have many users. To attach a role to a user by inserting a record in the intermediate table that joins the models, use the `attach` method:

Eloquent는 또한 연관된 모델들을 다루는 데 편리한 몇 개의 헬퍼 메소드를 추가로 제공합니다. 예를 들어 한 사용자가 여러 역할을 가질 수 있고 한 역할이 여러 사용자를 갖는다고 상상해봅시다. 모델들을 합치는 중간 테이블에 기록을 추가하여 사용자에게 역할을 추가하려면 `attach` 메소드를 사용하세요:

    $user = App\Models\User::find(1);

    $user->roles()->attach($roleId);

When attaching a relationship to a model, you may also pass an array of additional data to be inserted into the intermediate table:

모델에 관계를 추가할 때 중간 테이블에 삽입될 추가 데이터의 배열을 전달할 수도 있습니다.

    $user->roles()->attach($roleId, ['expires' => $expires]);

Sometimes it may be necessary to remove a role from a user. To remove a many-to-many relationship record, use the `detach` method. The `detach` method will delete the appropriate record out of the intermediate table; however, both models will remain in the database:

경우에 따라 사용자에게서 역할을 분리하는 것이 필요할 수 있습니다. 다대다 관계 기록을 제거하려면 `detach` 메소드를 이용하면 됩니다. `detach` 메소드는 중간 테이블에서 적절한 기록을 삭제할 것입니다. 하지만 두 모델은 모두 데이터베이스에 남을 것입니다.

    // Detach a single role from the user...
    $user->roles()->detach($roleId);

    // Detach all roles from the user...
    $user->roles()->detach();

For convenience, `attach` and `detach` also accept arrays of IDs as input:

보다 편리하게, `attach`와 `detach` ID의 배열 또한 입력으로 전달 받습니다.

    $user = App\Models\User::find(1);

    $user->roles()->detach([1, 2, 3]);

    $user->roles()->attach([
        1 => ['expires' => $expires],
        2 => ['expires' => $expires],
    ]);

#### Syncing Associations
#### 연결 동기화

You may also use the `sync` method to construct many-to-many associations. The `sync` method accepts an array of IDs to place on the intermediate table. Any IDs that are not in the given array will be removed from the intermediate table. So, after this operation is complete, only the IDs in the given array will exist in the intermediate table:

다대다 관계를 생성하기 위해 `sync` 메소드를 사용할 수도 있습니다. `sync` 메소드는 중간 테이블에 놓을 ID의 배열을 전달 받습니다. 주어진 배열에 포함되어 있지 않는 ID는 중간 테이블에서 제거됩니다. 따라서 이 작업이 끝나면 주어진 배열에 포함된 ID들만이 중간 테이블에 존재할 것입니다.

    $user->roles()->sync([1, 2, 3]);

You may also pass additional intermediate table values with the IDs:

ID들과 함께 중간 테이블 값을 추가로 전달할 수도 있습니다.

    $user->roles()->sync([1 => ['expires' => true], 2, 3]);

If you do not want to detach existing IDs, you may use the `syncWithoutDetaching` method:

존재하는 ID들을 삭제하고 싶지 않으면, `syncWithoutDetaching` 메소드를 사용하면 됩니다.

    $user->roles()->syncWithoutDetaching([1, 2, 3]);

#### Toggling Associations
#### 연결 켜고 끄기(토클)

The many-to-many relationship also provides a `toggle` method which "toggles" the attachment status of the given IDs. If the given ID is currently attached, it will be detached. Likewise, if it is currently detached, it will be attached:

다대다 관계는 또한 주어진 ID들의 추가 상태를 "전환" 할 수 있는 `toggle` 메소드를 제공합니다. 주어진 ID가 현재 추가되어 있으면 해제됩니다. 마찬가지로 현재 해제되어 있다면 추가될 것입니다.

    $user->roles()->toggle([1, 2, 3]);

#### Saving Additional Data On A Pivot Table
#### 피벗 테이블에 추가 데이터 저장하기

When working with a many-to-many relationship, the `save` method accepts an array of additional intermediate table attributes as its second argument:

다대다 관계를 작업할 때, `save` 메소드는 두번째 인자로 추가적인 중간 테이블의 속성에 대한 배열을 받아 들입니다.

    App\Models\User::find(1)->roles()->save($role, ['expires' => $expires]);

#### Updating A Record On A Pivot Table
#### 피벗 테이블 레코드 수정하기

If you need to update an existing row in your pivot table, you may use `updateExistingPivot` method. This method accepts the pivot record foreign key and an array of attributes to update:

피벗 테이블에 존재하는 레코드를 수정할 필요가 있다면, `updateExistingPivot` 메소드를 사용하면 됩니다. 이 메소드는 피벗 테이블의 외래 키와 수정하려는 속성에 대한 배열을 인자로 받습니다.

    $user = App\Models\User::find(1);

    $user->roles()->updateExistingPivot($roleId, $attributes);

<a name="touching-parent-timestamps"></a>
## Touching Parent Timestamps
## 부모의 타임스탬프 값 갱신

When a model `belongsTo` or `belongsToMany` another model, such as a `Comment` which belongs to a `Post`, it is sometimes helpful to update the parent's timestamp when the child model is updated. For example, when a `Comment` model is updated, you may want to automatically "touch" the `updated_at` timestamp of the owning `Post`. Eloquent makes it easy. Just add a `touches` property containing the names of the relationships to the child model:

`Post`에 속하는 `Comment`와 같이 한 모델이 다른 모델에 `belongsTo`하거나 `belongsToMany`할 때, 자식 모델이 업데이트되었을 때 부모의 타임스탬프를 갱신하는 것이 유용할 수 있습니다. 예를 들어 `Comment` 모델이 업데이트되었을 때, 소유하는 `Post`의 `updated_at` 타임스탬프의 값을 자동으로 갱신하고자 할 수 있습니다. Eloquent는 이를 손쉽게 할 수 있게 해줍니다. 단순히 자식 모델에 관계의 이름들을 포함하는 `touches` 속성을 추가하면 됩니다.

    <?php

    namespace App\Models;

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
            return $this->belongsTo('App\Models\Post');
        }
    }

Now, when you update a `Comment`, the owning `Post` will have its `updated_at` column updated as well, making it more convenient to know when to invalidate a cache of the `Post` model:

이제 `Comment`를 업데이트할 때 소유하는 `Post`의 `updated_at` 컬럼 또한 업데이트될 것이고, 이렇게 하는 것은 `Post` 모델의 캐시가 갱신되는 편리한 방법이기도 합니다.

    $comment = App\Models\Comment::find(1);

    $comment->text = 'Edit to this comment!';

    $comment->save();
