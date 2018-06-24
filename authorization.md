# Authorization-승인

- [소개](#introduction)
- [Abilities 정의하기](#defining-abilities)
- [Abilities 체크하기](#checking-abilities)
	- [Gate 파사드를 통한 방법](#via-the-gate-facade)
	- [User 모델을 통한 방법](#via-the-user-model)
	- [블레이드 템플릿 안에서 사용하기](#within-blade-templates)
    - [Form Requests 안에서 사용하기](#within-form-requests)
- [Policies](#policies)
    - [Creating Policies](#creating-policies)
	- [Policies 생성하기](#creating-policies)
    - [Writing Policies](#writing-policies)
	- [Policies 작성하기](#writing-policies)
    - [Checking Policies](#checking-policies)
	- [Policy 확인하기](#checking-policies)
- [컨트롤러에서 Authorization-승인하기](#controller-authorization)

<a name="introduction"></a>
## 소개

별다른 설정 없이도, [인증](/docs/{{version}}/authentication) 서비스 활용할 수 있는데에 더하여, 라라벨은 authorization-승인 로직을 처리하고 resources에 접근을 제어하도록 하는 간단한 방법을 제공합니다. authorization-승인 로직을 구성 하는데 다양한 메소드와 헬퍼들을 사용할 수 있으며 이 문서에서 하나씩 알아볼 것입니다. 

> **참고:** authorization-승인은 Laravel 5.1.11에서 추가되었습니다. 이 기능들을 애플리케이션과 통합하기 전에 [업그레이드 가이드](/docs/{{version}}/upgrade)를 참고하시기 바랍니다. 

<a name="defining-abilities"></a>
## Abilities 정의하기

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
	        	return $user->id === $post->user_id;
	        });
	    }
	}

`$user`가 `NULL`이 아닌지 확인하지 않았다는 것에 유의하십시오. 인증된 사용자가 없거나 특정 사용자가 `forUser` 메소드로 명시되지 않았을 경우 `Gate`는 **모든 ability**에 대해 자동으로 `false`를 반환할 것입니다. 

#### 클래스 기반의 ability

`Closures`를 authorization-승인을 위한 콜백으로 등록하는 것 이외에도 클래스 이름과 메소드를 포함한 string을 전달하여 클래스 메소드를 등록할 수 있습니다. 필요한 경우 [서비스 컨테이너](/docs/{{version}}/container)에 의해 클래스에 의존성 주입이 될 것입니다.

    $gate->define('update-post', 'Class@method');

<a name="intercepting-all-checks"></a>
<a name="intercepting-authorization-checks"></a>
#### Authorization-승인 체크 로직 수행의 전,후에 필요한 로직 수행하기

때로는, 특정 사용자에게 모든 ability를 부여하고 싶을 수 있습니다. 이 경우 `before` 메소드로 다른 모든 authorization-승인 점검이 이뤄지기 전에 실행될 콜백을 정의합니다. 

    $gate->before(function ($user, $ability) {
        if ($user->isSuperAdmin()) {
            return true;
        }
    });

`before` 콜백이 null이 아닌 결과를 반환한다면 그 결과값은 확인 결과로 간주될 것입니다.

`after` 메소드로 매 authorization-승인 체크 뒤에 콜백이 실행되도록 정의할 수 있습니다. 하지만 `after` 콜백으로 authorization-승인 확인의 결과값을 변경할 수는 없습니다. :

    $gate->after(function ($user, $ability, $result, $arguments) {
        //
    });

<a name="checking-abilities"></a>
## Abilities 체크하기

<a name="via-the-gate-facade"></a>
### Gate 파사드를 통한 방법

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

물론 `allows` 메소드는 단순하게 `denies` 메소드의 반대로, 액션-동작이 승인되면 `true`를 반환합니다. `check` 메소드는 `allows` 메소드의 또 다른 이름입니다. 

#### 지정된 사용자에 대한 Abilities 확인하기

**현재 승인된 사용자 외의 다른 사용자**가 특정 ability를 가지고 있는지 확인하기 위해서 `Gate` 파사드를 사용하고자 한다면, `forUser` 메소드를 사용하면 됩니다: 

	if (Gate::forUser($user)->allows('update-post', $post)) {
		//
	}

#### 여러개의 인자 전달하기

당연하게도, ability 콜백은 여러개의 인자를 전달 받을 수 있습니다. 

	Gate::define('delete-comment', function ($user, $post, $comment) {
		//
	});

어떤 ability가 여러개의 인자를 필요로 한다면 단순히 인자들의 배열을 `Gate` 메소드로 전달하면 됩니다: 

	if (Gate::allows('delete-comment', [$post, $comment])) {
		//
	}

<a name="via-the-user-model"></a>
### User 모델을 통한 방법

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

물론 `can` 메소드는 단순하게, `cannot` 메소드의 반대의 동작입니다: 

	if ($request->user()->can('update-post', $post)) {
		// Update Post...
	}

<a name="within-blade-templates"></a>
### 블레이드 템플릿 안에서 사용하기

보다 편리하게, 라라벨은 현재 승인된 사용자가 특정 ability를 가지고 있는지 빠르게 확인하기 위한 `@can` 블레이드 지시어를 제공합니다. 예를 들어: 

	<a href="/post/{{ $post->id }}">View Post</a>

	@can('update-post', $post)
		<a href="/post/{{ $post->id }}/edit">Edit Post</a>
	@endcan

`@can` 지시어와 `@else` 지시어를 조합할 수도 있습니다. 

	@can('update-post', $post)
		<!-- The Current User Can Update The Post -->
	@else
		<!-- The Current User Can't Update The Post -->
	@endcan

<a name="within-form-requests"></a>
### Form Requests 안에서 사용하기

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
## Policies-정책

<a name="creating-policies"></a>
### Policies 생성하기

규모가 큰 애플리케이션에서 모든 승인-authorization 로직을 `AuthServiceProvider`에서 정의하는 것은 번거로울 수 있기 때문에 라라벨은 "Policy" 클래스로 authorization-승인 로직을 나눌 수 있도록 해줍니다. Policies-정책들은 authorization-승인 로직들이 승인하는 자원에 따라 승인 로직을 그룹화시킨 순수 PHP 클래스 입니다. 

먼저, `Post` 모델의 authorization-승인을 관리할 policy 클래스를 생성합니다. `make:policy` [아티즌 명령어](/docs/{{version}}/artisan)를 통해서 policy 클래스를 생성할 수 있습니다. 생성된 policy은 `app/Policies` 디렉토리에 위치합니다:

	php artisan make:policy PostPolicy

#### Policies 등록하기

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
### Policies 작성하기

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

policy가 승인하는 다양한 ability들을 위해 필요한 추가적인 메소드를 계속해서 정의할 수 있습니다. 예로 들어 다양한 `Post` 동작들을 승인하기 위해 `show`, `destroy`, 혹은 `addComment` 메소드를 정의할 수 있습니다.   

> **참고:** 모든 policy들은 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)를 통해 의존성이 해결되며, 이는 policy의 constructor에 의존객체들을  타입힌트하면 자동으로 주입된다는 것을 의미합니다. 

#### 모든 체크를 수행하기 전,후에 필요한 로직 추가하기

종종 특정 사용자에게 모든 ability을 부여하고 싶을 수도 있습니다. 이 경우에는 policy에 `before` 메소드를 정의하면 됩니다. 이 메소드는 다른 모든 authorization을 확인하기 전에 실행될 것입니다: 

    public function before($user, $ability)
    {
        if ($user->isSuperAdmin()) {
            return true;
        }
    }

`before` 메소드가 null을 반환하지 않았다면 그 결과값이 확인의 결과로 간주될 것입니다. 

<a name="checking-policies"></a>
### Policy 확인하기

Policy 메소드는 `Closure` 기반의 authorization-승인 콜백과 동일한 방식으로 호출됩니다. `Gate` 파사드, `User` 모델, `@can` 블레이드 지시어 혹은 policy 헬퍼를 사용할 수 있습니다. 

#### Gate 파사드를 통한 방법

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

#### User 모델을 통한 방법

`User` 모델의 `can` 과 `cannot` 메소드 또한 주어진 인자들에 대해서 사용 가능한 policy가 있다면, 자동으로 사용합니다. 이 메소드들은 애플리케이션이 획득한 `User` 인스턴스에 대한 동작을 승인할 수 있는 편리한 방법을 제공합니다. 

	if ($user->can('update', $post)) {
		//
	}

	if ($user->cannot('update', $post)) {
		//
	}

#### 블레이드 템플릿 안에서 사용하기

`@can` 블레이드 지시어 또한 주어진 인자들에 의해 사용 가능할 때 policy들을 활용할 것입니다. 

	@can('update', $post)
		<!-- The Current User Can Update The Post -->
	@endcan

#### Policy 헬퍼를 통한 방법

글로벌 `policy` 헬퍼 함수는 주어진 클래스 인스턴스에 따라 `Policy` 클래스를 찾을 수 있게 해줍니다. 예를 들어 `PostPolicy` 클래스의 인스턴스를 갖기 위해 `policy` 헬퍼에 `Post` 인스턴스를 전달할 수 있습니다.:  

	if (policy($post)->update($user, $post)) {
		//
	}

<a name="controller-authorization"></a>
## 컨트롤러에서 Authorization-승인하기

기본적으로 `App\Http\Controllers\Controller` 베이스 클래스는 `AuthorizesRequests` 트레이트-trait을 사용합니다. 이 트레이트-trait는 `authorize` 메소드를 제공하여 특정 동작에 대해 빠르게 승인하거나, 승인되지 않을 경우 `HttpException`을 던지게 됩니다. 

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

동작이 승인되면 컨트롤러는 계속해서 정상적으로 동작 할 것입니다. 하지만 `authorize` 메소드에서 동작이 승인되지 않았다고 판정될 경우 `HttpException`이 던져지고 이에 따라 자동적으로 `403 Not Authorized` 상태 코드를 포함한 HTTP 응답이 생성될 것입니다. `authorize` 메소드는 단 한 줄의 코드로 빠르고 편리하게 특정 동작을 승인하거나 예외를 던질 수 있는 방법입니다. 

`AuthorizesRequests` 트레이트-trait는 현재 승인된 사용자가 아닌 특정 사용자에 대한 동작을 승인하도록 하는 `authorizeForUser` 메소드를 제공합니다: 

	$this->authorizeForUser($user, 'update', $post);

#### 자동으로 Policy 메소드 결정하기

빈번하게, policy의 메소드는 컨트롤러의 메소드에 대응 될 것입니다. 예를 들어, 앞에서 살펴본 `update` 메소드와 같이 컨트롤러 메소드와 policy 메소드는 `update`라는 같은 이름을 가집니다. 

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
