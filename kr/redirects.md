# HTTP Redirects
# HTTP 리다이렉트

- [Creating Redirects](#creating-redirects)
- [리다이렉트 생성하기](#creating-redirects)
- [Redirecting To Named Routes](#redirecting-named-routes)
- [이름이 지정된 라우트로 리다이렉트 하기](#redirecting-named-routes)
- [Redirecting To Controller Actions](#redirecting-controller-actions)
- [컨트롤러 액션으로 리다이렉트 하기](#redirecting-controller-actions)
- [Redirecting With Flashed Session Data](#redirecting-with-flashed-session-data)
- [세션의 임시 데이터와 함께 리다이렉트 하기](#redirecting-with-flashed-session-data)

<a name="creating-redirects"></a>
## Creating Redirects
## 리다이렉트 생성하기

Redirect responses are instances of the `Illuminate\Http\RedirectResponse` class, and contain the proper headers needed to redirect the user to another URL. There are several ways to generate a `RedirectResponse` instance. The simplest method is to use the global `redirect` helper:

리다이렉트 Response 는 `Illuminate\Http\RedirectResponse` 클래스의 인스턴스이며, 사용자를 다른 URL로 리다이렉트하는데 필요한 적절한 헤더를 포함하고 있습니다. `RedirectResponse` 인스턴스를 생성하려면 여러가지 방법이 있습니다. 가장 쉬운 방법은 글로벌 `redirect` 헬퍼 함수를 사용하는 것입니다.

    Route::get('dashboard', function () {
        return redirect('home/dashboard');
    });

Sometimes you may wish to redirect the user to their previous location, such as when a submitted form is invalid. You may do so by using the global `back` helper function. Since this feature utilizes the [session](/docs/{{version}}/session), make sure the route calling the `back` function is using the `web` middleware group or has all of the session middleware applied:

때때로, 전송된 form 양식이 유효하지 않은 경우와 같이 사용자를 이전 위치로 리다이렉트 시키고자 할 수 있습니다. 글로벌 `back` 헬퍼 함수를 사용하여 그렇게 할 수 있습니다. 이 기능은 [세션](/docs/{{version}}/session)을 사용하기 때문에 `back` 함수를 사용하는 라우트 호출은 `web` 미들웨어 그룹 안에 있거나 세션 미들웨어가 적용되어 있어야 합니다.

    Route::post('user/profile', function () {
        // Validate the request...

        return back()->withInput();
    });

<a name="redirecting-named-routes"></a>
## Redirecting To Named Routes
## 이름이 지정된 라우트로 리다이렉트 하기

When you call the `redirect` helper with no parameters, an instance of `Illuminate\Routing\Redirector` is returned, allowing you to call any method on the `Redirector` instance. For example, to generate a `RedirectResponse` to a named route, you may use the `route` method:

`redirect` 헬퍼가 아무런 인자 없이 호출될 때에는, `Illuminate\Routing\Redirector` 인스턴스가 반환되어, `Redirector` 인스턴스의 메소드를 사용할 수 있습니다. 다음과 같이 이름이 지정된 라우트에 대한 `RedirectResponse` 를 생성하려면 `route` 메소드를 사용할 수 있습니다.

    return redirect()->route('login');

If your route has parameters, you may pass them as the second argument to the `route` method:

라우트가 인자를 받아야 한다면, `route` 메소드의 두번째 인자로 이를 전달할 수 있습니다.

    // For a route with the following URI: profile/{id}

    return redirect()->route('profile', ['id' => 1]);

#### Populating Parameters Via Eloquent Models
#### Eloquent 모델을 통한 파라미터 채우기

If you are redirecting to a route with an "ID" parameter that is being populated from an Eloquent model, you may pass the model itself. The ID will be extracted automatically:

Eloquent 모델에 의해서 채워지는 "ID" 파라미터를 가진 라우트로 리다이렉트 하는 경우, 모델 그 자체를 전달할 수 있습니다. ID 는 자동으로 추출됩니다.

    // For a route with the following URI: profile/{id}

    return redirect()->route('profile', [$user]);

If you would like to customize the value that is placed in the route parameter, you should override the `getRouteKey` method on your Eloquent model:

만약 라우트 파라미터에 위치한 값을 커스터마이징 하려면, Eloquent 모델의 `getRouteKey` 메소드를 오버라이드해야합니다.

    /**
     * Get the value of the model's route key.
     *
     * @return mixed
     */
    public function getRouteKey()
    {
        return $this->slug;
    }

<a name="redirecting-controller-actions"></a>
## Redirecting To Controller Actions
## 컨트롤러 액션으로 리다이렉트 하기

You may also generate redirects to [controller actions](/docs/{{version}}/controllers). To do so, pass the controller and action name to the `action` method: 

또한 [컨트롤러 액션](/docs/{{version}}/controllers)으로 리다이렉트하는 응답을 생성할 수도 있습니다. 이렇게 하기 위해서는, 컨트롤러와 액션의 이름을 `action` 메소드에 전달하면 됩니다. 

    use App\Http\Controllers\HomeController;

    return redirect()->action([HomeController::class, 'index']);

If your controller route requires parameters, you may pass them as the second argument to the `action` method:

컨트롤러 라우트에 파라미터가 필요한 경우, `action` 메소드의 두번째 인자로 전달하면 됩니다.

    return redirect()->action(
        [UserController::class, 'profile'], ['id' => 1]
    );

<a name="redirecting-with-flashed-session-data"></a>
## Redirecting With Flashed Session Data
## 세션의 임시 데이터와 함께 리다이렉트 하기

Redirecting to a new URL and [flashing data to the session](/docs/{{version}}/session#flash-data) are usually done at the same time. Typically, this is done after successfully performing an action when you flash a success message to the session. For convenience, you may create a `RedirectResponse` instance and flash data to the session in a single, fluent method chain:

새로운 URL로 리다이렉팅 되는 것과 [세션에 데이터를 임시 저장하는것](/docs/{{version}}/session#flash-data)은 일반적으로 동시에 완료됩니다. 일반적으로, 이 작업은 여러분이 성공 메세지를 세션에 임시 저장할 때 작업을 성공적으로 액션을 수행한 다음에 완료됩니다. 보다 간편하게, 한번에 `RedirectResponse` 인스턴스를 생성하고 데이터를 세션에 임시저장하는 유연한 메소드 체이닝을 할 수 있습니다.

    Route::post('user/profile', function () {
        // Update the user's profile...

        return redirect('dashboard')->with('status', 'Profile updated!');
    });

After the user is redirected, you may display the flashed message from the [session](/docs/{{version}}/session). For example, using [Blade syntax](/docs/{{version}}/blade):

사용자가 리다이렉션 된 이후에, [세션](/docs/{{version}}/session)에서 임시 저장된 데이터를 표시할 수 있습니다. 예를 들어 [블레이드 문법](/docs/{{version}}/blade)을 사용해 보겠습니다.

    @if (session('status'))
        <div class="alert alert-success">
            {{ session('status') }}
        </div>
    @endif
