# Authorization
# Authorization-승인

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Defining Abilities](#defining-abilities)
- [Abilities 정의하기](#defining-abilities)
- [Checking Abilities](#checking-abilities)
- [Abilities 체크하기](#checking-abilities)
    - [Via The Gate Facade](#via-the-gate-facade)
    - [Gate 파사드를 통한 방법](#via-the-gate-facade)
    - [Via The User Model](#via-the-user-model)
    - [User 모델을 통한 방법](#via-the-user-model)
    - [Within Blade Templates](#within-blade-templates)
    - [블레이드 템플릿 안에서 사용하기](#within-blade-templates)
    - [Within Form Requests](#within-form-requests)
    - [Form Requests 안에서 사용하기](#within-form-requests)
- [Policies](#policies)
- [Policies](#policies)
    - [Creating Policies](#creating-policies)
    - [Policies 생성하기](#creating-policies)
    - [Writing Policies](#writing-policies)
    - [Policies 작성하기](#writing-policies)
    - [Checking Policies](#checking-policies)
    - [Policies 확인하기](#checking-policies)
- [Controller Authorization](#controller-authorization)
- [컨트롤러에서 Authorization-승인하기](#controller-authorization)

<a name="introduction"></a>
## Introduction
## 시작하기

In addition to providing [authentication](/docs/{{version}}/authentication) services out of the box, Laravel also provides a simple way to organize authorization logic and control access to resources. There are a variety of methods and helpers to assist you in organizing your authorization logic, and we'll cover each of them in this document.

별다른 설정 없이도, [인증](/docs/{{version}}/authentication) 서비스 활용할 수 있는데에 더하여, 라라벨은 authorization-승인 로직을 처리하고 resources에 접근을 제어하도록 하는 간단한 방법을 제공합니다. authorization-승인 로직을 구성 하는데 다양한 메소드와 헬퍼들을 사용할 수 있으며 이 문서에서 하나씩 알아볼 것입니다. 

<a name="defining-abilities"></a>
## Defining Abilities
## Abilities 정의하기

The simplest way to determine if a user may perform a given action is to define an "ability" using the `Illuminate\Auth\Access\Gate` class. The `AuthServiceProvider` which ships with Laravel serves as a convenient location to define all of the abilities for your application. For example, let's define an `update-post` ability which receives the current `User` and a `Post` [model](/docs/{{version}}/eloquent). Within our ability, we will determine if the user's `id` matches the post's `user_id`:

사용자가 특정 동작을 실행할 수 있는지 알아보는 가장 쉬운 방법은 `Illuminate\Auth\Access\Gate` 클래스를 이용해서 "Ability"를 정의하는 것입니다. 라라벨에 기본적으로 제공되는 `AuthServiceProvider`는 애플리케이션의 모든 ability를 정의할 수 있는 유용한 공간으로써의 역할합니다. 예를 들어 현재 `User`와 `Post` [모델](/docs/{{version}}/eloquent)을 인자로 받는 `update-post` ability를 정의해보겠습니다. ability 안에서 사용자의 `id`가 글의 `user_id`와 맞는지 확인할 것입니다. :

    <?php

    namespace App\Providers;

    use Illuminate\Contracts\Auth\Access\Gate as GateContract;
    use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;

    class AuthServiceProvider extends ServiceProvider
    {
        /**
         * Register any application authentication / authorization services.
         *
         * @param  \Illuminate\Contracts\Auth\Access\Gate  $gate
         * @return void
         */
        public function boot(GateContract $gate)
        {
            $this->registerPolicies($gate);

            $gate->define('update-post', function ($user, $post) {
                return $user->id == $post->user_id;
            });
        }
    }

Note that we did not check if the given `$user` is not `NULL`. The `Gate` will automatically return `false` for **all abilities** when there is not an authenticated user or a specific user has not been specified using the `forUser` method.

`$user`가 `NULL`이 아닌지 확인하지 않았다는 것에 유의하십시오. 인증된 사용자가 없거나 특정 사용자가 `forUser` 메소드로 명시되지 않았을 경우 `Gate`는 **모든 ability**에 대해 자동으로 `false`를 반환할 것입니다. 

#### Class Based Abilities
#### 클래스 기반의 ability

In addition to registering `Closures` as authorization callbacks, you may register class methods by passing a string containing the class name and the method. When needed, the class will be resolved via the [service container](/docs/{{version}}/container):

`Closures`를 authorization-승인을 위한 콜백으로 등록하는 것 이외에도 클래스 이름과 메소드를 포함한 string을 전달하여 클래스 메소드를 등록할 수 있습니다. 필요한 경우 [서비스 컨테이너](/docs/{{version}}/container)에 의해 클래스에 의존성 주입이 될 것입니다.

    $gate->define('update-post', 'Class@method');

<a name="intercepting-all-checks"></a>
<a name="intercepting-authorization-checks"></a>
#### Intercepting Authorization Checks
#### Authorization-승인 체크 로직 수행의 전,후에 필요한 로직 수행하기

Sometimes, you may wish to grant all abilities to a specific user. For this situation, use the `before` method to define a callback that is run before all other authorization checks:

때로는, 특정 사용자에게 모든 ability를 부여하고 싶을 수 있습니다. 이 경우 `before` 메소드로 다른 모든 authorization-승인 점검이 이뤄지기 전에 실행될 콜백을 정의합니다. 

    $gate->before(function ($user, $ability) {
        if ($user->isSuperAdmin()) {
            return true;
        }
    });

If the `before` callback returns a non-null result that result will be considered the result of the check.

`before` 콜백이 null이 아닌 결과를 반환한다면 그 결과값은 확인 결과로 간주될 것입니다.

You may use the `after` method to define a callback to be executed after every authorization check. However, you may not modify the result of the authorization check from an `after` callback:

`after` 메소드로 매 authorization-승인 체크 뒤에 콜백이 실행되도록 정의할 수 있습니다. 하지만 `after` 콜백으로 authorization-승인 확인의 결과값을 변경할 수는 없습니다. :

    $gate->after(function ($user, $ability, $result, $arguments) {
        //
    });

<a name="checking-abilities"></a>
## Checking Abilities
## Abilities 체크하기

<a name="via-the-gate-facade"></a>
### Via The Gate Facade
### Gate 파사드를 통한 방법

Once an ability has been defined, we may "check" it in a variety of ways. First, we may use the `check`, `allows`, or `denies` methods on the `Gate` [facade](/docs/{{version}}/facades). All of these methods receive the name of the ability and the arguments that should be passed to the ability's callback. You do **not** need to pass the current user to these methods, since the `Gate` will automatically prepend the current user to the arguments passed to the callback. So, when checking the `update-post` ability we defined earlier, we only need to pass a `Post` instance to the `denies` method:

ability가 정의되면 다양한 방법으로 "check-확인" 해볼 수 있습니다. 먼저 `Gate` [파사드](/docs/{{version}}/facades)에 `check`, `allows`, 혹은 `denies` 메소드를 사용할 수 있습니다. 이런 메소드들은 모두 ability의 이름과 콜백으로 전달될 인자들을 받습니다. `Gate`가 자동으로 현재의 사용자를 콜백으로 전달된 인자들에 추가할 것이기 때문에 현재 사용자를 이 메소드들에 전달할 필요가 없습니다. 따라서 앞서 정의한 `update-post` ability를 체크할 때 `denies` 메소드에 필요한 `Post` 인스턴스만 전달하면 됩니다: 

    <?php

    namespace App\Http\Controllers;

    use Gate;
    use App\User;
    use App\Post;
    use App\Http\Controllers\Controller;

    class PostController extends Controller
    {
        /**
         * Update the given post.
         *
         * @param  int  $id
         * @return Response
         */
        public function update($id)
        {
            $post = Post::findOrFail($id);

            if (Gate::denies('update-post', $post)) {
                abort(403);
            }

            // Update Post...
        }
    }

Of course, the `allows` method is simply the inverse of the `denies` method, and returns `true` if the action is authorized. The `check` method is an alias of the `allows` method.

물론 `allows` 메소드는 단순하게 `denies` 메소드의 반대로, 액션-동작이 승인되면 `true`를 반환합니다. `check` 메소드는 `allows` 메소드의 또 다른 이름입니다. 

#### Checking Abilities For Specific Users
#### 지정된 사용자에 대한 Abilities 확인하기

If you would like to use the `Gate` facade to check if a user **other than the currently authenticated user** has a given ability, you may use the `forUser` method:

**현재 승인된 사용자 외의 다른 사용자**가 특정 ability를 가지고 있는지 확인하기 위해서 `Gate` 파사드를 사용하고자 한다면, `forUser` 메소드를 사용하면 됩니다: 

    if (Gate::forUser($user)->allows('update-post', $post)) {
        //
    }

#### Passing Multiple Arguments
#### 여러개의 인자 전달하기

Of course, ability callbacks may receive multiple arguments:

당연하게도, ability 콜백은 여러개의 인자를 전달 받을 수 있습니다. 


    Gate::define('delete-comment', function ($user, $post, $comment) {
        //
    });

If your ability needs multiple arguments, simply pass an array of arguments to the `Gate` methods:

어떤 ability가 여러개의 인자를 필요로 한다면 단순히 인자들의 배열을 `Gate` 메소드로 전달하면 됩니다: 

    if (Gate::allows('delete-comment', [$post, $comment])) {
        //
    }

<a name="via-the-user-model"></a>
### Via The User Model
### User 모델을 통한 방법

Alternatively, you may check abilities via the `User` model instance. By default, Laravel's `App\User` model uses an `Authorizable` trait which provides two methods: `can` and `cannot`. These methods may be used similarly to the `allows` and `denies` methods present on the `Gate` facade. So, using our previous example, we may modify our code like so:

앞의 방법 외에, `User` 모델의 인스턴스를 통해서 ability를 확인할 수 있습니다. 라라벨의 `App\User` 모델은 `Authorizable` 속성을 사용하여 `can`과 `cannot` 메소드를 제공합니다. 이 메소드들은 `Gate` 파사드에 존재하는 `allows`와 `denies` 메소드 같이 사용될 수 있습니다. 따라서 앞의 예제의 경우, 다음과 같이 코드를 변경할 수 있습니다:

    <?php

    namespace App\Http\Controllers;

    use App\Post;
    use Illuminate\Http\Request;
    use App\Http\Controllers\Controller;

    class PostController extends Controller
    {
        /**
         * Update the given post.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  int  $id
         * @return Response
         */
        public function update(Request $request, $id)
        {
            $post = Post::findOrFail($id);

            if ($request->user()->cannot('update-post', $post)) {
                abort(403);
            }

            // Update Post...
        }
    }

Of course, the `can` method is simply the inverse of the `cannot` method:

물론 `can` 메소드는 단순하게, `cannot` 메소드의 반대의 동작입니다: 

    if ($request->user()->can('update-post', $post)) {
        // Update Post...
    }

<a name="within-blade-templates"></a>
### Within Blade Templates
### 블레이드 템플릿 안에서 사용하기

For convenience, Laravel provides the `@can` Blade directive to quickly check if the currently authenticated user has a given ability. For example:

보다 편리하게, 라라벨은 현재 승인된 사용자가 특정 ability를 가지고 있는지 빠르게 확인하기 위한 `@can` 블레이드 지시어를 제공합니다. 예를 들어: 

    <a href="/post/{{ $post->id }}">View Post</a>

    @can('update-post', $post)
        <a href="/post/{{ $post->id }}/edit">Edit Post</a>
    @endcan

You may also combine the `@can` directive with `@else` directive:

`@can` 지시어와 `@else` 지시어를 조합할 수도 있습니다. 

    @can('update-post', $post)
        <!-- The Current User Can Update The Post -->
    @else
        <!-- The Current User Can't Update The Post -->
    @endcan

<a name="within-form-requests"></a>
### Within Form Requests
### Form Requests 안에서 사용하기

You may also choose to utilize your `Gate` defined abilities from a [form request's](/docs/{{version}}/validation#form-request-validation) `authorize` method. For example:

[Form Request](/docs/{{version}}/validation#form-request-validation)의 `authorize` 메소드를 통해서도 `Gate`에서 정의된 ability들을 활용할 수 있습니다. 예를 들어: 

    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize()
    {
        $postId = $this->route('post');

        return Gate::allows('update', Post::findOrFail($postId));
    }

<a name="policies"></a>
## Policies
## Policies-정책

<a name="creating-policies"></a>
### Creating Policies
### Policies 생성하기

Since defining all of your authorization logic in the `AuthServiceProvider` could become cumbersome in large applications, Laravel allows you to split your authorization logic into "Policy" classes. Policies are plain PHP classes that group authorization logic based on the resource they authorize.

규모가 큰 애플리케이션에서 모든 승인-authorization 로직을 `AuthServiceProvider`에서 정의하는 것은 번거로울 수 있기 때문에 라라벨은 "Policy" 클래스로 authorization-승인 로직을 나눌 수 있도록 해줍니다. Policies-정책들은 authorization-승인 로직들이 승인하는 자원에 따라 승인 로직을 그룹화시킨 순수 PHP 클래스 입니다. 

First, let's generate a policy to manage authorization for our `Post` model. You may generate a policy using the `make:policy` [artisan command](/docs/{{version}}/artisan). The generated policy will be placed in the `app/Policies` directory:

먼저, `Post` 모델의 authorization-승인을 관리할 policy 클래스를 생성합니다. `make:policy` [아티즌 명령어](/docs/{{version}}/artisan)를 통해서 policy 클래스를 생성할 수 있습니다. 생성된 policy은 `app/Policies` 디렉토리에 위치합니다:

    php artisan make:policy PostPolicy

#### Registering Policies
#### Policies 등록하기

Once the policy exists, we need to register it with the `Gate` class. The `AuthServiceProvider` contains a `policies` property which maps various entities to the policies that manage them. So, we will specify that the `Post` model's policy is the `PostPolicy` class:

policy을 생성하고 나면, `Gate` 클래스와 함께 등록할 필요가 있습니다. `AuthServiceProvider`는 policy가 관리하는 다양한 객체들을 보여주는 `policies` 속성을 가지고 있습니다. 따라서 `Post` 모델의 policy를 `PostPolicy`로 지정할 것입니다: 

    <?php

    namespace App\Providers;

    use App\Post;
    use App\Policies\PostPolicy;
    use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;

    class AuthServiceProvider extends ServiceProvider
    {
        /**
         * The policy mappings for the application.
         *
         * @var array
         */
        protected $policies = [
            Post::class => PostPolicy::class,
        ];

        /**
         * Register any application authentication / authorization services.
         *
         * @param  \Illuminate\Contracts\Auth\Access\Gate  $gate
         * @return void
         */
        public function boot(GateContract $gate)
        {
            $this->registerPolicies($gate);
        }
    }

<a name="writing-policies"></a>
### Writing Policies
### Policies 작성하기

Once the policy has been generated and registered, we can add methods for each ability it authorizes. For example, let's define an `update` method on our `PostPolicy`, which will determine if the given `User` can "update" a `Post`:

policy을 생성하고 등록한 뒤에는 policy가 승인하는 각 ability에 메소드를 추가할 수 있습니다. 예를 들어 특정 `User` 사용자가 `Post`를 "업데이트"할 수 있는지 결정하는 `update` 메소드를 `PostPolicy`에 정의해보겠습니다:

    <?php

    namespace App\Policies;

    use App\User;
    use App\Post;

    class PostPolicy
    {
        /**
         * Determine if the given post can be updated by the user.
         *
         * @param  \App\User  $user
         * @param  \App\Post  $post
         * @return bool
         */
        public function update(User $user, Post $post)
        {
            return $user->id === $post->user_id;
        }
    }

You may continue to define additional methods on the policy as needed for the various abilities it authorizes. For example, you might define `show`, `destroy`, or `addComment` methods to authorize various `Post` actions.

policy가 승인하는 다양한 ability들을 위해 필요한 추가적인 메소드를 계속해서 정의할 수 있습니다. 예로 들어 다양한 `Post` 동작들을 승인하기 위해 `show`, `destroy`, 혹은 `addComment` 메소드를 정의할 수 있습니다.   

> **Note:** All policies are resolved via the Laravel [service container](/docs/{{version}}/container), meaning you may type-hint any needed dependencies in the policy's constructor and they will be automatically injected.

> **참고:** 모든 policy들은 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)를 통해 의존성이 해결되며, 이는 policy의 constructor에 의존객체들을  타입힌트하면 자동으로 주입된다는 것을 의미합니다. 

#### Intercepting All Checks
#### 모든 체크를 수행하기 전,후에 필요한 로직 추가하기

Sometimes, you may wish to grant all abilities to a specific user on a policy. For this situation, define a `before` method on the policy. This method will be run before all other authorization checks on the policy:

종종 특정 사용자에게 모든 ability을 부여하고 싶을 수도 있습니다. 이 경우에는 policy에 `before` 메소드를 정의하면 됩니다. 이 메소드는 다른 모든 authorization을 확인하기 전에 실행될 것입니다: 

    public function before($user, $ability)
    {
        if ($user->isSuperAdmin()) {
            return true;
        }
    }

If the `before` method returns a non-null result that result will be considered the result of the check.

`before` 메소드가 null을 반환하지 않았다면 그 결과값이 확인의 결과로 간주될 것입니다. 

<a name="checking-policies"></a>
### Checking Policies
### Policy 확인하기

Policy methods are called in exactly the same way as `Closure` based authorization callbacks. You may use the `Gate` facade, the `User` model, the `@can` Blade directive, or the `policy` helper.

Policy 메소드는 `Closure` 기반의 authorization-승인 콜백과 동일한 방식으로 호출됩니다. `Gate` 파사드, `User` 모델, `@can` 블레이드 지시어 혹은 policy 헬퍼를 사용할 수 있습니다. 

#### Via The Gate Facade
#### Gate 파사드를 통한 방법

The `Gate` will automatically determine which policy to use by examining the class of the arguments passed to its methods. So, if we pass a `Post` instance to the `denies` method, the `Gate` will utilize the corresponding `PostPolicy` to authorize actions:

`Gate`는 메소드에 전달된 인자의 클래스를 검토함으로써 어떤 policy를 사용할 지 자동으로 결정합니다. 따라서 `denies` 메소드에 `Post` 인스턴스를 전달하면 `Gate`는 그에 상응하는 `PostPolicy`를 활용하여 동작을 승인하게 됩니다:

    <?php

    namespace App\Http\Controllers;

    use Gate;
    use App\User;
    use App\Post;
    use App\Http\Controllers\Controller;

    class PostController extends Controller
    {
        /**
         * Update the given post.
         *
         * @param  int  $id
         * @return Response
         */
        public function update($id)
        {
            $post = Post::findOrFail($id);

            if (Gate::denies('update', $post)) {
                abort(403);
            }

            // Update Post...
        }
    }

#### Via The User Model
#### User 모델을 통한 방법

The `User` model's `can` and `cannot` methods will also automatically utilize policies when they are available for the given arguments. These methods provide a convenient way to authorize actions for any `User` instance retrieved by your application:

`User` 모델의 `can` 과 `cannot` 메소드 또한 주어진 인자들에 대해서 사용 가능한 policy가 있다면, 자동으로 사용합니다. 이 메소드들은 애플리케이션이 획득한 `User` 인스턴스에 대한 동작을 승인할 수 있는 편리한 방법을 제공합니다. 

    if ($user->can('update', $post)) {
        //
    }

    if ($user->cannot('update', $post)) {
        //
    }

#### Within Blade Templates
#### 블레이드 템플릿 안에서 사용하기

Likewise, the `@can` Blade directive will utilize policies when they are available for the given arguments:

`@can` 블레이드 지시어 또한 주어진 인자들에 의해 사용 가능할 때 policy들을 활용할 것입니다. 

    @can('update', $post)
        <!-- The Current User Can Update The Post -->
    @endcan

#### Via The Policy Helper
#### Policy 헬퍼를 통한 방법

The global `policy` helper function may be used to retrieve the `Policy` class for a given class instance. For example, we may pass a `Post` instance to the `policy` helper to get an instance of our corresponding `PostPolicy` class:

글로벌 `policy` 헬퍼 함수는 주어진 클래스 인스턴스에 따라 `Policy` 클래스를 찾을 수 있게 해줍니다. 예를 들어 `PostPolicy` 클래스의 인스턴스를 갖기 위해 `policy` 헬퍼에 `Post` 인스턴스를 전달할 수 있습니다.:  

    if (policy($post)->update($user, $post)) {
        //
    }

<a name="controller-authorization"></a>
## Controller Authorization
## 컨트롤러 Authorization-승인

By default, the base `App\Http\Controllers\Controller` class included with Laravel uses the `AuthorizesRequests` trait. This trait provides the `authorize` method, which may be used to quickly authorize a given action and throw a `AuthorizationException` if the action is not authorized.

기본적으로 `App\Http\Controllers\Controller` 베이스 클래스는 `AuthorizesRequests` 트레이트-trait을 사용합니다. 이 트레이트-trait는 `authorize` 메소드를 제공하여 특정 동작에 대해 빠르게 승인하거나, 승인되지 않을 경우 `AuthorizationException`을 던지게 됩니다. 

The `authorize` method shares the same signature as the various other authorization methods such as `Gate::allows` and `$user->can()`. So, let's use the `authorize` method to quickly authorize a request to update a `Post`:

`authorize` 메소드는 `Gate::allows`와 `$user->can()`과 같은 다양한 다른 authorization-승인 메소드들과 같은 서명을 공유합니다. 따라서 `authorize` 메소드를 써서 빠르게 `Post` 수정 요청을 authorization-승인하도록 해보겠습니다:

    <?php

    namespace App\Http\Controllers;

    use App\Post;
    use App\Http\Controllers\Controller;

    class PostController extends Controller
    {
        /**
         * Update the given post.
         *
         * @param  int  $id
         * @return Response
         */
        public function update($id)
        {
            $post = Post::findOrFail($id);

            $this->authorize('update', $post);

            // Update Post...
        }
    }

If the action is authorized, the controller will continue executing normally; however, if the `authorize` method determines that the action is not authorized, a `AuthorizationException` will automatically be thrown which generates a HTTP response with a `403 Not Authorized` status code. As you can see, the `authorize` method is a convenient, fast way to authorize an action or throw an exception with a single line of code.

동작이 승인되면 컨트롤러는 계속해서 정상적으로 동작 할 것입니다. 하지만 `authorize` 메소드에서 동작이 승인되지 않았다고 판정될 경우 `AuthorizationException`이 던져지고 이에 따라 자동적으로 `403 Not Authorized` 상태 코드를 포함한 HTTP 응답이 생성될 것입니다. `authorize` 메소드는 단 한 줄의 코드로 빠르고 편리하게 특정 동작을 승인하거나 예외를 던질 수 있는 방법입니다. 

The `AuthorizesRequests` trait also provides the `authorizeForUser` method to authorize an action on a user that is not the currently authenticated user:

`AuthorizesRequests` 트레이트-trait는 현재 승인된 사용자가 아닌 특정 사용자에 대한 동작을 승인하도록 하는 `authorizeForUser` 메소드를 제공합니다: 

    $this->authorizeForUser($user, 'update', $post);

#### Automatically Determining Policy Methods
#### 자동으로 Policy 메소드 결정하기

Frequently, a policy's methods will correspond to the methods on a controller. For example, in the `update` method above, the controller method and the policy method share the same name: `update`.

빈번하게, policy의 메소드는 컨트롤러의 메소드에 대응 될 것입니다. 예를 들어, 앞에서 살펴본 `update` 메소드와 같이 컨트롤러 메소드와 policy 메소드는 `update`라는 같은 이름을 가집니다. 

For this reason, Laravel allows you to simply pass the instance arguments to the `authorize` method, and the ability being authorized will automatically be determined based on the name of the calling function. In this example, since `authorize` is called from the controller's `update` method, the `update` method will also be called on the `PostPolicy`:

이를 통해서, 라라벨은 간단하게 `authorize` 메소드에 인스턴스 인자를 전달할 수 있도록 해주고, 승인된 ablity는 자동으로 호출 기능의 이름에 따라 결정됩니다. 아래의 예제에서는 `authorize`가 컨트롤러의 `update` 메소드에서 호출되었기 때문에 `update` 메소드는 `PostPolicy`에서도 호출 될것입니다:

    /**
     * Update the given post.
     *
     * @param  int  $id
     * @return Response
     */
    public function update($id)
    {
        $post = Post::findOrFail($id);

        $this->authorize($post);

        // Update Post...
    }
