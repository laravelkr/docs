# 권한 확인

- [시작하기](#introduction)
- [Gate](#gates)
    - [Gates 작성하기](#writing-gates)
    - [액션을 수행할 수 있는 권한 확인하기](#authorizing-actions-via-gates)
    - [게이트 응답](#gate-responses)
    - [Gate 체크 로직의 후킹](#intercepting-gate-checks)
- [Policy 생성하기](#creating-policies)
    - [Policies 파일 생성하기](#generating-policies)
    - [Policies 등록하기](#registering-policies)
- [Policies 작성하기](#writing-policies)
    - [Policy 메소드](#policy-methods)
    - [Policy 응답](#policy-responses)
    - [모델없는 메소드](#methods-without-models)
    - [게스트 사용자](#guest-users)
    - [Policy 필터](#policy-filters)
- [Policy 를 사용하여 액션을 수행할 수 있는 권한 확인하기](#authorizing-actions-using-policies)
    - [User 모델을 통해서](#via-the-user-model)
    - [미들웨어를 통해서](#via-middleware)
    - [컨트롤러 헬퍼를 통해서](#via-controller-helpers)
    - [블레이드 템플릿을 통해서](#via-blade-templates)
    - [추가 컨텍스트 제공하기](#supplying-additional-context)

<a name="introduction"></a>
## 시작하기

별다른 설정 없이도 [인증-authentication](/docs/{{version}}/authentication) 기능을 제공하는 것에 더하여, 라라벨은 주어진 특정 리소스에 대하여 사용자가 액션을 수행할 수 있는지 권한을 확인하는(authorize) 간단한 방법을 제공합니다. 사용자 인증처럼, 권한 확인에 대한 라라벨의 접근 방식은 간단하며, 액션을 수행할 수 있는 권한을 확인하는 두가지 기본 방법이 있습니다. gate 와 policy 입니다.

Gate와 policy를 라우트와 컨트롤러처럼 생각하십시오. Gate는 간단한, 클로저 기반의 접근 방식을 제공하고, 컨트롤러와 같은 역할을 하는 policy는 특정 모델이나 리소스에 대한 로직을 정리 한 것입니다. 먼저 gate를 살펴본 후 policy를 설명하겠습니다.

애플리케이션을 구성할 때 gate 만을 사용하거나, policy 만을 사용하지 않아도 됩니다. 대부분의 애플리케이션에서 gate와 policy를 혼합하여 사용되며, 이렇게 사용해도 아무런 문제가 없습니다. 관리자 대시보드를 구성하는 것과 같이 모델과 리소스에 관련되지 않은 작업들은 주로 gate에 적합합니다. 이에 반해, policy는 특정 모델이나 리소스에 대한 작업 권한을 확인 할 때 사용해야합니다.

<a name="gates"></a>
## Gate

<a name="writing-gates"></a>
### Gates 작성하기

Gate는 사용자가 주어진 액션에 대해서 수행할 수 있는 권한이 있는지 없는지 확인하는 클로저로, 일반적으로 `App\Providers\AuthServiceProvider` 클래스에서 `Gate` 파사드를 사용하여 정의됩니다. Gate는 항상 사용자 인스턴스를 첫번째 인자로 받고, 관련된 Eloquent 모델과 같은 추가적인 인자들을 선택적으로 전달 받습니다.

    /**
     * Register any authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();

        Gate::define('edit-settings', function ($user) {
            return $user->isAdmin;
        });

        Gate::define('update-post', function ($user, $post) {
            return $user->id === $post->user_id;
        });
    }

Gate는 컨트롤러와 같이 `Class@method` 스타일의 콜백 문자열 형태로 정의할 수 있습니다.

    /**
     * Register any authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();

        Gate::define('update-post', 'App\Policies\PostPolicy@update');
    }

<a name="authorizing-actions-via-gates"></a>
### 액션을 실행할 수 있는 권한 확인하기

Gate를 사용하여 현재 사용자가 지정된 액션에 대한 권한을 가지고 있는지 확인하려면, `allows` 또는 `denies`메소드를 사용해야 합니다. 이 두 개의 메소드에 현재 인증된 사용자를 전달할 필요는 없습니다. 라라벨이 자동으로 Gate 클로저에 사용자를 전달합니다.

    if (Gate::allows('edit-settings')) {
        // The current user can edit settings
    }

    if (Gate::allows('update-post', $post)) {
        // The current user can update the post...
    }

    if (Gate::denies('update-post', $post)) {
        // The current user can't update the post...
    }

특정 사용자를 지정하여 액션을 수행 할 수 있는 권한이 있는지 확인하려는 경우, `Gate` 파사드의 `forUser` 메소드를 사용할 수 있습니다.

    if (Gate::forUser($user)->allows('update-post', $post)) {
        // The user can update the post...
    }

    if (Gate::forUser($user)->denies('update-post', $post)) {
        // The user can't update the post...
    }

`any` 또는 `none` 메소드로 한 번에 여러 액션을 인증 할 수 있습니다.

    if (Gate::any(['update-post', 'delete-post'], $post)) {
        // The user can update or delete the post
    }

    if (Gate::none(['update-post', 'delete-post'], $post)) {
        // The user cannot update or delete the post
    }

#### 권한 확인 혹은 예외 처리

액션의 권한을 확인한 뒤 유저가 해당 액션을 수행할 권한이 없을 때, 자동으로 `Illuminate\Auth\Access\AuthorizationException` 예외를 처리하고 싶다면, `Gate::authorize` 메소드를 사용할 수 있습니다. `AuthorizationException` 인스턴스는 자동으로 `403` HTTP 응답으로 변환됩니다.

    Gate::authorize('update-post', $post);

    // The action is authorized...

#### 추가 컨텍스트 제공하기

권한을 확인하는 게이트 메소드(`allows`, `denies`, `check`, `any`, `none`, `authorize`, `can`, `cannot`)와 [Blade directives](#via-blade-templates) (`@can`, `@cannot`, `@canany`)는 두 번째 인자로 배열을 전달받을 수 있습니다. 배열의 원소들은 파라미터로서 게이트로 전달되고 권한 결정을 내릴 때 추가 컨텍스트로 활용될 수 있습니다.

    Gate::define('create-post', function ($user, $category, $extraFlag) {
        return $category->group > 3 && $extraFlag === true;
    });

    if (Gate::check('create-post', [$category, $extraFlag])) {
        // The user can create the post...
    }

<a name="gate-responses"></a>
### 게이트 응답

지금까지 우리는 단순한 Boolean값을 반환하는 게이트만 살펴보았습니다. 그러나 때로는 에러 메시지를 포함한 더욱 자세한 응답을 반환해야 할 때도 있습니다. 이때 게이트에서 `Illuminate\Auth\Access\Response`를 반환할 수 있습니다.

    use Illuminate\Auth\Access\Response;
    use Illuminate\Support\Facades\Gate;

    Gate::define('edit-settings', function ($user) {
        return $user->isAdmin
                    ? Response::allow()
                    : Response::deny('You must be a super administrator.');
    });

게이트에서 권한 확인 응답을 반환할 때 `Gate::allows` 메소드는 여전히 단순한 Boolean 값을 반환합니다. 그러나 `Gate::inspect` 메소드를 이용하면 전체 권한 확인 응답을 반환할 수 있습니다.

    $response = Gate::inspect('edit-settings', $post);

    if ($response->allowed()) {
        // The action is authorized...
    } else {
        echo $response->message();
    }

물론, 액션 수행에 권한이 없을 때 `AuthorizationException` 예외를 실행하도록 `Gate::authorize` 메소드를 사용한다면, 권한 확인 응답의 에러 메시지는 HTTP 응답으로 전달됩니다.

    Gate::authorize('edit-settings', $post);

    // The action is authorized...

<a name="intercepting-gate-checks"></a>
### Gate 체크 로직의 후킹

때로는 특정 사용자에게 모든 권한을 허용하고자 할 수도 있습니다. 권한을 확인하는 모든 체크 로직 앞에서 실행되는 콜백을 정의하기 위해서 `before` 메소드를 사용할 수 있습니다.

    Gate::before(function ($user, $ability) {
        if ($user->isSuperAdmin()) {
            return true;
        }
    });

`before` 콜백이 null이 아닌 값을 반환한다면, 이 값을 권한 확인의 결과 값으로 간주합니다.

권한을 확인하는 다른 모든 체크 로직 뒤에 특정한 콜백을 실행하도록 `after` 메소드를 사용할 수 있습니다.

    Gate::after(function ($user, $ability, $result, $arguments) {
        if ($user->isSuperAdmin()) {
            return true;
        }
    });

`before` 체크로직과 유사하게, `after` 콜백이 null이 아닌 값을 반환하면 그 값은 체크 로직의 결과 값으로 간주됩니다.

<a name="creating-policies"></a>
## Policy 생성하기

<a name="generating-policies"></a>
### Policies 파일 생성하기

Policy는 특정 모델 또는 리소스에 대한 권한을 확인하는 방법을 클래스로 만든 것입니다. 예를 들어, 블로그 애플리케이션의 경우, `Post` 모델과 해당 포스트를 생성하거나 수정할 수 있는 액션을 허용하는 `PostPolicy`가 있을 수 있습니다.

`make:policy` [아티즌 명령어](/docs/{{version}}/artisan)를 사용하여 policy 클래스를 생성할 수 있습니다. 생성된 policy는 `app/Policies` 디렉토리에 있습니다. 만약 애플리케이션에서 이 디렉토리가 존재하지 않더라도, 라라벨이 디렉토리를 생성해줍니다.

    php artisan make:policy PostPolicy

`make:policy` 명령어는 비어 있는 policy 클래스를 생성합니다. 기본적인 "CRUD" policy 메소드가 이미 포함된 클래스를 생성하려면, 명령어를 실행할 때 `--model`을 지정하십시오.

    php artisan make:policy PostPolicy --model=Post

> {tip} 모든 policy는 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)에 의해서 의존성이 해결되기 때문에, policy의 생성자에 필요한 의존성을 타입 힌트 하기만 하면 자동으로 의존 객체들이 주입됩니다.

<a name="registering-policies"></a>
### Policy 등록하기

Policy를 생성하고 나면, 이를 등록해야 사용할 수 있습니다. 새롭게 설치한 라라벨 애플리케이션에 포함된 `AuthServiceProvider`는 Eloquent 모델과 해당 모델에 상응하는 policy에 매핑되는 `policies` 속성을 갖고 있습니다. Policy를 등록하는 것은 라라벨이 주어진 모델의 액션에 권한을 확인 할 때 어떤 policy를 사용 할 지 알려주게 됩니다.

    <?php

    namespace App\Providers;

    use App\Policies\PostPolicy;
    use App\Post;
    use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;
    use Illuminate\Support\Facades\Gate;

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

#### Policy Auto-Discovery

직접 Policy모델을 등록하는 대신에, 라라벨은 모델과 Policy이 표준 라라벨 네이밍 규칙을 따른다면 auto-discover Policy을 사용할 수 있습니다. 분명하게, Policy는 models-모델을 포함하는 디렉토리의 아래로 `Policies` 디렉토리를 위치해야합니다. 예를들어, models-모델은 `app` 디렉토리 아래에 위치한다면, Policy 는 `app/Policies` 디렉토리에 위치할 수 있습니다. 또한, Policy 이름은 model-모델 이름과 매칭되어야되며, `Policy` 접미사를 써야합니다. 예를들어, 모델 이름이 `User` 이라면 Policy는 `UserPolicy` 클래스의 이름으로 이용하면 됩니다.

만약 자신만의 Policy Discovery 로직을 사용하고 싶다면, `Gate::guessPolicyNamesUsing` 메소드를 커스텀 콜백을 등록 할 수 있습니다. 일반적으로, 이 메소드는 애플리케이션의 `AuthServiceProvider` 의 `boot` 메소드로 부터 불려집니다.

    use Illuminate\Support\Facades\Gate;

    Gate::guessPolicyNamesUsing(function ($modelClass) {
        // return policy class name...
    });

> {note} `AuthServiceProvider` 에 명시적으로 매핑된 모든 Policy는 모든 잠재적인 auto-discovered Policy 보다 우선됩니다.

<a name="writing-policies"></a>
## Policy 작성하기

<a name="policy-methods"></a>
### Policy 메소드

Policy를 등록하고 나면, 권한을 확인 하고자 하는 각 액션에 대한 메소드를 추가해야 합니다. 예를 들어, 주어진 `User`가 `Post` 인스턴스를 수정할 수 있는지 확인하는 `PostPolicy` 클래스의 `update` 메소드를 정의해 보겠습니다.

`update` 메소드는 한개의 `User` 와 한개의 `Post` 인스턴스를 인자로 받고, 해당 사용자가 주어진 `Post`를 수정할 수 있는 권한이 가지고 있는지 결과를 `true` 또는 `false`를 반환해야합니다. 따라서 이 예제에서는, post의 `user_id`가 user의 `id` 와 일치하는지 확인 해보겠습니다.

    <?php

    namespace App\Policies;

    use App\Post;
    use App\User;

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

Policy는 권한을 확인하고자 하는 다양한 액션 만큼 필요한 메소드를 policy 클래스에 계속해서 정의하면 됩니다. 예를 들어 `Post`에 권한을 확인하는 `view`나 `delete` 메소드를 정의할 수도 있습니다. Policy의 이름은 여러분이 원하는 대로 자유롭게 작성할 수 있습니다.

> {tip} 만약 아티즌 명령어를 통해 Policy 클래스를 생성할 때 `--model` 옵션을 사용했다면, 이미 `viewAny`, `view`, `create`, `update`, `delete`, `restore` 그리고 `forceDelete` 액션에 해당하는 메소드가 포함되어 있을 겁니다.

<a name="policy-responses"></a>
### Policy 응답

지금까지 우리는 단순한 Boolean값을 반환하는 Policy 메소드만 살펴보았습니다. 그러나 때로는 에러 메시지를 포함한 더욱 자세한 응답을 반환해야 할 때도 있습니다. 이때 Policy 메소드에서 `Illuminate\Auth\Access\Response`를 반환할 수 있습니다.

    use Illuminate\Auth\Access\Response;

    /**
     * Determine if the given post can be updated by the user.
     *
     * @param  \App\User  $user
     * @param  \App\Post  $post
     * @return \Illuminate\Auth\Access\Response
     */
    public function update(User $user, Post $post)
    {
        return $user->id === $post->user_id
                    ? Response::allow()
                    : Response::deny('You do not own this post.');
    }

Policy에서 권한 확인 응답을 반환할 때 `Gate::allows` 메소드는 여전히 단순한 Boolean 값을 반환합니다. 그러나 `Gate::inspect` 메소드를 이용하면 전체 권한 확인 응답을 반환할 수 있습니다.

    $response = Gate::inspect('update', $post);

    if ($response->allowed()) {
        // The action is authorized...
    } else {
        echo $response->message();
    }

물론, 액션 수행에 권한이 없을 때 `AuthorizationException` 예외를 실행하도록 `Gate::authorize` 메소드를 사용한다면, 권한 확인 응답의 에러 메시지는 HTTP 응답으로 전달됩니다.

    Gate::authorize('update', $post);

    // The action is authorized...

<a name="methods-without-models"></a>
### 모델없는 메소드

때로는 Policy 메소드는 권한을 확인하기 위해서 모델 인스턴스가 아닌, 현재 인증된 사용자만을 전달 받을 수도 있습니다. 이런 상황은 대부분 `create` 액션에 권한을 확인 할 때 나타납니다. 예를 들어 블로그를 만드는 경우, 사용자가 게시물을 생성할 수 있는 권한이 있는지 확인할 수 있습니다.

`create` 메소드와 같은 policy 메소드를 정의할 때는, 모델을 전달 받지 않습니다. 대신에, 인증된 사용자를 받는 메소드로 정의해야 합니다.

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

<a name="guest-users"></a>
### 게스트 사용자

기본적으로, 들어오는 HTTP 요청이 인증 된 사용자에 의해 시작되지 않으면 모든 Gate와 Policy는 자동으로 `false` 를 반환합니다. 그러나 "optional" 타입 힌트를 선언하거나 User 인수에 대한 `null`을 기본값으로 제공하여 이러한 권한 검사가 Gate 및 Policy에 전달되도록 허용 할 수 있습니다.

    <?php

    namespace App\Policies;

    use App\Post;
    use App\User;

    class PostPolicy
    {
        /**
         * Determine if the given post can be updated by the user.
         *
         * @param  \App\User  $user
         * @param  \App\Post  $post
         * @return bool
         */
        public function update(?User $user, Post $post)
        {
            return optional($user)->id === $post->user_id;
        }
    }

<a name="policy-filters"></a>
### Policy 필터

특정 사용자에게는, 주어진 policy 안의 모든 액션에 대해 권한을 가지고 있다고 처리하고 싶을 수도 있습니다. 이런 경우라면, policy에 `before` 메소드를 정의하면 됩니다. `before` 메소드는 policy에서 다른 메소드가 호출되기 전에 실행되는데, 원래의 policy 메소드가 실제로 호출되기 전에 해당 액션에 대한 권한을 확인합니다. 이 기능은 애플리케이션의 관리자가 어떤 액션이든 수행가능하도록 권한을 부여하는 데 가장 흔하게 사용됩니다.

    public function before($user, $ability)
    {
        if ($user->isSuperAdmin()) {
            return true;
        }
    }

반대로 특정 사용자에게 모든 권한을 허용하지 않으려면, `before` 메소드에서 `false`를 반환하면 됩니다. 만약 `null`이 반환되면, 권한을 확인하는 과정은 policy 메소드까지 도달하게 됩니다.

> {note} 클래스가 확인하려는 ability와 매칭되는 이름의 메소드를 포함하고 있지 않다면, policy 클래스의 `before` 메소드가 호출되지 않습니다.

<a name="authorizing-actions-using-policies"></a>
## Policy를 사용하여 액션의 권한을 확인하기

<a name="via-the-user-model"></a>
### User 모델을 통해서

라라벨 애플리케이션에 포함되어 있는 `User` 모델은 액션을 수행할 수 있는지에 대한 권한을 확인하는데 사용가능한 두가지 유용한 메소드를 갖고 있습니다. `can` 과 `cant` 입니다. `can` 메소드는 권한을 확인 하고자 하는 액션, 그리고 연관된 모델을 인자로 받습니다. 예를 들어 사용자에게 주어진 `Post` 모델을 수정할 권한이 있는지 확인해보겠습니다.

    if ($user->can('update', $post)) {
        //
    }

만약 주어진 모델을 위한 [Policy 가 등록되어](#registering-policies)있다면, `can` 메소드는 자동으로 적절한 policy를 호출하고 불리언(boolean: true or false)형태의 결과를 반환합니다. 모델을 위한 policy가 등록되지 않은 경우, `can` 메소드는 주어진 액션 이름과 매칭되는 클로저 기반의 Gate 호출을 시도합니다.

#### 모델을 인자로 전달 받지 않는 액션

이전에 말했다시피 `create`와 같은 몇몇 액션은 모델 인스턴스를 필요로 하지 않습니다. 이러한 경우에는 `can` 메소드에 클래스 이름을 전달하면 됩니다. 클래스 이름은 액션에 대한 권한을 확인 할 때 어떤 policy가 사용될지 결정하는데 이용됩니다.

    use App\Post;

    if ($user->can('create', Post::class)) {
        // Executes the "create" method on the relevant policy...
    }

<a name="via-middleware"></a>
### 미들웨어를 통해서

라라벨에는 유입되는 Request-요청이 라우트 또는 컨트롤러에 도달하기도 전에 권한을 검사할 수 있는 미들웨어가 포함되어 있습니다. 기본적으로 `App\Http\Kernel` 클래스의 `can` 키에는 `Illuminate\Auth\Middleware\Authorize` 미들웨어가 할당되어 있습니다. 사용자가 블로그 포스트를 수정할 권한을 부여하는 `can` 미들웨어를 사용하는 예제를 살펴보겠습니다.

    use App\Post;

    Route::put('/post/{post}', function (Post $post) {
        // The current user may update the post...
    })->middleware('can:update,post');

이 예제에서, `can` 미들웨어에 두개의 인자를 전달합니다. 첫번째는 권한을 확인하고자 하는 액션의 이름이고, 두번째는 policy 메소드에 전달하고자 하는 라우트 파라미터 입니다. 이 경우, [묵시적(implicit) 모델 바인딩](/docs/{{version}}/routing#implicit-binding)을 사용하고 있기 때문에, 하나의 `Post` 모델이 policy 메소드에 전달됩니다. 만약 사용자가 주어진 액션을 수행할 권한이 없다면, 미들웨어에 의해서 `403` 상태코드를 가지는 HTTP response-응답이 생성됩니다.

#### 모델을 필요로 하지 않는 액션

다시 한번 이야기 하지만, `create`와 같은 몇몇 액션은 모델 인스턴스를 필요로 하지 않습니다. 이러한 경우 미들웨어에 클래스 이름을 전달하면 됩니다. 클래스 이름은 액션에 대한 권한을 확인 할 때 어떤 policy가 사용될지 결정하는데 이용됩니다.

    Route::post('/post', function () {
        // The current user may create posts...
    })->middleware('can:create,App\Post');

<a name="via-controller-helpers"></a>
### 컨트롤러 헬퍼를 통해서

`User` 모델에서 제공하는 유용한 메소드에 더해, 라라벨에서는 `App\Http\Controllers\Controller`라는 베이스 클래스를 상속받는 컨트롤러에서 사용할 수 있도록 `authorize` 메소드를 제공합니다. `can` 메소드와 같이, 이 메소드는 권한을 확인 하고자 하는 액션의 이름과, 관련된 모델을 인자로 받습니다. 만약 액션이 허용되지 않는 다면, `authorize` 메소드는 라라벨의 exception 핸들러에 의해서 `403` 상태코드를 가진 HTTP 응답-response로 변환되는 `Illuminate\Auth\Access\AuthorizationException`를 발생시킵니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Post;
    use Illuminate\Http\Request;

    class PostController extends Controller
    {
        /**
         * Update the given blog post.
         *
         * @param  Request  $request
         * @param  Post  $post
         * @return Response
         * @throws \Illuminate\Auth\Access\AuthorizationException
         */
        public function update(Request $request, Post $post)
        {
            $this->authorize('update', $post);

            // The current user can update the blog post...
        }
    }

#### 모델을 필요로 하지 않는 액션

앞서 계속해서 이야기한 바와 같이, `create`와 같은 몇몇 액션은 모델 인스턴스를 필요로 하지 않습니다. 이러한 경우 `authorize` 메소드에 클래스 이름을 전달하면 됩니다. 클래스 이름은 액션의 권한을 확인 할 때 어떤 policy가 사용될지 결정하는데 이용됩니다.

    /**
     * Create a new blog post.
     *
     * @param  Request  $request
     * @return Response
     * @throws \Illuminate\Auth\Access\AuthorizationException
     */
    public function create(Request $request)
    {
        $this->authorize('create', Post::class);

        // The current user can create blog posts...
    }

#### 리소스 컨트롤러에서 권한 확인

[리소스 컨트롤러](/docs/{{version}}/controllers##resource-controllers)를 사용한다면, 컨트롤러의 생성자 안에서 `authorizeResource` 메소드를 사용할 수 있습니다. 이 메소드는 `can` 미들웨어의 정의사항을 리소스 컨트롤러의 메소드에 추가하게 됩니다.

`authorizeResource` 메소드는 모델의 클래스 이름을 첫번째 인자로, 해당 모델의 ID를 파라미터로 받는 라우트의 이름을 두번째 인자로 전달받습니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Post;
    use Illuminate\Http\Request;

    class PostController extends Controller
    {
        public function __construct()
        {
            $this->authorizeResource(Post::class, 'post');
        }
    }

다음 컨트롤러 메소드는 해당 policy 메소드에 매핑됩니다.

| Controller Method | Policy Method |
| --- | --- |
| index | viewAny |
| show | view |
| create | create |
| store | create |
| edit | update |
| update | update |
| destroy | delete |

> {tip} `make:policy` 명령어에 `--model` 옵션을 지정해서 주어진 모델에 대한 Policy 클래스를 생성할 수 있습니다. `php artisan make:policy PostPolicy --model=Post`.

<a name="via-blade-templates"></a>
### 블레이드 템플릿을 통해서

블레이드 템플릿을 작성할 때, 사용자가 주어진 액션을 수행 할 권한이 있는 경우에만 페이지를 일부를 보여주고자 할 수 있습니다. 예를 들어, 사용자가 실제로 블로그 포스트를 수정할 권한이 있는 경우에만 업데이트 버튼을 보여주고자 하는 경우가 그렇습니다. 이런 경우, `@can` 과 `@cannot` 지시어를 사용할 수 있습니다.

    @can('update', $post)
        <!-- The Current User Can Update The Post -->
    @elsecan('create', App\Post::class)
        <!-- The Current User Can Create New Post -->
    @endcan

    @cannot('update', $post)
        <!-- The Current User Cannot Update The Post -->
    @elsecannot('create', App\Post::class)
        <!-- The Current User Cannot Create A New Post -->
    @endcannot

이 지시어는 `@if` 와 `@unless` 를 작성하는 편리한 단축키와 같습니다. `@can` and `@cant` 구문은 다음의 구문으로 변환됩니다.

    @if (Auth::user()->can('update', $post))
        <!-- The Current User Can Update The Post -->
    @endif

    @unless (Auth::user()->can('update', $post))
        <!-- The Current User Cannot Update The Post -->
    @endunless

또한 제시된 권한 리스트를 통해 해당 유저가 권한이 있는지 확인할 수 있습니다. 그러기 위해서는 `@canany` 지시어를 사용하십시오.

    @canany(['update', 'view', 'delete'], $post)
        // The current user can update, view, or delete the post
    @elsecanany(['create'], \App\Post::class)
        // The current user can create a post
    @endcanany

#### 모델을 필요로 하지 않는 액션

다른 권한을 확인하는 메소드 대부분과 동일하게, 확인하고자 하는 액션이 모델을 필요로 하지 않는 경우 `@can` 과 `@cannot` 지시어에 클래스 이름을 전달할 수 있습니다.

    @can('create', App\Post::class)
        <!-- The Current User Can Create Posts -->
    @endcan

    @cannot('create', App\Post::class)
        <!-- The Current User Can't Create Posts -->
    @endcannot

<a name="supplying-additional-context"></a>
### 추가 컨텍스트 제공하기

Policy를 이용하여 액션에 대한 권한을 확인 할 때, 다양한 권한 확인 함수와 헬퍼 함수의 두 번째 인자로서 배열을 전달할 수 있습니다. 배열의 첫 번째 원소는 실행할 Policy룰 결정하고, 나머지 원소들은 Policy 메소드의 파라미터로 전달되며 권한 확인 결정을 내릴 때 추가 컨텍스트로 이용될 수 있습니다. 예를 들어 아래의 `PostPolicy` 메소드의 정의는 추가로 `$category` 파라미터를 포함하고 있습니다.

    /**
     * Determine if the given post can be updated by the user.
     *
     * @param  \App\User  $user
     * @param  \App\Post  $post
     * @param  int  $category
     * @return bool
     */
    public function update(User $user, Post $post, int $category)
    {
        return $user->id === $post->user_id &&
               $category > 3;
    }

유저가 해당 포스트를 수정할 권한이 있는지 확인 할 때 Policy 메소드를 다음과 같이 불러올 수 있습니다.

    /**
     * Update the given blog post.
     *
     * @param  Request  $request
     * @param  Post  $post
     * @return Response
     * @throws \Illuminate\Auth\Access\AuthorizationException
     */
    public function update(Request $request, Post $post)
    {
        $this->authorize('update', [$post, $request->input('category')]);

        // The current user can update the blog post...
    }
