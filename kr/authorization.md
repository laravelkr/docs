# Authorization
# 권한 승인

- [Introduction](#introduction)
- [소개하기](#introduction)
- [Gates](#gates)
- [Gate](#gates)
    - [Writing Gates](#writing-gates)
    - [Gates 작성하기](#writing-gates)
    - [Authorizing Actions](#authorizing-actions-via-gates)
    - [승인 액션](#authorizing-actions-via-gates)
- [Creating Policies](#creating-policies)
- [Policy 생성하기](#creating-policies)
    - [Generating Policies](#generating-policies)
    - [Policies 파일 생성하기](#generating-policies)
    - [Registering Policies](#registering-policies)
    - [Policies 등록하기](#registering-policies)
- [Writing Policies](#writing-policies)
- [Policies 작성하기](#writing-policies)
    - [Policy Methods](#policy-methods)
    - [Policy 메소드](#policy-methods)
    - [Methods Without Models](#methods-without-models)
    - [모델없는 메소드](#methods-without-models)
    - [Policy Filters](#policy-filters)
    - [Policy 필터](#policy-filters)
- [Authorizing Actions Using Policies](#authorizing-actions-using-policies)
- [Policy 를 사용하여 액션의 권한승인하기](#authorizing-actions-using-policies)
    - [Via The User Model](#via-the-user-model)
    - [User 모델을 통해서](#via-the-user-model)
    - [Via Middleware](#via-middleware)
    - [미들웨어를 통해서](#via-middleware)
    - [Via Controller Helpers](#via-controller-helpers)
    - [컨트롤러 헬퍼를 통해서](#via-controller-helpers)
    - [Via Blade Templates](#via-blade-templates)
    - [블레이드 템플릿을 통해서](#via-blade-templates)

<a name="introduction"></a>
## Introduction
## 소개하기

In addition to providing [authentication](/docs/{{version}}/authentication) services out of the box, Laravel also provides a simple way to authorize user actions against a given resource. Like authentication, Laravel's approach to authorization is simple, and there are two primary ways of authorizing actions: gates and policies.

별다른 설정없이도 [인증](/docs/{{version}}/authentication) 기능을 제공하는 것에 더하여, 라라벨은 간단한 방법으로 주어진 리소스에 대한 사용자 액션의 권한을 승인하는 방법을 제공합니다. 사용자 인증과 같이 권한 승인에 대한 라라벨의 접근 방식은 간단하며, 액션에 대한 승인을 위한 두가지 기본 방법이 있습니다: Gate 와 Policy 입니다.

Think of gates and policies like routes and controllers. Gates provide a simple, Closure based approach to authorization while policies, like controllers, group their logic around a particular model or resource. We'll explore gates first and then examine policies.

gate 와 policy은 라우트와 컨트롤러와 같다고 생각됩니다. gate는 간단한, 클로저 기반의 권한 승인에 대한 접근방식을 제공하고, 컨트롤러와 같은 역할을 하는 policy는 특정 모델이나 리소스에 대한 로직을 정리 한 것입니다. 먼저 gate를 설명하고, policy에 대해서 확인해보겠습니다.

It is important to not view gates and policies as mutually exclusive for your application. Most applications will most likely contain a mixture of gates and policies, and that is perfectly fine! Gates are most applicable to actions which are not related to any model or resource, such as viewing an administrator dashboard. In contrast, policies should be used when you wish to authorize an action for a particular model or resource.

gate와 policy는 어플리케이션에서 서로 반대되는 기능이라고 보지 않는 것이 중요합니다. 대부분의 어플리케이션에서 gate와 policy를 혼합하여 사용되며, 이것은 아무런 문제가 없습니다. 관리자 대시보드와 같이 모델과 리소스에 관련되지 않은 작업들이 주로 gate에 정합합니다. 이에 반해, policy는 특정 모델이나 리소스에 대한 작업을 승인하고자 하는 경우 사용해야합니다.

<a name="gates"></a>
## Gates
## Gate

<a name="writing-gates"></a>
### Writing Gates
### Gates 작성하기

Gates are Closures that determine if a user is authorized to perform a given action and are typically defined in the `App\Providers\AuthServiceProvider` class using the `Gate` facade. Gates always receive a user instance as their first argument, and may optionally receive additional arguments such as a relevant Eloquent model:

Gate는 사용자가 주어진 액션에 대해서 수행할 수 있는 권한이 있는지 없는지 확인하는 클로저로, 일반적으로 `App\Providers\AuthServiceProvider` 클래스에 `Gate` 파사드를 사용하여 정의되어 있습니다. Gate는 항상 사용자 인스턴스를 첫번째 인자로 받고, 관련된 Eloquent 모델과 같은 추가적인 인자들을 선택적으로 전달 받습니다:

    /**
     * Register any authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();

        Gate::define('update-post', function ($user, $post) {
            return $user->id == $post->user_id;
        });
    }

Gates may also be defined using a `Class@method` style callback string, like controllers:

Gate는 또한 컨트롤러와 같이 `Class@method` 스타일의 콜백 문자열으로도 정의할 수 있습니다: 

    /**
     * Register any authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();

        Gate::define('update-post', 'PostPolicy@update');
    }

#### Resource Gates
#### 리소스 Gate

You may also define multiple Gate abilities at once using the `resource` method:

`resource` 메소드를 사용하여 여러개의 Gate 액션을 정의할 수도 있습니다: 

    Gate::resource('posts', 'PostPolicy');

This is identical to manually defining the following Gate definitions:

이렇게 정의하면 직접 다음의 Gate 정의 한것과 동일하게 적용됩니다: 

    Gate::define('posts.view', 'PostPolicy@view');
    Gate::define('posts.create', 'PostPolicy@create');
    Gate::define('posts.update', 'PostPolicy@update');
    Gate::define('posts.delete', 'PostPolicy@delete');

By default, the `view`, `create`, `update`, and `delete` abilities will be defined. You may define additional abilities by passing an array as third argument to the `resource` method. The key of the array defines the name of the ability while the value defines the method name:

기본적으로 `view`, `create`, `update` 그리고 `delete` 이 정의됩니다. `resource` 메소드에 세번째 인자로 배열을 전달하여 추가적인 메소드를 정의할 수 있습니다. 배열의 키는 Gate 엑션의 이름을, 값은 메소드를 정의합니다:   

    Gate::resource('posts', 'PostPolicy', [
        'posts.photo' => 'updatePhoto',
        'posts.image' => 'updateImage',
    ]);

<a name="authorizing-actions-via-gates"></a>
### Authorizing Actions
### 액션 권한 승인하기

To authorize an action using gates, you should use the `allows` method. Note that you are not required to pass the currently authenticated user to the `allows` method. Laravel will automatically take care of passing the user into the gate Closure:

gate를 사용하여 액션에 대한 권한을 승인하려면, `allows` 메소드를 사용해야 합니다. 현재 인증된 사용자를 `allow` 메소드에 전달할 필요는 없습니다. 라라벨이 자동으로 Gate 클로저에 사용자를 전달합니다:

    if (Gate::allows('update-post', $post)) {
        // The current user can update the post...
    }

If you would like to determine if a particular user is authorized to perform an action, you may use the `forUser` method on the `Gate` facade:

특정 사용자가 액션을 수행 할 수 있는 권한이 있는지 확인하려는 경우, `Gate` 파사드의 `forUser` 메소드를 사용할 수 있습니다:

    if (Gate::forUser($user)->allows('update-post', $post)) {
        // The user can update the post...
    }

<a name="creating-policies"></a>
## Creating Policies
## Policy 생성하기

<a name="generating-policies"></a>
### Generating Policies
### Policies 파일 생성하기

Policies are classes that organize authorization logic around a particular model or resource. For example, if your application is a blog, you may have a `Post` model and a corresponding `PostPolicy` to authorize user actions such as creating or updating posts.

Policy는 특정 모델 또는 리소스에 대한 권한 승인 로직을 구성한 클래스 입니다. 예를 들어 블로그 어플리케이션의 경우, `Post` 모델과 해당 포스트를 생성하거나 수정할 수 있는 액션을 허용하는 `PostPolicy`가 있을 수 있습니다.

You may generate a policy using the `make:policy` [artisan command](/docs/{{version}}/artisan). The generated policy will be placed in the `app/Policies` directory. If this directory does not exist in your application, Laravel will create it for you:

`make:policy` [아티즌 명령어](/docs/{{version}}/artisan)를 사용하여 policy 클래스를 생성할 수 있습니다. 생성된 policy는 `app/Policies` 디렉토리에 있습니다. 만약 어플리케이션에서 이 디렉토리가 존재하지 않더라도, 라라벨이 디렉토리를 생성해줍니다:

    php artisan make:policy PostPolicy

The `make:policy` command will generate an empty policy class. If you would like to generate a class with the basic "CRUD" policy methods already included in the class, you may specify a `--model` when executing the command:

`make:policy` 명령어는 비어 있는 policy 클래스를 생성합니다. 기본적인 "CRUD"에 해당하는 policy 메소드를 새로 생성하는 클래스에 포함시키려면, 명령어를 실행할 때 `--model`을 지정하십시오.

    php artisan make:policy PostPolicy --model=Post

> {tip} All policies are resolved via the Laravel [service container](/docs/{{version}}/container), allowing you to type-hint any needed dependencies in the policy's constructor to have them automatically injected.

> {tip} 모든 policy는 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)에 의해서 의존성이 해결되기 때문에, policy의 생성자에 필요한 의존성을 타입 힌트 하기만 하면, 자동으로 주입됩니다.

<a name="registering-policies"></a>
### Registering Policies
### Policy 등록하기

Once the policy exists, it needs to be registered. The `AuthServiceProvider` included with fresh Laravel applications contains a `policies` property which maps your Eloquent models to their corresponding policies. Registering a policy will instruct Laravel which policy to utilize when authorizing actions against a given model:

policy를 생성하고 나면, 이를 등록해야 합니다. 새롭게 설치한 라라벨 어플리케이션에 포함되어 있는 `AuthServiceProvider` 는 `policies` 속성은 Eloquent 모델과 해당 모델에 대한 policy의 맵핑 내역입니다. policy를 등록하면 라라벨은 특정 모델에 대한 액션을 승인 할 때 어떤 policy를 활용할 것인지 알려주게 됩니다:

    <?php

    namespace App\Providers;

    use App\Post;
    use App\Policies\PostPolicy;
    use Illuminate\Support\Facades\Gate;
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
         * @return void
         */
        public function boot()
        {
            $this->registerPolicies();

            //
        }
    }

<a name="writing-policies"></a>
## Writing Policies
## Policy 작성하기

<a name="policy-methods"></a>
### Policy Methods
### Policy 메소드

Once the policy has been registered, you may add methods for each action it authorizes. For example, let's define an `update` method on our `PostPolicy` which determines if a given `User` can update a given `Post` instance.

Policy를 등록하고 나면, 각각의 권한을 승인하려는 액션에 관련된 메소드를 추가해야합니다. 예를 들어, 주어진 `User` 가 `Post` 인스턴스를 수정할 수 있는지 확인하는 `PostPolicy` 클래스의 `update` 메소드를 정의해 보겠습니다.

The `update` method will receive a `User` and a `Post` instance as its arguments, and should return `true` or `false` indicating whether the user is authorized to update the given `Post`. So, for this example, let's verify that the user's `id` matches the `user_id` on the post:

`update` 메소드는 한개의 `User` 와 한개의 `Post` 인스턴스를 인자로 받고, 해당 사용자가 주어진 `Post` 를 수정할 수 있는 권한이 있는지에 대한 결과로 `true` 또는 `false` 를 반환해야합니다. 따라서 이 예제에서는 포스트의 `user_id`가 사용자의 `id` 와 일치하는지 확인하도록 해보겠습니다:

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

You may continue to define additional methods on the policy as needed for the various actions it authorizes. For example, you might define `view` or `delete` methods to authorize various `Post` actions, but remember you are free to give your policy methods any name you like.

계속해서 권한 승인이 필요한 다양한 액션에 대한 추가적인 메소드를 정의할 수 있습니다. 예를 들어 `Post` 에 대한 다양한 액션을 승인하기 위해서 `view`나 `delete` 메소드를 정의할 수도 있습니다. policy의 이름은 여러분이 원하는 대로 자유롭게 구성할 수 있습니다.

> {tip} If you used the `--model` option when generating your policy via the Artisan console, it will already contain methods for the `view`, `create`, `update`, and `delete` actions.

> {tip} 만약 아티즌 명령어를 통해서 Policy 클래스를 생성할 때 `--model` 옵션을 사용하였다면, 이미 `view`, `create`, `update` 그리고 `delete` 액션에 해당하는 메소드가 포함되어 있습니다.

<a name="methods-without-models"></a>
### Methods Without Models
### 모델없는 메소드

Some policy methods only receive the currently authenticated user and not an instance of the model they authorize. This situation is most common when authorizing `create` actions. For example, if you are creating a blog, you may wish to check if a user is authorized to create any posts at all.

어떤 Policy 메소드는 단지 권한 승인이 필요한 모델 인스턴스가 아닌, 현재 인승된 사용자만을 전달 받습니다. 이런 상황은 대부분, `create` 액션에 대한 권한을 승인하고자 할 때 일반적으로 나타납니다. 예를 들어 블로그를 만드는 경우, 사용자가 게시물을 생성할 수 있는 권한이 있는지 확인할 수 있습니다.

When defining policy methods that will not receive a model instance, such as a `create` method, it will not receive a model instance. Instead, you should define the method as only expecting the authenticated user:

`create` 메소드와 같은 policy 메소드를 정의할 때는, 모델 인스턴스를 전달 받지 않습니다. 대신에, 인증된 사용자를 받는 메소드를 정의해야 합니다:

    /**
     * Determine if the given user can create posts.
     *
     * @param  \App\User  $user
     * @return bool
     */
    public function create(User $user)
    {
        //
    }

<a name="policy-filters"></a>
### Policy Filters
### Policy 필터

For certain users, you may wish to authorize all actions within a given policy. To accomplish this, define a `before` method on the policy. The `before` method will be executed before any other methods on the policy, giving you an opportunity to authorize the action before the intended policy method is actually called. This feature is most commonly used for authorizing application administrators to perform any action:

특정 사용자를 위해서, 주어진 Policy 안에서 모든 액션에 대한 권한을 승인하고자 할 수 있습니다. 이렇게 하기 위해서는, policy에 `before` 메소드를 정의하면 됩니다. `before` 메소드는 policy에서 다른 메소드가 호출 되기 전에 실행되는 메소드로, 액션에 대한 권한을 승인할 수 있게 해줍니다. 이 기능은 어플리케이션의 관리자에게 모든 액션을 수행하도록 권한을 부여하는 데 가장 일반적으로 사용됩니다:

    public function before($user, $ability)
    {
        if ($user->isSuperAdmin()) {
            return true;
        }
    }

If you would like to deny all authorizations for a user you should return `false` from the `before` method. If `null` is returned, the authorization will fall through to the policy method.

특정 한 사용자에게 모든 권한을 허용하지 않으려면, `before` 메소드에서 `false` 를 반환해야 합니다. 만약 `null`이 반환되면, policy 메소드에 대한 권한 확인이 실패해버립니다.

<a name="authorizing-actions-using-policies"></a>
## Authorizing Actions Using Policies
## Policy 를 사용하여 액션의 권한승인하기

<a name="via-the-user-model"></a>
### Via The User Model
### User 모델을 통해서

The `User` model that is included with your Laravel application includes two helpful methods for authorizing actions: `can` and `cant`. The `can` method receives the action you wish to authorize and the relevant model. For example, let's determine if a user is authorized to update a given `Post` model:

라라벨 어플리케이션에 기본적으로 포함되어 있는 `User` 모델에는 `can` 과 `cant` 액션에 대해 권한을 승인할 수 있는 두개의 헬퍼 메소드를 포함하고 있습니다. `can` 메소드는 권한을 승인하고자 하는 액션과 연관된 모델을 인자로 받습니다. 예를 들어 주어진 `Post` 모델을 수정할 수 있는 권한이 있는지 확인한다고 해보겠습니다:

    if ($user->can('update', $post)) {
        //
    }

If a [policy is registered](#registering-policies) for the given model, the `can` method will automatically call the appropriate policy and return the boolean result. If no policy is registered for the model, the `can` method will attempt to call the Closure based Gate matching the given action name.

만약 주어진 모델을 위한 [Policy 가 등록되어](#registering-policies)있다면, `can` 메소드는 자동으로 적절한 policy를 호출하고 불리언(boolean: true or false)형태의 결과를 반환합니다. 모델을 위한 policy가 등록되지 않은 경우, `can` 메소드는 주어진 액션 이름과 매칭되는 클로저 기반의 Gate 호출을 시도합니다.

#### Actions That Don't Require Models
#### 모델을 필요로 하지 않는 액션

Remember, some actions like `create` may not require a model instance. In these situations, you may pass a class name to the `can` method. The class name will be used to determine which policy to use when authorizing the action:

`create`와 같은 몇몇 액션은 모델 인스턴스를 필요로 하지 않는 다는 것을 기억하십시오. 이러한 경우에는 `can` 메소드에 클래스 이름을 전달하면 됩니다. 클래스 이름은 액션을 승인할 때 어떤 policy가 사용될지 결정하는데 이용됩니다:

    use App\Post;

    if ($user->can('create', Post::class)) {
        // Executes the "create" method on the relevant policy...
    }

<a name="via-middleware"></a>
### Via Middleware
### 미들웨어를 통해서

Laravel includes a middleware that can authorize actions before the incoming request even reaches your routes or controllers. By default, the `Illuminate\Auth\Middleware\Authorize` middleware is assigned the `can` key in your `App\Http\Kernel` class. Let's explore an example of using the `can` middleware to authorize that a user can update a blog post:

라라벨에는 유입되는 request가 라우트나 컨트롤러에 도달하기 전에 액션을 승인할 수 있는 미들웨어가 포함되어 있습니다. 기본적으로 `App\Http\Kernel` 클래스의 `can` 키에는 `Illuminate\Auth\Middleware\Authorize` 미들웨어가 할당되어 있습니다. 사용자가 블로그 포스트를 수정할 수 있는지 권한을 승인하는 `can` 미들웨어를 사용하는 예제를 살펴보겠습니다:

    use App\Post;

    Route::put('/post/{post}', function (Post $post) {
        // The current user may update the post...
    })->middleware('can:update,post');

In this example, we're passing the `can` middleware two arguments. The first is the name of the action we wish to authorize and the second is the route parameter we wish to pass to the policy method. In this case, since we are using [implicit model binding](/docs/{{version}}/routing#implicit-binding), a `Post` model will be passed to the policy method. If the user is not authorized to perform the given action, a HTTP response with a `403` status code will be generated by the middleware.

이 예제에서, `can` 미들웨어에 두개의 인자를 전달합니다. 첫번째는 승인하고자 하는 액션의 이름이고, 두번째는 policy 메소드에 전달하고자 하는 라우트 파라미터 입니다. 이 경우, [묵시적(implicit) 모델 바인딩](/docs/{{version}}/routing#implicit-binding)을 사용하고 있기 때문에, 하나의 `Post` 모델이 policy 메소드에 전달됩니다. 만약 사용자가 주어진 액션을 수행할 권한이 없다면, 미들웨어에 의해서 `403` 상태코드를 가지는 HTTP response-응답이 생성됩니다.

#### Actions That Don't Require Models
#### 모델을 필요로 하지 않는 액션

Again, some actions like `create` may not require a model instance. In these situations, you may pass a class name to the middleware. The class name will be used to determine which policy to use when authorizing the action:

다시한번 이야기 하지만, `create`와 같은 몇몇 액션은 모델 인스턴스를 필요로 하지 않습니다. 이러한 경우 미들웨어에 클래스 이름을 전달하면 됩니다. 클래스 이름은 액션을 승인할 때 어떤 policy가 사용될지 결정하는데 이용됩니다:

    Route::post('/post', function () {
        // The current user may create posts...
    })->middleware('can:create,App\Post');

<a name="via-controller-helpers"></a>
### Via Controller Helpers
### 컨트롤러 헬퍼를 통해서

In addition to helpful methods provided to the `User` model, Laravel provides a helpful `authorize` method to any of your controllers which extend the `App\Http\Controllers\Controller` base class. Like the `can` method, this method accepts the name of the action you wish to authorize and the relevant model. If the action is not authorized, the `authorize` method will throw an `Illuminate\Auth\Access\AuthorizationException`, which the default Laravel exception handler will convert to an HTTP response with a `403` status code:

`User` 모델이 제공하는 편리함 메소드에 더하여, 라라벨에서는 `App\Http\Controllers\Controller` 베이스 클래스를 상속받는 컨트롤러가 사용할 수 있는 `authorize` 메소드를 제공합니다. `can` 메소드와 같이, 이 메소드는 승인하고자 하는 액션의 이름과, 관련된 모델을 인자로 받습니다. 만약 액션이 허용되지 않는 다면, `authorize` 메소드는 라라벨의 exception 핸들러에 의해서 `403` 상태코드를 가진 HTTP 응답-response로 변환되는 `Illuminate\Auth\Access\AuthorizationException`를 발생시킵니다:

    <?php

    namespace App\Http\Controllers;

    use App\Post;
    use Illuminate\Http\Request;
    use App\Http\Controllers\Controller;

    class PostController extends Controller
    {
        /**
         * Update the given blog post.
         *
         * @param  Request  $request
         * @param  Post  $post
         * @return Response
         */
        public function update(Request $request, Post $post)
        {
            $this->authorize('update', $post);

            // The current user can update the blog post...
        }
    }

#### Actions That Don't Require Models
#### 모델을 필요로 하지 않는 액션

As previously discussed, some actions like `create` may not require a model instance. In these situations, you may pass a class name to the `authorize` method. The class name will be used to determine which policy to use when authorizing the action:

앞서 계속해서 이야기한 바와 같이, `create`와 같은 몇몇 액션은 모델 인스턴스를 필요로 하지 않습니다. 이러한 경우 `authorize` 메소드에 클래스 이름을 전달하면 됩니다. 클래스 이름은 액션을 승인할 때 어떤 policy가 사용될지 결정하는데 이용됩니다:

    /**
     * Create a new blog post.
     *
     * @param  Request  $request
     * @return Response
     */
    public function create(Request $request)
    {
        $this->authorize('create', Post::class);

        // The current user can create blog posts...
    }

<a name="via-blade-templates"></a>
### Via Blade Templates
### 블레이드 템플릿을 통해서

When writing Blade templates, you may wish to display a portion of the page only if the user is authorized to perform a given action. For example, you may wish to show an update form for a blog post only if the user can actually update the post. In this situation, you may use the `@can` and `@cannot` family of directives:

블레이드 템플릿을 작성할 때, 사용자가 주어진 액션을 수행 할 권한이 있는 경우에만 페이지를 일부를 보여주고자 할 수 있습니다. 예를 들어, 사용자가 실제로 블로그 포스트를 수정할 권한이 있는 경우에만 업데이트 버튼을 보여주고자 하는 경우가 그렇습니다. 이런 경우, `@can` 과 `@cannot` 지시어를 사용할 수 있습니다.

    @can('update', $post)
        <!-- The Current User Can Update The Post -->
    @elsecan('create', $post)
        <!-- The Current User Can Create New Post -->
    @endcan

    @cannot('update', $post)
        <!-- The Current User Can't Update The Post -->
    @elsecannot('create', $post)
        <!-- The Current User Can't Create New Post -->
    @endcannot

These directives are convenient shortcuts for writing `@if` and `@unless` statements. The `@can` and `@cannot` statements above respectively translate to the following statements:

이 지시어는 `@if` 와 `@unless` 를 작성하는 편리한 단축키와 같습니다. `@can` and `@cant` 구문은 다음의 구문으로 변환됩니다:

    @if (Auth::user()->can('update', $post))
        <!-- The Current User Can Update The Post -->
    @endif

    @unless (Auth::user()->can('update', $post))
        <!-- The Current User Can't Update The Post -->
    @endunless

#### Actions That Don't Require Models
#### 모델을 필요로 하지 않는 액션

Like most of the other authorization methods, you may pass a class name to the `@can` and `@cannot` directives if the action does not require a model instance:

다른 권한 인증 메소드 대부분과 같이, 액션이 모델을 필요로 하지 않는 경우 `@can` 과 `@cannot` 지시어에 클래스 이름을 전달할 수 있습니다:

    @can('create', App\Post::class)
        <!-- The Current User Can Create Posts -->
    @endcan

    @cannot('create', App\Post::class)
        <!-- The Current User Can't Create Posts -->
    @endcannot
