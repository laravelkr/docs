# 권한 확인

- [시작하기](#introduction)
- [Gate](#gates)
    - [Gate 작성하기](#writing-gates)
    - [액션을 수행할 수 있는 권한 확인하기](#authorizing-actions-via-gates)
    - [Gate 응답](#gate-responses)
    - [Gate 체크 로직의 후킹](#intercepting-gate-checks)
    - [인라인 권한 확인](#inline-authorization)
- [정책 생성하기](#creating-policies)
    - [정책 파일 생성하기](#generating-policies)
    - [정책 등록하기](#registering-policies)
- [정책 작성하기](#writing-policies)
    - [Policy 메소드](#policy-methods)
    - [Policy 응답](#policy-responses)
    - [모델없는 메소드](#methods-without-models)
    - [게스트 사용자](#guest-users)
    - [Policy 필터](#policy-filters)
- [정책을 사용하여 액션을 수행할 수 있는 권한 확인하기](#authorizing-actions-using-policies)
    - [User 모델을 통해서](#via-the-user-model)
    - [컨트롤러 헬퍼를 통해서](#via-controller-helpers)
    - [미들웨어를 통해서](#via-middleware)
    - [블레이드 템플릿을 통해서](#via-blade-templates)
    - [추가 컨텍스트 제공하기](#supplying-additional-context)

<a name="introduction"></a>
## 시작하기

내장된 [인증](/docs/{{version}}/authentication) 서비스를 제공하는 것 외에도 Laravel은 주어진 리소스에 대한 사용자의 권한을 확인하는 간단한 방법도 제공합니다. 예를 들어, 사용자가 인증을 받았더라도 애플리케이션에서 관리하는 특정 Eloquent 모델 또는 데이터베이스 레코드를 업데이트하거나 삭제할 권한이 없을 수 있습니다. Laravel의 권한 확인 기능은 앞의 예시와 같은 유형의 권한 확인을 관리하는 쉽고 조직적인 방법을 제공합니다.

라라벨은 권한 확인하는 두 가지 기본 방법 [gate](#gates)와 [정책](#creating-policies)을 제공합니다. gate와 정책을 라우트와 컨트롤러처럼 생각하십시오. gate는 권한 부여에 대한 간단한 closure-based 접근 방식을 제공하는 반면, 컨트롤러와 비슷한 정책은 특정 모델 또는 리소스를 중심으로 그룹화합니다. 이 문서에서는 먼저 gate를 탐색한 다음 정책을 검토합니다.

애플리케이션을 구성할 때 gate만 사용하거나, 정책만 사용하지 않아도 됩니다. 대부분의 애플리케이션에서 gate와 정책을 혼합하여 사용해도 되고, 아무런 문제가 없습니다! gate는 관리자 대시보드 보기와 같이 모델이나 리소스와 관련이 없는 작업에 가장 적합합니다. 반대로, 특정 모델이나 리소스에 대한 작업을 승인하려는 경우 정책을 사용해야 합니다.

<a name="gates"></a>
## Gate

<a name="writing-gates"></a>
### Gate 작성하기

> Gate는 Laravel의 인증 기능에 대한 기본 사항을 배울 수 있는 좋은 방법입니다. 그러나 강력한 Laravel 애플리케이션을 구축할 때 [정책](#creating-policies)을 사용하여 권한 부여 규칙을 구성하는 것을 고려해야 합니다.

Gate는 사용자가 주어진 작업을 수행할 권한이 있는지 여부를 결정하는 단순한 closure입니다. 일반적으로, gate는 `Gate` 파사드를 사용하여 `App\Providers\AuthServiceProvider` 클래스의 `boot` 메소드 내에서 정의됩니다. Gate는 항상 첫 번째 argument로 사용자 인스턴스를 받고, 관련된 Eloquent 모델과 같은 arguments를 선택적으로 추가할 수 있습니다.

이 예에서 우리는 사용자가 주어진 `App\Models\Post` 모델을 업데이트할 수 있는지 결정하기 위한 gate를 정의할 것입니다. gate는 사용자의 `id`와 게시물을 작성한 사용자의 `user_id`를 비교하여 수행합니다.

    use App\Models\Post;
    use App\Models\User;
    use Illuminate\Support\Facades\Gate;

    /**
     * Register any authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();

        Gate::define('update-post', function (User $user, Post $post) {
            return $user->id === $post->user_id;
        });
    }

컨트롤러와 마찬가지로, gate는 클래스 콜백 배열을 사용하여 정의할 수도 있습니다.

    use App\Policies\PostPolicy;
    use Illuminate\Support\Facades\Gate;

    /**
     * Register any authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();

        Gate::define('update-post', [PostPolicy::class, 'update']);
    }

<a name="authorizing-actions-via-gates"></a>
### 액션을 실행할 수 있는 권한 확인하기

Gate를 사용하여 작업의 권한 확인하려면 `Gate` 파사드에서 제공하는 `allows` 또는 `denies` 메서드를 사용해야 합니다. 현재 인증된 사용자를 이러한 메서드에 전달할 필요는 없습니다. Laravel은 자동으로 사용자를 gate closure로 전달합니다. 승인이 필요한 작업을 수행하기 전에 애플리케이션 컨트롤러 내에서 gate 권한 확인 메소드를 호출하는 것이 일반적입니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Models\Post;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Gate;

    class PostController extends Controller
    {
        /**
         * Update the given post.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  \App\Models\Post  $post
         * @return \Illuminate\Http\Response
         */
        public function update(Request $request, Post $post)
        {
            if (! Gate::allows('update-post', $post)) {
                abort(403);
            }

            // Update the post...
        }
    }

현재 인증된 사용자가 아닌 다른 사용자가 작업을 수행할 권한이 있는지 확인하려면 `Gate` 파사드에서 `forUser` 메서드를 사용할 수 있습니다.

    if (Gate::forUser($user)->allows('update-post', $post)) {
        // The user can update the post...
    }

    if (Gate::forUser($user)->denies('update-post', $post)) {
        // The user can't update the post...
    }

`any` 또는 `none` 메서드를 사용하여 한 번에 여러 액션을 인증 할 수 있습니다.

    if (Gate::any(['update-post', 'delete-post'], $post)) {
        // The user can update or delete the post...
    }

    if (Gate::none(['update-post', 'delete-post'], $post)) {
        // The user can't update or delete the post...
    }

<a name="authorizing-or-throwing-exceptions"></a>
#### 권한 확인 혹은 예외 처리

사용자가 주어진 작업을 수행하도록 허용되지 않은 경우, 자동으로 `Illuminate\Auth\Access\AuthorizationException`을 발생시키려면 `Gate` 파사드의 `authorize` 메소드를 사용할 수 있습니다. `AuthorizationException`의 인스턴스는 Laravel의 예외 핸들러에 의해 자동으로 403 HTTP 응답으로 변환됩니다.

    Gate::authorize('update-post', $post);

    // The action is authorized...

<a name="gates-supplying-additional-context"></a>
#### 추가 컨텍스트 제공하기

권한 확인을 위한 gate 메소드(`allows`, `denies`, `check`, `any`, `none`, `authorize`, `can`, `cannot`)와 [Blade directives](#via-blade-templates) (`@can`, `@cannot`, `@canany`)은 배열을 두 번째 argument로 받을 수 있습니다. 이러한 배열 요소는 gate closure에 parameters로 전달되며 권한 확인시 추가 컨텍스트에 사용할 수 있습니다.

    use App\Models\Category;
    use App\Models\User;
    use Illuminate\Support\Facades\Gate;

    Gate::define('create-post', function (User $user, Category $category, $pinned) {
        if (! $user->canPublishToGroup($category->group)) {
            return false;
        } elseif ($pinned && ! $user->canPinPosts()) {
            return false;
        }

        return true;
    });

    if (Gate::check('create-post', [$category, $pinned])) {
        // The user can create the post...
    }

<a name="gate-responses"></a>
### Gate 응답

지금까지 우리는 단순한 Boolean값을 반환하는 gate만 살펴보았습니다. 그러나 때로는 에러 메시지를 포함한 더욱 자세한 응답을 반환해야 할 때도 있습니다. 이때 gate에서 `Illuminate\Auth\Access\Response`를 반환할 수 있습니다.

    use App\Models\User;
    use Illuminate\Auth\Access\Response;
    use Illuminate\Support\Facades\Gate;

    Gate::define('edit-settings', function (User $user) {
        return $user->isAdmin
                    ? Response::allow()
                    : Response::deny('You must be an administrator.');
    });

Gate에서 권한 확인 응답을 반환할 때 `Gate::allows` 메소드는 여전히 단순한 Boolean 값을 반환합니다. 그러나 `Gate::inspect` 메소드를 이용하면 전체 권한 확인 응답을 반환할 수 있습니다.

    $response = Gate::inspect('edit-settings');

    if ($response->allowed()) {
        // The action is authorized...
    } else {
        echo $response->message();
    }

액션 수행에 권한이 없을 때 `AuthorizationException` 예외를 실행하도록 `Gate::authorize` 메소드를 사용한다면, 권한 확인 응답의 에러 메시지는 HTTP 응답으로 전달됩니다.

    Gate::authorize('edit-settings');

    // The action is authorized...

<a name="intercepting-gate-checks"></a>
### Gate 체크 로직의 후킹

때로는 특정 사용자에게 모든 권한을 허용하고자 할 수도 있습니다. 권한을 확인하는 모든 체크 로직 앞에서 실행되는 closure를 정의하기 위해서 `before` 메소드를 사용할 수 있습니다.

    use Illuminate\Support\Facades\Gate;

    Gate::before(function ($user, $ability) {
        if ($user->isAdministrator()) {
            return true;
        }
    });

`before` closure가 null이 아닌 값을 반환한다면, 이 값을 권한 확인의 결과 값으로 간주합니다.

권한을 확인하는 다른 모든 체크 로직 뒤에 특정한 closure를 실행하도록 `after` 메소드를 사용할 수 있습니다.

    Gate::after(function ($user, $ability, $result, $arguments) {
        if ($user->isAdministrator()) {
            return true;
        }
    });

`before` method와 유사하게, `after` closure가 null이 아닌 값을 반환하면 그 값은 체크 로직의 결과 값으로 간주됩니다.

<a name="inline-authorization"></a>
### 인라인 권한 확인

경우에 따라 현재 인증된 사용자가 해당 작업에 해당하는 전용 gate를 작성하지 않고 주어진 작업을 수행할 권한이 있는지 확인하고 싶을 수 있습니다. 라라벨은 `Gate::allowIf` 및 `Gate::denyIf` 메소드를 통해 이러한 유형의 "인라인" 인증 검사를 수행할 수 있습니다.

```php
use Illuminate\Support\Facades\Auth;

Gate::allowIf(fn ($user) => $user->isAdministrator());

Gate::denyIf(fn ($user) => $user->banned());
```

작업이 승인되지 않았거나 현재 인증된 사용자가 없는 경우 Laravel은 자동으로 `Illuminate\Auth\Access\AuthorizationException` 예외를 발생시킵니다. `AuthorizationException`의 인스턴스는 Laravel의 예외 핸들러에 의해 자동으로 403 HTTP 응답으로 변환됩니다.

<a name="creating-policies"></a>
## 정책 생성하기

<a name="generating-policies"></a>
### 정책 파일 생성하기

정책은 특정 모델 또는 리소스에 대한 권한을 확인하는 방법을 클래스로 만든 것입니다. 예를 들어, 블로그 애플리케이션의 경우, 게시물 생성 또는 업데이트와 같은 사용자 작업을 승인하기 위해 `App\Models\Post` 모델과 해당 `App\Policies\PostPolicy`가 있을 수 있습니다.

`make:policy` 아티즌 명령어를 사용하여 정책을 생성할 수 있습니다. 생성된 정책은 `app/Policies` 디렉토리에 있습니다. 만약 애플리케이션에서 이 디렉토리가 존재하지 않더라도, 라라벨이 디렉토리를 생성해줍니다.

    php artisan make:policy PostPolicy

`make:policy` 명령은 빈 policy 클래스를 생성합니다. 리소스 보기, 생성, 업데이트 및 삭제와 관련된 예제 policy 메서드로 클래스를 생성하려면 다음 명령을 실행할 때 `--model` 옵션을 제공할 수 있습니다.

    php artisan make:policy PostPolicy --model=Post

<a name="registering-policies"></a>
### 정책 등록하기

policy 클래스가 생성되면 등록해야 합니다. 정책을 등록하는 것은 주어진 모델 유형에 대한 권한 확인할 때 사용할 정책을 Laravel에 알리는 방법입니다.

Laravel 애플리케이션에 포함된 `App\Providers\AuthServiceProvider`에는 Eloquent 모델을 해당 정책에 매핑하는 `policies` 속성이 포함되어 있습니다. 정책을 등록하면 지정된 Eloquent 모델에 대한 권한 확인할 때 어떤 정책을 사용할지 Laravel에 지시합니다.

    <?php

    namespace App\Providers;

    use App\Models\Post;
    use App\Policies\PostPolicy;
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

<a name="policy-auto-discovery"></a>
#### 정책 자동 검색

직접 모델 정책을 등록하는 대신에, 라라벨은 모델과 정책은 표준 라라벨 네이밍 규칙을 따른다면 자동 검색 정책을 사용할 수 있습니다. 구체적으로 말하면, 정책은 모델이 포함된 디렉토리 또는 그 위의 `Policies` 디렉토리에 있어야합니다. 예를 들어 모델은 `app/Models` 디렉토리에 배치되고 정책은 `app/Policies` 디렉토리에 배치 할 수 있습니다. 이 상황에서 라라벨은 `app/Models/Policies` 다음에 `app/Policies` 에서 정책을 확인합니다. 또한 policy 클래스의 이름은 모델 이름과 일치해야 하며 `Policy` 접미사가 있어야 합니다. 따라서 `User` 모델의 policy 클래스는 `UserPolicy` 클래스가 됩니다.

고유한 정책 검색 로직을 정의하려면 `Gate::guessPolicyNamesUsing` 메소드를 사용하여 커스텀 정책 검색 callback을 등록할 수 있습니다. 일반적으로 이 메소드는 애플리케이션의 `AuthServiceProvider`의 `boot` 메소드에서 호출해야 합니다.

    use Illuminate\Support\Facades\Gate;

    Gate::guessPolicyNamesUsing(function ($modelClass) {
        // Return the name of the policy class for the given model...
    });

> {note} `AuthServiceProvider` 에 명시적으로 매핑된 모든 정책은 모든 잠재적으로 자동 검색된 정책 보다 우선됩니다.

<a name="writing-policies"></a>
## 정책 작성하기

<a name="policy-methods"></a>
### Policy 메소드

policy 클래스를 등록하고 나면, 권한을 확인 하고자 하는 각 액션에 대한 메소드를 추가해야 합니다. 예를 들어, 주어진 `App\Models\User`가 `App\Models\Post` 인스턴스를 수정할 수 있는지 확인하는 `PostPolicy` 클래스의 `update` 메소드를 정의해 보겠습니다.

`update` 메소드는 한개의 `User` 와 한개의 `Post` 인스턴스를 인자로 받고, 해당 사용자가 주어진 `Post`를 수정할 수 있는 권한이 가지고 있는지 결과를 `true` 또는 `false`를 반환해야합니다. 따라서 이 예제에서는, post의 `user_id`가 user의 `id` 와 일치하는지 확인 해보겠습니다.

    <?php

    namespace App\Policies;

    use App\Models\Post;
    use App\Models\User;

    class PostPolicy
    {
        /**
         * Determine if the given post can be updated by the user.
         *
         * @param  \App\Models\User  $user
         * @param  \App\Models\Post  $post
         * @return bool
         */
        public function update(User $user, Post $post)
        {
            return $user->id === $post->user_id;
        }
    }

정책은 권한을 확인하고자 하는 다양한 액션 만큼 필요한 메소드를 policy 클래스에 계속해서 정의하면 됩니다. 예를 들어 `Post`의 다양한 권한 확인을 위한 `view` 또는 `delete` 메소드를 정의할 수 있지만 policy 메서드는 여러분이 원하는 대로 자유롭게 작성할 수 있습니다.

만약 아티즌 명령어를 통해 policy 클래스를 생성할 때 `--model` 옵션을 사용했다면, 이미 `viewAny`, `view`, `create`, `update`, `delete`, `restore` 그리고 `forceDelete` 액션에 해당하는 메소드가 포함되어 있을 겁니다.

> {tip} 모든 정책은 Laravel [서비스 컨테이너](/docs/{{version}}/container)를 통해 해결되므로 policy 생성자에 필요한 종속성을 입력하여 자동으로 삽입할 수 있습니다.

<a name="policy-responses"></a>
### Policy 응답

지금까지 우리는 단순한 Boolean값을 반환하는 Policy 메소드만 살펴보았습니다. 그러나 때로는 에러 메시지를 포함한 더욱 자세한 응답을 반환해야 할 때도 있습니다. 이때 policy 메소드에서 `Illuminate\Auth\Access\Response` 인스턴스를 반환할 수 있습니다.

    use App\Models\Post;
    use App\Models\User;
    use Illuminate\Auth\Access\Response;

    /**
     * Determine if the given post can be updated by the user.
     *
     * @param  \App\Models\User  $user
     * @param  \App\Models\Post  $post
     * @return \Illuminate\Auth\Access\Response
     */
    public function update(User $user, Post $post)
    {
        return $user->id === $post->user_id
                    ? Response::allow()
                    : Response::deny('You do not own this post.');
    }

정책에서 권한 확인 응답을 반환할 때 `Gate::allows` 메소드는 여전히 단순한 Boolean 값을 반환합니다. 그러나 `Gate::inspect` 메소드를 이용하면 전체 권한 확인 응답을 반환할 수 있습니다.

    use Illuminate\Support\Facades\Gate;

    $response = Gate::inspect('update', $post);

    if ($response->allowed()) {
        // The action is authorized...
    } else {
        echo $response->message();
    }

액션에 권한이 없을 경우 `AuthorizationException`을 발생시키는 `Gate::authorize` 메소드를 사용할 때, 권한 확인 응답의 에러 메시지는 HTTP 응답으로 전달됩니다.

    Gate::authorize('update', $post);

    // The action is authorized...

<a name="methods-without-models"></a>
### 모델없는 메소드

일부 정책은 현재 인증된 사용자의 인스턴스만 수신합니다. 이 상황은 `create` 할 때 가장 일반적입니다. 예를 들어, 블로그를 만들 경우 사용자에게 게시물을 만들 권한이 있는지 확인하고 싶을 수 있습니다. 이러한 상황에서 policy 메서드는 사용자 인스턴스만 수신할 것으로 예상해야 합니다.

    /**
     * Determine if the given user can create posts.
     *
     * @param  \App\Models\User  $user
     * @return bool
     */
    public function create(User $user)
    {
        return $user->role == 'writer';
    }

<a name="guest-users"></a>
### 게스트 사용자

기본적으로, 들어오는 HTTP 요청이 인증 된 사용자에 의해 시작되지 않으면 모든 gate와 정책은 자동으로 `false` 를 반환합니다. 그러나 "optional" 타입 힌트를 선언하거나 User 인수에 대한 `null`을 기본값으로 제공하여 이러한 권한 검사가 Gate 및 정책이 전달되도록 허용 할 수 있습니다.

    <?php

    namespace App\Policies;

    use App\Models\Post;
    use App\Models\User;

    class PostPolicy
    {
        /**
         * Determine if the given post can be updated by the user.
         *
         * @param  \App\Models\User  $user
         * @param  \App\Models\Post  $post
         * @return bool
         */
        public function update(?User $user, Post $post)
        {
            return optional($user)->id === $post->user_id;
        }
    }

<a name="policy-filters"></a>
### Policy 필터

특정 사용자에게는, 주어진 정책 안의 모든 액션에 대해 권한을 가지고 있다고 처리하고 싶을 수도 있습니다. 이런 경우라면, 정책에 `before` 메소드를 정의하면 됩니다. `before` 메소드는 policy 클래스의 다른 메소드가 호출되기 전에 실행되는데, 원래의 policy 메소드가 실제로 호출되기 전에 해당 액션에 대한 권한을 확인합니다. 이 기능은 애플리케이션의 관리자가 어떤 액션이든 수행가능하도록 권한을 부여하는데 가장 흔하게 사용됩니다.

    use App\Models\User;

    /**
     * Perform pre-authorization checks.
     *
     * @param  \App\Models\User  $user
     * @param  string  $ability
     * @return void|bool
     */
    public function before(User $user, $ability)
    {
        if ($user->isAdministrator()) {
            return true;
        }
    }

특정 사용자에게 모든 권한을 허용하지 않으려면, `before` 메소드에서 `false`를 반환하면 됩니다. 만약 `null`이 반환되면, 권한을 확인하는 과정은 policy 메소드까지 도달하게 됩니다.

> {note} 클래스가 확인하려는 ability와 매칭되는 이름의 메소드를 포함하고 있지 않다면, policy 클래스의 `before` 메소드가 호출되지 않습니다.

<a name="authorizing-actions-using-policies"></a>
## 정책을 사용하여 액션의 권한을 확인하기

<a name="via-the-user-model"></a>
### User 모델을 통해서

라라벨 애플리케이션에 포함된 `App\Models\User` 모델에는 `can`과 `cannot`의 두 가지 유용한 권한 확인 방법이 포함되어 있습니다. `can` 및 `cannot` 메서드는 권한 확인하려는 액션의 이름과 관련 모델을 받습니다. 예를 들어 사용자가 지정된 `App\Models\Post` 모델을 업데이트할 권한이 있는지 확인합니다. 일반적으로 이것은 컨트롤러 메서드 내에서 수행됩니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Models\Post;
    use Illuminate\Http\Request;

    class PostController extends Controller
    {
        /**
         * Update the given post.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  \App\Models\Post  $post
         * @return \Illuminate\Http\Response
         */
        public function update(Request $request, Post $post)
        {
            if ($request->user()->cannot('update', $post)) {
                abort(403);
            }

            // Update the post...
        }
    }

만약 주어진 모델을 위한 [등록된 정책](#registering-policies)이 있다면, `can` 메소드는 자동으로 적절한 정책을 호출하고 불리언(boolean: true or false)형태의 결과를 반환합니다. 모델을 위한 정책이 등록되지 않은 경우, `can` 메소드는 주어진 액션 이름과 매칭되는 closure 기반의 gate 호출을 시도합니다.

<a name="user-model-actions-that-dont-require-models"></a>
#### 모델을 인자로 전달 받지 않는 액션

일부 작업은 모델 인스턴스가 필요하지 않은 `create`와 같은 policy 메서드에 해당할 수 있습니다. 이러한 경우에는 `can` 메소드에 클래스 이름을 전달하면 됩니다. 클래스 이름은 액션에 대한 권한을 확인 할 때 어떤 정책이 사용될지 결정하는데 이용됩니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Models\Post;
    use Illuminate\Http\Request;

    class PostController extends Controller
    {
        /**
         * Create a post.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return \Illuminate\Http\Response
         */
        public function store(Request $request)
        {
            if ($request->user()->cannot('create', Post::class)) {
                abort(403);
            }

            // Create the post...
        }
    }

<a name="via-controller-helpers"></a>
### 컨트롤러 헬퍼를 통해서

`App\Models\User` 모델에서 제공하는 유용한 메소드에 더해, 라라벨에서는 `App\Http\Controllers\Controller`라는 베이스 클래스를 상속받는 컨트롤러에서 사용할 수 있도록 `authorize` 메소드를 제공합니다.

`can` 메소드와 같이, 이 메소드는 권한을 확인 하고자 하는 액션의 이름과, 관련된 모델을 인자로 받습니다. 만약 액션이 허용되지 않는 다면, `authorize` 메소드는 라라벨의 exception 핸들러에 의해서 `403` 상태코드를 가진 HTTP 응답-response로 변환되는 `Illuminate\Auth\Access\AuthorizationException`를 발생시킵니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Models\Post;
    use Illuminate\Http\Request;

    class PostController extends Controller
    {
        /**
         * Update the given blog post.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  \App\Models\Post  $post
         * @return \Illuminate\Http\Response
         *
         * @throws \Illuminate\Auth\Access\AuthorizationException
         */
        public function update(Request $request, Post $post)
        {
            $this->authorize('update', $post);

            // The current user can update the blog post...
        }
    }

<a name="controller-actions-that-dont-require-models"></a>
#### 모델을 필요로 하지 않는 액션

앞서 논의한 바와 같이 `create`와 같은 일부 policy 메서드는 모델 인스턴스가 필요하지 않습니다. 이러한 상황에서는 클래스 이름을 `authorize` 메소드에 전달해야 합니다. 클래스 이름은 인증할 때 사용할 정책을 결정하는 데 사용됩니다.

    use App\Models\Post;
    use Illuminate\Http\Request;

    /**
     * Create a new blog post.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return \Illuminate\Http\Response
     *
     * @throws \Illuminate\Auth\Access\AuthorizationException
     */
    public function create(Request $request)
    {
        $this->authorize('create', Post::class);

        // The current user can create blog posts...
    }

<a name="authorizing-resource-controllers"></a>
#### 리소스 컨트롤러에서 권한 확인

[리소스 컨트롤러](/docs/{{version}}/controllers#resource-controllers)를 활용하는 경우, 컨트롤러의 생성자에서 `authorizeResource` 메서드를 사용할 수 있습니다. 이 메소드는 적절한 `can` 미들웨어 정의를 리소스 컨트롤러의 메소드에 첨부합니다.

`authorizeResource` 메소드는 모델의 클래스 이름을 첫 번째 argument로 받아들이고, 모델의 ID를 포함할 경로 요청 매개변수의 이름을 두 번째 argument로 받아들입니다. [리소스 컨트롤러](/docs/{{version}}/controllers#resource-controllers)가 `--model` 플래그를 사용하여 생성되어 필요한 메서드 서명과 유형 힌트가 있는지 확인해야 합니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Models\Post;
    use Illuminate\Http\Request;

    class PostController extends Controller
    {
        /**
         * Create the controller instance.
         *
         * @return void
         */
        public function __construct()
        {
            $this->authorizeResource(Post::class, 'post');
        }
    }

다음 컨트롤러 메서드는 해당 policy 메서드에 매핑됩니다. 요청이 지정된 컨트롤러 메서드로 라우팅되면 컨트롤러 메서드가 실행되기 전에 해당 policy 메서드가 자동으로 호출됩니다.

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

<a name="via-middleware"></a>
### 미들웨어를 통해서

라라벨에는 유입되는 Request-요청이 라우트 또는 컨트롤러에 도달하기도 전에 권한을 검사할 수 있는 미들웨어가 포함되어 있습니다. 기본적으로 `App\Http\Kernel` 클래스의 `can` 키에는 `Illuminate\Auth\Middleware\Authorize` 미들웨어가 할당되어 있습니다. 사용자가 포스트를 수정할 권한을 부여하는 `can` 미들웨어를 사용하는 예제를 살펴보겠습니다.

    use App\Models\Post;

    Route::put('/post/{post}', function (Post $post) {
        // The current user may update the post...
    })->middleware('can:update,post');

이 예제에서, `can` 미들웨어에 두개의 인자를 전달합니다. 첫번째는 권한을 확인하고자 하는 액션의 이름이고, 두번째는 policy 메소드에 전달하고자 하는 라우트 파라미터 입니다. 이 경우, [묵시적(implicit) 모델 바인딩](/docs/{{version}}/routing#implicit-binding)을 사용하고 있기 때문에, 하나의 `Post` 모델이 policy 메소드에 전달됩니다. 만약 사용자가 주어진 액션을 수행할 권한이 없다면, 미들웨어에 의해서 `403` 상태코드를 가지는 HTTP response-응답이 반환됩니다.

편의를 위해 `can` 메소드를 사용하여 `can` 미들웨어를 경로에 연결할 수도 있습니다.

    use App\Models\Post;

    Route::put('/post/{post}', function (Post $post) {
        // The current user may update the post...
    })->can('update', 'post');

<a name="middleware-actions-that-dont-require-models"></a>
#### 모델을 필요로 하지 않는 액션

다시 한번 이야기 하지만, `create`와 같은 몇몇 policy 메서드는 모델 인스턴스를 필요로 하지 않습니다. 이러한 경우 미들웨어에 클래스 이름을 전달하면 됩니다. 클래스 이름은 액션에 대한 권한을 확인 할 때 어떤 정책을 사용할지 결정하는데 이용됩니다.

    Route::post('/post', function () {
        // The current user may create posts...
    })->middleware('can:create,App\Models\Post');

문자열 미들웨어 정의 내에서 전체 클래스 이름을 지정하는 것은 번거로울 수 있습니다. 이러한 이유로, `can` 메소드를 사용하여 `can` 미들웨어를 경로에 연결하도록 선택할 수 있습니다.

    use App\Models\Post;

    Route::post('/post', function () {
        // The current user may create posts...
    })->can('create', Post::class);

<a name="via-blade-templates"></a>
### 블레이드 템플릿을 통해서

블레이드 템플릿을 작성할 때, 사용자가 주어진 액션을 수행 할 권한이 있는 경우에만 페이지를 일부를 보여주고자 할 수 있습니다. 예를 들어, 사용자가 실제로 블로그 포스트를 수정할 권한이 있는 경우에만 업데이트 버튼을 보여주고자 하는 경우가 그렇습니다. 이런 경우, `@can` 과 `@cannot` 지시어를 사용할 수 있습니다.

```html
@can('update', $post)
<!-- The current user can update the post... -->
@elsecan('create', App\Models\Post::class)
<!-- The current user can create new posts... -->
@else
<!-- ... -->
@endcan

@cannot('update', $post)
<!-- The current user cannot update the post... -->
@elsecannot('create', App\Models\Post::class)
<!-- The current user cannot create new posts... -->
@endcannot
```

이 지시어는 `@if` 및 `@unless` 문을 작성하기 위한 편리한 단축키입니다. 위의 `@can` 과 `@cannot` 문과 동일합니다.

```html
@if (Auth::user()->can('update', $post))
<!-- The current user can update the post... -->
@endif

@unless (Auth::user()->can('update', $post))
<!-- The current user cannot update the post... -->
@endunless
```

또한 사용자가 지정된 작업 배열에서 작업을 수행할 권한이 있는지 확인할 수도 있습니다. 그러기 위해서는 `@canany` 지시어를 사용하십시오.

```html
@canany(['update', 'view', 'delete'], $post)
<!-- The current user can update, view, or delete the post... -->
@elsecanany(['create'], \App\Models\Post::class)
<!-- The current user can create a post... -->
@endcanany
```

<a name="blade-actions-that-dont-require-models"></a>
#### 모델을 필요로 하지 않는 액션

다른 권한을 확인하는 메소드 대부분과 동일하게, 확인하고자 하는 액션이 모델을 필요로 하지 않는 경우 `@can` 과 `@cannot` 지시어에 클래스 이름을 전달할 수 있습니다.

```html
@can('create', App\Models\Post::class)
<!-- The current user can create posts... -->
@endcan

@cannot('create', App\Models\Post::class)
<!-- The current user can't create posts... -->
@endcannot
```

<a name="supplying-additional-context"></a>
### 추가 컨텍스트 제공하기

정책을 이용하여 액션에 대한 권한을 확인 할 때, 다양한 권한 확인 함수와 헬퍼 함수의 두 번째 인자로서 배열을 전달할 수 있습니다. 배열의 첫 번째 원소는 실행할 정책을 결정하고, 나머지 원소들은 정책 메소드의 파라미터로 전달되며 권한 확인 결정을 내릴 때 추가 컨텍스트로 이용될 수 있습니다. 예를 들어 아래의 `PostPolicy` 메소드의 정의는 추가로 `$category` 파라미터를 포함하고 있습니다.

    /**
     * Determine if the given post can be updated by the user.
     *
     * @param  \App\Models\User  $user
     * @param  \App\Models\Post  $post
     * @param  int  $category
     * @return bool
     */
    public function update(User $user, Post $post, int $category)
    {
        return $user->id === $post->user_id &&
               $user->canUpdateCategory($category);
    }

유저가 해당 포스트를 수정할 권한이 있는지 확인 할 때 policy 메소드를 다음과 같이 불러올 수 있습니다.

    /**
     * Update the given blog post.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \App\Models\Post  $post
     * @return \Illuminate\Http\Response
     *
     * @throws \Illuminate\Auth\Access\AuthorizationException
     */
    public function update(Request $request, Post $post)
    {
        $this->authorize('update', [$post, $request->category]);

        // The current user can update the blog post...
    }
